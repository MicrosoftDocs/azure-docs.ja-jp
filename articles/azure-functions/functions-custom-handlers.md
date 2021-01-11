---
title: Azure Functions のカスタム ハンドラー (プレビュー)
description: 任意の言語またはランタイム バージョンで Azure Functions を使用する方法について説明します。
author: craigshoemaker
ms.author: cshoe
ms.date: 3/18/2020
ms.topic: article
ms.openlocfilehash: cdbb5bbde1e5efef9bef992a62a54f1525a16df7
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/02/2020
ms.locfileid: "85052587"
---
# <a name="azure-functions-custom-handlers-preview"></a>Azure Functions のカスタム ハンドラー (プレビュー)

すべての Function App は、言語固有のハンドラーによって実行されます。 Azure Functions では、多くの[言語ハンドラー](./supported-languages.md)が既定でサポートされていますが、アプリ実行環境をさらに制御することが必要になる場合があります。 カスタム ハンドラーにより、このような追加の制御が提供されます。

カスタム ハンドラーは、Functions ホストからイベントを受信する軽量の Web サーバーです。 HTTP プリミティブをサポートするすべての言語で、カスタム ハンドラーを実装できます。

カスタム ハンドラーは、次のような場合に最適です。

- 正式にサポートされていない言語で Function App を実装する。
- 既定でサポートされていない言語バージョンまたはランタイムで Function App を実装する。
- Function App の実行環境のきめ細かい制御を提供する。

カスタム ハンドラーを使用すると、すべての [トリガーと入出力バインド](./functions-triggers-bindings.md)は[拡張バンドル](./functions-bindings-register.md)によりサポートされます。

## <a name="overview"></a>概要

次の図は、カスタム ハンドラーとして実装されている Web サーバーと Functions ホストの関係を示しています。

![Azure Functions のカスタム ハンドラーの概要](./media/functions-custom-handlers/azure-functions-custom-handlers-overview.png)

- イベントが、Functions ホストに送信された要求をトリガーします。 このイベントには、未加工の HTTP ペイロード (バインドなしで HTTP によってトリガーされる関数の場合)、または関数の入力バインド データを保持するペイロードのいずれかが含まれます。
- その後、Functions ホストが、[要求ペイロード](#request-payload)を発行して、Web サーバーへの要求をプロキシします。
- Web サーバーは個々の関数を実行し、[応答ペイロード](#response-payload)を Functions ホストに返します。
- Functions ホストは、応答を出力バインドのペイロードとしてターゲットにプロキシします。

カスタム ハンドラーとして実装されている Azure Functions アプリでは、いくつかの規則に従って、*host.json* ファイルおよび *function.json* ファイルを構成する必要があります。

## <a name="application-structure"></a>アプリケーション構造

カスタム ハンドラーを実装するには、アプリケーションに次の要素が必要です。

- *host.json* ファイル: アプリのルート
- *function.json* ファイル: 関数ごとに必要 (関数名と一致する名前のフォルダー内)
- コマンド、スクリプト、または実行可能ファイル: Web サーバーを実行

次の図は、"order" という名前の関数の場合に、ファイル システム内でこれらのファイルがどのように配置されるかを示しています。

```bash
| /order
|   function.json
|
| host.json
```

### <a name="configuration"></a>構成

アプリケーションの構成には、*host.json* ファイルを使用します。 このファイルは、HTTP イベントを処理できる Web サーバーを指すことによって、要求の送信先を Functions ホストに指示します。

*host.json* ファイルの `httpWorker` セクションで Web サーバーを実行する方法の詳細を構成することにより、カスタム ハンドラーを定義します。

```json
{
    "version": "2.0",
    "httpWorker": {
        "description": {
            "defaultExecutablePath": "server.exe"
        }
    }
}
```

`httpWorker` セクションは、`defaultExecutablePath` で定義されているターゲットを指します。 実行ターゲットは、Web サーバーが実装されているコマンド、実行可能ファイル、またはファイルにすることができます。

スクリプト アプリの場合、`defaultExecutablePath` はスクリプト言語のランタイムを指し、`defaultWorkerPath` はスクリプト ファイルの場所を指します。 次の例は、Node.js の JavaScript アプリをカスタム ハンドラーとして構成する方法を示しています。

```json
{
    "version": "2.0",
    "httpWorker": {
        "description": {
            "defaultExecutablePath": "node",
            "defaultWorkerPath": "server.js"
        }
    }
}
```

`arguments` 配列を使用して引数を渡すこともできます。

```json
{
    "version": "2.0",
    "httpWorker": {
        "description": {
            "defaultExecutablePath": "node",
            "defaultWorkerPath": "server.js",
            "arguments": [ "--argument1", "--argument2" ]
        }
    }
}
```

多くのデバッグ設定には引数が必要です。 詳細については、「[デバッグ](#debugging)」セクションを参照してください。

> [!NOTE]
> *host.json* ファイルは、実行中の Web サーバーと同じレベルのディレクトリ構造にある必要があります。 既定では、一部の言語およびツールチェーンでこのファイルをアプリケーション ルートに配置することはできません。

#### <a name="bindings-support"></a>バインドのサポート

標準トリガーと入出力バインドは、*host.json* ファイル内の[拡張バンドル](./functions-bindings-register.md)を参照することによって利用できます。

### <a name="function-metadata"></a>関数のメタデータ

*function.json* の内容をカスタム ハンドラーと共に使用する場合、その内容は他のコンテキストで関数を定義する場合と変わりありません。 唯一の要件は、*function.json* ファイルは、関数名と一致する名前のフォルダーに格納しなければならないということです。

### <a name="request-payload"></a>要求ペイロード

純粋な HTTP 関数の要求ペイロードは、未加工の HTTP 要求ペイロードです。 純粋な HTTP 関数は、入力バインドも出力バインドもない、HTTP 応答を返す関数として定義されます。

その他の種類の関数 (入出力バインドがある、または HTTP 以外のイベント ソースによってトリガーされるもの) には、カスタム要求ペイロードがあります。

次のコードは、要求ペイロードのサンプルを表します。 ペイロードには、2 つのメンバー (`Data` と `Metadata`) を持つ JSON 構造体が含まれています。

`Data` メンバーには、*function.json* ファイルにバインド配列の形式で定義されている入力名およびトリガー名と一致するキーが含まれています。

`Metadata` メンバーには、[イベント ソースから生成されたメタデータ](./functions-bindings-expressions-patterns.md#trigger-metadata)が含まれています。

次の *function.json* ファイルに、次のようにバインドが定義されているとします。

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

次の例のような要求ペイロードが返されます。

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
| `Outputs`     | JSON      | *function.json* ファイルの `bindings` 配列によって定義される応答値を保持します。<br /><br />たとえば、関数に "blob" という名前の blob ストレージの出力バインドが構成されている場合、`Outputs` には、`blob` という名前のキーが格納され、このキーは blob の値に設定されます。 |
| `Logs`        | array     | Functions の呼び出しログにメッセージが表示されます。<br /><br />Azure で実行すると、メッセージは Application Insights に表示されます。 |
| `ReturnValue` | string    | *function.json* ファイルの `$return` として出力が構成されている場合に、応答を提供するために使用されます。 |

[サンプルのペイロードの例](#bindings-implementation)を参照してください。

## <a name="examples"></a>例

カスタム ハンドラーは、HTTP イベントをサポートする任意の言語で実装できます。 Azure Functions は[JavaScript と Node.js を完全に](./functions-reference-node.md)サポートしていますが、次の例では、説明のために Node.js で JavaScript を使用してカスタム ハンドラーを実装する方法を示しています。

> [!TIP]
> ここに示す Node.js ベースの例は、他の言語でカスタム ハンドラーを実装する方法を学習するためのガイドとしても、サポートされていないバージョンの Node.js で Functions アプリを実行する場合にも役立ちます。

## <a name="http-only-function"></a>HTTP のみの関数

次の例では、追加のバインドまたは出力を使用せずに、HTTP によってトリガーされる関数を構成する方法を示します。 この例で実装されているシナリオは、`GET` または `POST` を受け入れる `http` という名前の関数を特徴としています。

次のスニペットは、この関数に対する要求がどのように構成されるかを示しています。

```http
POST http://127.0.0.1:7071/api/hello HTTP/1.1
content-type: application/json

{
  "message": "Hello World!"
}
```

<a id="hello-implementation" name="hello-implementation"></a>

### <a name="implementation"></a>実装

*http* という名前のフォルダー内の *function.json* ファイルで、HTTP によってトリガーされる関数を構成します。

```json
{
  "bindings": [
    {
      "type": "httpTrigger",
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

アプリのルートにある *host.json* ファイルで、Node.js を実行し、`server.js` ファイルを指すように構成します。

```json
{
    "version": "2.0",
    "httpWorker": {
        "description": {
            "defaultExecutablePath": "node",
            "defaultWorkerPath": "server.js"
        }
    }
}
```

*server.js* ファイルは、Web サーバーと HTTP 関数を実装します。

```javascript
const express = require("express");
const app = express();

app.use(express.json());

const PORT = process.env.FUNCTIONS_HTTPWORKER_PORT;

const server = app.listen(PORT, "localhost", () => {
  console.log(`Your port is ${PORT}`);
  const { address: host, port } = server.address();
  console.log(`Example app listening at http://${host}:${port}`);
});

app.get("/hello", (req, res) => {
  res.json("Hello World!");
});

app.post("/hello", (req, res) => {
  res.json({ value: req.body });
});
```

この例では、Express を使用して HTTP イベントを処理する Web サーバーを作成し、`FUNCTIONS_HTTPWORKER_PORT` 経由で要求をリッスンするように設定します。

関数はパス `/hello` で定義します。 `GET` 要求は単純な JSON オブジェクトを返すことによって処理され、`POST` 要求は `req.body` 経由で要求本文にアクセスできます。

ここでは order 関数のルートは `/hello` であり、`/api/hello` ではありません。Functions ホストはカスタム ハンドラーに対して要求をプロキシするためです。

>[!NOTE]
>`FUNCTIONS_HTTPWORKER_PORT` は、関数の呼び出しに使用されるパブリック ポートではありません。 このポートは、カスタム ハンドラーを呼び出すために Functions ホストによって使用されます。

## <a name="function-with-bindings"></a>関数とバインド

この例で実装されているシナリオは、商品の注文を表すペイロードを持つ `POST` を受け入れる `order` という名前の関数を特徴としています。 関数に注文がポストされると、Queue Storage メッセージが作成され、HTTP 応答が返されます。

```http
POST http://127.0.0.1:7071/api/order HTTP/1.1
content-type: application/json

{
  "id": 1005,
  "quantity": 2,
  "color": "black"
}
```

<a id="bindings-implementation" name="bindings-implementation"></a>

### <a name="implementation"></a>実装

*order* という名前のフォルダー内の *function.json* ファイルで、HTTP によってトリガーされる関数を構成します。

```json
{
  "bindings": [
    {
      "type": "httpTrigger",
      "authLevel": "function",
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

アプリのルートにある *host.json* ファイルで、Node.js を実行し、`server.js` ファイルを指すように構成します。

```json
{
    "version": "2.0",
    "httpWorker": {
        "description": {
            "defaultExecutablePath": "node",
            "defaultWorkerPath": "server.js"
        }
    }
}
```

*server.js* ファイルは、Web サーバーと HTTP 関数を実装します。

```javascript
const express = require("express");
const app = express();

app.use(express.json());

const PORT = process.env.FUNCTIONS_HTTPWORKER_PORT;

const server = app.listen(PORT, "localhost", () => {
  console.log(`Your port is ${PORT}`);
  const { address: host, port } = server.address();
  console.log(`Example app listening at http://${host}:${port}`);
});

app.post("/order", (req, res) => {
  const message = req.body.Data.req.Body;
  const response = {
    Outputs: {
      message: message,
      res: {
        statusCode: 200,
        body: "Order complete"
      }
    },
    Logs: ["order processed"]
  };
  res.json(response);
});
```

この例では、Express を使用して HTTP イベントを処理する Web サーバーを作成し、`FUNCTIONS_HTTPWORKER_PORT` 経由で要求をリッスンするように設定します。

関数はパス `/order` で定義します。  ここでは order 関数のルートは `/order` であり、`/api/order` ではありません。Functions ホストはカスタム ハンドラーに対して要求をプロキシするためです。

`POST` 要求がこの関数に送信されると、次を経由して公開されます。

- 要求本文は `req.body` 経由で入手できます。
- 関数にポストされたデータは、`req.body.Data.req.Body` 経由で入手できます。

関数の応答は、キーと値のペアの形式で設定されます。この場合、`Outputs` メンバーは、*function.json* ファイルで定義されている出力とキーが一致する JSON 値を保持します。

この関数は、`message` を要求から送信されたメッセージと同じ値に設定し、`res` を予想される HTTP 応答に設定することにより、Queue Storage にメッセージを出力し、HTTP 応答を返します。

## <a name="debugging"></a>デバッグ

Functions カスタム ハンドラー アプリをデバッグするには、言語とランタイムに適した引数を追加してデバッグを有効にする必要があります。

たとえば、Node.js アプリケーションをデバッグするには、*host.json* ファイルで `--inspect` フラグを引数として渡します。

```json
{
    "version": "2.0",
    "httpWorker": {
        "description": {
            "defaultExecutablePath": "node",
            "defaultWorkerPath": "server.js",
            "arguments": [ "--inspect" ]
        }
    }
}
```

> [!NOTE]
> デバッグ構成は *host.json* ファイルの一部であるため、運用環境にデプロイする前にいくつかの引数を削除する必要がある場合があります。

この構成では、次のコマンドを使用して、関数のホスト プロセスを開始できます。

```bash
func host start
```

プロセスが開始されたら、デバッガーをアタッチしてブレークポイントをヒットできます。

### <a name="visual-studio-code"></a>Visual Studio Code

次の例は、アプリを Visual Studio Code デバッガーに接続するための *launch.json* ファイルを設定する方法を示すサンプル構成です。

この例は Node.js 用であるため、他の言語またはランタイムでは、この例を変更しなければならない場合があります。

```json
{
  "version": "0.2.0",
  "configurations": [
    {
      "name": "Attach to Node Functions",
      "type": "node",
      "request": "attach",
      "port": 9229,
      "preLaunchTask": "func: host start"
    }
  ]
}
```

## <a name="deploying"></a>デプロイ中

カスタム ハンドラーは、ほぼすべての Azure Functions ホスティング オプションに配置できます ([の制限](#restrictions)を参照してください)。 ハンドラーがカスタム依存関係 (言語のランタイムなど) を必要とする場合は、[カスタム コンテナー](./functions-create-function-linux-custom-image.md)を使用する必要がある場合があります。

Azure Functions Core Tools を使用してカスタム ハンドラー アプリをデプロイするには、次のコマンドを実行します。

```bash
func azure functionapp publish $functionAppName --no-build --force
```

## <a name="restrictions"></a>制限

- Web サーバーは 60 秒以内に開始する必要があります。

## <a name="samples"></a>サンプル

さまざまな言語で関数を実装する方法の例については、[カスタム ハンドラー サンプル GitHub リポジトリ](https://github.com/Azure-Samples/functions-custom-handlers) を参照してください。
