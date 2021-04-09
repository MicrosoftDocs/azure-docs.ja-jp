---
title: Azure Stream Analytics での一般的なクエリ パターン
description: この記事では、Azure Stream Analytics ジョブで役に立つさまざまな一般的なクエリ パターンとデザインについて説明します。
services: stream-analytics
author: rodrigoaatmicrosoft
ms.author: rodrigoa
ms.service: stream-analytics
ms.topic: how-to
ms.date: 12/18/2019
ms.custom: devx-track-js
ms.openlocfilehash: 8fcda77858b3feb78a04971a7ad1f20c0ea51fa1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98019994"
---
# <a name="common-query-patterns-in-azure-stream-analytics"></a>Azure Stream Analytics での一般的なクエリ パターン

Azure Stream Analytics のクエリは SQL に類似したクエリ言語で表現されます。 言語の構造については、「[Stream Analytics query language reference](/stream-analytics-query/stream-analytics-query-language-reference)」(Stream Analytics クエリ言語リファレンス) ガイドで確認できます。 

クエリのデザインでは、イベント データを 1 つの入力ストリームから出力データ ストアに移動する、単純なパススルー ロジックを表すことができます。また、「[Stream Analytics を使って IoT ソリューションを構築する](stream-analytics-build-an-iot-solution-using-stream-analytics.md)」のガイドで説明しているように、豊富なパターン マッチングとテンポラル解析を行って、さまざまな時間枠にわたって集計を計算することもできます。 複数の入力からのデータを結合してストリーミング イベントを結合し、静的な参照データに対する参照を行ってイベントの値を多様化することができます。 複数の出力にデータを書き込むこともできます。

この記事では、実際のシナリオに基づいて、いくつかの一般的なクエリ パターンの対処方法について説明します。

## <a name="supported-data-formats"></a>サポートされるデータ形式

Azure Stream Analytics では、CSV、JSON、および Avro データ形式のイベントの処理をサポートします。

JSON と Avro のどちらも、入れ子になったオブジェクト (レコード) や配列などの複合型を含むことができます。 これらの複雑なデータ型の操作については、[JSON および AVRO データの解析](stream-analytics-parsing-json.md)に関する記事をご覧ください。

## <a name="send-data-to-multiple-outputs"></a>複数の出力にデータを送信する

複数の **SELECT** ステートメントを使用して、異なる出力シンクにデータを出力できます。 たとえば、一方の **SELECT** でしきい値に基づくアラートを出力し、もう一方の SELECT で Blob Storage にイベントを出力することができます。

**入力**:

| Make | Time |
| --- | --- |
| Make1 |2015-01-01T00:00:01.0000000Z |
| Make1 |2015-01-01T00:00:02.0000000Z |
| Make2 |2015-01-01T00:00:01.0000000Z |
| Make2 |2015-01-01T00:00:02.0000000Z |
| Make2 |2015-01-01T00:00:03.0000000Z |

**出力 ArchiveOutput**:

| Make | Time |
| --- | --- |
| Make1 |2015-01-01T00:00:01.0000000Z |
| Make1 |2015-01-01T00:00:02.0000000Z |
| Make2 |2015-01-01T00:00:01.0000000Z |
| Make2 |2015-01-01T00:00:02.0000000Z |
| Make2 |2015-01-01T00:00:03.0000000Z |

**出力 AlertOutput**:

| Make | Time | Count |
| --- | --- | --- |
| Make2 |2015-01-01T00:00:10.0000000Z |3 |

**Query**:

```SQL
SELECT
    *
INTO
    ArchiveOutput
FROM
    Input TIMESTAMP BY Time

SELECT
    Make,
    System.TimeStamp() AS Time,
    COUNT(*) AS [Count]
INTO
    AlertOutput
FROM
    Input TIMESTAMP BY Time
GROUP BY
    Make,
    TumblingWindow(second, 10)
HAVING
    [Count] >= 3
```

**INTO** 句を使用して、Stream Analytics に対して、データを書き込む出力先を指定します。 最初の **SELECT** では、入力からデータを受信して **ArchiveOutput** という名前の出力に送信するパススルー クエリを定義します。 2 番目のクエリでは、簡単な集計とフィルター処理を行い、その結果を下流のアラート システム出力である **AlertOutput** に送信します。

**WITH** 句を使用して、複数のサブクエリ ブロックを定義できることに注意してください。 このオプションには、入力ソースに対して開くリーダーが少なくて済むという利点があります。

**Query**:

```SQL
WITH ReaderQuery AS (
    SELECT
        *
    FROM
        Input TIMESTAMP BY Time
)

SELECT * INTO ArchiveOutput FROM ReaderQuery

SELECT 
    Make,
    System.TimeStamp() AS Time,
    COUNT(*) AS [Count] 
INTO AlertOutput 
FROM ReaderQuery
GROUP BY
    Make,
    TumblingWindow(second, 10)
HAVING [Count] >= 3
```

詳細については、[**WITH** 句](/stream-analytics-query/with-azure-stream-analytics)に関するページを参照してください。

## <a name="simple-pass-through-query"></a>単純なパススルー クエリ

単純なパススルー クエリを使用すると、入力ストリーム データを出力にコピーできます。 たとえば、リアルタイムの車両情報を含むデータ ストリームを SQL データベースに保存して、レター分析を行う必要がある場合、単純なパススルー クエリを使用してジョブを実行します。

**入力**:

| Make | Time | Weight |
| --- | --- | --- |
| Make1 |2015-01-01T00:00:01.0000000Z |"1000" |
| Make1 |2015-01-01T00:00:02.0000000Z |"2000" |

**出力**:

| Make | Time | Weight |
| --- | --- | --- |
| Make1 |2015-01-01T00:00:01.0000000Z |"1000" |
| Make1 |2015-01-01T00:00:02.0000000Z |"2000" |

**Query**:

```SQL
SELECT
    *
INTO Output
FROM Input
```

**SELECT** * クエリを実行すると、受信イベントのすべてのフィールドが射影され、出力に送信されます。 同様に、**SELECT** を使用して、入力から必須フィールドのみを射影することもできます。 以下の例では、車両の *Make* と *Time* のみが保存される必須フィールドである場合、これらのフィールドを **SELECT** ステートメントで指定できます。

**入力**:

| Make | Time | Weight |
| --- | --- | --- |
| Make1 |2015-01-01T00:00:01.0000000Z |1000 |
| Make1 |2015-01-01T00:00:02.0000000Z |2000 |
| Make2 |2015-01-01T00:00:04.0000000Z |1500 |

**出力**:

| Make | Time |
| --- | --- |
| Make1 |2015-01-01T00:00:01.0000000Z |
| Make1 |2015-01-01T00:00:02.0000000Z |
| Make2 |2015-01-01T00:00:04.0000000Z |

**Query**:

```SQL
SELECT
    Make, Time
INTO Output
FROM Input
```

## <a name="string-matching-with-like-and-not-like"></a>LIKE と NOT LIKE を使用した文字列の照合

**LIKE** や **NOT LIKE** を使用して、フィールドが特定のパターンに一致するかどうかを検証することができます。 たとえば、文字 "A" で始まり、番号 9 で終わるナンバー プレートだけを返すフィルターを作成できます。

**入力**:

| Make | License_plate | Time |
| --- | --- | --- |
| Make1 |ABC-123 |2015-01-01T00:00:01.0000000Z |
| Make2 |AAA-999 |2015-01-01T00:00:02.0000000Z |
| Make3 |ABC-369 |2015-01-01T00:00:03.0000000Z |

**出力**:

| Make | License_plate | Time |
| --- | --- | --- |
| Make2 |AAA-999 |2015-01-01T00:00:02.0000000Z |
| Make3 |ABC-369 |2015-01-01T00:00:03.0000000Z |

**Query**:

```SQL
SELECT
    *
FROM
    Input TIMESTAMP BY Time
WHERE
    License_plate LIKE 'A%9'
```

**LIKE** ステートメントを使用して **License_plate** フィールドの値を検査します。 値は、文字 "A" で始まり、0 文字以上の文字列が続き、数字 9 で終わる必要があります。

## <a name="calculation-over-past-events"></a>過去のイベントに対する計算

**LAG** 関数を使用すると、時間枠内の過去のイベントを調べて、現在のイベントと比較することができます。 たとえば、現在の自動車メーカーが料金所を通過した最後の自動車と異なる場合に、現在の自動車メーカーを出力できます。

**入力**:

| Make | Time |
| --- | --- |
| Make1 |2015-01-01T00:00:01.0000000Z |
| Make2 |2015-01-01T00:00:02.0000000Z |

**出力**:

| Make | Time |
| --- | --- |
| Make2 |2015-01-01T00:00:02.0000000Z |

**Query**:

```SQL
SELECT
    Make,
    Time
FROM
    Input TIMESTAMP BY Time
WHERE
    LAG(Make, 1) OVER (LIMIT DURATION(minute, 1)) <> Make
```

**LAG** を使用して入力ストリームで 1 つ前のイベントを調べて、*Make* の値を取得し、その値を現在のイベントの *Make* と比較し、イベントを出力します。

詳細については、[**LAG**](/stream-analytics-query/lag-azure-stream-analytics) に関するページを参照してください。

## <a name="return-the-last-event-in-a-window"></a>期間内の最後のイベントを返す

イベントはシステムによってリアルタイムで使用されるため、イベントがその時間枠で到着する最後のイベントかどうかを判断できる関数はありません。 これを実現するには、入力ストリームを別の入力ストリームと結合する必要があります。この場合、イベントの時間はその時間枠におけるすべてのイベントの最大時間になります。

**入力**:

| License_plate | Make | Time |
| --- | --- | --- |
| DXE 5291 |Make1 |2015-07-27T00:00:05.0000000Z |
| YZK 5704 |Make3 |2015-07-27T00:02:17.0000000Z |
| RMV 8282 |Make1 |2015-07-27T00:05:01.0000000Z |
| YHN 6970 |Make2 |2015-07-27T00:06:00.0000000Z |
| VFE 1616 |Make2 |2015-07-27T00:09:31.0000000Z |
| QYF 9358 |Make1 |2015-07-27T00:12:02.0000000Z |
| MDR 6128 |Make4 |2015-07-27T00:13:45.0000000Z |

**出力**:

| License_plate | Make | Time |
| --- | --- | --- |
| VFE 1616 |Make2 |2015-07-27T00:09:31.0000000Z |
| MDR 6128 |Make4 |2015-07-27T00:13:45.0000000Z |

**Query**:

```SQL
WITH LastInWindow AS
(
    SELECT 
        MAX(Time) AS LastEventTime
    FROM 
        Input TIMESTAMP BY Time
    GROUP BY 
        TumblingWindow(minute, 10)
)

SELECT 
    Input.License_plate,
    Input.Make,
    Input.Time
FROM
    Input TIMESTAMP BY Time 
    INNER JOIN LastInWindow
    ON DATEDIFF(minute, Input, LastInWindow) BETWEEN 0 AND 10
    AND Input.Time = LastInWindow.LastEventTime
```

クエリの最初の手順では、10 分の時間枠で最大のタイム スタンプを検索します。これは、その時間枠で最後のイベントのタイム スタンプです。 2 番目の手順では、最初のクエリの結果と元のストリームを結合し、各期間で最後のタイム スタンプに一致するイベントを検索します。 

**DATEDIFF** は、2 つの DateTime フィールド間の時差を比較して返す、日付固有の関数です。詳細については、[日付関数](/stream-analytics-query/date-and-time-functions-azure-stream-analytics)に関するページを参照してください。

ストリームの結合の詳細については、[**JOIN**](/stream-analytics-query/join-azure-stream-analytics) に関するページを参照してください。

## <a name="data-aggregation-over-time"></a>時間ごとのデータ集計

時間枠で情報を計算するために、データをまとめて集計できます。 この例では、特定の自動車メーカーごとに直近 10 秒間のカウントが計算されます。

**入力**:

| Make | Time | Weight |
| --- | --- | --- |
| Make1 |2015-01-01T00:00:01.0000000Z |1000 |
| Make1 |2015-01-01T00:00:02.0000000Z |2000 |
| Make2 |2015-01-01T00:00:04.0000000Z |1500 |

**出力**:

| Make | Count |
| --- | --- |
| Make1 | 2 |
| Make2 | 1 |

**Query**:

```SQL
SELECT
    Make,
    COUNT(*) AS Count
FROM
    Input TIMESTAMP BY Time
GROUP BY
    Make,
    TumblingWindow(second, 10)
```

この集計では、自動車を *Make* でグループ化して、10 秒ごとにカウントします。 出力には、料金所を通過した自動車の *Make* と *Count* が含まれます。

TumblingWindow は、イベントをグループ化するために使用されるウィンドウ関数です。 集計は、グループ化されたすべてのイベントに適用できます。 詳細については、[ウィンドウ関数](stream-analytics-window-functions.md)に関するページを参照してください。

集計の詳細については、[集計関数](/stream-analytics-query/aggregate-functions-azure-stream-analytics)に関するページを参照してください。

## <a name="periodically-output-values"></a>定期的な値の出力

不規則または見つからないイベントが発生した場合は、よりまばらなデータ入力から定期的な間隔の出力を生成できます。 たとえば、最近検出されたデータ ポイントを報告する 5 秒ごとのイベントを生成します。

**入力**:

| Time | 値 |
| --- | --- |
| "2014-01-01T06:01:00" |1 |
| "2014-01-01T06:01:05" |2 |
| "2014-01-01T06:01:10" |3 |
| "2014-01-01T06:01:15" |4 |
| "2014-01-01T06:01:30" |5 |
| "2014-01-01T06:01:35" |6 |

**出力 (最初の 10 行)** :

| Window_end | Last_event.Time | Last_event.Value |
| --- | --- | --- |
| 2014-01-01T14:01:00.000Z |2014-01-01T14:01:00.000Z |1 |
| 2014-01-01T14:01:05.000Z |2014-01-01T14:01:05.000Z |2 |
| 2014-01-01T14:01:10.000Z |2014-01-01T14:01:10.000Z |3 |
| 2014-01-01T14:01:15.000Z |2014-01-01T14:01:15.000Z |4 |
| 2014-01-01T14:01:20.000Z |2014-01-01T14:01:15.000Z |4 |
| 2014-01-01T14:01:25.000Z |2014-01-01T14:01:15.000Z |4 |
| 2014-01-01T14:01:30.000Z |2014-01-01T14:01:30.000Z |5 |
| 2014-01-01T14:01:35.000Z |2014-01-01T14:01:35.000Z |6 |
| 2014-01-01T14:01:40.000Z |2014-01-01T14:01:35.000Z |6 |
| 2014-01-01T14:01:45.000Z |2014-01-01T14:01:35.000Z |6 |

**Query**:

```SQL
SELECT
    System.Timestamp() AS Window_end,
    TopOne() OVER (ORDER BY Time DESC) AS Last_event
FROM
    Input TIMESTAMP BY Time
GROUP BY
    HOPPINGWINDOW(second, 300, 5)
```

このクエリは、5 秒ごとにイベントを生成し、それまでに受信した最後のイベントを出力します。 **HOPPINGWINDOW** 期間で、クエリが最新のイベントを検出するためにさかのぼる期間を指定します。

詳細については、[ホッピング ウィンドウ](/stream-analytics-query/hopping-window-azure-stream-analytics)に関するページを参照してください。

## <a name="correlate-events-in-a-stream"></a>ストリームでのイベントの関連付け

**LAG** 関数を使用して、過去のイベントを調べることにより、同じストリーム内のイベントを関連付けることができます。 たとえば、直近 90 秒間に同じ *Make* の 2 台の自動車が連続して料金所を通過した場合に出力を生成できます。

**入力**:

| Make | License_plate | Time |
| --- | --- | --- |
| Make1 |ABC-123 |2015-01-01T00:00:01.0000000Z |
| Make1 |AAA-999 |2015-01-01T00:00:02.0000000Z |
| Make2 |DEF-987 |2015-01-01T00:00:03.0000000Z |
| Make1 |GHI-345 |2015-01-01T00:00:04.0000000Z |

**出力**:

| Make | Time | Current_car_license_plate | First_car_license_plate | First_car_time |
| --- | --- | --- | --- | --- |
| Make1 |2015-01-01T00:00:02.0000000Z |AAA-999 |ABC-123 |2015-01-01T00:00:01.0000000Z |

**Query**:

```SQL
SELECT
    Make,
    Time,
    License_plate AS Current_car_license_plate,
    LAG(License_plate, 1) OVER (LIMIT DURATION(second, 90)) AS First_car_license_plate,
    LAG(Time, 1) OVER (LIMIT DURATION(second, 90)) AS First_car_time
FROM
    Input TIMESTAMP BY Time
WHERE
    LAG(Make, 1) OVER (LIMIT DURATION(second, 90)) = Make
```

**LAG** 関数を使用して、入力ストリームで 1 つ前のイベントを調べて、*Make* の値を取得し、現在のイベントの *Make* の値と比較することができます。  条件が満たされると、**SELECT** ステートメントで **LAG** を使用して、前のイベントのデータを射影できます。

詳細については、[LAG](/stream-analytics-query/lag-azure-stream-analytics) に関するページを参照してください。

## <a name="detect-the-duration-between-events"></a>イベントの間隔を検出する

イベントの間隔は、終了イベントを受信したときに最後の開始イベントを調べることによって計算できます。 このクエリは、ユーザーがページまたは機能に費やした時間を判断するのに役立ちます。

**入力**:  

| User | 機能 | Event | Time |
| --- | --- | --- | --- |
| user@location.com |RightMenu |[開始] |2015-01-01T00:00:01.0000000Z |
| user@location.com |RightMenu |End |2015-01-01T00:00:08.0000000Z |

**出力**:  

| User | 機能 | Duration |
| --- | --- | --- |
| user@location.com |RightMenu |7 |

**Query**:

```SQL
SELECT
    [user],
    feature,
    DATEDIFF(
        second,
        LAST(Time) OVER (PARTITION BY [user], feature LIMIT DURATION(hour, 1) WHEN Event = 'start'),
        Time) as duration
FROM input TIMESTAMP BY Time
WHERE
    Event = 'end'
```

**LAST** 関数を使用して、特定の条件内で最後のイベントを取得できます。 この例では、条件は Start 型のイベントで、ユーザーと機能に対して **PARTITION BY** を実行することで検索をパーティション分割します。 これにより、すべてのユーザーと機能が、開始イベントを検索するときに個別に処理されます。 **LIMIT DURATION** を使用して、終了イベントと開始イベントの間の過去 1 時間に検索を制限します。

## <a name="count-unique-values"></a>一意の値をカウントする

**COUNT** と **DISTINCT** を使用すると、ストリームに出現するフィールドの、値ごとの件数を一定間隔でカウントできます。 料金所を通過した自動車の、*Make* ごとの台数を 2 秒間隔で計算するクエリを作成できます。

**入力**:

| Make | Time |
| --- | --- |
| Make1 |2015-01-01T00:00:01.0000000Z |
| Make1 |2015-01-01T00:00:02.0000000Z |
| Make2 |2015-01-01T00:00:01.0000000Z |
| Make2 |2015-01-01T00:00:02.0000000Z |
| Make2 |2015-01-01T00:00:03.0000000Z |

**出力:**

| Count_make | Time |
| --- | --- |
| 2 |2015-01-01T00:00:02.000Z |
| 1 |2015-01-01T00:00:04.000Z |

**クエリ:**

```SQL
SELECT
     COUNT(DISTINCT Make) AS Count_make,
     System.TIMESTAMP() AS Time
FROM Input TIMESTAMP BY TIME
GROUP BY 
     TumblingWindow(second, 2)
```

**COUNT(DISTINCT Make)** は、ある時間枠内で、**Make** 列の個別の値の数を返します。
詳細については、[**COUNT** 集計関数](/stream-analytics-query/count-azure-stream-analytics)に関するページを参照してください。

## <a name="retrieve-the-first-event-in-a-window"></a>期間内の最初のイベントを取得する

**IsFirst** は、時間枠内の最初のイベントを取得するために使用できます。 たとえば、10 分間隔で最初の自動車情報を出力します。

**入力**:

| License_plate | Make | Time |
| --- | --- | --- |
| DXE 5291 |Make1 |2015-07-27T00:00:05.0000000Z |
| YZK 5704 |Make3 |2015-07-27T00:02:17.0000000Z |
| RMV 8282 |Make1 |2015-07-27T00:05:01.0000000Z |
| YHN 6970 |Make2 |2015-07-27T00:06:00.0000000Z |
| VFE 1616 |Make2 |2015-07-27T00:09:31.0000000Z |
| QYF 9358 |Make1 |2015-07-27T00:12:02.0000000Z |
| MDR 6128 |Make4 |2015-07-27T00:13:45.0000000Z |

**出力**:

| License_plate | Make | Time |
| --- | --- | --- |
| DXE 5291 |Make1 |2015-07-27T00:00:05.0000000Z |
| QYF 9358 |Make1 |2015-07-27T00:12:02.0000000Z |

**Query**:

```SQL
SELECT 
    License_plate,
    Make,
    Time
FROM 
    Input TIMESTAMP BY Time
WHERE 
    IsFirst(minute, 10) = 1
```

**IsFirst** を使用して、データをパーティション分割し、10 分間隔で検出された自動車の *Make* ごとの最初のイベントを計算することもできます。

**出力**:

| License_plate | Make | Time |
| --- | --- | --- |
| DXE 5291 |Make1 |2015-07-27T00:00:05.0000000Z |
| YZK 5704 |Make3 |2015-07-27T00:02:17.0000000Z |
| YHN 6970 |Make2 |2015-07-27T00:06:00.0000000Z |
| QYF 9358 |Make1 |2015-07-27T00:12:02.0000000Z |
| MDR 6128 |Make4 |2015-07-27T00:13:45.0000000Z |

**Query**:

```SQL
SELECT 
    License_plate,
    Make,
    Time
FROM 
    Input TIMESTAMP BY Time
WHERE 
    IsFirst(minute, 10) OVER (PARTITION BY Make) = 1
```

詳細については、[**IsFirst**](/stream-analytics-query/isfirst-azure-stream-analytics) に関するページを参照してください。

## <a name="remove-duplicate-events-in-a-window"></a>期間内の重複するイベントを削除する

特定の期間内のイベントに対する平均の計算などの操作を実行するときは、重複するイベントを除外する必要があります。 次の例では、2 番目のイベントは最初のイベントの重複です。

**入力**:  

| deviceId | Time | 属性 | 値 |
| --- | --- | --- | --- |
| 1 |2018-07-27T00:00:01.0000000Z |気温 |50 |
| 1 |2018-07-27T00:00:01.0000000Z |気温 |50 |
| 2 |2018-07-27T00:00:01.0000000Z |気温 |40 |
| 1 |2018-07-27T00:00:05.0000000Z |気温 |60 |
| 2 |2018-07-27T00:00:05.0000000Z |気温 |50 |
| 1 |2018-07-27T00:00:10.0000000Z |気温 |100 |

**出力**:  

| AverageValue | deviceId |
| --- | --- |
| 70 | 1 |
|45 | 2 |

**Query**:

```SQL
With Temp AS (
SELECT
    COUNT(DISTINCT Time) AS CountTime,
    Value,
    DeviceId
FROM
    Input TIMESTAMP BY Time
GROUP BY
    Value,
    DeviceId,
    SYSTEM.TIMESTAMP()
)

SELECT
    AVG(Value) AS AverageValue, DeviceId
INTO Output
FROM Temp
GROUP BY DeviceId,TumblingWindow(minute, 5)
```

**COUNT(DISTINCT Time)** は、特定の時間枠内での、Time 列の個別の値の数を返します。 その後、最初のステップの出力を使用し、重複を除去することによって、デバイスあたりの平均を計算できます。

詳細については、[COUNT(DISTINCT Time)](/stream-analytics-query/count-azure-stream-analytics) に関するページを参照してください。

## <a name="specify-logic-for-different-casesvalues-case-statements"></a>異なるケース/値に異なるロジックを指定する (CASE ステートメント)

**CASE** ステートメントでは、特定の条件に基づいてさまざまなフィールドに対して異なる計算を行うことができます。 たとえば、レーン "A" を *Make1* の自動車、レーン "B" をその他のメーカーに割り当てることができます。

**入力**:

| Make | Time |
| --- | --- |
| Make1 |2015-01-01T00:00:01.0000000Z |
| Make2 |2015-01-01T00:00:02.0000000Z |
| Make2 |2015-01-01T00:00:03.0000000Z |

**出力**:

| Make |Dispatch_to_lane | Time |
| --- | --- | --- |
| Make1 |"A" |2015-01-01T00:00:01.0000000Z |
| Make2 |"B" |2015-01-01T00:00:02.0000000Z |

**解決策**:

```SQL
SELECT
    Make
    CASE
        WHEN Make = "Make1" THEN "A"
        ELSE "B"
    END AS Dispatch_to_lane,
    System.TimeStamp() AS Time
FROM
    Input TIMESTAMP BY Time
```

**CASE** 式を使用して、式を一連の単純な式と比較して結果を決定します。 この例では *Make1* の車両はレーン "A" にディスパッチされますが、その他のメーカーの車両にはレーン "B" が割り当てられます。

詳細については、[case 式](/stream-analytics-query/case-azure-stream-analytics)に関するページを参照してください。

## <a name="data-conversion"></a>データ変換

**CAST** メソッドを使用して、データをリアルタイムでキャストできます。 たとえば、自動車の重量を型 **nvarchar (max)** から型 **bigint** に変換し、数値計算で使用することができます。

**入力**:

| Make | Time | Weight |
| --- | --- | --- |
| Make1 |2015-01-01T00:00:01.0000000Z |"1000" |
| Make1 |2015-01-01T00:00:02.0000000Z |"2000" |

**出力**:

| Make | Weight |
| --- | --- |
| Make1 |3000 |

**Query**:

```SQL
SELECT
    Make,
    SUM(CAST(Weight AS BIGINT)) AS Weight
FROM
    Input TIMESTAMP BY Time
GROUP BY
    Make,
    TumblingWindow(second, 10)
```

**CAST** ステートメントを使用してデータ型を指定します。 サポートされるデータ型の一覧については、「[データ型 (Azure Stream Analytics)](/stream-analytics-query/data-types-azure-stream-analytics)」をご覧ください。

詳細については、[データ変換関数](/stream-analytics-query/conversion-functions-azure-stream-analytics)に関するページを参照してください。

## <a name="detect-the-duration-of-a-condition"></a>条件の期間を検出する

複数のイベントが対象となる条件の場合は、**LAG** 関数を使用して、その条件の期間を特定できます。 たとえば、バグのためにすべての自動車の重量が正しくない (20,000 ポンドを超過) 結果になった場合、バグが継続した期間を計算する必要があります。

**入力**:

| Make | Time | Weight |
| --- | --- | --- |
| Make1 |2015-01-01T00:00:01.0000000Z |2000 |
| Make2 |2015-01-01T00:00:02.0000000Z |25000 |
| Make1 |2015-01-01T00:00:03.0000000Z |26000 |
| Make2 |2015-01-01T00:00:04.0000000Z |25000 |
| Make1 |2015-01-01T00:00:05.0000000Z |26000 |
| Make2 |2015-01-01T00:00:06.0000000Z |25000 |
| Make1 |2015-01-01T00:00:07.0000000Z |26000 |
| Make2 |2015-01-01T00:00:08.0000000Z |2000 |

**出力**:

| Start_fault | End_fault |
| --- | --- |
| 2015-01-01T00:00:02.000Z |2015-01-01T00:00:07.000Z |

**Query**:

```SQL
WITH SelectPreviousEvent AS
(
SELECT
    *,
    LAG([time]) OVER (LIMIT DURATION(hour, 24)) as previous_time,
    LAG([weight]) OVER (LIMIT DURATION(hour, 24)) as previous_weight
FROM input TIMESTAMP BY [time]
)

SELECT 
    LAG(time) OVER (LIMIT DURATION(hour, 24) WHEN previous_weight < 20000 ) [Start_fault],
    previous_time [End_fault]
FROM SelectPreviousEvent
WHERE
    [weight] < 20000
    AND previous_weight > 20000
```
最初の **SELECT** ステートメントでは、現在の重量測定値を前の測定値と関連付け、現在の測定値と共に射影します。 2 番目の **SELECT** では、*previous_weight* が 20000 未満であり、現在の重量が 20000 未満で、現在のイベントの *previous_weight* が 20000 を超えていた最後のイベントを探します。

End_fault は、前のイベントでは問題が発生し、現在のイベントでは問題が発生していないイベントです。Start_fault は、それより前で問題が発生していない最後のイベントです。

## <a name="process-events-with-independent-time-substreams"></a>個別の時間でイベントを処理する (サブストリーム)

イベント プロデューサー間またはパーティション間のクロックのずれや、ネットワーク待機時間が原因でイベントが遅れて、あるいは順序がずれて到着することがあります。
たとえば、*TollID* 2 のデバイス クロックは *TollID* 1 より 5 秒遅れており、*TollID* 3 のデバイス クロックは *TollID* 1 より 10 秒遅れています。 計算は料金所ごとに個別に実行され、各料金所のクロック データだけがタイムスタンプとして考慮されます。

**入力**:

| LicensePlate | Make | Time | TollID |
| --- | --- | --- | --- |
| DXE 5291 |Make1 |2015-07-27T00:00:01.0000000Z | 1 |
| YHN 6970 |Make2 |2015-07-27T00:00:05.0000000Z | 1 |
| QYF 9358 |Make1 |2015-07-27T00:00:01.0000000Z | 2 |
| GXF 9462 |Make3 |2015-07-27T00:00:04.0000000Z | 2 |
| VFE 1616 |Make2 |2015-07-27T00:00:10.0000000Z | 1 |
| RMV 8282 |Make1 |2015-07-27T00:00:03.0000000Z | 3 |
| MDR 6128 |Make3 |2015-07-27T00:00:11.0000000Z | 2 |
| YZK 5704 |Make4 |2015-07-27T00:00:07.0000000Z | 3 |

**出力**:

| TollID | Count |
| --- | --- |
| 1 | 2 |
| 2 | 2 |
| 1 | 1 |
| 3 | 1 |
| 2 | 1 |
| 3 | 1 |

**Query**:

```SQL
SELECT
      TollId,
      COUNT(*) AS Count
FROM input
      TIMESTAMP BY Time OVER TollId
GROUP BY TUMBLINGWINDOW(second, 5), TollId
```

**TIMESTAMP OVER BY** 句では、サブストリームを使用して各デバイスのタイムラインで個別に検索します。 各 *TollID* の出力イベントは、計算されるときに生成されます。つまり、すべてのデバイスが同じクロックを参照しているように順序が変更されるのではなく、各 *TollID* を基準にしてイベントが順序付けられます。

詳細については、[TIMESTAMP BY OVER](/stream-analytics-query/timestamp-by-azure-stream-analytics#over-clause-interacts-with-event-ordering) に関するページを参照してください。

## <a name="session-windows"></a>セッション ウィンドウ

セッション ウィンドウとは、イベントが発生している間、拡張し続ける時間枠のことです。セッション ウィンドウは、イベントを受信しないまま一定時間が経過したり、時間枠が最大継続時間に達したりすると計算のために終了します。
この時間枠は、ユーザー操作データを計算する場合に特に役立ちます。 時間枠は、ユーザーがシステムを操作し始めると開始し、イベントがそれ以上観測されなくなると (つまり、ユーザーが操作をやめると) 終了します。
たとえば、ユーザーが Web ページを操作していて、クリック回数がログに記録される場合、セッション ウィンドウを使用して、ユーザーがサイトを操作した時間を調べることができます。

**入力**:

| User_id | Time | URL |
| --- | --- | --- |
| 0 | 2017-01-26T00:00:00.0000000Z | "www.example.com/a.html" |
| 0 | 2017-01-26T00:00:20.0000000Z | "www.example.com/b.html" |
| 1 | 2017-01-26T00:00:55.0000000Z | "www.example.com/c.html" |
| 0 | 2017-01-26T00:01:10.0000000Z | "www.example.com/d.html" |
| 1 | 2017-01-26T00:01:15.0000000Z | "www.example.com/e.html" |

**出力**:

| User_id | StartTime | EndTime | Duration_in_seconds |
| --- | --- | --- | --- |
| 0 | 2017-01-26T00:00:00.0000000Z | 2017-01-26T00:01:10.0000000Z | 70 |
| 1 | 2017-01-26T00:00:55.0000000Z | 2017-01-26T00:01:15.0000000Z | 20 |

**Query**:

``` SQL
SELECT
    user_id,
    MIN(time) as StartTime,
    MAX(time) as EndTime,
    DATEDIFF(second, MIN(time), MAX(time)) AS duration_in_seconds
FROM input TIMESTAMP BY time
GROUP BY
    user_id,
    SessionWindow(minute, 1, 60) OVER (PARTITION BY user_id)
```

**SELECT** を使用して、ユーザー操作に関連するデータを操作時間と共に射影します。 ユーザーと **SessionWindow** でデータをグループ化します。このセッション ウィンドウでは 1 分間操作が発生しないと終了し、最大ウィンドウ サイズは 60 分です。

**SessionWindow** の詳細については、[セッション ウィンドウ](/stream-analytics-query/session-window-azure-stream-analytics)に関するページを参照してください。

## <a name="language-extensibility-with-user-defined-function-in-javascript-and-c"></a>JavaScript と C# でのユーザー定義関数を使用した言語拡張

Azure Stream Analytics のクエリ言語は、JavaScript または C# 言語で記述されたカスタム関数を使用して拡張できます。 ユーザー定義関数 (UDF) とは、**SQL** 言語を使用して簡単に表現することができないカスタムまたは複雑な計算です。 これらの UDF は一度定義すると、クエリ内で複数回使用できます。 たとえば、UDF を使用して、16 進数の *nvarchar(max)* 値を *bigint* 値に変換できます。

**入力**:

| Device_id | HexValue |
| --- | --- |
| 1 | "B4" |
| 2 | "11B" |
| 3 | "121" |

**出力**:

| Device_id | Decimal |
| --- | --- |
| 1 | 180 |
| 2 | 283 |
| 3 | 289 |

```JavaScript
function hex2Int(hexValue){
    return parseInt(hexValue, 16);
}
```

```C#
public static class MyUdfClass {
    public static long Hex2Int(string hexValue){
        return int.Parse(hexValue, System.Globalization.NumberStyles.HexNumber);
    }
}
```

```SQL
SELECT
    Device_id,
    udf.Hex2Int(HexValue) AS Decimal
From
    Input
```

ユーザー定義関数では、使用されるすべてのイベントに対して HexValue の *bigint* 値を計算します。

詳細については、[JavaScript](./stream-analytics-javascript-user-defined-functions.md) と [C#](./stream-analytics-edge-csharp-udf.md) に関するページを参照してください。

## <a name="advanced-pattern-matching-with-match_recognize"></a>MATCH_RECOGNIZE を使用した高度なパターン マッチング

**MATCH_RECOGNIZE** は、イベントのシーケンスを適切に定義された正規表現パターンに一致させるために使用できる高度なパターン マッチング メカニズムです。
たとえば、ATM の障害をリアルタイムで監視しており、ATM の操作時に管理者への通知が必要な警告メッセージが 2 つ連続して発生したとします。

**入力**:

| ATM_id | Operation_id | Return_Code | Time |
| --- | --- | --- | --- |
| 1 | "Entering Pin" | "Success" | 2017-01-26T00:10:00.0000000Z |
| 2 | "Opening Money Slot" | "Success" | 2017-01-26T00:10:07.0000000Z |
| 2 | "Closing Money Slot" | "Success" | 2017-01-26T00:10:11.0000000Z |
| 1 | "Entering Withdraw Quantity" | "Success" | 2017-01-26T00:10:08.0000000Z |
| 1 | "Opening Money Slot" | "Warning" | 2017-01-26T00:10:14.0000000Z |
| 1 | "Printing Bank Balance" | "Warning" | 2017-01-26T00:10:19.0000000Z |

**出力**:

| ATM_id | First_Warning_Operation_id | Warning_Time |
| --- | --- | --- |
| 1 | "Opening Money Slot" | 2017-01-26T00:10:14.0000000Z |

```SQL
SELECT *
FROM input TIMESTAMP BY time OVER ATM_id
MATCH_RECOGNIZE (
    LIMIT DURATION(minute, 1)
    PARTITON BY ATM_id
    MEASURES
        First(Warning.ATM_id) AS ATM_id,
        First(Warning.Operation_Id) AS First_Warning_Operation_id,
        First(Warning.Time) AS Warning_Time
    AFTER MATCH SKIP TO NEXT ROW
    PATTERN (Success* Warning{2,})
    DEFINE
        Success AS Succes.Return_Code = 'Success',
        Failure AS Warning.Return_Code <> 'Success'
) AS patternMatch
```

このクエリは、少なくとも 2 つの連続するエラー イベントに一致し、条件が満たされたときにアラームを生成します。
**PATTERN** では、照合に使用される正規表現を定義します。このケースでは、任意の数の成功した操作の後、少なくとも 2 つの連続した失敗が続きます。
成功と失敗は、Return_Code 値を使用して定義します。条件が満たされると、*ATM_id*、最初の警告操作、最初の警告時刻を使用して **MEASURES** が射影されます。

詳細については、[MATCH_RECOGNIZE](/stream-analytics-query/match-recognize-stream-analytics) に関するページを参照してください。

## <a name="geofencing-and-geospatial-queries"></a>ジオフェンシングおよび地理空間クエリ

Azure Stream Analytics には、フリート管理、ライド シェア、コネクテッド カー、および資産追跡などのシナリオを実装するために使用できる、組み込みの地理空間関数が用意されています。
地理空間データは、イベント ストリームまたは参照データの一部として、GeoJSON または WKT 形式で取り込むことができます。
たとえば、パスポート印刷機械の製造を専門とする会社が、政府機関や領事館に機械をリースします。 その機械を置き間違えてパスポートの偽造に使われることがないようにするため、そのような機械の場所は厳重に管理されます。 各機械には GPS トラッカーが搭載され、その情報は Azure Stream Analytics ジョブにリレーされます。
製造元では、これらの機械の場所を追跡し、そのうちの 1 つが承認済みのエリアを離れたときにアラートを受け取るようにします。こうすれば、製造元は装置をリモートで使用不可能にし、関係機関に注意喚起し、装置を取り返すことができます。

**入力**:

| Equipment_id | Equipment_current_location | Time |
| --- | --- | --- |
| 1 | "POINT(-122.13288797982818 47.64082002051315)" | 2017-01-26T00:10:00.0000000Z |
| 1 | "POINT(-122.13307252987875 47.64081350934929)" | 2017-01-26T00:11:00.0000000Z |
| 1 | "POINT(-122.13308862313283 47.6406508603241)" | 2017-01-26T00:12:00.0000000Z |
| 1 | "POINT(-122.13341048821462 47.64043760861279)" | 2017-01-26T00:13:00.0000000Z |

**参照データ入力**:

| Equipment_id | Equipment_lease_location |
| --- | --- |
| 1 | "POLYGON((-122.13326028450979 47.6409833866794,-122.13261655434621 47.6409833866794,-122.13261655434621 47.64061471602751,-122.13326028450979 47.64061471602751,-122.13326028450979 47.6409833866794))" |

**出力**:

| Equipment_id | Equipment_alert_location | Time |
| --- | --- | --- |
| 1 | "POINT(-122.13341048821462 47.64043760861279)" | 2017-01-26T00:13:00.0000000Z |

```SQL
SELECT
    input.Equipment_id AS Equipment_id,
    input.Equipment_current_location AS Equipment_current_location,
    input.Time AS Time
FROM input TIMESTAMP BY time
JOIN
    referenceInput 
    ON input.Equipment_id = referenceInput.Equipment_id
    WHERE 
        ST_WITHIN(input.Equipment_currenct_location, referenceInput.Equipment_lease_location) = 1
```

このクエリを使用すると、製造元では機械の場所を自動的に監視し、機械が許可されたジオフェンスから離れたときにアラートを受け取ることができます。 組み込みの地理空間関数を活用すれば、サードパーティのライブラリを使用せずに、クエリ内で GPS データを使用できます。

詳細については、「[Azure Stream Analytics を使用したジオフェンシングおよび地理空間集計のシナリオ](geospatial-scenarios.md)」を参照してください。

## <a name="get-help"></a>ヘルプの参照

詳細については、[Azure Stream Analytics に関する Microsoft Q&A 質問ページ](/answers/topics/azure-stream-analytics.html)を参照してください。

## <a name="next-steps"></a>次のステップ
* [Azure Stream Analytics の概要](stream-analytics-introduction.md)
* [Azure Stream Analytics の使用](stream-analytics-real-time-fraud-detection.md)
* [Azure Stream Analytics ジョブのスケーリング](stream-analytics-scale-jobs.md)
* [Stream Analytics Query Language Reference (Stream Analytics クエリ言語リファレンス)](/stream-analytics-query/stream-analytics-query-language-reference)
* [Azure Stream Analytics management REST API reference (Azure ストリーム分析の管理 REST API リファレンス)](/rest/api/streamanalytics/)