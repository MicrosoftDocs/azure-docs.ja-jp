---
title: アクションを繰り返すループを追加する
description: Azure Logic Apps 内のワークフロー アクションを繰り返す、または配列を処理するループを作成する
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 01/05/2019
ms.openlocfilehash: aa4be5852b4f8af00346a3ea9a86b13a85f99824
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "93358458"
---
# <a name="create-loops-that-repeat-workflow-actions-or-process-arrays-in-azure-logic-apps"></a>Azure Logic Apps 内のワークフロー アクションを繰り返す、または配列を処理するループを作成する

ロジック アプリで配列を処理するために、["Foreach" ループ](#foreach-loop)を作成できます。 このループでは、配列の項目ごとに 1 つ以上のアクションが繰り返されます。 "Foreach" ループで処理できる配列項目の数に対する制限については、「[コンカレンシー、ループ、および分割処理の制限](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits)」を参照してください。

条件が満たされるか、状態が変更されるまでアクションを繰り返すには、["Until" ループ](#until-loop)を作成できます。 ロジック アプリでは最初にループ内部のすべてのアクションが実行されて、条件または状態がチェックされます。 条件が満たされると、ループが停止します。 そうでない場合は、ループが繰り返されます。 ロジック アプリの実行で処理できる "Until" ループの数の既定値と上限については、「[コンカレンシー、ループ、および分割処理の制限](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits)」を参照してください。

> [!TIP]
> 配列を受け取るトリガーがあり、各配列項目のワークフローを実行する場合は、[**SplitOn** トリガー プロパティ](../logic-apps/logic-apps-workflow-actions-triggers.md#split-on-debatch)を使用してその配列を "*バッチ解除*" できます。

## <a name="prerequisites"></a>前提条件

* Azure アカウントとサブスクリプション。 サブスクリプションをお持ちでない場合には、[無料の Azure アカウントにサインアップ](https://azure.microsoft.com/free/)してください。 

* [ロジック アプリの作成方法](../logic-apps/quickstart-create-first-logic-app-workflow.md)に関する基本的な知識

<a name="foreach-loop"></a>

## <a name="foreach-loop"></a>"Foreach" ループ

"Foreach" ループは、配列項目ごとに 1 つ以上のアクションを繰り返し、配列でのみ動作します。 "Foreach" ループを使用する場合の考慮事項のいくつかを次に示します。

* "Foreach" ループは、制限された数の配列項目を処理できます。 この制限については、「[コンカレンシー、ループ、および分割処理の制限](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits)」を参照してください。

* 既定では、"Foreach" ループ内のイテレーションは同時または並列に実行されます。 この動作は、[Power Automate の **Apply to each** ループ](/power-automate/apply-to-each)とは異なっています。このループ内では、イテレーションは一度に 1 つずつ、つまり順番に実行されます。 ただし、[シーケンシャル "Foreach" ループ イテレーションを設定する](#sequential-foreach-loop)こともできます。 たとえば、"Foreach" ループ内の次のイテレーションを[遅延アクション](../connectors/connectors-native-delay.md)を使用して一時停止するには、そのループが順番に実行されるように設定する必要があります。

  この既定の動作の例外は、入れ子になったループです。このループでは、イテレーションは常に、並列ではなく順番に実行されます。 入れ子になったループ内の項目に対して並列で操作を実行するには、[子ロジック アプリ](../logic-apps/logic-apps-http-endpoint.md)を作成して呼び出します。

* 各ループ イテレーション中での変数に対する操作の予測可能な結果を取得するには、これらのループを順番に実行します。 たとえば、同時実行ループが終了すると、変数操作に対する増分、減分、追加で予測可能な結果が返されます。 ただし、同時実行ループでの各イテレーション中に、これらの操作で予期しない結果が返される可能性があります。 

* "Foreach" ループ内のアクションは、[`@item()`](../logic-apps/workflow-definition-language-functions-reference.md#item) 
式を使用して、配列内の各項目を参照して処理します。 配列にないデータを指定すると、ロジック アプリのワークフローが失敗します。 

このロジック アプリの例では、Web サイトの RSS フィードの日次サマリーを送信します。 アプリは、新しい項目ごとに電子メールを送信する "Foreach" ループを使用します。

1. Outlook.com アカウントや職場または学校アカウントを使用して、[このサンプルのロジック アプリを作成](../logic-apps/quickstart-create-first-logic-app-workflow.md)します。

2. RSS トリガーと電子メール送信アクションの間に、"Foreach" ループを追加します。 

   1. ステップの間にループを追加するには、これらのステップ間の矢印の上にポインターを移動します。 
   表示される **プラス記号** ( **+** ) を選択し、 **[アクションの追加]** を選択します。

      ![[アクションの追加] を選択](media/logic-apps-control-flow-loops/add-for-each-loop.png)

   1. 検索ボックスで、 **[すべて]** を選択します。 検索ボックスに、フィルターとして「for each」と入力します。 アクションの一覧から、次のアクションを選択します。**For each - コントロール**

      !["For each" ループを追加](media/logic-apps-control-flow-loops/select-for-each.png)

3. これでループをビルドします。 **[動的なコンテンツの追加]** リストが表示された後に、 **[Select an output from previous steps]\(前のステップから出力を選択する\)** で、RSS トリガーからの出力である **[フィード リンク]** 配列を選択します。 

   ![動的コンテンツ リストから選択する](media/logic-apps-control-flow-loops/for-each-loop-dynamic-content-list.png)

   > [!NOTE] 
   > 選択できるのは。前のステップからの配列の出力 "*のみ*" です。

   選択した配列がここに表示されます。

   ![配列を選択する](media/logic-apps-control-flow-loops/for-each-loop-select-array.png)

4. 各配列項目でアクションを実行するには、**電子メールを送信する** アクションをループにドラッグします。 

   ロジック アプリは次の例のようになります。

   !["Foreach"ループにステップを追加する](media/logic-apps-control-flow-loops/for-each-loop-with-step.png)

5. ロジック アプリを保存します。 ロジック アプリを手動でテストするには、デザイナーのツール バーで **[実行]** を選択します。

<a name="for-each-json"></a>

## <a name="foreach-loop-definition-json"></a>"Foreach" ループの定義 (JSON)

ロジック アプリをコード ビューで操作している場合、代わりに次の例のようにロジック アプリの JSON 定義で `Foreach` ループを定義できます。

``` json
"actions": {
   "myForEachLoopName": {
      "type": "Foreach",
      "actions": {
         "Send_an_email": {
            "type": "ApiConnection",
            "inputs": {
               "body": {
                  "Body": "@{item()}",
                  "Subject": "New CNN post @{triggerBody()?['publishDate']}",
                  "To": "me@contoso.com"
               },
               "host": {
                  "api": {
                     "runtimeUrl": "https://logic-apis-westus.azure-apim.net/apim/office365"
                  },
                  "connection": {
                     "name": "@parameters('$connections')['office365']['connectionId']"
                  }
               },
               "method": "post",
               "path": "/Mail"
            },
            "runAfter": {}
         }
      },
      "foreach": "@triggerBody()?['links']",
      "runAfter": {}
   }
}
```

<a name="sequential-foreach-loop"></a>

## <a name="foreach-loop-sequential"></a>"Foreach" ループ:シーケンシャル

既定では、"Foreach" ループのサイクルは並列に実行されます。 各サイクルをシーケンシャルに実行するには、ループの **[シーケンシャル]** オプションを設定します。 予測可能な結果が想定されるループ内でループや変数を入れ子にしている場合、"Foreach" ループは順番に実行する必要があります。 

1. ループの右上隅で、**省略記号** ( **...** ) > **[設定]** と選択します。

   !["Foreach" ループで、[...] > [設定] を選択する](media/logic-apps-control-flow-loops/for-each-loop-settings.png)

1. **[同時実行制御]** で、 **[同時実行制御]** 設定を **[オン]** にします。 **[並列処理の次数]** スライダーを **1** まで動かし、 **[完了]** を選択します。

   ![同時実行制御をオンにする](media/logic-apps-control-flow-loops/for-each-loop-sequential-setting.png)

ロジック アプリの JSON 定義を使用している場合、`operationOptions` パラメーターを追加することにより `Sequential` オプションを使用できます。たとえば次のようになります。

``` json
"actions": {
   "myForEachLoopName": {
      "type": "Foreach",
      "actions": {
         "Send_an_email": { }
      },
      "foreach": "@triggerBody()?['links']",
      "runAfter": {},
      "operationOptions": "Sequential"
   }
}
```

<a name="until-loop"></a>

## <a name="until-loop"></a>"Until" ループ
  
アクションを実行し、条件が満たされるか、状態が変更されるまで繰り返すには、これらのアクションを "Until" ループに入れます。 ロジック アプリでは最初にループ内部のすべてのアクションが実行されて、条件または状態がチェックされます。 条件が満たされると、ループが停止します。 そうでない場合は、ループが繰り返されます。 ロジック アプリの実行で処理できる "Until" ループの数の既定値と上限については、「[コンカレンシー、ループ、および分割処理の制限](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits)」を参照してください。

"Until" ループを使用できる一般的なシナリオをいくつか紹介します。

* 必要なレスポンスを受け取るまでエンドポイントを呼び出す。

* データベース内にレコードを作成する。 そのレコード内の特定のフィールドが承認されるまで待機します。 処理し続けます。 

このロジック アプリ例は、毎日午前 8 時 00 分に開始し、ある変数の値が 10 と等しくなるまでその変数を増分させます。 その後、このロジック アプリは現在の値を確認する電子メールを送信します。 

> [!NOTE]
> これらの手順では Office 365 Outlook を使用できますが、Logic Apps がサポートするどの電子メール プロバイダーでも使用できます。 
> [こちらからコネクタの一覧を確認してください](/connectors/)。 別の電子メール アカウントを使用する場合、おおよその手順は変わりませんが、UI の表示がやや異なることがあります。 

1. 空のロジック アプリを作成します。 ロジック アプリ デザイナーの検索ボックスの下で、 **[すべて]** を選択します。 「定期的」を検索します。 
   トリガーの一覧から、 **[定期的なスケジュール]** を選択します。

   ![[定期的なスケジュール] トリガーの追加](./media/logic-apps-control-flow-loops/do-until-loop-add-trigger.png)

1. 間隔、頻度、時刻を設定して、トリガーが実行されるタイミングを指定します。 時刻を設定するには、 **[詳細オプションを表示する]** を選択します。

   ![定期的なスケジュールを設定する](./media/logic-apps-control-flow-loops/do-until-loop-set-trigger-properties.png)

   | プロパティ | 値 |
   | -------- | ----- |
   | **間隔** | 1 | 
   | **頻度** | 日 |
   | **設定時刻 (時間)** | 8 |
   ||| 

1. トリガーで、 **[新しいステップ]** を選択します。 
   "変数" を検索し、次のアクションを選択します: **[変数の初期化 - 変数]**

   ![変数の初期化 - 変数 を追加する](./media/logic-apps-control-flow-loops/do-until-loop-add-variable.png)

1. 次の値を使用して変数を設定します。

   ![変数のプロパティを設定する](./media/logic-apps-control-flow-loops/do-until-loop-set-variable-properties.png)

   | プロパティ | 値 | 説明 |
   | -------- | ----- | ----------- |
   | **名前** | 制限 | 変数の名前 | 
   | **Type** | Integer | 変数のデータ型 | 
   | **Value** | 0 | 変数の開始値 | 
   |||| 

1. **[変数を初期化する]** アクションの下で、 **[新しいステップ]** を選択します。 

1. 検索ボックスで、 **[すべて]** を選択します。 "期限" を検索し、次のアクションを選択します: **[期限 - コントロール]**

   ![[期限] ループを追加する](./media/logic-apps-control-flow-loops/do-until-loop-add-until-loop.png)

1. **Limit** 変数と **is equal to** 演算子を選択して、ループの終了条件をビルドします。 
   比較対象値として **10** と入力します。

   ![ループを停止させる終了条件をビルドする](./media/logic-apps-control-flow-loops/do-until-loop-settings.png)

1. ループ内で、 **[アクションの追加]** を選択します。 

1. 検索ボックスで、 **[すべて]** を選択します。 "変数" を検索し、次のアクションを選択します: **[変数の値を増やす - 変数]**

   ![変数の値を増やすアクションを追加する](./media/logic-apps-control-flow-loops/do-until-loop-increment-variable.png)

1. **[名前]** には、**Limit** 変数を選択します。 **[値]** には、「1」と入力します。 

     !["Limit" を 1 ずつ増やす](./media/logic-apps-control-flow-loops/do-until-loop-increment-variable-settings.png)

1. ループ外部の下側で、 **[新しいステップ]** を選択します。 

1. 検索ボックスで、 **[すべて]** を選択します。 
     電子メールを送信するアクションを検索し、追加します。たとえば、次のようにします。 

     ![電子メールを送信するアクションを追加する](media/logic-apps-control-flow-loops/do-until-loop-send-email.png)

1. メッセージに従ってメール アカウントにサインインします。

1. 電子メール アクションのプロパティを設定します。 件名に **Limit** 変数を追加します。 こうすることで、次のように、変数の現在の値が指定の条件と一致していることを確認できます。

      ![電子メールのプロパティを設定する](./media/logic-apps-control-flow-loops/do-until-loop-send-email-settings.png)

      | プロパティ | 値 | 説明 |
      | -------- | ----- | ----------- | 
      | **To** | *\<email-address\@domain>* | 受信者の電子メール アドレス。 テストのために、自分の電子メール アドレスを使用します。 | 
      | **件名** | "Limit" の現在の値は **Limit** | 電子メールの件名を指定します。 この例では、**Limit** 変数が含まれていることを確認してください。 | 
      | **本文** | <*email-content*> | 送信する電子メール メッセージの内容を指定します。 この例では、任意のテキストを入力してください。 | 
      |||| 

1. ロジック アプリを保存します。 ロジック アプリを手動でテストするには、デザイナーのツール バーで **[実行]** を選択します。

      ロジックの実行が開始された後に、指定した内容の電子メールを受け取ります。

      ![受信した電子メール](./media/logic-apps-control-flow-loops/do-until-loop-sent-email.png)

<a name="prevent-endless-loops"></a>

## <a name="prevent-endless-loops"></a>無限ループを防ぐ

"Until" ループは、これらのプロパティに基づいて実行を停止するため、これらの値を適宜設定してください。

* **Count**:この値は、ループが終了するまでに実行されるループの最大数です。 ロジック アプリの実行で処理できる "Until" ループの数の既定値と上限については、「[コンカレンシー、ループ、および分割処理の制限](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits)」を参照してください。

* **タイムアウト**:この値は、ループを終了するまでに実行できる最大時間数であり、[ISO 8601 形式](https://en.wikipedia.org/wiki/ISO_8601)で指定されます。 **Timeout** 値の既定値と上限については、「[コンカレンシー、ループ、および分割処理の制限](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits)」を参照してください。

  タイムアウト値は、ループのサイクルごとに評価されます。 ループ内のアクションがタイムアウト制限より長くなる場合、現在のサイクルは停止しません。 ただし、制限の条件が満たされていないため、次のサイクルは開始しません。

これらの制限を変更するには、ループ アクションで **[制限の変更]** を選択します。

<a name="until-json"></a>

## <a name="until-definition-json"></a>"Until" 定義 (JSON)

ロジック アプリをコード ビューで操作している場合、代わりに次の例のようにロジック アプリの JSON 定義で `Until` ループを定義できます。

``` json
"actions": {
   "Initialize_variable": {
      // Definition for initialize variable action
   },
   "Send_an_email": {
      // Definition for send email action
   },
   "Until": {
      "type": "Until",
      "actions": {
         "Increment_variable": {
            "type": "IncrementVariable",
            "inputs": {
               "name": "Limit",
               "value": 1
            },
            "runAfter": {}
         }
      },
      "expression": "@equals(variables('Limit'), 10)",
      // To prevent endless loops, an "Until" loop 
      // includes these default limits that stop the loop. 
      "limit": { 
         "count": 60,
         "timeout": "PT1H"
      },
      "runAfter": {
         "Initialize_variable": [
            "Succeeded"
         ]
      }
   }
}
```

この「期限」ループ例では、リソースを作成する HTTP エンドポイントを呼び出します。 HTTP 応答の本文で `Completed` ステータスが返されると、ループは停止します。 無限ループを防ぐために、このループは次の条件のいずれかが発生する場合でも中止されます。

* ループが 10 回実行された。これは `count` 属性で指定されています。 既定値は 60 回です。 

* ループが 2 時間実行した。これは `timeout` 属性で ISO 8601 形式で指定されています。 既定値は 1 時間です。
  
``` json
"actions": {
   "myUntilLoopName": {
      "type": "Until",
      "actions": {
         "Create_new_resource": {
            "type": "Http",
            "inputs": {
               "body": {
                  "resourceId": "@triggerBody()"
               },
               "url": "https://domain.com/provisionResource/create-resource",
               "body": {
                  "resourceId": "@triggerBody()"
               }
            },
            "runAfter": {},
            "type": "ApiConnection"
         }
      },
      "expression": "@equals(triggerBody(), 'Completed')",
      "limit": {
         "count": 10,
         "timeout": "PT2H"
      },
      "runAfter": {}
   }
}
```

## <a name="get-support"></a>サポートを受ける

* ご質問がある場合は、[Azure Logic Apps に関する Microsoft Q&A 質問ページ](/answers/topics/azure-logic-apps.html)を参照してください。
* 機能や提案について投稿や投票を行うには、[Azure Logic Apps のユーザー フィードバック サイト](https://aka.ms/logicapps-wish)にアクセスしてください。

## <a name="next-steps"></a>次のステップ

* [条件に基づいてステップを実行する (条件付きステートメント)](../logic-apps/logic-apps-control-flow-conditional-statement.md)
* [さまざまな値に基づいてステップを実行する (switch ステートメント)](../logic-apps/logic-apps-control-flow-switch-statement.md)
* [並列ステップを実行または結合する (分岐)](../logic-apps/logic-apps-control-flow-branches.md)
* [グループ化されたアクションの状態に基づいてステップを実行する (スコープ)](../logic-apps/logic-apps-control-flow-run-steps-group-scopes.md)
