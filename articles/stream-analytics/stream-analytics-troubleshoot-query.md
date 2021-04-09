---
title: Azure Stream Analytics のクエリのトラブルシューティング
description: この記事では、Azure Stream Analytics ジョブのクエリのトラブルシューティングを行う方法について説明します。
author: sidramadoss
ms.author: sidram
ms.service: stream-analytics
ms.topic: troubleshooting
ms.date: 03/31/2020
ms.custom: seodec18
ms.openlocfilehash: 0dc36b817d5b5cdf731edecd64e1879c153d866a
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "98015132"
---
# <a name="troubleshoot-azure-stream-analytics-queries"></a>Azure Stream Analytics のクエリのトラブルシューティング

この記事では、Stream Analytics のクエリの開発に関する一般的な問題と、そのトラブルシューティングの方法について説明します。

この記事では、Azure Stream Analytics クエリの開発に関する一般的な問題、クエリの問題のトラブルシューティングの方法、および問題を修正する方法について説明します。 多くのトラブルシューティングの手順では、Stream Analytics ジョブに対してリソース ログを有効にする必要があります。 リソース ログが有効になっていない場合は、「[リソース ログを使用した Azure Stream Analytics のトラブルシューティング](stream-analytics-job-diagnostic-logs.md)」を参照してください。

## <a name="query-is-not-producing-expected-output"></a>クエリが予想される出力を生成しない

1.  ローカルでテストしてエラーを調査します。

    - Azure portal の **[クエリ]** タブで **[テスト]** を選択します。 ダウンロードしたサンプル データを使用して[クエリをテスト](stream-analytics-test-query.md)します。 すべてのエラーを調査し、修正を試みます。   
    - また、Visual Studio の Azure Stream Analytics ツール、または [Visual Studio Code](visual-studio-code-local-run-live-input.md) を使用して、[クエリをローカルでテスト](stream-analytics-live-data-local-testing.md)することもできます。 

2.  Visual Studio Code の Azure Stream Analytics ツールで、[ジョブ ダイアグラムを使用して段階を追ってローカルでクエリをデバッグ](debug-locally-using-job-diagram-vs-code.md)します。 ジョブ ダイアグラムには、入力ソース (イベント ハブ、IoT Hub など) のデータが複数のクエリ手順を介して最終的に出力シンクまでどのように流れるかが示されます。 各クエリ ステップは、WITH ステートメントを使用してスクリプトに定義された一時的結果セットにマップされます。 各中間結果セット内のデータとメトリックを表示して、問題の原因を見つけることができます。

    ![ジョブ ダイアグラムのプレビュー結果](./media/debug-locally-using-job-diagram-vs-code/preview-result.png)

3.  [**Timestamp By**](/stream-analytics-query/timestamp-by-azure-stream-analytics) を使用する場合は、イベントのタイムスタンプが [ジョブの開始時刻](./stream-analytics-time-handling.md)より後であることを確認します。

4.  よくある次のような問題を解消する。
    - クエリ内の [**WHERE**](/stream-analytics-query/where-azure-stream-analytics) 句がイベントをすべて除外してしまっている。この場合、出力が生成されません。
    - [**CAST**](/stream-analytics-query/cast-azure-stream-analytics) 関数が失敗したため、ジョブが失敗する。 型キャスト エラーを回避するには、代わりに [**TRY_CAST**](/stream-analytics-query/try-cast-azure-stream-analytics) を使用します。
    - ウィンドウ関数を使用している場合に、ウィンドウ時間が終わっていない。ウィンドウ時間が完了し、クエリの出力が表示されるのを待つ必要があります。
    - イベントのタイムスタンプがジョブの開始時刻よりも前になっている。イベントがドロップされてしまいます。
    - [**JOIN**](/stream-analytics-query/join-azure-stream-analytics) 条件が一致しない。 どれとも一致しない場合は、出力は 0 個になります。

5.  イベント順序ポリシーが期待どおりに構成されていることを確認します。 **[設定]** に移動し、[ **[イベント順序]**](./stream-analytics-time-handling.md) を選択します。 このポリシーは、 **[テスト]** ボタンを使用してクエリをテストする場合には適用 "*されません*"。 この結果が、ブラウザーでテストする場合と、運用環境でジョブを実行する場合の相違点の 1 つです。 

6. アクティビティとリソース ログを使用したデバッグ:
    - [アクティビティ ログ](../azure-resource-manager/management/view-activity-logs.md)を使用してフィルター処理を行い、エラーを特定してデバッグします。
    - [ジョブのリソース ログ](stream-analytics-job-diagnostic-logs.md)を使用してエラーを特定し、デバッグします。

## <a name="resource-utilization-is-high"></a>リソース使用率が高い

Azure Stream Analytics で並列処理を活用していることを確認します。 入力パーティションの構成と分析クエリ定義のチューニングによって、Stream Analytics ジョブの[クエリ並列処理を使用してスケーリングする](stream-analytics-parallelization.md)ことをお勧めします。

リソース使用率が常に 80% を超え、透かしの遅延が増加し、バックログされたイベントの数が増加している場合は、ストリーミング ユニットを増やすことを検討してください。 使用率が高い場合は、最大数に近い割り当てリソースがジョブによって使用されていることを示します。

## <a name="debug-queries-progressively"></a>クエリを段階的にデバッグする

リアルタイムのデータ処理では、クエリの実行中にデータの状況を把握することが役に立つ場合があります。 これは、Visual Studio のジョブ ダイアグラムを使用して確認できます。 Visual Studio がない場合は、中間データを出力するための追加の手順を実行できます。

Azure Stream Analytics ジョブの入力またはステップは複数回読み取ることができるため、追加の SELECT INTO ステートメントを記述することができます。 これを実行すると、中間データがストレージに出力され、データの正確性を確認できるようになります。これは、プログラムをデバッグする際に "*watch 変数*" によって行われる確認とまったく同じです。

Azure Stream Analytics ジョブの次のサンプル クエリには、1 つのストリーム入力と 2 つの参照データ入力があり、Azure Table Storage に出力が行われます。 このクエリはイベント ハブと 2 つの参照 BLOB からのデータを結合し、名前とカテゴリの情報を取得します。

![Stream Analytics の SELECT INTO クエリの例](./media/stream-analytics-select-into/stream-analytics-select-into-query1.png)

ジョブが実行中なのに出力でイベントが生成されていないことに注意してください。 次に示す **[監視]** タイルでは、入力からデータが生成中であることがわかります。しかし、**JOIN** のどのステップが原因ですべてのイベントが欠落したのかはわかりません。

![Stream Analytics の [監視] タイル](./media/stream-analytics-select-into/stream-analytics-select-into-monitor.png)

この状況では、いくつかの SELECT INTO ステートメントを追加して、JOIN の中間結果と入力から読み取られたデータの "ログを記録" することができます。

この例では、2 つの "一時的な出力" を新しく追加しました。 これらの出力には任意のシンクを使用してかまいません。 ここでは例として Azure Storage を使用します。

![Stream Analytics クエリへの SELECT INTO ステートメントの追加](./media/stream-analytics-select-into/stream-analytics-select-into-outputs.png)

クエリは次のように書き換えることができます。

![書き換えられた SELECT INTO Stream Analytics クエリ](./media/stream-analytics-select-into/stream-analytics-select-into-query2.png)

もう一度ジョブを開始して、数分間実行します。 temp1 と temp2 の各クエリによって、Visual Studio Cloud Explorer で次のテーブルが生成されます。

**temp1 テーブル**
![SELECT INTO temp1 テーブル Stream Analytics クエリ](./media/stream-analytics-select-into/stream-analytics-select-into-temp-table-1.png)

**temp2 テーブル**
![SELECT INTO temp2 テーブル Stream Analytics クエリ](./media/stream-analytics-select-into/stream-analytics-select-into-temp-table-2.png)

ご覧のとおり、temp1 と temp2 のどちらにもデータがあり、temp2 では名前列が正しく入力されています。 しかし出力には依然としてデータがなく、何らかの問題が発生していることがわかります。

![データのない SELECT INTO output1 テーブル Stream Analytics クエリ](./media/stream-analytics-select-into/stream-analytics-select-into-out-table-1.png)

データをサンプリングすることで、2 番目の JOIN に問題があることがほぼ確実にわかります。 BLOB から参照データをダウンロードして確認できます。

![SELECT INTO ref テーブル Stream Analytics クエリ](./media/stream-analytics-select-into/stream-analytics-select-into-ref-table-1.png)

ご覧のとおり、この参照データの GUID の形式が temp2 の "from" 列の形式と異なります。 これが、データが想定どおりに output1 に届かなかった原因です。

データ形式を修正して参照 BLOB にアップロードし、やり直すことができます。

![SELECT INTO temp テーブル Stream Analytics クエリ](./media/stream-analytics-select-into/stream-analytics-select-into-ref-table-2.png)

今度は、出力のデータが想定どおりに書式設定されて入力されます。

![SELECT INTO final テーブル Stream Analytics クエリ](./media/stream-analytics-select-into/stream-analytics-select-into-final-table.png)

## <a name="get-help"></a>ヘルプの参照

詳細については、[Azure Stream Analytics に関する Microsoft Q&A 質問ページ](/answers/topics/azure-stream-analytics.html)を参照してください。

## <a name="next-steps"></a>次のステップ

* [Azure Stream Analytics の概要](stream-analytics-introduction.md)
* [Azure Stream Analytics の使用](stream-analytics-real-time-fraud-detection.md)
* [Azure Stream Analytics ジョブのスケーリング](stream-analytics-scale-jobs.md)
* [Stream Analytics Query Language Reference (Stream Analytics クエリ言語リファレンス)](/stream-analytics-query/stream-analytics-query-language-reference)
* [Azure Stream Analytics management REST API reference (Azure ストリーム分析の管理 REST API リファレンス)](/rest/api/streamanalytics/)
