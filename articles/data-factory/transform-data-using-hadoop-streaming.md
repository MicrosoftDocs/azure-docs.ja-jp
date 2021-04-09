---
title: Hadoop ストリーミング アクティビティを使用したデータ変換
description: Azure Data Factory で Hadoop Streaming アクティビティを使用して、Hadoop クラスターで Hadoop Streaming プログラムを実行することによってデータを変換する方法について説明します。
author: nabhishek
ms.author: abnarain
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 05/08/2020
ms.openlocfilehash: e2a9bc9d664ba15da3cdefa5cf28519ab703d6ce
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "100361436"
---
# <a name="transform-data-using-hadoop-streaming-activity-in-azure-data-factory"></a>Azure Data Factory での Hadoop Streaming アクティビティを使用したデータの変換
> [!div class="op_single_selector" title1="使用している Data Factory サービスのバージョンを選択してください:"]
> * [Version 1](v1/data-factory-hadoop-streaming-activity.md)
> * [現在のバージョン](transform-data-using-hadoop-streaming.md)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Data Factory [パイプライン](concepts-pipelines-activities.md)の HDInsight Streaming アクティビティでは、[独自の](compute-linked-services.md#azure-hdinsight-linked-service)または[オンデマンドの](compute-linked-services.md#azure-hdinsight-on-demand-linked-service) HDInsight クラスターで Hadoop Streaming プログラムを実行します。 この記事は、データ変換とサポートされる変換アクティビティの概要を説明する、 [データ変換アクティビティ](transform-data.md) に関する記事に基づいています。

Azure Data Factory の使用経験がない場合は、この記事を読む前に、「[Azure Data Factory の概要](introduction.md)」を参照し、[データの変換のチュートリアル](tutorial-transform-data-spark-powershell.md)を実行してください。 

## <a name="json-sample"></a>JSON のサンプル
```json
{
    "name": "Streaming Activity",
    "description": "Description",
    "type": "HDInsightStreaming",
    "linkedServiceName": {
        "referenceName": "MyHDInsightLinkedService",
        "type": "LinkedServiceReference"
    },
    "typeProperties": {
        "mapper": "MyMapper.exe",
        "reducer": "MyReducer.exe",
        "combiner": "MyCombiner.exe",
        "fileLinkedService": {
            "referenceName": "MyAzureStorageLinkedService",
            "type": "LinkedServiceReference"
        },
        "filePaths": [
            "<containername>/example/apps/MyMapper.exe",
            "<containername>/example/apps/MyReducer.exe",
            "<containername>/example/apps/MyCombiner.exe"
        ],
        "input": "wasb://<containername>@<accountname>.blob.core.windows.net/example/input/MapperInput.txt",
        "output": "wasb://<containername>@<accountname>.blob.core.windows.net/example/output/ReducerOutput.txt",
        "commandEnvironment": [
            "CmdEnvVarName=CmdEnvVarValue"
        ],
        "getDebugInfo": "Failure",
        "arguments": [
            "SampleHadoopJobArgument1"
        ],
        "defines": {
            "param1": "param1Value"
        }
    }
}
```

## <a name="syntax-details"></a>構文の詳細

| プロパティ          | 説明                              | 必須 |
| ----------------- | ---------------------------------------- | -------- |
| name              | アクティビティの名前                     | はい      |
| description       | アクティビティの用途を説明するテキストです。 | いいえ       |
| type              | Hadoop Streaming アクティビティの場合、アクティビティの種類は HDInsightStreaming です。 | はい      |
| linkedServiceName | Data Factory のリンクされたサービスとして登録されている HDInsight クラスターへの参照。 このリンクされたサービスの詳細については、[計算のリンクされたサービス](compute-linked-services.md)に関する記事をご覧ください。 | はい      |
| mapper            | mapper 実行可能ファイルの名前を指定します。 | はい      |
| reducer           | reducer 実行可能ファイルの名前を指定します。 | はい      |
| combiner          | combiner 実行可能ファイルの名前を指定します。 | いいえ       |
| fileLinkedService | 実行されるマッパー、コンバイナー、レジューサの各プログラムを格納するために使用される Azure Storage のリンクされたサービスへの参照。 ここでは **[Azure Blob Storage](./connector-azure-blob-storage.md)** および **[ADLS Gen2](./connector-azure-data-lake-storage.md)** にリンクされたサービスのみがサポートされています。 このリンクされたサービスを指定していない場合は、HDInsight のリンクされたサービスで定義されている Azure Storage のリンクされたサービスが使用されます。 | いいえ       |
| filePath          | fileLinkedService によって参照される、Azure Storage に格納された マッパー、コンバイナー、レジューサ の各プログラムのパスの配列を指定します。 パスの大文字と小文字は区別されます。 | はい      |
| input             | マッパーの入力ファイルの WASB パスを指定します。 | はい      |
| output            | レジューサの出力ファイルの WASB パスを指定します。 | はい      |
| getDebugInfo      | HDInsight クラスターで使用されている Azure Storage または scriptLinkedService で指定された Azure Storage にログ ファイルがコピーされるタイミングを指定します。 使用できる値は以下の通りです。None、Always、または Failure。 既定値:[なし] : | いいえ       |
| 引数         | Hadoop ジョブの引数の配列を指定します。 引数はコマンド ライン引数として各タスクに渡されます。 | いいえ       |
| defines           | Hive スクリプト内で参照するキーと値のペアとしてパラメーターを指定します。 | いいえ       | 

## <a name="next-steps"></a>次のステップ
別の手段でデータを変換する方法を説明している次の記事を参照してください。 

* [U-SQL アクティビティ](transform-data-using-data-lake-analytics.md)
* [Hive アクティビティ](transform-data-using-hadoop-hive.md)
* [Pig アクティビティ](transform-data-using-hadoop-pig.md)
* [MapReduce アクティビティ](transform-data-using-hadoop-map-reduce.md)
* [Spark アクティビティ](transform-data-using-spark.md)
* [.NET カスタム アクティビティ](transform-data-using-dotnet-custom-activity.md)
* [Azure Machine Learning スタジオ (クラシック) のバッチ実行アクティビティ](transform-data-using-machine-learning.md)
* [ストアド プロシージャ アクティビティ](transform-data-using-stored-procedure.md)