---
title: "従来の Azure 仮想ネットワーク ゲートウェイ SKU | Microsoft Docs"
description: "以前の仮想ネットワーク ゲートウェイ SKU について説明します。"
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: timlt
editor: 
tags: azure-resource-manager,azure-service-management
ms.assetid: 
ms.service: vpn-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/01/2017
ms.author: cherylmc
ms.translationtype: HT
ms.sourcegitcommit: 1e6fb68d239ee3a66899f520a91702419461c02b
ms.openlocfilehash: 3b2126b1ecd1613950bbf311ae08fafd4af0d51f
ms.contentlocale: ja-jp
ms.lasthandoff: 08/16/2017

---
# <a name="working-with-virtual-network-gateway-skus-legacy-skus"></a>仮想ネットワーク ゲートウェイ SKU (従来の SKU) の使用

この記事には、従来の (古い) 仮想ネットワーク ゲートウェイ SKU に関する情報が含まれています。 従来の SKU は、作成済みの VPN ゲートウェイの両方のデプロイメント モデルで引き続き動作します。 クラシック VPN ゲートウェイでは、既存のゲートウェイと新しいゲートウェイの両方に対して従来の SKU が引き続き使用されます。 新しい Resource Manager VPN ゲートウェイを作成する場合は、新しいゲートウェイ SKU を使用します。 新しい SKU については、「[VPN Gateway について](vpn-gateway-about-vpngateways.md)」を参照してください。

## <a name="gwsku"></a>ゲートウェイの SKU

[!INCLUDE [Legacy gateway SKUs](../../includes/vpn-gateway-gwsku-legacy-include.md)]

## <a name="agg"></a>SKU の予測される合計スループット

[!INCLUDE [Aggregated throughput by legacy SKU](../../includes/vpn-gateway-table-gwtype-legacy-aggtput-include.md)]

## <a name="config"></a>SKU と VPN の種類別のサポートされる構成

[!INCLUDE [Table requirements for old SKUs](../../includes/vpn-gateway-table-requirements-legacy-sku-include.md)]

## <a name="resize"></a>ゲートウェイのサイズを変更する (ゲートウェイ SKU を変更する)

同じ SKU ファミリ内のゲートウェイ SKU のサイズを変更することができます。 たとえば、Standard SKU の場合は、HighPerformance SKU にサイズ変更可能です。 古い SKU と新しい SKU ファミリとの間で VPN ゲートウェイのサイズを変更することはできません。 たとえば、Standard SKU を VpnGw2 SKU にすることはできません。 

クラシック デプロイメント モデルのゲートウェイ SKU のサイズを変更するには、次のコマンドを使用します。

```powershell
Resize-AzureVirtualNetworkGateway -GatewayId <Gateway ID> -GatewaySKU HighPerformance
```

Resource Manager デプロイメント モデルのゲートウェイ SKU のサイズを変更するには、次のコマンドを使用します。

```powershell
$gw = Get-AzureRmVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg
Resize-AzureRmVirtualNetworkGateway -VirtualNetworkGateway $gw -GatewaySku HighPerformance
```

## <a name="migrate"></a>新しいゲートウェイ SKU に移行する

Resource Manager デプロイメント モデルで作業している場合は、新しいゲートウェイ SKU に移行することができます。 クラシック デプロイメント モデルで作業している場合は、新しい SKU に移行することはできません。したがって、引き続き従来の SKU を使用する必要があります。

[!INCLUDE [Migrate SKU](../../includes/vpn-gateway-migrate-legacy-sku-include.md)]

## <a name="next-steps"></a>次のステップ

新しいゲートウェイ SKU の詳細については、「[ゲートウェイの SKU](vpn-gateway-about-vpngateways.md#gwsku)」を参照してください。

構成設定の詳細については、「[VPN ゲートウェイの構成設定について](vpn-gateway-about-vpn-gateway-settings.md)」を参照してください。
