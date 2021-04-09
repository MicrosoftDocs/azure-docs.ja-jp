---
title: Azure Synapse Analytics の機械学習
description: Azure Synapse Analytics の機械学習機能の概要
services: synapse-analytics
ms.service: synapse-analytics
ms.subservice: machine-learning
ms.topic: overview
ms.reviewer: jrasnick, garye
ms.date: 09/25/2020
author: nelgson
ms.author: negust
ms.openlocfilehash: 68b113de63cfefde805c1c46e9303829c4eb33a7
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "98222141"
---
# <a name="machine-learning-capabilities-in-azure-synapse-analytics"></a>Azure Synapse Analytics の機械学習機能

Azure Synapse Analytics では、さまざまな機械学習の機能を提供しています。 この記事では、Azure Synapse のコンテキストで Machine Learning を適用する方法について概要を示します。

この概要では、データ サイエンス プロセスの観点から、機械学習に関連する Synapse のさまざまな機能について説明します。

一般的なデータ サイエンス プロセスがどのようなものか理解しておられるかもしれません。 これはよく知られたプロセスであり、ほとんどの機械学習プロジェクトはこのプロセスに従います。

大まかに言えば、このプロセスには次の手順が含まれます。
* (ビジネスの把握)
* データの取得と理解
* モデリング
* モデルのデプロイとスコアリング

この記事では、データ サイエンス プロセスの観点から、さまざまな分析エンジンにおける Azure Synapse の機械学習機能について説明します。 データ サイエンス プロセスのステップごとに、役立つ Azure Synapse の機能がまとめられています。

## <a name="azure-synapse-machine-learning-capabilities"></a>Azure Synapse の機械学習機能

### <a name="data-acquisition-and-understanding"></a>データの取得と理解

ほとんどの機械学習プロジェクトには、確立された手順が含まれています。そのような手順の 1 つは、データにアクセスして理解することです。

#### <a name="data-source-and-pipelines"></a>データ ソースとパイプライン

Azure Synapse のネイティブに統合されているパーツである [Azure Data Factory](../../data-factory/introduction.md) により、データ インジェストとデータ オーケストレーションのパイプラインに使用できる強力なツール セットが存在します。 これにより、データ パイプラインを簡単に作成して、データにアクセスしたり、機械学習に使用できる形式にデータを変換したりすることができます。 Synapse のデータ パイプラインについて詳しくは、[こちら](../../data-factory/concepts-pipelines-activities.md?bc=%2fazure%2fsynapse-analytics%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fsynapse-analytics%2ftoc.json)をご覧ください。 

#### <a name="data-preparation-and-explorationvisualization"></a>データの準備と探索/視覚化

機械学習プロセスにおいて、探索と視覚化によってデータについて理解することは重要な部分となります。

データの格納場所に応じて、Synapse には、分析と機械学習のためにデータを探索し、準備するためのさまざまなツールが用意されています。 データ探索を開始する最も簡単な方法の 1 つは、データ レイク内のデータに対して、Apache Spark またはサーバーレス SQL プールを直接使用することです。

* [Apache Spark for Azure Synapse](../spark/apache-spark-overview.md) には、大規模なデータの変換、準備、および探索を行う機能が用意されています。 これらの Spark プールでは、大規模なデータ処理のための PySpark/Python、Scala、.NET などのツールが提供されています。 強力な視覚化ライブラリを使用することで、データ探索のエクスペリエンスを向上させ、データの理解を深めることができます。 Spark を使用して Synapse のデータを探索し、視覚化する方法については、[こちらの記事](../get-started-analyze-spark.md)を参照してください。

* [サーバーレス SQL プール](../sql/on-demand-workspace-overview.md)では、データ レイクに対して TSQL を直接使用してデータを探索する方法が提供されます。 サーバーレス SQL プールには、Synapse Studio で使用する組み込みの視覚化機能もいくつか用意されています。 [サーバーレス SQL プールを使用してデータを探索する方法について学習してください](../get-started-analyze-sql-on-demand.md)。

### <a name="modeling"></a>モデリング

Azure Synapse では、PySpark/Python、Scala、.NET などのツールを使用して、Apache Spark プールで機械学習モデルのトレーニングを実行できます。

#### <a name="train-models-on-spark-pools-with-mllib"></a>MLlib を使用した Spark プールでのモデルのトレーニング

機械学習モデルは、さまざまなアルゴリズムやライブラリを利用してトレーニングできます。 [Spark MLlib](http://spark.apache.org/docs/latest/ml-guide.html) では、ほとんどの従来の機械学習の問題を解決するのに役立つ、スケーラブルな機械学習アルゴリズムが提供されます。 [このチュートリアル](../spark/apache-spark-machine-learning-mllib-notebook.md)では、Synapse で MLlib を使用してモデルをトレーニングする方法について説明します。

MLlib に加えて、[Scikit Learn](https://scikit-learn.org/stable/) などの一般的なライブラリを使用してモデルを開発することもできます。 Synapse Spark プールにライブラリをインストールする方法の詳細については、「[Azure Synapse Analytics で Apache Spark 用のライブラリを管理する](../spark/apache-spark-azure-portal-add-libraries.md)」を参照してください。

#### <a name="train-models-with-azure-machine-learning-automated-ml"></a>Azure Machine Learning の自動 ML を使用してモデルをトレーニングする

機械学習の知識をあまり必要としない、機械学習モデルのもう 1 つのトレーニング方法は、自動 ML を使用することです。 [自動 ML](../../machine-learning/concept-automated-ml.md) は、一連の機械学習モデルを自動的にトレーニングし、ユーザーが特定のメトリックに基づいて最適なモデルを選択できるようにする機能です。 Azure Synapse Notebooks から Azure Machine Learning とシームレスに統合できるため、ユーザーは Azure Active Directory パススルー認証を使用して Synapse で自動 ML を簡単に利用できます。  つまり、Azure Machine Learning ワークスペースをポイントするだけでよく、資格情報を入力する必要はありません。 Synapse Spark プールで Azure Machine Learning の自動 ML を使用してモデルをトレーニングする方法については、[自動 ML のチュートリアル](../spark/apache-spark-azure-machine-learning-tutorial.md)をご覧ください。

### <a name="model-deployment-and-scoring"></a>モデルのデプロイとスコアリング

Azure Synapse または外部の Azure Synapse でトレーニングされたモデルは、バッチ スコアリングに簡単に使用できます。 現在、Synapse には、バッチ スコアリングを実行する方法が 2 つあります。

* Synapse SQL プールで [TSQL PREDICT 関数](../sql-data-warehouse/sql-data-warehouse-predict.md)を使用して、データが存在する場所で直接予測を実行できます。 この強力でスケーラブルな関数を使用すると、データ ウェアハウスからデータを移動せずにデータを強化することができます。 [Synapse Studio での新しいガイド付き機械学習モデルのエクスペリエンス](./tutorial-sql-pool-model-scoring-wizard.md)が導入され、PREDICT を使用してバッチ スコアリングを行うために、Synapse SQL プールの Azure Machine Learning モデル レジストリから ONNX モデルをデプロイできるようになりました。

* Azure Synapse で機械学習モデルのバッチ スコアリングを行うためのもう 1 つのオプションは、Azure Synapse の Apache Spark プールを活用することです。 モデルのトレーニングに使用するライブラリによっては、コード エクスペリエンスを使用してバッチ スコアリングを実行できます。

## <a name="next-steps"></a>次の手順

* [Azure Synapse Analytics の使用を開始する](../get-started.md)
* [ワークスペースを作成する](../get-started-create-workspace.md)
* [クイック スタート: Synapse で Azure Machine Learning のリンクされたサービスを新規作成する](quickstart-integrate-azure-machine-learning.md)
* [チュートリアル: 機械学習モデル スコアリング ウィザード - 専用 SQL プール](tutorial-sql-pool-model-scoring-wizard.md)