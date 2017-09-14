---
title: "条件を追加してワークフローを開始する - Azure Logic Apps | Microsoft Docs"
description: "条件付きロジック、トリガー、アクション、およびパラメーターを追加することで Azure Logic Apps でのワークフローの実行を制御します。"
author: stepsic-microsoft-com
manager: anneta
editor: 
services: logic-apps
documentationcenter: 
ms.assetid: e4e24de4-049a-4b3a-a14c-3bf3163287a8
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/28/2017
ms.author: LADocs; stepsic
ms.translationtype: Human Translation
ms.sourcegitcommit: 7c69630688e4bcd68ab3b4ee6d9fdb0e0c46d04b
ms.openlocfilehash: e632c48ed31e82536db55a9c54438bece0c38fd4
ms.contentlocale: ja-jp
ms.lasthandoff: 06/24/2017


---
# <a name="use-logic-apps-features"></a>Logic Apps の機能を使用する

[前のトピック](../logic-apps/logic-apps-create-a-logic-app.md)では、初めてのロジック アプリを作成しました。 ロジック アプリのワークフローを制御するために、実行するロジック アプリのさまざまなパスと、配列、コレクション、バッチ内のデータを処理する方法を指定できます。 ロジック アプリ ワークフローにこれらの要素を含める必要があります。

* 条件と [switch ステートメント](../logic-apps/logic-apps-switch-case.md)を使用すると、特定の条件を満たしているかどうかに基づいて、ロジック アプリで異なるアクションを実行できます。

* [ループ](../logic-apps/logic-apps-loops-and-scopes.md)を使用すると、ロジック アプリでステップを繰り返し実行できます。 たとえば、**For_each** ループを使用して、1 つの配列に対してアクションを繰り返すことができます。 また、**Until** ループを使用すると、条件を満たすまでアクションを繰り返すことができます。

* [スコープ](../logic-apps/logic-apps-loops-and-scopes.md)を使用すると、一連のアクションをグループ化し、たとえば例外処理を実装することができます。

* [分割処理](../logic-apps/logic-apps-loops-and-scopes.md)で **SplitOn** コマンドを使用すると、ロジック アプリで配列内の項目に別のワークフローを開始することができます。

このトピックでは、ロジック アプリを構築するためのその他の概念を紹介します。

* 既存のロジック アプリを編集するためのコード ビュー
* ワークフローを開始するためのオプション

## <a name="conditions-run-steps-only-after-meeting-a-condition"></a>条件: 条件を満たした後にのみステップを実行する

ロジック アプリで、データが特定の条件を満たした場合にのみステップを実行するには、ワークフロー内のデータを特定のフィールドまたは値と比較する条件を追加します。

たとえば、Web サイトの RSS フィードへの投稿を通知する電子メールが多すぎるロジック アプリがあるとします。 新しい投稿が特定のカテゴリに属する場合にのみロジック アプリから電子メールを送信するように、条件を追加することができます。

1. [Azure ポータル](https://portal.azure.com)のロジック アプリ デザイナーでロジック アプリを選択して開きます。

2. 必要なワークフローの場所に条件を追加します。 

   ロジック アプリ ワークフローの既存のステップ間に条件を追加するには、条件を追加する矢印にポインターを移動します。 
   **プラス記号** (**+**) を選択し、**[条件の追加]** を選択します。 For example:

   ![ロジック アプリに条件を追加する](./media/logic-apps-use-logic-app-features/add-condition.png)

   > [!NOTE]
   > 現在のワークフローの末尾に条件を追加する場合は、ロジック アプリの下部にある **[+ 新しいステップ]** を選択します。

3. 次に条件を定義します。 評価するソース フィールド、実行する操作、およびターゲットの値またはフィールドを指定します。 既存のフィールドを条件に追加するには、**[Add dynamic content list]\(動的コンテンツ リストの追加\)** から選択します。

   For example:

   ![基本モードで条件を編集する](./media/logic-apps-use-logic-app-features/edit-condition-basic-mode.png)

   完成した条件を次に示します。

   ![完成した条件](./media/logic-apps-use-logic-app-features/edit-condition-basic-mode-2.png)

   > [!TIP]
   > コードで条件を定義するには、**[詳細設定モードで編集]** を選択します。 For example:
   > 
   > ![コードで条件を編集する](./media/logic-apps-use-logic-app-features/edit-condition-advanced-mode.png)

4. **IF YES** と **IF NO** の下に、条件を満たすかどうかに基づいて実行するステップを追加します。

   For example:

   ![YES および NO のパスの条件](./media/logic-apps-use-logic-app-features/condition-yes-no-path.png)

   > [!TIP]
   > 既存のアクションを **IF YES** と **IF NO** のパスにドラッグできます。

5. 完了したら、ロジック アプリを保存します。

投稿が条件を満たしている場合にのみ、電子メールが送信されるようになります。

## <a name="repeat-actions-over-a-list-with-foreach"></a>forEach を使用してリストに対してアクションを繰り返す

ForEach ループでは、アクションを繰り返す配列を指定します。 配列ではない場合、フローは失敗します。 たとえば、メッセージの配列を出力する action1 があり、各メッセージを送信する場合は、アクションのプロパティに `forEach : "@action('action1').outputs.messages"` のような forEach ステートメントを組み込むことができます。

## <a name="edit-the-code-definition-for-a-logic-app"></a>ロジック アプリのコード定義を編集する

Logic App デザイナーがある場合でも、ロジック アプリを定義するコードを直接編集することができます。

1. コマンド バーで、**[コード ビュー]** を選択します。

    全画面のエディターが開き、編集した定義が表示されます。

    ![[コード ビュー]](media/logic-apps-use-logic-app-features/codeview.png)

    テキスト エディターで、同じロジック アプリ内やロジック アプリ間で任意の数のアクションをコピーして貼り付けることができます。 
    定義からセクション全体を追加または削除することも簡単です。他のユーザーと定義を共有することもできます。

2. 編集内容を保存するには、**[保存]** を選択します。

## <a name="parameters"></a>parameters

パラメーターなどの一部の Logic App 機能は、コード ビューでのみ使用できます。 パラメーターを使用すると、ロジック アプリ全体にわたって簡単に値を再利用できます。 たとえば、いくつかの操作で使用する電子メール アドレスがある場合、その電子メール アドレスをパラメーターとして定義する必要があります。

パラメーターは、よく変更する可能性がある値を抜き出すのに適しています。 さまざまな環境でパラメーターを上書きする必要がある場合に特に便利です。 環境に基づいてパラメーターを上書きする方法については、「[ロジック アプリの定義を作成する](../logic-apps/logic-apps-author-definitions.md)」と [REST API のドキュメント](https://docs.microsoft.com/rest/api/logic)を参照してください。

この例では、クエリ用語でパラメーターを使用できるように、既存のロジック アプリを更新する方法を示します。

1. コード ビューで、`parameters : {}` オブジェクトを見つけて、`currentFeedUrl` オブジェクトを追加します。

        "currentFeedUrl" : {
            "type" : "string",
            "defaultValue" : "http://rss.cnn.com/rss/cnn_topstories.rss"
        }

2. `When_a_feed-item_is_published` アクションに移動して、`queries` セクションを見つけて、クエリ値を `"feedUrl": "#@{parameters('currentFeedUrl')}"` で置き換えます。 

    2 つ以上の文字列を結合する場合、`concat` 関数を使用することもできます。 
    たとえば、`"@concat('#',parameters('currentFeedUrl'))"` 
    は上記と同じように動作します。

3.  完了したら、**[保存]** を選択します。 

    これで、`currentFeedURL` オブジェクトから別の URL を渡すことで、Web サイトの RSS フィードを変更できるようになりました。

「[JSON を使用してロジック アプリのワークフロー定義を作成する](../logic-apps/logic-apps-author-definitions.md)」を参照してください。

## <a name="start-logic-app-workflows"></a>ロジック アプリ ワークフローを開始する

ロジック アプリで定義されているワークフローを開始するためのさまざまなオプションがあります。 ワークフローはいつでも、[Azure Portal] からオンデマンドで開始できます。

### <a name="recurrence-triggers"></a>定期実行のトリガー

定期実行のトリガーは、指定した間隔で実行されます。 トリガーに条件付きロジックが設定されている場合は、トリガーによって、ワークフローを実行する必要があるかどうかが判断されます。 トリガーから `200` の状態コードが返された場合は、ワークフローを実行する必要があることを示しています。 ワークフローを実行する必要がない場合、トリガーから `202` の状態コードが返されます。

### <a name="callback-using-rest-apis"></a>REST API を使用したコールバック

サービスでロジック アプリのエンドポイントを呼び出して、ワークフローを開始することができます。 この種のロジック アプリをオンデマンドで開始するには、コマンド バーの **[今すぐ実行]** を選択します。 [トリガーとしてのロジック アプリのエンドポイントの呼び出しによるワークフローの開始](../logic-apps/logic-apps-http-endpoint.md)に関するページを参照してください。 

<!-- Shared links -->
[Azure Portal]: https://portal.azure.com

## <a name="next-steps"></a>次のステップ

* [Switch ステートメント](../logic-apps/logic-apps-switch-case.md) 
* [ループ、スコープ、分割処理](../logic-apps/logic-apps-loops-and-scopes.md)
* [ロジック アプリの定義を作成する](../logic-apps/logic-apps-author-definitions.md)
