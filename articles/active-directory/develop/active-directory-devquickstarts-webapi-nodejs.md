---
title: "Azure AD Node.js の概要 | Microsoft Docs"
description: "認証のために Azure AD と連携する Node.js REST Web API を構築する方法。"
services: active-directory
documentationcenter: nodejs
author: xerners
manager: mbaldwin
editor: 
ms.assetid: 7654ab4c-4489-4ea5-aba9-d7cdc256e42a
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: javascript
ms.topic: article
ms.date: 01/07/2017
ms.author: brandwe
translationtype: Human Translation
ms.sourcegitcommit: bb1ca3189e6c39b46eaa5151bf0c74dbf4a35228
ms.openlocfilehash: 707bb2b7cd9796e0e05234aa08002bbc1820f871
ms.lasthandoff: 03/18/2017


---
# <a name="get-started-with-web-apis-for-nodejs"></a>Node.js の Web API の概要
[!INCLUDE [active-directory-devguide](../../../includes/active-directory-devguide.md)]

*Passport* は Node.js 用の認証ミドルウェアです。 Passport は、柔軟で高度なモジュール構造をしており、任意の Express ベースまたは Resitify Web アプリケーションに、問題なく組み込むことができます。 包括的な認証手法セットにより、ユーザー名とパスワード、Facebook、Twitter などでの認証をサポートします。 Microsoft Azure Active Directory (Azure AD) 用の手法も開発しました。 ここでは、このモジュールをインストールした後、Microsoft Azure Active Directory `passport-azure-ad` プラグインを追加します。

そのためには、次の手順を実行する必要があります。

1. アプリケーションを Azure AD に登録する。
2. Passport の `passport-azure-ad` プラグインを使用するようにアプリをセットアップする。
3. To Do List Web API を呼び出すように、クライアント アプリケーションを構成する。

このチュートリアルのコードは、 [GitHub](https://github.com/Azure-Samples/active-directory-node-webapi)で管理されています。

> [!NOTE]
> この記事では、Azure AD B2C でサインイン、サインアップ、またはプロファイルの管理を実装する方法については説明しません。 ユーザーが既に認証された後での Web API の呼び出しに焦点を合わせています。  Azure Active Directory の基本について、まず、[Azure Active Directory と統合する方法](active-directory-how-to-integrate.md)に関するページを参照することをお勧めします。
>
>

この実稼働するサンプルに関連するすべてのソース コードが、MIT ライセンスの下で、GitHub で提供されています。自由にクローン (できれば、フォーク) 操作を行って、フィードバックとプル リクエストを行ってください。

## <a name="about-nodejs-modules"></a>Node.js モジュールとは
このチュートリアルでは、Node.js モジュールを使用します。 モジュールは、アプリケーションに対して特定の機能を提供する読み込み可能な JavaScript パッケージです。 通常は、Node.js (NPM コマンド ライン ツール) を使用して、NPM インストール ディレクトリにモジュールをインストールします。 しかし、HTTP モジュールなどの一部のモジュールは、コア Node.js パッケージに含まれています。

インストールされたモジュールは、Node.js インストール ディレクトリのルートにある **node_modules** ディレクトリに保存されます。 **node_modules** ディレクトリの各モジュールは、それが依存関係を持つすべてのモジュールを含む独自の **node_modules** ディレクトリを維持します。 また、各必須モジュールには **node_modules** ディレクトリがあります。 この再帰的なディレクトリ構造は、依存関係チェーンを表します。

この依存関係チェーン構造のため、アプリケーションのフットプリントは大きくなります。 しかし、すべての依存関係が満たされることと、開発時に使用されるモジュールのバージョンが運用時にも使用されることが保証されます。 このため、運用アプリの動作がより期待どおりになり、ユーザーに悪影響を及ぼすバージョン管理の問題を防ぐことができます。

## <a name="step-1-register-an-azure-ad-tenant"></a>手順 1. Azure AD テナントを登録する
このサンプルを使用するには、Azure Active Directory テナントが必要です。 テナントについて不明な場合、または取得方法が不明な場合は、「[Azure Active Directory テナントを取得する方法](active-directory-howto-tenant.md)」を参照してください。

## <a name="step-2-create-an-application"></a>手順 2: アプリケーションの作成
次に、ディレクトリにアプリを作成します。このディレクトリによって、アプリと安全に通信するために必要な情報を Azure AD に提供します。  ここでは、クライアント アプリと Web API の両方が単一の**アプリケーション ID** で表されます。これは、クライアント アプリと Web API が 1 つの論理アプリを構成するためです。  アプリを作成するには、 [こちらの手順](active-directory-how-applications-are-added.md)に従ってください。 基幹業務アプリケーションを作成する場合、[この追加手順が役に立つ場合があります](../active-directory-applications-guiding-developers-for-lob-applications.md)。

アプリケーションを作成するには:

1. [Azure ポータル](https://portal.azure.com)にサインインします。

2. 上部のメニューで、自分のアカウントを選択します。 次に、**[ディレクトリ]** の一覧から、アプリケーションを登録する Active Directory テナントを選択します。

3. 左側のメニューで **[その他のサービス]** を選択し、**[Azure Active Directory]** を選択します。

4. **[アプリの登録]** を選択し、**[追加]** を選択します。

5. 画面の指示に従い、**Web アプリケーションまたは WebAPI** (またはその両方) を作成します。

      * アプリケーションの **[名前]** には、エンド ユーザーがアプリケーションの機能を把握できるような名前を設定します。

      * **[サインオン URL]** は、アプリのベース URL です。  サンプル コードの既定の URL は、`https://localhost:8080` です。

6. 登録が完了すると、Azure AD によってアプリに一意のアプリケーション ID が割り当てられます。 この値は次のセクションで必要になるので、アプリケーション ページからコピーします。

7. アプリケーションの **[設定]**  ->  **[プロパティ]** ページで、アプリ ID URI を更新します。 **[アプリケーション ID/URI]** は、アプリケーションの一意識別子です。 形式は、`https://<tenant-domain>/<app-name>` (たとえば `https://contoso.onmicrosoft.com/my-first-aad-app`) です。

8. **[設定]** ページでアプリケーション用の**キー**を作成し、どこかにコピーしておきます。 すぐ後で必要になります。

## <a name="step-3-download-nodejs-for-your-platform"></a>手順 3. プラットフォーム用の Node.js をダウンロードする
このサンプルを正常に使用するには、Node.js の実稼働するインストール環境が必要になります。

Node.js を [http://nodejs.org](http://nodejs.org)からインストールします。

## <a name="step-4-install-mongodb-on-your-platform"></a>手順 4. プラットフォームに MongoDB をインストールする
このサンプルを正常に使用するには、MongoDB の実稼働するインストール環境が必要になります。 MongoDB を使用して、REST API がサーバー インスタンス間で持続されるようにします。

MongoDB を [http://mongodb.org](http://www.mongodb.org)からインストールします。

> [!NOTE]
> このチュートリアルでは、MongoDB の既定のインストール環境とサーバー エンドポイント (チュートリアルの記述時点では mongodb://localhost) が使用されることを想定しています。
>
>

## <a name="step-5-install-the-restify-modules-in-your-web-api"></a>手順 5. Web API に Restify モジュールをインストールする
Restify を使用して REST API を構築します。 Restify は、Express から派生した、最小限の柔軟な Node.js アプリケーション フレームワークです。 Connect 上に REST API を構築するための堅牢な機能のセットが用意されています。

### <a name="install-restify"></a>Restify をインストールする
1. コマンド ラインで、**azuread** ディレクトリに移動します。 **azuread** ディレクトリが存在しない場合は、作成します。

        `cd azuread - or- mkdir azuread; cd azuread`

2. 次のコマンドを入力します。

    `npm install restify`

    このコマンドにより、Restify がインストールされます。

#### <a name="did-you-get-an-error"></a>エラーが発生した場合
一部のオペレーティング システムで NPM を使用すると、"**Error: EPERM, chmod '/usr/local/bin/..'**" というエラーと、 管理者としてアカウントを実行してみるようにという指示が表示されることがあります。 このような場合は、sudo コマンドを使用して、より高い権限レベルで NPM を実行します。

#### <a name="did-you-get-an-error-regarding-dtrace"></a>DTRACE に関するエラーが表示された場合
Restify をインストールするときに、次のようなエラーが表示されることがあります。

```Shell
clang: error: no such file or directory: 'HD/azuread/node_modules/restify/node_modules/dtrace-provider/libusdt'
make: *** [Release/DTraceProviderBindings.node] Error 1
gyp ERR! build error
gyp ERR! stack Error: `make` failed with exit code: 2
gyp ERR! stack     at ChildProcess.onExit (/usr/local/lib/node_modules/npm/node_modules/node-gyp/lib/build.js:267:23)
gyp ERR! stack     at ChildProcess.EventEmitter.emit (events.js:98:17)
gyp ERR! stack     at Process.ChildProcess._handle.onexit (child_process.js:789:12)
gyp ERR! System Darwin 13.1.0
gyp ERR! command "node" "/usr/local/lib/node_modules/npm/node_modules/node-gyp/bin/node-gyp.js" "rebuild"
gyp ERR! cwd /Volumes/Development HD/azuread/node_modules/restify/node_modules/dtrace-provider
gyp ERR! node -v v0.10.11
gyp ERR! node-gyp -v v0.10.0
gyp ERR! not ok
npm WARN optional dep failed, continuing dtrace-provider@0.2.8
```
Restify は、DTrace を使用して REST 呼び出しをトレースする強力なメカニズムを備えています。 ただし、多くのオペレーティング システムには DTrace がありません。 これらのエラーは無視してかまいません。

このコマンドの出力は次のようになります。

    restify@2.6.1 node_modules/restify
    ├── assert-plus@0.1.4
    ├── once@1.3.0
    ├── deep-equal@0.0.0
    ├── escape-regexp-component@1.0.2
    ├── qs@0.6.5
    ├── tunnel-agent@0.3.0
    ├── keep-alive-agent@0.0.1
    ├── lru-cache@2.3.1
    ├── node-uuid@1.4.0
    ├── negotiator@0.3.0
    ├── mime@1.2.11
    ├── semver@2.2.1
    ├── spdy@1.14.12
    ├── backoff@2.3.0
    ├── formidable@1.0.14
    ├── verror@1.3.6 (extsprintf@1.0.2)
    ├── csv@0.3.6
    ├── http-signature@0.10.0 (assert-plus@0.1.2, asn1@0.1.11, ctype@0.5.2)
    └── bunyan@0.22.0 (mv@0.0.5)


## <a name="step-6-install-passportjs-in-your-web-api"></a>手順 6. Passport.js を Web API にインストールする
[Passport](http://passportjs.org/) は Node.js 用の認証ミドルウェアです。 Passport は、柔軟で高度なモジュール構造をしており、任意の Express ベースまたは Resitify Web アプリケーションに、問題なく組み込むことができます。 包括的な認証手法セットにより、ユーザー名とパスワード、Facebook、Twitter などでの認証をサポートします。

Azure Active Directory 用の認証手法を開発しました。 このモジュールをインストールし、Azure Active Directory 認証手法プラグインを追加します。

1. コマンド ラインで、**azuread** ディレクトリに移動します。

2. Passport.js をインストールするには、次のコマンドを入力します。

    `npm install passport`

    コマンドの出力は次のようになります。

``
        passport@0.1.17 node_modules\passport
        ├── pause@0.0.1
        └── pkginfo@0.2.3
``

## <a name="step-7-add-passport-azure-ad-to-your-web-api"></a>手順 7. Passport-Azure-AD を Web API に追加する
次に、Azure Active Directory を Passport に接続する戦略のスイートである `passport-azure-ad` を使用して、OAuth 戦略を追加します。 この REST API の例では、このベアラー トークン用の戦略を使用します。

> [!NOTE]
> OAuth2 は、任意の既知のトークン タイプを発行できるフレームワークを提供しますが、よく使用されるのは一部のトークン タイプのみです。 ベアラー トークンは、エンドポイントを保護するために最もよく使用されているトークンです。 これが、OAuth2 で最も広く発行されている種類のトークンです。 多くの実装では、ベアラー トークンが、発行される唯一の種類のトークンであると想定されています。
>
>

コマンド ラインで、**azuread** ディレクトリに移動します。

次のコマンドを入力して、Passport.js `passport-azure-ad module` をインストールします。

`npm install passport-azure-ad`

コマンドの出力は次のようになります。


    passport-azure-ad@1.0.0 node_modules/passport-azure-ad
    ├── xtend@4.0.0
    ├── xmldom@0.1.19
    ├── passport-http-bearer@1.0.1 (passport-strategy@1.0.0)
    ├── underscore@1.8.3
    ├── async@1.3.0
    ├── jsonwebtoken@5.0.2
    ├── xml-crypto@0.5.27 (xpath.js@1.0.6)
    ├── ursa@0.8.5 (bindings@1.2.1, nan@1.8.4)
    ├── jws@3.0.0 (jwa@1.0.1, base64url@1.0.4)
    ├── request@2.58.0 (caseless@0.10.0, aws-sign2@0.5.0, forever-agent@0.6.1, stringstream@0.0.4, tunnel-agent@0.4.1, oauth-sign@0.8.0, isstream@0.1.2, extend@2.0.1, json-stringify-safe@5.0.1, node-uuid@1.4.3, qs@3.1.0, combined-stream@1.0.5, mime-types@2.0.14, form-data@1.0.0-rc1, http-signature@0.11.0, bl@0.9.4, tough-cookie@2.0.0, hawk@2.3.1, har-validator@1.8.0)
    └── xml2js@0.4.9 (sax@0.6.1, xmlbuilder@2.6.4)



## <a name="step-8-add-mongodb-modules-to-your-web-api"></a>手順 8. MongoDB モジュールを Web API に追加する
MongoDB をデータストアとして使用します。 そのため、モデルとスキーマの管理に広く使用されている、Mongoose と呼ばれるプラグインをインストールする必要があります。 さらに、MongoDB 用のデータベース ドライバー (これも MongoDB と呼ばれます) もインストールする必要があります。

 `npm install mongoose`

## <a name="step-9-install-additional-modules"></a>手順 9. その他のモジュールをインストールする
次に、その他の必須モジュールをインストールします。

1. コマンド ラインで、**azuread** フォルダーに移動します (現在のディレクトリがこのディレクトリではない場合)。

    `cd azuread`

2. 次のコマンドを入力して、以下のモジュールを **node_modules** ディレクトリにインストールします。

    * `npm install assert-plus`
    * `npm install bunyan`
    * `npm update`

## <a name="step-10-create-a-serverjs-with-your-dependencies"></a>手順 10. 依存関係を定義する server.js を作成する
server.js ファイルは、Web API サーバーのほとんどの機能を提供します。 ほとんどのコードは、このファイルに追加します。 運用環境では、ルートとコントローラーを分割するなどして、機能をより小さなファイルに分散することをお勧めします。 このデモでは、この機能用に server.js を使用します。

1. コマンド ラインで、**azuread** フォルダーに移動します (現在のディレクトリがこのディレクトリではない場合)。

    `cd azuread`

2. お気に入りのエディターを使用して `server.js` ファイルを作成し、次の情報を追加します。

    ```Javascript
        'use strict';

        /**
         * Module dependencies.
         */

        var fs = require('fs');
        var path = require('path');
        var util = require('util');
        var assert = require('assert-plus');
        var bunyan = require('bunyan');
        var getopt = require('posix-getopt');
        var mongoose = require('mongoose/');
        var restify = require('restify');
        var passport = require('passport');
      var BearerStrategy = require('passport-azure-ad').BearerStrategy;
    ```

3. ファイルを保存します。 この後すぐに、このファイルを使用します。

## <a name="step-11-create-a-config-file-to-store-your-azure-ad-settings"></a>手順 11. Azure AD 設定を格納するための構成ファイルを作成する
このコード ファイルは、構成パラメーターを Azure Active Directory ポータルから Passport.js に渡します。 これらの構成値は、チュートリアルの初期の手順で Web API をポータルに追加したときに作成されています。 ここでは、コードをコピーした後に、これらのパラメーターにどのような値を設定するかについて説明します。

1. コマンド ラインで、**azuread** フォルダーに移動します (現在のディレクトリがこのディレクトリではない場合)。

    `cd azuread`

2. お気に入りのエディターを使用して `config.js` ファイルを作成し、次の情報を追加します。

    ```Javascript
         exports.creds = {
             mongoose_auth_local: 'mongodb://localhost/tasklist', // Your mongo auth uri goes here
             clientID: 'your client ID',
             audience: 'your application URL',
            // you cannot have users from multiple tenants sign in to your server unless you use the common endpoint
          // example: https://login.microsoftonline.com/common/.well-known/openid-configuration
             identityMetadata: 'https://login.microsoftonline.com/<your tenant id>/.well-known/openid-configuration',
             validateIssuer: true, // if you have validation on, you cannot have users from multiple tenants sign in to your server
             passReqToCallback: false,
             loggingLevel: 'info' // valid are 'info', 'warn', 'error'. Error always goes to stderr in Unix.

         };
    ```
3. ファイルを保存します。

## <a name="step-12-add-configuration-values-to-your-serverjs-file"></a>手順 12. 構成値を server.js ファイルに追加する
これらの値は、アプリケーションで作成した .config ファイルから読み込む必要があります。 そうするために、アプリケーションで必要なリソースとして .config ファイルを追加します。 次に、config.js ドキュメント内の変数に一致するようにグローバル変数を設定します。

1. コマンド ラインで、**azuread** フォルダーに移動します (現在のディレクトリがこのディレクトリではない場合)。

    `cd azuread`

2. お気に入りのエディターで `server.js` ファイルを開き、次の情報を追加します。

    ```Javascript
    var config = require('./config');
    ```
3. 次に、`server.js` に新しいセクションを追加して、次のコードを記述します。

    ```Javascript
    var options = {
        // The URL of the metadata document for your app. We will put the keys for token validation from the URL found in the jwks_uri tag of the in the metadata.
        identityMetadata: config.creds.identityMetadata,
        clientID: config.creds.clientID,
        validateIssuer: config.creds.validateIssuer,
        audience: config.creds.audience,
        passReqToCallback: config.creds.passReqToCallback,
        loggingLevel: config.creds.loggingLevel

    };

    // Array to hold logged in users and the current logged in user (owner).
    var users = [];
    var owner = null;

    // Our logger.
    var log = bunyan.createLogger({
        name: 'Azure Active Directory Bearer Sample',
             streams: [
            {
                stream: process.stderr,
                level: "error",
                name: "error"
            },
            {
                stream: process.stdout,
                level: "warn",
                name: "console"
            }, ]
    });

      // If the logging level is specified, switch to it.
      if (config.creds.loggingLevel) { log.levels("console", config.creds.loggingLevel); }

    // MongoDB setup.
    // Set up some configuration.
    var serverPort = process.env.PORT || 8080;
    var serverURI = (process.env.PORT) ? config.creds.mongoose_auth_mongohq : config.creds.mongoose_auth_local;
    ```

4. ファイルを保存します。

## <a name="step-13-add-the-mongodb-model-and-schema-information-by-using-mongoose"></a>手順 13. Mongoose を使用して MongoDB のモデルとスキーマ情報を追加する
これら 3 つのファイルを REST API サービスに組み込むと、これまでの準備の効果が得られるようになります。

このチュートリアルでは、手順 4. で説明したように、MongoDB を使用してタスクを格納します。

手順 11. で作成した `config.js` ファイルでは、データベースを `tasklist` と呼んでいました。それが、**mogoose_auth_local** 接続 URL の末尾で指定した名前であったためです。 MongoDB では、事前にこのデータベースを作成する必要はありません。 代わりに、サーバー アプリケーションを初めて実行するときに、MongoDB によって作成されます (データベースがまだ存在しない場合)。

これで、どの MongoDB データベースを使用するかをサーバーに通知したことになります。次に、サーバーのタスク用のモデルとスキーマを作成する追加コードを記述する必要があります。

### <a name="discussion-of-the-model"></a>モデルについて
スキーマ モデルは単純です。 必要に応じて拡張します。

NAME: タスクに割り当てられている人の名前。 **String**。

TASK: タスク自体。 **String**。

DATE: タスクの期限日。 **DATETIME**。

COMPLETED: タスクが完了したかどうか。 **BOOLEAN**。

### <a name="creating-the-schema-in-the-code"></a>スキーマをコードで作成する
1. コマンド ラインで、**azuread** フォルダーに移動します (現在のディレクトリがこのディレクトリではない場合)。

    `cd azuread`

2. お気に入りのエディターを使用して `server.js` ファイルを開き、次の情報を構成エントリの下に追加します。

    ```Javascript
    // Connect to MongoDB.
    global.db = mongoose.connect(serverURI);
    var Schema = mongoose.Schema;
    log.info('MongoDB Schema loaded');

    // Here we create a schema to store our tasks and users. It's a fairly simple schema for now.
    var TaskSchema = new Schema({
        owner: String,
        task: String,
        completed: Boolean,
        date: Date
    });

    // Use the schema to register a model.
    mongoose.model('Task', TaskSchema);
    var Task = mongoose.model('Task');
    ```
コードからわかるように、スキーマを先に作成します。 次に、**ルート**を定義するときにコード全体のデータを格納するために使用するモデル オブジェクトを作成します。

## <a name="step-14-add-our-routes-for-our-task-rest-api-server"></a>手順 14. タスク REST API サーバー用のルートを追加する
これで、操作対象のデータベース モデルが作成されたので、REST API サーバー用に使用するルートを追加します。

### <a name="about-routes-in-restify"></a>Restify でのルートについて
ルートは、Express スタックを使用する場合と同じように、Restify 内で動作します。 ルートは、クライアント アプリが呼び出すことが想定される URI を使用して定義されます。 通常、ルートは個別のファイルで定義されますが、 チュートリアルでの目的に沿って、すべてのルートを server.js ファイルに格納します。 運用環境では、各ルートを独自のファイルに格納することをお勧めします。

Restify ルートの典型的なパターンを次に示します。

```Javascript
function createObject(req, res, next) {

// Do work on object.

 _object.name = req.params.object; // passed value is in req.params under object

 ///...

return next(); // Keep the server going.
}

....

server.post('/service/:add/:object', createObject); // Calls createObject on routes that match this.

```


これは、最も基本的なレベルのパターンです。 Restify (と Express) では、アプリケーション タイプの定義、異なるエンドポイントにまたがる複雑なルーティングなどのより高度な機能を提供できます。 ここではチュートリアルの目的に沿って、ルートを単純なままにしておきます。

### <a name="add-default-routes-to-our-server"></a>既定のルートをサーバーに追加する
Create、Retrieve、Update、Delete の基本的な CRUD ルートを追加します。

1. コマンド ラインで、**azuread** フォルダーに移動します (現在のディレクトリがこのディレクトリではない場合)。

    `cd azuread`

2. お気に入りのエディターで `server.js` ファイルを開き、前に作成したデータベース エントリの下に次の情報を追加します。

```Javascript

/**
 *
 * APIs for our REST Task server.
 */

// Create a task.

function createTask(req, res, next) {

    // Restify currently has a bug which doesn't allow you to set default headers.
    // These headers comply with CORS and allow us to mongodbServer our response to any origin.

    res.header("Access-Control-Allow-Origin", "*");
    res.header("Access-Control-Allow-Headers", "X-Requested-With");

    // Create a new task model, fill it, and save it to Mongodb.
    var _task = new Task();

    if (!req.params.task) {
        req.log.warn('createTodo: missing task');
        next(new MissingTaskError());
        return;
    }

    _task.owner = owner;
    _task.task = req.params.task;
    _task.date = new Date();

    _task.save(function(err) {
        if (err) {
            req.log.warn(err, 'createTask: unable to save');
            next(err);
        } else {
            res.send(201, _task);

        }
    });

    return next();

}


// Delete a task by name.

function removeTask(req, res, next) {

    Task.remove({
        task: req.params.task,
        owner: owner
    }, function(err) {
        if (err) {
            req.log.warn(err,
                'removeTask: unable to delete %s',
                req.params.task);
            next(err);
        } else {
            log.info('Deleted task:', req.params.task);
            res.send(204);
            next();
        }
    });
}

// Delete all tasks.

function removeAll(req, res, next) {
    Task.remove();
    res.send(204);
    return next();
}


// Get a specific task based on name.

function getTask(req, res, next) {

    log.info('getTask was called for: ', owner);
    Task.find({
        owner: owner
    }, function(err, data) {
        if (err) {
            req.log.warn(err, 'get: unable to read %s', owner);
            next(err);
            return;
        }

        res.json(data);
    });

    return next();
}

/// Simple returns the list of TODOs that were loaded.

function listTasks(req, res, next) {
    // Restify currently has a bug which doesn't allow you to set default headers.
    // These headers comply with CORS and allow us to mongodbServer our response to any origin.

    res.header("Access-Control-Allow-Origin", "*");
    res.header("Access-Control-Allow-Headers", "X-Requested-With");

    log.info("listTasks was called for: ", owner);

    Task.find({
        owner: owner
    }).limit(20).sort('date').exec(function(err, data) {

        if (err) {
            return next(err);
        }

        if (data.length > 0) {
            log.info(data);
        }

        if (!data.length) {
            log.warn(err, "There is no tasks in the database. Did you initialize the database as stated in the README?");
        }

        if (!owner) {
            log.warn(err, "You did not pass an owner when listing tasks.");
        } else {

            res.json(data);

        }
    });

    return next();
}

```

### <a name="add-error-handling-in-our-apis"></a>エラー処理を API に追加する
```

///--- Errors for communicating something interesting back to the client.

function MissingTaskError() {
    restify.RestError.call(this, {
        statusCode: 409,
        restCode: 'MissingTask',
        message: '"task" is a required parameter',
        constructorOpt: MissingTaskError
    });

    this.name = 'MissingTaskError';
}
util.inherits(MissingTaskError, restify.RestError);


function TaskExistsError(owner) {
    assert.string(owner, 'owner');

    restify.RestError.call(this, {
        statusCode: 409,
        restCode: 'TaskExists',
        message: owner + ' already exists',
        constructorOpt: TaskExistsError
    });

    this.name = 'TaskExistsError';
}
util.inherits(TaskExistsError, restify.RestError);


function TaskNotFoundError(owner) {
    assert.string(owner, 'owner');

    restify.RestError.call(this, {
        statusCode: 404,
        restCode: 'TaskNotFound',
        message: owner + ' was not found',
        constructorOpt: TaskNotFoundError
    });

    this.name = 'TaskNotFoundError';
}

util.inherits(TaskNotFoundError, restify.RestError);
```


## <a name="step-15-create-your-server"></a>手順 15. サーバーを作成する
データベースを定義し、ルートを配置しました。 最後に、呼び出しを管理するサーバー インスタンスを追加します。

Restify (と Express) では、REST API サーバーに対してさまざまなカスタマイズを実行できますが、ここでもチュートリアルの目的に沿って、最も基本的なセットアップを使用します。

```Javascript
/**
 * Our server.
 */


var server = restify.createServer({
    name: "Azure Active Directroy TODO Server",
    version: "2.0.1"
});

// Ensure we don't drop data on uploads.
server.pre(restify.pre.pause());

// Clean up sloppy paths like //todo//////1//.
server.pre(restify.pre.sanitizePath());

// Handle annoying user agents (curl).
server.pre(restify.pre.userAgentConnection());

// Set a per request bunyan logger (with requestid filled in).
server.use(restify.requestLogger());

// Allow five requests per second by IP, and burst to 10.
server.use(restify.throttle({
    burst: 10,
    rate: 5,
    ip: true,
}));

// Use the common stuff you probably want.
server.use(restify.acceptParser(server.acceptable));
server.use(restify.dateParser());
server.use(restify.queryParser());
server.use(restify.gzipResponse());
server.use(restify.bodyParser({
    mapParams: true
})); // Allow for JSON mapping to REST.
```

## <a name="step-16-add-the-routes-to-the-server-without-authentication-for-now"></a>手順 16. サーバーにルートを追加する (まだ認証は行われません)
```Javascript
/// Now the real handlers. Here we just CRUD.
/**
/*
/* Each of these handlers is protected by our OIDCBearerStrategy by invoking 'oidc-bearer'.
/* In the pasport.authenticate() method. We set 'session: false' because REST is stateless and
/* we don't need to maintain session state. You can experiment with removing API protection
/* by removing the passport.authenticate() method as follows:
/*
/* server.get('/tasks', listTasks);
/*
**/
server.get('/tasks', listTasks);
server.get('/tasks', listTasks);
server.get('/tasks/:owner', getTask);
server.head('/tasks/:owner', getTask);
server.post('/tasks/:owner/:task', createTask);
server.post('/tasks', createTask);
server.del('/tasks/:owner/:task', removeTask);
server.del('/tasks/:owner', removeTask);
server.del('/tasks', removeTask);
server.del('/tasks', removeAll, function respond(req, res, next) {
res.send(204);
next();
});
// Register a default '/' handler.
server.get('/', function root(req, res, next) {
var routes = [
'GET /',
'POST /tasks/:owner/:task',
'POST /tasks (for JSON body)',
'GET /tasks',
'PUT /tasks/:owner',
'GET /tasks/:owner',
'DELETE /tasks/:owner/:task'
];
res.send(200, routes);
next();
});
server.listen(serverPort, function() {
var consoleMessage = '\n Microsoft Azure Active Directory Tutorial';
consoleMessage += '\n +++++++++++++++++++++++++++++++++++++++++++++++++++++';
consoleMessage += '\n %s server is listening at %s';
consoleMessage += '\n Open your browser to %s/tasks\n';
consoleMessage += '+++++++++++++++++++++++++++++++++++++++++++++++++++++ \n';
consoleMessage += '\n !!! why not try a $curl -isS %s | json to get some ideas? \n';
consoleMessage += '+++++++++++++++++++++++++++++++++++++++++++++++++++++ \n\n';
});
```

## <a name="step-17-run-the-server-before-adding-oauth-support"></a>手順 17. サーバーを実行する (OAuth サポートを追加する前)
認証を追加する前に、サーバーをテストします。

サーバーをテストするための最も簡単な方法は、コマンド ラインで curl を使用することです。 その前に、出力を JSON として解析できるようにするユーティリティが必要です。

1. 次の JSON ツールをインストールします (以下のすべての例で、このツールを使用します)。

    `$npm install -g jsontool`

    これにより、JSON ツールがグローバルにインストールされます。 用意ができたので、サーバーの操作を開始します。

2. まず、MongoDB インスタンスが実行されていることを確認します。

    `$sudo mongod`

3. 次に、ディレクトリを変更し、curl を開始します。

    `$ cd azuread`
    `$ node server.js`

    `$ curl -isS http://127.0.0.1:8080 | json`

    ```Shell
    HTTP/1.1 200 OK
    Connection: close
    Content-Type: application/json
    Content-Length: 171
    Date: Tue, 14 Jul 2015 05:43:38 GMT
    [
    "GET /",
    "POST /tasks/:owner/:task",
    "POST /tasks (for JSON body)",
    "GET /tasks",
    "PUT /tasks/:owner",
    "GET /tasks/:owner",
    "DELETE /tasks/:owner/:task"
    ]
    ```

4. 最後に、タスクを次のように追加します。

    `$ curl -isS -X POST http://127.0.0.1:8080/tasks/brandon/Hello`

    次のような応答が返ります。

        ```Shell
        HTTP/1.1 201 Created
        Connection: close
        Access-Control-Allow-Origin: *
        Access-Control-Allow-Headers: X-Requested-With
        Content-Type: application/x-www-form-urlencoded
        Content-Length: 5
        Date: Tue, 04 Feb 2014 01:02:26 GMT
        Hello
        ```
    Brandon 用のタスクを次の方法でリストできます。

        `$ curl -isS http://127.0.0.1:8080/tasks/brandon/`

これらのすべてが正常に機能した場合、OAuth を REST API サーバーに追加できます。

これで、MongoDB がある REST API サーバーが用意できました。

## <a name="step-18-add-authentication-to-our-rest-api-server"></a>手順 18. REST API サーバーに認証を追加する
実行中の REST API があるので、Azure AD で活用できるようにしましょう。

コマンド ラインで、**azuread** フォルダーに移動します (現在のディレクトリがこのディレクトリではない場合)。

`cd azuread`

### <a name="use-the-oidcbearerstrategy-that-is-included-with-passport-azure-ad"></a>passport-azure-ad に含まれている OIDCBearerStrategy の使用
ここまで、認証がまったく行われない REST TODO サーバーを構築してきました。 ここから、認証を配置する手順を開始します。

1. 最初に、Passport を使用することを指定する必要があります。 次のコードを、他のサーバー構成の直後に配置します。

    ```Javascript
            // Let's start using Passport.js.

            server.use(passport.initialize()); // Starts passport.
            server.use(passport.session()); // Provides session support.
    ```
    > [!TIP]
    > API を記述するときは、ユーザーがなりすますことができないトークンの一意の情報に常にデータをリンクすることをお勧めします。 このサーバーは、TODO 項目を保存するときに、トークン内のユーザーのオブジェクト ID (token.oid を介して呼び出されます) に基づいてそれらを保存します。この ID は、"owner" フィールドに設定した値です。 こうすることにより、そのユーザーのみが自分の TODO にアクセスできるようになります。 API 内で "owner" が公開されることはないため、外部ユーザーは、他のユーザーの TODO を、認証される場合でも要求することができます。                    

2. 次に、`passport-azure-ad` に含まれているベアラー戦略を使用します。 ここではコードを見てください。他のことは、すぐ後で説明します。 これを、前に貼り付けたコードの後ろに配置します。

```Javascript
    /**
    /*
    /* Calling the OIDCBearerStrategy and managing users.
    /*
    /* Passport pattern provides the need to manage users and info tokens
    /* with a FindorCreate() method that must be provided by the implementor.
    /* Here we just auto-register any user and implement a FindById().
    /* You'll want to do something smarter.
    **/

    var findById = function(id, fn) {
        for (var i = 0, len = users.length; i < len; i++) {
            var user = users[i];
            if (user.sub === id) {
                log.info('Found user: ', user);
                return fn(null, user);
            }
        }
        return fn(null, null);
    };


    var bearerStrategy = new BearerStrategy(options,
        function(token, done) {
            log.info('verifying the user');
            log.info(token, 'was the token retreived');
            findById(token.sub, function(err, user) {
                if (err) {
                    return done(err);
                }
                if (!user) {
                    // "Auto-registration"
                    log.info('User was added automatically as they were new. Their sub is: ', token.sub);
                    users.push(token);
                    owner = token.sub;
                    return done(null, token);
                }
                owner = token.sub;
                return done(null, user, token);
            });
        }
    );

    passport.use(bearerStrategy);
```

Passport は、すべての戦略ライターが従うすべての戦略 (Twitter や Facebook など) に対して類似するパターンを使用します。 戦略を調べると、それにパラメーターとして token と done を持つ関数が渡されることがわかります。 戦略は、その処理を終えると戻ってきます。 戻ったら、再度要求しなくてもいいように、ユーザーを保存し、トークンを隠します。

> [!IMPORTANT]
> 前のコードでは、サーバーに認証を求めたすべてのユーザーを受け入れています。 これは、自動登録と呼ばれます。 運用サーバーでは、指定した登録プロセスを先に実行しない限り、どのユーザーも受け入れないようにすることをお勧めします。 これは、コンシューマー アプリで通常見られるパターンです。たとえば、Facebook への登録は許可しても、その後で追加情報の入力を求めます。 これがコマンド ライン プログラムでなければ、返されるトークン オブジェクトから電子メールを抽出した後、ユーザーに追加情報の入力を要求できたでしょう。 これはテスト サーバーなので、単純にユーザーをメモリ内データベースに追加します。
>
>

### <a name="protect-some-endpoints"></a>いくつかのエンドポイントを保護する
エンドポイントを保護するには、使用するプロトコルをパラメーターとして、`passport.authenticate()` 呼び出しを指定します。

さらに興味深いことを行うためにサーバー コードのルートを編集してみましょう。

```Javascript
server.get('/tasks', passport.authenticate('oauth-bearer', {
session: false
}), listTasks);
server.get('/tasks', passport.authenticate('oauth-bearer', {
session: false
}), listTasks);
server.get('/tasks/:owner', passport.authenticate('oauth-bearer', {
session: false
}), getTask);
server.head('/tasks/:owner', passport.authenticate('oauth-bearer', {
session: false
}), getTask);
server.post('/tasks/:owner/:task', passport.authenticate('oauth-bearer', {
session: false
}), createTask);
server.post('/tasks', passport.authenticate('oauth-bearer', {
session: false
}), createTask);
server.del('/tasks/:owner/:task', passport.authenticate('oauth-bearer', {
session: false
}), removeTask);
server.del('/tasks/:owner', passport.authenticate('oauth-bearer', {
session: false
}), removeTask);
server.del('/tasks', passport.authenticate('oauth-bearer', {
session: false
}), removeTask);
server.del('/tasks', passport.authenticate('oauth-bearer', {
session: false
}), removeAll, function respond(req, res, next) {
res.send(204);
next();
});
```

## <a name="step-19-run-your-server-application-again-and-ensure-it-rejects-you"></a>手順 19. サーバー アプリケーションを再実行し、自分が拒否されることを確認する
再び `curl` を使用して、エンドポイントに対して OAuth2 保護が有効になっていることを確認します。 この操作は、このエンドポイントに対してクライアント SDK のいずれかを実行する前に行います。 返されるヘッダーだけで、正しく操作できていることを確認するには十分です。

1. まず、MongoDB インスタンスが実行されていることを確認します。

    `$sudo mongod`

2. 次に、ディレクトリを変更し、curl を開始します。

      `$ cd azuread`
      `$ node server.js`

3. 基本的な POST を試してください。

    `$ curl -isS -X POST http://127.0.0.1:8080/tasks/brandon/Hello`

    ```Shell
    HTTP/1.1 401 Unauthorized
    Connection: close
    WWW-Authenticate: Bearer realm="Users"
    Date: Tue, 14 Jul 2015 05:45:03 GMT
    Transfer-Encoding: chunked
    ```

ここでは、401 が適切な応答です。 この応答は、Passport レイヤーが認証エンドポイントへのリダイレクトを試みていることを示しています。それこそが、期待している動作です。

## <a name="next-steps"></a>次のステップ
OAuth2 互換のクライアントを使用することなく、このサーバーを使用して最大限のことを実現できました。 別のチュートリアルに進むことが必要になります。

ここまでで、Restify と OAuth2 を使用して REST API を実装する方法を学習しました。 サービスの開発とこの例を基盤にした学習を続けるための十分なコードも入手できました。

ADAL に関連するさらに高度な手順に興味がある場合は、以下のいくつかのサポートされている ADAL クライアントについて学習することをお勧めします。

開発マシンにクローンして、チュートリアルで説明されているように構成します。

[ADAL for iOS](https://github.com/MSOpenTech/azure-activedirectory-library-for-ios)

[ADAL for Android](https://github.com/MSOpenTech/azure-activedirectory-library-for-android)

[!INCLUDE [active-directory-devquickstarts-additional-resources](../../../includes/active-directory-devquickstarts-additional-resources.md)]

