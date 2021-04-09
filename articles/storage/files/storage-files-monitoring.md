---
title: Azure Files の監視 | Microsoft Docs
description: Azure Files のパフォーマンスと可用性を監視する方法について説明します。 Azure Files のデータを監視します。構成、およびメトリックとログ データの分析について説明します。
author: normesta
services: storage
ms.service: storage
ms.subservice: files
ms.topic: conceptual
ms.date: 3/02/2021
ms.author: normesta
ms.reviewer: fryu
ms.custom: monitoring, devx-track-csharp, devx-track-azurecli
ms.openlocfilehash: 16a899b7f0e40c2eee91d1dd445f0992572a9dda
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "103418166"
---
# <a name="monitoring-azure-files"></a>Azure Files の監視

Azure リソースに依存するクリティカルなアプリケーションとビジネス プロセスがある場合は、それらのリソースの可用性、パフォーマンス、操作を監視する必要があります。 この記事では、Azure Files によって生成される監視データと、Azure Monitor の機能を使用してこのデータに関するアラートを分析する方法について説明します。

## <a name="monitor-overview"></a>監視の概要

Azure portal で、各 Azure Files リソースの **[概要]** ページには、リソースの要求や時間ごとの課金など、リソースの使用状況の要約が表示されます。 この情報は役立ちますが、使用できる監視データはごくわずかです。 このデータの一部は自動的に収集され、リソースを作成するとすぐに分析に使用できるようになります。 一定の構成によって追加の種類のデータ収集を有効にすることができます。

## <a name="what-is-azure-monitor"></a>Azure Monitor とは
Azure Files は、Azure のフルスタック監視サービスである [Azure Monitor](../../azure-monitor/overview.md) を使用して監視データを作成します。 Azure Monitor には、Azure リソースと、他のクラウドおよびオンプレミスのリソースを監視するための完全な機能セットが用意されています。 

まず「[Azure Monitor を使用した Azure リソースの監視](../../azure-monitor/essentials/monitor-azure-resource.md)」の記事にある次の事項の説明をお読みください。

- Azure Monitor とは
- 監視に関連するコスト
- Azure で収集される監視データ
- データ収集の構成
- 監視データの分析とアラート生成のための Azure の標準ツール

以下のセクションでは、この記事に基づき、Azure Files から収集された特定のデータについて説明します。 例では、Azure ツールを使用してデータ コレクションを構成し、このデータを分析する方法について説明します。

## <a name="monitoring-data"></a>データの監視

Azure Files では、他の Azure リソースと同じ種類の監視データが収集されます。これについては、[Azure リソースの監視データ](../../azure-monitor/essentials/monitor-azure-resource.md#monitoring-data)に関するページをご覧ください。 

Azure Files によって作成されるメトリックとログの詳細については、「[Azure Files 監視データのリファレンス](storage-files-monitoring-reference.md)」を参照してください。

Azure Monitor のメトリックとログでは、Azure Resource Manager ストレージ アカウントのみがサポートされています。 Azure Monitor では、従来のストレージ アカウントはサポートされていません。 従来のストレージ アカウントでメトリックまたはログを使用する場合、Azure Resource Manager ストレージ アカウントに移行する必要があります。 [Azure Resource Manager への移行](../../virtual-machines/migration-classic-resource-manager-overview.md)に関するページを参照してください。

## <a name="collection-and-routing"></a>収集とルーティング

プラットフォーム メトリックとアクティビティ ログは自動的に収集されますが、診断設定を使用して他の場所にルーティングすることができます。 

リソース ログを収集するには、診断設定を作成する必要があります。 設定を作成するときに、ログを有効にするストレージの種類として **[ファイル]** を選択します。 次に、ログ収集の対象とする操作のカテゴリを次のいずれかから指定します。 

| カテゴリ | 説明 |
|:---|:---|
| StorageRead | オブジェクトに対する読み取り操作。 |
| StorageWrite | オブジェクトに対する書き込み操作。 |
| StorageDelete | オブジェクトに対する削除操作。 |

ログに記録された SMB と REST の各操作の一覧を取得するには、[Storage によって記録される操作やステータス メッセージ](/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages)に関するページと「[Azure Files 監視データのリファレンス](storage-files-monitoring-reference.md)」を参照してください。

## <a name="creating-a-diagnostic-setting"></a>診断設定の作成

診断設定を作成するには、Azure portal、PowerShell、Azure CLI、または Azure Resource Manager テンプレートを使用します。

> [!NOTE]
> Azure Monitor の Azure Storage ログはパブリック プレビュー段階にあり、すべてのパブリック クラウド リージョンでプレビュー テスト用に使用できます。 このプレビューでは、BLOB (Azure Data Lake Storage Gen2 を含む)、ファイル、キュー、およびテーブルに対してログが有効になります。 この機能は、Azure Resource Manager デプロイ モデルを使用して作成されたすべてのストレージ アカウントで使用できます。 「[ストレージ アカウントの概要](../common/storage-account-overview.md)」を参照してください。

一般的なガイダンスについては、[Azure でプラットフォーム ログとメトリックを収集するための診断設定の作成](../../azure-monitor/essentials/diagnostic-settings.md)に関するページを参照してください。

### <a name="azure-portal"></a>[Azure Portal](#tab/azure-portal)

1. Azure portal にサインインします。

2. ストレージ アカウントに移動します。

3. **[監視]** セクションで、 **[診断設定 (プレビュー)]** をクリックします。

   > [!div class="mx-imgBorder"]
   > ![ポータル - 診断ログ](media/storage-files-monitoring/diagnostic-logs-settings-pane.png)   

4. ログを有効にするストレージの種類として **[ファイル]** を選択します。

5. **[診断設定の追加]** をクリックします。

   > [!div class="mx-imgBorder"]
   > ![ポータル - リソース ログ - 診断設定の追加](media/storage-files-monitoring/diagnostic-logs-settings-pane-2.png)

   **[診断設定]** ページが表示されます。

   > [!div class="mx-imgBorder"]
   > ![リソース ログ ページ](media/storage-files-monitoring/diagnostic-logs-page.png)

6. このページの **名前** のフィールドに、このリソース ログ設定の名前を入力します。 次に、ログする操作 (読み取り、書き込み、削除の各操作) と、ログの送信先を選択します。

#### <a name="archive-logs-to-a-storage-account"></a>ログをストレージ アカウントにアーカイブする

ログをストレージ アカウントにアーカイブすることを選択した場合は、ストレージ アカウントに送信されるログの量に対して課金されます。 具体的な料金については、「[Azure Monitor の価格](https://azure.microsoft.com/pricing/details/monitor/#platform-logs)」ページの「**プラットフォーム ログ**」セクションを参照してください。

1. **[ストレージ アカウントへのアーカイブ]** チェック ボックスをオンにし、 **[構成]** ボタンをクリックします。

   > [!div class="mx-imgBorder"]   
   > ![[診断設定] ページのアーカイブ ストレージ](media/storage-files-monitoring/diagnostic-logs-settings-pane-archive-storage.png)

2. **[ストレージ アカウント]** ドロップダウン リストで、ログのアーカイブ先となるストレージ アカウントを選択し、 **[OK]** ボタンをクリックして、 **[保存]** ボタンをクリックします。

   [!INCLUDE [no retention policy](../../../includes/azure-storage-logs-retention-policy.md)]

   > [!NOTE]
   > ストレージ アカウントをエクスポート先として選択する前に、[Azure リソース ログのアーカイブ](../../azure-monitor/essentials/resource-logs.md#send-to-azure-storage)に関するページを参照して、ストレージ アカウントに関する前提条件をご確認ください。

#### <a name="stream-logs-to-azure-event-hubs"></a>ログを Azure Event Hubs にストリーミングする

ログをイベント ハブにストリーミングすることを選択した場合は、イベント ハブに送信されるログの量に対して課金されます。 具体的な料金については、「[Azure Monitor の価格](https://azure.microsoft.com/pricing/details/monitor/#platform-logs)」ページの「**プラットフォーム ログ**」セクションを参照してください。

1. **[Stream to an event hub]\(イベント ハブへのストリーム\)** チェックボックスをオンにし、 **[構成]** ボタンをクリックします。

2. **[イベント ハブを選択してください]** ペインで、ログのストリーミング先とするイベン ト ハブの名前空間、名前、およびポリシー名を選択します。 

   > [!div class="mx-imgBorder"]
   > ![[診断設定] ページのイベント ハブ](media/storage-files-monitoring/diagnostic-logs-settings-pane-event-hub.png)

3. **[OK]** ボタンをクリックしてから、 **[保存]** ボタンをクリックします。

#### <a name="send-logs-to-azure-log-analytics"></a>Azure Log Analytics にログを送信する

1. **[Send to Log Analytics]\(Log Analytics への送信\)** チェックボックスを オンにして Log Analytics ワークスペースを選択し、 **[保存]** ボタンをクリックします。

   > [!div class="mx-imgBorder"]   
   > ![[診断設定] ページの Log Analytics](media/storage-files-monitoring/diagnostic-logs-settings-pane-log-analytics.png)

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

1. Windows PowerShell コマンド ウィンドウを開き、`Connect-AzAccount` コマンドを使用して Azure サブスクリプションにサインインします。 その後、画面の指示に従います。

   ```powershell
   Connect-AzAccount
   ```

2. アクティブなサブスクリプションを、ログを有効にするストレージ アカウントのサブスクリプションに設定します。

   ```powershell
   Set-AzContext -SubscriptionId <subscription-id>
   ```

#### <a name="archive-logs-to-a-storage-account"></a>ログをストレージ アカウントにアーカイブする

ログをストレージ アカウントにアーカイブすることを選択した場合は、ストレージ アカウントに送信されるログの量に対して課金されます。 具体的な料金については、「[Azure Monitor の価格](https://azure.microsoft.com/pricing/details/monitor/#platform-logs)」ページの「**プラットフォーム ログ**」セクションを参照してください。

`StorageAccountId` パラメーターを指定した [Set-AzDiagnosticSetting](/powershell/module/az.monitor/set-azdiagnosticsetting) PowerShell コマンドレットを使用して、ログを有効にします。

```powershell
Set-AzDiagnosticSetting -ResourceId <storage-service-resource-id> -StorageAccountId <storage-account-resource-id> -Enabled $true -Category <operations-to-log> 
```

このスニペットの `<storage-service-resource--id>` プレースホルダーを Azure File Service のリソース ID に置き換えます。 ストレージ アカウントの **[プロパティ]** ページを開くと、Azure portal 上でリソース ID を確認できます。

**Category** パラメーターの値には、`StorageRead`、`StorageWrite`、および `StorageDelete` を使用できます。

[!INCLUDE [no retention policy](../../../includes/azure-storage-logs-retention-policy.md)]

次に例を示します。

`Set-AzDiagnosticSetting -ResourceId /subscriptions/208841be-a4v3-4234-9450-08b90c09f4/resourceGroups/myresourcegroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount/fileServices/default -StorageAccountId /subscriptions/208841be-a4v3-4234-9450-08b90c09f4/resourceGroups/myresourcegroup/providers/Microsoft.Storage/storageAccounts/myloggingstorageaccount -Enabled $true -Category StorageWrite,StorageDelete`

各パラメーターの説明については、[Azure PowerShell を使用した Azure リソース ログのアーカイブ](../../azure-monitor/essentials/resource-logs.md#send-to-azure-storage)に関するページを参照してください。

#### <a name="stream-logs-to-an-event-hub"></a>イベント ハブにログをストリーム配信する

ログをイベント ハブにストリーミングすることを選択した場合は、イベント ハブに送信されるログの量に対して課金されます。 具体的な料金については、「[Azure Monitor の価格](https://azure.microsoft.com/pricing/details/monitor/#platform-logs)」ページの「**プラットフォーム ログ**」セクションを参照してください。

`EventHubAuthorizationRuleId` パラメーターを指定した [Set-AzDiagnosticSetting](/powershell/module/az.monitor/set-azdiagnosticsetting) PowerShell コマンドレットを使用して、ログを有効にします。

```powershell
Set-AzDiagnosticSetting -ResourceId <storage-service-resource-id> -EventHubAuthorizationRuleId <event-hub-namespace-and-key-name> -Enabled $true -Category <operations-to-log> -RetentionEnabled <retention-bool> -RetentionInDays <number-of-days>
```

次に例を示します。

`Set-AzDiagnosticSetting -ResourceId /subscriptions/208841be-a4v3-4234-9450-08b90c09f4/resourceGroups/myresourcegroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount/fileServices/default -EventHubAuthorizationRuleId /subscriptions/20884142-a14v3-4234-5450-08b10c09f4/resourceGroups/myresourcegroup/providers/Microsoft.EventHub/namespaces/myeventhubnamespace/authorizationrules/RootManageSharedAccessKey -Enabled $true -Category StorageDelete`

各パラメーターの説明については、[PowerShell コマンドレットを使用した Event Hubs へのデータのストリーム配信](../../azure-monitor/essentials/resource-logs.md#send-to-azure-event-hubs)に関するページを参照してください。

#### <a name="send-logs-to-log-analytics"></a>ログを Log Analytics に送信する

`WorkspaceId` パラメーターを指定した [Set-AzDiagnosticSetting](/powershell/module/az.monitor/set-azdiagnosticsetting) PowerShell コマンドレットを使用して、ログを有効にします。

```powershell
Set-AzDiagnosticSetting -ResourceId <storage-service-resource-id> -WorkspaceId <log-analytics-workspace-resource-id> -Enabled $true -Category <operations-to-log> -RetentionEnabled <retention-bool> -RetentionInDays <number-of-days>
```

次に例を示します。

`Set-AzDiagnosticSetting -ResourceId /subscriptions/208841be-a4v3-4234-9450-08b90c09f4/resourceGroups/myresourcegroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount/fileServices/default -WorkspaceId /subscriptions/208841be-a4v3-4234-9450-08b90c09f4/resourceGroups/myresourcegroup/providers/Microsoft.OperationalInsights/workspaces/my-analytic-workspace -Enabled $true -Category StorageDelete`

詳しくは、[Azure Monitor の Log Analytics ワークスペースへの Azure リソース ログのストリーム配信](../../azure-monitor/essentials/resource-logs.md#send-to-log-analytics-workspace)に関するページを参照してください。

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

1. まず、[Azure Cloud Shell](../../cloud-shell/overview.md) を開きます。または、Azure CLI をローカルに[インストール](/cli/azure/install-azure-cli)した場合は、Windows PowerShell などのコマンド コンソール アプリケーションを開きます。

2. 自分の ID が複数のサブスクリプションに関連付けられている場合は、アクティブなサブスクリプションを、ログを有効にするストレージ アカウントのサブスクリプションに設定します。

   ```azurecli-interactive
   az account set --subscription <subscription-id>
   ```

   `<subscription-id>` プレースホルダーの値をサブスクリプションの ID に置き換えます。

#### <a name="archive-logs-to-a-storage-account"></a>ログをストレージ アカウントにアーカイブする

ログをストレージ アカウントにアーカイブすることを選択した場合は、ストレージ アカウントに送信されるログの量に対して課金されます。 具体的な料金については、「[Azure Monitor の価格](https://azure.microsoft.com/pricing/details/monitor/#platform-logs)」ページの「**プラットフォーム ログ**」セクションを参照してください。

[az monitor diagnostic-settings create](/cli/azure/monitor/diagnostic-settings#az-monitor-diagnostic-settings-create) コマンドを使用してログを有効にします。

```azurecli-interactive
az monitor diagnostic-settings create --name <setting-name> --storage-account <storage-account-name> --resource <storage-service-resource-id> --resource-group <resource-group> --logs '[{"category": <operations>, "enabled": true}]'
```

このスニペットの `<storage-service-resource--id>` プレースホルダーを BLOB ストレージ サービスのリソース ID に置き換えます。 ストレージ アカウントの **[プロパティ]** ページを開くと、Azure portal 上でリソース ID を確認できます。

**category** パラメーターの値には、`StorageRead`、`StorageWrite`、および `StorageDelete` を使用できます。

[!INCLUDE [no retention policy](../../../includes/azure-storage-logs-retention-policy.md)]

次に例を示します。

`az monitor diagnostic-settings create --name setting1 --storage-account mystorageaccount --resource /subscriptions/938841be-a40c-4bf4-9210-08bcf06c09f9/resourceGroups/myresourcegroup/providers/Microsoft.Storage/storageAccounts/myloggingstorageaccount/fileServices/default --resource-group myresourcegroup --logs '[{"category": StorageWrite, "enabled": true}]'`

各パラメーターの説明については、[Azure CLI を使用した Azure リソース ログのアーカイブ](../../azure-monitor/essentials/resource-logs.md#send-to-azure-storage)に関するページを参照してください。

#### <a name="stream-logs-to-an-event-hub"></a>イベント ハブにログをストリーム配信する

ログをイベント ハブにストリーミングすることを選択した場合は、イベント ハブに送信されるログの量に対して課金されます。 具体的な料金については、「[Azure Monitor の価格](https://azure.microsoft.com/pricing/details/monitor/#platform-logs)」ページの「**プラットフォーム ログ**」セクションを参照してください。

[az monitor diagnostic-settings create](/cli/azure/monitor/diagnostic-settings#az-monitor-diagnostic-settings-create) コマンドを使用してログを有効にします。

```azurecli-interactive
az monitor diagnostic-settings create --name <setting-name> --event-hub <event-hub-name> --event-hub-rule <event-hub-namespace-and-key-name> --resource <storage-account-resource-id> --logs '[{"category": <operations>, "enabled": true "retentionPolicy": {"days": <number-days>, "enabled": <retention-bool}}]'
```

次に例を示します。

`az monitor diagnostic-settings create --name setting1 --event-hub myeventhub --event-hub-rule /subscriptions/938841be-a40c-4bf4-9210-08bcf06c09f9/resourceGroups/myresourcegroup/providers/Microsoft.EventHub/namespaces/myeventhubnamespace/authorizationrules/RootManageSharedAccessKey --resource /subscriptions/938841be-a40c-4bf4-9210-08bcf06c09f9/resourceGroups/myresourcegroup/providers/Microsoft.Storage/storageAccounts/myloggingstorageaccount/fileServices/default --logs '[{"category": StorageDelete, "enabled": true }]'`

各パラメーターの説明については、[Azure CLI を使用した Event Hubs へのデータのストリーム配信](../../azure-monitor/essentials/resource-logs.md#send-to-azure-event-hubs)に関するページを参照してください。

#### <a name="send-logs-to-log-analytics"></a>ログを Log Analytics に送信する

[az monitor diagnostic-settings create](/cli/azure/monitor/diagnostic-settings#az-monitor-diagnostic-settings-create) コマンドを使用してログを有効にします。

```azurecli-interactive
az monitor diagnostic-settings create --name <setting-name> --workspace <log-analytics-workspace-resource-id> --resource <storage-account-resource-id> --logs '[{"category": <category name>, "enabled": true "retentionPolicy": {"days": <days>, "enabled": <retention-bool}}]'
```

次に例を示します。

`az monitor diagnostic-settings create --name setting1 --workspace /subscriptions/208841be-a4v3-4234-9450-08b90c09f4/resourceGroups/myresourcegroup/providers/Microsoft.OperationalInsights/workspaces/my-analytic-workspace --resource /subscriptions/938841be-a40c-4bf4-9210-08bcf06c09f9/resourceGroups/myresourcegroup/providers/Microsoft.Storage/storageAccounts/myloggingstorageaccount/fileServices/default --logs '[{"category": StorageDelete, "enabled": true ]'`

 詳しくは、[Azure Monitor の Log Analytics ワークスペースへの Azure リソース ログのストリーム配信](../../azure-monitor/essentials/resource-logs.md#send-to-log-analytics-workspace)に関するページを参照してください。

### <a name="template"></a>[テンプレート](#tab/template)

診断設定を作成する Azure Resource Manager テンプレートを表示するには、「[Azure Storage の診断設定](../../azure-monitor/essentials/resource-manager-diagnostic-settings.md#diagnostic-setting-for-azure-storage)」を参照してください。

---

## <a name="analyzing-metrics"></a>メトリックの分析

メトリックス エクスプローラーを使用して、他の Azure サービスのメトリックと共に Azure Storage のメトリックを分析できます。 メトリックス エクスプローラーを開くには、 **[Azure Monitor]** メニューの **[メトリック]** を選択します。 このツールの使用方法の詳細については、「[Azure メトリックス エクスプローラーの概要](../../azure-monitor/essentials/metrics-getting-started.md)」をご覧ください。 

ディメンションをサポートするメトリックについては、目的のディメンション値でメトリックをフィルター処理できます。  Azure Storage でサポートされるディメンションの完全な一覧については、「[メトリックのディメンション](storage-files-monitoring-reference.md#metrics-dimensions)」をご覧ください。 Azure Files のメトリックは、こちらの名前空間にあります。 

- Microsoft.Storage/storageAccounts
- Microsoft.Storage/storageAccounts/fileServices

(Azure Files を含む) すべての Azure Monitor サポート メトリックの一覧については、「[Azure Monitor のサポートされるメトリック](../../azure-monitor/essentials/metrics-supported.md#microsoftstoragestorageaccountsfileservices)」をご覧ください。

### <a name="accessing-metrics"></a>メトリックにアクセスする

> [!TIP]
> Azure CLI または .NET の例を表示するには、ここに示した対応するタブを選択します。

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

#### <a name="list-the-metric-definition"></a>メトリック定義の一覧表示

ストレージ アカウントまたは Azure Files サービスのメトリック定義を一覧表示できます。 [Get-AzMetricDefinition](/powershell/module/az.monitor/get-azmetricdefinition) コマンドレットを使用します。

この例では、`<resource-ID>` プレースホルダーをストレージ アカウント全体のリソース ID または Azure Files サービスのリソース ID に置き換えます。  これらのリソース ID は、Azure portal 上のストレージ アカウントの **[プロパティ]** ページで確認できます。

```powershell
   $resourceId = "<resource-ID>"
   Get-AzMetricDefinition -ResourceId $resourceId
```

#### <a name="reading-metric-values"></a>メトリック値を読み取る

ストレージ アカウントまたは Azure Files サービスのアカウント レベルのメトリック値を読み取ることができます。 [Get-AzMetric](/powershell/module/Az.Monitor/Get-AzMetric) コマンドレットを使用します。

```powershell
   $resourceId = "<resource-ID>"
   Get-AzMetric -ResourceId $resourceId -MetricNames "UsedCapacity" -TimeGrain 01:00:00
```

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

#### <a name="list-the-account-level-metric-definition"></a>アカウント レベルのメトリック定義を一覧表示する

ストレージ アカウントまたは Azure Files サービスのメトリック定義を一覧表示できます。 [az monitor metrics list-definitions](/cli/azure/monitor/metrics#az-monitor-metrics-list-definitions) コマンドを使用します。
 
この例では、`<resource-ID>` プレースホルダーをストレージ アカウント全体のリソース ID または Azure Files サービスのリソース ID に置き換えます。 これらのリソース ID は、Azure portal 上のストレージ アカウントの **[プロパティ]** ページで確認できます。

```azurecli-interactive
   az monitor metrics list-definitions --resource <resource-ID>
```

#### <a name="read-account-level-metric-values"></a>アカウント レベルのメトリック値を読み取る

ストレージ アカウントまたは Azure Files サービスのメトリック値を読み取ることができます。 [az monitor metrics list](/cli/azure/monitor/metrics#az-monitor-metrics-list) コマンドを使用します。

```azurecli-interactive
   az monitor metrics list --resource <resource-ID> --metric "UsedCapacity" --interval PT1H
```

### <a name="net"></a>[.NET](#tab/azure-portal)

Azure Monitor には、メトリックの定義と値を読み取るための [.NET SDK](https://www.nuget.org/packages/Microsoft.Azure.Management.Monitor/) が用意されています。 [サンプル コード](https://azure.microsoft.com/resources/samples/monitor-dotnet-metrics-api/)では、さまざまなパラメーターで SDK を使用する方法を示します。 ストレージ メトリックスについては `0.18.0-preview` 以降のバージョンを使用する必要があります。
 
この例では、`<resource-ID>` プレースホルダーをストレージ アカウント全体または Azure Files サービスのリソース ID に置き換えます。 これらのリソース ID は、Azure portal 上のストレージ アカウントの **[プロパティ]** ページで確認できます。

`<subscription-ID>` 変数をご自身のサブスクリプションの ID に置き換えます。 `<tenant-ID>`、`<application-ID>`、および `<AccessKey>` の値を取得する方法のガイダンスについては、「[リソースにアクセスできる Azure AD アプリケーションとサービス プリンシパルをポータルで作成する](../../active-directory/develop/howto-create-service-principal-portal.md)」をご覧ください。 

#### <a name="list-the-account-level-metric-definition"></a>アカウント レベルのメトリック定義を一覧表示する

次の例は、アカウント レベルでメトリック定義を一覧表示する方法を示しています。

```csharp
    public static async Task ListStorageMetricDefinition()
    {
        var resourceId = "<resource-ID>";
        var subscriptionId = "<subscription-ID>";
        var tenantId = "<tenant-ID>";
        var applicationId = "<application-ID>";
        var accessKey = "<AccessKey>";


        MonitorManagementClient readOnlyClient = AuthenticateWithReadOnlyClient(tenantId, applicationId, accessKey, subscriptionId).Result;
        IEnumerable<MetricDefinition> metricDefinitions = await readOnlyClient.MetricDefinitions.ListAsync(resourceUri: resourceId, cancellationToken: new CancellationToken());

        foreach (var metricDefinition in metricDefinitions)
        {
            // Enumrate metric definition:
            //    Id
            //    ResourceId
            //    Name
            //    Unit
            //    MetricAvailabilities
            //    PrimaryAggregationType
            //    Dimensions
            //    IsDimensionRequired
        }
    }

```

#### <a name="reading-account-level-metric-values"></a>アカウント レベルのメトリック値を読み取る

次の例は、アカウント レベルで `UsedCapacity` データを読み取る方法を示しています。

```csharp
    public static async Task ReadStorageMetricValue()
    {
        var resourceId = "<resource-ID>";
        var subscriptionId = "<subscription-ID>";
        var tenantId = "<tenant-ID>";
        var applicationId = "<application-ID>";
        var accessKey = "<AccessKey>";

        MonitorClient readOnlyClient = AuthenticateWithReadOnlyClient(tenantId, applicationId, accessKey, subscriptionId).Result;

        Microsoft.Azure.Management.Monitor.Models.Response Response;

        string startDate = DateTime.Now.AddHours(-3).ToUniversalTime().ToString("o");
        string endDate = DateTime.Now.ToUniversalTime().ToString("o");
        string timeSpan = startDate + "/" + endDate;

        Response = await readOnlyClient.Metrics.ListAsync(
            resourceUri: resourceId,
            timespan: timeSpan,
            interval: System.TimeSpan.FromHours(1),
            metricnames: "UsedCapacity",

            aggregation: "Average",
            resultType: ResultType.Data,
            cancellationToken: CancellationToken.None);

        foreach (var metric in Response.Value)
        {
            // Enumrate metric value
            //    Id
            //    Name
            //    Type
            //    Unit
            //    Timeseries
            //        - Data
            //        - Metadatavalues
        }
    }

```

#### <a name="reading-multidimensional-metric-values"></a>多次元メトリック値を読み取る

多次元メトリックの場合、特定のディメンション値に対するメトリック データを読み取る必要がある場合は、メタデータ フィルターを定義する必要があります。

次の例では、多次元をサポートするメトリックについてのメトリック データを読み取る方法を示します。

```csharp
    public static async Task ReadStorageMetricValueTest()
    {
        // Resource ID for Azure Files
        var resourceId = "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{storageAccountName}/fileServices/default";
        var subscriptionId = "<subscription-ID}";
        // How to identify Tenant ID, Application ID and Access Key: https://azure.microsoft.com/documentation/articles/resource-group-create-service-principal-portal/
        var tenantId = "<tenant-ID>";
        var applicationId = "<application-ID>";
        var accessKey = "<AccessKey>";

        MonitorManagementClient readOnlyClient = AuthenticateWithReadOnlyClient(tenantId, applicationId, accessKey, subscriptionId).Result;

        Microsoft.Azure.Management.Monitor.Models.Response Response;

        string startDate = DateTime.Now.AddHours(-3).ToUniversalTime().ToString("o");
        string endDate = DateTime.Now.ToUniversalTime().ToString("o");
        string timeSpan = startDate + "/" + endDate;
        // It's applicable to define meta data filter when a metric support dimension
        // More conditions can be added with the 'or' and 'and' operators, example: BlobType eq 'BlockBlob' or BlobType eq 'PageBlob'
        ODataQuery<MetadataValue> odataFilterMetrics = new ODataQuery<MetadataValue>(
            string.Format("BlobType eq '{0}'", "BlockBlob"));

        Response = readOnlyClient.Metrics.List(
                        resourceUri: resourceId,
                        timespan: timeSpan,
                        interval: System.TimeSpan.FromHours(1),
                        metricnames: "BlobCapacity",
                        odataQuery: odataFilterMetrics,
                        aggregation: "Average",
                        resultType: ResultType.Data);

        foreach (var metric in Response.Value)
        {
            //Enumrate metric value
            //    Id
            //    Name
            //    Type
            //    Unit
            //    Timeseries
            //        - Data
            //        - Metadatavalues
        }
    }

```

# <a name="template"></a>[テンプレート](#tab/template)

該当なし。

---

## <a name="analyzing-logs"></a>ログの分析

リソース ログには、ストレージ アカウント内の BLOB として、イベント データとして、またはログ分析クエリを使用してアクセスできます。

ログに記録された SMB と REST の各操作の一覧を取得するには、[Storage によって記録される操作やステータス メッセージ](/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages)に関するページと「[Azure Files 監視データのリファレンス](storage-files-monitoring-reference.md)」を参照してください。

> [!NOTE]
> Azure Monitor の Azure Storage ログはパブリック プレビュー段階にあり、すべてのパブリック クラウド リージョンでプレビュー テスト用に使用できます。 このプレビューでは、汎用 v1 と汎用 v2 ストレージ アカウントの BLOB (Azure Data Lake Storage Gen2 を含む)、ファイル、キュー、テーブル、Premium ストレージ アカウントに対してログが有効になります。 クラシック ストレージ アカウントはサポートされていません。

ログ エントリが作成されるのは、サービス エンドポイントに対して行われた要求がある場合に限られます。 たとえば、ストレージ アカウントのファイル エンドポイントにはアクティビティが存在するが、テーブルまたはキュー エンドポイントには存在しない場合、Azure File Service に関連したログだけが作成されます。 Azure Storage ログには、ストレージ サービスに対する要求の成功と失敗についての詳細な情報が含まれています。 この情報を使って個々の要求を監視したり、ストレージ サービスに関する問題を診断したりできます。 要求は、ベスト エフォートでログに記録されます。

### <a name="log-authenticated-requests"></a>認証済み要求をログに記録する

 次のタイプの認証済み要求が記録されます。

- 成功した要求
- 失敗した要求 (タイムアウト、スロットル、ネットワーク、承認などに関する各種エラー)
- Kerberos、NTLM、または Shared Access Signature (SAS) を使用した要求 (失敗した要求と成功した要求を含む)
- 分析データ ( **$logs** コンテナーの従来のログ データと、 **$metric** テーブルの従来のメトリック データ) に対する要求

Azure Files サービスそのものによる要求 (ログの作成や削除など) は記録されません。 ログに記録された SMB と REST の各要求の完全な一覧については、[Storage によって記録される操作やステータス メッセージ](/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages)に関するページと「[Azure Files 監視データのリファレンス](storage-files-monitoring-reference.md)」を参照してください。

### <a name="accessing-logs-in-a-storage-account"></a>ストレージ アカウントのログにアクセスする

ログは、ターゲット ストレージ アカウントのコンテナーに格納された BLOB として表示されます。 データは、行区切りの JSON ペイロードとして収集され、1 つの BLOB 内に格納されます。 BLOB のこの名前は次の名前付け規則に従っています。

`https://<destination-storage-account>.blob.core.windows.net/insights-logs-<storage-operation>/resourceId=/subscriptions/<subscription-ID>/resourceGroups/<resource-group-name>/providers/Microsoft.Storage/storageAccounts/<source-storage-account>/fileServices/default/y=<year>/m=<month>/d=<day>/h=<hour>/m=<minute>/PT1H.json`

次に例を示します。

`https://mylogstorageaccount.blob.core.windows.net/insights-logs-storagewrite/resourceId=/subscriptions/`<br>`208841be-a4v3-4234-9450-08b90c09f4/resourceGroups/myresourcegroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount/fileServices/default/y=2019/m=07/d=30/h=23/m=12/PT1H.json`

### <a name="accessing-logs-in-an-event-hub"></a>イベント ハブのログにアクセスする

イベント ハブに送信されたログはファイルとして保存されませんが、イベント ハブでログ情報が受信されたことを確認することはできます。 Azure portal でイベント ハブに移動し、**受信メッセージ** の数がゼロより大きいことを確認します。 

![監査ログ](media/storage-files-monitoring/event-hub-log.png)

イベント ハブに送信されるログ データのアクセスと読み取りには、セキュリティ情報とイベント管理のツールと監視ツールを使用します。 詳細については、[イベント ハブに送信された監視データを処理する方法](../../azure-monitor/essentials/stream-monitoring-data-event-hubs.md)に関するページを参照してください。

### <a name="accessing-logs-in-a-log-analytics-workspace"></a>Log Analytics ワークスペースのログにアクセスする

Azure Monitor ログ クエリを使用して、Log Analytics ワークスペースに送信されたログにアクセスできます。 データは **StorageFileLogs** テーブルに格納されます。 

詳細については、「[Log Analytics のチュートリアル](../../azure-monitor/logs/log-analytics-tutorial.md)」を参照してください。

#### <a name="sample-kusto-queries"></a>サンプル Kusto クエリ

**[ログ検索]** バーに入力して Azure Files の監視に利用できるクエリを紹介します。 これらのクエリは[新しい言語](../../azure-monitor/logs/log-query-overview.md)で使用できます。

> [!IMPORTANT]
> ストレージ アカウント リソース グループ メニューから **[ログ]** を選択すると、クエリのスコープが現在のリソース グループに設定された状態で Log Analytics が開きます。 つまり、ログ クエリには、そのリソース グループのデータのみが含まれます。 他のリソースのデータや他の Azure サービスのデータを含むクエリを実行する場合は、**Azure Monitor** のメニューから **[ログ]** を選択します。 詳細については、「[Azure Monitor Log Analytics のログ クエリのスコープと時間範囲](../../azure-monitor/logs/scope.md)」を参照してください。

これらのクエリを使用すると、Azure ファイル共有の監視に役立ちます。

- 先週の SMB エラーを表示する

```Kusto
StorageFileLogs
| where Protocol == "SMB" and TimeGenerated >= ago(7d) and StatusCode contains "-"
| sort by StatusCode
```
- 先週の SMB 操作の円グラフを作成する

```Kusto
StorageFileLogs
| where Protocol == "SMB" and TimeGenerated >= ago(7d) 
| summarize count() by OperationName
| sort by count_ desc
| render piechart
```

- 先週の REST エラーを表示する

```Kusto
StorageFileLogs
| where Protocol == "HTTPS" and TimeGenerated >= ago(7d) and StatusText !contains "Success"
| sort by StatusText asc
```

- 先週の REST 操作の円グラフを作成する

```Kusto
StorageFileLogs
| where Protocol == "HTTPS" and TimeGenerated >= ago(7d) 
| summarize count() by OperationName
| sort by count_ desc
| render piechart
```

Azure Files の列名と説明の一覧については、「[StorageFileLogs](/azure/azure-monitor/reference/tables/storagefilelogs)」を参照してください。

クエリを記述する方法の詳細については、「[Log Analytics のチュートリアル](../../azure-monitor/logs/log-analytics-tutorial.md)」を参照してください。

## <a name="alerts"></a>警告

Azure Monitor のアラートは、監視データで重要な状態が見つかると事前に通知します。 これにより、ユーザーが気付く前に、管理者が問題を識別して対処できます。 アラートは[メトリック](../../azure-monitor/alerts/alerts-metric-overview.md)、[ログ](../../azure-monitor/alerts/alerts-unified-log.md)、[アクティビティ ログ](../../azure-monitor/alerts/activity-log-alerts.md)に対して設定できます。 

次の表に、監視するシナリオの例とアラートに使用する適切なメトリックを示します。

| シナリオ | アラートに使用するメトリック |
|-|-|
| ファイル共有がスロットルされている。 | メトリック: トランザクション<br>ディメンション名:応答の種類 <br>ディメンション名:FileShare (Premium ファイル共有のみ) |
| ファイル共有のサイズが容量の 80% である。 | メトリック: File Capacity (ファイル容量)<br>ディメンション名:FileShare (Premium ファイル共有のみ) |
| ファイル共有のエグレスが 1 日に 500 GiB を超えた。 | メトリック: エグレス<br>ディメンション名:FileShare (Premium ファイル共有のみ) |

### <a name="how-to-create-alerts-for-azure-files"></a>Azure Files のアラートを作成する方法

1. **Azure portal** で目的の **ストレージ アカウント** に移動します。 

2. **[アラート]** をクリックし、次に **[+ 新しいアラート ルール]** をクリックします。

3. **[リソースの編集]** をクリックし、 **[ファイル リソースの種類]** を選択してから、 **[完了]** をクリックします。 

4. **[条件の追加]** をクリックし、アラートに関する次の情報を指定します。 

    - **メトリック**
    - **ディメンション名**
    - **アラート ロジック**

5. **[アクション グループの追加]** をクリックし、既存のアクション グループを選択するか、新しいアクション グループを作成して、アクション グループ (メール、SMS など) をアラートに追加します。

6. **[アラート ルール名]** 、 **[説明]** 、 **[重大度]** などの **[アラートの詳細]** を指定します。

7. **[アラート ルールの作成]** をクリックして、アラートを作成します。

> [!NOTE]  
> アラートを作成するときに雑音が多すぎる場合は、しきい値とアラート ロジックを調整します。

### <a name="how-to-create-an-alert-if-a-file-share-is-throttled"></a>ファイル共有がスロットルされた場合にアラートを作成する方法

1. **Azure portal** で目的の **ストレージ アカウント** に移動します。
2. **[監視]** セクションで **[アラート]** をクリックしてから、 **[+ 新しいアラート ルール]** をクリックします。
3. **[リソースの編集]** をクリックし、ストレージ アカウントの **ファイル リソースの種類** を選択してから、 **[完了]** をクリックします。 たとえば、ストレージ アカウント名が `contoso` である場合、`contoso/file` リソースを選択します。
4. **[条件の追加]** をクリックして条件を追加します。
5. ストレージ アカウントでサポートされているシグナルの一覧が表示されたら、 **[トランザクション]** メトリックを選択します。
6. **[シグナルロジックの構成]** ブレードで、 **[ディメンション名]** ドロップダウンをクリックし、 **[応答の種類]** を選択します。
7. **[ディメンション値]** ドロップダウンをクリックし、ファイル共有に適した応答の種類を選択します。

    Standard ファイル共有の場合、次の応答の種類を選択します。

    - SuccessWithShareIopsThrottling
    - SuccessWithThrottling
    - ClientShareIopsThrottlingError

    Premium ファイル共有の場合、次の応答の種類を選択します。

    - SuccessWithShareEgressThrottling
    - SuccessWithShareIngressThrottling
    - SuccessWithShareIopsThrottling
    - ClientShareEgressThrottlingError
    - ClientShareIngressThrottlingError
    - ClientShareIopsThrottlingError

   > [!NOTE]
   > 応答の種類が **[ディメンション値]** ドロップダウンに一覧表示されない場合は、リソースがスロットルされていないことを意味します。 ディメンション値を追加するには、 **[ディメンション値]** ドロップダウン リストの横にある **[カスタム値を追加]** を選択し、応答の種類 (**SuccessWithThrottling** など) を入力し、 **[OK]** を選択します。その後、これらの手順を繰り返して、自分のファイル共有に適用できるすべての応答の種類を追加します。

8. **Premium ファイル共有** の場合は、 **[ディメンション名]** ドロップダウンをクリックし、 **[ファイル共有]** を選択します。 **Standard ファイル共有** の場合は、**手順 #10** に進みます。

   > [!NOTE]
   > ファイル共有が Standard ファイル共有の場合、 **[ファイル共有]** ディメンションにはファイル共有が一覧に表示されません。これは、Standard ファイル共有では、共有ごとのメトリックを使用できないためです。 ストレージ アカウント内のいずれかのファイル共有がスロットルされている場合は、標準ファイル共有のスロットリング アラートがトリガーされ、どのファイル共有がスロットルされたかはアラートによって識別されません。 標準ファイル共有では共有ごとのメトリックを使用できないため、ストレージ アカウントごとに 1 つのファイル共有を使用することをお勧めします。

9. **[ディメンション値]** ドロップダウンをクリックして、アラートの対象とするファイル共有を選択します。
10. **アラート パラメーター** (しきい値、演算子、集計粒度、評価の頻度) を定義し、 **[完了]** をクリックします。

    > [!TIP]
    > 静的しきい値を使おうとしている場合は、ファイル共有が現在スロットルされているかどうかの妥当なしきい値を判断するのにメトリック グラフが役立つことがあります。 動的しきい値を使用している場合は、メトリック グラフに最新のデータに基づいて計算されたしきい値が表示されます。

11. **[Add action groups]\(アクション グループの追加\)** をクリックし、既存のアクション グループを選択するか、新しいアクション グループを作成して、**アクション グループ** (電子メール、SMS など) をアラートに追加します。
12. **[アラート ルール名]** 、 **[説明]** 、 **[重大度]** などの **[アラートの詳細]** を指定します。
13. **[アラート ルールの作成]** をクリックして、アラートを作成します。

### <a name="how-to-create-an-alert-if-the-azure-file-share-size-is-80-of-capacity"></a>Azure ファイル共有のサイズが容量の80% である場合にアラートを作成する方法

1. **Azure portal** で目的の **ストレージ アカウント** に移動します。
2. **[監視]** セクションで **[アラート]** をクリックしてから、 **[+ 新しいアラート ルール]** をクリックします。
3. **[リソースの編集]** をクリックし、ストレージ アカウントの **ファイル リソースの種類** を選択してから、 **[完了]** をクリックします。 たとえば、ストレージ アカウント名が `contoso` である場合、`contoso/file` リソースを選択します。
4. **[条件の追加]** をクリックして条件を追加します。
5. ストレージ アカウントでサポートされているシグナルの一覧が表示されたら、 **[ファイル容量]** メトリックを選択します。
6. **Premium ファイル共有** の場合は、 **[ディメンション名]** ドロップダウンをクリックし、 **[ファイル共有]** を選択します。 **Standard ファイル共有** の場合は、**手順 #8** に進みます。

   > [!NOTE]
   > ファイル共有が Standard ファイル共有の場合、 **[ファイル共有]** ディメンションにはファイル共有が一覧に表示されません。これは、Standard ファイル共有では、共有ごとのメトリックを使用できないためです。 Standard ファイル共有のアラートは、ストレージ アカウント内のすべてのファイル共有に基づいています。 標準ファイル共有では共有ごとのメトリックを使用できないため、ストレージ アカウントごとに 1 つのファイル共有を使用することをお勧めします。

7. **[ディメンション値]** ドロップダウンをクリックして、アラートの対象とするファイル共有を選択します。
8. **[しきい値]** をバイト単位で入力します。 たとえば、ファイル共有サイズが 100 TiB で、ファイル共有のサイズが容量の 80% である場合にアラートを受け取るには、しきい値 (バイト単位) は 87960930222080 です。
9. 残りの **アラート パラメーター** (集計粒度、評価の頻度) を定義し、 **[完了]** をクリックします。
10. **[アクション グループの追加]** をクリックし、既存のアクション グループを選択するか、新しいアクション グループを作成して、**アクション グループ** (メール、SMS など) をアラートに追加します。
11. **[アラート ルール名]** 、 **[説明]** 、 **[重大度]** などの **[アラートの詳細]** を指定します。
12. **[アラート ルールの作成]** をクリックして、アラートを作成します。

### <a name="how-to-create-an-alert-if-the-azure-file-share-egress-has-exceeded-500-gib-in-a-day"></a>Azure ファイル共有のエグレスが 1 日に 500 GiB を超えた場合にアラートを作成する方法

1. **Azure portal** で目的の **ストレージ アカウント** に移動します。
2. [監視] セクションで **[アラート]** をクリックしてから、 **[+ 新しいアラート ルール]** をクリックします。
3. **[リソースの編集]** をクリックし、ストレージ アカウントの **ファイル リソースの種類** を選択してから、 **[完了]** をクリックします。 たとえば、ストレージ アカウント名が contoso の場合は、contoso/ファイル リソースを選択します。
4. **[条件の追加]** をクリックして条件を追加します。
5. ストレージ アカウントでサポートされているシグナルの一覧が表示されたら、 **[エグレス]** メトリックを選択します。
6. **Premium ファイル共有** の場合は、 **[ディメンション名]** ドロップダウンをクリックし、 **[ファイル共有]** を選択します。 **Standard ファイル共有** の場合は、**手順 #8** に進みます。

   > [!NOTE]
   > ファイル共有が Standard ファイル共有の場合、 **[ファイル共有]** ディメンションにはファイル共有が一覧に表示されません。これは、Standard ファイル共有では、共有ごとのメトリックを使用できないためです。 Standard ファイル共有のアラートは、ストレージ アカウント内のすべてのファイル共有に基づいています。 標準ファイル共有では共有ごとのメトリックを使用できないため、ストレージ アカウントごとに 1 つのファイル共有を使用することをお勧めします。

7. **[ディメンション値]** ドロップダウンをクリックして、アラートの対象とするファイル共有を選択します。
8. しきい値に「**536870912000**」バイトと入力します。 
9. **[集計粒度]** ドロップダウンをクリックし、 **[24 時間]** を選択します。
10. **[評価の頻度]** を選択し、 **[完了]** クリックします。
11. **[アクション グループの追加]** をクリックし、既存のアクション グループを選択するか、新しいアクション グループを作成して、**アクション グループ** (メール、SMS など) をアラートに追加します。
12. **[アラート ルール名]** 、 **[説明]** 、 **[重大度]** などの **[アラートの詳細]** を指定します。
13. **[アラート ルールの作成]** をクリックして、アラートを作成します。

## <a name="next-steps"></a>次のステップ

- [Azure Files 監視データのリファレンス](storage-files-monitoring-reference.md)
- [Azure Monitor を使用した Azure リソースの監視](../../azure-monitor/essentials/monitor-azure-resource.md)
- [Azure Storage メトリックの移行](../common/storage-metrics-migration.md)
- [Azure Files のデプロイの計画](./storage-files-planning.md)
- [Azure Files のデプロイ方法](./storage-how-to-create-file-share.md)
- [Azure Files のトラブルシューティング - Windows](./storage-troubleshoot-windows-file-connection-problems.md)
- [Azure Files のトラブルシューティング - Linux](./storage-troubleshoot-linux-file-connection-problems.md)
