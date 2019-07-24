---
title: Azure Analysis Services の診断ログ | Microsoft Docs
description: Azure Analysis Services の診断ログの設定について説明します。
author: minewiskan
manager: kfile
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 02/14/2019
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 2303d385d3d688050a8d82c07e78a68588f41e88
ms.sourcegitcommit: 15e9613e9e32288e174241efdb365fa0b12ec2ac
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/28/2019
ms.locfileid: "57010924"
---
# <a name="setup-diagnostic-logging"></a>診断ログのセットアップ

Analysis Services ソリューションの重要な部分は、サーバーのパフォーマンスを監視することです。 [Azure リソースの診断ログ](../azure-monitor/platform/diagnostic-logs-overview.md)を使用すると、ログを監視して [Azure Storage](https://azure.microsoft.com/services/storage/) に送信したり、ログを [Azure Event Hubs](https://azure.microsoft.com/services/event-hubs/) にストリーミング配信したり、[Azure Monitor ログ](../azure-monitor/azure-monitor-log-hub.md)にエクスポートしたりすることができます。

![Storage、Event Hubs、または Azure Monitor ログに対する診断ログ記録](./media/analysis-services-logging/aas-logging-overview.png)

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="whats-logged"></a>ログに記録されるもの

**[エンジン]**、**[サービス]**、および **[メトリック]** のカテゴリを選択できます。

### <a name="engine"></a>Engine

**[エンジン]** を選択すると、すべての [xEvents](https://docs.microsoft.com/sql/analysis-services/instances/monitor-analysis-services-with-sql-server-extended-events) がログに記録されます。 個々のイベントを選択することはできません。 

|XEvent カテゴリ |イベント名  |
|---------|---------|
|セキュリティ監査    |   Audit Login      |
|セキュリティ監査    |   Audit Logout      |
|セキュリティ監査    |   Audit Server Starts And Stops      |
|進行状況レポート     |   Progress Report Begin      |
|進行状況レポート     |   Progress Report End      |
|進行状況レポート     |   Progress Report Current      |
|クエリ     |  Query Begin       |
|クエリ     |   Query End      |
|command     |  Command Begin       |
|command     |  Command End       |
|エラーと警告     |   Error      |
|発見     |   Discover End      |
|通知     |    通知     |
|Session     |  Session Initialize       |
|ロック    |  Deadlock       |
|クエリの処理     |   VertiPaq SE Query Begin      |
|クエリの処理     |   VertiPaq SE Query End      |
|クエリの処理     |   VertiPaq SE Query Cache Match      |
|クエリの処理     |   Direct Query Begin      |
|クエリの処理     |  Direct Query End       |

### <a name="service"></a>Service

|操作の名前  |発生するタイミング  |
|---------|---------|
|ResumeServer     |    サーバーを再開するとき     |
|SuspendServer    |   サーバーを一時停止するとき      |
|DeleteServer     |    サーバーの削除     |
|RestartServer    |     ユーザーが SSMS または PowerShell を経由してサーバーを再起動するとき    |
|GetServerLogFiles    |    ユーザーが PowerShell を通じてサーバー ログをエクスポートするとき     |
|ExportModel     |   ユーザーが [Visual Studio で開く] を使用してポータルでモデルをエクスポートするとき     |

### <a name="all-metrics"></a>すべてのメトリック

メトリックのカテゴリは、メトリックに表示されるのと同じ[サーバーのメトリック](analysis-services-monitor.md#server-metrics)をログ記録します。

## <a name="setup-diagnostics-logging"></a>診断ログのセットアップ

### <a name="azure-portal"></a>Azure ポータル

1. [Azure Portal](https://portal.azure.com) > サーバーで、左のナビゲーションから、**[診断ログ]** をクリックし、**[診断をオンにする]** をクリックします。

    ![Azure Portal で Azure Cosmos DB の診断ログを有効にする](./media/analysis-services-logging/aas-logging-turn-on-diagnostics.png)

2. **[診断設定]** で、次のオプションを指定します。 

    * **名前**。 作成するログの名前を入力します。

    * **[ストレージ アカウントへのアーカイブ]**。 このオプションを使用するには、接続先として既存のストレージ アカウントが必要です。 「[ストレージ アカウントを作成する](../storage/common/storage-create-storage-account.md)」を参照してください。 指示に従って、Resource Manager の汎用アカウントを作成し、ポータルのこのページに戻ってストレージ アカウントを選択します。 新しく作成されたストレージ アカウントがドロップダウン メニューに表示されるまでには、数分かかる場合があります。
    * **イベント ハブにストリーミングします**。 このオプションを使用するには、既存の Event Hubs 名前空間と接続先のイベント ハブが必要です。 詳細については、「[Azure Portal を使用して Event Hubs 名前空間とイベント ハブを作成する](../event-hubs/event-hubs-create.md)」をご覧ください。 Portal でこのページに戻り、Event Hubs 名前空間とポリシー名を選択します。
    * **Azure Monitor (Log Analytics ワークスペース) に送信します**。 このオプションを使用するには、既存のワークスペースを使用するか、またはポータルで[新しいワークスペース リソースを作成](../azure-monitor/learn/quick-create-workspace.md)します。 ログを表示する方法について詳しくは、この記事の「[Log Analytics ワークスペースでログを表示する](#view-logs-in-log-analytics-workspace)」をご覧ください。

    * **エンジン**。 xEvents をログ記録するには、このオプションを選択します。 ストレージ アカウントにアーカイブする場合、診断ログのリテンション期間を選択できます。 リテンション期間が過ぎると、ログは自動的に削除されます。
    * **サービス**。 サービス レベル イベントをログ記録するには、このオプションを選択します。 ストレージ アカウントにアーカイブする場合、診断ログのリテンション期間を選択できます。 リテンション期間が過ぎると、ログは自動的に削除されます。
    * **メトリック**。 [メトリック](analysis-services-monitor.md#server-metrics)に詳細データを保存するには、このオプションを使用します。 ストレージ アカウントにアーカイブする場合、診断ログのリテンション期間を選択できます。 リテンション期間が過ぎると、ログは自動的に削除されます。

3. **[Save]** をクリックします。

    "\<ワークスペース名> の診断を更新できませんでした。 サブスクリプション \<サブスクリプション ID> は microsoft.insights を使用するために登録されていません。" というエラーが表示される場合は、[Azure Diagnostics のトラブルシューティング](https://docs.microsoft.com/azure/log-analytics/log-analytics-azure-storage)に関する指示に従ってアカウントを登録してから、この手順を再試行してください。

    今後、診断ログを保存する方法を変更する場合は、このページに戻って設定を変更できます。

### <a name="powershell"></a>PowerShell

興味を引く基本的なコマンドを次に示します。 PowerShell を使用してストレージ アカウントにログ記録をセットアップする詳細な手順が必要な場合は、この記事で後述するチュートリアルを参照してください。

PowerShell を使用してメトリックと診断のロギングを有効にするには、次のコマンドを使用します。

- ストレージ アカウントへの診断ログの保存を有効にするには、次のコマンドを使用します。

   ```powershell
   Set-AzDiagnosticSetting -ResourceId [your resource id] -StorageAccountId [your storage account id] -Enabled $true
   ```

   ストレージ アカウント ID は、ログの送信先となるストレージ アカウントのリソース ID です。

- Event Hubs への診断ログのストリーミングを有効にするには、次のコマンドを使用します。

   ```powershell
   Set-AzDiagnosticSetting -ResourceId [your resource id] -ServiceBusRuleId [your service bus rule id] -Enabled $true
   ```

   Azure Service Bus ルール ID は、次の形式の文字列です。

   ```powershell
   {service bus resource ID}/authorizationrules/{key name}
   ``` 

- Log Analytics ワークスペースへの診断ログの送信を有効にするには、次のコマンドを使用します。

   ```powershell
   Set-AzDiagnosticSetting -ResourceId [your resource id] -WorkspaceId [resource id of the log analytics workspace] -Enabled $true
   ```

- 次のコマンドを使用して、Log Analytics ワークスペースのリソース ID を取得できます。

   ```powershell
   (Get-AzOperationalInsightsWorkspace).ResourceId
   ```

このパラメーターを組み合わせて、複数の出力オプションを有効にできます。

### <a name="rest-api"></a>REST API

[Azure Monitor REST API を使用して診断設定を変更する](https://docs.microsoft.com/rest/api/monitor/)方法を学習します。 

### <a name="resource-manager-template"></a>Resource Manager テンプレート

[Resource Manager テンプレートを使用してリソースの作成時に診断設定を有効にする](../azure-monitor/platform/diagnostic-logs-stream-template.md)方法を学習します。 

## <a name="manage-your-logs"></a>ログを管理する

ログは通常、ログをセットアップしてから 2 時間以内に利用できるようになります。 ストレージ アカウントでのログの管理はお客様に委ねられます。

* ログにアクセスできるユーザーを制限することでログのセキュリティを保護するには、標準的な Azure アクセス制御方法を使用します。
* ストレージ アカウントに保持する必要がなくなったログは削除します。
* 古いログをストレージ アカウントから削除するためのリテンション期間を必ず設定してください。

## <a name="view-logs-in-log-analytics-workspace"></a>Log Analytics ワークスペースでログを表示する

メトリックとサーバー イベントは、side-by-side 分析のため、Log Analytics ワークスペース リソースで xEvents と統合されます。 他の Azure サービスからイベントを受信するように Log Analytics ワークスペースを構成して、アーキテクチャ全体の診断ログ データの全体像を提供することもできます。

診断データを表示するには、Log Analytics ワークスペースの左側のメニューで **[ログ]** を開きます。

![Azure Portal の [ログ検索] オプション](./media/analysis-services-logging/aas-logging-open-log-search.png)

クエリ ビルダーで、**LogManagement** > **AzureDiagnostics** を展開します。 AzureDiagnostics には、エンジンとサービスのイベントが含まれています。 クエリが即座に作成されることに注目してください。 EventClass\_s フィールドには、オンプレミスのログ記録の xEvents を使用している場合にはなじみのある、xEvent 名が含まれています。 **[EventClass\_s]** またはイベント名のいずれかをクリックすると、Log Analytics ワークスペースでクエリの作成が続行されます。 後で再利用するため、クエリは必ず保存しておいてください。

### <a name="example-query"></a>クエリの例
このクエリでは、モデル データベースとサーバーに対する各クエリ終了/更新終了イベントの CPU が計算されて返されます。

```Kusto
let window =  AzureDiagnostics
   | where ResourceProvider == "MICROSOFT.ANALYSISSERVICES" and ServerName_s =~"MyServerName" and DatabaseName_s == "Adventure Works Localhost" ;
window
| where OperationName has "QueryEnd" or (OperationName has "CommandEnd" and EventSubclass_s == 38)
| where extract(@"([^,]*)", 1,Duration_s, typeof(long)) > 0
| extend DurationMs=extract(@"([^,]*)", 1,Duration_s, typeof(long))
| extend Engine_CPUTime=extract(@"([^,]*)", 1,CPUTime_s, typeof(long))
| project  StartTime_t,EndTime_t,ServerName_s,OperationName,RootActivityId_g ,TextData_s,DatabaseName_s,ApplicationName_s,Duration_s,EffectiveUsername_s,User_s,EventSubclass_s,DurationMs,Engine_CPUTime
| join kind=leftouter (
window
    | where OperationName == "ProgressReportEnd" or (OperationName == "VertiPaqSEQueryEnd" and EventSubclass_s  != 10) or OperationName == "DiscoverEnd" or (OperationName has "CommandEnd" and EventSubclass_s != 38)
    | summarize sum_Engine_CPUTime = sum(extract(@"([^,]*)", 1,CPUTime_s, typeof(long))) by RootActivityId_g
    ) on RootActivityId_g
| extend totalCPU = sum_Engine_CPUTime + Engine_CPUTime

```


使用できるクエリは数百あります。 クエリについて詳しくは、「[Azure Monitor ログ クエリの使用を開始する](../azure-monitor/log-query/get-started-queries.md)」をご覧ください。


## <a name="turn-on-logging-by-using-powershell"></a>PowerShell を使用してログ記録を有効にする

このクイック チュートリアルでは、Analysis Service サーバーと同じサブスクリプションとリソース グループでストレージ アカウントを作成します。 次に、Set-AzDiagnosticSetting を使用して、診断ログを有効にし、出力を新しいストレージ アカウントに送信します。

### <a name="prerequisites"></a>前提条件
このチュートリアルを完了するには、以下のリソースが必要です。

* 既存の Azure Analysis Services サーバー。 サーバー リソースの作成方法については、[Azure Portal で Azure Analysis Services サーバーを作成する](analysis-services-create-server.md)方法に関するページ、または「[PowerShell を使って Azure Analysis Services サーバーを作成する](analysis-services-create-powershell.md)」を参照してください。

### <a name="aconnect-to-your-subscriptions"></a></a>サブスクリプションへの接続

Azure PowerShell セッションを開始し、次のコマンドで Azure アカウントにサインインします。  

```powershell
Connect-AzAccount
```

ポップアップ ブラウザー ウィンドウで、Azure アカウントのユーザー名とパスワードを入力します。 Azure PowerShell は、このアカウントに関連付けられているすべてのサブスクリプションを取得し、既定で最初のサブスクリプションを使用します。

複数のサブスクリプションをお持ちの場合は、Azure Key Vault を作成するときに使用した特定の 1 つを指定することが必要なことがあります。 アカウントのサブスクリプションを確認するには、次を入力します。

```powershell
Get-AzSubscription
```

ログを記録する Azure Analysis Services アカウントに関連付けられているサブスクリプションを指定するには、次を入力します。

```powershell
Set-AzContext -SubscriptionId <subscription ID>
```

> [!NOTE]
> 複数のサブスクリプションがアカウントに関連付けられている場合は、サブスクリプションを指定することが重要です。
>
>

### <a name="create-a-new-storage-account-for-your-logs"></a>ログ用に新しいストレージ アカウントを作成する

ログの既存のストレージ アカウントがサーバーと同じサブスクリプション内にある場合は、それを使用できます。 このチュートリアルでは、Analysis Services ログ専用の新しいストレージ アカウントを作成します。 便宜上、ストレージ アカウントの詳細を **sa** という変数に格納します。

また、お使いの Analysis Services サーバーを含むリソース グループと同じリソース グループを使用します。 `awsales_resgroup`、`awsaleslogs`、`West Central US` の値を独自の値に置き換えます。

```powershell
$sa = New-AzStorageAccount -ResourceGroupName awsales_resgroup `
-Name awsaleslogs -Type Standard_LRS -Location 'West Central US'
```

### <a name="identify-the-server-account-for-your-logs"></a>ログのサーバー アカウントを特定する

アカウント名を **account** という変数に設定します。この ResourceName は、アカウントの名前です。

```powershell
$account = Get-AzResource -ResourceGroupName awsales_resgroup `
-ResourceName awsales -ResourceType "Microsoft.AnalysisServices/servers"
```

### <a name="enable-logging"></a>ログの有効化

ログ記録を有効にするために、Set-AzDiagnosticSetting コマンドレットを、新しいストレージ アカウント、サーバー アカウント、およびカテゴリの変数と組み合わせて使用します。 次のコマンドを実行し、**-Enabled** フラグを **$true** に設定します。

```powershell
Set-AzDiagnosticSetting  -ResourceId $account.ResourceId -StorageAccountId $sa.Id -Enabled $true -Categories Engine
```

出力は次の例のようになります。

```powershell
StorageAccountId            : 
/subscriptions/a23279b5-xxxx-xxxx-xxxx-47b7c6d423ea/resourceGroups/awsales_resgroup/providers/Microsoft.Storage/storageAccounts/awsaleslogs
ServiceBusRuleId            :
EventHubAuthorizationRuleId :
Metrics                    
    TimeGrain       : PT1M
    Enabled         : False
    RetentionPolicy
    Enabled : False
    Days    : 0


Logs                       
    Category        : Engine
    Enabled         : True
    RetentionPolicy
    Enabled : False
    Days    : 0


    Category        : Service
    Enabled         : False
    RetentionPolicy
    Enabled : False
    Days    : 0


WorkspaceId                 :
Id                          : /subscriptions/a23279b5-xxxx-xxxx-xxxx-47b7c6d423ea/resourcegroups/awsales_resgroup/providers/microsoft.analysisservic
es/servers/awsales/providers/microsoft.insights/diagnosticSettings/service
Name                        : service
Type                        :
Location                    :
Tags                        :
```

この出力を見れば、ログ記録がサーバーに対して有効になっていること、ストレージ アカウントに情報が保存されることを確認できます。

古いログが自動的に削除されるように、ログのアイテム保持ポリシーを設定することもできます。 たとえば、**-RetentionEnabled** フラグを **$true** に設定し、**-RetentionInDays** パラメーターを **90** に設定したアイテム保持ポリシーを設定します。 90 日を経過したログは自動的に削除されます。

```powershell
Set-AzDiagnosticSetting -ResourceId $account.ResourceId`
 -StorageAccountId $sa.Id -Enabled $true -Categories Engine`
  -RetentionEnabled $true -RetentionInDays 90
```

## <a name="next-steps"></a>次の手順

[Azure リソースの診断ログ](../azure-monitor/platform/diagnostic-logs-overview.md)についてさらに詳しく学習します。

PowerShell ヘルプの「[Set-AzDiagnosticSetting](https://docs.microsoft.com/powershell/module/az.monitor/set-azdiagnosticsetting)」を参照してください。
