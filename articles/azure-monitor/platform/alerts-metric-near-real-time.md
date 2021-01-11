---
title: Azure Monitor のメトリック アラートでサポートされるリソース
description: Azure Monitor のメトリック アラートでサポートされるメトリックとログのリファレンス
author: harelbr
ms.author: harelbr
services: monitoring
ms.topic: conceptual
ms.date: 8/20/2020
ms.subservice: alerts
ms.openlocfilehash: 05586f95de03b09b95eb589812935970126b12f2
ms.sourcegitcommit: d18a59b2efff67934650f6ad3a2e1fe9f8269f21
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/20/2020
ms.locfileid: "88661318"
---
# <a name="supported-resources-for-metric-alerts-in-azure-monitor"></a>Azure Monitor のメトリック アラートでサポートされるリソース

Azure Monitor が[新しいタイプのメトリック アラート](./alerts-overview.md)をサポートするようになりました。このタイプには、古い[クラシック メトリック アラート](./alerts-classic.overview.md)と比較して多くのメリットがあります。 メトリックは[さまざまな Azure サービスで](./metrics-supported.md)利用できます。 新しいアラートが対応しているリソース タイプは限られていますが、その数は増えています。 この記事では、このサブセットを一覧で示します。

新しいメトリック アラートは、メトリックとして抽出された、Log Analytics ワークスペースに格納された一般的なログ データに対しても使用できます。 詳細については、[ログのメトリック アラート](./alerts-metric-logs.md)に関するページを参照してください。

## <a name="portal-powershell-cli-rest-support"></a>ポータル、PowerShell、CLI、REST のサポート
現在、新しいメトリック アラートは Azure portal、[REST API](/rest/api/monitor/metricalerts/)、または [Resource Manager テンプレート](./alerts-metric-create-templates.md)でのみ作成できます。 PowerShell と Azure CLI バージョン 2.0 以降を使用した新しいアラートの構成は、近日中にサポートされる予定です。

## <a name="metrics-and-dimensions-supported"></a>サポートされるメトリックとディメンション
新しいメトリック アラートでは、ディメンションを使用するメトリックのアラートがサポートされています。 ディメンションを使用すると、メトリックを適切なレベルにフィルター処理できます。 サポートされるすべてのメトリックと適用可能なディメンションは、[Azure Monitor - メトリックス エクスプローラー](./metrics-charts.md)から探索および視覚化できます。

新しいアラートでサポートされている Azure Monitor のメトリック ソースの完全な一覧を次に示します。

|リソースの種類  |サポートされるディメンション |マルチリソース アラート| 使用可能なメトリック|
|---------|---------|-----|----------|
|Microsoft.Aadiam/azureADMetrics | はい | いいえ | |
|Microsoft.ApiManagement/service | はい | いいえ | [API Management](./metrics-supported.md#microsoftapimanagementservice) |
|Microsoft.AppConfiguration/configurationStores |[はい] | いいえ | [App Configuration](./metrics-supported.md#microsoftappconfigurationconfigurationstores) |
|Microsoft.AppPlatform/Spring | [はい] | いいえ | [Azure Spring Cloud](./metrics-supported.md#microsoftappplatformspring) |
|Microsoft.Automation/automationAccounts | はい| いいえ | [Automation アカウント](./metrics-supported.md#microsoftautomationautomationaccounts) |
|Microsoft.AVS/privateClouds | いいえ | いいえ | |
|Microsoft.Batch/batchAccounts | [はい] | いいえ | [Batch アカウント](./metrics-supported.md#microsoftbatchbatchaccounts) |
|Microsoft.Cache/Redis | はい | いいえ | [Azure Cache for Redis](./metrics-supported.md#microsoftcacheredis) |
|Microsoft.ClassicStorage/storageAccounts | [はい] | いいえ | [ストレージ アカウント (クラシック)](./metrics-supported.md#microsoftclassicstoragestorageaccounts) |
|Microsoft.ClassicStorage/storageAccounts/blobServices | [はい] | いいえ | |
|Microsoft.ClassicStorage/storageAccounts/fileServices | [はい] | いいえ | |
|Microsoft.ClassicStorage/storageAccounts/queueServices | [はい] | いいえ | |
|Microsoft.ClassicStorage/storageAccounts/tableServices | [はい] | いいえ | |
|Microsoft.CognitiveServices/accounts | [はい] | いいえ | [Cognitive Services](./metrics-supported.md#microsoftcognitiveservicesaccounts) |
|Microsoft.Compute/virtualMachines | はい | 可<sup>1</sup> | [Virtual Machines](./metrics-supported.md#microsoftcomputevirtualmachines) |
|Microsoft.Compute/virtualMachineScaleSets | [はい] | いいえ |[仮想マシン スケール セット](./metrics-supported.md#microsoftcomputevirtualmachinescalesets) |
|Microsoft.ContainerInstance/containerGroups | はい| いいえ | [コンテナー グループ](./metrics-supported.md#microsoftcontainerinstancecontainergroups) |
|Microsoft.ContainerRegistry/registries | × | いいえ | [コンテナー レジストリ](./metrics-supported.md#microsoftcontainerregistryregistries) |
|Microsoft.ContainerService/managedClusters | はい | いいえ | [マネージド クラスター](./metrics-supported.md#microsoftcontainerservicemanagedclusters) |
|Microsoft.DataBoxEdge/dataBoxEdgeDevices | はい | ○ | [Data Box](./metrics-supported.md#microsoftdataboxedgedataboxedgedevices) |
|Microsoft.DataFactory/datafactories| はい| いいえ | [データ ファクトリ V1](./metrics-supported.md#microsoftdatafactorydatafactories) |
|Microsoft.DataFactory/factories |はい | × | [データ ファクトリ V2](./metrics-supported.md#microsoftdatafactoryfactories) |
|Microsoft.DataShare/accounts | [はい] | × | |
|Microsoft.DBforMariaDB/servers | × | いいえ | [DB for MariaDB](./metrics-supported.md#microsoftdbformariadbservers) |
|Microsoft.DBforMySQL/servers | × | いいえ |[DB for MySQL](./metrics-supported.md#microsoftdbformysqlservers)|
|Microsoft.DBforPostgreSQL/flexibleServers | はい | いいえ | |
|Microsoft.DBforPostgreSQL/servers | いいえ | いいえ | [DB for PostgreSQL](./metrics-supported.md#microsoftdbforpostgresqlservers)|
|Microsoft.DBforPostgreSQL/serversv2 | いいえ | いいえ | [DB for PostgreSQL V2](./metrics-supported.md#microsoftdbforpostgresqlserversv2)|
|Microsoft.DBforPostgreSQL/singleservers | いいえ | いいえ | [DB for PostgreSQL (単一サーバー)](./metrics-supported.md#microsoftdbforpostgresqlsingleservers)|
|Microsoft.Devices/IotHubs | [はい] | いいえ |[IoT Hub](./metrics-supported.md#microsoftdevicesiothubs) |
|Microsoft.Devices/provisioningServices| はい | いいえ | [デバイス プロビジョニング サービス](./metrics-supported.md#microsoftdevicesprovisioningservices) |
|Microsoft.DigitalTwins/digitalTwinsInstances | はい | いいえ | |
|Microsoft.DocumentDB/databaseAccounts | はい | いいえ | [Cosmos DB](./metrics-supported.md#microsoftdocumentdbdatabaseaccounts) |
|Microsoft.EventGrid/domains | はい | いいえ | [Event Grid ドメイン](./metrics-supported.md#microsofteventgriddomains) |
|Microsoft.EventGrid/systemTopics | はい | いいえ | [Event Grid システム トピック](./metrics-supported.md#microsofteventgridsystemtopics) |
|Microsoft.EventGrid/topics |はい | いいえ | [Event Grid トピック](./metrics-supported.md#microsofteventgridtopics) |
|Microsoft.EventHub/clusters |はい| いいえ | [Event Hubs クラスター](./metrics-supported.md#microsofteventhubclusters) |
|Microsoft.EventHub/namespaces |はい| いいえ | [Event Hubs](./metrics-supported.md#microsofteventhubnamespaces) |
|Microsoft.HDInsight/clusters | はい | いいえ | [HDInsight クラスター](./metrics-supported.md#microsofthdinsightclusters) |
|Microsoft.Insights/Components | はい | いいえ | [Application Insights](./metrics-supported.md#microsoftinsightscomponents) |
|Microsoft.KeyVault/vaults | はい |はい |[資格情報コンテナー](./metrics-supported.md#microsoftkeyvaultvaults)|
|Microsoft.Kusto/Clusters | はい |いいえ |[Data Explorer クラスター](./metrics-supported.md#microsoftkustoclusters)|
|Microsoft.Logic/integrationServiceEnvironments | はい | × |[統合サービス環境](./metrics-supported.md#microsoftlogicintegrationserviceenvironments) |
|Microsoft.Logic/workflows | × | いいえ |[Logic Apps](./metrics-supported.md#microsoftlogicworkflows) |
|Microsoft.MachineLearningServices/workspaces | はい | いいえ | [Machine Learning](./metrics-supported.md#microsoftmachinelearningservicesworkspaces) |
|Microsoft.Maps/accounts | はい | いいえ | [Maps アカウント](./metrics-supported.md#microsoftmapsaccounts) |
|Microsoft.Media/mediaservices | いいえ | いいえ | [Media Services](./metrics-supported.md#microsoftmediamediaservices) |
|Microsoft.Media/mediaservices/streamingEndpoints | はい | いいえ | [Media Services ストリーミング エンドポイント](./metrics-supported.md#microsoftmediamediaservicesstreamingendpoints) |
|Microsoft.NetApp/netAppAccounts/capacityPools | はい | はい | [Azure NetApp 容量プール](./metrics-supported.md#microsoftnetappnetappaccountscapacitypools) |
|Microsoft.NetApp/netAppAccounts/capacityPools/volumes | はい | はい | [Azure NetApp ボリューム](./metrics-supported.md#microsoftnetappnetappaccountscapacitypoolsvolumes) |
|Microsoft.Network/applicationGateways | はい | いいえ | [アプリケーション ゲートウェイ](./metrics-supported.md#microsoftnetworkapplicationgateways) |
|Microsoft.Network/azurefirewalls | はい | × | [ファイアウォール](./metrics-supported.md#microsoftnetworkazurefirewalls) |
|Microsoft.Network/dnsZones | × | いいえ | [DNS Zones](./metrics-supported.md#microsoftnetworkdnszones) |
|Microsoft.Network/expressRouteCircuits | 該当なし | × |[Express Route 回線](./metrics-supported.md#microsoftnetworkexpressroutecircuits) |
|Microsoft.Network/loadBalancers (Standard SKU に対してのみ)| はい| いいえ | [Load Balancers](./metrics-supported.md#microsoftnetworkloadbalancers) |
|Microsoft.Network/natGateways| いいえ | × | |
|Microsoft.Network/privateEndpoints| いいえ | × | |
|Microsoft.Network/privateLinkServices| いいえ | × |
|Microsoft.Network/publicipaddresses | × | いいえ |[パブリック IP アドレス](./metrics-supported.md#microsoftnetworkpublicipaddresses)|
|Microsoft.Network/trafficManagerProfiles | はい | いいえ | [Traffic Manager プロファイル](./metrics-supported.md#microsoftnetworktrafficmanagerprofiles) |
|Microsoft.OperationalInsights/workspaces| はい | いいえ | [Log Analytics ワークスペース](./metrics-supported.md#microsoftoperationalinsightsworkspaces)|
|Microsoft.Peering/peerings | はい | いいえ | [ピアリング](./metrics-supported.md#microsoftpeeringpeerings) |
|Microsoft.Peering/peeringServices | はい | いいえ | [Peering Services](./metrics-supported.md#microsoftpeeringpeeringservices) |
|Microsoft.PowerBIDedicated/capacities | × | いいえ | [Capacities](./metrics-supported.md#microsoftpowerbidedicatedcapacities) |
|Microsoft.Relay/namespaces | はい | いいえ | [リレー](./metrics-supported.md#microsoftrelaynamespaces) |
|Microsoft.Search/searchServices | いいえ | いいえ | [検索サービス](./metrics-supported.md#microsoftsearchsearchservices) |
|Microsoft.ServiceBus/namespaces | はい | × | [Service Bus](./metrics-supported.md#microsoftservicebusnamespaces) |
|Microsoft.Sql/managedInstances | × | ○ | [SQL Managed Instance](./metrics-supported.md#microsoftsqlmanagedinstances) |
|Microsoft.Sql/servers/databases | いいえ | ○ | [SQL Database](./metrics-supported.md#microsoftsqlserversdatabases) |
|Microsoft.Sql/servers/elasticPools | × | ○ | [SQL エラスティック プール](./metrics-supported.md#microsoftsqlserverselasticpools) |
|Microsoft.Storage/storageAccounts |はい | いいえ | [ストレージ アカウント](./metrics-supported.md#microsoftstoragestorageaccounts)|
|Microsoft.Storage/storageAccounts/services | はい| いいえ | [Blob service](./metrics-supported.md#microsoftstoragestorageaccountsblobservices)、[ファイル サービス](./metrics-supported.md#microsoftstoragestorageaccountsfileservices)、[Queue サービス](./metrics-supported.md#microsoftstoragestorageaccountsqueueservices)、および [Table service](./metrics-supported.md#microsoftstoragestorageaccountstableservices)|
|Microsoft.StorageCache/caches | はい | × | |
|Microsoft.StorageSync/storageSyncServices | はい | × | [ストレージ同期サービス](./metrics-supported.md#microsoftstoragesyncstoragesyncservices) |
|Microsoft.StreamAnalytics/streamingjobs | はい | × | [Stream Analytics](./metrics-supported.md#microsoftstreamanalyticsstreamingjobs) |
|Microsoft.VMWareCloudSimple/virtualMachines | はい | いいえ | [CloudSimple 仮想マシン](./metrics-supported.md#microsoftvmwarecloudsimplevirtualmachines) |
|Microsoft.Web/hostingEnvironments/multiRolePools | はい | いいえ | [App Service Environment マルチロール プール](./metrics-supported.md#microsoftwebhostingenvironmentsmultirolepools)|
|Microsoft.Web/hostingEnvironments/workerPools | はい | いいえ | [App Service Environment ワーカー プール](./metrics-supported.md#microsoftwebhostingenvironmentsworkerpools)|
|Microsoft.Web/serverfarms | はい | いいえ | [App Service プラン](./metrics-supported.md#microsoftwebserverfarms)|
|Microsoft.Web/sites | はい | いいえ | [App Services](./metrics-supported.md#microsoftwebsites-excluding-functions) と [Functions](./metrics-supported.md#microsoftwebsites-functions)|
|Microsoft.Web/sites/slots | はい | いいえ | [App Service スロット](./metrics-supported.md#microsoftwebsitesslots)|

<sup>1</sup> (カスタム メトリックではなく) プラットフォーム メトリックのみでサポートされます

## <a name="payload-schema"></a>ペイロード スキーマ

> [!NOTE]
> [共通アラート スキーマ](https://aka.ms/commonAlertSchemaDocs)を使用することもできます。このスキーマの利点は、Azure Monitor のすべてのアラート サービスの垣根を越えて、拡張可能かつ一元化された単一のアラート ペイロードによって Webhook の統合を実現できることです。 [共通アラート スキーマの定義については、こちらを参照してください。](https://aka.ms/commonAlertSchemaDefinitions)


適切に構成された[アクション グループ](./action-groups.md)が使用されている場合、POST 操作には、すべての新しいメトリック アラートに対する以下の JSON ペイロードとスキーマが含まれます。

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
      "severity":"3",
      "condition": {
        "windowSize": "PT5M",
        "allOf": [
          {
            "metricName": "Transactions",
            "metricNamespace":"microsoft.storage/storageAccounts",
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

## <a name="next-steps"></a>次のステップ

* 新しい[アラート エクスペリエンス](./alerts-overview.md)の詳細について学習します。
* [Azure でのログ アラート](./alerts-unified-log.md)について学習します。
* [Azure のアラート](./alerts-overview.md)について確認します。

