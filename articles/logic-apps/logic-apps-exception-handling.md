---
title: "エラーと例外の処理 - Azure Logic Apps | Microsoft Docs"
description: "Azure Logic Apps におけるエラーと例外の処理パターン"
services: logic-apps
documentationcenter: .net,nodejs,java
author: jeffhollan
manager: anneta
editor: 
ms.assetid: e50ab2f2-1fdc-4d2a-be40-995a6cc5a0d4
ms.service: logic-apps
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 10/18/2016
ms.author: jehollan
translationtype: Human Translation
ms.sourcegitcommit: 8a531f70f0d9e173d6ea9fb72b9c997f73c23244
ms.openlocfilehash: be14485c9070d7dce5ecbaea778f31f30e13cfa9
ms.lasthandoff: 03/10/2017


---
# <a name="handle-errors-and-exceptions-in-azure-logic-apps"></a>Azure Logic Apps におけるエラーと例外の処理

Azure Logic Apps には、安定した統合を実現すると共に障害からの回復力を高めるためのツールとパターンが豊富に用意されています。 どのような統合アーキテクチャにも、依存システムによるダウンタイムや問題を適切に処理できるようにするという課題が伴います。 Logic Apps は、ワークフロー内で発生した例外やエラーを処理するうえで必要なツールを備えており、きわめて高度なエラー処理が可能となっています。

## <a name="retry-policies"></a>再試行ポリシー

例外とエラーを処理するうえで最も基本的な方法が再試行ポリシーです。 最初の要求がタイムアウトになるか失敗した場合 (要求への応答として 429 または 5xx が返された場合) にアクションを再試行するかどうかは、このポリシーによって定義されます。 すべてのアクションは、既定により 20 秒間隔で 4 回再試行されます。 つまり最初の要求で `500 Internal Server Error` 応答が返された場合は、ワークフロー エンジンによって 20 秒間の一時停止後に要求が再試行されます。 すべての再試行を終えても例外またはエラーが返される場合、ワークフローは続行され、そのアクションの状態は `Failed` としてマークされます。

再試行ポリシーは、特定のアクションの **inputs** で構成できます。 たとえば、1 時間間隔で 4 回試行するように再試行ポリシーを構成できます。 inputs プロパティの詳細については、「[Workflow Actions and Triggers (ワークフローのアクションとトリガー)][retryPolicyMSDN]」を参照してください。

```json
"retryPolicy" : {
      "type": "<type-of-retry-policy>",
      "interval": <retry-interval>,
      "count": <number-of-retry-attempts>
    }
```

HTTP アクションの再試行回数を 4 回とし、試行までの待ち時間を 10 分とする場合は、次の定義を使用します。

```json
"HTTP": 
{
    "inputs": {
        "method": "GET",
        "uri": "http://myAPIendpoint/api/action",
        "retryPolicy" : {
            "type": "fixed",
            "interval": "PT10M",
            "count": 4
        }
    },
    "runAfter": {},
    "type": "Http"
}
```

サポートされている構文の詳細については、「[Workflow Actions and Triggers (ワークフローのアクションとトリガー)][retryPolicyMSDN]」の再試行ポリシーに関するセクションを参照してください。

## <a name="catch-failures-with-the-runafter-property"></a>RunAfter プロパティによるエラーのキャッチ

ロジック アプリの各アクションでは、ワークフローのステップを順序付けするように、そのアクションの開始前にどのアクションを完了する必要があるかを宣言します。 アクションの定義では、この順序が `runAfter` プロパティで指定されます。 このプロパティは特定のアクションを実行するうえでの前提条件となるアクションとその状態を記述したオブジェクトです。 既定では、ロジック アプリ デザイナーを介して追加されたアクションはすべて、直前のステップの後、かつそのステップが `Succeeded` であった場合に実行されるよう `runAfter` で設定されます。 ただし、この値はカスタマイズすることができます。直前のアクションが `Failed` であった場合や `Skipped` であった場合にアクションを実行できるほか、これらの値を自由に組み合わせて実行条件とすることもできます。 `Insert_Row` という特定のアクションが失敗した後に、指定した Service Bus トピックに項目を追加する場合、`runAfter` を次のように設定します。

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

`Insert_Row` アクションが `Failed` であった場合に実行されるよう `runAfter` プロパティが設定されていることに注目してください。 アクションの状態が `Succeeded`、`Failed`、`Skipped` のいずれかであった場合にアクションを実行するには、次の構文を使用します。

```json
"runAfter": {
        "Insert_Row": [
            "Failed", "Succeeded", "Skipped"
        ]
    }
```

> [!TIP]
> 先行するアクションが失敗した後に実行されて正常に完了したアクションは `Succeeded` としてマークされます。 この動作は、ワークフロー内で発生したすべてのエラーが正常にキャッチされた場合、その実行そのものは `Succeeded` としてマークされることを意味します。

## <a name="scopes-and-results-to-evaluate-actions"></a>アクションを評価するためのスコープと結果

アクションの実行条件には、個々のアクションを指定するだけでなく、複数のアクションを[スコープ](../logic-apps/logic-apps-loops-and-scopes.md)としてグループ化して指定することもできます。このときスコープは、アクションの論理的なまとまりとして作用します。 ロジック アプリのアクションを体系化したり、スコープの状態に対して全体的な評価を実行したりする場合にスコープを活用できます。 スコープ自体には、スコープ内のすべてのアクションが完了した後に状態が返されます。 スコープの状態は、実行条件と同じ条件で判定されます。 実行分岐の最後のアクションが `Failed` または `Aborted` である場合、状態は `Failed` となります。

スコープ内で何らかのエラーが発生した場合に特定のアクションを実行するには、`runAfter` を使用し、スコープが `Failed` としてマークされたことをその条件とします。 スコープの失敗後という実行条件により、スコープ内の "*いずれかの*" アクションが失敗した場合に発生するエラーを&1; つのアクションでキャッチできます。

### <a name="getting-the-context-of-failures-with-results"></a>結果と共にエラーのコンテキストを取得する

スコープ単位でエラーをキャッチできるのは便利ですが、失敗したアクションや返されたエラーまたは状態コードを正確に把握するためには、スコープの結果だけでなくコンテキストが必要となります。 `@result()` ワークフロー関数を使用すると、スコープ内のすべてのアクションの結果に関するコンテキストが得られます。

`@result()` は、単一のパラメーターとしてスコープ名を受け取り、そのスコープに含まれるアクションの結果をすべて含んだ配列を返します。 これらのアクション オブジェクトには、 `@actions()` オブジェクトと同じ属性が存在します (アクションの開始時刻と終了時刻、アクションの状態、アクションの入力、アクションの相関関係 ID、アクションの出力など)。 `runAfter` に `@result()` 関数を組み合わせるだけで、スコープ内で失敗したすべてのアクションのコンテキストを受け取ることができます。

スコープ内の `Failed` となったアクション "*ごとに*" 特定のアクションを実行し、結果の配列をフィルター処理して失敗したアクションを抽出するには、`@result()` と**[配列のフィルター処理](../connectors/connectors-native-query.md)**アクションおよび **[ForEach](../logic-apps/logic-apps-loops-and-scopes.md)** ループを組み合わせて使用します。 抽出した結果の配列を **ForEach** ループに渡すことで、それぞれのエラーに対してアクションを実行することができます。 次の例では、`My_Scope` というスコープ内で失敗したすべてのアクションの応答本文を含む HTTP POST 要求が送信されます (詳細については例の後に記載)。

```json
"Filter_array": {
    "inputs": {
        "from": "@result('My_Scope')",
        "where": "@equals(item()['status'], 'Failed')"
    },
    "runAfter": {
        "My_Scope": [
            "Failed"
        ]
    },
    "type": "Query"
},
"For_each": {
    "actions": {
        "Log_Exception": {
            "inputs": {
                "body": "@item()['outputs']['body']",
                "method": "POST",
                "headers": {
                    "x-failed-action-name": "@item()['name']",
                    "x-failed-tracking-id": "@item()['clientTrackingId']"
                },
                "uri": "http://requestb.in/"
            },
            "runAfter": {},
            "type": "Http"
        }
    },
    "foreach": "@body('Filter_array')",
    "runAfter": {
        "Filter_array": [
            "Succeeded"
        ]
    },
    "type": "Foreach"
}
```

実際の動作についての詳細は以下のとおりです。

1. `My_Scope` 内のすべてのアクションの結果を抽出するために、**配列のフィルター処理**アクションによって `@result('My_Scope')` をフィルター処理します。

2. **配列のフィルター処理**の条件は、状態が `Failed` と等しいすべての `@result()` 要素です。 この条件により、`My_Scope` のすべてのアクションの結果を含む配列がフィルター処理され、失敗したアクションの結果のみを抽出した配列が得られます。

3. **フィルター後の配列**の出力に対して **For Each** アクションを実行します。 このステップでは、フィルター処理済みの失敗したアクションの結果 "*ごとに*" 特定のアクションが実行されます。

    スコープ内の&1; つのアクションが失敗した場合、`foreach` 内のアクションは&1; 回だけ実行されます。 
    失敗したアクションが複数ある場合は、エラーごとに&1; つのアクションが実行されることになります。

4. `foreach` 要素の応答本文 (`@item()['outputs']['body']`) で HTTP POST を送信します。 `@result()` 要素の構造は `@actions()` と同じであり、同じ方法で解析することができます。

5. `@item()['name']` と `@item()['clientTrackingId']` という&2; つのカスタム ヘッダーが含まれます。前者は失敗したアクションの名前、後者は失敗した実行のクライアント追跡 ID です。

参考例として、前述の例で解析した `name`、`body`、`clientTrackingId` の各プロパティを含む&1; つの `@result()` 要素を次に示します。 `foreach` の外側では、`@result()` によってこれらのオブジェクトの配列が返されます。

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

前出の式を使用することで、さまざまな例外処理パターンを実行できます。 フィルターで抽出したエラーの配列全体を、スコープ外の単一の例外処理アクションに渡して実行してもかまいません。その場合、`foreach` は不要です。 前述した `@result()` の応答には、他にも便利なプロパティがあるので、それらを含めることもできます。

## <a name="azure-diagnostics-and-telemetry"></a>Azure 診断とテレメトリ

ここで取り上げたパターンは、発生したエラーや例外を実行中に処理するうえで、きわめて効果的な方法です。しかし実行そのものとは切り離して、エラーを特定し、対応することもできます。 
ワークフローで発生したあらゆるイベント (実行とアクションのすべての状態を含む) は、[Azure 診断](../logic-apps/logic-apps-monitor-your-logic-apps.md)を使用することで簡単に Azure Storage アカウントや Azure Event Hub に送信することができます。 ログやメトリックを監視したり、それらを好きな監視ツールに発行したりすることによって、実行の状態を評価することができます。 その中の一つの方法として、すべてのイベントを Azure Event Hub を介して [Stream Analytics](https://azure.microsoft.com/services/stream-analytics/) にストリーミングすることが考えられます。 Stream Analytics では、診断ログから得られる異常、平均値、エラーに基づいて適宜必要なクエリを記述することができます。 Stream Analytics からの出力は、キューやトピック、SQL、DocumentDB、Power BI などの他のデータ ソースに簡単に渡すことができます。

## <a name="next-steps"></a>次のステップ

* [Azure Logic Apps を使用したエラー処理の構築方法を確認する](../logic-apps/logic-apps-scenario-error-and-exception-handling.md)
* [さらに他の Logic Apps の例とシナリオを見る](../logic-apps/logic-apps-examples-and-scenarios.md)
* [ロジック アプリの自動デプロイの作成方法を確認する](../logic-apps/logic-apps-create-deploy-template.md)
* [Visual Studio でロジック アプリをビルドしてデプロイする](logic-apps-deploy-from-vs.md)

<!-- References -->
[retryPolicyMSDN]: https://docs.microsoft.com/rest/api/logic/actions-and-triggers#Anchor_9

