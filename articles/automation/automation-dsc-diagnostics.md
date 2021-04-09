---
title: Azure Monitor ログとの統合
description: この記事では、Desired State Configuration レポート データを Azure Automation State Configuration から Azure Monitor ログに送信する方法について説明します。
services: automation
ms.service: automation
ms.subservice: dsc
author: mgoedtel
ms.author: magoedte
ms.date: 11/06/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 0afe349473bcddcbf1ac35136f2991ffe82670c6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "100576062"
---
# <a name="integrate-with-azure-monitor-logs"></a>Azure Monitor ログとの統合

Azure Automation State Configuration は、ノード状態のデータを 30 日間保持します。 このデータを長期間保持したい場合は、ノード状態のデータを Log Analytics ワークスペースに送信することができます。 Azure Portal または PowerShell を使用して、ノード構成内のノードや個々の DSC リソースのコンプライアンス ステータスを確認できます。 

Azure Monitor ログによって、Automation State Configuration のデータの状態をさらに詳しく把握でき、インシデントにより迅速に対処できるようになります。 Azure Monitor ログを使用すると、次のことを行うことができます。

- マネージド ノードや個々のリソースのコンプライアンスに関する情報を取得します。
- コンプライアンス ステータスに基づいてメールまたは警告をトリガーします。
- 複数のマネージド ノードを対象とする高度なクエリを記述します。
- Automation アカウント間でコンプライアンス ステータスを関連付けます。
- カスタム ビューと検索クエリを使用して、Runbook の結果、Runbook ジョブの状態、その他の関連する主要な指標やメトリックを視覚化します。

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="prerequisites"></a>前提条件

Azure Monitor ログへの Automation State Configuration レポートの送信を開始するには、次のものが必要です。

- 2016 年 11 月以降のリリースの [Azure PowerShell](/powershell/azure/) (v2.3.0)。
- Azure Automation アカウント。 詳細については、「[Azure Automation の概要](automation-intro.md)」を参照してください。
- Automation & Control サービス プラン付きの Log Analytics ワークスペース。 詳細については、「[Azure Monitor で Log Analytics の使用を開始する](../azure-monitor/logs/log-analytics-tutorial.md)」を参照してください。
- 1 つ以上の Azure Automation State Configuration ノード。 詳細については、「[Azure Automation State Configuration による管理のためのマシンのオンボード](automation-dsc-onboarding.md)」をご覧ください。
- [xDscDiagnostics](https://www.powershellgallery.com/packages/xDscDiagnostics/2.7.0.0) モジュール、バージョン 2.7.0.0 以上。 インストール手順については、「[Azure Automation Desired State Configuration (DSC) の問題をトラブルシューティングする](./troubleshoot/desired-state-configuration.md)」を参照してください。

## <a name="set-up-integration-with-azure-monitor-logs"></a>Azure Monitor ログとの統合の設定

Azure Automation State Configuration から Azure Monitor ログへのデータのインポートを始めるには、次の手順のようにします。

1. PowerShell で Azure アカウントにログインします。 [Azure PowerShell を使用してサインインする](/powershell/azure/authenticate-azureps)に関するページをご覧ください。
1. 次の PowerShell コマンドレットを実行して、Automation アカウントのリソース ID を取得します。 複数の Automation アカウントがある場合は、構成するアカウントのリソース ID を選択します。

   ```powershell
   # Find the ResourceId for the Automation account
   Get-AzResource -ResourceType 'Microsoft.Automation/automationAccounts'
   ```

1. 次の PowerShell コマンドレットを実行して、Log Analytics ワークスペースのリソース ID を取得します。 複数のワークスペースがある場合は、構成するワークスペースのリソース ID を選択します。

   ```powershell
   # Find the ResourceId for the Log Analytics workspace
   Get-AzResource -ResourceType 'Microsoft.OperationalInsights/workspaces'
   ```

1. 次の PowerShell コマンドレットを実行します。`<AutomationResourceId>` と `<WorkspaceResourceId>` はそれぞれ前の手順で取得した `ResourceId` の値に置き換えます。

   ```powershell
   Set-AzDiagnosticSetting -ResourceId <AutomationResourceId> -WorkspaceId <WorkspaceResourceId> -Enabled $true -Category 'DscNodeStatus'
   ```

1. Azure Automation State Configuration から Azure Monitor ログへのデータのインポートを停止するには、次の PowerShell コマンドレットを実行します。

   ```powershell
   Set-AzDiagnosticSetting -ResourceId <AutomationResourceId> -WorkspaceId <WorkspaceResourceId> -Enabled $false -Category 'DscNodeStatus'
   ```

## <a name="view-the-state-configuration-logs"></a>State Configuration ログを表示する

Automation State Configuration データの Azure Monitor ログとの統合を設定した後は、State Configuration (DSC) ページの左側のウィンドウにある **[監視]** セクションで **[ログ]** を選択することで表示できます。

![ログ](media/automation-dsc-diagnostics/automation-dsc-logs-toc-item.png)

[ログ検索] ペインが開き、Automation アカウント リソースを対象としたクエリ リージョンが表示されます。 Azure Monitor ログで検索すると、State Configuration ログで DSC 操作を検索できます。 DSC の操作のレコードは、`AzureDiagnostics` テーブルに格納されます。 たとえば、準拠していないノードを検索するには、次のクエリを入力します。

```AzureDiagnostics
| where Category == 'DscNodeStatus' 
| where OperationName contains 'DSCNodeStatusData'
| where ResultType != 'Compliant'
```

フィルター処理の詳細:

* `DscNodeStatusData` でフィルター処理を実行すると、各 State Configuration ノードの操作が返されます。
* `DscResourceStatusData` でフィルター処理を実行すると、そのリソースに適用されているノード構成で呼び出された各 DSC リソースに対して操作が返されます。 
* `DscResourceStatusData` でフィルター処理を実行すると、失敗したすべての DSC リソースのエラー情報が返されます。

ログ クエリを構築してデータを検索する方法の詳細については、[Azure Monitor のログ クエリの概要](../azure-monitor/logs/log-query-overview.md)に関するページを参照してください。

### <a name="send-an-email-when-a-state-configuration-compliance-check-fails"></a>State Configuration のコンプライアンス チェックでエラーになったときに電子メールを送信する

お客様からの要望として多いのは、DSC 構成に問題が発生したときに電子メールまたはテキストを送信する機能です。

警告ルールを作成するには、まず、警告を呼び出す State Configuration レポートのレコードに対するログ検索を作成します。 **[新しいアラート ルール]** をクリックし、アラート ルールを作成して構成します。

1. Log Analytics ワークスペースの [概要] ページで、 **[ログ]** をクリックします。
1. クエリ フィールドに次の検索クエリを入力して、アラート用のログ検索クエリを作成します。`Type=AzureDiagnostics Category='DscNodeStatus' NodeName_s='DSCTEST1' OperationName='DscNodeStatusData' ResultType='Failed'`

   複数の Automation アカウントまたはサブスクリプションからワークスペースへのログをセットアップしてある場合は、サブスクリプションおよび Automation アカウントごとにアラートをグループ化することができます。 `DscNodeStatusData` レコードの検索の `Resource` フィールドから、Automation アカウント名を抽出します。
1. **[ルールの作成]** 画面を開くには、ページの上部にある **[新しいアラート ルール]** をクリックします。 

アラートの構成オプションについて詳しくは、[アラート ルールの作成](../azure-monitor/alerts/alerts-metric.md)に関する記事をご覧ください。

### <a name="find-failed-dsc-resources-across-all-nodes"></a>すべてのノードで失敗した DSC リソースを検索する

Azure Monitor ログを使用する利点の 1 つは、失敗したチェックをノードを越えて検索できることです。 失敗した DSC リソースのすべてのインスタンスを見つけるには、次のようにします。

1. Log Analytics ワークスペースの [概要] ページで、 **[ログ]** をクリックします。
1. クエリ フィールドに次のように入力して、アラートに対するログ検索クエリを作成します。`Type=AzureDiagnostics Category='DscNodeStatus' OperationName='DscResourceStatusData' ResultType='Failed'`

### <a name="view-historical-dsc-node-status"></a>DSC ノードの状態の履歴を表示する

DSC ノードの状態の履歴を時系列で視覚化するには、次のクエリを使用できます。

`Type=AzureDiagnostics ResourceProvider="MICROSOFT.AUTOMATION" Category=DscNodeStatus NOT(ResultType="started") | measure Count() by ResultType interval 1hour`

このクエリにより、ノードの状態が時系列でグラフに表示されます。

## <a name="azure-monitor-logs-records"></a>Azure Monitor ログのレコード

Azure Automation の診断により、Azure Monitor ログに 2 つのカテゴリのレコードが作成されます。

* ノード状態データ (`DscNodeStatusData`)
* リソース状態データ (`DscResourceStatusData`)

### <a name="dscnodestatusdata"></a>DscNodeStatusData

| プロパティ | 説明 |
| --- | --- |
| TimeGenerated |コンプライアンス チェックが実行された日時。 |
| OperationName |`DscNodeStatusData`. |
| ResultType |ノードが準拠しているかどうかを示す値。 |
| NodeName_s |管理対象ノードの名前。 |
| NodeComplianceStatus_s |ノードが準拠しているかどうかを示す状態の値。 |
| DscReportStatus |コンプライアンス チェックが正常に実行されたかどうかを示す状態の値。 |
| ConfigurationMode | ノードに構成を適用するために使用されたモード。 次のいずれかの値になります。 <ul><li>`ApplyOnly`:DSC が構成を適用し、以降は新しい構成がターゲット ノードにプッシュない限り、または新しい構成がサーバーからプルされるまで何もしません。 新しい構成が最初に適用された後、DSC は以前の構成された状態からの誤差を確認しません。 DSC では、`ApplyOnly` 値が有効になる前に、構成の適用が成功するまで試みられます。 </li><li>`ApplyAndMonitor`:これが既定値です。 LCM が任意の新しい構成を適用します。 新しい構成が最初に適用された後、ターゲット ノードが目的の状態から変わった場合、DSC はログに不一致を報告します。 DSC では、`ApplyAndMonitor` 値が有効になる前に、構成の適用が成功するまで試みられます。</li><li>`ApplyAndAutoCorrect`:DSC によって新しい構成が適用されます。 新しい構成が最初に適用された後、ターゲット ノードが目的の状態から変わった場合、DSC はログに不一致を報告し、現在の構成を再適用します。</li></ul> |
| HostName_s | 管理対象ノードの名前。 |
| IPAddress | 管理対象ノードの IPv4 アドレス。 |
| カテゴリ | `DscNodeStatus`. |
| リソース | Azure Automation アカウントの名前。 |
| Tenant_g | 呼び出し元のテナントを識別する GUID。 |
| NodeId_g | 管理対象ノードを識別する GUID。 |
| DscReportId_g | レポートを識別する GUID。 |
| LastSeenTime_t | レポートが最後に閲覧された日時。 |
| ReportStartTime_t | レポートが最後に開始された日時。 |
| ReportEndTime_t | レポートが完了した日時。 |
| NumberOfResources_d | ノードに適用された構成で呼び出された DSC ソース。 |
| SourceSystem | Azure Monitor ログでのデータ収集方法を示すソース システム。 Azure Diagnostics の場合は常に `Azure` です。 |
| ResourceId |Azure Automation アカウントのリソース識別子。 |
| ResultDescription | この操作に対するリソースの説明。 |
| SubscriptionId | Automation アカウントの Azure サブスクリプション ID (GUID) です。 |
| ResourceGroup | Automation アカウントのリソース グループの名前。 |
| ResourceProvider | MICROSOFT.AUTOMATION。 |
| ResourceType | AUTOMATIONACCOUNTS。 |
| CorrelationId | コンプライアンス レポートの相関 ID である GUID。 |

### <a name="dscresourcestatusdata"></a>DscResourceStatusData

| プロパティ | 説明 |
| --- | --- |
| TimeGenerated |コンプライアンス チェックが実行された日時。 |
| OperationName |`DscResourceStatusData`.|
| ResultType |リソースが準拠しているかどうか。 |
| NodeName_s |管理対象ノードの名前。 |
| カテゴリ | DscNodeStatus。 |
| リソース | Azure Automation アカウントの名前。 |
| Tenant_g | 呼び出し元のテナントを識別する GUID。 |
| NodeId_g |管理対象ノードを識別する GUID。 |
| DscReportId_g |レポートを識別する GUID。 |
| DscResourceId_s |DSC リソース インスタンスの名前。 |
| DscResourceName_s |DSC リソースの名前。 |
| DscResourceStatus_s |DSC リソースが適合しているかどうか。 |
| DscModuleName_s |DSC リソースを含む PowerShell モジュールの名前。 |
| DscModuleVersion_s |DSC リソースを含む PowerShell モジュールのバージョン。 |
| DscConfigurationName_s |ノードに適用されている構成の名前。 |
| ErrorCode_s | リソースが失敗した場合のエラー コード。 |
| ErrorMessage_s |リソースが失敗した場合のエラー メッセージ。 |
| DscResourceDuration_d |DSC リソースの実行時間 (秒)。 |
| SourceSystem | Azure Monitor ログでのデータ収集方法。 Azure Diagnostics の場合は常に `Azure` です。 |
| ResourceId |Azure Automation アカウントの識別子。 |
| ResultDescription | この操作の説明。 |
| SubscriptionId | Automation アカウントの Azure サブスクリプション ID (GUID) です。 |
| ResourceGroup | Automation アカウントのリソース グループの名前。 |
| ResourceProvider | MICROSOFT.AUTOMATION。 |
| ResourceType | AUTOMATIONACCOUNTS。 |
| CorrelationId |コンプライアンス レポートの相関 ID である GUID。 |

## <a name="next-steps"></a>次のステップ

- 概要については、[Azure Automation State Configuration の概要](automation-dsc-overview.md)に関するページを参照してください。
- 使用を開始するには、「[Azure Automation State Configuration の使用を開始する](automation-dsc-getting-started.md)」をご覧ください。
- DSC 構成をコンパイルしてターゲット ノードに割り当てる方法の詳細については、「[Azure Automation State Configuration で DSC 構成をコンパイルする](automation-dsc-compile.md)」をご覧ください。
- PowerShell コマンドレットのリファレンスについては、「[Az.Automation](/powershell/module/az.automation)」をご覧ください。
- 料金情報については、[Azure Automation State Configuration の価格](https://azure.microsoft.com/pricing/details/automation/)に関するページをご覧ください。
- 継続的なデプロイ パイプラインで Azure Automation State Configuration を使う例については、「[Chocolatey を使用して継続的配置を設定する](automation-dsc-cd-chocolatey.md)」をご覧ください。
- 各種検索クエリの作成方法と、Azure Monitor ログでの Automation State Configuration ログの確認方法の詳細については、[Azure Monitor ログでのログ検索](../azure-monitor/logs/log-query-overview.md)に関するページを参照してください。
- Azure Monitor ログとデータ収集ソースの詳細については、[Azure Monitor ログにおける Azure Storage データの収集の概要](../azure-monitor/essentials/resource-logs.md#send-to-log-analytics-workspace)に関するページを参照してください。