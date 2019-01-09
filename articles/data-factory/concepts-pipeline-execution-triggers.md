---
title: Azure Data Factory でのパイプラインの実行とトリガー| Microsoft Docs
description: この記事では、オンデマンドで、またはトリガーを作成して、Azure Data Factory でパイプラインを実行する方法に関する情報を提供します。
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/05/2018
ms.author: shlo
ms.openlocfilehash: 58fffafe9658919a96d1aef2881424c0d324e688
ms.sourcegitcommit: b0f39746412c93a48317f985a8365743e5fe1596
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/04/2018
ms.locfileid: "52876479"
---
# <a name="pipeline-execution-and-triggers-in-azure-data-factory"></a>Azure Data Factory でのパイプラインの実行とトリガー
> [!div class="op_single_selector" title1="使用している Data Factory サービスのバージョンを選択してください:"]
> * [Version 1](v1/data-factory-scheduling-and-execution.md)
> * [現在のバージョン](concepts-pipeline-execution-triggers.md)

Azure Data Factory の "_パイプライン実行_" により、パイプラインの実行のインスタンスが定義されます。 たとえば、午前 8 時、午前 9 時、午前 10 時に実行するパイプラインがあるとします。 この場合、パイプラインの 3 つの独立した実行 (パイプライン実行) があることになります。 各パイプライン実行には、一意のパイプライン実行 ID があります。 実行 ID は、特定のパイプライン実行を一意に定義する GUID です。

パイプライン実行は、通常、パイプラインで定義したパラメーターに引数を渡してインスタンス化されます。 パイプラインを実行するには、手動で行う方法と "_トリガー_" を使用する方法があります。 この記事では、パイプラインを実行する両方の方法の詳細について説明します。

## <a name="manual-execution-on-demand"></a>手動での実行 (オンデマンド)
パイプラインの手動での実行は、"_オンデマンド_" 実行とも呼ばれます。

たとえば、**copyPipeline** という名前の基本的なパイプラインを実行するとします。 このパイプラインは、Azure Blob Storage のソース フォルダーから、同じストレージ内のコピー先フォルダーにコピーするアクティビティを伴う 1 つの単純なパイプラインです。 次の JSON 定義は、このサンプル パイプラインを示します。

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

この JSON 定義のパイプラインは、**sourceBlobContainer** および **sinkBlobContainer** という 2 つのパラメーターを使用します。 実行時に、これらのパラメーターに値を渡します。

次のいずれかの方法を使用して、パイプラインを手動で実行できます。
- .NET SDK
- Azure PowerShell モジュール
- REST API
- Python SDK

### <a name="rest-api"></a>REST API
次のサンプル コマンドは、REST API を使用してパイプラインを手動で実行する方法を示します。

```
POST
https://management.azure.com/subscriptions/mySubId/resourceGroups/myResourceGroup/providers/Microsoft.DataFactory/factories/myDataFactory/pipelines/copyPipeline/createRun?api-version=2017-03-01-preview
```

サンプルの詳細については、[REST API を使用してデータ ファクトリを作成する方法のクイック スタート](quickstart-create-data-factory-rest-api.md)に関する記事を参照してください。

### <a name="azure-powershell"></a>Azure PowerShell
次のサンプル コマンドは、Azure PowerShell を使用してパイプラインを手動で実行する方法を示します。

```powershell
Invoke-AzureRmDataFactoryV2Pipeline -DataFactory $df -PipelineName "Adfv2QuickStartPipeline" -ParameterFile .\PipelineParameters.json
```

パラメーターは要求ペイロードの本文に入れて渡します。 .NET SDK、Azure PowerShell、Python SDK では、呼び出しに引数として渡されるディクショナリに値を入れて渡します。

```json
{
  "sourceBlobContainer": "MySourceFolder",
  "sinkBlobContainer": "MySinkFolder"
}
```

応答ペイロードは、パイプライン実行の一意の ID です。

```json
{
  "runId": "0448d45a-a0bd-23f3-90a5-bfeea9264aed"
}
```

サンプルの詳細については、[Azure PowerShell を使用してデータ ファクトリを作成する方法のクイック スタート](quickstart-create-data-factory-powershell.md)に関する記事を参照してください。

### <a name="net-sdk"></a>.NET SDK
次のサンプル呼び出しは、.NET SDK を使用してパイプラインを手動で実行する方法を示します。

```csharp
client.Pipelines.CreateRunWithHttpMessagesAsync(resourceGroup, dataFactoryName, pipelineName, parameters)
```

サンプルの詳細については、[.NET SDK を使用してデータ ファクトリを作成する方法のクイック スタート](quickstart-create-data-factory-dot-net.md)に関する記事を参照してください。

> [!NOTE]
> .NET SDK を使用すると、Azure Functions や独自の Web サービスなどから Data Factory パイプラインを呼び出すことができます。

<h2 id="triggers">トリガー実行</h2>
トリガーは、パイプラインの実行を開始するためのもう 1 つの方法です。 トリガーは、パイプラインの実行をいつ開始する必要があるかを決定する処理単位を表します。 現時点で、Data Factory では次の 3 種類のトリガーがサポートされています。

- スケジュール トリガー:実時間のスケジュールによってパイプラインを起動するトリガー。

- タンブリング ウィンドウ トリガー:状態を保持しながら定期的に実行されるトリガー。

- イベントベースのトリガー:イベントに応答するトリガー。

パイプラインとトリガーには多対多の関係があります。 複数のトリガーで 1 つのパイプラインを開始したり、1 つのトリガーで複数のパイプラインを開始したりできます。 次のトリガー定義では、**pipelines** プロパティは、特定のトリガーによってトリガーされるパイプラインのリストを参照します。 プロパティ定義には、パイプライン パラメーターの値が含まれています。

### <a name="basic-trigger-definition"></a>基本的なトリガー定義

```json
{
    "properties": {
        "name": "MyTrigger",
        "type": "<type of trigger>",
        "typeProperties": {...},
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
                    "<parameter 2 Name>": "<parameter 2 Value>"
                }
            }
        ]
    }
}
```

## <a name="schedule-trigger"></a>スケジュール トリガー
スケジュール トリガーは、実時間のスケジュールでパイプラインを実行します。 このトリガーは、定期的および高度な予定表のオプションをサポートしています。 たとえば、トリガーでは、"毎週" または "月曜日午後 5 時と木曜日午後 9 時" のような間隔がサポートされています。 スケジュール トリガーは、時系列データと非時系列データを区別せず、データセット パターンに依存しないため、柔軟性があります。

スケジュール トリガーの詳細と例については、[スケジュール トリガーの作成](how-to-create-schedule-trigger.md)に関するページを参照してください。

## <a name="schedule-trigger-definition"></a>スケジュール トリガーの定義
スケジュール トリガーを作成する場合、JSON 定義を使用してスケジュール設定と繰り返しを指定します。

スケジュール トリガーにパイプライン実行を開始させるには、特定のパイプラインのパイプライン参照をトリガー定義に組み込みます。 パイプラインとトリガーには多対多の関係があります。 複数のトリガーが 1 つのパイプラインを開始することができます。 1 つのトリガーが複数のパイプラインを開始することもできます。

```json
{
  "properties": {
    "type": "ScheduleTrigger",
    "typeProperties": {
      "recurrence": {
        "frequency": <<Minute, Hour, Day, Week, Year>>,
        "interval": <<int>>, // How often to fire
        "startTime": <<datetime>>,
        "endTime": <<datetime>>,
        "timeZone": "UTC",
        "schedule": { // Optional (advanced scheduling specifics)
          "hours": [<<0-24>>],
          "weekDays": [<<Monday-Sunday>>],
          "minutes": [<<0-60>>],
          "monthDays": [<<1-31>>],
          "monthlyOccurrences": [
            {
              "day": <<Monday-Sunday>>,
              "occurrence": <<1-5>>
            }
          ]
        }
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
        "<parameter 2 Name>": "<parameter 2 Value>"
      }
    }
  ]}
}
```

> [!IMPORTANT]
> **parameters** プロパティは、**pipelines** 要素の必須プロパティです。 パイプラインにパラメーターがない場合は、**parameters** プロパティ用に空の JSON 定義を含める必要があります。

### <a name="schema-overview"></a>スキーマの概要
次の表は、トリガーの繰り返しとスケジュール設定に関連する主要なスキーマ要素の概要を示しています。

| JSON プロパティ | 説明 |
|:--- |:--- |
| **startTime** | 日付/時刻の値。 基本的なスケジュールの場合、**startTime** プロパティの値が最初の発生日時に適用されます。 複雑なスケジュールの場合、指定した **startTime** 値になるとすぐにトリガーが起動します。 |
| **endTime** | トリガーの終了日時。 指定した終了日時を過ぎると、トリガーは実行されません。 このプロパティの値に過去の日時を指定することはできません。 <!-- This property is optional. --> |
| **timeZone** | タイム ゾーン。 現在、サポートされているタイム ゾーンは UTC のみです。 |
| **recurrence** | トリガーの繰り返し規則を指定する recurrence オブジェクト。 この recurrence オブジェクトは、**frequency**、**interval** **endTime** **count**、**schedule** の各要素をサポートします。 recurrence オブジェクトを定義する場合、**frequency** 要素は必須です。 recurrence オブジェクトの他の要素は省略可能です。 |
| **frequency** | トリガーが繰り返される頻度の単位。 サポートされる値には、"minute"、"hour"、"day"、"week"、"month" があります。 |
| **interval** | **frequency** 値の間隔を示す正の整数。 **frequency** の値は、トリガーの実行頻度を決定します。 たとえば、**interval** が 3 で **frequency** が "week" の場合、トリガーは 3 週間ごとに繰り返されます。 |
| **schedule** | トリガーの繰り返しのスケジュール。 **frequency** 値が指定されたトリガーは、繰り返しのスケジュールに基づいて繰り返しが変更されます。 **schedule** プロパティには、分、時間、曜日、日にち、週番号に基づいた繰り返しの変更を指定します。

### <a name="schedule-trigger-example"></a>スケジュール トリガーの例

```json
{
    "properties": {
        "name": "MyTrigger",
        "type": "ScheduleTrigger",
        "typeProperties": {
            "recurrence": {
                "frequency": "Hour",
                "interval": 1,
                "startTime": "2017-11-01T09:00:00-08:00",
                "endTime": "2017-11-02T22:00:00-08:00"
            }
        },
        "pipelines": [{
                "pipelineReference": {
                    "type": "PipelineReference",
                    "referenceName": "SQLServerToBlobPipeline"
                },
                "parameters": {}
            },
            {
                "pipelineReference": {
                    "type": "PipelineReference",
                    "referenceName": "SQLServerToAzureSQLPipeline"
                },
                "parameters": {}
            }
        ]
    }
}
```

### <a name="schema-defaults-limits-and-examples"></a>スキーマの既定値、制限、例

| JSON プロパティ | type | 必須 | 既定値 | 有効な値 | 例 |
|:--- |:--- |:--- |:--- |:--- |:--- |
| **startTime** | string | [はい] | なし | ISO 8601 の日付/時刻 | `"startTime" : "2013-01-09T09:30:00-08:00"` |
| **recurrence** | オブジェクト | [はい] | なし | recurrence オブジェクト | `"recurrence" : { "frequency" : "monthly", "interval" : 1 }` |
| **interval** | number | いいえ  | 1 | 1 から 1,000 | `"interval":10` |
| **endTime** | string | [はい] | なし | 将来の時刻を表す日付/時刻の値 | `"endTime" : "2013-02-09T09:30:00-08:00"` |
| **schedule** | オブジェクト | いいえ  | なし | schedule オブジェクト | `"schedule" : { "minute" : [30], "hour" : [8,17] }` |

### <a name="starttime-property"></a>startTime プロパティ
次の表に、**startTime** プロパティでトリガー実行を制御する方法を示します。

| startTime の値 | スケジュールなしの繰り返し | スケジュールありの繰り返し |
|:--- |:--- |:--- |
| **開始時刻が過去** | 開始時刻より後の将来の最初の実行時刻を計算し、その時刻に実行されます。<br /><br />2 回目以降は、最後の実行時刻から計算して実行します。<br /><br />この表の後の例を参照してください。 | トリガーは、指定した開始時刻になると "_すぐに_" 起動します。 最初は、開始時刻から計算したスケジュールに基づいて実行されます。<br /><br />以降は、繰り返しのスケジュールに基づいて実行されます。 |
| **開始時刻が将来または現在** | 指定した開始時刻に 1 回実行されます。<br /><br />2 回目以降は、最後の実行時刻から計算して実行します。 | トリガーは、指定した開始時刻になると "_すぐに_" 起動します。 最初は、開始時刻から計算したスケジュールに基づいて実行されます。<br /><br />以降は、繰り返しのスケジュールに基づいて実行されます。 |

ここでは、開始時刻が過去であり、スケジュールなしの繰り返しが設定されている場合の動作の例を説明します。 現在の時刻が 2017 年 4 月 8 日 13 時、開始時刻が 2017 年 4 月 7 日 14 時、繰り返しが 2 日ごとであると仮定します (**recurrence** 値を定義するには、**frequency** プロパティを "day"、**interval** プロパティを 2 に設定します)。**startTime** 値が過去であり、現在の時刻よりも前であることに注意してください。

この条件では、最初の実行は 2017 年 4 月 9 日の 14 時になります。 Scheduler エンジンは、開始時刻から実行を計算します。 過去のインスタンスはすべて破棄されます。 エンジンは、将来発生する次回のインスタンスを使用します。 このシナリオでは、開始時刻は 2017 年 4 月 7 日の午後 2 時になります。 次回のインスタンスは 2 日後、つまり 2017 年 4 月 9 日の午後 2 時となります。

**startTime** が 2017 年 4 月 5 日 14 時または 2017 年 4 月 1 日 14 時であっても、最初の実行時間は同じです。 最初の実行後、以降の実行はスケジュールを使用して計算されます。 したがって、以降の実行は、2017 年 4 月 11 日の午後 2 時、2017 年 4 月 13 日の午後 2 時、2017 年 4 月 15 日の午後 2 時と続きます。

最後に、トリガーのスケジュールに時間または分が設定されていない場合は、最初の実行の時間または分が既定値として使用されます。

### <a name="schedule-property"></a>schedule プロパティ
**schedule** を使用すると、トリガーの実行回数を "*制限*" できます。 たとえば、月単位の頻度を指定したトリガーを 31 日にのみ実行するようにスケジュールすると、トリガーは 31 日がある月にのみ実行されます。

**schedule** を使用してトリガーの実行回数を "*増やす*" こともできます。 たとえば、月単位の頻度を指定したトリガーを月の 1 日と 2 日に実行するようにスケジュールすると、トリガーは月に 1 回ではなく、月の 1 日と 2 日に実行されます。

複数の **schedule** 要素を指定した場合は、評価の順序は大きいスケジュール設定から小さいスケジュール設定の順序となります。つまり、週番号、月の日、曜日、時間、および分という順序です。

次の表に、**schedule** の要素の詳細を示します。

| JSON 要素 | 説明 | 有効な値 |
|:--- |:--- |:--- |
| **分** | トリガーを実行する時刻 (分)。 |- Integer<br />- 整数の配列|
| **hours** | トリガーを実行する時刻 (時)。 |- Integer<br />- 整数の配列|
| **weekDays** | トリガーを実行する曜日。 この値を指定できるのは、頻度が週単位の場合のみです。|<br />- Monday<br />- Tuesday<br />- Wednesday<br />- Thursday<br />- Friday<br />- Saturday<br />- Sunday<br />- 曜日の値の配列 (配列の最大サイズは 7)<br /><br />曜日の値の大文字小文字は区別されません|
| **monthlyOccurrences** | トリガーが実行される月の特定曜日。 この値を指定できるのは、頻度が月単位の場合のみです。 |- **monthlyOccurrence** オブジェクトの配列: `{ "day": day,  "occurrence": occurrence }`<br />- **day** 属性は、トリガーが実行される曜日を表します。 たとえば、**monthlyOccurrences** プロパティの **day** 値が `{Sunday}` の場合は、月の毎週日曜日を意味します。 **day** 属性は必須です。<br />- **occurrence** 属性は、月内の指定した **day** の出現を表します。 たとえば、**monthlyOccurrences** プロパティの **day** 値と **occurrence** 値が `{Sunday, -1}` の場合、月の最後の日曜日を意味します。 **occurrence** 属性は省略可能です。|
| **monthDays** | トリガーが実行される日にち。 この値を指定できるのは、頻度が月単位の場合のみです。 |- -1 以下かつ -31 以上の任意の値<br />- 1 以上かつ 31 以下の任意の値<br />- 値の配列|

## <a name="tumbling-window-trigger"></a>タンブリング ウィンドウ トリガー
タンブリング ウィンドウ トリガーは、状態を維持しながら、指定した開始時刻から定期的に実行される種類のトリガーです。 タンブリング ウィンドウとは、固定サイズで重複しない一連の連続する時間間隔です。

タンブリング ウィンドウ トリガーの詳細と例については、[タンブリング ウィンドウ トリガーの作成](how-to-create-tumbling-window-trigger.md)に関するページを参照してください。

## <a name="event-based-trigger"></a>イベントベースのトリガー

イベントベースのトリガーは、ファイルの到着、ファイルの削除などのイベントに応答して、Azure Blob Storage 内でパイプラインを実行します。

イベントベースのトリガーの詳細については、「[Create a trigger that runs a pipeline in response to an event (イベントに応答してパイプラインを実行するトリガーの作成)](how-to-create-event-trigger.md)」を参照してください。

## <a name="examples-of-trigger-recurrence-schedules"></a>トリガーの繰り返しのスケジュールの例
このセクションでは、繰り返しのスケジュールの例を示します。 **schedule** オブジェクトとその要素に焦点を当てています。

各例は、**interval** 値が 1 であり、schedule の定義に従って **frequency** に適切な値が指定されていることを前提としています。 たとえば、**frequency** 値に "day" を指定し、**schedule** オブジェクトで **monthDays** の変更を指定することはできません。 これらの種類の制限については、前のセクションの表で説明されています。

| 例 | 説明 |
|:--- |:--- |
| `{"hours":[5]}` | 毎日午前 5 時に実行されます。 |
| `{"minutes":[15], "hours":[5]}` | 毎日午前 5 時 15 分に実行されます。 |
| `{"minutes":[15], "hours":[5,17]}` | 毎日午前 5 時 15 分と午後 5 時 15 分に実行されます。 |
| `{"minutes":[15,45], "hours":[5,17]}` | 毎日午前 5 時 15 分、午前 5 時 45 分、午後 5 時 15 分、午後 5 時 45 分に実行されます。 |
| `{"minutes":[0,15,30,45]}` | 15 分ごとに実行されます。 |
| `{hours":[0, 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 17, 18, 19, 20, 21, 22, 23]}` | 1 時間ごとに実行されます。<br /><br />このトリガーは 1 時間おきに実行されます。 **startTime** 値が指定されている場合、分はこの値によって制御されます。 値が指定されていない場合は、作成時刻によって制御されます。 たとえば、開始時刻または作成時刻が (どちらが適用される場合でも) 午後 12 時 25 分の場合、トリガーは 0 時 25 分、1 時 25 分、2 時 25 分...23 時 25 分に実行されます。<br /><br />このスケジュールは、**frequency** 値に "hour"、**interval** 値に 1 を指定し、**schedule** を指定しないトリガーに相当します。 **frequency** と **interval** に別の値を指定してこのスケジュールを使用すると、他のトリガーを作成できます。 たとえば、**frequency** 値が "month" の場合は、月に 1 回だけ実行され、**frequency** 値が "day" の場合は、毎日実行されます。 |
| `{"minutes":[0]}` | 毎正時に実行されます。<br /><br />このトリガーは、毎正時 (午前 0 時、午前 1 時、午前 2 時 (以降も同様)) に実行されます。<br /><br />このスケジュールは、**frequency** 値に "hour"、**startTime** 値に 0 分を指定したトリガー、または **schedule** を設定せずに **frequency** 値に "day" を指定したトリガーに相当します。 **frequency** 値が "week" または "month" の場合、スケジュールはそれぞれ週に 1 日または月に 1 日だけ実行されます。 |
| `{"minutes":[15]}` | 毎正時から 15 分後に実行されます。<br /><br />このトリガーは、毎正時から 15 分後 (午前 0 時 15 分、午前 1 時 15 分、午前 2 時 15 分 (以降も同様)、最後が午後 11 時 15 分) に実行されます。 |
| `{"hours":[17], "weekDays":["saturday"]}` | 毎週土曜日の午後 5 時に実行されます。 |
| `{"hours":[17], "weekDays":["monday", "wednesday", "friday"]}` | 毎週月曜日、水曜日、金曜日の午後 5 時に実行されます。 |
| `{"minutes":[15,45], "hours":[17], "weekDays":["monday", "wednesday", "friday"]}` | 毎週月曜日、水曜日、金曜日の午後 5 時 15 分と午後 5 時 45 分に実行されます。 |
| `{"minutes":[0,15,30,45], "weekDays":["monday", "tuesday", "wednesday", "thursday", "friday"]}` | 平日に 15 分ごとに実行されます。 |
| `{"minutes":[0,15,30,45], "hours": [9, 10, 11, 12, 13, 14, 15, 16] "weekDays":["monday", "tuesday", "wednesday", "thursday", "friday"]}` | 平日の午前 9 時から午後 4 時 45 分まで 15 分ごとに実行されます。 |
| `{"weekDays":["tuesday", "thursday"]}` | 火曜日と木曜日の指定された開始時刻に実行されます。 |
| `{"minutes":[0], "hours":[6], "monthDays":[28]}` | 毎月 28 日の午前 6 時に実行されます (**frequency** 値が "month" であることを想定)。 |
| `{"minutes":[0], "hours":[6], "monthDays":[-1]}` | 月の最終日の午前 6 時に実行されます。<br /><br />月の最終日にトリガーを実行するには、28、29、30、31 という日にちではなく -1 を使用します。 |
| `{"minutes":[0], "hours":[6], "monthDays":[1,-1]}` | 毎月最初と最後の日の午前 6 時に実行されます。 |
| `{monthDays":[1,14]}` | 毎月 1 日と 14 日の指定された開始時刻に実行されます。 |
| `{"minutes":[0], "hours":[5], "monthlyOccurrences":[{"day":"friday", "occurrence":1}]}` | 毎月最初の金曜日の午前 5 時に実行されます。 |
| `{"monthlyOccurrences":[{"day":"friday", "occurrence":1}]}` | 毎月最初の金曜日の指定された開始時刻に実行されます。 |
| `{"monthlyOccurrences":[{"day":"friday", "occurrence":-3}]}` | 毎月、月の最後から 3 番目の金曜日の指定された開始時刻に実行されます。 |
| `{"minutes":[15], "hours":[5], "monthlyOccurrences":[{"day":"friday", "occurrence":1},{"day":"friday", "occurrence":-1}]}` | 毎月最初と最後の金曜日の午前 5 時 15 分に実行されます。 |
| `{"monthlyOccurrences":[{"day":"friday", "occurrence":1},{"day":"friday", "occurrence":-1}]}` | 毎月最初と最後の金曜日の指定された開始時刻に実行されます。 |
| `{"monthlyOccurrences":[{"day":"friday", "occurrence":5}]}` | 毎月第 5 金曜日の指定された開始時刻に実行されます。<br /><br />月に第 5 金曜日がない場合、パイプラインは実行されません。 月の最後の金曜日にトリガーを実行する場合は、**occurrence** 値に 5 ではなく -1 を使用することを検討してください。 |
| `{"minutes":[0,15,30,45], "monthlyOccurrences":[{"day":"friday", "occurrence":-1}]}` | 月の最後の金曜日に 15 分ごとに実行されます。 |
| `{"minutes":[15,45], "hours":[5,17], "monthlyOccurrences":[{"day":"wednesday", "occurrence":3}]}` | 毎月第 3 水曜日の午前 5 時 15 分、午前 5 時 45 分、午後 5 時 15 分、午後 5 時 45 分に実行されます。 |

## <a name="trigger-type-comparison"></a>トリガーの種類の比較
タンブリング ウィンドウ トリガーとスケジュール トリガーは、どちらも時間のハートビートに対して動作します。 これらはどのように違うのでしょうか。

次の表では、タンブリング ウィンドウ トリガーとスケジュール トリガーの比較を示します。

|  | タンブリング ウィンドウ トリガー | スケジュール トリガー |
|:--- |:--- |:--- |
| **バックフィル シナリオ** | サポートされています。 パイプライン実行は、過去のウィンドウ用にスケジュールを設定できます。 | サポートされていません。 パイプライン実行は、現時点および将来の期間のみに対して実行できます。 |
| **信頼性** | 100% の信頼性です。 パイプライン実行は、指定した開始日から隙間なくすべてのウィンドウでスケジュールできます。 | 信頼性は低くなります。 |
| **再試行機能** | サポートされています。 失敗したパイプライン実行には、既定の再試行ポリシー 0 か、トリガー定義でユーザーが指定したポリシーがあります。 コンカレンシー/サーバー/調整の制限が原因でパイプライン実行に失敗した場合は、自動的に再試行されます (つまり、状態コードが 400:ユーザー エラー、429:要求が多すぎる、500:内部サーバー エラーの場合)。 | サポートされていません。 |
| **コンカレンシー** | サポートされています。 ユーザーは、トリガーのコンカレンシーの制限を明示的に設定できます。 1 から 50 個の同時実行のトリガーされたパイプライン実行が許可されます。 | サポートされていません。 |
| **システム変数** | **WindowStart** および **WindowEnd** システム変数の使用がサポートされます。 ユーザーは、トリガー定義のトリガー システム変数として `triggerOutputs().windowStartTime` および `triggerOutputs().windowEndTime` にアクセスできます。 値はそれぞれ、ウィンドウの開始時刻と終了時刻として使用されます。 たとえば、1 時間ごとに実行されるタンブリング ウィンドウ トリガーの場合、午前 1 時から午前 2 時までのウィンドウの定義は `triggerOutputs().WindowStartTime = 2017-09-01T01:00:00Z` と `triggerOutputs().WindowEndTime = 2017-09-01T02:00:00Z` です。 | サポートされていません。 |
| **パイプラインとトリガーのリレーションシップ** | 一対一のリレーションシップをサポートします。 トリガーできるパイプラインは 1 つだけです。 | 多対多のリレーションシップをサポートします。 複数のトリガーが 1 つのパイプラインを開始することができます。 1 つのトリガーが複数のパイプラインを開始することもできます。 |

## <a name="next-steps"></a>次の手順
次のチュートリアルを参照してください。

- [クイック スタート:.NET SDK を使用してデータ ファクトリを作成する](quickstart-create-data-factory-dot-net.md)
- [スケジュール トリガーの作成](how-to-create-schedule-trigger.md)
- [タンブリング ウィンドウ トリガーの作成](how-to-create-tumbling-window-trigger.md)
