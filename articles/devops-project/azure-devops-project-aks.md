---
title: Azure DevOps Starter を使用して ASP.NET Core アプリを Azure Kubernetes Service にデプロイする
description: Azure DevOps Starter を利用すると、Azure を使い始めるのが簡単になります。 DevOps Starter を使用することによって、いくつかの簡単な手順で ASP.NET Core アプリを Azure Kubernetes Service (AKS) に簡単にデプロイできます。
ms.author: mlearned
ms.manager: gwallace
ms.prod: devops
ms.technology: devops-cicd
ms.topic: tutorial
ms.date: 03/24/2020
author: mlearned
ms.openlocfilehash: 9ccf28f5431a92f71b1c18e609639d0abf309c06
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "100590862"
---
# <a name="deploy-aspnet-core-apps-to-azure-kubernetes-service-with-azure-devops-starter"></a>Azure DevOps Starter を使用して ASP.NET Core アプリを Azure Kubernetes Service にデプロイする

Azure DevOps Starter によって提供される簡略化されたエクスペリエンスを使用すると、既存のコードと Git リポジトリを使用するか、サンプル アプリケーションを選択して、Azure への継続的インテグレーション (CI) と継続的デリバリー (CD) のパイプラインを作成することができます。 

DevOps Starter では次のこともできます。

* Azure Kubernetes Service (AKS) などの Azure リソースを自動的に作成する。
* CI/CD 用のビルドおよびリリース パイプラインを設定するリリース パイプラインを Azure DevOps に作成し、構成する。
* 監視のために Azure Application Insights リソースを作成する。
* [コンテナーに対する Azure Monitor](../azure-monitor/containers/container-insights-overview.md) で、AKS クラスター上のコンテナー ワークロードに対するパフォーマンスを監視できるようにする

このチュートリアルでは、次のことについて説明します。

> [!div class="checklist"]
> * DevOps Starter を使用して、ASP.NET Core アプリを AKS にデプロイする
> * Azure DevOps と Azure サブスクリプションを構成する 
> * AKS クラスターを確認する
> * CI パイプライン を確認する
> * CD パイプライン を確認する
> * 変更を Git にコミットし、Azure に自動的にデプロイする
> * リソースをクリーンアップする

## <a name="prerequisites"></a>前提条件

* Azure サブスクリプション。 [Visual Studio Dev Essentials](https://visualstudio.microsoft.com/dev-essentials/) を通じて無料で取得できます。

## <a name="use-devops-starter-to-deploy-an-aspnet-core-app-to-aks"></a>DevOps Starter を使用して、ASP.NET Core アプリを AKS にデプロイする

DevOps Starter によって、Azure Pipelines に CI/CD パイプラインが作成されます。 新しい Azure DevOps 組織を作成するか、既存の組織を使用できます。 DevOps Starter によって、選択した Azure サブスクリプションに AKS クラスターなどの Azure リソースも作成されます。

1. [Azure portal](https://portal.azure.com) にサインインします。

1. 検索ボックスに「**DevOps Starter**」と入力して選択します。 新しく作成するには、 **[追加]** をクリックします。

    ![DevOps Starter ダッシュボード](_img/azure-devops-starter-aks/search-devops-starter.png)

1. **[.NET]** を選択し、 **[次へ]** を選択します。

1. **[アプリケーション フレームワークを選択します]** で **[ASP.NET Core]** を選択し、 **[次へ]** を選択します。

1. **[Kubernetes サービス]** を選択し、 **[次へ]** を選択します。 

## <a name="configure-azure-devops-and-an-azure-subscription"></a>Azure DevOps と Azure サブスクリプションを構成する

1. 新しい Azure DevOps 組織を作成するか、既存の組織を選択します。 

1. Azure DevOps プロジェクトの名前を入力します。 

1. Azure サブスクリプションを選択します。

1. 追加の Azure 構成設定を表示し、AKS クラスターのノード数を特定するには、 **[変更]** を選択します。 このウィンドウには、Azure サービスの種類と場所を構成するためのさまざまなオプションが表示されます。
 
1. Azure の構成領域を終了し、 **[完了]** を選択します。 数分後に、処理が完了します。 サンプル ASP.NET Core アプリが Azure DevOps 組織内の Git リポジトリに設定され、AKS クラスターが作成されます。そして CI/CD パイプラインが実行され、アプリが Azure にデプロイされます。 

    このすべてが完了すると、Azure DevOps Starter ダッシュボードが Azure portal に表示されます。 DevOps Starter ダッシュボードには、Azure portal の **[すべてのリソース]** から直接移動することもできます。 

    このダッシュボードでは、Azure DevOps コード リポジトリ、CI/CD パイプライン、および AKS クラスターが可視化されます。 Azure DevOps パイプラインで追加の CI/CD オプションを構成できます。 右側の **[参照]** を選択すると、実行中のアプリが表示されます。

## <a name="examine-the-aks-cluster"></a>AKS クラスターを確認する

DevOps Starter によって、AKS クラスターが自動的に構成されます。このクラスターを調べて、カスタマイズすることができます。 AKS クラスターについて理解するには、次の手順に従います。

1. DevOps Starter ダッシュボードに移動します。

1. 右側で AKS サービスを選択します。 AKS クラスターのウィンドウが開きます。 このビューから、コンテナーの正常性の監視、ログの検索、Kubernetes ダッシュボードの表示など、さまざまなアクションを実行することができます。

1. 右側で **[Kubernetes ダッシュボードを表示]** を選択します。 必要に応じて、Kubernetes ダッシュボードを開く手順に従います。

## <a name="examine-the-ci-pipeline"></a>CI パイプライン を確認する

DevOps Starter によって、Azure DevOps 組織内に CI/CD パイプラインが自動的に構成されます。 パイプラインを探索し、カスタマイズできます。 これについて理解するには、次の手順に従います。

1. DevOps Starter ダッシュボードに移動します。

1. DevOps Starter ダッシュボードの上部の **[ビルド パイプライン]** を選択します。  ブラウザーのタブに、新しいプロジェクトのビルド パイプラインが表示されます。

1. **[状態]** フィールドをポイントして、省略記号 (...) を選択します。メニューには、新しいビルドをキューに入れる、ビルドを一時停止する、ビルド パイプラインを編集するなど、いくつかのオプションが表示されます。

1. **[編集]** を選択します。

1. このウィンドウで、ビルド パイプラインのさまざまなタスクを調べることができます。 ビルドでは、Git リポジトリからのソースのフェッチ、依存関係の復元、デプロイに使用した出力の発行など、さまざまなタスクが実行されます。

1. ビルド パイプラインの上部で、ビルド パイプラインの名前を選択します。

1. ビルド パイプラインの名前をよりわかりやすい名前に変更し、 **[保存してキューに登録]** を選択して、 **[保存]** を選択します。

1. ご自身のビルド パイプラインの名前の下で、 **[履歴]** を選択します。 このウィンドウには、ビルドに対する最近の変更の監査証跡が表示されます。 ビルド パイプラインに対するすべての変更が Azure DevOps によって追跡されるため、各バージョンを比較できます。

1. **[トリガー]** を選択します。 DevOps Starter では、CI トリガーが自動的に作成され、リポジトリに対してコミットするたびに新しいビルドが開始されます。 必要に応じて、CI プロセスのブランチを含めるか除外するかを選択できます。

1. **[保持]** を選択します。 シナリオに基づいて、特定の数のビルドを保持または削除するポリシーを指定できます。

## <a name="examine-the-cd-release-pipeline"></a>CD リリース パイプラインを調べる

DevOps Starter では、Azure DevOps 組織から Azure サブスクリプションにデプロイするために必要な手順が自動的に作成され、構成されます。 これらの手順には、Azure サブスクリプションで Azure DevOps を認証するための Azure サービス接続の構成が含まれます。 自動化によってリリース パイプラインも作成され、このパイプラインによって CD が Azure に提供されます。 リリース パイプラインの詳細を知るには、次の手順を行います。

1. **[ビルドとリリース]** を選択し、 **[リリース]** を選択します。  DevOps Starter により、Azure へのデプロイを管理するリリース パイプラインが作成されます。

1. リリース パイプラインの横にある省略記号 (...) を選択し、 **[編集]** を選択します。 リリース パイプラインには、リリース プロセスを定義する *パイプライン* が含まれています。

1. **[成果物]** で、 **[ドロップ]** を選択します。 前の手順で調べたビルド パイプラインでは、成果物に使用される出力が生成されます。 

1. **[ドロップ]** アイコンの右側にある **[継続的配置トリガー]** を選択します。 このリリース パイプラインには、新しいビルド成果物が使用可能になるたびにデプロイを実行する有効な CD トリガーがあります。 必要に応じて、手動でのデプロイが必須となるように、トリガーを無効にすることができます。 

1. 右側で **[リリースの表示]** を選択して、リリースの履歴を表示します。

1. リリースの横にある省略記号 (...) を選択し、 **[開く]** を選択します。 リリース概要、関連付けられた作業項目、テストなど、いくつかのメニューを調べることができます。

1. **[コミット]** を選択します。 このビューには、このデプロイに関連付けられているコードのコミットが表示されます。 リリースを比較して、デプロイ間のコミットの相違を表示します。

1. **[ログ]** を選択します。 ログには、デプロイ プロセスに関する有用な情報が含まれます。 それらは、デプロイ中もデプロイ後も表示されます。

## <a name="commit-changes-to-azure-repos-and-automatically-deploy-them-to-azure"></a>変更を Azure Repos にコミットし、Azure に自動的にデプロイする 

 > [!NOTE]
 > 次の手順では、単純なテキストの変更を行って、CI/CD パイプラインをテストします。

Web サイトに最新の作業を自動的にデプロイする CI/CD プロセスを使用して、アプリに対してチームで共同作業を行う準備ができました。 Git リポジトリに対する各変更により、Azure DevOps でビルドが開始され、CD パイプラインによって Azure へのデプロイが実行されます。 このセクションの手順に従うか、他の手法を使用して、リポジトリに変更をコミットします。 たとえば、お気に入りのツールや IDE で Git リポジトリを複製し、変更をそのリポジトリにプッシュできます。

1. Azure DevOps メニューで **[コード]**  >  **[ファイル]** の順に選択し、リポジトリに移動します。

1. *Views\Home* ディレクトリに移動し、*Index.cshtml* ファイルの横にある省略記号 (...) を選択し、 **[編集]** を選択します。

1. いずれかの div タグ内にテキストを追加するなど、ファイルを変更します。 

1. 右上の **[コミット]** を選択し、もう一度 **[コミット]** を選択して、変更をプッシュします。 しばらくすると Azure DevOps でビルドが開始され、リリースが実行されて、変更がデプロイされます。 DevOps Starter ダッシュボードで、またはブラウザーで Azure DevOps 組織を使用して、ビルドの状態を監視します。

1. リリースが完了したら、アプリを更新して、変更を確認します。

## <a name="clean-up-resources"></a>リソースをクリーンアップする

テストを行っている場合は、リソースをクリーンアップすることによって、課金を回避することができます。 このチュートリアルで作成した AKS クラスターと関連リソースが必要なくなったら、削除してかまいません。 そうするには、DevOps Starter ダッシュボードで **削除** 機能を使用します。

> [!IMPORTANT]
> 次の手順で、リソースが完全に削除されます。 "*削除*" 機能では、DevOps Starter のプロジェクトによって作成されたデータが Azure と Azure DevOps の両方で破棄され、取得できなくなります。 画面に表示される説明を慎重に読んでから、この手順を行ってください。

1. Azure portal で、DevOps Starter ダッシュボードに移動します。
1. 右上の **[削除]** を選択します。 
1. 確認のメッセージで **[はい]** を選択すると、リソースが "*完全に削除*" されます。

## <a name="next-steps"></a>次のステップ

必要に応じて、チームのニーズを満たすようにこれらのビルドおよびリリース パイプラインを変更できます。 この CI/CD パターンをご自身の他のパイプラインのテンプレートとして使用することもできます。 このチュートリアルでは、以下の内容を学習しました。

> [!div class="checklist"]
> * DevOps Starter を使用して、ASP.NET Core アプリを AKS にデプロイする
> * Azure DevOps と Azure サブスクリプションを構成する 
> * AKS クラスターを確認する
> * CI パイプライン を確認する
> * CD パイプライン を確認する
> * 変更を Git にコミットし、Azure に自動的にデプロイする
> * リソースをクリーンアップする

Kubernetes ダッシュボードの使い方の詳細については、以下を参照してください。

> [!div class="nextstepaction"]
> [Kubernetes ダッシュボードを使用する](../aks/kubernetes-dashboard.md)