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
ms.topic: article
ms.date: 06/18/2019
ms.author: cephalin
ms.reviewer: dariac
ms.custom: seodec18
ms.openlocfilehash: 9714052de5cdfbdbf0789c7b4711469ee9202bac
ms.sourcegitcommit: 82499878a3d2a33a02a751d6e6e3800adbfa8c13
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/28/2019
ms.locfileid: "70070639"
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

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="deploy-with-kudu-build-server"></a>Kudu ビルド サーバーを使用してデプロイする

Kudu App Service ビルド サーバーを使用してアプリのローカル Git デプロイを有効にするには、Azure Cloud Shell を使用するのが最も簡単な方法です。 

### <a name="configure-a-deployment-user"></a>デプロイ ユーザーを構成する

[!INCLUDE [Configure a deployment user](../../includes/configure-deployment-user-no-h.md)]

### <a name="get-the-deployment-url"></a>デプロイ URL を取得する

既存のアプリのローカル Git デプロイを有効にするための URL を取得するには、Cloud Shell で [`az webapp deployment source config-local-git`](/cli/azure/webapp/deployment/source?view=azure-cli-latest#az-webapp-deployment-source-config-local-git) を実行します。 \<app-name> と \< group-name> を、ご自分のアプリの名前とその Azure リソース グループの名前に置き換えます。

```azurecli-interactive
az webapp deployment source config-local-git --name <app-name> --resource-group <group-name>
```

Git 対応アプリを新規に作成するには、Cloud Shell で `--deployment-local-git` パラメーターを指定して [`az webapp create`](/cli/azure/webapp?view=azure-cli-latest#az-webapp-create) を実行します。 \<app-name>、\<group-name>、\<plan-name> を、ご利用になる新しい Git アプリの名前、その Azure リソース グループの名前、およびその Azure App Service プランの名前に置き換えます。

```azurecli-interactive
az webapp create --name <app-name> --resource-group <group-name> --plan <plan-name> --deployment-local-git
```

どちらのコマンドからも次のような URL が返されます: `https://<deployment-username>@<app-name>.scm.azurewebsites.net/<app-name>.git` 次の手順で、この URL を使用して、ご利用になるアプリをデプロイします。

このアカウント レベルの URL を使用するのでなく、アプリ レベルの資格情報を使用してローカル Git を有効にすることもできます。 Azure App Service では、すべてのアプリに対してこれらの資格情報が自動的に作成されます。 

Cloud Shell で次のコマンドを実行して、アプリの資格情報を取得します。 \<app-name> と \<group-name> をご利用のアプリの名前と Azure リソース グループの名前に置き換えます。

```azurecli-interactive
az webapp deployment list-publishing-credentials --name <app-name> --resource-group <group-name> --query scmUri --output tsv
```

次の手順では、返された URL を使用してアプリをデプロイします。

### <a name="deploy-the-web-app"></a>Web アプリのデプロイ

1. ローカル ターミナル ウィンドウを開き、ご利用のローカル Git リポジトリに Azure リモートを追加します。 次のコマンドでは、\<url>を、前の手順で取得したユーザー固有のデプロイ URL またはアプリ固有のデプロイ URL に置き換えます。
   
   ```bash
   git remote add azure <url>
   ```
   
1. `git push azure master` を使用して Azure リモートにプッシュします。 
   
1. **[Git Credential Manager]** ウィンドウで、ご利用の Azure サインインパスワードではなく、ご利用の[デプロイ ユーザー パスワード](#configure-a-deployment-user)を入力します。
   
1. 出力結果を確認します。 MSBuild (ASP.NET 向け)、`npm install` (Node.js 向け)、`pip install` (Python 向け) など、ランタイム固有のオートメーションが表示される場合があります。 
   
1. Azure portal でご利用のアプリに移動して、コンテンツがデプロイされていることを確認します。

## <a name="deploy-with-azure-pipelines-builds"></a>Azure Pipelines ビルドを使用してデプロイする

必要なアクセス許可がご利用のアカウントにある場合は、ご利用になるアプリのローカル Git デプロイを有効にするように Azure Pipelines (プレビュー) を設定することができます。 

- ご利用の Azure アカウントには、Azure Active Directory への書き込みを行うためのアクセス許可とサービスを作成するためのアクセス許可が必要です。 
  
- ご利用の Azure アカウントには、Azure サブスクリプションの**所有者**ロールが必要です。

- ご自身が、使用する Azure DevOps プロジェクトの管理者である必要があります。

Azure Pipelines (プレビュー) を使用してアプリのローカル Git デプロイを有効にするには:

1. Azure portal でご利用の [Azure App Service](https://portal.azure.com) アプリのページに移動し、左側のメニューで **[デプロイ センター]** を選択します。
   
1. **[デプロイ センター]** ページで、 **[ローカル Git]** を選択して、 **[続行]** を選択します。 
   
   ![[ローカル Git] を選択してから、[続行] を選択します](media/app-service-deploy-local-git/portal-enable.png)
   
1. **[ビルド プロバイダー]** ページで **[Azure Pipelines]** (プレビュー) を選択して、 **[続行]** を選択します。 
   
   ![[Azure Pipelines] (プレビュー) を選択してから、[続行] を選択します。](media/app-service-deploy-local-git/pipeline-builds.png)

1. **[構成]** ページで、新しい Azure DevOps 組織を構成するか、または既存の組織を指定します。次に、 **[続行]** を選択します。
   
   > [!NOTE]
   > 既存の Azure DevOps 組織が一覧に表示されていない場合は、それをお使いの Azure サブスクリプションにリンクしなければならない場合があります。 詳細については、「[Define your CD release pipeline](/azure/devops/pipelines/apps/cd/deploy-webdeploy-webapps#cd)」(CD リリース パイプラインを定義する) を参照してください。
   
1. App Service プランの[価格レベル](https://azure.microsoft.com/pricing/details/app-service/plans/)によっては、 **[ステージングへのデプロイ]** ページが表示される場合があります。 [デプロイ スロットを有効にする](deploy-staging-slots.md)かどうかを選択して、 **[続行]** を選択します。
   
1. **[概要]** ページで設定を確認して、 **[完了]** を選択します。
   
1. Azure Pipeline の準備ができたら、 **[デプロイ センター]** ページから、次の手順で使用する Git リポジトリの URL をコピーします。 
   
   ![Git リポジトリの URL をコピーする](media/app-service-deploy-local-git/vsts-repo-ready.png)

1. ローカル ターミナル ウィンドウで、ご利用のローカル Git リポジトリに Azure リモートを追加します。 コマンドで、前の手順で取得した Git リポジトリの URL に \<url> を置き換えます。
   
   ```bash
   git remote add azure <url>
   ```
   
1. `git push azure master` を使用して Azure リモートにプッシュします。 
   
1. **[Git Credential Manager]** ページで、ご自分の visualstudio.com ユーザーを使用してサインインします。 他の認証方法については、[Azure DevOps Services 認証の概要](/vsts/git/auth-overview?view=vsts)に関する記事を参照してください。
   
1. デプロイが完了したら、`https://<azure_devops_account>.visualstudio.com/<project_name>/_build` でビルドの進行状況を、`https://<azure_devops_account>.visualstudio.com/<project_name>/_release` でデプロイの進行状況を確認します。
   
1. Azure portal でご利用のアプリに移動して、コンテンツがデプロイされていることを確認します。

[!INCLUDE [What happens to my app during deployment?](../../includes/app-service-deploy-atomicity.md)]

## <a name="troubleshoot-deployment"></a>デプロイのトラブルシューティング

Git を使用して Azure の App Service アプリに発行すると、次の一般的なエラーが表示される場合があります。

|Message|原因|解決策
---|---|---|
|`Unable to access '[siteURL]': Failed to connect to [scmAddress]`|アプリが稼働していません。|Azure portal でアプリを起動します。 Web アプリが停止しているとき、Git デプロイは利用できません。|
|`Couldn't resolve host 'hostname'`|'azure' リモートのアドレス情報が正しくありません。|`git remote -v` コマンドを使用して、すべてのリモートおよび関連付けられている URL を一覧表示します。 "azure" リモートの URL が正しいことを確認します。 必要に応じて、このリモートを削除し、正しい URL を使用して再作成します。|
|`No refs in common and none specified; doing nothing. Perhaps you should specify a branch such as 'master'.`|`git push` の間にブランチを指定しなかったか、または `.gitconfig` に `push.default` 値を設定していません。|master ブランチを指定して、もう一度 `git push` を実行します: `git push azure master`。|
|`src refspec [branchname] does not match any.`|"azure" リモートの master 以外のブランチにプッシュしようとしました。|master ブランチを指定して、もう一度 `git push` を実行します: `git push azure master`。|
|`RPC failed; result=22, HTTP code = 5xx.`|このエラーは、HTTPS 経由で大規模な Git リポジトリをプッシュしようとした場合に発生する可能性があります。|ローカル コンピューター上の Git 構成を変更して `postBuffer` を増やします。 (例: `git config --global http.postBuffer 524288000`)。|
|`Error - Changes committed to remote repository but your web app not updated.`|追加の必須モジュールを指定する _package.json_ ファイルを使用して Node.js アプリをデプロイしました。|失敗に関する詳細なコンテキストについては、このエラーの前の `npm ERR!` エラー メッセージを確認してください。 このエラーの既知の原因と、対応する `npm ERR!` メッセージを以下に示します。<br /><br />**形式が正しくない package.json ファイル**: `npm ERR! Couldn't read dependencies.`<br /><br />**Windows 用のバイナリ配布がないネイティブ モジュール**:<br />`npm ERR! \cmd "/c" "node-gyp rebuild"\ failed with 1` <br />or <br />`npm ERR! [modulename@version] preinstall: \make || gmake\ `|

## <a name="additional-resources"></a>その他のリソース

- [Project Kudu に関するドキュメント](https://github.com/projectkudu/kudu/wiki)
- [Azure App Service への継続的デプロイ](deploy-continuous-deployment.md)
- [サンプル: Web アプリを作成してローカル Git リポジトリからコードをデプロイする (Azure CLI)](./scripts/cli-deploy-local-git.md?toc=%2fcli%2fazure%2ftoc.json)
- [サンプル: Web アプリを作成してローカル Git リポジトリからコードをデプロイする (PowerShell)](./scripts/powershell-deploy-local-git.md?toc=%2fpowershell%2fmodule%2ftoc.json)
