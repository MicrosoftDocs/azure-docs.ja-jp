---
title: Azure Monitor ログに Azure Automation のジョブ データを転送する
description: この記事では、ジョブの状態と Runbook ジョブ ストリームを Azure Monitor ログに送信する方法について説明します。
services: automation
ms.subservice: process-automation
ms.date: 09/02/2020
ms.topic: conceptual
ms.openlocfilehash: 4199c5576662eee1dd6cedc388440a71e21f8b74
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "100581221"
---
# <a name="forward-azure-automation-job-data-to-azure-monitor-logs"></a>Azure Monitor ログに Azure Automation のジョブ データを転送する

Azure Automation では、Runbook ジョブの状態とジョブ ストリームを Log Analytics ワークスペースに送信できます。 このプロセスは、ワークスペースのリンク設定を伴わず、完全に独立しています。 ジョブ ログとジョブ ストリームは、Azure Portal または PowerShell を使用してジョブごとに表示できます。これを使用して、簡単な調査を行うことができます。 Azure Monitor ログを使用すると、次のことを行うことができます。

* Automation ジョブの状態に関する分析情報を得る。
* Runbook ジョブの状態 (失敗、中断など) に基づいて電子メールまたはアラートをトリガーする。
* ジョブ ストリームをまたぐ高度なクエリを記述する。
* Automation アカウントをまたいでジョブどうしを関連付ける。
* カスタム ビューと検索クエリを使用して、Runbook の結果、Runbook ジョブの状態、その他の関連する主要な指標やメトリックを視覚化します。

## <a name="prerequisites"></a>前提条件

Azure Monitor ログへの Automation ログの送信を開始するには、次のものが必要です。

* [Azure PowerShell](/powershell/azure/) の最新リリース。

* Log Analytics ワークスペースとそのリソース ID。 詳細については、[Azure Monitor ログの使用](../azure-monitor/overview.md)に関するページを参照してください

* Azure Automation アカウントのリソース ID。

## <a name="how-to-find-resource-ids"></a>リソース ID を見つける方法

1. 自分の Azure Automation アカウントのリソース ID を調べるには、次のコマンドを使用します。

    ```powershell-interactive
    # Find the ResourceId for the Automation account
    Get-AzResource -ResourceType "Microsoft.Automation/automationAccounts"
    ```

2. **ResourceID** の値をコピーします。

3. Log Analytics ワークスペースのリソース ID を見つけるには、次のコマンドを使用します。

    ```powershell-interactive
    # Find the ResourceId for the Log Analytics workspace
    Get-AzResource -ResourceType "Microsoft.OperationalInsights/workspaces"
    ```

4. **ResourceID** の値をコピーします。

特定のリソース グループの結果を返すには、`-ResourceGroupName` パラメーターを含めます。 詳細については、「[Get-AzResource](/powershell/module/az.resources/get-azresource)」を参照してください。

前のコマンドの出力に複数の Automation アカウントまたはワークスペースが存在する場合、Automation アカウントの完全なリソース ID に含まれる関連するプロパティ (名前など) を次の手順に従って見つけることができます。

1. [Azure portal](https://portal.azure.com) にサインインします。
1. Azure portal の **Automation アカウント** ページから Automation アカウントを選択します。
1. 選択した Automation アカウントのページの **[アカウント設定]** で、 **[プロパティ]** を選択します。
1. **[プロパティ]** ページで、以下の情報を書き留めます。

    ![Automation アカウントのプロパティ](media/automation-manage-send-joblogs-log-analytics/automation-account-properties.png).

## <a name="configure-diagnostic-settings"></a>診断設定を構成する

Automation の診断設定では、次のプラットフォーム ログとメトリック データの転送がサポートされています。

* JobLogs
* JobStreams
* DSCNodeStatus
* メトリック - 合計ジョブ数、更新プログラムのデプロイ マシンの合計実行回数、更新プログラムのデプロイの合計実行回数

Automation ログの Azure Monitor ログへの送信を開始するには、[診断設定の作成](../azure-monitor/essentials/diagnostic-settings.md)を確認して、プラットフォーム ログを送信するように診断設定を構成するために使用できる機能と方法を理解します。

## <a name="azure-monitor-log-records"></a>Azure Monitor のログ レコード

Azure Automation の診断により、`AzureDiagnostics` というタグの付いた 2 種類のレコードが Azure Monitor ログに作成されます。 次のセクションの表では、Azure Automation によって生成されるレコードの例と、ログの検索結果に表示されるデータの種類を示します。

### <a name="job-logs"></a>ジョブ ログ

| プロパティ | 説明 |
| --- | --- |
| TimeGenerated |Runbook ジョブが実行された日付と時刻。 |
| RunbookName_s |Runbook の名前。 |
| Caller_s |操作を開始した呼び出し元。 スケジュールされたジョブのシステムまたは電子メール アドレスが記録されます。 |
| Tenant_g | 呼び出し元のテナントを識別する GUID。 |
| JobId_g |Runbook ジョブを識別する GUID。 |
| ResultType |Runbook ジョブの状態。 次のいずれかの値になります。<br>- 新規<br>- 作成済み<br>- 開始済み<br>- 停止済み<br>- 中断<br>- 失敗<br>- 完了 |
| カテゴリ | データの種類の分類。 Automation の場合、値は JobLogs です。 |
| OperationName | Azure で実行された操作の種類。 Automation の場合、値は Job です。 |
| リソース | Automation アカウントの名前 |
| SourceSystem | データを収集するために Azure Monitor ログによって使用されるシステム。 Azure Diagnostics の場合、値は常に "Azure" です。 |
| ResultDescription |Runbook ジョブの結果の状態。 次のいずれかの値になります。<br>- ジョブが開始されました<br>- ジョブが失敗しました<br>- ジョブが完了しました |
| CorrelationId |Runbook ジョブの関連付け GUID。 |
| ResourceId |Runbook の Azure Automation アカウントのリソース ID。 |
| SubscriptionId | Automation アカウントの Azure サブスクリプション GUID。 |
| ResourceGroup | Automation アカウントのリソース グループの名前。 |
| ResourceProvider | リソース プロバイダー。 値は MICROSOFT.AUTOMATION です。 |
| ResourceType | リソースの種類。 値は AUTOMATIONACCOUNTS です。 |

### <a name="job-streams"></a>ジョブ ストリーム
| プロパティ | 説明 |
| --- | --- |
| TimeGenerated |Runbook ジョブが実行された日付と時刻。 |
| RunbookName_s |Runbook の名前。 |
| Caller_s |操作を開始した呼び出し元。 スケジュールされたジョブのシステムまたは電子メール アドレスが記録されます。 |
| StreamType_s |ジョブ ストリームの種類。 次のいずれかの値になります。<br>\- 進行状況<br>- 出力<br>- 警告<br>- エラー<br>- デバッグ<br>- 詳細 |
| Tenant_g | 呼び出し元のテナントを識別する GUID。 |
| JobId_g |Runbook ジョブを識別する GUID。 |
| ResultType |Runbook ジョブの状態。 次のいずれかの値になります。<br>- In Progress |
| カテゴリ | データの種類の分類。 Automation の場合、値は JobStreams です。 |
| OperationName | Azure で実行された操作の種類。 Automation の場合、値は Job です。 |
| リソース | Automation アカウントの名前。 |
| SourceSystem | データを収集するために Azure Monitor ログによって使用されるシステム。 Azure Diagnostics の場合、値は常に "Azure" です。 |
| ResultDescription |Runbook からの出力ストリームが含まれる説明。 |
| CorrelationId |Runbook ジョブの関連付け GUID。 |
| ResourceId |Runbook の Azure Automation アカウントのリソース ID。 |
| SubscriptionId | Automation アカウントの Azure サブスクリプション GUID。 |
| ResourceGroup | Automation アカウントのリソース グループの名前。 |
| ResourceProvider | リソース プロバイダー。 値は MICROSOFT.AUTOMATION です。 |
| ResourceType | リソースの種類。 値は AUTOMATIONACCOUNTS です。 |

## <a name="view-automation-logs-in-azure-monitor-logs"></a>Azure Monitor ログでの Automation ログの確認

これで、Automation ジョブ ストリームとログの Azure Monitor ログへの送信が開始されたので、Azure Monitor ログの内部でこれらのログを使用して何ができるかを見てみましょう。

ログを表示するには、次のクエリを実行します。`AzureDiagnostics | where ResourceProvider == "MICROSOFT.AUTOMATION"`

### <a name="send-an-email-when-a-runbook-job-fails-or-suspends"></a>Runbook ジョブが失敗または中断したときに電子メールを送信する

次の手順は、Runbook ジョブに何か問題が生じたときに通知を受けるためのアラートを Azure Monitor で設定する方法を示しています。

アラート ルールを作成するには、まずアラートを呼び出す Runbook ジョブ レコードに対するログ検索を作成します。 **[アラート]** ボタンをクリックし、アラート ルールを作成して構成します。

1. Log Analytics ワークスペースの [概要] ページで、 **[ログの表示]** をクリックします。

2. クエリ フィールドに次の検索クエリを入力して、アラート用のログ検索クエリを作成します。`AzureDiagnostics | where ResourceProvider == "MICROSOFT.AUTOMATION" and Category == "JobLogs" and (ResultType == "Failed" or ResultType == "Suspended")`<br><br>次のクエリを使用して、Runbook 名でグループ化することもできます。`AzureDiagnostics | where ResourceProvider == "MICROSOFT.AUTOMATION" and Category == "JobLogs" and (ResultType == "Failed" or ResultType == "Suspended") | summarize AggregatedValue = count() by RunbookName_s`

   複数の Automation アカウントまたはサブスクリプションからワークスペースへのログをセットアップしてある場合は、サブスクリプションおよび Automation アカウントごとにアラートをグループ化することができます。 Automation アカウント名は `JobLogs` の検索の `Resource` フィールドで確認できます。

3. **[ルールの作成]** 画面を開くには、ページの上部にある **[新しいアラート ルール]** をクリックします。 アラートの構成オプションについて詳しくは、「[Azure Monitor でのログ アラート](../azure-monitor/alerts/alerts-unified-log.md)」をご覧ください。

### <a name="find-all-jobs-that-have-completed-with-errors"></a>エラーが発生したすべてのジョブを特定する

エラーに関するアラートだけでなく、Runbook ジョブが終了しないときにもエラーが表示されます。 このような場合、PowerShell ではエラー ストリームが生成されますが、ジョブが終了しないエラーでは、ジョブの中断や失敗は起こりません。

1. Log Analytics ワークスペースで **[ログ]** をクリックします。

2. クエリ フィールドに、「`AzureDiagnostics | where ResourceProvider == "MICROSOFT.AUTOMATION" and Category == "JobStreams" and StreamType_s == "Error" | summarize AggregatedValue = count() by JobId_g`」と入力します。

3. **[検索]** ボタンをクリックします。

### <a name="view-job-streams-for-a-job"></a>ジョブのジョブ ストリームを確認する

ジョブのデバッグを行っているときに、ジョブ ストリームの確認が必要になることもあります。 次のクエリは、GUID が `2ebd22ea-e05e-4eb9-9d76-d73cbd4356e0` である 1 つのジョブのすべてのストリームを示しています。

```kusto
AzureDiagnostics
| where ResourceProvider == "MICROSOFT.AUTOMATION" and Category == "JobStreams" and JobId_g == "2ebd22ea-e05e-4eb9-9d76-d73cbd4356e0"
| sort by TimeGenerated asc
| project ResultDescription
```

### <a name="view-historical-job-status"></a>ジョブの状態の履歴を確認する

最後に、ジョブの履歴を時系列で視覚化することが必要になる場合があります。 次のクエリを使うと、ジョブの状態を時系列で検索できます。

```kusto
AzureDiagnostics
| where ResourceProvider == "MICROSOFT.AUTOMATION" and Category == "JobLogs" and ResultType != "started"
| summarize AggregatedValue = count() by ResultType, bin(TimeGenerated, 1h)
```

![Log Analytics でのジョブの状態の履歴を示すグラフ](media/automation-manage-send-joblogs-log-analytics/historical-job-status-chart.png)

### <a name="filter-job-status-output-converted-into-a-json-object"></a>JSON オブジェクトに変換されたジョブの状態の出力をフィルター処理する

最近、Log Analytics サービスで Automation ログ データが `AzureDiagnostics` テーブルに書き込まれる場合の動作が変更されており、JSON プロパティが個別のフィールドに分割されなくなりました。 出力ストリーム内のオブジェクトを個別の列として JSON 形式で書式設定するように Runbook を構成している場合は、それらのプロパティにアクセスするために、そのフィールドを JSON オブジェクトに解析するようにクエリを再構成する必要があります。 これは、[parsejson](/azure/data-explorer/kusto/query/samples?pivots=#parsejson) を使用して、既知のパスにある特定の JSON 要素にアクセスすることによって実現されます。

たとえば、Runbook によって、出力ストリーム内の *ResultDescription* プロパティが複数のフィールドを含む JSON 形式で書式設定されるとします。 **Status** という名前のフィールドで指定されるエラー状態にあるジョブの状態を検索するには、次のクエリ例を使用して、**Failed** の状態を持つ *ResultDescription* を検索します。

```kusto
AzureDiagnostics
| where Category == 'JobStreams'
| extend jsonResourceDescription = parse_json(ResultDescription)
| where jsonResourceDescription.Status == 'Failed'
```

![Log Analytics 履歴ジョブ ストリームの JSON 形式](media/automation-manage-send-joblogs-log-analytics/job-status-format-json.png)

## <a name="next-steps"></a>次のステップ

* 検索クエリの作成方法と、Azure Monitor ログでの Automation ジョブ ログの確認方法については、[Azure Monitor ログでのログ検索](../azure-monitor/logs/log-query-overview.md)に関するページを参照してください。
* Runbook の出力とエラー メッセージを作成および取得する方法については、「[Runbook 出力を監視する](automation-runbook-output-and-messages.md)」を参照してください。
* Runbook の実行、Runbook ジョブの監視方法、その他の技術的な詳細については、「[Azure Automation での Runbook の実行](automation-runbook-execution.md)」を参照してください。
* Azure Monitor ログとデータ収集ソースの詳細については、[Azure Monitor ログにおける Azure Storage データの収集の概要](../azure-monitor/essentials/resource-logs.md#send-to-log-analytics-workspace)に関するページを参照してください。
* Log Analytics のトラブルシューティングについては、[Log Analytics がデータを収集しなくなった場合のトラブルシューティング](../azure-monitor/logs/manage-cost-storage.md#troubleshooting-why-log-analytics-is-no-longer-collecting-data)に関する記事を参照してください。