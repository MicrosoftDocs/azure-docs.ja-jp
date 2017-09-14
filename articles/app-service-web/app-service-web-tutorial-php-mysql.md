---
title: "Azure で PHP と MySQL Web アプリを構築する | Microsoft Docs"
description: "Azure で動作し、MySQL データベースに接続する PHP アプリの入手方法を説明します。"
services: app-service\web
documentationcenter: nodejs
author: cephalin
manager: erikre
editor: 
ms.assetid: 14feb4f3-5095-496e-9a40-690e1414bd73
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: nodejs
ms.topic: tutorial
ms.date: 07/21/2017
ms.author: cephalin
ms.custom: mvc
ms.translationtype: HT
ms.sourcegitcommit: 8b857b4a629618d84f66da28d46f79c2b74171df
ms.openlocfilehash: 64e6e00f33735f50870e4cbda2ca7255686f36b5
ms.contentlocale: ja-jp
ms.lasthandoff: 08/04/2017

---
# <a name="build-a-php-and-mysql-web-app-in-azure"></a>Azure で PHP と MySQL Web アプリを構築する

[Azure Web Apps](https://docs.microsoft.com/azure/app-service-web/app-service-web-overview) では、高度にスケーラブルな自己適用型の Web ホスティング サービスを提供しています。 このチュートリアルでは、Azure で PHP Web アプリを作成し、MySQL データベースに接続する方法について説明します。 このチュートリアルを終了すると、Azure App Service Web Apps で実行される [Laravel](https://laravel.com/) アプリが完成します。

![Azure App Service で実行される PHP アプリ](./media/app-service-web-tutorial-php-mysql/complete-checkbox-published.png)

このチュートリアルで学習する内容は次のとおりです。

> [!div class="checklist"]
> * Azure で MySQL データベースを作成する
> * PHP アプリを MySQL に接続する
> * Azure にアプリケーションをデプロイする
> * データ モデルを更新し、アプリを再デプロイする
> * Azure から診断ログをストリーミングする
> * Azure Portal でアプリを管理する

## <a name="prerequisites"></a>前提条件

このチュートリアルを完了するには、以下が必要です。

* [Git をインストールする](https://git-scm.com/)
* [PHP 5.6.4 以降をインストールする](http://php.net/downloads.php)
* [Composer をインストールする](https://getcomposer.org/doc/00-intro.md)
* Laravel で必要な PHP 拡張機能 (OpenSSL、PDO-MySQL、Mbstring、Tokenizer、XML) を有効にする
* [MySQL をインストールして起動する](https://dev.mysql.com/doc/refman/5.7/en/installing.html) 

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

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

リポジトリのルートに、*.env* という名前のファイルを作成します。 次の変数を *.env* ファイルにコピーします。 _&lt;root_password >_ プレース ホルダーを、MySQL ルート ユーザーのパスワードに置き換えます。

```
APP_ENV=local
APP_DEBUG=true
APP_KEY=SomeRandomString

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

![MySQL に正常に接続されている PHP](./media/app-service-web-tutorial-php-mysql/mysql-connect-success.png)

PHP を停止するには、ターミナルで `Ctrl + C` キーを押します。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-mysql-in-azure"></a>Azure に MySQL を作成する

この手順では、MySQL データベースを[Azure Database for MySQL (Preview)](/azure/mysql) に作成します。 その後、このデータベースに接続するように PHP アプリケーションを構成します。

### <a name="create-a-resource-group"></a>リソース グループの作成

[!INCLUDE [Create resource group](../../includes/app-service-web-create-resource-group-no-h.md)] 

### <a name="create-a-mysql-server"></a>MySQL サーバーを作成する

[az mysql server create](/cli/azure/mysql/server#create) コマンドを使用して、Azure Database for MySQL (Preview) にサーバーを作成します。

次のコマンドで、_&lt;mysql_server_name>_ プレースホルダーを MySQL サーバー名に置き換えます (有効な文字は `a-z`、`0-9`、および `-` です)。 この名前は、MySQL サーバーのホスト名 (`<mysql_server_name>.database.windows.net`) の一部であるため、グローバルに一意である必要があります。

```azurecli-interactive
az mysql server create \
    --name <mysql_server_name> \
    --resource-group myResourceGroup \
    --location "North Europe" \
    --admin-user adminuser \
    --admin-password MySQLAzure2017
```

MySQL サーバーが作成されると、Azure CLI によって、次の例のような情報が表示されます。

```json
{
  "administratorLogin": "adminuser",
  "administratorLoginPassword": null,
  "fullyQualifiedDomainName": "<mysql_server_name>.database.windows.net",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.DBforMySQL/servers/<mysql_server_name>",
  "location": "northeurope",
  "name": "<mysql_server_name>",
  "resourceGroup": "myResourceGroup",
  ...
}
```

### <a name="configure-server-firewall"></a>サーバーのファイアウォールを構成する

[az mysql server firewall-rule create](/cli/azure/mysql/server/firewall-rule#create) コマンドを使用して、MySQL サーバーのクライアントへの接続を許可するファイアウォール ルールを作成します。

```azurecli-interactive
az mysql server firewall-rule create \
    --name allIPs \
    --server <mysql_server_name> \
    --resource-group myResourceGroup \
    --start-ip-address 0.0.0.0 \
    --end-ip-address 255.255.255.255
```

> [!NOTE]
> 現在のところ、Azure Database for MySQL (Preview) では、接続を Azure Services のみに制限することはできません。 Azure の IP アドレスは動的に割り当てられるため、すべての IP アドレスを有効にしておくことをお勧めします。 このサービスはプレビューの段階です。 データベースを保護するためのより優れた方法が提供される予定です。
>
>

### <a name="connect-to-production-mysql-server-locally"></a>ローカルに運用 MySQL サーバーに接続する

ターミナル ウィンドウで、Azure の MySQL サーバーに接続します。 _&lt;mysql_server_name>_ に指定した値を使用します。

```bash
mysql -u adminuser@<mysql_server_name> -h <mysql_server_name>.database.windows.net -P 3306 -p
```

パスワードの入力を求められたら、データベースの作成時に指定した _$tr0ngPa$w0rd!_ を使用します。

### <a name="create-a-production-database"></a>運用データベースを作成する

`mysql` プロンプトで、データベースを作成します。

```sql
CREATE DATABASE sampledb;
```

### <a name="create-a-user-with-permissions"></a>アクセス許可を持つユーザーを作成する

_phpappuser_ というデータベース ユーザーを作成し、このユーザーに `sampledb` データベースのすべての特権を付与します。

```sql
CREATE USER 'phpappuser' IDENTIFIED BY 'MySQLAzure2017'; 
GRANT ALL PRIVILEGES ON sampledb.* TO 'phpappuser';
```

「`quit`」と入力して、サーバー接続を終了します。

```sql
quit
```

## <a name="connect-app-to-azure-mysql"></a>アプリを Azure MySQL に接続する

この手順では、Azure Database for MySQL (Preview) に作成した MySQL データベースに PHP アプリケーションを接続します。

<a name="devconfig"></a>

### <a name="configure-the-database-connection"></a>データベース接続を構成する

リポジトリのルートに _.env.production_ ファイルを作成し、その中に次の変数をコピーします。 プレースホルダー _&lt;mysql_server_name>_ を置き換えます。

```
APP_ENV=production
APP_DEBUG=true
APP_KEY=SomeRandomString

DB_CONNECTION=mysql
DB_HOST=<mysql_server_name>.database.windows.net
DB_DATABASE=sampledb
DB_USERNAME=phpappuser@<mysql_server_name>
DB_PASSWORD=MySQLAzure2017
MYSQL_SSL=true
```

変更を保存します。

> [!TIP]
> MySQL の接続情報を保護するために、このファイルは既に Git リポジトリから除外されています (リポジトリのルートで _.gitignore_ を参照してください)。 後で、App Service の環境変数を構成して Azure Database for MySQL (Preview) のデータベースに接続する方法を学習します。 環境変数を構成するので、App Service には *.env* ファイルは必要ありません。
>

### <a name="configure-ssl-certificate"></a>SSL 証明書を構成する

既定では、Azure Database for MySQL はクライアントからの SSL 接続を強制します。 Azure で MySQL データベースに接続するには、_.pem_ SSL 証明書を使用する必要があります。

_config/database.php_ を開き、次のコードに示すように _sslmode_ パラメーターと _options_ パラメーターを `connections.mysql` に追加します。

```php
'mysql' => [
    ...
    'sslmode' => env('DB_SSLMODE', 'prefer'),
    'options' => (env('MYSQL_SSL')) ? [
        PDO::MYSQL_ATTR_SSL_KEY    => '/ssl/certificate.pem', 
    ] : []
],
```

この _certificate.pem_ の生成方法については、「[Azure Database for MySQL に安全に接続するためにアプリケーションで SSL 接続を構成する](../mysql/howto-configure-ssl.md)」をご覧ください。

> [!TIP]
> パス _/ssl/certificate.pem_ は、Git リポジトリ内の既存の _certificate.pem_ ファイルをポイントします。 このファイルは、このチュートリアルの便宜上提供されています。 ベスト プラクティスとしては、_.pem_ 証明書をソース管理にコミットしないでください。 
>

### <a name="test-the-application-locally"></a>ローカルでアプリケーションをテストする

環境ファイルとして _.env.production_ を使用して Laravel データベースの移行を実行して、Azure Database for MySQL (Preview) の MySQL データベース内にテーブルを作成します。 _.env.production_ には Azure の MySQL データベースへの接続情報が含まれていることに注意してください。

```bash
php artisan migrate --env=production --force
```

この時点では、_.env.production_ には有効なアプリケーション キーはありません。 ターミナルで、新しいものを生成します。

```bash
php artisan key:generate --env=production --force
```

環境ファイルとして _.env.production_ を使用してサンプル アプリケーションを実行します。

```bash
php artisan serve --env=production
```

`http://localhost:8000` に移動します。 エラーなしでページが読み込まれれば、PHP アプリケーションは Azure の MySQL データベースに接続しています。

ページにいくつかのタスクを追加します。

![PHP が Azure Database for MySQL (Preview) に正常にデータベースに接続されている](./media/app-service-web-tutorial-php-mysql/mysql-connect-success.png)

PHP を停止するには、ターミナルで `Ctrl + C` キーを押します。

### <a name="commit-your-changes"></a>変更をコミットする

次の Git コマンドを実行して、変更をコミットします。

```bash
git add .
git commit -m "database.php updates"
```

アプリをデプロイする準備ができました。

## <a name="deploy-to-azure"></a>Azure へのデプロイ

この手順では、MySQL に接続される PHP アプリケーションを Azure App Service にデプロイします。

### <a name="create-an-app-service-plan"></a>App Service プランを作成する

[!INCLUDE [Create app service plan no h](../../includes/app-service-web-create-app-service-plan-no-h.md)]

### <a name="create-a-web-app"></a>Web アプリを作成する

[!INCLUDE [Create web app no h](../../includes/app-service-web-create-web-app-no-h.md)]

### <a name="set-the-php-version"></a>PHP のバージョンを設定する

[az webapp config set](/cli/azure/webapp/config#set) コマンドを使用して、アプリケーションで必要な PHP のバージョンを設定します。

次のコマンドは、PHP のバージョンを _7.0_ に設定します。

```azurecli-interactive
az webapp config set \
    --name <app_name> \
    --resource-group myResourceGroup \
    --php-version 7.0
```

### <a name="configure-database-settings"></a>データベース設定を構成する

既に指摘したように、App Service の環境変数を使用して、Azure MySQL データベースに接続できます。

App Service では、[az webapp config appsettings set](/cli/azure/webapp/config/appsettings#set) コマンドを使用して、環境変数を "_アプリ設定_" として設定します。

次のコマンドでは、アプリ設定 `DB_HOST`、`DB_DATABASE`、`DB_USERNAME`、および `DB_PASSWORD` を構成します。 プレースホルダーの _&lt;appname>_ と _&lt;mysql_server_name>_ を置き換えます。

```azurecli-interactive
az webapp config appsettings set \
    --name <app_name> \
    --resource-group myResourceGroup \
    --settings DB_HOST="<mysql_server_name>.database.windows.net" DB_DATABASE="sampledb" DB_USERNAME="phpappuser@<mysql_server_name>" DB_PASSWORD="MySQLAzure2017" MYSQL_SSL="true"
```

PHP [getenv](http://www.php.net/manual/function.getenv.php) メソッドを使用して、これらの設定にアクセスできます。 Laravel コードでは、PHP `getenv` に対して [env](https://laravel.com/docs/5.4/helpers#method-env) ラッパーが使用されます。 たとえば、_config/database.php_ の MySQL 構成は次のコードのようになります。

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

`php artisan` を使用して新しいアプリケーションキーを生成します (_.env_ には保存されません)。

```bash
php artisan key:generate --show
```

[az webapp config appsettings set](/cli/azure/webapp/config/appsettings#set) コマンドを使用して、App Service Web アプリにアプリケーション キーを設定します。 プレースホルダーの _&lt;appname>_ と _&lt;outputofphpartisankey:generate>_ を置き換えます。

```azurecli-interactive
az webapp config appsettings set \
    --name <app_name> \
    --resource-group myResourceGroup \
    --settings APP_KEY="<output_of_php_artisan_key:generate>" APP_DEBUG="true"
```

`APP_DEBUG="true"` は、デプロイした Web アプリでエラーが発生した場合にデバッグ情報を返すように Laravel に指示します。 運用アプリケーションを実行するときは、`false` に設定してセキュリティを強化します。

### <a name="set-the-virtual-application-path"></a>仮想アプリケーション パスを設定する

Web アプリの仮想アプリケーション パスを設定します。 この手順が必要なのは、[Laravel アプリケーション のライフサイクル](https://laravel.com/docs/5.4/lifecycle)がアプリケーションのルート ディレクトリではなく_パブリック_ ディレクトリから始まるためです。 ライフ サイクルがルート ディレクトリから始まる PHP フレームワークは、仮想アプリケーション パスの手動での構成なしで動作できます。

[az resource update](/cli/azure/resource#update) コマンドを使用して、仮想アプリケーション パスを設定します。 _&lt;appname>_ プレースホルダーを置き換えます。

```azurecli-interactive
az resource update \
    --name web \
    --resource-group myResourceGroup \
    --namespace Microsoft.Web \
    --resource-type config \
    --parent sites/<app_name> \
    --set properties.virtualApplications[0].physicalPath="site\wwwroot\public" \
    --api-version 2015-06-01
```

既定では、Azure App Service は、デプロイされたアプリケーション ファイルのルート ディレクトリ (_sites\wwwroot_) に対して仮想アプリケーションのルート パス (_/_) をポイントします。

### <a name="configure-a-deployment-user"></a>デプロイ ユーザーを構成する

[!INCLUDE [Configure deployment user](../../includes/configure-deployment-user-no-h.md)]

### <a name="configure-local-git-deployment"></a>ローカル Git デプロイの構成

[!INCLUDE [Configure local git](../../includes/app-service-web-configure-local-git-no-h.md)]

### <a name="push-to-azure-from-git"></a>Git から Azure へのプッシュ

ローカル Git リポジトリに Azure リモートを追加します。

```bash
git remote add azure <paste_copied_url_here>
```

Azure リモートにプッシュして、PHP アプリケーションをデプロイします。 デプロイ ユーザーの作成時に指定したパスワードを入力するように求めるメッセージが表示されます。

```bash
git push azure master
```

デプロイ中、Azure App Service は進行状況について Git と通信します。

```bash
Counting objects: 3, done.
Delta compression using up to 8 threads.
Compressing objects: 100% (3/3), done.
Writing objects: 100% (3/3), 291 bytes | 0 bytes/s, done.
Total 3 (delta 2), reused 0 (delta 0)
remote: Updating branch 'master'.
remote: Updating submodules.
remote: Preparing deployment for commit id 'a5e076db9c'.
remote: Running custom deployment command...
remote: Running deployment command...
...
< Output has been truncated for readability >
```

> [!NOTE]
> デプロイ プロセスの最後に [Composer](https://getcomposer.org/) パッケージがインストールされることに気付くかもしれません。 App Service では既定のデプロイ中にこれらの自動化が実行されないため、このサンプル レポジトリには、有効化するための3 つのファイルがルート ディレクトリに追加されます。
>
> - `.deployment` - このファイルは、`bash deploy.sh` をカスタム デプロイ スクリプトとして実行するよう App Service に指示します。
> - `deploy.sh` - カスタム デプロイ スクリプト。 このファイルを確認すると、`npm install` の後で `php composer.phar install` が実行されることがわかります。
> - `composer.phar` - Composer パッケージ マネージャー。
>
> この方法を使用して、App Service に対する Git ベースのデプロイに対して任意の手順を追加できます。 詳細については、「[Custom Deployment Script (カスタム デプロイ スクリプト)](https://github.com/projectkudu/kudu/wiki/Custom-Deployment-Script)」を参照してください。
>

### <a name="browse-to-the-azure-web-app"></a>Azure Web アプリを参照する

`http://<app_name>.azurewebsites.net` を参照し、一覧にいくつかのタスクを追加します。

![Azure App Service で実行される PHP アプリ](./media/app-service-web-tutorial-php-mysql/php-mysql-in-azure.png)

データ主導型の PHP アプリが Azure App Service で実行されています。

## <a name="update-model-locally-and-redeploy"></a>ローカルにモデルを更新し、再デプロイする

この手順では、`task` データ モデルと Web アプリに単純な変更を加え、変更内容を Azure に発行します。

このタスク シナリオでは、タスクを完了としてマークできるようにアプリケーションを変更します。

### <a name="add-a-column"></a>列を追加する

ターミナルで、Git リポジトリのルートに移動します。

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

ターミナルで、Laravel データベースの移行を実行して、ローカル データベースを変更します。

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

Git レポジトリのルート ディレクトリから、開発サーバーを実行します。

```bash
php artisan serve
```

タスクの状態の変更を確認するには、ブラウザーで `http://localhost:8000` に移動し、チェック ボックスをオンにします。

![タスクに追加されたチェック ボックス](./media/app-service-web-tutorial-php-mysql/complete-checkbox.png)

PHP を停止するには、ターミナルで `Ctrl + C` キーを押します。

### <a name="publish-changes-to-azure"></a>Azure に変更を発行する

ターミナルで、運用環境の接続文字列を使用して Laravel データベースの移行を実行して、Azure の運用データベースを変更します。

```bash
php artisan migrate --env=production --force
```

すべての変更を Git にコミットした後、コードの変更を Azure にプッシュします。

```bash
git add .
git commit -m "added complete checkbox"
git push azure master
```

`git push` が完了したら、Azure Web アプリに移動し、新機能を試します。

![Azure に発行されたモデルとデータベースの変更](media/app-service-web-tutorial-php-mysql/complete-checkbox-published.png)

タスクを追加した場合は、そのタスクがデータベースに保持されます。 データ スキーマに対する更新では、既存のデータはそのまま残ります。

## <a name="stream-diagnostic-logs"></a>診断ログをストリーミングする

Azure App Service で PHP アプリケーションを実行している場合、コンソール ログをターミナルにパイプできます。 このようにすると、アプリケーション エラーのデバッグに役立つ同じ診断メッセージを取得できます。

ログのストリーミングを開始するには、[az webapp log tail](/cli/azure/webapp/log#tail) コマンドを使用します。

```azurecli-interactive
az webapp log tail \
    --name <app_name> \
    --resource-group myResourceGroup
```

ログのストリーミングが開始されたら、ブラウザーで Azure Web アプリを最新の情報に更新して、Web トラフィックを取得します。 ターミナルにパイプされたコンソール ログが表示されます。 コンソール ログがすぐに表示されない場合は、30 秒以内にもう一度確認します。

任意のタイミングでログのストリーミングを停止するには、`Ctrl` + `C` キーを押します。

> [!TIP]
> PHP アプリケーションは、標準の [error_log()](http://php.net/manual/function.error-log.php) を使用してコンソールに出力できます。 サンプル アプリケーションでは、_app/Http/routes.php_ でこの方法を使用しています。
>
> Web フレームワークとして、[Laravel では、ログ記録プロバイダーとして Monolog を使用](https://laravel.com/docs/5.4/errors)しています。 Monolog でメッセージをコンソールに出力する方法については、「[PHP: How to use monolog to log to console (php://out) (PHP: monolog を使用してコンソールにログを出力する方法 (php://out)) ](http://stackoverflow.com/questions/25787258/php-how-to-use-monolog-to-log-to-console-php-out)」を参照してください。
>
>

## <a name="manage-the-azure-web-app"></a>Azure Web アプリを管理する

[Azure Portal](https://portal.azure.com) に移動し、作成した Web アプリを管理します。

左側のメニューで **[App Services]** をクリックした後、Azure Web アプリの名前をクリックします。

![Azure Web アプリへのポータル ナビゲーション](./media/app-service-web-tutorial-php-mysql/access-portal.png)

Web アプリの [概要] ページを確認します。 ここでは、停止、開始、再開、参照、削除のような基本的な管理タスクを行うことができます。

左側のメニューは、アプリを構成するためのページを示しています。

![Azure Portal の [App Service] ページ](./media/app-service-web-tutorial-php-mysql/web-app-blade.png)

[!INCLUDE [cli-samples-clean-up](../../includes/cli-samples-clean-up.md)]

<a name="next"></a>

## <a name="next-steps"></a>次のステップ

このチュートリアルで学習した内容は次のとおりです。

> [!div class="checklist"]
> * Azure で MySQL データベースを作成する
> * PHP アプリを MySQL に接続する
> * Azure にアプリケーションをデプロイする
> * データ モデルを更新し、アプリを再デプロイする
> * Azure から診断ログをストリーミングする
> * Azure Portal でアプリを管理する

次のチュートリアルに進み、カスタム DNS 名を Web アプリにマップする方法を学習してください。

> [!div class="nextstepaction"]
> [既存のカスタム DNS 名を Azure Web Apps にマップする](app-service-web-tutorial-custom-domain.md)

