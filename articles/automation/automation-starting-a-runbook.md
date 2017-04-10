---
title: "Azure Automation での Runbook の開始 | Microsoft Docs"
description: "Azure Automation の Runbook を開始するために使用できる各種方法についてまとめ、Azure ポータルと Windows PowerShell の両方の詳細な使用方法について説明します。"
services: automation
documentationcenter: 
author: mgoedtel
manager: jwhit
editor: tysonn
ms.assetid: 6ee756b4-9200-4eb2-9bda-ec156853803b
ms.service: automation
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/08/2016
ms.author: magoedte;bwren
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 0bacd2ccb08d20c4e0457d9fc83468e747cde870


---
# <a name="starting-a-runbook-in-azure-automation"></a>Azure Automation での Runbook の開始
次の表は、特定のシナリオに最も適している、Azure Automation の Runbook を開始する方法を決定するときに役立ちます。 この記事には、Azure ポータルと Windows PowerShell を使用して Runbook を開始する詳細情報が含まれます。 他の方法の詳細情報については、以下のリンクからアクセスできる他のドキュメントに記されています。

| **方法** | **特性** |
| --- | --- |
| [Azure ポータル](#starting-a-runbook-with-the-azure-portal) |<li>対話型ユーザー インターフェイスを使用する最も簡単な方法です。<br> <li>単純なパラメーター値を提供するフォームです。<br> <li>ジョブの状態を簡単に追跡できます。<br> <li>アクセスを Azure ログオンで認証します。 |
| [Windows PowerShell](https://msdn.microsoft.com/library/dn690259.aspx) |<li>Windows PowerShell コマンドレットを使用してコマンドラインから呼び出します。<br> <li>複数のステップで自動化されたソリューションに含めることができます。<br> <li>証明書または OAuth ユーザー プリンシパル/サービス プリンシパルで要求が認証されます。<br> <li>単純および複雑なパラメーター値を提供します。<br> <li>ジョブの状態を追跡できます。<br> <li>PowerShell コマンドレットをサポートするために必要なクライアントです。 |
| [Azure Automation API](https://msdn.microsoft.com/library/azure/mt662285.aspx) |<li>最も柔軟性の高い方法ですが、最も複雑でもあります。<br> <li>HTTP 要求を発行することが可能なあらゆるカスタム コードから呼び出せます。<br> <li>証明書または OAuth ユーザー プリンシパル/サービス プリンシパルでの認証を要求します。<br> <li>単純および複雑なパラメーター値を提供します。<br> <li>ジョブの状態を追跡できます。 |
| [Webhook](automation-webhooks.md) |<li>1 つの HTTP 要求で Runbook を開始します。<br> <li>URL のセキュリティ トークンを使用して認証します。<br> <li>クライアントは Webhook の作成時に指定されたパラメーター値を上書きできません。 Runbook は、HTTP 要求の詳細が含まれる 1 つのパラメーターを定義できます。<br> <li>Webhook URL でジョブの状態を追跡することはできません。 |
| [Azure アラートに応答する](../log-analytics/log-analytics-alerts.md) |<li>Azure アラートに応答して Runbook を開始します。<br> <li>runbook 用の Webhook とアラートへのリンクを構成します。<br> <li>URL のセキュリティ トークンを使用して認証します。<br> <li>現時点ではメトリックに関するアラートのみサポートします。 |
| [スケジュール](automation-schedules.md) |<li>時間、日、週の単位のスケジュールで Runbook を自動的に開始できます。<br> <li>Azure ポータル、PowerShell コマンドレット、または Azure API を使用してスケジュールを設定します。<br> <li>スケジュールで使用するパラメーター値を提供します。 |
| [別の Runbook から](automation-child-runbooks.md) |<li>別の Runbook の活動として Runbook を使用します。<br> <li>複数の Runbook で使用する機能に役立ちます。<br> <li>子 Runbook にパラメーター値を指定し、出力を親 Runbook で使用します。 |

次の図は、Runbook のライフ サイクルにおけるプロセスをステップごとに詳細に示したものです。 これには、Azure Automation で Runbook を開始するためのさまざまな方法、Hybrid Runbook Worker で Azure Automation Runbook を実行するために必要な各コンポーネント、異なるコンポーネント間でのやり取りなどが示されています。 自社のデータセンターで Automation Runbook を実行する方法については、 [Hybrid Runbook Worker](automation-hybrid-runbook-worker.md)

![Runbook のアーキテクチャ](media/automation-starting-runbook/runbooks-architecture.png)

## <a name="starting-a-runbook-with-the-azure-portal"></a>Azure ポータルでの Runbook の開始
1. Azure ポータルで、 **[Automation]** を選択し、次に Automation アカウントの名前をクリックします。
2. **[Runbook]** タブを選択します。
3. Runbook を選択し、**[開始]** をクリックします。
4. Runbook にパラメーターがある場合は、各パラメーターのテキスト ボックスに値を指定するように求めるプロンプトが表示されます。 パラメーターについて詳しくは、「 [Runbook のパラメーター](#Runbook-parameters) 」をご覧ください。
5. **[開始]** Runbook メッセージの隣にある **[ジョブの表示]** を選択するか、Runbook の **[ジョブ]** タブを選択してその Runbook ジョブの状態を表示します。

## <a name="starting-a-runbook-with-the-azure-portal"></a>Azure ポータルでの Runbook の開始
1. Automation アカウントから、**[Runbook]** 部分をクリックして **[Runbook]** ブレードを開きます。
2. Runbook をクリックすると、その **[Runbook]** ブレードが開きます。
3. **[開始]**をクリックします。
4. Runbook がパラメーターを持っていない場合は、開始するかどうかを確認するプロンプトが表示されます。 Runbook にパラメーターがある場合、 **[Runbook の開始]** ブレードが開くので、パラメーター値を指定できます。 パラメーターについて詳しくは、「 [Runbook のパラメーター](#Runbook-parameters) 」をご覧ください。
5. **[ジョブ]** ブレードが開くので、ジョブの状態を追跡できます。

## <a name="starting-a-runbook-with-windows-powershell"></a>Windows PowerShell での Runbook の開始
[Start-AzureRmAutomationRunbook](https://msdn.microsoft.com/library/mt603661.aspx) を使用して、Windows PowerShell で Runbook を開始できます。 次のサンプル コードは、Test-Runbook という Runbook を開始します。

```
Start-AzureRmAutomationRunbook -AutomationAccountName "MyAutomationAccount" -Name "Test-Runbook" -ResourceGroupName "ResourceGroup01"
```

Start-AzureRmAutomationRunbook は、Runbook の開始後にその状態を追跡するために使用できるジョブ オブジェクトを返します。 このジョブ オブジェクトは、[Get-AzureRmAutomationJob](https://msdn.microsoft.com/library/mt619440.aspx) で使用するとジョブの状態を確認でき、[Get-AzureRmAutomationJobOutput](https://msdn.microsoft.com/library/mt603476.aspx) で使用すると出力を取得できます。 次のサンプル コードは、Test-Runbook という Runbook を開始し、完了するまで待機してからその出力を表示します。

```
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

Runbook でパラメーターが必要な場合には、それらを [Hashtable](http://technet.microsoft.com/library/hh847780.aspx) として指定する必要があります。その Hashtable のキーがパラメーター名、値がパラメーター値になります。 次の例は、FirstName と LastName という 2 つの文字列パラメーターと、RepeatCount という名前の整数、および Show という名前のブール型パラメーターが含まれる Runbook を開始する方法を示します。 パラメーターについて詳しくは、次に取り上げる「[Runbook のパラメーター](#Runbook-parameters)」をご覧ください。

```
$params = @{"FirstName"="Joe";"LastName"="Smith";"RepeatCount"=2;"Show"=$true}
Start-AzureRmAutomationRunbook –AutomationAccountName "MyAutomationAccount" –Name "Test-Runbook" -ResourceGroupName "ResourceGroup01" –Parameters $params
```

## <a name="runbook-parameters"></a>Runbook のパラメーター
Azure ポータルまたは Windows PowerShell から Runbook を開始する場合、命令は、Azure Automation Web サービスを通じて送信されます。 このサービスは、複雑なデータ型を持つパラメーターをサポートしていません。 複合型のパラメーター値を指定する必要がある場合、「 [Azure Automation での子 Runbook](automation-child-runbooks.md)」で説明されているように別の Runbook からインラインで呼び出す必要があります。

Azure Automation Web サービスは、次のセクションで説明されているように特定のデータ型を使用してパラメーターに対して特殊な機能を提供します。

### <a name="named-values"></a>名前付きの値
パラメーターのデータ型が [object] の場合、 *{Name1:'Value1', Name2:'Value2', Name3:'Value3'}*という JSON 形式を使用して名前付きの値の一覧を送信できます。 これらの値は単純型にする必要があります。 Runbook は、それぞれの名前付き値に対応するプロパティが設定された [PSCustomObject](https://msdn.microsoft.com/library/system.management.automation.pscustomobject%28v=vs.85%29.aspx) としてパラメーターを受け取ります。

user というパラメーターを受け入れる次のテスト Runbook について考慮してください。

```
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

```
{FirstName:'Joe',LastName:'Smith',RepeatCount:'2',Show:'True'}
```

次の出力が生成されます。

```
Joe
Smith
Joe
Smith
```

### <a name="arrays"></a>配列
パラメーターが [array] や [string] などの配列の場合、*[Value1,Value2,Value3]*という JSON 形式を使用して値の一覧を送信できます。 これらの値は単純型にする必要があります。

*user*というパラメーターを受け入れる次のテスト Runbook について考慮してください。

```
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

```
["Joe","Smith",2,true]
```

次の出力が生成されます。

```
Joe
Smith
Joe
Smith
```

### <a name="credentials"></a>資格情報
パラメーターのデータ型が **PSCredential**の場合、Azure Automation の [資格情報資産](automation-credentials.md)の名前を指定できます。 Runbook は、指定した名前を持つ資格情報を取得します。

credential というパラメーターを受け入れる次のテスト Runbook について考慮してください。

```
Workflow Test-Parameters
{
   param (
      [Parameter(Mandatory=$true)][PSCredential]$credential
   )
   $credential.UserName
}
```

*My Credential*という資格情報資産がある場合、user パラメーターで次のテキストを使用できます。

```
My Credential
```

資格情報のユーザー名が *jsmith*であると仮定すると、次の出力が生成されます。

```
jsmith
```

## <a name="next-steps"></a>次のステップ
* この記事の Runbook アーキテクチャは、Hybrid Runbook Worker で Azure とオンプレミスのリソースを管理する Runbook の概要を示しています。  自社のデータセンターで Automation Runbook を実行する方法については、 [Hybrid Runbook Worker](automation-hybrid-runbook-worker.md)に関する記事をご覧ください。
* 他の Runbook で特定の関数または一般的な関数に使用されるモジュールの Runbook の作成方法については、 [子 Runbook](automation-child-runbooks.md)に関する記事をご覧ください。




<!--HONumber=Nov16_HO3-->


