---
title: 従来の Azure 仮想ネットワーク VPN ゲートウェイ SKU | Microsoft Docs
description: 古い仮想ネットワーク ゲートウェイ SKU (Basic、Standard、HighPerformance) を使用する方法。
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: jpconnock
editor: ''
tags: azure-resource-manager,azure-service-management
ms.assetid: ''
ms.service: vpn-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/10/2019
ms.author: cherylmc
ms.openlocfilehash: 00f1677e2691f9be5bb4584b07ca00340a52b1e1
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/13/2019
ms.locfileid: "67056443"
---
# <a name="working-with-virtual-network-gateway-skus-legacy-skus"></a>仮想ネットワーク ゲートウェイ SKU (従来の SKU) の使用

この記事には、従来の (古い) 仮想ネットワーク ゲートウェイ SKU に関する情報が含まれています。 従来の SKU は、作成済みの VPN ゲートウェイの両方のデプロイメント モデルで引き続き動作します。 クラシック VPN ゲートウェイでは、既存のゲートウェイと新しいゲートウェイの両方に対して従来の SKU が引き続き使用されます。 新しい Resource Manager VPN ゲートウェイを作成する場合は、新しいゲートウェイ SKU を使用します。 新しい SKU については、「[VPN Gateway について](vpn-gateway-about-vpngateways.md)」を参照してください。

## <a name="gwsku"></a>ゲートウェイの SKU

[!INCLUDE [Legacy gateway SKUs](../../includes/vpn-gateway-gwsku-legacy-include.md)]

レガシ ゲートウェイの価格は、[ExpressRoute の価格ページ](https://azure.microsoft.com/pricing/details/expressroute)にある「**仮想ネットワーク ゲートウェイ**」セクションで表示できます。

## <a name="agg"></a>SKU の予測される合計スループット

[!INCLUDE [Aggregated throughput by legacy SKU](../../includes/vpn-gateway-table-gwtype-legacy-aggtput-include.md)]

## <a name="config"></a>SKU と VPN の種類別のサポートされる構成

[!INCLUDE [Table requirements for old SKUs](../../includes/vpn-gateway-table-requirements-legacy-sku-include.md)]

## <a name="resize"></a>ゲートウェイのサイズを変更する

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

使用しているゲートウェイのサイズを、同じ SKU ファミリ内のゲートウェイ SKU のサイズに変更できます。 たとえば、Standard SKU の場合は、HighPerformance SKU にサイズ変更可能です。 ただし、古い SKU と新しい SKU ファミリとの間で VPN ゲートウェイのサイズを変更することはできません。 たとえば、Standard SKU から VpnGw2 SKU にしたり、Basic SKU から VpnGw1 にしたりすることはできません。

クラシック デプロイ モデルのゲートウェイのサイズを変更するには、次のコマンドを使用します。

```powershell
Resize-AzureVirtualNetworkGateway -GatewayId <Gateway ID> -GatewaySKU HighPerformance
```

Resource Manager デプロイ モデルのゲートウェイのサイズを変更するには、PowerShell で次のコマンドを使用します。

```powershell
$gw = Get-AzVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg
Resize-AzVirtualNetworkGateway -VirtualNetworkGateway $gw -GatewaySku HighPerformance
```
ゲートウェイのサイズ変更は、Azure Portal でも行うことができます。

## <a name="change"></a>新しいゲートウェイ SKU に変更する

[!INCLUDE [Change to the new SKUs](../../includes/vpn-gateway-gwsku-change-legacy-sku-include.md)]

## <a name="next-steps"></a>次の手順

新しいゲートウェイ SKU の詳細については、「[ゲートウェイの SKU](vpn-gateway-about-vpngateways.md#gwsku)」を参照してください。

構成設定の詳細については、「[VPN ゲートウェイの構成設定について](vpn-gateway-about-vpn-gateway-settings.md)」を参照してください。
