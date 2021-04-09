---
title: 'クイックスタート: Azure portal を使用してルート サーバーを作成および構成する'
description: このクイックスタートでは、Azure portal を使用して、ルート サーバーを作成して構成する方法について学習します。
services: route-server
author: duongau
ms.service: route-server
ms.topic: quickstart
ms.date: 03/03/2021
ms.author: duau
ms.openlocfilehash: f76c48af4f5ebc8013daad457f9973cf7792c7c6
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102547996"
---
# <a name="quickstart-create-and-configure-route-server-using-the-azure-portal"></a>クイックスタート: Azure portal を使用してルート サーバーを作成および構成する

この記事は、Azure portal を使用して、仮想ネットワーク内のネットワーク仮想アプライアンス (NVA) とピアリングするように Azure Route Server を構成するのに役立ちます。 Azure Route Server では、NVA からルートを学習し、仮想ネットワーク内の仮想マシン上でそれらをプログラムします。 また、Azure Route Server は NVA に仮想ネットワーク ルートをアドバタイズします。 詳細については、[Azure Route Server](overview.md) に関するページを参照してください。

> [!IMPORTANT]
> Azure Route Server (プレビュー) は現在、パブリック プレビュー段階にあります。
> このプレビュー バージョンはサービス レベル アグリーメントなしで提供されています。運用環境のワークロードに使用することはお勧めできません。 特定の機能はサポート対象ではなく、機能が制限されることがあります。
> 詳しくは、[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)に関するページをご覧ください。

## <a name="prerequisites"></a>前提条件

* アクティブなサブスクリプションが含まれる Azure アカウント。 [無料でアカウントを作成できます](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
* [Azure Route Server のサービスの制限](route-server-faq.md#limitations)を確認します。

## <a name="create-a-route-server"></a>ルート サーバーを作成する

### <a name="sign-in-to-your-azure-account-and-select-your-subscription"></a>Azure アカウントにサインインしてサブスクリプションを選択します。

ブラウザーから [Azure ポータル](https://portal.azure.com) に移動し、Azure アカウントでサインインします。

### <a name="create-a-route-server"></a>ルート サーバーを作成する

1. https://aka.ms/routeserver にアクセスします。

1. **[+ Create new route server]\(+ 新しいルート サーバーの作成\)** を選択します。

    :::image type="content" source="./media/quickstart-configure-route-server-portal/route-server-landing-page.png" alt-text="ルート サーバーのランディング ページのスクリーンショット。"::: 

1. **[Create a Route Server]\(ルート サーバーの作成\)** ページで、必要な情報を入力または選択します。

    :::image type="content" source="./media/quickstart-configure-route-server-portal/create-route-server-page.png" alt-text="ルート サーバーの作成ページのスクリーンショット。":::     

    | 設定 | 値 |
    |----------|-------|
    | サブスクリプション | ルート サーバーのデプロイに使用する Azure サブスクリプションを選択します。 |
    | リソース グループ | ルート サーバーを作成するリソース グループを選択します。 既存のリソース グループがない場合は、新しいものを作成できます。 |
    | 名前 | ルート サーバーの名前を入力します。 |
    | リージョン | ルート サーバーを作成するリージョンを選択します。 以前に作成した仮想ネットワークと同じリージョンを選択すると、ドロップダウンにその仮想ネットワークが表示されます。 |
    | Virtual Network | ルート サーバーを作成する仮想ネットワークを選択します。 新しい仮想ネットワークを作成することも、既存の仮想ネットワークを使用することもできます。 既存の仮想ネットワークを使用する場合は、ルート サーバーのサブネット要件に対応するために、既存の仮想ネットワークに少なくとも /27 サブネットの十分な空間があることを確認してください。 ドロップダウンにお使いの仮想ネットワークが表示されない場合は、適切なリソース グループまたはリージョンを選択していることを確認してください。 |
    | Subnet | 仮想ネットワークを作成または選択すると、[サブネット] フィールドが表示されます。 このサブネットは、ルート サーバー専用です。 **[Manage subnet configuration]\(サブネット構成の管理\)** を選択し、Azure Route Server のサブネットを作成します。 **[+ サブネット]** を選択し、これらのガイドラインに従ってサブネットを作成します。</br><br>- サブネットの名前は *RouteServerSubnet* にする必要があります。</br><br>- サブネットは、少なくとも /27 以上である必要があります。</br> |

1. **[Review + create]\(確認と作成\)** を選択し、概要を確認して、 **[作成]** を選択します。 

    > [!NOTE]
    > ルート サーバーのデプロイには約 20 分かかります。

## <a name="set-up-peering-with-nva"></a>NVA とのピアリングを設定する

このセクションは、お使いの NVA との BGP ピアリングを構成するのに役立ちます。

1. Azure portal で [[Route Server]\(ルート サーバー\)](https://aka.ms/routeserver) に移動し、構成するルート サーバーを選択します。

    :::image type="content" source="./media/quickstart-configure-route-server-portal/select-route-server.png" alt-text="ルート サーバーの一覧のスクリーンショット。"::: 

1. 左側のナビゲーション パネルの *[設定]* で、 **[ピア]** を選択します。 次に、 **[+ 追加]** を選択し、新しいピアを追加します。

    :::image type="content" source="./media/quickstart-configure-route-server-portal/peers-landing-page.png" alt-text="ピアのランディング ページのスクリーンショット。"::: 

1. お使いの NVA ピアに関する次の情報を入力します。

    :::image type="content" source="./media/quickstart-configure-route-server-portal/add-peer-page.png" alt-text="ピアの追加ページのスクリーンショット。":::

    | 設定 | 値 |
    |----------|-------|
    | 名前 | お使いのルート サーバーと NVA の間のピアリングの名前を指定します。 |
    | ASN |  お使いの NVA の自律システム番号 (ASN) を入力します。 |
    | [IPv4 アドレス] | ルート サーバーが BGP を確立するために通信する NVA の IP アドレスを入力します。 |

1. **[追加]** を選択して、このピアを追加します。

## <a name="complete-the-configuration-on-the-nva"></a>NVA の構成を完了する

お使いの NVA での構成を完了して BGP セッションを確立するには、Azure Route Server のピア IP と ASN が必要です。 この情報は、お使いのルート サーバーの概要ページから取得できます。

:::image type="content" source="./media/quickstart-configure-route-server-portal/route-server-overview.png" alt-text="ルート サーバーの概要ページのスクリーンショット。":::

## <a name="configure-route-exchange"></a>ルート交換を構成する

ExpressRoute ゲートウェイや VPN ゲートウェイがあり、それらがルート サーバーとルートを交換するようにする場合は、ルート交換を有効にできます。

1. Azure portal で [[Route Server]\(ルート サーバー\)](https://aka.ms/routeserver) に移動し、構成するルート サーバーを選択します。

1. 左側のナビゲーション パネルの *[設定]* で、 **[構成]** を選択します。

1. **[ブランチ間]** 設定で **[Enable]\(有効\)** を選択し、 **[保存]** を選択します。

    :::image type="content" source="./media/quickstart-configure-route-server-portal/enable-route-exchange.png" alt-text="ルート交換を有効にする方法のスクリーンショット。":::

## <a name="clean-up-resources"></a>リソースをクリーンアップする

Azure Route Server が不要になった場合は、概要ページの **[削除]** を選択して、ルート サーバーのプロビジョニングを解除します。

:::image type="content" source="./media/quickstart-configure-route-server-portal/delete-route-server.png" alt-text="ルート サーバーを削除する方法のスクリーンショット。":::

## <a name="next-steps"></a>次のステップ

Azure Route Server を作成した後、Azure Route Server が ExpressRoute および VPN Gateway とどのように連携するかについては、以下を参照してください。 

> [!div class="nextstepaction"]
> [Azure ExpressRoute と Azure VPN のサポート](expressroute-vpn-support.md)
