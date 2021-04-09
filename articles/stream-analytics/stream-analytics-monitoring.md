---
title: Azure Stream Analytics でのジョブ監視について
description: この記事では、Azure portal で Azure Stream Analytics ジョブを監視する方法について説明します。
author: sidramadoss
ms.author: sidram
ms.service: stream-analytics
ms.topic: how-to
ms.date: 03/08/2021
ms.custom: seodec18
ms.openlocfilehash: 6d3558511721a91c3a195cb510a1a00d5d8a9a51
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102487880"
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
| CPU 使用率 (%) (プレビュー)       | ジョブで使用されている CPU の割合。 この値が非常に高い (90% 以上) 場合であっても、このメトリックだけに基づいて SU の数を増やすことはしないでください。 バックログされた入力イベントまたは透かしの遅延の数が増えた場合、この CPU% 使用率メトリックを使用し、CPU がボトルネックかどうかを判断できます。 このメトリックは間欠的に急変化することがあります。 CPU ボトルネックに起因し、入力がバックログされたり、透かしの遅延が増えたりするとき、その境目となるジョブの上限を判断する目的でスケール テストを実行することをお勧めします。 |
| 初期入力イベント       | アプリケーション タイムスタンプが受信時間より 5 分より前のイベント。 |
| 失敗した関数の要求 | 失敗した Azure Machine Learning 関数呼び出しの数 (存在する場合)。 |
| 関数のイベント        | Azure Machine Learning 関数に送られたイベントの数 (存在する場合)。 |
| 関数の要求      | Azure Machine Learning 関数に対する呼び出しの数 (存在する場合)。 |
| 逆シリアル化の入力エラー       | 逆シリアル化できなかった入力イベントの数。  |
| 入力イベントのバイト数      | Stream Analytics ジョブが受信したデータの量 (バイト単位)。 イベントが入力ソースに送信されることを検証するために使用できます。 |
| 入力イベント           | 入力イベントから逆シリアル化されたレコードの数。 この数には、逆シリアル化エラーが発生する受信イベントは含みません。 内部回復や自己結合などのシナリオでは、Stream Analytics によって同じイベントを複数回取り込むことができます。 そのため、ジョブにシンプルな "パススルー" クエリが含まれている場合は、入力イベントと出力イベントのメトリックが一致すると想定しないことをお勧めします。 |
| 受信した入力ソース       | ジョブが受信したメッセージ数。 Event Hub の場合、メッセージは 1 つの EventData です。 BLOB の場合、メッセージは 1 つの BLOB です。 なお、入力ソースは逆シリアル化前にカウントされます。 逆シリアル化エラーが発生した場合、入力ソースは入力イベントよりも大きくなる場合があります。 それ以外の場合、各メッセージには複数のイベントが含まれる場合があるので、入力イベント以下になります。 |
| 遅延入力イベント      | 構成済みの到着遅延許容期間より後に到着したイベント。 詳細については [Azure Stream Analytics のイベントの順序に関する考慮事項](./stream-analytics-time-handling.md)を確認してください。 |
| 異常イベント    | イベント順序ポリシーに基づいて、削除された、または調整されたタイムスタンプが付与された、順不同で受信したイベントの数。 誤順序の許容期間の設定の構成により、影響を受けることがあります。 |
| 出力イベント          | Stream Analytics のジョブから出力ターゲットに送信されたデータのイベントの量。 |
| 実行時エラー         | クエリ処理に関連するエラーの総数 (イベントの取り込み時または結果の出力時に見つかったエラーを除く) |
| SU % 使用率       | ジョブで使用されているメモリの割合。 SU % 使用率が常に 80% を超え、透かしの遅延が増加し、バックログされたイベントの数が増加している場合は、ストリーミング ユニットを増やすことを検討してください。 使用率が高い場合は、最大数に近い割り当てリソースがジョブによって使用されていることを示します。 |
| 透かしの遅延       | ジョブ内のすべての出力のすべてのパーティションにわたる透かしの最大遅延。 |

これらのメトリックを使用して、[Stream Analytics ジョブのパフォーマンスを監視](./stream-analytics-set-up-alerts.md#scenarios-to-monitor)することができます。 

## <a name="customizing-monitoring-in-the-azure-portal"></a>Azure ポータルでの監視のカスタマイズ
グラフのタイプ、表示されるメトリック、および時間範囲を [グラフの編集] 設定で調整できます。 詳細については、「[監視のカスタマイズ方法](../azure-monitor/data-platform.md)」をご覧ください。

  ![Stream Analytics クエリ モニター タイム グラフ](./media/stream-analytics-monitoring/08-stream-analytics-monitoring.png)  


## <a name="latest-output"></a>最新の出力
[概要] ページに示されているように、ジョブを監視する他の対象データ ポイントは最後の出力時刻です。
この時刻は、ジョブの最新の出力のアプリケーション時間 (つまり、イベント データからのタイムスタンプを使用した時間) です。

## <a name="get-help"></a>ヘルプの参照
詳細については、[Azure Stream Analytics に関する Microsoft Q&A 質問ページ](/answers/topics/azure-stream-analytics.html)を参照してください

## <a name="next-steps"></a>次のステップ
* [Azure Stream Analytics の概要](stream-analytics-introduction.md)
* [Azure Stream Analytics の使用](stream-analytics-real-time-fraud-detection.md)
* [Azure Stream Analytics ジョブのスケーリング](stream-analytics-scale-jobs.md)
* [Stream Analytics Query Language Reference (Stream Analytics クエリ言語リファレンス)](/stream-analytics-query/stream-analytics-query-language-reference)
* [Azure Stream Analytics management REST API reference (Azure ストリーム分析の管理 REST API リファレンス)](/rest/api/streamanalytics/)
