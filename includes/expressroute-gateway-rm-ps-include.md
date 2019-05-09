---
title: インクルード ファイル
description: インクルード ファイル
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: include
ms.date: 02/21/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 03a56951b68163a9160cc4a57f15354b5f210eb7
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2019
ms.locfileid: "58125097"
---
このタスクの手順では、以下の構成参照一覧の値に基づいて VNet を使用します。 その他の設定と名前についても、一覧で概要を説明します。 ここでは、この一覧内の値に基づいて変数を追加しますが、どの手順でもこの一覧を直接使用しません。 参照として使用する一覧をコピーし、値を独自の値で置き換えることができます。

* Virtual Network 名 = "TestVNet"
* Virtual Network のアドレス空間 = 192.168.0.0/16
* リソース グループ = "TestRG"
* Subnet1 名 = "FrontEnd" 
* Subnet1 アドレス空間 = "192.168.1.0/24"
* ゲートウェイ サブネット名: "GatewaySubnet"。ゲートウェイ サブネットには、常に *GatewaySubnet* という名前を付ける必要があります。
* ゲートウェイ サブネットのアドレス空間 = "192.168.200.0/26"
* リージョン = "米国東部"
* ゲートウェイ名 = "GW"
* ゲートウェイの IP 名 = "GWIP"
* ゲートウェイの IP 構成名 = "gwipconf"
* 種類 = "ExpressRoute"。ExpressRoute 構成には、この種類が必須です。
* ゲートウェイのパブリック IP 名 = "gwpip"

## <a name="add-a-gateway"></a>ゲートウェイを追加する
1. Azure サブスクリプションに接続します。

   [!INCLUDE [Sign in](expressroute-cloud-shell-connect.md)]
2. この演習用に変数を宣言します。 サンプルを編集して、使用する設定を反映します。

   ```azurepowershell-interactive 
   $RG = "TestRG"
   $Location = "East US"
   $GWName = "GW"
   $GWIPName = "GWIP"
   $GWIPconfName = "gwipconf"
   $VNetName = "TestVNet"
   ```
3. 仮想ネットワーク オブジェクトを変数として格納します。

   ```azurepowershell-interactive
   $vnet = Get-AzVirtualNetwork -Name $VNetName -ResourceGroupName $RG
   ```
4. ゲートウェイ サブネットを Virtual Network に追加します。 ゲートウェイ サブネット名は、"GatewaySubnet " にする必要があります。 /27 以上 (/26、/25 など) のゲートウェイ サブネットを作成する必要があります。

   ```azurepowershell-interactive
   Add-AzVirtualNetworkSubnetConfig -Name GatewaySubnet -VirtualNetwork $vnet -AddressPrefix 192.168.200.0/26
   ```
5. 構成を設定します。

   ```azurepowershell-interactive
   $vnet = Set-AzVirtualNetwork -VirtualNetwork $vnet
   ```
6. 変数としてゲートウェイ サブネットを格納します。

   ```azurepowershell-interactive
   $subnet = Get-AzVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -VirtualNetwork $vnet
   ```
7. パブリック IP アドレスを要求します。 IP アドレスは、ゲートウェイを作成する前に要求されます。 使用する IP アドレスは指定できません。IP アドレスは動的に割り当てられます。 この IP アドレスは、次の構成セクションで使用します。 AllocationMethod は動的である必要があります。

   ```azurepowershell-interactive
   $pip = New-AzPublicIpAddress -Name $GWIPName  -ResourceGroupName $RG -Location $Location -AllocationMethod Dynamic
   ```
8. ゲートウェイの構成を作成します。 ゲートウェイの構成で、使用するサブネットとパブリック IP アドレスを定義します。 この手順では、ケーブルの作成時に使用される構成を指定します。 この手順では、ゲートウェイ オブジェクトを実際に作成しません。 次のサンプルを使用して、ゲートウェイの構成を作成します。

   ```azurepowershell-interactive
   $ipconf = New-AzVirtualNetworkGatewayIpConfig -Name $GWIPconfName -Subnet $subnet -PublicIpAddress $pip
   ```
9. ゲートウェイを作成します。 この手順では、 **-GatewayType** が特に重要です。 値 **ExpressRoute**を使用する必要があります。 これらのコマンドレットを実行してからゲートウェイが作成されるまでに 45 分以上かかる場合があります。

   ```azurepowershell-interactive
   New-AzVirtualNetworkGateway -Name $GWName -ResourceGroupName $RG -Location $Location -IpConfigurations $ipconf -GatewayType Expressroute -GatewaySku Standard
   ```

## <a name="verify-the-gateway-was-created"></a>ゲートウェイが作成されていることの確認
次のコマンドを使用して、ゲートウェイが作成されていることを確認します。

```azurepowershell-interactive
Get-AzVirtualNetworkGateway -ResourceGroupName $RG
```

## <a name="resize-a-gateway"></a>ゲートウェイのサイズを変更する
[ゲートウェイ SKU](../articles/expressroute/expressroute-about-virtual-network-gateways.md)には複数の種類があります。 次のコマンドを使用して、ゲートウェイ SKU をいつでも変更できます。

> [!IMPORTANT]
> このコマンドは UltraPerformance ゲートウェイでは使用できません。 ゲートウェイを UltraPerformance ゲートウェイに変更するには、既存の ExpressRoute ゲートウェイを削除してから、新しい UltraPerformance ゲートウェイを作成します。 ゲートウェイを UltraPerformance ゲートウェイからダウングレードするには、UltraPerformance ゲートウェイを削除してから、新しいゲートウェイを作成します。
> 
> 

```azurepowershell-interactive
$gw = Get-AzVirtualNetworkGateway -Name $GWName -ResourceGroupName $RG
Resize-AzVirtualNetworkGateway -VirtualNetworkGateway $gw -GatewaySku HighPerformance
```

## <a name="remove-a-gateway"></a>ゲートウェイを削除する
次のコマンドを使用してゲートウェイを削除します。

```azurepowershell-interactive
Remove-AzVirtualNetworkGateway -Name $GWName -ResourceGroupName $RG
```
