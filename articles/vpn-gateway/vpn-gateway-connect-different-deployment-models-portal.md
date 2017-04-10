---
title: "クラシック仮想ネットワークを Azure Resource Manager VNet に接続する: ポータル | Microsoft Docs"
description: "VPN Gateway とポータルを使用して、クラシック VNet と Resource Manager の VNet の間に VPN 接続を作成する方法について説明します。"
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: timlt
editor: 
tags: azure-service-management,azure-resource-manager
ms.assetid: 5a90498c-4520-4bd3-a833-ad85924ecaf9
ms.service: vpn-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/17/2017
ms.author: cherylmc
translationtype: Human Translation
ms.sourcegitcommit: 10985eaebd9148ef4fbd22eac0ba50076ca71ef5
ms.openlocfilehash: c20d72f25571e582310ae65ad1d887dbdb7b011c


---
# <a name="connect-virtual-networks-from-different-deployment-models-using-the-portal"></a>ポータルを使って異なるデプロイ モデルの仮想ネットワークを接続する
> [!div class="op_single_selector"]
> * [ポータル](vpn-gateway-connect-different-deployment-models-portal.md)
> * [PowerShell](vpn-gateway-connect-different-deployment-models-powershell.md)
> 
> 

Azure には現在、クラシックと Resource Manager (RM) の&2; つの管理モデルがあります。 これまで Azure を使用してきているユーザーであれば、おそらく Azure VM およびクラシック VNet で実行されているインスタンス ロールを利用されていることでしょう。 新しい VM とロール インスタンスが、Resource Manager で作成された VNet 上で実行されていることも考えられます。 この記事では、クラシック VNet を Resource Manager VNet に接続し、異なるデプロイ モデルにあるリソースをゲートウェイ接続経由で相互に通信できるようにする方法について説明します。 

別のサブスクリプション、別のリージョンに存在する VNet 間で接続を作成することができます。 オンプレミスのネットワークに既に接続されている VNet を接続することもできます。ただし、Vnet が構成されているゲートウェイが動的またはルート ベースである場合に限ります。 VNet 間接続について詳しくは、この記事の最後にある「[VNet 間の考慮事項](#faq)」セクションをご覧ください。

### <a name="deployment-models-and-methods-for-vnet-to-vnet-connections"></a>VNet 間接続で使用できるデプロイメント モデルとデプロイ方法
[!INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-classic-rm-include.md)]

以下の表は、この構成について新しい記事、追加のツールが利用できるようになったら更新されるものです。 記事が利用できるようになったら、表から直接リンクできるようにします。<br><br>

**VNet 間**

[!INCLUDE [vpn-gateway-table-vnet-vnet](../../includes/vpn-gateway-table-vnet-to-vnet-include.md)]

**VNet ピアリング**

[!INCLUDE [vpn-gateway-vnetpeeringlink](../../includes/vpn-gateway-vnetpeeringlink-include.md)]

## <a name="a-namebeforeabefore-beginning"></a><a name="before"></a>作業を開始する前に
次の手順では、各 VNet 用に動的またはルート ベースのゲートウェイを構成してゲートウェイ間の VPN 接続を作成する際に必要な設定について説明します。 この構成は、静的またはポリシー ベースのゲートウェイをサポートしていません。 

この記事では、Azure Portal と PowerShell を使います。 仮想ネットワーク間の接続を作成するには、PowerShell が必要です。 ポータルを使ってこの構成の接続を作成することはできません。

### <a name="prerequisites"></a>前提条件
* 両方の VNet が既に作成されている。
* これらの VNet のアドレス範囲が互いに重複していない。また、ゲートウェイの接続先になる可能性のある他の接続の範囲と重複していない。
* 最新の PowerShell コマンドレットがインストール済みである。 詳細については、「 [Azure PowerShell のインストールおよび構成方法](/powershell/azureps-cmdlets-docs) 」ご覧ください。 必ずサービス管理 (SM) と Resource Manager (RM) のコマンドレットの両方をインストールしてください。 

### <a name="a-namevaluesaexample-settings"></a><a name="values"></a>設定例
設定の例は、リファレンスとして使用できます。

**クラシック VNet の設定**

VNet Name = ClassicVNet  <br>
Address space = 10.0.0.0/24 <br>
Subnet-1 = 10.0.0.0/27 <br>
Resource Group = ClassicRG <br>
Location = West US <br>
GatewaySubnet = 10.0.0.32/28 <br>
Local site = RMVNetLocal <br>

**Resource Manager の VNet の設定**

VNet Name = RMVNet  <br>
Address space = 192.168.0.0/16 <br>
Subnet-1 = 192.168.1.0/24 <br>
GatewaySubnet = 192.168.0.0/26 <br>
Resource Group = RG1 <br>
Location = East US <br>
Virtual network gateway name = RMGateway <br>
Gateway type = VPN <br>
VPN type = Route-based <br>
Gateway public IP name = gwpip <br>
Local network gateway = ClassicVNetLocal <br>

## <a name="a-namecreatesmgwasection-1-configure-classic-vnet-settings"></a><a name="createsmgw"></a>セクション 1: クラシック VNet の設定を構成する
このセクションでは、クラシック VNet のローカル ネットワーク (ローカル サイト) と仮想ネットワーク ゲートウェイを作成します。 このセクションの手順では Azure Portal を使います。 この手順では、クラシック VNet に対して VPN Gateway が作成されていないものとします。 既にゲートウェイがある場合は、動的ゲートウェイであることを確認してください。 静的の場合は、最初に VPN Gateway を削除してから、次の手順に進む必要があります。

### <a name="part-1---configure-the-local-site"></a>パート 1 - ローカル サイトを構成する

[Azure Portal](https://ms.portal.azure.com) を開き、Azure アカウントでサインインします。

1. **[すべてのリソース]** に移動し、クラシック仮想ネットワークを見つけます。
2. **[概要]** ブレードの **[VPN 接続]** セクションで、**[ゲートウェイ]** のグラフィックをクリックしてゲートウェイを作成します。

    ![VPN Gateway を構成する](./media/vpn-gateway-connect-different-deployment-models-portal/gatewaygraphic.png "VPN Gateway を構成する")
3. **[新しい VPN 接続]** ブレードの **[接続の種類]** で、**[サイト対サイト]** を選びます。
4. **[ローカル サイト]** で、**[必要な設定の構成]** をクリックします。 これにより、**[ローカル サイト]** ブレードが開きます。
5. **[ローカル サイト]** ブレードで、Resource Manager の VNet を参照するために使う名前を作成します。 たとえば、RMVNetLocal などとします。
6. Resource Manager VNet の VPN Gateway にパブリック IP アドレスが既にある場合は、**[VPN ゲートウェイの IP アドレス]** フィールドでその値を使います。 Resource Manager VNet の仮想ネットワーク ゲートウェイをまだ作成していない場合は、プレースホルダー IP アドレスを使うことができます。 プレースホルダー IP アドレスで有効な形式が使われていることを確認します。 後で、プレースホルダー IP アドレスを、Resource Manager 仮想ネットワーク ゲートウェイのパブリック IP アドレスに置き換える必要があります。
7. **[Client Address Space (クライアント アドレス空間)]** に対しては、Resource Manager VNet の仮想ネットワーク IP アドレス空間の値を使います。 この設定を使って、RM VNet にルーティングするアドレス空間を指定します。
8. **[OK]** をクリックして値を保存し、**[新しい VPN 接続]** ブレードに戻ります。

### <a name="part-2---create-the-virtual-network-gateway"></a>パート 2 - 仮想ネットワーク ゲートウェイを作成する
1. **[新しい VPN 接続]** ブレードで、**[ゲートウェイをすぐに作成する]** チェック ボックスをオンにし、**[ゲートウェイの構成 (オプション)]** をクリックして **[ゲートウェイの構成]** ブレードを開きます。 

    ![ゲートウェイの構成ブレードを開く](./media/vpn-gateway-connect-different-deployment-models-portal/optionalgatewayconfiguration.png "ゲートウェイの構成ブレードを開く")
2. **[Subnet - Configure required settings (サブネット - 必要な設定の構成)]** ブレードをクリックして、**[サブネットの追加]** を開きます。 このブレードでは、名前が既に必要な値 **GatewaySubnet** に構成されていることがわかります。
3. **[アドレス範囲]** は、ゲートウェイ サブネットの範囲を示します。 /29 アドレス範囲 (3 アドレス) のゲートウェイ サブネットを作成することもできますが、将来の構成でさらに多くの IP アドレスが必要になるときに備えて、使用可能な IP アドレスをされに多く含むゲートウェイ サブネットを作成することをお勧めします。 可能であれば、/27 または /28 を使います。 **[OK]** をクリックして、ゲートウェイ サブネットを作成します。
4. **[ゲートウェイの構成]** ブレードの **[サイズ]** は、ゲートウェイの SKU を示します。 VPN Gateway のゲートウェイ SKU を選びます。
5. **[ルーティングの種類]** が **[動的]** であることを確認した後、**[OK]** をクリックして **[新しい VPN 接続]** ブレードに戻ります。
6. **[新しい VPN 接続]** ブレードで、**[OK]** をクリックして VPN Gateway の作成を開始します。 完了するまで最大 45 分かかることがあります。


### <a name="a-nameipapart-3---copy-the-virtual-network-gateway-public-ip-address"></a><a name="ip"></a>パート 3 - 仮想ネットワーク ゲートウェイのパブリック IP アドレスをコピーする
仮想ネットワーク ゲートウェイが作成されると、ゲートウェイ IP アドレスを確認できます。 

1. クラシック VNet に移動し、**[概要]** をクリックします。
2. **[VPN 接続]** をクリックして、[VPN 接続] ブレードを開きます。 [VPN 接続] ブレードで、パブリック IP アドレスを見ることができます。 これは、仮想ネットワーク ゲートウェイに割り当てられたパブリック IP アドレスです。 この IP アドレスを、書き留めるかコピーしておきます。 後で、Resource Manager ローカル ネットワーク ゲートウェイの構成を設定するときに使います。 ゲートウェイ接続の状態も見ることができます。 作成したローカル ネットワーク サイトが [接続中] になっていることに注意してください。 接続を作成すると、状態が変化します。 ゲートウェイの IP アドレスをコピーした後、ブレードを閉じます。

## <a name="a-namecreatermgwasection-2-configure-resource-manager-vnet-settings"></a><a name="creatermgw"></a>セクション 2: Resource Manager の VNet の設定を構成する
このセクションでは、仮想ネットワーク ゲートウェイと、Resource Manager の VNet 用のローカル ネットワークを作成します。 スクリーンショットは例として示されています。 サンプルの値は必ず実際の値に変更してください。 練習としてこの構成を作成する場合は、上記の [値](#values)を参照してください。

### <a name="part-1---create-a-gateway-subnet"></a>パート 1 - ゲートウェイ サブネットを作成する
仮想ネットワーク ゲートウェイを作成する前に、まずゲートウェイ サブネットを作成する必要があります。 CIDR カウント /28 以上 (/27、/26 など) で、ゲートウェイ サブネットを 作成します。

[!INCLUDE [vpn-gateway-no-nsg-include](../../includes/vpn-gateway-no-nsg-include.md)]

ブラウザーから [Azure ポータル](http://portal.azure.com) に移動し、Azure アカウントでサインインします。

[!INCLUDE [vpn-gateway-add-gwsubnet-rm-portal](../../includes/vpn-gateway-add-gwsubnet-rm-portal-include.md)]

### <a name="part-2---create-a-virtual-network-gateway"></a>パート 2 - 仮想ネットワーク ゲートウェイを作成する
[!INCLUDE [vpn-gateway-add-gw-rm-portal](../../includes/vpn-gateway-add-gw-rm-portal-include.md)]

仮想ネットワーク ゲートウェイの作成には、最大で 45 分かかる場合があります。 Resource Manager による仮想ネットワーク ゲートウェイの作成が完了するまで待っても、「[パート 3 - ローカル ネットワーク ゲートウェイを作成する](#createlng)」に進んでも構いません。 ゲートウェイが作成されると、パブリック IP アドレスがゲートウェイに割り当てられます。 後の手順で、この IP アドレスを使って、セクション 1 で作成したクラシック VNet ローカル サイトの設定のプレースホルダー IP アドレスの情報を置き換えます。 

### <a name="a-namecreatelngapart-3---create-a-local-network-gateway"></a><a name="createlng"></a>パート 3 - ローカル ネットワーク ゲートウェイを作成する

ローカル ネットワーク ゲートウェイでは、クラシック VNet およびその仮想ネットワーク ゲートウェイに関連付けられているアドレス範囲とパブリック IP アドレスを指定します。

- [前のセクション](#ip)でクラシック VNet ゲートウェイに割り当てられたパブリック IP アドレスを使います。 
- Azure から参照できるように、ローカル ネットワーク ゲートウェイに名前を付けます。 たとえば、"ClassicVNetLocal" などとします。
- サブネットだけではなくクラシック VNet に割り当てたアドレス空間を使います。

[!INCLUDE [vpn-gateway-add-lng-rm-portal](../../includes/vpn-gateway-add-lng-rm-portal-include.md)]


## <a name="section-3-modify-the-classic-vnet-local-site-settings"></a>セクション 3: クラシック VNet ローカル サイトの設定を変更する

このセクションでは、クラシック VNet を使います。 ローカル サイトの設定を指定するときに使ったプレースホルダー IP アドレスを置き換えます。 このセクションでは、クラシック (SM) PowerShell コマンドレットを使います。 まだ行っていない場合は、Resource Manager VNet の仮想ネットワーク ゲートウェイを表示し、パブリック IP アドレスをコピーします。 この IP アドレスを使って、プレースホルダー IP アドレスを置き換えます。

「[作業を開始する前に](#before)」セクションで指定されているように、コマンドレットの最新バージョンがダウンロードされていることを確認します。

1. Azure Portal でクラシック仮想ネットワークに移動します。
2. 仮想ネットワークのブレードで、**[概要]** をクリックします。
3. **[VPN 接続]** セクションのグラフィックで、ローカル サイトの名前をクリックします。

    ![VPN 接続](./media/vpn-gateway-connect-different-deployment-models-portal/vpnconnections.png "VPN 接続")
4. **[サイト対サイト VPN 接続]** ブレードで、サイトの名前をクリックします。

    ![サイト名](./media/vpn-gateway-connect-different-deployment-models-portal/sitetosite3.png "ローカル サイトの名前")
5. ローカル サイトの接続ブレードで、ローカル サイトの名前をクリックして、**[ローカル サイト]** ブレードを開きます。

    ![ローカル サイトを開く](./media/vpn-gateway-connect-different-deployment-models-portal/openlocal.png "ローカル サイトを開く")
6. **[ローカル サイト]** ブレードで、VPN Gateway の IP アドレスを、Resource Manager ゲートウェイの IP アドレスに置き換えます。

    ![ゲートウェイの IP アドレス](./media/vpn-gateway-connect-different-deployment-models-portal/gwipaddress.png "ゲートウェイの IP アドレス")
7. **[OK]** をクリックして、IP アドレスを更新します。


## <a name="a-nameconnectasection-4-create-the-connection"></a><a name="connect"></a>セクション 4: 接続を作成する
このセクションでは、VNet 間の接続を作成します。 この手順には PowerShell が必要です。 どのポータルでもこの接続は作成できません。 クラシック (SM) と Resource Manager (RM) の両方の PowerShell コマンドレットをダウンロードしてインストールしていることを確認してください。

### <a name="part-1---log-in-to-your-azure-account"></a>パート 1 - Azure アカウントにログインする

1. 管理者特権を使って PowerShell コンソールを開き、Azure アカウントにログインします。 次のコマンドレットは、Azure アカウントのログイン資格情報をユーザーに求めます。 ログイン後にアカウント設定がダウンロードされ、Azure PowerShell で使用できるようになります。
   
        Login-AzureRmAccount 
   
2. 複数のサブスクリプションがある場合は、Azure サブスクリプションの一覧が表示されます。
   
        Get-AzureRmSubscription
   
3. 使用するサブスクリプションを指定します。 
   
        Select-AzureRmSubscription -SubscriptionName "Name of subscription"

4. 従来の PowerShell コマンドレットを使用して、Azure アカウントを追加します。 これを行うには、次のコマンドを使用できます。
   
        Add-AzureAccount

### <a name="part-2---download-your-network-configuration-file"></a>パート 2 - ネットワーク構成ファイルをダウンロードする

デプロイメント モデルでの違いのため、Azure Portal でクラシック VNet の設定を作成するときに、クラシック VNet およびローカル ネットワーク サイトの名前が、ネットワーク構成ファイルで変更される場合があります。 たとえば、この例では、Azure Portal を使って、クラシック VNet に "Classic VNet" という名前を付け、"ClassicRG" という名前のリソース グループにそれを作成しました。 ネットワーク構成ファイルに含まれる名前は、"Group ClassicRG Classic VNet" に変換されています。 スペースを含む VNet の名前を指定するときは、引用符を使って値を囲みます。

1. 次のコマンドを実行して、Azure のネットワーク構成ファイルをエクスポートします。 必要に応じて、ファイルの場所を変更して別の場所にエクスポートすることもできます。
   
        Get-AzureVNetConfig -ExportToFile C:\AzureNet\NetworkConfig.xml
3. メモ帳などのテキスト エディターを使って .xml ファイルを開き、内容を表示します。 クラシック仮想ネットワークとローカル ネットワーク サイトの名前の値を確認します。

### <a name="part-3---set-the-shared-key"></a>パート 3 - 共有キーを設定する

クラシック VNet から Resource Manager VNet への接続の共有キーを設定します。 これらのコマンドレットを使うときは、ネットワーク構成ファイルを使って `-VNetName` と `-LocalNetworkSiteName` の値を確認してください。 スペースを含む名前を指定するときは、引用符を使って値を囲みます。 

- この例では、`-VNetName` はクラシック VNet の名前です。 
- `-LocalNetworkSiteName` は、ローカル サイトに対して指定した名前です。
- `-SharedKey` は、管理者が生成して指定する値です。 この例では *abc123* を使いましたが、さらに複雑な値を生成できます。 重要なのは、ここで指定する値は、次の手順で接続を作成するときに指定するものと同じ値でなければならないということです。

PowerShell コンソールで、次のサンプルを実行して共有キーを設定します。値は実際のものに置き換えてください。 このサンプルの戻り値は、 **Status: Successful**を示します。
   
        Set-AzureVNetGatewayKey -VNetName "Group ClassicRG ClassicVNet" `
        -LocalNetworkSiteName "172B9E16_RMVNetLocal" -SharedKey abc123
### <a name="part-4---create-the-vpn-connection-by-running-the-following-commands"></a>パート 4 - 次のコマンドを実行して VPN 接続を作成する
   
1. 変数を設定します。
   
        $vnet01gateway = Get-AzureRMLocalNetworkGateway -Name ClassicVNetLocal -ResourceGroupName RG1
        $vnet02gateway = Get-AzureRmVirtualNetworkGateway -Name RMGateway -ResourceGroupName RG1
   
2. 接続を作成します。 この例の `-Name` は、既に作成した名前ではなく、接続に指定する名前です。 次の例では、"RM-Classic" という名前の接続を作成します。 `-ConnectionType` が "Vnet2Vnet" ではなく "IPsec" であること、および `-SharedKey` が前に設定したキーと一致することに注意してください。
   
        New-AzureRmVirtualNetworkGatewayConnection -Name RM-Classic -ResourceGroupName RG1 `
        -Location "East US" -VirtualNetworkGateway1 `
        $vnet02gateway -LocalNetworkGateway2 `
        $vnet01gateway -ConnectionType IPsec -RoutingWeight 10 -SharedKey 'abc123'

## <a name="section-5-verify-your-connections"></a>セクション 5: 接続を確認する
Azure Portal または PowerShell を使って、接続を確認できます。 確認するときは、接続が作成されるまで&1; ～&2; 分待たなければならない場合があります。 接続が成功すると、接続状態が [接続中] から [接続済み] に変わります。

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


