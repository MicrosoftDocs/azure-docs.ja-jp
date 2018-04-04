---
title: Azure Functions における HTTP と Webhook のバインド
description: Azure Functions で HTTP トリガー、Webhook トリガー、バインドを使用する方法について説明します。
services: functions
documentationcenter: na
author: mattchenderson
manager: cfowler
editor: ''
tags: ''
keywords: Azure Functions, 関数, イベント処理, webhook, 動的コンピューティング, サーバーレス アーキテクチャ, HTTP, API, REST
ms.service: functions
ms.devlang: multiple
ms.topic: reference
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 11/21/2017
ms.author: mahender
ms.openlocfilehash: a46177183035a53128c5341a3ce4c63dbc3a7497
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/16/2018
---
# <a name="azure-functions-http-and-webhook-bindings"></a>Azure Functions における HTTP と Webhook のバインド

この記事では、Azure Functions で HTTP バインドを使用する方法について説明します。 Azure Functions は、HTTP のトリガーと出力バインドをサポートしています。

HTTP トリガーは [webhook](https://en.wikipedia.org/wiki/Webhook) に応答するようにカスタマイズすることができます。 webhook トリガーは JSON ペイロードのみを受け入れ、JSON を検証します。 webhook トリガーには特別なバージョンがあり、そのバージョンでは特定のプロバイダー (GitHub や Slack など) の webhook の処理が容易になります。

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

[!INCLUDE [HTTP client best practices](../../includes/functions-http-client-best-practices.md)]

## <a name="packages"></a>パッケージ

HTTP バインディングは [Microsoft.Azure.WebJobs.Extensions.Http](http://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.Http) NuGet パッケージで提供されます。 パッケージのソース コードは、[azure-webjobs-sdk-extensions](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.Http/) GitHub リポジトリにあります。

[!INCLUDE [functions-package-auto](../../includes/functions-package-auto.md)]

## <a name="trigger"></a>トリガー

HTTP トリガーでは、HTTP 要求で関数を呼び出すことができます。 HTTP トリガーを使用して、サーバーなしの API を構築し、webhook に応答することができます。 

既定では、HTTP トリガーは要求に HTTP 200 OK 状態コードおよび空の本文で応答します。 応答を変更するには、[HTTP 出力バインド](#http-output-binding)を構成します。

## <a name="trigger---example"></a>トリガー - 例

言語固有の例をご覧ください。

* [C#](#trigger---c-example)
* [C# スクリプト (.csx)](#trigger---c-script-example)
* [F#](#trigger---f-example)
* [JavaScript](#trigger---javascript-example)

### <a name="trigger---c-example"></a>トリガー - C# の例

次の例は、クエリ文字列または HTTP 要求の本文で `name`パラメーターを探す [C# 関数](functions-dotnet-class-library.md)を示しています。

```cs
[FunctionName("HttpTriggerCSharp")]
public static async Task<HttpResponseMessage> Run(
    [HttpTrigger(AuthorizationLevel.Function, "get", "post", Route = null)]HttpRequestMessage req, 
    TraceWriter log)
{
    log.Info("C# HTTP trigger function processed a request.");

    // parse query parameter
    string name = req.GetQueryNameValuePairs()
        .FirstOrDefault(q => string.Compare(q.Key, "name", true) == 0)
        .Value;

    // Get request body
    dynamic data = await req.Content.ReadAsAsync<object>();

    // Set name to query string or body data
    name = name ?? data?.name;

    return name == null
        ? req.CreateResponse(HttpStatusCode.BadRequest, "Please pass a name on the query string or in the request body")
        : req.CreateResponse(HttpStatusCode.OK, "Hello " + name);
}
```

### <a name="trigger---c-script-example"></a>トリガー - C# スクリプトの例

次の例は、*function.json* ファイルのトリガー バインドと、そのバインドが使用される [C# スクリプト関数](functions-reference-csharp.md)を示しています。 この関数は、クエリ文字列または HTTP 要求の本文で `name` パラメーターを探します。

*function.json* ファイルのバインディング データを次に示します。

```json
{
    "name": "req",
    "type": "httpTrigger",
    "direction": "in",
    "authLevel": "function"
},
```

これらのプロパティについては、「[構成](#trigger---configuration)」セクションを参照してください。

`HttpRequestMessage` にバインドする C# スクリプト コードを次に示します。

```csharp
using System.Net;
using System.Threading.Tasks;

public static async Task<HttpResponseMessage> Run(HttpRequestMessage req, TraceWriter log)
{
    log.Info($"C# HTTP trigger function processed a request. RequestUri={req.RequestUri}");

    // parse query parameter
    string name = req.GetQueryNameValuePairs()
        .FirstOrDefault(q => string.Compare(q.Key, "name", true) == 0)
        .Value;

    // Get request body
    dynamic data = await req.Content.ReadAsAsync<object>();

    // Set name to query string or body data
    name = name ?? data?.name;

    return name == null
        ? req.CreateResponse(HttpStatusCode.BadRequest, "Please pass a name on the query string or in the request body")
        : req.CreateResponse(HttpStatusCode.OK, "Hello " + name);
}
```

`HttpRequestMessage` の代わりにカスタム オブジェクトにバインドできます。 このオブジェクトは、JSON として解析され、要求の本文から作成されます。 同様に、型は HTTP 応答出力バインディングに渡すことができ、200 のステータス コードと共に、応答本文として返されます。

```csharp
using System.Net;
using System.Threading.Tasks;

public static string Run(CustomObject req, TraceWriter log)
{
    return "Hello " + req?.name;
}

public class CustomObject {
     public String name {get; set;}
}
```

### <a name="trigger---f-example"></a>トリガー - F# の例

次の例は、*function.json* ファイルのトリガー バインドと、そのバインドが使用される [F# 関数](functions-reference-fsharp.md)を示しています。 この関数は、クエリ文字列または HTTP 要求の本文で `name` パラメーターを探します。

*function.json* ファイルのバインディング データを次に示します。

```json
{
    "name": "req",
    "type": "httpTrigger",
    "direction": "in",
    "authLevel": "function"
},
```

これらのプロパティについては、「[構成](#trigger---configuration)」セクションを参照してください。

F# コードを次に示します。

```fsharp
open System.Net
open System.Net.Http
open FSharp.Interop.Dynamic

let Run(req: HttpRequestMessage) =
    async {
        let q =
            req.GetQueryNameValuePairs()
                |> Seq.tryFind (fun kv -> kv.Key = "name")
        match q with
        | Some kv ->
            return req.CreateResponse(HttpStatusCode.OK, "Hello " + kv.Value)
        | None ->
            let! data = Async.AwaitTask(req.Content.ReadAsAsync<obj>())
            try
                return req.CreateResponse(HttpStatusCode.OK, "Hello " + data?name)
            with e ->
                return req.CreateErrorResponse(HttpStatusCode.BadRequest, "Please pass a name on the query string or in the request body")
    } |> Async.StartAsTask
```

次の例に示すように、NuGet を使用して `FSharp.Interop.Dynamic` および `Dynamitey` アセンブリを参照する `project.json` ファイルが必要です。

```json
{
  "frameworks": {
    "net46": {
      "dependencies": {
        "Dynamitey": "1.0.2",
        "FSharp.Interop.Dynamic": "3.0.0"
      }
    }
  }
}
```

### <a name="trigger---javascript-example"></a>トリガー - JavaScript の例

次の例は、*function.json* ファイルのトリガー バインドと、そのバインドを使用する [JavaScript 関数](functions-reference-node.md)を示しています。 この関数は、クエリ文字列または HTTP 要求の本文で `name` パラメーターを探します。

*function.json* ファイルのバインディング データを次に示します。

```json
{
    "name": "req",
    "type": "httpTrigger",
    "direction": "in",
    "authLevel": "function"
},
```

これらのプロパティについては、「[構成](#trigger---configuration)」セクションを参照してください。

JavaScript コードを次に示します。

```javascript
module.exports = function(context, req) {
    context.log('Node.js HTTP trigger function processed a request. RequestUri=%s', req.originalUrl);

    if (req.query.name || (req.body && req.body.name)) {
        context.res = {
            // status: 200, /* Defaults to 200 */
            body: "Hello " + (req.query.name || req.body.name)
        };
    }
    else {
        context.res = {
            status: 400,
            body: "Please pass a name on the query string or in the request body"
        };
    }
    context.done();
};
```
     
## <a name="trigger---webhook-example"></a>トリガー - webhook の例

言語固有の例をご覧ください。

* [C#](#webhook---c-example)
* [C# スクリプト (.csx)](#webhook---c-script-example)
* [F#](#webhook---f-example)
* [JavaScript](#webhook---javascript-example)

### <a name="webhook---c-example"></a>webhook - C# の例

次の例は、一般的な JSON 要求への応答で HTTP 200 を送信する [C# 関数](functions-dotnet-class-library.md)を示しています。

```cs
[FunctionName("HttpTriggerCSharp")]
public static HttpResponseMessage Run([HttpTrigger(AuthorizationLevel.Anonymous, WebHookType = "genericJson")] HttpRequestMessage req)
{
    return req.CreateResponse(HttpStatusCode.OK);
}
```

### <a name="webhook---c-script-example"></a>webhook - C# スクリプトの例

次の例は、*function.json* ファイルの webhook トリガー バインドと、そのバインドが使用される [C# スクリプト関数](functions-reference-csharp.md)を示しています。 この関数は、GitHub の問題に対するコメントをログに記録します。

*function.json* ファイルのバインディング データを次に示します。

```json
{
    "webHookType": "github",
    "name": "req",
    "type": "httpTrigger",
    "direction": "in",
},
```

これらのプロパティについては、「[構成](#trigger---configuration)」セクションを参照してください。

C# スクリプト コードを次に示します。

```csharp
#r "Newtonsoft.Json"

using System;
using System.Net;
using System.Threading.Tasks;
using Newtonsoft.Json;

public static async Task<object> Run(HttpRequestMessage req, TraceWriter log)
{
    string jsonContent = await req.Content.ReadAsStringAsync();
    dynamic data = JsonConvert.DeserializeObject(jsonContent);

    log.Info($"WebHook was triggered! Comment: {data.comment.body}");

    return req.CreateResponse(HttpStatusCode.OK, new {
        body = $"New GitHub comment: {data.comment.body}"
    });
}
```

### <a name="webhook---f-example"></a>webhook - F# の例

次の例は、*function.json* ファイルの webhook トリガー バインドと、そのバインドが使用される [F# 関数](functions-reference-fsharp.md)を示しています。 この関数は、GitHub の問題に対するコメントをログに記録します。

*function.json* ファイルのバインディング データを次に示します。

```json
{
    "webHookType": "github",
    "name": "req",
    "type": "httpTrigger",
    "direction": "in",
},
```

これらのプロパティについては、「[構成](#trigger---configuration)」セクションを参照してください。

F# コードを次に示します。

```fsharp
open System.Net
open System.Net.Http
open FSharp.Interop.Dynamic
open Newtonsoft.Json

type Response = {
    body: string
}

let Run(req: HttpRequestMessage, log: TraceWriter) =
    async {
        let! content = req.Content.ReadAsStringAsync() |> Async.AwaitTask
        let data = content |> JsonConvert.DeserializeObject
        log.Info(sprintf "GitHub WebHook triggered! %s" data?comment?body)
        return req.CreateResponse(
            HttpStatusCode.OK,
            { body = sprintf "New GitHub comment: %s" data?comment?body })
    } |> Async.StartAsTask
```

### <a name="webhook---javascript-example"></a>webhook - JavaScript の例

次の例は、*function.json* ファイルの webhook トリガー バインドと、そのバインドが使用される [JavaScript 関数](functions-reference-node.md)を示しています。 この関数は、GitHub の問題に対するコメントをログに記録します。

*function.json* ファイルのバインディング データを次に示します。

```json
{
    "webHookType": "github",
    "name": "req",
    "type": "httpTrigger",
    "direction": "in",
},
```

これらのプロパティについては、「[構成](#trigger---configuration)」セクションを参照してください。

JavaScript コードを次に示します。

```javascript
module.exports = function (context, data) {
    context.log('GitHub WebHook triggered!', data.comment.body);
    context.res = { body: 'New GitHub comment: ' + data.comment.body };
    context.done();
};
```

## <a name="trigger---attributes"></a>トリガー - 属性

[C# クラス ライブラリ](functions-dotnet-class-library.md)では、[HttpTrigger](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/dev/src/WebJobs.Extensions.Http/HttpTriggerAttribute.cs) 属性を使用します。

属性のコンストラクターのパラメーターに承認レベルと許容される HTTP メソッドを設定できます。また、webhook の種類とルートのテンプレートに対応するプロパティがあります。 これらの設定の詳細については、「[トリガー - 構成](#trigger---configuration)」を参照してください。 メソッド シグネチャでの `HttpTrigger` 属性を次に示します。

```csharp
[FunctionName("HttpTriggerCSharp")]
public static HttpResponseMessage Run(
    [HttpTrigger(AuthorizationLevel.Anonymous, WebHookType = "genericJson")] HttpRequestMessage req)
{
    ...
}
 ```

完全な例については、「[トリガー - C# の例](#trigger---c-example)」を参照してください。

## <a name="trigger---configuration"></a>トリガー - 構成

次の表は、*function.json* ファイルと `HttpTrigger` 属性で設定したバインド構成のプロパティを説明しています。


|function.json のプロパティ | 属性のプロパティ |説明|
|---------|---------|----------------------|
| **type** | 該当なし| 必須 - `httpTrigger` に設定する必要があります。 |
| **direction** | 該当なし| 必須 - `in` に設定する必要があります。 |
| **name** | 該当なし| 必須 - 要求または要求本文の関数コードで使用される変数名。 |
| <a name="http-auth"></a>**authLevel** |  **AuthLevel** |関数を呼び出すために、要求にどのキーが存在する必要があるかを決定します。 承認レベルは、次のいずれかの値になります。 <ul><li><code>anonymous</code>&mdash;API キーは必要ありません。</li><li><code>function</code>&mdash;関数固有の API キーが必要です。 何も指定されなかった場合は、これが既定値になります。</li><li><code>admin</code>&mdash;マスター キーが必要です。</li></ul> 詳しくは、[承認キー](#authorization-keys)に関するセクションをご覧ください。 |
| **methods** |**メソッド** | 関数が応答する HTTP メソッドの配列。 指定しない場合、関数はすべての HTTP メソッドに応答します。 「[HTTP エンドポイントのカスタマイズ](#trigger---customize-the-http-endpoint)」をご覧ください。 |
| **route** | **Route** | 関数がどの要求 URL に応答するかを制御するルート テンプレートを定義します。 何も指定しなかった場合の既定値は `<functionname>` です。 詳しくは、「[HTTP エンドポイントのカスタマイズ](#customize-the-http-endpoint)」をご覧ください。 |
| **webHookType** | **WebHookType** |指定したプロバイダーの [webhook](https://en.wikipedia.org/wiki/Webhook) レシーバーとして機能する HTTP トリガーを構成します。 このプロパティを設定する場合は、`methods` プロパティを設定しないでください。 webhook の種類は、次のいずれかの値になります。<ul><li><code>genericJson</code>&mdash;特定のプロバイダー用のロジックを持たない汎用 webhook エンドポイントです。 この設定では、要求が、HTTP POST を使用していてコンテンツの種類が `application/json` であるものだけに制限されます。</li><li><code>github</code>&mdash;関数は [GitHub webhook](https://developer.github.com/webhooks/) に応答します。 GitHub webhook に対して _authLevel_ プロパティを使用しないでください。 詳しくは、この記事で後述する「GitHub webhook」セクションをご覧ください。</li><li><code>slack</code>&mdash;関数は [Slack webhook](https://api.slack.com/outgoing-webhooks) に応答します。 Slack webhook に対して _authLevel_ プロパティを使用しないでください。 詳しくは、この記事で後述する「Slack webhook」セクションをご覧ください。</li></ul>|

## <a name="trigger---usage"></a>トリガー - 使用方法

C# および F# の関数では、トリガー入力の型を `HttpRequestMessage` 型かカスタム型として宣言できます。 `HttpRequestMessage` を選択した場合は、要求オブジェクトへのフル アクセスが取得されます。 カスタム型 の場合、関数は JSON 要求本文を解析して、オブジェクトのプロパティを設定しようとします。 

JavaScript 関数の場合、Functions ランタイムは request オブジェクトではなく、要求本文を提供します。 詳しくは、[JavaScript トリガーの例](#trigger---javascript-example)をご覧ください。

### <a name="github-webhooks"></a>GitHub webhook

GitHub webhook に応答するには、まず、HTTP トリガーで関数を作成し、**webHookType** プロパティを `github` に設定します。 次に、その URL と API キーを GitHub リポジトリの **[webhook の追加]** ページにコピーします。 

![](./media/functions-bindings-http-webhook/github-add-webhook.png)

例については、「[GitHub webhook でトリガーされる関数の作成](functions-create-github-webhook-triggered-function.md)」を参照してください。

### <a name="slack-webhooks"></a>Slack webhook

Slack webhook では、指定しなくてもトークンが自動的に生成されます。そのため、Slack によって生成されたトークンで、関数固有のキーを構成する必要があります。 「[承認キー](#authorization-keys)」をご覧ください。

### <a name="customize-the-http-endpoint"></a>HTTP エンドポイントのカスタマイズ

既定では、HTTP トリガーまたは WebHook の関数を作成する際に、次の形式のルートを使用して関数のアドレスを指定できます。

    http://<yourapp>.azurewebsites.net/api/<funcname> 

HTTP トリガーの入力バインドで省略可能な `route` プロパティを使用すると、このルートをカスタマイズできます。 たとえば、次の *function.json* ファイルでは HTTP トリガーの `route` プロパティを定義します。

```json
{
    "bindings": [
    {
        "type": "httpTrigger",
        "name": "req",
        "direction": "in",
        "methods": [ "get" ],
        "route": "products/{category:alpha}/{id:int?}"
    },
    {
        "type": "http",
        "name": "res",
        "direction": "out"
    }
    ]
}
```

この構成を使用すると、元のルートではなく、次のルートを使用して関数のアドレスを指定できるようになります。

```
http://<yourapp>.azurewebsites.net/api/products/electronics/357
```

これにより、関数のコードはアドレス内で _category_ と _id_ という 2 つのパラメーターをサポートできます。パラメーターでは任意の [Web API ルート制約](https://www.asp.net/web-api/overview/web-api-routing-and-actions/attribute-routing-in-web-api-2#constraints)を使用できます。 次の C# 関数コードは両方のパラメーターを使用します。

```csharp
public static Task<HttpResponseMessage> Run(HttpRequestMessage req, string category, int? id, 
                                                TraceWriter log)
{
    if (id == null)
        return  req.CreateResponse(HttpStatusCode.OK, $"All {category} items were requested.");
    else
        return  req.CreateResponse(HttpStatusCode.OK, $"{category} item with id = {id} has been requested.");
}
```

同じルート パラメーターを使用する Node.js 関数コードを次に示します。

```javascript
module.exports = function (context, req) {

    var category = context.bindingData.category;
    var id = context.bindingData.id;

    if (!id) {
        context.res = {
            // status: 200, /* Defaults to 200 */
            body: "All " + category + " items were requested."
        };
    }
    else {
        context.res = {
            // status: 200, /* Defaults to 200 */
            body: category + " item with id = " + id + " was requested."
        };
    }

    context.done();
} 
```

既定では、すべての関数のルートには *api* というプレフィックスが付きます。 [host.json](functions-host-json.md) ファイルで `http.routePrefix` プロパティを使用すると、このプレフィックスをカスタマイズまたは削除できます。 次の例では、*host.json* ファイル内でプレフィックスに空の文字列を使用することで、*api* ルート プレフィックスを削除します。

```json
{
    "http": {
    "routePrefix": ""
    }
}
```

### <a name="authorization-keys"></a>承認キー

HTTP トリガーを使用すると、セキュリティを強化するためにキーを利用できます。 標準的な HTTP トリガーは、それらを API キーとして使用できます。キーは、要求内に存在する必要があります。 webhook はキーを使用して、プロバイダーで何がサポートされているかに応じてさまざまな方法で要求を承認することができます。

> [!NOTE]
> 関数をローカルで実行している場合、`function.json` で設定された `authLevel` に関係なく、承認は無効になります。 Azure Functions に発行すると、`authLevel` は直ちに有効になります。

キーは関数アプリの一部として Azure に格納され、保存中は暗号化されます。 キーを表示するには、新しいキーを作成するか、キーを新しい値にロールし、ポータル内でいずれかの関数に移動して、[管理] を選択します。 

キーには、次の 2 つの種類があります。

- **ホスト キー**: これらのキーは、関数アプリ内のすべての関数で共有されます。 API キーとして使用した場合は、関数アプリ内のすべての関数がアクセスできます。
- **関数キー**: これらのキーは、それらが定義されている特定の関数にのみ適用されます。 API キーとして使用した場合は、その関数だけがアクセスできます。

各キーには、参照用に名前が付けられており、関数レベルおよびホスト レベルで "default" という名前の既定のキーがあります。 関数キーが、ホスト キーよりも優先されます。 2 つのキーが同じ名前で定義されている場合は、関数キーが使用されます。

**マスター キー**は "_master" という名前の既定のホスト キーであり、関数アプリごとに定義されています。 このキーを取り消すことはできません。 このキーによって、ランタイム API に対する管理アクセスが可能になります。 バインド JSON で `"authLevel": "admin"` を使用するには、要求内にこのキーが存在する必要があります。他のキーである場合は、承認エラーになります。

> [!IMPORTANT]  
> マスター キーは管理者特権を付与するものであるため、このキーを第三者と共有したり、ネイティブ クライアント アプリケーションで配布したりしないでください。 管理者承認レベルを選択する場合は注意が必要です。

### <a name="api-key-authorization"></a>API キーの承認

既定では、HTTP トリガーには HTTP 要求内の API キーが必要です。 そのため、HTTP 要求は、通常は次のようになります。

    https://<yourapp>.azurewebsites.net/api/<function>?code=<ApiKey>

キーは、上記のように `code` という名前のクエリ文字列変数に含めることも、`x-functions-key` HTTP ヘッダーに含めることもできます。 キーの値には、関数のために定義されている任意の関数キーまたは任意のホスト キーを指定できます。

匿名要求を許可できます。この要求ではキーが不要です。 マスター キーを使用するように要求することもできます。 既定の承認レベルを変更するには、バインド JSON の `authLevel` プロパティを使用します。 詳しくは、「[トリガー - 構成](#trigger---configuration)」をご覧ください。

### <a name="keys-and-webhooks"></a>キーと webhook

webhook の承認は、HTTP トリガーの一部である webhook レシーバー コンポーネントによって処理されますが、そのメカニズムは webhook の種類によって異なります。 ただし、各メカニズムはキーに依存します。 既定では、"default" という名前の関数キーが使用されます。 別のキーを使用するには、次のいずれかの方法で、要求と共にキー名を送信するように webhook プロバイダーを構成します。

- **クエリ文字列**: プロバイダーが `clientid` クエリ文字列パラメーターでキー名を渡します (`https://<yourapp>.azurewebsites.net/api/<funcname>?clientid=<keyname>` など)。
- **要求ヘッダー**: プロバイダーが `x-functions-clientid` ヘッダーでキー名を渡します。

## <a name="trigger---limits"></a>トリガー - 制限

HTTP 要求の長さは 100 K (102,400) バイトに、URL の長さは 4 K (4,096) バイトに制限されています。 これらの制限は、ランタイムの [Web.config ファイル](https://github.com/Azure/azure-webjobs-sdk-script/blob/v1.x/src/WebJobs.Script.WebHost/Web.config)の `httpRuntime` 要素で指定されています。

HTTP トリガーを使用する関数が約 2.5 分以内に完了しない場合、ゲートウェイでタイムアウトが発生し、HTTP 502 エラーが返されます。 この関数は実行を継続しますが、HTTP 応答を返すことはできません。 実行時間が長い関数の場合は、非同期パターンに従い、要求の状態について ping で確認できる場所を返すことをお勧めします。 関数を実行できる時間については、[スケールとホスティングに関するページの「従量課金プラン」](functions-scale.md#consumption-plan)を参照してください。 

## <a name="trigger---hostjson-properties"></a>トリガー - host.json のプロパティ

[host.json](functions-host-json.md) ファイルには、HTTP トリガーの動作を制御する設定が含まれています。

[!INCLUDE [functions-host-json-http](../../includes/functions-host-json-http.md)]

## <a name="output"></a>出力

HTTP 要求送信者に応答するには、HTTP 出力バインドを使用します。 このバインドには、HTTP トリガーが必要です。このバインドを使用すると、トリガーの要求に関連付けられている応答をカスタマイズすることができます。 HTTP 出力バインドが指定されない場合、HTTP トリガーは HTTP 200 OK と空の本文を返します。 

## <a name="output---configuration"></a>出力 - 構成

C# クラス ライブラリの場合、出力固有のバインド構成プロパティはありません。 HTTP 応答を送信するには、関数が型 `HttpResponseMessage` または `Task<HttpResponseMessage>` を戻すようにします。

他の言語では、HTTP 出力バインドは、次の例に示すように、function.json の `bindings` 配列の JSON オブジェクトとして定義されます。

```json
{
    "name": "res",
    "type": "http",
    "direction": "out"
}
```

次の表は、*function.json* ファイルで設定したバインド構成のプロパティを説明しています。

|プロパティ  |[説明]  |
|---------|---------|
| **type** |`http` に設定する必要があります。 |
| **direction** | `out` に設定する必要があります。 |
|**name** | 応答の関数コードで使用される変数名。 |

## <a name="output---usage"></a>出力 - 使用方法

HTTP または webhook の呼び出し元に応答するために、出力パラメーターを使用できます。 また、言語標準の応答パターンを使用することもできます。 応答の例については、[トリガーの例](#trigger---example)および[webhook の例](#trigger---webhook-example)をご覧ください。

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [Azure Functions のトリガーとバインドの詳細情報](functions-triggers-bindings.md)
