---
title: チュートリアル:PHP アプリと MySQL
description: Azure で動作し、MySQL データベースに接続する PHP アプリの入手方法を説明します。 このチュートリアルでは Laravel を使用します。
ms.assetid: 14feb4f3-5095-496e-9a40-690e1414bd73
ms.devlang: php
ms.topic: tutorial
ms.date: 06/15/2020
ms.custom: mvc, cli-validate, seodec18, devx-track-azurecli
zone_pivot_groups: app-service-platform-windows-linux
ms.openlocfilehash: aaef3f28ea9b7e41bce35661e9515f6efcc9ade5
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102184442"
---
# <a name="tutorial-build-a-php-and-mysql-app-in-azure-app-service"></a>チュートリアル:Azure App Service で PHP および MySQL アプリを構築する

::: zone pivot="platform-windows"  

[Azure App Service](overview.md) は、Windows オペレーティング システムを使用する、高度にスケーラブルな自己適用型の Web ホスティング サービスを提供します。 このチュートリアルでは、Azure で PHP アプリを作成し、MySQL データベースに接続する方法について説明します。 このチュートリアルを終了すると、Azure App Service on Windows で実行される [Laravel](https://laravel.com/) アプリが完成します。

::: zone-end

::: zone pivot="platform-linux"

[Azure App Service](overview.md) は、Linux オペレーティング システムを使用する、高度にスケーラブルな自己適用型の Web ホスティング サービスを提供します。 このチュートリアルでは、Azure で PHP アプリを作成し、MySQL データベースに接続する方法について説明します。 このチュートリアルを終了すると、Azure App Service on Linux で実行される [Laravel](https://laravel.com/) アプリが完成します。

::: zone-end

:::image type="content" source="./media/tutorial-php-mysql-app/complete-checkbox-published.png" alt-text="Task List という PHP サンプル アプリのスクリーンショット。":::

このチュートリアルでは、以下の内容を学習します。

> [!div class="checklist"]
> * Azure で MySQL データベースを作成する
> * PHP アプリを MySQL に接続する
> * Azure にアプリケーションをデプロイする
> * データ モデルを更新し、アプリを再デプロイする
> * Azure から診断ログをストリーミングする
> * Azure Portal でアプリを管理する

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>前提条件

このチュートリアルを完了するには、以下が必要です。

- [Git をインストールする](https://git-scm.com/)
- [PHP 5.6.4 以降をインストールする](https://php.net/downloads.php)
- [Composer をインストールする](https://getcomposer.org/doc/00-intro.md)
- Laravel で必要な PHP 拡張機能 (OpenSSL、PDO-MySQL、Mbstring、Tokenizer、XML) を有効にする
- [MySQL をインストールして起動する](https://dev.mysql.com/doc/refman/5.7/en/installing.html)
[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../includes/azure-cli-prepare-your-environment-no-header.md)] 

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

ターミナル ウィンドウから、`cd` コマンドで作業ディレクトリに移動します。

次のコマンドを実行して、サンプル レポジトリを複製します。

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

![MySQL に正常に接続されている PHP](./media/tutorial-php-mysql-app/mysql-connect-success.png)

PHP を停止するには、ターミナルで `Ctrl + C` キーを押します。

## <a name="create-mysql-in-azure"></a>Azure に MySQL を作成する

この手順では、MySQL データベースを [Azure Database for MySQL](../mysql/index.yml) に作成します。 その後、このデータベースに接続するように PHP アプリケーションを構成します。

### <a name="create-a-resource-group"></a>リソース グループを作成する

[!INCLUDE [Create resource group](../../includes/app-service-web-create-resource-group-no-h.md)] 

### <a name="create-a-mysql-server"></a>MySQL サーバーを作成する

Cloud Shell で [`az mysql server create`](/cli/azure/mysql/server#az-mysql-server-create) コマンドを使用して、Azure Database for MySQL にサーバーを作成します。

次のコマンドの *\<mysql-server-name>* プレースホルダーを一意のサーバー名で置き換え、 *\<admin-user>* プレースホルダーをユーザー名で置き換え、 *\<admin-password>* プレースホルダーをパスワードで置き換えます。 このサーバー名は、MySQL エンドポイント (`https://<mysql-server-name>.mysql.database.azure.com`) の一部として使用されるため、Azure のすべてのサーバーで一意である必要があります。 MySQL DB SKU の選択の詳細については、「[Azure Database for MySQL サーバーの作成](../mysql/quickstart-create-mysql-server-database-using-azure-cli.md#create-an-azure-database-for-mysql-server)」をご覧ください。

```azurecli-interactive
az mysql server create --resource-group myResourceGroup --name <mysql-server-name> --location "West Europe" --admin-user <admin-user> --admin-password <admin-password> --sku-name B_Gen5_1
```

MySQL サーバーが作成されると、Azure CLI によって、次の例のような情報が表示されます。

<pre>
{
  "administratorLogin": "&lt;admin-user&gt;",
  "administratorLoginPassword": null,
  "fullyQualifiedDomainName": "&lt;mysql-server-name&gt;.mysql.database.azure.com",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.DBforMySQL/servers/&lt;mysql-server-name&gt;",
  "location": "westeurope",
  "name": "&lt;mysql-server-name&gt;",
  "resourceGroup": "myResourceGroup",
  ...
  -  &lt; Output has been truncated for readability &gt;
}
</pre>

### <a name="configure-server-firewall"></a>サーバーのファイアウォールを構成する

Cloud Shell で [`az mysql server firewall-rule create`](/cli/azure/mysql/server/firewall-rule#az-mysql-server-firewall-rule-create) コマンドを使用して、MySQL サーバーでクライアント接続を許可するためのファイアウォール規則を作成します。 開始 IP と終了 IP の両方が 0.0.0.0 に設定されている場合、ファイアウォールは他の Azure リソースに対してのみ開かれます。 

```azurecli-interactive
az mysql server firewall-rule create --name allAzureIPs --server <mysql-server-name> --resource-group myResourceGroup --start-ip-address 0.0.0.0 --end-ip-address 0.0.0.0
```

> [!TIP] 
> [アプリで使用する送信 IP アドレスのみを使用する](overview-inbound-outbound-ips.md#find-outbound-ips)ことで、ファイアウォール規則による制限をさらに厳しくすることができます。
>

Cloud Shell 内で *\<your-ip-address>* を [ローカル IPv4 IP アドレス](https://www.whatsmyip.org/)に置き換えてコマンドを再び実行し、ローカル コンピューターからアクセスできるようにします。

```azurecli-interactive
az mysql server firewall-rule create --name AllowLocalClient --server <mysql-server-name> --resource-group myResourceGroup --start-ip-address=<your-ip-address> --end-ip-address=<your-ip-address>
```

### <a name="connect-to-production-mysql-server-locally"></a>ローカルに運用 MySQL サーバーに接続する

ローカルのターミナル ウィンドウで、Azure の MySQL サーバーに接続します。 _&lt;admin-user>_ と _&lt;mysql-server-name>_ に指定した値を使用します。 パスワードの入力を求められたら、Azure でデータベースの作成時に指定したパスワードを使用します。

```bash
mysql -u <admin-user>@<mysql-server-name> -h <mysql-server-name>.mysql.database.azure.com -P 3306 -p
```

### <a name="create-a-production-database"></a>運用データベースを作成する

`mysql` プロンプトで、データベースを作成します。

```sql
CREATE DATABASE sampledb;
```

### <a name="create-a-user-with-permissions"></a>アクセス許可を持つユーザーを作成する

_phpappuser_ というデータベース ユーザーを作成し、このユーザーに `sampledb` データベースのすべての特権を付与します。 パスワードには _MySQLAzure2017_ を使用します。このチュートリアルのために単純なパスワードを選択しています。

```sql
CREATE USER 'phpappuser' IDENTIFIED BY 'MySQLAzure2017'; 
GRANT ALL PRIVILEGES ON sampledb.* TO 'phpappuser';
```

「`quit`」と入力して、サーバー接続を終了します。

```sql
quit
```

## <a name="connect-app-to-azure-mysql"></a>アプリを Azure MySQL に接続する

この手順では、Azure Database for MySQL に作成した MySQL データベースに PHP アプリケーションを接続します。

<a name="devconfig"></a>

### <a name="configure-the-database-connection"></a>データベース接続を構成する

リポジトリのルートに _.env.production_ ファイルを作成し、その中に次の変数をコピーします。 *DB_HOST* と *DB_USERNAME* の両方で、プレースホルダーの _&lt;mysql_server_name>_ を置き換えます。

```
APP_ENV=production
APP_DEBUG=true
APP_KEY=

DB_CONNECTION=mysql
DB_HOST=<mysql-server-name>.mysql.database.azure.com
DB_DATABASE=sampledb
DB_USERNAME=phpappuser@<mysql-server-name>
DB_PASSWORD=MySQLAzure2017
MYSQL_SSL=true
```

変更を保存します。

> [!TIP]
> MySQL の接続情報を保護するために、このファイルは既に Git リポジトリから除外されています (リポジトリのルートで _.gitignore_ を参照してください)。 後で、App Service の環境変数を構成して Azure Database for MySQL のデータベースに接続する方法を学習します。 環境変数を構成するので、App Service には *.env* ファイルは必要ありません。
>

### <a name="configure-tlsssl-certificate"></a>TLS/SSL 証明書を構成する

既定では、Azure Database for MySQL はクライアントからの TLS 接続を強制します。 Azure で MySQL データベースに接続するには、[Azure Database for MySQL から提供された _.pem_ 証明書](../mysql/howto-configure-ssl.md)を使用する必要があります。

_config/database.php_ を開き、次のコードに示すように `sslmode` パラメーターと `options` パラメーターを `connections.mysql` に追加します。

::: zone pivot="platform-windows"  

```php
'mysql' => [
    ...
    'sslmode' => env('DB_SSLMODE', 'prefer'),
    'options' => (env('MYSQL_SSL')) ? [
        PDO::MYSQL_ATTR_SSL_KEY    => '/ssl/BaltimoreCyberTrustRoot.crt.pem', 
    ] : []
],
```

::: zone-end

::: zone pivot="platform-linux"

```php
'mysql' => [
    ...
    'sslmode' => env('DB_SSLMODE', 'prefer'),
    'options' => (env('MYSQL_SSL') && extension_loaded('pdo_mysql')) ? [
        PDO::MYSQL_ATTR_SSL_KEY    => '/ssl/BaltimoreCyberTrustRoot.crt.pem',
    ] : []
],
```

::: zone-end

このチュートリアルでは、便宜上、証明書 `BaltimoreCyberTrustRoot.crt.pem` がリポジトリに用意されています。 

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

![PHP が Azure Database for MySQL に正常にデータベースに接続されている](./media/tutorial-php-mysql-app/mysql-connect-success.png)

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

[!INCLUDE [Configure deployment user](../../includes/configure-deployment-user-no-h.md)]

### <a name="create-an-app-service-plan"></a>App Service プランを作成する

::: zone pivot="platform-windows"  

[!INCLUDE [Create app service plan no h](../../includes/app-service-web-create-app-service-plan-no-h.md)]

::: zone-end

::: zone pivot="platform-linux"

[!INCLUDE [Create app service plan no h](../../includes/app-service-web-create-app-service-plan-linux-no-h.md)]

::: zone-end

<a name="create"></a>
### <a name="create-a-web-app"></a>Web アプリを作成する

::: zone pivot="platform-windows"  

[!INCLUDE [Create web app no h](../../includes/app-service-web-create-web-app-php-no-h.md)] 

::: zone-end

::: zone pivot="platform-linux"

[!INCLUDE [Create web app](../../includes/app-service-web-create-web-app-php-linux-no-h.md)] 

::: zone-end

### <a name="configure-database-settings"></a>データベース設定を構成する

App Service で、[`az webapp config appsettings set`](/cli/azure/webapp/config/appsettings#az-webapp-config-appsettings-set) コマンドを使用して、環境変数を "_アプリ設定_" として設定します。

次のコマンドでは、アプリ設定 `DB_HOST`、`DB_DATABASE`、`DB_USERNAME`、および `DB_PASSWORD` を構成します。 プレースホルダーの _&lt;app-name>_ と _&lt;mysql-server-name>_ を置き換えます。

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group myResourceGroup --settings DB_HOST="<mysql-server-name>.mysql.database.azure.com" DB_DATABASE="sampledb" DB_USERNAME="phpappuser@<mysql-server-name>" DB_PASSWORD="MySQLAzure2017" MYSQL_SSL="true"
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

::: zone pivot="platform-windows"  

アプリの仮想アプリケーション パスを設定します。 この手順が必要なのは、[Laravel アプリケーション のライフサイクル](https://laravel.com/docs/5.4/lifecycle)がアプリケーションのルート ディレクトリではなく _パブリック_ ディレクトリから始まるためです。 ライフ サイクルがルート ディレクトリから始まる PHP フレームワークは、仮想アプリケーション パスの手動での構成なしで動作できます。

Cloud Shell で [`az resource update`](/cli/azure/resource#az-resource-update) コマンドを使用して、仮想アプリケーション パスを設定します。 _&lt;app-name>_ プレースホルダーを置換します。

```azurecli-interactive
az resource update --name web --resource-group myResourceGroup --namespace Microsoft.Web --resource-type config --parent sites/<app_name> --set properties.virtualApplications[0].physicalPath="site\wwwroot\public" --api-version 2015-06-01
```

既定では、Azure App Service は、デプロイされたアプリケーション ファイルのルート ディレクトリ (_sites\wwwroot_) に対して仮想アプリケーションのルート パス ( _/_ ) をポイントします。

::: zone-end

::: zone pivot="platform-linux"

[Laravel アプリケーションのライフサイクル](https://laravel.com/docs/5.4/lifecycle)は、アプリケーションのルート ディレクトリではなく "_パブリック_" ディレクトリから始まります。 App Service の既定の PHP Docker イメージでは Apache が使用されていて、Laravel 用に `DocumentRoot` をカスタマイズすることはできません。 ただし、`.htaccess` を使用して、ルート ディレクトリではなく _/public_ を指すようにすべての要求を書き換えることができます。 リポジトリ ルートには、この目的のために既に `.htaccess` が追加されています。 これにより、Laravel アプリケーションをすぐにデプロイできます。

詳細については、「[Change site root (サイトのルートを変更する)](configure-language-php.md#change-site-root)」を参照してください。

::: zone-end

### <a name="push-to-azure-from-git"></a>Git から Azure へのプッシュ

::: zone pivot="platform-windows"  

[!INCLUDE [app-service-plan-no-h](../../includes/app-service-web-git-push-to-azure-no-h.md)]

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

> [!NOTE]
> デプロイ プロセスの最後に [Composer](https://getcomposer.org/) パッケージがインストールされることに気付くかもしれません。 App Service では既定のデプロイ中にこれらの自動化が実行されないため、このサンプル レポジトリには、有効化するための3 つのファイルがルート ディレクトリに追加されます。
>
> - `.deployment` - このファイルは、`bash deploy.sh` をカスタム デプロイ スクリプトとして実行するよう App Service に指示します。
> - `deploy.sh` - カスタム デプロイ スクリプト。 このファイルを確認すると、`npm install` の後で `php composer.phar install` が実行されることがわかります。
> - `composer.phar` - Composer パッケージ マネージャー。
>
> この方法を使用して、App Service に対する Git ベースのデプロイに対して任意の手順を追加できます。 詳細については、「[Custom Deployment Script (カスタム デプロイ スクリプト)](https://github.com/projectkudu/kudu/wiki/Custom-Deployment-Script)」を参照してください。
>

::: zone-end

::: zone pivot="platform-linux"

[!INCLUDE [app-service-plan-no-h](../../includes/app-service-web-git-push-to-azure-no-h.md)]

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

::: zone-end

### <a name="browse-to-the-azure-app"></a>Azure アプリを参照する

`http://<app-name>.azurewebsites.net` を参照し、一覧にいくつかのタスクを追加します。

:::image type="content" source="./media/tutorial-php-mysql-app/php-mysql-in-azure.png" alt-text="Task List という Azure サンプル アプリに新しいタスクが追加された画面のスクリーンショット。":::

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

![タスクに追加されたチェック ボックス](./media/tutorial-php-mysql-app/complete-checkbox.png)

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

![Azure に発行されたモデルとデータベースの変更](media/tutorial-php-mysql-app/complete-checkbox-published.png)

タスクを追加した場合は、そのタスクがデータベースに保持されます。 データ スキーマに対する更新では、既存のデータはそのまま残ります。

## <a name="stream-diagnostic-logs"></a>診断ログをストリーミングする

::: zone pivot="platform-windows"  

Azure App Service で PHP アプリケーションを実行している場合、コンソール ログをターミナルにパイプできます。 このようにすると、アプリケーション エラーのデバッグに役立つ同じ診断メッセージを取得できます。

ログのストリーミングを開始するには、Cloud Shell で [`az webapp log tail`](/cli/azure/webapp/log#az-webapp-log-tail) コマンドを使用します。

```azurecli-interactive
az webapp log tail --name <app_name> --resource-group myResourceGroup
```

ログのストリーミングが開始されたら、ブラウザーで Azure アプリを最新の情報に更新して、Web トラフィックを取得します。 ターミナルにパイプされたコンソール ログが表示されます。 コンソール ログがすぐに表示されない場合は、30 秒以内にもう一度確認します。

任意のタイミングでログのストリーミングを停止するには、`Ctrl`+`C` と入力します。

::: zone-end

::: zone pivot="platform-linux"

[!INCLUDE [Access diagnostic logs](../../includes/app-service-web-logs-access-no-h.md)]

::: zone-end

> [!TIP]
> PHP アプリケーションは、標準の [error_log()](https://php.net/manual/function.error-log.php) を使用してコンソールに出力できます。 サンプル アプリケーションでは、_app/Http/routes.php_ でこの方法を使用しています。
>
> Web フレームワークとして、[Laravel では、ログ記録プロバイダーとして Monolog を使用](https://laravel.com/docs/5.4/errors)しています。 Monolog でコンソールにメッセージを出力する方法については、「[PHP:How to use monolog to log to console (php://out)](https://stackoverflow.com/questions/25787258/php-how-to-use-monolog-to-log-to-console-php-out)」 (PHP: monolog を使用してコンソールにログを記録する方法 (php://out)) を参照してください。
>
>

## <a name="manage-the-azure-app"></a>Azure アプリの管理

[Azure portal](https://portal.azure.com) に移動し、お客様が作成したアプリを管理します。

左側のメニューで **[App Services]** をクリックしてから、お客様の Azure アプリの名前をクリックします。

![Azure アプリへのポータル ナビゲーション](./media/tutorial-php-mysql-app/access-portal.png)

お客様のアプリの [概要] ページを確認します。 ここでは、停止、開始、再開、参照、削除のような基本的な管理タスクを行うことができます。

左側のメニューは、アプリを構成するためのページを示しています。

![Azure Portal の [App Service] ページ](./media/tutorial-php-mysql-app/web-app-blade.png)

[!INCLUDE [cli-samples-clean-up](../../includes/cli-samples-clean-up.md)]

<a name="next"></a>

## <a name="next-steps"></a>次のステップ

このチュートリアルでは、以下の内容を学習しました。

> [!div class="checklist"]
> * Azure で MySQL データベースを作成する
> * PHP アプリを MySQL に接続する
> * Azure にアプリケーションをデプロイする
> * データ モデルを更新し、アプリを再デプロイする
> * Azure から診断ログをストリーミングする
> * Azure Portal でアプリを管理する

次のチュートリアルに進み、カスタム DNS 名をアプリにマップする方法を学習してください。

> [!div class="nextstepaction"]
> [チュートリアル:カスタム DNS 名をアプリにマップする](app-service-web-tutorial-custom-domain.md)

または、他のリソースを参照してください。

> [!div class="nextstepaction"]
> [PHP アプリの構成](configure-language-php.md)
