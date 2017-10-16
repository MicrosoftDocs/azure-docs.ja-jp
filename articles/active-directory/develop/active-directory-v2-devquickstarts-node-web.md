---
title: "Azure Active Directory v2.0 を使用した Node.js Web アプリのサインイン | Microsoft Docs"
description: "サインインに個人の Microsoft アカウントと職場/学校アカウントの両方を使用する Node.js Web アプリの構築方法を説明します。"
services: active-directory
documentationcenter: nodejs
author: navyasric
manager: mbaldwin
editor: 
ms.assetid: 1b889e72-f5c3-464a-af57-79abf5e2e147
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: javascript
ms.topic: article
ms.date: 05/13/2017
ms.author: nacanuma
ms.custom: aaddev
ms.openlocfilehash: 6d49c742f72440e22830915c90de009d9188db2a
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="add-sign-in-to-a-nodejs-web-app"></a>サインインを Node.js Web アプリに追加する

> [!NOTE]
> Azure Active Directory のシナリオと機能のすべてが v2.0 エンドポイントで機能するわけではありません。 v2.0 エンドポイントと v1.0 エンドポイントのどちらを使用するかを判断するには、[v2.0 の制限事項](active-directory-v2-limitations.md)に関する記事をご覧ください。
> 

このチュートリアルでは、Passport を使用して次のタスクを実行します。

* Azure Active Directory (Azure AD) と v2.0 エンドポイントを使用して、ユーザーを Web アプリにサインインします。
* ユーザーについての情報を表示します。
* ユーザーをアプリからサインアウトします。

**Passport** は Node.js 用の認証ミドルウェアです。 Passport は、柔軟で高度なモジュール構造をしており、任意の Express ベースまたは Resitify Web アプリケーションに、支障をきたすことなくドロップできます。 Passport では、包括的な認証手法セットにより、ユーザー名とパスワードを使用する認証、Facebook、Twitter などをサポートします。 Microsoft は Azure AD 用の認証手法を開発しました。 この記事では、モジュールをインストールした後に Azure AD `passport-azure-ad` プラグインを追加する方法について説明します。

## <a name="download"></a>ダウンロード
このチュートリアルのコードは、 [GitHub](https://github.com/AzureADQuickStarts/AppModelv2-WebApp-OpenIDConnect-nodejs)で管理されています。 チュートリアルを実行するには、[アプリのスケルトン (.zip ファイル) をダウンロード](https://github.com/AzureADQuickStarts/AppModelv2-WebApp-OpenIDConnect-nodejs/archive/skeleton.zip)するか、次のようにスケルトンを複製します。

```git clone --branch skeleton https://github.com/AzureADQuickStarts/AppModelv2-WebApp-OpenIDConnect-nodejs.git```

また、このチュートリアルの終了時には、完成したアプリケーションを取得できます。

## <a name="1-register-an-app"></a>1: アプリを登録する
[apps.dev.microsoft.com](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList) で新しいアプリを作成するか、この[詳細な手順](active-directory-v2-app-registration.md)に従ってアプリを登録します。 以下を実行します。

* アプリに割り当てられた**アプリケーション ID** をコピーしておきます。 このチュートリアルで必要になります。
* アプリの **Web** プラットフォームを追加します。
* ポータルから **リダイレクト URI** をコピーしておきます。 既定の URI 値 `urn:ietf:wg:oauth:2.0:oob` を使用する必要があります。

## <a name="2-add-prerequisities-to-your-directory"></a>2: ディレクトリに前提条件を追加する
コマンド プロンプトで、ルート フォルダーに移動します (現在のディレクトリがこのディレクトリでない場合)。 次のコマンドを実行します。

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

さらに、クイック スタートのスケルトンで、`passport-azure-ad` を使用します。

* `npm install passport-azure-ad`

これにより、`passport-azure-ad` が使用するライブラリがインストールされます。

## <a name="3-set-up-your-app-to-use-the-passport-node-js-strategy"></a>3: passport-node-js 戦略を使用するようにアプリを設定する
OpenID Connect 認証プロトコルを使用するように、Express ミドルウェアを設定します。 Passport は、サインイン要求とサインアウト要求の発行、ユーザー セッションの管理、ユーザーに関する情報の取得などを行うために使用します。

1.  プロジェクトのルートにある Config.js ファイルを開きます。 `exports.creds` セクションで、アプリケーションの構成値を入力します。
  
  * `clientID`: Azure Portal のアプリに割り当てられた**アプリケーション ID** です。
  * `returnURL`: ポータルで入力した**リダイレクト URI** です。
  * `clientSecret`: ポータルで生成したシークレットです。

2.  プロジェクトのルートで App.js ファイルを開きます。 `passport-azure-ad` に付属する OIDCStrategy 戦略を呼び出すには、次の呼び出しを追加します。

  ```JavaScript
  var OIDCStrategy = require('passport-azure-ad').OIDCStrategy;

  // Add some logging
  var log = bunyan.createLogger({
      name: 'Microsoft OIDC Example Web Application'
  });
  ```

3.  サインイン要求を処理するには、今参照した戦略を使用します。

  ```JavaScript
  // Use the OIDCStrategy within Passport (section 2)
  //
  //   Strategies in Passport require a `validate` function. The function accepts
  //   credentials (in this case, an OpenID identifier), and invokes a callback
  //   with a user object.
  passport.use( new OIDCStrategy({
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
      // Asynchronous verification, for effect...
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

Passport は、Twitter や Facebook などのすべての戦略に、同様のパターンを使用します。 すべての戦略記述者がこのパターンに従います。 パラメーターとして token と `done` を使用する `function()` の戦略を渡します。 すべての作業が終わった後で戦略が返されます。 再度要求しなくて済むように、ユーザーを格納し、トークンを保存します。

  > [!IMPORTANT]
  > 上記のコードでは、サーバーに対して認証可能なすべてのユーザーを受け入れます。 これは、自動登録と呼ばれます。 運用サーバーでは、指定された登録プロセスを先に実行していないユーザーにはアクセスを許可しないように設定できます。 これは、コンシューマー アプリで通常見られるパターンです。 Facebook での登録を許可するものの、その後で追加情報の入力を求めるアプリもあります。 このチュートリアルでコマンドライン プログラムを使用していなければ、返されるトークン オブジェクトからメールを抽出できます。 その後、ユーザーに追加情報の入力を要求できます。 これはテスト サーバーなので、ユーザーをメモリ内データベースに直接追加します。
  > 
  > 

4.  Passport では、サインインしているユーザーの追跡に使用するメソッドが必要なため、これを追加します。 これには、ユーザーの情報のシリアル化と逆シリアル化が含まれます。

  ```JavaScript

  // Passport session setup (section 2)

  //   To support persistent login sessions, Passport needs to be able to
  //   serialize users into, and deserialize users out of, the session. Typically,
  //   this is as simple as storing the user ID when serializing, and finding
  //   the user by ID when deserializing.
  passport.serializeUser(function(user, done) {
    done(null, user.email);
  });

  passport.deserializeUser(function(id, done) {
    findByEmail(id, function (err, user) {
      done(err, user);
    });
  });

  // Array to hold signed-in users
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

5.  Express エンジンを読み込むコードを追加します。 Express が提供する既定の /views と /routes のパターンを使用します。

  ```JavaScript

  // Set up Express (section 2)

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

6.  実際のサインイン要求を `passport-azure-ad` エンジンに渡す POST ルートを追加します。

  ```JavaScript

  // Auth routes (section 3)

  // GET /auth/openid
  //   Use passport.authenticate() as route middleware to authenticate the
  //   request. The first step in OpenID authentication involves redirecting
  //   the user to the user's OpenID provider. After authenticating, the OpenID
  //   provider redirects the user back to this application at
  //   /auth/openid/return.

  app.get('/auth/openid',
    passport.authenticate('azuread-openidconnect', { failureRedirect: '/login' }),
    function(req, res) {
      log.info('Authentication was called in the sample');
      res.redirect('/');
    });

  // GET /auth/openid/return
  //   Use passport.authenticate() as route middleware to authenticate the
  //   request. If authentication fails, the user is redirected back to the
  //   sign-in page. Otherwise, the primary route function is called.
  //   In this example, it redirects the user to the home page.
  app.get('/auth/openid/return',
    passport.authenticate('azuread-openidconnect', { failureRedirect: '/login' }),
    function(req, res) {

      res.redirect('/');
    });

  // POST /auth/openid/return
  //   Use passport.authenticate() as route middleware to authenticate the
  //   request. If authentication fails, the user is redirected back to the
  //   sign-in page. Otherwise, the primary route function is called. 
  //   In this example, it redirects the user to the home page.

  app.post('/auth/openid/return',
    passport.authenticate('azuread-openidconnect', { failureRedirect: '/login' }),
    function(req, res) {

      res.redirect('/');
    });
  ```

## <a name="4-use-passport-to-issue-sign-in-and-sign-out-requests-to-azure-ad"></a>4: Passport を使用して、サインイン要求とサインアウト要求を Azure AD に発行する
ここまでで、OpenID Connect 認証プロトコルを使用して v2.0 エンドポイントと通信するようにアプリが設定されました。 `passport-azure-ad` の戦略は、認証メッセージの作成、Azure AD から受け取ったトークンの検証、ユーザー セッションの維持のためのすべての処理を実行します。 残りの作業は、サインインとサインアウトの方法をユーザーに提示することと、サインインしているユーザーに関する追加情報を収集することです。

1.  **既定**のメソッド、**login** メソッド、**account** メソッド、**logout** メソッドを App.js ファイルに追加します。

  ```JavaScript

  //Routes (section 4)

  app.get('/', function(req, res){
    res.render('index', { user: req.user });
  });

  app.get('/account', ensureAuthenticated, function(req, res){
    res.render('account', { user: req.user });
  });

  app.get('/login',
    passport.authenticate('azuread-openidconnect', { failureRedirect: '/login' }),
    function(req, res) {
      log.info('Login was called in the sample');
      res.redirect('/');
  });

  app.get('/logout', function(req, res){
    req.logout();
    res.redirect('/');
  });

  ```

  これらについて詳しく説明します。
    
    * `/` ルートは、index.ejs ビューにリダイレクトします。 次に、要求内のユーザーを渡します (存在する場合)。
    * `/account` ルートはまず、"*認証されていることを確認します*" (これは次のコードで実装します)。 次に、要求内のユーザーを渡します。 これにより、ユーザーに関する詳細情報を得ることができます。
    * `/login` ルートは、`passport-azuread` から `azuread-openidconnect` 認証子を呼び出します。 これが成功しなかった場合は、ユーザーを `/login` にリダイレクトして戻します。
    * `/logout` ルートは、logout.ejs ビュー (およびルート) を呼び出します。 これにより Cookie をクリアし、ユーザーを index.ejs に返します。

2.  上記の `/account` で使用した **EnsureAuthenticated** メソッドを追加します。

  ```JavaScript

  // Route middleware to ensure the user is authenticated (section 4)

  //   Use this route middleware on any resource that needs to be protected. If
  //   the request is authenticated (typically via a persistent login session),
  //   the request proceeds. Otherwise, the user is redirected to the
  //   sign-in page.
  function ensureAuthenticated(req, res, next) {
    if (req.isAuthenticated()) { return next(); }
    res.redirect('/login')
  }

  ```

3.  App.js でサーバーを作成します。

  ```JavaScript

  app.listen(3000);

  ```


## <a name="5-create-the-views-and-routes-in-express-that-you-show-your-user-on-the-website"></a>5: Web サイトにユーザーを表示する Express のビューとルートを作成する
ユーザーに情報を表示するルートとビューを追加します。 これらのルートとビューは、作成した `/logout` ルートと `/login` ルートの処理も行います。

1. ルート ディレクトリに `/routes/index.js` ルートを作成します。

  ```JavaScript

  /*
  * GET home page.
  */

  exports.index = function(req, res){
    res.render('index', { title: 'Express' });
  };
  ```

2.  ルート ディレクトリに `/routes/user.js` ルートを作成します。

  ```JavaScript

  /*
  * GET users listing.
  */

  exports.list = function(req, res){
    res.send("respond with a resource");
  };
  ```

  `/routes/index.js` と `/routes/user.js` は、要求 (存在する場合はユーザーも) をビューに渡すだけの単純なルートです。

3.  ルート ディレクトリに `/views/index.ejs` ビューを作成します。 このページは **login** メソッドと **logout** メソッドを呼び出します。 また `/views/index.ejs` ビューを使用して、アカウント情報を取得します。 要求でユーザーを渡すときに、条件付きの `if (!user)` を使用できます。 これは、サインインしているユーザーが存在していることの証拠です。

  ```JavaScript
  <% if (!user) { %>
      <h2>Welcome! Please sign in.</h2>
      <a href="/login">Sign in</a>
  <% } else { %>
      <h2>Hello, <%= user.displayName %>.</h2>
      <a href="/account">Account info</a></br>
      <a href="/logout">Sign out</a>
  <% } %>
  ```

4.  ルート ディレクトリに `/views/account.ejs` ビューを作成します。 `/views/account.ejs` ビューでは、`passport-azuread` によってユーザー要求に配置された追加情報を表示できます。

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
  <p>Full Claimes</p>
  <%- JSON.stringify(user) %>
  <p></p>
  <a href="/logout">Sign out</a>
  <% } %>
  ```

5.  レイアウトを追加します。 ルート ディレクトリに `/views/layout.ejs` ビューを作成します。

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
              <a href="/login">Sign in</a>
              </p>
          <% } else { %>
              <p>
              <a href="/">Home</a> |
              <a href="/account">Account</a> |
              <a href="/logout">Sign out</a>
              </p>
          <% } %>
          <%- body %>
      </body>
  </html>
  ```

6.  アプリをビルドして実行するには、`node app.js` を実行します。 次に、`http://localhost:3000` に移動します。

7.  個人の Microsoft アカウントまたは職場/学校アカウントを使用してサインインします。 ユーザーの ID が /account リストに反映されることに注意してください。 

これで、業界標準のプロトコルを使用して Web アプリが保護されました。 ユーザーの個人アカウントや職場/学校アカウントを使用して、アプリでユーザーを認証できます。

## <a name="next-steps"></a>次のステップ
参考のため、完成済みサンプル (構成値を除く) が [.zip ファイル](https://github.com/AzureADQuickStarts/AppModelv2-WebApp-OpenIDConnect-nodejs/archive/complete.zip)で提供されています。 GitHub から複製することもできます。

```git clone --branch complete https://github.com/AzureADQuickStarts/AppModelv2-WebApp-OpenIDConnect-nodejs.git```

次はさらに高度なトピックに進むことができます。 以下のトピックを参照してください。

[v2.0 エンドポイントを使用して Node.js Web API をセキュリティで保護する](active-directory-v2-devquickstarts-node-api.md)

次にその他のリソースを示します。

* [Azure AD v2.0 開発者ガイド](active-directory-appmodel-v2-overview.md)
* [Stack Overflow "azure-Active Directory" タグ](http://stackoverflow.com/questions/tagged/azure-active-directory)

### <a name="get-security-updates-for-our-products"></a>Microsoft 製品のセキュリティ更新プログラムの取得
サインアップして、セキュリティの問題が発生したときに通知を受け取ることをお勧めします。 [マイクロソフト テクニカル セキュリティ通知](https://technet.microsoft.com/security/dd252948)のページで、セキュリティ アドバイザリ アラートに登録してください。

