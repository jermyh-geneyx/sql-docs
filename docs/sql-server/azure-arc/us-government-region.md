---
title: Azure Government Preview
description: "Describes features and limitations currently available for SQL Server enabled by Azure Arc on US Government region."
author: AbdullahMSFT
ms.author: amamun
ms.reviewer: MikeRayMSFT
ms.topic: conceptual #Don't change
ms.date: 06/09/2025

#customer intent: As a U.S. government agency or organization, I want to manage SQL Server instances outside of Azure from the Azure Government portal so that I can ensure secure and compliant management of my SQL Server estate.

---

# SQL Server enabled by Azure Arc in US Government Preview

This article describes features and limitations currently available for SQL Server enabled by Azure Arc on US Government regions. It also:

- Points to instructions how to on board your SQL Server in US Government regions
- Lists known issues and options to work around the issues

SQL Server enabled by Azure Arc creates SQL Server Azure Arc resources in US Government. View these resources in the Azure Government portal just like any Azure resource. The portal provides a single pane of glass to monitor and organize your SQL Server estate in the government cloud.  

[!INCLUDE [preview](../../includes/preview.md)]

Currently, these features are only available:

- In the US Government Virginia region
- On Windows operating system

With SQL Server enabled by Azure Arc, U.S. government agencies and organizations can manage SQL Server instances outside of Azure from the Azure Government portal, in a secure and compliant manner.

The latest updates are available in the [Release notes - SQL Server enabled by Azure Arc](release-notes.md).

## Onboard your SQL Server

To onboard your SQL Server, complete the following steps from the Azure (US Gov) portal.

1. [Connect hybrid machines with Azure Arc-enabled servers](/azure/azure-arc/servers/learn/quick-enable-hybrid-vm)
1. [Connect your SQL Server to Azure Arc on a server already enabled by Azure Arc](connect-already-enabled.md)

## Available features

Currently, in the US Government Virginia region, SQL Server registration provides the following features:

- [Connect](connect.md) (onboard) a SQL Server instance to Azure Arc.
- [SQL Server inventory](overview.md#manage-your-sql-server-instances-at-scale-from-a-single-point-of-control) which includes the following capabilities in the Azure portal:
   - View the SQL Server instance as an Azure resource. 
   - View databases as an Azure resource. 
   - View the properties for each server. For example, you can view the version, edition, and database for each instance.

[All other features](overview.md#feature-differentiation), including Extended Security Updates (ESU), are not currently available.

## Limitations

The following SQL Server features are not currently available in any US Government region:

- Failover cluster instance (FCI)
- Availability group (AG)
- SQL Server services like [!INCLUDE [ssis-md](../../includes/ssis-md.md)], [!INCLUDE [ssrs](../../includes/ssrs.md)], or Power BI Report Server as well.

## Known issues

- In Azure portal, the Arc SQL Server Instance properties page does not show the `licenseType` property value.
  - **Workaround**: Refer to Arc Server page for accurate and updated license information.

- Resources deleted on-premises might not be immediately deleted from Azure portal. For example, if you delete a database on-premises, the image of the database on the portal remains until a refresh occurs.
  - **Workaround**: To reflect the change immediately, delete the resource in the portal.

## Related content

- [SQL Server enabled by Azure Arc](overview.md)
