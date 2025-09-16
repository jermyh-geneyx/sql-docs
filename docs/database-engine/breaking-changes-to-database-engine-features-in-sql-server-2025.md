---
title: "Database Engine: Breaking Changes"
titleSuffix: "SQL Server 2025 Preview"
description: Breaking changes to database engine features in SQL Server 2025 Preview.
author: MikeRayMSFT
ms.author: mikeray
ms.reviewer: randolphwest, mathoma
ms.date: 09/15/2025
ms.service: sql
ms.subservice: release-landing
ms.topic: conceptual
helpviewer_keywords:
  - "breaking changes 2017 [SQL Server]"
monikerRange: ">=sql-server-2017 || >=sql-server-linux-2017"
---
# Breaking changes to Database Engine features in SQL Server 2025 Preview

[!INCLUDE [sqlserver2025](../includes/applies-to-version/sqlserver2025.md)]

This article describes the breaking changes to features in the SQL Server Database Engine introduced with [!INCLUDE [sssql25-md](../includes/sssql25-md.md)]. These changes can break applications, scripts, or functionalities that are based on earlier versions of [!INCLUDE [ssNoVersion](../includes/ssnoversion-md.md)].

## Linked server connections fail after an upgrade

[!INCLUDE [sssql25-md](../includes/sssql25-md.md)] includes changes to [encryption](../relational-databases/security/networking/tds-8.md) that introduce a breaking change to [linked servers](../relational-databases/linked-servers/linked-servers-database-engine.md#sql-server-2025-and-msoledbsql-version-19). These changes can break applications, scripts, or functionalities that are based on earlier versions of [!INCLUDE [ssNoVersion](../includes/ssnoversion-md.md)].

When you upgrade from previous versions of SQL Server to [!INCLUDE [sssql25-md](../includes/sssql25-md.md)] with Microsoft OLE DB Driver 19, existing linked server configurations might fail. Different default values for the encryption parameter might cause this failure unless a valid certificate is provided.

In [!INCLUDE [sssql25-md](../includes/sssql25-md.md)]:

- [Linked servers to instances of SQL Server 2025 Preview must use the `Encrypt` parameter in the connection string](../relational-databases/linked-servers/linked-servers-database-engine.md#sql-server-2025-and-msoledbsql-version-19)
- [When you migrate from previous editions of SQL Server to SQL Server 2025 Preview with Microsoft OLE DB Driver 19, existing linked server configurations can fail](../relational-databases/linked-servers/linked-servers-database-engine.md#updating-from-previous-oledb-versions)

For information about how to connect securely to [!INCLUDE [sssql25-md](../includes/sssql25-md.md)] instances, see [TDS 8.0](../relational-databases/security/networking/tds-8.md).

## Replication components fail after an upgrade

[!INCLUDE [sssql25-md](../includes/sssql25-md.md)] includes changes to [encryption](../relational-databases/security/networking/tds-8.md) that introduce a breaking change to [Transactional](../relational-databases/replication/transactional/transactional-replication.md#configure-tls-13-encryption), [Snapshot](../relational-databases/replication/snapshot-replication.md#configure-tls-13-encryption), [Peer-to-peer](../relational-databases/replication/transactional/peer-to-peer-transactional-replication.md#configure-tls-13-encryption) and [Merge](../relational-databases/replication/merge/merge-replication.md#configure-tls-13-encryption) replication.

Replication components might fail after an upgrade to [!INCLUDE [sssql25-md](../includes/sssql25-md.md)] from all previous versions of SQL Server if your SQL Server instance:

- Is configured as a replication publisher.
- Has a remote distributor in the replication topology.
- Isn't configured with a trusted certificate.

You might see the following behavior after the upgrade:

- Replication continues to succeed but changes to the publication fail.
- Replication Monitor in SQL Server Management Studio (SSMS) fails.
- Agent status in the SSMS UI fails.

A remote distributor uses a linked server for communication between the publisher and distributor. The secure default introduced in [!INCLUDE [sssql25-md](../includes/sssql25-md.md)] of the OLEDB 19 provider requires that `TrustServerCertificate=False`.

You can resolve this issue preemptively before you start the upgrade, or you can resolve the issue if replication components fail after an upgrade.

### Before starting the upgrade

If you know that your SQL Server instance is going to encounter this issue after an upgrade, you can preemptively mitigate the failure by configuring the SQL Server instance to use a [public commercial certificate](../database-engine/configure-windows/special-cases-for-encrypting-connections-sql-server.md#use-a-certificate-issued-by-a-public-commercial-certificate-authority-and-only-some-clients-need-encrypted-connections) or a certificate from an [internal certificate authority](../database-engine/configure-windows/special-cases-for-encrypting-connections-sql-server.md#use-a-certificate-issued-by-an-internal-ca-or-created-by-using-new-selfsignedcertificate-or-makecert).

This is the recommended option for maximum security.

### Failed components after an upgrade

If your replication components fail after an upgrade, you can still configure the SQL Server instance to use a [public commercial certificate](../database-engine/configure-windows/special-cases-for-encrypting-connections-sql-server.md#use-a-certificate-issued-by-a-public-commercial-certificate-authority-and-only-some-clients-need-encrypted-connections) or a certificate from an [internal certificate authority](../database-engine/configure-windows/special-cases-for-encrypting-connections-sql-server.md#use-a-certificate-issued-by-an-internal-ca-or-created-by-using-new-selfsignedcertificate-or-makecert).

Alternatively, you can choose the less secure option to override the secure default of the OLEDB 19 provider and set `trust_distributor_certificate=yes` so the distributor trusts the self-signed certificate.

To override the new secure default, use the [sp_changedistributor_property](../relational-databases/system-stored-procedures/sp-changedistributor-property-transact-sql.md) stored procedure to set the `trust_distributor_certificate` option to `yes`:

```sql
exec sp_changedistributor_property @property = N'trust_distributor_certificate', @value = N'yes'
```

[!INCLUDE [sql-25-repl-distributor-info](../includes/sql-25-repl-distributor-info.md)]

## Adding a remote replication distributor fails

[!INCLUDE [sssql25-md](../includes/sssql25-md.md)] includes changes to [Encryption](../relational-databases/security/networking/tds-8.md) that introduce a breaking change to [Transactional](../relational-databases/replication/transactional/transactional-replication.md#configure-tls-13-encryption), [Snapshot](../relational-databases/replication/snapshot-replication.md#configure-tls-13-encryption), [Peer-to-peer](../relational-databases/replication/transactional/peer-to-peer-transactional-replication.md#configure-tls-13-encryption) and [Merge](../relational-databases/replication/merge/merge-replication.md#configure-tls-13-encryption) replication.

When configuring a distributor for replication, the [Sp_adddistributor](../relational-databases/system-stored-procedures/sp-adddistributor-transact-sql.md) stored procedure fails when:

- The publisher is a [!INCLUDE [sssql25-md](../includes/sssql25-md.md)] instance.
- The distributor is remote.
- The distributor isn't configured with a trusted certificate.

You might see the following error when running `sp_adddistributor` on the publisher instance:

```error-text
OLE DB provider "MSOLEDBSQL19" for linked server "repl_distributor" returned message
"Client unable to establish connection".
Msg -2146893019, Level 16, State 1, Line 21
SSL Provider: The certificate chain was issued by an authority that is not trusted.
```

A remote distributor uses a linked server for communication between the publisher and distributor. The secure default introduced in [!INCLUDE [sssql25-md](../includes/sssql25-md.md)] of the OLEDB 19 provider requires that `TrustServerCertificate=False`.

To resolve this issue, configure the distributor SQL Server instance to use a [public commercial certificate](../database-engine/configure-windows/special-cases-for-encrypting-connections-sql-server.md#use-a-certificate-issued-by-a-public-commercial-certificate-authority-and-only-some-clients-need-encrypted-connections) or a certificate from an [internal certificate authority](../database-engine/configure-windows/special-cases-for-encrypting-connections-sql-server.md#use-a-certificate-issued-by-an-internal-ca-or-created-by-using-new-selfsignedcertificate-or-makecert).

Alternatively, you can choose the less secure option to override the secure default of the OLEDB 19 provider and set `TrustServerCertificate=True` so the distributor trusts the self-signed certificate. To override the default, use the `trust_distributor_certificate` parameter when calling the [sp_adddistributor](../relational-databases/system-stored-procedures/sp-adddistributor-transact-sql.md) stored procedure:

```sql
exec sys.sp_adddistributor @trust_distributor_certificate = 'yes';
```

[!INCLUDE [sql-25-repl-distributor-info](../includes/sql-25-repl-distributor-info.md)]

## Remote log shipping monitoring can break

[!INCLUDE [sssql25-md](../includes/sssql25-md.md)] includes changes to [encryption](../relational-databases/security/networking/tds-8.md) that introduce a breaking change to [log shipping](log-shipping/about-log-shipping-sql-server.md#enforce-tls-13-encryption). You might encounter these issues when you upgrade.

Log shipping monitoring can break if the monitor is a remote [!INCLUDE [sssql25-md](../includes/sssql25-md.md)] instance when other SQL Server instances in the log shipping topology use a previous version.

For information about how to connect securely to [!INCLUDE [sssql25-md](../includes/sssql25-md.md)] instances, see [TDS 8.0](../relational-databases/security/networking/tds-8.md).

## Related content

- [TDS 8.0](../relational-databases/security/networking/tds-8.md)
- [Deprecated Database Engine features in SQL Server 2019 (15.x)](deprecated-database-engine-features-in-sql-server-2019.md)
- [Discontinued Database Engine functionality in SQL Server](discontinued-database-engine-functionality-in-sql-server.md)
