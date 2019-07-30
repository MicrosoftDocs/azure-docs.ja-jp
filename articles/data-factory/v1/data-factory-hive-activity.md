---
title: Hive アクティビティを使用したデータ変換 - Azure | Microsoft Docs
description: Azure データ ファクトリで Hive アクティビティを使用して、オンデマンドまたは独自の HDInsight クラスターで Hive クエリを実行する方法について説明します。
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
ms.assetid: 80083218-743e-4da8-bdd2-60d1c77b1227
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: shlo
robots: noindex
ms.openlocfilehash: a63ef969f17fc48145174d99fec53e77b61885a4
ms.sourcegitcommit: 441e59b8657a1eb1538c848b9b78c2e9e1b6cfd5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/11/2019
ms.locfileid: "67827973"
---
# <a name="transform-data-using-hive-activity-in-azure-data-factory"></a>Azure Data Factory での Hive アクティビティを使用したデータ変換 
> [!div class="op_single_selector" title1="変換アクティビティ"]
> * [Hive アクティビティ](data-factory-hive-activity.md) 
> * [Pig アクティビティ](data-factory-pig-activity.md)
> * [MapReduce アクティビティ](data-factory-map-reduce.md)
> * [Hadoop ストリーミング アクティビティ](data-factory-hadoop-streaming-activity.md)
> * [Spark アクティビティ](data-factory-spark.md)
> * [Machine Learning バッチ実行アクティビティ](data-factory-azure-ml-batch-execution-activity.md)
> * [Machine Learning 更新リソース アクティビティ](data-factory-azure-ml-update-resource-activity.md)
> * [ストアド プロシージャ アクティビティ](data-factory-stored-proc-activity.md)
> * [Data Lake Analytics U-SQL アクティビティ](data-factory-usql-activity.md)
> * [.NET カスタム アクティビティ](data-factory-use-custom-activities.md)

> [!NOTE]
> この記事は、Data Factory のバージョン 1 に適用されます。 最新バージョンの Data Factory サービスを使用している場合は、[Data Factory での Hive アクティビティを使用したデータ変換](../transform-data-using-hadoop-hive.md)に関するページを参照してください。

Data Factory [パイプライン](data-factory-create-pipelines.md) の HDInsight Hive アクティビティでは、[独自](data-factory-compute-linked-services.md#azure-hdinsight-linked-service) または [オンデマンド](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service) の Windows/Linux ベースの HDInsight クラスターで Hive クエリを実行します。 この記事は、データ変換とサポートされる変換アクティビティの概要を説明する、 [データ変換アクティビティ](data-factory-data-transformation-activities.md) に関する記事に基づいています。

> [!NOTE] 
> Azure Data Factory を初めて利用する場合は、この記事を読む前に、「[Azure Data Factory の概要](data-factory-introduction.md)」を参照してから、[初めてのデータ パイプラインの作成](data-factory-build-your-first-pipeline.md)に関するチュートリアルを完了してください。 

## <a name="syntax"></a>構文

```JSON
{
    "name": "Hive Activity",
    "description": "description",
    "type": "HDInsightHive",
    "inputs": [
      {
        "name": "input tables"
      }
    ],
    "outputs": [
      {
        "name": "output tables"
      }
    ],
    "linkedServiceName": "MyHDInsightLinkedService",
    "typeProperties": {
      "script": "Hive script",
      "scriptPath": "<pathtotheHivescriptfileinAzureblobstorage>",
      "defines": {
        "param1": "param1Value"
      }
    },
   "scheduler": {
      "frequency": "Day",
      "interval": 1
    }
}
```
## <a name="syntax-details"></a>構文の詳細
| プロパティ | 説明 | 必須 |
| --- | --- | --- |
| 名前 |アクティビティの名前 |はい |
| description |アクティビティの用途を説明するテキストです。 |いいえ |
| type |HDinsightHive |はい |
| inputs |Hive アクティビティによって使用される入力 |いいえ |
| outputs |Hive アクティビティによって生成される出力 |はい |
| linkedServiceName |Data Factory のリンクされたサービスとして登録されている HDInsight クラスターへの参照 |はい |
| script |Hive スクリプトをインラインに指定します |いいえ |
| scriptPath |Hive スクリプトを Azure BLOB ストレージに格納し、ファイルへのパスを指定します。 'script' プロパティまたは 'scriptPath' プロパティを使用します。 両方を同時に使用することはできません。 ファイル名は大文字と小文字が区別されます。 |いいえ |
| defines |'hiveconf' を使用して Hive スクリプト内で参照するキーと値のペアとしてパラメーターを指定します |いいえ |

## <a name="example"></a>例
ゲームのログ分析の例について考えてみましょう。ここでは、お客様の会社が発売したゲームをユーザーがプレイした時間を特定します。 

次のログはゲーム ログのサンプルです。コンマ (`,`) で区切られていて、ProfileID、SessionStart、Duration、SrcIPAddress、GameType の各フィールドが含まれています。

```
1809,2014-05-04 12:04:25.3470000,14,221.117.223.75,CaptureFlag
1703,2014-05-04 06:05:06.0090000,16,12.49.178.247,KingHill
1703,2014-05-04 10:21:57.3290000,10,199.118.18.179,CaptureFlag
1809,2014-05-04 05:24:22.2100000,23,192.84.66.141,KingHill
.....
```

このデータを処理する **Hive スクリプト** :

```
DROP TABLE IF EXISTS HiveSampleIn; 
CREATE EXTERNAL TABLE HiveSampleIn 
(
    ProfileID        string, 
    SessionStart     string, 
    Duration         int, 
    SrcIPAddress     string, 
    GameType         string
) ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' LINES TERMINATED BY '10' STORED AS TEXTFILE LOCATION 'wasb://adfwalkthrough@<storageaccount>.blob.core.windows.net/samplein/'; 

DROP TABLE IF EXISTS HiveSampleOut; 
CREATE EXTERNAL TABLE HiveSampleOut 
(    
    ProfileID     string, 
    Duration     int
) ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' LINES TERMINATED BY '10' STORED AS TEXTFILE LOCATION 'wasb://adfwalkthrough@<storageaccount>.blob.core.windows.net/sampleout/';

INSERT OVERWRITE TABLE HiveSampleOut
Select 
    ProfileID,
    SUM(Duration)
FROM HiveSampleIn Group by ProfileID
```

Data Factory パイプラインでこの Hive スクリプトを実行するには、以下の手順を実行する必要があります。

1. リンクされたサービスを作成し、[独自の HDInsight コンピューティング クラスター](data-factory-compute-linked-services.md#azure-hdinsight-linked-service)に登録するか、または[オンデマンドの HDInsight コンピューティング クラスター](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service)を構成します。 このリンクされたサービスを "HDInsightLinkedService" と呼ぶことにしましょう。
2. [リンクされたサービス](data-factory-azure-blob-connector.md) を作成し、データをホストする Azure BLOB ストレージへの接続を構成します。 このリンクされたサービスを "StorageLinkedService" と呼ぶことにしましょう。
3. 入力と出力のデータを指定する [データセット](data-factory-create-datasets.md) を作成します。 入力データセットは "HiveSampleIn"、出力データセットは "HiveSampleOut" と呼ぶことにしましょう。
4. 上記の手順 2. で構成した Azure Blob Storage にファイルとして Hive クエリをコピーします。 データをホストするストレージがこのクエリ ファイルをホストするものと異なる場合には、別の Azure Storage のリンクされたサービスを作成し、アクティビティでこのリンクされたサービスを参照します。 **scriptPath** を使用して Hive クエリ ファイルへのパスを指定し、**scriptLinkedService** を使用して、このスクリプト ファイルを含む Azure Storage を指定します。 
   
   > [!NOTE]
   > また、**script** プロパティを使用して、アクティビティ定義で Hive スクリプトをインライン化することもできます。 JSON ドキュメント内のスクリプトのすべての特殊文字をエスケープする必要があり、デバッグの問題を引き起こすことがあるため、この方法はお勧めできません。 手順 4. の使用をお勧めします。
   > 
   > 
5. HDInsightHive アクティビティでパイプラインを作成します。 このアクティビティはデータの処理や変換を行います。

    ```JSON   
    {   
        "name": "HiveActivitySamplePipeline",
        "properties": {
        "activities": [
            {
                "name": "HiveActivitySample",
                "type": "HDInsightHive",
                "inputs": [
                {
                    "name": "HiveSampleIn"
                }
                ],
                "outputs": [
                {
                    "name": "HiveSampleOut"
                }
                ],
                "linkedServiceName": "HDInsightLinkedService",
                "typeproperties": {
                    "scriptPath": "adfwalkthrough\\scripts\\samplehive.hql",
                    "scriptLinkedService": "StorageLinkedService"
                },
                "scheduler": {
                    "frequency": "Hour",
                    "interval": 1
                }
            }
            ]
        }
    }
    ```
6. パイプラインをデプロイします。 詳細については、 [パイプラインの作成](data-factory-create-pipelines.md) に関する記事を参照してください。 
7. データ ファクトリの監視と管理のビューを使用して、パイプラインを監視します。 詳細については、 [Data Factory パイプラインの監視と管理](data-factory-monitor-manage-pipelines.md) に関する記事を参照してください。 

## <a name="specifying-parameters-for-a-hive-script"></a>Hive スクリプトのパラメーターの指定
ゲームのログが Azure BLOB ストレージに毎日取り込まれ、日付と時刻で分割されたフォルダーに格納される例を考えてみましょう。 Hive スクリプトをパラメーター化し、実行時に入力フォルダーの場所を動的に渡し、さらに、日付と時刻で分割された出力も生成する必要があるとします。

パラメーター化された Hive スクリプトを使用するには、次の手順に従います。

* **defines**でパラメーターを定義します。

    ```JSON  
    {
        "name": "HiveActivitySamplePipeline",
          "properties": {
        "activities": [
             {
                "name": "HiveActivitySample",
                "type": "HDInsightHive",
                "inputs": [
                      {
                        "name": "HiveSampleIn"
                      }
                ],
                "outputs": [
                      {
                        "name": "HiveSampleOut"
                    }
                ],
                "linkedServiceName": "HDInsightLinkedService",
                "typeproperties": {
                      "scriptPath": "adfwalkthrough\\scripts\\samplehive.hql",
                      "scriptLinkedService": "StorageLinkedService",
                      "defines": {
                        "Input": "$$Text.Format('wasb://adfwalkthrough@<storageaccountname>.blob.core.windows.net/samplein/yearno={0:yyyy}/monthno={0:MM}/dayno={0:dd}/', SliceStart)",
                        "Output": "$$Text.Format('wasb://adfwalkthrough@<storageaccountname>.blob.core.windows.net/sampleout/yearno={0:yyyy}/monthno={0:MM}/dayno={0:dd}/', SliceStart)"
                      },
                       "scheduler": {
                          "frequency": "Hour",
                          "interval": 1
                    }
                }
              }
        ]
      }
    }
    ```
* Hive スクリプトでは、 **${hiveconf:parameterName}** を使用してパラメーターを参照します。 
  
    ```
    DROP TABLE IF EXISTS HiveSampleIn; 
    CREATE EXTERNAL TABLE HiveSampleIn 
    (
        ProfileID     string, 
        SessionStart     string, 
        Duration     int, 
        SrcIPAddress     string, 
        GameType     string
    ) ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' LINES TERMINATED BY '10' STORED AS TEXTFILE LOCATION '${hiveconf:Input}'; 

    DROP TABLE IF EXISTS HiveSampleOut; 
    CREATE EXTERNAL TABLE HiveSampleOut 
    (
        ProfileID     string, 
        Duration     int
    ) ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' LINES TERMINATED BY '10' STORED AS TEXTFILE LOCATION '${hiveconf:Output}';

    INSERT OVERWRITE TABLE HiveSampleOut
    Select 
        ProfileID,
        SUM(Duration)
    FROM HiveSampleIn Group by ProfileID
    ```
  ## <a name="see-also"></a>関連項目
* [Pig アクティビティ](data-factory-pig-activity.md)
* [MapReduce アクティビティ](data-factory-map-reduce.md)
* [Hadoop ストリーミング アクティビティ](data-factory-hadoop-streaming-activity.md)
* [Spark プログラムを呼び出す](data-factory-spark.md)
* [R スクリプトを呼び出す](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/RunRScriptUsingADFSample)

