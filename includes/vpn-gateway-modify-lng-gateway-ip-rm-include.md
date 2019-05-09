---
title: インクルード ファイル
description: インクルード ファイル
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 02/14/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 6505b12b35ee436930ba6571c27db30c12030041
ms.sourcegitcommit: 79038221c1d2172c0677e25a1e479e04f470c567
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/19/2019
ms.locfileid: "56418321"
---
### <a name="gwipnoconnection"></a>ローカル ネットワーク ゲートウェイの "GatewayIpAddress" を変更するには (ゲートウェイに接続していない場合)

接続先の VPN デバイスのパブリック IP アドレスが変更された場合には、ローカル ネットワーク ゲートウェイを変更してアドレスの変更を反映する必要があります。 ゲートウェイに接続していないローカル ネットワーク ゲートウェイに変更を加える場合には、以下の例を使用してください。

この値を変更するときには、同時にアドレス プレフィックスを変更することもできます。 現在の設定を上書きするときは、必ずローカル ネットワーク ゲートウェイの既存の名前を使用してください。 別の名前を使用した場合には、既存のゲートウェイが上書きされずに、新しいローカル ネットワーク ゲートウェイが作成されます。

```azurepowershell-interactive
New-AzLocalNetworkGateway -Name Site1 `
-Location "East US" -AddressPrefix @('10.101.0.0/24','10.101.1.0/24') `
-GatewayIpAddress "5.4.3.2" -ResourceGroupName TestRG1
```

### <a name="gwipwithconnection"></a>ローカル ネットワーク ゲートウェイの "GatewayIpAddress" を変更するには (ゲートウェイに接続している場合)

接続先の VPN デバイスのパブリック IP アドレスが変更された場合には、ローカル ネットワーク ゲートウェイを変更してアドレスの変更を反映する必要があります。 既にゲートウェイ接続が存在する場合はまず、その接続を削除する必要があります。 接続を削除したら、ゲートウェイ IP アドレスを変更し、新しい接続を作成し直します。 同時にアドレス プレフィックスを変更することもできます。 これに伴い、VPN 接続のためにある程度のダウンタイムが発生します。 ゲートウェイの IP アドレスを変更するときに、VPN ゲートウェイを削除する必要はありません。 削除が必要になるのは、接続のみです。
 

1. 接続を削除します。 "Get-AzVirtualNetworkGatewayConnection" コマンドレットを使用して、接続の名前を調べることができます。

   ```azurepowershell-interactive
   Remove-AzVirtualNetworkGatewayConnection -Name VNet1toSite1 `
   -ResourceGroupName TestRG1
   ```
2. "GatewayIpAddress" の値を変更します。 同時にアドレス プレフィックスを変更することもできます。 必ず、ローカル ネットワーク ゲートウェイの既存の名前を使用して、現在の設定を上書きしてください。 そうしないと、既存のゲートウェイが上書きされずに、新しいローカル ネットワーク ゲートウェイが作成されます。

   ```azurepowershell-interactive
   New-AzLocalNetworkGateway -Name Site1 `
   -Location "East US" -AddressPrefix @('10.101.0.0/24','10.101.1.0/24') `
   -GatewayIpAddress "104.40.81.124" -ResourceGroupName TestRG1
   ```
3. 接続を作成します。 この例では、IPsec という接続の種類を構成します。 接続を作成し直すときは、実際の構成で指定した接続の種類を使用してください。 その他の種類の接続については、 [PowerShell コマンドレット](https://msdn.microsoft.com/library/mt603611.aspx) のページを参照してください。  VirtualNetworkGateway の名前は、"Get-AzVirtualNetworkGateway" コマンドレットを実行して取得できます。
   
    変数を設定します。

   ```azurepowershell-interactive
   $local = Get-AzLocalNetworkGateway -Name Site1 -ResourceGroupName TestRG1

   $vnetgw = Get-AzVirtualNetworkGateway -Name VNet1GW -ResourceGroupName TestRG1
   ```
   
    接続を作成します。

   ```azurepowershell-interactive 
   New-AzVirtualNetworkGatewayConnection -Name VNet1Site1 -ResourceGroupName TestRG1 `
   -Location "East US" `
   -VirtualNetworkGateway1 $vnetgw `
   -LocalNetworkGateway2 $local `
   -ConnectionType IPsec -RoutingWeight 10 -SharedKey 'abc123'
   ```