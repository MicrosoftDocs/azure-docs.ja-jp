---
title: Azure Stream Analytics の入力のトラブルシューティング
description: この記事では、Azure Stream Analytics ジョブの入力接続のトラブルシューティングを行う方法について説明します。
author: sidramadoss
ms.author: sidram
ms.service: stream-analytics
ms.topic: troubleshooting
ms.date: 05/01/2020
ms.custom: seodec18
ms.openlocfilehash: 2d7171c9ec1e60447fb3342caa72098fb2eb9337
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98019909"
---
# <a name="troubleshoot-input-connections"></a>入力接続のトラブルシューティング

この記事では、Azure Stream Analytics の入力接続に関する一般的な問題、入力の問題のトラブルシューティングの方法、および問題を修正する方法について説明します。 多くのトラブルシューティングの手順では、Stream Analytics ジョブに対してリソース ログを有効にする必要があります。 リソース ログが有効になっていない場合は、「[診断ログを使用した Azure Stream Analytics のトラブルシューティング](stream-analytics-job-diagnostic-logs.md)」を参照してください。

## <a name="input-events-not-received-by-job"></a>ジョブによって受信されない入力イベント 

1.  入力と出力の接続をテストします。 各入力と出力の **[接続のテスト]** ボタンを使用して、入力と出力への接続を確認します。

2.  入力データを確認します。

    1. 各入力の [ **[サンプル データ]**](./stream-analytics-test-query.md) ボタンを使用します。 入力のサンプル データをダウンロードします。
        
    1. サンプル データを調べて、スキーマと[データ型](/stream-analytics-query/data-types-azure-stream-analytics)を理解します。
    
    1. [イベント ハブのメトリック](../event-hubs/event-hubs-metrics-azure-monitor.md)を確認して、イベントが送信されていることを確認します。 Event Hubs がメッセージを受信している場合、メッセージ メトリックは 0 より大きい必要があります。

3.  入力プレビューで時間の範囲を選択していることを確認します。 **[時間範囲の選択]** を選択し、クエリをテストする前にサンプル期間を入力します。

## <a name="malformed-input-events-causes-deserialization-errors"></a>形式が正しくない入力イベントが原因の逆シリアル化エラー 

Stream Analytics ジョブの入力ストリームに間違った形式のメッセージが含まれている場合は、逆シリアル化の問題が発生します。 たとえば、間違った形式のメッセ―ジは、JSON オブジェクト内でのかっこや中かっこの不足、または時間フィールドでの不正なタイム スタンプ形式によって発生する可能性があります。 
 
Stream Analytics ジョブは、入力から間違った形式のメッセージを受信すると、メッセージを削除し、警告で通知します。 警告記号は、Stream Analytics ジョブの **[入力]** タイルの上に表示されます。 次の警告記号は、ジョブが実行状態である限り存在します。

![Azure Stream Analytics の入力タイル](media/stream-analytics-malformed-events/stream-analytics-inputs-tile.png)

リソース ログを有効にして、エラーの詳細と、エラーの原因となったメッセージ (ペイロード) を表示します。 逆シリアル化エラーが発生する理由は複数あります。 特定の逆シリアル化エラーの詳細については、「[入力データ エラー](data-errors.md#input-data-errors)」を参照してください。 リソース ログが有効になっていない場合は、Azure portal で簡単な通知を利用できます。

![入力の詳細の警告通知](media/stream-analytics-malformed-events/warning-message-with-offset.png)

メッセージ ペイロードが 32 KB より大きいかバイナリ形式の場合は、[GitHub サンプル リポジトリ](https://github.com/Azure/azure-stream-analytics/tree/master/Samples/CheckMalformedEventsEH)から入手できる CheckMalformedEvents.cs コードを実行します。 このコードは、パーティション ID とオフセットを読み取り、そのオフセットにあるデータを出力します。 

## <a name="job-exceeds-maximum-event-hub-receivers"></a>ジョブがイベント ハブ レシーバーの最大数を超えている

Event Hubs を使用する際のベスト プラクティスは、ジョブの拡張性のために複数のコンシューマー グループを使用することです。 特定の入力に関する Stream Analytics ジョブのリーダーの数は、1 つのコンシューマー グループ内のリーダーの数に影響します。 レシーバーの正確な数は、スケールアウト トポロジ ロジックの内部実装の詳細に基づいて決まり、外部には公開されません。 リーダーの数は、ジョブの開始時またはジョブのアップグレード中に変更される可能性があります。

レシーバー数が最大数を超えると、次のエラー メッセージが表示されます。 エラー メッセージには、コンシューマー グループのイベント ハブに対して行われた既存の接続の一覧が含まれています。 タグ `AzureStreamAnalytics` は、接続が Azure ストリーミング サービスからのものであることを示します。

```
The streaming job failed: Stream Analytics job has validation errors: Job will exceed the maximum amount of Event Hub Receivers.

The following information may be helpful in identifying the connected receivers: Exceeded the maximum number of allowed receivers per partition in a consumer group which is 5. List of connected receivers – 
AzureStreamAnalytics_c4b65e4a-f572-4cfc-b4e2-cf237f43c6f0_1, 
AzureStreamAnalytics_c4b65e4a-f572-4cfc-b4e2-cf237f43c6f0_1, 
AzureStreamAnalytics_c4b65e4a-f572-4cfc-b4e2-cf237f43c6f0_1, 
AzureStreamAnalytics_c4b65e4a-f572-4cfc-b4e2-cf237f43c6f0_1, 
AzureStreamAnalytics_c4b65e4a-f572-4cfc-b4e2-cf237f43c6f0_1.
```

> [!NOTE]
> ジョブのアップグレード中にリーダーの数が変更されると、一時的な警告が監査ログに書き込まれます。 Stream Analytics ジョブは、これらの一時的な問題から自動的に回復します。

### <a name="add-a-consumer-group-in-event-hubs"></a>Event Hubs でコンシューマー グループを追加する

Event Hubs インスタンスに新しいコンシューマー グループを追加するには、次の手順に従います。

1. Azure portal にサインインします。

2. Event Hubs のある場所を探します。

3. **Entities** 見出しの部分から **Event Hubs** を選択します｡

4. 名前でイベント ハブを選択します｡

5. **Event Hubs インスタンス** ページの **Entities** 見出しの部分から **コンシューマー グループ** を選択します｡ **$Default** という名前のコンシューマー グループが表示されます｡

6. **[+ コンシューマー グループ]** を選択し、新しいコンシューマー グループを追加します。 

   ![Event Hubs でコンシューマー グループを追加する](media/stream-analytics-event-hub-consumer-groups/new-eh-consumer-group.png)

7. Stream Analytics ジョブで入力を作成し､イベント ハブをポイントすると､そこにコンシューマー グループを指定したことになります｡ 名前を指定しなかった場合は､ **$Default** が使用されます。 コンシューマー グループを新規作成したら､Stream Analytics ジョブで Event Hub 入力を編集し､新しいコンシューマー グループの名前を指定します｡

## <a name="readers-per-partition-exceeds-event-hubs-limit"></a>パーティションあたりのリーダー数が Event Hubs の上限を上回る

ストリーミング クエリ構文で同じ入力 Event Hub リソースが複数回参照されている場合、ジョブ エンジンは、同じコンシューマー グループからクエリ 1 つに複数のリーダーを使用することができます。 同じコンシューマー グループに対する参照回数が多すぎると､上限の 5 つを超え､エラーになることがあります｡ その場合は､以下で説明するソリューションを使って､複数の入力を複数のコンシューマー グループにまたがらせることで分割することができます｡ 

パーティションあたりのリーダーの数が Event Hubs の上限である 5 つを上回るシナリオとしては、次のものがあります。

* 複数の SELECT ステートメント:**同じ** イベント ハブ入力を参照する複数の SELECT ステートメントを使用する場合、各 SELECT ステートメントによって新しいレシーバーが作成されます。

* UNION:UNION を使用する場合、**同じ** イベント ハブとコンシューマー グループを参照する複数の入力を使用できます。

* SELF JOIN:SELF JOIN 操作を使用する場合、**同じ** イベント ハブを複数回参照できます。

次のベスト プラクティスは、パーティションあたりのリーダーの数が 5 つという Event Hubs の制限を超えるシナリオに対処するのに役立ちます。

### <a name="split-your-query-into-multiple-steps-by-using-a-with-clause"></a>WITH 句を使用してクエリを複数のステップに分割する

WITH 句を使用すると、クエリ内で FROM によって参照できる一時的な名前付き結果セットを指定できます。 WITH 句は、単一の SELECT ステートメントの実行スコープで定義します。

たとえば、次のクエリの代わりに、

```SQL
SELECT foo 
INTO output1
FROM inputEventHub

SELECT bar
INTO output2
FROM inputEventHub 
…
```

次のクエリを使用します。

```SQL
WITH data AS (
   SELECT * FROM inputEventHub
)

SELECT foo
INTO output1
FROM data

SELECT bar
INTO output2
FROM data
…
```

### <a name="ensure-that-inputs-bind-to-different-consumer-groups"></a>入力が異なるコンシューマー グループにバインドされるようにする

3 つ以上の入力が同じ Event Hubs コンシューマー グループに接続されるクエリでは、別のコンシューマー グループを作成します。 そのためには、追加の Stream Analytics 入力を作成する必要があります。

### <a name="create-separate-inputs-with-different-consumer-groups"></a>コンシューマー グループが異なる個別の入力を作成する

同じイベント ハブに対して、異なるコンシューマー グループを使用して個別の入力を作成できます。 次の UNION クエリは *InputOne* と *InputTwo* が同じイベント ハブ ソースを参照している例です。 クエリでは、個別の入力に異なるコンシューマー グループを持たせることができます。 UNION クエリは 1 つの例にすぎません。

```sql
WITH 
DataOne AS 
(
SELECT * FROM InputOne 
),

DataTwo AS 
(
SELECT * FROM InputTwo 
),

SELECT foo FROM DataOne
UNION 
SELECT foo FROM DataTwo

```

## <a name="readers-per-partition-exceeds-iot-hub-limit"></a>パーティションあたりのリーダー数が IoT Hub の上限を上回る

Stream Analytics ジョブでは、IoT Hub に組み込まれた[イベント ハブ互換エンドポイント](../iot-hub/iot-hub-devguide-messages-read-builtin.md)を使用して IoT ハブに接続し、イベントが読み取られます。 パーティションあたりのリーダー数が IoT Hub の上限を上回る場合は、[イベントハブでの解決策](#readers-per-partition-exceeds-event-hubs-limit)を使用して問題を解決できます。 組み込みエンドポイントのコンシューマー グループは、IoT Hub ポータルのエンドポイント セッションまたは [IoT Hub SDK](/rest/api/iothub/IotHubResource/CreateEventHubConsumerGroup) を使用して作成できます。

## <a name="get-help"></a>ヘルプの参照

詳細については、[Azure Stream Analytics に関する Microsoft Q&A 質問ページ](/answers/topics/azure-stream-analytics.html)を参照してください。

## <a name="next-steps"></a>次のステップ

* [Azure Stream Analytics の概要](stream-analytics-introduction.md)
* [Azure Stream Analytics の使用](stream-analytics-real-time-fraud-detection.md)
* [Azure Stream Analytics ジョブのスケーリング](stream-analytics-scale-jobs.md)
* [Stream Analytics Query Language Reference (Stream Analytics クエリ言語リファレンス)](/stream-analytics-query/stream-analytics-query-language-reference)
* [Azure Stream Analytics management REST API reference (Azure ストリーム分析の管理 REST API リファレンス)](/rest/api/streamanalytics/)