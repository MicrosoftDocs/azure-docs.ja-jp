---
title: Azure Stream Analytics の出力のトラブルシューティング
description: この記事では、Azure Stream Analytics ジョブの出力接続のトラブルシューティングを行う方法について説明します。
author: sidramadoss
ms.author: sidram
ms.service: stream-analytics
ms.topic: troubleshooting
ms.date: 10/05/2020
ms.custom: seodec18
ms.openlocfilehash: 02a3a7ad73bf0434a215c5ab7a6e89c299e9518b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98019858"
---
# <a name="troubleshoot-azure-stream-analytics-outputs"></a>Azure Stream Analytics の出力のトラブルシューティング

この記事では、Azure Stream Analytics の出力接続に関する一般的な問題と、出力の問題のトラブルシューティング方法について説明します。 トラブルシューティング手順の多くでは、Stream Analytics ジョブに対してリソース ログや他の診断ログを有効にする必要があります。 リソース ログが有効になっていない場合は、「[診断ログを使用した Azure Stream Analytics のトラブルシューティング](stream-analytics-job-diagnostic-logs.md)」を参照してください。

## <a name="the-job-doesnt-produce-output"></a>ジョブで出力が生成されない

1. 各出力の **[テスト接続]** ボタンを使用して、出力への接続性を確認します。
1. **[監視]** タブの [監視メトリック](stream-analytics-monitoring.md)を確認します。値が集計される関係上、メトリックは数分間遅延します。

   * **[入力イベント]** の値が 0 より大きい場合、ジョブでは入力データを読み取ることができます。 **[入力イベント]** の値が 0 より大きくない場合は、ジョブの入力に問題があります。 詳しくは、「[入力接続のトラブルシューティング](stream-analytics-troubleshoot-input.md)」をご覧ください。 ジョブに参照データ入力が含まれている場合は、**入力イベント** メトリックを参照するときに、論理名で分割を適用します。 参照データのみからの入力イベントがない場合は、この入力ソースが適切な参照データセットをフェッチするように適切に構成されていない可能性があります。
   * **[データ変換エラー]** の値が 0 より大きく、上昇している場合は、「[Azure Stream Analytics データ エラー](data-errors.md)」でデータ変換エラーの詳細について確認してください。
   * **[ランタイム エラー]** の値が 0 より大きい場合は、ジョブはデータを受け取っていますが、クエリの処理中にエラーが発生しています。 エラーを確認するには、[監査ログ](../azure-resource-manager/management/view-activity-logs.md)に移動し、 **[失敗]** ステータスでフィルター処理します。
   * **[入力イベント]** の値が 0 より大きく、 **[出力イベント]** の値が 0 の場合は、次のいずれかの状態になっています。
      * クエリの処理結果で、出力イベントが 0 個だった。
      * イベントまたはフィールドの形式が不適切なため、クエリ処理の実行後に出力が 0 個になった。
      * 接続または認証が原因で、ジョブがデータを出力シンクにプッシュできない。

   クエリのロジックですべてのイベントが除外された場合を除き、発生中のものも含めて、操作ログ メッセージで詳細を確認できます。 複数のイベントの処理でエラーが発生した場合、エラーは 10 分ごとに集計されます。

## <a name="the-first-output-is-delayed"></a>最初の出力が遅延される

Stream Analytics ジョブが開始すると、入力イベントが読み取られます。 ただし、特定の状況では、出力に遅延が発生する場合があります。

テンポラル クエリ要素の時刻の値が大きい場合、出力の遅延が引き起こされる場合があります。 大きい時間枠について正しい出力を生成するため、ストリーミング ジョブでは可能な最新時刻からデータが読み取られて、時間枠が埋められます。 最大で過去 7 日間のデータを使用できます。 未処理の入力イベントが読み取られるまで、出力は生成されません。 この問題は、システムによってストリーミング ジョブがアップグレードされたときに、発生する可能性があります。 アップグレードが行われると、ジョブが再開されます。 このようなアップグレードは通常、2 か月に 1 度行われます。

Stream Analytics クエリを設計するときは、慎重に行ってください。 ジョブのクエリ構文でテンポラル要素に大きな時間枠を使用した場合、ジョブが開始または再開されるときに、最初の出力で遅延が発生する可能性があります。 大きな時間枠と見なされるのは、数時間以上、最大 7 日までです。

このような最初の出力の遅延に対する軽減策の 1 つは、データのパーティション分割など、クエリの並列処理の手法を使用することです。 または、ストリーミング ユニットを追加して、ジョブが追いつくまでスループットを向上させることもできます。  詳しくは、[Stream Analytics ジョブを作成する場合の考慮事項](stream-analytics-concepts-checkpoint-replay.md)に関するページをご覧ください。

最初の出力の適時性は、以下の要因によって影響を受けます。

* ウィンドウ集計の使用 (タンブリング ウィンドウ、ホッピング ウィンドウ、スライディング ウィンドウの GROUP BY 句など):

  * タンブリングまたはホッピング ウィンドウ集計の場合は、ウィンドウ時間枠の最後に結果が生成されます。
  * スライディング ウィンドウの場合は、イベントがスライディング ウィンドウに出入りするときに、結果が生成されます。
  * 1 時間以上などの大きなウィンドウ サイズを使用する場合は、ホッピング ウィンドウまたはスライディング ウィンドウを選択するのが最善です。 これらのウィンドウの種類を使用すると、出力をより頻繁に表示できます。

* テンポラル結合の使用 (DATEDIFF を使用した JOIN など):
  * 一致するイベントの両側を受け取ると、すぐに一致が生成されます。
  * 一致 (LEFT OUTER JOIN など) が欠如しているデータは、左側の各イベントに関する DATEDIFF ウィンドウの最後に生成されます。

* テンポラル分析関数の使用 (LIMIT DURATION を使用した ISFIRST、LAST、LAG など):
  * 分析関数の場合、すべてのイベントに対して出力が生成されます。 遅延はありません。

## <a name="the-output-falls-behind"></a>出力が遅れる

ジョブの通常の動作中に、出力の待機時間が徐々に長くなることがあります。 出力がそのように遅くなった場合は、以下の要因を調べることで、根本原因を特定できます。

* ダウンストリーム シンクが調整されているか
* アップストリーム ソースが調整されているか
* クエリの処理ロジックがコンピューティング集約型かどうか

出力の詳細を確認するには、Azure portal でストリーミング ジョブを選択して、 **[ジョブ ダイアグラム]** を選択します。 各入力に対し、パーティションごとのバックログ イベント メトリックがあります。 メトリックが継続的に増加している場合、システム リソースが制約を受けていることを示す指標になります。 そのような増加は、出力シンクの調整または高い CPU 使用率が原因になっている可能性があります。 詳しくは、「[ジョブ ダイアグラムを使用したデータ主導型デバッグ](stream-analytics-job-diagram-with-metrics.md)」をご覧ください。

## <a name="key-violation-warning-with-azure-sql-database-output"></a>Azure SQL Database の出力でのキー違反の警告

Azure SQL データベースを Stream Analytics ジョブへの出力として構成すると、宛先テーブルにレコードが一括挿入されます。 一般に、Azure Stream Analytics では、出力シンクに[少なくとも 1 回の配信](/stream-analytics-query/event-delivery-guarantees-azure-stream-analytics)が保証されます。 それでも、SQL テーブルに一意制約が定義されているときは、SQL 出力に対して [1 回限りの配信を実現する]( https://blogs.msdn.microsoft.com/streamanalytics/2017/01/13/how-to-achieve-exactly-once-delivery-for-sql-output/)ことができます。

SQL テーブルに一意のキー制約を設定すると、Azure Stream Analytics によって重複するレコードが削除されます。 データはバッチに分割され、単一の重複レコードが見つかるまで、バッチの再帰的な挿入が行われます。 分割および挿入プロセスでは、一度に 1 つの重複が無視されます。 重複する行が多数あるストリーミング ジョブの場合、プロセスは効率が悪く、時間がかかります。 過去 1 時間のアクティビティ ログにキー違反の警告メッセージが複数ある場合は、SQL 出力によってジョブ全体の速度が低下している可能性があります。

この問題を解決するには、IGNORE_DUP_KEY オプションを有効にして、キー違反の原因となっている[インデックスを構成]( https://docs.microsoft.com/sql/t-sql/statements/create-index-transact-sql)します。 このオプションを使用すると、SQL では一括挿入の間に重複する値を無視できます。 Azure SQL Database では、エラーではなく警告メッセージが生成されるだけです。 その結果、Azure Stream Analytics で主キー違反エラーが生成されなくなります。

IGNORE_DUP_KEY を構成する際には、一部のインデックスに関する次の考慮事項に注意してください。

* ALTER INDEX を使用する主キーや一意制約については、IGNORE_DUP_KEY を設定することはできません。 インデックスをドロップし、再作成する必要があります。  
* 一意インデックスの ALTER INDEX を使用して、IGNORE_DUP_KEY を設定できます。 このインスタンスは、PRIMARY KEY/UNIQUE 制約とは異なり、CREATE INDEX または INDEX 定義を使用して作成されます。  
* IGNORE_DUP_KEY オプションは、列ストア インデックスには適用されません (一意性を強制できないため)。

## <a name="sql-output-retry-logic"></a>SQL 出力再試行ロジック

SQL 出力が含まれる Stream Analytics ジョブで最初のイベント バッチを受け取ると、次の手順が実行されます。

1. ジョブによって SQL への接続が試みられます。
2. ジョブによって、ターゲット テーブルのスキーマがフェッチされます。
3. ジョブによって、ターゲット テーブル スキーマに対して列の名前と型が検証されます。
4. ジョブによって、バッチ内の出力レコードからインメモリ データ テーブルが準備されます。
5. ジョブによって BulkCopy [API](/dotnet/api/system.data.sqlclient.sqlbulkcopy.writetoserver) が使用され、データ テーブルが SQL に書き込まれます。

これらの手順中、SQL 出力で次の種類のエラーが発生する可能性があります。

* エクスポネンシャル バックオフの再試行戦略を使用して再試行される一時的な[エラー](../azure-sql/database/troubleshoot-common-errors-issues.md#transient-fault-error-messages-40197-40613-and-others)。 最小再試行間隔はエラー コードごとに異なりますが、間隔は通常 60 秒未満です。 上限は、最大 5 分です。 

   [ログイン エラー](../azure-sql/database/troubleshoot-common-errors-issues.md#unable-to-log-in-to-the-server-errors-18456-40531)と[ファイアウォールの問題](../azure-sql/database/troubleshoot-common-errors-issues.md#cannot-connect-to-server-due-to-firewall-issues)は、前回の試行から少なくとも 5 分後に再試行され、成功するまで再試行されます。

* キャスト エラーやスキーマ制約違反などのデータ エラーは、出力エラー ポリシーを使用して処理されます。 これらのエラーは、エラーの原因となった個々のレコードがスキップまたは再試行によって処理されるまで、バイナリ分割されたバッチを再試行することによって処理されます。 主/一意キーの制約違反は[常に処理されます](./stream-analytics-troubleshoot-output.md#key-violation-warning-with-azure-sql-database-output)。

* 一時的でないエラーは、SQL サービスの問題または内部コードの不具合がある場合に発生する可能性があります。 たとえば、(コード 1132) Elastic Pool hitting its storage limit (Elastic Pool のストレージ制限に到達しました) のようなエラーの場合、再試行を行ってもエラーは解決されません。 これらのシナリオでは、Stream Analytics ジョブのエクスペリエンスが[低下](job-states.md)します。
* `BulkCopy` タイムアウトは、手順 5 の `BulkCopy` 中に発生する可能性があります。 `BulkCopy` では、操作のタイムアウトがときどき発生する場合があります。 構成されている既定のタイムアウトの最少値は 5 分で、連続して到達すると倍になります。
タイムアウトが 15 分を超えると、バッチあたりのイベント数が 100 個になるまで、`BulkCopy` への最大バッチ サイズのヒントが半分になります。

## <a name="column-names-are-lowercase-in-azure-stream-analytics-10"></a>Azure Stream Analytics (1.0) では、列名は小文字です

元の互換性レベル (1.0) を使用すると、Azure Stream Analytics によって列名が小文字に変更されます。 この動作は、以降の互換性レベルで修正されました。 大文字と小文字の使い分けを維持するには、互換性レベル 1.1 以降に移行します。 詳しくは、「[Azure Stream Analytics ジョブの互換性レベル](./stream-analytics-compatibility-level.md)」をご覧ください。

## <a name="get-help"></a>ヘルプの参照

詳細については、[Azure Stream Analytics に関する Microsoft Q&A 質問ページ](/answers/topics/azure-stream-analytics.html)を参照してください。

## <a name="next-steps"></a>次のステップ

* [Azure Stream Analytics の概要](stream-analytics-introduction.md)
* [Azure Stream Analytics の使用](stream-analytics-real-time-fraud-detection.md)
* [Azure Stream Analytics ジョブのスケーリング](stream-analytics-scale-jobs.md)
* [Azure Stream Analytics クエリ言語リファレンス](/stream-analytics-query/stream-analytics-query-language-reference)
* [Azure Stream Analytics の管理 REST API リファレンス](/rest/api/streamanalytics/)