---
title: Express.js を Azure Functions に切り替える
description: Express.js のエンドポイントを Azure Functions にリファクターする方法について説明します。
author: craigshoemaker
ms.topic: conceptual
ms.date: 07/31/2020
ms.author: cshoe
ms.openlocfilehash: 266df5371ff5f47526fa9d6567c62e31d51ebb05
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "87810226"
---
# <a name="shifting-from-expressjs-to-azure-functions"></a>Express.js を Azure Functions に切り替える

Express.js は、Web 開発者の間で最も支持されている Node.js フレームワークの 1 つであり、API エンドポイントを提供するアプリの構築に最適な選択肢であり続けています。

コードをサーバーレス アーキテクチャに移行する際、Express.js のエンドポイントをリファクターすると次の領域に影響が及びます。

- **ミドルウェア**: Express.js は、堅牢なミドルウェア群を特徴としています。 Azure Functions と [Azure API Management](../api-management/api-management-key-concepts.md) の機能から、ミドルウェア モジュールはその多くが不要となります。 重要なミドルウェアによって処理されるロジックは、エンドポイントを移行する前に複製するか、置き換えるようにしてください。

- **異なる API**: 要求と応答の両方を処理するために使用される API が Azure Functions と Express.js とでは異なります。 必要な変更については、以下の例で詳しく取り上げます。

- **既定のルート**: 既定では、Azure Functions のエンドポイントが `api` ルートで公開されます。 ルーティング規則は、[_host.json_ ファイル内の `routePrefix`](./functions-bindings-http-webhook-output.md#hostjson-settings) で構成できます。

- **構成と規則**: 関数アプリでは、_function.json_ ファイルを使用して HTTP 動詞やセキュリティ ポリシーを定義するほか、関数の [入力と出力](./functions-triggers-bindings.md)を構成することができます。 既定では、関数ファイルを含んだフォルダーの名前によってエンドポイントの名前が定義されますが、その名前は、[function.json](./functions-bindings-http-webhook-trigger.md#customize-the-http-endpoint) ファイル内の `route` プロパティで変更することができます。

> [!TIP]
> 詳細については、対話型チュートリアル「[Azure Functions で Node.js と Express API をサーバーレス API にリファクターする](/learn/modules/shift-nodejs-express-apis-serverless/)」を参照してください。

## <a name="example"></a>例

### <a name="expressjs"></a>Express.js

一般的な Express.js `GET` エンドポイントの例を次に示します。

```javascript
// server.js
app.get('/hello', (req, res) => {
  try {
    res.send("Success!");
  } catch(error) {
    const err = JSON.stringify(error);
    res.status(500).send(`Request error. ${err}`);
  }
});
```

`GET` 要求が `/hello` に送信されると、`Success` を含んだ `HTTP 200` 応答が返されます。 エンドポイントでエラーが発生した場合は、エラーの詳細を含んだ `HTTP 500` 応答が返されます。

### <a name="azure-functions"></a>Azure Functions

Azure Functions では、構成およびコード ファイルが関数ごとに単一のフォルダーに整理されます。 既定では、フォルダーの名前によって関数名が決まります。

たとえば、`hello` という名前の関数には、次のファイルを含んだフォルダーがあります。

``` files
| - hello
|  - function.json
|  - index.js
```

次の例は、上記の Express.js エンドポイントと同じ結果を Azure Functions で実装したものです。

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
// hello/index.js
module.exports = async function (context, req) {
  try {
    context.res = { body: "Success!" };
  } catch(error) {
    const err = JSON.stringify(error);
    context.res = {
      status: 500,
      body: `Request error. ${err}`
    };
  }
};
```

# <a name="typescript"></a>[TypeScript](#tab/typescript)

```typescript
// hello/index.ts
import { AzureFunction, Context, HttpRequest } from "@azure/functions";

const httpTrigger: AzureFunction = async function (context: Context, req: HttpRequest): Promise<void> {
  try {
    context.res = { body: "Success!" };
  } catch (error) {
    const err = JSON.stringify(error);
    context.res = {
      status: 500,
      body: `Request error. ${err}`,
    };
  }
};

export default httpTrigger;
```

---

Functions に移行するときは、次の変更を行います。

- **モジュール:** 関数コードは JavaScript モジュールとして実装されます。

- **コンテキストと応答オブジェクト**: [`context`](./functions-reference-node.md#context-object) を使用すると、Functions のランタイムと通信を行うことができます。 そのコンテキストから、要求データを読み取ったり、関数の応答を設定したりすることができます。 同期コードでは、`context.done()` を呼び出して実行を完了する必要があるのに対し、`asyc` 関数では、要求が暗黙的に解決されます。

- **命名規則**: 既定では、Azure Functions のファイルを格納するために使用されるフォルダーの名前がエンドポイント名として使用されます (これは [function.json](./functions-bindings-http-webhook-trigger.md#customize-the-http-endpoint) でオーバーライドできます)。

- **構成**:HTTP 動詞は、`POST` や `PUT` などの [function.json](./functions-bindings-http-webhook-trigger.md#customize-the-http-endpoint) ファイルで定義します。

次の _function.json_ ファイルには、関数の構成情報が保持されています。

```json
{
  "bindings": [
    {
      "authLevel": "function",
      "type": "httpTrigger",
      "direction": "in",
      "name": "req",
      "methods": ["get"]
    },
    {
      "type": "http",
      "direction": "out",
      "name": "res"
    }
  ]
}
```

`methods` 配列に `get` を定義することにより、HTTP `GET` 要求からこの関数を利用することができます。 同様に、API で `POST` 要求をサポートしたい場合も、配列に `post` を追加することができます。

## <a name="next-steps"></a>次のステップ

- 詳細については、対話型チュートリアル「[Azure Functions で Node.js と Express API をサーバーレス API にリファクターする](/learn/modules/shift-nodejs-express-apis-serverless/)」を参照してください。