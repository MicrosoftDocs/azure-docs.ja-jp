---
title: 'チュートリアル: Azure DevOps Starter を使用して、Azure Cosmos DB を使用する Node.js アプリをデプロイする'
description: Azure DevOps Starter を利用すると、Azure を使い始めるのが簡単になります。 DevOps Starter を使用すると、Azure Cosmos DB を使用する Node.js アプリを簡単な手順で Windows Web アプリにデプロイできます。
ms.author: mlearned
ms.manager: gwallace
ms.prod: devops
ms.technology: devops-cicd
ms.topic: tutorial
ms.date: 03/24/2020
author: mlearned
ms.custom: devx-track-js
ms.openlocfilehash: d1bdae976d5e5b7d98311d33bb38b5035f2557ca
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102548701"
---
# <a name="deploy-nodejs-apps-powered-by-azure-cosmos-db-with-devops-starter"></a>DevOps Starter を使用して、Azure Cosmos DB を使用する Node.js アプリをデプロイする

Azure DevOps Starter によって提供される合理化されたエクスペリエンスを使用すると、Azure への継続的インテグレーション (CI) と継続的デプロイ (CD) のパイプラインを作成することができます。 これは、既存のコードと Git リポジトリを使用するか、またはサンプル アプリケーションを選択することによって行います。

DevOps Starter では次のこともできます。

* Azure Cosmos DB、Azure Application Insights、Azure App Service、App Service プランなど、Azure のリソースを自動的に作成する

* Azure DevOps に CI/CD リリース パイプラインを作成し、構成する

このチュートリアルでは、次の作業を行います。

> [!div class="checklist"]
> * DevOps Starter を使用して Azure Cosmos DB を使用する Node.js アプリをデプロイする
> * Azure DevOps と Azure サブスクリプションを構成する
> * Azure Cosmos DB を確認する
> * CI パイプライン を確認する
> * CD パイプライン を確認する
> * 変更を Git にコミットし、Azure に自動的にデプロイする
> * リソースのクリーンアップ

## <a name="prerequisites"></a>前提条件

Azure サブスクリプションが必要です。Azure サブスクリプションは、[Visual Studio Dev Essentials](https://visualstudio.microsoft.com/dev-essentials/) を通じて無料で入手できます。

## <a name="use-devops-starter-to-deploy-nodejs-app"></a>DevOps Starter を使用して、Node.js アプリをデプロイする

DevOps Starter によって、Azure Pipelines に CI/CD パイプラインが作成されます。 新しい Azure DevOps 組織を作成するか、既存の組織を使用できます。 DevOps Starter では、Azure Cosmos DB、Application Insights、App Service、App Service プランなどの Azure リソースも、選択した Azure サブスクリプションに作成されます。

1. [Azure portal](https://portal.azure.com) にサインインします。

1. 検索ボックスに「**DevOps Starter**」と入力して選択します。 新しく作成するには、 **[追加]** をクリックします。

    ![DevOps Starter ダッシュボード](_img/azure-devops-starter-aks/search-devops-starter.png)

1. ランタイムとして **[Node.js]** を選択し、次に **[次へ]** を選択します。 **[アプリケーション フレームワークを選択します]** で、**[Express.js]** を選択します。

1. **[Cosmos DB]** に対して **[データベースを追加する]** セクションを有効にし、**[次へ]** を選択します。

    ![データベースを追加する](_img/azure-devops-project-cosmos-db/add-database.png)

    Azure DevOps Starter では、**Express.js**、**サンプル Node.js アプリ**、**Sail.js** などさまざまなアプリケーション フレームワークがサポートされます。 このチュートリアルでは、**Express.js** を使用します。

1. アプリケーションをデプロイする Azure サービスを選択し、**[次へ]** を選択します。 Windows Web アプリ、Azure Kubernetes Service、Azure Web App for Containers などを選択できます。 このチュートリアルでは、**Windows Web アプリ** を使用します。

## <a name="configure-azure-devops-and-azure-subscription"></a>Azure DevOps と Azure サブスクリプションを構成する

1. Azure DevOps プロジェクトの名前を入力します。

1. 新しい Azure DevOps 組織を作成するか、既存の組織を選択します。

1. Azure サブスクリプションを選択します。

1. 追加の Azure 構成設定を表示し、価格レベルと場所を特定するには、**[追加設定]** を選択します。 このウィンドウには、Azure サービスの価格レベルと場所を構成するためのさまざまなオプションが表示されます。

1. Azure の構成領域を終了し、 **[完了]** を選択します。

1. 数分後に、処理が完了します。 サンプル Node.js アプリが Azure DevOps 組織の Git リポジトリに設定されます。 次に、Azure Cosmos DB、App Service、App Service プラン、Application Insights の各リソースに加え、CI/CD パイプラインが作成されます。 その後、アプリが Azure にデプロイされます。

   これらの処理がすべて完了すると、Azure DevOps Starter ダッシュボードが Azure portal に表示されます。 DevOps Starter ダッシュボードには、Azure portal の **[すべてのリソース]** から直接移動することもできます。

   このダッシュボードによって、Azure DevOps コード リポジトリ、CI/CD パイプライン、Azure Cosmos DB データベースが可視化されます。 Azure DevOps パイプラインで追加の CI/CD オプションを構成できます。 ダッシュボードの右側の **[Azure Cosmos DB]** を選択すると、それらのオプションが表示されます。

## <a name="examine-azure-cosmos-db"></a>Azure Cosmos DB を確認する

DevOps Starter によって、Azure Cosmos DB が自動的に構成されます。これを調べて、カスタマイズすることができます。 Azure Cosmos DB について理解するには、次の手順に従います。

1. DevOps Starter ダッシュボードに移動します。

    ![DevOps Projects ダッシュボード](_img/azure-devops-project-cosmos-db/devops-starter-dashboard.png)

1. 右側の [Azure Cosmos DB] を選択します。 Azure Cosmos DB のウィンドウが開きます。 このビューから、操作の監視やログの検索など、さまざまなアクションを実行できます。

    ![Azure Cosmos DB ウィンドウ](_img/azure-devops-project-cosmos-db/cosmos-db.png)

## <a name="examine-the-ci-pipeline"></a>CI パイプライン を確認する

DevOps Starter によって、Azure DevOps 組織内に CI/CD パイプラインが自動的に構成されます。 パイプラインを探索し、カスタマイズできます。 これについて理解するには、次の手順に従います。

1. DevOps Starter ダッシュボードに移動します。

1. **[ビルド]** の下のハイパーリンクを選択します。 ブラウザーのタブに、新しいプロジェクトのビルド パイプラインが表示されます。

    ![ビルド ウィンドウ](_img/azure-devops-project-cosmos-db/build.png)

1. **[編集]** を選択します。 このウィンドウで、ビルド パイプラインのさまざまなタスクを調べることができます。 ビルドでは、Git リポジトリからのソース コードのフェッチ、アプリケーションのビルド、単体テストの実行、デプロイに使用される出力の発行など、さまざまなタスクが実行されます。

1. **[トリガー]** を選択します。 DevOps Starter では、CI トリガーが自動的に作成され、リポジトリに対してコミットするたびに新しいビルドが開始されます。 CI プロセスのブランチを含めるか除外するかを選択できます。

1. **[保持]** を選択します。 シナリオに基づいて、特定の数のビルドを保持または削除するポリシーを指定できます。

1. ビルド パイプラインの上部で、ビルド パイプラインの名前を選択します。

1. ビルド パイプラインの名前をよりわかりやすい名前に変更し、 **[保存してキューに登録]** ドロップダウンから **[保存]** を選択します。

1. ご自身のビルド パイプラインの名前の下で、 **[履歴]** を選択します。 このウィンドウには、ビルドに対する最近の変更の監査証跡が表示されます。 ビルド パイプラインに対するすべての変更が Azure DevOps によって追跡されるため、各バージョンを比較できます。

## <a name="examine-the-cd-release-pipeline"></a>CD リリース パイプラインを調べる

DevOps Starter では、Azure DevOps 組織から Azure サブスクリプションにデプロイするために必要な手順が自動的に作成され、構成されます。 これらの手順には、Azure サブスクリプションで Azure DevOps を認証するための Azure サービス接続の構成が含まれます。 自動化によってリリース パイプラインも作成され、このパイプラインによって CD が Azure に提供されます。 リリース パイプラインの詳細を知るには、次の手順を行います。

1. **[パイプライン]** に移動し、**[リリース]** を選択します。

1. **[編集]** を選択します。

1. **[成果物]** で、 **[ドロップ]** を選択します。 前の手順で調べたビルド パイプラインでは、成果物に使用される出力が生成されます。

1. **[ドロップ]** アイコンの右側にある **[継続的配置トリガー]** を選択します。 このリリース パイプラインには、新しいビルド成果物が使用可能になるたびにデプロイを実行する、有効な継続的デプロイ トリガーがあります。 このトリガーを無効にすることで、デプロイを手動で実行することができます。

1. 右側で、**[リリースの表示]** セクションを選択して、リリースの履歴を表示します。

1. リリースを選択します。パイプラインが表示されます。 任意の環境を選択して、リリースの [概要]、[コミット]、関連する [作業項目] を確認します。

1. **[コミット]** を選択します。 このビューには、このデプロイに関連付けられているコードのコミットが表示されます。 リリースを比較して、デプロイ間のコミットの相違を表示します。

1. **[ログの表示]** を選択します。 ログには、デプロイ プロセスに関する有用な情報が含まれます。 それらは、デプロイ中もデプロイ後も表示されます。

## <a name="commit-code-changes-and-execute-the-cicd-pipeline"></a>コードの変更をコミットし、CI/CD パイプラインを実行する

> [!NOTE]
> 次の手順では、単純なテキストの変更を行って、CI/CD パイプラインをテストします。

App Service に最新の作業をデプロイする CI/CD プロセスを使用して、アプリに対してチームで共同作業を行う準備ができました。 Git リポジトリに対する各変更により、Azure DevOps でビルドが開始され、CD パイプラインによって Azure へのデプロイが実行されます。 このセクションの手順に従うか、他の手法を使用して、リポジトリに変更をコミットします。 たとえば、お気に入りのツールや IDE で Git リポジトリを複製し、変更をそのリポジトリにプッシュできます。

1. Azure DevOps メニューで **[リポジトリ]** を選択し、**[ファイル]** を選択します。 リポジトリに移動します。

1. リポジトリには、作成プロセスで選択したアプリケーションの言語に基づくコードが既に含まれています。 **Application/views/index.pug** ファイルを開きます。

1. **[編集]** を選択した後、**15 行目** を変更します。 たとえば、"Azure Cosmos DB を使用した Azure App Service への最初のデプロイ" に変更できます。

1. 右上隅の **[コミット]** を選択し、もう一度 **[コミット]** を選択して、変更をプッシュします。

     数秒後に Azure DevOps でビルドが開始され、リリースが実行されて、変更がデプロイされます。 DevOps Starter ダッシュボードで、またはブラウザーで Azure DevOps 組織を使用して、ビルドの状態を監視します。

## <a name="clean-up-resources"></a>リソースをクリーンアップする

作成した関連リソースが必要なくなったら、削除してください。 DevOps Starter ダッシュボードで **削除** 機能を使用します。

## <a name="next-steps"></a>次のステップ

チームのニーズを満たすようにこれらのビルドおよびリリース パイプラインを変更できます。 この CI/CD パターンをご自身の他のパイプラインのテンプレートとして使用することもできます。 このチュートリアルでは、以下の内容を学習しました。

> [!div class="checklist"]
> * DevOps Starter を使用して Azure Cosmos DB を使用する Node.js アプリをデプロイする
> * Azure DevOps と Azure サブスクリプションを構成する 
> * Azure Cosmos DB を確認する
> * CI パイプライン を確認する
> * CD パイプライン を確認する
> * 変更を Git にコミットし、Azure に自動的にデプロイする
> * リソースをクリーンアップする

詳細および次の手順については、「[複数ステージの継続的デプロイ (CD) パイプラインを定義する](/azure/devops/pipelines/release/define-multistage-release-process)」を参照してください。