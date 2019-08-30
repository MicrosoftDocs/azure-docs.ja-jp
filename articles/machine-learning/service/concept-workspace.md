---
title: ワークスペースとは
titleSuffix: Azure Machine Learning service
description: ワークスペースは、Azure Machine Learning service の最上位のリソースです。 スクリプトのログ、メトリック、出力、スナップショットなど、すべてのトレーニング実行の履歴も保持されています。 この情報を使用して、最適なモデルを生成するトレーニング実行を判断します
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sgilley
author: sdgilley
ms.date: 08/06/2019
ms.openlocfilehash: cb1fd8e98a5eba350774ff6ccb8f86dcd3e4d734
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/08/2019
ms.locfileid: "68856210"
---
# <a name="what-is-an-azure-machine-learning-service-workspace"></a>Azure Machine Learning service ワークスペースとは

ワークスペースは、Azure Machine Learning service の最上位のリソースで、Azure Machine Learning service を使用するときに作成するすべての成果物を操作するための一元的な場所を提供します。  ワークスペースには、スクリプトのログ、メトリック、出力、スナップショットなど、すべてのトレーニング実行の履歴が保持されます。 この情報を使用して、最適なモデルを生成するトレーニング実行を判断します。  

必要なモデルを作成したら、ワークスペースに登録します。 次に、登録済みモデルとスコアリング スクリプトを使用して、Azure Container Instances、Azure Kubernetes Service、または Field-Programmable Gate Array (FPGA) に REST ベースの HTTP エンドポイントとしてデプロイします。 モジュールとして Azure IoT Edge デバイスにモデルをデプロイすることもできます。

## <a name="taxonomy"></a>分類 

ワークスペースの分類を次の図に示します。

[![ワークスペースの分類](./media/concept-azure-machine-learning-architecture/azure-machine-learning-taxonomy.png)](./media/concept-azure-machine-learning-architecture/azure-machine-learning-taxonomy.png#lightbox)

この図は、ワークスペースの次のコンポーネントを示しています。

+ ワークスペースには、Azure Machine Learning の実行に必要な Python 環境で構成されたクラウド リソース、[Notebook VM](tutorial-1st-experiment-sdk-setup.md) を含めることができます。
+ [ユーザー ロール](how-to-assign-roles.md)を使用すると、お使いのワークスペースを、他のユーザー、チーム、またはプロジェクトと共有できます。
+ [コンピューティング ターゲット](concept-azure-machine-learning-architecture.md#compute-targets)は、ご自身の実験の実行に使用されます。
+ ワークスペースを作成すると、[関連するリソース](#resources)も自動的に作成されます。
+ [実験](concept-azure-machine-learning-architecture.md#experiments)は、ご自身のモデルの構築に使用するトレーニング実行です。  以下を使用して、実験を作成および実行できます
    + [Azure Machine Learning SDK for Python](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)。
    + Azure portal の[自動化された機械学習の実験 (プレビュー)](how-to-create-portal-experiments.md) セクション。
    + [ビジュアル インターフェイス (プレビュー)](ui-concept-visual-interface.md)。
+ [パイプライン](concept-azure-machine-learning-architecture.md#ml-pipelines)は、お使いのモデルをトレーニングおよび再トレーニングするための再利用可能なワークフローです。
+ [データセット](concept-azure-machine-learning-architecture.md#datasets-and-datastores)は、モデルのトレーニングとパイプラインの作成に使用するデータの管理に役立ちます。
+ デプロイするモデルを作成したら、登録済みモデルを作成します。
+ 登録したモデルとスコアリング スクリプトを使用して、[デプロイ](concept-azure-machine-learning-architecture.md#deployment)を作成します。

## <a name="tools-for-workspace-interaction"></a>ワークスペース操作のためのツール

ご自身のワークスペースは、次の方法で操作できます。

+ Web 上:
    + [Azure Portal](https://portal.azure.com)
    + [ビジュアル インターフェイス (プレビュー)](ui-concept-visual-interface.md)
+ Python では Azure Machine Learning [SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py) を使用
+ コマンド ラインでは Azure Machine Learning [CLI 拡張機能](https://docs.microsoft.com/azure/machine-learning/service/reference-azure-machine-learning-cli)を使用

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

| ワークスペース管理タスク   | ポータル              | SDK        | CLI        |
|---------------------------|------------------|------------|------------|
| ワークスペースの作成        | **&check;**     | **&check;** | **&check;** |
| コンピューティング リソースを作成して管理する    | **&check;**   | **&check;** |  **&check;**   |
| ワークスペース アクセスの管理    | **&check;**   | |  **&check;**    |
| ノートブック VM を作成する | **&check;**   | |     |

### <a name='create-workspace'></a> ワークスペースを作成する

ワークスペースは、さまざまな方法で作成できます。

* ポイント アンド クリック形式のインターフェイスで各手順の作業を行う場合は、[Azure portal](how-to-manage-workspace.md) を使用します。
* Python スクリプトまたは Jupiter Notebook からオンザフライでワークスペースを作成するには、[Azure Machine Learning SDK for Python](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py#workspace) を使用します。
* 会社のセキュリティ標準に沿って作成を自動化またはカスタマイズするには、[Azure Resource Manager テンプレート](how-to-create-workspace-template.md)または [Azure Machine Learning CLI](reference-azure-machine-learning-cli.md) を使用します。
* Visual Studio Code で作業する場合は、[VS Code 拡張機能](how-to-vscode-tools.md#get-started-with-azure-machine-learning)を使用します。

## <a name="resources"></a> 関連するリソース

新しいワークスペースを作成すると、ワークスペースによって使用される複数の Azure リソースが自動的に作成されます。

+ [Azure Container Registry](https://azure.microsoft.com/services/container-registry/):トレーニング中およびモデルのデプロイ時に使用する Docker コンテナーを登録します。 コストを最小限に抑えるために、ACR は、デプロイ イメージが作成されるまで**遅延読み込み**されます。
+ [Microsoft Azure Storage アカウント](https://azure.microsoft.com/services/storage/):ワークスペースの既定のデータストアとして使用されます。  ノートブック VM で使用される Jupyter ノートブックもここに格納されます。
+ [Azure Application Insights](https://azure.microsoft.com/services/application-insights/):モデルに関する監視情報を格納します。
+ [Azure Key Vault](https://azure.microsoft.com/services/key-vault/):コンピューティング先で使用されるシークレット、およびワークスペースで必要な他の機密情報を格納します。

> [!NOTE]
> 新しいバージョンを作成するだけでなく、既存の Azure サービスを使用することもできます。

## <a name="next-steps"></a>次の手順

Azure Machine Learning service の利用を開始する場合は、以下を参照してください。

+ [Azure Machine Learning service の概要](overview-what-is-azure-ml.md)
+ [ワークスペースを作成する](how-to-manage-workspace.md)
+ [ワークスペースを管理する](how-to-manage-workspace.md)
+ [チュートリアル:モデルをトレーニングする](tutorial-train-models-with-aml.md)
