---
title: Mobile Apps 用 Node.js バックエンド サーバー SDK を操作する方法 | Microsoft Docs
description: Azure App Service Mobile Apps 用の Node.js バックエンド サーバー SDK を操作する方法について説明します。
services: app-service\mobile
documentationcenter: ''
author: elamalani
manager: elamalani
editor: ''
ms.assetid: e7d97d3b-356e-4fb3-ba88-38ecbda5ea50
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-multiple
ms.devlang: node
ms.topic: article
ms.date: 10/01/2016
ms.author: crdun
ms.openlocfilehash: 831f6b4bdc99e63859b390f8a9bb88d74301284e
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2019
ms.locfileid: "57997535"
---
# <a name="how-to-use-the-mobile-apps-nodejs-sdk"></a>Mobile Apps Node.js SDK の使用方法

[!INCLUDE [app-service-mobile-selector-server-sdk](../../includes/app-service-mobile-selector-server-sdk.md)]

この記事では、Azure App Service の Mobile Apps 機能で Node.js バックエンドを使用する方法についての詳細な情報と例を提供します。

## <a name="Introduction"></a>はじめに

Mobile Apps は、Web アプリケーションにモバイルに最適化されたデータ アクセス Web API を追加する機能を提供します。 Mobile Apps SDK は、ASP.NET と Node.js の Web アプリケーション向けに用意されています。 この SDK を使用すると、次の処理を実行できます。

* データ アクセスのためのテーブル操作 (読み取り、挿入、更新、削除)
* カスタム API 操作

どちらの操作も、Azure App Service が許可するすべての ID プロバイダーに認証を提供します。 このようなプロバイダーには、Facebook、Twitter、Google、Microsoft などのソーシャル ID プロバイダー、エンタープライズ ID のための Azure Active Directory などがあります。

各ユース ケースのサンプルは、 [GitHub の samples ディレクトリ]にあります。

## <a name="supported-platforms"></a>サポートされるプラットフォーム

Mobile Apps Node.js SDK は、Node の現在の LTS リリース以降をサポートします。 現在、最新の LTS バージョンは Node v4.5.0 です。 Node の他のバージョンが動作する場合もありますが、サポートされているわけではありません。

Mobile Apps Node.js SDK は、次の 2 つのデータベース ドライバーをサポートしています。 

* node-mssql ドライバーは、Azure SQL Database とローカルの SQL Server インスタンスをサポートしています。  
* sqlite3 ドライバーは、単一のインスタンスでのみ、SQLite データベースをサポートします。

### <a name="howto-cmdline-basicapp"></a>コマンド ラインを使用して基本的な Node.js バックエンドを作成する

Mobile Apps Node.js バックエンドはすべて ExpressJS アプリケーションとして開始されます。 ExpressJS は、Node.js で使用可能な最も人気のある Web サービス フレームワークです。 次のように、基本的な [Express] アプリケーションを作成できます。

1. コマンド ウィンドウまたは PowerShell ウィンドウで、プロジェクトのディレクトリを作成します。

        mkdir basicapp

1. `npm init` を実行して、パッケージの構造を初期化します。

        cd basicapp
        npm init

   `npm init` コマンドでは、プロジェクトを初期化するための一連の質問が示されます。 次の出力例を参照してください。

   ![npm init の出力][0]

1. npm リポジトリから `express` ライブラリと `azure-mobile-apps` ライブラリをインストールします。

        npm install --save express azure-mobile-apps

1. app.js ファイルを作成して、基本的なモバイル サーバーを実装します。

    ```javascript
    var express = require('express'),
        azureMobileApps = require('azure-mobile-apps');

    var app = express(),
        mobile = azureMobileApps();

    // Define a TodoItem table.
    mobile.tables.add('TodoItem');

    // Add the Mobile API so it is accessible as a Web API.
    app.use(mobile);

    // Start listening on HTTP.
    app.listen(process.env.PORT || 3000);
    ```

このアプリケーションは、単一のエンドポイント (`/tables/TodoItem`) でモバイルに最適化された Web API を作成します。この Web API は、動的スキーマを使用して基になる SQL データ ストアへの認証されていないアクセスを可能にします。 次のクライアント ライブラリのクイックスタートに従う場合に適しています。

* [Android クライアントのクイックスタート]
* [Apache Cordova クライアントのクイックスタート]
* [iOS クライアントのクイックスタート]
* [Windows ストア クライアントのクイックスタート]
* [Xamarin.iOS クライアントのクイックスタート]
* [Xamarin.Android クライアントのクイックスタート]
* [Xamarin.Forms クライアントのクイックスタート]

この基本的なアプリケーションのコードは、 [GitHub の basicapp サンプル]にあります。

### <a name="howto-vs2015-basicapp"></a>Visual Studio 2015 を使用して Node.js バックエンドを作成する

Visual Studio 2015 には、IDE 内で Node.js アプリケーションを開発するための拡張機能が必要です。 まず、 [Node.js Tools 1.1 for Visual Studio]をインストールします。 インストールが完了したら、Express 4.x アプリケーションを作成します。

1. **[新しいプロジェクト]** ダイアログ ボックスを開きます (**[ファイル]** > **[新規作成]** > **[プロジェクト]** の順にクリックします)。
1. **[テンプレート]** > **[JavaScript]** > **[Node.js]** の順に展開します。
1. **[Basic Azure Node.js Express 4 Application]** を選択します。
1. プロジェクト名を入力します。 **[OK]** を選択します。

   ![Visual Studio 2015 の新しいプロジェクト][1]
1. **npm** ノードを右クリックし、**[新しい npm パッケージのインストール]** を選択します。
1. 最初の Node.js アプリケーションを作成した後に、npm カタログの更新が必要になる場合があります。 必要に応じて、**[最新の情報に更新]** を選択します。
1. 検索ボックスに「 **azure-mobile-apps** 」と入力します。 **azure-mobile-apps 2.0.0** パッケージを選択し、**[パッケージのインストール]** を選択します。

   ![新しい npm パッケージのインストール][2]
1. **[閉じる]** を選択します。
1. app.js ファイルを開き、Mobile Apps SDK のサポートを追加します。 ライブラリの `require` ステートメントの下の 6 行目に、次のコードを追加します。

    ```javascript
    var bodyParser = require('body-parser');
    var azureMobileApps = require('azure-mobile-apps');
    ```

    他の `app.use` ステートメントの後の約 27 行目に、次のコードを追加します。

    ```javascript
    app.use('/users', users);

    // Mobile Apps initialization
    var mobile = azureMobileApps();
    mobile.tables.add('TodoItem');
    app.use(mobile);
    ```

    ファイルを保存します。

1. アプリケーションをローカルで実行するか (API は `http://localhost:3000` で動作します)、Azure に発行します。

### <a name="create-node-backend-portal"></a>Azure Portal を使用して Node.js バックエンドを作成する

Mobile Apps バックエンドは、[Azure Portal] ですぐに作成できます。 次の手順を実行するか、[モバイル アプリの作成](app-service-mobile-ios-get-started.md)のチュートリアルに従ってクライアントとサーバーをまとめて作成することもできます。 このチュートリアルにはこれらの手順の簡略化されたバージョンが含まれており、プロジェクトの概念の実証に最適です。

[!INCLUDE [app-service-mobile-dotnet-backend-create-new-service-classic](../../includes/app-service-mobile-dotnet-backend-create-new-service-classic.md)]

**[テーブル API の作成]** の **[はじめに]** ウィンドウに戻り、バックエンド言語として **[Node.js]** を選択します。
**[これにより、すべてのサイト コンテンツが上書きされることを確認しました。]** のボックスをオンにし、**[TodoItem テーブルを作成する]** を選択します。

### <a name="download-quickstart"></a>Git を使用して Node.js バックエンド クイックスタート コード プロジェクトをダウンロードする

ポータルの **[クイック スタート]** ウィンドウを使用して Node.js Mobile Apps バックエンドを作成すると、Node.js プロジェクトが自動的に作成され、サイトにデプロイされます。 ポータルでは、テーブルと API を追加し、Node.js バックエンドのコード ファイルを編集することができます。 また、さまざまなデプロイ ツールを使用してバックエンド プロジェクトをダウンロードすると、テーブルと API を追加または変更した後でプロジェクトを再発行できます。 詳細については、[Azure App Service のデプロイ ガイド]に関するページを参照してください。

次の手順では、Git リポジトリを使用して、クイック スタート プロジェクトのコードをダウンロードします。

1. Git をまだインストールしていない場合はインストールします。 Git をインストールするために必要な手順は、オペレーティング システムによって異なります。 オペレーティング システム固有の配布とインストールのガイダンスについては、[Git のインストール](https://git-scm.com/book/en/Getting-Started-Installing-Git)に関するページを参照してください。
2. バックエンド サイトの Git リポジトリを有効にする方法については、「[リポジトリを準備する](../app-service/deploy-local-git.md#prepare-your-repository)」を参照してください。 デプロイ用のユーザー名とパスワードをメモしておきます。
3. Mobile Apps バックエンドのウィンドウで、**[Git クローン URL]** の設定をメモしておきます。
4. Git クローン URL を使用して `git clone` コマンドを実行します。 次の例のように、必要に応じてパスワードを入力します。

        $ git clone https://username@todolist.scm.azurewebsites.net:443/todolist.git

5. ローカル ディレクトリ (前の例では `/todolist`) を参照し、プロジェクト ファイルがダウンロードされていることを確認します。 `/tables` ディレクトリ内にある todoitem.json ファイルを見つけます。 このファイルでは、テーブルに対するアクセス許可を定義します。 同じディレクトリ内にある todoitem.js ファイルも見つけます。 このファイルには、テーブルの CRUD 操作スクリプトが定義されています。
6. プロジェクト ファイルを変更した後、次のコマンドを実行して変更を追加してコミットし、サイトにアップロードします。

        $ git commit -m "updated the table script"
        $ git push origin master

   新しいファイルをプロジェクトに追加する場合は、最初に `git add .` コマンドを実行する必要があります。

一連の新しいコミットがサイトにプッシュされるたびに、そのサイトは再発行されます。

### <a name="howto-publish-to-azure"></a>Azure に Node.js バックエンドを発行する

Microsoft Azure では、Azure サービスに Mobile Apps Node.js バックエンドを発行するための多数のメカニズムが提供されます。 これらのメカニズムには、Visual Studio に統合されたデプロイ ツール、コマンドライン ツール、ソース管理に基づく継続的なデプロイ オプションも含まれます。 詳細については、[Azure App Service のデプロイ ガイド]に関するページを参照してください。

Azure App Service には、バックエンドを公開する前に確認する必要がある Node.js アプリケーションに関する具体的なアドバイスがあります。

* 方法: [Node のバージョンを指定する]
* [Node モジュールを使用する]

### <a name="howto-enable-homepage"></a>アプリケーションのホーム ページを有効にする

多くのアプリケーションは、Web アプリとモバイル アプリの組み合わせです。 ExpressJS フレームワークを使用すると、2 つのファセットを結合することができます。 ただし、モバイル インターフェイスのみを実装する場合もあります。 アプリ サービスを確実に稼動させるために、ホーム ページを用意すると便利です。 ホーム ページを指定するか、一時的なホーム ページを有効にします。 一時的なホーム ページを有効にするには、次のコードを使用して Mobile Apps をインスタンス化します。

```javascript
var mobile = azureMobileApps({ homePage: true });
```

ローカルで開発するときにのみ、このオプションを利用する場合は、この設定を azureMobile.js ファイルに追加します。

## <a name="TableOperations"></a>テーブル操作

azure-mobile-apps Node.js Server SDK では、Web API として Azure SQL Database に格納されたデータ テーブルを公開するためのメカニズムが提供されます。 以下の 5 つの操作が提供されます。

| Operation | 説明 |
| --- | --- |
| GET /tables/*tablename* |テーブルのすべてのレコードを取得します。 |
| GET /tables/*tablename*/:id |テーブルの特定のレコードを取得します。 |
| POST /tables/*tablename* |テーブルのレコードを作成します。 |
| PATCH /tables/*tablename*/:id |テーブルのレコードを更新します。 |
| DELETE /tables/*tablename*/:id |テーブルのレコードを削除します。 |

この Web API は [OData] をサポートし、テーブル スキーマを拡張して[オフライン データ同期]をサポートします。

### <a name="howto-dynamicschema"></a>動的スキーマを使用してテーブルを定義する

テーブルを使用する前に、テーブルを定義する必要があります。 静的スキーマで (スキーマ内の列を定義する場合)、または動的に (SDK が受信要求に基づいてスキーマを制御する場合) テーブルを定義できます。 さらに、定義に JavaScript コードを追加することで、Web API の特定の側面を制御できます。

ベスト プラクティスとして、`tables` ディレクトリ内の JavaScript ファイルに各テーブルを定義し、`tables.import()` メソッドを使用してテーブルをインポートする必要があります。 basic-app サンプルを拡張して、app.js ファイルを次のように調整します。

```javascript
var express = require('express'),
    azureMobileApps = require('azure-mobile-apps');

var app = express(),
    mobile = azureMobileApps();

// Define the database schema that is exposed.
mobile.tables.import('./tables');

// Provide initialization of any tables that are statically defined.
mobile.tables.initialize().then(function () {
    // Add the Mobile API so it is accessible as a Web API.
    app.use(mobile);

    // Start listening on HTTP.
    app.listen(process.env.PORT || 3000);
});
```

次のように ./tables/TodoItem.js にテーブルを定義します。

```javascript
var azureMobileApps = require('azure-mobile-apps');

var table = azureMobileApps.table();

// Additional configuration for the table goes here.

module.exports = table;
```

テーブルでは、既定で動的スキーマが使用されます。 動的スキーマをグローバルに無効にするには、Azure Portal で `MS_DynamicSchema` アプリ設定を false に設定します。

完全な例は [GitHub の todo サンプル]にあります。

### <a name="howto-staticschema"></a>静的スキーマを使用してテーブルを定義する

Web API を使用して公開する列を明示的に定義することができます。 azure-mobile-apps Node.js SDK では、オフライン データ同期に必要なその他の列が指定したリストに自動的に追加されます。 たとえば、クイックスタート クライアント アプリケーションには、`text` (文字列) と `complete` (ブール値) という 2 つの列を持つテーブルが必要になります。  
このテーブルは、次のように、(`tables` ディレクトリにある) テーブル定義 JavaScript ファイルで定義できます。

```javascript
var azureMobileApps = require('azure-mobile-apps');

var table = azureMobileApps.table();

// Define the columns within the table.
table.columns = {
    "text": "string",
    "complete": "boolean"
};

// Turn off the dynamic schema.
table.dynamicSchema = false;

module.exports = table;
```

静的にテーブルを定義する場合は、`tables.initialize()` メソッドを呼び出して起動時にデータベース スキーマを作成する必要もあります。 Web サービスがデータベースの初期化前に要求を処理しないように、`tables.initialize()` メソッドは [promise] を返します。

### <a name="howto-sqlexpress-setup"></a>ローカル コンピューター上で開発データ ストアとして SQL Server Express を使用する

Mobile Apps Node.js SDK には、すぐに利用できるデータを提供する 3 つのオプションが用意されています。

* **メモリ** ドライバーを使用して、非永続ストアの例を提供します。
* **mssql** ドライバーを使用して、開発用の SQL Server Express データ ストアを提供します。
* **mssql** ドライバーを使用して、運用環境の Azure SQL Database データ ストアを提供します。

Mobile Apps Node.js SDK では [mssql Node.js パッケージ] を使用して、SQL Server Express と SQL Database 両方への接続を確立および使用します。 このパッケージでは、SQL Server Express インスタンスで TCP 接続を有効にする必要があります。

> [!TIP]
> メモリ ドライバーでは、テスト用の完全な機能セットは提供されません。 ローカルでバックエンドをテストする場合は、SQL Server Express データ ストアと mssql ドライバーを使用することをお勧めします。

1. [Microsoft SQL Server 2014 Express]をダウンロードしてインストールします。 必ず、SQL Server 2014 Express with Tools エディションをインストールしてください。 64 ビット サポートを明示的に要求した場合を除き、32 ビット版を使用することで実行時のメモリ使用量が少なくなります。
1. SQL Server 2014 構成マネージャーを実行します。

   a. ツリー メニューの **[SQL Server ネットワークの構成]** ノードを展開します。

   b. **[SQLEXPRESS のプロトコル]** を選択します。

   c. **[TCP/IP]** を右クリックし、**[有効化]** を選択します。 ポップアップ ダイアログ ボックスで **[OK]** を選択します。

   d. **[TCP/IP]** を右クリックし、**[プロパティ]** を選択します。

   e. **[IP アドレス]** タブを選択します。

   f. **[IPAll]** ノードを見つけます。 **[TCP ポート]** フィールドに、「**1433**」と入力します。

      ![TCP/IP 用に SQL Server Express を構成する][3]

   g. **[OK]** を選択します。 ポップアップ ダイアログ ボックスで **[OK]** を選択します。

   h. ツリー メニューの **[SQL Server のサービス]** を選択します。

   i. **[SQL Server (SQLEXPRESS)]** を右クリックし、**[再起動]** を選択します。

   j. SQL Server 2014 構成マネージャーを閉じます。

1. SQL Server 2014 Management Studio を実行して、ローカルの SQL Server Express インスタンスに接続します。

   1. オブジェクト エクスプローラーでインスタンスを右クリックし、**[プロパティ]** を選択します。
   1. **[セキュリティ]** ページを選択します。
   1. **[SQL Server 認証モードと Windows 認証モード]** が選択されていることを確認します。
   1. **[OK]** を選択します。

      ![SQL Server Express 認証の構成][4]
   1. オブジェクト エクスプローラーで **[セキュリティ]** > **[ログイン]** の順に展開します。
   1. **[ログイン]** を右クリックし、**[新しいログイン]** を選択します。
   1. ログイン名を入力します。 **[SQL Server 認証]** を選択します。 パスワードを入力し、**[パスワードの確認入力]** に同じパスワードを入力します。 パスワードは、Windows の複雑さの要件を満たしている必要があります。
   1. **[OK]** を選択します。

      ![SQL Server Express に新しいユーザーを追加する][5]
   1. 新しいログインを右クリックし、**[プロパティ]** を選択します。
   1. **[サーバー ロール]** ページを選択します。
   1. **dbcreator** サーバー ロールのチェック ボックスをオンにします。
   1. **[OK]** を選択します。
   1. SQL Server 2015 Management Studio を閉じます。

選択したユーザー名とパスワードは必ずメモしておきます。 データベース要件によっては、他のサーバーの役割またはアクセス許可の割り当てが必要になる場合があります。

Node.js アプリケーションは、`SQLCONNSTR_MS_TableConnectionString` 環境変数を読み取って、このデータベースの接続文字列を確認します。 ご使用の環境にこの変数を設定できます。 たとえば、PowerShell を使用して、以下のようにこの環境変数を設定することができます。

    $env:SQLCONNSTR_MS_TableConnectionString = "Server=127.0.0.1; Database=mytestdatabase; User Id=azuremobile; Password=T3stPa55word;"

TCP/IP 接続を介してデータベースにアクセスします。 接続に使用するユーザー名とパスワードを入力します。

### <a name="howto-config-localdev"></a>ローカル開発用のプロジェクトを構成する

Mobile Apps は、ローカル ファイルシステムから *azureMobile.js* という JavaScript ファイルを読み取ります。 運用環境の Mobile Apps SDK の構成にはこのファイルを使用しないでください。 運用環境では、[Azure Portal] の **[アプリ設定]** を使用してください。

azureMobile.js ファイルでは構成オブジェクトをエクスポートする必要があります。 最も一般的な設定は次のとおりです。

* データベース設定
* 診断ログ設定
* 代替 CORS 設定

前のデータベース設定を実装する **azureMobile.js** ファイルの例を次に示します。

```javascript
module.exports = {
    cors: {
        origins: [ 'localhost' ]
    },
    data: {
        provider: 'mssql',
        server: '127.0.0.1',
        database: 'mytestdatabase',
        user: 'azuremobile',
        password: 'T3stPa55word'
    },
    logging: {
        level: 'verbose'
    }
};
```

パスワードがクラウドに保存されないように、**azureMobile.js** を **.gitignore** ファイル (または他のソース コード管理の無視ファイル) に追加することをお勧めします。 運用環境の設定は、必ず [Azure Portal] の **[アプリ設定]** で構成してください。

### <a name="howto-appsettings"></a>モバイル アプリのアプリ設定を構成する

azureMobile.js ファイル内のほとんどの設定には、[Azure Portal] 内に対応するアプリ設定があります。 次の一覧を参照して、**[アプリ設定]** でアプリを構成してください。

| アプリ設定 | azureMobile.js setting | 説明 | 有効な値 |
|:--- |:--- |:--- |:--- |
| **MS_MobileAppName** |name |アプリの名前 |文字列 |
| **MS_MobileLoggingLevel** |logging.level |ログ記録するメッセージの最小ログ レベル |error、warning、info、verbose、debug、silly |
| **MS_DebugMode** |debug |デバッグ モードを有効または無効にします |true、false |
| **MS_TableSchema** |data.schema |SQL テーブルの既定のスキーマ名 |string (既定: dbo) |
| **MS_DynamicSchema** |data.dynamicSchema |デバッグ モードを有効または無効にします |true、false |
| **MS_DisableVersionHeader** |version (undefined に設定) |X-ZUMO-Server-Version ヘッダーの無効化 |true、false |
| **MS_SkipVersionCheck** |skipversioncheck |クライアント API バージョン チェックの無効化 |true、false |

アプリ設定を設定するには:

1. [Azure Portal] にサインインします。
1. **[すべてのリソース]** または **[App Services]** を選択し、モバイル アプリの名前をクリックします。
1. **[設定]** ウィンドウが既定で開きます。 開かない場合は、**[すべての設定]** を選択します。
1. **[全般]** メニューの **[アプリケーション設定]** を選択します。
1. **[アプリ設定]** セクションまでスクロールします。
1. アプリ設定が既に存在する場合は、アプリ設定の値を選択して値を編集します。
   アプリ設定が存在しない場合は、**[キー]** ボックスにアプリ設定を入力し、**[値]** ボックスに値を入力します。
1. **[保存]** を選択します。

ほとんどのアプリ設定を変更した場合、サービスの再起動が必要になります。

### <a name="howto-use-sqlazure"></a>SQL Database を運用データ ストアとして使用する

<!--- ALTERNATE INCLUDE - we can't use ../includes/app-service-mobile-dotnet-backend-create-new-service.md - slightly different semantics -->

Azure SQL Database をデータ ストアとして使用する方法は、Azure App Service アプリケーションのすべての種類で同じです。 Mobile Apps バックエンドをまだ作成していない場合は、次の手順に従って作成します。

1. [Azure Portal] にサインインします。
1. ウィンドウの左上で、**[+ 新規]**、**[Web + モバイル]** > **[モバイル アプリ]** の順に選択し、Mobile Apps バックエンドの名前を指定します。
1. **[リソース グループ]** ボックスで、アプリと同じ名前を入力します。
1. 既定の App Service プランが選択されています。 App Service プランを変更する場合:

   a. **[App Service プラン]** > **[+ 新規作成]** の順に選択します。

   b. 新しい App Service プランの名前を指定し、適切な場所を選択します。

   c. サービスの適切な価格レベルを選択します。 **[すべて表示]** を選択して、**Free** や **Shared** などの価格オプションをさらに表示します。

   d. **[選択]** ボタンをクリックします。

   e. **[App Service プラン]** ウィンドウに戻り、**[OK]** を選択します。
1. **作成**を選択します。

Mobile Apps バックエンドのプロビジョニングには数分かかる場合があります。 Mobile Apps バックエンドのプロビジョニングが完了すると、ポータルで Mobile Apps バックエンドの **[設定]** ウィンドウが開きます。

既存の SQL データベースを Mobile Apps バックエンドに接続するか、新しい SQL データベースを作成するかを選択できます。 このセクションでは、SQL データベースを作成します。

> [!NOTE]
> Mobile Apps バックエンドと同じ場所に、既にデータベースがある場合は、**[既存のデータベースを使用する]** を選択すると、そのデータベースを選択できます。 別の場所にあるデータベースを使用すると、待機時間が増加するため、これはお勧めできません。

1. 新しい Mobile Apps バックエンドで、**[設定]** > **[モバイル アプリ]** > **[データ]** > **[+ 追加]** の順に選択します。
1. **[データ接続の追加]** ウィンドウで、**[SQL Database - 必要な設定の構成]** > **[新しいデータベースの作成]** の順に選択します。 **[名前]** ボックスに新しいデータベースの名前を入力します。
1. **[サーバー]** を選択します。 **[新しいサーバー]** ウィンドウで、**[サーバー名]** ボックスに一意のサーバー名を入力し、サーバー管理ログインとパスワードに適切な内容を指定します。 **[Azure サービスにサーバーへのアクセスを許可する]** が選択されていることを確認します。 **[OK]** を選択します。

   ![Azure SQL データベースの作成][6]
1. **[新しいデータベース]** ウィンドウで、**[OK]** を選択します。
1. **[データ接続の追加]** ウィンドウに戻り、**[接続文字列]** を選択して、データベースの作成時に指定したログイン名とパスワードを入力します。 既存のデータベースを使用する場合は、そのデータベースのログイン資格情報を入力します。 **[OK]** を選択します。
1. もう一度 **[データ接続の追加]** ウィンドウに戻り、**[OK]** を選択してデータベースを作成します。

<!--- END OF ALTERNATE INCLUDE -->

データベースの作成には数分かかる場合があります。 **[通知]** 領域を使用して、デプロイの進行状況を監視します。 データベースのデプロイが正常に完了するまで、先に進まないでください。 データベースがデプロイされると、Mobile Apps バックエンドの [アプリ設定] で、SQL データベース インスタンスの接続文字列が作成されます。 このアプリ設定を確認するには、**[設定]** > **[アプリケーション設定]** > **[接続文字列]** を順に選択します。

### <a name="howto-tables-auth"></a>テーブルへのアクセスに認証を要求する

`tables` エンドポイントで App Service 認証を使用する場合は、まず、[Azure Portal] で App Service 認証を構成する必要があります。 詳細については、使用する ID プロバイダーの構成ガイドを参照してください。

* [Azure Active Directory 認証を構成する]
* [Facebook 認証を構成する]
* [Google 認証を構成する]
* [Microsoft 認証を構成する]
* [Twitter 認証を構成する]

各テーブルには、テーブルへのアクセスを制御するために使用できる access プロパティがあります。 次のサンプルでは、認証を必要とする静的に定義されたテーブルを示します。

```javascript
var azureMobileApps = require('azure-mobile-apps');

var table = azureMobileApps.table();

// Define the columns within the table.
table.columns = {
    "text": "string",
    "complete": "boolean"
};

// Turn off the dynamic schema.
table.dynamicSchema = false;

// Require authentication to access the table.
table.access = 'authenticated';

module.exports = table;
```

access プロパティには、次の 3 つの値を使用できます。

* *anonymous* は、認証なしでデータを読み取る許可をクライアント アプリケーションに与えることを示します。
* *authenticated* は、クライアント アプリケーションが要求で有効な認証トークンを送信する必要があることを示します。
* *disabled* は、このテーブルが現在無効になっていることを示します。

access プロパティが定義されていない場合は、非認証アクセスが許可されます。

### <a name="howto-tables-getidentity"></a>テーブルで認証要求を使用する
認証が設定されている場合、要求されている各種要求を設定できます。 これらの要求は `context.user` オブジェクトでは通常使用できません。 ただし、`context.user.getIdentity()` メソッドを使用して取得できます。 `getIdentity()` メソッドは、オブジェクトに解決される promise を返します。 オブジェクトのキーは、認証方法 (`facebook`、`google`、`twitter`、`microsoftaccount`、`aad`) に設定されます。

たとえば、Microsoft アカウント認証を設定し、電子メール アドレス要求を要求する場合は、次のテーブル コントローラーを使用してレコードに電子メール アドレスを追加できます。

```javascript
var azureMobileApps = require('azure-mobile-apps');

// Create a new table definition.
var table = azureMobileApps.table();

table.columns = {
    "emailAddress": "string",
    "text": "string",
    "complete": "boolean"
};
table.dynamicSchema = false;
table.access = 'authenticated';

/**
* Limit the context query to those records with the authenticated user email address
* @param {Context} context the operation context
* @returns {Promise} context execution Promise
*/
function queryContextForEmail(context) {
    return context.user.getIdentity().then((data) => {
        context.query.where({ emailAddress: data.microsoftaccount.claims.emailaddress });
        return context.execute();
    });
}

/**
* Adds the email address from the claims to the context item - used for
* insert operations
* @param {Context} context the operation context
* @returns {Promise} context execution Promise
*/
function addEmailToContext(context) {
    return context.user.getIdentity().then((data) => {
        context.item.emailAddress = data.microsoftaccount.claims.emailaddress;
        return context.execute();
    });
}

// Configure specific code when the client does a request.
// READ: only return records that belong to the authenticated user.
table.read(queryContextForEmail);

// CREATE: add or overwrite the userId based on the authenticated user.
table.insert(addEmailToContext);

// UPDATE: only allow updating of records that belong to the authenticated user.
table.update(queryContextForEmail);

// DELETE: only allow deletion of records that belong to the authenticated user.
table.delete(queryContextForEmail);

module.exports = table;
```

どのような要求が使用できるかを確認するには、Web ブラウザーを使用し、サイトの `/.auth/me` エンドポイントを表示します。

### <a name="howto-tables-disabled"></a>特定のテーブル操作へのアクセスを無効にする

テーブルでの表示だけでなく、個々の操作を制御するために access プロパティを使用できます。 操作には以下の 4 つがあります。

* `read` は、テーブルに対する RESTful GET 操作です。
* `insert` は、テーブルに対する RESTful POST 操作です。
* `update` は、テーブルに対する RESTful PATCH 操作です。
* `delete` は、テーブルに対する RESTful DELETE 操作です。

たとえば、読み取り専用の認証されていないテーブルを指定できます。

```javascript
var azureMobileApps = require('azure-mobile-apps');

var table = azureMobileApps.table();

// Read-only table. Only allow READ operations.
table.read.access = 'anonymous';
table.insert.access = 'disabled';
table.update.access = 'disabled';
table.delete.access = 'disabled';

module.exports = table;
```

### <a name="howto-tables-query"></a>テーブル操作で使用されるクエリを調整する

テーブル操作の一般的な要件は、データの制限付きビューを提供することです。 たとえば、ユーザーが独自のレコードの読み取りまたは更新のみを実行できるように、認証済みユーザー ID でタグ付けされているテーブルを提供できます。 次のテーブル定義では、この機能を提供しています。

```javascript
var azureMobileApps = require('azure-mobile-apps');

var table = azureMobileApps.table();

// Define a static schema for the table.
table.columns = {
    "userId": "string",
    "text": "string",
    "complete": "boolean"
};
table.dynamicSchema = false;

// Require authentication for this table.
table.access = 'authenticated';

// Ensure that only records for the authenticated user are retrieved.
table.read(function (context) {
    context.query.where({ userId: context.user.id });
    return context.execute();
});

// When adding records, add or overwrite the userId with the authenticated user.
table.insert(function (context) {
    context.item.userId = context.user.id;
    return context.execute();
});

module.exports = table;
```

通常はクエリを実行する操作には、`where` 句で調整できる query プロパティがあります。 query プロパティは [QueryJS] オブジェクトであり、これを使用して、データ バックエンドで処理できるものに OData クエリを変換します。 (上記のような) 単純な等式の場合は、マップを使用できます。 また、特定の SQL 句を追加することもできます。

```javascript
context.query.where('myfield eq ?', 'value');
```

### <a name="howto-tables-softdelete"></a>テーブルの論理削除を構成する

論理削除では実際にレコードは削除されません。 代わりに、削除列を true に設定して、データベース内のレコードを削除済みとしてマークします。 Mobile Client SDK で `IncludeDeleted()` が使用されない限り、Mobile Apps SDK によって結果から論理削除レコードが自動的に削除されます。 論理削除のテーブルを構成するには、テーブル定義ファイルで `softDelete` プロパティを設定します。

```javascript
var azureMobileApps = require('azure-mobile-apps');

var table = azureMobileApps.table();

// Define the columns within the table.
table.columns = {
    "text": "string",
    "complete": "boolean"
};

// Turn off the dynamic schema.
table.dynamicSchema = false;

// Turn on soft delete.
table.softDelete = true;

// Require authentication to access the table.
table.access = 'authenticated';

module.exports = table;
```

クライアント アプリケーション、WebJob、Azure 関数、カスタム API など、レコードを削除するためのメカニズムを確立する必要があります。

### <a name="howto-tables-seeding"></a>データベースに対するデータのシード処理を実行する

新しいアプリケーションを作成する場合、データでのテーブルのシード処理が必要になることがあります。 これは、以下のようにテーブル定義 JavaScript ファイル内で行うことができます。

```javascript
var azureMobileApps = require('azure-mobile-apps');

var table = azureMobileApps.table();

// Define the columns within the table.
table.columns = {
    "text": "string",
    "complete": "boolean"
};
table.seed = [
    { text: 'Example 1', complete: false },
    { text: 'Example 2', complete: true }
];

// Turn off the dynamic schema.
table.dynamicSchema = false;

// Require authentication to access the table.
table.access = 'authenticated';

module.exports = table;
```

データのシード処理は、Mobile Apps SDK を使用してテーブルを作成した場合にのみ発生します。 データベースにテーブルが既に存在する場合、テーブルにデータは挿入されません。 動的スキーマが有効になっている場合、スキーマはシード処理されたデータから推論されます。

`tables.initialize()` メソッドを明示的に呼び出して、サービスの実行開始時にテーブルを作成することをお勧めします。

### <a name="Swagger"></a>Swagger のサポートを有効にする
Mobile Apps には、組み込みの [Swagger] のサポートが付属しています。 Swagger のサポートを有効にするには、最初に依存関係として swagger ui をインストールします。

    npm install --save swagger-ui

インストール後は Mobile Apps コンストラクターで Swagger のサポートを有効にすることができます。

```javascript
var mobile = azureMobileApps({ swagger: true });
```

開発エディションでのみ Swagger サポートを有効にするには、 `NODE_ENV` アプリ設定を使用します。

```javascript
var mobile = azureMobileApps({ swagger: process.env.NODE_ENV !== 'production' });
```

`swagger` エンドポイントは、 http://*自分のサイト*.azurewebsites.net/swagger にあります。 Swagger UI には、 `/swagger/ui` エンドポイントからアクセスできます。 アプリケーション全体で認証を必要とする場合、エラーが生成されます。 最良の結果を得るには、Azure App Service の [認証/承認] 設定で認証されていない要求を許可し、`table.access` プロパティを使用して認証を制御します。

また、ローカルで開発する場合にのみ、Swagger のサポートが必要な場合は、azureMobile.js ファイルに Swagger オプションを追加できます。

## <a name="a-namepushpush-notifications"></a><a name="push"/>プッシュ通知

Mobile Apps と Azure Notification Hubs を統合することで、あらゆる主要なプラットフォーム間で、数百万台のデバイスに対して、対象設定済みのプッシュ通知を送信できます。 Notification Hubs を使用して、iOS デバイス、Android デバイス、および Windows デバイスにプッシュ通知を送信できます。 Notification Hubs で実行可能なすべての操作については、「[Azure 通知ハブ](../notification-hubs/notification-hubs-push-notification-overview.md)」を参照してください。

### <a name="send-push"></a>プッシュ通知の送信

次のコードに、`push` オブジェクトを使用して、登録済みの iOS デバイスにブロードキャスト プッシュ通知を送信する方法を示します。

```javascript
// Create an APNS payload.
var payload = '{"aps": {"alert": "This is an APNS payload."}}';

// Only do the push if configured.
if (context.push) {
    // Send a push notification by using APNS.
    context.push.apns.send(null, payload, function (error) {
        if (error) {
            // Do something or log the error.
        }
    });
}
```

クライアントからテンプレート プッシュ登録を作成することで、代わりに、サポートされるすべてのプラットフォーム上のデバイスにテンプレート プッシュ メッセージを送信できます。 次のコードに、テンプレート通知を送信する方法を示します。

```javascript
// Define the template payload.
var payload = '{"messageParam": "This is a template payload."}';

// Only do the push if configured.
if (context.push) {
    // Send a template notification.
    context.push.send(null, payload, function (error) {
        if (error) {
            // Do something or log the error.
        }
    });
}
```

### <a name="push-user"></a>タグを使用して認証済みのユーザーにプッシュ通知を送信する
認証済みのユーザーがプッシュ通知に登録すると、ユーザー ID タグが登録に自動的に追加されます。 このタグを使用すると、特定のユーザーが登録したすべてのデバイスにプッシュ通知を送信できます。 次のコードでは、要求を行ったユーザーの SID を取得し、そのユーザーのすべてのデバイス登録にテンプレート プッシュ通知を送信します。

```javascript
// Only do the push if configured.
if (context.push) {
    // Send a notification to the current user.
    context.push.send(context.user.id, payload, function (error) {
        if (error) {
            // Do something or log the error.
        }
    });
}
```

認証されたクライアントからプッシュ通知を登録する場合は、登録を試みる前に、認証が完了していることを確認します。

## <a name="CustomAPI"></a> カスタム API

### <a name="howto-customapi-basic"></a>カスタム API を定義する

`/tables` エンドポイント経由のデータ アクセス API に加え、Mobile Apps ではカスタム API も提供できます。 カスタム API はテーブル定義と同じような方法で定義され、認証を含む、すべての同じ機能にアクセスできます。

カスタム API で App Service 認証を使用する場合は、まず、[Azure Portal] で App Service 認証を構成する必要があります。 詳細については、使用する ID プロバイダーの構成ガイドを参照してください。

* [Azure Active Directory 認証を構成する]
* [Facebook 認証を構成する]
* [Google 認証を構成する]
* [Microsoft 認証を構成する]
* [Twitter 認証を構成する]

カスタム API は、テーブル API とほぼ同じ方法で定義されます。

1. `api` ディレクトリを作成します。
1. `api` ディレクトリに API 定義 JavaScript ファイルを作成します。
1. import メソッドを使用して、`api` ディレクトリをインポートします。

前に使用した basic-app サンプルに基づくプロトタイプの API 定義を以下に示します。

```javascript
var express = require('express'),
    azureMobileApps = require('azure-mobile-apps');

var app = express(),
    mobile = azureMobileApps();

// Import the custom API.
mobile.api.import('./api');

// Add the Mobile API so it is accessible as a Web API.
app.use(mobile);

// Start listening on HTTP
app.listen(process.env.PORT || 3000);
```

ここでは、`Date.now()` メソッドを使用してサーバーの日付を返す API の例を見てみましょう。 api/date.js ファイルを以下に示します。

```javascript
var api = {
    get: function (req, res, next) {
        var date = { currentTime: Date.now() };
        res.status(200).type('application/json').send(date);
    });
};

module.exports = api;
```

各パラメーターは、標準的な RESTful 動詞(GET、POST、PATCH または DELETE) のいずれかです。 メソッドは、必要な出力を送信する標準的な [ExpressJS ミドルウェア]関数です。

### <a name="howto-customapi-auth"></a>カスタム API へのアクセスに認証を要求する

Mobile Apps SDK では、`tables` エンドポイントとカスタム API の両方に対して同じ方法で認証を実装します。 前のセクションで開発した API に認証を追加するには、`access` プロパティを追加します。

```javascript
var api = {
    get: function (req, res, next) {
        var date = { currentTime: Date.now() };
        res.status(200).type('application/json').send(date);
    });
};
// All methods must be authenticated.
api.access = 'authenticated';

module.exports = api;
```

以下のように、特定の操作で認証を指定することもできます。

```javascript
var api = {
    get: function (req, res, next) {
        var date = { currentTime: Date.now() };
        res.status(200).type('application/json').send(date);
    }
};
// The GET methods must be authenticated.
api.get.access = 'authenticated';

module.exports = api;
```

認証を必要とするカスタム API には、`tables` エンドポイントで使用されるものと同じトークンを使用する必要があります。

### <a name="howto-customapi-auth"></a>大きなファイルのアップロードを処理する

Mobile Apps SDK では、[body-parser ミドルウェア](https://github.com/expressjs/body-parser)を使用して、送信された本文のコンテンツを受け入れ、デコードします。 大きなファイルのアップロードを受け入れるように body-parser を事前構成できます。

```javascript
var express = require('express'),
    bodyParser = require('body-parser'),
    azureMobileApps = require('azure-mobile-apps');

var app = express(),
    mobile = azureMobileApps();

// Set up large body content handling.
app.use(bodyParser.json({ limit: '50mb' }));
app.use(bodyParser.urlencoded({ limit: '50mb', extended: true }));

// Import the custom API.
mobile.api.import('./api');

// Add the Mobile API so it is accessible as a Web API.
app.use(mobile);

// Start listening on HTTP.
app.listen(process.env.PORT || 3000);
```

ファイルは送信前に base 64 でエンコードされます。 このエンコードによって実際のアップロードのサイズは増加します (そのため、このサイズを考慮する必要があります)。

### <a name="howto-customapi-sql"></a>カスタム SQL ステートメントを実行する

Mobile Apps SDK を使用すると、要求オブジェクトを介してコン​​テキスト全体にアクセスできます。 定義されたデータ プロバイダーに対して、パラメーター化された SQL ステートメントを簡単に実行できます。

```javascript
var api = {
    get: function (request, response, next) {
        // Check for parameters. If not there, pass on to a later API call.
        if (typeof request.params.completed === 'undefined')
            return next();

        // Define the query. Anything that the mssql
        // driver can handle is allowed.
        var query = {
            sql: 'UPDATE TodoItem SET complete=@completed',
            parameters: [{
                completed: request.params.completed
            }]
        };

        // Execute the query. The context for Mobile Apps is available through
        // request.azureMobile. The data object contains the configured data provider.
        request.azureMobile.data.execute(query)
        .then(function (results) {
            response.json(results);
        });
    }
};

api.get.access = 'authenticated';
module.exports = api;
```

## <a name="Debugging"></a>デバッグ、Easy Tables、Easy API

### <a name="howto-diagnostic-logs"></a>Mobile Apps をデバッグ、診断、およびトラブルシューティングする

Azure App Service では、Node.js アプリケーションに関するいくつかのデバッグとトラブルシューティングの手法が提供されます。
Node.js Mobile Apps バックエンドのトラブルシューティングを開始する場合は、次の記事を参照してください。

* [Azure App Service の監視]
* [Azure App Service での診断ログの有効化]
* [Visual Studio での Azure App Service のトラブルシューティング]

Node.js アプリケーションは、広範囲の診断ログ ツールにアクセスできます。 Mobile Apps Node.js SDK は、内部で診断ログに [Winston] を使用します。 デバッグ モードを有効にするか、[Azure Portal] で `MS_DebugMode` アプリ設定を true に設定すると、ログは自動的に有効になります。 生成されたログは、[Azure Portal] の診断ログに表示されます。

### <a name="in-portal-editing"></a><a name="work-easy-tables"></a>Azure Portal で Easy Tables を使用する

[テーブルの簡単操作] を使用すると、ポータル内でテーブルをすぐに作成して操作できます。 CSV 形式で、Easy Tables にデータセットをアップロードできます。 Mobile Apps バックエンドのシステム プロパティ名と競合するプロパティ名 (お使いの CSV データセット内) は使用できないことに注意してください。 システム プロパティ名は次のとおりです。
* createdAt
* updatedAt
* deleted
* version

App Service Editor を使用してテーブルの操作を編集することもできます。 バックエンド サイトの設定で **[Easy Tables]** を選択すると、テーブルを追加、変更、または削除できます。 さらに、テーブル内のデータを表示することもできます。

![Work with Easy Tables](./media/app-service-mobile-node-backend-how-to-use-server-sdk/mobile-apps-easy-tables.png)

テーブル用のコマンド バーには、次のコマンドが用意されています。

* **[アクセス許可の変更]**:テーブルに対する読み取り、挿入、更新、削除操作のアクセス許可を変更します。
 匿名アクセスを許可するオプション、認証を要求するオプション、操作に対するすべてのアクセスを無効にするオプションがあります。
* **[スクリプトの編集]**:テーブルのスクリプト ファイルは、App Service Editor で開きます。
* **[スキーマの管理]**:列の追加または削除やテーブルのインデックスの変更を実行します。
* **[テーブルのクリア]**:既存のテーブルですべてのデータ行が削除されるように切り捨てます。ただし、スキーマは変更されません。
* **[行の削除]**:個々のデータ行を削除します。
* **[ストリーミング ログの表示]**:サイトのストリーミング ログ サービスに接続します。

### <a name="work-easy-apis"></a>Azure Portal で [API の簡単操作] を使用する

[API の簡単操作] を使用すると、ポータル内でカスタム API をすぐに作成して操作できます。 App Service Editor を使用して、API のスクリプトを編集できます。

バックエンド サイトの設定で **[API の簡単操作]** を選択すると、カスタム API エンドポイントを追加、変更、または削除できます。

![Work with Easy APIs](./media/app-service-mobile-node-backend-how-to-use-server-sdk/mobile-apps-easy-apis.png)

ポータルでは、HTTP アクションのアクセス許可を変更できます。また、App Service Editor で API スクリプト ファイルを編集したり、ストリーミング ログを表示したりできます。

### <a name="online-editor"></a>App Service Editor でコードを編集する

Azure Portal では、ローカル コンピューターにプロジェクトをダウンロードする必要なく、App Service Editor を使用して Node.js バックエンド スクリプト ファイルを編集できます。 オンライン エディターでスクリプト ファイルを編集するには、次の手順に従います。

1. Mobile Apps バックエンドのウィンドウで、**[すべての設定]** を選択し、**[Easy Tables]** または **[API の簡単操作]** を選択します。 テーブルまたは API を選択し、**[スクリプトの編集]** を選択します。 App Service Editor でスクリプト ファイルが開きます。

   ![App Service Editor](./media/app-service-mobile-node-backend-how-to-use-server-sdk/mobile-apps-visual-studio-editor.png)
1. オンライン エディターで、コード ファイルを変更します。 変更内容は、入力時に自動的に保存されています。

<!-- Images -->
[0]: ./media/app-service-mobile-node-backend-how-to-use-server-sdk/npm-init.png
[1]: ./media/app-service-mobile-node-backend-how-to-use-server-sdk/vs2015-new-project.png
[2]: ./media/app-service-mobile-node-backend-how-to-use-server-sdk/vs2015-install-npm.png
[3]: ./media/app-service-mobile-node-backend-how-to-use-server-sdk/sqlexpress-config.png
[4]: ./media/app-service-mobile-node-backend-how-to-use-server-sdk/sqlexpress-authconfig.png
[5]: ./media/app-service-mobile-node-backend-how-to-use-server-sdk/sqlexpress-newuser-1.png
[6]: ./media/app-service-mobile-node-backend-how-to-use-server-sdk/dotnet-backend-create-db.png

<!-- URLs -->
[Android クライアントのクイックスタート]: app-service-mobile-android-get-started.md
[Apache Cordova クライアントのクイックスタート]: app-service-mobile-cordova-get-started.md
[iOS クライアントのクイックスタート]: app-service-mobile-ios-get-started.md
[Xamarin.iOS クライアントのクイックスタート]: app-service-mobile-xamarin-ios-get-started.md
[Xamarin.Android クライアントのクイックスタート]: app-service-mobile-xamarin-android-get-started.md
[Xamarin.Forms クライアントのクイックスタート]: app-service-mobile-xamarin-forms-get-started.md
[Windows ストア クライアントのクイックスタート]: app-service-mobile-windows-store-dotnet-get-started.md
[オフライン データ同期]: app-service-mobile-offline-data-sync.md
[Azure Active Directory 認証を構成する]: ../app-service/configure-authentication-provider-aad.md
[Facebook 認証を構成する]: ../app-service/configure-authentication-provider-facebook.md
[Google 認証を構成する]: ../app-service/configure-authentication-provider-google.md
[Microsoft 認証を構成する]: ../app-service/configure-authentication-provider-microsoft.md
[Twitter 認証を構成する]: ../app-service/configure-authentication-provider-twitter.md
[Azure App Service のデプロイ ガイド]: ../app-service/deploy-local-git.md
[Azure App Service の監視]: ../app-service/web-sites-monitor.md
[Azure App Service での診断ログの有効化]: ../app-service/troubleshoot-diagnostic-logs.md
[Visual Studio での Azure App Service のトラブルシューティング]: ../app-service/troubleshoot-dotnet-visual-studio.md
[Node のバージョンを指定する]: ../nodejs-specify-node-version-azure-apps.md
[Node モジュールを使用する]: ../nodejs-use-node-modules-azure-apps.md
[Create a new Azure App Service]: ../app-service/
[azure-mobile-apps]: https://www.npmjs.com/package/azure-mobile-apps
[Express]: https://expressjs.com/
[Swagger]: https://swagger.io/

[Azure Portal]: https://portal.azure.com/
[OData]: https://www.odata.org
[Promise]: https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise
[GitHub の basicapp サンプル]: https://github.com/azure/azure-mobile-apps-node/tree/master/samples/basic-app
[GitHub の todo サンプル]: https://github.com/azure/azure-mobile-apps-node/tree/master/samples/todo
[GitHub の samples ディレクトリ]: https://github.com/azure/azure-mobile-apps-node/tree/master/samples
[static-schema sample on GitHub]: https://github.com/azure/azure-mobile-apps-node/tree/master/samples/static-schema
[QueryJS]: https://github.com/Azure/queryjs
[Node.js Tools 1.1 for Visual Studio]: https://github.com/Microsoft/nodejstools/releases/tag/v1.1-RC.2.1
[mssql Node.js パッケージ]: https://www.npmjs.com/package/mssql
[Microsoft SQL Server 2014 Express]: https://www.microsoft.com/en-us/server-cloud/Products/sql-server-editions/sql-server-express.aspx
[ExpressJS ミドルウェア]: https://expressjs.com/guide/using-middleware.html
[Winston]: https://github.com/winstonjs/winston
