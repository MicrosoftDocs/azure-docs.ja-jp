---
title: Azure Functions の HTTP トリガー
description: HTTP 経由で Azure 関数を呼び出す方法について説明します。
author: craigshoemaker
ms.topic: reference
ms.date: 02/21/2020
ms.author: cshoe
ms.custom: devx-track-csharp, devx-track-python
ms.openlocfilehash: e8641cada03577cf22e6627bdf53b2a8a0e836ef
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102216385"
---
# <a name="azure-functions-http-trigger"></a>Azure Functions の HTTP トリガー

HTTP トリガーでは、HTTP 要求で関数を呼び出すことができます。 HTTP トリガーを使用して、サーバーなしの API を構築し、webhook に応答することができます。

HTTP によってトリガーされる関数の既定の戻り値は次のとおりです。

- Functions 2.x 以降は、本文が空の `HTTP 204 No Content`
- Functions 1.x は、本文が空の `HTTP 200 OK`

HTTP 応答を変更するには、[出力バインド](./functions-bindings-http-webhook-output.md)を構成します。

HTTP バインドの詳細については、[概要](./functions-bindings-http-webhook.md)と[出力バインドのリファレンス](./functions-bindings-http-webhook-output.md)に関するページを参照してください。

[!INCLUDE [HTTP client best practices](../../includes/functions-http-client-best-practices.md)]

## <a name="example"></a>例

# <a name="c"></a>[C#](#tab/csharp)

次の例は、クエリ文字列または HTTP 要求の本文で `name`パラメーターを探す [C# 関数](functions-dotnet-class-library.md)を示しています。 戻り値は出力バインドの使われますが、戻り値の属性は必要ないことに注意してください。

```cs
[FunctionName("HttpTriggerCSharp")]
public static async Task<IActionResult> Run(
    [HttpTrigger(AuthorizationLevel.Function, "get", "post", Route = null)]
    HttpRequest req, ILogger log)
{
    log.LogInformation("C# HTTP trigger function processed a request.");

    string name = req.Query["name"];
    
    string requestBody = String.Empty;
    using (StreamReader streamReader =  new  StreamReader(req.Body))
    {
        requestBody = await streamReader.ReadToEndAsync();
    }
    dynamic data = JsonConvert.DeserializeObject(requestBody);
    name = name ?? data?.name;
    
    return name != null
        ? (ActionResult)new OkObjectResult($"Hello, {name}")
        : new BadRequestObjectResult("Please pass a name on the query string or in the request body");
}
```

# <a name="c-script"></a>[C# スクリプト](#tab/csharp-script)

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

これらのプロパティについては、「[構成](#configuration)」セクションを参照してください。

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
    
    string requestBody = String.Empty;
    using (StreamReader streamReader =  new  StreamReader(req.Body))
    {
        requestBody = await streamReader.ReadToEndAsync();
    }
    dynamic data = JsonConvert.DeserializeObject(requestBody);
    name = name ?? data?.name;
    
    return name != null
        ? (ActionResult)new OkObjectResult($"Hello, {name}")
        : new BadRequestObjectResult("Please pass a name on the query string or in the request body");
}
```

`HttpRequest` の代わりにカスタム オブジェクトにバインドできます。 このオブジェクトは要求の本文から作成され、JSON として解析されます。 同様に、型は HTTP 応答出力バインドに渡すことができ、`200` のステータス コードと共に、応答本文として返されます。

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

# <a name="java"></a>[Java](#tab/java)

* [クエリ文字列からのパラメーターの読み取り](#read-parameter-from-the-query-string)
* [Post 要求からの本文の読み取り](#read-body-from-a-post-request)
* [ルートからのパラメーターの読み取り](#read-parameter-from-a-route)
* [Post 要求からの POJO 本文の読み取り](#read-pojo-body-from-a-post-request)

次の例は、HTTP トリガー バインドを示しています。

#### <a name="read-parameter-from-the-query-string"></a>クエリ文字列からのパラメーターの読み取り

この例では、クエリ文字列から `id` という名前のパラメーターを読み取り、そのパラメーターを使用して、コンテンツ タイプ `application/json` でクライアントに返される JSON ドキュメントを作成します。

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

#### <a name="read-body-from-a-post-request"></a>POST 要求からの本文の読み取り

この例では、Post 要求の本文を `String` として読み取り、その内容を使用して、コンテンツ タイプ `application/json` でクライアントに返される JSON ドキュメントを作成します。

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

#### <a name="read-parameter-from-a-route"></a>ルートからのパラメーターの読み取り

この例では、ルート パスから `id` という名前の必須パラメーターとオプション パラメーター `name` を読み取り、それらのパラメーターを使用して、コンテンツ タイプ `application/json` でクライアントに返される JSON ドキュメントを作成します。 T

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

#### <a name="read-pojo-body-from-a-post-request"></a>POST 要求からの POJO 本文の読み取り

この例で参照されるクラス `ToDoItem` のコードを次に示します。

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

この例では、POST 要求の本文を読み取ります。 要求本文を `ToDoItem` オブジェクトに自動的に逆シリアル化し、コンテンツ タイプ `application/json` でクライアントに返します。 `ToDoItem` パラメーターは、`HttpMessageResponse.Builder` クラスの `body` プロパティに割り当てられる際に、Functions ランタイムによってシリアル化されます。

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

# <a name="javascript"></a>[JavaScript](#tab/javascript)

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

これらのプロパティについては、「[構成](#configuration)」セクションを参照してください。

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

# <a name="powershell"></a>[PowerShell](#tab/powershell)

次の例は、*function.json* ファイルのトリガー バインドと、[PowerShell 関数](functions-reference-node.md)を示しています。 この関数は、クエリ文字列または HTTP 要求の本文で `name` パラメーターを探します。

```json
{
  "bindings": [
    {
      "authLevel": "function",
      "type": "httpTrigger",
      "direction": "in",
      "name": "Request",
      "methods": [
        "get",
        "post"
      ]
    },
    {
      "type": "http",
      "direction": "out",
      "name": "Response"
    }
  ]
}
```

```powershell
using namespace System.Net

# Input bindings are passed in via param block.
param($Request, $TriggerMetadata)

# Write to the Azure Functions log stream.
Write-Host "PowerShell HTTP trigger function processed a request."

# Interact with query parameters or the body of the request.
$name = $Request.Query.Name
if (-not $name) {
    $name = $Request.Body.Name
}

$body = "This HTTP triggered function executed successfully. Pass a name in the query string or in the request body for a personalized response."

if ($name) {
    $body = "Hello, $name. This HTTP triggered function executed successfully."
}

# Associate values to output bindings by calling 'Push-OutputBinding'.
Push-OutputBinding -Name Response -Value ([HttpResponseContext]@{
    StatusCode = [HttpStatusCode]::OK
    Body       = $body
})
```


# <a name="python"></a>[Python](#tab/python)

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
            "name": "$return"
        }
    ]
}
```

これらのプロパティについては、「[構成](#configuration)」セクションを参照してください。

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

---

## <a name="attributes-and-annotations"></a>属性と注釈

[C# クラス ライブラリ](functions-dotnet-class-library.md)と Java では、`HttpTrigger` 属性を使用して関数を構成できます。

属性のコンストラクターのパラメーター、Webhook の種類、ルートのテンプレートに承認レベルと許容される HTTP メソッドを設定できます。 これらの設定の詳細については、「[構成](#configuration)」のセクションを参照してください。

# <a name="c"></a>[C#](#tab/csharp)

この例では、[HttpTrigger](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/dev/src/WebJobs.Extensions.Http/HttpTriggerAttribute.cs) 属性の使用方法を示します。

```csharp
[FunctionName("HttpTriggerCSharp")]
public static Task<IActionResult> Run(
    [HttpTrigger(AuthorizationLevel.Anonymous)] HttpRequest req)
{
    ...
}
```

完全な例については、[トリガーの例](#example)を参照してください。

# <a name="c-script"></a>[C# スクリプト](#tab/csharp-script)

属性は、C# スクリプトではサポートされていません。

# <a name="java"></a>[Java](#tab/java)

この例では、[HttpTrigger](https://github.com/Azure/azure-functions-java-library/blob/dev/src/main/java/com/microsoft/azure/functions/annotation/HttpTrigger.java) 属性の使用方法を示します。

```java
@FunctionName("HttpTriggerJava")
public HttpResponseMessage<String> HttpTrigger(
        @HttpTrigger(name = "req",
                     methods = {"get"},
                     authLevel = AuthorizationLevel.ANONYMOUS) HttpRequestMessage<String> request,
        final ExecutionContext context) {

    ...
}
```

完全な例については、[トリガーの例](#example)を参照してください。

# <a name="javascript"></a>[JavaScript](#tab/javascript)

属性は、JavaScript ではサポートされていません。

# <a name="powershell"></a>[PowerShell](#tab/powershell)

属性は、PowerShell ではサポートされていません。

# <a name="python"></a>[Python](#tab/python)

属性は、Python ではサポートされていません。

---

## <a name="configuration"></a>構成

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

## <a name="payload"></a>ペイロード

トリガーの入力型は、`HttpRequest` またはカスタム型のいずれかとして宣言されています。 `HttpRequest` を選択した場合は、要求オブジェクトへのフル アクセスが取得されます。 カスタム型 の場合、ランタイムは JSON 要求本文を解析して、オブジェクトのプロパティを設定しようとします。

## <a name="customize-the-http-endpoint"></a>HTTP エンドポイントのカスタマイズ

既定では、HTTP トリガーの関数を作成する際に、次の形式のルートを使用して関数のアドレスを指定できます。

```http
http://<APP_NAME>.azurewebsites.net/api/<FUNCTION_NAME>
```

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
http://<APP_NAME>.azurewebsites.net/api/products/electronics/357
```

この構成により、関数コードではアドレスに _category_ と _id_ の 2 つのパラメーターをサポートできます。URL でルート パラメーターをトークン化する方法の詳細については、「[ASP.NET Core のルーティング](https://docs.microsoft.com/aspnet/core/fundamentals/routing#route-constraint-reference)」を参照してください。

# <a name="c"></a>[C#](#tab/csharp)

パラメーターでは任意の [Web API ルート制約](https://www.asp.net/web-api/overview/web-api-routing-and-actions/attribute-routing-in-web-api-2#constraints)を使用できます。 次の C# 関数コードは両方のパラメーターを使用します。

```csharp
using System.Net;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Extensions.Primitives;

public static IActionResult Run(HttpRequest req, string category, int? id, ILogger log)
{
    var message = String.Format($"Category: {category}, ID: {id}");
    return (ActionResult)new OkObjectResult(message);
}
```

# <a name="c-script"></a>[C# スクリプト](#tab/csharp-script)

パラメーターでは任意の [Web API ルート制約](https://www.asp.net/web-api/overview/web-api-routing-and-actions/attribute-routing-in-web-api-2#constraints)を使用できます。 次の C# 関数コードは両方のパラメーターを使用します。

```csharp
#r "Newtonsoft.Json"

using System.Net;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Extensions.Primitives;

public static IActionResult Run(HttpRequest req, string category, int? id, ILogger log)
{
    var message = String.Format($"Category: {category}, ID: {id}");
    return (ActionResult)new OkObjectResult(message);
}
```

# <a name="java"></a>[Java](#tab/java)

関数の実行コンテキストは、`HttpTrigger` 属性で宣言されたプロパティです。 属性を使用すると、ルート パラメーター、承認レベル、HTTP 動詞、受信要求インスタンスを定義できます。

ルート パラメーターは、`HttpTrigger` 属性を使用して定義されます。

```java
package com.function;

import java.util.*;
import com.microsoft.azure.functions.annotation.*;
import com.microsoft.azure.functions.*;

public class HttpTriggerJava {
    public HttpResponseMessage<String> HttpTrigger(
            @HttpTrigger(name = "req",
                         methods = {"get"},
                         authLevel = AuthorizationLevel.FUNCTION,
                         route = "products/{category:alpha}/{id:int}") HttpRequestMessage<String> request,
            @BindingName("category") String category,
            @BindingName("id") int id,
            final ExecutionContext context) {

        String message = String.format("Category  %s, ID: %d", category, id);
        return request.createResponseBuilder(HttpStatus.OK).body(message).build();
    }
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Node では、Functions ランタイムによって、`context` オブジェクトから要求本文が提供されます。 詳しくは、[JavaScript トリガーの例](#example)をご覧ください。

次の例では、`context.bindingData` からルート パラメーターを読み取る方法を示します。

```javascript
module.exports = function (context, req) {

    var category = context.bindingData.category;
    var id = context.bindingData.id;
    var message = `Category: ${category}, ID: ${id}`;

    context.res = {
        body: message;
    }

    context.done();
}
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

*function.json* ファイルで宣言されたルート パラメーターは、`$Request.Params` オブジェクトのプロパティとしてアクセスできます。

```powershell
$Category = $Request.Params.category
$Id = $Request.Params.id

$Message = "Category:" + $Category + ", ID: " + $Id

Push-OutputBinding -Name Response -Value ([HttpResponseContext]@{
    StatusCode = [HttpStatusCode]::OK
    Body = $Message
})
```

# <a name="python"></a>[Python](#tab/python)

関数の実行コンテキストは、`func.HttpRequest` として宣言されたパラメーターを介して公開されます。 このインスタンスを使用すると、関数で、データ ルート パラメーター、クエリ文字列の値、HTTP 応答を返すことができるメソッドにアクセスできます。

定義したら、`route_params` メソッドを呼び出すことによって、ルート パラメーターを関数に使用できます。

```python
import logging

import azure.functions as func

def main(req: func.HttpRequest) -> func.HttpResponse:

    category = req.route_params.get('category')
    id = req.route_params.get('id')
    message = f"Category: {category}, ID: {id}"

    return func.HttpResponse(message)
```

---

既定では、すべての関数のルートには *api* というプレフィックスが付きます。 [host.json](functions-host-json.md) ファイルで `extensions.http.routePrefix` プロパティを使用すると、このプレフィックスをカスタマイズまたは削除できます。 次の例では、*host.json* ファイル内でプレフィックスに空の文字列を使用することで、*api* ルート プレフィックスを削除します。

```json
{
    "extensions": {
        "http": {
            "routePrefix": ""
        }
    }
}
```

## <a name="using-route-parameters"></a>ルート パラメーターの使用

関数の `route` パターンを定義したルート パラメーターは、各バインドで使用できます。 たとえば、ルートが `"route": "products/{id}"` として定義されている場合、テーブル ストレージ バインドでは、バインド構成の `{id}` パラメーターの値を使用できます。

次の構成は、`{id}` パラメーターをバインドの `rowKey` に渡す方法を示しています。

```json
{
    "type": "table",
    "direction": "in",
    "name": "product",
    "partitionKey": "products",
    "tableName": "products",
    "rowKey": "{id}"
}
```

ルート パラメーターを使用すると、関数に対して `invoke_URL_template` が自動的に作成されます。 クライアントは、URL を使って関数を呼び出すときにその URL に渡す必要があるパラメーターについて、URL テンプレートを使用して理解することができます。 [Azure portal](https://portal.azure.com) で HTTP によってトリガーされる関数のいずれかに移動し、 **[関数の URL の取得]** を選択します。

[List 関数](/rest/api/appservice/webapps/listfunctions)または [Get 関数](/rest/api/appservice/webapps/getfunction)に対して Azure Resource Manager API を使用して、`invoke_URL_template` にプログラムでアクセスすることができます。

## <a name="working-with-client-identities"></a>クライアント ID の操作

関数アプリが [App Service の認証と承認](../app-service/overview-authentication-authorization.md)を使用している場合は、コードから認証されたクライアントに関する情報を確認することができます。 この情報は、[プラットフォームによって挿入された要求ヘッダー](../app-service/app-service-authentication-how-to.md#access-user-claims)として使用できます。

また、この情報はバインディング データから参照することもできます。 この機能は、Functions 2.x 以降の Functions ランタイムのみで使用可能です。 また、現在のところ .NET 言語でのみ使用可能です。

# <a name="c"></a>[C#](#tab/csharp)

認証されたクライアントに関する情報は、[ClaimsPrincipal](/dotnet/api/system.security.claims.claimsprincipal) として使用できます。 ClaimsPrincipal は、次の例に示すように、要求コンテキストの一部として使用可能です。

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

# <a name="c-script"></a>[C# スクリプト](#tab/csharp-script)

認証されたクライアントに関する情報は、[ClaimsPrincipal](/dotnet/api/system.security.claims.claimsprincipal) として使用できます。 ClaimsPrincipal は、次の例に示すように、要求コンテキストの一部として使用可能です。

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

# <a name="java"></a>[Java](#tab/java)

認証されたユーザーは、[HTTP ヘッダー](../app-service/app-service-authentication-how-to.md#access-user-claims)経由で使用できます。

# <a name="javascript"></a>[JavaScript](#tab/javascript)

認証されたユーザーは、[HTTP ヘッダー](../app-service/app-service-authentication-how-to.md#access-user-claims)経由で使用できます。

# <a name="powershell"></a>[PowerShell](#tab/powershell)

認証されたユーザーは、[HTTP ヘッダー](../app-service/app-service-authentication-how-to.md#access-user-claims)経由で使用できます。

# <a name="python"></a>[Python](#tab/python)

認証されたユーザーは、[HTTP ヘッダー](../app-service/app-service-authentication-how-to.md#access-user-claims)経由で使用できます。


---

## <a name="function-access-keys"></a><a name="authorization-keys"></a>関数のアクセス キー

[!INCLUDE [functions-authorization-keys](../../includes/functions-authorization-keys.md)]

## <a name="obtaining-keys"></a>キーを入手する

キーは関数アプリの一部として Azure に格納され、保存中は暗号化されます。 キーを表示したり、新しいものを作成したり、新しい値にキーをロールしたりするには、[Azure portal](https://portal.azure.com) で HTTP によってトリガーされる関数のいずれかに移動して、 **[関数キー]** を選択します。

ホスト キーを管理することもできます。 [Azure portal](https://portal.azure.com) で関数アプリに移動し、[**アプリ キー]** を選択します。

関数およびホスト キーは、Azure Resource Manager API を使用してプログラムで取得できます。 [List 関数キー](/rest/api/appservice/webapps/listfunctionkeys)と [List ホスト キー](/rest/api/appservice/webapps/listhostkeys)への API があります。デプロイ スロットを使用する場合の同等の API は、[List 関数キー スロット](/rest/api/appservice/webapps/listfunctionkeysslot)と [List ホスト キー スロット](/rest/api/appservice/webapps/listhostkeysslot)です。

また、[関数シークレットの作成または更新](/rest/api/appservice/webapps/createorupdatefunctionsecret)、[関数シークレット スロットの作成または更新](/rest/api/appservice/webapps/createorupdatefunctionsecretslot)、[ホスト シークレットの作成または更新](/rest/api/appservice/webapps/createorupdatehostsecret)および[ホスト シークレット スロットの作成または更新](/rest/api/appservice/webapps/createorupdatehostsecretslot) API を使用して、プログラムで新しい関数およびホスト キーを作成することもできます。

関数およびホスト キーは、[関数シークレットの削除](/rest/api/appservice/webapps/deletefunctionsecret)、[関数シークレット スロットの削除](/rest/api/appservice/webapps/deletefunctionsecretslot)、[ホスト シークレットの削除](/rest/api/appservice/webapps/deletehostsecret)、および[ホスト シークレット スロットの削除](/rest/api/appservice/webapps/deletehostsecretslot) API を使用して、プログラムで削除できます。

[従来のキー管理 API を使用して関数キーを取得する](https://github.com/Azure/azure-functions-host/wiki/Key-management-API)こともできますが、代わりに Azure Resource Manager API を使用することをお勧めします。

## <a name="api-key-authorization"></a>API キーの承認

ほとんどの HTTP トリガー テンプレートには、要求内の API キーが必要です。 そのため、HTTP 要求は、通常は次の URL のようになります。

```http
https://<APP_NAME>.azurewebsites.net/api/<FUNCTION_NAME>?code=<API_KEY>
```

上記のように、キーは`code`というクエリ文字列変数に含めることができます。 `x-functions-key`HTTP ヘッダーに含めることもできます。 キーの値には、関数のために定義されている任意の関数キーまたは任意のホスト キーを指定できます。

匿名要求を許可できます。この要求ではキーが不要です。 マスター キーを使用するように要求することもできます。 既定の承認レベルを変更するには、バインド JSON の `authLevel` プロパティを使用します。 詳しくは、「[トリガー - 構成](#configuration)」をご覧ください。

> [!NOTE]
> 機能をローカルで実行する場合、指定された承認レベルの設定に関係なく、許可は無効になります。 Azure に発行した後、トリガーの`authLevel`設定が適用されます。 [コンテナーをローカル](functions-create-function-linux-custom-image.md#build-the-container-image-and-test-locally)で実行する場合もキーが必要です。


## <a name="secure-an-http-endpoint-in-production"></a>運用環境で HTTP エンドポイントを保護します。

運用環境で、関数エンドポイントを完全に保護するには、次の関数アプリレベルのセキュリティ オプションのいずれかの実装を検討してください。 これらの関数アプリレベル セキュリティ メソッドのいずれかを使用する場合は、HTTP トリガー関数の承認レベルを `anonymous` に設定する必要があります。

[!INCLUDE [functions-enable-auth](../../includes/functions-enable-auth.md)]

#### <a name="deploy-your-function-app-in-isolation"></a>関数アプリを分離してデプロイする

[!INCLUDE [functions-deploy-isolation](../../includes/functions-deploy-isolation.md)]

## <a name="webhooks"></a>Webhooks

> [!NOTE]
> Webhook モードは、関数ランタイムのバージョン 1.x でのみ使用できます。 この変更は、バージョン 2.x 以降での HTTP トリガーのパフォーマンスを向上させるために行われました。

バージョン 1.x では、Webhook テンプレートで Webhook ペイロードの追加検証が提供されます。 バージョン 2.x 以降では、基本 HTTP トリガーは引き続き機能し、Webhook の推奨アプローチです。 

### <a name="github-webhooks"></a>GitHub webhook

GitHub webhook に応答するには、まず、HTTP トリガーで関数を作成し、**webHookType** プロパティを `github` に設定します。 次に、その URL と API キーを GitHub リポジトリの **[webhook の追加]** ページにコピーします。 

![関数の Webhook の追加方法を示すスクリーンショット。](./media/functions-bindings-http-webhook/github-add-webhook.png)

### <a name="slack-webhooks"></a>Slack webhook

Slack webhook では、指定しなくてもトークンが自動的に生成されます。そのため、Slack によって生成されたトークンで、関数固有のキーを構成する必要があります。 「[承認キー](#authorization-keys)」をご覧ください。

## <a name="webhooks-and-keys"></a>Webhook とキー

webhook の承認は、HTTP トリガーの一部である webhook レシーバー コンポーネントによって処理されますが、そのメカニズムは webhook の種類によって異なります。 ただし、各メカニズムはキーに依存します。 既定では、"default" という名前の関数キーが使用されます。 別のキーを使用するには、次のいずれかの方法で、要求と共にキー名を送信するように webhook プロバイダーを構成します。

* **クエリ文字列**:プロバイダーにより、`clientid` クエリ文字列パラメーターでキー名 (`https://<APP_NAME>.azurewebsites.net/api/<FUNCTION_NAME>?clientid=<KEY_NAME>` など) が渡されます。
* **要求ヘッダー**:プロバイダーにより、`x-functions-clientid` ヘッダーでキー名が渡されます。

## <a name="content-types"></a>コンテンツの種類

バイナリ データとフォームデータを C# 以外の関数に渡すには、適切な Content-Type ヘッダーを使用する必要があります。 サポートされるコンテンツの種類としては、バイナリ データ用の `octet-stream` と、[マルチパート型](https://www.iana.org/assignments/media-types/media-types.xhtml#multipart)が含まれます。

### <a name="known-issues"></a>既知の問題

C# 以外の関数では、Content-Type `image/jpeg` を使用して要求を送信すると、`string` 値が関数に渡されます。 このような場合は、`string` 値を手動でバイト配列に変換することで、生のバイナリ データにアクセスすることができます。

## <a name="limits"></a>制限

HTTP 要求の長さは 100 MB (104,857,600 バイト) に、URL の長さは 4 KB (4,096 バイト) バイトに制限されています。 これらの制限は、ランタイムの [Web.config ファイル](https://github.com/Azure/azure-functions-host/blob/v3.x/src/WebJobs.Script.WebHost/web.config)の `httpRuntime` 要素で指定されています。

HTTP トリガーを使用する関数が 230 秒以内に完了しない場合、[Azure Load Balancer](../app-service/faq-availability-performance-application-issues.md#why-does-my-request-time-out-after-230-seconds) でタイムアウトが発生し、HTTP 502 エラーが返されます。 この関数は実行を継続しますが、HTTP 応答を返すことはできません。 実行時間が長い関数の場合は、非同期パターンに従い、要求の状態について ping で確認できる場所を返すことをお勧めします。 関数を実行できる時間については、[スケールとホスティングに関するページの「従量課金プラン」](functions-scale.md#timeout)を参照してください。


## <a name="next-steps"></a>次のステップ

- [関数から HTTP 応答を返す](./functions-bindings-http-webhook-output.md)
