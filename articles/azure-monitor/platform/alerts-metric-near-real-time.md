---
title: Azure Monitor のメトリック アラートでサポートされるリソース
description: Azure Monitor のメトリック アラートでサポートされるメトリックとログのリファレンス
author: snehithm
services: monitoring
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 06/29/2018
ms.author: snmuvva
ms.component: alerts
ms.openlocfilehash: be9f8079480438b194f0d5779ac0da50328b5dd3
ms.sourcegitcommit: 7cd706612a2712e4dd11e8ca8d172e81d561e1db
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/18/2018
ms.locfileid: "53587345"
---
# <a name="supported-resources-for-metric-alerts-in-azure-monitor"></a>Azure Monitor のメトリック アラートでサポートされるリソース

Azure Monitor が[新しいタイプのメトリック アラート](../../azure-monitor/platform/alerts-overview.md)をサポートするようになりました。このタイプには、古い[クラシック メトリック アラート](../../azure-monitor/platform/alerts-classic.overview.md)と比較して多くのメリットがあります。 メトリックは[さまざまな Azure サービスで](../../azure-monitor/platform/metrics-supported.md)利用できます。 新しいアラートが対応しているリソース タイプは限られていますが、その数は増えています。 この記事では、このサブセットを一覧で示します。


新しいメトリック アラートは、メトリックとして抽出された一般的な Log Analytics ログに対しても使用できます。 詳細については、[ログのメトリック アラート](../../azure-monitor/platform/alerts-metric-logs.md)に関するページを参照してください。

## <a name="portal-powershell-cli-rest-support"></a>ポータル、PowerShell、CLI、REST のサポート
現在、新しいメトリック アラートは Azure portal、[REST API](https://docs.microsoft.com/rest/api/monitor/metricalerts/)、または [Resource Manager テンプレート](../../azure-monitor/platform/alerts-metric-create-templates.md)でのみ作成できます。 PowerShell と Azure CLI バージョン 2.0 以降を使用した新しいアラートの構成は、近日中にサポートされる予定です。

## <a name="metrics-and-dimensions-supported"></a>サポートされるメトリックとディメンション
新しいメトリック アラートでは、ディメンションを使用するメトリックのアラートがサポートされています。 ディメンションを使用すると、メトリックを適切なレベルにフィルター処理できます。 サポートされるすべてのメトリックと適用可能なディメンションは、[Azure Monitor - メトリックス エクスプローラー](../../azure-monitor/platform/metrics-charts.md)から探索および視覚化できます。

新しいアラートでサポートされている Azure Monitor のメトリック ソースの完全な一覧を次に示します。

|リソースの種類  |サポートされるディメンション  | 使用可能なメトリック|
|---------|---------|----------------|
|Microsoft.ApiManagement/service     | [はい]        | [API Management](../../azure-monitor/platform/metrics-supported.md#microsoftapimanagementservice)|
|Microsoft.Automation/automationAccounts     |     [はい]   | [Automation アカウント](../../azure-monitor/platform/metrics-supported.md#microsoftautomationautomationaccounts)|
|Microsoft.Batch/batchAccounts | 該当なし| [Batch アカウント](../../azure-monitor/platform/metrics-supported.md#microsoftbatchbatchaccounts)|
|Microsoft.Cache/Redis     |    該当なし     |[Azure Cache for Redis](../../azure-monitor/platform/metrics-supported.md#microsoftcacheredis)|
|Microsoft.CognitiveServices/accounts     |    該当なし     | [Cognitive Services](../../azure-monitor/platform/metrics-supported.md#microsoftcognitiveservicesaccounts)|
|Microsoft.Compute/virtualMachines     |    該当なし     | [Virtual Machines](../../azure-monitor/platform/metrics-supported.md#microsoftcomputevirtualmachines)|
|Microsoft.Compute/virtualMachineScaleSets     |   該当なし      |[仮想マシン スケール セット](../../azure-monitor/platform/metrics-supported.md#microsoftcomputevirtualmachinescalesets)|
|Microsoft.ContainerInstance/containerGroups | [はい]| [コンテナー グループ](../../azure-monitor/platform/metrics-supported.md#microsoftcontainerinstancecontainergroups)|
|Microsoft.ContainerService/managedClusters | [はい] | [マネージド クラスター](../../azure-monitor/platform/metrics-supported.md#microsoftcontainerservicemanagedclusters)|
|Microsoft.DataFactory/datafactories| [はい]| [データ ファクトリ V1](../../azure-monitor/platform/metrics-supported.md#microsoftdatafactorydatafactories)|
|Microsoft.DataFactory/factories     |   [はい]     |[データ ファクトリ V2](../../azure-monitor/platform/metrics-supported.md#microsoftdatafactoryfactories)|
|Microsoft.DBforMySQL/servers     |   該当なし      |[DB for MySQL](../../azure-monitor/platform/metrics-supported.md#microsoftdbformysqlservers)|
|Microsoft.DBforPostgreSQL/servers     |    該当なし     | [DB for PostgreSQL](../../azure-monitor/platform/metrics-supported.md#microsoftdbforpostgresqlservers)|
|Microsoft.EventHub/namespaces     |  [はい]      |[Event Hubs](../../azure-monitor/platform/metrics-supported.md#microsofteventhubnamespaces)|
|Microsoft.KeyVault/vaults| いいえ  | [資格情報コンテナー](../../azure-monitor/platform/metrics-supported.md#microsoftkeyvaultvaults)|
|Microsoft.Logic/workflows     |     該当なし    |[Logic Apps](../../azure-monitor/platform/metrics-supported.md#microsoftlogicworkflows) |
|Microsoft.Network/applicationGateways     |    該当なし     | [アプリケーション ゲートウェイ](../../azure-monitor/platform/metrics-supported.md#microsoftnetworkapplicationgateways) |
|Microsoft.Network/expressRouteCircuits | 該当なし |  [Express Route 回線](../../azure-monitor/platform/metrics-supported.md#microsoftnetworkexpressroutecircuits) |
|Microsoft.Network/dnsZones | 該当なし| [DNS Zones](../../azure-monitor/platform/metrics-supported.md#microsoftnetworkdnszones) |
|Microsoft.Network/loadBalancers (Standard SKU に対してのみ)| [はい]| [Load Balancers](../../azure-monitor/platform/metrics-supported.md#microsoftnetworkloadbalancers) |
|Microsoft.Network/publicipaddresses     |  該当なし       |[パブリック IP アドレス](../../azure-monitor/platform/metrics-supported.md#microsoftnetworkpublicipaddresses)|
|Microsoft.PowerBIDedicated/capacities | 該当なし | [Capacities](../../azure-monitor/platform/metrics-supported.md#microsoftpowerbidedicatedcapacities)|
|Microsoft.Network/trafficManagerProfiles | [はい] | [Traffic Manager プロファイル](../../azure-monitor/platform/metrics-supported.md#microsoftnetworktrafficmanagerprofiles) |
|Microsoft.Search/searchServices     |   該当なし      |[検索サービス](../../azure-monitor/platform/metrics-supported.md#microsoftsearchsearchservices)|
|Microsoft.ServiceBus/namespaces     |  [はい]       |[Service Bus](../../azure-monitor/platform/metrics-supported.md#microsoftservicebusnamespaces)|
|Microsoft.Storage/storageAccounts     |    [はい]     | [ストレージ アカウント](../../azure-monitor/platform/metrics-supported.md#microsoftstoragestorageaccounts)|
|Microsoft.Storage/storageAccounts/services     |     [はい]    | [Blob service](../../azure-monitor/platform/metrics-supported.md#microsoftstoragestorageaccountsblobservices)、[ファイル サービス](../../azure-monitor/platform/metrics-supported.md#microsoftstoragestorageaccountsfileservices)、[Queue サービス](../../azure-monitor/platform/metrics-supported.md#microsoftstoragestorageaccountsqueueservices)、および [Table service](../../azure-monitor/platform/metrics-supported.md#microsoftstoragestorageaccountstableservices)|
|Microsoft.StreamAnalytics/streamingjobs     |  該当なし       | [Stream Analytics](../../azure-monitor/platform/metrics-supported.md#microsoftstreamanalyticsstreamingjobs)|
| Microsoft.Web/serverfarms | [はい] | [App Service プラン](../../azure-monitor/platform/metrics-supported.md#microsoftwebserverfarms)  |
| Microsoft.Web/sites | [はい] | [App Services](../../azure-monitor/platform/metrics-supported.md#microsoftwebsites-excluding-functions) と [Functions](../../azure-monitor/platform/metrics-supported.md#microsoftwebsites-functions)|
| Microsoft.Web/sites/slots | [はい] | [App Service スロット](../../azure-monitor/platform/metrics-supported.md#microsoftwebsitesslots)|
|Microsoft.OperationalInsights/workspaces| [はい]|[Log Analytics ワークスペース](../../azure-monitor/platform/metrics-supported.md#microsoftoperationalinsightsworkspaces)|



## <a name="payload-schema"></a>ペイロード スキーマ

適切に構成された[アクション グループ](../../azure-monitor/platform/action-groups.md)が使用されている場合、POST 操作には、すべての新しいメトリック アラートに対する以下の JSON ペイロードとスキーマが含まれます。

```json
{
  "schemaId": "AzureMonitorMetricAlert",
  "data": {
    "version": "2.0",
    "status": "Activated",
    "context": {
      "timestamp": "2018-02-28T10:44:10.1714014Z",
      "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/Contoso/providers/microsoft.insights/metricAlerts/StorageCheck",
      "name": "StorageCheck",
      "description": "",
      "conditionType": "SingleResourceMultipleMetricCriteria",
      "condition": {
        "windowSize": "PT5M",
        "allOf": [
          {
            "metricName": "Transactions",
            "dimensions": [
              {
                "name": "AccountResourceId",
                "value": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/Contoso/providers/Microsoft.Storage/storageAccounts/diag500"
              },
              {
                "name": "GeoType",
                "value": "Primary"
              }
            ],
            "operator": "GreaterThan",
            "threshold": "0",
            "timeAggregation": "PT5M",
            "metricValue": 1
          }
        ]
      },
      "subscriptionId": "00000000-0000-0000-0000-000000000000",
      "resourceGroupName": "Contoso",
      "resourceName": "diag500",
      "resourceType": "Microsoft.Storage/storageAccounts",
      "resourceId": "/subscriptions/1e3ff1c0-771a-4119-a03b-be82a51e232d/resourceGroups/Contoso/providers/Microsoft.Storage/storageAccounts/diag500",
      "portalLink": "https://portal.azure.com/#resource//subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/Contoso/providers/Microsoft.Storage/storageAccounts/diag500"
    },
    "properties": {
      "key1": "value1",
      "key2": "value2"
    }
  }
}
```

## <a name="next-steps"></a>次の手順

* 新しい[アラート エクスペリエンス](../../azure-monitor/platform/alerts-overview.md)の詳細について学習します。
* [Azure でのログ アラート](../../azure-monitor/platform/alerts-unified-log.md)について学習します。
* [Azure のアラート](../../azure-monitor/platform/alerts-overview.md)について確認します。
