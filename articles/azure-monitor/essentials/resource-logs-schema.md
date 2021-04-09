---
title: Azure リソース ログでサポートされているサービスとスキーマ
description: Azure リソース ログでサポートされているサービスとイベント スキーマについて説明します。
ms.topic: reference
ms.date: 09/01/2020
ms.openlocfilehash: 661cba2d78dbb176bb04e6831cf8e045a285535e
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102033097"
---
# <a name="common-and-service-specific-schema-for-azure-resource-logs"></a>Azure リソース ログの共通およびサービス固有のスキーマ

> [!NOTE]
> リソース ログは、以前は診断ログと呼ばれていました。 この名前は、Azure Monitor によって収集されたログの種類が Azure リソースだけを含むようになったため、2019 年 10 月に変更されました。 また、収集できるリソース ログのカテゴリの一覧は、この記事に表示されていました。 これらは、[リソース ログのカテゴリ](resource-logs-categories.md)に移動しました。 

[Azure Monitor リソース ログ](../essentials/platform-logs-overview.md)は、Azure サービスによって出力されるログであり、そのサービスやリソースの操作が記述されます。 Azure Monitor を通じて使用できるすべてのリソース ログには、共通の上位スキーマが共有されます。各サービスが、独自のイベントに固有のプロパティを出力するための柔軟性も備わっています。

(`resourceId` プロパティで使用可能な) リソースの種類と `category` を組み合わせて、スキーマを一意に識別します。 この記事では、リソース ログの上位スキーマについて説明し、各サービスのスキーマへのリンクを示します。


## <a name="top-level-common-schema"></a>上位の共通スキーマ

| 名前 | 必須/省略可能 | 説明 |
|---|---|---|
| time | 必須 | イベントのタイムスタンプ (UTC)。 |
| resourceId | 必須 | イベントを出力したリソースのリソース ID。 テナント サービスの場合、形式は /tenants/tenant-id/providers/provider-name です。 |
| tenantId | テナント ログで必須 | このイベントが関連付けられている Active Directory テナントのテナント ID。 このプロパティは、テナントレベルのログでのみ使用されます。リソースレベルのログには表示されません。 |
| operationName | 必須 | このイベントで表される操作の名前。 イベントが Azure RBAC 操作を表す場合、これは Azure RBAC 操作名 (例: Microsoft.Storage/storageAccounts/blobServices/blobs/Read) です。 実際の文書化されている Resource Manager 操作でない場合でも、通常は、Resource Manager 操作の形式でモデル化されます (`Microsoft.<providerName>/<resourceType>/<subtype>/<Write/Read/Delete/Action>`)。 |
| operationVersion | 省略可能 | operationName が API を使用して実行された場合は、操作に関連付けられている api-version (例: `http://myservice.windowsazure.net/object?api-version=2016-06-01`)。 この操作に対応する API がない場合、バージョンは、操作に関連付けられているプロパティが今後、変更された場合、その操作のバージョンを表します。 |
| category | 必須 | イベントのログ カテゴリ。 カテゴリは細分化されており、これを使用して、特定のリソースのログを有効または無効にすることができます。 イベントのプロパティ BLOB 内に表示されるプロパティは、特定のログ カテゴリとリソースの種類内のものと同じです。 一般的なログ カテゴリは、"Audit"、"Operational"、"Execution"、"Request" です。 |
| resultType | 省略可能 | イベントの状態。 一般的な値は、Started、In Progress、Succeeded、Failed、Active、Resolved です。 |
| resultSignature | 省略可能 | イベントの副状態。 この操作が REST API 呼び出しに対応している場合、このフィールドは、対応する REST 呼び出しの HTTP 状態コードです。 |
| resultDescription | 省略可能 | この操作を説明する静的テキスト (例: "Get storage file")。 |
| durationMs | 省略可能 | 操作時間 (ミリ秒)。 |
| callerIpAddress | 省略可能 | 操作が、一般的に利用できる IP アドレスを持つエンティティからの API 呼び出しに対応している場合は、呼び出し元 IP アドレス。 |
| correlationId | 省略可能 | 関連するイベントのセットをグループ化するために使用される GUID。 通常、2 つのイベントの operationName は同じであるものの、異なる 2 つの状態 (例: "Started" と "Succeeded") の場合は、同じ関連付け ID を共有します。 これは、イベント間の他のリレーションシップを表す場合もあります。 |
| identity | 省略可能 | 操作を実行したユーザーまたはアプリケーションの ID を説明する JSON BLOB。 通常、このフィールドにはアクティブ ディレクトリからの認証および要求 / JWT トークンが含まれます。 |
| Level | 省略可能 | イベントの重大度レベル。 Informational、Warning、Error、Critical のいずれかである必要があります。 |
| location | 省略可能 | イベントを出力するリソースの領域 (例: "East US" または "France South") |
| properties | 省略可能 | この特定のイベント カテゴリに関連する拡張プロパティ。 すべてのカスタム/一意のプロパティは、このスキーマの "Part B" 内に配置する必要があります。 |

## <a name="service-specific-schemas"></a>サービス固有のスキーマ

リソース ログのスキーマは、リソースとログ カテゴリによって異なります。 リソース ログを使用できるようにするサービスと、そのサービスおよびカテゴリ固有のスキーマへのリンク (使用可能な場合) を、次の一覧に示します。 この一覧は、新しいサービスが追加されるたびに変更されるため、以下で必要な情報が表示されない場合は、検索エンジンを使用して追加のドキュメントを検索してください。 この記事で自由に GitHub 問題を開いてください。Microsoft が更新を行います。

| サービス | スキーマとドキュメント |
| --- | --- |
| Azure Active Directory | [概要](../../active-directory/reports-monitoring/concept-activity-logs-azure-monitor.md)、[監査ログ スキーマ](../../active-directory/reports-monitoring/reference-azure-monitor-audit-log-schema.md)、および[サインイン スキーマ](../../active-directory/reports-monitoring/reference-azure-monitor-sign-ins-log-schema.md) |
| Analysis Services | [Azure Analysis Services - 診断ログを設定する](../../analysis-services/analysis-services-logging.md) |
| API Management | [API Management のリソース ログ](../../api-management/api-management-howto-use-azure-monitor.md#resource-logs) |
| App Service | [App Service ログ](../../app-service/troubleshoot-diagnostic-logs.md)
| Application Gateway |[Application Gateway のログ記録](../../application-gateway/application-gateway-diagnostics.md) |
| Azure Automation |[Azure Automation のログ分析](../../automation/automation-manage-send-joblogs-log-analytics.md) |
| Azure Batch |[Azure Batch のログ記録](../../batch/batch-diagnostics.md) |
| Cognitive Services | [Azure Cognitive Services のログ記録](../../cognitive-services/diagnostic-logging.md) |
| Container Registry | [Azure Container Registry のログ記録](../../container-registry/container-registry-diagnostics-audit-logs.md) |
| Content Delivery Network | [CDN の Azure ログ](../../cdn/cdn-azure-diagnostic-logs.md) |
| Cosmos DB | [Azure Cosmos DB のログ](../../cosmos-db/monitor-cosmos-db.md) |
| Data Factory | [Azure Monitor を使用して、データ ファクトリを監視する](../../data-factory/monitor-using-azure-monitor.md) |
| Data Lake Analytics |[Azure Data Lake Analytics のログへのアクセス](../../data-lake-analytics/data-lake-analytics-diagnostic-logs.md) |
| Data Lake Store |[Azure Data Lake Store のログへのアクセス](../../data-lake-store/data-lake-store-diagnostic-logs.md) |
| Azure Data Explorer | [Azure Data Explorer のログ](/azure/data-explorer/using-diagnostic-logs) |
| Azure Database for MySQL | [Azure Database for MySQL の診断ログ](../../mysql/concepts-server-logs.md#diagnostic-logs) |
| Azure Database for PostgreSQL | [Azure Database for PostgreSQL のログ](../../postgresql/concepts-server-logs.md#resource-logs) |
| Azure Databricks | [Azure Databricks の診断ログ](/azure/databricks/administration-guide/account-settings/azure-diagnostic-logs) |
| Azure Digital Twins | [Azure Digital Twins の診断を設定する](../../digital-twins/troubleshoot-diagnostics.md#log-schemas)
| Event Hubs |[Azure Event Hubs のログ](../../event-hubs/event-hubs-diagnostic-logs.md) |
| ExpressRoute | スキーマは使用できません。 |
| Azure Firewall | スキーマは使用できません。 |
| フロントドア | [Front Door のログ](../../frontdoor/front-door-diagnostics.md) |
| IoT Hub | [IoT Hub 操作](../../iot-hub/monitor-iot-hub-reference.md#resource-logs) |
| Key Vault |[Azure Key Vault のログ記録](../../key-vault/general/logging.md) |
| Kubernetes Service |[Azure Kubernetes のログ記録](../../aks/view-control-plane-logs.md#log-event-schema) |
| Load Balancer |[Azure Load Balancer のログ分析](../../load-balancer/load-balancer-monitor-log.md) |
| Logic Apps |[Logic Apps B2B カスタム追跡スキーマ](../../logic-apps/logic-apps-track-integration-account-custom-tracking-schema.md) |
| ネットワーク セキュリティ グループ |[ネットワーク セキュリティ グループ (NSG) のためのログ分析](../../virtual-network/virtual-network-nsg-manage-log.md) |
| DDoS Protection | [Azure DDoS Protection Standard のログ](../../ddos-protection/diagnostic-logging.md#log-schemas) |
| Power BI 専用 | [Azure の Power BI Embedded のログ記録](/power-bi/developer/azure-pbie-diag-logs) |
| 復旧サービス | [Azure Backup のデータ モデル](../../backup/backup-azure-reports-data-model.md)|
| 検索 |[検索トラフィックの分析の有効化と使用](../../search/search-traffic-analytics.md) |
| Service Bus |[Azure Service Bus のログ](../../service-bus-messaging/service-bus-diagnostic-logs.md) |
| SQL Database | [Azure SQL Database のログ記録](../../azure-sql/database/metrics-diagnostic-telemetry-logging-streaming-export-configure.md) |
| Stream Analytics |[ジョブのログ](../../stream-analytics/stream-analytics-job-diagnostic-logs.md) |
| Traffic Manager | [Traffic Manager のログのスキーマ](../../traffic-manager/traffic-manager-diagnostic-logs.md) |
| 仮想ネットワーク | スキーマは使用できません。 |
| 仮想ネットワーク ゲートウェイ | スキーマは使用できません。 |



## <a name="next-steps"></a>次の手順

* [収集できるリソース ログのカテゴリを確認する](resource-logs-categories.md)
* [リソース ログの詳細を確認する](../essentials/platform-logs-overview.md)
* [リソースのリソース ログを **Event Hubs** にストリーミングする](./resource-logs.md#send-to-azure-event-hubs)
* [Azure Monitor REST API を使用してリソース ログの診断設定を変更する](/rest/api/monitor/diagnosticsettings)
* [Log Analytics を使用した、Azure ストレージからのログの分析](./resource-logs.md#send-to-log-analytics-workspace)