---
title: "Azure B2C 用の Node.js Web アプリにサインインを追加する | Microsoft Docs"
description: "B2C テナントを使用してユーザーをサインインさせる Node.js Web アプリを作成する方法。"
services: active-directory-b2c
documentationcenter: 
author: xerners
manager: mbaldwin
editor: 
ms.assetid: db97f84a-1f24-447b-b6d2-0265c6896b27
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: javascript
ms.topic: hero-article
ms.date: 03/10/2017
ms.author: brandwe
translationtype: Human Translation
ms.sourcegitcommit: a087df444c5c88ee1dbcf8eb18abf883549a9024
ms.openlocfilehash: 443516838190ccad408bc2b3b6b63513b3a3041e
ms.lasthandoff: 03/15/2017


---

# <a name="azure-ad-b2c-add-sign-in-to-a-nodejs-web-app"></a>Azure AD B2C: Node.js Web アプリにサインインを追加する

**Passport** は Node.js 用の認証ミドルウェアです。 Passport は、非常に柔軟で高度なモジュール構造をしており、任意の Express ベースまたは Restify Web アプリケーションに、支障をきたすことなくインストールされます。 包括的な認証手法セットにより、ユーザー名とパスワードを使用する認証、Facebook、Twitter などをサポートします。 

Azure Active Directory (Azure AD) 用の認証手法を開発しました。 このモジュールをインストールし、Azure AD `passport-azure-ad` プラグインを追加します。

そのためには、次の手順を実行する必要があります。

1. Azure AD を使用してアプリケーションを登録する
2. `passport-azure-ad` プラグインを使用するようにアプリをセットアップする
3. Passport を使用して、サインイン要求とサインアウト要求を Azure AD に発行する
4. ユーザー データを出力する

このチュートリアルのコードは、 [GitHub](https://github.com/AzureADQuickStarts/B2C-WebApp-OpenIDConnect-NodeJS)で管理されています。 追加の参考資料として、[アプリのスケルトン (.zip ファイル) をダウンロード](https://github.com/AzureADQuickStarts/B2C-WebApp-OpenIDConnect-NodeJS/archive/skeleton.zip)できます。 スケルトンを複製することもできます。

```git clone --branch skeleton https://github.com/AzureADQuickStarts/B2C-WebApp-OpenIDConnect-NodeJS.git```

完成したアプリケーションは、このチュートリアルの終わりに示しています。

> [!WARNING]
現時点では、Web API タスク サーバーとそのサーバーに接続するクライアントの両方に同じ**クライアント ID**/**アプリケーション ID** およびポリシーを使用する必要があります。 これは、iOS と Android のチュートリアルにも当てはまります。 これらのクイック スタートのいずれかでアプリケーションを作成したことがある場合は、新しい値を作成するのではなく、それらの値を使用してください。

## <a name="get-an-azure-ad-b2c-directory"></a>Azure AD B2C ディレクトリの取得

Azure AD B2C を使用するには、ディレクトリ (つまり、テナント) を作成しておく必要があります。  ディレクトリは、ユーザー、アプリ、グループなどをすべて格納するためのコンテナーです。 まだディレクトリを作成していない場合は、先に進む前に [B2C ディレクトリを作成](active-directory-b2c-get-started.md) してください。

## <a name="create-an-application"></a>アプリケーションの作成

次に、B2C ディレクトリにアプリを作成する必要があります。 これにより、アプリと安全に通信するために必要な情報を Azure AD に提供します。 クライアント アプリと Web API の両方が単一の**アプリケーション ID** で表されます。これは、クライアント アプリと Web API が&1; つの論理アプリを構成するためです。 アプリを作成するには、 [こちらの手順](active-directory-b2c-app-registration.md)に従ってください。 次を行ってください。

- アプリケーションに **Web アプリ**/**Web API** を含めます。
- **[応答 URL]** に「`http://localhost:3000/auth/openid/return`」と入力します。 これはこのサンプル コードで使用する既定の URL です。
- アプリケーション用の **アプリケーション シークレット** を作成し、それをメモしておきます。 この情報は後で必要になります。 この値は、使用する前に [XML エスケープ](https://www.w3.org/TR/2006/REC-xml11-20060816/#dt-escape) する必要があることに注意してください。
- アプリに割り当てられた **アプリケーション ID** をコピーしておきます。 この情報も後で必要になります。

[!INCLUDE [active-directory-b2c-devquickstarts-v2-apps](../../includes/active-directory-b2c-devquickstarts-v2-apps.md)]

## <a name="create-your-policies"></a>ポリシーの作成

Azure AD B2C では、すべてのユーザー エクスペリエンスが [ポリシー](active-directory-b2c-reference-policies.md)によって定義されます。 このアプリには、3 つの ID エクスペリエンス (サインアップ、サインイン、および Facebook を使用したサインイン) が含まれています。 [ポリシーについてのリファレンス記事](active-directory-b2c-reference-policies.md#create-a-sign-up-policy)で説明されているように、このポリシーを種類ごとに作成する必要があります。 3 つのポリシーを作成するときは、以下の点に注意してください。

- サインアップ ポリシーで、 **[表示名]** と他のサインアップ属性を選択します。
- すべてのポリシーで、アプリケーション要求として **[表示名]** と **[オブジェクト ID]** を選択します。 その他のクレームも選択できます。
- ポリシーの作成後、各ポリシーの **名前** をコピーしておきます。 名前には、 `b2c_1_`というプレフィックスが付加されています。  これらのポリシー名は後で必要になります。

[!INCLUDE [active-directory-b2c-devquickstarts-policy](../../includes/active-directory-b2c-devquickstarts-policy.md)]

3 つのポリシーを作成した後は、いつでもアプリをビルドできます。

この記事では、作成したポリシーの使用方法については説明しません。 ポリシーが Azure AD B2C でどのように機能するかを学習する場合は、 [.NET Web アプリ入門チュートリアル](active-directory-b2c-devquickstarts-web-dotnet.md)から始めてください。

## <a name="add-prerequisites-to-your-directory"></a>ディレクトリに前提条件を追加する

コマンド ラインで、ルート フォルダーに移動します (現在のディレクトリがこのディレクトリではない場合)。 次のコマンドを実行します。

- `npm install express`
- `npm install ejs`
- `npm install ejs-locals`
- `npm install restify`
- `npm install mongoose`
- `npm install bunyan`
- `npm install assert-plus`
- `npm install passport`
- `npm install webfinger`
- `npm install body-parser`
- `npm install express-session`
- `npm install cookie-parser`

さらに、クイック スタートのスケルトンでは、プレビュー用に `passport-azure-ad` を使用しています。

- `npm install passport-azure-ad`

これにより、`passport-azure-ad` が依存するライブラリがインストールされます。

## <a name="set-up-your-app-to-use-the-passport-nodejs-strategy"></a>Passport-Node.js 戦略を使用するようにアプリを設定する
OpenID Connect 認証プロトコルを使用するように、Express ミドルウェアを構成します。 Passport は、サインイン要求とサインアウト要求の発行、ユーザー セッションの管理、ユーザーに関する情報の取得などの操作を行うために使用されます。

プロジェクトのルートにある `config.js` ファイルを開いて、アプリの構成値を `exports.creds` セクションに入力します。
- `clientID`: 登録ポータルでアプリに割り当てられた**アプリケーション ID** です。
- `returnURL`: ポータルで入力した**リダイレクト URI** です。
- `tenantName`: アプリのテナント名です (例: **contoso.onmicrosoft.com**)。

[!INCLUDE [active-directory-b2c-devquickstarts-tenant-name](../../includes/active-directory-b2c-devquickstarts-tenant-name.md)]

プロジェクトのルートにある `app.js` ファイルを開きます。 `passport-azure-ad` に付属する `OIDCStrategy` 戦略を呼び出す次の呼び出しを追加します。


```JavaScript
var OIDCStrategy = require('passport-azure-ad').OIDCStrategy;

// Add some logging
var log = bunyan.createLogger({
    name: 'Microsoft OIDC Example Web Application'
});
```

今参照した戦略を使用してサインイン要求を処理します。

```JavaScript
// Use the OIDCStrategy in Passport (Section 2).
//
//   Strategies in Passport require a "validate" function that accepts
//   credentials (in this case, an OpenID identifier), and invokes a callback
//   by using a user object.
passport.use(new OIDCStrategy({
    callbackURL: config.creds.returnURL,
    realm: config.creds.realm,
    clientID: config.creds.clientID,
    oidcIssuer: config.creds.issuer,
    identityMetadata: config.creds.identityMetadata,
    skipUserProfile: config.creds.skipUserProfile,
    responseType: config.creds.responseType,
    responseMode: config.creds.responseMode,
    tenantName: config.creds.tenantName
  },
  function(iss, sub, profile, accessToken, refreshToken, done) {
    log.info('Example: Email address we received was: ', profile.email);
    // asynchronous verification, for effect...
    process.nextTick(function () {
      findByEmail(profile.email, function(err, user) {
        if (err) {
          return done(err);
        }
        if (!user) {
          // "Auto-registration"
          users.push(profile);
          return done(null, profile);
        }
        return done(null, user);
      });
    });
  }
));
```
Passport は、Twitter や Facebook などのすべての戦略に、同様のパターンを使用します。 すべての戦略記述者がこのパターンに準拠しています。 戦略を調べると、パラメーターとして token と `done` を持つ `function()` が渡されることがわかります。 戦略は、すべての作業が終わった後で返されます。 もう一度要求しなくて済むように、ユーザーを格納し、トークンを保存します。

> [!IMPORTANT]
上記のコードでは、サーバーが認証したすべてのユーザーを受け入れています。 これが自動登録です。 運用サーバーでは、指定された登録プロセスを先に実行していないユーザーは受け入れないようにする必要があります。 このようなパターンは、コンシューマー アプリでよく見られます。 これによって、Facebook を使用した登録を許可できるものの、後で追加情報の入力が求められます。 このアプリケーションがサンプルでなければ、返されるトークン オブジェクトから電子メール アドレスを抽出した後、追加情報の入力をユーザーに要求できます。 ここでは、テスト サーバーであるため、単純にユーザーをメモリ内データベースに追加します。

Passport で必要な、サインインしているユーザーの追跡を可能にするメソッドを追加します。 これには、ユーザー情報のシリアル化と逆シリアル化が含まれます。

```JavaScript

// Passport session setup. (Section 2)

//   To support persistent sign-in sessions, Passport needs to be able to
//   serialize users into and deserialize users out of sessions. Typically,
//   this is as simple as storing the user ID when Passport serializes a user
//   and finding the user by ID when Passport deserializes that user.
passport.serializeUser(function(user, done) {
  done(null, user.email);
});

passport.deserializeUser(function(id, done) {
  findByEmail(id, function (err, user) {
    done(err, user);
  });
});

// Array to hold users who have signed in
var users = [];

var findByEmail = function(email, fn) {
  for (var i = 0, len = users.length; i < len; i++) {
    var user = users[i];
    log.info('we are using user: ', user);
    if (user.email === email) {
      return fn(null, user);
    }
  }
  return fn(null, null);
};

```

Express エンジンを読み込むコードを追加します。 以下では、Express が提供する既定の `/views` と `/routes` のパターンを使用していることがわかります。

```JavaScript

// configure Express (Section 2)

var app = express();


app.configure(function() {
  app.set('views', __dirname + '/views');
  app.set('view engine', 'ejs');
  app.use(express.logger());
  app.use(express.methodOverride());
  app.use(cookieParser());
  app.use(expressSession({ secret: 'keyboard cat', resave: true, saveUninitialized: false }));
  app.use(bodyParser.urlencoded({ extended : true }));
  // Initialize Passport!  Also use passport.session() middleware to support
  // persistent sign-in sessions (recommended).
  app.use(passport.initialize());
  app.use(passport.session());
  app.use(app.router);
  app.use(express.static(__dirname + '/../../public'));
});

```

実際のサインイン要求を `passport-azure-ad` エンジンに渡す `POST` ルートを追加します。

```JavaScript

// Our Auth routes (Section 3)

// GET /auth/openid
//   Use passport.authenticate() as route middleware to authenticate the
//   request. The first step in OpenID authentication involves redirecting
//   the user to an OpenID provider. After the user is authenticated,
//   the OpenID provider redirects the user back to this application at
//   /auth/openid/return

app.get('/auth/openid',
  passport.authenticate('azuread-openidconnect', { failureRedirect: '/login' }),
  function(req, res) {
    log.info('Authentication was called in the Sample');
    res.redirect('/');
  });

// GET /auth/openid/return
//   Use passport.authenticate() as route middleware to authenticate the
//   request. If authentication fails, the user will be redirected back to the
//   sign-in page. Otherwise, the primary route function will be called.
//   In this example, it redirects the user to the home page.
app.get('/auth/openid/return',
  passport.authenticate('azuread-openidconnect', { failureRedirect: '/login' }),
  function(req, res) {

    res.redirect('/');
  });

// POST /auth/openid/return
//   Use passport.authenticate() as route middleware to authenticate the
//   request. If authentication fails, the user will be redirected back to the
//   sign-in page. Otherwise, the primary route function will be called.
//   In this example, it will redirect the user to the home page.

app.post('/auth/openid/return',
  passport.authenticate('azuread-openidconnect', { failureRedirect: '/login' }),
  function(req, res) {

    res.redirect('/');
  });
```

## <a name="use-passport-to-issue-sign-in-and-sign-out-requests-to-azure-ad"></a>Passport を使用してサインイン要求とサインアウト要求を Azure AD に発行する

これまでに、アプリは、OpenID Connect 認証プロトコルを使用して v2.0 エンドポイントと適切に通信するように構成されています。 `passport-azure-ad` は、認証メッセージの構築、Azure AD からのトークンの検証、およびユーザー セッションの維持を行うための処理を実行します。 残っているのは、サインインとサインアウトを行う方法をユーザーに提示することと、サインインしているユーザーに関する追加情報を収集することです。

まず、既定のメソッド、sign-in メソッド、account メソッド、sign-out メソッドを `app.js` ファイルに追加します。

```JavaScript

//Routes (Section 4)

app.get('/', function(req, res){
  res.render('index', { user: req.user });
});

app.get('/account', ensureAuthenticated, function(req, res){
  res.render('account', { user: req.user });
});

app.get('/login',
  passport.authenticate('azuread-openidconnect', { failureRedirect: '/login' }),
  function(req, res) {
    log.info('Login was called in the Sample');
    res.redirect('/');
});

app.get('/logout', function(req, res){
  req.logout();
  res.redirect('/');
});

```

これらのメソッドについて詳しく説明します。
- `/` ルートは、`index.ejs` ビューにリダイレクトして、要求内のユーザーを渡します (存在する場合)。
- まず、`/account` ルートは、ユーザーが認証されていることを確認します (この実装は下記のとおりです)。 次に、ユーザーに関する追加情報を取得できるように、要求でユーザーを渡します。
- `/login` ルートは、`passport-azure-ad` から `azuread-openidconnect` 認証子を呼び出します。 成功しなかった場合、ルートはユーザーを `/login` にリダイレクトして戻します。
- `/logout` は、`logout.ejs` (およびそのルート) を呼び出します。 これにより、Cookie をクリアし、ユーザーを `index.ejs` に返します。


`app.js` の最後の部分に、`/account` ルートで使用される `EnsureAuthenticated` メソッドを追加します。

```JavaScript

// Simple route middleware to ensure that the user is authenticated. (Section 4)

//   Use this route middleware on any resource that needs to be protected. If
//   the request is authenticated (typically via a persistent sign-in session),
//   then the request will proceed. Otherwise, the user will be redirected to the
//   sign-in page.
function ensureAuthenticated(req, res, next) {
  if (req.isAuthenticated()) { return next(); }
  res.redirect('/login')
}

```

最後に、`app.js` でサーバー自体を作成します。

```JavaScript

app.listen(3000);

```


## <a name="create-the-views-and-routes-in-express-to-call-your-policies"></a>ポリシーを呼び出すビューおよびルートを Express に作成する

これで `app.js` が完成しました。 次に実行する必要があるのは、サインイン ポリシーとサインアップ ポリシーを呼び出すことができるルートとビューを追加することです。 これにより、作成済みの `/logout` ルートと `/login` ルートも処理されます。

ルート ディレクトリの下に `/routes/index.js` ルートを作成します。

```JavaScript

/*
 * GET home page.
 */

exports.index = function(req, res){
  res.render('index', { title: 'Express' });
};
```

ルート ディレクトリの下に `/routes/user.js` ルートを作成します。

```JavaScript

/*
 * GET users listing.
 */

exports.list = function(req, res){
  res.send("respond with a resource");
};
```

これらは、要求をビューに渡すだけの単純なルートです。 存在する場合はユーザーも渡されます。

ルート ディレクトリの下に `/views/index.ejs` ビューを作成します。 これは、サインイン ポリシーとサインアウト ポリシーを呼び出す単純なページです。 また、アカウント情報を取得するためにも使用できます。 ユーザーがサインインしている証拠を示すために要求でユーザーを渡す際に、条件付きの `if (!user)` を使用できることに注目してください。

```JavaScript
<% if (!user) { %>
    <h2>Welcome! Please sign in.</h2>
    <a href="/login/?p=your facebook policy">Sign in with Facebook</a>
    <a href="/login/?p=your email sign-in policy">Sign in with email</a>
    <a href="/login/?p=your email sign-up policy">Sign up with email</a>
<% } else { %>
    <h2>Hello, <%= user.displayName %>.</h2>
    <a href="/account">Account info</a></br>
    <a href="/logout">Log out</a>
<% } %>
```

ルート ディレクトリの下に `/views/account.ejs` ビューを作成し、`passport-azure-ad` によってユーザー要求の中に配置された追加情報を表示できるようにします。

```Javascript
<% if (!user) { %>
    <h2>Welcome! Please sign in.</h2>
    <a href="/login">Sign in</a>
<% } else { %>
<p>displayName: <%= user.displayName %></p>
<p>givenName: <%= user.name.givenName %></p>
<p>familyName: <%= user.name.familyName %></p>
<p>UPN: <%= user._json.upn %></p>
<p>Profile ID: <%= user.id %></p>
<p>Full Claims</p>
<%- JSON.stringify(user) %>
<p></p>
<a href="/logout">Log Out</a>
<% } %>
```

これで、アプリを構築して実行できます。

`node app.js` を実行し、`http://localhost:3000` に移動します。


電子メールまたは Facebook を使用してアプリにサインアップまたはサインインします。 サインアウトし、別のユーザーとしてサインインします。

##<a name="next-steps"></a>次のステップ

参考のため、完成済みサンプル (構成値を除く) が [.zip ファイルとして提供されています](https://github.com/AzureADQuickStarts/B2C-WebApp-OpenIDConnect-NodeJS/archive/complete.zip)。 GitHub から複製することもできます。

```git clone --branch complete https://github.com/AzureADQuickStarts/B2C-WebApp-OpenIDConnect-nodejs.git```

これで、さらに高度なトピックに進む準備ができました。 次のトピックをご覧ください。

[Node.js の B2C モデルを使用して Web API をセキュリティで保護する](active-directory-b2c-devquickstarts-api-node.md)

<!--

For additional resources, check out:
You can now move on to more advanced B2C topics. You might try:

[Call a Node.js web API from a Node.js web app]()

[Customizing the your B2C App's UX >>]()

-->

