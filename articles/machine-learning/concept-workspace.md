---
title: ワークスペースとは
titleSuffix: Azure Machine Learning
description: ワークスペースは、Azure Machine Learning の最上位のリソースです。 スクリプトのログ、メトリック、出力、スナップショットとともに、すべてのトレーニング実行の履歴も保持されています。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sgilley
author: sdgilley
ms.date: 09/22/2020
ms.openlocfilehash: bf2419799ad6c229c6ffb891bc588479e134d427
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102500395"
---
# <a name="what-is-an-azure-machine-learning-workspace"></a>Azure Machine Learning ワークスペースとは

ワークスペースは、Azure Machine Learning の最上位のリソースで、Azure Machine Learning を使用するときに作成するすべての成果物を操作するための一元的な場所を提供します。  ワークスペースには、スクリプトのログ、メトリック、出力、スナップショットなど、すべてのトレーニング実行の履歴が保持されます。 この情報を使用して、最適なモデルを生成するトレーニング実行を判断します。  

必要なモデルを作成したら、ワークスペースに登録します。 次に、登録済みモデルとスコアリング スクリプトを使用して、Azure Container Instances、Azure Kubernetes Service、または Field-Programmable Gate Array (FPGA) に REST ベースの HTTP エンドポイントとしてデプロイします。 モジュールとして Azure IoT Edge デバイスにモデルをデプロイすることもできます。

## <a name="taxonomy"></a>分類 

ワークスペースの分類を次の図に示します。

[![ワークスペースの分類](./media/concept-workspace/azure-machine-learning-taxonomy.png)](./media/concept-workspace/azure-machine-learning-taxonomy.png#lightbox)

この図は、ワークスペースの次のコンポーネントを示しています。

+ ワークスペースには、Azure Machine Learning の実行に必要な Python 環境で構成されたクラウド リソースである [Azure Machine Learning コンピューティング インスタンス](concept-compute-instance.md)を含めることができます。

+ [ユーザー ロール](how-to-assign-roles.md)を使用すると、お使いのワークスペースを他のユーザー、チーム、またはプロジェクトと共有できます。
+ [コンピューティング ターゲット](concept-azure-machine-learning-architecture.md#compute-targets)は、ご自身の実験の実行に使用されます。
+ ワークスペースを作成すると、[関連するリソース](#resources)も自動的に作成されます。
+ [実験](concept-azure-machine-learning-architecture.md#experiments)は、ご自身のモデルの構築に使用するトレーニング実行です。  
+ [パイプライン](concept-azure-machine-learning-architecture.md#ml-pipelines)は、お使いのモデルをトレーニングおよび再トレーニングするための再利用可能なワークフローです。
+ [データセット](concept-azure-machine-learning-architecture.md#datasets-and-datastores)は、モデルのトレーニングとパイプラインの作成に使用するデータの管理に役立ちます。
+ デプロイするモデルを作成したら、登録済みモデルを作成します。
+ 登録したモデルとスコアリング スクリプトを使用して、[デプロイ エンドポイント](concept-azure-machine-learning-architecture.md#endpoints)を作成します。

## <a name="tools-for-workspace-interaction"></a>ワークスペース操作のためのツール

ご自身のワークスペースは、次の方法で操作できます。

> [!IMPORTANT]
> 以下に "(プレビュー)" と付記されているツールは、現在、パブリック プレビュー段階です。
> プレビュー バージョンはサービス レベル アグリーメントなしで提供されています。運用環境のワークロードに使用することはお勧めできません。 特定の機能はサポート対象ではなく、機能が制限されることがあります。 詳しくは、[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)に関するページをご覧ください。

+ Web 上:
    + [Azure Machine Learning Studio ](https://ml.azure.com) 
    + [Azure Machine Learning デザイナー](concept-designer.md) 
+ [Azure Machine Learning SDK for Python](/python/api/overview/azure/ml/intro) を使用したすべての Python 環境で。
+ [Azure Machine Learning SDK for R (プレビュー)](https://azure.github.io/azureml-sdk-for-r/reference/index.html) を使用したすべての R 環境で。
+ コマンド ラインでは Azure Machine Learning [CLI 拡張機能](./reference-azure-machine-learning-cli.md)を使用
+ [Azure Machine Learning VS Code 拡張機能](how-to-manage-resources-vscode.md#workspaces)


## <a name="machine-learning-with-a-workspace"></a>ワークスペースを使用した機械学習

機械学習タスクでは、お使いのワークスペースに対する成果物の読み取り/書き込みが行われます。

+ 実験を実行してモデルをトレーニングします - 実験の実行結果がワークスペースに書き込まれます。
+ 自動 ML を使用してモデルをトレーニングします - トレーニング結果がワークスペースに書き込まれます。
+ ワークスペースでモデルを登録します。
+ モデルをデプロイします - 登録したモデルを使用してデプロイが作成されます。
+ 再利用可能なワークフローを作成して実行します。
+ 実験、パイプライン、モデル、デプロイなどの機械学習の成果物を表示します。
+ モデルを追跡および監視します。

## <a name="workspace-management"></a>ワークスペースの管理

次のワークスペース管理タスクを実行することもできます。

| ワークスペース管理タスク   | ポータル              | スタジオ | Python SDK/R SDK       | CLI        | VS Code
|---------------------------|---------|---------|------------|------------|------------|
| ワークスペースの作成        | **&check;**     | | **&check;** | **&check;** | **&check;** |
| ワークスペース アクセスの管理    | **&check;**   || |  **&check;**    ||
| コンピューティング リソースを作成して管理する    | **&check;**   | **&check;** | **&check;** |  **&check;**   ||
| ノートブック VM を作成する |   | **&check;** | |     ||

> [!WARNING]
> Azure Machine Learning ワークスペースを別のサブスクリプションに移動したり、所有するサブスクリプションを新しいテナントに移動したりすることは、サポートされていません。 エラーの原因になります。

## <a name="create-a-workspace"></a><a name='create-workspace'></a> ワークスペースを作成する

ワークスペースは、さまざまな方法で作成できます。  

* ポイント アンド クリック形式のインターフェイスで各手順の作業を行う場合は、[Azure portal](how-to-manage-workspace.md?tabs=azure-portal#create-a-workspace) を使用します。
* Python スクリプトまたは Jupyter Notebook からオンザフライでワークスペースを作成するには、[Azure Machine Learning SDK for Python](how-to-manage-workspace.md?tabs=python#create-a-workspace) を使用します。
* 会社のセキュリティ標準に沿って作成を自動化またはカスタマイズするには、[Azure Resource Manager テンプレート](how-to-create-workspace-template.md)または [Azure Machine Learning CLI](reference-azure-machine-learning-cli.md) を使用します。
* Visual Studio Code で作業する場合は、[VS Code 拡張機能](how-to-manage-resources-vscode.md#create-a-workspace)を使用します。

> [!NOTE]
> ワークスペース名では、大文字と小文字は区別されません。

## <a name="associated-resources"></a><a name="resources"></a> 関連するリソース

新しいワークスペースを作成すると、ワークスペースによって使用される複数の Azure リソースが自動的に作成されます。

+ [Microsoft Azure Storage アカウント](https://azure.microsoft.com/services/storage/):ワークスペースの既定のデータストアとして使用されます。  Azure Machine Learning コンピューティングインスタンスで使用される Jupyter ノートブックもここに保存されます。 
  
  > [!IMPORTANT]
  > 既定では、ストレージ アカウントは汎用 v1 アカウントです。 ワークスペースの作成後に、[これを汎用 v2 にアップグレード](../storage/common/storage-account-upgrade.md)することができます。 汎用 v2 にアップグレードした後は、ストレージ アカウントで階層型名前空間を有効にしないでください。

  既存の Azure Storage アカウントを使用する場合は、Premium アカウント (Premium_LRS と Premium_GRS) であってはいけません。 また、階層的名前空間 (Azure Data Lake Storage Gen2 で使用されます) を含めることもできません。 ワークスペースの _既定の_ ストレージ アカウントでは、Premium Storage と階層型名前空間はサポートされていません。 "_既定以外_" のストレージ アカウントでは、Premium Storage または階層型名前空間を使用できます。
  
+ [Azure Container Registry](https://azure.microsoft.com/services/container-registry/):トレーニング中およびモデルのデプロイ時に使用する Docker コンテナーを登録します。 コストを最小限に抑えるために、ACR は、デプロイ イメージが作成されるまで **遅延読み込み** されます。

+ [Azure Application Insights](https://azure.microsoft.com/services/application-insights/):モデルに関する監視情報を格納します。

+ [Azure Key Vault](https://azure.microsoft.com/services/key-vault/):コンピューティング先で使用されるシークレット、およびワークスペースで必要な他の機密情報を格納します。

> [!NOTE]
> 代わりに、[Python SDK](how-to-manage-workspace.md?tabs=python#create-a-workspace)、[R SDK](https://azure.github.io/azureml-sdk-for-r/reference/create_workspace.html)、または Azure Machine Learning CLI ([ARM テンプレートを使用](how-to-create-workspace-template.md)) を使用してワークスペースを作成するときに、既存の Azure リソース インスタンスを使用することができます。

<a name="wheres-enterprise"></a>

## <a name="what-happened-to-enterprise-edition"></a>Enterprise Edition の変更点

2020 年 9 月の時点で、Enterprise Edition ワークスペースで利用できるすべての機能は、Basic Edition ワークスペースでも使用できるようになりました。 新しいエンタープライズ ワークスペースは作成できなくなりました。  `sku` パラメーターを使用する SDK、CLI、または Azure Resource Manager の呼び出しは引き続き機能しますが、Basic ワークスペースがプロビジョニングされます。

12 月 21 日以降、すべての Enterprise Edition ワークスペースが自動的に、同じ機能を備えている Basic Edition に設定されます。 この処理中にダウンタイムは発生しません。 2021 年 1 月 1 日に、Enterprise Edition は正式に廃止されます。 

どちらのエディションでも、お客様は使用されている Azure リソースのコストに責任を持ち、Azure Machine Learning の追加料金を支払う必要はありません。 詳細については、[Azure Machine Learning の価格に関するページ](https://azure.microsoft.com/pricing/details/machine-learning/)を参照してください。

## <a name="next-steps"></a>次のステップ

Azure Machine Learning の利用を開始するには、以下を参照してください。

+ [Azure Machine Learning の概要](overview-what-is-azure-ml.md)
+ [ワークスペースの作成と管理](how-to-manage-workspace.md)
+ [チュートリアル: ](tutorial-1st-experiment-sdk-setup-local.md)個人の開発環境で Azure Machine Learning の使用を開始する
+ [チュートリアル: コンピューティング インスタンスで初めての ML 実験の作成を開始する](tutorial-1st-experiment-sdk-setup.md)
+ [チュートリアル:自動機械学習を使用して最初の分類モデルを作成する](tutorial-first-experiment-automated-ml.md) 
+ [チュートリアル:デザイナーを使用して自動車の価格を予測する](tutorial-designer-automobile-price-train-score.md)
