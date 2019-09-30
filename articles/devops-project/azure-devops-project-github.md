---
title: チュートリアル:Azure DevOps Projects を使用して既存のコードの CI/CD パイプラインを作成する
description: Azure DevOps Projects を利用すると、Azure を使い始めるのが簡単になります。 DevOps Projects により、独自のコードと GitHub リポジトリを使用して、いくつかの簡単な手順で任意の Azure サービス上のアプリを起動できるようになります。
services: vsts
documentationcenter: vs-devops-build
ms.author: mlearned
ms.manager: gwallace
editor: ''
ms.assetid: ''
ms.workload: web
ms.prod: devops
ms.technology: devops-cicd
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 07/09/2018
author: mlearned
ms.custom: mvc
monikerRange: vsts
ms.openlocfilehash: 2abe24ad65e1e8997b48a28b35ec0e65162022f2
ms.sourcegitcommit: 3e7646d60e0f3d68e4eff246b3c17711fb41eeda
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/11/2019
ms.locfileid: "70898006"
---
# <a name="tutorial-create-a-cicd-pipeline-for-your-existing-code-by-using-azure-devops-projects"></a>チュートリアル:Azure DevOps Projects を使用して既存のコードの CI/CD パイプラインを作成する

Azure DevOps Projects によって提供される簡略化されたエクスペリエンスを使用すると、既存のコードと Git リポジトリを使用するか、サンプル アプリケーションを選択して、Azure への継続的インテグレーション (CI) と継続的デリバリー (CD) のパイプラインを作成することができます。

このチュートリアルの内容は次のとおりです。

> [!div class="checklist"]
> * DevOps Projects を使用して CI/CD パイプラインを作成する
> * GitHub リポジトリへのアクセスを構成し、フレームワークを選択する
> * Azure DevOps と Azure サブスクリプションを構成する 
> * 変更を GitHub にコミットし、Azure に自動的にデプロイする
> * Azure Pipelines CI/CD パイプラインを調べる
> * Azure Application Insights の監視を構成する
> * リソースのクリーンアップ

## <a name="prerequisites"></a>前提条件

* Azure サブスクリプション。 [Visual Studio Dev Essentials](https://visualstudio.microsoft.com/dev-essentials/) を通じて無料で取得できます。
* GitHub、または .NET、Java、PHP、Node、Python、静的 Web コードのいずれかを含む外部 Git リポジトリにアクセスします。

## <a name="sign-in-to-the-azure-portal"></a>Azure portal にサインインする

Azure DevOps Projects によって、Azure Pipelines に CI/CD パイプラインが作成されます。 新しい Azure DevOps 組織を作成するか、既存の組織を使用できます。 Azure DevOps Projects では、選択した Azure サブスクリプションに Azure リソースも作成されます。

1. [Azure Portal](https://portal.azure.com) にサインインします。

1. 左側のウィンドウで、 **[新規作成]** を選択します。

1. 検索ボックスに「**DevOps Projects**」と入力し、 **[作成]** を選択します。

    ![DevOps Projects ダッシュボード](_img/azure-devops-project-github/fullbrowser.png)

1. **[独自のコードを使用する]** を選択し、 **[次へ]** を選択します。

## <a name="configure-access-to-your-github-repo-and-choose-a-framework"></a>GitHub リポジトリへのアクセスを構成し、フレームワークを選択する

1. **GitHub** または外部の Git リポジトリを選択し、リポジトリと、アプリケーションが含まれているブランチを選択します。

1. Web フレームワークを選択し、 **[次へ]** を選択します。

    ![.NET Framework](_img/azure-devops-project-github/webframework.png)

    前に選択したアプリケーション フレームワークによって、ここで使用可能な Azure サービスのデプロイ ターゲットの種類が決まります。 
    
1. 目的のサービスを選択し、 **[次へ]** を選択します。

## <a name="configure-azure-devops-and-an-azure-subscription"></a>Azure DevOps と Azure サブスクリプションを構成する 

1. 新しい Azure DevOps 組織を作成するか、既存の組織を選択します。

    a. Azure DevOps のプロジェクトの名前を入力します。 
    
    b. Azure サブスクリプションと場所を選択し、アプリケーションの名前を入力して、 **[完了]** を選択します。

    数分後、DevOps Projects ダッシュボードが Azure portal に表示されます。 サンプル アプリケーションが Azure DevOps 組織内のリポジトリに設定され、ビルドが実行され、アプリケーションが Azure にデプロイされます。 このダッシュボードでは、GitHub コード リポジトリ、CI/CD パイプライン、および Azure のアプリケーションが可視化されます。 
    
1. **[参照]** を選択すると、実行中のアプリケーションが表示されます。

    ![DevOps Projects ダッシュボードのビュー](_img/azure-devops-project-github/dashboardnopreview.png) 
    
Azure DevOps Projects によって、CI ビルドおよびリリース トリガーが自動的に構成されます。 コードは、GitHub リポジトリまたはその他の外部リポジトリに残ります。 

## <a name="commit-changes-to-github-and-automatically-deploy-them-to-azure"></a>変更を GitHub にコミットし、Azure に自動的にデプロイする 

Web サイトに最新の作業を自動的にデプロイする CI/CD プロセスを使用して、アプリに対してチームで共同作業を行う準備ができました。 GitHub リポジトリに対する各変更により、Azure DevOps でビルドが開始され、CD パイプラインによって Azure へのデプロイが実行されます。

1. アプリケーションに変更を加え、変更を GitHub リポジトリにコミットします。  
    しばらくすると、Azure Pipelines でビルドが開始されます。 ビルドの状態を、DevOps Projects ダッシュボードで監視するか、ブラウザーで Azure DevOps 組織を使用して監視することができます。

1. ビルドが完了したら、アプリケーションを更新して、変更を確認します。

## <a name="examine-the-azure-pipelines-cicd-pipeline"></a>Azure Pipelines CI/CD パイプラインを調べる

Azure DevOps Projects によって、Azure Pipelines に CI/CD パイプラインが自動的に構成されます。 パイプラインを探索し、必要に応じてカスタマイズします。 ビルドおよびリリース パイプラインについて理解するには、次の手順を行います。

1. DevOps Projects ダッシュボードの上部の **[ビルド パイプライン]** を選択します。  
    ブラウザーのタブに、新しいプロジェクトのビルド パイプラインが表示されます。

1. **[状態]** フィールドをポイントして、省略記号 (...) を選択します。  
    メニューには、新しいビルドをキューに入れる、ビルドを一時停止する、ビルド パイプラインを編集するなど、いくつかのオプションが表示されます。

1. **[編集]** を選択します。

1. このウィンドウで、ビルド パイプラインのさまざまなタスクを調べることができます。  
    ビルドでは、Git リポジトリからのソースのフェッチ、依存関係の復元、デプロイに使用した出力の発行など、さまざまなタスクが実行されます。

1. ビルド パイプラインの上部で、ビルド パイプラインの名前を選択します。

1. ビルド パイプラインの名前をよりわかりやすい名前に変更し、 **[保存してキューに登録]** を選択して、 **[保存]** を選択します。

1. ご自身のビルド パイプラインの名前の下で、 **[履歴]** を選択します。  
    ビルドの最近の変更の監査証跡が表示されます。 ビルド パイプラインに対するすべての変更が Azure DevOps によって追跡されるため、各バージョンを比較できます。

1. **[トリガー]** を選択します。  
    Azure DevOps Projects では、CI トリガーが自動的に作成され、リポジトリに対してコミットするたびに新しいビルドが開始されます。 必要に応じて、CI プロセスのブランチを含めるか除外するかを選択できます。

1. **[保持]** を選択します。  
        シナリオに基づいて、特定の数のビルドを保持または削除するポリシーを指定できます。

1. **[ビルドとリリース]** を選択し、 **[リリース]** を選択します。  
    Azure DevOps Projects により、Azure へのデプロイを管理するリリース パイプラインが作成されます。

1. リリース パイプラインの横にある省略記号 (...) を選択し、 **[編集]** を選択します。  
    リリース パイプラインには、リリース プロセスを定義する*パイプライン*が含まれています。 
    
1. **[成果物]** で、 **[ドロップ]** を選択します。  
    前の手順で調べたビルド パイプラインでは、成果物に使用される出力が生成されます。 

1. **[ドロップ]** アイコンの横にある **[継続的配置トリガー]** を選択します。  
    このリリース パイプラインでは、新しいビルド成果物が使用可能になるたびにデプロイを実行する有効な CD トリガーがあります。 必要に応じて、手動でのデプロイが必須となるように、トリガーを無効にすることができます。 

1. 左側の **[タスク]** を選択します。  
    タスクは、デプロイ プロセスによって実行されるアクティビティです。 この例では、Azure App Service にデプロイするタスクが作成されました。

1. 右側で **[リリースの表示]** を選択して、リリースの履歴を表示します。

1. リリースの横にある省略記号 (...) を選択し、 **[開く]** を選択します。  
    リリース概要、関連付けられた作業項目、テストなど、調べる必要があるいくつかのメニューがあります。

1. **[コミット]** を選択します。  
    このビューには、このデプロイに関連付けられているコードのコミットが表示されます。 

1. **[ログ]** を選択します。  
    ログには、デプロイ プロセスに関する有用な情報が含まれます。 それらは、デプロイ中もデプロイ後も表示されます。

## <a name="configure-azure-application-insights-monitoring"></a>Azure Application Insights の監視を構成する

Azure Application Insights を使うと、アプリケーションのパフォーマンスと利用状況を簡単に監視できます。 Azure DevOps Projects によって、アプリケーションの Application Insights リソースが自動的に構成されます。 必要に応じて、さまざまなアラートと監視機能をさらに構成できます。

1. Azure portal で、DevOps Projects ダッシュボードに移動します。 

1. 右下で、アプリの **[Application Insights]** リンクを選択します。  
    **[Application Insights]** ウィンドウが開かれます。 このビューには、アプリの利用状況、パフォーマンス、可用性の監視情報が含まれています。

    ![[Application Insights] ウィンドウ](_img/azure-devops-project-github/appinsights.png) 

1. **[時間の範囲]** を選択し、 **[過去 1 時間]** を選択します。 結果をフィルター処理するには、 **[更新]** を選択します。  
    これで、過去 60 分間のすべてのアクティビティが表示されます。 時間の範囲を終了するには、 **[x]** を選択します。

1. **[アラート]** を選択し、 **[メトリック アラートの追加]** を選択します。 

1. アラートの名前を入力します。

1. **[Source Alter on]\(変更するソース\)** ドロップダウン リストで、**App Service リソース**を選択します。 <!-- Please confirm whether this should be "Source Alter on" or "Source Alert on" -->

1. **[メトリック]** ドロップダウン リストで、さまざまなアラート メトリックを確認します。  
    既定のアラートの対象は、**1 秒を超えるサーバー応答時間**です。 アプリの監視機能を向上させるためにさまざまなアラートを簡単に構成できます。

1. **[Notify via Email owners, contributors, and readers]\(電子メール、所有者、共同作成者、および閲覧者による通知\)** チェック ボックスをオンにします。  
    必要に応じて、アラートが表示されたときに、Azure ロジック アプリを実行することによって追加のアクションを実行できます。

1. **[OK]** を選択してアラートを作成します。  
    しばらくして、アラートがダッシュボードにアクティブとして表示されます。
    
1. **[アラート]** 領域を終了し、 **[Application Insights]** ウィンドウに戻ります。

1. **[可用性]** を選択し、 **[テストの追加]** を選択します。 

1. テスト名を入力し、 **[作成]** を選択します。  
    アプリケーションの可用性を確認する簡単な ping テストが作成されます。 数分後にテスト結果が使用可能になり、Application Insights ダッシュボードに可用性の状態が表示されます。

## <a name="clean-up-resources"></a>リソースのクリーンアップ

このチュートリアルで作成した Azure App サービスと関連リソースが必要なくなったら、削除してかまいません。 そうするには、DevOps Projects ダッシュボードで**削除**機能を使用します。

## <a name="next-steps"></a>次の手順

このチュートリアルで CI/CD プロセスを構成したときに、ビルドとリリース パイプラインが Azure DevOps Projects に自動的に作成されました。 チームのニーズを満たすようにこれらのビルドおよびリリース パイプラインを変更できます。 以下の方法について学習しました。

> [!div class="checklist"]
> * DevOps Projects を使用して CI/CD パイプラインを作成する
> * GitHub リポジトリへのアクセスを構成し、フレームワークを選択する
> * Azure DevOps と Azure サブスクリプションを構成する 
> * 変更を GitHub にコミットし、Azure に自動的にデプロイする
> * Azure Pipelines CI/CD パイプラインを調べる
> * Azure Application Insights の監視を構成する
> * リソースのクリーンアップ

CI/CD パイプラインの詳細については、以下を参照してください。

> [!div class="nextstepaction"]
> [複数ステージの継続的デプロイ (CD) パイプラインを定義する](https://docs.microsoft.com/azure/devops/pipelines/release/define-multistage-release-process?view=vsts)
