---
title: ワークフローにおけるエラーと例外の処理
description: Azure Logic Apps を使用して、作成した自動化されたタスクとワークフローで発生するエラーと例外を処理する方法について説明します。
services: logic-apps
ms.suite: integration
author: dereklee
ms.author: deli
ms.reviewer: estfan, logicappspm, azla
ms.date: 02/18/2021
ms.topic: article
ms.openlocfilehash: fbe797937021763bb97ca09e1da792d9a7010f9a
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/03/2021
ms.locfileid: "101702506"
---
# <a name="handle-errors-and-exceptions-in-azure-logic-apps"></a>Azure Logic Apps におけるエラーと例外の処理

あらゆる統合アーキテクチャに言えることですが、依存システムに起因するダウンタイムや問題の適切な処理方法には難しい問題が伴うことがあります。 問題やエラーを円滑に処理する堅牢で回復力の高い統合を構築しやすいよう、Logic Apps には、エラーと例外の処理に関してきわめて優れた機能が用意されています。

<a name="retry-policies"></a>

## <a name="retry-policies"></a>再試行ポリシー

"*再試行ポリシー*" は、例外とエラーの最も基本的な処理として、それをサポートしているあらゆるアクションやトリガーで使うことができます。例については、[HTTP アクション](../logic-apps/logic-apps-workflow-actions-triggers.md#http-trigger)に関するページを参照してください。 最初の要求がタイムアウトしたりエラーが発生したりした場合 (つまり何らかの要求に対する応答として 408、429、5xx のいずれかが返された場合)、アクションまたはトリガーが要求を再試行するかどうか、また、どのように再試行するかを再試行ポリシーで指定します。 他の再試行ポリシーが使用されていない場合は、既定のポリシーが使用されます。

再試行ポリシーの種類を次に示します。

| Type | 説明 |
|------|-------------|
| **[Default]** | このポリシーは、"*指数関数的に増加*" する間隔で、最大 4 回の再試行を送信します。間隔の増加係数は 7.5 秒で、下限と上限はそれぞれ 5 秒と 45 秒になります。 |
| **指数間隔**  | このポリシーは、指数関数的に増加する範囲から選択されるランダムな間隔を待ち時間として、次の要求を送信します。 |
| **固定間隔**  | このポリシーは、指定された間隔を待ち時間として、次の要求を送信します。 |
| **なし**  | 要求を再送信しません。 |
|||

再試行ポリシーの制限については、「[Logic Apps の制限と構成](../logic-apps/logic-apps-limits-and-config.md#http-limits)」をご覧ください。

### <a name="change-retry-policy"></a>再試行ポリシーの変更

異なる再試行ポリシーを選択するには、次の手順に従います。

1. ロジック アプリ デザイナーでロジック アプリを開きます。

1. アクションまたはトリガーの **[設定]** を開きます。

1. アクションまたはトリガーが再試行ポリシーをサポートしている場合、 **[再試行ポリシー]** で目的の種類を選択します。

または、再試行ポリシーをサポートするアクションまたはトリガーの `inputs` セクションで、再試行ポリシーを手動で指定することもできます。 再試行ポリシーを指定しなかった場合は、既定のポリシーが使用されます。

```json
"<action-name>": {
   "type": "<action-type>",
   "inputs": {
      "<action-specific-inputs>",
      "retryPolicy": {
         "type": "<retry-policy-type>",
         "interval": "<retry-interval>",
         "count": <retry-attempts>,
         "minimumInterval": "<minimum-interval>",
         "maximumInterval": "<maximum-interval>"
      },
      "<other-action-specific-inputs>"
   },
   "runAfter": {}
}
```

*必須*

| 値 | Type | 説明 |
|-------|------|-------------|
| <*retry-policy-type*> | String | 使用する再試行ポリシーの種類: `default`、`none`、`fixed`、または `exponential` |
| <*retry-interval*> | String | 再試行間隔。この値には [ISO 8601 形式](https://en.wikipedia.org/wiki/ISO_8601#Combined_date_and_time_representations)を使用する必要があります。 既定の最小間隔は `PT5S` で、最大間隔は `PT1D` です。 指数の間隔ポリシーを使用するとき、最小と最大にさまざまな値を指定できます。 |
| <*retry-attempts*> | Integer | 再試行の回数。1 - 90 で指定する必要があります。 |
||||

*省略可能*

| 値 | Type | 説明 |
|-------|------|-------------|
| <*minimum-interval*> | String | 指数間隔ポリシーに関して、ランダムに選択される間隔の最小値です ([ISO 8601 形式](https://en.wikipedia.org/wiki/ISO_8601#Combined_date_and_time_representations))。 |
| <*maximum-interval*> | String | 指数間隔ポリシーに関して、ランダムに選択される間隔の最大値です ([ISO 8601 形式](https://en.wikipedia.org/wiki/ISO_8601#Combined_date_and_time_representations))。 |
||||

以下、各種のポリシーについて説明します。

<a name="default-retry"></a>

### <a name="default"></a>Default

再試行ポリシーを指定しなかった場合は、既定のポリシーが使用されます。実質的には[指数間隔ポリシー](#exponential-interval)であり、最大 4 回の再試行が送信される間隔は、7.5 秒を増加係数として指数関数的に増やされます。 間隔の範囲は 5 秒から 45 秒です。

実際のアクションやトリガーで既定のポリシーを明示的に定義することはありませんが、HTTP アクションを例に、既定のポリシーの動作を以下に示します。

```json
"HTTP": {
   "type": "Http",
   "inputs": {
      "method": "GET",
      "uri": "http://myAPIendpoint/api/action",
      "retryPolicy" : {
         "type": "exponential",
         "interval": "PT7S",
         "count": 4,
         "minimumInterval": "PT5S",
         "maximumInterval": "PT1H"
      }
   },
   "runAfter": {}
}
```

### <a name="none"></a>なし

失敗した要求を再試行しないようアクションまたはトリガーに指定するには、<*retry-policy-type*> を `none` に設定します。

### <a name="fixed-interval"></a>固定間隔

指定した間隔の経過後に次の要求を送信するようアクションまたはトリガーに指定するには、<*retry-policy-type*> を `fixed` に設定します。

*例*

この再試行ポリシーは、最新のニュースを取得する要求が失敗した後、2 回の再試行を行います。それぞれの再試行間には 30 秒の延期期間が設けられます。

```json
"Get_latest_news": {
   "type": "Http",
   "inputs": {
      "method": "GET",
      "uri": "https://mynews.example.com/latest",
      "retryPolicy": {
         "type": "fixed",
         "interval": "PT30S",
         "count": 2
      }
   }
}
```

<a name="exponential-interval"></a>

### <a name="exponential-interval"></a>指数関数的な間隔

ランダムな間隔の経過後に次の要求を送信するようアクションまたはトリガーに指定するには、<*retry-policy-type*> を `exponential` に設定します。 ランダムな間隔は、指数関数的に増加する範囲から選択されます。 最小間隔と最大間隔の既定値は、それぞれ独自の間隔を指定することにより、必要に応じて上書きすることができます。

**ランダム変数の範囲**

次の表を見ると、最大再試行回数に到達するまで、指定された範囲内の一様なランダム変数が再試行ごとに生成されるようすがわかります。

| 再試行回数 | 最小間隔 | 最大間隔 |
|--------------|------------------|------------------|
| 1 | max(0, <*minimum-interval*>) | min(interval, <*maximum-interval*>) |
| 2 | max(interval, <*minimum-interval*>) | min(2 * interval, <*maximum-interval*>) |
| 3 | max(2 * interval, <*minimum-interval*>) | min(4 * interval, <*maximum-interval*>) |
| 4 | max(4 * interval, <*minimum-interval*>) | min(8 * interval, <*maximum-interval*>) |
| .... | .... | .... |
||||

<a name="control-run-after-behavior"></a>

## <a name="catch-and-handle-failures-by-changing-run-after-behavior"></a>"実行条件" を変更して、失敗をキャッチして処理する

ロジック アプリ デザイナーでアクションを追加するときは、これらのアクションを実行するために使用する順序を暗黙的に宣言します。 アクションの実行が完了すると、そのアクションには `Succeeded`、`Failed`、`Skipped`、`TimedOut` などの状態が設定されます。 各アクションの定義では、`runAfter` プロパティによって、最初に完了する必要がある先行処理アクションと、後続処理アクションが実行される前にその先行処理に許可される状態が指定されます。 既定では、デザイナーで追加するアクションは、先行処理が `Succeeded` 状態で完了した後にのみ実行されます。

アクションが未処理エラーまたは例外をスローした場合、アクションは `Failed` としてマークされ、後続処理アクションはすべて `Skipped` としてマークされます。 並列分岐があるアクションに対してこの動作が発生した場合、Logic Apps エンジンはその他の分岐に従い、完了状態を確認します。 たとえば、分岐が `Skipped` アクションで終了した場合、その分岐の完了状態はそのスキップされたアクションの先行処理状態に基づきます。 ロジック アプリの実行が完了すると、すべての分岐の状態を評価することによって、実行全体の状態がエンジンによって決定されます。 分岐のいずれかが失敗すると、ロジック アプリの実行全体が `Failed` としてマークされます。

![実行状態の評価方法を示す例](./media/logic-apps-exception-handling/status-evaluation-for-parallel-branches.png)

先行処理の状態にかかわらずアクションを引き続き実行できるようにするには、[アクションの "実行条件" 動作をカスタマイズ](#customize-run-after)して、先行処理の失敗状態を処理します。

<a name="customize-run-after"></a>

### <a name="customize-run-after-behavior"></a>"実行条件" 動作をカスタマイズする

先行処理の状態が `Succeeded`、`Failed`、`Skipped`、`TimedOut`、またはこれらのいずれかの状態になったときにアクションが実行されるように、アクションの "実行条件" 動作をカスタマイズすることができます。 たとえば、Excel Online の `Add_a_row_into_a_table` 先行処理アクションが `Succeeded` ではなく `Failed` であるとマークされた後にメールを送信するには、次のいずれかの手順に従って "実行条件" 動作を変更します。

* デザイン ビューで、省略記号 ( **...** ) ボタンを選択し、 **[実行条件の構成]** を選択します。

  ![アクションの "実行条件" 動作を構成する](./media/logic-apps-exception-handling/configure-run-after-property-setting.png)

  アクション図形には、先行処理アクションに必要な既定の状態が表示されます。この例では "**表に行を追加**" です。

  ![アクションの既定の "実行条件" 動作](./media/logic-apps-exception-handling/change-run-after-property-status.png)

  "実行条件" 動作を希望の状態に変更します。この例では **[が失敗しました]** です。

  !["実行条件" 動作を [が失敗しました] に変更する](./media/logic-apps-exception-handling/run-after-property-status-set-to-failed.png)

  先行処理アクションが `Failed`、`Skipped` または `TimedOut` としてマークされている場合にアクションを実行するように指定するには、その他の状態を選択します。

  !["実行条件" 動作をその他の状態に変更する](./media/logic-apps-exception-handling/run-after-property-multiple-statuses.png)

* コード ビューで、アクションの JSON 定義の `runAfter` プロパティを次の構文に従って編集します。

  ```json
  "<action-name>": {
     "inputs": {
        "<action-specific-inputs>"
     },
     "runAfter": {
        "<preceding-action>": [
           "Succeeded"
        ]
     },
     "type": "<action-type>"
  }
  ```

  たとえば、`runAfter` プロパティを `Succeeded` から `Failed` に変更します。

  ```json
  "Send_an_email_(V2)": {
     "inputs": {
        "body": {
           "Body": "<p>Failed to&nbsp;add row to &nbsp;@{body('Add_a_row_into_a_table')?['Terms']}</p>",,
           "Subject": "Add row to table failed: @{body('Add_a_row_into_a_table')?['Terms']}",
           "To": "Sophia.Owen@fabrikam.com"
        },
        "host": {
           "connection": {
              "name": "@parameters('$connections')['office365']['connectionId']"
           }
        },
        "method": "post",
        "path": "/v2/Mail"
     },
     "runAfter": {
        "Add_a_row_into_a_table": [
           "Failed"
        ]
     },
     "type": "ApiConnection"
  }
  ```

  先行処理アクションが `Failed`、`Skipped` または `TimedOut` としてマークされている場合にアクションを実行するように指定するには、その他の状態を追加します。

  ```json
  "runAfter": {
     "Add_a_row_into_a_table": [
        "Failed", "Skipped", "TimedOut"
     ]
  },
  ```

<a name="scopes"></a>

## <a name="evaluate-actions-with-scopes-and-their-results"></a>スコープとその結果を使用してアクションを評価する

`runAfter` プロパティを使用して個々のアクションの後で手順を実行するように、アクションを[スコープ](../logic-apps/logic-apps-control-flow-run-steps-group-scopes.md)にまとめることができます。 アクションを論理的にグループ化し、スコープの集合的な状態を調査し、その状態に基づいてアクションを実行する場合は、スコープを使用できます。 そのスコープ内のすべてのアクションの実行が完了すると、スコープが独自の状態を取得します。

スコープの状態を調べるには、ロジック アプリの実行状態を調べるのと同じ基準 (`Succeeded` や `Failed` など) を使用できます。

既定では、スコープのすべてのアクションが成功すると、そのスコープの状態は `Succeeded` とマークされます。 スコープの最後のアクションが `Failed` または `Aborted` になると、スコープの状態は `Failed` とマークされます。

`Failed` スコープの例外をキャッチして、そのエラーを処理するアクションを実行するには、その `Failed` スコープの `runAfter` プロパティを使用できます。 このように、スコープ内の "*いずれかの*" アクションが失敗したときに、そのスコープに対して `runAfter` プロパティを使用している場合、エラーをキャッチする 1 つのアクションを作成できます。

スコープの制限については、[制限と構成](../logic-apps/logic-apps-limits-and-config.md)に関するページをご覧ください。

<a name="get-results-from-failures"></a>

### <a name="get-context-and-results-for-failures"></a>エラーのコンテキストと結果を取得する

スコープ単位でエラーをキャッチできるのは便利ですが、失敗したアクションや返されたエラーまたは状態コードを正確に把握するためには、スコープの結果だけでなくコンテキストが必要となります。 [`result()` 関数](../logic-apps/workflow-definition-language-functions-reference.md#result)は、単一のパラメーター (スコープの名前) を受け取り、その第 1 レベルのアクションからの結果を格納した配列を返すことによって、スコープ付きアクション内の最上位レベルのアクションの結果を返します。 これらのアクションのオブジェクトには、`actions()` 関数によって返されるものと同じ属性 (アクションの開始時刻、終了時刻、状態、入力、相関 ID、出力など) が含まれます。 

> [!NOTE]
> `result()` 関数では、第 1 レベルのアクションからの結果 *だけ* が返されます。switch アクションや condition アクションなど、より深い入れ子のアクションからは返されません。

スコープ内の失敗したアクションに関するコンテキストを取得するには、スコープの名前と `runAfter` プロパティを指定した `@result()` 式を使用します。 返された配列を、ステータスが `Failed` のアクションに絞り込むには、[**配列のフィルター処理** アクション](logic-apps-perform-data-operations.md#filter-array-action)を追加します。 返された失敗アクションに対してアクションを実行するには、返されたフィルター処理済みの配列を取得し、[**For each** ループ](../logic-apps/logic-apps-control-flow-loops.md)を使用します。

次の例では、"My_Scope" というスコープ アクション内で失敗したすべてのアクションの応答本文を含む HTTP POST 要求が送信されます (詳細については例の後に記載)。

```json
"Filter_array": {
   "type": "Query",
   "inputs": {
      "from": "@result('My_Scope')",
      "where": "@equals(item()['status'], 'Failed')"
   },
   "runAfter": {
      "My_Scope": [
         "Failed"
      ]
    }
},
"For_each": {
   "type": "foreach",
   "actions": {
      "Log_exception": {
         "type": "Http",
         "inputs": {
            "method": "POST",
            "body": "@item()['outputs']['body']",
            "headers": {
               "x-failed-action-name": "@item()['name']",
               "x-failed-tracking-id": "@item()['clientTrackingId']"
            },
            "uri": "http://requestb.in/"
         },
         "runAfter": {}
      }
   },
   "foreach": "@body('Filter_array')",
   "runAfter": {
      "Filter_array": [
         "Succeeded"
      ]
   }
}
```

上の例における実際の動作についての詳細は以下のとおりです。

1. "My_Scope" 内のすべてのアクションの結果を取得するために、 **[配列のフィルター処理]** アクションには、`@result('My_Scope')` というフィルター式が使用されます

1. **[配列のフィルター処理]** の条件は、状態が `Failed` と等しいすべての `@result()` 要素です。 この条件により、"My_Scope" のすべてのアクションの結果を含む配列がフィルター処理され、失敗したアクションの結果のみを抽出した配列が得られます。

1. "*フィルター後の配列*" の出力に対して `For_each` ループ アクションを実行します。 このステップでは、フィルター処理済みの失敗したアクションの結果ごとに特定のアクションが実行されます。

   スコープ内の 1 つのアクションが失敗した場合、`For_each` ループ内のアクションは 1 回だけ実行されます。 失敗したアクションが複数ある場合は、エラーごとに 1 つのアクションが実行されます。

1. `For_each` 項目の応答本文すなわち `@item()['outputs']['body']` 式で HTTP POST を送信します。

   `@result()` 項目の構造は `@actions()` と同じであり、同じ方法で解析することができます。

1. `@item()['name']` と `@item()['clientTrackingId']` という 2 つのカスタム ヘッダーが含まれます。前者は失敗したアクションの名前、後者は失敗した実行のクライアント追跡 ID です。

参考例として、前述の例で解析した `name`、`body`、`clientTrackingId` の各プロパティを含む 1 つの `@result()` 要素を次に示します。 `For_each` アクションの外側では、`@result()` によってこれらのオブジェクトの配列が返されます。

```json
{
   "name": "Example_Action_That_Failed",
   "inputs": {
      "uri": "https://myfailedaction.azurewebsites.net",
      "method": "POST"
   },
   "outputs": {
      "statusCode": 404,
      "headers": {
         "Date": "Thu, 11 Aug 2016 03:18:18 GMT",
         "Server": "Microsoft-IIS/8.0",
         "X-Powered-By": "ASP.NET",
         "Content-Length": "68",
         "Content-Type": "application/json"
      },
      "body": {
         "code": "ResourceNotFound",
         "message": "/docs/folder-name/resource-name does not exist"
      }
   },
   "startTime": "2016-08-11T03:18:19.7755341Z",
   "endTime": "2016-08-11T03:18:20.2598835Z",
   "trackingId": "bdd82e28-ba2c-4160-a700-e3a8f1a38e22",
   "clientTrackingId": "08587307213861835591296330354",
   "code": "NotFound",
   "status": "Failed"
}
```

この記事の前出の式を使用することで、さまざまな例外処理パターンを実行できます。 フィルターで抽出したエラーの配列全体を、スコープ外の単一の例外処理アクションに渡して実行してもかまいません。その場合、`For_each` は不要です。 前述した `\@result()` の応答には、他にも便利なプロパティがあるので、それらを含めることもできます。

## <a name="set-up-azure-monitor-logs"></a>Azure Monitor ログを設定する

ここで取り上げたパターンは、発生したエラーや例外を実行中に処理するうえで、きわめて効果的な方法です。しかし実行そのものとは切り離して、エラーを特定し、対応することもできます。 [Azure Monitor](../azure-monitor/overview.md) を使用すると、すべての実行とアクションの状態を含む、すべてのワークフロー イベントを [Log Analytics ワークスペース](../azure-monitor/logs/data-platform-logs.md)、[Azure ストレージ アカウント](../storage/blobs/storage-blobs-overview.md)、または [Azure Event Hubs](../event-hubs/event-hubs-about.md) に簡単に送信できます。

ログやメトリックを監視したり、それらを好きな監視ツールに発行したりすることによって、実行の状態を評価することができます。 その中の一つの方法として、すべてのイベントを Event Hubs を介して [Azure Stream Analytics](https://azure.microsoft.com/services/stream-analytics/) にストリーミングすることが考えられます。 Stream Analytics では、診断ログから得られる異常、平均値、またはエラーに基づいて適宜必要なクエリを記述できます。 Stream Analytics を使用して、キュー、トピック、SQL、Azure Cosmos DB、Power BI などのその他のデータ ソースに情報を送信できます。

## <a name="next-steps"></a>次のステップ

* [Azure Logic Apps を使用したエラー処理の構築方法を確認する](../logic-apps/logic-apps-scenario-error-and-exception-handling.md)
* [さらに他の Logic Apps の例とシナリオを見る](../logic-apps/logic-apps-examples-and-scenarios.md)
