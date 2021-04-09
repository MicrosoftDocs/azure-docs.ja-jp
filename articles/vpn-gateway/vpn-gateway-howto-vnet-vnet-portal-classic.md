---
title: 'VNet 間の接続の作成: クラシック: Azure portal'
description: PowerShell と Azure Portal を使って複数の Azure 仮想ネットワークを接続します。
services: vpn-gateway
titleSuffix: Azure VPN Gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 10/15/2020
ms.author: cherylmc
ms.openlocfilehash: 0d81e0474d898ffee7f128c0bcea61f077c3d758
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "92103222"
---
# <a name="configure-a-vnet-to-vnet-connection-classic"></a>VNet 間の接続の構成 (クラシック)

この記事では、仮想ネットワーク間で VPN ゲートウェイ接続を確立する方法について説明します。 仮想ネットワークが属しているリージョンやサブスクリプションは異なっていてもかまいません。

:::image type="content" source="./media/vpn-gateway-howto-vnet-vnet-portal-classic/v2vclassic.png" alt-text="従来の VNet 間アーキテクチャを示す図":::

[!INCLUDE [deployment models](../../includes/vpn-gateway-classic-deployment-model-include.md)]

この記事のこの手順は、クラシック デプロイ モデルと Azure Portal に適用されます。 また、この構成の作成には、次のリストから別のオプションを選択して、別のデプロイ ツールまたはデプロイ モデルを使用することもできます。

> [!div class="op_single_selector"]
> * [クラシック](vpn-gateway-howto-vnet-vnet-portal-classic.md)
> * [Resource Manager](vpn-gateway-howto-vnet-vnet-resource-manager-portal.md)
> * [異なるデプロイ モデルで VNet を接続する](vpn-gateway-connect-different-deployment-models-portal.md)
>
>

## <a name="about-vnet-to-vnet-connections"></a>VNet 間接続の概要

VPN Gateway を使用したクラシック デプロイ モデルでの仮想ネットワーク間 (VNet 間) の接続は、仮想ネットワークをオンプレミスのサイトの場所に接続することと似ています。 どちらの接続タイプでも、VPN ゲートウェイを使用して、IPsec/IKE を使った安全なトンネルが確保されます。

接続する VNet は、サブスクリプションやリージョンが異なっていてもかまいません。 マルチサイト構成と VNet 対 VNet 通信を組み合わせることができます。 そのため、クロスプレミス接続と仮想ネットワーク間接続とを組み合わせたネットワーク トポロジを確立することができます。

:::image type="content" source="./media/vpn-gateway-howto-vnet-vnet-portal-classic/aboutconnections.png" alt-text="接続を示す図":::

### <a name="why-connect-virtual-networks"></a><a name="why"></a>仮想ネットワークを接続する理由

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

## <a name="prerequisites"></a>前提条件

ほとんどの手順でポータルを使用しますが、VNet 間の接続を確立する場合は、PowerShell を使う必要があります。 Azure portal では共有キーを指定する方法がないため、ポータルを使用して接続を作成することはできません。 [!INCLUDE [vpn-gateway-classic-powershell](../../includes/vpn-gateway-powershell-classic-locally.md)]

## <a name="planning"></a><a name="planning"></a>計画

仮想ネットワークの構成に使用する範囲を決めることは重要です。 この構成では、VNet のアドレス範囲が、互いにまたは接続先のすべてのローカル ネットワークと重複していないことを確認する必要があります。

### <a name="vnets"></a><a name="vnet"></a>VNet

この演習では、次のサンプル値を使用します。

**TestVNet1 の値**

名前: TestVNet1<br>
アドレス空間: 10.11.0.0/16, 10.12.0.0/16 (省略可能)<br>
サブネット名: 既定<br>
サブネットのアドレス範囲: 10.11.0.0/24<br>
リソース グループ: ClassicRG<br>
場所: 米国東部<br>
GatewaySubnet: 10.11.1.0/27

**TestVNet4 の値**

名前: TestVNet4<br>
アドレス空間: 10.41.0.0/16, 10.42.0.0/16 (省略可能)<br>
サブネット名: 既定<br>
サブネットのアドレス範囲: 10.41.0.0/24<br>
リソース グループ: ClassicRG<br>
場所: 米国西部<br>
GatewaySubnet: 10.41.1.0/27

### <a name="connections"></a><a name="plan"></a>接続

VNet を接続する方法の例を次の表に示します。 範囲はあくまでも参考です。 仮想ネットワークの範囲をメモしておいてください。 この情報は後続の手順で必要になります。

この例では、TestVNet1 は "VNet4Local" という名前で作成するローカル ネットワーク サイトに接続します。 VNet4Local の設定には、TestVNet4 のアドレス プレフィックスが含まれています。
それぞれの VNet のローカル サイトが、もう一方の VNet になります。 この構成には、次の例の値が使用されます。

**例**

| Virtual Network | アドレス空間 | 場所 | ローカル ネットワーク サイトへの接続 |
|:--- |:--- |:--- |:--- |
| TestVNet1 |TestVNet1<br>(10.11.0.0/16)<br>(10.12.0.0/16) |米国東部 |SiteVNet4<br>(10.41.0.0/16)<br>(10.42.0.0/16) |
| TestVNet4 |TestVNet4<br>(10.41.0.0/16)<br>(10.42.0.0/16) |米国西部 |SiteVNet1<br>(10.11.0.0/16)<br>(10.12.0.0/16) |

## <a name="create-virtual-networks"></a><a name="vnetvalues"></a>仮想ネットワークを作成する

この手順では、TestVNet1 と TestVNet4 の 2 つのクラシック仮想ネットワークを作成します。 この記事を演習として使用している場合、[サンプル値](#vnet)を使用します。

**VNet を作成するときは、次の設定に注意してください。**

* **[仮想ネットワーク アドレス空間]** – [仮想ネットワーク アドレス空間] ページには、仮想ネットワークに使用するアドレス範囲を指定します。 これらが動的 IP アドレスとして、この仮想ネットワークにデプロイする VM や各種ロール インスタンスに割り当てられます。<br>他の VNet や、この VNet の接続先となるオンプレミスの場所のアドレス空間と重複するアドレス空間を選択することはできません。

* **[場所]** - 仮想ネットワークを作成するとき、仮想ネットワークを Azure の場所 (リージョン) に関連付けます。 たとえば、仮想ネットワークにデプロイされた VM を物理的に米国西部に配置する場合は、その場所を選択します。 仮想ネットワークを作成した後で、その仮想ネットワークに関連付けられた場所を変更することはできません。

**VNet を作成した後、次の設定を追加できます。**

* **[アドレス空間]** – この構成用に追加のアドレス空間は必要ありませんが、VNet を作成した後にさらにアドレス空間を追加することもできます。

* **[サブネット]** – この構成用に追加のサブネットは必要ありませんが、他のロール インスタンスとは別個のサブネットに VM を配置することができます。

* **[DNS サーバー]** – DNS サーバー名と IP アドレスを入力します。 この設定で、DNS サーバーは作成されません。 この設定では、この仮想ネットワークの名前解決に使用する DNS サーバーを指定することができます。

### <a name="to-create-a-classic-virtual-network"></a>クラシック仮想ネットワークを作成するには

[!INCLUDE [basic classic vnet](../../includes/vpn-gateway-vnet-classic.md)]

[!INCLUDE [basic classic DNS](../../includes/vpn-gateway-dns-classic.md)]

## <a name="configure-sites-and-gateways"></a><a name="localsite"></a>サイトとゲートウェイを構成する

Azure では、各ローカル ネットワーク サイトで指定されている設定を使用して、VNet 間でトラフィックをルーティングする方法を決定します。 各 VNet は、トラフィックのルーティング先とする対応するローカル ネットワークをポイントする必要があります。 各ローカル ネットワーク サイトの参照に使用する名前を指定します。 わかりやすい名前を使用することをお勧めします。

たとえば、TestVNet1 は "VNet4Local" という名前で作成するローカル ネットワーク サイトに接続します。 VNet4Local の設定には、TestVNet4 のアドレス プレフィックスが含まれています。

それぞれの VNet のローカル サイトが、もう一方の VNet になることに注意してください。

| Virtual Network | アドレス空間 | 場所 | ローカル ネットワーク サイトへの接続 |
|:--- |:--- |:--- |:--- |
| TestVNet1 |TestVNet1<br>(10.11.0.0/16)<br>(10.12.0.0/16) |米国東部 |SiteVNet4<br>(10.41.0.0/16)<br>(10.42.0.0/16) |
| TestVNet4 |TestVNet4<br>(10.41.0.0/16)<br>(10.42.0.0/16) |米国西部 |SiteVNet1<br>(10.11.0.0/16)<br>(10.12.0.0/16) |

### <a name="to-configure-a-site"></a><a name="site"></a>サイトを構成するには

通常、ローカル サイトとはオンプレミスの場所を指します。 ここには、接続の作成先となる VPN デバイスの IP アドレスのほか、VPN ゲートウェイ経由で VPN デバイスにルーティングされる IP アドレス範囲が含まれます。

1. ご自分の VNet 用のページで、 **[設定]** の下の **[サイト間接続]** を選択します。
1. [サイト間接続] ページで、 **[+ 追加]** を選択します。
1. **[VPN 接続とゲートウェイの構成]** ページで、 **[接続の種類]** には **[サイト間接続]** が選択されたままにします。

   * **[VPN ゲートウェイの IP アドレス]:** これは、オンプレミス ネットワークの VPN デバイスのパブリック IP アドレスです。 この演習では、他のサイトの VPN ゲートウェイの IP アドレスをまだ入手していないため、ダミー アドレスを入力できます。 たとえば、5.4.3.2 などです。 その後、他の VNet のゲートウェイを構成したら、この値を調整できます。

   * **[クライアント アドレス空間]:** このゲートウェイ経由で他の VNet にルーティングする IP アドレス範囲をリストします。 複数のアドレス領域の範囲を追加することができます。 ここで指定する範囲は、仮想ネットワークの接続先となる他のネットワークの範囲、または仮想ネットワーク自体のアドレス範囲と重複しないようにしてください。
1. ページの下部にある [確認と作成] を選択しないでください。 代わりに、 **[次へ: ゲートウェイ>]** を選択します。

### <a name="to-configure-a-virtual-network-gateway"></a><a name="sku"></a>仮想ネットワーク ゲートウェイを構成するには

1. **[ゲートウェイ]** ページで、次の値を選択します。

   * **Size:** これは、仮想ネットワーク ゲートウェイの作成に使用するゲートウェイ SKU です。 クラシック VPN ゲートウェイでは、古い (レガシ) ゲートウェイ SKU が使用されます。 レガシ ゲートウェイ SKU の詳細については、[仮想ネットワーク ゲートウェイ SKU (古い SKU) の利用](vpn-gateway-about-skus-legacy.md)に関するページを参照してください。 この演習では、 **[Standard]** を選択できます。

   * **ルーティングの種類:** お使いのゲートウェイのルーティングの種類を選択します。 これは VPN の種類とも呼ばれます。 ゲートウェイの種類は切り替えることができないため、適切な種類を選択することが重要です。 VPN デバイスは、選択するルーティングの種類に適合している必要があります。 ルーティングの種類の詳細については、「[VPN の種類](vpn-gateway-about-vpn-gateway-settings.md#vpntype)」を参照してください。 "RouteBased" と "PolicyBased" という VPN の種類の項目がありますが、 "動的" は "RouteBased"、"静的" は "PolicyBased" に対応しています。 この構成では、 **[動的]** を選択します。

   * **ゲートウェイ サブネット:** 指定したゲートウェイ サブネットのサイズは、作成する VPN ゲートウェイの構成によって異なります。 /29 のような小さいゲートウェイ サブネットを作成できますが、/27 または /28 を使用することをお勧めします。 これにより、より多くのアドレスを含む大きなサブネットが作成されます。 大規模なゲートウェイ サブネットを使用すると、将来の構成に対応するのに十分な IP アドレスを確保できます。

1. ページの下部にある **[確認と作成]** を選択して、設定を検証します。 **[作成]** を選択してデプロイします。 選択した SKU によっては、仮想ネットワーク ゲートウェイを作成するまでに最大で 45 分かかる可能性があります。
1. このゲートウェイが作成されている間、次の手順に進むことができます。

### <a name="configure-testvnet4-settings"></a>TestVNet4 設定を構成する

必要に応じて値を置き換えて、[サイトとゲートウェイを作成する](#localsite)手順を繰り返し、TestVNet4 を構成します。 これを演習として実行している場合、[サンプル値](#planning)を使用します。

## <a name="update-local-sites"></a><a name="updatelocal"></a>ローカル サイトの更新

両方の VNet に対して仮想ネットワーク ゲートウェイを作成したら、**VPN ゲートウェイの IP アドレス** についてローカル サイトのプロパティを調整する必要があります。

|VNet の名前|接続されているサイト|ゲートウェイ IP アドレス|
|:--- |:--- |:--- |
|TestVNet1|VNet4Local|TestVNet4 の VPN ゲートウェイ IP アドレス|
|TestVNet4|VNet1Local|TestVNet1 の VPN ゲートウェイ IP アドレス|

### <a name="part-1---get-the-virtual-network-gateway-public-ip-address"></a>パート 1 - 仮想ネットワーク ゲートウェイのパブリック IP アドレスを取得する

1. **[リソース グループ]** に移動し、仮想ネットワークを選択して、VNet に移動します。
1. 仮想ネットワークのページの右側にある **[Essentials]** ペインで、 **[ゲートウェイ IP アドレス]** を見つけてクリップボードにコピーします。

### <a name="part-2---modify-the-local-site-properties"></a>パート 2 - ローカル サイトのプロパティを変更する

1. [サイト間接続] で、接続を選択します。 たとえば、SiteVNet4 です。
1. サイト間接続の **[プロパティ]** ページで、 **[ローカル サイトの編集]** を選択します。
1. **[VPN ゲートウェイの IP アドレス]** フィールドに、前のセクションでコピーした VPN ゲートウェイの IP アドレスを貼り付けます。
1. **[OK]** を選択します。
1. システムでフィールドが更新されます。 この方法を使用して、このサイトにルーティングする IP アドレスを追加することもできます。

### <a name="part-3---repeat-steps-for-the-other-vnet"></a>パート 3 - その他の VNet の手順を繰り返す

TestVNet4 について、手順を繰り返します。

## <a name="retrieve-configuration-values"></a><a name="getvalues"></a>構成値を取得する

[!INCLUDE [retrieve values](../../includes/vpn-gateway-values-classic.md)]

## <a name="create-connections"></a><a name="createconnections"></a>接続を作成する

以上の手順がすべて完了したら、IPsec/IKE 事前共有キーを設定して接続を作成できます。 この一連の手順では、PowerShell を使用します。 クラシック デプロイ モデルの VNet 間接続を、Azure portal で構成することはできません。これは、ポータルでは共有キーを指定することができないためです。

次の例では、共有キーがまったく同じであることに注目してください。 共有キーは常に一致する必要があります。 これらの例の値を、VNet やローカル ネットワーク サイトに使用する正確な名前で置き換えます。

1. TestVNet1 から TestVNet4 への接続を作成します。 必ず値を変更してください。

   ```powershell
   Set-AzureVNetGatewayKey -VNetName 'Group ClassicRG TestVNet1' `
   -LocalNetworkSiteName 'value for _VNet4Local' -SharedKey A1b2C3D4
   ```
2. TestVNet4 から TestVNet1 への接続を作成します。

   ```powershell
   Set-AzureVNetGatewayKey -VNetName 'Group ClassicRG TestVNet4' `
   -LocalNetworkSiteName 'value for _VNet1Local' -SharedKey A1b2C3D4
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

## <a name="faq-and-considerations"></a><a name="faq"></a>FAQ と考慮事項

これらの考慮事項は、クラシック仮想ネットワークとクラシック仮想ネットワーク ゲートウェイに適用されます。

* 仮想ネットワークが属しているサブスクリプションは異なっていてもかまいません。
* 仮想ネットワークが属している Azure リージョン (場所) は異なっていてもかまいません。
* クラウド サービスや負荷分散エンドポイントは、仮想ネットワークが相互に接続されていても、仮想ネットワークの境界を越えることはできません。
* 複数の仮想ネットワークを接続する場合は、VPN デバイスは必要ありません。
* VNet 間接続でサポートされるのは、Azure Virtual Network の接続です。 仮想ネットワークにデプロイされていない仮想マシンやクラウド サービスを接続することはできません。
* VNet 対 VNet 通信には、動的ルーティング ゲートウェイが必要です。 Azure 静的ルーティング ゲートウェイはサポートされません。
* 仮想ネットワーク接続は、マルチサイト VPN と同時に使用することができます。 1 つの仮想ネットワーク VPN ゲートウェイに最大 10 本の VPN トンネルを確立し、他の仮想ネットワークまたはオンプレミス サイトに接続することが可能です。
* 仮想ネットワークのアドレス空間とオンプレミスのローカル ネットワーク サイトのアドレス空間とが重複しないようにする必要があります。 アドレス空間が重複していると、仮想ネットワークの作成または netcfg 構成ファイルのアップロードに失敗します。
* 一対の仮想ネットワーク間に冗長トンネルを確立することはできません。
* VNet のすべての VPN トンネル (P2S VPN を含む) は、VPN ゲートウェイ上の使用可能な帯域幅を共有し、Azure 内の同じ VPN ゲートウェイ アップタイム SLA を共有します。
* VNet 間のトラフィックは、Azure バックボーン経由で送信できます。

## <a name="next-steps"></a>次のステップ

接続を確認します。 「[VPN Gateway 接続の確認](vpn-gateway-verify-connection-resource-manager.md)」を参照してください。
