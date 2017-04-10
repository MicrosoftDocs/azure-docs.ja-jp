---
title: "Azure 仮想ネットワークを別の VNet に接続する: PowerShell | Microsoft Docs"
description: "この記事では、Azure リソース マネージャーおよび PowerShell を使用して仮想ネットワーク同士を接続する方法を説明します。"
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 0683c664-9c03-40a4-b198-a6529bf1ce8b
ms.service: vpn-gateway
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/27/2017
ms.author: cherylmc
translationtype: Human Translation
ms.sourcegitcommit: 197ebd6e37066cb4463d540284ec3f3b074d95e1
ms.openlocfilehash: a4cbc4cd1c48da1120c643892b19692ac583d4c3
ms.lasthandoff: 03/31/2017


---
# <a name="configure-a-vnet-to-vnet-connection-using-powershell"></a>PowerShell を使用して VNet 間接続を構成する
> [!div class="op_single_selector"]
> * [Resource Manager - Azure Portal](vpn-gateway-howto-vnet-vnet-resource-manager-portal.md)
> * [Resource Manager - PowerShell](vpn-gateway-vnet-vnet-rm-ps.md)
> * [クラシック - Azure Portal](vpn-gateway-howto-vnet-vnet-portal-classic.md)
> * [クラシック - クラシック ポータル](virtual-networks-configure-vnet-to-vnet-connection.md)
> 
>

この記事では、VPN Gateway を使用して Resource Manager デプロイメント モデルで VNet 間の接続を作成する手順について説明します。 仮想ネットワークが属しているリージョンやサブスクリプションは異なっていてもかまいません。

![v2v diagram](./media/vpn-gateway-vnet-vnet-rm-ps/v2vrmps.png)

### <a name="deployment-models-and-methods-for-vnet-to-vnet-connections"></a>VNet 間接続で使用できるデプロイメント モデルとデプロイ方法
[!INCLUDE [deployment models](../../includes/vpn-gateway-deployment-models-include.md)]

以下の表は、VNet 間の構成に関して現在利用できるデプロイメント モデルとデプロイ方法を示しています。 構成手順を説明した記事が利用できるようになったら、表から直接リンクできるようにします。 

[!INCLUDE [vpn-gateway-table-vnet-vnet](../../includes/vpn-gateway-table-vnet-to-vnet-include.md)]

**VNet ピアリング**

[!INCLUDE [vpn-gateway-vnetpeeringlink](../../includes/vpn-gateway-vnetpeeringlink-include.md)]

## <a name="about-vnet-to-vnet-connections"></a>VNet 間接続の概要
仮想ネットワークどうし (VNet 間) の接続は、VNet をオンプレミス サイトの場所に接続することと似ています。 どちらの接続タイプでも、Azure VPN ゲートウェイを使用し、IPsec/IKE を使った安全なトンネルが確保されます。 接続する VNet は、リージョンが異なっていてもかまいません。 または、サブスクリプションが異なっていてもかまいません。 マルチサイト構成と VNet 間通信を組み合わせることもできます。 そのため、クロスプレミス接続と仮想ネットワーク間接続を組み合わせたネットワーク トポロジを確立することができます (下図参照)。

![接続について](./media/vpn-gateway-vnet-vnet-rm-ps/aboutconnections.png)

### <a name="why-connect-virtual-networks"></a>仮想ネットワークを接続する理由
仮想ネットワークを接続するのは次のような場合です。

* **リージョン間の geo 冗長性および geo プレゼンス**
  
  * インターネット接続エンドポイントを介さず、安全な接続を使って独自の geo レプリケーションや geo 同期をセットアップすることができます。
  * Azure Traffic Manager および Load Balancer を使用し、複数の Azure リージョンをまたぐ geo 冗長性を備えた、可用性に優れたワークロードをセットアップすることができます。 たとえば、複数の Azure リージョンにまたがる SQL AlwaysOn 可用性グループをセットアップすることができます。
* **特定のリージョン内で分離または管理境界を備えた多層アプリケーション**
  
  * 同じリージョン内で、分離または管理要件に基づいて相互に接続された複数の仮想ネットワークを利用し、多層アプリケーションをセットアップすることができます。

### <a name="vnet-to-vnet-considerations"></a>VNet 間の考慮事項
[!INCLUDE [vpn-gateway-vnet-vnet-faq](../../includes/vpn-gateway-vnet-vnet-faq-include.md)]

## <a name="which-set-of-steps-should-i-use"></a>どの手順を利用するべきでしょうか。
この記事では、2 種類の手順について説明します。 1 つは [VNet が同じサブスクリプション内に存在する](#samesub)場合の手順で、もう 1 つは [VNet が異なるサブスクリプション内に存在する](#difsub)場合の手順です。 すべての仮想ネットワーク リソースとゲートウェイ リソースを同じ PowerShell セッションで作成して構成できるかどうかが、両者の大きな違いとなります。

この記事の手順では、各セクションの冒頭で宣言される変数を使用します。 既に既存の VNet を使用している場合は、実際の環境の設定に応じて変数を変更してください。 

![両方の接続](./media/vpn-gateway-vnet-vnet-rm-ps/differentsubscription.png)

## <a name="samesub"></a>同じサブスクリプション内にある VNet を接続する方法
![v2v diagram](./media/vpn-gateway-vnet-vnet-rm-ps/v2vrmps.png)

### <a name="before-you-begin"></a>開始する前に
開始する前に、Azure Resource Manager PowerShell コマンドレットをインストールする必要があります。 PowerShell コマンドレットのインストールの詳細については、「 [Azure PowerShell のインストールおよび構成方法](/powershell/azureps-cmdlets-docs) 」を参照してください。

### <a name="Step1"></a>手順 1 - IP アドレス範囲を決める
以下の手順に従って、2 つの仮想ネットワークを、それぞれのゲートウェイ サブネットおよび構成と共に作成します。 その後、2 つの VNet 間の VPN 接続を作成します。 ネットワーク構成の IP アドレスの範囲を計画することが重要です。 VNet の範囲やローカル ネットワークの範囲が重複することは、どのような形であれ許容されないので注意してください。

例では、次の値を使用します。

**TestVNet1 の値:**

* VNet の名前: TestVNet1
* リソース グループ: TestRG1
* 場所: 米国東部
* TestVNet1: 10.11.0.0/16 & 10.12.0.0/16
* FrontEnd: 10.11.0.0/24
* BackEnd: 10.12.0.0/24
* GatewaySubnet: 10.12.255.0/27
* DNS サーバー: 8.8.8.8
* ゲートウェイの名前: VNet1GW
* パブリック IP: VNet1GWIP
* VPN の種類: RouteBased
* 接続 (1 ～ 4): VNet1toVNet4
* 接続 (1 ～ 5): VNet1toVNet5
* 接続の種類: VNet2VNet

**TestVNet4 の値:**

* VNet の名前: TestVNet4
* TestVNet2: 10.41.0.0/16 & 10.42.0.0/16
* FrontEnd: 10.41.0.0/24
* BackEnd: 10.42.0.0/24
* GatewaySubnet: 10.42.255.0/27
* リソース グループ: TestRG4
* 場所: 米国西部
* DNS サーバー: 8.8.8.8
* ゲートウェイの名前: VNet4GW
* パブリック IP: VNet4GWIP
* VPN の種類: RouteBased
* 接続: VNet4toVNet1
* 接続の種類: VNet2VNet

### <a name="Step2"></a>手順 2 - TestVNet1 を作成し、構成する
1. 変数を宣言する
   
    まず変数を宣言します。 この例では、この演習の値を使って変数を宣言します。 ほとんどの場合、値は実際のものに置き換える必要があります。 しかし、この手順を実行してこの種の構成に慣れたら、これらの変数を利用してもかまいません。 変数を必要に応じて変更したうえでコピーし、PowerShell コンソールに貼り付けます。
   
        $Sub1 = "Replace_With_Your_Subcription_Name"
        $RG1 = "TestRG1"
        $Location1 = "East US"
        $VNetName1 = "TestVNet1"
        $FESubName1 = "FrontEnd"
        $BESubName1 = "Backend"
        $GWSubName1 = "GatewaySubnet"
        $VNetPrefix11 = "10.11.0.0/16"
        $VNetPrefix12 = "10.12.0.0/16"
        $FESubPrefix1 = "10.11.0.0/24"
        $BESubPrefix1 = "10.12.0.0/24"
        $GWSubPrefix1 = "10.12.255.0/27"
        $DNS1 = "8.8.8.8"
        $GWName1 = "VNet1GW"
        $GWIPName1 = "VNet1GWIP"
        $GWIPconfName1 = "gwipconf1"
        $Connection14 = "VNet1toVNet4"
        $Connection15 = "VNet1toVNet5"
2. サブスクリプションへの接続
   
    PowerShell モードを切り替え、Resource Manager コマンドレットを使用します。 PowerShell コンソールを開き、アカウントに接続します。 接続については、次の例を参考にしてください。
   
        Login-AzureRmAccount
   
    アカウントのサブスクリプションを確認します。
   
        Get-AzureRmSubscription 
   
    使用するサブスクリプションを指定します。
   
        Select-AzureRmSubscription -SubscriptionName $Sub1
3. 新しいリソース グループを作成する
   
        New-AzureRmResourceGroup -Name $RG1 -Location $Location1
4. TestVNet1 のサブネット構成を作成する
   
    この例では、TestVNet1 という名前の仮想ネットワークと 3 つのサブネットを作成します。サブネットの名前は GatewaySubnet、FrontEnd、Backend です。 値を代入するときは、ゲートウェイの名前を必ず GatewaySubnet にすることが重要です。 別の名前にすると、接続を作成できません。 
   
    次の例では、先ほど設定した変数を使用します。 この例では、ゲートウェイ サブネットに /27 が使用されています。 /29 と同程度の小規模なゲートウェイ サブネットを作成することはできますが、少なくとも /28 または /27 以上を選択してさらに多くのアドレスが含まれる大規模なサブネットを作成することをお勧めします。 これにより、十分な数のアドレスが、将来的に必要になる可能性のある追加の構成に対応できるようになります。 
   
        $fesub1 = New-AzureRmVirtualNetworkSubnetConfig -Name $FESubName1 -AddressPrefix $FESubPrefix1
        $besub1 = New-AzureRmVirtualNetworkSubnetConfig -Name $BESubName1 -AddressPrefix $BESubPrefix1
        $gwsub1 = New-AzureRmVirtualNetworkSubnetConfig -Name $GWSubName1 -AddressPrefix $GWSubPrefix1
5. TestVNet1 を作成する
   
        New-AzureRmVirtualNetwork -Name $VNetName1 -ResourceGroupName $RG1 `
        -Location $Location1 -AddressPrefix $VNetPrefix11,$VNetPrefix12 -Subnet $fesub1,$besub1,$gwsub1
6. パブリック IP アドレスの要求
   
    VNet 用に作成するゲートウェイに割り当てるパブリック IP アドレスを要求します。 AllocationMethod が Dynamic であることに注意してください。 使用する IP アドレスを指定することはできません。 IP アドレスはゲートウェイに動的に割り当てられます。 
   
        $gwpip1 = New-AzureRmPublicIpAddress -Name $GWIPName1 -ResourceGroupName $RG1 `
        -Location $Location1 -AllocationMethod Dynamic
7. ゲートウェイ構成の作成
   
    ゲートウェイの構成で、使用するサブネットとパブリック IP アドレスを定義します。 サンプルを使用してゲートウェイの構成を作成します。 
   
        $vnet1 = Get-AzureRmVirtualNetwork -Name $VNetName1 -ResourceGroupName $RG1
        $subnet1 = Get-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet1
        $gwipconf1 = New-AzureRmVirtualNetworkGatewayIpConfig -Name $GWIPconfName1 `
        -Subnet $subnet1 -PublicIpAddress $gwpip1
8. TestVNet1 のゲートウェイを作成する
   
    この手順では、TestVNet1 の仮想ネットワーク ゲートウェイを作成します。 VNet 間構成では、RouteBased VpnType が必要です。 ゲートウェイの作成には時間がかかります (完了まで 45 分以上)。
   
        New-AzureRmVirtualNetworkGateway -Name $GWName1 -ResourceGroupName $RG1 `
        -Location $Location1 -IpConfigurations $gwipconf1 -GatewayType Vpn `
        -VpnType RouteBased -GatewaySku Standard

### <a name="step-3---create-and-configure-testvnet4"></a>手順 3 - TestVNet4 を作成し、構成する
TestVNet1 を構成したら、TestVNet4 を作成します。 下の手順を実行します。値は必要に応じて実際の値に置換します。 サブスクリプションが同じであるため、この手順は同じ PowerShell セッション内で実行できます。

1. 変数を宣言する
   
    値は実際の構成で使用する値に置換します。
   
        $RG4 = "TestRG4"
        $Location4 = "West US"
        $VnetName4 = "TestVNet4"
        $FESubName4 = "FrontEnd"
        $BESubName4 = "Backend"
        $GWSubName4 = "GatewaySubnet"
        $VnetPrefix41 = "10.41.0.0/16"
        $VnetPrefix42 = "10.42.0.0/16"
        $FESubPrefix4 = "10.41.0.0/24"
        $BESubPrefix4 = "10.42.0.0/24"
        $GWSubPrefix4 = "10.42.255.0/27"
        $DNS4 = "8.8.8.8"
        $GWName4 = "VNet4GW"
        $GWIPName4 = "VNet4GWIP"
        $GWIPconfName4 = "gwipconf4"
        $Connection41 = "VNet4toVNet1"
   
    続行する前に、サブスクリプション 1 に接続されていることを確認してください。
2. 新しいリソース グループを作成する
   
        New-AzureRmResourceGroup -Name $RG4 -Location $Location4
3. TestVNet4 のサブネット構成を作成する
   
        $fesub4 = New-AzureRmVirtualNetworkSubnetConfig -Name $FESubName4 -AddressPrefix $FESubPrefix4
        $besub4 = New-AzureRmVirtualNetworkSubnetConfig -Name $BESubName4 -AddressPrefix $BESubPrefix4
        $gwsub4 = New-AzureRmVirtualNetworkSubnetConfig -Name $GWSubName4 -AddressPrefix $GWSubPrefix4
4. TestVNet4 を作成する
   
        New-AzureRmVirtualNetwork -Name $VnetName4 -ResourceGroupName $RG4 `
        -Location $Location4 -AddressPrefix $VnetPrefix41,$VnetPrefix42 -Subnet $fesub4,$besub4,$gwsub4
5. パブリック IP アドレスの要求
   
        $gwpip4 = New-AzureRmPublicIpAddress -Name $GWIPName4 -ResourceGroupName $RG4 `
        -Location $Location4 -AllocationMethod Dynamic
6. ゲートウェイ構成の作成
   
        $vnet4 = Get-AzureRmVirtualNetwork -Name $VnetName4 -ResourceGroupName $RG4
        $subnet4 = Get-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet4
        $gwipconf4 = New-AzureRmVirtualNetworkGatewayIpConfig -Name $GWIPconfName4 -Subnet $subnet4 -PublicIpAddress $gwpip4
7. TestVNet4 ゲートウェイを作成する
   
        New-AzureRmVirtualNetworkGateway -Name $GWName4 -ResourceGroupName $RG4 `
        -Location $Location4 -IpConfigurations $gwipconf4 -GatewayType Vpn `
        -VpnType RouteBased -GatewaySku Standard

### <a name="step-4---connect-the-gateways"></a>手順 4 - ゲートウェイを接続する
1. 両方の仮想ネットワーク ゲートウェイを取得する
   
    この例では、両方のゲートウェイのサブスクリプションが同じであるため、この手順を同じ PowerShell セッションで完了できます。
   
        $vnet1gw = Get-AzureRmVirtualNetworkGateway -Name $GWName1 -ResourceGroupName $RG1
        $vnet4gw = Get-AzureRmVirtualNetworkGateway -Name $GWName4 -ResourceGroupName $RG4
2. TestVNet1 から TestVNet4 への接続を作成する
   
    この手順では、TestVNet1 から TestVNet4 への接続を作成します。 この例では、参照される共有キーが表示されます。 共有キーには独自の値を使用することができます。 両方の接続の共有キーが一致することが重要です。 接続の作成完了までしばらくかかります。
   
        New-AzureRmVirtualNetworkGatewayConnection -Name $Connection14 -ResourceGroupName $RG1 `
        -VirtualNetworkGateway1 $vnet1gw -VirtualNetworkGateway2 $vnet4gw -Location $Location1 `
        -ConnectionType Vnet2Vnet -SharedKey 'AzureA1b2C3'
3. TestVNet4 から TestVNet1 への接続を作成する
   
    この手順は上記の手順と同じですが、TestVNet4 から TestVNet1 への接続になります。 共有キーが一致することを確認してください。
   
        New-AzureRmVirtualNetworkGatewayConnection -Name $Connection41 -ResourceGroupName $RG4 `
        -VirtualNetworkGateway1 $vnet4gw -VirtualNetworkGateway2 $vnet1gw -Location $Location4 `
        -ConnectionType Vnet2Vnet -SharedKey 'AzureA1b2C3'
   
    数分後に接続が確立されます。
4. 接続を確認します。 「 [接続を確認する方法](#verify)」を参照してください。

## <a name="difsub"></a>異なるサブスクリプション内にある VNet を接続する方法
![v2v diagram](./media/vpn-gateway-vnet-vnet-rm-ps/v2vdiffsub.png)

このシナリオでは、TestVNet1 と TestVNet5 を接続します。 TestVNet1 と TestVNet5 は、異なるサブスクリプションに存在します。 この構成の手順では、TestVNet1 を TestVNet5 に接続するために VNet 間接続を追加します。 

ここでの違いは、2 つ目のサブスクリプションとの関連で、構成手順を一部別個の PowerShell セッションで実行する必要があることです  (特に 2 つのサブスクリプションが異なる組織に属する場合)。 

以下の指示は、前の手順からの続きになります。 TestVNet1 と TestVNet1 の VPN ゲートウェイを作成して構成するには、[手順 1.](#Step1) と[手順 2.](#Step2) を完了する必要があります。 手順 1. と手順 2. が完了したら、引き続き手順 5. で TestVNet5 を作成してください。

### <a name="step-5---verify-the-additional-ip-address-ranges"></a>手順 5 - 追加の IP アドレス範囲を確認する
新しい仮想ネットワークである TestVNet5 の IP アドレス スペースが自分の VNet 範囲やローカルのネットワーク ゲートウェイ範囲と重ならないようにします。 

この例は、仮想ネットワークが異なる組織に属することも想定されています。 この演習では、TestVNet5 に次の値を使用します。

**TestVNet5 の値:**

* VNet の名前: TestVNet5
* リソース グループ: TestRG5
* 場所: 東日本
* TestVNet5: 10.51.0.0/16 & 10.52.0.0/16
* FrontEnd: 10.51.0.0/24
* BackEnd: 10.52.0.0/24
* GatewaySubnet: 10.52.255.0.0/27
* DNS サーバー: 8.8.8.8
* GatewayName: VNet5GW
* パブリック IP: VNet5GWIP
* VPN の種類: RouteBased
* 接続: VNet5toVNet1
* 接続の種類: VNet2VNet

**TestVNet1 の追加の値:**

* 接続: VNet1toVNet5

### <a name="step-6---create-and-configure-testvnet5"></a>手順 6 - TestVNet5 を作成し、構成する
この手順は新しいサブスクリプションとの関連で実行する必要があります。 この部分は、サブスクリプションを所有する別の組織の管理者が実行することがあります。

1. 変数を宣言する
   
    値は実際の構成で使用する値に置換します。
   
        $Sub5 = "Replace_With_the_New_Subcription_Name"
        $RG5 = "TestRG5"
        $Location5 = "Japan East"
        $VnetName5 = "TestVNet5"
        $FESubName5 = "FrontEnd"
        $BESubName5 = "Backend"
        $GWSubName5 = "GatewaySubnet"
        $VnetPrefix51 = "10.51.0.0/16"
        $VnetPrefix52 = "10.52.0.0/16"
        $FESubPrefix5 = "10.51.0.0/24"
        $BESubPrefix5 = "10.52.0.0/24"
        $GWSubPrefix5 = "10.52.255.0/27"
        $DNS5 = "8.8.8.8"
        $GWName5 = "VNet5GW"
        $GWIPName5 = "VNet5GWIP"
        $GWIPconfName5 = "gwipconf5"
        $Connection51 = "VNet5toVNet1"
2. サブスクリプション 5 に接続する
   
    PowerShell コンソールを開き、アカウントに接続します。 接続するには、次のサンプルを参照してください。
   
        Login-AzureRmAccount
   
    アカウントのサブスクリプションを確認します。
   
        Get-AzureRmSubscription 
   
    使用するサブスクリプションを指定します。
   
        Select-AzureRmSubscription -SubscriptionName $Sub5
3. 新しいリソース グループを作成する
   
        New-AzureRmResourceGroup -Name $RG5 -Location $Location5
4. TestVNet4 のサブネット構成を作成する
   
        $fesub5 = New-AzureRmVirtualNetworkSubnetConfig -Name $FESubName5 -AddressPrefix $FESubPrefix5
        $besub5 = New-AzureRmVirtualNetworkSubnetConfig -Name $BESubName5 -AddressPrefix $BESubPrefix5
        $gwsub5 = New-AzureRmVirtualNetworkSubnetConfig -Name $GWSubName5 -AddressPrefix $GWSubPrefix5
5. TestVNet5 を作成する
   
        New-AzureRmVirtualNetwork -Name $VnetName5 -ResourceGroupName $RG5 -Location $Location5 `
        -AddressPrefix $VnetPrefix51,$VnetPrefix52 -Subnet $fesub5,$besub5,$gwsub5
6. パブリック IP アドレスの要求
   
        $gwpip5 = New-AzureRmPublicIpAddress -Name $GWIPName5 -ResourceGroupName $RG5 `
        -Location $Location5 -AllocationMethod Dynamic
7. ゲートウェイ構成の作成
   
        $vnet5 = Get-AzureRmVirtualNetwork -Name $VnetName5 -ResourceGroupName $RG5
        $subnet5  = Get-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet5
        $gwipconf5 = New-AzureRmVirtualNetworkGatewayIpConfig -Name $GWIPconfName5 -Subnet $subnet5 -PublicIpAddress $gwpip5
8. TestVNet5 ゲートウェイを作成する
   
        New-AzureRmVirtualNetworkGateway -Name $GWName5 -ResourceGroupName $RG5 -Location $Location5 `
        -IpConfigurations $gwipconf5 -GatewayType Vpn -VpnType RouteBased -GatewaySku Standard

### <a name="step-7---connecting-the-gateways"></a>手順 7 - ゲートウェイを接続する
この例では、ゲートウェイが異なるサブスクリプションにあるため、[Subscription 1] および [Subscription 5] というマークの付いた 2 つの PowerShell セッションにこの手順を分けました。

1. **[Subscription 1]** サブスクリプション 1 の仮想ネットワーク ゲートウェイを取得する
   
    ログインし、サブスクリプション 1 に接続します。
   
        $vnet1gw = Get-AzureRmVirtualNetworkGateway -Name $GWName1 -ResourceGroupName $RG1
   
    次の要素の出力をコピーし、メールや他の方法でサブスクリプション 5 の管理者に送信します。
   
        $vnet1gw.Name
        $vnet1gw.Id
   
    これら 2 つの要素には、次のサンプル出力ような値が与えられます。
   
        PS D:\> $vnet1gw.Name
        VNet1GW
        PS D:\> $vnet1gw.Id
        /subscriptions/b636ca99-6f88-4df4-a7c3-2f8dc4545509/resourceGroupsTestRG1/providers/Microsoft.Network/virtualNetworkGateways/VNet1GW
2. **[Subscription 5]** サブスクリプション 5 の仮想ネットワーク ゲートウェイを取得する
   
    ログインし、サブスクリプション 5 に接続します。
   
        $vnet5gw = Get-AzureRmVirtualNetworkGateway -Name $GWName5 -ResourceGroupName $RG5
   
    次の要素の出力をコピーし、メールや他の方法でサブスクリプション 1 の管理者に送信します。
   
        $vnet5gw.Name
        $vnet5gw.Id
   
    これら 2 つの要素には、次のサンプル出力ような値が与えられます。
   
        PS C:\> $vnet5gw.Name
        VNet5GW
        PS C:\> $vnet5gw.Id
        /subscriptions/66c8e4f1-ecd6-47ed-9de7-7e530de23994/resourceGroups/TestRG5/providers/Microsoft.Network/virtualNetworkGateways/VNet5GW
3. **[Subscription 1]** TestVNet1 から TestVNet5 への接続を作成する
   
    この手順では、TestVNet1 から TestVNet5 への接続を作成します。 ここでの違いは、サブスクリプションが異なるため、$vnet5gw を直接取得できないことです。 上記の手順でサブスクリプション 1 から伝えられた値を利用して、新しい PowerShell オブジェクトを作成する必要があります。 次の例を使用します。 名前、ID、共有キーを実際の値に置き換えてください。 両方の接続の共有キーが一致することが重要です。 接続の作成完了までしばらくかかります。
   
    サブスクリプション 1 に接続します。 
   
        $vnet5gw = New-Object Microsoft.Azure.Commands.Network.Models.PSVirtualNetworkGateway
        $vnet5gw.Name = "VNet5GW"
        $vnet5gw.Id   = "/subscriptions/66c8e4f1-ecd6-47ed-9de7-7e530de23994/resourceGroups/TestRG5/providers/Microsoft.Network/virtualNetworkGateways/VNet5GW"
        $Connection15 = "VNet1toVNet5"
        New-AzureRmVirtualNetworkGatewayConnection -Name $Connection15 -ResourceGroupName $RG1 -VirtualNetworkGateway1 $vnet1gw -VirtualNetworkGateway2 $vnet5gw -Location $Location1 -ConnectionType Vnet2Vnet -SharedKey 'AzureA1b2C3'
4. **[Subscription 5]** TestVNet5 から TestVNet1 への接続を作成する
   
    この手順は上記の手順と同じですが、TestVNet5 から TestVNet1 への接続になります。 サブスクリプション 1 から取得した値に基づいて PowerShell オブジェクトを作成するという同じプロセスをここでも実行します。 この手順では、共有キーが一致することを確認します。
   
    サブスクリプション 5 に接続します。
   
        $vnet1gw = New-Object Microsoft.Azure.Commands.Network.Models.PSVirtualNetworkGateway
        $vnet1gw.Name = "VNet1GW"
        $vnet1gw.Id = "/subscriptions/b636ca99-6f88-4df4-a7c3-2f8dc4545509/resourceGroups/TestRG1/providers/Microsoft.Network/virtualNetworkGateways/VNet1GW "
        New-AzureRmVirtualNetworkGatewayConnection -Name $Connection51 -ResourceGroupName $RG5 -VirtualNetworkGateway1 $vnet5gw -VirtualNetworkGateway2 $vnet1gw -Location $Location5 -ConnectionType Vnet2Vnet -SharedKey 'AzureA1b2C3'

## <a name="verify"></a>接続を確認する方法
[!INCLUDE [vpn-gateway-no-nsg-include](../../includes/vpn-gateway-no-nsg-include.md)]

[!INCLUDE [verify connection powershell](../../includes/vpn-gateway-verify-connection-ps-rm-include.md)]

## <a name="next-steps"></a>次のステップ

* 接続が完成したら、仮想ネットワークに仮想マシンを追加することができます。 詳細については、 [Virtual Machines のドキュメント](https://docs.microsoft.com/azure/#pivot=services&panel=Compute) を参照してください。
* BGP の詳細については、[BGP の概要](vpn-gateway-bgp-overview.md)に関する記事と [BGP の構成方法](vpn-gateway-bgp-resource-manager-ps.md)に関する記事を参照してください。 


