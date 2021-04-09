---
title: 'クイック スタート: 静的 HTML Web アプリを作成する'
description: Azure App Service に、初めての HTML の Hello World を数分でデプロイします。 デプロイには、App Service への数あるデプロイ方法の 1 つである Git を使用します。
author: msangapu-msft
ms.assetid: 60495cc5-6963-4bf0-8174-52786d226c26
ms.topic: quickstart
ms.date: 08/23/2019
ms.author: msangapu
ms.custom: mvc, cli-validate, seodec18, devx-track-azurecli
adobe-target: true
adobe-target-activity: DocsExp–386541–A/B–Enhanced-Readability-Quickstarts–2.19.2021
adobe-target-experience: Experience B
adobe-target-content: ./quickstart-html-uiex
ms.openlocfilehash: 1a179f30b4004eba105780ee73e25394e6a569a6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "101708987"
---
# <a name="create-a-static-html-web-app-in-azure"></a>Azure で静的 HTML Web アプリを作成する

[Azure App Service](overview.md) は、非常にスケーラブルな、自己適用型の Web ホスティング サービスを提供します。 このクイック スタートでは、基本的な HTML+CSS サイトを Azure App Service にデプロイする方法を示します。 このクイック スタートは [Cloud Shell](../cloud-shell/overview.md) で行いますが、これらのコマンドは [Azure CLI](/cli/azure/install-azure-cli) を使用してローカルで実行することもできます。

![サンプル アプリのホーム ページ](media/quickstart-html/hello-world-in-browser-az.png)

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="download-the-sample"></a>サンプルのダウンロード

Cloud Shell で、クイックスタートのディレクトリを作成し、それに変更します。

```bash
mkdir quickstart

cd $HOME/quickstart
```

次に、以下のコマンドを実行して、サンプル アプリのリポジトリをクイックスタートのディレクトリに複製します。

```bash
git clone https://github.com/Azure-Samples/html-docs-hello-world.git
```

## <a name="create-a-web-app"></a>Web アプリを作成する

サンプル コードが含まれているディレクトリに移動し、`az webapp up` コマンドを実行します。 次の例で、<app_name> を一意のアプリ名に置き換えます。 静的コンテンツは `--html` フラグによって示されます。

```bash
cd html-docs-hello-world

az webapp up --location westeurope --name <app_name> --html
```

`az webapp up` コマンドは、次の処理を実行します。

- 既定のリソース グループを作成する。

- 既定の App Service プランを作成する。

- 指定された名前でアプリを作成する。

- 現在の作業ディレクトリから Web アプリにファイルを [zip してデプロイ](./deploy-zip.md)する。

このコマンドの実行には、数分かかる場合があります。 実行中、次の例のような情報が表示されます。

```output
{
  "app_url": "https://&lt;app_name&gt;.azurewebsites.net",
  "location": "westeurope",
  "name": "&lt;app_name&gt;",
  "os": "Windows",
  "resourcegroup": "appsvc_rg_Windows_westeurope",
  "serverfarm": "appsvc_asp_Windows_westeurope",
  "sku": "FREE",
  "src_path": "/home/&lt;username&gt;/quickstart/html-docs-hello-world ",
  &lt; JSON data removed for brevity. &gt;
}
```

`resourceGroup` の値を書き留めておきます。 これは、「[リソースのクリーンアップ](#clean-up-resources)」セクションで必要になります。

## <a name="browse-to-the-app"></a>アプリの参照

ブラウザーで、アプリの URL (`http://<app_name>.azurewebsites.net`) に移動します。

ページは、Azure App Service Web アプリとして実行されています。

![サンプル アプリのホーム ページ](media/quickstart-html/hello-world-in-browser-az.png)

**お疲れさまでした。** App Service に初めての HTML アプリをデプロイしました。

## <a name="update-and-redeploy-the-app"></a>アプリを更新して再デプロイする

Cloud Shell で、「`nano index.html`」と入力して nano テキスト エディターを開きます。 次に示すように、`<h1>` 見出しタグで "Azure App Service - Sample Static HTML Site" から "Azure App Service" に変更します。

![Nano index.html](media/quickstart-html/nano-index-html.png)

変更内容を保存し、nano を終了します。 コマンド `^O` を使用して保存し、`^X` を使用して終了します。

同じ `az webapp up` コマンドを使用して、アプリを再デプロイします。

```bash
az webapp up --location westeurope --name <app_name> --html
```

デプロイが完了したら、「**アプリの参照**」の手順で開いたブラウザー ウィンドウに戻り、ページを更新します。

![更新されたサンプル アプリのホーム ページ](media/quickstart-html/hello-azure-in-browser-az.png)

## <a name="manage-your-new-azure-app"></a>新しい Azure アプリの管理

作成した Web アプリを管理するには、[Azure portal](https://portal.azure.com) で **[App Services]** を検索して選択します。 

![Azure portal で [App Services] を選択する](./media/quickstart-html/portal0.png)

**[App Services]** ページで、Azure アプリの名前を選択します。

![Azure アプリへのポータル ナビゲーション](./media/quickstart-html/portal1.png)

Web アプリの [概要] ページを確認します。 ここでは、参照、停止、開始、再開、削除のような基本的な管理タスクを行うことができます。

![Azure Portal の App Service ブレード](./media/quickstart-html/portal2.png)

左側のメニューは、アプリを構成するためのさまざまなページを示しています。

## <a name="clean-up-resources"></a>リソースをクリーンアップする

前の手順では、リソース グループ内に Azure リソースを作成しました。 これらのリソースが将来必要になると想定していない場合、Cloud Shell で次のコマンドを実行して、リソース グループを削除します。 「[Web アプリを作成する](#create-a-web-app)」の手順で、リソース グループ名が自動的に生成されたことを思い出してください。

```bash
az group delete --name appsvc_rg_Windows_westeurope
```

このコマンドの実行には、少し時間がかかる場合があります。

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [カスタム ドメインをマップする](app-service-web-tutorial-custom-domain.md)
