---
title: Web アプリから Web API を呼び出す - Microsoft ID プラットフォーム | Azure
description: Web API を呼び出す Web アプリの構築方法について説明します (保護された Web API の呼び出し)
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 07/14/2019
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 1e448f52f4e8c24dd8552cae873edac841e57fc6
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/23/2020
ms.locfileid: "87058437"
---
# <a name="a-web-app-that-calls-web-apis-call-a-web-api"></a>Web API を呼び出す Web アプリ: Web API を呼び出す

トークンを取得すると、保護された Web API を呼び出せます。

## <a name="call-a-protected-web-api"></a>保護された Web API を呼び出す

保護された Web API の呼び出しは、選択した言語とフレームワークによって異なります。

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

以下に、`HomeController` のアクションの簡略化されたコードを示します。 このコードでは、Microsoft Graph を呼び出すトークンを取得します。 Microsoft Graph を REST API として呼び出す方法を示すコードが追加されています。 Microsoft Graph API の URL は、appsettings.json ファイルで提供され、`webOptions` という名前の変数に読み取られます。

```json
{
  "AzureAd": {
    "Instance": "https://login.microsoftonline.com/",
    ...
  },
  ...
  "GraphApiUrl": "https://graph.microsoft.com"
}
```

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
> Azure のほとんどの Web API には、API の呼び出しを簡略化する SDK が用意されています。 これは Microsoft Graph にも当てはまります。 次の記事では、API の使用方法を示すチュートリアルの探し方について説明します。

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

> [!div class="nextstepaction"]
> [運用環境への移行](scenario-web-app-call-api-production.md)
