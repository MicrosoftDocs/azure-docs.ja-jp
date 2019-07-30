---
title: Azure Stream Analytics の出力のトラブルシューティング
description: この記事では、Azure Stream Analytics ジョブの出力接続のトラブルシューティングを行う方法について説明します。
services: stream-analytics
author: sidram
ms.author: sidram
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 12/07/2018
ms.custom: seodec18
ms.openlocfilehash: a07ac40ad3adda486b5216e83d683e00ec93265d
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/07/2019
ms.locfileid: "67620784"
---
# <a name="troubleshoot-azure-stream-analytics-outputs"></a>Azure Stream Analytics の出力のトラブルシューティング

このページでは、出力接続での一般的な問題とそのトラブルシューティングおよび対処方法について説明します。

## <a name="output-not-produced-by-job"></a>出力がジョブによって生成されない 
1.  各出力の **[テスト接続]** ボタンを使用して、出力への接続性を確認します。

2.  **[監視]** タブで[**監視メトリック**](stream-analytics-monitoring.md)を確認します。値が集計される関係上、メトリックは数分間遅延します。
    - 入力イベントが 0 より大きい場合、ジョブは入力データを読み取ることができます。 入力イベントが 0 以下の場合、次のことを行います。
      - データ ソースに有効なデータがあるかどうか確認するために、[Service Bus エクスプローラー](https://code.msdn.microsoft.com/windowsapps/Service-Bus-Explorer-f2abca5a)を使用する。 この確認は、ジョブがイベント ハブを入力として使用している場合に必要になります。
      - データのシリアル化形式とデータのエンコードが想定どおりであるかどうかを確認する。
      - ジョブがイベント ハブを使用している場合は、メッセージ本文が *Null* であるかどうかを確認する。
      
    - データ変換エラーが 0 より大きく、上昇している場合は、次の内容を意味する場合があります。
      - 出力イベントは、ターゲット シンクのスキーマに準拠しません。 
      - イベント スキーマが、クエリ内のイベントの定義済みのスキーマまたは想定しているスキーマと一致していない可能性がある。
      - イベントの一部のフィールドのデータ型が、想定どおりでない可能性がある。
      
    - ランタイム エラーが 0 より大きい場合、ジョブはデータを受信できていますが、クエリの処理中にエラーが発生しています。
      - [監査ログ](../azure-resource-manager/resource-group-audit.md)を参照して *Failed* ステータスでフィルター処理すると、エラーを確認できます。
      
    - 入力イベントが 0 より大きく、出力イベントが 0 である場合、次のいずれかの内容を意味します。
      - クエリの処理結果で、出力イベントが 0 個だった。
      - イベントまたはそのフィールドの形式が不適切なため、クエリ処理の実行後に出力が 0 個になった。
      - 接続または認証が原因で、ジョブがデータを出力シンクにプッシュできない。
      
    - クエリ ロジックですべてのイベントを除外した場合を除き、以上のすべてのエラーについて、操作ログ メッセージから詳細を確認できます (発生中のエラーを含みます)。 複数のイベントの処理でエラーが発生した場合、Stream Analytics では、10 分間に発生した同じ種類のエラーに関するエラー メッセージを最初の 3 回分だけ、操作ログに記録します。 4 回目以降は非表示となり、"エラーが短期間に大量に発生したため、非表示になっています" という意味のメッセージが表示されます。
    
## <a name="job-output-is-delayed"></a>ジョブの出力が遅延される

### <a name="first-output-is-delayed"></a>最初の出力が遅延される
Stream Analytics ジョブが開始されると、入力イベントが読み取られますが、特定の状況で生成された出力に遅延が発生する場合があります。

テンポラル クエリ要素の時刻の値が大きい場合、出力の遅延が引き起こされる場合があります。 大きい時間枠で正しい出力を生成するために、ストリーミング ジョブは使用可能な最新時刻 (最大 7 日前まで) からデータを読み取って時間枠を設定することによって起動します。 その間、未処理の入力イベントのキャッチアップの読み取りが完了するまで、出力は生成されません。 この問題は、システムがストリーミング ジョブをアップグレードし、それに伴ってジョブが再起動されるときに発生する可能性があります。 このようなアップグレードは通常、2 か月に 1 度行われます。 

そのため、Stream Analytics クエリを設計するときは慎重に行ってください。 ジョブのクエリ構文に大きな時間枠 (数時間以上、最大 7 日間) を使用する場合は、ジョブが起動または再起動されるときの最初の出力で遅延が発生する可能性があります。  

最初の出力におけるこの種の遅延を軽減する 1 つの方策は、クエリ並列処理の技術 (データのパーティション化) を使用するか、またはより多くのストリーミング ユニットを追加して、ジョブが遅れなくなるまでスループットを改善することです。  詳細については、[Stream Analytics ジョブを作成する場合の考慮事項](stream-analytics-concepts-checkpoint-replay.md)に関するページをご覧ください。

以下の要因は、生成された最初の出力のタイムラインに影響を及ぼします。

1. ウィンドウ集計 (タンブリング ウィンドウ、ホッピング ウィンドウ、スライディング ウィンドウの GROUP BY) の使用
   - タンブリング ウィンドウまたはホッピング ウィンドウ集計の場合、ウィンドウ時間枠の最後に結果が生成されます。 
   - スライディング ウィンドウの場合、イベントがスライディング ウィンドウに出入りするときに、結果が生成されます。 
   - 大きなウィンドウ サイズ (1 時間を超える) を使用することを計画している場合は、より頻繁に出力を確認できるように、ホッピング ウィンドウまたはスライディング ウィンドウを選択することをお勧めします。

2. テンポラル結合 (DATEDIFF を使用した JOIN) の使用
   - 一致するイベントの両端を受け取ると、すぐに一致が生成されます。
   - 一致 (LEFT OUTER JOIN) が欠如しているデータは、左側の各イベントに関する DATEDIFF ウィンドウの最後に生成されます。

3. テンポラル分析関数 (LIMIT DURATION を使用した ISFIRST、LAST、および LAG) の使用
   - 分析関数の場合、イベントごとに出力が生成され、遅延はありません。

### <a name="output-falls-behind"></a>出力が遅れる
ジョブの通常操作中に、ジョブの出力が遅れている (待機時間がより長くなる) ことに気付いた場合は、以下の要因を検証することで、根本原因を特定できます。
- ダウンストリーム シンクが調整されているか
- アップストリーム ソースが調整されているか
- クエリの処理ロジックがコンピューティング集約型かどうか

これらの詳細を確認するには、Azure Portal で、ストリーミング ジョブを選択して、 **[ジョブ ダイアグラム]** を選択します。 各入力には、パーティションごとのバックログ イベント メトリックがあります。 バックログ イベント メトリックが継続的に増加している場合、システム リソースが制約を受けていることを示す指標になります。 出力のシンク調整または高使用率の CPU が原因になっている可能性があります。 ジョブ ダイアグラムの使用に関する詳細については、「[ジョブ ダイアグラムを使用したデータ主導型デバッグ](stream-analytics-job-diagram-with-metrics.md)」をご覧ください。

## <a name="key-violation-warning-with-azure-sql-database-output"></a>Azure SQL Database の出力でのキー違反の警告

Azure SQL データベースを Stream Analytics ジョブへの出力として構成すると、宛先テーブルにレコードが一括挿入されます。 通常、Azure Stream Analytics では、出力シンクに対して[最低 1 回の配信](https://docs.microsoft.com/stream-analytics-query/event-delivery-guarantees-azure-stream-analytics)が保証されますが、SQL テーブルで一意制約が定義されている場合は、[厳密に 1 回だけの配信を保証]( https://blogs.msdn.microsoft.com/streamanalytics/2017/01/13/how-to-achieve-exactly-once-delivery-for-sql-output/)することもできます。 

SQL テーブルに一意キー制約が設定された後に、SQL テーブルに挿入される重複したレコードがあると、Azure Stream Analytics によって重複したレコードは削除されます。 データはバッチに分割され、単一の重複レコードが見つかるでバッチの再帰的な挿入が行われます。 ストリーミング ジョブに多数の重複行が含まれる場合は、この分割と挿入の処理で重複を 1 つずつ無視する必要があり、効率が低下して時間がかかってしまいます。 過去 1 時間以内のアクティビティ ログにキー違反の警告メッセージが複数ある場合は、SQL 出力によってジョブ全体の速度が低下している可能性があります。 

この問題を解決するには、IGNORE_DUP_KEY オプションを有効にして、キー違反の原因となっている[インデックスを構成する]( https://docs.microsoft.com/sql/t-sql/statements/create-index-transact-sql)必要があります。 このオプションを有効にすると、一括挿入時の重複値は SQL によって無視され、SQL Azure ではエラーの代わりに警告メッセージが生成されます。 Azure Stream Analytics では、主キー違反エラーが生成されなくなります。

IGNORE_DUP_KEY を構成する際には、一部のインデックスに関する次の考慮事項に注意してください。

* ALTER INDEX を使用する主キーや一意制約については、IGNORE_DUP_KEY を設定することはできません。インデックスをドロップし、再作成する必要があります。  
* 一意なインデックスについては、IGNORE_DUP_KEY オプションを設定できます。このインデックスは、主キー/一意制約とは異なり、CREATE INDEX または INDEX 定義を使用して作成されます。  
* IGNORE_DUP_KEY は、列ストア インデックスには適用されません (このようなインデックスに対して一意性を強制することはできないため)。  

## <a name="column-names-are-lower-cased-by-azure-stream-analytics"></a>列名は Azure Stream Analytics によって小文字に変換されます
元の互換性レベル (1.0) を使用していたとき、Azure Stream Analytics は列名を小文字に変更するために使用していました。 この動作は、以降の互換性レベルで修正されました。 大文字と小文字を保持するためには、お客様が互換性レベル 1.1 以降に移行することをお勧めします。 詳細については、「[Azure Stream Analytics ジョブの互換性レベル](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-compatibility-level)」を参照してください。


## <a name="get-help"></a>問い合わせ

さらにサポートが必要な場合は、 [Azure Stream Analytics フォーラム](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics)を参照してください。

## <a name="next-steps"></a>次のステップ

* [Azure Stream Analytics の概要](stream-analytics-introduction.md)
* [Azure Stream Analytics の使用](stream-analytics-real-time-fraud-detection.md)
* [Azure Stream Analytics ジョブのスケーリング](stream-analytics-scale-jobs.md)
* [Stream Analytics Query Language Reference (Stream Analytics クエリ言語リファレンス)](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference)
* [Azure Stream Analytics management REST API reference (Azure ストリーム分析の管理 REST API リファレンス)](https://msdn.microsoft.com/library/azure/dn835031.aspx)
