---
title: 'VNet 間の接続の作成: クラシック: Azure portal | Microsoft Docs'
description: PowerShell と Azure Portal を使って複数の Azure 仮想ネットワークを接続します。
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: jpconnock
editor: ''
tags: azure-service-management
ms.assetid: ''
ms.service: vpn-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/14/2018
ms.author: cherylmc
ms.openlocfilehash: e323a8d71bbffd1d29ad793dff7b5b4a072b6979
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/18/2019
ms.locfileid: "59046124"
---
# <a name="configure-a-vnet-to-vnet-connection-classic"></a>VNet 間の接続の構成 (クラシック)

[!INCLUDE [deployment models](../../includes/vpn-gateway-classic-deployment-model-include.md)]

この記事では、仮想ネットワーク間で VPN ゲートウェイ接続を確立する方法について説明します。 仮想ネットワークが属しているリージョンやサブスクリプションは異なっていてもかまいません。 この記事のこの手順は、クラシック デプロイ モデルと Azure Portal に適用されます。 また、この構成の作成には、次のリストから別のオプションを選択して、別のデプロイ ツールまたはデプロイ モデルを使用することもできます。

> [!div class="op_single_selector"]
> * [Azure Portal](vpn-gateway-howto-vnet-vnet-resource-manager-portal.md)
> * [PowerShell](vpn-gateway-vnet-vnet-rm-ps.md)
> * [Azure CLI](vpn-gateway-howto-vnet-vnet-cli.md)
> * [Azure Portal (クラシック)](vpn-gateway-howto-vnet-vnet-portal-classic.md)
> * [異なるデプロイメント モデルの接続 - Azure Portal](vpn-gateway-connect-different-deployment-models-portal.md)
> * [異なるデプロイメント モデルの接続 - PowerShell](vpn-gateway-connect-different-deployment-models-powershell.md)
>
>

![VNet 間接続の図](./media/vpn-gateway-howto-vnet-vnet-portal-classic/v2vclassic.png)

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="about-vnet-to-vnet-connections"></a>VNet 間接続の概要

VPN Gateway を使用したクラシック デプロイ モデルでの仮想ネットワーク間 (VNet 間) の接続は、仮想ネットワークをオンプレミスのサイトの場所に接続することと似ています。 どちらの接続タイプでも、VPN ゲートウェイを使用して、IPsec/IKE を使った安全なトンネルが確保されます。

接続する VNet は、サブスクリプションやリージョンが異なっていてもかまいません。 マルチサイト構成と VNet 対 VNet 通信を組み合わせることができます。 そのため、クロスプレミス接続と仮想ネットワーク間接続とを組み合わせたネットワーク トポロジを確立することができます。

![VNet 間接続](./media/vpn-gateway-howto-vnet-vnet-portal-classic/aboutconnections.png)

### <a name="why"></a>仮想ネットワークを接続する理由

仮想ネットワークを接続するのは次のような場合です。

* **リージョン間の geo 冗長性および geo プレゼンス**

  * インターネット接続エンドポイントを介さず、安全な接続を使って独自の geo レプリケーションや geo 同期をセットアップすることができます。
  * Azure Load Balancer と Microsoft (またはサード パーティ) のクラスタリング テクノロジを使用し、複数の Azure リージョンをまたぐ geo 冗長性を備えた、可用性に優れたワークロードをセットアップすることができます。 たとえば、複数の Azure リージョンにまたがる SQL AlwaysOn 可用性グループをセットアップすることができます。
* **特定の地域内で強固な分離境界を備えた多層アプリケーション**

  * 同じリージョン内の相互に接続された複数の VNet を使って多層アプリケーションをセットアップすることができます。それぞれの層のアプリケーションが強固に分離され、安全な層間通信を実現することができます。
* **サブスクリプションや組織の境界を越えた通信を Azure 内で実現**

  * Azure サブスクリプションを複数所有している場合、異なるサブスクリプションからのワークロードを仮想ネットワークを介して安全に接続することができます。
  * 企業やサービス プロバイダーが、安全な VPN テクノロジを使用した組織間の通信を Azure 内で実現できます。

VNet 間接続の詳細については、この記事の最後にある「[VNet 間の考慮事項](#faq)」を参照してください。

### <a name="before-you-begin"></a>開始する前に

この演習を開始する前に、Azure サービス管理 (SM) PowerShell コマンドレットの最新バージョンをダウンロードしてインストールします。 詳細については、「 [Azure PowerShell のインストールと構成の方法](/powershell/azure/overview)」を参照してください。 ほとんどの手順でポータルを使用しますが、VNet 間の接続を確立する場合は、PowerShell を使う必要があります。 Azure Portal を使用して接続を確立することはできません。

## <a name="plan"></a>手順 1 - IP アドレス範囲を決める

仮想ネットワークの構成に使用する範囲を決めることは重要です。 この構成では、VNet のアドレス範囲が、互いにまたは接続先のすべてのローカル ネットワークと重複していないことを確認する必要があります。

VNet の定義の例を下表に示します。 範囲はあくまでも参考です。 仮想ネットワークの範囲をメモしておいてください。 この情報は後続の手順で必要になります。

**例**

| Virtual Network | アドレス空間 | リージョン | ローカル ネットワーク サイトへの接続 |
|:--- |:--- |:--- |:--- |
| TestVNet1 |TestVNet1<br>(10.11.0.0/16)<br>(10.12.0.0/16) |米国東部 |VNet4Local<br>(10.41.0.0/16)<br>(10.42.0.0/16) |
| TestVNet4 |TestVNet4<br>(10.41.0.0/16)<br>(10.42.0.0/16) |米国西部 |VNet1Local<br>(10.11.0.0/16)<br>(10.12.0.0/16) |

## <a name="vnetvalues"></a>手順 2 - 仮想ネットワークを作成する

[Azure Portal](https://portal.azure.com) で 2 つの仮想ネットワークを作成します。 従来型の仮想ネットワークを作成する手順については、[従来型の仮想ネットワークの作成](../virtual-network/virtual-networks-create-vnet-classic-pportal.md)に関するページをご覧ください。 

ポータルを使ってクラシック仮想ネットワークを作成するときは、以下の手順を使って仮想ネットワーク ページに移動する必要があります。そうしないと、クラシック仮想ネットワークを作成するオプションが表示されません。

1. [+] をクリックして [新規作成] ページを開きます。
2. [Marketplace を検索] フィールドに「仮想ネットワーク」と入力します。 代わりに、[ネットワーク] -> [仮想ネットワーク] を選ぶと、クラシック VNet を作成するオプションが表示されません。
3. 検索結果の一覧から "仮想ネットワーク" を探してクリックし、[仮想ネットワーク] ページを開きます。 
4. [仮想ネットワーク] ページで、[クラシック] を選んでクラシック VNet を作成します。 

この記事を演習として使用している場合、以下のサンプル値を使用できます。

**TestVNet1 の値**

名前:TestVNet1<br>
アドレス空間:10.11.0.0/16, 10.12.0.0/16 (オプション)<br>
サブネット名: 既定<br>
サブネットのアドレス範囲: 10.11.0.1/24<br>
リソース グループ: ClassicRG<br>
場所:米国東部<br>
GatewaySubnet: 10.11.1.0/27

**TestVNet4 の値**

名前:TestVNet4<br>
アドレス空間:10.41.0.0/16、10.42.0.0/16 (オプション)<br>
サブネット名: 既定<br>
サブネットのアドレス範囲: 10.41.0.1/24<br>
リソース グループ: ClassicRG<br>
場所:米国西部<br>
GatewaySubnet: 10.41.1.0/27

**VNet を作成するときは、次の設定に注意してください。**

* **[仮想ネットワーク アドレス空間]** – [仮想ネットワーク アドレス空間] ページには、仮想ネットワークに使用するアドレス範囲を指定します。 これらが動的 IP アドレスとして、この仮想ネットワークにデプロイする VM や各種ロール インスタンスに割り当てられます。<br>他の VNet や、この VNet の接続先となるオンプレミスの場所のアドレス空間と重複するアドレス空間を選択することはできません。

* **[場所]** : 仮想ネットワークを作成するとき、仮想ネットワークを Azure の場所 (リージョン) に関連付けます。 たとえば、仮想ネットワークにデプロイされた VM を物理的に米国西部に配置する場合は、その場所を選択します。 仮想ネットワークを作成した後で、その仮想ネットワークに関連付けられた場所を変更することはできません。

**VNet を作成した後、次の設定を追加できます。**

* **[アドレス空間]** – この構成用に追加のアドレス空間は必要ありませんが、VNet を作成した後にさらにアドレス空間を追加することもできます。

* **[サブネット]** – この構成用に追加のサブネットは必要ありませんが、他のロール インスタンスとは別個のサブネットに VM を配置することができます。

* **[DNS サーバー]** – DNS サーバー名と IP アドレスを入力します。 この設定で、DNS サーバーは作成されません。 この設定では、この仮想ネットワークの名前解決に使用する DNS サーバーを指定することができます。

このセクションでは、接続の種類とローカル サイトを構成し、ゲートウェイを作成します。

## <a name="localsite"></a>手順 3 - ローカル サイトを構成する

Azure では、各ローカル ネットワーク サイトで指定されている設定を使用して、VNet 間でトラフィックをルーティングする方法を決定します。 各 VNet は、トラフィックのルーティング先とする対応するローカル ネットワークをポイントする必要があります。 各ローカル ネットワーク サイトの参照に使用する名前を指定します。 わかりやすい名前を使用することをお勧めします。

たとえば、TestVNet1 は "VNet4Local" という名前で作成するローカル ネットワーク サイトに接続します。 VNet4Local の設定には、TestVNet4 のアドレス プレフィックスが含まれています。

それぞれの VNet のローカル サイトが、もう一方の VNet になります。 この構成には、次の例の値が使用されます。

| Virtual Network | アドレス空間 | リージョン | ローカル ネットワーク サイトへの接続 |
|:--- |:--- |:--- |:--- |
| TestVNet1 |TestVNet1<br>(10.11.0.0/16)<br>(10.12.0.0/16) |米国東部 |VNet4Local<br>(10.41.0.0/16)<br>(10.42.0.0/16) |
| TestVNet4 |TestVNet4<br>(10.41.0.0/16)<br>(10.42.0.0/16) |米国西部 |VNet1Local<br>(10.11.0.0/16)<br>(10.12.0.0/16) |

1. Azure Portal で TestVNet1 を見つけます。 ページの **[VPN 接続]** セクションで、**[ゲートウェイ]** をクリックします。

    ![ゲートウェイがない場合](./media/vpn-gateway-howto-vnet-vnet-portal-classic/nogateway.png)
2. **[新しい VPN 接続]** ページで、**[サイト対サイト]** を選択します。
3. **[ローカル サイト]** をクリックして [ローカル サイト] ページを開き、設定を構成します。
4. **[ローカル サイト]** ページで、ローカル サイトに名前を付けます。 この例では、ローカル サイトに "VNet4Local" という名前を付けます。
5. **[VPN ゲートウェイの IP アドレス]** には、有効な形式であればどの IP アドレスでも使用できます。 通常は、VPN デバイスの実際の外部 IP アドレスを使用します。 ただし、クラシック VNet 間構成では、VNet のゲートウェイに割り当てられているパブリック IP アドレスを使用します。 仮想ネットワーク ゲートウェイをまだ作成していない場合は、プレース ホルダーとして任意の有効なパブリック IP アドレスを指定します。<br>この項目を空白のままにしないでください。この構成では省略可能ではありません。 Azure によってゲートウェイが生成されたら、後でこれらの設定に戻り、対応する仮想ネットワーク ゲートウェイ IP アドレスを使用して設定を構成します。
6. **[クライアント アドレス空間]** には、その他の VNet のアドレス空間を使用します。 計画例を参照してください。 **[OK]** をクリックして設定を保存し、**[新しい VPN 接続]** ページに戻ります。

    ![ローカル サイト](./media/vpn-gateway-howto-vnet-vnet-portal-classic/localsite.png)

## <a name="gw"></a>手順 4 - 仮想ネットワーク ゲートウェイを作成する

各仮想ネットワークには、仮想ネットワーク ゲートウェイが必要です。 仮想ネットワーク ゲートウェイは、トラフィックをルーティングおよび暗号化します。

1. **[新しい VPN 接続]** ページで、**[ゲートウェイをすぐに作成する]** チェック ボックスをオンにします。
2. **[サブネット、サイズ、ルーティングの種類]** をクリックします。 **[ゲートウェイの構成]** ページで、**[サブネット]** をクリックします。
3. ゲートウェイ サブネットの名前は、必須の名前 "GatewaySubnet" を使用して自動的に入力されます。 **[アドレス範囲]** には、VPN Gateway サービスに割り当てられている IP アドレスが含まれています。 一部の構成ではゲートウェイ サブネット /29 を使用できますが、将来の構成でゲートウェイ サービスに、より多くの IP アドレスが必要になった場合に備えて、/28 または /27 を使用することをお勧めします。 この例の設定では、10.11.1.0/27 を使用します。 アドレス空間を調整し、**[OK]** をクリックします。
4. **[ゲートウェイのサイズ]** を構成します。 この設定では、[ゲートウェイの SKU](vpn-gateway-about-vpn-gateway-settings.md#gwsku) を参照します。
5. **[ルーティングの種類]** を構成します。 この構成のルーティングの種類は**動的**である必要があります。 ゲートウェイを破棄し、新しいゲートウェイを作成しない限り、ルーティングの種類を後で変更することはできません。
6. Click **OK**.
7. **[新しい VPN 接続]** ページで、**[OK]** をクリックして仮想ネットワーク ゲートウェイの作成を開始します。 選択したゲートウェイ SKU によっては、ゲートウェイの作成に 45 分以上かかる場合も少なくありません。

## <a name="vnet4settings"></a>手順 5 - TestVNet4 設定を構成する

必要に応じて値を置き換えて、[ローカル サイトを作成する](#localsite)手順と[仮想ネットワーク ゲートウェイを作成する](#gw)手順を繰り返し、TestVNet4 を構成します。 これを演習として実行している場合、[例の値](#vnetvalues)を使用します。

## <a name="updatelocal"></a>手順 6 - ローカル サイトを更新する

両方の VNet に対して仮想ネットワーク ゲートウェイを作成したら、ローカル サイトの **VPN ゲートウェイの IP アドレス**の値を調整する必要があります。

|VNet の名前|接続されているサイト|ゲートウェイ IP アドレス|
|:--- |:--- |:--- |
|TestVNet1|VNet4Local|TestVNet4 の VPN ゲートウェイ IP アドレス|
|TestVNet4|VNet1Local|TestVNet1 の VPN ゲートウェイ IP アドレス|

### <a name="part-1---get-the-virtual-network-gateway-public-ip-address"></a>パート 1 - 仮想ネットワーク ゲートウェイのパブリック IP アドレスを取得する

1. Azure Portal で、使用する仮想ネットワークを見つけます。
2. VNet の **[概要]** ページをクリックして開きます。 このページの **[VPN 接続]** に、仮想ネットワーク ゲートウェイの IP アドレスが表示されます。

   ![パブリック IP](./media/vpn-gateway-howto-vnet-vnet-portal-classic/publicIP.png)
3. IP アドレスをコピーします。 これは、次のセクションで使用します。
4. TestVNet4 について、これらの手順を繰り返します。

### <a name="part-2---modify-the-local-sites"></a>パート 2 - ローカル サイトを変更する

1. Azure Portal で、使用する仮想ネットワークを見つけます。
2. VNet の **[概要]** ページで、ローカル サイトをクリックします。

   ![作成されたローカル サイト](./media/vpn-gateway-howto-vnet-vnet-portal-classic/local.png)
3. **[サイト対サイト VPN 接続]** ページで、変更するローカル サイトの名前をクリックします。

   ![ローカル サイトを開く](./media/vpn-gateway-howto-vnet-vnet-portal-classic/openlocal.png)
4. 変更する **[ローカル サイト]** をクリックします。

   ![サイトを変更する](./media/vpn-gateway-howto-vnet-vnet-portal-classic/connections.png)
5. **[VPN ゲートウェイの IP アドレス]** を更新し、**[OK]** をクリックして設定を保存します。

   ![ゲートウェイの IP](./media/vpn-gateway-howto-vnet-vnet-portal-classic/gwupdate.png)
6. 他のページを閉じます。
7. TestVNet4 について、これらの手順を繰り返します。

## <a name="getvalues"></a>手順 7 - ネットワーク構成ファイルから値を取得する

Azure Portal でクラシック VNets を作成するときに表示される名前は、PowerShell に使用する完全な名前ではありません。 たとえば、Azure Portal に **TestVNet1** という名前で表示されている VNet は、ネットワーク構成ファイルではそれよりもかなり長い名前である可能性があります。 名前は次のように表示されます。**Group ClassicRG TestVNet1**。 接続を作成するときは、ネットワーク構成ファイルに示されている値を使用することが重要です。

次の手順では、Azure アカウントに接続し、ネットワーク構成ファイルをダウンロードして表示し、接続に必要な値を取得します。

1. Azure サービス管理 (SM) PowerShell コマンドレットの最新バージョンをダウンロードしてインストールします。 詳細については、「 [Azure PowerShell のインストールと構成の方法](/powershell/azure/overview)」を参照してください。

2. 管理者特権で PowerShell コンソールを開き、アカウントに接続します。 接続については、次の例を参考にしてください。

   ```powershell
   Connect-AzAccount
   ```

   アカウントのサブスクリプションを確認します。

   ```powershell
   Get-AzSubscription
   ```

   複数のサブスクリプションがある場合は、使用するサブスクリプションを選択します。

   ```powershell
   Select-AzSubscription -SubscriptionName "Replace_with_your_subscription_name"
   ```

   次に、次のコマンドレットを使用して、Azure サブスクリプションをクラシック デプロイ モデルの PowerShell に追加します。

   ```powershell
   Add-AzureAccount
   ```
3. ネットワーク構成ファイルをエクスポートして、表示します。 コンピューターにディレクトリを作成し、ネットワーク構成ファイルをそのディレクトリにエクスポートします。 この例では、ネットワーク構成ファイルは **C:\AzureNet** にエクスポートされます。

   ```powershell
   Get-AzureVNetConfig -ExportToFile C:\AzureNet\NetworkConfig.xml
   ```
4. テキスト エディターでファイルを開き、VNet とサイトの名前を確認します。 接続を作成するときには、これらの名前を使用します。<br>VNet 名は **VirtualNetworkSite name =** と示されています<br>サイト名は **LocalNetworkSiteRef name =** と示されています

## <a name="createconnections"></a>手順 8 - VPN ゲートウェイ接続を作成する

以上の手順がすべて完了したら、IPsec/IKE 事前共有キーを設定して接続を作成できます。 この一連の手順では、PowerShell を使用します。 クラシック デプロイ モデルの VNet 間接続を、Azure Portal で構成することはできません。

次の例では、共有キーがまったく同じであることに注目してください。 共有キーは常に一致する必要があります。 これらの例の値を、VNet やローカル ネットワーク サイトに使用する正確な名前で置き換えます。

1. TestVNet1 から TestVNet4 への接続を作成します。

   ```powershell
   Set-AzureVNetGatewayKey -VNetName 'Group ClassicRG TestVNet1' `
   -LocalNetworkSiteName '17BE5E2C_VNet4Local' -SharedKey A1b2C3D4
   ```
2. TestVNet4 から TestVNet1 への接続を作成します。

   ```powershell
   Set-AzureVNetGatewayKey -VNetName 'Group ClassicRG TestVNet4' `
   -LocalNetworkSiteName 'F7F7BFC7_VNet1Local' -SharedKey A1b2C3D4
   ```
3. 接続が初期化されるのを待ちます。 ゲートウェイが初期化されると、状態は "成功" になります。

   ```
   Error          :
   HttpStatusCode : OK
   Id             :
   Status         : Successful
   RequestId      :
   StatusCode     : OK
   ```

## <a name="faq"></a>クラシック VNet での VNet 間の考慮事項
* 仮想ネットワークが属しているサブスクリプションは異なっていてもかまいません。
* 仮想ネットワークが属している Azure リージョン (場所) は異なっていてもかまいません。
* クラウド サービスや負荷分散エンドポイントは、仮にそれらが相互に接続されていたとしても、仮想ネットワークの境界を越えることはできません。
* 複数の仮想ネットワークを接続する場合は、VPN デバイスは必要ありません。
* VNet 間接続でサポートされるのは、Azure Virtual Network の接続です。 仮想ネットワークにデプロイされていない仮想マシンやクラウド サービスを接続することはできません。
* VNet 対 VNet 通信には、動的ルーティング ゲートウェイが必要です。 Azure 静的ルーティング ゲートウェイはサポートされません。
* 仮想ネットワーク接続は、マルチサイト VPN と同時に使用することができます。 1 つの仮想ネットワーク VPN ゲートウェイに最大 10 本の VPN トンネルを確立し、他の仮想ネットワークまたはオンプレミス サイトに接続することが可能です。
* 仮想ネットワークのアドレス空間とオンプレミスのローカル ネットワーク サイトのアドレス空間とが重複しないようにする必要があります。 アドレス空間が重複していると、仮想ネットワークの作成または netcfg 構成ファイルのアップロードに失敗します。
* 一対の仮想ネットワーク間に冗長トンネルを確立することはできません。
* VNet のすべての VPN トンネル (P2S VPN を含む) は、VPN ゲートウェイ上の使用可能な帯域幅を共有し、Azure 内の同じ VPN ゲートウェイ アップタイム SLA を共有します。
* VNet 間のトラフィックは、Azure バックボーン経由で送信できます。

## <a name="next-steps"></a>次の手順
接続を確認します。 「[VPN Gateway 接続の確認](vpn-gateway-verify-connection-resource-manager.md)」を参照してください。
