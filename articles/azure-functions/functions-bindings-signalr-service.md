---
title: Azure Functions における SignalR Service のバインド
description: Azure Functions で SignalR Service のバインドを使用する方法を説明します。
services: functions
documentationcenter: na
author: craigshoemaker
manager: gwallace
editor: ''
tags: ''
keywords: Azure Functions, 関数, イベント処理, 動的コンピューティング, サーバーなしのアーキテクチャ
ms.service: azure-functions
ms.topic: reference
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 02/28/2019
ms.author: cshoe
ms.openlocfilehash: 82bbd728bfd51595f0b6eacf344bc95ea5db1ea8
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/28/2019
ms.locfileid: "70086158"
---
# <a name="signalr-service-bindings-for-azure-functions"></a>Azure Functions における SignalR サービスのバインド

この記事では、Azure Functions で SignalR Service のバインドを使用して、[Azure SignalR Service](https://azure.microsoft.com/services/signalr-service/) に接続されたクライアントに対して認証を行い、リアルタイム メッセージを送信する方法を説明します。 Azure Functions は、SignalR Service の入力および出力バインドをサポートしています。

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="packages---functions-2x"></a>パッケージ - Functions 2.x

SignalR Service のバインドは [Microsoft.Azure.WebJobs.Extensions.SignalRService](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.SignalRService) NuGet パッケージ、バージョン 1.* で提供されます。 パッケージのソース コードは、[azure-functions-signalrservice-extension](https://github.com/Azure/azure-functions-signalrservice-extension) GitHub リポジトリにあります。

[!INCLUDE [functions-package-v2](../../includes/functions-package-v2-manual-portal.md)]


### <a name="java-annotations"></a>Java の注釈

SignalR サービスの注釈を Java 関数で使用するには、*azure-functions-java-library-signalr* アーティファクト (バージョン 1.0 以降) への依存関係を pom.xml に追加する必要があります。

```xml
<dependency>
    <groupId>com.microsoft.azure.functions</groupId>
    <artifactId>azure-functions-java-library-signalr</artifactId>
    <version>1.0.0</version>
</dependency>
```

> [!NOTE]
> Java で SignalR Service バインディングを使用するには、Azure Functions Core Tools のバージョン 2.4.419 (ホストのバージョン 2.0.12332) を使用していることを確認してください。

## <a name="using-signalr-service-with-azure-functions"></a>Azure Functions での SignalR Service の使用

SignalR Service と Azure Functions を構成して一緒に使用する方法の詳細については、「[Azure Functions development and configuration with Azure SignalR Service (Azure SignalR Service を使用した Azure Functions の開発と構成)](../azure-signalr/signalr-concept-serverless-development-config.md)」を参照してください。

## <a name="signalr-connection-info-input-binding"></a>SignalR 接続情報入力バインド

クライアントが Azure SignalR Service に接続するには、そのクライアントに、サービス エンドポイント URL と有効なアクセス トークンが必要です。 サービスへの接続に使用される SignalR Service エンドポイント URL と有効なトークンは、*SignalRConnectionInfo* 入力バインドによって生成されます。 トークンを使用できる期間は限られています。また、トークンを使うと、特定のユーザーを接続に対して認証できます。このため、トークンをキャッシュしたりクライアント間で共有したりすることはお勧めしません。 このバインドを使用した HTTP トリガーは、クライアントが接続情報を取得するときに使用できます。

言語固有の例をご覧ください。

* [2.x C#](#2x-c-input-examples)
* [2.x JavaScript](#2x-javascript-input-examples)
* [2.x Java](#2x-java-input-examples)

SignalR クライアント SDK によって使用できる "negotiate" 関数を作成するためのこのバインディングの使用方法の詳細については、 SignalR Service の概念に関するドキュメントの [Azure Functions の開発と構成](../azure-signalr/signalr-concept-serverless-development-config.md)に関する記事を参照してください。

### <a name="2x-c-input-examples"></a>2.x C# 入力の例

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

#### <a name="authenticated-tokens"></a>認証済みトークン

認証済みクライアントによって関数がトリガーされている場合は、ユーザー ID 要求を生成済みトークンに追加できます。 [App Service 認証](../app-service/overview-authentication-authorization.md)を使用すると、認証を関数アプリに簡単に追加することができます。

App Service 認証によって、`x-ms-client-principal-id` および `x-ms-client-principal-name` という名前の HTTP ヘッダーが設定されます。この 2 つの HTTP ヘッダーには、認証済みユーザーのクライアント プリンシパルの ID と名前がそれぞれ含まれています。 バインドの `UserId` プロパティをいずれかのヘッダーの値に設定するには、[バインド式](./functions-bindings-expressions-patterns.md)として `{headers.x-ms-client-principal-id}` または `{headers.x-ms-client-principal-name}` を使用します。 

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

### <a name="2x-javascript-input-examples"></a>2.x JavaScript 入力の例

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

#### <a name="authenticated-tokens"></a>認証済みトークン

認証済みクライアントによって関数がトリガーされている場合は、ユーザー ID 要求を生成済みトークンに追加できます。 [App Service 認証](../app-service/overview-authentication-authorization.md)を使用すると、認証を関数アプリに簡単に追加することができます。

App Service 認証によって、`x-ms-client-principal-id` および `x-ms-client-principal-name` という名前の HTTP ヘッダーが設定されます。この 2 つの HTTP ヘッダーには、認証済みユーザーのクライアント プリンシパルの ID と名前がそれぞれ含まれています。 バインドの `userId` プロパティをいずれかのヘッダーの値に設定するには、[バインド式](./functions-bindings-expressions-patterns.md)として `{headers.x-ms-client-principal-id}` または `{headers.x-ms-client-principal-name}` を使用します。 

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

### <a name="2x-java-input-examples"></a>2.x Java 入力の例

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

#### <a name="authenticated-tokens"></a>認証済みトークン

認証済みクライアントによって関数がトリガーされている場合は、ユーザー ID 要求を生成済みトークンに追加できます。 [App Service 認証](../app-service/overview-authentication-authorization.md)を使用すると、認証を関数アプリに簡単に追加することができます。

App Service 認証によって、`x-ms-client-principal-id` および `x-ms-client-principal-name` という名前の HTTP ヘッダーが設定されます。この 2 つの HTTP ヘッダーには、認証済みユーザーのクライアント プリンシパルの ID と名前がそれぞれ含まれています。 バインドの `UserId` プロパティをいずれかのヘッダーの値に設定するには、[バインド式](./functions-bindings-expressions-patterns.md)として `{headers.x-ms-client-principal-id}` または `{headers.x-ms-client-principal-name}` を使用します。

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

## <a name="signalr-output-binding"></a>SignalR 出力バインド

*SignalR* 出力バインドを使用して、Azure SignalR Service で 1 つ以上のメッセージを送信します。 すべての接続済みクライアントにメッセージをブロードキャストすることも、特定のユーザーに対して認証されている接続済みクライアントだけにブロードキャストすることも可能です。

これは、ユーザーが属するグループの管理にも使用できます。

言語固有の例をご覧ください。

* [2.x C#](#2x-c-send-message-output-examples)
* [2.x JavaScript](#2x-javascript-send-message-output-examples)
* [2.x Java](#2x-java-send-message-output-examples)

### <a name="2x-c-send-message-output-examples"></a>2.x C# 送信メッセージ出力の例

#### <a name="broadcast-to-all-clients"></a>すべてのクライアントにブロードキャストする

次の例は、出力バインドを使用してすべての接続済みクライアントにメッセージを送信する [C# 関数](functions-dotnet-class-library.md)を示しています。 `Target` は、各クライアントで呼び出されるメソッドの名前です。 `Arguments` プロパティは、クライアント メソッドに渡される 0 個以上のオブジェクトの配列です。

```cs
[FunctionName("SendMessage")]
public static Task SendMessage(
    [HttpTrigger(AuthorizationLevel.Anonymous, "post")]object message, 
    [SignalR(HubName = "chat")]IAsyncCollector<SignalRMessage> signalRMessages)
{
    return signalRMessages.AddAsync(
        new SignalRMessage 
        {
            Target = "newMessage", 
            Arguments = new [] { message } 
        });
}
```

#### <a name="send-to-a-user"></a>ユーザーに送信する

ユーザーに対して認証された接続だけにメッセージを送信するには、SignalR メッセージの `UserId` プロパティを設定します。

```cs
[FunctionName("SendMessage")]
public static Task SendMessage(
    [HttpTrigger(AuthorizationLevel.Anonymous, "post")]object message, 
    [SignalR(HubName = "chat")]IAsyncCollector<SignalRMessage> signalRMessages)
{
    return signalRMessages.AddAsync(
        new SignalRMessage 
        {
            // the message will only be sent to this user ID
            UserId = "userId1",
            Target = "newMessage",
            Arguments = new [] { message }
        });
}
```

#### <a name="send-to-a-group"></a>グループに送信する

グループに追加された接続だけにメッセージを送信するには、SignalR メッセージの `GroupName` プロパティを設定します。

```cs
[FunctionName("SendMessage")]
public static Task SendMessage(
    [HttpTrigger(AuthorizationLevel.Anonymous, "post")]object message,
    [SignalR(HubName = "chat")]IAsyncCollector<SignalRMessage> signalRMessages)
{
    return signalRMessages.AddAsync(
        new SignalRMessage
        {
            // the message will be sent to the group with this name
            GroupName = "myGroup",
            Target = "newMessage",
            Arguments = new [] { message }
        });
}
```

### <a name="2x-c-group-management-output-examples"></a>2.x C# グループ管理出力の例

SignalR Service を使用すると、ユーザーをグループに追加できます。 その後にメッセージをグループに送信できます。 `SignalRGroupAction` クラスと `SignalR` 出力バインドを使用して、ユーザーのグループ メンバーシップを管理できます。

#### <a name="add-user-to-a-group"></a>ユーザーをグループに追加する

次の例では、ユーザーをグループに追加します。

```csharp
[FunctionName("addToGroup")]
public static Task AddToGroup(
    [HttpTrigger(AuthorizationLevel.Anonymous, "post")]HttpRequest req,
    ClaimsPrincipal claimsPrincipal,
    [SignalR(HubName = "chat")]
        IAsyncCollector<SignalRGroupAction> signalRGroupActions)
{
    var userIdClaim = claimsPrincipal.FindFirst(ClaimTypes.NameIdentifier);
    return signalRGroupActions.AddAsync(
        new SignalRGroupAction
        {
            UserId = userIdClaim.Value,
            GroupName = "myGroup",
            Action = GroupAction.Add
        });
}
```

#### <a name="remove-user-from-a-group"></a>グループからユーザーを削除する

次の例では、ユーザーをグループから削除します。

```csharp
[FunctionName("removeFromGroup")]
public static Task RemoveFromGroup(
    [HttpTrigger(AuthorizationLevel.Anonymous, "post")]HttpRequest req,
    ClaimsPrincipal claimsPrincipal,
    [SignalR(HubName = "chat")]
        IAsyncCollector<SignalRGroupAction> signalRGroupActions)
{
    var userIdClaim = claimsPrincipal.FindFirst(ClaimTypes.NameIdentifier);
    return signalRGroupActions.AddAsync(
        new SignalRGroupAction
        {
            UserId = userIdClaim.Value,
            GroupName = "myGroup",
            Action = GroupAction.Remove
        });
}
```

> [!NOTE]
> `ClaimsPrincipal` を正しくバインドするためには、Azure Functions で認証設定を構成しておく必要があります。

### <a name="2x-javascript-send-message-output-examples"></a>2.x JavaScript 送信メッセージ出力の例

#### <a name="broadcast-to-all-clients"></a>すべてのクライアントにブロードキャストする

次の例は、*function.json* ファイルの SignalR 出力バインドと、そのバインドを使用して Azure SignalR Service でメッセージを送信する [JavaScript 関数](functions-reference-node.md)を示しています。 出力バインドを、1 つ以上の SignalR メッセージの配列に設定します。 SignalR メッセージは、クライアントごとに呼び出すメソッドの名前を指定する `target` プロパティと、クライアント メソッドに引数として渡されるオブジェクトの配列である `arguments` プロパティで構成されます。

*function.json* ファイルのバインド データを次に示します。

function.json の例:

```json
{
  "type": "signalR",
  "name": "signalRMessages",
  "hubName": "<hub_name>",
  "connectionStringSetting": "<name of setting containing SignalR Service connection string>",
  "direction": "out"
}
```

JavaScript コードを次に示します。

```javascript
module.exports = async function (context, req) {
    context.bindings.signalRMessages = [{
        "target": "newMessage",
        "arguments": [ req.body ]
    }];
};
```

#### <a name="send-to-a-user"></a>ユーザーに送信する

ユーザーに対して認証された接続だけにメッセージを送信するには、SignalR メッセージの `userId` プロパティを設定します。

*function.json* は同じままです。 JavaScript コードを次に示します。

```javascript
module.exports = async function (context, req) {
    context.bindings.signalRMessages = [{
        // message will only be sent to this user ID
        "userId": "userId1",
        "target": "newMessage",
        "arguments": [ req.body ]
    }];
};
```

#### <a name="send-to-a-group"></a>グループに送信する

グループに追加された接続だけにメッセージを送信するには、SignalR メッセージの `groupName` プロパティを設定します。

*function.json* は同じままです。 JavaScript コードを次に示します。

```javascript
module.exports = async function (context, req) {
    context.bindings.signalRMessages = [{
        // message will only be sent to this group
        "groupName": "myGroup",
        "target": "newMessage",
        "arguments": [ req.body ]
    }];
};
```

### <a name="2x-javascript-group-management-output-examples"></a>2.x JavaScript グループ管理出力の例

SignalR Service を使用すると、ユーザーをグループに追加できます。 その後にメッセージをグループに送信できます。 `SignalR` 出力バインドを使用して、ユーザーのグループ メンバーシップを管理できます。

#### <a name="add-user-to-a-group"></a>ユーザーをグループに追加する

次の例では、ユーザーをグループに追加します。

*function.json*

```json
{
  "disabled": false,
  "bindings": [
    {
      "authLevel": "anonymous",
      "type": "httpTrigger",
      "direction": "in",
      "name": "req",
      "methods": [
        "post"
      ]
    },
    {
      "type": "http",
      "direction": "out",
      "name": "res"
    },
    {
      "type": "signalR",
      "name": "signalRGroupActions",
      "connectionStringSetting": "<name of setting containing SignalR Service connection string>",
      "hubName": "chat",
      "direction": "out"
    }
  ]
}
```

*index.js*

```javascript
module.exports = async function (context, req) {
  context.bindings.signalRGroupActions = [{
    "userId": req.query.userId,
    "groupName": "myGroup",
    "action": "add"
  }];
};
```

#### <a name="remove-user-from-a-group"></a>グループからユーザーを削除する

次の例では、ユーザーをグループから削除します。

*function.json*

```json
{
  "disabled": false,
  "bindings": [
    {
      "authLevel": "anonymous",
      "type": "httpTrigger",
      "direction": "in",
      "name": "req",
      "methods": [
        "post"
      ]
    },
    {
      "type": "http",
      "direction": "out",
      "name": "res"
    },
    {
      "type": "signalR",
      "name": "signalRGroupActions",
      "connectionStringSetting": "<name of setting containing SignalR Service connection string>",
      "hubName": "chat",
      "direction": "out"
    }
  ]
}
```

*index.js*

```javascript
module.exports = async function (context, req) {
  context.bindings.signalRGroupActions = [{
    "userId": req.query.userId,
    "groupName": "myGroup",
    "action": "remove"
  }];
};
```

### <a name="2x-java-send-message-output-examples"></a>2.x Java 送信メッセージ出力の例

#### <a name="broadcast-to-all-clients"></a>すべてのクライアントにブロードキャストする

次の例は、出力バインドを使用してすべての接続済みクライアントにメッセージを送信する [Java 関数](functions-reference-java.md)を示しています。 `target` は、各クライアントで呼び出されるメソッドの名前です。 `arguments` プロパティは、クライアント メソッドに渡される 0 個以上のオブジェクトの配列です。

```java
@FunctionName("sendMessage")
@SignalROutput(name = "$return", hubName = "chat")
public SignalRMessage sendMessage(
        @HttpTrigger(
            name = "req",
            methods = { HttpMethod.POST },
            authLevel = AuthorizationLevel.ANONYMOUS) HttpRequestMessage<Object> req) {

    SignalRMessage message = new SignalRMessage();
    message.target = "newMessage";
    message.arguments.add(req.getBody());
    return message;
}
```

#### <a name="send-to-a-user"></a>ユーザーに送信する

ユーザーに対して認証された接続だけにメッセージを送信するには、SignalR メッセージの `userId` プロパティを設定します。

```java
@FunctionName("sendMessage")
@SignalROutput(name = "$return", hubName = "chat")
public SignalRMessage sendMessage(
        @HttpTrigger(
            name = "req",
            methods = { HttpMethod.POST },
            authLevel = AuthorizationLevel.ANONYMOUS) HttpRequestMessage<Object> req) {

    SignalRMessage message = new SignalRMessage();
    message.userId = "userId1";
    message.target = "newMessage";
    message.arguments.add(req.getBody());
    return message;
}
```

#### <a name="send-to-a-group"></a>グループに送信する

グループに追加された接続だけにメッセージを送信するには、SignalR メッセージの `groupName` プロパティを設定します。

```java
@FunctionName("sendMessage")
@SignalROutput(name = "$return", hubName = "chat")
public SignalRMessage sendMessage(
        @HttpTrigger(
            name = "req",
            methods = { HttpMethod.POST },
            authLevel = AuthorizationLevel.ANONYMOUS) HttpRequestMessage<Object> req) {

    SignalRMessage message = new SignalRMessage();
    message.groupName = "myGroup";
    message.target = "newMessage";
    message.arguments.add(req.getBody());
    return message;
}
```

### <a name="2x-java-group-management-output-examples"></a>2.x Java グループ管理出力の例

SignalR Service を使用すると、ユーザーをグループに追加できます。 その後にメッセージをグループに送信できます。 `SignalRGroupAction` クラスと `SignalROutput` 出力バインドを使用して、ユーザーのグループ メンバーシップを管理できます。

#### <a name="add-user-to-a-group"></a>ユーザーをグループに追加する

次の例では、ユーザーをグループに追加します。

```java
@FunctionName("addToGroup")
@SignalROutput(name = "$return", hubName = "chat")
public SignalRGroupAction addToGroup(
        @HttpTrigger(
            name = "req",
            methods = { HttpMethod.POST },
            authLevel = AuthorizationLevel.ANONYMOUS) HttpRequestMessage<Object> req,
        @BindingName("userId") String userId) {

    SignalRGroupAction groupAction = new SignalRGroupAction();
    groupAction.action = "add";
    groupAction.userId = userId;
    groupAction.groupName = "myGroup";
    return action;
}
```

#### <a name="remove-user-from-a-group"></a>グループからユーザーを削除する

次の例では、ユーザーをグループから削除します。

```java
@FunctionName("removeFromGroup")
@SignalROutput(name = "$return", hubName = "chat")
public SignalRGroupAction removeFromGroup(
        @HttpTrigger(
            name = "req",
            methods = { HttpMethod.POST },
            authLevel = AuthorizationLevel.ANONYMOUS) HttpRequestMessage<Object> req,
        @BindingName("userId") String userId) {

    SignalRGroupAction groupAction = new SignalRGroupAction();
    groupAction.action = "remove";
    groupAction.userId = userId;
    groupAction.groupName = "myGroup";
    return action;
}
```

## <a name="configuration"></a>構成

### <a name="signalrconnectioninfo"></a>SignalRConnectionInfo

次の表は、*function.json* ファイルと `SignalRConnectionInfo` 属性で設定したバインド構成のプロパティを説明しています。

|function.json のプロパティ | 属性のプロパティ |説明|
|---------|---------|----------------------|
|**type**|| `signalRConnectionInfo` に設定する必要があります。|
|**direction**|| `in` に設定する必要があります。|
|**name**|| 接続情報オブジェクトの関数コードで使用される変数名。 |
|**hubName**|**HubName**| この値は、接続情報が生成される SignalR ハブの名前に設定する必要があります。|
|**userId**|**UserId**| 省略可能:アクセス キー トークンに設定するユーザー識別子要求の値。 |
|**connectionStringSetting**|**ConnectionStringSetting**| SignalR Service 接続文字列を含むアプリ設定の名前 (既定値は "AzureSignalRConnectionString") |

### <a name="signalr"></a>SignalR

次の表は、*function.json* ファイルと `SignalR` 属性で設定したバインド構成のプロパティを説明しています。

|function.json のプロパティ | 属性のプロパティ |説明|
|---------|---------|----------------------|
|**type**|| `signalR` に設定する必要があります。|
|**direction**|| `out` に設定する必要があります。|
|**name**|| 接続情報オブジェクトの関数コードで使用される変数名。 |
|**hubName**|**HubName**| この値は、接続情報が生成される SignalR ハブの名前に設定する必要があります。|
|**connectionStringSetting**|**ConnectionStringSetting**| SignalR Service 接続文字列を含むアプリ設定の名前 (既定値は "AzureSignalRConnectionString") |

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [Azure Functions のトリガーとバインドの詳細情報](functions-triggers-bindings.md)

> [!div class="nextstepaction"]
> [Azure SignalR Service を使用した Azure Functions の開発と構成](../azure-signalr/signalr-concept-serverless-development-config.md)
