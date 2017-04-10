---
title: "クラシック仮想ネットワークを Azure Resource Manager VNet に接続する: PowerShell | Microsoft Docs"
description: "VPN Gateway と PowerShell を使用して、クラシック VNet と Resource Manager の VNet の間に VPN 接続を作成する方法について説明します。"
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: timlt
editor: 
tags: azure-service-management,azure-resource-manager
ms.assetid: f17c3bf0-5cc9-4629-9928-1b72d0c9340b
ms.service: vpn-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/12/2017
ms.author: cherylmc
translationtype: Human Translation
ms.sourcegitcommit: 10985eaebd9148ef4fbd22eac0ba50076ca71ef5
ms.openlocfilehash: 430d2c02537e6907da09926db52a3d07b975bb64


---
# <a name="connect-virtual-networks-from-different-deployment-models-using-powershell"></a>異なるデプロイ モデルの仮想ネットワークを PowerShell を使用して接続する
> [!div class="op_single_selector"]
> * [ポータル](vpn-gateway-connect-different-deployment-models-portal.md)
> * [PowerShell](vpn-gateway-connect-different-deployment-models-powershell.md)
> 
> 

Azure には現在、クラシックと Resource Manager (RM) の&2; つの管理モデルがあります。 これまで Azure を使用してきているユーザーであれば、おそらく Azure VM およびクラシック VNet で実行されているインスタンス ロールを利用されていることでしょう。 新しい VM とロール インスタンスが、Resource Manager で作成された VNet 上で実行されていることも考えられます。 この記事では、クラシック VNet を Resource Manager VNet に接続し、異なるデプロイ モデルにあるリソースをゲートウェイ接続経由で相互に通信できるようにする方法について説明します。

別のサブスクリプション、別のリージョンに存在する VNet 間で接続を作成することができます。 オンプレミスのネットワークに既に接続されている VNet を接続することもできます。ただし、Vnet が構成されているゲートウェイが動的またはルート ベースである場合に限ります。 VNet 間接続の詳細については、この記事の最後にある「[VNet 間接続に関してよく寄せられる質問](#faq)」を参照してください。

### <a name="deployment-models-and-methods-for-connecting-vnets-in-different-deployment-models"></a>さまざまなデプロイ モデルで Vnet を接続するためのデプロイのモデルと方法
[!INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-classic-rm-include.md)]

以下の表は、この構成について新しい記事、追加のツールが利用できるようになったら更新されるものです。 記事が利用できるようになったら、表から直接リンクできるようにします。<br><br>

[!INCLUDE [vpn-gateway-table-vnet-vnet](../../includes/vpn-gateway-table-vnet-to-vnet-include.md)]

**VNet ピアリング**

[!INCLUDE [vpn-gateway-vnetpeeringlink](../../includes/vpn-gateway-vnetpeeringlink-include.md)]

## <a name="before-beginning"></a>作業を開始する前に
次の手順では、各 VNet 用に動的またはルート ベースのゲートウェイを構成してゲートウェイ間の VPN 接続を作成する際に必要な設定について説明します。 この構成は、静的またはポリシー ベースのゲートウェイをサポートしていません。

### <a name="prerequisites"></a>前提条件
* 両方の VNet が既に作成されている。
* これらの VNet のアドレス範囲が互いに重複していない。また、ゲートウェイの接続先になる可能性のある他の接続の範囲と重複していない。
* 最新の PowerShell コマンドレット (1.0.2 以降) がインストール済みである。 詳細については、「 [Azure PowerShell のインストールおよび構成方法](/powershell/azureps-cmdlets-docs) 」ご覧ください。 必ずサービス管理 (SM) と Resource Manager (RM) のコマンドレットの両方をインストールしてください。 

### <a name="a-nameexamplerefaexample-settings"></a><a name="exampleref"></a>設定例
設定の例は、リファレンスとして使用できます。

**クラシック VNet の設定**

VNet Name = ClassicVNet  <br>
Location = West US <br>
Virtual Network Address Spaces = 10.0.0.0/24 <br>
Subnet-1 = 10.0.0.0/27 <br>
GatewaySubnet = 10.0.0.32/29 <br>
Local Network Name = RMVNetLocal <br>
GatewayType = DynamicRouting

**Resource Manager の VNet の設定**

VNet Name = RMVNet  <br>
Resource Group = RG1 <br>
Virtual Network IP Address Spaces = 192.168.0.0/16 <br>
Subnet-1 = 192.168.1.0/24 <br>
GatewaySubnet = 192.168.0.0/26 <br>
Location = East US <br>
Gateway public IP name = gwpip <br>
Local Network Gateway = ClassicVNetLocal <br>
Virtual Network Gateway name = RMGateway <br>
Gateway IP addressing configuration = gwipconfig

## <a name="a-namecreatesmgwasection-1---configure-the-classic-vnet"></a><a name="createsmgw"></a>セクション 1 - クラシック VNet を構成する
### <a name="part-1---download-your-network-configuration-file"></a>パート 1 - ネットワーク構成ファイルをダウンロードする
1. 管理特権を使って PowerShell コンソールで Azure アカウントにログインします。 次のコマンドレットは、Azure アカウントのログイン資格情報をユーザーに求めます。 ログイン後にアカウント設定がダウンロードされ、Azure PowerShell で使用できるようになります。 構成のこの部分を完了するには、SM PowerShell コマンドレットを使用します。
   
        Add-AzureAccount
2. 次のコマンドを実行して、Azure のネットワーク構成ファイルをエクスポートします。 必要に応じて、ファイルの場所を変更して別の場所にエクスポートすることもできます。 ファイルを編集し、Azure にインポートします。
   
        Get-AzureVNetConfig -ExportToFile C:\AzureNet\NetworkConfig.xml
3. ダウンロードした .xml ファイルを開き、編集します。 ネットワーク構成ファイルの例については、 [ネットワークの構成スキーマ](https://msdn.microsoft.com/library/jj157100.aspx)に関するページを参照してください。

### <a name="part-2--verify-the-gateway-subnet"></a>パート 2 - ゲートウェイ サブネットを確認する
**VirtualNetworkSites** 要素にゲートウェイ サブネットがまだ作成されていない場合は、VNet に追加します。 ネットワーク構成ファイルを使用する場合は、ゲートウェイ サブネットの名前が "GatewaySubnet" である必要があります。それ以外の場合は、Azure でこれを認識してゲートウェイ サブネットとして使用することができません。

[!INCLUDE [vpn-gateway-no-nsg-include](../../includes/vpn-gateway-no-nsg-include.md)]

**例:**

    <VirtualNetworkSites>
      <VirtualNetworkSite name="ClassicVNet" Location="West US">
        <AddressSpace>
          <AddressPrefix>10.0.0.0/24</AddressPrefix>
        </AddressSpace>
        <Subnets>
          <Subnet name="Subnet-1">
            <AddressPrefix>10.0.0.0/27</AddressPrefix>
          </Subnet>
          <Subnet name="GatewaySubnet">
            <AddressPrefix>10.0.0.32/29</AddressPrefix>
          </Subnet>
        </Subnets>
      </VirtualNetworkSite>
    </VirtualNetworkSites>

### <a name="part-3---add-the-local-network-site"></a>パート 3 - ローカル ネットワーク サイトを追加する
追加するローカル ネットワーク サイトは、接続先の RM VNet を表します。 まだ存在していない場合は、**LocalNetworkSites** 要素をファイルに追加する必要があります。 構成のこの時点では、Resource Manager の VNet のゲートウェイをまだ作成していないため、VPNGatewayAddress は任意の有効なパブリック IP アドレスにすることができます。 ゲートウェイを作成したら、このプレースホルダー IP アドレスを、RM ゲートウェイに割り当てられている正しいパブリック IP アドレスに置き換えます。

    <LocalNetworkSites>
      <LocalNetworkSite name="RMVNetLocal">
        <AddressSpace>
          <AddressPrefix>192.168.0.0/16</AddressPrefix>
        </AddressSpace>
        <VPNGatewayAddress>13.68.210.16</VPNGatewayAddress>
      </LocalNetworkSite>
    </LocalNetworkSites>

### <a name="part-4---associate-the-vnet-with-the-local-network-site"></a>パート 4 - VNet をローカル ネットワーク サイトに関連付ける
このセクションでは、VNet の接続先のローカル ネットワーク サイトを指定します。 ここでは、先ほど参照した Resource Manager の VNet が該当します。 名前が一致することを確認します。 この手順では、ゲートウェイは作成しません。 ゲートウェイの接続先となるローカル ネットワークを指定します。

        <Gateway>
          <ConnectionsToLocalNetwork>
            <LocalNetworkSiteRef name="RMVNetLocal">
              <Connection type="IPsec" />
            </LocalNetworkSiteRef>
          </ConnectionsToLocalNetwork>
        </Gateway>

### <a name="part-5---save-the-file-and-upload"></a>パート 5 - ファイルを保存してアップロードする
ファイルを保存してから、次のコマンドを実行して Azure にインポートします。 必要に応じて、ファイル パスを環境に合わせて変更してください。

        Set-AzureVNetConfig -ConfigurationPath C:\AzureNet\NetworkConfig.xml

この結果と同様の情報が表示され、インポートが成功したことがわかります。

        OperationDescription        OperationId                      OperationStatus                                                
        --------------------        -----------                      ---------------                                                
        Set-AzureVNetConfig        e0ee6e66-9167-cfa7-a746-7casb9    Succeeded 

### <a name="part-6---create-the-gateway"></a>パート 6 - ゲートウェイを作成する
クラシック ポータルまたは PowerShell を使用して、仮想ネットワーク ゲートウェイを作成できます。

このサンプルを実行する前に、ダウンロードしたネットワーク構成ファイルで、Azure が期待する正確な名前を確認します。 ネットワーク構成ファイルには、クラシック仮想ネットワークの値が含まれています。 デプロイメント モデルでの違いのため、Azure Portal でクラシック VNet の設定を作成するときに、クラシック VNet の名前がネットワーク構成ファイルで変更される場合があります。 たとえば、Azure Portal を使って、"Classic VNet" という名前のクラシック VNet を、"ClassicRG" という名前のリソース グループに作成した場合、ネットワーク構成ファイルでは "Group ClassicRG Classic VNet" という名前に変換されます。 スペースを含む VNet の名前を指定するときは、引用符を使って値を囲みます。


次の例を使用して、動的ルーティング ゲートウェイを作成します。

    New-AzureVNetGateway -VNetName ClassicVNet -GatewayType DynamicRouting

`Get-AzureVNetGateway` コマンドレットを使用するとゲートウェイの状態を確認できます。

## <a name="a-namecreatermgwasection-2-configure-the-rm-vnet-gateway"></a><a name="creatermgw"></a>セクション 2: RM VNet ゲートウェイを構成する
RM VNet 用の VPN ゲートウェイを作成するには、次の手順に従います。 クラシック VNet のゲートウェイのパブリック IP アドレスを取得するまで、手順を開始しないでください。 

1. **Azure アカウントにログインします** 。 次のコマンドレットは、Azure アカウントのログイン資格情報をユーザーに求めます。 ログイン後にアカウント設定がダウンロードされ、Azure PowerShell で使用できるようになります。
   
        Login-AzureRmAccount 
   
     複数のサブスクリプションがある場合は、Azure サブスクリプションの一覧が表示されます。
   
        Get-AzureRmSubscription
   
    使用するサブスクリプションを指定します。 
   
        Select-AzureRmSubscription -SubscriptionName "Name of subscription"
2. **ローカル ネットワーク ゲートウェイの作成** 仮想ネットワークでは、ローカル ネットワーク ゲートウェイは通常、オンプレミスの場所を指します。 ここでは、ローカル ネットワーク ゲートウェイはクラシック VNet を意味します。 Azure で参照できる名前を付けて、アドレス空間のプレフィックスも指定します。 指定した IP アドレス プレフィックスは、Azure がオンプレミスの場所に送信するトラフィックを特定するときに使用されます。 ここの情報を後で調整する必要がある場合は、ゲートウェイを作成する前に、値を変更してサンプルをもう一度実行することができます。<br><br>
   
   * `-Name` は、ローカル ネットワーク ゲートウェイを参照するために割り当てる名前です。<br>
   * `-AddressPrefix` は、クラシック VNet のアドレス空間です。<br>
   * `-GatewayIpAddress` は、クラシック VNet のゲートウェイのパブリック IP アドレスです。 次の例を必ず変更して、正しい IP アドレスを反映させてください。
     
           New-AzureRmLocalNetworkGateway -Name ClassicVNetLocal `
           -Location "West US" -AddressPrefix "10.0.0.0/24" `
           -GatewayIpAddress "n.n.n.n" -ResourceGroupName RG1
3. Resource Manager VNet の仮想ネットワーク ゲートウェイに割り当てる**パブリック IP アドレスを要求します**。 使用する IP アドレスを指定することはできません。 IP アドレスは仮想ネットワーク ゲートウェイに動的に割り当てられます。 ただし、これは IP アドレスが変更されるという意味ではありません。 仮想ネットワーク ゲートウェイの IP アドレスが変更されるのは、ゲートウェイが削除され、再度作成されたときのみです。 ゲートウェイのサイズ変更、リセット、その他の内部メンテナンス/アップグレードの際には変更されません。<br>この手順では、後の手順で使用する変数も設定します。

        $ipaddress = New-AzureRmPublicIpAddress -Name gwpip `
        -ResourceGroupName RG1 -Location 'EastUS' `
        -AllocationMethod Dynamic

1. **仮想ネットワークにゲートウェイ サブネットがあることを確認します**。 ゲートウェイ サブネットが存在しない場合は追加します。 ゲートウェイ サブネットには、必ず *GatewaySubnet*という名前を付けてください。
2. **サブネットを取得します** 。 この手順では、次の手順で使用する変数も設定します。
   
   * `-Name` は、Resource Manager の VNet の名前です。
   * `-ResourceGroupName` は、VNet が関連付けられているリソース グループです。 ゲートウェイ サブネットが正常に動作するには、ゲートウェイ サブネットがこの VNet に対して既に存在し、 *GatewaySubnet* という名前が付けられている必要があります。

            $subnet = Get-AzureRmVirtualNetworkSubnetConfig -Name GatewaySubnet `
            -VirtualNetwork (Get-AzureRmVirtualNetwork -Name RMVNet -ResourceGroupName RG1) 

1. **ゲートウェイ IP アドレス指定の構成を作成する** ゲートウェイの構成で、使用するサブネットとパブリック IP アドレスを定義します。 次のサンプルを使用して、ゲートウェイの構成を作成します。<br>この手順では、`-SubnetId` パラメーターと `-PublicIpAddressId` パラメーターはそれぞれ、サブネットの id プロパティと IP アドレス オブジェクトの id プロパティを渡さなければなりません。 単純な文字列を使用することはできません。 これらの変数は、パブリック IP を要求する手順とサブネットを取得する手順で設定されます。
   
        $gwipconfig = New-AzureRmVirtualNetworkGatewayIpConfig `
        -Name gwipconfig -SubnetId $subnet.id `
        -PublicIpAddressId $ipaddress.id
2. **Resource Manager の仮想ネットワーク ゲートウェイを作成します** 。 `-VpnType` は、 *RouteBased*である必要があります。 完了には 45 分以上かかることがあります。
   
        New-AzureRmVirtualNetworkGateway -Name RMGateway -ResourceGroupName RG1 `
        -Location "EastUS" -GatewaySKU Standard -GatewayType Vpn `
        -IpConfigurations $gwipconfig `
        -EnableBgp $false -VpnType RouteBased
3. **パブリック IP アドレスをコピーします** 。 このアドレスは、クラシック VNet のローカル ネットワーク設定を構成するときに使用します。 次のコマンドレットを使用して、パブリック IP アドレスを取得できます。 パブリック IP アドレスは、 *IpAddress*として戻り値に表示されます。
   
        Get-AzureRmPublicIpAddress -Name gwpip -ResourceGroupName RG1

## <a name="section-3-modify-the-classic-vnet-local-site-settings"></a>セクション 3: クラシック VNet ローカル サイトの設定を変更する

このセクションでは、クラシック VNet を使います。 Resource Manager VNet ゲートウェイに接続するために使われるローカル サイトの設定を指定するときに使ったプレースホルダー IP アドレスを置き換えます。 

1. ネットワーク構成ファイルをエクスポートします。

        Get-AzureVNetConfig -ExportToFile C:\AzureNet\NetworkConfig.xml
2. テキスト エディターを使って、VPNGatewayAddress の値を変更します。 プレースホルダー IP アドレスを Resource Manager ゲートウェイのパブリック IP アドレスに置き換えた後、変更を保存します。
   
        <VPNGatewayAddress>13.68.210.16</VPNGatewayAddress>
3. 変更したネットワーク構成ファイルを Azure にインポートします。

        Set-AzureVNetConfig -ConfigurationPath C:\AzureNet\NetworkConfig.xml

## <a name="a-nameconnectasection-4-create-a-connection-between-the-gateways"></a><a name="connect"></a>セクション 4: ゲートウェイ間の接続を作成する
ゲートウェイ間の接続を作成するには PowerShell が必要です。 従来の PowerShell コマンドレットを使用して、Azure アカウントを追加する必要が生じる場合もあります。 そのためには、`Add-AzureAccount` を使います。

1. PowerShell コンソールで、共有キーを設定します。 コマンドレットを実行する前に、ダウンロードしたネットワーク構成ファイルで、Azure が期待する正確な名前を確認します。 スペースを含む VNet の名前を指定するときは、単一引用符を使って値を囲みます。<br><br>次の例では、`-VNetName` はクラシック VNet の名前で、`-LocalNetworkSiteName` はローカル ネットワーク サイトに対して指定した名前です。 `-SharedKey` は、管理者が生成して指定する値です。 この例では "abc123" を使いましたが、さらに複雑な値を生成して使うことができます。 重要なのは、ここで指定する値は、次の手順で接続を作成するときに指定するものと同じ値でなければならないということです。 戻り値が **Status: Successful** を示している必要があります。 
   
        Set-AzureVNetGatewayKey -VNetName ClassicVNet `
        -LocalNetworkSiteName RMVNetLocal -SharedKey abc123
2. 次のコマンドを実行して、VPN 接続を作成します。
   
    **変数を設定する**
   
        $vnet01gateway = Get-AzureRMLocalNetworkGateway -Name ClassicVNetLocal -ResourceGroupName RG1
        $vnet02gateway = Get-AzureRmVirtualNetworkGateway -Name RMGateway -ResourceGroupName RG1
   
    **接続を作成する**<br> `-ConnectionType` が IPsec であり、Vnet2Vnet ではないことに注意してください。
   
        New-AzureRmVirtualNetworkGatewayConnection -Name RM-Classic -ResourceGroupName RG1 `
        -Location "East US" -VirtualNetworkGateway1 `
        $vnet02gateway -LocalNetworkGateway2 `
        $vnet01gateway -ConnectionType IPsec -RoutingWeight 10 -SharedKey 'abc123'

## <a name="section-5-verify-your-connections"></a>セクション 5: 接続を確認する

### <a name="to-verify-the-connection-from-your-classic-vnet-to-your-resource-manager-vnet"></a>クラシック VNet から Resource Manager VNet への接続を確認するには

####<a name="verify-your-connection-using-powershell"></a>PowerShell を使って接続を確認する


[!INCLUDE [vpn-gateway-verify-connection-ps-classic](../../includes/vpn-gateway-verify-connection-ps-classic-include.md)]


####<a name="verify-your-connection-using-the-azure-portal"></a>Azure Portal を使って接続を確認する

[!INCLUDE [vpn-gateway-verify-connection-azureportal-classic](../../includes/vpn-gateway-verify-connection-azureportal-classic-include.md)]


###<a name="to-verify-the-connection-from-your-resource-manager-vnet-to-your-classic-vnet"></a>Resource Manager VNet からクラシック VNet への接続を確認するには

####<a name="verify-your-connection-using-powershell"></a>PowerShell を使って接続を確認する

[!INCLUDE [vpn-gateway-verify-ps-rm](../../includes/vpn-gateway-verify-connection-ps-rm-include.md)]

####<a name="verify-your-connection-using-the-azure-portal"></a>Azure Portal を使って接続を確認する

[!INCLUDE [vpn-gateway-verify-connection-portal-rm](../../includes/vpn-gateway-verify-connection-portal-rm-include.md)]

## <a name="a-namefaqavnet-to-vnet-considerations"></a><a name="faq"></a>VNet 間の考慮事項

[!INCLUDE [vpn-gateway-vnet-vnet-faq](../../includes/vpn-gateway-vnet-vnet-faq-include.md)]




<!--HONumber=Jan17_HO4-->


