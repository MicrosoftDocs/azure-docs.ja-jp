---
title: "Azure Data Factory で Spark アクティビティを使用してデータを変換する | Microsoft Docs"
description: "Spark アクティビティを使用して、Azure Data Factory パイプラインから Spark プログラムを実行することによってデータを変換する方法について説明します。"
services: data-factory
documentationcenter: 
author: shengcmsft
manager: jhubbard
editor: spelluru
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/10/2017
ms.author: shengc
ms.translationtype: HT
ms.sourcegitcommit: c3a2462b4ce4e1410a670624bcbcec26fd51b811
ms.openlocfilehash: ecef890d26aa097d9a5db63555ae59e16e80c9e1
ms.contentlocale: ja-jp
ms.lasthandoff: 09/25/2017

---
# <a name="transform-data-using-spark-activity-in-azure-data-factory"></a>Azure Data Factory での Spark アクティビティを使用したデータの変換
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [バージョン 1 - GA](v1/data-factory-spark.md)
> * [バージョン 2 - プレビュー](transform-data-using-spark.md)


Spark アクティビティは、Azure Data Factory でサポートされる[データ変換アクティビティ](transform-data.md)の 1 つです。 このアクティビティでは、指定された Spark プログラムが Azure HDInsight の Apache Spark クラスターで実行されます。    

> [!NOTE]
> この記事は、現在プレビュー段階にある Data Factory のバージョン 2 に適用されます。 一般公開 (GA) されている Data Factory サービスのバージョン 1 を使用している場合は、[V1 の Spark アクティビティ](v1/data-factory-spark.md)を参照してください。

> [!IMPORTANT]
> Spark アクティビティでは、Azure Data Lake Store をプライマリ ストレージとして使用する HDInsight Spark クラスターはサポートされません。

## <a name="spark-activity-properties"></a>Spark アクティビティのプロパティ
Spark アクティビティのサンプルの JSON 定義を次に示します。    

```json
{
    "name": "Spark Activity",
    "description": "Description",
    "type": "HDInsightSpark",
    "linkedServiceName": {
        "referenceName": "MyHDInsightLinkedService",
        "type": "LinkedServiceReference"
    },
    "typeProperties": {
        "sparkJobLinkedService": {
            "referenceName": "MyAzureStorageLinkedService",
            "type": "LinkedServiceReference"
        },
        "rootPath": "adfspark\\pyFiles",
        "entryFilePath": "test.py",
        "arguments": [ "arg1", "arg2" ],
        "sparkConfig": {
            "ConfigItem1": "Value"
        },
        "getDebugInfo": "Failure",
        "arguments": [
            "SampleHadoopJobArgument1"
        ]
    }
}
```

次の表で、JSON 定義で使用される JSON プロパティについて説明します。

| プロパティ              | 説明                              | 必須 |
| --------------------- | ---------------------------------------- | -------- |
| name                  | パイプラインのアクティビティの名前。    | はい      |
| 説明           | アクティビティの動作を説明するテキスト。  | いいえ       |
| type                  | Spark アクティビティの場合、アクティビティの種類は HDInsightSpark です。 | あり      |
| 既定のコンテナー     | Spark プログラムが実行されている HDInsight Spark のリンクされたサービスの名前。 このリンクされたサービスの詳細については、[計算のリンクされたサービス](compute-linked-services.md)に関する記事をご覧ください。 | あり      |
| SparkJobLinkedService | Spark ジョブ ファイル、依存関係、およびログが含まれる Azure Storage のリンクされたサービス。  指定しない場合は、HDInsight クラスターに関連付けられているストレージが使用されます。 | いいえ       |
| rootPath              | Azure BLOB コンテナーと Spark ファイルを含むフォルダー。 ファイル名は大文字と小文字が区別されます。 このフォルダーの構造の詳細については、「フォルダー構造」(次のセクション) をご覧ください。 | あり      |
| entryFilePath         | Spark コード/パッケージのルート フォルダーへの相対パス。 | はい      |
| className             | アプリケーションの Java/Spark のメイン クラス      | なし       |
| arguments             | Spark プログラムのコマンドライン引数の一覧です。 | いいえ       |
| proxyUser             | Spark プログラムの実行を偽装する借用すユーザー アカウント | いいえ       |
| sparkConfig           | 「[Spark Configuration - Application properties (Spark 構成 - アプリケーションのプロパティ)](https://spark.apache.org/docs/latest/configuration.html#available-properties)」と題するトピックに示されている Spark 構成プロパティの値を指定します。 | いいえ       |
| getDebugInfo          | HDInsight クラスターで使用されている Azure Storage または sparkJobLinkedService で指定された Azure Storage に Spark ログ ファイルがコピーされるタイミングを指定します。 使用できる値: None、Always、または Failure。 既定値: None。 | いいえ       |

## <a name="folder-structure"></a>フォルダー構造
Spark ジョブは、Pig/Hive ジョブよりも拡張性に優れています。 Spark ジョブの場合、jar パッケージ (java CLASSPATH に配置)、python ファイル (PYTHONPATH に配置) など、複数の依存関係を利用できます。

HDInsight のリンクされたサービスによって参照される Azure Blob Storage に、次のフォルダー構造を作成します。 その後、依存ファイルを、**entryFilePath** で表されるルート フォルダー内の適切なサブフォルダーにアップロードします。 たとえば、python ファイルはルート フォルダーの pyFiles サブフォルダーに、jar ファイルはルート フォルダーの jar サブフォルダーにアップロードします。 実行時、Data Factory サービスに必要な Azure Blob Storage のフォルダー構造を次に示します。     

| パス                  | Description                              | 必須 | 型   |
| --------------------- | ---------------------------------------- | -------- | ------ |
| `.` (ルート)            | ストレージのリンクされたサービスにおける Spark ジョブのルート パス | はい      | フォルダー |
| &lt;user defined &gt; | Spark ジョブの入力ファイルを指定するパス | はい      | ファイル   |
| ./jars                | このフォルダーのすべてのファイルがアップロードされ、クラスターの java classpath に配置されます | なし       | フォルダー |
| ./pyFiles             | このフォルダーのすべてのファイルがアップロードされ、クラスターの PYTHONPATH に配置されます | いいえ       | フォルダー |
| ./files               | このフォルダーのすべてのファイルがアップロードされ、Executor 作業ディレクトリに配置されます | いいえ       | フォルダー |
| ./archives            | このフォルダーのファイルは圧縮されていません | なし       | フォルダー |
| ./logs                | Spark クラスターのログが格納されているフォルダー。 | いいえ       | フォルダー |

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
## <a name="next-steps"></a>次のステップ
他の方法でデータを変換する方法を説明する次の記事を参照してください。 

* [U-SQL アクティビティ](transform-data-using-data-lake-analytics.md)
* [Hive アクティビティ](transform-data-using-hadoop-hive.md)
* [Pig アクティビティ](transform-data-using-hadoop-pig.md)
* [MapReduce アクティビティ](transform-data-using-hadoop-map-reduce.md)
* [Hadoop Streaming アクティビティ](transform-data-using-hadoop-streaming.md)
* [Spark アクティビティ](transform-data-using-spark.md)
* [.NET カスタム アクティビティ](transform-data-using-dotnet-custom-activity.md)
* [Machine Learning バッチ実行アクティビティ](transform-data-using-machine-learning.md)
* [ストアド プロシージャ アクティビティ](transform-data-using-stored-procedure.md)

