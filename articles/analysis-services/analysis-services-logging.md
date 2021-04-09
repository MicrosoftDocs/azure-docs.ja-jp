---
title: Azure Analysis Services の診断ログ | Microsoft Docs
description: ログを設定して Azure Analysis Services サーバーを監視する方法について説明します。
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 05/19/2020
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 2bee856adef1208aabbe65ecd5fd11235579bb82
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "100582711"
---
# <a name="setup-diagnostic-logging"></a>診断ログのセットアップ

Analysis Services ソリューションの重要な部分は、サーバーのパフォーマンスを監視することです。 Azure Analysis Services は Azure Monitor と統合されています。 [Azure Monitor のリソース ログ](../azure-monitor/essentials/platform-logs-overview.md)を使用すると、ログを監視して [Azure Storage](https://azure.microsoft.com/services/storage/) に送信したり、ログを [Azure Event Hubs](https://azure.microsoft.com/services/event-hubs/) にストリーミング配信したり、[Azure Monitor ログ](../azure-monitor/overview.md)にエクスポートしたりすることができます。

![Storage、Event Hubs、または Azure Monitor ログへのリソースのログ記録](./media/analysis-services-logging/aas-logging-overview.png)

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="whats-logged"></a>ログに記録されるもの

**[エンジン]** 、 **[サービス]** 、および **[メトリック]** のカテゴリを選択できます。

### <a name="engine"></a>エンジン

**[エンジン]** を選択すると、すべての [xEvents](/analysis-services/instances/monitor-analysis-services-with-sql-server-extended-events) がログに記録されます。 個々のイベントを選択することはできません。 

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
|コマンド     |  Command Begin       |
|コマンド     |  Command End       |
|エラーと警告     |   エラー      |
|発見     |   Discover End      |
|Notification     |    Notification     |
|Session     |  Session Initialize       |
|Locks    |  Deadlock       |
|クエリ処理     |   VertiPaq SE Query Begin      |
|クエリ処理     |   VertiPaq SE Query End      |
|クエリ処理     |   VertiPaq SE Query Cache Match      |
|クエリ処理     |   Direct Query Begin      |
|クエリ処理     |  Direct Query End       |

### <a name="service"></a>サービス

|操作の名前  |発生するタイミング  |
|---------|---------|
|ResumeServer     |    サーバーを再開するとき     |
|SuspendServer    |   サーバーを一時停止するとき      |
|DeleteServer     |    サーバーの削除     |
|RestartServer    |     ユーザーが SSMS または PowerShell を経由してサーバーを再起動するとき    |
|GetServerLogFiles    |    ユーザーが PowerShell を通じてサーバー ログをエクスポートするとき     |
|ExportModel     |   ユーザーが [Visual Studio で開く] を使用してポータルでモデルをエクスポートするとき     |

### <a name="all-metrics"></a>すべてのメトリック

[メトリック] カテゴリでは、同じ[サーバー メトリック](analysis-services-monitor.md#server-metrics)が AzureMetrics テーブルに記録されます。 クエリの [スケールアウト](analysis-services-scale-out.md)を使用していて、読み取りレプリカごとにメトリックを分離する必要がある場合は、代わりに AzureDiagnostics テーブルを使用します。この **OperationName** は **LogMetric** と同じです。

## <a name="setup-diagnostics-logging"></a>診断ログのセットアップ

### <a name="azure-portal"></a>Azure portal

1. [Azure portal](https://portal.azure.com) > サーバーで、左のナビゲーションにある **[診断設定]** をクリックし、 **[診断をオンにする]** をクリックします。

    ![Azure portal で Azure Cosmos DB のリソースのログ記録を有効にする](./media/analysis-services-logging/aas-logging-turn-on-diagnostics.png)

2. **[診断設定]** で、次のオプションを指定します。 

    * **名前**。 作成するログの名前を入力します。

    * **[ストレージ アカウントへのアーカイブ]** 。 このオプションを使用するには、接続先として既存のストレージ アカウントが必要です。 「[ストレージ アカウントを作成する](../storage/common/storage-account-create.md)」を参照してください。 指示に従って、Resource Manager の汎用アカウントを作成し、ポータルのこのページに戻ってストレージ アカウントを選択します。 新しく作成されたストレージ アカウントがドロップダウン メニューに表示されるまでには、数分かかる場合があります。
    * **イベント ハブにストリーミングします**。 このオプションを使用するには、既存の Event Hubs 名前空間と接続先のイベント ハブが必要です。 詳細については、「[Azure Portal を使用して Event Hubs 名前空間とイベント ハブを作成する](../event-hubs/event-hubs-create.md)」をご覧ください。 Portal でこのページに戻り、Event Hubs 名前空間とポリシー名を選択します。
    * **Azure Monitor (Log Analytics ワークスペース) に送信します**。 このオプションを使用するには、既存のワークスペースを使用するか、またはポータルで[新しいワークスペース リソースを作成](../azure-monitor/logs/quick-create-workspace.md)します。 ログを表示する方法について詳しくは、この記事の「[Log Analytics ワークスペースでログを表示する](#view-logs-in-log-analytics-workspace)」をご覧ください。

    * **エンジン**。 xEvents をログ記録するには、このオプションを選択します。 ストレージ アカウントにアーカイブする場合、リソース ログの保持期間を選択できます。 リテンション期間が過ぎると、ログは自動的に削除されます。
    * **サービス**。 サービス レベル イベントをログ記録するには、このオプションを選択します。 ストレージ アカウントにアーカイブする場合、リソース ログの保持期間を選択できます。 リテンション期間が過ぎると、ログは自動的に削除されます。
    * **メトリック**。 [メトリック](analysis-services-monitor.md#server-metrics)に詳細データを保存するには、このオプションを使用します。 ストレージ アカウントにアーカイブする場合、リソース ログの保持期間を選択できます。 リテンション期間が過ぎると、ログは自動的に削除されます。

3. **[保存]** をクリックします。

    "\<workspace name> の診断を更新できませんでした。 サブスクリプション \<subscription id> は microsoft.insights を使用するために登録されていません。" というエラーが表示される場合は、[Azure Diagnostics のトラブルシューティング](../azure-monitor/essentials/resource-logs.md)に関する指示に従ってアカウントを登録してから、この手順を再試行してください。

    今後、リソース ログを保存する方法を変更する場合は、このページに戻って設定を変更できます。

### <a name="powershell"></a>PowerShell

興味を引く基本的なコマンドを次に示します。 PowerShell を使用してストレージ アカウントにログ記録をセットアップする詳細な手順が必要な場合は、この記事で後述するチュートリアルを参照してください。

PowerShell を使用してメトリックとリソースのログ記録を有効にするには、次のコマンドを使用します。

- ストレージ アカウントでのリソース ログの保存を有効にするには、次のコマンドを使います。

   ```powershell
   Set-AzDiagnosticSetting -ResourceId [your resource id] -StorageAccountId [your storage account id] -Enabled $true
   ```

   ストレージ アカウント ID は、ログの送信先となるストレージ アカウントのリソース ID です。

- イベント ハブへのリソース ログのストリーミングを有効にするには、次のコマンドを使います。

   ```powershell
   Set-AzDiagnosticSetting -ResourceId [your resource id] -ServiceBusRuleId [your service bus rule id] -Enabled $true
   ```

   Azure Service Bus ルール ID は、次の形式の文字列です。

   ```powershell
   {service bus resource ID}/authorizationrules/{key name}
   ``` 

- Log Analytics ワークスペースへのリソース ログの送信を有効にするには、次のコマンドを使用します。

   ```powershell
   Set-AzDiagnosticSetting -ResourceId [your resource id] -WorkspaceId [resource id of the log analytics workspace] -Enabled $true
   ```

- 次のコマンドを使用して、Log Analytics ワークスペースのリソース ID を取得できます。

   ```powershell
   (Get-AzOperationalInsightsWorkspace).ResourceId
   ```

このパラメーターを組み合わせて、複数の出力オプションを有効にできます。

### <a name="rest-api"></a>REST API

[Azure Monitor の REST API を使用して診断設定を変更する](/rest/api/monitor/)方法を学習します。 

### <a name="resource-manager-template"></a>Resource Manager テンプレート

[Resource Manager テンプレートを使用してリソースの作成時に診断設定を有効にする](../azure-monitor/essentials/resource-manager-diagnostic-settings.md)方法を学習します。 

## <a name="manage-your-logs"></a>ログを管理する

ログは通常、ログをセットアップしてから 2 時間以内に利用できるようになります。 ストレージ アカウントでのログの管理はお客様に委ねられます。

* 標準的な Azure アクセス制御メソッドを使用してアクセスできるユーザーを制限することで、ログをセキュリティで保護します。
* ストレージ アカウントに保持する必要がなくなったログは削除します。
* 古いログをストレージ アカウントから削除するためのリテンション期間を必ず設定してください。

## <a name="view-logs-in-log-analytics-workspace"></a>Log Analytics ワークスペースでログを表示する

メトリックとサーバー イベントは、side-by-side 分析のため、Log Analytics ワークスペース リソースで xEvents と統合されます。 他の Azure サービスからイベントを受信するように Log Analytics ワークスペースを構成して、アーキテクチャ全体の診断ログ データの全体像を提供することもできます。

診断データを表示するには、Log Analytics ワークスペースの左側のメニューで **[ログ]** を開きます。

![Azure Portal の [ログ検索] オプション](./media/analysis-services-logging/aas-logging-open-log-search.png)

クエリ ビルダーで、**LogManagement** > **AzureDiagnostics** を展開します。 AzureDiagnostics には、エンジンとサービスのイベントが含まれています。 クエリが即座に作成されることに注目してください。 EventClass\_s フィールドには、オンプレミスのログ記録の xEvents を使用している場合にはなじみのある、xEvent 名が含まれています。 **[EventClass\_s]** またはイベント名のいずれかをクリックすると、Log Analytics ワークスペースでクエリの作成が続行されます。 後で再利用するため、クエリは必ず保存しておいてください。

### <a name="example-queries"></a>クエリの例

#### <a name="example-1"></a>例 1

次のクエリでは、モデル データベースとサーバーの各クエリの終了/更新終了イベントの期間が返されます。 スケールアウトした場合、レプリカ番号は ServerName_s に含まれているので、結果はレプリカごとに分割されます。 RootActivityId_g によるグループ化で Azure Diagnostics REST API から取得される行数が減り、[Log Analytics のレート制限](https://dev.loganalytics.io/documentation/Using-the-API/Limits)に関する記事で説明されている制限内に収めることができます。

```Kusto
let window = AzureDiagnostics
   | where ResourceProvider == "MICROSOFT.ANALYSISSERVICES" and Resource =~ "MyServerName" and DatabaseName_s =~ "MyDatabaseName" ;
window
| where OperationName has "QueryEnd" or (OperationName has "CommandEnd" and EventSubclass_s == 38)
| where extract(@"([^,]*)", 1,Duration_s, typeof(long)) > 0
| extend DurationMs=extract(@"([^,]*)", 1,Duration_s, typeof(long))
| project  StartTime_t,EndTime_t,ServerName_s,OperationName,RootActivityId_g,TextData_s,DatabaseName_s,ApplicationName_s,Duration_s,EffectiveUsername_s,User_s,EventSubclass_s,DurationMs
| order by StartTime_t asc
```

#### <a name="example-2"></a>例 2

次のクエリでは、サーバーのメモリと QPU の消費量が返されます。 スケールアウトした場合、レプリカ番号は ServerName_s に含まれているので、結果はレプリカごとに分割されます。

```Kusto
let window = AzureDiagnostics
   | where ResourceProvider == "MICROSOFT.ANALYSISSERVICES" and Resource =~ "MyServerName";
window
| where OperationName == "LogMetric" 
| where name_s == "memory_metric" or name_s == "qpu_metric"
| project ServerName_s, TimeGenerated, name_s, value_s
| summarize avg(todecimal(value_s)) by ServerName_s, name_s, bin(TimeGenerated, 1m)
| order by TimeGenerated asc 
```

#### <a name="example-3"></a>例 3

次のクエリでは、サーバーの Rows read/sec Analysis Services エンジン パフォーマンス カウンターが返されます。

```Kusto
let window =  AzureDiagnostics
   | where ResourceProvider == "MICROSOFT.ANALYSISSERVICES" and Resource =~ "MyServerName";
window
| where OperationName == "LogMetric" 
| where parse_json(tostring(parse_json(perfobject_s).counters))[0].name == "Rows read/sec" 
| extend Value = tostring(parse_json(tostring(parse_json(perfobject_s).counters))[0].value) 
| project ServerName_s, TimeGenerated, Value
| summarize avg(todecimal(Value)) by ServerName_s, bin(TimeGenerated, 1m)
| order by TimeGenerated asc 
```

使用できるクエリは数百あります。 クエリについて詳しくは、「[Azure Monitor ログ クエリの使用を開始する](../azure-monitor/logs/get-started-queries.md)」をご覧ください。


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

ログ記録を有効にするために、Set-AzDiagnosticSetting コマンドレットを、新しいストレージ アカウント、サーバー アカウント、およびカテゴリの変数と組み合わせて使用します。 次のコマンドを実行し、 **-Enabled** フラグを **$true** に設定します。

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

古いログが自動的に削除されるように、ログのアイテム保持ポリシーを設定することもできます。 たとえば、 **-RetentionEnabled** フラグを **$true** に設定し、 **-RetentionInDays** パラメーターを **90** に設定したアイテム保持ポリシーを設定します。 90 日を経過したログは自動的に削除されます。

```powershell
Set-AzDiagnosticSetting -ResourceId $account.ResourceId`
 -StorageAccountId $sa.Id -Enabled $true -Categories Engine`
  -RetentionEnabled $true -RetentionInDays 90
```

## <a name="next-steps"></a>次のステップ

[Azure Monitor のリソースのログ記録](../azure-monitor/essentials/platform-logs-overview.md)の詳細について確認します。

PowerShell ヘルプの「[Set-AzDiagnosticSetting](/powershell/module/az.monitor/set-azdiagnosticsetting)」を参照してください。