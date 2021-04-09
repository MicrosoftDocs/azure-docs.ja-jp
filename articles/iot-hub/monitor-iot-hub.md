---
title: Azure IoT Hub の監視
description: Azure IoT Hub を監視する方法については、こちらでご確認ください
author: robinsh
ms.author: robinsh
ms.topic: conceptual
ms.service: iot-hub
ms.custom: subject-monitoring
ms.date: 11/06/2020
ms.openlocfilehash: c9e6107e981d2bae96c5d9f368c8122d3ce245f0
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "100570948"
---
# <a name="monitoring-azure-iot-hub"></a>Azure IoT Hub の監視

Azure リソースに依存するクリティカルなアプリケーションとビジネス プロセスがある場合は、それらのリソースの可用性、パフォーマンス、操作を監視する必要があります。 この記事では、Azure IoT Hub によって生成される監視データと、Azure Monitor の機能を使用してこのデータについての分析とアラートを行う方法について説明します。

## <a name="monitor-overview"></a>監視の概要

Azure portal の各 IoT ハブの **[概要]** ページには、使用されているメッセージの数や IoT ハブに接続されているデバイスの数など、いくつかの使用状況メトリックを示すグラフが含まれています。

:::image type="content" source="media/monitor-iot-hub/overview-portal.png" alt-text="IoT ハブの [概要] ページの既定のメトリック グラフ。":::

メッセージ数の値は 1 分遅れることがあり、IoT Hub サービス インフラストラクチャに関連する理由により、値が更新時に値の上限と下限の間でバウンスする場合があることに注意する必要があります。 この数は、最後の 1 分間に増加した値についてのみ正しくありません。

概要ウィンドウに表示される情報は便利ですが、IoT Hub に使用できる監視データは、ごくわずかな量しか示しません。 いくつかの監視データは自動的に収集され、IoT ハブを作成するとすぐに分析に使用できるようになります。 一定の構成によって追加の種類のデータ収集を有効にすることができます。

## <a name="what-is-azure-monitor"></a>Azure Monitor とは

Azure IoT Hub では、[Azure Monitor](../azure-monitor/overview.md) を使用して監視データを作成します。Azure Monitor は Azure のフル スタックの監視サービスであり、他のクラウドやオンプレミスのリソースに加えて、Azure リソースを監視するための完全な機能セットが提供されます。

まず「[Azure Monitor を使用した Azure リソースの監視](../azure-monitor/essentials/monitor-azure-resource.md)」の記事にある次の概念の説明をお読みください。

- Azure Monitor とは
- 監視に関連するコスト
- Azure で収集される監視データ
- データ収集の構成
- 監視データの分析とアラート生成のための Azure の標準ツール

以下のセクションでは、この記事を基に、Azure IoT Hub に関して収集される特定のデータについて説明します。また、Azure のツールを使用してデータの収集を構成し、このデータを分析するための例を示します。

## <a name="monitoring-data"></a>データの監視

Azure IoT Hub では、他の Azure リソースと同じ種類の監視データが収集されます。これについては、[Azure リソースの監視データ](../azure-monitor/essentials/monitor-azure-resource.md#monitoring-data)に関するページをご覧ください。

Azure IoT Hub によって作成されるメトリックとログの詳細については、「[Monitoring Azure IoT Hub data reference (Azure IoT Hub 監視データのリファレンス)](monitor-iot-hub-reference.md)」を参照してください。

> [!IMPORTANT]
> Azure Monitor のリソース ログを使用して IoT Hub サービスによって出力されるイベントは、信頼性も順序も保証されません。 一部のイベントが失われたり、順序どおりに配信されないことがあります。 さらに、リソース ログはリアルタイムではなく、選択した出力先に記録されるまで数分かかる場合があります。

## <a name="collection-and-routing"></a>収集とルーティング

プラットフォーム メトリックとアクティビティ ログは自動的に収集および格納されますが、診断設定を使用して他の場所にルーティングすることもできます。

リソース ログは、診断設定を作成して 1 つ以上の場所にルーティングするまでは収集および格納されません。

メトリックとログは、次のような複数の場所にルーティングできます。
- Azure Monitor には、関連付けられた Log Analytics ワークスペースを使用してログが格納されます。 ここで、Log Analytics を使用してこれらを分析できます。
- アーカイブとオフライン分析のための Azure Storage 
- 外部アプリケーションで読み取れる Azure Event Hubs エンドポイント (サードパーティ製の SIEM ツールなど)。

Azure portal で、IoT ハブの左ウィンドウの **[監視]** の下にある **[診断設定]** を選択してから **[診断設定の追加]** を選択すると、IoT ハブによって出力されるログとプラットフォーム メトリックを対象範囲とする診断設定を作成できます。

次のスクリーンショットは、リソース ログ タイプの *接続操作* とすべてのプラットフォーム メトリックを Log Analytics ワークスペースにルーティングするための診断設定を示しています。

:::image type="content" source="media/monitor-iot-hub/diagnostic-setting-portal.png" alt-text="IoT ハブの [診断設定] ウィンドウ。":::

Azure portal、CLI、または PowerShell を使用して診断設定を作成するプロセスの詳細については、「[Azure でプラットフォーム ログとメトリックを収集するための診断設定を作成する](../azure-monitor/essentials/diagnostic-settings.md)」を参照してください。 診断設定を作成するときは、収集するログのカテゴリを指定します。 Azure IoT Hub のカテゴリの一覧については、[「Monitoring Azure IoT Hub data reference (Azure IoT Hub 監視データのリファレンス)」の「リソース ログ」](monitor-iot-hub-reference.md#resource-logs)を参照してください。

IoT Hub のプラットフォーム メトリックを他の場所にルーティングするときは、以下の点にご注意ください。

- 次のプラットフォーム メトリックは、診断設定を使用してエクスポートできません:*接続されているデバイス (プレビュー)* と *デバイスの合計数 (プレビュー)* 。

- 多次元メトリック (一部の[ルーティング メトリック](monitor-iot-hub-reference.md#routing-metrics)など) は、現在、ディメンション値間で集計され、フラット化された単一ディメンションのメトリックとしてエクスポートされます。 詳細については、「[他の場所へのプラットフォーム メトリックのエクスポート](../azure-monitor/essentials/metrics-supported.md#exporting-platform-metrics-to-other-locations)」を参照してください。

## <a name="analyzing-metrics"></a>メトリックの分析

**Azure Monitor** のメニューから **[メトリック]** を開き、メトリックス エクスプローラーを使用して、Azure IoT Hub のメトリックを、他の Azure サービスからのメトリックと一緒に分析することができます。 このツールの使用方法の詳細については、「[Azure メトリックス エクスプローラーの概要](../azure-monitor/essentials/metrics-getting-started.md)」を参照してください。

Azure portal で、IoT ハブの左ウィンドウの **[監視]** の下にある **[メトリック]** を選択すると、IoT ハブによって出力されるプラットフォーム メトリック (既定) を対象範囲とするメトリックス エクスプローラーが開きます。

:::image type="content" source="media/monitor-iot-hub/metrics-portal.png" alt-text="IoT ハブのメトリックス エクスプローラー ページ。":::

Azure IoT Hub に関して収集されるプラットフォーム メトリックの一覧については、[「Monitoring Azure IoT Hub data reference (Azure IoT Hub 監視データのリファレンス)」の「メトリック」](monitor-iot-hub-reference.md#metrics)を参照してください。 すべての Azure サービスに関して収集されるプラットフォーム メトリックの一覧については、「[Azure Monitor のサポートされるメトリック](../azure-monitor/essentials/metrics-supported.md)」を参照してください。

カウント単位で収集される IoT Hub のプラットフォーム メトリックについては、一部の集計が有効でない場合や使用できない場合があります。 詳細については、[「Monitoring Azure IoT Hub data reference (Azure IoT Hub 監視データのリファレンス)」の「サポートされる集計」](monitor-iot-hub-reference.md#supported-aggregations)を参照してください。

[ルーティング メトリック](monitor-iot-hub-reference.md#routing-metrics)など、いくつかの IoT Hub メトリックは多次元です。 これらのメトリックについては、[フィルター](../azure-monitor/essentials/metrics-charts.md#filters)を適用し、ディメンションに基づいてグラフに[分割](../azure-monitor/essentials/metrics-charts.md#apply-splitting)することができます。

## <a name="analyzing-logs"></a>ログの分析

Azure Monitor ログのデータはテーブルに格納され、各テーブルには独自の一意のプロパティ セットがあります。 これらのテーブル内のデータは、Log Analytics ワークスペースに関連付けられており、Log Analytics でクエリを実行できます。 Azure Monitor ログの詳細については、Azure Monitor のドキュメントの「[Azure Monitor ログの概要](../azure-monitor/logs/data-platform-logs.md)」を参照してください。 

データを Azure Monitor ログにルーティングするには、リソース ログまたはプラットフォーム メトリックを Log Analytics ワークスペースに送信するための診断設定を作成する必要があります。 詳細については、「[コレクションとルーティング](#collection-and-routing)」を参照してください。

Azure portal で、IoT ハブの左ウィンドウの **[監視]** の下にある **[ログ]** を選択すると、Azure Monitor ログで IoT ハブに関して収集されるログとメトリック (既定) を対象範囲とする Log Analytics クエリを実行できます。

:::image type="content" source="media/monitor-iot-hub/logs-portal.png" alt-text="IoT ハブの [ログ] ページ。":::

Azure Monitor ログで使用され、Log Analytics でクエリを実行できるテーブルの一覧については、[「Monitoring Azure IoT Hub data reference (Azure IoT Hub 監視データのリファレンス)」の「Azure Monitor ログ テーブル」](monitor-iot-hub-reference.md#azure-monitor-logs-tables)を参照してください。

Azure Monitor 内のすべてのリソース ログには、同じフィールドの後にサービス固有のフィールドがあります。 共通のスキーマの概要については、[Azure Monitor リソース ログのスキーマ](../azure-monitor/essentials/resource-logs-schema.md#top-level-common-schema)に関する記事をご覧ください。 Azure IoT Hub に関して収集されるリソース ログのスキーマとカテゴリについては、[「Monitoring Azure IoT Hub data reference (Azure IoT Hub 監視データのリファレンス)」の「リソース ログ」](monitor-iot-hub-reference.md#resource-logs)を参照してください。

[アクティビティ ログ](../azure-monitor/essentials/activity-log.md)は、Azure のプラットフォーム ログであり、サブスクリプション レベルのイベントの分析情報が提供されます。 個別に表示できるほか、Azure Monitor ログにルーティングして、Log Analytics を使用してより複雑なクエリを実行することもできます。  

IoT Hub のプラットフォーム メトリックを Azure Monitor ログにルーティングするときは、以下の点にご注意ください。

- 次のプラットフォーム メトリックは、診断設定を使用してエクスポートできません:*接続されているデバイス (プレビュー)* と *デバイスの合計数 (プレビュー)* 。

- 多次元メトリック (一部の[ルーティング メトリック](monitor-iot-hub-reference.md#routing-metrics)など) は、現在、ディメンション値間で集計され、フラット化された単一ディメンションのメトリックとしてエクスポートされます。 詳細については、「[他の場所へのプラットフォーム メトリックのエクスポート](../azure-monitor/essentials/metrics-supported.md#exporting-platform-metrics-to-other-locations)」を参照してください。

IoT Hub に関する一般的なクエリについては、「[サンプル Kusto クエリ](#sample-kusto-queries)」を参照してください。 Log Analytics クエリの使用の詳細については、「[Azure Monitor のログ クエリの概要](../azure-monitor/logs/log-query-overview.md)」を参照してください。

### <a name="sdk-version-in-iot-hub-logs"></a>IoT Hub ログの SDK バージョン

IoT Hub リソース ログの一部の操作では、`properties` オブジェクト内に `sdkVersion` プロパティが返されます。 これらの操作では、デバイスまたはバックエンド アプリがいずれかの Azure IoT SDK を使用している場合、このプロパティには、使用されている SDK、SDK のバージョン、および SDK が実行されているプラットフォームに関する情報が含まれています。 次の例は、Node.js デバイス SDK を使用するときに [`deviceConnect`](monitor-iot-hub-reference.md#connections) 操作に対して生成される `sdkVersion` プロパティを示しています: `"azure-iot-device/1.17.1 (node v10.16.0; Windows_NT 10.0.18363; x64)"`。 .NET (C#) SDK に対して生成される値の例は次のとおりです: `".NET/1.21.2 (.NET Framework 4.8.4200.0; Microsoft Windows 10.0.17763 WindowsProduct:0x00000004; X86)"`。

次の表には、さまざまな Azure IoT SDK に使用される SDK 名が示されています。

| sdkVersion プロパティでの SDK 名 | 言語 |
|----------|----------|
| .NET | .NET (C#) |
| microsoft.azure.devices | .NET (C#) サービス SDK |
| microsoft.azure.devices.client | .NET (C#) デバイス SDK |
| iothubclient | C または Python v1 (非推奨) デバイス SDK |
| iothubserviceclient | C または Python v1 (非推奨) サービス SDK |
| azure-iot-device-iothub-py | Python デバイス SDK |
| azure-iot-device | Node.js デバイス SDK |
| azure-iothub | Node.js サービス SDK |
| com.microsoft.azure.iothub-java-client | Java デバイス SDK |
| com.microsoft.azure.iothub.service.sdk | Java サービス SDK |
| com.microsoft.azure.sdk.iot.iot-device-client | Java デバイス SDK |
| com.microsoft.azure.sdk.iot.iot-service-client | Java サービス SDK |
| C | 埋め込み C |
| C + (OSSimplified = Azure RTOS) | Azure RTOS |

IoT Hub リソース ログに対してクエリを実行するときに、SDK バージョン プロパティを抽出できます。 たとえば、次のクエリは、接続操作によって返されるプロパティから SDK バージョン プロパティ (およびデバイス ID) を抽出します。 これら 2 つのプロパティは、操作の時刻と、デバイスが接続している IoT ハブのリソース ID と一緒に結果に書き込まれます。

```kusto
// SDK version of devices
// List of devices and their SDK versions that connect to IoT Hub
AzureDiagnostics
| where ResourceProvider == "MICROSOFT.DEVICES" and ResourceType == "IOTHUBS"
| where Category == "Connections"
| extend parsed_json = parse_json(properties_s) 
| extend SDKVersion = tostring(parsed_json.sdkVersion) , DeviceId = tostring(parsed_json.deviceId)
| distinct DeviceId, SDKVersion, TimeGenerated, _ResourceId
```

### <a name="sample-kusto-queries"></a>サンプル Kusto クエリ

> [!IMPORTANT]
> IoT ハブのメニューから **[ログ]** を選択すると、クエリのスコープが現在の IoT ハブに設定された状態で Log Analytics が開きます。 つまり、ログ クエリには、そのリソースからのデータのみが含まれます。 他の IoT ハブのデータや他の Azure サービスのデータを含むクエリを実行する場合は、 **[Azure Monitor]** メニューから **[ログ]** を選択します。 詳細については、「[Azure Monitor Log Analytics のログ クエリのスコープと時間範囲](../azure-monitor/logs/scope.md)」を参照してください。

次に、IoT ハブの監視に使用できるクエリを示します。

- 接続エラー:デバイス接続エラーを識別します。

    ```kusto
    AzureDiagnostics
    | where ResourceProvider == "MICROSOFT.DEVICES" and ResourceType == "IOTHUBS"
    | where Category == "Connections" and Level == "Error"
    ```

- 調整エラー:調整エラーが発生する要求を最も多く行ったデバイスを識別します。

    ```kusto
    AzureDiagnostics
    | where ResourceProvider == "MICROSOFT.DEVICES" and ResourceType == "IOTHUBS"
    | where ResultType == "429001"
    | extend DeviceId = tostring(parse_json(properties_s).deviceId)
    | summarize count() by DeviceId, Category, _ResourceId
    | order by count_ desc
    ```

- 配信不能エンドポイント:問題が報告された回数とその理由によって、配信不能または異常なエンドポイントを特定します。

    ```kusto
    AzureDiagnostics
    | where ResourceProvider == "MICROSOFT.DEVICES" and ResourceType == "IOTHUBS"
    | where Category == "Routes" and OperationName in ("endpointDead", "endpointUnhealthy")
    | extend parsed_json = parse_json(properties_s)
    | extend Endpoint = tostring(parsed_json.endpointName), Reason = tostring(parsed_json.details) 
    | summarize count() by Endpoint, OperationName, Reason, _ResourceId
    | order by count_ desc
    ```

- エラーの概要:すべての操作に関する種類別のエラー数。

    ```kusto
    AzureDiagnostics
    | where ResourceProvider == "MICROSOFT.DEVICES" and ResourceType == "IOTHUBS"
    | where Level == "Error"
    | summarize count() by ResultType, ResultDescription, Category, _ResourceId
    ```

- 最近接続されたデバイス:指定した期間内に IoT Hub に接続されたデバイスの一覧。

    ```kusto
    AzureDiagnostics
    | where ResourceProvider == "MICROSOFT.DEVICES" and ResourceType == "IOTHUBS"
    | where Category == "Connections" and OperationName == "deviceConnect"
    | extend DeviceId = tostring(parse_json(properties_s).deviceId)
    | summarize max(TimeGenerated) by DeviceId, _ResourceId
    ```

- デバイスの SDK バージョン:デバイスと、デバイス接続またはデバイスからクラウドへのツイン操作の SDK バージョンの一覧。

    ```kusto
    AzureDiagnostics
    | where ResourceProvider == "MICROSOFT.DEVICES" and ResourceType == "IOTHUBS"
    | where Category == "Connections" or Category == "D2CTwinOperations"
    | extend parsed_json = parse_json(properties_s)
    | extend SDKVersion = tostring(parsed_json.sdkVersion) , DeviceId = tostring(parsed_json.deviceId)
    | distinct DeviceId, SDKVersion, TimeGenerated, _ResourceId
    ```

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

## <a name="alerts"></a>警告

Azure Monitor のアラートは、監視データで重要な状態が見つかると事前に通知します。 これにより、ユーザーが気付く前に、管理者が問題を識別して対処できます。 アラートは[メトリック](../azure-monitor/alerts/alerts-metric-overview.md)、[ログ](../azure-monitor/alerts/alerts-unified-log.md)、[アクティビティ ログ](../azure-monitor/alerts/activity-log-alerts.md)に対して設定できます。 アラートの種類に応じて、さまざまな利点と欠点があります。

プラットフォーム メトリックに基づいてアラート ルールを作成する場合は、カウント単位で収集される IoT Hub のプラットフォーム メトリックについて、一部の集計が有効でない場合や使用できない場合があることにご注意ください。 詳細については、[「Monitoring Azure IoT Hub data reference (Azure IoT Hub 監視データのリファレンス)」の「サポートされる集計」](monitor-iot-hub-reference.md#supported-aggregations)を参照してください。

## <a name="monitor-per-device-disconnects-with-event-grid"></a>Event Grid を使用したデバイスごとの切断の監視

Azure Monitor には、"*接続されているデバイス*" のメトリックが用意されています。このメトリックを使用して IoT Hub に接続されているデバイスの数を監視し、接続されているデバイスの数がしきい値を下回るとアラートをトリガーできます。 一部のシナリオではこれで十分ですが、[Azure Event Grid](../event-grid/index.yml) では、重要なデバイスとインフラストラクチャのデバイス接続を追跡するために使用できる、待機時間の短い、デバイスごとの監視ソリューションが提供されます。

Event Grid を使用すると、IoT Hub [**DeviceConnected** と **DeviceDisconnected** イベント](iot-hub-event-grid.md#event-types)をサブスクライブして、アラートをトリガーし、デバイスの接続状態を監視することができます。 Event Grid は Azure Monitor と比べてはるかにイベント待機時間が短く、接続されているデバイスの合計数ではなく、デバイスごとに監視できます。 これらの要因により、Event Grid は重要なデバイスとインフラストラクチャの接続を監視するための推奨される方法となっています。 運用環境では、デバイスの接続の監視には Event Grid を使用することを強くお勧めします。

Event Grid と Azure Monitor を使用したデバイス接続の監視の詳細については、「[Azure IoT Hub との切断に関する監視、診断、およびトラブルシューティング](iot-hub-troubleshoot-connectivity.md)」を参照してください。

## <a name="next-steps"></a>次のステップ

- Azure Storage によって作成されるメトリック、ログ、その他の重要な値のリファレンスについては、「[Monitoring Azure IoT Hub data reference (Azure IoT Hub 監視データのリファレンス)](monitor-iot-hub-reference.md)」を参照してください。

- Azure リソースの監視の詳細については、「[Azure Monitor を使用した Azure リソースの監視](../azure-monitor/essentials/monitor-azure-resource.md)」を参照してください。
