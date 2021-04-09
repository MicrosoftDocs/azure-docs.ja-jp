---
title: 'クイックスタート: Linux で Python アプリを作成する'
description: App Service の Linux コンテナーに Python アプリをデプロイして、Azure App Service の使用を開始します。
ms.topic: quickstart
ms.date: 09/22/2020
ms.custom: seo-python-october2019, cli-validate, devx-track-python, devx-track-azurecli
zone_pivot_groups: python-frameworks-01
robots: noindex
ms.openlocfilehash: ef32b8ff9282e92511047840576ddfe6751b24e0
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102184510"
---
# <a name="quickstart-create-a-python-app-in-azure-app-service-on-linux"></a>クイック スタート:Azure App Service on Linux で Python アプリを作成する

このクイック スタートでは、Azure のスケーラビリティに優れた自己適用型の Web ホスティング サービスである [App Service on Linux](overview.md#app-service-on-linux) に、Python Web アプリをデプロイします。 Mac、Linux、または Windows コンピューター上でローカル [Azure コマンドライン インターフェイス (CLI)](/cli/azure/install-azure-cli) を使用して、Flask または Django のいずれかのフレームワークを使用したサンプルをデプロイします。 構成する Web アプリでは、App Service の Free レベルを使用するため、この記事の中で料金が発生することはありません。

> [!TIP]
> IDE を使用してアプリをデプロイする場合は、 **[Visual Studio Code から App Service への Python アプリのデプロイ](/azure/developer/python/tutorial-deploy-app-service-on-linux-01)** に関する記事をご覧ください。

## <a name="set-up-your-initial-environment"></a>初期環境を設定する

1. アクティブなサブスクリプションが含まれる Azure アカウントを用意します。 [無料でアカウントを作成できます](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)。
1. <a href="https://www.python.org/downloads/" target="_blank">Python 3.6 以降</a>をインストールします。
1. <a href="/cli/azure/install-azure-cli" target="_blank">Azure CLI</a> 2.0.80 以降をインストールします。それを任意のシェルから使用してコマンドを実行することで、Azure リソースのプロビジョニングと構成を行います。

ターミナル ウィンドウを開き、Python のバージョンが 3.6 以降であることを確認します。

# <a name="bash"></a>[Bash](#tab/bash)

```bash
python3 --version
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```cmd
py -3 --version
```

# <a name="cmd"></a>[Cmd](#tab/cmd)

```cmd
py -3 --version
```

---

Azure CLI のバージョンが 2.0.80 以降であることを確認します。

```azurecli
az --version
```

次に CLI から Azure にサインインします。

```azurecli
az login
```

このコマンドを実行すると、お客様の資格情報を収集するためにブラウザーが開かれます。 コマンドが終了すると、ご利用のサブスクリプションに関する情報を含んだ JSON 出力が表示されます。

サインイン後は、Azure CLI を使用して Azure コマンドを実行して、サブスクリプション内のリソースを操作することができます。

[問題がある場合は、お知らせください。](https://aka.ms/FlaskCLIQuickstartHelp)

## <a name="clone-the-sample"></a>サンプルを複製する

次のコマンドを使用してサンプル リポジトリを複製し、サンプル フォルダーに移動します (git をまだインストールしていない場合は、[git をインストール](https://git-scm.com/downloads)します)。

::: zone pivot="python-framework-flask"
```terminal
git clone https://github.com/Azure-Samples/python-docs-hello-world
```

次に、そのフォルダーに移動します。

```terminal
cd python-docs-hello-world
```
::: zone-end

::: zone pivot="python-framework-django"
```terminal
git clone https://github.com/Azure-Samples/python-docs-hello-django
```

次に、そのフォルダーに移動します。

```terminal
cd python-docs-hello-django
```
::: zone-end

このサンプルには、Azure App Service がアプリの起動時に認識するフレームワーク固有のコードが含まれています。 詳細については、「[コンテナーのスタートアップ プロセス](configure-language-python.md#container-startup-process)」を参照してください。

[問題がある場合は、お知らせください。](https://aka.ms/FlaskCLIQuickstartHelp)

## <a name="deploy-the-sample"></a>サンプルのデプロイ

`az webapp up` コマンドを使用して、ローカル フォルダー (*python-docs-hello-world*) にコードをデプロイします。

```azurecli
az webapp up --sku B1 --name <app-name>
```

- `az` コマンドが認識されない場合は、「[初期環境を設定する](#set-up-your-initial-environment)」の説明に従って Azure CLI がインストールされていることを確認してください。
- `webapp` コマンドが認識されない場合、それはご利用の Azure CLI のバージョンが 2.0.80 以上だからです。 そうでない場合は、最[新バージョンをインストール](/cli/azure/install-azure-cli)してください。
- `<app_name>` を Azure 全体で一意の名前で置き換えます ("*有効な文字は、`a-z`、`0-9`、および `-` です*")。 会社名とアプリ識別子を組み合わせて使用すると、適切なパターンになります。
- `--sku B1` 引数により、Basic 価格レベルで Web アプリが作成され、時間単位のわずかなコストが発生します。 より高速な Premium レベルを使用するには、この引数を省略します。
- 必要に応じて、引数 `--location <location-name>` を含めることができます。ここで、`<location_name>` は利用可能な Azure リージョンです。 [`az account list-locations`](/cli/azure/appservice#az-appservice-list-locations) コマンドを実行すると、お使いの Azure アカウントで使用可能なリージョンの一覧を取得できます。
- "Could not auto-detect the runtime stack of your app (アプリのランタイム スタックを自動検出できませんでした)" というエラーが表示された場合は、*requirements.txt* ファイルがある *python-docs-hello-world* フォルダー (Flask) または *python-docs-hello-django フォルダー (Django)* でコマンドを実行していることを確認してください。 (GitHub の [az webapp up を使用して自動検出の問題をトラブルシューティングする方法](https://github.com/Azure/app-service-linux-docs/blob/master/AzWebAppUP/runtime_detection.md)に関するページを参照してください。)

コマンドが完了するまでに数分かかる場合があります。 実行中には、リソース グループ、App Service プラン、およびホスティング アプリの作成、ログ記録の構成、ZIP デプロイの実行に関するメッセージが表示されます。 次に、"http://&lt;app-name&gt;.azurewebsites.net でアプリを起動することができます" という内容のメッセージが表示されます。これは、Azure 上のアプリの URL です。

![az webapp up コマンドの出力例](./media/quickstart-python/az-webapp-up-output.png)

[問題がある場合は、お知らせください。](https://aka.ms/FlaskCLIQuickstartHelp)

[!include [az webapp up command note](../../includes/app-service-web-az-webapp-up-note.md)]

## <a name="browse-to-the-app"></a>アプリの参照

URL `http://<app-name>.azurewebsites.net` を使って、お使いの Web ブラウザーでデプロイされたアプリケーションを参照します。 最初にアプリを起動するのにしばらく時間がかかります。

Python サンプル コードによって、App Service 内で、組み込みのイメージを使用して、Linux コンテナーが実行されています。

![サンプル Python アプリを Azure で実行する](./media/quickstart-python/run-hello-world-sample-python-app-in-browser.png)

**お疲れさまでした。** App Service に Python アプリをデプロイすることができました。

[問題がある場合は、お知らせください。](https://aka.ms/FlaskCLIQuickstartHelp)

## <a name="run-the-sample"></a>サンプルを実行する 

::: zone pivot="python-framework-flask"
1. *python-docs-hello world* フォルダーを開いていることを確認します。 

1. 仮想環境を作成し、依存関係をインストールします。

    [!include [virtual environment setup](../../includes/app-service-quickstart-python-venv.md)]

    "[Errno 2] そのようなファイルまたはディレクトリはありません: 'requirements.txt'。" と表示された場合は、*python-docs-hello-world* フォルダーを開いていることを確認してください。

1. 開発サーバーを実行します。

    ```terminal  
    flask run
    ```
    
    既定では、サーバーによって、アプリのエントリ モジュールが、サンプルで使用されている *app.py* 内にあると想定されています (別のモジュール名を使用する場合は、`FLASK_APP` 環境変数をその名前に設定します)。

1. Web ブラウザーを開き、`http://localhost:5000/` のサンプル アプリに移動します。 アプリに、**Hello World!** というメッセージが表示されます。

    ![サンプル Python アプリをローカルで実行する](./media/quickstart-python/run-hello-world-sample-python-app-in-browser-localhost.png)
    
1. ターミナル ウィンドウで **Ctrl** + **C** キーを押して、開発サーバーを終了します。
::: zone-end

::: zone pivot="python-framework-django"
1. *python-docs-hello-django* フォルダーを開いていることを確認します。 

1. 仮想環境を作成し、依存関係をインストールします。

    [!include [virtual environment setup](../../includes/app-service-quickstart-python-venv.md)]

    "[Errno 2] そのようなファイルまたはディレクトリはありません: 'requirements.txt'。" と表示された場合は、*python-docs-hello-django* フォルダーを開いていることを確認してください。
    
1. 開発サーバーを実行します。

    # <a name="bash"></a>[Bash](#tab/bash)

    ```bash
    python3 manage.py runserver
    ```

    # <a name="powershell"></a>[PowerShell](#tab/powershell)

    ```powershell
    py -3 manage.py runserver
    ```

    # <a name="cmd"></a>[Cmd](#tab/cmd)

    ```cmd
    py -3 manage.py runserver
    ```

    ---    

1. Web ブラウザーを開き、`http://localhost:8000/` のサンプル アプリに移動します。 アプリに、**Hello World!** というメッセージが表示されます。

    ![サンプル Python アプリをローカルで実行する](./media/quickstart-python/run-hello-world-sample-python-app-in-browser-localhost.png)
    
1. ターミナル ウィンドウで **Ctrl** + **C** キーを押して、開発サーバーを終了します。
::: zone-end

[問題がある場合は、お知らせください。](https://aka.ms/FlaskCLIQuickstartHelp)

## <a name="redeploy-updates"></a>更新の再デプロイ

このセクションでは、小さなコード変更を行ってから、コードを Azure に再デプロイします。 このコード変更には、次のセクションで使用するログ出力を生成するための `print` ステートメントが含まれます。

::: zone pivot="python-framework-flask"
エディターで *app.py* を開き、次のコードに一致するように `hello` 関数を更新します。 

```python
def hello():
    print("Handling request to home page.")
    return "Hello, Azure!"
```
::: zone-end
::: zone pivot="python-framework-django"
エディターで *hello/views.py* を開き、次のコードに一致するように `hello` 関数を更新します。

```python
def hello(request):
    print("Handling request to home page.")
    return HttpResponse("Hello, Azure!")
```
::: zone-end
    
変更を保存してから、もう一度 `az webapp up` コマンドを使用してアプリを再デプロイします。

```azurecli
az webapp up
```

このコマンドでは、 *.azure/config* ファイルにローカルでキャッシュされている値 (アプリ名、リソース グループ、App Service プランなど) を使用します。

デプロイが完了すると、`http://<app-name>.azurewebsites.net` が開かれたブラウザー ウィンドウに戻ります。 ページを最新の情報に更新すると、変更されたメッセージが表示されます。

![更新したサンプル Python アプリを Azure で実行する](./media/quickstart-python/run-updated-hello-world-sample-python-app-in-browser.png)

[問題がある場合は、お知らせください。](https://aka.ms/FlaskCLIQuickstartHelp)

> [!TIP]
> Visual Studio Code には、Python と Azure App Service 用の強力な拡張機能が用意されています。これを使うと、App Service に Python Web アプリをデプロイするプロセスが簡単になります。 詳細については、[Visual Studio Code から App Service への Python アプリのデプロイ](/azure/python/tutorial-deploy-app-service-on-linux-01)に関する記事をご覧ください。

## <a name="stream-logs"></a>ログのストリーミング

アプリ、およびそれを実行するコンテナー内から生成されたコンソール ログに、アクセスすることができます。 ログには、`print` ステートメントを使って生成されたすべての出力が含まれます。

ログをストリーミングするには、[az webapp log tail](/cli/azure/webapp/log#az_webapp_log_tail) コマンドを実行します。

```azurecli
az webapp log tail
```

また、`az webapp up` コマンドに `--logs` パラメーターを指定して、デプロイ時にログ ストリームを自動的に開くこともできます。

ブラウザーでアプリを最新の情報に更新して、コンソール ログを生成します。これには、アプリに対する HTTP 要求に関するメッセージが含まれています。 すぐに出力が表示されない場合は、30 秒後に再試行してください。

`https://<app-name>.scm.azurewebsites.net/api/logs/docker` で、ブラウザーからログ ファイルを検査することもできます。

ログ ストリーミングを任意のタイミングで停止するには、ターミナルで **Ctrl** + **C** キーを押します。

[問題がある場合は、お知らせください。](https://aka.ms/FlaskCLIQuickstartHelp)

## <a name="manage-the-azure-app"></a>Azure アプリの管理

<a href="https://portal.azure.com" target="_blank">Azure portal</a> に移動し、お客様が作成したアプリを管理します。 **[App Services]** を検索して選択します。

![Azure portal で App Services に移動する](./media/quickstart-python/navigate-to-app-services-in-the-azure-portal.png)

使用する Azure アプリの名前を選択します。

![Azure portal で App Services の Python アプリに移動する](./media/quickstart-python/navigate-to-app-in-app-services-in-the-azure-portal.png)

アプリを選択すると **[概要]** ページが開きます。ここでは、参照、停止、開始、再開、削除のような基本的な管理タスクを行うことができます。

![Azure portal の [概要] ページで Python アプリを管理する](./media/quickstart-python/manage-an-app-in-app-services-in-the-azure-portal.png)

App Service のメニューには、アプリを構成するためのさまざまなページが用意されています。

[問題がある場合は、お知らせください。](https://aka.ms/FlaskCLIQuickstartHelp)

## <a name="clean-up-resources"></a>リソースをクリーンアップする

前の手順では、リソース グループ内に Azure リソースを作成しました。 リソース グループには、お客様の場所に応じて "appsvc_rg_Linux_CentralUS" のような名前が付いています。 Web アプリを実行し続けると、継続的なコストが発生します (「[App Service の価格](https://azure.microsoft.com/pricing/details/app-service/linux/)」をご覧ください)。

今後これらのリソースが不要である場合は、次のコマンドを実行してリソース グループを削除します。

```azurecli
az group delete --no-wait
```

このコマンドでは、 *azure/config* ファイルにキャッシュされているリソース グループ名を使用します。

`--no-wait` 引数を使用すると、操作が完了する前にコマンドから戻ることができます。

[問題がある場合は、お知らせください。](https://aka.ms/FlaskCLIQuickstartHelp)

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [チュートリアル:PostgreSQL を使った Python (Django) Web アプリ](tutorial-python-postgresql-app.md)

> [!div class="nextstepaction"]
> [Python アプリの構成](configure-language-python.md)

> [!div class="nextstepaction"]
> [Python Web アプリにユーザーのサインインを追加する](../active-directory/develop/quickstart-v2-python-webapp.md)

> [!div class="nextstepaction"]
> [チュートリアル:Python アプリをカスタム コンテナーで実行する](tutorial-custom-container.md)
