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
ms.date: 03/28/2019
ms.author: cephalin
ms.custom: seodec18
ms.openlocfilehash: ea247907aebc241fb8f1b266ad55bc2fc983607f
ms.sourcegitcommit: 470041c681719df2d4ee9b81c9be6104befffcea
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/12/2019
ms.locfileid: "67853982"
---
# <a name="create-a-python-app-in-azure-app-service-on-linux-preview"></a>Azure App Service on Linux で Python アプリを作成する (プレビュー)

[App Service on Linux](app-service-linux-intro.md) は、Linux オペレーティング システムを使用する、高度にスケーラブルな自己適用型の Web ホスティング サービスを提供します。 このクイック スタートでは、[Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview) を使用して、App Service on Linux の組み込み Python イメージ (プレビュー) の上に Python アプリをデプロイする方法を示します。

この記事の手順は、Mac、Windows、または Linux コンピューターを使って実行できます。

![Azure で実行されるサンプル アプリ](media/quickstart-python/hello-world-in-browser.png)

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

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

次の例では、 *\<app_name>* をグローバルに一意のアプリ名に置き換えてください (有効な文字は `a-z`、`0-9`、`-`)。

```bash
cd python-docs-hello-world

az webapp up -n <app_name>
```

このコマンドの実行には、数分かかる場合があります。 実行中、次の例のような情報が表示されます。

```json
The behavior of this command has been altered by the following extension: webapp
Creating Resource group 'appsvc_rg_Linux_CentralUS' ...
Resource group creation complete
Creating App service plan 'appsvc_asp_Linux_CentralUS' ...
App service plan creation complete
Creating app '<app_name>' ....
Webapp creation complete
Creating zip with contents of dir /home/username/quickstart/python-docs-hello-world ...
Preparing to deploy contents to app.
All done.
{
  "app_url": "https:/<app_name>.azurewebsites.net",
  "location": "Central US",
  "name": "<app_name>",
  "os": "Linux",
  "resourcegroup": "appsvc_rg_Linux_CentralUS ",
  "serverfarm": "appsvc_asp_Linux_CentralUS",
  "sku": "BASIC",
  "src_path": "/home/username/quickstart/python-docs-hello-world ",
  "version_detected": "-",
  "version_to_create": "python|3.7"
}
```

`az webapp up` コマンドは、次の処理を実行します。

- 既定のリソース グループを作成する。

- 既定の App Service プランを作成する。

- 指定された名前でアプリを作成する。

- 現在の作業ディレクトリからアプリにファイルを [zip してデプロイ](https://docs.microsoft.com/azure/app-service/deploy-zip)する。

## <a name="browse-to-the-app"></a>アプリの参照

Web ブラウザーを使用して、デプロイされたアプリケーションを参照します。

```bash
http://<app_name>.azurewebsites.net
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

これでアプリを再デプロイします。 `<app_name>` をお客様のアプリに置き換えます。

```bash
az webapp up -n <app_name>
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

App Service on Linux の組み込み Python イメージは現在プレビュー段階であり、アプリを開始するために使用するコマンドをカスタマイズすることができます。 代わりにカスタム コンテナーを使用して運用環境向け Python アプリを作成することもできます。

> [!div class="nextstepaction"]
> [チュートリアル:PostgreSQL を使った Python アプリ](tutorial-python-postgresql-app.md)

> [!div class="nextstepaction"]
> [Python アプリの構成](how-to-configure-python.md)

> [!div class="nextstepaction"]
> [チュートリアル:プライベート コンテナー リポジトリからデプロイする](tutorial-custom-docker-image.md)
