---
title: "Configure PolyBase Support for Managed Identity"
description: "Explains how to configure PolyBase to query resources in Azure with managed identity."
author: MikeRayMSFT
ms.author: mikeray
ms.date: 08/08/2025
ms.service: sql
ms.topic: concept-article
---

# Connect to Azure storage with managed identity from PolyBase

Beginning with [!INCLUDE [sssql25-md](../../includes/sssql25-md.md)] you can use managed identities to access:

- Microsoft Azure Blob Storage
- Microsoft Azure Data Lake

## Prerequisites

- [!INCLUDE [sssql25-md](../../includes/sssql25-md.md)]
- [SQL Server enabled by Azure Arc](../../sql-server/azure-arc/managed-identity.md)
- Enable `sp_configure 'allow server scoped db credentials'`

## Update the registry

> [!WARNING]  
> Incorrectly editing the registry can severely damage your system. Before making changes to the registry, we recommend you back up any valued data on the computer.

Update the registry subkey `\HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Microsoft SQL Server\MSSQL17.MSSQLSERVER\MSSQLServer\FederatedAuthentication`. Add the following entries for your data storage types.

| Entry | Value |
| --- | --- |
| `AADDataLakeEndPoint` | `datalake.azure.net` |
| `AADAzureStorageEndPoint` | `storage.azure.com` |

These keys are in addition to the registry keys required as described in [Managed identity (preview) for SQL Server enabled by Azure Arc](../../sql-server/azure-arc/managed-identity.md?tabs=manual#update-the-registry).

## Create database scoped credentials

Add a database scoped credential for managed identity.

1. Allow server scoped database credentials. Run the following command.

   ```sql
   EXECUTE sp_configure 'allow server scoped db credentials',1;
   GO
   RECONFIGURE;
   ```

1. Create a database scoped credential. The example uses the name `managed_id`.

   ```sql
   CREATE DATABASE SCOPED CREDENTIAL [managed_id]
   WITH IDENTITY = 'Managed Identity';
   ```

## Create external data source

Create the external data source.

| External data source | Connector location prefix | Location path | Supported locations by product / service | Authentication |
| --- | --- | --- | --- | --- |
| Azure Storage Account (V2) | `abs` | `abs://<container_name>@<storage_account_name>.blob.core.windows.net/`<br />or<br />`abs://<storage_account_name>.blob.core.windows.net/<container_name>` | - [!INCLUDE [sssql22-md](../../includes/sssql22-md.md)]: Hierarchical Namespace supported<br />- [!INCLUDE [sssql25-md](../../includes/sssql25-md.md)] enabled by Azure Arc | Shared access signature (SAS)<br />or<br />Managed Identity |
| Azure Data Lake Storage | `adls` | `adls://<container_name>@<storage_account_name>.dfs.core.windows.net/`<br />or<br />`adls://<storage_account_name>.dfs.core.windows.net/<container_name>` | - [!INCLUDE [sssql22-md](../../includes/sssql22-md.md)]<br />- [!INCLUDE [sssql25-md](../../includes/sssql25-md.md)] enabled by Azure Arc | Shared access signature (SAS)<br />or<br />Managed Identity |

## Query a parquet file in Azure Blob Storage

[!INCLUDE [sssql25-md](../../includes/sssql25-md.md)] supports for Manage Identity through Azure Arc. For instructions, review [Managed identity (preview) for SQL Server enabled by Azure Arc](../../sql-server/azure-arc/managed-identity.md).

The following example queries a parquet file in Azure Blob Storage

```sql
EXECUTE sp_configure 'allow server scoped db credentials', 1;
RECONFIGURE;
GO

CREATE DATABASE SCOPED CREDENTIAL [managed_id]
WITH IDENTITY = 'Managed Identity';

CREATE EXTERNAL DATA SOURCE [my_external_data_source]
WITH (
    LOCATION = 'abs://<container>@<storage_account_name>.blob.core.windows.net/',
    CREDENTIAL = managed_id
);
```

## Errors and solutions

[!INCLUDE [sssql25-md](../../includes/sssql25-md.md)] enabled by Azure Arc fails to authenticate using Managed Identity.

To use managed identity, [!INCLUDE [sssql25-md](../../includes/sssql25-md.md)] must be enabled by Azure Arc. For instructions on how to enable by Azure Arc, review [Managed identity (preview) for SQL Server enabled by Azure Arc](../../sql-server/azure-arc/managed-identity.md).

Enable `sp_configure 'allow server scoped db credentials'`.

If any of the following are true, the PolyBase query fails:

- The SQL Server instance isn't properly configured for Azure Arc
- Registry entries are missing
- `allow server scoped db credentials` is disabled

The query will return one of the following errors when trying to access Azure Blob Storage or Azure Data Lake:

```output
Msg 16562, Level 16, State 1, Line 79
External table <name> is not accessible because location does not exist or it is used by another process.
```

Or

```output
Msg 16562, Level 16, State 1, Line 79
External table <name> is not accessible because location does not exist or it is used by another process.
```

## Related content

- [Managed identity (preview) for SQL Server enabled by Azure Arc](../../sql-server/azure-arc/managed-identity.md)
