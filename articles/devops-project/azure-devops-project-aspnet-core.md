---
title: クイック スタート:Azure DevOps Starter を使用して .NET 用 CI/CD パイプラインを作成する
description: Azure DevOps Starter を利用すると、Azure を使い始めるのが簡単になります。 いくつかの簡単な手順で、任意の Azure サービス上で .NET アプリを起動できます。
ms.prod: devops
ms.technology: devops-cicd
services: azure-devops-project
documentationcenter: vs-devops-build
author: mlearned
manager: gwallace
editor: ''
ms.assetid: ''
ms.workload: web
ms.tgt_pltfrm: na
ms.topic: quickstart
ms.date: 02/23/2021
ms.author: mlearned
ms.custom: devx-track-csharp, mvc
ms.openlocfilehash: 75531b9a75c2d28ad665748a9be4e37e2056b451
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102547970"
---
# <a name="create-a-cicd-pipeline-for-net-with-azure-devops-starter"></a>Azure DevOps Starter を使用して .NET 用 CI/CD パイプラインを作成する

DevOps Starter を使用して、.NET Core または ASP.NET アプリケーション用に継続的インテグレーション (CI) と継続的デリバリー (CD) を構成します。 DevOps Starter によって、Azure Pipelines のビルドおよびリリース パイプラインの初期構成が簡略化されます。

Azure サブスクリプションをお持ちでない場合は、[Visual Studio Dev Essentials](https://visualstudio.microsoft.com/dev-essentials/) を通じて無料で取得できます。

## <a name="sign-in-to-the-azure-portal"></a>Azure portal にサインインする

DevOps Starter によって、Azure DevOps に CI/CD パイプラインが作成されます。 新しい Azure DevOps 組織を作成するか、既存の組織を使用できます。 DevOps Starter では、選択した Azure サブスクリプションに Azure リソースも作成されます。

1. [Microsoft Azure ポータル](https://portal.azure.com)にサインインします。

1. 検索ボックスに「**DevOps Starter**」と入力して選択します。 新しく作成するには、 **[追加]** をクリックします。 

    ![DevOps Starter ダッシュボード](_img/azure-devops-starter-aks/search-devops-starter.png)

## <a name="select-a-sample-application-and-azure-service"></a>サンプル アプリケーションと Azure サービスを選択する

1. **.NET** サンプル アプリケーションを選択します。 .NET のサンプルには、オープン ソースの ASP.NET Framework またはクロスプラットフォームの .NET Core Framework のいずれかの選択肢が含まれます。

   ![.NET Framework](_img/azure-devops-project-aspnet-core/select-dotnet.png)
   
   > [!NOTE]
   > DevOps Starter を設定するための既定のオプションは **GitHub** ですが、この設定はウィザードから変更できます。
2. このサンプルは、ASP.NET Core MVC アプリケーションです。 **.NET Core** アプリケーション フレームワークを選択し、 **[次へ]** を選択します。    
    
3. デプロイ ターゲットとして **[Windows Web アプリ]** を選択し、 **[次へ]** を選択します。 必要に応じて、デプロイ用に他の Azure サービスを選択できます。 前に選択したアプリケーション フレームワークによって、ここで使用可能な Azure サービスのデプロイ ターゲットの種類が決まります。

## <a name="configure-azure-devops-and-an-azure-subscription"></a>Azure DevOps と Azure サブスクリプションを構成する 

1. **プロジェクト名** を入力します。

2. 新しい無料の **Azure DevOps 組織** を作成するか、ドロップダウンから既存の組織を選択します。

3. 自分の **Azure サブスクリプション** を選択し、**Web アプリ** の名前を入力するか既定のままにしてから、 **[完了]** を選択します。 数分後、DevOps Starter のデプロイの概要が Azure portal に表示されます。 

4. **[リソースに移動]** を選択して、DevOps Starter ダッシュボードを表示します。 すばやくアクセスできるように、右上隅で、**プロジェクト** をダッシュ​​ボードにピン留めします。 サンプル アプリが **Azure DevOps 組織** のリポジトリに設定されます。 ビルドが実行され、アプリが Azure にデプロイされます。

5. ダッシュボードでは、コード リポジトリ、CI/CD パイプライン、および Azure のアプリが可視化されます。 右側の Azure リソースの下の **[参照]** を選択すると、実行中のアプリが表示されます。

   ![ダッシュボード ビュー](_img/azure-devops-project-aspnet-core/dashboardnopreview.png) 

## <a name="commit-code-changes-and-execute-cicd"></a>コードの変更をコミットし、CI/CD を実行する

DevOps Starter によって、Azure Repos または GitHub に Git リポジトリが作成されます。 リポジトリを表示し、アプリケーションにコード変更を加えるには、次の手順に従います。

1. DevOps Starter ダッシュボードの左側にある **メイン** ブランチのリンクを選択します。 このリンクは、新しく作成された Git リポジトリのビューを開きます。

2. 次のいくつかの手順では、Web ブラウザーを使用して **メイン** ブランチに直接コード変更を行い、コミットできます。 リポジトリ ページの右上の **[Clone]\(複製\)** を選択して、Git リポジトリをお気に入りの IDE に複製することもできます。 

3. 左側で、アプリケーション ファイル構造の中の **Application/aspnet-core-dotnet-core/Pages/Index.cshtml** に移動します。

4. **[編集]** を選択し、h2 見出しに変更を加えます。 たとえば、「**Azure DevOps Starter を今すぐ開始**」と入力したり、その他の変更を加えたりします。

      ![コードの編集](_img/azure-devops-project-aspnet-core/codechange.png)

5. **[コミット]** を選択し、コメントを入力してから **[コミット]** をもう一度選択します。

6. ブラウザーで Azure DevOps Starter ダッシュボードに移動します。  ビルドが進行中であることが表示されます。 行った変更は、CI/CD パイプラインを介して自動的にビルドおよびデプロイされます。

## <a name="examine-the-cicd-pipeline"></a>CI/CD パイプラインを確認する

前の手順で、Azure DevOps Starter によって完全な CI/CD パイプラインが自動的に構成されました。 パイプラインを探索し、必要に応じてカスタマイズします。 Azure DevOps のビルドおよびリリース パイプラインについて理解するには、次の手順を行います。

1. DevOps Starter ダッシュボードの上部の **[ビルド パイプライン]** を選択します。 このリンクによって、ブラウザーのタブが開かれ、新しいプロジェクトの Azure DevOps ビルド パイプラインが表示されます。

1. 省略記号 (...) を選択します。この操作により、キューへの新しいビルドの挿入、ビルドの一時停止、ビルド パイプラインの編集などのいくつかのアクティビティを開始できるメニューが開きます。

1. **[編集]** を選択します。

    ![ビルド パイプライン](_img/azure-devops-project-aspnet-core/builddef.png)

1. このウィンドウで、ビルド パイプラインのさまざまなタスクを調べることができます。 ビルドでは、Git リポジトリからのソースのフェッチ、依存関係の復元、デプロイに使用した出力の発行など、さまざまなタスクが実行されます。

1. ビルド パイプラインの上部で、ビルド パイプラインの名前を選択します。

1. ビルド パイプラインの名前をよりわかりやすい名前に変更し、 **[保存してキューに登録]** を選択して、 **[保存]** を選択します。

1. ご自身のビルド パイプラインの名前の下で、 **[履歴]** を選択します。   
**[履歴]** ウィンドウに、ビルドの最近の変更の監査証跡が表示されます。  ビルド パイプラインに対するすべての変更が Azure Pipelines によって追跡されるため、各バージョンを比較できます。

1. **[トリガー]** を選択します。 DevOps Starter では、CI トリガーが自動的に作成され、リポジトリに対してコミットするたびに新しいビルドが開始されます。 必要に応じて、CI プロセスのブランチを含めるか除外するかを選択できます。

1. **[保持]** を選択します。 シナリオに基づいて、特定の数のビルドを保持または削除するポリシーを指定できます。

1. **[ビルドとリリース]** を選択し、 **[リリース]** を選択します。  
DevOps Starter により、Azure へのデプロイを管理するリリース パイプラインが作成されます。

1.  左側で、リリース パイプラインの横にある省略記号 (...) を選択し、 **[編集]** を選択します。 リリース パイプラインには、リリース プロセスを定義するパイプラインが含まれています。  

1. **[成果物]** で、 **[ドロップ]** を選択します。 前の手順で調べたビルド パイプラインでは、成果物に使用される出力が生成されます。 

1. **[ドロップ]** アイコンの横にある **[継続的配置トリガー]** を選択します。 このリリース パイプラインには、新しいビルド成果物が使用可能になるたびにデプロイを実行する有効な CD トリガーがあります。 必要に応じて、手動でのデプロイが必須となるように、トリガーを無効にすることができます。  

1. 左側の **[タスク]** を選択します。  タスクは、デプロイ プロセスによって実行されるアクティビティです。 この例では、Azure App Service にデプロイするタスクが作成されました。

1. 右側の **[リリースの表示]** を選択します。 このビューには、リリースの履歴が表示されます。

1. いずれかのリリースの横にある省略記号 (...) を選択し、 **[開く]** を選択します。 リリース概要、関連付けられた作業項目、テストなど、調べる必要があるいくつかのメニューがあります。

1. **[コミット]** を選択します。 このビューには、特定のデプロイに関連付けられているコードのコミットが表示されます。 

1. **[ログ]** を選択します。 ログには、デプロイ プロセスに関する有用な情報が含まれます。 これらは、デプロイ中とデプロイ後の両方に表示できます。

## <a name="clean-up-resources"></a>リソースをクリーンアップする

作成した Azure App Service とその他の関連リソースが必要なくなったら、削除してかまいません。 DevOps Starter ダッシュボードで **削除** 機能を使用します。

## <a name="next-steps"></a>次のステップ

チームのニーズを満たすためのビルドおよびリリース パイプラインの変更について詳しくは、このチュートリアルをご覧ください。

> [!div class="nextstepaction"]
> [CD プロセスをカスタマイズする](/azure/devops/pipelines/release/define-multistage-release-process)

## <a name="videos"></a>ビデオ

> [!VIDEO https://www.youtube.com/embed/itwqMf9aR0w]
