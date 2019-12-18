---
title: Web API を呼び出すシングル ページ アプリを構築する - Microsoft IDプラットフォーム | Azure
description: Web API を呼び出すシングル ページ アプリケーションをビルドする方法について学ぶ
services: active-directory
documentationcenter: dev-center-name
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/06/2019
ms.author: ryanwi
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1171d8c3bc28c7b325cc8daf6cc072965363339c
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/10/2019
ms.locfileid: "74965333"
---
# <a name="single-page-application-call-a-web-api"></a>シングルページ アプリケーション：Web API を呼び出す

Web API を呼び出す前に、`acquireTokenSilent` メソッドを呼び出してアクセス トークンを取得または更新することをお勧めします。 トークンを取得した後に、保護された Web API を呼び出せます。

## <a name="call-a-web-api"></a>Web API を呼び出す

### <a name="javascript"></a>JavaScript

取得したアクセス トークンを HTTP 要求のベアラーとして使用して、Microsoft Graph API などの任意の Web API を呼び出します。 例:

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

### <a name="angular"></a>Angular

MSAL Angular ラッパーを使用すると、自動的にアクセス トークンをサイレントに取得して API への HTTP 要求にそれを添付するために HTTP インターセプターを利用できます。 詳細については、[API を呼び出すためのトークンの取得](scenario-spa-acquire-token.md)に関するページを参照してください。

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [運用環境への移行](scenario-spa-production.md)
