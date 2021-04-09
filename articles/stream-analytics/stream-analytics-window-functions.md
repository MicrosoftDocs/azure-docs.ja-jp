---
title: Azure Stream Analytics ウィンドウ関数の概要
description: この記事では、Azure Stream Analytics ジョブで使用される 4 つのウィンドウ関数 (タンブリング、ホッピング、スライディング、セッション) について説明します。
author: jseb225
ms.author: jeanb
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 03/16/2021
ms.openlocfilehash: 5ff59b0add8a9b3c48ad8ae80a50c0a816c08d6e
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "104588081"
---
# <a name="introduction-to-stream-analytics-windowing-functions"></a>Stream Analytics ウィンドウ関数の概要

タイム ストリーミング シナリオでは、テンポラル ウィンドウに含まれているデータに対する操作の実行は一般的なパターンです。 Stream Analytics には、ウィンドウ関数に対するネイティブ サポートがあるため、開発者は複雑なストリーム処理ジョブを最小限の作業で作成できます。

選択できる 5 種類のテンポラル ウィンドウには、[**タンブリング**](/stream-analytics-query/tumbling-window-azure-stream-analytics)、[**ホッピング**](/stream-analytics-query/hopping-window-azure-stream-analytics)、[**スライディング**](/stream-analytics-query/sliding-window-azure-stream-analytics)、[**セッション**](/stream-analytics-query/session-window-azure-stream-analytics)、および [**スナップショット**](/stream-analytics-query/snapshot-window-azure-stream-analytics) ウィンドウがあります。  ウィンドウ関数は、Stream Analytics ジョブ内のクエリ構文の [**GROUP BY**](/stream-analytics-query/group-by-azure-stream-analytics) 句で使用します。 [**Windows()** 関数](/stream-analytics-query/windows-azure-stream-analytics)を使用して、複数のウィンドウにわたってイベントを集計することもできます。

すべての [ウィンドウ](/stream-analytics-query/windowing-azure-stream-analytics)操作が、ウィンドウの **終了** 時に結果を出力します。 Stream Analytics ジョブを開始するときは、 *[ジョブの出力の開始時刻]* を指定でき、指定した時刻に最初のウィンドウを出力するために、受信ストリームで以前のイベントが自動的にフェッチされます。たとえば、 *[今すぐ]* オプションを使用して開始すると、すぐにデータの出力が開始されます。 ウィンドウの出力は、使用される集計関数に基づく単一のイベントになります。 出力イベントにはウィンドウの終了のタイム スタンプが割り当てられ、すべてのウィンドウ関数が固定長で定義されています。 

![Stream Analytics ウィンドウ関数の概念](media/stream-analytics-window-functions/stream-analytics-window-functions-conceptual.png)

## <a name="tumbling-window"></a>タンブリング ウィンドウ
タンブリング ウィンドウ関数は、次の例のように、データ ストリームを個別の時間セグメントに分割して、関数を実行するときに使用します。 タンブリング ウィンドウの主な差別化要素は、重複せずに繰り返すことであり、1 つのイベントが複数のタンブリング ウィンドウに属することはできません。

![Stream Analytics タンブリング ウィンドウ](media/stream-analytics-window-functions/stream-analytics-window-functions-tumbling-intro.png)

## <a name="hopping-window"></a>ホッピング ウィンドウ
ホッピング ウィンドウ関数は、一定の期間だけ前に進みます。 ウィンドウ サイズよりも頻繁に重複して出力できるタンブリング ウィンドウと考えるのが簡単です。 イベントは複数のホッピング ウィンドウ結果セットに属することができます。 ホッピング ウィンドウをタンブリング ウィンドウと同じにするには、ホップ サイズをウィンドウ サイズと同じに指定します。 

![Stream Analytics ホッピング ウィンドウ](media/stream-analytics-window-functions/stream-analytics-window-functions-hopping-intro.png)

## <a name="sliding-window"></a>スライディング ウィンドウ

スライディング ウィンドウには、タンブリングやホッピングの各ウィンドウとは異なり、ウィンドウの内容が実際に変更された時点のイベントのみが出力されます。 つまり、イベントがウィンドウに出入りしたときです。 そのため、すべてのウィンドウに少なくとも 1 つのイベントがあります。 ホッピング ウィンドウと同様に、イベントは複数のスライディング ウィンドウに属することができます。

![Stream Analytics スライディング ウィンドウ](media/stream-analytics-window-functions/stream-analytics-window-functions-sliding-intro.png)

## <a name="session-window"></a>セッション ウィンドウ
セッション ウィンドウ関数は、類似した時刻に到着するイベントをグループ化することにより、データが存在しない期間をフィルターで除外します。 これには、タイムアウト、最大期間、パーティション分割キー (省略可能) の 3 つの主なパラメーターがあります。

![Stream Analytics セッション ウィンドウ](media/stream-analytics-window-functions/stream-analytics-window-functions-session-intro.png)

セッション ウィンドウは、最初のイベントが発生したときに開始されます。 最後にイベントが取り込まれてから指定されたタイムアウト期間内に別のイベントが発生した場合、ウィンドウはその新しいイベントを含むように拡張されます。 タイムアウト期間内にどのイベントも発生しなかった場合、ウィンドウはタイムアウト時に閉じられます。

指定されたタイムアウト期間内にイベントが発生し続けた場合、セッション ウィンドウは最大期間に達するまで拡張し続けます。 最大期間のチェック間隔は、指定された最大期間と同じサイズに設定されます。 たとえば、最大期間が 10 である場合、ウィンドウが最大期間を超えたかどうかに関するチェックは t = 0、10、20、30 などに発生します。

パーティション キーが指定されている場合、イベントはそのキーでグループ化され、セッション ウィンドウは各グループに独立に適用されます。 このパーティション分割は、ユーザーまたはデバイスごとに異なるセッション ウィンドウが必要な場合に役立ちます。

## <a name="snapshot-window"></a>スナップショット ウィンドウ

スナップショット ウィンドウでは、同じタイムスタンプを持つイベントがグループ化されます。 特定のウィンドウ関数 ([SessionWindow()](/stream-analytics-query/session-window-azure-stream-analytics) など) を必要とする他のウィンドウの種類とは異なり、System.Timestamp() を GROUP BY 句に追加することでスナップショット ウィンドウを適用できます。

![Stream Analytics のスナップショット ウィンドウ](media/stream-analytics-window-functions/snapshot.png)

## <a name="next-steps"></a>次のステップ
* [Azure Stream Analytics の概要](stream-analytics-introduction.md)
* [Azure Stream Analytics の使用](stream-analytics-real-time-fraud-detection.md)
* [Azure Stream Analytics ジョブのスケーリング](stream-analytics-scale-jobs.md)
* [Stream Analytics Query Language Reference (Stream Analytics クエリ言語リファレンス)](/stream-analytics-query/stream-analytics-query-language-reference)
* [Azure Stream Analytics management REST API reference (Azure ストリーム分析の管理 REST API リファレンス)](/rest/api/streamanalytics/)
