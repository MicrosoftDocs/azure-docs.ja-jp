---
title: Azure portal を使用して HTTP から HTTPS にリダイレクトする Front Door を作成する
description: Azure portal を使用して、HTTP から HTTPS にトラフィックがリダイレクトされる Front Door を作成する方法について説明します。
services: front-door
author: duongau
ms.service: frontdoor
ms.topic: how-to
ms.date: 09/30/2020
ms.author: duau
ms.openlocfilehash: 19908b3cba63bc76a205097ef8d16e612d58503b
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "91626644"
---
# <a name="create-a-front-door-with-http-to-https-redirection-using-the-azure-portal"></a>Azure portal を使用して HTTP から HTTPS にリダイレクトする Front Door を作成する

Azure portal を使用して、TLS 終端の証明書で [Front Door を作成](quickstart-create-front-door.md)できます。 ルーティング規則を使用して、HTTP トラフィックを HTTPS にリダイレクトします。

## <a name="create-a-front-door-with-an-existing-web-app-resource"></a>既存の Web アプリ リソースを使用して Front Door を作成する

1. Azure Portal [https://portal.azure.com](https://portal.azure.com) にサインインします。

1. Azure Portal の左上隅にある **[リソースの作成]** を選択します。

1. 検索バーを使用して **Front Door** を検索し、リソースの種類を見つけたら、 **[作成]** を選択します。

1. *サブスクリプション* を選択して、既存のリソース グループを使用するか、新しいものを作成します。 **[次へ]** をクリックして構成タブに進みます。

    > [!NOTE]
    > UI で指定するよう求められる場所は、このリソース グループ専用です。 Front Door 構成は、[Azure Front Door の POP の場所](front-door-faq.md#what-are-the-pop-locations-for-azure-front-door)全体にわたってデプロイされます。

    :::image type="content" source="./media/front-door-url-redirect/front-door-create-basics.png" alt-text="新規 Front Door の基本を構成する":::

1. Front Door の構成は、3 つの手順、つまり既定のフロントエンド ホストの追加、バックエンド プールへのバックエンドの追加、およびフロントエンド ホストのルーティング動作をマップするルーティング規則の作成によって行われます。 _フロントエンド ホスト_ の ' **+** ' アイコンを選択して、フロントエンド ホストを作成します。

    :::image type="content" source="./media/front-door-url-redirect/front-door-designer.png" alt-text="Front Door 構成デザイナー":::

1. Front Door の既定のフロントエンド ホストのグローバルに一意の名前を入力します。 **[追加]** を選択して、次の手順に進みます。

    :::image type="content" source="./media/front-door-url-redirect/front-door-create-frontend-host.png" alt-text="フロントエンド ホストを追加する":::

### <a name="create-backend-pool"></a>バックエンド プールの作成

1. _[バックエンド プール]_ の ' **+** ' アイコンを選択して、バックエンド プールを作成します。 バックエンド プールの名前を指定し、 **[バックエンドの追加]** を選択します。

    :::image type="content" source="./media/front-door-url-redirect/front-door-designer-backend-pool.png" alt-text="Front Door 構成デザイナー バックエンド プール":::

1. [バックエンド ホストの種類] で _[App service]_ を選択します。 お使いの Web アプリがホストされているサブスクリプションを選択し、**バックエンド ホスト名** 用のドロップダウンから特定の Web アプリを選択します。

    :::image type="content" source="./media/front-door-url-redirect/front-door-create-backend-pool.png" alt-text="バックエンド プールにバックエンドを追加する":::

1. **[追加]** を選択してバックエンドを保存し、もう一度 **[追加]** を選択してバックエンド プールの構成を保存します。 

## <a name="create-http-to-https-redirect-rule"></a>HTTP から HTTPS へのリダイレクト ルールの作成

1. *[ルーティング規則]* で ' **+** ' アイコンを選択してルートを作成します。 ルートの名前 ('HttpToHttpsRedirect' など) を指定し、 *[受け入れ済みのプロトコル]* フィールドを **[HTTP のみ]** に設定します。 適切な *[フロントエンド/ドメイン]* が選択されていることを確認します。  

    :::image type="content" source="./media/front-door-url-redirect/front-door-designer-routing-rule.png" alt-text="Front Door 構成デザイナー ルーティング規則":::

1. *[ルートの詳細]* セクションで、 *[ルートの種類]* を **[リダイレクト]** に設定します。 *[リダイレクトの種類]* が **[検出 (302)]** に設定され、 *[リダイレクト プロトコル]* が **[HTTPS のみ]** に設定されていることを確認します。 

    :::image type="content" source="./media/front-door-url-redirect/front-door-redirect-config-example.png" alt-text="HTTP から HTTPS へのリダイレクト ルートを追加する":::

1. **[追加]** を選択して、HTTP から HTTPS へのリダイレクト用のルーティング規則を保存します。

## <a name="create-forwarding-rule"></a>転送ルールの作成

1. HTTPS トラフィックを処理するための別のルーティング規則を追加します。 *[ルーティング規則]* の ' **+** ' 記号を選択し、ルートの名前 ('DefaultForwardingRoute' など) を指定します。 次に *[受け入れ済みのプロトコル]* フィールドを **[HTTPS のみ]** に設定します。 適切な *[フロントエンド/ドメイン]* が選択されていることを確認します。

1. [ルートの詳細] セクションで、 *[ルートの種類]* を **[転送]** に設定します。 適切なバックエンド プールが選択されており、 *[転送プロトコル]* が **[HTTPS のみ]** に設定されていることを確認します。 

    :::image type="content" source="./media/front-door-url-redirect/front-door-forward-route-example.png" alt-text="HTTPS トラフィックの転送ルートを追加する" border="false":::

1. **[追加]** を選択して、要求の転送用のルーティング規則を保存します。

1. **[レビュー + 作成]** 、 **[作成]** の順に選択し、Front Door プロファイルを作成します。 作成されたリソースに移動します。

## <a name="next-steps"></a>次のステップ

- [Front Door のしくみ](front-door-routing-architecture.md)について学習します。
- [Front Door の URL リダイレクト](front-door-url-redirect.md)について詳しく学習します。
