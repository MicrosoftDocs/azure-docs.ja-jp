---
title: "仮想ネットワーク ゲートウェイの削除: PowerShell: Azure Resource Manager | Microsoft Docs"
description: "Resource Manager デプロイメント モデルで、PowerShell を使用して仮想ネットワーク ゲートウェイを削除します。"
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: vpn-gateway
ms.devlang: na
ms.topic: 
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/20/2017
ms.author: cherylmc
translationtype: Human Translation
ms.sourcegitcommit: 0d8472cb3b0d891d2b184621d62830d1ccd5e2e7
ms.openlocfilehash: 3032b09d06a103f9cd915e3803355199243488f9
ms.lasthandoff: 03/21/2017


---
# <a name="delete-a-virtual-network-gateway-using-powershell"></a>PowerShell を使用して仮想ネットワーク ゲートウェイを削除する
> [!div class="op_single_selector"]
> * [Resource Manager - PowerShell](vpn-gateway-delete-vnet-gateway-powershell.md)
> * [クラシック - PowerShell](vpn-gateway-delete-vnet-gateway-classic-powershell.md)
>
>

VPN ゲートウェイ構成の仮想ネットワーク ゲートウェイを削除する際に利用できる方法はいくつかあります。

- テスト環境の場合のように、すべてを削除してやり直す場合は、リソース グループ全体を削除することができます。 リソース グループを削除すると、グループ内のすべてのリソースが削除されます。 この方法は、リソース グループ内のどのリソースも保持する必要がない場合にのみお勧めします。 この方法を使用して一部のリソースだけを選択して削除することはできません。

- リソース グループ内の一部のリソースを保持する必要がある場合は、仮想ネットワーク ゲートウェイの削除が少し複雑になります。 仮想ネットワーク ゲートウェイを削除する前に、まず、そのゲートウェイに依存しているリソースをすべて削除する必要があります。 従う手順は、作成した接続の種類と、各接続に依存するリソースによって異なります。

##<a name="before-beginning"></a>作業を開始する前に

### <a name="1-download-the-latest-azure-resource-manager-powershell-cmdlets"></a>1.最新の Azure Resource Manager PowerShell コマンドレットをダウンロードします。
Azure Resource Manager PowerShell コマンドレットの最新版をダウンロードしてインストールします。 PowerShell コマンドレットのダウンロードとインストールの詳細については、[Azure PowerShell のインストールと構成の方法](/powershell/azureps-cmdlets-docs)に関するページを参照してください。

### <a name="2-connect-to-your-azure-account"></a>2.Azure アカウントに接続します。 
PowerShell コンソールを開き、アカウントに接続します。 接続については、次の例を参考にしてください。

    Login-AzureRmAccount

アカウントのサブスクリプションを確認します。

    Get-AzureRmSubscription

複数のサブスクリプションがある場合は、使用するサブスクリプションを指定します。

    Select-AzureRmSubscription -SubscriptionName "Replace_with_your_subscription_name"

##<a name="S2S"></a>サイト間 VPN ゲートウェイを削除する

S2S 構成の仮想ネットワーク ゲートウェイを削除するには、まず、仮想ネットワーク ゲートウェイに関連する各リソースを削除する必要があります。 リソースは、依存関係に応じた特定の順序で削除する必要があります。 以下の例を使用する場合、一部の値は明確に呼び出す必要がある一方で、その他の値は出力結果になります。 デモンストレーション用に、この例では次の特定の値を使用します。

VNet 名: VNet1<br>
リソース グループ名: RG1<br>
仮想ネットワーク ゲートウェイ名: GW1<br>

次の手順は、Resource Manager デプロイ モデルに該当します。

###<a name="1-get-the-virtual-network-gateway-that-you-want-to-delete"></a>1.削除する仮想ネットワーク ゲートウェイを取得します。

    $Gateway=get-azurermvirtualnetworkgateway -Name "GW1" -ResourceGroupName "RG1"

###<a name="2-check-to-see-if-the-virtual-network-gateway-has-any-connections"></a>2.仮想ネットワーク ゲートウェイに接続があるかどうかを確認します。

    get-azurermvirtualnetworkgatewayconnection -ResourceGroupName "RG1" | where-object {$_.VirtualNetworkGateway1.Id -eq $GW.Id}
    $Conns=get-azurermvirtualnetworkgatewayconnection -ResourceGroupName "RG1" | where-object {$_.VirtualNetworkGateway1.Id -eq $GW.Id}

###<a name="3-delete-all-connections"></a>3.すべての接続を削除します。
各接続の削除の確認を求めるメッセージが表示される場合があります。

    $Conns | ForEach-Object {Remove-AzureRmVirtualNetworkGatewayConnection -Name $_.name -ResourceGroupName $_.ResourceGroupName}

###<a name="4-get-the-list-of-the-corresponding-local-network-gateways"></a>4.対応するローカル ネットワーク ゲートウェイの一覧を取得します。

    $LNG=Get-AzureRmLocalNetworkGateway -ResourceGroupName "RG1" | where-object {$_.Id -In $Conns.LocalNetworkGateway2.Id}
    
###<a name="5-delete-the-local-network-gateways"></a>5.ローカル ネットワーク ゲートウェイを削除します。
各ローカル ネットワーク ゲートウェイの削除の確認を求めるメッセージが表示される場合があります。

    $LNG | ForEach-Object {Remove-AzureRmLocalNetworkGateway -Name $_.Name -ResourceGroupName $_.ResourceGroupName}

###<a name="6-delete-the-virtual-network-gateway"></a>6.仮想ネットワーク ゲートウェイを削除します。
ゲートウェイの削除の確認を求めるメッセージが表示される場合があります。

>[!NOTE]
> S2S 構成に加えてこの VNet に対する P2S 構成がある場合は、仮想ネットワーク ゲートウェイを削除すると、警告なしにすべての P2S クライアントが自動的に切断されます。
>
>

    Remove-AzureRmVirtualNetworkGateway -Name "GW1" -ResourceGroupName "RG1"

###<a name="7-get-the-ip-configurations-of-the-virtual-network-gateway"></a>7.仮想ネットワーク ゲートウェイの IP 構成を取得します。

    $GWIpConfigs = $Gateway.IpConfigurations

###<a name="8-get-the-list-of-public-ip-addresses-used-for-this-virtual-network-gateway"></a>8.この仮想ネットワーク ゲートウェイに使用されるパブリック IP アドレスの一覧を取得します。 
仮想ネットワーク ゲートウェイがアクティブ/アクティブであった場合は、2 つのパブリック IP アドレスが表示されます。

    $PubIP=Get-AzureRmPublicIpAddress | where-object {$_.Id -In $GWIpConfigs.PublicIpAddress.Id}

###<a name="9-delete-the-public-ips"></a>9.パブリック IP を削除します。

    $PubIP | foreach-object {remove-azurermpublicIpAddress -Name $_.Name -ResourceGroupName "RG1"}

###<a name="10-delete-the-gateway-subnet-and-set-the-configuration"></a>10.ゲートウェイ サブネットを削除し、構成を設定します。

    $GWSub = Get-AzureRmVirtualNetwork -ResourceGroupName "RG1" -Name "VNet1" | Remove-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet"
    Set-AzureRmVirtualNetwork -VirtualNetwork $GWSub

##<a name="v2v"></a>VNet 間 VPN ゲートウェイを削除する

V2V 構成の仮想ネットワーク ゲートウェイを削除するには、まず、仮想ネットワーク ゲートウェイに関連する各リソースを削除する必要があります。 リソースは、依存関係に応じた特定の順序で削除する必要があります。 以下の例を使用する場合、一部の値は明確に呼び出す必要がある一方で、その他の値は出力結果になります。 デモンストレーション用に、この例では次の特定の値を使用します。

VNet 名: VNet1<br>
リソース グループ名: RG1<br>
仮想ネットワーク ゲートウェイ名: GW1<br>

次の手順は、Resource Manager デプロイ モデルに該当します。

###<a name="1-get-the-virtual-network-gateway-that-you-want-to-delete"></a>1.削除する仮想ネットワーク ゲートウェイを取得します。

    $Gateway=get-azurermvirtualnetworkgateway -Name "GW1" -ResourceGroupName "RG1"

###<a name="2-check-to-see-if-the-virtual-network-gateway-has-any-connections"></a>2.仮想ネットワーク ゲートウェイに接続があるかどうかを確認します。

    get-azurermvirtualnetworkgatewayconnection -ResourceGroupName "RG1" | where-object {$_.VirtualNetworkGateway1.Id -eq $GW.Id}
 
別のリソース グループに含まれている、仮想ネットワーク ゲートウェイへの他の接続が存在する可能性があります。 追加の各リソース グループで、追加の接続を確認します。 この例では、RG2 からの接続を確認しています。 仮想ネットワーク ゲートウェイへの接続が存在する可能性があるリソース グループごとに、これを実行します。

    get-azurermvirtualnetworkgatewayconnection -ResourceGroupName "RG2" | where-object {$_.VirtualNetworkGateway2.Id -eq $GW.Id}

###<a name="3-get-the-list-of-connections-in-both-directions"></a>3.双方向の接続の一覧を取得します。
これは VNet 間の構成であるため、双方向の接続の一覧が必要です。

    $ConnsL=get-azurermvirtualnetworkgatewayconnection -ResourceGroupName "RG1" | where-object {$_.VirtualNetworkGateway1.Id -eq $GW.Id}
 
この例では、RG2 からの接続を確認しています。 仮想ネットワーク ゲートウェイへの接続が存在する可能性があるリソース グループごとに、これを実行します。
 
    $ConnsR=get-azurermvirtualnetworkgatewayconnection -ResourceGroupName "<NameOfResourceGroup2>" | where-object {$_.VirtualNetworkGateway2.Id -eq $GW.Id}

###<a name="4-delete-all-connections"></a>4.すべての接続を削除します。
各接続の削除の確認を求めるメッセージが表示される場合があります。

    $ConnsL | ForEach-Object {Remove-AzureRmVirtualNetworkGatewayConnection -Name $_.name -ResourceGroupName $_.ResourceGroupName}
    $ConnsR | ForEach-Object {Remove-AzureRmVirtualNetworkGatewayConnection -Name $_.name -ResourceGroupName $_.ResourceGroupName}

###<a name="5-delete-the-virtual-network-gateway"></a>5.仮想ネットワーク ゲートウェイを削除します。
仮想ネットワーク ゲートウェイの削除の確認を求めるメッセージが表示される場合があります。

>[!NOTE]
> V2V 構成に加えて VNet に対する P2S 構成がある場合は、仮想ネットワーク ゲートウェイを削除すると、警告なしにすべての P2S クライアントが自動的に切断されます。
>
>

    Remove-AzureRmVirtualNetworkGateway -Name "GW1" -ResourceGroupName "RG1"

###<a name="6-get-the-ip-configurations-of-the-virtual-network-gateway"></a>6.仮想ネットワーク ゲートウェイの IP 構成を取得します。

    $GWIpConfigs = $Gateway.IpConfigurations

###<a name="7-get-the-list-of-public-ip-addresses-used-for-this-virtual-network-gateway"></a>7.この仮想ネットワーク ゲートウェイに使用されるパブリック IP アドレスの一覧を取得します。 
仮想ネットワーク ゲートウェイがアクティブ/アクティブであった場合は、2 つのパブリック IP アドレスが表示されます。

    $PubIP=Get-AzureRmPublicIpAddress | where-object {$_.Id -In $GWIpConfigs.PublicIpAddress.Id}

###<a name="8-delete-the-public-ips"></a>8.パブリック IP を削除します。
パブリック IP の削除の確認を求めるメッセージが表示される場合があります。

    $PubIP | foreach-object {remove-azurermpublicIpAddress -Name $_.Name -ResourceGroupName "<NameOfResourceGroup1>"}

###<a name="9-delete-the-gateway-subnet-and-set-the-configuration"></a>9.ゲートウェイ サブネットを削除し、構成を設定します。
 
    $GWSub = Get-AzureRmVirtualNetwork -ResourceGroupName "RG1" -Name "VNet1" | Remove-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet"
    Set-AzureRmVirtualNetwork -VirtualNetwork $GWSub

##<a name="deletep2s"></a>ポイント対サイト VPN ゲートウェイを削除する

P2S 構成の仮想ネットワーク ゲートウェイを削除するには、まず、仮想ネットワーク ゲートウェイに関連する各リソースを削除する必要があります。 リソースは、依存関係に応じた特定の順序で削除する必要があります。 以下の例を使用する場合、一部の値は明確に呼び出す必要がある一方で、その他の値は出力結果になります。 デモンストレーション用に、この例では次の特定の値を使用します。

VNet 名: VNet1<br>
リソース グループ名: RG1<br>
仮想ネットワーク ゲートウェイ名: GW1<br>

次の手順は、Resource Manager デプロイ モデルに該当します。


>[!NOTE]
> VPN ゲートウェイを削除すると、接続しているすべてのクライアントが VNet から警告なしで切断されます。
>
>

###<a name="1-get-the-virtual-network-gateway-that-you-want-to-delete"></a>1.削除する仮想ネットワーク ゲートウェイを取得します。

    $Gateway=get-azurermvirtualnetworkgateway -Name "GW1" -ResourceGroupName "RG1"

###<a name="2-delete-the-virtual-network-gateway"></a>2.仮想ネットワーク ゲートウェイを削除します。
仮想ネットワーク ゲートウェイの削除の確認を求めるメッセージが表示される場合があります。

    Remove-AzureRmVirtualNetworkGateway -Name "GW1" -ResourceGroupName "RG1"

###<a name="3-get-the-ip-configurations-of-the-virtual-network-gateway"></a>手順 3.仮想ネットワーク ゲートウェイの IP 構成を取得します。

    $GWIpConfigs = $Gateway.IpConfigurations

###<a name="4-get-the-list-of-public-ip-addresses-used-for-this-virtual-network-gateway"></a>4.この仮想ネットワーク ゲートウェイに使用されるパブリック IP アドレスの一覧を取得します。 
仮想ネットワーク ゲートウェイがアクティブ/アクティブであった場合は、2 つのパブリック IP アドレスが表示されます。

    $PubIP=Get-AzureRmPublicIpAddress | where-object {$_.Id -In $GWIpConfigs.PublicIpAddress.Id}

###<a name="5-delete-the-public-ips"></a>5.パブリック IP を削除します。
パブリック IP の削除の確認を求めるメッセージが表示される場合があります。

    $PubIP | foreach-object {remove-azurermpublicIpAddress -Name $_.Name -ResourceGroupName "<NameOfResourceGroup1>"}

###<a name="6-delete-the-gateway-subnet-and-set-the-configuration"></a>6.ゲートウェイ サブネットを削除し、構成を設定します。
 
    $GWSub = Get-AzureRmVirtualNetwork -ResourceGroupName "RG1" -Name "VNet1" | Remove-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet"
    Set-AzureRmVirtualNetwork -VirtualNetwork $GWSub

##<a name="delete"></a>リソース グループを削除して VPN ゲートウェイを削除する

どのリソースも保持する必要がなく、単に最初からやり直したい場合は、リソース グループ全体を削除できます。 すべてを削除するには、これが簡単な方法です。 リソース グループ全体を削除する際に、削除するリソースについて選択することはできません。 そのため、この例を実行する前に、これが実行したいことであるかどうかを確認してください。

次の手順は、Resource Manager デプロイ モデルに該当します。

### <a name="1-get-a-list-of-all-the-resource-groups-in-your-subscription"></a>1.サブスクリプションのすべてのリソース グループの一覧を取得します。

    Get-AzureRmResourceGroup
### <a name="2-locate-the-resource-group-that-you-want-to-delete"></a>2.削除するリソース グループを探します。
削除するリソース グループを見つけ、そのリソース グループ内のリソースの一覧を表示します。 この例では、リソース グループの名前は RG1 です。 すべてのリソースの一覧を取得するように、例を変更します。

    Find-AzureRmResource -ResourceGroupNameContains RG1

### <a name="3-verify-the-resources-in-the-list"></a>3.一覧のリソースを確認します。
一覧が返されたら、内容を調べて、リソース グループ内のすべてのリソースのほか、リソース グループ自体も削除してよいことを確認します。 

### <a name="4-delete-the-resource-group-and-resources"></a>4.リソース グループとリソースを削除します。
リソース グループと、リソース グループに含まれているすべてのリソースを削除するには、例を変更して実行します。

    Remove-AzureRmResourceGroup -Name RG1

### <a name="5-check-the-status"></a>5.状態を確認します。
Azure によってすべてのリソースが削除されるまで、しばらく時間がかかります。 リソース グループの状態を確認するには、このコマンドレットを使用します。

    Get-AzureRmResourceGroup -ResourceGroupName RG1

結果が返され、"Succeeded" と表示されます。

    ResourceGroupName : RG1
    Location          : eastus
    ProvisioningState : Succeeded


