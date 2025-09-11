---
title: Determine Required Subnet Size and Range
titleSuffix: Azure SQL Managed Instance
description: Learn how to calculate the size of the subnet where you intend to deploy Azure SQL Managed Instance.
author: zoran-rilak-msft
ms.author: zoranrilak
ms.reviewer: mathoma, bonova, srbozovi, wiassaf
ms.date: 09/11/2025
ms.service: azure-sql-managed-instance
ms.subservice: deployment-configuration
ms.topic: how-to
ms.custom:
  - sqldbrb=1
  - ignite-2023
---

# Determine required subnet size and range for Azure SQL Managed Instance

[!INCLUDE [appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

This article helps you determine the appropriate subnet size and IP address range for Azure SQL Managed Instance.

## Overview

Azure SQL Managed Instance is made up of service components that are hosted on a *dedicated set of isolated virtual machines* placed inside one or more virtual machine groups hosted by a [virtual cluster](virtual-cluster-architecture.md) and deployed within an Azure [virtual network](/azure/virtual-network/virtual-networks-overview).

A virtual cluster, associated with a single subnet in a virtual network, can host one or more SQL managed instances. The number of instances that can be deployed to a subnet depends on the size of the subnet (subnet range).

When you create a SQL managed instance, Azure allocates the number of virtual machines based on the chosen service tier. Since these virtual machines are associated with your subnet, they require IP addresses. Azure can allocate more virtual machines to ensure high availability during regular operations and service maintenance. The number of required IP addresses in a subnet is typically larger than the number of SQL managed instances in that subnet.

## Determine subnet size

Carefully plan the subnet size for your SQL managed instance deployments.

By design, each SQL managed instance needs a minimum of 32 IP addresses in a subnet. You can use a minimum subnet mask of /27 when defining your subnet IP ranges.

Use the following list of considerations when determining the size of your subnet:

- Instance related considerations:
  - The number of SQL managed instances
  - The service tier of the instances
- Virtual cluster related considerations:
  - Hardware configurations
  - Maintenance window configurations
- Management operations related considerations:
  - Plans to scale up/down or change the service tier, hardware configuration, or maintenance window

Use the following parameters to help form a calculation:

- Azure uses five IP addresses in the subnet for its own needs.
- Each [virtual machine group](virtual-cluster-architecture.md#number-of-vm-groups) allocates six more addresses.
- The number of addresses that each SQL managed instance uses, depends on the service tier.
  - General Purpose SQL managed instance uses three addresses
  - Business Critical SQL managed instance uses five addresses
- Each scaling request temporarily doubles the number of addresses allocated for the instance being scaled.

> [!IMPORTANT]  
> Changing the subnet address range isn't supported when resources exist in the subnet. For this reason, it's better to use bigger subnets rather than smaller ones to prevent future issues.

### Single-deployed instance

The following table shows the number of IP addresses required for a single instance in a subnet deployed to each service tier:

| **Service tier** | **Azure usage** <sup>1</sup> | **VM group usage** <sup>2</sup> | **Instance usage** | **Total**<sup>3</sup> |
| --- | --- | --- | --- | --- |
| General Purpose | 5 | 6 | 3 | 14 |
| Business Critical | 5 | 6 | 5 | 16 |

<sup>1</sup> Addresses used by Azure are shared across all instances in the subnet.   
<sup>2</sup> Addresses used by the virtual machine (VM) group are shared across instances placed inside the same group.   
<sup>3</sup> The total number of addresses used by the instance.

Adding instances to the subnet increases the number of addresses used by the instance and therefore increases the total number of addresses.

### Multi-instance subnets

The formula in this section calculates the number of addresses necessary for multiple instances in a subnet. The formula takes into account the potential of creating new virtual machine groups during a subsequent instance create or update request, and the maintenance window and hardware requirements of virtual clusters.

Use the following formula to calculate the total number of IP addresses based on the number of instances:

`5 + (a * 6) + (b * 10) + (c * 6)` where

- a = number of GP instances
- b = number of BC instances
- c = number of different virtual machine groups

The following list explains the numbers used in the formula:

- 5 is the number of IP addresses reserved by Azure
- 6 addresses per GP instance (3 for the initial deployment, 3 for an eventual scaling operation)
- 10 addresses per BC instance (5 for the initial deployment, 5 for an eventual scaling operation)
- 6 addresses per virtual machine group

> [!IMPORTANT]  
> Since there's a limit to the number of virtual machines that can join a group, a lack of space in an existing group can result in creating a virtual machine group with identical specifications. It's possible for a subnet with a large number of instances to have multiple machine groups with the same configuration, and exceed 9 virtual machine groups.

#### Example 1

You plan to have three General Purpose and two Business Critical instances deployed to the same subnet. All instances have the same maintenance window and run on the same hardware configuration.

To plug these values into the formula:
`5 + (3 * 6) + (2 * 10) + (1 * 6) = 49`

Since IP ranges are defined in powers of 2, to support 49 IP addresses, your subnet requires a minimum IP range of 64 (2^6) for this deployment. Reserve the subnet with a subnet mask of /26.

#### Example 2

You plan to deploy a total of seven instances to the same subnet, four General Purpose, and three Business Critical instances. Three are dev/test instances running on Standard-series hardware with a default maintenance window (virtual machine group 1), while the remaining four are in production, running on Premium-series hardware with a weekend maintenance window (virtual machine group 2).

To plug these values into the formula:
`5 + (4 * 6) + (3 * 10) + (2 * 6) = 71`

Since IP ranges are defined in powers of 2, to support 71 IP addresses, your subnet requires a minimum IP range of 128 (2^7) for this deployment. You need to reserve the subnet with a subnet mask of /25.

> [!CAUTION]  
> It's possible to deploy SQL managed instances to a subnet with fewer IP addresses than the formula suggests. However, you should always consider using bigger subnets instead, to avoid future issues that stem from a lack of IP addresses. Such as, the inability to create more instances within the subnet or scale existing instances.

## Update scenarios

During a scaling operation, instances temporarily require added IP capacity that depends on the service tier.

The following table shows the temporary number of additional IP addresses required for a scale operation that doesn't require creating a new virtual machine group:

| **Service tier** | **Scenario** | **Additional addresses**  |
| --- | --- | --- |
| GP | Scaling vCores | 3 |
| GP | Scaling storage | 0 |
| GP | Switching to BC | 5 |
| BC | Scaling vCores | 5 |
| BC | Scaling storage | 5 |
| BC | Switching to GP | 3 |

Operations that result in creating a new virtual machine group, such as changing a hardware generation or maintenance window, require 6 more permanent addresses for the new group.

## Related content

- [What is Azure SQL Managed Instance?](sql-managed-instance-paas-overview.md)
- [Connectivity architecture for Azure SQL Managed Instance](connectivity-architecture-overview.md)
- [Virtual cluster architecture - Azure SQL Managed Instance](virtual-cluster-architecture.md)
- [Create a virtual network for Azure SQL Managed Instance](virtual-network-subnet-create-arm-template.md)
- [Resolve private domain names in Azure SQL Managed Instance](resolve-private-domain-names.md)
