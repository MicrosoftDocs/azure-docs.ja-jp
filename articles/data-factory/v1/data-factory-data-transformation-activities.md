---
title: 'データの変換: データのプロセスと変換 '
description: Azure Data Factory で Hadoop、 Azure Machine Learning Studio (クラシック)、または Azure Data Lake Analytics を使用して、データを変換または処理する方法について説明します。
author: dcstwh
ms.author: weetok
ms.reviewer: jburchel
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/10/2018
ms.openlocfilehash: 422acbaab097182b800e3bad35b0121284db9cd8
ms.sourcegitcommit: f611b3f57027a21f7b229edf8a5b4f4c75f76331
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/22/2021
ms.locfileid: "104782930"
---
# <a name="transform-data-in-azure-data-factory-version-1"></a>Azure Data Factory バージョン 1 でデータを変換する
> [!div class="op_single_selector"]
> * [Hive](data-factory-hive-activity.md)  
> * [Pig](data-factory-pig-activity.md)  
> * [MapReduce](data-factory-map-reduce.md)  
> * [Hadoop ストリーミング](data-factory-hadoop-streaming-activity.md)
> * [Azure Machine Learning Studio (クラシック)](data-factory-azure-ml-batch-execution-activity.md) 
> * [ストアド プロシージャ](data-factory-stored-proc-activity.md)
> * [Data Lake Analytics U-SQL](data-factory-usql-activity.md)
> * [.NET カスタム](data-factory-use-custom-activities.md)

## <a name="overview"></a>概要
> [!NOTE]
> この記事は、Data Factory のバージョン 1 に適用されます。 最新バージョンの Data Factory サービスを使用している場合は、[Data Factory でのデータ変換アクティビティ](../transform-data.md)に関するページをご覧ください。

この記事では、Azure Data Factory でのデータ変換アクティビティについて説明します。このアクティビティにより、生データを変換および処理することで、予測や把握が容易になります。 変換アクティビティは、Azure HDInsight クラスターや Azure Batch などのコンピューティング環境で実行されます。 各変換アクティビティの詳細情報に関する記事へのリンクが提供されています。

Data Factory は、次のデータ変換アクティビティをサポートしています。これらのアクティビティは、個別または他のアクティビティと連結した状態で[パイプライン](data-factory-create-pipelines.md)に追加できます。

> [!NOTE]
> 具体的な手順を示すチュートリアルについては、 [Hive 変換を使用するパイプラインを作成する](data-factory-build-your-first-pipeline.md) 記事を参照してください。  
> 
> 

## <a name="hdinsight-hive-activity"></a>HDInsight Hive アクティビティ
Data Factory パイプラインの HDInsight Hive アクティビティでは、独自またはオンデマンドの Windows/Linux ベースの HDInsight クラスターで Hive クエリを実行します。 このアクティビティの詳細については、記事「 [Hive アクティビティ](data-factory-hive-activity.md) 」を参照してください。 

## <a name="hdinsight-pig-activity"></a>HDInsight Pig アクティビティ
Data Factory パイプラインの HDInsight Pig アクティビティでは、独自またはオンデマンドの Windows/Linux ベースのHDInsight クラスターで Pig クエリを実行します。 このアクティビティの詳細については、記事「 [Pig アクティビティ](data-factory-pig-activity.md) 」を参照してください。 

## <a name="hdinsight-mapreduce-activity"></a>HDInsight MapReduce アクティビティ
Data Factory パイプラインの HDInsight MapReduce アクティビティは、独自の、またはオンデマンドの Windows/Linux ベースの HDInsight クラスターで MapReduce プログラムを実行します。 このアクティビティの詳細については、記事「 [MapReduce アクティビティ](data-factory-map-reduce.md) 」を参照してください。

## <a name="hdinsight-streaming-activity"></a>HDInsight Streaming アクティビティ
Data Factory パイプラインの HDInsight Streaming アクティビティは、独自の、またはオンデマンドの Windows/Linux ベースの HDInsight クラスターで Hadoop Streaming プログラムを実行します。 このアクティビティの詳細については、記事「 [HDInsight Streaming アクティビティ](data-factory-hadoop-streaming-activity.md) 」を参照してください。

## <a name="hdinsight-spark-activity"></a>HDInsight Spark アクティビティ
Data Factory パイプラインの HDInsight Spark アクティビティでは、独自の HDInsight クラスターで Spark プログラムを実行します。 詳細については、「[Data Factory から Spark プログラムを起動する](data-factory-spark.md)」を参照してください。 

## <a name="azure-machine-learning-studio-classic-activities"></a>Azure Machine Learning スタジオ (クラシック) のアクティビティ
Azure Data Factory を使用すると、公開された Azure Machine Learning Studio (classic) Web サービスを利用して予測分析を行うパイプラインを簡単に作成できます。 Azure Data Factory パイプライン内で[バッチ実行アクティビティ](data-factory-azure-ml-batch-execution-activity.md#invoking-a-web-service-using-batch-execution-activity)を使用すると、スタジオ (クラシック) Web サービスを呼び出して、データの予測を一括で行うことができます。

時間の経過と共に、スタジオ (クラシック) スコア付け実験の予測モデルには、新しい入力データセットを使用した再トレーニングが必要になります。 再トレーニングが完了したら、再トレーニング済みの機械学習モデルでスコア付け Web サービスを更新する必要があります。 [更新リソース アクティビティ](data-factory-azure-ml-batch-execution-activity.md#updating-models-using-update-resource-activity) を使用して、新しくトレーニングを行ったモデルで Web サービスを更新します。  

これらのスタジオ (クラシック) アクティビティの詳細については、[Azure Machine Learning Studio (クラシック) アクティビティの使用](data-factory-azure-ml-batch-execution-activity.md)に関するページをご覧ください。 

## <a name="stored-procedure-activity"></a>ストアド プロシージャ アクティビティ
SQL Server ストアド プロシージャ アクティビティを Data Factory のパイプライン内で使用して、次のいずれかのデータ ストア内のストアド プロシージャを呼び出すことができます。企業または Azure VM 内の Azure SQL Database、Azure Synapse Analytics、SQL Server データベース。 詳細については、記事「 [ストアド プロシージャ アクティビティ](data-factory-stored-proc-activity.md) 」を参照してください。  

## <a name="data-lake-analytics-u-sql-activity"></a>Data Lake Analytics U-SQL アクティビティ
Data Lake Analytics U-SQL アクティビティは、Azure Data Lake Analytics クラスターで U-SQL スクリプトを実行します。 詳細については、 [Data Analytics U-SQL アクティビティ](data-factory-usql-activity.md) に関する記事を参照してください。 

## <a name="net-custom-activity"></a>.NET カスタム アクティビティ
Data Factory でサポートされていない方法でデータを変換する必要がある場合は、独自のデータ処理ロジックを使用するカスタム アクティビティを作成し、パイプラインでそのアクティビティを使用できます。 Azure Batch サービスまたは Azure HDInsight クラスターを使用して実行するようにカスタム .NET アクティビティを構成できます。 [Use custom activities](data-factory-use-custom-activities.md) (カスタム アクティビティの使用) を参照してください。 

カスタム アクティビティを作成して、R がインストールされている HDInsight クラスターで R スクリプトを実行することができます。 [Azure Data Factory を使用した R スクリプトの実行](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV1/RunRScriptUsingADFSample)に関するトピックを参照してください。 

## <a name="compute-environments"></a>コンピューティング環境
変換アクティビティを定義するときには、コンピューティング環境のリンクされたサービスを作成したうえで、そのサービスを使用します。 Data Factory でサポートされているコンピューティング環境は 2 種類あります。 

1. **オンデマンド**: この場合、コンピューティング環境は Data Factory で完全に管理されます。 データを処理するためのジョブが送信される前に Data Factory サービスにより自動的に作成され、ジョブの完了時に削除されます。 ユーザーは、ジョブの実行、クラスターの管理、ブートストラップ アクションなどについて、オンデマンドのコンピューティング環境の詳細設定を構成および制御できます。 
2. **独自の環境を使用する**: この場合、Data Factory のリンクされたサービスとして、独自のコンピューティング環境 (HDInsight クラスターなど) を登録できます。 このコンピューティング環境はユーザーが自分で管理することになります。Data Factory サービスは、アクティビティを実行にこの環境を使用します。 

Data Factory でサポートされているコンピューティング サービスの詳細については、記事「 [コンピューティングのリンクされたサービス](data-factory-compute-linked-services.md) 」を参照してください。 

## <a name="summary"></a>まとめ
Azure Data Factory では、次のデータ変換アクティビティと、アクティビティのためのコンピューティング環境をサポートしています。 変換アクティビティは、個別または他のアクティビティと連結した状態でパイプラインに追加できます。

| データ変換アクティビティ | Compute 環境 |
|:--- |:--- |
| [Hive](data-factory-hive-activity.md) |HDInsight [Hadoop] |
| [Pig](data-factory-pig-activity.md) |HDInsight [Hadoop] |
| [MapReduce](data-factory-map-reduce.md) |HDInsight [Hadoop] |
| [Hadoop ストリーミング](data-factory-hadoop-streaming-activity.md) |HDInsight [Hadoop] |
| [Azure Machine Learning スタジオ (クラシック) のアクティビティ: バッチ実行とリソースの更新](data-factory-azure-ml-batch-execution-activity.md) |Azure VM |
| [ストアド プロシージャ](data-factory-stored-proc-activity.md) |Azure SQL、Azure Synapse Analytics、または SQL Server |
| [Data Lake Analytics U-SQL](data-factory-usql-activity.md) |Azure Data Lake Analytics |
| [DotNet](data-factory-use-custom-activities.md) |HDInsight [Hadoop] または Azure Batch |

