---
title: エラーと例外の処理 - Azure Logic Apps
description: Azure Logic Apps におけるエラーと例外の処理パターンについて説明します。
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: dereklee
ms.author: deli
ms.reviewer: klam, estfan, LADocs
ms.date: 01/31/2018
ms.topic: article
ms.openlocfilehash: 828bea50a66b90f35843901ae2d7c703ffa58f2d
ms.sourcegitcommit: 5f67772dac6a402bbaa8eb261f653a34b8672c3a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/01/2019
ms.locfileid: "70208179"
---
# <a name="handle-errors-and-exceptions-in-azure-logic-apps"></a>Azure Logic Apps におけるエラーと例外の処理

あらゆる統合アーキテクチャに言えることですが、依存システムに起因するダウンタイムや問題の適切な処理方法には難しい問題が伴うことがあります。 問題やエラーを円滑に処理する堅牢で回復力の高い統合を構築しやすいよう、Logic Apps には、エラーと例外の処理に関してきわめて優れた機能が用意されています。 

<a name="retry-policies"></a>

## <a name="retry-policies"></a>再試行ポリシー

"*再試行ポリシー*" は、例外とエラーの最も基本的な処理として、それをサポートしているあらゆるアクションやトリガーで使うことができます。 最初の要求がタイムアウトしたりエラーが発生したりした場合 (つまり何らかの要求に対する応答として 408、429、5xx のいずれかが返された場合)、アクションまたはトリガーが要求を再試行するかどうか、また、どのように再試行するかを再試行ポリシーで指定します。 他の再試行ポリシーが使用されていない場合は、既定のポリシーが使用されます。 

再試行ポリシーの種類を次に示します。 

| Type | 説明 | 
|------|-------------| 
| **既定値** | このポリシーは、"*指数関数的に増加*" する間隔で、最大 4 回の再試行を送信します。間隔の増加係数は 7.5 秒で、下限と上限はそれぞれ 5 秒と 45 秒になります。 | 
| **指数間隔**  | このポリシーは、指数関数的に増加する範囲から選択されるランダムな間隔を待ち時間として、次の要求を送信します。 | 
| **固定間隔**  | このポリシーは、指定された間隔を待ち時間として、次の要求を送信します。 | 
| **なし**  | 要求を再送信しません。 | 
||| 

再試行ポリシーの制限については、「[Logic Apps の制限と構成](../logic-apps/logic-apps-limits-and-config.md#request-limits)」をご覧ください。 

### <a name="change-retry-policy"></a>再試行ポリシーの変更

異なる再試行ポリシーを選択するには、次の手順に従います。 

1. ロジック アプリ デザイナーでロジック アプリを開きます。 

2. アクションまたはトリガーの **[設定]** を開きます。

3. アクションまたはトリガーが再試行ポリシーをサポートしている場合、 **[再試行ポリシー]** で目的の種類を選択します。 

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
         "maximumInterval": "<maximun-interval>"
      },
      "<other-action-specific-inputs>"
   },
   "runAfter": {}
}
```

*必須*

| 値 | Type | 説明 |
|-------|------|-------------|
| <*retry-policy-type*> | string | 使用する再試行ポリシーの種類: `default`、`none`、`fixed`、または `exponential` | 
| <*retry-interval*> | string | 再試行間隔。この値には [ISO 8601 形式](https://en.wikipedia.org/wiki/ISO_8601#Combined_date_and_time_representations)を使用する必要があります。 既定の最小間隔は `PT5S` で、最大間隔は `PT1D` です。 指数の間隔ポリシーを使用するとき、最小と最大にさまざまな値を指定できます。 | 
| <*retry-attempts*> | 整数 | 再試行の回数。1 - 90 で指定する必要があります。 | 
||||

*省略可能*

| 値 | Type | 説明 |
|-------|------|-------------|
| <*minimum-interval*> | string | 指数間隔ポリシーに関して、ランダムに選択される間隔の最小値です ([ISO 8601 形式](https://en.wikipedia.org/wiki/ISO_8601#Combined_date_and_time_representations))。 | 
| <*maximum-interval*> | string | 指数間隔ポリシーに関して、ランダムに選択される間隔の最大値です ([ISO 8601 形式](https://en.wikipedia.org/wiki/ISO_8601#Combined_date_and_time_representations))。 | 
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

## <a name="catch-and-handle-failures-with-the-runafter-property"></a>runAfter プロパティを使用してエラーをキャッチして処理する

ロジック アプリの各アクションは、そのアクションが開始する前に終了する必要があるアクションを宣言します。これは、ワークフローのステップの順序を指定する方法と同じです。 アクションの定義において、**runAfter** プロパティはこの順序を定義します。また、このプロパティは、特定のアクションを実行するうえでの前提条件となるアクションとその状態を記述するオブジェクトです。

既定では、ロジック アプリ デザイナーで追加されたすべてのアクションは、先行する手順の結果が **Succeeded** であれば、その手順の次に実行されるように設定されます。 ただし、**runAfter** の値をカスタマイズして、先行するアクションの結果が **Failed** や **Skipped** (または組み合わせ) の場合にもアクションが起動するようにできます。 たとえば、特定の **Insert_Row** アクションが失敗した後で、特定の Service Bus トピックに項目を追加するには、次の例の **runAfter** 定義を使用できます。

```json
"Send_message": {
    "inputs": {
        "body": {
            "ContentData": "@{encodeBase64(body('Insert_Row'))}",
            "ContentType": "{ \"content-type\" : \"application/json\" }"
        },
        "host": {
            "api": {
                "runtimeUrl": "https://logic-apis-westus.azure-apim.net/apim/servicebus"
            },
            "connection": {
                "name": "@parameters('$connections')['servicebus']['connectionId']"
            }
        },
        "method": "post",
        "path": "/@{encodeURIComponent('failures')}/messages"
    },
    "runAfter": {
        "Insert_Row": [
            "Failed"
        ]
    }
}
```

この **runAfter** プロパティは、**Insert_Row** アクションの状態が **Failed** のときに実行するように設定されています。 アクションの状態が **Succeeded**、**Failed**、または **Skipped** の場合にアクションを実行するには、次の構文を使用します。

```json
"runAfter": {
        "Insert_Row": [
            "Failed", "Succeeded", "Skipped"
        ]
    }
```

> [!TIP]
> 先行するアクションが失敗した後で実行され、正常に完了したアクションは **Succeeded** とマークされます。 この動作は、ワークフロー内で発生したすべてのエラーが正常にキャッチされた場合、その実行そのものは **Succeeded** としてマークされることを意味します。

<a name="scopes"></a>

## <a name="evaluate-actions-with-scopes-and-their-results"></a>スコープとその結果を使用してアクションを評価する

**runAfter** プロパティを使用して個々のアクションの後で手順を実行するように、アクションを[スコープ](../logic-apps/logic-apps-control-flow-run-steps-group-scopes.md)にまとめることができます。 アクションを論理的にグループ化し、スコープの集合的な状態を調査し、その状態に基づいてアクションを実行する場合は、スコープを使用できます。 そのスコープ内のすべてのアクションの実行が完了すると、スコープが独自の状態を取得します。 

スコープの状態を調べるには、ロジック アプリの実行状態を調べるのと同じ基準 (**Succeeded** や **Failed** など) を使用できます。 

既定では、スコープのすべてのアクションが成功すると、そのスコープの状態は **Succeeded** とマークされます。 スコープの最後のアクションが **Failed** または **Aborted** になると、スコープの状態は **Failed** とマークされます。 

**Failed** スコープの例外をキャッチして、そのエラーを処理するアクションを実行するには、その **Failed** スコープの **runAfter** プロパティを使用できます。 このように、スコープ内の "*いずれかの*" アクションが失敗したときに、そのスコープに対して **runAfter** プロパティを使用している場合、エラーをキャッチする 1 つのアクションを作成できます。

スコープの制限については、[制限と構成](../logic-apps/logic-apps-limits-and-config.md)に関するページをご覧ください。

<a name="get-results-from-failures"></a>

### <a name="get-context-and-results-for-failures"></a>エラーのコンテキストと結果を取得する

スコープ単位でエラーをキャッチできるのは便利ですが、失敗したアクションや返されたエラーまたは状態コードを正確に把握するためには、スコープの結果だけでなくコンテキストが必要となります。

[`result()`](../logic-apps/workflow-definition-language-functions-reference.md#result) 関数を使用すると、スコープ内のすべてのアクションの結果に関するコンテキストが得られます。 `result()` 関数は、単一のパラメーター (スコープの名前) を受け取り、そのスコープ内のアクションの結果をすべて含む配列を返します。 これらのアクションのオブジェクトには、`@actions()` オブジェクトと同じ属性 (アクションの開始時刻、終了時刻、状態、入力、相関 ID、出力など) が含まれます。 `@result()` 式と `runAfter` プロパティを組み合わせるだけで、スコープ内で失敗したすべてのアクションのコンテキストを受け取ることができます。

スコープ内の **Failed** となったアクションごとにアクションを実行し、失敗したアクションに到達するまで結果の配列をフィルター処理するには、`@result()`[ 式を **[配列のフィルター処理]** ](../connectors/connectors-native-query.md) アクションと [**For each**](../logic-apps/logic-apps-control-flow-loops.md) ループと組み合わせて使用します。 抽出した結果の配列を **For each** ループに渡すことで、それぞれのエラーに対してアクションを実行することができます。

次の例では、"My_Scope" というスコープ内で失敗したすべてのアクションの応答本文を含む HTTP POST 要求が送信されます (詳細については例の後に記載)。

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

2. **[配列のフィルター処理]** の条件は、状態が **Failed** と等しいすべての `@result()` 項目です。 この条件により、"My_Scope" のすべてのアクションの結果を含む配列がフィルター処理され、失敗したアクションの結果のみを抽出した配列が得られます。

3. "*フィルター後の配列*" の出力に対して **For each** ループ アクションを実行します。 このステップでは、フィルター処理済みの失敗したアクションの結果ごとに特定のアクションが実行されます。

   スコープ内の 1 つのアクションが失敗した場合、**For each** ループ内のアクションは 1 回だけ実行されます。 
   失敗したアクションが複数ある場合は、エラーごとに 1 つのアクションが実行されます。

4. **For each** 項目の応答本文すなわち `@item()['outputs']['body']` 式で HTTP POST を送信します。 

   `@result()` 項目の構造は `@actions()` と同じであり、同じ方法で解析することができます。

5. `@item()['name']` と `@item()['clientTrackingId']` という 2 つのカスタム ヘッダーが含まれます。前者は失敗したアクションの名前、後者は失敗した実行のクライアント追跡 ID です。

参考例として、前述の例で解析した **name**、**body**、**clientTrackingId** の各プロパティを含む 1 つの `@result()` 項目を次に示します。 **For each** アクションの外側では、`@result()` によってこれらのオブジェクトの配列が返されます。

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

この記事の前出の式を使用することで、さまざまな例外処理パターンを実行できます。 フィルターで抽出したエラーの配列全体を、スコープ外の単一の例外処理アクションに渡して実行してもかまいません。その場合、**For each** アクションは不要です。 前述した **\@result()** の応答には、他にも便利なプロパティがあるので、それらを含めることもできます。

## <a name="azure-diagnostics-and-metrics"></a>Azure Diagnostics とメトリック

ここで取り上げたパターンは、発生したエラーや例外を実行中に処理するうえで、きわめて効果的な方法です。しかし実行そのものとは切り離して、エラーを特定し、対応することもできます。 
[Azure Diagnostics](../logic-apps/logic-apps-monitor-your-logic-apps.md) を使用すると、ワークフローで発生したあらゆるイベントを、実行とアクションのすべての状態を含めて、簡単に Azure Storage アカウントや Azure Event Hubs で作成されたイベント ハブに送信できます。 

ログやメトリックを監視したり、それらを好きな監視ツールに発行したりすることによって、実行の状態を評価することができます。 その中の一つの方法として、すべてのイベントを Event Hubs を介して [Azure Stream Analytics](https://azure.microsoft.com/services/stream-analytics/) にストリーミングすることが考えられます。 Stream Analytics では、診断ログから得られる異常、平均値、またはエラーに基づいて適宜必要なクエリを記述できます。 Stream Analytics を使用して、キュー、トピック、SQL、Azure Cosmos DB、Power BI などのその他のデータ ソースに情報を送信できます。

## <a name="next-steps"></a>次の手順

* [Azure Logic Apps を使用したエラー処理の構築方法を確認する](../logic-apps/logic-apps-scenario-error-and-exception-handling.md)
* [さらに他の Logic Apps の例とシナリオを見る](../logic-apps/logic-apps-examples-and-scenarios.md)

<!-- References -->
[retryPolicyMSDN]: https://docs.microsoft.com/rest/api/logic/actions-and-triggers#Anchor_9
