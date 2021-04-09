---
title: PHP アプリの構成
description: Azure App Service のネイティブ Windows インスタンス、または事前に作成した PHP コンテナーで PHP アプリを構成する方法について学習します。 この記事では、最も一般的な構成タスクを紹介しています。
ms.devlang: php
ms.topic: article
ms.date: 06/02/2020
zone_pivot_groups: app-service-platform-windows-linux
ms.openlocfilehash: afac8273b5729bcf5470be471145214426dc7dab
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "90055301"
---
# <a name="configure-a-php-app-for-azure-app-service"></a>Azure App Service 向けの PHP アプリを構成する

このガイドでは、Azure App Service の PHP Web アプリ、モバイル バックエンド、API アプリを構成する方法を示します。

このガイドでは、App Service にアプリをデプロイする PHP 開発者向けに主要な概念と手順を示します。 Azure App Service を使用したことがない場合は、最初に [PHP クイック スタート](quickstart-php.md)と [PHP と MySQL のチュートリアル](tutorial-php-mysql-app.md)に従ってください。

## <a name="show-php-version"></a>PHP バージョンを表示する

::: zone pivot="platform-windows"  

現在の PHP バージョンを表示するには、[Cloud Shell](https://shell.azure.com) で次のコマンドを実行します。

```azurecli-interactive
az webapp config show --resource-group <resource-group-name> --name <app-name> --query phpVersion
```

サポートされているすべての PHP バージョンを表示するには、[Cloud Shell](https://shell.azure.com) で次のコマンドを実行します。

```azurecli-interactive
az webapp list-runtimes | grep php
```

::: zone-end

::: zone pivot="platform-linux"

現在の PHP バージョンを表示するには、[Cloud Shell](https://shell.azure.com) で次のコマンドを実行します。

```azurecli-interactive
az webapp config show --resource-group <resource-group-name> --name <app-name> --query linuxFxVersion
```

サポートされているすべての PHP バージョンを表示するには、[Cloud Shell](https://shell.azure.com) で次のコマンドを実行します。

```azurecli-interactive
az webapp list-runtimes --linux | grep PHP
```

::: zone-end

## <a name="set-php-version"></a>PHP バージョンを設定する

::: zone pivot="platform-windows"  

PHP バージョンを 7.4 に設定するには、[Cloud Shell](https://shell.azure.com) で次のコマンドを実行します。

```azurecli-interactive
az webapp config set --name <app-name> --resource-group <resource-group-name> --php-version 7.4
```

::: zone-end

::: zone pivot="platform-linux"

PHP バージョンを 7.2 に設定するには、[Cloud Shell](https://shell.azure.com) で次のコマンドを実行します。

```azurecli-interactive
az webapp config set --name <app-name> --resource-group <resource-group-name> --linux-fx-version "PHP|7.2"
```

::: zone-end

::: zone pivot="platform-windows"  

## <a name="run-composer"></a>Composer を実行する

デプロイ時に App Service で [Composer](https://getcomposer.org/) を実行する場合、最も簡単な方法は、Composer をリポジトリに含めることです。

ローカルのターミナル ウィンドウから、リポジトリのルートにディレクトリを変更し、[Download Composer](https://getcomposer.org/download/) (Composer のダウンロード) の指示に従い、ディレクトリ ルートに *composer.phar* をダウンロードします。

次のコマンドを実行します ([npm](https://www.npmjs.com/get-npm) をインストールしておく必要があります)。

```bash
npm install kuduscript -g
kuduscript --node --scriptType bash --suppressPrompt
```

リポジトリのルートには現在、 *.deployment* と *deploy.sh* の 2 つの追加ファイルがあります。

*deploy.sh* を開き、次のような `Deployment` セクションを見つけます。

```bash
##################################################################################################################################
# Deployment
# ----------
```

`Deployment` セクションの *末尾* に必要なツールの実行に必要なコード セクションを追加します。

```bash
# 4. Use composer
echo "$DEPLOYMENT_TARGET"
if [ -e "$DEPLOYMENT_TARGET/composer.json" ]; then
  echo "Found composer.json"
  pushd "$DEPLOYMENT_TARGET"
  php composer.phar install $COMPOSER_ARGS
  exitWithMessageOnError "Composer install failed"
  popd
fi
```

Git か、ビルド自動化を有効にした Zip デプロイを利用してすべての変更をコミットし、コードをデプロイします。 これで Composer はデプロイの自動化の一部として実行しているはずです。

## <a name="run-gruntbowergulp"></a>Grunt/Bower/Gulp を実行する

Grunt、Bower、Gulp など、一般的な自動化ツールを App Service でデプロイ時に実行する場合、[カスタム デプロイ スクリプト](https://github.com/projectkudu/kudu/wiki/Custom-Deployment-Script)を提供する必要があります。 App Service では、Git か、ビルド自動化を有効にした [Zip デプロイ](deploy-zip.md)を利用してデプロイするとき、このスクリプトが実行されます。 

リポジトリでこれらのツールを実行できるようにするには、*package.json* での依存関係にこれらを追加する必要があります。 次に例を示します。

```json
"dependencies": {
  "bower": "^1.7.9",
  "grunt": "^1.0.1",
  "gulp": "^3.9.1",
  ...
}
```

ローカル ターミナル ウィンドウから、ディレクトリをリポジトリのルートに変更し、次のコマンドを実行します ([npm](https://www.npmjs.com/get-npm) をインストールしておく必要があります)。

```bash
npm install kuduscript -g
kuduscript --node --scriptType bash --suppressPrompt
```

リポジトリのルートには現在、 *.deployment* と *deploy.sh* の 2 つの追加ファイルがあります。

*deploy.sh* を開き、次のような `Deployment` セクションを見つけます。

```bash
##################################################################################################################################
# Deployment
# ----------
```

このセクションは、`npm install --production` の実行で終わります。 `Deployment` セクションの *末尾* に必要なツールの実行に必要なコード セクションを追加します。

- [Bower](#bower)
- [Gulp](#gulp)
- [Grunt](#grunt)

[MEAN.js サンプルでの例](https://github.com/Azure-Samples/meanjs/blob/master/deploy.sh#L112-L135)を参照してください。ここでは、デプロイ スクリプトはカスタム `npm install` コマンドも実行します。

### <a name="bower"></a>Bower

このスニペットは `bower install` を実行します。

```bash
if [ -e "$DEPLOYMENT_TARGET/bower.json" ]; then
  cd "$DEPLOYMENT_TARGET"
  eval ./node_modules/.bin/bower install
  exitWithMessageOnError "bower failed"
  cd - > /dev/null
fi
```

### <a name="gulp"></a>Gulp

このスニペットは `gulp imagemin` を実行します。

```bash
if [ -e "$DEPLOYMENT_TARGET/gulpfile.js" ]; then
  cd "$DEPLOYMENT_TARGET"
  eval ./node_modules/.bin/gulp imagemin
  exitWithMessageOnError "gulp failed"
  cd - > /dev/null
fi
```

### <a name="grunt"></a>Grunt

このスニペットは `grunt` を実行します。

```bash
if [ -e "$DEPLOYMENT_TARGET/Gruntfile.js" ]; then
  cd "$DEPLOYMENT_TARGET"
  eval ./node_modules/.bin/grunt
  exitWithMessageOnError "Grunt failed"
  cd - > /dev/null
fi
```

::: zone-end

::: zone pivot="platform-linux"

## <a name="customize-build-automation"></a>ビルドの自動化のカスタマイズ

ビルドの自動化を有効にして Git または zip パッケージを使用してアプリをデプロイする場合、App Service のビルドの自動化によって、次の手順が実行されます。

1. `PRE_BUILD_SCRIPT_PATH` によって指定された場合、カスタム スクリプトを実行します。
1. `php composer.phar install` を実行します。
1. `POST_BUILD_SCRIPT_PATH` によって指定された場合、カスタム スクリプトを実行します。

`PRE_BUILD_COMMAND` および `POST_BUILD_COMMAND` は、既定では空の環境変数です。 ビルド前のコマンドを実行するには、`PRE_BUILD_COMMAND` を定義します。 ビルド後のコマンドを実行するには、`POST_BUILD_COMMAND` を定義します。

次の例では、一連のコマンドに対して 2 つの変数をコンマ区切りで指定しています。

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings PRE_BUILD_COMMAND="echo foo, scripts/prebuild.sh"
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings POST_BUILD_COMMAND="echo foo, scripts/postbuild.sh"
```

ビルドの自動化をカスタマイズするためのその他の環境変数については、「[Oryx の構成](https://github.com/microsoft/Oryx/blob/master/doc/configuration.md)」を参照してください。

Linux 上で App Service によって PHP アプリが実行されビルドされる方法に関する詳細については、[Oryx ドキュメントの PHP アプリが検出されビルドされる方法](https://github.com/microsoft/Oryx/blob/master/doc/runtimes/php.md)に関するページを参照してください。

## <a name="customize-start-up"></a>スタートアップのカスタマイズ

既定では、組み込みの PHP コンテナーによって Apache サーバーが実行されます。 起動時に `apache2ctl -D FOREGROUND"` を実行します。 必要に応じて、[Cloud Shell](https://shell.azure.com) で次のコマンドを実行することにより、起動時に別のコマンドを実行できます。

```azurecli-interactive
az webapp config set --resource-group <resource-group-name> --name <app-name> --startup-file "<custom-command>"
```

::: zone-end

## <a name="access-environment-variables"></a>環境変数へのアクセス

App Service では、アプリ コードの外部で[アプリ設定を指定](configure-common.md#configure-app-settings)できます。 その後、標準の [getenv()](https://secure.php.net/manual/function.getenv.php) パターンを使用して、それらにアクセスできます。 たとえば、`DB_HOST` というアプリ設定にアクセスするには、次のコードを使用します。

```php
getenv("DB_HOST")
```

## <a name="change-site-root"></a>サイト ルートを変更する

::: zone pivot="platform-windows"  

選択した Web フレームワークでは、サイト ルートとしてサブディレクトリを使用できます。 たとえば、[Laravel](https://laravel.com/) では、サイト ルートとして *public/* サブディレクトリが使用されます。

サイト ルートをカスタマイズするには、[`az resource update`](/cli/azure/resource#az-resource-update) コマンドを利用し、アプリの仮想アプリケーション パスを設定します。 次の例では、リポジトリの *public/* サブディレクトリにサイト ルートが設定されます。 

```azurecli-interactive
az resource update --name web --resource-group <group-name> --namespace Microsoft.Web --resource-type config --parent sites/<app-name> --set properties.virtualApplications[0].physicalPath="site\wwwroot\public" --api-version 2015-06-01
```

既定では、Azure App Service は、デプロイされたアプリケーション ファイルのルート ディレクトリ (_sites\wwwroot_) に対して仮想アプリケーションのルート パス ( _/_ ) をポイントします。

::: zone-end

::: zone pivot="platform-linux"

選択した Web フレームワークでは、サイト ルートとしてサブディレクトリを使用できます。 たとえば、[Laravel](https://laravel.com/) は、サイト ルートとして `public/` サブディレクトリを使用します。

App Service の既定の PHP イメージでは Apache が使用されていて、アプリ用にサイト ルートをカスタマイズすることはできません。 この制限を回避するには、次の内容で *.htaccess* ファイルをリポジトリ ルートに追加します。

```
<IfModule mod_rewrite.c>
    RewriteEngine on
    RewriteCond %{REQUEST_URI} ^(.*)
    RewriteRule ^(.*)$ /public/$1 [NC,L,QSA]
</IfModule>
```

*.htaccess* の書き換えを行わない場合は、代わりに [カスタム Docker イメージ](quickstart-custom-container.md)を使用して Laravel アプリケーションをデプロイできます。

::: zone-end

## <a name="detect-https-session"></a>HTTPS セッションの検出

App Service では、[SSL 終了](https://wikipedia.org/wiki/TLS_termination_proxy)がネットワーク ロード バランサーで発生するため、すべての HTTPS リクエストは暗号化されていない HTTP リクエストとしてアプリに到達します。 ユーザー要求が暗号化されているかどうかをアプリ ロジックが確認する必要がある場合は、`X-Forwarded-Proto` ヘッダーを調べます。

```php
if (isset($_SERVER['HTTP_X_FORWARDED_PROTO']) && $_SERVER['HTTP_X_FORWARDED_PROTO'] === 'https') {
// Do something when HTTPS is used
}
```

一般的な Web フレームワークでは、標準のアプリ パターンで `X-Forwarded-*` 情報にアクセスできます。 [CodeIgniter](https://codeigniter.com/) では、[is_https ()](https://github.com/bcit-ci/CodeIgniter/blob/master/system/core/Common.php#L338-L365) は既定で `X_FORWARDED_PROTO` の値をチェックします。

## <a name="customize-phpini-settings"></a>php.ini 設定をカスタマイズする

PHP のインストールを変更する必要がある場合は、以下の手順に従って、いずれかの [php.ini ディレクティブ](https://www.php.net/manual/ini.list.php)を変更できます。

> [!NOTE]
> PHP のバージョンと現在の *php.ini* 構成を確認する最善の方法は、アプリで [phpinfo()](https://php.net/manual/function.phpinfo.php) を呼び出すことです。
>

### <a name="customize-non-php_ini_system-directives"></a><a name="Customize-non-PHP_INI_SYSTEM directives"></a> 非 PHP_INI_SYSTEM ディレクティブをカスタマイズする

::: zone pivot="platform-windows"  

PHP_INI_USER、PHP_INI_PERDIR、PHP_INI_ALL ディレクティブ ([php.ini ディレクティブ](https://www.php.net/manual/ini.list.php)を参照) をカスタマイズするには、`.user.ini` ファイルをアプリのルート ディレクトリに追加します。

`php.ini` ファイルで使用するものと同じ構文を使用して、構成設定を `.user.ini` ファイルに追加します。 たとえば、`display_errors` 設定をオンにして `upload_max_filesize` を 10 M に設定する場合は、`.user.ini` ファイルに次のテキストを含めます。

```
 ; Example Settings
 display_errors=On
 upload_max_filesize=10M

 ; Write errors to d:\home\LogFiles\php_errors.log
 ; log_errors=On
```

変更内容でアプリを再デプロイし、再起動します。

`.user.ini` ファイルを使用する代わりに、アプリで [ini_set()](https://www.php.net/manual/function.ini-set.php) を使用し、これらの非 PHP_INI_SYSTEM ディレクティブをカスタマイズできます。

::: zone-end

::: zone pivot="platform-linux"

PHP_INI_USER、PHP_INI_PERDIR、および PHP_INI_ALL ディレクティブ ([php.ini ディレクティブ](https://www.php.net/manual/ini.list.php)を参照) をカスタマイズするには、 *.htaccess* ファイルをアプリのルート ディレクトリに追加します。

*.htaccess* ファイルで、`php_value <directive-name> <value>` 構文を使用してディレクティブを追加します。 次に例を示します。

```
php_value upload_max_filesize 1000M
php_value post_max_size 2000M
php_value memory_limit 3000M
php_value max_execution_time 180
php_value max_input_time 180
php_value display_errors On
php_value upload_max_filesize 10M
```

変更内容でアプリを再デプロイし、再起動します。 Kudu でデプロイする場合 (たとえば [Git](deploy-local-git.md) を使用)、デプロイ後に自動的に再起動します。

*.htaccess* を使用する代わりに、アプリで [ini_set()](https://www.php.net/manual/function.ini-set.php) を使用して、これらの非 PHP_INI_SYSTEM ディレクティブをカスタマイズできます。

::: zone-end

### <a name="customize-php_ini_system-directives"></a><a name="customize-php_ini_system-directives"></a>PHP_INI_SYSTEM ディレクティブをカスタマイズする

::: zone pivot="platform-windows"  

PHP_INI_SYSTEM ディレクティブをカスタマイズするには ([php.ini ディレクティブ](https://www.php.net/manual/ini.list.php)を参照)、 *.htaccess* アプローチは使用できません。 App Service は、`PHP_INI_SCAN_DIR` アプリ設定を使用して、別のメカニズムを提供します。

最初に、[Cloud Shell](https://shell.azure.com) で次のコマンドを実行して、`PHP_INI_SCAN_DIR` というアプリ設定を追加します。

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings PHP_INI_SCAN_DIR="d:\home\site\ini"
```

Kudu コンソール (`https://<app-name>.scm.azurewebsites.net/DebugConsole`) に移動し、`d:\home\site` に移動します。

`d:\home\site` に `ini` というディレクトリを作成し、続いてカスタマイズするディレクティブを使用した *.ini* ファイル (たとえば、*settings.ini*) を `d:\home\site\ini` ディレクトリに作成します。 *php.ini* ファイルで使用するものと同じ構文を使用します。 

たとえば、[expose_php](https://php.net/manual/ini.core.php#ini.expose-php) の値を変更するには、次のコマンドを実行します。

```bash
cd /home/site
mkdir ini
echo "expose_php = Off" >> ini/setting.ini
```

変更内容を有効にするには、アプリを再起動します。

::: zone-end

::: zone pivot="platform-linux"

PHP_INI_SYSTEM ディレクティブをカスタマイズするには ([php.ini ディレクティブ](https://www.php.net/manual/ini.list.php)を参照)、 *.htaccess* アプローチは使用できません。 App Service は、`PHP_INI_SCAN_DIR` アプリ設定を使用して、別のメカニズムを提供します。

最初に、[Cloud Shell](https://shell.azure.com) で次のコマンドを実行して、`PHP_INI_SCAN_DIR` というアプリ設定を追加します。

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings PHP_INI_SCAN_DIR="/usr/local/etc/php/conf.d:/home/site/ini"
```

`/usr/local/etc/php/conf.d` は、*php.ini* が存在している既定のディレクトリです。 `/home/site/ini` は、カスタム *.ini* ファイルを追加するカスタム ディレクトリです。 `:` で値を区切ります。

Linux コンテナーを含む Web SSH セッションに移動します (`https://<app-name>.scm.azurewebsites.net/webssh/host`)。

`/home/site` に `ini` というディレクトリを作成し、続いてカスタマイズするディレクティブを使用した *.ini* ファイル (たとえば、*settings.ini*) を `/home/site/ini` ディレクトリに作成します。 *php.ini* ファイルで使用するものと同じ構文を使用します。 

> [!TIP]
> App Service でのビルトイン Linux コンテナーで、 */home* は永続化された共有ストレージとして使用されます。 
>

たとえば、[expose_php](https://php.net/manual/ini.core.php#ini.expose-php) の値を変更するには、次のコマンドを実行します。

```bash
cd /home/site
mkdir ini
echo "expose_php = Off" >> ini/setting.ini
```

変更内容を有効にするには、アプリを再起動します。

::: zone-end

## <a name="enable-php-extensions"></a>PHP 拡張機能を有効にする

::: zone pivot="platform-windows"  

ビルトイン PHP のインストールには、最もよく使用される拡張機能が含まれています。 [php.ini ディレクティブのカスタマイズ](#customize-php_ini_system-directives)と同じ方法で、追加の拡張機能を有効にすることができます。

> [!NOTE]
> PHP のバージョンと現在の *php.ini* 構成を確認する最善の方法は、アプリで [phpinfo()](https://php.net/manual/function.phpinfo.php) を呼び出すことです。
>

追加の拡張機能を有効にするには、次の手順に従います。

アプリのルート ディレクトリに `bin` ディレクトリを追加して、その中に `.dll` 拡張ファイル (たとえば、*mongodb.dll*) を配置します。 拡張機能が Azure の PHP バージョンと互換性があり、VC9 および非スレッドセーフ (nts) 互換であることを確認してください。

変更をデプロイします。

[PHP_INI_SYSTEM ディレクティブのカスタマイズ](#customize-php_ini_system-directives)の手順に従って、[extension](https://www.php.net/manual/ini.core.php#ini.extension) または [zend_extension](https://www.php.net/manual/ini.core.php#ini.zend-extension) ディレクティブを使用したカスタム *.ini* ファイルに拡張機能を追加します。

```
extension=d:\home\site\wwwroot\bin\mongodb.dll
zend_extension=d:\home\site\wwwroot\bin\xdebug.dll
```

変更内容を有効にするには、アプリを再起動します。

::: zone-end

::: zone pivot="platform-linux"

ビルトイン PHP のインストールには、最もよく使用される拡張機能が含まれています。 [php.ini ディレクティブのカスタマイズ](#customize-php_ini_system-directives)と同じ方法で、追加の拡張機能を有効にすることができます。

> [!NOTE]
> PHP のバージョンと現在の *php.ini* 構成を確認する最善の方法は、アプリで [phpinfo()](https://php.net/manual/function.phpinfo.php) を呼び出すことです。
>

追加の拡張機能を有効にするには、次の手順に従います。

アプリのルート ディレクトリに `bin` ディレクトリを追加して、その中に `.so` 拡張ファイル (たとえば、*mongodb.so*) を配置します。 拡張機能が Azure の PHP バージョンと互換性があり、VC9 および非スレッドセーフ (nts) 互換であることを確認してください。

変更をデプロイします。

[PHP_INI_SYSTEM ディレクティブのカスタマイズ](#customize-php_ini_system-directives)の手順に従って、[extension](https://www.php.net/manual/ini.core.php#ini.extension) または [zend_extension](https://www.php.net/manual/ini.core.php#ini.zend-extension) ディレクティブを使用したカスタム *.ini* ファイルに拡張機能を追加します。

```ini
extension=/home/site/wwwroot/bin/mongodb.so
zend_extension=/home/site/wwwroot/bin/xdebug.so
```

変更内容を有効にするには、アプリを再起動します。

::: zone-end

## <a name="access-diagnostic-logs"></a>診断ログにアクセスする

::: zone pivot="platform-windows"  

標準の [error_log()](https://php.net/manual/function.error-log.php) ユーティリティを使用し、Azure App Service に診断ログを表示させます。

[!INCLUDE [Access diagnostic logs](../../includes/app-service-web-logs-access-no-h.md)]

::: zone-end

::: zone pivot="platform-linux"

[!INCLUDE [Access diagnostic logs](../../includes/app-service-web-logs-access-linux-no-h.md)]

::: zone-end

## <a name="troubleshooting"></a>トラブルシューティング

動作中の PHP アプリが App Service で異なる動作をしたり、エラーが発生した場合は、次のことを試してください。

- [ログ ストリームにアクセス](#access-diagnostic-logs)します。
- 実稼働モードでローカルにアプリをテストします。 App Service では、アプリが実稼働モードで実行されるので、プロジェクトがローカルで実稼働モードで予想どおりに動作することを確認する必要があります。 次に例を示します。
    - *composer.json* に応じて、実稼働モードに別々のパッケージ (`require` と `require-dev`) がインストールされる場合があります。
    - 特定の Web フレームワークでは、実稼働モードで静的ファイルを別にデプロイすることがあります。
    - 特定の Web フレームワークでは、実稼働モードで実行しているときにカスタム スタートアップ スクリプトを使用することがあります。
- デバッグ モードで Azure App Service でアプリを実行します。 たとえば、[Laravel](https://meanjs.org/) で、[`APP_DEBUG` アプリ設定を `true` に指定](configure-common.md#configure-app-settings)することにより、実稼働環境でのデバッグ メッセージを出力するようにアプリを構成できます。

::: zone pivot="platform-linux"

[!INCLUDE [robots933456](../../includes/app-service-web-configure-robots933456.md)]

::: zone-end

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [チュートリアル:PHP アプリと MySQL](tutorial-php-mysql-app.md)

::: zone pivot="platform-linux"

> [!div class="nextstepaction"]
> [App Service Linux の FAQ](faq-app-service-linux.md)

::: zone-end

