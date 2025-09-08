---
title: "SQL Server 2025 Known Issues"
description: "Known issues, causes, and workarounds for SQL Server 2025 Preview (17.x), covering upgrades, replication, PolyBase, session behavior, platform compatibility (Windows and Linux), backup compression, and other platform-specific limitations."
author: MikeRayMSFT
ms.author: mikeray
ms.reviewer: randolphwest
ms.date: 08/28/2025
ms.service: sql
ms.subservice: release-landing
ms.topic: troubleshooting-known-issue
monikerRange: ">=sql-server-2016"
---

# SQL Server 2025 Preview known issues

[!INCLUDE [sqlserver2025](../includes/applies-to-version/sqlserver2025.md)]

This article describes known issues for [!INCLUDE [sssql25-md](../includes/sssql25-md.md)].

[!INCLUDE [sssql25-md](../includes/sssql25-md.md)] has currently identified the following known issues:

- [Windows Arm64 not supported](#windows-arm64-not-supported)
- [In-place upgrade fails due to Microsoft Visual C++ Redistributable](#in-place-upgrade-fails-due-to-microsoft-visual-c-redistributable)
- [In-place upgrade fails due to replication](#in-place-upgrade-fails-due-to-replication)
- [Adding a remote replication distributor fails](#adding-a-remote-replication-distributor-fails)
- [Linked server connections fail after an upgrade](#linked-server-connections-fail-after-an-upgrade)
- [SQL Server on Windows fails to start on machines with more than 64 logical cores per NUMA node](#sql-server-on-windows-fails-to-start-on-machines-with-more-than-64-logical-cores-per-numa-node)
- [Incorrect behavior of SESSION_CONTEXT in parallel plans](#incorrect-behavior-of-session_context-in-parallel-plans)
- [Access violation exception occurs under certain conditions](#access-violation-exception-occurs-under-certain-conditions)
- [Issue when setting the backup compression algorithm to ZSTD](#setting-the-backup-compression-algorithm-to-zstd)
- [PolyBase components fail to start](#polybase-components-fail-to-start)
- [PolyBase connections fail to external SQL Server source](#polybase-connections-fail-to-external-sql-server-source)
- [SQL Server on Linux fails to start on machines with hybrid CPU architecture](#sql-server-on-linux-fails-to-start-on-machines-with-hybrid-cpu-architecture)
- [Linux PolyBase Network encryption enabled fails](#linux-polybase-network-encryption-enabled-fails)
- [Local ONNX models not supported on Linux operating systems](#local-onnx-models-not-supported-on-linux-operating-systems)
- [PBKDF2 hashing algorithm can affect login performance](#pbkdf2-hashing-algorithm-can-affect-login-performance)

## Windows Arm64 not supported

[!INCLUDE [sssql25-md](../includes/sssql25-md.md)] isn't supported on Windows Arm64. Only Intel and AMD x86-64 CPUs with [up to 64 cores per NUMA node](compute-capacity-limits-by-edition-of-sql-server.md#numa-64) are currently supported.

## In-place upgrade fails due to Microsoft Visual C++ Redistributable

An upgrade from the following versions might fail:

- [!INCLUDE [sssql16-md](../includes/sssql16-md.md)]
- [!INCLUDE [sssql17-md](../includes/sssql17-md.md)]

This can happen when the existing operating system environment is missing the Microsoft Visual C++ Redistributable for Visual Studio 2022, or an older version of this component is installed.

When this happens, the installation log includes an entry like the following example:

```output
This application requires Microsoft Visual C++ Redistributable for
Visual Studio 2022 (x64/x86, version 14.34 at minimum).
Please install the Redistributable, then run this installer again.
For more information, see: https://go.microsoft.com/fwlink/?linkid=2219560.
```

To complete the upgrade, add or repair the redistributable component, and run the installation again.

To get the redistributable file, review [Microsoft Visual C++ Redistributable latest supported downloads](/cpp/windows/latest-supported-vc-redist).

## In-place upgrade fails due to replication

Upgrades to [!INCLUDE [sssql25-md](../includes/sssql25-md.md)] from all previous versions of SQL Server might fail if your SQL Server instance:
- Is configured as a replication publisher.
- Has a remote distributor in the replication topology.
- Isn't configured with a trusted certificate.

In this scenario, during the in-place upgrade, the **SQL Server replication** component shows a status of **Failed** and you see the following error:

```error-text
There was an error executing the Replication upgrade scripts. See the SQL Server error log for details.
```

The SQL Server error log contains the following errors:

```error-text
OLE DB provider "MSOLEDBSQL19" for linked server "repl_distributor" returned message
"Client unable to establish connection".
SSL Provider: The certificate chain was issued by an authority that is not trusted.
Error executing sp_vupgrade_replication.
```

You might see the following behavior after the upgrade:

- Replication continues to succeed but changes to the publication fail.
- Replication Monitor in SQL Server Management Studio (SSMS) fails.
- Agent status in the SSMS UI fails.

You can resolve this issue preemptively before you start the upgrade, or you can resolve the issue after an upgrade fails.

### Before starting the upgrade

> [!NOTE]  
> The resolution in this section is intended for the current RC 0 release of [!INCLUDE [sssql25-md](../includes/sssql25-md.md)] and will be addressed differently in a future release.

If you know that your SQL Server upgrade is going to encounter this issue, you can preemptively mitigate the failure by configuring the SQL Server instance to use a [public commercial certificate](../database-engine/configure-windows/special-cases-for-encrypting-connections-sql-server.md#use-a-certificate-issued-by-a-public-commercial-certificate-authority-and-only-some-clients-need-encrypted-connections) or a certificate from an [internal certificate authority](../database-engine/configure-windows/special-cases-for-encrypting-connections-sql-server.md#use-a-certificate-issued-by-an-internal-ca-or-created-by-using-new-selfsignedcertificate-or-makecert).

This is the recommended option for maximum security.

### After a failed upgrade

> [!NOTE]  
> The workaround in this section is intended for the current RC 0 release of [!INCLUDE [sssql25-md](../includes/sssql25-md.md)] and will be addressed differently in a future release.

After an upgrade fails, you can still configure the SQL Server instance to use a [public commercial certificate](../database-engine/configure-windows/special-cases-for-encrypting-connections-sql-server.md#use-a-certificate-issued-by-a-public-commercial-certificate-authority-and-only-some-clients-need-encrypted-connections) or a certificate from an [internal certificate authority](../database-engine/configure-windows/special-cases-for-encrypting-connections-sql-server.md#use-a-certificate-issued-by-an-internal-ca-or-created-by-using-new-selfsignedcertificate-or-makecert).

After configuring your SQL Server instance to use a certificate, [repair the SQL Server installation](../database-engine/install-windows/repair-a-failed-sql-server-installation.md).

Alternatively, for SQL Server on Windows, you can choose the less secure option of overriding the secure default of the new OLEDB provider and set `TrustServerCertificate=True` to trust the self-signed certificate. Customers on Linux can't override defaults, and must import a trusted certificate to resolve this issue.

To override the new default, follow these steps:

1. Use the [sys.sp_serveroption](../relational-databases/system-stored-procedures/sp-serveroption-transact-sql.md) stored procedure to set the `TrustServerCertificate=Yes` option:

   ```sql
   exec sys.sp_serveroption N'repl_distributor', 'provider string', N'TrustServerCertificate=Yes'
   ```

1. Set the following [Trust Server Certificate registry entry](../connect/oledb/features/registry-settings.md#trust-server-certificate) to `1`:

   `HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\MSSQLServer\Client\SNI19.0\GeneralFlags\Flag2`

1. Restart the SQL Server instance.
1. [Repair a failed SQL Server installation](../database-engine/install-windows/repair-a-failed-sql-server-installation.md) to finalize the upgrade.

> [!NOTE]  
> The new secure defaults pertain to the new underlying OLEDB 19 provider, which enhances security. The option to override the default is less secure than configuring your instance to use a trusted certificate. After overriding the default, you have the option to configure SQL Server to use a certificate, and use the same [sys.sp_serveroption](../relational-databases/system-stored-procedures/sp-serveroption-transact-sql.md) stored procedure to set the default back to `TrustServerCertificate=No`.

<a id="adding-a-remote-replication-distributor-fails"></a>

## Add a remote replication distributor fails

> [!NOTE]  
> The resolution in this section is intended for the current RC 0 release of [!INCLUDE [sssql25-md](../includes/sssql25-md.md)] and will be addressed differently in a future release.

When configuring a distributor for replication, the [sp_adddistributor](../relational-databases/system-stored-procedures/sp-adddistributor-transact-sql.md) stored procedure fails when:

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

A remote distributor uses a linked server for communication between the publisher and distributor. The new secure default introduced in [!INCLUDE [sssql25-md](../includes/sssql25-md.md)] of the new OLEDB provider requires that `TrustServerCertificate=False`.

To resolve this issue, configure the distributor SQL Server instance to use a [public commercial certificate](../database-engine/configure-windows/special-cases-for-encrypting-connections-sql-server.md#use-a-certificate-issued-by-a-public-commercial-certificate-authority-and-only-some-clients-need-encrypted-connections) or a certificate from an [internal certificate authority](../database-engine/configure-windows/special-cases-for-encrypting-connections-sql-server.md#use-a-certificate-issued-by-an-internal-ca-or-created-by-using-new-selfsignedcertificate-or-makecert).

## Linked server connections fail after an upgrade

When you upgrade from previous versions of SQL Server to [!INCLUDE [sssql25-md](../includes/sssql25-md.md)] with Microsoft OLE DB Driver 19, existing linked server configurations might fail. Different default values for the encryption parameter might cause this failure unless a valid certificate is provided.

To learn more, review [Linked servers](../relational-databases/linked-servers/linked-servers-database-engine.md#updating-from-previous-oledb-versions).

## SQL Server on Windows fails to start on machines with more than 64 logical cores per NUMA node

**Issue**: SQL Server instances on Windows might fail to start after the installation if the machine has more than 64 logical cores per NUMA node.

For more information, see [Limit number of logical cores per NUMA node to 64](compute-capacity-limits-by-edition-of-sql-server.md#limit-number-of-logical-cores-per-numa-node-to-64).

## Incorrect behavior of SESSION_CONTEXT in parallel plans

Queries that use the built-in `SESSION_CONTEXT` function might return incorrect results or trigger access violation (AV) dumps when executed in parallel query plans. This issue stems from the way the function interacts with parallel execution threads, particularly when the session is reset for reuse.

For more information, see the [Known issues](../t-sql/functions/session-context-transact-sql.md#known-issues) section in `SESSION_CONTEXT`.

## Access violation exception occurs under certain conditions

When the Optional parameter plan optimization feature encounters a predicate that is based on a LOB column, an access violation exception can occur. A fix has been identified and will be part of the next preview release of SQL Server 2025.

Large object (LOB) data types in the Database Engine can store data that exceeds 8,000 bytes. These data types store data on a [row-overflow](../relational-databases/pages-and-extents-architecture-guide.md#row-overflow-considerations) data page. A LOB also encompasses data types that store data on dedicated LOB page structures, which use a text or image pointer of in-row references to LOB data pages. For more information about data storage, see [Pages and extents architecture guide](../relational-databases/pages-and-extents-architecture-guide.md).

<a id="setting-the-backup-compression-algorithm-to-zstd"></a>

## Issue when setting the backup compression algorithm to ZSTD

There's a known issue when attempting to set the [backup compression algorithm](../database-engine/configure-windows/view-or-configure-the-backup-compression-algorithm-server-configuration-option.md) to ZSTD.

When specifying the ZSTD algorithm (`backup compression algorithm = 3`), the following error message returns:

```output
Msg 15129, Level 16, State 1
Procedure sp_configure '3' is not a valid value for configuration option 'backup compression algorithm'.
```

Use the new compression algorithm directly in the [BACKUP](../t-sql/statements/backup-transact-sql.md#compression) Transact-SQL command instead of setting the server configuration option.

## PolyBase components fail to start

PolyBase components can fail to start after upgrading to, or installing a new instance of, [!INCLUDE [sssql25-md](../includes/sssql25-md.md)]. Review [PolyBase network encryption](../relational-databases/polybase/polybase-installation.md#polybase-network-encryption) to learn more.

## PolyBase connections fail to external SQL Server source

[!INCLUDE [sssql25-md](../includes/sssql25-md.md)] PolyBase connections can fail to external SQL Server sources if the external data source isn't [properly configured](../t-sql/statements/create-external-data-source-transact-sql.md#syntax-for-sql-server-2025-and-later-versions). Review the [PolyBase network encryption](../relational-databases/polybase/polybase-installation.md#polybase-network-encryption) documentation for more information.

## SQL Server on Linux fails to start on machines with hybrid CPU architecture

**Issue**: SQL Server instances on Linux might fail to start if the machine uses an Intel 12th Gen or later hybrid architecture CPU, and the host operating system is Linux.

You might see an error message similar to the following output:

```output
Reason: 0x00000004 Message: ASSERT: Expression=(result * DrtlGetProcessorCoreCount() == DrtlGetProcessorCount()) File=LibOS\Windows\Kernel\SQLPal\common\dk\sos\src\sosnumap.cpp Line=208
```

If you want to use a Linux host operating system, you can work around the issue by disabling efficiency cores (E-cores) in your BIOS. If you use containers, or a hypervisor like Hyper-V on Windows (including WSL), you aren't affected.

## Linux PolyBase Network encryption enabled fails

[!INCLUDE [polybase-release-candidate-0](../includes/polybase-release-candidate-0.md)]

## Local ONNX models not supported on Linux operating systems

[CREATE EXTERNAL MODEL](../t-sql/statements/create-external-model-transact-sql.md) local ONNX models hosted directly on the SQL Server aren't currently available for Linux on [!INCLUDE [sssql25-md](../includes/sssql25-md.md)] RC 0.

## PBKDF2 hashing algorithm can affect login performance

In [!INCLUDE [sssql25-md](../includes/sssql25-md.md)], password-based authentication uses PBKDF2 (RFC2898) as the default hashing algorithm. This enhancement improves password security by applying 100,000 iterations of SHA-512 hashing. The increased computational cost of PBKDF2 means slightly longer SQL Authentication login time. This effect is especially noticeable in environments without connection pooling, or where login latency is closely monitored. In pooled environments, the effect is typically minimal.

For more information, see [CREATE LOGIN](../t-sql/statements/create-login-transact-sql.md) and [Support for Iterated and Salted Hash Password Verifiers in SQL Server 2022 CU12](https://techcommunity.microsoft.com/blog/azuresqlblog/support-for-iterated-and-salted-hash-password-verifiers-in-sql-server-2022-cu12/4087155).

## Related content

- [What's new in SQL Server 2025 Preview](what-s-new-in-sql-server-2025.md)
- [SQL Server 2025 Preview release notes](sql-server-2025-release-notes.md)
- [Breaking changes to Database Engine features in SQL Server 2025](../database-engine/breaking-changes-to-database-engine-features-in-sql-server-2025.md)
