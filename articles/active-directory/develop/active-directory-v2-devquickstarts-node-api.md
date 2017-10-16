---
title: "Node.js を使用した Azure Active Directory v2.0 Web API のセキュリティ保護 | Microsoft Docs"
description: "個人の Microsoft アカウントと職場/学校アカウントの両方からのトークンを受け付ける Node.js Web API を構築する方法を説明します。"
services: active-directory
documentationcenter: nodejs
author: navyasric
manager: mbaldwin
editor: 
ms.assetid: 0b572fc1-2aaf-4cb6-82de-63010fb1941d
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: javascript
ms.topic: article
ms.date: 05/13/2017
ms.author: nacanuma
ms.custom: aaddev
ms.openlocfilehash: 94e945a52b9df7c495de1611baa08083357670c9
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="secure-a-web-api-by-using-nodejs"></a>Node.js を使用した Web API のセキュリティ保護
> [!NOTE]
> Azure Active Directory のシナリオおよび機能のすべてが v2.0 エンドポイントで機能するわけではありません。 v2.0 エンドポイントと v1.0 エンドポイントのどちらを使用するかを判断するには、[v2.0 の制限](active-directory-v2-limitations.md)に関する記事を参照してください。
> 
> 

Azure Active Directory (Azure AD) v2.0 エンドポイントを使用する場合は、[OAuth 2.0](active-directory-v2-protocols.md)アクセス トークンを使用して、Web API を保護できます。 OAuth 2.0 アクセス トークンでは、個人の Microsoft アカウントと職場または学校のアカウントの両方を持つユーザーが、Web API に安全にアクセスできます。

*Passport* は Node.js 用の認証ミドルウェアです。 Passport は、柔軟で高度なモジュール構造をしており、任意の Express ベースまたは Resitify Web アプリケーションに、支障をきたすことなくドロップできます。 Passport では、包括的な認証手法セットにより、ユーザー名とパスワードを使用する認証、Facebook、Twitter などをサポートします。 Microsoft は Azure AD 用の認証手法を開発しました。 この記事では、モジュールをインストールした後に Azure AD `passport-azure-ad` プラグインを追加する方法について説明します。

## <a name="download"></a>ダウンロード
このチュートリアルのコードは、 [GitHub](https://github.com/AzureADQuickStarts/AppModelv2-WebAPI-nodejs)で管理されています。 チュートリアルを実行するには、[アプリのスケルトン (.zip ファイル) をダウンロード](https://github.com/AzureADQuickStarts/AppModelv2-WebAPI-nodejs/archive/skeleton.zip)したり、次のようにスケルトンを複製したりすることができます。

```git clone --branch skeleton https://github.com/AzureADQuickStarts/AppModelv2-WebAPI-nodejs.git```

このチュートリアルの最後のところで、完成したアプリケーションを取得することもできます。

## <a name="1-register-an-app"></a>1: アプリを登録する
[apps.dev.microsoft.com](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList) で新しいアプリを作成するか、この[詳細な手順](active-directory-v2-app-registration.md)に従ってアプリを登録します。 以下を実行します。

* アプリに割り当てられた**アプリケーション ID** をコピーしておきます。 このチュートリアルで必要になります。
* アプリ用の **モバイル** プラットフォームを追加します。
* ポータルから **リダイレクト URI** をコピーしておきます。 既定の URI 値 `urn:ietf:wg:oauth:2.0:oob` を使用する必要があります。

## <a name="2-install-nodejs"></a>2: Node.js をインストールする
このチュートリアルのサンプルを使用するには、[Node.js をインストールする](http://nodejs.org)必要があります。

## <a name="3-install-mongodb"></a>3: MongoDB をインストールする
このサンプルを正常に使用するには、[MongoDB をインストールする](http://www.mongodb.org)必要があります。 このサンプルでは、MongoDB を使用して、REST API がサーバー インスタンス間で持続されるようにします。

> [!NOTE]
> この記事では、MongoDB の既定のインストールおよびサーバー エンドポイント (mongodb://localhost) を使用することを前提としています。
> 
> 

## <a name="4-install-the-restify-modules-in-your-web-api"></a>4: Web API に Restify モジュールをインストールする
Resitfy を使用して REST API を構築します。 Restify は、Express から派生した、最小限の柔軟な Node.js アプリケーション フレームワークです。 Restify は、Connect 上に REST API を構築するために使用できる一連の堅牢な機能を備えています。

### <a name="install-restify"></a>Restify のインストール
1.  コマンド プロンプトで、ディレクトリを **azuread** に変更します。

    `cd azuread`

    **azuread** ディレクトリが存在しない場合は作成します。

    `mkdir azuread`

2.  Restify をインストールします。

    `npm install restify`

    このコマンドの出力は、次のようになります。

    ```
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
    └── bunyan@0.22.0(mv@0.0.5)
    ```

#### <a name="did-you-get-an-error"></a>エラーが発生した場合
一部のオペレーティング システムでは、`npm` コマンドを使用すると、メッセージ `Error: EPERM, chmod '/usr/local/bin/..'` が表示されることがあります。 エラーの後に、管理者としてアカウントを実行してみるように要求されます。 これが発生した場合は、`sudo` コマンドを使用して、より高い権限レベルで `npm` を実行します。

#### <a name="did-you-get-an-error-related-to-dtrace"></a>DTrace に関するエラーが表示された場合
Restify をインストールすると、このメッセージが表示されることがあります。

```Shell
clang: error: no such file or directory: 'HD/azuread/node_modules/restify/node_modules/dtrace-provider/libusdt'
make: *** [Release/DTraceProviderBindings.node] Error 1
gyp ERR! build error
gyp ERR! stack Error: `make` failed with exit code: two
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

Restify は、DTrace を使用して REST 呼び出しをトレースする強力なメカニズムを備えています。 ただし、DTrace は、多くのオペレーティング システムで使用できません。 このエラーは無視してかまいません。


## <a name="5-install-passportjs-in-your-web-api"></a>5: Web API で Passport.js をインストールする
1.  コマンド プロンプトで、ディレクトリを **azuread** に変更します。

2.  Passport.js をインストールします。

    `npm install passport`

    このコマンドの出力は次のようになります。

    ```
     passport@0.1.17 node_modules\passport
    ├── pause@0.0.1
    └── pkginfo@0.2.3
    ```

## <a name="6-add-passport-azure-ad-to-your-web-api"></a>6: passport-azure-ad を Web API に追加する
次に、passport-azuread を使用して OAuth 戦略を追加します。 `passport-azuread` は、Azure AD と Passport を接続する戦略のスイートです。 この REST API の例では、このベアラー トークン用の戦略を使用します。

> [!NOTE]
> OAuth 2.0 は、任意の既知のトークン タイプを発行できるフレームワークを提供しますが、よく使用されるのは一部のトークン タイプです。 ベアラー トークンは、一般にエンドポイントの保護に使用されます。 ベアラー トークンは、OAuth 2.0 で最も広く発行されたトークンの種類です。 多くの OAuth 2.0 実装では、ベアラー トークンが、発行される唯一の種類のトークンであると想定されています。
> 
> 

1.  コマンド プロンプトで、ディレクトリを **azuread** に変更します。

    `cd azuread`

2.  Passport.js `passport-azure-ad` モジュールをインストールします。

    `npm install passport-azure-ad`

    このコマンドの出力は次のようになります。

    ```
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
    ```

## <a name="7-add-mongodb-modules-to-your-web-api"></a>7: MongoDB モジュールを Web API に追加する
このサンプルでは、データ ストアとして MongoDB を使用します。 

1.  Mongoose (モデルとスキーマの管理に広く使用されているプラグイン) をインストールします。 

    `npm install mongoose`

2.  さらに、MongoDB 用のデータベース ドライバー (これも MongoDB と呼ばれます) をインストールします。

    `npm install mongodb`

## <a name="8-install-additional-modules"></a>8: 追加モジュールをインストールする
その他の必須モジュールをインストールします。

1.  コマンド プロンプトで、ディレクトリを **azuread** に変更します。

    `cd azuread`

2.  次のコマンドを入力します。 コマンドによって、次のモジュールを node_modules ディレクトリにインストールします。

    *   `npm install crypto`
    *   `npm install assert-plus`
    *   `npm install posix-getopt`
    *   `npm install util`
    *   `npm install path`
    *   `npm install connect`
    *   `npm install xml-crypto`
    *   `npm install xml2js`
    *   `npm install xmldom`
    *   `npm install async`
    *   `npm install request`
    *   `npm install underscore`
    *   `npm install grunt-contrib-jshint@0.1.1`
    *   `npm install grunt-contrib-nodeunit@0.1.2`
    *   `npm install grunt-contrib-watch@0.2.0`
    *   `npm install grunt@0.4.1`
    *   `npm install xtend@2.0.3`
    *   `npm install bunyan`
    *   `npm update`

## <a name="9-create-a-serverjs-file-for-your-dependencies"></a>9: 依存関係を持つ Server.js ファイルの作成
Server.js ファイルは、Web API サーバー用のほとんどの機能を提供します。 ほとんどのコードは、このファイルに追加します。 運用環境では、ルートとコントローラーを分割するなどして、機能をより小さなファイルにリファクタリングできます。 この記事では、その目的で Server.js を使用します。

1.  コマンド プロンプトで、ディレクトリを **azuread** に変更します。

    `cd azuread`

2.  選択したエディターを使用して、Server.js ファイルを作成します。 ファイルに以下の情報を追加します。

    ```Javascript
    'use strict';
    /**
    * Module dependencies.
    */
    var util = require('util');
    var assert = require('assert-plus');
    var mongoose = require('mongoose/');
    var bunyan = require('bunyan');
    var restify = require('restify');
    var config = require('./config');
    var passport = require('passport');
    var OIDCBearerStrategy = require('passport-azure-ad').OIDCStrategy;
    ```

3.  ファイルを保存します。 この後すぐに、このファイルを使用します。

## <a name="10-create-a-config-file-to-store-your-azure-ad-settings"></a>10: Azure AD の設定を保存する構成ファイルを作成する
このコード ファイルは、構成パラメーターを Azure AD ポータルから Passport.js ファイルに渡します。 これらの構成値は、この記事の最初で Web API をポータルに追加したときに作成したものです。 ここでは、コードをコピーした後に、これらのパラメーターにどのような値を設定するかについて説明します。

1.  コマンド プロンプトで、ディレクトリを **azuread** に変更します。

    `cd azuread`

2.  エディターで、Config.js ファイルを作成します。 以下の情報を追加します。

    ```Javascript
    // Don't commit this file to your public repos. This config is for first-run.
    exports.creds = {
    mongoose_auth_local: 'mongodb://localhost/tasklist', // Your Mongo auth URI goes here.
    issuer: 'https://sts.windows.net/**<your application id>**/',
    audience: '<your redirect URI>',
    identityMetadata: 'https://login.microsoftonline.com/common/.well-known/openid-configuration' // For Microsoft, you should never need to change this.
    };

    ```



### <a name="required-values"></a>必要な値

*   **IdentityMetadata**: これは、`passport-azure-ad` が、ID プロバイダー (IDP) の構成データと、JSON Web トークン (JWT) を検証するためのキーを検索する場所です。 Azure AD を使用している場合は、これを変更する必要はないと考えられます。

*   **audience**: ポータルのリダイレクト URI。

> [!NOTE]
> キーは頻繁に公開されます。 "openid_keys" URL からキーを常に取得し、アプリがインターネットにアクセスできるようにしてください。
> 
> 

## <a name="11-add-the-configuration-to-your-serverjs-file"></a>11: 構成を server.js ファイルに追加する
アプリケーションは、作成した構成ファイルから値を読み取る必要があります。 アプリケーションで必要なリソースとして .config ファイルを追加します。 グローバル変数を Config.js 内にあるものに設定します。

1.  コマンド プロンプトで、ディレクトリを **azuread** に変更します。

    `cd azuread`

2.  エディターで Server.js を開きます。 以下の情報を追加します。

    ```Javascript
    var config = require('./config');
    ```

3.  Server.js に新しいセクションを追加します。

    ```Javascript
    // Pass these options in to the ODICBearerStrategy.
    var options = {
    // The URL of the metadata document for your app. Put the keys for token validation from the URL found in the jwks_uri tag in the metadata.
    identityMetadata: config.creds.identityMetadata,
    issuer: config.creds.issuer,
    audience: config.creds.audience
    };
    // Array to hold signed-in users and the current signed-in user (owner).
    var users = [];
    var owner = null;
    // Your logger
    var log = bunyan.createLogger({
    name: 'Microsoft Azure Active Directory Sample'
    });
    ```

## <a name="12-add-the-mongodb-model-and-schema-information-by-using-mongoose"></a>12: Mongoose を使用して、MongoDB モデルおよびスキーマ情報を追加する
次に REST API サービスでこれら 3 つのファイルを接続します。

この記事では、MongoDB を使用して、タスクを保存します。 これについては、*手順 4* で説明します。

手順 11 で作成した、Config.js ファイルで、データベースは *tasklist* と呼ばれます。 それは、Mongoose_auth_local 接続 URL の末尾に入れたものです。 MongoDB では、事前にこのデータベースを作成する必要はありません。 データベースは、サーバー アプリケーションの初回実行時に作成されます (データベースが存在していないものとして)。

使用する MongoDB データベースをサーバーに伝えます。 次に、サーバーのタスクのモデルとスキーマを作成する追加コードを記述する必要があります。

### <a name="the-model"></a>モデル
スキーマ モデルはきわめて基本的です。 必要に応じて拡張できます。 

スキーマ モデルには、次の値があります。

*   **NAME**。 タスクに割り当てられたユーザー。 これは、**string** 値です。
*   **TASK**。 タスクの名前。 これは、**string** 値です。
*   **DATE**。 タスクの期限日。 これは、**datetime** 値です。
*   **COMPLETED**。 タスクが完了したかどうか。 これは、**Boolean** 値です。

### <a name="create-the-schema-in-the-code"></a>コードでのスキーマの作成
1.  コマンド プロンプトで、ディレクトリを **azuread** に変更します。

    `cd azuread`

2.  エディターで Server.js を開きます。 構成エントリの下に、以下の情報を追加します。

    ```Javascript
    // MongoDB setup.
    // Set up some configuration.
    var serverPort = process.env.PORT || 8080;
    var serverURI = (process.env.PORT) ? config.creds.mongoose_auth_mongohq : config.creds.mongoose_auth_local;
    // Connect to MongoDB.
    global.db = mongoose.connect(serverURI);
    var Schema = mongoose.Schema;
    log.info('MongoDB Schema loaded');
    ```

このコードは、MongoDB サーバーに接続します。 また、スキーマ オブジェクトも返します。

#### <a name="using-the-schema-create-your-model-in-the-code"></a>スキーマを使用してコードでモデルを作成する
上記のコードの下に次のコードを追加します。

```Javascript
// Create a basic schema to store your tasks and users.
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

コードからわかるように、まずスキーマを作成します。 次に、モデル オブジェクトを作成します。 **ルート**を定義する際に、モデル オブジェクトを使用して、コード全体でデータを格納します。

## <a name="13-add-your-routes-for-your-task-rest-api-server"></a>13: タスク REST API サーバー用ルートを追加する
これで、操作対象のデータベース モデルが作成されたので、REST API サーバー用に使用するルートを追加します。

### <a name="about-routes-in-restify"></a>Restify 内のルートについて
Restify 内のルートは、Express スタックを使用する場合とまったく同じように機能します。 ルートは、クライアント アプリが呼び出すことが想定される URI を使用して定義されます。 通常、ルートは個別のファイルで定義されますが、 このチュートリアルでは、ルートを Server.js に格納します。 運用環境では、各ルートを独自のファイルに格納することをお勧めします。

Restify ルートの典型的なパターンを次に示します。

```Javascript
function createObject(req, res, next) {
// Do work on object.
_object.name = req.params.object; // Passed value is in req.params under object.
///...
return next(); // Keep the server going.
}
....
server.post('/service/:add/:object', createObject); // calls createObject on routes that match this.
```


これは、最も基本的なレベルのパターンです。 Restify (と Express) では、アプリケーションの種類を定義する機能や異なるエンドポイントにまたがる複雑なルーティングなどのより高度な機能を備えています。

#### <a name="add-default-routes-to-your-server"></a>サーバーへの既定のルートの追加
**create**、**retrieve**、**update**、**delete** の基本的な CRUD ルートを追加します。

1.  コマンド プロンプトで、ディレクトリを **azuread** に変更します。

    `cd azuread`

2.  エディターで Server.js を開きます。 前に作成したデータベース エントリの下に、以下の情報を追加します。

    ```Javascript
    /**
    *
    * APIs for your REST task server
    */
    // Create a task.
    function createTask(req, res, next) {
    // Resitify currently has a bug that doesn't allow you to set default headers.
    // These headers comply with CORS, and allow you to use MongoDB Server as your response to any origin.
    res.header("Access-Control-Allow-Origin", "*");
    res.header("Access-Control-Allow-Headers", "X-Requested-With");
    // Create a new task model, fill it, and save it to MongoDB.
    var _task = new Task();
    if (!req.params.task) {
    req.log.warn({
    params: p
    }, 'createTodo: missing task');
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
    /// Returns the list of TODOs that were loaded.
    function listTasks(req, res, next) {
    // Resitify currently has a bug that doesn't allow you to set default headers.
    // These headers comply with CORS, and allow us to use MongoDB Server as our response to any origin.
    res.header("Access-Control-Allow-Origin", "*");
    res.header("Access-Control-Allow-Headers", "X-Requested-With");
    log.info("listTasks was called for: ", owner);
    Task.find({
    owner: owner
    }).limit(20).sort('date').exec(function(err, data) {
    if (err)
    return next(err);
    if (data.length > 0) {
    log.info(data);
    }
    if (!data.length) {
    log.warn(err, "There are no tasks in the database. Add one!");
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

### <a name="add-error-handling-for-the-routes"></a>ルートのエラー処理の追加
発生した問題についてクライアントに伝えられるように、いくつかのエラー処理を追加します。

既に作成したコードの下に、次のコードを追加します。

```Javascript
///--- Errors for communicating something more information back to the client.
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


## <a name="14-create-your-server"></a>14: サーバーを作成する
最後に、サーバー インスタンスを追加します。 サーバー インスタンスは、呼び出しを管理します。

Restify (および Express) には REST API サーバーで使用できる詳細なカスタマイズが可能です。 このチュートリアルでは、最も基本的な設定を使用します。

```Javascript
/**
* Your server
*/
var server = restify.createServer({
name: "Microsoft Azure Active Directory TODO Server",
version: "2.0.1"
});
// Ensure that you don't drop data on uploads.
server.pre(restify.pre.pause());
// Clean up imprecise paths like //todo//////1//.
server.pre(restify.pre.sanitizePath());
// Handle annoying user agents (curl).
server.pre(restify.pre.userAgentConnection());
// Set a per-request Bunyan logger (with requestid filled in).
server.use(restify.requestLogger());
// Allow 5 requests/second by IP address, and burst to 10.
server.use(restify.throttle({
burst: 10,
rate: 5,
ip: true,
}));
// Use common commands, such as:
server.use(restify.acceptParser(server.acceptable));
server.use(restify.dateParser());
server.use(restify.queryParser());
server.use(restify.gzipResponse());
server.use(restify.bodyParser({
mapParams: true
}));
```
## <a name="15-add-the-routes-without-authentication-for-now"></a>15: ルートを追加する (まだ認証は行われません)
```Javascript
/// Use CRUD to add the real handlers.
/**
/*
/* Each of these handlers is protected by your Open ID Connect Bearer strategy. Invoke 'oidc-bearer'
/* in the pasport.authenticate() method. Because REST is stateless, set 'session: false'. You 
/* don't need to maintain session state. You can experiment with removing API protection.
/* To do this, remove the passport.authenticate() method:
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
// Register a default '/' handler
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
## <a name="16-run-the-server"></a>16: サーバーを実行します
認証を追加する前に、サーバーをテストすることをお勧めします。

サーバーをテストする最も簡単な方法は、コマンド プロンプトで curl を使用することです。 これには、出力を JSON として解析するために使用できる簡単なユーティリティが必要です。 

1.  次の例で使用する JSON ツールをインストールします。

    `$npm install -g jsontool`

    これにより、JSON ツールがグローバルにインストールされます。

2.  MongoDB インスタンスが実行されていることを確認してください。

    `$sudo mongod`

3.  ディレクトリを **azuread** に変更し、curl を実行します。

    `$ cd azuread`
    `$ node server.js`

    `$ curl -isS http://127.0.0.1:8080 | json`

    ```Shell
    HTTP/1.1 2.0OK
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

4.  タスクを追加するには、次を実行します。

    `$ curl -isS -X POST http://127.0.0.1:8888/tasks/brandon/Hello`

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

5.  Brandon のタスクを一覧表示します。

    `$ curl -isS http://127.0.0.1:8080/tasks/brandon/`

これらすべてのコマンドが、エラーなく実行した場合、OAuth を REST API サーバーに追加する準備ができています。

*これで、MongoDB がある REST API サーバーが用意できました。*

## <a name="17-add-authentication-to-your-rest-api-server"></a>17: REST API サーバーに認証を追加する
REST API が実行したので、Azure AD で使用するために設定します。

コマンド プロンプトで、ディレクトリを **azuread** に変更します。

`cd azuread`

### <a name="use-the-oidcbearerstrategy-thats-included-with-passport-azure-ad"></a>passport-azure-ad に含まれている oidcbearerstrategy の使用
ここまで、認証がまったく行われない一般的な REST TODO サーバーを構築してきました。 ここで、認証を追加します。

まず、Passport を使用することを指示します。 次のコードを、前のサーバー構成の直後に配置します。

```Javascript
// Start using Passport.js.

server.use(passport.initialize()); // Starts passport
server.use(passport.session()); // Provides session support
```

> [!TIP]
> API を記述するときは、ユーザーがなりすますことができないトークンの一意の情報に常にデータをリンクすることをお勧めします。 このサーバーは、TODO 項目を保存するときに、(token.sub を通して呼び出される) トークン内のユーザーのサブスクリプション ID に基づいてそれらを保存します。 "owner" フィールドに、token.sub を配置します。 こうすることにより、そのユーザーのみがユーザーの TODO にアクセスできるようになります。 入力した TODO には、他の誰もアクセスできません。 "owner" の API で公開されるものはありません。 外部ユーザーは、認証されている場合でも、他のユーザーの TODO を要求できます。
> 
> 

次に、`passport-azure-ad` に含まれる Open ID Connect Bearer 戦略を使用します。 これを、前に貼り付けたコードの後ろに配置します。

```Javascript
/**
/*
/* Calling the OIDCBearerStrategy and managing users.
/*
/* Because of the Passport pattern, you need to manage users and info tokens
/* with a FindorCreate() method. The method must be provided by the implementor.
/* In the following code, you autoregister any user and implement a FindById().
/* It's a good idea to do something more advanced.
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
var oidcStrategy = new OIDCBearerStrategy(options,
function(token, done) {
log.info('verifying the user');
log.info(token, 'was the token retrieved');
findById(token.sub, function(err, user) {
if (err) {
return done(err);
}
if (!user) {
// "Auto-registration"
log.info('User was added automatically, because they were new. Their sub is: ', token.sub);
users.push(token);
owner = token.sub;
return done(null, token);
}
owner = token.sub;
return done(null, user, token);
});
}
);
passport.use(oidcStrategy);
```

Passport は、Twitter や Facebook などのすべての戦略に、同様のパターンを使用します。 すべての戦略記述者がこのパターンに従います。 パラメーターとして token と `done` を使用する `function()` の戦略を渡します。 すべての作業が終わった後で戦略が返されます。 再度要求しなくて済むように、ユーザーを格納し、トークンを保存します。

> [!IMPORTANT]
> 上記のコードでは、サーバーに対して認証可能なすべてのユーザーを受け入れます。 これは、自動登録と呼ばれます。 運用サーバーでは、指定された登録プロセスを先に実行していないユーザーにはアクセスを許可しないように設定できます。 これは、コンシューマー アプリで通常見られるパターンです。 アプリによって Facebook への登録は許可されるものの、後で追加情報の入力が求められます。 このチュートリアルでコマンドライン プログラムを使用していなければ、返されるトークン オブジェクトからメールを抽出できます。 その後、ユーザーに追加情報の入力を要求できます。 これはテスト サーバーなので、ユーザーをメモリ内データベースに直接追加します。
> 
> 

### <a name="protect-endpoints"></a>エンドポイントの保護
エンドポイントを保護するには、使用するプロトコルをパラメーターとして、**passport.authenticate()** 呼び出しを指定します。

さらに詳細なオプションで、サーバー コード内のルートを編集できます。

```Javascript
server.get('/tasks', passport.authenticate('oidc-bearer', {
session: false
}), listTasks);
server.get('/tasks', passport.authenticate('oidc-bearer', {
session: false
}), listTasks);
server.get('/tasks/:owner', passport.authenticate('oidc-bearer', {
session: false
}), getTask);
server.head('/tasks/:owner', passport.authenticate('oidc-bearer', {
session: false
}), getTask);
server.post('/tasks/:owner/:task', passport.authenticate('oidc-bearer', {
session: false
}), createTask);
server.post('/tasks', passport.authenticate('oidc-bearer', {
session: false
}), createTask);
server.del('/tasks/:owner/:task', passport.authenticate('oidc-bearer', {
session: false
}), removeTask);
server.del('/tasks/:owner', passport.authenticate('oidc-bearer', {
session: false
}), removeTask);
server.del('/tasks', passport.authenticate('oidc-bearer', {
session: false
}), removeTask);
server.del('/tasks', passport.authenticate('oidc-bearer', {
session: false
}), removeAll, function respond(req, res, next) {
res.send(204);
next();
});
```

## <a name="18-run-your-server-application-again"></a>18: サーバー アプリケーションを再実行する
curl を再度使用して、エンドポイントに対して OAuth 2.0 保護が有効になっていることを確認できます。 この操作は、このエンドポイントに対して、いずれかのクライアント SDK を実行する前に行います。 返されるヘッダーに、認証が正常に機能しているかどうかが示されるはずです。

1.  MongoDB インスタンスが実行されていることを確認してください。

    `$sudo mongod`

2.  **azuread** ディレクトリに変更し、curl を使用します。

    `$ cd azuread`

    `$ node server.js`

3.  基本的な POST を試してください。

    `$ curl -isS -X POST http://127.0.0.1:8080/tasks/brandon/Hello`

    ```Shell
    HTTP/1.1 401 Unauthorized
    Connection: close
    WWW-Authenticate: Bearer realm="Users"
    Date: Tue, 14 Jul 2015 05:45:03 GMT
    Transfer-Encoding: chunked
    ```

401 応答は、Passport レイヤーが認証エンドポイントへのリダイレクトを試みていることを示しています。それこそが、期待している動作です。

*これで、OAuth 2.0 を使用する REST API サービスの準備ができました。*

OAuth 2.0 互換のクライアントを使用することなく、このサーバーを使用して最大限のことを実現できました。 そのためには、追加のチュートリアルを確認する必要があります。

## <a name="next-steps"></a>次のステップ
参考のため、完成済みサンプル (構成値を除く) が [.zip ファイル](https://github.com/AzureADQuickStarts/AppModelv2-WebAPI-nodejs/archive/complete.zip)で提供されています。 GitHub から複製することもできます。

```git clone --branch complete https://github.com/AzureADQuickStarts/AppModelv2-WebAPI-nodejs.git```

これで、さらに高度なトピックに進むことができます。 [v2.0 エンドポイントを使用して Node.js Web アプリをセキュリティで保護してみる](active-directory-v2-devquickstarts-node-web.md)こともできます。

次にその他のリソースを示します。

* [Azure AD v2.0 開発者ガイド](active-directory-appmodel-v2-overview.md)
* [Stack Overflow "azure-Active Directory" タグ](http://stackoverflow.com/questions/tagged/azure-active-directory)

### <a name="get-security-updates-for-our-products"></a>Microsoft 製品のセキュリティ更新プログラムの取得
サインアップして、セキュリティの問題が発生したときに通知を受け取ることをお勧めします。 [マイクロソフト テクニカル セキュリティ通知](https://technet.microsoft.com/security/dd252948)のページで、セキュリティ アドバイザリ アラートに登録してください。

