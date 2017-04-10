---
title: "Azure AD v2.0 NodeJS Web アプリ | Microsoft Docs"
description: "サインインに個人の Microsoft アカウントと会社/学校アカウントの両方を使用する Node JS Web アプリを構築する方法を説明します。"
services: active-directory
documentationcenter: nodejs
author: xerners
manager: mbaldwin
editor: 
ms.assetid: 1b889e72-f5c3-464a-af57-79abf5e2e147
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: javascript
ms.topic: article
ms.date: 09/16/2016
ms.author: brandwe
translationtype: Human Translation
ms.sourcegitcommit: 3e0bb32a6c60011d71606c896cc506f430bc3c27
ms.openlocfilehash: 372b1af96ec84f88d39aa567b5a24fc12a898551
ms.lasthandoff: 03/29/2017


---
# <a name="add-sign-in-to-a-nodejs-web-app"></a>サインインを nodeJS Web アプリに追加する
> [!NOTE]
> Azure Active Directory のシナリオおよび機能のすべてが v2.0 エンドポイントでサポートされているわけではありません。  v2.0 エンドポイントを使用する必要があるかどうかを判断するには、 [v2.0 の制限事項](active-directory-v2-limitations.md)に関するページをお読みください。
> 
> 

ここでは、Passport を使用して次の操作を行います。

* Azure AD と v2.0 エンドポイントを使用するアプリにユーザーをサインインします。
* ユーザーについての情報を表示します。
* ユーザーをアプリからサインアウトします。

**Passport** は Node.js 用の認証ミドルウェアです。 Passport は、非常に柔軟で高度なモジュール構造をしており、任意の Express ベースまたは Resitify Web アプリケーションに、支障をきたすことなくドロップされます。 包括的な認証手法セットにより、ユーザー名とパスワードを使用する認証、Facebook、Twitter などをサポートします。 Microsoft Azure Active Directory 用の戦略が開発されています。 ここでは、このモジュールをインストールした後、Microsoft Azure Active Directory `passport-azure-ad` プラグインを追加します。

## <a name="download"></a>ダウンロード
このチュートリアルのコードは、 [GitHub](https://github.com/AzureADQuickStarts/AppModelv2-WebApp-OpenIDConnect-nodejs)で管理されています。  追加の参考資料として、 [アプリのスケルトン (.zip) をダウンロード](https://github.com/AzureADQuickStarts/AppModelv2-WebApp-OpenIDConnect-nodejs/archive/skeleton.zip) したり、スケルトンを複製したりすることができます:

```git clone --branch skeleton https://github.com/AzureADQuickStarts/AppModelv2-WebApp-OpenIDConnect-nodejs.git```

完成したアプリケーションは、このチュートリアルの終わりにも示しています。

## <a name="1-register-an-app"></a>1.アプリを登録します
[apps.dev.microsoft.com](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList) で新しいアプリを作成するか、この[詳細な手順](active-directory-v2-app-registration.md)に従います。  次のことを確認します。

* アプリに割り当てられた**アプリケーション ID** をメモしておきます。これは後で必要になります。
* アプリの **Web** プラットフォームを追加します。
* 適切な **リダイレクト URI**を入力します。 リダイレクト URI は、認証の応答が送られる Azure AD を示します。このチュートリアルの既定値は `http://localhost:3000/auth/openid/return` です。

## <a name="2-add-pre-requisities-to-your-directory"></a>2.ディレクトリに前提条件を追加する
コマンド ラインから、ディレクトリをルート フォルダーに移動し (まだルート フォルダーでない場合)、次のコマンドを実行します。

* `npm install express`
* `npm install ejs`
* `npm install ejs-locals`
* `npm install restify`
* `npm install mongoose`
* `npm install bunyan`
* `npm install assert-plus`
* `npm install passport`
* `npm install webfinger`
* `npm install body-parser`
* `npm install express-session`
* `npm install cookie-parser`
* さらに、クイック スタートのスケルトンで、 `passport-azure-ad` を使用しています。
* `npm install passport-azure-ad`

これにより、passport-azure-ad が依存するライブラリがインストールされます。

## <a name="3-set-up-your-app-to-use-the-passport-node-js-strategy"></a>3.passport-node-js 戦略を使用するようにアプリを設定する
ここでは、OpenID Connect 認証プロトコルを使用するように、Express ミドルウェアを構成します。  Passport は、サインイン要求とサインアウト要求の発行、ユーザー セッションの管理、ユーザーに関する情報の取得などを行うために使用されます。

* 最初に、プロジェクトのルートにある `config.js` ファイルを開いて、`exports.creds` セクションでアプリの構成値を入力します。
  
  * `clientID:` は、登録ポータルでアプリに割り当てられた **アプリケーション ID** です。
  * `returnURL` は、ポータルで入力した **リダイレクト URI** です。
  * `clientSecret` は、ポータルで生成したシークレットです。
* 次に、プロジェクトのルートにある `app.js` ファイルを開き、次の呼び出しを追加して、`passport-azure-ad` に付属する `OIDCStrategy` 戦略を呼び出します。

```JavaScript
var OIDCStrategy = require('passport-azure-ad').OIDCStrategy;

// Add some logging
var log = bunyan.createLogger({
    name: 'Microsoft OIDC Example Web Application'
});
```

* その後、今参照した戦略を使用してログイン要求を処理します。

```JavaScript
// Use the OIDCStrategy within Passport. (Section 2)
//
//   Strategies in passport require a `validate` function, which accept
//   credentials (in this case, an OpenID identifier), and invoke a callback
//   with a user object.
passport.use(new OIDCStrategy({
    callbackURL: config.creds.returnURL,
    realm: config.creds.realm,
    clientID: config.creds.clientID,
    clientSecret: config.creds.clientSecret,
    oidcIssuer: config.creds.issuer,
    identityMetadata: config.creds.identityMetadata,
    responseType: config.creds.responseType,
    responseMode: config.creds.responseMode,
    skipUserProfile: config.creds.skipUserProfile
    scope: config.creds.scope
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
Passport は、すべての戦略ライターが従うすべての戦略 (Twitter や Facebook など) に対して類似するパターンを使用します。 戦略を調べると、それは、パラメーターとして token と done を持つ function() が渡されることがわかります。 戦略は、その処理をすべて終えると、必ず戻ってきます。 戻ったら、再度要求しなくてもいいように、ユーザーを保存し、トークンを隠します。

> [!IMPORTANT]
> 上記のコードでは、サーバーに認証を求めたすべてのユーザーを受け入れています。 これは、自動登録と呼ばれます。 運用サーバーでは、指定された登録プロセスを先に実行していないユーザーにはアクセスを許可しないように設定できます。 これは、Facebook への登録は許可するが、その後で追加情報の入力を求めるコンシューマー アプリで通常見られるパターンです。 これがサンプル アプリケーションでなければ、返されるトークン オブジェクトから電子メールを抽出した後、追加情報の入力を要求できます。 これはテスト サーバーなので、単純にユーザーをメモリ内データベースに追加します。
> 
> 

* 次に、Passport で必要な、ログオンしているユーザーの追跡を可能にするメソッドを追加します。 これには、ユーザーの情報のシリアル化と逆シリアル化が含まれます。

```JavaScript

// Passport session setup. (Section 2)

//   To support persistent login sessions, Passport needs to be able to
//   serialize users into and deserialize users out of the session.  Typically,
//   this will be as simple as storing the user ID when serializing, and finding
//   the user by ID when deserializing.
passport.serializeUser(function(user, done) {
  done(null, user.email);
});

passport.deserializeUser(function(id, done) {
  findByEmail(id, function (err, user) {
    done(err, user);
  });
});

// array to hold logged in users
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

* 次に、express エンジンを読み込むコードを追加します。 ここでは、Express が提供する既定の /views と /routes のパターンを使用します。

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
  // Initialize Passport!  Also use passport.session() middleware, to support
  // persistent login sessions (recommended).
  app.use(passport.initialize());
  app.use(passport.session());
  app.use(app.router);
  app.use(express.static(__dirname + '/../../public'));
});

```

* 最後に、実際のログイン要求を `passport-azure-ad` エンジンに渡す POST ルートを追加します。

```JavaScript

// Our Auth routes (Section 3)

// GET /auth/openid
//   Use passport.authenticate() as route middleware to authenticate the
//   request.  The first step in OpenID authentication will involve redirecting
//   the user to their OpenID provider.  After authenticating, the OpenID
//   provider will redirect the user back to this application at
//   /auth/openid/return

app.get('/auth/openid',
  passport.authenticate('azuread-openidconnect', { failureRedirect: '/login' }),
  function(req, res) {
    log.info('Authenitcation was called in the Sample');
    res.redirect('/');
  });

// GET /auth/openid/return
//   Use passport.authenticate() as route middleware to authenticate the
//   request.  If authentication fails, the user will be redirected back to the
//   login page.  Otherwise, the primary route function function will be called,
//   which, in this example, will redirect the user to the home page.
app.get('/auth/openid/return',
  passport.authenticate('azuread-openidconnect', { failureRedirect: '/login' }),
  function(req, res) {

    res.redirect('/');
  });

// POST /auth/openid/return
//   Use passport.authenticate() as route middleware to authenticate the
//   request.  If authentication fails, the user will be redirected back to the
//   login page.  Otherwise, the primary route function function will be called,
//   which, in this example, will redirect the user to the home page.

app.post('/auth/openid/return',
  passport.authenticate('azuread-openidconnect', { failureRedirect: '/login' }),
  function(req, res) {

    res.redirect('/');
  });
```

## <a name="4-use-passport-to-issue-sign-in-and-sign-out-requests-to-azure-ad"></a>4.Passport を使用してサインイン要求とサインアウト要求を Azure AD に発行する
OpenID Connect 認証プロトコルを使用して v2.0 エンドポイントと通信するように、アプリが適切に構成されました。  `passport-azure-ad` が行います。  残っているのは、サインインとサインアウトを行う方法をユーザーに提示することと、ログインしているユーザーに関する追加情報を収集することです。

* まず、既定のメソッド、login メソッド、account メソッド、logout メソッドを `app.js` ファイルに追加します。

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

* これらについて詳しく説明しましょう。
  
  * `/` ルートは、index.ejs ビューにリダイレクトして、要求内のユーザーを渡します (存在する場合)。
  * `/account` ルートは、"***ユーザーが認証されていることを確認***" した後 (次で実装します)、ユーザーに関する追加情報を取得できるように、要求内のユーザーを渡します。
  * `/login` ルートは、`passport-azuread` から azuread-openidconnect authenticator を呼び出し、これが成功しなかった場合は、ユーザーを /login にリダイレクトして戻します。
  * `/logout` は、クッキーをクリアする logout.ejs (およびルート) を呼び出した後、ユーザーを index.ejs に返します。
* `app.js` の最後の部分に、上記の `/account` で使用される EnsureAuthenticated メソッドを追加します。

```JavaScript

// Simple route middleware to ensure user is authenticated. (Section 4)

//   Use this route middleware on any resource that needs to be protected.  If
//   the request is authenticated (typically via a persistent login session),
//   the request will proceed.  Otherwise, the user will be redirected to the
//   login page.
function ensureAuthenticated(req, res, next) {
  if (req.isAuthenticated()) { return next(); }
  res.redirect('/login')
}

```

* 最後に、`app.js` でサーバー自体を実際に作成します。

```JavaScript

app.listen(3000);

```


## <a name="5-create-the-views-and-routes-in-express-to-display-our-user-in-the-website"></a>5.Web サイトにユーザーを表示する express のビューとルートを作成する
`app.js` が完成しました。 次に実行する必要があるのは、取得した情報をユーザーに表示するルートとビューを追加し、作成した `/logout` ルートと `/login` ルートを処理することだけです。

* ルート ディレクトリの下に `/routes/index.js` ルートを作成します。

```JavaScript

/*
 * GET home page.
 */

exports.index = function(req, res){
  res.render('index', { title: 'Express' });
};
```

* ルート ディレクトリの下に `/routes/user.js` ルートを作成します。

```JavaScript

/*
 * GET users listing.
 */

exports.list = function(req, res){
  res.send("respond with a resource");
};
```

これらは、要求 (存在する場合はユーザーも) をビューに渡すだけの単純なルートです。

* ルート ディレクトリの下に `/views/index.ejs` ビューを作成します。 これは login メソッドと logout メソッドを呼び出して、アカウント情報を取得できるようにする単純なページです。 条件付きの `if (!user)` を使用できることに注目してください。要求でユーザーが渡されることは、ログインしているユーザーが存在していることの証拠です。

```JavaScript
<% if (!user) { %>
    <h2>Welcome! Please log in.</h2>
    <a href="/login">Log In</a>
<% } else { %>
    <h2>Hello, <%= user.displayName %>.</h2>
    <a href="/account">Account Info</a></br>
    <a href="/logout">Log Out</a>
<% } %>
```

* ルート ディレクトリの下に `/views/account.ejs` ビューを作成し、`passport-azuread` がユーザー要求の中に配置された追加情報を表示できるようにします。

```Javascript
<% if (!user) { %>
    <h2>Welcome! Please log in.</h2>
    <a href="/login">Log In</a>
<% } else { %>
<p>displayName: <%= user.displayName %></p>
<p>givenName: <%= user.name.givenName %></p>
<p>familyName: <%= user.name.familyName %></p>
<p>UPN: <%= user._json.upn %></p>
<p>Profile ID: <%= user.id %></p>
<p>Full Claimes</p>
<%- JSON.stringify(user) %>
<p></p>
<a href="/logout">Log Out</a>
<% } %>
```

* 最後に、レイアウトを追加して、見やすくします。 ルート ディレクトリの下に '/views/layout.ejs' ビューを作成します。

```HTML

<!DOCTYPE html>
<html>
    <head>
        <title>Passport-OpenID Example</title>
    </head>
    <body>
        <% if (!user) { %>
            <p>
            <a href="/">Home</a> |
            <a href="/login">Log In</a>
            </p>
        <% } else { %>
            <p>
            <a href="/">Home</a> |
            <a href="/account">Account</a> |
            <a href="/logout">Log Out</a>
            </p>
        <% } %>
        <%- body %>
    </body>
</html>
```

最後に、アプリを構築して実行します。

`node app.js` を実行し、`http://localhost:3000` に移動します。

Microsoft の個人または職場/学校アカウントのいずれかでサインインすると、ユーザーの ID が /account リストにどのように反映されるかがわかります。  これで、Web アプリが業界標準のプロトコルで保護され、個人および職場/学校アカウントの両方でユーザーを認証できるようになりました。

## <a name="next-steps"></a>次のステップ
参照用に、完成したサンプル (構成値を除く) が[ここに .zip として提供されています](https://github.com/AzureADQuickStarts/AppModelv2-WebApp-OpenIDConnect-nodejs/archive/complete.zip)。または、GitHub から複製することもできます。

```git clone --branch complete https://github.com/AzureADQuickStarts/AppModelv2-WebApp-OpenIDConnect-nodejs.git```

これ以降は、さらに高度なトピックに進むことができます。  次のチュートリアルを試してみてください。

[v2.0 エンドポイントを使用して node.js Web API をセキュリティで保護する >>](active-directory-v2-devquickstarts-node-api.md)

その他のリソースについては、以下を参照してください。

* [v2.0 開発者向けガイド >>](active-directory-appmodel-v2-overview.md)
* [StackOverflow "azure-Active Directory" タグ >>](http://stackoverflow.com/questions/tagged/azure-active-directory)

## <a name="get-security-updates-for-our-products"></a>Microsoft 製品のセキュリティ更新プログラムの取得
セキュリティの問題が発生したときに通知を受け取ることをお勧めします。そのためには、[このページ](https://technet.microsoft.com/security/dd252948)にアクセスし、セキュリティ アドバイザリ通知を受信登録してください。


