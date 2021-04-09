---
title: Azure Automation でモジュラー Runbook を作成する
description: この記事では、別の Runbook によって呼び出される Runbook を作成する方法について説明します。
services: automation
ms.subservice: process-automation
ms.date: 01/17/2019
ms.topic: conceptual
ms.openlocfilehash: f0dd5cf5209924972080af6d22429252338754de
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "99491250"
---
# <a name="create-modular-runbooks"></a>モジュラー Runbook を作成する

Azure Automation では、他の Runbook によって呼び出される個別の関数を使用して、再利用可能なモジュール型 Runbook を記述する手法が推奨されます。 多くの場合、必要な機能を実行する 1 つまたは複数の子 Runbook が親 Runbook によって呼び出されます。 

子 Runbook を呼び出すには 2 つの方法があります。それぞれの特徴的な相違点を理解しておくと、ご自分のシナリオでどちらが適しているかを判断できるようになります。 次の表は、Runbook を別の Runbook から呼び出すための 2 つの方法の相違点をまとめたものです。

|  | インライン | コマンドレット |
|:--- |:--- |:--- |
| **ジョブ** |子 Runbook は、親と同じジョブで実行されます。 |子 Runbook 用に別のジョブが作成されます。 |
| **実行** |親 Runbook は、子 Runbook の完了を待ってから続行します。 |親 Runbook は、子 Runbook が開始されたらすぐに続行するか、 *または* 、子ジョブの完了を待ちます。 |
| **出力** |親 Runbook は、子 Runbook から出力を直接取得できます。 |親 Runbook は、子 Runbook ジョブから出力を取得する必要があるか、 *または* 、子 Runbook から出力を直接取得できます。 |
| **パラメーター** |子 Runbook のパラメーター値は個別に指定され、任意のデータ型を使用できます。 |子 Runbook のパラメーターの値は、1 つのハッシュテーブルに結合する必要があります。 このハッシュテーブルには、JSON のシリアル化が使用される、単純、配列、オブジェクトの各データ型のみを含めることができます。 |
| **Automation アカウント** |親 Runbook は、同じ Automation アカウントの子 Runbook のみを使用できます。 |親 Runbook は、同じ Azure サブスクリプション、および接続されている別のサブスクリプションの、任意の Automation アカウントの子 Runbook を使用できます。 |
| **発行** |親 Runbook を発行する前に、子 Runbook を発行する必要があります。 |子 Runbook は、親 Runbook が開始される前の任意の時点で発行されます。 |

## <a name="invoke-a-child-runbook-using-inline-execution"></a>インライン実行を使用して子 Runbook を呼び出す

別の Runbook からインラインで Runbook を呼び出すには、Runbook の名前を使用し、アクティビティやコマンドレットに使用するのと同じパラメーター値を指定します。 同じ Automation アカウントのすべての Runbook は、他のすべての Runbook で同じ方法で使用されます。 親 Runbook は子 Runbook が完了するのを待ってから次の行に移動します。すべての出力は親に直接返されます。

Runbook をインラインで呼び出すと、親 Runbook と同じジョブで実行されます。 子 Runbook はジョブ履歴には表示されません。 子 Runbook からのすべての例外とストリーム出力は、親に関連付けられます。 この動作により、ジョブの数が減り、追跡とトラブルシューティングが容易になります。

Runbook を発行する場合、呼び出される子 Runbook はすべて発行済みでなければなりません。 これは、Azure Automation が Runbook をコンパイルするときに子 Runbook との関連付けを行うためです。 子 Runbook がまだ発行されていない場合、親 Runbook は正常に発行されたかのように見えますが、起動時に例外が発生します。 これが発生した場合、子 Runbook が正しく参照されるように親 Runbook を再発行できます。 子 Runbook が変更されても、関連付けは作成済みのため、親 Runbook を再発行する必要はありません。

インラインで呼び出された子 Runbook のパラメーターには、複合オブジェクトを含む任意のデータ型を使用できます。 Azure portal や [Start-AzAutomationRunbook](/powershell/module/Az.Automation/Start-AzAutomationRunbook) コマンドレットを使用して Runbook を起動したときのような、[JSON のシリアル化](start-runbooks.md#work-with-runbook-parameters)は行われません。

### <a name="runbook-types"></a>Runbook の種類

相互に呼び出すことができる Runbook の種類:

* [PowerShell Runbook](automation-runbook-types.md#powershell-runbooks) と[グラフィカル Runbook](automation-runbook-types.md#graphical-runbooks) は、どちらも PowerShell ベースであるため、相互にインラインで呼び出すことができます。
* [PowerShell ワークフロー Runbook](automation-runbook-types.md#powershell-workflow-runbooks) とグラフィカル PowerShell ワークフロー Runbook は、どちらも PowerShell ワークフロー ベースであるため、相互にインラインで呼び出すことができます。
* PowerShell の型と PowerShell ワークフローの型は、相互にインラインで呼び出すことはできず、`Start-AzAutomationRunbook` を使用する必要があります。

発行順序が重要になる状況:

Runbook の発行順序は、PowerShell ワークフロー Runbook とグラフィカル PowerShell ワークフロー Runbook に対してのみ重要です。

お使いのRunbook で、インライン実行を使用してグラフィカルまたは PowerShell ワークフローの子 Runbook を呼び出すと、その Runbook の名前が使用されます。 そのスクリプトがローカル ディレクトリにあることを指定するには、名前が `.\\` で始まる必要があります。

### <a name="example"></a>例

次の例では、複合オブジェクト、整数値、およびブール値を受け入れるテスト用の子 Runbook を開始します。 子 Runbook の出力は、変数に割り当てられます。 この場合は、子 Runbook は PowerShell ワークフロー Runbook です。

```azurepowershell-interactive
$vm = Get-AzVM –ResourceGroupName "LabRG" –Name "MyVM"
$output = PSWF-ChildRunbook –VM $vm –RepeatCount 2 –Restart $true
```

次に示すのは、子として PowerShell Runbook を使用する場合の例です。

```azurepowershell-interactive
$vm = Get-AzVM –ResourceGroupName "LabRG" –Name "MyVM"
$output = .\PS-ChildRunbook.ps1 –VM $vm –RepeatCount 2 –Restart $true
```

## <a name="start-a-child-runbook-using-a-cmdlet"></a>コマンドレットを使用して子 Runbook を開始する

> [!IMPORTANT]
> Runbook で、`Wait` パラメーターを指定した `Start-AzAutomationRunbook` コマンドレットを使用して子 Runbook を呼び出し、子 Runbook がオブジェクト結果を生成する場合、操作でエラーが発生することがあります。 このエラーを回避するには、[オブジェクト出力がある子 Runbook](troubleshoot/runbooks.md#child-runbook-object) に関する記事を参照し、[Get-AzAutomationJobOutputRecord](/powershell/module/az.automation/get-azautomationjoboutputrecord) コマンドレットを使用して結果をポーリングするロジックの実装方法を確認してください。

[Windows PowerShell での Runbook の開始](start-runbooks.md#start-a-runbook-with-powershell)に関する記事で説明されているように、`Start-AzAutomationRunbook` を使用して Runbook を開始できます。 このコマンドレットの使用には 2 つのモードがあります。 一方のモードでは、コマンドレットは、子 Runbook のジョブが作成されるとジョブ ID を返します。 *Wait* パラメーターを指定することでスクリプトによって有効になるもう一方のモードでは、コマンドレットは、子ジョブが完了して子 Runbook からの出力を返すまで待機します。

コマンドレットによって開始された子 Runbook のジョブは、親 Runbook ジョブとは別で実行されます。 この動作により、インラインで Runbook を開始する場合よりも多くのジョブが実行されるため、ジョブの追跡がより困難になります。親は、それぞれの完了を待たずに、複数の子 Runbook を非同期に開始できます。 複数の子 Runbook をインラインで呼び出すこの並列実行では、親 Runbook で [parallel キーワード](automation-powershell-workflow.md#use-parallel-processing)を使用する必要があります。

子 Runbook の出力は、タイミングが原因で親 Runbook に確実に返されません。 また、`$VerbosePreference` や `$WarningPreference` などの変数は、子 runbook に反映されない場合があります。 これらの問題を回避するために、`Start-AzAutomationRunbook` を `Wait` パラメーターと共に使用して、子 Runbook を個別の Automation ジョブとして開始することができます。 この手法では、子 Runbook が完了するまで親 Runbook がブロックされます。

親 Runbook が待機中にブロックされないようにしたい場合は、`Start-AzAutomationRunbook` を `Wait` パラメーターなしで使用して子 Runbook を開始することができます。 この場合、Runbook は [Get-AzAutomationJob](/powershell/module/az.automation/get-azautomationjob) を使用してジョブの完了を待機する必要があります。 結果を取得するには、[Get-AzAutomationJobOutput](/powershell/module/az.automation/get-azautomationjoboutput) および [Get-AzAutomationJobOutputRecord](/powershell/module/az.automation/get-azautomationjoboutputrecord) も使用する必要があります。

コマンドレットで開始した子 Runbook のパラメーターは、「 [Runbook のパラメーター](start-runbooks.md#work-with-runbook-parameters)」で説明されているように、ハッシュテーブルとして提供されます。 単純なデータ型のみを使用できます。 Runbook に複雑なデータ型のパラメーターが使用されている場合は、インラインで呼び出す必要があります。

子 Runbook を別のジョブとして開始するときにサブスクリプションのコンテキストが失われることがあります。 子 Runbook で特定の Azure サブスクリプションに対して Az モジュール コマンドレットを実行するには、そのサブスクリプションに対する子の認証が、親 Runbook とは独立して行われる必要があります。

同じ Automation アカウント内のジョブを複数のサブスクリプションで使用する場合、あるジョブのサブスクリプションを選択すると、他のジョブ用に現在選択されているサブスクリプションのコンテキストも変わる可能性があります。 この状況を回避するには、各 Runbook の先頭で `Disable-AzContextAutosave –Scope Process` を使用します。 このアクションだけで、コンテキストがその Runbook の実行に保存されます。

### <a name="example"></a>例

次の例では、`Wait` パラメーターを指定した `Start-AzAutomationRunbook` コマンドレットを使用し、パラメーターを指定して子 Runbook を開始し、完了まで待機します。 この例では、完了すると、子 Runbook からコマンドレットの出力が収集されます。 `Start-AzAutomationRunbook` を使用するには、スクリプトで Azure サブスクリプションに対して認証を行う必要があります。

```azurepowershell-interactive
# Ensure that the runbook does not inherit an AzContext
Disable-AzContextAutosave –Scope Process

# Connect to Azure with Run As account
$ServicePrincipalConnection = Get-AutomationConnection -Name 'AzureRunAsConnection'

Connect-AzAccount `
    -ServicePrincipal `
    -Tenant $ServicePrincipalConnection.TenantId `
    -ApplicationId $ServicePrincipalConnection.ApplicationId `
    -CertificateThumbprint $ServicePrincipalConnection.CertificateThumbprint

$AzureContext = Set-AzContext -SubscriptionId $ServicePrincipalConnection.SubscriptionID

$params = @{"VMName"="MyVM";"RepeatCount"=2;"Restart"=$true}

Start-AzAutomationRunbook `
    –AutomationAccountName 'MyAutomationAccount' `
    –Name 'Test-ChildRunbook' `
    -ResourceGroupName 'LabRG' `
    -AzContext $AzureContext `
    –Parameters $params –Wait
```

## <a name="next-steps"></a>次のステップ

* Runbook を実行するには、「[Azure Automation で Runbook を開始する](start-runbooks.md)」を参照してください。
* Runbook 操作の監視については、[Azure Automation 内での Runbook の出力とメッセージ](automation-runbook-output-and-messages.md)に関する記事を参照してください。
