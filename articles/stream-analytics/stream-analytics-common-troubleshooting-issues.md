---
title: Azure Stream Analytics での一般的な問題のトラブルシューティング
description: この記事では、Azure Stream Analytics におけるいくつかの一般的な問題と、問題をトラブルシューティングするための手順について説明します。
services: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 12/06/2018
ms.custom: seodec18
ms.openlocfilehash: 0191c56e1140870b1710b48c4fa1189fd92a337b
ms.sourcegitcommit: 1c2cf60ff7da5e1e01952ed18ea9a85ba333774c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/12/2019
ms.locfileid: "59521477"
---
# <a name="common-issues-in-stream-analytics-and-steps-to-troubleshoot"></a>Stream Analytics での一般的な問題とトラブルシューティングの手順

## <a name="troubleshoot-malformed-input-events"></a>間違った形式の入力イベントをトラブルシューティングする

 Stream Analytics ジョブの入力ストリームに間違った形式のメッセージが含まれている場合は、シリアル化の問題が発生します。 たとえば、間違った形式のメッセ―ジは、JSON オブジェクト内でのかっこの不足や中かっこの不足、または時間フィールドでの不正なタイム スタンプ形式によって発生する可能性があります。 
 
 Stream Analytics ジョブは、入力から間違った形式のメッセージを受信すると、メッセージを削除し、ユーザーに警告で通知します。 警告の記号は、Stream Analytics ジョブの **[入力]** タイルに表示されます (この警告記号は、ジョブが実行中の状態である間、表示され続けます)。

![Azure Stream Analytics ダッシュボードの [入力] タイル](media/stream-analytics-malformed-events/stream-analytics-inputs-tile.png)

詳細な情報を確認するには、診断ログを有効にして警告の詳細を表示します。 間違った形式の入力イベントの場合、実行ログには次のようなメッセージを持つエントリが含まれます。"メッセージ:リソース \<blob URI> からの入力イベントを json として逆シリアル化できませんでした。" 

### <a name="troubleshooting-steps"></a>トラブルシューティングの手順

1. [入力] タイルに移動し、クリックして警告を表示します。

2. [入力の詳細] タイルに、一連の警告が問題に関する詳細と共に表示されます。 警告メッセージの例を次に示します、警告メッセージは、間違った形式の JSON データが存在するパーティション、オフセット、シーケンス番号を示します。 

   ![オフセットを含む入力警告メッセージ](media/stream-analytics-malformed-events/warning-message-with-offset.png)

3. 不適切な形式の JSON データを取得するには、CheckMalformedEvents.cs コードを実行します。 この例は、[GitHub サンプル リポジトリ](https://github.com/Azure/azure-stream-analytics/tree/master/Samples/CheckMalformedEventsEH)で使用可能です。 このコードは、パーティション ID とオフセットを読み取り、そのオフセットにあるデータを出力します。 

4. データの読み取り後、シリアル化形式を分析し、修正できます。

5. また、[Service Bus Explorer を使用して IoT Hub からイベントを読み取る](https://code.msdn.microsoft.com/How-to-read-events-from-an-1641eb1b)こともできます。

## <a name="delayed-output"></a>出力の遅延

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

これらの詳細を確認するには、Azure Portal で、ストリーミング ジョブを選択して、**[ジョブ ダイアグラム]** を選択します。 各入力には、パーティションごとのバックログ イベント メトリックがあります。 バックログ イベント メトリックが継続的に増加している場合、システム リソースが制約を受けていることを示す指標になります。 出力のシンク調整または高使用率の CPU が原因になっている可能性があります。 ジョブ ダイアグラムの使用に関する詳細については、「[ジョブ ダイアグラムを使用したデータ主導型デバッグ](stream-analytics-job-diagram-with-metrics.md)」をご覧ください。

## <a name="handle-duplicate-records-in-azure-sql-database-output"></a>Azure SQL Database 出力での重複レコードの処理

Azure SQL Database を Stream Analytics ジョブへの出力として構成すると、宛先テーブルにレコードが一括挿入されます。 通常、Azure Stream Analytics では、出力シンクに対して[最低 1 回の配信]( https://msdn.microsoft.com/azure/stream-analytics/reference/event-delivery-guarantees-azure-stream-analytics)が保証されますが、SQL テーブルで一意制約が定義されている場合は、[厳密に 1 回だけの配信を保証]( https://blogs.msdn.microsoft.com/streamanalytics/2017/01/13/how-to-achieve-exactly-once-delivery-for-sql-output/)することもできます。 

SQL テーブルに一意キー制約が設定された後に、SQL テーブルに挿入される重複したレコードがあると、Azure Stream Analytics によって重複したレコードは削除されます。 データはバッチに分割され、単一の重複レコードが見つかるでバッチの再帰的な挿入が行われます。 ストリーミング ジョブに多数の重複行が含まれる場合は、この分割と挿入の処理で重複を 1 つずつ無視する必要があり、効率が低下して時間がかかってしまいます。 過去 1 時間以内のアクティビティ ログにキー違反の警告メッセージが複数ある場合は、SQL 出力によってジョブ全体の速度が低下している可能性があります。 

この問題を解決するには、IGNORE_DUP_KEY オプションを有効にして、キー違反の原因となっている[インデックスを構成する]( https://docs.microsoft.com/sql/t-sql/statements/create-index-transact-sql)必要があります。 このオプションを有効にすると、一括挿入時の重複値は SQL によって無視され、SQL Azure ではエラーの代わりに警告メッセージが生成されます。 Azure Stream Analytics では、主キー違反エラーが生成されなくなります。

IGNORE_DUP_KEY を構成する際には、一部のインデックスに関する次の考慮事項に注意してください。

* ALTER INDEX を使用する主キーや一意制約については、IGNORE_DUP_KEY を設定することはできません。インデックスをドロップし、再作成する必要があります。  
* 一意なインデックスについては、IGNORE_DUP_KEY オプションを設定できます。このインデックスは、主キー/一意制約とは異なり、CREATE INDEX または INDEX 定義を使用して作成されます。  
* IGNORE_DUP_KEY は、列ストア インデックスには適用されません (このようなインデックスに対して一意性を強制することはできないため)。  

## <a name="next-steps"></a>次の手順
* [Stream Analytics Query Language Reference (Stream Analytics クエリ言語リファレンス)](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Azure Stream Analytics management REST API reference (Azure ストリーム分析の管理 REST API リファレンス)](https://msdn.microsoft.com/library/azure/dn835031.aspx)
