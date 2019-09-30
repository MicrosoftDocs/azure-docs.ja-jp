---
title: クイック スタート:Azure Active Directory による Web API のセキュリティ保護
description: 認証のために Azure AD と連携する Node.js REST Web API を構築する方法について説明します。
services: active-directory
documentationcenter: nodejs
author: rwike77
manager: CelesteDG
ms.assetid: 7654ab4c-4489-4ea5-aba9-d7cdc256e42a
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: javascript
ms.topic: quickstart
ms.date: 09/24/2018
ms.author: ryanwi
ms.custom: seo-javascript-september2019
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7307afb2ce9e10326c42f96733d3f80979c4a4b3
ms.sourcegitcommit: ca359c0c2dd7a0229f73ba11a690e3384d198f40
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/17/2019
ms.locfileid: "71056534"
---
# <a name="quickstart-secure-a-web-api-with-azure-active-directory"></a>クイック スタート:Azure Active Directory による Web API のセキュリティ保護

[!INCLUDE [active-directory-develop-applies-v1](../../../includes/active-directory-develop-applies-v1.md)]

このクイック スタートでは、Azure Active Directory (Azure AD) との通信を処理する [passport-azure-ad](https://github.com/AzureAD/passport-azure-ad) モジュールを使用して、[Restify](http://restify.com/) API エンドポイントを [Passport](http://passportjs.org/) でセキュリティ保護する方法について説明します。

このクイック スタートでは、API エンドポイントのセキュリティ保護に関する事柄を取り上げます。 サインインと認証トークンの保持に関する事柄はここでは取り上げていません。これらはクライアント アプリケーションの役割です。 クライアントの実装に関する詳細については、「[Azure AD を使用した Node.js Web アプリへのサインインおよびサインアウト](quickstart-v1-openid-connect-code.md)」をご覧ください。

この記事に関連する完全なコード サンプルは、[GitHub](https://github.com/Azure-Samples/active-directory-node-webapi-basic) で入手できます。

## <a name="prerequisites"></a>前提条件

最初に、以下の前提条件を完了します。

### <a name="create-the-sample-project"></a>サンプル プロジェクトを作成する

サーバー アプリケーションには、Azure AD に渡されるアカウント情報のほかに、Restify と Passport をサポートするためのいくつかのパッケージの依存関係が必要です。

まず、次のコードを `package.json` という名前のファイルにコピーします。

```Shell
{
  "name": "active-directory-webapi-nodejs",
  "version": "0.0.1",
  "scripts": {
    "start": "node app.js"
  },
  "dependencies": {
    "passport": "0.4.0",
    "passport-azure-ad": "4.0.0",
    "restify": "7.7.0"
  }
}
```

`package.json` が作成されたら、コマンド プロンプトで `npm install` を実行して、パッケージの依存関係をインストールします。

#### <a name="configure-the-project-to-use-active-directory"></a>Active Directory を使用するようにプロジェクトを構成する

アプリケーションの構成を開始するために、Azure CLI から取得できるアカウント固有の値がいくつかあります。 CLI で作業を開始するには Azure Cloud Shell を使用するのが最も簡単です。

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Cloud Shell で次のコマンドを入力します。

```azurecli-interactive
az ad app create --display-name node-aad-demo --homepage http://localhost --identifier-uris http://node-aad-demo
```

`create` コマンドの[引数](/cli/azure/ad/app?view=azure-cli-latest#az-ad-app-create)には、次が含まれます。

| 引数  | 説明 |
|---------|---------|
|`display-name` | 登録のフレンドリ名 |
|`homepage` | ユーザーがサインインしてアプリケーションを使用する URL |
|`identifier-uris` | Azure AD がこのアプリケーションで使用できる、スペースで区切られた一意の URI |

Azure Active Directory に接続する前に、次の情報が必要です。

| 名前  | 説明 | 構成ファイルの変数名 |
| ------------- | ------------- | ------------- |
| テナント名  | 認証に使用する[テナント名](quickstart-create-new-tenant.md) | `tenantName`  |
| クライアント ID  | クライアント ID は、AAD の _アプリケーション ID_ に使用される OAuth の用語です。 |  `clientID`  |

Azure Cloud Shell での登録の応答から、`appId` の値をコピーして `config.js` という名前の新しいファイルを作成します。 次に、次のコードを追加して、値をかっこで囲まれたトークンに置き換えます。

```JavaScript
const tenantName    = //<YOUR_TENANT_NAME>;
const clientID      = //<YOUR_APP_ID_FROM_CLOUD_SHELL>;
const serverPort    = 3000;

module.exports.serverPort = serverPort;

module.exports.credentials = {
  identityMetadata: `https://login.microsoftonline.com/${tenantName}.onmicrosoft.com/.well-known/openid-configuration`, 
  clientID: clientID
};
```

それぞれの構成設定の詳細については、[passport-azure-ad](https://github.com/AzureAD/passport-azure-ad#5-usage) モジュールのドキュメントをご覧ください。

### <a name="implement-the-server"></a>サーバーの実装

[passport-azure-ad](https://github.com/AzureAD/passport-azure-ad#5-usage) モジュールでは、2 つの認証方式がサポートされています。それは、[OIDC](https://github.com/AzureAD/passport-azure-ad#51-oidcstrategy) と[ベアラー](https://github.com/AzureAD/passport-azure-ad#52-bearerstrategy)です。 この記事で実装されるサーバーでは、API エンドポイントの保護にベアラー方式を使用しています。

### <a name="step-1-import-dependencies"></a>手順 1:依存関係のインポート

`app.js` という名前の新しいファイルを作成し、次のテキストを貼り付けます。

```JavaScript
const
      restify = require('restify')
    , restifyPlugins = require ('restify').plugins
    , passport = require('passport')
    , BearerStrategy = require('passport-azure-ad').BearerStrategy
    , config = require('./config')
    , authenticatedUserTokens = []
    , serverPort = process.env.PORT || config.serverPort
;
```

このセクションのコードでは、

- Restify サーバーを設定するために `restify` モジュールと plugins モジュールが参照されます。
- `passport` モジュールと `passport-azure-ad` モジュールが、Azure AD との通信を担当します。
- 変数 `config` は、前の手順で作成した `config.js` ファイルの値で初期化されます。
- ユーザー トークンがセキュリティで保護されたエンドポイントに渡されるときに、これらを保存するために `authenticatedUserTokens` に配列が作成されます。
- `serverPort` は、プロセスの環境のポートまたは構成ファイルのいずれかから定義されます。

### <a name="step-2-instantiate-an-authentication-strategy"></a>手順 2:認証方式のインスタンス化

エンドポイントをセキュリティで保護するには、現在の要求が認証されたユーザーからのものかどうかを判定する方式を指定する必要があります。 ここで、変数 `authenticatonStrategy` は `passport-azure-ad` `BearerStrategy` クラスのインスタンスです。 次のコードを `require` ステートメントの後ろに追加します。

```JavaScript
const authenticationStrategy = new BearerStrategy(config.credentials, (token, done) => {
    let currentUser = null;

    let userToken = authenticatedUserTokens.find((user) => {
        currentUser = user;
        user.sub === token.sub;
    });

    if(!userToken) {
        authenticatedUserTokens.push(token);
    }

    return done(null, currentUser, token);
});
```

この実装では、配列 `authenticatedUserTokens` に認証トークンを追加する (まだ存在していない場合) ことによって、自動登録を使用しています。

方式の新しいインスタンスが作成されたら、`use` メソッドを使用してこれを Passport に渡す必要があります。 Passport でこの方式を使用するように、次のコードを `app.js` に追加します。

```JavaScript
passport.use(authenticationStrategy);
```

### <a name="step-3-server-configuration"></a>手順 3:サーバー構成

認証方式を定義したら、次は Restify サーバーを設定します。いくつかの基本的な設定とともに、セキュリティのために Passport を使用するように設定します。

```JavaScript
const server = restify.createServer({ name: 'Azure Active Directory with Node.js Demo' });
server.use(restifyPlugins.authorizationParser());
server.use(passport.initialize());
server.use(passport.session());
```
このサーバーは初期化されて Authorization ヘッダーを解析するように構成された後に、Passport を使用するように設定されます。

### <a name="step-4-define-routes"></a>手順 4:ルートの定義

次に、ルートを定義し、Azure AD でどれをセキュリティ保護するかを決定します。 このプロジェクトには、ルート レベルがオープンで、`/api` ルートが認証を要求するように設定されている 2 つのルートが含まれています。

`app.js` に、ルート レベルのルートの次のコードを追加します。

```JavaScript
server.get('/', (req, res, next) => {
    res.send(200, 'Try: curl -isS -X GET http://127.0.0.1:3000/api');
    next();
});
```

ルートのルートは、ルート経由ですべての要求を許可し、`/api` ルートをテストするコマンドを含むメッセージを返します。 これに対し、`/api` ルートは [`passport.authenticate`](http://passportjs.org/docs/authenticate) を使用してロックダウンされます。 次のコードをルートのルートの後ろに追加します。

```JavaScript
server.get('/api', passport.authenticate('oauth-bearer', { session: false }), (req, res, next) => {
    res.json({ message: 'response from API endpoint' });
    return next();
});
```

この構成では、`/api` に対するベアラー トークンのアクセスを含む、認証された要求のみが許可されます。 `session: false` のオプションは、要求ごとにトークンを API に渡す必要があるセッションを無効にするために使用します。

最後に、`listen` メソッドを呼び出して、構成されたポートでリッスンするようにサーバーを設定します。

```JavaScript
server.listen(serverPort);
```

## <a name="run-the-sample"></a>サンプルを実行する

サーバーが実装されたので、コマンド プロンプトを開き、以下を入力してサーバーを起動します。

```shell
npm start
```

サーバーが起動したら、サーバーに要求を送信して結果をテストします。 ルートのルートからの応答を実際に試してみるには、bash シェルを開いて次のコードを入力します。

```shell
curl -isS -X GET http://127.0.0.1:3000/
```

サーバーが正しく構成されている場合、応答は次のようになります。

```shell
HTTP/1.1 200 OK
Server: Azure Active Directory with Node.js Demo
Content-Type: application/json
Content-Length: 49
Date: Tue, 10 Oct 2017 18:35:13 GMT
Connection: keep-alive

Try: curl -isS -X GET http://127.0.0.1:3000/api
```

次に、bash シェルに次のコマンドを入力して、認証を必要とするルートをテストします。

```shell
curl -isS -X GET http://127.0.0.1:3000/api
```

サーバーが正しく構成されている場合、サーバーは次のように `Unauthorized` のステータスを返します。

```shell
HTTP/1.1 401 Unauthorized
Server: Azure Active Directory with Node.js Demo
WWW-Authenticate: token is not found
Date: Tue, 10 Oct 2017 16:22:03 GMT
Connection: keep-alive
Content-Length: 12

Unauthorized
```

セキュリティで保護された API が作成されたので、API に認証トークンを渡すことができるクライアントを実装できます。

## <a name="next-steps"></a>次の手順

* サーバーに接続するためにサインイン、サインアウト、およびトークンの管理を処理するクライアント側の実装を行う必要があります。 コードに基づく例については、[iOS](https://github.com/MSOpenTech/azure-activedirectory-library-for-ios) と [Android](https://github.com/MSOpenTech/azure-activedirectory-library-for-android) のクライアント アプリケーションをご覧ください。
* ステップバイステップのチュートリアルについては、[Azure AD を使用した Node.js Web アプリへのサインインおよびサインアウト](quickstart-v1-openid-connect-code.md)を参照してください。
