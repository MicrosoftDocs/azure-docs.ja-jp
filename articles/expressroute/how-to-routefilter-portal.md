---
title: Microsoft ピアリングにルート フィルターを構成する:Azure ExpressRoute - ポータル | Microsoft Docs
description: この記事では、Azure portal を使って Microsoft ピアリングにルート フィルターを構成する方法について説明します。
services: expressroute
author: ganesr
ms.service: expressroute
ms.topic: article
ms.date: 09/26/2018
ms.author: ganesr
ms.custom: seodec18
ms.openlocfilehash: 0515b5e85c3bcf56f1f238620d6036d1be0bec7e
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/08/2018
ms.locfileid: "53104207"
---
# <a name="configure-route-filters-for-microsoft-peering-azure-portal"></a>Microsoft ピアリングにルート フィルターを構成する:Azure ポータル
> [!div class="op_single_selector"]
> * [Azure Portal](how-to-routefilter-portal.md)
> * [Azure PowerShell](how-to-routefilter-powershell.md)
> * [Azure CLI](how-to-routefilter-cli.md)
> 

ルート フィルターとは、Microsoft ピアリングでサポートされるサービスの一部だけを利用する手段です。 この記事の手順を通じて、ExpressRoute 回線にルート フィルターを構成し、管理することができます。

Dynamics 365 サービスと Office 365 サービス (Exchange Online、SharePoint Online、Skype for Business など) および Azure サービス (ストレージ、SQL DB など) には、Microsoft ピアリングを介してアクセスすることができます。 ExpressRoute 回線に Microsoft ピアリングを構成すると、これらのサービスに関連したすべてのプレフィックスが、確立された BGP セッションを通じてアドバタイズされます。 提供されているサービスをプレフィックスで識別するために、すべてのプレフィックスには BGP コミュニティ値がアタッチされます。 BGP コミュニティ値とサービスのマッピング一覧については、[BGP コミュニティ](expressroute-routing.md#bgp)に関するページを参照してください。

すべてのサービスに接続する必要がある場合、多数のプレフィックスが BGP を通じてアドバタイズされます。 このことによって、ネットワーク内のルーターによって管理されるルート テーブルのサイズは著しく増加します。 Microsoft ピアリング経由で提供されるサービスの一部しか利用する予定がない場合は、ルート テーブルのサイズを 2 とおりの方法で減らすことができます。 次のようにすることができます。

- BGP コミュニティにルート フィルターを適用して不要なプレフィックスを除外する。 この方法はネットワーク運用の標準的技法であり、多くのネットワークで広く使用されています。

- ルート フィルターを定義して ExpressRoute 回線に適用する。 ルート フィルターは、Microsoft ピアリング経由で利用する予定の一連のサービスを選択できる新しいリソースです。 ExpressRoute ルーターからは、ルート フィルターで識別されたサービスに属しているプレフィックスのリストだけが送信されます。

### <a name="about"></a>ルート フィルターについて

ExpressRoute 回線に Microsoft ピアリングが構成されると、Microsoft のエッジ ルーターは、(貴社または接続プロバイダーの) エッジ ルーターとの間に一対の BGP セッションを確立します。 貴社のネットワークにはルートが一切アドバタイズされません。 ネットワークに対するルート アドバタイズを有効にするには、ルート フィルターを関連付ける必要があります。

ExpressRoute 回線の Microsoft ピアリング経由で利用するサービスがルート フィルターによって識別されます。 これは、実質的には全 BGP コミュニティ値から成るホワイト リストです。 ルート フィルター リソースを定義して ExpressRoute 回線にアタッチすると、BGP コミュニティ値にマッピングされたすべてのプレフィックスが貴社のネットワークにアドバタイズされます。

その回線上の Office 365 サービスにルート フィルターをアタッチするには、ExpressRoute を経由して Office 365 サービスを利用することへの承認が必要となります。 ExpressRoute 経由で Office 365 サービスを利用することを承認されていない場合、ルート フィルターをアタッチすることはできません。 承認プロセスの詳細については、「[Office 365 向け Azure ExpressRoute](https://support.office.com/article/Azure-ExpressRoute-for-Office-365-6d2534a2-c19c-4a99-be5e-33a0cee5d3bd)」を参照してください。 Dynamics 365 サービスへの接続に事前の承認は一切必要ありません。

> [!IMPORTANT]
> 2017 年 8 月 1 日より前に構成された ExpressRoute 回線の Microsoft ピアリングでは、ルート フィルターが定義されていない場合でも、すべてのサービス プレフィックスが Microsoft ピアリングでアドバタイズされます。 2017 年 8 月 1 日以降に構成された ExpressRoute 回路の Microsoft ピアリングでは、ルート フィルターが回線にアタッチされるまで、プレフィックスはアドバタイズされません。
> 
> 

### <a name="workflow"></a>ワークフロー

Microsoft ピアリング経由でサービスに正しく接続するためには、次の構成手順を完了する必要があります。

- Microsoft ピアリングがプロビジョニングされたアクティブな ExpressRoute 回線を用意する。 その作業は、次の手順に従って実行できます。
  - あらかじめ [ExpressRoute 回線を作成](expressroute-howto-circuit-portal-resource-manager.md)し、接続プロバイダー経由で回線を有効にしておきます。 ExpressRoute 回線がプロビジョニングされて有効な状態になっている必要があります。
  - BGP セッションを直接管理する場合は、[Microsoft ピアリングを作成](expressroute-howto-routing-portal-resource-manager.md)します。 または、ご利用の回線に対する Microsoft ピアリングのプロビジョニングを接続プロバイダーに依頼します。

-  ルート フィルターを作成して構成する。
    - Microsoft ピアリング経由で利用するサービスを特定します。
    - それらのサービスに関連付けられている一連の BGP コミュニティ値を特定します。
    - それらの BGP コミュニティ値と合致した一連のプレフィックスを許可するルールを作成します。

-  ExpressRoute 回線にルート フィルターをアタッチする。

## <a name="before-you-begin"></a>開始する前に

構成を開始する前に、次の条件を満たす必要があります。

 - 構成を開始する前に、[前提条件](expressroute-prerequisites.md)と[ワークフロー](expressroute-workflows.md)を確認してください。

 - アクティブな ExpressRoute 回線が必要です。 手順に従って、[ExpressRoute 回線を作成](expressroute-howto-circuit-portal-resource-manager.md)し、接続プロバイダー経由で回線を有効にしてから続行してください。 ExpressRoute 回線がプロビジョニングされて有効な状態になっている必要があります。

 - アクティブな Microsoft ピアリングが必要です。 [ピアリング構成の作成と変更](expressroute-howto-routing-portal-resource-manager.md)に関するページの手順に従ってください。


## <a name="prefixes"></a>手順 1:一連のプレフィックスと BGP コミュニティ値を取得する

### <a name="1-get-a-list-of-bgp-community-values"></a>1.一連の BGP コミュニティ値を取得する

Microsoft ピアリング経由でアクセスできるサービスに関連付けられた BGP コミュニティ値は、「[ExpressRoute のルーティングの要件](expressroute-routing.md)」ページで確認できます。

### <a name="2-make-a-list-of-the-values-that-you-want-to-use"></a>2.使用する値をリストアップする

ルート フィルターで使用する BGP コミュニティ値をリストアップします。 たとえば Dynamics 365 サービスの BGP コミュニティ値は 12076:5040 です。

## <a name="filter"></a>手順 2:ルート フィルターとフィルター ルールを作成する

ルート フィルターに割り当てることができるルールは 1 つだけで、また "許可" タイプであることが必要です。 このルールに、一連の BGP コミュニティ値を関連付けることができます。

### <a name="1-create-a-route-filter"></a>1.ルート フィルターを作成する
新しいリソースを作成するオプションを選択して、ルート フィルターを作成できます。 次の図に示すように、**[リソースの作成]** > **[ネットワーク]** > **[RouteFilter]** をクリックします。

![ルート フィルターを作成する](./media/how-to-routefilter-portal/CreateRouteFilter1.png)

ルート フィルターは、リソース グループに配置する必要があります。 

![ルート フィルターを作成する](./media/how-to-routefilter-portal/CreateRouteFilter.png)

### <a name="2-create-a-filter-rule"></a>2.フィルター ルールを作成する

ルート フィルターの [管理規則] タブを選択することで、ルールを追加および更新できます。

![ルート フィルターを作成する](./media/how-to-routefilter-portal/ManageRouteFilter.png)


ドロップダウン リストから接続するサービスを選択し、接続が完了したときにルールを保存できます。

![ルート フィルターを作成する](./media/how-to-routefilter-portal/AddRouteFilterRule.png)


## <a name="attach"></a>手順 3:ルート フィルターを ExpressRoute 回線にアタッチする

[回線の追加] ボタンを選択し、ドロップダウン リストから、ExpressRoute 回線を選択することで、ルート フィルターを回線にアタッチできます。

![ルート フィルターを作成する](./media/how-to-routefilter-portal/AddCktToRouteFilter.png)

接続プロバイダーによって ExpressRoute 回線のピアリングが構成される場合は、[回線の追加] ボタンを選択する前に ExpressRoute 回線ブレードから回線を更新します。

![ルート フィルターを作成する](./media/how-to-routefilter-portal/RefreshExpressRouteCircuit.png)

## <a name="tasks"></a>一般的なタスク

### <a name="getproperties"></a>ルート フィルターのプロパティを取得するには

ポータルでリソースを開くときに、ルート フィルターのプロパティを表示できます。

![ルート フィルターを作成する](./media/how-to-routefilter-portal/ViewRouteFilter.png)


### <a name="updateproperties"></a>ルート フィルターのプロパティを更新するには

[管理ルール] ボタンを選択することで、回線にアタッチした BGP コミュニティ値の一覧を更新できます。


![ルート フィルターを作成する](./media/how-to-routefilter-portal/ManageRouteFilter.png)

![ルート フィルターを作成する](./media/how-to-routefilter-portal/AddRouteFilterRule.png) 


### <a name="detach"></a>ExpressRoute 回線からルート フィルターをデタッチするには

ルート フィルターから回線をデタッチするには、その回線を右クリックし、[関連付け解除] をクリックします。

![ルート フィルターを作成する](./media/how-to-routefilter-portal/DetachRouteFilter.png) 


### <a name="delete"></a>ルート フィルターを削除するには

ルート フィルターを削除するには、[削除] ボタンを選択します。 

![ルート フィルターを作成する](./media/how-to-routefilter-portal/DeleteRouteFilter.png) 

## <a name="next-steps"></a>次の手順

ExpressRoute の詳細については、「 [ExpressRoute のFAQ](expressroute-faqs.md)」をご覧ください。