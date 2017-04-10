---
title: "Azure Data Factory から Spark プログラムを呼び出す"
description: "MapReduce アクティビティを使用して Azure Data Factory から Spark プログラムを呼び出す方法について説明します。"
services: data-factory
documentationcenter: 
author: spelluru
manager: jhubbard
editor: monicar
ms.assetid: fd98931c-cab5-4d66-97cb-4c947861255c
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/21/2017
ms.author: spelluru
translationtype: Human Translation
ms.sourcegitcommit: 432752c895fca3721e78fb6eb17b5a3e5c4ca495
ms.openlocfilehash: 3c7d109ec7fd92859cad4ded7422105e8094485c
ms.lasthandoff: 03/30/2017


---
# <a name="invoke-spark-programs-from-data-factory"></a>Data Factory から Spark プログラムを起動する
> [!div class="op_single_selector" title1="Transformation Activities"]
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

## <a name="introduction"></a>はじめに
Data Factory [パイプライン](data-factory-create-pipelines.md)の HDInsight Spark アクティビティでは、[独自の](data-factory-compute-linked-services.md#azure-hdinsight-linked-service) HDInsight クラスターで Spark プログラムを実行します。 この記事は、データ変換とサポートされる変換アクティビティの概要を説明する、 [データ変換アクティビティ](data-factory-data-transformation-activities.md) に関する記事に基づいています。

> [!IMPORTANT]
> Azure Data Factory を初めて使う場合は、この記事の前に、[最初のパイプラインの作成](data-factory-build-your-first-pipeline.md)チュートリアルに従って操作することをお勧めします。 Data Factory サービスの概要については、[Azure Data Factory の概要](data-factory-introduction.md)に関するページをご覧ください。 

## <a name="apache-spark-cluster-in-azure-hdinsight"></a>Azure HDInsight の Apache Spark クラスター
最初に、[Azure HDInsight での Apache Spark クラスターの作成](../hdinsight/hdinsight-apache-spark-jupyter-spark-sql.md)チュートリアルの説明に従って、Azure HDInsight で Apache Spark クラスターを作成します。 

## <a name="create-data-factory"></a>データ ファクトリの作成 
Spark アクティビティで Data Factory パイプラインを作成する一般的な手順を次に示します。  

1. データ ファクトリを作成する
2. Azure HDInsight の Apache Spark クラスターをデータ ファクトリにリンクする、リンクされたサービスを作成します。
3. 現時点では、出力が生成されていなくても、アクティビティに対する出力データセットを指定する必要があります。 Azure BLOB データセットを作成するには、次の手順を実行します。
    1. Azure ストレージ アカウントをデータ ファクトリにリンクする、リンクされたサービスを作成します。
    2. Azure Storage のリンクされたサービスを参照するデータセットを作成します。  
3. 手順 2 で作成した Apache HDInsight のリンクされたサービスを参照する、Spark アクティビティでパイプラインを作成します。 アクティビティは、前の手順で出力データセットとして作成したデータセットで構成されます。 出力データセットは、スケジュール (1 時間に 1 回、毎日など) を開始するため、 アクティビティによって出力が実際に生成されていなくても、指定する必要があります。

データ ファクトリを作成する手順の詳細については、[最初のパイプラインの作成](data-factory-build-your-first-pipeline.md)チュートリアルを参照してください。 このチュートリアルでは、Hive アクティビティと HDInsight Hadoop クラスターが使用されますが、その手順は、Spark アクティビティと HDInsight Spark クラスターを使用する手順と似ています。   

次のセクションでは、データ ファクトリで Apache Spark クラスターと Spark アクティビティを使用するための Data Factory エンティティに関する情報を提供します。   

## <a name="hdinsight-linked-service"></a>HDInsight のリンクされたサービス
Data Factory パイプラインで Spark アクティビティを使用する前に、(ご自身の) HDInsight のリンクされたサービスを作成します。 次の JSON スニペットは、Azure HDInsight Spark クラスターを指定する、HDInsight のリンクされたサービスの定義を示しています。   

```json
{
    "name": "HDInsightLinkedService",
    "properties": {
        "type": "HDInsight",
        "typeProperties": {
            "clusterUri": "https://<nameofyoursparkcluster>.azurehdinsight.net/",
              "userName": "admin",
              "password": "password",
              "linkedServiceName": "MyHDInsightStoragelinkedService"
        }
    }
}
```

> [!NOTE]
> 現在、Spark アクティビティでは、Azure Data Lake Store をプライマリ ストレージとして使用する Spark クラスター、または HDInsight のオンデマンドのリンクされたサービスはサポートされません。 

HDInsight のリンクされたサービスおよびその他のコンピューティングのリンクされたサービスの詳細については、[Data Factory コンピューティングのリンクされたサービス](data-factory-compute-linked-services.md)に関するページをご覧ください。 

## <a name="spark-activity-json"></a>Spark アクティビティ JSON
Spark アクティビティを使用するパイプラインのサンプル JSON 定義を次に示します。    

```json
{
    "name": "SparkPipeline",
    "properties": {
        "activities": [
            {
                "type": "HDInsightSpark",
                "typeProperties": {
                    "rootPath": "adfspark\\pyFiles",
                    "entryFilePath": "test.py",
                    "arguments": [ "arg1", "arg2" ],
                    "sparkConfig": {
                        "spark.python.worker.memory": "512m"
                    }
                    "getDebugInfo": "Always"
                },
                "outputs": [
                    {
                        "name": "OutputDataset"
                    }
                ],
                "name": "MySparkActivity",
                "description": "This activity invokes the Spark program",
                "linkedServiceName": "HDInsightLinkedService"
            }
        ],
        "start": "2017-02-01T00:00:00Z",
        "end": "2017-02-02T00:00:00Z"
    }
}
```

以下の点に注意してください。 
- type プロパティは HDInsightSpark に設定されます。 
- rootPath は adfspark\\pyFiles に設定されます。ここで、adfspark は Azure BLOB コンテナー、pyFiles はそのコンテナーのファイル フォルダーです。 この例では、Azure Blob Storage は、Spark クラスターに関連付けられています。 ファイルは、別の Azure Storage にアップロードできます。 これを行う場合は、ストレージ アカウントをデータ ファクトリにリンクする Azure Storage のリンクされたサービスを作成します。 次に、リンクされたサービスの名前を、sparkJobLinkedService プロパティの値として指定します。 このプロパティと、Spark アクティビティでサポートされている他のプロパティの詳細については、「[Spark アクティビティのプロパティ](#spark-activity-properties)」を参照してください。  
- entryFilePath は、python ファイルである test.py に設定されます。 
- Spark プログラムのパラメーターの値は、引数のプロパティを使用して渡されます。 この例では、arg1 と arg2 の 2 つの引数があります。 
- getDebugInfo プロパティは Always に設定されます。つまり、ログ ファイルが常に生成されます (成功または失敗)。 
- sparkConfig セクションでは、1 つの python 環境設定 worker.memory を指定します。 
- 出力セクションには、1 つの出力データセットがあります。 出力データセットは、Spark プログラムによって出力が生成されなくても指定する必要があります。 出力データセットは、パイプラインのスケジュール (1 時間に 1 回、毎日など) を開始します。     

(typeProperties セクションの) type プロパティについては、この記事の「[Spark アクティビティのプロパティ](#spark-activity-properties)」を参照してください。 

前述のように、出力データセットはパイプラインのスケジュール (1 時間に 1 回、毎日など) を開始するため、アクティビティに対して指定する必要があります。 この例では、Azure BLOB データセットが使用されます。 Azure BLOB データセットを作成するには、最初に Azure Storage のリンクされたサービスを作成する必要があります。 

Azure Storage のリンクされたサービスと Azure BLOB データセットのサンプル定義を次に示します。 

**Azure Storage のリンクされたサービス:**
```json
{
    "name": "AzureStorageLinkedService",
    "properties": {
        "type": "AzureStorage",
        "typeProperties": {
            "connectionString": "DefaultEndpointsProtocol=https;AccountName=<storageaccountname>;AccountKey=<storageaccountkey>"
        }
    }
}
```
 

**Azure BLOB データセット:** 
```json
{
    "name": "OutputDataset",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "AzureStorageLinkedService",
        "typeProperties": {
            "fileName": "sparkoutput.txt",
            "folderPath": "spark/output/",
            "format": {
                "type": "TextFormat",
                "columnDelimiter": "\t"
            }
        },
        "availability": {
            "frequency": "Day",
            "interval": 1
        }
    }
}
```

このデータセットは、どちらかというとダミーのデータセットです。 Data Factory では頻度と間隔の設定を使用して、パイプラインを開始してから終了するまでの間、毎日実行します。 サンプル パイプラインの定義では、開始時刻と終了時刻が 1 日しか離れていないため、パイプラインが実行されるのは 1 回だけです。 

## <a name="spark-activity-properties"></a>Spark アクティビティのプロパティ

次の表で、JSON 定義で使用される JSON プロパティについて説明します。 

| プロパティ | 説明 | 必須 |
| -------- | ----------- | -------- |
| name | パイプラインのアクティビティの名前。 | はい |
| 説明 | アクティビティの動作を説明するテキスト。 | いいえ |
| type | このプロパティは HDInsightSpark に設定する必要があります。 | はい |
| 既定のコンテナー | Spark プログラムが実行されている HDInsight のリンクされたサービスの名前。 | はい |
| rootPath | Azure BLOB コンテナーと Spark ファイルを含むフォルダー。 ファイル名は大文字と小文字が区別されます。 | はい |
| entryFilePath | Spark コード/パッケージのルート フォルダーへの相対パス。 | はい |
| className | アプリケーションの Java/Spark のメイン クラス | なし | 
| arguments | Spark プログラムのコマンドライン引数の一覧です。 | いいえ | 
| proxyUser | Spark プログラムの実行を偽装する借用すユーザー アカウント | いいえ | 
| sparkConfig | Spark 構成のプロパティ。 | いいえ | 
| getDebugInfo | HDInsight クラスターで使用されている Azure Storage または sparkJobLinkedService で指定された Azure Storage に Spark ログ ファイルがコピーされるタイミングを指定します。 使用できる値: None、Always、または Failure。 既定値: None。 | なし | 
| sparkJobLinkedService | Spark ジョブ ファイル、依存関係、およびログが含まれる Azure Storage のリンクされたサービス。  指定しない場合は、HDInsight クラスターに関連付けられているストレージが使用されます。 | なし |

## <a name="folder-structure"></a>フォルダー構造
Spark アクティビティでは、Pig および Hive アクティビティが実行するインライン スクリプトがサポートされません。 また、Spark ジョブは、Pig/Hive ジョブよりも拡張できます。 Spark ジョブの場合、jar パッケージ (java CLASSPATH に配置)、python ファイル (PYTHONPATH に配置) など、複数の依存関係を利用できます。

HDInsight のリンクされたサービスによって参照される Azure Blob Storage に、次のフォルダー構造を作成します。 その後、依存ファイルを、**entryFilePath** で表されるルート フォルダー内の適切なサブフォルダーにアップロードします。 たとえば、python ファイルはルート フォルダーの pyFiles サブフォルダーに、jar ファイルはルート フォルダーの jar サブフォルダーにアップロードします。 実行時、Data Factory サービスに必要な Azure Blob Storage のフォルダー構造を次に示します。     

| パス | Description | 必須 | 型 |
| ---- | ----------- | -------- | ---- | 
| をクリックします。    | ストレージのリンクされたサービスにおける Spark ジョブのルート パス    | はい | フォルダー |
| &lt;user defined &gt; | Spark ジョブの入力ファイルを指定するパス | はい | ファイル | 
| ./jars | このフォルダーのすべてのファイルがアップロードされ、クラスターの java classpath に配置されます | なし | フォルダー |
| ./pyFiles | このフォルダーのすべてのファイルがアップロードされ、クラスターの PYTHONPATH に配置されます | いいえ | フォルダー |
| ./files | このフォルダーのすべてのファイルがアップロードされ、Executor 作業ディレクトリに配置されます | いいえ | フォルダー |
| ./archives | このフォルダーのファイルは圧縮されていません | なし | フォルダー |
| ./logs | Spark クラスターのログが格納されているフォルダー。| なし | フォルダー |

次の例のストレージには、HDInsight のリンクされたサービスによって参照される Azure Blob Storage に 2 つの Spark ジョブ ファイルが含まれています。 

```
SparkJob1
    main.jar
    files
        input1.txt
        input2.txt
    jars
        package1.jar
        package2.jar
    logs

SparkJob2
    main.py
    pyFiles
        scrip1.py
        script2.py
    logs    
```

> [!IMPORTANT]
> 変換アクティビティでパイプラインを作成する方法をさらに詳しく解説したチュートリアルについては、[データを変換するパイプラインの作成](data-factory-build-your-first-pipeline-using-editor.md)に関する記事をご覧ください。 



## <a name="see-also"></a>関連項目
* [Hive アクティビティ](data-factory-hive-activity.md)
* [Pig アクティビティ](data-factory-pig-activity.md)
* [MapReduce アクティビティ](data-factory-map-reduce.md)
* [Hadoop ストリーミング アクティビティ](data-factory-hadoop-streaming-activity.md)
* [R スクリプトを呼び出す](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/RunRScriptUsingADFSample)


