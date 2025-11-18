---
title: Prepare Environment for LRS Migration
titleSuffix: SQL Server migration in Azure Arc
description: Prepare your SQL Server instance enabled by Azure Arc for migration to Azure SQL Managed Instance by using the Log Replay Service.
author: danimir
ms.author: danil
ms.reviewer: mikeray, randolphwest, mathoma
ms.date: 11/18/2025
ms.topic: how-to
---

# Prepare environment for LRS migration - SQL Server migration in Azure Arc

[!INCLUDE [sqlserver](../../includes/applies-to-version/sqlserver.md)]

This article helps you prepare your environment for a [Log Replay Service (LRS) migration](migrate-to-azure-sql-managed-instance.md#integrated-migration-methods) of your SQL Server instance enabled by Azure Arc to [Azure SQL Managed Instance](/azure/azure-sql/managed-instance/sql-managed-instance-paas-overview) in the Azure portal.

With LRS, you can migrate your SQL Server databases to Azure SQL Managed Instance by using backup and restore through log shipping (online migration):

:::image type="content" source="media/migrate-to-azure-sql-managed-instance/lrs-migration-method.png" alt-text="Diagram showing Log Replay Service migration.":::

> [!NOTE]  
> You can provide feedback on your migration experience [directly to the product group](https://aka.ms/arc-migrations-feedback).

## Prerequisites

To migrate your SQL Server databases to Azure SQL Managed Instance through the Azure portal, you need the following prerequisites:

- An active Azure subscription. If you don't have one, [create a free account](https://azure.microsoft.com/pricing/purchase-options/azure-account?icid=azurefreeaccount).
- A [supported](#supported-sql-server-versions) instance of SQL Server [enabled by Azure Arc](overview.md) with the Azure extension for SQL Server version `1.1.3211.337` or later. You can upgrade your extension by using the [Azure portal](/azure/azure-arc/servers/manage-vm-extensions-portal#upgrade-extensions) or the [Azure CLI](/azure/azure-arc/servers/manage-vm-extensions-cli#upgrade-extensions).

## Supported SQL Server versions

Migration with LRS works with every edition of SQL Server on Windows. While migration to both the General Purpose and Business Critical service tiers of SQL Managed Instance is supported, migrating directly to the Business Critical service tier has some [important limitations](#limitations-when-migrating-to-the-business-critical-service-tier) to consider.

The following table lists the minimum supported SQL Server versions for LRS:

| SQL Server version | Minimum required servicing update |
| --- | --- |
| SQL Server 2025 (17.x) |  [SQL Server 2025 RTM (17.0.1000.7)](../sql-server-2025-release-notes.md) |
| SQL Server 2022 (16.x) |  [SQL Server 2022 RTM (16.0.1000.6)](../sql-server-2022-release-notes.md) | 
| SQL Server 2019 (15.x) | [SQL Server 2019 RTM (15.0.2000.5)](../sql-server-2019-release-notes.md) |
| SQL Server 2017 (14.x) | [SQL Server 2017 RTM (14.0.1000.169)](../sql-server-2017-release-notes.md) | 
| SQL Server 2016 (13.x) | [SQL Server 2016 RTM (13.0.1400.361)](../sql-server-2016-release-notes.md) |
| SQL Server 2014 (12.x)| [SQL Server 2014 RTM (12.0.2000.8)](../sql-server-2014-release-notes.md) |
| SQL Server 2012 (11.x)| [SQL Server 2012 RTM (11.0.2100.60)](../sql-server-2012-release-notes.md) |

Reverse migration is only supported to SQL Server 2025 and SQL Server 2022 from SQL managed instances with the corresponding [update policy](/azure/azure-sql/managed-instance/update-policy). You can manually reverse a migration through other tools such as [native backup and restore](/azure/azure-sql/managed-instance/restore-database-to-sql-server), or [manually configuring a link in SSMS](/azure/azure-sql/managed-instance/managed-instance-link-configure-how-to-ssms).

## Permissions

This section describes the permissions that you need to migrate your SQL Server instance to SQL Managed Instance through the Azure portal.

On the source SQL Server instance, you need the following permissions:

- If you enable [least privilege](configure-least-privilege.md), necessary permissions such as **sysadmin** are [granted](configure-windows-accounts-agent.md#database-migration) as needed during the database migration process.
- If you can't use least privilege, you need **sysadmin** permissions on the source SQL Server instance.

To migrate with LRS, you need one of the following permissions on the SQL Managed Instance target:

- [SQL Managed Instance Contributor](/azure/role-based-access-control/built-in-roles/databases#sql-managed-instance-contributor) role
- A role with the following permission: `Microsoft.Sql/managedInstances/databases/*`

## Create a storage account

You use an Azure Blob Storage account as intermediary storage for backup files between your SQL Server instance and your SQL Managed Instance deployment. The storage account needs to be in the same Azure subscription as your SQL Managed Instance target.

To create a new storage account and a blob container inside the storage account:

1. [Create a storage account](/azure/storage/common/storage-account-create?tabs=azure-portal).
1. [Create a blob container](/azure/storage/blobs/storage-quickstart-blobs-portal) inside the storage account.
1. (Optional) [Configure Azure storage behind a firewall](/azure/azure-sql/managed-instance/log-replay-service-migrate#create-a-storage-account).

## Grant permissions to Azure Blob Storage

SQL Server migration in Azure Arc with LRS uses a managed identity to authenticate to Azure Blob Storage. You need to grant access to the blob storage:

- [Grant managed identity access to the Azure Blob Storage account](/azure/storage/blobs/authorize-access-azure-active-directory)
- [Grant managed identity access to SQL Managed Instance](/azure/azure-sql/database/authentication-azure-ad-user-assigned-managed-identity). Assign the **Storage Blob Data Reader** role to the primary managed identity of Azure SQL Managed Instance.

Assign the following roles to the user who signs in to the Azure portal and performs the migration:
- [Storage Blob Data Reader](/azure/role-based-access-control/built-in-roles/storage#storage-blob-data-reader) on the resource group that contains the storage account.
- **Reader** on the resource group that contains the storage account.

## Upload backups to your Blob Storage account

When your blob container is ready and you've confirmed that your SQL managed instance can access the container, you can begin uploading your backups to your Azure Blob Storage account. When all of your backups are uploaded to your storage account, you're ready to proceed with the migration.

To upload your backups to Azure:
- [Take backups on a SQL Server instance](#take-backups-on-a-sql-server-instance).
- [Copy your backups to your Blob Storage account](#copy-backups-to-your-blob-storage-account).

Consider the following best practices:
- Take backups with `COMPRESSION` and `CHECKSUM` options to reduce the size of backup files and to prevent migrating a corrupt database.
- Take backups in smaller batches.
- Use parallel upload threads.
- Make the last backup file as small as possible.
- To migrate multiple databases by using the same Azure Blob Storage container, place all backup files for an individual database into a separate folder inside the container. Use flat-file structure for each database folder. Nesting folders inside database folders isn't supported.

### Take backups on a SQL Server instance

Set databases that you want to migrate to the full recovery model to allow log backups.

```sql
-- To permit log backups, before the full database backup, modify the database to use the full recovery
USE master;

ALTER DATABASE SampleDB
SET RECOVERY FULL;
GO
```

If you don't already have existing backups, then to manually make full, differential, and log backups of your database to local storage, use the following sample T-SQL scripts. `CHECKSUM` isn't required, but it's recommended to prevent migrating a corrupt database, and for faster restore times.

The following example takes a full database backup to the local disk:

```sql
-- Take full database backup to local disk
BACKUP DATABASE [SampleDB]
    TO DISK = 'C:\BACKUP\SampleDB_full.bak'
    WITH INIT, COMPRESSION, CHECKSUM;
GO
```

The following example takes a differential backup to the local disk:

```sql
-- Take differential database backup to local disk
BACKUP DATABASE [SampleDB]
    TO DISK = 'C:\BACKUP\SampleDB_diff.bak'
    WITH DIFFERENTIAL, COMPRESSION, CHECKSUM;
GO
```

The following example takes a transaction log backup to the local disk:

```sql
-- Take transactional log backup to local disk
BACKUP LOG [SampleDB]
    TO DISK = 'C:\BACKUP\SampleDB_log.trn'
    WITH COMPRESSION, CHECKSUM;
GO
```

### Copy backups to your Blob Storage account

After your backups are ready, and you want to start migrating databases to a SQL managed instance by using LRS, use the following approaches to copy existing backups to your Blob Storage account:

- Download and install [AzCopy](/azure/storage/common/storage-use-azcopy-v10).
- Download and install [Azure Storage Explorer](/azure/vs-azure-tools-storage-manage-with-storage-explorer?tabs=windows).
- Use [Storage Explorer in the Azure portal](/azure/storage/blobs/storage-quickstart-blobs-portal?source=recommendations).

> [!NOTE]  
> To migrate multiple databases by using the same Azure Blob Storage container, place all backup files for an individual database into a separate folder inside the container. Use flat-file structure for each database folder. Nesting folders inside database folders isn't supported.

## Limitations

The limitations of [LRS](/azure/azure-sql/managed-instance/log-replay-service-migrate#limitations) apply to migrations through the Azure portal.

### Limitations when migrating to the Business Critical service tier

When migrating to a SQL Managed Instance in the **Business Critical** service tier, consider the following limitations:

- When migrating large databases, you might experience considerable downtime because databases are unavailable after cutover while they're seeded to secondary replicas of the **Business Critical** service tier. Workarounds are listed in the [longer cutover](#longer-cutover-in-the-business-critical-service-tier) section.
- Migration automatically restarts from the beginning if unplanned failover, system update, or security patch interrupts the migration. This limitation makes it difficult to plan a predictable migration without last minute surprises.

> [!IMPORTANT]  
> These limitations only apply when migrating to Azure SQL Managed Instance in the **Business Critical** service tier, and not to the **General Purpose** service tier.

#### Longer cutover in the Business Critical service tier

If you're migrating to a SQL Managed Instance in the **Business Critical** service tier, account for the delay in bringing the databases online on the primary replica while they're seeded to the secondary replicas. This delay is especially true for larger databases.

Migrating to a SQL Managed Instance in the **Business Critical** service tier takes longer to complete than in the General Purpose service tier. After cutover to Azure completes, databases are unavailable until they're seeded from the primary replica to the three secondary replicas. The seeding process can take a prolonged amount of time depending on your database size. The larger the database, the longer seeding to the secondary replicas takes - up to several hours, potentially.

If it's important that databases are available as soon as cutover completes, consider the following workarounds:

- Migrate to the General Purpose service tier first, then upgrade to the **Business Critical** service tier. Upgrading your service tier is an online operation that keeps your databases online until a short failover as the final step of the upgrade operation.
- Use the [Managed Instance link](migration-sql-mi-prepare-link.md) for an online migration to a **Business Critical** instance without having to wait for databases to be available after the cutover.

Monitoring the migration through the Azure portal is available only to SQL Server instances that meet monitoring [licensing requirements](sql-monitoring.md#prerequisites).

## Next steps

> [!div class="nextstepaction"]
> [Migrate to Azure SQL Managed Instance](migrate-to-azure-sql-managed-instance.md)

## Related content

- [SQL Server migration in Azure Arc](migration-overview.md)
- [Prepare environment for a Managed Instance link migration](migration-sql-mi-prepare-link.md)
- [SQL Server enabled by Azure Arc overview](overview.md)   
- [Migration experience feedback directly to the product group](https://aka.ms/arc-migrations-feedback)
- [Migration to Azure SQL Managed Instance - SQL Server migration in Azure Arc](migrate-to-azure-sql-managed-instance.md)
