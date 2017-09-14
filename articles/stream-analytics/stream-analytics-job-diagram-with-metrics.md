---
title: "ジョブ ダイアグラムを使用した Azure Stream Analytics のデータ主導型デバッグ | Microsoft Docs"
description: "ジョブ ダイアグラムとメトリックを使用して Stream Analytics ジョブのトラブルシューティングを行います。"
keywords: 
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
ms.date: 05/01/2017
ms.author: samacha
ms.translationtype: HT
ms.sourcegitcommit: 8351217a29af20a10c64feba8ccd015702ff1b4e
ms.openlocfilehash: 5b689c07bf8baa531c7a50ca50ed5140c1787e7b
ms.contentlocale: ja-jp
ms.lasthandoff: 08/29/2017

---

# <a name="data-driven-debugging-by-using-the-job-diagram"></a>ジョブ ダイアグラムを使用したデータ主導型デバッグ

Azure Portal の **[監視]** ブレードのジョブ ダイアグラムは、ジョブ パイプラインの視覚化に役立ちます。 ここには、入力、出力、クエリのステップが表示されます。 ジョブ ダイアグラムを使用すると、各ステップのメトリックを確認して、問題のトラブルシューティングを行う際に問題の原因をより迅速に分離できます。

## <a name="using-the-job-diagram"></a>ジョブ ダイアグラムの使用

Stream Analytics ジョブの使用中に、Azure Portal の **[サポート + トラブルシューティング]** の下にある **[ジョブ ダイアグラム]** を選択します。

![メトリック付きのジョブ ダイアグラム - 場所](./media/stream-analytics-job-diagram-with-metrics/stream-analytics-job-diagram-with-metrics-portal-1.png)

各クエリ ステップを選択すると、クエリ編集ウィンドウに対応するセクションが表示されます。 ステップのメトリック グラフは、ページ下部のウィンドウに表示されます。

![メトリック付きのジョブ ダイアグラム - 基本的なジョブ](./media/stream-analytics-job-diagram-with-metrics/stream-analytics-job-diagram-with-metrics-portal-2.png)

Azure Event Hubs の入力をパーティション単位で確認するには、**[...]** を選択します。 コンテキスト メニューが表示されます。 入力を集合体として確認することもできます。

![メトリック付きのジョブ ダイアグラム - パーティションの展開](./media/stream-analytics-job-diagram-with-metrics/stream-analytics-job-diagram-with-metrics-portal-3.png)

1 つのパーティションのみのメトリック グラフを確認するには、パーティションのノードを選択します。 メトリックはページの下部に表示されます。

![メトリック付きのジョブ ダイアグラム - 多くのメトリック](./media/stream-analytics-job-diagram-with-metrics/stream-analytics-job-diagram-with-metrics-portal-4.png)

パーティションの集合体のメトリック グラフを確認するには、集合体のノードを選択します。 次のグラフは、削除または調整されたイベントがなかったことを示しています。

![メトリック付きのジョブ ダイアグラム - グリッド](./media/stream-analytics-job-diagram-with-metrics/stream-analytics-job-diagram-with-metrics-portal-5.png)

メトリックの値と時刻の詳細を確認するには、グラフをポイントします。

![メトリック付きのジョブ ダイアグラム - ホバー](./media/stream-analytics-job-diagram-with-metrics/stream-analytics-job-diagram-with-metrics-portal-6.png)

## <a name="troubleshoot-by-using-metrics"></a>メトリックを使用したトラブルシューティング

**QueryLastProcessedTime** メトリックは、特定のステップでデータが受信された時間を示します。 トポロジを確認することで、出力プロセッサからさかのぼって、どのステップでデータが受信されていないかを参照できます。 あるステップでデータが受信されていなかった場合、その直前のクエリ ステップに移動します。 前のクエリ ステップに時間枠があるかどうかを確認し、データが出力されるのに十分な時間が経過したかどうかを確認します  (時間枠は 1 時間単位となります)。
 
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

*   **InputEventsSourcesBackloggedTotal** では、Event Hubs と Azure IoT Hub の入力用に読み取る必要がある追加のメッセージの数がわかります。


## <a name="get-help"></a>問い合わせ
さらにサポートが必要な場合は、[Azure Stream Analytics フォーラム](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics)を参照してください。

## <a name="next-steps"></a>次のステップ
* [Stream Analytics の概要](stream-analytics-introduction.md)
* [Stream Analytics の使用](stream-analytics-real-time-fraud-detection.md)
* [Stream Analytics ジョブのスケール設定](stream-analytics-scale-jobs.md)
* [Stream Analytics クエリ言語リファレンス](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Stream Analytics 管理 REST API リファレンス](https://msdn.microsoft.com/library/azure/dn835031.aspx)

