---
title: Quota types in Azure Stack | Microsoft Docs
description: Review the different quota types available for services and resources in Azure Stack.
services: azure-stack
documentationcenter: 
author: ErikjeMS
manager: byronr
editor: 
ms.assetid: 
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 8/23/2017
ms.author: erikje
ms.translationtype: HT
ms.sourcegitcommit: 646886ad82d47162a62835e343fcaa7dadfaa311
ms.openlocfilehash: 9c65abd596b1a67175a4f91558c318f16ddbb11f
ms.contentlocale: ja-jp
ms.lasthandoff: 08/25/2017

---
# <a name="quota-types-in-azure-stack"></a>Quota types in Azure Stack
[Quotas](azure-stack-plan-offer-quota-overview.md#plans) define the limits of resources that a user subscription can provision or consume. For example, a quota might allow a user to create up to five VMs. Each resource can have its own types of quotas.

## <a name="compute-quota-types"></a>Compute quota types
| **Type** | **Default value** | **Description** |
| --- | --- | --- |
| Max number of virtual machines |50 | The maximum number of virtual machines that a subscription can create in this location. |
| Max number of virtual machine cores |100 | The maximum number of cores that a subscription can create in this location (for example, an A3 VM has four cores). |
| Max number of availability sets |10 | The maximum number of availability sets that can be created in this location. |
| Max number of virtual machine scale sets |100 | The maximum number of virtual machine scale sets that can be created in this location. |

> [!NOTE]
> Compute quotas are not enforced in this technical preview.
> 
> 

## <a name="storage-quota-types"></a>Storage quota types
| **Item** | **Default value** | **Description** |
| --- | --- | --- |
| Maximum capacity (GB) |500 |Total storage capacity that can be consumed by a subscription in this location. |
| Total number of storage accounts |20 |The maximum number of storage accounts that a subscription can create in this location. |

## <a name="network-quota-types"></a>Network quota types
| **Item** | **Default value** | **Description** |
| --- | --- | --- |
| Max public IPs |50 |The maximum number of public IPs that a subscription can create in this location. |
| Max virtual networks |50 |The maximum number of virtual networks that a subscription can create in this location. |
| Max virtual network gateways |1 |The maximum number of virtual network gateways (VPN Gateways) that a subscription can create in this location. |
| Max network connections |2 |The maximum number of network connections (point-to-point or site-to-site) that a subscription can create across all virtual network gateways in this location. |
| Max load balancers |50 |The maximum number of load balancers that a subscription can create in this location. |
| Max NICs |100 |The maximum number of network interfaces that a subscription can create in this location. |
| Max network security groups |50 |The maximum number of network security groups that a subscription can create in this location. |

## <a name="view-an-existing-quota"></a>View an existing quota
1. Click **More services** > **Resource Providers**.
2. Select the service with the quota that you want to view.
3. Click **Quotas**, and select the quota you want to view.

## <a name="next-steps"></a>Next steps
[Learn more about plans, offers, and quotas.](azure-stack-plan-offer-quota-overview.md)

[Create quotas while creating a plan.](azure-stack-create-plan.md)

