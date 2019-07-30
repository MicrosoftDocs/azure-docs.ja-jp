---
title: Azure Stream Analytics でのジョブ監視について
description: この記事では、Azure portal で Azure Stream Analytics ジョブを監視する方法について説明します。
services: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 6/21/2018
ms.custom: seodec18
ms.openlocfilehash: a4fc6db300a24d483e88efe668645d2cdb62496b
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/07/2019
ms.locfileid: "67612276"
---
# <a name="understand-stream-analytics-job-monitoring-and-how-to-monitor-queries"></a>Stream Analytics ジョブ監視とクエリの監視方法の概要

## <a name="introduction-the-monitor-page"></a>概要:[監視] ページ
Azure portal は、クエリとジョブ パフォーマンスの監視とトラブルシューティングに使用できる主要なパフォーマンス メトリックを明らかにします。 これらのメトリックを表示するには、メトリックを確認したい Stream Analytics ジョブを参照し、[概要] ページの **[監視]** 画面を表示します。  

![Stream Analytics ジョブ監視リンク](./media/stream-analytics-monitoring/02-stream-analytics-monitoring-block.png)

次のようなウィンドウが表示されます。

![Stream Analytics ジョブ監視ダッシュボード](./media/stream-analytics-monitoring/01-stream-analytics-monitoring.png)  

## <a name="metrics-available-for-stream-analytics"></a>Stream Analytics に利用できるメトリック
| メトリック                 | 定義                               |
| ---------------------- | ---------------------------------------- |
| バックログされた入力イベント       | バックログされた入力イベントの数。 このメトリックの 0 以外の値は、ジョブが受信イベントの数についていけないことを意味しています。 この値がゆっくり増加する場合や一貫して 0 以外である場合は、ジョブをスケールアウトする必要があります。 「[ストリーミング ユニットの理解と調整](stream-analytics-streaming-unit-consumption.md)」を参照すると、より深く学習できます。 |
| データ変換エラー | 想定の出力スキーマに変換できなかった出力イベントの数。 ラー ポリシーを "削除" に変更すると、このシナリオが発生するイベントを削除できます。 |
| 初期入力イベント       | アプリケーション タイムスタンプが受信時間より 5 分より前のイベント。 |
| 失敗した関数の要求 | 失敗した Azure Machine Learning 関数呼び出しの数 (存在する場合)。 |
| 関数のイベント        | Azure Machine Learning 関数に送られたイベントの数 (存在する場合)。 |
| 関数の要求      | Azure Machine Learning 関数に対する呼び出しの数 (存在する場合)。 |
| 逆シリアル化の入力エラー       | 逆シリアル化できなかった入力イベントの数。  |
| 入力イベントのバイト数      | Stream Analytics ジョブが受信したデータの量 (バイト単位)。 イベントが入力ソースに送信されることを検証するために使用できます。 |
| 入力イベント           | 入力イベントから逆シリアル化されたレコードの数。 この数には、逆シリアル化エラーが発生する受信イベントは含みません。 |
| 受信した入力ソース       | ジョブが受信したメッセージ数。 Event Hub の場合、メッセージは 1 つの EventData です。 BLOB の場合、メッセージは 1 つの BLOB です。 なお、入力ソースは逆シリアル化前にカウントされます。 逆シリアル化エラーが発生した場合、入力ソースは入力イベントよりも大きくなる場合があります。 それ以外の場合、各メッセージには複数のイベントが含まれる場合があるので、入力イベント以下になります。 |
| 遅延入力イベント      | 構成済みの到着遅延許容期間より後に到着したイベント。 詳細については [Azure Stream Analytics のイベントの順序に関する考慮事項](stream-analytics-out-of-order-and-late-events.md)を確認してください。 |
| 異常イベント    | イベント順序ポリシーに基づいて、削除された、または調整されたタイムスタンプが付与された、順不同で受信したイベントの数。 誤順序の許容期間の設定の構成により、影響を受けることがあります。 |
| 出力イベント          | Stream Analytics のジョブから出力ターゲットに送信されたデータのイベントの量。 |
| 実行時エラー         | クエリ処理に関連するエラーの総数 (イベントの取り込み時または結果の出力時に見つかったエラーを除く) |
| SU % 使用率       | ジョブの [スケール] タブからジョブに割り当てられたストリーミング ユニットの使用率。 このインジケーターが 80% 以上に達した場合、イベントの処理が遅れたり、進行が遅延または停止する可能性があります。 |
| 透かしの遅延       | ジョブ内のすべての出力のすべてのパーティションにわたる透かしの最大遅延。 |

これらのメトリックを使用して、[Stream Analytics ジョブのパフォーマンスを監視](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-set-up-alerts#scenarios-to-monitor)することができます。 

## <a name="customizing-monitoring-in-the-azure-portal"></a>Azure ポータルでの監視のカスタマイズ
グラフのタイプ、表示されるメトリック、および時間範囲を [グラフの編集] 設定で調整できます。 詳細については、「[監視のカスタマイズ方法](../monitoring-and-diagnostics/insights-how-to-customize-monitoring.md)」をご覧ください。

  ![Stream Analytics クエリ モニター タイム グラフ](./media/stream-analytics-monitoring/08-stream-analytics-monitoring.png)  


## <a name="latest-output"></a>最新の出力
[概要] ページに示されているように、ジョブを監視する他の対象データ ポイントは最後の出力時刻です。
この時刻は、ジョブの最新の出力のアプリケーション時間 (つまり、イベント データからのタイムスタンプを使用した時間) です。

## <a name="get-help"></a>問い合わせ
さらにサポートが必要な場合は、 [Azure Stream Analytics フォーラム](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics)

## <a name="next-steps"></a>次の手順
* [Azure Stream Analytics の概要](stream-analytics-introduction.md)
* [Azure Stream Analytics の使用](stream-analytics-real-time-fraud-detection.md)
* [Azure Stream Analytics ジョブのスケーリング](stream-analytics-scale-jobs.md)
* [Stream Analytics Query Language Reference (Stream Analytics クエリ言語リファレンス)](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference)
* [Azure Stream Analytics management REST API reference (Azure ストリーム分析の管理 REST API リファレンス)](https://msdn.microsoft.com/library/azure/dn835031.aspx)
