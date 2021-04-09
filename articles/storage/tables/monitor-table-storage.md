---
title: Azure Table ストレージの監視 | Microsoft Docs
description: Azure Table ストレージのパフォーマンスと可用性を監視する方法について説明します。 Azure Table ストレージのデータを監視し、構成について説明します。また、メトリックとログ データを分析します。
author: normesta
services: storage
ms.service: storage
ms.topic: conceptual
ms.date: 10/26/2020
ms.author: normesta
ms.reviewer: fryu
ms.custom: monitoring, devx-track-csharp, devx-track-azurecli
ms.openlocfilehash: d2973d95cf797123e02fd48dc2dfab1b172e05f5
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102499470"
---
# <a name="monitoring-azure-table-storage"></a>Azure Table ストレージの監視

Azure リソースに依存するクリティカルなアプリケーションとビジネス プロセスがある場合は、それらのリソースの可用性、パフォーマンス、操作を監視する必要があります。 この記事では、Azure Table ストレージによって生成される監視データと、Azure Monitor の機能を使用してこのデータに関するアラートを分析する方法について説明します。

> [!NOTE]
> Azure Monitor の Azure Storage ログはパブリック プレビュー段階にあり、すべてのパブリック クラウド リージョンでプレビュー テスト用に使用できます。 このプレビューでは、BLOB (Azure Data Lake Storage Gen2 を含む)、ファイル、キュー、およびテーブルに対してログが有効になります。 この機能は、Azure Resource Manager デプロイ モデルを使用して作成されたすべてのストレージ アカウントで使用できます。 「[ストレージ アカウントの概要](../common/storage-account-overview.md)」を参照してください。

## <a name="monitor-overview"></a>監視の概要

各 Table ストレージ リソースについての Azure portal の **[概要]** ページには、要求や時間ごとの課金など、リソースの使用状況に関する簡単なビューが含まれています。 この情報は役立ちますが、使用できる監視データはごくわずかです。 このデータの一部は自動的に収集され、リソースを作成するとすぐに分析に使用できるようになります。 一定の構成によって追加の種類のデータ収集を有効にすることができます。

## <a name="what-is-azure-monitor"></a>Azure Monitor とは
Azure Table ストレージでは、Azure のフルスタック監視サービスである [Azure Monitor](../../azure-monitor/overview.md) を使用して監視データが作成されます。 Azure Monitor には、Azure リソースと、他のクラウドおよびオンプレミスのリソースを監視するための完全な機能セットが用意されています。 

まず「[Azure Monitor を使用した Azure リソースの監視](../../azure-monitor/essentials/monitor-azure-resource.md)」の記事にある次の事項の説明をお読みください。

- Azure Monitor とは
- 監視に関連するコスト
- Azure で収集される監視データ
- データ収集の構成
- 監視データの分析とアラート生成のための Azure の標準ツール

以下のセクションでは、この記事に基づき、Azure Storage から収集された特定のデータについて説明します。 例では、Azure ツールを使用してデータ コレクションを構成し、このデータを分析する方法について説明します。

## <a name="monitoring-data"></a>データの監視

Azure Table ストレージでは、他の Azure リソースと同じ種類の監視データが収集されます。これについては、[Azure リソースの監視データ](../../azure-monitor/essentials/monitor-azure-resource.md#monitoring-data)に関するページを参照してください。 

Azure Table ストレージによって作成されるメトリックとログの詳細については、「[Azure Table ストレージ監視データのリファレンス](monitor-table-storage-reference.md)」を参照してください。

Azure Monitor のメトリックとログでは、Azure Resource Manager ストレージ アカウントのみがサポートされています。 Azure Monitor では、従来のストレージ アカウントはサポートされていません。 従来のストレージ アカウントでメトリックまたはログを使用する場合、Azure Resource Manager ストレージ アカウントに移行する必要があります。 [Azure Resource Manager への移行](../../virtual-machines/migration-classic-resource-manager-overview.md)に関するページを参照してください。

必要に応じて、従来のメトリックとログの使用を続けることができます。 実際に、従来のメトリックとログを Azure Monitor のメトリックとログと並行して使用できます。 このサポートは、Azure Storage で従来のメトリックとログのサービスが終了するまで引き続き提供されます。

## <a name="collection-and-routing"></a>収集とルーティング

プラットフォーム メトリックとアクティビティ ログは自動的に収集されますが、診断設定を使用して他の場所にルーティングすることができます。 

リソース ログを収集するには、診断設定を作成する必要があります。 設定を作成するときに、ログを有効にするストレージの種類として **[テーブル]** を選択します。 次に、ログ収集の対象とする操作のカテゴリを次のいずれかから指定します。 

| カテゴリ | 説明 |
|:---|:---|
| StorageRead | オブジェクトに対する読み取り操作。 |
| StorageWrite | オブジェクトに対する書き込み操作。 |
| StorageDelete | オブジェクトに対する削除操作。 |

## <a name="creating-a-diagnostic-setting"></a>診断設定の作成

診断設定を作成するには、Azure portal、PowerShell、Azure CLI、または Azure Resource Manager テンプレートを使用します。 

一般的なガイダンスについては、[Azure でプラットフォーム ログとメトリックを収集するための診断設定の作成](../../azure-monitor/essentials/diagnostic-settings.md)に関するページを参照してください。

> [!NOTE]
> Azure Monitor の Azure Storage ログはパブリック プレビュー段階にあり、すべてのパブリック クラウド リージョンでプレビュー テスト用に使用できます。 このプレビューでは、BLOB (Azure Data Lake Storage Gen2 を含む)、ファイル、キュー、およびテーブルに対してログが有効になります。 この機能は、Azure Resource Manager デプロイ モデルを使用して作成されたすべてのストレージ アカウントで使用できます。 「[ストレージ アカウントの概要](../common/storage-account-overview.md)」を参照してください。

### <a name="azure-portal"></a>[Azure Portal](#tab/azure-portal)

1. Azure portal にサインインします。

2. ストレージ アカウントに移動します。

3. **[監視]** セクションで、 **[診断設定 (プレビュー)]** をクリックします。

   > [!div class="mx-imgBorder"]
   > ![ポータル - 診断ログ](media/monitor-table-storage/diagnostic-logs-settings-pane.png)   

4. ログを有効にするストレージの種類として **[テーブル]** を選択します。

5. **[診断設定の追加]** をクリックします。

   > [!div class="mx-imgBorder"]
   > ![ポータル - リソース ログ - 診断設定の追加](media/monitor-table-storage/diagnostic-logs-settings-pane-2.png)

   **[診断設定]** ページが表示されます。

   > [!div class="mx-imgBorder"]
   > ![リソース ログ ページ](media/monitor-table-storage/diagnostic-logs-page.png)

6. このページの **名前** のフィールドに、このリソース ログ設定の名前を入力します。 次に、ログする操作 (読み取り、書き込み、削除の各操作) と、ログの送信先を選択します。

#### <a name="archive-logs-to-a-storage-account"></a>ログをストレージ アカウントにアーカイブする

ログをストレージ アカウントにアーカイブすることを選択した場合は、ストレージ アカウントに送信されるログの量に対して課金されます。 具体的な料金については、「[Azure Monitor の価格](https://azure.microsoft.com/pricing/details/monitor/#platform-logs)」ページの「**プラットフォーム ログ**」セクションを参照してください。

1. **[ストレージ アカウントへのアーカイブ]** チェック ボックスをオンにし、 **[構成]** ボタンをクリックします。

   > [!div class="mx-imgBorder"]   
   > ![[診断設定] ページのアーカイブ ストレージ](media/monitor-table-storage/diagnostic-logs-settings-pane-archive-storage.png)

2. **[ストレージ アカウント]** ドロップダウン リストで、ログのアーカイブ先となるストレージ アカウントを選択し、 **[OK]** ボタンをクリックして、 **[保存]** ボタンをクリックします。

   [!INCLUDE [no retention policy](../../../includes/azure-storage-logs-retention-policy.md)]

   > [!NOTE]
   > ストレージ アカウントをエクスポート先として選択する前に、[Azure リソース ログのアーカイブ](../../azure-monitor/essentials/resource-logs.md#send-to-azure-storage)に関するページを参照して、ストレージ アカウントに関する前提条件をご確認ください。

#### <a name="stream-logs-to-azure-event-hubs"></a>ログを Azure Event Hubs にストリーミングする

ログをイベント ハブにストリーミングすることを選択した場合は、イベント ハブに送信されるログの量に対して課金されます。 具体的な料金については、「[Azure Monitor の価格](https://azure.microsoft.com/pricing/details/monitor/#platform-logs)」ページの「**プラットフォーム ログ**」セクションを参照してください。

1. **[Stream to an event hub]\(イベント ハブへのストリーム\)** チェックボックスをオンにし、 **[構成]** ボタンをクリックします。

2. **[イベント ハブを選択してください]** ペインで、ログのストリーミング先とするイベン ト ハブの名前空間、名前、およびポリシー名を選択します。 

   > [!div class="mx-imgBorder"]
   > ![[診断設定] ページのイベント ハブ](media/monitor-table-storage/diagnostic-logs-settings-pane-event-hub.png)

3. **[OK]** ボタンをクリックしてから、 **[保存]** ボタンをクリックします。

#### <a name="send-logs-to-azure-log-analytics"></a>Azure Log Analytics にログを送信する

1. **[Send to Log Analytics]\(Log Analytics への送信\)** チェックボックスを オンにして Log Analytics ワークスペースを選択し、 **[保存]** ボタンをクリックします。

   > [!div class="mx-imgBorder"]   
   > ![[診断設定] ページの Log Analytics](media/monitor-table-storage/diagnostic-logs-settings-pane-log-analytics.png)

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

このスニペットの `<storage-service-resource--id>` プレースホルダーをテーブル サービスのリソース ID に置き換えます。 ストレージ アカウントの **[プロパティ]** ページを開くと、Azure portal 上でリソース ID を確認できます。

**Category** パラメーターの値には、`StorageRead`、`StorageWrite`、および `StorageDelete` を使用できます。

[!INCLUDE [no retention policy](../../../includes/azure-storage-logs-retention-policy.md)]

次に例を示します。

`Set-AzDiagnosticSetting -ResourceId /subscriptions/208841be-a4v3-4234-9450-08b90c09f4/resourceGroups/myresourcegroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount/tableServices/default -StorageAccountId /subscriptions/208841be-a4v3-4234-9450-08b90c09f4/resourceGroups/myresourcegroup/providers/Microsoft.Storage/storageAccounts/myloggingstorageaccount -Enabled $true -Category StorageWrite,StorageDelete`

Azure Storage へのリソース ログのアーカイブの詳細については、[Azure リソース ログ](../../azure-monitor/essentials/resource-logs.md#send-to-azure-storage)に関するページを参照してください。

#### <a name="stream-logs-to-an-event-hub"></a>イベント ハブにログをストリーム配信する

ログをイベント ハブにストリーミングすることを選択した場合は、イベント ハブに送信されるログの量に対して課金されます。 具体的な料金については、「[Azure Monitor の価格](https://azure.microsoft.com/pricing/details/monitor/#platform-logs)」ページの「**プラットフォーム ログ**」セクションを参照してください。

`EventHubAuthorizationRuleId` パラメーターを指定した [Set-AzDiagnosticSetting](/powershell/module/az.monitor/set-azdiagnosticsetting) PowerShell コマンドレットを使用して、ログを有効にします。

```powershell
Set-AzDiagnosticSetting -ResourceId <storage-service-resource-id> -EventHubAuthorizationRuleId <event-hub-namespace-and-key-name> -Enabled $true -Category <operations-to-log> -RetentionEnabled <retention-bool> -RetentionInDays <number-of-days>
```

次に例を示します。

`Set-AzDiagnosticSetting -ResourceId /subscriptions/208841be-a4v3-4234-9450-08b90c09f4/resourceGroups/myresourcegroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount/tableServices/default -EventHubAuthorizationRuleId /subscriptions/20884142-a14v3-4234-5450-08b10c09f4/resourceGroups/myresourcegroup/providers/Microsoft.EventHub/namespaces/myeventhubnamespace/authorizationrules/RootManageSharedAccessKey -Enabled $true -Category StorageDelete`

イベント ハブへのリソース ログの送信の詳細については、[Azure リソース ログ](../../azure-monitor/essentials/resource-logs.md#send-to-azure-event-hubs)に関するページを参照してください。

#### <a name="send-logs-to-log-analytics"></a>ログを Log Analytics に送信する

`WorkspaceId` パラメーターを指定した [Set-AzDiagnosticSetting](/powershell/module/az.monitor/set-azdiagnosticsetting) PowerShell コマンドレットを使用して、ログを有効にします。

```powershell
Set-AzDiagnosticSetting -ResourceId <storage-service-resource-id> -WorkspaceId <log-analytics-workspace-resource-id> -Enabled $true -Category <operations-to-log> -RetentionEnabled <retention-bool> -RetentionInDays <number-of-days>
```

次に例を示します。

`Set-AzDiagnosticSetting -ResourceId /subscriptions/208841be-a4v3-4234-9450-08b90c09f4/resourceGroups/myresourcegroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount/tableServices/default -WorkspaceId /subscriptions/208841be-a4v3-4234-9450-08b90c09f4/resourceGroups/myresourcegroup/providers/Microsoft.OperationalInsights/workspaces/my-analytic-workspace -Enabled $true -Category StorageDelete`

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
az monitor diagnostic-settings create --name <setting-name> --storage-account <storage-account-name> --resource <storage-service-resource-id> --resource-group <resource-group> --logs '[{"category": <operations>, "enabled": true "retentionPolicy": {"days": <number-days>, "enabled": <retention-bool}}]'
```

このスニペットの `<storage-service-resource--id>` プレースホルダーをテーブル ストレージ サービスのリソース ID に置き換えます。 ストレージ アカウントの **[プロパティ]** ページを開くと、Azure portal 上でリソース ID を確認できます。

**category** パラメーターの値には、`StorageRead`、`StorageWrite`、および `StorageDelete` を使用できます。

[!INCLUDE [no retention policy](../../../includes/azure-storage-logs-retention-policy.md)]

次に例を示します。

`az monitor diagnostic-settings create --name setting1 --storage-account mystorageaccount --resource /subscriptions/938841be-a40c-4bf4-9210-08bcf06c09f9/resourceGroups/myresourcegroup/providers/Microsoft.Storage/storageAccounts/myloggingstorageaccount/tableServices/default --resource-group myresourcegroup --logs '[{"category": StorageWrite, "enabled": true}]'`

#### <a name="stream-logs-to-an-event-hub"></a>イベント ハブにログをストリーム配信する

ログをイベント ハブにストリーミングすることを選択した場合は、イベント ハブに送信されるログの量に対して課金されます。 具体的な料金については、「[Azure Monitor の価格](https://azure.microsoft.com/pricing/details/monitor/#platform-logs)」ページの「**プラットフォーム ログ**」セクションを参照してください。

[az monitor diagnostic-settings create](/cli/azure/monitor/diagnostic-settings#az-monitor-diagnostic-settings-create) コマンドを使用してログを有効にします。

```azurecli-interactive
az monitor diagnostic-settings create --name <setting-name> --event-hub <event-hub-name> --event-hub-rule <event-hub-namespace-and-key-name> --resource <storage-account-resource-id> --logs '[{"category": <operations>, "enabled": true "retentionPolicy": {"days": <number-days>, "enabled": <retention-bool}}]'
```

次に例を示します。

`az monitor diagnostic-settings create --name setting1 --event-hub myeventhub --event-hub-rule /subscriptions/938841be-a40c-4bf4-9210-08bcf06c09f9/resourceGroups/myresourcegroup/providers/Microsoft.EventHub/namespaces/myeventhubnamespace/authorizationrules/RootManageSharedAccessKey --resource /subscriptions/938841be-a40c-4bf4-9210-08bcf06c09f9/resourceGroups/myresourcegroup/providers/Microsoft.Storage/storageAccounts/myloggingstorageaccount/tableServices/default --logs '[{"category": StorageDelete, "enabled": true }]'`

#### <a name="send-logs-to-log-analytics"></a>ログを Log Analytics に送信する

[az monitor diagnostic-settings create](/cli/azure/monitor/diagnostic-settings#az-monitor-diagnostic-settings-create) コマンドを使用してログを有効にします。

```azurecli-interactive
az monitor diagnostic-settings create --name <setting-name> --workspace <log-analytics-workspace-resource-id> --resource <storage-account-resource-id> --logs '[{"category": <category name>, "enabled": true "retentionPolicy": {"days": <days>, "enabled": <retention-bool}}]'
```

次に例を示します。

`az monitor diagnostic-settings create --name setting1 --workspace /subscriptions/208841be-a4v3-4234-9450-08b90c09f4/resourceGroups/myresourcegroup/providers/Microsoft.OperationalInsights/workspaces/my-analytic-workspace --resource /subscriptions/938841be-a40c-4bf4-9210-08bcf06c09f9/resourceGroups/myresourcegroup/providers/Microsoft.Storage/storageAccounts/myloggingstorageaccount/tableServices/default --logs '[{"category": StorageDelete, "enabled": true ]'`

 詳しくは、[Azure Monitor の Log Analytics ワークスペースへの Azure リソース ログのストリーム配信](../../azure-monitor/essentials/resource-logs.md#send-to-log-analytics-workspace)に関するページを参照してください。

### <a name="template"></a>[テンプレート](#tab/template)

診断設定を作成する Azure Resource Manager テンプレートを表示するには、「[Azure Storage の診断設定](../../azure-monitor/essentials/resource-manager-diagnostic-settings.md#diagnostic-setting-for-azure-storage)」を参照してください。

---

## <a name="analyzing-metrics"></a>メトリックの分析

メトリックス エクスプローラーを使用して、他の Azure サービスのメトリックと共に Azure Storage のメトリックを分析できます。 メトリックス エクスプローラーを開くには、 **[Azure Monitor]** メニューの **[メトリック]** を選択します。 このツールの使用方法の詳細については、「[Azure メトリックス エクスプローラーの概要](../../azure-monitor/essentials/metrics-getting-started.md)」をご覧ください。 

次の例は、アカウント レベルで **トランザクション** を表示する方法を示しています。

![Azure Portal でのメトリック アクセスのスクリーンショット](./media/monitor-table-storage/access-metrics-portal.png)

ディメンションをサポートするメトリックについては、目的のディメンション値でメトリックをフィルター処理できます。 次の例は、 **[API 名]** ディメンションの値を選択することで特定の操作に対するアカウント レベルの **トランザクション** を表示する方法を示しています。

![Azure Portal におけるディメンションでのメトリック アクセスのスクリーンショット](./media/monitor-table-storage/access-metrics-portal-with-dimension.png)

Azure Storage でサポートされるディメンションの完全な一覧については、「[メトリックのディメンション](monitor-table-storage-reference.md#metrics-dimensions)」をご覧ください。

Azure Table ストレージのメトリックは、次の名前空間にあります。 

- Microsoft.Storage/storageAccounts
- Microsoft.Storage/storageAccounts/tableServices

Azure Monitor でサポートされるすべてのメトリックの一覧 (Azure Table ストレージを含む) については、[Azure Monitor でサポートされるメトリック](../../azure-monitor/essentials/metrics-supported.md)に関するページを参照してください。


### <a name="accessing-metrics"></a>メトリックにアクセスする

> [!TIP]
> Azure CLI または .NET の例を表示するには、ここに示した対応するタブを選択します。

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

#### <a name="list-the-metric-definition"></a>メトリック定義の一覧表示

ストレージ アカウントまたは Table ストレージ サービスのメトリック定義を一覧表示できます。 [Get-AzMetricDefinition](/powershell/module/az.monitor/get-azmetricdefinition) コマンドレットを使用します。

この例では、`<resource-ID>` プレースホルダーを、ストレージ アカウント全体のリソース ID または Table ストレージ サービスのリソース ID に置き換えます。  これらのリソース ID は、Azure portal 上のストレージ アカウントの **[プロパティ]** ページで確認できます。

```powershell
   $resourceId = "<resource-ID>"
   Get-AzMetricDefinition -ResourceId $resourceId
```

#### <a name="reading-metric-values"></a>メトリック値を読み取る

ストレージ アカウントまたは Table ストレージ サービスのアカウント レベルのメトリック値を読み取ることができます。 [Get-AzMetric](/powershell/module/Az.Monitor/Get-AzMetric) コマンドレットを使用します。

```powershell
   $resourceId = "<resource-ID>"
   Get-AzMetric -ResourceId $resourceId -MetricNames "UsedCapacity" -TimeGrain 01:00:00
```

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

#### <a name="list-the-account-level-metric-definition"></a>アカウント レベルのメトリック定義を一覧表示する

ストレージ アカウントまたは Table ストレージ サービスのメトリック定義を一覧表示できます。 [az monitor metrics list-definitions](/cli/azure/monitor/metrics#az-monitor-metrics-list-definitions) コマンドを使用します。
 
この例では、`<resource-ID>` プレースホルダーを、ストレージ アカウント全体のリソース ID または Table ストレージ サービスのリソース ID に置き換えます。 これらのリソース ID は、Azure portal 上のストレージ アカウントの **[プロパティ]** ページで確認できます。

```azurecli-interactive
   az monitor metrics list-definitions --resource <resource-ID>
```

#### <a name="read-account-level-metric-values"></a>アカウント レベルのメトリック値を読み取る

ストレージ アカウントまたは Table ストレージ サービスのメトリック値を読み取ることができます。 [az monitor metrics list](/cli/azure/monitor/metrics#az-monitor-metrics-list) コマンドを使用します。

```azurecli-interactive
   az monitor metrics list --resource <resource-ID> --metric "UsedCapacity" --interval PT1H
```

### <a name="net"></a>[.NET](#tab/azure-portal)

Azure Monitor には、メトリックの定義と値を読み取るための [.NET SDK](https://www.nuget.org/packages/Microsoft.Azure.Management.Monitor/) が用意されています。 [サンプル コード](https://azure.microsoft.com/resources/samples/monitor-dotnet-metrics-api/)では、さまざまなパラメーターで SDK を使用する方法を示します。 ストレージ メトリックスについては `0.18.0-preview` 以降のバージョンを使用する必要があります。
 
この例では、`<resource-ID>` プレースホルダーを、ストレージ アカウント全体または Table ストレージ サービスのリソース ID に置き換えます。 これらのリソース ID は、Azure portal 上のストレージ アカウントの **[プロパティ]** ページで確認できます。

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
        // Resource ID for table storage
        var resourceId = "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{storageAccountName}/tableServices/default";
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

### <a name="template"></a>[テンプレート](#tab/template)

該当なし。

---

## <a name="analyzing-logs"></a>ログの分析

リソース ログには、ストレージ アカウント内の BLOB として、イベント データとして、またはログ分析クエリを使用してアクセスできます。

これらのログに記載されるフィールドの詳細なリファレンスについては、「[Azure Tabel ストレージ監視データのリファレンス](monitor-table-storage-reference.md)」を参照してください。

> [!NOTE]
> Azure Monitor の Azure Storage ログはパブリック プレビュー段階にあり、すべてのパブリック クラウド リージョンでプレビュー テスト用に使用できます。 このプレビューでは、汎用 v1 と汎用 v2 ストレージ アカウントの BLOB (Azure Data Lake Storage Gen2 を含む)、ファイル、キュー、テーブル、Premium ストレージ アカウントに対してログが有効になります。 クラシック ストレージ アカウントはサポートされていません。

ログ エントリが作成されるのは、サービス エンドポイントに対して行われた要求がある場合に限られます。 たとえば、ストレージ アカウントのテーブル エンドポイントにはアクティビティが存在するが、BLOB エンドポイントまたはキュー エンドポイントには存在しない場合、テーブル サービスに関連するログだけが作成されます。 Azure Storage ログには、ストレージ サービスに対する要求の成功と失敗についての詳細な情報が含まれています。 この情報を使って個々の要求を監視したり、ストレージ サービスに関する問題を診断したりできます。 要求は、ベスト エフォートでログに記録されます。

### <a name="log-authenticated-requests"></a>認証済み要求をログに記録する

 次のタイプの認証済み要求が記録されます。

- 成功した要求
- 失敗した要求 (タイムアウト、スロットル、ネットワーク、承認などに関する各種エラー)
- Shared Access Signature (SAS) または OAuth を使用した要求 (失敗した要求と成功した要求を含む)
- 分析データ ( **$logs** コンテナーの従来のログ データと、 **$metric** テーブルのクラス メトリック データ) に対する要求

Table ストレージ サービス自体による要求 (ログの作成や削除など) はログに記録されません。 ログに記録されるデータの完全な一覧については、[ストレージのログに記録された操作とステータス メッセージ](/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages)に関するページと、[ストレージのログの形式](monitor-table-storage-reference.md)に関するページをご覧ください。

### <a name="log-anonymous-requests"></a>匿名要求をログに記録する

 次のタイプの匿名要求が記録されます。

- 成功した要求
- サーバー エラー
- クライアントとサーバーの両方のタイムアウト エラー
- エラー コード 304 (変更されていません) で失敗した GET 要求

その他の失敗した匿名要求は一切記録されません。 ログに記録されるデータの完全な一覧については、[ストレージのログに記録された操作とステータス メッセージ](/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages)に関するページと、[ストレージのログの形式](monitor-table-storage-reference.md)に関するページをご覧ください。

### <a name="accessing-logs-in-a-storage-account"></a>ストレージ アカウントのログにアクセスする

ログは、ターゲット ストレージ アカウントのコンテナーに格納された BLOB として表示されます。 データは、行区切りの JSON ペイロードとして収集され、1 つの BLOB 内に格納されます。 BLOB のこの名前は次の名前付け規則に従っています。

`https://<destination-storage-account>.blob.core.windows.net/insights-logs-<storage-operation>/resourceId=/subscriptions/<subscription-ID>/resourceGroups/<resource-group-name>/providers/Microsoft.Storage/storageAccounts/<source-storage-account>/tableServices/default/y=<year>/m=<month>/d=<day>/h=<hour>/m=<minute>/PT1H.json`

次に例を示します。

`https://mylogstorageaccount.blob.core.windows.net/insights-logs-storagewrite/resourceId=/subscriptions/`<br>`208841be-a4v3-4234-9450-08b90c09f4/resourceGroups/myresourcegroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount/tableServices/default/y=2019/m=07/d=30/h=23/m=12/PT1H.json`

### <a name="accessing-logs-in-an-event-hub"></a>イベント ハブのログにアクセスする

イベント ハブに送信されたログはファイルとして保存されませんが、イベント ハブでログ情報が受信されたことを確認することはできます。 Azure portal でイベント ハブに移動し、**受信メッセージ** の数がゼロより大きいことを確認します。 

![監査ログ](media/monitor-table-storage/event-hub-log.png)

イベント ハブに送信されるログ データのアクセスと読み取りには、セキュリティ情報とイベント管理のツールと監視ツールを使用します。 詳細については、[Azure リソース ログ](../../azure-monitor/essentials/resource-logs.md#send-to-azure-event-hubs)に関するページを参照してください。

### <a name="accessing-logs-in-a-log-analytics-workspace"></a>Log Analytics ワークスペースのログにアクセスする

Azure Monitor ログ クエリを使用して、Log Analytics ワークスペースに送信されたログにアクセスできます。

詳細については、「[イベント ハブと外部パートナーへの Azure 監視データのストリーム配信](../../azure-monitor/essentials/stream-monitoring-data-event-hubs.md)」を参照してください。

データは **StorageTableLogs** テーブルに格納されます。 

#### <a name="sample-kusto-queries"></a>サンプル Kusto クエリ

ここでは、Table ストレージの監視に役立つ、 **[ログ検索]** バーに入力できるクエリの一部を紹介します。 これらのクエリは[新しい言語](../../azure-monitor/logs/log-query-overview.md)で使用できます。

> [!IMPORTANT]
> ストレージ アカウント リソース グループ メニューから **[ログ]** を選択すると、クエリのスコープが現在のリソース グループに設定された状態で Log Analytics が開きます。 つまり、ログ クエリには、そのリソース グループのデータのみが含まれます。 他のリソースのデータや他の Azure サービスのデータを含むクエリを実行する場合は、**Azure Monitor** のメニューから **[ログ]** を選択します。 詳細については、「[Azure Monitor Log Analytics のログ クエリのスコープと時間範囲](../../azure-monitor/logs/scope.md)」を参照してください。

これらのクエリを使用すると、Azure Storage アカウントの監視に役立ちます。

* 過去 3 日間に発生した 10 件の最も一般的なエラーの一覧を表示します。

    ```Kusto
    StorageTableLogs
    | where TimeGenerated > ago(3d) and StatusText !contains "Success"
    | summarize count() by StatusText
    | top 10 by count_ desc
    ```
* 過去 3 日間で最も多く発生したエラーの原因となった上位 10 件の操作を一覧表示します。

    ```Kusto
    StorageTableLogs
    | where TimeGenerated > ago(3d) and StatusText !contains "Success"
    | summarize count() by OperationName
    | top 10 by count_ desc
    ```
* 過去 3 日間でエンドツーエンドの待機時間が最も長かった上位 10 個の操作を一覧表示します。

    ```Kusto
    StorageTableLogs
    | where TimeGenerated > ago(3d)
    | top 10 by DurationMs desc
    | project TimeGenerated, OperationName, DurationMs, ServerLatencyMs, ClientLatencyMs = DurationMs - ServerLatencyMs
    ```
* 過去 3 日間にサーバー側の調整エラーの原因となったすべての操作を一覧表示します。

    ```Kusto
    StorageTableLogs
    | where TimeGenerated > ago(3d) and StatusText contains "ServerBusy"
    | project TimeGenerated, OperationName, StatusCode, StatusText
    ```
* 過去 3 日間の匿名アクセスを含むすべての要求を一覧表示します。

    ```Kusto
    StorageTableLogs
    | where TimeGenerated > ago(3d) and AuthenticationType == "Anonymous"
    | project TimeGenerated, OperationName, AuthenticationType, Uri
    ```
* 過去 3 日間に使用された操作の円グラフを作成します。
    ```Kusto
    StorageTableLogs
    | where TimeGenerated > ago(3d)
    | summarize count() by OperationName
    | sort by count_ desc 
    | render piechart
    ```
## <a name="faq"></a>よく寄せられる質問

**Azure Storage はマネージド ディスクまたはアンマネージド ディスクのメトリックをサポートしますか。**

いいえ。 Azure Compute はディスク上のメトリックをサポートします。 詳細については、[マネージド ディスクと非管理ディスクのディスクあたりのメトリック](https://azure.microsoft.com/blog/per-disk-metrics-managed-disks/)に関するページをご覧ください。

## <a name="next-steps"></a>次のステップ

- Azure Table ストレージによって作成されるログおよびメトリックのリファレンスについては、「[Azure Table ストレージ監視データのリファレンス](monitor-table-storage-reference.md)」を参照してください。
- Azure リソースの監視の詳細については、「[Azure Monitor で Azure リソースを管理する](../../azure-monitor/essentials/monitor-azure-resource.md)」をご覧ください。
- メトリックの移行の詳細については、「[Azure Storage メトリックの移行](../common/storage-metrics-migration.md)」をご覧ください。