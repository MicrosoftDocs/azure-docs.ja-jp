---
title: Azure リソース ログの概要 | Microsoft Docs
description: Azure リソース ログでサポートされているサービスとイベント スキーマについて説明します。
author: rboucher
services: azure-monitor
ms.service: azure-monitor
ms.topic: reference
ms.date: 09/20/2019
ms.author: robb
ms.subservice: logs
ms.openlocfilehash: bfcd2ded96c2679ba9177a760a8b11dc7d2c9a77
ms.sourcegitcommit: 55f7fc8fe5f6d874d5e886cb014e2070f49f3b94
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/25/2019
ms.locfileid: "71263034"
---
# <a name="azure-resource-logs-overview"></a>Azure リソース ログの概要
Azure リソース ログは、Azure リソースによって出力された [プラットフォーム ログ](platform-logs-overview.md) であり、内部操作を説明するものです。 すべてのリソース ログは共通の上位スキーマを共有し、各サービスで独自のイベントの一意のプロパティを出力する柔軟性を持ちます。

> [!NOTE]
> リソース ログは、以前は診断ログと呼ばれていました。

## <a name="collecting-resource-logs"></a>リソース ログの収集
リソース ログは、サポートされている Azure リソースによって自動的に生成されますが、[診断設定](diagnostic-settings.md)を使用して構成しない限り収集されません。 Azure リソースごとに診断設定を作成し、ログを次の送信先に転送します。

| Destination | シナリオ |
|:---|:---|:---|
| [Log Analytics ワークスペース](resource-logs-collect-storage.md) | 他の監視データと共にログを分析し、ログ クエリやログ アラートなどの Azure Monitor 機能を活用します。 |
| [Azure Storage](archive-diagnostic-logs.md) | 監査やバックアップのためにログをアーカイブします。 |
| [イベント ハブ](resource-logs-stream-event-hubs.md) | サード パーティのログ システムやテレメトリ システムにログをストリーミングします。  |

## <a name="compute-resources"></a>コンピューティング リソース
リソース ログは、Azure コンピューティング リソースのゲスト OS レベルのログとは異なります。 コンピューティング リソースには、イベント ログ、syslog、パフォーマンス カウンターなどのデータを含むログおよびメトリックをゲスト OS から収集するエージェントが必要です。 [診断の拡張機能](agents-overview.md#azure-diagnostic-extension)を使用して Azure 仮想マシンおよび [Log Analytics エージェント](agents-overview.md#log-analytics-agent)からログ データをルーティングし、Azure、他のクラウド、およびオンプレミスの仮想マシンから Log Analytics ワークスペースにログとメトリックを収集します。 詳細については、「[Azure Monitor で使用する監視データのソース](data-sources.md)」を参照してください。

## <a name="resource-logs-schema"></a>リソース ログのスキーマ
各 Azure サービスには、リソース ログがいずれかの送信先へ書き込まれるときの独自のスキーマがありますが、これらはすべて、次の表の上位スキーマを共有します。 各サービスのスキーマへのリンクについては、下記の「[サービス固有のスキーマ](#service-specific-schemas)」を参照してください。 

| 名前 | 必須/省略可能 | 説明 |
|---|---|---|
| time | 必須 | イベントのタイムスタンプ (UTC)。 |
| resourceId | 必須 | イベントを出力したリソースのリソース ID。 テナント サービスの場合、形式は /tenants/tenant-id/providers/provider-name です。 |
| tenantId | テナント ログで必須 | このイベントが関連付けられている Active Directory テナントのテナント ID。 このプロパティは、テナントレベルのログでのみ使用されます。リソースレベルのログには表示されません。 |
| operationName | 必須 | このイベントで表される操作の名前。 イベントが RBAC 操作を表す場合、これは RBAC 操作の名前になります (例: Microsoft.Storage/storageAccounts/blobServices/blobs/Read)。 実際の文書化されている Resource Manager 操作でない場合でも、通常は、Resource Manager 操作の形式でモデル化されます (`Microsoft.<providerName>/<resourceType>/<subtype>/<Write/Read/Delete/Action>`)。 |
| operationVersion | 省略可能 | operationName が API を使用して実行された場合は、操作に関連付けられている api-version (例: `http://myservice.windowsazure.net/object?api-version=2016-06-01`)。 この操作に対応する API がない場合、バージョンは、操作に関連付けられているプロパティが今後、変更された場合、その操作のバージョンを表します。 |
| category | 必須 | イベントのログ カテゴリ。 カテゴリは細分化されており、これを使用して、特定のリソースのログを有効または無効にすることができます。 イベントのプロパティ BLOB 内に表示されるプロパティは、特定のログ カテゴリとリソースの種類内のものと同じです。 一般的なログ カテゴリは、"Audit"、"Operational"、"Execution"、"Request" です。 |
| resultType | 省略可能 | イベントの状態。 一般的な値は、Started、In Progress、Succeeded、Failed、Active、Resolved です。 |
| resultSignature | 省略可能 | イベントの副状態。 この操作が REST API 呼び出しに対応している場合、これは、対応する REST 呼び出しの HTTP 状態コードです。 |
| resultDescription | 省略可能 | この操作を説明する静的テキスト (例: "Get storage file")。 |
| durationMs | 省略可能 | 操作時間 (ミリ秒)。 |
| callerIpAddress | 省略可能 | 操作が、一般的に利用できる IP アドレスを持つエンティティからの API 呼び出しに対応している場合は、呼び出し元 IP アドレス。 |
| correlationId | 省略可能 | 関連するイベントのセットをグループ化するために使用される GUID。 通常、2 つのイベントの operationName は同じであるものの、状態が異なる (たとえば、 "Started" と "Succeeded") 場合、同じ関連付け ID が共有されます。 これは、イベント間の他のリレーションシップを表す場合もあります。 |
| identity | 省略可能 | 操作を実行したユーザーまたはアプリケーションの ID を説明する JSON BLOB。 通常、これにはアクティブ ディレクトリからの認証および要求 / JWT トークンが含まれます。 |
| Level | 省略可能 | イベントの重大度レベル。 Informational、Warning、Error、Critical のいずれかである必要があります。 |
| location | 省略可能 | イベントを出力するリソースの領域 (例: "East US"、"France South")。 |
| properties | 省略可能 | この特定のイベント カテゴリに関連する拡張プロパティ。 すべてのカスタム/一意のプロパティは、このスキーマの "Part B" 内に配置する必要があります。 |

## <a name="service-specific-schemas"></a>サービス固有のスキーマ
リソース診断ログのスキーマは、`resourceId` プロパティで定義されているリソースの種類と、`category` プロパティによって異なります。 次の一覧は、リソース ログをサポートするすべての Azure サービスと、サービスおよびカテゴリ固有のスキーマ (使用可能な場合) へのリンクを示しています。

| Service | スキーマとドキュメント |
| --- | --- |
| Azure Active Directory | [概要](../../active-directory/reports-monitoring/concept-activity-logs-azure-monitor.md)、[監査ログ スキーマ](../../active-directory/reports-monitoring/reference-azure-monitor-audit-log-schema.md)、および[サインイン スキーマ](../../active-directory/reports-monitoring/reference-azure-monitor-sign-ins-log-schema.md) |
| Analysis Services | https://azure.microsoft.com/blog/azure-analysis-services-integration-with-azure-diagnostic-logs/ |
| API Management | [API Management の診断ログ](../../api-management/api-management-howto-use-azure-monitor.md#diagnostic-logs) |
| Application Gateway |[Application Gateway の診断ログ](../../application-gateway/application-gateway-diagnostics.md) |
| Azure Automation |[Azure Automation のログ分析](../../automation/automation-manage-send-joblogs-log-analytics.md) |
| Azure Batch |[Azure Batch 診断ログ](../../batch/batch-diagnostics.md) |
| Azure Database for MySQL | [Azure Database for MySQL の診断ログ](../../mysql/concepts-server-logs.md#diagnostic-logs) |
| Azure Database for PostgreSQL | [Azure Database for PostgreSQL の診断ログ](../../postgresql/concepts-server-logs.md#diagnostic-logs) |
| Cognitive Services | [Azure Cognitive Services の診断ログ](../../cognitive-services/diagnostic-logging.md) |
| Content Delivery Network | [CDN の Azure 診断ログ](../../cdn/cdn-azure-diagnostic-logs.md) |
| Cosmos DB | [Azure Cosmos DB のログ](../../cosmos-db/logging.md) |
| Data Factory | [Azure Monitor を使用して、データ ファクトリを監視する](../../data-factory/monitor-using-azure-monitor.md) |
| Data Lake Analytics |[Azure Data Lake Analytics の診断ログへのアクセス](../../data-lake-analytics/data-lake-analytics-diagnostic-logs.md) |
| Data Lake Store |[Azure Data Lake Store の診断ログへのアクセス](../../data-lake-store/data-lake-store-diagnostic-logs.md) |
| Event Hubs |[Azure Event Hubs の診断ログ](../../event-hubs/event-hubs-diagnostic-logs.md) |
| ExpressRoute | スキーマは使用できません。 |
| Azure Firewall | スキーマは使用できません。 |
| IoT Hub | [IoT Hub 操作](../../iot-hub/iot-hub-monitor-resource-health.md#use-azure-monitor) |
| Key Vault |[Azure Key Vault のログ記録](../../key-vault/key-vault-logging.md) |
| Load Balancer |[Azure Load Balancer のログ分析](../../load-balancer/load-balancer-monitor-log.md) |
| Logic Apps |[Logic Apps B2B カスタム追跡スキーマ](../../logic-apps/logic-apps-track-integration-account-custom-tracking-schema.md) |
| ネットワーク セキュリティ グループ |[ネットワーク セキュリティ グループ (NSG) のためのログ分析](../../virtual-network/virtual-network-nsg-manage-log.md) |
| DDOS Protection | [Azure DDoS Protection Standard を管理する](../../virtual-network/manage-ddos-protection.md) |
| Power BI 専用 | [Azure の Power BI Embedded の診断ログ](https://docs.microsoft.com/power-bi/developer/azure-pbie-diag-logs) |
| 復旧サービス | [Azure Backup のデータ モデル](../../backup/backup-azure-reports-data-model.md)|
| Search |[検索トラフィックの分析の有効化と使用](../../search/search-traffic-analytics.md) |
| Service Bus |[Azure Service Bus の診断ログ](../../service-bus-messaging/service-bus-diagnostic-logs.md) |
| SQL Database | [Azure SQL Database 診断ログ](../../sql-database/sql-database-metrics-diag-logging.md) |
| Stream Analytics |[ジョブの診断ログ](../../stream-analytics/stream-analytics-job-diagnostic-logs.md) |
| Traffic Manager | [Traffic Manager のログのスキーマ](../../traffic-manager/traffic-manager-diagnostic-logs.md) |
| 仮想ネットワーク | スキーマは使用できません。 |
| 仮想ネットワーク ゲートウェイ | スキーマは使用できません。 |

## <a name="next-steps"></a>次の手順

* Azure の監視に使用できる[その他の Azure プラットフォーム ログについて確認します](platform-logs-overview.md)。
