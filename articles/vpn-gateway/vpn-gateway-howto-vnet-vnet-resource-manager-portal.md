---
title: "Azure 仮想ネットワークを別の VNet に接続する: ポータル | Microsoft Docs"
description: "Resource Manager と Azure Portal を使用して VNet 間の VPN ゲートウェイ接続を作成します。"
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: a7015cfc-764b-46a1-bfac-043d30a275df
ms.service: vpn-gateway
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/27/2017
ms.author: cherylmc
translationtype: Human Translation
ms.sourcegitcommit: 197ebd6e37066cb4463d540284ec3f3b074d95e1
ms.openlocfilehash: c80ddbaf8c2c84735564e514ddaf4308c4aff303
ms.lasthandoff: 03/31/2017


---
# <a name="configure-a-vnet-to-vnet-connection-using-the-azure-portal"></a>Azure Portal を使用した VNet 間接続の構成
> [!div class="op_single_selector"]
> * [Resource Manager - Azure Portal](vpn-gateway-howto-vnet-vnet-resource-manager-portal.md)
> * [Resource Manager - PowerShell](vpn-gateway-vnet-vnet-rm-ps.md)
> * [クラシック - Azure Portal](vpn-gateway-howto-vnet-vnet-portal-classic.md)
> * [クラシック - クラシック ポータル](virtual-networks-configure-vnet-to-vnet-connection.md)
> 
> 

この記事では、VPN Gateway と Azure Portal を使用して Resource Manager デプロイメント モデルで VNet 間の接続を作成する手順について説明します。

Azure Portal を使用して仮想ネットワーク間を接続する場合、各 VNet は同じサブスクリプションに属していることが必要です。 各仮想ネットワークが異なるサブスクリプションに属している場合でも、[PowerShell](vpn-gateway-vnet-vnet-rm-ps.md) を使用した手順で接続することが可能です。

![v2v diagram](./media/vpn-gateway-howto-vnet-vnet-resource-manager-portal/v2vrmps.png)

### <a name="deployment-models-and-methods-for-vnet-to-vnet-connections"></a>VNet 間接続で使用できるデプロイメント モデルとデプロイ方法
[!INCLUDE [deployment models](../../includes/vpn-gateway-deployment-models-include.md)]

以下の表は、VNet 間の構成に関して現在利用できるデプロイメント モデルとデプロイ方法を示しています。 構成手順を説明した記事が利用できるようになったら、表から直接リンクできるようにします。

[!INCLUDE [vpn-gateway-table-vnet-vnet](../../includes/vpn-gateway-table-vnet-to-vnet-include.md)]

**VNet ピアリング**

[!INCLUDE [vpn-gateway-vnetpeeringlink](../../includes/vpn-gateway-vnetpeeringlink-include.md)]

## <a name="about-vnet-to-vnet-connections"></a>VNet 間接続の概要
仮想ネットワークどうし (VNet 間) の接続は、VNet をオンプレミス サイトの場所に接続することと似ています。 どちらの接続タイプでも、Azure VPN ゲートウェイを使用し、IPsec/IKE を使った安全なトンネルが確保されます。 接続する VNet は、リージョンやサブスクリプションが異なっていてもかまいません。

マルチサイト構成と VNet 間通信を組み合わせることもできます。 そのため、クロスプレミス接続と仮想ネットワーク間接続を組み合わせたネットワーク トポロジを確立することができます (下図参照)。

![接続について](./media/vpn-gateway-howto-vnet-vnet-resource-manager-portal/aboutconnections.png "接続について")

### <a name="why-connect-virtual-networks"></a>仮想ネットワークを接続する理由
仮想ネットワークを接続するのは次のような場合です。

* **リージョン間の geo 冗長性および geo プレゼンス**
  
  * インターネット接続エンドポイントを介さず、安全な接続を使って独自の geo レプリケーションや geo 同期をセットアップすることができます。
  * Azure Traffic Manager および Load Balancer を使用し、複数の Azure リージョンをまたぐ geo 冗長性を備えた、可用性に優れたワークロードをセットアップすることができます。 たとえば、複数の Azure リージョンにまたがる SQL AlwaysOn 可用性グループをセットアップすることができます。
* **特定のリージョン内で分離または管理境界を備えた多層アプリケーション**
  
  * 同じリージョン内で、分離または管理要件に基づいて相互に接続された複数の仮想ネットワークを利用し、多層アプリケーションをセットアップすることができます。

VNet 間接続の詳細については、この記事の最後にある「[VNet 間の考慮事項](#faq)」を参照してください。

### <a name="values"></a>設定例
以下の手順を練習として使用する場合は、次のサンプル構成値を使用してください。 ここでは、例として示すために VNet ごとに複数のアドレス空間を使用しますが、 VNet 間構成に複数のアドレス空間は必要ありません。

**TestVNet1 の値:**

* VNet の名前: TestVNet1
* アドレス空間: 10.11.0.0/16
  * サブネット名: FrontEnd
  * サブネットのアドレス範囲: 10.11.0.0/24
* リソース グループ: TestRG1
* 場所: 米国東部
* アドレス空間: 10.12.0.0/16
  * サブネット名: BackEnd
  * サブネットのアドレス範囲: 10.12.0.0/24
* ゲートウェイ サブネット名: GatewaySubnet (ポータルではこの名前が自動入力されます)
  * ゲートウェイ サブネットのアドレス範囲: 10.11.255.0/27
* DNS サーバー: ご利用の DNS サーバーの IP アドレスを使用
* 仮想ネットワーク ゲートウェイ名: TestVNet1GW
* ゲートウェイの種類: VPN
* VPN の種類: ルート ベース
* SKU: 使用するゲートウェイの SKU を選択
* パブリック IP アドレス名: TestVNet1GWIP
* 接続値:
  * 名前: TestVNet1toTestVNet4
  * 共有キー: 独自の共有キーを作成できます。 この例では、abc123 を使用します。 ここで重要なのは、VNet 間の接続を作成するときに、この値を一致させることです。

**TestVNet4 の値:**

* VNet の名前: TestVNet4
* アドレス空間: 10.41.0.0/16
  * サブネット名: FrontEnd
  * サブネットのアドレス範囲: 10.41.0.0/24
* リソース グループ: TestRG1
* 場所: 米国西部
* アドレス空間: 10.42.0.0/16
  * サブネット名: BackEnd
  * サブネットのアドレス範囲: 10.42.0.0/24
* ゲートウェイ サブネット名: GatewaySubnet (ポータルではこの名前が自動入力されます)
  * ゲートウェイ サブネットのアドレス範囲: 10.41.255.0/27
* DNS サーバー: ご利用の DNS サーバーの IP アドレスを使用
* 仮想ネットワーク ゲートウェイ名: TestVNet4GW
* ゲートウェイの種類: VPN
* VPN の種類: ルート ベース
* SKU: 使用するゲートウェイの SKU を選択
* パブリック IP アドレス名: TestVNet4GWIP
* 接続値:
  * 名前: TestVNet4toTestVNet1
  * 共有キー: 独自の共有キーを作成できます。 この例では、abc123 を使用します。 ここで重要なのは、VNet 間の接続を作成するときに、この値を一致させることです。

## <a name="CreatVNet"></a>1.TestVNet1 の作成と構成
既に VNet がある場合は、設定が VPN ゲートウェイの設計に適合していることを確認します。 特に、他のネットワークと重複している可能性のあるサブネットに注意してください。 サブネットの重複があると、接続が適切に動作しません。 VNet が正しい設定で構成されていたら、「 [DNS サーバーの指定](#dns) 」セクションの手順に進んでください。

### <a name="to-create-a-virtual-network"></a>仮想ネットワークを作成するには
[!INCLUDE [vpn-gateway-basic-vnet-rm-portal](../../includes/vpn-gateway-basic-vnet-rm-portal-include.md)]

## <a name="subnets"></a>2.アドレス空間の追加とサブネットの作成
VNet が作成されたら、アドレス空間をさらに追加してサブネットを作成することができます。

[!INCLUDE [vpn-gateway-additional-address-space](../../includes/vpn-gateway-additional-address-space-include.md)]

## <a name="gatewaysubnet"></a>3.ゲートウェイ サブネットの作成
仮想ネットワークをゲートウェイに接続する前に、まず、接続先の仮想ネットワークのゲートウェイ サブネットを作成する必要があります。 将来的な構成要件も見越して十分な IP アドレスを確保するために、可能であれば、/28 または /27 の CIDR ブロックを使用してゲートウェイ サブネットを作成することをお勧めします。

練習としてこの構成を作成する場合は、ゲートウェイ サブネットの作成時に、上記の[設定例](#values)を参照してください。

[!INCLUDE [vpn-gateway-no-nsg](../../includes/vpn-gateway-no-nsg-include.md)]

### <a name="to-create-a-gateway-subnet"></a>ゲートウェイ サブネットを作成するには
[!INCLUDE [vpn-gateway-add-gwsubnet-rm-portal](../../includes/vpn-gateway-add-gwsubnet-rm-portal-include.md)]

## <a name="DNSServer"></a>4.DNS サーバーの指定 (省略可能)
VNet 間接続に DNS は不要です。 ただし仮想ネットワークにデプロイされたリソースで名前解決を使用する場合は、DNS サーバーを指定する必要があります。 この設定では、この仮想ネットワークの名前解決に使用する DNS サーバーを指定することができます。 この設定で、DNS サーバーは作成されません。

[!INCLUDE [vpn-gateway-add-dns-rm-portal](../../includes/vpn-gateway-add-dns-rm-portal-include.md)]

## <a name="VNetGateway"></a>5.仮想ネットワーク ゲートウェイの作成
この手順では、VNet の仮想ネットワーク ゲートウェイを作成します。 完了までに最大で 45 分かかることがあります。 練習としてこの構成を作成する場合は、[設定例](#values)を参照してください。

### <a name="to-create-a-virtual-network-gateway"></a>仮想ネットワーク ゲートウェイを作成するには
[!INCLUDE [vpn-gateway-add-gw-rm-portal](../../includes/vpn-gateway-add-gw-rm-portal-include.md)]

## <a name="CreateTestVNet4"></a>6.TestVNet4 の作成と構成
TestVNet1 を構成したら、前の手順を繰り返して TestVNet4 を作成します。使用する値は TestVNet4 の値に置き換えてください。 TestVNet1 の仮想ネットワーク ゲートウェイの作成が完了するまで待つ必要はありません。すぐに TestVNet4 を構成できます。 独自の値を使用する場合は、接続先とするどの VNet ともアドレス空間が重複しないようにしてください。

## <a name="TestVNet1Connection"></a>7.TestVNet1 の接続の構成
TestVNet1 と TestVNet4 の仮想ネットワーク ゲートウェイの作成が両方とも完了したら、仮想ネットワーク ゲートウェイの接続を作成できます。 このセクションでは、VNet1 から VNet4 への接続を作成します。

1. **[すべてのリソース]** から VNet の仮想ネットワーク ゲートウェイに移動します。 たとえば、**[TestVNet1GW]** に移動します。 **[TestVNet1GW]** をクリックして仮想ネットワーク ゲートウェイのブレードを開きます。
   
    ![接続ブレード](./media/vpn-gateway-howto-vnet-vnet-resource-manager-portal/settings_connection.png "接続ブレード")
2. **[+追加]** をクリックして **[接続の追加]** ブレードを開きます。
3. **[接続の追加]** ブレードで、名前フィールドに接続の名前を入力します。 たとえば、「**TestVNet1toTestVNet4**」と入力します。
   
    ![接続名](./media/vpn-gateway-howto-vnet-vnet-resource-manager-portal/v1tov4.png "接続名")
4. **[接続の種類]** では、 ドロップダウン リストから **[VNet 対 VNet]** を選択します。
5. この接続は指定した仮想ネットワーク ゲートウェイから作成しているため、**[最初の仮想ネットワーク ゲートウェイ]** フィールドの値は自動的に入力されます。
6. **[2 番目の仮想ネットワーク ゲートウェイ]** フィールドでは、接続先として作成する VNet の仮想ネットワーク ゲートウェイを設定します。 **[別の仮想ネットワーク ゲートウェイを選択する]** をクリックして、**[仮想ネットワーク ゲートウェイの選択]** ブレードを開きます。
   
    ![接続の追加](./media/vpn-gateway-howto-vnet-vnet-resource-manager-portal/add_connection.png "接続の追加")
7. このブレードに一覧表示されている仮想ネットワーク ゲートウェイを確認します。 自分のサブスクリプションに属している仮想ネットワーク ゲートウェイのみが表示されていることがわかります。 自分のサブスクリプションに属していない仮想ネットワーク ゲートウェイに接続する場合は、[PowerShell に関する記事](vpn-gateway-vnet-vnet-rm-ps.md)を参照してください。 
8. 接続先とする仮想ネットワーク ゲートウェイをクリックします。
9. **[共有キー]** フィールドには、接続に使用する共有キーを入力します。 このキーは、自分で生成または作成できます。 サイト間接続では、オンプレミス デバイスと仮想ネットワーク ゲートウェイの接続にまったく同じキーを使用します。 Vnet 間接続の場合も、VPN デバイスではなく、別の仮想ネットワーク ゲートウェイに接続するという点を除けば、概念は同じです。
   
    ![共有キー](./media/vpn-gateway-howto-vnet-vnet-resource-manager-portal/sharedkey.png "共有キー")
10. ブレード下部の **[OK]** をクリックして、変更を保存します。

## <a name="TestVNet4Connection"></a>8.TestVNet4 の接続の構成
次は、TestVNet4 から TestVNet1 への接続を作成します。 TestVNet1 から TestVNet4 への接続を作成したときと同じ方法で作成できます。 必ず同一の共有キーを使用してください。

## <a name="VerifyConnection"></a>9.接続を確認する
接続を確認します。 仮想ネットワーク ゲートウェイごとに、次の操作を行います。

1. 仮想ネットワーク ゲートウェイのブレードを見つけます。 たとえば、**TestVNet4GW** のブレードとします。 
2. 仮想ネットワーク ゲートウェイのブレードで、**[接続]** をクリックして仮想ネットワーク ゲートウェイの接続のブレードを表示します。

接続の状態を確認します。 接続が作成されると、[状態] を示す値として **[成功]** と **[接続済み]** が表示されます。

![成功](./media/vpn-gateway-howto-vnet-vnet-resource-manager-portal/connected.png "成功")

各接続をダブルクリックすると、その接続についての詳細情報を個別に表示できます。

![要点](./media/vpn-gateway-howto-vnet-vnet-resource-manager-portal/essentials.png "要点")

## <a name="faq"></a>VNet 間の考慮事項
VNet 間接続に関するその他の情報についてよく寄せられる質問の詳細を示します。

[!INCLUDE [vpn-gateway-vnet-vnet-faq](../../includes/vpn-gateway-vnet-vnet-faq-include.md)]

## <a name="next-steps"></a>次のステップ
接続が完成したら、仮想ネットワークに仮想マシンを追加することができます。 詳細については、 [Virtual Machines のドキュメント](https://docs.microsoft.com/azure/#pivot=services&panel=Compute) を参照してください。

