---
title: パイプラインの作成とスケジュール、データ ファクトリのアクティビティの連鎖 | Microsoft Docs
description: Azure Data Factory でデータ パイプラインを作成してデータを移動および変換する方法について説明します。 データ主導ワークフローを作成して、すぐに使える情報を生成します。
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
ms.assetid: 13b137c7-1033-406f-aea7-b66f25b313c0
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: shlo
robots: noindex
ms.openlocfilehash: 58db6f9903c4dc02c2d76f3784b004972621a000
ms.sourcegitcommit: 64798b4f722623ea2bb53b374fb95e8d2b679318
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/11/2019
ms.locfileid: "67836494"
---
# <a name="pipelines-and-activities-in-azure-data-factory"></a>Azure Data Factory のパイプラインとアクティビティ
> [!div class="op_single_selector" title1="使用している Data Factory サービスのバージョンを選択してください:"]
> * [Version 1](data-factory-create-pipelines.md)
> * [バージョン 2 (最新バージョン)](../concepts-pipelines-activities.md)

> [!NOTE]
> この記事は、Data Factory のバージョン 1 に適用されます。 現在のバージョンの Data Factory サービスを使用している場合は、[V2 のパイプライン](../concepts-pipelines-activities.md)に関するページを参照してください。

この記事では、Azure Data Factory のパイプラインとアクティビティの概要、およびそれらを利用して、データ移動シナリオやデータ処理シナリオ用のエンド ツー エンドのデータ主導ワークフローを作成する方法について説明します。

> [!NOTE]
> この記事では、 [Azure Data Factory の概要](data-factory-introduction.md)に関する記事が確認済みであることを前提としています。 データ ファクトリを実際に作成したことがない場合は、[データ変換のチュートリアル](data-factory-build-your-first-pipeline.md)や [データ移動のチュートリアル](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)を行うと、この記事をより深く理解することができます。

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="overview"></a>概要
データ ファクトリは、1 つまたは複数のパイプラインを持つことができます。 パイプラインは、1 つのタスクを連携して実行するアクティビティの論理的なグループです。 パイプライン内の複数のアクティビティは、データに対して実行するアクションを定義します。 たとえば、コピー アクティビティを使用して、オンプレミス SQL Server から Azure Blob Storage にデータをコピーできます。 次に、Azure HDInsight クラスターで Hive スクリプトを実行する Hive アクティビティを使用し、Blob ストレージのデータを処理/変換して出力データを生成します。 最後に、2 番目のコピー アクティビティを使用して、ビジネス インテリジェンス (BI) レポート ソリューションが構築されている Azure SQL Data Warehouse に出力データをコピーします。

アクティビティは 0 個以上の入力[データセット](data-factory-create-datasets.md)を受け取り、1 個以上の出力[データセット](data-factory-create-datasets.md)を生成できます。 次の図は、Data Factory でのパイプライン、アクティビティ、データセットの関係を示しています。

![パイプライン、アクティビティ、データセットの関係](media/data-factory-create-pipelines/relationship-pipeline-activity-dataset.png)

パイプラインを使用すると、各アクティビティを個別に管理するのではなく、セットとして管理できます。 たとえば、パイプライン内のアクティビティを個別に扱うのではなく、パイプラインのデプロイ、スケジュール設定、中断、再開を行うことができます。

Data Factory では、データ移動アクティビティとデータ変換アクティビティの 2 種類のアクティビティがサポートされています。 アクティビティは 0 個以上の入力[データセット](data-factory-create-datasets.md)を受け取り、1 個以上の出力データセットを生成できます。

入力データセットはパイプラインのアクティビティの入力を表し、出力データセットはアクティビティの出力を表します。 データセットは、テーブル、ファイル、フォルダー、ドキュメントなど、さまざまなデータ ストア内のデータを示します。 作成したデータセットは、パイプライン内のアクティビティで使用できます。 たとえば、データセットはコピー アクティビティまたは HDInsightHive アクティビティの入力/出力データセットとして使用できます。 データセットの詳細については、「[Azure Data Factory のデータセット](data-factory-create-datasets.md)」の記事を参照してください。

### <a name="data-movement-activities"></a>データ移動アクティビティ
Data Factory のコピー アクティビティは、ソース データ ストアからシンク データ ストアにデータをコピーします。 Data Factory は次のデータ ストアをサポートしています。 また、任意のソースのデータを任意のシンクに書き込むことができます。 データ ストアをクリックすると、そのストアとの間でデータをコピーする方法がわかります。

[!INCLUDE [data-factory-supported-data-stores](../../../includes/data-factory-supported-data-stores.md)]

> [!NOTE]
> \* が付いたデータ ストアは、オンプレミスと Azure IaaS のどちらでもサポートされます。ただし、オンプレミス/Azure IaaS のコンピューターに [Data Management Gateway](data-factory-data-management-gateway.md) をインストールする必要があります。

詳細については、[データ移動アクティビティ](data-factory-data-movement-activities.md)に関する記事を参照してください。

### <a name="data-transformation-activities"></a>データ変換アクティビティ
[!INCLUDE [data-factory-transformation-activities](../../../includes/data-factory-transformation-activities.md)]

詳細については、[データ変換アクティビティ](data-factory-data-transformation-activities.md)に関する記事を参照してください。

### <a name="custom-net-activities"></a>カスタム .NET アクティビティ
コピー アクティビティでサポートされていないデータ ストアとの間でデータを移動する必要がある場合、つまり独自のロジックを使用してデータを変換する場合は、 **カスタム .NET アクティビティ**を作成します。 カスタム アクティビティの作成と使用の詳細については、「 [Azure Data Factory パイプラインでカスタム アクティビティを使用する](data-factory-use-custom-activities.md)」をご覧ください。

## <a name="schedule-pipelines"></a>パイプラインのスケジュール設定
パイプラインは、**開始**時刻と**終了**時刻の間のみアクティブです。 開始時刻より前または終了時刻より後には実行されません。 パイプラインは、一時停止している場合、その開始時刻と終了時刻に関係なく実行されません。 パイプラインを実行するには、一時停止しないでください。 Azure Data Factory でのスケジュールと実行の方法については、「 [スケジュールと実行](data-factory-scheduling-and-execution.md) 」を参照してください。

## <a name="pipeline-json"></a>パイプライン JSON
パイプラインを JSON 形式で定義する方法について詳しく説明します。 パイプラインの一般的な構造は次のようになります。

```json
{
    "name": "PipelineName",
    "properties":
    {
        "description" : "pipeline description",
        "activities":
        [

        ],
        "start": "<start date-time>",
        "end": "<end date-time>",
        "isPaused": true/false,
        "pipelineMode": "scheduled/onetime",
        "expirationTime": "15.00:00:00",
        "datasets":
        [
        ]
    }
}
```

| タグ | 説明 | 必須 |
| --- | --- | --- |
| name |パイプラインの名前。 パイプラインが実行するアクションを表す名前を指定します。 <br/><ul><li>最大文字数:260</li><li>文字、数字、またはアンダースコア (\_) で始まる必要があります</li><li>次の文字は使用できません: "."、"+"、"?"、"/"、"<"、">"、"\*"、"%"、"&"、":"、"\\"</li></ul> |はい |
| description | パイプラインの用途を説明するテキストを指定します。 |はい |
| activities | **activities** セクションでは、1 つまたは複数のアクティビティを定義できます。 activities JSON 要素の詳細については、次のセクションを参照してください。 | はい |
| start | パイプラインの開始日時。 [ISO 形式](https://en.wikipedia.org/wiki/ISO_8601)にする必要があります。 (例: `2016-10-14T16:32:41Z`)。 <br/><br/>東部標準時などの現地時間を指定できます。 たとえば、`2016-02-27T06:00:00-05:00` は、東部標準時で午前 6 時です。<br/><br/>start プロパティと end プロパティで、パイプラインの有効期間を指定します。 出力スライスは、この有効期間にのみ生成されます。 |いいえ<br/><br/>end プロパティの値を指定する場合は、start プロパティの値も指定する必要があります。<br/><br/>パイプラインを作成するには、開始時間と終了時間の両方が空でもかまいません。 パイプラインを実行できる有効期間を設定するには、両方の値を指定する必要があります。 パイプラインの作成時に開始時間と終了時間を指定しない場合、後で Set-AzDataFactoryPipelineActivePeriod コマンドレットを使用して設定できます。 |
| end | パイプラインの終了日時。 ISO 形式で指定する必要があります。 次に例を示します。`2016-10-14T17:32:41Z` <br/><br/>東部標準時などの現地時間を指定できます。 たとえば、`2016-02-27T06:00:00-05:00` は、東部標準時で午前 6 時です。<br/><br/>無期限でパイプラインを実行するには、end プロパティの値として 9999-09-09 を指定します。 <br/><br/> パイプラインは、開始時刻と終了時刻の間のみアクティブです。 開始時刻より前または終了時刻より後には実行されません。 パイプラインは、一時停止している場合、その開始時刻と終了時刻に関係なく実行されません。 パイプラインを実行するには、一時停止しないでください。 Azure Data Factory でのスケジュールと実行の方法については、「 [スケジュールと実行](data-factory-scheduling-and-execution.md) 」を参照してください。 |いいえ <br/><br/>start プロパティの値を指定する場合は、end プロパティの値も指定する必要があります。<br/><br/>**start** プロパティの注意事項を参照してください。 |
| isPaused | true に設定すると、パイプラインは実行されません。 一時停止状態になります。 既定値 = false。 このプロパティを使用してパイプラインを有効または無効にすることができます。 |いいえ |
| pipelineMode | パイプラインの実行のスケジューリングを行うためのメソッドです。 使用可能な値: "Scheduled" (既定)、"Onetime"。<br/><br/>"Scheduled" は、パイプラインがアクティブな期間 (開始時刻と終了時刻) に応じて、指定された間隔で実行されることを意味します。 "Onetime" はパイプラインが 1回だけ実行されることを意味します。 現時点では、作成された Onetime パイプラインを変更または更新することはできません。 1 回限りの設定の詳細については、「[1 回限りのパイプライン](#onetime-pipeline)」を参照してください。 |いいえ |
| expirationTime | [ワンタイム パイプライン](#onetime-pipeline)の作成後に、パイプラインが有効であり、プロビジョニングされた状態が維持される必要がある時間。 パイプラインは、アクティブ、エラー、または保留中の実行がない限り、有効期限に達すると自動的に削除されます。 既定値: `"expirationTime": "3.00:00:00"`|いいえ |
| datasets |パイプラインで定義されたアクティビティで使用されるデータセットの一覧。 このプロパティは、このパイプラインに固有の、Data Factory 内で定義されていないデータセットを定義するために使用できます。 このパイプライン内で定義されているデータセットは、このパイプラインでのみ使用でき、共有することはできません。 詳細については、「 [範囲指定されたデータセット](data-factory-create-datasets.md#scoped-datasets) 」を参照してください。 |いいえ |

## <a name="activity-json"></a>アクティビティ JSON
**activities** セクションでは、1 つまたは複数のアクティビティを定義できます。 各アクティビティには、次のような最上位構造があります。

```json
{
    "name": "ActivityName",
    "description": "description",
    "type": "<ActivityType>",
    "inputs": "[]",
    "outputs": "[]",
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
| name | アクティビティの名前。 アクティビティが実行するアクションを表す名前を指定します。 <br/><ul><li>最大文字数:260</li><li>文字、数字、またはアンダースコア (\_) で始まる必要があります</li><li>次の文字は使用できません："."、"+"、"?"、"/"、"<"、">"、"*"、"%"、"&"、":"、"\\"</li></ul> |はい |
| description | アクティビティの用途を説明するテキスト。 |はい |
| type | アクティビティの種類。 アクティビティの種類については、[データ移動アクティビティ](#data-movement-activities)に関するセクションと、[データ変換アクティビティ](#data-transformation-activities)に関するセクションを参照してください。 |はい |
| inputs |アクティビティで使用される入力テーブル<br/><br/>`// one input table`<br/>`"inputs":  [ { "name": "inputtable1"  } ],`<br/><br/>`// two input tables` <br/>`"inputs":  [ { "name": "inputtable1"  }, { "name": "inputtable2"  } ],` |はい |
| outputs |アクティビティで使用される出力テーブル。<br/><br/>`// one output table`<br/>`"outputs":  [ { "name": "outputtable1" } ],`<br/><br/>`//two output tables`<br/>`"outputs":  [ { "name": "outputtable1" }, { "name": "outputtable2" }  ],` |はい |
| linkedServiceName |アクティビティで使用される、リンクされたサービスの名前。 <br/><br/>アクティビティでは、必要なコンピューティング環境にリンクする、リンクされたサービスの指定が必要な場合があります。 |HDInsight アクティビティおよび Azure Machine Learning バッチ スコアリング アクティビティの場合は "はい" <br/><br/>それ以外の場合は "いいえ" |
| typeProperties |**typeProperties** セクションのプロパティは、アクティビティの種類によって異なります。 アクティビティの typeProperties を確認するには、前のセクションでアクティビティのリンクをクリックしてください。 | いいえ |
| policy |アクティビティの実行時の動作に影響するポリシーです。 指定されていない場合は、既定のポリシーが使用されます。 |いいえ |
| scheduler | "scheduler" プロパティは、アクティビティのスケジュールを定義するために使用します。 サブプロパティは、 [データセットの availability](data-factory-create-datasets.md#dataset-availability)プロパティにあるサブプロパティと同じです。 |いいえ |

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

## <a name="sample-copy-pipeline"></a>コピー パイプラインのサンプル
次のサンプル パイプラインでは、 **Copy** in the **アクティビティ** 型のアクティビティが 1 つあります。 このサンプルでは、[コピー アクティビティ](data-factory-data-movement-activities.md)が、Azure BLOB Storage から Azure SQL データベースにデータをコピーします。

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
    "start": "2016-07-12T00:00:00Z",
    "end": "2016-07-13T00:00:00Z"
  }
}
```

以下の点に注意してください。

* activities セクションに、**type** が **Copy** に設定されたアクティビティが 1 つだけあります。
* アクティビティの入力を **InputDataset** に設定し、出力を **OutputDataset** に設定します。 JSON でのデータセットの定義の詳細については、[データセット](data-factory-create-datasets.md)に関する記事を参照してください。
* **typeProperties** セクションでは、ソースの種類として **BlobSource** が指定され、シンクの種類として **SqlSink** が指定されています。 データ ストアとの間でのデータの移動については、「[データ移動アクティビティ](#data-movement-activities)」セクションで、ソースまたはシンクとして使用するデータ ストアをクリックしてください。

このパイプライン作成の完全なチュートリアルについては、[Blob Storage から SQL Database へのデータのコピー](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)に関するチュートリアルを参照してください。

## <a name="sample-transformation-pipeline"></a>変換パイプラインのサンプル
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
        "start": "2016-04-01T00:00:00Z",
        "end": "2016-04-02T00:00:00Z",
        "isPaused": false
    }
}
```

以下の点に注意してください。

* activities セクションに、**type** が **HDInsightHive** に設定されたアクティビティが 1 つだけあります。
* Hive スクリプト ファイル **partitionweblogs.hql** は、Azure ストレージ アカウント (scriptLinkedService によって指定され、**AzureStorageLinkedService** という名前) および **adfgetstarted** コンテナーの **script** フォルダーに格納されます。
* `defines` セクションは、Hive 構成値 (例: `${hiveconf:inputtable}`、`${hiveconf:partitionedtable}`) として Hive スクリプトに渡される実行時設定を指定するために使用されます。

**typeProperties** セクションは、変換アクティビティごとに異なります。 変換アクティビティでサポートされる typeProperties については、[データ変換アクティビティ](#data-transformation-activities)の表で変換アクティビティをクリックしてください。

このパイプライン作成の完全なチュートリアルについては、「[チュートリアル:Hadoop クラスターを使用してデータを処理する最初のパイプラインを作成する](data-factory-build-your-first-pipeline.md)」を参照してください。

## <a name="multiple-activities-in-a-pipeline"></a>パイプライン内の複数アクティビティ
前の 2 つのサンプル パプラインには 1 つのアクティビティしか含まれていません。 パイプラインに複数のアクティビティを含めることができます。

パイプラインに複数のアクティビティがあり、アクティビティの出力が別のアクティビティの入力ではない場合は、アクティビティの入力データ スライスの準備ができれば、アクティビティが並列実行する可能性があります。

2 つのアクティビティを連鎖させるには、一方のアクティビティの出力データセットを、もう一方のアクティビティの入力データセットとして指定します。 2 つ目のアクティビティは、1 つ目のアクティビティが正常に完了した後にのみ実行されます。

![同じパイプラインでのアクティビティの連鎖](./media/data-factory-create-pipelines/chaining-one-pipeline.png)

このサンプルでは、パイプラインに 2 つのアクティビティ Activity1 と Activity2 が含まれています。 Activity1 は Dataset1 を入力として受け取り、出力として Dataset2 を生成します。 Activity2 は Dataset2 を入力として受け取り、出力として Dataset3 を生成します。 Activity1 の出力 (Dataset2) は Activity2 の入力であるため、Activity2 が実行されるのは、Activity1 が正常に完了して Dataset2 スライスを生成した後です。 Activity1 が何らかの理由で失敗し、Dataset2 スライスが生成されない場合、Activity 2 はそのスライス (9 AM から 10 AM など) に対して実行されません。

別のパイプラインに含まれるアクティビティを連鎖することもできます。

![2 つのパイプラインでのアクティビティの連鎖](./media/data-factory-create-pipelines/chaining-two-pipelines.png)

このサンプルの Pipeline1 には、Dataset1 を入力として受け取り、Dataset2 を出力として生成するアクティビティのみが含まれます。 Pipeline2 にも、Dataset2 を入力として受け取り、Dataset3 を出力として生成するアクティビティのみが含まれます。

詳細については、「 [スケジュールと実行](data-factory-scheduling-and-execution.md#multiple-activities-in-a-pipeline)」を参照してください。
## <a name="create-and-monitor-pipelines"></a>パイプラインの作成と監視
次のツールや SDK のいずれかを使用してパイプラインを作成できます。

- コピー ウィザード
- Visual Studio
- Azure PowerShell
- Azure Resource Manager テンプレート
- REST API
- .NET API

これらのツールや SDK のいずれかを使用してパイプラインを作成する詳しい手順については、次のチュートリアルを参照してください。

- [データ変換アクティビティを含むパイプラインの作成](data-factory-build-your-first-pipeline.md)
- [データ移動アクティビティを含むパイプラインの作成](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)

パイプラインを作成/デプロイしたら、Azure Portal のブレードまたは監視と管理アプリを使用して、パイプラインを管理および監視できます。 詳しい手順については、次のトピックを参照してください。

- [Azure ポータルのブレードを使用したパイプラインの監視と管理](data-factory-monitor-manage-pipelines.md)
- [監視と管理アプリを使用したパイプラインの監視と管理](data-factory-monitor-manage-app.md)

## <a name="onetime-pipeline"></a>1 回限りのパイプライン
パイプライン定義で指定した開始時刻から終了時刻までの間に定期的に (毎時、毎日など) 実行するパイプラインを作成し、スケジュールを設定することができます。 詳細については、スケジュール設定のアクティビティを参照してください。 1 回だけ実行するパイプラインを作成することもできます。 これを行うには、次の JSON サンプルに示すように、パイプライン定義の **pipelineMode** プロパティを **onetime** に設定します。 このプロパティの既定値は **scheduled**です。

```json
{
    "name": "CopyPipeline",
    "properties": {
        "activities": [
            {
                "type": "Copy",
                "typeProperties": {
                    "source": {
                        "type": "BlobSource",
                        "recursive": false
                    },
                    "sink": {
                        "type": "BlobSink",
                        "writeBatchSize": 0,
                        "writeBatchTimeout": "00:00:00"
                    }
                },
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
                "name": "CopyActivity-0"
            }
        ],
        "pipelineMode": "OneTime"
    }
}
```

以下の点に注意してください。

* パイプラインの**開始**時刻と**終了**時刻は指定しません。
* Data Factory で値が使用されない場合でも、入力データセットと出力データセットの**abailability** (**freqeuncy** と **interval**) は指定します。
* ダイアグラム ビューには、1 回限りのパイプラインは表示されません。 この動作は仕様です。
* 1 回限りのパイプラインを更新することはできません。 1 回限りのパイプラインを複製して名前を変更し、プロパティを更新してデプロイすることで別のパイプラインを作成することができます。

## <a name="next-steps"></a>次の手順
- データセットの詳細については、[データセットの作成](data-factory-create-datasets.md)に関する記事を参照してください。
- パイプラインのスケジュール設定と実行の方法については、[Azure Data Factory でのスケジュール設定と実行](data-factory-scheduling-and-execution.md)に関する記事を参照してください。
