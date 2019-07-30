---
title: Linux での Python アプリの作成 - Azure App Service | Microsoft Docs
description: Azure App Service on Linux で、初めての Python の Hello World アプリを数分でデプロイします。
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
ms.date: 04/29/2019
ms.author: cephalin
ms.openlocfilehash: 557b917f53064f0e6e9ecd61e2d230a6a4fd2ad6
ms.sourcegitcommit: 470041c681719df2d4ee9b81c9be6104befffcea
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/12/2019
ms.locfileid: "67853676"
---
# <a name="create-a-python-app-in-azure-app-service-on-linux"></a>Azure App Service on Linux で Python アプリを作成する

このクイック スタートでは、高度にスケーラブルな自己適用型の Web ホスティング サービスである [App Service on Linux](app-service-linux-intro.md) にシンプルな Python アプリをデプロイします。 インタラクティブなブラウザーベースの Azure Cloud Shell を通じて Azure のコマンド ライン インターフェイス ([Azure CLI](/cli/azure/install-azure-cli)) を使用するので、以下の手順は、Mac、Linux、Windows のどのコンピューターからでも使用できます。

![Azure で実行されるサンプル アプリ](media/quickstart-python/hello-world-in-browser.png)

## <a name="prerequisites"></a>前提条件

このクイック スタートを完了するには、以下が必要です。

* <a href="https://www.python.org/downloads/" target="_blank">Python 3.7 をインストールする</a>
* <a href="https://git-scm.com/" target="_blank">Git をインストールする</a>
* Azure サブスクリプション。 まだお持ちでない場合は、開始する前に[無料アカウント](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)を作成してください。

## <a name="download-the-sample-locally"></a>サンプルをローカルでダウンロードする

ターミナル ウィンドウで、次のコマンドを実行して、ローカル コンピューターにサンプル アプリケーションを複製し、サンプル コードのあるディレクトリに移動します。

```bash
git clone https://github.com/Azure-Samples/python-docs-hello-world
cd python-docs-hello-world
```

リポジトリには、*application.py* が含まれています。これによって、リポジトリに Flask アプリが含まれていることが App Service に知らされます。 詳細については、「[コンテナーのスタートアップ プロセスとカスタマイズ](how-to-configure-python.md)」を参照してください。

## <a name="run-the-app-locally"></a>アプリをローカルで実行する

アプリケーションをローカルで実行すると、アプリケーションを Azure にデプロイするとどう表示されるかを把握できます。 ターミナル ウィンドウを開き、次のコマンドを使用して必要な依存関係をインストールし、組み込みの開発サーバーを起動します。 

```bash
# In Bash
python3 -m venv venv
source venv/bin/activate
pip install -r requirements.txt
FLASK_APP=application.py flask run

# In PowerShell
py -3 -m venv env
env\scripts\activate
pip install -r requirements.txt
Set-Item Env:FLASK_APP ".\application.py"
flask run
```

Web ブラウザーを開き、`http://localhost:5000/` のサンプル アプリに移動します。

サンプル アプリケーションから "**Hello World!** " というメッセージがページに表示されます。

![ローカルで実行されるサンプル アプリ](media/quickstart-python/hello-world-in-browser.png)

ターミナル ウィンドウで **Ctrl + C** キーを押して、Web サーバーを終了します。

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="download-the-sample"></a>サンプルのダウンロード

Cloud Shell で、クイックスタートのディレクトリを作成し、それに変更します。

```bash
mkdir quickstart

cd quickstart
```

次に、以下のコマンドを実行して、サンプル アプリのリポジトリをクイックスタートのディレクトリに複製します。

```bash
git clone https://github.com/Azure-Samples/python-docs-hello-world
```

実行中、次の例のような情報が表示されます。

```bash
Cloning into 'python-docs-hello-world'...
remote: Enumerating objects: 43, done.
remote: Total 43 (delta 0), reused 0 (delta 0), pack-reused 43
Unpacking objects: 100% (43/43), done.
Checking connectivity... done.
```

## <a name="create-a-web-app"></a>Web アプリを作成する

サンプル コードが含まれているディレクトリに移動し、`az webapp up` コマンドを実行します。

次の例では、`<app-name>` をグローバルに一意のアプリ名に置き換えてください (有効な文字は `a-z`、`0-9`、`-`)。

```bash
cd python-docs-hello-world

az webapp up -n <app-name>
```

このコマンドの実行には、数分かかる場合があります。 実行中、次の例のような情報が表示されます。

```json
The behavior of this command has been altered by the following extension: webapp
Creating Resource group 'appsvc_rg_Linux_CentralUS' ...
Resource group creation complete
Creating App service plan 'appsvc_asp_Linux_CentralUS' ...
App service plan creation complete
Creating app '<app-name>' ....
Webapp creation complete
Creating zip with contents of dir /home/username/quickstart/python-docs-hello-world ...
Preparing to deploy contents to app.
All done.
{
  "app_url": "https:/<app-name>.azurewebsites.net",
  "location": "Central US",
  "name": "<app-name>",
  "os": "Linux",
  "resourcegroup": "appsvc_rg_Linux_CentralUS ",
  "serverfarm": "appsvc_asp_Linux_CentralUS",
  "sku": "BASIC",
  "src_path": "/home/username/quickstart/python-docs-hello-world ",
  "version_detected": "-",
  "version_to_create": "python|3.7"
}
```

[!INCLUDE [AZ Webapp Up Note](../../../includes/app-service-web-az-webapp-up-note.md)]

## <a name="browse-to-the-app"></a>アプリの参照

Web ブラウザーを使用して、デプロイされたアプリケーションを参照します。

```bash
http://<app-name>.azurewebsites.net
```

App Service on Linux で組み込みのイメージを使用して Python サンプル コードが実行されています。

![Azure で実行されるサンプル アプリ](media/quickstart-python/hello-world-in-browser.png)

**お疲れさまでした。** App Service on Linux に初めての Python アプリをデプロイしました。

## <a name="update-locally-and-redeploy-the-code"></a>コードをローカルで更新して再デプロイする

Cloud Shell で、「`code application.py`」と入力して Cloud Shell エディターを開きます。

![Code application.py](media/quickstart-python/code-applicationpy.png)

 `return` を呼び出すテキストに小さな変更を加えます。

```python
return "Hello Azure!"
```

変更内容を保存し、エディターを終了します。 コマンド `^S` を使用して保存し、`^Q` を使用して終了します。

[`az webapp up`](/cli/azure/webapp#az-webapp-up) コマンドを使用してアプリを再デプロイします。 `<app-name>` は実際のアプリの名前に置き換え、`<location-name>` には、[`az account list-locations`](/cli/azure/appservice?view=azure-cli-latest.md#az-appservice-list-locations) コマンドから表示されるいずれかの値を使用して実際の場所を指定してください。

```bash
az webapp up -n <app-name> -l <location-name>
```

デプロイが完了したら、「**アプリの参照**」の手順で開いたブラウザー ウィンドウに戻り、ページを更新します。

![Azure で実行される更新済みのサンプル アプリ](media/quickstart-python/hello-azure-in-browser.png)

## <a name="manage-your-new-azure-app"></a>新しい Azure アプリの管理

<a href="https://portal.azure.com" target="_blank">Azure portal</a> に移動し、お客様が作成したアプリを管理します。

左側のメニューで **[App Services]** をクリックしてから、お客様の Azure アプリの名前をクリックします。

![Azure アプリへのポータル ナビゲーション](./media/quickstart-python/app-service-list.png)

お客様のアプリの [概要] ページを確認します。 ここでは、参照、停止、開始、再開、削除のような基本的な管理タスクを行うことができます。

![Azure Portal の [App Service] ページ](media/quickstart-python/app-service-detail.png)

左側のメニューは、アプリを構成するためのさまざまなページを示しています。 

[!INCLUDE [cli-samples-clean-up](../../../includes/cli-samples-clean-up.md)]

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [チュートリアル:PostgreSQL を使った Python アプリ](tutorial-python-postgresql-app.md)

> [!div class="nextstepaction"]
> [Python アプリの構成](how-to-configure-python.md)

> [!div class="nextstepaction"]
> [チュートリアル:Python アプリをカスタム コンテナーで実行する](tutorial-custom-docker-image.md)
