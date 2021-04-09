---
title: チュートリアル:Azure DevOps Starter を使用して ASP.NET アプリと Azure SQL Database コードをデプロイする
description: DevOps Starter を利用すると、Azure を使い始めるのが簡単になります。 DevOps Starter を使用すると、いくつかの簡単な手順で ASP.NET アプリと Azure SQL Database コードをデプロイできます。
ms.author: mlearned
ms.manager: gwallace
ms.prod: devops
ms.technology: devops-cicd
ms.topic: tutorial
ms.date: 03/24/2020
author: mlearned
ms.openlocfilehash: 7e85b698e1b3c3a1c3e8730ab85e8b72b7b0d9f6
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102548480"
---
# <a name="tutorial-deploy-your-aspnet-app-and-azure-sql-database-code-by-using-azure-devops-starter"></a>チュートリアル:Azure DevOps Starter を使用して ASP.NET アプリと Azure SQL Database コードをデプロイする

Azure DevOps Starter によって提供される簡略化されたエクスペリエンスを使用すると、既存のコードと Git リポジトリを使用するか、サンプル アプリケーションを選択して、Azure への継続的インテグレーション (CI) と継続的デリバリー (CD) のパイプラインを作成することができます。 

DevOps Starter では次のこともできます。
* Azure SQL Database のデータベースなどの Azure リソースを自動的に作成する。
* CI 用のビルド パイプラインを含むリリース パイプラインを Azure Pipelines に作成し、構成する。
* CD 用のリリース パイプラインをセットアップする。 
* 監視のために Azure Application Insights リソースを作成する。

このチュートリアルでは、次のことについて説明します。

> [!div class="checklist"]
> * Azure DevOps Starter を使用して ASP.NET アプリと Azure SQL Database コードをデプロイする
> * Azure DevOps と Azure サブスクリプションを構成する 
> * CI パイプライン を確認する
> * CD パイプライン を確認する
> * 変更を Azure Repos にコミットし、Azure に自動的にデプロイする
> * Azure SQL Database に接続する 
> * リソースをクリーンアップする

## <a name="prerequisites"></a>前提条件

* Azure サブスクリプション。 [Visual Studio Dev Essentials](https://visualstudio.microsoft.com/dev-essentials/) を通じて無料で取得できます。

## <a name="create-a-project-in-devops-projects-for-an-aspnet-app-and-azure-sql-database"></a>ASP.NET アプリと Azure SQL Database 用のプロジェクトを DevOps Projects で作成する

DevOps Starter によって、Azure Pipelines に CI/CD パイプラインが作成されます。 新しい Azure DevOps 組織を作成するか、既存の組織を使用できます。 DevOps Starter では、選択した Azure サブスクリプションに Azure SQL Database などの Azure リソースも作成されます。

1. [Azure portal](https://portal.azure.com) にサインインします。

1. 検索ボックスに「**DevOps Starter**」と入力して選択します。 新しく作成するには、 **[追加]** をクリックします。

    ![DevOps Starter ダッシュボード](_img/azure-devops-starter-aks/search-devops-starter.png)

1. **[.NET]** を選択し、 **[次へ]** を選択します。

1. **[アプリケーション フレームワークを選択します]** で **[ASP.NET]** を選択します。

1. **[データベースを追加する]** を選択し、 **[次へ]** を選択します。 前の手順で選択したアプリケーション フレームワークによって、ここで使用可能な Azure サービスのデプロイ ターゲットの種類が決まります。 
    
1. **[次へ]** を選択します。

## <a name="configure-azure-devops-and-an-azure-subscription"></a>Azure DevOps と Azure サブスクリプションを構成する

1. 新しい Azure DevOps 組織を作成するか、既存の組織を選択します。 

1. Azure DevOps プロジェクトの名前を入力します。 

1. Azure サブスクリプション サービスを選択します。 必要に応じて、追加の Azure 構成設定を表示し、 **[データベース サーバーのログインの詳細]** セクションでユーザー名を特定するために、 **[変更]** を選択できます。 このチュートリアルの後の手順で使うので、ユーザー名を保存します。 このオプションの手順を行った場合は、 **[完了]** を選択する前に、Azure の構成領域を終了します。
 
1. **[Done]** を選択します。 数分後、処理が完了し、Azure portal で DevOps Starter ダッシュボードが開かれます。 このダッシュボードには、Azure portal の **[すべてのリソース]** から直接移動することもできます。 右側の **[参照]** を選択すると、実行中のアプリケーションが表示されます。
    
## <a name="examine-the-ci-pipeline"></a>CI パイプライン を確認する

DevOps Starter によって、Azure Repos に完全な CI/CD パイプラインが自動的に構成されます。 パイプラインを探索し、カスタマイズできます。 Azure DevOps ビルド パイプラインについて理解するには、次の手順を行います。

1. DevOps Starter ダッシュボードの上部の **[ビルド パイプライン]** を選択します。 ブラウザーのタブに、新しいプロジェクトのビルド パイプラインが表示されます。

1. **[状態]** フィールドをポイントして、省略記号 (...) を選択します。メニューには、新しいビルドをキューに入れる、ビルドを一時停止する、ビルド パイプラインを編集するなど、いくつかのオプションが表示されます。

1. **[編集]** を選択します。

1. このウィンドウで、ビルド パイプラインのさまざまなタスクを調べることができます。 ビルドでは、Git リポジトリからのソースのフェッチ、依存関係の復元、デプロイに使用した出力の発行など、さまざまなタスクが実行されます。

1. ビルド パイプラインの上部で、ビルド パイプラインの名前を選択します。

1. ビルド パイプラインの名前をよりわかりやすい名前に変更し、 **[保存してキューに登録]** を選択して、 **[保存]** を選択します。

1. ご自身のビルド パイプラインの名前の下で、 **[履歴]** を選択します。 このウィンドウには、ビルドに対する最近の変更の監査証跡が表示されます。 ビルド パイプラインに対するすべての変更が Azure Pipelines によって追跡されるため、各バージョンを比較できます。

1. **[トリガー]** を選択します。 DevOps Starter では、CI トリガーが自動的に作成され、リポジトリに対してコミットするたびに新しいビルドが開始されます。 必要に応じて、CI プロセスのブランチを含めるか除外するかを選択できます。

1. **[保持]** を選択します。 シナリオに基づいて、特定の数のビルドを保持または削除するポリシーを指定できます。

## <a name="examine-the-cd-pipeline"></a>CD パイプライン を確認する

DevOps Starter では、Azure DevOps 組織から Azure サブスクリプションにデプロイするために必要な手順が自動的に作成され、構成されます。 これらの手順には、Azure サブスクリプションで Azure DevOps を認証するための Azure サービス接続の構成が含まれます。 自動化により CD パイプラインも作成され、これにより Azure 仮想マシンに CD が提供されます。 Azure DevOps CD パイプラインの詳細を知るには、次の手順を行います。

1. **[ビルドとリリース]** を選択し、 **[リリース]** を選択します。 DevOps Starter により、Azure へのデプロイを管理するリリース パイプラインが作成されます。

1. リリース パイプラインの横にある省略記号 (...) を選択し、 **[編集]** を選択します。 リリース パイプラインには、リリース プロセスを定義する *パイプライン* が含まれています。

1. **[成果物]** で、 **[ドロップ]** を選択します。 前の手順で調べたビルド パイプラインでは、成果物に使用される出力が生成されます。 

1. **[ドロップ]** アイコンの右側にある **[継続的配置トリガー]** を選択します。 このリリース パイプラインには、新しいビルド成果物が使用可能になるたびにデプロイを実行する有効な CD トリガーがあります。 必要に応じて、手動でのデプロイが必須となるように、トリガーを無効にすることができます。 

    DevOps Starter では、ランダムな SQL パスワードが設定され、リリース パイプラインに使用されます。
    
1. 左側の **[変数]** を選択します。 

   > [!NOTE]
   > 次の手順は、SQL Server のパスワードを変更した場合にのみ行います。 パスワード変数は 1 つだけあります。
  
1. **[値]** ボックスの横にある南京錠アイコンを選択し、新しいパスワードを入力して、 **[保存]** を選択します。

1. 左側の **[タスク]** を選択して、環境を選択します。 タスクはデプロイ プロセスによって実行されるアクティビティであり、フェーズとしてグループ化されます。 このリリース パイプラインには 1 つのフェーズがあり、それには "*Azure App Service 配置*" タスクと "*Azure SQL Database の配置*" タスクが含まれています。

1. "*Azure SQL を実行します*" タスクを選択し、SQL のデプロイに使用されるさまざまなプロパティを確認します。 **[配置パッケージ]** で、タスクが "*SQL DACPAC*" ファイルを使用しています。

1. 右側で **[リリースの表示]** を選択して、リリースの履歴を表示します。

1. リリースの横にある省略記号 (...) を選択し、 **[開く]** を選択します。 リリース概要、関連付けられた作業項目、テストなど、いくつかのメニューを調べることができます。

1. **[コミット]** を選択します。 このビューには、このデプロイに関連付けられているコードのコミットが表示されます。 リリースを比較して、デプロイ間のコミットの相違を表示します。

1. **[ログ]** を選択します。 ログには、デプロイ プロセスに関する有用な情報が含まれます。 それらは、デプロイ中もデプロイ後も表示されます。

## <a name="commit-changes-to-azure-repos-and-automatically-deploy-them-to-azure"></a>変更を Azure Repos にコミットし、Azure に自動的にデプロイする 

 > [!NOTE]
 > 次の手順では、単純なテキストの変更を行って、CI/CD パイプラインをテストします。 SQL のデプロイ プロセスをテストするには、必要に応じて、テーブルの SQL Server スキーマを変更します。

Web サイトに最新の作業を自動的にデプロイする CI/CD プロセスを使用して、アプリに対してチームで共同作業を行う準備ができました。 Git リポジトリに対する各変更により、Azure DevOps でビルドが開始され、CD パイプラインによって Azure へのデプロイが実行されます。 このセクションの手順に従うか、他の手法を使用して、リポジトリに変更をコミットします。 コードを変更すると CI/CD プロセスが開始され、変更が Azure に自動的にデプロイされます。

1. 左のウィンドウで **[コード]** を選択し、リポジトリに移動します。

1. *SampleWebApplication\Views\Home* ディレクトリに移動し、*Index.cshtml* ファイルの横にある省略記号 (...) を選択して、 **[編集]** を選択します。 

1. いずれかの div タグ内にテキストを追加するなど、ファイルを変更します。 

1. 右上の **[コミット]** を選択し、もう一度 **[コミット]** を選択して、変更をプッシュします。 しばらくすると Azure DevOps でビルドが開始され、リリースが実行されて、変更がデプロイされます。 DevOps Starter ダッシュボードで、またはブラウザーで Azure DevOps 組織を使用して、ビルドの状態を監視します。

1. リリースが完了したら、アプリケーションを更新して、変更を確認します。

## <a name="connect-to-azure-sql-database"></a>Azure SQL Database に接続する

Azure SQL Database に接続するには、適切なアクセス許可が必要です。

1. DevOps Starter ダッシュボードで **[SQL データベース]** を選択し、SQL Database の管理ページに移動します。
   
1. **[サーバー ファイアウォールの設定]** を選択し、 **[クライアント IP の追加]** を選択します。 

1. **[保存]** を選択します。 クライアント IP が、SQL Server Azure リソースにアクセスできるようになりました。

1. **[SQL データベース]** ウィンドウに戻ります。 

1. 右側でサーバー名を選択して、**SQL Server** の構成ページに移動します。

1. **[パスワードのリセット]** を選択し、SQL Server 管理者ログインのパスワードを入力して、 **[保存]** を選択します。 必ずこのパスワードを記録しておいてください。このチュートリアルの後の方で使用します。

    これで、必要に応じて、SQL Server Management Studio や Visual Studio などのクライアント ツールを使用して、SQL Server と Azure SQL Database に接続できるようになりました。 **[サーバー名]** プロパティを使用して接続します。

    DevOps Projects のプロジェクトの初期構成の際にデータベース ユーザー名を変更しなかった場合、ユーザー名はメール アドレスのローカル部分です。 たとえば、メール アドレスが *johndoe\@microsoft.com* の場合、ユーザー名は *johndoe* です。

   > [!NOTE]
   > SQL ログインのパスワードを変更する場合は、「[CD パイプラインを確認する](#examine-the-cd-pipeline)」セクションで説明したように、リリース パイプライン変数のパスワードを変更する必要があります。

## <a name="clean-up-resources"></a>リソースをクリーンアップする

テストを行っている場合は、リソースをクリーンアップすることによって、課金を回避することができます。 このチュートリアルで作成した Azure SQL Database と関連リソースが必要なくなったら、削除してかまいません。 そうするには、DevOps Starter ダッシュボードで **削除** 機能を使用します。

> [!IMPORTANT]
> 次の手順で、リソースが完全に削除されます。 "*削除*" 機能では、DevOps Starter のプロジェクトによって作成されたデータが Azure と Azure DevOps の両方で破棄され、取得できなくなります。 画面に表示される説明を慎重に読んでから、この手順を行ってください。

1. Azure portal で、DevOps Starter ダッシュボードに移動します。
2. 右上の **[削除]** を選択します。 
3. 確認のメッセージで **[はい]** を選択すると、リソースが "*完全に削除*" されます。

## <a name="next-steps"></a>次のステップ

必要に応じて、チームのニーズを満たすようにこれらのビルドおよびリリース パイプラインを変更できます。 この CI/CD パターンをご自身の他のパイプラインのテンプレートとして使用することもできます。 このチュートリアルでは、以下の内容を学習しました。

> [!div class="checklist"]
> * Azure DevOps Starter を使用して ASP.NET アプリと Azure SQL Database コードをデプロイする
> * Azure DevOps と Azure サブスクリプションを構成する 
> * CI パイプライン を確認する
> * CD パイプライン を確認する
> * 変更を Azure Repos にコミットし、Azure に自動的にデプロイする
> * Azure SQL Database に接続する 
> * リソースをクリーンアップする

CI/CD パイプラインの詳細については、以下を参照してください。

> [!div class="nextstepaction"]
> [複数ステージの継続的デプロイ (CD) パイプラインを定義する](/azure/devops/pipelines/release/define-multistage-release-process)

## <a name="videos"></a>ビデオ

> [!VIDEO https://channel9.msdn.com/Events/Build/2018/BRK3308/player]