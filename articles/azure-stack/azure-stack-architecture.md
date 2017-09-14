---
title: Microsoft Azure Stack Development Kit architecture | Microsoft Docs
description: View the Microsoft Azure Stack Development Kit architecture.
services: azure-stack
documentationcenter: 
author: heathl17
manager: byronr
editor: 
ms.assetid: a7e61ea4-be2f-4e55-9beb-7a079f348e05
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/03/2017
ms.author: helaw
ms.translationtype: HT
ms.sourcegitcommit: bde1bc7e140f9eb7bb864c1c0a1387b9da5d4d22
ms.openlocfilehash: ece8cb785ffcceaed476fa729e7471dc7831c155
ms.contentlocale: ja-jp
ms.lasthandoff: 07/21/2017

---
# <a name="microsoft-azure-stack-development-kit-architecture"></a>Microsoft Azure Stack Development Kit architecture
The Azure Stack Development Kit is a single-node deployment of Azure Stack. All the components are installed in virtual machines running on a single host machine. 

## <a name="logical-architecture-diagram"></a>Logical architecture diagram
The following diagram illustrates the logical architecture of the Azure Stack development kit and its components.

![](media/azure-stack-architecture/image1.png)

## <a name="virtual-machine-roles"></a>Virtual machine roles
The Azure Stack development kit offers services using the following VMs on the host:

| Name | Description |
| ----- | ----- |
| **AzS-ACS01** | Azure Stack storage services.|
| **AzS-ADFS01** | Active Directory Federation Services (ADFS).  |
| **AzS-BGPNAT01** | Edge router and provides NAT and VPN capabilities for Azure Stack. |
| **AzS-CA01** | Certificate authority services for Azure Stack role services.|
| **AzS-DC01** | Active Directory, DNS, and DHCP services for Microsoft Azure Stack.|
| **AzS-ERCS01** | Emergency Recovery Console VM. |
| **AzS-GWY01** | Edge gateway services such as VPN site-to-site connections for tenant networks.|
| **AzS-NC01** | Network Controller, which manages Azure Stack network services.  |
| **AzS-SLB01** | Load balancing multiplexer services in Azure Stack for both tenants and Azure Stack infrastructure services.  |
| **AzS-SQL01** | Internal data store for Azure Stack infrastructure roles.  |
| **AzS-WAS01** | Azure Stack administrative portal and Azure Resource Manager services.|
| **AzS-WASP01**| Azure Stack user (tenant) portal and Azure Resource Manager services.|
| **AzS-XRP01** | Infrastructure management controller for Microsoft Azure Stack, including the Compute, Network, and Storage resource providers.|


## <a name="next-steps"></a>Next steps
[Deploy Azure Stack](azure-stack-deploy.md)

[First scenarios to try](azure-stack-first-scenarios.md)


