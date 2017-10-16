---
title: "Azure 上の PySpark と Scala を使用した HDInsight Spark のチュートリアル | Microsoft Docs"
description: "Azure HDInsight Spark で PySpark と Scala を使用して予測分析を行う Team Data Science Process の例を紹介します。"
services: machine-learning
documentationcenter: 
author: bradsev
manager: jhubbard
editor: cgronlun
ms.assetid: 
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/04/2017
ms.author: bradsev
ms.openlocfilehash: 99b9f047525f46f68dc2c1029f6d53fbda87b69e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="hdinsight-spark-data-science-walkthroughs-using-pyspark-and-scala-on-azure"></a>Azure 上の PySpark と Scala を使用した HDInsight Spark データ サイエンスのチュートリアル

ここでは、Azure Spark クラスターで PySpark と Scala を使って予測分析を行うチュートリアルを紹介します。 Team Data Science Process で概説された手順を踏襲しています。 Team Data Science Process の概要については、[データ サイエンス プロセス](overview.md)に関するページを参照してください。 HDInsight における Spark の概要については、「[HDInsight での Spark の概要](../../hdinsight/hdinsight-apache-spark-overview.md)」を参照してください。

Team Data Science Process を実行するデータ サイエンスのチュートリアルは他にも存在し、使用する**プラットフォーム**ごとにグループ化されています。 これらの例の箇条書きについては、[Team Data Science Process を実行するチュートリアル](walkthroughs.md)に関するページをご覧ください。

## <a name="predict-taxi-tips-using-pyspark-on-azure-spark"></a>Azure Spark 上の PySpark を使ってタクシーのチップを予測する

[Azure HDInsight 上の Spark を使用](spark-overview.md)したチュートリアルでは、NYC タクシーのデータを使って、チップが支払われるかどうかと、想定される金額の範囲を予測します。 [Azure HDInsight Spark クラスター](https://azure.microsoft.com/services/hdinsight/)を使用したシナリオで Team Data Science Process を使用し、公開されている NYC タクシー乗車および料金データセットのデータを格納、調査し、特徴エンジニアリングを行います。 この概要トピックでは、同チュートリアルの中で使われている HDInsight Spark クラスターと Jupyter PySpark ノートブックについて補足しています。 これらのノートブックで、データを調査する方法や、モデルを作成して使用する方法を紹介しています。 高度なデータの探索と Notebook のモデリングでは、クロス検証、ハイパー パラメーター スイープ、モデルの評価を使用する方法を示します。

### <a name="data-exploration-and-modeling-with-spark"></a>Spark を使用したデータ探索とモデリング 
[Spark MLlib ツールキットを使用したデータの二項分類と回帰モデルの作成](spark-data-exploration-modeling.md)に関するトピックで、データセットの詳細を確認し、機械学習モデルの作成、スコア付け、評価を行います。

### <a name="model-consumption"></a>モデルの使用
このトピックで作成した分類モデルと回帰モデルにスコアを付ける方法については、[Spark で構築した機械学習モデルのスコア付けと評価](spark-model-consumption.md)に関するページを参照してください。

### <a name="cross-validation-and-hyperparameter-sweeping"></a>クロス検証とハイパーパラメーター スイープ
クロス検証とハイパーパラメーター スイープを使用したモデルのトレーニング方法については、「[Spark を使用した高度なデータ探索とモデリング](spark-advanced-data-exploration-modeling.md)」を参照してください。


## <a name="predict-taxi-tips-using-scala-on-azure-spark"></a>Azure Spark 上の Scala を使ってタクシーのチップを予測する

[Azure Spark 上の Scala を使用](scala-walkthrough.md)したチュートリアルでは、NYC タクシーのデータを使って、チップが支払われるかどうかと、想定される金額の範囲を予測します。 Azure HDInsight Spark クラスターで Spark の機械学習ライブラリ (MLlib) と SparkML パッケージを使用して、教師あり機械学習タスクに Scala を使用する方法を説明します。 また、 [データ サイエンス プロセス](http://aka.ms/datascienceprocess)(データの取り込みと探索、視覚化、特徴エンジニアリング、モデリング、モデルの使用) を構成するタスクについても説明します。 構築されるモデルには、ロジスティック回帰と線形回帰、ランダム フォレスト、勾配ブースティング ツリーなどがあります。


## <a name="next-steps"></a>次のステップ

Team Data Science Process を構成する主な要素については、[Team Data Science Process の概要](overview.md)に関するページを参照してください。

データ サイエンス プロジェクトの構築に使用できる Team Data Science Process のライフサイクルについては、「[Team Data Science Process ライフサイクル](lifecycle.md)」を参照してください。 このライフサイクルは、プロジェクトを実行する際に、その開始から終了までにわたって進められる通常のステップを大まかにまとめたものです。 

