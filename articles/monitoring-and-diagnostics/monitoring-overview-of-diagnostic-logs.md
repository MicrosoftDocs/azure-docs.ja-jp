---
title: "Azure 診断ログの概要 | Microsoft Docs"
description: "Azure 診断ログの概要と、診断ログを使用して Azure リソース内で発生するイベントを把握する方法について説明します。"
author: johnkemnetz
manager: rboucher
editor: 
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: fe8887df-b0e6-46f8-b2c0-11994d28e44f
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/17/2017
ms.author: johnkem; magoedte
translationtype: Human Translation
ms.sourcegitcommit: bb1ca3189e6c39b46eaa5151bf0c74dbf4a35228
ms.openlocfilehash: be27a3541caa1620af432dcff438f70cb9b1074b
ms.lasthandoff: 03/18/2017


---
# <a name="collect-and-consume-diagnostic-data-from-your-azure-resources"></a>Azure リソースからの診断データの収集と使用

## <a name="what-are-azure-diagnostic-logs"></a>Azure 診断ログとは
**Azure 診断ログ**は、リソースによって出力されるログであり、そのリソースの操作に関する豊富なデータを提供します。 これらのログの内容は、リソースの種類によって異なります。 たとえば、Windows イベント システム ログは、VM、BLOB、テーブルの診断ログのカテゴリの 1 つであり、キューのログは、ストレージ アカウントの診断ログのカテゴリです。

診断ログは、[アクティビティ ログ (以前は監査ログまたは操作ログと呼ばれていたもの)](monitoring-overview-activity-logs.md) とは異なります。 アクティビティ ログでは、サブスクリプションのリソースに対して実行された操作を調査できます。 診断ログでは、リソース自体が実行した操作を調査できます。

ここで説明する新しい種類の診断ログは、すべてのリソースでサポートされているわけではありません。 この記事には、新しい診断ログをサポートするリソースの種類の一覧を紹介するセクションがあります。

![診断ログとその他の種類のログ ](./media/monitoring-overview-of-diagnostic-logs/Diagnostics_Logs_vs_other_logs_v5.png)

図 1: 診断ログとその他の種類のログ

## <a name="what-you-can-do-with-diagnostic-logs"></a>診断ログで実行できること
診断ログでは次のことを実行できます。

![診断ログの論理的配置](./media/monitoring-overview-of-diagnostic-logs/Diagnostics_Logs_Actions.png)


* 監査や手動での検査に使用するために診断ログを[**ストレージ アカウント**](monitoring-archive-diagnostic-logs.md)に保存する。 **診断設定**を使用して、リテンション期間 (日数) を指定できます。
* サード パーティのサービスや PowerBI などのカスタム分析ソリューションで取り込むために、[診断ログを **Event Hubs** にストリーミング](monitoring-stream-diagnostic-logs-to-event-hubs.md)する。
* 診断ログを [OMS Log Analytics](../log-analytics/log-analytics-azure-storage.md)

ログを出力するサブスクリプションとは別のサブスクリプションで、ストレージ アカウントまたはイベント ハブ名前空間を使用できます。 設定を構成するユーザーは、両方のサブスクリプションに対して適切な RBAC アクセスを持っている必要があります。

## <a name="diagnostic-settings"></a>診断設定
非コンピューティング リソースの診断ログは、診断設定を使用して構成します。 **診断設定** では、以下を制御します。

* 診断ログの送信先 (ストレージ アカウント、Event Hubs、OMS Log Analytics)。
* 送信するログ カテゴリ。
* ログの各カテゴリをストレージ アカウントに保持する期間。
    - リテンション期間が 0 日の場合、ログは永続的に保持されます。 リテンション期間が 0 日の場合、ログは永続的に保持されます。
    - リテンション期間ポリシーが設定されていても、ストレージ アカウントへのログの保存が無効になっている場合 (たとえば、Event Hubs または OMS オプションだけが選択されている場合)、保持ポリシーは無効になります。
    - 保持ポリシーは日単位で適用されるため、その日の終わり (UTC) に、保持ポリシーの期間を超えることになるログは削除されます。 たとえば、保持ポリシーが 1 日の場合、その日が始まった時点で、一昨日のログは削除されます。

これらの設定は、Azure Portal 内のリソースの [診断] ブレード、Azure PowerShell および CLI のコマンド、または [Azure Monitor REST API](https://msdn.microsoft.com/library/azure/dn931943.aspx) を使用して簡単に構成できます。

> [!WARNING]
> コンピューティング リソース (VM や Service Fabric など) の診断ログとメトリックでは、 [出力の構成と選択に別のメカニズム](../azure-diagnostics.md)を使用します。
>
>

## <a name="how-to-enable-collection-of-diagnostic-logs"></a>診断ログの収集を有効にする方法
診断ログの収集は、リソースの作成中に有効にすることも、リソースの作成後にポータルのリソースのブレードで有効にすることもできます。 また、診断ログは、Azure PowerShell または CLI のコマンドを使用するか、Azure Monitor REST API を使用していつでも有効にすることができます。

> [!TIP]
> これらの手順は、すべてのリソースに直接適用できるわけではありません。 特定のリソースの種類に適用できる具体的な手順については、このページの最後にあるスキーマのリンクを参照してください。
>
>

[リソース テンプレートを使用して、リソースの作成時に診断設定を有効にする方法については、こちらの記事をご覧ください。](monitoring-enable-diagnostic-logs-using-template.md)

### <a name="enable-diagnostic-logs-in-the-portal"></a>ポータルでの診断ログの有効化
コンピューティング タイプのリソースの場合には、その作成時に Azure Portal で Windows または Linux の Azure Diagnostics 拡張機能を有効にすると、診断ログを有効にできます。

1. **[新規]** に移動し、興味のあるリソースを選択します。
2. 基本設定を構成し、サイズを選択したら、**[設定]** ブレードの **[監視]** で **[有効]** を選択し、診断ログの保存先となるストレージ アカウントを選択します。 診断データをストレージ アカウントに送信する際には、ストレージとトランザクションの通常のデータ料金が発生します。

   ![リソースの作成時に診断ログを有効にする](./media/monitoring-overview-of-diagnostic-logs/enable-portal-new.png)
3. **[OK]** をクリックしてリソースを作成します。

コンピューティング以外のリソースについては、リソースの作成後に Azure Portal で以下を行うと、診断ログを有効にできます。

1. リソースのブレードに移動し、 **[診断]** ブレードを開きます。
2. **[オン]** をクリックし、ストレージ アカウントおよび Event Hubs を選択します。

   ![リソースの作成後に診断ログを有効にする](./media/monitoring-overview-of-diagnostic-logs/enable-portal-existing.png)
3. **[ログ]** で、収集またはストリーミングする**ログ カテゴリ**を選択します。
4. [ **Save**] をクリックします。

### <a name="enable-diagnostic-logs-via-powershell"></a>PowerShell での診断ログの有効化
Azure PowerShell コマンドレットを使用して診断ログを有効にするには、次のコマンドを使用します。

ストレージ アカウントへの診断ログの保存を有効にするには、次のコマンドを使用します。

```powershell
    Set-AzureRmDiagnosticSetting -ResourceId [your resource id] -StorageAccountId [your storage account id] -Enabled $true
```

ストレージ アカウント ID は、ログの送信先となるストレージ アカウントのリソース ID です。

Event Hubs への診断ログのストリーミングを有効にするには、次のコマンドを使用します。

```powershell
    Set-AzureRmDiagnosticSetting -ResourceId [your resource id] -ServiceBusRuleId [your service bus rule id] -Enabled $true
```

Service Bus 規則 ID は、 `{service bus resource ID}/authorizationrules/{key name}`の形式の文字列です。

Log Analytics ワークスペースへの診断ログの送信を有効にするには、次のコマンドを使用します。

```powershell
    Set-AzureRmDiagnosticSetting -ResourceId [your resource id] -WorkspaceId [resource id of the log analytics workspace] -Enabled $true
```

次のコマンドを使用して、Log Analytics ワークスペースのリソース IDを取得できます。

```powershell
(Get-AzureRmOperationalInsightsWorkspace).ResourceId
```

このパラメーターを組み合わせて、複数の出力オプションを有効にできます。

### <a name="enable-diagnostic-logs-via-cli"></a>CLI での診断ログの有効化
Azure CLI を使用して診断ログを有効にするには、次のコマンドを使用します。

ストレージ アカウントへの診断ログの保存を有効にするには、次のコマンドを使用します。

```azurecli
    azure insights diagnostic set --resourceId <resourceId> --storageId <storageAccountId> --enabled true
```

ストレージ アカウント ID は、ログの送信先となるストレージ アカウントのリソース ID です。

Event Hubs への診断ログのストリーミングを有効にするには、次のコマンドを使用します。

```azurecli
    azure insights diagnostic set --resourceId <resourceId> --serviceBusRuleId <serviceBusRuleId> --enabled true
```

Service Bus 規則 ID は、 `{service bus resource ID}/authorizationrules/{key name}`の形式の文字列です。

Log Analytics ワークスペースへの診断ログの送信を有効にするには、次のコマンドを使用します。

```azurecli
    azure insights diagnostic set --resourceId <resourceId> --workspaceId <resource id of the log analytics workspace> --enabled true
```

このパラメーターを組み合わせて、複数の出力オプションを有効にできます。

### <a name="enable-diagnostic-logs-via-rest-api"></a>REST API での診断ログの有効化
Azure Monitor REST API を使用して診断設定を変更する場合は、[こちらのドキュメント](https://msdn.microsoft.com/library/azure/dn931931.aspx)をご覧ください。

## <a name="manage-diagnostic-settings-in-the-portal"></a>ポータルでの診断設定の管理
すべてのリソースについて診断設定がセットアップされていることを確認してください。 ポータルの **[監視]** ブレードに移動し、**[診断ログ]** ブレードを開きます。

![ポータルの [診断ログ] ブレード](./media/monitoring-overview-of-diagnostic-logs/manage-portal-nav.png)

[More services (その他のサービス)] をクリックして、[監視] ブレードを見つけます。

このブレードでは、診断ログをサポートするすべてのリソースを表示し、フィルター処理することで、有効になっている診断があるかどうかを確認できます。 さらに、これらのログがどのストレージ アカウント、イベント ハブ、Log Analytics ワークスペースに送られているかも確認できます。

![ポータルの [診断ログ] ブレードの結果](./media/monitoring-overview-of-diagnostic-logs/manage-portal-blade.png)

リソースをクリックすると、ストレージ アカウントに保存されているすべてのログを表示できるほか、診断設定を無効にしたり、変更したりすることができます。 ダウンロード アイコンをクリックすると、特定の期間のログをダウンロードできます。

![1 つのリソースの [診断ログ] ブレード](./media/monitoring-overview-of-diagnostic-logs/manage-portal-logs.png)

> [!NOTE]
> 診断ログは、このビューだけに表示され、診断ログをストレージ アカウントに保存するように構成した場合にのみダウンロードできます。
>
>

**[診断設定]** のリンクをクリックすると [診断設定] ブレードが表示され、選択したリソースの診断設定を有効化、無効化、または変更できます。

## <a name="supported-services-and-schema-for-diagnostic-logs"></a>診断ログでサポートされているサービスとスキーマ
診断ログのスキーマは、リソースとログ カテゴリによって異なります。   

| サービス | スキーマとドキュメント |
| --- | --- |
| Load Balancer |[Azure Load Balancer のログ分析](../load-balancer/load-balancer-monitor-log.md) |
| ネットワーク セキュリティ グループ |[ネットワーク セキュリティ グループ (NSG) のためのログ分析](../virtual-network/virtual-network-nsg-manage-log.md) |
| Application Gateway |[Application Gateway の診断ログ](../application-gateway/application-gateway-diagnostics.md) |
| Key Vault |[Azure Key Vault のログ記録](../key-vault/key-vault-logging.md) |
| Azure Search |[検索トラフィックの分析の有効化と使用](../search/search-traffic-analytics.md) |
| Data Lake Store |[Azure Data Lake Store の診断ログへのアクセス](../data-lake-store/data-lake-store-diagnostic-logs.md) |
| Data Lake Analytics |[Azure Data Lake Analytics の診断ログへのアクセス](../data-lake-analytics/data-lake-analytics-diagnostic-logs.md) |
| Logic Apps |[Logic Apps B2B カスタム追跡スキーマ](../logic-apps/logic-apps-track-integration-account-custom-tracking-schema.md) |
| Azure Batch |[Azure Batch 診断ログ](../batch/batch-diagnostics.md) |
| Azure Automation |[Azure Automation のログ分析](../automation/automation-manage-send-joblogs-log-analytics.md) |
| Event Hubs |[Azure Event Hubs の診断ログ](../event-hubs/event-hubs-diagnostic-logs.md) |
| Stream Analytics |[ジョブの診断ログ](../stream-analytics/stream-analytics-job-diagnostic-logs.md) |
| Service Bus |[Azure Service Bus の診断ログ](../service-bus-messaging/service-bus-diagnostic-logs.md) |


## <a name="supported-log-categories-per-resource-type"></a>リソースの種類ごとのサポートされているログ カテゴリ
|リソースの種類|カテゴリ|カテゴリの表示名|
|---|---|---|
|Microsoft.ApiManagement/service|GatewayLogs|ApiManagement Gateway に関連するログ|
|Microsoft.Automation/automationAccounts|JobLogs|ジョブ ログ|
|Microsoft.Automation/automationAccounts|JobStreams|ジョブ ストリーム|
|Microsoft.Automation/automationAccounts|DscNodeStatus|DSC ノードの状態|
|Microsoft.Batch/batchAccounts|ServiceLog|サービス ログ|
|Microsoft.DataLakeAnalytics/accounts|Audit|Audit Logs|
|Microsoft.DataLakeAnalytics/accounts|要求数|要求ログ|
|Microsoft.DataLakeStore/accounts|Audit|Audit Logs|
|Microsoft.DataLakeStore/accounts|要求数|要求ログ|
|Microsoft.EventHub/namespaces|ArchiveLogs|アーカイブ ログ|
|Microsoft.EventHub/namespaces|OperationalLogs|操作ログ|
|Microsoft.EventHub/namespaces|AutoScaleLogs|自動スケール ログ|
|Microsoft.KeyVault/vaults|AuditEvent|Audit Logs|
|Microsoft.Logic/workflows|WorkflowRuntime|ワークフロー ランタイムの診断イベント|
|Microsoft.Logic/integrationAccounts|IntegrationAccountTrackingEvents|統合アカウント追跡イベント|
|Microsoft.Network/networksecuritygroups|NetworkSecurityGroupEvent|ネットワーク セキュリティ グループ イベント|
|Microsoft.Network/networksecuritygroups|NetworkSecurityGroupRuleCounter|ネットワーク セキュリティ グループの規則数|
|Microsoft.Network/networksecuritygroups|NetworkSecurityGroupFlowEvent|ネットワーク セキュリティ グループの規則フロー イベント|
|Microsoft.Network/loadBalancers|LoadBalancerAlertEvent|ロード バランサーのアラート イベント|
|Microsoft.Network/loadBalancers|LoadBalancerProbeHealthStatus|ロード バランサーのプローブ正常性状態|
|Microsoft.Network/applicationGateways|ApplicationGatewayAccessLog|アプリケーション ゲートウェイのアクセス ログ|
|Microsoft.Network/applicationGateways|ApplicationGatewayPerformanceLog|アプリケーション ゲートウェイのパフォーマンス ログ|
|Microsoft.Network/applicationGateways|ApplicationGatewayFirewallLog|アプリケーション ゲートウェイのファイアウォール ログ|
|Microsoft.Network/expressRouteCircuits|GWMCountersTable|GWM カウンターのテーブル|
|Microsoft.Search/searchServices|OperationLogs|操作ログ|
|Microsoft.ServerManagement/nodes|RequestLogs|要求ログ|
|Microsoft.ServiceBus/namespaces|OperationalLogs|操作ログ|
|Microsoft.StreamAnalytics/streamingjobs|実行|実行|
|Microsoft.StreamAnalytics/streamingjobs|作成|作成|

## <a name="next-steps"></a>次のステップ

* [診断ログを **Event Hubs** にストリーミングする](monitoring-stream-diagnostic-logs-to-event-hubs.md)
* [Azure Monitor REST API を使用して診断設定を変更する](https://msdn.microsoft.com/library/azure/dn931931.aspx)
* [Log Analytics を使用した、Azure ストレージからのログの分析](../log-analytics/log-analytics-azure-storage.md)

