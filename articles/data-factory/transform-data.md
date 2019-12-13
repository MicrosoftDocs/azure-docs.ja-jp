---
title: データを変換する
description: Azure Data Factory で Hadoop、Machine Learning、または Azure Data Lake Analytics を使用してデータを変換または処理する方法について説明します。
services: data-factory
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
author: nabhishek
ms.author: abnarain
manager: shwang
ms.custom: seo-lt-2019
ms.date: 07/31/2018
ms.openlocfilehash: 1f920f2672c19455a8e8ac979e8d6d1eb14d4c35
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/08/2019
ms.locfileid: "74924295"
---
# <a name="transform-data-in-azure-data-factory"></a>Azure Data Factory でデータを変換する

> [!div class="op_single_selector"]
> * [マッピング データ フロー](data-flow-create.md)
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
この記事では、Azure Data Factory でのデータ変換アクティビティについて説明します。このアクティビティを使用すると、生データを大規模な予測と分析情報に変換して処理することができます。 変換アクティビティは、Azure Databricks や Azure HDInsight などのコンピューティング環境で実行されます。 各変換アクティビティの詳細情報に関する記事へのリンクが提供されています。

Data Factory は、次のデータ変換アクティビティをサポートしています。これらのアクティビティは、個別または他のアクティビティと連結した状態で[パイプライン](concepts-pipelines-activities.md)に追加できます。

## <a name="transform-natively-in-azure-data-factory-with-data-flows"></a>データ フローを使用して Azure Data Factory でネイティブに変換する

### <a name="mapping-data-flows"></a>データ フローのマッピング

マッピング データ フローは、Azure Data Factory での視覚的に設計されたデータ変換です。 データ フローを使用すると、データ エンジニアは、コードを記述することなくグラフィカルなデータ変換ロジックを開発できます。 生成されたデータ フローは、スケールアウトされた Spark クラスターを使用する Azure Data Factory パイプライン内のアクティビティとして実行されます。 データ フロー アクティビティは、既存の Data Factory のスケジュール設定、制御、フロー、および監視機能を通して運用可能にすることができます。 詳細については、[マッピング データ フロー](concepts-data-flow-overview.md)に関するページを参照してください。

### <a name="wrangling-data-flows"></a>ラングリング データ フロー

Azure Data Factory のラングリング データ フローを使用すると、コードなしのデータ準備をクラウド規模で繰り返し実行できます。 ラングリング データ フローは [Power Query Online](https://docs.microsoft.com/power-query/) と統合されており、Spark 実行を通して Power Query M 関数をデータ ラングリングに対してクラウド規模で使用可能にします。 詳細については、[ラングリング データ フロー](wrangling-data-flow-overview.md)に関するページを参照してください。

## <a name="external-transformations"></a>外部での変換

必要に応じて、変換を手動でコーディングし、外部のコンピューティング環境を自分で管理することもできます。

### <a name="hdinsight-hive-activity"></a>HDInsight Hive アクティビティ
Data Factory パイプラインの HDInsight Hive アクティビティでは、独自またはオンデマンドの Windows/Linux ベースの HDInsight クラスターで Hive クエリを実行します。 このアクティビティの詳細については、[Hive アクティビティ](transform-data-using-hadoop-hive.md)に関する記事をご覧ください。 

### <a name="hdinsight-pig-activity"></a>HDInsight Pig アクティビティ
Data Factory パイプラインの HDInsight Pig アクティビティでは、独自またはオンデマンドの Windows/Linux ベースのHDInsight クラスターで Pig クエリを実行します。 このアクティビティの詳細については、[Pig アクティビティ](transform-data-using-hadoop-pig.md)に関する記事をご覧ください。 

### <a name="hdinsight-mapreduce-activity"></a>HDInsight MapReduce アクティビティ
Data Factory パイプラインの HDInsight MapReduce アクティビティは、独自の、またはオンデマンドの Windows/Linux ベースの HDInsight クラスターで MapReduce プログラムを実行します。 このアクティビティの詳細については、[MapReduce アクティビティ](transform-data-using-hadoop-map-reduce.md)に関する記事をご覧ください。

### <a name="hdinsight-streaming-activity"></a>HDInsight Streaming アクティビティ
Data Factory パイプラインの HDInsight Streaming アクティビティは、独自の、またはオンデマンドの Windows/Linux ベースの HDInsight クラスターで Hadoop Streaming プログラムを実行します。 このアクティビティの詳細については、記事「 [HDInsight Streaming アクティビティ](transform-data-using-hadoop-streaming.md) 」を参照してください。

### <a name="hdinsight-spark-activity"></a>HDInsight Spark アクティビティ
Data Factory パイプラインの HDInsight Spark アクティビティでは、独自の HDInsight クラスターで Spark プログラムを実行します。 詳細については、「[Data Factory から Spark プログラムを起動する](transform-data-using-spark.md)」を参照してください。 

### <a name="machine-learning-activities"></a>Machine Learning アクティビティ
Azure Data Factory を使用すると、公開された Azure Machine Learning Web サービスを利用して予測分析を行うパイプラインを簡単に作成できます。 Azure Data Factory パイプラインで[バッチ実行アクティビティ](transform-data-using-machine-learning.md)を使用すると、Machine Learning Web サービスを呼び出して、データの予測をバッチで行うことができます。

時間の経過と共に、Machine Learning スコア付け実験の予測モデルには、新しい入力データセットを使用した再トレーニングが必要になります。 再トレーニングが完了したら、再トレーニング済みの Machine Learning モデルでスコア付け Web サービスを更新する必要があります。 [更新リソース アクティビティ](update-machine-learning-models.md)を使用して、新しくトレーニングを行ったモデルで Web サービスを更新します。  

これらの Machine Learning アクティビティの詳細については、 [Azure Machine Learning アクティビティの使用](transform-data-using-machine-learning.md) に関する記事を参照してください。 

### <a name="stored-procedure-activity"></a>ストアド プロシージャ アクティビティ
SQL Server ストアド プロシージャ アクティビティを Data Factory のパイプライン内で使用して、次のいずれかのデータ ストア内のストアド プロシージャを呼び出すことができます。企業または Azure VM 内の Azure SQL Database、Azure SQL Data Warehouse、SQL Server データベース。 詳細については、[ストアド プロシージャ アクティビティ](transform-data-using-stored-procedure.md)に関する記事をご覧ください。  

### <a name="data-lake-analytics-u-sql-activity"></a>Data Lake Analytics U-SQL アクティビティ
Data Lake Analytics U-SQL アクティビティは、Azure Data Lake Analytics クラスターで U-SQL スクリプトを実行します。 詳細については、[Data Analytics U-SQL アクティビティ](transform-data-using-data-lake-analytics.md)に関する記事をご覧ください。 

### <a name="databricks-notebook-activity"></a>Databricks Notebook アクティビティ

Data Factory パイプラインの Azure Databricks Notebook アクティビティは、Azure Databricks ワークスペースで Databricks ノートブックを実行します。 Azure Databricks は、Apache Spark を実行するための管理されたプラットフォームです。 「[Databricks Notebook を実行してデータを変換する](transform-data-databricks-notebook.md)」を参照してください。

### <a name="databricks-jar-activity"></a>Databricks Jar アクティビティ

Data Factory パイプラインの Azure Databricks Jar アクティビティは、Azure Databricks クラスターで Spark Jar を実行します。 Azure Databricks は、Apache Spark を実行するための管理されたプラットフォームです。 「[Azure Databricks で Jar アクティビティを実行してデータを変換する](transform-data-databricks-jar.md)」を参照してください。

### <a name="databricks-python-activity"></a>Databricks Python アクティビティ

Data Factory パイプラインの Azure Databricks Python アクティビティは、Azure Databricks クラスターで Python ファイルを実行します。 Azure Databricks は、Apache Spark を実行するための管理されたプラットフォームです。 「[Azure Databricks で Python アクティビティを実行してデータを変換する](transform-data-databricks-python.md)」を参照してください。

### <a name="custom-activity"></a>カスタム アクティビティ
Data Factory でサポートされていない方法でデータを変換する必要がある場合は、独自のデータ処理ロジックを使用するカスタム アクティビティを作成し、パイプラインでそのアクティビティを使用できます。 Azure Batch サービスまたは Azure HDInsight クラスターを使用して実行するようにカスタム .NET アクティビティを構成できます。 [Use custom activities](transform-data-using-dotnet-custom-activity.md) (カスタム アクティビティの使用) を参照してください。 

カスタム アクティビティを作成して、R がインストールされている HDInsight クラスターで R スクリプトを実行することができます。 [Azure Data Factory を使用した R スクリプトの実行](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV1/RunRScriptUsingADFSample)に関するトピックを参照してください。 

### <a name="compute-environments"></a>コンピューティング環境
変換アクティビティを定義するときには、コンピューティング環境のリンクされたサービスを作成したうえで、そのサービスを使用します。 Data Factory でサポートされているコンピューティング環境は 2 種類あります。 

- **オンデマンド**: この場合、コンピューティング環境は Data Factory によって完全に管理されます。 データを処理するためのジョブが送信される前に Data Factory サービスにより自動的に作成され、ジョブの完了時に削除されます。 ユーザーは、ジョブの実行、クラスターの管理、ブートストラップ アクションなどについて、オンデマンドのコンピューティング環境の詳細設定を構成および制御できます。 
- **独自の環境を使用する**: この場合、Data Factory のリンクされたサービスとして、独自のコンピューティング環境 (HDInsight クラスターなど) を登録できます。 このコンピューティング環境はユーザーが自分で管理することになります。Data Factory サービスは、アクティビティを実行にこの環境を使用します。 

Data Factory でサポートされているコンピューティング サービスの詳細については、記事「 [コンピューティングのリンクされたサービス](compute-linked-services.md) 」を参照してください。 

## <a name="next-steps"></a>次の手順
変換アクティビティの使用例については、次のチュートリアルをご覧ください。[チュートリアル: Spark を使用してデータを変換する](tutorial-transform-data-spark-powershell.md)
