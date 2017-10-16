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
ms.date: 04/21/2017
ms.author: cherylmc
ms.openlocfilehash: ad5700f1a85567a3e7f4ef80b778183929cb0d68
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="connect-virtual-networks-from-different-deployment-models-using-the-portal"></a>ポータルを使って異なるデプロイ モデルの仮想ネットワークを接続する

この記事では、クラシック VNet を Resource Manager VNet に接続し、異なるデプロイ モデルにあるリソースを相互に通信できるようにする方法について説明します。 この記事の手順では主に Azure Portal を使用しますが、PowerShell を使ってこの構成を作成することもできます。この場合は、次のリストから PowerShell に関する記事を選択してください。

> [!div class="op_single_selector"]
> * [ポータル](vpn-gateway-connect-different-deployment-models-portal.md)
> * [PowerShell](vpn-gateway-connect-different-deployment-models-powershell.md)
> 
> 

クラシック VNet から Resource Manager VNet への接続は、VNet をオンプレミス サイトの場所に接続することと似ています。 どちらの接続タイプでも、VPN ゲートウェイを使用して、IPsec/IKE を使った安全なトンネルが確保されます。 別のサブスクリプション、別のリージョンに存在する VNet 間で接続を作成することができます。 オンプレミスのネットワークに既に接続されている VNet を接続することもできます。ただし、Vnet が構成されているゲートウェイが動的またはルート ベースである場合に限ります。 VNet 間接続の詳細については、この記事の最後にある「[VNet 間接続に関してよく寄せられる質問](#faq)」を参照してください。 

複数の VNet が同じリージョンに存在する場合は、それらを VNet ピアリングで接続することを検討してください。 VNet ピアリングは、VPN ゲートウェイを使いません。 詳細については、「 [VNet ピアリング](../virtual-network/virtual-network-peering-overview.md)」を参照してください。 

### <a name="before"></a>開始する前に

* 次の手順では、両方の VNet が既に作成されていることを前提にしています。 この記事を演習として使用しており、VNet がない場合は、手順内のリンクを使用して作成できます。
* これらの VNet のアドレス範囲が互いに重複しておらず、ゲートウェイの接続先になる可能性のある他の接続の範囲と重複していないことを確認します。
* Resource Manager と Service Management (クラシック) の両方に最新の PowerShell コマンドレットをインストールします。 この記事では、Azure Portal と PowerShell の両方を使います。 PowerShell は、クラシック VNet から Resource Manager VNet への接続を作成するために必要です。 詳細については、「 [Azure PowerShell のインストールと構成の方法](/powershell/azure/overview)」を参照してください。 

### <a name="values"></a>設定例

この値を使用して、テスト環境を作成できます。また、この値を参考にしながら、この記事の例を確認していくこともできます。

**クラシック VNet**

VNet name = ClassicVNet <br>
Address space = 10.0.0.0/24 <br>
Subnet-1 = 10.0.0.0/27 <br>
Resource Group = ClassicRG <br>
Location = West US <br>
GatewaySubnet = 10.0.0.32/28 <br>
Local site = RMVNetLocal <br>

**Resource Manager の VNet**

VNet name = RMVNet <br>
Address space = 192.168.0.0/16 <br>
Subnet-1 = 192.168.1.0/24 <br>
GatewaySubnet = 192.168.0.0/26 <br>
Resource Group = RG1 <br>
Location = East US <br>
Virtual network gateway name = RMGateway <br>
Gateway type = VPN <br>
VPN type = Route-based <br>
Gateway Public IP address name = rmgwpip <br>
Local network gateway = ClassicVNetLocal <br>
Connection name = RMtoClassic

### <a name="connection-overview"></a>接続の概要

この構成では、仮想ネットワーク間で IPsec/IKE VPN トンネルを介した VPN Gateway 接続を作成します。 VNet のアドレス範囲が、互いに重複していないこと、または接続先のすべてのローカル ネットワークと重複していないことを確認します。

次の表には、VNet の例とローカル サイトの定義方法の例を示します。

| 仮想ネットワーク | アドレス空間 | リージョン | ローカル ネットワーク サイトへの接続 |
|:--- |:--- |:--- |:--- |
| ClassicVNet |(10.0.0.0/24) |米国西部 | RMVNetLocal (192.168.0.0/16) |
| RMVNet | (192.168.0.0/16) |米国東部 |ClassicVNetLocal (10.0.0.0/24) |

## <a name="classicvnet"></a>セクション 1 - クラシック VNet 設定を構成する

このセクションでは、クラシック VNet のローカル ネットワーク (ローカル サイト) と仮想ネットワーク ゲートウェイを作成します。 クラシック VNet を所有しておらず、これらの手順を演習として実行している場合、[この記事](../virtual-network/virtual-networks-create-vnet-classic-pportal.md)と上記の設定[例](#values)の値を使用して VNet を作成できます。

ポータルを使ってクラシック仮想ネットワークを作成するときは、以下の手順を使って仮想ネットワーク ブレードに移動する必要があります。そうしないと、クラシック仮想ネットワークを作成するオプションが表示されません。

1. +をクリックして 新規作成 ブレードを開きます。
2. [Marketplace を検索] フィールドに「仮想ネットワーク」と入力します。 代わりに、[ネットワーク] -> [仮想ネットワーク] を選ぶと、クラシック VNet を作成するオプションが表示されません。
3. 検索結果の一覧から "仮想ネットワーク" を探してクリックし、[仮想ネットワーク] ブレードを開きます。 
4. [仮想ネットワーク] ブレードで、[クラシック] を選んでクラシック VNet を作成します。 

VPN Gateway と共に VNet を既に使用している場合、そのゲートウェイが動的であることを確認してください。 静的である場合、まず、その VPN Gateway を削除してから進む必要があります。

スクリーンショットは例として示されています。 例の値を実際の値で置き換えるか、[例](#values)の値を使用してください。

### 1.<a name="local"></a>ローカル サイトの構成

[Azure Portal](https://ms.portal.azure.com) を開き、Azure アカウントでサインインします。

1. **[すべてのリソース]** に移動し、リスト内で **ClassicVNet** を見つけます。
2. **[概要]** ブレードの **[VPN 接続]** セクションで、**[ゲートウェイ]** のグラフィックをクリックしてゲートウェイを作成します。

    ![VPN Gateway を構成する](./media/vpn-gateway-connect-different-deployment-models-portal/gatewaygraphic.png "VPN Gateway を構成する")
3. **[新しい VPN 接続]** ブレードの **[接続の種類]** で、**[サイト対サイト]** を選びます。
4. **[ローカル サイト]** で、**[必要な設定の構成]** をクリックします。 これにより、**[ローカル サイト]** ブレードが開きます。
5. **[ローカル サイト]** ブレードで、Resource Manager の VNet を参照するための名前を作成します。 たとえば、「RMVNetLocal」などにします。
6. Resource Manager VNet の VPN Gateway にパブリック IP アドレスが既にある場合は、**[VPN ゲートウェイの IP アドレス]** フィールドでその値を使います。 これらの手順を演習として実行しているか、Resource Manager VNet の仮想ネットワーク ゲートウェイがまだない場合、プレースホルダー IP アドレスを作成できます。 プレースホルダー IP アドレスで有効な形式が使われていることを確認します。 後で、プレースホルダー IP アドレスを、Resource Manager 仮想ネットワーク ゲートウェイのパブリック IP アドレスで置き換えます。
7. **[Client Address Space (クライアント アドレス空間)]** に対しては、Resource Manager VNet の仮想ネットワーク IP アドレス空間の値を使います。 この設定を使用して、Resource Manager 仮想ネットワークにルーティングするアドレス空間を指定します。
8. **[OK]** をクリックして値を保存し、**[新しい VPN 接続]** ブレードに戻ります。

### <a name="classicgw"></a>2.仮想ネットワーク ゲートウェイを作成する

1. **[新しい VPN 接続]** ブレードで、**[ゲートウェイをすぐに作成する]** チェック ボックスをオンにし、**[ゲートウェイの構成 (オプション)]** をクリックして **[ゲートウェイの構成]** ブレードを開きます。 

    ![ゲートウェイの構成ブレードを開く](./media/vpn-gateway-connect-different-deployment-models-portal/optionalgatewayconfiguration.png "ゲートウェイの構成ブレードを開く")
2. **[Subnet - Configure required settings (サブネット - 必要な設定の構成)]** ブレードをクリックして、**[サブネットの追加]** を開きます。 **[名前]** は、必須の値 **GatewaySubnet** で既に構成されています。
3. **[アドレス範囲]** は、ゲートウェイ サブネットの範囲を示します。 /29 アドレス範囲 (3 アドレス) でゲートウェイ サブネットを作成することもできますが、さらに多くの IP アドレスを含むゲートウエイ サブネットを作成することをお勧めします。 これにより、将来の構成でさらに多くの IP アドレスが必要になった場合にも対応できるようになります。 可能であれば、/27 または /28 を使います。 これらの手順を演習として使用している場合は、[例](#values)の値を参照してください。 **[OK]** をクリックして、ゲートウェイ サブネットを作成します。
4. **[ゲートウェイの構成]** ブレードの **[サイズ]** は、ゲートウェイの SKU を示します。 VPN Gateway のゲートウェイ SKU を選びます。
5. **[ルーティングの種類]** が **[動的]** であることを確認した後、**[OK]** をクリックして **[新しい VPN 接続]** ブレードに戻ります。
6. **[新しい VPN 接続]** ブレードで、**[OK]** をクリックして VPN Gateway の作成を開始します。 VPN Gateway の作成を完了するまでに、最大 45 分かかることがあります。

### <a name="ip"></a>3.仮想ネットワーク ゲートウェイのパブリック IP アドレスをコピーする

仮想ネットワーク ゲートウェイが作成されると、ゲートウェイ IP アドレスを確認できます。 

1. クラシック VNet に移動し、**[概要]** をクリックします。
2. **[VPN 接続]** をクリックして、[VPN 接続] ブレードを開きます。 [VPN 接続] ブレードで、パブリック IP アドレスを確認できます。 これは、仮想ネットワーク ゲートウェイに割り当てられたパブリック IP アドレスです。 
3. この IP アドレスを、書き留めるかコピーしておきます。 後で、Resource Manager ローカル ネットワーク ゲートウェイの構成を設定するときに使います。 ゲートウェイ接続の状態も見ることができます。 作成したローカル ネットワーク サイトが [接続中] になっていることに注意してください。 接続を作成すると、状態が変化します。
4. ゲートウェイの IP アドレスをコピーした後、ブレードを閉じます。

## <a name="rmvnet"></a>セクション 2 - Resource Manager の VNet の設定を構成する

このセクションでは、仮想ネットワーク ゲートウェイと、Resource Manager の VNet 用のローカル ネットワーク ゲートウェイを作成します。 Resource Manager VNet を所有しておらず、これらの手順を演習として実行している場合、[この記事](../virtual-network/virtual-networks-create-vnet-arm-pportal.md)と上記の設定[例](#values)の値を使用して VNet を作成できます。

スクリーンショットは例として示されています。 例の値を実際の値で置き換えるか、[例](#values)の値を使用してください。

### <a name="1-create-a-gateway-subnet"></a>1.ゲートウェイ サブネットの作成

仮想ネットワーク ゲートウェイを作成する前に、まずゲートウェイ サブネットを作成する必要があります。 CIDR カウント /28 以上 (/27、/26 など) で、ゲートウェイ サブネットを 作成します。

[!INCLUDE [vpn-gateway-no-nsg-include](../../includes/vpn-gateway-no-nsg-include.md)]

[!INCLUDE [vpn-gateway-add-gwsubnet-rm-portal](../../includes/vpn-gateway-add-gwsubnet-rm-portal-include.md)]

### <a name="creategw"></a>2.仮想ネットワーク ゲートウェイの作成

[!INCLUDE [vpn-gateway-add-gw-rm-portal](../../includes/vpn-gateway-add-gw-rm-portal-include.md)]

### <a name="createlng"></a>3.ローカル ネットワーク ゲートウェイの作成

ローカル ネットワーク ゲートウェイでは、クラシック VNet およびその仮想ネットワーク ゲートウェイに関連付けられているアドレス範囲とパブリック IP アドレスを指定します。

これらの手順を演習として実行している場合、次の設定を参照してください。

| 仮想ネットワーク | アドレス空間 | リージョン | ローカル ネットワーク サイトへの接続 |ゲートウェイのパブリック IP アドレス|
|:--- |:--- |:--- |:--- |:--- |
| ClassicVNet |(10.0.0.0/24) |米国西部 | RMVNetLocal (192.168.0.0/16) |ClassicVNet ゲートウェイに割り当てられているパブリック IP アドレス|
| RMVNet | (192.168.0.0/16) |米国東部 |ClassicVNetLocal (10.0.0.0/24) |RMVNet ゲートウェイに割り当てられているパブリック IP アドレス|

[!INCLUDE [vpn-gateway-add-lng-rm-portal](../../includes/vpn-gateway-add-lng-rm-portal-include.md)]

## <a name="modifylng"></a>セクション 3 - クラシック VNet ローカル サイトの設定を変更する

このセクションでは、ローカル サイト設定を指定するときに使用したプレースホルダー IP アドレスを、Resource Manager VPN Gateway の IP アドレスで置き換えます。 このセクションでは、クラシック (SM) PowerShell コマンドレットを使います。

1. Azure Portal でクラシック仮想ネットワークに移動します。
2. 仮想ネットワークのブレードで、**[概要]** をクリックします。
3. **[VPN 接続]** セクションのグラフィックで、ローカル サイトの名前をクリックします。

    ![VPN 接続](./media/vpn-gateway-connect-different-deployment-models-portal/vpnconnections.png "VPN 接続")
4. **[サイト対サイト VPN 接続]** ブレードで、サイトの名前をクリックします。

    ![サイト名](./media/vpn-gateway-connect-different-deployment-models-portal/sitetosite3.png "ローカル サイトの名前")
5. ローカル サイトの接続ブレードで、ローカル サイトの名前をクリックして、**[ローカル サイト]** ブレードを開きます。

    ![ローカル サイトを開く](./media/vpn-gateway-connect-different-deployment-models-portal/openlocal.png "ローカル サイトを開く")
6. **[ローカル サイト]** ブレードで、**VPN Gateway の IP アドレス**を、Resource Manager ゲートウェイの IP アドレスで置き換えます。

    ![ゲートウェイの IP アドレス](./media/vpn-gateway-connect-different-deployment-models-portal/gwipaddress.png "ゲートウェイの IP アドレス")
7. **[OK]** をクリックして、IP アドレスを更新します。

## <a name="RMtoclassic"></a>セクション 4 - Resource Manager からクラシックへの接続の作成

次の手順では、Azure Portal を使用して、Resource Manager VNet からクラシック VNet への接続を構成します。

1. **[すべてのリソース]** で、ローカル ネットワーク ゲートウェイを見つけます。 この例では、ローカル ネットワーク ゲートウェイは **ClassicVNetLocal** です。
2. **[構成]** をクリックし、IP アドレス値がクラシック VNet の VPN Gateway であることを確認します。 必要に応じて更新し、**[保存]** をクリックします。 ブレードを閉じます。
3. **[すべてのリソース]** で、ローカル ネットワーク ゲートウェイをクリックします。
4. **[接続]** をクリックして、[接続] ブレードを開きます。
5. **[接続]** ブレードで、**+** をクリックして接続を追加します。
6. **[接続の追加]** ブレードで、接続に名前を付けます。 たとえば、「RMtoClassic」のようにします。
7. このブレードでは、**[サイト対サイト]** が既に選択されています。
8. このサイトに関連付ける仮想ネットワーク ゲートウェイを選択します。
9. **共有キー**を作成します。 このキーは、クラシック VNet から Resource Manager VNet に作成する接続でも使用されます。 キーは、生成することも、作成することもできます。 この例では "abc123" を使いますが、さらに複雑な値を使うこともできます (推奨)。
10. **[OK]** をクリックして、接続を作成します。

##<a name="classictoRM"></a>セクション 5 - クラシックから Resource Manager への接続の作成

次の手順では、クラシック VNet から Resource Manager VNet への接続を構成します。 この手順には PowerShell が必要です。 ポータルでこの接続を作成することはできません。 クラシック (SM) と Resource Manager (RM) の両方の PowerShell コマンドレットをダウンロードしてインストールしていることを確認してください。

### <a name="1-connect-to-your-azure-account"></a>1.Azure アカウントに接続する

管理者特権を使って PowerShell コンソールを開き、Azure アカウントにログインします。 次のコマンドレットは、Azure アカウントのログイン資格情報をユーザーに求めます。 ログイン後にアカウント設定がダウンロードされ、Azure PowerShell で使用できるようになります。

```powershell
Login-AzureRmAccount
```
   
複数のサブスクリプションがある場合は、Azure サブスクリプションの一覧が表示されます。

```powershell
Get-AzureRmSubscription
```

使用するサブスクリプションを指定します。 

```powershell
Select-AzureRmSubscription -SubscriptionName "Name of subscription"
```

従来の PowerShell コマンドレットを使用して、Azure アカウントを追加します (SM)。 これを行うには、次のコマンドを使用できます。

```powershell
Add-AzureAccount
```

### <a name="2-view-the-network-configuration-file-values"></a>2.ネットワーク構成ファイルの値を表示する

Azure Portal で VNet を作成するときには、Azure で使用される完全名は Azure Portal に表示されません。 たとえば、Azure Portal に "ClassicVNet" という名前で表示されている VNet が、ネットワーク構成ファイルではそれよりもかなり長い名前である可能性があります。 つまり、実際には "Group ClassicRG ClassicVNet" のような名前である場合があります。 次の手順では、ネットワーク構成ファイルをダウンロードして、値を表示します。

コンピューターにディレクトリを作成し、ネットワーク構成ファイルをそのディレクトリにエクスポートします。 この例では、ネットワーク構成ファイルは C:\AzureNet にエクスポートされます。

```powershell
Get-AzureVNetConfig -ExportToFile C:\AzureNet\NetworkConfig.xml
```

テキスト エディターでファイルを開き、クラシック VNet の名前を確認します。 PowerShell コマンドレットを実行するときは、ネットワーク構成ファイルの名前を使用します。

- VNet 名は **VirtualNetworkSite name =** と示されています
- サイト名は **LocalNetworkSite name=** と示されています

### <a name="3-create-the-connection"></a>3.接続の作成

共有キーを設定し、クラシック VNet から Resource Manager VNet への接続を作成します。 ポータルを使用して共有キーを設定することはできません。 次の手順を実行するときは、クラシック バージョンの PowerShell コマンドレットを使用してログインします。 それには、**Add-azureaccount** を使用します。 それ以外の場合は、"-AzureVNetGatewayKey" を設定できません。

- この例では、**-VNetName** はクラシック VNet の名前で、ネットワーク構成ファイルに示されているとおりです。 
- **-LocalNetworkSiteName** はローカル サイトに指定した名前で、ネットワーク構成ファイルに示されているとおりです。
- **-SharedKey** は、生成および指定する値です。 この例では *abc123* を使いましたが、さらに複雑な値を生成できます。 ここに指定する値は、Resource Manager からクラシックへの接続を作成するときに指定したものと同じ値である必要があります。

```powershell
Set-AzureVNetGatewayKey -VNetName "Group ClassicRG ClassicVNet" `
-LocalNetworkSiteName "172B9E16_RMVNetLocal" -SharedKey abc123
```

##<a name="verify"></a>セクション 6 - 接続を確認する

Azure Portal または PowerShell を使って、接続を確認できます。 確認するときは、接続が作成されるまで 1 ～ 2 分待たなければならない場合があります。 接続が成功すると、接続性の状態が [接続中] から [接続済み] に変わります。

### <a name="to-verify-the-connection-from-your-classic-vnet-to-your-resource-manager-vnet"></a>クラシック VNet から Resource Manager VNet への接続を確認するには

[!INCLUDE [vpn-gateway-verify-connection-azureportal-classic](../../includes/vpn-gateway-verify-connection-azureportal-classic-include.md)]

###<a name="to-verify-the-connection-from-your-resource-manager-vnet-to-your-classic-vnet"></a>Resource Manager VNet からクラシック VNet への接続を確認するには

[!INCLUDE [vpn-gateway-verify-connection-portal-rm](../../includes/vpn-gateway-verify-connection-portal-rm-include.md)]

## <a name="faq"></a>VNet 間接続に関してよく寄せられる質問

[!INCLUDE [vpn-gateway-vnet-vnet-faq](../../includes/vpn-gateway-faq-vnet-vnet-include.md)]
