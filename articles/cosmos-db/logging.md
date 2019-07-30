---
title: Azure Cosmos DB での診断ログ
description: Azure Cosmos DB に格納されているデータをログ記録および監視するさまざまな方法について説明します。
author: SnehaGunda
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/23/2019
ms.author: sngun
ms.custom: seodec18
ms.openlocfilehash: 41fa5a859e738c2bb70e4885aa856f247e922492
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/28/2019
ms.locfileid: "67448989"
---
# <a name="diagnostic-logging-in-azure-cosmos-db"></a>Azure Cosmos DB での診断ログ 

1 つまたは複数の Azure Cosmos DB データベースを使い始めた後、データベースのアクセス方法と時間を監視したいと考えるのではないでしょうか。 この記事では、Azure プラットフォームで利用できるログの概要を示します。 [Azure Storage](https://azure.microsoft.com/services/storage/) にログを送信するための監視を目的として診断ログを有効にする方法、[Azure Event Hubs](https://azure.microsoft.com/services/event-hubs/) にログをストリーミングする方法、[Azure Monitor ログ](https://azure.microsoft.com/services/log-analytics/)にログをエクスポートする方法を説明します。

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="logs-available-in-azure"></a>Azure で利用可能なログ

Azure Cosmos DB アカウントの監視方法を説明する前に、ログと監視に関するいくつかの点を明確にしておきます。 Azure プラットフォームのログには、さまざまな種類があります。 [Azure アクティビティ ログ](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs)、[Azure 診断ログ](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs)、[Azure メトリック](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-metrics)、イベント、ハートビート監視、操作ログなどです。 大量のログが存在します。 Azure portal の [Azure Monitor ログ](https://azure.microsoft.com/services/log-analytics/)で、ログの完全な一覧を確認できます。 

次の図は、利用可能な Azure ログの種類を示しています。

![さまざまな種類の Azure ログ](./media/logging/azurelogging.png)

この図の**コンピューティング リソース**とは、ユーザーが Microsoft ゲスト OS にアクセスできる Azure リソースを表します。 たとえば、Azure Virtual Machines、仮想マシン スケール セット、Azure Container Service などが、コンピューティング リソースと見なされます。 コンピューティング リソースは、アクション ログ、診断ログとアプリケーション ログを生成します。 詳細については、[Azure でのデータ監視のソース](../azure-monitor/platform/data-sources.md)に関する記事を参照してください。

**非コンピューティング リソース**とは、ユーザーが基盤となる OS にアクセスできず、リソースに直接作業するリソースです。 たとえば、ネットワーク セキュリティ グループ、Azure Logic Apps などが挙げられます。 Azure Cosmos DB は、非コンピューティング リソースです。 非コンピューティング リソースのログは、アクティビティ ログで確認するか、ポータルの [診断ログ] オプションを有効にすることで表示できます。 詳細については、[Azure Monitor でのデータのソース](../azure-monitor/platform/data-sources.md)に関する記事を参照してください。

アクティビティ ログは、Azure Cosmos DB のサブスクリプション レベルでの操作を記録します。 ListKeys や DatabaseAccounts の書き込みなどの操作が記録されます。 診断ログは、より詳細なログ記録を提供し、DataPlaneRequests (Create, Read, Query など) と MongoRequests を記録できます。


この記事では、Azure アクティビティ ログ、Azure 診断ログ、および Azure メトリックについて説明します。 この 3 つのログの違いは何でしょうか。 

### <a name="azure-activity-log"></a>[Azure Activity Log (Azure アクティビティ ログ)]

Azure アクティビティ ログは、Azure で発生したサブスクリプション レベルのイベントの分析に利用できるサブスクリプション ログです。 アクティビティ ログは、サブスクリプションのコントロール プレーン イベントを管理カテゴリで報告します。 アクティビティ ログを使うと、サブスクリプションのリソースに対して発生する書き込み操作 (PUT、POST、DELETE) すべてについて、"いつ誰が何を" 行ったのかを確認できます。 さらに、操作の状態など、重要性の大きなプロパティを確認することもできます。 

アクティビティ ログは診断ログとは異なります。 アクティビティ ログは、外部から行われるリソースの操作に関するデータを提供します ("_コントロール プレーン_")。 Azure Cosmos DB のコンテキストでは、コントロール プレーンの操作には、コンテナーの作成、キーの一覧表示、キーの削除、データベースの一覧表示などが含まれます。 診断ログは、リソースによって出力され、そのリソースの操作に関する情報を提供します ("_データ プレーン_")。 診断ログでのデータ プレーン操作の例としては、Delete、Insert、ReadFeed などがあります。

アクティビティ ログ (コントロール プレーン操作) は、実際は非常に大量になる可能性があり、呼び出し元の完全な電子メール アドレス、呼び出し元の IP アドレス、リソース名、操作名、TenantId などが含まれる可能性があります。 アクティビティ ログには、データの複数の[カテゴリ](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-activity-log-schema)が含まれます。 これらのカテゴリのスキーマの詳細については、「[Azure アクティビティ ログのイベント スキーマ](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-activity-log-schema)」を参照してください。 ただし、診断ログでは、多くの場合、個人データがこれらのログから削除されるため、実際は限定的である可能性があります。 呼び出し元の IP アドレスが存在する可能性がありますが、最後のオクテットは削除されています。

### <a name="azure-metrics"></a>Azure メトリック

大半の Azure リソースによって出力される Azure テレメトリ データ (_パフォーマンスカウンター_ とも呼ばれます)の最も重要な種類は、[Azure メトリック](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-metrics)です。 メトリックを使って、Azure Cosmos DB リソースのスループット、ストレージ、整合性、可用性、および 待機時間に関する情報を表示できます。 詳細については、「[Azure Cosmos DB のメトリックを使用した監視とデバッグ](use-metrics.md)」を参照してください。

### <a name="azure-diagnostic-logs"></a>Azure 診断ログ

Azure 診断ログはリソースによって出力され、そのリソースの操作に関する豊富なデータを提供します。 これらのログは、要求ごとにキャプチャされます。 これらのログの内容は、リソースの種類によって異なります。 リソースレベルの診断ログは、ゲスト OS レベルの診断ログとも異なります。 ゲスト OS 診断ログは、仮想マシンの内部で実行されているエージェントや、他のサポートされるリソースの種類によって収集されるログです。 リソース レベルの診断ログは、エージェントを必要とせず、Azure プラットフォーム自体からリソースに固有のデータをキャプチャします。 ゲスト OS レベルの診断ログは、オペレーティング システムと、仮想マシンで実行しているアプリケーションから、データをキャプチャします。

![Storage、Event Hubs、または Azure Monitor ログに対する診断ログ記録](./media/logging/azure-cosmos-db-logging-overview.png)

### <a name="what-is-logged-by-azure-diagnostic-logs"></a>Azure 診断ログによって記録されるもの

* すべての API で認証されたバックエンド要求 (TCP / REST) がすべて記録されます。たとえば、アクセス許可、システム エラー、または不正な要求の結果として失敗した要求などが記録されます。 ユーザーが開始した Graph、Cassandra、および Table API 要求のサポートは現在使用できません。
* データベース自体に対する操作。すべてのドキュメント、コンテナー、およびデータベースに対する CRUD 操作が含まれます。
* アカウント キーに対する操作。キーの作成、変更、または削除が含まれます。
* 結果として 401 応答が発生する、認証されていない要求。 たとえば、ベアラー トークンを持たない要求、形式が正しくない要求、有効期限切れの要求、または無効なトークンを持つ要求です。

<a id="#turn-on"></a>
## <a name="turn-on-logging-in-the-azure-portal"></a>Azure Portal でログを有効にする

以下の手順を使用して、Azure Portal で診断ログを有効にします。

1. [Azure Portal](https://portal.azure.com) にサインインします。 

1. Azure Cosmos アカウントに移動します。 **[診断設定]** ウィンドウを開き、 **[診断設定の追加]** オプションを選択します。

    ![Azure Portal で Azure Cosmos DB の診断ログを有効にする](./media/logging/turn-on-portal-logging.png)

1. **[診断設定]** ページで以下の詳細情報をフォームに入力します。 

    * **[名前]** :作成するログの名前を入力します。

    * 次のサービスにログを格納できます。

      * **[ストレージ アカウントへのアーカイブ]** :このオプションを使用するには、接続先として既存のストレージ アカウントが必要です。 ポータルで新しいストレージ アカウントを作成するには、「[ストレージ アカウントの作成](../storage/common/storage-create-storage-account.md)」の記事を参照してください。 次に、ポータルの Azure Cosmos DB の診断設定ウィンドウに戻り、ストレージ アカウントを選択します。 新しく作成されたストレージ アカウントがドロップダウン メニューに表示されるまでには、数分かかる場合があります。

      * **[イベント ハブへのストリーム]** :このオプションを使用するには、既存の Event Hubs 名前空間と接続先のイベント ハブが必要です。 Event Hubs 名前空間を作成するには、「[Azure Portal を使用して Event Hubs 名前空間とイベント ハブを作成する](../event-hubs/event-hubs-create.md)」をご覧ください。 ポータルでこのページに戻り、Event Hubs 名前空間とポリシー名を選択します。

      * **[Log Analytics への送信]** :このオプションを使用するには、既存のワークスペースを使用するか、ポータルで[新しいワークスペースを作成する](../azure-monitor/learn/quick-collect-azurevm.md#create-a-workspace)手順に従って新しい Log Analytics ワークスペースを作成します。 

   * 次のデータを記録できます。

      * **DataPlaneRequests**:SQL、Graph、MongoDB、Cassandra、および Azure Cosmos DB の Table API アカウントなど、すべての API へのバックエンド要求をログに記録するには、このオプションを選択します。 ストレージ アカウントにアーカイブする場合、診断ログのリテンション期間を選択できます。 リテンション期間が過ぎると、ログは自動的に削除されます。 次の JSON データは、DataPlaneRequests を使用してログに記録された詳細情報の出力例です。 注意すべき重要なプロパティは、Requestcharge、statusCode、clientIPaddress、および partitionID です。

       ```
       { "time": "2019-04-23T23:12:52.3814846Z", "resourceId": "/SUBSCRIPTIONS/<your_subscription_ID>/RESOURCEGROUPS/<your_resource_group>/PROVIDERS/MICROSOFT.DOCUMENTDB/DATABASEACCOUNTS/<your_database_account>", "category": "DataPlaneRequests", "operationName": "ReadFeed", "properties": {"activityId": "66a0c647-af38-4b8d-a92a-c48a805d6460","requestResourceType": "Database","requestResourceId": "","collectionRid": "","statusCode": "200","duration": "0","userAgent": "Microsoft.Azure.Documents.Common/2.2.0.0","clientIpAddress": "10.0.0.24","requestCharge": "1.000000","requestLength": "0","responseLength": "372","resourceTokenUserRid": "","region": "East US","partitionId": "062abe3e-de63-4aa5-b9de-4a77119c59f8","keyType": "PrimaryReadOnlyMasterKey","databaseName": "","collectionName": ""}}
       ```

      * **MongoRequests**:Azure Cosmos DB の MongoDB 用 API の要求にサービスを提供するために、ユーザーがフロントエンドから開始した要求をログに記録するには、このオプションを選択します。 MongoDB 要求は、DataPlaneRequests に加えて MongoRequests にも表示されます。 ストレージ アカウントにアーカイブする場合、診断ログのリテンション期間を選択できます。 リテンション期間が過ぎると、ログは自動的に削除されます。 次の JSON データは、MongoRequests を使用してログに記録された詳細情報の出力例です。 注意すべき重要なプロパティは、Requestcharge、opCode です。

       ```
       { "time": "2019-04-10T15:10:46.7820998Z", "resourceId": "/SUBSCRIPTIONS/<your_subscription_ID>/RESOURCEGROUPS/<your_resource_group>/PROVIDERS/MICROSOFT.DOCUMENTDB/DATABASEACCOUNTS/<your_database_account>", "category": "MongoRequests", "operationName": "ping", "properties": {"activityId": "823cae64-0000-0000-0000-000000000000","opCode": "MongoOpCode_OP_QUERY","errorCode": "0","duration": "0","requestCharge": "0.000000","databaseName": "admin","collectionName": "$cmd","retryCount": "0"}}
       ```

      * **QueryRuntimeStatistics**:実行されたクエリ テキストをログに記録するには、このオプションを選択します。  次の JSON データは、QueryRuntimeStatistics を使用してログに記録された詳細情報の出力例です。

       ```
       { "time": "2019-04-14T19:08:11.6353239Z", "resourceId": "/SUBSCRIPTIONS/<your_subscription_ID>/RESOURCEGROUPS/<your_resource_group>/PROVIDERS/MICROSOFT.DOCUMENTDB/DATABASEACCOUNTS/<your_database_account>", "category": "QueryRuntimeStatistics", "properties": {"activityId": "278b0661-7452-4df3-b992-8aa0864142cf","databasename": "Tasks","collectionname": "Items","partitionkeyrangeid": "0","querytext": "{"query":"SELECT *\nFROM c\nWHERE (c.p1__10 != true)","parameters":[]}"}}
       ```

      * **[Metric Requests]\(メトリック要求\)** :[Azure メトリックス](../azure-monitor/platform/metrics-supported.md)に詳細データを保存するには、このオプションを選択します。 ストレージ アカウントにアーカイブする場合、診断ログのリテンション期間を選択できます。 リテンション期間が過ぎると、ログは自動的に削除されます。

3. **[保存]** を選択します。

    "\<ワークスペース名> の診断を更新できませんでした。 サブスクリプション \<サブスクリプション ID&gt; は microsoft.insights を使用するために登録されていません。" というエラーが表示される場合は、[Azure Diagnostics のトラブルシューティング](https://docs.microsoft.com/azure/log-analytics/log-analytics-azure-storage)に関する指示に従ってアカウントを登録してから、この手順を再試行してください。

    今後、診断ログを保存する方法を変更する場合は、このページに戻ってアカウントの診断ログの設定を変更します。

## <a name="turn-on-logging-by-using-azure-cli"></a>Azure CLI を使用してログ記録を有効にする

Azure CLI を使ってメトリックと診断のロギングを有効にするには、次のコマンドを使います。

- ストレージ アカウントへの診断ログの保存を有効にするには、次のコマンドを使います。

   ```azurecli-interactive
   az monitor diagnostic-settings create --name DiagStorage --resource <resourceId> --storage-account <storageAccountName> --logs '[{"category": "QueryRuntimeStatistics", "enabled": true, "retentionPolicy": {"enabled": true, "days": 0}}]'
   ```

   `resource` は、Azure Cosmos DB アカウントの名前です。 リソースの形式は "/subscriptions/`<subscriptionId>`/resourceGroups/`<resource_group_name>`/providers/Microsoft.DocumentDB/databaseAccounts/<Azure_Cosmos_account_name>" になります。`storage-account` は、ログの送信先ストレージ アカウントの名前です。 他のログをロギングするには、カテゴリのパラメーター値を "MongoRequests" または "DataPlaneRequests" に更新します。 

- イベント ハブへの診断ログのストリーミングを有効にするには、次のコマンドを使用します。

   ```azurecli-interactive
   az monitor diagnostic-settings create --name cdbdiagsett --resourceId <resourceId> --event-hub-rule <eventHubRuleID> --logs '[{"category":"QueryRuntimeStatistics","enabled":true,"retentionPolicy":{"days":6,"enabled":true}}]'
   ```

   `resource` は、Azure Cosmos DB アカウントの名前です。 `event-hub-rule` は、イベント ハブのルール ID です。 

- Log Analytics ワークスペースへの診断ログの送信を有効にするには、次のコマンドを使います。

   ```azurecli-interactive
   az monitor diagnostic-settings create --name cdbdiagsett --resourceId <resourceId> --workspace <resource id of the log analytics workspace> --logs '[{"category":"QueryRuntimeStatistics","enabled":true,"retentionPolicy":{"days":6,"enabled":true}}]'
   ```

このパラメーターを組み合わせて、複数の出力オプションを有効にできます。

## <a name="turn-on-logging-by-using-powershell"></a>PowerShell を使用してログ記録を有効にする

PowerShell を使って診断ログを有効にするには、Azure PowerShell バージョン 1.0.1 以降が必要です。

Azure PowerShell をインストールして、Azure サブスクリプションに関連付けるには、「 [Azure PowerShell のインストールおよび構成方法](/powershell/azure/overview)」を参照してください。

Azure PowerShell をインストール済みで、バージョンがわからない場合は、PowerShell コンソールで「`(Get-Module azure -ListAvailable).Version`」と入力します。  

### <a id="connect"></a>サブスクリプションへの接続
Azure PowerShell セッションを開始し、次のコマンドで Azure アカウントにサインインします。  

```powershell
Connect-AzAccount
```

ポップアップ ブラウザー ウィンドウで、Azure アカウントのユーザー名とパスワードを入力します。 Azure PowerShell は、このアカウントに関連付けられているすべてのサブスクリプションを取得し、既定で最初のサブスクリプションを使います。

複数のサブスクリプションがある場合は、Azure Key Vault を作成するときに使って特定のサブスクリプションを指定することが必要なことがあります。 アカウントのサブスクリプションを確認するには、次のコマンドを入力します。

```powershell
Get-AzSubscription
```

ログを記録する Azure Cosmos DB アカウントに関連付けられているサブスクリプションを指定するには、次のコマンドを入力します。

```powershell
Set-AzContext -SubscriptionId <subscription ID>
```

> [!NOTE]
> 複数のサブスクリプションがアカウントに関連付けられている場合は、使うサブスクリプションを指定することが重要です。
>
>

Azure PowerShell の構成方法について詳しくは、「[Azure PowerShell のインストールおよび構成方法](/powershell/azure/overview)」をご覧ください。

### <a id="storage"></a>ログ用に新しいストレージ アカウントを作成する
ログに既存のストレージ アカウントを使うこともできますが、このチュートリアルでは、Azure Cosmos DB ログ専用の新しいストレージ アカウントを作成します。 便宜上、ストレージ アカウントの詳細を **sa** という変数に格納します。

さらに管理を簡単にするために、このチュートリアルでは、Azure Cosmos DB データベースと同じリソース グループを使います。 **ContosoResourceGroup**、**contosocosmosdblogs**、**North Central US** の各パラメーターは、適宜、実際の値に置き換えてください。

```powershell
$sa = New-AzStorageAccount -ResourceGroupName ContosoResourceGroup `
-Name contosocosmosdblogs -Type Standard_LRS -Location 'North Central US'
```

> [!NOTE]
> 既存のストレージ アカウントを使う場合は、アカウントは Azure Cosmos DB サブスクリプションと同じサブスクリプションを使う必要があります。 アカウントでは、クラシック デプロイ モデルではなく Resource Manager デプロイ モデルが使われている必要があります。
>
>

### <a id="identify"></a>ログ用の Azure Cosmos DB アカウントを指定する
Azure Cosmos DB アカウント名を **account** という変数に設定します。この **ResourceName** は、Azure Cosmos DB アカウントの名前です。

```powershell
$account = Get-AzResource -ResourceGroupName ContosoResourceGroup `
-ResourceName contosocosmosdb -ResourceType "Microsoft.DocumentDb/databaseAccounts"
```

### <a id="enable"></a>ログの有効化
Azure Cosmos DB のログ記録を有効にするには、新しいストレージ アカウント、Azure Cosmos DB アカウント、およびログ用に有効にするカテゴリの変数を指定して、`Set-AzDiagnosticSetting` コマンドレットを使います。 次のコマンドを実行し、 **-Enabled** フラグを **$true** に設定します。

```powershell
Set-AzDiagnosticSetting  -ResourceId $account.ResourceId -StorageAccountId $sa.Id -Enabled $true -Categories DataPlaneRequests
```

コマンドの出力は次のサンプルのようになります。

```powershell
    StorageAccountId            : /subscriptions/<subscription-ID>/resourceGroups/ContosoResourceGroup/providers`
    /Microsoft.Storage/storageAccounts/contosocosmosdblogs
    ServiceBusRuleId            :
    EventHubAuthorizationRuleId :
    Metrics
        TimeGrain       : PT1M
        Enabled         : False
        RetentionPolicy
        Enabled : False
        Days    : 0
    
    Logs
        Category        : DataPlaneRequests
        Enabled         : True
        RetentionPolicy
        Enabled : False
        Days    : 0
    
    WorkspaceId                 :
    Id                          : /subscriptions/<subscription-ID>/resourcegroups/ContosoResourceGroup/providers`
    /microsoft.documentdb/databaseaccounts/contosocosmosdb/providers/microsoft.insights/diagnosticSettings/service
    Name                        : service
    Type                        :
    Location                    :
    Tags                        :
```

コマンドからの出力では、データベースのログ記録が有効になり、情報がストレージ アカウントに保存されていることが確認されています。

必要に応じて、ログのアイテム保持ポリシーを使って、古いログが自動的に削除されるように設定することもできます。 たとえば、アイテム保持ポリシーを設定するには、 **-RetentionEnabled** フラグを **$true** に設定します。 90 日より古いログを自動的に削除するには、 **-RetentionInDays** パラメーターを **90** に設定します。

```powershell
Set-AzDiagnosticSetting -ResourceId $account.ResourceId`
 -StorageAccountId $sa.Id -Enabled $true -Categories DataPlaneRequests`
  -RetentionEnabled $true -RetentionInDays 90
```

### <a id="access"></a>ログへのアクセス
**DataPlaneRequests** カテゴリの Azure Cosmos DB ログは、指定したストレージ アカウントの **insights-logs-dataplanerequests** コンテナーに格納されます。 

最初に、コンテナー名の変数を作成します。 この変数は、チュートリアル全体で使われます。

```powershell
    $container = 'insights-logs-dataplanerequests'
```

このコンテナー内のすべての BLOB を一覧表示するには、次のように入力します。

```powershell
Get-AzStorageBlob -Container $container -Context $sa.Context
```

コマンドの出力は次のサンプルのようになります。

```powershell
ICloudBlob        : Microsoft.WindowsAzure.Storage.Blob.CloudBlockBlob
BlobType          : BlockBlob
Length            : 10510193
ContentType       : application/octet-stream
LastModified      : 9/28/2017 7:49:04 PM +00:00
SnapshotTime      :
ContinuationToken:
Context           : Microsoft.WindowsAzure.Commands.Common.Storage.`
                    LazyAzureStorageContext
Name              : resourceId=/SUBSCRIPTIONS/<subscription-ID>/RESOURCEGROUPS/CONTOSORESOURCEGROUP/PROVIDERS`
/MICROSOFT.DOCUMENTDB/DATABASEACCOUNTS/CONTOSOCOSMOSDB/y=2017/m=09/d=28/h=19/m=00/PT1H.json
```

この出力からわかるように、BLOB は次の命名規則に従います。`resourceId=/SUBSCRIPTIONS/<subscription-ID>/RESOURCEGROUPS/<resource group name>/PROVIDERS/MICROSOFT.DOCUMENTDB/DATABASEACCOUNTS/<Database Account Name>/y=<year>/m=<month>/d=<day of month>/h=<hour>/m=<minute>/filename.json`

日付と時刻の値には UTC が使用されます。

同じストレージ アカウントを使用して複数のリソースのログを収集することができるので、必要な特定の BLOB にアクセスしたり、ダウンロードしたりするには、BLOB 名の完全修飾リソース ID を使うことができます。 その前に、すべての BLOB をダウンロードする方法を説明します。

まず、フォルダーを作成して BLOB をダウンロードします。 例:

```powershell
New-Item -Path 'C:\Users\username\ContosoCosmosDBLogs'`
 -ItemType Directory -Force
```

次に、すべての BLOB の一覧を取得します。  

```powershell
$blobs = Get-AzStorageBlob -Container $container -Context $sa.Context
```

この一覧を `Get-AzStorageBlobContent` コマンドにパイプして、BLOB を保存先のフォルダーにダウンロードします。

```powershell
$blobs | Get-AzStorageBlobContent `
 -Destination 'C:\Users\username\ContosoCosmosDBLogs'
```

この 2 番目のコマンドを実行すると、BLOB 名に含まれる **/** 区切り記号によって、宛先フォルダーの下にフォルダー構造全体が作成されます。 このフォルダー構造は、BLOB をファイルとしてダウンロードし、保存するために使用されます。

BLOB を選択的にダウンロードするには、ワイルドカードを使用します。 例:

* 複数のデータベースを持っている場合に、**CONTOSOCOSMOSDB3** という名前のデータベースのみについてログをダウンロードするには、次のコマンドを使います。

    ```powershell
    Get-AzStorageBlob -Container $container `
     -Context $sa.Context -Blob '*/DATABASEACCOUNTS/CONTOSOCOSMOSDB3
    ```

* 複数のリソース グループを持っている場合、1 つのリソース グループのみについてログをダウンロードするには、`-Blob '*/RESOURCEGROUPS/<resource group name>/*'` コマンドを使います。

    ```powershell
    Get-AzStorageBlob -Container $container `
    -Context $sa.Context -Blob '*/RESOURCEGROUPS/CONTOSORESOURCEGROUP3/*'
    ```
* 2017 年 7 月のすべてのログをダウンロードする場合は、`-Blob '*/year=2017/m=07/*'` コマンドを使います。

    ```powershell
    Get-AzStorageBlob -Container $container `
     -Context $sa.Context -Blob '*/year=2017/m=07/*'
    ```

次のコマンドを実行することもできます。

* データベース リソースの診断設定の状態のクエリを実行するには、`Get-AzDiagnosticSetting -ResourceId $account.ResourceId` コマンドを使います。
* データベース アカウント リソースの **DataPlaneRequests** カテゴリのログ記録を無効にするには、`Set-AzDiagnosticSetting -ResourceId $account.ResourceId -StorageAccountId $sa.Id -Enabled $false -Categories DataPlaneRequests` コマンドを使います。


これらの各クエリで返される BLOB は、次のコードのように、JSON BLOB 形式のテキストに格納されます。

```json
{
    "records":
    [
        {
           "time": "Fri, 23 Jun 2017 19:29:50.266 GMT",
           "resourceId": "contosocosmosdb",
           "category": "DataPlaneRequests",
           "operationName": "Query",
           "resourceType": "Database",
           "properties": {"activityId": "05fcf607-6f64-48fe-81a5-f13ac13dd1eb",`
           "userAgent": "documentdb-dotnet-sdk/1.12.0 Host/64-bit MicrosoftWindowsNT/6.2.9200.0 AzureSearchIndexer/1.0.0",`
           "resourceType": "Database","statusCode": "200","documentResourceId": "",`
           "clientIpAddress": "13.92.241.0","requestCharge": "2.260","collectionRid": "",`
           "duration": "9250","requestLength": "72","responseLength": "209", "resourceTokenUserRid": ""}
        }
    ]
}
```

各 JSON BLOB のデータについては、「[Azure Cosmos DB ログを解釈する](#interpret)」を参照してください。

## <a name="manage-your-logs"></a>ログを管理する

診断ログは、アカウントで Azure Cosmos DB 操作が実行されてから 2 時間後に使用できます。 ストレージ アカウントでのログの管理はお客様に委ねられます。

* ログをセキュリティで保護し、ログにアクセスできるユーザーを制限するには、標準的な Azure アクセス制御方法を使います。
* ストレージ アカウントに保持する必要がなくなったログは削除します。
* ストレージ アカウントにアーカイブされるデータ プレーン要求のリテンション期間は、設定 **[Log DataPlaneRequests]\(DataPlaneRequests のログを記録する\)** をオンにしたときに構成されます。 この設定を変更する方法については、「[Azure Portal でログを有効にする](#turn-on-logging-in-the-azure-portal)」を参照してください。


<a id="#view-in-loganalytics"></a>
## <a name="view-logs-in-azure-monitor-logs"></a>Azure Monitor ログのログを表示する

ログ記録を有効にするときに **[Log Analytics への送信]** オプションを選択した場合、コンテナーの診断ログは 2 時間以内に Azure Monitor ログに転送されます。 ログ記録を有効にした直後に Azure Monitor ログを見ても、データは何も表示されません。 2 時間待ってから、もう一度やり直してください。 

ログを確認する前に、Log Analytics ワークスペースが新しい Kusto クエリ言語を使用できるようにアップグレードされているかどうかを確認します。 確認するには、[Azure portal](https://portal.azure.com) で左端の **[Log Analytics ワークスペース]** を選び、次の図のようにワークスペース名を選択します。 **[Log Analytics ワークスペース]** ページが表示されます。

![Azure portal の Azure Monitor ログ](./media/logging/azure-portal.png)

>[!NOTE]
>OMS ワークスペースは、Log Analytics ワークスペースと呼ばれるようになりました。  

**[Log Analytics ワークスペース]** ページに次のメッセージが表示される場合、ワークスペースは新しい言語を使用できるようにアップグレードされていません。 新しいクエリ言語にアップグレードする方法について詳しくは、「[新しいログ検索への Azure Log Analytics ワークスペースのアップグレード](../log-analytics/log-analytics-log-search-upgrade.md)」をご覧ください。 

![Azure Monitor ログのアップグレード・メッセージ](./media/logging/upgrade-notification.png)

Azure Monitor ログで診断データを表示するには、次の図のように左側のメニューまたはページの **[管理]** 領域から **[ログ検索]** ページを開きます。

![Azure Portal の [ログ検索] オプション](./media/logging/log-analytics-open-log-search.png)

データ収集が有効になったので、新しいクエリ言語を使って次のログ検索例 `AzureDiagnostics | take 10` を実行し、最新の 10 件のログを確認します。

![最新の 10 件のログのサンプル ログ検索](./media/logging/log-analytics-query.png)

<a id="#queries"></a>
### <a name="queries"></a>クエリ

**[ログ検索]** ボックスに入力して Azure Cosmos DB コンテナーの監視に利用できるその他のクエリを紹介します。 これらのクエリは[新しい言語](../log-analytics/log-analytics-log-search-upgrade.md)で使用できます。 

各ログ検索から返されるデータの意味については、「[Azure Cosmos DB ログを解釈する](#interpret)」をご覧ください。

* 指定した期間の Azure Cosmos DB のすべての診断ログをクエリするには:

    ```
    AzureDiagnostics | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests"
    ```

* ログに記録された最新の 10 件のイベントをクエリするには:

    ```
    AzureDiagnostics | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests" | take 10
    ```

* 操作の種類でグループ分けされたすべての操作をクエリするには:

    ```
    AzureDiagnostics | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests" | summarize count() by OperationName
    ```

* **リソース**でグループ分けされたすべての操作をクエリするには:

    ```
    AzureActivity | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests" | summarize count() by Resource
    ```

* リソースでグループ分けされたすべてのユーザー アクティビティをクエリするには:

    ```
    AzureActivity | where Caller == "test@company.com" and ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests" | summarize count() by Resource
    ```
    > [!NOTE]
    > このコマンドは、診断ログではなく、アクティビティ ログ用です。

* 実行時間が 3 ミリ秒を超えている操作をクエリするには:

    ```
    AzureDiagnostics | where toint(duration_s) > 30000 and ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests" | summarize count() by clientIpAddress_s, TimeGenerated
    ```

* 操作を実行しているエージェントをクエリするには:

    ```
    AzureDiagnostics | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests" | summarize count() by OperationName, userAgent_s
    ```

* 実行時間が長い操作が実行された時刻をクエリするには:

    ```
    AzureDiagnostics | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests" | project TimeGenerated , toint(duration_s)/1000 | render timechart
    ```

新しいログ検索言語の使い方について詳しくは、「[Azure Monitor ログでのログ検索について](../log-analytics/log-analytics-log-search-new.md)」をご覧ください。 

## <a id="interpret"></a>ログを解釈する

Azure Storage と Azure Monitor ログに格納されている診断データは、スキーマが似ています。 

次の表は、各ログ エントリの内容をまとめた一覧です。

| Azure Storage のフィールドまたはプロパティ | Azure Monitor ログのプロパティ | 説明 |
| --- | --- | --- |
| **time** | **TimeGenerated** | 操作が発生した日時 (UTC)。 |
| **resourceId** | **リソース** | ログが有効になっている Azure Cosmos DB アカウント。|
| **category** | **カテゴリ** | Azure Cosmos DB ログの場合、使用できる値は **DataPlaneRequests** のみです。 |
| **operationName** | **OperationName** | 操作の名前。 この値は、Create、Update、Read、ReadFeed、Delete、Replace、Execute、SqlQuery、Query、JSQuery、Head、HeadFeed、または Upsert 操作のいずれかです。   |
| **properties** | 該当なし | このフィールドの内容については、以下の行を参照してください。 |
| **activityId** | **activityId_g** | ログに記録された操作の一意の GUID。 |
| **userAgent** | **userAgent_s** | 要求を実行するクライアント ユーザー エージェントを示す文字列。 {ユーザー エージェント名}/{バージョン} という形式です。|
| **requestResourceType** | **requestResourceType_s** | アクセスされたリソースの種類。 この値は、リソースの種類 Database、Container、Document、Attachment、User、Permission、StoredProcedure、Trigger、UserDefinedFunction、または Offer のいずれかです。 |
| **statusCode** | **statusCode_s** | 操作の応答状態。 |
| **requestResourceId** | **ResourceId** | 要求に関連するリソース ID。 値は、実行された操作によって databaseRid、collectionRid、または documentRid を示す可能性があります。|
| **clientIpAddress** | **clientIpAddress_s** | クライアントの IP アドレス。 |
| **requestCharge** | **requestCharge_s** | 操作に使用された RU 数 |
| **collectionRid** | **collectionId_s** | コレクションの一意の ID。|
| **duration** | **duration_s** | 操作の期間 (ティック単位)。 |
| **requestLength** | **requestLength_s** | 要求の長さ (バイト単位)。 |
| **responseLength** | **responseLength_s** | 応答の長さ (バイト単位)。|
| **resourceTokenUserRid** | **resourceTokenUserRid_s** | [リソース トークン](https://docs.microsoft.com/azure/cosmos-db/secure-access-to-data#resource-tokens)が認証に使われると、この値は空ではありません。 値は、ユーザーのリソース ID を示します。 |

## <a name="next-steps"></a>次の手順

- ロギングを有効にする方法、および各種 Azure サービスでサポートされるメトリックとログのカテゴリーについて理解を深めるには、「[Microsoft Azure のメトリックの概要](../monitoring-and-diagnostics/monitoring-overview-metrics.md)」と「[Azure リソースからのログ データの収集と使用](../azure-monitor/platform/diagnostic-logs-overview.md)」の両方をご覧ください。
- Event Hubs については次の記事をご覧ください。
   - [Azure Event Hubs とは](../event-hubs/event-hubs-what-is-event-hubs.md)
   - [Event Hubs の使用](../event-hubs/event-hubs-csharp-ephcs-getstarted.md)
- [Azure Storage からメトリックとログをダウンロードする方法](../storage/blobs/storage-quickstart-blobs-dotnet.md#download-blobs)に関する記事をご覧ください。
- 「[Azure Monitor ログでのログ検索について](../log-analytics/log-analytics-log-search-new.md)」をお読みください。
