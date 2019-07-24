---
title: Azure Stream Analytics のクエリのトラブルシューティング
description: この記事では、Azure Stream Analytics ジョブのクエリのトラブルシューティングを行う方法について説明します。
services: stream-analytics
author: sidram
ms.author: sidram
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 12/07/2018
ms.custom: seodec18
ms.openlocfilehash: 7a1e440a8dc8f518e272df9e126771df54390ed5
ms.sourcegitcommit: efcd039e5e3de3149c9de7296c57566e0f88b106
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/10/2018
ms.locfileid: "53161986"
---
# <a name="troubleshoot-azure-stream-analytics-queries"></a>Azure Stream Analytics のクエリのトラブルシューティング

この記事では、Stream Analytics のクエリの開発に関する一般的な問題と、そのトラブルシューティングの方法について説明します。

## <a name="query-is-not-producing-expected-output"></a>クエリが予想される出力を生成しない 
1.  ローカルでテストしてエラーを調査します。
    - **[クエリ]** タブで **[テスト]** を選択します。 ダウンロードしたサンプル データを使用して[クエリをテスト](stream-analytics-test-query.md)します。 すべてのエラーを調査し、修正を試みます。   
    - また、Visual Studio 用の Stream Analytics ツールを使用して、[ライブ入力でクエリを直接テスト](stream-analytics-live-data-local-testing.md)することもできます。

2.  [**Timestamp By**](https://msdn.microsoft.com/library/azure/mt573293.aspx) を使用する場合は、イベントのタイムスタンプが[ジョブの開始時刻](stream-analytics-out-of-order-and-late-events.md)より後であることを確認します。

3.  よくある次のような問題を解消する。
    - クエリ内の [**WHERE**](https://msdn.microsoft.com/library/azure/dn835048.aspx) 句がイベントをすべて除外してしまっている。この場合、出力が生成されません。
    - [**CAST**](https://msdn.microsoft.com/azure/stream-analytics/reference/cast-azure-stream-analytics) 関数が失敗したため、ジョブが失敗する。 型キャスト エラーを回避するには、代わりに [**TRY_CAST**](https://msdn.microsoft.com/azure/stream-analytics/reference/try-cast-azure-stream-analytics) を使用します。
    - ウィンドウ関数を使用している場合に、ウィンドウ時間が終わっていない。ウィンドウ時間が完了し、クエリの出力が表示されるのを待つ必要があります。
    - イベントのタイムスタンプがジョブの開始時刻よりも前になっている。この状態だと、イベントがドロップされてしまいます。

4.  イベント順序ポリシーが期待どおりに構成されていることを確認します。 **[設定]** に移動し、[**[イベント順序]**](stream-analytics-out-of-order-and-late-events.md) を選択します。 このポリシーは、**[テスト]** ボタンを使用してクエリをテストする場合には適用 "*されません*"。 この結果が、ブラウザーでテストする場合と、運用環境でジョブを実行する場合の相違点の 1 つです。 

5. 監査ログと診断ログを使用してデバッグする。
    - [監査ログ](../azure-resource-manager/resource-group-audit.md)を使用してフィルター処理を行い、エラーを特定してデバッグします。
    - [ジョブの診断ログ](stream-analytics-job-diagnostic-logs.md)を使用してエラーを特定し、デバッグします。

## <a name="job-is-consuming-too-many-streaming-units"></a>ジョブで消費されるストリーミング ユニットが多すぎる
Azure Stream Analytics で並列処理を活用していることを確認します。 入力パーティションの構成と分析クエリ定義のチューニングによって、Stream Analytics ジョブの[クエリ並列処理を使用してスケーリングする](stream-analytics-parallelization.md)ことをお勧めします。

## <a name="debug-queries-progressively"></a>クエリを段階的にデバッグする

リアルタイムのデータ処理では、クエリの実行中にデータの状況を把握することが役に立つ場合があります。 Azure Stream Analytics ジョブの入力またはステップは複数回読み取ることができるため、追加の SELECT INTO ステートメントを記述することができます。 これを実行すると、中間データがストレージに出力され、データの正確性を確認できるようになります。これは、プログラムをデバッグする際に "*watch 変数*" によって行われる確認とまったく同じです。

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

## <a name="get-help"></a>問い合わせ

さらにサポートが必要な場合は、 [Azure Stream Analytics フォーラム](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics)を参照してください。

## <a name="next-steps"></a>次のステップ

* [Azure Stream Analytics の概要](stream-analytics-introduction.md)
* [Azure Stream Analytics の使用](stream-analytics-real-time-fraud-detection.md)
* [Azure Stream Analytics ジョブのスケーリング](stream-analytics-scale-jobs.md)
* [Stream Analytics Query Language Reference (Stream Analytics クエリ言語リファレンス)](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Azure Stream Analytics management REST API reference (Azure ストリーム分析の管理 REST API リファレンス)](https://msdn.microsoft.com/library/azure/dn835031.aspx)