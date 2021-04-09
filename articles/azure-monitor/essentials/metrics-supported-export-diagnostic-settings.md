---
title: NULL および 0 の値を使用したメトリックのエクスポート動作
description: メトリックをエクスポートするときの NULL とゼロの値、およびエクスポートできないメトリックのリストを指すポインターに関する説明。
services: azure-monitor
ms.topic: reference
ms.date: 07/22/2020
ms.openlocfilehash: 47b98fe46ac1f2a3e2f3f1a8078ad9ca6f867554
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102048847"
---
# <a name="azure-monitor-platform-metrics-exportable-via-diagnostic-settings"></a>診断設定を使用してエクスポート可能な Azure Monitor プラットフォーム メトリック

Azure Monitor には、構成なしの[プラットフォーム メトリック](../essentials/data-platform-metrics.md)が既定で用意されています。 これにより、複数の方法を使用してプラットフォーム メトリックを操作できます。たとえば、ポータルでメトリックをグラフ化したり、REST API でアクセスしたり、PowerShell や CLI を使ってクエリを実行したりできます。 Azure Monitor の統合メトリック パイプラインで現在利用可能なプラットフォーム メトリックの完全な一覧については、[サポートされるメトリック](./metrics-supported.md)に関するページを参照してください。 これらのメトリックを照会してアクセスするには、[2018-01-01 バージョンの API](/rest/api/monitor/metricdefinitions) を使ってください。 他のメトリックについては、ポータルや従来の API で使用できる場合があります。

## <a name="metrics-not-exportable-via-diagnostic-settings"></a>診断設定を使用してエクスポートできないメトリック

この場所にあったコンテンツは、[Azure Monitor メトリックのサポート リスト](./metrics-supported.md#exporting-platform-metrics-to-other-locations)に移動されました。

診断設定を使用してメトリックをエクスポートする場合は、制限があります。 メトリックはすべて、REST API を使用してエクスポートできます。 

## <a name="exported-zero-vs-null-values"></a>エクスポートされるゼロ値と NULL 値の比較 

0 または NULL 値を処理する場合、メトリックの動作は異なります。  メトリックの中には、データが取得されない場合に 0 を報告するものがあります (たとえば、http エラーに関するメトリック)。 他には、データが取得されない場合に NULL を格納するメトリックもあります。それによって、リソースがオフラインであることを示すことができるからです。 これらのメトリックをグラフ化する場合は、NULL 値を[破線](metrics-troubleshoot.md#chart-shows-dashed-line)として表示することで、違いを確認できます。 

診断設定を使用してプラットフォーム メトリックをエクスポートできる場合は、それらはメトリックの動作と一致します。 つまり、リソースからデータが送信されない場合は NULL がエクスポートされます。  '0' がエクスポートされるのは、基になるリソースによって実際に出力された場合に限られます。 

リソース グループまたは特定のリソースを削除すると、影響を受けるリソースからのメトリック データは、診断設定のエクスポート先に送信されなくなります。 つまり、Event Hubs、Azure Storage アカウント、Log Analytics ワークスペースに表示されなくなります。

### <a name="metrics-that-used-to-export-zero-for-null"></a>NULL の代わりに 0 をエクスポートしていたメトリック

2020 年 6 月 1 日より前、以下のメトリックは、データが存在しない場合に '0' を出力 **していました**。 これらのゼロは、Log Analytics や Azure Storage などの下流のシステムにエクスポートされる可能性がありました。  これにより、実際の '0' (リソースによって出力されたもの) と解釈された '0' (NULL) との間で混同が発生していました。そのため、前のセクションで説明したように、基になるメトリックのものと一致するように動作が変更されました。 

すべてのパブリックおよびプライベート クラウドで変更が行われました。

この変更は、次のいずれのエクスペリエンスの動作にも影響しませんでした。 
   - 診断設定を使用してエクスポートされるプラットフォーム リソース ログ
   - メトリックス エクスプローラーのメトリック グラフ
   - プラットフォーム メトリックに関するアラート
 
動作が変更されたメトリックの一覧を次に示します。 

| ResourceType                    | メトリック               |  MetricDisplayName  | 
|---------------------------------|----------------------|---------------------|
| Microsoft.ApiManagement/service | UnauthorizedRequests |  未承認ゲートウェイ要求 (非推奨)  | 
| Microsoft.ApiManagement/service | TotalRequests |  ゲートウェイ要求の合計 (非推奨)  | 
| Microsoft.ApiManagement/service | SuccessfulRequests |  成功したゲートウェイ要求 (非推奨)  | 
| Microsoft.ApiManagement/service | Requests |  Requests  | 
| Microsoft.ApiManagement/service | OtherRequests |  その他のゲートウェイ要求 (非推奨)  | 
| Microsoft.ApiManagement/service | FailedRequests |  失敗したゲートウェイ要求 (非推奨)  | 
| Microsoft.ApiManagement/service | EventHubTotalFailedEvents |  失敗した EventHub イベント数  | 
| Microsoft.ApiManagement/service | EventHubTotalEvents |  EventHub イベントの合計数  | 
| Microsoft.ApiManagement/service | EventHubTotalBytesSent |  EventHub イベントのサイズ  | 
| Microsoft.ApiManagement/service | EventHubTimedoutEvents |  タイムアウトした EventHub イベント数  | 
| Microsoft.ApiManagement/service | EventHubThrottledEvents |  調整された EventHub イベント数  | 
| Microsoft.ApiManagement/service | EventHubSuccessfulEvents |  成功した EventHub イベント数  | 
| Microsoft.ApiManagement/service | EventHubRejectedEvents |  拒否された EventHub イベント数  | 
| Microsoft.ApiManagement/service | EventHubDroppedEvents |  破棄された EventHub イベント数  | 
| Microsoft.ApiManagement/service | Duration |  ゲートウェイ要求の全体の期間  | 
| Microsoft.ApiManagement/service | BackendDuration |  バックエンド要求の期間  | 
| Microsoft.DBforMariaDB/servers | storage_used |  使用済みストレージ  | 
| Microsoft.DBforMariaDB/servers | storage_percent |  ストレージの割合  | 
| Microsoft.DBforMariaDB/servers | storage_limit |  ストレージの制限  | 
| Microsoft.DBforMariaDB/servers | serverlog_storage_usage |  サーバー ログ ストレージの使用量  | 
| Microsoft.DBforMariaDB/servers | serverlog_storage_percent |  サーバー ログ ストレージの割合  | 
| Microsoft.DBforMariaDB/servers | serverlog_storage_limit |  サーバー ログ ストレージの上限  | 
| Microsoft.DBforMariaDB/servers | seconds_behind_master |  レプリケーションのラグ (秒単位)  | 
| Microsoft.DBforMariaDB/servers | network_bytes_ingress |  Network In  | 
| Microsoft.DBforMariaDB/servers | network_bytes_egress |  Network Out  | 
| Microsoft.DBforMariaDB/servers | memory_percent |  メモリの割合  | 
| Microsoft.DBforMariaDB/servers | io_consumption_percent |  IO の割合  | 
| Microsoft.DBforMariaDB/servers | cpu_percent |  CPU 使用率  | 
| Microsoft.DBforMariaDB/servers | connections_failed |  失敗した接続  | 
| Microsoft.DBforMariaDB/servers | backup_storage_used |  使用されたバックアップ ストレージ  | 
| Microsoft.DBforMariaDB/servers | active_connections |  アクティブな接続  | 
| Microsoft.DBforMySQL/servers | storage_used |  使用済みストレージ  | 
| Microsoft.DBforMySQL/servers | storage_percent |  ストレージの割合  | 
| Microsoft.DBforMySQL/servers | storage_limit |  ストレージの制限  | 
| Microsoft.DBforMySQL/servers | serverlog_storage_usage |  サーバー ログ ストレージの使用量  | 
| Microsoft.DBforMySQL/servers | serverlog_storage_percent |  サーバー ログ ストレージの割合  | 
| Microsoft.DBforMySQL/servers | serverlog_storage_limit |  サーバー ログ ストレージの上限  | 
| Microsoft.DBforMySQL/servers | seconds_behind_master |  レプリケーションのラグ (秒単位)  | 
| Microsoft.DBforMySQL/servers | network_bytes_ingress |  Network In  | 
| Microsoft.DBforMySQL/servers | network_bytes_egress |  Network Out  | 
| Microsoft.DBforMySQL/servers | memory_percent |  メモリの割合  | 
| Microsoft.DBforMySQL/servers | io_consumption_percent |  IO の割合  | 
| Microsoft.DBforMySQL/servers | cpu_percent |  CPU 使用率  | 
| Microsoft.DBforMySQL/servers | connections_failed |  失敗した接続  | 
| Microsoft.DBforMySQL/servers | backup_storage_used |  使用されたバックアップ ストレージ  | 
| Microsoft.DBforMySQL/servers | active_connections |  アクティブな接続  | 
| Microsoft.Devices/Account | digitaltwins.telemetry.nodes |  Digital Twins ノード テレメトリのプレースホルダー  | 
| Microsoft.Devices/IotHubs | twinQueries.success |  成功したツイン クエリ  | 
| Microsoft.Devices/IotHubs | twinQueries.resultSize |  ツイン クエリの結果のサイズ  | 
| Microsoft.Devices/IotHubs | twinQueries.failure |  失敗したツイン クエリ  | 
| Microsoft.Devices/IotHubs | jobs.queryJobs.success |  成功したジョブ クエリ  | 
| Microsoft.Devices/IotHubs | jobs.queryJobs.failure |  失敗したジョブ クエリ  | 
| Microsoft.Devices/IotHubs | jobs.listJobs.success |  成功したジョブ一覧の呼び出し  | 
| Microsoft.Devices/IotHubs | jobs.listJobs.failure |  失敗したジョブ一覧の呼び出し  | 
| Microsoft.Devices/IotHubs | jobs.failed |  失敗したジョブ  | 
| Microsoft.Devices/IotHubs | jobs.createTwinUpdateJob.success |  成功したツイン更新ジョブの作成  | 
| Microsoft.Devices/IotHubs | jobs.createTwinUpdateJob.failure |  失敗したツイン更新ジョブの作成  | 
| Microsoft.Devices/IotHubs | jobs.createDirectMethodJob.success |  成功したメソッド呼び出しジョブの作成  | 
| Microsoft.Devices/IotHubs | jobs.createDirectMethodJob.failure |  失敗したメソッド呼び出しジョブの作成  | 
| Microsoft.Devices/IotHubs | jobs.completed |  完了したジョブ  | 
| Microsoft.Devices/IotHubs | jobs.cancelJob.success |  成功したジョブの取り消し  | 
| Microsoft.Devices/IotHubs | jobs.cancelJob.failure |  失敗したジョブの取り消し  | 
| Microsoft.Devices/IotHubs | EventGridLatency |  Event Grid の待機時間 (プレビュー)  | 
| Microsoft.Devices/IotHubs | EventGridDeliveries |  Event Grid 配信 (プレビュー)  | 
| Microsoft.Devices/IotHubs | devices.totalDevices |  デバイスの総数 (非推奨)  | 
| Microsoft.Devices/IotHubs | devices.connectedDevices.allProtocol |  接続されているデバイス (非推奨)   | 
| Microsoft.Devices/IotHubs | deviceDataUsageV2 |  デバイス データの合計使用量 (プレビュー)  | 
| Microsoft.Devices/IotHubs | deviceDataUsage |  デバイス データの合計使用量  | 
| Microsoft.Devices/IotHubs | dailyMessageQuotaUsed |  使用されているメッセージの合計数  | 
| Microsoft.Devices/IotHubs | d2c.twin.update.success |  成功したデバイスからのツイン更新  | 
| Microsoft.Devices/IotHubs | d2c.twin.update.size |  デバイスからのツイン更新のサイズ  | 
| Microsoft.Devices/IotHubs | d2c.twin.update.failure |  失敗したデバイスからのツイン更新  | 
| Microsoft.Devices/IotHubs | d2c.twin.read.success |  成功したデバイスからのツイン読み取り  | 
| Microsoft.Devices/IotHubs | d2c.twin.read.size |  デバイスからのツイン読み取りの応答サイズ  | 
| Microsoft.Devices/IotHubs | d2c.twin.read.failure |  失敗したデバイスからのツイン読み取り  | 
| Microsoft.Devices/IotHubs | d2c.telemetry.ingress.success |  送信済みテレメトリ メッセージ  | 
| Microsoft.Devices/IotHubs | d2c.telemetry.ingress.sendThrottle |  調整エラーの数  | 
| Microsoft.Devices/IotHubs | d2c.telemetry.ingress.allProtocol |  テレメトリ メッセージ送信試行  | 
| Microsoft.Devices/IotHubs | d2c.telemetry.egress.success |  ルーティング: 配信されたテレメトリ メッセージ  | 
| Microsoft.Devices/IotHubs | d2c.telemetry.egress.orphaned |  ルーティング: 孤立したテレメトリ メッセージ   | 
| Microsoft.Devices/IotHubs | d2c.telemetry.egress.invalid |  ルーティング: 互換性のないテレメトリ メッセージ  | 
| Microsoft.Devices/IotHubs | d2c.telemetry.egress.fallback |  ルーティング: フォールバックのために配信されたメッセージ  | 
| Microsoft.Devices/IotHubs | d2c.telemetry.egress.dropped |  ルーティング: 破棄されたテレメトリ メッセージ   | 
| Microsoft.Devices/IotHubs | d2c.endpoints.latency.storage |  ルーティング: ストレージのメッセージの待機時間  | 
| Microsoft.Devices/IotHubs | d2c.endpoints.latency.serviceBusTopics |  ルーティング: Service Bus トピックのメッセージの待機時間  | 
| Microsoft.Devices/IotHubs | d2c.endpoints.latency.serviceBusQueues |  ルーティング: Service Bus キューのメッセージの待機時間  | 
| Microsoft.Devices/IotHubs | d2c.endpoints.latency.eventHubs |  ルーティング: Event Hub のメッセージの待機時間  | 
| Microsoft.Devices/IotHubs | d2c.endpoints.latency.builtIn.events |  ルーティング: メッセージ/イベントのメッセージの待機時間  | 
| Microsoft.Devices/IotHubs | d2c.endpoints.egress.storage.bytes |  ルーティング: ストレージに配信されたデータ  | 
| Microsoft.Devices/IotHubs | d2c.endpoints.egress.storage.blobs |  ルーティング: ストレージに配信された BLOB  | 
| Microsoft.Devices/IotHubs | d2c.endpoints.egress.storage |  ルーティング: ストレージに配信されたメッセージ  | 
| Microsoft.Devices/IotHubs | d2c.endpoints.egress.serviceBusTopics |  ルーティング: Service Bus トピックに配信されたメッセージ  | 
| Microsoft.Devices/IotHubs | d2c.endpoints.egress.serviceBusQueues |  ルーティング: Service Bus キューに配信されたメッセージ  | 
| Microsoft.Devices/IotHubs | d2c.endpoints.egress.eventHubs |  ルーティング: Event Hub に配信されたメッセージ  | 
| Microsoft.Devices/IotHubs | d2c.endpoints.egress.builtIn.events |  ルーティング: メッセージ/イベントに配信されたメッセージ  | 
| Microsoft.Devices/IotHubs | configuration |  構成メトリック  | 
| Microsoft.Devices/IotHubs | C2DMessagesExpired |  期限切れ C2D メッセージ (プレビュー)  | 
| Microsoft.Devices/IotHubs | c2d.twin.update.success |  成功したバックエンドからのツイン更新  | 
| Microsoft.Devices/IotHubs | c2d.twin.update.size |  バックエンドからのツイン更新のサイズ  | 
| Microsoft.Devices/IotHubs | c2d.twin.update.failure |  失敗したバック エンドからのツイン更新  | 
| Microsoft.Devices/IotHubs | c2d.twin.read.success |  成功したバック エンドからのツイン読み取り  | 
| Microsoft.Devices/IotHubs | c2d.twin.read.size |  バック エンドからのツイン読み取りの応答サイズ  | 
| Microsoft.Devices/IotHubs | c2d.twin.read.failure |  失敗したバックエンドからのツイン読み取り  | 
| Microsoft.Devices/IotHubs | c2d.methods.success |  成功したダイレクト メソッドの呼び出し  | 
| Microsoft.Devices/IotHubs | c2d.methods.responseSize |  ダイレクト メソッドの呼び出しの応答サイズ  | 
| Microsoft.Devices/IotHubs | c2d.methods.requestSize |  ダイレクト メソッドの呼び出しの要求サイズ  | 
| Microsoft.Devices/IotHubs | c2d.methods.failure |  失敗したダイレクト メソッドの呼び出し  | 
| Microsoft.Devices/IotHubs | c2d.commands.egress.reject.success |  C2D メッセージ拒否数  | 
| Microsoft.Devices/IotHubs | c2d.commands.egress.complete.success |  C2D メッセージ配信完了  | 
| Microsoft.Devices/IotHubs | c2d.commands.egress.abandon.success |  破棄した C2D メッセージ  | 
| Microsoft.Devices/provisioningServices | RegistrationAttempts |  Registration attempts (登録試行回数)  | 
| Microsoft.Devices/provisioningServices | DeviceAssignments |  Devices assigned (割り当て済みデバイス数)  | 
| Microsoft.Devices/provisioningServices | AttestationAttempts |  Attestation attempts (構成証明試行回数)  | 
| Microsoft.DocumentDB/databaseAccounts | TotalRequestUnits |  合計要求単位数  | 
| Microsoft.DocumentDB/databaseAccounts | TotalRequests |  要求の合計数  | 
| Microsoft.DocumentDB/databaseAccounts | MongoRequests |  Mongo Requests (Mongo 要求数)  | 
| Microsoft.DocumentDB/databaseAccounts | MongoRequestCharge |  Mongo 要求の料金  | 
| Microsoft.EventGrid/domains | PublishSuccessLatencyInMs |  成功した発行の待機時間  | 
| Microsoft.EventGrid/domains | PublishSuccessCount |  発行されたイベント  | 
| Microsoft.EventGrid/domains | PublishFailCount |  発行失敗イベント  | 
| Microsoft.EventGrid/domains | MatchedEventCount |  一致するイベント  | 
| Microsoft.EventGrid/domains | DroppedEventCount |  削除されたイベント  | 
| Microsoft.EventGrid/domains | DeliverySuccessCount |  配信されたイベント  | 
| Microsoft.EventGrid/domains | DeadLetteredCount |  配信不能イベント  | 
| Microsoft.EventGrid/eventSubscriptions | MatchedEventCount |  一致するイベント  | 
| Microsoft.EventGrid/eventSubscriptions | DroppedEventCount |  削除されたイベント  | 
| Microsoft.EventGrid/eventSubscriptions | DeliverySuccessCount |  配信されたイベント  | 
| Microsoft.EventGrid/eventSubscriptions | DeadLetteredCount |  配信不能イベント  | 
| Microsoft.EventGrid/extensionTopics | UnmatchedEventCount |  不一致のイベント  | 
| Microsoft.EventGrid/extensionTopics | PublishSuccessLatencyInMs |  成功した発行の待機時間  | 
| Microsoft.EventGrid/extensionTopics | PublishSuccessCount |  発行されたイベント  | 
| Microsoft.EventGrid/extensionTopics | PublishFailCount |  発行失敗イベント  | 
| Microsoft.EventGrid/topics | UnmatchedEventCount |  不一致のイベント  | 
| Microsoft.EventGrid/topics | PublishSuccessLatencyInMs |  成功した発行の待機時間  | 
| Microsoft.EventGrid/topics | PublishSuccessCount |  発行されたイベント  | 
| Microsoft.EventGrid/topics | PublishFailCount |  発行失敗イベント  | 
| Microsoft.EventHub/clusters | OutgoingMessages |  送信メッセージ  | 
| Microsoft.EventHub/clusters | OutgoingBytes |  発信バイト数。  | 
| Microsoft.EventHub/clusters | IncomingRequests |  受信要求  | 
| Microsoft.EventHub/clusters | IncomingMessages |  受信メッセージ  | 
| Microsoft.EventHub/clusters | IncomingBytes |  着信バイト数。  | 
| Microsoft.EventHub/namespaces | SVRBSY |  サーバー ビジー エラー数 (非推奨)  | 
| Microsoft.EventHub/namespaces | SUCCREQ |  成功した要求数 (非推奨)  | 
| Microsoft.EventHub/namespaces | OUTMSGS |  送信メッセージ (廃止) (非推奨)  | 
| Microsoft.EventHub/namespaces | OutgoingMessages |  送信メッセージ  | 
| Microsoft.EventHub/namespaces | OutgoingBytes |  発信バイト数。  | 
| Microsoft.EventHub/namespaces | MISCERR |  その他のエラー数 (非推奨)  | 
| Microsoft.EventHub/namespaces | INTERR |  内部サーバー エラー数 (非推奨)  | 
| Microsoft.EventHub/namespaces | INREQS |  受信要求数 (非推奨)  | 
| Microsoft.EventHub/namespaces | INMSGS |  受信メッセージ (廃止) (非推奨)  | 
| Microsoft.EventHub/namespaces | IncomingRequests |  受信要求  | 
| Microsoft.EventHub/namespaces | IncomingMessages |  受信メッセージ  | 
| Microsoft.EventHub/namespaces | IncomingBytes |  着信バイト数。  | 
| Microsoft.EventHub/namespaces | FAILREQ |  失敗した要求数 (非推奨)  | 
| Microsoft.EventHub/namespaces | EHOUTMSGS |  送信メッセージ (非推奨)  | 
| Microsoft.EventHub/namespaces | EHOUTMBS |  送信バイト数 (廃止) (非推奨)  | 
| Microsoft.EventHub/namespaces | EHOUTBYTES |  送信バイト数 (非推奨)  | 
| Microsoft.EventHub/namespaces | EHINMSGS |  受信メッセージ (非推奨)  | 
| Microsoft.EventHub/namespaces | EHINMBS |  受信バイト数 (廃止) (非推奨)  | 
| Microsoft.EventHub/namespaces | EHINBYTES |  受信バイト数 (非推奨)  | 
| Microsoft.EventHub/namespaces | EHAMSGS |  アーカイブ メッセージ数 (非推奨)  | 
| Microsoft.EventHub/namespaces | EHAMBS |  アーカイブ メッセージ スループット (非推奨)  | 
| Microsoft.EventHub/namespaces | EHABL |  アーカイブ バックログ メッセージ数 (非推奨)  | 
| Microsoft.HDInsight/clusters | NumActiveWorkers |  アクティブなワーカーの数  | 
| Microsoft.HDInsight/clusters | GatewayRequests |  ゲートウェイ要求数  | 
| Microsoft.HDInsight/clusters | CategorizedGatewayRequests |  カテゴリ別のゲートウェイ要求数  | 
| Microsoft.Insights/AutoscaleSettings | ScaleActionsInitiated |  スケール アクション開始  | 
| Microsoft.Insights/Components | traces/count |  トレース  | 
| Microsoft.Insights/Components | performanceCounters/requestsPerSecond |  HTTP 要求率  | 
| Microsoft.Insights/Components | performanceCounters/requestsInQueue |  アプリケーション キュー内の HTTP 要求  | 
| Microsoft.Insights/Components | performanceCounters/exceptionsPerSecond |  例外レート  | 
| Microsoft.Insights/Components | pageViews/count |  ページ ビュー  | 
| Microsoft.Insights/Components | exceptions/count |  例外  | 
| Microsoft.Kusto/Clusters | StreamingIngestResults |  ストリーミング インジェストの結果  | 
| Microsoft.Kusto/Clusters | StreamingIngestDuration |  ストリーミング インジェストの期間  | 
| Microsoft.Kusto/Clusters | StreamingIngestDataRate |  ストリーミング インジェストのデータ速度  | 
| Microsoft.Kusto/Clusters | SteamingIngestRequestRate |  ストリーミング インジェストの要求率  | 
| Microsoft.Kusto/Clusters | QueryDuration |  クエリ実行時間  | 
| Microsoft.Kusto/Clusters | KeepAlive |  キープ アライブ  | 
| Microsoft.Kusto/Clusters | IngestionVolumeInMB |  インジェストの量 (MB 単位)  | 
| Microsoft.Kusto/Clusters | IngestionUtilization |  インジェストの使用率  | 
| Microsoft.Kusto/Clusters | IngestionResult |  インジェストの結果  | 
| Microsoft.Kusto/Clusters | IngestionLatencyInSeconds |  インジェストの待機時間 (秒単位)  | 
| Microsoft.Kusto/Clusters | ExportUtilization |  エクスポート使用率  | 
| Microsoft.Kusto/Clusters | EventsProcessedForEventHubs |  (Event/IoT Hubs の) 処理されたイベント  | 
| Microsoft.Kusto/Clusters | CPU |  CPU  | 
| Microsoft.Kusto/Clusters | ContinuousExportResult |  連続エクスポートの結果  | 
| Microsoft.Kusto/Clusters | ContinuousExportPendingCount |  保留中の連続エクスポートの数  | 
| Microsoft.Kusto/Clusters | ContinuousExportNumOfRecordsExported |  連続エクスポート - エクスポートされたレコードの数  | 
| Microsoft.Kusto/Clusters | ContinuousExportMaxLatenessMinutes |  連続エクスポートの最大遅延分数  | 
| Microsoft.Kusto/Clusters | CacheUtilization |  キャッシュ使用率  | 
| Microsoft.Logic/integrationServiceEnvironments | TriggerThrottledEvents |  トリガー スロットル イベント  | 
| Microsoft.Logic/integrationServiceEnvironments | TriggersSucceeded |  成功したトリガー   | 
| Microsoft.Logic/integrationServiceEnvironments | TriggersStarted |  開始したトリガー   | 
| Microsoft.Logic/integrationServiceEnvironments | TriggersSkipped |  スキップされたトリガー  | 
| Microsoft.Logic/integrationServiceEnvironments | TriggersFired |  起動されたトリガー   | 
| Microsoft.Logic/integrationServiceEnvironments | TriggersFailed |  失敗したトリガー   | 
| Microsoft.Logic/integrationServiceEnvironments | TriggersCompleted |  完了したトリガー   | 
| Microsoft.Logic/integrationServiceEnvironments | RunThrottledEvents |  実行スロットル イベント  | 
| Microsoft.Logic/integrationServiceEnvironments | RunStartThrottledEvents |  実行開始スロットル イベント数  | 
| Microsoft.Logic/integrationServiceEnvironments | RunsSucceeded |  成功した実行  | 
| Microsoft.Logic/integrationServiceEnvironments | RunsStarted |  開始した実行  | 
| Microsoft.Logic/integrationServiceEnvironments | RunsFailed |  失敗した実行  | 
| Microsoft.Logic/integrationServiceEnvironments | RunsCompleted |  完了した実行  | 
| Microsoft.Logic/integrationServiceEnvironments | RunsCancelled |  取り消された実行  | 
| Microsoft.Logic/integrationServiceEnvironments | RunFailurePercentage |  実行エラーの割合  | 
| Microsoft.Logic/integrationServiceEnvironments | ActionThrottledEvents |  アクション スロットル イベント  | 
| Microsoft.Logic/integrationServiceEnvironments | ActionsSucceeded |  成功したアクション   | 
| Microsoft.Logic/integrationServiceEnvironments | ActionsStarted |  開始したアクション   | 
| Microsoft.Logic/integrationServiceEnvironments | ActionsSkipped |  スキップされたアクション   | 
| Microsoft.Logic/integrationServiceEnvironments | ActionsFailed |  失敗したアクション   | 
| Microsoft.Logic/integrationServiceEnvironments | ActionsCompleted |  完了したアクション   | 
| Microsoft.Logic/workflows | TriggerThrottledEvents |  トリガー スロットル イベント  | 
| Microsoft.Logic/workflows | TriggersSucceeded |  成功したトリガー   | 
| Microsoft.Logic/workflows | TriggersStarted |  開始したトリガー   | 
| Microsoft.Logic/workflows | TriggersSkipped |  スキップされたトリガー  | 
| Microsoft.Logic/workflows | TriggersFired |  起動されたトリガー   | 
| Microsoft.Logic/workflows | TriggersFailed |  失敗したトリガー   | 
| Microsoft.Logic/workflows | TriggersCompleted |  完了したトリガー   | 
| Microsoft.Logic/workflows | TotalBillableExecutions |  課金対象の実行の合計数  | 
| Microsoft.Logic/workflows | RunThrottledEvents |  実行スロットル イベント  | 
| Microsoft.Logic/workflows | RunStartThrottledEvents |  実行開始スロットル イベント数  | 
| Microsoft.Logic/workflows | RunsSucceeded |  成功した実行  | 
| Microsoft.Logic/workflows | RunsStarted |  開始した実行  | 
| Microsoft.Logic/workflows | RunsFailed |  失敗した実行  | 
| Microsoft.Logic/workflows | RunsCompleted |  完了した実行  | 
| Microsoft.Logic/workflows | RunsCancelled |  取り消された実行  | 
| Microsoft.Logic/workflows | RunFailurePercentage |  実行エラーの割合  | 
| Microsoft.Logic/workflows | BillingUsageStorageConsumption |  ストレージ使用実行の利用状況に応じた課金  | 
| Microsoft.Logic/workflows | BillingUsageStorageConsumption |  ストレージ使用実行の利用状況に応じた課金  | 
| Microsoft.Logic/workflows | BillingUsageStandardConnector |  Standard コネクタ実行の利用状況に応じた課金  | 
| Microsoft.Logic/workflows | BillingUsageStandardConnector |  Standard コネクタ実行の利用状況に応じた課金  | 
| Microsoft.Logic/workflows | BillingUsageNativeOperation |  ネイティブ操作実行の利用状況に応じた課金  | 
| Microsoft.Logic/workflows | BillingUsageNativeOperation |  ネイティブ操作実行の利用状況に応じた課金  | 
| Microsoft.Logic/workflows | BillableTriggerExecutions |  課金対象のトリガーの実行  | 
| Microsoft.Logic/workflows | BillableActionExecutions |  課金対象のアクションの実行  | 
| Microsoft.Logic/workflows | ActionThrottledEvents |  アクション スロットル イベント  | 
| Microsoft.Logic/workflows | ActionsSucceeded |  成功したアクション   | 
| Microsoft.Logic/workflows | ActionsStarted |  開始したアクション   | 
| Microsoft.Logic/workflows | ActionsSkipped |  スキップされたアクション   | 
| Microsoft.Logic/workflows | ActionsFailed |  失敗したアクション   | 
| Microsoft.Logic/workflows | ActionsCompleted |  完了したアクション   | 
| Microsoft.Network/frontdoors | WebApplicationFirewallRequestCount |  Web アプリケーション ファイアウォール要求の数  | 
| Microsoft.Network/frontdoors | TotalLatency |  合計待機時間  | 
| Microsoft.Network/frontdoors | ResponseSize |  応答サイズ  | 
| Microsoft.Network/frontdoors | RequestSize |  要求サイズ  | 
| Microsoft.Network/frontdoors | RequestCount |  要求数  | 
| Microsoft.Network/frontdoors | BillableResponseSize |  課金対象の応答サイズ  | 
| Microsoft.Network/frontdoors | BackendRequestLatency |  バックエンド要求の待機時間  | 
| Microsoft.Network/frontdoors | BackendRequestCount |  バックエンド要求数  | 
| Microsoft.Network/frontdoors | BackendHealthPercentage |  バックエンドの正常性の割合  | 
| Microsoft.Network/trafficManagerProfiles | QpsByEndpoint |  Queries by Endpoint Returned (エンドポイント別の返されたクエリ数)  | 
| Microsoft.NotificationHubs/Namespaces/NotificationHubs | scheduled.pending |  予定されていた通知の保留  | 
| Microsoft.NotificationHubs/Namespaces/NotificationHubs | registration.update |  登録更新操作  | 
| Microsoft.NotificationHubs/Namespaces/NotificationHubs | registration.get |  登録読み取り操作  | 
| Microsoft.NotificationHubs/Namespaces/NotificationHubs | registration.delete |  登録削除操作  | 
| Microsoft.NotificationHubs/Namespaces/NotificationHubs | registration.create |  登録作成操作  | 
| Microsoft.NotificationHubs/Namespaces/NotificationHubs | registration.all |  登録操作  | 
| Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.wns.wrongtoken |  WNS 承認エラー (間違ったトークン)  | 
| Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.wns.tokenproviderunreachable |  WNS 承認エラー (到達不可)  | 
| Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.wns.throttled |  WNS 調整済み通知  | 
| Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.wns.success |  WNS 正常通知  | 
| Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.wns.pnserror |  WNS エラー  | 
| Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.wns.invalidtoken |  WNS 承認エラー (無効なトークン)  | 
| Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.wns.invalidnotificationsize |  WNS 無効通知サイズ エラー  | 
| Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.wns.invalidnotificationformat |  WNS 無効通知形式  | 
| Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.wns.invalidcredentials |  WNS 承認エラー (無効な資格情報)  | 
| Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.wns.expiredchannel |  WNS 期限切れチャネル エラー  | 
| Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.wns.dropped |  WNS ドロップ通知  | 
| Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.wns.channelthrottled |  WNS 調整済みチャネル  | 
| Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.wns.channeldisconnected |  WNS チャネル切断  | 
| Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.wns.badchannel |  WNS 不良チャネル エラー  | 
| Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.wns.authenticationerror |  WNS 認証エラー  | 
| Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.mpns.throttled |  MPNS 調整済み通知  | 
| Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.mpns.success |  MPNS 正常通知  | 
| Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.mpns.pnserror |  MPNS エラー  | 
| Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.mpns.invalidnotificationformat |  MPNS 無効通知形式  | 
| Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.mpns.invalidcredentials |  MPNS 無効な資格情報  | 
| Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.mpns.dropped |  MPNS ドロップ通知  | 
| Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.mpns.channeldisconnected |  MPNS チャネル切断  | 
| Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.mpns.badchannel |  MPNS 不良チャネル エラー  | 
| Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.mpns.authenticationerror |  MPNS 認証エラー  | 
| Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.gcm.wrongchannel |  GCM 誤チャネル エラー  | 
| Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.gcm.throttled |  GCM 調整済み通知  | 
| Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.gcm.success |  GCM 正常通知  | 
| Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.gcm.pnserror |  GCM エラー  | 
| Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.gcm.invalidnotificationsize |  GCM 無効通知サイズ エラー  | 
| Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.gcm.invalidnotificationformat |  GCM 無効通知形式  | 
| Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.gcm.invalidcredentials |  GCM 承認エラー (無効な資格情報)  | 
| Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.gcm.expiredchannel |  GCM 期限切れチャネル エラー  | 
| Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.gcm.badchannel |  GCM 不良チャネル エラー  | 
| Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.gcm.authenticationerror |  GCM 認証エラー  | 
| Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.apns.success |  APNS 正常通知  | 
| Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.apns.pnserror |  APNS エラー  | 
| Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.apns.invalidnotificationsize |  APNS 無効通知サイズ エラー  | 
| Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.apns.invalidcredentials |  APNS 認証エラー  | 
| Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.apns.expiredchannel |  APNS 期限切れチャネル エラー  | 
| Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.apns.badchannel |  APNS 不良チャネル エラー  | 
| Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.allpns.success |  正常な通知  | 
| Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.allpns.pnserror |  外部通知システム エラー  | 
| Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.allpns.invalidpayload |  ペイロード エラー  | 
| Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.allpns.channelerror |  チャネル エラー  | 
| Microsoft.NotificationHubs/Namespaces/NotificationHubs | notificationhub.pushes |  All Outgoing Notifications (すべての送信通知)  | 
| Microsoft.NotificationHubs/Namespaces/NotificationHubs | installation.upsert |  インストール操作を作成または更新します  | 
| Microsoft.NotificationHubs/Namespaces/NotificationHubs | installation.patch |  インストール操作をパッチします  | 
| Microsoft.NotificationHubs/Namespaces/NotificationHubs | installation.get |  インストール操作の取得  | 
| Microsoft.NotificationHubs/Namespaces/NotificationHubs | installation.delete |  インストール操作の削除  | 
| Microsoft.NotificationHubs/Namespaces/NotificationHubs | installation.all |  インストール管理操作  | 
| Microsoft.NotificationHubs/Namespaces/NotificationHubs | incoming.scheduled.cancel |  予定されていたプッシュ通知が取り消されました  | 
| Microsoft.NotificationHubs/Namespaces/NotificationHubs | incoming.scheduled |  予定されていたプッシュ通知の送信  | 
| Microsoft.NotificationHubs/Namespaces/NotificationHubs | incoming.all.requests |  すべての受信要求  | 
| Microsoft.NotificationHubs/Namespaces/NotificationHubs | incoming.all.failedrequests |  失敗したすべての受信要求  | 
| Microsoft.NotificationHubs/Namespaces/NotificationHubs | incoming |  受信メッセージ  | 
| Microsoft.OperationalInsights/workspaces | Heartbeat |  Heartbeat  | 
| Microsoft.Relay/namespaces | BytesTransferred |  BytesTransferred  | 
| Microsoft.ServiceBus/namespaces | OutgoingMessages |  送信メッセージ  | 
| Microsoft.ServiceBus/namespaces | IncomingRequests |  受信要求  | 
| Microsoft.ServiceBus/namespaces | IncomingMessages |  受信メッセージ  | 
| Microsoft.SignalRService/SignalR | UserErrors |  User Errors (ユーザー エラー)  | 
| Microsoft.SignalRService/SignalR | SystemErrors |  System Errors (システム エラー)  | 
| Microsoft.SignalRService/SignalR | OutboundTraffic |  送信トラフィック  | 
| Microsoft.SignalRService/SignalR | MessageCount |  メッセージ数  | 
| Microsoft.SignalRService/SignalR | InboundTraffic |  受信トラフィック  | 
| Microsoft.SignalRService/SignalR | ConnectionCount |  Connection Count (接続数)  | 
| Microsoft.Sql/managedInstances | avg_cpu_percent |  平均 CPU の割合  | 
| Microsoft.Sql/servers | dtu_used |  使用された DTU  | 
| Microsoft.Sql/servers | dtu_consumption_percent |  DTU の割合  | 
| Microsoft.Sql/servers/databases | xtp_storage_percent |  インメモリ OLTP ストレージの割合  | 
| Microsoft.Sql/servers/databases | workers_percent |  ワーカーの割合  | 
| Microsoft.Sql/servers/databases | sessions_percent |  セッションの割合  | 
| Microsoft.Sql/servers/databases | physical_data_read_percent |  データ IO の割合  | 
| Microsoft.Sql/servers/databases | log_write_percent |  ログ IO の割合  | 
| Microsoft.Sql/servers/databases | dwu_used |  使用済み DWU  | 
| Microsoft.Sql/servers/databases | dwu_consumption_percent |  DWU の割合  | 
| Microsoft.Sql/servers/databases | dtu_used |  使用された DTU  | 
| Microsoft.Sql/servers/databases | dtu_consumption_percent |  DTU の割合  | 
| Microsoft.Sql/servers/databases | deadlock |  デッドロック  | 
| Microsoft.Sql/servers/databases | cpu_used |  使用された CPU  | 
| Microsoft.Sql/servers/databases | cpu_percent |  CPU の割合  | 
| Microsoft.Sql/servers/databases | connection_successful |  成功した接続  | 
| Microsoft.Sql/servers/databases | connection_failed |  失敗した接続  | 
| Microsoft.Sql/servers/databases | cache_hit_percent |  キャッシュ ヒットの割合  | 
| Microsoft.Sql/servers/databases | blocked_by_firewall |  ファイアウォールによってブロックされる  | 
| Microsoft.Sql/servers/elasticPools | xtp_storage_percent |  インメモリ OLTP ストレージの割合  | 
| Microsoft.Sql/servers/elasticPools | workers_percent |  ワーカーの割合  | 
| Microsoft.Sql/servers/elasticPools | sessions_percent |  セッションの割合  | 
| Microsoft.Sql/servers/elasticPools | physical_data_read_percent |  データ IO の割合  | 
| Microsoft.Sql/servers/elasticPools | log_write_percent |  ログ IO の割合  | 
| Microsoft.Sql/servers/elasticPools | eDTU_used |  使用済み eDTU  | 
| Microsoft.Sql/servers/elasticPools | dtu_consumption_percent |  DTU の割合  | 
| Microsoft.Sql/servers/elasticPools | cpu_percent |  CPU の割合  | 
| Microsoft.Web/hostingEnvironments/multiRolePools | TotalFrontEnds |  フロント エンドの合計  | 
| Microsoft.Web/hostingEnvironments/multiRolePools | SmallAppServicePlanInstances |  S App Service プランの worker の数  | 
| Microsoft.Web/hostingEnvironments/multiRolePools | Requests |  Requests  | 
| Microsoft.Web/hostingEnvironments/multiRolePools | MemoryPercentage |  メモリの割合  | 
| Microsoft.Web/hostingEnvironments/multiRolePools | MediumAppServicePlanInstances |  M App Service プランの worker の数  | 
| Microsoft.Web/hostingEnvironments/multiRolePools | LargeAppServicePlanInstances |  L App Service プランの worker の数  | 
| Microsoft.Web/hostingEnvironments/multiRolePools | HttpQueueLength |  HTTP キューの長さ  | 
| Microsoft.Web/hostingEnvironments/multiRolePools | Http5xx |  HTTP サーバー エラー  | 
| Microsoft.Web/hostingEnvironments/multiRolePools | Http4xx |  HTTP 4xx  | 
| Microsoft.Web/hostingEnvironments/multiRolePools | Http406 |  HTTP 406  | 
| Microsoft.Web/hostingEnvironments/multiRolePools | Http404 |  HTTP 404  | 
| Microsoft.Web/hostingEnvironments/multiRolePools | Http403 |  HTTP 403  | 
| Microsoft.Web/hostingEnvironments/multiRolePools | Http401 |  HTTP 401  | 
| Microsoft.Web/hostingEnvironments/multiRolePools | Http3xx |  HTTP 3xx  | 
| Microsoft.Web/hostingEnvironments/multiRolePools | Http2xx |  HTTP 2xx  | 
| Microsoft.Web/hostingEnvironments/multiRolePools | Http101 |  Http 101  | 
| Microsoft.Web/hostingEnvironments/multiRolePools | DiskQueueLength |  ディスク キューの長さ  | 
| Microsoft.Web/hostingEnvironments/multiRolePools | CpuPercentage |  CPU の割合  | 
| Microsoft.Web/hostingEnvironments/multiRolePools | BytesSent |  送信データ  | 
| Microsoft.Web/hostingEnvironments/multiRolePools | BytesReceived |  受信データ  | 
| Microsoft.Web/hostingEnvironments/multiRolePools | AverageResponseTime |  平均応答時間  | 
| Microsoft.Web/hostingEnvironments/multiRolePools | ActiveRequests |  アクティブな要求数  | 
| Microsoft.Web/hostingEnvironments/workerPools | WorkersUsed |  使用済みの worker の数  | 
| Microsoft.Web/hostingEnvironments/workerPools | WorkersTotal |  worker の合計数  | 
| Microsoft.Web/hostingEnvironments/workerPools | WorkersAvailable |  利用可能な worker の数  | 
| Microsoft.Web/hostingEnvironments/workerPools | MemoryPercentage |  メモリの割合  | 
| Microsoft.Web/hostingEnvironments/workerPools | CpuPercentage |  CPU の割合  | 
| Microsoft.Web/serverfarms | TcpTimeWait |  TCP 待機中  | 
| Microsoft.Web/serverfarms | TcpSynSent |  TCP SYN 送信済み  | 
| Microsoft.Web/serverfarms | TcpSynReceived |  TCP SYN 受信済み  | 
| Microsoft.Web/serverfarms | TcpLastAck |  TCP 最後の ACK  | 
| Microsoft.Web/serverfarms | TcpFinWait2 |  TCP FIN の待機 2  | 
| Microsoft.Web/serverfarms | TcpFinWait1 |  TCP FIN の待機 1  | 
| Microsoft.Web/serverfarms | TcpEstablished |  TCP 確立済み  | 
| Microsoft.Web/serverfarms | TcpClosing |  TCP 終了  | 
| Microsoft.Web/serverfarms | TcpCloseWait |  TCP 終了を待機中  | 
| Microsoft.Web/serverfarms | MemoryPercentage |  メモリの割合  | 
| Microsoft.Web/serverfarms | HttpQueueLength |  HTTP キューの長さ  | 
| Microsoft.Web/serverfarms | DiskQueueLength |  ディスク キューの長さ  | 
| Microsoft.Web/serverfarms | CpuPercentage |  CPU の割合  | 
| Microsoft.Web/serverfarms | BytesSent |  送信データ  | 
| Microsoft.Web/serverfarms | BytesReceived |  受信データ  | 
| Microsoft.Web/sites | TotalAppDomainsUnloaded |  アンロード済みのアプリ ドメイン合計数  | 
| Microsoft.Web/sites | TotalAppDomains |  アプリ ドメイン合計数  | 
| Microsoft.Web/sites | Threads |  Thread Count  | 
| Microsoft.Web/sites | RequestsInApplicationQueue |  アプリケーション キュー内の要求数  | 
| Microsoft.Web/sites | Requests |  Requests  | 
| Microsoft.Web/sites | PrivateBytes |  Private Bytes  | 
| Microsoft.Web/sites | MemoryWorkingSet |  メモリ ワーキング セット  | 
| Microsoft.Web/sites | IoWriteOperationsPerSecond |  IO 書き込み操作/秒  | 
| Microsoft.Web/sites | IoWriteBytesPerSecond |  IO 書き込みバイト/秒  | 
| Microsoft.Web/sites | IoReadOperationsPerSecond |  IO 読み取り操作/秒  | 
| Microsoft.Web/sites | IoReadBytesPerSecond |  IO 読み取りバイト/秒  | 
| Microsoft.Web/sites | IoOtherOperationsPerSecond |  IO その他の操作/秒  | 
| Microsoft.Web/sites | IoOtherBytesPerSecond |  IO その他のバイト/秒  | 
| Microsoft.Web/sites | HttpResponseTime |  応答時間  | 
| Microsoft.Web/sites | Http5xx |  HTTP サーバー エラー  | 
| Microsoft.Web/sites | Http4xx |  HTTP 4xx  | 
| Microsoft.Web/sites | Http406 |  HTTP 406  | 
| Microsoft.Web/sites | Http404 |  HTTP 404  | 
| Microsoft.Web/sites | Http403 |  HTTP 403  | 
| Microsoft.Web/sites | Http401 |  HTTP 401  | 
| Microsoft.Web/sites | Http3xx |  HTTP 3xx  | 
| Microsoft.Web/sites | Http2xx |  HTTP 2xx  | 
| Microsoft.Web/sites | Http101 |  Http 101  | 
| Microsoft.Web/sites | HealthCheckStatus |  正常性チェックの状態  | 
| Microsoft.Web/sites | ハンドル |  ハンドルの数  | 
| Microsoft.Web/sites | Gen2Collections |  Gen 2 ガベージ コレクション  | 
| Microsoft.Web/sites | Gen1Collections |  Gen 1 ガベージ コレクション  | 
| Microsoft.Web/sites | Gen0Collections |  Gen 0 ガベージ コレクション  | 
| Microsoft.Web/sites | FunctionExecutionUnits |  関数の実行単位  | 
| Microsoft.Web/sites | FunctionExecutionCount |  関数の実行回数  | 
| Microsoft.Web/sites | CurrentAssemblies |  現在のアセンブリ  | 
| Microsoft.Web/sites | CpuTime |  CPU 時間  | 
| Microsoft.Web/sites | BytesSent |  送信データ  | 
| Microsoft.Web/sites | BytesReceived |  受信データ  | 
| Microsoft.Web/sites | AverageResponseTime |  平均応答時間  | 
| Microsoft.Web/sites | AverageMemoryWorkingSet |  平均メモリ ワーキング セット  | 
| Microsoft.Web/sites | AppConnections |  接続  | 
| Microsoft.Web/sites/slots | TotalAppDomainsUnloaded |  アンロード済みのアプリ ドメイン合計数  | 
| Microsoft.Web/sites/slots | TotalAppDomains |  アプリ ドメイン合計数  | 
| Microsoft.Web/sites/slots | Threads |  Thread Count  | 
| Microsoft.Web/sites/slots | RequestsInApplicationQueue |  アプリケーション キュー内の要求数  | 
| Microsoft.Web/sites/slots | Requests |  Requests  | 
| Microsoft.Web/sites/slots | PrivateBytes |  Private Bytes  | 
| Microsoft.Web/sites/slots | MemoryWorkingSet |  メモリ ワーキング セット  | 
| Microsoft.Web/sites/slots | IoWriteOperationsPerSecond |  IO 書き込み操作/秒  | 
| Microsoft.Web/sites/slots | IoWriteBytesPerSecond |  IO 書き込みバイト/秒  | 
| Microsoft.Web/sites/slots | IoReadOperationsPerSecond |  IO 読み取り操作/秒  | 
| Microsoft.Web/sites/slots | IoReadBytesPerSecond |  IO 読み取りバイト/秒  | 
| Microsoft.Web/sites/slots | IoOtherOperationsPerSecond |  IO その他の操作/秒  | 
| Microsoft.Web/sites/slots | IoOtherBytesPerSecond |  IO その他のバイト/秒  | 
| Microsoft.Web/sites/slots | HttpResponseTime |  応答時間  | 
| Microsoft.Web/sites/slots | Http5xx |  HTTP サーバー エラー  | 
| Microsoft.Web/sites/slots | Http4xx |  HTTP 4xx  | 
| Microsoft.Web/sites/slots | Http406 |  HTTP 406  | 
| Microsoft.Web/sites/slots | Http404 |  HTTP 404  | 
| Microsoft.Web/sites/slots | Http403 |  HTTP 403  | 
| Microsoft.Web/sites/slots | Http401 |  HTTP 401  | 
| Microsoft.Web/sites/slots | Http3xx |  HTTP 3xx  | 
| Microsoft.Web/sites/slots | Http2xx |  HTTP 2xx  | 
| Microsoft.Web/sites/slots | Http101 |  Http 101  | 
| Microsoft.Web/sites/slots | HealthCheckStatus |  正常性チェックの状態  | 
| Microsoft.Web/sites/slots | ハンドル |  ハンドルの数  | 
| Microsoft.Web/sites/slots | Gen2Collections |  Gen 2 ガベージ コレクション  | 
| Microsoft.Web/sites/slots | Gen1Collections |  Gen 1 ガベージ コレクション  | 
| Microsoft.Web/sites/slots | Gen0Collections |  Gen 0 ガベージ コレクション  | 
| Microsoft.Web/sites/slots | FunctionExecutionUnits |  関数の実行単位  | 
| Microsoft.Web/sites/slots | FunctionExecutionCount |  関数の実行回数  | 
| Microsoft.Web/sites/slots | CurrentAssemblies |  現在のアセンブリ  | 
| Microsoft.Web/sites/slots | CpuTime |  CPU 時間  | 
| Microsoft.Web/sites/slots | BytesSent |  送信データ  | 
| Microsoft.Web/sites/slots | BytesReceived |  受信データ  | 
| Microsoft.Web/sites/slots | AverageResponseTime |  平均応答時間  | 
| Microsoft.Web/sites/slots | AverageMemoryWorkingSet |  平均メモリ ワーキング セット  | 
| Microsoft.Web/sites/slots | AppConnections |  接続  | 
| Microsoft.Sql/servers/databases | cpu_percent | CPU の割合 | 
| Microsoft.Sql/servers/databases | physical_data_read_percent | データ IO の割合 | 
| Microsoft.Sql/servers/databases | log_write_percent | ログ IO の割合 | 
| Microsoft.Sql/servers/databases | dtu_consumption_percent | DTU の割合 | 
| Microsoft.Sql/servers/databases | connection_successful | 成功した接続 | 
| Microsoft.Sql/servers/databases | connection_failed | 失敗した接続 | 
| Microsoft.Sql/servers/databases | blocked_by_firewall | ファイアウォールによってブロックされる | 
| Microsoft.Sql/servers/databases | deadlock | デッドロック | 
| Microsoft.Sql/servers/databases | xtp_storage_percent | インメモリ OLTP ストレージの割合 | 
| Microsoft.Sql/servers/databases | workers_percent | ワーカーの割合 | 
| Microsoft.Sql/servers/databases | sessions_percent | セッションの割合 | 
| Microsoft.Sql/servers/databases | dtu_used | 使用された DTU | 
| Microsoft.Sql/servers/databases | cpu_used | 使用された CPU | 
| Microsoft.Sql/servers/databases | dwu_consumption_percent | DWU の割合 | 
| Microsoft.Sql/servers/databases | dwu_used | 使用済み DWU | 
| Microsoft.Sql/servers/databases | cache_hit_percent | キャッシュ ヒットの割合 | 
| Microsoft.Sql/servers/databases | wlg_allocation_relative_to_system_percent | Workload group allocation by system percent (システム割合別のワークロード グループの割り当て) | 
| Microsoft.Sql/servers/databases | wlg_allocation_relative_to_wlg_effective_cap_percent | Workload group allocation by max resource percent (最大リソース割合別のワークロード グループの割り当て) | 
| Microsoft.Sql/servers/databases | wlg_active_queries | Workload group active queries (ワークロード グループのアクティブなクエリ) | 
| Microsoft.Sql/servers/databases | wlg_queued_queries | Workload group queued queries (ワークロード グループのキューに登録されたクエリ) | 
| Microsoft.Sql/servers/databases | active_queries | アクティブなクエリ | 
| Microsoft.Sql/servers/databases | queued_queries | キューに置かれたクエリ | 
| Microsoft.Sql/servers/databases | wlg_active_queries_timeouts | Workload group query timeouts (ワークロード グループのクエリのタイムアウト) | 
| Microsoft.Sql/servers/databases | wlg_queued_queries_timeouts | ワークロード グループのキューに登録されたクエリのタイムアウト | 
| Microsoft.Sql/servers/databases | wlg_effective_min_resource_percent | Effective min resource percent (有効な最小リソース割合) | 
| Microsoft.Sql/servers/databases | wlg_effective_cap_resource_percent | Effective cap resource percent (有効な上限リソース割合) | 
| Microsoft.Sql/servers/elasticPools | cpu_percent | CPU の割合 | 
| Microsoft.Sql/servers/elasticPools | physical_data_read_percent | データ IO の割合 | 
| Microsoft.Sql/servers/elasticPools | log_write_percent | ログ IO の割合 | 
| Microsoft.Sql/servers/elasticPools | dtu_consumption_percent | DTU の割合 | 
| Microsoft.Sql/servers/elasticPools | workers_percent | ワーカーの割合 | 
| Microsoft.Sql/servers/elasticPools | sessions_percent | セッションの割合 | 
| Microsoft.Sql/servers/elasticPools | eDTU_used | 使用済み eDTU | 
| Microsoft.Sql/servers/elasticPools | xtp_storage_percent | インメモリ OLTP ストレージの割合 | 
| Microsoft.Sql/servers | dtu_consumption_percent | DTU の割合 | 
| Microsoft.Sql/servers | dtu_used | 使用された DTU | 
| Microsoft.Sql/managedInstances | avg_cpu_percent | 平均 CPU の割合 |