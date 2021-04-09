---
title: 時間のギャップを埋めて欠損値を補完する - Azure SQL Edge
description: Azure SQL Edge で時間のギャップを埋めたり、欠損値を補完したりする方法について説明します
keywords: SQL Edge, TimeSeries
services: sql-edge
ms.service: sql-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 09/22/2020
ms.openlocfilehash: c444732a497d595235ac337d7f5c71fb84f17cca
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "96185591"
---
# <a name="filling-time-gaps-and-imputing-missing-values"></a>時間のギャップを埋めて欠損値を補完する 

時系列データを処理する場合、時系列データの属性に欠損値がある可能性があります。 また、データの性質によって、またはデータ収集が中断しているために、データセットに時間の *ギャップ* がある可能性もあります。

たとえば、スマート デバイスのエネルギー使用状況の統計情報を収集する場合、デバイスが動作していないときは常に、使用状況の統計にギャップが生じます。 同様に、マシンの利用統計情報の収集シナリオでは、さまざまなセンサーがさまざまな周波数でデータを出力するように構成されていて、その結果、センサーの値が欠損する可能性があります。 たとえば、電圧と圧力という 2 つのセンサーがあり、それぞれ 100 Hz と 10 Hz の周波数で構成されている場合、電圧センサーは 100 分の 1 秒ごとにデータを出力しますが、圧力センサーは 10 分の 1 秒ごとにのみデータを出力します。

次の表で、1 秒間隔で収集されたマシンの利用統計データセットについて説明します。 

```
timestamp               VoltageReading  PressureReading
----------------------- --------------- ----------------
2020-09-07 06:14:41.000 164.990400      97.223600
2020-09-07 06:14:42.000 162.241300      93.992800
2020-09-07 06:14:43.000 163.271200      NULL
2020-09-07 06:14:44.000 161.368100      93.403700
2020-09-07 06:14:45.000 NULL            NULL
2020-09-07 06:14:46.000 NULL            98.364800
2020-09-07 06:14:49.000 NULL            94.098300
2020-09-07 06:14:51.000 157.695700      103.359100
2020-09-07 06:14:52.000 157.019200      NULL
2020-09-07 06:14:54.000 NULL            95.352000
2020-09-07 06:14:56.000 159.183500      100.748200

```

上記のデータセットには、2 つの重要な特徴があります。 

- このデータセットには、いくつかのタイムスタンプ (`2020-09-07 06:14:47.000`、`2020-09-07 06:14:48.000`、`2020-09-07 06:14:50.000`、`2020-09-07 06:14:53.000`、および `2020-09-07 06:14:55.000`) に関連するデータ ポイントが含まれていません。 これらのタイムスタンプがデータセット内の *ギャップ* です。  
- 電圧と圧力の測定値に、`null` として表される欠損値があります。 

## <a name="gap-filling"></a>ギャップを埋める 

ギャップを埋めることは、連続した順序付けられたタイムスタンプのセットを作成して、時系列データの分析を容易にするのに役立つ手法です。 Azure SQL Edge では、時系列データセットのギャップを埋める最も簡単な方法は、目的の時間分布を含む一時テーブルを定義し、データセット テーブルで `LEFT OUTER JOIN` または `RIGHT OUTER JOIN` 操作を実行することです。 

上記の `MachineTelemetry` のデータを例として使用すると、次のクエリを使用して、分析用の連続した順序付けられたタイムスタンプのセットを生成できます。 

> [!NOTE]
> 次のクエリでは、不足している行が、属性のタイムスタンプ値と `null` 値を含めて生成されます。 

```sql
Create Table #SeriesGenerate(dt datetime Primary key Clustered)
GO

Declare @startdate datetime = '2020-09-07 06:14:41.000', @endtime datetime = '2020-09-07 06:14:56.000'
While (@startdate <= @endtime)
BEGIN
Insert into #SeriesGenerate values (@startdate)
set @startdate = DATEADD(SECOND, 1, @startdate)
END

Select a.dt as timestamp, b.VoltageReading, b.PressureReading 
From 
#SeriesGenerate a LEFT OUTER JOIN MachineTelemetry b 
    on a.dt = b.[timestamp]
```
上記のクエリでは、指定された範囲内のすべての *1 秒* のタイムスタンプを含む次の出力が生成されます。

結果セットは次のようになります

```

timestamp               VoltageReading    PressureReading
----------------------- ----------------- ----------------
2020-09-07 06:14:41.000 164.990400        97.223600
2020-09-07 06:14:42.000 162.241300        93.992800
2020-09-07 06:14:43.000 163.271200        NULL
2020-09-07 06:14:44.000 161.368100        93.403700
2020-09-07 06:14:45.000 NULL              NULL
2020-09-07 06:14:46.000 NULL              98.364800
2020-09-07 06:14:47.000 NULL              NULL
2020-09-07 06:14:48.000 NULL              NULL
2020-09-07 06:14:49.000 NULL              94.098300
2020-09-07 06:14:50.000 NULL              NULL
2020-09-07 06:14:51.000 157.695700        103.359100
2020-09-07 06:14:52.000 157.019200        NULL
2020-09-07 06:14:53.000 NULL              NULL
2020-09-07 06:14:54.000 NULL              95.352000
2020-09-07 06:14:55.000 NULL              NULL
2020-09-07 06:14:56.000 159.183500        100.748200
```

## <a name="imputing-missing-values"></a>欠損値を補完する

上記のクエリでは、データ分析用の不足しているタイムスタンプが生成されましたが、`voltage` と `pressure` の測定値については、欠損値 (null として表されます) のいずれにも置き換えられませんでした。 Azure SQL Edge では、T-SQL の `LAST_VALUE()` および `FIRST_VALUE()` 関数に新しい構文が追加されました。これらは、データセット内の前または次の値に基づいて欠損値を補完するためのメカニズムを提供します。 

新しい構文では、`LAST_VALUE()` および `FIRST_VALUE()` 関数に `IGNORE NULLS` および `RESPECT NULLS` 句が追加されます。 `MachineTelemetry` データセットに対する次のクエリでは、last_value 関数を使用して欠損値を計算します。ここで、欠損値は、データセット内の最後の観測値に置き換えられます。

```sql
Select 
    timestamp,
    VoltageReading As OriginalVoltageValues,
    LAST_VALUE(VoltageReading) IGNORE NULLS OVER (ORDER BY timestamp) As ImputedUsingLastValue, 
    PressureReading As OriginalPressureValues,
    LAST_VALUE(PressureReading) IGNORE NULLS OVER (ORDER BY timestamp) As ImputedUsingLastValue
From 
MachineTelemetry 
order by timestamp 
```
結果セットは次のようになります

```

timestamp               OrigVoltageVals  ImputedVoltage OrigPressureVals  ImputedPressure
----------------------- ---------------- -------------- ----------------- ----------------
2020-09-07 06:14:41.000 164.990400       164.990400     97.223600         97.223600
2020-09-07 06:14:42.000 162.241300       162.241300     93.992800         93.992800
2020-09-07 06:14:43.000 163.271200       163.271200     NULL              93.992800
2020-09-07 06:14:44.000 161.368100       161.368100     93.403700         93.403700
2020-09-07 06:14:45.000 NULL             161.368100     NULL              93.403700
2020-09-07 06:14:46.000 NULL             161.368100     98.364800         98.364800
2020-09-07 06:14:49.000 NULL             161.368100     94.098300         94.098300
2020-09-07 06:14:51.000 157.695700       157.695700     103.359100        103.359100
2020-09-07 06:14:52.000 157.019200       157.019200     NULL              103.359100
2020-09-07 06:14:54.000 NULL             157.019200     95.352000         95.352000
2020-09-07 06:14:56.000 159.183500       159.183500     100.748200        100.748200

```

次のクエリでは、`LAST_VALUE()` および `FIRST_VALUE` 関数の両方を使用して欠損値を補完します。 出力列 `ImputedVoltage` では、欠損値は最後の観測値に置き換えられますが、出力列 `ImputedPressure` では、欠損値はデータセット内の次の観測値に置き換えられます。 

```sql
Select 
    dt as timestamp, 
    VoltageReading As OrigVoltageVals,
    LAST_VALUE(VoltageReading) IGNORE NULLS OVER (ORDER BY dt) As ImputedVoltage, 
    PressureReading As OrigPressureVals,
    First_VALUE(PressureReading) IGNORE NULLS OVER (ORDER BY dt ROWS 
                    BETWEEN CURRENT ROW AND UNBOUNDED FOLLOWING) As ImputedPressure
From 
(Select a.dt, b.VoltageReading,b.PressureReading  from 
    #SeriesGenerate a 
        LEFT OUTER JOIN 
    MachineTelemetry b 
        on a.dt = b.[timestamp]) A
order by timestamp
```
結果セットは次のようになります

```

timestamp               OrigVoltageVals  ImputedVoltage  OrigPressureVals  ImputedPressure
----------------------- ---------------- --------------- ----------------- ---------------
2020-09-07 06:14:41.000 164.990400       164.990400      97.223600         97.223600
2020-09-07 06:14:42.000 162.241300       162.241300      93.992800         93.992800
2020-09-07 06:14:43.000 163.271200       163.271200      NULL              93.403700
2020-09-07 06:14:44.000 161.368100       161.368100      93.403700         93.403700
2020-09-07 06:14:45.000 NULL             161.368100      NULL              98.364800
2020-09-07 06:14:46.000 NULL             161.368100      98.364800         98.364800
2020-09-07 06:14:47.000 NULL             161.368100      NULL              94.098300
2020-09-07 06:14:48.000 NULL             161.368100      NULL              94.098300
2020-09-07 06:14:49.000 NULL             161.368100      94.098300         94.098300
2020-09-07 06:14:50.000 NULL             161.368100      NULL              103.359100
2020-09-07 06:14:51.000 157.695700       157.695700      103.359100        103.359100
2020-09-07 06:14:52.000 157.019200       157.019200      NULL              95.352000
2020-09-07 06:14:53.000 NULL             157.019200      NULL              95.352000
2020-09-07 06:14:54.000 NULL             157.019200      95.352000         95.352000
2020-09-07 06:14:55.000 NULL             157.019200      NULL              100.748200
2020-09-07 06:14:56.000 159.183500       159.183500      100.748200        100.748200
```

> [!NOTE]
> 上記のクエリでは、`FIRST_VALUE()` 関数を使用して、欠損値を次の観測値に置き換えます。 `LAST_VALUE()` 関数を `ORDER BY <ordering_column> DESC` 句と共に使用すると、同じ結果を得ることができます。

## <a name="next-steps"></a>次のステップ 

- [FIRST_VALUE (Transact-SQL)](/sql/t-sql/functions/first-value-transact-sql?toc=%2fazure%2fazure-sql-edge%2ftoc.json)
- [LAST_VALUE (Transact-SQL)](/sql/t-sql/functions/last-value-transact-sql?toc=%2fazure%2fazure-sql-edge%2ftoc.json)
- [DATE_BUCKET (Transact-SQL)](date-bucket-tsql.md)
- [集計関数 (Transact-SQL)](/sql/t-sql/functions/aggregate-functions-transact-sql)