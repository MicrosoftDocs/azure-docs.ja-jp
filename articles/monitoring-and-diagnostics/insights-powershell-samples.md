---
title: "Azure Monitor の PowerShell クイック スタート サンプル | Microsoft Docs"
description: "PowerShell を使って、自動スケール、アラート、webhook、アクティビティ ログの検索などの Azure Monitor の機能にアクセスします。"
author: kamathashwin
manager: carolz
editor: 
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: c0761814-7148-4ab5-8c27-a2c9fa4cfef5
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/06/2017
ms.author: ashwink
translationtype: Human Translation
ms.sourcegitcommit: 7c28fda22a08ea40b15cf69351e1b0aff6bd0a95
ms.openlocfilehash: 66848eee215732803a1070e13d56f35b17e54975
ms.lasthandoff: 03/07/2017


---
# <a name="azure-monitor-powershell-quick-start-samples"></a>Azure Monitor の PowerShell クイック スタート サンプル
この記事では、Azure Monitor の機能にアクセスするために役立つ PowerShell のサンプル コマンドを紹介します。 Azure Monitor では、Cloud Services、Virtual Machines、Web Apps を自動スケールできます。また、アラート通知の送信や、構成済みのテレメトリ データの値に基づく Web URL の呼び出しも行うことができます。

> [!NOTE]
> Azure Monitor は、2016 年 9 月 25 日まで "Azure Insights" と呼ばれていたサービスの新しい名前です。 ただし、名前空間と、それに伴って以下のコマンドには引き続き "insights" が含まれています。
> 
> 

## <a name="set-up-powershell"></a>PowerShell のセットアップ
コンピューターで実行するために PowerShell をセットアップします (まだセットアップしていない場合)。 詳細については、「 [Azure PowerShell のインストールおよび構成方法](/powershell/azureps-cmdlets-docs) 」をご覧ください。

## <a name="examples-in-this-article"></a>この記事の例
この記事の各例は、Azure Monitor コマンドレットの使用方法を示しています。 「[Azure Monitor (Insights) Cmdlets (Azure Monitor (Insights) コマンドレット)](https://msdn.microsoft.com/library/azure/mt282452#40v=azure.200#41.aspx)」で、Azure Monitor PowerShell コマンドレットのすべてのリストを確認することもできます。

## <a name="sign-in-and-use-subscriptions"></a>サインインとサブスクリプションの使用
まず、Azure サブスクリプションにログインします。

```PowerShell
Login-AzureRmAccount
```

そのためには、サインインする必要があります。 サインインすると、アカウント、テナント ID、既定のサブスクリプション ID が表示されます。 すべての Azure コマンドレットは、既定のサブスクリプションのコンテキストで動作します。 アクセスできるサブスクリプションのリストを表示するには、次のコマンドを使用します。

```PowerShell
Get-AzureRmSubscription
```

作業コンテキストを別のサブスクリプションに変更するには、次のコマンドを使用します。

```PowerShell
Set-AzureRmContext -SubscriptionId <subscriptionid>
```


## <a name="retrieve-activity-log-for-a-subscription"></a>サブスクリプションのアクティビティ ログの取得
`Get-AzureRmLog` コマンドレットを使用します。  一般的な例を以下に示します。

指定した日時のログ エントリを取得します。

```PowerShell
Get-AzureRmLog -StartTime 2016-03-01T10:30
```

範囲内の日時のログ エントリを取得します。

```PowerShell
Get-AzureRmLog -StartTime 2015-01-01T10:30 -EndTime 2015-01-01T11:30
```

特定のリソース グループのログ エントリを取得します。

```PowerShell
Get-AzureRmLog -ResourceGroup 'myrg1'
```

範囲内の日時の特定リソース プロバイダーのログ エントリを取得します。

```PowerShell
Get-AzureRmLog -ResourceProvider 'Microsoft.Web' -StartTime 2015-01-01T10:30 -EndTime 2015-01-01T11:30
```

特定の呼び出し元が含まれたすべてのログ エントリを取得します。

```PowerShell
Get-AzureRmLog -Caller 'myname@company.com'
```

次のコマンドは、アクティビティ ログから最後の 1000 件のイベントを取得します。

```PowerShell
Get-AzureRmLog -MaxEvents 1000
```

`Get-AzureRmLog` は、他にも多くのパラメーターをサポートしています。 詳細については、 `Get-AzureRmLog` のリファレンスをご覧ください。

> [!NOTE]
> `Get-AzureRmLog` は、15 日間の履歴のみを提供します。 **-MaxEvents** パラメーターを使用すると、15 日間を超えて最後の N 件のイベントを照会できます。 15 日より前のイベントにアクセスするには、REST API または SDK (SDK を使用した C# のサンプル) を使用します。 **StartTime** を指定しない場合、既定値は **EndTime** から&1; 時間引いた値になります。 **EndTime**を指定しない場合、既定値は現在の時刻です。 時刻はすべて UTC 形式です。
> 
> 

## <a name="retrieve-alerts-history"></a>アラートの履歴の取得
すべてのアラート イベントを表示するには、次の例を使用して Azure Resource Manager のログを照会します。

```PowerShell
Get-AzureRmLog -Caller "Microsoft.Insights/alertRules" -DetailedOutput -StartTime 2015-03-01
```

特定のアラート ルールの履歴を表示するには、そのアラート ルールのリソース ID を渡して `Get-AzureRmAlertHistory` コマンドレットを使用します。

```PowerShell
Get-AzureRmAlertHistory -ResourceId /subscriptions/s1/resourceGroups/rg1/providers/microsoft.insights/alertrules/myalert -StartTime 2016-03-1 -Status Activated
```

`Get-AzureRmAlertHistory` コマンドレットは、さまざまなパラメーターをサポートしています。 詳細については、「 [Get-AlertHistory](https://msdn.microsoft.com/library/mt282453.aspx)」をご覧ください。

## <a name="retrieve-information-on-alert-rules"></a>アラート ルールに関する情報の取得
以下のコマンドは、いずれも "montest" という名前のリソース グループに影響を及ぼします。

アラート ルールのすべてのプロパティを表示します。

```PowerShell
Get-AzureRmAlertRule -Name simpletestCPU -ResourceGroup montest -DetailedOutput
```

リソース グループに対するすべてのアラートを取得します。

```PowerShell
Get-AzureRmAlertRule -ResourceGroup montest
```

ターゲット リソースに設定されたすべてのアラート ルールを取得します。 たとえば、VM に設定されたすべてのアラート ルールを取得します。

```PowerShell
Get-AzureRmAlertRule -ResourceGroup montest -TargetResourceId /subscriptions/s1/resourceGroups/montest/providers/Microsoft.Compute/virtualMachines/testconfig
```

`Get-AzureRmAlertRule` は、他のパラメーターもサポートしています。 詳細については、「 [Get-AlertRule](https://msdn.microsoft.com/library/mt282459.aspx) 」をご覧ください。

## <a name="create-alert-rules"></a>アラート ルールの作成
`Add-AlertRule` コマンドレットを使用して、アラート ルールを作成、更新、または無効化できます。

電子メール プロパティと webhook プロパティは、それぞれ `New-AzureRmAlertRuleEmail` と `New-AzureRmAlertRuleWebhook` を使用して作成できます。 アラート ルール コマンドレットでは、これらをアラート ルールの **Actions** プロパティにアクションとして割り当てます。

次のセクションには、さまざまなパラメーターを指定してアラート ルールを作成する方法を示すサンプルが記載されています。

### <a name="alert-rule-on-a-metric"></a>メトリックのアラート ルール
メトリックを使用するアラートを作成する際に使用されるパラメーターと値を次の表に示します。

| パラメーター | 値 |
| --- | --- |
| 名前 |simpletestdiskwrite |
| このアラート ルールの場所 |米国東部 |
| ResourceGroup |montest |
| TargetResourceId |/subscriptions/s1/resourceGroups/montest/providers/Microsoft.Compute/virtualMachines/testconfig |
| 作成されたアラートの MetricName |\PhysicalDisk(_Total)\Disk Writes/sec. 正確なメトリック名を取得する方法については、後述の `Get-MetricDefinitions` コマンドレットをご覧ください。 |
| operator |GreaterThan |
| しきい値 (このメトリックの場合、数/秒) |1 |
| WindowSize (hh:mm:ss 形式) |00:05:00 |
| アグリゲーター (メトリックの統計。この例では Average を使用) |平均 |
| カスタム電子メール (文字列配列) |'foo@example.com'、'bar@example.com' |
| 所有者、共同作成者、および閲覧者への電子メールの送信 |-SendToServiceOwners |

Email アクションを作成する

```PowerShell
$actionEmail = New-AzureRmAlertRuleEmail -CustomEmail myname@company.com
```

Webhook アクションを作成する

```PowerShell
$actionWebhook = New-AzureRmAlertRuleWebhook -ServiceUri https://example.com?token=mytoken
```

クラシック VM の CPU 使用率メトリックのアラート ルールを作成する

```PowerShell
Add-AzureRmMetricAlertRule -Name vmcpu_gt_1 -Location "East US" -ResourceGroup myrg1 -TargetResourceId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.ClassicCompute/virtualMachines/my_vm1 -MetricName "Percentage CPU" -Operator GreaterThan -Threshold 1 -WindowSize 00:05:00 -TimeAggregationOperator Average -Actions $actionEmail, $actionWebhook -Description "alert on CPU > 1%"
```

アラート ルールを取得する

```PowerShell
Get-AzureRmAlertRule -Name vmcpu_gt_1 -ResourceGroup myrg1 -DetailedOutput
```

Add アラート コマンドレットは、指定されたプロパティのアラート ルールが既に存在する場合に、そのルールの更新も実行します。 アラート ルールを無効にするには、 **-DisableRule**パラメーターを含めます。

### <a name="alert-on-activity-log-event"></a>アクティビティ ログのイベントのアラート
> [!NOTE]
> この機能はプレビュー段階で、今後ある時点で削除されます (置き換えられます)。
> 
> 

このシナリオでは、リソース グループ *abhingrgtest123*において、ユーザーのサブスクリプションで Web サイトが正常に開始されたときに電子メールを送信します。

電子メール ルールを設定する

```PowerShell
$actionEmail = New-AzureRmAlertRuleEmail -CustomEmail myname@company.com
```

Webhook ルールを設定する

```PowerShell
$actionWebhook = New-AzureRmAlertRuleWebhook -ServiceUri https://example.com?token=mytoken
```

イベントのルールを作成する

```PowerShell
Add-AzureRmLogAlertRule -Name superalert1 -Location "East US" -ResourceGroup myrg1 -OperationName microsoft.web/sites/start/action -Status Succeeded -TargetResourceGroup abhingrgtest123 -Actions $actionEmail, $actionWebhook
```

アラート ルールを取得する

```PowerShell
Get-AzureRmAlertRule -Name superalert1 -ResourceGroup myrg1 -DetailedOutput
```

`Add-AlertRule` コマンドレットでは、他にもさまざまなパラメーターを使用できます。 詳細については、「 [Add-AlertRule](https://msdn.microsoft.com/library/mt282468.aspx)」を参照してください。

## <a name="get-a-list-of-available-metrics-for-alerts"></a>アラートで使用可能なメトリックのリストの取得
`Get-AzureRmMetricDefinition` コマンドレットを使用して、特定のリソースのすべてのメトリックのリストを表示できます。

```PowerShell
Get-AzureRmMetricDefinition -ResourceId <resource_id>
```

次の例では、メトリックの名前 (Name) と単位 (Unit) を含むテーブルを生成します。

```PowerShell
Get-AzureRmMetricDefinition -ResourceId <resource_id> | Format-Table -Property Name,Unit
```

`Get-AzureRmMetricDefinition` で使用できるオプションの詳細な一覧については、「 [Get-MetricDefinitions](https://msdn.microsoft.com/library/mt282458.aspx)」を参照してください。

## <a name="create-and-manage-autoscale-settings"></a>自動スケール設定の作成と管理
Web Apps、VM、Cloud Services、VM Scale Set などのリソースは、そのリソース用に構成された自動スケール設定を&1; つだけ使用できます。
ただし、各自動スケール設定では複数のプロファイルを使用できます。 たとえば、パフォーマンス ベースのスケール プロファイルを使用し、2 つ目のプロファイルとしてスケジュール ベースのプロファイルを使用できます。 各プロファイルには、複数のルールを構成できます。 自動スケールの詳細については、 [アプリケーションの自動スケールの方法](../cloud-services/cloud-services-how-to-scale.md)に関する記事を参照してください。

ここでは、次の手順を使用します。

1. ルールを作成します。
2. 前の手順で作成したルールをプロファイルにマッピングしてプロファイルを作成します。
3. 省略可能: Webhook プロパティと電子メール プロパティを構成して、自動スケールの通知を作成します。
4. これまでの手順で作成したプロファイルと通知をマッピングし、ターゲット リソースでの名前を指定して自動スケール設定を作成します。

以下の例は、CPU 使用率メトリックを使用して、Windows オペレーティング システム ベースの VM スケール セットの自動スケール設定を作成する方法を示しています。

まず、インスタンス数を増やしてスケールアウトするルールを作成します。

```PowerShell
$rule1 = New-AzureRmAutoscaleRule -MetricName "Percentage CPU" -MetricResourceId /subscriptions/s1/resourceGroups/big2/providers/Microsoft.Compute/virtualMachineScaleSets/big2 -Operator GreaterThan -MetricStatistic Average -Threshold 60 -TimeGrain 00:01:00 -TimeWindow 00:10:00 -ScaleActionCooldown 00:10:00 -ScaleActionDirection Increase -ScaleActionValue 1
```        

次に、インスタンス数を減らしてスケールインするルールを作成します。

```PowerShell
$rule2 = New-AzureRmAutoscaleRule -MetricName "Percentage CPU" -MetricResourceId /subscriptions/s1/resourceGroups/big2/providers/Microsoft.Compute/virtualMachineScaleSets/big2 -Operator GreaterThan -MetricStatistic Average -Threshold 30 -TimeGrain 00:01:00 -TimeWindow 00:10:00 -ScaleActionCooldown 00:10:00 -ScaleActionDirection Decrease -ScaleActionValue 1
```

ルールのプロファイルを作成します。

```PowerShell
$profile1 = New-AzureRmAutoscaleProfile -DefaultCapacity 2 -MaximumCapacity 10 -MinimumCapacity 2 -Rules $rule1,$rule2 -Name "My_Profile"
```

Webhook プロパティを作成します。

```PowerShell
$webhook_scale = New-AzureRmAutoscaleWebhook -ServiceUri "https://example.com?mytoken=mytokenvalue"
```

前に作成した電子メールと Webhook を含めて、自動スケール設定の通知プロパティを作成します。

```PowerShell
$notification1= New-AzureRmAutoscaleNotification -CustomEmails ashwink@microsoft.com -SendEmailToSubscriptionAdministrators SendEmailToSubscriptionCoAdministrators -Webhooks $webhook_scale
```

最後に、自動スケール設定を作成して、上記で作成したプロファイルを追加します。

```PowerShell
Add-AzureRmAutoscaleSetting -Location "East US" -Name "MyScaleVMSSSetting" -ResourceGroup big2 -TargetResourceId /subscriptions/s1/resourceGroups/big2/providers/Microsoft.Compute/virtualMachineScaleSets/big2 -AutoscaleProfiles $profile1 -Notifications $notification1
```

自動スケール設定の管理の詳細については、「 [Get-AutoscaleSetting](https://msdn.microsoft.com/library/mt282461.aspx)」を参照してください。

## <a name="autoscale-history"></a>自動スケールの履歴
次の例は、最近の自動スケール イベントとアラート イベントを表示する方法を示しています。 アクティビティ ログの検索を使用して、自動スケールの履歴を表示します。

```PowerShell
Get-AzureRmLog -Caller "Microsoft.Insights/autoscaleSettings" -DetailedOutput -StartTime 2015-03-01
```

`Get-AzureRmAutoScaleHistory` コマンドレットを使用して、自動スケールの履歴を取得できます。

```PowerShell
Get-AzureRmAutoScaleHistory -ResourceId /subscriptions/s1/resourceGroups/myrg1/providers/microsoft.insights/autoscalesettings/myScaleSetting -StartTime 2016-03-15 -DetailedOutput
```

詳細については、「 [Get-AutoscaleHistory](https://msdn.microsoft.com/library/mt282464.aspx)」を参照してください。

### <a name="view-details-for-an-autoscale-setting"></a>自動スケール設定の詳細の表示
`Get-Autoscalesetting` コマンドレットを使用して、自動スケール設定の詳細を取得できます。

次の例では、リソース グループ "myrg1" のすべての自動スケール設定の詳細を表示します。

```PowerShell
Get-AzureRmAutoscalesetting -ResourceGroup myrg1 -DetailedOutput
```

次の例では、リソース グループ "myrg1" の自動スケール設定の詳細を表示します。具体的には、"MyScaleVMSSSetting" という名前の自動スケール設定の詳細を表示します。

```PowerShell
Get-AzureRmAutoscalesetting -ResourceGroup myrg1 -Name MyScaleVMSSSetting -DetailedOutput
```

### <a name="remove-an-autoscale-setting"></a>自動スケール設定の削除
`Remove-Autoscalesetting` コマンドレットを使用して、自動スケール設定を削除できます。

```PowerShell
Remove-AzureRmAutoscalesetting -ResourceGroup myrg1 -Name MyScaleVMSSSetting
```

## <a name="manage-log-profiles-for-activity-log"></a>アクティビティ ログのログ プロファイルの管理
"*ログ プロファイル*" を作成し、アクティビティ ログのデータをストレージ アカウントにエクスポートできます。また、ストレージ アカウントのデータ リテンション期間を構成することもできます。 必要に応じて、データをイベント ハブにストリーミングすることもできます。 この機能は現在プレビュー段階にあり、サブスクリプションごとに作成できるログ プロファイルは&1; つに限られます。 現在のサブスクリプションで以下のコマンドレットを使用して、ログ プロファイルを作成し、管理できます。 また、特定のサブスクリプションを選択することもできます。 PowerShell では現在のサブスクリプションが既定で使用されますが、`Set-AzureRmContext` を使用してサブスクリプションをいつでも変更できます。 そのサブスクリプション内の任意のストレージ アカウントまたはイベント ハブにデータをルーティングするようにアクティビティ ログを構成できます。 データは、JSON 形式で BLOB ファイルとして書き込まれます。

### <a name="get-a-log-profile"></a>ログ プロファイルの取得
既存のログ プロファイルを取得するには、 `Get-AzureRmLogProfile` コマンドレットを使用します。

### <a name="add-a-log-profile-without-data-retention"></a>データ リテンション期間を指定しないログ プロファイルの追加
```PowerShell
Add-AzureRmLogProfile -Name my_log_profile_s1 -StorageAccountId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.Storage/storageAccounts/my_storage -Locations global,westus,eastus,northeurope,westeurope,eastasia,southeastasia,japaneast,japanwest,northcentralus,southcentralus,eastus2,centralus,australiaeast,australiasoutheast,brazilsouth,centralindia,southindia,westindia
```

### <a name="remove-a-log-profile"></a>ログ プロファイルの削除
```PowerShell
Remove-AzureRmLogProfile -name my_log_profile_s1
```

### <a name="add-a-log-profile-with-data-retention"></a>データ リテンション期間を指定したログ プロファイルの追加
データを保持する日数を正の整数で指定して、 **-RetentionInDays** プロパティを指定できます。

```PowerShell
Add-AzureRmLogProfile -Name my_log_profile_s1 -StorageAccountId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.Storage/storageAccounts/my_storage -Locations global,westus,eastus,northeurope,westeurope,eastasia,southeastasia,japaneast,japanwest,northcentralus,southcentralus,eastus2,centralus,australiaeast,australiasoutheast,brazilsouth,centralindia,southindia,westindia -RetentionInDays 90
```

### <a name="add-log-profile-with-retention-and-eventhub"></a>リテンション期間とイベント ハブを指定したログ プロファイルの追加
データは、ストレージ アカウントにルーティングするだけでなく、イベント ハブにストリーミングすることもできます。 このプレビュー リリースでは、ストレージ アカウント構成は必須ですが、イベント ハブ構成は省略可能です。

```PowerShell
Add-AzureRmLogProfile -Name my_log_profile_s1 -StorageAccountId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.Storage/storageAccounts/my_storage -serviceBusRuleId /subscriptions/s1/resourceGroups/Default-ServiceBus-EastUS/providers/Microsoft.ServiceBus/namespaces/mytestSB/authorizationrules/RootManageSharedAccessKey -Locations global,westus,eastus,northeurope,westeurope,eastasia,southeastasia,japaneast,japanwest,northcentralus,southcentralus,eastus2,centralus,australiaeast,australiasoutheast,brazilsouth,centralindia,southindia,westindia -RetentionInDays 90
```

## <a name="configure-diagnostics-logs"></a>診断ログの構成
多数の Azure サービスで追加のログとテレメトリが提供されます。Azure ストレージ アカウントへのデータの保存、Event Hubs への送信、OMS Log Analytics ワークスペースへの送信を行うようにこれらのサービスを構成できます。 この操作はリソース レベルでのみ実行できます。ストレージ アカウントまたはイベント ハブは、診断設定が構成されているターゲット リソースと同じリージョンに存在する必要があります。

### <a name="get-diagnostic-setting"></a>診断設定の取得
```PowerShell
Get-AzureRmDiagnosticSetting -ResourceId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.Logic/workflows/andy0315logicapp
```

診断設定の無効化

```PowerShell
Set-AzureRmDiagnosticSetting -ResourceId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.Logic/workflows/andy0315logicapp -StorageAccountId /subscriptions/s1/resourceGroups/Default-Storage-WestUS/providers/Microsoft.Storage/storageAccounts/mystorageaccount -Enable $false
```

リテンション期間を指定せずに診断設定を有効にする

```PowerShell
Set-AzureRmDiagnosticSetting -ResourceId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.Logic/workflows/andy0315logicapp -StorageAccountId /subscriptions/s1/resourceGroups/Default-Storage-WestUS/providers/Microsoft.Storage/storageAccounts/mystorageaccount -Enable $true
```

リテンション期間を指定して診断設定を有効にする

```PowerShell
Set-AzureRmDiagnosticSetting -ResourceId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.Logic/workflows/andy0315logicapp -StorageAccountId /subscriptions/s1/resourceGroups/Default-Storage-WestUS/providers/Microsoft.Storage/storageAccounts/mystorageaccount -Enable $true -RetentionEnabled $true -RetentionInDays 90
```

特定のログ カテゴリのリテンション期間を指定して診断設定を有効にする

```PowerShell
Set-AzureRmDiagnosticSetting -ResourceId /subscriptions/s1/resourceGroups/insights-integration/providers/Microsoft.Network/networkSecurityGroups/viruela1 -StorageAccountId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.Storage/storageAccounts/sakteststorage -Categories NetworkSecurityGroupEvent -Enable $true -RetentionEnabled $true -RetentionInDays 90
```

Event Hubs の診断設定を有効にする

```PowerShell
Set-AzureRmDiagnosticSetting -ResourceId /subscriptions/s1/resourceGroups/insights-integration/providers/Microsoft.Network/networkSecurityGroups/viruela1 -serviceBusRuleId /subscriptions/s1/resourceGroups/Default-ServiceBus-EastUS/providers/Microsoft.ServiceBus/namespaces/mytestSB/authorizationrules/RootManageSharedAccessKey -Enable $true
```

OMS の診断設定を有効にする

```PowerShell
Set-AzureRmDiagnosticSetting -ResourceId /subscriptions/s1/resourceGroups/insights-integration/providers/Microsoft.Network/networkSecurityGroups/viruela1 -WorkspaceId 76d785fd-d1ce-4f50-8ca3-858fc819ca0f -Enabled $true

```

