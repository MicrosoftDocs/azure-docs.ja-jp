---
title: "データ接続: イベント ストリームからのデータ ストリーム入力 | Microsoft Docs"
description: "'入力' という Stream Analytics に対するデータ接続を設定する方法について説明します。 入力には、イベントからのデータ ストリームだけでなく、参照データも含まれます。"
keywords: "データ ストリーム、データ接続、イベント ストリーム"
services: stream-analytics
documentationcenter: 
author: samacha
manager: jhubbard
editor: cgronlun
ms.assetid: 8155823c-9dd8-4a6b-8393-34452d299b68
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 07/05/2017
ms.author: samacha
ms.translationtype: HT
ms.sourcegitcommit: a0b98d400db31e9bb85611b3029616cc7b2b4b3f
ms.openlocfilehash: 8ea05e1c3419f3e9c6b5806c1a2d4035239809d8
ms.contentlocale: ja-jp
ms.lasthandoff: 08/29/2017

---
# <a name="data-connection-learn-about-data-stream-inputs-from-events-to-stream-analytics"></a>データ接続: Stream Analytics に対するイベントのデータ ストリーム入力の概要
Stream Analytics ジョブに対するデータ接続は、データ ソースからのイベントのデータ ストリームです。これはジョブの "*入力*" と呼ばれます。 Stream Analytics は、Azure データ ストリーム ソース ([Azure Event Hubs](https://azure.microsoft.com/services/event-hubs/)、[Azure IoT Hub](https://azure.microsoft.com/services/iot-hub/)、[Azure Blob Storage](https://azure.microsoft.com/services/storage/blobs/) など) と高度に統合されています。 分析ジョブと同じ Azure サブスクリプションまたは異なるサブスクリプションの入力ソースを指定できます。

## <a name="data-input-types-data-stream-and-reference-data"></a>データ入力の種類: データ ストリームと参照データ
データがそのデータ ソースにプッシュされると、Stream Analytics ジョブによって使用され、リアルタイムで処理されます。 入力は 2 つのタイプであるデータ ストリーム入力と参照データ入力に分けられます。

### <a name="data-stream-inputs"></a>データ ストリーム入力
データ ストリームは、時間をかけて受け取る、制約のない一連のイベントです。 Stream Analytics のジョブには、少なくとも 1 つのデータ ストリーム入力を含める必要があります。 Event Hubs、IoT Hub、および Blob Storage は、データ ストリーム入力ソースとしてサポートされます。 Event Hubs は、複数のデバイスとサービスからイベント ストリームを収集するために使用されます。 これらのストリームには、ソーシャル メディア アクティビティ フィード、株式取引情報、またはセンサーからのデータが含まれている場合があります。 IoT Hub はモノのインターネット化 (IoT) シナリオ内で、接続されているデバイスからデータを収集するように最適化されています。  ストリームとしてバルク データ (ログ ファイルなど) を取り込むための入力ソースとして、Blob Storage を使用できます。  

### <a name="reference-data"></a>参照データ
Stream Analytics は、"*参照データ*" と呼ばれる入力もサポートします。 これは静的データまたは変更頻度の低い補足データであり、 通常は相関関係の関連付けと参照を実行するために使用されます。 たとえば、SQL の結合を実行して静的な値を参照する場合と同様に、データ ストリーム入力のデータを参照データのデータに結合できます。 現在、Azure BLOB ストレージは参照データをサポートする唯一の入力ソースです。 参照データ ソースの BLOB のサイズは、最大で 100 MB に制限されています。

参照データ入力を作成する方法については、「[Stream Analytics の入力ストリームでの参照データまたはルックアップ テーブルの使用](stream-analytics-use-reference-data.md)」を参照してください。  

## <a name="create-data-stream-input-from-event-hubs"></a>Event Hubs からデータ ストリーム入力を作成する

Azure Event Hubs はスケーラブルな発行/サブスクライブ イベント インジェスターを提供します。 1 秒間に数百万件のイベントを取り込むことができるため、接続されたデバイスとアプリケーションで生成される大量のデータを処理および分析できます。 Event Hubs と Stream Analytics を一緒に使用することで、リアルタイム分析用のエンド ツー エンドのソリューションを提供します。Event Hubs を使用すると、Azure にイベントをリアルタイムでフィードし、Stream Analytics ジョブでこれらのイベントをリアルタイムで処理することができます。 たとえば、Web クリック、センサーの読み取り、オンライン ログ イベントを Event Hubs に送信できます。 続いて、リアルタイムのフィルター処理、集計、相関関係に入力データ ストリームとして Event Hubs を使用する Stream Analytics ジョブを作成できます。

Stream Analytics で Event Hubs から返されるイベントの既定のタイムスタンプは、このイベントが Event Hubs に到達したときのタイムスタンプです。このタイムスタンプが `EventEnqueuedUtcTime` です。 イベント ペイロードのタイムスタンプを利用してデータをストリームとして処理するには、[TIMESTAMP BY](https://msdn.microsoft.com/library/azure/dn834998.aspx) キーワードを使用する必要があります。

### <a name="consumer-groups"></a>コンシューマー グループ
Stream Analytics イベント ハブの各入力は、独自のコンシューマー グループを持つように構成する必要があります。 ジョブに自己結合または複数の入力が含まれる場合、一部の入力は複数の閲覧者ダウンストリームによって読み取られる可能性があります。 この状況は 1 つのコンシューマー グループの閲覧者数に影響を与えます。 閲覧者の数を各パーティションのコンシューマー グループ別に 5 人とする Event Hubs の上限を回避するには、Stream Analytics ジョブごとにコンシューマー グループを指定するのが最良事例となります。 イベント ハブごとに 20 個のコンシューマー グループという制限もあります。 詳細については、「[Event Hubs のプログラミング ガイド](../event-hubs/event-hubs-programming-guide.md)」を参照してください。

### <a name="configure-an-event-hub-as-a-data-stream-input"></a>データ ストリーム入力としてイベント ハブを構成する
次の表に、入力としてイベント ハブを構成する場合に使用する、Azure Portal の **[新しい入力]** ブレードにある各プロパティの説明を示しています。

| プロパティ | Description |
| --- | --- |
| **入力のエイリアス** |この入力を参照するジョブのクエリで使用するわかりやすい名前。 |
| **Service Bus 名前空間** |Azure Service Bus 名前空間。これは、一連のメッセージング エンティティのコンテナーです。 新しいイベント ハブを作成するときは、Service Bus 名前空間も作成します。 |
| **イベント ハブ名** |入力として使用するイベント ハブの名前。 |
| **イベント ハブ ポリシー名** |イベント ハブへのアクセスを提供する共有アクセス ポリシー。 各共有アクセス ポリシーには、名前、設定したアクセス許可、アクセス キーが含まれています。 |
| **イベント ハブ コンシューマー グループ** (省略可能) |イベント ハブからのデータを取り込むために使用するコンシューマー グループです。 コンシューマー グループが指定されていない場合、Stream Analytics ジョブは既定のコンシューマー グループを使用します。 Stream Analytics ジョブごとに個別のコンシューマー グループを使用することをお勧めします。 |
| **イベントのシリアル化の形式** |入ってくるデータ ストリームのシリアル化形式 (JSON、CSV、または Avro)。 |
| **Encoding** | 現在のところ、UTF-8 が、唯一サポートされているエンコード形式です。 |

データがイベント ハブから送信される場合、Stream Analytics クエリでは次のメタデータ フィールドにアクセスできます。

| プロパティ | Description |
| --- | --- |
| **EventProcessedUtcTime** |Stream Analytics によってイベントが処理された日時。 |
| **EventEnqueuedUtcTime** |Event Hubs でイベントを受信した日時。 |
| **PartitionId** |入力アダプターの 0 から始まるパーティション ID。 |

たとえば、これらのフィールドを使用して、次の例のようにクエリを記述できます。

````
SELECT
    EventProcessedUtcTime,
    EventEnqueuedUtcTime,
    PartitionId
FROM Input
````

## <a name="create-data-stream-input-from-iot-hub"></a>IoT Hub からデータ ストリーム入力を作成する
Azure IoT Hub は、IoT シナリオ向けに最適化されたスケーラブルな発行/サブスクライブ イベント インジェスターです。

Stream Analytics で IoT Hub から返されるイベントの既定のタイムスタンプは、このイベントが IoT Hub に到達したときのタイムスタンプです。このタイムスタンプが `EventEnqueuedUtcTime` です。 イベント ペイロードのタイムスタンプを利用してデータをストリームとして処理するには、[TIMESTAMP BY](https://msdn.microsoft.com/library/azure/dn834998.aspx) キーワードを使用する必要があります。

> [!NOTE]
> `DeviceClient` プロパティを使用して送信されたメッセージだけを処理できます。
> 
> 

### <a name="consumer-groups"></a>コンシューマー グループ
Stream Analytics IoT Hub の各入力は、独自のコンシューマー グループを持つように構成する必要があります。 ジョブに自己結合または複数の入力が含まれる場合、一部の入力は複数の閲覧者ダウンストリームによって読み取られる可能性があります。 この状況は 1 つのコンシューマー グループの閲覧者数に影響を与えます。 閲覧者の数を各パーティションのコンシューマー グループ別に 5 人とする Azure IoT Hub の上限を回避するには、Stream Analytics ジョブごとにコンシューマー グループを指定するのが最良事例となります。

### <a name="configure-an-iot-hub-as-a-data-stream-input"></a>データ ストリーム入力として IoT Hub を構成する
次の表に、入力として IoT Hub を構成する場合に使用する、Azure Portal の **[新しい入力]** ブレードにある各プロパティの説明を示しています。

| プロパティ | Description |
| --- | --- |
| **入力のエイリアス** |この入力を参照するジョブのクエリで使用するわかりやすい名前。|
| **IoT Hub** |入力として使用する IoT Hub の名前。 |
| **エンドポイント** |IoT Hub のエンドポイント。|
| **共有アクセス ポリシー名** |IoT Hub へのアクセスを提供する共有アクセス ポリシー。 各共有アクセス ポリシーには、名前、設定したアクセス許可、アクセス キーが含まれています。 |
| **共有アクセス ポリシー キー** |IoT Hub へのアクセスを承認するために使用する共有アクセス キー。 |
| **コンシューマー グループ** (省略可能) |IoT Hub からのデータを取り込むために使用するコンシューマー グループです。 コンシューマー グループが指定されていない場合、Stream Analytics ジョブは既定のコンシューマー グループを使用します。 Stream Analytics ジョブごとに個別のコンシューマー グループを使用することをお勧めします。 |
| **イベントのシリアル化の形式** |入ってくるデータ ストリームのシリアル化形式 (JSON、CSV、または Avro)。 |
| **Encoding** |現在のところ、UTF-8 が、唯一サポートされているエンコード形式です。 |

データが IoT Hub から送信される場合、Stream Analytics クエリでは次のメタデータ フィールドにアクセスできます。

| プロパティ | Description |
| --- | --- |
| **EventProcessedUtcTime** |イベントが処理された日時。 |
| **EventEnqueuedUtcTime** |IoT Hub でイベントを受信した日時。 |
| **PartitionId** |入力アダプターの 0 から始まるパーティション ID。 |
| **IoTHub.MessageId** | IoT Hub での双方向通信の関連付けに使用する ID。 |
| **IoTHub.CorrelationId** |IoT Hub でのメッセージ応答とフィードバックに使用する ID。 |
| **IoTHub.ConnectionDeviceId** |このメッセージの送信に使用される認証 ID。 この値は、IoT Hub により servicebound メッセージにスタンプされます。 |
| **IoTHub.ConnectionDeviceGenerationId** |このメッセージの送信に使用された認証済みデバイスの世代 ID。 この値は、IoT Hub により servicebound メッセージにスタンプされます。 |
| **IoTHub.EnqueuedTime** |IoT Hub でメッセージを受信した時刻。 |
| **IoTHub.StreamId** |送信元デバイスにより追加されたカスタム イベント プロパティ。 |


## <a name="create-data-stream-input-from-blob-storage"></a>Blob Storage からデータ ストリーム入力を作成する
大量の非構造化データをクラウドに保存する場合は、Azure Blob Storage を使用するとコスト効率の高いスケーラブルなソリューションが実現します。 通常、Blob Storage のデータは保存データと見なされます。 ただし、このデータは Stream Analytics でデータ ストリームとして処理できます。 Stream Analytics による Blob Storage 入力の一般的なシナリオは、ログの処理です。 このシナリオでは、利用統計情報がシステムから取得されます。この情報を解析および処理して意味のあるデータを抽出する必要があります。

Stream Analytics の Blob Storage イベントの既定のタイムスタンプは BLOB が最後に変更されたときのタイムスタンプです。このタイムスタンプが `BlobLastModifiedUtcTime` です。 イベント ペイロードのタイムスタンプを利用してデータをストリームとして処理するには、[TIMESTAMP BY](https://msdn.microsoft.com/library/azure/dn834998.aspx) キーワードを使用する必要があります。

CSV 形式の入力についても、データ セットのフィールドを定義するためのヘッダー行が "*必須*" です。 また、ヘッダー行のフィールドはすべて一意であることが必要です。

> [!NOTE]
> Stream Analytics では、既存の BLOB ファイルにコンテンツを追加できません。 Stream Analytics は各ファイルを 1 回だけ表示します。ジョブがデータを読み取った後にファイルで発生した変更は処理されません。 ベスト プラクティスとして、すべての BLOB ファイルのデータを一度にアップロードし、追加の新しいイベントを、別の新しい BLOB ファイルに追加することをお勧めします。
> 

### <a name="configure-blob-storage-as-a-data-stream-input"></a>データ ストリーム入力として Blob Storage を構成する

次の表に、入力として Blob Storage を構成する場合に使用する、Azure Portal の **[新しい入力]** ブレードにある各プロパティの説明を示しています。

| プロパティ | Description |
| --- | --- |
| **入力のエイリアス** | この入力を参照するジョブのクエリで使用するわかりやすい名前。 |
| **ストレージ アカウント** | BLOB ファイルが配置されるストレージ アカウントの名前。 |
| **ストレージ アカウント キー** | ストレージ アカウントに関連付けられている秘密キー。 |
| **コンテナー** | BLOB 入力のコンテナーです。 コンテナーにより、Microsoft Azure BLOB サービスに格納される BLOB が論理的にグループ化されます。 BLOB を Azure Blob Storage サービスにアップロードするとき、その BLOB のコンテナーを指定する必要があります。 |
| **パス パターン** (省略可能) | 指定されたコンテナー内に BLOB を配置するために使用されるファイル パス。 このパス内に、3 つの変数 (`{date}`、`{time}`、`{partition}`) の 1 つ以上のインスタンスを指定できます。<br/><br/>例 1: `cluster1/logs/{date}/{time}/{partition}`<br/><br/>例 2: `cluster1/logs/{date}`<br/><br/>`*` 文字はパス プレフィックスの許容値ではありません。 許容値は、有効な <a HREF="https://msdn.microsoft.com/library/azure/dd135715.aspx">Azure BLOB 文字</a>のみです。 |
| **日付形式** (省略可能) | パスで日付変数を使用する場合は、ファイルを編成する日付形式です。 例: `YYYY/MM/DD` |
| **時刻形式** (省略可能) |  パスで時刻変数を使用する場合は、ファイルを編成する時刻形式です。 現在唯一サポートされている値は `HH` です。 |
| **イベントのシリアル化の形式** | 入ってくるデータ ストリームのシリアル化形式 (JSON、CSV、または Avro)。 |
| **Encoding** | CSV と JSON では、現在のところ、UTF-8 が唯一サポートされているエンコード形式です。 |

データが Blob Storage のソースから送信される場合、Stream Analytics クエリでは次のメタデータ フィールドにアクセスできます。

| プロパティ | Description |
| --- | --- |
| **BlobName** |イベントに起因する入力 BLOB の名前。 |
| **EventProcessedUtcTime** |Stream Analytics によってイベントが処理された日時。 |
| **BlobLastModifiedUtcTime** |BLOB が最後に変更された日時。 |
| **PartitionId** |入力アダプターの 0 から始まるパーティション ID。 |

たとえば、これらのフィールドを使用して、次の例のようにクエリを記述できます。

````
SELECT
    BlobName,
    EventProcessedUtcTime,
    BlobLastModifiedUtcTime
FROM Input
````

## <a name="get-help"></a>問い合わせ
さらにサポートが必要な場合は、 [Azure Stream Analytics フォーラム](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics)を参照してください。

## <a name="next-steps"></a>次のステップ
ここでは、Azure での Stream Analytics ジョブのデータ接続オプションについて説明しました。 Stream Analytics の詳細については、以下を参照してください。

* [Azure Stream Analytics の使用](stream-analytics-real-time-fraud-detection.md)
* [Azure Stream Analytics ジョブのスケーリング](stream-analytics-scale-jobs.md)
* [Stream Analytics Query Language Reference (Stream Analytics クエリ言語リファレンス)](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Azure Stream Analytics management REST API reference (Azure ストリーム分析の管理 REST API リファレンス)](https://msdn.microsoft.com/library/azure/dn835031.aspx)

<!--Link references-->
[stream.analytics.developer.guide]: ../stream-analytics-developer-guide.md
[stream.analytics.scale.jobs]: stream-analytics-scale-jobs.md
[stream.analytics.introduction]: stream-analytics-introduction.md
[stream.analytics.get.started]: stream-analytics-real-time-fraud-detection.md
[stream.analytics.query.language.reference]: http://go.microsoft.com/fwlink/?LinkID=513299
[stream.analytics.rest.api.reference]: http://go.microsoft.com/fwlink/?LinkId=517301

