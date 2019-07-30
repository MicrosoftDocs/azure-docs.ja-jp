---
title: Web API を呼び出す Web アプリ (Web API の呼び出し) - Microsoft ID プラットフォーム
description: Web API を呼び出す Web アプリの構築方法について説明します (Web API の呼び出し)
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/07/2019
ms.author: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3624f4e859081e53ee27b6f8415eb3f9b5a2a5fa
ms.sourcegitcommit: 1572b615c8f863be4986c23ea2ff7642b02bc605
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/10/2019
ms.locfileid: "67785458"
---
# <a name="web-app-that-calls-web-apis---call-a-web-api"></a>Web API を呼び出す Web アプリ - Web API の呼び出し

トークンを取得したので、保護された Web API を呼び出すことができます。

## <a name="aspnet-core"></a>ASP.NET Core

以下に、`HomeController` のアクションの簡略化されたコードを示します。 このコードは、Microsoft Graph を呼び出すトークンを取得します。 今回コードが追加されて、Microsoft Graph を REST API として呼び出す方法が示されています。 Graph API の URL は、`appsettings.json` ファイルで提供され、`webOptions` という名前の変数に読み取られます。

```JSon
{
  "AzureAd": {
    "Instance": "https://login.microsoftonline.com/",
    ...
  },
  ...
  "GraphApiUrl": "https://graph.microsoft.com"
}
```

```CSharp
public async Task<IActionResult> Profile()
{
 var application = BuildConfidentialClientApplication(HttpContext, HttpContext.User);
 string accountIdentifier = claimsPrincipal.GetMsalAccountId();
 string loginHint = claimsPrincipal.GetLoginHint();

 // Get the account
 IAccount account = await application.GetAccountAsync(accountIdentifier);

 // Special case for guest users as the Guest iod / tenant id are not surfaced.
 if (account == null)
 {
  var accounts = await application.GetAccountsAsync();
  account = accounts.FirstOrDefault(a => a.Username == loginHint);
 }

 AuthenticationResult result;
 result = await application.AcquireTokenSilent(new []{"user.read"}, account)
                            .ExecuteAsync();
 var accessToken = result.AccessToken;

 // Calls the web API (here the graph)
 HttpClient httpClient = new HttpClient();
 httpClient.DefaultRequestHeaders.Authorization =
     new AuthenticationHeaderValue(Constants.BearerAuthorizationScheme,accessToken);
 var response = await httpClient.GetAsync($"{webOptions.GraphApiUrl}/beta/me");

 if (response.StatusCode == HttpStatusCode.OK)
 {
  var content = await response.Content.ReadAsStringAsync();

  dynamic me = JsonConvert.DeserializeObject(content);
  return me;
 }

 ViewData["Me"] = me;
 return View();
}
```

> [!NOTE]
> 同じ原則を使用するとどの web API も呼び出すことができます。
>
> ほとんどの Azure の Web API には、呼び出しを簡略化する SDK が用意されています。 これは、Microsoft Graph の場合も同じです。 次の記事では、これらの側面を示すチュートリアルの場所について説明します。

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [運用環境への移行](scenario-web-app-call-api-production.md)
