---
title: Azure Data Factory - JSON スクリプトのリファレンス | Microsoft Docs
description: Data Factory のエンティティ用の JSON スキーマを紹介します。
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
editor: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: shlo
robots: noindex
ms.openlocfilehash: 279a016d60ecb1bc80baf92a7fa60365145e397d
ms.sourcegitcommit: 64798b4f722623ea2bb53b374fb95e8d2b679318
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/11/2019
ms.locfileid: "67836259"
---
# <a name="azure-data-factory---json-scripting-reference"></a>Azure Data Factory - JSON スクリプトのリファレンス
> [!NOTE]
> この記事は、Data Factory のバージョン 1 に適用されます。


この記事では、Azure Data Factory のエンティティ (パイプライン、アクティビティ、データセット、リンクされたサービス) を定義するための JSON スキーマと例を紹介します。

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="pipeline"></a>パイプライン
パイプライン定義の大まかな構造を次に示します。

```json
{
  "name": "SamplePipeline",
  "properties": {
    "description": "Describe what pipeline does",
    "activities": [
    ],
    "start": "2016-07-12T00:00:00",
    "end": "2016-07-13T00:00:00"
  }
}
```

次の表では、パイプラインの JSON 定義内のプロパティを説明します。

| プロパティ | 説明 | 必須
-------- | ----------- | --------
| name | パイプラインの名前。 アクティビティまたはパイプラインで実行するように構成されているアクションを表す名前を指定します。<br/><ul><li>最大文字数: 260</li><li>文字、数字、またはアンダースコア (\_) で始まる必要があります</li><li>次の文字は使用できません："."、"+"、"?"、"/"、"<"、">"、"*"、"%"、"&"、":"、"\\"</li></ul> |はい |
| description |アクティビティまたはパイプラインの用途を説明するテキストです。 | いいえ |
| activities | 一連のアクティビティを含みます。 | はい |
| start |パイプラインの開始日時。 [ISO 形式](https://en.wikipedia.org/wiki/ISO_8601)にする必要があります。 例: 2014-10-14T16:32:41。 <br/><br/>東部標準時などの現地時間を指定できます。 たとえば、`2016-02-27T06:00:00**-05:00` は、東部標準時で午前 6 時です。<br/><br/>start プロパティと end プロパティで、パイプラインの有効期間を指定します。 出力スライスは、この有効期間にのみ生成されます。 |いいえ<br/><br/>end プロパティの値を指定する場合は、start プロパティの値も指定する必要があります。<br/><br/>パイプラインを作成するには、開始時間と終了時間の両方が空でもかまいません。 パイプラインを実行できる有効期間を設定するには、両方の値を指定する必要があります。 パイプラインの作成時に開始時間と終了時間を指定しない場合、後で Set-AzDataFactoryPipelineActivePeriod コマンドレットを使用して設定できます。 |
| end |パイプラインの終了日時。 ISO 形式で指定する必要があります。 例: 2014-10-14T17:32:41 <br/><br/>東部標準時などの現地時間を指定できます。 たとえば、`2016-02-27T06:00:00**-05:00` は、東部標準時で午前 6 時です。<br/><br/>無期限でパイプラインを実行するには、end プロパティの値として 9999-09-09 を指定します。 |いいえ <br/><br/>start プロパティの値を指定する場合は、end プロパティの値も指定する必要があります。<br/><br/>**start** プロパティの注意事項を参照してください。 |
| isPaused |true に設定すると、パイプラインは実行されません。 既定値 = false。 このプロパティを使用して有効または無効にすることができます。 |いいえ |
| pipelineMode |パイプラインの実行のスケジューリングを行うためのメソッドです。 使用可能な値: "Scheduled" (既定)、"Onetime"。<br/><br/>"Scheduled" は、パイプラインがアクティブな期間 (開始時刻と終了時刻) に応じて、指定された間隔で実行されることを意味します。 "Onetime" はパイプラインが 1回だけ実行されることを意味します。 現時点では、作成された Onetime パイプラインを変更または更新することはできません。 1 回限りの設定の詳細については、「[1 回限りのパイプライン](data-factory-create-pipelines.md#onetime-pipeline)」を参照してください。 |いいえ |
| expirationTime |パイプラインの作成後に、パイプラインが有効であり、プロビジョニングされた状態が維持される必要がある時間。 パイプラインは、アクティブ、エラー、または保留中の実行がない限り、有効期限に達すると自動的に削除されます。 |いいえ |


## <a name="activity"></a>アクティビティ
パイプライン定義におけるアクティビティ (activities 要素) の大まかな構造は次のとおりです。

```json
{
    "name": "ActivityName",
    "description": "description",
    "type": "<ActivityType>",
    "inputs":  "[]",
    "outputs":  "[]",
    "linkedServiceName": "MyLinkedService",
    "typeProperties":
    {

    },
    "policy":
    {
    },
    "scheduler":
    {
    }
}
```

アクティビティの JSON 定義内のプロパティを次の表で説明します。

| タグ | 説明 | 必須 |
| --- | --- | --- |
| name |アクティビティの名前。 アクティビティで実行するように構成されているアクションを表す名前を指定します。<br/><ul><li>最大文字数: 260</li><li>文字、数字、またはアンダースコア (\_) で始まる必要があります</li><li>次の文字は使用できません："."、"+"、"?"、"/"、"<"、">"、"*"、"%"、"&"、":"、"\\"</li></ul> |はい |
| description |アクティビティの用途を説明するテキスト。 |いいえ |
| type |アクティビティの種類を指定します。 アクティビティの種類については、「[データ ストア](#data-stores)」および「[データ変換アクティビティ](#data-transformation-activities)」をご覧ください。 |はい |
| inputs |アクティビティで使用される入力テーブル<br/><br/>`// one input table`<br/>`"inputs":  [ { "name": "inputtable1"  } ],`<br/><br/>`// two input tables` <br/>`"inputs":  [ { "name": "inputtable1"  }, { "name": "inputtable2"  } ],` |HDInsightStreaming および SqlServerStoredProcedure アクティビティの場合は "いいえ" <br/> <br/> それ以外の場合は "はい" |
| outputs |アクティビティで使用される出力テーブル。<br/><br/>`// one output table`<br/>`"outputs":  [ { "name": “outputtable1” } ],`<br/><br/>`//two output tables`<br/>`"outputs":  [ { "name": “outputtable1” }, { "name": “outputtable2” }  ],` |はい |
| linkedServiceName |アクティビティで使用される、リンクされたサービスの名前。 <br/><br/>アクティビティでは、必要なコンピューティング環境にリンクする、リンクされたサービスの指定が必要な場合があります。 |HDInsight アクティビティ、Azure Machine Learning アクティビティ、ストアド プロシージャ アクティビティの場合は "はい"。 <br/><br/>それ以外の場合は "いいえ" |
| typeProperties |TypeProperties セクションのプロパティは、アクティビティの種類によって異なります。 |いいえ |
| policy |アクティビティの実行時の動作に影響するポリシーです。 指定されていない場合は、既定のポリシーが使用されます。 |いいえ |
| scheduler |"scheduler" プロパティは、アクティビティのスケジュールを定義するために使用します。 サブプロパティは、 [データセットの availability](data-factory-create-datasets.md#dataset-availability)プロパティにあるサブプロパティと同じです。 |いいえ |

### <a name="policies"></a>ポリシー
ポリシーはアクティビティの実行時の動作に影響します。具体的には、テーブルのスライスがいつ処理されるかです。 次の表で詳細に説明します。

| プロパティ | 使用できる値 | Default value | 説明 |
| --- | --- | --- | --- |
| concurrency |整数 <br/><br/>最大値: 10 |1 |アクティビティの同時実行の数。<br/><br/>異なるスライスで実行できる並列アクティビティ実行の数を決定します。 たとえば、アクティビティが大量のデータを処理する必要がある場合、コンカレンシーの値を大きくするとデータ処理が速くなります。 |
| executionPriorityOrder |NewestFirst<br/><br/>OldestFirst |OldestFirst |処理されるデータ スライスの順序を決定します。<br/><br/>たとえば、2 個のスライス (午後 4 時と午後 5 時の実行) があり、どちらも実行が保留されているとします。 executionPriorityOrder を NewestFirst に設定すると、午後 5 時のスライスが最初に処理されます。 同様に、executionPriorityORder を OldestFIrst に設定すると、午後 4 時のスライスが処理されます。 |
| retry |整数<br/><br/>最大値は 10 |0 |スライスのデータ処理が失敗としてマークされるまでの再試行回数。 データ スライスのアクティビティの実行は、指定された再試行回数まで再試行されます。 再試行は、障害発生後にできるだけ早く行われます。 |
| timeout |TimeSpan |00:00:00 |アクティビティのタイムアウト。 例:00:10:00 (10 分のタイムアウトを示す)<br/><br/>値が指定されていない場合、または値が 0 の場合は、タイムアウトは無期限です。<br/><br/>スライスのデータ処理時間がタイムアウト値を超えた場合、処理は取り消され、システムは処理の再試行を試みます。 再試行の回数は、retry プロパティで指定します。 タイムアウトが発生すると、ステータスは TimedOut に設定されます。 |
| delay |TimeSpan |00:00:00 |スライスのデータ処理を開始する前の遅延時間を指定します。<br/><br/>データ スライスのアクティビティの実行は、予想実行時刻を Delay だけ過ぎてから開始します。<br/><br/>例:00:10:00 (10 分の遅延を示す) |
| longRetry |整数<br/><br/>最大値: 10 |1 |スライスの実行が失敗になるまでの、長い再試行の回数。<br/><br/>longRetry の試行は longRetryInterval の間隔で行われます。 再試行間隔の時間を指定する必要がある場合は、longRetry を使用します。 Retry と longRetry の両方を指定すると、各 longRetry に Retry が含まれ、最大再試行回数は Retry * longRetry になります。<br/><br/>たとえば、アクティビティ ポリシーに次のような設定があるとします。<br/>Retry: 3<br/>longRetry: 2<br/>longRetryInterval: 01:00:00<br/><br/>実行するスライスは 1 つだけ (ステータスは Waiting)、アクティビティ実行は毎回失敗するとします。 最初に 3 つの連続する試行があります。 試行するたびに、スライスの状態は Retry になります。 最初の 3 つの試行が終わると、スライスの状態は LongRetry になります。<br/><br/>1 時間 (longRetryInteval の値) が経過した後、再度 3 回連続して試行されます。 その後、スライスの状態は Failed になり、それ以上再試行は行われません。 したがって、全部で 6 回試行されます。<br/><br/>いずれかの実行が成功すると、スライスの状態は Ready になり、それ以上再試行は行われません。<br/><br/>longRetry は、依存するデータがいつ到着するかわからない場合、またはデータ処理が行われる環境全体が当てにならない場合などに使用します。 このような場合、連続して再試行しても意味がなく、時間をおくと成功することがあります。<br/><br/>注意: longRetry または longRetryInterval に大きい値を設定しないでください。 通常、大きな値は、その他のシステムの問題があることを意味します。 |
| longRetryInterval |TimeSpan |00:00:00 |長い再試行の間の遅延 |

### <a name="typeproperties-section"></a>typeProperties セクション
typeProperties セクションは、アクティビティごとに異なります。 変換アクティビティには type プロパティのみが存在します。 パイプラインの変換アクティビティを定義する JSON サンプルについては、この記事の[データ変換アクティビティ](#data-transformation-activities)に関するセクションを参照してください。

**コピー アクティビティ**の typeProperties セクションには、**source** と **sink** の 2 つのサブセクションが存在します。 データ ストアを source や sink として使用する JSON サンプルについては、この記事の「[データ ストア](#data-stores)」セクションを参照してください。

### <a name="sample-copy-pipeline"></a>コピー パイプラインのサンプル
次のサンプル パイプラインでは、 **Copy** in the **アクティビティ** 型のアクティビティが 1 つあります。 このサンプルでは、 [コピー アクティビティ](data-factory-data-movement-activities.md) が、Azure BLOB ストレージから Azure SQL データベースにデータをコピーします。

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
        "Policy": {
          "concurrency": 1,
          "executionPriorityOrder": "NewestFirst",
          "retry": 0,
          "timeout": "01:00:00"
        }
      }
    ],
    "start": "2016-07-12T00:00:00",
    "end": "2016-07-13T00:00:00"
  }
}
```

以下の点に注意してください。

* activities セクションに、**type** が **Copy** に設定されたアクティビティが 1 つだけあります。
* アクティビティの入力を **InputDataset** に設定し、出力を **OutputDataset** に設定します。
* **typeProperties** セクションでは、ソースの種類として **BlobSource** が指定され、シンクの種類として **SqlSink** が指定されています。

データ ストアを source や sink として使用する JSON サンプルについては、この記事の「[データ ストア](#data-stores)」セクションを参照してください。

このパイプラインを作成する完全なチュートリアルについては、[Blob Storage から SQL Database へのデータのコピー](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)に関するチュートリアルを参照してください。

### <a name="sample-transformation-pipeline"></a>変換パイプラインのサンプル
次のサンプル パイプラインでは、 **HDInsightHive** in the **アクティビティ** 型のアクティビティが 1 つあります。 このサンプルでは、 [HDInsight Hive アクティビティ](data-factory-hive-activity.md) が、Azure HDInsight Hadoop クラスターで Hive スクリプト ファイルを実行して、Azure BLOB ストレージからデータを変換します。

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
                    "concurrency": 1,
                    "retry": 3
                },
                "scheduler": {
                    "frequency": "Month",
                    "interval": 1
                },
                "name": "RunSampleHiveActivity",
                "linkedServiceName": "HDInsightOnDemandLinkedService"
            }
        ],
        "start": "2016-04-01T00:00:00",
        "end": "2016-04-02T00:00:00",
        "isPaused": false
    }
}
```

以下の点に注意してください。

* activities セクションに、**type** が **HDInsightHive** に設定されたアクティビティが 1 つだけあります。
* Hive スクリプト ファイル **partitionweblogs.hql** は、Azure ストレージ アカウント (scriptLinkedService によって指定され、**AzureStorageLinkedService** という名前) および **adfgetstarted** コンテナーの **script** フォルダーに格納されます。
* **defines** セクションは、Hive 構成値 (例: `${hiveconf:inputtable}`、`${hiveconf:partitionedtable}`) として Hive スクリプトに渡される実行時設定を指定するために使用されます。

パイプラインの変換アクティビティを定義する JSON サンプルについては、この記事の[データ変換アクティビティ](#data-transformation-activities)に関するセクションを参照してください。

このパイプラインを作成する完全なチュートリアルについては、「[チュートリアル: Hadoop クラスターを使用してデータを処理する最初のパイプラインを作成する](data-factory-build-your-first-pipeline.md)」を参照してください。

## <a name="linked-service"></a>リンクされたサービス
リンクされたサービスの定義の大まかな構造を次に示します。

```json
{
    "name": "<name of the linked service>",
    "properties": {
        "type": "<type of the linked service>",
        "typeProperties": {
        }
    }
}
```

アクティビティの JSON 定義内のプロパティを次の表で説明します。

| プロパティ | 説明 | 必須 |
| -------- | ----------- | -------- |
| 名前 | リンクされたサービスの名前。 | はい |
| properties - type | リンクされたサービスの種類 例: Azure Storage、Azure SQL Database。 |
| typeProperties | typeProperties セクション内の要素は、データ ストアまたはコンピューティング環境ごとに異なります。 データ ストアのリンクされたすべてのサービスについては「データ ストア」セクション、コンピューティングのリンクされたすべてのサービスについては、「[コンピューティング環境](#compute-environments)」を参照してください。 |

## <a name="dataset"></a>Dataset
Azure Data Factory のデータセットは次のように定義されます。

```json
{
    "name": "<name of dataset>",
    "properties": {
        "type": "<type of dataset: AzureBlob, AzureSql etc...>",
        "external": <boolean flag to indicate external data. only for input datasets>,
        "linkedServiceName": "<Name of the linked service that refers to a data store.>",
        "structure": [
            {
                "name": "<Name of the column>",
                "type": "<Name of the type>"
            }
        ],
        "typeProperties": {
            "<type specific property>": "<value>",
            "<type specific property 2>": "<value 2>",
        },
        "availability": {
            "frequency": "<Specifies the time unit for data slice production. Supported frequency: Minute, Hour, Day, Week, Month>",
            "interval": "<Specifies the interval within the defined frequency. For example, frequency set to 'Hour' and interval set to 1 indicates that new data slices should be produced hourly>"
        },
        "policy":
        {
        }
    }
}
```

次の表では、上記の JSON のプロパティについて説明します。

| プロパティ | 説明 | 必須 | 既定値 |
| --- | --- | --- | --- |
| 名前 | データセットの名前。 名前付け規則については、「 [Azure Data Factory - 名前付け規則](data-factory-naming-rules.md) 」を参照してください。 |はい |NA |
| type | データセットの型。 Azure Data Factory でサポートされている型のいずれかを指定します (たとえば、AzureBlob、AzureSqlTable)。 Data Factory でサポートされるデータ ストアとデータセットの種類の全一覧については、「[データ ストア](#data-stores)」セクションを参照してください。 |
| structure | データセットのスキーマ。 列やその型が含まれます。 | いいえ |NA |
| typeProperties | 選択された型に対応するプロパティ。 サポートされている型とそのプロパティについては、「[データ ストア](#data-stores)」セクションを参照してください。 |はい |NA |
| external | データセットをデータ ファクトリ パイプラインによって明示的に生成するかどうかを指定するブール型のフラグ。 |いいえ |false |
| availability | データセット生成の処理時間枠またはスライシング モデルを定義します。 データセットのスライシング モデルの詳細については、 [スケジュール設定と実行](data-factory-scheduling-and-execution.md) に関する記事を参照してください。 |はい |NA |
| policy |データセット スライスで満たさなければならない基準または条件を定義します。 <br/><br/>詳細については、「データセット ポリシー」セクションを参照してください。 |いいえ |NA |

**structure** セクションの各列には次のプロパティが含まれます。

| プロパティ | 説明 | 必須 |
| --- | --- | --- |
| 名前 |列の名前です。 |はい |
| type |列のデータ型です。  |いいえ |
| culture |型を指定するときに使用される .NET ベースのカルチャ。 .NET 型の `Datetime` または `Datetimeoffset` です。 既定値は `en-us` です。 |いいえ |
| format |型を指定するときに使用される書式指定文字列。 .NET 型の `Datetime` または `Datetimeoffset` です。 |いいえ |

次の例では、データセットに `slicetimestamp`、`projectname`、および `pageviews` の 3 つの列があり、それらの型はそれぞれ String、String、および Decimal です。

```json
structure:
[
    { "name": "slicetimestamp", "type": "String"},
    { "name": "projectname", "type": "String"},
    { "name": "pageviews", "type": "Decimal"}
]
```

次の表では、**availability** セクションで使用できるプロパティについて説明します。

| プロパティ | 説明 | 必須 | 既定値 |
| --- | --- | --- | --- |
| frequency |データセット スライス生成の時間単位を指定します。<br/><br/><b>サポートされる frequency</b>: Minute、Hour、Day、Week、Month |はい |NA |
| interval |頻度の乗数を指定します<br/><br/>"frequency x interval" により、スライスが生成される頻度が決まります。<br/><br/>データセットを時間単位でスライスする必要がある場合は、<b>frequency</b> を <b>Hour</b> に設定し、<b>interval</b> を <b>1</b> に設定します。<br/><br/><b>メモ</b>:frequency に Minute を指定する場合は、interval を 15 以上に設定することをお勧めします |はい |NA |
| style |スライスを間隔の始めまたは終わりに生成するかどうかを指定します。<ul><li>StartOfInterval</li><li>EndOfInterval</li></ul><br/><br/>frequency を Month に設定し、style を EndOfInterval に設定すると、スライスは月の最終日に生成されます。 style が StartOfInterval に設定されていると、スライスは月の最初の日に生成されます。<br/><br/>frequency を Day に設定し、style を EndOfInterval に設定すると、スライスは 1 日の最後の 1 時間に生成されます。<br/><br/>frequency を Hour に設定し、style を EndOfInterval に設定すると、スライスは時間の終わりに生成されます。 たとえば、午後 1 時 ～ 午後 2 時のスライスの場合、午後 2 時にスライスが生成されます。 |いいえ |EndOfInterval |
| anchorDateTime |データセット スライスの境界を計算するためにスケジューラによって使用される時間の絶対位置を定義します。 <br/><br/><b>メモ</b>:AnchorDateTime に frequency より細かい日付部分が含まれている場合、そのより細かい部分は無視されます。 <br/><br/>たとえば、<b>間隔</b>が<b>時間単位</b> (frequency が Hour で interval が 1) で、<b>AnchorDateTime</b> に<b>分と秒</b>が含まれる場合、AnchorDateTime の<b>分と秒</b>部分は無視されます。 |いいえ |01/01/0001 |
| offset |すべてのデータセット スライスの開始と終了がシフトされる時間帯です。 <br/><br/><b>メモ</b>:anchorDateTime と offset の両方が指定されている場合は、結果としてシフトが結合されます。 |いいえ |NA |

次の availability セクションでは、出力データセットが 1 時間ごとに生成されるか、入力データセットが 1 時間ごとに使用可能となるように指定されます。

```json
"availability":
{
    "frequency": "Hour",
    "interval": 1
}
```

データセット定義の **policy** セクションでは、データセット スライスで満たさなければならない基準または条件を定義します。

| ポリシー名 | 説明 | 適用先 | 必須 | 既定値 |
| --- | --- | --- | --- | --- |
| minimumSizeMB |**Azure BLOB** のデータが最小サイズ要件 (MB 単位) を満たすことを検証します。 |Azure BLOB |いいえ |NA |
| minimumRows |**Azure SQL データベース**または **Azure テーブル**のデータに最小行数が含まれていることを検証します。 |<ul><li>Azure SQL Database</li><li>Azure テーブル</li></ul> |いいえ |NA |

**例:**

```json
"policy":

{
    "validation":
    {
        "minimumSizeMB": 10.0
    }
}
```

データセットは Azure Data Factory で作成されている場合を除き、 **external**とマークされます。 この設定は通常、パイプライン内の最初のアクティビティの入力に適用されます (アクティビティまたはパイプラインの連鎖が使用されている場合を除く)。

| name | 説明 | 必須 | Default value |
| --- | --- | --- | --- |
| dataDelay |特定のスライスの外部データの可用性チェックを遅らせる時間。 データが 1 時間ごとに使用できる場合、外部データが利用可能であるかどうかと、対応するスライスが準備完了であるかどうかのチェックは、dataDelay を使用して延期できます。<br/><br/>これは、現在の時刻にのみ適用されます。  たとえば、現在時刻が午後 1 時 00 分で、この値が 10 分の場合、検証は午後 1 時 10 分に開始されます。<br/><br/>この設定は、過去のスライス ([スライス終了時間] + dataDelay < 現在時刻) には影響しません。過去のスライスは遅延なく処理されます。<br/><br/>23 時間 59 分を超える時間は、`day.hours:minutes:seconds` 形式で指定する必要があります。 たとえば、24 時間を指定するには、24:00:00 ではなく、1.00:00:00 を使用します。 24:00:00 を使用した場合は、24 日間 (24.00:00:00) として処理されます。 1 日と 4 時間の場合は 1:04:00:00 と指定します。 |いいえ |0 |
| retryInterval |エラーと次の再試行の間の待機時間です。 再試行に失敗した場合、次に試行されるのは retryInterval 後になります。 <br/><br/>現在時刻が午後 1 時 00 分の場合に最初の試行を開始したとします。 最初の検証チェックを完了するための時間が 1 分のとき、操作に失敗した場合、次の再試行は "1:00 + 1 分 (チェック時間) + 1 分 (再試行間隔) = 1:02 PM" になります。 <br/><br/>過去のスライスの場合、遅延はありません。 再試行は直ちに行われます。 |いいえ |00:01:00 (1 分) |
| retryTimeout |各再試行のタイムアウト。<br/><br/>このプロパティが 10 分に設定されている場合、検証を 10 分以内に完了する必要があります。 検証に 10 分より長い時間がかかった場合、再試行がタイムアウトします。<br/><br/>検証のすべての試行がタイムアウトした場合、スライスに TimedOut のマークが付きます。 |いいえ |00:10:00 (10 分) |
| maximumRetry |外部データの可用性の確認回数です。 許容される最大値は 10 です。 |いいえ |3 |


## <a name="data-stores"></a>データ ストア
「[リンクされたサービス](#linked-service)」セクションでは、リンクされたサービスのすべての種類に共通する JSON 要素について説明しました。 このセクションでは、それぞれのデータ ストアに固有の JSON 要素について詳しく説明します。

「[データセット](#dataset)」セクションでは、すべての種類のデータセットに共通する JSON 要素について説明しました。 このセクションでは、それぞれのデータ ストアに固有の JSON 要素について詳しく説明します。

「[アクティビティ](#activity)」セクションでは、すべての種類のアクティビティに共通する JSON 要素について説明しました。 このセクションでは、データ ストアをコピー アクティビティの source/sink として使用したときの、それぞれのデータ ストアに固有の JSON 要素について詳しく説明します。

リンクされたサービス、データセット、コピー アクティビティの source/sink に使用される JSON スキーマについては、目的のデータ ストアのリンクをクリックしてください。

| Category | データ ストア
|:--- |:--- |
| **Azure** |[Azure BLOB Storage](#azure-blob-storage) |
| &nbsp; |Azure Data Lake Store |
| &nbsp; |[Azure Cosmos DB](#azure-cosmos-db) |
| &nbsp; |[Azure SQL Database](#azure-sql-database) |
| &nbsp; |[Azure SQL Data Warehouse](#azure-sql-data-warehouse) |
| &nbsp; |[Azure Search](#azure-search) |
| &nbsp; |[Azure Table Storage](#azure-table-storage) |
| **データベース** |[Amazon Redshift](#amazon-redshift) |
| &nbsp; |[IBM DB2](#ibm-db2) |
| &nbsp; |[MySQL](#mysql) |
| &nbsp; |[Oracle](#oracle) |
| &nbsp; |[PostgreSQL](#postgresql) |
| &nbsp; |[SAP Business Warehouse](#sap-business-warehouse) |
| &nbsp; |[SAP HANA](#sap-hana) |
| &nbsp; |[SQL Server](#sql-server) |
| &nbsp; |[Sybase](#sybase) |
| &nbsp; |[Teradata](#teradata) |
| **NoSQL** |[Cassandra](#cassandra) |
| &nbsp; |[MongoDB](#mongodb) |
| **ファイル** |[Amazon S3](#amazon-s3) |
| &nbsp; |[ファイル システム](#file-system) |
| &nbsp; |[FTP](#ftp) |
| &nbsp; |[HDFS](#hdfs) |
| &nbsp; |[SFTP](#sftp) |
| **その他** |[HTTP](#http) |
| &nbsp; |[OData](#odata) |
| &nbsp; |[ODBC](#odbc) |
| &nbsp; |[Salesforce](#salesforce) |
| &nbsp; |Web テーブル |

## <a name="azure-blob-storage"></a>Azure Blob Storage

### <a name="linked-service"></a>リンクされたサービス
リンクされたサービスには、Azure Storage のリンクされたサービスと Azure Storage SAS のリンクされたサービスの 2 種類があります。

#### <a name="azure-storage-linked-service"></a>Azure Storage のリンクされたサービス
**アカウント キー**を使用して Azure Storage アカウントをデータ ファクトリにリンクさせるには、Azure Storage のリンクされたサービスを作成します。 Azure Storage のリンクされたサービスを定義するには、リンクされたサービスの **type** を **AzureStorage** に設定します。 そのうえで、**typeProperties** セクションに以下のプロパティを指定することができます。

| プロパティ | 説明 | 必須 |
|:--- |:--- |:--- |
| connectionString |connectionString プロパティのために Azure Storage に接続するために必要な情報を指定します。 |はい |

##### <a name="example"></a>例

```json
{
    "name": "StorageLinkedService",
    "properties": {
        "type": "AzureStorage",
        "typeProperties": {
            "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
        }
    }
}
```

#### <a name="azure-storage-sas-linked-service"></a>Azure Storage SAS のリンクされたサービス
Azure Storage SAS のリンクされたサービスを利用すると、Shared Access Signature (SAS) を使用して Azure ストレージ アカウントを Azure Data Factory にリンクできます。 これによって、Data Factory は、ストレージ内のすべてまたは特定のリソース (BLOB/コンテナー) へのアクセスが制限付きまたは期限付きになります。 Shared Access Signature を使用して Azure Storage アカウントをデータ ファクトリにリンクさせるには、Azure Storage SAS のリンクされたサービスを作成します。 Azure Storage SAS のリンクされたサービスを定義するには、リンクされたサービスの **type** を **AzureStorageSas** に設定します。 そのうえで、**typeProperties** セクションに以下のプロパティを指定することができます。

| プロパティ | 説明 | 必須 |
|:--- |:--- |:--- |
| sasUri |BLOB、コンテナー、テーブルなどの Azure Storage リソースへの Shared Access Signature URI を指定します。 |はい |

##### <a name="example"></a>例

```json
{
    "name": "StorageSasLinkedService",
    "properties": {
        "type": "AzureStorageSas",
        "typeProperties": {
            "sasUri": "<storageUri>?<sasToken>"
        }
    }
}
```

これらのリンクされたサービスの詳細については、[Azure Blob Storage コネクタ](data-factory-azure-blob-connector.md#linked-service-properties)に関するページを参照してください。

### <a name="dataset"></a>Dataset
Azure BLOB データセットを定義するには、データセットの **type** を **AzureBlob** に設定します。 そのうえで、以下の Azure BLOB 固有のプロパティを **typeProperties** セクションで指定します。

| プロパティ | 説明 | 必須 |
| --- | --- | --- |
| folderPath |BLOB ストレージのコンテナーとフォルダーのパス。 例: myblobcontainer\myblobfolder\ |はい |
| fileName |BLOB の名前です。 fileName は省略可能で、大文字と小文字を区別します。<br/><br/>fileName を指定すると、アクティビティ (コピーを含む) は特定の BLOB で動作します。<br/><br/>fileName が指定されていない場合、コピーには入力データセットの folderPath のすべての BLOB が含まれます。<br/><br/>出力データセットに fileName が指定されていない場合、生成されるファイルの名前は次の形式になります。`Data.<Guid>.txt` (例: Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt |いいえ |
| partitionedBy |partitionedBy は任意のプロパティです。 これを使用し、時系列データに動的な folderPath と fileName を指定できます。 たとえば、1 時間ごとのデータに対して folderPath をパラメーター化できます。 |いいえ |
| format | 次の種類の形式がサポートされます:**TextFormat**、**JsonFormat**、**AvroFormat**、**OrcFormat**、**ParquetFormat** です。 形式の **type** プロパティをいずれかの値に設定します。 詳細については、[Text Format](data-factory-supported-file-and-compression-formats.md#text-format)、[Json Format](data-factory-supported-file-and-compression-formats.md#json-format)、[Avro Format](data-factory-supported-file-and-compression-formats.md#avro-format)、[Orc Format](data-factory-supported-file-and-compression-formats.md#orc-format)、[Parquet Format](data-factory-supported-file-and-compression-formats.md#parquet-format) の各セクションを参照してください。 <br><br> ファイルベースのストア間で**ファイルをそのままコピー** (バイナリ コピー) する場合は、入力と出力の両方のデータセット定義で format セクションをスキップします。 |いいえ |
| compression | データの圧縮の種類とレベルを指定します。 サポートされる種類は、**GZip**、**Deflate**、**BZip2**、**ZipDeflate** です。 サポートされるレベルは、**Optimal** と **Fastest** です。 詳細については、「[Azure Data Factory のファイル形式と圧縮形式](data-factory-supported-file-and-compression-formats.md#compression-support)」を参照してください。 |いいえ |

#### <a name="example"></a>例

```json
{
    "name": "AzureBlobInput",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "AzureStorageLinkedService",
        "typeProperties": {
            "fileName": "input.log",
            "folderPath": "adfgetstarted/inputdata",
            "format": {
                "type": "TextFormat",
                "columnDelimiter": ","
            }
        },
        "availability": {
            "frequency": "Month",
            "interval": 1
        },
        "external": true,
        "policy": {}
    }
}
 ```


詳細については、[Azure BLOB コネクタ](data-factory-azure-blob-connector.md#dataset-properties)に関する記事を参照してください。

### <a name="blobsource-in-copy-activity"></a>コピー アクティビティの BlobSource
Azure Blob Storage からデータをコピーする場合は、コピー アクティビティの **source type** を **BlobSource** に設定し、**source** セクションで以下のプロパティを指定します。

| プロパティ | 説明 | 使用できる値 | 必須 |
| --- | --- | --- | --- |
| recursive |データをサブ フォルダーから再帰的に読み取るか、指定したフォルダーからのみ読み取るかを指定します。 |True (既定値)、False |いいえ |

#### <a name="example-blobsource"></a>例:**BlobSource**
```json
{
    "name": "SamplePipeline",
    "properties": {
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-01T19:00:00",
        "description": "pipeline with copy activity",
        "activities": [{
            "name": "AzureBlobtoSQL",
            "description": "Copy Activity",
            "type": "Copy",
            "inputs": [{
                "name": "AzureBlobInput"
            }],
            "outputs": [{
                "name": "AzureSqlOutput"
            }],
            "typeProperties": {
                "source": {
                    "type": "BlobSource"
                },
                "sink": {
                    "type": "SqlSink"
                }
            },
            "policy": {
                "concurrency": 1,
                "executionPriorityOrder": "OldestFirst",
                "retry": 0,
                "timeout": "01:00:00"
            }
        }]
    }
}
```
### <a name="blobsink-in-copy-activity"></a>コピー アクティビティの BlobSink
Azure Blob Storage にデータをコピーする場合は、コピー アクティビティの **sink type** を **BlobSink** に設定し、**sink** セクションで以下のプロパティを指定します。

| プロパティ | 説明 | 使用できる値 | 必須 |
| --- | --- | --- | --- |
| copyBehavior |ソースが BlobSource または FileSystem である場合のコピー動作を定義します。 |<b>PreserveHierarchy</b>: ターゲット フォルダー内でファイル階層を保持します。 ソース フォルダーに対するソース ファイルの相対パスと、ターゲット フォルダーに対するターゲット ファイルの相対パスが一致します。<br/><br/><b>FlattenHierarchy</b>: ソース フォルダーのすべてのファイルがターゲット フォルダーの最初のレベルになります。 ターゲット ファイルは、自動生成された名前になります。 <br/><br/><b>MergeFiles (既定値):</b> ソース フォルダーのすべてのファイルを 1 つのファイルにマージします。 ファイル/Blob の名前を指定した場合、マージされたファイル名は指定した名前になります。それ以外は自動生成されたファイル名になります。 |いいえ |

#### <a name="example-blobsink"></a>例:BlobSink

```json
{
    "name": "SamplePipeline",
    "properties": {
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-01T19:00:00",
        "description": "pipeline for copy activity",
        "activities": [{
            "name": "AzureSQLtoBlob",
            "description": "copy activity",
            "type": "Copy",
            "inputs": [{
                "name": "AzureSQLInput"
            }],
            "outputs": [{
                "name": "AzureBlobOutput"
            }],
            "typeProperties": {
                "source": {
                    "type": "SqlSource",
                    "SqlReaderQuery": "$$Text.Format('select * from MyTable where timestampcolumn >= \\'{0:yyyy-MM-dd HH:mm}\\' AND timestampcolumn < \\'{1:yyyy-MM-dd HH:mm}\\'', WindowStart, WindowEnd)"
                },
                "sink": {
                    "type": "BlobSink"
                }
            },
            "policy": {
                "concurrency": 1,
                "executionPriorityOrder": "OldestFirst",
                "retry": 0,
                "timeout": "01:00:00"
            }
        }]
    }
}
```

詳細については、[Azure BLOB コネクタ](data-factory-azure-blob-connector.md#copy-activity-properties)に関する記事を参照してください。

## <a name="azure-data-lake-store"></a>Azure Data Lake Store

### <a name="linked-service"></a>リンクされたサービス
Azure Data Lake Store のリンクされたサービスを定義するには、リンクされたサービスの type を **AzureDataLakeStore** に設定し、**typeProperties** セクションで以下のプロパティを指定します。

| プロパティ | 説明 | 必須 |
|:--- |:--- |:--- |
| type | type プロパティは、次のように設定する必要があります:**AzureDataLakeStore** | はい |
| dataLakeStoreUri | Azure Data Lake Store アカウントの情報を指定します。 `https://[accountname].azuredatalakestore.net/webhdfs/v1` または `adl://[accountname].azuredatalakestore.net/` という形式で指定します。 | はい |
| subscriptionId | Data Lake Store が所属する Azure サブスクリプション ID。 | シンクでは必須 |
| resourceGroupName | Data Lake Store が所属する Azure リソース グループの名前。 | シンクでは必須 |
| servicePrincipalId | アプリケーションのクライアント ID を取得します。 | はい (サービス プリンシパル認証の場合) |
| servicePrincipalKey | アプリケーションのキーを取得します。 | はい (サービス プリンシパル認証の場合) |
| tenant | アプリケーションが存在するテナントの情報 (ドメイン名またはテナント ID) を指定します。 Azure Portal の右上隅にマウスを置くことで取得できます。 | はい (サービス プリンシパル認証の場合) |
| authorization | **Data Factory エディター**で **[承認する]** をクリックし、資格情報を入力すると、自動生成された承認 URL がこのプロパティに割り当てられます。 | はい (ユーザー資格情報認証の場合)|
| sessionId | OAuth 承認セッションの OAuth セッション ID。 各セッション ID は一意であり、1 回のみ使用できます。 Data Factory エディターを使用すると、この設定が自動的に生成されます。 | はい (ユーザー資格情報認証の場合) |

#### <a name="example-using-service-principal-authentication"></a>例: サービス プリンシパル認証の使用
```json
{
    "name": "AzureDataLakeStoreLinkedService",
    "properties": {
        "type": "AzureDataLakeStore",
        "typeProperties": {
            "dataLakeStoreUri": "https://<accountname>.azuredatalakestore.net/webhdfs/v1",
            "servicePrincipalId": "<service principal id>",
            "servicePrincipalKey": "<service principal key>",
            "tenant": "<tenant info. Example: microsoft.onmicrosoft.com>"
        }
    }
}
```

#### <a name="example-using-user-credential-authentication"></a>例: ユーザー資格情報認証の使用
```json
{
    "name": "AzureDataLakeStoreLinkedService",
    "properties": {
        "type": "AzureDataLakeStore",
        "typeProperties": {
            "dataLakeStoreUri": "https://<accountname>.azuredatalakestore.net/webhdfs/v1",
            "sessionId": "<session ID>",
            "authorization": "<authorization URL>",
            "subscriptionId": "<subscription of ADLS>",
            "resourceGroupName": "<resource group of ADLS>"
        }
    }
}
```

詳細については、[Azure Data Lake Store コネクタ](data-factory-azure-datalake-connector.md#linked-service-properties)に関する記事を参照してください。

### <a name="dataset"></a>Dataset
Azure Data Lake Store データセットを定義するには、データセットの **type** を **AzureDataLakeStore** に設定し、**typeProperties** セクションで以下のプロパティを指定します。

| プロパティ | 説明 | 必須 |
|:--- |:--- |:--- |
| folderPath |Azure Data Lake Store のコンテナーとフォルダーのパス。 |はい |
| fileName |Azure Data Lake Store 内のファイルの名前。 fileName は省略可能で、大文字と小文字を区別します。 <br/><br/>fileName を指定すると、アクティビティ (コピーを含む) は特定のファイルで動作します。<br/><br/>fileName が指定されていない場合、コピーには入力データセットの folderPath のすべてのファイルが含まれます。<br/><br/>出力データセットに fileName が指定されていない場合、生成されるファイルの名前は次の形式になります。`Data.<Guid>.txt` (例: Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt |いいえ |
| partitionedBy |partitionedBy は任意のプロパティです。 これを使用し、時系列データに動的な folderPath と fileName を指定できます。 たとえば、1 時間ごとのデータに対して folderPath をパラメーター化できます。 |いいえ |
| format | 次の種類の形式がサポートされます:**TextFormat**、**JsonFormat**、**AvroFormat**、**OrcFormat**、**ParquetFormat** です。 形式の **type** プロパティをいずれかの値に設定します。 詳細については、[Text Format](data-factory-supported-file-and-compression-formats.md#text-format)、[Json Format](data-factory-supported-file-and-compression-formats.md#json-format)、[Avro Format](data-factory-supported-file-and-compression-formats.md#avro-format)、[Orc Format](data-factory-supported-file-and-compression-formats.md#orc-format)、[Parquet Format](data-factory-supported-file-and-compression-formats.md#parquet-format) の各セクションを参照してください。 <br><br> ファイルベースのストア間で**ファイルをそのままコピー** (バイナリ コピー) する場合は、入力と出力の両方のデータセット定義で format セクションをスキップします。 |いいえ |
| compression | データの圧縮の種類とレベルを指定します。 サポートされる種類は、**GZip**、**Deflate**、**BZip2**、**ZipDeflate** です。 サポートされるレベルは、**Optimal** と **Fastest** です。 詳細については、「[Azure Data Factory のファイル形式と圧縮形式](data-factory-supported-file-and-compression-formats.md#compression-support)」を参照してください。 |いいえ |

#### <a name="example"></a>例
```json
{
    "name": "AzureDataLakeStoreInput",
    "properties": {
        "type": "AzureDataLakeStore",
        "linkedServiceName": "AzureDataLakeStoreLinkedService",
        "typeProperties": {
            "folderPath": "datalake/input/",
            "fileName": "SearchLog.tsv",
            "format": {
                "type": "TextFormat",
                "rowDelimiter": "\n",
                "columnDelimiter": "\t"
            }
        },
        "external": true,
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "policy": {
            "externalData": {
                "retryInterval": "00:01:00",
                "retryTimeout": "00:10:00",
                "maximumRetry": 3
            }
        }
    }
}
```

詳細については、[Azure Data Lake Store コネクタ](data-factory-azure-datalake-connector.md#dataset-properties)に関する記事を参照してください。

### <a name="azure-data-lake-store-source-in-copy-activity"></a>コピー アクティビティの Azure Data Lake Store ソース
Azure Data Lake Store からデータをコピーする場合は、コピー アクティビティの **source type** を **AzureDataLakeStoreSource** に設定し、**source** セクションで以下のプロパティを指定します。

**AzureDataLakeStoreSource** の **typeProperties** セクションでは、次のプロパティがサポートされます。

| プロパティ | 説明 | 使用できる値 | 必須 |
| --- | --- | --- | --- |
| recursive |データをサブ フォルダーから再帰的に読み取るか、指定したフォルダーからのみ読み取るかを指定します。 |True (既定値)、False |いいえ |

#### <a name="example-azuredatalakestoresource"></a>例:AzureDataLakeStoreSource

```json
{
    "name": "SamplePipeline",
    "properties": {
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-01T19:00:00",
        "description": "pipeline for copy activity",
        "activities": [{
            "name": "AzureDakeLaketoBlob",
            "description": "copy activity",
            "type": "Copy",
            "inputs": [{
                "name": "AzureDataLakeStoreInput"
            }],
            "outputs": [{
                "name": "AzureBlobOutput"
            }],
            "typeProperties": {
                "source": {
                    "type": "AzureDataLakeStoreSource"
                },
                "sink": {
                    "type": "BlobSink"
                }
            },
            "policy": {
                "concurrency": 1,
                "executionPriorityOrder": "OldestFirst",
                "retry": 0,
                "timeout": "01:00:00"
            }
        }]
    }
}
```

詳細については、[Azure Data Lake Store コネクタ](data-factory-azure-datalake-connector.md#copy-activity-properties)に関する記事を参照してください。

### <a name="azure-data-lake-store-sink-in-copy-activity"></a>コピー アクティビティの Azure Data Lake Store シンク
Azure Data Lake Store にデータをコピーする場合は、コピー アクティビティの **sink type** を **AzureDataLakeStoreSink** に設定し、**sink** セクションで以下のプロパティを指定します。

| プロパティ | 説明 | 使用できる値 | 必須 |
| --- | --- | --- | --- |
| copyBehavior |コピー動作を指定します。 |<b>PreserveHierarchy</b>: ターゲット フォルダー内でファイル階層を保持します。 ソース フォルダーに対するソース ファイルの相対パスと、ターゲット フォルダーに対するターゲット ファイルの相対パスが一致します。<br/><br/><b>FlattenHierarchy</b>: ソース フォルダーのすべてのファイルがターゲット フォルダーの最初のレベルに作成されます。 ターゲット ファイルは、自動生成された名前で作成されます。<br/><br/><b>MergeFiles</b>: ソース フォルダーのすべてのファイルを 1 つのファイルにマージします。 ファイル/Blob の名前を指定した場合、マージされたファイル名は指定した名前になります。それ以外は自動生成されたファイル名になります。 |いいえ |

#### <a name="example-azuredatalakestoresink"></a>例:AzureDataLakeStoreSink
```json
{
    "name": "SamplePipeline",
    "properties": {
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-01T19:00:00",
        "description": "pipeline with copy activity",
        "activities": [{
            "name": "AzureBlobtoDataLake",
            "description": "Copy Activity",
            "type": "Copy",
            "inputs": [{
                "name": "AzureBlobInput"
            }],
            "outputs": [{
                "name": "AzureDataLakeStoreOutput"
            }],
            "typeProperties": {
                "source": {
                    "type": "BlobSource"
                },
                "sink": {
                    "type": "AzureDataLakeStoreSink"
                }
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "policy": {
                "concurrency": 1,
                "executionPriorityOrder": "OldestFirst",
                "retry": 0,
                "timeout": "01:00:00"
            }
        }]
    }
}
```

詳細については、[Azure Data Lake Store コネクタ](data-factory-azure-datalake-connector.md#copy-activity-properties)に関する記事を参照してください。

## <a name="azure-cosmos-db"></a>Azure Cosmos DB

### <a name="linked-service"></a>リンクされたサービス
Azure Cosmos DB のリンクされたサービスを定義するには、リンクされたサービスの **type** を **DocumentDb** に設定し、**typeProperties** セクションで以下のプロパティを指定します。

| **プロパティ** | **説明** | **必須** |
| --- | --- | --- |
| connectionString |Azure Cosmos DB データベースに接続するために必要な情報を指定します。 |はい |

#### <a name="example"></a>例

```json
{
    "name": "CosmosDBLinkedService",
    "properties": {
        "type": "DocumentDb",
        "typeProperties": {
            "connectionString": "AccountEndpoint=<EndpointUrl>;AccountKey=<AccessKey>;Database=<Database>"
        }
    }
}
```
詳細については、[Azure Cosmos DB コネクタ](data-factory-azure-documentdb-connector.md#linked-service-properties)に関する記事をご覧ください。

### <a name="dataset"></a>Dataset
Azure Cosmos DB データセットを定義するには、データセットの **type** を **DocumentDbCollection** に設定し、**typeProperties** セクションで以下のプロパティを指定します。

| **プロパティ** | **説明** | **必須** |
| --- | --- | --- |
| collectionName |Azure Cosmos DB コレクションの名前。 |はい |

#### <a name="example"></a>例

```json
{
    "name": "PersonCosmosDBTable",
    "properties": {
        "type": "DocumentDbCollection",
        "linkedServiceName": "CosmosDBLinkedService",
        "typeProperties": {
            "collectionName": "Person"
        },
        "external": true,
        "availability": {
            "frequency": "Day",
            "interval": 1
        }
    }
}
```
詳細については、[Azure Cosmos DB コネクタ](data-factory-azure-documentdb-connector.md#dataset-properties)に関する記事をご覧ください。

### <a name="azure-cosmos-db-collection-source-in-copy-activity"></a>コピー アクティビティの Azure Cosmos DB コレクション ソース
Azure Cosmos DB からデータをコピーする場合は、コピー アクティビティの **source type** を **DocumentDbCollectionSource** に設定し、**source** セクションで以下のプロパティを指定します。


| **プロパティ** | **説明** | **使用できる値** | **必須** |
| --- | --- | --- | --- |
| query |データを読み取るためのクエリを指定します。 |Azure Cosmos DB でサポートされているクエリ文字列。 <br/><br/>例: `SELECT c.BusinessEntityID, c.PersonType, c.NameStyle, c.Title, c.Name.First AS FirstName, c.Name.Last AS LastName, c.Suffix, c.EmailPromotion FROM c WHERE c.ModifiedDate > \"2009-01-01T00:00:00\"` |いいえ <br/><br/>指定されていない場合に実行される SQL ステートメント: `select <columns defined in structure> from mycollection` |
| nestingSeparator |ドキュメントが入れ子であることを示す特殊文字 |任意の文字。 <br/><br/>Azure Cosmos DB は JSON ドキュメントの NoSQL ストアであり、入れ子構造が許可されます。 Azure Data Factory を利用すると、nestingSeparator で階層を示すことができます。 上記の例では「.」です。 区切り記号により、コピー アクティビティで「Name」オブジェクトが 3 つの子要素 (First、Middle、Last) で生成されます。これはテーブル定義の「Name.First」、「Name.Middle」、「Name.Last」に基づきます。 |いいえ |

#### <a name="example"></a>例

```json
{
    "name": "DocDbToBlobPipeline",
    "properties": {
        "activities": [{
            "type": "Copy",
            "typeProperties": {
                "source": {
                    "type": "DocumentDbCollectionSource",
                    "query": "SELECT Person.Id, Person.Name.First AS FirstName, Person.Name.Middle as MiddleName, Person.Name.Last AS LastName FROM Person",
                    "nestingSeparator": "."
                },
                "sink": {
                    "type": "BlobSink",
                    "blobWriterAddHeader": true,
                    "writeBatchSize": 1000,
                    "writeBatchTimeout": "00:00:59"
                }
            },
            "inputs": [{
                "name": "PersonCosmosDBTable"
            }],
            "outputs": [{
                "name": "PersonBlobTableOut"
            }],
            "policy": {
                "concurrency": 1
            },
            "name": "CopyFromCosmosDbToBlob"
        }],
        "start": "2016-04-01T00:00:00",
        "end": "2016-04-02T00:00:00"
    }
}
```

### <a name="azure-cosmos-db-collection-sink-in-copy-activity"></a>コピー アクティビティの Azure Cosmos DB コレクション シンク
Azure Cosmos DB にデータをコピーする場合は、コピー アクティビティの **sink type** を **DocumentDbCollectionSink** に設定し、**sink** セクションで以下のプロパティを指定します。

| **プロパティ** | **説明** | **使用できる値** | **必須** |
| --- | --- | --- | --- |
| nestingSeparator |入れ子になった文書が必要であることを示すソース列名の特殊文字。 <br/><br/>上記の例の場合: 出力テーブルの `Name.First` は、Cosmos DB ドキュメントで次の JSON 構造を生成します。<br/><br/>"Name": {<br/>    "First":"John"<br/>}, |入れ子レベルの分割に使用される文字。<br/><br/>既定値は `.` (ドット) です。 |入れ子レベルの分割に使用される文字。 <br/><br/>既定値は `.` (ドット) です。 |
| writeBatchSize |Azure Cosmos DB サービスにドキュメントの作成を要求する並列要求の数。<br/><br/>このプロパティを使用して、Azure Cosmos DB との間でデータをコピーするときのパフォーマンスを微調整できます。 writeBatchSize を増やすと、Azure Cosmos DB に送信される並列要求の数が増えるため、パフォーマンスを向上させることができます。 ただし、"要求レートが大きい" というエラー メッセージをスローする可能性のあるスロットルを回避する必要があります。<br/><br/>スロットルは、ドキュメントのサイズ、ドキュメント内の語句の数、ターゲット コレクションの索引作成ポリシーなど、さまざまな要因により決定されます。コピー操作の場合、もっとよいコレクションを利用し (S3 など)、最大のスループットを得ることができます (毎秒 2,500 要求ユニット)。 |整数 |いいえ (既定値:5) |
| writeBatchTimeout |タイムアウトする前に操作の完了を待つ時間です。 |TimeSpan<br/><br/> 例:"00:30:00" (30 分)。 |いいえ |

#### <a name="example"></a>例

```json
{
    "name": "BlobToDocDbPipeline",
    "properties": {
        "activities": [{
            "type": "Copy",
            "typeProperties": {
                "source": {
                    "type": "BlobSource"
                },
                "sink": {
                    "type": "DocumentDbCollectionSink",
                    "nestingSeparator": ".",
                    "writeBatchSize": 2,
                    "writeBatchTimeout": "00:00:00"
                },
                "translator": {
                    "type": "TabularTranslator",
                    "ColumnMappings": "FirstName: Name.First, MiddleName: Name.Middle, LastName: Name.Last, BusinessEntityID: BusinessEntityID, PersonType: PersonType, NameStyle: NameStyle, Title: Title, Suffix: Suffix"
                }
            },
            "inputs": [{
                "name": "PersonBlobTableIn"
            }],
            "outputs": [{
                "name": "PersonCosmosDbTableOut"
            }],
            "policy": {
                "concurrency": 1
            },
            "name": "CopyFromBlobToCosmosDb"
        }],
        "start": "2016-04-14T00:00:00",
        "end": "2016-04-15T00:00:00"
    }
}
```

詳細については、[Azure Cosmos DB コネクタ](data-factory-azure-documentdb-connector.md#copy-activity-properties)に関する記事をご覧ください。

## <a name="azure-sql-database"></a>Azure SQL Database

### <a name="linked-service"></a>リンクされたサービス
Azure SQL Database のリンクされたサービスを定義するには、リンクされたサービスの **type** を **AzureSqlDatabase** に設定し、**typeProperties** セクションで以下のプロパティを指定します。

| プロパティ | 説明 | 必須 |
| --- | --- | --- |
| connectionString |connectionString プロパティの Azure SQL データベース インスタンスに接続するために必要な情報を指定します。 |はい |

#### <a name="example"></a>例
```json
{
    "name": "AzureSqlLinkedService",
    "properties": {
        "type": "AzureSqlDatabase",
        "typeProperties": {
            "connectionString": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;User ID=<username>@<servername>;Password=<password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
        }
    }
}
```

詳細については、[Azure SQL コネクタ](data-factory-azure-sql-connector.md#linked-service-properties)に関する記事を参照してください。

### <a name="dataset"></a>Dataset
Azure SQL Database データセットを定義するには、データセットの **type** を **AzureSqlTable** に設定し、**typeProperties** セクションで以下のプロパティを指定します。

| プロパティ | 説明 | 必須 |
| --- | --- | --- |
| tableName |リンクされたサービスが参照する Azure SQL データベース インスタンスのテーブルまたはビューの名前です。 |はい |

#### <a name="example"></a>例

```json
{
    "name": "AzureSqlInput",
    "properties": {
        "type": "AzureSqlTable",
        "linkedServiceName": "AzureSqlLinkedService",
        "typeProperties": {
            "tableName": "MyTable"
        },
        "external": true,
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "policy": {
            "externalData": {
                "retryInterval": "00:01:00",
                "retryTimeout": "00:10:00",
                "maximumRetry": 3
            }
        }
    }
}
```
詳細については、[Azure SQL コネクタ](data-factory-azure-sql-connector.md#dataset-properties)に関する記事を参照してください。

### <a name="sql-source-in-copy-activity"></a>コピー アクティビティの SQL ソース
Azure SQL Database からデータをコピーする場合は、コピー アクティビティの **source type** を **SqlSource** に設定し、**source** セクションで以下のプロパティを指定します。


| プロパティ | 説明 | 使用できる値 | 必須 |
| --- | --- | --- | --- |
| sqlReaderQuery |カスタム クエリを使用してデータを読み取ります。 |SQL クエリ文字列。 例: `select * from MyTable`. |いいえ |
| sqlReaderStoredProcedureName |ソース テーブルからデータを読み取るストアド プロシージャの名前。 |ストアド プロシージャの名前。 |いいえ |
| storedProcedureParameters |ストアド プロシージャのパラメーター。 |名前と値のペア。 パラメーターの名前とその大文字と小文字は、ストアド プロシージャのパラメーターの名前とその大文字小文字と一致する必要があります。 |いいえ |

#### <a name="example"></a>例

```json
{
    "name": "SamplePipeline",
    "properties": {
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-01T19:00:00",
        "description": "pipeline for copy activity",
        "activities": [{
            "name": "AzureSQLtoBlob",
            "description": "copy activity",
            "type": "Copy",
            "inputs": [{
                "name": "AzureSQLInput"
            }],
            "outputs": [{
                "name": "AzureBlobOutput"
            }],
            "typeProperties": {
                "source": {
                    "type": "SqlSource",
                    "SqlReaderQuery": "$$Text.Format('select * from MyTable where timestampcolumn >= \\'{0:yyyy-MM-dd HH:mm}\\' AND timestampcolumn < \\'{1:yyyy-MM-dd HH:mm}\\'', WindowStart, WindowEnd)"
                },
                "sink": {
                    "type": "BlobSink"
                }
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "policy": {
                "concurrency": 1,
                "executionPriorityOrder": "OldestFirst",
                "retry": 0,
                "timeout": "01:00:00"
            }
        }]
    }
}
```
詳細については、[Azure SQL コネクタ](data-factory-azure-sql-connector.md#copy-activity-properties)に関する記事を参照してください。

### <a name="sql-sink-in-copy-activity"></a>コピー アクティビティの SQL シンク
Azure SQL Database にデータをコピーする場合は、コピー アクティビティの **sink type** を **SqlSink** に設定し、**sink** セクションで以下のプロパティを指定します。

| プロパティ | 説明 | 使用できる値 | 必須 |
| --- | --- | --- | --- |
| writeBatchTimeout |タイムアウトする前に一括挿入操作の完了を待つ時間です。 |TimeSpan<br/><br/> 例:"00:30:00" (30 分)。 |いいえ |
| writeBatchSize |バッファー サイズが writeBatchSize に達したときに SQL テーブルにデータを挿入します。 |整数 (行数) |いいえ (既定値:10000) |
| sqlWriterCleanupScript |特定のスライスのデータを消去するコピー アクティビティのクエリを指定します。 |クエリ ステートメント。 |いいえ |
| sliceIdentifierColumnName |自動生成スライス ID を入力するためのコピー アクティビティの列名を指定します。再実行時、特定のスライスのデータを消去するときに使用されます。 |バイナリ (32) のデータ型の列の列名。 |いいえ |
| sqlWriterStoredProcedureName |対象テーブルにデータをアップサート (更新/挿入) するストアド プロシージャの名前。 |ストアド プロシージャの名前。 |いいえ |
| storedProcedureParameters |ストアド プロシージャのパラメーター。 |名前と値のペア。 パラメーターの名前とその大文字と小文字は、ストアド プロシージャのパラメーターの名前とその大文字小文字と一致する必要があります。 |いいえ |
| sqlWriterTableType |ストアド プロシージャで使用するテーブル型の名前を指定します。 コピー アクティビティでは、このテーブル型の一時テーブルでデータを移動できます。 その後、ストアド プロシージャのコードにより、コピーされたデータを既存のデータと結合できます。 |テーブルの種類の名前。 |いいえ |

#### <a name="example"></a>例

```json
{
    "name": "SamplePipeline",
    "properties": {
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-01T19:00:00",
        "description": "pipeline with copy activity",
        "activities": [{
            "name": "AzureBlobtoSQL",
            "description": "Copy Activity",
            "type": "Copy",
            "inputs": [{
                "name": "AzureBlobInput"
            }],
            "outputs": [{
                "name": "AzureSqlOutput"
            }],
            "typeProperties": {
                "source": {
                    "type": "BlobSource",
                    "blobColumnSeparators": ","
                },
                "sink": {
                    "type": "SqlSink"
                }
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "policy": {
                "concurrency": 1,
                "executionPriorityOrder": "OldestFirst",
                "retry": 0,
                "timeout": "01:00:00"
            }
        }]
    }
}
```

詳細については、[Azure SQL コネクタ](data-factory-azure-sql-connector.md#copy-activity-properties)に関する記事を参照してください。

## <a name="azure-sql-data-warehouse"></a>Azure SQL Data Warehouse

### <a name="linked-service"></a>リンクされたサービス
Azure SQL Data Warehouse のリンクされたサービスを定義するには、リンクされたサービスの **type** を **AzureSqlDW** に設定し、**typeProperties** セクションで以下のプロパティを指定します。

| プロパティ | 説明 | 必須 |
| --- | --- | --- |
| connectionString |connectionString プロパティの Azure SQL Data Warehouse インスタンスに接続するために必要な情報を指定します。 |はい |



#### <a name="example"></a>例

```json
{
    "name": "AzureSqlDWLinkedService",
    "properties": {
        "type": "AzureSqlDW",
        "typeProperties": {
            "connectionString": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;User ID=<username>@<servername>;Password=<password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
        }
    }
}
```

詳細については、[Azure SQL Data Warehouse コネクタ](data-factory-azure-sql-data-warehouse-connector.md#linked-service-properties)に関する記事を参照してください。

### <a name="dataset"></a>Dataset
Azure SQL Data Warehouse データセットを定義するには、データセットの **type** を **AzureSqlDWTable** に設定し、**typeProperties** セクションで以下のプロパティを指定します。

| プロパティ | 説明 | 必須 |
| --- | --- | --- |
| tableName |リンクされたサービスが参照する Azure SQL Data Warehouse データベースのテーブルまたはビューの名前です。 |はい |

#### <a name="example"></a>例

```json
{
    "name": "AzureSqlDWInput",
    "properties": {
    "type": "AzureSqlDWTable",
        "linkedServiceName": "AzureSqlDWLinkedService",
        "typeProperties": {
            "tableName": "MyTable"
        },
        "external": true,
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "policy": {
            "externalData": {
                "retryInterval": "00:01:00",
                "retryTimeout": "00:10:00",
                "maximumRetry": 3
            }
        }
    }
}
```

詳細については、[Azure SQL Data Warehouse コネクタ](data-factory-azure-sql-data-warehouse-connector.md#dataset-properties)に関する記事を参照してください。

### <a name="sql-dw-source-in-copy-activity"></a>コピー アクティビティの SQL DW ソース
Azure SQL Data Warehouse からデータをコピーする場合は、コピー アクティビティの **source type** を **SqlDWSource** に設定し、**source** セクションで以下のプロパティを指定します。


| プロパティ | 説明 | 使用できる値 | 必須 |
| --- | --- | --- | --- |
| sqlReaderQuery |カスタム クエリを使用してデータを読み取ります。 |SQL クエリ文字列。 (例: `select * from MyTable`)。 |いいえ |
| sqlReaderStoredProcedureName |ソース テーブルからデータを読み取るストアド プロシージャの名前。 |ストアド プロシージャの名前。 |いいえ |
| storedProcedureParameters |ストアド プロシージャのパラメーター。 |名前と値のペア。 パラメーターの名前とその大文字と小文字は、ストアド プロシージャのパラメーターの名前とその大文字小文字と一致する必要があります。 |いいえ |

#### <a name="example"></a>例

```json
{
    "name": "SamplePipeline",
    "properties": {
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-01T19:00:00",
        "description": "pipeline for copy activity",
        "activities": [{
            "name": "AzureSQLDWtoBlob",
            "description": "copy activity",
            "type": "Copy",
            "inputs": [{
                "name": "AzureSqlDWInput"
            }],
            "outputs": [{
                "name": "AzureBlobOutput"
            }],
            "typeProperties": {
                "source": {
                    "type": "SqlDWSource",
                    "sqlReaderQuery": "$$Text.Format('select * from MyTable where timestampcolumn >= \\'{0:yyyy-MM-dd HH:mm}\\' AND timestampcolumn < \\'{1:yyyy-MM-dd HH:mm}\\'', WindowStart, WindowEnd)"
                },
                "sink": {
                    "type": "BlobSink"
                }
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "policy": {
                "concurrency": 1,
                "executionPriorityOrder": "OldestFirst",
                "retry": 0,
                "timeout": "01:00:00"
            }
        }]
    }
}
```

詳細については、[Azure SQL Data Warehouse コネクタ](data-factory-azure-sql-data-warehouse-connector.md#copy-activity-properties)に関する記事を参照してください。

### <a name="sql-dw-sink-in-copy-activity"></a>コピー アクティビティの SQL DW シンク
Azure SQL Data Warehouse にデータをコピーする場合は、コピー アクティビティの **sink type** を **SqlDWSink** に設定し、**sink** セクションで以下のプロパティを指定します。

| プロパティ | 説明 | 使用できる値 | 必須 |
| --- | --- | --- | --- |
| sqlWriterCleanupScript |特定のスライスのデータを消去するコピー アクティビティのクエリを指定します。 |クエリ ステートメント。 |いいえ |
| allowPolyBase |BULKINSERT メカニズムではなく PolyBase (該当する場合) を使用するかどうかを示します。 <br/><br/> **SQL Data Warehouse へのデータ読み込みには、PolyBase の使用をお勧めします。** |True <br/>False (既定値) |いいえ |
| polyBaseSettings |**allowPolybase** プロパティが **true** に設定されているときに指定できるプロパティのグループ。 |&nbsp; |いいえ |
| rejectValue |クエリが失敗するまでに拒否できる行の数または割合を指定します。 <br/><br/>PolyBase の拒否オプションの詳細については、「 **CREATE EXTERNAL TABLE (Transact-SQL)** 」の「 [Arguments (引数)](https://msdn.microsoft.com/library/dn935021.aspx) 」をご覧ください。 |0 (既定値)、1、2、… |いいえ |
| rejectType |rejectValue オプションをリテラル値と割合のどちらで指定するかを指定します。 |Value (既定値)、Percentage |いいえ |
| rejectSampleValue |拒否された行の割合が PolyBase で再計算されるまでに取得する行数を決定します。 |1、2、… |はい (**rejectType** が **percentage** の場合) |
| useTypeDefault |PolyBase がテキスト ファイルからデータを取得する場合にどのように区切りテキスト ファイル内の不足値を処理するかを、指定します。<br/><br/>このプロパティの詳細については、[CREATE EXTERNAL FILE FORMAT (Transact-SQL)](https://msdn.microsoft.com/library/dn935026.aspx) Arguments セクションをご覧ください。 |True、False (既定値) |いいえ |
| writeBatchSize |バッファー サイズが writeBatchSize に到達したときに SQL テーブルにデータを挿入します。 |整数 (行数) |いいえ (既定値:10000) |
| writeBatchTimeout |タイムアウトする前に一括挿入操作の完了を待つ時間です。 |TimeSpan<br/><br/> 例:"00:30:00" (30 分)。 |いいえ |

#### <a name="example"></a>例

```json
{
    "name": "SamplePipeline",
    "properties": {
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-01T19:00:00",
        "description": "pipeline with copy activity",
        "activities": [{
            "name": "AzureBlobtoSQLDW",
            "description": "Copy Activity",
            "type": "Copy",
            "inputs": [{
                "name": "AzureBlobInput"
            }],
            "outputs": [{
                "name": "AzureSqlDWOutput"
            }],
            "typeProperties": {
                "source": {
                "type": "BlobSource",
                    "blobColumnSeparators": ","
                },
                "sink": {
                    "type": "SqlDWSink",
                    "allowPolyBase": true
                }
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "policy": {
                "concurrency": 1,
                "executionPriorityOrder": "OldestFirst",
                "retry": 0,
                "timeout": "01:00:00"
            }
        }]
    }
}
```

詳細については、[Azure SQL Data Warehouse コネクタ](data-factory-azure-sql-data-warehouse-connector.md#copy-activity-properties)に関する記事を参照してください。

## <a name="azure-search"></a>Azure Search

### <a name="linked-service"></a>リンクされたサービス
Azure Search のリンクされたサービスを定義するには、リンクされたサービスの **type** を **AzureSearch** に設定し、**typeProperties** セクションで以下のプロパティを指定します。

| プロパティ | 説明 | 必須 |
| -------- | ----------- | -------- |
| url | Azure Search サービスの URL。 | はい |
| key | Azure Search サービスの管理者キー。 | はい |

#### <a name="example"></a>例

```json
{
    "name": "AzureSearchLinkedService",
    "properties": {
        "type": "AzureSearch",
        "typeProperties": {
            "url": "https://<service>.search.windows.net",
            "key": "<AdminKey>"
        }
    }
}
```

詳細については、[Azure Search コネクタ](data-factory-azure-search-connector.md#linked-service-properties)に関する記事を参照してください。

### <a name="dataset"></a>Dataset
Azure Search データセットを定義するには、データセットの **type** を **AzureSearchIndex** に設定し、**typeProperties** セクションで以下のプロパティを指定します。

| プロパティ | 説明 | 必須 |
| -------- | ----------- | -------- |
| type | type プロパティを **AzureSearchIndex** に設定する必要があります。| はい |
| indexName | Azure Search インデックスの名前。 Data Factory では、インデックスは作成されません。 Azure Search にこのインデックスが存在する必要があります。 | はい |

#### <a name="example"></a>例

```json
{
    "name": "AzureSearchIndexDataset",
    "properties": {
        "type": "AzureSearchIndex",
        "linkedServiceName": "AzureSearchLinkedService",
        "typeProperties": {
            "indexName": "products"
        },
        "availability": {
            "frequency": "Minute",
            "interval": 15
        }
    }
}
```

詳細については、[Azure Search コネクタ](data-factory-azure-search-connector.md#dataset-properties)に関する記事を参照してください。

### <a name="azure-search-index-sink-in-copy-activity"></a>コピー アクティビティの Azure Search インデックス シンク
Azure Search インデックスにデータをコピーする場合は、コピー アクティビティの **sink type** を **AzureSearchIndexSink** に設定し、**sink** セクションで以下のプロパティを指定します。

| プロパティ | 説明 | 使用できる値 | 必須 |
| -------- | ----------- | -------------- | -------- |
| WriteBehavior | ドキュメントがそのインデックスに既に存在する場合に、マージするか置換するかを指定します。 | マージ (既定値)<br/>アップロード| いいえ |
| WriteBatchSize | バッファー サイズが writeBatchSize に達したときに、Azure Search インデックスにデータをアップロードします。 | 1 ～ 1,000。 既定値は 1,000 です。 | いいえ |

#### <a name="example"></a>例

```json
{
    "name": "SamplePipeline",
    "properties": {
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-01T19:00:00",
        "description": "pipeline for copy activity",
        "activities": [{
            "name": "SqlServertoAzureSearchIndex",
            "description": "copy activity",
            "type": "Copy",
            "inputs": [{
                "name": " SqlServerInput"
            }],
            "outputs": [{
                "name": "AzureSearchIndexDataset"
            }],
            "typeProperties": {
                "source": {
                    "type": "SqlSource",
                    "SqlReaderQuery": "$$Text.Format('select * from MyTable where timestampcolumn >= \\'{0:yyyy-MM-dd HH:mm}\\' AND timestampcolumn < \\'{1:yyyy-MM-dd HH:mm}\\'', WindowStart, WindowEnd)"
                },
                "sink": {
                    "type": "AzureSearchIndexSink"
                }
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "policy": {
                "concurrency": 1,
                "executionPriorityOrder": "OldestFirst",
                "retry": 0,
                "timeout": "01:00:00"
            }
        }]
    }
}
```

詳細については、[Azure Search コネクタ](data-factory-azure-search-connector.md#copy-activity-properties)に関する記事を参照してください。

## <a name="azure-table-storage"></a>Azure Table Storage

### <a name="linked-service"></a>リンクされたサービス
リンクされたサービスには、Azure Storage のリンクされたサービスと Azure Storage SAS のリンクされたサービスの 2 種類があります。

#### <a name="azure-storage-linked-service"></a>Azure Storage のリンクされたサービス
**アカウント キー**を使用して Azure Storage アカウントをデータ ファクトリにリンクさせるには、Azure Storage のリンクされたサービスを作成します。 Azure Storage のリンクされたサービスを定義するには、リンクされたサービスの **type** を **AzureStorage** に設定します。 そのうえで、**typeProperties** セクションに以下のプロパティを指定することができます。

| プロパティ | 説明 | 必須 |
|:--- |:--- |:--- |
| type |type プロパティは、次のように設定する必要があります:**AzureStorage** |はい |
| connectionString |connectionString プロパティのために Azure Storage に接続するために必要な情報を指定します。 |はい |

**例:**

```json
{
    "name": "StorageLinkedService",
    "properties": {
        "type": "AzureStorage",
        "typeProperties": {
            "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
        }
    }
}
```

#### <a name="azure-storage-sas-linked-service"></a>Azure Storage SAS のリンクされたサービス
Azure Storage SAS のリンクされたサービスを利用すると、Shared Access Signature (SAS) を使用して Azure ストレージ アカウントを Azure Data Factory にリンクできます。 これによって、Data Factory は、ストレージ内のすべてまたは特定のリソース (BLOB/コンテナー) へのアクセスが制限付きまたは期限付きになります。 Shared Access Signature を使用して Azure Storage アカウントをデータ ファクトリにリンクさせるには、Azure Storage SAS のリンクされたサービスを作成します。 Azure Storage SAS のリンクされたサービスを定義するには、リンクされたサービスの **type** を **AzureStorageSas** に設定します。 そのうえで、**typeProperties** セクションに以下のプロパティを指定することができます。

| プロパティ | 説明 | 必須 |
|:--- |:--- |:--- |
| type |type プロパティは、次のように設定する必要があります:**AzureStorageSas** |はい |
| sasUri |BLOB、コンテナー、テーブルなどの Azure Storage リソースへの Shared Access Signature URI を指定します。 |はい |

**例:**

```json
{
    "name": "StorageSasLinkedService",
    "properties": {
        "type": "AzureStorageSas",
        "typeProperties": {
            "sasUri": "<storageUri>?<sasToken>"
        }
    }
}
```

これらのリンクされたサービスの詳細については、[Azure Table Storage コネクタ](data-factory-azure-table-connector.md#linked-service-properties)に関する記事を参照してください。

### <a name="dataset"></a>Dataset
Azure Table データセットを定義するには、データセットの **type** を **AzureTable** に設定し、**typeProperties** セクションで以下のプロパティを指定します。

| プロパティ | 説明 | 必須 |
| --- | --- | --- |
| tableName |リンクされたサービスが参照する Azure テーブル データベース インスタンスのテーブルの名前です。 |はい。 azureTableSourceQuery を付けないで tableName を指定すると、テーブルのすべてのレコードがコピー先にコピーされます。 azureTableSourceQuery も指定した場合、クエリを満たすテーブルのレコードがコピー先にコピーされます。 |

#### <a name="example"></a>例

```json
{
    "name": "AzureTableInput",
    "properties": {
        "type": "AzureTable",
        "linkedServiceName": "StorageLinkedService",
        "typeProperties": {
            "tableName": "MyTable"
        },
        "external": true,
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "policy": {
            "externalData": {
                "retryInterval": "00:01:00",
                "retryTimeout": "00:10:00",
                "maximumRetry": 3
            }
        }
    }
}
```

これらのリンクされたサービスの詳細については、[Azure Table Storage コネクタ](data-factory-azure-table-connector.md#dataset-properties)に関する記事を参照してください。

### <a name="azure-table-source-in-copy-activity"></a>コピー アクティビティの Azure Table ソース
Azure Table Storage からデータをコピーする場合は、コピー アクティビティの **source type** を **AzureTableSource** に設定し、**source** セクションで以下のプロパティを指定します。

| プロパティ | 説明 | 使用できる値 | 必須 |
| --- | --- | --- | --- |
| azureTableSourceQuery |カスタム クエリを使用してデータを読み取ります。 |Azure テーブルのクエリ文字列。 次のセクションの例を参照してください。 |いいえ。 azureTableSourceQuery を付けないで tableName を指定すると、テーブルのすべてのレコードがコピー先にコピーされます。 azureTableSourceQuery も指定した場合、クエリを満たすテーブルのレコードがコピー先にコピーされます。 |
| azureTableSourceIgnoreTableNotFound |テーブルが存在しないという例外を受け入れるかどうかを示します。 |TRUE<br/>FALSE |いいえ |

#### <a name="example"></a>例

```json
{
    "name": "SamplePipeline",
    "properties": {
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-01T19:00:00",
        "description": "pipeline for copy activity",
        "activities": [{
            "name": "AzureTabletoBlob",
            "description": "copy activity",
            "type": "Copy",
            "inputs": [{
                "name": "AzureTableInput"
            }],
            "outputs": [{
                "name": "AzureBlobOutput"
            }],
            "typeProperties": {
                "source": {
                    "type": "AzureTableSource",
                    "AzureTableSourceQuery": "PartitionKey eq 'DefaultPartitionKey'"
                },
                "sink": {
                    "type": "BlobSink"
                }
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "policy": {
                "concurrency": 1,
                "executionPriorityOrder": "OldestFirst",
                "retry": 0,
                "timeout": "01:00:00"
            }
        }]
    }
}
```

これらのリンクされたサービスの詳細については、[Azure Table Storage コネクタ](data-factory-azure-table-connector.md#copy-activity-properties)に関する記事を参照してください。

### <a name="azure-table-sink-in-copy-activity"></a>コピー アクティビティの Azure Table シンク
Azure Table Storage にデータをコピーする場合は、コピー アクティビティの **sink type** を **AzureTableSink** に設定し、**sink** セクションで以下のプロパティを指定します。

| プロパティ | 説明 | 使用できる値 | 必須 |
| --- | --- | --- | --- |
| azureTableDefaultPartitionKeyValue |シンクで使用できる既定のパーティション キー値です。 |文字列の値。 |いいえ |
| azureTablePartitionKeyName |値をパーティション キーとして使用する列の名前を指定します。 指定しない場合、AzureTableDefaultPartitionKeyValue がパーティション キーとして使用されます。 |列の名前。 |いいえ |
| azureTableRowKeyName |値を行キーとして使用する列の名前を指定します。 指定しない場合、各行に GUID を使用します。 |列の名前。 |いいえ |
| azureTableInsertType |Azure テーブルにデータを挿入する方法です。<br/><br/>このプロパティは、一致するパーティションと列キーを持つ出力テーブル内の既存の行で、値を置換するか結合するかを制御します。 <br/><br/>これらの設定 (結合と置換) の機能については、「[Insert or Merge Entity (エンティティの挿入または結合)](https://msdn.microsoft.com/library/azure/hh452241.aspx)」および「[Insert or Replace Entity (エンティティの挿入または置換)](https://msdn.microsoft.com/library/azure/hh452242.aspx)」をご覧ください。 <br/><br> この設定は、テーブル レベルではなく、行レベルで適用されます。どちらのオプションでも、出力テーブル内の、入力内に存在しない行は削除されません。 |merge (既定値)<br/>replace |いいえ |
| writeBatchSize |writeBatchSize または writeBatchTimeout に達したときに、Azure テーブルにデータを挿入します。 |整数 (行数) |いいえ (既定値:10000) |
| writeBatchTimeout |writeBatchSize または writeBatchTimeout に達したときに、Azure テーブルにデータを挿入します。 |TimeSpan<br/><br/>例:"00:20:00" (20 分) |No (既定値はストレージ クライアントの既定のタイムアウト値の 90 秒) |

#### <a name="example"></a>例

```json
{
    "name": "SamplePipeline",
    "properties": {
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-01T19:00:00",
        "description": "pipeline with copy activity",
        "activities": [{
            "name": "AzureBlobtoTable",
            "description": "Copy Activity",
            "type": "Copy",
            "inputs": [{
                "name": "AzureBlobInput"
            }],
            "outputs": [{
                "name": "AzureTableOutput"
            }],
            "typeProperties": {
                "source": {
                    "type": "BlobSource"
                },
                "sink": {
                    "type": "AzureTableSink",
                    "writeBatchSize": 100,
                    "writeBatchTimeout": "01:00:00"
                }
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "policy": {
                "concurrency": 1,
                "executionPriorityOrder": "OldestFirst",
                "retry": 0,
                "timeout": "01:00:00"
            }
        }]
    }
}
```
これらのリンクされたサービスの詳細については、[Azure Table Storage コネクタ](data-factory-azure-table-connector.md#copy-activity-properties)に関する記事を参照してください。

## <a name="amazon-redshift"></a>Amazon RedShift

### <a name="linked-service"></a>リンクされたサービス
Amazon Redshift のリンクされたサービスを定義するには、リンクされたサービスの **type** を **AmazonRedshift** に設定し、**typeProperties** セクションで以下のプロパティを指定します。

| プロパティ | 説明 | 必須 |
| --- | --- | --- |
| server |Amazon Redshift サーバーの IP アドレスまたはホスト名。 |はい |
| port |Amazon Redshift サーバーがクライアント接続のリッスンに使用する TCP ポートの数。 |いいえ、既定値: 5439 |
| database |Amazon Redshift データベースの名前。 |はい |
| username |データベースへのアクセスを持つユーザーの名前。 |はい |
| password |ユーザー アカウントのパスワード。 |はい |

#### <a name="example"></a>例

```json
{
    "name": "AmazonRedshiftLinkedService",
    "properties": {
        "type": "AmazonRedshift",
        "typeProperties": {
            "server": "<Amazon Redshift host name or IP address>",
            "port": 5439,
            "database": "<database name>",
            "username": "user",
            "password": "password"
        }
    }
}
```

詳細については、Amazon Redshift コネクタに関する記事を参照してください。

### <a name="dataset"></a>Dataset
Amazon Redshift データセットを定義するには、データセットの **type** を **RelationalTable** に設定し、**typeProperties** セクションで以下のプロパティを指定します。

| プロパティ | 説明 | 必須 |
| --- | --- | --- |
| tableName |リンクされたサービスが参照する Amazon Redshift データベースのテーブルの名前です。 |いいえ (**RelationalSource** の **クエリ** が指定されている場合) |


#### <a name="example"></a>例

```json
{
    "name": "AmazonRedshiftInputDataset",
    "properties": {
        "type": "RelationalTable",
        "linkedServiceName": "AmazonRedshiftLinkedService",
        "typeProperties": {
            "tableName": "<Table name>"
        },
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": true
    }
}
```
詳細については、Amazon Redshift コネクタに関する記事を参照してください。

### <a name="relational-source-in-copy-activity"></a>コピー アクティビティのリレーショナル ソース
Amazon Redshift からデータをコピーする場合は、コピー アクティビティの **source type** を **RelationalSource** に設定し、**source** セクションで以下のプロパティを指定します。

| プロパティ | 説明 | 使用できる値 | 必須 |
| --- | --- | --- | --- |
| query |カスタム クエリを使用してデータを読み取ります。 |SQL クエリ文字列。 (例: `select * from MyTable`)。 |いいえ (**データセット**の **tableName** が指定されている場合) |

#### <a name="example"></a>例

```json
{
    "name": "CopyAmazonRedshiftToBlob",
    "properties": {
        "description": "pipeline for copy activity",
        "activities": [{
            "type": "Copy",
            "typeProperties": {
                "source": {
                    "type": "RelationalSource",
                    "query": "$$Text.Format('select * from MyTable where timestamp >= \\'{0:yyyy-MM-ddTHH:mm:ss}\\' AND timestamp < \\'{1:yyyy-MM-ddTHH:mm:ss}\\'', WindowStart, WindowEnd)"
                },
                "sink": {
                    "type": "BlobSink",
                    "writeBatchSize": 0,
                    "writeBatchTimeout": "00:00:00"
                }
            },
            "inputs": [{
                "name": "AmazonRedshiftInputDataset"
            }],
            "outputs": [{
                "name": "AzureBlobOutputDataSet"
            }],
            "policy": {
                "timeout": "01:00:00",
                "concurrency": 1
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "name": "AmazonRedshiftToBlob"
        }],
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-01T19:00:00"
    }
}
```
詳細については、Amazon Redshift コネクタに関する記事を参照してください。

## <a name="ibm-db2"></a>IBM DB2

### <a name="linked-service"></a>リンクされたサービス
IBM DB2 のリンクされたサービスを定義するには、リンクされたサービスの **type** を **OnPremisesDB2** に設定し、**typeProperties** セクションで以下のプロパティを指定します。

| プロパティ | 説明 | 必須 |
| --- | --- | --- |
| server |DB2 サーバーの名前です。 |はい |
| database |DB2 データベースの名前です。 |はい |
| schema |データベース内のスキーマの名前です。 スキーマ名は、大文字と小文字が区別されます。 |いいえ |
| authenticationType |DB2 データベースへの接続に使用される認証の種類です。 次のいずれかの値になります。Anonymous、Basic、および Windows。 |はい |
| username |Basic または Windows 認証を使用している場合は、ユーザー名を指定します。 |いいえ |
| password |ユーザー名に指定したユーザー アカウントのパスワードを指定します。 |いいえ |
| gatewayName |Data Factory サービスが、オンプレミスの DB2 データベースへの接続に使用するゲートウェイの名前です。 |はい |

#### <a name="example"></a>例
```json
{
    "name": "OnPremDb2LinkedService",
    "properties": {
        "type": "OnPremisesDb2",
        "typeProperties": {
            "server": "<server>",
            "database": "<database>",
            "schema": "<schema>",
            "authenticationType": "<authentication type>",
            "username": "<username>",
            "password": "<password>",
            "gatewayName": "<gatewayName>"
        }
    }
}
```
詳細については、IBM DB2 コネクタに関する記事を参照してください。

### <a name="dataset"></a>Dataset
DB2 データセットを定義するには、データセットの **type** を **RelationalTable** に設定し、**typeProperties** セクションで以下のプロパティを指定します。

| プロパティ | 説明 | 必須 |
| --- | --- | --- |
| tableName |リンクされたサービスが参照する DB2 データベース インスタンスのテーブルの名前です。 tableName は、大文字と小文字が区別されます。 |いいえ (**RelationalSource** の **クエリ** が指定されている場合)

#### <a name="example"></a>例
```json
{
    "name": "Db2DataSet",
    "properties": {
        "type": "RelationalTable",
        "linkedServiceName": "OnPremDb2LinkedService",
        "typeProperties": {},
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": true,
        "policy": {
            "externalData": {
                "retryInterval": "00:01:00",
                "retryTimeout": "00:10:00",
                "maximumRetry": 3
            }
        }
    }
}
```

詳細については、IBM DB2 コネクタに関する記事を参照してください。

### <a name="relational-source-in-copy-activity"></a>コピー アクティビティのリレーショナル ソース
IBM DB2 からデータをコピーする場合は、コピー アクティビティの **source type** を **RelationalSource** に設定し、**source** セクションで以下のプロパティを指定します。


| プロパティ | 説明 | 使用できる値 | 必須 |
| --- | --- | --- | --- |
| query |カスタム クエリを使用してデータを読み取ります。 |SQL クエリ文字列。 (例: `"query": "select * from "MySchema"."MyTable""`)。 |いいえ (**データセット**の **tableName** が指定されている場合) |

#### <a name="example"></a>例
```json
{
    "name": "CopyDb2ToBlob",
    "properties": {
        "description": "pipeline for copy activity",
        "activities": [{
            "type": "Copy",
            "typeProperties": {
                "source": {
                    "type": "RelationalSource",
                    "query": "select * from \"Orders\""
                },
                "sink": {
                    "type": "BlobSink"
                }
            },
            "inputs": [{
                "name": "Db2DataSet"
            }],
            "outputs": [{
                "name": "AzureBlobDb2DataSet"
            }],
            "policy": {
                "timeout": "01:00:00",
                "concurrency": 1
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "name": "Db2ToBlob"
        }],
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-01T19:00:00"
    }
}
```
詳細については、IBM DB2 コネクタに関する記事を参照してください。

## <a name="mysql"></a>MySQL

### <a name="linked-service"></a>リンクされたサービス
MySQL のリンクされたサービスを定義するには、リンクされたサービスの **type** を **OnPremisesMySql** に設定し、**typeProperties** セクションで以下のプロパティを指定します。

| プロパティ | 説明 | 必須 |
| --- | --- | --- |
| server |MySQL サーバーの名前です。 |はい |
| database |MySQL データベースの名前です。 |はい |
| schema |データベース内のスキーマの名前です。 |いいえ |
| authenticationType |MySQL データベースへの接続に使用される認証の種類です。 次のいずれかの値になります。`Basic` |はい |
| userName |MySQL データベースに接続するユーザー名を指定します。 |はい |
| password |指定したユーザー アカウントのパスワードを指定します。 |はい |
| gatewayName |Data Factory サービスが、オンプレミスの MySQL データベースへの接続に使用するゲートウェイの名前です。 |はい |

#### <a name="example"></a>例

```json
{
    "name": "OnPremMySqlLinkedService",
    "properties": {
        "type": "OnPremisesMySql",
        "typeProperties": {
            "server": "<server name>",
            "database": "<database name>",
            "schema": "<schema name>",
            "authenticationType": "<authentication type>",
            "userName": "<user name>",
            "password": "<password>",
            "gatewayName": "<gateway>"
        }
    }
}
```

詳細については、[MySQL コネクタ](data-factory-onprem-mysql-connector.md#linked-service-properties)に関する記事を参照してください。

### <a name="dataset"></a>Dataset
MySQL データセットを定義するには、データセットの **type** を **RelationalTable** に設定し、**typeProperties** セクションで以下のプロパティを指定します。

| プロパティ | 説明 | 必須 |
| --- | --- | --- |
| tableName |リンクされたサービスが参照する MySQL Databases インスタンスのテーブルの名前です。 |いいえ (**RelationalSource** の **クエリ** が指定されている場合) |

#### <a name="example"></a>例

```json
{
    "name": "MySqlDataSet",
    "properties": {
        "type": "RelationalTable",
        "linkedServiceName": "OnPremMySqlLinkedService",
        "typeProperties": {},
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": true,
        "policy": {
            "externalData": {
                "retryInterval": "00:01:00",
                "retryTimeout": "00:10:00",
                "maximumRetry": 3
            }
        }
    }
}
```
詳細については、[MySQL コネクタ](data-factory-onprem-mysql-connector.md#dataset-properties)に関する記事を参照してください。

### <a name="relational-source-in-copy-activity"></a>コピー アクティビティのリレーショナル ソース
MySQL データベースからデータをコピーする場合は、コピー アクティビティの **source type** を **RelationalSource** に設定し、**source** セクションで以下のプロパティを指定します。


| プロパティ | 説明 | 使用できる値 | 必須 |
| --- | --- | --- | --- |
| query |カスタム クエリを使用してデータを読み取ります。 |SQL クエリ文字列。 (例: `select * from MyTable`)。 |いいえ (**データセット**の **tableName** が指定されている場合) |


#### <a name="example"></a>例
```json
{
    "name": "CopyMySqlToBlob",
    "properties": {
        "description": "pipeline for copy activity",
        "activities": [{
            "type": "Copy",
            "typeProperties": {
                "source": {
                    "type": "RelationalSource",
                    "query": "$$Text.Format('select * from MyTable where timestamp >= \\'{0:yyyy-MM-ddTHH:mm:ss}\\' AND timestamp < \\'{1:yyyy-MM-ddTHH:mm:ss}\\'', WindowStart, WindowEnd)"
                },
                "sink": {
                    "type": "BlobSink",
                    "writeBatchSize": 0,
                    "writeBatchTimeout": "00:00:00"
                }
            },
            "inputs": [{
                "name": "MySqlDataSet"
            }],
            "outputs": [{
                "name": "AzureBlobMySqlDataSet"
            }],
            "policy": {
                "timeout": "01:00:00",
                "concurrency": 1
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "name": "MySqlToBlob"
        }],
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-01T19:00:00"
    }
}
```

詳細については、[MySQL コネクタ](data-factory-onprem-mysql-connector.md#copy-activity-properties)に関する記事を参照してください。

## <a name="oracle"></a>Oracle

### <a name="linked-service"></a>リンクされたサービス
Oracle のリンクされたサービスを定義するには、リンクされたサービスの **type** を **OnPremisesOracle** に設定し、**typeProperties** セクションで以下のプロパティを指定します。

| プロパティ | 説明 | 必須 |
| --- | --- | --- |
| driverType | Oracle Database との間でデータをコピーするときに使用するドライバーを指定します。 使用できる値は **Microsoft** または **ODP** (既定値) です。 ドライバーの詳細については、「サポートされているバージョンとインストール」セクションを参照してください。 | いいえ |
| connectionString | connectionString プロパティの Oracle Database インスタンスに接続するために必要な情報を指定します。 | はい |
| gatewayName | オンプレミスの Oracle サーバーへの接続に使用されるゲートウェイの名前です |はい |

#### <a name="example"></a>例
```json
{
    "name": "OnPremisesOracleLinkedService",
    "properties": {
        "type": "OnPremisesOracle",
        "typeProperties": {
            "driverType": "Microsoft",
            "connectionString": "Host=<host>;Port=<port>;Sid=<sid>;User Id=<username>;Password=<password>;",
            "gatewayName": "<gateway name>"
        }
    }
}
```

詳細については、[Oracle コネクタ](data-factory-onprem-oracle-connector.md#linked-service-properties)に関する記事を参照してください。

### <a name="dataset"></a>Dataset
Oracle データセットを定義するには、データセットの **type** を **OracleTable** に設定し、**typeProperties** セクションで以下のプロパティを指定します。

| プロパティ | 説明 | 必須 |
| --- | --- | --- |
| tableName |リンクされたサービスが参照する Oracle データベース インスタンスのテーブルの名前です。 |いいえ (**OracleSource** の **oracleReaderQuery** が指定されている場合) |

#### <a name="example"></a>例

```json
{
    "name": "OracleInput",
    "properties": {
        "type": "OracleTable",
        "linkedServiceName": "OnPremisesOracleLinkedService",
        "typeProperties": {
            "tableName": "MyTable"
        },
        "external": true,
        "availability": {
            "offset": "01:00:00",
            "interval": "1",
            "anchorDateTime": "2016-02-27T12:00:00",
            "frequency": "Hour"
        },
        "policy": {
            "externalData": {
                "retryInterval": "00:01:00",
                "retryTimeout": "00:10:00",
                "maximumRetry": 3
            }
        }
    }
}
```
詳細については、[Oracle コネクタ](data-factory-onprem-oracle-connector.md#dataset-properties)に関する記事を参照してください。

### <a name="oracle-source-in-copy-activity"></a>コピー アクティビティの Oracle ソース
Oracle データベースからデータをコピーする場合は、コピー アクティビティの **source type** を **OracleSource** に設定し、**source** セクションで以下のプロパティを指定します。

| プロパティ | 説明 | 使用できる値 | 必須 |
| --- | --- | --- | --- |
| oracleReaderQuery |カスタム クエリを使用してデータを読み取ります。 |SQL クエリ文字列。 次に例を示します。`select * from MyTable` <br/><br/>指定されていない場合に実行される SQL ステートメント: `select * from MyTable` |いいえ (**データセット**の **tableName** が指定されている場合) |

#### <a name="example"></a>例

```json
{
    "name": "SamplePipeline",
    "properties": {
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-01T19:00:00",
        "description": "pipeline for copy activity",
        "activities": [{
            "name": "OracletoBlob",
            "description": "copy activity",
            "type": "Copy",
            "inputs": [{
                "name": " OracleInput"
            }],
            "outputs": [{
                "name": "AzureBlobOutput"
            }],
            "typeProperties": {
                "source": {
                    "type": "OracleSource",
                    "oracleReaderQuery": "$$Text.Format('select * from MyTable where timestampcolumn >= \\'{0:yyyy-MM-dd HH:mm}\\' AND timestampcolumn < \\'{1:yyyy-MM-dd HH:mm}\\'', WindowStart, WindowEnd)"
                },
                "sink": {
                    "type": "BlobSink"
                }
            },
            "scheduler": {
            "frequency": "Hour",
                "interval": 1
            },
            "policy": {
                "concurrency": 1,
                "executionPriorityOrder": "OldestFirst",
                "retry": 0,
                "timeout": "01:00:00"
            }
        }]
    }
}
```

詳細については、[Oracle コネクタ](data-factory-onprem-oracle-connector.md#copy-activity-properties)に関する記事を参照してください。

### <a name="oracle-sink-in-copy-activity"></a>コピー アクティビティの Oracle シンク
Oracle データベースにデータをコピーする場合は、コピー アクティビティの **sink type** を **OracleSink** に設定し、**sink** セクションで以下のプロパティを指定します。

| プロパティ | 説明 | 使用できる値 | 必須 |
| --- | --- | --- | --- |
| writeBatchTimeout |タイムアウトする前に一括挿入操作の完了を待つ時間です。 |TimeSpan<br/><br/> 例:00:30:00 (30 分) |いいえ |
| writeBatchSize |バッファー サイズが writeBatchSize に達したときに SQL テーブルにデータを挿入します。 |整数 (行数) |いいえ (既定値: 100) |
| sqlWriterCleanupScript |特定のスライスのデータを消去するコピー アクティビティのクエリを指定します。 |クエリ ステートメント。 |いいえ |
| sliceIdentifierColumnName |自動生成スライス ID を入力するためのコピー アクティビティの列名を指定します。再実行時、特定のスライスのデータを消去するときに使用されます。 |バイナリ (32) のデータ型の列の列名。 |いいえ |

#### <a name="example"></a>例
```json
{
    "name": "SamplePipeline",
    "properties": {
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-05T19:00:00",
        "description": "pipeline with copy activity",
        "activities": [{
            "name": "AzureBlobtoOracle",
            "description": "Copy Activity",
            "type": "Copy",
            "inputs": [{
                "name": "AzureBlobInput"
            }],
            "outputs": [{
                "name": "OracleOutput"
            }],
            "typeProperties": {
                "source": {
                    "type": "BlobSource"
                },
                "sink": {
                    "type": "OracleSink"
                }
            },
            "scheduler": {
                "frequency": "Day",
                "interval": 1
            },
            "policy": {
                "concurrency": 1,
                "executionPriorityOrder": "OldestFirst",
                "retry": 0,
                "timeout": "01:00:00"
            }
        }]
    }
}
```
詳細については、[Oracle コネクタ](data-factory-onprem-oracle-connector.md#copy-activity-properties)に関する記事を参照してください。

## <a name="postgresql"></a>PostgreSQL

### <a name="linked-service"></a>リンクされたサービス
PostgreSQL のリンクされたサービスを定義するには、リンクされたサービスの **type** を **OnPremisesPostgreSql** に設定し、**typeProperties** セクションで以下のプロパティを指定します。

| プロパティ | 説明 | 必須 |
| --- | --- | --- |
| server |PostgreSQL サーバーの名前です。 |はい |
| database |PostgreSQL データベースの名前です。 |はい |
| schema |データベース内のスキーマの名前です。 スキーマ名は、大文字と小文字が区別されます。 |いいえ |
| authenticationType |PostgreSQL データベースへの接続に使用される認証の種類です。 次のいずれかの値になります。Anonymous、Basic、および Windows。 |はい |
| username |Basic または Windows 認証を使用している場合は、ユーザー名を指定します。 |いいえ |
| password |ユーザー名に指定したユーザー アカウントのパスワードを指定します。 |いいえ |
| gatewayName |Data Factory サービスが、オンプレミスの PostgreSQL データベースへの接続に使用するゲートウェイの名前です。 |はい |

#### <a name="example"></a>例

```json
{
    "name": "OnPremPostgreSqlLinkedService",
    "properties": {
        "type": "OnPremisesPostgreSql",
        "typeProperties": {
            "server": "<server>",
            "database": "<database>",
            "schema": "<schema>",
            "authenticationType": "<authentication type>",
            "username": "<username>",
            "password": "<password>",
            "gatewayName": "<gatewayName>"
        }
    }
}
```
詳細については、[PostgreSQL コネクタ](data-factory-onprem-postgresql-connector.md#linked-service-properties)に関する記事を参照してください。

### <a name="dataset"></a>Dataset
PostgreSQL データセットを定義するには、データセットの **type** を **RelationalTable** に設定し、**typeProperties** セクションで以下のプロパティを指定します。

| プロパティ | 説明 | 必須 |
| --- | --- | --- |
| tableName |リンクされたサービスが参照する PostgreSQL Databases インスタンスのテーブルの名前です。 tableName は、大文字と小文字が区別されます。 |いいえ (**RelationalSource** の **クエリ** が指定されている場合) |

#### <a name="example"></a>例
```json
{
    "name": "PostgreSqlDataSet",
    "properties": {
        "type": "RelationalTable",
        "linkedServiceName": "OnPremPostgreSqlLinkedService",
        "typeProperties": {},
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": true,
        "policy": {
            "externalData": {
                "retryInterval": "00:01:00",
                "retryTimeout": "00:10:00",
                "maximumRetry": 3
            }
        }
    }
}
```
詳細については、[PostgreSQL コネクタ](data-factory-onprem-postgresql-connector.md#dataset-properties)に関する記事を参照してください。

### <a name="relational-source-in-copy-activity"></a>コピー アクティビティのリレーショナル ソース
PostgreSQL データベースからデータをコピーする場合は、コピー アクティビティの **source type** を **RelationalSource** に設定し、**source** セクションで以下のプロパティを指定します。


| プロパティ | 説明 | 使用できる値 | 必須 |
| --- | --- | --- | --- |
| query |カスタム クエリを使用してデータを読み取ります。 |SQL クエリ文字列。 例: "query": "select * from \"MySchema\".\"MyTable\"" |いいえ (**データセット**の **tableName** が指定されている場合) |

#### <a name="example"></a>例

```json
{
    "name": "CopyPostgreSqlToBlob",
    "properties": {
        "description": "pipeline for copy activity",
        "activities": [{
            "type": "Copy",
            "typeProperties": {
                "source": {
                    "type": "RelationalSource",
                    "query": "select * from \"public\".\"usstates\""
                },
                "sink": {
                    "type": "BlobSink"
                }
            },
            "inputs": [{
                "name": "PostgreSqlDataSet"
            }],
            "outputs": [{
                "name": "AzureBlobPostgreSqlDataSet"
            }],
            "policy": {
                "timeout": "01:00:00",
                "concurrency": 1
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "name": "PostgreSqlToBlob"
        }],
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-01T19:00:00"
    }
}
```

詳細については、[PostgreSQL コネクタ](data-factory-onprem-postgresql-connector.md#copy-activity-properties)に関する記事を参照してください。

## <a name="sap-business-warehouse"></a>SAP Business Warehouse


### <a name="linked-service"></a>リンクされたサービス
SAP Business Warehouse (BW) のリンクされたサービスを定義するには、リンクされたサービスの **type** を **SapBw** に設定し、**typeProperties** セクションで以下のプロパティを指定します。

プロパティ | 説明 | 使用できる値 | 必須
-------- | ----------- | -------------- | --------
server | SAP BW インスタンスが存在するサーバーの名前。 | string | はい
systemNumber | SAP BW システムのシステムの数。 | 2 桁の 10 進数の文字列として表されます。 | はい
clientId | SAP BW システム内のクライアントのクライアント ID。 | 3 桁の 10 進数の文字列として表されます。 | はい
username | SAP サーバーにアクセスするユーザーの名前 | string | はい
password | ユーザーのパスワード。 | string | はい
gatewayName | Data Factory サービスが、オンプレミスの SAP BW インスタンスへの接続に使用するゲートウェイの名前。 | string | はい
encryptedCredential | 暗号化された資格情報の文字列。 | string | いいえ

#### <a name="example"></a>例

```json
{
    "name": "SapBwLinkedService",
    "properties": {
        "type": "SapBw",
        "typeProperties": {
            "server": "<server name>",
            "systemNumber": "<system number>",
            "clientId": "<client id>",
            "username": "<SAP user>",
            "password": "<Password for SAP user>",
            "gatewayName": "<gateway name>"
        }
    }
}
```

詳細については、[SAP Business Warehouse コネクタ](data-factory-sap-business-warehouse-connector.md#linked-service-properties)に関する記事を参照してください。

### <a name="dataset"></a>Dataset
SAP BW データセットを定義するには、データセットの **type** を **RelationalTable** に設定します。 **RelationalTable** 型の SAP BW データセットに対して、サポートされている型固有のプロパティはありません。

#### <a name="example"></a>例

```json
{
    "name": "SapBwDataset",
    "properties": {
        "type": "RelationalTable",
        "linkedServiceName": "SapBwLinkedService",
        "typeProperties": {},
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": true
    }
}
```
詳細については、[SAP Business Warehouse コネクタ](data-factory-sap-business-warehouse-connector.md#dataset-properties)に関する記事を参照してください。

### <a name="relational-source-in-copy-activity"></a>コピー アクティビティのリレーショナル ソース
SAP Business Warehouse からデータをコピーする場合は、コピー アクティビティの **source type** を **RelationalSource** に設定し、**source** セクションで以下のプロパティを指定します。


| プロパティ | 説明 | 使用できる値 | 必須 |
| --- | --- | --- | --- |
| query | SAP BW インスタンスからデータを読み取る MDX クエリを指定します。 | MDX クエリ。 | はい |

#### <a name="example"></a>例

```json
{
    "name": "CopySapBwToBlob",
    "properties": {
        "description": "pipeline for copy activity",
        "activities": [{
            "type": "Copy",
            "typeProperties": {
                "source": {
                    "type": "RelationalSource",
                    "query": "<MDX query for SAP BW>"
                },
                "sink": {
                    "type": "BlobSink",
                    "writeBatchSize": 0,
                    "writeBatchTimeout": "00:00:00"
                }
            },
            "inputs": [{
                "name": "SapBwDataset"
            }],
            "outputs": [{
                "name": "AzureBlobDataSet"
            }],
            "policy": {
                "timeout": "01:00:00",
                "concurrency": 1
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "name": "SapBwToBlob"
        }],
        "start": "2017-03-01T18:00:00",
        "end": "2017-03-01T19:00:00"
    }
}
```

詳細については、[SAP Business Warehouse コネクタ](data-factory-sap-business-warehouse-connector.md#copy-activity-properties)に関する記事を参照してください。

## <a name="sap-hana"></a>SAP HANA

### <a name="linked-service"></a>リンクされたサービス
SAP HANA のリンクされたサービスを定義するには、リンクされたサービスの **type** を **SapHana** に設定し、**typeProperties** セクションで以下のプロパティを指定します。

プロパティ | 説明 | 使用できる値 | 必須
-------- | ----------- | -------------- | --------
server | SAP HANA インスタンスが存在するサーバーの名前。 カスタマイズされたポートをサーバーが使用している場合は、`server:port` を指定します。 | string | はい
authenticationType | 認証の種類。 | string。 "Basic" または"Windows" | はい
username | SAP サーバーにアクセスするユーザーの名前 | string | はい
password | ユーザーのパスワード。 | string | はい
gatewayName | Data Factory サービスが、オンプレミスの SAP HANA インスタンスへの接続に使用するゲートウェイの名前。 | string | はい
encryptedCredential | 暗号化された資格情報の文字列。 | string | いいえ

#### <a name="example"></a>例

```json
{
    "name": "SapHanaLinkedService",
    "properties": {
        "type": "SapHana",
        "typeProperties": {
            "server": "<server name>",
            "authenticationType": "<Basic, or Windows>",
            "username": "<SAP user>",
            "password": "<Password for SAP user>",
            "gatewayName": "<gateway name>"
        }
    }
}

```
詳細については、[SAP HANA コネクタ](data-factory-sap-hana-connector.md#linked-service-properties)に関する記事を参照してください。

### <a name="dataset"></a>Dataset
SAP HANA データセットを定義するには、データセットの **type** を **RelationalTable** に設定します。 **RelationalTable** 型の SAP HANA データセットに対して、サポートされている型固有のプロパティはありません。

#### <a name="example"></a>例

```json
{
    "name": "SapHanaDataset",
    "properties": {
        "type": "RelationalTable",
        "linkedServiceName": "SapHanaLinkedService",
        "typeProperties": {},
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": true
    }
}
```
詳細については、[SAP HANA コネクタ](data-factory-sap-hana-connector.md#dataset-properties)に関する記事を参照してください。

### <a name="relational-source-in-copy-activity"></a>コピー アクティビティのリレーショナル ソース
SAP HANA データ ストアからデータをコピーする場合は、コピー アクティビティの **source type** を **RelationalSource** に設定し、**source** セクションで以下のプロパティを指定します。

| プロパティ | 説明 | 使用できる値 | 必須 |
| --- | --- | --- | --- |
| query | SAP HANA インスタンスからデータを読み取る SQL クエリを指定します。 | SQL クエリ。 | はい |


#### <a name="example"></a>例


```json
{
    "name": "CopySapHanaToBlob",
    "properties": {
        "description": "pipeline for copy activity",
        "activities": [{
            "type": "Copy",
            "typeProperties": {
                "source": {
                    "type": "RelationalSource",
                    "query": "<SQL Query for HANA>"
                },
                "sink": {
                    "type": "BlobSink",
                    "writeBatchSize": 0,
                    "writeBatchTimeout": "00:00:00"
                }
            },
            "inputs": [{
                "name": "SapHanaDataset"
            }],
            "outputs": [{
                "name": "AzureBlobDataSet"
            }],
            "policy": {
                "timeout": "01:00:00",
                "concurrency": 1
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "name": "SapHanaToBlob"
        }],
        "start": "2017-03-01T18:00:00",
        "end": "2017-03-01T19:00:00"
    }
}
```

詳細については、[SAP HANA コネクタ](data-factory-sap-hana-connector.md#copy-activity-properties)に関する記事を参照してください。


## <a name="sql-server"></a>SQL Server

### <a name="linked-service"></a>リンクされたサービス
**OnPremisesSqlServer** 型のリンクされたサービスを使用し、オンプレミスの SQL Server データベースをデータ ファクトリにリンクします。 次の表は、オンプレミスの SQL Server のリンクされたサービスに固有の JSON 要素の説明をまとめたものです。

次の表は、SQL Server のリンクされたサービスに固有の JSON 要素の説明をまとめたものです。

| プロパティ | 説明 | 必須 |
| --- | --- | --- |
| type |type プロパティは次の値に設定されます。**OnPremisesSqlServer** |はい |
| connectionString |SQL 認証または Windows 認証を使用して、オンプレミス SQL Server データベースに接続するために必要な connectionString 情報を指定します。 |はい |
| gatewayName |Data Factory サービスが、オンプレミスの SQL Server データベースへの接続に使用するゲートウェイの名前です。 |はい |
| username |Windows 認証を使用している場合は、ユーザー名を指定します。 例: **domainname\\username**。 |いいえ |
| password |ユーザー名に指定したユーザー アカウントのパスワードを指定します。 |いいえ |

**New-AzDataFactoryEncryptValue** コマンドレットを使用して資格情報を暗号化し、次の例で示すようにそれを接続文字列で使用できます (**EncryptedCredential** プロパティ)。

```json
"connectionString": "Data Source=<servername>;Initial Catalog=<databasename>;Integrated Security=True;EncryptedCredential=<encrypted credential>",
```


#### <a name="example-json-for-using-sql-authentication"></a>例:SQL 認証を使用するための JSON

```json
{
    "name": "MyOnPremisesSQLDB",
    "properties": {
        "type": "OnPremisesSqlServer",
        "typeProperties": {
            "connectionString": "Data Source=<servername>;Initial Catalog=MarketingCampaigns;Integrated Security=False;User ID=<username>;Password=<password>;",
            "gatewayName": "<gateway name>"
        }
    }
}
```
#### <a name="example-json-for-using-windows-authentication"></a>例:Windows 認証を使用するための JSON

ユーザー名とパスワードが指定されている場合、ゲートウェイはその情報を使用して指定されたユーザー アカウントに偽装して、オンプレミス SQL Server データベースに接続します。 指定されていない場合、ゲートウェイは、Gateway (スタートアップ アカウント) のセキュリティ コンテキストを使用して SQL Server に直接接続します。

```json
{
    "Name": " MyOnPremisesSQLDB",
    "Properties": {
        "type": "OnPremisesSqlServer",
        "typeProperties": {
            "ConnectionString": "Data Source=<servername>;Initial Catalog=MarketingCampaigns;Integrated Security=True;",
            "username": "<domain\\username>",
            "password": "<password>",
            "gatewayName": "<gateway name>"
        }
    }
}
```

詳細については、[SQL Server コネクタ](data-factory-sqlserver-connector.md#linked-service-properties)に関する記事を参照してください。

### <a name="dataset"></a>Dataset
SQL Server データセットを定義するには、データセットの **type** を **SqlServerTable** に設定し、**typeProperties** セクションで以下のプロパティを指定します。

| プロパティ | 説明 | 必須 |
| --- | --- | --- |
| tableName |リンクされたサービスが参照する SQL Server Database インスタンスのテーブルまたはビューの名前です。 |はい |

#### <a name="example"></a>例
```json
{
    "name": "SqlServerInput",
    "properties": {
        "type": "SqlServerTable",
        "linkedServiceName": "SqlServerLinkedService",
        "typeProperties": {
            "tableName": "MyTable"
        },
        "external": true,
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "policy": {
            "externalData": {
                "retryInterval": "00:01:00",
                "retryTimeout": "00:10:00",
                "maximumRetry": 3
            }
        }
    }
}
```

詳細については、[SQL Server コネクタ](data-factory-sqlserver-connector.md#dataset-properties)に関する記事を参照してください。

### <a name="sql-source-in-copy-activity"></a>コピー アクティビティの SQL ソース
SQL Server データベースからデータをコピーする場合は、コピー アクティビティの **source type** を **SqlSource** に設定し、**source** セクションで以下のプロパティを指定します。


| プロパティ | 説明 | 使用できる値 | 必須 |
| --- | --- | --- | --- |
| sqlReaderQuery |カスタム クエリを使用してデータを読み取ります。 |SQL クエリ文字列。 (例: `select * from MyTable`)。 入力データセットによって参照されるデータベースから複数のテーブルを参照する場合があります。 指定されていない場合に実行される SQL ステートメント: select from MyTable |いいえ |
| sqlReaderStoredProcedureName |ソース テーブルからデータを読み取るストアド プロシージャの名前。 |ストアド プロシージャの名前。 |いいえ |
| storedProcedureParameters |ストアド プロシージャのパラメーター。 |名前と値のペア。 パラメーターの名前とその大文字と小文字は、ストアド プロシージャのパラメーターの名前とその大文字小文字と一致する必要があります。 |いいえ |

SqlSource に **sqlReaderQuery** が指定されている場合、コピー アクティビティでは、データを取得するために SQL Server Database ソースに対してこのクエリを実行します。

または、**sqlReaderStoredProcedureName** と **storedProcedureParameters** を指定して、ストアド プロシージャを指定することができます (ストアド プロシージャでパラメーターを使用する場合)。

SqlReaderQuery または sqlReaderStoredProcedureName を指定しない場合は、SQL Server Database に対して実行する選択クエリを作成するために、構造セクションで定義された列が使用されます。 データセット定義に構造がない場合は、すべての列がテーブルから選択されます。

> [!NOTE]
> **sqlReaderStoredProcedureName** を使用する場合でも、データセット JSON の **tableName** プロパティの値を指定する必要があります。 ただし、このテーブルに対して実行される検証はありません。


#### <a name="example"></a>例
```json
{
    "name": "SamplePipeline",
    "properties": {
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-01T19:00:00",
        "description": "pipeline for copy activity",
        "activities": [{
            "name": "SqlServertoBlob",
            "description": "copy activity",
            "type": "Copy",
            "inputs": [{
                "name": " SqlServerInput"
            }],
            "outputs": [{
                "name": "AzureBlobOutput"
            }],
            "typeProperties": {
                "source": {
                    "type": "SqlSource",
                    "SqlReaderQuery": "$$Text.Format('select * from MyTable where timestampcolumn >= \\'{0:yyyy-MM-dd HH:mm}\\' AND timestampcolumn < \\'{1:yyyy-MM-dd HH:mm}\\'', WindowStart, WindowEnd)"
                },
                "sink": {
                    "type": "BlobSink"
                }
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "policy": {
                "concurrency": 1,
                "executionPriorityOrder": "OldestFirst",
                "retry": 0,
                "timeout": "01:00:00"
            }
        }]
    }
}
```

この例では、SqlSource に **sqlReaderQuery** が指定されています。 コピー アクティビティでは、データを取得するために SQL Server Database ソースに対してこのクエリを実行します。 または、**sqlReaderStoredProcedureName** と **storedProcedureParameters** を指定して、ストアド プロシージャを指定することができます (ストアド プロシージャでパラメーターを使用する場合)。 sqlReaderQuery は、入力データセットによって参照されるデータベースで複数のテーブルを参照できます。 参照できるテーブルは、データセットの tableName typeProperty として設定されるテーブルに限りません。

SqlReaderQuery または sqlReaderStoredProcedureName を指定しない場合は、SQL Server Database に対して実行する選択クエリを作成するために、構造セクションで定義された列が使用されます。 データセット定義に構造がない場合は、すべての列がテーブルから選択されます。

詳細については、[SQL Server コネクタ](data-factory-sqlserver-connector.md#copy-activity-properties)に関する記事を参照してください。

### <a name="sql-sink-in-copy-activity"></a>コピー アクティビティの SQL シンク
SQL Server データベースにデータをコピーする場合は、コピー アクティビティの **sink type** を **SqlSink** に設定し、**sink** セクションで以下のプロパティを指定します。

| プロパティ | 説明 | 使用できる値 | 必須 |
| --- | --- | --- | --- |
| writeBatchTimeout |タイムアウトする前に一括挿入操作の完了を待つ時間です。 |TimeSpan<br/><br/> 例:"00:30:00" (30 分)。 |いいえ |
| writeBatchSize |バッファー サイズが writeBatchSize に達したときに SQL テーブルにデータを挿入します。 |整数 (行数) |いいえ (既定値:10000) |
| sqlWriterCleanupScript |特定のスライスのデータを消去するコピー アクティビティのクエリを指定します。 詳細については、「再現性」セクションを参照してください。 |クエリ ステートメント。 |いいえ |
| sliceIdentifierColumnName |自動生成スライス ID を入力するためのコピー アクティビティの列名を指定します。再実行時、特定のスライスのデータを消去するときに使用されます。 詳細については、「再現性」セクションを参照してください。 |バイナリ (32) のデータ型の列の列名。 |いいえ |
| sqlWriterStoredProcedureName |対象テーブルにデータをアップサート (更新/挿入) するストアド プロシージャの名前。 |ストアド プロシージャの名前。 |いいえ |
| storedProcedureParameters |ストアド プロシージャのパラメーター。 |名前と値のペア。 パラメーターの名前とその大文字と小文字は、ストアド プロシージャのパラメーターの名前とその大文字小文字と一致する必要があります。 |いいえ |
| sqlWriterTableType |ストアド プロシージャで使用するテーブル型の名前を指定します。 コピー アクティビティでは、このテーブル型の一時テーブルでデータを移動できます。 その後、ストアド プロシージャのコードにより、コピーされたデータを既存のデータと結合できます。 |テーブルの種類の名前。 |いいえ |

#### <a name="example"></a>例
パイプラインには、この入力データセットと出力データセットを使用するように構成され、1 時間おきに実行するようにスケジュールされているコピー アクティビティが含まれています。 パイプラインの JSON 定義で、**source** 型が **BlobSource** に設定され、**sink** 型が **SqlSink** に設定されています。

```json
{
    "name": "SamplePipeline",
    "properties": {
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-01T19:00:00",
        "description": "pipeline with copy activity",
        "activities": [{
            "name": "AzureBlobtoSQL",
            "description": "Copy Activity",
            "type": "Copy",
            "inputs": [{
                "name": "AzureBlobInput"
            }],
            "outputs": [{
                "name": " SqlServerOutput "
            }],
            "typeProperties": {
                "source": {
                    "type": "BlobSource",
                    "blobColumnSeparators": ","
                },
                "sink": {
                    "type": "SqlSink"
                }
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "policy": {
                "concurrency": 1,
                "executionPriorityOrder": "OldestFirst",
                "retry": 0,
                "timeout": "01:00:00"
            }
        }]
    }
}
```

詳細については、[SQL Server コネクタ](data-factory-sqlserver-connector.md#copy-activity-properties)に関する記事を参照してください。

## <a name="sybase"></a>Sybase

### <a name="linked-service"></a>リンクされたサービス
Sybase のリンクされたサービスを定義するには、リンクされたサービスの **type** を **OnPremisesSybase** に設定し、**typeProperties** セクションで以下のプロパティを指定します。

| プロパティ | 説明 | 必須 |
| --- | --- | --- |
| server |Sybase サーバーの名前です。 |はい |
| database |Sybase データベースの名前です。 |はい |
| schema |データベース内のスキーマの名前です。 |いいえ |
| authenticationType |Sybase データベースへの接続に使用される認証の種類です。 次のいずれかの値になります。Anonymous、Basic、および Windows。 |はい |
| username |Basic または Windows 認証を使用している場合は、ユーザー名を指定します。 |いいえ |
| password |ユーザー名に指定したユーザー アカウントのパスワードを指定します。 |いいえ |
| gatewayName |Data Factory サービスが、オンプレミスの Sybase データベースへの接続に使用するゲートウェイの名前です。 |はい |

#### <a name="example"></a>例
```json
{
    "name": "OnPremSybaseLinkedService",
    "properties": {
        "type": "OnPremisesSybase",
        "typeProperties": {
            "server": "<server>",
            "database": "<database>",
            "schema": "<schema>",
            "authenticationType": "<authentication type>",
            "username": "<username>",
            "password": "<password>",
            "gatewayName": "<gatewayName>"
        }
    }
}
```

詳細については、[Sybase コネクタ](data-factory-onprem-sybase-connector.md#linked-service-properties)に関する記事を参照してください。

### <a name="dataset"></a>Dataset
Sybase データセットを定義するには、データセットの **type** を **RelationalTable** に設定し、**typeProperties** セクションで以下のプロパティを指定します。

| プロパティ | 説明 | 必須 |
| --- | --- | --- |
| tableName |リンクされたサービスが参照する Sybase データベース インスタンスのテーブルの名前です。 |いいえ (**RelationalSource** の **クエリ** が指定されている場合) |

#### <a name="example"></a>例

```json
{
    "name": "SybaseDataSet",
    "properties": {
        "type": "RelationalTable",
        "linkedServiceName": "OnPremSybaseLinkedService",
        "typeProperties": {},
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": true,
        "policy": {
            "externalData": {
                "retryInterval": "00:01:00",
                "retryTimeout": "00:10:00",
                "maximumRetry": 3
            }
        }
    }
}
```

詳細については、[Sybase コネクタ](data-factory-onprem-sybase-connector.md#dataset-properties)に関する記事を参照してください。

### <a name="relational-source-in-copy-activity"></a>コピー アクティビティのリレーショナル ソース
Sybase データベースからデータをコピーする場合は、コピー アクティビティの **source type** を **RelationalSource** に設定し、**source** セクションで以下のプロパティを指定します。


| プロパティ | 説明 | 使用できる値 | 必須 |
| --- | --- | --- | --- |
| query |カスタム クエリを使用してデータを読み取ります。 |SQL クエリ文字列。 (例: `select * from MyTable`)。 |いいえ (**データセット**の **tableName** が指定されている場合) |

#### <a name="example"></a>例

```json
{
    "name": "CopySybaseToBlob",
    "properties": {
        "description": "pipeline for copy activity",
        "activities": [{
            "type": "Copy",
            "typeProperties": {
                "source": {
                    "type": "RelationalSource",
                    "query": "select * from DBA.Orders"
                },
                "sink": {
                    "type": "BlobSink"
                }
            },
            "inputs": [{
                "name": "SybaseDataSet"
            }],
            "outputs": [{
                "name": "AzureBlobSybaseDataSet"
            }],
            "policy": {
                "timeout": "01:00:00",
                "concurrency": 1
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "name": "SybaseToBlob"
        }],
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-01T19:00:00"
    }
}
```

詳細については、[Sybase コネクタ](data-factory-onprem-sybase-connector.md#copy-activity-properties)に関する記事を参照してください。

## <a name="teradata"></a>Teradata

### <a name="linked-service"></a>リンクされたサービス
Teradata のリンクされたサービスを定義するには、リンクされたサービスの **type** を **OnPremisesTeradata** に設定し、**typeProperties** セクションで以下のプロパティを指定します。

| プロパティ | 説明 | 必須 |
| --- | --- | --- |
| server |Teradata のサーバーの名前です。 |はい |
| authenticationType |Teradata データベースへの接続に使用される認証の種類です。 次のいずれかの値になります。Anonymous、Basic、および Windows。 |はい |
| username |Basic または Windows 認証を使用している場合は、ユーザー名を指定します。 |いいえ |
| password |ユーザー名に指定したユーザー アカウントのパスワードを指定します。 |いいえ |
| gatewayName |Data Factory サービスが、オンプレミスの Teradata データベースへの接続に使用するゲートウェイの名前です。 |はい |

#### <a name="example"></a>例
```json
{
    "name": "OnPremTeradataLinkedService",
    "properties": {
        "type": "OnPremisesTeradata",
        "typeProperties": {
            "server": "<server>",
            "authenticationType": "<authentication type>",
            "username": "<username>",
            "password": "<password>",
            "gatewayName": "<gatewayName>"
        }
    }
}
```

詳細については、[Teradata コネクタ](data-factory-onprem-teradata-connector.md#linked-service-properties)に関する記事を参照してください。

### <a name="dataset"></a>Dataset
Teradata BLOB データセットを定義するには、データセットの **type** を **RelationalTable** に設定します。 現時点では、Teradata データセットでサポートされる type プロパティはありません。

#### <a name="example"></a>例
```json
{
    "name": "TeradataDataSet",
    "properties": {
        "type": "RelationalTable",
        "linkedServiceName": "OnPremTeradataLinkedService",
        "typeProperties": {},
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": true,
        "policy": {
            "externalData": {
                "retryInterval": "00:01:00",
                "retryTimeout": "00:10:00",
                "maximumRetry": 3
            }
        }
    }
}
```

詳細については、[Teradata コネクタ](data-factory-onprem-teradata-connector.md#dataset-properties)に関する記事を参照してください。

### <a name="relational-source-in-copy-activity"></a>コピー アクティビティのリレーショナル ソース
Teradata データベースからデータをコピーする場合は、コピー アクティビティの **source type** を **RelationalSource** に設定し、**source** セクションで以下のプロパティを指定します。

| プロパティ | 説明 | 使用できる値 | 必須 |
| --- | --- | --- | --- |
| query |カスタム クエリを使用してデータを読み取ります。 |SQL クエリ文字列。 (例: `select * from MyTable`)。 |はい |

#### <a name="example"></a>例

```json
{
    "name": "CopyTeradataToBlob",
    "properties": {
        "description": "pipeline for copy activity",
        "activities": [{
            "type": "Copy",
            "typeProperties": {
                "source": {
                    "type": "RelationalSource",
                    "query": "$$Text.Format('select * from MyTable where timestamp >= \\'{0:yyyy-MM-ddTHH:mm:ss}\\' AND timestamp < \\'{1:yyyy-MM-ddTHH:mm:ss}\\'', SliceStart, SliceEnd)"
                },
                "sink": {
                    "type": "BlobSink",
                    "writeBatchSize": 0,
                    "writeBatchTimeout": "00:00:00"
                }
            },
            "inputs": [{
                "name": "TeradataDataSet"
            }],
            "outputs": [{
                "name": "AzureBlobTeradataDataSet"
            }],
            "policy": {
                "timeout": "01:00:00",
                "concurrency": 1
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "name": "TeradataToBlob"
        }],
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-01T19:00:00",
        "isPaused": false
    }
}
```

詳細については、[Teradata コネクタ](data-factory-onprem-teradata-connector.md#copy-activity-properties)に関する記事を参照してください。

## <a name="cassandra"></a>Cassandra


### <a name="linked-service"></a>リンクされたサービス
Cassandra のリンクされたサービスを定義するには、リンクされたサービスの **type** を **OnPremisesCassandra** に設定し、**typeProperties** セクションで以下のプロパティを指定します。

| プロパティ | 説明 | 必須 |
| --- | --- | --- |
| host |Cassandra サーバーの 1 つまたは複数の IP アドレスかホスト名。<br/><br/>IP アドレスまたはホスト名のコンマ区切りのリストを指定して、すべてのサーバーに同時に接続します。 |はい |
| port |Cassandra サーバーがクライアント接続のリッスンに使用する TCP ポート。 |いいえ、既定値: 9042 |
| authenticationType |Basic、または匿名 |はい |
| username |ユーザー アカウントのユーザー名を指定します。 |はい (authenticationType が Basic に設定されている場合)。 |
| password |ユーザー アカウントのパスワードを指定します。 |はい (authenticationType が Basic に設定されている場合)。 |
| gatewayName |オンプレミスの Cassandra データベースへの接続に使用されるゲートウェイの名前。 |はい |
| encryptedCredential |ゲートウェイによって暗号化された資格情報。 |いいえ |

#### <a name="example"></a>例

```json
{
    "name": "CassandraLinkedService",
    "properties": {
        "type": "OnPremisesCassandra",
        "typeProperties": {
            "authenticationType": "Basic",
            "host": "<cassandra server name or IP address>",
            "port": 9042,
            "username": "user",
            "password": "password",
            "gatewayName": "<onpremgateway>"
        }
    }
}
```

詳細については、[Cassandra コネクタ](data-factory-onprem-cassandra-connector.md#linked-service-properties)に関する記事を参照してください。

### <a name="dataset"></a>Dataset
Cassandra データセットを定義するには、データセットの **type** を **CassandraTable** に設定し、**typeProperties** セクションで以下のプロパティを指定します。

| プロパティ | 説明 | 必須 |
| --- | --- | --- |
| keyspace |Cassandra データベースのkeyspace またはスキーマの名前。 |はい (**CassandraSource** の**クエリ**が定義されていない場合)。 |
| tableName |Cassandra データベースのテーブル名。 |はい (**CassandraSource** の**クエリ**が定義されていない場合)。 |

#### <a name="example"></a>例

```json
{
    "name": "CassandraInput",
    "properties": {
        "linkedServiceName": "CassandraLinkedService",
        "type": "CassandraTable",
        "typeProperties": {
            "tableName": "mytable",
            "keySpace": "<key space>"
        },
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": true,
        "policy": {
            "externalData": {
                "retryInterval": "00:01:00",
                "retryTimeout": "00:10:00",
                "maximumRetry": 3
            }
        }
    }
}
```

詳細については、[Cassandra コネクタ](data-factory-onprem-cassandra-connector.md#dataset-properties)に関する記事を参照してください。

### <a name="cassandra-source-in-copy-activity"></a>コピー アクティビティの Cassandra ソース
Cassandra からデータをコピーする場合は、コピー アクティビティの **source type** を **CassandraSource** に設定し、**source** セクションで以下のプロパティを指定します。

| プロパティ | 説明 | 使用できる値 | 必須 |
| --- | --- | --- | --- |
| query |カスタム クエリを使用してデータを読み取ります。 |SQL-92 クエリまたはCQL クエリ。 「 [CQL reference (CQL リファレンス)](https://docs.datastax.com/en/cql/3.1/cql/cql_reference/cqlReferenceTOC.html)」をご覧ください。 <br/><br/>SQL クエリを使用する場合は、クエリを実行するテーブルを表す **keyspace name.table name** を指定します。 |いいえ (データセットの tableName と keyspace が定義されていない場合)。 |
| consistencyLevel |一貫性レベルは、データがクライアント アプリケーションに返される前に、読み取り要求に応答する必要があるレプリカの数を指定します。 Cassandra は読み取り要求を満たすために、データの指定された数のレプリカを確認します。 |ONE、TWO、THREE、QUORUM、ALL、 LOCAL_QUORUM、EACH_QUORUM、 LOCAL_ONE。 詳細については、「 [Configuring data consistency (データ整合性の構成)](https://docs.datastax.com/en/cassandra/2.1/cassandra/dml/dml_config_consistency_c.html) 」をご覧ください。 |いいえ。 既定値は ONE です。 |

#### <a name="example"></a>例

```json
{
    "name": "SamplePipeline",
    "properties": {
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-01T19:00:00",
        "description": "pipeline with copy activity",
        "activities": [{
            "name": "CassandraToAzureBlob",
            "description": "Copy from Cassandra to an Azure blob",
            "type": "Copy",
            "inputs": [{
                "name": "CassandraInput"
            }],
            "outputs": [{
                "name": "AzureBlobOutput"
            }],
            "typeProperties": {
                "source": {
                    "type": "CassandraSource",
                    "query": "select id, firstname, lastname from mykeyspace.mytable"
                },
                "sink": {
                    "type": "BlobSink"
                }
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "policy": {
                "concurrency": 1,
                "executionPriorityOrder": "OldestFirst",
                "retry": 0,
                "timeout": "01:00:00"
            }
        }]
    }
}
```

詳細については、[Cassandra コネクタ](data-factory-onprem-cassandra-connector.md#copy-activity-properties)に関する記事を参照してください。

## <a name="mongodb"></a>MongoDB

### <a name="linked-service"></a>リンクされたサービス
MongoDB のリンクされたサービスを定義するには、リンクされたサービスの **type** を **OnPremisesMongoDB** に設定し、**typeProperties** セクションで以下のプロパティを指定します。

| プロパティ | 説明 | 必須 |
| --- | --- | --- |
| server |MongoDB サーバーの IP アドレスまたはホスト名。 |はい |
| port |MongoDB サーバーがクライアント接続のリッスンに使用する TCP ポート。 |省略可能、既定値: 27017 |
| authenticationType |Basic または Anonymous。 |はい |
| username |MongoDB にアクセスするためのユーザー アカウント。 |はい (基本認証が使用される場合)。 |
| password |ユーザーのパスワード。 |はい (基本認証が使用される場合)。 |
| authSource |認証のために資格情報を確認する際に使用する MongoDB データベースの名前。 |省略可能 (基本認証が使用される場合)。 既定では、管理者アカウントと、databaseName プロパティで指定されたデータベースが使用されます。 |
| databaseName |アクセスする MongoDB データベースの名前。 |はい |
| gatewayName |データ ストアにアクセスするゲートウェイの名前。 |はい |
| encryptedCredential |ゲートウェイによって暗号化された資格情報。 |省略可能 |

#### <a name="example"></a>例

```json
{
    "name": "OnPremisesMongoDbLinkedService",
    "properties": {
        "type": "OnPremisesMongoDb",
        "typeProperties": {
            "authenticationType": "<Basic or Anonymous>",
            "server": "< The IP address or host name of the MongoDB server >",
            "port": "<The number of the TCP port that the MongoDB server uses to listen for client connections.>",
            "username": "<username>",
            "password": "<password>",
            "authSource": "< The database that you want to use to check your credentials for authentication. >",
            "databaseName": "<database name>",
            "gatewayName": "<onpremgateway>"
        }
    }
}
```

詳細については、[MongoDB コネクタに関する記事](data-factory-on-premises-mongodb-connector.md#linked-service-properties)を参照してください。

### <a name="dataset"></a>Dataset
MongoDB データセットを定義するには、データセットの **type** を **MongoDbCollection** に設定し、**typeProperties** セクションで以下のプロパティを指定します。

| プロパティ | 説明 | 必須 |
| --- | --- | --- |
| collectionName |MongoDB データベースのコレクション名前。 |はい |

#### <a name="example"></a>例

```json
{
    "name": "MongoDbInputDataset",
    "properties": {
        "type": "MongoDbCollection",
        "linkedServiceName": "OnPremisesMongoDbLinkedService",
        "typeProperties": {
            "collectionName": "<Collection name>"
        },
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": true
    }
}
```

詳細については、[MongoDB コネクタに関する記事](data-factory-on-premises-mongodb-connector.md#dataset-properties)を参照してください。

#### <a name="mongodb-source-in-copy-activity"></a>コピー アクティビティの MongoDB ソース
MongoDB からデータをコピーする場合は、コピー アクティビティの **source type** を **MongoDbSource** に設定し、**source** セクションで以下のプロパティを指定します。

| プロパティ | 説明 | 使用できる値 | 必須 |
| --- | --- | --- | --- |
| query |カスタム クエリを使用してデータを読み取ります。 |SQL-92 クエリ文字列。 (例: `select * from MyTable`)。 |いいえ (**データセット**の **collectionName** が指定されている場合) |

#### <a name="example"></a>例

```json
{
    "name": "CopyMongoDBToBlob",
    "properties": {
        "description": "pipeline for copy activity",
        "activities": [{
            "type": "Copy",
            "typeProperties": {
                "source": {
                    "type": "MongoDbSource",
                    "query": "select * from MyTable"
                },
                "sink": {
                    "type": "BlobSink",
                    "writeBatchSize": 0,
                    "writeBatchTimeout": "00:00:00"
                }
            },
            "inputs": [{
                "name": "MongoDbInputDataset"
            }],
            "outputs": [{
                "name": "AzureBlobOutputDataSet"
            }],
            "policy": {
                "timeout": "01:00:00",
                "concurrency": 1
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "name": "MongoDBToAzureBlob"
        }],
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-01T19:00:00"
    }
}
```

詳細については、[MongoDB コネクタに関する記事](data-factory-on-premises-mongodb-connector.md#copy-activity-properties)を参照してください。

## <a name="amazon-s3"></a>Amazon S3


### <a name="linked-service"></a>リンクされたサービス
Amazon S3 のリンクされたサービスを定義するには、リンクされたサービスの **type** を **AwsAccessKey** に設定し、**typeProperties** セクションで以下のプロパティを指定します。

| プロパティ | 説明 | 使用できる値 | 必須 |
| --- | --- | --- | --- |
| accessKeyID |シークレット アクセス キーの ID。 |string |はい |
| secretAccessKey |シークレット アクセス キー自体。 |暗号化された秘密文字列 |はい |

#### <a name="example"></a>例
```json
{
    "name": "AmazonS3LinkedService",
    "properties": {
        "type": "AwsAccessKey",
        "typeProperties": {
            "accessKeyId": "<access key id>",
            "secretAccessKey": "<secret access key>"
        }
    }
}
```

詳細については、[Amazon S3 コネクタに関する記事](data-factory-amazon-simple-storage-service-connector.md#linked-service-properties)を参照してください。

### <a name="dataset"></a>Dataset
Amazon S3 データセットを定義するには、データセットの **type** を **AmazonS3** に設定し、**typeProperties** セクションで以下のプロパティを指定します。

| プロパティ | 説明 | 使用できる値 | 必須 |
| --- | --- | --- | --- |
| bucketName |S3 バケットの名前。 |string |はい |
| key |S3 オブジェクト キー。 |string |いいえ |
| prefix |S3 オブジェクト キーのプレフィックス。 キーがこのプレフィックスで始まるオブジェクトが選択されます。 キーが空の場合にのみ適用されます。 |string |いいえ |
| version |S3 のバージョン管理が有効になっている場合の S3 オブジェクトのバージョン。 |string |いいえ |
| format | 次の種類の形式がサポートされます:**TextFormat**、**JsonFormat**、**AvroFormat**、**OrcFormat**、**ParquetFormat** です。 形式の **type** プロパティをいずれかの値に設定します。 詳細については、[Text Format](data-factory-supported-file-and-compression-formats.md#text-format)、[Json Format](data-factory-supported-file-and-compression-formats.md#json-format)、[Avro Format](data-factory-supported-file-and-compression-formats.md#avro-format)、[Orc Format](data-factory-supported-file-and-compression-formats.md#orc-format)、[Parquet Format](data-factory-supported-file-and-compression-formats.md#parquet-format) の各セクションを参照してください。 <br><br> ファイルベースのストア間で**ファイルをそのままコピー** (バイナリ コピー) する場合は、入力と出力の両方のデータセット定義で format セクションをスキップします。 |いいえ | |
| compression | データの圧縮の種類とレベルを指定します。 サポートされる種類は、**GZip**、**Deflate**、**BZip2**、**ZipDeflate** です。 サポートされるレベルは、**Optimal** と **Fastest** です。 詳細については、「[Azure Data Factory のファイル形式と圧縮形式](data-factory-supported-file-and-compression-formats.md#compression-support)」を参照してください。 |いいえ | |


> [!NOTE]
> bucketName をキーの組み合わせによって S3 オブジェクトの場所が指定されます。バケットは S3 オブジェクトのルート コンテナーであり、キーは、S3 オブジェクトへの完全パスです。

#### <a name="example-sample-dataset-with-prefix"></a>例:プレフィックスを持つサンプル データセット

```json
{
    "name": "dataset-s3",
    "properties": {
        "type": "AmazonS3",
        "linkedServiceName": "link- testS3",
        "typeProperties": {
            "prefix": "testFolder/test",
            "bucketName": "<S3 bucket name>",
            "format": {
                "type": "OrcFormat"
            }
        },
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": true
    }
}
```
#### <a name="example-sample-data-set-with-version"></a>例:サンプル データセット (バージョンあり)

```json
{
    "name": "dataset-s3",
    "properties": {
        "type": "AmazonS3",
        "linkedServiceName": "link- testS3",
        "typeProperties": {
            "key": "testFolder/test.orc",
            "bucketName": "<S3 bucket name>",
            "version": "XXXXXXXXXczm0CJajYkHf0_k6LhBmkcL",
            "format": {
                "type": "OrcFormat"
            }
        },
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": true
    }
}
```

#### <a name="example-dynamic-paths-for-s3"></a>例:S3 の動的パス
このサンプルでは、Amazon S3 データセット内のキーおよび bucketName プロパティの固定値を使用します。

```json
"key": "testFolder/test.orc",
"bucketName": "<S3 bucket name>",
```

実行時に、SliceStart などのシステム変数を使用して、Data Factory でキーと bucketName を動的に計算できます。

```json
"key": "$$Text.Format('{0:MM}/{0:dd}/test.orc', SliceStart)"
"bucketName": "$$Text.Format('{0:yyyy}', SliceStart)"
```

同じことを、Amazon S3 データセットの prefix プロパティにも行うことができます。 サポートされる関数と変数の一覧については、「 [Data Factory の関数およびシステム変数](data-factory-functions-variables.md) 」を参照してください。

詳細については、[Amazon S3 コネクタに関する記事](data-factory-amazon-simple-storage-service-connector.md#dataset-properties)を参照してください。

### <a name="file-system-source-in-copy-activity"></a>コピー アクティビティのファイル システム ソース
Amazon S3 からデータをコピーする場合は、コピー アクティビティの **source type** を **FileSystemSource** に設定し、**source** セクションで以下のプロパティを指定します。


| プロパティ | 説明 | 使用できる値 | 必須 |
| --- | --- | --- | --- |
| recursive |ディレクトリで S3 オブジェクトを再帰的に一覧表示するかどうかを指定します。 |true または false |いいえ |


#### <a name="example"></a>例


```json
{
    "name": "CopyAmazonS3ToBlob",
    "properties": {
        "description": "pipeline for copy activity",
        "activities": [{
            "type": "Copy",
            "typeProperties": {
                "source": {
                    "type": "FileSystemSource",
                    "recursive": true
                },
                "sink": {
                    "type": "BlobSink",
                    "writeBatchSize": 0,
                    "writeBatchTimeout": "00:00:00"
                }
            },
            "inputs": [{
                "name": "AmazonS3InputDataset"
            }],
            "outputs": [{
                "name": "AzureBlobOutputDataSet"
            }],
            "policy": {
                "timeout": "01:00:00",
                "concurrency": 1
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "name": "AmazonS3ToBlob"
        }],
        "start": "2016-08-08T18:00:00",
        "end": "2016-08-08T19:00:00"
    }
}
```

詳細については、[Amazon S3 コネクタに関する記事](data-factory-amazon-simple-storage-service-connector.md#copy-activity-properties)を参照してください。

## <a name="file-system"></a>ファイル システム


### <a name="linked-service"></a>リンクされたサービス
**オンプレミスのファイル サーバー**のリンクされているサービスで、オンプレミスのファイル システムを Azure Data Factory にリンクできます。 次の表は、オンプレミスのファイル サーバーのリンクされたサービスに固有の JSON 要素の説明をまとめたものです。

| プロパティ | 説明 | 必須 |
| --- | --- | --- |
| type |type プロパティが **OnPremisesFileServer** に設定されていることを確認します。 |はい |
| host |コピーするフォルダーのルート パスを指定します。 文字列内の特殊文字にはエスケープ文字 "\" を使用します。 例については、「サンプルのリンクされたサービスとデータセットの定義」を参照してください。 |はい |
| userid |サーバーにアクセスするユーザーの ID を指定します。 |No (encryptedCredential を選択する場合) |
| password |ユーザー (userid) のパスワードを指定します。 |いいえ (encryptedCredential を選択する場合) |
| encryptedCredential |New-AzDataFactoryEncryptValue コマンドレットを実行して取得できる暗号化された資格情報を指定します。 |いいえ (プレーン テキストでユーザー ID とパスワードを指定する場合) |
| gatewayName |Data Factory が、オンプレミスのファイル サーバーへの接続に使用するゲートウェイの名前を指定します。 |はい |

#### <a name="sample-folder-path-definitions"></a>フォルダー パス定義の例

| シナリオ | リンクされたサービス定義のホスト | データセット定義の folderPath |
| --- | --- | --- |
| Data Management Gateway コンピューター上のローカル フォルダー: <br/><br/>次に例を示します。D:\\\* または D:\folder\subfolder\\* |D:\\\\ (Data Management Gateway 2.0 以降のバージョンの場合) <br/><br/> localhost (Data Management Gateway 2.0 より以前のバージョン) |\\\\またはフォルダー\\\\サブフォルダー (Data Management Gateway 2.0 以降のバージョン) <br/><br/>D:\\\\ または D:\\\\フォルダー\\\\サブフォルダー (バージョン 2.0 より前のゲートウェイ) |
| リモート共有フォルダー: <br/><br/>例: \\\\myserver\\share\\\* または \\\\myserver\\share\\フォルダー\\サブフォルダー\\* |\\\\\\\\myserver\\\\share |.\\\\ またはフォルダー\\\\サブフォルダー |


#### <a name="example-using-username-and-password-in-plain-text"></a>例:プレーンテキストでのユーザー名とパスワードの使用

```json
{
    "Name": "OnPremisesFileServerLinkedService",
    "properties": {
        "type": "OnPremisesFileServer",
        "typeProperties": {
            "host": "\\\\Contosogame-Asia",
            "userid": "Admin",
            "password": "123456",
            "gatewayName": "<onpremgateway>"
        }
    }
}
```

#### <a name="example-using-encryptedcredential"></a>例:encryptedcredential の使用

```json
{
    "Name": " OnPremisesFileServerLinkedService ",
    "properties": {
        "type": "OnPremisesFileServer",
        "typeProperties": {
            "host": "D:\\",
            "encryptedCredential": "WFuIGlzIGRpc3Rpbmd1aXNoZWQsIG5vdCBvbmx5IGJ5xxxxxxxxxxxxxxxxx",
            "gatewayName": "<onpremgateway>"
        }
    }
}
```

詳細については、[ファイル システム コネクタに関する記事](data-factory-onprem-file-system-connector.md#linked-service-properties)を参照してください。

### <a name="dataset"></a>Dataset
ファイル システム データセットを定義するには、データセットの **type** を **FileShare** に設定し、**typeProperties** セクションで以下のプロパティを指定します。

| プロパティ | 説明 | 必須 |
| --- | --- | --- |
| folderPath |フォルダーへのサブパスを指定します。 文字列内の特殊文字にはエスケープ文字 "\" を使用します。 例については、「サンプルのリンクされたサービスとデータセットの定義」を参照してください。<br/><br/>このプロパティを **partitionBy** と組み合わせて、スライスの開始/終了日時に基づくフォルダー パスを使用できます。 |はい |
| fileName |テーブルでフォルダー内の特定のファイルを参照するには、**folderPath** にファイルの名前を指定します。 このプロパティの値を設定しない場合、テーブルはフォルダー内のすべてのファイルを参照します。<br/><br/>出力データセットに fileName が指定されていない場合、生成されるファイルの名前は次の形式になります。 <br/><br/>`Data.<Guid>.txt` (例: Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt) |いいえ |
| fileFilter |すべてのファイルではなく、folderPath 内のファイルのサブセットを選択するために使用するフィルターを指定します。 <br/><br/>使用可能な値: `*` (複数の文字) および `?` (単一の文字)。<br/><br/>例 1: "fileFilter": "*.log"<br/>例 2: "fileFilter": 2016-1-?.txt"<br/><br/>fileFilter は FileShare 入力データセットに適用されることに注意してください。 |いいえ |
| partitionedBy |partitionedBy を使用して時系列データに動的な folderPath/fileName を指定できます。 たとえば、1 時間ごとのデータに対して folderPath がパラメーター化されます。 |いいえ |
| format | 次の種類の形式がサポートされます:**TextFormat**、**JsonFormat**、**AvroFormat**、**OrcFormat**、**ParquetFormat** です。 形式の **type** プロパティをいずれかの値に設定します。 詳細については、[Text Format](data-factory-supported-file-and-compression-formats.md#text-format)、[Json Format](data-factory-supported-file-and-compression-formats.md#json-format)、[Avro Format](data-factory-supported-file-and-compression-formats.md#avro-format)、[Orc Format](data-factory-supported-file-and-compression-formats.md#orc-format)、[Parquet Format](data-factory-supported-file-and-compression-formats.md#parquet-format) の各セクションを参照してください。 <br><br> ファイルベースのストア間で**ファイルをそのままコピー** (バイナリ コピー) する場合は、入力と出力の両方のデータセット定義で format セクションをスキップします。 |いいえ |
| compression | データの圧縮の種類とレベルを指定します。 サポートされる種類は、**GZip**、**Deflate**、**BZip2**、および **ZipDeflate** であり、サポートされるレベルは**Optimal** と **Fastest** です。 「[Azure Data Factory のファイル形式と圧縮形式](data-factory-supported-file-and-compression-formats.md#compression-support)」を参照してください。 |いいえ |

> [!NOTE]
> fileName と fileFilter は、同時に使用することができません。

#### <a name="example"></a>例

```json
{
    "name": "OnpremisesFileSystemInput",
    "properties": {
        "type": " FileShare",
        "linkedServiceName": " OnPremisesFileServerLinkedService ",
        "typeProperties": {
            "folderPath": "mysharedfolder/yearno={Year}/monthno={Month}/dayno={Day}",
            "fileName": "{Hour}.csv",
            "partitionedBy": [{
                "name": "Year",
                "value": {
                    "type": "DateTime",
                    "date": "SliceStart",
                        "format": "yyyy"
                }
            }, {
                "name": "Month",
                "value": {
                    "type": "DateTime",
                    "date": "SliceStart",
                    "format": "MM"
                }
            }, {
                "name": "Day",
                "value": {
                    "type": "DateTime",
                    "date": "SliceStart",
                    "format": "dd"
                }
            }, {
                "name": "Hour",
                "value": {
                    "type": "DateTime",
                    "date": "SliceStart",
                    "format": "HH"
                }
            }]
        },
        "external": true,
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "policy": {
            "externalData": {
                "retryInterval": "00:01:00",
                "retryTimeout": "00:10:00",
                "maximumRetry": 3
            }
        }
    }
}
```

詳細については、[ファイル システム コネクタに関する記事](data-factory-onprem-file-system-connector.md#dataset-properties)を参照してください。

### <a name="file-system-source-in-copy-activity"></a>コピー アクティビティのファイル システム ソース
ファイル システムからデータをコピーする場合は、コピー アクティビティの **source type** を **FileSystemSource** に設定し、**source** セクションで以下のプロパティを指定します。

| プロパティ | 説明 | 使用できる値 | 必須 |
| --- | --- | --- | --- |
| recursive |データをサブフォルダーから再帰的に読み取るか、指定したフォルダーからのみ読み取るかを指定します。 |True、False (既定値) |いいえ |

#### <a name="example"></a>例

```json
{
    "name": "SamplePipeline",
    "properties": {
        "start": "2015-06-01T18:00:00",
        "end": "2015-06-01T19:00:00",
        "description": "Pipeline for copy activity",
        "activities": [{
            "name": "OnpremisesFileSystemtoBlob",
            "description": "copy activity",
            "type": "Copy",
            "inputs": [{
                "name": "OnpremisesFileSystemInput"
            }],
            "outputs": [{
                "name": "AzureBlobOutput"
            }],
            "typeProperties": {
                "source": {
                    "type": "FileSystemSource"
                },
                "sink": {
                    "type": "BlobSink"
                }
            },
            "scheduler": {
            "frequency": "Hour",
                "interval": 1
            },
            "policy": {
                "concurrency": 1,
                "executionPriorityOrder": "OldestFirst",
                "retry": 0,
                "timeout": "01:00:00"
            }
        }]
    }
}
```
詳細については、[ファイル システム コネクタに関する記事](data-factory-onprem-file-system-connector.md#copy-activity-properties)を参照してください。

### <a name="file-system-sink-in-copy-activity"></a>コピー アクティビティのファイル システム シンク
ファイル システムにデータをコピーする場合は、コピー アクティビティの **sink type** を **FileSystemSink** に設定し、**sink** セクションで以下のプロパティを指定します。

| プロパティ | 説明 | 使用できる値 | 必須 |
| --- | --- | --- | --- |
| copyBehavior |ソースが BlobSource または FileSystem である場合のコピー動作を定義します。 |**PreserveHierarchy:** ターゲット フォルダー内でファイル階層を保持します。 つまり、ソース フォルダーへのソース ファイルの相対パスはターゲット フォルダーへのターゲット ファイルの相対パスと同じになります。<br/><br/>**FlattenHierarchy:** ソース フォルダーのすべてのファイルがターゲット フォルダーの最初のレベルに作成されます。 ターゲット ファイルは、自動生成された名前で作成されます。<br/><br/>**MergeFiles:** ソース フォルダーのすべてのファイルを 1 つのファイルにマージします。 ファイル名/BLOB 名を指定した場合、マージされたファイル名は指定した名前になります。 それ以外は自動生成されたファイル名になります。 |いいえ |

auto-

#### <a name="example"></a>例

```json
{
    "name": "SamplePipeline",
    "properties": {
        "start": "2015-06-01T18:00:00",
        "end": "2015-06-01T20:00:00",
        "description": "pipeline for copy activity",
        "activities": [{
            "name": "AzureSQLtoOnPremisesFile",
            "description": "copy activity",
            "type": "Copy",
            "inputs": [{
                "name": "AzureSQLInput"
            }],
            "outputs": [{
                "name": "OnpremisesFileSystemOutput"
            }],
            "typeProperties": {
                "source": {
                    "type": "SqlSource",
                    "SqlReaderQuery": "$$Text.Format('select * from MyTable where timestampcolumn >= \\'{0:yyyy-MM-dd}\\' AND timestampcolumn < \\'{1:yyyy-MM-dd}\\'', WindowStart, WindowEnd)"
                },
                "sink": {
                    "type": "FileSystemSink"
                }
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "policy": {
                "concurrency": 1,
                "executionPriorityOrder": "OldestFirst",
                "retry": 3,
                "timeout": "01:00:00"
            }
        }]
    }
}
```

詳細については、[ファイル システム コネクタに関する記事](data-factory-onprem-file-system-connector.md#copy-activity-properties)を参照してください。

## <a name="ftp"></a>FTP

### <a name="linked-service"></a>リンクされたサービス
FTP のリンクされたサービスを定義するには、リンクされたサービスの **type** を **FtpServer** に設定し、**typeProperties** セクションで以下のプロパティを指定します。

| プロパティ | 説明 | 必須 | 既定値 |
| --- | --- | --- | --- |
| host |FTP サーバーの名前または IP アドレス |はい |&nbsp; |
| authenticationType |認証の種類を指定します |はい |Basic、Anonymous |
| username |FTP サーバーへのアクセスを持つユーザー |いいえ |&nbsp; |
| password |ユーザーのパスワード (ユーザー名) |いいえ |&nbsp; |
| encryptedCredential |FTP サーバーにアクセスするための暗号化された資格情報 |いいえ |&nbsp; |
| gatewayName |オンプレミスの FTP サーバーに接続するための Data Management Gateway の名前 |いいえ |&nbsp; |
| port |FTP サーバーがリッスンしているポート |いいえ |21 |
| enableSsl |FTP over SSL/TLS チャネルを使用するかどうかを指定します |いいえ |true |
| enableServerCertificateValidation |FTP over SSL/TLS チャネルを使用した場合にサーバーの SSL 証明書の検証を有効にするかどうかを指定します |いいえ |true |

#### <a name="example-using-anonymous-authentication"></a>例:匿名認証を使用する

```json
{
    "name": "FTPLinkedService",
    "properties": {
        "type": "FtpServer",
            "typeProperties": {
            "authenticationType": "Anonymous",
            "host": "myftpserver.com"
        }
    }
}
```

#### <a name="example-using-username-and-password-in-plain-text-for-basic-authentication"></a>例:基本認証のためユーザー名とパスワードをプレーン テキストで使用

```json
{
    "name": "FTPLinkedService",
    "properties": {
        "type": "FtpServer",
        "typeProperties": {
            "host": "myftpserver.com",
            "authenticationType": "Basic",
            "username": "Admin",
            "password": "123456"
        }
    }
}
```

#### <a name="example-using-port-enablessl-enableservercertificatevalidation"></a>例:ポート、enableSsl、enableServerCertificateValidation を使用

```json
{
    "name": "FTPLinkedService",
    "properties": {
        "type": "FtpServer",
        "typeProperties": {
            "host": "myftpserver.com",
            "authenticationType": "Basic",
            "username": "Admin",
            "password": "123456",
            "port": "21",
            "enableSsl": true,
            "enableServerCertificateValidation": true
        }
    }
}
```

#### <a name="example-using-encryptedcredential-for-authentication-and-gateway"></a>例:認証およびゲートウェイに encryptedCredential を使用

```json
{
    "name": "FTPLinkedService",
    "properties": {
        "type": "FtpServer",
        "typeProperties": {
            "host": "myftpserver.com",
            "authenticationType": "Basic",
            "encryptedCredential": "xxxxxxxxxxxxxxxxx",
            "gatewayName": "<onpremgateway>"
        }
      }
}
```

詳細については、[FTP コネクタ](data-factory-ftp-connector.md#linked-service-properties)に関する記事を参照してください。

### <a name="dataset"></a>Dataset
FTP データセットを定義するには、データセットの **type** を **FileShare** に設定し、**typeProperties** セクションで以下のプロパティを指定します。

| プロパティ | 説明 | 必須 |
| --- | --- | --- |
| folderPath |フォルダーへのサブ パス。 文字列内の特殊文字にはエスケープ文字 "\" を使用します。 例については、「サンプルのリンクされたサービスとデータセットの定義」を参照してください。<br/><br/>このプロパティを **partitionBy** と組み合わせて、スライスの開始/終了日時に基づくフォルダー パスを使用できます。 |はい
| fileName |テーブルでフォルダー内の特定のファイルを参照するには、**folderPath** にファイルの名前を指定します。 このプロパティの値を設定しない場合、テーブルはフォルダー内のすべてのファイルを参照します。<br/><br/>出力データセットに fileName が指定されていない場合、生成されるファイルの名前は次の形式になります。 <br/><br/>`Data.<Guid>.txt` (例: Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt) |いいえ |
| fileFilter |すべてのファイルではなく、folderPath 内のファイルのサブセットを選択するために使用するフィルターを指定します。<br/><br/>使用可能な値: `*` (複数の文字) および `?` (単一の文字)。<br/><br/>例 1: `"fileFilter": "*.log"`<br/>例 2: `"fileFilter": 2016-1-?.txt"`<br/><br/> fileFilter は FileShare 入力データセットに適用されます。 このプロパティは、HDFS ではサポートされません。 |いいえ |
| partitionedBy |partitionedBy を使用して時系列データに動的な folderPath と fileName を指定できます。 たとえば、1 時間ごとのデータに対して folderPath がパラメーター化されます。 |いいえ |
| format | 次の種類の形式がサポートされます:**TextFormat**、**JsonFormat**、**AvroFormat**、**OrcFormat**、**ParquetFormat** です。 形式の **type** プロパティをいずれかの値に設定します。 詳細については、[Text Format](data-factory-supported-file-and-compression-formats.md#text-format)、[Json Format](data-factory-supported-file-and-compression-formats.md#json-format)、[Avro Format](data-factory-supported-file-and-compression-formats.md#avro-format)、[Orc Format](data-factory-supported-file-and-compression-formats.md#orc-format)、[Parquet Format](data-factory-supported-file-and-compression-formats.md#parquet-format) の各セクションを参照してください。 <br><br> ファイルベースのストア間で**ファイルをそのままコピー** (バイナリ コピー) する場合は、入力と出力の両方のデータセット定義で format セクションをスキップします。 |いいえ |
| compression | データの圧縮の種類とレベルを指定します。 サポートされる種類は、**GZip**、**Deflate**、**BZip2**、および **ZipDeflate** であり、サポートされるレベルは**Optimal** と **Fastest** です。 詳細については、「[Azure Data Factory のファイル形式と圧縮形式](data-factory-supported-file-and-compression-formats.md#compression-support)」を参照してください。 |いいえ |
| useBinaryTransfer |バイナリ転送モードを使用するかどうかを指定します。 バイナリ モードの場合は true、ASCII モードの場合は false です。 既定値:True。 このプロパティを使用できるのは、関連するリンクされたサービスの種類が FtpServer である場合だけです。 |いいえ |

> [!NOTE]
> fileName と fileFilter は、同時に使用することができません。

#### <a name="example"></a>例

```json
{
    "name": "FTPFileInput",
    "properties": {
        "type": "FileShare",
        "linkedServiceName": "FTPLinkedService",
        "typeProperties": {
            "folderPath": "<path to shared folder>",
            "fileName": "test.csv",
            "useBinaryTransfer": true
        },
        "external": true,
        "availability": {
            "frequency": "Hour",
            "interval": 1
        }
    }
}
```

詳細については、[FTP コネクタ](data-factory-ftp-connector.md#dataset-properties)に関する記事を参照してください。

### <a name="file-system-source-in-copy-activity"></a>コピー アクティビティのファイル システム ソース
FTP サーバーからデータをコピーする場合は、コピー アクティビティの **source type** を **FileSystemSource** に設定し、**source** セクションで以下のプロパティを指定します。

| プロパティ | 説明 | 使用できる値 | 必須 |
| --- | --- | --- | --- |
| recursive |データをサブ フォルダーから再帰的に読み取るか、指定したフォルダーからのみ読み取るかを指定します。 |True、False (既定値) |いいえ |

#### <a name="example"></a>例

```json
{
    "name": "pipeline",
    "properties": {
        "activities": [{
            "name": "FTPToBlobCopy",
            "inputs": [{
                "name": "FtpFileInput"
            }],
            "outputs": [{
                "name": "AzureBlobOutput"
            }],
            "type": "Copy",
            "typeProperties": {
                "source": {
                    "type": "FileSystemSource"
                },
                "sink": {
                    "type": "BlobSink"
                }
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "policy": {
                "concurrency": 1,
                "executionPriorityOrder": "NewestFirst",
                "retry": 1,
                "timeout": "00:05:00"
            }
        }],
        "start": "2016-08-24T18:00:00",
        "end": "2016-08-24T19:00:00"
    }
}
```

詳細については、[FTP コネクタ](data-factory-ftp-connector.md#copy-activity-properties)に関する記事を参照してください。


## <a name="hdfs"></a>HDFS

### <a name="linked-service"></a>リンクされたサービス
HDFS のリンクされたサービスを定義するには、リンクされたサービスの **type** を **Hdfs** に設定し、**typeProperties** セクションで以下のプロパティを指定します。

| プロパティ | 説明 | 必須 |
| --- | --- | --- |
| type |type プロパティは、次のように設定する必要があります:**Hdfs** |はい |
| url |HDFS への URL |はい |
| authenticationType |Anonymous または Basic。 <br><br> HDFS コネクタに **Kerberos 認証**を使用するには、こちらのセクションを参照して、オンプレミス環境を設定します。 |はい |
| userName |Windows 認証のユーザー名。 |あり (Windows 認証用) |
| password |Windows 認証のパスワード。 |あり (Windows 認証用) |
| gatewayName |Data Factory サービスが、HDFS への接続に使用するゲートウェイの名前。 |はい |
| encryptedCredential |アクセス資格情報の [New-AzDataFactoryEncryptValue](https://docs.microsoft.com/powershell/module/az.datafactory/new-azdatafactoryencryptvalue) 出力。 |いいえ |

#### <a name="example-using-anonymous-authentication"></a>例:匿名認証を使用する

```json
{
    "name": "HDFSLinkedService",
    "properties": {
        "type": "Hdfs",
        "typeProperties": {
            "authenticationType": "Anonymous",
            "userName": "hadoop",
            "url": "http://<machine>:50070/webhdfs/v1/",
            "gatewayName": "<onpremgateway>"
        }
    }
}
```

#### <a name="example-using-windows-authentication"></a>例:Windows 認証を使用する

```json
{
    "name": "HDFSLinkedService",
    "properties": {
        "type": "Hdfs",
        "typeProperties": {
            "authenticationType": "Windows",
            "userName": "Administrator",
            "password": "password",
            "url": "http://<machine>:50070/webhdfs/v1/",
            "gatewayName": "<onpremgateway>"
        }
    }
}
```

詳細については、HDFS コネクタに関する記事を参照してください。

### <a name="dataset"></a>Dataset
HDFS データセットを定義するには、データセットの **type** を **FileShare** に設定し、**typeProperties** セクションで以下のプロパティを指定します。

| プロパティ | 説明 | 必須 |
| --- | --- | --- |
| folderPath |フォルダーへのパス。 例: `myfolder`<br/><br/>文字列内の特殊文字にはエスケープ文字 "\" を使用します。 例: folder\subfolder には、folder\\\\subfolder を指定し、d:\samplefolder には、d:\\\\samplefolder を指定します。<br/><br/>このプロパティを **partitionBy** と組み合わせて、スライスの開始/終了日時に基づくフォルダー パスを使用できます。 |はい |
| fileName |テーブルでフォルダー内の特定のファイルを参照するには、**folderPath** にファイルの名前を指定します。 このプロパティの値を設定しない場合、テーブルはフォルダー内のすべてのファイルを参照します。<br/><br/>出力データセットに fileName が指定されていない場合、生成されるファイルの名前は次の形式になります。 <br/><br/>`Data.<Guid>.txt` (例:Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt |いいえ |
| partitionedBy |partitionedBy を使用して時系列データに動的な folderPath と fileName を指定できます。 例: 1 時間ごとのデータに対して folderPath がパラメーター化されます。 |いいえ |
| format | 次の種類の形式がサポートされます:**TextFormat**、**JsonFormat**、**AvroFormat**、**OrcFormat**、**ParquetFormat** です。 形式の **type** プロパティをいずれかの値に設定します。 詳細については、[Text Format](data-factory-supported-file-and-compression-formats.md#text-format)、[Json Format](data-factory-supported-file-and-compression-formats.md#json-format)、[Avro Format](data-factory-supported-file-and-compression-formats.md#avro-format)、[Orc Format](data-factory-supported-file-and-compression-formats.md#orc-format)、[Parquet Format](data-factory-supported-file-and-compression-formats.md#parquet-format) の各セクションを参照してください。 <br><br> ファイルベースのストア間で**ファイルをそのままコピー** (バイナリ コピー) する場合は、入力と出力の両方のデータセット定義で format セクションをスキップします。 |いいえ |
| compression | データの圧縮の種類とレベルを指定します。 サポートされる種類は、**GZip**、**Deflate**、**BZip2**、**ZipDeflate** です。 サポートされるレベルは、**Optimal** と **Fastest** です。 詳細については、「[Azure Data Factory のファイル形式と圧縮形式](data-factory-supported-file-and-compression-formats.md#compression-support)」を参照してください。 |いいえ |

> [!NOTE]
> fileName と fileFilter は、同時に使用することができません。

#### <a name="example"></a>例

```json
{
    "name": "InputDataset",
    "properties": {
        "type": "FileShare",
        "linkedServiceName": "HDFSLinkedService",
        "typeProperties": {
            "folderPath": "DataTransfer/UnitTest/"
        },
        "external": true,
        "availability": {
            "frequency": "Hour",
            "interval": 1
        }
    }
}
```

詳細については、HDFS コネクタに関する記事を参照してください。

### <a name="file-system-source-in-copy-activity"></a>コピー アクティビティのファイル システム ソース
HDFS からデータをコピーする場合は、コピー アクティビティの **source type** を **FileSystemSource** に設定し、**source** セクションで以下のプロパティを指定します。

**FileSystemSource** では次のプロパティがサポートされます。

| プロパティ | 説明 | 使用できる値 | 必須 |
| --- | --- | --- | --- |
| recursive |データをサブ フォルダーから再帰的に読み取るか、指定したフォルダーからのみ読み取るかを指定します。 |True、False (既定値) |いいえ |

#### <a name="example"></a>例

```json
{
    "name": "pipeline",
    "properties": {
        "activities": [{
            "name": "HdfsToBlobCopy",
            "inputs": [{
                "name": "InputDataset"
            }],
            "outputs": [{
                "name": "OutputDataset"
            }],
            "type": "Copy",
            "typeProperties": {
                "source": {
                    "type": "FileSystemSource"
                },
                "sink": {
                    "type": "BlobSink"
                }
            },
            "policy": {
                "concurrency": 1,
                "executionPriorityOrder": "NewestFirst",
                "retry": 1,
                "timeout": "00:05:00"
            }
        }],
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-01T19:00:00"
    }
}
```

詳細については、HDFS コネクタに関する記事を参照してください。

## <a name="sftp"></a>SFTP


### <a name="linked-service"></a>リンクされたサービス
SFTP のリンクされたサービスを定義するには、リンクされたサービスの **type** を **Sftp** に設定し、**typeProperties** セクションで以下のプロパティを指定します。

| プロパティ | 説明 | 必須 |
| --- | --- | --- |
| host | SFTP サーバーの名前または IP アドレス。 |はい |
| port |SFTP サーバーがリッスンしているポート。 既定値は21 |いいえ |
| authenticationType |認証の種類を指定します。 使用できる値は以下の通りです。**Basic**、**SshPublicKey**。 <br><br> プロパティと JSON サンプルの詳細については、「基本認証を使用する」セクションと「[SSH 公開キー認証を使用する](#using-ssh-public-key-authentication)」セクションをそれぞれ参照してください。 |はい |
| skipHostKeyValidation | ホスト キーの検証をスキップするかどうかを指定します。 | いいえ。 既定値: false |
| hostKeyFingerprint | ホスト キーの指紋を指定します。 | はい (`skipHostKeyValidation` が false に設定されている場合)。  |
| gatewayName |オンプレミスの SFTP サーバーに接続するための Data Management Gateway の名前。 | はい (オンプレミスの SFTP サーバーからデータをコピーする場合)。 |
| encryptedCredential | SFTP サーバーにアクセスするための暗号化された資格情報。 コピー ウィザードまたは ClickOnce ポップアップ ダイアログで、基本認証 (ユーザー名とパスワード) または SshPublicKey 認証 (ユーザー名と秘密キーのパスまたはコンテンツ) を指定すると自動生成されます。 | いいえ。 オンプレミスの SFTP サーバーからデータをコピーする場合にのみ適用します。 |

#### <a name="example-using-basic-authentication"></a>例:基本認証を使用する

基本認証を使用するには、`authenticationType` を `Basic` に設定し、前のセクションで導入した一般的な SFTP コネクタ プロパティのほかに、次のプロパティを指定します。

| プロパティ | 説明 | 必須 |
| --- | --- | --- |
| username | SFTP サーバーにアクセスできるユーザー。 |はい |
| password | ユーザー (username) のパスワード。 | はい |

```json
{
    "name": "SftpLinkedService",
    "properties": {
        "type": "Sftp",
        "typeProperties": {
            "host": "<SFTP server name or IP address>",
            "port": 22,
            "authenticationType": "Basic",
            "username": "xxx",
            "password": "xxx",
            "skipHostKeyValidation": false,
            "hostKeyFingerPrint": "ssh-rsa 2048 xx:00:00:00:xx:00:x0:0x:0x:0x:0x:00:00:x0:x0:00",
            "gatewayName": "<onpremgateway>"
        }
    }
}
```

#### <a name="example-basic-authentication-with-encrypted-credential"></a>例:**資格情報が暗号化された基本認証**

```json
{
    "name": "SftpLinkedService",
    "properties": {
        "type": "Sftp",
        "typeProperties": {
            "host": "<FTP server name or IP address>",
            "port": 22,
            "authenticationType": "Basic",
            "username": "xxx",
            "encryptedCredential": "xxxxxxxxxxxxxxxxx",
            "skipHostKeyValidation": false,
            "hostKeyFingerPrint": "ssh-rsa 2048 xx:00:00:00:xx:00:x0:0x:0x:0x:0x:00:00:x0:x0:00",
            "gatewayName": "<onpremgateway>"
        }
    }
}
```

#### <a name="using-ssh-public-key-authentication"></a>**SSH 公開キー認証を使用する:**

基本認証を使用するには、`authenticationType` を `SshPublicKey` に設定し、前のセクションで導入した一般的な SFTP コネクタ プロパティのほかに、次のプロパティを指定します。

| プロパティ | 説明 | 必須 |
| --- | --- | --- |
| username |SFTP サーバーにアクセスできるユーザー |はい |
| privateKeyPath | ゲートウェイがアクセスできる秘密キー ファイルへの絶対パスを指定します。 | `privateKeyPath` または `privateKeyContent` を指定します。 <br><br> オンプレミスの SFTP サーバーからデータをコピーする場合にのみ適用します。 |
| privateKeyContent | 秘密キーのコンテンツのシリアル化された文字列。 コピー ウィザードでは、秘密キー ファイルを読み取り、秘密キーのコンテンツを自動的に抽出できます。 その他のツール/SDK を使用している場合は、代わりに privateKeyPath プロパティを使用します。 | `privateKeyPath` または `privateKeyContent` を指定します。 |
| passPhrase | キー ファイルがパス フレーズで保護されている場合は、パス フレーズ/パスワードを指定して、秘密キーを復号化します。 | はい (秘密キー ファイルがパス フレーズで保護されている場合)。 |

```json
{
    "name": "SftpLinkedServiceWithPrivateKeyPath",
    "properties": {
        "type": "Sftp",
        "typeProperties": {
            "host": "<FTP server name or IP address>",
            "port": 22,
            "authenticationType": "SshPublicKey",
            "username": "xxx",
            "privateKeyPath": "D:\\privatekey_openssh",
            "passPhrase": "xxx",
            "skipHostKeyValidation": true,
            "gatewayName": "<onpremgateway>"
        }
    }
}
```

#### <a name="example-sshpublickey-authentication-using-private-key-content"></a>例:**秘密キーのコンテンツを使用した SshPublicKey 認証**

```json
{
    "name": "SftpLinkedServiceWithPrivateKeyContent",
    "properties": {
        "type": "Sftp",
        "typeProperties": {
            "host": "mysftpserver.westus.cloudapp.azure.com",
            "port": 22,
            "authenticationType": "SshPublicKey",
            "username": "xxx",
            "privateKeyContent": "<base64 string of the private key content>",
            "passPhrase": "xxx",
            "skipHostKeyValidation": true
        }
    }
}
```

詳細については、[SFTP コネクタ](data-factory-sftp-connector.md#linked-service-properties)に関する記事を参照してください。

### <a name="dataset"></a>Dataset
SFTP データセットを定義するには、データセットの **type** を **FileShare** に設定し、**typeProperties** セクションで以下のプロパティを指定します。

| プロパティ | 説明 | 必須 |
| --- | --- | --- |
| folderPath |フォルダーへのサブ パス。 文字列内の特殊文字にはエスケープ文字 "\" を使用します。 例については、「サンプルのリンクされたサービスとデータセットの定義」を参照してください。<br/><br/>このプロパティを **partitionBy** と組み合わせて、スライスの開始/終了日時に基づくフォルダー パスを使用できます。 |はい |
| fileName |テーブルでフォルダー内の特定のファイルを参照するには、**folderPath** にファイルの名前を指定します。 このプロパティの値を設定しない場合、テーブルはフォルダー内のすべてのファイルを参照します。<br/><br/>出力データセットに fileName が指定されていない場合、生成されるファイルの名前は次の形式になります。 <br/><br/>`Data.<Guid>.txt` (例: Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt) |いいえ |
| fileFilter |すべてのファイルではなく、folderPath 内のファイルのサブセットを選択するために使用するフィルターを指定します。<br/><br/>使用可能な値: `*` (複数の文字) および `?` (単一の文字)。<br/><br/>例 1: `"fileFilter": "*.log"`<br/>例 2: `"fileFilter": 2016-1-?.txt"`<br/><br/> fileFilter は FileShare 入力データセットに適用されます。 このプロパティは、HDFS ではサポートされません。 |いいえ |
| partitionedBy |partitionedBy を使用して時系列データに動的な folderPath と fileName を指定できます。 たとえば、1 時間ごとのデータに対して folderPath がパラメーター化されます。 |いいえ |
| format | 次の種類の形式がサポートされます:**TextFormat**、**JsonFormat**、**AvroFormat**、**OrcFormat**、**ParquetFormat** です。 形式の **type** プロパティをいずれかの値に設定します。 詳細については、[Text Format](data-factory-supported-file-and-compression-formats.md#text-format)、[Json Format](data-factory-supported-file-and-compression-formats.md#json-format)、[Avro Format](data-factory-supported-file-and-compression-formats.md#avro-format)、[Orc Format](data-factory-supported-file-and-compression-formats.md#orc-format)、[Parquet Format](data-factory-supported-file-and-compression-formats.md#parquet-format) の各セクションを参照してください。 <br><br> ファイルベースのストア間で**ファイルをそのままコピー** (バイナリ コピー) する場合は、入力と出力の両方のデータセット定義で format セクションをスキップします。 |いいえ |
| compression | データの圧縮の種類とレベルを指定します。 サポートされる種類は、**GZip**、**Deflate**、**BZip2**、**ZipDeflate** です。 サポートされるレベルは、**Optimal** と **Fastest** です。 詳細については、「[Azure Data Factory のファイル形式と圧縮形式](data-factory-supported-file-and-compression-formats.md#compression-support)」を参照してください。 |いいえ |
| useBinaryTransfer |バイナリ転送モードを使用するかどうかを指定します。 バイナリ モードの場合は true、ASCII モードの場合は false です。 既定値:True。 このプロパティを使用できるのは、関連するリンクされたサービスの種類が FtpServer である場合だけです。 |いいえ |

> [!NOTE]
> fileName と fileFilter は、同時に使用することができません。

#### <a name="example"></a>例

```json
{
    "name": "SFTPFileInput",
    "properties": {
        "type": "FileShare",
        "linkedServiceName": "SftpLinkedService",
        "typeProperties": {
            "folderPath": "<path to shared folder>",
            "fileName": "test.csv"
        },
        "external": true,
        "availability": {
            "frequency": "Hour",
            "interval": 1
        }
    }
}
```

詳細については、[SFTP コネクタ](data-factory-sftp-connector.md#dataset-properties)に関する記事を参照してください。

### <a name="file-system-source-in-copy-activity"></a>コピー アクティビティのファイル システム ソース
SFTP ソースからデータをコピーする場合は、コピー アクティビティの **source type** を **FileSystemSource** に設定し、**source** セクションで以下のプロパティを指定します。

| プロパティ | 説明 | 使用できる値 | 必須 |
| --- | --- | --- | --- |
| recursive |データをサブ フォルダーから再帰的に読み取るか、指定したフォルダーからのみ読み取るかを指定します。 |True、False (既定値) |いいえ |



#### <a name="example"></a>例

```json
{
    "name": "pipeline",
    "properties": {
        "activities": [{
            "name": "SFTPToBlobCopy",
            "inputs": [{
                "name": "SFTPFileInput"
            }],
            "outputs": [{
                "name": "AzureBlobOutput"
            }],
            "type": "Copy",
            "typeProperties": {
                "source": {
                    "type": "FileSystemSource"
                },
                "sink": {
                    "type": "BlobSink"
                }
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "policy": {
                "concurrency": 1,
                "executionPriorityOrder": "NewestFirst",
                "retry": 1,
                "timeout": "00:05:00"
            }
        }],
        "start": "2017-02-20T18:00:00",
        "end": "2017-02-20T19:00:00"
    }
}
```

詳細については、[SFTP コネクタ](data-factory-sftp-connector.md#copy-activity-properties)に関する記事を参照してください。


## <a name="http"></a>HTTP

### <a name="linked-service"></a>リンクされたサービス
HTTP のリンクされたサービスを定義するには、リンクされたサービスの **type** を **Http** に設定し、**typeProperties** セクションで以下のプロパティを指定します。

| プロパティ | 説明 | 必須 |
| --- | --- | --- |
| url | Web サーバーへのベース URL | はい |
| authenticationType | 認証の種類を指定します。 使用できる値は、以下のとおりです。**Anonymous**、**Basic**、**Digest**、**Windows**、**ClientCertificate**。 <br><br> これらの認証の種類それぞれのプロパティと JSON の使用例については、この表の後のセクションを参照してください。 | はい |
| enableServerCertificateValidation | ソースが HTTPS Web サーバーである場合に、サーバーの SSL 証明書の検証を有効にするかどうかを指定します。 | いいえ。既定値は true です。 |
| gatewayName | オンプレミスの HTTP ソースに接続するための Data Management Gateway の名前。 | はい (オンプレミスの HTTP ソースからデータをコピーする場合)。 |
| encryptedCredential | HTTP エンドポイントにアクセスするための暗号化された資格情報。 コピー ウィザードまたは ClickOnce ポップアップ ダイアログで認証情報を構成すると自動生成されます。 | いいえ。 オンプレミスの HTTP サーバーからデータをコピーする場合にのみ適用します。 |

#### <a name="example-using-basic-digest-or-windows-authentication"></a>例:基本、ダイジェスト、または Windows 認証の使用
`authenticationType` を `Basic`、`Digest`、または `Windows` として設定し、上で紹介した HTTP コネクタの一般的なプロパティのほかに、次のプロパティを指定します。

| プロパティ | 説明 | 必須 |
| --- | --- | --- |
| username | HTTP エンドポイントにアクセスするためのユーザー名。 | はい |
| password | ユーザー (username) のパスワード。 | はい |

```json
{
    "name": "HttpLinkedService",
    "properties": {
        "type": "Http",
        "typeProperties": {
            "authenticationType": "basic",
            "url": "https://en.wikipedia.org/wiki/",
            "userName": "user name",
            "password": "password"
        }
    }
}
```

#### <a name="example-using-clientcertificate-authentication"></a>例:クライアント証明書認証の使用

基本認証を使用するには、`authenticationType` を `ClientCertificate` として設定し、上で紹介した HTTP コネクタの一般的なプロパティのほかに、次のプロパティを指定します。

| プロパティ | 説明 | 必須 |
| --- | --- | --- |
| embeddedCertData | Personal Information Exchange (PFX) ファイルのバイナリ データの Base64 でエンコードされたコンテンツ。 | `embeddedCertData` または `certThumbprint` を指定します。 |
| certThumbprint | ゲートウェイ コンピューターの証明書ストアにインストールされた証明書の拇印。 オンプレミスの HTTP ソースからデータをコピーする場合にのみ適用します。 | `embeddedCertData` または `certThumbprint` を指定します。 |
| password | 証明書に関連付けられているパスワード。 | いいえ |

認証に `certThumbprint` を使用し、証明書がローカル コンピューターの個人用ストアにインストールされている場合は、ゲートウェイ サービスに読み取りアクセス許可を付与する必要があります。

1. Microsoft 管理コンソール (MMC) を起動します。 **ローカル コンピューター**を対象とする **[証明書]** スナップインを追加します。
2. **[証明書]** 、 **[個人]** の順に展開し、 **[証明書]** をクリックします。
3. 個人用ストアの証明書を右クリックし、 **[すべてのタスク]** -> **[秘密キーの管理]** の順に選択します。
3. **[セキュリティ]** タブで、証明書に対する読み取りアクセス権を使用して Data Management Gateway Host Service を実行しているユーザー アカウントを追加します。

**例: クライアント証明書の使用**このリンクされたサービスは、データ ファクトリをオンプレミスの HTTP Web サーバーにリンクします。 Data Management Gateway がインストールされているコンピューターにインストールされているクライアント証明書を使用します。

```json
{
    "name": "HttpLinkedService",
    "properties": {
        "type": "Http",
        "typeProperties": {
            "authenticationType": "ClientCertificate",
            "url": "https://en.wikipedia.org/wiki/",
            "certThumbprint": "thumbprint of certificate",
            "gatewayName": "gateway name"
        }
    }
}
```

#### <a name="example-using-client-certificate-in-a-file"></a>例: ファイル内のクライアント証明書を使用する
このリンクされたサービスは、データ ファクトリをオンプレミスの HTTP Web サーバーにリンクします。 Data Management Gateway がインストールされているコンピューターのクライアント証明書ファイルを使用します。

```json
{
    "name": "HttpLinkedService",
    "properties": {
        "type": "Http",
        "typeProperties": {
            "authenticationType": "ClientCertificate",
            "url": "https://en.wikipedia.org/wiki/",
            "embeddedCertData": "base64 encoded cert data",
            "password": "password of cert"
        }
    }
}
```

詳細については、[HTTP コネクタ](data-factory-http-connector.md#linked-service-properties)に関する記事を参照してください。

### <a name="dataset"></a>Dataset
HTTP データセットを定義するには、データセットの **type** を **Http** に設定し、**typeProperties** セクションで以下のプロパティを指定します。

| プロパティ | 説明 | 必須 |
|:--- |:--- |:--- |
| relativeUrl | データを含むリソースへの相対 URL。 パスが指定されていないとき、リンクされたサービス定義に指定されている URL のみだけが使用されます。 <br><br> 動的 URL を構築するには、[データ ファクトリ関数とシステム変数](data-factory-functions-variables.md)を利用できます。たとえば、`"relativeUrl": "$$Text.Format('/my/report?month={0:yyyy}-{0:MM}&fmt=csv', SliceStart)"` となります。 | いいえ |
| requestMethod | Http メソッド。 使用できる値は、**GET** または **POST** です。 | いいえ。 既定値は `GET` です。 |
| additionalHeaders | 追加の HTTP 要求ヘッダー。 | いいえ |
| requestBody | HTTP 要求の本文。 | いいえ |
| format | **HTTP エンドポイントからデータをそのまま取得する**だけで、解析しない場合は、この形式の設定を省略してください。 <br><br> コピー中に HTTP 応答の内容を解析する場合にサポートされる形式の種類は、**TextFormat**、**JsonFormat**、**AvroFormat**、**OrcFormat**、**ParquetFormat** です。 詳細については、[Text Format](data-factory-supported-file-and-compression-formats.md#text-format)、[Json Format](data-factory-supported-file-and-compression-formats.md#json-format)、[Avro Format](data-factory-supported-file-and-compression-formats.md#avro-format)、[Orc Format](data-factory-supported-file-and-compression-formats.md#orc-format)、[Parquet Format](data-factory-supported-file-and-compression-formats.md#parquet-format) の各セクションを参照してください。 |いいえ |
| compression | データの圧縮の種類とレベルを指定します。 サポートされる種類は、**GZip**、**Deflate**、**BZip2**、**ZipDeflate** です。 サポートされるレベルは、**Optimal** と **Fastest** です。 詳細については、「[Azure Data Factory のファイル形式と圧縮形式](data-factory-supported-file-and-compression-formats.md#compression-support)」を参照してください。 |いいえ |

#### <a name="example-using-the-get-default-method"></a>例: GET (既定) メソッドを使用する

```json
{
    "name": "HttpSourceDataInput",
    "properties": {
        "type": "Http",
        "linkedServiceName": "HttpLinkedService",
        "typeProperties": {
            "relativeUrl": "XXX/test.xml",
            "additionalHeaders": "Connection: keep-alive\nUser-Agent: Mozilla/5.0\n"
        },
        "external": true,
        "availability": {
            "frequency": "Hour",
            "interval": 1
        }
    }
}
```

#### <a name="example-using-the-post-method"></a>例: POST メソッドを使用する

```json
{
    "name": "HttpSourceDataInput",
    "properties": {
        "type": "Http",
        "linkedServiceName": "HttpLinkedService",
        "typeProperties": {
            "relativeUrl": "/XXX/test.xml",
            "requestMethod": "Post",
            "requestBody": "body for POST HTTP request"
        },
        "external": true,
        "availability": {
            "frequency": "Hour",
            "interval": 1
        }
    }
}
```
詳細については、[HTTP コネクタ](data-factory-http-connector.md#dataset-properties)に関する記事を参照してください。

### <a name="http-source-in-copy-activity"></a>コピー アクティビティの HTTP ソース
HTTP ソースからデータをコピーする場合は、コピー アクティビティの **source type** を **HttpSource** に設定し、**source** セクションで以下のプロパティを指定します。

| プロパティ | 説明 | 必須 |
| -------- | ----------- | -------- |
| httpRequestTimeout | HTTP 要求が応答を取得する際のタイムアウト (TimeSpan)。 応答データの読み取りのタイムアウトではなく、応答の取得のタイムアウトです。 | いいえ。 既定値:00:01:40 |


#### <a name="example"></a>例

```json
{
    "name": "SamplePipeline",
    "properties": {
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-01T19:00:00",
        "description": "pipeline with copy activity",
        "activities": [{
            "name": "HttpSourceToAzureBlob",
            "description": "Copy from an HTTP source to an Azure blob",
            "type": "Copy",
            "inputs": [{
                "name": "HttpSourceDataInput"
            }],
            "outputs": [{
                "name": "AzureBlobOutput"
            }],
            "typeProperties": {
                "source": {
                    "type": "HttpSource"
                },
                "sink": {
                    "type": "BlobSink"
                }
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "policy": {
                "concurrency": 1,
                "executionPriorityOrder": "OldestFirst",
                "retry": 0,
                "timeout": "01:00:00"
            }
        }]
    }
}
```

詳細については、[HTTP コネクタ](data-factory-http-connector.md#copy-activity-properties)に関する記事を参照してください。

## <a name="odata"></a>OData

### <a name="linked-service"></a>リンクされたサービス
OData のリンクされたサービスを定義するには、リンクされたサービスの **type** を **OData** に設定し、**typeProperties** セクションで以下のプロパティを指定します。

| プロパティ | 説明 | 必須 |
| --- | --- | --- |
| url |OData サービスの URL です。 |はい |
| authenticationType |OData ソースへの接続に使用される認証の種類です。 <br/><br/> クラウド OData の場合、有効な値は、匿名、基本、または OAuth です (Azure Data Factory で現在サポートされているのは Azure Active Directory ベースの OAuth のみです)。 <br/><br/> オンプレミスの OData では、Anonymous、Basic、Windows のいずれかの値になります。 |はい |
| username |基本認証を使用している場合は、ユーザー名を指定します。 |はい (基本認証を使用している場合のみ) |
| password |ユーザー名に指定したユーザー アカウントのパスワードを指定します。 |はい (基本認証を使用している場合のみ) |
| authorizedCredential |OAuth を使用している場合は、Data Factory コピー ウィザードまたはエディターの **[承認]** ボタンをクリックして資格情報を入力すると、このプロパティの値が自動生成されます。 |はい (OAuth 認証を使用している場合のみ) |
| gatewayName |Data Factory サービスが、オンプレミスの OData サービスへの接続に使用するゲートウェイの名前。 オンプレミスの OData ソースからデータをコピーする場合にのみ指定します。 |いいえ |

#### <a name="example---using-basic-authentication"></a>例: 基本認証を使用する
```json
{
    "name": "inputLinkedService",
    "properties": {
        "type": "OData",
        "typeProperties": {
            "url": "https://services.odata.org/OData/OData.svc",
            "authenticationType": "Basic",
            "username": "username",
            "password": "password"
        }
    }
}
```

#### <a name="example---using-anonymous-authentication"></a>例: 匿名認証を使用する

```json
{
    "name": "ODataLinkedService",
    "properties": {
        "type": "OData",
        "typeProperties": {
            "url": "https://services.odata.org/OData/OData.svc",
            "authenticationType": "Anonymous"
        }
    }
}
```

#### <a name="example---using-windows-authentication-accessing-on-premises-odata-source"></a>例: Windows 認証を使用してオンプレミスの OData ソースにアクセスする

```json
{
    "name": "inputLinkedService",
    "properties": {
        "type": "OData",
        "typeProperties": {
            "url": "<endpoint of on-premises OData source, for example, Dynamics CRM>",
            "authenticationType": "Windows",
            "username": "domain\\user",
            "password": "password",
            "gatewayName": "<onpremgateway>"
        }
    }
}
```

#### <a name="example---using-oauth-authentication-accessing-cloud-odata-source"></a>例: OAuth 認証を使用してクラウド OData ソースにアクセスする
```json
{
    "name": "inputLinkedService",
    "properties":
    {
        "type": "OData",
            "typeProperties":
        {
            "url": "<endpoint of cloud OData source, for example, https://<tenant>.crm.dynamics.com/XRMServices/2011/OrganizationData.svc>",
            "authenticationType": "OAuth",
            "authorizedCredential": "<auto generated by clicking the Authorize button on UI>"
        }
    }
}
```

詳細については、[OData コネクタ](data-factory-odata-connector.md#linked-service-properties)に関する記事を参照してください。

### <a name="dataset"></a>Dataset
OData データセットを定義するには、データセットの **type** を **ODataResource** に設定し、**typeProperties** セクションで以下のプロパティを指定します。

| プロパティ | 説明 | 必須 |
| --- | --- | --- |
| path |OData リソースへのパス |いいえ |

#### <a name="example"></a>例

```json
{
    "name": "ODataDataset",
    "properties": {
        "type": "ODataResource",
        "typeProperties": {
            "path": "Products"
        },
        "linkedServiceName": "ODataLinkedService",
        "structure": [],
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": true,
        "policy": {
            "retryInterval": "00:01:00",
            "retryTimeout": "00:10:00",
            "maximumRetry": 3
        }
    }
}
```

詳細については、[OData コネクタ](data-factory-odata-connector.md#dataset-properties)に関する記事を参照してください。

### <a name="relational-source-in-copy-activity"></a>コピー アクティビティのリレーショナル ソース
OData ソースからデータをコピーする場合は、コピー アクティビティの **source type** を **RelationalSource** に設定し、**source** セクションで以下のプロパティを指定します。

| プロパティ | 説明 | 例 | 必須 |
| --- | --- | --- | --- |
| query |カスタム クエリを使用してデータを読み取ります。 |"?$select=Name, Description&$top=5" |いいえ |

#### <a name="example"></a>例

```json
{
    "name": "CopyODataToBlob",
    "properties": {
        "description": "pipeline for copy activity",
        "activities": [{
            "type": "Copy",
            "typeProperties": {
                "source": {
                    "type": "RelationalSource",
                    "query": "?$select=Name, Description&$top=5"
                },
                "sink": {
                    "type": "BlobSink",
                    "writeBatchSize": 0,
                    "writeBatchTimeout": "00:00:00"
                }
            },
            "inputs": [{
                "name": "ODataDataSet"
            }],
            "outputs": [{
                "name": "AzureBlobODataDataSet"
            }],
            "policy": {
                "timeout": "01:00:00",
                "concurrency": 1
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "name": "ODataToBlob"
        }],
        "start": "2017-02-01T18:00:00",
        "end": "2017-02-03T19:00:00"
    }
}
```

詳細については、[OData コネクタ](data-factory-odata-connector.md#copy-activity-properties)に関する記事を参照してください。


## <a name="odbc"></a>ODBC


### <a name="linked-service"></a>リンクされたサービス
ODBC のリンクされたサービスを定義するには、リンクされたサービスの **type** を **OnPremisesOdbc** に設定し、**typeProperties** セクションで以下のプロパティを指定します。

| プロパティ | 説明 | 必須 |
| --- | --- | --- |
| connectionString |接続文字列の非アクセス資格情報部分と省略可能な暗号化された資格情報。 次のセクションの例を参照してください。 |はい |
| credential |ドライバー固有のプロパティ値の形式で指定された接続文字列のアクセス資格情報の部分。 例: `“Uid=<user ID>;Pwd=<password>;RefreshToken=<secret refresh token>;”.` |いいえ |
| authenticationType |ODBC データ ストアへの接続に使用される認証の種類です。 次のいずれかの値になります。Anonymous および Basic。 |はい |
| username |基本認証を使用している場合は、ユーザー名を指定します。 |いいえ |
| password |ユーザー名に指定したユーザー アカウントのパスワードを指定します。 |いいえ |
| gatewayName |Data Factory サービスが、ODBC データ ストアへの接続に使用するゲートウェイの名前。 |はい |

#### <a name="example---using-basic-authentication"></a>例: 基本認証を使用する

```json
{
    "name": "ODBCLinkedService",
    "properties": {
        "type": "OnPremisesOdbc",
        "typeProperties": {
            "authenticationType": "Basic",
            "connectionString": "Driver={SQL Server};Server=Server.database.windows.net; Database=TestDatabase;",
            "userName": "username",
            "password": "password",
            "gatewayName": "<onpremgateway>"
        }
    }
}
```
#### <a name="example---using-basic-authentication-with-encrypted-credentials"></a>例: 暗号化された資格情報で基本認証を使用する
[New-AzDataFactoryEncryptValue](https://docs.microsoft.com/powershell/module/az.datafactory/new-azdatafactoryencryptvalue) コマンドレットを使用して資格情報を暗号化できます。

```json
{
    "name": "ODBCLinkedService",
    "properties": {
        "type": "OnPremisesOdbc",
        "typeProperties": {
            "authenticationType": "Basic",
            "connectionString": "Driver={SQL Server};Server=myserver.database.windows.net; Database=TestDatabase;;EncryptedCredential=eyJDb25uZWN0...........................",
            "gatewayName": "<onpremgateway>"
        }
    }
}
```

#### <a name="example-using-anonymous-authentication"></a>例:匿名認証を使用する

```json
{
    "name": "ODBCLinkedService",
    "properties": {
        "type": "OnPremisesOdbc",
        "typeProperties": {
            "authenticationType": "Anonymous",
            "connectionString": "Driver={SQL Server};Server={servername}.database.windows.net; Database=TestDatabase;",
            "credential": "UID={uid};PWD={pwd}",
            "gatewayName": "<onpremgateway>"
        }
    }
}
```

詳細については、[ODBC コネクタ](data-factory-odbc-connector.md#linked-service-properties)に関する記事を参照してください。

### <a name="dataset"></a>Dataset
ODBC データセットを定義するには、データセットの **type** を **RelationalTable** に設定し、**typeProperties** セクションで以下のプロパティを指定します。

| プロパティ | 説明 | 必須 |
| --- | --- | --- |
| tableName |ODBC データ ストア内のテーブルの名前。 |はい |


#### <a name="example"></a>例

```json
{
    "name": "ODBCDataSet",
    "properties": {
        "type": "RelationalTable",
        "linkedServiceName": "ODBCLinkedService",
        "typeProperties": {},
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": true,
        "policy": {
            "externalData": {
                "retryInterval": "00:01:00",
                "retryTimeout": "00:10:00",
                "maximumRetry": 3
            }
        }
    }
}
```

詳細については、[ODBC コネクタ](data-factory-odbc-connector.md#dataset-properties)に関する記事を参照してください。

### <a name="relational-source-in-copy-activity"></a>コピー アクティビティのリレーショナル ソース
ODBC データ ストアからデータをコピーする場合は、コピー アクティビティの **source type** を **RelationalSource** に設定し、**source** セクションで以下のプロパティを指定します。

| プロパティ | 説明 | 使用できる値 | 必須 |
| --- | --- | --- | --- |
| query |カスタム クエリを使用してデータを読み取ります。 |SQL クエリ文字列。 (例: `select * from MyTable`)。 |はい |

#### <a name="example"></a>例

```json
{
    "name": "CopyODBCToBlob",
    "properties": {
        "description": "pipeline for copy activity",
        "activities": [{
            "type": "Copy",
            "typeProperties": {
                "source": {
                    "type": "RelationalSource",
                    "query": "$$Text.Format('select * from MyTable where timestamp >= \\'{0:yyyy-MM-ddTHH:mm:ss}\\' AND timestamp < \\'{1:yyyy-MM-ddTHH:mm:ss}\\'', WindowStart, WindowEnd)"
                },
                "sink": {
                    "type": "BlobSink",
                    "writeBatchSize": 0,
                    "writeBatchTimeout": "00:00:00"
                }
            },
            "inputs": [{
                "name": "OdbcDataSet"
            }],
            "outputs": [{
                "name": "AzureBlobOdbcDataSet"
            }],
            "policy": {
                "timeout": "01:00:00",
                "concurrency": 1
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "name": "OdbcToBlob"
        }],
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-01T19:00:00"
    }
}
```

詳細については、[ODBC コネクタ](data-factory-odbc-connector.md#copy-activity-properties)に関する記事を参照してください。

## <a name="salesforce"></a>Salesforce


### <a name="linked-service"></a>リンクされたサービス
Salesforce のリンクされたサービスを定義するには、リンクされたサービスの **type** を **Salesforce** に設定し、**typeProperties** セクションで以下のプロパティを指定します。

| プロパティ | 説明 | 必須 |
| --- | --- | --- |
| environmentUrl | Salesforce インスタンスの URL を指定します。 <br><br> - 既定では "https:\//login.salesforce.com" です。 <br> - サンドボックスからデータをコピーするには、"https://test.salesforce.com" を指定します。 <br> - カスタム ドメインからデータをコピーするには、たとえば "https://[ドメイン].my.salesforce.com" を指定します。 |いいえ |
| username |ユーザー アカウントのユーザー名を指定します。 |はい |
| password |ユーザー アカウントのパスワードを指定します。 |はい |
| securityToken |ユーザー アカウントのセキュリティ トークンを指定します。 セキュリティ トークンのリセット/取得方法については、 [セキュリティ トークンの取得](https://help.salesforce.com/apex/HTViewHelpDoc?id=user_security_token.htm) に関する記事をご覧ください。 セキュリティ トークンの概要については、「[Security and the API (セキュリティと API)](https://developer.salesforce.com/docs/atlas.en-us.api.meta/api/sforce_api_concepts_security.htm)」をご覧ください。 |はい |

#### <a name="example"></a>例

```json
{
    "name": "SalesforceLinkedService",
    "properties": {
        "type": "Salesforce",
        "typeProperties": {
            "username": "<user name>",
            "password": "<password>",
            "securityToken": "<security token>"
        }
    }
}
```

詳細については、[Salesforce コネクタ](data-factory-salesforce-connector.md#linked-service-properties)に関する記事を参照してください。

### <a name="dataset"></a>Dataset
Salesforce データセットを定義するには、データセットの **type** を **RelationalTable** に設定し、**typeProperties** セクションで以下のプロパティを指定します。

| プロパティ | 説明 | 必須 |
| --- | --- | --- |
| tableName |Salesforce のテーブル名。 |いいえ (**RelationalSource** の**クエリ**が指定されている場合) |

#### <a name="example"></a>例

```json
{
    "name": "SalesforceInput",
    "properties": {
        "linkedServiceName": "SalesforceLinkedService",
        "type": "RelationalTable",
        "typeProperties": {
            "tableName": "AllDataType__c"
        },
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": true,
        "policy": {
            "externalData": {
                "retryInterval": "00:01:00",
                "retryTimeout": "00:10:00",
                "maximumRetry": 3
            }
        }
    }
}
```

詳細については、[Salesforce コネクタ](data-factory-salesforce-connector.md#dataset-properties)に関する記事を参照してください。

### <a name="relational-source-in-copy-activity"></a>コピー アクティビティのリレーショナル ソース
Salesforce からデータをコピーする場合は、コピー アクティビティの **source type** を **RelationalSource** に設定し、**source** セクションで以下のプロパティを指定します。

| プロパティ | 説明 | 使用できる値 | 必須 |
| --- | --- | --- | --- |
| query |カスタム クエリを使用してデータを読み取ります。 |SQL-92 クエリまたは [Salesforce オブジェクト クエリ言語 (SOQL)](https://developer.salesforce.com/docs/atlas.en-us.soql_sosl.meta/soql_sosl/sforce_api_calls_soql.htm) クエリ。 たとえば、「`select * from MyTable__c`」のように入力します。 |いいえ (**dataset** の **tableName** が指定されている場合) |

#### <a name="example"></a>例



```json
{
    "name": "SamplePipeline",
    "properties": {
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-01T19:00:00",
        "description": "pipeline with copy activity",
        "activities": [{
            "name": "SalesforceToAzureBlob",
            "description": "Copy from Salesforce to an Azure blob",
            "type": "Copy",
            "inputs": [{
                "name": "SalesforceInput"
            }],
            "outputs": [{
                "name": "AzureBlobOutput"
            }],
            "typeProperties": {
                "source": {
                    "type": "RelationalSource",
                    "query": "SELECT Id, Col_AutoNumber__c, Col_Checkbox__c, Col_Currency__c, Col_Date__c, Col_DateTime__c, Col_Email__c, Col_Number__c, Col_Percent__c, Col_Phone__c, Col_Picklist__c, Col_Picklist_MultiSelect__c, Col_Text__c, Col_Text_Area__c, Col_Text_AreaLong__c, Col_Text_AreaRich__c, Col_URL__c, Col_Text_Encrypt__c, Col_Lookup__c FROM AllDataType__c"
                },
                "sink": {
                    "type": "BlobSink"
                }
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "policy": {
                "concurrency": 1,
                "executionPriorityOrder": "OldestFirst",
                "retry": 0,
                "timeout": "01:00:00"
            }
        }]
    }
}
```

> [!IMPORTANT]
> カスタム オブジェクトには、API 名の "__c" の部分が必要となります。

詳細については、[Salesforce コネクタ](data-factory-salesforce-connector.md#copy-activity-properties)に関する記事を参照してください。

## <a name="web-data"></a>Web データ

### <a name="linked-service"></a>リンクされたサービス
Web のリンクされたサービスを定義するには、リンクされたサービスの **type** を **Web** に設定し、**typeProperties** セクションで以下のプロパティを指定します。

| プロパティ | 説明 | 必須 |
| --- | --- | --- |
| Url |Web ソースへの URL |はい |
| authenticationType |Anonymous |はい |


#### <a name="example"></a>例


```json
{
    "name": "web",
    "properties": {
        "type": "Web",
        "typeProperties": {
            "authenticationType": "Anonymous",
            "url": "https://en.wikipedia.org/wiki/"
        }
    }
}
```

詳細については、[Web テーブル コネクタ](data-factory-web-table-connector.md#linked-service-properties)に関する記事を参照してください。

### <a name="dataset"></a>Dataset
Web データセットを定義するには、データセットの **type** を **WebTable** に設定し、**typeProperties** セクションで以下のプロパティを指定します。

| プロパティ | 説明 | 必須 |
|:--- |:--- |:--- |
| type |データセットの型。 **データセット** |はい |
| path |テーブルを含むリソースの相対 URL。 |いいえ。 パスが指定されていないとき、リンクされたサービス定義に指定されている URL のみだけが使用されます。 |
| index |リソースのテーブルのインデックス。 HTML ページでテーブルのインデックスを取得する手順については、「HTML ページのテーブルのインデックスを取得する」セクションを参照してください。 |はい |

#### <a name="example"></a>例

```json
{
    "name": "WebTableInput",
    "properties": {
        "type": "WebTable",
        "linkedServiceName": "WebLinkedService",
        "typeProperties": {
            "index": 1,
            "path": "AFI's_100_Years...100_Movies"
        },
        "external": true,
        "availability": {
            "frequency": "Hour",
            "interval": 1
        }
    }
}
```

詳細については、[Web テーブル コネクタ](data-factory-web-table-connector.md#dataset-properties)に関する記事を参照してください。

### <a name="web-source-in-copy-activity"></a>コピー アクティビティの Web ソース
Web テーブルからデータをコピーする場合は、コピー アクティビティの **source type** を **WebSource** に設定します。 現時点では、ソースが **WebSource**型のコピー アクティビティの場合、追加プロパティはサポートされません。

#### <a name="example"></a>例

```json
{
    "name": "SamplePipeline",
    "properties": {
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-01T19:00:00",
        "description": "pipeline with copy activity",
        "activities": [{
            "name": "WebTableToAzureBlob",
            "description": "Copy from a Web table to an Azure blob",
            "type": "Copy",
            "inputs": [{
                "name": "WebTableInput"
            }],
            "outputs": [{
                "name": "AzureBlobOutput"
            }],
            "typeProperties": {
                "source": {
                    "type": "WebSource"
                },
                "sink": {
                    "type": "BlobSink"
                }
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "policy": {
                "concurrency": 1,
                "executionPriorityOrder": "OldestFirst",
                "retry": 0,
                "timeout": "01:00:00"
            }
        }]
    }
}
```

詳細については、[Web テーブル コネクタ](data-factory-web-table-connector.md#copy-activity-properties)に関する記事を参照してください。

## <a name="compute-environments"></a>コンピューティング環境
次の表は、Data Factory でサポートされているコンピューティング環境と、その環境で実行できる変換アクティビティの一覧です。 リンクされたサービスをデータ ファクトリにリンクさせるための JSON スキーマについては、目的のコンピューティング環境のリンクをクリックしてください。

| Compute 環境 | アクティビティ |
| --- | --- |
| [On-demand HDInsight クラスター](#on-demand-azure-hdinsight-cluster)または[独自の HDInsight クラスター](#existing-azure-hdinsight-cluster) |[.NET カスタム アクティビティ](#net-custom-activity)、[Hive アクティビティ](#hdinsight-hive-activity)、[Pig アクティビティ](#hdinsight-pig-activity)、[MapReduce アクティビティ](#hdinsight-mapreduce-activity)、Hadoop ストリーミング アクティビティ、[Spark アクティビティ](#hdinsight-spark-activity) |
| [Azure Batch](#azure-batch) |[.NET カスタム アクティビティ](#net-custom-activity) |
| [Azure Machine Learning](#azure-machine-learning) | [Machine Learning バッチ実行アクティビティ](#machine-learning-batch-execution-activity)、[Machine Learning 更新リソース アクティビティ](#machine-learning-update-resource-activity) |
| [Azure Data Lake Analytics](#azure-data-lake-analytics) |[Data Lake Analytics U-SQL](#data-lake-analytics-u-sql-activity) |
| [Azure SQL Database](#azure-sql-database-1)、[Azure SQL Data Warehouse](#azure-sql-data-warehouse-1)、[SQL Server](#sql-server-1) |[ストアド プロシージャ](#stored-procedure-activity) |

## <a name="on-demand-azure-hdinsight-cluster"></a>オンデマンド Azure HDInsight クラスター
Azure Data Factory サービスは、データを処理するための Windows/Linux ベースのオンデマンド HDInsight クラスターを自動的に作成します。 このクラスターはクラスターに関連付けられているストレージ アカウント (JSON の linkedServiceName プロパティ) と同じリージョンで作成されます。 このリンクされたサービスでは、変換アクティビティとして、[.NET カスタム アクティビティ](#net-custom-activity)、[Hive アクティビティ](#hdinsight-hive-activity)、[Pig アクティビティ](#hdinsight-pig-activity)、[MapReduce アクティビティ](#hdinsight-mapreduce-activity)、Hadoop ストリーミング アクティビティ、[Spark アクティビティ](#hdinsight-spark-activity)を実行することができます。

### <a name="linked-service"></a>リンクされたサービス
次の表は、オンデマンド HDInsight のリンクされたサービスの Azure JSON 定義で使用されるプロパティの説明です。

| プロパティ | 説明 | 必須 |
| --- | --- | --- |
| type |type プロパティは **HDInsightOnDemand**に設定されます。 |はい |
| clusterSize |クラスター内の worker/データ ノードの数です。 このプロパティで指定した worker ノード数と共に 2 つのヘッド ノードを使用して HDInsight クラスターが作成されます。 ノードのサイズは Standard_D3 (4 コア) であるため、4 worker ノード クラスターのコアは 24 個になります (worker ノード用に 4\*4 = 16 個のコアと、ヘッド ノード用に 2\*4 = 8 個のコア)。 Standard_D3 レベルの詳細については、「[HDInsight での Linux ベースの Hadoop クラスターの作成](../../hdinsight/hdinsight-hadoop-provision-linux-clusters.md)」を参照してください。 |はい |
| timetolive |オンデマンド HDInsight クラスターに許可されるアイドル時間です。 他のアクティブなジョブがクラスターにない場合、アクティビティ実行の完了後にオンデマンド HDInsight クラスターが起動状態を維持する時間を指定します。<br/><br/>たとえば、アクティビティ実行に 6 分かかるときに timetolive が 5 分に設定されている場合、アクティビティ実行の 6 分間の処理の後、クラスターが起動状態を 5 分間維持します。 別のアクティビティ実行が 6 分の時間枠で実行される場合、それは同じクラスターで処理されます。<br/><br/>オンデマンド HDInsight クラスターの作成は高額な作業であり (時間もかかることがあります)、オンデマンド HDInsight クラスターを再利用し、Data Factory のパフォーマンスを改善する必要がある場合にこの設定を利用します。<br/><br/>timetolive 値を 0 に設定した場合、アクティビティ実行の処理直後にクラスターが削除されます。 その一方で、高い値を設定した場合、クラスターは不必要にアイドル状態を維持し、コストの上昇を招きます。 そのため、ニーズに合わせて適切な値を設定することが重要です。<br/><br/>timetolive プロパティ値が適切に設定されている場合、複数のパイプラインでオンデマンド HDInsight クラスターの同じインスタンスを共有できます。 |はい |
| version |HDInsight クラスターのバージョン。 詳細については、「[Azure Data Factory でサポートされる HDInsight バージョン](data-factory-compute-linked-services.md#supported-hdinsight-versions-in-azure-data-factory)」をご覧ください。 |いいえ |
| linkedServiceName |データを保存し、処理するためにオンデマンド クラスターで使用される Azure Storage のリンクされたサービスです。 <p>現時点では、Azure Data Lake Store をストレージとして使用するオンデマンド HDInsight クラスターを作成することはできません。 HDInsight 処理の結果データを Azure Data Lake Store に保存する必要がある場合は、コピー アクティビティを使用して、Azure Blob Storage から Azure Data Lake Store にデータをコピーします。</p>  | はい |
| additionalLinkedServiceNames |Data Factory サービスがあなたの代わりに登録できるように、HDInsight の「リンクされたサービス」の追加ストレージ アカウントを指定します。 |いいえ |
| osType |オペレーティング システムの種類。 使用できる値は、以下のとおりです。Windows (既定) および Linux |いいえ |
| hcatalogLinkedServiceName |HCatalog データベースを指す Azure SQL のリンクされたサービスの名前。 オンデマンド HDInsight クラスターは、Azure SQL データベースを metastore として使用して作成されます。 |いいえ |

### <a name="json-example"></a>JSON の例
次の JSON は、Linux ベースのオンデマンド HDInsight のリンクされたサービスを定義します。 Data Factory サービスは、データ スライスを処理するときに、 **Linux ベースの** HDInsight クラスターを自動的に作成します。

```json
{
    "name": "HDInsightOnDemandLinkedService",
    "properties": {
        "type": "HDInsightOnDemand",
        "typeProperties": {
            "version": "3.5",
            "clusterSize": 1,
            "timeToLive": "00:05:00",
            "osType": "Linux",
            "linkedServiceName": "StorageLinkedService"
        }
    }
}
```

詳細については、[コンピューティングのリンクされたサービス](data-factory-compute-linked-services.md)に関する記事を参照してください。

## <a name="existing-azure-hdinsight-cluster"></a>既存の Azure HDInsight クラスター
Azure HDInsight の「リンクされたサービス」を作成し、独自の HDInsight クラスターを Data Factory に登録できます。 このリンクされたサービスでは、データ変換アクティビティとして、[.NET カスタム アクティビティ](#net-custom-activity)、[Hive アクティビティ](#hdinsight-hive-activity)、[Pig アクティビティ](#hdinsight-pig-activity)、[MapReduce アクティビティ](#hdinsight-mapreduce-activity)、Hadoop ストリーミング アクティビティ、[Spark アクティビティ](#hdinsight-spark-activity)を実行することができます。

### <a name="linked-service"></a>リンクされたサービス
次の表は、Azure HDInsight のリンクされたサービスの Azure JSON 定義で使用されるプロパティの説明です。

| プロパティ | 説明 | 必須 |
| --- | --- | --- |
| type |type プロパティは **HDInsight**に設定されます。 |はい |
| clusterUri |HDInsight クラスターの URI です。 |はい |
| username |既存の HDInsight クラスターに接続するために使用されるユーザーの名前を指定します。 |はい |
| password |ユーザー アカウントのパスワードを指定します。 |はい |
| linkedServiceName | HDInsight クラスターで使われる Azure Blob Storage を参照する Azure Storage のリンクされたサービスの名前です。 <p>現在は、Azure Data Lake Store のリンクされたサービスをこのプロパティに指定することはできません。 HDInsight クラスターが Data Lake Store にアクセスできる場合、Hive/Pig スクリプトから Azure Data Lake Store 内のデータにアクセスできます。 </p>  |はい |

サポートされている HDInsight クラスターのバージョン一覧は、「[サポートされる HDInsight のバージョン](data-factory-compute-linked-services.md#supported-hdinsight-versions-in-azure-data-factory)」をご覧ください。

#### <a name="json-example"></a>JSON の例

```json
{
    "name": "HDInsightLinkedService",
    "properties": {
        "type": "HDInsight",
        "typeProperties": {
            "clusterUri": " https://<hdinsightclustername>.azurehdinsight.net/",
            "userName": "admin",
            "password": "<password>",
            "linkedServiceName": "MyHDInsightStoragelinkedService"
        }
    }
}
```

## <a name="azure-batch"></a>Azure Batch
Azure Batch のリンクされたサービスを作成し、仮想マシン (VM) の Batch プールをデータ ファクトリに登録できます。 Azure Batch と Azure HDInsight のいずれかを利用し、.NET カスタム アクティビティを実行できます。 このリンクされたサービスで、[.NET カスタム アクティビティ](#net-custom-activity)を実行することができます。

### <a name="linked-service"></a>リンクされたサービス
次の表は、Azure Batch のリンクされたサービスの Azure JSON 定義で使用されるプロパティの説明です。

| プロパティ | 説明 | 必須 |
| --- | --- | --- |
| type |type プロパティは **AzureBatch**に設定されます。 |はい |
| accountName |Azure Batch アカウントの名前です。 |はい |
| accessKey |Azure Batch アカウントのアクセス キーです。 |はい |
| poolName |仮想マシンのプールの名前です。 |はい |
| linkedServiceName |この Azure Batch の「リンクされたサービス」に関連付けられている Azure Storage の「リンクされたサービス」の名前です。 この「リンクされたサービス」はアクティビティの実行に必要なファイルのステージングとアクティビティ実行ログの保存に利用されます。 |はい |


#### <a name="json-example"></a>JSON の例

```json
{
    "name": "AzureBatchLinkedService",
    "properties": {
        "type": "AzureBatch",
        "typeProperties": {
            "accountName": "<Azure Batch account name>",
            "accessKey": "<Azure Batch account key>",
            "poolName": "<Azure Batch pool name>",
            "linkedServiceName": "<Specify associated storage linked service reference here>"
        }
    }
}
```

## <a name="azure-machine-learning"></a>Azure Machine Learning
Azure Machine Learning のリンクされたサービスを作成し、Machine Learning のバッチ スコアリング エンドポイントを Data Factory に登録します。 このリンクされたサービスで実行できる 2 つのデータ変換アクティビティ: [Machine Learning バッチ実行アクティビティ](#machine-learning-batch-execution-activity)、[Machine Learning 更新リソース アクティビティ](#machine-learning-update-resource-activity)。

### <a name="linked-service"></a>リンクされたサービス
次の表は、Azure Machine Learning のリンクされたサービスの Azure JSON 定義で使用されるプロパティの説明です。

| プロパティ | 説明 | 必須 |
| --- | --- | --- |
| Type |type プロパティは次の値に設定されます。**AzureML**。 |はい |
| mlEndpoint |バッチ スコアリング URL です。 |はい |
| apiKey |公開されたワークスペース モデルの API です。 |はい |

#### <a name="json-example"></a>JSON の例

```json
{
    "name": "AzureMLLinkedService",
    "properties": {
        "type": "AzureML",
        "typeProperties": {
            "mlEndpoint": "https://[batch scoring endpoint]/jobs",
            "apiKey": "<apikey>"
        }
    }
}
```

## <a name="azure-data-lake-analytics"></a>Azure Data Lake Analytics
**Azure Data Lake Analytics** リンク サービスを作成して、Azure Data Lake Analytics コンピューティング サービスを Azure Data Factory にリンクしてから、パイプラインで [Data Lake Analytics U-SQL アクティビティ](data-factory-usql-activity.md) を使用します。

### <a name="linked-service"></a>リンクされたサービス

次の表は、Azure Data Lake Analytics のリンクされたサービスの JSON 定義で使用されるプロパティの説明です。

| プロパティ | 説明 | 必須 |
| --- | --- | --- |
| Type |type プロパティは次の値に設定されます。**AzureDataLakeAnalytics**。 |はい |
| accountName |Azure Data Lake Analytics アカウント名。 |はい |
| dataLakeAnalyticsUri |Azure Data Lake Analytics URI。 |いいえ |
| authorization |Data Factory Editor で **[承認]** ボタンをクリックし、OAuth ログインを完了すると、承認コードが自動的に取得されます。 |はい |
| subscriptionId |Azure サブスクリプション ID |いいえ (指定されていない場合、Data Factory のサブスクリプションが使用されます)。 |
| resourceGroupName |Azure リソース グループ名 |いいえ (指定されていない場合は Data Factory のリソース グループが使用されます)。 |
| sessionId |OAuth 承認セッションのセッション ID です。 各セッション ID は一意であり、1 回のみ使用できます。 この ID は、Data Factory エディターを使用するときに自動的に生成されます。 |はい |


#### <a name="json-example"></a>JSON の例
次の例は、Azure Data Lake Analytics リンク サービスの JSON 定義です。

```json
{
    "name": "AzureDataLakeAnalyticsLinkedService",
    "properties": {
        "type": "AzureDataLakeAnalytics",
        "typeProperties": {
            "accountName": "<account name>",
            "dataLakeAnalyticsUri": "datalakeanalyticscompute.net",
            "authorization": "<authcode>",
            "sessionId": "<session ID>",
            "subscriptionId": "<subscription id>",
            "resourceGroupName": "<resource group name>"
        }
    }
}
```

## <a name="azure-sql-database"></a>Azure SQL Database
Azure SQL のリンクされたサービスを作成し、 [ストアド プロシージャ アクティビティ](#stored-procedure-activity) で使用して、Data Factory パイプラインからストアド プロシージャを起動します。

### <a name="linked-service"></a>リンクされたサービス
Azure SQL Database のリンクされたサービスを定義するには、リンクされたサービスの **type** を **AzureSqlDatabase** に設定し、**typeProperties** セクションで以下のプロパティを指定します。

| プロパティ | 説明 | 必須 |
| --- | --- | --- |
| connectionString |connectionString プロパティの Azure SQL データベース インスタンスに接続するために必要な情報を指定します。 |はい |

#### <a name="json-example"></a>JSON の例

```json
{
    "name": "AzureSqlLinkedService",
    "properties": {
        "type": "AzureSqlDatabase",
        "typeProperties": {
            "connectionString": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;User ID=<username>@<servername>;Password=<password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
        }
    }
}
```

このリンクされたサービスの詳細については、 [Azure SQL コネクタ](data-factory-azure-sql-connector.md#linked-service-properties) に関する記事を参照してください。

## <a name="azure-sql-data-warehouse"></a>Azure SQL Data Warehouse
Azure SQL Data Warehouse のリンクされたサービスを作成し、 [ストアド プロシージャ アクティビティ](data-factory-stored-proc-activity.md) で使用して、Data Factory パイプラインからストアド プロシージャを起動します。

### <a name="linked-service"></a>リンクされたサービス
Azure SQL Data Warehouse のリンクされたサービスを定義するには、リンクされたサービスの **type** を **AzureSqlDW** に設定し、**typeProperties** セクションで以下のプロパティを指定します。

| プロパティ | 説明 | 必須 |
| --- | --- | --- |
| connectionString |connectionString プロパティの Azure SQL Data Warehouse インスタンスに接続するために必要な情報を指定します。 |はい |

#### <a name="json-example"></a>JSON の例

```json
{
    "name": "AzureSqlDWLinkedService",
    "properties": {
        "type": "AzureSqlDW",
        "typeProperties": {
            "connectionString": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;User ID=<username>@<servername>;Password=<password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
        }
    }
}
```

詳細については、[Azure SQL Data Warehouse コネクタ](data-factory-azure-sql-data-warehouse-connector.md#linked-service-properties)に関する記事を参照してください。

## <a name="sql-server"></a>SQL Server
SQL Server のリンクされたサービスを作成し、 [ストアド プロシージャ アクティビティ](data-factory-stored-proc-activity.md) で使用して、Data Factory パイプラインからストアド プロシージャを起動します。

### <a name="linked-service"></a>リンクされたサービス
**OnPremisesSqlServer** 型のリンクされたサービスを使用し、オンプレミスの SQL Server データベースをデータ ファクトリにリンクします。 次の表は、オンプレミスの SQL Server のリンクされたサービスに固有の JSON 要素の説明をまとめたものです。

次の表は、SQL Server のリンクされたサービスに固有の JSON 要素の説明をまとめたものです。

| プロパティ | 説明 | 必須 |
| --- | --- | --- |
| type |type プロパティは次の値に設定されます。**OnPremisesSqlServer** |はい |
| connectionString |SQL 認証または Windows 認証を使用して、オンプレミス SQL Server データベースに接続するために必要な connectionString 情報を指定します。 |はい |
| gatewayName |Data Factory サービスが、オンプレミスの SQL Server データベースへの接続に使用するゲートウェイの名前です。 |はい |
| username |Windows 認証を使用している場合は、ユーザー名を指定します。 例: **domainname\\username**。 |いいえ |
| password |ユーザー名に指定したユーザー アカウントのパスワードを指定します。 |いいえ |

**New-AzDataFactoryEncryptValue** コマンドレットを使用して資格情報を暗号化し、次の例で示すようにそれを接続文字列で使用できます (**EncryptedCredential** プロパティ)。

```JSON
"connectionString": "Data Source=<servername>;Initial Catalog=<databasename>;Integrated Security=True;EncryptedCredential=<encrypted credential>",
```


#### <a name="example-json-for-using-sql-authentication"></a>例:SQL 認証を使用するための JSON

```json
{
    "name": "MyOnPremisesSQLDB",
    "properties": {
        "type": "OnPremisesSqlServer",
        "typeProperties": {
            "connectionString": "Data Source=<servername>;Initial Catalog=MarketingCampaigns;Integrated Security=False;User ID=<username>;Password=<password>;",
            "gatewayName": "<gateway name>"
        }
    }
}
```
#### <a name="example-json-for-using-windows-authentication"></a>例:Windows 認証を使用するための JSON

ユーザー名とパスワードが指定されている場合、ゲートウェイはその情報を使用して指定されたユーザー アカウントに偽装して、オンプレミス SQL Server データベースに接続します。 指定されていない場合、ゲートウェイは、Gateway (スタートアップ アカウント) のセキュリティ コンテキストを使用して SQL Server に直接接続します。

```json
{
    "Name": " MyOnPremisesSQLDB",
    "Properties": {
        "type": "OnPremisesSqlServer",
        "typeProperties": {
            "ConnectionString": "Data Source=<servername>;Initial Catalog=MarketingCampaigns;Integrated Security=True;",
            "username": "<domain\\username>",
            "password": "<password>",
            "gatewayName": "<gateway name>"
        }
    }
}
```

詳細については、[SQL Server コネクタ](data-factory-sqlserver-connector.md#linked-service-properties)に関する記事を参照してください。

## <a name="data-transformation-activities"></a>データ変換アクティビティ

アクティビティ | 説明
-------- | -----------
[HDInsight Hive アクティビティ](#hdinsight-hive-activity) | Data Factory パイプラインの HDInsight Hive アクティビティでは、独自またはオンデマンドの Windows/Linux ベースの HDInsight クラスターで Hive クエリを実行します。
[HDInsight Pig アクティビティ](#hdinsight-pig-activity) | Data Factory パイプラインの HDInsight Pig アクティビティでは、独自またはオンデマンドの Windows/Linux ベースのHDInsight クラスターで Pig クエリを実行します。
[HDInsight MapReduce アクティビティ](#hdinsight-mapreduce-activity) | Data Factory パイプラインの HDInsight MapReduce アクティビティは、独自の、またはオンデマンドの Windows/Linux ベースの HDInsight クラスターで MapReduce プログラムを実行します。
[HDInsight Streaming アクティビティ](#hdinsight-streaming-activity) | Data Factory パイプラインの HDInsight Streaming アクティビティは、独自の、またはオンデマンドの Windows/Linux ベースの HDInsight クラスターで Hadoop Streaming プログラムを実行します。
[HDInsight Spark アクティビティ](#hdinsight-spark-activity) | Data Factory パイプラインの HDInsight Spark アクティビティでは、独自の HDInsight クラスターで Spark プログラムを実行します。
[Machine Learning バッチ実行アクティビティ](#machine-learning-batch-execution-activity) | Azure Data Factory を使用すると、公開された Azure Machine Learning Web サービスを利用して予測分析を行うパイプラインを簡単に作成できます。 Azure Data Factory パイプラインでバッチ実行アクティビティを使用すると、Machine Learning Web サービスを呼び出して、データの予測を一括で行うことができます。
[Machine Learning 更新リソース アクティビティ](#machine-learning-update-resource-activity) | 時間の経過と共に、Machine Learning スコア付け実験の予測モデルには、新しい入力データセットを使用した再トレーニングが必要になります。 再トレーニングが完了したら、再トレーニング済みの Machine Learning モデルでスコア付け Web サービスを更新する必要があります。 更新リソース アクティビティを使用して、新しくトレーニングを行ったモデルで Web サービスを更新します。
[ストアド プロシージャ アクティビティ](#stored-procedure-activity) | Data Factory パイプラインでストアド プロシージャ アクティビティを使用して、次のいずれかのデータ ストアでストアド プロシージャを呼び出すことができます。企業または Azure VM 内の Azure SQL Database、Azure SQL Data Warehouse、SQL Server データベース。
[Data Lake Analytics U-SQL アクティビティ](#data-lake-analytics-u-sql-activity) | Data Lake Analytics U-SQL アクティビティは、Azure Data Lake Analytics クラスターで U-SQL スクリプトを実行します。
[.NET カスタム アクティビティ](#net-custom-activity) | Data Factory でサポートされていない方法でデータを変換する必要がある場合は、独自のデータ処理ロジックを使用するカスタム アクティビティを作成し、パイプラインでそのアクティビティを使用できます。 Azure Batch サービスまたは Azure HDInsight クラスターを使用して実行するようにカスタム .NET アクティビティを構成できます。


## <a name="hdinsight-hive-activity"></a>HDInsight Hive アクティビティ
Hive アクティビティの JSON 定義では、以下のプロパティを指定できます。 このアクティビティの type プロパティは **HDInsightHive** である必要があります。 HDInsight のリンクされたサービスを先に作成し、その名前を **linkedServiceName** プロパティの値として指定してください。 アクティビティの種類を HDInsightHive に設定する場合、**typeProperties** セクションで以下のプロパティがサポートされます。

| プロパティ | 説明 | 必須 |
| --- | --- | --- |
| script |Hive スクリプトをインラインに指定します |いいえ |
| scriptPath |Hive スクリプトを Azure BLOB ストレージに格納し、ファイルへのパスを指定します。 'script' プロパティまたは 'scriptPath' プロパティを使用します。 両方を同時に使用することはできません。 ファイル名は大文字と小文字が区別されます。 |いいえ |
| defines |'hiveconf' を使用して Hive スクリプト内で参照するキーと値のペアとしてパラメーターを指定します |いいえ |

これらの種類のプロパティは、Hive アクティビティに固有です。 その他 (typeProperties セクション外) のプロパティは、すべてのアクティビティで使用できます。

### <a name="json-example"></a>JSON の例
次の JSON は、パイプラインの HDInsight Hive アクティビティを定義します。

```json
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

詳細については、[Hive アクティビティ](data-factory-hive-activity.md)に関する記事を参照してください。

## <a name="hdinsight-pig-activity"></a>HDInsight Pig アクティビティ
Pig アクティビティの JSON 定義では、以下のプロパティを指定できます。 このアクティビティの type プロパティは **HDInsightPig** である必要があります。 HDInsight のリンクされたサービスを先に作成し、その名前を **linkedServiceName** プロパティの値として指定してください。 アクティビティの種類を HDInsightPig に設定する場合、**typeProperties** セクションで以下のプロパティがサポートされます。

| プロパティ | 説明 | 必須 |
| --- | --- | --- |
| script |Pig スクリプトをインラインに指定します |いいえ |
| scriptPath |Pig スクリプトを Azure BLOB ストレージに格納し、ファイルへのパスを指定します。 'script' プロパティまたは 'scriptPath' プロパティを使用します。 両方を同時に使用することはできません。 ファイル名は大文字と小文字が区別されます。 |いいえ |
| defines |Pig スクリプト内で参照するキーと値のペアとしてパラメーターを指定します |いいえ |

これらの種類のプロパティは、Pig アクティビティに固有です。 その他 (typeProperties セクション外) のプロパティは、すべてのアクティビティで使用できます。

### <a name="json-example"></a>JSON の例

```json
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

詳細については、Pig アクティビティに関する記事を参照してください。

## <a name="hdinsight-mapreduce-activity"></a>HDInsight MapReduce アクティビティ
MapReduce アクティビティの JSON 定義では、以下のプロパティを指定できます。 このアクティビティの type プロパティは **HDInsightMapReduce** である必要があります。 HDInsight のリンクされたサービスを先に作成し、その名前を **linkedServiceName** プロパティの値として指定してください。 アクティビティの種類を HDInsightMapReduce に設定する場合、**typeProperties** セクションで以下のプロパティがサポートされます。

| プロパティ | 説明 | 必須 |
| --- | --- | --- |
| jarLinkedService | JAR ファイルが含まれる Azure Storage のリンクされたサービスの名前。 | はい |
| jarFilePath | Azure Storage 内の JAR ファイルのパス。 | はい |
| className | JAR ファイルのメイン クラスの名前。 | はい |
| arguments | MapReduce プログラムに対するコンマ区切り引数のリスト。 実行時に、MapReduce フレームワークのいくつかの引数 (mapreduce.job.tags など) が表示されます。 MapReduce の引数と区別するために、次の例のように、オプションと値の両方を引数として使用することを検討してください (-s、--input、--output などがオプションであり、直後に値が続きます)。 | いいえ |

### <a name="json-example"></a>JSON の例

```json
{
    "name": "MahoutMapReduceSamplePipeline",
    "properties": {
        "description": "Sample Pipeline to Run a Mahout Custom Map Reduce Jar. This job calculates an Item Similarity Matrix to determine the similarity between two items",
        "activities": [
            {
                "type": "HDInsightMapReduce",
                "typeProperties": {
                    "className": "org.apache.mahout.cf.taste.hadoop.similarity.item.ItemSimilarityJob",
                    "jarFilePath": "adfsamples/Mahout/jars/mahout-examples-0.9.0.2.2.7.1-34.jar",
                    "jarLinkedService": "StorageLinkedService",
                    "arguments": ["-s", "SIMILARITY_LOGLIKELIHOOD", "--input", "wasb://adfsamples@spestore.blob.core.windows.net/Mahout/input", "--output", "wasb://adfsamples@spestore.blob.core.windows.net/Mahout/output/", "--maxSimilaritiesPerItem", "500", "--tempDir", "wasb://adfsamples@spestore.blob.core.windows.net/Mahout/temp/mahout"]
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
        "start": "2017-01-03T00:00:00",
        "end": "2017-01-04T00:00:00"
    }
}
```

詳細については、[MapReduce アクティビティ](data-factory-map-reduce.md)に関する記事を参照してください。

## <a name="hdinsight-streaming-activity"></a>HDInsight Streaming アクティビティ
Hadoop Streaming アクティビティの JSON 定義では、以下のプロパティを指定できます。 このアクティビティの type プロパティは **HDInsightStreaming** という種類のアクティビティが 1 つだけあります。 HDInsight のリンクされたサービスを先に作成し、その名前を **linkedServiceName** プロパティの値として指定してください。 アクティビティの種類を HDInsightStreaming に設定する場合、**typeProperties** セクションで以下のプロパティがサポートされます。

| プロパティ | 説明 |
| --- | --- |
| mapper | mapper 実行可能ファイルの名前。 例では、cat.exe が mapper 実行可能ファイルです。|
| reducer | reducer 実行可能ファイルの名前。 例では、wc.exe が reducer 実行可能ファイルです。 |
| input | マッパーの入力ファイル (場所も含む)。 `"wasb://adfsample@<account name>.blob.core.windows.net/example/data/gutenberg/davinci.txt"` の例で、adfsample は BLOB コンテナー、example/data/Gutenberg はフォルダー、davinci.txt は BLOB です。 |
| output | レジューサーの出力ファイル (場所も含む)。 Hadoop ストリーミング ジョブの出力は、このプロパティに指定されている場所に書き込まれます。 |
| filePaths | mapper と reducer の実行可能ファイルのパス。 例の "adfsample/example/apps/wc.exe"： adfsample は BLOB コンテナー、example/apps はフォルダー、wc.exe は実行可能ファイルです。 |
| fileLinkedService | filePaths セクションで指定されたファイルを含む Azure Storage のリンクされたサービス。 |
| arguments | MapReduce プログラムに対するコンマ区切り引数のリスト。 実行時に、MapReduce フレームワークのいくつかの引数 (mapreduce.job.tags など) が表示されます。 MapReduce の引数と区別するために、次の例のように、オプションと値の両方を引数として使用することを検討してください (-s、--input、--output などがオプションであり、直後に値が続きます)。 |
| getDebugInfo | 省略可能な要素。 Failure に設定されていると、エラー時にのみログがダウンロードされます。 All に設定されていると、ログは実行状態に関係なく常にダウンロードされます。 |

> [!NOTE]
> **outputs** プロパティには、Hadoop ストリーミング アクティビティ用の出力データセットを指定する必要があります。 パイプラインのスケジュール (毎時、毎日など) を実行するために必要となるダミーのデータセットでもかまいません。 アクティビティが入力を受け取らない場合は、**inputs** プロパティに対するアクティビティの入力データセットの指定は省略できます。

## <a name="json-example"></a>JSON の例

```json
{
    "name": "HadoopStreamingPipeline",
    "properties": {
        "description": "Hadoop Streaming Demo",
        "activities": [
            {
                "type": "HDInsightStreaming",
                "typeProperties": {
                    "mapper": "cat.exe",
                    "reducer": "wc.exe",
                    "input": "wasb://<nameofthecluster>@spestore.blob.core.windows.net/example/data/gutenberg/davinci.txt",
                    "output": "wasb://<nameofthecluster>@spestore.blob.core.windows.net/example/data/StreamingOutput/wc.txt",
                    "filePaths": ["<nameofthecluster>/example/apps/wc.exe","<nameofthecluster>/example/apps/cat.exe"],
                    "fileLinkedService": "StorageLinkedService",
                    "getDebugInfo": "Failure"
                },
                "outputs": [
                    {
                        "name": "StreamingOutputDataset"
                    }
                ],
                "policy": {
                    "timeout": "01:00:00",
                    "concurrency": 1,
                    "executionPriorityOrder": "NewestFirst",
                    "retry": 1
                },
                "scheduler": {
                    "frequency": "Day",
                    "interval": 1
                },
                "name": "RunHadoopStreamingJob",
                "description": "Run a Hadoop streaming job",
                "linkedServiceName": "HDInsightLinkedService"
            }
        ],
        "start": "2014-01-04T00:00:00",
        "end": "2014-01-05T00:00:00"
    }
}
```

詳細については、[Hadoop ストリーミング アクティビティ](data-factory-hadoop-streaming-activity.md)に関する記事を参照してください。

## <a name="hdinsight-spark-activity"></a>HDInsight Spark アクティビティ
Spark アクティビティの JSON 定義では、以下のプロパティを指定できます。 このアクティビティの type プロパティは **HDInsightSpark** である必要があります。 HDInsight のリンクされたサービスを先に作成し、その名前を **linkedServiceName** プロパティの値として指定してください。 アクティビティの種類を HDInsightSpark に設定する場合、**typeProperties** セクションで以下のプロパティがサポートされます。

| プロパティ | 説明 | 必須 |
| -------- | ----------- | -------- |
| rootPath | Azure BLOB コンテナーと Spark ファイルを含むフォルダー。 ファイル名は大文字と小文字が区別されます。 | はい |
| entryFilePath | Spark コード/パッケージのルート フォルダーへの相対パス。 | はい |
| className | アプリケーションの Java/Spark のメイン クラス | いいえ |
| arguments | Spark プログラムのコマンドライン引数の一覧です。 | いいえ |
| proxyUser | Spark プログラムの実行を偽装する借用すユーザー アカウント | いいえ |
| sparkConfig | Spark 構成のプロパティ。 | いいえ |
| getDebugInfo | HDInsight クラスターで使用されている Azure Storage または sparkJobLinkedService で指定された Azure Storage に Spark ログ ファイルがコピーされるタイミングを指定します。 使用できる値は以下の通りです。None、Always、または Failure。 既定値:なし。 | いいえ |
| sparkJobLinkedService | Spark ジョブ ファイル、依存関係、およびログが含まれる Azure Storage のリンクされたサービス。  指定しない場合は、HDInsight クラスターに関連付けられているストレージが使用されます。 | いいえ |

### <a name="json-example"></a>JSON の例

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
                    "getDebugInfo": "Always"
                },
                "outputs": [
                    {
                        "name": "OutputDataset"
                    }
                ],
                "name": "MySparkActivity",
                "linkedServiceName": "HDInsightLinkedService"
            }
        ],
        "start": "2017-02-05T00:00:00",
        "end": "2017-02-06T00:00:00"
    }
}
```
以下の点に注意してください。

- **type** プロパティは **HDInsightSpark** に設定されます。
- **rootPath** は **adfspark\\pyFiles** に設定されます。ここで、adfspark は Azure BLOB コンテナー、pyFiles はそのコンテナーのファイル フォルダーです。 この例では、Azure Blob Storage は、Spark クラスターに関連付けられています。 ファイルは、別の Azure Storage にアップロードできます。 これを行う場合は、ストレージ アカウントをデータ ファクトリにリンクする Azure Storage のリンクされたサービスを作成します。 次に、リンクされたサービスの名前を、**sparkJobLinkedService** プロパティの値として指定します。 このプロパティと、Spark アクティビティでサポートされている他のプロパティの詳細については、「Spark アクティビティのプロパティ」を参照してください。
- **entryFilePath** は、python ファイルである **test.py** に設定されます。
- **getDebugInfo** プロパティは **Always** に設定されます。つまり、ログ ファイルが常に生成されます (成功または失敗)。

    > [!IMPORTANT]
    > 問題のトラブルシューティングを行う場合を除き、運用環境でこのプロパティを Always に設定しないことをお勧めします。
- **outputs** セクションには、1 つの出力データセットがあります。 出力データセットは、Spark プログラムによって出力が生成されなくても指定する必要があります。 出力データセットは、パイプラインのスケジュール (1 時間に 1 回、毎日など) を開始します。

このアクティビティの詳細については、[Spark アクティビティ](data-factory-spark.md)に関する記事を参照してください。

## <a name="machine-learning-batch-execution-activity"></a>Machine Learning バッチ実行アクティビティ
Azure Machine Learning Studio バッチ実行アクティビティの JSON 定義では、以下のプロパティを指定できます。 このアクティビティの type プロパティは **AzureMLBatchExecution** である必要があります。 Azure Machine Learning のリンクされたサービスを先に作成し、その名前を **linkedServiceName** プロパティの値として指定してください。 アクティビティの種類を AzureMLBatchExecution に設定する場合、**typeProperties** セクションで以下のプロパティがサポートされます。

プロパティ | 説明 | 必須
-------- | ----------- | --------
webServiceInput | Azure Machine Learning Studio Web サービスの入力として渡すデータセット。 アクティビティの入力にも、このデータセットを含める必要があります。 |webServiceInput または webServiceInputs を使用します。 |
webServiceInputs | Azure Machine Learning Studio Web サービスの入力として渡す複数のデータセットを指定します。 Web サービスが複数の入力を受け取る場合は、webServiceInput プロパティではなく、webServiceInputs プロパティを使用します。 **webServiceInputs** から参照されているデータセットもアクティビティの **inputs** に含める必要があります。 | webServiceInput または webServiceInputs を使用します。 |
webServiceOutputs | Azure Machine Learning Studio Web サービスの出力として割り当てられるデータセット。 このデータセットで Web サービスの出力データが返されます。 | はい |
globalParameters | このセクションの Web サービス パラメーターの値を指定します。 | いいえ |

### <a name="json-example"></a>JSON の例
この例では、アクティビティの入力としてデータセット **MLSqlInput** を、出力として **MLSqlOutput** を設定しています。 **MLSqlInput** は、**webServiceInput** JSON プロパティを使用して Web サービスの入力として渡します。 **MLSqlOutput** は、**webServiceOutputs** JSON プロパティを使用して Web サービスの出力として渡します。

```json
{
   "name": "MLWithSqlReaderSqlWriter",
   "properties": {
      "description": "Azure ML model with sql azure reader/writer",
      "activities": [{
         "name": "MLSqlReaderSqlWriterActivity",
         "type": "AzureMLBatchExecution",
         "description": "test",
         "inputs": [ { "name": "MLSqlInput" }],
         "outputs": [ { "name": "MLSqlOutput" } ],
         "linkedServiceName": "MLSqlReaderSqlWriterDecisionTreeModel",
         "typeProperties":
         {
            "webServiceInput": "MLSqlInput",
            "webServiceOutputs": {
               "output1": "MLSqlOutput"
            },
            "globalParameters": {
               "Database server name": "<myserver>.database.windows.net",
               "Database name": "<database>",
               "Server user account name": "<user name>",
               "Server user account password": "<password>"
            }
         },
         "policy": {
            "concurrency": 1,
            "executionPriorityOrder": "NewestFirst",
            "retry": 1,
            "timeout": "02:00:00"
         }
      }],
      "start": "2016-02-13T00:00:00",
       "end": "2016-02-14T00:00:00"
   }
}
```

この例の JSON では、デプロイされた Azure Machine Learning Web サービスが、リーダー モジュールとライター モジュールを使用して、Azure SQL Database のデータを読み書きします。 この Web サービスでは、Database server name、Database name、Server user account name、Server user account password という 4 つのパラメーターが公開されています。

> [!NOTE]
> AzureMLBatchExecution アクティビティの入力および出力だけを、パラメーターとして Web サービスに渡すことができます。 たとえば、上の JSON スニペットでは、MLSqlInput は AzureMLBatchExecution アクティビティへの入力であり、webServiceInput パラメーターを使用して Web サービスに入力として渡されます。

## <a name="machine-learning-update-resource-activity"></a>Machine Learning 更新リソース アクティビティ
Azure Machine Learning Studio 更新リソース アクティビティの JSON 定義では、以下のプロパティを指定できます。 このアクティビティの type プロパティは **AzureMLUpdateResource** である必要があります。 Azure Machine Learning のリンクされたサービスを先に作成し、その名前を **linkedServiceName** プロパティの値として指定してください。 アクティビティの種類を AzureMLUpdateResource に設定する場合、**typeProperties** セクションで以下のプロパティがサポートされます。

プロパティ | 説明 | 必須
-------- | ----------- | --------
trainedModelName | 再トレーニング済みモデルの名前。 | はい |
trainedModelDatasetName | 再トレーニング操作から返される iLearner ファイルを指すデータセット。 | はい |

### <a name="json-example"></a>JSON の例
パイプラインには、**AzureMLBatchExecution** と **AzureMLUpdateResource** の 2 つのアクティビティが含まれています。 Azure Machine Learning Studioバッチ実行アクティビティはトレーニング データを入力として使用し、iLearner ファイルを出力として作成します。 このアクティビティは、トレーニング Web サービス (Web サービスとして公開されたトレーニング実験) と入力トレーニング データを呼び出し、Web サービスから ilearner ファイルを受け取ります。 placeholderBlob は、パイプラインを実行するために、Azure Data Factory サービスで必要とされるダミーの出力データセットです。


```json
{
    "name": "pipeline",
    "properties": {
        "activities": [
            {
                "name": "retraining",
                "type": "AzureMLBatchExecution",
                "inputs": [
                    {
                        "name": "trainingData"
                    }
                ],
                "outputs": [
                    {
                        "name": "trainedModelBlob"
                    }
                ],
                "typeProperties": {
                    "webServiceInput": "trainingData",
                    "webServiceOutputs": {
                        "output1": "trainedModelBlob"
                    }
                 },
                "linkedServiceName": "trainingEndpoint",
                "policy": {
                    "concurrency": 1,
                    "executionPriorityOrder": "NewestFirst",
                    "retry": 1,
                    "timeout": "02:00:00"
                }
            },
            {
                "type": "AzureMLUpdateResource",
                "typeProperties": {
                    "trainedModelName": "trained model",
                    "trainedModelDatasetName" :  "trainedModelBlob"
                },
                "inputs": [{ "name": "trainedModelBlob" }],
                "outputs": [{ "name": "placeholderBlob" }],
                "policy": {
                    "timeout": "01:00:00",
                    "concurrency": 1,
                    "retry": 3
                },
                "name": "AzureML Update Resource",
                "linkedServiceName": "updatableScoringEndpoint2"
            }
        ],
        "start": "2016-02-13T00:00:00",
        "end": "2016-02-14T00:00:00"
    }
}
```

## <a name="data-lake-analytics-u-sql-activity"></a>Data Lake Analytics U-SQL アクティビティ
U-SQL アクティビティの JSON 定義では、以下のプロパティを指定できます。 このアクティビティの type プロパティは **DataLakeAnalyticsU-SQL** である必要があります。 Azure Data Lake Analytics のリンクされたサービスを作成し、その名前を **linkedServiceName** プロパティの値として指定してください。 アクティビティの種類を DataLakeAnalyticsU-SQL に設定する場合、**typeProperties** セクションで以下のプロパティがサポートされます。

| プロパティ | 説明 | 必須 |
|:--- |:--- |:--- |
| scriptPath |U-SQL スクリプトを含むフォルダーのパス。 ファイル名は大文字と小文字が区別されます。 |いいえ (スクリプトを使用する場合) |
| scriptLinkedService |Data Factory に対するスクリプトを含むストレージをリンクするリンク サービス |いいえ (スクリプトを使用する場合) |
| script |scriptPath と scriptLinkedService を指定する代わりに、インライン スクリプトを指定します。 例: "script": "CREATE DATABASE テスト"。 |いいえ (scriptPath と scriptLinkedService を使用する場合) |
| degreeOfParallelism |ジョブを実行するために同時に使用される最大ノード数。 |いいえ |
| priority |キューされているすべてのジョブのうち、先に実行するジョブを決定します。 数値が小さいほど、優先度は高くなります。 |いいえ |
| parameters |U-SQL スクリプトのパラメーター |いいえ |

### <a name="json-example"></a>JSON の例

```json
{
    "name": "ComputeEventsByRegionPipeline",
    "properties": {
        "description": "This pipeline computes events for en-gb locale and date less than Feb 19, 2012.",
        "activities":
        [
            {
                "type": "DataLakeAnalyticsU-SQL",
                "typeProperties": {
                    "scriptPath": "scripts\\kona\\SearchLogProcessing.txt",
                    "scriptLinkedService": "StorageLinkedService",
                    "degreeOfParallelism": 3,
                    "priority": 100,
                    "parameters": {
                        "in": "/datalake/input/SearchLog.tsv",
                        "out": "/datalake/output/Result.tsv"
                    }
                },
                "inputs": [
                    {
                        "name": "DataLakeTable"
                    }
                ],
                "outputs":
                [
                    {
                        "name": "EventsByRegionTable"
                    }
                ],
                "policy": {
                    "timeout": "06:00:00",
                    "concurrency": 1,
                    "executionPriorityOrder": "NewestFirst",
                    "retry": 1
                },
                "scheduler": {
                    "frequency": "Day",
                    "interval": 1
                },
                "name": "EventsByRegion",
                "linkedServiceName": "AzureDataLakeAnalyticsLinkedService"
            }
        ],
        "start": "2015-08-08T00:00:00",
        "end": "2015-08-08T01:00:00",
        "isPaused": false
    }
}
```

詳細については、「[Data Lake Analytics U-SQL アクティビティ](data-factory-usql-activity.md)」を参照してください。

## <a name="stored-procedure-activity"></a>ストアド プロシージャ アクティビティ
ストアド プロシージャ アクティビティの JSON 定義では、以下のプロパティを指定できます。 このアクティビティの type プロパティは **SqlServerStoredProcedure** である必要があります。 次のいずれかのリンクされたサービスを作成し、その名前を **linkedServiceName** プロパティの値として指定してください。

- SQL Server
- Azure SQL Database
- Azure SQL Data Warehouse

アクティビティの種類を SqlServerStoredProcedure に設定する場合、**typeProperties** セクションで以下のプロパティがサポートされます。

| プロパティ | 説明 | 必須 |
| --- | --- | --- |
| storedProcedureName |出力テーブルに使用するリンク サービスで示される Azure SQL データベースまたは Azure SQL Data Warehouse のストアド プロシージャ名を指定します。 |はい |
| storedProcedureParameters |ストアド プロシージャのパラメーター値を指定します。 パラメーターで null を渡す必要がある場合は、構文として "param1": null (すべて小文字) を使用します。 このプロパティの使用方法については、次のサンプルをご覧ください。 |いいえ |

入力データセットを指定した場合、ストアド プロシージャ アクティビティの実行に使用できる ("準備完了" 状態である) 必要があります。 ストアド プロシージャで入力データセットをパラメーターとして使用することはできません。 入力データセットは、ストアド プロシージャ アクティビティを開始する前に、依存関係の確認にのみ使用されます。 ストアド プロシージャ アクティビティの出力データセットを指定する必要があります。

出力データセットでは、ストアド プロシージャ アクティビティの**スケジュール** (毎時、毎週、毎月など) を指定します。 出力データセットでは、ストアド プロシージャを実行する、Azure SQL Database、Azure SQL Data Warehouse、または SQL Server Database を表す**リンクされたサービス**を使用する必要があります。 出力データセットは、パイプラインの別のアクティビティ ([連鎖するアクティビティ](data-factory-scheduling-and-execution.md##multiple-activities-in-a-pipeline)) による後続処理のために、ストアド プロシージャの結果を渡す 1 つの方法として使用できます。 ただし、Data Factory では、ストアド プロシージャの出力をこのデータセットに自動的に書き込むわけではありません。 出力データセットが参照する SQL テーブルへの書き込みは、ストアド プロシージャが実行します。 出力データセットに**ダミー データセット**を指定できる場合もあります。ダミー データセットは、ストアド プロシージャ アクティビティを実行するスケジュールの指定にのみ使用されます。

### <a name="json-example"></a>JSON の例

```json
{
    "name": "SprocActivitySamplePipeline",
    "properties": {
        "activities": [
            {
                "type": "SqlServerStoredProcedure",
                "typeProperties": {
                    "storedProcedureName": "usp_sample",
                    "storedProcedureParameters": {
                        "DateTime": "$$Text.Format('{0:yyyy-MM-dd HH:mm:ss}', SliceStart)"
                    }
                },
                "outputs": [{ "name": "sprocsampleout" }],
                "name": "SprocActivitySample"
            }
        ],
         "start": "2016-08-02T00:00:00",
         "end": "2016-08-02T05:00:00",
        "isPaused": false
    }
}
```

詳細については、「[ストアド プロシージャ アクティビティ](data-factory-stored-proc-activity.md)」の記事を参照してください。

## <a name="net-custom-activity"></a>.NET カスタム アクティビティ
.NET カスタム アクティビティの JSON 定義では、以下のプロパティを指定できます。 このアクティビティの type プロパティは **DotNetActivity** です。 Azure HDInsight または Azure Batch のリンクされたサービスを作成し、その名前を **linkedServiceName** プロパティの値として指定してください。 アクティビティの種類を DotNetActivity に設定する場合、**typeProperties** セクションで以下のプロパティがサポートされます。

| プロパティ | 説明 | 必須 |
|:--- |:--- |:--- |
| AssemblyName | アセンブリの名前。 この例では、それは **MyDotnetActivity.dll**. です。 | はい |
| EntryPoint |IDotNetActivity インターフェイスを実装するクラスの名前。 この例では、それは **MyDotNetActivityNS.MyDotNetActivity** です。ここで、MyDotNetActivityNS は名前空間であり、MyDotNetActivity はクラスです。  | はい |
| PackageLinkedService | カスタム アクティビティの zip ファイルが格納された Blob Storage を指す、Azure Storage のリンクされたサービスの名前。 この例では、それは **AzureStorageLinkedService** を作成します。| はい |
| PackageFile | zip ファイルの名前。 この例では **customactivitycontainer/MyDotNetActivity.zip** が該当します。 | はい |
| extendedProperties | 独自に定義して .NET コードに渡すことができる拡張プロパティ。 この例では、**SliceStart** 変数に、SliceStart というシステム変数に基づいた値が設定されます。 | いいえ |

### <a name="json-example"></a>JSON の例

```json
{
  "name": "ADFTutorialPipelineCustom",
  "properties": {
    "description": "Use custom activity",
    "activities": [
      {
        "Name": "MyDotNetActivity",
        "Type": "DotNetActivity",
        "Inputs": [
          {
            "Name": "InputDataset"
          }
        ],
        "Outputs": [
          {
            "Name": "OutputDataset"
          }
        ],
        "LinkedServiceName": "AzureBatchLinkedService",
        "typeProperties": {
          "AssemblyName": "MyDotNetActivity.dll",
          "EntryPoint": "MyDotNetActivityNS.MyDotNetActivity",
          "PackageLinkedService": "AzureStorageLinkedService",
          "PackageFile": "customactivitycontainer/MyDotNetActivity.zip",
          "extendedProperties": {
            "SliceStart": "$$Text.Format('{0:yyyyMMddHH-mm}', Time.AddMinutes(SliceStart, 0))"
          }
        },
        "Policy": {
          "Concurrency": 2,
          "ExecutionPriorityOrder": "OldestFirst",
          "Retry": 3,
          "Timeout": "00:30:00",
          "Delay": "00:00:00"
        }
      }
    ],
    "start": "2016-11-16T00:00:00",
    "end": "2016-11-16T05:00:00",
    "isPaused": false
  }
}
```

詳細については、[Data Factory でのカスタム アクティビティの使用](data-factory-use-custom-activities.md)に関する記事を参照してください。

## <a name="next-steps"></a>次の手順
次のチュートリアルを参照してください。

- [チュートリアル: コピー アクティビティを含んだパイプラインを作成する](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)
- [チュートリアル: Hive アクティビティを含んだパイプラインを作成する](data-factory-build-your-first-pipeline.md)
