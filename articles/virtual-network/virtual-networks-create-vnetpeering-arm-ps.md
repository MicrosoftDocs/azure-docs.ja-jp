---
title: "Azure の仮想ネットワーク ピアリング - PowerShell | Microsoft Docs"
description: "PowerShell を使用して仮想ネットワーク ピアリングを作成する方法を説明します。"
services: virtual-network
documentationcenter: 
author: NarayanAnnamalai
manager: jefco
editor: 
tags: azure-resource-manager
ms.assetid: dac579bd-7545-461a-bdac-301c87434c84
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/14/2016
ms.author: narayan; annahar
translationtype: Human Translation
ms.sourcegitcommit: 3fcd6583b415cea6b2151651297c55c93e59c796
ms.openlocfilehash: b0375a99f5ea3d6af2d3ead382f9a43f1fd285f0
ms.lasthandoff: 02/22/2017


---
# <a name="create-a-virtual-network-peering-using-azure-powershell"></a>PowerShell を使用した仮想ネットワーク ピアリングの作成
[!INCLUDE [virtual-networks-create-vnet-selectors-arm-include](../../includes/virtual-networks-create-vnetpeering-selectors-arm-include.md)]

[!INCLUDE [virtual-networks-create-vnet-intro](../../includes/virtual-networks-create-vnetpeering-intro-include.md)]

[!INCLUDE [virtual-networks-create-vnet-scenario-basic-include](../../includes/virtual-networks-create-vnetpeering-scenario-basic-include.md)]

PowerShell を使用して VNet ピアリングを作成するには、次の手順に従います。

1. Azure PowerShell を初めて使用する場合は、 [Azure PowerShell のインストールおよび構成方法](/powershell/azureps-cmdlets-docs) を参照し、このページにある手順をすべて最後まで実行し、Azure にサインインしてサブスクリプションを選択します。

    > [!NOTE]
    > VNet ピアリングを管理するための PowerShell コマンドレットは、 [Azure PowerShell 1.6](http://www.powershellgallery.com/packages/Azure/1.6.0)
    >

2. 仮想ネットワーク オブジェクトを読み取ります。

    ```powershell
    $vnet1 = Get-AzureRmVirtualNetwork -ResourceGroupName vnet101 -Name vnet1
    $vnet2 = Get-AzureRmVirtualNetwork -ResourceGroupName vnet101 -Name vnet2
    ```

3. VNET ピアリングを確立するには、2 つのリンク (各方向につき&1; つ) を作成する必要があります。 まず、VNet1 から VNet2 への VNET ピアリング リンクを次の手順で作成します。

    ```powershell
    Add-AzureRmVirtualNetworkPeering -Name LinkToVNet2 -VirtualNetwork $vnet1 -RemoteVirtualNetworkId $vnet2.Id
    ```

    返される出力:
        
        Name            : LinkToVNet2
        Id: /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/vnet101/providers/Microsoft.Network/virtualNetworks/vnet1/virtualNetworkPeerings/LinkToVNet2
        Etag            : W/"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
        ResourceGroupName    : vnet101
        VirtualNetworkName    : vnet1
        PeeringState        : Initiated
        ProvisioningState    : Succeeded
        RemoteVirtualNetwork    : {
        "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/vnet101/providers/Microsoft.Network/virtualNetworks/vnet2"
                                        }
        AllowVirtualNetworkAccess    : True
        AllowForwardedTraffic    : False
        AllowGatewayTransit    : False
            UseRemoteGateways    : False
            RemoteGateways        : null
            RemoteVirtualNetworkAddressSpace : null

4. VNet2 から VNet1 への VNET ピアリング リンクを作成します。

    ```powershell
    Add-AzureRmVirtualNetworkPeering -Name LinkToVNet1 -VirtualNetwork $vnet2 -RemoteVirtualNetworkId $vnet1.Id
    ```

    返される出力:

        Name            : LinkToVNet1
        Id                : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/vnet101/providers/Microsoft.Network/virtualNetworks/vnet2/virtualNetworkPeerings/LinkToVNet1
        Etag            : W/"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
        ResourceGroupName    : vnet101
        VirtualNetworkName    : vnet2
        PeeringState        : Connected
        ProvisioningState    : Succeeded
        RemoteVirtualNetwork    : {
                                            "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/vnet101/providers/Microsoft.Network/virtualNetworks/vnet1"
                                        }
        AllowVirtualNetworkAccess    : True
        AllowForwardedTraffic    : False
        AllowGatewayTransit    : False
        UseRemoteGateways    : False
        RemoteGateways        : null
        RemoteVirtualNetworkAddressSpace : null
5. VNet ピアリング リンクが作成されたら、次のコマンドを入力してリンクの状態を表示します。

    ```powershell
    Get-AzureRmVirtualNetworkPeering -VirtualNetworkName vnet1 -ResourceGroupName vnet101 -Name linktovnet2
    ```

    返される出力:
   
        Name            : LinkToVNet2
        Id                : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/vnet101/providers/Microsoft.Network/virtualNetworks/vnet1/virtualNetworkPeerings/LinkToVNet2
        Etag            : W/"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
        ResourceGroupName    : vnet101
        VirtualNetworkName    : vnet1
        PeeringState        : Connected
        ProvisioningState    : Succeeded
        RemoteVirtualNetwork    : {
                                             "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/vnet101/providers/Microsoft.Network/virtualNetworks/vnet2"
                                        }
        AllowVirtualNetworkAccess    : True
        AllowForwardedTraffic            : False
        AllowGatewayTransit              : False
        UseRemoteGateways                : False
        RemoteGateways                   : null
        RemoteVirtualNetworkAddressSpace : null
   
    VNet ピアリングには、構成可能なプロパティがいくつかあります。
   
   | オプション | Description | 既定値 |
   |:--- |:--- |:--- |
   | AllowVirtualNetworkAccess |ピア VNet のアドレス空間を Virtual_network タグの一部として含めるかどうかを選択します。 |はい |
   | AllowForwardedTraffic |ピアリングされた VNET 以外の送信元のトラフィックを許可するか破棄するかを選択します。 |いいえ |
   | AllowGatewayTransit |VNet ゲートウェイの使用をピア VNet に許可するかどうかを選択します。 |いいえ |
   | UseRemoteGateways |ピアの VNet ゲートウェイを使用します。 ピア VNET でゲートウェイが構成され、かつ AllowGatewayTransit が選択されている必要があります。 ゲートウェイをローカルで構成した場合、このオプションは使用できません。 |いいえ |

    VNet ピアリングの各リンクには、上記のプロパティ セットがあります。 たとえば、`AllowVirtualNetworkAccess` は、VNet1 から VNet2 への VNET ピアリング リンクの場合は *True* に、逆方向の VNet ピアリング リンクの場合は *False* に設定します。

    ```powershell
    $LinktoVNet2 = Get-AzureRmVirtualNetworkPeering -VirtualNetworkName vnet1 `
    -ResourceGroupName vnet101 -Name LinkToVNet2

    $LinktoVNet2.AllowForwardedTraffic = $true
    Set-AzureRmVirtualNetworkPeering -VirtualNetworkPeering $LinktoVNet2
    ```

    `Get-AzureRmVirtualNetworkPeering` を実行して、変更後のプロパティの値をもう一度確認できます。 出力から、上記のコマンドレットを実行した後で `AllowForwardedTraffic` が *True* に変更されていることを確認できます。

        Name            : LinkToVNet2
        Id            : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/vnet101/providers/Microsoft.Network/virtualNetworks/vnet1/virtualNetworkPeerings/LinkToVNet2
        Etag            : W/"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
        ResourceGroupName    : vnet101
        VirtualNetworkName    : vnet1
        PeeringState        : Connected
        ProvisioningState    : Succeeded
        RemoteVirtualNetwork    : {
                                            "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/vnet101/providers/Microsoft.Network/virtualNetworks/vnet2"
                                        }
        AllowVirtualNetworkAccess    : True
        AllowForwardedTraffic        : True
        AllowGatewayTransit        : False
        UseRemoteGateways        : False
        RemoteGateways        : null
        RemoteVirtualNetworkAddressSpace : null

    ピアリングが確立されると、VM は両方の VNet を介して互いに通信できるようになります。 既定では `AllowVirtualNetworkAccess` が *True* に設定され、VNet 間の通信を許可する適切な ACL が VNet ピアリングによってプロビジョニングされます。 ただし、ネットワーク セキュリティ グループ (NSG) ルールを適用して接続をブロックすることはできます。特定のサブネット間や仮想マシン間の接続をブロックすることで、2 つの VNet 間のアクセスを細かく制御することができます。 NSG の詳細については、[ネットワーク セキュリティ グループ](virtual-networks-create-nsg-arm-ps.md)に関する記事をご覧ください。

[!INCLUDE [virtual-networks-create-vnet-scenario-crosssub-include](../../includes/virtual-networks-create-vnetpeering-scenario-crosssub-include.md)]

PowerShell を使用してサブスクリプション間の VNet ピアリングを作成するには、次の手順を実行します。

1. サブスクリプション A の特権 User-A アカウントで Azure にサインインし、次のコマンドレットを実行します。

    ```powershell
    New-AzureRmRoleAssignment -SignInName <UserB ID> -RoleDefinitionName "Network Contributor" -Scope /subscriptions/<Subscription-A-ID>/resourceGroups/<ResourceGroupName>/providers/Microsoft.Network/VirtualNetworks/VNet5
    ```

    これは必須ではありません。 ユーザーが個々の VNet に対して個別にピアリング要求を行った場合でも、双方の要求が合致すればピアリングは確立されます。 相手側 VNET の特権ユーザーをローカル VNET のユーザーとして追加すると、セットアップしやすくなります。
2. サブスクリプション B の特権 User-B アカウントで Azure にサインインし、次のコマンドレットを実行します。

    ```powershell
    New-AzureRmRoleAssignment -SignInName <UserA ID> -RoleDefinitionName "Network Contributor" -Scope /subscriptions/<Subscription-B-ID>/resourceGroups/<ResourceGroupName>/providers/Microsoft.Network/VirtualNetworks/VNet3
    ```

    > [!IMPORTANT]
    > Azure Resource Manager デプロイメント モデルを使用して作成された&2; つの VNet の間にピアリングを作成している場合は、このセクションの残りの手順に進んでください。 2 つの VNet が異なるデプロイメント モデルを使用して作成されている場合は、このセクションの残りの手順を省略し、この記事の「[Peering virtual networks created through different deployment models (異なるデプロイメント モデルを使用して作成された仮想ネットワークのピアリング)](#x-model)」の手順を実行してください。

3. User-A のログイン セッションで次のコマンドを実行します。

    ```powershell
    $vnet3 = Get-AzureRmVirtualNetwork -ResourceGroupName hr-vnets -Name vnet3

    Add-AzureRmVirtualNetworkPeering -Name LinkToVNet5 -VirtualNetwork $vnet3 -RemoteVirtualNetworkId "/subscriptions/<Subscription-B-Id>/resourceGroups/<ResourceGroupName>/providers/Microsoft.Network/virtualNetworks/VNet5" -BlockVirtualNetworkAccess
    ```

4. User-B のログイン セッションで次のコマンドレットを実行します。

    ```powershell
    $vnet5 = Get-AzureRmVirtualNetwork -ResourceGroupName vendor-vnets -Name vnet5

    Add-AzureRmVirtualNetworkPeering -Name LinkToVNet3 -VirtualNetwork $vnet5 -RemoteVirtualNetworkId "/subscriptions/<Subscriptoin-A-Id>/resourceGroups/<ResourceGroupName>/providers/Microsoft.Network/virtualNetworks/VNet3" -BlockVirtualNetworkAccess
    ```

5. ピアリングが確立されると、VNet3 内のすべての VM が VNet5 内のすべての VM と通信できるようになります。

[!INCLUDE [virtual-networks-create-vnet-scenario-transit-include](../../includes/virtual-networks-create-vnetpeering-scenario-transit-include.md)]

1. このシナリオでは、次の PowerShell コマンドレットを実行して、VNet ピアリングを確立します。 `AllowForwardedTraffic` プロパティを *True* に設定し、VNET1 を HubVNet にリンクする必要があります。こうすることで、ピアリング VNet のアドレス空間外からの受信トラフィックが許可されます。

    ```powershell
    $hubVNet = Get-AzureRmVirtualNetwork -ResourceGroupName vnet101 -Name HubVNet
    $vnet1 = Get-AzureRmVirtualNetwork -ResourceGroupName vnet101 -Name vnet1

    Add-AzureRmVirtualNetworkPeering -Name LinkToHub -VirtualNetwork $vnet1 -RemoteVirtualNetworkId $HubVNet.Id -AllowForwardedTraffic

    Add-AzureRmVirtualNetworkPeering -Name LinkToVNet1 -VirtualNetwork $HubVNet -RemoteVirtualNetworkId $vnet1.Id
    ```

2. ピアリングが確立されたら、こちらの[記事](virtual-network-create-udr-arm-ps.md)を参照してください。VNet1 トラフィックを仮想アプライアンス経由でリダイレクトするようにユーザー定義ルート (UDR) を設定することで、その機能を利用することができます。 ルートの次ホップ アドレスを指定するときは、ピア VNET (HubVNet) に存在する仮想アプライアンスの IP アドレスを設定します。 サンプルを次に示します。

    ```powershell
    $route = New-AzureRmRouteConfig -Name TestNVA -AddressPrefix 10.3.0.0/16 -NextHopType VirtualAppliance -NextHopIpAddress 192.0.1.5

    $routeTable = New-AzureRmRouteTable -ResourceGroupName VNet101 -Location brazilsouth -Name TestRT -Route $route

    $vnet1 = Get-AzureRmVirtualNetwork -ResourceGroupName VNet101 -Name VNet1

    Set-AzureRmVirtualNetworkSubnetConfig -VirtualNetwork $vnet1 -Name subnet-1 -AddressPrefix 10.1.1.0/24 -RouteTable $routeTable

    Set-AzureRmVirtualNetwork -VirtualNetwork $vnet1
    ```

[!INCLUDE [virtual-networks-create-vnet-scenario-asmtoarm-include](../../includes/virtual-networks-create-vnetpeering-scenario-asmtoarm-include.md)]

1. "*同じ*" サブスクリプション内の異なるデプロイメント モデルを使用してデプロイされた VNet 間にピアリングを作成している場合は、手順 2. に進んでください。 "*異なる*" サブスクリプション内の異なるデプロイメント モデルを使用してデプロイされた VNet 間に VNet ピアリングを作成する機能は、**プレビュー** リリースに用意されています。 プレビュー リリースの機能は、一般向けリリースの機能と同等レベルの信頼性とサービス レベル アグリーメントを備えていません。 異なるサブスクリプション内の異なるデプロイメント モデルを使用してデプロイされた VNet 間にピアリングを作成している場合は、最初に次のタスクを完了する必要があります。
    - PowerShell から次のコマンドを入力して、Azure サブスクリプションにプレビュー機能を登録します: `Register-AzureRmProviderFeature -FeatureName AllowClassicCrossSubscriptionPeering -ProviderNamespace Microsoft.Network` および `Register-AzureRmResourceProvider -ProviderNamespace Microsoft.Network`
    - この記事の「[Peering across subscriptions (サブスクリプション間のピアリング)](#x-sub)」の手順 1. ～ 2. を実行します。
2. 次のコマンドを入力して、**VNET1** (Azure Resource Manager 仮想ネットワーク) の仮想ネットワーク オブジェクトを読み取ります。

    ```powershell
    $vnet1 = Get-AzureRmVirtualNetwork -ResourceGroupName vnet101 -Name vnet1
    ```

3. このシナリオで VNet ピアリングを確立するには、リンクを&1; つだけ作成します。具体的には、**VNET1** から **VNET2** にリンクを作成します。 この手順では、クラシック VNet のリソース ID を知っておく必要があります。 リソース グループ ID の形式は、次の例のようになります。

           subscriptions/{SubscriptionID}/resourceGroups/{ResourceGroupName}/providers/Microsoft.ClassicNetwork/virtualNetworks/{VirtualNetworkName}

    SubscriptionID、ResourceGroupName、および VirtualNetworkName は適切な名前に置き換えてください。

    これを行うには、次のコマンドを入力します。

    ```powershell
    Add-AzureRmVirtualNetworkPeering -Name LinkToVNet2 -VirtualNetwork $vnet1 -RemoteVirtualNetworkId /subscriptions/xxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxx/resourceGroups/MyResourceGroup/providers/Microsoft.ClassicNetwork/virtualNetworks/VNET2
    ```

4. この VNet ピアリング リンクが作成されると、次の出力に示されているように、リンクの状態が表示されます。
   
        Name                             : LinkToVNet2
        Id                               : /subscriptions/xxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxx/resourceGroups/MyResourceGroup/providers/Microsoft.Network/virtualNetworks/VNET1/virtualNetworkPeerings/LinkToVNet2
        Etag                             : W/"acecbd0f-766c-46be-aa7e-d03e41c46b16"
        ResourceGroupName                : MyResourceGroup
        VirtualNetworkName               : VNET1
        PeeringState                     : Connected
        ProvisioningState                : Succeeded
        RemoteVirtualNetwork             : {
                                         "Id": "/subscriptions/xxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxx/resourceGroups/MyResourceGroup/providers/Microsoft.ClassicNetwork/virtualNetworks/VNET2"
                                       }
        AllowVirtualNetworkAccess        : True
        AllowForwardedTraffic            : False
        AllowGatewayTransit              : False
        UseRemoteGateways                : False
        RemoteGateways                   : null
        RemoteVirtualNetworkAddressSpace : null

## <a name="remove-a-vnet-peering"></a>VNet ピアリングの削除
1. VNet ピアリングを削除するには、次のコマンドレットを実行する必要があります。

    ```powershell
    Remove-AzureRmVirtualNetworkPeering
    ```

    両方のリンクを削除するには、次のコマンドを入力します。

    ```powershell
    Remove-AzureRmVirtualNetworkPeering -ResourceGroupName vnet101 -VirtualNetworkName vnet1 -Name linktovnet2
    Remove-AzureRmVirtualNetworkPeering -ResourceGroupName vnet101 -VirtualNetworkName vnet1 -Name linktovnet2
    ```

2. VNET ピアリングのいずれかのリンクを削除すると、ピアのリンク状態が "*切断*" に変わります。 この状態になると、ピア リンク状態が "*Initiated (開始済み)*" に変化するまではリンクを再作成できません。 両方のリンクを削除してから、VNET ピアリングを作成し直すことをお勧めします。


