---
title: Azure IoT Hub 操作の監視 (非推奨) | Microsoft Docs
description: Azure IoT Hub 操作の監視を使用して、IoT Hub に対する操作の状態をリアルタイムで監視する方法。
author: robinsh
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 03/11/2019
ms.author: robinsh
ms.custom: amqp, devx-track-csharp
ms.openlocfilehash: 045d5693c4388c6285bc6983ac2a385ceac9f6d0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "94408126"
---
# <a name="iot-hub-operations-monitoring-deprecated"></a>IoT Hub 操作の監視 (非推奨)

IoT Hub の操作の監視では、IoT Hub に対する操作の状態をリアルタイムで監視することができます。 IoT Hub は、複数のカテゴリにまたがる操作のイベントを追跡します。 1 つ以上のカテゴリから IoT ハブのエンドポイントにイベントを送信して処理するように選択することができます。 データを監視してエラーがないか確認したり、データ パターンに基づいてより複雑な処理をセットアップしたりできます。

>[!NOTE]
>IoT Hub の **操作の監視は非推奨になっており、2019 年 3 月 10 日をもって IoT Hub から削除される予定です**。 IoT Hub の操作と正常性の監視については、[IoT Hub の監視](monitor-iot-hub.md)に関するページを参照してください。 廃止のスケジュールについて詳しくは、「[Monitor your Azure IoT solutions with Azure Monitor and Azure Resource Health](https://azure.microsoft.com/blog/monitor-your-azure-iot-solutions-with-azure-monitor-and-azure-resource-health)」(Azure Monitor および Azure Resource Health による Azure IoT ソリューションの監視) をご覧ください。

IoT Hub では、次の 6 つのカテゴリのイベントを監視します。

* デバイス ID の操作
* デバイス テレメトリ
* クラウドからデバイスへのメッセージ
* 接続
* ファイルのアップロード
* メッセージ ルーティング

> [!IMPORTANT]
> IoT Hub の操作の監視では、イベントの信頼性および配信順序は保証されません。 IoT Hub の基になるインフラストラクチャによっては、一部のイベントが失われたり、順序どおりに配信されない可能性があります。 接続試行の失敗や、特定のデバイスの高頻度での切断など、エラーのシグナルに基づいてアラートを生成するには、操作の監視を使います。 デバイスの状態の一貫性のあるストアを作成するには、操作の監視イベントに依存しないでください (デバイスの接続または切断状態を追跡するストアなど)。 

## <a name="how-to-enable-operations-monitoring"></a>操作の監視を有効にする方法

1. IoT Hub を作成します。 IoT ハブの作成方法の手順については、[使用開始](quickstart-send-telemetry-dotnet.md)に関するガイドを参照してください。

2. IoT Hub のブレードを開きます。 このブレードで、 **[操作の監視]** をクリックします。

    ![ポータルでのアクセス操作監視の設定](./media/iot-hub-operations-monitoring/enable-OM-1.png)

3. 監視する監視カテゴリを選択し、 **[保存]** をクリックします。 イベントは、 **[監視の設定]** に一覧表示された Event Hub 対応のエンドポイントから読み取ることができます。 IoT Hub エンドポイントの名前は `messages/operationsmonitoringevents`です。

    ![IoT Hub での操作監視の設定](./media/iot-hub-operations-monitoring/enable-OM-2.png)

> [!NOTE]
> **[接続]** カテゴリに対して **[詳細]** 監視を選ぶと、IoT Hub は追加の診断メッセージを生成します。 他のすべてのカテゴリでは、 **[詳細]** 設定を選ぶと、IoT Hub が個々のエラー メッセージに含める情報の量が変わります。

## <a name="event-categories-and-how-to-use-them"></a>イベント カテゴリとその使用方法

操作監視の各カテゴリでは、IoT Hub との各種のやり取りを追跡します。各監視カテゴリは、カテゴリ内のイベントの構成方法を定義するスキーマを備えています。

### <a name="device-identity-operations"></a>デバイス ID の操作

デバイス ID の操作のカテゴリでは、IoT Hub の ID レジストリ内でエントリの作成、更新、または削除を試みたときに発生するエラーを追跡します。 このカテゴリの追跡は、プロビジョニングのシナリオで便利です。

```json
{
    "time": "UTC timestamp",
    "operationName": "create",
    "category": "DeviceIdentityOperations",
    "level": "Error",
    "statusCode": 4XX,
    "statusDescription": "MessageDescription",
    "deviceId": "device-ID",
    "durationMs": 1234,
    "userAgent": "userAgent",
    "sharedAccessPolicy": "accessPolicy"
}
```

### <a name="device-telemetry"></a>デバイス テレメトリ

デバイス テレメトリのカテゴリでは、IoT Hub で発生し、かつテレメトリ パイプラインに関連しているエラーを追跡します。 このカテゴリーには、テレメトリ イベントの送信時に発生したエラー (スロットルなど) やテレメトリ イベントの受信時に発生したエラー (許可されていないリーダーなど) が含まれます。 このカテゴリでは、デバイス自体で実行されているコードに起因するエラーをキャッチできません。

```json
{
    "messageSizeInBytes": 1234,
    "batching": 0,
    "protocol": "Amqp",
    "authType": "{\"scope\":\"device\",\"type\":\"sas\",\"issuer\":\"iothub\"}",
    "time": "UTC timestamp",
    "operationName": "ingress",
    "category": "DeviceTelemetry",
    "level": "Error",
    "statusCode": 4XX,
    "statusType": 4XX001,
    "statusDescription": "MessageDescription",
    "deviceId": "device-ID",
    "EventProcessedUtcTime": "UTC timestamp",
    "PartitionId": 1,
    "EventEnqueuedUtcTime": "UTC timestamp"
}
```

### <a name="cloud-to-device-commands"></a>クラウドからデバイスへのコマンド

C2D コマンド カテゴリでは、IoT Hub で発生し、かつクラウドからデバイスへのメッセージ パイプラインに関連しているエラーを追跡します。 このカテゴリには、クラウドからデバイスへのメッセージの送信時のエラー (許可されていない送信者など)、クラウドからデバイスへのメッセージの受信時のエラー (配信数が上限を超えているなど)、クラウドからデバイスへのメッセージ フィードバックの受信時のエラー (フィードバックの有効期限切れなど) が含まれます。 このカテゴリでは、クラウドからデバイスへのメッセージが正常に配信されてもクラウドからデバイスへのメッセージを適切に処理しないデバイスのエラーはキャッチしません。

```json
{
    "messageSizeInBytes": 1234,
    "authType": "{\"scope\":\"hub\",\"type\":\"sas\",\"issuer\":\"iothub\"}",
    "deliveryAcknowledgement": 0,
    "protocol": "Amqp",
    "time": " UTC timestamp",
    "operationName": "ingress",
    "category": "C2DCommands",
    "level": "Error",
    "statusCode": 4XX,
    "statusType": 4XX001,
    "statusDescription": "MessageDescription",
    "deviceId": "device-ID",
    "EventProcessedUtcTime": "UTC timestamp",
    "PartitionId": 1,
    "EventEnqueuedUtcTime": "UTC timestamp"
}
```

### <a name="connections"></a>接続

接続のカテゴリでは、デバイスが IoT Hub に接続したときに発生する、または IoT Hub から切断したときのエラーを追跡します。 このカテゴリの追跡は、許可されていない接続の試行を識別する場合、および接続状態が悪い領域内で接続が失われたタイミングを突き止める場合に便利です

```json
{
    "durationMs": 1234,
    "authType": "{\"scope\":\"hub\",\"type\":\"sas\",\"issuer\":\"iothub\"}",
    "protocol": "Amqp",
    "time": " UTC timestamp",
    "operationName": "deviceConnect",
    "category": "Connections",
    "level": "Error",
    "statusCode": 4XX,
    "statusType": 4XX001,
    "statusDescription": "MessageDescription",
    "deviceId": "device-ID"
}
```

### <a name="file-uploads"></a>ファイルのアップロード

ファイルのアップロード カテゴリでは、IoT Hub で発生し、かつファイルのアップロード機能に関連しているエラーを追跡します。 このカテゴリには、次のエラーが含まれます。

* SAS URI で発生したエラー (デバイスがアップロード完了をハブに通知する前に期限切れになった、など)。

* デバイスによって報告されたアップロード エラー。

* IoT Hub 通知メッセージの作成中にストレージでファイルが見つからないときに発生するエラー。

このカテゴリでは、デバイスがファイルをストレージにアップロードしているときに直接発生したエラーをキャッチできません。

```json
{
    "authType": "{\"scope\":\"hub\",\"type\":\"sas\",\"issuer\":\"iothub\"}",
    "protocol": "HTTP",
    "time": " UTC timestamp",
    "operationName": "ingress",
    "category": "fileUpload",
    "level": "Error",
    "statusCode": 4XX,
    "statusType": 4XX001,
    "statusDescription": "MessageDescription",
    "deviceId": "device-ID",
    "blobUri": "http//bloburi.com",
    "durationMs": 1234
}
```

### <a name="message-routing"></a>メッセージ ルーティング

メッセージ ルーティング カテゴリは、メッセージ ルート評価および IoT Hub によって認識されるエンドポイント正常性において発生するエラーを追跡します。 このカテゴリには、ルールが "未定義" と評価されたとき、IoT Hub がエンドポイントをデッドとしてマークしたとき、およびエンドポイントから受信したその他のすべてのエラーなどのイベントが含まれます。 このカテゴリには、メッセージ自体に関する具体的なエラー (デバイス調整エラーなど) は含まれません。このようなエラーは、"デバイス テレメトリ" カテゴリで報告されます。

```json
{
    "messageSizeInBytes": 1234,
    "time": "UTC timestamp",
    "operationName": "ingress",
    "category": "routes",
    "level": "Error",
    "deviceId": "device-ID",
    "messageId": "ID of message",
    "routeName": "myroute",
    "endpointName": "myendpoint",
    "details": "ExternalEndpointDisabled"
}
```

## <a name="connect-to-the-monitoring-endpoint"></a>監視エンドポイントへの接続

IoT Hub での監視エンドポイントは、Event Hub と互換性のあるエンドポイントです。 Event Hub で動作する任意のメカニズムを使用して、このエンドポイントから監視メッセージを読み取ることができます。 次のサンプルで作成される基本的なリーダーは、高スループットのデプロイには適していません。 Event Hubs からのメッセージを処理する方法の詳細については、「 [Event Hubs の使用](../event-hubs/event-hubs-dotnet-standard-getstarted-send.md) 」のチュートリアルを参照してください。

監視エンドポイントに接続するには、接続文字列とエンドポイント名が必要です。 次の手順は、ポータルで必要な値を検索する方法を示しています。

1. ポータルで、IoT Hub リソース ブレードに移動します。

2. **[操作の監視]** を選択して、 **[Event Hub 互換名]** と **[Event Hub 互換エンドポイント]** の値をメモします。

    ![Event Hub 互換エンドポイントの値](./media/iot-hub-operations-monitoring/monitoring-endpoint.png)

3. **[共有アクセス ポリシー]** を選択し、 **[サービス]** を選択します。 **[主キー]** の値をメモします。

    ![サービスの共有アクセス ポリシーの主キー](./media/iot-hub-operations-monitoring/service-key.png)

次の C# コード サンプルは、Visual Studio の **Windows クラシック デスクトップ** C# コンソール アプリからの抜粋です。 このプロジェクトでは、**WindowsAzure.ServiceBus** NuGet パッケージがインストールされています。

* 次の例に示されているように、接続文字列プレースホルダーを、以前にメモした **Event Hub 互換エンドポイント** とサービスの **主キー** の値に置き換えます。

    ```csharp
    "Endpoint={your Event Hub-compatible endpoint};SharedAccessKeyName=service;SharedAccessKey={your service primary key value}"
    ```

* 監視エンドポイント名プレース ホルダーを、以前にメモした **Event Hub 互換名** に置き換えます。

```csharp
class Program
{
    static string connectionString = "{your monitoring endpoint connection string}";
    static string monitoringEndpointName = "{your monitoring endpoint name}";
    static EventHubClient eventHubClient;

    static void Main(string[] args)
    {
        Console.WriteLine("Monitoring. Press Enter key to exit.\n");

        eventHubClient = EventHubClient.CreateFromConnectionString(connectionString, monitoringEndpointName);
        var d2cPartitions = eventHubClient.GetRuntimeInformation().PartitionIds;
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
            }
        }
    }
}
```

## <a name="next-steps"></a>次のステップ

Azure Monitor を使用してさらに探索し、IoT Hub を監視するには、次を参照してください。

* [IoT Hub の監視](monitor-iot-hub.md)

* [IoT Hub 操作の監視から Azure Monitor に移行する](iot-hub-migrate-to-diagnostics-settings.md)