---
title: "Stream Analytics の一般的使用状況パターンのクエリ例 | Microsoft Docs"
description: "一般的な Azure Stream Analytics クエリのパターン  "
keywords: "クエリ例"
services: stream-analytics
documentationcenter: 
author: jeffstokes72
manager: jhubbard
editor: cgronlun
ms.assetid: 6b9a7d00-fbcc-42f6-9cbb-8bbf0bbd3d0e
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 03/28/2017
ms.author: jeffstok
translationtype: Human Translation
ms.sourcegitcommit: 4c3f32cd6159052f17557c51e08e7e3f611aa338
ms.openlocfilehash: 7a1e705e40cd8f7b260c38f41e81e2f199555059
ms.lasthandoff: 01/06/2017


---
# <a name="query-examples-for-common-stream-analytics-usage-patterns"></a>一般的 Stream Analytics 使用状況パターンのクエリ例
## <a name="introduction"></a>はじめに
Azure Stream Analytics でのクエリは、SQL に似たクエリ言語で表されます。この言語については、[Stream Analytics クエリ言語リファレンス](https://msdn.microsoft.com/library/azure/dn834998.aspx)をご覧ください。  この記事では、実際のシナリオに基づいて、いくつかの一般的なクエリ パターンの対処方法について説明します。  このドキュメントは作成中であり、継続的に新しいパターンで更新されます。

## <a name="query-example-data-type-conversions"></a>クエリ例: データ型の変換
**説明**: 入力ストリームのプロパティの型を定義します。
たとえば、車の重量は入力ストリームでは文字列ですが、合計を計算するために INT に変換する必要があります。

**入力**:

| 保存する | Time | 重量 |
| --- | --- | --- |
| Honda |2015-01-01T00:00:01.0000000Z |"1000" |
| Honda |2015-01-01T00:00:02.0000000Z |"2000" |

**出力**:

| 保存する | 重量 |
| --- | --- |
| Honda |3000 |

**解決策**:

    SELECT
        Make,
        SUM(CAST(Weight AS BIGINT)) AS Weight
    FROM
        Input TIMESTAMP BY Time
    GROUP BY
        Make,
        TumblingWindow(second, 10)

**説明**: Weight フィールドに対して CAST ステートメントを使用し、型を指定します (サポートされるデータ型の一覧は [こちら](https://msdn.microsoft.com/library/azure/dn835065.aspx)を参照)。

## <a name="query-example-using-likenot-like-to-do-pattern-matching"></a>クエリ例: Like/Not like を使用したパターン マッチング
**説明**: イベントのフィールド値が特定のパターンと一致することをチェックします。たとえば、A で始まって 9 で終わるライセンス プレートを返します。

**入力**:

| 保存する | LicensePlate | Time |
| --- | --- | --- |
| Honda |ABC-123 |2015-01-01T00:00:01.0000000Z |
| Toyota |AAA-999 |2015-01-01T00:00:02.0000000Z |
| Nissan |ABC-369 |2015-01-01T00:00:03.0000000Z |

**出力**:

| 保存する | LicensePlate | Time |
| --- | --- | --- |
| Toyota |AAA-999 |2015-01-01T00:00:02.0000000Z |
| Nissan |ABC-369 |2015-01-01T00:00:03.0000000Z |

**解決策**:

    SELECT
        *
    FROM
        Input TIMESTAMP BY Time
    WHERE
        LicensePlate LIKE 'A%9'

**説明**: LIKE ステートメントを使用して、LicensePlate フィールドの値が、A で始まり、0 文字以上の任意の文字列があって、9 で終わるかどうかをチェックします。 

## <a name="query-example-specify-logic-for-different-casesvalues-case-statements"></a>クエリ例: 異なるケース/値に異なるロジックを指定する (CASE ステートメント)
**説明**: 特定の条件に基づいて異なる計算をフィールドに適用します。
たとえば、通過した自動車の台数を製造元ごとに表す文字列情報を指定します。このとき、台数が 1 のときだけ異なる計算を適用するものとします。

**入力**:

| 保存する | Time |
| --- | --- |
| Honda |2015-01-01T00:00:01.0000000Z |
| Toyota |2015-01-01T00:00:02.0000000Z |
| Toyota |2015-01-01T00:00:03.0000000Z |

**出力**:

| CarsPassed | Time |
| --- | --- | --- |
| 1 Honda |2015-01-01T00:00:10.0000000Z |
| 2 Toyotas |2015-01-01T00:00:10.0000000Z |

**解決策**:

    SELECT
        CASE
            WHEN COUNT(*) = 1 THEN CONCAT('1 ', Make)
            ELSE CONCAT(CAST(COUNT(*) AS NVARCHAR(MAX)), ' ', Make, 's')
        END AS CarsPassed,
        System.TimeStamp AS Time
    FROM
        Input TIMESTAMP BY Time
    GROUP BY
        Make,
        TumblingWindow(second, 10)

**説明**: CASE 句を使用すると、条件に基づいて異なる計算を適用できます (この例では、集計ウィンドウでの車の台数)。

## <a name="query-example-send-data-to-multiple-outputs"></a>クエリ例: 複数の出力にデータを送信する
**説明**:&1; つのジョブから複数の出力ターゲットにデータを送信します。
たとえば、しきい値に基づくアラートのデータを分析し、すべてのイベントを Blob Storage にアーカイブします。

**入力**:

| 保存する | Time |
| --- | --- |
| Honda |2015-01-01T00:00:01.0000000Z |
| Honda |2015-01-01T00:00:02.0000000Z |
| Toyota |2015-01-01T00:00:01.0000000Z |
| Toyota |2015-01-01T00:00:02.0000000Z |
| Toyota |2015-01-01T00:00:03.0000000Z |

**出力&1;**:

| 保存する | Time |
| --- | --- |
| Honda |2015-01-01T00:00:01.0000000Z |
| Honda |2015-01-01T00:00:02.0000000Z |
| Toyota |2015-01-01T00:00:01.0000000Z |
| Toyota |2015-01-01T00:00:02.0000000Z |
| Toyota |2015-01-01T00:00:03.0000000Z |

**出力&2;**:

| 保存する | Time | カウント |
| --- | --- | --- |
| Toyota |2015-01-01T00:00:10.0000000Z |3 |

**解決策**:

    SELECT
        *
    INTO
        ArchiveOutput
    FROM
        Input TIMESTAMP BY Time

    SELECT
        Make,
        System.TimeStamp AS Time,
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

**説明**: INTO 句は、Stream Analytics に対して、このステートメントからのデータを書き込む出力を指定します。
1 番目のクエリは、受け取ったデータを ArchiveOutput という名前の出力にパススルーします。
2 番目のクエリは、簡単な集計とフィルター処理を行い、結果を下流のアラート システムに送信します。
*注*: CTE の結果を複数の出力ステートメントで再利用することもできます (WITH ステートメント)。これには、入力ソースに対して開くリーダーが少なくて済む利点があります。
たとえば、次のように入力します。 

    WITH AllRedCars AS (
        SELECT
            *
        FROM
            Input TIMESTAMP BY Time
        WHERE
            Color = 'red'
    )
    SELECT * INTO HondaOutput FROM AllRedCars WHERE Make = 'Honda'
    SELECT * INTO ToyotaOutput FROM AllRedCars WHERE Make = 'Toyota'

## <a name="query-example-counting-unique-values"></a>クエリ例: ユニークな値のカウント
**説明**: ストリームに出現するフィールドの、値ごとの件数を一定間隔でカウントします。
たとえば、料金所を通過した自動車の、製造元ごとの台数を 2 秒間隔でカウントするにはどうすればよいのでしょうか。

**入力**:

| 保存する | Time |
| --- | --- |
| Honda |2015-01-01T00:00:01.0000000Z |
| Honda |2015-01-01T00:00:02.0000000Z |
| Toyota |2015-01-01T00:00:01.0000000Z |
| Toyota |2015-01-01T00:00:02.0000000Z |
| Toyota |2015-01-01T00:00:03.0000000Z |

**出力:**

| カウント | Time |
| --- | --- |
| 2 |2015-01-01T00:00:02.000Z |
| 1 |2015-01-01T00:00:04.000Z |

**解決策:**

````
SELECT
     COUNT(DISTINCT Make) AS CountMake,
     System.TIMESTAMP AS TIME
FROM Input TIMESTAMP BY TIME
GROUP BY 
     TumblingWindow(second, 2)
````


**説明:** COUNT(DISTINCT Make) は、特定の時間枠内での、"Make" 列の個別の値の数を返します。

## <a name="query-example-determine-if-a-value-has-changed"></a>クエリ例: 値が変化したかどうかを判定する
**説明**: 前の値を見て、現在の値と異なるかどうかを判定します (例: 有料道路上の前の自動車は、現在の自動車と同じメーカーか)。

**入力**:

| 保存する | Time |
| --- | --- |
| Honda |2015-01-01T00:00:01.0000000Z |
| Toyota |2015-01-01T00:00:02.0000000Z |

**出力**:

| 保存する | Time |
| --- | --- |
| Toyota |2015-01-01T00:00:02.0000000Z |

**解決策**:

    SELECT
        Make,
        Time
    FROM
        Input TIMESTAMP BY Time
    WHERE
        LAG(Make, 1) OVER (LIMIT DURATION(minute, 1)) <> Make

**説明**: LAG を使用して入力ストリームで&1; つ前のイベントを調べて、Make の値を取得します。 次に、現在のイベントの Make と比較し、異なる場合はイベントを出力します。

## <a name="query-example-find-first-event-in-a-window"></a>クエリ例: 期間内の最初のイベントを検索する
**説明**: 10 分間隔で最初の自動車を検索します。

**入力**:

| LicensePlate | 保存する | Time |
| --- | --- | --- |
| DXE 5291 |Honda |2015-07-27T00:00:05.0000000Z |
| YZK 5704 |Ford |2015-07-27T00:02:17.0000000Z |
| RMV 8282 |Honda |2015-07-27T00:05:01.0000000Z |
| YHN 6970 |Toyota |2015-07-27T00:06:00.0000000Z |
| VFE 1616 |Toyota |2015-07-27T00:09:31.0000000Z |
| QYF 9358 |Honda |2015-07-27T00:12:02.0000000Z |
| MDR 6128 |BMW |2015-07-27T00:13:45.0000000Z |

**出力**:

| LicensePlate | 保存する | Time |
| --- | --- | --- |
| DXE 5291 |Honda |2015-07-27T00:00:05.0000000Z |
| QYF 9358 |Honda |2015-07-27T00:12:02.0000000Z |

**解決策**:

    SELECT 
        LicensePlate,
        Make,
        Time
    FROM 
        Input TIMESTAMP BY Time
    WHERE 
        IsFirst(minute, 10) = 1

問題を変更して、10 分ごとに特定のメーカーの最初の自動車を検索します。

| LicensePlate | 保存する | Time |
| --- | --- | --- |
| DXE 5291 |Honda |2015-07-27T00:00:05.0000000Z |
| YZK 5704 |Ford |2015-07-27T00:02:17.0000000Z |
| YHN 6970 |Toyota |2015-07-27T00:06:00.0000000Z |
| QYF 9358 |Honda |2015-07-27T00:12:02.0000000Z |
| MDR 6128 |BMW |2015-07-27T00:13:45.0000000Z |

**解決策**:

    SELECT 
        LicensePlate,
        Make,
        Time
    FROM 
        Input TIMESTAMP BY Time
    WHERE 
        IsFirst(minute, 10) OVER (PARTITION BY Make) = 1

## <a name="query-example-find-last-event-in-a-window"></a>クエリ例: 期間内の最後のイベントを検索する
**説明**: 10 分間隔で最後の自動車を検索します。

**入力**:

| LicensePlate | 保存する | Time |
| --- | --- | --- |
| DXE 5291 |Honda |2015-07-27T00:00:05.0000000Z |
| YZK 5704 |Ford |2015-07-27T00:02:17.0000000Z |
| RMV 8282 |Honda |2015-07-27T00:05:01.0000000Z |
| YHN 6970 |Toyota |2015-07-27T00:06:00.0000000Z |
| VFE 1616 |Toyota |2015-07-27T00:09:31.0000000Z |
| QYF 9358 |Honda |2015-07-27T00:12:02.0000000Z |
| MDR 6128 |BMW |2015-07-27T00:13:45.0000000Z |

**出力**:

| LicensePlate | 保存する | Time |
| --- | --- | --- |
| VFE 1616 |Toyota |2015-07-27T00:09:31.0000000Z |
| MDR 6128 |BMW |2015-07-27T00:13:45.0000000Z |

**解決策**:

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
        Input.LicensePlate,
        Input.Make,
        Input.Time
    FROM
        Input TIMESTAMP BY Time 
        INNER JOIN LastInWindow
        ON DATEDIFF(minute, Input, LastInWindow) BETWEEN 0 AND 10
        AND Input.Time = LastInWindow.LastEventTime

**説明**: クエリには 2 つのステップがあります。第 1 のステップで、10 分の期間内の最後のタイムスタンプを検索します。 第&2; のステップで、第&1; のクエリの結果と元のストリームを結合し、各期間で最後のタイムスタンプに一致するイベントを検索します。 

## <a name="query-example-detect-the-absence-of-events"></a>クエリ例: イベントがないことを検出する
**説明**: 特定の条件と一致する値がストリームに存在しないことを確認します。
たとえば、同じ製造元の 2 台の自動車が、90 秒以内に続けて有料道路に進入したことを把握するには、どうすればよいのでしょうか。

**入力**:

| 保存する | LicensePlate | Time |
| --- | --- | --- |
| Honda |ABC-123 |2015-01-01T00:00:01.0000000Z |
| Honda |AAA-999 |2015-01-01T00:00:02.0000000Z |
| Toyota |DEF-987 |2015-01-01T00:00:03.0000000Z |
| Honda |GHI-345 |2015-01-01T00:00:04.0000000Z |

**出力**:

| 保存する | Time | CurrentCarLicensePlate | FirstCarLicensePlate | FirstCarTime |
| --- | --- | --- | --- | --- |
| Honda |2015-01-01T00:00:02.0000000Z |AAA-999 |ABC-123 |2015-01-01T00:00:01.0000000Z |

**解決策**:

    SELECT
        Make,
        Time,
        LicensePlate AS CurrentCarLicensePlate,
        LAG(LicensePlate, 1) OVER (LIMIT DURATION(second, 90)) AS FirstCarLicensePlate,
        LAG(Time, 1) OVER (LIMIT DURATION(second, 90)) AS FirstCarTime
    FROM
        Input TIMESTAMP BY Time
    WHERE
        LAG(Make, 1) OVER (LIMIT DURATION(second, 90)) = Make

**説明**: LAG を使用して入力ストリームで&1; つ前のイベントを調べて、Make の値を取得します。 次にそれを現在のイベントの Make と比較して、それらが同じ場合はイベントを出力し、LAG を使用して前の自動車についてのデータを取得します。

## <a name="query-example-detect-duration-between-events"></a>クエリ例: イベントの間隔を検出する
**説明**: 特定のイベントの間隔を検出します。 たとえば、Web クリック ストリームから、特定の機能に費やされた時間を調べます。

**入力**:  

| User | 機能 | イベント | Time |
| --- | --- | --- | --- |
| user@location.com |RightMenu |開始 |2015-01-01T00:00:01.0000000Z |
| user@location.com |RightMenu |End |2015-01-01T00:00:08.0000000Z |

**出力**:  

| User | 機能 | 時間 |
| --- | --- | --- |
| user@location.com |RightMenu |7 |

**ソリューション**

````
    SELECT
        [user], feature, DATEDIFF(second, LAST(Time) OVER (PARTITION BY [user], feature LIMIT DURATION(hour, 1) WHEN Event = 'start'), Time) as duration
    FROM input TIMESTAMP BY Time
    WHERE
        Event = 'end'
````

**説明**: LAST 関数を使用して、イベントの種類が "Start" であった最後の Time 値を取得します。 LAST 関数に PARTITION BY [user] が使用されていることに注目してください。この指定によって、一意のユーザーごとに結果が計算されます。  このクエリでは、"Start" イベントと "Stop" イベントの時間差の最大しきい値を 1 時間としていますが、必要に応じて構成可能です (LIMIT DURATION(hour, 1))。

## <a name="query-example-detect-duration-of-a-condition"></a>クエリ例: 条件の期間を検出する
**説明**: 条件が発生していた時間の長さを調べます。
たとえば、すべての自動車の重量が正しくない (20,000 ポンド超) 結果になるバグがあったと想定して。バグの期間を計算します。

**入力**:

| 保存する | Time | 重量 |
| --- | --- | --- |
| Honda |2015-01-01T00:00:01.0000000Z |2000 |
| Toyota |2015-01-01T00:00:02.0000000Z |25000 |
| Honda |2015-01-01T00:00:03.0000000Z |26000 |
| Toyota |2015-01-01T00:00:04.0000000Z |25000 |
| Honda |2015-01-01T00:00:05.0000000Z |26000 |
| Toyota |2015-01-01T00:00:06.0000000Z |25000 |
| Honda |2015-01-01T00:00:07.0000000Z |26000 |
| Toyota |2015-01-01T00:00:08.0000000Z |2000 |

**出力**:

| StartFault | EndFault |
| --- | --- |
| 2015-01-01T00:00:02.000Z |2015-01-01T00:00:07.000Z |

**解決策**:

````
    WITH SelectPreviousEvent AS
    (
    SELECT
    *,
        LAG([time]) OVER (LIMIT DURATION(hour, 24)) as previousTime,
        LAG([weight]) OVER (LIMIT DURATION(hour, 24)) as previousWeight
    FROM input TIMESTAMP BY [time]
    )

    SELECT 
        LAG(time) OVER (LIMIT DURATION(hour, 24) WHEN previousWeight < 20000 ) [StartFault],
        previousTime [EndFault]
    FROM SelectPreviousEvent
    WHERE
        [weight] < 20000
        AND previousWeight > 20000
````

**説明**: LAG を使用して 24 時間の入力ストリームに着目し、重量の上限を 20000 として、StartFault と StopFault の範囲で該当するインスタンスを探します。

## <a name="query-example-fill-missing-values"></a>クエリ例: 欠落値を入力する
**説明**: 欠落値があるイベントのストリームの場合、定期的な間隔でイベントのストリームを生成します。
たとえば、最近検出されたデータ ポイントを報告する 5 秒ごとのイベントを生成します。

**入力**:

| t | 値 |
| --- | --- |
| "2014-01-01T06:01:00" |1 |
| "2014-01-01T06:01:05" |2 |
| "2014-01-01T06:01:10" |3 |
| "2014-01-01T06:01:15" |4 |
| "2014-01-01T06:01:30" |5 |
| "2014-01-01T06:01:35" |6 |

**出力 (最初の 10 行)**:

| windowend | lastevent.t | lastevent.value |
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

**解決策**:

    SELECT
        System.Timestamp AS windowEnd,
        TopOne() OVER (ORDER BY t DESC) AS lastEvent
    FROM
        input TIMESTAMP BY t
    GROUP BY HOPPINGWINDOW(second, 300, 5)


**説明**: このクエリは、5 秒ごとにイベントを生成し、それまでに受信した最後のイベントを出力します。 [ホッピング ウィンドウ](https://msdn.microsoft.com/library/dn835041.aspx "ホッピング ウィンドウ - Azure Stream Analytics") 期間は、クエリが最新のイベントを検出するためにさかのぼる期間 (この例では&300; 秒) を指定します。

## <a name="get-help"></a>問い合わせ
さらにサポートが必要な場合は、 [Azure Stream Analytics フォーラム](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics)

## <a name="next-steps"></a>次のステップ
* [Azure Stream Analytics の概要](stream-analytics-introduction.md)
* [Azure Stream Analytics の使用](stream-analytics-get-started.md)
* [Azure Stream Analytics ジョブのスケーリング](stream-analytics-scale-jobs.md)
* [Stream Analytics Query Language Reference (Stream Analytics クエリ言語リファレンス)](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Azure Stream Analytics management REST API reference (Azure ストリーム分析の管理 REST API リファレンス)](https://msdn.microsoft.com/library/azure/dn835031.aspx)


