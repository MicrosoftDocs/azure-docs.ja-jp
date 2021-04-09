---
title: Azure VMware Solution の制限
description: Azure VMware Solution の制限。
ms.topic: include
ms.date: 03/16/2021
ms.openlocfilehash: 0e2359d951f5348b69e95ab7fa046981b2b7b32d
ms.sourcegitcommit: 27cd3e515fee7821807c03e64ce8ac2dd2dd82d2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/16/2021
ms.locfileid: "103622208"
---
<!-- Used in /azure/azure-resource-manager/management/azure-subscription-service-limits.md -->

Azure VMware Solution の上限を次の表に示します。

| **リソース** | **制限** |
| :-- | :-- |
| プライベート クラウドあたりのクラスター数 | 12 |
| クラスターごとのノードの最小数 | 3 |
| クラスターごとのノードの最大数 | 16 |
| プライベート クラウドあたりのノード数 | 64 |
| プライベート クラウドあたりの vCenter 数 | 1  |
| HCX サイトのペアリング数 | Advanced エディションの場合は 3、Enterprise エディションの場合は 10 |
| AVS ExpressRoute にリンクされる SDDC の最大数 | 4 |
| AVS ExpressRoute のポート速度 | 10 Gbps<br />実際の帯域幅は、使用される仮想ネットワーク ゲートウェイによって決まります。 詳細については、「[ExpressRoute の仮想ネットワーク ゲートウェイについて](../../expressroute/expressroute-about-virtual-network-gateways.md)」を参照してください | 
| vWAN を介して公開されるパブリック IP 数 | 100 |
| vSAN の容量制限 | 使用可能な合計の 75% (SLA のために 25% を使用可能に保つ)  |

VMware 固有のその他の制限については、[VMware Configuration Maximums ツール](https://configmax.vmware.com/)を使用してください。
