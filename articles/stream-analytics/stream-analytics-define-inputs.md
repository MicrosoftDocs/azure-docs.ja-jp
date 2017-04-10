---
title: "データ接続: イベント ストリームからのデータ ストリーム入力 | Microsoft Docs"
description: "&quot;入力&quot; という Stream Analytics に対するデータ接続を設定する方法について説明します。 入力には、イベントからのデータ ストリームだけでなく、参照データも含まれます。"
keywords: "データ ストリーム、データ接続、イベント ストリーム"
services: stream-analytics
documentationcenter: 
author: jeffstokes72
manager: jhubbard
editor: cgronlun
ms.assetid: 8155823c-9dd8-4a6b-8393-34452d299b68
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 03/28/2017
ms.author: jeffstok
translationtype: Human Translation
ms.sourcegitcommit: dcda8b30adde930ab373a087d6955b900365c4cc
ms.openlocfilehash: 2961d05d0a81ab30296e084dd40618828b960bd2
ms.lasthandoff: 12/08/2016


---
# <a name="data-connection-learn-about-data-stream-inputs-from-events-to-stream-analytics"></a>データ接続: Stream Analytics に対するイベントのデータ ストリーム入力の概要
Stream Analytics に対するデータ接続は、データ ソースからのイベントのデータ ストリームです。 これは "入力" と呼ばれます。 Stream Analytics は、分析ジョブと同じ、または異なる Azure サブスクリプションの Azure データ ストリーム ソースの Event Hub、IoT Hub、BLOB ストレージと高度に統合されています。

## <a name="data-input-types-data-stream-and-reference-data"></a>データ入力の種類: データ ストリームと参照データ
データがそのデータ ソースにプッシュされると、Stream Analytics ジョブによって使用され、リアルタイムで処理されます。 入力は 2 つの個別のタイプであるデータ ストリーム入力と参照データ入力に分けられます。

### <a name="data-stream-inputs"></a>データ ストリーム入力
データ ストリームは、時間をかけて受け取る、制約のない一連のイベントです。 Stream Analytics のジョブには、ジョブで使用および変換される少なくとも 1 つのデータ ストリームの入力を含める必要があります。 BLOB ストレージ、Event Hubs および IoT Hub は、データ ストリーム入力ソースとしてサポートされます。 Event Hubs は複数のデバイスとソースから、ソーシャル メディア アクティビティ フィード、株式取引情報、センサーからのデータなどのイベント ストリームを回収するために使用されます。 IoT Hub はモノのインターネット化 (IoT) シナリオ内で、接続されているデバイスからデータを収集するように最適化されています。  ストリームとしてバルク データを取り込むための入力ソースとして、BLOB ストレージを使用できます。  

### <a name="reference-data"></a>参照データ
Stream Analytics は、参照データと呼ばれる 2 番目のタイプの入力をサポートします。 これは静的データまたは変更頻度の低い補足データであり、通常は相関関係の関連付けと参照を実行するために使用されます。 現在、Azure BLOB ストレージは参照データをサポートする唯一の入力ソースです。 参照データ ソースの BLOB のサイズは、最大で 100MB に制限されています。
参照データ入力を作成する方法については、「 [参照データの使用](stream-analytics-use-reference-data.md)  

## <a name="create-a-data-stream-input-with-an-event-hub"></a>Event Hub でデータ ストリーム入力を作成する
[Azure Event Hubs](https://azure.microsoft.com/services/event-hubs/) はスケーラブルな発行/サブスクライブ イベント インジェスターです。 1 秒間に数百万件のイベントを取り込むことができるため、接続されたデバイスとアプリケーションで生成される大量のデータを処理および分析できます。 Stream Analytics で最もよく使用される入力の 1 つがこれです。 Event Hubs と Stream Analytics を一緒に使用することで、リアルタイム分析用のエンド ツー エンドのソリューションをお客様に提供できます。 お客様は、Event Hubs を使用して Azure にイベントをリアルタイムでフィードし、Stream Analytics ジョブでこれらのイベントをリアルタイムで処理することができます。 たとえば、お客様は、Web クリック、センサーの読み取り、オンライン ログ イベントを Event Hubs に送信し、リアルタイムのフィルター処理、集計、相関関係に入力データ ストリームとして Event Hubs を使用する Stream Analytics ジョブを作成できます。

Stream Analytics で Event Hubs から返されるイベントの既定のタイムスタンプは、このイベントが Event Hubs に到達したときのタイムスタンプであることに注意することが重要です。このタイムスタンプが EventEnqueuedUtcTime です。 イベント ペイロードのタイムスタンプを利用してデータを処理するには、 [TIMESTAMP BY](https://msdn.microsoft.com/library/azure/dn834998.aspx) キーワードを使用する必要があります。

### <a name="consumer-groups"></a>コンシューマー グループ
Stream Analytics イベント ハブの各入力は、独自のコンシューマー グループを持つように構成する必要があります。 ジョブに自己結合または複数の入力が含まれる場合、一部の入力は複数の閲覧者ダウンストリームによって読み取られる可能性があります。これは 1 つのコンシューマー グループの閲覧者数に影響を与えます。 閲覧者の数を各パーティションのコンシューマー グループ別に 5 人とするイベント ハブの上限を回避するには、Stream Analytics ジョブごとにコンシューマー グループを指定するのが最良事例となります。 Event Hub ごとに 20 個のコンシューマー グループという制限もある点に注意してください。 詳細については、「[Event Hubs のプログラミング ガイド](../event-hubs/event-hubs-programming-guide.md)」をご覧ください。

### <a name="configure-event-hub-as-an-input-data-stream"></a>入力データ ストリームとしてイベント ハブを構成する
次の表に、[イベント ハブ入力] タブの各プロパティとその説明を示しています。

| プロパティ名 | 説明 |
| --- | --- |
| 入力のエイリアス |この入力を参照するジョブ クエリで使用されるわかりやすい名前。 |
| Service Bus 名前空間 |Service Bus 名前空間は、一連のメッセージング エンティティのコンテナーです。 新しいイベント ハブを作成するときは、Service Bus 名前空間も作成します。 |
| イベント ハブ |イベント ハブ入力の名前。 |
| イベント ハブ ポリシー名 |[イベント ハブの構成] タブで作成できる共有アクセス ポリシー。 各共有アクセス ポリシーには、名前、設定したアクセス許可、およびアクセス キーが含まれます。 |
| イベント ハブ ポリシー キー |Service Bus 名前空間へのアクセスを認証するために使用する共有アクセス キー。 |
| イベント ハブ コンシューマー グループ (省略可能) |イベント ハブからのデータを取り込むためのコンシューマー グループです。 指定しないと、Stream Analytics ジョブは既定のコンシューマー グループを使用してイベント ハブからデータを取り込みます。 Stream Analytics ジョブごとに個別のコンシューマー グループを使用することをお勧めします。 |
| イベントのシリアル化の形式 |クエリを予想どおりに動作させるには、入ってくるデータ ストリームに使用しているシリアル化形式 (JSON、CSV、Avro) が Stream Analytics で認識される必要があります。 |
| エンコード |現在のところ、UTF-8 が、唯一サポートされているエンコード形式です。 |

データがイベント ハブのソースから送信されるとき、Stream Analytics クエリでアクセスできるメタデータ フィールドはほとんどありません。 次の表に、そのフィールドと説明を一覧表示します。

| プロパティ | 説明 |
| --- | --- |
| EventProcessedUtcTime |Stream Analytics によってイベントが処理された日時。 |
| EventEnqueuedUtcTime |Event Hubs でイベントを受信した日時。 |
| PartitionId |入力アダプターの 0 から始まるパーティション ID。 |

たとえば、次のようにクエリを記述することができます。

````
SELECT
    EventProcessedUtcTime,
    EventEnqueuedUtcTime,
    PartitionId
FROM Input
````

## <a name="create-an-iot-hub-data-stream-input"></a>IoT Hub データ ストリーム入力の作成
Azure IoT Hub は、IoT シナリオ向けに最適化されたスケーラブルな発行/サブスクライブ イベント インジェスターです。
Stream Analytics で IoT Hub から返されるイベントの既定のタイムスタンプは、このイベントが IoT Hub に到達したときのタイムスタンプであることに注意することが重要です。このタイムスタンプが EventEnqueuedUtcTime です。 イベント ペイロードのタイムスタンプを利用してデータを処理するには、 [TIMESTAMP BY](https://msdn.microsoft.com/library/azure/dn834998.aspx) キーワードを使用する必要があります。

> [!NOTE]
> DeviceClient プロパティを使用して送信されたメッセージだけを処理できます。
> 
> 

### <a name="consumer-groups"></a>コンシューマー グループ
Stream Analytics IoT Hub の各入力は、独自のコンシューマー グループを持つように構成する必要があります。 ジョブに自己結合または複数の入力が含まれる場合、一部の入力は複数の閲覧者ダウンストリームによって読み取られる可能性があります。これは 1 つのコンシューマー グループの閲覧者数に影響を与えます。 閲覧者の数を各パーティションのコンシューマー グループ別に 5 人とする IoT Hub の上限を回避するには、Stream Analytics ジョブごとにコンシューマー グループを指定するのが最良事例となります。

### <a name="configure-iot-hub-as-an-data-stream-input"></a>データ ストリーム入力として IoT Hub を構成する
次の表に、IoT Hub の入力タブの各プロパティとその説明を示しています。

| プロパティ名 | 説明 |
| --- | --- |
| 入力のエイリアス |この入力を参照するジョブ クエリで使用されるわかりやすい名前。 |
| IoT Hub |IoT Hub は、一連のメッセージング エンティティのコンテナーです。 |
| エンドポイント |IoT Hub エンドポイントの名前。 |
| 共有アクセス ポリシー名 |IoT Hub にアクセス権を付与する共有アクセス ポリシー。 各共有アクセス ポリシーには、名前、設定したアクセス許可、およびアクセス キーが含まれます。 |
| 共有アクセス ポリシー キー |IoT Hub へのアクセスを認証するために使用する共有アクセス キー。 |
| コンシューマー グループ (省略可能) |IoT Hub からのデータを取り込むためのコンシューマー グループです。 指定しないと、Stream Analytics ジョブは既定のコンシューマー グループを使用して IoT Hub からデータを取り込みます。 Stream Analytics ジョブごとに個別のコンシューマー グループを使用することをお勧めします。 |
| イベントのシリアル化の形式 |クエリを予想どおりに動作させるには、入ってくるデータ ストリームに使用しているシリアル化形式 (JSON、CSV、Avro) が Stream Analytics で認識される必要があります。 |
| エンコード |現在のところ、UTF-8 が、唯一サポートされているエンコード形式です。 |

データが IoT Hub のソースから送信されるとき、Stream Analytics クエリでアクセスできるメタデータ フィールドはほとんどありません。 次の表に、そのフィールドと説明を一覧表示します。

| プロパティ | 説明 |
| --- | --- |
| EventProcessedUtcTime |イベントが処理された日時。 |
| EventEnqueuedUtcTime |IoT Hub でイベントを受信した日時。 |
| PartitionId |入力アダプターの 0 から始まるパーティション ID。 |
| IoTHub.MessageId |IoT Hub での双方向通信の関連付けに使用します。 |
| IoTHub.CorrelationId |IoT Hub でのメッセージ応答とフィードバックに使用します。 |
| IoTHub.ConnectionDeviceId |メッセージの送信に使用される認証済み ID であり、IoT Hub により servicebound メッセージにスタンプされます。 |
| IoTHub.ConnectionDeviceGenerationId |メッセージの送信に使用される認証済みデバイスの generationId であり、IoT Hub により servicebound メッセージにスタンプされます。 |
| IoTHub.EnqueuedTime |IoT Hub でメッセージを受信した時刻。 |
| IoTHub.StreamId |送信元デバイスにより追加されたカスタム イベント プロパティ。 |

## <a name="create-a-blob-storage-data-stream-input"></a>BLOB Storage データ ストリーム入力の作成
大量の非構造化データをクラウドに保存する場合は、BLOB ストレージを使用するとコスト効率の高いスケーラブルなソリューションが実現します。 [BLOB ストレージ](https://azure.microsoft.com/services/storage/blobs/) のデータは一般的に "休息中の" データとして見なされますが、Stream Analytics でデータ ストリームとして処理できます。 BLOB ストレージと Stream Analytics の一般的なシナリオには、製品利用統計情報をシステムから取得し、解析し、処理し、意味のあるデータを抽出するログ処理があります。

Stream Analytics の BLOB Storage イベントの既定のタイムスタンプは BLOB が最後に更新されたタイムスタンプであることに注意してください。このタイムスタンプが *BlobLastModifiedUtcTime* です。 イベント ペイロードのタイムスタンプを利用してデータを処理するには、 [TIMESTAMP BY](https://msdn.microsoft.com/library/azure/dn834998.aspx) キーワードを使用する必要があります。

CSV 形式の入力についても、データ セットのフィールドを定義するためのヘッダー行が **必須** であることに注意してください。 また、ヘッダー行のフィールドはすべて **一意**であることが必要です。

> [!NOTE]
> Stream Analytics では、既存の BLOB にコンテンツを追加できません。 Stream Analytics は BLOB を 1 回だけ表示します。その読み込み後の変更は処理されません。 ベスト プラクティスとしては、すべてのデータを一度にアップロードし、BLOB ストアにイベントを追加しないことです。
> 
> 

次の表に、[BLOB ストレージ入力] タブの各プロパティとその説明を示しています。

<table>
<tbody>
<tr>
<td>プロパティ名</td>
<td>説明</td>
</tr>
<tr>
<td>入力のエイリアス</td>
<td>この入力を参照するジョブ クエリで使用されるわかりやすい名前。</td>
</tr>
<tr>
<td>ストレージ アカウント</td>
<td>BLOB ファイルが配置されるストレージ アカウントの名前。</td>
</tr>
<tr>
<td>ストレージ アカウント キー</td>
<td>ストレージ アカウントに関連付けられている秘密キー。</td>
</tr>
<tr>
<td>ストレージ コンテナー
</td>
<td>コンテナーにより、Microsoft Azure BLOB サービスに格納される BLOB が論理的にグループ化されます。 BLOB を BLOB サービスにアップロードするとき、その BLOB のコンテナーを指定する必要があります。</td>
</tr>
<tr>
<td>パス プレフィックスのパターン [省略可能]</td>
<td>指定されたコンテナー内に BLOB を配置するために使用されるファイル パス。
このパス内に、次の 3 つの変数のいずれかまたはすべてのインスタンスを指定できます。<BR>{date}, {time},<BR>{partition}<BR>例 1: cluster1/logs/{date}/{time}/{partition}<BR>例 2: cluster1/logs/{date}<P>"*" は Pathprefix の許容値ではありません。 許容値は、有効な <a HREF="https://msdn.microsoft.com/library/azure/dd135715.aspx">Azure BLOB 文字</a>のみです。</td>
</tr>
<tr>
<td>日付形式 [省略可能]</td>
<td>日付トークンがプレフィックス パスで使用されている場合、ファイルを編成する日付形式を選択できます。 例: YYYY/MM/DD</td>
</tr>
<tr>
<td>時刻形式 [省略可能]</td>
<td>時刻トークンがプレフィックス パスで使用されている場合、ファイルを編成する時刻形式を指定します。 現在唯一サポートされている値は HH です。</td>
</tr>
<tr>
<td>イベントのシリアル化の形式</td>
<td>クエリを予想どおりに動作させるには、入ってくるデータ ストリームに使用しているシリアル化形式 (JSON、CSV、Avro) が Stream Analytics で認識される必要があります。</td>
</tr>
<tr>
<td>エンコード</td>
<td>CSV と JSON では、現在のところ、UTF-8 が唯一サポートされているエンコード形式です。</td>
</tr>
<tr>
<td>区切り記号</td>
<td>Stream Analytics は、CSV 形式のデータをシリアル化するために、一般的な区切り記号をサポートしています。 サポートしている値は、コンマ、セミコロン、スペース、タブ、および縦棒です。</td>
</tr>
</tbody>
</table>

データが BLOB ストレージのソースから送信されるとき、Stream Analytics クエリでアクセスできるメタデータ フィールドはわずかです。 次の表に、そのフィールドと説明を一覧表示します。

| プロパティ | 説明 |
| --- | --- |
| BlobName |イベントに起因する入力 BLOB の名前。 |
| EventProcessedUtcTime |Stream Analytics によってイベントが処理された日時。 |
| BlobLastModifiedUtcTime |BLOB が最後に変更された日時。 |
| PartitionId |入力アダプターの 0 から始まるパーティション ID。 |

たとえば、次のようにクエリを記述することができます。

````
SELECT
    BlobName,
    EventProcessedUtcTime,
    BlobLastModifiedUtcTime
FROM Input
````


## <a name="get-help"></a>問い合わせ
さらにサポートが必要な場合は、 [Azure Stream Analytics フォーラム](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics)

## <a name="next-steps"></a>次のステップ
ここでは、Azure での Stream Analytics ジョブのデータ接続オプションについて説明しました。 Stream Analytics の詳細については、以下を参照してください。

* [Azure Stream Analytics の使用](stream-analytics-get-started.md)
* [Azure Stream Analytics ジョブのスケーリング](stream-analytics-scale-jobs.md)
* [Stream Analytics Query Language Reference (Stream Analytics クエリ言語リファレンス)](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Azure Stream Analytics management REST API reference (Azure ストリーム分析の管理 REST API リファレンス)](https://msdn.microsoft.com/library/azure/dn835031.aspx)

<!--Link references-->
[stream.analytics.developer.guide]: ../stream-analytics-developer-guide.md
[stream.analytics.scale.jobs]: stream-analytics-scale-jobs.md
[stream.analytics.introduction]: stream-analytics-introduction.md
[stream.analytics.get.started]: stream-analytics-get-started.md
[stream.analytics.query.language.reference]: http://go.microsoft.com/fwlink/?LinkID=513299
[stream.analytics.rest.api.reference]: http://go.microsoft.com/fwlink/?LinkId=517301

