---
title: チュートリアル:Azure Virtual WAN を使用して ExpressRoute 接続を作成する
description: このチュートリアルでは、Azure Virtual WAN を使用して、Azure 環境とオンプレミス環境への ExpressRoute 接続を作成する方法について説明します。
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: tutorial
ms.date: 10/07/2020
ms.author: cherylmc
Customer intent: As someone with a networking background, I want to connect my corporate on-premises network(s) to my VNets using Virtual WAN and ExpressRoute.
ms.openlocfilehash: 080136b8fc25b08a6b96464f0a61115a4bb2f3f8
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102426615"
---
# <a name="tutorial-create-an-expressroute-association-using-azure-virtual-wan"></a>チュートリアル:Azure Virtual WAN を使用して ExpressRoute の関連付けを作成する

このチュートリアルでは、Virtual WAN を使用し、ExpressRoute 回線経由で Azure リソースに接続する方法を示します。 Virtual WAN と Virtual WAN リソースの詳細については、[Virtual WAN の概要](virtual-wan-about.md)に関するページを参照してください。

このチュートリアルでは、以下の内容を学習します。

> [!div class="checklist"]
> * 仮想 WAN を作成する
> * ハブとゲートウェイを作成する
> * VNet をハブに接続する
> * 回線をハブ ゲートウェイに接続する
> * 接続をテストする
> * ゲートウェイ サイズを変更する
> * 既定のルートをアドバタイズする

## <a name="prerequisites"></a>前提条件

構成を開始する前に、以下の条件を満たしていることを確認します。

* 接続先の仮想ネットワークが用意されていること。 オンプレミス ネットワークのどのサブネットも接続先の仮想ネットワークと重複していないことを確認してください。 Azure portal で仮想ネットワークを作成するには、[クイックスタート](../virtual-network/quick-create-portal.md)を参照してください。

* 仮想ネットワークに仮想ネットワーク ゲートウェイが存在しないこと。 仮想ネットワークにゲートウェイ (VPN または ExpressRoute のどちらか) が存在する場合は、すべてのゲートウェイを削除する必要があります。 代わりに、この構成では、仮想ネットワークが Virtual WAN ハブ ゲートウェイに接続されている必要があります。

* ハブ リージョンの IP アドレス範囲を取得します。 このハブは、Virtual WAN によって作成および使用される仮想ネットワークです。 ハブに指定するアドレス範囲が、接続先の既存の仮想ネットワークと重複することはできません。 さらに、オンプレミスで接続するアドレス範囲と重複することもできません。 オンプレミス ネットワーク構成に含まれている IP アドレス範囲になじみがない場合は、それらの詳細を提供できるだれかと調整してください。

* ハブ ゲートウェイに接続するには、ExpressRoute 回線を Premium または Standard 回線にする必要があります。

* Azure サブスクリプションをお持ちでない場合は、[無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)を作成してください。

## <a name="create-a-virtual-wan"></a><a name="openvwan"></a>仮想 WAN を作成する

ブラウザーから [Azure ポータル](https://portal.azure.com) に移動し、Azure アカウントでサインインします。

1. [仮想 WAN] ページに移動します。 ポータルで **+ [リソースの作成]** をクリックします。 検索ボックスに「**Virtual WAN**」と入力し、Enter キーを押します。
2. 結果から **[Virtual WAN]** を選択します。 [Virtual WAN] (仮想 WAN) ページで、 **[作成]** をクリックして [WAN の作成] ページを開きます。
3. **[WAN の作成]** ページの **[基本]** タブで、次のフィールドに入力します。

   ![WAN の作成](./media/virtual-wan-expressroute-portal/createwan.png)

   * **[サブスクリプション]** - 使用するサブスクリプションを選択します。
   * **[リソース グループ]** - 新規に作成するか、既存のものを使用します。
   * **[リソース グループの場所]** - ドロップダウンからリソースの場所を選択します。 WAN はグローバルなリソースであり、特定のリージョンに存在するものではありません。 ただし、作成する WAN リソースをより簡単に管理および検索するために、リージョンを選択する必要があります。
   * **[名前]** - WAN に付ける名前を入力します。
   * **[種類]** - **[標準]** を選択します。 Basic SKU を使用して ExpressRoute ゲートウェイを作成することはできません。
4. フィールドへの入力を完了したら、 **[確認および作成]** を選択します。
5. 検証に合格したら、 **[作成]** を選択して仮想 WAN を作成します。

## <a name="create-a-virtual-hub-and-gateway"></a><a name="hub"></a>仮想ハブとゲートウェイを作成する

仮想ハブは、仮想 WAN によって作成および使用される仮想ネットワークです。 VPN や ExpressRoute など、さまざまなゲートウェイを含めることができます。 このセクションでは、仮想ハブの ExpressRoute ゲートウェイを作成します。 [新しい仮想ハブを作成する](#newhub)ときにゲートウェイを作成するか、[既存のハブ](#existinghub)を編集し、そのゲートウェイを作成できます。 

ExpressRoute ゲートウェイは 2 Gbps の単位でプロビジョニングされます。 1 スケール ユニット = 2 Gbps で最大 10 スケール ユニットをサポート = 20 Gbps。 仮想ハブとゲートウェイが完全に作成されるまでに約 30 分かかります。

### <a name="to-create-a-new-virtual-hub-and-a-gateway"></a><a name="newhub"></a>新しい仮想ハブとゲートウェイを作成するには

新しい仮想ハブを作成します。 ハブが作成されると、サイトをアタッチしていない場合でも、ハブに対して課金されます。

[!INCLUDE [Create a hub](../../includes/virtual-wan-tutorial-er-hub-include.md)]

### <a name="to-create-a-gateway-in-an-existing-hub"></a><a name="existinghub"></a>既存のハブでゲートウェイを作成するには

既存のハブを編集し、ゲートウェイを作成することもできます。

1. 編集する仮想ハブに移動し、それを選択します。
2. **[仮想ハブを編集する]** ページで、 **[ExpressRoute ゲートウェイを含める]** オンにします。
3. **[確定]** を選択して変更を確定します。 ハブとハブ リソースが完全に作成されるまでに約 30 分かかります。

   ![既存のハブ](./media/virtual-wan-expressroute-portal/edithub.png "ハブの編集")

### <a name="to-view-a-gateway"></a>ゲートウェイを表示するには

ExpressRoute ゲートウェイを作成したら、ゲートウェイの詳細を表示できます。 ハブに移動し、 **[ExpressRoute]** を選択し、ゲートウェイを表示します。

![ゲートウェイの表示](./media/virtual-wan-expressroute-portal/viewgw.png "ゲートウェイの表示")

## <a name="connect-your-vnet-to-the-hub"></a><a name="connectvnet"></a>VNet をハブに接続する

このセクションでは、ハブと VNet の間にピアリング接続を作成します。 接続する VNet ごとにこれらの手順を繰り返します。

1. 仮想 WAN のページで、 **[仮想ネットワーク接続]** をクリックします。
2. 仮想ネットワーク接続のページで、 **[+ 接続の追加]** をクリックします。
3. **[接続の追加]** ページで、次のフィールドに入力します。

    * **[接続名]** - 接続に名前を付けます。
    * **[ハブ]** - この接続に関連付けるハブを選択します。
    * **[サブスクリプション]** - サブスクリプションを確認します。
    * **[仮想ネットワーク]** - このハブに接続する仮想ネットワークを選択します。 仮想ネットワークに既存の仮想ネットワーク ゲートウェイを設定することは (VPN と ExpressRoute のどちらでも) できません。

## <a name="connect-your-circuit-to-the-hub-gateway"></a><a name="connectcircuit"></a>回線をハブ ゲートウェイに接続する

ゲートウェイが作成されたら、[ExpressRoute 回線](../expressroute/expressroute-howto-circuit-portal-resource-manager.md) をそれに接続できます。 ExpressRoute Global Reach がサポートされている場所にある ExpressRoute の Standard または Premium 回路を Virtual WAN ExpressRoute ゲートウェイに接続し、Virtual WAN のあらゆる転送機能 (VPN 間、VPN、ExpressRoute) を利用できます。 Global Reach の場所ではないところにある ExpressRoute の Standard または Premium 回路は Azure リソースに接続できますが、Virtual WAN の転送機能は使用できません。 ExpressRoute Local が Azure Virtual WAN ハブでサポートされるのは、仮想 WAN ハブに接続されたスポーク VNET が仮想 WAN ハブと同じリージョンに存在する場合に限られます。

### <a name="to-connect-the-circuit-to-the-hub-gateway"></a>回線をハブ ゲートウェイに接続するには

ポータルで、**[仮想ハブ]、[接続]、[ExpressRoute]** ページの順に進みます。 サブスクリプションで ExpressRoute 回線にアクセスできる場合、回線の一覧に使用する回線が表示されます。 回線が表示されないが、承認キーとピア回線 URI が与えられている場合、回線を利用して接続できます。 「[承認キーを利用して接続するには](#authkey)」を参照してください。

1. 回線を選択します。
2. **[Connect circuit(s)]\(回線の接続\)** を選択します。

   ![回線の接続](./media/virtual-wan-expressroute-portal/cktconnect.png "回線の接続")

### <a name="to-connect-by-redeeming-an-authorization-key"></a><a name="authkey"></a>承認キーを利用して接続するには

接続するために与えられた承認キーと回線 URI を使用します。

1. [ExpressRoute] ページで、**[+Redeem authorization key]\(+承認キーを利用する\)** をクリックします。

   ![仮想ハブの ExpressRoute を示すスクリーンショット。[+Redeem authorization key]\(+ 承認キーを利用する\) が選択されている。](./media/virtual-wan-expressroute-portal/redeem.png "利用")
2. [Redeem authorization key]\(承認キーを利用する\) ページで値を入力します。

   ![キー値を利用する](./media/virtual-wan-expressroute-portal/redeemkey2.png "キー値を利用する")
3. **[追加]** を選択してキーを追加します。
4. 回線を表示します。 利用された回線には、名前のみが表示されます (種類、プロバイダー、その他の情報はありません)。ユーザーのサブスクリプションとはことなるサブスクリプションにあるためです。

## <a name="to-test-connectivity"></a>接続をテストするには

回線接続が確立されたら、ハブ接続の状態に "このハブ" と示されます。これは、ハブ ExpressRoute ゲートウェイへの接続が確立されたことを意味します。 前に作成した VNet の VM など、ExpressRoute 回路の後にあるクライアントからの接続は約 5 分待ってからテストしてください。

ExpressRoute ゲートウェイと同じハブでサイトを Virtual WAN VPN ゲートウェイに接続している場合、VPN と ExpressRoute のエンド ポイント間で双方向接続が与えられます。 動的ルーティング (BGP) がサポートされています。 ハブ内のゲートウェイの ASN は固定であり、この時点では編集できません。

## <a name="to-change-the-size-of-a-gateway"></a>ゲートウェイのサイズを変更するには

ExpressRoute ゲートウェイのサイズを変更する場合、ハブ内で ExpressRoute ゲートウェイを見つけ、ドロップダウンからスケール ユニットを選択します。 変更を保存します。 ハブ ゲートウェイの更新には約 30 分かかります。

![ゲートウェイ サイズの変更](./media/virtual-wan-expressroute-portal/changescale.png "ゲートウェイ サイズの変更")

## <a name="to-advertise-default-route-00000-to-endpoints"></a>既定ルート 0.0.0.0/0 をエンドポイントにアドバタイズするには

Azure 仮想ハブで既定ルート 0.0.0.0/0 を ExpressRoute エンド ポイントにアドバタイズする場合、[Propagate default route]\(既定ルートの伝達\) を有効にする必要があります。

1. **[回線]、[...]、[接続の編集]** の順に選択します。

   ![接続の編集](./media/virtual-wan-expressroute-portal/defaultroute1.png "接続の編集")

2. **[有効にする]** を選択し、既定ルートを伝達します。

   ![既定ルートの伝達](./media/virtual-wan-expressroute-portal/defaultroute2.png "既定ルートの伝達")

## <a name="clean-up-resources"></a><a name="cleanup"></a>リソースをクリーンアップする

作成したリソースは、不要になったら削除してください。 Virtual WAN リソースのいくつかは、依存関係に応じた特定の順序で削除する必要があります。 削除が完了するまでに 30 分程度かかる場合があります。

[!INCLUDE [Delete resources](../../includes/virtual-wan-resource-cleanup.md)]

## <a name="next-steps"></a>次のステップ

次に、Virtual WAN の詳細については下記を参照してください。

> [!div class="nextstepaction"]
> * [Virtual WAN の FAQ](virtual-wan-faq.md)
