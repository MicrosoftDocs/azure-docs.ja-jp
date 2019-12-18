---
title: Web API を呼び出す Web API - Microsoft ID プラットフォ―ム | Azure
description: Web API を呼び出す Web API を構築する方法について説明します。
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
ms.openlocfilehash: 6063d143e2f217426bdf1db217fde46f8542d314
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/10/2019
ms.locfileid: "74965213"
---
# <a name="web-api-that-calls-web-apis---call-an-api"></a>Web API を呼び出す Web API - API の呼び出し

トークンを取得すると、保護された Web API を呼び出すことができます。 これは、ASP.NET/ASP.NET Core web API のコント ローラーから行われます。

## <a name="controller-code"></a>コントローラー コード

以下に示すのは、API コントローラーのアクションで呼び出されて、ダウンストリーム API (todolist という名前) を呼び出す「[Web API を呼び出す保護された Web API - トークンの取得](scenario-web-api-call-api-acquire-token.md)」で示されたサンプル コードの続きです。

トークンを取得したら、ダウン ストリーム API を呼び出すベアラー トークンとしてそれを使用します。

```CSharp
private async Task GetTodoList(bool isAppStarting)
{
 ...
 //
 // Get an access token to call the To Do service.
 //
 AuthenticationResult result = null;
 try
 {
  app = BuildConfidentialClient(HttpContext, HttpContext.User);
  result = await app.AcquireTokenSilent(Scopes, account)
                     .ExecuteAsync()
                     .ConfigureAwait(false);
 }
...

// Once the token has been returned by MSAL, add it to the http authorization header, before making the call to access the To Do list service.
_httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", result.AccessToken);

// Call the To Do list service.
HttpResponseMessage response = await _httpClient.GetAsync(TodoListBaseAddress + "/api/todolist");
...
}
```

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [運用環境への移行](scenario-web-api-call-api-production.md)
