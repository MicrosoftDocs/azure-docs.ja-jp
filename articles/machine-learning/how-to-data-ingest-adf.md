---
title: Azure Data Factory を使用したデータ インジェスト
titleSuffix: Azure Machine Learning
description: Azure Data Factory を使用してデータ インジェスト パイプラインを作成する方法について説明します。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: iefedore
author: eedorenko
manager: davete
ms.reviewer: larryfr
ms.date: 03/01/2020
ms.topic: conceptual
ms.custom: how-to, devx-track-python
ms.openlocfilehash: b756e83f1d810007e9e9ef6cf2987c3cf60b7f7d
ms.sourcegitcommit: 7fe8df79526a0067be4651ce6fa96fa9d4f21355
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/06/2020
ms.locfileid: "87852856"
---
# <a name="data-ingestion-with-azure-data-factory"></a>Azure Data Factory を使用したデータ インジェスト

この記事では、Azure Data Factory (ADF) を使用して、データ インジェスト パイプラインを作成する方法について説明します。 このパイプラインは、Azure Machine Learning で使用するデータを取り込むために使用されます。 Azure Data Factory を使用すると、データの抽出、変換、読み込み (ETL) を簡単に実行できます。 変換されてストレージに読み込まれたデータは、機械学習モデルのトレーニングに使用できます。

単純なデータ変換は、[データ フロー](https://docs.microsoft.com/azure/data-factory/control-flow-execute-data-flow-activity)などのネイティブ ADF アクティビティとインストルメントを使用して処理できます。 より複雑なシナリオについては、何らかのカスタム コードを使用してデータを処理できます。 たとえば、Python や R コードです。

インジェスト中にデータを変換するために Azure Data Factory を使用する一般的な手法がいくつかあります。 各手法には、長所と短所があり、特定のユース ケースに適しているかどうかがわかります。

| 手法 | 長所 | 短所 |
| ----- | ----- | ----- |
| ADF と Azure Functions | 短い待機時間、サーバーレス コンピューティング</br>ステートフル関数</br>再利用可能な関数 | 実行時間の短い処理にのみ適している |
| ADF とカスタム コンポーネント | 大規模な並列コンピューティング</br>大量のアルゴリズムに適している | 実行可能ファイルにコードをラップする</br>依存関係と IO の処理が複雑である |
| ADF と Azure Databricks ノートブック | Apache Spark</br>ネイティブ Python 環境 | コストがかかる可能性がある</br>クラスターの初回作成に時間がかかり、待機時間が増加する

## <a name="adf-with-azure-functions"></a>ADF と Azure Functions

![adf-function](media/how-to-data-ingest-adf/adf-function.png)

Azure Functions を使用すると、アプリケーションのインフラストラクチャについて気にすることなく、小規模なコード (関数) を実行できます。 このオプションでは、Azure 関数にラップされたカスタム Python コードを使用してデータが処理されます。 

この関数は、[ADF の Azure 関数アクティビティ](https://docs.microsoft.com/azure/data-factory/control-flow-azure-function-activity)を使用して呼び出されます。 この方法は、軽量なデータ変換に適したオプションです。 

* 長所:
    * データは、比較的短い待機時間でサーバーレス コンピューティングで処理されます
    * ADF パイプラインは、高度なデータ変換フローを実装できる [Durable Azure Functions](/azure/azure-functions/durable/durable-functions-overview) を呼び出すことができます 
    * データ変換の詳細は、Azure Functions によって抽象化され、他の場所から再利用して呼び出すことができます
* 短所:
    * Azure Functions を ADF で使用する前に作成しておく必要があります
    * Azure Functions は、実行時間の短いデータ処理にのみ適しています

## <a name="adf-with-custom-component-activity"></a>ADF とカスタム コンポーネント アクティビティ

![adf-customcomponent](media/how-to-data-ingest-adf/adf-customcomponent.png)

このオプションでは、実行可能ファイルにラップされたカスタム Python コードを使用してデータが処理されます。 これは、[ADF カスタム コンポーネント アクティビティ](https://docs.microsoft.com/azure/data-factory/transform-data-using-dotnet-custom-activity)を使用して呼び出されます。 この方法は、前の手法に比べて大規模なデータにより適しています。

* 長所:
    * データは [Azure Batch](https://docs.microsoft.com/azure/batch/batch-technical-overview) プールで処理されます。これにより、大規模な並列かつハイパフォーマンス コンピューティングが実現されます
    * 大量のアルゴリズムの実行や大量のデータの処理に使用できます
* 短所:
    * Azure Batch プールを ADF で使用する前に作成しておく必要があります
    * Python コードを実行可能ファイルにラップすることに関連した過度なエンジニアリング。 依存関係と入出力パラメーターの処理が複雑です

## <a name="adf-with-azure-databricks-python-notebook"></a>ADF と Azure Databricks Python ノートブック

![adf-databricks](media/how-to-data-ingest-adf/adf-databricks.png)

[Azure Databricks](https://azure.microsoft.com/services/databricks/) は、Microsoft クラウドの Apache Spark ベースの分析プラットフォームです。

この手法では、Azure Databricks クラスターで実行されている [Python ノートブック](https://docs.microsoft.com/azure/data-factory/transform-data-using-databricks-notebook)によってデータ変換が実行されます。 これはおそらく、Azure Databricks サービスの能力を最大限に活用する最も一般的な方法です。 これは、大規模な分散データ処理向けに設計されています。

* 長所:
    * データは、Apache Spark 環境によってバックアップされる、最も強力なデータ処理 Azure サービスで変換されます
    * Python に加え、データ サイエンス フレームワークとライブラリ (TensorFlow、PyTorch、scikit-learn など) のネイティブ サポート
    * Python コードを関数または実行可能モジュールにラップする必要はありません。 コードはそのままで動作します。
* 短所:
    * Azure Databricks インフラストラクチャを ADF で使用する前に作成しておく必要があります
    * Azure Databricks 構成によってはコストが高くなる可能性があります
    * コンピューティング クラスターを "コールド" モードから起動するにはしばらく時間がかかり、ソリューションで長い待機時間が発生します 
    

## <a name="consuming-data-in-azure-machine-learning-pipelines"></a>Azure Machine Learning パイプラインでのデータの使用

![aml-dataset](media/how-to-data-ingest-adf/aml-dataset.png)

ADF パイプラインから変換されたデータは、データ ストレージ (Azure Blob など) に保存されます。 Azure Machine Learning は、[データストア](https://docs.microsoft.com/azure/machine-learning/how-to-access-data#create-and-register-datastores)と[データセット](https://docs.microsoft.com/azure/machine-learning/how-to-create-register-datasets)を使用してこのデータにアクセスできます。

ADF パイプラインを実行するたびに、データはストレージ内の別の場所に保存されます。 この場所を Azure Machine Learning に渡すために、ADF パイプラインは Azure Machine Learning パイプラインを呼び出します。 ML パイプラインを呼び出すと、データの場所と実行 ID がパラメーターとして送信されます。 その後、ML パイプラインでは、そのデータの場所を使用してデータストアまたはデータセットを作成できます。 

> [!TIP]
> データセットは[バージョン管理をサポートしている](https://docs.microsoft.com/azure/machine-learning/how-to-version-track-datasets)ため、ML パイプラインは ADF パイプラインからの最新のデータを指すデータセットの新しいバージョンを登録できます。

データストアまたはデータセットを使用してデータにアクセスできるようになったら、それを使用して ML モデルをトレーニングできます。 トレーニング プロセスは、ADF から呼び出されるものと同じ ML パイプラインの一部である場合があります。 または、Jupyter ノートブックでの実験など、別のプロセスである場合もあります。

データセットはバージョン管理をサポートし、パイプラインから実行するたびに新しいバージョンが作成されるため、モデルのトレーニングに使用されたデータのバージョンを簡単に把握できます。

## <a name="next-steps"></a>次のステップ

* [Azure Data Factory で Databricks ノートブックを実行する](https://docs.microsoft.com/azure/data-factory/transform-data-using-databricks-notebook)
* [Azure ストレージ サービスのデータにアクセスする](https://docs.microsoft.com/azure/machine-learning/how-to-access-data#create-and-register-datastores)
* [Azure Machine Learning でデータセットを使用してモデルをトレーニングする](https://docs.microsoft.com/azure/machine-learning/how-to-train-with-datasets)
* [データ インジェスト パイプラインの DevOps](https://docs.microsoft.com/azure/machine-learning/how-to-cicd-data-ingestion)

