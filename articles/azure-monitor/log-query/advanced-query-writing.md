---
title: Azure Monitor での高度なクエリ | Microsoft Docs
description: この記事では、Analytics ポータルを使用して Azure Monitor でクエリを記述することに関するチュートリアルを提供します。
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 11/15/2018
ms.author: bwren
ms.openlocfilehash: 65713ed9c2d0635e776a7a7e5f205b6d55438ed4
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/06/2019
ms.locfileid: "57451622"
---
# <a name="writing-advanced-queries-in-azure-monitor"></a>Azure Monitor での高度なクエリの記述

> [!NOTE]
> このレッスンを完了する前に、「[Azure Monitor Log Analytics の使用を開始する](get-started-portal.md)」と、[クエリの使用開始](get-started-queries.md)に関する記事の手順を完了しておく必要があります。

[!INCLUDE [log-analytics-demo-environment](../../../includes/log-analytics-demo-environment.md)]

## <a name="reusing-code-with-let"></a>let を使用したコードの再利用
`let` を使用して結果を変数に割り当てます。後ほどクエリでこれを参照します。

```Kusto
// get all events that have level 2 (indicates warning level)
let warning_events=
Event
| where EventLevel == 2;
// count the number of warning events per computer
warning_events
| summarize count() by Computer 
```

定数値を変数に割り当てることもできます。 これは、クエリを実行するたびに変更する必要のあるフィールドに対して、パラメーターを設定するメソッドをサポートしています。 これらのパラメーターを必要に応じて変更します。 たとえば、指定された時間枠の空きディスク領域とメモリを (パーセンタイルで) 計算するには、次を使用します。

```Kusto
let startDate = datetime(2018-08-01T12:55:02);
let endDate = datetime(2018-08-02T13:21:35);
let FreeDiskSpace =
Perf
| where TimeGenerated between (startDate .. endDate)
| where ObjectName=="Logical Disk" and CounterName=="Free Megabytes"
| summarize percentiles(CounterValue, 50, 75, 90, 99);
let FreeMemory =
Perf
| where TimeGenerated between (startDate .. endDate)
| where ObjectName=="Memory" and CounterName=="Available MBytes Memory"
| summarize percentiles(CounterValue, 50, 75, 90, 99);
union FreeDiskSpace, FreeMemory
```

これにより、次回クエリを実行したときに、終了時刻の開始を簡単に変更できるようになります。

### <a name="local-functions-and-parameters"></a>ローカル関数とパラメーター
`let` ステートメントを使用して、同じクエリで使用できる関数を作成します。 たとえば、(UTC 形式の) datetime フィールドを使用し、米国の標準形式に変換する関数を定義します。 

```Kusto
let utc_to_us_date_format = (t:datetime)
{
  strcat(getmonth(t), "/", dayofmonth(t),"/", getyear(t), " ",
  bin((t-1h)%12h+1h,1s), iff(t%24h<12h, "AM", "PM"))
};
Event 
| where TimeGenerated > ago(1h) 
| extend USTimeGenerated = utc_to_us_date_format(TimeGenerated)
| project TimeGenerated, USTimeGenerated, Source, Computer, EventLevel, EventData 
```

## <a name="print"></a>Print
`print` では 1 つの列と行を含むテーブルが返されます。このテーブルには計算結果が表示されています。 これは単純な計算が必要な場合によく使用されます。 たとえば、PST の現在の時刻を検索して EST の列を追加するには、次を使用します。

```Kusto
print nowPst = now()-8h
| extend nowEst = nowPst+3h
```

## <a name="datatable"></a>Datatable
`datatable` を使用すると、データ セットを定義できます。 スキーマと値のセットを指定し、テーブルを、その他のクエリ要素にパイプ処理します。 たとえば、RAM 使用量のテーブルを作成し、1 時間あたりのその平均値を計算するには、次を使用します。

```Kusto
datatable (TimeGenerated: datetime, usage_percent: double)
[
  "2018-06-02T15:15:46.3418323Z", 15.5,
  "2018-06-02T15:45:43.1561235Z", 20.2,
  "2018-06-02T16:16:49.2354895Z", 17.3,
  "2018-06-02T16:46:44.9813459Z", 45.7,
  "2018-06-02T17:15:41.7895423Z", 10.9,
  "2018-06-02T17:44:23.9813459Z", 24.7,
  "2018-06-02T18:14:59.7283023Z", 22.3,
  "2018-06-02T18:45:12.1895483Z", 25.4
]
| summarize avg(usage_percent) by bin(TimeGenerated, 1h)
```

ルックアップ テーブルを作成するときは、Datatable コンストラクトも非常に役立ちます。 たとえば、イベント ID などのテーブル データを、_SecurityEvent_ イベントから他の場所にあるイベントの種類にマップするには、`datatable` を使用してイベントの種類を含むルックアップ テーブルを作成し、この datatable と _SecurityEvent_ データを結合します。

```Kusto
let eventCodes = datatable (EventID: int, EventType:string)
[
    4625, "Account activity",
    4688, "Process action",
    4634, "Account activity",
    4672, "Access",
    4624, "Account activity",
    4799, "Access management",
    4798, "Access management",
    5059, "Key operation",
    4648, "A logon was attempted using explicit credentials",
    4768, "Access management",
    4662, "Other",
    8002, "Process action",
    4904, "Security event management",
    4905, "Security event management",
];
SecurityEvent
| where TimeGenerated > ago(1h) 
| join kind=leftouter (
  eventCodes
) on EventID
| project TimeGenerated, Account, AccountType, Computer, EventType
```

## <a name="next-steps"></a>次の手順
Azure Monitor ログ データと共に [Kusto クエリ言語](/azure/kusto/query/)を使用することに関するその他のレッスンを参照してください。

- [文字列操作](string-operations.md)
- [日付と時刻の操作](datetime-operations.md)
- [集計関数](aggregations.md)
- [高度な集計](advanced-aggregations.md)
- [JSON とデータ構造](json-data-structures.md)
- [結合](joins.md)
- [グラフ](charts.md)
