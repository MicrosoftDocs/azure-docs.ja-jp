---
title: 'パイプライン: 機会学習ワークフローの最適化'
titleSuffix: Azure Machine Learning service
description: この記事では、Azure Machine Learning SDK for Python を使用して構築できる機械学習パイプラインと、パイプラインを使用する利点について説明します。 機械学習 (ML) パイプラインは、データ サイエンティストが機械学習ワークフローを構築、最適化、管理するために使用します。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: sanpil
author: sanpil
ms.date: 12/4/2018
ms.custom: seodec18
ms.openlocfilehash: 3f1dd0921153d6b65bdc257f91019483adbb18fa
ms.sourcegitcommit: c712cb5c80bed4b5801be214788770b66bf7a009
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/01/2019
ms.locfileid: "57213672"
---
# <a name="build-machine-learning-pipelines-with-the-azure-machine-learning-service"></a>Azure Machine Learning サービスを使用して機械学習パイプラインを構築する

この記事では、Azure Machine Learning SDK for Python を使用して構築できる機械学習パイプラインと、パイプラインを使用する利点について説明します。

## <a name="what-are-machine-learning-pipelines"></a>機械学習パイプラインとは

機械学習 (ML) パイプラインを使用すると、データ サイエンティスト、データ エンジニア、および IT プロフェッショナルは、以下に含まれるステップで共同作業を行うことができます。
+ データ準備 (正規化や変換など)
+ モデル トレーニング
+ モデルの評価
+ Deployment 

次の図はパイプラインの例を示しています。

![Azure Machine Learning サービスでの機械学習パイプライン](./media/concept-ml-pipelines/pipelines.png)

### <a name="which-azure-pipeline-technology-should-i-use"></a>どの Azure パイプライン テクノロジを使用すべきか

Azure クラウドでは、目的が異なるさまざまなパイプラインが提供されています。 次の表に、それらのさまざまなパイプラインとその用途を示します。

| パイプライン | 実行内容 | Canonical パイプ |
| ---- | ---- | ---- |
| Azure Machine Learning パイプライン | 機械学習シナリオ用のテンプレートとして使用できる、再利用可能な機械学習ワークフローを定義します。 | データ -> モデル |
| [Azure Data Factory のパイプライン](https://docs.microsoft.com/azure/data-factory/concepts-pipelines-activities) | 1 つのタスクを実行するために必要なデータ移動、変換、および制御アクティビティをグループ化します。  | データ -> データ |
| [Azure パイプライン](https://azure.microsoft.com/services/devops/pipelines/) | 任意のプラットフォーム/任意のクラウドへの自分のアプリケーションの継続的インテグレーションとデリバリー  | コード -> アプリ/サービス |

## <a name="why-build-pipelines-with-azure-machine-learning"></a>Azure Machine Learning を使用してパイプラインを構築する理由

[Azure Machine Learning SDK for Python](#the-python-sdk-for-pipelines) を使用すると、ML パイプラインを作成したり、個々のパイプライン実行を送信して追跡したりできます。

パイプラインを使用すると、シンプルさ、速度、移植性、再利用によってワークフローを最適化できます。 Azure Machine Learning を使用してパイプラインを構築する場合は、インフラストラクチャにではなく、専門知識である機械学習に焦点を絞ることができます。

個別のステップを使用すると、ワークフローを調整してテストするときに、必要なステップのみを再実行することが可能になります。 ステップとは、パイプラインにおけるコンピューティング単位です。 前の図に示すように、データを準備するタスクには多数のステップが含まれる場合があります。 これらのステップには正規化、変換、検証、および特徴付けが含まれますが、それだけに限定されるわけではありません。 データ ソースと中間データはパイプライン全体で再利用され、コンピューティングの時間とリソースが節約されます。 

パイプラインが設計された後には、多くの場合、パイプラインのトレーニング ループを中心にさらに多くの微調整が行われます。 パイプラインを再実行すると、再実行する必要があるステップ (更新されたトレーニング スクリプトなど) にジャンプし、変更されていないものはスキップされます。 ステップの実行に使用される変更されていないスクリプトにも同じパラダイムが適用されます。 

Azure Machine Learning では、パイプライン内の各ステップに対して、PyTorch や TensorFlow などの各種のツールキットとフレームワークを使用できます。 Azure では、使用するさまざまな[コンピューティング ターゲット](concept-azure-machine-learning-architecture.md)間の調整が行われるため、中間データをダウンストリームのコンピューティング ターゲットと容易に共有できます。 

Azure portal で直接、[パイプライン実験のメトリックを追跡](https://docs.microsoft.com/azure/machine-learning/service/how-to-track-experiments)できます。 

## <a name="key-advantages"></a>主な利点

機械学習ワークフローのパイプラインを構築する主な利点を次に示します。

|主な利点|説明|
|:-------:|-----------|
|**無人&nbsp;実行**|複数のステップを無人で確実に並列実行または順次実行するようにスケジュールします。 データの準備やモデリングには数日または数週間かかる場合があるため、これにより、パイプラインが実行されている間に他のタスクに集中できるようになります。 |
|**混在した多様なコンピューティング**|スケーラブルな異種コンピューティングおよびストレージ間で確実に調整された複数のパイプラインを使用します。 個々のパイプライン ステップを、HDInsight、GPU Data Science VM、Databricks などのさまざまなコンピューティング ターゲット上で実行できます。 これにより、使用可能なコンピューティング オプションを効率的に使用できます。|
|**再利用**|パイプラインは、再トレーニングやバッチ スコアリングなどの特定のシナリオ向けにテンプレート化できます。 単純な REST 呼び出しを使用して、外部システムからパイプラインをトリガーします。|
|**追跡とバージョン管理**|反復処理しながらデータや結果パスを手動で追跡するのではなく、パイプライン SDK を使用して、データ ソース、入力、および出力に明示的に名前を付けてバージョン管理します。 また、生産性を向上させるために、スクリプトとデータを個別に管理することもできます。|

## <a name="the-python-sdk-for-pipelines"></a>パイプライン用 Python SDK

ML パイプラインを作成するには Python を使用します。 Azure Machine Learning SDK では、データ依存関係が存在しない場合に、パイプラインのステップを順次実行および並列実行するための命令型のコンストラクトを提供します。 それを、Jupyter Notebook や別の望ましい統合開発環境で操作できます。 

宣言型のデータ依存関係を使用して、タスクを最適化できます。 この SDK には、データ転送やモデルの公開などの一般的なタスク用に事前に構築されたモジュールのフレームワークが含まれています。 パイプライン間で再利用可能なカスタム ステップを実装することにより、フレームワークを拡張して独自の規則をモデル化できます。 また、コンピューティング ターゲットとストレージ リソースを SDK から直接管理することもできます。

パイプラインをテンプレートとして保存し、それを REST エンドポイントにデプロイすることができるため、バッチ スコアリングまたは再トレーニング ジョブをスケジュールできます。

独自のパイプラインを構築する方法を確認するには、[パイプラインに関する Python SDK リファレンス ドキュメント](https://docs.microsoft.com/python/api/azureml-pipeline-core/?view=azure-ml-py)および次のセクションの Notebook を参照してください。

## <a name="example-notebooks"></a>サンプルの Notebook
 
[how-to-use-azureml/machine-learning-pipelines](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/machine-learning-pipelines) の Notebook は、Azure Machine Learning を使用したパイプラインを示しています。
 
[!INCLUDE [aml-clone-in-azure-notebook](../../../includes/aml-clone-for-examples.md)]

## <a name="next-steps"></a>次の手順

[最初のパイプラインを作成する](how-to-create-your-first-pipeline.md)方法を学習します。
