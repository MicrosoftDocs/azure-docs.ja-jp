---
title: ML パイプラインとは
titleSuffix: Azure Machine Learning service
description: この記事では、Azure Machine Learning SDK for Python を使用して構築できる機械学習パイプラインと、パイプラインを使用する利点について説明します。 機械学習 (ML) パイプラインは、データ サイエンティストが機械学習ワークフローを構築、最適化、管理するために使用します。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: sanpil
author: sanpil
ms.date: 08/08/2019
ms.custom: seodec18
ms.openlocfilehash: 22d1da4c194b392993b37b16ab20673120c3362e
ms.sourcegitcommit: acffa72239413c62662febd4e39ebcb6c6c0dd00
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/12/2019
ms.locfileid: "68951796"
---
# <a name="what-are-ml-pipelines-in-azure-machine-learning-service"></a>Azure Machine Learning service の ML パイプラインとは

Azure Machine Learning service を使用して構築および管理できる機械学習パイプラインについて説明します。 

機械学習 (ML) パイプラインを使用すると、データ サイエンティスト、データ エンジニア、および IT プロフェッショナルは、以下に含まれるステップで共同作業を行うことができます。
+ データ準備 (正規化や変換など)
+ モデル トレーニング
+ モデルの評価
+ Deployment

[最初のパイプラインを作成する](how-to-create-your-first-pipeline.md)方法を学習します。

![Azure Machine Learning サービスでの機械学習パイプライン](./media/concept-ml-pipelines/pipeline-flow.png)

<a name="compare"></a>
### <a name="which-azure-pipeline-technology-should-i-use"></a>どの Azure パイプライン テクノロジを使用すべきか

Azure クラウドでは、目的が異なるさまざまなパイプラインが提供されています。 次の表に、それらのさまざまなパイプラインとその用途を示します。

| パイプライン | 実行内容 | Canonical パイプ |
| ---- | ---- | ---- |
| Azure Machine Learning パイプライン | 機械学習シナリオ用のテンプレートとして使用できる、再利用可能な機械学習ワークフローを定義します。 | データ -> モデル |
| [Azure Data Factory のパイプライン](https://docs.microsoft.com/azure/data-factory/concepts-pipelines-activities) | 1 つのタスクを実行するために必要なデータ移動、変換、および制御アクティビティをグループ化します。  | データ -> データ |
| [Azure パイプライン](https://azure.microsoft.com/services/devops/pipelines/) | 任意のプラットフォーム/任意のクラウドへの自分のアプリケーションの継続的インテグレーションとデリバリー  | コード -> アプリ/サービス |

## <a name="why-build-pipelines-with-azure-machine-learning"></a>Azure Machine Learning を使用してパイプラインを構築する理由

機械学習パイプラインによって、速度、移植性、再利用に関するワークフローが最適化されます。その結果、インフラストラクチャや自動化ではなく、お客様の専門知識や機械学習に専念できます。

パイプラインは、パイプライン内の個別のコンピューティング単位である複数の**ステップ**から構成されます。 各ステップは個別に実行でき、分離されたコンピューティングリソースを使用できます。 これにより、複数のデータ サイエンティストが、コンピューティング リソースを過剰に使用することなく、同じパイプラインで同時に作業を行うことができます。また、各ステップに対して異なるコンピューティングの種類/サイズを簡単に使用できるようにします。

パイプラインが設計された後には、多くの場合、パイプラインのトレーニング ループを中心にさらに多くの微調整が行われます。 パイプラインを再実行すると、再実行する必要がある個別のステップ (更新されたトレーニング スクリプトなど) にジャンプし、変更されていないものはスキップされます。 ステップの実行に使用される変更されていないスクリプトにも同じパラダイムが適用されます。 この機能により、基のデータが変更されていない場合に、データ インジェストや変換など、コストや時間のかかる処理を回避できます。

Azure Machine Learning では、パイプライン内の各ステップに対して、PyTorch や TensorFlow などの各種のツールキットとフレームワークを使用できます。 Azure では、使用するさまざまな[コンピューティング ターゲット](concept-azure-machine-learning-architecture.md)間の調整が行われるため、中間データをダウンストリームのコンピューティング ターゲットと容易に共有できます。

Azure portal で直接、[パイプライン実験のメトリックを追跡](https://docs.microsoft.com/azure/machine-learning/service/how-to-track-experiments)できます。 パイプラインを発行した後は、任意のプラットフォームまたはスタックからパイプラインを再実行できる REST エンドポイントを構成できます。

## <a name="key-advantages"></a>主な利点

機械学習ワークフローのパイプラインを使用する主な利点を次に示します。

|主な利点|説明|
|:-------:|-----------|
|**無人&nbsp;実行**|複数のステップを無人で確実に並列実行または順次実行するようにスケジュールします。 データの準備とモデリングには数日または数週間かかる可能性があります。パイプラインを使用すると、このプロセスの実行中に他のタスクに専念できます。 |
|**異種コンピューティング**|スケーラブルな異種コンピューティング リソースとストレージの場所全体で確実に調整された複数のパイプラインを使用します。 個々のパイプライン ステップを、HDInsight、GPU Data Science VM、Databricks などのさまざまなコンピューティング ターゲット上で実行します。 これにより、使用可能なコンピューティング オプションを効率的に使用できます。|
|**再利用**|再トレーニングやバッチ スコアリングなどの特定のシナリオ向けにパイプライン テンプレートを作成します。 単純な REST 呼び出しを使用して外部システムから発行されたパイプラインをトリガーします。|
|**追跡とバージョン管理**|反復処理しながらデータや結果パスを手動で追跡するのではなく、パイプライン SDK を使用して、データ ソース、入力、および出力に明示的に名前を付けてバージョン管理します。 また、生産性を向上させるために、スクリプトとデータを個別に管理することもできます。|
|**コラボレーション**|パイプラインを使用すると、データ科学者は、機械学習の設計プロセスのすべてのリージョンで共同作業を行うことができます。さらに、パイプラインの複数のステップに同時に取り組むこともできます。|

## <a name="the-python-sdk-for-pipelines"></a>パイプライン用 Python SDK

[Python SDK](https://docs.microsoft.com/python/api/azureml-pipeline-core/?view=azure-ml-py) を使用して、好みの IDE または Jupyter ノートブックで ML パイプラインを作成します。 Azure Machine Learning SDK では、データ依存関係が存在しない場合に、パイプラインのステップを順次実行および並列実行するための命令型のコンストラクトを提供します。 

宣言型のデータ依存関係を使用して、タスクを最適化できます。 この SDK には、データ転送やモデルの公開などの一般的なタスク用に事前に構築されたモジュールのフレームワークが含まれています。 パイプライン間で再利用可能なカスタム ステップを実装することにより、フレームワークを拡張して独自の規則をモデル化できます。 また、コンピューティング ターゲットとストレージ リソースを SDK から直接管理することもできます。

パイプラインをテンプレートとして保存し、バッチスコアリング ジョブまたは再トレーニング ジョブのために REST エンドポイントにデプロイします。

Azure Machine Learning には、[azureml-pipelines-core](https://docs.microsoft.com/python/api/azureml-pipeline-core/?view=azure-ml-py) と [azureml-pipeline-steps](https://docs.microsoft.com/en-us/python/api/azureml-pipeline-steps/?view=azure-ml-py) という 2 つのパイプライン用 Python パッケージがあります。

## <a name="next-steps"></a>次の手順

+ [最初のパイプラインを作成する](how-to-create-your-first-pipeline.md)方法を学習します。

+ [大量のデータに対してバッチ予測を実行する](how-to-run-batch-predictions.md)方法を学習します。

+ [パイプラインの SDK リファレンス ドキュメント](https://docs.microsoft.com/python/api/azureml-pipeline-core/?view=azure-ml-py)を読みます。

+ [Azure Machine Learning パイプライン](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/machine-learning-pipelines)を紹介する Jupyter ノートブックの例を試します。 [ノートブックを実行してこのサービスを調べる](samples-notebooks.md)方法を学習します。
