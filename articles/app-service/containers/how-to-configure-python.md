---
title: Python アプリを構成する - Azure App Service
description: このチュートリアルでは、Azure App Service on Linux 向けに Python アプリを作成および構成するためのオプションについて説明します。
services: app-service\web
documentationcenter: ''
author: cephalin
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 03/28/2019
ms.author: astay;cephalin;kraigb
ms.custom: seodec18
ms.openlocfilehash: f8894132dae179be2d5d9d9b6887851be78d7746
ms.sourcegitcommit: 031e4165a1767c00bb5365ce9b2a189c8b69d4c0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/13/2019
ms.locfileid: "59548151"
---
# <a name="configure-a-linux-python-app-for-azure-app-service"></a>Azure App Service 向けの Linux Python アプリを構成する

この記事では、[Azure App Service](app-service-linux-intro.md) で Python アプリが実行される方法と、必要に応じて App Service の動作をカスタマイズする方法について説明します。 Python アプリは、必要なすべての [pip](https://pypi.org/project/pip/) モジュールと共にデプロイする必要があります。

App Service デプロイ エンジンは、自動的に仮想環境をアクティブ化し、[Git リポジトリ](../deploy-local-git.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json)をデプロイするとき、またはビルド プロセスがオンになっている [Zip パッケージ](../deploy-zip.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json)をデプロイするときに、`pip install -r requirements.txt` を実行します。

このガイドでは、App Service の組み込み Linux コンテナーを使用する Python 開発者のために、主要な概念と手順を示します。 Azure App Service を使用したことがない場合は、まず [Python クイック スタート](quickstart-python.md)と [PostgreSQL を使った Python のチュートリアル](tutorial-python-postgresql-app.md)に従ってください。

> [!NOTE]
> Linux は現在、App Service で Python アプリを実行するための推奨されるオプションです。 Windows オプションについては、[Windows フレーバーの App Service での Python](https://docs.microsoft.com/visualstudio/python/managing-python-on-azure-app-service) に関するページを参照してください。
>

## <a name="show-python-version"></a>Python バージョンの表示

現在の Python バージョンを表示するには、[Cloud Shell](https://shell.azure.com) で次のコマンドを実行します。

```azurecli-interactive
az webapp config show --resource-group <resource-group-name> --name <app-name> --query linuxFxVersion
```

サポートされているすべての Python バージョンを表示するには、[Cloud Shell](https://shell.azure.com) で次のコマンドを実行します。

```azurecli-interactive
az webapp list-runtimes --linux | grep PYTHON
```

サポートされない Python バージョンを実行するには、代わりに独自のコンテナー イメージを作成します。 詳細については、[カスタム Docker イメージの使用](tutorial-custom-docker-image.md)に関するページを参照してください。

## <a name="set-python-version"></a>Python バージョンの設定

Python バージョンを 3.7 に設定するには、[Cloud Shell](https://shell.azure.com) で次のコマンドを実行します。

```azurecli-interactive
az webapp config set --resource-group <resource-group-name> --name <app-name> --linux-fx-version "PYTHON|3.7"
```

## <a name="container-characteristics"></a>コンテナーの特性

App Service on Linux にデプロイされた Python アプリは、GitHub リポジトリの [Python 3.6](https://github.com/Azure-App-Service/python/tree/master/3.6.6) または [Python 3.7](https://github.com/Azure-App-Service/python/tree/master/3.7.0) で定義されている Docker コンテナー内で実行されます。

このコンテナーには次の特性があります。

- アプリは、[Gunicorn WSGI HTTP サーバー](https://gunicorn.org/)を使用して実行されます。このとき、追加の引数 `--bind=0.0.0.0 --timeout 600` が使用されます。

- 既定では、基本のイメージに Flask Web フレームワークが含まれています。ただし、コンテナーは、WSGI に準拠していて Python 3.7 と互換性のある他のフレームワーク (Django など) をサポートしています。

- Django など、追加のパッケージをインストールするには、`pip freeze > requirements.txt` を使用して、お客様のプロジェクトのルートに [*requirements.txt*](https://pip.pypa.io/en/stable/user_guide/#requirements-files) ファイルを作成します。 次に、Git デプロイを使用してお客様のプロジェクトを App Service に公開します。これにより、コンテナー内で `pip install -r requirements.txt` が自動的に実行され、お客様のアプリの依存関係がインストールされます。

## <a name="container-startup-process"></a>コンテナーのスタートアップ プロセス

App Service on Linux コンテナーでは、起動中に次の手順が実行されます。

1. [カスタム スタートアップ コマンド](#customize-startup-command)が提供されている場合は、これを使用します。
2. [Django アプリ](#django-app)の存在を確認し、検出された場合はそれための Gunicorn を起動します。
3. [Flask アプリ](#flask-app)の存在を確認し、検出された場合はそれための Gunicorn を起動します。
4. 他のアプリが見つからない場合は、コンテナーに組み込まれている既定のアプリを起動します。

次のセクションでは、各オプションについてさらに詳しく説明します。

### <a name="django-app"></a>Django アプリ

Django アプリの場合、App Service によってお客様のアプリ コード内で `wsgi.py` という名前のファイルが検索され、次のコマンドを使用して Gunicorn が実行されます。

```bash
# <module> is the path to the folder that contains wsgi.py
gunicorn --bind=0.0.0.0 --timeout 600 <module>.wsgi
```

スタートアップ コマンドをより細かく制御したい場合は、[カスタム スタートアップ コマンド](#customize-startup-command)を使用し、`<module>` を、*wsgi.py* が含まれているモジュールの名前に置き換えます。

### <a name="flask-app"></a>Flask アプリ

Flask の場合、App Service によって *application.py* または *app.py* という名前のファイルが検索され、Gunicorn が次のように起動されます。

```bash
# If application.py
gunicorn --bind=0.0.0.0 --timeout 600 application:app
# If app.py
gunicorn --bind=0.0.0.0 --timeout 600 app:app
```

お客様のメイン アプリ モジュールが別のファイルに含まれている場合は、アプリ オブジェクトに別の名前を使用します。また、Gunicorn に追加の引数を指定したい場合は、[カスタム スタートアップ コマンド](#customize-startup-command)を使用します。

### <a name="default-behavior"></a>既定の動作

カスタム コマンド、Django アプリ、または Flask アプリが App Service によって検出されない場合は、_opt/defaultsite_ フォルダーにある既定の読み取り専用アプリが実行されます。 既定のアプリは次のように表示されます。

![App Service on Linux の既定の Web ページ](media/how-to-configure-python/default-python-app.png)

## <a name="customize-startup-command"></a>スタートアップ コマンドのカスタマイズ

カスタム Gunicorn スタートアップ コマンドを指定することで、コンテナーの起動動作を制御できます。 これを行うには、[Cloud Shell](https://shell.azure.com) で次のコマンドを実行します。

```azurecli-interactive
az webapp config set --resource-group <resource-group-name> --name <app-name> --startup-file "<custom-command>"
```

たとえば、メイン モジュールが *hello.py* で、そのファイルにおける Flask アプリ オブジェクトの名前が `myapp` である Flask アプリがある場合、*\<custom-command>* は次のようになります。

```bash
gunicorn --bind=0.0.0.0 --timeout 600 hello:myapp
```

メイン モジュールがサブフォルダー (`website` など) に存在する場合、そのフォルダーを `--chdir` 引数で指定します。

```bash
gunicorn --bind=0.0.0.0 --timeout 600 --chdir website hello:myapp
```

また、`--workers=4` のように、Gunicorn の追加の引数を *\<custom-command>* に追加することもできます。 詳細については、「[Running Gunicorn (Gunicorn の実行)」](https://docs.gunicorn.org/en/stable/run.html) (docs.gunicorn.org) を参照してください。

[aiohttp](https://aiohttp.readthedocs.io/en/stable/web_quickstart.html) のような Gunicorn 以外のサーバーを使用するには、*\<custom-command>* を次のように置き換えることができます。

```bash
python3.7 -m aiohttp.web -H localhost -P 8080 package.module:init_func
```

> [!Note]
> App Service では、カスタム コマンド ファイルの処理中に発生したエラーが無視されて、Django アプリおよび Flask アプリが検索されることでスタートアップ プロセスが続行されます。 意図した動作が得られない場合は、お客様のスタートアップ ファイルが App Service にデプロイされていること、およびそのファイルにエラーが含まれていないことを確認してください。

## <a name="access-environment-variables"></a>環境変数へのアクセス

App Service では、アプリ コードの外部で[アプリ設定を指定](../web-sites-configure.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#app-settings)できます。 その後、標準の [os.environ](https://docs.python.org/3/library/os.html#os.environ) パターンを使用して、それらにアクセスできます。 たとえば、`WEBSITE_SITE_NAME` というアプリ設定にアクセスするには、次のコードを使用します。

```python
os.environ['WEBSITE_SITE_NAME']
```

## <a name="detect-https-session"></a>HTTPS セッションの検出

App Service では、[SSL 終了](https://wikipedia.org/wiki/TLS_termination_proxy)がネットワーク ロード バランサーで発生するため、すべての HTTPS リクエストは暗号化されていない HTTP リクエストとしてアプリに到達します。 ユーザー要求が暗号化されているかどうかをアプリ ロジックが確認する必要がある場合は、`X-Forwarded-Proto` ヘッダーを調べます。

```python
if 'X-Forwarded-Proto' in request.headers and request.headers['X-Forwarded-Proto'] == 'https':
# Do something when HTTPS is used
```

一般的な Web フレームワークでは、標準のアプリ パターンで `X-Forwarded-*` 情報にアクセスできます。 [CodeIgniter](https://codeigniter.com/) では、[is_https ()](https://github.com/bcit-ci/CodeIgniter/blob/master/system/core/Common.php#L338-L365) は既定で `X_FORWARDED_PROTO` の値をチェックします。

## <a name="access-diagnostic-logs"></a>診断ログにアクセスする

[!INCLUDE [Access diagnostic logs](../../../includes/app-service-web-logs-access-no-h.md)]

## <a name="open-ssh-session-in-browser"></a>ブラウザーで SSH セッションを開く

[!INCLUDE [Open SSH session in browser](../../../includes/app-service-web-ssh-connect-builtin-no-h.md)]

## <a name="troubleshooting"></a>トラブルシューティング

- **自分のアプリ コードをデプロイした後に既定のアプリが表示される。** 既定のアプリは、アプリ コードが App Service にデプロイされていない場合、またはアプリ コードが App Service によって検出されず、代わりに既定のアプリが実行された場合に表示されます。
- App Service を再起動し、15 から 20 秒待って、アプリをもう一度確認します。
- Windows ベースのインスタンスではなく、App Service for Linux が使用されていることを確認してください。 Azure CLI から `az webapp show --resource-group <resource_group_name> --name <app_service_name> --query kind` コマンドを実行します。`<resource_group_name>` と `<app_service_name>` は適宜置き換えてください。 出力として `app,linux` が表示されるはずです。それ以外の場合は、App Service を再作成し、Linux を選択してください。
- SSH または Kudu コンソールを使用して App Service に直接接続し、お客様のファイルが *site/wwwroot* に存在することを確認します。 ファイルが存在しない場合は、デプロイ プロセスを見直してアプリを再デプロイします。
- ファイルが存在する場合は、お客様固有のスタートアップ ファイルを App Service が識別できていません。 [Django](#django-app) または [Flask](#flask-app) に関して App Service で想定されているとおりにお客様のアプリが構造化されていることをチェックします。または、[カスタム スタートアップ コマンド](#customize-startup-command)を使用します。
- **ブラウザーに "サービスは利用できません" というメッセージが表示される。** ブラウザーは App Service からの応答を待ってタイムアウトしました。これは、App Service によって Gunicorn サーバーが起動されたもののアプリ コードを指定する引数が正しくないことを示しています。
- ブラウザーを最新の情報に更新します (特に、お客様が App Service プランの最も低い価格レベルを使用している場合)。 たとえば、無料のレベルを使用しているときは、アプリの起動にかかる時間が長くなることがあります。その場合、ブラウザーを最新の情報に更新すると、応答が速くなります。
- [Django](#django-app) または [Flask](#flask-app) に関して App Service で想定されているとおりにお客様のアプリが構造化されていることをチェックします。または、[カスタム スタートアップ コマンド](#customize-startup-command)を使用します。
- [ログ ストリームにアクセス](#access-diagnostic-logs)します。

## <a name="next-steps"></a>次の手順

App Service on Linux の組み込み Python イメージは現在プレビュー段階であり、アプリを開始するために使用するコマンドをカスタマイズすることができます。 代わりにカスタム コンテナーを使用して運用環境向け Python アプリを作成することもできます。

> [!div class="nextstepaction"]
> [チュートリアル:PostgreSQL を使った Python アプリ](tutorial-python-postgresql-app.md)

> [!div class="nextstepaction"]
> [チュートリアル:プライベート コンテナー リポジトリからデプロイする](tutorial-custom-docker-image.md)

> [!div class="nextstepaction"]
> [App Service Linux の FAQ](app-service-linux-faq.md)