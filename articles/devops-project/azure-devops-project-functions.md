---
title: チュートリアル:Azure DevOps Starter を使用して Azure Functions に ASP.NET アプリをデプロイする
description: Azure DevOps Starter を利用すると、Azure を使い始めるのが簡単になります。 DevOps Starter を使用することによって、いくつかの簡単な手順で ASP.NET アプリを Azure Functions にデプロイできます。
ms.author: mlearned
ms.manager: gwallace
ms.prod: devops
ms.technology: devops-cicd
ms.topic: tutorial
ms.date: 03/24/2020
author: mlearned
ms.custom: devx-track-csharp
ms.openlocfilehash: 3277fefb5615fd492b2859b741ef8e6f991eaf23
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "91355476"
---
# <a name="deploy-to-azure-functions-with-devops-starter"></a>DevOps Starter を使用して Azure Functions にデプロイする

Azure DevOps Starter によって提供される簡略化されたエクスペリエンスを使用すると、既存のコードと Git リポジトリを使用するか、サンプル アプリケーションを選択して、Azure への継続的インテグレーション (CI) と継続的デリバリー (CD) のパイプラインを作成することができます。

DevOps Starter では次のこともできます。

* Azure Functions などの Azure リソースを自動的に作成する

* CI/CD 用に Azure DevOps にリリース パイプラインを作成し、構成する

このチュートリアルでは、次のことについて説明します。

> [!div class="checklist"]
>* DevOps Starter を使用して、ASP.NET アプリを Azure 関数にデプロイする
>* Azure DevOps と Azure サブスクリプションを構成する
>* Azure 関数を調べる
>* CI パイプライン を確認する
>* CD パイプライン を確認する
>* 変更を Git にコミットし、Azure に自動的にデプロイする
>* リソースをクリーンアップする

現在、関数用にサポートされているランタイムは **.NET** と **Node.js** です。 このチュートリアルでは、 .NET ランタイムを使用して、Azure Functions へのデプロイを行います。 

## <a name="prerequisites"></a>前提条件

* Azure サブスクリプション。 [Visual Studio Dev Essentials](https://visualstudio.microsoft.com/dev-essentials/) を通じて無料で取得できます

## <a name="use-devops-starter-to-deploy-an-aspnet-app-to-azure-functions"></a>DevOps Starter を使用して、ASP.NET アプリを Azure Functions にデプロイする

DevOps Starter によって、Azure Pipelines に CI/CD パイプラインが作成されます。 新しい Azure DevOps 組織を作成するか、既存の組織を使用できます。 DevOps Projects によって、選択した Azure サブスクリプションに IoTHub などの Azure リソースも作成されます。

1. [Azure ポータル](https://portal.azure.com)

1. 検索ボックスに「**DevOps Starter**」と入力して選択します。 新しく作成するには、 **[追加]** をクリックします。

    ![DevOps Starter ダッシュボード](_img/azure-devops-starter-aks/search-devops-starter.png)

1. **[.NET]** を選択し、 **[次へ]** を選択します。 **[アプリケーション フレームワークを選択します]** で **[ASP.NET]** を選択し、 **[次へ]** をクリックします。

1. **[Function App]** を選択し、 **[次へ]** を選択します。

## <a name="configure-azure-devops-and-azure-subscription"></a>Azure DevOps と Azure サブスクリプションを構成する

1. Azure DevOps プロジェクトの名前を入力します。

1. 新しい Azure DevOps 組織を作成するか、既存の組織を選択します。

1. Azure サブスクリプションを選択します。

1. 追加の Azure 構成設定を表示し、価格レベルと場所を特定するには、 **[追加設定]** をクリックします。 このウィンドウには、Azure サービスの価格レベルと場所を構成するためのさまざまなオプションが表示されます。

1. Azure の構成領域を終了し、 **[完了]** を選択します。

1. 数分後に、処理が完了します。 サンプル ASP.NET アプリが Azure DevOps 組織内の Git リポジトリに設定され、関数アプリと Application Insights が作成され、CI/CD パイプラインが実行され、アプリが Azure にデプロイされます。

   このすべてが完了すると、Azure DevOps Starter ダッシュボードが Azure portal に表示されます。 DevOps Starter ダッシュボードには、Azure portal の **[すべてのリソース]** から直接移動することもできます。

   このダッシュボードでは、Azure DevOps コード リポジトリ、CI/CD パイプライン、および Azure 関数が可視化されます。 Azure DevOps パイプラインで追加の CI/CD オプションを構成できます。 右側で、表示する **関数アプリ** を選択します。

## <a name="examine-the-function-app"></a>関数アプリを調べる

DevOps Starter によって、関数アプリが自動的に構成されます。それを調べて、カスタマイズすることができます。 関数アプリを把握するには、次の手順のようにします。

1. DevOps Starter ダッシュボードに移動します。

    ![DevOps Projects ダッシュボード](_img/azure-devops-project-functions/fapp-dashboard.png)

1. 右側で、関数アプリを選択します。 関数アプリのウィンドウが開きます。 このビューから、操作監視やログの検索など、さまざまなアクションを実行できます。

    ![Function App](_img/azure-devops-project-functions/function-app.png)

## <a name="examine-the-ci-pipeline"></a>CI パイプライン を確認する

DevOps Starter によって、Azure DevOps 組織内に CI/CD パイプラインが自動的に構成されます。 パイプラインを探索し、カスタマイズできます。 これについて理解するには、次の手順に従います。

1. DevOps Starter ダッシュボードに移動します。

1. **[ビルド]** の下のハイパーリンクをクリックします。 ブラウザーのタブに、新しいプロジェクトのビルド パイプラインが表示されます。

    ![Azure portal の DevOps Starter ダッシュボードの、CI/CD パイプラインの [ビルド] の下にあるリンクを指し示す矢印を示すスクリーンショット。](_img/azure-devops-project-functions/build.png)

1. **[編集]** を選択します。 このウィンドウで、ビルド パイプラインのさまざまなタスクを調べることができます。 ビルドでは、Git リポジトリからのソース コードのフェッチ、アプリケーションのビルド、単体テストの実行、デプロイに使用される出力の発行など、さまざまなタスクが実行されます。

1. **[トリガー]** を選択します。 DevOps Starter では、CI トリガーが自動的に作成され、リポジトリに対してコミットするたびに新しいビルドが開始されます。 必要に応じて、CI プロセスのブランチを含めるか除外するかを選択できます。

1. **[保持]** を選択します。 シナリオに基づいて、特定の数のビルドを保持または削除するポリシーを指定できます。

1. ビルド パイプラインの上部で、ビルド パイプラインの名前を選択します。

1. ビルド パイプラインの名前をよりわかりやすい名前に変更し、 **[保存してキューに登録]** ドロップダウンから **[保存]** を選択します。

1. ご自身のビルド パイプラインの名前の下で、 **[履歴]** を選択します。 このウィンドウには、ビルドに対する最近の変更の監査証跡が表示されます。 ビルド パイプラインに対するすべての変更が Azure DevOps によって追跡されるため、各バージョンを比較できます。

## <a name="examine-the-cd-release-pipeline"></a>CD リリース パイプラインを調べる

DevOps Starter では、Azure DevOps 組織から Azure サブスクリプションにデプロイするために必要な手順が自動的に作成され、構成されます。 これらの手順には、Azure サブスクリプションで Azure DevOps を認証するための Azure サービス接続の構成が含まれます。 自動化によってリリース パイプラインも作成され、このパイプラインによって CD が Azure に提供されます。 リリース パイプラインの詳細を知るには、次の手順を行います。

1. **[パイプライン] > [リリース]** に移動します。

1. **[Edit]\(編集\)** をクリックします。

1. **[成果物]** で、 **[ドロップ]** を選択します。 前の手順で調べたビルド パイプラインでは、成果物に使用される出力が生成されます。

1. **[ドロップ]** アイコンの右側にある **[継続的配置トリガー]** を選択します。 このリリース パイプラインには、新しいビルド成果物が使用可能になるたびにデプロイを実行する有効な CD トリガーがあります。 必要に応じて、手動でのデプロイが必須となるように、トリガーを無効にすることができます。

1. 右側で **[リリースの表示]** を選択して、リリースの履歴を表示します。

1. リリースをクリックします。パイプラインが表示されます。 任意の環境をクリックして、リリースの **[概要]、[コミット]** 、関連する **[作業項目]** を確認します。

1. **[コミット]** を選択します。 このビューには、このデプロイに関連付けられているコードのコミットが表示されます。 リリースを比較して、デプロイ間のコミットの相違を表示します。

1. **[ログの表示]** を選択します。 ログには、デプロイ プロセスに関する有用な情報が含まれます。 それらは、デプロイ中もデプロイ後も表示されます。

## <a name="commit-code-changes-and-execute-cicd"></a>コードの変更をコミットし、CI/CD を実行する

> [!NOTE]
> 次の手順では、単純なテキストの変更を行って、CI/CD パイプラインをテストします。

お使いの Azure 関数に最新の作業を自動的にデプロイする CI/CD プロセスを使用して、アプリに対してチームで共同作業を行う準備ができました。 Git リポジトリに対する各変更により、Azure DevOps でビルドが開始され、CD パイプラインによって Azure へのデプロイが実行されます。 このセクションの手順に従うか、他の手法を使用して、リポジトリに変更をコミットします。 たとえば、お気に入りのツールや IDE で Git リポジトリを複製し、変更をそのリポジトリにプッシュできます。

1. Azure DevOps メニューで、 **[Repos]\(リポジトリ\) > [ファイル]** の順に選択し、対象のリポジトリに移動します。

1. リポジトリには、作成プロセスで選択したアプリケーションの言語に基づいて、**SampleFunctionApp** というコードが既に含まれています。 **Application/SampleFunctionApp/Function1.cs** ファイルを開きます。

1. **[編集]** を選択した後、**31 行目** を変更します。 たとえば、「**Hello there!Welcome to Azure Functions using DevOps Starter**」に更新します

1. 右上の **[コミット]** を選択し、もう一度 **[コミット]** を選択して、変更をプッシュします。

1. **Application/SampleFunctionApp.Test/Function1TestRunner.cs** ファイルを開きます。 

1. **[編集]** を選択した後、**21 行目** を変更します。 たとえば、「**Hello there!Welcome to Azure Functions using Azure DevOps Starter**」に更新します。

     しばらくすると Azure DevOps でビルドが開始され、リリースが実行されて、変更がデプロイされます。 DevOps Starter ダッシュボードで、またはブラウザーで Azure DevOps 組織を使用して、ビルドの状態を監視します。

## <a name="clean-up-resources"></a>リソースをクリーンアップする

作成した関連リソースが必要なくなったら、削除してかまいません。 DevOps Starter ダッシュボードで **削除** 機能を使用します。

## <a name="next-steps"></a>次のステップ

必要に応じて、チームのニーズを満たすようにこれらのビルドおよびリリース パイプラインを変更できます。 この CI/CD パターンをご自身の他のパイプラインのテンプレートとして使用することもできます。 このチュートリアルでは、以下の内容を学習しました。

> [!div class="checklist"]
> * DevOps Starter を使用して、ASP.NET Core アプリを Azure 関数にデプロイする
> * Azure DevOps と Azure サブスクリプションを構成する 
> * Azure 関数を調べる
> * CI パイプライン を確認する
> * CD パイプライン を確認する
> * 変更を Git にコミットし、Azure に自動的にデプロイする
> * リソースをクリーンアップする

