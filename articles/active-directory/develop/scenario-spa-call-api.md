---
title: Web API を呼び出すシングル ページ アプリを作成する
titleSuffix: Microsoft identity platform
description: Web API を呼び出すシングル ページ アプリケーションをビルドする方法について学ぶ
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/06/2019
ms.author: ryanwi
ms.custom: aaddev
ms.openlocfilehash: 4dd4a40233fc1c030581fd1ae2827061435a0ab3
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "98753547"
---
# <a name="single-page-application-call-a-web-api"></a>シングルページ アプリケーション：Web API を呼び出す

Web API を呼び出す前に、`acquireTokenSilent` メソッドを呼び出してアクセス トークンを取得または更新することをお勧めします。 トークンを取得した後に、保護された Web API を呼び出せます。

## <a name="call-a-web-api"></a>Web API を呼び出す

# <a name="javascript"></a>[JavaScript](#tab/javascript)

取得したアクセス トークンを HTTP 要求のベアラーとして使用して、Microsoft Graph API などの任意の Web API を呼び出します。 次に例を示します。

```javascript
    var headers = new Headers();
    var bearer = "Bearer " + access_token;
    headers.append("Authorization", bearer);
    var options = {
         method: "GET",
         headers: headers
    };
    var graphEndpoint = "https://graph.microsoft.com/v1.0/me";

    fetch(graphEndpoint, options)
        .then(function (response) {
             //do something with response
        }
```

# <a name="angular"></a>[Angular](#tab/angular)

MSAL Angular ラッパーを使用すると、自動的にアクセス トークンをサイレントに取得して API への HTTP 要求にそれを添付するために HTTP インターセプターを利用できます。 詳細については、[API を呼び出すためのトークンの取得](scenario-spa-acquire-token.md)に関するページを参照してください。

---

## <a name="next-steps"></a>次のステップ

このシナリオの次の記事「[運用環境に移行する](scenario-spa-production.md)」に進みます。