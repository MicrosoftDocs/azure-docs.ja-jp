---
title: Private Link を使用して Azure Front Door Premium を Web アプリの配信元に接続する
titleSuffix: Azure Private Link
description: Azure Front Door Premium を webapp にプライベートに接続する方法について説明します。
services: frontdoor
author: duongau
ms.service: frontdoor
ms.topic: how-to
ms.date: 02/18/2021
ms.author: tyao
ms.openlocfilehash: 805c3ba0360fcffe2bfd4217c0ef625fe61e5d64
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/04/2021
ms.locfileid: "102030581"
---
# <a name="connect-azure-front-door-premium-to-a-web-app-origin-with-private-link"></a>Private Link を使用して Azure Front Door Premium を Web アプリの配信元に接続する

この記事では、Azure Private Link サービスを使用して Web アプリにプライベートに接続するように Azure Front Door Premium SKU を構成する方法について説明します。

## <a name="prerequisites"></a>前提条件

* アクティブなサブスクリプションが含まれる Azure アカウント。 [無料でアカウントを作成できます](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
* 配信元の Web サーバー用に [Private Link](../../private-link/create-private-link-service-portal.md) サービスを作成します。

> [!Note]
> プライベート エンドポイントは、PremiumV2 レベル、PremiumV3 レベルの Windows Web アプリ、Linux Web アプリ、Azure Functions Premium プラン (エラスティック Premium プランとも呼ばれます) に対するパブリック リージョンで利用できます。

## <a name="sign-in-to-azure"></a>Azure へのサインイン

[Azure portal](https://portal.azure.com) にサインインします。

## <a name="enable-private-link-to-a-web-app-in-azure-front-door-premium"></a>Azure Front Door Premium で Web アプリへの Private Link を有効にする
 
このセクションでは、Private Link サービスを、Azure Front Door のプライベート ネットワークに作成されたプライベート エンドポイントにマップします。 

1. Azure Front Door Premium プロファイル内の *[設定]* で、 **[配信元グループ]** を選択します。

1. Private Link を有効にする Web アプリの配信元が含まれている配信元グループを選択します。

1. **[+ Add an origin]\(+ 配信元の追加\)** を選択して新しい Web アプリの配信元を追加するか、以前に作成した Web アプリの配信元を一覧から選択します。

    :::image type="content" source="../media/how-to-enable-private-link-web-app/private-endpoint-web-app.png" alt-text="Web アプリへのプライベート リンクを有効にするスクリーンショット。":::

1. **[Azure リソースの選択]** で、 **[In my directory]\(マイ ディレクトリ内\)** を選択します。 次の設定を選択または入力して、Azure Front Door Premium でプライベートに接続するサイトを構成します。

    | 設定 | 値 |
    | ------- | ----- |
    | リージョン | 配信元と同じか最も近いリージョンを選択します。 |
    | リソースの種類 | **[Microsoft.Web/sites]** を選択します。 |
    | リソース | **myPrivateLinkService** を選択します。 |
    | ターゲット サブリソース | sites |
    | 要求メッセージ | メッセージをカスタマイズするか、既定値を選択します。 |

1. 次に、 **[追加]** を選択して構成を保存します。

## <a name="approve-azure-front-door-premium-private-endpoint-connection-from-web-app"></a>Web アプリからからの Azure Front Door Premium プライベート エンドポイント接続を承認する

1. 最後のセクションで、Private Link を構成する Web アプリにアクセスします。 **[設定]** で、 **[ネットワーク]** を選択します。

1. **[Networking]\(ネットワーク\)** で、 **[Configure your private endpoint connections]\(プライベート エンドポイント接続の構成\)** を選択します。

    :::image type="content" source="../media/how-to-enable-private-link-web-app/web-app-configure-endpoint.png" alt-text="Web アプリのネットワーク設定のスクリーンショット。":::

1. Azure Front Door Premium から "*保留中*" のプライベート エンドポイント要求を選択し、 **[承認]** を選択します。

    :::image type="content" source="../media/how-to-enable-private-link-web-app/private-endpoint-pending-approval.png" alt-text="保留中のプライベート エンドポイント要求のスクリーンショット。":::

1. 承認されると、次のスクリーンショットのようになります。 接続が完全に確立されるまで数分かかります。 これで、Azure Front Door Premium から Web アプリにアクセスできるようになりました。 プライベート エンドポイントが有効になると、パブリック インターネットから Web アプリへの直接アクセスが無効になります。

    :::image type="content" source="../media/how-to-enable-private-link-web-app/private-endpoint-approved.png" alt-text="承認されたエンドポイント要求のスクリーンショット。":::

## <a name="next-steps"></a>次のステップ

[Azure Web アプリでの Private Link サービス](../../app-service/networking/private-endpoint.md)について学習します。
