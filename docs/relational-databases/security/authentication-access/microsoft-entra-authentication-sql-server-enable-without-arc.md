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
- This setup uses an App Registration to associate SQL Server with Microsoft Entra ID. Follow the guide to [register an application](/entra/identity-platform/quickstart-register-app#register-an-application) in Microsoft Entra ID.
- Verify that the SQL Server has network connectivity to Azure, specifically to the Microsoft Entra ID service and below addresses.
  - login.windows.net
  - login.microsoftonline.com
  - graph.microsoft.com
  - graph.windows.net
  - database.windows.net


