---
title: Data Factory を使用したスケジュール設定と実行
description: Azure Data Factory アプリケーション モデルのスケジュール設定と実行の側面について説明します。
author: dcstwh
ms.author: weetok
ms.reviewer: jburchel
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/10/2018
ms.openlocfilehash: f613a14f84999fced888ae72de11328bc7c90811
ms.sourcegitcommit: f611b3f57027a21f7b229edf8a5b4f4c75f76331
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/22/2021
ms.locfileid: "104780006"
---
# <a name="data-factory-scheduling-and-execution"></a>Data Factory のスケジュール設定と実行
> [!NOTE]
> この記事は、Data Factory のバージョン 1 に適用されます。 現在のバージョンの Data Factory サービスを使用している場合は、[パイプラインの実行とトリガー](../concepts-pipeline-execution-triggers.md)に関する記事を参照してください。

この記事では、Azure Data Factory アプリケーション モデルのスケジュール設定と実行の側面について説明します。 この記事は、Data Factory アプリケーション モデルの概念の基本事項 (アクティビティ、パイプライン、リンクされたサービス、データセットなど) を理解していることを前提としています。 Azure Data Factory の基本的な概念については、次の記事を参照してください。

* [Data Factory の概要](data-factory-introduction.md)
* [パイプライン](data-factory-create-pipelines.md)
* [データセット](data-factory-create-datasets.md) 

## <a name="start-and-end-times-of-pipeline"></a>パイプラインの開始時刻と終了時刻
パイプラインは、**開始** 時刻と **終了** 時刻の間のみアクティブです。 開始時刻より前または終了時刻より後には実行されません。 パイプラインは、一時停止している場合、その開始時刻と終了時刻に関係なく実行されません。 パイプラインを実行するには、一時停止しないでください。 これらの設定 (開始、終了、一時停止) は、パイプライン定義にあります。 

```json
"start": "2017-04-01T08:00:00Z",
"end": "2017-04-01T11:00:00Z"
"isPaused": false
```

これらのプロパティの詳細については、[パイプラインの作成](data-factory-create-pipelines.md)に関する記事を参照してください。 


## <a name="specify-schedule-for-an-activity"></a>アクティビティのスケジュールの指定
実行されるのはパイプラインではありません。 パイプラインのコンテキスト全体で実行されるのはパイプライン内のアクティビティです。 アクティビティ JSON の **scheduler** セクションを使用して、アクティビティの定期的なスケジュールを指定できます。 たとえば、次のように毎時間実行されるアクティビティをスケジュールできます。  

```json
"scheduler": {
    "frequency": "Hour",
    "interval": 1
},
```

次の図に示すように、アクティビティのスケジュールを指定すると、パイプラインの開始時刻と終了時刻を含む一連のタンブリング ウィンドウが作成されます。 タンブリング ウィンドウとは、固定サイズで重複しない一連の連続する時間間隔です。 アクティビティに対するこれらの論理的なタンブリング ウィンドウは、**アクティビティ ウィンドウ** と呼ばれます。

![アクティビティ スケジューラの例](media/data-factory-scheduling-and-execution/scheduler-example.png)

アクティビティの **scheduler** プロパティはオプションです。 このプロパティを指定する場合は、アクティビティの出力データセットの定義で指定するパターンと一致させる必要があります。 現在、スケジュールは出力データセットによって開始されます。 そのため、アクティビティが出力を生成しない場合でも、出力データセットを作成する必要があります。 

## <a name="specify-schedule-for-a-dataset"></a>データセットのスケジュールの指定
Data Factory パイプラインのアクティビティは 0 個以上の入力 **データセット** を受け取り、1 個以上の出力データセットを生成できます。 アクティビティでは、データセット定義の **availability** セクションを使用して、入力データを利用できる、または出力データが生成されるパターンを指定できます。 

**availability** セクションの **frequency** には、時間単位を指定します。 frequency の許容値は、Minute、Hour、Day、Week、および Month です。 availability セクションの **interval** プロパティには、frequency の乗数を指定します。 たとえば、出力データセットの frequency が Day に、interval が 1 に設定されている場合、出力データが毎日生成されます。 frequency に Minute を指定する場合は、interval を 15 以上に設定することをお勧めします。 

次の例では、入力データを 1 時間ごとに利用でき、出力データが 1 時間ごとに生成されます (`"frequency": "Hour", "interval": 1`)。 

**入力データセット:** 

```json
{
    "name": "AzureSqlInput",
    "properties": {
        "published": false,
        "type": "AzureSqlTable",
        "linkedServiceName": "AzureSqlLinkedService",
        "typeProperties": {
            "tableName": "MyTable"
        },
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": true,
        "policy": {}
    }
}
```


**出力データセット**

```json
{
    "name": "AzureBlobOutput",
    "properties": {
        "published": false,
        "type": "AzureBlob",
        "linkedServiceName": "StorageLinkedService",
        "typeProperties": {
            "folderPath": "mypath/{Year}/{Month}/{Day}/{Hour}",
            "format": {
                "type": "TextFormat"
            },
            "partitionedBy": [
                { "name": "Year", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyy" } },
                { "name": "Month", "value": { "type": "DateTime", "date": "SliceStart", "format": "MM" } },
                { "name": "Day", "value": { "type": "DateTime", "date": "SliceStart", "format": "dd" } },
                { "name": "Hour", "value": { "type": "DateTime", "date": "SliceStart", "format": "HH" }}
            ]
        },
        "availability": {
            "frequency": "Hour",
            "interval": 1
        }
    }
}
```

現在、**スケジュールは出力データセットによって開始されます**。 つまり、実行時にアクティビティを実行するために、出力データセットに指定されたスケジュールが使用されます。 そのため、アクティビティが出力を生成しない場合でも、出力データセットを作成する必要があります。 アクティビティが入力を受け取らない場合は、入力データセットの作成を省略できます。 

次のパイプライン定義では、**scheduler** プロパティを使用して、アクティビティのスケジュールを指定しています。 このプロパティは省略可能です。 現在、アクティビティのスケジュールは、出力データセットに指定されているスケジュールと一致する必要があります。
 
```json
{
    "name": "SamplePipeline",
    "properties": {
        "description": "copy activity",
        "activities": [
            {
                "type": "Copy",
                "name": "AzureSQLtoBlob",
                "description": "copy activity",
                "typeProperties": {
                    "source": {
                        "type": "SqlSource",
                        "sqlReaderQuery": "$$Text.Format('select * from MyTable where timestampcolumn >= \\'{0:yyyy-MM-dd HH:mm}\\' AND timestampcolumn < \\'{1:yyyy-MM-dd HH:mm}\\'', WindowStart, WindowEnd)"
                    },
                    "sink": {
                        "type": "BlobSink",
                        "writeBatchSize": 100000,
                        "writeBatchTimeout": "00:05:00"
                    }
                },
                "inputs": [
                    {
                        "name": "AzureSQLInput"
                    }
                ],
                "outputs": [
                    {
                        "name": "AzureBlobOutput"
                    }
                ],
                "scheduler": {
                    "frequency": "Hour",
                    "interval": 1
                }
            }
        ],
        "start": "2017-04-01T08:00:00Z",
        "end": "2017-04-01T11:00:00Z"
    }
}
```

この例では、アクティビティはパイプラインの開始時刻から終了時刻まで 1 時間ごとに実行されます。 出力データは、3 つの時間枠 (午前 8 時～午前 9 時、午前 9 時～午前 10 時、午前 10 時～午後 11 時) に 1 回ずつ生成されます。 

アクティビティ実行で使用および生成されるデータの各ユニットは、**データ スライス** と呼ばれます。 次の図は、入力データセットと出力データセットが 1 つずつ含まれているアクティビティの例です。 

![可用性スケジューラ](./media/data-factory-scheduling-and-execution/availability-scheduler.png)

図には、入力データセットと出力データセットの 1 時間ごとのデータ スライスが示されています。 この図には、処理可能な入力スライスが 3 つあります。 10-11 AM のアクティビティが実行中であり、10-11 AM 出力スライスが生成されます。 

現在のスライスに関連付けられた時間間隔にアクセスするには、データセット JSON で変数 [SliceStart](data-factory-functions-variables.md#data-factory-system-variables) と [SliceEnd](data-factory-functions-variables.md#data-factory-system-variables) を使用します。 同様に、アクティビティ ウィンドウに関連付けられている時間間隔には、WindowStart と WindowEnd を使用してアクセスできます。 アクティビティのスケジュールは、アクティビティの出力データセットのスケジュールと一致する必要があります。 そのため、SliceStart と SliceEnd の値はそれぞれ WindowStart と WindowEnd の値と同じです。 これらの変数の詳細については、[Data Factory の関数およびシステム変数](data-factory-functions-variables.md#data-factory-system-variables)に関する記事を参照してください。  

アクティビティ JSON では、これらの変数をさまざまな目的で使用します。 たとえば、これらの変数を使用して、入出力データセットから時系列データを表すデータ (例:午前 8 時～午前 9 時) を選択できます。 この例でも **WindowStart** と **WindowEnd** を使用して、アクティビティ実行の関連データを選択し、適切な **folderPath** を持つ BLOB にコピーします。 **folderPath** は、1 時間ごとに個別のフォルダーを使用するようにパラメーター化されています。  

前の例では、入力データセットと出力データセットに指定されているスケジュールは同じです (1 時間ごと)。 アクティビティの入力データセットを異なる頻度で利用できる場合 (15 分ごとなど) でも、アクティビティのスケジュールは出力データセットによって開始されるため、この出力データセットを生成するアクティビティは 1 時間に 1 回実行されます。 詳細については、「[頻度が異なるデータセットのモデル化](#model-datasets-with-different-frequencies)」を参照してください。

## <a name="dataset-availability-and-policies"></a>データセットの可用性とポリシー
データセット定義の availability セクションの frequency プロパティと interval プロパティを使用する方法を見てきました。 アクティビティのスケジュールと実行に影響を与えるプロパティは他にもいくつかあります。 

### <a name="dataset-availability"></a>データセットの可用性 
次の表では、**availability** セクションで使用できるプロパティについて説明します。

| プロパティ | 説明 | 必須 | Default |
| --- | --- | --- | --- |
| frequency |データセット スライス生成の時間単位を指定します。<br/><br/><b>サポートされる frequency</b>: Minute、Hour、Day、Week、Month |はい |NA |
| interval |頻度の乗数を指定します<br/><br/>"frequency x interval" により、スライスが生成される頻度が決まります。<br/><br/>データセットを時間単位でスライスする必要がある場合は、<b>frequency</b> を <b>Hour</b> に設定し、<b>interval</b> を <b>1</b> に設定します。<br/><br/><b>注</b>:frequency に Minute を指定する場合は、interval を 15 以上に設定することをお勧めします |はい |NA |
| style |スライスを間隔の始めまたは終わりに生成するかどうかを指定します。<ul><li>StartOfInterval</li><li>EndOfInterval</li></ul><br/><br/>frequency を Month に設定し、style を EndOfInterval に設定すると、スライスは月の最終日に生成されます。 style が StartOfInterval に設定されていると、スライスは月の最初の日に生成されます。<br/><br/>frequency を Day に設定し、style を EndOfInterval に設定すると、スライスは 1 日の最後の 1 時間に生成されます。<br/><br/>frequency を Hour に設定し、style を EndOfInterval に設定すると、スライスは時間の終わりに生成されます。 たとえば、午後 1 時 ～ 午後 2 時のスライスの場合、午後 2 時にスライスが生成されます。 |いいえ |EndOfInterval |
| anchorDateTime |データセット スライスの境界を計算するためにスケジューラによって使用される時間の絶対位置を定義します。 <br/><br/><b>注</b>:AnchorDateTime に frequency より細かい日付部分が含まれている場合、そのより細かい部分は無視されます。 <br/><br/>たとえば、<b>間隔</b>が<b>時間単位</b> (frequency: Hour、interval:1) で、<b>AnchorDateTime</b> に<b>分と秒</b>が含まれる場合、AnchorDateTime の<b>分と秒</b>部分は無視されます。 |いいえ |01/01/0001 |
| offset |すべてのデータセット スライスの開始と終了がシフトされる時間帯です。 <br/><br/><b>注</b>:anchorDateTime と offset の両方が指定されている場合は、結果としてシフトが結合されます。 |いいえ |NA |

### <a name="offset-example"></a>offset の例
既定では、毎日 (`"frequency": "Day", "interval": 1`) のスライスは UTC 時 12 AM (午前 0 時) に開始します。 開始時刻を 6 AM UTC にするには、次のスニペットに示すようにオフセットを設定します。 

```json
"availability":
{
    "frequency": "Day",
    "interval": 1,
    "offset": "06:00:00"
}
```
### <a name="anchordatetime-example"></a>anchorDateTime の例
次の例では、データセットは 23 時間ごとに 1 回生成されます。 最初のスライスは anchorDateTime に指定された時刻に開始します (`2017-04-19T08:00:00` (UTC 時間) に設定されています)。

```json
"availability":    
{    
    "frequency": "Hour",        
    "interval": 23,    
    "anchorDateTime":"2017-04-19T08:00:00"    
}
```

### <a name="offsetstyle-example"></a>offset/style の例
次のデータセットは月単位のデータセットです。毎月 3 日の 8:00 AM (`3.08:00:00`) に生成されます。

```json
"availability": {
    "frequency": "Month",
    "interval": 1,
    "offset": "3.08:00:00", 
    "style": "StartOfInterval"
}
```

### <a name="dataset-policy"></a>データセット ポリシー
スライス実行を使用する前に、スライス実行で生成されるデータを検証する方法を指定した検証ポリシーをデータセットに定義することができます。 この例では、スライスの実行が完了すると、出力スライスの状態は、サブジェクト状態が **Validation** の **Waiting** に変わります。 スライスの検証が完了すると、スライスの状態は **Ready** に変わります。 データ スライスが生成されても、検証に合格しなかった場合、そのスライスに依存するダウンストリーム スライスのアクティビティ実行は処理されません。 [パイプラインの監視と管理](data-factory-monitor-manage-pipelines.md) に関する記事で説明されています。

データセット定義の **policy** セクションでは、データセット スライスで満たさなければならない基準または条件を定義します。 次の表では、**policy** セクションで使用できるプロパティについて説明します。

| ポリシー名 | 説明 | 適用先 | 必須 | Default |
| --- | --- | --- | --- | --- |
| minimumSizeMB | **Azure BLOB** のデータが最小サイズ要件 (MB 単位) を満たすことを検証します。 |Azure BLOB |いいえ |NA |
| minimumRows | **Azure SQL Database** または **Azure テーブル** のデータに最小行数が含まれていることを検証します。 |<ul><li>Azure SQL データベース</li><li>Azure テーブル</li></ul> |いいえ |NA |

#### <a name="examples"></a>例
**minimumSizeMB:**

```json
"policy":

{
    "validation":
    {
        "minimumSizeMB": 10.0
    }
}
```

**minimumRows**

```json
"policy":
{
    "validation":
    {
        "minimumRows": 100
    }
}
```

これらのプロパティの詳細と例については、[データセットの作成](data-factory-create-datasets.md)に関する記事を参照してください。 

## <a name="activity-policies"></a>アクティビティ ポリシー
ポリシーはアクティビティの実行時の動作に影響します。具体的には、テーブルのスライスがいつ処理されるかです。 次の表で詳細に説明します。

| プロパティ | 使用できる値 | Default value | 説明 |
| --- | --- | --- | --- |
| concurrency |Integer <br/><br/>最大値: 10 |1 |アクティビティの同時実行の数。<br/><br/>異なるスライスで実行できる並列アクティビティ実行の数を決定します。 たとえば、アクティビティが大量のデータを処理する必要がある場合、コンカレンシーの値を大きくするとデータ処理が速くなります。 |
| executionPriorityOrder |NewestFirst<br/><br/>OldestFirst |OldestFirst |処理されるデータ スライスの順序を決定します。<br/><br/>たとえば、2 個のスライス (午後 4 時と午後 5 時の実行) があり、どちらも実行が保留されているとします。 executionPriorityOrder を NewestFirst に設定すると、午後 5 時のスライスが最初に処理されます。 同様に、executionPriorityORder を OldestFIrst に設定すると、午後 4 時のスライスが処理されます。 |
| retry |Integer<br/><br/>最大値は 10 |0 |スライスのデータ処理が失敗としてマークされるまでの再試行回数。 データ スライスのアクティビティの実行は、指定された再試行回数まで再試行されます。 再試行は、障害発生後にできるだけ早く行われます。 |
| timeout |TimeSpan |00:00:00 |アクティビティのタイムアウト。 例:00:10:00 (10 分のタイムアウトを示す)<br/><br/>値が指定されていない場合、または値が 0 の場合は、タイムアウトは無期限です。<br/><br/>スライスのデータ処理時間がタイムアウト値を超えた場合、処理は取り消され、システムは処理の再試行を試みます。 再試行の回数は、retry プロパティで指定します。 タイムアウトが発生すると、ステータスは TimedOut に設定されます。 |
| delay |TimeSpan |00:00:00 |スライスのデータ処理を開始する前の遅延時間を指定します。<br/><br/>データ スライスのアクティビティの実行は、予想実行時刻を Delay だけ過ぎてから開始します。<br/><br/>例:00:10:00 (10 分の遅延を示す) |
| longRetry |Integer<br/><br/>最大値: 10 |1 |スライスの実行が失敗になるまでの、長い再試行の回数。<br/><br/>longRetry の試行は longRetryInterval の間隔で行われます。 再試行間隔の時間を指定する必要がある場合は、longRetry を使用します。 Retry と longRetry の両方を指定すると、各 longRetry に Retry が含まれ、最大再試行回数は Retry * longRetry になります。<br/><br/>たとえば、アクティビティ ポリシーに次のような設定があるとします。<br/>Retry: 3<br/>longRetry: 2<br/>longRetryInterval: 01:00:00<br/><br/>実行するスライスは 1 つだけ (ステータスは Waiting)、アクティビティ実行は毎回失敗するとします。 最初に 3 つの連続する試行があります。 試行するたびに、スライスの状態は Retry になります。 最初の 3 つの試行が終わると、スライスの状態は LongRetry になります。<br/><br/>1 時間 (longRetryInteval の値) が経過した後、再度 3 回連続して試行されます。 その後、スライスの状態は Failed になり、それ以上再試行は行われません。 したがって、全部で 6 回試行されます。<br/><br/>いずれかの実行が成功すると、スライスの状態は Ready になり、それ以上再試行は行われません。<br/><br/>longRetry は、依存するデータがいつ到着するかわからない場合、またはデータ処理が行われる環境全体が当てにならない場合などに使用します。 このような場合、連続して再試行しても意味がなく、時間をおくと成功することがあります。<br/><br/>注意: longRetry または longRetryInterval に大きい値を設定しないでください。 通常、大きな値は、その他のシステムの問題があることを意味します。 |
| longRetryInterval |TimeSpan |00:00:00 |長い再試行の間の遅延 |

詳細については、[パイプライン](data-factory-create-pipelines.md)に関する記事を参照してください。 

## <a name="parallel-processing-of-data-slices"></a>データ スライスの並列処理
パイプラインには過去の開始日を設定できます。 設定した場合、Data Factory によって過去のすべてのデータ スライスが自動的に計算 (バックフィル) され、処理が開始されます。 たとえば、開始日が 2017-04-01 のパイプラインを作成し、現在の日付が 2017-04-10 だとします。 出力データセットのパターンが 1 日ごとの場合、開始日が過去であるため、Data Factory は 2017-04-01 から 2017-04-09 までのすべてのスライスの処理をすぐに開始します。 availability セクションの style プロパティの値が既定では EndOfInterval であるため、2017-04-10 のスライスはまだ処理されません。 executionPriorityOrder の既定値が OldestFirst であるため、最も古いスライスが最初に処理されます。 style プロパティの説明については、「[データセットの可用性](#dataset-availability)」セクションを参照してください。 executionPriorityOrder セクションの説明については、「[アクティビティ ポリシー](#activity-policies)」セクションを参照してください。 

アクティビティ JSON の **policy** セクションに **concurrency** プロパティを設定することで、バックフィルされたデータ スライスが並列で処理されるように構成できます。 このプロパティにより、異なるスライスで実行できる並列アクティビティ実行の数が決定します。 concurrency プロパティの既定値は 1 です。 そのため、既定では、一度に 1 つのスライスが処理されます。 最大値は 10 です。 パイプラインで大量のデータを処理する必要がある場合、コンカレンシーの値を大きくするとデータ処理が速くなります。 

## <a name="rerun-a-failed-data-slice"></a>失敗したデータ スライスを再実行する
データ スライスの処理中にエラーが発生した場合、Azure Portal のブレードまたは監視と管理アプリを使用してスライスの処理が失敗した理由を調べることができます。 詳細については、[Azure Portal の各ブレードを使用したパイプラインの監視と管理](data-factory-monitor-manage-pipelines.md)に関する記事、または[監視と管理用のアプリ](data-factory-monitor-manage-app.md)に関する記事を参照してください。

2 つのアクティビティがある次の例について考えてみます。 Activity1 と Activity2 です。 Activity1 は Dataset1 のスライスを使用し、Dataset2 のスライスを生成します。そのスライスを Activity2 が入力として使用して、最終的なデータセットのスライスを生成します。

![失敗したスライス](./media/data-factory-scheduling-and-execution/failed-slice.png)

この図は、最近の 3 つのスライスのうち、Dataset2 の 9-10 AM スライスの生成時にエラーが発生したことを示しています。 Data Factory では、時系列データセットの依存関係が自動的に追跡されます。 このため、9-10 AM のダウンストリーム スライスのアクティビティ実行は開始されません。

Data Factory の監視および管理ツールを使用すると、失敗したスライスの診断ログを詳細に調べ、問題の根本原因を簡単に見つけて修正できます。 問題を解決したら、すぐにアクティビティ実行を開始して失敗したスライスを生成できます。 再実行の方法とデータ スライスの状態遷移の詳細については、[Azure Portal の各ブレードを使用したパイプラインの監視と管理](data-factory-monitor-manage-pipelines.md)に関する記事、または[監視と管理用のアプリ](data-factory-monitor-manage-app.md)に関する記事を参照してください。

**Dataset2** の 9-10 AM スライスを再実行すると、Data Factory では最終データセットに対して 9-10 AM 依存スライスの実行が開始されます。

![失敗したスライスの再実行](./media/data-factory-scheduling-and-execution/rerun-failed-slice.png)

## <a name="multiple-activities-in-a-pipeline"></a>パイプライン内の複数アクティビティ
パイプラインに複数のアクティビティを含めることができます。 パイプラインに複数のアクティビティがあり、アクティビティの出力が別のアクティビティの入力ではない場合は、アクティビティの入力データ スライスの準備ができれば、アクティビティが並列実行される可能性があります。

2 つのアクティビティを連鎖させる (アクティビティを連続的に実行する) には、一方のアクティビティの出力データセットを、もう一方のアクティビティの入力データセットとして指定します。 このとき、同じパイプラインのアクティビティと、別のパイプラインのアクティビティを指定できます。 2 つ目のアクティビティは、1 つ目のアクティビティが正常に完了した後にのみ実行されます。

たとえば、パイプラインに 2 つのアクティビティがある場合を考えてみましょう。

1. アクティビティ A1 は、外部入力データセット D1 を必要とし、出力データセット D2 を生成します。
2. アクティビティ A2 は、データセット D2 からの入力を必要とし、出力データセット D3 を生成します。

このシナリオでは、アクティビティ A1 と A2 は同じパイプラインにあります。 外部データが使用可能なときにアクティビティ A1 が実行され、スケジュールされた可用性の頻度に達します。 D2 のスケジュールされたスライスが使用可能になると、アクティビティ A2 が実行され、スケジュールされた可用性の頻度に達します。 データセット D2 のスライスのいずれかでエラーが発生した場合、スライスが使用可能になるまで、そのスライスに対して A2 は実行されません。

同じパイプラインに両方のアクティビティがあるダイアグラム ビューは、次の図のようになります。

![同じパイプラインでのアクティビティの連鎖](./media/data-factory-scheduling-and-execution/chaining-one-pipeline.png)

既に説明したように、アクティビティは異なるパイプラインに指定できます。 そうしたシナリオでは、ダイアグラム ビューは次の図のようになります。

![2 つのパイプラインでのアクティビティの連鎖](./media/data-factory-scheduling-and-execution/chaining-two-pipelines.png)

例については、付録の順次コピーに関するセクションを参照してください。

## <a name="model-datasets-with-different-frequencies"></a>頻度が異なるデータセットのモデル化
各サンプルでは、入力および出力データセットとアクティビティ スケジュール ウィンドウの頻度は同じでした。 シナリオによっては、1 つまたは複数の入力の頻度とは異なる頻度で出力を生成できる必要があります。 Data Factory は、このようなシナリオのモデル化をサポートしています。

### <a name="sample-1-produce-a-daily-output-report-for-input-data-that-is-available-every-hour"></a>サンプル 1:毎時取得できる入力データの出力レポートを 1 日に 1 回生成する
センサーからの入力測定データを Azure BLOB Strage で 1 時間ごとに使用できるシナリオについて考えてみます。 Data Factory の [Hive アクティビティ](data-factory-hive-activity.md)を使用して、1 日の平均値、最大値、最小値など、統計情報を含む日次集計レポートを生成します。

Data Factory を使用して、このシナリオをモデル化する方法を次に示します。

**入力データセット**

特定の日のフォルダーに毎時の入力ファイルが生成されます。 入力の availability は **Hour** に設定されています (frequency:Hour、interval:1)。

```json
{
  "name": "AzureBlobInput",
  "properties": {
    "type": "AzureBlob",
    "linkedServiceName": "StorageLinkedService",
    "typeProperties": {
      "folderPath": "mycontainer/myfolder/{Year}/{Month}/{Day}/",
      "partitionedBy": [
        { "name": "Year", "value": {"type": "DateTime","date": "SliceStart","format": "yyyy"}},
        { "name": "Month","value": {"type": "DateTime","date": "SliceStart","format": "MM"}},
        { "name": "Day","value": {"type": "DateTime","date": "SliceStart","format": "dd"}}
      ],
      "format": {
        "type": "TextFormat"
      }
    },
    "external": true,
    "availability": {
      "frequency": "Hour",
      "interval": 1
    }
  }
}
```
**出力データセット**

毎日、その日のフォルダーに出力ファイルが 1 つ作成されます。 出力の availability は **Day** に設定されています (frequency:Day、interval:1)。

```json
{
  "name": "AzureBlobOutput",
  "properties": {
    "type": "AzureBlob",
    "linkedServiceName": "StorageLinkedService",
    "typeProperties": {
      "folderPath": "mycontainer/myfolder/{Year}/{Month}/{Day}/",
      "partitionedBy": [
        { "name": "Year", "value": {"type": "DateTime","date": "SliceStart","format": "yyyy"}},
        { "name": "Month","value": {"type": "DateTime","date": "SliceStart","format": "MM"}},
        { "name": "Day","value": {"type": "DateTime","date": "SliceStart","format": "dd"}}
      ],
      "format": {
        "type": "TextFormat"
      }
    },
    "availability": {
      "frequency": "Day",
      "interval": 1
    }
  }
}
```

**アクティビティ: パイプラインの Hive アクティビティ**

次のスニペットに示すように、Hive スクリプトは *WindowStart* 変数を使用したパラメーターとして適切な **DateTime** 情報を受け取ります。 Hive スクリプトは、この変数を使用して、その日の適切なフォルダーからデータを読み込み、集計を実行して出力を生成します。

```json
{  
    "name":"SamplePipeline",
    "properties":{  
    "start":"2015-01-01T08:00:00",
    "end":"2015-01-01T11:00:00",
    "description":"hive activity",
    "activities": [
        {
            "name": "SampleHiveActivity",
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
            "linkedServiceName": "HDInsightLinkedService",
            "type": "HDInsightHive",
            "typeProperties": {
                "scriptPath": "adftutorial\\hivequery.hql",
                "scriptLinkedService": "StorageLinkedService",
                "defines": {
                    "Year": "$$Text.Format('{0:yyyy}',WindowStart)",
                    "Month": "$$Text.Format('{0:MM}',WindowStart)",
                    "Day": "$$Text.Format('{0:dd}',WindowStart)"
                }
            },
            "scheduler": {
                "frequency": "Day",
                "interval": 1
            },            
            "policy": {
                "concurrency": 1,
                "executionPriorityOrder": "OldestFirst",
                "retry": 2,
                "timeout": "01:00:00"
            }
         }
     ]
   }
}
```

次の図は、データ依存関係の観点からこのシナリオを示しています。

![データ依存関係](./media/data-factory-scheduling-and-execution/data-dependency.png)

毎日の出力スライスは、入力データセットの 24 時間のスライスに依存しています。 Data Factory では、生成する出力スライスと同じ期間に属する入力データ スライスを特定して、これらの依存関係を自動的に計算します。 24 個の入力スライスのいずれかを使用できない場合、Data Factory では入力スライスの準備が完了するまで待機してから、毎日のアクティビティ実行を開始します。

### <a name="sample-2-specify-dependency-with-expressions-and-data-factory-functions"></a>サンプル 2:式と Data Factory の関数を使用して依存関係を指定する
別のシナリオについて考えてみましょう。 2 つの入力データセットを処理する Hive アクティビティがあるとします。 一方のデータセットには毎日新しいデータが入力されますが、もう片方のデータセットでは 1 週間ごとに新しいデータを取得します。 これら 2 つの入力を結合して、毎日 1 つの出力を生成してみましょう。

出力データ スライスの期間に合わせることで処理対象の入力スライスを Data Factory で自動的に特定するという単純な方法では、こうした操作を行うことはできません。

個々のアクティビティ実行で、毎週の入力データセットに対して最新の週のデータ セットが Data Factory に使用されるように指定する必要があります。 この動作を実装するには、次のスニペットに示すように Azure Data Factory の関数を使用します。

**Input1:Azure blob**

最初の入力は、毎日更新される Azure BLOB にします。

```json
{
  "name": "AzureBlobInputDaily",
  "properties": {
    "type": "AzureBlob",
    "linkedServiceName": "StorageLinkedService",
    "typeProperties": {
      "folderPath": "mycontainer/myfolder/{Year}/{Month}/{Day}/",
      "partitionedBy": [
        { "name": "Year", "value": {"type": "DateTime","date": "SliceStart","format": "yyyy"}},
        { "name": "Month","value": {"type": "DateTime","date": "SliceStart","format": "MM"}},
        { "name": "Day","value": {"type": "DateTime","date": "SliceStart","format": "dd"}}
      ],
      "format": {
        "type": "TextFormat"
      }
    },
    "external": true,
    "availability": {
      "frequency": "Day",
      "interval": 1
    }
  }
}
```

**Input2:Azure blob**

Input2 は、毎週更新される Azure BLOB にします。

```json
{
  "name": "AzureBlobInputWeekly",
  "properties": {
    "type": "AzureBlob",
    "linkedServiceName": "StorageLinkedService",
    "typeProperties": {
      "folderPath": "mycontainer/myfolder/{Year}/{Month}/{Day}/",
      "partitionedBy": [
        { "name": "Year", "value": {"type": "DateTime","date": "SliceStart","format": "yyyy"}},
        { "name": "Month","value": {"type": "DateTime","date": "SliceStart","format": "MM"}},
        { "name": "Day","value": {"type": "DateTime","date": "SliceStart","format": "dd"}}
      ],
      "format": {
        "type": "TextFormat"
      }
    },
    "external": true,
    "availability": {
      "frequency": "Day",
      "interval": 7
    }
  }
}
```

**Output:Azure blob**

毎日、その日のフォルダーに出力ファイルが 1 つ作成されます。 出力の availability は **day** に設定されています (frequency:Day, interval:1)。

```json
{
  "name": "AzureBlobOutputDaily",
  "properties": {
    "type": "AzureBlob",
    "linkedServiceName": "StorageLinkedService",
    "typeProperties": {
      "folderPath": "mycontainer/myfolder/{Year}/{Month}/{Day}/",
      "partitionedBy": [
        { "name": "Year", "value": {"type": "DateTime","date": "SliceStart","format": "yyyy"}},
        { "name": "Month","value": {"type": "DateTime","date": "SliceStart","format": "MM"}},
        { "name": "Day","value": {"type": "DateTime","date": "SliceStart","format": "dd"}}
      ],
      "format": {
        "type": "TextFormat"
      }
    },
    "availability": {
      "frequency": "Day",
      "interval": 1
    }
  }
}
```

**アクティビティ: パイプラインの Hive アクティビティ**

Hive アクティビティは 2 つの入力を受け取り、出力スライスを毎日生成します。 次のように、毎週の入力に対する前の週の入力スライスに応じて、毎日の出力スライスを指定できます。

```json
{  
    "name":"SamplePipeline",
    "properties":{  
    "start":"2015-01-01T08:00:00",
    "end":"2015-01-01T11:00:00",
    "description":"hive activity",
    "activities": [
      {
        "name": "SampleHiveActivity",
        "inputs": [
          {
            "name": "AzureBlobInputDaily"
          },
          {
            "name": "AzureBlobInputWeekly",
            "startTime": "Date.AddDays(SliceStart, - Date.DayOfWeek(SliceStart))",
            "endTime": "Date.AddDays(SliceEnd,  -Date.DayOfWeek(SliceEnd))"  
          }
        ],
        "outputs": [
          {
            "name": "AzureBlobOutputDaily"
          }
        ],
        "linkedServiceName": "HDInsightLinkedService",
        "type": "HDInsightHive",
        "typeProperties": {
          "scriptPath": "adftutorial\\hivequery.hql",
          "scriptLinkedService": "StorageLinkedService",
          "defines": {
            "Year": "$$Text.Format('{0:yyyy}',WindowStart)",
            "Month": "$$Text.Format('{0:MM}',WindowStart)",
            "Day": "$$Text.Format('{0:dd}',WindowStart)"
          }
        },
        "scheduler": {
          "frequency": "Day",
          "interval": 1
        },            
        "policy": {
          "concurrency": 1,
          "executionPriorityOrder": "OldestFirst",
          "retry": 2,  
          "timeout": "01:00:00"
        }
       }
     ]
   }
}
```

Data Factory でサポートされている関数とシステム変数の一覧については、 [Data Factory の関数とシステム変数](data-factory-functions-variables.md) に関する記事をご覧ください。

## <a name="appendix"></a>付録

### <a name="example-copy-sequentially"></a>例: 順次コピー
複数のコピー操作を、順番にまたは順序を指定して 1 つずつ実行できます。 たとえば、パイプラインに 2 つのコピー アクティビティ (CopyActivity1 と CopyActivity2) があり、入力データと出力データセットは次のとおりであるとします。   

CopyActivity1

次の内容を入力します。Dataset。 出力:Dataset2。

CopyActivity2

次の内容を入力します。Dataset2。  出力:Dataset3。

CopyActivity2 は、CopyActivity1 が正常に実行していて、Dataset2 を使用できた場合にのみ実行します。

パイプライン JSON の例を次に示します。

```json
{
    "name": "ChainActivities",
    "properties": {
        "description": "Run activities in sequence",
        "activities": [
            {
                "type": "Copy",
                "typeProperties": {
                    "source": {
                        "type": "BlobSource"
                    },
                    "sink": {
                        "type": "BlobSink",
                        "copyBehavior": "PreserveHierarchy",
                        "writeBatchSize": 0,
                        "writeBatchTimeout": "00:00:00"
                    }
                },
                "inputs": [
                    {
                        "name": "Dataset1"
                    }
                ],
                "outputs": [
                    {
                        "name": "Dataset2"
                    }
                ],
                "policy": {
                    "timeout": "01:00:00"
                },
                "scheduler": {
                    "frequency": "Hour",
                    "interval": 1
                },
                "name": "CopyFromBlob1ToBlob2",
                "description": "Copy data from a blob to another"
            },
            {
                "type": "Copy",
                "typeProperties": {
                    "source": {
                        "type": "BlobSource"
                    },
                    "sink": {
                        "type": "BlobSink",
                        "writeBatchSize": 0,
                        "writeBatchTimeout": "00:00:00"
                    }
                },
                "inputs": [
                    {
                        "name": "Dataset2"
                    }
                ],
                "outputs": [
                    {
                        "name": "Dataset3"
                    }
                ],
                "policy": {
                    "timeout": "01:00:00"
                },
                "scheduler": {
                    "frequency": "Hour",
                    "interval": 1
                },
                "name": "CopyFromBlob2ToBlob3",
                "description": "Copy data from a blob to another"
            }
        ],
        "start": "2016-08-25T01:00:00Z",
        "end": "2016-08-25T01:00:00Z",
        "isPaused": false
    }
}
```

この例では、最初のコピー アクティビティの出力データセット (Dataset2) が 2 番目のアクティビティの入力として指定されています。 したがって、2 番目のアクティビティは、最初のアクティビティからの出力データセットが準備ができたときにのみ実行されます。  

この例では、CopyActivity2 で別の入力 (Dataset3 など) を使用できますが、CopyActivity2 への入力として Dataset2 も指定しているため、CopyActivity1 が完了するまでこのアクティビティは実行されません。 次に例を示します。

CopyActivity1

次の内容を入力します。Dataset1。 出力:Dataset2。

CopyActivity2

入力:Dataset3、Dataset2。 出力:Dataset4。

```json
{
    "name": "ChainActivities",
    "properties": {
        "description": "Run activities in sequence",
        "activities": [
            {
                "type": "Copy",
                "typeProperties": {
                    "source": {
                        "type": "BlobSource"
                    },
                    "sink": {
                        "type": "BlobSink",
                        "copyBehavior": "PreserveHierarchy",
                        "writeBatchSize": 0,
                        "writeBatchTimeout": "00:00:00"
                    }
                },
                "inputs": [
                    {
                        "name": "Dataset1"
                    }
                ],
                "outputs": [
                    {
                        "name": "Dataset2"
                    }
                ],
                "policy": {
                    "timeout": "01:00:00"
                },
                "scheduler": {
                    "frequency": "Hour",
                    "interval": 1
                },
                "name": "CopyFromBlobToBlob",
                "description": "Copy data from a blob to another"
            },
            {
                "type": "Copy",
                "typeProperties": {
                    "source": {
                        "type": "BlobSource"
                    },
                    "sink": {
                        "type": "BlobSink",
                        "writeBatchSize": 0,
                        "writeBatchTimeout": "00:00:00"
                    }
                },
                "inputs": [
                    {
                        "name": "Dataset3"
                    },
                    {
                        "name": "Dataset2"
                    }
                ],
                "outputs": [
                    {
                        "name": "Dataset4"
                    }
                ],
                "policy": {
                    "timeout": "01:00:00"
                },
                "scheduler": {
                    "frequency": "Hour",
                    "interval": 1
                },
                "name": "CopyFromBlob3ToBlob4",
                "description": "Copy data from a blob to another"
            }
        ],
        "start": "2017-04-25T01:00:00Z",
        "end": "2017-04-25T01:00:00Z",
        "isPaused": false
    }
}
```

この例では、2 つの入力データセットが、2 番目のコピー アクティビティに対して指定されています。 複数の入力を指定すると、データのコピーに使用されるのは最初の入力データセットのみで、他のデータセットは依存関係として使用されます。 CopyActivity2 は、次の条件が満たされた場合にのみ開始されます。

* CopyActivity1 が正常に完了していて、Dataset2 を使用できる。 データを Dataset4 にコピーするときに、このデータセットは使用されません。 これは、CopyActivity2 のスケジュールの依存関係としてのみ機能します。   
* Dataset3 を使用できる。 このデータセットは、コピー先にコピーされるデータを表します。 
