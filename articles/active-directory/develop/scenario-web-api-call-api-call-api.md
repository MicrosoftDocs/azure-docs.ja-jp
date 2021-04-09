---
title: Web API を呼び出す Web API | Azure
titleSuffix: Microsoft identity platform
description: Web API を呼び出す Web API を構築する方法について説明します。
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 09/26/2020
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: f4dfd2c7f9dbdd111f70a5dd5a648c11eacbf7b0
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102038545"
---
# <a name="a-web-api-that-calls-web-apis-call-an-api"></a>Web API を呼び出す Web API: API を呼び出す

トークンを取得した後に、保護された Web API を呼び出せます。 通常、Web アプリのコントローラーまたはページからダウンストリーム API を呼び出します。

## <a name="controller-code"></a>コントローラー コード

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

*Microsoft.Identity.Web* を使用する場合、3 つの使用シナリオがあります。

- [オプション 1: SDK を使用して Microsoft Graph を呼び出す](#option-1-call-microsoft-graph-with-the-sdk)
- [オプション 2: ヘルパー クラスを使用してダウンストリーム Web API を呼び出す](#option-2-call-a-downstream-web-api-with-the-helper-class)
- [オプション 3:ヘルパー クラスを使用せずにダウンストリーム Web API を呼び出す](#option-3-call-a-downstream-web-api-without-the-helper-class)

#### <a name="option-1-call-microsoft-graph-with-the-sdk"></a>オプション 1: SDK を使用して Microsoft Graph を呼び出す

このシナリオでは、[コード構成](scenario-web-api-call-api-app-configuration.md#option-1-call-microsoft-graph)に関するページで説明されているように *Startup.cs* に `.AddMicrosoftGraph()` を追加し、コントローラーまたはページ コンストラクターに `GraphServiceClient` を直接挿入してアクションで使用することができます。 次の例の Razor ページには、サインインしたユーザーの写真が表示されます。

```CSharp
 [Authorize]
 [AuthorizeForScopes(Scopes = new[] { "user.read" })]
 public class IndexModel : PageModel
 {
     private readonly GraphServiceClient _graphServiceClient;

     public IndexModel(GraphServiceClient graphServiceClient)
     {
         _graphServiceClient = graphServiceClient;
     }

     public async Task OnGet()
     {
         var user = await _graphServiceClient.Me.Request().GetAsync();
         try
         {
             using (var photoStream = await _graphServiceClient.Me.Photo.Content.Request().GetAsync())
             {
                 byte[] photoByte = ((MemoryStream)photoStream).ToArray();
                 ViewData["photo"] = Convert.ToBase64String(photoByte);
             }
             ViewData["name"] = user.DisplayName;
         }
         catch (Exception)
         {
             ViewData["photo"] = null;
         }
     }
 }
```

#### <a name="option-2-call-a-downstream-web-api-with-the-helper-class"></a>オプション 2:ヘルパー クラスを使用してダウンストリーム Web API を呼び出す

このシナリオでは、[コード構成](scenario-web-api-call-api-app-configuration.md#option-2-call-a-downstream-web-api-other-than-microsoft-graph)に関するページで説明されているように、*Startup.cs* に `.AddDownstreamWebApi()` を追加し、コントローラーまたはページ コンストラクターに `IDownstreamWebApi` サービスを直接挿入してアクションでそれを使用することができます。

```CSharp
 [Authorize]
 [AuthorizeForScopes(ScopeKeySection = "TodoList:Scopes")]
 public class TodoListController : Controller
 {
     private IDownstreamWebApi _downstreamWebApi;
     private const string ServiceName = "TodoList";

     public TodoListController(IDownstreamWebApi downstreamWebApi)
     {
         _downstreamWebApi = downstreamWebApi;
     }

     public async Task<ActionResult> Details(int id)
     {
         var value = await _downstreamWebApi.CallWebApiForUserAsync(
             ServiceName,
             options =>
             {
                 options.RelativePath = $"me";
             });
         return View(value);
     }
```

また、`CallWebApiForUserAsync` メソッドには、オブジェクトを直接受け取ることができる、厳密に型指定されたジェネリック オーバーライドもあります。 たとえば、次のメソッドは `Todo` インスタンスを受け取ります。これは、Web API から返された JSON の厳密に型指定された表現です。

```CSharp
 // GET: TodoList/Details/5
 public async Task<ActionResult> Details(int id)
 {
     var value = await _downstreamWebApi.CallWebApiForUserAsync<object, Todo>(
         ServiceName,
         null,
         options =>
         {
             options.HttpMethod = HttpMethod.Get;
             options.RelativePath = $"api/todolist/{id}";
         });
     return View(value);
 }
```

#### <a name="option-3-call-a-downstream-web-api-without-the-helper-class"></a>オプション 3:ヘルパー クラスを使用せずにダウンストリーム Web API を呼び出す

`ITokenAcquisition` サービスを使用して手動でトークンを取得することにした場合、トークンを使用する必要があります。 この場合、次のコードは、「[Web API を呼び出す Web API:アプリのトークンを取得する](scenario-web-api-call-api-acquire-token.md)」で示されているコード例の続きです。 このコードは、API コントローラーのアクションで呼び出されます。 *todolist* という名前のダウンストリーム API を呼び出します。

 トークンを取得した後、ダウンストリーム API を呼び出すベアラー トークンとしてそれを使用します。

```csharp
 private async Task CallTodoListService(string accessToken)
 {
  // After the token has been returned by Microsoft.Identity.Web, add it to the HTTP authorization header before making the call to access the todolist service.
 _httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", result.AccessToken);

 // Call the todolist service.
 HttpResponseMessage response = await _httpClient.GetAsync(TodoListBaseAddress + "/api/todolist");
 // ...
 }
 ```

# <a name="java"></a>[Java](#tab/java)

次のコードは、「[Web API を呼び出す Web API: アプリのトークンを取得する](scenario-web-api-call-api-acquire-token.md)」で示されているコード例の続きです。 このコードは、API コントローラーのアクションで呼び出されます。 ダウンストリーム API MS Graph を呼び出します。

トークンを取得した後、ダウンストリーム API を呼び出すベアラー トークンとしてそれを使用します。

```Java
private String callMicrosoftGraphMeEndpoint(String accessToken){
    RestTemplate restTemplate = new RestTemplate();

    HttpHeaders headers = new HttpHeaders();
    headers.setContentType(MediaType.APPLICATION_JSON);

    headers.set("Authorization", "Bearer " + accessToken);

    HttpEntity<String> entity = new HttpEntity<>(null, headers);

    String result = restTemplate.exchange("https://graph.microsoft.com/v1.0/me", HttpMethod.GET,
            entity, String.class).getBody();

    return result;
}
```

# <a name="python"></a>[Python](#tab/python)
このフローを MSAL Python でデモンストレーションするサンプルは、[ms-identity-python-on-behalf-of](https://github.com/Azure-Samples/ms-identity-python-on-behalf-of) で入手できます。

---

## <a name="next-steps"></a>次のステップ

このシナリオの次の記事「[運用環境に移行する](scenario-web-api-call-api-production.md)」に進みます。
