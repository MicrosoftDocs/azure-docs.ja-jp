---
title: 認証トークンをキャッシュする
titleSuffix: Azure Cognitive Services
description: この記事では、認証トークンをキャッシュする方法について説明します。
author: metanMSFT
manager: guillasi
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: how-to
ms.date: 01/14/2020
ms.author: metang
ms.custom: devx-track-js, devx-track-csharp
ms.openlocfilehash: e79ae3914e32038e2823fb37e3eee658c95e0003
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "102608769"
---
# <a name="how-to-cache-the-authentication-token"></a>認証トークンをキャッシュする方法

この記事では、アプリケーションのパフォーマンスを高めるために認証トークンをキャッシュする方法について説明します。

## <a name="using-aspnet"></a>ASP.NET を使用する

**Microsoft.IdentityModel.Clients.ActiveDirectory** の NuGet パッケージをインポートします。これは、トークンを取得する際に使用されます。 次に、以下のコードを使用して `AuthenticationResult` を取得します。[イマーシブ リーダー リソースを作成](./how-to-create-immersive-reader.md)するときに取得した認証値を使用してください。

```csharp
private async Task<AuthenticationResult> GetTokenAsync()
{
    AuthenticationContext authContext = new AuthenticationContext($"https://login.windows.net/{TENANT_ID}");
    ClientCredential clientCredential = new ClientCredential(CLIENT_ID, CLIENT_SECRET);
    AuthenticationResult authResult = await authContext.AcquireTokenAsync("https://cognitiveservices.azure.com/", clientCredential);
    return authResult;
}
```

`AuthenticationResult` オブジェクトには、`AccessToken` プロパティがあります。このプロパティが、SDK を使用してイマーシブ リーダーを起動する際に実際に使用することになるトークンです。 また、トークンの有効期限を表す `ExpiresOn` プロパティもあります。 イマーシブ リーダーを起動する前に、トークンの有効期限が切れているかどうかを調べ、切れている場合にのみ新しいトークンを取得することができます。

## <a name="using-nodejs"></a>Node.JS を使用する

[**request**](https://www.npmjs.com/package/request) npm パッケージをプロジェクトに追加します。 以下のコードで、[イマーシブ リーダー リソースを作成](./how-to-create-immersive-reader.md)するときに取得した認証値を使用してトークンを取得します。

```javascript
router.get('/token', function(req, res) {
    request.post(
        {
            headers: { 'content-type': 'application/x-www-form-urlencoded' },
            url: `https://login.windows.net/${TENANT_ID}/oauth2/token`,
            form: {
                grant_type: 'client_credentials',
                client_id: CLIENT_ID,
                client_secret: CLIENT_SECRET,
                resource: 'https://cognitiveservices.azure.com/'
            }
        },
        function(err, resp, json) {
            const result = JSON.parse(json);
            return res.send({
                access_token: result.access_token,
                expires_on: result.expires_on
            });
        }
    );
});
```

`expires_on` プロパティはトークンの有効期限が切れる日時で、1970 年 1 月 1 日 (UTC) からの経過秒数として表されます。 新しいトークンを取得する際は、この値を使用して、トークンの有効期限が切れているかどうかを事前に調べてください。

```javascript
async function getToken() {
    if (Date.now() / 1000 > CREDENTIALS.expires_on) {
        CREDENTIALS = await refreshCredentials();
    }
    return CREDENTIALS.access_token;
}
```

## <a name="next-steps"></a>次のステップ

* [イマーシブ リーダー SDK リファレンス](./reference.md)を参照する