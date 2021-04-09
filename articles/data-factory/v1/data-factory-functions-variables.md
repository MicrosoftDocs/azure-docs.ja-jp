---
title: Data Factory の関数およびシステム変数
description: Azure Data Factory の関数およびシステム変数の一覧を提供する
author: dcstwh
ms.author: weetok
ms.reviewer: jburchel
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/10/2018
ms.openlocfilehash: 498f40a1783903b5dca0a2fe3204cc6aa25a2fec
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "104786432"
---
# <a name="azure-data-factory---functions-and-system-variables"></a>Azure Data Factory - 関数およびシステム変数
> [!NOTE]
> この記事は、Data Factory のバージョン 1 に適用されます。 現在のバージョンの Data Factory サービスを使用している場合は、[Data Factory のシステム変数](../control-flow-system-variables.md)に関するページを参照してください。

この記事では、Azure Data Factory でサポートされている関数および変数に関する情報を提供します。

## <a name="data-factory-system-variables"></a>Data Factory のシステム変数

| 変数名 | 説明 | オブジェクトのスコープ | JSON のスコープと使用事例 |
| --- | --- | --- | --- |
| WindowStart |現在のアクティビティ実行ウィンドウの時間間隔の開始 |activity |<ol><li>データ選択クエリを指定します。 [データ移動アクティビティ](data-factory-data-movement-activities.md) の記事で参照されているコネクタの記事を参照してください。</li> |
| WindowEnd |現在のアクティビティ実行ウィンドウの時間間隔の終了 |activity |WindowStart と同じです。 |
| SliceStart |生成されているデータ スライスの時間間隔の開始 |activity<br/>dataset |<ol><li>[Azure Blob](data-factory-azure-blob-connector.md) と [ファイル システム データセット](data-factory-onprem-file-system-connector.md) の処理で、動的フォルダー パスおよびファイル名を指定します。</li><li>アクティビティ入力コレクションで、Data Factory 関数を使用して入力の依存関係を指定する。</li></ol> |
| SliceEnd |現在のデータ スライスの時間間隔の終了。 |activity<br/>dataset |SliceStart と同じです。 |

> [!NOTE]
> 現在、データ ファクトリでは、アクティビティに指定されたスケジュールが、出力データセットの availability に指定されたスケジュールと正確に一致する必要があります。 したがって、WindowStart、WindowEnd、SliceStart、SliceEnd は、常に同じ期間と 1 つの出力スライスにマップされます。
> 

### <a name="example-for-using-a-system-variable"></a>システム変数を使用した例
次の例では、**SliceStart** の年、月、日、時刻が **folderPath** プロパティと **fileName** プロパティで使用される個別の変数に抽出されます。

```json
"folderPath": "wikidatagateway/wikisampledataout/{Year}/{Month}/{Day}",
"fileName": "{Hour}.csv",
"partitionedBy":
 [
    { "name": "Year", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyy" } },
    { "name": "Month", "value": { "type": "DateTime", "date": "SliceStart", "format": "MM" } },
    { "name": "Day", "value": { "type": "DateTime", "date": "SliceStart", "format": "dd" } },
    { "name": "Hour", "value": { "type": "DateTime", "date": "SliceStart", "format": "hh" } }
],
```

## <a name="data-factory-functions"></a>Data Factory の関数
Data Factory の関数は、システム変数と共に次の用途で使用できます。

1. データ選択クエリを指定する ( [データ移動アクティビティ](data-factory-data-movement-activities.md) の記事で参照されているコネクタの記事を参照してください。
   
   データ選択クエリと、アクティビティおよびデータセットの他のプロパティの場合、Data Factory 関数を呼び出す構文は **$$\<function>** です。  
2. アクティビティ入力コレクションで、Data Factory 関数を使用して入力の依存関係を指定する。
   
    入力の依存関係式を指定する場合、$$ は不要です。     

次のサンプルでは、JSON ファイルの **sqlReaderQuery** プロパティが、`Text.Format` 関数から返される値に割り当てられます。 また、**WindowStart** というシステム変数も使用されます。この変数は、アクティビティ実行ウィンドウの開始時刻を表します。

```json
{
    "Type": "SqlSource",
    "sqlReaderQuery": "$$Text.Format('SELECT * FROM MyTable WHERE StartTime = \\'{0:yyyyMMdd-HH}\\'', WindowStart)"
}
```

使用できるさまざまな書式設定オプション (例: yy と yyyy) については、「[カスタム日時書式指定文字列](/dotnet/standard/base-types/custom-date-and-time-format-strings)」を参照してください。 

### <a name="functions"></a>関数
次の表は、Azure Data Factory の全関数の一覧です。

| カテゴリ | 機能 | パラメーター | 説明 |
| --- | --- | --- | --- |
| Time |AddHours(X,Y) |X:DateTime <br/><br/>Y: int |指定した時刻 X に Y 時間を追加します。 <br/><br/>例: `9/5/2013 12:00:00 PM + 2 hours = 9/5/2013 2:00:00 PM` |
| Time |AddMinutes(X,Y) |X:DateTime <br/><br/>Y: int |X に Y 分を追加します。<br/><br/>例: `9/15/2013 12: 00:00 PM + 15 minutes = 9/15/2013 12: 15:00 PM` |
| Time |StartOfHour(X) |X:Datetime |X の時間コンポーネントで表される時間の開始時刻を取得します。 <br/><br/>例: `StartOfHour of 9/15/2013 05: 10:23 PM is 9/15/2013 05: 00:00 PM` |
| Date |AddDays(X,Y) |X:DateTime<br/><br/>Y: int |X に Y 日を追加します。 <br/><br/>例:9/15/2013 12:00:00 PM + 2 days = 9/17/2013 12:00:00 PM<br/><br/>Y を負数に指定して、日数を減算することもできます。<br/><br/>例: `9/15/2013 12:00:00 PM - 2 days = 9/13/2013 12:00:00 PM`. |
| Date |AddMonths(X,Y) |X:DateTime<br/><br/>Y: int |X に Y か月を追加します。<br/><br/>`Example: 9/15/2013 12:00:00 PM + 1 month = 10/15/2013 12:00:00 PM`.<br/><br/>Y を負数に指定して、月数を減算することもできます。<br/><br/>例: `9/15/2013 12:00:00 PM - 1 month = 8/15/2013 12:00:00 PM`.|
| Date |AddQuarters(X,Y) |X:DateTime <br/><br/>Y: int |X に Y * 3 か月を追加します。<br/><br/>例: `9/15/2013 12:00:00 PM + 1 quarter = 12/15/2013 12:00:00 PM` |
| Date |AddWeeks(X,Y) |X:DateTime<br/><br/>Y: int |X に Y * 7 日を追加します。<br/><br/>例:9/15/2013 12:00:00 PM + 1 week = 9/22/2013 12:00:00 PM<br/><br/>Y を負数に指定して、週数を減算することもできます。<br/><br/>例: `9/15/2013 12:00:00 PM - 1 week = 9/7/2013 12:00:00 PM`. |
| Date |AddYears(X,Y) |X:DateTime<br/><br/>Y: int |X に Y 年を追加します。<br/><br/>`Example: 9/15/2013 12:00:00 PM + 1 year = 9/15/2014 12:00:00 PM`<br/><br/>Y を負数に指定して、年数を減算することもできます。<br/><br/>例: `9/15/2013 12:00:00 PM - 1 year = 9/15/2012 12:00:00 PM`. |
| Date |Day(X) |X:DateTime |X の日付コンポーネントを取得します。<br/><br/>例: `Day of 9/15/2013 12:00:00 PM is 9`. |
| Date |DayOfWeek(X) |X:DateTime |X の曜日コンポーネントを取得します。<br/><br/>例: `DayOfWeek of 9/15/2013 12:00:00 PM is Sunday`. |
| Date |DayOfYear(X) |X:DateTime |X の年コンポーネントで表される、その年の日付を取得します。<br/><br/>例 :<br/>`12/1/2015: day 335 of 2015`<br/>`12/31/2015: day 365 of 2015`<br/>`12/31/2016: day 366 of 2016 (Leap Year)` |
| Date |DaysInMonth(X) |X:DateTime |パラメーター X の月コンポーネントで表される、その月の日数を取得します。<br/><br/>例: `DaysInMonth of 9/15/2013 are 30 since there are 30 days in the September month`. |
| Date |EndOfDay(X) |X:DateTime |X の日 (日コンポーネント) の終了を表す日時を取得します。<br/><br/>例: `EndOfDay of 9/15/2013 05:10:23 PM is 9/15/2013 11:59:59 PM`. |
| Date |EndOfMonth(X) |X:DateTime |パラメーター X の月コンポーネントで表される月の終了を取得します。 <br/><br/>例: `EndOfMonth of 9/15/2013 05:10:23 PM is 9/30/2013 11:59:59 PM` (9 月の終了を表す日時) |
| Date |StartOfDay(X) |X:DateTime |パラメーター X の日コンポーネントで表される日の開始を取得します。<br/><br/>例: `StartOfDay of 9/15/2013 05:10:23 PM is 9/15/2013 12:00:00 AM`. |
| DateTime |From(X) |X:String |文字列 X を日時にパースします。 |
| DateTime |Ticks(X) |X:DateTime |パラメーター X の ticks プロパティを取得します。1 ティックは 100 ナノ秒です。 このプロパティの値は、0001 年 1 月 1 日深夜 12:00:00 以降の経過時間のティック数を表しています。 |
| Text |Format(X) |X:文字列変数 |テキストを書式設定します (`\\'` の組み合わせを使用して `'` 文字をエスケープします)。|

> [!IMPORTANT]
> 別の関数内で関数を使用する場合、内側の関数に **$$** プレフィックスを付ける必要はありません。 例: $$Text.Format('PartitionKey eq \\'my_pkey_filter_value\\' and RowKey ge \\'{0: yyyy-MM-dd HH:mm:ss}\\'', Time.AddHours(SliceStart, -6))。 この例でわかるように、**Time.AddHours** 関数に **$$** プレフィックスは使用されていません。 

#### <a name="example"></a>例
次の例では、Hive アクティビティの入力パラメーターと出力パラメーターが、`Text.Format` 関数と SliceStart システム変数を使用して決定されます。 

```json  
{
    "name": "HiveActivitySamplePipeline",
        "properties": {
    "activities": [
            {
            "name": "HiveActivitySample",
            "type": "HDInsightHive",
            "inputs": [
                    {
                    "name": "HiveSampleIn"
                    }
            ],
            "outputs": [
                    {
                    "name": "HiveSampleOut"
                }
            ],
            "linkedServiceName": "HDInsightLinkedService",
            "typeproperties": {
                    "scriptPath": "adfwalkthrough\\scripts\\samplehive.hql",
                    "scriptLinkedService": "StorageLinkedService",
                    "defines": {
                        "Input": "$$Text.Format('wasb://adfwalkthrough@<storageaccountname>.blob.core.windows.net/samplein/yearno={0:yyyy}/monthno={0:MM}/dayno={0:dd}/', SliceStart)",
                        "Output": "$$Text.Format('wasb://adfwalkthrough@<storageaccountname>.blob.core.windows.net/sampleout/yearno={0:yyyy}/monthno={0:MM}/dayno={0:dd}/', SliceStart)"
                    },
                    "scheduler": {
                        "frequency": "Hour",
                        "interval": 1
                }
            }
            }
    ]
    }
}
```

### <a name="example-2"></a>例 2

次の例では、ストアド プロシージャ アクティビティの DateTime パラメーターが、Text. Format 関数と SliceStart 変数を使用して決定されます。 

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
                "outputs": [
                    {
                        "name": "sprocsampleout"
                    }
                ],
                "scheduler": {
                    "frequency": "Hour",
                    "interval": 1
                },
                "name": "SprocActivitySample"
            }
        ],
            "start": "2016-08-02T00:00:00Z",
            "end": "2016-08-02T05:00:00Z",
        "isPaused": false
    }
}
```

### <a name="example-3"></a>例 3
SliceStart によって表される日ではなく前の日のデータを読み取るには、次の例に示されているように、AddDays 関数を使用します。 

```json
{
    "name": "SamplePipeline",
    "properties": {
        "start": "2016-01-01T08:00:00",
        "end": "2017-01-01T11:00:00",
        "description": "hive activity",
        "activities": [
            {
                "name": "SampleHiveActivity",
                "inputs": [
                    {
                        "name": "MyAzureBlobInput",
                        "startTime": "Date.AddDays(SliceStart, -1)",
                        "endTime": "Date.AddDays(SliceEnd, -1)"
                    }
                ],
                "outputs": [
                    {
                        "name": "MyAzureBlobOutput"
                    }
                ],
                "linkedServiceName": "HDInsightLinkedService",
                "type": "HDInsightHive",
                "typeProperties": {
                    "scriptPath": "adftutorial\\hivequery.hql",
                    "scriptLinkedService": "StorageLinkedService",
                    "defines": {
                        "Year": "$$Text.Format('{0:yyyy}',WindowsStart)",
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

使用できるさまざまな書式設定オプション (例: yy と yyyy) については、「 [カスタム日時書式指定文字列](/dotnet/standard/base-types/custom-date-and-time-format-strings) 」をご覧ください。