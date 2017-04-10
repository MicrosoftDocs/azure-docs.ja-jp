---
title: "パイプラインの作成とスケジュール、データ ファクトリのアクティビティの連鎖 | Microsoft Docs"
description: "Azure Data Factory でデータ パイプラインを作成してデータを移動および変換する方法について説明します。 データ主導ワークフローを作成して、すぐに使える情報を生成します。"
keywords: "データ パイプライン, データ主導ワークフロー"
services: data-factory
documentationcenter: 
author: sharonlo101
manager: jhubbard
editor: monicar
ms.assetid: 13b137c7-1033-406f-aea7-b66f25b313c0
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/25/2017
ms.author: shlo
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: d841c57dc736f7d690a6dc97863b7568365fd01a


---
# <a name="pipelines-and-activities-in-azure-data-factory"></a>Azure Data Factory のパイプラインとアクティビティ
この記事では、Azure Data Factory のパイプラインとアクティビティの概要、およびそれらを利用して、データ移動シナリオやデータ処理シナリオ用のエンド ツー エンドのデータ主導ワークフローを作成する方法について説明します。  

> [!NOTE]
> この記事では、 [Azure Data Factory の概要](data-factory-introduction.md)に関する記事が確認済みであることを前提としています。 データ ファクトリを実際に作成したことがない場合は、 [初めてのデータ ファクトリを作成する](data-factory-build-your-first-pipeline.md) チュートリアルを行うと、この記事をより深く理解することができます。  
> 
> 

## <a name="what-is-a-data-pipeline"></a>データ パイプラインとは
**パイプライン**は、論理的に関連付けられた**アクティビティ**のグループです。 パイプラインは、タスクを実行するユニットにアクティビティをグループ化するために使用されます。 パイプラインをより深く理解するには、最初にアクティビティを理解する必要があります。 

## <a name="what-is-an-activity"></a>アクティビティとは
アクティビティは、データに対して実行するアクションを定義します。 各アクティビティは、入力として 0 個以上の [データセット](data-factory-create-datasets.md) を受け取り、出力として 1 つまたは複数のデータセットを生成します。 

たとえば、コピー アクティビティを使用して、データ ストア間のデータのコピーを調整できます。 同様に、Azure HDInsight クラスターに対して Hive クエリを実行する HDInsight Hive アクティビティを使用して、データを変換できます。 Azure Data Factory には、[データ変換](data-factory-data-transformation-activities.md)および[データ移動](data-factory-data-movement-activities.md)のための広範なアクティビティがあります。 また、カスタム .NET アクティビティを作成して独自のコードを実行することもできます。 

## <a name="sample-copy-pipeline"></a>コピー パイプラインのサンプル
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
    "start": "2016-07-12T00:00:00Z",
    "end": "2016-07-13T00:00:00Z"
  }
} 
```

次の点に注意してください。

* activities セクションに、**type** が **Copy** に設定されたアクティビティが 1 つだけあります。
* アクティビティの入力を **InputDataset** に設定し、出力を **OutputDataset** に設定します。
* **typeProperties** セクションでは、ソースの種類として **BlobSource** が指定され、シンクの種類として **SqlSink** が指定されています。

このパイプライン作成の完全なチュートリアルについては、 [Blob Storage から SQL Database へのデータ コピーのチュートリアル](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)をご覧ください。 

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

次の点に注意してください。 

* activities セクションに、**type** が **HDInsightHive** に設定されたアクティビティが 1 つだけあります。
* Hive スクリプト ファイル **partitionweblogs.hql** は、Azure ストレージ アカウント (scriptLinkedService によって指定され、**AzureStorageLinkedService** という名前) および **adfgetstarted** コンテナーの **script** フォルダーに格納されます。
* **defines** セクションは、Hive 構成値 (例: ${hiveconf:inputtable}、${hiveconf:partitionedtable}) として Hive スクリプトに渡される実行時設定を指定するために使用されます。

このパイプライン作成する完全なチュートリアルについては、「 [チュートリアル: Hadoop クラスターを使用してデータを処理する最初のパイプラインを作成する](data-factory-build-your-first-pipeline.md)」を参照してください。 

## <a name="chaining-activities"></a>連鎖するアクティビティ
パイプラインに複数のアクティビティがあり、アクティビティの出力が別のアクティビティの入力ではない場合は、アクティビティの入力データ スライスの準備ができれば、アクティビティが並列実行する可能性があります。 

2 つのアクティビティを連鎖させるには、一方のアクティビティの出力データセットを、もう一方のアクティビティの入力データセットとして指定します。 このとき、同じパイプラインのアクティビティと、別のパイプラインのアクティビティを指定できます。 2 つ目のアクティビティは、1 つ目のアクティビティが正常に完了した後にのみ実行されます。 

たとえば、次の場合を考えてみましょう。

1. パイプライン P1 には、外部入力データセット D1 を必要とし、**出力**データセット **D2** を生成するアクティビティ A1 があります。
2. パイプライン P2 には、データセット **D2** からの**入力**を必要とし、出力データセット D3 を生成するアクティビティ A2 があります。

このシナリオでは、外部データが使用可能なときにアクティビティ A1 が実行され、スケジュールされた可用性の頻度に達します。  D2 のスケジュールされたスライスが使用可能になると、アクティビティ A2 が実行され、スケジュールされた可用性の頻度に達します。 データセット D2 のスライスのいずれかでエラーが発生した場合、スライスが使用可能になるまで、そのスライスに対して A2 は実行されません。

ダイアグラム ビュー

![2 つのパイプラインでアクティビティを連鎖させる](./media/data-factory-create-pipelines/chaining-two-pipelines.png)

同じパイプラインに両方のアクティビティがあるダイアグラム ビューは、次のようになります。 

![同じパイプラインでアクティビティを連鎖させる](./media/data-factory-create-pipelines/chaining-one-pipeline.png)

詳細については、「 [スケジュールと実行](#chaining-activities)」を参照してください。 

## <a name="scheduling-and-execution"></a>スケジュールと実行
パイプラインとアクティビティについては理解できました。 また、Azure Data Factory でのアクティビティの定義と概要も説明しました。 次に、実行方法を説明します。 

パイプラインは、開始時刻と終了時刻の間のみアクティブです。 開始時刻より前または終了時刻より後には実行されません。 パイプラインは、一時停止している場合、その開始時刻と終了時刻に関係なく実行されません。 パイプラインを実行するには、一時停止しないでください。 実際には、実行されるのはパイプラインではありません。 実行されるのはパイプライン内のアクティビティです。 ただし、アクティビティはパイプラインの全体的なコンテキストで実行されます。 

Azure Data Factory でのスケジュールと実行の方法については、「 [スケジュールと実行](data-factory-scheduling-and-execution.md) 」を参照してください。

## <a name="create-pipelines"></a>パイプラインを作成する
Azure Data Factory は、(1 つ以上のアクティビティを含む) パイプラインを作成およびデプロイするさまざまなメカニズムを提供します。 

### <a name="using-azure-portal"></a>Azure Portal の使用
Azure ポータルで Data Factory エディターを使用して、パイプラインを作成できます。 エンド ツー エンド チュートリアルについては、 [Azure Data Factory の使用 (Data Factory エディター)](data-factory-build-your-first-pipeline-using-editor.md) に関するページをご覧ください。 

### <a name="using-visual-studio"></a>Visual Studio の使用
Visual Studio を使用して、パイプラインを作成して Azure Data Factory にデプロイできます。 エンド ツー エンド チュートリアルについては、 [Azure Data Factory の使用 (Visual Studio)](data-factory-build-your-first-pipeline-using-vs.md) に関するページをご覧ください。 

### <a name="using-azure-powershell"></a>Azure PowerShell の使用
Azure PowerShell を使用して、Azure Data Factory にパイプラインを作成できます。 たとえば、c:\DPWikisample.json のファイルでパイプライン JSON を定義しておきます。 次の例のように Azure Data Factory インスタンスにそれをアップロードできます。

```PowerShell
New-AzureRmDataFactoryPipeline -ResourceGroupName ADF -Name DPWikisample -DataFactoryName wikiADF -File c:\DPWikisample.json
```

パイプラインを備えたデータ ファクトリを作成するためのエンド ツー エンド チュートリアルについては、 [Azure PowerShell を使用した Azure Data Factory の使用](data-factory-build-your-first-pipeline-using-powershell.md) に関する記事を参照してください。 

### <a name="using-net-sdk"></a>.NET SDK の使用
.NET SDK を使用してパイプラインを作成およびデプロイすることもできます。 このメカニズムを使用すると、プログラムでパイプラインを作成できます。 詳細については、 [プログラムによるデータ ファクトリの作成、管理、および監視](data-factory-create-data-factories-programmatically.md)に関するページをご覧ください。 

### <a name="using-azure-resource-manager-template"></a>Azure Resource Manager テンプレートの使用
Azure Resource Manager テンプレートを使用してパイプラインを作成およびデプロイすることができます。 詳細については、 [Azure Data Factory の使用 (Azure Resource Manager)](data-factory-build-your-first-pipeline-using-arm.md)に関するページをご覧ください。 

### <a name="using-rest-api"></a>REST API の使用
REST API を使用してパイプラインを作成およびデプロイすることもできます。 このメカニズムを使用すると、プログラムでパイプラインを作成できます。 詳細については、「 [パイプラインの作成または更新](https://msdn.microsoft.com/library/azure/dn906741.aspx)」を参照してください。 

## <a name="monitor-and-manage-pipelines"></a>パイプラインの監視と管理
パイプラインをデプロイした後は、パイプライン、スライス、実行を管理および監視できます。 詳細については、「 [パイプラインの監視と管理](data-factory-monitor-manage-pipelines.md)」を参照してください。

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
        "end": "<end date-time>"
    }
}
```

**activities** セクションでは、1 つまたは複数のアクティビティを定義できます。 各アクティビティには、次のような最上位構造があります。

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
    }
    "scheduler":
    {
    }
}
```

次の表では、アクティビティとパイプラインの JSON 定義で使用されるプロパティを説明します。

| タグ | Description | 必須 |
| --- | --- | --- |
| name |アクティビティまたはパイプラインの名前です。 アクティビティまたはパイプラインで実行するように構成されているアクションを表す名前を指定します。<br/><ul><li>最大文字数：260文字</li><li>文字、数字、アンダー スコア (_) のいずれかで始める必要があります。</li><li>次の文字は使用できません："."、"+"、"?"、"/"、"<"、">"、"*"、"%"、"&"、":"、"\\"</li></ul> |はい |
| Description |アクティビティまたはパイプラインの用途を説明するテキストです。 |はい |
| type |アクティビティの種類を指定します。 アクティビティの種類については、[データ移動アクティビティ](data-factory-data-movement-activities.md)に関する記事と、[データ変換アクティビティ](data-factory-data-transformation-activities.md)に関する記事を参照してください。 |はい |
| inputs |アクティビティで使用される入力テーブル<br/><br/>// 1 つの入力テーブル<br/>"inputs":  [ { "name": "inputtable1"  } ],<br/><br/>// 2 つの入力テーブル <br/>"inputs":  [ { "name": "inputtable1"  }, { "name": "inputtable2"  } ], |はい |
| outputs |アクティビティによって使用される出力テーブル// 1 つの出力テーブル<br/>"outputs":  [ { "name": “outputtable1” } ],<br/><br/>// 2 つの出力テーブル<br/>"outputs":  [ { "name": “outputtable1” }, { "name": “outputtable2” }  ], |はい |
| linkedServiceName |アクティビティで使用される、リンクされたサービスの名前。 <br/><br/>アクティビティでは、必要なコンピューティング環境にリンクする、リンクされたサービスの指定が必要な場合があります。 |HDInsight アクティビティおよび Azure Machine Learning バッチ スコアリング アクティビティの場合は "はい" <br/><br/>それ以外の場合は "いいえ" |
| typeProperties |TypeProperties セクションのプロパティは、アクティビティの種類によって異なります。 |なし |
| policy |アクティビティの実行時の動作に影響するポリシーです。 指定されていない場合は、既定のポリシーが使用されます。 |なし |
| start |パイプラインの開始日時。 [ISO 形式](http://en.wikipedia.org/wiki/ISO_8601)にする必要があります。 (例: 2014-10-14T16:32:41Z)。 <br/><br/>東部標準時などの現地時間を指定できます。 例："2016-02-27T06:00:00**-05:00**" は、東部標準時で午前 6 時です。<br/><br/>start プロパティと end プロパティで、パイプラインの有効期間を指定します。 出力スライスは、この有効期間にのみ生成されます。 |いいえ<br/><br/>end プロパティの値を指定する場合は、start プロパティの値も指定する必要があります。<br/><br/>パイプラインを作成するには、開始時間と終了時間の両方が空でもかまいません。 パイプラインを実行できる有効期間を設定するには、両方の値を指定する必要があります。 パイプラインの作成時に開始時間と終了時間を指定しない場合、後で Set-AzureRmDataFactoryPipelineActivePeriod コマンドレットを使用して設定できます。 |
| end |パイプラインの終了日時。 ISO 形式で指定する必要があります。 例: 2014-10-14T17:32:41Z <br/><br/>東部標準時などの現地時間を指定できます。 例："2016-02-27T06:00:00**-05:00**" は、東部標準時で午前 6 時です。<br/><br/>無期限でパイプラインを実行するには、end プロパティの値として 9999-09-09 を指定します。 |いいえ <br/><br/>start プロパティの値を指定する場合は、end プロパティの値も指定する必要があります。<br/><br/>**start** プロパティの注意事項を参照してください。 |
| isPaused |true に設定すると、パイプラインは実行されません。 既定値 = false。 このプロパティを使用して有効または無効にすることができます。 |なし |
| scheduler |"scheduler" プロパティは、アクティビティのスケジュールを定義するために使用します。 サブプロパティは、 [データセットの availability](data-factory-create-datasets.md#Availability)プロパティにあるサブプロパティと同じです。 |なし |
| pipelineMode |パイプラインの実行のスケジューリングを行うためのメソッドです。 使用可能な値: "Scheduled" (既定)、"Onetime"。<br/><br/>"Scheduled" は、パイプラインがアクティブな期間 (開始時刻と終了時刻) に応じて、指定された間隔で実行されることを意味します。 "Onetime" はパイプラインが 1回だけ実行されることを意味します。 現時点では、作成された Onetime パイプラインを変更または更新することはできません。 1 回限りの設定の詳細については、「[1 回限りのパイプライン](data-factory-scheduling-and-execution.md#onetime-pipeline)」を参照してください。 |いいえ |
| expirationTime |パイプラインの作成後に、パイプラインが有効であり、プロビジョニングされた状態が維持される必要がある時間。 パイプラインは、アクティブ、エラー、または保留中の実行がない限り、有効期限に達すると自動的に削除されます。 |なし |
| データセット |パイプラインで定義されたアクティビティで使用されるデータセットの一覧。 このプロパティは、このパイプラインに固有の、Data Factory 内で定義されていないデータセットを定義するために使用できます。 このパイプライン内で定義されているデータセットは、このパイプラインでのみ使用でき、共有することはできません。 詳細については、「 [範囲指定されたデータセット](data-factory-create-datasets.md#scoped-datasets) 」を参照してください。 |なし |

### <a name="policies"></a>ポリシー
ポリシーはアクティビティの実行時の動作に影響します。具体的には、テーブルのスライスがいつ処理されるかです。 次の表で詳細に説明します。

| プロパティ | 使用できる値 | 既定値 | Description |
| --- | --- | --- | --- |
| 同時実行 |整数 <br/><br/>最大値: 10 |1 |アクティビティの同時実行の数。<br/><br/>異なるスライスで実行できる並列アクティビティ実行の数を決定します。 たとえば、アクティビティが大量のデータを処理する必要がある場合、同時実行の値を大きくするとデータ処理が速くなります。 |
| executionPriorityOrder |NewestFirst<br/><br/>OldestFirst |OldestFirst |処理されるデータ スライスの順序を決定します。<br/><br/>たとえば、2 個のスライス (午後 4 時と午後 5 時の実行) があり、どちらも実行が保留されているとします。 executionPriorityOrder を NewestFirst に設定すると、午後 5 時のスライスが最初に処理されます。 同様に、executionPriorityORder を OldestFIrst に設定すると、午後 4 時のスライスが処理されます。 |
| retry |整数<br/><br/>最大値は 10 |0 |スライスのデータ処理が失敗としてマークされるまでの再試行回数。 データ スライスのアクティビティの実行は、指定された再試行回数まで再試行されます。 再試行は、障害発生後にできるだけ早く行われます。 |
| timeout |TimeSpan |00:00:00 |アクティビティのタイムアウト。 例： 00:10:00 (タイムアウトが 10 分であることを意味します)<br/><br/>値が指定されていない場合、または値が 0 の場合は、タイムアウトは無期限です。<br/><br/>スライスのデータ処理時間がタイムアウト値を超えた場合、処理は取り消され、システムは処理の再試行を試みます。 再試行の回数は、retry プロパティで指定します。 タイムアウトが発生すると、ステータスは TimedOut に設定されます。 |
| delay |TimeSpan |00:00:00 |スライスのデータ処理を開始する前の遅延時間を指定します。<br/><br/>データ スライスのアクティビティの実行は、予想実行時刻を Delay だけ過ぎてから開始します。<br/><br/>例: 00:10:00 (10 分の遅延を意味します) |
| longRetry |整数<br/><br/>最大値: 10 |1 |スライスの実行が失敗になるまでの、長い再試行の回数。<br/><br/>longRetry の試行は longRetryInterval の間隔で行われます。 再試行間隔の時間を指定する必要がある場合は、longRetry を使用します。 Retry と longRetry の両方を指定すると、各 longRetry に Retry が含まれ、最大再試行回数は Retry * longRetry になります。<br/><br/>たとえば、アクティビティ ポリシーに次のような設定があるとします。<br/>Retry: 3<br/>longRetry: 2<br/>longRetryInterval: 01:00:00<br/><br/>実行するスライスは 1 つだけ (ステータスは Waiting)、アクティビティ実行は毎回失敗するとします。 最初に 3 つの連続する試行があります。 試行するたびに、スライスの状態は Retry になります。 最初の 3 つの試行が終わると、スライスの状態は LongRetry になります。<br/><br/>1 時間 (longRetryInteval の値) が経過した後、再度 3 回連続して試行されます。 その後、スライスの状態は Failed になり、それ以上再試行は行われません。 したがって、全部で 6 回試行されます。<br/><br/>いずれかの実行が成功すると、スライスの状態は Ready になり、それ以上再試行は行われません。<br/><br/>longRetry は、依存するデータがいつ到着するかわからない場合、またはデータ処理が行われる環境全体が当てにならない場合などに使用します。 このような場合、連続して再試行しても意味がなく、時間をおくと成功することがあります。<br/><br/>注意: longRetry または longRetryInterval に大きい値を設定しないでください。 通常、大きな値は、その他のシステムの問題があることを意味します。 |
| longRetryInterval |TimeSpan |00:00:00 |長い再試行の間の遅延 |

## <a name="next-steps"></a>次のステップ
* [Azure Data Factory でのスケジュールと実行](data-factory-scheduling-and-execution.md)について理解してください。  
* Azure Data Factory での[データの移動](data-factory-data-movement-activities.md)および[データ変換機能](data-factory-data-transformation-activities.md)について読んでください。
* [Azure Data Factory での管理と監視](data-factory-monitor-manage-pipelines.md)について理解してください。
* [初めてのパイプラインをビルドしてデプロイしてください](data-factory-build-your-first-pipeline.md)。 




<!--HONumber=Nov16_HO3-->


