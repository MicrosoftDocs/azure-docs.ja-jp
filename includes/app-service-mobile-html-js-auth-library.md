---
author: conceptdev
ms.service: app-service-mobile
ms.topic: include
ms.date: 08/23/2018
ms.author: crdun
ms.openlocfilehash: 5fe9fe8ced675f68161f0df9f2665b47f9d47ac5
ms.sourcegitcommit: e51e940e1a0d4f6c3439ebe6674a7d0e92cdc152
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/08/2019
ms.locfileid: "55905203"
---
### <a name="server-auth"></a>方法: プロバイダーでの認証 (サーバー フロー)
Mobile Apps によってアプリの認証プロセスを管理するには、アプリを ID プロバイダーに登録する必要があります。 その後、Azure App Service 内で、プロバイダーから提供されたアプリケーション ID とシークレットを構成する必要があります。
詳細については、チュートリアル「 [アプリへの認証の追加](../articles/app-service-mobile/app-service-mobile-cordova-get-started-users.md)」を参照してください。

ID プロバイダーを登録したら、プロバイダーの名前を指定して `.login()` メソッドを呼び出します。 たとえば、Facebook にサインインするには、次のコードを使用します。

```javascript
client.login("facebook").done(function (results) {
     alert("You are now signed in as: " + results.userId);
}, function (err) {
     alert("Error: " + err);
});
```

プロバイダーの名前として有効な値は、"aad"、"facebook"、"google"、"microsoftaccount"、"twitter" です。

> [!NOTE]
> 現在、サーバー フローでは Google 認証が正しく機能しません。  Google の認証には、[クライアント フロー](#client-auth)を使用する必要があります。

この場合は、Azure App Service が OAuth 2.0 認証フローを管理します。  選択されたプロバイダーのサインイン ページを表示し、ID プロバイダーでのサインインが成功した後で App Service 認証トークンを生成します。 login 関数は、完了すると、userId フィールドのユーザー ID と authenticationToken フィールドの App Service 認証トークンの両方を公開する JSON オブジェクトを返します。 このトークンをキャッシュし、有効期限が切れるまで再利用できます。

### <a name="client-auth"></a>方法: プロバイダーでの認証 (クライアント フロー)

アプリケーションは個別に ID プロバイダーにアクセスして、返されたトークンを認証のために App Service に提供することもできます。 このクライアント フローでは、ユーザーにシングル サインイン エクスペリエンスを提供したり、ID プロバイダーから追加のユーザー データを取得したりすることができます。

#### <a name="social-authentication-basic-example"></a>ソーシャル認証の基本的な例

この例では、認証用の Facebook クライアント SDK を使用します。

```javascript
client.login(
     "facebook",
     {"access_token": token})
.done(function (results) {
     alert("You are now signed in as: " + results.userId);
}, function (err) {
     alert("Error: " + err);
});

```
この例では、それぞれのプロバイダー SDK で提供されるトークンが変数 token に格納されるとします。

### <a name="auth-getinfo"></a>方法: 認証されたユーザーに関する情報の取得

認証情報は、AJAX ライブラリによる HTTP 呼び出しを使用して `/.auth/me` エンドポイントから取得できます。  `X-ZUMO-AUTH` ヘッダーを認証トークンに設定していることを確認してください。  認証トークンは `client.currentUser.mobileServiceAuthenticationToken`に格納されています。  たとえば、次のフェッチ API を使用します。

```javascript
var url = client.applicationUrl + '/.auth/me';
var headers = new Headers();
headers.append('X-ZUMO-AUTH', client.currentUser.mobileServiceAuthenticationToken);
fetch(url, { headers: headers })
    .then(function (data) {
        return data.json()
    }).then(function (user) {
        // The user object contains the claims for the authenticated user
    });
```

フェッチは [npm パッケージ](https://www.npmjs.com/package/whatwg-fetch)として、または [CDNJS](https://cdnjs.com/libraries/fetch) からのブラウザーのダウンロードに使用できます。 jQuery または別の AJAX API を使用して情報を取得することも可能です。  データは JSON オブジェクトとして取得されます。
