---
title: 継続的なデプロイを構成する
description: GitHub、BitBucket、Azure Repos、またはその他のリポジトリから Azure App Service への CI/CD を有効にする方法について説明します。 ニーズに合ったビルド パイプラインを選択します。
ms.assetid: 6adb5c84-6cf3-424e-a336-c554f23b4000
ms.topic: article
ms.date: 03/12/2021
ms.reviewer: dariac
ms.custom: seodec18
ms.openlocfilehash: 52f0db739cff9614dc4e9f5ef71d582e926fc65a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "103470270"
---
# <a name="continuous-deployment-to-azure-app-service"></a>Azure App Service への継続的デプロイ

[Azure App Service](overview.md) により、最新の更新プログラムをプルすることで、[GitHub](https://help.github.com/articles/create-a-repo)、[BitBucket](https://confluence.atlassian.com/get-started-with-bitbucket/create-a-repository-861178559.html)、[Azure Repos](/azure/devops/repos/git/creatingrepo) のリポジトリからの継続的なデプロイが可能になります。

> [!NOTE]
> Azure portal の **[開発センター (クラシック)]** ページ (以前のデプロイ エクスペリエンス) は、2021 年 3 月に非推奨となる予定です。 この変更はアプリの既存のデプロイ設定には影響せず、 **[デプロイ センター]** ページで引き続きアプリのデプロイを管理できます。

[!INCLUDE [Prepare repository](../../includes/app-service-deploy-prepare-repo.md)]

## <a name="configure-deployment-source"></a>デプロイ ソースを構成する

1. [Azure portal](https://portal.azure.com)で、App Service アプリの管理ページに移動します。

1. 左側のメニューで、 **[デプロイ センター]**  >  **[設定]** の順にクリックします。 

1. **[ソース]** で、いずれかの CI/CD オプションを選択します。

    ![Azure App Service のデプロイ センターでデプロイ ソースを選択する方法について説明します](media/app-service-continuous-deployment/choose-source.png)

選択したステップに対応するタブを選択します。

# <a name="github"></a>[GitHub](#tab/github)

4. [GitHub Actions](#how-the-github-actions-build-provider-works) は、既定のビルド プロバイダーです。 これを変更するには、 **[プロバイダーの変更]**  >  **[App Service のビルド サービス]** (Kudu) > **[OK]** の順にクリックします。

    > [!NOTE]
    > App Service アプリのビルド プロバイダーとして Azure Pipelines を使用するには、App Service でそれを構成しないでください。 代わりに、Azure Pipelines から CI/CD を直接構成してください。 **Azure Pipelines** のオプションは、正しい方向を示すだけです。

1. 初めて GitHub からデプロイする場合は、 **[承認]** をクリックし、承認のプロンプトに従います。 別のユーザーのリポジトリからデプロイする場合は、 **[アカウントの変更]** をクリックします。

1. GitHub で Azure アカウントを承認したら、 **[組織]** 、 **[リポジトリ]** 、 **[ブランチ]** の順に選択して CI/CD を構成します。

1. GitHub Actions が選択したビルド プロバイダーの場合は、 **[ランタイム スタック]** および **[バージョン]** ドロップダウンで目的のワークフロー ファイルを選択できます。 Azure では、選択された GitHub リポジトリにこのワークフロー ファイルをコミットして、ビルドとデプロイのタスクを処理します。 変更を保存する前にこのファイルを確認するには、 **[ファイルのプレビュー]** をクリックします。

    > [!NOTE]
    > App Service では、アプリの "[言語スタック設定](configure-common.md#configure-language-stack-settings)" を検出し、最も適切なワークフロー テンプレートを選択します。 別のテンプレートを選択すると、適切に実行されないアプリがデプロイされる可能性があります。 詳細については、「[GitHub Actions ビルド プロバイダーのしくみ](#how-the-github-actions-build-provider-works)」を参照してください。

1. **[保存]** をクリックします。
   
    選択したリポジトリおよびブランチでの新しいコミットが App Service アプリに継続的にデプロイされるようになりました。 コミットとデプロイは、 **[ログ]** タブで追跡できます。

# <a name="bitbucket"></a>[BitBucket](#tab/bitbucket)

BitBucket 統合では、ビルドの自動化のために App Service ビルド サービス (Kudu) を使用します。

4. BitBucket から初めてデプロイする場合は、 **[承認]** をクリックし、承認のプロンプトに従います。 別のユーザーのリポジトリからデプロイする場合は、 **[アカウントの変更]** をクリックします。

1. Bitbucket の場合、継続的にデプロイする Bitbucket の **チーム**、**リポジトリ**、**ブランチ** を選択します。

1. **[保存]** をクリックします。
   
    選択したリポジトリおよびブランチでの新しいコミットが App Service アプリに継続的にデプロイされるようになりました。 コミットとデプロイは、 **[ログ]** タブで追跡できます。
   
# <a name="local-git"></a>[ローカル Git](#tab/local)

「[Azure App Service へのローカル Git デプロイ](deploy-local-git.md)」を参照してください。

# <a name="azure-repos"></a>[Azure Repos](#tab/repos)

> [!NOTE]
> デプロイ ソースとしての Azure Repos は、Windows アプリのサポートです。
>

4. App Service ビルド サービス (Kudu) は、既定のビルド プロバイダーです。

    > [!NOTE]
    > App Service アプリのビルド プロバイダーとして Azure Pipelines を使用するには、App Service でそれを構成しないでください。 代わりに、Azure Pipelines から CI/CD を直接構成してください。 **Azure Pipelines** のオプションは、正しい方向を示すだけです。

1. 継続的にデプロイする **Azure DevOps の組織**、**プロジェクト**、**リポジトリ**、**ブランチ** を選択します。 

    DevOps 組織が一覧に表示されていない場合は、お使いの Azure サブスクリプションにまだリンクされていません。 詳細については、[Azure サービス接続の作成](/azure/devops/pipelines/library/connect-to-azure)に関するページを参照してください。

-----

## <a name="disable-continuous-deployment"></a>継続的なデプロイの無効化

1. [Azure portal](https://portal.azure.com)で、App Service アプリの管理ページに移動します。

1. 左側のメニューで、 **[デプロイ センター]** 、 **[設定]** 、 **[接続解除]** の順にクリックします。 

    ![Azure portal の App Service アプリでクラウド フォルダーの同期を切断する方法を示します。](media/app-service-continuous-deployment/disable.png)

1. 既定では、GitHub Actions ワークフロー ファイルはリポジトリに保持されますが、引き続きアプリへのデプロイをトリガーします。 これをリポジトリから削除するには、 **[ワークフロー ファイルの削除]** を選択します。

1. **[OK]** をクリックします。

[!INCLUDE [What happens to my app during deployment?](../../includes/app-service-deploy-atomicity.md)]

## <a name="how-the-github-actions-build-provider-works"></a>GitHub Actions ビルド プロバイダーのしくみ

GitHub Actions ビルド プロバイダーは、[GitHub の CI/CD](#configure-deployment-source) 用のオプションであり、CI/CD を設定するために次の操作を実行します。

- Github Actions ワークフロー ファイルを GitHub リポジトリに保管して、App Service のビルドとデプロイのタスクを処理します。
- アプリの発行プロファイルを GitHub シークレットとして追加します。 ワークフロー ファイルは、このシークレットを使用して App Service に対する認証を行います。
- [ワークフロー実行ログ](https://docs.github.com/actions/managing-workflow-runs/using-workflow-run-logs)から情報をキャプチャし、それをアプリの "**デプロイ センター**" の **[ログ]** タブに表示します。

GitHub Actions ビルド プロバイダーは、次の方法でカスタマイズできます。

- GitHub リポジトリでワークフロー ファイルが生成されたら、それをカスタマイズします。 詳しくは、「[GitHub Actions のワークフロー構文](https://docs.github.com/actions/reference/workflow-syntax-for-github-actions)」を参照してください。 ワークフローが [azure/webapps-deploy](https://github.com/Azure/webapps-deploy) アクションを使用して App Service にデプロイされていることを確認してください。
- 選択したブランチが保護されている場合でも、構成を保存せずに引き続きワークフロー ファイルをプレビューしてから、手動でリポジトリに追加することができます。 この方法では、Azure portal とのログ統合は行われません。
- 発行プロファイルではなく、Azure Active Directory で[サービス プリンシパル](../active-directory/develop/app-objects-and-service-principals.md#service-principal-object)を使用してデプロイします。

#### <a name="authenticate-with-a-service-principal"></a>サービス プリンシパルでの認証

このオプション構成では、既定の認証が、生成されたワークフロー ファイル内の発行プロファイルに置き換えられます。

1. [Azure CLI](/cli/azure/) で [az ad sp create-for-rbac](/cli/azure/ad/sp#az-ad-sp-create-for-rbac) コマンドを使用して、サービス プリンシパルを作成します。 次の例では、 *\<subscription-id>* 、 *\<group-name>* 、および *\<app-name>* を独自の値に置き換えます。

    ```azurecli-interactive
    az ad sp create-for-rbac --name "myAppDeployAuth" --role contributor \
                                --scopes /subscriptions/<subscription-id>/resourceGroups/<group-name>/providers/Microsoft.Web/sites/<app-name> \
                                --sdk-auth
    ```
    
    > [!IMPORTANT]
    > セキュリティのために、サービス プリンシパルに最低限必要なアクセス権を付与してください。 前の例の範囲は、リソース グループ全体ではなく、特定の App Service アプリに限定されます。
    
1. 最上位レベル `{}` を含め、次の手順の JSON 出力全体を保存します。

1. [GitHub](https://github.com/) で、自分のリポジトリを参照し、 **[設定] > [シークレット] > [新しいシークレットの追加]** を選択します。

1. Azure CLI コマンドからの JSON 出力全体をシークレットの値フィールドに貼り付けます。 シークレットに `AZURE_CREDENTIALS` などの名前を付けます。

1. "**デプロイ センター**" によって生成されたワークフロー ファイルで、次の例のようなコードで `azure/webapps-deploy` ステップを修正します (これは Node.js ワークフロー ファイルから変更されています)。

    ```yaml
    - name: Sign in to Azure 
    # Use the GitHub secret you added
    - uses: azure/login@v1
      with:
        creds: ${{ secrets.AZURE_CREDENTIALS }}
    - name: Deploy to Azure Web App
    # Remove publish-profile
    - uses: azure/webapps-deploy@v2
      with:
        app-name: '<app-name>'
        slot-name: 'production'
        package: .
    - name: Sign out of Azure
      run: |
        az logout
    ```
    
## <a name="deploy-from-other-repositories"></a>他のリポジトリからデプロイする

Windows アプリの場合、ポータルで直接サポートされていないクラウド Git または Mercurial リポジトリ ([GitLab](https://gitlab.com/) など) からの継続的なデプロイを手動で構成できます。 これを行うには、 **[ソース]** ドロップダウンで [外部 Git] を選択します。 詳細については、[手動の手順を使用した継続的デプロイの設定](https://github.com/projectkudu/kudu/wiki/Continuous-deployment#setting-up-continuous-deployment-using-manual-steps)に関するページを参照してください。

## <a name="more-resources"></a>その他のリソース

* [Azure Pipelines から Azure App Services にデプロイする](/azure/devops/pipelines/apps/cd/deploy-webdeploy-webapps)
* [継続的なデプロイに関する一般的な問題の調査](https://github.com/projectkudu/kudu/wiki/Investigating-continuous-deployment)
* [Azure PowerShell の使用](/powershell/azure/)
* [プロジェクト Kudu](https://github.com/projectkudu/kudu/wiki)
