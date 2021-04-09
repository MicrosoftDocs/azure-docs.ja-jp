---
title: Azure Functions のカスタム ハンドラー
description: 任意の言語またはランタイム バージョンで Azure Functions を使用する方法について説明します。
author: anthonychu
ms.author: antchu
ms.date: 12/1/2020
ms.topic: article
ms.openlocfilehash: dd112c74ea9f013a0e14bddd735060ddbf73c14e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "100578528"
---
# <a name="azure-functions-custom-handlers"></a>Azure Functions のカスタム ハンドラー

すべてのFunction App は、言語固有のハンドラーによって実行されます。 Azure Functions では多くの[言語ハンドラー](./supported-languages.md)が既定でサポートされていますが、他の言語やランタイムを使用したい場合もあります。

カスタム ハンドラーは、Functions ホストからイベントを受信する軽量の Web サーバーです。 HTTP プリミティブをサポートするすべての言語で、カスタム ハンドラーを実装できます。

カスタム ハンドラーは、次のような場合に最適です。

- Go や Rust など、現在サポートされていない言語でFunction App を実装する。
- Deno など、現在サポートされていないランタイムでFunction App を実装する。

カスタム ハンドラーを使用すると、[拡張バンドル](./functions-bindings-register.md)を介して、[トリガーと入出力バインド](./functions-triggers-bindings.md)を使用できます。

[Go および Rust のクイックスタート](create-first-function-vs-code-other.md)を使用して Azure Functions カスタム ハンドラーの使用を開始します。

## <a name="overview"></a>概要

次の図は、カスタム ハンドラーとして実装されている Web サーバーと Functions ホストの関係を示しています。

![Azure Functions のカスタム ハンドラーの概要](./media/functions-custom-handlers/azure-functions-custom-handlers-overview.png)

1. 各イベントは、Functions ホストに送信される要求をトリガーします。 イベントは、Azure Functions によってサポートされている任意のトリガーです。
1. Functions ホストは、その後、Web サーバーに[要求ペイロード](#request-payload)を発行します。 このペイロードには、トリガーおよび入力バインド データと、関数のためのその他のメタデータが保持されます。
1. Web サーバーは個々の関数を実行し、[応答ペイロード](#response-payload)を Functions ホストに返します。
1. Functions ホストは、応答のデータを処理するために関数の出力バインドに渡します。

カスタム ハンドラーとして実装されている Azure Functions アプリでは、いくつかの規則に従って、*host.json*、*local.settings.json*、および *function.json* ファイルを構成する必要があります。

## <a name="application-structure"></a>アプリケーション構造

カスタム ハンドラーを実装するには、アプリケーションに次の要素が必要です。

- *host.json* ファイル: アプリのルート
- *local.settings.json* ファイル: アプリのルート
- *function.json* ファイル: 関数ごとに必要 (関数名と一致する名前のフォルダー内)
- コマンド、スクリプト、または実行可能ファイル: Web サーバーを実行

次の図は、"MyQueueFunction" という名前の関数と、*handler.exe* という名前のカスタム ハンドラー実行可能ファイルのファイル システムでこれらのファイルがどのように表示されるかを示します。

```bash
| /MyQueueFunction
|   function.json
|
| host.json
| local.settings.json
| handler.exe
```

### <a name="configuration"></a>構成

アプリケーションは、*host.json* および *local.settings.json* ファイルを使用して構成されます。

#### <a name="hostjson"></a>host.json

*host.json* は、HTTP イベントを処理できる Web サーバーを指すことによって、要求の送信先を Functions ホストに指示します。

*host.json* ファイルの `customHandler` セクションで Web サーバーを実行する方法の詳細を構成することにより、カスタム ハンドラーを定義します。

```json
{
  "version": "2.0",
  "customHandler": {
    "description": {
      "defaultExecutablePath": "handler.exe"
    }
  }
}
```

`customHandler` セクションは、`defaultExecutablePath` で定義されているターゲットを指します。 実行ターゲットは、Web サーバーが実装されているコマンド、実行可能ファイル、またはファイルにすることができます。

`arguments` 配列を使用して、引数を実行可能ファイルに渡します。 引数では、`%%` 表記を使用した環境変数 (アプリケーション設定) の展開がサポートされています。

また、`workingDirectory` を使用して、実行可能ファイルによって使用される作業ディレクトリを変更することもできます。

```json
{
  "version": "2.0",
  "customHandler": {
    "description": {
      "defaultExecutablePath": "app/handler.exe",
      "arguments": [
        "--database-connection-string",
        "%DATABASE_CONNECTION_STRING%"
      ],
      "workingDirectory": "app"
    }
  }
}
```

##### <a name="bindings-support"></a>バインドのサポート

標準トリガーと入出力バインドは、*host.json* ファイル内の [拡張バンドル](./functions-bindings-register.md)を参照することによって利用できます。

#### <a name="localsettingsjson"></a>local.settings.json

*local.settings.json* では、Function App をローカルで実行する際に使用されるアプリケーション設定を定義します。 シークレットが含まれる可能性があるため、*local.settings.json* はソース管理から除外する必要があります。 Azure では、代わりにアプリケーション設定を使用します。

カスタム ハンドラーの場合、*local.settings.json* で `FUNCTIONS_WORKER_RUNTIME` を `Custom` に設定します。

```json
{
  "IsEncrypted": false,
  "Values": {
    "FUNCTIONS_WORKER_RUNTIME": "Custom"
  }
}
```

### <a name="function-metadata"></a>関数のメタデータ

*function.json* の内容をカスタム ハンドラーと共に使用する場合、その内容は他のコンテキストで関数を定義する場合と変わりありません。 唯一の要件は、*function.json* ファイルは、関数名と一致する名前のフォルダーに格納しなければならないということです。

次の *function.json* では、キュー トリガーとキュー出力バインドを含む関数を構成しています。 *MyQueueFunction* という名前のフォルダー内にあるため、*MyQueueFunction* という名前の関数が定義されます。

**MyQueueFunction/function.json**

```json
{
  "bindings": [
    {
      "name": "myQueueItem",
      "type": "queueTrigger",
      "direction": "in",
      "queueName": "messages-incoming",
      "connection": "AzureWebJobsStorage"
    },
    {
      "name": "$return",
      "type": "queue",
      "direction": "out",
      "queueName": "messages-outgoing",
      "connection": "AzureWebJobsStorage"
    }
  ]
}
```

### <a name="request-payload"></a>要求ペイロード

キュー メッセージを受信すると、Functions ホストはカスタム ハンドラーに、ペイロードが本文にある HTTP POST 要求を送信します。

次のコードは、要求ペイロードのサンプルを表します。 ペイロードには、2 つのメンバー (`Data` と `Metadata`) を持つ JSON 構造体が含まれています。

`Data` メンバーには、*function.json* ファイルにバインド配列の形式で定義されている入力名およびトリガー名と一致するキーが含まれています。

`Metadata` メンバーには、[イベント ソースから生成されたメタデータ](./functions-bindings-expressions-patterns.md#trigger-metadata)が含まれています。

```json
{
  "Data": {
    "myQueueItem": "{ message: \"Message sent\" }"
  },
  "Metadata": {
    "DequeueCount": 1,
    "ExpirationTime": "2019-10-16T17:58:31+00:00",
    "Id": "800ae4b3-bdd2-4c08-badd-f08e5a34b865",
    "InsertionTime": "2019-10-09T17:58:31+00:00",
    "NextVisibleTime": "2019-10-09T18:08:32+00:00",
    "PopReceipt": "AgAAAAMAAAAAAAAAAgtnj8x+1QE=",
    "sys": {
      "MethodName": "QueueTrigger",
      "UtcNow": "2019-10-09T17:58:32.2205399Z",
      "RandGuid": "24ad4c06-24ad-4e5b-8294-3da9714877e9"
    }
  }
}
```

### <a name="response-payload"></a>応答ペイロード:

規則により、関数の応答はキーと値のペアの形式で設定されます。 サポートされているキーは次のとおりです。

| <nobr>ペイロードのキー</nobr>   | データ型 | 解説                                                      |
| ------------- | --------- | ------------------------------------------------------------ |
| `Outputs`     | object    | *function.json* の `bindings` 配列によって定義される応答値を保持します。<br /><br />たとえば、関数が "myQueueOutput" という名前のキュー出力バインドを使用して構成されている場合、`Outputs` には、`myQueueOutput` という名前のキーが格納されます。これは、カスタム ハンドラーによって、キューに送信されるメッセージに設定されます。 |
| `Logs`        | array     | Functions の呼び出しログにメッセージが表示されます。<br /><br />Azure で実行すると、メッセージは Application Insights に表示されます。 |
| `ReturnValue` | string    | *function.json* ファイルの `$return` として出力が構成されている場合に、応答を提供するために使用されます。 |

これは、応答ペイロードの例です。

```json
{
  "Outputs": {
    "res": {
      "body": "Message enqueued"
    },
    "myQueueOutput": [
      "queue message 1",
      "queue message 2"
    ]
  },
  "Logs": [
    "Log message 1",
    "Log message 2"
  ],
  "ReturnValue": "{\"hello\":\"world\"}"
}
```

## <a name="examples"></a>例

カスタム ハンドラーは、HTTP イベントの受信をサポートする任意の言語で実装できます。 以下の例では、Go プログラミング言語を使用してカスタム ハンドラーを実装する方法を示しています。

### <a name="function-with-bindings"></a>関数とバインド

この例で実装されているシナリオは、商品の注文を表すペイロードを持つ `POST` を受け入れる `order` という名前の関数を特徴としています。 関数に注文がポストされると、Queue Storage メッセージが作成され、HTTP 応答が返されます。

<a id="bindings-implementation" name="bindings-implementation"></a>

#### <a name="implementation"></a>実装

*order* という名前のフォルダー内の *function.json* ファイルで、HTTP によってトリガーされる関数を構成します。

**order/function.json**

```json
{
  "bindings": [
    {
      "type": "httpTrigger",
      "direction": "in",
      "name": "req",
      "methods": ["post"]
    },
    {
      "type": "http",
      "direction": "out",
      "name": "res"
    },
    {
      "type": "queue",
      "name": "message",
      "direction": "out",
      "queueName": "orders",
      "connection": "AzureWebJobsStorage"
    }
  ]
}
```

この関数は [HTTP によってトリガーされる関数](./functions-bindings-http-webhook-trigger.md)として定義され、[HTTP 応答](./functions-bindings-http-webhook-output.md) を返し、[Queue Storage](./functions-bindings-storage-queue-output.md) メッセージを出力します。

アプリのルートにある *host.json* ファイルは、`handler.exe` (Linux または macOS の場合は `handler`) という名前の実行可能ファイルを実行するように構成されています。

```json
{
  "version": "2.0",
  "customHandler": {
    "description": {
      "defaultExecutablePath": "handler.exe"
    }
  },
  "extensionBundle": {
    "id": "Microsoft.Azure.Functions.ExtensionBundle",
    "version": "[1.*, 2.0.0)"
  }
}
```

これは、Functions ランタイムに送信される HTTP 要求です。

```http
POST http://127.0.0.1:7071/api/order HTTP/1.1
Content-Type: application/json

{
  "id": 1005,
  "quantity": 2,
  "color": "black"
}
```

その後、Functions ランタイムは次の HTTP 要求をカスタム ハンドラーに送信します。

```http
POST http://127.0.0.1:<FUNCTIONS_CUSTOMHANDLER_PORT>/order HTTP/1.1
Content-Type: application/json

{
  "Data": {
    "req": {
      "Url": "http://localhost:7071/api/order",
      "Method": "POST",
      "Query": "{}",
      "Headers": {
        "Content-Type": [
          "application/json"
        ]
      },
      "Params": {},
      "Body": "{\"id\":1005,\"quantity\":2,\"color\":\"black\"}"
    }
  },
  "Metadata": {
  }
}
```

> [!NOTE]
> 簡潔にするために、ペイロードの一部は削除されています。

*handler.exe* はコンパイル済みの Go カスタム ハンドラー プログラムで、Web サーバーを実行し、Functions ホストからの関数呼び出し要求に応答します。

```go
package main

import (
    "encoding/json"
    "fmt"
    "log"
    "net/http"
    "os"
)

type InvokeRequest struct {
    Data     map[string]json.RawMessage
    Metadata map[string]interface{}
}

type InvokeResponse struct {
    Outputs     map[string]interface{}
    Logs        []string
    ReturnValue interface{}
}

func orderHandler(w http.ResponseWriter, r *http.Request) {
    var invokeRequest InvokeRequest

    d := json.NewDecoder(r.Body)
    d.Decode(&invokeRequest)

    var reqData map[string]interface{}
    json.Unmarshal(invokeRequest.Data["req"], &reqData)

    outputs := make(map[string]interface{})
    outputs["message"] = reqData["Body"]

    resData := make(map[string]interface{})
    resData["body"] = "Order enqueued"
    outputs["res"] = resData
    invokeResponse := InvokeResponse{outputs, nil, nil}

    responseJson, _ := json.Marshal(invokeResponse)

    w.Header().Set("Content-Type", "application/json")
    w.Write(responseJson)
}

func main() {
    customHandlerPort, exists := os.LookupEnv("FUNCTIONS_CUSTOMHANDLER_PORT")
    if !exists {
        customHandlerPort = "8080"
    }
    mux := http.NewServeMux()
    mux.HandleFunc("/order", orderHandler)
    fmt.Println("Go server Listening on: ", customHandlerPort)
    log.Fatal(http.ListenAndServe(":"+customHandlerPort, mux))
}
```

この例では、カスタム ハンドラーは、HTTP イベントを処理するために Web サーバーを実行し、`FUNCTIONS_CUSTOMHANDLER_PORT` を介して要求をリッスンするように設定されています。

Functions ホストが `/api/order` で元の HTTP 要求を受信した場合でも、関数名 (そのフォルダー名) を使用してカスタム ハンドラーを呼び出します。 この例では、この関数が `/order` のパスで定義されています。 ホストは、`/order` のパスで HTTP 要求をカスタム ハンドラーに送信します。

`POST` 要求がこの関数に送信されると、トリガーのデータと関数のメタデータは、HTTP 要求本文を介して使用できるようになります。 元の HTTP 要求本文には、ペイロードの `Data.req.Body` でアクセスできます。

関数の応答は、キーと値のペアの形式で設定されます。この場合、`Outputs` メンバーは、*function.json* ファイルで定義されている出力とキーが一致する JSON 値を保持します。

これは、このハンドラーが Functions ホストに返すペイロードの例です。

```json
{
  "Outputs": {
    "message": "{\"id\":1005,\"quantity\":2,\"color\":\"black\"}",
    "res": {
      "body": "Order enqueued"
    }
  },
  "Logs": null,
  "ReturnValue": null
}
```

`message` 出力を、要求から送信された注文データと同じになるよう設定することで、この関数は、構成されたキューにその注文データを出力します。 また、Functions ホストは、`res` で構成された HTTP 応答を呼び出し元に返します。

### <a name="http-only-function"></a>HTTP のみの関数

追加のバインドや出力がない HTTP トリガー関数の場合、カスタム ハンドラーの[要求](#request-payload)および[応答](#response-payload)ペイロードではなく HTTP 要求と応答をハンドラーで直接操作したい場合があります。 この動作は、*host.json* で `enableForwardingHttpRequest` 設定を使用して構成できます。

> [!IMPORTANT]
> カスタム ハンドラー機能の主な目的は、現在 Azure Functions で最高レベルのサポートを受けていない言語とランタイムを有効にすることです。 カスタム ハンドラーを使用して Web アプリケーションを実行することはできますが、Azure Functions は標準のリバース プロキシではありません。 応答ストリーミング、HTTP/2、WebSocket など、一部の機能は使用できません。 HTTP 要求の一部のコンポーネント (特定のヘッダーやルートなど) が制限される場合があります。 アプリケーションで[コールド スタート](event-driven-scaling.md#cold-start)が過剰に発生する場合もあります。
>
> このような状況に対処するには、Web アプリを [Azure App Service](../app-service/overview.md) で実行することを検討してください。

次の例では、追加のバインドまたは出力を使用せずに、HTTP によってトリガーされる関数を構成する方法を示します。 この例で実装されているシナリオは、`GET` または `POST` を受け入れる `hello` という名前の関数を特徴としています。

<a id="hello-implementation" name="hello-implementation"></a>

#### <a name="implementation"></a>実装

*hello* という名前のフォルダー内の *function.json* ファイルで、HTTP によってトリガーされる関数を構成します。

**hello/function.json**

```json
{
  "bindings": [
    {
      "type": "httpTrigger",
      "authLevel": "anonymous",
      "direction": "in",
      "name": "req",
      "methods": ["get", "post"]
    },
    {
      "type": "http",
      "direction": "out",
      "name": "res"
    }
  ]
}
```

この関数は `GET` と `POST` の両方の要求を受け入れるように構成され、結果の値は `res` という名前の引数を使用して渡されます。

アプリのルートにある *host.json* ファイルは、`handler.exe` を実行するように構成され、`enableForwardingHttpRequest` は `true` に設定されています。

```json
{
  "version": "2.0",
  "customHandler": {
    "description": {
      "defaultExecutablePath": "handler.exe"
    },
    "enableForwardingHttpRequest": true
  }
}
```

`enableForwardingHttpRequest` が `true` に設定されている場合、HTTP 専用関数の動作は、次のように、既定のカスタム ハンドラーの動作とは異なります。

* HTTP 要求には、カスタム ハンドラーの[要求](#request-payload)ペイロードが含まれていません。 代わりに、Functions ホストは元の HTTP 要求のコピーを使用してハンドラーを呼び出します。
* Functions ホストは、すべてのクエリ文字列パラメーターを含む元の要求と同じパスのハンドラーを呼び出します。
* Functions ホストは、元の要求への応答として、ハンドラーの HTTP 応答のコピーを返します。

以下は、Functions ホストに対する POST 要求です。 その後、Functions ホストは、同じパスにあるカスタム ハンドラーに要求のコピーを送信します。

```http
POST http://127.0.0.1:7071/api/hello HTTP/1.1
Content-Type: application/json

{
  "message": "Hello World!"
}
```

*handler.go* ファイルは、Web サーバーと HTTP 関数を実装します。

```go
package main

import (
    "fmt"
    "io/ioutil"
    "log"
    "net/http"
    "os"
)

func helloHandler(w http.ResponseWriter, r *http.Request) {
    w.Header().Set("Content-Type", "application/json")
    if r.Method == "GET" {
        w.Write([]byte("hello world"))
    } else {
        body, _ := ioutil.ReadAll(r.Body)
        w.Write(body)
    }
}

func main() {
    customHandlerPort, exists := os.LookupEnv("FUNCTIONS_CUSTOMHANDLER_PORT")
    if !exists {
        customHandlerPort = "8080"
    }
    mux := http.NewServeMux()
    mux.HandleFunc("/api/hello", helloHandler)
    fmt.Println("Go server Listening on: ", customHandlerPort)
    log.Fatal(http.ListenAndServe(":"+customHandlerPort, mux))
}
```

この例では、カスタム ハンドラーは、HTTP イベントを処理するために Web サーバーを作成し、`FUNCTIONS_CUSTOMHANDLER_PORT` を介して要求をリッスンするように設定されています。

`GET` 要求は文字列を返すことによって処理され、`POST` 要求は要求本文にアクセスできます。

ここでの order 関数のルートは、元の要求と同じ `/api/hello` です。

>[!NOTE]
>`FUNCTIONS_CUSTOMHANDLER_PORT` は、関数の呼び出しに使用されるパブリック ポートではありません。 このポートは、カスタム ハンドラーを呼び出すために Functions ホストによって使用されます。

## <a name="deploying"></a>デプロイ中

カスタム ハンドラーは、すべての Azure Functions ホスティング オプションにデプロイできます。 ハンドラーにオペレーティング システムまたはプラットフォームの依存関係 (言語のランタイムなど) が必要な場合は、[カスタム コンテナー](./functions-create-function-linux-custom-image.md)を使用する必要がある場合があります。

カスタム ハンドラー用に Azure でFunction App を作成する場合は、スタックとして .NET Core を選択することをお勧めします。 カスタム ハンドラー用の "カスタム" スタックは、今後追加される予定です。

Azure Functions Core Tools を使用してカスタム ハンドラー アプリをデプロイするには、次のコマンドを実行します。

```bash
func azure functionapp publish $functionAppName
```

> [!NOTE]
> カスタム ハンドラーを実行するために必要なすべてのファイルがフォルダー内にあり、デプロイに含まれていることを確認します。 カスタム ハンドラーがバイナリ実行可能ファイルであるか、プラットフォーム固有の依存関係がある場合は、これらのファイルがターゲット デプロイ プラットフォームと一致していることを確認します。

## <a name="restrictions"></a>制限

- カスタム ハンドラー Web サーバーは、60 秒以内に起動する必要があります。

## <a name="samples"></a>サンプル

さまざまな言語で関数を実装する方法の例については、[カスタム ハンドラー サンプル GitHub リポジトリ](https://github.com/Azure-Samples/functions-custom-handlers) を参照してください。

## <a name="troubleshooting-and-support"></a>トラブルシューティングとサポート

### <a name="trace-logging"></a>Trace logging

カスタム ハンドラー プロセスの起動に失敗した場合、または Functions ホストとの通信に問題がある場合は、Function App のログ レベルを `Trace` に上げて、ホストからの診断メッセージをさらに表示できます。

Function App の既定のログ レベルを変更するには、*host.json* の `logging` セクションで `logLevel` 設定を構成します。

```json
{
  "version": "2.0",
  "customHandler": {
    "description": {
      "defaultExecutablePath": "handler.exe"
    }
  },
  "logging": {
    "logLevel": {
      "default": "Trace"
    }
  }
}
```

Functions ホストは、カスタム ハンドラー プロセスに関連する情報を含む、追加のログ メッセージを出力します。 ログを使用して、カスタム ハンドラー プロセスを開始するときの問題、またはカスタム ハンドラーで関数を呼び出すときの問題を調査します。

ローカルでは、ログはコンソールに出力されます。

Azure では、ログ メッセージを表示するには [Application Insights トレースのクエリ](analyze-telemetry-data.md#query-telemetry-data)を実行します。 アプリによって大量のログが生成される場合は、ログ メッセージのサブセットのみが Application Insights に送信されます。 すべてのメッセージがログに記録されるようにするには、[サンプリングを無効](configure-monitoring.md#configure-sampling)にします。

### <a name="test-custom-handler-in-isolation"></a>カスタム ハンドラーを分離してテストする

カスタム ハンドラー アプリは Web サーバー プロセスであるため、[cURL](https://curl.haxx.se/) や [Postman](https://www.postman.com/) などのツールを使用してモック [HTTP 要求](#request-payload)を送信することによって、独自に起動して関数呼び出しをテストすると便利な場合があります。

また、この方法を CI/CD パイプラインで使用して、カスタム ハンドラーに対する自動テストを実行することもできます。

### <a name="execution-environment"></a>実行環境

カスタム ハンドラーは、一般的な Azure Functions アプリと同じ環境で実行されます。 ハンドラーをテストして、実行に必要なすべての依存関係が環境に含まれていることを確認します。 追加の依存関係を必要とするアプリの場合は、Azure Functions の [Premium プラン](functions-premium-plan.md)でホストされている[カスタム コンテナー イメージ](functions-create-function-linux-custom-image.md)を使用して実行する必要がある場合があります。

### <a name="get-support"></a>サポートを受ける

カスタム ハンドラーがあるFunction App に関するヘルプが必要な場合は、通常のサポート チャネルを通じてリクエストを送信できます。 ただし、カスタム ハンドラー アプリのビルドにはさまざまな言語が使用されるため、サポートは無制限ではありません。

Functions ホストの起動またはカスタム ハンドラー プロセスとの通信に問題がある場合は、サポートを利用できます。 選択した言語またはフレームワークに関する問題など、カスタム ハンドラー プロセスの内部動作に固有の問題については、このコンテキストでサポート チームが支援を行うことができません。

## <a name="next-steps"></a>次のステップ

[カスタム ハンドラーのクイックスタート](create-first-function-vs-code-other.md)を使用して、Go または Rust での Azure Functions アプリの構築を開始します。
