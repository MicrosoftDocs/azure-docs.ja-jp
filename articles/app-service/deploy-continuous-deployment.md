---
title: 継続的なデプロイを構成する
description: GitHub、BitBucket、Azure Repos、またはその他のリポジトリから Azure App Service への CI/CD を有効にする方法について説明します。 ニーズに合ったビルド パイプラインを選択します。
ms.assetid: 6adb5c84-6cf3-424e-a336-c554f23b4000
ms.topic: article
ms.date: 03/20/2020
ms.reviewer: dariac
ms.custom: seodec18
ms.openlocfilehash: fa30c6c2c3ecd9c9c119fee80b7ef90999e42d30
ms.sourcegitcommit: 648c8d250106a5fca9076a46581f3105c23d7265
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/27/2020
ms.locfileid: "88962623"
---
# <a name="continuous-deployment-to-azure-app-service"></a>Azure App Service への継続的デプロイ

[Azure App Service](overview.md) により、最新の更新プログラムをプルすることで、GitHub、BitBucket、[Azure Repos](https://azure.microsoft.com/services/devops/repos/) のリポジトリからの継続的なデプロイが可能になります。 この記事では、Azure portal を使用して、Kudu ビルド サービスまたは [Azure Pipelines](https://azure.microsoft.com/services/devops/pipelines/) を通じてアプリを継続的にデプロイする方法について説明します。 

ソース管理サービスの詳細については、「[Create a repo (GitHub) (リポジトリの作成 (GitHub))]」(GitHub)、「[Create a repo (BitBucket) (リポジトリの作成 (BitBucket))]」(BitBucket)、「[新しい Git リポジトリの作成 (Azure Repos)]」(Azure Repos) を参照してください。

[!INCLUDE [Prepare repository](../../includes/app-service-deploy-prepare-repo.md)]

## <a name="authorize-azure-app-service"></a>Azure App Service を承認する 

Azure Repos を使用するには、Azure DevOps 組織がお使いの Azure サブスクリプションにリンクされていることを確認します。 詳細については、[Web アプリにデプロイできるようにするための Azure DevOps Services アカウントの設定](/azure/devops/pipelines/apps/cd/deploy-webdeploy-webapps?view=azure-devops)に関するページを参照してください。

Bitbucket または GitHub の場合は、Azure App Service がお使いのリポジトリに接続することを承認します。 ソース管理サービスを使用して承認する必要があるのは 1 回だけです。 

1. [Azure portal](https://portal.azure.com) で、**App Services** を検索して選択します。

   ![App Services を検索します。](media/app-service-continuous-deployment/search-for-app-services.png)

1. デプロイする App Service を選択します。

   ![アプリを選択します。](media/app-service-continuous-deployment/select-your-app.png)
   
1. アプリのページで、左側のメニューから **[管理者用センター]** を選択します。
   
1. **[管理者用センター]** ページで、 **[GitHub]** または **[Bitbucket]** を選択し、次に **[承認]** を選択します。 
   
   ![ソース管理サービスを選択し、[承認] を選択します。](media/app-service-continuous-deployment/github-choose-source.png)
   
1. 必要に応じてサービスにサインインし、承認のプロンプトに従います。 

## <a name="enable-continuous-deployment"></a>継続的配置を有効にする 

ソース管理サービスを承認した後、組み込みの [Kudu App Service](#option-1-kudu-app-service) ビルド サーバー、または [Azure Pipelines](#option-2-azure-pipelines) を通じて継続的なデプロイ用にアプリを構成します。 

### <a name="option-1-kudu-app-service"></a>オプション 1: Kudu App Service

組み込みの Kudu App Service ビルド サーバーを使用して、GitHub、Bitbucket、または Azure Repos から継続的にデプロイできます。 

1. [Azure portal](https://portal.azure.com) で、**App Services** を検索して選択した後、デプロイする Web アプリを選択します。 
   
1. アプリのページで、左側のメニューから **[管理者用センター]** を選択します。
   
1. **[管理者用センター]** ページで、承認したソース管理プロバイダーを選択し、 **[続行]** を選択します。 GitHub または Bitbucket の場合、さらに **[アカウントの変更]** を選択して、承認されたアカウントを変更することもできます。 
   
   > [!NOTE]
   > Azure Repos を使用するには、Azure DevOps Services 組織がお使いの Azure サブスクリプションにリンクされていることを確認します。 詳細については、[Web アプリにデプロイできるようにするための Azure DevOps Services アカウントの設定](/azure/devops/pipelines/apps/cd/deploy-webdeploy-webapps?view=azure-devops)に関するページを参照してください。
   
1. GitHub または Azure Repos の場合、 **[ビルド プロバイター]** ページで **[App Service のビルド サービス]** を選択し、 **[続行]** を選択します。 Bitbucket では常に App Service のビルド サービスが使用されます。
   
   ![[App Service のビルド サービス] を選択して、[続行] を選択します。](media/app-service-continuous-deployment/choose-kudu.png)
   
1. **[構成]** ページで、次を実行します。
   
   - GitHub の場合、ドロップダウンから、継続的にデプロイする**組織**、**リポジトリ**、および**ブランチ**を選択します。
     
     > [!NOTE]
     > リポジトリが表示されない場合は、GitHub で Azure App Service を承認しなければならない場合があります。 GitHub リポジトリを参照し、 **[設定]**  >  **[アプリケーション]**  >  **[Authorized OAuth Apps]\(認可された OAuth アプリ\)** の順に移動します。 **[Azure App Service]** を選択し、次に **[許可]\(Grant\)** を選択します。 組織リポジトリの場合は、アクセス許可を付与する組織の所有者である必要があります。
     
   - Bitbucket の場合、継続的にデプロイする Bitbucket の **チーム**、**リポジトリ**、**ブランチ**を選択します。
     
   - Azure Repos の場合、継続的にデプロイする **Azure DevOps の組織**、**プロジェクト**、**リポジトリ**、**ブランチ**を選択します。
     
     > [!NOTE]
     > Azure DevOps 組織が一覧に表示されていない場合は、お使いの Azure サブスクリプションにリンクされていることを確認してください。 詳細については、[Web アプリにデプロイできるようにするための Azure DevOps Services アカウントの設定](/azure/devops/pipelines/apps/cd/deploy-webdeploy-webapps?view=azure-devops)に関するページを参照してください。
     
1. **[続行]** をクリックします。
   
   ![リポジトリの情報を入力し、[続行] を選択します。](media/app-service-continuous-deployment/configure-kudu.png)
   
1. ビルド プロバイダーを構成したら、 **[概要]** ページで設定を確認し、 **[完了]** を選択します。
   
1. 選択したリポジトリおよびブランチでの新しいコミットが App Service アプリに継続的にデプロイされるようになりました。 **[管理者用センター]** ページで、コミットとデプロイを追跡できます。
   
   ![管理者用センターでコミットとデプロイを追跡する](media/app-service-continuous-deployment/github-finished.png)

### <a name="option-2-azure-pipelines"></a>オプション 2:Azure Pipelines 

アカウントに、必要なアクセス許可がある場合は、GitHub または Azure Repos から継続的にデプロイするように Azure Pipelines を設定できます。 Azure Pipelines を使用したデプロイの詳細については、「[Web アプリを Azure App Services にデプロイする](/azure/devops/pipelines/apps/cd/deploy-webdeploy-webapps)」を参照してください。

#### <a name="prerequisites"></a>前提条件

Azure App Service で Azure Pipelines を使用する継続的デリバリーを作成するには、Azure DevOps 組織が次のアクセス許可を所有している必要があります。 

- ご利用の Azure アカウントには、Azure Active Directory への書き込みを行うためのアクセス許可とサービスを作成するためのアクセス許可が必要です。 
  
- ご利用の Azure アカウントには、Azure サブスクリプションの**所有者**ロールが必要です。

- ご自身が、使用する Azure DevOps プロジェクトの管理者である必要があります。

#### <a name="github--azure-pipelines"></a>GitHub と Azure Pipelines

1. [Azure portal](https://portal.azure.com) で、**App Services** を検索して選択した後、デプロイする Web アプリを選択します。 
   
1. アプリのページで、左側のメニューから **[デプロイ センター]** を選択します。

1. **[デプロイ センター]** ページで、ソース管理プロバイダーとして **[GitHub]** を選択し、 **[続行]** を選択します。 **GitHub** の場合、 **[アカウントの変更]** を選択して、承認されたアカウントを変更できます。

    ![ソース管理 (source control)](media/app-service-continuous-deployment/deployment-center-src-control.png)
   
1. **[ビルド プロバイダー]** ページで **[Azure Pipelines (プレビュー)]** を選択した後、 **[続行]** を選択します。

    ![ビルド プロバイダー](media/app-service-continuous-deployment/select-build-provider.png)
   
1. **[構成]** ページの **[コード]** セクションで、継続的デプロイのソースになる**組織**、**リポジトリ**、および**ブランチ**を選択し、 **[続行]** を選択します。
     
     > [!NOTE]
     > リポジトリが表示されない場合は、GitHub で Azure App Service を承認しなければならない場合があります。 GitHub リポジトリを参照し、 **[Settings]\(設定\)**  >  **[Applications]\(アプリケーション\)**  >  **[Authorized OAuth Apps]\(認可された OAuth アプリ\)** の順に移動します。 **[Azure App Service]** を選択し、次に **[Grant]\(許可\)** を選択します。 組織リポジトリの場合は、アクセス許可を付与する組織の所有者である必要があります。
       
    **[ビルド]** セクションで、Azure Pipelines でビルド タスクを実行するために使用する必要がある Azure DevOps 組織、プロジェクト、言語フレームワークを指定した後、 **[続行]** を選択します。

   ![ビルド プロバイダー](media/app-service-continuous-deployment/build-configure.png)

1. ビルド プロバイダーを構成したら、 **[概要]** ページで設定を確認し、 **[完了]** を選択します。

   ![ビルド プロバイダー](media/app-service-continuous-deployment/summary.png)
   
1. 選択したリポジトリおよびブランチでの新しいコミットが App Service に継続的にデプロイされるようになりました。 **[管理者用センター]** ページで、コミットとデプロイを追跡できます。
   
   ![管理者用センターでコミットとデプロイを追跡する](media/app-service-continuous-deployment/github-finished.png)

#### <a name="azure-repos--azure-pipelines"></a>Azure Repos と Azure Pipelines

1. [Azure portal](https://portal.azure.com) で、**App Services** を検索して選択した後、デプロイする Web アプリを選択します。 
   
1. アプリのページで、左側のメニューから **[デプロイ センター]** を選択します。

1. **[デプロイ センター]** ページで、ソース管理プロバイダーとして **[Azure Repos]** を選択し、 **[続行]** を選択します。

    ![ソース管理 (source control)](media/app-service-continuous-deployment/deployment-center-src-control.png)

1. **[ビルド プロバイダー]** ページで **[Azure Pipelines (プレビュー)]** を選択した後、 **[続行]** を選択します。

    ![ソース管理 (source control)](media/app-service-continuous-deployment/azure-pipelines.png)

1. **[構成]** ページの **[コード]** セクションで、継続的デプロイのソースになる**組織**、**リポジトリ**、および**ブランチ**を選択し、 **[続行]** を選択します。

   > [!NOTE]
   > 既存の Azure DevOps 組織が一覧に表示されていない場合は、それをお使いの Azure サブスクリプションにリンクしなければならない場合があります。 詳細については、「[Define your CD release pipeline](/azure/devops/pipelines/apps/cd/deploy-webdeploy-webapps#cd)」(CD リリース パイプラインを定義する) を参照してください。

   **[ビルド]** セクションで、Azure Pipelines でビルド タスクを実行するために使用する必要がある Azure DevOps 組織、プロジェクト、言語フレームワークを指定した後、 **[続行]** を選択します。

   ![ビルド プロバイダー](media/app-service-continuous-deployment/build-configure.png)

1. ビルド プロバイダーを構成したら、 **[概要]** ページで設定を確認し、 **[完了]** を選択します。  
     
   ![ビルド プロバイダー](media/app-service-continuous-deployment/summary-azure-pipelines.png)

1. 選択したリポジトリおよびブランチでの新しいコミットが App Service に継続的にデプロイされるようになりました。 **[管理者用センター]** ページで、コミットとデプロイを追跡できます。

## <a name="disable-continuous-deployment"></a>継続的なデプロイの無効化

継続的なデプロイを無効にするには、アプリの **[管理者用センター]** ページの上部にある **[切断]** を選択します。

![継続的なデプロイの無効化](media/app-service-continuous-deployment/disable.png)

[!INCLUDE [What happens to my app during deployment?](../../includes/app-service-deploy-atomicity.md)]

## <a name="use-unsupported-repos"></a>サポートされていないリポジトリを使用する

Windows アプリの場合、ポータルで直接サポートされていないクラウド Git または Mercurial リポジトリ ([GitLab](https://gitlab.com/) など) からの継続的なデプロイを手動で構成できます。 これを行うには、 **[デプロイ センター]** ページで [外部] ボックスを選択します。 詳細については、[手動の手順を使用した継続的デプロイの設定](https://github.com/projectkudu/kudu/wiki/Continuous-deployment#setting-up-continuous-deployment-using-manual-steps)に関するページを参照してください。

## <a name="additional-resources"></a>その他のリソース

* [継続的なデプロイに関する一般的な問題の調査](https://github.com/projectkudu/kudu/wiki/Investigating-continuous-deployment)
* [Azure PowerShell の使用](/powershell/azure/)
* [Git のドキュメント](https://git-scm.com/documentation)
* [プロジェクト Kudu](https://github.com/projectkudu/kudu/wiki)

[Create a repo (GitHub) (リポジトリの作成 (GitHub))]: https://help.github.com/articles/create-a-repo
[Create a repo (BitBucket) (リポジトリの作成 (BitBucket))]: https://confluence.atlassian.com/get-started-with-bitbucket/create-a-repository-861178559.html
[新しい Git リポジトリの作成 (Azure Repos)]: /azure/devops/repos/git/creatingrepo