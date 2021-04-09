---
title: Azure Data Factory から MapReduce プログラムを起動する
description: Azure Data Factory から Azure HDInsight クラスターで MapReduce プログラムを実行してデータを処理する方法について説明します。
author: dcstwh
ms.author: weetok
ms.reviewer: jburchel
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/10/2018
ms.openlocfilehash: f1f54e972d59d3de3b0f93b3150ee1150eb6f612
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "104786398"
---
# <a name="invoke-mapreduce-programs-from-data-factory"></a>Data Factory から MapReduce プログラムを起動する
> [!div class="op_single_selector" title1="変換アクティビティ"]
> * [Hive アクティビティ](data-factory-hive-activity.md) 
> * [Pig アクティビティ](data-factory-pig-activity.md)
> * [MapReduce アクティビティ](data-factory-map-reduce.md)
> * [Hadoop ストリーミング アクティビティ](data-factory-hadoop-streaming-activity.md)
> * [Spark アクティビティ](data-factory-spark.md)
> * [Azure Machine Learning スタジオ (クラシック) のバッチ実行アクティビティ](data-factory-azure-ml-batch-execution-activity.md)
> * [Azure Machine Learning スタジオ (クラシック) の更新リソース アクティビティ](data-factory-azure-ml-update-resource-activity.md)
> * [ストアド プロシージャ アクティビティ](data-factory-stored-proc-activity.md)
> * [Data Lake Analytics U-SQL アクティビティ](data-factory-usql-activity.md)
> * [.NET カスタム アクティビティ](data-factory-use-custom-activities.md)

> [!NOTE]
> この記事は、Data Factory のバージョン 1 に適用されます。 最新バージョンの Data Factory サービスを使用している場合は、[Data Factory での MapReduce アクティビティを使用したデータ変換](../transform-data-using-hadoop-map-reduce.md)に関するページを参照してください。


Data Factory [パイプライン](data-factory-create-pipelines.md)の HDInsight MapReduce アクティビティは、[独自の](data-factory-compute-linked-services.md#azure-hdinsight-linked-service)、または[オンデマンドの](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service) Windows/Linux ベースの HDInsight クラスターで MapReduce プログラムを実行します。 この記事は、データ変換とサポートされる変換アクティビティの概要を説明する、 [データ変換アクティビティ](data-factory-data-transformation-activities.md) に関する記事に基づいています。

> [!NOTE] 
> Azure Data Factory の使用経験がない場合は、この記事を読む前に、「[Azure Data Factory の概要](data-factory-introduction.md)」を参照し、[最初のデータ パイプラインの作成](data-factory-build-your-first-pipeline.md)チュートリアルを実行してください。  

## <a name="introduction"></a>はじめに
Azure Data Factory のパイプラインは、リンクされたコンピューティング サービスを使用して、リンクされたストレージ サービス内のデータを処理します。 パイプラインは、一連のアクティビティで構成されます。各アクティビティは、特定の処理操作を実行します。 この記事では、HDInsight MapReduce アクティビティを使用する方法について説明しています。

HDInsight Pig と Hive アクティビティを使用してパイプラインから Windows/Linux ベースの HDInsight クラスターで Pig/Hive スクリプトを実行する方法の詳細については、[Pig](data-factory-pig-activity.md) と [Hive](data-factory-hive-activity.md) に関する記事を参照してください。 

## <a name="json-for-hdinsight-mapreduce-activity"></a>HDInsight MapReduce アクティビティの JSON
HDInsight アクティビティの JSON の定義で、以下を設定します。 

1. **activity** の **type** を **HDInsight** に設定します。
2. クラスの名前を **className** プロパティに対して指定します。
3. ファイル名を含む JAR ファイルへのパスを **jarFilePath** プロパティに対して指定します。
4. JAR ファイルを含む Azure Blob Storage を参照するリンクされたサービスを **jarLinkedService** プロパティに対して指定します。   
5. **arguments** セクションでは MapReduce プログラムの引数を指定します。 実行時に、MapReduce フレームワークのいくつかの引数 (mapreduce.job.tags など) が表示されます。 MapReduce の引数と区別するために、次の例のように、オプションと値の両方を引数として使用することを検討してください (-s、--input、--output などがオプションであり、直後に値が続きます)。

    ```JSON   
    {
        "name": "MahoutMapReduceSamplePipeline",
        "properties": {
            "description": "Sample Pipeline to Run a Mahout Custom Map Reduce Jar. This job calcuates an Item Similarity Matrix to determine the similarity between 2 items",
            "activities": [
                {
                    "type": "HDInsightMapReduce",
                    "typeProperties": {
                        "className": "org.apache.mahout.cf.taste.hadoop.similarity.item.ItemSimilarityJob",
                        "jarFilePath": "adfsamples/Mahout/jars/mahout-examples-0.9.0.2.2.7.1-34.jar",
                        "jarLinkedService": "StorageLinkedService",
                        "arguments": [
                            "-s",
                            "SIMILARITY_LOGLIKELIHOOD",
                            "--input",
                            "wasb://adfsamples@spestore.blob.core.windows.net/Mahout/input",
                            "--output",
                            "wasb://adfsamples@spestore.blob.core.windows.net/Mahout/output/",
                            "--maxSimilaritiesPerItem",
                            "500",
                            "--tempDir",
                            "wasb://adfsamples@spestore.blob.core.windows.net/Mahout/temp/mahout"
                        ]
                    },
                    "inputs": [
                        {
                            "name": "MahoutInput"
                        }
                    ],
                    "outputs": [
                        {
                            "name": "MahoutOutput"
                        }
                    ],
                    "policy": {
                        "timeout": "01:00:00",
                        "concurrency": 1,
                        "retry": 3
                    },
                    "scheduler": {
                        "frequency": "Hour",
                        "interval": 1
                    },
                    "name": "MahoutActivity",
                    "description": "Custom Map Reduce to generate Mahout result",
                    "linkedServiceName": "HDInsightLinkedService"
                }
            ],
            "start": "2017-01-03T00:00:00Z",
            "end": "2017-01-04T00:00:00Z"
        }
    }
    ```
   HDInsight MapReduce アクティビティを使用して、HDInsight クラスター上で MapReduce jar ファイルを実行できます。 次のサンプルのパイプラインの JSON 定義では、Mahout JAR ファイルを実行するために HDInsight アクティビティが構成されます。

## <a name="sample-on-github"></a>GitHub 上のサンプル
[GitHub の Data Factory のサンプル](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV1/JSON/MapReduce_Activity_Sample)から、HDInsight MapReduce アクティビティを使用するためのサンプルをダウンロードできます。  

## <a name="running-the-word-count-program"></a>Word Count プログラムの実行
このチュートリアルのパイプラインでは、Azure HDInsight クラスターで Word Count Map/Reduce プログラムを実行します。   

### <a name="linked-services"></a>リンクされたサービス
最初に、Azure HDInsight クラスターによって使用される Azure Storage を Azure データ ファクトリにリンクする、リンクされたサービスを作成します。 次のコードをコピー/貼り付けする場合は、**アカウント名** と **アカウント キー** を、使用する Azure Storage のアカウント名とアカウント キーに必ず置き換えてください。 

#### <a name="azure-storage-linked-service"></a>Azure Storage のリンクされたサービス

```JSON
{
    "name": "StorageLinkedService",
    "properties": {
        "type": "AzureStorage",
        "typeProperties": {
            "connectionString": "DefaultEndpointsProtocol=https;AccountName=<account name>;AccountKey=<account key>"
        }
    }
}
```

#### <a name="azure-hdinsight-linked-service"></a>Azure HDInsight のリンクされたサービス
次に、Azure HDInsight クラスターを Azure データ ファクトリにリンクする、リンクされたサービスを作成します。 次のコードをコピー/貼り付けする場合は、 **HDInsight クラスター名** を使用する HDInsight クラスターの名前に置き換え、ユーザー名とパスワードを変更してください。   

```JSON
{
    "name": "HDInsightLinkedService",
    "properties": {
        "type": "HDInsight",
        "typeProperties": {
            "clusterUri": "https://<HDInsight cluster name>.azurehdinsight.net",
            "userName": "admin",
            "password": "**********",
            "linkedServiceName": "StorageLinkedService"
        }
    }
}
```

### <a name="datasets"></a>データセット
#### <a name="output-dataset"></a>出力データセット
この例のパイプラインには入力はありません。 HDInsight MapReduce アクティビティ用の出力データセットを指定します。 このデータセットは、パイプラインのスケジュールを実行するために必要となる単なるダミーのデータセットです。  

```JSON
{
    "name": "MROutput",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "StorageLinkedService",
        "typeProperties": {
            "fileName": "WordCountOutput1.txt",
            "folderPath": "example/data/",
            "format": {
                "type": "TextFormat",
                "columnDelimiter": ","
            }
        },
        "availability": {
            "frequency": "Day",
            "interval": 1
        }
    }
}
```

### <a name="pipeline"></a>パイプライン
この例のパイプラインには、HDInsightMapReduce という種類のアクティビティが 1 つだけあります。 JSON の重要なプロパティの一部を次に示します。 

| プロパティ | Notes |
|:--- |:--- |
| type |type には、 **HDInsightMapReduce** を設定する必要があります。 |
| className |クラスの名前は **wordcount** |
| jarFilePath |クラスを含む jar ファイルのパス。 次のコードをコピー/貼り付けする場合は、クラスターの名前を必ず変更してください。 |
| jarLinkedService |jar ファイルが含まれるAzure Storage のリンクされたサービス。 このリンクされたサービスは、HDInsight クラスターに関連付けられるストレージです。 |
| 引数 |wordcount プログラムは、入力と出力という2 つの引数を使用します。 入力ファイルは davinci.txt ファイルです。 |
| frequency/interval |これらのプロパティの値は、出力データセットと一致します。 |
| linkedServiceName |前に作成した HDInsight のリンクされたサービスを参照します。 |

```JSON
{
    "name": "MRSamplePipeline",
    "properties": {
        "description": "Sample Pipeline to Run the Word Count Program",
        "activities": [
            {
                "type": "HDInsightMapReduce",
                "typeProperties": {
                    "className": "wordcount",
                    "jarFilePath": "<HDInsight cluster name>/example/jars/hadoop-examples.jar",
                    "jarLinkedService": "StorageLinkedService",
                    "arguments": [
                        "/example/data/gutenberg/davinci.txt",
                        "/example/data/WordCountOutput1"
                    ]
                },
                "outputs": [
                    {
                        "name": "MROutput"
                    }
                ],
                "policy": {
                    "timeout": "01:00:00",
                    "concurrency": 1,
                    "retry": 3
                },
                "scheduler": {
                    "frequency": "Day",
                    "interval": 1
                },
                "name": "MRActivity",
                "linkedServiceName": "HDInsightLinkedService"
            }
        ],
        "start": "2014-01-03T00:00:00Z",
        "end": "2014-01-04T00:00:00Z"
    }
}
```

## <a name="run-spark-programs"></a>Spark プログラムの実行
MapReduce アクティビティを使用して、HDInsight Spark クラスターで Spark プログラムを実行することができます。 詳細については、「 [Invoke Spark programs from Azure Data Factory (Azure Data Factory から Spark プログラムを呼び出す)](data-factory-spark.md) 」を参照してください。  

[developer-reference]: /previous-versions/azure/dn834987(v=azure.100)
[cmdlet-reference]: https://go.microsoft.com/fwlink/?LinkId=517456


[adfgetstarted]: data-factory-copy-data-from-azure-blob-storage-to-sql-database.md
[adfgetstartedmonitoring]:data-factory-copy-data-from-azure-blob-storage-to-sql-database.md#monitor-pipelines 

[Developer Reference]: /previous-versions/azure/dn834987(v=azure.100)
[Azure Portal]: https://portal.azure.com

## <a name="see-also"></a>参照
* [Hive アクティビティ](data-factory-hive-activity.md)
* [Pig アクティビティ](data-factory-pig-activity.md)
* [Hadoop ストリーミング アクティビティ](data-factory-hadoop-streaming-activity.md)
* [Spark プログラムを呼び出す](data-factory-spark.md)
* [R スクリプトを呼び出す](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV1/RunRScriptUsingADFSample)