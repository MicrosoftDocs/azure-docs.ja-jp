---
title: Azure Stream Analytics ウィンドウ関数の概要
description: この記事では、Azure Stream Analytics ジョブで使用される 4 つのウィンドウ関数 (タンブリング、ホッピング、スライディング、セッション) について説明します。
services: stream-analytics
author: jseb225
ms.author: jeanb
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 06/11/2019
ms.openlocfilehash: 530ff8d09d6c580a31ae26929fafcec5bb5b471b
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/07/2019
ms.locfileid: "67621586"
---
# <a name="introduction-to-stream-analytics-windowing-functions"></a>Stream Analytics ウィンドウ関数の概要

タイム ストリーミング シナリオでは、テンポラル ウィンドウに含まれているデータに対する操作の実行は一般的なパターンです。 Stream Analytics には、ウィンドウ関数に対するネイティブ サポートがあるため、開発者は複雑なストリーム処理ジョブを最小限の作業で作成できます。

選択できるテンポラル ウィンドウには、[**タンブリング**](https://docs.microsoft.com/stream-analytics-query/tumbling-window-azure-stream-analytics)、[**ホッピング**](https://docs.microsoft.com/stream-analytics-query/hopping-window-azure-stream-analytics)、[**スライディング**](https://docs.microsoft.com/stream-analytics-query/sliding-window-azure-stream-analytics)、および[**セッション**](https://docs.microsoft.com/stream-analytics-query/session-window-azure-stream-analytics) ウィンドウの 4 種類があります。  ウィンドウ関数は、Stream Analytics ジョブ内のクエリ構文の [**GROUP BY**](https://docs.microsoft.com/stream-analytics-query/group-by-azure-stream-analytics) 句で使用します。 [**Windows()** 関数](https://docs.microsoft.com/stream-analytics-query/windows-azure-stream-analytics)を使用して、複数のウィンドウにわたってイベントを集計することもできます。

すべての[ウィンドウ](https://docs.microsoft.com/stream-analytics-query/windowing-azure-stream-analytics)操作が、ウィンドウの**終了**時に結果を出力します。 ウィンドウの出力は、使用される集計関数に基づく単一のイベントになります。 出力イベントにはウィンドウの終了のタイム スタンプが割り当てられ、すべてのウィンドウ関数が固定長で定義されています。 

![Stream Analytics ウィンドウ関数の概念](media/stream-analytics-window-functions/stream-analytics-window-functions-conceptual.png)

## <a name="tumbling-window"></a>タンブリング ウィンドウ
タンブリング ウィンドウ関数は、次の例のように、データ ストリームを個別の時間セグメントに分割して、関数を実行するときに使用します。 タンブリング ウィンドウの主な差別化要素は、重複せずに繰り返すことであり、1 つのイベントが複数のタンブリング ウィンドウに属することはできません。

![Stream Analytics タンブリング ウィンドウ](media/stream-analytics-window-functions/stream-analytics-window-functions-tumbling-intro.png)

## <a name="hopping-window"></a>ホッピング ウィンドウ
ホッピング ウィンドウ関数は、一定の期間だけ前に進みます。 重複できるタンブリング ウィンドウと考えると簡単で、イベントは複数のホッピング ウィンドウ結果セットに属することができます。 ホッピング ウィンドウをタンブリング ウィンドウと同じにするには、ホップ サイズをウィンドウ サイズと同じに指定します。 

![Stream Analytics ホッピング ウィンドウ](media/stream-analytics-window-functions/stream-analytics-window-functions-hopping-intro.png)

## <a name="sliding-window"></a>スライディング ウィンドウ
タンブリング ウィンドウまたはホッピング ウィンドウとは異なり、スライディング ウィンドウ関数は、イベント発生時に出力 **のみ** を生成します。 すべてのウィンドウに少なくとも 1 つのイベントがあり、€ (イプシロン) ごとに継続的に前に移動します。 ホッピング ウィンドウと同様に、イベントは複数のスライディング ウィンドウに属することができます。

![Stream Analytics スライディング ウィンドウ](media/stream-analytics-window-functions/stream-analytics-window-functions-sliding-intro.png)

## <a name="session-window"></a>セッション ウィンドウ
セッション ウィンドウ関数は、類似した時刻に到着するイベントをグループ化することにより、データが存在しない期間をフィルターで除外します。 これには、タイムアウト、最大期間、パーティション分割キー (省略可能) の 3 つの主なパラメーターがあります。

![Stream Analytics セッション ウィンドウ](media/stream-analytics-window-functions/stream-analytics-window-functions-session-intro.png)

セッション ウィンドウは、最初のイベントが発生したときに開始されます。 最後にイベントが取り込まれてから指定されたタイムアウト期間内に別のイベントが発生した場合、ウィンドウはその新しいイベントを含むように拡張されます。 タイムアウト期間内にどのイベントも発生しなかった場合、ウィンドウはタイムアウト時に閉じられます。

指定されたタイムアウト期間内にイベントが発生し続けた場合、セッション ウィンドウは最大期間に達するまで拡張し続けます。 最大期間のチェック間隔は、指定された最大期間と同じサイズに設定されます。 たとえば、最大期間が 10 である場合、ウィンドウが最大期間を超えたかどうかに関するチェックは t = 0、10、20、30 などに発生します。

パーティション キーが指定されている場合、イベントはそのキーでグループ化され、セッション ウィンドウは各グループに独立に適用されます。 このパーティション分割は、ユーザーまたはデバイスごとに異なるセッション ウィンドウが必要な場合に役立ちます。


## <a name="next-steps"></a>次の手順
* [Azure Stream Analytics の概要](stream-analytics-introduction.md)
* [Azure Stream Analytics の使用](stream-analytics-real-time-fraud-detection.md)
* [Azure Stream Analytics ジョブのスケーリング](stream-analytics-scale-jobs.md)
* [Stream Analytics Query Language Reference (Stream Analytics クエリ言語リファレンス)](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference)
* [Azure Stream Analytics management REST API reference (Azure ストリーム分析の管理 REST API リファレンス)](https://msdn.microsoft.com/library/azure/dn835031.aspx)

