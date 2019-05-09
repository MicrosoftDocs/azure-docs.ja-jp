---
title: Azure IoT Hub の正常性の監視 | Microsoft Docs
description: Azure Monitor と Azure Resource Health を使用して IoT Hub を監視し、問題を迅速に診断します
author: kgremban
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 02/27/2019
ms.author: kgremban
ms.openlocfilehash: 6dea1add1e329cfc894068732898a856a69c9b4c
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/18/2019
ms.locfileid: "59274044"
---
# <a name="monitor-the-health-of-azure-iot-hub-and-diagnose-problems-quickly"></a>Azure IoT Hub の正常性を監視し、問題をすばやく診断する

Azure IoT Hub を実装する企業では、そのリソースに信頼性の高いパフォーマンスを期待します。 操作の緊密な監視を維持できるように、IoT Hub は、[Azure Monitor](../azure-monitor/index.yml) と [Azure Resource Health](../service-health/resource-health-overview.md) と完全に統合されています。 これら 2 つのサービスが連携して、IoT ソリューションを常に正常な状態で稼働させるために必要なデータが提供されます。

Azure Monitor は、すべての Azure サービスの監視およびログの唯一のソースです。 Azure Monitor が生成する診断ログを Azure Monitor ログ、Event Hubs、または Azure Storage に送信して、カスタムの処理を実行できます。 Azure Monitor のメトリックと診断の設定により、リソースのパフォーマンスを把握できます。 この記事を読み進めると、IoT Hub で [Azure Monitor を使用する](#use-azure-monitor)方法を理解することができます。 

> [!IMPORTANT]
> Azure Monitor の診断ログを使用して IoT Hub サービスによって出力されるイベントは、信頼性も順序も保証されません。 一部のイベントが失われたり、順序どおりに配信されないことがあります。 さらに、診断ログはリアルタイムではなく、選択した出力先に記録されるまで数分かかる場合があります。

Azure での問題がお客様のリソースに影響を及ぼす場合は、Azure Resource Health によって診断とサポートを受けられます。 ダッシュボードには、お使いの各 IoT ハブの現在と過去の正常性の状態が表示されます。 この記事の最後のセクションで、IoT ハブで [Azure Resource Health を使用する](#use-azure-resource-health)方法について説明します。 

IoT Hub には、IoT リソースの状態を把握するために利用できる独自のメトリックも用意されています。 詳細については、「[IoT Hub メトリックの理解](iot-hub-metrics.md)」を参照してください。

## <a name="use-azure-monitor"></a>Azure Monitor の使用

Azure Monitor は、Azure リソースの診断情報を提供します。これは、IoT Hub 内で実行される操作を監視できることを意味します。

Azure Monitor の診断設定は、IoT Hub の操作の監視機能を置き換えます。 現在、操作の監視機能を使用している場合は、ワークフローを移行する必要があります。 詳細については、[操作の監視から診断設定への移行](iot-hub-migrate-to-diagnostics-settings.md)に関するページを参照してください。

Azure Monitor が監視する特定のメトリックとイベントの詳細については、「[Azure Monitor のサポートされるメトリック](../azure-monitor/platform/metrics-supported.md)」と「[Azure 診断ログでサポートされているサービス、スキーマ、カテゴリ](../azure-monitor/platform/diagnostic-logs-schema.md)」を参照してください。

[!INCLUDE [iot-hub-diagnostics-settings](../../includes/iot-hub-diagnostics-settings.md)]

### <a name="understand-the-logs"></a>ログを理解する

Azure Monitor を使用すると、IoT Hub で発生するさまざまな操作を追跡できます。 各カテゴリには、そのカテゴリ内のイベントの報告方法を定義するスキーマがあります。

#### <a name="connections"></a>Connections

接続カテゴリは、エラーだけでなく、IoT Hub に対するデバイスの接続と切断イベントも追跡します。 このカテゴリは、承認されていない接続の試行を識別したり、デバイスへの接続を失ったときに警告したりするのに役立ちます。

> [!NOTE]
> デバイスの信頼できる接続状態については、「[デバイスのハートビート](iot-hub-devguide-identity-registry.md#device-heartbeat)」を参照してください。

```json
{
   "records":
   [
        {
            "time": " UTC timestamp",
            "resourceId": "Resource Id",
            "operationName": "deviceConnect",
            "category": "Connections",
            "level": "Information",
            "properties": "{\"deviceId\":\"<deviceId>\",\"protocol\":\"<protocol>\",\"authType\":\"{\\\"scope\\\":\\\"device\\\",\\\"type\\\":\\\"sas\\\",\\\"issuer\\\":\\\"iothub\\\",\\\"acceptingIpFilterRule\\\":null}\",\"maskedIpAddress\":\"<maskedIpAddress>\"}",
            "location": "Resource location"
        }
    ]
}
```

#### <a name="cloud-to-device-commands"></a>クラウドからデバイスへのコマンド

C2D コマンド カテゴリでは、IoT Hub で発生し、かつクラウドからデバイスへのメッセージ パイプラインに関連しているエラーを追跡します。 このカテゴリには、以下から発生するエラーが含まれます。

* 送信 cloud-to-device メッセージ (承認されていない送信者のエラーなど)、
* 受信 cloud-to-device メッセージ (配信数超過のエラーなど)、および
* 受信 cloud-to-device メッセージのフィードバック (フィードバックの有効期限切れのエラーなど)。

このカテゴリでは、cloud-to-device メッセージが正常に配信されたときのエラーはキャッチされませんが、デバイスによって不適切に処理されたときのエラーはキャッチされます。

```json
{
    "records":
    [
        {
            "time": " UTC timestamp",
            "resourceId": "Resource Id",
            "operationName": "messageExpired",
            "category": "C2DCommands",
            "level": "Error",
            "resultType": "Event status",
            "resultDescription": "MessageDescription",
            "properties": "{\"deviceId\":\"<deviceId>\",\"messageId\":\"<messageId>\",\"messageSizeInBytes\":\"<messageSize>\",\"protocol\":\"Amqp\",\"deliveryAcknowledgement\":\"<None, NegativeOnly, PositiveOnly, Full>\",\"deliveryCount\":\"0\",\"expiryTime\":\"<timestamp>\",\"timeInSystem\":\"<timeInSystem>\",\"ttl\":<ttl>, \"EventProcessedUtcTime\":\"<UTC timestamp>\",\"EventEnqueuedUtcTime\":\"<UTC timestamp>\", \"maskedIpAddress\": \"<maskedIpAddress>\", \"statusCode\": \"4XX\"}",
            "location": "Resource location"
        }
    ]
}
```

#### <a name="device-identity-operations"></a>デバイス ID の操作

デバイス ID の操作のカテゴリでは、IoT Hub の ID レジストリ内でエントリの作成、更新、または削除を試みたときに発生するエラーを追跡します。 このカテゴリの追跡は、プロビジョニングのシナリオで便利です。

```json
{
    "records":
    [
        {
            "time": "UTC timestamp",
            "resourceId": "Resource Id",
            "operationName": "get",
            "category": "DeviceIdentityOperations",
            "level": "Error",
            "resultType": "Event status",
            "resultDescription": "MessageDescription",
            "properties": "{\"maskedIpAddress\":\"<maskedIpAddress>\",\"deviceId\":\"<deviceId>\", \"statusCode\":\"4XX\"}",
            "location": "Resource location"
        }
    ]
}
```

#### <a name="routes"></a>Routes

メッセージ ルーティング カテゴリは、メッセージ ルート評価および IoT Hub によって認識されるエンドポイント正常性において発生するエラーを追跡します。 このカテゴリには、以下のようなイベントが含まれます。

* "未定義" に評価されるルール、
* IoT Hub がエンドポイントをデッドとしてマークしている、または
* エンドポイントから受信したすべてのエラー。 

このカテゴリには、メッセージ自体に関する具体的なエラー (デバイス調整エラーなど) は含まれません。このようなエラーは、"デバイス テレメトリ" カテゴリで報告されます。

```json
{
    "records":
    [
        {
            "time": "UTC timestamp",
            "resourceId": "Resource Id",
            "operationName": "endpointUnhealthy",
            "category": "Routes",
            "level": "Error",
            "properties": "{\"deviceId\": \"<deviceId>\",\"endpointName\":\"<endpointName>\",\"messageId\":<messageId>,\"details\":\"<errorDetails>\",\"routeName\": \"<routeName>\"}",
            "location": "Resource location"
        }
    ]
}
```

#### <a name="device-telemetry"></a>デバイス テレメトリ

デバイス テレメトリのカテゴリでは、IoT Hub で発生し、かつテレメトリ パイプラインに関連しているエラーを追跡します。 このカテゴリーには、テレメトリ イベントの送信時に発生したエラー (スロットルなど) やテレメトリ イベントの受信時に発生したエラー (許可されていないリーダーなど) が含まれます。 このカテゴリでは、デバイス自体で実行されているコードに起因するエラーをキャッチできません。

```json
{
    "records":
    [
        {
            "time": "UTC timestamp",
            "resourceId": "Resource Id",
            "operationName": "ingress",
            "category": "DeviceTelemetry",
            "level": "Error",
            "resultType": "Event status",
            "resultDescription": "MessageDescription",
            "properties": "{\"deviceId\":\"<deviceId>\",\"batching\":\"0\",\"messageSizeInBytes\":\"<messageSizeInBytes>\",\"EventProcessedUtcTime\":\"<UTC timestamp>\",\"EventEnqueuedUtcTime\":\"<UTC timestamp>\",\"partitionId\":\"1\"}", 
            "location": "Resource location"
        }
    ]
}
```

#### <a name="file-upload-operations"></a>ファイルのアップロード操作

ファイルのアップロード カテゴリでは、IoT Hub で発生し、かつファイルのアップロード機能に関連しているエラーを追跡します。 このカテゴリには、次のエラーが含まれます。

* SAS URI で発生したエラー (デバイスがアップロード完了をハブに通知する前に期限切れになった、など)。

* デバイスによって報告されたアップロード エラー。

* IoT Hub 通知メッセージの作成中にストレージでファイルが見つからないときに発生するエラー。

このカテゴリでは、デバイスがファイルをストレージにアップロードしているときに直接発生したエラーをキャッチできません。

```json
{
    "records":
    [
        {
            "time": "UTC timestamp",
            "resourceId": "Resource Id",
            "operationName": "ingress",
            "category": "FileUploadOperations",
            "level": "Error",
            "resultType": "Event status",
            "resultDescription": "MessageDescription",
            "durationMs": "1",
            "properties": "{\"deviceId\":\"<deviceId>\",\"protocol\":\"<protocol>\",\"authType\":\"{\\\"scope\\\":\\\"device\\\",\\\"type\\\":\\\"sas\\\",\\\"issuer\\\":\\\"iothub\\\",\\\"acceptingIpFilterRule\\\":null}\",\"blobUri\":\"http//bloburi.com\"}",
            "location": "Resource location"
        }
    ]
}
```

#### <a name="cloud-to-device-twin-operations"></a>クラウドからデバイスへのツイン操作

クラウドからデバイスへのツイン操作カテゴリでは、デバイス ツイン上のサービスによって開始されたイベントを追跡します。 これらの操作には、ツインの取得、タグの更新または置換、および必要なプロパティの更新または置換が含まれます。

```json
{
    "records":
    [
        {
            "time": "UTC timestamp",
            "resourceId": "Resource Id",
            "operationName": "read",
            "category": "C2DTwinOperations",
            "level": "Information",
            "durationMs": "1",
            "properties": "{\"deviceId\":\"<deviceId>\",\"sdkVersion\":\"<sdkVersion>\",\"messageSize\":\"<messageSize>\"}",
            "location": "Resource location"
        }
    ]
}
```

#### <a name="device-to-cloud-twin-operations"></a>デバイスからクラウドへのツイン操作

デバイスからクラウドへのツイン操作カテゴリでは、デバイス ツイン上のデバイスによって開始されたイベントを追跡します。 これらの操作には、ツインの取得、報告されたプロパティの更新、および必要なプロパティへのサブスクライブが含まれます。

```json
{
    "records":
    [
        {
            "time": "UTC timestamp",
            "resourceId": "Resource Id",
            "operationName": "update",
            "category": "D2CTwinOperations",
            "level": "Information",
            "durationMs": "1",
            "properties": "{\"deviceId\":\"<deviceId>\",\"protocol\":\"<protocol>\",\"authenticationType\":\"{\\\"scope\\\":\\\"device\\\",\\\"type\\\":\\\"sas\\\",\\\"issuer\\\":\\\"iothub\\\",\\\"acceptingIpFilterRule\\\":null}\"}",
            "location": "Resource location"
        }
    ]
}
```

#### <a name="twin-queries"></a>ツイン クエリ

ツイン クエリのカテゴリでは、クラウドで開始されたデバイス ツインのクエリ要求を報告します。

```json
{
    "records":
    [
        {
            "time": "UTC timestamp",
            "resourceId": "Resource Id",
            "operationName": "query",
            "category": "TwinQueries",
            "level": "Information",
            "durationMs": "1",
            "properties": "{\"query\":\"<twin query>\",\"sdkVersion\":\"<sdkVersion>\",\"messageSize\":\"<messageSize>\",\"pageSize\":\"<pageSize>\", \"continuation\":\"<true, false>\", \"resultSize\":\"<resultSize>\"}",
            "location": "Resource location"
        }
    ]
}
```

#### <a name="jobs-operations"></a>ジョブ操作

ジョブ操作カテゴリでは、デバイス ツインを更新するジョブ要求または複数のデバイスでダイレクト メソッドを呼び出すジョブ要求を報告します。 これらの要求は、クラウドで開始されます。

```json
{
    "records":
    [
        {
            "time": "UTC timestamp",
            "resourceId": "Resource Id",
            "operationName": "jobCompleted",
            "category": "JobsOperations",
            "level": "Information",
            "durationMs": "1",
            "properties": "{\"jobId\":\"<jobId>\", \"sdkVersion\": \"<sdkVersion>\",\"messageSize\": <messageSize>,\"filter\":\"DeviceId IN ['1414ded9-b445-414d-89b9-e48e8c6285d5']\",\"startTimeUtc\":\"Wednesday, September 13, 2017\",\"duration\":\"0\"}",
            "location": "Resource location"
        }
    ]
}
```

#### <a name="direct-methods"></a>ダイレクト メソッド

ダイレクト メソッド カテゴリでは、個々のデバイスに送信される要求 - 応答のインタラクションを追跡します。 これらの要求は、クラウドで開始されます。

```json
{
    "records":
    [
        {
            "time": "UTC timestamp",
            "resourceId": "Resource Id",
            "operationName": "send",
            "category": "DirectMethods",
            "level": "Information",
            "durationMs": "1",
            "properties": "{\"deviceId\":<messageSize>, \"RequestSize\": 1, \"ResponseSize\": 1, \"sdkVersion\": \"2017-07-11\"}",
            "location": "Resource location"
        }
    ]
}
```

#### <a name="distributed-tracing-preview"></a>分散トレース (プレビュー)

分散トレース カテゴリでは、トレース コンテキスト ヘッダーが含まれれるメッセージの関連付け ID が追跡されます。 これらのログを完全に有効にするには、[IoT Hub 分散トレース (プレビュー) を使用した IoT アプリケーションの エンド ツー エンドの分析と診断](iot-hub-distributed-tracing.md)に関する記事に従って、クライアント側のコードを更新する必要があります。

`correlationId` が [W3C トレース コンテキスト](https://github.com/w3c/trace-context)の提案に準拠していることに注意してください。これには、`trace-id` と `span-id` が含まれます。

##### <a name="iot-hub-d2c-device-to-cloud-logs"></a>IoT Hub D2C (device-to-cloud) のログ

IoT Hub では、有効なトレース プロパティを含むメッセージが IoT Hub に到着すると、このログが記録されます。

```json
{
    "records":
    [
        {
            "time": "UTC timestamp",
            "resourceId": "Resource Id",
            "operationName": "DiagnosticIoTHubD2C",
            "category": "DistributedTracing",
            "correlationId": "00-8cd869a412459a25f5b4f31311223344-0144d2590aacd909-01",
            "level": "Information",
            "resultType": "Success",
            "resultDescription":"Receive message success",
            "durationMs": "",
            "properties": "{\"messageSize\": 1, \"deviceId\":\"<deviceId>\", \"callerLocalTimeUtc\": : \"2017-02-22T03:27:28.633Z\", \"calleeLocalTimeUtc\": \"2017-02-22T03:27:28.687Z\"}",
            "location": "Resource location"
        }
    ]
}
```

ここで、IoT Hub のクロックがデバイスのクロックと同期していない可能性があり、経過時間を計算すると誤解を招く場合があるので、`durationMs` は計算されません。 `properties` セクションのタイムスタンプを使用するロジックを記述して、device-to-cloud 待機時間のスパイクをキャプチャすることをお勧めします。

| プロパティ | Type | 説明 |
|--------------------|-----------------------------------------------|------------------------------------------------------------------------------------------------|
| **messageSize** | 整数 | device-to-cloud メッセージのサイズ (バイト単位) |
| **deviceId** | ASCII の 7 ビットの英数字の文字列 | デバイスの ID |
| **callerLocalTimeUtc** | UTC タイムスタンプ | デバイスのローカル クロックによって報告されたメッセージの作成時刻 |
| **calleeLocalTimeUtc** | UTC タイムスタンプ | IoT Hub サービス側のクロックによって報告された、IoT Hub のゲートウェイへのメッセージの到着時刻 |

##### <a name="iot-hub-ingress-logs"></a>IoT Hub のイングレス ログ

有効なトレース プロパティを含むメッセージが内部または組み込みの Event Hub に書き込むと、IoT Hub でこのログが記録されます。

```json
{
    "records":
    [
        {
            "time": "UTC timestamp",
            "resourceId": "Resource Id",
            "operationName": "DiagnosticIoTHubIngress",
            "category": "DistributedTracing",
            "correlationId": "00-8cd869a412459a25f5b4f31311223344-349810a9bbd28730-01",
            "level": "Information",
            "resultType": "Success",
            "resultDescription":"Ingress message success",
            "durationMs": "10",
            "properties": "{\"isRoutingEnabled\": \"true\", \"parentSpanId\":\"0144d2590aacd909\"}",
            "location": "Resource location"
        }
    ]
}
```

`properties` セクションでは、このログにはメッセージのイングレスに関する追加情報が含まれています

| プロパティ | Type | 説明 |
|--------------------|-----------------------------------------------|------------------------------------------------------------------------------------------------|
| **isRoutingEnabled** | String | true または false。IoT Hub でメッセージのルーティングが有効になっているかどうかを示します |
| **parentSpanId** | String | 親メッセージの [span-id](https://w3c.github.io/trace-context/#parent-id)。この場合は、D2C のメッセージ トレースです |

##### <a name="iot-hub-egress-logs"></a>IoT Hub のエグレス ログ

[ルーティング](iot-hub-devguide-messages-d2c.md)が有効になっていて、メッセージが[エンドポイント](iot-hub-devguide-endpoints.md)に書き込まれると、IoT Hub でこのログが記録されます。 ルーティングが有効でない場合、IoT Hub はこのログを記録しません。

```json
{
    "records":
    [
        {
            "time": "UTC timestamp",
            "resourceId": "Resource Id",
            "operationName": "DiagnosticIoTHubEgress",
            "category": "DistributedTracing",
            "correlationId": "00-8cd869a412459a25f5b4f31311223344-98ac3578922acd26-01",
            "level": "Information",
            "resultType": "Success",
            "resultDescription":"Egress message success",
            "durationMs": "10",
            "properties": "{\"endpointType\": \"EventHub\", \"endpointName\": \"myEventHub\", \"parentSpanId\":\"349810a9bbd28730\"}",
            "location": "Resource location"
        }
    ]
}
```

`properties` セクションでは、このログにはメッセージのイングレスに関する追加情報が含まれています

| プロパティ | Type | 説明 |
|--------------------|-----------------------------------------------|------------------------------------------------------------------------------------------------|
| **endpointName** | String | ルーティング エンドポイントの名前 |
| **endpointType** | String | ルーティング エンドポイントの種類 |
| **parentSpanId** | String | 親メッセージの [span-id](https://w3c.github.io/trace-context/#parent-id)。この場合は、IoT Hub のイングレス メッセージ トレースです |

### <a name="read-logs-from-azure-event-hubs"></a>Azure Event Hubs からのログの読み取り

診断設定を使用してイベント ログを設定した後は、ログを読み取るアプリケーションを作成できます。これにより、ログの情報を基にしてアクションを実行できるようになります。 イベント ハブからログを取得するサンプル コードを次に示します。

```csharp
class Program
{ 
    static string connectionString = "{your AMS eventhub endpoint connection string}";
    static string monitoringEndpointName = "{your AMS event hub endpoint name}";
    static EventHubClient eventHubClient;
    //This is the Diagnostic Settings schema
    class AzureMonitorDiagnosticLog
    {
        string time { get; set; }
        string resourceId { get; set; }
        string operationName { get; set; }
        string category { get; set; }
        string level { get; set; }
        string resultType { get; set; }
        string resultDescription { get; set; }
        string durationMs { get; set; }
        string callerIpAddress { get; set; }
        string correlationId { get; set; }
        string identity { get; set; }
        string location { get; set; }
        Dictionary<string, string> properties { get; set; }
    };

    static void Main(string[] args)
    {
        Console.WriteLine("Monitoring. Press Enter key to exit.\n");
        eventHubClient = EventHubClient.CreateFromConnectionString(connectionString, monitoringEndpointName);
        var d2cPartitions = eventHubClient.GetRuntimeInformationAsync().PartitionIds;
        CancellationTokenSource cts = new CancellationTokenSource();
        var tasks = new List<Task>();
        foreach (string partition in d2cPartitions)
        {
            tasks.Add(ReceiveMessagesFromDeviceAsync(partition, cts.Token));
        }
        Console.ReadLine();
        Console.WriteLine("Exiting...");
        cts.Cancel();
        Task.WaitAll(tasks.ToArray());
    }

    private static async Task ReceiveMessagesFromDeviceAsync(string partition, CancellationToken ct)
    {
        var eventHubReceiver = eventHubClient.GetDefaultConsumerGroup().CreateReceiver(partition, DateTime.UtcNow);
        while (true)
        {
            if (ct.IsCancellationRequested)
            {
                await eventHubReceiver.CloseAsync();
                break;
            }
            EventData eventData = await eventHubReceiver.ReceiveAsync(new TimeSpan(0,0,10));
            if (eventData != null)
            {
                string data = Encoding.UTF8.GetString(eventData.GetBytes());
                Console.WriteLine("Message received. Partition: {0} Data: '{1}'", partition, data);
                var deserializer = new JavaScriptSerializer();
                //deserialize json data to azure monitor object
                AzureMonitorDiagnosticLog message = new JavaScriptSerializer().Deserialize<AzureMonitorDiagnosticLog>(result);
            }
        }
    }
}
```

## <a name="use-azure-resource-health"></a>Azure Resource Health の使用

Azure Resource Health を使用すると、IoT Hub が実行中かどうかを監視できます。 また、リージョンで発生した停電が IoT Hub の正常性に影響を与えているかどうかを知ることもできます。 Azure IoT Hub の正常性状態に関する詳細を理解するには、[Azure Monitor を使用する](#use-azure-monitor)ことをお勧めします。

Azure IoT Hub では、リージョン レベルでの正常性が示されます。 リージョンでの停電が IoT ハブに影響している場合、正常性状態は **[不明]** と表示されます。 詳細については、「[Azure Resource Health で利用できるリソースの種類と正常性チェック](../service-health/resource-health-checks-resource-types.md)」を参照してください。

IoT Hub の正常性を確認するには、次の手順を実行します。

1. [Azure Portal](https://portal.azure.com) にサインインします。

2. **[Service Health]\(サービス正常性\)** > **[リソース正常性]** に移動します。

3. ドロップダウン ボックスから、サブスクリプションを選択し、リソースの種類として **[IoT ハブ]** を選択します。

正常性データを解釈する方法の詳細については、「[Azure Resource Health の概要](../service-health/resource-health-overview.md)」を参照してください。

## <a name="next-steps"></a>次の手順

* [IoT Hub メトリックの理解](iot-hub-metrics.md)
* [Azure Logic Apps で IoT Hub とメールボックスに接続した状態での IoT リモート監視と通知](iot-hub-monitoring-notifications-with-azure-logic-apps.md)