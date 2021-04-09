---
title: クイック スタート:Java 用の CI/CD パイプラインを作成する - Azure DevOps Starter
description: Azure DevOps Starter の簡素化されたエクスペリエンスを使用した、Azure Pipelines 内での Java アプリ用の継続的インテグレーション (CI) および継続的デリバリー (CD) のパイプラインの設定について説明します。
ms.prod: devops
ms.technology: devops-cicd
services: vsts
documentationcenter: vs-devops-build
author: mlearned
manager: gwallace
ms.workload: web
ms.tgt_pltfrm: na
ms.topic: quickstart
ms.date: 03/24/2020
ms.author: mlearned
ms.custom: mvc, seo-java-july2019, seo-java-august2019, seo-java-september2019, devx-track-java
ms.openlocfilehash: d76c4206ca39518c3a09d27ee470c05db108ae6d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "102551883"
---
# <a name="set-up-a-cicd-pipeline-for-a-java-app-with-azure-devops-starter"></a>Azure DevOps Starter を使用して Java アプリ用の CI/CD パイプラインを設定する

このクイックスタートでは、Azure DevOps Starter の簡素化されたエクスペリエンスを使用して、Azure Pipelines 内に Java アプリ用の継続的インテグレーション (CI) および継続的デリバリー (CD) のパイプラインを設定します。 Azure DevOps Starter では、アプリの開発、デプロイ、監視に必要なすべてのものが設定されます。 

## <a name="prerequisites"></a>前提条件

- アクティブなサブスクリプションが含まれる Azure アカウント。 [無料でアカウントを作成できます](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)。 
- [Azure DevOps](https://azure.microsoft.com/services/devops/) のアカウントと組織。

## <a name="sign-in-to-the-azure-portal"></a>Azure portal にサインインする

DevOps Starter によって、Azure Pipelines に CI/CD パイプラインが作成されます。 新しい Azure DevOps 組織を作成するか、既存の組織を使用できます。 DevOps Starter では、選択した Azure サブスクリプションに Azure リソースも作成されます。

1. [Azure portal](https://portal.azure.com) にサインインします。

1. 検索ボックスに「**DevOps Starter**」と入力して選択します。 新しく作成するには、 **[追加]** をクリックします。

    ![DevOps Starter ダッシュボード](_img/azure-devops-starter-aks/search-devops-starter.png)

## <a name="select-a-sample-application-and-azure-service"></a>サンプル アプリケーションと Azure サービスを選択する

1. Java サンプル アプリケーションを選択します。 Java のサンプルでは、複数のアプリケーション フレームワークから選択できます。

1. 既定のサンプル フレームワークは Spring です。 既定の設定のままにして、**[次へ]** を選択します。  Web App for Containers が既定のデプロイ ターゲットです。 前に選択したアプリケーション フレームワークによって、ここで使用可能な Azure サービスのデプロイ ターゲットの種類が決まります。 

2. 既定のサービスのままにして、 **[次へ]** を選択します。
 
## <a name="configure-azure-devops-and-an-azure-subscription"></a>Azure DevOps と Azure サブスクリプションを構成する 

1. 新しい Azure DevOps 組織を作成するか、既存の組織を選択します。 
   
   1. プロジェクトの名前を選択します。 
   
   1. Azure サブスクリプションと場所を選択し、アプリケーションの名前を選択して、**[完了]** を選択します。  
   数分後、DevOps Starter ダッシュボードが Azure portal に表示されます。 サンプル アプリケーションが Azure DevOps 組織内のリポジトリに設定され、ビルドが実行され、アプリケーションが Azure にデプロイされます。 このダッシュボードでは、コード リポジトリ、CI/CD パイプライン、および Azure のアプリケーションが可視化されます。
   
2. **[参照]** を選択すると、実行中のアプリケーションが表示されます。
   
   ![Azure portal でアプリケーション ダッシュボードを表示する](_img/azure-devops-project-java/azure-devops-application-dashboard.png) 

DevOps Starter によって、CI ビルドおよびリリース トリガーが自動的に構成されました。  Web サイトに最新の作業を自動的にデプロイする CI/CD プロセスを使用して、Java アプリに対してチームで共同作業を行う準備ができました。

## <a name="commit-code-changes-and-execute-cicd"></a>コードの変更をコミットし、CI/CD を実行する

DevOps Starter によって、Azure Repos または GitHub に Git リポジトリが作成されます。 リポジトリを表示し、アプリケーションにコード変更を加えるには、次の手順に従います。

1. DevOps Starter ダッシュボードの左側にあるメイン ブランチのリンクを選択します。 このリンクは、新しく作成された Git リポジトリのビューを開きます。

1. リポジトリのクローン URL を表示するには、ブラウザーの右上の **[複製]** を選択します。 お気に入りの IDE で Git リポジトリを複製できます。 次のいくつかの手順では、Web ブラウザーを使用してメイン ブランチに直接コード変更を行い、コミットできます。

1. ブラウザーの左側で、**src/main/webapp/index.html** ファイルに移動します。

1. **[編集]** を選択し、テキストの一部を変更します。
    たとえば、div タグの 1 つのテキストの一部を変更します。

1. **[コミット]** を選択し、変更を保存します。

1. ブラウザーで DevOps Starter ダッシュボードに移動します。   
進行中のビルドが表示されるようになりました。 行った変更は、CI/CD パイプラインを介して自動的にビルドおよびデプロイされます。

## <a name="examine-the-cicd-pipeline"></a>CI/CD パイプラインを確認する

 前の手順で、DevOps Starter によって完全な CI/CD パイプラインが自動的に構成されました。 パイプラインを探索し、必要に応じてカスタマイズします。 ビルドおよびリリース パイプラインについて理解するには、次の手順を行います。

1. DevOps Starter ダッシュボードの上部の **[ビルド パイプライン]** を選択します。 このリンクによって、ブラウザーのタブが開かれ、新しいプロジェクトのビルド パイプラインが表示されます。

1. **[状態]** フィールドをポイントして、省略記号 (...) を選択します。この操作により、キューへの新しいビルドの挿入、ビルドの一時停止、ビルド パイプラインの編集などのいくつかのアクティビティを開始できるメニューが開きます。

1. **[編集]** を選択します。

1. このウィンドウで、ビルド パイプラインのさまざまなタスクを調べることができます。 ビルドでは、Git リポジトリからのソースのフェッチ、依存関係の復元、デプロイに使用した出力の発行など、さまざまなタスクが実行されます。

1. ビルド パイプラインの上部で、ビルド パイプラインの名前を選択します。

1. ビルド パイプラインの名前をよりわかりやすい名前に変更し、 **[保存してキューに登録]** を選択して、 **[保存]** を選択します。

1. ご自身のビルド パイプラインの名前の下で、 **[履歴]** を選択します。   
**[履歴]** ウィンドウに、ビルドの最近の変更の監査証跡が表示されます。  ビルド パイプラインに対するすべての変更が Azure Pipelines によって追跡されるため、各バージョンを比較できます。

1. **[トリガー]** を選択します。  DevOps Starter では、CI トリガーが自動的に作成され、リポジトリに対してコミットするたびに新しいビルドが開始されます。  必要に応じて、CI プロセスのブランチを含めるか除外するかを選択できます。

1. **[保持]** を選択します。 シナリオに基づいて、特定の数のビルドを保持または削除するポリシーを指定できます。

1. **[ビルドとリリース]** を選択し、 **[リリース]** を選択します。  
 DevOps Starter により、Azure へのデプロイを管理するリリース パイプラインが作成されます。

1. 左側で、リリース パイプラインの横にある省略記号 (...) を選択し、**[編集]** を選択します。 リリース パイプラインには、リリース プロセスを定義するパイプラインが含まれています。  
    
12. **[成果物]** で、 **[ドロップ]** を選択します。 前の手順で調べたビルド パイプラインでは、成果物に使用される出力が生成されます。 

1. **[ドロップ]** アイコンの横にある **[継続的配置トリガー]** を選択します。 このリリース パイプラインには、新しいビルド成果物が使用可能になるたびにデプロイを実行する有効な CD トリガーがあります。 必要に応じて、手動でのデプロイが必須となるように、トリガーを無効にすることができます。 

1. 左側の **[タスク]** を選択します。 タスクは、デプロイ プロセスによって実行されるアクティビティです。 この例では、Azure App Service にデプロイするタスクが作成されました。

1. 右側の **[リリースの表示]** を選択します。 このビューには、リリースの履歴が表示されます。

1. いずれかのリリースの横にある省略記号 (...) を選択し、 **[開く]** を選択します。 リリース概要、関連付けられた作業項目、テストなど、調べる必要があるいくつかのメニューがあります。

1. **[コミット]** を選択します。 このビューには、特定のデプロイに関連付けられているコードのコミットが表示されます。 

1. **[ログ]** を選択します。 ログには、デプロイ プロセスに関する有用な情報が含まれます。 これらは、デプロイ中とデプロイ後の両方に表示できます。

## <a name="clean-up-resources"></a>リソースをクリーンアップする

Azure App Service とその他の関連リソースが必要なくなったら、削除してかまいません。 DevOps Starter ダッシュボードで **削除** 機能を使用します。

## <a name="next-steps"></a>次のステップ

CI/CD プロセスを構成したときに、ビルドとリリース パイプラインが自動的に作成されました。 チームのニーズを満たすようにこれらのビルドおよびリリース パイプラインを変更できます。 CI/CD パイプラインの詳細については、以下を参照してください。

> [!div class="nextstepaction"]
> [CD プロセスをカスタマイズする](/azure/devops/pipelines/release/define-multistage-release-process)