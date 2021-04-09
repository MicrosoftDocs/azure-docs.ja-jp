---
title: 従来の Azure 仮想ネットワーク VPN ゲートウェイ SKU
description: 古い仮想ネットワーク ゲートウェイ SKU (Basic、Standard、HighPerformance) を使用する方法。
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: article
ms.date: 08/15/2019
ms.author: cherylmc
ms.openlocfilehash: 9c5e6d5aca51bd560a46837ba47de86362665773
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "84687788"
---
# <a name="working-with-virtual-network-gateway-skus-legacy-skus"></a>仮想ネットワーク ゲートウェイ SKU (従来の SKU) の使用

この記事には、従来の (古い) 仮想ネットワーク ゲートウェイ SKU に関する情報が含まれています。 従来の SKU は、作成済みの VPN ゲートウェイの両方のデプロイメント モデルで引き続き動作します。 クラシック VPN ゲートウェイでは、既存のゲートウェイと新しいゲートウェイの両方に対して従来の SKU が引き続き使用されます。 新しい Resource Manager VPN ゲートウェイを作成する場合は、新しいゲートウェイ SKU を使用します。 新しい SKU については、「[VPN Gateway について](vpn-gateway-about-vpngateways.md)」を参照してください。

## <a name="gateway-skus"></a><a name="gwsku"></a>ゲートウェイの SKU

[!INCLUDE [Legacy gateway SKUs](../../includes/vpn-gateway-gwsku-legacy-include.md)]

レガシ ゲートウェイの価格は、[ExpressRoute の価格ページ](https://azure.microsoft.com/pricing/details/expressroute)にある「**仮想ネットワーク ゲートウェイ**」セクションで表示できます。

## <a name="estimated-aggregate-throughput-by-sku"></a><a name="agg"></a>SKU の予測される合計スループット

[!INCLUDE [Aggregated throughput by legacy SKU](../../includes/vpn-gateway-table-gwtype-legacy-aggtput-include.md)]

## <a name="supported-configurations-by-sku-and-vpn-type"></a><a name="config"></a>SKU と VPN の種類別のサポートされる構成

[!INCLUDE [Table requirements for old SKUs](../../includes/vpn-gateway-table-requirements-legacy-sku-include.md)]

## <a name="resize-a-gateway"></a><a name="resize"></a>ゲートウェイのサイズを変更する

使用しているゲートウェイのサイズを、同じ SKU ファミリ内のゲートウェイ SKU のサイズに変更できます。 たとえば、Standard SKU の場合は、HighPerformance SKU にサイズ変更可能です。 ただし、古い SKU と新しい SKU ファミリとの間で VPN ゲートウェイのサイズを変更することはできません。 たとえば、Standard SKU から VpnGw2 SKU にしたり、Basic SKU から VpnGw1 にしたりすることはできません。

### <a name="resource-manager"></a>リソース マネージャー

Resource Manager デプロイ モデルのゲートウェイのサイズを変更するには、PowerShell で次のコマンドを使用します。

```powershell
$gw = Get-AzVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg
Resize-AzVirtualNetworkGateway -VirtualNetworkGateway $gw -GatewaySku HighPerformance
```

ゲートウェイのサイズ変更は、Azure Portal でも行うことができます。

### <a name="classic"></a><a name="classicresize"></a>クラシック

クラシック デプロイ モデル用のゲートウェイのサイズを変更するには、サービス管理の PowerShell コマンドレットを使用する必要があります。 次のコマンドを使用します。

```powershell
Resize-AzureVirtualNetworkGateway -GatewayId <Gateway ID> -GatewaySKU HighPerformance
```

## <a name="change-to-the-new-gateway-skus"></a><a name="change"></a>新しいゲートウェイ SKU に変更する

[!INCLUDE [Change to the new SKUs](../../includes/vpn-gateway-gwsku-change-legacy-sku-include.md)]

## <a name="next-steps"></a>次のステップ

新しいゲートウェイ SKU の詳細については、「[ゲートウェイの SKU](vpn-gateway-about-vpngateways.md#gwsku)」を参照してください。

構成設定の詳細については、「[VPN ゲートウェイの構成設定について](vpn-gateway-about-vpn-gateway-settings.md)」を参照してください。
