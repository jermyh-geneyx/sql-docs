---
title: "MSOLEDBSQL major version differences"
description: A description of the differences between the OLE DB Driver 19 for SQL Server and the OLE DB Driver for SQL Server
author: David-Engel
ms.author: davidengel
ms.date: 05/02/2025
ms.service: sql
ms.subservice: connectivity
ms.topic: "reference"
helpviewer_keywords:
  - "MSOLEDBSQL, additional resources"
  - "MSOLEDBSQL19, additional resources"
  - "OLE DB Driver for SQL Server, additional resources"
  - "OLE DB Driver 19 for SQL Server, additional resources"
---
# Major version differences

## Encryption property changes

In the Microsoft OLE DB Driver 19 for SQL Server, there are changes to the `Encrypt` property/connection string keyword and certificate validation behavior.

First, the driver property `SSPROP_INIT_ENCRYPT` changes from a `VT_BOOL` to a `VT_BSTR`. The valid values of this property are `no`/`yes`/`true`/`false`/`Optional`/`Mandatory`/`Strict`. The valid values for the provider connection string keyword `Encrypt` change from `no`/`yes` to `no`/`yes`/`true`/`false`/`Optional`/`Mandatory`/`Strict`. Similarly, for the `IDataInitialize` connection string keyword `Use Encryption for Data`, the valid values change from `true`/`false` to `no`/`yes`/`true`/`false`/`Optional`/`Mandatory`/`Strict`. The `Optional` value is synonymous with the old `no`/`false` values and the `Mandatory` value is synonymous with the old `yes`/`true` values. `Strict` is a new value added in version 19.0.0 of the OLE DB Driver for SQL Server and encrypts `PRELOGIN` packets in addition to all other communication with the server. `Strict` encryption is only supported on SQL Server endpoints that support TDS 8.0, otherwise the driver fails to connect. The OLE DB Driver 19 for SQL Server continues to support all legacy keyword values for backwards compatibility.

Second, the default value changes from `no`/`false` to `Mandatory`. This change means that connections are encrypted by default. Previously, the driver would encrypt connections if explicitly set by the user and/or mandated by the SQL Server when the server side property `Force Encryption` was set to `yes`. To use old default behavior, include `Encrypt=Optional;` in the provider connection string, or `Use Encryption for Data=Optional;` in the `IDataInitialize` connection string.

Third, the `Trust Server Certificate` option is disconnected from the `Encrypt`/`Use Encryption for Data` option. In previous versions, when `Encrypt` was `false` on the client, the `Trust Server Certificate` setting was always ignored, even if the server required encryption (the server-side `Force Encryption` setting). Starting with version 19, if either the client or the server negotiates encryption on the connection, the `Trust Server Certificate` setting is evaluated to determine whether the client validates the certificate. This behavior change causes version 19 clients that use default settings to fail to connect when the server forces encryption and uses an untrusted certificate (an insecure server configuration). Clients must change their `Trust Server Certificate` registry setting and connection option to connect to servers configured that way. For more information, see [Registry settings](features/registry-settings.md) and [Encryption and certificate validation](features/encryption-and-certificate-validation.md) 

## Driver name changes

The new Microsoft OLE DB Driver 19 for SQL Server supports side by side installation with the older Microsoft OLE DB Driver for SQL Server. To be able to differentiate the drivers, the name was changed to include the major version number. To use the new driver in an application, the user must specify the new driver name. The new driver name, along with the corresponding CLSID, is specified in the updated `msoledbsql.h` header that must be included in the project. Connections through the `IDBInitialize` interface require no further changes since `MSOLEDBSQL_CLSID` specifies the CLSID of the OLE DB Driver 19 for SQL Server. Connections through the `IDataInitialize` interface must replace the value of the `Provider` keyword with `MSOLEDBSQL19` to use the Microsoft OLE DB Driver 19 for SQL Server. In graphical user interfaces such as data link properties or linked server setup in SSMS, "Microsoft OLE DB Driver 19 for SQL Server" must be selected from the list of installed providers.

## See also
[OLE DB Driver for SQL Server](oledb-driver-for-sql-server.md)  
[Using Connection String Keywords with OLE DB Driver](applications/using-connection-string-keywords-with-oledb-driver-for-sql-server.md)  
[Encryption and certificate validation](features/encryption-and-certificate-validation.md)  
[Universal Data Link (UDL) Configuration](help-topics/data-link-pages.md)  
[SQL Server Login Dialog Box (OLE DB)](help-topics/sql-server-login-dialog.md)  
[Initialization and authorization properties (OLE DB driver)](ole-db-data-source-objects/initialization-and-authorization-properties.md)  
[Registry settings](features/registry-settings.md)  
  
