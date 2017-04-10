---
title: "OMS Log Analytics への Azure Automation ジョブ データの転送 | Microsoft Docs"
description: "この記事では、ジョブの状態と Runbook ジョブ ストリームを Microsoft Operations Management Suite Log Analytics に送信して、詳細な情報の入手ときめ細かい管理を実現する方法について説明します。"
services: automation
documentationcenter: 
author: MGoedtel
manager: jwhit
editor: tysonn
ms.assetid: c12724c6-01a9-4b55-80ae-d8b7b99bd436
ms.service: automation
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/03/2017
ms.author: magoedte
translationtype: Human Translation
ms.sourcegitcommit: b4802009a8512cb4dcb49602545c7a31969e0a25
ms.openlocfilehash: 5cfbd39d2f66fb6632495eb7cd789ed39b0cc309
ms.lasthandoff: 03/29/2017


---
# <a name="forward-job-status-and-job-streams-from-automation-to-log-analytics-oms"></a>Automation から Log Analytics (OMS) へのジョブの状態とジョブ ストリームの転送
Automation からは、Runbook ジョブの状態とジョブ ストリームを Microsoft Operations Management Suite (OMS) Log Analytics ワークスペースに送信できます。  ジョブ ログとジョブ ストリームは、Azure Portal または PowerShell を使用してジョブごとに表示できます。これを使用して、簡単な調査を行うことができます。 Log Analytics では、次のことが可能になりました。

* Automation ジョブに関する情報を得る
* Runbook ジョブの状態 (失敗、中断など) に基づいて電子メールまたはアラートをトリガーする
* ジョブ ストリームをまたぐ高度なクエリを記述する
* Automation アカウントをまたいでジョブどうしを関連付ける
* ジョブの履歴を時系列で視覚化する     

## <a name="prerequisites-and-deployment-considerations"></a>前提条件とデプロイに関する考慮事項
Log Analytics への Automation ログの送信を開始するには、次のものが必要です。

1. 2016 年 11 月以降のリリースの [Azure PowerShell](https://docs.microsoft.com/powershell/azureps-cmdlets-docs/) (v2.3.0)。
2. Log Analytics ワークスペース。 詳細については、「[Log Analytics の起動と開始](../log-analytics/log-analytics-get-started.md)」を参照してください。
3. Azure Automation アカウントの ResourceId

Azure Automation アカウントと Log Analytics ワークスペースの ResourceId を調べるには、次の PowerShell を実行します。

```powershell
# Find the ResourceId for the Automation Account
Find-AzureRmResource -ResourceType "Microsoft.Automation/automationAccounts"

# Find the ResourceId for the Log Analytics workspace
Find-AzureRmResource -ResourceType "Microsoft.OperationalInsights/workspaces"
```

複数の Automation アカウントまたはワークスペースがある場合、前のコマンドの出力内で、構成する必要がある "*名前*" を見つけ、*ResourceId* 用にその値をコピーします。

Automation アカウントの "*名前*" の値を調べる必要がある場合、Azure Portal の **[Automation アカウント]** ブレードから Automation アカウントを選択し、**[すべての設定]** を選択します。  **[すべての設定]** ブレードで、**[アカウント設定]** の **[プロパティ]** を選択します。  **[プロパティ]** ブレードで、値をメモすることができます。<br> ![Automation Account properties](media/automation-manage-send-joblogs-log-analytics/automation-account-properties.png)に関するページを参照してください。

## <a name="set-up-integration-with-log-analytics"></a>Log Analytics との統合のセットアップ
1. コンピューターの**スタート**画面から、**Windows PowerShell** を起動します。  
2. 次の PowerShell をコピーして貼り付け、`$workspaceId` と `$automationAccountId` の値を編集します。  `-Environment` パラメーターについては、作業しているクラウド環境に応じて有効な値が *Azure Cloud* または *AzureUSGovernment* になります。     

```powershell
[cmdletBinding()]
    Param
    (
        [Parameter(Mandatory=$True)]
        [ValidateSet("AzureCloud","AzureUSGovernment")]
        [string]$Environment="AzureCloud"
    )

#Check to see which cloud environment to sign into.
Switch ($Environment)
   {
       "AzureCloud" {Login-AzureRmAccount}
       "AzureUSGovernment" {Login-AzureRmAccount -EnvironmentName AzureUSGovernment}
   }

# if you have one Log Analytics workspace you can use the following command to get the resource id of the workspace
$workspaceId = (Get-AzureRmOperationalInsightsWorkspace).ResourceId

$automationAccountId = "/SUBSCRIPTIONS/ec11ca60-1234-491e-5678-0ea07feae25c/RESOURCEGROUPS/DEMO/PROVIDERS/MICROSOFT.AUTOMATION/ACCOUNTS/DEMO"

Set-AzureRmDiagnosticSetting -ResourceId $automationAccountId -WorkspaceId $workspaceId -Enabled $true

```

このスクリプトを実行すると、Log Analytics に 10 分以内に新しいジョブ ログやジョブ ストリームのレコードが書き込まれます。

ログを表示するには、次のクエリを実行します。`Type=AzureDiagnostics ResourceProvider="MICROSOFT.AUTOMATION"`

### <a name="verify-configuration"></a>構成の確認
Automation アカウントから Log Analytics ワークスペースにログが送信されていることを確認するには、次の PowerShell を使用して、Automation アカウントに診断が正しく設定されていることを確認します。

```powershell
[cmdletBinding()]
    Param
    (
        [Parameter(Mandatory=$True)]
        [ValidateSet("AzureCloud","AzureUSGovernment")]
        [string]$Environment="AzureCloud"
    )

#Check to see which cloud environment to sign into.
Switch ($Environment)
   {
       "AzureCloud" {Login-AzureRmAccount}
       "AzureUSGovernment" {Login-AzureRmAccount -EnvironmentName AzureUSGovernment}
   }
# if you have one Log Analytics workspace you can use the following command to get the resource id of the workspace
$workspaceId = (Get-AzureRmOperationalInsightsWorkspace).ResourceId

$automationAccountId = "/SUBSCRIPTIONS/ec11ca60-1234-491e-5678-0ea07feae25c/RESOURCEGROUPS/DEMO/PROVIDERS/MICROSOFT.AUTOMATION/ACCOUNTS/DEMO"

Get-AzureRmDiagnosticSetting -ResourceId $automationAccountId
```

出力で次の点を確認します。
+ *Logs* の *Enabled* の値が *True* である
+ *WorkspaceId* の値が、Log Analytics ワークスペースの ResourceId に設定されている


## <a name="log-analytics-records"></a>Log Analytics のレコード
Azure Automation の診断から、Log Analytics に 2 種類のレコードが作成されます。

### <a name="job-logs"></a>ジョブ ログ
| プロパティ | 説明 |
| --- | --- |
| TimeGenerated |Runbook ジョブが実行された日付と時刻。 |
| RunbookName_s |Runbook の名前。 |
| Caller_s |操作を開始したユーザー。  スケジュールされたジョブのシステムまたは電子メール アドレスが記録されます。 |
| Tenant_g | 呼び出し元のテナントを識別する GUID です。 |
| JobId_g |GUID。Runbook ジョブの ID です。 |
| ResultType |Runbook ジョブの状態。  次のいずれかの値になります。<br>- 開始済み<br>- 停止済み<br>- 中断<br>- 失敗<br>- 完了 |
| カテゴリ | データの種類の分類。  Automation の場合、値は JobLogs です。 |
| OperationName | Azure で実行された操作の種類を指定します。  Automation の場合、値は Job です。 |
| リソース | Automation アカウントの名前です。 |
| SourceSystem | Log Analytics がデータを収集する方法。 Azure 診断の場合、常に *Azure* です。 |
| ResultDescription |Runbook ジョブの結果の状態について説明します。  次のいずれかの値になります。<br>- ジョブが開始されました<br>- ジョブが失敗しました<br>- ジョブが完了しました |
| CorrelationId |GUID。Runbook ジョブの関連付け ID です。 |
| ResourceId |Runbook の Azure Automation アカウントのリソース ID を指定します。 |
| SubscriptionId | Automation アカウントの Azure サブスクリプション ID (GUID) です。 |
| ResourceGroup | Automation アカウントのリソース グループの名前です。 |
| ResourceProvider | MICROSOFT.AUTOMATION |
| ResourceType | AUTOMATIONACCOUNTS |


### <a name="job-streams"></a>ジョブ ストリーム
| プロパティ | 説明 |
| --- | --- |
| TimeGenerated |Runbook ジョブが実行された日付と時刻。 |
| RunbookName_s |Runbook の名前。 |
| Caller_s |操作を開始したユーザー。  スケジュールされたジョブのシステムまたは電子メール アドレスが記録されます。 |
| StreamType_s |ジョブ ストリームの種類。 次のいずれかの値になります。<br>- 進行状況<br>- 出力<br>- 警告<br>- エラー<br>- デバッグ<br>- 詳細 |
| Tenant_g | 呼び出し元のテナントを識別する GUID です。 |
| JobId_g |GUID。Runbook ジョブの ID です。 |
| ResultType |Runbook ジョブの状態。  次のいずれかの値になります。<br>- In Progress |
| カテゴリ | データの種類の分類。  Automation の場合、値は JobStreams です。 |
| OperationName | Azure で実行された操作の種類を指定します。  Automation の場合、値は Job です。 |
| リソース | Automation アカウントの名前です。 |
| SourceSystem | Log Analytics がデータを収集する方法。 Azure 診断の場合、常に *Azure* です。 |
| ResultDescription |Runbook からの出力ストリームが含まれます。 |
| CorrelationId |GUID。Runbook ジョブの関連付け ID です。 |
| ResourceId |Runbook の Azure Automation アカウントのリソース ID を指定します。 |
| SubscriptionId | Automation アカウントの Azure サブスクリプション ID (GUID) です。 |
| ResourceGroup | Automation アカウントのリソース グループの名前です。 |
| ResourceProvider | MICROSOFT.AUTOMATION |
| ResourceType | AUTOMATIONACCOUNTS |

## <a name="viewing-automation-logs-in-log-analytics"></a>Log Analytics での Automation ログの確認
Automation ジョブのログを Log Analytics に送信し始めたので、次は、Log Analytics 内でこれらのログに対して何ができるかを確認しましょう。

ログを表示するには、次のクエリを実行します。`Type=AzureDiagnostics ResourceProvider="MICROSOFT.AUTOMATION"`

### <a name="send-an-email-when-a-runbook-job-fails-or-suspends"></a>Runbook ジョブが失敗または中断したときに電子メールを送信する
顧客からの問い合わせとして多いのは、Runbook ジョブに問題が発生したときに電子メールまたはテキストを送信する機能です。   

アラート ルールを作成するには、まずアラートを呼び出す Runbook ジョブ レコードに対するログ検索を作成します。  **[アラート]** ボタンをクリックし、アラート ルールを作成して構成します。

1. Log Analytics の [Overview (概要)] ページで、**[Log Search (ログ検索)]** をクリックします。
2. クエリ フィールドに次の検索クエリを入力して、アラート用のログ検索クエリを作成します。`Type=AzureDiagnostics ResourceProvider="MICROSOFT.AUTOMATION" Category=JobLogs (ResultType=Failed OR ResultType=Suspended)`次の内容を使用して、Runbook 名でグループ化することもできます。`Type=AzureDiagnostics ResourceProvider="MICROSOFT.AUTOMATION" Category=JobLogs (ResultType=Failed OR ResultType=Suspended) | measure Count() by RunbookName_s`   

   複数の Automation アカウントまたはサブスクリプションからワークスペースへのログをセットアップしてある場合は、サブスクリプションおよび Automation アカウントごとにアラートをグループ化することができます。  Automation アカウント名は JobLogs の検索のリソース フィールドから派生していることもあります。  
3. **[アラート ルールの追加]** 画面を開くには、ページの上部にある **[アラート]** をクリックします。 アラートの構成オプションの詳細については、「[Log Analytics のアラート](../log-analytics/log-analytics-alerts.md#alert-rules)」を参照してください。

### <a name="find-all-jobs-that-have-completed-with-errors"></a>エラーが発生したすべてのジョブを特定する
エラーに関するアラートだけでなく、Runbook ジョブが終了しないときにもエラーが表示されます。 このような場合、PowerShell ではエラー ストリームが生成されますが、ジョブが終了しないエラーでは、ジョブの中断や失敗は起こりません。    

1. Log Analytics ワークスペースで **[ログ検索]** をクリックします。
2. クエリ フィールドに 「 `Type=AzureDiagnostics ResourceProvider="MICROSOFT.AUTOMATION" Category=JobStreams StreamType_s=Error | measure count() by JobId_g` 」と入力し、 **[検索]**をクリックします。

### <a name="view-job-streams-for-a-job"></a>ジョブのジョブ ストリームを確認する
ジョブのデバッグを行っているときに、ジョブ ストリームの確認が必要になることもあります。  次のクエリは、GUID が 2ebd22ea-e05e-4eb9-9d76-d73cbd4356e0 である 1 つのジョブのすべてのストリームを表示します。   

`Type=AzureDiagnostics ResourceProvider="MICROSOFT.AUTOMATION" Category=JobStreams JobId_g="2ebd22ea-e05e-4eb9-9d76-d73cbd4356e0" | sort TimeGenerated | select ResultDescription`

### <a name="view-historical-job-status"></a>ジョブの状態の履歴を確認する
最後に、ジョブの履歴を時系列で視覚化する必要が生じることもあります。  次のクエリを使うと、ジョブの状態を時系列で検索できます。

`Type=AzureDiagnostics ResourceProvider="MICROSOFT.AUTOMATION" Category=JobLogs NOT(ResultType="started") | measure Count() by ResultType interval 1hour`  
<br> ![OMS Historical Job Status Chart](media/automation-manage-send-joblogs-log-analytics/historical-job-status-chart.png)<br>

## <a name="summary"></a>まとめ
Automation ジョブの状態およびストリーム データを Log Analytics に送信し、次の対応を行うことで、Automation ジョブの状態をより理解できるようになります。
+ 問題が発生した場合に通知するアラートの設定
+ カスタム ビューと検索クエリを使用した Runbook の結果、Runbook ジョブの状態、その他の関連する主要な指標やメトリックの視覚化。  

Log Analytics によって、Automation ジョブの状態をさらに詳しく把握でき、インシデントにより迅速に対処できるようになります。  

## <a name="next-steps"></a>次のステップ
* 各種検索クエリの作成方法と、Log Analytics での Automation ジョブの確認方法の詳細については、「 [Log Analytics におけるログの検索](../log-analytics/log-analytics-log-searches.md)
* Runbook から出力とエラー メッセージを作成および取得する方法については、 [Runbook の出力とメッセージ](automation-runbook-output-and-messages.md)
* Runbook の実行、Runbook ジョブの監視方法、その他の技術的な詳細については、 [Runbook ジョブの追跡](automation-runbook-execution.md)
* OMS Log Analytics とデータ収集ソースの詳細については、 [Log Analytics での Azure Storage データの収集の概要](../log-analytics/log-analytics-azure-storage.md)

