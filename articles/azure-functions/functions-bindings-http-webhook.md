---
title: Azure Functions のトリガーとバインド
description: Azure Functions で HTTP トリガーとバインドを使用する方法を説明します。
services: functions
documentationcenter: na
author: craigshoemaker
manager: gwallace
keywords: Azure Functions, 関数, イベント処理, webhook, 動的コンピューティング, サーバーレス アーキテクチャ, HTTP, API, REST
ms.service: azure-functions
ms.topic: reference
ms.date: 11/21/2017
ms.author: cshoe
ms.openlocfilehash: 983cf250f3a7188741c41386aac256bfdb28749b
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/28/2019
ms.locfileid: "70097344"
---
# <a name="azure-functions-http-triggers-and-bindings"></a>Azure Functions のトリガーとバインド

この記事では、Azure Functions で HTTP トリガーと出力バインドを操作する方法について説明します。

HTTP トリガーは [webhook](https://en.wikipedia.org/wiki/Webhook) に応答するようにカスタマイズすることができます。

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

[!INCLUDE [HTTP client best practices](../../includes/functions-http-client-best-practices.md)]

この記事のコードは .NET Core を使用する Functions 2.x 構文が既定です。 1\.x 構文については、[1.x 関数テンプレート](https://github.com/Azure/azure-functions-templates/tree/v1.x/Functions.Templates/Templates)を参照してください。

## <a name="packages---functions-1x"></a>パッケージ - Functions 1.x

HTTP バインディングは [Microsoft.Azure.WebJobs.Extensions.Http](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.Http) NuGet パッケージ バージョン 1.x で提供されます。 パッケージのソース コードは、[azure-webjobs-sdk-extensions](https://github.com/Azure/azure-webjobs-sdk-extensions/tree/v2.x/src/WebJobs.Extensions.Http) GitHub リポジトリにあります。

[!INCLUDE [functions-package-auto](../../includes/functions-package-auto.md)]

## <a name="packages---functions-2x"></a>パッケージ - Functions 2.x

HTTP バインディングは [Microsoft.Azure.WebJobs.Extensions.Http](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.Http) NuGet パッケージ バージョン 3.x で提供されます。 パッケージのソース コードは、[azure-webjobs-sdk-extensions](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.Http/) GitHub リポジトリにあります。

[!INCLUDE [functions-package](../../includes/functions-package-auto.md)]

## <a name="trigger"></a>トリガー

HTTP トリガーでは、HTTP 要求で関数を呼び出すことができます。 HTTP トリガーを使用して、サーバーなしの API を構築し、webhook に応答することができます。

既定では、HTTP トリガーは、Functions 1.x では HTTP 200 OK と空の本文を返し、Functions 2.x では HTTP 204 No Content と空の本文を返します。 応答を変更するには、[HTTP 出力バインド](#output)を構成します。

## <a name="trigger---example"></a>トリガー - 例

言語固有の例をご覧ください。

* [C#](#trigger---c-example)
* [C# スクリプト (.csx)](#trigger---c-script-example)
* [F#](#trigger---f-example)
* [Java](#trigger---java-examples)
* [JavaScript](#trigger---javascript-example)
* [Python](#trigger---python-example)

### <a name="trigger---c-example"></a>トリガー - C# の例

次の例は、クエリ文字列または HTTP 要求の本文で `name`パラメーターを探す [C# 関数](functions-dotnet-class-library.md)を示しています。 戻り値は出力バインドの使われますが、戻り値の属性は必要ないことに注意してください。

```cs
[FunctionName("HttpTriggerCSharp")]
public static async Task<IActionResult> Run(
    [HttpTrigger(AuthorizationLevel.Function, "get", "post", Route = null)] 
    HttpRequest req, ILogger log)
{
    log.LogInformation("C# HTTP trigger function processed a request.");

    string name = req.Query["name"];

    string requestBody = await new StreamReader(req.Body).ReadToEndAsync();
    dynamic data = JsonConvert.DeserializeObject(requestBody);
    name = name ?? data?.name;

    return name != null
        ? (ActionResult)new OkObjectResult($"Hello, {name}")
        : new BadRequestObjectResult("Please pass a name on the query string or in the request body");
}
```

### <a name="trigger---c-script-example"></a>トリガー - C# スクリプトの例

次の例は、*function.json* ファイルのトリガー バインドと、そのバインドが使用される [C# スクリプト関数](functions-reference-csharp.md)を示しています。 この関数は、クエリ文字列または HTTP 要求の本文で `name` パラメーターを探します。

*function.json* ファイルを次に示します。

```json
{
    "disabled": false,
    "bindings": [
        {
            "authLevel": "function",
            "name": "req",
            "type": "httpTrigger",
            "direction": "in",
            "methods": [
                "get",
                "post"
            ]
        },
        {
            "name": "$return",
            "type": "http",
            "direction": "out"
        }
    ]
}
```

これらのプロパティについては、「[構成](#trigger---configuration)」セクションを参照してください。

`HttpRequest` にバインドする C# スクリプト コードを次に示します。

```cs
#r "Newtonsoft.Json"

using System.Net;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Extensions.Primitives;
using Newtonsoft.Json;

public static async Task<IActionResult> Run(HttpRequest req, ILogger log)
{
    log.LogInformation("C# HTTP trigger function processed a request.");

    string name = req.Query["name"];

    string requestBody = await new StreamReader(req.Body).ReadToEndAsync();
    dynamic data = JsonConvert.DeserializeObject(requestBody);
    name = name ?? data?.name;

    return name != null
        ? (ActionResult)new OkObjectResult($"Hello, {name}")
        : new BadRequestObjectResult("Please pass a name on the query string or in the request body");
}
```

`HttpRequest` の代わりにカスタム オブジェクトにバインドできます。 このオブジェクトは要求の本文から作成され、JSON として解析されます。 同様に、型は HTTP 応答出力バインディングに渡すことができ、200 のステータス コードと共に、応答本文として返されます。

```csharp
using System.Net;
using System.Threading.Tasks;
using Microsoft.Extensions.Logging;

public static string Run(Person person, ILogger log)
{   
    return person.Name != null
        ? (ActionResult)new OkObjectResult($"Hello, {person.Name}")
        : new BadRequestObjectResult("Please pass an instance of Person.");
}

public class Person {
     public string Name {get; set;}
}
```

### <a name="trigger---f-example"></a>トリガー - F# の例

次の例は、*function.json* ファイルのトリガー バインドと、そのバインドが使用される [F# 関数](functions-reference-fsharp.md)を示しています。 この関数は、クエリ文字列または HTTP 要求の本文で `name` パラメーターを探します。

*function.json* ファイルを次に示します。

```json
{
  "bindings": [
    {
      "authLevel": "function",
      "name": "req",
      "type": "httpTrigger",
      "direction": "in"
    },
    {
      "name": "res",
      "type": "http",
      "direction": "out"
    }
  ],
  "disabled": false
}
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

*function.json* ファイルを次に示します。

```json
{
    "disabled": false,    
    "bindings": [
        {
            "authLevel": "function",
            "type": "httpTrigger",
            "direction": "in",
            "name": "req"
        },
        {
            "type": "http",
            "direction": "out",
            "name": "res"
        }
    ]
}
```

これらのプロパティについては、「[構成](#trigger---configuration)」セクションを参照してください。

JavaScript コードを次に示します。

```javascript
module.exports = function(context, req) {
    context.log('Node.js HTTP trigger function processed a request. RequestUri=%s', req.originalUrl);

    if (req.query.name || (req.body && req.body.name)) {
        context.res = {
            // status defaults to 200 */
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

### <a name="trigger---python-example"></a>トリガー - Python の例

次の例は、*function.json* ファイルのトリガー バインドと、そのバインドが使用される [Python 関数](functions-reference-python.md)を示しています。 この関数は、クエリ文字列または HTTP 要求の本文で `name` パラメーターを探します。

*function.json* ファイルを次に示します。

```json
{
    "scriptFile": "__init__.py",
    "disabled": false,    
    "bindings": [
        {
            "authLevel": "function",
            "type": "httpTrigger",
            "direction": "in",
            "name": "req"
        },
        {
            "type": "http",
            "direction": "out",
            "name": "res"
        }
    ]
}
```

これらのプロパティについては、「[構成](#trigger---configuration)」セクションを参照してください。

Python コードを次に示します。

```python
import logging
import azure.functions as func


def main(req: func.HttpRequest) -> func.HttpResponse:
    logging.info('Python HTTP trigger function processed a request.')

    name = req.params.get('name')
    if not name:
        try:
            req_body = req.get_json()
        except ValueError:
            pass
        else:
            name = req_body.get('name')

    if name:
        return func.HttpResponse(f"Hello {name}!")
    else:
        return func.HttpResponse(
            "Please pass a name on the query string or in the request body",
            status_code=400
        )
```

### <a name="trigger---java-examples"></a>トリガー - Java の例

* [クエリ文字列からのパラメーターの読み取り](#read-parameter-from-the-query-string-java)
* [Post 要求からの本文の読み取り](#read-body-from-a-post-request-java)
* [ルートからのパラメーターの読み取り](#read-parameter-from-a-route-java)
* [Post 要求からの POJO 本文の読み取り](#read-pojo-body-from-a-post-request-java)

次の例は、*function.json* ファイルの HTTP トリガー バインドと、そのバインドを使用する個別の [Java 関数](functions-reference-java.md)を示しています。 

*function.json* ファイルを次に示します。

```json
{
    "disabled": false,    
    "bindings": [
        {
            "authLevel": "anonymous",
            "type": "httpTrigger",
            "direction": "in",
            "name": "req"
        },
        {
            "type": "http",
            "direction": "out",
            "name": "res"
        }
    ]
}
```

#### <a name="read-parameter-from-the-query-string-java"></a>クエリ文字列からのパラメーターの読み取り (Java)  

この例では、クエリ文字列から ```id``` という名前のパラメーターを読み取り、そのパラメーターを使用して、コンテンツ タイプ ```application/json``` でクライアントに返される JSON ドキュメントを作成します。 

```java
    @FunctionName("TriggerStringGet")
    public HttpResponseMessage run(
            @HttpTrigger(name = "req", 
              methods = {HttpMethod.GET}, 
              authLevel = AuthorizationLevel.ANONYMOUS)
            HttpRequestMessage<Optional<String>> request,
            final ExecutionContext context) {
        
        // Item list
        context.getLogger().info("GET parameters are: " + request.getQueryParameters());

        // Get named parameter
        String id = request.getQueryParameters().getOrDefault("id", "");

        // Convert and display
        if (id.isEmpty()) {
            return request.createResponseBuilder(HttpStatus.BAD_REQUEST)
                          .body("Document not found.")
                          .build();
        } 
        else {
            // return JSON from to the client
            // Generate document
            final String name = "fake_name";
            final String jsonDocument = "{\"id\":\"" + id + "\", " + 
                                         "\"description\": \"" + name + "\"}";
            return request.createResponseBuilder(HttpStatus.OK)
                          .header("Content-Type", "application/json")
                          .body(jsonDocument)
                          .build();
        }
    }
```

#### <a name="read-body-from-a-post-request-java"></a>Post 要求からの本文の読み取り (Java)  

この例では、Post 要求の本文を ```String``` として読み取り、その内容を使用して、コンテンツ タイプ ```application/json``` でクライアントに返される JSON ドキュメントを作成します。

```java
    @FunctionName("TriggerStringPost")
    public HttpResponseMessage run(
            @HttpTrigger(name = "req", 
              methods = {HttpMethod.POST}, 
              authLevel = AuthorizationLevel.ANONYMOUS)
            HttpRequestMessage<Optional<String>> request,
            final ExecutionContext context) {
        
        // Item list
        context.getLogger().info("Request body is: " + request.getBody().orElse(""));

        // Check request body
        if (!request.getBody().isPresent()) {
            return request.createResponseBuilder(HttpStatus.BAD_REQUEST)
                          .body("Document not found.")
                          .build();
        } 
        else {
            // return JSON from to the client
            // Generate document
            final String body = request.getBody().get();
            final String jsonDocument = "{\"id\":\"123456\", " + 
                                         "\"description\": \"" + body + "\"}";
            return request.createResponseBuilder(HttpStatus.OK)
                          .header("Content-Type", "application/json")
                          .body(jsonDocument)
                          .build();
        }
    }
```

#### <a name="read-parameter-from-a-route-java"></a>ルートからのパラメーターの読み取り (Java)  

この例では、ルート パスから ```id``` という名前の必須パラメーターとオプション パラメーター ```name``` を読み取り、それらのパラメーターを使用して、コンテンツ タイプ ```application/json``` でクライアントに返される JSON ドキュメントを作成します。 T

```java
    @FunctionName("TriggerStringRoute")
    public HttpResponseMessage run(
            @HttpTrigger(name = "req", 
              methods = {HttpMethod.GET}, 
              authLevel = AuthorizationLevel.ANONYMOUS,
              route = "trigger/{id}/{name=EMPTY}") // name is optional and defaults to EMPTY
            HttpRequestMessage<Optional<String>> request,
            @BindingName("id") String id,
            @BindingName("name") String name,
            final ExecutionContext context) {
        
        // Item list
        context.getLogger().info("Route parameters are: " + id);

        // Convert and display
        if (id == null) {
            return request.createResponseBuilder(HttpStatus.BAD_REQUEST)
                          .body("Document not found.")
                          .build();
        } 
        else {
            // return JSON from to the client
            // Generate document
            final String jsonDocument = "{\"id\":\"" + id + "\", " + 
                                         "\"description\": \"" + name + "\"}";
            return request.createResponseBuilder(HttpStatus.OK)
                          .header("Content-Type", "application/json")
                          .body(jsonDocument)
                          .build();
        }
    }
```

#### <a name="read-pojo-body-from-a-post-request-java"></a>Post 要求からの POJO 本文の読み取り (Java)  

この例で参照されるクラス ```ToDoItem``` のコードを次に示します。

```java

public class ToDoItem {

  private String id;
  private String description;  

  public ToDoItem(String id, String description) {
    this.id = id;
    this.description = description;
  }

  public String getId() {
    return id;
  }

  public String getDescription() {
    return description;
  }
  
  @Override
  public String toString() {
    return "ToDoItem={id=" + id + ",description=" + description + "}";
  }
}

```

この例では、POST 要求の本文を読み取ります。 要求本文を ```ToDoItem``` オブジェクトに自動的に逆シリアル化し、コンテンツ タイプ ```application/json``` でクライアントに返します。 ```ToDoItem``` パラメーターは、```HttpMessageResponse.Builder``` クラスの ```body``` プロパティに割り当てられる際に、Functions ランタイムによってシリアル化されます。

```java
    @FunctionName("TriggerPojoPost")
    public HttpResponseMessage run(
            @HttpTrigger(name = "req", 
              methods = {HttpMethod.POST}, 
              authLevel = AuthorizationLevel.ANONYMOUS)
            HttpRequestMessage<Optional<ToDoItem>> request,
            final ExecutionContext context) {
        
        // Item list
        context.getLogger().info("Request body is: " + request.getBody().orElse(null));

        // Check request body
        if (!request.getBody().isPresent()) {
            return request.createResponseBuilder(HttpStatus.BAD_REQUEST)
                          .body("Document not found.")
                          .build();
        } 
        else {
            // return JSON from to the client
            // Generate document
            final ToDoItem body = request.getBody().get();
            return request.createResponseBuilder(HttpStatus.OK)
                          .header("Content-Type", "application/json")
                          .body(body)
                          .build();
        }
    }
```

## <a name="trigger---attributes"></a>トリガー - 属性

[C# クラス ライブラリ](functions-dotnet-class-library.md)では、[HttpTrigger](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/dev/src/WebJobs.Extensions.Http/HttpTriggerAttribute.cs) 属性を使用します。

属性のコンストラクターのパラメーターに承認レベルと許容される HTTP メソッドを設定できます。また、webhook の種類とルートのテンプレートに対応するプロパティがあります。 これらの設定の詳細については、「[トリガー - 構成](#trigger---configuration)」を参照してください。 メソッド シグネチャでの `HttpTrigger` 属性を次に示します。

```csharp
[FunctionName("HttpTriggerCSharp")]
public static Task<IActionResult> Run(
    [HttpTrigger(AuthorizationLevel.Anonymous)] HttpRequest req)
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
| **methods** |**メソッド** | 関数が応答する HTTP メソッドの配列。 指定しない場合、関数はすべての HTTP メソッドに応答します。 「[HTTP エンドポイントのカスタマイズ](#customize-the-http-endpoint)」をご覧ください。 |
| **route** | **Route** | 関数がどの要求 URL に応答するかを制御するルート テンプレートを定義します。 何も指定しなかった場合の既定値は `<functionname>` です。 詳しくは、「[HTTP エンドポイントのカスタマイズ](#customize-the-http-endpoint)」をご覧ください。 |
| **webHookType** | **WebHookType** | _バージョン 1.x ランタイムでのみサポートされます。_<br/><br/>指定したプロバイダーの [webhook](https://en.wikipedia.org/wiki/Webhook) レシーバーとして機能する HTTP トリガーを構成します。 このプロパティを設定する場合は、`methods` プロパティを設定しないでください。 webhook の種類は、次のいずれかの値になります。<ul><li><code>genericJson</code>&mdash;特定のプロバイダー用のロジックを持たない汎用 webhook エンドポイントです。 この設定では、要求が、HTTP POST を使用していてコンテンツの種類が `application/json` であるものだけに制限されます。</li><li><code>github</code>&mdash;関数は [GitHub webhook](https://developer.github.com/webhooks/) に応答します。 GitHub webhook に対して _authLevel_ プロパティを使用しないでください。 詳しくは、この記事で後述する「GitHub webhook」セクションをご覧ください。</li><li><code>slack</code>&mdash;関数は [Slack webhook](https://api.slack.com/outgoing-webhooks) に応答します。 Slack webhook に対して _authLevel_ プロパティを使用しないでください。 詳しくは、この記事で後述する「Slack webhook」セクションをご覧ください。</li></ul>|

## <a name="trigger---usage"></a>トリガー - 使用方法

C# および F# の関数では、トリガー入力の型を `HttpRequest` 型かカスタム型として宣言できます。 `HttpRequest` を選択した場合は、要求オブジェクトへのフル アクセスが取得されます。 カスタム型 の場合、ランタイムは JSON 要求本文を解析して、オブジェクトのプロパティを設定しようとします。

JavaScript 関数の場合、Functions ランタイムは request オブジェクトではなく、要求本文を提供します。 詳しくは、[JavaScript トリガーの例](#trigger---javascript-example)をご覧ください。

### <a name="customize-the-http-endpoint"></a>HTTP エンドポイントのカスタマイズ

既定では、HTTP トリガーの関数を作成する際に、次の形式のルートを使用して関数のアドレスを指定できます。

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
public static Task<IActionResult> Run(HttpRequest req, string category, int? id, ILogger log)
{
    if (id == null)
    {
        return (ActionResult)new OkObjectResult($"All {category} items were requested.");
    }
    else
    {
        return (ActionResult)new OkObjectResult($"{category} item with id = {id} has been requested.");
    }
    
    // -----
    log.LogInformation($"C# HTTP trigger function processed a request. RequestUri={req.RequestUri}");
}
```

同じルート パラメーターを使用する Node.js 関数コードを次に示します。

```javascript
module.exports = function (context, req) {

    var category = context.bindingData.category;
    var id = context.bindingData.id;

    if (!id) {
        context.res = {
            // status defaults to 200 */
            body: "All " + category + " items were requested."
        };
    }
    else {
        context.res = {
            // status defaults to 200 */
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

### <a name="working-with-client-identities"></a>クライアント ID の操作

関数アプリが [App Service の認証と承認](../app-service/overview-authentication-authorization.md)を使用している場合は、コードから認証されたクライアントに関する情報を確認することができます。 この情報は、[プラットフォームによって挿入された要求ヘッダー](../app-service/app-service-authentication-how-to.md#access-user-claims)として使用できます。 

また、この情報はバインディング データから参照することもできます。 この機能は、Functions 2.x ランタイムのみで使用可能です。 また、現在のところ .NET 言語でのみ使用可能です。

.NET 言語では、この情報は [ClaimsPrincipal](https://docs.microsoft.com/dotnet/api/system.security.claims.claimsprincipal) として使用可能です。 ClaimsPrincipal は、次の例に示すように、要求コンテキストの一部として使用可能です。

```csharp
using System.Net;
using Microsoft.AspNetCore.Mvc;
using System.Security.Claims;

public static IActionResult Run(HttpRequest req, ILogger log)
{
    ClaimsPrincipal identities = req.HttpContext.User;
    // ...
    return new OkObjectResult();
}
```

また、ClaimsPrincipal を単に追加のパラメーターとして関数シグネチャに含めることもできます。

```csharp
#r "Newtonsoft.Json"

using System.Net;
using Microsoft.AspNetCore.Mvc;
using System.Security.Claims;
using Newtonsoft.Json.Linq;

public static void Run(JObject input, ClaimsPrincipal principal, ILogger log)
{
    // ...
    return;
}

```

### <a name="authorization-keys"></a>承認キー

関数を使用すると、開発中に HTTP 関数のエンドポイントにアクセスするのをより困難にするようにキーを使用できます。  標準の HTTP トリガーでは、そのような API キーが要求内に存在する必要があります。 

> [!IMPORTANT]
> キーは開発中に HTTP エンドポイントを難読化するのに役立つかもしれませんが、運用環境で HTTP トリガーを確保する方法としては意図されていません。 詳細については、[運用環境で HTTP エンドポイントを保護する](#secure-an-http-endpoint-in-production)を参照してください。

> [!NOTE]
> Functions 1.x ランタイムでは、Webhook プロバイダーは、プロバイダーがサポートするものに応じて、さまざまな方法で要求を認可するためにキーを使用することがあります。 これについては、[Webhook とキー](#webhooks-and-keys)を参照してください。 バージョン 2.x ランタイムには、Webhook プロバイダーの組み込みサポートは含まれていません。

キーには、次の 2 つの種類があります。

* **ホスト キー**:これらのキーは、関数アプリ内のすべての関数で共有されます。 API キーとして使用した場合は、関数アプリ内のすべての関数がアクセスできます。
* **関数キー**:これらのキーは、それらが定義されている特定の関数にのみ適用されます。 API キーとして使用した場合は、その関数だけがアクセスできます。

各キーには、参照用に名前が付けられており、関数レベルおよびホスト レベルで "default" という名前の既定のキーがあります。 関数キーが、ホスト キーよりも優先されます。 2 つのキーが同じ名前で定義されている場合は、関数キーが使用されます。

各関数アプリには特別な**マスター キー**もあります。 このキーは`_master`という名前のホスト キーで、ランタイム API への管理アクセスを提供します。 このキーを取り消すことはできません。 認可レベルを`admin`に設定すると、要求でマスター キーを使用する必要があります。 その他のキーを使用すると認可エラーになります。

> [!CAUTION]  
> マスター キーによって付与された関数 app の権限が昇格しているため、このキーを第三者と共有したり、ネイティブ クライアント アプリケーションに配布したりしないでください。 管理者承認レベルを選択する場合は注意が必要です。

### <a name="obtaining-keys"></a>キーを入手する

キーは関数アプリの一部として Azure に格納され、保存中は暗号化されます。 キーを表示には、新しい値を作成したり、新しい値にキーをロールしたり、[Azure ポータル](https://portal.azure.com)で HTTP トリガー機能に移動して、**管理**を選択します。

![ポータルでのファンクション キーを管理します。](./media/functions-bindings-http-webhook/manage-function-keys.png)

[Key Management API](https://github.com/Azure/azure-functions-host/wiki/Key-management-API) を使用して、関数キーをプログラムで取得することができます。

### <a name="api-key-authorization"></a>API キーの承認

ほとんどの HTTP トリガー テンプレートには、要求内の API キーが必要です。 そのため、HTTP 要求は、通常は次の URL のようになります。

    https://<APP_NAME>.azurewebsites.net/api/<FUNCTION_NAME>?code=<API_KEY>

上記のように、キーは`code`というクエリ文字列変数に含めることができます。 `x-functions-key`HTTP ヘッダーに含めることもできます。 キーの値には、関数のために定義されている任意の関数キーまたは任意のホスト キーを指定できます。

匿名要求を許可できます。この要求ではキーが不要です。 マスター キーを使用するように要求することもできます。 既定の承認レベルを変更するには、バインド JSON の `authLevel` プロパティを使用します。 詳しくは、「[トリガー - 構成](#trigger---configuration)」をご覧ください。

> [!NOTE]
> 機能をローカルで実行する場合、指定された認証レベルの設定に関係なく、許可は無効になります。 Azure に発行した後、トリガーの`authLevel`設定が適用されます。



### <a name="secure-an-http-endpoint-in-production"></a>運用環境で HTTP エンドポイントを保護します。

運用環境で、関数エンドポイントを完全に保護するには、次の関数アプリ レベルのセキュリティ オプションのいずれかの実装を検討してください。

* 機能アプリの App サービス認証/認可をオンにします。 App Service プラットフォームでは、Azure Active Directory (AAD) といくつかのサード パーティの ID プロバイダーを使用してクライアントを認証することができます。 これを使用して、関数のカスタム認可ルールを実装し、関数コードのユーザー情報を操作できます。 詳細については、「[Azure App Service での認証および認可](../app-service/overview-authentication-authorization.md)」および「[クライアント ID の操作](#working-with-client-identities)」を参照してください。

* 要求の認証に Azure API Management (APIM) を使用します。 APIM では、受信要求用のさまざまな API のセキュリティ オプションを提供します。 詳細については、 [API Management の認証ポリシー](../api-management/api-management-authentication-policies.md)を参照してください。 APIM を適切に配置すると、APIM インスタンスの IP アドレスからの要求のみを受け入れるように関数アプリを設定できます。 詳細については、[IP アドレス制限](ip-addresses.md#ip-address-restrictions)を参照してください。

* Azure App Service 環境 (ASE) で関数アプリをデプロイします。 ASE では、関数を実行するための専用のホスティング環境を提供します。 ASE では、すべての着信要求の認証に使用できる 1 つのフロント エンド ゲートウェイを構成できます。 詳細情報については、[App Service 環境の Web アプリケーション ファイアウォール (WAF) を構成する](../app-service/environment/app-service-app-service-environment-web-application-firewall.md)を参照してください。

これらの関数アプリレベル セキュリティ メソッドのいずれかを使用する場合は、HTTP トリガー関数認証レベルを`anonymous`に設定する必要があります。

### <a name="webhooks"></a>webhooks

> [!NOTE]
> Webhook モードは、関数ランタイムのバージョン 1.x でのみ使用できます。 この変更は、バージョン 2.x での HTTP トリガーのパフォーマンスを向上させるために行われました。

バージョン 1.x では、Webhook テンプレートで Webhook ペイロードの追加検証が提供されます。 バージョン 2.x では、基本 HTTP トリガーは引き続き機能し、Webhook の推奨アプローチです。 

#### <a name="github-webhooks"></a>GitHub webhook

GitHub webhook に応答するには、まず、HTTP トリガーで関数を作成し、**webHookType** プロパティを `github` に設定します。 次に、その URL と API キーを GitHub リポジトリの **[webhook の追加]** ページにコピーします。 

![](./media/functions-bindings-http-webhook/github-add-webhook.png)

#### <a name="slack-webhooks"></a>Slack webhook

Slack webhook では、指定しなくてもトークンが自動的に生成されます。そのため、Slack によって生成されたトークンで、関数固有のキーを構成する必要があります。 「[承認キー](#authorization-keys)」をご覧ください。

### <a name="webhooks-and-keys"></a>Webhook とキー

webhook の承認は、HTTP トリガーの一部である webhook レシーバー コンポーネントによって処理されますが、そのメカニズムは webhook の種類によって異なります。 ただし、各メカニズムはキーに依存します。 既定では、"default" という名前の関数キーが使用されます。 別のキーを使用するには、次のいずれかの方法で、要求と共にキー名を送信するように webhook プロバイダーを構成します。

* **クエリ文字列**:プロバイダーにより、`clientid` クエリ文字列パラメーターでキー名 (`https://<APP_NAME>.azurewebsites.net/api/<FUNCTION_NAME>?clientid=<KEY_NAME>` など) が渡されます。
* **要求ヘッダー**:プロバイダーにより、`x-functions-clientid` ヘッダーでキー名が渡されます。

## <a name="trigger---limits"></a>トリガー - 制限

HTTP 要求の長さは 100 MB (104,857,600 バイト) に、URL の長さは 4 KB (4,096 バイト) バイトに制限されています。 これらの制限は、ランタイムの [Web.config ファイル](https://github.com/Azure/azure-webjobs-sdk-script/blob/v1.x/src/WebJobs.Script.WebHost/Web.config)の `httpRuntime` 要素で指定されています。

HTTP トリガーを使用する関数が約 2.5 分以内に完了しない場合、ゲートウェイでタイムアウトが発生し、HTTP 502 エラーが返されます。 この関数は実行を継続しますが、HTTP 応答を返すことはできません。 実行時間が長い関数の場合は、非同期パターンに従い、要求の状態について ping で確認できる場所を返すことをお勧めします。 関数を実行できる時間については、[スケールとホスティングに関するページの「従量課金プラン」](functions-scale.md#timeout)を参照してください。

## <a name="trigger---hostjson-properties"></a>トリガー - host.json のプロパティ

[host.json](functions-host-json.md) ファイルには、HTTP トリガーの動作を制御する設定が含まれています。

[!INCLUDE [functions-host-json-http](../../includes/functions-host-json-http.md)]

## <a name="output"></a>Output

HTTP 要求送信者に応答するには、HTTP 出力バインドを使用します。 このバインドには、HTTP トリガーが必要です。このバインドを使用すると、トリガーの要求に関連付けられている応答をカスタマイズすることができます。 HTTP 出力バインドが提供されていない場合、HTTP トリガーは、Functions 1.x では HTTP 200 OK と空の本文を返し、Functions 2.x では HTTP 204 No Content と空の本文を返します。

## <a name="output---configuration"></a>出力 - 構成

次の表は、*function.json* ファイルで設定したバインド構成のプロパティを説明しています。 C# クラス ライブラリには、*function.json* のこれらのプロパティに対応する属性プロパティはありません。

|プロパティ  |説明  |
|---------|---------|
| **type** |`http` に設定する必要があります。 |
| **direction** | `out` に設定する必要があります。 |
|**name** | 応答の関数コードで使用される変数名、または戻り値を使うことを示す `$return`。 |

## <a name="output---usage"></a>出力 - 使用方法

HTTP 応答を送信するには、言語標準の応答パターンを使います。 C# またはで C# スクリプトでは、関数の戻り値の型を `IActionResult` または `Task<IActionResult>` にします。 C# の場合、戻り値の属性は必要ありません。

応答の例のため、[トリガー例](#trigger---example)を参照してください。

## <a name="next-steps"></a>次の手順

[Azure Functions のトリガーとバインドの詳細情報](functions-triggers-bindings.md)
