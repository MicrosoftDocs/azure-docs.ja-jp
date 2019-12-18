---
title: Azure VPN Gateway:ゲートウェイの削除:PowerShell
description: Resource Manager デプロイ モデルで、PowerShell を使用して仮想ネットワーク ゲートウェイを削除します。
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.date: 02/07/2019
ms.author: cherylmc
ms.topic: conceptual
ms.openlocfilehash: 2c02b656f8d7879115d25516bf49f49d9921a290
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/17/2019
ms.locfileid: "74146333"
---
# <a name="delete-a-virtual-network-gateway-using-powershell"></a>PowerShell を使用して仮想ネットワーク ゲートウェイを削除する
> [!div class="op_single_selector"]
> * [Azure Portal](vpn-gateway-delete-vnet-gateway-portal.md)
> * [PowerShell](vpn-gateway-delete-vnet-gateway-powershell.md)
> * [PowerShell (クラシック)](vpn-gateway-delete-vnet-gateway-classic-powershell.md)
>
>

VPN ゲートウェイ構成の仮想ネットワーク ゲートウェイを削除する際に利用できる方法はいくつかあります。

- テスト環境の場合のように、すべてを削除してやり直す場合は、リソース グループを削除することができます。 リソース グループを削除すると、グループ内のすべてのリソースが削除されます。 この方法は、リソース グループ内のどのリソースも保持する必要がない場合にのみお勧めします。 この方法を使用して一部のリソースだけを選択して削除することはできません。

- リソース グループ内の一部のリソースを保持する必要がある場合は、仮想ネットワーク ゲートウェイの削除が少し複雑になります。 仮想ネットワーク ゲートウェイを削除する前に、まず、そのゲートウェイに依存しているリソースをすべて削除する必要があります。 従う手順は、作成した接続の種類と、各接続に依存するリソースによって異なります。

## <a name="before-beginning"></a>作業を開始する前に

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

### <a name="1-download-the-latest-azure-resource-manager-powershell-cmdlets"></a>1.最新の Azure Resource Manager PowerShell コマンドレットをダウンロードします。

Azure Resource Manager PowerShell コマンドレットの最新版をダウンロードしてインストールします。 PowerShell コマンドレットのダウンロードとインストールの詳細については、[Azure PowerShell のインストールと構成の方法](/powershell/azure/overview)に関するページを参照してください。

### <a name="2-connect-to-your-azure-account"></a>2.Azure アカウントに接続します。

PowerShell コンソールを開き、アカウントに接続します。 接続については、次の例を参考にしてください。

```powershell
Connect-AzAccount
```

アカウントのサブスクリプションを確認します。

```powershell
Get-AzSubscription
```

複数のサブスクリプションがある場合は、使用するサブスクリプションを指定します。

```powershell
Select-AzSubscription -SubscriptionName "Replace_with_your_subscription_name"
```

## <a name="S2S"></a>サイト間 VPN ゲートウェイを削除する

S2S 構成の仮想ネットワーク ゲートウェイを削除するには、まず、仮想ネットワーク ゲートウェイに関連する各リソースを削除する必要があります。 リソースは、依存関係に応じた特定の順序で削除する必要があります。 以下の例を使用する場合、一部の値は指定する必要がありますが、その他の値は出力結果です。 デモンストレーション用に、この例では次の特定の値を使用します。

VNet 名:VNet1<br>
リソース グループ名:RG1<br>
仮想ネットワーク ゲートウェイ名:GW1<br>

次の手順は、Resource Manager デプロイ モデルに該当します。

### <a name="1-get-the-virtual-network-gateway-that-you-want-to-delete"></a>1.削除する仮想ネットワーク ゲートウェイを取得します。

```powershell
$GW=get-Azvirtualnetworkgateway -Name "GW1" -ResourceGroupName "RG1"
```

### <a name="2-check-to-see-if-the-virtual-network-gateway-has-any-connections"></a>2.仮想ネットワーク ゲートウェイに接続があるかどうかを確認します。

```powershell
get-Azvirtualnetworkgatewayconnection -ResourceGroupName "RG1" | where-object {$_.VirtualNetworkGateway1.Id -eq $GW.Id}
$Conns=get-Azvirtualnetworkgatewayconnection -ResourceGroupName "RG1" | where-object {$_.VirtualNetworkGateway1.Id -eq $GW.Id}
```

### <a name="3-delete-all-connections"></a>3.すべての接続を削除します。

各接続の削除の確認を求めるメッセージが表示される場合があります。

```powershell
$Conns | ForEach-Object {Remove-AzVirtualNetworkGatewayConnection -Name $_.name -ResourceGroupName $_.ResourceGroupName}
```

### <a name="4-delete-the-virtual-network-gateway"></a>4.仮想ネットワーク ゲートウェイを削除します。

ゲートウェイの削除の確認を求めるメッセージが表示される場合があります。 S2S 構成に加えてこの VNet に対する P2S 構成がある場合は、仮想ネットワーク ゲートウェイを削除すると、警告なしにすべての P2S クライアントが自動的に切断されます。


```powershell
Remove-AzVirtualNetworkGateway -Name "GW1" -ResourceGroupName "RG1"
```

この時点で、仮想ネットワーク ゲートウェイは削除されています。 次の手順を使用して、使用されなくなったリソースを削除できます。

### <a name="5-delete-the-local-network-gateways"></a>5\. ローカル ネットワーク ゲートウェイを削除します。

対応するローカル ネットワーク ゲートウェイの一覧を取得します。

```powershell
$LNG=Get-AzLocalNetworkGateway -ResourceGroupName "RG1" | where-object {$_.Id -In $Conns.LocalNetworkGateway2.Id}
```

ローカル ネットワーク ゲートウェイを削除します。 各ローカル ネットワーク ゲートウェイの削除の確認を求めるメッセージが表示される場合があります。

```powershell
$LNG | ForEach-Object {Remove-AzLocalNetworkGateway -Name $_.Name -ResourceGroupName $_.ResourceGroupName}
```

### <a name="6-delete-the-public-ip-address-resources"></a>6.パブリック IP アドレス リソースを削除します。

仮想ネットワーク ゲートウェイの IP 構成を取得します。

```powershell
$GWIpConfigs = $Gateway.IpConfigurations
```

この仮想ネットワーク ゲートウェイに使用されるパブリック IP アドレス リソースの一覧を取得します。 仮想ネットワーク ゲートウェイがアクティブ/アクティブであった場合は、2 つのパブリック IP アドレスが表示されます。

```powershell
$PubIP=Get-AzPublicIpAddress | where-object {$_.Id -In $GWIpConfigs.PublicIpAddress.Id}
```

パブリック IP リソースを削除します。

```powershell
$PubIP | foreach-object {remove-AzpublicIpAddress -Name $_.Name -ResourceGroupName "RG1"}
```

### <a name="7-delete-the-gateway-subnet-and-set-the-configuration"></a>7.ゲートウェイ サブネットを削除し、構成を設定します。

```powershell
$GWSub = Get-AzVirtualNetwork -ResourceGroupName "RG1" -Name "VNet1" | Remove-AzVirtualNetworkSubnetConfig -Name "GatewaySubnet"
Set-AzVirtualNetwork -VirtualNetwork $GWSub
```

## <a name="v2v"></a>VNet 間 VPN ゲートウェイを削除する

V2V 構成の仮想ネットワーク ゲートウェイを削除するには、まず、仮想ネットワーク ゲートウェイに関連する各リソースを削除する必要があります。 リソースは、依存関係に応じた特定の順序で削除する必要があります。 以下の例を使用する場合、一部の値は指定する必要がありますが、その他の値は出力結果です。 デモンストレーション用に、この例では次の特定の値を使用します。

VNet 名:VNet1<br>
リソース グループ名:RG1<br>
仮想ネットワーク ゲートウェイ名:GW1<br>

次の手順は、Resource Manager デプロイ モデルに該当します。

### <a name="1-get-the-virtual-network-gateway-that-you-want-to-delete"></a>1.削除する仮想ネットワーク ゲートウェイを取得します。

```powershell
$GW=get-Azvirtualnetworkgateway -Name "GW1" -ResourceGroupName "RG1"
```

### <a name="2-check-to-see-if-the-virtual-network-gateway-has-any-connections"></a>2.仮想ネットワーク ゲートウェイに接続があるかどうかを確認します。

```powershell
get-Azvirtualnetworkgatewayconnection -ResourceGroupName "RG1" | where-object {$_.VirtualNetworkGateway1.Id -eq $GW.Id}
```
 
別のリソース グループに含まれている、仮想ネットワーク ゲートウェイへの他の接続が存在する可能性があります。 追加の各リソース グループで、追加の接続を確認します。 この例では、RG2 からの接続を確認しています。 仮想ネットワーク ゲートウェイへの接続が存在する可能性があるリソース グループごとに、これを実行します。

```powershell
get-Azvirtualnetworkgatewayconnection -ResourceGroupName "RG2" | where-object {$_.VirtualNetworkGateway2.Id -eq $GW.Id}
```

### <a name="3-get-the-list-of-connections-in-both-directions"></a>3.双方向の接続の一覧を取得します。

これは VNet 間の構成であるため、双方向の接続の一覧が必要です。

```powershell
$ConnsL=get-Azvirtualnetworkgatewayconnection -ResourceGroupName "RG1" | where-object {$_.VirtualNetworkGateway1.Id -eq $GW.Id}
```
 
この例では、RG2 からの接続を確認しています。 仮想ネットワーク ゲートウェイへの接続が存在する可能性があるリソース グループごとに、これを実行します。

```powershell
 $ConnsR=get-Azvirtualnetworkgatewayconnection -ResourceGroupName "<NameOfResourceGroup2>" | where-object {$_.VirtualNetworkGateway2.Id -eq $GW.Id}
 ```

### <a name="4-delete-all-connections"></a>4.すべての接続を削除します。

各接続の削除の確認を求めるメッセージが表示される場合があります。

```powershell
$ConnsL | ForEach-Object {Remove-AzVirtualNetworkGatewayConnection -Name $_.name -ResourceGroupName $_.ResourceGroupName}
$ConnsR | ForEach-Object {Remove-AzVirtualNetworkGatewayConnection -Name $_.name -ResourceGroupName $_.ResourceGroupName}
```

### <a name="5-delete-the-virtual-network-gateway"></a>5.仮想ネットワーク ゲートウェイを削除します。

仮想ネットワーク ゲートウェイの削除の確認を求めるメッセージが表示される場合があります。 V2V 構成に加えて VNet に対する P2S 構成がある場合は、仮想ネットワーク ゲートウェイを削除すると、警告なしにすべての P2S クライアントが自動的に切断されます。

```powershell
Remove-AzVirtualNetworkGateway -Name "GW1" -ResourceGroupName "RG1"
```

この時点で、仮想ネットワーク ゲートウェイは削除されています。 次の手順を使用して、使用されなくなったリソースを削除できます。

### <a name="6-delete-the-public-ip-address-resources"></a>6.パブリック IP アドレス リソースを削除します。

仮想ネットワーク ゲートウェイの IP 構成を取得します。

```powershell
$GWIpConfigs = $Gateway.IpConfigurations
```

この仮想ネットワーク ゲートウェイに使用されるパブリック IP アドレス リソースの一覧を取得します。 仮想ネットワーク ゲートウェイがアクティブ/アクティブであった場合は、2 つのパブリック IP アドレスが表示されます。

```powershell
$PubIP=Get-AzPublicIpAddress | where-object {$_.Id -In $GWIpConfigs.PublicIpAddress.Id}
```

パブリック IP リソースを削除します。 パブリック IP の削除の確認を求めるメッセージが表示される場合があります。

```powershell
$PubIP | foreach-object {remove-AzpublicIpAddress -Name $_.Name -ResourceGroupName "<NameOfResourceGroup1>"}
```

### <a name="7-delete-the-gateway-subnet-and-set-the-configuration"></a>7.ゲートウェイ サブネットを削除し、構成を設定します。

```powershell
$GWSub = Get-AzVirtualNetwork -ResourceGroupName "RG1" -Name "VNet1" | Remove-AzVirtualNetworkSubnetConfig -Name "GatewaySubnet"
Set-AzVirtualNetwork -VirtualNetwork $GWSub
```

## <a name="deletep2s"></a>ポイント対サイト VPN ゲートウェイを削除する

P2S 構成の仮想ネットワーク ゲートウェイを削除するには、まず、仮想ネットワーク ゲートウェイに関連する各リソースを削除する必要があります。 リソースは、依存関係に応じた特定の順序で削除する必要があります。 以下の例を使用する場合、一部の値は指定する必要がありますが、その他の値は出力結果です。 デモンストレーション用に、この例では次の特定の値を使用します。

VNet 名:VNet1<br>
リソース グループ名:RG1<br>
仮想ネットワーク ゲートウェイ名:GW1<br>

次の手順は、Resource Manager デプロイ モデルに該当します。


>[!NOTE]
> VPN ゲートウェイを削除すると、接続しているすべてのクライアントが VNet から警告なしで切断されます。
>
>

### <a name="1-get-the-virtual-network-gateway-that-you-want-to-delete"></a>1.削除する仮想ネットワーク ゲートウェイを取得します。

```powershell
$GW=get-Azvirtualnetworkgateway -Name "GW1" -ResourceGroupName "RG1"
```

### <a name="2-delete-the-virtual-network-gateway"></a>2.仮想ネットワーク ゲートウェイを削除します。

仮想ネットワーク ゲートウェイの削除の確認を求めるメッセージが表示される場合があります。

```powershell
Remove-AzVirtualNetworkGateway -Name "GW1" -ResourceGroupName "RG1"
```

この時点で、仮想ネットワーク ゲートウェイは削除されています。 次の手順を使用して、使用されなくなったリソースを削除できます。

### <a name="3-delete-the-public-ip-address-resources"></a>3.パブリック IP アドレス リソースを削除します。

仮想ネットワーク ゲートウェイの IP 構成を取得します。

```powershell
$GWIpConfigs = $Gateway.IpConfigurations
```

この仮想ネットワーク ゲートウェイに使用されるパブリック IP アドレスの一覧を取得します。 仮想ネットワーク ゲートウェイがアクティブ/アクティブであった場合は、2 つのパブリック IP アドレスが表示されます。

```powershell
$PubIP=Get-AzPublicIpAddress | where-object {$_.Id -In $GWIpConfigs.PublicIpAddress.Id}
```

パブリック IP を削除します。 パブリック IP の削除の確認を求めるメッセージが表示される場合があります。

```powershell
$PubIP | foreach-object {remove-AzpublicIpAddress -Name $_.Name -ResourceGroupName "<NameOfResourceGroup1>"}
```

### <a name="4-delete-the-gateway-subnet-and-set-the-configuration"></a>4.ゲートウェイ サブネットを削除し、構成を設定します。

```powershell
$GWSub = Get-AzVirtualNetwork -ResourceGroupName "RG1" -Name "VNet1" | Remove-AzVirtualNetworkSubnetConfig -Name "GatewaySubnet"
Set-AzVirtualNetwork -VirtualNetwork $GWSub
```

## <a name="delete"></a>リソース グループを削除して VPN ゲートウェイを削除する

リソース グループにどのリソースも保持する必要がなく、単に最初からやり直したい場合は、リソース グループ全体を削除できます。 すべてを削除するには、これが簡単な方法です。 次の手順は、Resource Manager デプロイ モデルに該当します。

### <a name="1-get-a-list-of-all-the-resource-groups-in-your-subscription"></a>1.サブスクリプションのすべてのリソース グループの一覧を取得します。

```powershell
Get-AzResourceGroup
```

### <a name="2-locate-the-resource-group-that-you-want-to-delete"></a>2.削除するリソース グループを探します。

削除するリソース グループを見つけ、そのリソース グループ内のリソースの一覧を表示します。 この例では、リソース グループの名前は RG1 です。 すべてのリソースの一覧を取得するように、例を変更します。

```powershell
Find-AzResource -ResourceGroupNameContains RG1
```

### <a name="3-verify-the-resources-in-the-list"></a>3.一覧のリソースを確認します。

一覧が返されたら、内容を調べて、リソース グループ内のすべてのリソースのほか、リソース グループ自体も削除してよいことを確認します。 リソース グループ内の一部のリソースを保持する場合は、この記事の前のセクションに記載した手順を使用して、ゲートウェイを削除します。

### <a name="4-delete-the-resource-group-and-resources"></a>4.リソース グループとリソースを削除します。

リソース グループと、リソース グループに含まれているすべてのリソースを削除するには、例を変更して実行します。

```powershell
Remove-AzResourceGroup -Name RG1
```

### <a name="5-check-the-status"></a>5.状態を確認します。

Azure によってすべてのリソースが削除されるまで、しばらく時間がかかります。 リソース グループの状態を確認するには、このコマンドレットを使用します。

```powershell
Get-AzResourceGroup -ResourceGroupName RG1
```

結果が返され、"Succeeded" と表示されます。

```
ResourceGroupName : RG1
Location          : eastus
ProvisioningState : Succeeded
```
