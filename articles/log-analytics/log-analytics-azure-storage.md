---
title: "Log Analytics での Azure サービスのログとメトリックの収集 | Microsoft Docs"
description: "Log Analytics にログとメトリックが書き込まれるように Azure のリソースの診断を構成します。"
services: log-analytics
documentationcenter: 
author: bandersmsft
manager: carmonm
editor: 
ms.assetid: 84105740-3697-4109-bc59-2452c1131bfe
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/09/2017
ms.author: banders
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: a087df444c5c88ee1dbcf8eb18abf883549a9024
ms.openlocfilehash: 04bab3024474ae521090a8693b6c095e1704d4dc
ms.lasthandoff: 03/15/2017


---
# <a name="collect-azure-service-logs-and-metrics-for-use-in-log-analytics"></a>Log Analytics で Azure サービスのログとメトリックを使用できるように収集する

Azure サービスのログとメトリックを収集する方法は&4; 種類あります。

1. Azure 診断から Log Analytics に直接 (次の表の "*診断*")
2. Azure 診断から Azure storage 経由で Log Analytics に (次の表の "*ストレージ*")
3. Azure サービスのコネクタ (次の表の "*コネクタ*")
4. スクリプトでデータを収集して Log Analytics に投稿 (次の表の空白セルと、表に記載されていないサービス用)


| サービス                 | リソースの種類                           | ログ        | メトリック     | 解決策 |
| --- | --- | --- | --- | --- |
| アプリケーション ゲートウェイ    | Microsoft.Network/applicationGateways   | 診断 | 診断 | [Azure Application Gateway 分析](log-analytics-azure-networking-analytics.md#azure-application-gateway-analytics-solution-in-log-analytics) |
| Application Insights    |                                         | コネクタ   | コネクタ   | [Application Insights コネクタ](https://blogs.technet.microsoft.com/msoms/2016/09/26/application-insights-connector-in-oms/) (プレビュー) |
| Automation アカウント     | Microsoft.Automation/AutomationAccounts | 診断 |             | [詳細情報](../automation/automation-manage-send-joblogs-log-analytics.md)|
| Batch アカウント          | Microsoft.Batch/batchAccounts           | 診断 | 診断 | |
| 従来のクラウド サービス  |                                         | Storage     |             | [詳細情報](log-analytics-azure-storage-iis-table.md) |
| Cognitive Services      | Microsoft.CognitiveServices/accounts    |             | 診断 | |
| Data Lake Analytics     | Microsoft.DataLakeAnalytics/accounts    | 診断 |             | |
| Data Lake Store         | Microsoft.DataLakeStore/accounts        | 診断 |             | |
| Event Hub 名前空間     | Microsoft.EventHub/namespaces           | 診断 | 診断 | |
| IoT Hub                | Microsoft.Devices/IotHubs               |             | 診断 | |
| Key Vault               | Microsoft.KeyVault/vaults               | 診断 |             | [KeyVault 分析](log-analytics-azure-key-vault.md) |
| ロード バランサー          | Microsoft.Network/loadBalancers         | 診断 |             |  |
| Logic Apps              | Microsoft.Logic/workflows <br> Microsoft.Logic/integrationAccounts | 診断 | 診断 | |
| ネットワーク セキュリティ グループ | Microsoft.Network/networksecuritygroups | 診断 |             | [Azure ネットワーク セキュリティ グループ分析](log-analytics-azure-networking-analytics.md#azure-network-security-group-analytics-solution-in-log-analytics) |
| Recovery コンテナー         | Microsoft.RecoveryServices/vaults       |             |             | [Azure Recovery Services Analytics (プレビュー)](https://github.com/krnese/AzureDeploy/blob/master/OMS/MSOMS/Solutions/recoveryservices/)|
| Search サービス         | Microsoft.Search/searchServices         | 診断 | 診断 | |
| Service Bus 名前空間   | Microsoft.ServiceBus/namespaces         | 診断 | 診断 | [Service Bus Analytics (プレビュー)](https://github.com/Azure/azure-quickstart-templates/tree/master/oms-servicebus-solution)|
| Service Fabric          |                                         | Storage     |             | [Service Fabric Analytics (プレビュー)](log-analytics-service-fabric.md) |
| SQL (v12)               | Microsoft.Sql/servers/databases <br> Microsoft.Sql/servers/elasticPools |             | 診断 | [Azure SQL Analytics (プレビュー)](log-analytics-azure-sql.md) |
| Storage                 |                                         |             | スクリプト      | [Azure Storage Analytics (プレビュー)](https://github.com/Azure/azure-quickstart-templates/tree/master/oms-azure-storage-analytics-solution) |
| Virtual Machines        | Microsoft.Compute/virtualMachines       | 内線番号   | 内線番号 <br> 診断  | |
| 仮想マシン スケール セット | Microsoft.Compute/virtualMachines <br> Microsoft.Compute/virtualMachineScaleSets/virtualMachines |             | 診断 | |
| Web サーバー ファーム        | Microsoft.Web/serverfarms               |             | 診断 | |
| Web サイト               | Microsoft.Web/sites <br> Microsoft.Web/sites/slots |             | 診断 | [Azure Web Apps Analytics (プレビュー)](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.AzureWebAppsAnalyticsOMS?tab=Overview) |


> [!NOTE]
> Azure 仮想マシン (Linux と Windows の両方) を監視する場合は、[Log Analytics VM 拡張機能](log-analytics-azure-vm-extension.md)のインストールをお勧めします。 仮想マシン内から収集された洞察は、エージェントによって提供されます。 仮想マシン スケール セットの拡張機能を使用することもできます。
>
>

## <a name="azure-diagnostics-direct-to-log-analytics"></a>Azure 診断から Log Analytics に直接
多くの Azure リソースで診断ログとメトリックを Log Analytics に直接書き込むことができます。分析用のデータを収集するにはこの方法がお勧めです。 Azure 診断を使用すると、データが Log Analytics に即座に書き込まれるため、データを最初にストレージに出力する必要はありません。

[Azure Monitor](../monitoring-and-diagnostics/monitoring-overview.md) をサポートする Azure リソースのログとメトリックは、Log Analytics に直接送信できます。

* 使用可能なメトリックの詳細については、「[Azure Monitor のサポートされるメトリック](../monitoring-and-diagnostics/monitoring-supported-metrics.md)」を参照してください。
* 利用できるログの詳細については、「[診断ログでサポートされているサービスとスキーマ](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md#supported-services-and-schema-for-diagnostic-logs)」を参照してください。

### <a name="enable-diagnostics-with-powershell"></a>PowerShell を使用して診断を有効にする
2016 年 11 月 (v2.3.0) 以降のリリースの [Azure PowerShell](https://docs.microsoft.com/powershell/azureps-cmdlets-docs/) が必要です。

次の PowerShell の例は、[Set-AzureRmDiagnosticSetting](https://docs.microsoft.com/powershell/resourcemanager/azurerm.insights/v2.3.0/set-azurermdiagnosticsetting) を使用してネットワーク セキュリティ グループで診断を有効にする方法を示しています。 同じ方法をサポート対象のすべてのリソースで利用できます。診断を有効にするリソースのリソース ID に `$resourceId` を設定します。

```powershell
$workspaceId = "/subscriptions/d2e37fee-1234-40b2-5678-0b2199de3b50/resourcegroups/oi-default-east-us/providers/microsoft.operationalinsights/workspaces/rollingbaskets"

$resourceId = "/SUBSCRIPTIONS/ec11ca60-1234-491e-5678-0ea07feae25c/RESOURCEGROUPS/DEMO/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/DEMO"

Set-AzureRmDiagnosticSetting -ResourceId $ResourceId  -WorkspaceId $workspaceId -Enabled $true
```

### <a name="enable-diagnostics-with-resource-manager-templates"></a>Resource Manager テンプレートを使用して診断を有効にする

リソースを作成するときにリソースで診断を有効にして診断を Log Analytics ワークスペースに送信するには、次のようなテンプレートを使用できます。 この例は Automation アカウント向けですが、サポートされているすべてのリソース タイプで動作します。

```json
        {
            "type": "Microsoft.Automation/automationAccounts/providers/diagnosticSettings",
            "name": "[concat(parameters('omsAutomationAccountName'), '/', 'Microsoft.Insights/service')]",
            "apiVersion": "2015-07-01",
            "dependsOn": [
                "[concat('Microsoft.Automation/automationAccounts/', parameters('omsAutomationAccountName'))]",
                "[concat('Microsoft.OperationalInsights/workspaces/', parameters('omsWorkspaceName'))]"
            ],
            "properties": {
                "workspaceId": "[resourceId('Microsoft.OperationalInsights/workspaces', parameters('omsWorkspaceName'))]",
                "logs": [
                    {
                        "category": "JobLogs",
                        "enabled": true
                    },
                    {
                        "category": "JobStreams",
                        "enabled": true
                    }
                ]
            }
        }
```

[!INCLUDE [log-analytics-troubleshoot-azure-diagnostics](../../includes/log-analytics-troubleshoot-azure-diagnostics.md)]

## <a name="azure-diagnostics-to-storage-then-to-log-analytics"></a>Azure 診断からストレージ経由で Log Analytics に

一部のリソース内からログを収集するには、Azure Storage にログを送信して、そこからログを読み取るように Log Analytics を構成することができます。

Log Analytics では、この方法を使用して、次のリソースとログについて Azure Storage から診断を収集できます。

| リソース | ログ |
| --- | --- |
| Service Fabric |ETWEvent <br> 操作イベント <br> Reliable Actor イベント <br> Reliable Service イベント |
| Virtual Machines |Linux Syslog <br> Windows イベント <br> IIS ログ <br> Windows ETWEvent |
| Web ロール <br> worker ロール |Linux Syslog <br> Windows イベント <br> IIS ログ <br> Windows ETWEvent |

> [!NOTE]
> ストレージ アカウントに診断を送信する際、および Log Analytics がストレージ アカウントからデータを読み取る際のデータの格納と転送には、通常の Azure 料金が課金されます。
>
>

Log Analytics でこれらのログを収集する方法の詳細については、[IIS の Blob Storage とイベントの Table Storage の使用](log-analytics-azure-storage-iis-table.md)に関するページをご覧ください。

## <a name="connectors-for-azure-services"></a>Azure サービスのコネクタ

Application Insights にはコネクタがあり、これを使用して、Application Insights で収集されたデータを Log Analytics に送信することができます。

[Application Insights コネクタ](https://blogs.technet.microsoft.com/msoms/2016/09/26/application-insights-connector-in-oms/)に関する詳細を確認してください。

## <a name="scripts-to-collect-and-post-data-to-log-analytics"></a>スクリプトでデータを収集して Log Analytics に投稿

Log Analytics にログとメトリックを直接送信する手段が提供されていない Azure のサービスについては、Azure Automation スクリプトを使用してログとメトリックを収集することができます。 次に、このスクリプトで[データ コレクター API](log-analytics-data-collector-api.md) を使用して Log Analytics にデータを送信できます。

Azure テンプレート ギャラリーには、サービスからデータを収集し、Log Analytics に送信するために [Azure Automation を使用する例](https://azure.microsoft.com/en-us/resources/templates/?term=OMS)が用意されています。

## <a name="next-steps"></a>次のステップ

* [Blob Storage (IIS の場合) と Table Storage (イベントの場合) を使用](log-analytics-azure-storage-iis-table.md)して、診断情報を Table Storage に出力する Azure サービスのログや、Blob Storage に出力された IIS ログを読み取ります。
* [ソリューションを有効](log-analytics-add-solutions.md) にして、データに対する洞察を得ます。
* [検索クエリを使用](log-analytics-log-searches.md) して、データを分析します。

