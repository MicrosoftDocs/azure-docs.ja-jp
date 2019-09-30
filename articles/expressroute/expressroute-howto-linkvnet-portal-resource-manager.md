---
title: 仮想ネットワークを回線にリンクする - ExpressRoute:Azure portal | Microsoft Docs
description: Azure ExpressRoute 回線に VNet を接続する 手順を説明します。
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: conceptual
ms.date: 09/17/2019
ms.author: cherylmc
ms.custom: seodec18
ms.openlocfilehash: 6185540340bba4299eb9307432480c91d531779a
ms.sourcegitcommit: c79aa93d87d4db04ecc4e3eb68a75b349448cd17
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/18/2019
ms.locfileid: "71088909"
---
# <a name="connect-a-virtual-network-to-an-expressroute-circuit-using-the-portal"></a>ポータルを使用して仮想ネットワークを ExpressRoute 回線に接続する
> [!div class="op_single_selector"]
> * [Azure Portal](expressroute-howto-linkvnet-portal-resource-manager.md)
> * [PowerShell](expressroute-howto-linkvnet-arm.md)
> * [Azure CLI](howto-linkvnet-cli.md)
> * [ビデオ - Azure Portal](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-a-connection-between-your-vpn-gateway-and-expressroute-circuit)
> * [PowerShell (クラシック)](expressroute-howto-linkvnet-classic.md)
> 

この記事では、Azure Portal を使って Azure ExpressRoute 回線に仮想ネットワークをリンクするための接続を作成する方法を説明します。 Azure ExpressRoute 回線に接続する仮想ネットワークは、同じサブスクリプションにあっても、別のサブスクリプションの一部であってもかまいません。

## <a name="before-you-begin"></a>開始する前に

* 構成を開始する前に、[前提条件](expressroute-prerequisites.md)、[ルーティングの要件](expressroute-routing.md)、[ワークフロー](expressroute-workflows.md)を確認します。

* アクティブな ExpressRoute 回線が必要です。
  * 手順に従って、 [ExpressRoute 回線を作成](expressroute-howto-circuit-portal-resource-manager.md) し、接続プロバイダー経由で回線を有効にしてください。
  * 回線用に Azure プライベート ピアリングが構成されていることを確認してください。 ピアリングとルーティングの手順については、「[ExpressRoute 回線のピアリングの作成と変更](expressroute-howto-routing-portal-resource-manager.md)」をご覧ください。
  * Azure プライベート ピアリングが構成されていることを確認します。また、エンド ツー エンド接続を有効にできるように、ネットワークと Microsoft の間の BGP ピアリングを起動しておく必要があります。
  * 仮想ネットワークと仮想ネットワーク ゲートウェイを作成し、完全にプロビジョニングします。 指示に従って [ExpressRoute 用の仮想ネットワーク ゲートウェイを作成](expressroute-howto-add-gateway-resource-manager.md)します。 ExpressRoute 用の仮想ネットワーク ゲートウェイは、VPN ではなく GatewayType 'ExpressRoute' を使用します。

* 最大 10 個の仮想ネットワークを標準 ExpressRoute 回線に接続できます。 標準 ExpressRoute 回線を使用する場合は、すべての仮想ネットワークが同じ地理的リージョンに存在する必要があります。

* 単一の VNet を最大 4 つの ExpressRoute 回線にリンクできます。 以下のプロセスを使用して、接続先の各 ExpressRoute 回線の新しい接続オブジェクトを作成します。 ExpressRoute 回線は、同じサブスクリプション、異なるサブスクリプション、または両方の組み合わせにすることができます。

* ExpressRoute Premium アドオンを有効にした場合は、ExpressRoute 回線の地理的リージョンの外部にある仮想ネットワークをリンクしたり、さらに多くの仮想ネットワークを ExpressRoute 回線にリンクしたりすることができます。 Premium アドオンの詳細については、 [FAQ](expressroute-faqs.md) を確認してください。

* 手順をより理解するため、開始する前に[ビデオを参照](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-a-connection-between-your-vpn-gateway-and-expressroute-circuit)できます。

## <a name="connect-a-vnet-to-a-circuit---same-subscription"></a>VNet を回線に接続する - 同じサブスクリプション

> [!NOTE]
> ピアリングをレイヤー 3 プロバイダーが構成した場合、BGP の構成情報は表示されません。 回線がプロビジョニング済み状態の場合は、接続を作成できます。
>

### <a name="to-create-a-connection"></a>接続を作成するには

1. ExpressRoute 回線と Azure プライベート ピアリングが正常に構成されていることを確認します。 「[ExpressRoute 回線の作成](expressroute-howto-circuit-arm.md)」と「[ExpressRoute 回線のピアリングの作成と変更](expressroute-howto-routing-arm.md)」の手順に従います。 ExpressRoute 回線は次の図のようになります。

   [![ExpressRoute 回線のスクリーンショット](./media/expressroute-howto-linkvnet-portal-resource-manager/routing1.png "回線の表示")](./media/expressroute-howto-linkvnet-portal-resource-manager/routing1-exp.png#lightbox)
2. ExpressRoute 回線に仮想ネットワーク ゲートウェイをリンクする接続のプロビジョニングを開始できるようになりました。 **[接続]**  >  **[追加]** をクリックして **[接続の追加]** ページを開き、値を構成します。

   [![接続の追加のスクリーンショット](./media/expressroute-howto-linkvnet-portal-resource-manager/samesub1.png "接続の追加のスクリーンショット")](./media/expressroute-howto-linkvnet-portal-resource-manager/samesub1-exp.png#lightbox)
3. 接続が正常に構成されると、接続オブジェクトにより、接続の情報が表示されます。

   ![接続オブジェクトのスクリーンショット](./media/expressroute-howto-linkvnet-portal-resource-manager/samesub2.png)


## <a name="connect-a-vnet-to-a-circuit---different-subscription"></a>VNet を回線に接続する - 異なるサブスクリプション

複数のサブスクリプションで ExpressRoute 回線を共有できます。 下図に、複数のサブスクリプションで ExpressRoute 回線を共有するしくみについて概略を示します。

![サブスクリプション間接続](./media/expressroute-howto-linkvnet-portal-resource-manager/cross-subscription.png)

- 大規模クラウド内のそれぞれの小規模クラウドは、組織内のさまざまな部門に属するサブスクリプションを表すために使用されています。
- 組織内の各部門はサービスのデプロイ用に固有のサブスクリプションを使用できますが、1 つの ExpressRoute 回線を共有することで、オンプレミス ネットワークに接続し直すことができます。
- 1 つの部門 (この例では IT) で ExpressRoute 回線を所有できます。 組織内の他のサブスクリプションも、ExpressRoute 回線と、その他の Azure Active Directory テナントおよび Enterprise Agreement の登録にリンクされたサブスクリプションを含む回線に関連付けられた承認を使用できます。

  > [!NOTE]
  > 専用回線の接続と帯域幅の料金は、ExpressRoute 回線の所有者が負担することになります。 すべての仮想ネットワークが同じ帯域幅を共有します。
  >
  >

### <a name="administration---about-circuit-owners-and-circuit-users"></a>管理 - 回線所有者と回線ユーザーについて

"回線所有者" は、ExpressRoute 回線リソースの権限のあるパワー ユーザーです。 回線所有者は、"回線ユーザー" が利用できる承認を作成できます。 回線ユーザーは、ExpressRoute 回線と同じサブスクリプション内にない仮想ネットワーク ゲートウェイの所有者です。 回線ユーザーは、承認を利用できます (仮想ネットワークごとに 1 つの承認)。

回線所有者は、承認をいつでも変更し、取り消す権限を持っています。 承認を取り消すと、アクセスが取り消されたサブスクリプションからすべてのリンク接続が削除されます。

### <a name="circuit-owner-operations"></a>回線所有者の操作

**接続の承認を作成するには**

回線所有者は、承認を作成します。 その結果、回線ユーザーが各自の仮想ネットワーク ゲートウェイを ExpressRoute 回線に接続するために使用できる承認キーが作成されます。 承認は、1 つの接続に対してのみ有効です。

> [!NOTE]
> 接続ごとに別個の認可が必要です。
>

1. [ExpressRoute] ページで **[承認]** をクリックし、承認の**名前**を入力して、 **[保存]** をクリックします。

   ![[Authorizations]](./media/expressroute-howto-linkvnet-portal-resource-manager/authorization.png)
2. 構成が保存されたら、**リソース ID** と**承認キー**をコピーします。

   ![承認キー](./media/expressroute-howto-linkvnet-portal-resource-manager/authkey.png)

**接続の承認を削除するには**

接続を削除するには、接続のページで **[削除]** アイコンを選びます。

### <a name="circuit-user-operations"></a>回線ユーザーの操作

回線ユーザーは、リソース ID と回線所有者が作成した承認キーを必要とします。

**接続の承認を利用するには**

1. **[+ 新規]** ボタンをクリックします。

   ![[新規] をクリック](./media/expressroute-howto-linkvnet-portal-resource-manager/Connection1.png)
2. Marketplace で **"接続"** を検索し、選択して **[作成]** をクリックします。

   ![接続の検索](./media/expressroute-howto-linkvnet-portal-resource-manager/Connection2.png)
3. **[接続の種類]** を [ExpressRoute] に設定します。
4. [基本] ページで詳細を入力し、 **[OK]** をクリックします。

   ![基本ページ](./media/expressroute-howto-linkvnet-portal-resource-manager/Connection3.png)
5. **[設定]** ページで **[仮想ネットワーク ゲートウェイ]** を選び、 **[承認の利用]** チェック ボックスをオンにします。
6. **承認キー**と**ピア回線 URI** を入力し、接続に名前を付けます。 Click **OK**. **[ピア回線の URI]** は、ExpressRoute 回線のリソース ID です (ExpressRoute 回線の [Properties Setting]\(プロパティ設定\) ウィンドウで確認できます)。

   ![[設定] ページ](./media/expressroute-howto-linkvnet-portal-resource-manager/Connection4.png)
7. **[概要]** ページの内容を確認し、 **[OK]** をクリックします。

**接続の承認を解除するには**

ExpressRoute 回線を仮想ネットワークにリンクしている接続を削除することで、承認を解除できます。

## <a name="delete-a-connection-to-unlink-a-vnet"></a>接続を削除して VNet のリンクを解除する

接続のページの **[削除]** アイコンを選ぶことにより、接続を削除して、ExpressRoute 回線への VNet のリンクを解除することができます。

## <a name="next-steps"></a>次の手順
ExpressRoute の詳細については、「 [ExpressRoute のFAQ](expressroute-faqs.md)」をご覧ください。
