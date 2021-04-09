---
title: Ruby アプリを構成する - Azure App Service
description: アプリ用に事前構築済みの Ruby コンテナーを構成する方法について説明します。 この記事では、最も一般的な構成タスクを紹介しています。
ms.topic: quickstart
ms.date: 06/18/2020
ms.reviewer: astay; kraigb
ms.custom: mvc, seodec18, devx-track-azurecli
ms.openlocfilehash: 038d62573b491325adc60647debf17fa87e06cfe
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "92743685"
---
# <a name="configure-a-linux-ruby-app-for-azure-app-service"></a>Azure App Service 向けの Linux Ruby アプリを構成する

この記事では、[Azure App Service](overview.md) を使用して Linux コンテナー内で Ruby アプリを実行する方法と、必要に応じて App Service の動作をカスタマイズする方法について説明します。 Ruby アプリは、必要なすべての [gems](https://rubygems.org/gems) と共にデプロイする必要があります。

このガイドでは、App Service のビルトイン Linux コンテナーを使用する Ruby 開発者のために、主要な概念と手順を紹介します。 Azure App Service を使用したことがない場合は、まず [Ruby 用のクイック スタート](quickstart-ruby.md)と [PostgreSQL を使った Ruby のチュートリアル](tutorial-ruby-postgres-app.md)に従ってください。

## <a name="show-ruby-version"></a>Ruby のバージョンを表示する

Ruby の現在のバージョンを表示するには、[Cloud Shell](https://shell.azure.com) で次のコマンドを実行します。

```azurecli-interactive
az webapp config show --resource-group <resource-group-name> --name <app-name> --query linuxFxVersion
```

Ruby のサポートされているすべてのバージョンを表示するには、[Cloud Shell](https://shell.azure.com) で次のコマンドを実行します。

```azurecli-interactive
az webapp list-runtimes --linux | grep RUBY
```

サポートされないバージョンの Ruby を実行するには、代わりに独自のコンテナー イメージを作成します。 詳細については、[カスタム Docker イメージの使用](tutorial-custom-container.md?pivots=container-linux)に関するページを参照してください。

## <a name="set-ruby-version"></a>Ruby のバージョンを設定する

Ruby のバージョンを 2.3 に設定するには、[Cloud Shell](https://shell.azure.com) で次のコマンドを実行します。

```azurecli-interactive
az webapp config set --resource-group <resource-group-name> --name <app-name> --linux-fx-version "RUBY|2.3"
```

> [!NOTE]
> デプロイ中に次のようなエラーが表示されることがあります。
> ```
> Your Ruby version is 2.3.3, but your Gemfile specified 2.3.1
> ```
> or
> ```
> rbenv: version `2.3.1' is not installed
> ```
> これは、プロジェクトに構成されている Ruby のバージョンが、実行中のコンテナーにインストールされているバージョン (上の例では `2.3.3`) と異なることを意味します。 上の例では、*Gemfile* と *.ruby-version* の両方をチェックし、Ruby のバージョンが未設定になっているか、または実行中のコンテナーにインストールされているバージョン (上の例では `2.3.3`) に設定されていることを確認してください。

## <a name="access-environment-variables"></a>環境変数へのアクセス

App Service では、アプリ コードの外部で[アプリ設定を指定](configure-common.md#configure-app-settings)できます。 その後、標準の [ENV['\<path-name>']](https://ruby-doc.org/core-2.3.3/ENV.html) パターンを使用して、それらにアクセスできます。 たとえば、`WEBSITE_SITE_NAME` というアプリ設定にアクセスするには、次のコードを使用します。

```ruby
ENV['WEBSITE_SITE_NAME']
```

## <a name="customize-deployment"></a>デプロイのカスタマイズ

[Git リポジトリ](deploy-local-git.md)またはビルド プロセスがオンになっている [Zip パッケージ](deploy-zip.md)をデプロイすると、デプロイ後にデプロイ エンジン (Kudu) によって既定で次のステップが自動的に実行されます。

1. *Gemfile* が存在するかどうかを確認します。
1. `bundle clean` を実行します。 
1. `bundle install --path "vendor/bundle"` を実行します。
1. `bundle package` を実行して、vendor/cache フォルダーに gems をパッケージします。

### <a name="use---without-flag"></a>--without フラグを使用する

[--without](https://bundler.io/man/bundle-install.1.html) フラグを使用して `bundle install` を実行するには、`BUNDLE_WITHOUT` [アプリ設定](configure-common.md#configure-app-settings)に一連のグループをコンマ区切りで指定します。 たとえば、次のコマンドでは `development,test` に設定しています。

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings BUNDLE_WITHOUT="development,test"
```

この設定が定義されている場合、`bundle install` は、デプロイ エンジンによって `--without $BUNDLE_WITHOUT` を使用して実行されます。

### <a name="precompile-assets"></a>アセットをプリコンパイルする

既定では、デプロイ後のステップでアセットがプリコンパイルされることはありません。 アセットのプリコンパイルを有効にするには、`ASSETS_PRECOMPILE` [アプリ設定](configure-common.md#configure-app-settings)を `true` に設定します。 これで、デプロイ後ステップの最後に `bundle exec rake --trace assets:precompile` コマンドが実行されます。 次に例を示します。

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings ASSETS_PRECOMPILE=true
```

詳細については、「[静的アセットを提供する](#serve-static-assets)」を参照してください。

## <a name="customize-start-up"></a>スタートアップのカスタマイズ

既定では、Ruby コンテナーによって Rails サーバーが次の順序で起動されます (詳細については、[スタートアップ スクリプト](https://github.com/Azure-App-Service/ruby/blob/master/2.3.8/startup.sh)を参照してください)。

1. [secret_key_base](https://edgeguides.rubyonrails.org/security.html#environmental-security) の値がまだ存在しない場合は、この値を生成します。 アプリを実稼働モードで実行するためには、この値が必要になります。
1. `RAILS_ENV` 環境変数を `production` に設定します。
1. これまでに Rails サーバーを実行したことで *.pid* ファイルが残っていれば *tmp/pids* ディレクトリからすべて削除します。
1. すべての依存関係がインストール済みであるかどうかを確認します。 インストールされていない場合は、ローカル *vendor/cache* ディレクトリから gems をインストールします。
1. `rails server -e $RAILS_ENV` を実行します。

このスタートアップ プロセスは、次の方法でカスタマイズできます。

- [静的アセットを提供する](#serve-static-assets)
- [非実稼働モードで実行する](#run-in-non-production-mode)
- [secret_key_base を手動で設定する](#set-secret_key_base-manually)

### <a name="serve-static-assets"></a>静的アセットを提供する

Ruby コンテナー内の Rails サーバーは、既定では実稼働モードで実行され、また、[アセットがプリコンパイル済みで Web サーバーから提供されることを想定](https://guides.rubyonrails.org/asset_pipeline.html#in-production)しています。 Rails サーバーから静的アセットを提供するには、次の 2 つのことを行う必要があります。

- **アセットをプリコンパイルする** - [静的アセットをローカルでプリコンパイル](https://guides.rubyonrails.org/asset_pipeline.html#local-precompilation)し、それらを手動でデプロイします。 または、その処理をデプロイ エンジンで行います (「[アセットをプリコンパイルする](#precompile-assets)」を参照)。
- **静的ファイルの提供を有効にする** - Ruby コンテナーから静的アセットを提供するには、[`RAILS_SERVE_STATIC_FILES` アプリ設定を `true` に設定](configure-common.md#configure-app-settings)します。 次に例を示します。

    ```azurecli-interactive
    az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings RAILS_SERVE_STATIC_FILES=true
    ```

### <a name="run-in-non-production-mode"></a>非実稼働モードで実行する

既定では、Rails サーバーが実稼働モードで実行されます。 たとえば、開発モードで実行する場合は、`RAILS_ENV` [アプリ設定](configure-common.md#configure-app-settings)を `development` に設定することになります。

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings RAILS_ENV="development"
```

ただし、この設定だけを行った場合、Rails サーバーが開発モードで起動し、localhost 要求しか受け付けなくなるので、コンテナーの外部からアクセスすることができません。 リモートのクライアント要求を受け付けるには、`APP_COMMAND_LINE` [アプリ設定](configure-common.md#configure-app-settings)を `rails server -b 0.0.0.0` に設定してください。 このアプリ設定により、Ruby コンテナーでカスタム コマンドを実行することができます。 次に例を示します。

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings APP_COMMAND_LINE="rails server -b 0.0.0.0"
```

### <a name="set-secret_key_base-manually"></a><a name="set-secret_key_base-manually"></a> secret_key_base を手動で設定する

`secret_key_base` の値を App Service で自動的に生成するのではなく独自の値を使用するには、`SECRET_KEY_BASE` [アプリ設定](configure-common.md#configure-app-settings)にその値を設定します。 次に例を示します。

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings SECRET_KEY_BASE="<key-base-value>"
```

## <a name="access-diagnostic-logs"></a>診断ログにアクセスする

[!INCLUDE [Access diagnostic logs](../../includes/app-service-web-logs-access-no-h.md)]

## <a name="open-ssh-session-in-browser"></a>ブラウザーで SSH セッションを開く

[!INCLUDE [Open SSH session in browser](../../includes/app-service-web-ssh-connect-builtin-no-h.md)]

[!INCLUDE [robots933456](../../includes/app-service-web-configure-robots933456.md)]

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [チュートリアル:PostgreSQL を使った Rails アプリ](tutorial-ruby-postgres-app.md)

> [!div class="nextstepaction"]
> [App Service Linux の FAQ](faq-app-service-linux.md)
