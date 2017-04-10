---
title: "ワークフローのアクションとトリガー - Azure Logic Apps | Microsoft Docs"
description: 
services: logic-apps
author: MandiOhlinger
manager: anneta
editor: 
documentationcenter: 
ms.assetid: 86a53bb3-01ba-4e83-89b7-c9a7074cb159
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 11/17/2016
ms.author: mandia
translationtype: Human Translation
ms.sourcegitcommit: 424d8654a047a28ef6e32b73952cf98d28547f4f
ms.openlocfilehash: 3a240ff317e1b3ea450703965629c08053668856
ms.lasthandoff: 03/22/2017

---

# <a name="workflow-actions-and-triggers-for-azure-logic-apps"></a>Azure Logic Apps のワークフローのアクションとトリガー

ロジック アプリは、トリガーとアクションで構成されます。 6 種類のトリガーがあります。 トリガーのインターフェイスと動作は、種類ごとに異なります。 また、[ワークフロー定義言語](logic-apps-workflow-definition-language.md)の詳細についてもご覧ください。  
  
ここでは、トリガーとアクションの詳細、およびトリガーとアクションを使ってロジック アプリを作成し、仕事のプロセスとワークフローを改善する方法について説明します。  
  
### <a name="triggers"></a>トリガー  

トリガーは、ロジック アプリ ワークフローの実行を開始できる呼び出しを指定します。 ワークフローの実行を開始するには、次の 2 つの方法があります。  
  
-   ポーリング トリガー  

-   プッシュ トリガー - [ワークフロー サービス REST API](https://docs.microsoft.com/rest/api/logic/workflows) を呼び出します  
  
すべてのトリガーには、以下の最上位要素が含まれます。  
  
```json
"<name-of-the-trigger>" : {
    "type": "<type-of-trigger>",
    "inputs": { <settings-for-the-call> },
    "recurrence": {  
        "frequency": "Second|Minute|Hour|Week|Month|Year",
        "interval": "<recurrence interval in units of frequency>"
    },
    "conditions": [ <array-of-required-conditions > ],
    "splitOn" : "<property to create runs for>",
    "operationOptions": "<operation options on the trigger>"
}
```

### <a name="trigger-types-and-their-inputs"></a>トリガーの種類と入力  

以下の種類のトリガーを使うことができます。
  
-   **Request** \- ロジック アプリを、呼び出すエンドポイントにします  
  
-   **Recurrence** \- 定義されているスケジュールに基づいて起動します  
  
-   **HTTP** \- HTTP Web エンドポイントをポーリングします。 HTTP エンドポイントは、202 非同期パターンを使うか、またはアレイを返すことによって、特定のトリガー コントラクトに従う必要があります  
  
-   **ApiConnection** \- HTTP トリガーと同様にポーリングしますが、[Microsoft が管理する API](https://docs.microsoft.com/azure/connectors/apis-list) を利用します  
  
-   **HTTPWebhook** \- 手動トリガーと同様にエンドポイントを開きますが、指定された URL を呼び出して、登録および登録解除を行います  
  
-   **ApiConnectionWebhook** \- Microsoft が管理する API を利用して、HTTPWebhook トリガーと同様に動作します       
    各トリガーの種類には、その動作を定義する異なる**入力**のセットがあります。  
  
## <a name="request-trigger"></a>Request トリガー  

このトリガーは、ロジック アプリを起動するために HTTP 要求を介して呼び出すエンドポイントとして機能します。 Request トリガーの例を次に示します。  
  
```json
"<name-of-the-trigger>" : {
    "type" : "request",
    "kind": "http",
    "inputs" : {
        "schema" : {
            "properties" : {
                "myInputProperty1" : { "type" : "string" },
                "myInputProperty2" : { "type" : "number" }
            },
        "required" : [ "myInputProperty1" ],
        "type" : "object"
        }
    }
} 
```

**schema** という名前の省略可能なプロパティもあります。  
  
|要素名|必須|Description|  
|----------------|------------|---------------|  
|schema|なし|受信要求を検証する JSON スキーマです。 以降のワークフロー手順において参照するプロパティを特定するのに役立ちます。|

このエンドポイントを起動するには、*listCallbackUrl* API を呼び出す必要があります。 [ワークフロー サービスの REST API に関するページ](https://docs.microsoft.com/rest/api/logic/workflows)をご覧ください。  
  
## <a name="recurrence-trigger"></a>Recurrence トリガー  

Recurrence トリガーとは、定義されたスケジュールに基づいて実行するトリガーです。 Recurrence トリガーの例を次に示します。  

```json
"dailyReport" : {
    "type": "recurrence",
    "recurrence": {
        "frequency": "Day",
        "interval": "1"
    }
}
```

ご覧のように、ワークフローを実行する簡単な方法です。  
  
|要素名|必須|Description|  
|----------------|------------|---------------|  
|frequency|はい|トリガーを実行する頻度です。 second、minute、hour、day、week、month、year のいずれか 1 つだけを使うことができます。|  
|interval|はい|指定した頻度で繰り返し実行する間隔|  
|startTime|なし|startTime が UTC オフセットなしで指定されている場合、この timeZone が使われます。|  
|timeZone|×|startTime が UTC オフセットなしで指定されている場合、この timeZone が使われます。|  
  
将来のある時点で実行を開始するようにトリガーをスケジュールすることもできます。 たとえば、毎週月曜日に週間レポートを開始したい場合、次のトリガーを作成することにより、毎週月曜日に開始するようにロジック アプリをスケジュールできます。  

```json
"dailyReport" : {
    "type": "recurrence",
    "recurrence": {
        "frequency": "Week",
        "interval": "1",
        "startTime" : "2015-06-22T00:00:00Z"
    }
}
```

## <a name="http-trigger"></a>HTTP トリガー  

HTTP トリガーは、指定されたエンドポイントをポーリングし、応答を調べて、ワークフローを実行する必要があるかどうかを判断します。 入力オブジェクトは、HTTP 呼び出しを作成するために必要なパラメーターのセットを受け取ります。  
  
|要素名|必須|Description|型|  
|----------------|------------|---------------|--------|  
|method|○|HTTP メソッド GET、POST、PUT、DELETE、PATCH、HEAD のいずれかを指定できます。|String|  
|uri|○|呼び出す http または https エンドポイントです。 最大 2 キロバイトです。|String|  
|クエリ|なし|URL に追加するクエリ パラメーターを表すオブジェクトです。 たとえば、`"queries" : { "api-version": "2015-02-01" }` は `?api-version=2015-02-01` を URLに追加します。|オブジェクト|  
|headers|なし|要求に送信される各ヘッダーを表すオブジェクトです。 たとえば、言語と種類を要求に設定するには、次のようにします。`"headers" : { "Accept-Language": "en-us",  "Content-Type": "application/json" }`|オブジェクト|  
|body|いいえ|エンドポイントに送信されるペイロードを表すオブジェクトです。|オブジェクト|  
|retryPolicy|なし|4 xx または 5 xx エラーの再試行動作をカスタマイズできるオブジェクトです。|オブジェクト|  
|authentication|いいえ|要求の認証方法を表します。 このオブジェクトについて詳しくは、「[Scheduler 送信認証](https://docs.microsoft.com/azure/scheduler/scheduler-outbound-authentication)」をご覧ください。 スケジューラの他に、もう 1 つのサポートされるプロパティ `authority` があります。指定しないときの既定値は `https://login.windows.net` ですが、`https://login.windows\-ppe.net` などの別の対象を使うことができます。|オブジェクト|  
  
HTTP トリガーがロジック アプリでうまく動作するには、HTTP API が特定のパターンに準拠している必要があります。 次のフィールドが必要です。  
  
|応答|Description|  
|------------|---------------|  
|状態コード|状態コード 200 \(OK\) の場合は実行されます。 その他のすべての状態コードでは実行されません。|  
|Retry\-After ヘッダー|ロジック アプリがエンドポイントを再度ポーリングするまでの秒数です。|  
|Location ヘッダー|次のポーリング間隔で呼び出す URL です。 指定されていない場合は、元の URL が使われます。|  
  
異なる要求の種類に対する異なる動作の例を次に示します。  
  
|応答コード|Retry\-After|動作|  
|-----------------|----------------|------------|  
|200|"\(なし\)"|有効なトリガーではありませんが、Retry\-After は必要であり、ないとエンジンは次の要求をポーリングしません。|  
|202|60|ワークフローをトリガーしません。 1 分後に次の試行が行われます。|  
|200|10|ワークフローを実行し、10 秒後にさらにコンテンツがあるかどうか再確認します。|  
|400|"\(なし\)"|正しくない要求です。ワークフローを実行しないでください。 **再試行ポリシー**が定義されていない場合は、既定のポリシーが使われます。 再試行回数に達すると、トリガーは無効になります。|  
|500|"\(なし\)"|サーバー エラーです。ワークフローを実行しないでください。  **再試行ポリシー**が定義されていない場合は、既定のポリシーが使われます。 再試行回数に達すると、トリガーは無効になります。|  
  
HTTP トリガーの出力は次の例のようになります。  
  
|要素名|Description|型|  
|----------------|---------------|--------|  
|headers|HTTP 応答のヘッダーです。|オブジェクト|  
|body|HTTP 応答の本文です。|オブジェクト|  
  
## <a name="api-connection-trigger"></a>APIConnection トリガー  

APIConnection トリガーの基本的な機能は、HTTP トリガーに似ています。 ただし、アクションを識別するためのパラメーターは異なります。 たとえば次のようになります。  
  
```json
"dailyReport" : {
    "type": "ApiConnection",
    "inputs": {
        "host": {
            "api": {
                "runtimeUrl": "https://myarticles.example.com/"
            },
        }
        "connection": {
            "name": "@parameters('$connections')['myconnection'].name"
        }
    },  
    "method": "POST",
    "body": {
        "category": "awesomest"
    }
}
```

|要素名|必須|型|Description|  
|----------------|------------|--------|---------------|  
|host|はい||ApiApp でホストされたゲートウェイと ID。|  
|method|はい|String|HTTP メソッド **GET**、**POST**、**PUT**、**DELETE**、**PATCH**、**HEAD** のいずれかを指定できます。|  
|クエリ|いいえ|オブジェクト|URL に追加するクエリ パラメーターを表します。 たとえば、`"queries" : { "api-version": "2015-02-01" }` は `?api-version=2015-02-01` を URLに追加します。|  
|headers|いいえ|オブジェクト|要求に送信される各ヘッダーを表します。 たとえば、言語と種類を要求に設定するには、次のようにします。`"headers" : { "Accept-Language": "en-us",  "Content-Type": "application/json" }`|  
|body|いいえ|オブジェクト|エンドポイントに送信されるペイロードを表します。|  
|retryPolicy|なし|オブジェクト|4 xx または 5 xx エラーの再試行動作をカスタマイズできます。|  
|authentication|いいえ|オブジェクト|要求の認証方法を表します。 このオブジェクトについて詳しくは、「[Scheduler 送信認証](https://docs.microsoft.com/azure/scheduler/scheduler-outbound-authentication)」をご覧ください。|  
  
host のプロパティは次のとおりです。  
  
|要素名|必須|Description|  
|----------------|------------|---------------|  
|api runtimeUrl|はい|マネージ API のエンドポイントです。|  
|connection name||`$connection` という名前のパラメーターへの参照でなければならず、ワークフローが使うマネージ API 接続の名前です。|
  
APIConnection トリガーの出力は次のとおりです。
  
|要素名|型|Description|  
|----------------|--------|---------------|  
|headers|オブジェクト|HTTP 応答のヘッダーです。|  
|body|オブジェクト|HTTP 応答の本文です。|  
  
## <a name="httpwebhook-trigger"></a>HTTPWebhook トリガー  

HTTPWebhook トリガーは、手動トリガーと同様にエンドポイントを開きますが、指定された URL も呼び出して、登録および登録解除を行います HTTPWebhook トリガーの例を次に示します。  

```json
"myappspottrigger": {
    "type": "httpWebhook",
    "inputs": {
        "subscribe": {
            "method": "POST",
            "uri": "https://pubsubhubbub.appspot.com/subscribe",
            "headers": { },
            "body": {
                "hub.callback": "@{listCallbackUrl()}",
                "hub.mode": "subscribe",
                "hub.topic": "https://pubsubhubbub.appspot.com/articleCategories/technology"
            },
            "authentication": { },
            "retryPolicy": { }
        },
        "unsubscribe": {
            "url": "https://pubsubhubbub.appspot.com/subscribe",
            "body": {
                "hub.callback": "@{workflow().endpoint}@{listCallbackUrl()}",
                "hub.mode": "unsubscribe",
                "hub.topic": "https://pubsubhubbub.appspot.com/articleCategories/technology"
            },
            "method": "POST",
            "authentication": { }
        }
    },
    "conditions": [ ]
    }
```

これらのセクションの多くは省略可能であり、Webhook の動作は、どのセクションがが指定され、どのセクションが省略されたかによって異なります。  
Webhook のプロパティは次のとおりです。  
  
|要素名|必須|Description|  
|----------------|------------|---------------|  
|subscribe|いいえ|トリガーが作成されて初期登録を実行するときに呼び出される送信要求です。|  
|unsubscribe|なし|トリガーが削除されるときの送信要求です。|  
  
-   **subscribe** は、イベントのリッスンを開始するために行われる発信呼び出しです。 この呼び出しは、通常の HTTP アクションと同じパラメーターセットで開始します。 この発信呼び出しは、ワークフロー変更されると常に行われます。たとえば、資格情報が展開されたときや、トリガーの入力パラメーターが変化したときなどです。
  
    この呼び出しをサポートするために、新しい関数 `@listCallbackUrl()` があります。 この関数は、このワークフローでのこの特定のトリガーの一意の URL を返します。 サービス REST を使うエンドポイントの一意の ID を表します。  
  
-   **unsubscribe** は、操作によってこのトリガーが無効になると呼び出されます。次のような場合です。  
  
    -   トリガーを削除または無効にする  
  
    -   ワークフローを削除または無効にする  
  
    -   サブスクリプションを削除または無効にする  
  
    ロジック アプリは、サブスクリプション解除アクションを自動的に呼び出します。 この関数のパラメーターは、HTTP トリガーと同じです。  
  
    HTTPWebhook トリガーの出力は、受信要求の内容です。  
  
|要素名|型|Description|  
|-----------------|--------|---------------|  
|headers|オブジェクト|HTTP 要求のヘッダーです。|  
|body|オブジェクト|HTTP 要求の本文です。|  

Webhook アクションに対する制限は、[HTTP 非同期制限](#asynchronous-limits)とおなじ方法で指定できます。
  

## <a name="conditions"></a>条件  

すべてのトリガーについて、1 つまたは複数の条件を使って、ワークフローを実行する必要があるかどうかを決定できます。 次に例を示します。  

```json
"dailyReport" : {
    "type": "recurrence",
    "conditions": [ {
        "expression": "@parameters('sendReports')"
    } ],
    "recurrence": {
        "frequency": "Day",
        "interval": "1"
    }
}
```

この場合、ワークフローの `sendReports` パラメーターが true に設定されている間だけレポートはトリガーします。 最後に、条件はトリガーの状態コードを参照できます。 たとえば、Web サイトが状態コード 500 を返したときにのみ、ワークフローを開始できます。つぎはその例です。
  
```  
"conditions": [  
        {  
          "expression": "@equals(triggers().code, 'InternalServerError')"  
        }  
      ]  
```  
  
> [!NOTE]  
> いずれかの式がトリガーの状態コードを \(何であっても\) 参照すると、既定の動作 \(200 \(OK\) のときにのみトリガー\) が置き換えられます。 たとえば、状態コード 200 と状態コード 201 の両方でトリガーしたい場合は、条件として `@or(equals(triggers().code, 200),equals(triggers().code,201))` を含める必要があります。  
  
## <a name="start-multiple-runs-for-a-request"></a>1 つの要求に対して複数の実行を開始する

1 つの要求に対して複数の実行を開始するには、`splitOn` を使います。たとえば、ポーリング間隔の間に複数の新しい項目ができる可能性のあるエンドポイントをポーリングするような場合です。
  
`splitOn` では、項目の配列を含む応答ペイロード内のプロパティを指定します。各項目を、トリガーの実行の開始に使います。 たとえば、次の応答を返す API があるものとします。  
  
```json
{
    "Status" : "success",
    "Rows" : [
        {  
            "id" : 938109380,
            "name" : "mycoolrow"
        },
        {
            "id" : 938109381,
            "name" : "another row"
        }
    ]
}
```
  
ロジック アプリで必要なのは Rows の内容だけなので、次の例のようなトリガーを作成できます。  
  
```json
"mysplitter" : {
    "type" : "http",
    "recurrence": {
        "frequency": "Minute",
        "interval": "1"
    },
    "intputs" : {
        "uri" : "https://mydomain.com/myAPI",
        "method" : "GET"
    },
    "splitOn" : "@triggerBody()?.Rows"
}
```
  
その後、ワークフローので意義では、`@triggerBody().name` は第 1 の実行に対して `mycoolrow` を返し、第 2 の実行に対して `another row` を返します。 トリガーの出力は次の例のようになります。  
  
```json
{
    "body" : {
        "id" : 938109381,
        "name" : "another row"
    }
}
```

したがって、`SplitOn` を使う場合、配列の外側にあるプロパティ (この例では `Status` フィールド) を取得することはできません。  
  
> [!NOTE]  
> この例では、`?` 演算子を使って、`Rows` プロパティが存在しない場合のエラーを回避しています。 
  
## <a name="single-run-instance"></a>1 つの実行インスタンス

すべてのアクティブな実行が完了した場合にのみ発生するような反復プロパティを持つトリガーを構成できます。 まだ完了していない実行が存在する間にスケジュールされた反復が発生した場合、トリガーはスキップし、次のスケジュールされた反復間隔を再確認するまで待機します。

操作オプションを使ってこの設定を構成することができます。

```json
"triggers": {
    "mytrigger": {
        "type": "http",
        "inputs": { ... },
        "recurrence": { ... },
        "operationOptions": "singleInstance"
    }
}
```

## <a name="types-and-inputs"></a>種類と入力  

アクションには多くの種類があり、それぞれが固有の動作を持ちます。 コレクション アクションは、それ自体の中に他の多くのアクションを含むことができます。

### <a name="standard-actions"></a>標準アクション  

-   **HTTP** - このアクションは、HTTP Web エンドポイントを呼び出します。  
  
-   **ApiConnection** \- このアクションは HTTP アクションと同様に動作しますが、Microsoft が管理する API を使います。  
  
-   **ApiConnectionWebhook** \- HTTPWebhook と似ていますが、Microsoft が管理する API を使います。  
  
-   **Response** \- このアクションは、着信呼び出しの応答を定義します。  
  
-   **Wait** \- この簡単なアクションは、一定の時間または特定の時刻まで待機します。  
  
-   **Workflow** \- このアクションは、入れ子になったワークフローを表します。  

### <a name="collection-actions"></a>コレクション アクション

-   **Scope** \- このアクションは、他のアクションの論理的なグループです。

-   **Condition** \- このアクションは、式を評価して、対応する結果の分岐を実行します。

-   **ForEach** \- このループ アクションは、配列を反復処理し、各項目の内部アクションを実行します。

-   **Until** \- このループ アクションは、条件が true になるまで内部アクションを実行します。
  
どのアクションの種類にも、アクションの動作を定義する一連の異なる**入力**があります。  
  
## <a name="http-action"></a>HTTP アクション  

HTTP アクションは、指定されたエンドポイントを呼び出し、応答を調べて、ワークフローを実行する必要があるかどうかを判断します。 **入力**オブジェクトは、HTTP 呼び出しを作成するために必要なパラメーターのセットを受け取ります。  
  
|要素名|必須|型|説明|  
|----------------|------------|--------|---------------|  
|method|はい|String|HTTP メソッド **GET**、**POST**、**PUT**、**DELETE**、**PATCH**、**HEAD** のいずれかを指定できます。|  
|uri|はい|String|呼び出す http または https エンドポイントです。 最大長は 2 キロバイトです。|  
|クエリ|なし|オブジェクト|URL に追加するクエリ パラメーターを表します。 たとえば、`"queries" : { "api-version": "2015-02-01" }` は `?api-version=2015-02-01` を URLに追加します。|  
|headers|いいえ|オブジェクト|要求に送信される各ヘッダーを表します。 たとえば、言語と種類を要求に設定するには、次のようにします。`"headers" : { "Accept-Language": "en-us",  "Content-Type": "application/json" }`|  
|body|なし|オブジェクト|エンドポイントに送信されるペイロードを表します。|  
|retryPolicy|いいえ|オブジェクト|4 xx または 5 xx エラーの再試行動作をカスタマイズできます。|  
|operationsOptions|なし|String|オーバーライドする特殊な動作のセットを定義します。|  
|authentication|いいえ|オブジェクト|要求の認証方法を表します。 このオブジェクトについて詳しくは、「[Scheduler 送信認証](https://docs.microsoft.com/azure/scheduler/scheduler-outbound-authentication)」をご覧ください。 スケジューラ以外にもう 1 つサポートされるプロパティ、`authority` があります。 指定しない場合の既定は `https://login.windows.net` ですが、`https://login.windows\-ppe.net` のような異なる対象を使うことができます。|  
  
HTTP \(および APIConnection\) アクションは、再試行ポリシーをサポートします。 再試行ポリシーは、接続の例外に加えて、HTTP 状態コード 408、429、5 xx などの断続的エラーに適用されます。 このポリシーは、次のように定義される *retryPolicy* オブジェクトを使って記述します。
  
```json
"retryPolicy" : {
    "type": "<type-of-retry-policy>",
    "interval": <retry-interval>,
    "count": <number-of-retry-attempts>
}
```
  
再試行間隔は、ISO 8601 形式で指定します。 この間隔の既定値および最小値は 20 秒で、最大値は 1 時間です。 既定および最大の再試行回数は 4 回です。 再試行ポリシーの定義を指定しないと、既定の再試行回数と間隔の値で `fixed` 戦略が使われます。 再試行ポリシーを無効にするには、種類を `None` に設定します。  
  
たとえば、最新のニュースをフェッチする次のアクションは、断続的エラーが発生した場合は 30 秒の再試行間隔で 2 回再試行を行うので、実行回数は全部で 3 回になります。  
  
```json
"latestNews" : {
    "type": "http",
    "inputs": {
        "method": "GET",
        "uri": "uri": "https://mynews.example.com/latest",
        "retryPolicy" : {
            "type": "fixed",
            "interval": "PT30S",
            "count": 2
        }
    }
}
```
### <a name="asynchronous-patterns"></a>非同期パターン

既定では、HTTP ベースのすべてのアクションは、標準的な非同期操作パターンをサポートします。 したがって、リモート サーバーが要求の処理を応答 202 \(Accepted\) で受け付けた場合、Logic Apps エンジンは、終了状態 \(202 以外の応答\) になるまで、応答の location ヘッダーで指定された URL へのポーリングを続けます。  
  
前に説明した非同期動作を無効にするには、アクションの入力で `DisableAsyncPattern` オプションを設定します。 この場合、アクションの出力は、サーバーからの最初の 202 応答に基づきます。  
  
```json
"invokeLongRunningOperation" : {
    "type": "http",
    "inputs": {
        "method": "POST",
        "uri": "https://host.example.com/resources"
    },
    "operationOptions": "DisableAsyncPattern"
}
```

#### <a name="asynchronous-limits"></a>非同期の制限

非同期パターンは、特定の期間に制限できます。  その期間内に終了状態にならなかった場合、アクションの状態は `ActionTimedOut` コードで `Cancelled` に設定されます。  タイムアウト制限は、ISO 8601 形式で指定します。  制限は次の構文で指定できます。

``` json
"<action-name>": {
    "type": "workflow|webhook|http|apiconnectionwebhook|apiconnection",
    "inputs": { },
    "limit": {
        "timeout": "PT10S"
    }
}
```
  
## <a name="api-connection"></a>API 接続  

API 接続は、Microsoft で管理されたコネクタを参照するアクションです。
このアクションには、有効な接続の参照と、API およびパラメーターについての情報が必要です。

|要素名|必須|型|Description|  
|----------------|------------|--------|---------------|  
|host|はい|オブジェクト|runtimeUrl などのコネクタ情報と、接続オブジェクトへの参照を表します。|
|method|はい|String|HTTP メソッド **GET**、**POST**、**PUT**、**DELETE**、**PATCH**、**HEAD** のいずれかを指定できます。|  
|path|はい|String|API 操作のパスです。|  
|クエリ|いいえ|オブジェクト|URL に追加するクエリ パラメーターを表します。 たとえば、`"queries" : { "api-version": "2015-02-01" }` は `?api-version=2015-02-01` を URLに追加します。|  
|headers|いいえ|オブジェクト|要求に送信される各ヘッダーを表します。 たとえば、言語と種類を要求に設定するには、次のようにします。`"headers" : { "Accept-Language": "en-us",  "Content-Type": "application/json" }`|  
|body|いいえ|オブジェクト|エンドポイントに送信されるペイロードを表します。|  
|retryPolicy|いいえ|オブジェクト|4 xx または 5 xx エラーの再試行動作をカスタマイズできます。|  
|operationsOptions|いいえ|String|オーバーライドする特殊な動作のセットを定義します。|  

```json
"Send_Email": {
    "type": "apiconnection",
    "inputs": {
        "host": {
            "api": {
                "runtimeUrl": "https://logic-apis-df.azure-apim.net/apim/office365"
            },
            "connection": {
                "name": "@parameters('$connections')['office365']['connectionId']"
            }
        },
        "method": "post",
        "body": {
            "Subject": "New Tweet from @{triggerBody()['TweetedBy']}",
            "Body": "@{triggerBody()['TweetText']}",
            "To": "me@example.com"
        },
        "path": "/Mail"
    },
    "runAfter": {}
    }
```

## <a name="api-connection-webhook-action"></a>API 接続 Webhook アクション

```json
"Send_approval_email": {
    "type": "apiconnectionwebhook",
    "inputs": {
        "host": {
            "api": {
                "runtimeUrl": "https://logic-apis-df.azure-apim.net/apim/office365"
            },
            "connection": {
                "name": "@parameters('$connections')['office365']['connectionId']"
            }
        },
        "body": {
            "Message": {
                "Subject": "Approval Request",
                "Options": "Approve, Reject",
                "Importance": "Normal",
                "To": "me@email.com"
            }
        },
        "path": "/approvalmail",
        "authentication": "@parameters('$authentication')"
    },
    "runAfter": {}
}
```

Webhook アクションに対する制限は、[HTTP 非同期制限](#asynchronous-limits)とおなじ方法で指定できます。
  
## <a name="response-action"></a>応答アクション  

このアクションの種類には、HTTP 要求からの応答ペイロード全体と、状態コード、本文、およびヘッダーが含まれています。  
  
```json
"myresponse" : {
    "type" : "response",
    "inputs" : {
        "statusCode" : 200,
        "body" : {
            "contentFieldOne" : "value100",
            "anotherField" : 10.001
        },
        "headers" : {
            "x-ms-date" : "@utcnow()",
            "Content-type" : "application/json"
        }
    },
    "runAfter": {}
}
```
  
応答アクションには、他のアクションには適用されない特別な制限があります。 具体的には次の処理が行われます。  
  
-   受信した要求に対しては確定的な応答が必要であるため、応答アクションを並列に定義することはできません。  
  
-   受信した要求が応答を受け経った後で応答アクションに達した場合、アクションは失敗 \(競合\) と見なされ、その結果、実行は `Failed` になります。  
  
-   応答アクションのあるワークフローでは、1 回の呼び出しに対して多数の実行が発生する `splitOn` をトリガーで使うことはできません。 そのため、フローが PUT で不正要求になるときは、これを確認する必要があります。  
  
## <a name="wait-action"></a>待機アクション  

`wait` アクションは、指定した時間だけワークフローの実行を中断します。 たとえば、15 分間待機するには、次のスニペットを使うことができます。  
  
```json
"waitForFifteenMinutes" : {
    "type": "wait",
    "inputs": {
        "interval": {
            "unit" : "minute",
            "count" : 15
        }
    }
}
```  
  
または、特定の時点まで待機するには、次の例を使うことができます。  
  
```json
"waitUntilOctober" : {
    "type": "wait",
    "inputs": {
        "until": {
            "timestamp" : "2016-10-01T00:00:00Z"
        }
    }
}
```
  
> [!NOTE]  
> 待機期間は **interval** オブジェクトまたは **until** オブジェクトを使って指定できますが、両方を同時に使うことはできません。  
  
|名前|必須|型|Description|  
|--------|------------|--------|---------------|  
|interval|なし|オブジェクト|時間に基づく待機期間です。|  
|interval unit|はい|String|second、minute、hour、day、week、month、year のいずれかです。|  
|interval count|はい|String|指定された内部単位に基づく期間です。|  
|until|いいえ|オブジェクト|特定時刻に基づく待機期間です。|  
|until timestamp|はい|String|待機の期限を示す UTC の時刻です。|  

## <a name="query-action"></a>クエリ アクション

`query` アクションでは、条件に基づいて配列をフィルター処理できます。 たとえば、2 より大きい数値を選ぶには、次のようにします。

```json
"FilterNumbers" : {
    "type": "query",
    "inputs": {
        "from": [ 1, 3, 0, 5, 4, 2 ],
        "where": "@greater(item(), 2)"
    }
}
```

`query` アクションからの出力は、入力配列の要素のうち条件を満たす要素の配列です。

> [!NOTE]
> `where` の条件を満たす値がない場合、結果は空の配列になります。

|名前|必須|型|Description|
|--------|------------|--------|---------------|
|ファイル|はい|array|ソース配列です。|
|各値の説明:|はい|String|ソース配列の各要素に適用する条件です。|

## <a name="terminate-action"></a>終了アクション

終了アクションは、ワークフローの実行を停止し、実行中のアクションを中止して、残りのアクションをスキップします。 たとえば、状態 **Failed** で実行を終了するには、次のスニペットを使うことができます。

```json
"HandleUnexpectedResponse" : {
    "type": "terminate",
    "inputs": {
        "runStatus" : "failed",
        "runError": {
            "code": "UnexpectedResponse",
            "message": "Received an unexpected response.",
        }
    }
}
```

> [!NOTE]
> 既に完了したアクションは、終了アクションの影響を受けません。

|名前|必須|型|Description|
|--------|------------|--------|---------------|
|runStatus|はい|String|ターゲットの実行状態です。 **Failed** または **Cancelled** です。|
|runError|なし|オブジェクト|エラーの詳細です。 **runStatus** が **Failed** に設定されている場合にのみサポートされます。|
|runError code|なし|String|実行エラー コードです。|
|runError message|いいえ|String|実行エラー メッセージです。|

## <a name="compose-action"></a>作成アクション

作成アクションでは、任意のオブジェクトを作成できます。 作成アクションの出力は、その入力の評価結果です。 たとえば、作成アクションを使って、複数のアクションの出力をマージできます。

```json
"composeUserRecord" : {
    "type": "compose",
    "inputs": {
        "firstName": "@actions('getUser').firstName",
        "alias": "@actions('getUser').alias",
        "thumbnailLink": "@actions('lookupThumbnail').url"
        }
    }
}
```

> [!NOTE]
> **Compose** アクションを使うと、オブジェクト、配列、およびロジック アプリによってネイティブにサポートされている XML やバイナリなどの他の型など、任意の出力を作成できます。

## <a name="workflow-action"></a>ワークフロー アクション   

|名前|必須|型|説明|  
|--------|------------|--------|---------------|  
|host id|はい|String|呼び出すワークフローのリソース ID です。|  
|host triggerName|はい|String|呼び出すトリガーの名前です。|  
|クエリ|いいえ|オブジェクト|URL に追加するクエリ パラメーターを表します。 たとえば、`"queries" : { "api-version": "2015-02-01" }` は `?api-version=2015-02-01` を URLに追加します。|  
|headers|いいえ|オブジェクト|要求に送信される各ヘッダーを表します。 たとえば、言語と種類を要求に設定するには、次のようにします。`"headers" : { "Accept-Language": "en-us",  "Content-Type": "application/json" }`|  
|body|いいえ|オブジェクト|エンドポイントに送信されるペイロードを表します。|  
  
```json
"mynestedwf" : {
    "type" : "workflow",
    "inputs" : {
        "host" : {
            "id" : "/subscriptions/xxxxyyyyzzz/resourceGroups/rg001/providers/Microsoft.Logic/mywf001",
            "triggerName " : "mytrigger001"
        },
        "queries" : {
            "extrafield" : "specialValue"
        },  
        "headers" : {
            "x-ms-date" : "@utcnow()",
            "Content-type" : "application/json"
        },
        "body" : {
            "contentFieldOne" : "value100",
            "anotherField" : 10.001
        }
    },
    "runAfter": {}
    }
```
  
ワークフロー \(より具体的にはトリガー\) に対するアクセス チェックが行われます。つまり、ワークフローに対するアクセス権が必要です。  
  
`workflow` アクションからの出力は、子ワークフローの `response` アクションで定義されているアクションに基づきます。 `response` アクションを定義していない場合、出力は空になります。  

## <a name="collection-actions-scopes-and-loops"></a>コレクション アクション (スコープとループ)

一部のアクションの種類は、それ自身にアクションを含めることができます。 コレクション内の参照アクションは、コレクションの外部で直接参照できます。 スコープで `http` を定義した場合でも、`@body('http')` はワークフローの任意の場所で有効です。 コレクション内のアクションは、同じコレクション内の他のアクションの `runAfter` のみ可能です。

## <a name="scope-action"></a>スコープ アクション

`scope` アクションを使うと、ワークフロー内のアクションを論理的にグループ化できます。

|名前|必須|型|説明|  
|--------|------------|--------|---------------|  
|アクション|はい|オブジェクト|スコープ内で実行する内部アクションです。|

```json
{
    "myScope": {
        "type": "scope",
        "actions": {
            "call_bing": {
                "type": "http",
                "inputs": {
                    "url": "http://www.bing.com"
                }
            }
        }
    }
}
```

## <a name="foreach-action"></a>ForEach アクション

このループ アクションは、配列を反復処理し、各項目の内部アクションを実行します。 既定では、ForEach ループは並列に実行します (一度に 20 のアクションを並列実行)。 `operationOptions` パラメーターを使って実行ルールを設定できます。

|名前|必須|型|Description|  
|--------|------------|--------|---------------|  
|アクション|はい|オブジェクト|ループ内で実行する内部アクションです。|
|foreach|はい|string|反復処理する配列です。|
|operationOptions|×|string|動作に対する任意の操作オプションです。 現在は、反復を順番に実行する `sequential` のみがサポートされています (既定の動作は並列)。|

```json
"forEach_email": {
    "type": "foreach",
    "foreach": "@body('email_filter')",
    "actions": {
        "send_email": {
            "type": "ApiConnection",
            "inputs": {
                "body": {
                    "to": "@item()",
                    "from": "me@contoso.com",
                    "message": "Hello, thank you for ordering"
                }
                "host": {
                    "connection": {
                        "id": "@parameters('$connections')['office365']['connection']['id']"
                    }
                }
            }
        }
    },
    "runAfter":{
        "email_filter": [ "Succeeded" ]
    }
}
```

## <a name="until-action"></a>Until アクション

このループ アクションは、条件が true になるまで内部アクションを実行します。

|名前|必須|型|説明|  
|--------|------------|--------|---------------|  
|アクション|はい|オブジェクト|ループ内で実行する内部アクションです。|
|expression|はい|string|反復のたびに評価する式です。|
|limit|○|オブジェクト|ループの制限です。少なくとも 1 つの制限を定義する必要があります。|
|count|×|int|実行できる反復の数の制限です。|
|timeout|×|string|ループのタイムアウト時間です。  ISO 8601 形式です。|


```json
 "Until_succeeded": {
    "actions": {
        "Http": {
            "inputs": {
                "method": "GET",
                "uri": "http://myurl"
            },
            "runAfter": {},
            "type": "Http"
        }
    },
    "expression": "@equals(outputs('Http')['statusCode', 200)",
    "limit": {
        "count": 1000,
        "timeout": "PT1H"
    },
    "runAfter": {},
    "type": "Until"
}
```

## <a name="conditions---if-action"></a>条件 - If アクション

`If` アクションを使うと、条件を評価し、式が `true` として評価されたかどうかに基づいて分岐を実行できます。

|名前|必須|型|Description|  
|--------|------------|--------|---------------|  
|アクション|はい|オブジェクト|式が `true` に評価されるときに実行する内部アクションです。|
|expression|はい|string|評価する式です。|
|else|×|オブジェクト|式が `false` に評価されるときに実行する内部アクションです。|
  
```json
"My_condition": {
    "actions": {
        "If_true": {
            "inputs": {
                "method": "GET",
                "uri": "http://myurl"
            },
            "runAfter": {},
            "type": "Http"
        }
    },
    "else": {
        "actions": {
            "if_false": {
                "inputs": {
                    "method": "GET",
                    "uri": "http://myurl"
                },
                "runAfter": {},
                "type": "Http"
            }
        }
    },
    "expression": "@equals(triggerBody(), json(true))",
    "runAfter": {},
    "type": "If"
}
```  
  
次の表では、条件でアクションの式を使う方法の例を示します。  
  
|JSON 値|結果|  
|--------------|----------|  
|`"expression": "@parameters('hasSpecialAction')"`|true として評価されるすべての値は、この条件に合格します。 ブール式のみがサポートされています。 他の型をブール値に変換するには、`empty`、`equals` 関数を使います。|  
|`"expression": "@greater(actions('act1').output.value, parameters('threshold'))"`|比較関数がサポートされています。 この例では、act1 の出力がしきい値より大きい場合にのみ、アクションが実行されます。|  
|`"expression": "@or(greater(actions('act1').output.value, parameters('threshold')), less(actions('act1').output.value, 100))"`|入れ子になったブール式を作成するため、論理関数もサポートされます。 この場合、act1 の出力がしきい値より大きいか、または 100 未満の場合に、アクションが実行されます。|  
|`"expression": "@equals(length(actions('act1').outputs.errors), 0))"`|配列関数を使って、配列に項目があるかどうかを確認できます。 この場合、エラー配列が空の場合に、アクションが実行されます。| 
|`"expression": "parameters('hasSpecialAction')"`|エラー - 条件には @ が必要であるため、有効な条件ではありません。|  
  
条件が正常に評価されると、条件は `Succeeded` とマークされます。 `actions` または `else` オブジェクト内のアクションは、正常に実行される `Succeeded` に、実行が失敗すると `Failed` に、その分岐が実行されないと `Skipped` に、それぞれ設定されます。

## <a name="next-steps"></a>次のステップ

[ワークフロー サービスの REST API](https://docs.microsoft.com/rest/api/logic/workflows)
