---
title: 'クイック スタート: 静的 HTML Web アプリを作成する'
description: Azure App Service に、初めての HTML の Hello World を数分でデプロイします。 デプロイには、App Service への数あるデプロイ方法の 1 つである Git を使用します。
author: msangapu-msft
ms.assetid: 60495cc5-6963-4bf0-8174-52786d226c26
ms.topic: quickstart
ms.date: 08/23/2019
ms.author: msangapu
ms.custom: mvc, cli-validate, seodec18, devx-track-azurecli
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 538d414ce606b944fcea7adbb1c817386e13090e
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102178577"
---
# <a name="create-a-static-html-web-app-in-azure"></a>Azure で静的 HTML Web アプリを作成する

このクイックスタートでは、基本的な HTML+CSS サイトを Azure App Service にデプロイする方法を示します。 <abbr title="Web アプリケーション、REST API、およびモバイル バックエンド アプリケーションをホストするための HTTP ベースのサービス。">Azure App Service</abbr>. このクイック スタートは [Cloud Shell](../cloud-shell/overview.md) で行いますが、これらのコマンドは [Azure CLI](/cli/azure/install-azure-cli) を使用してローカルで実行することもできます。

## <a name="1-prepare-your-environment"></a>1.環境を準備する

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[Cloud Shell](../cloud-shell/overview.md) でクイックスタートのディレクトリを作成し、そのディレクトリに移動します。

```bash
mkdir quickstart

cd $HOME/quickstart
```

次に、以下のコマンドを実行して、サンプル アプリのリポジトリをクイックスタートのディレクトリに複製します。

```bash
git clone https://github.com/Azure-Samples/html-docs-hello-world.git
```
<hr/>

## <a name="2-create-a-web-app"></a>2. Web アプリを作成する

サンプル コードが含まれているディレクトリに移動し、`az webapp up` コマンドを実行します。 `<app-name>` は、グローバルに一意の名前に **置き換え** てください。

```bash
cd html-docs-hello-world

az webapp up --location westeurope --name <app_name> --html
```

<details>
<summary>トラブルシューティング</summary>
<ul>
<li><code>az</code> コマンドが認識されない場合は、「<a href="#1-prepare-your-environment">環境を準備する</a>」の説明に従って Azure CLI がインストールされていることを確認してください。</li>
<li><code>&lt;app-name&gt;</code> を Azure 全体で一意の名前で置き換えます ("<em>有効な文字は、<code>a-z</code>、<code>0-9</code>、および <code>-</code> です</em>")。 会社名とアプリ識別子を組み合わせて使用すると、適切なパターンになります。</li>
<li><code>--sku F1</code> 引数を使用すると、Free 価格レベルで Web アプリが作成されます。 この引数を省略するとより高速な Premium レベルが使用されるため、時間単位のコストが発生します。</li>
<li><code>--html</code> 引数を指定することで、フォルダーの内容がすべて静的コンテンツとして扱われ、ビルドの自動化は無効になります。</li>
<li>必要に応じて、引数 <code>--location &lt;location-name&gt;</code> を含めることができます。ここで、<code>&lt;location-name&gt;</code> は利用可能な Azure リージョンです。 <a href="/cli/azure/appservice#az-appservice-list-locations"><code>az account list-locations</code></a> コマンドを実行すると、お使いの Azure アカウントで使用可能なリージョンの一覧を取得できます。</li>
</ul>
</details>

コマンドが完了するまでに数分かかる場合があります。 

<details>
<summary><code>az webapp up</code> の処理の内容</summary>
<p><code>az webapp up</code> コマンドは、次の処理を実行します。</p>
<ul>
<li>既定のリソース グループを作成する。</li>
<li>既定の App Service プランを作成する。</li>
<li>指定された名前で <a href="/cli/azure/webapp#az-webapp-create">App Service アプリを作成</a>する。</li>
<li>現在の作業ディレクトリからアプリにファイルを <a href="/azure/app-service/deploy-zip">zip してデプロイ</a>する。</li>
<li>実行中、リソースの作成、ログ記録、ZIP デプロイに関するメッセージが表示されます。</li>
</ul>

完了すると、次の例のような情報が表示されます。

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

</details>

`resourceGroup` の値は、後で[リソースをクリーンアップ](#6-clean-up-resources)する際に必要になります。

<hr/>

## <a name="3-browse-to-the-app"></a>3. アプリの参照

ブラウザーで、アプリの URL (`http://<app_name>.azurewebsites.net`) に移動します。

ページは、Azure App Service Web アプリとして実行されています。

![サンプル アプリのホーム ページ](media/quickstart-html/hello-world-in-browser-az.png)

<hr/>

## <a name="4-update-and-redeploy-the-app"></a>4.アプリを更新して再デプロイする

Cloud Shell で、「`nano index.html`」と **入力** して nano テキスト エディターを開きます。 

`<h1>` 見出しタグで "Azure App Service - Sample Static HTML Site" から "Azure App Service" に変更します。

![Nano index.html](media/quickstart-html/nano-index-html.png)

`^O` コマンドを使用して変更内容を **保存** します。

`^X` コマンドを使用して nano を **終了** します。

`az webapp up` コマンドを使用してアプリを再デプロイします。

```bash
az webapp up --html
```

「**アプリの参照**」の手順で開いたブラウザー ウィンドウに戻ります。

ページを **最新の情報に更新** します。

![更新されたサンプル アプリのホーム ページ](media/quickstart-html/hello-azure-in-browser-az.png)

<hr/>

## <a name="5-manage-your-new-azure-app"></a>5.新しい Azure アプリの管理

[Azure portal](https://portal.azure.com) に **移動** します 

**[App Services]** を **検索** して **選択** します。

![Azure portal で [App Services] を選択する](./media/quickstart-html/portal0.png)

使用する Azure アプリの名前を **選択** します。

![Azure アプリへのポータル ナビゲーション](./media/quickstart-html/portal1.png)

Web アプリの [概要] ページを確認します。 ここでは、参照、停止、開始、再開、削除のような基本的な管理タスクを行うことができます。

![Azure Portal の App Service ブレード](./media/quickstart-html/portal2.png)

左側のメニューは、アプリを構成するためのさまざまなページを示しています。

<hr/>

## <a name="6-clean-up-resources"></a>6.リソースをクリーンアップする

前の手順では、リソース グループ内に Azure リソースを作成しました。 これらのリソースが将来必要になると想定していない場合、Cloud Shell で次のコマンドを実行して、リソース グループを削除します。 「[Web アプリを作成する](#2-create-a-web-app)」の手順で、リソース グループ名が自動的に生成されたことを思い出してください。

```bash
az group delete --name appsvc_rg_Windows_westeurope
```

このコマンドの実行には、少し時間がかかる場合があります。

<hr/>

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [カスタム ドメインをマップする](app-service-web-tutorial-custom-domain-uiex.md)
