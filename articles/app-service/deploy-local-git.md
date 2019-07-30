---
title: ローカル Git リポジトリからデプロイする - Azure App Service
description: Azure App Service へのローカル Git デプロイを有効にする方法を説明します。
services: app-service
documentationcenter: ''
author: cephalin
manager: cfowler
ms.assetid: ac50a623-c4b8-4dfd-96b2-a09420770063
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/14/2019
ms.author: dariagrigoriu;cephalin
ms.custom: seodec18
ms.openlocfilehash: e66c625c3f30580715762d2dd3f48eeaa6e548dc
ms.sourcegitcommit: 22c97298aa0e8bd848ff949f2886c8ad538c1473
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/14/2019
ms.locfileid: "67143947"
---
# <a name="local-git-deployment-to-azure-app-service"></a>Azure App Service へのローカル Git デプロイ

このハウツー ガイドでは、ローカル コンピューター上の Git リポジトリから [Azure App Service](overview.md) にコードをデプロイする方法を説明します。

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>前提条件

このハウツー ガイドの手順に従うには:

* [Git をインストールします](https://www.git-scm.com/downloads)。
* デプロイするコードを含むローカル Git リポジトリのメンテナンスを管理します。

サンプル リポジトリを使用して手順に従うには、ローカルのターミナル ウィンドウで次のコマンドを実行します。

```bash
git clone https://github.com/Azure-Samples/nodejs-docs-hello-world.git
```

[!INCLUDE [Prepare repository](../../includes/app-service-deploy-prepare-repo.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="deploy-with-kudu-builds"></a>Kudu ビルドを使用してデプロイする

Kudu ビルド サーバーを使用したアプリへのローカル Git のデプロイを有効にする最も簡単な方法は、Cloud Shell を使用することです。

### <a name="configure-a-deployment-user"></a>デプロイ ユーザーを構成する

[!INCLUDE [Configure a deployment user](../../includes/configure-deployment-user-no-h.md)]

> [!NOTE]
> アカウントレベルの資格情報ではなく、各アプリに対して自動的に生成されるアプリレベルの資格情報を使用してデプロイすることもできます。
>

### <a name="enable-local-git-with-kudu"></a>Kudu を使用するローカル Git を有効にする

Kudu ビルド サーバーを使用したアプリへのローカル Git のデプロイを有効にするには、Cloud Shell で [`az webapp deployment source config-local-git`](/cli/azure/webapp/deployment/source?view=azure-cli-latest#az-webapp-deployment-source-config-local-git) を実行します。

```azurecli-interactive
az webapp deployment source config-local-git --name <app-name> --resource-group <group-name>
```

Git 対応アプリを作成するには、Cloud Shell で `--deployment-local-git` パラメーターを指定して [`az webapp create`](/cli/azure/webapp?view=azure-cli-latest#az-webapp-create) を実行します。

```azurecli-interactive
az webapp create --name <app-name> --resource-group <group-name> --plan <plan-name> --deployment-local-git
```

### <a name="deploy-your-project"></a>プロジェクトのデプロイ

"_ローカル ターミナル ウィンドウ_" で、ローカル Git リポジトリに Azure リモートを追加します。 _\<username>_ は[デプロイ ユーザーの構成](#configure-a-deployment-user)のページのデプロイ ユーザーに、 _\<app-name>_ は[アプリ用の Git を有効にする](#enable-local-git-with-kudu)方法のページのアプリ名に置き換えます。

```bash
git remote add azure https://<username>@<app-name>.scm.azurewebsites.net/<app-name>.git
```

> [!NOTE]
> 代わりにアプリレベルの資格情報を使用してデプロイするには、Cloud Shell で次のコマンドを実行してアプリに固有の資格情報を取得します。
>
> ```azurecli-interactive
> az webapp deployment list-publishing-credentials -n <app-name> -g <group-name> --query scmUri --output tsv
> ```
>
> 次に、コマンド出力を使用して上記のように `git remote add azure <url>` を実行します。

アプリをデプロイするために、次のコマンドで Azure リモートにプッシュします。 パスワードの入力を求められたら、Azure portal へのサインインに使用するパスワードではなく、「[デプロイ ユーザーの構成](#configure-a-deployment-user)」で作成したパスワードを入力するようにしてください。

```bash
git push azure master
```

出力には、MSBuild (ASP.NET 向け)、`npm install` (Node.js 向け)、`pip install` (Python 向け) など、ランタイム固有のオートメーションが表示される場合があります。 

アプリに移動して、コンテンツがデプロイされていることを確認します。

## <a name="deploy-with-azure-devops-builds"></a>Azure DevOps ビルドを使用してデプロイする

> [!NOTE]
> App Service で必要な Azure Pipelines を Azure DevOps Services 組織に作成するには、Azure アカウントが Azure サブスクリプションの**所有者**ロールを持っている必要があります。
>

Kudu ビルド サーバーを使用したアプリへの ローカルGit のデプロイを有効にするには、[Azure Portal](https://portal.azure.com) でアプリに移動します。

アプリ ページの左側のナビゲーションで、 **[デプロイ センター]**  >  **[ローカル Git]**  >  **[継続]** をクリックします。

![](media/app-service-deploy-local-git/portal-enable.png)

**[Azure Pipelines (プレビュー)]**  >  **[続行]** の順にクリックします。

![](media/app-service-deploy-local-git/pipeline-builds.png)

**[構成]** ページで、新しい Azure DevOps 組織を構成するか、既存の組織を指定します。 完了したら、 **[続行]** をクリックします。

> [!NOTE]
> 一覧にない既存の Azure DevOps 組織を使用する場合、[Azure DevOps Services 組織を Azure サブスクリプションにリンク](https://github.com/projectkudu/kudu/wiki/Setting-up-a-VSTS-account-so-it-can-deploy-to-a-Web-App)する必要があります。

App Service プランの[価格レベル](https://azure.microsoft.com/pricing/details/app-service/plans/)によっては、 **[ステージングへのデプロイ]** ページも表示される場合があります。 デプロイ スロットを有効にするかどうかを選択し、 **[続行]** をクリックします。

**[概要]** ページで、選択内容を確認し、 **[完了]** をクリックします。

Azure DevOps Services 組織の準備が完了するまで数分かかります。 準備ができたら、デプロイ センターで Git リポジトリの URL をコピーします。

![](media/app-service-deploy-local-git/vsts-repo-ready.png)

"_ローカル ターミナル ウィンドウ_" で、ローカル Git リポジトリに Azure リモートを追加します。 _\<url>_ を、最後の手順で取得した URL に置き換えます。

```bash
git remote add vsts <url>
```

アプリをデプロイするために、次のコマンドで Azure リモートにプッシュします。 Git Credential Manager のプロンプトが表示されたら、visualstudio.com ユーザーでサインインします。 追加の認証方法については、[Azure DevOps Services 認証の概要](/vsts/git/auth-overview?view=vsts)に関する記事を参照してください。

```bash
git push vsts master
```

デプロイが完了したら、`https://<vsts_account>.visualstudio.com/<project_name>/_build` でビルドの進行状況を、`https://<vsts_account>.visualstudio.com/<project_name>/_release` でデプロイの進行状況を確認できます。

アプリに移動して、コンテンツがデプロイされていることを確認します。

[!INCLUDE [What happens to my app during deployment?](../../includes/app-service-deploy-atomicity.md)]

## <a name="troubleshooting-kudu-deployment"></a>Kudu デプロイのトラブルシューティング

Git を使用して Azure の App Service アプリに発行するときの一般的なエラーまたは問題を以下に示します。

---
**症状**: `Unable to access '[siteURL]': Failed to connect to [scmAddress]`

**原因**: このエラーはアプリが実行されていない場合に発生する可能性があります。

**解決策**: Azure portal でアプリを起動します。 Web アプリが停止している間は、Git デプロイは利用できません。

---
**症状**: `Couldn't resolve host 'hostname'`

**原因**: このエラーは、"azure" リモートを作成するときに入力したアドレス情報が間違っている場合に発生する可能性があります。

**解決策**: `git remote -v` コマンドを使用して、すべてのリモートおよび関連付けられている URL を一覧表示します。 "azure" リモートの URL が正しいことを確認します。 必要に応じて、このリモートを削除し、正しい URL を使用して再作成します。

---
**症状**: `No refs in common and none specified; doing nothing. Perhaps you should specify a branch such as 'master'.`

**原因**: このエラーは、`git push` でブランチを指定しなかった場合、または `.gitconfig` に `push.default` 値を設定しなかった場合に発生する可能性があります。

**解決策**: master ブランチを指定して、もう一度 `git push` を実行します。 例:

```bash
git push azure master
```

---
**症状**: `src refspec [branchname] does not match any.`

**原因**: このエラーは、"azure" リモートの master 以外のブランチにプッシュしようとした場合に発生する可能性があります。

**解決策**: master ブランチを指定して、もう一度 `git push` を実行します。 例:

```bash
git push azure master
```

---
**症状**: `RPC failed; result=22, HTTP code = 5xx.`

**原因**: このエラーは、HTTPS 経由で大規模な Git リポジトリをプッシュしようとした場合に発生する可能性があります。

**解決策**: ローカル コンピューター上の Git 構成を変更して postBuffer を増やします。

```bash
git config --global http.postBuffer 524288000
```

---
**症状**: `Error - Changes committed to remote repository but your web app not updated.`

**原因**: このエラーは、追加の必須モジュールを指定する _package.json_ ファイルを含む Node.js アプリをデプロイする場合に発生する可能性があります。

**解決策**: "npm ERR!" を含む追加のメッセージが、 このエラーの前に、ログに記録されます。このメッセージによって、このエラーに関する追加のコンテキストが提供される場合があります。 このエラーの既知の原因と、対応する "npm ERR!" メッセージを以下に示します。 メッセージ:

* **形式が正しくない package.json ファイル**: npm ERR! Couldn't read dependencies.
* **Windows 用のバイナリ配布がないネイティブ モジュール**:

  * `npm ERR! \cmd "/c" "node-gyp rebuild"\ failed with 1`

      または
  * `npm ERR! [modulename@version] preinstall: \make || gmake\`

## <a name="additional-resources"></a>その他のリソース

* [Project Kudu に関するドキュメント](https://github.com/projectkudu/kudu/wiki)
* [Azure App Service への継続的なデプロイ](deploy-continuous-deployment.md)
* [サンプル: Web アプリを作成してローカル Git リポジトリからコードをデプロイする (Azure CLI)](./scripts/cli-deploy-local-git.md?toc=%2fcli%2fazure%2ftoc.json)
* [サンプル: Web アプリを作成してローカル Git リポジトリからコードをデプロイする (PowerShell)](./scripts/powershell-deploy-local-git.md?toc=%2fpowershell%2fmodule%2ftoc.json)
