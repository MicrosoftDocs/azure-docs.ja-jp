---
title: Azure Data Factory を使用してデータを変換する | Microsoft Docs
description: Azure Data Factory で Hadoop、Machine Learning、または Azure Data Lake Analytics を使用してデータを変換または処理する方法について説明します。
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 07/31/2018
author: nabhishek
ms.author: abnarain
manager: craigg
ms.openlocfilehash: 2c674f77ef0f779c9764771e2e0ae7a4aea47548
ms.sourcegitcommit: 30a0007f8e584692fe03c0023fe0337f842a7070
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/07/2019
ms.locfileid: "57575224"
---
# <a name="transform-data-in-azure-data-factory"></a>Azure Data Factory でデータを変換する
> [!div class="op_single_selector"]
> * [Hive](transform-data-using-hadoop-hive.md)  
> * [Pig](transform-data-using-hadoop-pig.md)  
> * [MapReduce](transform-data-using-hadoop-map-reduce.md)  
> * [HDInsight Streaming](transform-data-using-hadoop-streaming.md)
> * [HDInsight Spark](transform-data-using-spark.md)
> * [Machine Learning](transform-data-using-machine-learning.md) 
> * [ストアド プロシージャ](transform-data-using-stored-procedure.md)
> * [Data Lake Analytics U-SQL](transform-data-using-data-lake-analytics.md)
> * [Databricks Notebook](transform-data-databricks-notebook.md)
> * [Databricks Jar](transform-data-databricks-jar.md)
> * [Databricks Python](transform-data-databricks-python.md)
> * [.NET カスタム](transform-data-using-dotnet-custom-activity.md)

## <a name="overview"></a>概要
この記事では、Azure Data Factory でのデータ変換アクティビティについて説明します。このアクティビティにより、生データを変換および処理することで、予測や把握が容易になります。 変換アクティビティは、Azure HDInsight クラスターや Azure Batch などのコンピューティング環境で実行されます。 各変換アクティビティの詳細情報に関する記事へのリンクが提供されています。

Data Factory は、次のデータ変換アクティビティをサポートしています。これらのアクティビティは、個別または他のアクティビティと連結した状態で[パイプライン](concepts-pipelines-activities.md)に追加できます。

## <a name="hdinsight-hive-activity"></a>HDInsight Hive アクティビティ
Data Factory パイプラインの HDInsight Hive アクティビティでは、独自またはオンデマンドの Windows/Linux ベースの HDInsight クラスターで Hive クエリを実行します。 このアクティビティの詳細については、[Hive アクティビティ](transform-data-using-hadoop-hive.md)に関する記事をご覧ください。 

## <a name="hdinsight-pig-activity"></a>HDInsight Pig アクティビティ
Data Factory パイプラインの HDInsight Pig アクティビティでは、独自またはオンデマンドの Windows/Linux ベースのHDInsight クラスターで Pig クエリを実行します。 このアクティビティの詳細については、[Pig アクティビティ](transform-data-using-hadoop-pig.md)に関する記事をご覧ください。 

## <a name="hdinsight-mapreduce-activity"></a>HDInsight MapReduce アクティビティ
Data Factory パイプラインの HDInsight MapReduce アクティビティは、独自の、またはオンデマンドの Windows/Linux ベースの HDInsight クラスターで MapReduce プログラムを実行します。 このアクティビティの詳細については、[MapReduce アクティビティ](transform-data-using-hadoop-map-reduce.md)に関する記事をご覧ください。

## <a name="hdinsight-streaming-activity"></a>HDInsight Streaming アクティビティ
Data Factory パイプラインの HDInsight Streaming アクティビティは、独自の、またはオンデマンドの Windows/Linux ベースの HDInsight クラスターで Hadoop Streaming プログラムを実行します。 このアクティビティの詳細については、記事「 [HDInsight Streaming アクティビティ](transform-data-using-hadoop-streaming.md) 」を参照してください。

## <a name="hdinsight-spark-activity"></a>HDInsight Spark アクティビティ
Data Factory パイプラインの HDInsight Spark アクティビティでは、独自の HDInsight クラスターで Spark プログラムを実行します。 詳細については、「[Data Factory から Spark プログラムを起動する](transform-data-using-spark.md)」を参照してください。 

## <a name="machine-learning-activities"></a>Machine Learning アクティビティ
Azure Data Factory を使用すると、公開された Azure Machine Learning Web サービスを利用して予測分析を行うパイプラインを簡単に作成できます。 Azure Data Factory パイプラインで[バッチ実行アクティビティ](transform-data-using-machine-learning.md)を使用すると、Machine Learning Web サービスを呼び出して、データの予測をバッチで行うことができます。

時間の経過と共に、Machine Learning スコア付け実験の予測モデルには、新しい入力データセットを使用した再トレーニングが必要になります。 再トレーニングが完了したら、再トレーニング済みの Machine Learning モデルでスコア付け Web サービスを更新する必要があります。 [更新リソース アクティビティ](update-machine-learning-models.md)を使用して、新しくトレーニングを行ったモデルで Web サービスを更新します。  

これらの Machine Learning アクティビティの詳細については、 [Azure Machine Learning アクティビティの使用](transform-data-using-machine-learning.md) に関する記事を参照してください。 

## <a name="stored-procedure-activity"></a>ストアド プロシージャ アクティビティ
SQL Server ストアド プロシージャ アクティビティを Data Factory のパイプライン内で使用して、次のいずれかのデータ ストア内のストアド プロシージャを呼び出すことができます。社内または Azure VM 内の Azure SQL Database、Azure SQL Data Warehouse、SQL Server データベース。 詳細については、[ストアド プロシージャ アクティビティ](transform-data-using-stored-procedure.md)に関する記事をご覧ください。  

## <a name="data-lake-analytics-u-sql-activity"></a>Data Lake Analytics U-SQL アクティビティ
Data Lake Analytics U-SQL アクティビティは、Azure Data Lake Analytics クラスターで U-SQL スクリプトを実行します。 詳細については、[Data Analytics U-SQL アクティビティ](transform-data-using-data-lake-analytics.md)に関する記事をご覧ください。 

## <a name="databricks-notebook-activity"></a>Databricks Notebook アクティビティ

Data Factory パイプラインの Azure Databricks Notebook アクティビティは、Azure Databricks ワークスペースで Databricks Notebook を実行します。Azure Databricks は、Apache Spark を実行するためのマネージド プラットフォームです。 「[Databricks Notebook を実行してデータを変換する](transform-data-databricks-notebook.md)」を参照してください。

## <a name="databricks-jar-activity"></a>Databricks Jar アクティビティ

Data Factory パイプラインの Azure Databricks Jar アクティビティは、Azure Databricks クラスターで Spark Jar を実行します。 Azure Databricks は、Apache Spark を実行するための管理されたプラットフォームです。 「[Azure Databricks で Jar アクティビティを実行してデータを変換する](transform-data-databricks-jar.md)」を参照してください。

## <a name="databricks-python-activity"></a>Databricks Python アクティビティ

Data Factory パイプラインの Azure Databricks Python アクティビティは、Azure Databricks クラスターで Python ファイルを実行します。 Azure Databricks は、Apache Spark を実行するための管理されたプラットフォームです。 「[Azure Databricks で Python アクティビティを実行してデータを変換する](transform-data-databricks-python.md)」を参照してください。

## <a name="custom-activity"></a>カスタム アクティビティ
Data Factory でサポートされていない方法でデータを変換する必要がある場合は、独自のデータ処理ロジックを使用するカスタム アクティビティを作成し、パイプラインでそのアクティビティを使用できます。 Azure Batch サービスまたは Azure HDInsight クラスターを使用して実行するようにカスタム .NET アクティビティを構成できます。 [Use custom activities](transform-data-using-dotnet-custom-activity.md) (カスタム アクティビティの使用) を参照してください。 

カスタム アクティビティを作成して、R がインストールされている HDInsight クラスターで R スクリプトを実行することができます。 [Azure Data Factory を使用した R スクリプトの実行](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/RunRScriptUsingADFSample)に関するトピックを参照してください。 

## <a name="compute-environments"></a>コンピューティング環境
変換アクティビティを定義するときには、コンピューティング環境のリンクされたサービスを作成したうえで、そのサービスを使用します。 Data Factory でサポートされているコンピューティング環境は 2 種類あります。 

- **オンデマンド**: この場合、コンピューティング環境は Data Factory によって完全に管理されます。 データを処理するためのジョブが送信される前に Data Factory サービスにより自動的に作成され、ジョブの完了時に削除されます。 ユーザーは、ジョブの実行、クラスターの管理、ブートストラップ アクションなどについて、オンデマンドのコンピューティング環境の詳細設定を構成および制御できます。 
- **独自の環境を使用する**: この場合、Data Factory のリンクされたサービスとして、独自のコンピューティング環境 (HDInsight クラスターなど) を登録できます。 このコンピューティング環境はユーザーが自分で管理することになります。Data Factory サービスは、アクティビティを実行にこの環境を使用します。 

Data Factory でサポートされているコンピューティング サービスの詳細については、記事「 [コンピューティングのリンクされたサービス](compute-linked-services.md) 」を参照してください。 

## <a name="next-steps"></a>次の手順
変換アクティビティの使用例については、次のチュートリアルをご覧ください。[チュートリアル: Spark を使用してデータを変換する](tutorial-transform-data-spark-powershell.md)
