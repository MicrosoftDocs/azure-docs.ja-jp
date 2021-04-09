---
title: データのインジェストと自動化
titleSuffix: Azure Machine Learning
description: 機械学習モデルのトレーニングに使用できるデータ インジェスト オプションの長所と短所について説明します。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: nibaccam
author: nibaccam
ms.author: nibaccam
ms.date: 02/26/2020
ms.custom: devx-track-python, data4ml
ms.openlocfilehash: a096375e32e3d8a6760da88fe5ec86a70d364aff
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "98872097"
---
# <a name="data-ingestion-options-for-azure-machine-learning-workflows"></a>Azure Machine Learning ワークフローのデータ インジェスト オプション

この記事では、Azure Machine Learning で使用できるデータ インジェスト オプションの長所と短所について説明します。 

次の中から選択します。
+ データの抽出、読み込み、および変換を目的として構築された [Azure Data Factory](#azure-data-factory) パイプライン

+ データ インジェスト タスク用のカスタム コード ソリューションを提供する [Azure Machine Learning Python SDK](#azure-machine-learning-python-sdk)。

+ 両方の組み合わせ

データ インジェストは、構造化されていないデータを 1 つまたは複数のソースから抽出し、機械学習モデルのトレーニング用に準備するプロセスです。 また、特に手動で行う場合や、複数のソースに大量のデータがある場合は、時間がかかります。 この作業を自動化することで、リソースが解放され、お使いのモデルで最新の適用可能なデータが確実に使用されるようになります。

## <a name="azure-data-factory"></a>Azure Data Factory

[Azure Data Factory](../data-factory/introduction.md) では、データ ソース監視のネイティブ サポートとデータ インジェスト パイプラインのトリガーが提供されています。  

次の表は、データ インジェスト ワークフローで Azure Data Factory を使用する場合の長所と短所をまとめたものです。

|長所|短所
---|---
データの抽出、読み込み、変換を目的として構築されています。|現時点では、Azure Data Factory パイプライン タスクの限定セットが提供されています 
データ移動とデータ変換を大規模に調整するためのデータ駆動型ワークフローを作成できます。|構築と保守にコストがかかります。 詳細については、Azure Data Factory の [価格に関するページ](https://azure.microsoft.com/pricing/details/data-factory/data-pipeline/)を参照してください。
[Azure Databricks](../data-factory/transform-data-using-databricks-notebook.md) や [Azure Functions](../data-factory/control-flow-azure-function-activity.md) など、さまざまな Azure ツールと統合 | スクリプトをネイティブに実行せず、代わりにスクリプトの実行を個別のコンピューティングに依存します 
データ ソースによってトリガーされるデータ インジェストをネイティブにサポートします| 
データ準備とモデル トレーニングのプロセスが異なります。|
Azure Data Factory データフローの埋め込みデータ系列機能|
コードの作成経験が少ない方向けに、スクリプトを使用しない方法として[ユーザー インターフェイス](../data-factory/quickstart-create-data-factory-portal.md)が提供されています |

次の手順と図は、Azure Data Factory のデータ インジェスト ワークフローを示しています。

1. データをソースからプルします
1. データを変換して出力 BLOB コンテナーに保存します。コンテナーは、Azure Machine Learning のデータ ストレージとして機能します
1. 準備したデータを保存すると、Azure Data Factory パイプラインにより、Machine Learning のトレーニング パイプラインが呼び出され、モデル トレーニング用に準備したデータを受け取ります


    ![ADF データ インジェスト](media/concept-data-ingestion/data-ingest-option-one.svg)
    
[Azure Data Factory](how-to-data-ingest-adf.md) を使用して、機械学習のデータ インジェスト パイプラインを作成する方法について説明します。

## <a name="azure-machine-learning-python-sdk"></a>Azure Machine Learning Python SDK 

[Python SDK](/python/api/overview/azure/ml) を使用して、[Azure Machine Learning パイプライン](./how-to-create-machine-learning-pipelines.md)の手順にデータ インジェスト タスクを組み込むことができます。

次の表は、データ インジェスト タスクに SDK と ML パイプラインの手順を使用する場合の長所と短所をまとめたものです。

長所| 短所
---|---
独自の Python スクリプトを構成します | データ ソース変更のトリガーをネイティブでサポートしていません。 Logic App または Azure 関数の実装が必要です
データ準備が、すべてのモデル トレーニング実行に含まれます|データ インジェスト スクリプトを作成するための開発スキルが必要です
[Azure Machine Learning コンピューティング](concept-compute-target.md#azure-machine-learning-compute-managed)など、さまざまなコンピューティング先のデータ準備スクリプトをサポートしています |インジェスト メカニズム作成のユーザー インターフェイスが提供されていません

次の図の Azure Machine Learning パイプラインは、データ インジェストとモデル トレーニングの 2 つの手順で構成されています。 データ インジェストの手順には、Python ライブラリと Python SDK を使用して実行できるタスクが含まれます。たとえば、ローカル ソースや Web ソースからのデータの抽出、欠損値の補完のようなデータ変換などです。 次に、トレーニングの手順で、準備したデータをトレーニング スクリプトへの入力として使用して、機械学習モデルをトレーニングします。 

![Azure パイプライン + SDK データ インジェスト](media/concept-data-ingestion/data-ingest-option-two.png)

## <a name="next-steps"></a>次のステップ

以下のハウツー記事に従ってください。
* [Azure Data Factory を使用してデータ インジェスト パイプラインを作成する](how-to-data-ingest-adf.md)

* [Azure Pipelines を使用して、データ インジェスト パイプラインを自動化および管理する](how-to-cicd-data-ingestion.md)