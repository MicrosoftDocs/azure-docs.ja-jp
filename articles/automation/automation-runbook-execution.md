---
title: Azure Automation での Runbook の実行
description: Azure Automation で Runbook が処理される方法の詳細について説明します。
services: automation
ms.service: automation
ms.subservice: process-automation
author: mgoedtel
ms.author: magoedte
ms.date: 04/04/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: ddeeaeccc0a10d19a070a91d7bd9bef2b31c0570
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/05/2019
ms.locfileid: "74850756"
---
# <a name="runbook-execution-in-azure-automation"></a>Azure Automation での Runbook の実行

Azure Automation で runbook を開始するときに、ジョブが作成されます。 ジョブは、Runbook の単一の実行インスタンスです。 各ジョブを実行する Azure Automation ワーカーが割り当てられます。 ワーカーは多数の Azure アカウントで共有されますが、異なる Automation アカウントからのジョブは互いに分離されます。 ジョブに対する要求をどのワーカーで処理するかを、制御することはできません。 1 つの Runbook で、多数のジョブを同時に実行することができます。 同じ Automation アカウントからのジョブの実行環境を再利用できます。 同時に実行するジョブが多いほど、同じサンドボックスにディスパッチする頻度が上がります。 同じサンドボックス プロセスで実行するジョブは、お互いに影響し合う可能性があります。1 つの例は、`Disconnect-AzureRMAccount` コマンドレットの実行です。 このコマンドレットを実行すると、共有サンドボックス プロセス内の各 Runbook ジョブが切断されます。 Azure Portal で Runbook の一覧を表示すると、各 Runbook に対して起動されたすべてのジョブの状態が一覧表示されます。 Runbook ごとにジョブの一覧を表示して、それぞれの状態を追跡できます。 ジョブのログは、最大 30 日間保存されます。 ジョブのさまざまな状態の説明については、「[ジョブの状態](#job-statuses)」をご覧ください。

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-dsr-and-stp-note.md)]

次の図に、[PowerShell Runbook](automation-runbook-types.md#powershell-runbooks)、[グラフィカル Runbook](automation-runbook-types.md#graphical-runbooks)、および [PowerShell ワークフロー Runbook](automation-runbook-types.md#powershell-workflow-runbooks) のための Runbook ジョブのライフサイクルを示します。

![ジョブの状態 - PowerShell Workflow](./media/automation-runbook-execution/job-statuses.png)

ジョブは、Azure サブスクリプションに接続することにより Azure リソースにアクセスします。 データ センター内のリソースにパブリック クラウドからアクセスできる場合、ジョブはそれらのリソースにのみアクセスします。

## <a name="where-to-run-your-runbooks"></a>Runbook を実行する場所

Azure Automation の Runbook は、Azure のサンドボックスまたは [Hybrid Runbook Worker](automation-hybrid-runbook-worker.md) のいずれかで実行できます。 サンドボックスは、複数のジョブで使用できる Azure での共有環境です。 同じサンドボックスを使用するジョブは、サンドボックスのリソース制限に縛られます。 Hybrid Runbook Worker は、ロールをホストしているコンピューターで、環境内のリソースに対して Runbook を直接実行し、それらのローカル リソースを管理できます。 Runbook は Azure Automation で格納および管理された後、1 つ以上の割り当て済みコンピューターに配信されます。 ほとんどの Runbook は、Azure のサンドボックスで容易に実行できます。 Runbook の実行のために、Azure サンドボックスよりも Hybrid Runbook を選択することが推奨される特定のシナリオがあります。 一部のシナリオの例を示した一覧については、次の表を参照してください。

|タスク|最適な選択肢|メモ|
|---|---|---|
|Azure リソースと統合する|Azure サンドボックス|Azure でホストされ、認証がより簡単です。 Azure VM で Hybrid Runbook Worker を使用している場合は、[Azure リソース用のマネージド ID](automation-hrw-run-runbooks.md#managed-identities-for-azure-resources) を使用できます|
|最適なパフォーマンスで Azure リソースを管理する|Azure サンドボックス|スクリプトは同じ環境内で実行されます。それが、待ち時間がより短いことにつながります|
|運用コストを最小限に抑える|Azure サンドボックス|コンピューティングのオーバーヘッドがなく、VM の必要がありません|
|実行時間の長いスクリプト|Hybrid Runbook Worker|Azure のサンドボックスには[リソースの制限があります](../azure-subscription-service-limits.md#automation-limits)|
|ローカル サービスと相互作用する|Hybrid Runbook Worker|ホスト マシンに直接アクセスできます|
|サード パーティ製ソフトウェアと実行可能ファイルが必要|Hybrid Runbook Worker|OS を管理し、ソフトウェアをインストールできます|
|Runbook でファイルまたはフォルダーを監視する|Hybrid Runbook Worker|Hybrid Runbook Worker で [Watcher タスク](automation-watchers-tutorial.md)を使用します|
|多量のリソースを消費するスクリプト|Hybrid Runbook Worker| Azure のサンドボックスには[リソースの制限があります](../azure-subscription-service-limits.md#automation-limits)|
|特定の要件でのモジュールの使用| Hybrid Runbook Worker|次に例をいくつか示します。</br> **WinSCP** - winscp.exe への依存関係 </br> **IISAdministration** - IIS を有効にする必要がある|
|インストーラーが必要なモジュールをインストールする|Hybrid Runbook Worker|サンドボックス用のモジュールはコピー可能であることが必要です|
|4\.7.2 以外の .NET Framework が必要な Runbook またはモジュールの使用|Hybrid Runbook Worker|Automation のサンドボックスには .NET Framework 4.7.2 が備わっており、それをアップグレードする方法がありません|
|昇格が必要なスクリプト|Hybrid Runbook Worker|サンド ボックスでは、昇格は許可されません。 これを解決するには、Hybrid Runbook Worker を使用してください。UAC をオフにして、昇格が必要なコマンドを実行するときに `Invoke-Command` を使用できます|
|WMI へのアクセスが必要なスクリプト|Hybrid Runbook Worker|クラウドのサンドボックスで実行しているジョブには [WMI へのアクセス権がありません](#device-and-application-characteristics)|

## <a name="runbook-behavior"></a>Runbook の動作

Runbook は、その内部で定義されているロジックに基づいて実行されます。 Runbook は、中断された場合、先頭から再開されます。 この動作のためには、一時的な問題があった場合の Runbook の再起動をサポートする方法で、Runbook が記述される必要があります。

Azure サンドボックスで実行された Runbook から開始された PowerShell ジョブは完全言語モードで実行されないことがあります。 PowerShell 言語モードの詳細については、「[PowerShell 言語モード](/powershell/module/microsoft.powershell.core/about/about_language_modes)」を参照してください。 Azure Automation でジョブを操作する方法に関する詳細については、「[PowerShell を使用したジョブの状態の取得」](#retrieving-job-status-using-powershell)を参照してください

### <a name="creating-resources"></a>リソースの作成

リソースを作成するスクリプトの場合、リソースの再作成を試みる前に、それが既に存在するかどうかを確認する必要があります。 次の例で、基本となる例を示します。

```powershell
$vmName = "WindowsVM1"
$resourceGroupName = "myResourceGroup"
$myCred = Get-AutomationPSCredential "MyCredential"
$vmExists = Get-AzureRmResource -Name $vmName -ResourceGroupName $resourceGroupName

if(!$vmExists)
    {
    Write-Output "VM $vmName does not exists, creating"
    New-AzureRmVM -Name $vmName -ResourceGroupName $resourceGroupName -Credential $myCred
    }
else
    {
    Write-Output "VM $vmName already exists, skipping"
    }
```

### <a name="time-dependant-scripts"></a>時間依存のスクリプト

Runbook の作成時に慎重に検討してください。 前述のように、Runbook は堅牢で、Runbook の再起動または失敗を引き起こす可能性のある一時的なエラーを処理できる方法で作成する必要があります。 Runbook は、失敗した場合には再試行されます。 Runbook が、通常は時間の制約内で実行される場合、実行時間を調べるロジックを Runbook 内に実装し、起動、シャットダウン、スケールアウトなどの操作が特定時間中にのみ実行されるようにする必要があります。

> [!NOTE]
> Azure のサンド ボックス プロセス上のローカル時刻は UTC 時刻に設定されます。 Runbook で日付と時刻を計算するには、これを考慮する必要があります。

### <a name="tracking-progress"></a>進行状況の追跡

本質的にモジュラー形式である Runbook を作成することをお勧めします。 これは、容易に再利用と再起動が可能なように Runbook のロジックを構築することを意味します。 Runbook の進行状況を追跡することは、問題がある場合に Runbook のロジックが正常に実行されることを保証する適切な方法です。 Runbook の進行状況を追跡する方法は、ストレージ アカウント、データベース、ファイル共有のような外部ソースを使用する方法など、いくつか考えられます。 外部から状態を追跡することで、Runbook のロジックを作成できます。Runbook の最後のアクションの状態をまずチェックします。 その後、その結果に基づいて、Runbook 内の特定のタスクをスキップまたは続行するようにします。

### <a name="prevent-concurrent-jobs"></a>同時実行ジョブの防止

一部の Runbook は、複数のジョブにわたって同時に実行されると、おかしな動作をすることがあります。 この場合、Runbook に既に実行中のジョブがあるかどうかをチェックするロジックを実装することが重要です。 この動作を実行可能な方法の基本例を、次の例に示します。

```powershell
# Authenticate to Azure
$connection = Get-AutomationConnection -Name AzureRunAsConnection
Connect-AzureRmAccount -ServicePrincipal -Tenant $connection.TenantID `
-ApplicationID $connection.ApplicationID -CertificateThumbprint $connection.CertificateThumbprint

$AzureContext = Select-AzureRmSubscription -SubscriptionId $connection.SubscriptionID

# Check for already running or new runbooks
$runbookName = "<RunbookName>"
$rgName = "<ResourceGroupName>"
$aaName = "<AutomationAccountName>"
$jobs = Get-AzureRmAutomationJob -ResourceGroupName $rgName -AutomationAccountName $aaName -RunbookName $runbookName -AzureRmContext $AzureContext

# If then check to see if it is already running
$runningCount = ($jobs | ? {$_.Status -eq "Running"}).count

If (($jobs.status -contains "Running" -And $runningCount -gt 1 ) -Or ($jobs.Status -eq "New")) {
    # Exit code
    Write-Output "Runbook is already running"
    Exit 1
} else {
    # Insert Your code here
}
```

### <a name="working-with-multiple-subscriptions"></a>複数のサブスクリプションの操作

複数のサブスクリプションを扱う Runbook を作成するとき、Runbook が [Disable-AzureRmContextAutosave](/powershell/module/azurerm.profile/disable-azurermcontextautosave) コマンドレットを使用して、同じサンドボックスで実行している可能性がある別の Runbook から認証コンテキストを取得しないようにする必要があります。 その後、`AzureRM`コマンドレットで `-AzureRmContext` パラメーターを使用して、それを適切なコンテキストに渡す必要があります。

```powershell
# Ensures you do not inherit an AzureRMContext in your runbook
Disable-AzureRmContextAutosave –Scope Process

$Conn = Get-AutomationConnection -Name AzureRunAsConnection
Connect-AzureRmAccount -ServicePrincipal `
-Tenant $Conn.TenantID `
-ApplicationID $Conn.ApplicationID `
-CertificateThumbprint $Conn.CertificateThumbprint

$context = Get-AzureRmContext

$ChildRunbookName = 'ChildRunbookDemo'
$AutomationAccountName = 'myAutomationAccount'
$ResourceGroupName = 'myResourceGroup'

Start-AzureRmAutomationRunbook `
    -ResourceGroupName $ResourceGroupName `
    -AutomationAccountName $AutomationAccountName `
    -Name $ChildRunbookName `
    -DefaultProfile $context
```

### <a name="handling-exceptions"></a>例外の処理

スクリプトを作成するときは、例外や潜在的に発生する間欠的エラーを処理できるようにすることが重要です。 例外や間欠的な問題を Runbook で処理する方法をいくつか次に示します。

#### <a name="erroractionpreference"></a>$ErrorActionPreference

[$ErrorActionPreference](/powershell/module/microsoft.powershell.core/about/about_preference_variables#erroractionpreference) ユーザー設定変数は、終了しないエラーに対して PowerShell が応答する方法を決定します。 終了するエラーは `$ErrorActionPreference` の影響を受けず、常に終了します。 `$ErrorActionPreference` を使用すると、`Get-ChildItem` コマンドレットの `PathNotFound` のような通常は終了しないエラーによって、Runbook が完了できなくなります。 次の例に、`$ErrorActionPreference` の使用方法を示します。 スクリプトが停止するため、最後の `Write-Output` 行は実行されません。

```powershell-interactive
$ErrorActionPreference = 'Stop'
Get-Childitem -path nofile.txt
Write-Output "This message will not show"
```

#### <a name="try-catch-finally"></a>Try Catch Finally

[Try Catch](/powershell/module/microsoft.powershell.core/about/about_try_catch_finally) は、終了するエラーを処理するために PowerShell スクリプトで使用されます。 Try Catch を使用すると、特定の例外または一般的な例外をキャッチできます。 Catch ステートメントは、エラーを追跡するため、またはエラーの処理を試行するために使用してください。 次の例は、存在しないファイルをダウンロードしようとしています。 これは `System.Net.WebException` 例外をキャッチします。別の例外がある場合には、最後の値が返されます。

```powershell-interactive
try
{
   $wc = new-object System.Net.WebClient
   $wc.DownloadFile("http://www.contoso.com/MyDoc.doc")
}
catch [System.Net.WebException]
{
    "Unable to download MyDoc.doc from http://www.contoso.com."
}
catch
{
    "An error occurred that could not be resolved."
}
```

#### <a name="throw"></a>Throw

[Throw](/powershell/module/microsoft.powershell.core/about/about_throw) を使用すると、終了するエラーを生成できます。 これは、Runbook で独自のロジックを定義するときに役立ちます。 スクリプトを停止したい特定の条件が満たされると、`throw` を使用してスクリプトを停止できます。 次の例は、`throw` を使用して、必要な関数パラメーターをマシンに表示します。

```powershell-interactive
function Get-ContosoFiles
{
  param ($path = $(throw "The Path parameter is required."))
  Get-ChildItem -Path $path\*.txt -recurse
}
```

### <a name="using-executables-or-calling-processes"></a>実行可能ファイルの使用またはプロセスの呼び出し

Azure サンドボックスで実行される Runbook は、プロセス (.exe や subprocess.call など) の呼び出しをサポートしていません。 これは、Azure サンドボックスはコンテナーで実行される共有プロセスであり、基になっているすべての API にアクセスできるとは限らないためです。 サード パーティー製ソフトウェアやサブ プロセスの呼び出しが必要なシナリオの場合は、[Hybrid Runbook Worker](automation-hybrid-runbook-worker.md) で Runbook を実行することをお勧めします。

### <a name="device-and-application-characteristics"></a>デバイスとアプリケーションの特性

Azure サンドボックスで実行される Runbook ジョブには、デバイスまたはアプリケーションの特性に対するアクセス権はありません。 Windows 上のパフォーマンス メトリックにクエリを実行するために使用される最も一般的な API は WMI です。 一般的なメトリックとしてはメモリや CPU の使用率があります。 ただし、どの API を使用するかは関係ありません。 クラウドで実行するジョブには、デバイスとアプリケーションの特性を定義する業界標準である Common Information Model (CIM) 上に構築された Web Based Enterprise Management (WBEM) の Microsoft 実装に対するアクセス権はありません。

## <a name="job-statuses"></a>ジョブの状態

次の表には、ジョブが取り得るさまざまな状態を説明します。 PowerShell には、終了するエラーと終了しないエラーという、2 種類のエラーがあります。 終了するエラーは、発生した場合に Runbook の状態を **[失敗]** に設定します。 終了しないエラーの場合、エラー発生後もスクリプトを継続できます。 終了しないエラーの例では、実在しないパスで `Get-ChildItem` コマンドレットを使用しています。 PowerShell では、パスが存在しないことを確認して、エラーをスローし、次のフォルダーへと処理を継続します。 このエラーでは、Runbook の状態を **[失敗]** に設定せず、 **[完了]** とマークすることが可能です。 終了しないエラー時に Runbook を強制的に停止するには、コマンドレットで `-ErrorAction Stop` を使用できます。

| Status | 説明 |
|:--- |:--- |
| 完了 |ジョブは正常に完了しました。 |
| 失敗 |[グラフィカル Runbook と PowerShell Workflow Runbook](automation-runbook-types.md)では、Runbook のコンパイルが失敗しました。 [PowerShell スクリプト Runbook](automation-runbook-types.md) では、Runbook の開始に失敗したか、ジョブで例外が発生しました。 |
| 失敗、リソースを待機中 |ジョブは [fair share](#fair-share) の限界に 3 回到達し、毎回、同じチェックポイントから、または Runbook の先頭から起動したために、失敗しました。 |
| キューに登録済み |ジョブは Automation ワーカー上のリソースが使用できるようになるのを待機しています。そうなれば、ジョブを起動できます。 |
| 開始中 |ジョブがワーカーに割り当てられており、システムがジョブを起動しています。 |
| 再開中 |システムは、ジョブが停止された後、そのジョブを再開しています。 |
| 実行中 |ジョブは実行中です。 |
| 実行中、リソースを待機中 |ジョブは [fair share](#fair-share) 制限に達したためにアンロードされました。 ジョブは最後のチェックポイントからすぐに再開します。 |
| 停止済み |ジョブは完了した後、ユーザーによって停止されました。 |
| 停止中 |システムは、ジョブを停止しています。 |
| Suspended |ユーザーか、システムか、または Runbook 内のコマンドによってジョブは中断されました。 Runbook にチェックポイントがない場合は、Runbook の先頭から開始されます。 チェックポイントがある場合は、最後のチェックポイントからもう一度再開できます。 Runbook は、例外が発生した場合にシステムによってのみ中断されます。 既定では、ErrorActionPreference は、エラーでもジョブの実行を継続することを意味する **Continue** に設定されています。 このユーザー設定変数を **Stop** に設定すると、エラー発生時にジョブは中断します。 [グラフィカル Runbook と PowerShell Workflow Runbook](automation-runbook-types.md) のみに適用されます。 |
| 中断中 |ユーザーの要求を受けてシステムはジョブを中断しようとしています。 Runbook は、次のチェックポイントに到達してからでないと、中断できません。 Runbook は、次のチェックポイントに到達してからでないと、中断できません。 [グラフィカル Runbook と PowerShell Workflow Runbook](automation-runbook-types.md) のみに適用されます。 |

## <a name="viewing-job-status-from-the-azure-portal"></a>Azure Portal を使用したジョブの状態の表示

すべての Runbook ジョブの状態の概要を表示したり、Azure portal での特定の Runbook ジョブの詳細にドリルダウンしたりできます。 Log Analytics ワークスペースとの統合を構成し、Runbook のジョブの状態やジョブ ストリームを転送することも可能です。 Azure Monitor ログとの統合の詳細については、「[Automation から Azure Monitor ログにジョブの状態とジョブ ストリームを転送する](automation-manage-send-joblogs-log-analytics.md)」を参照してください。

### <a name="automation-runbook-jobs-summary"></a>Automation Runbook ジョブの概要

選択した Automation アカウントの右にある **[ジョブの統計情報]** タイルでは、すべての Runbook ジョブの概要を確認できます。

![[ジョブの統計情報] タイル](./media/automation-runbook-execution/automation-account-job-status-summary.png)

このタイルでは、実行されたすべてのジョブの数を確認できるほか、その状態がグラフィカルに表示されます。

タイルをクリックすると、実行されたすべてのジョブの概要を示す一覧が含まれる **[ジョブ]** ページが表示されます。 このページには、状態、開始時刻、および完了時刻が表示されます。

![Automation アカウントの [ジョブ] ページ](./media/automation-runbook-execution/automation-account-jobs-status-blade.png)

ジョブのリストをフィルター処理するには、 **[ジョブのフィルター]** を選択します。特定の Runbook、ジョブの状態でフィルターしたり、検索対象の時刻の範囲をドロップダウン リストから選択したりできます。

![[フィルター] の [ジョブの状態]](./media/automation-runbook-execution/automation-account-jobs-filter.png)

また、特定の Runbook について、ジョブ概要の詳細情報を表示することもできます。それには、Automation アカウントで **[Runbook]** ページからその Runbook を選択し、 **[ジョブ]** タイルを選択します。 この操作で **[ジョブ]** ページが表示され、そのページでジョブ レコードをクリックすると、そのジョブの詳細と出力を表示できます。

![Automation アカウントの [ジョブ] ページ](./media/automation-runbook-execution/automation-runbook-job-summary-blade.png)

### <a name="job-summary"></a>ジョブの概要

特定の Runbook 用に作成されたすべてのジョブと、それらのジョブの最新の状態を、一覧で表示できます。 この一覧は、ジョブの状態とジョブに最後に変更を加えた日付の範囲とによってフィルター処理することができます。 詳細な情報と出力を表示するには、ジョブの名前をクリックします。 ジョブの詳細表示には、そのジョブに指定された Runbook パラメーターの値が含まれます。

次の手順を使用して Runbook のジョブを表示します。

1. Azure Portal で、 **[Automation]** を選択し、次に Automation アカウントの名前を選択します。
2. ハブから **[Runbook]** を選択し、 **[Runbook]** ページで、一覧から 1 つの Runbook を選択します。
3. 選択した Runbook のページで、 **[ジョブ]** タイルをクリックします。
4. 一覧のジョブのいずれかをクリックすると、Runbook のジョブの詳細ページに、そのジョブの詳細と出力を表示できます。

## <a name="retrieving-job-status-using-powershell"></a>PowerShell を使用したジョブの状態の取得

[Get-AzureRmAutomationJob](https://docs.microsoft.com/powershell/module/azurerm.automation/get-azurermautomationjob) を使用して、Runbook 用に作成されたジョブと、特定のジョブの詳細を取得できます。 [Start-AzureRmAutomationRunbook](https://docs.microsoft.com/powershell/module/azurerm.automation/start-azurermautomationrunbook) を使用して PowerShell で Runbook を開始すると、結果として作成されたジョブが返されます。 [Get-AzureRmAutomationJobOutput](https://docs.microsoft.com/powershell/module/azurerm.automation/get-azurermautomationjoboutput) を使用して、ジョブの出力を取得します。

次のサンプル コマンドは、サンプル Runbook の最後のジョブを取得し、その状態、Runbook パラメーターに指定された値、およびジョブの出力を表示します。

```azurepowershell-interactive
$job = (Get-AzureRmAutomationJob –AutomationAccountName "MyAutomationAccount" `
–RunbookName "Test-Runbook" -ResourceGroupName "ResourceGroup01" | sort LastModifiedDate –desc)[0]
$job.Status
$job.JobParameters
Get-AzureRmAutomationJobOutput -ResourceGroupName "ResourceGroup01" `
–AutomationAccountName "MyAutomationAcct" -Id $job.JobId –Stream Output
```

次の例では、特定のジョブの出力を取得し、各レコードを返します。 いずれかのレコードで例外が発生した場合、値ではなく、その例外が書き出されます。 出力中に正常にログに記録されない可能性がある追加情報を例外で提供できるため、この動作は有用です。

```azurepowershell-interactive
$output = Get-AzureRmAutomationJobOutput -AutomationAccountName <AutomationAccountName> -Id <jobID> -ResourceGroupName <ResourceGroupName> -Stream "Any"
foreach($item in $output)
{
    $fullRecord = Get-AzureRmAutomationJobOutputRecord -AutomationAccountName <AutomationAccountName> -ResourceGroupName <ResourceGroupName> -JobId <jobID> -Id $item.StreamRecordId
    if ($fullRecord.Type -eq "Error")
    {
        $fullRecord.Value.Exception
    }
    else
    {
    $fullRecord.Value
    }
}
```

## <a name="get-details-from-activity-log"></a>アクティビティ ログから詳細を取得する

Runbook を開始したユーザーやアカウントなどの他の詳細情報は、Automation アカウントのアクティビティ ログから取得できます。 次の PowerShell の例では、対象の Runbook を最後に実行したユーザーが提供されます。

```powershell-interactive
$SubID = "00000000-0000-0000-0000-000000000000"
$AutomationResourceGroupName = "MyResourceGroup"
$AutomationAccountName = "MyAutomationAccount"
$RunbookName = "MyRunbook"
$StartTime = (Get-Date).AddDays(-1)
$JobActivityLogs = Get-AzureRmLog -ResourceGroupName $AutomationResourceGroupName -StartTime $StartTime `
                                | Where-Object {$_.Authorization.Action -eq "Microsoft.Automation/automationAccounts/jobs/write"}

$JobInfo = @{}
foreach ($log in $JobActivityLogs)
{
    # Get job resource
    $JobResource = Get-AzureRmResource -ResourceId $log.ResourceId

    if ($JobInfo[$log.SubmissionTimestamp] -eq $null -and $JobResource.Properties.runbook.name -eq $RunbookName)
    {
        # Get runbook
        $Runbook = Get-AzureRmAutomationJob -ResourceGroupName $AutomationResourceGroupName -AutomationAccountName $AutomationAccountName `
                                            -Id $JobResource.Properties.jobId | ? {$_.RunbookName -eq $RunbookName}

        # Add job information to hash table
        $JobInfo.Add($log.SubmissionTimestamp, @($Runbook.RunbookName,$Log.Caller, $JobResource.Properties.jobId))
    }
}
$JobInfo.GetEnumerator() | sort key -Descending | Select-Object -First 1
```

## <a name="fair-share"></a>fair share

クラウド内のすべての Runbook 間でリソースを共有するため、3 時間以上実行されているジョブがあると、Azure Automation はそれらのジョブを一時的にアンロードまたは停止します。 [PowerShell ベースの Runbook](automation-runbook-types.md#powershell-runbooks) および [Python の Runbook](automation-runbook-types.md#python-runbooks) に対するジョブは、停止されて再起動されずに、ジョブの状態には [停止済み] と表示されます。

長時間実行されるタスクの場合は、[Hybrid Runbook Worker](automation-hrw-run-runbooks.md#job-behavior) の使用をお勧めします。 Hybrid Runbook Worker はフェア シェアによって制限されず、Runbook が実行できる時間に制限がありません。 その他のジョブの[制限](../azure-subscription-service-limits.md#automation-limits)は、Azure サンドボックスと Hybrid Runbook Worker の両方に適用されます。 Hybrid Runbook Worker は 3 時間のフェア シェア制限を受けませんが、Hybrid Runbook Worker で実行される Runbook は、予期しないローカル インフラストラクチャの問題からの再起動動作をサポートするように開発する必要があります。

もう 1 つのオプションは、子 Runbook を使用して Runbook を最適化することです。 Runbook で、複数のリソースに対して同じ関数をループ処理する場合 (複数のデータベースに対するデータベース操作など)、その関数を[子 Runbook](automation-child-runbooks.md) に移動して、[Start-AzureRMAutomationRunbook](/powershell/module/azurerm.automation/start-azurermautomationrunbook) コマンドレットで呼び出すことができます。 これらの各子 Runbook は別々のプロセスで並列に実行されます。 この動作によって、親 Runbook の完了までにかかる合計時間は減ります。 お使いの Runbook で [Get-AzureRmAutomationJob](/powershell/module/azurerm.automation/Get-AzureRmAutomationJob) コマンドレットを使用すると、子 Runbook の完了後に実行する操作がある場合、各子のジョブの状態を確認できます。

## <a name="next-steps"></a>次の手順

* 「[Azure Automation での Runbook の開始](automation-starting-a-runbook.md)」で、Runbook を開始するさまざまな方法を確認します
* PowerShell (言語リファレンス、学習モジュールを含む) の詳細については、[PowerShell ドキュメント](https://docs.microsoft.com/powershell/scripting/overview)に関するページを参照してください。
