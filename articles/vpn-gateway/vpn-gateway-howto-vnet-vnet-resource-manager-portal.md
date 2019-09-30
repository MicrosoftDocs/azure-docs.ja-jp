---
title: Azure ポータルを使用して VNet 対 VNet VPN ゲートウェイ接続を構成する | Microsoft Docs
description: Resource Manager と Azure Portal を使用して VNet 間の VPN ゲートウェイ接続を作成します。
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 09/24/2019
ms.author: cherylmc
ms.openlocfilehash: 7ad83327d5b85784f523a5931f277cd00009e0ed
ms.sourcegitcommit: 3f22ae300425fb30be47992c7e46f0abc2e68478
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/25/2019
ms.locfileid: "71266464"
---
# <a name="configure-a-vnet-to-vnet-vpn-gateway-connection-by-using-the-azure-portal"></a>Azure ポータルを使用して VNet 間 VPN ゲートウェイ接続を構成する

この記事は、VNet 間という接続の種類を使用して、仮想ネットワーク (VNets) を接続する際に役立ちます。 仮想ネットワークが属しているリージョンやサブスクリプションは異なっていてもかまいません。 異なるサブスクリプションの VNet を接続する場合、サブスクリプションが同じ Active Directory テナントに関連付けられている必要はありません。 

![v2v diagram](./media/vpn-gateway-howto-vnet-vnet-resource-manager-portal/v2vrmps.png)

この記事の手順は、Azure Resource Manager デプロイ モデルに適用されます。また、この手順では Azure ポータルを使用します。 以下の記事で説明されているオプションを使用して、別のデプロイ ツールやモデルでこの構成を作成することができます。

> [!div class="op_single_selector"]
> * [Azure Portal](vpn-gateway-howto-vnet-vnet-resource-manager-portal.md)
> * [PowerShell](vpn-gateway-vnet-vnet-rm-ps.md)
> * [Azure CLI](vpn-gateway-howto-vnet-vnet-cli.md)
> * [Azure Portal (クラシック)](vpn-gateway-howto-vnet-vnet-portal-classic.md)
> * [異なるデプロイメント モデルの接続 - Azure Portal](vpn-gateway-connect-different-deployment-models-portal.md)
> * [異なるデプロイメント モデルの接続 - PowerShell](vpn-gateway-connect-different-deployment-models-powershell.md)
>
>

## <a name="about-connecting-vnets"></a>VNet の接続について

以下のセクションでは、仮想ネットワークを接続するさまざまな方法について説明します。

### <a name="vnet-to-vnet"></a>VNet 間

VNet 間接続の構成は、VNet を接続するためのシンプルな方法です。 VNet 間接続 (VNet2VNet) の種類を使用して仮想ネットワークどうしを接続する場合、それはオンプレミスの場所へのサイト間 IPsec 接続を作成することに似ています。 どちらの接続の種類でも、VPN ゲートウェイを使用して IPsec/IKE を使った安全なトンネルが提供され、通信時には同じように機能します。 しかし、ローカル ネットワーク ゲートウェイの構成方法は異なります。 

VNet 間接続を作成するときに、ローカル ネットワーク ゲートウェイのアドレス空間は自動的に作成され、設定されます。 一方の VNet のアドレス空間を更新すると、更新されたアドレス空間へのルーティングがもう一方の VNet で自動的に行われます。 VNet 間接続の作成は、通常、サイト間接続の場合よりも高速で簡単です。

### <a name="site-to-site-ipsec"></a>サイト間 (IPsec)

複雑なネットワーク構成で作業している場合は、代わりに[サイト間接続](vpn-gateway-howto-site-to-site-resource-manager-portal.md)を使用して VNet を接続する方がよい場合もあります。 サイト間 IPsec の手順に従う場合は、ローカル ネットワーク ゲートウェイを手動で作成して構成します。 各 VNet のローカル ネットワーク ゲートウェイは、他方の VNet をローカル サイトとして扱います。 これらの手順では、トラフィックをルーティングするための追加のアドレス空間をローカル ネットワーク ゲートウェイに指定することができます。 VNet のアドレス空間が変更されたら、対応するローカル ネットワーク ゲートウェイを手動で更新する必要があります。

### <a name="vnet-peering"></a>VNET ピアリング

VNet ピアリングを使用して VNet を接続することもできます。 VNet ピアリングは VPN ゲートウェイを使用せず、さまざまな制約があります。 さらに、[VNET ピアリングの料金](https://azure.microsoft.com/pricing/details/virtual-network)は、[VNet 間 VPN Gateway の料金](https://azure.microsoft.com/pricing/details/vpn-gateway)と計算方法が異なります。 詳細については、「 [VNet ピアリング](../virtual-network/virtual-network-peering-overview.md)」を参照してください。

## <a name="why-create-a-vnet-to-vnet-connection"></a>VNet 間接続を作成する理由

VNet 間接続を使用する仮想ネットワークの接続が望ましいのは、次のような場合です。

### <a name="cross-region-geo-redundancy-and-geo-presence"></a>リージョン間の geo 冗長性および geo プレゼンス

  * インターネット接続エンドポイントを介さず、安全な接続を使って独自の geo レプリケーションや同期を設定することができます。
  * Azure Traffic Manager および Azure Load Balancer を使用し、複数の Azure リージョンをまたぐ geo 冗長性を備えた、可用性に優れたワークロードを設定することができます。 たとえば、複数の Azure リージョン間で SQL Server Always On 可用性グループを設定できます。

### <a name="regional-multi-tier-applications-with-isolation-or-administrative-boundaries"></a>特定のリージョン内で分離または管理境界を備えた多層アプリケーション

  * 同じリージョン内で、分離または管理要件に基づいて相互に接続された複数の仮想ネットワークを利用し、多層アプリケーションを設定することができます。

マルチサイト構成と VNet 間通信を組み合わせることができます。 これらの構成では、クロスプレミス接続と仮想ネットワーク間接続を組み合わせたネットワーク トポロジを確立することができます (下図参照)。

![接続について](./media/vpn-gateway-howto-vnet-vnet-resource-manager-portal/aboutconnections.png "接続について")

この記事では、VNet 間という接続の種類を使用して、VNet を接続する方法を示します。 演習として以下の手順に従う場合は、次の例の設定値を使用できます。 この例では、仮想ネットワークは同じサブスクリプション内にありながら、異なるリソース グループに含まれます。 対象となる VNet がそれぞれ異なるサブスクリプションに存在する場合、ポータルで接続を作成することはできません。 代わりに、[PowerShell](vpn-gateway-vnet-vnet-rm-ps.md) または [CLI](vpn-gateway-howto-vnet-vnet-cli.md) を使用します。 VNet 間接続の詳細については、「[VNet 間接続に関してよく寄せられる質問](#vnet-to-vnet-faq)」を参照してください。

### <a name="example-settings"></a>設定例

**TestVNet1 の値:**

- **仮想ネットワークの設定**
    - **名前**:「*TestVNet1*」と入力します。
    - **アドレス空間**:「*10.11.0.0/16*」と入力します。
    - **サブスクリプション**:使用するサブスクリプションを選択します。
    - **リソース グループ**:「*TestRG1*」と入力します。
    - **場所**: **[米国東部]** を選択します。
    - **サブネット**
        - **名前**:「*FrontEnd*」と入力します。
        - **アドレス範囲**:「*10.11.0.0/24*」と入力します。
    - **ゲートウェイ サブネット**:
        - **名前**:*GatewaySubnet* が自動入力されます。
        - **アドレス範囲**:「*10.11.255.0/27*」と入力します。

- **仮想ネットワーク ゲートウェイの設定** 
    - **名前**:「*TestVNet1GW*」と入力します。
    - **ゲートウェイの種類**: **[VPN]** を選択します。
    - **VPN の種類**: **[ルート ベース]** を選択します。
    - **SKU**:使用するゲートウェイの SKU を選択します。
    - **パブリック IP アドレス名**:「*TestVNet1GWIP*」と入力します
    - **Connection** 
       - **名前**:「*TestVNet1toTestVNet4*」と入力します。
       - **共有キー**:「*abc123*」と入力します。 独自の共有キーを作成できます。 VNet 間の接続を作成する際に、値が一致する必要があります。

**TestVNet4 の値:**

- **仮想ネットワークの設定**
   - **名前**:「*TestVNet4*」と入力します。
   - **アドレス空間**:「*10.41.0.0/16*」と入力します。
   - **サブスクリプション**:使用するサブスクリプションを選択します。
   - **リソース グループ**:「*TestRG4*」と入力します。
   - **場所**: **[米国西部]** を選択します。
   - **サブネット** 
      - **名前**:「*FrontEnd*」と入力します。
      - **アドレス範囲**:「*10.41.0.0/24*」と入力します。
   - **GatewaySubnet** 
      - **名前**:*GatewaySubnet* が自動入力されます。
      - **アドレス範囲**:「*10.41.255.0/27*」と入力します。

- **仮想ネットワーク ゲートウェイの設定** 
    - **名前**:「*TestVNet4GW*」と入力します。
    - **ゲートウェイの種類**: **[VPN]** を選択します。
    - **VPN の種類**: **[ルート ベース]** を選択します。
    - **SKU**:使用するゲートウェイの SKU を選択します。
    - **パブリック IP アドレス名**:「*TestVNet4GWIP*」と入力します。
    - **Connection** 
       - **名前**:「*TestVNet4toTestVNet1*」と入力します。
       - **共有キー**:「*abc123*」と入力します。 独自の共有キーを作成できます。 VNet 間の接続を作成する際に、値が一致する必要があります。

## <a name="create-and-configure-testvnet1"></a>TestVNet1 の作成と構成
既に VNet がある場合は、設定が VPN ゲートウェイの設計に適合していることを確認します。 特に、他のネットワークと重複している可能性のあるサブネットに注意してください。 サブネットの重複があると、接続が適切に動作しません。

### <a name="to-create-a-virtual-network"></a>仮想ネットワークを作成するには
[!INCLUDE [vpn-gateway-basic-vnet-rm-portal](../../includes/vpn-gateway-basic-vnet-rm-portal-include.md)]

## <a name="add-additional-address-space-and-create-subnets"></a>アドレス空間の追加とサブネットの作成
VNet が作成されたら、アドレス空間をさらに追加してサブネットを作成することができます。

[!INCLUDE [vpn-gateway-additional-address-space](../../includes/vpn-gateway-additional-address-space-include.md)]

## <a name="create-a-virtual-network-gateway"></a>仮想ネットワーク ゲートウェイの作成
この手順では、VNet の仮想ネットワーク ゲートウェイを作成します。 選択したゲートウェイ SKU によっては、ゲートウェイの作成に 45 分以上かかる場合も少なくありません。 演習としてこの構成を作成する場合は、「[設定例](#example-settings)」を参照してください。

[!INCLUDE [About gateway subnets](../../includes/vpn-gateway-about-gwsubnet-portal-include.md)]

### <a name="to-create-a-virtual-network-gateway"></a>仮想ネットワーク ゲートウェイを作成するには
[!INCLUDE [vpn-gateway-add-gw-rm-portal](../../includes/vpn-gateway-add-gw-rm-portal-include.md)]

[!INCLUDE [vpn-gateway-no-nsg](../../includes/vpn-gateway-no-nsg-include.md)]

## <a name="create-and-configure-testvnet4"></a>TestVNet4 の作成と構成
TestVNet1 を構成した後、前の手順を繰り返し、値を TestVNet4 の値に置き換えて、TestVNet4 を作成します。 TestVNet4 を構成する前に、TestVNet1 用の仮想ネットワーク ゲートウェイの作成が完了するまで待つ必要はありません。 独自の値を使用する場合は、接続先とするどの VNet ともアドレス空間が重複しないようにしてください。

## <a name="configure-the-testvnet1-gateway-connection"></a>TestVNet1 ゲートウェイ接続の構成
TestVNet1 と TestVNet4 の仮想ネットワーク ゲートウェイの作成が両方とも完了したら、仮想ネットワーク ゲートウェイの接続を作成できます。 このセクションでは、VNet1 から VNet4 への接続を作成します。 これらの手順は、同じサブスクリプションに存在する VNet でのみ使用できます。 VNet が異なるサブスクリプションにある場合は、[PowerShell](vpn-gateway-vnet-vnet-rm-ps.md) を使用して接続する必要があります。 しかし、VNet が同じサブスクリプション内の異なるリソース グループにある場合は、ポータルを使用して接続できます。

1. Azure ポータルで、 **[すべてのリソース]** を選択し、検索ボックスに*仮想ネットワーク ゲートウェイ* を入力してから、VNet の仮想ネットワーク ゲートウェイに移動します。 たとえば、 **[TestVNet1GW]** に移動します。 それを選択して、**仮想ネットワーク ゲートウェイ**のページを開きます。

   ![[接続] ページ](./media/vpn-gateway-howto-vnet-vnet-resource-manager-portal/1to4connect2.png "[接続] ページ")
2. **[設定]** で **[接続]** を選択し、 **[追加]** を選んで **[接続の追加]** ページを開きます。

   ![接続の追加](./media/vpn-gateway-howto-vnet-vnet-resource-manager-portal/add.png "接続の追加")
3. **[接続の追加]** ページで、接続の値を入力します。

   - **名前**:接続の名前を入力します。 たとえば、「*TestVNet1toTestVNet4*」と入力します。

   - **接続の種類**:ドロップダウンから **[VNet 間]** を選択します。

   - **最初の仮想ネットワーク ゲートウェイ**:この接続は指定した仮想ネットワーク ゲートウェイから作成しているため、このフィールドの値は自動的に入力されます。

   - **2 番目の仮想ネットワーク ゲートウェイ**:このフィールドでは、接続先として作成する VNet の仮想ネットワーク ゲートウェイを設定します。 **[別の仮想ネットワーク ゲートウェイを選択する]** を選び、 **[仮想ネットワーク ゲートウェイの選択]** ページを開きます。

     - このページに一覧表示されている仮想ネットワーク ゲートウェイを確認します。 自分のサブスクリプションに属している仮想ネットワーク ゲートウェイのみが表示されていることがわかります。 自分のサブスクリプションに属していない仮想ネットワーク ゲートウェイに接続する場合は、[PowerShell](vpn-gateway-vnet-vnet-rm-ps.md) を使用します。

     - 接続先となる仮想ネットワーク ゲートウェイを選択します。

     - **共有キー (PSK)** :このフィールドには、接続の共有キーを入力します。 このキーは、自分で生成または作成できます。 サイト間接続では、使用するキーは、オンプレミス デバイスと仮想ネットワーク ゲートウェイの接続の場合と同じになります。 VPN デバイスではなく、別の仮想ネットワーク ゲートウェイに接続するという点を除けば、概念は同じです。
    
4. **[OK]** を選択して変更を保存します。

## <a name="configure-the-testvnet4-gateway-connection"></a>TestVNet4 ゲートウェイ接続の構成
次は、TestVNet4 から TestVNet1 への接続を作成します。 ポータルで、TestVNet4 に関連付けられている仮想ネットワーク ゲートウェイを探します。 前のセクションの手順に従います。その際、TestVNet4 から TestVNet1 への接続を作成するように値を置き換えます。 必ず同一の共有キーを使用してください。

## <a name="verify-your-connections"></a>接続の確認

Azure ポータルで仮想ネットワーク ゲートウェイを探します。 **仮想ネットワーク ゲートウェイ**のページで、 **[接続]** を選択して仮想ネットワーク ゲートウェイの **[接続]** ページを表示します。 接続が確立された後、 **[状態]** の値が **[成功]** と **[接続済み]** に変わります。 接続を選択して **[要点]** ページを開き、詳細情報を表示します。

![成功](./media/vpn-gateway-howto-vnet-vnet-resource-manager-portal/connected.png "成功")

データのフローが開始されると、 **[データ入力]** と **[データ出力]** に値が表示されます。

![要点](./media/vpn-gateway-howto-vnet-vnet-resource-manager-portal/essentials.png "要点")

## <a name="add-additional-connections"></a>さらに接続を追加する

さらに接続を追加する場合は、接続の作成元となる仮想ネットワーク ゲートウェイに移動し、 **[接続]** を選択します。 別の VNet 間接続を作成することも、オンプレミスの場所への IPsec サイト間接続を作成することもできます。 作成する接続の種類に合わせて、 **[接続の種類]** を調整してください。 追加の接続を作成する前に、仮想ネットワークのアドレス空間が、接続先のアドレス空間と重複していないことを確認してください。 サイト間接続を作成する手順については、[サイト間接続の作成](vpn-gateway-howto-site-to-site-resource-manager-portal.md)に関するページを参照してください。

## <a name="vnet-to-vnet-faq"></a>VNet 間接続に関してよく寄せられる質問
VNet 間接続に関するその他の情報についてよく寄せられる質問の詳細を示します。

[!INCLUDE [vpn-gateway-vnet-vnet-faq](../../includes/vpn-gateway-faq-vnet-vnet-include.md)]

## <a name="next-steps"></a>次の手順

仮想ネットワーク内のリソースへのネットワーク トラフィックを制限する方法については、[ネットワーク セキュリティ](../virtual-network/security-overview.md)に関するページを参照してください。

Azure がトラフィックを Azure、オンプレミス、インターネット リソースの間でどのようにルーティングするかについては、「[仮想ネットワーク トラフィックのルーティング](../virtual-network/virtual-networks-udr-overview.md)」を参照してください。
