---
title: 'Azure VPN Gateway の VNet 対 VNet 接続を使用して VNet を別の VNet に接続する: PowerShell'
description: VNet 間接続と PowerShell を使用して仮想ネットワークどうしを接続します。
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 09/02/2020
ms.author: cherylmc
ms.openlocfilehash: 7de83302dd91d7d679b9c35718d184a9767ba436
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "94655359"
---
# <a name="configure-a-vnet-to-vnet-vpn-gateway-connection-using-powershell"></a>PowerShell を使用した VNet 間 VPN Gateway 接続を構成する

この記事は、VNet 間という接続の種類を使用して仮想ネットワークを接続する際に役立ちます。 仮想ネットワークが属しているリージョンやサブスクリプションは異なっていてもかまいません。 異なるサブスクリプションの VNet を接続する場合、サブスクリプションが同じ Active Directory テナントに関連付けられている必要はありません。

この記事の手順は、Resource Manager デプロイ モデルに適用されます。ここでは、PowerShell を使用します。 また、この構成の作成には、次のリストから別のオプションを選択して、別のデプロイ ツールまたはデプロイ モデルを使用することもできます。

> [!div class="op_single_selector"]
> * [Azure Portal](vpn-gateway-howto-vnet-vnet-resource-manager-portal.md)
> * [PowerShell](vpn-gateway-vnet-vnet-rm-ps.md)
> * [Azure CLI](vpn-gateway-howto-vnet-vnet-cli.md)
> * [Azure Portal (クラシック)](vpn-gateway-howto-vnet-vnet-portal-classic.md)
> * [異なるデプロイメント モデルの接続 - Azure Portal](vpn-gateway-connect-different-deployment-models-portal.md)
> * [異なるデプロイメント モデルの接続 - PowerShell](vpn-gateway-connect-different-deployment-models-powershell.md)

## <a name="about-connecting-vnets"></a><a name="about"></a>VNet の接続について

VNet の接続方法は複数あります。 以降のセクションでは、仮想ネットワークを接続するさまざまな方法について説明します。

### <a name="vnet-to-vnet"></a>VNet 間

VNet 間接続の構成は、VNet を簡単に接続するための良い方法です。 VNet 間接続 (VNet2VNet) の種類を使用して仮想ネットワークどうしを接続することは、オンプレミスの場所へのサイト間 IPsec 接続を作成することに似ています。  どちらの接続の種類も、VPN ゲートウェイを使用して IPsec/IKE を使った安全なトンネルが確保され、通信時には同じように機能します。 この接続の種類の違いは、ローカル ネットワーク ゲートウェイの構成方法にあります。 VNet 間接続を作成するときは、ローカル ネットワーク ゲートウェイのアドレス空間は見えません。 自動的に作成されて値が設定されます。 一方の VNet のアドレス空間を更新した場合、もう一方の VNet が、更新されたアドレス空間へのルーティングを自動的に認識します。 VNet 間接続の作成は、通常、VNet どうしのサイト間接続を作成するよりも高速で簡単です。

### <a name="site-to-site-ipsec"></a>サイト間 (IPsec)

複雑なネットワーク構成で作業している場合は、VNet 間の手順の代わりに、[サイト間](vpn-gateway-create-site-to-site-rm-powershell.md)の手順を使用して VNet を接続する方がよい場合もあります。 サイト間の手順を使用する場合は、ローカル ネットワーク ゲートウェイを手動で作成および構成します。 各 VNet のローカル ネットワーク ゲートウェイは、他方の VNet をローカル サイトとして扱います。 そうすることで、トラフィックをルーティングするための追加のアドレス空間をローカル ネットワーク ゲートウェイに指定することができます。 VNet のアドレス空間が変更されたら、変更を反映するように、対応するローカル ネットワーク ゲートウェイを更新する必要があります。 自動的には更新されません。

### <a name="vnet-peering"></a>VNET ピアリング

VNET ピアリングを使用して VNet を接続することを検討する場合もあります。 VNET ピアリングは VPN ゲートウェイを使用せず、さまざまな制約があります。 さらに、[VNET ピアリングの料金](https://azure.microsoft.com/pricing/details/virtual-network)は、[VNet 間 VPN Gateway の料金](https://azure.microsoft.com/pricing/details/vpn-gateway)と計算方法が異なります。 詳細については、「 [VNet ピアリング](../virtual-network/virtual-network-peering-overview.md)」を参照してください。

## <a name="why-create-a-vnet-to-vnet-connection"></a><a name="why"></a>VNet 間接続を作成する理由

VNet 間接続による仮想ネットワークの接続が望ましいのは、次のような場合です。

* **リージョン間の geo 冗長性および geo プレゼンス**

  * インターネット接続エンドポイントを介さず、安全な接続を使って独自の geo レプリケーションや geo 同期をセットアップすることができます。
  * Azure Traffic Manager および Load Balancer を使用し、複数の Azure リージョンをまたぐ geo 冗長性を備えた、可用性に優れたワークロードをセットアップすることができます。 たとえば、複数の Azure リージョンにまたがる SQL AlwaysOn 可用性グループをセットアップすることができます。
* **特定のリージョン内で分離または管理境界を備えた多層アプリケーション**

  * 同じリージョン内で、分離または管理要件に基づいて相互に接続された複数の仮想ネットワークを利用し、多層アプリケーションをセットアップすることができます。

マルチサイト構成と VNet 間通信を組み合わせることができます。 そのため、クロスプレミス接続と仮想ネットワーク間接続とを組み合わせたネットワーク トポロジを確立することができます。

## <a name="which-vnet-to-vnet-steps-should-i-use"></a><a name="steps"></a>どの VNet 間の手順を使用する必要がありますか。

この記事では、2 種類の手順について説明します。 1 つは [VNet が同じサブスクリプション内に存在する](#samesub)場合の手順で、もう 1 つは [VNet が別のサブスクリプション内に存在する](#difsub)場合の手順です。
大きく違うのは、異なるサブスクリプションに存在する VNet の接続を構成するときは、別々の PowerShell セッションを使用する必要がある点です。 

この演習では、構成を組み合わせるか、希望する方のみを選んでもかまいません。 どの構成でも、接続の種類として VNet 間を使用します。 ネットワーク トラフィックは、互いに直接接続されている VNet 間を行き来します。 この演習では、TestVNet4 からのトラフィックが TestVNet5 にルーティングされることはありません。

* [同じサブスクリプション内にある VNet](#samesub):この構成の手順では、TestVNet1 と TestVNet4 を使用します。

  ![同じサブスクリプション内に存在する VNet における、VNet 間の手順を示す図。](./media/vpn-gateway-vnet-vnet-rm-ps/v2vrmps.png)

* [異なるサブスクリプション内にある VNet](#difsub):この構成の手順では、TestVNet1 と TestVNet5 を使用します。

  ![v2v diagram](./media/vpn-gateway-vnet-vnet-rm-ps/v2vdiffsub.png)

## <a name="how-to-connect-vnets-that-are-in-the-same-subscription"></a><a name="samesub"></a>同じサブスクリプション内にある VNet を接続する方法

### <a name="before-you-begin"></a>開始する前に

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

* ゲートウェイの作成には最大で 45 分かかるため、この演習の間に Azure Cloud Shell が定期的にタイムアウトします。 ターミナルの左上をクリックして、Cloud Shell を再起動できます。 ターミナルを再起動したら、忘れずにすべての変数を再宣言してください。

* 代わりに最新バージョンの Azure PowerShell モジュールをローカル環境にインストールしたい場合は、「[Azure PowerShell のインストールおよび構成方法](/powershell/azure/)」をご覧ください。

### <a name="step-1---plan-your-ip-address-ranges"></a><a name="Step1"></a>手順 1 - IP アドレス範囲を決める

以下の手順に従って、2 つの仮想ネットワークを、それぞれのゲートウェイ サブネットおよび構成と共に作成します。 その後、2 つの VNet 間の VPN 接続を作成します。 ネットワーク構成の IP アドレスの範囲を計画することが重要です。 VNet の範囲やローカル ネットワークの範囲が重複することは、どのような形であれ許容されないので注意してください。 以下の例では、DNS サーバーは含まれていません。 仮想ネットワークの名前解決が必要な場合は、[名前解決](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md)に関する記事を参照してください。

例では、次の値を使用します。

**TestVNet1 の値:**

* VNet 名: TestVNet1
* リソース グループ:TestRG1
* 場所:米国東部
* TestVNet1: 10.11.0.0/16 と 10.12.0.0/16
* FrontEnd:10.11.0.0/24
* BackEnd: 10.12.0.0/24
* GatewaySubnet: 10.12.255.0/27
* GatewayName: VNet1GW
* パブリック IP: VNet1GWIP
* VPNType: RouteBased
* 接続 (1 から 4): VNet1toVNet4
* 接続 (1 から 5): VNet1toVNet5 (VNet が異なるサブスクリプションに存在する場合)
* ConnectionType: VNet2VNet

**TestVNet4 の値:**

* VNet 名: TestVNet4
* TestVNet2: 10.41.0.0/16 と 10.42.0.0/16
* FrontEnd:10.41.0.0/24
* BackEnd: 10.42.0.0/24
* GatewaySubnet: 10.42.255.0/27
* リソース グループ:TestRG4
* 場所:米国西部
* GatewayName: VNet4GW
* パブリック IP: VNet4GWIP
* VPNType: RouteBased
* 接続:VNet4toVNet1
* ConnectionType: VNet2VNet


### <a name="step-2---create-and-configure-testvnet1"></a><a name="Step2"></a>手順 2 - TestVNet1 を作成し、構成する

1. サブスクリプションの設定を確認します

   Azure PowerShell をローカル環境のコンピューターで実行している場合は、自分のアカウントに接続します。 Azure Cloud Shell を使用している場合は、自動的に接続されます。

   ```azurepowershell-interactive
   Connect-AzAccount
   ```

   アカウントのサブスクリプションを確認します。

   ```azurepowershell-interactive
   Get-AzSubscription
   ```

   複数のサブスクリプションがある場合は、使用するサブスクリプションを指定します。

   ```azurepowershell-interactive
   Select-AzSubscription -SubscriptionName nameofsubscription
   ```
2. 変数を宣言します。 この例では、この演習の値を使って変数を宣言します。 ほとんどの場合、値は実際のものに置き換える必要があります。 しかし、この手順を実行してこの種の構成に慣れたら、これらの変数を利用してもかまいません。 変数を必要に応じて変更したうえでコピーし、PowerShell コンソールに貼り付けます。

   ```azurepowershell-interactive
   $RG1 = "TestRG1"
   $Location1 = "East US"
   $VNetName1 = "TestVNet1"
   $FESubName1 = "FrontEnd"
   $BESubName1 = "Backend"
   $VNetPrefix11 = "10.11.0.0/16"
   $VNetPrefix12 = "10.12.0.0/16"
   $FESubPrefix1 = "10.11.0.0/24"
   $BESubPrefix1 = "10.12.0.0/24"
   $GWSubPrefix1 = "10.12.255.0/27"
   $GWName1 = "VNet1GW"
   $GWIPName1 = "VNet1GWIP"
   $GWIPconfName1 = "gwipconf1"
   $Connection14 = "VNet1toVNet4"
   $Connection15 = "VNet1toVNet5"
   ```
3. リソース グループを作成します。

   ```azurepowershell-interactive
   New-AzResourceGroup -Name $RG1 -Location $Location1
   ```
4. TestVNet1 のサブネット構成を作成します。 この例では、TestVNet1 という名前の仮想ネットワークと 3 つのサブネットを作成します。サブネットの名前は GatewaySubnet、FrontEnd、Backend です。 値を代入するときは、ゲートウェイの名前を必ず GatewaySubnet にすることが重要です。 別の名前にすると、ゲートウェイの作成は失敗します。 このため、以下の変数を使用して割り当てることはできません。

   次の例では、先ほど設定した変数を使用します。 この例では、ゲートウェイ サブネットに /27 が使用されています。 /29 と同程度の小規模なゲートウェイ サブネットを作成することはできますが、少なくとも /28 または /27 以上を選択してさらに多くのアドレスが含まれる大規模なサブネットを作成することをお勧めします。 これにより、十分な数のアドレスが、将来的に必要になる可能性のある追加の構成に対応できるようになります。

   ```azurepowershell-interactive
   $fesub1 = New-AzVirtualNetworkSubnetConfig -Name $FESubName1 -AddressPrefix $FESubPrefix1
   $besub1 = New-AzVirtualNetworkSubnetConfig -Name $BESubName1 -AddressPrefix $BESubPrefix1
   $gwsub1 = New-AzVirtualNetworkSubnetConfig -Name "GatewaySubnet" -AddressPrefix $GWSubPrefix1
   ```
5. TestVNet1 を作成します。

   ```azurepowershell-interactive
   New-AzVirtualNetwork -Name $VNetName1 -ResourceGroupName $RG1 `
   -Location $Location1 -AddressPrefix $VNetPrefix11,$VNetPrefix12 -Subnet $fesub1,$besub1,$gwsub1
   ```
6. VNet 用に作成するゲートウェイに割り当てるパブリック IP アドレスを要求します。 AllocationMethod が Dynamic であることに注意してください。 使用する IP アドレスを指定することはできません。 IP アドレスはゲートウェイに動的に割り当てられます。 

   ```azurepowershell-interactive
   $gwpip1 = New-AzPublicIpAddress -Name $GWIPName1 -ResourceGroupName $RG1 `
   -Location $Location1 -AllocationMethod Dynamic
   ```
7. ゲートウェイ構成を作成します。 ゲートウェイの構成で、使用するサブネットとパブリック IP アドレスを定義します。 サンプルを使用してゲートウェイの構成を作成します。

   ```azurepowershell-interactive
   $vnet1 = Get-AzVirtualNetwork -Name $VNetName1 -ResourceGroupName $RG1
   $subnet1 = Get-AzVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet1
   $gwipconf1 = New-AzVirtualNetworkGatewayIpConfig -Name $GWIPconfName1 `
   -Subnet $subnet1 -PublicIpAddress $gwpip1
   ```
8. TestVNet1 のゲートウェイを作成します。 この手順では、TestVNet1 の仮想ネットワーク ゲートウェイを作成します。 VNet 間構成では、RouteBased VpnType が必要です。 選択したゲートウェイ SKU によっては、ゲートウェイの作成に 45 分以上かかる場合も少なくありません。

   ```azurepowershell-interactive
   New-AzVirtualNetworkGateway -Name $GWName1 -ResourceGroupName $RG1 `
   -Location $Location1 -IpConfigurations $gwipconf1 -GatewayType Vpn `
   -VpnType RouteBased -GatewaySku VpnGw1
   ```

コマンドが完了した後、このゲートウェイが作成されるまでに最大で 45 分かかります。 Azure Cloud Shell を使用している場合は、Cloud Shell ターミナルの左上をクリックして、Cloud Shell セッションを再起動できます。その後、TestVNet4 を構成します。 TestVNet1 ゲートウェイが完了するまで待つ必要はありません。

### <a name="step-3---create-and-configure-testvnet4"></a>手順 3 - TestVNet4 を作成し、構成する

TestVNet1 を構成したら、TestVNet4 を作成します。 下の手順を実行します。値は必要に応じて実際の値に置換します。

1. 接続して変数を宣言します。 値は実際の構成で使用する値に置換します。

   ```azurepowershell-interactive
   $RG4 = "TestRG4"
   $Location4 = "West US"
   $VnetName4 = "TestVNet4"
   $FESubName4 = "FrontEnd"
   $BESubName4 = "Backend"
   $VnetPrefix41 = "10.41.0.0/16"
   $VnetPrefix42 = "10.42.0.0/16"
   $FESubPrefix4 = "10.41.0.0/24"
   $BESubPrefix4 = "10.42.0.0/24"
   $GWSubPrefix4 = "10.42.255.0/27"
   $GWName4 = "VNet4GW"
   $GWIPName4 = "VNet4GWIP"
   $GWIPconfName4 = "gwipconf4"
   $Connection41 = "VNet4toVNet1"
   ```
2. リソース グループを作成します。

   ```azurepowershell-interactive
   New-AzResourceGroup -Name $RG4 -Location $Location4
   ```
3. TestVNet4 のサブネット構成を作成します。

   ```azurepowershell-interactive
   $fesub4 = New-AzVirtualNetworkSubnetConfig -Name $FESubName4 -AddressPrefix $FESubPrefix4
   $besub4 = New-AzVirtualNetworkSubnetConfig -Name $BESubName4 -AddressPrefix $BESubPrefix4
   $gwsub4 = New-AzVirtualNetworkSubnetConfig -Name "GatewaySubnet" -AddressPrefix $GWSubPrefix4
   ```
4. TestVNet4 を作成します。

   ```azurepowershell-interactive
   New-AzVirtualNetwork -Name $VnetName4 -ResourceGroupName $RG4 `
   -Location $Location4 -AddressPrefix $VnetPrefix41,$VnetPrefix42 -Subnet $fesub4,$besub4,$gwsub4
   ```
5. パブリック IP アドレスを要求します。

   ```azurepowershell-interactive
   $gwpip4 = New-AzPublicIpAddress -Name $GWIPName4 -ResourceGroupName $RG4 `
   -Location $Location4 -AllocationMethod Dynamic
   ```
6. ゲートウェイ構成を作成します。

   ```azurepowershell-interactive
   $vnet4 = Get-AzVirtualNetwork -Name $VnetName4 -ResourceGroupName $RG4
   $subnet4 = Get-AzVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet4
   $gwipconf4 = New-AzVirtualNetworkGatewayIpConfig -Name $GWIPconfName4 -Subnet $subnet4 -PublicIpAddress $gwpip4
   ```
7. TestVNet4 ゲートウェイを作成する 選択したゲートウェイ SKU によっては、ゲートウェイの作成に 45 分以上かかる場合も少なくありません。

   ```azurepowershell-interactive
   New-AzVirtualNetworkGateway -Name $GWName4 -ResourceGroupName $RG4 `
   -Location $Location4 -IpConfigurations $gwipconf4 -GatewayType Vpn `
   -VpnType RouteBased -GatewaySku VpnGw1
   ```

### <a name="step-4---create-the-connections"></a>手順 4 - 接続を作成する

両方のゲートウェイが完了するまで待ちます。 Azure Cloud Shell セッションを再起動し、手順 2 と手順 3 の先頭から変数をコピーしてコンソールに貼り付け、値を再宣言します。

1. 両方の仮想ネットワーク ゲートウェイを取得します。

   ```azurepowershell-interactive
   $vnet1gw = Get-AzVirtualNetworkGateway -Name $GWName1 -ResourceGroupName $RG1
   $vnet4gw = Get-AzVirtualNetworkGateway -Name $GWName4 -ResourceGroupName $RG4
   ```
2. TestVNet1 から TestVNet4 への接続を作成します。 この手順では、TestVNet1 から TestVNet4 への接続を作成します。 この例では、参照される共有キーが表示されます。 共有キーには独自の値を使用することができます。 両方の接続の共有キーが一致することが重要です。 接続の作成完了までしばらくかかります。

   ```azurepowershell-interactive
   New-AzVirtualNetworkGatewayConnection -Name $Connection14 -ResourceGroupName $RG1 `
   -VirtualNetworkGateway1 $vnet1gw -VirtualNetworkGateway2 $vnet4gw -Location $Location1 `
   -ConnectionType Vnet2Vnet -SharedKey 'AzureA1b2C3'
   ```
3. TestVNet4 から TestVNet1 への接続を作成します。 この手順は上記の手順と同じですが、TestVNet4 から TestVNet1 への接続になります。 共有キーが一致することを確認してください。 数分後に接続が確立されます。

   ```azurepowershell-interactive
   New-AzVirtualNetworkGatewayConnection -Name $Connection41 -ResourceGroupName $RG4 `
   -VirtualNetworkGateway1 $vnet4gw -VirtualNetworkGateway2 $vnet1gw -Location $Location4 `
   -ConnectionType Vnet2Vnet -SharedKey 'AzureA1b2C3'
   ```
4. 接続を確認します。 「 [接続を確認する方法](#verify)」を参照してください。

## <a name="how-to-connect-vnets-that-are-in-different-subscriptions"></a><a name="difsub"></a>異なるサブスクリプション内にある VNet を接続する方法

このシナリオでは、TestVNet1 と TestVNet5 を接続します。 TestVNet1 と TestVNet5 は、異なるサブスクリプションに存在します。 サブスクリプションが同じ Active Directory テナントに関連付けられている必要はありません。

以下の手順とこれまでの手順の違いは、2 つ目のサブスクリプションとの関連で、構成手順を一部別個の PowerShell セッションで実行する必要があることです (特に 2 つのサブスクリプションが異なる組織に属する場合)。

この演習ではサブスクリプションのコンテキストを変更するため、手順 8 では、Azure Cloud Shell を使用するのではなく、ローカル環境のコンピューター上の PowerShell を使用する方が簡単な場合があります。

### <a name="step-5---create-and-configure-testvnet1"></a>手順 5 - TestVNet1 を作成し、構成する

TestVNet1 と TestVNet1 の VPN ゲートウェイを作成して構成するには、前のセクションの[手順 1.](#Step1) と[手順 2.](#Step2) を完了する必要があります。 この構成では、前のセクションの TestVNet4 を作成する必要はありません。ただし、TestVNet4 を作成しても以下の手順に影響はありません。 手順 1. と手順 2. が完了したら、手順 6. に進んで TestVNet5 を作成してください。

### <a name="step-6---verify-the-ip-address-ranges"></a>手順 6 - IP アドレス範囲を確認する

新しい仮想ネットワークである TestVNet5 の IP アドレス スペースが自分の VNet 範囲やローカルのネットワーク ゲートウェイ範囲と重ならないようにします。 この例は、仮想ネットワークが異なる組織に属することも想定されています。 この演習では、TestVNet5 に次の値を使用します。

**TestVNet5 の値:**

* VNet 名: TestVNet5
* リソース グループ:TestRG5
* 場所:東日本
* TestVNet5: 10.51.0.0/16 と 10.52.0.0/16
* FrontEnd:10.51.0.0/24
* BackEnd: 10.52.0.0/24
* GatewaySubnet: 10.52.255.0.0/27
* GatewayName: VNet5GW
* パブリック IP: VNet5GWIP
* VPNType: RouteBased
* 接続:VNet5toVNet1
* ConnectionType: VNet2VNet

### <a name="step-7---create-and-configure-testvnet5"></a>手順 7 - TestVNet5 を作成し、構成する

この手順は新しいサブスクリプションとの関連で実行する必要があります。 この部分は、サブスクリプションを所有する別の組織の管理者が実行することがあります。

1. 変数を宣言します。 値は実際の構成で使用する値に置換します。

   ```azurepowershell-interactive
   $Sub5 = "Replace_With_the_New_Subscription_Name"
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
   $GWName5 = "VNet5GW"
   $GWIPName5 = "VNet5GWIP"
   $GWIPconfName5 = "gwipconf5"
   $Connection51 = "VNet5toVNet1"
   ```
2. サブスクリプション 5 に接続します。 PowerShell コンソールを開き、アカウントに接続します。 接続するには、次のサンプルを参照してください。

   ```azurepowershell-interactive
   Connect-AzAccount
   ```

   アカウントのサブスクリプションを確認します。

   ```azurepowershell-interactive
   Get-AzSubscription
   ```

   使用するサブスクリプションを指定します。

   ```azurepowershell-interactive
   Select-AzSubscription -SubscriptionName $Sub5
   ```
3. 新しいリソース グループを作成します。

   ```azurepowershell-interactive
   New-AzResourceGroup -Name $RG5 -Location $Location5
   ```
4. TestVNet5 のサブネット構成を作成します。

   ```azurepowershell-interactive
   $fesub5 = New-AzVirtualNetworkSubnetConfig -Name $FESubName5 -AddressPrefix $FESubPrefix5
   $besub5 = New-AzVirtualNetworkSubnetConfig -Name $BESubName5 -AddressPrefix $BESubPrefix5
   $gwsub5 = New-AzVirtualNetworkSubnetConfig -Name $GWSubName5 -AddressPrefix $GWSubPrefix5
   ```
5. TestVNet5 を作成します。

   ```azurepowershell-interactive
   New-AzVirtualNetwork -Name $VnetName5 -ResourceGroupName $RG5 -Location $Location5 `
   -AddressPrefix $VnetPrefix51,$VnetPrefix52 -Subnet $fesub5,$besub5,$gwsub5
   ```
6. パブリック IP アドレスを要求します。

   ```azurepowershell-interactive
   $gwpip5 = New-AzPublicIpAddress -Name $GWIPName5 -ResourceGroupName $RG5 `
   -Location $Location5 -AllocationMethod Dynamic
   ```
7. ゲートウェイ構成を作成します。

   ```azurepowershell-interactive
   $vnet5 = Get-AzVirtualNetwork -Name $VnetName5 -ResourceGroupName $RG5
   $subnet5  = Get-AzVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet5
   $gwipconf5 = New-AzVirtualNetworkGatewayIpConfig -Name $GWIPconfName5 -Subnet $subnet5 -PublicIpAddress $gwpip5
   ```
8. TestVNet5 ゲートウェイを作成します。

   ```azurepowershell-interactive
   New-AzVirtualNetworkGateway -Name $GWName5 -ResourceGroupName $RG5 -Location $Location5 `
   -IpConfigurations $gwipconf5 -GatewayType Vpn -VpnType RouteBased -GatewaySku VpnGw1
   ```

### <a name="step-8---create-the-connections"></a>手順 8 - 接続を作成する

この例では、ゲートウェイが異なるサブスクリプションにあるため、[Subscription 1] および [Subscription 5] というマークの付いた 2 つの PowerShell セッションにこの手順を分けました。

1. **[Subscription 1]** サブスクリプション 1 の仮想ネットワーク ゲートウェイを取得します。 次の例を実行する前に、サブスクリプション 1 にサインインして接続します。

   ```azurepowershell-interactive
   $vnet1gw = Get-AzVirtualNetworkGateway -Name $GWName1 -ResourceGroupName $RG1
   ```

   次の要素の出力をコピーし、メールや他の方法でサブスクリプション 5 の管理者に送信します。

   ```azurepowershell-interactive
   $vnet1gw.Name
   $vnet1gw.Id
   ```

   これら 2 つの要素には、次のサンプル出力ような値が与えられます。

   ```
   PS D:\> $vnet1gw.Name
   VNet1GW
   PS D:\> $vnet1gw.Id
   /subscriptions/b636ca99-6f88-4df4-a7c3-2f8dc4545509/resourceGroupsTestRG1/providers/Microsoft.Network/virtualNetworkGateways/VNet1GW
   ```
2. **[Subscription 5]** サブスクリプション 5 の仮想ネットワーク ゲートウェイを取得します。 次の例を実行する前に、サブスクリプション 5 にサインインして接続します。

   ```azurepowershell-interactive
   $vnet5gw = Get-AzVirtualNetworkGateway -Name $GWName5 -ResourceGroupName $RG5
   ```

   次の要素の出力をコピーし、メールや他の方法でサブスクリプション 1 の管理者に送信します。

   ```azurepowershell-interactive
   $vnet5gw.Name
   $vnet5gw.Id
   ```

   これら 2 つの要素には、次のサンプル出力ような値が与えられます。

   ```
   PS C:\> $vnet5gw.Name
   VNet5GW
   PS C:\> $vnet5gw.Id
   /subscriptions/66c8e4f1-ecd6-47ed-9de7-7e530de23994/resourceGroups/TestRG5/providers/Microsoft.Network/virtualNetworkGateways/VNet5GW
   ```
3. **[Subscription 1]** TestVNet1 から TestVNet5 への接続を作成します。 この手順では、TestVNet1 から TestVNet5 への接続を作成します。 ここでの違いは、サブスクリプションが異なるため、$vnet5gw を直接取得できないことです。 上記の手順でサブスクリプション 1 から伝えられた値を利用して、新しい PowerShell オブジェクトを作成する必要があります。 次の例を使用します。 名前、ID、共有キーを実際の値に置き換えてください。 両方の接続の共有キーが一致することが重要です。 接続の作成完了までしばらくかかります。

   次の例を実行する前に、サブスクリプション 1 に接続します。

   ```azurepowershell-interactive
   $vnet5gw = New-Object -TypeName Microsoft.Azure.Commands.Network.Models.PSVirtualNetworkGateway
   $vnet5gw.Name = "VNet5GW"
   $vnet5gw.Id   = "/subscriptions/66c8e4f1-ecd6-47ed-9de7-7e530de23994/resourceGroups/TestRG5/providers/Microsoft.Network/virtualNetworkGateways/VNet5GW"
   $Connection15 = "VNet1toVNet5"
   New-AzVirtualNetworkGatewayConnection -Name $Connection15 -ResourceGroupName $RG1 -VirtualNetworkGateway1 $vnet1gw -VirtualNetworkGateway2 $vnet5gw -Location $Location1 -ConnectionType Vnet2Vnet -SharedKey 'AzureA1b2C3'
   ```
4. **[Subscription 5]** TestVNet5 から TestVNet1 への接続を作成します。 この手順は上記の手順と同じですが、TestVNet5 から TestVNet1 への接続になります。 サブスクリプション 1 から取得した値に基づいて PowerShell オブジェクトを作成するという同じプロセスをここでも実行します。 この手順では、共有キーが一致することを確認します。

   次の例を実行する前に、サブスクリプション 5 に接続します。

   ```azurepowershell-interactive
   $vnet1gw = New-Object -TypeName Microsoft.Azure.Commands.Network.Models.PSVirtualNetworkGateway
   $vnet1gw.Name = "VNet1GW"
   $vnet1gw.Id = "/subscriptions/b636ca99-6f88-4df4-a7c3-2f8dc4545509/resourceGroups/TestRG1/providers/Microsoft.Network/virtualNetworkGateways/VNet1GW "
   $Connection51 = "VNet5toVNet1"
   New-AzVirtualNetworkGatewayConnection -Name $Connection51 -ResourceGroupName $RG5 -VirtualNetworkGateway1 $vnet5gw -VirtualNetworkGateway2 $vnet1gw -Location $Location5 -ConnectionType Vnet2Vnet -SharedKey 'AzureA1b2C3'
   ```

## <a name="how-to-verify-a-connection"></a><a name="verify"></a>接続を確認する方法

[!INCLUDE [vpn-gateway-no-nsg-include](../../includes/vpn-gateway-no-nsg-include.md)]

[!INCLUDE [verify connections PowerShell](../../includes/vpn-gateway-verify-connection-ps-rm-include.md)]

## <a name="vnet-to-vnet-faq"></a><a name="faq"></a>VNet 間接続に関してよく寄せられる質問

[!INCLUDE [vpn-gateway-vnet-vnet-faq](../../includes/vpn-gateway-faq-vnet-vnet-include.md)]

## <a name="next-steps"></a>次のステップ

* 接続が完成したら、仮想ネットワークに仮想マシンを追加することができます。 詳細については、 [Virtual Machines のドキュメント](../index.yml) を参照してください。
* BGP の詳細については、[BGP の概要](vpn-gateway-bgp-overview.md)に関する記事と [BGP の構成方法](vpn-gateway-bgp-resource-manager-ps.md)に関する記事を参照してください。