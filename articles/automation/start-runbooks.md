---
title: Azure Automation で Runbook を開始する
description: Azure Automation の Runbook を開始するために使用できる各種方法についてまとめ、Azure ポータルと Windows PowerShell の両方の詳細な使用方法について説明します。
services: automation
ms.service: automation
ms.subservice: process-automation
author: mgoedtel
ms.author: magoedte
ms.date: 03/16/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: a6ef8d81e8a2845e62bf25d0bba4d6967cca65a4
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/05/2019
ms.locfileid: "74849413"
---
# <a name="start-a-runbook-in-azure-automation"></a>Azure Automation で Runbook を開始する

次の表は、特定のシナリオに最も適している、Azure Automation の Runbook を開始する方法を決定するときに役立ちます。 この記事には、Azure ポータルと Windows PowerShell を使用して Runbook を開始する詳細情報が含まれます。 他の方法の詳細情報については、以下のリンクからアクセスできる他のドキュメントに記されています。

| **メソッド** | **特性** |
| --- | --- |
| [Azure Portal](#start-a-runbook-with-the-azure-portal) |<li>対話型ユーザー インターフェイスを使用する最も簡単な方法です。<br> <li>単純なパラメーター値を提供するフォームです。<br> <li>ジョブの状態を簡単に追跡できます。<br> <li>Azure のサインインで認証されたアクセスです。 |
| [Windows PowerShell](/powershell/module/azurerm.automation/start-azurermautomationrunbook) |<li>Windows PowerShell コマンドレットを使用してコマンドラインから呼び出します。<br> <li>複数のステップで自動化されたソリューションに含めることができます。<br> <li>証明書または OAuth ユーザー プリンシパル/サービス プリンシパルで要求が認証されます。<br> <li>単純および複雑なパラメーター値を提供します。<br> <li>ジョブの状態を追跡できます。<br> <li>PowerShell コマンドレットをサポートするために必要なクライアントです。 |
| [Azure Automation API](/rest/api/automation/) |<li>最も柔軟性の高い方法ですが、最も複雑でもあります。<br> <li>HTTP 要求を発行することが可能なあらゆるカスタム コードから呼び出せます。<br> <li>証明書または OAuth ユーザー プリンシパル/サービス プリンシパルでの認証を要求します。<br> <li>単純および複雑なパラメーター値を提供します。 *API を使用して Python Runbook を呼び出している場合は、JSON ペイロードをシリアル化する必要があります。*<br> <li>ジョブの状態を追跡できます。 |
| [Webhook](automation-webhooks.md) |<li>1 つの HTTP 要求で Runbook を開始します。<br> <li>URL のセキュリティ トークンを使用して認証します。<br> <li>クライアントは Webhook の作成時に指定されたパラメーター値をオーバーライドできません。 Runbook は、HTTP 要求の詳細が含まれる 1 つのパラメーターを定義できます。<br> <li>Webhook URL でジョブの状態を追跡することはできません。 |
| [Azure アラートに応答する](../log-analytics/log-analytics-alerts.md) |<li>Azure アラートに応答して Runbook を開始します。<br> <li>runbook 用の Webhook とアラートへのリンクを構成します。<br> <li>URL のセキュリティ トークンを使用して認証します。 |
| [スケジュール](automation-schedules.md) |<li>時間、日、週、または月単位のスケジュールで Runbook を自動的に開始できます。<br> <li>Azure ポータル、PowerShell コマンドレット、または Azure API を使用してスケジュールを設定します。<br> <li>スケジュールで使用するパラメーター値を提供します。 |
| [別の Runbook から](automation-child-runbooks.md) |<li>別の Runbook の活動として Runbook を使用します。<br> <li>複数の Runbook で使用する機能に役立ちます。<br> <li>子 Runbook にパラメーター値を指定し、出力を親 Runbook で使用します。 |

次の図は、Runbook のライフ サイクルにおけるプロセスをステップごとに詳細に示したものです。 これには、Azure Automation で Runbook を開始するためのさまざまな方法、Hybrid Runbook Worker で Azure Automation Runbook を実行するために必要な各コンポーネント、異なるコンポーネント間でのやり取りなどが示されています。 自社のデータセンターで Automation Runbook を実行する方法については、 [Hybrid Runbook Worker](automation-hybrid-runbook-worker.md)

![Runbook のアーキテクチャ](media/automation-starting-runbook/runbooks-architecture.png)

## <a name="start-a-runbook-with-the-azure-portal"></a>Azure portal で Runbook を開始する

1. Azure Portal で、 **[Automation]** を選択し、Automation アカウントの名前をクリックします。
2. [ハブ] メニューで、 **[Runbook]** を選択します。
3. **[Runbook]** ページで Runbook を選択し、 **[開始]** をクリックします。
4. Runbook にパラメーターがある場合は、各パラメーターのテキスト ボックスに値を指定するように求めるプロンプトが表示されます。 パラメーターについて詳しくは、「[Runbook のパラメーター](#runbook-parameters)」をご覧ください。
5. **[ジョブ]** ページで、Runbook ジョブの状態を表示することができます。

## <a name="start-a-runbook-with-powershell"></a>PowerShell で Runbook を開始する

[Start-AzureRmAutomationRunbook](https://docs.microsoft.com/powershell/module/azurerm.automation/start-azurermautomationrunbook) を使用して、Windows PowerShell で Runbook を開始できます。 次のサンプル コードは、Test-Runbook という Runbook を開始します。

```azurepowershell-interactive
Start-AzureRmAutomationRunbook -AutomationAccountName "MyAutomationAccount" -Name "Test-Runbook" -ResourceGroupName "ResourceGroup01"
```

Start-AzureRmAutomationRunbook は、Runbook の開始後にその状態を追跡するために使用できるジョブ オブジェクトを返します。 このジョブ オブジェクトは、[Get-AzureRmAutomationJob](https://docs.microsoft.com/powershell/module/azurerm.automation/get-azurermautomationjob) で使用するとジョブの状態を確認でき、[Get-AzureRmAutomationJobOutput](https://docs.microsoft.com/powershell/module/azurerm.automation/get-azurermautomationjoboutput) で使用すると出力を取得できます。 次のサンプル コードは、Test-Runbook という Runbook を開始し、完了するまで待機してからその出力を表示します。

```azurepowershell-interactive
$runbookName = "Test-Runbook"
$ResourceGroup = "ResourceGroup01"
$AutomationAcct = "MyAutomationAccount"

$job = Start-AzureRmAutomationRunbook –AutomationAccountName $AutomationAcct -Name $runbookName -ResourceGroupName $ResourceGroup

$doLoop = $true
While ($doLoop) {
   $job = Get-AzureRmAutomationJob –AutomationAccountName $AutomationAcct -Id $job.JobId -ResourceGroupName $ResourceGroup
   $status = $job.Status
   $doLoop = (($status -ne "Completed") -and ($status -ne "Failed") -and ($status -ne "Suspended") -and ($status -ne "Stopped"))
}

Get-AzureRmAutomationJobOutput –AutomationAccountName $AutomationAcct -Id $job.JobId -ResourceGroupName $ResourceGroup –Stream Output
```

Runbook にパラメーターが必要な場合は、[ハッシュテーブル](https://technet.microsoft.com/library/hh847780.aspx)として提供する必要があります。 ハッシュテーブルのキーはパラメーター名と一致する必要があり、値はパラメーターの値です。 次の例は、FirstName と LastName という 2 つの文字列パラメーターと、RepeatCount という名前の整数、および Show という名前のブール型パラメーターが含まれる Runbook を開始する方法を示します。 パラメーターについて詳しくは、次の「[Runbook のパラメーター](#runbook-parameters)」をご覧ください。

```azurepowershell-interactive
$params = @{"FirstName"="Joe";"LastName"="Smith";"RepeatCount"=2;"Show"=$true}
Start-AzureRmAutomationRunbook –AutomationAccountName "MyAutomationAccount" –Name "Test-Runbook" -ResourceGroupName "ResourceGroup01" –Parameters $params
```

## <a name="runbook-parameters"></a>Runbook のパラメーター

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

*user*というパラメーターを受け入れる次のテスト Runbook について考慮してください。

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

パラメーターのデータ型が **PSCredential**の場合、Azure Automation の [資格情報資産](automation-credentials.md)の名前を指定できます。 Runbook は、指定した名前を持つ資格情報を取得します。

credential というパラメーターを受け入れる次のテスト Runbook について考慮してください。

```powershell
Workflow Test-Parameters
{
   param (
      [Parameter(Mandatory=$true)][PSCredential]$credential
   )
   $credential.UserName
}
```

*My Credential*という資格情報資産がある場合、user パラメーターで次のテキストを使用できます。

```input
My Credential
```

資格情報のユーザー名が *jsmith*であると仮定すると、次の出力が生成されます。

```output
jsmith
```

## <a name="next-steps"></a>次の手順

* この記事の Runbook アーキテクチャは、Hybrid Runbook Worker で Azure とオンプレミスのリソースを管理する Runbook の概要を示しています。 自社のデータセンターで Automation Runbook を実行する方法については、 [Hybrid Runbook Worker](automation-hybrid-runbook-worker.md)に関する記事をご覧ください。
* 他の Runbook で特定の関数または一般的な関数に使用されるモジュールの Runbook の作成方法については、 [子 Runbook](automation-child-runbooks.md)に関する記事をご覧ください。
* PowerShell (言語リファレンス、学習モジュールを含む) の詳細については、[PowerShell ドキュメント](https://docs.microsoft.com/powershell/scripting/overview)に関するページを参照してください。
