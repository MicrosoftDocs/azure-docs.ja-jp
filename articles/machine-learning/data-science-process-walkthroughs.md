---
title: "Team Data Science Process のチュートリアル | Microsoft Docs"
description: "チュートリアルでは、クラウドとオンプレミスのツールとサービスをワークフローまたはパイプラインに組み込んで、インテリジェントなアプリケーションを作成する方法について説明します。"
services: machine-learning
documentationcenter: 
author: bradsev
manager: jhubbard
editor: cgronlun
ms.assetid: aa63d5a5-25ee-4c4b-9a4c-7553b98d7f6e
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/31/2017
ms.author: bradsev
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: fde069560d53c5b5010bbb59b52d60a6971d8dac
ms.lasthandoff: 11/17/2016


---
# <a name="team-data-science-process-walkthroughs"></a>Team Data Science Process のチュートリアル
ここで紹介する各エンド ツー エンド チュートリアルで取り上げるのは、**特定のシナリオ**に対する Team Data Science Process のステップです。 クラウド、オンプレミスのツール、およびサービスをワークフローまたはパイプラインに組み込んで、インテリジェントなアプリケーションを作成する方法について説明します。

## <a name="use-sql-data-warehouse"></a>SQL Data Warehouse を使用する
「 [Team Data Science Process の活用: SQL Data Warehouse の使用](machine-learning-data-science-process-sqldw-walkthrough.md) 」チュートリアルでは、SQL Data Warehouse (SQL DW) と公開されている NYC タクシー乗車および料金データセットを使用して、機械学習の分類および回帰モデルの構築とデプロイを行う方法を説明します。

## <a name="use-sql-server"></a>SQL Server を使用する
「 [Team Data Science Process in action: using SQL Server (Team Data Science Process の活用: SQL Sever の使用)](machine-learning-data-science-process-sql-walkthrough.md) 」チュートリアルでは、SQL Server と公開されている NYC タクシー乗車および料金データセットを使用して、機械学習の分類および回帰モデルの構築とデプロイを行う方法を説明します。

## <a name="use-hdinsight-hadoop-clusters"></a>HDInsight Hadoop クラスターを使用する
「[Team Data Science Process の活用: HDInsight Hadoop クラスターの使用](machine-learning-data-science-process-hive-walkthrough.md)」チュートリアルでは、[Azure HDInsight Hadoop クラスター](https://azure.microsoft.com/services/hdinsight/)を使用して、公開されている NYC タクシー乗車および料金データセットのデータを格納、調査し、特徴エンジニアリングを行います。

## <a name="use-azure-hdinsight-hadoop-clusters-on-a-1-tb-dataset"></a>1 TB データセットで Azure HDInsight Hadoop クラスターを使用する
「[Team Data Science Process の活用 - 1 TB データセットでの Azure HDInsight Hadoop クラスターの使用](machine-learning-data-science-process-hive-criteo-walkthrough.md)」チュートリアルでは、[Azure HDInsight Hadoop クラスター](https://azure.microsoft.com/services/hdinsight/)を使用して、公開されている [Criteo](http://labs.criteo.com/downloads/download-terabyte-click-logs/) データセットのいずれかのサンプル データを格納、調査し、特徴エンジニアリングとダウンサンプリングを行うエンド ツー エンドのシナリオを紹介します。

## <a name="data-science-using-python-with-spark-on-azure"></a>Azure 上の Spark での Python を使用したデータ サイエンス
[Azure HDInsight での Spark を使用したデータ サイエンス](machine-learning-data-science-spark-overview.md)に関するチュートリアルでは、[Azure HDInsight Spark クラスター](https://azure.microsoft.com/services/hdinsight/)を使用したエンド ツー エンドのシナリオで Team Data Science Process を使用して、公開されている NYC タクシー乗車および料金データセットのデータを格納、調査し、特徴エンジニアリングを行います。 

## <a name="data-science-using-scala-with-spark-on-azure"></a>Azure 上の Spark での Scala を使用したデータ サイエンス
[Azure 上の Spark での Scala を使用したデータ サイエンス](machine-learning-data-science-process-scala-walkthrough.md) に関するチュートリアルでは、Azure HDInsight Spark クラスターで Spark のスケーラブルな Machine Learning ライブラリ (MLlib) と SparkML パッケージを使用して、教師あり機械学習タスクに Scala を使用する方法を説明します。 また、 [データ サイエンス プロセス](http://aka.ms/datascienceprocess)(データの取り込みと探索、視覚化、特徴エンジニアリング、モデリング、モデルの使用) を構成するタスクについても説明します。 構築されるモデルには、ロジスティック回帰と線形回帰、ランダム フォレスト、勾配ブースティング ツリーなどがあります。

## <a name="use-azure-data-lake-storage-and-analytics"></a>Azure Data Lake Storage と Analytics を使用する
「[Azure Data Lake のスケーラブルなデータ サイエンス: エンドツーエンド チュートリアル](machine-learning-data-science-process-data-lake-walkthrough.md)」では、NYC タクシーのデータセットのサンプルで Azure Data Lake を使用して、データ調査タスクと 2 項分類タスクを実行し、乗客ごとにチップが支払われるかどうかを予測します。 

## <a name="use-r-with-sql-server-r-services"></a>SQL Server R Services で R を使用する
[SQL Server R Services を使用したデータ サイエンスのエンド ツー エンド チュートリアル](https://msdn.microsoft.com/library/mt612857.aspx) では、データ サイエンティスト向けに、R モデルを構築して SQL Server にデプロイするための、R コード、SQL Server データ、カスタム SQL 関数の組み合わせを提供します。

## <a name="use-t-sql-with-sql-server-r-services"></a>SQL Server R Services で T-SQL を使用する
[SQL 開発者を対象とした高度な In-Database 分析](https://msdn.microsoft.com/library/mt683480.aspx)チュートリアルは、SQL プログラマ向けに、SQL Server R Services で Transact-SQL を使用した高度な分析ソリューション構築の体験を提供して、R ソリューションを扱えるようにします。

## <a name="whats-next"></a>次の手順
Azure でのデータ サイエンス プロセスを構成するタスクについて説明したトピックの概要については、 [データ サイエンス プロセス](http://aka.ms/datascienceprocess)に関するページをご覧ください。 


