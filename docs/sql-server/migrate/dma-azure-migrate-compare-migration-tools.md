---
title: "Compare SQL Data Migration Tools"
titleSuffix: SQL Server
description: "Compare SQL data migration tools to determine which tool best suits your business needs, such as Azure Migrate, Azure Database Migration Service (DMS), SQL Server Migration Assistant (SSMA), Database Experimentation Assistant (DEA). "
author: croblesm
ms.author: roblescarlos
ms.reviewer: randolphwest, maghan
ms.date: 06/16/2025
ms.service: sql
ms.subservice: dma
ms.topic: conceptual
helpviewer_keywords:
  - "migration, on-premises SQL Server"
---

# Compare SQL data migration tools

Microsoft provides a suite of tools and services to assist users with migrating various source databases to different target environments.

This article briefly overviews the tools available to migrate to SQL Server and Azure SQL.

## Azure Database Migration Service (DMS)

Azure Database Migration Service enables seamless migrations from SQL Server database sources to Azure SQL offerings with minimal downtime. The Database Migration Service provides a resilient and reliable migration pipeline that requires minimal user involvement during the overall migration process.

With Azure Database Migration Service currently we offer two versions:

1. Azure Database Migration Service can be accessed via [Azure SQL migration extension for Azure Data Studio](/azure/dms/migration-using-azure-data-studio), [Azure portal](https://portal.azure.com/#create/Microsoft.AzureDMS), PowerShell and Azure CLI.

  Use the latest Azure Database Migration Service in the following scenarios:

  - Migrate both databases to Azure SQL, especially at scale, and for extensive (in terms of number and size of databases) migrations.
  - Migrate databases to Azure Database.

2. Azure Database Migration Service (classic) can be accessed via Azure portal, PowerShell and Azure CLI. It is an older version of the Azure Database Migration Service that offers database modernization to Azure and support scenarios like - PostgreSQL, MySQL, and MongoDB.

> [!NOTE]
> DMS(classic) - SQL scenario are on the deprecation path. Refer [here](https://azure.microsoft.com/updates?id=retirement-azure-database-migration-service-classic-sql-server-scenarios-deprecation)

For more information, visit [Azure Database Migration Service documentation](/azure/dms/).

## Azure Migrate

Azure Migrate provides a centralized hub to discover and assess on-premises servers, infrastructure, applications, and data to Azure at scale. Azure Migrate offers a unified migration across your servers, databases, and applications.

Use Azure Migrate to discover all your SQL Server instances across your datacenter, assess application dependencies, understand the readiness of these SQL Server instances migrating to Azure SQL, and get Microsoft recommendations, such as the optimal Azure SQL deployment option and the correct SKU that can fit the performance needs for your workloads. You can also get the monthly estimates running these databases on Azure SQL to cater to your licensing benefits.

Use Azure Migrate in the following scenarios:

- Assess and discover your SQL Server data estate.
- Get Azure SQL deployment recommendations, target sizing, and monthly estimates.
- Lift your entire data estate to SQL Server on Azure VMs.

For more information, visit [Azure Migrate documentation](/azure/migrate/).

## SQL Server Migration Assistant (SSMA)

SQL Server Migration Assistant (SSMA) is a tool designed to automate database migration to SQL Server and Azure SQL from alternative database engines.

Use SSMA in the following scenario:

- Migrate Microsoft Access, DB2, MySQL, Oracle, and SAP ASE databases to SQL Server.
- Migrate Microsoft Access, DB2, MySQL, Oracle, and SAP ASE databases to Azure SQL.

For more information, visit [SQL Server Migration Assistant documentation](../../ssma/sql-server-migration-assistant.md).

## Quick comparison

Use the following chart to compare capabilities of the SQL migration tools:

| Capability | Azure Migrate | SQL migration component | SSMA | DMS (classic) | Azure Data Studio extension (DMS) |
| ---------- | ------------- | --- | ---- | --- | --- | ----------------------------|
| Discover and assess SQL data estate | At scale | Yes | No | No | Yes |
| Migrate SQL Server objects to SQL Database or SQL Managed Instance | No | No | No | NA (deprecated) | Yes |
| Lift and shift SQL Server to SQL Server on Azure VM | Yes | No | No | No | No |
| Migrate (and/or upgrade) SQL Server to SQL Server on Azure VM | No | Yes | No | No | Yes (Migrate only) |
| Migrate non-SQL objects<br />(Oracle, Access, DB2, and so on) | No | No | Yes | No | No |
| Migrate open source databases<br />(MySQL, PostgreSQL, MariaDB, and so on) | No | No | No | Yes | No |

## Related content

- Get started with migrating to [SQL Server](../../ssma/sql-server-migration-assistant.md) from another database engine, migrate to [Azure SQL](/azure/azure-sql/migration-guides/), or assess your SQL data estate with [Azure Migrate](/azure/migrate/how-to-create-azure-sql-assessment).
on
