---
title: "Azure Functions における HTTP と webhook のバインド | Microsoft Docs"
description: "Azure Functions で HTTP トリガー、Webhook トリガー、バインドを使用する方法について説明します。"
services: functions
documentationcenter: na
author: mattchenderson
manager: erikre
editor: 
tags: 
keywords: "Azure Functions, 関数, イベント処理, webhook, 動的コンピューティング, サーバーレス アーキテクチャ, HTTP, API, REST"
ms.assetid: 2b12200d-63d8-4ec1-9da8-39831d5a51b1
ms.service: functions
ms.devlang: multiple
ms.topic: reference
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 11/18/2016
ms.author: mahender
translationtype: Human Translation
ms.sourcegitcommit: 503f5151047870aaf87e9bb7ebf2c7e4afa27b83
ms.openlocfilehash: 06958522139d621f86afd8bf25128ee64cf822b3
ms.lasthandoff: 03/29/2017


---
# <a name="azure-functions-http-and-webhook-bindings"></a>Azure Functions における HTTP と Webhook のバインド
[!INCLUDE [functions-selector-bindings](../../includes/functions-selector-bindings.md)]

この記事では、Azure Functions で HTTP トリガーとバインドを構成および操作する方法について説明します。
これらと Azure Functions を使用して、サーバーなしの API を構築し、webhook に応答することができます。

Azure Functions には、以下のバインドが用意されています。
- [HTTP トリガー](#httptrigger)では、HTTP 要求で関数を呼び出すことができます。 これを [webhook](#hooktrigger) に応答するようにカスタマイズすることができます。
- [HTTP 出力バインド](#output)では、要求に応答することができます。

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

> [!TIP]
>
> [HTTPClient](https://github.com/mspnp/performance-optimization/blob/master/ImproperInstantiation/docs/ImproperInstantiation.md) のベスト プラクティスに関するドキュメントを読むことをお勧めします。
>

<a name="httptrigger"></a>

## <a name="http-trigger"></a>HTTP トリガー
HTTP トリガーは、HTTP 要求に応答して、関数を実行します。 これを、特定の URL または HTTP メソッドのセットに応答するようにカスタマイズできます。 HTTP トリガーも、webhook に応答するように構成することができます。 

Functions ポータルを使用している場合は、事前作成されたテンプレートをすぐに使用し始めることもできます。 **[新しい関数]** を選択し、**[シナリオ]** ドロップダウンで [API と webhook] を選択します。 いずれかのテンプレートを選択し、**[作成]** をクリックします。

既定では、HTTP トリガーは要求に HTTP 200 OK 状態コードおよび空の本文で応答します。 応答を変更するには、[HTTP 出力バインド](#output)を構成します。

### <a name="configuring-an-http-trigger"></a>HTTP トリガーの構成
HTTP トリガーを定義するには、次のような JSON オブジェクトを function.json の `bindings` 配列に含めます。

```json
{
    "name": "req",
    "type": "httpTrigger",
    "direction": "in",
    "authLevel": "function",
    "methods": [ "GET" ],
    "route": "values/{id}"
},
```
バインドでは以下のプロパティがサポートされています。

* **name**: 必須 - 要求または要求本文の関数コードで使用される変数名。 「[コードからの HTTP トリガーの操作](#httptriggerusage)」を参照してください。
* **type**: 必須 - "httpTrigger" に設定する必要があります。
* **direction**: 必須 - "in" に設定する必要があります。
* _authLevel_: 関数を呼び出すには要求にどのキーが存在する必要があるかを決定します。 後の「[キーの操作](#keys)」を参照してください。 値は次のいずれかになります。
    * _anonymous_: API キーは必要ありません。
    * _function_: 関数固有の API キーが必要です。 何も指定されなかった場合は、これが既定値になります。
    * _admin_: マスター キーが必要です。
* **methods**: 関数が応答する HTTP メソッドの配列です。 指定しない場合、関数はすべての HTTP メソッドに応答します。 「[HTTP エンドポイントのカスタマイズ](#url)」を参照してください。
* **route**: 関数がどの要求 URL に応答するかを制御するルート テンプレートを定義します。 何も指定しなかった場合の既定値は `<functionname>` です。 「[HTTP エンドポイントのカスタマイズ](#url)」を参照してください。
* **webHookType**: 指定したプロバイダーの webhook レシーバーとして機能する HTTP トリガーを構成します。 これを選択した場合は、_methods_ プロパティを設定しないでください。 「[webhook への応答](#hooktrigger)」を参照してください。 値は次のいずれかになります。
    * _genericJson_: 特定のプロバイダー用のロジックを持たない汎用 webhook エンドポイントです。
    * _github_: 関数は GitHub webhook に応答します。 これを選択した場合は、_authLevel_ プロパティを設定しないでください。
    * _slack_: 関数は Slack webhook に応答します。 これを選択した場合は、_authLevel_ プロパティを設定しないでください。

<a name="httptriggerusage"></a>
### <a name="working-with-an-http-trigger-from-code"></a>コードからの HTTP トリガーの操作
C# および F# の関数では、トリガー入力の型を `HttpRequestMessage` 型かカスタム型として宣言できます。 `HttpRequestMessage` を選択した場合は、要求オブジェクトへのフル アクセスが取得されます。 カスタム型 (POCO など) の場合、関数は要求本文を JSON として解析し、オブジェクトのプロパティに値を設定しようとします。

Node.js 関数の場合、Functions ランタイムは request オブジェクトではなく、要求本文を提供します。

使用例については、「[HTTP トリガーのサンプル](#httptriggersample)」を参照してください。


<a name="output"></a>
## <a name="http-response-output-binding"></a>HTTP 応答出力バインド
HTTP 要求送信者に応答するには、HTTP 出力バインドを使用します。 このバインドには、HTTP トリガーが必要です。このバインドを使用すると、トリガーの要求に関連付けられている応答をカスタマイズすることができます。 HTTP 出力バインドが指定されない場合、HTTP トリガーは HTTP 200 OK と空の本文を返します。 

### <a name="configuring-an-http-output-binding"></a>HTTP 出力バインドの構成
HTTP 出力バインドを定義するには、次のような JSON オブジェクトを function.json の `bindings` 配列に含めます。

```json
{
    "name": "res",
    "type": "http",
    "direction": "out"
}
```
バインドには、以下のプロパティが含まれています。

* **name**: 必須 - 応答の関数コードで使用される変数名。 「[コードからの HTTP 出力バインドの操作](#outputusage)」を参照してください。
* **type**: 必須 - "http" に設定する必要があります。
* **direction**: 必須 - "out" に設定する必要があります。

<a name="outputusage"></a>
### <a name="working-with-an-http-output-binding-from-code"></a>コードからの HTTP 出力バインドの操作
http または webhook の呼び出し元に応答するために、出力パラメーター ("res" など) を使用できます。 また、応答を返すために、標準の `Request.CreateResponse()` (C#) または `context.res` (Node.JS) パターンを使用することもできます。 後のメソッドの使用方法の例については、「[HTTP トリガーのサンプル](#httptriggersample)」および「[webhook のサンプル](#hooktriggersample)」を参照してください。


<a name="hooktrigger"></a>
## <a name="responding-to-webhooks"></a>webhook への応答
_webHookType_ プロパティのある HTTP トリガーは、[webhooks](https://en.wikipedia.org/wiki/Webhook) に応答するように構成されます。 基本的な構成では、"genericJson" 設定を使用します。 この設定では、要求が、HTTP POST を使用していてコンテンツの種類が `application/json` であるものだけに制限されます。

トリガーは、さらに特定の webhook プロバイダー ([GitHub](https://developer.github.com/webhooks/)、[Slack](https://api.slack.com/outgoing-webhooks) など) 用にカスタマイズできます。 プロバイダーが指定されている場合は、Functions ランタイムがプロバイダーの検証ロジックを処理できます。  

### <a name="configuring-github-as-a-webhook-provider"></a>webhook プロバイダーとしての GitHub の構成
GitHub webhook に応答するには、まず、HTTP トリガーで関数を作成し、_webHookType_ プロパティを "github" に設定します。 次に、その [URL](#url) と [API キー](#keys)を GitHub リポジトリの **[Webhook の追加]** ページにコピーします。 詳細については、GitHub のドキュメント「[Creating Webhooks (webhook の作成)](http://go.microsoft.com/fwlink/?LinkID=761099&clcid=0x409)」を参照してください。

![](./media/functions-bindings-http-webhook/github-add-webhook.png)

### <a name="configuring-slack-as-a-webhook-provider"></a>webhook プロバイダーとしての Slack の構成
Slack webhook では、指定しなくてもトークンが自動的に生成されます。そのため、Slack によって生成されたトークンで、関数固有のキーを構成する必要があります。 「[キーの操作](#keys)」を参照してください。

<a name="url"></a>
## <a name="customizing-the-http-endpoint"></a>HTTP エンドポイントのカスタマイズ
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

    http://<yourapp>.azurewebsites.net/api/products/electronics/357

これにより、関数のコードはアドレス内で "category" と "id" という 2 つのパラメーターをサポートできます。 パラメーターでは任意の [Web API ルート制約](https://www.asp.net/web-api/overview/web-api-routing-and-actions/attribute-routing-in-web-api-2#constraints)を使用できます。 次の C# 関数コードは両方のパラメーターを使用します。

```csharp
    public static Task<HttpResponseMessage> Run(HttpRequestMessage request, string category, int? id, 
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

既定では、すべての関数のルートには *api* というプレフィックスが付きます。 *host.json* ファイルで `http.routePrefix` プロパティを使用すると、このプレフィックスをカスタマイズまたは削除できます。 次の例では、*host.json* ファイル内でプレフィックスに空の文字列を使用することで、*api* ルート プレフィックスを削除します。

```json
    {
      "http": {
        "routePrefix": ""
      }
    }
```

関数の *host.json* ファイルを更新する方法の詳細については、「[関数アプリ ファイルを更新する方法](functions-reference.md#fileupdate)」を参照してください。 

*host.json* ファイルで構成できるその他のプロパティについては、[host.json のリファレンス](https://github.com/Azure/azure-webjobs-sdk-script/wiki/host.json)を参照してください。


<a name="keys"></a>
## <a name="working-with-keys"></a>キーの操作
HttpTrigger は、セキュリティを強化するためにキーを利用できます。 標準的な HttpTrigger は、それらを API キーとして使用できます。キーは、要求内に存在する必要があります。 webhook はキーを使用して、プロバイダーで何がサポートされているかに応じてさまざまな方法で要求を承認することができます。

キーは関数アプリの一部として Azure に格納され、保存中は暗号化されます。 キーを表示するには、新しいキーを作成するか、キーを新しい値にロールし、ポータル内でいずれかの関数に移動して、[管理] を選択します。 

キーには、次の 2 つの種類があります。
- **ホスト キー**: これらのキーは、関数アプリ内のすべての関数で共有されます。 API キーとして使用した場合は、関数アプリ内のすべての関数がアクセスできます。
- **関数キー**: これらのキーは、それらが定義されている特定の関数にのみ適用されます。 API キーとして使用した場合は、その関数だけがアクセスできます。

各キーには、参照用に名前が付けられており、関数レベルおよびホスト レベルで "default" という名前の既定のキーがあります。 **マスター キー**は "_master" という名前の既定のホスト キーであり、各関数アプリに対して定義されています。これを取り消すことはできません。 このキーによって、ランタイム API に対する管理アクセスが可能になります。 バインド JSON で `"authLevel": "admin"` を使用するには、要求内にこのキーが存在する必要があります。他のキーである場合は、承認エラーになります。

> [!NOTE]
> マスター キーは管理者特権を付与するものであるため、このキーを第三者と共有したり、ネイティブ クライアント アプリケーションで配布したりしないでください。 管理者承認レベルを選択する場合は注意が必要です。
> 
> 

### <a name="api-key-authorization"></a>API キーの承認
既定では、HttpTrigger には HTTP 要求内の API キーが必要です。 そのため、HTTP 要求は、通常は次のようになります。

    https://<yourapp>.azurewebsites.net/api/<function>?code=<ApiKey>

キーは、上記のように `code` という名前のクエリ文字列変数に含めることも、`x-functions-key` HTTP ヘッダーに含めることもできます。 キーの値には、関数のために定義されている任意の関数キーまたは任意のホスト キーを指定できます。

バインド JSON の `authLevel` プロパティを変更することによって、キーを持たない要求を許可するか、マスター キーを使用する必要があることを指定するかを選択することができます (「[HTTP トリガー](#httptrigger)」を参照してください)。

### <a name="keys-and-webhooks"></a>キーと webhook
webhook の承認は、HttpTrigger の一部である webhook レシーバー コンポーネントによって処理されますが、そのメカニズムは webhook の種類によって異なります。 ただし、各メカニズムはキーに依存します。 既定では、"default" という名前の関数キーが使用されます。 別のキーを使用する場合は、次のいずれかの方法で、要求と共にキー名を送信するように webhook プロバイダーを構成する必要があります。

- **クエリ文字列**: プロバイダーが `clientid` クエリ文字列パラメーターでキー名を渡します (たとえば `https://<yourapp>.azurewebsites.net/api/<funcname>?clientid=<keyname>`)。
- **要求ヘッダー**: プロバイダーが `x-functions-clientid` ヘッダーでキー名を渡します。

> [!NOTE]
> 関数キーが、ホスト キーよりも優先されます。 2 つのキーが同じ名前で定義されている場合は、関数キーが使用されます。
> 
> 


<a name="httptriggersample"></a>
## <a name="http-trigger-samples"></a>HTTP トリガーのサンプル
function.json の `bindings` 配列に次の HTTP トリガーがあるとします。

```json
{
    "name": "req",
    "type": "httpTrigger",
    "direction": "in",
    "authLevel": "function"
},
```

クエリ文字列または HTTP 要求の本文で `name` パラメーターを検索する、言語固有のサンプルを参照してください。

* [C#](#httptriggercsharp)
* [F#](#httptriggerfsharp)
* [Node.JS](#httptriggernodejs)


<a name="httptriggercsharp"></a>
### <a name="http-trigger-sample-in-c"></a>C での HTTP トリガーのサンプル# #
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

<a name="httptriggerfsharp"></a>
### <a name="http-trigger-sample-in-f"></a>F での HTTP トリガーのサンプル# #
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

次のように、NuGet を使用して `FSharp.Interop.Dynamic` および `Dynamitey` アセンブリを参照する `project.json` ファイルが必要です。

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

このファイルは NuGet を使用して依存関係を取得し、それをスクリプト内で参照します。

<a name="httptriggernodejs"></a>
### <a name="http-trigger-sample-in-nodejs"></a>Node.js での HTTP トリガーのサンプル
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



<a name="hooktriggersample"></a>
## <a name="webhook-samples"></a>webhook のサンプル
function.json の `bindings` 配列に次の webhook トリガーがあるとします。

```json
{
    "webHookType": "github",
    "name": "req",
    "type": "httpTrigger",
    "direction": "in",
},
```

GitHub の問題のコメントを記録する、言語固有のサンプルを参照してください。

* [C#](#hooktriggercsharp)
* [F#](#hooktriggerfsharp)
* [Node.JS](#hooktriggernodejs)

<a name="hooktriggercsharp"></a>

### <a name="webhook-sample-in-c"></a>C での webhook のサンプル# #
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

<a name="hooktriggerfsharp"></a>

### <a name="webhook-sample-in-f"></a>F での webhook のサンプル# #
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

<a name="hooktriggernodejs"></a>

### <a name="webhook-sample-in-nodejs"></a>Node.JS での webhook のサンプル
```javascript
module.exports = function (context, data) {
    context.log('GitHub WebHook triggered!', data.comment.body);
    context.res = { body: 'New GitHub comment: ' + data.comment.body };
    context.done();
};
```


## <a name="next-steps"></a>次のステップ
[!INCLUDE [next steps](../../includes/functions-bindings-next-steps.md)]


