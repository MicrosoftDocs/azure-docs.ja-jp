---
title: クイック スタート:PHP Web アプリの作成
description: Azure App Service に、初めての PHP の Hello World を数分でデプロイします。 デプロイには、App Service への数あるデプロイ方法の 1 つである Git を使用します。
ms.assetid: 6feac128-c728-4491-8b79-962da9a40788
ms.topic: quickstart
ms.date: 08/01/2020
zone_pivot_groups: app-service-platform-windows-linux
ms.openlocfilehash: f6876d0aef0d3d87e038b623c395f8368a14e90c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "97561853"
---
# <a name="create-a-php-web-app-in-azure-app-service"></a>Azure App Service に PHP Web アプリを作成する

::: zone pivot="platform-windows"  
[Azure App Service](overview.md) は、非常にスケーラブルな、自己適用型の Web ホスティング サービスを提供します。  このクイックスタート チュートリアルでは、Azure App Service on Windows に PHP アプリをデプロイする方法を説明します。
::: zone-end  

::: zone pivot="platform-linux"
[Azure App Service](overview.md) は、非常にスケーラブルな、自己適用型の Web ホスティング サービスを提供します。  このクイックスタート チュートリアルでは、Azure App Service on Linux に PHP アプリをデプロイする方法を説明します。
::: zone-end  

[Azure CLI](/cli/azure/get-started-with-azure-cli) を使って Cloud Shell で Web アプリを作成し、Git を使用して Web アプリに PHP のサンプル コードをデプロイします。

![Azure で実行されるサンプル アプリ](media/quickstart-php/hello-world-in-browser.png)

以下の手順は、Mac、Windows、または Linux コンピューターを使って実行できます。 前提条件のインストールを終えてから、以降の手順を完了するまでに約 5 分かかります。

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>前提条件

このクイック スタートを完了するには、以下が必要です。

* <a href="https://git-scm.com/" target="_blank">Git をインストールする</a>
* <a href="https://php.net/manual/install.php" target="_blank">PHP をインストールする</a>

## <a name="download-the-sample-locally"></a>サンプルをローカルでダウンロードする

ターミナル ウィンドウで次のコマンドを実行します。 これにより、ローカル コンピューターにサンプル アプリケーションが複製され、サンプル コードを含むディレクトリに移動します。 

```bash
git clone https://github.com/Azure-Samples/php-docs-hello-world
cd php-docs-hello-world
```

## <a name="run-the-app-locally"></a>アプリをローカルで実行する

アプリケーションをローカルで実行すると、アプリケーションを Azure にデプロイするとどう表示されるかを把握できます。 ターミナル ウィンドウを開き、`php` コマンドを使用して組み込みの PHP Web サーバーを起動します。

```bash
php -S localhost:8080
```

Web ブラウザーを開き、`http://localhost:8080` のサンプル アプリに移動します。

サンプル アプリケーションから "**Hello World!** " というメッセージがページに表示されます。

![ローカルで実行されるサンプル アプリ](media/quickstart-php/localhost-hello-world-in-browser.png)

ターミナル ウィンドウで **Ctrl + C** キーを押して、Web サーバーを終了します。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

[!INCLUDE [Configure deployment user](../../includes/configure-deployment-user.md)]

::: zone pivot="platform-windows"  
[!INCLUDE [Create resource group](../../includes/app-service-web-create-resource-group.md)]
::: zone-end  

::: zone pivot="platform-linux"
[!INCLUDE [Create resource group](../../includes/app-service-web-create-resource-group-linux.md)]
::: zone-end

[!INCLUDE [Create app service plan](../../includes/app-service-web-create-app-service-plan.md)]

## <a name="create-a-web-app"></a>Web アプリを作成する

Cloud Shell で [`az webapp create`](/cli/azure/webapp#az-webapp-create) コマンドを使用して、`myAppServicePlan` App Service プランに Web アプリを作成します。 

次の例では、`<app-name>` をグローバルに一意のアプリ名に置き換えてください (有効な文字は `a-z`、`0-9`、`-`)。 ランタイムは `PHP|7.4` に設定されています。 サポートされているすべてのランタイムを確認するには、[`az webapp list-runtimes`](/cli/azure/webapp#az-webapp-list-runtimes) を実行します。 

```azurecli-interactive
# Bash
az webapp create --resource-group myResourceGroup --plan myAppServicePlan --name <app-name> --runtime "PHP|7.4" --deployment-local-git
# PowerShell
az --% webapp create --resource-group myResourceGroup --plan myAppServicePlan --name <app-name> --runtime "PHP|7.4" --deployment-local-git
```

> [!NOTE]
> 解析停止記号 `(--%)` は、PowerShell のコマンドや式として入力が解釈されないようにするための命令で、PowerShell 3.0 で導入されました。
>

Web アプリが作成されると、Azure CLI によって次の例のような出力が表示されます。

<pre>
Local git is configured with url of 'https://&lt;username&gt;@&lt;app-name&gt;.scm.azurewebsites.net/&lt;app-name&gt;.git'
{
  "availabilityState": "Normal",
  "clientAffinityEnabled": true,
  "clientCertEnabled": false,
  "cloningInfo": null,
  "containerSize": 0,
  "dailyMemoryTimeQuota": 0,
  "defaultHostName": "&lt;app-name&gt;.azurewebsites.net",
  "enabled": true,
  &lt; JSON data removed for brevity. &gt;
}
</pre>

Git デプロイが有効な、空の新しい Web アプリが作成されました。

> [!NOTE]
> Git リモートの URL は `deploymentLocalGitUrl` プロパティに `https://<username>@<app-name>.scm.azurewebsites.net/<app-name>.git` 形式で出力されます。 この URL は後で必要になるので保存しておいてください。
>

新しく作成された Web アプリに移動します。 _&lt;app-name>_ は、前の手順で作成したアプリの一意の名前で置き換えます。

```bash
http://<app-name>.azurewebsites.net
```

新しい Web アプリは次のようになります。

![空の Web アプリ ページ](media/quickstart-php/app-service-web-service-created.png)

[!INCLUDE [Push to Azure](../../includes/app-service-web-git-push-to-azure.md)] 

<pre>
Counting objects: 2, done.
Delta compression using up to 4 threads.
Compressing objects: 100% (2/2), done.
Writing objects: 100% (2/2), 352 bytes | 0 bytes/s, done.
Total 2 (delta 1), reused 0 (delta 0)
remote: Updating branch 'main'.
remote: Updating submodules.
remote: Preparing deployment for commit id '25f18051e9'.
remote: Generating deployment script.
remote: Running deployment command...
remote: Handling Basic Web Site deployment.
remote: Kudu sync from: '/home/site/repository' to: '/home/site/wwwroot'
remote: Copying file: '.gitignore'
remote: Copying file: 'LICENSE'
remote: Copying file: 'README.md'
remote: Copying file: 'index.php'
remote: Ignoring: .git
remote: Finished successfully.
remote: Running post deployment command(s)...
remote: Deployment successful.
To https://&lt;app-name&gt;.scm.azurewebsites.net/&lt;app-name&gt;.git
   cc39b1e..25f1805  main -> main
</pre>

## <a name="browse-to-the-app"></a>アプリの参照

Web ブラウザーを使用して、デプロイされたアプリケーションを参照します。

```
http://<app-name>.azurewebsites.net
```

PHP のサンプル コードは、Azure App Service の Web アプリで実行されています。

![Azure で実行されるサンプル アプリ](media/quickstart-php/hello-world-in-browser.png)

**お疲れさまでした。** App Service に初めての PHP アプリをデプロイしました。

## <a name="update-locally-and-redeploy-the-code"></a>コードをローカルで更新して再デプロイする

ローカルのテキスト エディターを使用して、PHP アプリ内の `index.php` ファイルを開き、`echo` に続く文字列の中のテキストを少し変更します。

```php
echo "Hello Azure!";
```

ローカル ターミナル ウィンドウで、変更を Git にコミットし、コード変更を Azure にプッシュします。

```bash
git commit -am "updated output"
git push azure main
```

デプロイが完了したら、「**アプリの参照**」の手順で開いたブラウザー ウィンドウに戻り、ページを更新します。

![Azure で実行される更新済みのサンプル アプリ](media/quickstart-php/hello-azure-in-browser.png)

## <a name="manage-your-new-azure-app"></a>新しい Azure アプリの管理

1. <a href="https://portal.azure.com" target="_blank">Azure Portal</a> に移動し、作成した Web アプリを管理します。 **[App Services]** を検索して選択します。

    ![App Services の検索、Azure portal、PHP Web アプリの作成](media/quickstart-php/navigate-to-app-services-in-the-azure-portal.png)

2. 使用する Azure アプリの名前を選択します。

    ![Azure アプリへのポータル ナビゲーション](./media/quickstart-php/php-docs-hello-world-app-service-list.png)

    Web アプリの **[概要]** ページが表示されます。 ここでは、**参照**、**停止**、**再開**、**削除** のような基本的な管理タスクを行うことができます。

    ![Azure Portal の [App Service] ページ](media/quickstart-php/php-docs-hello-world-app-service-detail.png)

    Web アプリ メニューに、アプリを構成するためのさまざまなオプションが示されます。 

[!INCLUDE [cli-samples-clean-up](../../includes/cli-samples-clean-up.md)]

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [PHP と MySQL](tutorial-php-mysql-app.md)

> [!div class="nextstepaction"]
> [PHP アプリの構成](configure-language-php.md)
