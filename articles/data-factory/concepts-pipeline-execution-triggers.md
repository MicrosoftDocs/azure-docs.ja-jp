---
title: "Azure Data Factory でのパイプラインの実行とトリガー| Microsoft Docs"
description: "この記事では、オンデマンドで、またはトリガーを作成して、Azure Data Factory でパイプラインを実行する方法に関する情報を提供します。"
services: data-factory
documentationcenter: 
author: sharonlo101
manager: jhubbard
editor: spelluru
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 08/10/2017
ms.author: shlo
ms.openlocfilehash: c319979cce23da69965d4fbab037919461f67b3a
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="pipeline-execution-and-triggers-in-azure-data-factory"></a>Azure Data Factory でのパイプラインの実行とトリガー 
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [バージョン 1 - 一般公開](v1/data-factory-scheduling-and-execution.md)
> * [バージョン 2 - プレビュー](concepts-pipeline-execution-triggers.md)

**パイプライン実行**は、パイプラインの実行のインスタンスを定義する Azure Data Factory バージョン 2 での用語です。 たとえば、午前 8 時、午前 9 時、午前 10 時に実行するパイプラインがあるとします。 ここでは、パイプラインの 3 つの独立した実行 (パイプライン実行) があることになります。 各パイプライン実行には、一意のパイプライン実行 ID があります。それぞれが特定のパイプライン実行を一意に定義する GUID です。 パイプライン実行は、通常、パイプラインで定義されたパラメーターに引数を渡してインスタンス化されます。 パイプラインを実行する 2 つの方法があります。**手動で**行う方法と**トリガー**を介して行う方法です。 この記事では、パイプラインを実行する両方の方法の詳細について説明します。 

> [!NOTE]
> この記事は、現在プレビュー段階にある Data Factory のバージョン 2 に適用されます。 一般公開 (GA) されている Data Factory サービスのバージョン 1 を使用している場合は、[Data Factory V1 でのスケジュールと実行](v1/data-factory-scheduling-and-execution.md)に関する記事をご覧ください。

## <a name="run-pipeline-on-demand"></a>オンデマンドでのパイプラインの実行
この方式では、パイプラインを手動で実行します。 これはパイプラインのオンデマンド実行とも見なされます。 

たとえば、**copyPipeline** という名前のパイプラインを実行するとします。 このパイプラインは、Azure Blob Storage 内のソース フォルダーから、同じストレージ内のコピー先フォルダーにコピーするアクティビティを伴う 1 つの単純なパイプラインです。 サンプル パイプラインの定義を次に示します。 

```json
{
  "name": "copyPipeline",
  "properties": {
    "activities": [
      {
        "type": "Copy",
        "typeProperties": {
          "source": {
            "type": "BlobSource"
          },
          "sink": {
            "type": "BlobSink"
          }
        },
        "name": "CopyBlobtoBlob",
        "inputs": [
          {
            "referenceName": "sourceBlobDataset",
            "type": "DatasetReference"
          }
        ],
        "outputs": [
          {
            "referenceName": "sinkBlobDataset",
            "type": "DatasetReference"
          }
        ]
      }
    ],
    "parameters": {
      "sourceBlobContainer": {
        "type": "String"
      },
      "sinkBlobContainer": {
        "type": "String"
      }
    }
  }
}

```
このパイプラインは、JSON 定義に示されている 2 つのパラメーター sourceBlobContainer と sinkBlobContainer を取ります。 実行時に、これらのパラメーターに値を渡します。 

パイプラインを手動で実行するには、次の方法のいずれかを使用できます。.NET、PowerShell、REST、Python です。 

### <a name="rest-api"></a>REST API
サンプルの REST コマンドを次に示します。  

```
POST
https://management.azure.com/subscriptions/mySubId/resourceGroups/myResourceGroup/providers/Microsoft.DataFactory/factories/myDataFactory/pipelines/copyPipeline/createRun?api-version=2017-03-01-preview
```
完全なサンプルについては、「[Quickstart: create a data factory using REST API (クイック スタート: REST API を使用するデータ ファクトリの作成)](quickstart-create-data-factory-rest-api.md)」を参照してください。

### <a name="powershell"></a>PowerShell
サンプルの PowerShell コマンドを次に示します。 

```powershell
Invoke-AzureRmDataFactoryV2Pipeline -DataFactory $df -PipelineName "Adfv2QuickStartPipeline" -ParameterFile .\PipelineParameters.json
```

パラメーターは要求ペイロードの本文に入れて渡します。 .NET、PowerShell、Python では、呼び出しに引数として渡されるディクショナリに値を入れて渡します。

```json
{
  “sourceBlobContainer”: “MySourceFolder”,
  “sinkBlobCountainer”: “MySinkFolder”
}
```

応答ペイロードは、パイプライン実行の一意の ID です。

```json
{
  "runId": "0448d45a-a0bd-23f3-90a5-bfeea9264aed"
}
```


完全なサンプルについては、「[Quickstart: create a data factory using PowerShell (クイック スタート: PowerShell を使用するデータ ファクトリの作成)](quickstart-create-data-factory-powershell.md)」を参照してください。

### <a name="net"></a>.NET 
サンプルの .NET 呼び出しを次に示します。 

```csharp
client.Pipelines.CreateRunWithHttpMessagesAsync(resourceGroup, dataFactoryName, pipelineName, parameters)
```

完全なサンプルについては、「[Quickstart: create a data factory using .NET (クイック スタート: .NET を使用するデータ ファクトリの作成)](quickstart-create-data-factory-dot-net.md)」を参照してください。

> [!NOTE]
> .NET API を使用すると、Azure Functions や、独自の Web サービスなどから、Data Factory パイプラインを呼び出すことができます。

## <a name="triggers"></a>トリガー
トリガーは、パイプライン実行を行う 2 番目の方法を提供します。 トリガーは、パイプラインの実行をいつ開始する必要があるかを決定する処理単位を表します。 現在、Data Factory は、実時間のスケジュールによってパイプラインを起動するトリガーをサポートしています。 それは**スケジューラ トリガー**と呼ばれます。 現在、Data Factory は、ファイル到着のイベント発生時のパイプライン実行のトリガーなど、イベント ベースのトリガーをサポートしていません。

パイプラインとトリガーには "n-m" の関係があります。 複数のトリガーで 1 つのパイプラインを開始したり、1 つのトリガーで複数のパイプラインを開始したりできます。 次のトリガーの JSON 定義では、**pipelines** プロパティは、特定のトリガーによってトリガーされるパイプラインのリストと、パイプライン パラメーターの値を参照します。

### <a name="basic-trigger-definition"></a>基本的なトリガー定義: 
```json
    "properties": {
        "name": "MyTrigger",
        "type": "<type of trigger>",
        "typeProperties": {
            …
        },
        "pipelines": [
            {
                "pipelineReference": {
                    "type": "PipelineReference",
                    "referenceName": "<Name of your pipeline>"
                },
                "parameters": {
                    "<parameter 1 Name>": {
                        "type": "Expression",
                          "value": "<parameter 1 Value>"
                    },
                    "<parameter 2 Name>" : "<parameter 2 Value>"
                }
            }
        ]
    }
```

## <a name="scheduler-trigger"></a>スケジューラ トリガー
スケジューラ トリガーは、実時間のスケジュールでパイプラインを実行します。 このトリガーは、定期的、および高度な予定表のオプションをサポートしています (毎週、月曜日の午後 5 時と火曜日の午後 9 時)。 時系列データと非時系列データとを区別せず、データセット パターンに依存しないため、柔軟性があります。

### <a name="scheduler-trigger-json-definition"></a>スケジューラ トリガー JSON 定義
スケジューラ トリガーを作成するときには、このセクションの例に示されているように、JSON を使用してスケジュールと繰り返しを指定できます。 

スケジューラ トリガーにパイプライン実行を開始させるには、特定のパイプラインのパイプライン参照をトリガー定義に組み込みます。 パイプラインとトリガーには "n-m" の関係があります。 複数のトリガーが 1 つのパイプラインを開始することができます。 同じトリガーが複数のパイプラインを開始することもできます。

```json
{
  "properties": {
    "type": "ScheduleTrigger",
    "typeProperties": {
      "recurrence": {
        "frequency": <<Minute, Hour, Day, Week, Year>>,
        "interval": <<int>>,             // optional, how often to fire (default to 1)
        "startTime": <<datetime>>,
        "endTime": <<datetime>>,
        "timeZone": <<default UTC>>
        "schedule": {                    // optional (advanced scheduling specifics)
          "hours": [<<0-24>>],
          "weekDays": ": [<<Monday-Sunday>>],
          "minutes": [<<0-60>>],
          "monthDays": [<<1-31>>],
          "monthlyOccurences": [
               {
                    "day": <<Monday-Sunday>>,
                    "occurrence": <<1-5>>
               }
           ] 
      }
    },
   "pipelines": [
            {
                "pipelineReference": {
                    "type": "PipelineReference",
                    "referenceName": "<Name of your pipeline>"
                },
                "parameters": {
                    "<parameter 1 Name>": {
                        "type": "Expression",
                        "value": "<parameter 1 Value>"
                    },
                    "<parameter 2 Name> : "<parameter 2 Value>"
                }
           }
      ]
  }
}
```

### <a name="overview-scheduler-trigger-schema"></a>概要: スケジューラ トリガーのスキーマ
次の表に、トリガーでの繰り返しとスケジュール設定に関連する主要な要素の概要を示します。

JSON プロパティ |     Description
------------- | -------------
startTime | startTime は、日付/時刻です。 単純なスケジュールでは、startTime は最初の発生日時です。 複雑なスケジュールでは、トリガーは startTime になるとすぐに起動します。
recurrence | recurrence オブジェクトは、トリガーの繰り返し規則を指定します。 この recurrence オブジェクトは、frequency、interval、endTime、count、schedule という要素をサポートします。 recurrence を定義する場合は、frequency が必要です。recurrence の他の要素は省略可能です。
frequency | トリガーが繰り返す頻度の単位を表します。 サポートされる値は `minute`、`hour`、`day`、`week`、`month` です。
interval | interval は正の整数です。 トリガーを実行する頻度を決定する frequency の間隔を示します。 たとえば、interval が 3 で frequency が "week" の場合は、トリガーは 3 週間ごとに繰り返されます。
endTime | トリガーの終了日付/時刻を指定します。 トリガーはこの時刻より後には実行されません。 過去の日時を endTime に指定するのは無効です。
schedule | トリガーに頻度を指定すると、繰り返しのスケジュールに基づいて、そのジョブの繰り返しを変更できます。 schedule には、分、時間、曜日、月の日、週番号に基づいた変更を指定します。

### <a name="overview-scheduler-trigger-schema-defaults-limits-and-examples"></a>概要: スケジューラ トリガーのスキーマの既定値、制限、例

JSON での名前 | 値の型 | 必須 | 既定値 | 有効な値 | 例
--------- | ---------- | --------- | ------------- | ------------ | -------
startTime | String | はい | なし | ISO-8601 の日付/時刻 | ```"startTime" : "2013-01-09T09:30:00-08:00"```
recurrence | オブジェクト | あり | なし | recurrence オブジェクト | ```"recurrence" : { "frequency" : "monthly", "interval" : 1 }```
interval | Number | なし | 1 | 1 ～ 1000。 | ```"interval":10```
endTime | String | はい | なし | 将来の時刻を表す日付/時刻の値 | `"endTime" : "2013-02-09T09:30:00-08:00"`
schedule | オブジェクト | なし | なし | schedule オブジェクト | `"schedule" : { "minute" : [30], "hour" : [8,17] }`

### <a name="deep-dive-starttime"></a>詳細情報: startTime
次の表では、startTime による、トリガーの実行の制御方法を説明しています。

startTime の値 | スケジュールなしの繰り返し | スケジュールありの繰り返し
--------------- | --------------------------- | ------------------------
開始時刻が過去に設定されている | 開始時刻より後の将来の最初の実行時刻を計算し、その時点で実行します。<p>2 回目以降のジョブは、最後の実行時刻から計算して実行します。</p><p>この表の後の例を参照してください。</p> | トリガーは、指定した開始時刻_になるとすぐに_開始します。 最初は、開始時刻から計算したスケジュールに基づいて実行します。 <p>2 回目以降のジョブは、繰り返しのスケジュールに基づいて実行</p>
開始時刻が将来または現在に設定されている | 指定した開始時刻に 1 回実行します。 <p>2 回目以降のジョブは、最後の実行時刻から計算して実行します。</p> | トリガーは、指定した開始時刻_になるとすぐに_開始します。 最初は、開始時刻から計算したスケジュールに基づいて実行します。<p>2 回目以降のジョブは、繰り返しのスケジュールに基づいて実行します。</p>

ここでは、startTime が過去であり、recurrence を設定し、schedule を設定していない場合についての動作の例を説明します。 現在の時刻が `2017-04-08 13:00`、startTime が `2017-04-07 14:00` で、recurrence は 2 日ごと (frequency に day、interval に 2 を指定) であると仮定します。startTime が過去、つまり現在の時刻よりも前であることにご注意ください。

この条件では、最初の実行は `2017-04-09 at 14:00` になります。 Scheduler エンジンは、開始時刻から実行を計算します。 過去のインスタンスはすべて破棄されます。 エンジンは、将来発生する次回のインスタンスを使用します。 したがって、この場合は、startTime が `2017-04-07 at 2:00pm` となり、次回のインスタンスは 2 日後、つまり `2017-04-09 at 2:00pm` となります。

startTime が `2017-04-05 14:00` または `2017-04-01 14:00` であっても、最初の実行時間は同じです。 最初の実行後、後続の実行はスケジュールを使用して計算されます。 したがって、`2017-04-11 at 2:00pm`、`2017-04-13 at 2:00pm`、そして `2017-04-15 at 2:00pm` などとなります。

最後に、トリガーにスケジュールが設定されている場合に、スケジュールに時/分 (一方または両方) が設定されていないと、既定では、最初の実行に指定した時/分が、それぞれ使用されます。

### <a name="deep-dive-schedule"></a>詳細情報: schedule
一方で、schedule は、トリガーの実行回数を制限できます。 たとえば、frequency が "month" のトリガーに、31 日にのみ実行する schedule を設定すると、トリガーは 31 日がある月にのみ実行されます。

他方、schedule によりトリガーの実行回数を増やすこともできます。 たとえば、frequency が "month" のトリガーに、月の 1 日と 2 日に実行する schedule を設定すると、トリガーは、月に 1 回ではなく、毎月 1 日と 2 日に実行されます。

schedule の要素を複数指定した場合は、評価の順序は大きい要素から小さい要素の順序となります。つまり、週番号、月の日、曜日、時間、分という順序です。

schedule の要素を次の表に詳しく示します。


JSON での名前 | Description | 有効な値
--------- | ----------- | ------------
minutes | トリガーを実行する時刻 (分)。 | <ul><li>整数</li><li>整数の配列</li></ul>
hours | トリガーを実行する時刻 (時)。 | <ul><li>整数</li><li>整数の配列</li></ul>
weekDays | トリガーを実行する曜日。 週単位の頻度だけを指定できます。 | <ul><li>Monday、Tuesday、Wednesday、Thursday、Friday、Saturday、Sunday</li><li>上記の任意の値の配列 (最大配列サイズは 7)</li></p>大文字/小文字は区別されません</p>
monthlyOccurrences | トリガーを実行する月の日にちを指定します。 月単位の頻度だけを指定できます。 | monthlyOccurence オブジェクトの配列: `{ "day": day,  "occurrence": occurence }`。 <p> day はトリガーを実行する曜日です。たとえば、`{Sunday}` は、月の毎週日曜日という意味です。 必須。<p>occurrence は、月の第何週に実行するかを表します。たとえば、`{Sunday, -1}` は月の最終日曜日という意味です。 省略可能。
monthDays | トリガーが実行される月の日にち。 月単位の頻度だけを指定できます。 | <ul><li>-1 以下かつ -31 以上の任意の値</li><li>1 以上かつ 31 以下の任意の値</li><li>上記の値の配列</li>


## <a name="examples-recurrence-schedules"></a>例: 繰り返しのスケジュール
このセクションでは、schedule オブジェクトとそのサブ要素に注目して、さまざまな繰り返しの例を示します。

スケジュール例では、間隔が 1 に設定されていると仮定します。 また、schedule の値に合った適切な頻度が指定されていると仮定します。たとえば、frequency に "day" を使用し、schedule に "monthDays" の変更を指定することはできません。 これらの制限は、前のセクションの表で説明されています。 

例 | Description
------- | -----------
`{"hours":[5]}` | 毎日午前 5 時に実行
`{"minutes":[15], "hours":[5]}` | 毎日午前 5 時 15 分に実行
`{"minutes":[15], "hours":[5,17]}` | 毎日午前 5 時 15 分と午後 5 時 15 分に実行
`{"minutes":[15,45], "hours":[5,17]}` | 毎日午前 5 時 15 分、午前 5 時 45 分、午後 5 時 15 分、午後 5 時 45 分に実行
`{"minutes":[0,15,30,45]}` | 15 分ごとに実行
`{hours":[0, 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 17, 18, 19, 20, 21, 22, 23]}` | 1 時間ごとに実行。 このトリガーは 1 時間おきに実行されます。 分は、指定されている場合は startTime によって制御されます。指定されていない場合は、作成時刻によって制御されます。 たとえば、開始時刻または作成時刻が午後 12 時 25 分である場合 (どちらが適用される場合でも)、トリガーは 0 時 25 分、1 時 25 分、2 時 25 分...23 時 25 分に実行されます。 このスケジュールは、frequency に "hour"、interval に 1 を指定して、schedule を指定しないトリガーと同等です。 両者の違いは、このスケジュールは、frequency と interval を変えることで、他のトリガーの作成に使用できる点にあります。 たとえば、frequency が "month" の場合には、月 1 回だけ実行されます。frequency が "day" の場合には、スケジュールは毎日実行されます。
`{"minutes":[0]}` | 毎正時に実行。 このトリガーも 1 時間ごとに実行されますが、正時に実行されます。(例: 午前 0 時、午前 1 時、午前 2 時)。 この設定は、frequency に "hour"、startTime に 0 分を設定し、さらに頻度が "day" であれば、schedule を設定しなかったトリガーと同等です。ただし、頻度が "week" または "month" の場合は、このスケジュールは、それぞれ週に 1 日だけ、または月に 1 日だけ実行します。
`{"minutes":[15]}` | 毎正時から 15 分経過後に実行。 1 時間ごとに実行。午前 0 時 15 分から開始し、午前 1 時 15 分、午前 2 時 15 分...午後 10 時 15 分と続き、最後に午後 11 時 15 分に実行します。
`{"hours":[17], "weekDays":["saturday"]}` | 毎週土曜日の午後 5 時に実行
`{"hours":[17], "weekDays":["monday", "wednesday", "friday"]}` | 毎週月曜日、水曜日、金曜日の午後 5 時に実行
`{"minutes":[15,45], "hours":[17], "weekDays":["monday", "wednesday", "friday"]}` | 毎週月曜日、水曜日、金曜日の午後 5 時 15 分と午後 5 時 45 分に実行
`{"minutes":[0,15,30,45], "weekDays":["monday", "tuesday", "wednesday", "thursday", "friday"]}` | 平日に 15 分ごとに実行
`{"minutes":[0,15,30,45], "hours": [9, 10, 11, 12, 13, 14, 15, 16] "weekDays":["monday", "tuesday", "wednesday", "thursday", "friday"]}` | 平日の午前 9 時と午後 4 時 45 分の間、15 分ごとに実行
`{"weekDays":["tuesday", "thursday"]}` | 火曜日と木曜日の指定された開始時刻に実行。
`{"minutes":[0], "hours":[6], "monthDays":[28]}` | 毎月 28 日の午前 6 時に実行 (頻度を月と仮定した場合)
`{"minutes":[0], "hours":[6], "monthDays":[-1]}` | 月の最終日の午前 6 時に実行。 月の最終日にトリガーを実行する場合は、28、29、30、31 という日にちではなく -1 を使用します。
`{"minutes":[0], "hours":[6], "monthDays":[1,-1]}` | 毎月の最初と最後の日の午前 6 時に実行
`{monthDays":[1,14]}` | 毎月 1 日と 14 日の指定された開始時刻に実行。
`{"minutes":[0], "hours":[5], "monthlyOccurrences":[{"day":"friday", "occurrence":1}]}` | 毎月の最初の金曜日の午前 5 時に実行
`{"monthlyOccurrences":[{"day":"friday", "occurrence":1}]}` | 毎月の最初の金曜日の指定された開始時刻に実行。
`{"monthlyOccurrences":[{"day":"friday", "occurrence":-3}]}` | 毎月、月の最後から 3 番目の金曜日の開始時刻に実行
`{"minutes":[15], "hours":[5], "monthlyOccurrences":[{"day":"friday", "occurrence":1},{"day":"friday", "occurrence":-1}]}` | 毎月の最初と最後の金曜日の午前 5 時 15 分に実行
`{"monthlyOccurrences":[{"day":"friday", "occurrence":1},{"day":"friday", "occurrence":-1}]}` | 毎月の最初と最後の金曜日の指定された開始時刻に実行
`{"monthlyOccurrences":[{"day":"friday", "occurrence":5}]}` | 毎月の第 5 金曜日の開始時刻に実行。 第 5 金曜日にのみ実行するスケジュールとなっているため、月に第 5 金曜日がない場合は、パイプラインは実行されません。  月の最終金曜日にトリガーを実行する場合は、occurrence に 5 ではなく -1 を使用することをご検討ください。
`{"minutes":[0,15,30,45], "monthlyOccurrences":[{"day":"friday", "occurrence":-1}]}` | 月の最終金曜日に 15 分ごとに実行。
`{"minutes":[15,45], "hours":[5,17], "monthlyOccurrences":[{"day":"wednesday", "occurrence":3}]}` | 毎月第 3 水曜日の午前 5 時 15 分、午前 5 時 45 分、午後 5 時 15 分、午後 5 時 45 分に実行。




## <a name="next-steps"></a>次のステップ
次のチュートリアルを参照してください。 

- [Quickstart: create a data factory using .NET (クイック スタート: .NET を使用してデータ ファクトリを作成する)](quickstart-create-data-factory-dot-net.md)
