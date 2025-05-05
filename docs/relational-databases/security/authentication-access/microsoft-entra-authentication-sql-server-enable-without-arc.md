---
title: "Enable Microsoft Entra Authentication for SQL Server on Windows without Azure Arc"
description: Tutorial on how to set up Microsoft Entra authentication for SQL Server without Azure Arc
author: PratimDasgupta
ms.author: prdasgu
ms.reviewer: vanto
ms.date: 05/01/2025
ms.service: sql
ms.subservice: security
ms.topic: tutorial
monikerRange: ">=sql-server-ver16||>= sql-server-linux-ver16"
---

# Tutorial: Enable Microsoft Entra authentication for SQL Server on Windows without Azure Arc

[!INCLUDE [SQL Server 2022](../../../includes/applies-to-version/sqlserver2022.md)]

This article describes how to authenticate with Microsoft Entra ID without setting up Azure Arc for your on-premise SQL Server 2022 and later versions. Microsoft Entra authentication is a cloud-based identity management service that provides secure access to SQL Server databases. This tutorial guides you through the process of setting up Microsoft Entra authentication for SQL Server on Windows without Azure Arc.

[!INCLUDE [entra-id](../../../includes/entra-id.md)]

In this tutorial, you learn how to:

> [!div class="checklist"]
> - Deploying

## Prerequisites

- An on-premises SQL Server 2022 or later version.
- An active [Microsoft Entra ID](/entra/fundamentals/whatis) tenant.
- This setup uses an application registration to associate SQL Server with Microsoft Entra ID. Follow the guide to [register an application](/entra/identity-platform/quickstart-register-app#register-an-application) in Microsoft Entra ID.
- Verify that the SQL Server has network connectivity to Azure, specifically to the following Microsoft Entra ID service and addresses:
  - login.windows.net.
  - login.microsoftonline.com.
  - graph.microsoft.com.
  - graph.windows.net.
  - database.windows.net.
  - The full list of IP addresses and URLs aren't needed, but can be found in the article, [Microsoft 365 URLs and IP address ranges](/microsoft-365/enterprise/urls-and-ip-address-ranges).

## Deployment

Follow these steps to enable Microsoft Entra authentication for SQL Server without using the Azure extension for SQL Server.

### Obtain a certificate

1. Obtain a certificate to use for the SQL Server and import it into the computer certificate store. We recommend a CA signed certificate.
   1. Use a unique CN name for the certificate that does not match any certificates installed in the certificate store.
   1. Install the certificate in the computer certificate store. For more information, see [Import the certificate into the local computer store](/troubleshoot/windows-server/certificates-and-public-key-infrastructure-pki/install-imported-certificates#import-the-certificate-into-the-local-computer-store).
   1. Add `Read` permissions for the SQL Server service account on the certificate.

      :::image type="content" source="media/microsoft-entra-authentication-sql-server-enable-without-arc/certificate-permissions.png" alt-text="Screenshot of the Permissions properties of a certificate.":::

### Install adal.dll



### Create and register a Microsoft Entra ID application


