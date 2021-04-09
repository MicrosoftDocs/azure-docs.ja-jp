---
title: ローカル Git リポジトリからデプロイする
description: Azure App Service へのローカル Git デプロイを有効にする方法を説明します。 ローカル コンピューターからコードをデプロイする最も簡単な方法の 1 つです。
ms.assetid: ac50a623-c4b8-4dfd-96b2-a09420770063
ms.topic: article
ms.date: 02/16/2021
ms.reviewer: dariac
ms.custom: seodec18, devx-track-azurecli
ms.openlocfilehash: 5dd6183bf88c167adb2f084c319cd90b94351dfb
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "100560489"
---
# <a name="local-git-deployment-to-azure-app-service"></a>Azure App Service へのローカル Git デプロイ

このハウツー ガイドでは、ローカル コンピューター上の Git リポジトリから [Azure App Service](overview.md) にアプリをデプロイする方法を説明します。

## <a name="prerequisites"></a>前提条件

このハウツー ガイドの手順に従うには:

- [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]
  
- [Git をインストールします](https://www.git-scm.com/downloads)。

- デプロイするコードを含むローカル Git リポジトリを用意します。 サンプル リポジトリをダウンロードするには、ご利用のローカル ターミナル ウィンドウで次のコマンドを実行します。
  
  ```bash
  git clone https://github.com/Azure-Samples/nodejs-docs-hello-world.git
  ```

[!INCLUDE [Prepare repository](../../includes/app-service-deploy-prepare-repo.md)]

## <a name="configure-a-deployment-user"></a>デプロイ ユーザーを構成する

「[Azure App Service のデプロイ資格情報の構成](deploy-configure-credentials.md)」を参照してください。 ユーザースコープの資格情報またはアプリケーションスコープの資格情報のいずれかを使用できます。

## <a name="create-a-git-enabled-app"></a>Git 対応アプリを作成する

App Service アプリを既に所有していて、そのためのローカル Git デプロイを構成する場合は、代わりに「[既存のアプリを構成する](#configure-an-existing-app)」を参照してください。

# <a name="azure-cli"></a>[Azure CLI](#tab/cli)

`--deployment-local-git` オプションを指定して [`az webapp create`](/cli/azure/webapp#az_webapp_create) を実行します。 次に例を示します。

```azurecli-interactive
az webapp create --resource-group <group-name> --plan <plan-name> --name <app-name> --runtime "<runtime-flag>" --deployment-local-git
```

出力には、`https://<deployment-username>@<app-name>.scm.azurewebsites.net/<app-name>.git` のような URL が含まれています。 次の手順で、この URL を使用して、ご利用になるアプリをデプロイします。

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/powershell)

自分の Git リポジトリのルートから [New-AzWebApp](/powershell/module/az.websites/new-azwebapp) を実行します。 次に例を示します。

```azurepowershell-interactive
New-AzWebApp -Name <app-name>
```

Git リポジトリであるディレクトリからこのコマンドレットを実行すると、自分の App Service アプリへの Git リモートが `azure` という名前で自動的に作成されます。

# <a name="azure-portal"></a>[Azure Portal](#tab/portal)

ポータルでは、最初にアプリを作成し、次にそのデプロイを構成する必要があります。 「[既存のアプリを構成する](#configure-an-existing-app)」を参照してください。

-----

## <a name="configure-an-existing-app"></a>既存のアプリを構成する

まだアプリを作成していない場合は、代わりに「[Git 対応アプリを作成する](#create-a-git-enabled-app)」を参照してください。

# <a name="azure-cli"></a>[Azure CLI](#tab/cli)

[`az webapp deployment source config-local-git`](/cli/azure/webapp/deployment/source#az-webapp-deployment-source-config-local-git) を実行します。 次に例を示します。

```azurecli-interactive
az webapp deployment source config-local-git --name <app-name> --resource-group <group-name>
```

出力には、`https://<deployment-username>@<app-name>.scm.azurewebsites.net/<app-name>.git` のような URL が含まれています。 次の手順で、この URL を使用して、ご利用になるアプリをデプロイします。

> [!TIP]
> この URL には、ユーザースコープのデプロイのユーザー名が含まれています。 必要に応じて、[アプリケーションスコープの資格情報を使用](deploy-configure-credentials.md#appscope)することができます。 

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/powershell)

自分のアプリの `scmType` を設定するには、[Set-AzResource](/powershell/module/az.resources/set-azresource) コマンドレットを実行します。

```powershell-interactive
$PropertiesObject = @{
    scmType = "LocalGit";
}

Set-AzResource -PropertyObject $PropertiesObject -ResourceGroupName <group-name> `
-ResourceType Microsoft.Web/sites/config -ResourceName <app-name>/web `
-ApiVersion 2015-08-01 -Force
```

# <a name="azure-portal"></a>[Azure Portal](#tab/portal)

1. [Azure portal](https://portal.azure.com) で、アプリの管理ページに移動します。

1. 左側のメニューで、 **[デプロイ センター]**  >  **[設定]** の順に選択します。 **[ソース]** で **[ローカル Git]** を選択してから、 **[保存]** をクリックします。

    ![Azure portal で App Service のローカル Git デプロイを有効にする方法を示しています](./media/deploy-local-git/enable-portal.png)

1. [ローカル Git] セクションで、後で使用するために **Git Clone URI** をコピーします。 この URI に資格情報は含まれていません。

-----

## <a name="deploy-the-web-app"></a>Web アプリのデプロイ

1. ローカル ターミナル ウィンドウで、ディレクトリを自分の Git リポジトリのルートに変更し、自分のアプリから取得した URL を使用して Git リモートを追加します。 選択した方法で URL を取得できない場合は、`https://<app-name>.scm.azurewebsites.net/<app-name>.git` を使用し、`<app-name>` にアプリの名前を指定します。
   
   ```bash
   git remote add azure <url>
   ```

    > [!NOTE]
    > [New-AzWebApp を使用して PowerShell で Git 対応アプリを作成した](#create-a-git-enabled-app)場合、既にリモートは自動的に作成されています。
   
1. `git push azure master` を使用して Azure リモートにプッシュします。 
   
1. **[Git Credential Manager]\(Git 資格情報マネージャー\)** ウィンドウには、自分の Azure サインイン資格情報ではなく、自分の [ユーザースコープまたはアプリケーションスコープの資格情報](#configure-a-deployment-user)を入力します。

    自分の Git リモート URL に既にユーザー名とパスワードが含まれている場合、プロンプトは表示されません。 
   
1. 出力結果を確認します。 MSBuild (ASP.NET 向け)、`npm install` (Node.js 向け)、`pip install` (Python 向け) など、ランタイム固有のオートメーションが表示される場合があります。 
   
1. Azure portal でご利用のアプリに移動して、コンテンツがデプロイされていることを確認します。

## <a name="troubleshoot-deployment"></a>デプロイのトラブルシューティング

Git を使用して Azure の App Service アプリに発行すると、次の一般的なエラーが表示される場合があります。

|Message|原因|解決方法
---|---|---|
|`Unable to access '[siteURL]': Failed to connect to [scmAddress]`|アプリが稼働していません。|Azure portal でアプリを起動します。 Web アプリが停止しているとき、Git デプロイは利用できません。|
|`Couldn't resolve host 'hostname'`|'azure' リモートのアドレス情報が正しくありません。|`git remote -v` コマンドを使用して、すべてのリモートおよび関連付けられている URL を一覧表示します。 "azure" リモートの URL が正しいことを確認します。 必要に応じて、このリモートを削除し、正しい URL を使用して再作成します。|
|`No refs in common and none specified; doing nothing. Perhaps you should specify a branch such as 'main'.`|`git push` の間にブランチを指定しなかったか、または `.gitconfig` に `push.default` 値を設定していません。|メイン ブランチを指定して、もう一度 `git push` を実行します: `git push azure main`。|
|`src refspec [branchname] does not match any.`|"azure" リモートのメイン以外のブランチにプッシュしようとしました。|メイン ブランチを指定して、もう一度 `git push` を実行します: `git push azure main`。|
|`RPC failed; result=22, HTTP code = 5xx.`|このエラーは、HTTPS 経由で大規模な Git リポジトリをプッシュしようとした場合に発生する可能性があります。|ローカル コンピューター上の Git 構成を変更して `postBuffer` を増やします。 (例: `git config --global http.postBuffer 524288000`)。|
|`Error - Changes committed to remote repository but your web app not updated.`|追加の必須モジュールを指定する _package.json_ ファイルを使用して Node.js アプリをデプロイしました。|失敗に関する詳細なコンテキストについては、このエラーの前の `npm ERR!` エラー メッセージを確認してください。 このエラーの既知の原因と、対応する `npm ERR!` メッセージを以下に示します。<br /><br />**形式が正しくない package.json ファイル**: `npm ERR! Couldn't read dependencies.`<br /><br />**Windows 用のバイナリ配布がないネイティブ モジュール**:<br />`npm ERR! \cmd "/c" "node-gyp rebuild"\ failed with 1` <br />or <br />`npm ERR! [modulename@version] preinstall: \make || gmake\ `|

## <a name="additional-resources"></a>その他のリソース

- [App Service ビルド サーバー (Project Kudu に関するドキュメント)](https://github.com/projectkudu/kudu/wiki)
- [Azure App Service への継続的デプロイ](deploy-continuous-deployment.md)
- [サンプル:Web アプリを作成してローカル Git リポジトリからコードをデプロイする (Azure CLI)](./scripts/cli-deploy-local-git.md?toc=%2fcli%2fazure%2ftoc.json)
- [サンプル:Web アプリを作成してローカル Git リポジトリからコードをデプロイする (PowerShell)](./scripts/powershell-deploy-local-git.md?toc=%2fpowershell%2fmodule%2ftoc.json)
