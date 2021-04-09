---
title: チュートリアル:VNet を ExpressRoute 回線にリンクする - Azure portal
description: このチュートリアルでは、Azure portal を使って Azure ExpressRoute 回線に仮想ネットワークをリンクするための接続を作成する方法を説明します。
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: tutorial
ms.date: 10/15/2020
ms.author: duau
ms.custom: seodec18
ms.openlocfilehash: 0ffc9c2ee17862497d3fd986da8e003f7a497056
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "95998772"
---
# <a name="tutorial-connect-a-virtual-network-to-an-expressroute-circuit-using-the-portal"></a>チュートリアル:ポータルを使用して仮想ネットワークを ExpressRoute 回線に接続する

> [!div class="op_single_selector"]
> * [Azure Portal](expressroute-howto-linkvnet-portal-resource-manager.md)
> * [PowerShell](expressroute-howto-linkvnet-arm.md)
> * [Azure CLI](howto-linkvnet-cli.md)
> * [ビデオ - Azure Portal](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-a-connection-between-your-vpn-gateway-and-expressroute-circuit)
> * [PowerShell (クラシック)](expressroute-howto-linkvnet-classic.md)
> 

このチュートリアルでは、Azure portal を使って Azure ExpressRoute 回線に仮想ネットワークをリンクするための接続を作成する方法を説明します。 Azure ExpressRoute 回線に接続する仮想ネットワークは、同じサブスクリプションにあっても、別のサブスクリプションの一部であってもかまいません。

このチュートリアルでは、以下の内容を学習します。
> [!div class="checklist"]
> - 仮想ネットワークを同じサブスクリプション内の回線に接続します。
> - 仮想ネットワークを別のサブスクリプションの回線に接続します。
> - 仮想ネットワークと ExpressRoute 回線の間のリンクを削除します。

## <a name="prerequisites"></a>前提条件

* 構成を開始する前に、[前提条件](expressroute-prerequisites.md)、[ルーティングの要件](expressroute-routing.md)、[ワークフロー](expressroute-workflows.md)を確認します。

* アクティブな ExpressRoute 回線が必要です。
  * 手順に従って、 [ExpressRoute 回線を作成](expressroute-howto-circuit-portal-resource-manager.md) し、接続プロバイダー経由で回線を有効にしてください。
  * 回線用に Azure プライベート ピアリングが構成されていることを確認してください。 ピアリングとルーティングの手順については、「[ExpressRoute 回線のピアリングの作成と変更](expressroute-howto-routing-portal-resource-manager.md)」をご覧ください。
  * エンドツーエンド接続のために、確実に Azure プライベート ピアリングが構成され、ご使用のネットワークと Microsoft の間の BGP ピアリングを確立します。
  * 仮想ネットワークと仮想ネットワーク ゲートウェイを作成し、完全にプロビジョニングします。 指示に従って [ExpressRoute 用の仮想ネットワーク ゲートウェイを作成](expressroute-howto-add-gateway-resource-manager.md)します。 ExpressRoute 用の仮想ネットワーク ゲートウェイは、VPN ではなく GatewayType 'ExpressRoute' を使用します。

* 最大 10 個の仮想ネットワークを標準 ExpressRoute 回線に接続できます。 標準 ExpressRoute 回線を使用する場合は、すべての仮想ネットワークが同じ地理的リージョンに存在する必要があります。

* 単一の VNet を最大 4 つの ExpressRoute 回線にリンクできます。 次のプロセスを使用して、接続先の各 ExpressRoute 回線の新しい接続オブジェクトを作成します。 ExpressRoute 回線は、同じサブスクリプション、異なるサブスクリプション、または両方の組み合わせにすることができます。

* ExpressRoute Premium アドオンを有効にした場合は、ExpressRoute 回線の地理的リージョンの外部にある仮想ネットワークをリンクすることができます。 Premium アドオンを使用すると、選択した帯域幅に応じて、10 を超える仮想ネットワークを ExpressRoute 回線に接続することもできます。 Premium アドオンの詳細については、 [FAQ](expressroute-faqs.md) を確認してください。

* 手順をより理解するため、開始する前に[ビデオを参照](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-a-connection-between-your-vpn-gateway-and-expressroute-circuit)できます。

## <a name="connect-a-vnet-to-a-circuit---same-subscription"></a>VNet を回線に接続する - 同じサブスクリプション

> [!NOTE]
> ピアリングをレイヤー 3 プロバイダーが構成した場合、BGP の構成情報は表示されません。 回線がプロビジョニング済み状態の場合は、接続を作成できます。
>

### <a name="to-create-a-connection"></a>接続を作成するには

1. ExpressRoute 回線と Azure プライベート ピアリングが正常に構成されていることを確認します。 「[ExpressRoute 回線の作成](expressroute-howto-circuit-arm.md)」と「[ExpressRoute 回線のピアリングの作成と変更](expressroute-howto-routing-arm.md)」の手順に従います。 ExpressRoute 回線は次の図のようになります。

    :::image type="content" source="./media/expressroute-howto-linkvnet-portal-resource-manager/express-route-circuit.png" alt-text="ExpressRoute 回線のスクリーンショット":::

1. ExpressRoute 回線に仮想ネットワーク ゲートウェイをリンクする接続のプロビジョニングを開始できるようになりました。 **[接続]**  >  **[追加]** を選択して **[接続の追加]** ページを開きます。

    :::image type="content" source="./media/expressroute-howto-linkvnet-portal-resource-manager/add-connection.png" alt-text="接続追加のスクリーンショット":::

1. 接続の名前を入力し、 **[次へ: 設定 >]** を選択します。

    :::image type="content" source="./media/expressroute-howto-linkvnet-portal-resource-manager/create-connection-basic.png" alt-text="[接続の作成] の [基本] ページ":::

1. 回線にリンクする仮想ネットワークに属しているゲートウェイを選択し、 **[確認および作成]** を選択します。 その後、検証が完了したら、 **[作成]** を選択してください。

    :::image type="content" source="./media/expressroute-howto-linkvnet-portal-resource-manager/create-connection-settings.png" alt-text="[接続の作成] の [設定] ページ":::

1. 接続が正常に構成されると、接続オブジェクトにより、接続の情報が表示されます。

    :::image type="content" source="./media/expressroute-howto-linkvnet-portal-resource-manager/connection-object.png" alt-text="接続オブジェクトのスクリーンショット":::

## <a name="connect-a-vnet-to-a-circuit---different-subscription"></a>VNet を回線に接続する - 異なるサブスクリプション

複数のサブスクリプションで ExpressRoute 回線を共有できます。 下図に、複数のサブスクリプションで ExpressRoute 回線を共有するしくみについて概略を示します。

:::image type="content" source="./media/expressroute-howto-linkvnet-portal-resource-manager/cross-subscription.png" alt-text="サブスクリプション間接続":::

大規模クラウド内のそれぞれの小規模クラウドは、組織内のさまざまな部門に属するサブスクリプションを表すために使用されています。 組織内の各部門は、サービスのデプロイを目的として、固有のサブスクリプションを使用できますが、1 つの ExpressRoute 回線を共有することで、オンプレミス ネットワークに接続し直すことができます。 1 つの部門 (この例では IT) で ExpressRoute 回線を所有できます。 組織内の他のサブスクリプションを使用する場合も、ExpressRoute 回線を使用できます。

  > [!NOTE]
  > 専用回線の接続と帯域幅の料金は、ExpressRoute 回線の所有者が負担することになります。 すべての仮想ネットワークが同じ帯域幅を共有します。
  >

### <a name="administration---about-circuit-owners-and-circuit-users"></a>管理 - 回線所有者と回線ユーザーについて

"回線所有者" は、ExpressRoute 回線リソースの権限のあるパワー ユーザーです。 回線所有者は、"回線ユーザー" が利用できる承認を作成できます。 回線ユーザーは、ExpressRoute 回線と同じサブスクリプション内には存在しない仮想ネットワーク ゲートウェイの所有者です。 回線ユーザーは、承認を利用できます (仮想ネットワークごとに 1 つの承認)。

回線所有者は、承認をいつでも変更し、取り消す権限を持っています。 承認を取り消すと、アクセスが取り消されたサブスクリプションからすべてのリンク接続が削除されます。

### <a name="circuit-owner-operations"></a>回線所有者の操作

**接続の承認を作成するには**

回線所有者が承認を作成すると、承認キーが作成されます。回線ユーザーは、その承認キーを使用して、各自の仮想ネットワーク ゲートウェイを ExpressRoute 回線に接続します。 承認は、1 つの接続に対してのみ有効です。

> [!NOTE]
> 接続ごとに別個の認可が必要です。
>

1. [ExpressRoute] ページで **[承認]** をクリックし、承認の **名前** を入力して、 **[保存]** をクリックします。

    :::image type="content" source="./media/expressroute-howto-linkvnet-portal-resource-manager/authorization.png" alt-text="[Authorizations]":::

2. 構成が保存されたら、**リソース ID** と **承認キー** をコピーします。

    :::image type="content" source="./media/expressroute-howto-linkvnet-portal-resource-manager/authorization-key.png" alt-text="承認キー":::

**接続の承認を削除するには**

接続を削除するには、接続の承認キーの **[削除]** アイコンを選択します。

:::image type="content" source="./media/expressroute-howto-linkvnet-portal-resource-manager/delete-authorization-key.png" alt-text="承認キーを削除する":::

接続のみ削除し、承認キーは保持したい場合は、回線の [接続] ページから接続を削除することができます。

:::image type="content" source="./media/expressroute-howto-linkvnet-portal-resource-manager/delete-connection-owning-circuit.png" alt-text="接続によって所有されている回線を削除する":::

### <a name="circuit-user-operations"></a>回線ユーザーの操作

回線ユーザーは、リソース ID と回線所有者が作成した承認キーを必要とします。

**接続の承認を利用するには**

1. **[+ リソースの作成]** ボタンを選択します。 「**接続**」を検索し、 **[作成]** を選択します。

    :::image type="content" source="./media/expressroute-howto-linkvnet-portal-resource-manager/create-new-resources.png" alt-text="新しいリソースの作成に関するページ":::

1. *[接続の種類]* を **[ExpressRoute]** に設定します。 *[リソース グループ]* と *[場所]* を選択し、[基本] ページで **[OK]** を選択します。

    > [!NOTE]
    > この場所は、接続を作成している仮想ネットワーク ゲートウェイの場所と一致している "*必要があります*"。

    :::image type="content" source="./media/expressroute-howto-linkvnet-portal-resource-manager/connection-basics.png" alt-text="[基本] ページ":::

1. **[設定]** ページで *[仮想ネットワーク ゲートウェイ]* を選び、**[承認の利用]** チェック ボックスをオンにします。 *承認キー* と *ピア回線 URI* を入力し、接続に名前を付けます。 **[OK]** を選択します。 
 
    > [!NOTE]
    > *[ピア回線の URI]* は、ExpressRoute 回線のリソース ID です (ExpressRoute 回線の [Properties Setting]\(プロパティ設定\) ウィンドウで確認できます)。

    :::image type="content" source="./media/expressroute-howto-linkvnet-portal-resource-manager/connection-settings.png" alt-text="設定ページ":::

1. **[概要]** ページの内容を確認し、 **[OK]** を選択します。

    :::image type="content" source="./media/expressroute-howto-linkvnet-portal-resource-manager/connection-summary.png" alt-text="[概要] ページ":::

## <a name="clean-up-resources"></a>リソースをクリーンアップする

接続のページの **[削除]** アイコンを選ぶことにより、接続を削除して、ExpressRoute 回線への VNet のリンクを解除することができます。

:::image type="content" source="./media/expressroute-howto-linkvnet-portal-resource-manager/delete-connection.png" alt-text="接続の削除":::

## <a name="next-steps"></a>次のステップ

このチュートリアルでは、同じサブスクリプションと別のサブスクリプションの回線に、仮想ネットワークを接続する方法について説明しました。 ExpressRoute ゲートウェイの詳細については、次を参照してください。 

> [!div class="nextstepaction"]
> [ExpressRoute の仮想ネットワーク ゲートウェイについて](expressroute-about-virtual-network-gateways.md)
