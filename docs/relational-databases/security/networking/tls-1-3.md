---
title: TLS 1.3 support
description: This article discusses TLS 1.3 support with SQL Server 2022 and Azure SQL Database.
author: srdan-bozovic-msft
ms.author: srbozovi
ms.date: 08/15/2025
ms.service: sql
ms.subservice: security
ms.topic: conceptual
monikerRange: ">=sql-server-ver16 || =azuresqldb-current|| >=sql-server-linux-ver16"
---
# TLS 1.3 support

[!INCLUDE [SQL Server 2022](../../../includes/applies-to-version/sqlserver2022.md)] [!INCLUDE[Azure SQL Database](../../../includes/applies-to-version/asdb.md)]

[!INCLUDE [ssnoversion-md](../../../includes/ssnoversion-md.md)] (Beginning with [!INCLUDE [sssql22-md](../../../includes/sssql22-md.md)]), and Azure SQL Database support Transport Layer Security (TLS) 1.3 when TDS 8.0 is used.

> [!IMPORTANT]
> Even with TLS 1.3 support for TDS connections, TLS 1.2 is still required for starting up [!INCLUDE [ssnoversion-md](../../../includes/ssnoversion-md.md)] satellite services. Don't disable TLS 1.2 on the machine.

[!INCLUDE [sssql19-md](../../../includes/sssql19-md.md)] and earlier versions don't support TLS 1.3.

## Differences between TLS 1.2 and TLS 1.3

TLS 1.3 reduces the number of round trips from two to one during the handshake phase, making it faster and more secure than TLS 1.2. The server hello packet containing server certificate is encrypted and one Round Trip Time (1-RTT) resumption is discontinued, and replaced with 0-RTT resumption based on client key share. Added security of TLS 1.3 comes from discontinuing certain cyphers and algorithms.

Here's a list of algorithms and ciphers removed in TLS 1.3:

- RC4 stream cipher
- RSA key exchange
- SHA-1 hash function
- CBC (block) mode ciphers
- MD5 algorithm
- Various non-ephemeral Diffie-Hellman groups
- EXPORT-strength ciphers
- DES
- 3DES

## Driver support

Review the [Driver feature support matrix](../../../connect/driver-feature-matrix.md) to determine what drivers currently support TLS 1.3. 

## Operating system support

Currently, the following operating systems support TLS 1.3: 

- [Windows 11](/windows/win32/secauthn/tls-cipher-suites-in-windows-11)
- [Windows Server 2022](/windows/win32/secauthn/tls-cipher-suites-in-windows-server-2022)

## SQL Server 2025 support

[!INCLUDE [sssql25-md](../../../includes/sssql25-md.md)] introduces TLS 1.3 support for the following features: 

- [SQL Server Agent](/ssms/agent/sql-server-agent#tds-80-and-strict-encryption-support)
- [Always On availability groups](connect-with-strict-encryption.md#connect-to-an-always-on-availability-group)
- [Always On failover cluster instances (FCI)](connect-with-strict-encryption.md#connect-to-a-failover-cluster-instance)
- [Linked servers](../../linked-servers/linked-servers-database-engine.md#sql-server-2025-and-msoledbsql-version-19)
- [Transactional replication](../../replication/transactional/transactional-replication.md#configure-tls-13-encryption)
- [Merge replication](../../replication/merge/merge-replication.md#configure-tls-13-encryption)
- [Snapshot replication](../../replication/snapshot-replication.md#configure-tls-13-encryption)
- [Log shipping](../../../database-engine/log-shipping/about-log-shipping-sql-server.md#enforce-tls-13-encryption)

## Related content

- [Connect to SQL Server with strict encryption](connect-with-strict-encryption.md)
- [Configure TLS 1.3](connect-with-tls-1-3.md)
