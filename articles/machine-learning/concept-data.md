---
title: クラウドでのデータ アクセスをセキュリティ保護する
titleSuffix: Azure Machine Learning
description: Azure Machine Learning からデータに安全に接続する方法と、データセットおよびデータストアを ML タスクに使用する方法について説明します。 データストアには、Azure Blob、Azure Data Lake Gen 1 および 2、SQL DB、Databricks などからのデータを格納できます。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: nibaccam
author: nibaccam
ms.author: nibaccam
ms.date: 04/24/2020
ms.custom: devx-track-python
ms.openlocfilehash: dadd3a8316efc5bf090a84a738c8f6da223d4572
ms.sourcegitcommit: 271601d3eeeb9422e36353d32d57bd6e331f4d7b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/20/2020
ms.locfileid: "88651796"
---
# <a name="secure-data-access-in-azure-machine-learning"></a>Azure Machine Learning でのデータ アクセスをセキュリティ保護する

Azure Machine Learning を使用すると、クラウド内のデータに簡単に接続できます。  基になるストレージ サービスに抽象レイヤーが用意されているため、ストレージの種類に固有のコードを記述しなくても、データに安全にアクセスし、操作することができます。 Azure Machine Learning には、次のデータ機能も用意されています。

*    データ系列のバージョン管理と追跡
*    データのラベル付け 
*    データの誤差の監視
*    Pandas および Spark DataFrames による相互運用性

## <a name="data-workflow"></a>データ ワークフロー

クラウドベースのストレージ ソリューションでデータを使用できる場合は、次のデータ配信ワークフローをお勧めします。 このワークフローでは、Azure のクラウドベースのストレージ サービスに [Azure ストレージアカウント](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account?tabs=azure-portal)とデータがあることを前提としています。 

1. 接続情報をご自身の Azure ストレージに格納するための [Azure Machine Learning データストア](#datastores)を作成します。

2. そのデータストアから [Azure Machine Learning データセット](#datasets)を作成し、基になるお使いのストレージにある特定のファイルを指すように指定します。 

3. そのデータセットを機械学習の実験で使用するには、以下のいずれかを実行します
    1. モデル トレーニングのために実験のコンピューティング先にマウントします。

        **OR** 

    1. 自動機械学習 (自動 ML) 実験の実行、機械学習パイプライン、または [Azure Machine Learning デザイナー](concept-designer.md)などの Azure Machine Learning ソリューションで直接使用します。

4. データ ドリフトを検出するために、モデルの出力データセット用の[データセット モニター](#data-drift)を作成します。 

5. データ ドリフトが検出された場合は、入力データセットを更新し、それに応じてモデルを再トレーニングします。

次の図は、この推奨ワークフローの視覚的なデモンストレーションを示しています。

![データの概念図](./media/concept-data/data-concept-diagram.svg)

## <a name="datastores"></a>データストア

Azure Machine Learning データストアには、Azure ストレージ サービスへの接続情報が安全に保持されるため、ご自身のスクリプトでそのコードを書く必要はありません。 ストレージ アカウントに簡単に接続し、下位の Azure ストレージ サービスのデータにアクセスするために、[データストアを登録および作成します](how-to-access-data.md)。 

データストアとして登録可能な、Azure 内でサポートされているクラウドベースのストレージ サービスは次のとおりです。

+ Azure BLOB コンテナー
+ Azure ファイル共有
+ Azure Data Lake
+ Azure Data Lake Gen2
+ Azure SQL データベース
+ Azure Database for PostgreSQL
+ Databricks ファイル システム
+ Azure Database for MySQL

## <a name="datasets"></a>データセット

Azure Machine Learning データセットは、ストレージ サービス内のデータを指定する参照です。 これらはデータのコピーではないため、追加のストレージ コストは発生しません。また、元のデータ ソースの整合性が危険にさらされることもありません。

 ストレージ内のデータを操作するには、お使いのデータを、機械学習タスク用の使用可能なオブジェクトとしてパッケージ化するために、[データセットを作成](how-to-create-register-datasets.md)します。 データ インジェストが複雑になることなく、さまざまな実験でデータセットを共有して再利用できるように、データセットをワークスペースに登録します。

データセットは、ローカル ファイル、パブリック URL、[Azure Open Datasets](https://azure.microsoft.com/services/open-datasets/)、または Azure ストレージ サービスから、データストアを介して作成できます。 イン メモリの pandas データ フレームからデータセットを作成するには、parquet などのローカル ファイルにデータを書き込み、そのファイルからデータセットを作成します。  

2 種類のデータセットがサポートされています。 

+ [FileDataset](https://docs.microsoft.com/python/api/azureml-core/azureml.data.file_dataset.filedataset?view=azure-ml-py) は、データストアまたはパブリック URL 内の 1 つまたは複数のファイルを参照します。 データがクレンジング済みで、トレーニング実験で使用できる状態になっている場合は、コンピューティング先に、FileDatasets によって参照されている[ファイルをダウンロードまたはマウント](how-to-train-with-datasets.md#mount-files-to-remote-compute-targets)できます。

+ [TabularDataset](https://docs.microsoft.com/python/api/azureml-core/azureml.data.tabulardataset?view=azure-ml-py) は、指定されたファイルまたはファイルのリストを解析して、データを表形式で表します。 TabularDataset を Pandas または Spark DataFrame に読み込んで、さらに操作とクレンジングを行うことができます。 TabularDatasets を作成できるデータ形式の完全な一覧については、[TabularDatasetFactory クラス](https://aka.ms/tabulardataset-api-reference)に関するページをご覧ください。

追加のデータセット機能については、次のドキュメントを参照してください。

+ データセット系列に[バージョンをつけて追跡する](how-to-version-track-datasets.md)。
+ [データセットを監視](how-to-monitor-datasets.md)して、データ ドリフトの検出に役立てる。    

## <a name="work-with-your-data"></a>データを処理する

データセットを使用すると、Azure Machine Learning 機能とのシームレスな統合を通じて、数多くの機械学習タスクを実行できます。 

+ [データのラベル付けプロジェクト](#label)を作成します。
+ 機械学習モデルをトレーニングします。
     + [自動 ML の実験](how-to-use-automated-ml-for-ml-models.md)
     + [デザイナー](tutorial-designer-automobile-price-train-score.md#import-data)
     + [ノートブック](how-to-train-with-datasets.md)
     + [Azure Machine Learning パイプライン](how-to-create-your-first-pipeline.md)
+ [機械学習パイプライ](how-to-create-your-first-pipeline.md)ンでの[バッチ推論](how-to-use-parallel-run-step.md)によるスコアリングのためのデータセットにアクセスします。
+ [データ ドリフト](#drift)の検出のためのデータセット モニターを設定します。

<a name="label"></a>

## <a name="data-labeling"></a>データのラベル付け

大量のデータにラベルを付けることは、多くの場合、機械学習プロジェクトでは困難でした。 画像の分類やオブジェクトの検出など、Computer Vision コンポーネントを使用するそれらには、通常、何千ものイメージとそれに対応するラベルが必要です。

Azure Machine Learning を使用すると、ラベル付けプロジェクトの作成、管理、監視を一元的に行うことができます。 ラベル付けプロジェクトは、データ、ラベル、チーム メンバーの間の調整に役立ち、ラベル付けタスクをより効率的に管理できるようになります。 現在サポートされているタスクは、複数ラベルまたは多クラスでの画像分類と、境界ボックスを使用したオブジェクト識別です。

[データラベル付けプロジェクト](how-to-create-labeling-projects.md)を作成し、機械学習の実験で使用するためのデータセットを出力します。

<a name="drift"></a>

## <a name="data-drift"></a>データ ドリフト

機械学習においてデータの誤差とは、モデルのパフォーマンスの低下につながるモデルの入力データの変更のことです。 これはモデルの精度が時間の経過と共に低下する主な理由の 1 つであるため、データ ドリフトの監視はモデルのパフォーマンスに関する問題の検出に役立ちます。

データセット内の新しいデータに対するデータ ドリフトを検出してアラートを生成する方法の詳細については、[データセット モニターの作成](how-to-monitor-datasets.md)に関する記事を参照してください。

## <a name="next-steps"></a>次のステップ 

+ [こちらの手順を使用](how-to-create-register-datasets.md)して、Azure Machine Learning Studio に、または Python SDK でデータセットを作成します。
+ [サンプル ノートブック](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/work-with-data/)を使用して、データセットのトレーニング例を試してみてください。
