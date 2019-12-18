---
title: 'クラシック仮想ネットワークを Azure Resource Manager VNet に接続する: ポータル | Microsoft Docs'
description: VPN Gateway とポータルを使用して従来の VNets を Resource Manager VNets に接続する手順
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 09/24/2019
ms.author: cherylmc
ms.openlocfilehash: 722907328fe17c4116f4f8d948e081f9582ca712
ms.sourcegitcommit: 3f22ae300425fb30be47992c7e46f0abc2e68478
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/25/2019
ms.locfileid: "71266575"
---
# <a name="connect-virtual-networks-from-different-deployment-models-using-the-portal"></a>ポータルを使って異なるデプロイ モデルの仮想ネットワークを接続する

この記事では、クラシック VNet を Resource Manager VNet に接続し、異なるデプロイ モデルにあるリソースを相互に通信できるようにする方法について説明します。 この記事の手順では主に Azure Portal を使用しますが、PowerShell を使ってこの構成を作成することもできます。この場合は、次のリストから PowerShell に関する記事を選択してください。

> [!div class="op_single_selector"]
> * [ポータル](vpn-gateway-connect-different-deployment-models-portal.md)
> * [PowerShell](vpn-gateway-connect-different-deployment-models-powershell.md)
> 
> 

クラシック VNet から Resource Manager VNet への接続は、VNet をオンプレミス サイトの場所に接続することと似ています。 どちらの接続タイプでも、VPN ゲートウェイを使用して、IPsec/IKE を使った安全なトンネルが確保されます。 別のサブスクリプション、別のリージョンに存在する VNet 間で接続を作成することができます。 オンプレミスのネットワークに既に接続されている VNet を接続することもできます。ただし、Vnet が構成されているゲートウェイが動的またはルート ベースである場合に限ります。 VNet 間接続の詳細については、この記事の最後にある「[VNet 間接続に関してよく寄せられる質問](#faq)」を参照してください。 

仮想ネットワーク ゲートウェイがまだなく、新しく作成しない場合は、代わりに VNet ピアリングを使用して VNet を接続することを検討する可能性があります。 VNet ピアリングは、VPN ゲートウェイを使用しません。 詳細については、「 [VNet ピアリング](../virtual-network/virtual-network-peering-overview.md)」を参照してください。

### <a name="before"></a>開始する前に

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

* 次の手順では、両方の VNet が既に作成されていることを前提にしています。 この記事を演習として使用しており、VNet がない場合は、手順内のリンクを使用して作成できます。
* これらの VNet のアドレス範囲が互いに重複しておらず、ゲートウェイの接続先になる可能性のある他の接続の範囲と重複していないことを確認します。
* Resource Manager と Service Management (クラシック) の両方に最新の PowerShell コマンドレットをインストールします。 この記事では、Azure Portal と PowerShell の両方を使います。 PowerShell は、クラシック VNet から Resource Manager VNet への接続を作成するために必要です。 詳細については、「 [Azure PowerShell のインストールと構成の方法](/powershell/azure/overview)」を参照してください。 

### <a name="values"></a>設定例

この値を使用して、テスト環境を作成できます。また、この値を参考にしながら、この記事の例を確認していくこともできます。

**クラシック VNet**

VNet name = ClassicVNet <br>
Address space = 10.0.0.0/24 <br>
サブネット名 = Subnet-1 <br>
サブネットのアドレス範囲 = 10.0.0.0/27 <br>
サブスクリプション = 使用するサブスクリプション <br>
Resource Group = ClassicRG <br>
Location = West US <br>
GatewaySubnet = 10.0.0.32/28 <br>
Local site = RMVNetLocal <br>

**Resource Manager の VNet**

VNet name = RMVNet <br>
Address space = 192.168.0.0/16 <br>
Resource Group = RG1 <br>
Location = East US <br>
サブネット名 = Subnet-1 <br>
アドレス範囲 = 192.168.1.0/24 <br>
GatewaySubnet = 192.168.0.0/26 <br>
Virtual network gateway name = RMGateway <br>
Gateway type = VPN <br>
VPN type = Route-based <br>
SKU = VpnGw1 <br>
Location = East US <br>
仮想ネットワーク = RMVNet <br> (VPN ゲートウェイをこの VNet に関連付ける) 最初の IP 構成 = rmgwpip <br> (ゲートウェイ パブリック IP アドレス) ローカル ネットワーク ゲートウェイ = ClassicVNetLocal <br>
Connection name = RMtoClassic

### <a name="connectoverview"></a>接続の概要

この構成では、仮想ネットワーク間で IPsec/IKE VPN トンネルを介した VPN Gateway 接続を作成します。 VNet のアドレス範囲が、互いに重複していないこと、または接続先のすべてのローカル ネットワークと重複していないことを確認します。

次の表には、VNet の例とローカル サイトの定義方法の例を示します。

| Virtual Network | アドレス空間 | リージョン | ローカル ネットワーク サイトへの接続 |
|:--- |:--- |:--- |:--- |
| ClassicVNet |(10.0.0.0/24) |米国西部 | RMVNetLocal (192.168.0.0/16) |
| RMVNet | (192.168.0.0/16) |East US |ClassicVNetLocal (10.0.0.0/24) |

## <a name="classicvnet"></a>セクション 1 - クラシック VNet 設定を構成する

このセクションでは、クラシック VNet、ローカル ネットワーク (ローカル サイト)、および仮想ネットワーク ゲートウェイを作成します。 スクリーンショットは例として示されています。 例の値を実際の値で置き換えるか、[例](#values)の値を使用してください。

### 1.<a name="classicvnet"></a>クラシック VNet の作成

クラシック VNet を所有しておらず、これらの手順を演習として実行している場合、[この記事](../virtual-network/virtual-networks-create-vnet-classic-pportal.md)と上記の設定[例](#values)の値を使用して VNet を作成できます。

VPN Gateway と共に VNet を既に使用している場合、そのゲートウェイが動的であることを確認してください。 静的である場合は、まず VPN ゲートウェイを削除してから、「[ローカル サイトの構成](#local)」に進む必要があります。

1. [Azure Portal](https://ms.portal.azure.com) を開き、Azure アカウントでサインインします。
2. **[+ リソースの作成]** をクリックして、"新しい" ページを開きます。
3. [Marketplace を検索] フィールドに「仮想ネットワーク」と入力します。 代わりに、[ネットワーク] -> [仮想ネットワーク] を選ぶと、クラシック VNet を作成するオプションが表示されません。
4. 検索結果の一覧から "仮想ネットワーク" を探してクリックし、[仮想ネットワーク] ページを開きます。 
5. [仮想ネットワーク] ページで、[クラシック] を選んでクラシック VNet を作成します。 ここで既定値を使用すると、Resource Manager VNet が作成されます。

### 2.<a name="local"></a>ローカル サイトの構成

1. **[すべてのリソース]** に移動し、リスト内で **ClassicVNet** を見つけます。
2. **[概要]** ページの **[VPN 接続]** セクションで、 **[ゲートウェイ]** をクリックしてゲートウェイを作成します。
  ![VPN Gateway を構成する](./media/vpn-gateway-connect-different-deployment-models-portal/gatewaygraphic.png "VPN Gateway を構成する")
3. **[新しい VPN 接続]** ページの **[接続の種類]** で、 **[サイト対サイト]** を選びます。
4. **[ローカル サイト]** で、 **[必要な設定の構成]** をクリックします。 これにより、 **[ローカル サイト]** ページが開きます。
5. **[ローカル サイト]** ページで、Resource Manager の VNet を参照するための名前を作成します。 たとえば、「RMVNetLocal」などにします。
6. Resource Manager VNet の VPN Gateway にパブリック IP アドレスが既にある場合は、 **[VPN ゲートウェイの IP アドレス]** フィールドでその値を使います。 これらの手順を演習として実行しているか、Resource Manager VNet の仮想ネットワーク ゲートウェイがまだない場合、プレースホルダー IP アドレスを作成できます。 プレースホルダー IP アドレスで有効な形式が使われていることを確認します。 後で、プレースホルダー IP アドレスを、Resource Manager 仮想ネットワーク ゲートウェイのパブリック IP アドレスで置き換えます。
7. **[クライアント アドレス空間]** に対しては、Resource Manager VNet の仮想ネットワーク IP アドレス空間の[値](#connectoverview)を使います。 この設定を使用して、Resource Manager 仮想ネットワークにルーティングするアドレス空間を指定します。 この例では、RMVNet のアドレス範囲である 192.168.0.0/16 を使用します。
8. **[OK]** をクリックして値を保存し、 **[新しい VPN 接続]** ページに戻ります。

### <a name="classicgw"></a>3.仮想ネットワーク ゲートウェイを作成する

1. **[新しい VPN 接続]** ページで、 **[ゲートウェイをすぐに作成する]** チェック ボックスをオンにします。
2. **[ゲートウェイの構成 (オプション)]** をクリックして、 **[ゲートウェイの構成]** ページを開きます。

   ![[ゲートウェイの構成] ページを開く](./media/vpn-gateway-connect-different-deployment-models-portal/optionalgatewayconfiguration.png "[ゲートウェイの構成] ページを開く")
3. **[Subnet - Configure required settings]\(サブネット - 必要な設定の構成\)** ページをクリックして、 **[サブネットの追加]** を開きます。 **[名前]** は、必須の値 **GatewaySubnet**。
4. **[アドレス範囲]** は、ゲートウェイ サブネットの範囲を示します。 /29 アドレス範囲 (3 アドレス) でゲートウェイ サブネットを作成することもできますが、さらに多くの IP アドレスを含むゲートウエイ サブネットを作成することをお勧めします。 これにより、将来の構成でさらに多くの IP アドレスが必要になった場合にも対応できるようになります。 可能であれば、/27 または /28 を使います。 これらの手順を演習として使用している場合は、[例の値](#values)を参照してください。 この例では、10.0.0.32/28 を使用します。 **[OK]** をクリックして、ゲートウェイ サブネットを作成します。
5. **[ゲートウェイの構成]** ページの **[サイズ]** は、ゲートウェイの SKU を示します。 VPN Gateway のゲートウェイ SKU を選びます。
6. **[ルーティングの種類]** が **[動的]** であることを確認した後、 **[OK]** をクリックして **[新しい VPN 接続]** ページに戻ります。
7. **[新しい VPN 接続]** ページで、 **[OK]** をクリックして VPN Gateway の作成を開始します。 VPN Gateway の作成を完了するまでに、最大 45 分かかることがあります。

### <a name="ip"></a>4.仮想ネットワーク ゲートウェイのパブリック IP アドレスをコピーする

仮想ネットワーク ゲートウェイが作成されると、ゲートウェイ IP アドレスを確認できます。 

1. クラシック VNet に移動し、 **[概要]** をクリックします。
2. **[VPN 接続]** をクリックして、[VPN 接続] ページを開きます。 [VPN 接続] ページで、パブリック IP アドレスを確認できます。 これは、仮想ネットワーク ゲートウェイに割り当てられたパブリック IP アドレスです。 IP アドレスを書き留めておきます。 後で、Resource Manager ローカル ネットワーク ゲートウェイの構成を設定するときに使います。 
3. ゲートウェイ接続の状態を見ることができます。 作成したローカル ネットワーク サイトが [接続中] になっていることに注意してください。 接続を作成すると、状態が変化します。 状態の表示を確認したらこのページを閉じます。

## <a name="rmvnet"></a>セクション 2 - Resource Manager の VNet の設定を構成する

このセクションでは、仮想ネットワーク ゲートウェイと、Resource Manager の VNet 用のローカル ネットワーク ゲートウェイを作成します。 スクリーンショットは例として示されています。 例の値を実際の値で置き換えるか、[例](#values)の値を使用してください。

### <a name="1-create-a-virtual-network"></a>1.仮想ネットワークの作成

**値の例:**

* VNet name = RMVNet <br>
* Address space = 192.168.0.0/16 <br>
* Resource Group = RG1 <br>
* Location = East US <br>
* サブネット名 = Subnet-1 <br>
* アドレス範囲 = 192.168.1.0/24 <br>

Resource Manager VNet を所有しておらず、これらの手順を演習として実行している場合は、例の値を使用して、[仮想ネットワークの作成](../virtual-network/quick-create-portal.md)の手順で仮想ネットワークを作成できます。

### <a name="creategw"></a>2.仮想ネットワーク ゲートウェイの作成

この手順では、VNet の仮想ネットワーク ゲートウェイを作成します。 選択したゲートウェイ SKU によっては、ゲートウェイの作成に 45 分以上かかる場合も少なくありません。

[!INCLUDE [About gateway subnets](../../includes/vpn-gateway-about-gwsubnet-portal-include.md)]

**値の例:**

* Virtual network gateway name = RMGateway <br>
* Gateway type = VPN <br>
* VPN type = Route-based <br>
* SKU = VpnGw1 <br>
* Location = East US <br>
* 仮想ネットワーク = RMVNet <br>
* GatewaySubnet = 192.168.0.0/26 <br>
* 最初の IP構成 = rmgwpip <br>

[!INCLUDE [vpn-gateway-add-gw-rm-portal](../../includes/vpn-gateway-add-gw-rm-portal-include.md)]

[!INCLUDE [vpn-gateway-no-nsg-include](../../includes/vpn-gateway-no-nsg-include.md)]

### <a name="createlng"></a>3.ローカル ネットワーク ゲートウェイの作成

**値の例:** Local network gateway = ClassicVNetLocal

| Virtual Network | アドレス空間 | リージョン | ローカル ネットワーク サイトへの接続 |ゲートウェイのパブリック IP アドレス|
|:--- |:--- |:--- |:--- |:--- |
| ClassicVNet |(10.0.0.0/24) |米国西部 | RMVNetLocal (192.168.0.0/16) |ClassicVNet ゲートウェイに割り当てられているパブリック IP アドレス|
| RMVNet | (192.168.0.0/16) |East US |ClassicVNetLocal (10.0.0.0/24) |RMVNet ゲートウェイに割り当てられているパブリック IP アドレス|

ローカル ネットワーク ゲートウェイでは、クラシック VNet およびその仮想ネットワーク ゲートウェイに関連付けられているアドレス範囲とパブリック IP アドレスを指定します。 これらの手順を演習として実行している場合、例の値を参照してください。

[!INCLUDE [vpn-gateway-add-lng-rm-portal](../../includes/vpn-gateway-add-lng-rm-portal-include.md)]

## <a name="modifylng"></a>セクション 3 - クラシック VNet ローカル サイトの設定を変更する

このセクションでは、ローカル サイト設定を指定するときに使用したプレースホルダー IP アドレスを、Resource Manager VPN Gateway の IP アドレスで置き換えます。 このセクションでは、クラシック (SM) PowerShell コマンドレットを使います。

1. Azure Portal でクラシック仮想ネットワークに移動します。
2. 仮想ネットワークのページで、 **[概要]** をクリックします。
3. **[VPN 接続]** セクションのグラフィックで、ローカル サイトの名前をクリックします。

   ![VPN 接続](./media/vpn-gateway-connect-different-deployment-models-portal/vpnconnections.png "VPN 接続")
4. **[サイト対サイト VPN 接続]** ページで、サイトの名前をクリックします。

   ![サイト名](./media/vpn-gateway-connect-different-deployment-models-portal/sitetosite3.png "ローカル サイトの名前")
5. ローカル サイトの接続ページで、ローカル サイトの名前をクリックして、 **[ローカル サイト]** ページを開きます。

   ![ローカル サイトを開く](./media/vpn-gateway-connect-different-deployment-models-portal/openlocal.png "ローカル サイトを開く")
6. **[ローカル サイト]** ページで、**VPN Gateway の IP アドレス**を、Resource Manager ゲートウェイの IP アドレスで置き換えます。

   ![ゲートウェイの IP アドレス](./media/vpn-gateway-connect-different-deployment-models-portal/gwipaddress.png "ゲートウェイの IP アドレス")
7. **[OK]** をクリックして、IP アドレスを更新します。

## <a name="RMtoclassic"></a>セクション 4 - Resource Manager からクラシックへの接続の作成

次の手順では、Azure Portal を使用して、Resource Manager VNet からクラシック VNet への接続を構成します。

1. **[すべてのリソース]** で、ローカル ネットワーク ゲートウェイを見つけます。 この例では、ローカル ネットワーク ゲートウェイは **ClassicVNetLocal** です。
2. **[構成]** をクリックし、IP アドレス値がクラシック VNet の VPN Gateway であることを確認します。 必要に応じて更新し、 **[保存]** をクリックします。 ページを閉じます。
3. **[すべてのリソース]** で、ローカル ネットワーク ゲートウェイをクリックします。
4. **[接続]** をクリックして、[接続] ページを開きます。
5. **[接続]** ページで、 **[+]** をクリックして接続を追加します。
6. **[接続の追加]** ページで、接続に名前を付けます。 たとえば、「RMtoClassic」のようにします。
7. このページでは、 **[サイト対サイト]** が既に選択されています。
8. このサイトに関連付ける仮想ネットワーク ゲートウェイを選択します。
9. **共有キー**を作成します。 このキーは、クラシック VNet から Resource Manager VNet に作成する接続でも使用されます。 キーは、生成することも、作成することもできます。 この例では "abc123" を使いますが、さらに複雑な値を使うこともできます (推奨)。
10. **[OK]** をクリックして、接続を作成します。

## <a name="classictoRM"></a>セクション 5 - クラシックから Resource Manager への接続の作成

次の手順では、クラシック VNet から Resource Manager VNet への接続を構成します。 この手順には PowerShell が必要です。 ポータルでこの接続を作成することはできません。 クラシック (SM) と Resource Manager (RM) の両方の PowerShell コマンドレットをダウンロードしてインストールしていることを確認してください。

### <a name="1-connect-to-your-azure-account"></a>1.Azure アカウントに接続する

管理者特権を使って PowerShell コンソールを開き、Azure アカウントにログインします。 ログイン後にアカウント設定がダウンロードされ、Azure PowerShell で使用できるようになります。 次のコマンドレットは、Resource Manager デプロイ モデルの Azure アカウント用のログイン資格情報をユーザーに求めます。

```powershell
Connect-AzAccount
```

Azure サブスクリプションの一覧を取得します。

```powershell
Get-AzSubscription
```

複数のサブスクリプションがある場合は、使用するサブスクリプションを指定します。

```powershell
Select-AzSubscription -SubscriptionName "Name of subscription"
```

次に、ログインしてクラシック PowerShell コマンドレット (Service Management) を使用します。 クラシック デプロイ モデルに Azure アカウントを追加するには、次のコマンドを使用します。

```powershell
Add-AzureAccount
```

サブスクリプションの一覧を取得します。 この手順は、Azure モジュールのインストールによっては、Service Management コマンドレットを追加するときに必要になる可能性があります。

```powershell
Get-AzureSubscription
```

複数のサブスクリプションがある場合は、使用するサブスクリプションを指定します。

```powershell
Select-AzureSubscription -SubscriptionName "Name of subscription"
```

### <a name="2-view-the-network-configuration-file-values"></a>2.ネットワーク構成ファイルの値を表示する

Azure Portal で VNet を作成するときには、Azure で使用される完全名は Azure Portal に表示されません。 たとえば、Azure Portal に "ClassicVNet" という名前で表示されている VNet が、ネットワーク構成ファイルではそれよりもかなり長い名前である可能性があります。 名前は次のように表示されます。'Group ClassicRG ClassicVNet' 次の手順では、ネットワーク構成ファイルをダウンロードして、値を表示します。

コンピューターにディレクトリを作成し、ネットワーク構成ファイルをそのディレクトリにエクスポートします。 この例では、ネットワーク構成ファイルは C:\AzureNet にエクスポートされます。

```powershell
Get-AzureVNetConfig -ExportToFile C:\AzureNet\NetworkConfig.xml
```

テキスト エディターでファイルを開き、クラシック VNet の名前を確認します。 PowerShell コマンドレットを実行するときは、ネットワーク構成ファイルの名前を使用します。

- VNet 名は **VirtualNetworkSite name =** と示されています
- サイト名は **LocalNetworkSite name=** と示されています

### <a name="3-create-the-connection"></a>3.接続の作成

共有キーを設定し、クラシック VNet から Resource Manager VNet への接続を作成します。 ポータルを使用して共有キーを設定することはできません。 次の手順を実行するときは、クラシック バージョンの PowerShell コマンドレットを使用してログインします。 それには、**Add-azureaccount** を使用します。 それ以外の場合は、"-AzureVNetGatewayKey" を設定できません。

- この例では、 **-VNetName** はクラシック VNet の名前で、ネットワーク構成ファイルに示されているとおりです。 
- **-LocalNetworkSiteName** はローカル サイトに指定した名前で、ネットワーク構成ファイルに示されているとおりです。
- **-SharedKey** は、生成および指定する値です。 この例では *abc123* を使いましたが、さらに複雑な値を生成できます。 ここに指定する値は、Resource Manager からクラシックへの接続を作成するときに指定したものと同じ値である必要があります。

```powershell
Set-AzureVNetGatewayKey -VNetName "Group ClassicRG ClassicVNet" `
-LocalNetworkSiteName "172B9E16_RMVNetLocal" -SharedKey abc123
```

## <a name="verify"></a>セクション 6 - 接続を確認する

Azure Portal または PowerShell を使って、接続を確認できます。 確認するときは、接続が作成されるまで 1 ～ 2 分待たなければならない場合があります。 接続が成功すると、接続性の状態が [接続中] から [接続済み] に変わります。

### <a name="to-verify-the-connection-from-your-classic-vnet-to-your-resource-manager-vnet"></a>クラシック VNet から Resource Manager VNet への接続を確認するには

[!INCLUDE [vpn-gateway-verify-connection-azureportal-classic](../../includes/vpn-gateway-verify-connection-azureportal-classic-include.md)]

### <a name="to-verify-the-connection-from-your-resource-manager-vnet-to-your-classic-vnet"></a>Resource Manager VNet からクラシック VNet への接続を確認するには

[!INCLUDE [vpn-gateway-verify-connection-portal-rm](../../includes/vpn-gateway-verify-connection-portal-rm-include.md)]

## <a name="faq"></a>VNet 間接続に関してよく寄せられる質問

[!INCLUDE [vpn-gateway-vnet-vnet-faq](../../includes/vpn-gateway-faq-vnet-vnet-include.md)]
