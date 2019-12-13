---
title: Azure Automation での子 Runbook
description: Azure Automation で別の Runbook から Runbook を開始し、それらの間で情報共有する異なる方法について説明します。
services: automation
ms.service: automation
ms.subservice: process-automation
author: mgoedtel
ms.author: magoedte
ms.date: 01/17/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: e7341a8c270d16497430a70c2a1b21354a775787
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/05/2019
ms.locfileid: "74850450"
---
# <a name="child-runbooks-in-azure-automation"></a>Azure Automation での子 Runbook

Azure Automation では、個別の関数 (つまり他の Runbook) によって再利用可能なモジュールの Runbook を記述する手法が推奨されます。 多くの場合、必要な機能を実行する 1 つまたは複数の子 Runbook が親 Runbook によって呼び出されます。 子 Runbook を呼び出すには 2 つの方法があります。それぞれの特徴的な相違点を理解しておくと、さまざまなシナリオでどちらが適しているかを判断できるようになります。

## <a name="invoking-a-child-runbook-using-inline-execution"></a>インライン実行で子 Runbook を呼び出す

別の Runbook からインラインで Runbook を呼び出すには、Runbook の名前を使用し、アクティビティやコマンドレットに使用するのと同じパラメーター値を指定します。  同じ Automation アカウントのすべての Runbook は、他のすべての Runbook で同じ方法で使用されます。 親 Runbook は子 Runbook が完了してから次の行に移動し、すべての出力は直接親に返されます。親 Runbook は子 Runbook が完了してから次の行に移動し、すべての出力は直接親に返されます。

Runbook をインラインで呼び出すと、親 Runbook と同じジョブで実行されます。 実行された子 Runbook はジョブ履歴には表示されません。 子 Runbook からのすべての例外とストリーム出力は、親に関連付けられます。 この動作により、子 Runbook やストリーム出力のいずれかによって発生した例外が親のジョブに関連付けられるため、ジョブの数が減って追跡および問題の解決がしやすくなります。

Runbook を発行する場合、呼び出される子 Runbook はすべて発行済みでなければなりません。 これは、Runbook のコンパイル時に、Azure Automation によってすべての子 Runbook と関連付けが行われるためです。 そうでないと、親 Runbook は正常に発行されたかのように見えますが、起動時に例外が発生します。 これが発生した場合、子 Runbook が正しく参照されるように親 Runbook を再発行できます。 子 Runbook が変更されても、関連付けは作成済みのため、親 Runbook を再発行する必要はありません。

インラインで呼び出された子 Runbook のパラメーターには、複合オブジェクトを含む任意のデータ型を使用できます。 Azure portal や Start-AzureRmAutomationRunbook コマンドレットを使用して Runbook を起動したときのような、[JSON のシリアル化](start-runbooks.md#runbook-parameters)は行われません。

### <a name="runbook-types"></a>Runbook の種類

相互に呼び出すことができる種類:

* [PowerShell Runbook](automation-runbook-types.md#powershell-runbooks) と[グラフィカル Runbook](automation-runbook-types.md#graphical-runbooks) は、相互にインラインで呼び出すことができます (両方とも PowerShell ベースです)。
* [PowerShell ワークフロー Runbook](automation-runbook-types.md#powershell-workflow-runbooks) とグラフィカル PowerShell ワークフロー Runbook は、相互にインラインで呼び出すことができます (両方とも PowerShell ワークフロー ベースです)
* PowerShell の型と PowerShell ワークフローの型は、相互にインラインで呼び出すことはできません。また、Start-AzureRmAutomationRunbook を使用する必要があります。

発行順序が重要になる状況:

* Runbook の発行順序は、PowerShell ワークフロー Runbook とグラフィカル PowerShell ワークフロー Runbook に対してのみ重要です。

インライン実行を使用してグラフィカル Runbook または PowerShell ワークフロー Runbook を子として呼び出すときは、Runbook の名前を使用します。  PowerShell の子 Runbook を呼び出すときは、名前の前に *.\\* を付けて、そのスクリプトがローカル ディレクトリにあることを指定する必要があります。

### <a name="example"></a>例

次の例では、複合オブジェクト、整数、ブール値の 3 つのパラメーターを受け入れるテスト用の子 Runbook を開始します。 子 Runbook の出力は、変数に割り当てられます。  この場合は、子 Runbook は PowerShell ワークフロー Runbook です。

```azurepowershell-interactive
$vm = Get-AzureRmVM –ResourceGroupName "LabRG" –Name "MyVM"
$output = PSWF-ChildRunbook –VM $vm –RepeatCount 2 –Restart $true
```

次に示すのは、子として PowerShell Runbook を使用する場合の例です。

```azurepowershell-interactive
$vm = Get-AzureRmVM –ResourceGroupName "LabRG" –Name "MyVM"
$output = .\PS-ChildRunbook.ps1 –VM $vm –RepeatCount 2 –Restart $true
```

## <a name="starting-a-child-runbook-using-cmdlet"></a>コマンドレットを使用して子 Runbook を開始する

> [!IMPORTANT]
> `-Wait` スイッチが含まれた `Start-AzureRmAutomationRunbook` コマンドレットを使用して子 Runbook を呼び出していて、子 Runbook の結果がオブジェクトである場合、エラーが発生する可能性があります。 エラーを回避するには、[オブジェクト出力がある子 Runbook](troubleshoot/runbooks.md#child-runbook-object) に関するセクションを参照し、結果をポーリングするロジックを実装して [Get-AzureRmAutomationJobOutputRecord](/powershell/module/azurerm.automation/get-azurermautomationjoboutputrecord) を使用する方法について確認してください。

「[Windows PowerShell での Runbook の開始](start-runbooks.md#start-a-runbook-with-powershell)」で説明されているように、[Start-AzureRmAutomationRunbook](/powershell/module/AzureRM.Automation/Start-AzureRmAutomationRunbook) コマンドレットを使用して Runbook を開始できます。 このコマンドレットの使用には 2 つのモードがあります。  一方のモードでは、コマンドレットは、子 Runbook の子ジョブが作成されるとジョブ ID を返します。  **-wait** を指定すると有効になる他のモードでは、コマンドレットは、子ジョブが完了して子 Runbook からの出力を返すまで待機します。

コマンドレットによって開始された子 Runbook のジョブは、親 Runbook とは別のジョブで実行されます。 この動作により、インラインで Runbook を開始する場合よりも多くのジョブが実行されるため、追跡が困難になります。親は、それぞれの完了を待たずに、複数の子 Runbook を非同期に開始できます。 複数の子 Runbook をインラインで同じように並列実行するには、親 Runbook で [parallel キーワード](automation-powershell-workflow.md#parallel-processing)を使用する必要があります。

子 Runbook の出力は、タイミングが原因で確実に親 Runbook に返されません。 また、$VerbosePreference や $WarningPreference などの特定の変数も子 Runbook に伝播されない可能性があります。 これらの問題を回避するために、`Start-AzureRmAutomationRunbook` コマンドレットを `-Wait` スイッチと共に使用して、子 Runbook を個別の Automation ジョブとして開始することができます。 これにより、子 Runbook が完了するまで親 Runbook がブロックされます。

親 Runbook が待機中にブロックされないようにしたい場合は、`Start-AzureRmAutomationRunbook` コマンドレットを `-Wait` スイッチなしで使用して子 Runbook を開始することができます。 その後、`Get-AzureRmAutomationJob` を使用してジョブの完了を待ち、`Get-AzureRmAutomationJobOutput` および `Get-AzureRmAutomationJobOutputRecord` を使用して結果を取得する必要があります。

コマンドレットで開始した子 Runbook のパラメーターは、「 [Runbook のパラメーター](start-runbooks.md#runbook-parameters)」で説明されているハッシュテーブルとして提供されます。 単純なデータ型のみを使用できます。 Runbook に複雑なデータ型のパラメーターが使用されている場合は、インラインで呼び出す必要があります。

子 Runbook を別のジョブとして開始するときにサブスクリプションのコンテキストが失われることがあります。 子 Runbook によって Azure RM コマンドレットが特定の Azure サブスクリプションに対して実行されるには、そのサブスクリプションに対する子 Runbook の認証が、親 Runbook とは独立して行われる必要があります。

同じ Automation アカウント内のジョブを複数のサブスクリプションで使用する場合、あるジョブのサブスクリプションを選択すると、他のジョブ用に現在選択されているサブスクリプションのコンテキストも変わる可能性があります。 この問題を回避するには、各 Runbook の先頭で `Disable-AzureRmContextAutosave –Scope Processsave` を使用します。 このアクションだけで、コンテキストがその Runbook の実行に保存されます。

### <a name="example"></a>例

次の例では、Start-AzureRmAutomationRunbook -wait パラメーターを使用することにより、パラメーターを含む子 Runbook を開始して、完了まで待機します。 完了すると、その出力が子 Runbook から収集されます。 `Start-AzureRmAutomationRunbook` を使用するには、Azure サブスクリプションに対して認証を行う必要があります。

```azurepowershell-interactive
# Ensures you do not inherit an AzureRMContext in your runbook
Disable-AzureRmContextAutosave –Scope Process

# Connect to Azure with RunAs account
$ServicePrincipalConnection = Get-AutomationConnection -Name 'AzureRunAsConnection'

Add-AzureRmAccount `
    -ServicePrincipal `
    -TenantId $ServicePrincipalConnection.TenantId `
    -ApplicationId $ServicePrincipalConnection.ApplicationId `
    -CertificateThumbprint $ServicePrincipalConnection.CertificateThumbprint

$AzureContext = Select-AzureRmSubscription -SubscriptionId $ServicePrincipalConnection.SubscriptionID

$params = @{"VMName"="MyVM";"RepeatCount"=2;"Restart"=$true}

Start-AzureRmAutomationRunbook `
    –AutomationAccountName 'MyAutomationAccount' `
    –Name 'Test-ChildRunbook' `
    -ResourceGroupName 'LabRG' `
    -AzureRMContext $AzureContext `
    –Parameters $params –wait
```

## <a name="comparison-of-methods-for-calling-a-child-runbook"></a>子 Runbook を呼び出す方法の比較

次の表は、別の Runbook から Runbook を呼び出すための次の 2 つの方法の相違点をまとめたものです。

|  | インライン | コマンドレット |
|:--- |:--- |:--- |
| ジョブ |子 Runbook は、親と同じジョブで実行されます。 |子 Runbook 用に別のジョブが作成されます。 |
| Execution |親 Runbook は、子 Runbook の完了を待ってから続行します。 |親 Runbook は、子 Runbook が開始されたらすぐに続行するか、 *または* 、子ジョブの完了を待ちます。 |
| Output |親 Runbook は、子 Runbook から出力を直接取得できます。 |親 Runbook は、子 Runbook ジョブから出力を取得する必要があるか、 *または* 、子 Runbook から出力を直接取得できます。 |
| parameters |子 Runbook のパラメーター値は個別に指定され、任意のデータ型を使用できます。 |子 Runbook のパラメーターの値は、1 つのハッシュテーブルに結合する必要があります。 このハッシュテーブルには、JSON のシリアル化が使用される、単純、配列、オブジェクトの各データ型のみを含めることができます。 |
| Automation アカウント |親 Runbook は、同じ Automation アカウントの子 Runbook のみを使用できます。 |親 Runbook では、同じ Azure サブスクリプションの Automation アカウントの子 Runbook のほか、接続されていれば別のサブスクリプションの Automation アカウントの子 Runbook も使用できます。 |
| 発行 |親 Runbook を発行する前に、子 Runbook を発行する必要があります。 |親 Runbook が開始される前のある時点で、子 Runbook が発行される必要があります。 |

## <a name="next-steps"></a>次の手順

* [Azure Automation での Runbook を開始する](start-runbooks.md)
* [Runbook output and messages in Azure Automation (Azure Automation での Runbook の出力および メッセージ)](automation-runbook-output-and-messages.md)

