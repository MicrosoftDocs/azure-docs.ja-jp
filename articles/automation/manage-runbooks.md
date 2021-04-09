---
title: Azure Automation で Runbook を管理する
description: この記事では、Azure Automation で Runbook を管理する方法について説明します。
services: automation
ms.subservice: process-automation
ms.date: 02/24/2021
ms.topic: conceptual
ms.openlocfilehash: af767ab37e8e77195b7d13b24ea78f4fb88485fb
ms.sourcegitcommit: dac05f662ac353c1c7c5294399fca2a99b4f89c8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/04/2021
ms.locfileid: "102122138"
---
# <a name="manage-runbooks-in-azure-automation"></a>Azure Automation で Runbook を管理する

Azure Automation には、新しい Runbook を作成するか、ファイルまたは [Runbook ギャラリー](automation-runbook-gallery.md)から既存の Runbook をインポートすることによって Runbook を追加することができます。 この記事では、ファイルからインポートした Runbook の管理について取り上げます。 コミュニティによって提供されている Runbook やモジュールの利用について詳しくは、[Azure Automation 用の Runbook ギャラリーとモジュール ギャラリー](automation-runbook-gallery.md)に関するページを参照してください。

## <a name="create-a-runbook"></a>Runbook を作成する

Azure portal または Windows PowerShell を使用して、Azure Automation に新しい Runbook を作成します。 作成した Runbook は、次の情報を使用して編集できます。

* [Azure Automation でのテキスト形式の Runbook の編集](automation-edit-textual-runbook.md)
* [Automation Runbook 向けの Windows PowerShell ワークフローの基本的な概念の説明](automation-powershell-workflow.md)
* [Azure Automation で Python 2 パッケージを管理する](python-packages.md)
* [Azure Automation で Python 3 パッケージ (プレビュー) を管理する](python-3-packages.md)

### <a name="create-a-runbook-in-the-azure-portal"></a>Azure portal で Runbook を作成する

1. Azure ポータルで、Automation アカウントを開きます。
2. ハブから、 **[プロセス オートメーション]** の **[Runbook]** を選択し、Runbook の一覧を開きます。
3. **[Runbook の作成]** をクリックします。
4. Runbook の名前を入力し、その [[種類]](automation-runbook-types.md) を選択します。 Runbook 名は、先頭を英字にする必要があり、英字、数字、アンダースコア、およびダッシュを使用できます。
5. **[作成]** をクリックして Runbook を作成し、エディターを開きます。

### <a name="create-a-runbook-with-powershell"></a>PowerShell で Runbook を作成する

空の Runbook を作成するには、[New-AzAutomationRunbook](/powershell/module/az.automation/new-azautomationrunbook) コマンドレットを使用します。 `Type` パラメーターを使用して、`New-AzAutomationRunbook` に対して定義されている Runbook の種類の 1 つを指定します。

次の例は、新しい空の Runbook を作成する方法を示しています。

```azurepowershell-interactive
New-AzAutomationRunbook -AutomationAccountName MyAccount `
-Name NewRunbook -ResourceGroupName MyResourceGroup -Type PowerShell
```

## <a name="import-a-runbook"></a>Runbook をインポートする

PowerShell または PowerShell ワークフロー ( **.ps1**) スクリプト、グラフィカル Runbook ( **.graphrunbook**)、Python 2 または Python 3 スクリプト ( **.py**) をインポートして独自の Runbook を作成することができます。 以下の点を考慮して、インポート中に作成される [Runbook の種類](automation-runbook-types.md) を指定する必要があります。

* ワークフローが含まれていない **.ps1** ファイルは、[PowerShell Runbook](automation-runbook-types.md#powershell-runbooks) または [PowerShell ワークフロー Runbook](automation-runbook-types.md#powershell-workflow-runbooks) のどちらにもインポートすることができます。 それを PowerShell ワークフロー Runbook にインポートすると、ワークフローに変換されます。 この場合、Runbook には、加えられた変更を説明するコメントが追加されます。

* PowerShell ワークフローを含む **.ps1** ファイルは、[PowerShell ワークフロー Runbook](automation-runbook-types.md#powershell-workflow-runbooks) にのみインポートできます。 ファイルに複数の PowerShell ワークフローが含まれている場合、インポートは失敗します。 各ワークフローを専用のファイルに保存し、それぞれを個別にインポートする必要があります。

* PowerShell ワークフローを含む **.ps1** ファイルは PowerShell スクリプト エンジンでは認識できないため、[PowerShell Runbook](automation-runbook-types.md#powershell-runbooks) にインポートしないでください。

* **.graphrunbook** ファイルは、新しい [グラフィカル Runbook](automation-runbook-types.md#graphical-runbooks) にのみインポートします。

### <a name="import-a-runbook-from-the-azure-portal"></a>Azure portal から Runbook をインポートする

次の手順を使用して、スクリプト ファイルを Azure Automation にインポートできます。

> [!NOTE]
> このポータルを使用して PowerShell ワークフロー Runbook にインポートできるのは **.ps1** ファイルだけです。

1. Azure ポータルで、Automation アカウントを開きます。
2. **[プロセス オートメーション]** の **[Runbook]** を選択し、Runbook の一覧を開きます。
3. **[Runbook のインポート]** をクリックします。
4. **[Runbook ファイル]** をクリックし、インポートするファイルを選択します。
5. **[名前]** フィールドが有効になっている場合は、必要に応じて Runbook 名を変更することができます。 名前は文字で始める必要があり、文字、数字、アンダースコア、およびダッシュを含めることができます。
6. [Runbook の種類](automation-runbook-types.md) は自動的に選択されますが、適切な制限を考慮して変更することもできます。
7. **Create** をクリックしてください。 Automation アカウントの Runbook の一覧に新しい Runbook が表示されます。
8. Runbook を実行するには、先に [Runbook を発行する](#publish-a-runbook) 必要があります。

> [!NOTE]
> グラフィカル Runbook は、インポート後、他の種類に変換することができます。 ただし、グラフィカル Runbook をテキスト形式の Runbook に変換することはできません。

### <a name="import-a-runbook-with-windows-powershell"></a>Windows PowerShell を使用して Runbook をインポートする

Runbook のドラフトとしてスクリプト ファイルをインポートするには、[Import-AzAutomationRunbook](/powershell/module/az.automation/import-azautomationrunbook) コマンドレットを使用します。 Runbook が既に存在する場合、コマンドレットで `Force` パラメーターを使用しないと、インポートは失敗します。

次の例では、スクリプト ファイルを Runbook にインポートする方法を示します。

```azurepowershell-interactive
$automationAccountName =  "AutomationAccount"
$runbookName = "Sample_TestRunbook"
$scriptPath = "C:\Runbooks\Sample_TestRunbook.ps1"
$RGName = "ResourceGroup"

Import-AzAutomationRunbook -Name $runbookName -Path $scriptPath `
-ResourceGroupName $RGName -AutomationAccountName $automationAccountName `
-Type PowerShellWorkflow
```

## <a name="handle-resources"></a>リソースを処理する

Runbook で[リソース](automation-runbook-execution.md#resources)を作成する場合、リソースの作成を試みる前に、リソースが既に存在するかどうかをスクリプトで確認する必要があります。 基本的な例を次に示します。

```powershell
$vmName = "WindowsVM1"
$resourceGroupName = "myResourceGroup"
$myCred = Get-AutomationPSCredential "MyCredential"
$vmExists = Get-AzResource -Name $vmName -ResourceGroupName $resourceGroupName

if(!$vmExists)
    {
    Write-Output "VM $vmName does not exist, creating"
    New-AzVM -Name $vmName -ResourceGroupName $resourceGroupName -Credential $myCred
    }
else
    {
    Write-Output "VM $vmName already exists, skipping"
    }
```

## <a name="retrieve-details-from-activity-log"></a>アクティビティ ログから詳細を取得する

Runbook を開始したユーザーやアカウントなど、Runbook に関する詳細を、Automation アカウントの[アクティビティ ログ](automation-runbook-execution.md#activity-logging)から取得できます。 次の PowerShell の例では、指定した Runbook を最後に実行したユーザーが提供されます。

```powershell-interactive
$SubID = "00000000-0000-0000-0000-000000000000"
$AutomationResourceGroupName = "MyResourceGroup"
$AutomationAccountName = "MyAutomationAccount"
$RunbookName = "MyRunbook"
$StartTime = (Get-Date).AddDays(-1)
$JobActivityLogs = Get-AzLog -ResourceGroupName $AutomationResourceGroupName -StartTime $StartTime `
                                | Where-Object {$_.Authorization.Action -eq "Microsoft.Automation/automationAccounts/jobs/write"}

$JobInfo = @{}
foreach ($log in $JobActivityLogs)
{
    # Get job resource
    $JobResource = Get-AzResource -ResourceId $log.ResourceId

    if ($JobInfo[$log.SubmissionTimestamp] -eq $null -and $JobResource.Properties.runbook.name -eq $RunbookName)
    {
        # Get runbook
        $Runbook = Get-AzAutomationJob -ResourceGroupName $AutomationResourceGroupName -AutomationAccountName $AutomationAccountName `
                                            -Id $JobResource.Properties.jobId | ? {$_.RunbookName -eq $RunbookName}

        # Add job information to hashtable
        $JobInfo.Add($log.SubmissionTimestamp, @($Runbook.RunbookName,$Log.Caller, $JobResource.Properties.jobId))
    }
}
$JobInfo.GetEnumerator() | sort key -Descending | Select-Object -First 1
```

## <a name="track-progress"></a>進捗状況の追跡

簡単に再利用や再起動ができるロジックを使用したモジュールの形式で Runbook を作成することをお勧めします。 問題が発生しても、Runbook の進行状況が追跡されていれば、Runbook のロジックを正常に実行することができます。

Runbook の進行状況は、ストレージ アカウント、データベース、ファイル共有などの外部ソースを使用して追跡できます。 最後に実行されたアクションの状態をまずチェックするロジックを、Runbook に作成します。 その後、チェックの結果に基づいて、Runbook 内の特定のタスクをスキップまたは続行できます。

## <a name="prevent-concurrent-jobs"></a>同時実行ジョブの防止

一部の Runbook は、同時に複数のジョブで実行されると、おかしな動作をすることがあります。 この場合は、既に実行中のジョブがあるかどうかをチェックするロジックを Runbook に実装することが重要です。 基本的な例を次に示します。

```powershell
# Authenticate to Azure
$connection = Get-AutomationConnection -Name AzureRunAsConnection
Connect-AzAccount -ServicePrincipal -Tenant $connection.TenantID `
-ApplicationId $connection.ApplicationID -CertificateThumbprint $connection.CertificateThumbprint

$AzureContext = Get-AzSubscription -SubscriptionId $connection.SubscriptionID

# Check for already running or new runbooks
$runbookName = "<RunbookName>"
$rgName = "<ResourceGroupName>"
$aaName = "<AutomationAccountName>"
$jobs = Get-AzAutomationJob -ResourceGroupName $rgName -AutomationAccountName $aaName -RunbookName $runbookName -AzContext $AzureContext

# Check to see if it is already running
$runningCount = ($jobs | ? {$_.Status -eq "Running"}).count

If (($jobs.status -contains "Running" -And $runningCount -gt 1 ) -Or ($jobs.Status -eq "New")) {
    # Exit code
    Write-Output "Runbook is already running"
    Exit 1
} else {
    # Insert Your code here
}
```

## <a name="handle-transient-errors-in-a-time-dependent-script"></a>時間に依存するスクリプトの一時的なエラーを処理する

Runbook は堅牢であること、そして再起動や失敗を引き起こす可能性のある[エラー](automation-runbook-execution.md#errors) (一時的なエラーを含む) を処理できることが必要です。 Runbook が失敗した場合、Azure Automation によって再試行されます。

Runbook が時間制約内で普通に実行される場合は、スクリプトで実行時間をチェックするロジックを実装します。 このチェックにより、特定の時間帯にのみ、起動、シャットダウン、スケールアウトなどの操作が実行されることが保証されます。

> [!NOTE]
> Azure サンドボックス プロセス上のローカル時刻は UTC に設定されます。 Runbook での日付と時刻の計算では、この事実を考慮する必要があります。

## <a name="work-with-multiple-subscriptions"></a>複数のサブスクリプションの操作

Runbook は、[サブスクリプション](automation-runbook-execution.md#subscriptions)を操作できることが必要です。 たとえば、複数のサブスクリプションを処理するために、Runbook には [Disable-AzContextAutosave](/powershell/module/Az.Accounts/Disable-AzContextAutosave) コマンドレットが使用されます。 このコマンドレットを使用するのは、同じサンドボックスで実行されている別の Runbook から認証コンテキストが取得されないようにするためです。 また、Runbook は `Get-AzContext` コマンドレットを使用して現在のセッションのコンテキストを取得し、変数 `$AzureContext` に割り当てます。

```powershell
Disable-AzContextAutosave -Scope Process

$Conn = Get-AutomationConnection -Name AzureRunAsConnection
$AzureContext = Connect-AzAccount -ServicePrincipal `
-Tenant $Conn.TenantID `
-ApplicationId $Conn.ApplicationID `
-CertificateThumbprint $Conn.CertificateThumbprint `
-Subscription $Conn.SubscriptionId

$ChildRunbookName = 'ChildRunbookDemo'
$AutomationAccountName = 'myAutomationAccount'
$ResourceGroupName = 'myResourceGroup'

Start-AzAutomationRunbook `
-ResourceGroupName $ResourceGroupName `
-AutomationAccountName $AutomationAccountName `
-Name $ChildRunbookName `
-DefaultProfile $AzureContext
```

## <a name="work-with-a-custom-script"></a>カスタム スクリプトを使用する

> [!NOTE]
> 通常、Log Analytics エージェントがインストールされているホストでカスタム スクリプトと Runbook を実行することはできません。

カスタム スクリプトを使用するには、次の手順に従います。

1. Automation アカウントを作成して[共同作成者ロール](automation-role-based-access-control.md)を取得します。
2. その[アカウントを Azure ワークスペースにリンク](../security-center/security-center-enable-data-collection.md)させます。
3. [Hybrid Runbook Worker](automation-hybrid-runbook-worker.md)、[Update Management](./update-management/overview.md)、または別の Automation 機能を有効にします。 
4. Linux マシンを使用している場合、高い権限が必要です。 ログインして[署名のチェックをオフ](automation-linux-hrw-install.md#turn-off-signature-validation)にしてください。

## <a name="test-a-runbook"></a>Runbook をテストする

Runbook のテスト時には [ドラフト バージョン](#publish-a-runbook) が実行され、そのバージョンで行われるすべてのアクションが完了します。 ジョブ履歴は作成されませんが、[テスト出力] ペインに[出力](automation-runbook-output-and-messages.md#use-the-output-stream)および[警告とエラー](automation-runbook-output-and-messages.md#working-with-message-streams) ストリームが表示されます。 [VerbosePreference](automation-runbook-output-and-messages.md#work-with-preference-variables) 変数が `Continue` に設定されている場合のみ、[詳細ストリーム](automation-runbook-output-and-messages.md#write-output-to-verbose-stream)に対するメッセージが [出力] ペインに表示されます。

ドラフト バージョンの実行中でも、Runbook の実行は通常どおり行われて、環境内のリソースに対するすべてのアクションが実行されます。 このため、Runbook をテストするのは非運用環境のリソースのみにしてください。

[Runbook の各種類](automation-runbook-types.md)をテストする手順はいずれも同じです。 Azure portal のテキスト エディターとグラフィカル エディターではテスト方法に違いはありません。

1. [テキスト エディター](automation-edit-textual-runbook.md)または[グラフィカル エディター](automation-graphical-authoring-intro.md)のどちらかで、ドラフト バージョンの Runbook を開きます。
1. **[テスト]** をクリックして、テスト ページを開きます。
1. Runbook にパラメーターがある場合は左ペインに表示され、そこでテストに使用する値を指定することができます。
1. [Hybrid Runbook Worker](automation-hybrid-runbook-worker.md) に対してテストを実施する場合は、 **[実行設定]** を **[ハイブリッド worker]** に変更してターゲット グループの名前を選択します。  それ以外の場合は、既定の **[Azure]** をそのまま使用してクラウドでテストを実行します。
1. **[開始]** をクリックしてテストを開始します。
1. [出力] ウィンドウの下にあるボタンを使用すれば、テスト中に、[PowerShell ワークフロー](automation-runbook-types.md#powershell-workflow-runbooks)または[グラフィック](automation-runbook-types.md#graphical-runbooks) Runbook を停止したり中断したりできます。 Runbook は、中断される場合は中断前に現在のアクティビティを完了します。 Runbook を中断した後で、停止または再開できます。
1. [出力] ウィンドウで Runbook からの出力を確認します。

## <a name="publish-a-runbook"></a>Runbook を発行する

新しい Runbook を作成またはインポートしたときは、実行する前に発行する必要があります。 Azure Automation の各 Runbook には、ドラフト バージョンと発行バージョンがあります。 実行できるのは発行バージョンのみで、編集できるのはドラフト バージョンのみです。 ドラフト バージョンを変更しても発行バージョンに影響はありません。 ドラフト バージョンを使用できるようにする必要がある場合は、それを発行します。これにより、現在発行されているバージョンがドラフト バージョンで上書きされます。

### <a name="publish-a-runbook-in-the-azure-portal"></a>Azure portal で Runbook を発行する

1. Azure ポータルで Runbook を開きます。
2. **[編集]** をクリックします。
3. **[発行]** をクリックしてから、確認メッセージに対して **[はい]** クリックします。

### <a name="publish-a-runbook-using-powershell"></a>PowerShell を使用して Runbook を発行する

[Publish-AzAutomationRunbook](/powershell/module/Az.Automation/Publish-AzAutomationRunbook) コマンドレットを使用して Runbook を発行します。 

```azurepowershell-interactive
$automationAccountName =  "AutomationAccount"
$runbookName = "Sample_TestRunbook"
$RGName = "ResourceGroup"

Publish-AzAutomationRunbook -AutomationAccountName $automationAccountName `
-Name $runbookName -ResourceGroupName $RGName
```

## <a name="schedule-a-runbook-in-the-azure-portal"></a>Azure portal で Runbook をスケジュールする

Runbook が発行済みであるときに、操作のスケジュールを設定できます。

1. Azure ポータルで Runbook を開きます。
2. **[リソース]** の下の **[スケジュール]** を選択します。
3. **[スケジュールの追加]** を選択します。
4. [Runbook のスケジュール] ウィンドウで、 **[スケジュールを Runbook にリンクします]** を選択します。
5. [スケジュール] ウィンドウで、 **[新しいスケジュールを作成します]** をクリックします。
6. [新しいスケジュール] ウィンドウで、名前、説明、およびその他のパラメーターを入力します。
7. スケジュールを作成したら、それを強調表示し、 **[OK]** をクリックします。 これで、Runbook にリンクされるようになります。
8. メールボックスの電子メールで、Runbook の状態通知を探します。

## <a name="obtain-job-statuses"></a>ジョブの状態を取得する

### <a name="view-statuses-in-the-azure-portal"></a>Azure portal で状態を表示する

Azure Automation におけるジョブの処理について詳しくは、「[ジョブ](automation-runbook-execution.md#jobs)」を参照してください。 Runbook ジョブを見る準備が整ったら、Azure portal を使用して Automation アカウントにアクセスします。 右側にある **[ジョブの統計情報]** で、すべての Runbook ジョブの概要を確認できます。

![[ジョブの統計情報] タイル](./media/manage-runbooks/automation-account-job-status-summary.png)

概要では、実行された各ジョブの数を確認できるほか、その状態がグラフィカルに表示されます。

タイルをクリックすると、実行されたすべてのジョブの概要を示す一覧が含まれた [ジョブ] ページが表示されます。 このページには、各ジョブの状態、Runbook 名、開始時刻、完了時間が表示されます。

:::image type="content" source="./media/manage-runbooks/automation-account-jobs-status-blade.png" alt-text="[ジョブ] ページのスクリーンショット。":::

**[ジョブのフィルター]** を選択すると、ジョブの一覧をフィルター処理すできます。 特定の Runbook、ジョブの状態、またはドロップダウン リストから選択したものでフィルター処理し、検索の時間範囲を指定します。

![ジョブの状態でフィルター処理する](./media/manage-runbooks/automation-account-jobs-filter.png)

また、Automation アカウントの [Runbook] ページで Runbook を選択してから、 **[ジョブ]** を選択することで、特定の Runbook のジョブ概要の詳細を表示することができます。 この操作により、[ジョブ] ページが表示されます。 ここから、ジョブ レコードをクリックして、その詳細と出力を表示できます。

:::image type="content" source="./media/manage-runbooks/automation-runbook-job-summary-blade.png" alt-text="[エラー] ボタンが強調表示された [ジョブ] ページのスクリーンショット。":::

### <a name="retrieve-job-statuses-using-powershell"></a>PowerShell を使用してジョブの状態を取得する

Runbook 用に作成されたジョブと、特定のジョブの詳細を取得するには、[Get-AzAutomationJob](/powershell/module/Az.Automation/Get-AzAutomationJob) コマンドレットを使用します。 `Start-AzAutomationRunbook` を使用して Runbook を開始すると、結果として作成されたジョブが返されます。 ジョブの出力を取得するには [Get-AzAutomationJobOutput](/powershell/module/Az.Automation/Get-AzAutomationJobOutput) を使用します。

次の例では、サンプル Runbook の最後のジョブが取得されて、その状態、Runbook パラメーターに指定された値、およびジョブの出力が表示されます。

```azurepowershell-interactive
$job = (Get-AzAutomationJob –AutomationAccountName "MyAutomationAccount" `
–RunbookName "Test-Runbook" -ResourceGroupName "ResourceGroup01" | sort LastModifiedDate –desc)[0]
$job.Status
$job.JobParameters
Get-AzAutomationJobOutput -ResourceGroupName "ResourceGroup01" `
–AutomationAccountName "MyAutomationAcct" -Id $job.JobId –Stream Output
```

次の例では、特定のジョブの出力が取得されて、各レコードが返されます。 いずれかのレコードで[例外](automation-runbook-execution.md#exceptions)が発生した場合、スクリプトでは値ではなく例外が書き出されます。 この動作は役に立ちます。出力時に通常はログに記録されない追加情報が、例外によって提供される可能性があるためです。

```azurepowershell-interactive
$output = Get-AzAutomationJobOutput -AutomationAccountName <AutomationAccountName> -Id <jobID> -ResourceGroupName <ResourceGroupName> -Stream "Any"
foreach($item in $output)
{
    $fullRecord = Get-AzAutomationJobOutputRecord -AutomationAccountName <AutomationAccountName> -ResourceGroupName <ResourceGroupName> -JobId <jobID> -Id $item.StreamRecordId
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

## <a name="next-steps"></a>次のステップ

* Runbook の管理の詳細については「[Azure Automation での Runbook の実行](automation-runbook-execution.md)」を参照してください。
* PowerShell Runbook の準備については、「[Azure Automation でのテキスト形式の Runbook の編集](automation-edit-textual-runbook.md)」を参照してください。
* Runbook の実行に関する問題のトラブルシューティングについては、[Runbook の問題のトラブルシューティング](troubleshoot/runbooks.md)に関するページを参照してください。