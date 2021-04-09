---
title: チュートリアル:Microsoft ピアリングにルート フィルターを構成する - Azure Portal
description: このチュートリアルでは、Azure portal を使用して Microsoft ピアリングにルート フィルターを構成する方法について説明します。
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: tutorial
ms.date: 10/15/2020
ms.author: duau
ms.custom: seodec18
ms.openlocfilehash: d142028d197f9e279b5f1e05757946dc40d2c153
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "92109138"
---
# <a name="tutorial-configure-route-filters-for-microsoft-peering-using-the-azure-portal"></a>チュートリアル:Azure Portal を使用して Microsoft ピアリングにルート フィルターを構成する

> [!div class="op_single_selector"]
> * [Azure Portal](how-to-routefilter-portal.md)
> * [Azure PowerShell](how-to-routefilter-powershell.md)
> * [Azure CLI](how-to-routefilter-cli.md)
> 

ルート フィルターとは、Microsoft ピアリングでサポートされるサービスの一部だけを利用する手段です。 この記事の手順を通じて、ExpressRoute 回線にルート フィルターを構成し、管理することができます。

Microsoft 365 サービス (Exchange Online、SharePoint Online、Skype for Business など) には、Microsoft ピアリングを介してアクセスできます。 ExpressRoute 回線に Microsoft ピアリングを構成すると、これらのサービスに関連したすべてのプレフィックスが、確立された BGP セッションを通じてアドバタイズされます。 提供されているサービスをプレフィックスで識別するために、すべてのプレフィックスには BGP コミュニティ値がアタッチされます。 BGP コミュニティ値とサービスのマッピング一覧については、[BGP コミュニティ](expressroute-routing.md#bgp)に関するページを参照してください。

すべての Azure および Microsoft 365 サービスに接続すると、多数のプレフィックスが BGP を通じてアドバタイズされます。 このようにプレフィックスが多数になると、ネットワーク内のルーターによって管理されるルート テーブルのサイズが著しく増加します。 Microsoft ピアリング経由で提供されるサービスの一部しか利用する予定がない場合は、ルート テーブルのサイズを 2 とおりの方法で減らすことができます。 次のようにすることができます。

* BGP コミュニティにルート フィルターを適用して不要なプレフィックスを除外する。 ルート フィルター処理はネットワーク運用の標準的技法であり、多くのネットワークで広く使用されています。

* ルート フィルターを定義して ExpressRoute 回線に適用する。 ルート フィルターは、Microsoft ピアリング経由で利用する予定の一連のサービスを選択できる新しいリソースです。 ExpressRoute ルーターからは、ルート フィルターで識別されたサービスに属しているプレフィックスのリストだけが送信されます。

このチュートリアルでは、以下の内容を学習します。
> [!div class="checklist"]
> - BGP コミュニティ値を取得する。
> - ルート フィルターとフィルターのルールを作成する。
> - ExpressRoute 回線にルート フィルターを関連付ける。

### <a name="about-route-filters"></a><a name="about"></a>ルート フィルターについて

ExpressRoute 回線に Microsoft ピアリングが構成されると、接続プロバイダーを介してご利用のエッジ ルーターとの間に一対の BGP セッションが Microsoft エッジ ルーターによって確立されます。 貴社のネットワークにはルートが一切アドバタイズされません。 ネットワークに対するルート アドバタイズを有効にするには、ルート フィルターを関連付ける必要があります。

ExpressRoute 回線の Microsoft ピアリング経由で利用するサービスがルート フィルターによって識別されます。 これは、実質的にはすべての BGP コミュニティ値から成る許可リストです。 ルート フィルター リソースを定義して ExpressRoute 回線にアタッチすると、BGP コミュニティ値にマッピングされたすべてのプレフィックスが貴社のネットワークにアドバタイズされます。

Microsoft 365 サービスにルート フィルターをアタッチするには、ExpressRoute 経由での Microsoft 365 サービスの利用に対する承認が必要となります。 ExpressRoute 経由で Microsoft 365 サービスを利用することが承認されていない場合、ルート フィルターをアタッチする操作は失敗します。 承認プロセスの詳細については、[Microsoft 365 向け Azure ExpressRoute](/microsoft-365/enterprise/azure-expressroute)に関する記事を参照してください。

> [!IMPORTANT]
> 2017 年 8 月 1 日より前に構成された ExpressRoute 回線の Microsoft ピアリングでは、ルート フィルターが定義されていない場合でも、すべてのサービス プレフィックスが Microsoft ピアリングでアドバタイズされます。 2017 年 8 月 1 日以降に構成された ExpressRoute 回線の Microsoft ピアリングでは、ルート フィルターが回線に接続されるまで、プレフィックスはアドバタイズされません。
> 

## <a name="prerequisites"></a>前提条件

- 構成を開始する前に、[前提条件](expressroute-prerequisites.md)と[ワークフロー](expressroute-workflows.md)を確認してください。

- Microsoft ピアリングがプロビジョニングされたアクティブな ExpressRoute 回線を用意する。 その作業は、次の手順に従って実行できます。
  - あらかじめ [ExpressRoute 回線を作成](expressroute-howto-circuit-portal-resource-manager.md)し、接続プロバイダー経由で回線を有効にしておきます。 ExpressRoute 回線がプロビジョニングされて有効な状態になっている必要があります。
  - BGP セッションを直接管理する場合は、[Microsoft ピアリングを作成](expressroute-howto-routing-portal-resource-manager.md)します。 または、ご利用の回線に対する Microsoft ピアリングのプロビジョニングを接続プロバイダーに依頼します。

## <a name="get-a-list-of-prefixes-and-bgp-community-values"></a><a name="prefixes"></a>一連のプレフィックスと BGP コミュニティ値を取得する

### <a name="get-a-list-of-bgp-community-values"></a>一連の BGP コミュニティ値を取得する

Microsoft ピアリング経由でアクセスできるサービスに関連付けられた BGP コミュニティ値は、「[ExpressRoute のルーティングの要件](expressroute-routing.md)」ページで確認できます。

### <a name="make-a-list-of-the-values-that-you-want-to-use"></a>使用する値をリストアップする

ルート フィルターで使用する [BGP コミュニティ値](expressroute-routing.md#bgp)をリストアップします。 

## <a name="create-a-route-filter-and-a-filter-rule"></a><a name="filter"></a>ルート フィルターとフィルター ルールを作成する

ルート フィルターに割り当てることができるルールは 1 つだけで、また "許可" タイプであることが必要です。 このルールに、一連の BGP コミュニティ値を関連付けることができます。

1. **[リソースの作成]** を選択して、次の図に示すように "*ルート フィルター*" を検索します。

    :::image type="content" source="./media/how-to-routefilter-portal/create-route-filter.png" alt-text="[ルート フィルター] ページを示すスクリーンショット":::

1. ルート フィルターをリソース グループに配置します。 場所は確実に ExpressRoute 回線と同じであるようにします。 **[確認および作成]** 、 **[作成]** の順に選択します。

    :::image type="content" source="./media/how-to-routefilter-portal/create-route-filter-basic.png" alt-text="サンプルの値が入力された [ルート フィルターの作成] ページを示すスクリーンショット":::

### <a name="create-a-filter-rule"></a>フィルター ルールを作成する

1. ルールを追加および更新するには、ルート フィルターの [ルールの管理] タブを選択します。

    :::image type="content" source="./media/how-to-routefilter-portal/manage-route-filter.png" alt-text="[ルールの管理] アクションが強調表示されている [概要] ページを示すスクリーンショット":::

1. ドロップダウン リストから接続するサービスを選択し、完了したらルールを保存します。

    :::image type="content" source="./media/how-to-routefilter-portal/add-route-filter-rule.png" alt-text="[許可されたサービス コミュニティ] ドロップダウン リストでサービスが選択されている [ルールの管理] ウィンドウを示すスクリーンショット":::

## <a name="attach-the-route-filter-to-an-expressroute-circuit"></a><a name="attach"></a>ルート フィルターを ExpressRoute 回線にアタッチする

**[+ 回線の追加]** ボタンを選択し、ドロップダウン リストから ExpressRoute 回線を選択することで、ルート フィルターを回線にアタッチします。

:::image type="content" source="./media/how-to-routefilter-portal/add-circuit-to-route-filter.png" alt-text="[回線の追加] アクションが選択されている [概要] ページを示すスクリーンショット":::

ご利用の ExpressRoute 回線のピアリングを接続プロバイダーが構成する場合は、 **[+ 回線の追加]** ボタンを選択する前に ExpressRoute 回線ページから回線を更新します。

:::image type="content" source="./media/how-to-routefilter-portal/refresh-express-route-circuit.png" alt-text="[更新] アクションが選択されている [概要] ページを示すスクリーンショット。":::

## <a name="common-tasks"></a><a name="tasks"></a>一般的なタスク

### <a name="to-get-the-properties-of-a-route-filter"></a><a name="getproperties"></a>ルート フィルターのプロパティを取得するには

ポータルでリソースを開くときに、ルート フィルターのプロパティを表示できます。

:::image type="content" source="./media/how-to-routefilter-portal/view-route-filter.png" alt-text="[概要] ページを示すスクリーンショット":::

### <a name="to-update-the-properties-of-a-route-filter"></a><a name="updateproperties"></a>ルート フィルターのプロパティを更新するには

1. **[ルールの管理]** ボタンを選択することで、回線にアタッチした BGP コミュニティ値の一覧を更新できます。

    :::image type="content" source="./media/how-to-routefilter-portal/update-route-filter.png" alt-text="[ルールの管理] アクションを使用してルート フィルターを更新する":::

1. 必要なサービス コミュニティを選択し、 **[保存]** を選択します。

    :::image type="content" source="./media/how-to-routefilter-portal/add-route-filter-rule.png" alt-text="サービスが選択されている [ルールの管理] ウィンドウを示すスクリーンショット":::

### <a name="to-detach-a-route-filter-from-an-expressroute-circuit"></a><a name="detach"></a>ExpressRoute 回線からルート フィルターをデタッチするには

ルート フィルターから回線をデタッチするには、その回線を右クリックし、 **[関連付け解除]** を選択します。

:::image type="content" source="./media/how-to-routefilter-portal/detach-route-filter.png" alt-text="[関連付け解除] アクションが強調表示されている [概要] ページを示すスクリーンショット":::


## <a name="clean-up-resources"></a>リソースをクリーンアップする

ルート フィルターを削除するには、 **[削除]** ボタンを選択します。 この操作を行う前に、ルート フィルターが確実にどの回線にも関連付けられていないようにします。

:::image type="content" source="./media/how-to-routefilter-portal/delete-route-filter.png" alt-text="ルート フィルターを削除する":::

## <a name="next-steps"></a>次の手順

ルーター構成のサンプルについては、以下を参照してください。

> [!div class="nextstepaction"]
> [ルーティングをセットアップして管理するためのルーター構成のサンプル](expressroute-config-samples-routing.md)
