---
title: Synapse ワークスペースの継続的インテグレーションとデリバリー
description: 継続的インテグレーションとデリバリーを使用してワークスペース内の変更をある環境 (開発、テスト、運用) から別の環境にデプロイする方法について説明します。
services: synapse-analytics
author: liud
ms.service: synapse-analytics
ms.topic: conceptual
ms.date: 11/20/2020
ms.author: liud
ms.reviewer: pimorano
ms.openlocfilehash: ba5286b16b6e640e968b50174e39a05328e750a4
ms.sourcegitcommit: 95c2cbdd2582fa81d0bfe55edd32778ed31e0fe8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/26/2021
ms.locfileid: "98797310"
---
# <a name="continuous-integration-and-delivery-for-azure-synapse-workspace"></a>Azure Synapse ワークスペースの継続的インテグレーションとデリバリー

## <a name="overview"></a>概要

継続的インテグレーション (CI) は、チーム メンバーが変更をバージョン コントロールにコミットするたびに、コードのビルドとテストを自動化するプロセスです。 継続的配置 (CD) は、複数のテスト環境またはステージング環境から運用環境へのビルド、テスト、構成、およびデプロイを行うプロセスです。

Azure Synapse ワークスペースでは、継続的インテグレーションとデリバリー (CI/CD) を使用して、すべてのエンティティをある環境 (開発、テスト、運用) から別の環境に移動します。 ワークスペースを別のワークスペースに昇格させるには、次の 2 つの部分があります。[Resource Manager テンプレート](../../azure-resource-manager/templates/overview.md)を使用して、ワークスペース リソース (プールとワークスペース) を作成または更新します。Azure DevOps の Synapse CI/CD ツールを使用して、アーティファクト (SQL スクリプト、ノートブック、Spark ジョブの定義、パイプライン、データセット、データフローなど) を移行します。 

この記事では、Azure リリース パイプラインを使用して、複数の環境への Synapse ワークスペースのデプロイを自動化する方法について説明します。

## <a name="prerequisites"></a>[前提条件]

-   開発に使用されているワークスペースは、Studio の Git リポジトリを使用して構成されています。[Synapse Studio でのソース管理](source-control.md)に関するページを参照してください。
-   リリース パイプラインを実行するための Azure DevOps プロジェクトが準備されています。

## <a name="set-up-a-release-pipelines"></a>リリース パイプラインの設定

1.  [Azure DevOps](https://dev.azure.com/) で、リリース用に作成されたプロジェクトを開きます。

1.  ページの左側で、 **[パイプライン]** を選択して **[リリース]** を選択します。

    ![パイプラインの選択、リリース](media/create-release-1.png)

1.  **[新しいパイプライン]** を選択するか、既存のパイプラインがある場合は **[新規]** 、 **[新しいリリース パイプライン]** の順に選択します。

1.  **[空のジョブ]** テンプレートを選択します。

    ![空のジョブの選択](media/create-release-select-empty.png)

1.  **[ステージ名]** ボックスに、環境の名前を入力します。

1.  **[成果物の追加]** を選択し、開発 Synapse Studio で構成した Git リポジトリを選択します。 プールとワークスペースの ARM テンプレートを管理するために使用した Git リポジトリを選択します。 ソースとして GitHub を使用する場合は、GitHub アカウントとプル リポジトリのサービス接続を作成する必要があります。 [サービス接続](/azure/devops/pipelines/library/service-endpoints)の詳細情報 

    ![発行ブランチの追加](media/release-creation-github.png)

1.  リソース更新用の ARM テンプレートのブランチを選択します。 **[既定のバージョン]** では **[既定のブランチからの最新バージョン]** を選択します。

    ![ARM テンプレートの追加](media/release-creation-arm-branch.png)

1.  **既定のブランチ** のリポジトリの [発行ブランチ](source-control.md#configure-publishing-settings)を選択します。 既定では、この発行ブランチは `workspace_publish` です。 **[既定のバージョン]** では **[既定のブランチからの最新バージョン]** を選択します。

    ![アーティファクトの追加](media/release-creation-publish-branch.png)

## <a name="set-up-a-stage-task-for-arm-resource-create-and-update"></a>ARM リソースの作成と更新のステージ タスクを設定する 

リソース (ワークスペース、プールなど) を作成または更新するための Azure Resource Manager デプロイ タスクを追加します。

1. ステージ ビューで、 **[ステージ タスクを表示します]** を選択します。

    ![ステージ ビュー](media/release-creation-stage-view.png)

1. 新しいタスクを作成します。 **[ARM テンプレートのデプロイ]** を探して **[追加]** を選択します。

1. デプロイ タスクでは、ターゲットのワークスペースのサブスクリプション、リソース グループ、および場所を選択します。 必要であれば資格情報を指定します。

1. **[Action]\(アクション\)** 一覧で、 **[Create or update resource group]\(リソース グループの作成または更新\)** を選択します。

1. **[テンプレート]** ボックスの横にある省略記号ボタン ( **[…]** ) を選択します。 ターゲットのワークスペースの Azure Resource Manager テンプレートを参照します

1. **[…]** を選択します ( **[Template parameters]\(テンプレート パラメーター\)** ボックスの横にあります)。これにより、パラメーター ファイルを選択します。

1. **[…]** を選択します ( **[テンプレート パラメーターのオーバーライド]** ボックスの横にあります)。ターゲットのワークスペースに望ましいパラメーター値を入力します。 

1. **[配置モード]** で **[Incremental]\(増分\)** を選択します。
    
    ![ワークスペースとプールのデプロイ](media/pools-resource-deploy.png)

1. (省略可能) ワークスペースの付与と更新のロール割り当てのために **Azure PowerShell** を追加します。 リリース パイプラインを使用して Synapse ワークスペースを作成する場合は、パイプラインのサービス プリンシパルが既定のワークスペース管理者として追加されます。PowerShell を実行して、他のアカウントにワークスペースへのアクセスを許可することができます。 
    
    ![アクセス許可の付与](media/release-creation-grant-permission.png)

 > [!WARNING]
> 完全なデプロイ モードでは、リソース グループに存在していても、新しい Resource Manager テンプレート内で指定されていないリソースは、**削除** されます。 詳細については、「[Azure Resource Manager のデプロイ モード](../../azure-resource-manager/templates/deployment-modes.md)」を参照してください。

## <a name="set-up-a-stage-task-for-artifacts-deployment"></a>アーティファクトのデプロイのステージ タスクを設定する 

[Synapse ワークスペースのデプロイ](https://marketplace.visualstudio.com/items?itemName=AzureSynapseWorkspace.synapsecicd-deploy)拡張機能を使用して、データセット、SQL スクリプト、ノートブック、Spark ジョブ定義、データフロー、パイプライン、リンクされたサービス、資格情報、IR (Integration Runtime) などの他のアイテムを Synapse ワークスペースにデプロイします。  

1. この拡張機能を **Azure DevOps マーケットプレース** で検索して入手します (https://marketplace.visualstudio.com/azuredevops) 

     ![拡張機能の入手](media/get-extension-from-market.png)

1. 拡張機能をインストールする組織を選択します。 

     ![拡張機能のインストール](media/install-extension.png)

1. Azure DevOps パイプラインのサービス プリンシパルにサブスクリプションのアクセス許可が付与され、ターゲット ワークスペースのワークスペース管理者としても割り当てられていることを確認します。 

1. 新しいタスクを作成します。 **[Synapse ワークスペースのデプロイ]** を探して **[追加]** を選択します。

     ![拡張機能の追加](media/add-extension-task.png)

1.  タスクで、 **[…]** を選択します ( **[テンプレート]** ボックスの横にあります)。これにより、テンプレート ファイルを選択します。

1. **[…]** を選択します ( **[Template parameters]\(テンプレート パラメーター\)** ボックスの横にあります)。これにより、パラメーター ファイルを選択します。

1. ターゲット ワークスペースの接続、リソース グループ、および名前を選択します。 

1. **[…]** を選択します ( **[テンプレート パラメーターのオーバーライド]** ボックスの横にあります)。ターゲットのワークスペースに望ましいパラメーター値を入力します。 

    ![Synapse ワークスペースのデプロイ](media/create-release-artifacts-deployment.png)

> [!IMPORTANT]
> CI/CD のシナリオでは、異なる環境内の統合ランタイム (IR) の種類は同じである必要があります。 たとえば、開発環境にセルフホステッド IR がある場合、テストや運用などの他の環境でも、IR の種類はセルフホステッドである必要があります。 同様に、複数のステージ間で統合ランタイムを共有している場合は、開発、テスト、運用など、すべての環境で、統合ランタイムをリンクされたセルフホステッドとして構成する必要があります。

## <a name="create-release-for-deployment"></a>デプロイのリリースを作成する 

すべての変更を保存したら、 **[リリースの作成]** を選択してリリースを手動で作成できます。 リリースの作成を自動化するには、[Azure DevOps のリリース トリガー](/azure/devops/pipelines/release/triggers)に関するページを参照してください。

   ![[Create release]\(リリースの作成\) の選択](media/release-creation-manually.png)

## <a name="best-practices-for-cicd"></a>CI/CD のベスト プラクティス

Synapse ワークスペースとの Git 統合を使用していて、変更を開発からテストを経て運用環境に移行する CI/CD パイプラインがある場合は、次のベスト プラクティスをお勧めします。

-   **Git 統合**。 Git 統合で開発 Synapse ワークスペースのみを構成します。 テスト ワークスペースと運用ワークスペースへの変更は CI/CD を介してデプロイされるので、Git 統合は必要ありません。
-   **アーティファクトの移行前にプールを準備します**。 SQL スクリプトまたはノートブックが開発ワークスペース内のプールにアタッチされている場合は、別の環境にも同じ名前のプールが必要です。 
-   **コードとしてのインフラストラクチャ (IaC)** 。 記述型モデルでのインフラストラクチャ (ネットワーク、仮想マシン、ロード バランサー、接続トポロジ) の管理であり、DevOps チームがソース コードに使用するものと同じバージョン管理を使用します。 
-   **その他**。 [ADF アーティファクトのベスト プラクティス](../../data-factory/continuous-integration-deployment.md#best-practices-for-cicd)に関するページを参照してください

## <a name="troubleshooting-artifacts-deployment"></a>アーティファクト デプロイのトラブルシューティング 

### <a name="use-the-synapse-workspace-deployment-task"></a>Synapse ワークスペース デプロイ タスクを使用する

Synapse では、あらゆる種類のアーティファクトが、ADF の場合とは異なり、ARM リソースではありません。 ARM テンプレート デプロイ タスクを使用して Synapse アーティファクトをデプロイすることはできません
 
### <a name="unexpected-token-error-in-release"></a>リリースにおける予期しないトークン エラー

エスケープ処理されていないパラメーター値がパラメーター ファイルに含まれているとき、予期しないトークン エラーにより、リリース パイプラインでファイルを解析できないことがあります。 パラメーターまたは KeyVault をオーバーライドしてパラメーターを取得することをお勧めします。 回避策としてダブル エスケープすることもできます。