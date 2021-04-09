---
title: Azure Functions SignalR Service 入力バインド
description: Azure Functions で SignalR サービスエンドポイント URL とアクセストークンを返却する方法について説明します。
author: craigshoemaker
ms.topic: reference
ms.custom: devx-track-csharp
ms.date: 02/20/2020
ms.author: cshoe
ms.openlocfilehash: 3f3a99c83d4a18f3085419b91be947dd67f8eec4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "97763321"
---
# <a name="signalr-service-input-binding-for-azure-functions"></a>Azure Functions における SignalR Service の入力バインド

クライアントが Azure SignalR Service に接続するには、そのクライアントに、サービス エンドポイント URL と有効なアクセス トークンが必要です。 サービスへの接続に使用される SignalR Service エンドポイント URL と有効なトークンは、*SignalRConnectionInfo* 入力バインドによって生成されます。 トークンを使用できる期間は限られています。また、トークンを使うと、特定のユーザーを接続に対して認証できます。このため、トークンをキャッシュしたりクライアント間で共有したりすることはお勧めしません。 このバインドを使用した HTTP トリガーは、クライアントが接続情報を取得するときに使用できます。

SignalR クライアント SDK によって使用できる "negotiate" 関数を作成するためのこのバインディングの使用方法の詳細については、 SignalR Service の概念に関するドキュメントの [Azure Functions の開発と構成](../azure-signalr/signalr-concept-serverless-development-config.md)に関する記事を参照してください。

セットアップと構成の詳細については、[概要](functions-bindings-signalr-service.md)に関するページをご覧ください。

## <a name="example"></a>例

# <a name="c"></a>[C#](#tab/csharp)

次の例は、入力バインドを使用して SignalR 接続情報を取得し、HTTP 経由でそれを返す [C# 関数](functions-dotnet-class-library.md)を示しています。

```cs
[FunctionName("negotiate")]
public static SignalRConnectionInfo Negotiate(
    [HttpTrigger(AuthorizationLevel.Anonymous)]HttpRequest req,
    [SignalRConnectionInfo(HubName = "chat")]SignalRConnectionInfo connectionInfo)
{
    return connectionInfo;
}
```

# <a name="c-script"></a>[C# スクリプト](#tab/csharp-script)

次の例は、*function.json* ファイルの SignalR 接続情報入力バインドと、そのバインドを使用して接続情報を返す [C# スクリプト関数](functions-reference-csharp.md)を示しています。

*function.json* ファイルのバインド データを次に示します。

function.json の例:

```json
{
    "type": "signalRConnectionInfo",
    "name": "connectionInfo",
    "hubName": "chat",
    "connectionStringSetting": "<name of setting containing SignalR Service connection string>",
    "direction": "in"
}
```

C# スクリプト コードを次に示します。

```cs
#r "Microsoft.Azure.WebJobs.Extensions.SignalRService"
using Microsoft.Azure.WebJobs.Extensions.SignalRService;

public static SignalRConnectionInfo Run(HttpRequest req, SignalRConnectionInfo connectionInfo)
{
    return connectionInfo;
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

次の例は、*function.json* ファイルの SignalR 接続情報入力バインドと、そのバインドを使用して接続情報を返す [JavaScript 関数](functions-reference-node.md)を示しています。

*function.json* ファイルのバインド データを次に示します。

function.json の例:

```json
{
    "type": "signalRConnectionInfo",
    "name": "connectionInfo",
    "hubName": "chat",
    "connectionStringSetting": "<name of setting containing SignalR Service connection string>",
    "direction": "in"
}
```

JavaScript コードを次に示します。

```javascript
module.exports = async function (context, req, connectionInfo) {
    context.res.body = connectionInfo;
};
```

# <a name="python"></a>[Python](#tab/python)

次の例は、*function.json* ファイルの SignalR 接続情報入力バインドと、そのバインドを使用して接続情報を返す [Python 関数](functions-reference-python.md)を示しています。

*function.json* ファイルのバインド データを次に示します。

function.json の例:

```json
{
    "type": "signalRConnectionInfo",
    "name": "connectionInfo",
    "hubName": "chat",
    "connectionStringSetting": "<name of setting containing SignalR Service connection string>",
    "direction": "in"
}
```

Python コードを次に示します。

```python
def main(req: func.HttpRequest, connectionInfoJson: str) -> func.HttpResponse:
    return func.HttpResponse(
        connectionInfoJson,
        status_code=200,
        headers={
            'Content-type': 'application/json'
        }
    )
```

# <a name="java"></a>[Java](#tab/java)

次の例は、入力バインドを使用して SignalR 接続情報を取得し、HTTP 経由でそれを返す [Java 関数](functions-reference-java.md)を示しています。

```java
@FunctionName("negotiate")
public SignalRConnectionInfo negotiate(
        @HttpTrigger(
            name = "req",
            methods = { HttpMethod.POST },
            authLevel = AuthorizationLevel.ANONYMOUS) HttpRequestMessage<Optional<String>> req,
        @SignalRConnectionInfoInput(
            name = "connectionInfo",
            hubName = "chat") SignalRConnectionInfo connectionInfo) {
    return connectionInfo;
}
```

---

## <a name="authenticated-tokens"></a>認証済みトークン

認証済みクライアントによって関数がトリガーされている場合は、ユーザー ID 要求を生成済みトークンに追加できます。 [App Service 認証](../app-service/overview-authentication-authorization.md)を使用すると、認証を関数アプリに簡単に追加することができます。

App Service 認証によって、`x-ms-client-principal-id` および `x-ms-client-principal-name` という名前の HTTP ヘッダーが設定されます。この 2 つの HTTP ヘッダーには、認証済みユーザーのクライアント プリンシパルの ID と名前がそれぞれ含まれています。

# <a name="c"></a>[C#](#tab/csharp)

バインドの `UserId` プロパティをいずれかのヘッダーの値に設定するには、[バインド式](./functions-bindings-expressions-patterns.md)として `{headers.x-ms-client-principal-id}` または `{headers.x-ms-client-principal-name}` を使用します。

```cs
[FunctionName("negotiate")]
public static SignalRConnectionInfo Negotiate(
    [HttpTrigger(AuthorizationLevel.Anonymous)]HttpRequest req, 
    [SignalRConnectionInfo
        (HubName = "chat", UserId = "{headers.x-ms-client-principal-id}")]
        SignalRConnectionInfo connectionInfo)
{
    // connectionInfo contains an access key token with a name identifier claim set to the authenticated user
    return connectionInfo;
}
```

# <a name="c-script"></a>[C# スクリプト](#tab/csharp-script)

バインドの `userId` プロパティをいずれかのヘッダーの値に設定するには、[バインド式](./functions-bindings-expressions-patterns.md)として `{headers.x-ms-client-principal-id}` または `{headers.x-ms-client-principal-name}` を使用します。

function.json の例:

```json
{
    "type": "signalRConnectionInfo",
    "name": "connectionInfo",
    "hubName": "chat",
    "userId": "{headers.x-ms-client-principal-id}",
    "connectionStringSetting": "<name of setting containing SignalR Service connection string>",
    "direction": "in"
}
```

C# スクリプト コードを次に示します。

```cs
#r "Microsoft.Azure.WebJobs.Extensions.SignalRService"
using Microsoft.Azure.WebJobs.Extensions.SignalRService;

public static SignalRConnectionInfo Run(HttpRequest req, SignalRConnectionInfo connectionInfo)
{
    // connectionInfo contains an access key token with a name identifier
    // claim set to the authenticated user
    return connectionInfo;
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

バインドの `userId` プロパティをいずれかのヘッダーの値に設定するには、[バインド式](./functions-bindings-expressions-patterns.md)として `{headers.x-ms-client-principal-id}` または `{headers.x-ms-client-principal-name}` を使用します。

function.json の例:

```json
{
    "type": "signalRConnectionInfo",
    "name": "connectionInfo",
    "hubName": "chat",
    "userId": "{headers.x-ms-client-principal-id}",
    "connectionStringSetting": "<name of setting containing SignalR Service connection string>",
    "direction": "in"
}
```

JavaScript コードを次に示します。

```javascript
module.exports = async function (context, req, connectionInfo) {
    // connectionInfo contains an access key token with a name identifier
    // claim set to the authenticated user
    context.res.body = connectionInfo;
};
```

# <a name="python"></a>[Python](#tab/python)

バインドの `userId` プロパティをいずれかのヘッダーの値に設定するには、[バインド式](./functions-bindings-expressions-patterns.md)として `{headers.x-ms-client-principal-id}` または `{headers.x-ms-client-principal-name}` を使用します。

function.json の例:

```json
{
    "type": "signalRConnectionInfo",
    "name": "connectionInfo",
    "hubName": "chat",
    "userId": "{headers.x-ms-client-principal-id}",
    "connectionStringSetting": "<name of setting containing SignalR Service connection string>",
    "direction": "in"
}
```

Python コードを次に示します。

```python
def main(req: func.HttpRequest, connectionInfo: str) -> func.HttpResponse:
    # connectionInfo contains an access key token with a name identifier
    # claim set to the authenticated user
    return func.HttpResponse(
        connectionInfo,
        status_code=200,
        headers={
            'Content-type': 'application/json'
        }
    )
```

# <a name="java"></a>[Java](#tab/java)

バインドの `userId` プロパティをいずれかのヘッダーの値に設定するには、[バインド式](./functions-bindings-expressions-patterns.md)として `{headers.x-ms-client-principal-id}` または `{headers.x-ms-client-principal-name}` を使用します。

```java
@FunctionName("negotiate")
public SignalRConnectionInfo negotiate(
        @HttpTrigger(
            name = "req",
            methods = { HttpMethod.POST },
            authLevel = AuthorizationLevel.ANONYMOUS) HttpRequestMessage<Optional<String>> req,
        @SignalRConnectionInfoInput(
            name = "connectionInfo",
            hubName = "chat",
            userId = "{headers.x-ms-client-principal-id}") SignalRConnectionInfo connectionInfo) {
    return connectionInfo;
}
```

---

## <a name="next-steps"></a>次のステップ

- [SignalR Service からのメッセージの処理 (トリガー バインド)](./functions-bindings-signalr-service-trigger.md)
- [SignalR Service メッセージを送信する (出力バインド)](./functions-bindings-signalr-service-output.md) 
