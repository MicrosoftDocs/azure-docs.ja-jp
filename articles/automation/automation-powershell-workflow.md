---
title: Azure Automation の PowerShell ワークフローについて
description: この記事では、PowerShell ワークフローと PowerShell の違い、および Automation Runbook に適用される概念について説明します。
services: automation
ms.subservice: process-automation
ms.date: 12/14/2018
ms.topic: conceptual
ms.openlocfilehash: 483d4c16f1b77bf7328857eb25e1571a741d144e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98896920"
---
# <a name="learn-powershell-workflow-for-azure-automation"></a>Azure Automation の PowerShell ワークフローについて

Azure Automation の Runbook は、Windows PowerShell ワークフロー、つまり Windows Workflow Foundation を利用する Windows PowerShell スクリプトとして実装されます。 A workflow is a sequence of programmed, connected steps that perform long-running tasks or require the coordination of multiple steps across multiple devices or managed nodes. 

ワークフローは Windows PowerShell の構文で記述され、Windows PowerShell によって起動されますが、Windows Workflow Foundation によって処理されます。 ワークフローが通常のスクリプトよりも優れている点としては、複数のデバイスに対する操作の同時実行や、障害からの自動復元などが挙げられます。 

> [!NOTE]
> PowerShell ワークフロー スクリプトは Windows PowerShell スクリプトと非常に似ていますが、新規ユーザーにはわかりにくい大きな違いがいくつかあります。 そのため、[チェックポイント](#use-checkpoints-in-a-workflow)を使用する必要がある場合にのみ、PowerShell ワークフローを使用して Runbook を作成することをお勧めします。 

この記事のトピックに関する詳細については、「[Windows PowerShell ワークフローについて](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/jj134242(v=ws.11))」をご覧ください。

## <a name="use-workflow-keyword"></a>ワークフロー キーワードを使用する

PowerShell スクリプトを PowerShell ワークフローに変換する最初のステップは、スクリプトを `Workflow` キーワードで囲むことです。 ワークフローは、`Workflow` キーワードで始まり、中かっこで囲まれたスクリプトの本文が後に続きます。 次の構文に示すように、ワークフローの名前は `Workflow` キーワードの後に続きます。

```powershell
Workflow Test-Workflow
{
    <Commands>
}
```

ワークフローの名前は、Automation の Runbook の名前と一致する必要があります。 Runbook がインポートされている場合、ファイル名はワークフロー名と一致していなければならず、末尾は " **.ps1**" でなければなりません。

ワークフローにパラメーターを追加するには、スクリプトの場合と同様に `Param` キーワードを使用します。

## <a name="learn-differences-between-powershell-workflow-code-and-powershell-script-code"></a>PowerShell ワークフロー コードと PowerShell スクリプト コードの違いについて

PowerShell ワークフローのコードは PowerShell スクリプト コードとほぼ同じですが、いくつかの重要な変更点があります。 次のセクションでは、ワークフローで実行するための PowerShell スクリプトに対して行う必要がある変更について説明します。

### <a name="activities"></a>Activities

アクティビティは、シーケンス内で実行されるワークフロー内の特定のタスクです。 Windows PowerShell ワークフローでは、ワークフローの実行時に Windows PowerShell コマンドレットの多くが自動でアクティビティに変換されます。 Runbook でこれらのコマンドレットのいずれかを指定すると、対応するアクティビティは、Windows Workflow Foundation で実行されます。 

対応するアクティビティがコマンドレットにない場合、Windows PowerShell ワークフローは [InlineScript](#use-inlinescript) アクティビティ内でそのコマンドレットを自動的に実行します。 コマンドレットの中には、InlineScript ブロックに明示的に含めないと除外され、ワークフローでは使用できないものもあります。 詳細については、「[スクリプト ワークフローでのアクティビティの使用](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/jj574194(v=ws.11))」を参照してください。

ワークフロー アクティビティは、操作を構成するための一連の共通パラメーターを共有します。 「[about_WorkflowCommonParameters](/powershell/module/psworkflow/about/about_workflowcommonparameters)」を参照してください。

### <a name="positional-parameters"></a>位置指定パラメーター

ワークフローのアクティビティおよびコマンドレットでは、位置指定パラメーターを使用できません。 そのため、パラメーター名を使用する必要があります。 実行中のすべてのサービスを取得する次のコードについて考えてみます。

```azurepowershell-interactive
Get-Service | Where-Object {$_.Status -eq "Running"}
```

このコードをワークフローで実行しようとすると、`Parameter set cannot be resolved using the specified named parameters.` のようなメッセージが表示されます。この問題を修正するには、次の例に示すように、パラメーター名を指定します。

```powershell
Workflow Get-RunningServices
{
    Get-Service | Where-Object -FilterScript {$_.Status -eq "Running"}
}
```

### <a name="deserialized-objects"></a>逆シリアル化されたオブジェクト

ワークフロー内のオブジェクトは逆シリアル化されます。つまり、そのプロパティは引き続き使用できますが、メソッドは使用できません。  例として、`Service` オブジェクトの `Stop` メソッドを使用してサービスを停止する次の PowerShell コードを考えます。

```azurepowershell-interactive
$Service = Get-Service -Name MyService
$Service.Stop()
```

ワークフローでこれを実行しようとすると、`Method invocation is not supported in a Windows PowerShell Workflow.` のようなエラーが表示されます。

1 つの対処方法として、以下の 2 行のコードを [InlineScript](#use-inlinescript) ブロックでラップします。 この場合、`Service` はブロック内のサービス オブジェクトを表します。

```powershell
Workflow Stop-Service
{
    InlineScript {
        $Service = Get-Service -Name MyService
        $Service.Stop()
    }
}
```

もう 1 つの方法は、メソッドとして同じ機能を持つ別のコマンドレットがある場合は、それを使用することです。 この例では、`Stop-Service` コマンドレットは `Stop` メソッドと同じ機能を提供し、ワークフローでは次のコードを使用できます。

```powershell
Workflow Stop-MyService
{
    $Service = Get-Service -Name MyService
    Stop-Service -Name $Service.Name
}
```

## <a name="use-inlinescript"></a>InlineScript を使用する

`InlineScript` アクティビティは、PowerShell ワークフローではなく従来の PowerShell スクリプトとして 1 つまたは複数のコマンドを実行する必要がある場合に便利です。  ワークフロー内のコマンドは Windows Workflow Foundation に送信されて処理されますが、InlineScript ブロック内のコマンドは Windows PowerShell によって処理されます。

InlineScript は、次に示す構文を使用します。

```powershell
InlineScript
{
    <Script Block>
} <Common Parameters>
```

InlineScript の出力を変数に割り当てることで、出力を返すことができます。 次の例では、サービスを停止した後、サービス名を出力しています。

```powershell
Workflow Stop-MyService
{
    $Output = InlineScript {
        $Service = Get-Service -Name MyService
        $Service.Stop()
        $Service
    }

    $Output.Name
}
```

InlineScript ブロックに値を渡すことはできますが、 **$Using** スコープ修飾子を使用する必要があります。  次の例は前の例と同じですが、サービス名を変数によって渡しています。

```powershell
Workflow Stop-MyService
{
    $ServiceName = "MyService"

    $Output = InlineScript {
        $Service = Get-Service -Name $Using:ServiceName
        $Service.Stop()
        $Service
    }

    $Output.Name
}
```

InlineScript アクティビティは特定のワークフローで不可欠な場合がありますが、ワークフロー コンストラクトをサポートしていません。 次の理由により、これらは必要な場合にのみ使用してください。

* InlineScript ブロックの内部で[チェックポイント](#use-checkpoints-in-a-workflow)を使用することはできません。 ブロック内でエラーが発生した場合は、ブロックの先頭から再開する必要があります。
* InlineScript ブロックの内部で[並列実行](#use-parallel-processing)を使用することはできません。
* InlineScript は、InlineScript ブロックの長さ全体について Windows PowerShell セッションを保持するため、ワークフローの拡張性に影響します。

InlineScript の使用について詳しくは、「[ワークフローでの Windows PowerShell コマンドの実行](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/jj574197(v=ws.11))」および「[about_InlineScript](/powershell/module/psworkflow/about/about_inlinescript)」をご覧ください。

## <a name="use-parallel-processing"></a>並列処理を使用する

Windows PowerShell ワークフローの利点の 1 つは、一般的なスクリプトのように順番に実行するのでなく、一連のコマンドを並行して実行できることです。

`Parallel` キーワードを使用して、同時に実行される複数のコマンドを含むスクリプト ブロックを作成できます。 これは、次に示す構文を使用します。 この場合、Activity1 と Activity2 は同時に開始されます。 Activity3 は、Activity1 と Activity2 の両方が完了した後にのみ開始されます。

```powershell
Parallel
{
    <Activity1>
    <Activity2>
}
<Activity3>
```

例として、複数のファイルをネットワーク上にコピーする次の PowerShell コマンドを考えます。 これらのコマンドは、1 つのファイルのコピーが完了してから次へファイルのコピーが開始されるように、順番に実行されます。

```azurepowershell-interactive
Copy-Item -Path C:\LocalPath\File1.txt -Destination \\NetworkPath\File1.txt
Copy-Item -Path C:\LocalPath\File2.txt -Destination \\NetworkPath\File2.txt
Copy-Item -Path C:\LocalPath\File3.txt -Destination \\NetworkPath\File3.txt
```

次のワークフローでは、すべてのコピーが同時に開始されるように、同じコマンドが並列実行されます。  すべてのファイルがコピーされると、完了メッセージが表示されます。

```powershell
Workflow Copy-Files
{
    Parallel
    {
        Copy-Item -Path "C:\LocalPath\File1.txt" -Destination "\\NetworkPath"
        Copy-Item -Path "C:\LocalPath\File2.txt" -Destination "\\NetworkPath"
        Copy-Item -Path "C:\LocalPath\File3.txt" -Destination "\\NetworkPath"
    }

    Write-Output "Files copied."
}
```

`ForEach -Parallel` の構文を使用することにより、コレクション内の各項目のコマンドを同時に処理できます。 コレクション内の項目は並行して処理され、スクリプト ブロック内のコマンドは順番に実行されます。 このプロセスは、次に示す構文を使用します。 この場合、Activity1 は、コレクション内のすべての項目に対して同時に開始されます。 各項目について、Activity1 が完了してから Activity2 が開始されます。 Activity3 は、すべての項目における Activity1 と Activity2 の両方が完了した後にのみ開始されます。 並列処理を制限するため、`ThrottleLimit` パラメーターを使用します。 `ThrottleLimit` が高すぎると、問題が発生することがあります。 `ThrottleLimit` パラメーターの理想的な値は、環境内のさまざまな要因によって異なります。 最初は低い値から始めて、特定の状況で機能するものが見つかるまで値を大きくしてさまざまな値を試してください。

```powershell
ForEach -Parallel -ThrottleLimit 10 ($<item> in $<collection>)
{
    <Activity1>
    <Activity2>
}
<Activity3>
```

次の例は、並列でファイルのコピーを行う前の例と似ています。  この場合、各ファイルのコピーが完了するたびに、メッセージが表示されます。  すべてがコピーされた後にのみ、最終的な完了メッセージが表示されます。

```powershell
Workflow Copy-Files
{
    $files = @("C:\LocalPath\File1.txt","C:\LocalPath\File2.txt","C:\LocalPath\File3.txt")

    ForEach -Parallel -ThrottleLimit 10 ($File in $Files)
    {
        Copy-Item -Path $File -Destination \\NetworkPath
        Write-Output "$File copied."
    }

    Write-Output "All files copied."
}
```

> [!NOTE]
> 子 runbook を並列で実行することはお勧めしません。これを行うと、結果の信頼性が低くなることが示されているためです。 子 Runbook からの出力は表示されない場合があるため、ある子 Runbook での設定が、並列に実行されている他の子 Runbook に影響を与える可能性があります。 `VerbosePreference` や `WarningPreference` などの変数は、子 Runbook に反映されない場合があります。 また、子 Runbook がこれらの値を変更した場合は、呼び出しの後に正しく復元されない可能性があります。

## <a name="use-checkpoints-in-a-workflow"></a>ワークフローでチェックポイントを使用する

チェックポイントは、変数の現在の値と、その時点までに生成された出力を含むワークフローの現在の状態のスナップショットです。 ワークフローがエラーで終了するか中断した場合、次に実行されるときに、最初からではなく、最後のチェックポイントから開始されます。 

`Checkpoint-Workflow` アクティビティを使用してワークフローにチェックポイントを設定できます。 Azure Automation には[フェア シェア](automation-runbook-execution.md#fair-share)という機能があり、実行時間が 3 時間に達した Runbook は、他の Runbook を実行できるようにアンロードされます。 最終的には、アンロードされた Runbook は再度読み込まれます。 その場合、Runbook で最後に取得されたチェックポイントから実行が再開されます。

最終的に Runbook が確実に完了するように、3 時間未満の実行間隔でチェックポイントを追加する必要があります。 各実行中に新しいチェックポイントが追加され、3 時間後にエラーが原因で Runbook が削除されると、Runbook の再開がいつまでも実行されます。

次の例では、Activity2 の後に例外が発生し、ワークフローが終了します。 ワークフローを再実行すると、設定された最後のチェックポイントの直後に Activity2 があるため、まず Activity2 が実行されます。

```powershell
<Activity1>
Checkpoint-Workflow
<Activity2>
<Exception>
<Activity3>
```

例外を引き起こす可能性があり、ワークフローが再開された場合は繰り返す必要のないアクティビティの後に、ワークフローのチェックポイントを設定します。 たとえば、ワークフローによって仮想マシンが作成される場合があります。 チェックポイントをコマンドの前後に設定して、仮想マシンを作成できます。 作成に失敗した場合、ワークフローが再開されると、コマンドは繰り返し実行されます。 作成の成功後にワークフローが失敗した場合、ワークフローが再開されても仮想マシンが再び作成されることはありません。

次の例では、ネットワーク上の場所に複数のファイルをコピーし、各ファイルの後にチェックポイントを設定します。  ネットワーク上の場所が失われた場合は、ワークフローがエラーで終了します。  再度開始されると、最後のチェックポイントから再開されます。 既にコピーされているファイルのみがスキップされます。

```powershell
Workflow Copy-Files
{
    $files = @("C:\LocalPath\File1.txt","C:\LocalPath\File2.txt","C:\LocalPath\File3.txt")

    ForEach ($File in $Files)
    {
        Copy-Item -Path $File -Destination \\NetworkPath
        Write-Output "$File copied."
        Checkpoint-Workflow
    }

    Write-Output "All files copied."
}
```

ユーザー名資格情報は、[Suspend-Workflow](/powershell/module/psworkflow/about/about_suspend-workflow) アクティビティを呼び出した後、または最後のチェックポイントの後は保持されないため、資格情報を null に設定し、`Suspend-Workflow` またはチェックポイントが呼び出された後にアセット ストアから再取得する必要があります。  そうしないと、次のエラー メッセージが表示される場合があります。`The workflow job cannot be resumed, either because persistence data could not be saved completely, or saved persistence data has been corrupted. You must restart the workflow.`

次の同じコードは、PowerShell ワークフロー Runbook でこの状況を処理する方法を示しています。

```powershell
workflow CreateTestVms
{
    $Cred = Get-AzAutomationCredential -Name "MyCredential"
    $null = Connect-AzAccount -Credential $Cred

    $VmsToCreate = Get-AzAutomationVariable -Name "VmsToCreate"

    foreach ($VmName in $VmsToCreate)
        {
        # Do work first to create the VM (code not shown)

        # Now add the VM
        New-AzVM -VM $Vm -Location "WestUs" -ResourceGroupName "ResourceGroup01"

        # Checkpoint so that VM creation is not repeated if workflow suspends
        $Cred = $null
        Checkpoint-Workflow
        $Cred = Get-AzAutomationCredential -Name "MyCredential"
        $null = Connect-AzAccount -Credential $Cred
        }
}
```

> [!NOTE]
> 非グラフィカル PowerShell Runbook の場合、`Add-AzAccount` と `Add-AzureRMAccount` は [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) のエイリアスです。 これらのコマンドレットを使用するか、Automation アカウントの[モジュール最新バージョンに更新](automation-update-azure-modules.md)することができます。 Automation アカウントを作成したばかりのときでも、モジュールを更新する必要がある場合があります。 サービス プリンシパルで構成された実行アカウントを使用して認証を行う場合は、これらのコマンドレットを使用する必要ありません。

チェックポイントの詳細については、「[スクリプト ワークフローへのチェックポイントの追加](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/jj574114(v=ws.11))」を参照してください。

## <a name="next-steps"></a>次のステップ

* PowerShell ワークフロー Runbook の詳細については、「[チュートリアル: PowerShell ワークフロー Runbook を作成する](learn/automation-tutorial-runbook-textual.md)」を参照してください。
