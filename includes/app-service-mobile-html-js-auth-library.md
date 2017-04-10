### <a name="a-nameserver-authahow-to-authenticate-with-a-provider-server-flow"></a><a name="server-auth"></a>方法: プロバイダーでの認証 (サーバー フロー)
Mobile Apps によってアプリの認証プロセスを管理するには、アプリを ID プロバイダーに登録する必要があります。 その後、Azure App Service 内で、プロバイダーから提供されたアプリケーション ID とシークレットを構成する必要があります。
詳細については、チュートリアル「 [アプリへの認証の追加](../articles/app-service-mobile/app-service-mobile-cordova-get-started-users.md)」を参照してください。

ID プロバイダーを登録したら、プロバイダーの名前を指定して `.login()` メソッドを呼び出します。 たとえば、Facebook にログインするには、次のコードを使用します。

```
client.login("facebook").done(function (results) {
     alert("You are now logged in as: " + results.userId);
}, function (err) {
     alert("Error: " + err);
});
```

プロバイダーの名前として有効な値は、"aad"、"facebook"、"google"、"microsoftaccount"、"twitter" です。

> [!NOTE]
> 現在、サーバー フローでは Google 認証が正しく機能しません。  Google の認証には、[クライアント フロー](#client-auth)を使用する必要があります。

この場合は、Azure App Service が OAuth 2.0 認証フローを管理します。  選択されたプロバイダーのログイン ページを表示し、ID プロバイダーでのログインが成功した後で App Service 認証トークンを生成します。 login 関数は、完了すると、userId フィールドのユーザー ID と authenticationToken フィールドの App Service 認証トークンの両方を公開する JSON オブジェクトを返します。 このトークンをキャッシュし、有効期限が切れるまで再利用できます。

###<a name="a-nameclient-authahow-to-authenticate-with-a-provider-client-flow"></a><a name="client-auth"></a>方法: プロバイダーでの認証 (クライアント フロー)

アプリケーションは個別に ID プロバイダーにアクセスして、返されたトークンを認証のために App Service に提供することもできます。 このクライアント フローでは、ユーザーにシングル サインイン エクスペリエンスを提供したり、ID プロバイダーから追加のユーザー データを取得したりすることができます。

#### <a name="social-authentication-basic-example"></a>ソーシャル認証の基本的な例

この例では、認証用の Facebook クライアント SDK を使用します。

```
client.login(
     "facebook",
     {"access_token": token})
.done(function (results) {
     alert("You are now logged in as: " + results.userId);
}, function (err) {
     alert("Error: " + err);
});

```
この例では、それぞれのプロバイダー SDK で提供されるトークンが変数 token に格納されるとします。

#### <a name="microsoft-account-example"></a>Microsoft アカウントの例

次の例では、Windows ストア アプリケーションのシングル サインオンを Microsoft アカウントによってサポートしている Live SDK を使用します。

```
WL.login({ scope: "wl.basic"}).then(function (result) {
      client.login(
            "microsoftaccount",
            {"authenticationToken": result.session.authentication_token})
      .done(function(results){
            alert("You are now logged in as: " + results.userId);
      },
      function(error){
            alert("Error: " + err);
      });
});

```

この例では、トークンを Live Connect から取得します。このトークンは、login 関数を呼び出すことによって、App Service に渡されます。

###<a name="a-nameauth-getinfoahow-to-obtain-information-about-the-authenticated-user"></a><a name="auth-getinfo"></a>方法: 認証されたユーザーに関する情報の取得

認証情報は、AJAX ライブラリによる HTTP 呼び出しを使用して `/.auth/me` エンドポイントから取得できます。  `X-ZUMO-AUTH` ヘッダーを認証トークンに設定していることを確認してください。  認証トークンは `client.currentUser.mobileServiceAuthenticationToken`に格納されています。  たとえば、次のフェッチ API を使用します。

```
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
