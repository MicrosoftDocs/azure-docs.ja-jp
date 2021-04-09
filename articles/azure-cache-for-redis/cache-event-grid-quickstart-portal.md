---
title: 'クイックスタート: Azure portal を使用して Azure Cache for Redis のイベントを Web エンドポイントにルーティングする'
description: Azure Event Grid を使用して Azure Cache for Redis のイベントをサブスクライブし、それらのイベントを Webhook に送信して Web アプリケーションで処理します。
ms.date: 1/5/2021
ms.topic: quickstart
ms.service: cache
author: curib
ms.author: cauribeg
ms.openlocfilehash: 5bdd6b0e6f97f7e5a738ab17d68282cf402004b0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "99056484"
---
# <a name="quickstart-route-azure-cache-for-redis-events-to-web-endpoint-with-the-azure-portal"></a>クイックスタート: Azure portal を使用して Azure Cache for Redis のイベントを Web エンドポイントにルーティングする

Azure Event Grid は、クラウドのイベント処理サービスです。 このクイックスタートでは、Azure portal から Azure Cache for Redis インスタンスを作成してそのイベントをサブスクライブし、イベントをトリガーして結果を表示します。 通常は、イベント データを処理し、アクションを実行するエンドポイントにイベントを送信します。 ただし単純化するために、このクイックスタートではメッセージを収集して表示する Web アプリにイベントを送信します。 

[!INCLUDE [quickstarts-free-trial-note.md](../../includes/quickstarts-free-trial-note.md)]

最後に、イベント データが Web アプリに送信されたことを確認します。

:::image type="content" source="media/cache-event-grid-portal/event-grid-scaling.png" alt-text="Azure Event Grid ビューアーで JSON 形式のスケーリングを表示する。":::

## <a name="create-an-azure-cache-for-redis-cache-instance"></a>Azure Cache for Redis キャッシュ インスタンスを作成する 

[!INCLUDE [redis-cache-create](../../includes/redis-cache-create.md)]

## <a name="create-a-message-endpoint"></a>メッセージ エンドポイントの作成

キャッシュ インスタンスのイベントをサブスクライブする前に、イベント メッセージのエンドポイントを作成しましょう。 通常、エンドポイントは、イベント データに基づくアクションを実行します。 このクイックスタートを簡素化するために、イベント メッセージを表示する[構築済みの Web アプリ](https://github.com/Azure-Samples/azure-event-grid-viewer)をデプロしします。 デプロイされたソリューションには、App Service プラン、App Service Web アプリ、および GitHub からのソース コードが含まれています。

1. GitHub の README で **[Deploy to Azure]\(Azure へのデプロイ\)** を選択して、ソリューションをサブスクリプションにデプロイします。 

:::image type="content" source="media/cache-event-grid-portal/deploy-to-azure.png" alt-text="[Deploy to Azure]\(Azure へのデプロイ\) ボタン。":::

2. **[カスタム デプロイ]** ページで、次の手順を実行します。 
    1. **[リソース グループ]** で、キャッシュ インスタンスの作成時に作成したリソース グループを選択します。 チュートリアルの完了後は、リソース グループを削除して容易にクリーンアップすることができます。  
    2. **[サイト名]** に、Web アプリの名前を入力します。
    3. Web アプリのホストに使用する App Service プランの名前を **[ホスティング プラン名]** に入力します。
    4. **[上記の使用条件に同意する]** のチェック ボックスをオンにします。 
    5. **[購入]** を選択します。 
    
    | 設定      | 推奨値  | 説明 |
    | ------------ |  ------- | -------------------------------------------------- |
    | **サブスクリプション** | ドロップダウンで、ご自身のサブスクリプションを選択します。 | この Web アプリを作成するサブスクリプション。 | 
    | **リソース グループ** | ドロップ ダウンでリソース グループを選択するか、 **[新規作成]** を選択し、新しいリソース グループの名前を入力します。 | すべてのアプリ リソースを 1 つのリソース グループに配置することで、それらをまとめて簡単に管理または削除できます。 | 
    | **サイト名** | Web アプリの名前を入力します。 | この値は空にできません。 | 
    | **ホスティング プラン名** | Web アプリをホストするために使用する App Service プランの名前を入力します。 | この値は空にできません。 | 

1. ポータルでアラート (ベルのアイコン) を選択し、 **[リソース グループに移動]** を選択します。 

    :::image type="content" source="media/cache-event-grid-portal/deployment-notification.png" alt-text="Azure portal デプロイ通知。":::

4. **[リソース グループ]** ページのリソースの一覧で、作成した Web アプリを選択します。 この一覧には、App Service プランとキャッシュ インスタンスも表示されます。 

5. Web アプリの **[App Service]** ページで、Web サイトに移動するための URL を選択します。 この URL は、`https://<your-site-name>.azurewebsites.net` 形式になっている必要があります。

6. サイトは表示されますが、イベントはまだ送信されていないことを確認します。

    :::image type="content" source="media/cache-event-grid-portal/blank-event-grid-viewer.png" alt-text="空の Event Grid ビューアー サイト。":::

[!INCLUDE [event-grid-register-provider-portal.md](../../includes/event-grid-register-provider-portal.md)]

## <a name="subscribe-to-the-azure-cache-for-redis-instance"></a>Azure Cache for Redis インスタンスをサブスクライブする

この手順では、トピックをサブスクライブすることによって、追跡するイベントとそれらのイベントの送信先を Event Grid に伝えます。

1. ポータルで、先ほど作成したキャッシュ インスタンスに移動します。 
2. **[Azure Cache for Redis]** ページの左側のメニューで **[イベント]** を選択します。 
3. **[webhook]** を選択します。 エンドポイントの Web hook を使用して、ビューアー アプリにイベントを送信します。 

     :::image type="content" source="media/cache-event-grid-portal/event-grid-web-hook.png" alt-text="Azure portal の [イベント] ページ。":::

4. **[イベント サブスクリプションの作成]** ページで、次の値を入力します。 

    | 設定      | 推奨値  | 説明 |
    | ------------ |  ------- | -------------------------------------------------- |
    | **名前** | イベント サブスクリプションの名前を入力します。 | 値の長さは 3 から 64 文字にする必要があります。 使用できるのは、英字、数字、ダッシュのみです。 | 
    | **イベントの種類** | 宛先にプッシュするイベントの種類をドロップ ダウンから選択します。 このクイックスタートでは、キャッシュ インスタンスをスケーリングします。 | パッチ適用、スケーリング、インポート、エクスポートを選択できます。 | 
    | **エンドポイントの種類** | **[webhook]** を選択します。 | イベントを受信するイベント ハンドラー。 | 
    | **エンドポイント** | **[エンドポイントの選択]** をクリックし、Web アプリの URL を入力して、ホーム ページの URL に `api/updates` を追加し (例: `https://cache.azurewebsites.net/api/updates`)、 **[選択の確認]** を選択します。 | これは先ほど作成した Web アプリの URL です。 | 

5. 次に、 **[イベント サブスクリプションの作成]** ページの **[作成]** を選択して、イベント サブスクリプションを作成します。 

6. Web アプリをもう一度表示し、その Web アプリにサブスクリプションの検証イベントが送信されたことに注目します。 目のアイコンを選択してイベント データを展開します。 Event Grid は検証イベントを送信するので、エンドポイントはイベント データを受信することを確認できます。 Web アプリには、サブスクリプションを検証するコードが含まれています。

    :::image type="content" source="media/cache-event-grid-portal/subscription-event.png" alt-text="Azure Event Grid ビューアー。":::

## <a name="send-an-event-to-your-endpoint"></a>エンドポイントへのイベントの送信

では、イベントをトリガーして、Event Grid がメッセージをエンドポイントに配信するようすを見てみましょう。 Azure Cache for Redis インスタンスをスケーリングします。

1. Azure portal で、Azure Cache for Redis インスタンスに移動し、左側のメニューの **[スケール]** を選択します。

1. **[スケール]** ページから希望の価格レベルを選択し、 **[選択]** をクリックします。 

    別の価格レベルにスケーリングできますが、次のような制約があります。
    
    * 上位の価格レベルから下位の価格レベルにスケーリングすることはできません。
      * **Premium** キャッシュから **Standard** または **Basic** キャッシュにスケールすることはできません。
      * **Standard** キャッシュから **Basic** キャッシュにスケールすることはできません。
    * **Basic** キャッシュから **Standard** キャッシュにスケールすることはできますが、同時にサイズを変更することはできません。 サイズを変更する必要がある場合、後続のスケーリング操作でサイズを変更できます。
    * **Basic** キャッシュから直接 **Premium** キャッシュにスケールすることはできません。 まず、1 回のスケーリング操作で **Basic** から **Standard** にスケーリングし、その後の操作で **Standard** から **Premium** にスケーリングします。
    * **C0 (250 MB)** サイズにそれより大きなサイズからスケールダウンすることはできません。
 
    キャッシュを新しい価格レベルにスケーリングするときに、 **[Azure Cache for Redis]** ブレードで状態が **[拡大中]** と表示されます。 スケーリングが完了すると、状態が **[拡大中]** から **[実行中]** に変わります。

1. 以上でイベントがトリガーされ、そのメッセージが、Event Grid によってサブスクライブ時に構成したエンドポイントに送信されました。 メッセージは JSON 形式であり、1 つまたは複数のイベントの配列が含まれています。 次の例の JSON メッセージには、1 つのイベントの配列が含まれています。 Web アプリを表示して、**ScalingCompleted** イベントが受信されたことを確認します。 

    :::image type="content" source="media/cache-event-grid-portal/event-grid-scaling.png" alt-text="Azure Event Grid ビューアーで JSON 形式のスケーリングを表示する。":::

## <a name="clean-up-resources"></a>リソースをクリーンアップする

引き続きこのイベントを使用する場合は、このクイックスタートで作成したリソースをクリーンアップしないでください。 それ以外の場合は、このクイックスタートで作成したリソースを削除してください。

リソース グループを選択し、 **[リソース グループの削除]** を選択します。

## <a name="next-steps"></a>次のステップ

カスタム トピックを作成し、イベントをサブスクライブする方法がわかったら、Event Grid でできることについて、さらに情報を収集しましょう。

- [Azure Cache for Redis イベントへの対応](cache-event-grid.md)
- [Event Grid について](../event-grid/overview.md)

