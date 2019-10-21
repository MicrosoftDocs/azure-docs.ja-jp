---
title: Azure でログとメトリックを収集するための診断設定を作成する | Microsoft Docs
description: Azure プラットフォーム ログを Azure Monitor ログ、Azure Storage、または Azure Event Hubs に転送するための診断設定を作成します。
author: bwren
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 07/31/2019
ms.author: bwren
ms.subservice: logs
ms.openlocfilehash: ec1842d534dcb1e9ddef149d3ae879677b29e715
ms.sourcegitcommit: 55f7fc8fe5f6d874d5e886cb014e2070f49f3b94
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/25/2019
ms.locfileid: "71263026"
---
# <a name="create-diagnostic-setting-to-collect-platform-logs-and-metrics-in-azure"></a>Azure でプラットフォーム ログとメトリックを収集するための診断設定を作成する
Azure の[プラットフォーム ログ](resource-logs-overview.md)では、Azure リソースとそれらが依存している Azure プラットフォームの詳細な診断情報と監査情報が提供されます。 この記事では、プラットフォーム ログを収集してさまざまな送信先に送信するための診断設定を作成して構成する方法について詳しく説明します。

各 Azure リソースには、独自の診断設定が必要です。 診断設定では、そのリソースに対して以下のことを定義します。

- 設定で定義されている送信先に送信されるログとメトリック データのカテゴリ。 使用できるカテゴリは、リソースの種類によって異なります。
- ログを送信する 1 つ以上の送信先。 現在の送信先には、Log Analytics ワークスペース、Event Hubs、Azure Storage が含まれます。
- Azure Storage の格納データのリテンション ポリシー。
 
1 つの診断設定で、各送信先を 1 つずつ定義できます。 特定の種類の複数の送信先 (たとえば、2 つの異なる Log Analytics ワークスペース) にデータを送信する場合は、複数の設定を作成します。 各リソースには、最大 5 つの診断設定を作成できます。

> [!NOTE]
> アクティビティ ログは、他のプラットフォーム ログと同じ送信先に転送できますが、診断設定では構成されません。 詳細については、「[Azure のプラットフォーム ログの概要](platform-logs-overview.md#destinations)」を参照してください。

> [!NOTE]
> [プラットフォーム メトリック](metrics-supported.md)は、[Azure Monitor のメトリック](data-platform-metrics.md)へと自動的に収集されます。 診断設定を使用すると、特定の Azure サービスのメトリックを Azure Monitor ログに収集し、[ログ クエリ](../log-query/log-query-overview.md)を使用して、他の監視データと組み合わせて分析することができます。

## <a name="destinations"></a>Destinations 
プラットフォーム ログは、次の表の送信先に送信できます。 各送信先の構成は、この記事で説明している診断設定を作成する場合と同じプロセスを使用して実行します。 その送信先にデータを送信する方法の詳細については、次の表の各リンクを参照してください。

| Destination | 説明 |
|:---|:---|
| [Log Analytics ワークスペース](resource-logs-collect-workspace.md) | Log Analytics ワークスペースにリソース ログを収集すると、強力なログ クエリを使用して、Azure Monitor で収集された他の監視データと組み合わせて分析できるほか、アラートや視覚化などの Azure Monitor の他の機能を活用することもできます。 |
| [Event Hubs](resource-logs-stream-event-hubs.md) | Event Hubs にログを送信すると、サードパーティ製の SIEM やその他のログ分析ソリューションなどの外部システムにデータをストリーミングできます。 |
| [Azure Storage アカウント](resource-logs-collect-storage.md) | Azure ストレージ アカウントにログをアーカイブすると、監査、スタティック分析、またはバックアップに役立ちます。 |




## <a name="create-diagnostic-settings-in-azure-portal"></a>Azure portal で診断設定を作成する
Azure portal では、Azure Monitor メニューから、またはリソースのメニューから診断設定を構成できます。

1. Azure portal の Azure Monitor メニューで、 **[設定]** の **[診断設定]** をクリックし、次にリソースをクリックします。

    ![診断設定](media/diagnostic-settings/menu-monitor.png)

    または、Azure portal のリソースのメニューで、 **[監視]** の **[診断設定]** をクリックします。

    ![診断設定](media/diagnostic-settings/menu-resource.png)

2. 選択したリソースの設定が存在しない場合は、設定を作成するように求められます。 **[診断を有効にする]** をクリックします。

   ![診断設定の追加 - 既存の設定が存在しない](media/diagnostic-settings/add-setting.png)

   リソースに既存の設定が存在する場合は、構成済みの設定の一覧が表示されます。 **[診断設定を追加する]** をクリックして新しい設定を追加するか、または **[設定の編集]** を選択して既存の設定を編集します。 各設定には、各送信先の種類を 1 つだけ含めることができます。

   ![診断設定の追加 - 既存の設定が存在する](media/diagnostic-settings/edit-setting.png)

3. 設定にまだ名前がない場合は、名前を付けます。
4. ログを送信する各送信先のチェック ボックスをオンにします。 **[構成]** をクリックし、次の表に示すようにそれらの設定を指定します。

    | Setting | 説明 |
    |:---|:---|
    | Log Analytics ワークスペース | ワークスペースの名前。 |
    | ストレージ アカウント | ストレージ アカウントの名前。 |
    | イベント ハブの名前空間 | イベント ハブが作成される名前空間 (ログを初めてストリーミングする場合)、またはストリーミング先の名前空間 (そのログ カテゴリをこの名前空間にストリーミングしているリソースが既に存在する場合)。
    | イベント ハブ名 | 必要に応じて、設定のすべてのデータを送信するイベント ハブの名前を指定します。 名前を指定しない場合は、ログ カテゴリごとにイベント ハブが作成されます。 複数のカテゴリを送信する場合は、名前を指定して、作成するイベント ハブの数を制限することができます。 詳細については、「[Azure Event Hubs のクォータと制限](../../event-hubs/event-hubs-quotas.md)」を参照してください。 |
    | イベント ハブ ポリシー名 | ストリーミング メカニズムのアクセス許可を定義します。 |

    ![診断設定の追加 - 既存の設定が存在する](media/diagnostic-settings/setting-details.png)

5. 指定した送信先に送信するデータの各カテゴリのチェック ボックスをオンにします。 **ストレージ アカウントへのアーカイブ**を行うオプションを選択した場合は、[リテンション期間](resource-logs-collect-storage.md#data-retention)を指定する必要もあります。



> [!NOTE]
> 診断設定を使用した多ディメンション メトリックの送信は現在サポートされていません。 ディメンションを含むメトリックは、ディメンション値間で集計され、フラット化された単一ディメンションのメトリックとしてエクスポートされます。
>
> *例*: イベント ハブの "受信メッセージ" メトリックは、キュー単位のレベルで調査およびグラフ化できます。 ただし、診断設定を使用してエクスポートすると、メトリックは、イベント ハブ内のすべてのキューのすべての受信メッセージとして表されます。

4. **[Save]** をクリックします。

しばらくすると、このリソースの設定一覧に新しい設定が表示され、新しいイベント データが生成されると、ログが指定の送信先にストリーミングされます。 イベントが生成されてから、それが [Log Analytics ワークスペースに表示される](data-ingestion-time.md)までに 15 分ほどかかる場合があるので注意してください。



## <a name="create-diagnostic-settings-using-powershell"></a>PowerShell を使用して診断設定を作成する
[Azure PowerShell](powershell-quickstart-samples.md) を使用して診断設定を作成するには、[Set-AzDiagnosticSetting](https://docs.microsoft.com/en-us/powershell/module/az.monitor/set-azdiagnosticsetting) コマンドレットを使用します。 パラメーターの説明については、このコマンドレットのドキュメントを参照してください。

3 つの送信先すべてを使用して診断設定を作成するための PowerShell コマンドレットの例を次に示します。


```powershell
Set-AzDiagnosticSetting -Name KeyVault-Diagnostics -ResourceId /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myresourcegroup/providers/Microsoft.KeyVault/vaults/mykeyvault -Category AuditEvent -MetricCategory AllMetrics -Enabled $true -StorageAccountId /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myresourcegroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount -RetentionEnabled $true -RetentionInDays 7 -WorkspaceId /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/oi-default-east-us/providers/microsoft.operationalinsights/workspaces/myworkspace  -EventHubAuthorizationRuleId /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myresourcegroup/providers/Microsoft.EventHub/namespaces/myeventhub/authorizationrules/RootManageSharedAccessKey
```


## <a name="create-diagnostic-settings-using-azure-cli"></a>Azure CLI を使用して診断設定を作成する
[Azure CLI](https://docs.microsoft.com/cli/azure/monitor?view=azure-cli-latest) を使用して診断設定を作成するには、[az monitor diagnostic-settings create](https://docs.microsoft.com/cli/azure/monitor/diagnostic-settings?view=azure-cli-latest#az-monitor-diagnostic-settings-create) コマンドを使用します。 パラメーターの説明については、このコマンドのドキュメントを参照してください。

3 つの送信先すべてを使用して診断設定を作成するための CLI コマンドの例を次に示します。



```azurecli
az monitor diagnostic-settings create  \
--name KeyVault-Diagnostics \
--resource /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myresourcegroup/providers/Microsoft.KeyVault/vaults/mykeyvault \
--logs    '[{"category": "AuditEvent","enabled": true,"retentionPolicy": {"days": 7,"enabled": true}}]' \
--metrics '[{"category": "AllMetrics","enabled": true,"retentionPolicy": {"days": 7,"enabled": true}}]' \
--storage-account /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myresourcegroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount \
--workspace /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/oi-default-east-us/providers/microsoft.operationalinsights/workspaces/myworkspace \
--event-hub-rule /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myresourcegroup/providers/Microsoft.EventHub/namespaces/myeventhub/authorizationrules/RootManageSharedAccessKey
```

### <a name="configure-diagnostic-settings-using-rest-api"></a>REST API を使用して診断設定を構成する
[Azure Monitor REST API](https://docs.microsoft.com/rest/api/monitor/) を使用して診断設定を作成または更新するには、「[診断設定](https://docs.microsoft.com/rest/api/monitor/diagnosticsettings)」を参照してください。


### <a name="configure-diagnostic-settings-using-resource-manager-template"></a>Resource Manager テンプレートを使用して診断設定を構成する
Resource Manager テンプレートを使用して診断設定を作成または更新するには、[Resource Manager テンプレートを使用してリソース作成時に自動的に診断設定を有効にする](diagnostic-settings-template.md)方法を参照してください。

## <a name="next-steps"></a>次の手順

* [Azure でのプラットフォーム ログの詳細について読む](resource-logs-overview.md)