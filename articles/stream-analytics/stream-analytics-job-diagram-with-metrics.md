---
title: Azure Stream Analytics でのデータ主導型デバッグ
description: この記事では、Azure Portal のジョブ ダイアグラムとメトリックを使って、Azure Stream Analytics ジョブのトラブルシューティングを行う方法について説明します。
author: jseb225
ms.author: jeanb
ms.service: stream-analytics
ms.topic: how-to
ms.date: 05/01/2017
ms.openlocfilehash: 6d20454515088ccca87665d9b3b27c0d82c3cdf9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98020402"
---
# <a name="data-driven-debugging-by-using-the-job-diagram"></a>ジョブ ダイアグラムを使用したデータ主導型デバッグ

Azure Portal の **[監視]** ブレードのジョブ ダイアグラムは、ジョブ パイプラインの視覚化に役立ちます。 ここには、入力、出力、クエリのステップが表示されます。 ジョブ ダイアグラムを使用すると、各ステップのメトリックを確認して、問題のトラブルシューティングを行う際に問題の原因をより迅速に分離できます。

## <a name="using-the-job-diagram"></a>ジョブ ダイアグラムの使用

Stream Analytics ジョブの使用中に、Azure Portal の **[サポート + トラブルシューティング]** の下にある **[ジョブ ダイアグラム]** を選択します。

![メトリック付きのジョブ ダイアグラム - 場所](./media/stream-analytics-job-diagram-with-metrics/stream-analytics-job-diagram-with-metrics-portal-1.png)

各クエリ ステップを選択すると、クエリ編集ウィンドウに対応するセクションが表示されます。 ステップのメトリック グラフは、ページ下部のウィンドウに表示されます。

![メトリック付きのジョブ ダイアグラム - 基本的なジョブ](./media/stream-analytics-job-diagram-with-metrics/stream-analytics-job-diagram-with-metrics-portal-2.png)

Azure Event Hubs の入力をパーティション単位で確認するには、 **[...]** を選択します。 コンテキスト メニューが表示されます。 入力を集合体として確認することもできます。

![メトリック付きのジョブ ダイアグラム - パーティションの展開](./media/stream-analytics-job-diagram-with-metrics/stream-analytics-job-diagram-with-metrics-portal-3.png)

1 つのパーティションのみのメトリック グラフを確認するには、パーティションのノードを選択します。 メトリックはページの下部に表示されます。

![メトリック付きのジョブ ダイアグラム - 多くのメトリック](./media/stream-analytics-job-diagram-with-metrics/stream-analytics-job-diagram-with-metrics-portal-4.png)

パーティションの集合体のメトリック グラフを確認するには、集合体のノードを選択します。 次のグラフは、削除または調整されたイベントがなかったことを示しています。

![メトリック付きのジョブ ダイアグラム - グリッド](./media/stream-analytics-job-diagram-with-metrics/stream-analytics-job-diagram-with-metrics-portal-5.png)

メトリックの値と時刻の詳細を確認するには、グラフをポイントします。

![メトリック付きのジョブ ダイアグラム - ホバー](./media/stream-analytics-job-diagram-with-metrics/stream-analytics-job-diagram-with-metrics-portal-6.png)

## <a name="troubleshoot-by-using-metrics"></a>メトリックを使用したトラブルシューティング

**QueryLastProcessedTime** メトリックは、特定のステップでデータが受信された時間を示します。 トポロジを確認することで、出力プロセッサからさかのぼって、どのステップでデータが受信されていないかを参照できます。 あるステップでデータが受信されていなかった場合、その直前のクエリ ステップに移動します。 前のクエリ ステップに時間枠があるかどうかを確認し、データが出力されるのに十分な時間が経過したかどうかを確認します (時間枠は 1 時間単位となります)。
 
前のクエリ ステップが入力プロセッサである場合、入力メトリックを使用して、以下の目的の質問に答えるために役立てます。 これらは、ジョブが入力ソースからデータを取得しているかどうかを判断するうえで役に立ちます。 クエリがパーティション分割されている場合は、各パーティションを調べます。
 
### <a name="how-much-data-is-being-read"></a>読み取られているデータの量はどのくらいか。

*   **InputEventsSourcesTotal** は、読み取られたデータ ユニットの数です。 たとえば、BLOB の数です。
*   **InputEventsTotal** は、読み取られたイベントの数です。 このメトリックは、パーティションごとに使用できます。
*   **InputEventsInBytesTotal** は、読み取られたバイト数です。
*   **InputEventsLastArrivalTime** は、受信したイベントがエンキューされるたびにそのときの時刻で更新されます。
 
### <a name="is-time-moving-forward-if-actual-events-are-read-punctuation-might-not-be-issued"></a>時間が先に進んでいるかどうか。 実際のイベントが読み取られる場合、区切り記号は発行されません。

*   **InputEventsLastPunctuationTime** は、時間を常に先に進めるために、区切り記号が発行された時刻を示します。 区切り記号が発行されない場合は、データ フローをブロックすることができます。
 
### <a name="are-there-any-errors-in-the-input"></a>入力にエラーがあるかどうか。

*   **InputEventsEventDataNullTotal** は、null データが含まれたイベントの数です。
*   **InputEventsSerializerErrorsTotal** は、正常に逆シリアル化されないイベントの数です。
*   **InputEventsDegradedTotal** は、逆シリアル化以外に関する問題が発生したイベントの数です。
 
### <a name="are-events-being-dropped-or-adjusted"></a>イベントが削除または調整されているかどうか。

*   **InputEventsEarlyTotal** は、高基準値以前のアプリケーション タイムスタンプが付いたイベントの数です。
*   **InputEventsLateTotal** は、高基準値より後のアプリケーション タイムスタンプが付いたイベントの数です。
*   **InputEventsDroppedBeforeApplicationStartTimeTotal** は、ジョブの開始時刻の前に削除されたイベントの数です。
 
### <a name="are-we-falling-behind-in-reading-data"></a>データの読み取りを追跡しているかどうか。

*   **Input Events Backlogged (Total)** では、Event Hubs と Azure IoT Hub の入力用に読み取る必要がある追加のメッセージの数がわかります。 この数値が 0 より大きい場合は、データの取得後すぐに、ジョブがデータを処理できないことを意味します。 この場合、必要に応じて、ストリーミング ユニット数を増やしたり、ジョブを並列処理できるようにしたりします。 この処理の詳細については、[クエリの並列化に関するページ](./stream-analytics-parallelization.md)を参照してください。 


## <a name="get-help"></a>ヘルプの参照
さらにサポートが必要な場合は、[Azure Stream Analytics に関する Microsoft Q&A 質問ページ](/answers/topics/azure-stream-analytics.html)を参照してください。 

## <a name="next-steps"></a>次のステップ
* [Stream Analytics の概要](stream-analytics-introduction.md)
* [Stream Analytics の使用](stream-analytics-real-time-fraud-detection.md)
* [Stream Analytics ジョブのスケール設定](stream-analytics-scale-jobs.md)
* [Stream Analytics クエリ言語リファレンス](/stream-analytics-query/stream-analytics-query-language-reference)
* [Stream Analytics 管理 REST API リファレンス](/rest/api/streamanalytics/)