---
title: Web アプリから Web API を呼び出す | Azure
titleSuffix: Microsoft identity platform
description: Web API を呼び出す Web アプリの構築方法について説明します (保護された Web API の呼び出し)
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 09/25/2020
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: b294a56a523adaa2629a5d1e72a7ccef532956e0
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "98753283"
---
# <a name="a-web-app-that-calls-web-apis-call-a-web-api"></a>Web API を呼び出す Web アプリ: Web API を呼び出す

トークンを取得すると、保護された Web API を呼び出せます。 通常、Web アプリのコントローラーまたはページからダウンストリーム API を呼び出します。

## <a name="call-a-protected-web-api"></a>保護された Web API を呼び出す

保護された Web API の呼び出しは、選択した言語とフレームワークによって異なります。

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

*Microsoft.Identity.Web* を使用する場合、API を呼び出すための使用オプションが 3 つあります。

- [オプション 1: Microsoft Graph SDK を使用して Microsoft Graph を呼び出す](#option-1-call-microsoft-graph-with-the-sdk)
- [オプション 2: ヘルパー クラスを使用してダウンストリーム Web API を呼び出す](#option-2-call-a-downstream-web-api-with-the-helper-class)
- [オプション 3:ヘルパー クラスを使用せずにダウンストリーム Web API を呼び出す](#option-3-call-a-downstream-web-api-without-the-helper-class)

#### <a name="option-1-call-microsoft-graph-with-the-sdk"></a>オプション 1: SDK を使用して Microsoft Graph を呼び出す

Microsoft Graph を呼び出す必要があります。 このシナリオでは、[コード構成](scenario-web-app-call-api-app-configuration.md#option-1-call-microsoft-graph)に関するページで説明されているように *Startup.cs* に `AddMicrosoftGraph` を追加し、コントローラーまたはページ コンストラクターに `GraphServiceClient` を直接挿入してアクションで使用することができます。 次の例の Razor ページには、サインインしたユーザーの写真が表示されます。

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

Microsoft Graph 以外の Web API を呼び出す必要があります。 この場合、[コード構成](scenario-web-app-call-api-app-configuration.md#option-2-call-a-downstream-web-api-other-than-microsoft-graph)に関するページで説明されているように、*Startup.cs* に `AddDownstreamWebApi` を追加し、コントローラーまたはページ コンストラクターに `IDownstreamWebApi` サービスを直接挿入してアクションでそれを使用することができます。

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
}
```

また、`CallWebApiForUserAsync` には、オブジェクトを直接受け取ることができる、厳密に型指定されたジェネリック オーバーライドもあります。 たとえば、次のメソッドは `Todo` インスタンスを受け取ります。これは、Web API から返された JSON の厳密に型指定された表現です。

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

`ITokenAcquisition` サービスを使用して手動でトークンを取得することにしました。そのため、トークンを使用する必要があります。 この場合、次のコードは、「[Web API を呼び出す Web アプリ: アプリのトークンを取得する](scenario-web-app-call-api-acquire-token.md)」で示されているコード例の続きです。 このコードは、Web アプリ コントローラーのアクションで呼び出されます。

トークンを取得したら、それをベアラー トークンとして使用してダウンストリーム API (この場合は Microsoft Graph) を呼び出します。

 ```csharp
public async Task<IActionResult> Profile()
{
 // Acquire the access token.
 string[] scopes = new string[]{"user.read"};
 string accessToken = await tokenAcquisition.GetAccessTokenForUserAsync(scopes);

 // Use the access token to call a protected web API.
 HttpClient client = new HttpClient();
 client.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", accessToken);

  var response = await httpClient.GetAsync($"{webOptions.GraphApiUrl}/beta/me");

  if (response.StatusCode == HttpStatusCode.OK)
  {
   var content = await response.Content.ReadAsStringAsync();

   dynamic me = JsonConvert.DeserializeObject(content);
   ViewData["Me"] = me;
  }

  return View();
}
```
> [!NOTE]
> 同じ原則を使用するとどの web API も呼び出すことができます。
>
> ほとんどの Azure Web API には、Microsoft Graph の場合のように、API の呼び出しを簡略化する SDK が用意されています。 たとえば、Microsoft.Identity.Web と Azure Storage SDK を使用する Web アプリの例については、「[Azure AD で Blob Storage へのアクセスを承認する Web アプリケーションを作成する](../../storage/common/storage-auth-aad-app.md?tabs=dotnet&toc=%2fazure%2fstorage%2fblobs%2ftoc.json)」を参照してください。

# <a name="java"></a>[Java](#tab/java)

```Java
private String getUserInfoFromGraph(String accessToken) throws Exception {
    // Microsoft Graph user endpoint
    URL url = new URL("https://graph.microsoft.com/v1.0/me");

    HttpURLConnection conn = (HttpURLConnection) url.openConnection();

    // Set the appropriate header fields in the request header.
    conn.setRequestProperty("Authorization", "Bearer " + accessToken);
    conn.setRequestProperty("Accept", "application/json");

    String response = HttpClientHelper.getResponseStringFromConn(conn);

    int responseCode = conn.getResponseCode();
    if(responseCode != HttpURLConnection.HTTP_OK) {
        throw new IOException(response);
    }

    JSONObject responseObject = HttpClientHelper.processResponse(responseCode, response);
    return responseObject.toString();
}

```

# <a name="python"></a>[Python](#tab/python)

```Python
@app.route("/graphcall")
def graphcall():
    token = _get_token_from_cache(app_config.SCOPE)
    if not token:
        return redirect(url_for("login"))
    graph_data = requests.get(  # Use token to call downstream service.
        app_config.ENDPOINT,
        headers={'Authorization': 'Bearer ' + token['access_token']},
        ).json()
    return render_template('display.html', result=graph_data)
```

---

## <a name="next-steps"></a>次のステップ

このシナリオの次の記事「[運用環境に移行する](scenario-web-app-call-api-production.md)」に進みます。