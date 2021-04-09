---
title: Azure Data Factory のパイプラインとアクティビティ
description: Azure Data Factory のパイプラインとアクティビティについて。
author: dcstwh
ms.author: weetok
ms.service: data-factory
ms.topic: conceptual
ms.date: 11/19/2019
ms.openlocfilehash: 870c812a68f765f987cfd3d1b953e0afeb3e9055
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "100364530"
---
# <a name="pipelines-and-activities-in-azure-data-factory"></a>Azure Data Factory のパイプラインとアクティビティ

> [!div class="op_single_selector" title1="使用している Data Factory サービスのバージョンを選択します。"]
> * [Version 1](v1/data-factory-create-pipelines.md)
> * [現在のバージョン](concepts-pipelines-activities.md)
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

この記事では、Azure Data Factory のパイプラインとアクティビティの概要、およびそれらを利用して、データ移動シナリオやデータ処理シナリオ用のエンド ツー エンドのデータ主導ワークフローを作成する方法について説明します。

## <a name="overview"></a>概要
データ ファクトリは、1 つまたは複数のパイプラインを持つことができます。 パイプラインは、1 つのタスクを連携して実行するアクティビティの論理的なグループです。 たとえば、ログ データを取り込んでクリーニングしてから、マッピング データ フローを開始してそのログ データを分析するアクティビティのセットをパイプラインに組み込むこともできます。 パイプラインを使用すると、各アクティビティを個別に管理するのではなく、セットとして管理できます。 デプロイとスケジュール設定を、アクティビティごとではなく、パイプライン単位で行うことができます。

パイプライン内の複数のアクティビティは、データに対して実行するアクションを定義します。 たとえば、コピー アクティビティを使用して、SQL Server から Azure Blob Storage にデータをコピーできます。 次に、データ フロー アクティビティまたは Databricks Notebook アクティビティを使用して、BLOB ストレージから、ビジネス インテリジェンス レポート ソリューションが構築された Azure Synapse Analytics プールにデータを処理して変換します。

Data Factory では、[データ移動アクティビティ](copy-activity-overview.md)、[データ変換アクティビティ](transform-data.md)、[制御アクティビティ](#control-flow-activities)の 3 種類のアクティビティ グループがあります。 アクティビティは 0 個以上の入力[データセット](concepts-datasets-linked-services.md)を受け取り、1 個以上の出力[データセット](concepts-datasets-linked-services.md)を生成できます。 次の図は、Data Factory でのパイプライン、アクティビティ、データセットの関係を示しています。

![データセット、アクティビティ、パイプラインの関係](media/concepts-pipelines-activities/relationship-between-dataset-pipeline-activity.png)

入力データセットはパイプライン内のアクティビティに対する入力を表し、出力データセットはアクティビティの出力を表します。 データセットは、テーブル、ファイル、フォルダー、ドキュメントなど、さまざまなデータ ストア内のデータを示します。 作成したデータセットは、パイプライン内のアクティビティで使用できます。 たとえば、データセットはコピー アクティビティまたは HDInsightHive アクティビティの入力/出力データセットとして使用できます。 データセットの詳細については、「[Azure Data Factory のデータセット](concepts-datasets-linked-services.md)」の記事を参照してください。

## <a name="data-movement-activities"></a>データ移動アクティビティ

Data Factory のコピー アクティビティは、ソース データ ストアからシンク データ ストアにデータをコピーします。 Data Factory は、このセクションの表に挙げられているデータ ストアをサポートしています。 また、任意のソースのデータを任意のシンクに書き込むことができます。 データ ストアをクリックすると、そのストアとの間でデータをコピーする方法がわかります。

[!INCLUDE [data-factory-v2-supported-data-stores](../../includes/data-factory-v2-supported-data-stores.md)]

詳細については、「[Copy Activity - Overview (コピー アクティビティの概要)](copy-activity-overview.md)」を参照してください。

## <a name="data-transformation-activities"></a>データ変換アクティビティ
Azure Data Factory は、次の変換アクティビティをサポートしています。これらのアクティビティは、個別または他のアクティビティと連結した状態でパイプラインに追加できます。

データ変換アクティビティ | Compute 環境
---------------------------- | -------------------
[データ フロー](control-flow-execute-data-flow-activity.md) | Azure Data Factory によって管理される Azure Databricks
[Azure 関数](control-flow-azure-function-activity.md) | Azure Functions
[Hive](transform-data-using-hadoop-hive.md) | HDInsight [Hadoop]
[Pig](transform-data-using-hadoop-pig.md) | HDInsight [Hadoop]
[MapReduce](transform-data-using-hadoop-map-reduce.md) | HDInsight [Hadoop]
[Hadoop ストリーミング](transform-data-using-hadoop-streaming.md) | HDInsight [Hadoop]
[Spark](transform-data-using-spark.md) | HDInsight [Hadoop]
[Azure Machine Learning スタジオ (クラシック) のアクティビティ: バッチ実行とリソースの更新](transform-data-using-machine-learning.md) | Azure VM
[ストアド プロシージャ](transform-data-using-stored-procedure.md) | Azure SQL、Azure Synapse Analytics、または SQL Server
[U-SQL](transform-data-using-data-lake-analytics.md) | Azure Data Lake Analytics
[カスタム アクティビティ](transform-data-using-dotnet-custom-activity.md) | Azure Batch
[Databricks Notebook](transform-data-databricks-notebook.md) | Azure Databricks
[Databricks Jar アクティビティ](transform-data-databricks-jar.md) | Azure Databricks
[Databricks Python アクティビティ](transform-data-databricks-python.md) | Azure Databricks

詳細については、[データ変換アクティビティ](transform-data.md)に関する記事を参照してください。

## <a name="control-flow-activities"></a>制御フロー アクティビティ
次の制御フロー アクティビティがサポートされています。

制御アクティビティ | 説明
---------------- | -----------
[変数の追加](control-flow-append-variable-activity.md) | 既存の配列変数に値を追加します。
[パイプラインの実行](control-flow-execute-pipeline-activity.md) | パイプラインの実行アクティビティを使用すると、Data Factory の 1 つのパイプラインから別のパイプラインを呼び出すことができます。
[Assert](control-flow-filter-activity.md) | 入力配列にフィルター式を適用します
[For Each](control-flow-for-each-activity.md) | ForEach アクティビティは、パイプライン内の繰り返し制御フローを定義します。 このアクティビティは、コレクションを反復処理するために使用され、指定されたアクティビティをループで実行します。 このアクティビティのループの実装は、プログラミング言語の Foreach ループ構造に似ています。
[メタデータの取得](control-flow-get-metadata-activity.md) | GetMetadata アクティビティを使用すると、Azure Data Factory で任意のデータのメタデータを取得できます。
[If Condition アクティビティ](control-flow-if-condition-activity.md) | If Condition は、true または false として評価される条件に基づき分岐を行うために使用できます。 If Condition アクティビティは、プログラミング言語における if ステートメントと同じ働きを持ちます。 条件が `true` に評価されたときの一連のアクティビティと `false.` に評価されたときの一連のアクティビティが評価されます
[ルックアップ アクティビティ](control-flow-lookup-activity.md) | ルックアップ アクティビティを使用して、任意の外部ソースからレコード/テーブル名/値を読み取ったり検索したりできます。 この出力は、後続のアクティビティによってさらに参照できます。
[変数の設定](control-flow-set-variable-activity.md) | 既存の変数の値を設定します。
[Until アクティビティ](control-flow-until-activity.md) | プログラミング言語の Do-Until ループ構造に似た Do-Until ループを実装します。 Until アクティビティでは、そこに関連付けられている条件が true に評価されるまで、一連のアクティビティがループ実行されます。 Data Factory では、until アクティビティのタイムアウト値を指定することができます。
[検証アクティビティ](control-flow-validation-activity.md) | 参照データセットが存在する、指定された条件を満たす、またはタイムアウトに達した場合にのみ、パイプラインが実行を継続するようにします。
[Wait アクティビティ](control-flow-wait-activity.md) | パイプラインで Wait アクティビティを使用すると、パイプラインは、指定した期間待った後、後続のアクティビティの実行を続行します。
[Web アクティビティ](control-flow-web-activity.md) | Web アクティビティを使用すると、Data Factory パイプラインからカスタム REST エンドポイントを呼び出すことができます。 このアクティビティで使用したり、アクセスしたりするデータセットやリンクされたサービスを渡すことができます。
[Webhook アクティビティ](control-flow-webhook-activity.md) | Webhook アクティビティを使用すると、エンドポイントを呼び出し、コールバック URL を渡すことができます。 パイプラインの実行は、コールバックが呼び出されるのを待ってから、次のアクティビティに進みます。

## <a name="pipeline-json"></a>パイプライン JSON
パイプラインを JSON 形式で定義する方法を示します。

```json
{
    "name": "PipelineName",
    "properties":
    {
        "description": "pipeline description",
        "activities":
        [
        ],
        "parameters": {
        },
        "concurrency": <your max pipeline concurrency>,
        "annotations": [
        ]
    }
}
```

タグ | 説明 | Type | 必須
--- | ----------- | ---- | --------
name | パイプラインの名前。 パイプラインが実行するアクションを表す名前を指定します。 <br/><ul><li>最大文字数: 140</li><li>文字、数字、アンダー スコア (\_) のいずれかで始める必要があります</li><li>次の文字は使用できません: "."、"+"、"?"、"/"、"<"、">"、"*"、"%"、" &"、":"、" \" </li></ul> | String | はい
description | パイプラインの用途を説明するテキストを指定します。 | String | いいえ
activities | **activities** セクションでは、1 つまたは複数のアクティビティを定義できます。 activities JSON 要素の詳細については、「[アクティビティ JSON](#activity-json)」のセクションを参照してください。 | Array | はい
parameters | **parameters** セクションでは、パイプライン内に 1 つ以上のパラメーターを定義できるので、パイプラインの再利用に柔軟性を持たせることができます。 | List | いいえ
concurrency | パイプラインで可能な同時実行の最大数。 既定では、最大値は設定されていません。 同時実行の制限に達した場合、前の実行が完了するまでパイプラインの実行がキューに追加されます | Number | いいえ 
annotations | パイプラインに関連付けられているタグの一覧 | Array | いいえ

## <a name="activity-json"></a>アクティビティ JSON
**activities** セクションでは、1 つまたは複数のアクティビティを定義できます。 アクティビティには、主に次の 2 種類があります:実行アクティビティと制御アクティビティ。

### <a name="execution-activities"></a>実行アクティビティ
実行アクティビティには、[データ移動アクティビティ](#data-movement-activities)と[データ変換アクティビティ](#data-transformation-activities)が含まれます。 これらのアクティビティには、次のような最上位構造があります。

```json
{
    "name": "Execution Activity Name",
    "description": "description",
    "type": "<ActivityType>",
    "typeProperties":
    {
    },
    "linkedServiceName": "MyLinkedService",
    "policy":
    {
    },
    "dependsOn":
    {
    }
}
```

アクティビティの JSON 定義内のプロパティを次の表で説明します。

タグ | 説明 | 必須
--- | ----------- | ---------
name | アクティビティの名前。 アクティビティが実行するアクションを表す名前を指定します。 <br/><ul><li>最大文字数: 55</li><li>文字、数字、またはアンダースコア (\_) で始まる必要があります</li><li>次の文字は使用できません: "."、"+"、"?"、"/"、"<"、">"、"*"、"%"、" &"、":"、" \" | はい</li></ul>
description | アクティビティの用途を説明するテキスト。 | はい
type | アクティビティの種類。 各種のアクティビティについては、[データ移動アクティビティ](#data-movement-activities)、[データ変換アクティビティ](#data-transformation-activities)、[制御アクティビティ](#control-flow-activities)に関するセクションを参照してください。 | はい
linkedServiceName | アクティビティで使用される、リンクされたサービスの名前。<br/><br/>アクティビティでは、必要なコンピューティング環境にリンクする、リンクされたサービスの指定が必要な場合があります。 | HDInsight アクティビティ、Azure Machine Learning スタジオ (クラシック) バッチ スコアリング アクティビティ、ストアド プロシージャ アクティビティの場合は "はい"。 <br/><br/>それ以外の場合は "いいえ"
typeProperties | typeProperties セクションのプロパティは、アクティビティの種類に応じて異なります。 アクティビティの typeProperties を確認するには、前のセクションでアクティビティのリンクをクリックしてください。 | いいえ
policy | アクティビティの実行時の動作に影響するポリシーです。 このプロパティには、タイムアウトと再試行の動作が含まれます。 指定されていない場合は、既定値が使用されます。 詳細については、「[アクティビティ ポリシー](#activity-policy)」のセクションを参照してください。 | いいえ
dependsOn | このプロパティを使用して、アクティビティの依存関係と、後続のアクティビティが前のアクティビティにどのように依存するかを定義します。 詳細については、「[アクティビティの依存関係](#activity-dependency)」を参照してください | いいえ

### <a name="activity-policy"></a>アクティビティ ポリシー
ポリシーはアクティビティの実行時の動作に影響します。これによって、構成機能のオプションが得られます。 アクティビティ ポリシーは実行アクティビティ専用です。

### <a name="activity-policy-json-definition"></a>アクティビティ ポリシー JSON 定義

```json
{
    "name": "MyPipelineName",
    "properties": {
      "activities": [
        {
          "name": "MyCopyBlobtoSqlActivity",
          "type": "Copy",
          "typeProperties": {
            ...
          },
         "policy": {
            "timeout": "00:10:00",
            "retry": 1,
            "retryIntervalInSeconds": 60,
            "secureOutput": true
         }
        }
      ],
        "parameters": {
           ...
        }
    }
}
```

JSON での名前 | 説明 | 使用できる値 | 必須
--------- | ----------- | -------------- | --------
timeout | アクティビティの実行に関するタイムアウトを指定します。 | Timespan | いいえ。 既定のタイムアウトは 7 日間です。
retry | 最大再試行回数 | Integer | いいえ。 既定値は 0 です
retryIntervalInSeconds | 再試行の間の遅延 (秒単位) | Integer | いいえ。 既定値は 30 秒です
secureOutput | true に設定すると、アクティビティからの出力が安全と見なされ、監視用にログが記録されません。 | Boolean | いいえ。 既定値は false です。

### <a name="control-activity"></a>制御アクティビティ
制御アクティビティには、次のような最上位構造があります。

```json
{
    "name": "Control Activity Name",
    "description": "description",
    "type": "<ActivityType>",
    "typeProperties":
    {
    },
    "dependsOn":
    {
    }
}
```

タグ | 説明 | 必須
--- | ----------- | --------
name | アクティビティの名前。 アクティビティが実行するアクションを表す名前を指定します。<br/><ul><li>最大文字数: 55</li><li>文字、数字、またはアンダースコア (\_) で始まる必要があります</li><li>次の文字は使用できません: "."、"+"、"?"、"/"、"<"、">"、"*"、"%"、" &"、":"、" \" | はい</li><ul>
description | アクティビティの用途を説明するテキスト。 | はい
type | アクティビティの種類。 各種のアクティビティについては、[データ移動アクティビティ](#data-movement-activities)、[データ変換アクティビティ](#data-transformation-activities)、[制御アクティビティ](#control-flow-activities)に関するセクションを参照してください。 | はい
typeProperties | typeProperties セクションのプロパティは、アクティビティの種類に応じて異なります。 アクティビティの typeProperties を確認するには、前のセクションでアクティビティのリンクをクリックしてください。 | いいえ
dependsOn | このプロパティを使用して、アクティビティの依存関係と、後続のアクティビティが前のアクティビティにどのように依存するかを定義します。 詳細については、「[アクティビティの依存関係](#activity-dependency)」を参照してください。 | いいえ

### <a name="activity-dependency"></a>アクティビティの依存関係
アクティビティの依存関係では、後続のアクティビティが前のアクティビティにどのように依存するかを定義するので、次のタスクの実行を続行するかどうかの条件を決めることができます。 さまざまな依存関係の条件を使用して、1 つのアクティビティを 1 つ以上の前のアクティビティに依存させることができます。

依存関係の条件には次のものがあります:Succeeded、Failed、Skipped、Completed。

たとえば、パイプラインに Activity A -> Activity B がある場合、次のようなさまざまなシナリオが考えられます。

- Activity B が Activity A に対する **succeeded** の依存関係の条件を持つ場合:Activity A の最終的な状態が succeeded の場合にのみ Activity B が実行されます
- Activity B が Activity A に対する **failed** の依存関係の条件を持つ場合:Activity A の最終的な状態が failed の場合にのみ Activity B が実行されます
- Activity B が Activity A に対する **completed** の依存関係の条件を持つ場合:Activity A の最終的な状態が succeeded か failed の場合に Activity B が実行されます
- Activity B が Activity A に対する **skipped** の依存関係の条件を持つ場合:Activity A の最終的な状態が skipped の場合に Activity B が実行されます。 Activity X -> Activity Y -> Activity Z のシナリオで、各アクティビティが前のアクティビティが成功した場合のみ実行される場合、skipped が発生します。 Activity X が失敗した場合、Activity Y が実行されることはないので、Activity Y の状態は "Skipped" になります。 同様に、Activity Z の状態も "Skipped" になります。

#### <a name="example-activity-2-depends-on-the-activity-1-succeeding"></a>例:Activity 2 は Activity 1 の成功に依存している

```json
{
    "name": "PipelineName",
    "properties":
    {
        "description": "pipeline description",
        "activities": [
         {
            "name": "MyFirstActivity",
            "type": "Copy",
            "typeProperties": {
            },
            "linkedServiceName": {
            }
        },
        {
            "name": "MySecondActivity",
            "type": "Copy",
            "typeProperties": {
            },
            "linkedServiceName": {
            },
            "dependsOn": [
            {
                "activity": "MyFirstActivity",
                "dependencyConditions": [
                    "Succeeded"
                ]
            }
          ]
        }
      ],
      "parameters": {
       }
    }
}

```

## <a name="sample-copy-pipeline"></a>コピー パイプラインのサンプル
次のサンプル パイプラインでは、**Copy** in the **アクティビティ** 型のアクティビティが 1 つあります。 このサンプルでは、[コピー アクティビティ](copy-activity-overview.md)によって、Azure BLOB Storage から Azure SQL Database 内のデータベースにデータをコピーします。

```json
{
  "name": "CopyPipeline",
  "properties": {
    "description": "Copy data from a blob to Azure SQL table",
    "activities": [
      {
        "name": "CopyFromBlobToSQL",
        "type": "Copy",
        "inputs": [
          {
            "name": "InputDataset"
          }
        ],
        "outputs": [
          {
            "name": "OutputDataset"
          }
        ],
        "typeProperties": {
          "source": {
            "type": "BlobSource"
          },
          "sink": {
            "type": "SqlSink",
            "writeBatchSize": 10000,
            "writeBatchTimeout": "60:00:00"
          }
        },
        "policy": {
          "retry": 2,
          "timeout": "01:00:00"
        }
      }
    ]
  }
}
```
以下の点に注意してください。

- activities セクションに、**type** が **Copy** に設定されたアクティビティが 1 つだけあります。
- アクティビティの入力を **InputDataset** に設定し、出力を **OutputDataset** に設定します。 JSON でのデータセットの定義の詳細については、[データセット](concepts-datasets-linked-services.md)に関する記事を参照してください。
- **typeProperties** セクションでは、ソースの種類として **BlobSource** が指定され、シンクの種類として **SqlSink** が指定されています。 データ ストアとの間でのデータの移動については、「[データ移動アクティビティ](#data-movement-activities)」セクションで、ソースまたはシンクとして使用するデータ ストアをクリックしてください。

このパイプラインの作成に関する完全なチュートリアルについては、「[Quickstart: create a data factory (クイック スタート: データ ファクトリを作成する)](quickstart-create-data-factory-powershell.md)」を参照してください。

## <a name="sample-transformation-pipeline"></a>変換パイプラインのサンプル
次のサンプル パイプラインでは、**HDInsightHive** in the **アクティビティ** 型のアクティビティが 1 つあります。 このサンプルでは、 [HDInsight Hive アクティビティ](transform-data-using-hadoop-hive.md) が、Azure HDInsight Hadoop クラスターで Hive スクリプト ファイルを実行して、Azure BLOB ストレージからデータを変換します。

```json
{
    "name": "TransformPipeline",
    "properties": {
        "description": "My first Azure Data Factory pipeline",
        "activities": [
            {
                "type": "HDInsightHive",
                "typeProperties": {
                    "scriptPath": "adfgetstarted/script/partitionweblogs.hql",
                    "scriptLinkedService": "AzureStorageLinkedService",
                    "defines": {
                        "inputtable": "wasb://adfgetstarted@<storageaccountname>.blob.core.windows.net/inputdata",
                        "partitionedtable": "wasb://adfgetstarted@<storageaccountname>.blob.core.windows.net/partitioneddata"
                    }
                },
                "inputs": [
                    {
                        "name": "AzureBlobInput"
                    }
                ],
                "outputs": [
                    {
                        "name": "AzureBlobOutput"
                    }
                ],
                "policy": {
                    "retry": 3
                },
                "name": "RunSampleHiveActivity",
                "linkedServiceName": "HDInsightOnDemandLinkedService"
            }
        ]
    }
}
```
以下の点に注意してください。

- activities セクションに、**type** が **HDInsightHive** に設定されたアクティビティが 1 つだけあります。
- Hive スクリプト ファイル **partitionweblogs.hql** は、Azure Storage アカウント (scriptLinkedService によって指定され、AzureStorageLinkedService という名前) と `adfgetstarted` コンテナーの script フォルダーに格納されます。
- `defines` セクションは、Hive 構成値 (例: $`{hiveconf:inputtable}`, `${hiveconf:partitionedtable}`) として Hive スクリプトに渡される実行時設定を指定するために使用されます。

**typeProperties** セクションは、変換アクティビティごとに異なります。 変換アクティビティでサポートされる typeProperties については、「[データ変換アクティビティ](#data-transformation-activities)」で変換アクティビティをクリックしてください。

このパイプライン作成の完全なチュートリアルについては、「[Tutorial: transform data using Spark (チュートリアル: Spark を使用してデータを変換する)](tutorial-transform-data-spark-powershell.md)」を参照してください。

## <a name="multiple-activities-in-a-pipeline"></a>パイプライン内の複数アクティビティ
前の 2 つのサンプル パプラインには 1 つのアクティビティしか含まれていません。 パイプラインに複数のアクティビティを含めることができます。 1 つのパイプラインに複数のアクティビティがあり、後続のアクティビティが前のアクティビティに依存していない場合、これらのアクティビティは並列に実行されることもあります。

[アクティビティの依存関係](#activity-dependency)を使用して、2 つのアクティビティを連鎖させることができます。アクティビティの依存関係は、後続のアクティビティが前のアクティビティにどのように依存するかを定義するので、次のタスクの実行を続行するかどうかの条件を決めることができます。 さまざまな依存関係の条件を使用して、1 つのアクティビティを 1 つ以上の前のアクティビティに依存させることができます。

## <a name="scheduling-pipelines"></a>パイプラインのスケジュール設定
パイプラインは、トリガーによってスケジュール設定されます。 さまざまな種類のトリガーがあります (実時間のスケジュールでパイプラインをトリガーできるスケジューラ トリガーや、オンデマンドでパイプラインをトリガーする手動トリガー)。 トリガーの詳細については、[パイプラインの実行とトリガー](concepts-pipeline-execution-triggers.md)に関する記事を参照してください。

トリガーにパイプライン実行を開始させるには、特定のパイプラインのパイプライン参照をトリガー定義に組み込む必要があります。 パイプラインとトリガーには n-m の関係があります。 複数のトリガーで 1 つのパイプラインを開始したり、1 つのトリガーで複数のパイプラインを開始したりできます。 トリガーを定義し終えたらそのトリガーを開始して、パイプラインのトリガーを開始させる必要があります。 トリガーの詳細については、[パイプラインの実行とトリガー](concepts-pipeline-execution-triggers.md)に関する記事を参照してください。

たとえば、スケジューラ トリガー "Trigger A" があり、それによってパイプライン "MyCopyPipeline" を開始させるとします。 次の例のようにこのトリガーを定義します。

### <a name="trigger-a-definition"></a>Trigger A の定義

```json
{
  "name": "TriggerA",
  "properties": {
    "type": "ScheduleTrigger",
    "typeProperties": {
      ...
      }
    },
    "pipeline": {
      "pipelineReference": {
        "type": "PipelineReference",
        "referenceName": "MyCopyPipeline"
      },
      "parameters": {
        "copySourceName": "FileSource"
      }
    }
  }
}
```

## <a name="next-steps"></a>次のステップ
アクティビティを使用してパイプラインを作成する詳しい手順については、次のチュートリアルを参照してください。

- [Build a pipeline with a copy activity (コピー アクティビティを含むパイプラインの作成)](quickstart-create-data-factory-powershell.md)
- [データ変換アクティビティを含むパイプラインの作成](tutorial-transform-data-spark-powershell.md)
