---
title: "Azure Stream Analytics によるイベントの順序と遅延の処理 | Microsoft Docs"
description: "データ ストリーム中の順序を逸脱したイベントまたは遅延イベントが Stream Analytics によって処理されるしくみを説明します。"
keywords: "順序の逸脱, 遅延, イベント"
documentationcenter: 
services: stream-analytics
author: samacha
manager: jhubbard
editor: cgronlun
ms.assetid: 
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 04/20/2017
ms.author: samacha
ms.translationtype: HT
ms.sourcegitcommit: 8351217a29af20a10c64feba8ccd015702ff1b4e
ms.openlocfilehash: 5089dda48ea829902663ef9d09fe83177df6f220
ms.contentlocale: ja-jp
ms.lasthandoff: 08/29/2017

---
# <a name="azure-stream-analytics-event-order-handling"></a>Azure Stream Analytics によるイベントの順序の処理

時間の経過に伴い発生するイベントのデータ ストリームでは、各イベントは受信された時刻と共に記録されます。 いくつかの条件が原因で、一部のイベントが送信されたのと異なる順序でイベント ストリームに届くことがあります。 このような状況が発生するのは、単純な TCP 再送信、または送信デバイスと受信イベント ハブの時刻のずれが原因の場合があります。 イベントの到着がない場合に時間を進めるために、"区切り記号" イベントも受信イベント ストリームに追加されます。 これらのイベントは、"3 分間ログインがない場合に通知する" などのシナリオで必要です。

順序どおりでない入力ストリームは、次のいずれかに当てはまります。
* 並べ替えられた (そのため**遅延**した)。
* ユーザーが指定したポリシーに従って、システムによって調整された。


## <a name="lateness-tolerance"></a>遅延の許容範囲
Stream Analytics では、上に挙げた種類のシナリオが許容されます。 Stream Analytics には "順序を逸脱した" イベントと "遅延" イベントへの対応策があり、 これらのイベントは次の方法で処理されます。

* 順序を逸脱しているものの、設定された許容範囲内であるイベントは、**タイムスタンプに基づいて並べ替え**られます。
* 許容範囲を超えたイベントは、**削除または調整**されます。
    * **調整**: 許容される最新の時刻に届いたかのように調整されます。
    * **削除**: 破棄されます。

![Stream Analytics のイベントの処理](media/stream-analytics-event-handling/stream-analytics-event-handling.png)

## <a name="reduce-the-number-of-out-of-order-events"></a>順序を逸脱したイベントの数の抑制

受信イベントを処理する際に一時的な変換を適用するため (例: ウィンドウ表示の集計または一時的な結合)、Stream Analytics はタイムスタンプの順序に基づいて受信イベントを並べ替えます。

"TIMESTAMP BY" キーワードを**使用しなかった**場合には、Azure Event Hubs のイベント エンキュー時間が既定で使用されます。 Event Hubs では、イベント ハブの各パーティションに対するタイムスタンプの単調性が保証されます。 さらに、すべてのパーティションからのイベントがタイムスタンプの順序でマージされることも保証されます。 Event Hubs によるこれら 2 つの保証があるため、順序を逸脱したイベントは発生しません。

送信側のタイムスタンプを使用することが重要な場合があります。 この場合、イベント ペイロードからのタイムスタンプは "TIMESTAMP BY" を使用して選択します。 これらのシナリオでは、イベントが順序を逸脱する原因が 1 つまたは複数含まれる可能性があります。

* イベント生成元に時刻のずれがある。 コンピューターが異なっているため生成元の時計が異なる場合、時刻のずれがよく発生します。
* イベントの送信元から送信先のイベント ハブの間でネットワークの遅延がある。
* イベント ハブ パーティションの間で時刻のずれが存在する。 Stream Analytics では最初に、すべてのイベント ハブ パーティションからのイベントがイベント エンキュー時間に基づいて並べ替えられます。 次に、順序を逸脱したイベントの有無についてデータ ストリームがチェックされます。

構成タブに表示される既定値は次のとおりです。

![Stream Analytics による順序の逸脱の処理](media/stream-analytics-event-handling/stream-analytics-out-of-order-handling.png)

順序の逸脱の許容期間として 0 秒を使用する場合、すべてのイベントが常に順番どおりであると仮定することになります。 イベントが順序を逸脱する原因として上の 3 つがあることを考慮すると、この仮定が真であるとは考えにくいと言えます。 

Stream Analytics がイベントの順序の逸脱を修正できるようにするには、順序の逸脱の許容期間として 0 秒ではない値を指定します。 Stream Analytics はこの期間が経過するまでイベントをバッファーしてから、選択されたタイムスタンプを使用してそれらのイベントを並べ替えます。 その後、一時的な変換が適用されます。 許容期間は 3 秒から開始できます。また、値を調整して時間調整されるイベントの数を減らすことができます。 

バッファリングの副作用は、出力が**同じ時間だけ遅延する**ことです。 値を調整して順序を逸脱したイベントの数を抑制したり、ジョブの待ち時間が短い状態を維持したりできます。

## <a name="get-help"></a>問い合わせ
さらにサポートが必要な場合は、[Azure Stream Analytics フォーラム](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics)を参照してください。

## <a name="next-steps"></a>次のステップ
* [Stream Analytics の概要](stream-analytics-introduction.md)
* [Stream Analytics の使用](stream-analytics-real-time-fraud-detection.md)
* [Stream Analytics ジョブのスケール設定](stream-analytics-scale-jobs.md)
* [Stream Analytics クエリ言語リファレンス](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Stream Analytics 管理 REST API リファレンス](https://msdn.microsoft.com/library/azure/dn835031.aspx)
