---
title: チュートリアル:Azure Database for MySQL フレキシブル サーバーを使用して PHP (Laravel) アプリを構築する
description: このチュートリアルでは、フレキシブル サーバーを使用して PHP アプリを構築する方法について説明します。
author: mksuni
ms.author: sumuth
ms.service: mysql
ms.topic: tutorial
ms.devlang: php
ms.date: 9/21/2020
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: 93e605cb20d593750100ec8e340a7ad74c4dd385
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "97587895"
---
# <a name="tutorial-build-a-php-laravel-and-mysql-flexible-server-preview-app-in-azure-app-service"></a>チュートリアル:Azure App Service で PHP (Laravel) と MySQL フレキシブル サーバー (プレビュー) のアプリを構築する


:::image type="content" source="media/tutorial-php-database-app/complete-checkbox-published.png" alt-text="フレキシブル サーバーを使用した Azure での PHP Web アプリ":::

[Azure App Service](../../app-service/overview.md) は、Linux オペレーティング システムを使用する、高度にスケーラブルな自己適用型の Web ホスティング サービスを提供します。 このチュートリアルでは、Azure で PHP アプリを作成し、MySQL データベースに接続する方法について説明します。 このチュートリアルを終了すると、Azure App Service on Linux で実行される [Laravel](https://laravel.com/) アプリが完成します。

このチュートリアルでは、以下の内容を学習します。
> [!div class="checklist"]
> * ローカル MySQL を使用して PHP (Laravel) アプリをセットアップする
> * MySQL フレキシブル サーバー (プレビュー) を作成する
> * PHP アプリを MySQL フレキシブル サーバー (プレビュー) に接続する
> * Azure App Service にアプリを配置する
> * データ モデルを更新し、アプリを再デプロイする
> * Azure Portal でアプリを管理する

[Azure サブスクリプション](../../guides/developer/azure-developer-guide.md#understanding-accounts-subscriptions-and-billing)をお持ちでない場合は、開始する前に[無料アカウント](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)を作成してください。

## <a name="prerequisites"></a>前提条件

このチュートリアルを完了するには、以下が必要です。

1. [Git をインストールする](https://git-scm.com/)
2. [PHP 5.6.4 以降をインストールする](https://php.net/downloads.php)
3. [Composer をインストールする](https://getcomposer.org/doc/00-intro.md)
4. Laravel で必要な PHP 拡張機能 (OpenSSL、PDO-MySQL、Mbstring、Tokenizer、XML) を有効にする
5. [MySQL をインストールして起動する](https://dev.mysql.com/doc/refman/5.7/en/installing.html)

## <a name="prepare-local-mysql"></a>ローカル MySQL を準備する

この手順では、このチュートリアルで使用するデータベースをローカル MySQL サーバーに作成します。

### <a name="connect-to-local-mysql-server"></a>ローカル MySQL サーバーに接続する

ターミナル ウィンドウで、ローカル MySQL サーバーに接続します。 このチュートリアルでは、ターミナル ウィンドウを使ってすべてのコマンドを実行します。

```bash
mysql -u root -p
```

パスワードの入力を求められたら、`root` アカウントのパスワードを入力します。 ルート アカウントのパスワードを思い出せない場合は、「[MySQL: root のパスワードをリセットする方法](https://dev.mysql.com/doc/refman/5.7/en/resetting-permissions.html)」を参照してください。

コマンドが正常に実行されれば、MySQL サーバーは実行されています。 正常に実行されない場合は、[MySQL のインストール後の手順](https://dev.mysql.com/doc/refman/5.7/en/postinstallation.html)に従って、MySQL サーバーが起動されたことを確認してください。

### <a name="create-a-database-locally"></a>ローカルにデータベースを作成する

`mysql` プロンプトで、データベースを作成します。

```sql
CREATE DATABASE sampledb;
```

「`quit`」と入力して、サーバー接続を終了します。

```sql
quit
```

<a name="step2"></a>

## <a name="create-a-php-app-locally"></a>ローカルに PHP アプリを作成する
この手順では、Laravel サンプル アプリケーションを取得し、データベース接続を構成してローカルで実行します。

### <a name="clone-the-sample"></a>サンプルを複製する

ターミナル ウィンドウで、サンプル アプリケーションを複製できる空のディレクトリに移動します。  次のコマンドを実行して、サンプル レポジトリを複製します。

```bash
git clone https://github.com/Azure-Samples/laravel-tasks
```

`cd` コマンドで複製したディレクトリに移動します。
必要なパッケージをインストールします。

```bash
cd laravel-tasks
composer install
```

### <a name="configure-mysql-connection"></a>MySQL 接続を構成する

リポジトリのルートに、 *.env* という名前のファイルを作成します。 次の変数を *.env* ファイルにコピーします。 _&lt;root_password >_ プレース ホルダーを、MySQL ルート ユーザーのパスワードに置き換えます。

```txt
APP_ENV=local
APP_DEBUG=true
APP_KEY=

DB_CONNECTION=mysql
DB_HOST=127.0.0.1
DB_DATABASE=sampledb
DB_USERNAME=root
DB_PASSWORD=<root_password>
```

この _.env_ ファイルを Laravel でどのように使用するかの詳細については、[Laravel 環境の構成](https://laravel.com/docs/5.4/configuration#environment-configuration)に関するページを参照してください。

### <a name="run-the-sample-locally"></a>ローカルでサンプルを実行する

[Laravel データベースの移行](https://laravel.com/docs/5.4/migrations)を実行して、アプリケーションで必要なテーブルを作成します。 移行で作成されるテーブルを確認するには、Git レポジトリの _database/migrations_ ディレクトリを調べます。

```bash
php artisan migrate
```

新しい Laravel アプリケーション キーを生成します。

```bash
php artisan key:generate
```

アプリケーションを実行します。

```bash
php artisan serve
```

ブラウザーで `http://localhost:8000` にアクセスします。 ページで、いくつかのタスクを追加します。

:::image type="content" source="media/tutorial-php-database-app/mysql-connect-success.png" alt-text="MySQL に正常に接続されている PHP":::

PHP を停止するには、ターミナルで `Ctrl + C` キーを押します。

## <a name="create-a-mysql-flexible-server-preview"></a>MySQL フレキシブル サーバー (プレビュー) を作成する
このステップでは、[Azure Database for MySQL フレキシブル サーバー](../index.yml) (パブリック プレビュー) に MySQL データベースを作成します。 その後、このデータベースに接続するように PHP アプリケーションを構成します。 [Azure Cloud Shell](../../cloud-shell/overview.md) で、[`az flexible-server create`](/cli/azure/mysql/server#az-mysql-flexible-server-create) コマンドを使用してサーバーを作成します。

```azurecli-interactive
az mysql flexible-server create  --resource-group myResourceGroup --public-access <IP-Address>
```

> [!IMPORTANT]
> - 次のステップで接続して Laravel データの移行を実行するときに使用するので、**サーバー名** と **接続文字列** を記録しておきます。
> - **IP-Address** 引数では、クライアント コンピューターの IP アドレスを指定します。 サーバーは作成時にロックされているので、ローカル環境でサーバーを管理するには、クライアント コンピューターへのアクセスを許可する必要があります。

### <a name="configure-server-firewall-to-allow-web-app-to-connect-to-the-server"></a>Web アプリがサーバーに接続できるようにサーバー ファイアウォールを構成する

Cloud Shell で az mysql server firewall-rule create コマンドを使用し、MySQL サーバーのクライアントへの接続を許可するファイアウォール ルールを作成します。 開始 IP と終了 IP の両方を ```0.0.0.0``` に設定すると、サーバーに接続するための静的 IP を持たない他の Azure サービスに対してのみ、ファイアウォールが開かれます。

```azurecli
az mysql flexible-server firewall-rule create --name allanyAzureIPs --server <mysql-server-name> --resource-group myResourceGroup --start-ip-address 0.0.0.0 --end-ip-address 0.0.0.0
```

### <a name="connect-to-production-mysql-server-locally"></a>ローカルに運用 MySQL サーバーに接続する

ローカルのターミナル ウィンドウで、Azure の MySQL サーバーに接続します。 前に ```<admin-user>``` と ```<mysql-server-name>``` に指定した値を使用します。 パスワードの入力を求められたら、Azure でデータベースの作成時に指定したパスワードを使用します。

```bash
mysql -u <admin-user> -h <mysql-server-name>.mysql.database.azure.com -P 3306 -p
```

### <a name="create-a-production-database"></a>運用データベースを作成する

`mysql` プロンプトで、データベースを作成します。

```sql
CREATE DATABASE sampledb;
```

### <a name="create-a-user-with-permissions"></a>アクセス許可を持つユーザーを作成する

_phpappuser_ というデータベース ユーザーを作成し、このユーザーに `sampledb` データベースのすべての特権を付与します。 チュートリアルを簡単にするため、パスワードには _MySQLAzure2020_ を使用します。

```sql
CREATE USER 'phpappuser' IDENTIFIED BY 'MySQLAzure2020';
GRANT ALL PRIVILEGES ON sampledb.* TO 'phpappuser';
```

「`quit`」と入力して、サーバー接続を終了します。

```sql
quit
```

## <a name="connect-app-to-mysql-flexible-server"></a>アプリを MySQL フレキシブル サーバーに接続する

この手順では、Azure Database for MySQL に作成した MySQL データベースに PHP アプリケーションを接続します。

<a name="devconfig"></a>

### <a name="configure-the-database-connection"></a>データベース接続を構成する

リポジトリのルートに _.env.production_ ファイルを作成し、その中に次の変数をコピーします。 *DB_HOST* と *DB_USERNAME* の両方で、プレースホルダー _&lt;mysql-server-name>_ を置き換えます。

```
APP_ENV=production
APP_DEBUG=true
APP_KEY=

DB_CONNECTION=mysql
DB_HOST=<mysql-server-name>.mysql.database.azure.com
DB_DATABASE=sampledb
DB_USERNAME=phpappuser
DB_PASSWORD=MySQLAzure2017
MYSQL_SSL=true
```

変更を保存します。

> [!TIP]
> MySQL の接続情報を保護するために、このファイルは既に Git リポジトリから除外されています (リポジトリのルートで _.gitignore_ を参照してください)。 後で、App Service の環境変数を構成して Azure Database for MySQL のデータベースに接続する方法を学習します。 環境変数を構成するので、App Service には *.env* ファイルは必要ありません。
>

### <a name="configure-tlsssl-certificate"></a>TLS/SSL 証明書を構成する

既定では、MySQL フレキシブル サーバーによって、クライアントからの TLS 接続が強制されます。 Azure で MySQL データベースに接続するには、[Azure Database for MySQL フレキシブル サーバーから提供された _.pem_ 証明書](https://dl.cacerts.digicert.com/DigiCertGlobalRootCA.crt.pem)を使用する必要があります。 [この証明書](https://dl.cacerts.digicert.com/DigiCertGlobalRootCA.crt.pem)をダウンロードし、サンプル アプリ リポジトリのローカル コピーの **SSL** フォルダーに置きます。

_config/database.php_ を開き、次のコードに示すように `sslmode` パラメーターと `options` パラメーターを `connections.mysql` に追加します。

```php
'mysql' => [
    ...
    'sslmode' => env('DB_SSLMODE', 'prefer'),
    'options' => (env('MYSQL_SSL') && extension_loaded('pdo_mysql')) ? [
        PDO::MYSQL_ATTR_SSL_KEY    => '/ssl/DigiCertGlobalRootCA.crt.pem',
    ] : []
],
```

### <a name="test-the-application-locally"></a>ローカルでアプリケーションをテストする

環境ファイルとして _.env.production_ を使用して Laravel データベースの移行を実行して、Azure Database for MySQL の MySQL データベース内にテーブルを作成します。 _.env.production_ には Azure の MySQL データベースへの接続情報が含まれていることに注意してください。

```bash
php artisan migrate --env=production --force
```

この時点では、 _.env.production_ には有効なアプリケーション キーはありません。 ターミナルで、新しいものを生成します。

```bash
php artisan key:generate --env=production --force
```

環境ファイルとして _.env.production_ を使用してサンプル アプリケーションを実行します。

```bash
php artisan serve --env=production
```

`http://localhost:8000` に移動します。 エラーなしでページが読み込まれれば、PHP アプリケーションは Azure の MySQL データベースに接続しています。

ページで、いくつかのタスクを追加します。

:::image type="content" source="media/tutorial-php-database-app/mysql-connect-success.png" alt-text="PHP が Azure Database for MySQL に正常にデータベースに接続されている":::

PHP を停止するには、ターミナルで `Ctrl + C` キーを押します。

### <a name="commit-your-changes"></a>変更をコミットする

次の Git コマンドを実行して、変更をコミットします。

```bash
git add .
git commit -m "database.php updates"
```

アプリをデプロイする準備ができました。

## <a name="deploy-to-azure"></a>Deploy to Azure (Azure へのデプロイ)

この手順では、MySQL に接続される PHP アプリケーションを Azure App Service にデプロイします。

### <a name="configure-a-deployment-user"></a>デプロイ ユーザーを構成する

FTP およびローカルの Git では、"デプロイ ユーザー" を使用して Azure Web アプリにデプロイできます。 デプロイ ユーザーを構成すると、すべての Azure デプロイでこのユーザーを使用できます。 アカウントレベルのデプロイのユーザー名とパスワードは、Azure サブスクリプションの資格情報とは異なります。

デプロイ ユーザーを構成するには、Azure Cloud Shell で [az webapp deployment user set](/cli/azure/webapp/deployment/user#az-webapp-deployment-user-set) コマンドを実行します。 _&lt;username>_ と _&lt;password>_ を、デプロイ ユーザーのユーザー名とパスワードに置き換えます。

ユーザー名は、Azure 内で一意である必要があり、ローカル Git プッシュの場合は "\@" シンボルを含めることはできません。
パスワードは長さが 8 文字以上で、文字、数字、記号のうち 2 つを含む必要があります。

```bash
az appservice plan create --name myAppServicePlan --resource-group myResourceGroup --sku F1 --is-linux
```

JSON の出力では、パスワードは null として表示されます。 "競合" が発生した場合。 詳細:409 エラーが発生した場合は、ユーザー名を変更します。 "正しくない要求" が発生した場合。 詳細:400 エラーが発生した場合は、より強力なパスワードを使用します。 **Web アプリのデプロイに使用するユーザー名とパスワードを記録します。**

### <a name="create-an-app-service-plan"></a>App Service プランを作成する

Cloud Shell で [az appservice plan create](/cli/azure/appservice/plan#az-appservice-plan-create) コマンドを使用して、リソース グループに App Service プランを作成します。 次の例では、myAppServicePlan という名前の App Service プランを、Free 価格レベル (--sku F1) で Linux コンテナー (--is-linux) に作成します。

az appservice plan create --name myAppServicePlan --resource-group myResourceGroup --sku F1 --is-linux

<a name="create"></a>

### <a name="create-a-web-app"></a>Web アプリを作成する

myAppServicePlan App Service プランに [Web アプリ](../../app-service/overview.md#app-service-on-linux)を作成します。

Cloud Shell では、[az webapp create](/cli/azure/webapp#az-webapp-create) コマンドを使用できます。 次の例では、 _&lt;app-name>_ を、グローバルに一意のアプリ名に置き換えます (有効な文字は `a-z`、`0-9`、`-`)。 ランタイムは `PHP|7.0` に設定されています。 サポートされているすべてのランタイムを確認するには、[az webapp list-runtimes --linux](/cli/azure/webapp#az-webapp-list-runtimes) を実行します。

```bash
az webapp create --resource-group myResourceGroup --plan myAppServicePlan --name <app-name> --runtime "PHP|7.3" --deployment-local-git
```

Web アプリが作成されると、Azure CLI によって次の例のような出力が表示されます。

```
Local git is configured with url of 'https://<username>@<app-name>.scm.azurewebsites.net/<app-name>.git'
{
  "availabilityState": "Normal",
  "clientAffinityEnabled": true,
  "clientCertEnabled": false,
  "cloningInfo": null,
  "containerSize": 0,
  "dailyMemoryTimeQuota": 0,
  "defaultHostName": "<app-name>.azurewebsites.net",
  "deploymentLocalGitUrl": "https://<username>@<app-name>.scm.azurewebsites.net/<app-name>.git",
  "enabled": true,
  < JSON data removed for brevity. >
}
```

Git デプロイが有効な、空の新しい Web アプリが作成されました。

> [!NOTE]
> Git リモートの URL は、deploymentLocalGitUrl プロパティに、 https://<username>@<app-name>.scm.azurewebsites.net/<app-name>.git という形式で表示されます。 この URL は後で必要になるので保存しておいてください。

### <a name="configure-database-settings"></a>データベース設定を構成する

App Service で、[`az webapp config appsettings set`](/cli/azure/webapp/config/appsettings#az-webapp-config-appsettings-set) コマンドを使用して、環境変数を "_アプリ設定_" として設定します。

次のコマンドでは、アプリ設定 `DB_HOST`、`DB_DATABASE`、`DB_USERNAME`、および `DB_PASSWORD` を構成します。 プレースホルダーの _&lt;app-name>_ と _&lt;mysql-server-name>_ を置き換えます。

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group myResourceGroup --settings DB_HOST="<mysql-server-name>.mysql.database.azure.com" DB_DATABASE="sampledb" DB_USERNAME="phpappuser" DB_PASSWORD="MySQLAzure2017" MYSQL_SSL="true"
```

PHP [getenv](https://www.php.net/manual/en/function.getenv.php) メソッドを使用して、これらの設定にアクセスできます。 Laravel コードでは、PHP `getenv` に対して [env](https://laravel.com/docs/5.4/helpers#method-env) ラッパーが使用されます。 たとえば、_config/database.php_ の MySQL 構成は次のコードのようになります。

```php
'mysql' => [
    'driver'    => 'mysql',
    'host'      => env('DB_HOST', 'localhost'),
    'database'  => env('DB_DATABASE', 'forge'),
    'username'  => env('DB_USERNAME', 'forge'),
    'password'  => env('DB_PASSWORD', ''),
    ...
],
```

### <a name="configure-laravel-environment-variables"></a>Laravel の環境変数を構成する

Laravel には App Service のアプリケーション キーが必要です。 これはアプリ設定で構成できます。

ローカル ターミナル ウィンドウで、`php artisan` を使用して新しいアプリケーションキーを生成します ( _.env_ には保存されません)。

```bash
php artisan key:generate --show
```

Cloud Shell で [`az webapp config appsettings set`](/cli/azure/webapp/config/appsettings#az-webapp-config-appsettings-set) コマンドを使用して、App Service アプリにアプリケーション キーを設定します。 プレースホルダーの _&lt;app-name>_ と _&lt;outputofphpartisankey:generate>_ を置き換えます。

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group myResourceGroup --settings APP_KEY="<output_of_php_artisan_key:generate>" APP_DEBUG="true"
```

`APP_DEBUG="true"` は、デプロイしたアプリでエラーが発生した場合にデバッグ情報を返すように Laravel に指示します。 運用アプリケーションを実行するときは、`false` に設定してセキュリティを強化します。

### <a name="set-the-virtual-application-path"></a>仮想アプリケーション パスを設定する

[Laravel アプリケーションのライフサイクル](https://laravel.com/docs/5.4/lifecycle)は、アプリケーションのルート ディレクトリではなく "_パブリック_" ディレクトリから始まります。 App Service の既定の PHP Docker イメージでは Apache が使用されていて、Laravel 用に `DocumentRoot` をカスタマイズすることはできません。 ただし、`.htaccess` を使用して、ルート ディレクトリではなく _/public_ を指すようにすべての要求を書き換えることができます。 リポジトリ ルートには、この目的のために既に `.htaccess` が追加されています。 これにより、Laravel アプリケーションをすぐにデプロイできます。

詳細については、「[Change site root (サイトのルートを変更する)](../../app-service/configure-language-php.md?pivots=platform-linux#change-site-root)」を参照してください。

### <a name="push-to-azure-from-git"></a>Git から Azure へのプッシュ

ローカル ターミナル ウィンドウで、ローカル Git リポジトリに Azure リモートを追加します。 _&lt;deploymentLocalGitUrl-from-create-step>_ を、[Web アプリの作成](#create-a-web-app)に関するセクションで保存した Git リモートの URL に置き換えます。

```bash
git remote add azure <deploymentLocalGitUrl-from-create-step>
```

アプリをデプロイするために、次のコマンドで Azure リモートにプッシュします。 Git Credential Manager によって資格情報の入力を求めるメッセージが表示されたら、Azure portal へのサインインに使用する資格情報ではなく、「**デプロイ ユーザーを構成する**」で作成した資格情報を入力してください。

```bash
git push azure main
```

このコマンドの実行には、数分かかる場合があります。 実行中、次の例のような情報が表示されます。

<pre>
Counting objects: 3, done.
Delta compression using up to 8 threads.
Compressing objects: 100% (3/3), done.
Writing objects: 100% (3/3), 291 bytes | 0 bytes/s, done.
Total 3 (delta 2), reused 0 (delta 0)
remote: Updating branch 'main'.
remote: Updating submodules.
remote: Preparing deployment for commit id 'a5e076db9c'.
remote: Running custom deployment command...
remote: Running deployment command...
...
&lt; Output has been truncated for readability &gt;
</pre>

### <a name="browse-to-the-azure-app"></a>Azure アプリを参照する

`http://<app-name>.azurewebsites.net` を参照し、一覧にいくつかのタスクを追加します。

:::image type="content" source="media/tutorial-php-database-app/php-mysql-in-azure.png" alt-text="Azure での PHP Web アプリ":::

データ主導型の PHP アプリが Azure App Service で実行されています。

## <a name="update-model-locally-and-redeploy"></a>ローカルにモデルを更新し、再デプロイする

この手順では、`task` データ モデルと Web アプリに単純な変更を加え、変更内容を Azure に発行します。

このタスク シナリオでは、タスクを完了としてマークできるようにアプリケーションを変更します。

### <a name="add-a-column"></a>列を追加する

ローカル ターミナル ウィンドウで、Git リポジトリのルートに移動します。

`tasks` テーブル用の新しいデータベースの移行を生成します。

```bash
php artisan make:migration add_complete_column --table=tasks
```

このコマンドは、生成される移行ファイルの名前を表示します。 このファイルを _database/migrations_ で探して開きます。

`up` メソッドを次のコードに置き換えます。

```php
public function up()
{
    Schema::table('tasks', function (Blueprint $table) {
        $table->boolean('complete')->default(False);
    });
}
```

上のコードは、`tasks` テーブルに `complete` と呼ばれるブール値の列を追加します。

`down` メソッドを、ロールバック アクション用の次のコードに置き換えます。

```php
public function down()
{
    Schema::table('tasks', function (Blueprint $table) {
        $table->dropColumn('complete');
    });
}
```

ローカル ターミナル ウィンドウで、Laravel データベースの移行を実行して、ローカル データベースを変更します。

```bash
php artisan migrate
```

[Laravel の名前付け規則](https://laravel.com/docs/5.4/eloquent#defining-models)に基づいて、モデル `Task` (_app/Task.php_ 参照) を `tasks` テーブルに既定でマップします。

### <a name="update-application-logic"></a>アプリケーション ロジックを更新する

*routes/web.php* ファイルを開きます。 アプリケーションは、そのルートとビジネス ロジックをここに定義します。

ファイルの末尾に、次のコードを使用してルートを追加します。

```php
/**
 * Toggle Task completeness
 */
Route::post('/task/{id}', function ($id) {
    error_log('INFO: post /task/'.$id);
    $task = Task::findOrFail($id);

    $task->complete = !$task->complete;
    $task->save();

    return redirect('/');
});
```

上のコードは、データ モデルに対して `complete` 値の切り替えによる単純な更新を行います。

### <a name="update-the-view"></a>ビューを更新する

*resources/views/tasks.blade.php* ファイルを開きます。 `<tr>` 開始タグを探し、次のコードに置き換えます。

```html
<tr class="{{ $task->complete ? 'success' : 'active' }}" >
```

上のコードは、タスクが完了しているかどうかに応じて行の色を変更します。

次の行には、次のコードがあります。

```html
<td class="table-text"><div>{{ $task->name }}</div></td>
```

この行全体を次のコードに置き換えます。

```html
<td>
    <form action="{{ url('task/'.$task->id) }}" method="POST">
        {{ csrf_field() }}

        <button type="submit" class="btn btn-xs">
            <i class="fa {{$task->complete ? 'fa-check-square-o' : 'fa-square-o'}}"></i>
        </button>
        {{ $task->name }}
    </form>
</td>
```

上のコードは、前に定義したルートを参照する送信ボタンを追加します。

### <a name="test-the-changes-locally"></a>変更をローカルでテストする

ローカル ターミナル ウィンドウで、Git リポジトリのルート ディレクトリから開発サーバーを実行します。

```bash
php artisan serve
```

タスクの状態の変更を確認するには、ブラウザーで `http://localhost:8000` に移動し、チェック ボックスをオンにします。

:::image type="content" source="media/tutorial-php-database-app/complete-checkbox.png" alt-text="タスクに追加されたチェック ボックス":::

PHP を停止するには、ターミナルで `Ctrl + C` キーを押します。

### <a name="publish-changes-to-azure"></a>Azure に変更を発行する

ローカル ターミナル ウィンドウで、運用環境の接続文字列を使用して Laravel データベースの移行を実行して、Azure の運用データベースを変更します。

```bash
php artisan migrate --env=production --force
```

すべての変更を Git にコミットした後、コードの変更を Azure にプッシュします。

```bash
git add .
git commit -m "added complete checkbox"
git push azure main
```

`git push` が完了したら、Azure アプリに移動し、新機能を試します。

:::image type="content" source="media/tutorial-php-database-app/complete-checkbox-published.png" alt-text="Azure に発行されたモデルとデータベースの変更":::

タスクを追加した場合は、そのタスクがデータベースに保持されます。 データ スキーマに対する更新では、既存のデータはそのまま残ります。

## <a name="clean-up-resources"></a>リソースをクリーンアップする
前の手順では、リソース グループ内に Azure リソースを作成しました。 これらのリソースが将来必要になると想定していない場合、Cloud Shell で次のコマンドを実行して、リソース グループを削除します。

```bash
az group delete --name myResourceGroup
```

<a name="next"></a>

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [Azure portal でリソースを管理する方法](../../azure-resource-manager/management/manage-resources-portal.md) <br/>
> [!div class="nextstepaction"]
> [サーバーを管理する方法](how-to-manage-server-cli.md)
