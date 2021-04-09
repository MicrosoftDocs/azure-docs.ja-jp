---
title: Azure Data Factory での Pig アクティビティを使用したデータ変換
description: Azure Data Factory v1 で Pig アクティビティを使用して、オンデマンドまたは独自の HDInsight クラスターで Pig スクリプトを実行する方法について説明します。
author: dcstwh
ms.author: weetok
ms.reviewer: jburchel
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/10/2018
ms.openlocfilehash: 85c34bfc306b49bb826a342846bf5e4557e3052f
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "104786364"
---
# <a name="transform-data-using-pig-activity-in-azure-data-factory"></a>Azure Data Factory での Pig アクティビティを使用したデータ変換
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
> この記事は、Data Factory のバージョン 1 に適用されます。 最新バージョンの Data Factory サービスを使用している場合は、[Data Factory での Pig アクティビティを使用したデータ変換](../transform-data-using-hadoop-pig.md)に関するページをご覧ください。


Data Factory [パイプライン](data-factory-create-pipelines.md)の HDInsight Pig アクティビティでは、[独自](data-factory-compute-linked-services.md#azure-hdinsight-linked-service)または[オンデマンド](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service)の Windows/Linux ベースの HDInsight クラスターで Pig クエリを実行します。 この記事は、データ変換とサポートされる変換アクティビティの概要を説明する、 [データ変換アクティビティ](data-factory-data-transformation-activities.md) に関する記事に基づいています。

> [!NOTE] 
> Azure Data Factory の使用経験がない場合は、この記事を読む前に、「[Azure Data Factory の概要](data-factory-introduction.md)」を参照し、[最初のデータ パイプラインの作成](data-factory-build-your-first-pipeline.md)チュートリアルを実行してください。 

## <a name="syntax"></a>構文

```JSON
{
  "name": "HiveActivitySamplePipeline",
  "properties": {
    "activities": [
      {
        "name": "Pig Activity",
        "description": "description",
        "type": "HDInsightPig",
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
          "script": "Pig script",
          "scriptPath": "<pathtothePigscriptfileinAzureblobstorage>",
          "defines": {
            "param1": "param1Value"
          }
        },
        "scheduler": {
          "frequency": "Day",
          "interval": 1
        }
      }
    ]
  }
}
```

## <a name="syntax-details"></a>構文の詳細

| プロパティ | 説明 | 必須 |
| --- | --- | --- |
| name |アクティビティの名前 |はい |
| description |アクティビティの用途を説明するテキストです。 |いいえ |
| type |HDInsightPig |はい |
| inputs |Pig のアクティビティによって使用される 1 つ以上の入力 |いいえ |
| outputs |Pig のアクティビティによって生成される 1 つ以上の出力 |はい |
| linkedServiceName |Data Factory のリンクされたサービスとして登録されている HDInsight クラスターへの参照 |はい |
| script |Pig スクリプトをインラインに指定します |いいえ |
| scriptPath |Pig スクリプトを Azure BLOB ストレージに格納し、ファイルへのパスを指定します。 'script' プロパティまたは 'scriptPath' プロパティを使用します。 両方を同時に使用することはできません。 ファイル名は大文字と小文字が区別されます。 |いいえ |
| defines |Pig スクリプト内で参照するキーと値のペアとしてパラメーターを指定します |いいえ |

## <a name="example"></a>例
ゲームのログ分析の例について考えてみましょう。ここでは、お客様の会社が発売したゲームをユーザーがプレイした時間を特定します。

以下のサンプル ゲーム ログは、コンマ (,) 区切りのファイルです。 このファイルには、ProfileID、SessionStart、Duration、SrcIPAddress、GameType の各フィールドが含まれています。

```
1809,2014-05-04 12:04:25.3470000,14,221.117.223.75,CaptureFlag
1703,2014-05-04 06:05:06.0090000,16,12.49.178.247,KingHill
1703,2014-05-04 10:21:57.3290000,10,199.118.18.179,CaptureFlag
1809,2014-05-04 05:24:22.2100000,23,192.84.66.141,KingHill
.....
```

このデータを処理する **Pig スクリプト** :

```
PigSampleIn = LOAD 'wasb://adfwalkthrough@anandsub14.blob.core.windows.net/samplein/' USING PigStorage(',') AS (ProfileID:chararray, SessionStart:chararray, Duration:int, SrcIPAddress:chararray, GameType:chararray);

GroupProfile = Group PigSampleIn all;

PigSampleOut = Foreach GroupProfile Generate PigSampleIn.ProfileID, SUM(PigSampleIn.Duration);

Store PigSampleOut into 'wasb://adfwalkthrough@anandsub14.blob.core.windows.net/sampleoutpig/' USING PigStorage (',');
```

Data Factory パイプラインでこの Pig スクリプトを実行するには、以下の手順を実行します。

1. リンクされたサービスを作成し、[独自の HDInsight コンピューティング クラスター](data-factory-compute-linked-services.md#azure-hdinsight-linked-service)に登録するか、または[オンデマンドの HDInsight コンピューティング クラスター](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service)を構成します。 このリンクされたサービスを **HDInsightLinkedService** と呼ぶことにしましょう。
2. [リンクされたサービス](data-factory-azure-blob-connector.md) を作成し、データをホストする Azure BLOB ストレージへの接続を構成します。 このリンクされたサービスを **StorageLinkedService** と呼ぶことにしましょう。
3. 入力と出力のデータを指定する [データセット](data-factory-create-datasets.md) を作成します。 入力データセットは **PigSampleIn**、出力データセットは **PigSampleOut** と呼ぶことにしましょう。
4. 上記の手順 2. で構成した Azure BLOB ストレージのファイルに Pig クエリをコピーします。 データをホストするリンクされた Azure ストレージが、クエリ ファイルをホストするものと異なる場合には、別の Azure Storage のリンクされたサービスを作成します。 アクティビティの構成で、このリンクされたサービスを参照します。 **scriptPath** を使用して、pig スクリプト ファイルへのパスと **scriptLinkedService** を指定します。 
   
   > [!NOTE]
   > また、**script** プロパティを使用して、アクティビティ定義で Pig スクリプトをインライン化することもできます。 ただし、スクリプトのすべての特殊文字をエスケープする必要があり、デバッグの問題を引き起こすことがあるため、この方法はお勧めできません。 手順 4. の使用をお勧めします。
   >
   >
5. HDInsightPig アクティビティでパイプラインを作成します。 このアクティビティは、HDInsight クラスターで Pig スクリプトを実行して、入力データを処理します。

    ```JSON
    {
      "name": "PigActivitySamplePipeline",
      "properties": {
        "activities": [
          {
            "name": "PigActivitySample",
            "type": "HDInsightPig",
            "inputs": [
              {
                "name": "PigSampleIn"
              }
            ],
            "outputs": [
              {
                "name": "PigSampleOut"
              }
            ],
            "linkedServiceName": "HDInsightLinkedService",
            "typeproperties": {
              "scriptPath": "adfwalkthrough\\scripts\\enrichlogs.pig",
              "scriptLinkedService": "StorageLinkedService"
            },
            "scheduler": {
              "frequency": "Day",
              "interval": 1
            }
          }
        ]
      }
    }
    ```
6. パイプラインをデプロイします。 詳細については、 [パイプラインの作成](data-factory-create-pipelines.md) に関する記事を参照してください。 
7. データ ファクトリの監視と管理のビューを使用して、パイプラインを監視します。 詳細については、 [Data Factory パイプラインの監視と管理](data-factory-monitor-manage-pipelines.md) に関する記事を参照してください。

## <a name="specifying-parameters-for-a-pig-script"></a>Pig スクリプトのパラメーターの指定
ゲームのログが Azure BLOB ストレージに毎日取り込まれ、日付と時刻に基づいて分割されたフォルダーに格納される例を考えてみましょう。 Pig スクリプトをパラメーター化し、実行時に入力フォルダーの場所を動的に渡し、さらに、日付と時刻で分割された出力も生成する必要があるとします。

パラメーター化された Pig スクリプトを使用するには、以下の手順を実行します。

* **defines** でパラメーターを定義します。

    ```JSON
    {
      "name": "PigActivitySamplePipeline",
      "properties": {
        "activities": [
          {
            "name": "PigActivitySample",
            "type": "HDInsightPig",
            "inputs": [
              {
                "name": "PigSampleIn"
              }
            ],
            "outputs": [
              {
                "name": "PigSampleOut"
              }
            ],
            "linkedServiceName": "HDInsightLinkedService",
            "typeproperties": {
              "scriptPath": "adfwalkthrough\\scripts\\samplepig.hql",
              "scriptLinkedService": "StorageLinkedService",
              "defines": {
                "Input": "$$Text.Format('wasb: //adfwalkthrough@<storageaccountname>.blob.core.windows.net/samplein/yearno={0: yyyy}/monthno={0:MM}/dayno={0: dd}/',SliceStart)",
                "Output": "$$Text.Format('wasb://adfwalkthrough@<storageaccountname>.blob.core.windows.net/sampleout/yearno={0:yyyy}/monthno={0:MM}/dayno={0:dd}/', SliceStart)"
              }
            },
            "scheduler": {
              "frequency": "Day",
              "interval": 1
            }
          }
        ]
      }
    }
    ```
* 次の例に示すように、' **$parameterName**' を使用するパラメーターを Pig スクリプトで参照します。

    ```
    PigSampleIn = LOAD '$Input' USING PigStorage(',') AS (ProfileID:chararray, SessionStart:chararray, Duration:int, SrcIPAddress:chararray, GameType:chararray);
    GroupProfile = Group PigSampleIn all;
    PigSampleOut = Foreach GroupProfile Generate PigSampleIn.ProfileID, SUM(PigSampleIn.Duration);
    Store PigSampleOut into '$Output' USING PigStorage (','); 
    ```

## <a name="see-also"></a>参照
* [Hive アクティビティ](data-factory-hive-activity.md)
* [MapReduce アクティビティ](data-factory-map-reduce.md)
* [Hadoop ストリーミング アクティビティ](data-factory-hadoop-streaming-activity.md)
* [Spark プログラムを呼び出す](data-factory-spark.md)
* [R スクリプトを呼び出す](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV1/RunRScriptUsingADFSample)
