---
title: Azure Automation で Runbook を開始する
description: この記事では、Azure Automation で Runbook を開始する方法について説明します。
services: automation
ms.subservice: process-automation
ms.date: 03/16/2018
ms.topic: conceptual
ms.openlocfilehash: 7cc1a25fbfc4d3f063c6fc6d225c3f5c7a4b5a3c
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "100572557"
---
# <a name="start-a-runbook-in-azure-automation"></a>Azure Automation で Runbook を開始する

次の表は、特定のシナリオに最も適している、Azure Automation の Runbook を開始する方法を決定するときに役立ちます。 この記事には、Azure ポータルと Windows PowerShell を使用して Runbook を開始する詳細情報が含まれます。 他の方法の詳細情報については、以下のリンクからアクセスできる他のドキュメントに記されています。

| **方法** | **特性** |
| --- | --- |
| [Azure Portal](#start-a-runbook-with-the-azure-portal) |<li>対話型ユーザー インターフェイスを使用する最も簡単な方法です。<br> <li>単純なパラメーター値を提供するフォームです。<br> <li>ジョブの状態を簡単に追跡できます。<br> <li>Azure のサインインで認証されたアクセスです。 |
| [Windows PowerShell](/powershell/module/azurerm.automation/start-azurermautomationrunbook) |<li>Windows PowerShell コマンドレットを使用してコマンドラインから呼び出します。<br> <li>複数のステップで自動化された機能に含めることができます。<br> <li>証明書または OAuth ユーザー プリンシパル/サービス プリンシパルで要求が認証されます。<br> <li>単純および複雑なパラメーター値を提供します。<br> <li>ジョブの状態を追跡できます。<br> <li>PowerShell コマンドレットをサポートするために必要なクライアントです。 |
| [Azure Automation API](/rest/api/automation/) |<li>最も柔軟性の高い方法ですが、最も複雑でもあります。<br> <li>HTTP 要求を発行することが可能なあらゆるカスタム コードから呼び出せます。<br> <li>証明書または OAuth ユーザー プリンシパル/サービス プリンシパルでの認証を要求します。<br> <li>単純および複雑なパラメーター値を提供します。 *API を使用して Python Runbook を呼び出している場合は、JSON ペイロードをシリアル化する必要があります。*<br> <li>ジョブの状態を追跡できます。 |
| [Webhook](automation-webhooks.md) |<li>1 つの HTTP 要求で Runbook を開始します。<br> <li>URL のセキュリティ トークンを使用して認証します。<br> <li>クライアントは Webhook の作成時に指定されたパラメーター値をオーバーライドできません。 Runbook は、HTTP 要求の詳細が含まれる 1 つのパラメーターを定義できます。<br> <li>Webhook URL でジョブの状態を追跡することはできません。 |
| [Azure アラートに応答する](../azure-monitor/alerts/alerts-overview.md) |<li>Azure アラートに応答して Runbook を開始します。<br> <li>runbook 用の Webhook とアラートへのリンクを構成します。<br> <li>URL のセキュリティ トークンを使用して認証します。 |
| [[スケジュール]](./shared-resources/schedules.md) |<li>時間、日、週、または月単位のスケジュールで Runbook を自動的に開始できます。<br> <li>Azure ポータル、PowerShell コマンドレット、または Azure API を使用してスケジュールを設定します。<br> <li>スケジュールで使用するパラメーター値を提供します。 |
| [別の Runbook から](automation-child-runbooks.md) |<li>別の Runbook の活動として Runbook を使用します。<br> <li>複数の Runbook で使用する機能に役立ちます。<br> <li>子 Runbook にパラメーター値を指定し、出力を親 Runbook で使用します。 |

次の図は、Runbook のライフ サイクルにおけるプロセスをステップごとに詳細に示したものです。 これには、Azure Automation で Runbook を開始するためのさまざまな方法、Hybrid Runbook Worker で Azure Automation Runbook を実行するために必要な各コンポーネント、異なるコンポーネント間でのやり取りなどが示されています。 自社のデータセンターで Automation Runbook を実行する方法については、 [Hybrid Runbook Worker](automation-hybrid-runbook-worker.md)

![Runbook のアーキテクチャ](media/automation-starting-runbook/runbooks-architecture.png)

## <a name="work-with-runbook-parameters"></a>Runbook のパラメーターを使用する

Azure Portal または Windows PowerShell から Runbook を開始する場合、命令は、Azure Automation Web サービスを通じて送信されます。 このサービスでは、複雑なデータ型を持つパラメーターはサポートされていません。 複合型のパラメーター値を指定する必要がある場合、「 [Azure Automation での子 Runbook](automation-child-runbooks.md)」で説明されているように別の Runbook からインラインで呼び出す必要があります。

Azure Automation Web サービスは、次のセクションで説明されているように特定のデータ型を使用してパラメーターに対して特殊な機能を提供します。

### <a name="named-values"></a>名前付きの値

パラメーターのデータ型が [object] の場合、 *{Name1:'Value1', Name2:'Value2', Name3:'Value3'}* という JSON 形式を使用して名前付きの値の一覧を送信できます。 これらの値は単純型にする必要があります。 Runbook は、それぞれの名前付き値に対応するプロパティが設定された [PSCustomObject](/dotnet/api/system.management.automation.pscustomobject) としてパラメーターを受け取ります。

user というパラメーターを受け入れる次のテスト Runbook について考慮してください。

```powershell
Workflow Test-Parameters
{
   param (
      [Parameter(Mandatory=$true)][object]$user
   )
    $userObject = $user | ConvertFrom-JSON
    if ($userObject.Show) {
        foreach ($i in 1..$userObject.RepeatCount) {
            $userObject.FirstName
            $userObject.LastName
        }
    }
}
```

user パラメーターに対して、次のテキストを使用できます。

```json
{FirstName:'Joe',LastName:'Smith',RepeatCount:'2',Show:'True'}
```

次の出力が生成されます。

```output
Joe
Smith
Joe
Smith
```

### <a name="arrays"></a>配列

パラメーターが [array] や [string[]] などの配列の場合、 *[Value1, Value2, Value3]* という JSON 形式を使用して値の一覧を送信できます。 これらの値は単純型にする必要があります。

*user* というパラメーターを受け入れる次のテスト Runbook について考慮してください。

```powershell
Workflow Test-Parameters
{
   param (
      [Parameter(Mandatory=$true)][array]$user
   )
    if ($user[3]) {
        foreach ($i in 1..$user[2]) {
            $ user[0]
            $ user[1]
        }
    }
}
```

user パラメーターに対して、次のテキストを使用できます。

```input
["Joe","Smith",2,true]
```

次の出力が生成されます。

```output
Joe
Smith
Joe
Smith
```

### <a name="credentials"></a>資格情報

パラメーターのデータ型が `PSCredential` の場合、Azure Automation の[資格情報資産](./shared-resources/credentials.md)の名前を指定できます。 Runbook は、指定した名前を持つ資格情報を取得します。 次のテスト Runbook は、`credential` というパラメーターを受け入れます。

```powershell
Workflow Test-Parameters
{
   param (
      [Parameter(Mandatory=$true)][PSCredential]$credential
   )
   $credential.UserName
}
```

`My Credential` という資格情報資産がある場合、次のテキストを user パラメーターに使用できます。

```input
My Credential
```

資格情報のユーザー名が `jsmith` である場合、次の出力が表示されます。

```output
jsmith
```

## <a name="start-a-runbook-with-the-azure-portal"></a>Azure portal で Runbook を開始する

1. Azure portal で、 **[Automation]** を選択し、次に Automation アカウントの名前をクリックします。
2. [ハブ] メニューで、 **[Runbook]** を選択します。
3. [Runbook] ページで Runbook を選択し、 **[開始]** をクリックします。
4. Runbook にパラメーターがある場合は、各パラメーターのテキスト ボックスに値を指定するように求めるプロンプトが表示されます。 パラメーターについて詳しくは、「[Runbook のパラメーター](#work-with-runbook-parameters)」をご覧ください。
5. [ジョブ] ペインで、Runbook ジョブの状態を表示することができます。

## <a name="start-a-runbook-with-powershell"></a>PowerShell で Runbook を開始する

[Start-AzAutomationRunbook](/powershell/module/az.automation/start-azautomationrunbook) を使用して、Windows PowerShell で Runbook を開始できます。 次のサンプル コードは、**Test-Runbook** という Runbook を開始します。

```azurepowershell-interactive
Start-AzAutomationRunbook -AutomationAccountName "MyAutomationAccount" -Name "Test-Runbook" -ResourceGroupName "ResourceGroup01"
```

`Start-AzAutomationRunbook` は、Runbook の開始後に状態を追跡するために使用できるジョブ オブジェクトを返します。 次に、このジョブ オブジェクトを [Get-AzAutomationJob](/powershell/module/Az.Automation/Get-AzAutomationJob) で使用してジョブの状態を判断し、[Get-AzAutomationJobOutput](/powershell/module/az.automation/get-azautomationjoboutput) で使用してその出力を取得できます。 次の例は、**Test-Runbook** という Runbook を開始し、完了するまで待ってからその出力を表示します。

```azurepowershell-interactive
$runbookName = "Test-Runbook"
$ResourceGroup = "ResourceGroup01"
$AutomationAcct = "MyAutomationAccount"

$job = Start-AzAutomationRunbook –AutomationAccountName $AutomationAcct -Name $runbookName -ResourceGroupName $ResourceGroup

$doLoop = $true
While ($doLoop) {
   $job = Get-AzAutomationJob –AutomationAccountName $AutomationAcct -Id $job.JobId -ResourceGroupName $ResourceGroup
   $status = $job.Status
   $doLoop = (($status -ne "Completed") -and ($status -ne "Failed") -and ($status -ne "Suspended") -and ($status -ne "Stopped"))
}

Get-AzAutomationJobOutput –AutomationAccountName $AutomationAcct -Id $job.JobId -ResourceGroupName $ResourceGroup –Stream Output
```

Runbook にパラメーターが必要な場合は、[ハッシュテーブル](/powershell/module/microsoft.powershell.core/about/about_hash_tables)として提供する必要があります。 ハッシュテーブルのキーはパラメーター名と一致する必要があり、値はパラメーターの値です。 次の例は、FirstName と LastName という 2 つの文字列パラメーターと、RepeatCount という名前の整数、および Show という名前のブール型パラメーターが含まれる Runbook を開始する方法を示します。 パラメーターについて詳しくは、「[Runbook のパラメーター](#work-with-runbook-parameters)」をご覧ください。

```azurepowershell-interactive
$params = @{"FirstName"="Joe";"LastName"="Smith";"RepeatCount"=2;"Show"=$true}
Start-AzAutomationRunbook –AutomationAccountName "MyAutomationAccount" –Name "Test-Runbook" -ResourceGroupName "ResourceGroup01" –Parameters $params
```

## <a name="next-steps"></a>次のステップ

* Runbook の管理の詳細については「[Azure Automation で Runbook を管理する](manage-runbooks.md)」を参照してください。
* PowerShell の詳細については、[PowerShell のドキュメント](/powershell/scripting/overview)を参照してください。
* Runbook の実行に関する問題のトラブルシューティングについては、[Runbook の問題のトラブルシューティング](troubleshoot/runbooks.md)に関するページを参照してください。
