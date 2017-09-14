---
title: "Azure Stream Analytics のトラブルシューティング ガイド | Microsoft Docs"
description: "Stream Analytics ジョブのトラブルシューティング方法"
keywords: "トラブルシューティング ガイド"
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
ms.openlocfilehash: dcff312e4a282b15e76ea32aadb1981a496a2446
ms.contentlocale: ja-jp
ms.lasthandoff: 08/29/2017

---

# <a name="troubleshooting-guide-for-azure-stream-analytics"></a>Azure Stream Analytics のトラブルシューティング ガイド

Azure Stream Analytics のトラブルシューティングは、一見複雑な作業に見えるかもしれません。 このガイドは、そのプロセスを合理化し、入力、出力、クエリ、機能に関して推測に頼った作業をなくすために、多くのユーザーと協力して作成されました。

## <a name="troubleshoot-your-stream-analytics-job"></a>Stream Analytics ジョブのトラブルシューティング

Stream Analytics ジョブのトラブルシューティングで最善の結果を得るには、次のガイドラインを使用します。

1.  接続をテストする。
    - 各入力と出力の **[接続のテスト]** ボタンを使用して、入力と出力への接続を確認します。

2.  入力データを確認する。
    - 入力データがイベント ハブに送信されていることを確認するために、[Service Bus エクスプローラー](https://code.msdn.microsoft.com/windowsapps/Service-Bus-Explorer-f2abca5a)を使用して、Azure イベント ハブに接続します (イベント ハブの入力を使用している場合)。  
    - 各入力の [**[サンプル データ]**](stream-analytics-sample-data-input.md) ボタンを使用して、入力のサンプル データをダウンロードします。
    - サンプル データを調べて、データの構造 (スキーマおよび[データ型](https://msdn.microsoft.com/library/azure/dn835065.aspx)) を理解します。

3.  クエリをテストする。
    - **[クエリ]** タブで、**[テスト]** ボタンを使用してクエリをテストし、ダウンロードしたサンプル データを使用して[クエリをテストします](stream-analytics-test-query.md)。 すべてのエラーを調査し、修正を試みます。
    - [**Timestamp By**](https://msdn.microsoft.com/library/azure/mt573293.aspx) を使用する場合は、イベントのタイムスタンプが[ジョブの開始時刻](stream-analytics-out-of-order-and-late-events.md)より後であることを確認します。

4.  クエリをデバッグする。
    - 単純な SELECT ステートメントから、ステップを使用したさらに複雑な集計へと、クエリを徐々に構築し直します。 段階的にクエリ ロジックを構築するには、[WITH](https://msdn.microsoft.com/library/azure/dn835049.aspx) 句を使用します。
    - [SELECT INTO](stream-analytics-select-into.md) を使用して、クエリ ステップをデバッグします。

5.  よくある次のような問題を解消する。
    - クエリ内の [**WHERE**](https://msdn.microsoft.com/library/azure/dn835048.aspx) 句がイベントをすべて除外してしまっている。この場合、出力が生成されません。
    - ウィンドウ関数を使用している場合に、ウィンドウ時間が終わっていない。ウィンドウ時間が完了し、クエリの出力が表示されるのを待つ必要があります。
    - イベントのタイムスタンプがジョブの開始時刻よりも前になっている。この状態だと、イベントがドロップされてしまいます。

6.  イベントの順序を使用する。
    - 以上のすべてのステップが適切に機能している場合は、**[設定]** ブレードに移動し、[**[イベント順序]**](stream-analytics-out-of-order-and-late-events.md) を選択します。 このポリシーが自分のシナリオに適するように構成されていることを確認します。 このポリシーは、**[テスト]** ボタンを使用してクエリをテストする場合には適用 "*されません*"。 この結果が、ブラウザーでテストする場合と、運用環境でジョブを実行する場合の相違点の 1 つです。

7.  メトリックを使用してデバッグする。
    - 予想していた時間 (クエリに基づく) を過ぎても出力を取得できない場合は、次のことを試してください。
        - **[監視]** タブで[**監視メトリック**](stream-analytics-monitoring.md)を確認します。値が集計される関係上、メトリックは数分間遅延します。
            - 入力イベントが 0 より大きい場合、ジョブは入力データを読み取ることができます。 入力イベントが 0 以下の場合、次のことを行います。
                - データ ソースに有効なデータがあるかどうか確認するために、[Service Bus エクスプローラー](https://code.msdn.microsoft.com/windowsapps/Service-Bus-Explorer-f2abca5a)を使用する。 この確認は、ジョブがイベント ハブを入力として使用している場合に必要になります。
                - データのシリアル化形式とデータのエンコードが想定どおりであるかどうかを確認する。
                - ジョブがイベント ハブを使用している場合は、メッセージ本文が *Null* であるかどうかを確認する。
            - データ変換エラーが 0 より大きく、上昇している場合は、次の内容を意味する場合があります。
                - ジョブで、イベントを逆シリアル化できていない可能性がある。
                - イベント スキーマが、クエリ内のイベントの定義済みのスキーマまたは想定しているスキーマと一致していない可能性がある。
                - イベントの一部のフィールドのデータ型が、想定どおりでない可能性がある。
            - ランタイム エラーが 0 より大きい場合、ジョブはデータを受信できていますが、クエリの処理中にエラーが発生しています。
                - [監査ログ](../azure-resource-manager/resource-group-audit.md)を参照して *Failed* ステータスでフィルター処理すると、エラーを確認できます。
            - 入力イベントが 0 より大きく、出力イベントが 0 である場合、次のいずれかの内容を意味します。
                - クエリの処理結果で、出力イベントが 0 個だった。
                - イベントまたはそのフィールドの形式が不適切なため、クエリ処理の実行後に出力が 0 個になった。
                - 接続または認証が原因で、ジョブがデータを[出力シンク](stream-analytics-select-into.md)にプッシュできない。
        - クエリ ロジックですべてのイベントを除外した場合を除き、以上のすべてのエラーについて、操作ログ メッセージから詳細を確認できます (発生中のエラーを含みます)。 複数のイベントの処理でエラーが発生した場合、Stream Analytics では、10 分間に発生した同じ種類のエラーに関するエラー メッセージを最初の 3 回分だけ、操作ログに記録します。 4 回目以降は非表示となり、"エラーが短期間に大量に発生したため、非表示になっています" という意味のメッセージが表示されます。

8. 監査ログと診断ログを使用してデバッグする。
    - [監査ログ](../azure-resource-manager/resource-group-audit.md)を使用してフィルター処理を行い、エラーを特定してデバッグします。
    - [ジョブの診断ログ](stream-analytics-job-diagnostic-logs.md)を使用してエラーを特定し、デバッグします。

9. 出力を確認する。
    - ジョブの状態が "*実行中*" のときは、クエリで規定されている時間に応じて、シンク データ ソースで出力を確認できます。
    - 特定の出力の種類に該当する出力が表示されない場合は、Azure BLOB などの比較的複雑でない出力タイプにリダイレクトします。 ストレージ エクスプローラーを使用して、出力が表示されるかどうかを確認します。 さらに、出力でのスロットルの制限値がデータの受信を妨げていないかどうかを確認します。

10. ジョブ ダイアグラムとメトリックでデータ フロー分析を使用する。
    - データ フローを分析して問題を特定するには、[メトリック付きのジョブ ダイアグラム](stream-analytics-job-diagram-with-metrics.md)を使用します。

11. サポート ケースを開く。
    - 最後に、これらすべてが失敗した場合は、ジョブを含むサブスクリプション ID を使用して、Microsoft サポート ケースを開きます。

## <a name="get-help"></a>問い合わせ

さらにサポートが必要な場合は、 [Azure Stream Analytics フォーラム](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics)を参照してください。

## <a name="next-steps"></a>次のステップ

* [Azure Stream Analytics の概要](stream-analytics-introduction.md)
* [Azure Stream Analytics の使用](stream-analytics-real-time-fraud-detection.md)
* [Azure Stream Analytics ジョブのスケーリング](stream-analytics-scale-jobs.md)
* [Stream Analytics Query Language Reference (Stream Analytics クエリ言語リファレンス)](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Azure Stream Analytics management REST API reference (Azure ストリーム分析の管理 REST API リファレンス)](https://msdn.microsoft.com/library/azure/dn835031.aspx)

