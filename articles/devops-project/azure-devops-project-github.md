---
title: 'チュートリアル: Azure DevOps Starter を使用して既存のコードの CI/CD パイプラインを作成する'
description: Azure DevOps Starter を利用すると、Azure を使い始めるのが簡単になります。 DevOps Projects により、独自のコードと GitHub リポジトリを使用して、いくつかの簡単な手順で Azure サービス上にアプリを立ち上げることができます。
services: vsts
documentationcenter: vs-devops-build
ms.author: mlearned
ms.manager: gwallace
ms.workload: web
ms.prod: devops
ms.technology: devops-cicd
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 03/24/2020
author: mlearned
ms.custom: mvc
ms.openlocfilehash: c03ba657ac264b72d035f28956354398421ed2f9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "102566602"
---
# <a name="create-a-cicd-pipeline-for-github-repo-using-azure-devops-starter"></a>Azure DevOps Starter を使用して GitHub リポジトリ用の CI/CD パイプラインを作成する

Azure DevOps Starter は、Azure に対する継続的インテグレーション (CI) と継続的デリバリー (CD) のパイプラインを作成するプロセスを簡素化します。 既存のコードと Git リポジトリを持ち込むことができるほか、サンプル アプリケーションを選択することもできます。

次の処理を行います。

> [!div class="checklist"]
> * DevOps Starter を使用して CI/CD パイプラインを作成する
> * GitHub リポジトリへのアクセスを構成し、フレームワークを選択する
> * Azure DevOps と Azure サブスクリプションを構成する 
> * 変更を GitHub にコミットし、Azure に自動的にデプロイする
> * Azure Pipelines CI/CD パイプラインを調べる
> * リソースをクリーンアップする

## <a name="prerequisites"></a>前提条件

* Azure サブスクリプション。 [Visual Studio Dev Essentials](https://visualstudio.microsoft.com/dev-essentials/) を通じて無料で取得できます。
* GitHub、または .NET、Java、PHP、Node.js、Python、静的 Web コードのいずれかを含む外部 Git リポジトリにアクセスします。

## <a name="sign-in-to-the-azure-portal"></a>Azure portal にサインインする

Azure DevOps Starter によって、Azure Pipelines に CI/CD パイプラインが作成されます。 新しい Azure DevOps 組織を作成するか、既存の組織を使用できます。 Azure DevOps Starter では、選択した Azure サブスクリプションに Azure リソースも作成されます。

1. [Azure portal](https://portal.azure.com) にサインインします。

1. 検索ボックスに「**DevOps Starter**」と入力して選択します。 新しく作成するには、 **[追加]** をクリックします。

    ![DevOps Starter ダッシュボード](_img/azure-devops-starter-aks/search-devops-starter.png)
    
1. **[独自のコードを使用する]** を選択し、**[次へ]** を選択します。

## <a name="configure-access-to-your-github-repo-and-select-a-framework"></a>GitHub リポジトリへのアクセスを構成し、フレームワークを選択する

1. **GitHub** または外部の **Git** コード リポジトリを選択します。 このチュートリアルでは、 **[GitHub]** を選択します。 GitHub リポジトリへのアクセスを Azure に許可するため、初回は GitHub に対する認証が要求される場合があります。

1. **[リポジトリ]** と **[ブランチ]** を選択し、**[次へ]** を選択します。

1. Docker コンテナーを使用している場合は、**[Docker でコンテナー化するアプリ]** を **[はい]** に変更します。 このチュートリアルでは **[いいえ]** が選択されたまま **[次へ]** を選択します。 Docker コンテナーの使用の詳細については、 **[i]** アイコンにマウス ポインターを合わせてください。

   ![ドロップダウン メニューでのアプリケーション フレームワークの選択](_img/azure-devops-project-github/appframework.png)

1. ドロップダウン メニューから **アプリケーション ランタイム** と **アプリケーション フレームワーク** を選択し、**[次へ]** を選択します。 アプリケーション フレームワークによって、使用可能な Azure サービスのデプロイ ターゲットの種類が決まります。

1. アプリケーションをデプロイする **Azure サービス** を選択し、**[次へ]** を選択します。

## <a name="configure-azure-devops-and-an-azure-subscription"></a>Azure DevOps と Azure サブスクリプションを構成する

1. **[プロジェクト名]** に名前を入力します。

1. **[Azure DevOps 組織]** で新しい無料の組織を作成するか、ドロップダウン メニューから既存の組織を選択します。

1. **[Azure サブスクリプション]** でサブスクリプションを選択し、**[Web アプリ]** に名前を入力するか、または既定値を使用します。 **[場所]** を選択し、**[完了]** を選択します。 数分後、DevOps Starter のデプロイの概要が Azure portal に表示されます。

1. **[リソースに移動]** を選択して、DevOps Starter ダッシュボードを表示します。 すばやくアクセスできるように、右上隅で、**プロジェクト** をダッシュ​​ボードにピン留めします。 Azure DevOps Starter によって、CI ビルドおよびリリース トリガーが自動的に構成されます。 コードは、GitHub リポジトリまたはその他の外部リポジトリに残り、サンプル アプリが **Azure DevOps 組織** のリポジトリに設定されます。 Azure DevOps Starter によってビルドが実行され、アプリが Azure にデプロイされます。

   ![Azure DevOps Projects ダッシュボードのビュー](_img/azure-devops-project-github/projectsdashboard.png)

1. ダッシュボードには、コード リポジトリ、CI/CD パイプライン、および Azure のアプリが表示されます。 右側の Azure リソースの下の **[参照]** を選択すると、実行中のアプリが表示されます。

## <a name="commit-changes-to-github-and-automatically-deploy-them-to-azure"></a>変更を GitHub にコミットし、Azure に自動的にデプロイする

これでアプリの共同作業をチームで行う準備が整いました。 最新の作業が、CI/CD プロセスによって自動的に Web サイトにデプロイされます。 GitHub リポジトリに対する各変更により、Azure DevOps でビルドが開始され、CD パイプラインによって Azure へのデプロイが実行されます。

1. DevOps Starter ダッシュボードから **[リポジトリ]** を選択します。 ご利用の GitHub リポジトリが新しいブラウザー タブに表示されます。アプリケーションに変更を加え、 **[変更のコミット]** を選択します。

1. しばらくすると、Azure Pipelines でビルドが開始されます。 ビルドの状態は、DevOps Starter ダッシュボードで監視できます。 または、自分の Azure DevOps 組織で、DevOps Starter ダッシュボードの **[ビルド パイプライン]** タブを選択して監視することもできます。

## <a name="examine-the-azure-pipelines-cicd-pipeline"></a>Azure Pipelines CI/CD パイプラインを調べる

Azure DevOps Starter によって、Azure Pipelines に CI/CD パイプラインが自動的に構成されます。 パイプラインを探索し、必要に応じてカスタマイズします。 ビルドおよびリリース パイプラインについて理解するには、次の手順を行います。

1. DevOps Starter ダッシュボードで、 **[ビルド パイプライン]** を選択します。

1. **[Azure Pipelines]** ページが開いて、最新のビルドの履歴と各ビルドの状態が表示されます。

   ![Azure Pipelines の [ビルド] ページ](_img/azure-devops-project-github/pipelinesbuildpage.png)

1. **[ビルド]** ページの右上隅では、**[編集]** を選択して現在のビルドに変更を加えたり、**[キュー]** を選択して新しいビルドを追加したり、省略記号ボタン (**&#8942;**) を選択してその他のオプションを含んだメニューを表示したりすることができます。 **[編集]** を選択します。

1. ビルドでは、リポジトリからのソースのフェッチ、依存関係の復元、デプロイに使用した出力の発行など、さまざまなタスクが実行されます。 右側の **[名前]** で、ビルド パイプラインの名前をもっとわかりやすい名前に変更します。 **[保存してキューに登録]** を選択し、 **[保存]** を選択します。 コメントを入力してから、もう一度 **[保存]** を選択します。

   ![Azure DevOps の [ビルド] ページ](_img/azure-devops-project-github/buildpage.png)

1. ビルドの最近の変更の監査証跡を表示するには、 **[履歴]** タブを選択します。ビルド パイプラインに対するすべての変更が Azure DevOps によって追跡され、バージョンを比較できます。

1. **[トリガー]** タブを選択します。Azure DevOps Projects によって、いくつかの既定の設定を使用して CI トリガーが自動的に作成されます。 **[継続的インテグレーションを有効にする]** などのトリガーを設定することで、コードの変更をコミットするたびにビルドを実行できます。 トリガーを設定して、ビルドを特定の時刻に実行するようにスケジュールすることもできます。

## <a name="clean-up-resources"></a>リソースをクリーンアップする

このチュートリアルで作成した Azure App Service とその関連リソースは、不要になったら削除してかまいません。 DevOps Projects ダッシュボードで **削除** 機能を使用します。

## <a name="next-steps"></a>次のステップ

このチュートリアルでは、CI/CD プロセスを構成したときに、ビルドとリリース パイプラインが Azure DevOps Projects に自動的に作成されました。 チームのニーズを満たすようにこれらのビルドおよびリリース パイプラインを変更できます。

CI/CD パイプラインの詳細については、以下を参照してください。

> [!div class="nextstepaction"]
> [複数ステージの継続的デプロイ (CD) パイプラインを定義する](/azure/devops/pipelines/release/define-multistage-release-process)

アプリケーションの監視の詳細については、次のページを参照してください。
  
 > [!div class="nextstepaction"]
 > [Azure Monitor とは](../azure-monitor/overview.md)