---
title: 継続的デプロイ - Azure App Service | Microsoft Docs
description: Azure App Service への継続的なデプロイを有効にする方法を説明します。
services: app-service
documentationcenter: ''
author: cephalin
manager: cfowler
ms.assetid: 6adb5c84-6cf3-424e-a336-c554f23b4000
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/03/2018
ms.author: cephalin;dariagrigoriu
ms.custom: seodec18
ms.openlocfilehash: fcb2c270b36d5efbe7b799787cf2a123b51bea5c
ms.sourcegitcommit: 02d17ef9aff49423bef5b322a9315f7eab86d8ff
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/21/2019
ms.locfileid: "58337546"
---
# <a name="continuous-deployment-to-azure-app-service"></a>Azure App Service への継続的デプロイ
この記事では、[Azure App Service](overview.md) の継続的デプロイを構成する方法を示します。 App Service は、BitBucket、GitHub、および [Azure DevOps Services](https://www.visualstudio.com/team-services/) の既存のリポジトリから最新の更新プログラムをプルすることで、これらのサービスから継続的デプロイを実行できます。

Azure Portal に表示されないクラウド リポジトリ ([GitLab](https://gitlab.com/) など) の継続的デプロイを手動で構成する方法については、「[Setting up continuous deployment using manual steps](https://github.com/projectkudu/kudu/wiki/Continuous-deployment#setting-up-continuous-deployment-using-manual-steps)」(手動による手順を使用した継続的デプロイの設定) を参照してください。

[!INCLUDE [Prepare repository](../../includes/app-service-deploy-prepare-repo.md)]

準備したリポジトリをサポートされているサービスのいずれかに発行します。 これらのサービスにプロジェクトを発行する方法の詳細については、「[Create a repo (GitHub) (リポジトリの作成 (GitHub))]」、「[Create a repo (BitBucket) (リポジトリの作成 (BitBucket))]」、「[Azure DevOps Services で作業を始める]」を参照してください。

## <a name="deploy-continuously-from-github"></a>GitHub からの継続的デプロイ

GitHub を使用する継続的デプロイを有効にするには、[Azure Portal](https://portal.azure.com) の App Service アプリ のページに移動します。

左側のメニューで、**[デプロイ センター]** > **[GitHub]** > **[承認]** をクリックします。 承認のプロンプトに従います。 

![](media/app-service-continuous-deployment/github-choose-source.png)

GitHub の承認は、1 回のみ実行する必要があります。 既に承認されている場合は、**[続行]** をクリックします。 **[アカウントの変更]** をクリックすることで、承認された GitHub アカウントを変更できます。

![](media/app-service-continuous-deployment/github-continue.png)

**[ビルド プロバイダー]** ページで、ビルド プロバイダーを選択し、**[続行]** をクリックします。

### <a name="option-1-use-app-service-kudu-build-server"></a>オプション 1: App Service Kudu ビルド サーバーを使用する

**[構成]** ページで、継続的デプロイのソースになる組織、リポジトリ、およびブランチを選択します。 完了したら、**[続行]** をクリックします。

GitHub 組織でリポジトリからデプロイするには、GitHub を参照して、**[設定]** > **[アプリケーション]** > **[Authorized OAuth Apps]\(承認済み OAuth アプリ\)** に移動します。 次に、[Azure App Service] をクリックします。

![[設定] > [アプリケーション] > [Authorized OAuth Apps]\(承認済み OAuth アプリ\) > [Azure App Service]](media/app-service-continuous-deployment/github-settings-navigation.png)

次のページで、右側にある [許可] ボタンをクリックして、App Service がご自身の組織のリポジトリにアクセスできるようにします。

![[許可] をクリックして組織のリポジトリに App Service がアクセスできるようにする](media/app-service-continuous-deployment/grant-access.png)

これでご自身の組織が、デプロイ センターの **[構成]** ページにある [組織] の一覧に表示されるようになりました。

### <a name="option-2-use-azure-pipelines-preview"></a>オプション 2: Azure Pipelines (プレビュー) を使用する

> [!NOTE]
> App Service で必要な Azure Pipelines を Azure DevOps Services 組織に作成するには、Azure アカウントが Azure サブスクリプションの**所有者**ロールを持っている必要があります。
>

**[構成]** ページの **[コード]** セクションで、継続的デプロイのソースになる組織、リポジトリ、およびブランチを選択します。 完了したら、**[続行]** をクリックします。

**[構成]** ページの **[ビルド]** セクションで、新しい Azure DevOps Services 組織を構成するか、既存の組織を指定します。 完了したら、**[続行]** をクリックします。

> [!NOTE]
> 一覧にない既存の Azure DevOps Services 組織を使用する場合、[Azure DevOps Services 組織を Azure サブスクリプションにリンク](https://github.com/projectkudu/kudu/wiki/Setting-up-a-VSTS-account-so-it-can-deploy-to-a-Web-App)する必要があります。

**[テスト]** ページで、ロード テストを有効にするかどうかを選択し、**[続行]** をクリックします。

App Service プランの[価格レベル](https://azure.microsoft.com/pricing/details/app-service/plans/)によっては、**[ステージングへのデプロイ]** ページも表示される場合があります。 [デプロイ スロットを有効にする](deploy-staging-slots.md)かどうかを選択し、**[続行]** をクリックします。

### <a name="finish-configuration"></a>構成を終了する

**[概要]** ページで、選択内容を確認し、**[完了]** をクリックします。

構成が完了したら、選択したリポジトリ内の新しいコミットが App Service アプリに継続的にデプロイされます。

![](media/app-service-continuous-deployment/github-finished.png)

## <a name="deploy-continuously-from-bitbucket"></a>BitBucket からの継続的デプロイ

BitBucket を使用する継続的デプロイを有効にするには、[Azure Portal](https://portal.azure.com) の App Service アプリ のページに移動します。

左側のメニューで、**[デプロイ センター]** > **[BitBucket]** > **[承認]** をクリックします。 承認のプロンプトに従います。 

![](media/app-service-continuous-deployment/bitbucket-choose-source.png)

BitBucket の承認は、1 回のみ実行する必要があります。 既に承認されている場合は、**[続行]** をクリックします。 **[アカウントの変更]** をクリックすることで、承認された BitBucket アカウントを変更できます。

![](media/app-service-continuous-deployment/bitbucket-continue.png)

**[構成]** ページで、継続的デプロイのソースになるリポジトリとブランチを選択します。 完了したら、**[続行]** をクリックします。

**[概要]** ページで、選択内容を確認し、**[完了]** をクリックします。

構成が完了したら、選択したリポジトリ内の新しいコミットが App Service アプリに継続的にデプロイされます。

## <a name="deploy-continuously-from-azure-repos-devops-services"></a>Azure Repos (DevOps Services) から継続的にデプロイする

[Azure Repos](https://docs.microsoft.com/azure/devops/repos/index) による継続的デプロイを有効にするには、[Azure portal](https://portal.azure.com) の App Service アプリのページに移動します。

左側のメニューで、**[デプロイ センター]** > **[Azure Repos]** > **[続行]** をクリックします。 

![](media/app-service-continuous-deployment/vsts-choose-source.png)

**[ビルド プロバイダー]** ページで、ビルド プロバイダーを選択し、**[続行]** をクリックします。

> [!NOTE]
> 一覧にない既存の Azure DevOps Services 組織を使用する場合、[Azure DevOps Services 組織を Azure サブスクリプションにリンク](https://github.com/projectkudu/kudu/wiki/Setting-up-a-VSTS-account-so-it-can-deploy-to-a-Web-App)する必要があります。

### <a name="option-1-use-app-service-kudu-build-server"></a>オプション 1: App Service Kudu ビルド サーバーを使用する

**[構成]** ページで、継続的デプロイのソースになる Azure DevOps Services 組織、プロジェクト、リポジトリ、およびブランチを選択します。 完了したら、**[続行]** をクリックします。

### <a name="option-2-use-azure-devops-services-continuous-delivery"></a>オプション 2: Azure DevOps Services の継続的デリバリーを使用する

> [!NOTE]
> App Service で必要な Azure Pipelines を Azure DevOps Services 組織に作成するには、Azure アカウントが Azure サブスクリプションの**所有者**ロールを持っている必要があります。
>

**[構成]** ページの **[コード]** セクションで、継続的デプロイのソースになる Azure DevOps Services 組織、プロジェクト、リポジトリ、およびブランチを選択します。 完了したら、**[続行]** をクリックします。

**[構成]** ページの **[ビルド]** セクションで、選択されたリポジトリに対して Azure DevOps Services がビルド タスクを実行するために使用する必要がある言語フレームワークを指定します。 完了したら、**[続行]** をクリックします。

**[テスト]** ページで、ロード テストを有効にするかどうかを選択し、**[続行]** をクリックします。

App Service プランの[価格レベル](https://azure.microsoft.com/pricing/details/app-service/plans/)によっては、**[ステージングへのデプロイ]** ページも表示される場合があります。 [デプロイ スロットを有効にする](deploy-staging-slots.md)かどうかを選択し、**[続行]** をクリックします。 

### <a name="finish-configuration"></a>構成を終了する

**[概要]** ページで、選択内容を確認し、**[完了]** をクリックします。

構成が完了したら、選択したリポジトリ内の新しいコミットが App Service アプリに継続的にデプロイされます。

## <a name="disable-continuous-deployment"></a>継続的なデプロイの無効化

継続的デプロイを無効にするには、[Azure Portal](https://portal.azure.com) の App Service アプリ のページに移動します。

左側のメニューで、**[デプロイ センター]** > **[GitHub]** または **[Azure DevOps Services]** または **[BitBucket]** > **[切断]** をクリックします。

![](media/app-service-continuous-deployment/disable.png)

[!INCLUDE [What happens to my app during deployment?](../../includes/app-service-deploy-atomicity.md)]

## <a name="additional-resources"></a>その他のリソース

* [継続的なデプロイに関する一般的な問題の調査方法](https://github.com/projectkudu/kudu/wiki/Investigating-continuous-deployment)
* [How to use PowerShell for Azure (Azure 用の PowerShell を使用する方法)]
* [Git に関するドキュメント]
* [プロジェクト Kudu](https://github.com/projectkudu/kudu/wiki)
* [Use Azure to automatically generate a CI/CD pipeline to deploy an ASP.NET 4 app](https://www.visualstudio.com/docs/build/get-started/aspnet-4-ci-cd-azure-automatic) (Azure を使用して、ASP.NET 4 アプリをデプロイする CI/CD パイプラインを自動的に生成する)

[Azure portal]: https://portal.azure.com
[Azure DevOps portal]: https://azure.microsoft.com/services/devops/
[Installing Git]: https://git-scm.com/book/en/Getting-Started-Installing-Git
[How to use PowerShell for Azure (Azure 用の PowerShell を使用する方法)]: /powershell/azureps-cmdlets-docs
[Git に関するドキュメント]: https://git-scm.com/documentation

[Create a repo (GitHub) (リポジトリの作成 (GitHub))]: https://help.github.com/articles/create-a-repo
[Create a repo (BitBucket) (リポジトリの作成 (BitBucket))]: https://confluence.atlassian.com/get-started-with-bitbucket/create-a-repository-861178559.html
[Azure DevOps Services で作業を始める]: https://docs.microsoft.com/azure/devops/user-guide/devops-alm-overview
