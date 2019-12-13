---
title: Azure での Update Management のデプロイで事前および事後スクリプトを構成する
description: この記事では、更新プログラムの展開のための事前スクリプトおよび事後スクリプトを構成および管理する方法について説明します。
services: automation
ms.service: automation
ms.subservice: update-management
author: mgoedtel
ms.author: magoedte
ms.date: 05/17/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: a5d68b64fc24577621c82be62f833c356e8fb9c2
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/05/2019
ms.locfileid: "74850246"
---
# <a name="manage-pre-and-post-scripts"></a>事前および事後スクリプトを管理する

事前スクリプトおよび事後スクリプトを使用すると、更新プログラムの展開の前 (事前タスク) と後 (事後タスク) に Azure Automation アカウントで PowerShell Runbook を実行できます。 事前および事後スクリプトはローカルではなく、Azure コンテキストで実行されます。 事前スクリプトは、更新プログラムのデプロイの開始時に実行されます。 事後スクリプトは、展開の最後に、構成されているすべての再起動の後で実行されます。

## <a name="runbook-requirements"></a>Runbook の要件

Runbook が事前または事後スクリプトとして使用されるようにするには、その Runbook を Automation アカウントにインポートして発行する必要があります。 このプロセスの詳細については、「[Runbook を発行する](manage-runbooks.md#publish-a-runbook)」を参照してください。

## <a name="using-a-pre-script-or-post-script"></a>事前スクリプトまたは事後スクリプトを使用する

更新プログラムの展開において事前スクリプトまたは事後スクリプトを使用するには、更新プログラムの展開の作成から始めます。 **[事前スクリプトと事後スクリプト]** を選択します。 この操作で、 **[Select Pre-scripts + Post-scripts] (事前スクリプト + 事後スクリプトの選択)** ページが開きます。

![スクリプトを選択する](./media/pre-post-scripts/select-scripts.png)

使用するスクリプトを選択します。 この例では、**UpdateManagement-TurnOnVms** Runbook を使用します。 Runbook を選択すると、 **[スクリプトの構成]** ページが開きます。 **[事前スクリプト]** を選択してから、 **[OK]** を選択します。

**UpdateManagement-TurnOffVms** スクリプトについて、このプロセスを繰り返します。 ただし、 **[スクリプトの種類]** を選択した場合は、 **[事後スクリプト]** を選択します。

これで、 **[選択された項目]** セクションに、ご自身が選択した両方のスクリプトが表示されます。 1 つは事前スクリプトで、もう 1 つは事後スクリプトです。

![[Selected items] (選択された項目)](./media/pre-post-scripts/selected-items.png)

更新プログラムの展開の構成を終了します。

更新プログラムの展開が完了したら、 **[更新プログラムの展開]** に移動して結果を表示できます。 見てわかるように、事前スクリプトと事後スクリプトの状態が表示されます。

![更新プログラムの結果](./media/pre-post-scripts/update-results.png)

実行された更新プログラムの展開を選択すると、事前および事後スクリプトの追加の詳細情報が表示されます。 実行の時点でのスクリプト ソースへのリンクが表示されます。

![展開の実行の結果](./media/pre-post-scripts/deployment-run.png)

## <a name="passing-parameters"></a>パラメーターを渡す

事前および事後スクリプトを構成する場合、Runbook のスケジュール設定のように、パラメーターを渡すことができます。 パラメーターは、更新プログラムの展開の作成の時点で定義されます。 事前および事後スクリプトでは、次の型がサポートされます。

* [char]
* [byte]
* [int]
* [long]
* [decimal]
* [single]
* [double]
* [DateTime]
* [string]

別のオブジェクト型が必要な場合は、Runbook 内の独自のロジックで別の方にキャストできます。

標準の Runbook パラメーターに加えて、もう 1 つの次のパラメーターが表示されます。**SoftwareUpdateConfigurationRunContext**

このパラメーターは JSON 文字列であるため、事前または事後スクリプト内にパラメーターを定義すると、更新プログラムが展開されることによって、自動的に渡されます。 パラメーターには、[SoftwareUpdateconfigurations API](/rest/api/automation/softwareupdateconfigurations/getbyname#updateconfiguration) から返された情報のサブセットである、更新プログラムの展開に関する情報が含まれます。 

次の表に、変数内で指定されるプロパティを示します。

### <a name="softwareupdateconfigurationruncontext-properties"></a>SoftwareUpdateConfigurationRunContext プロパティ

|プロパティ  |説明  |
|---------|---------|
|SoftwareUpdateConfigurationName     | ソフトウェア更新構成の名前。        |
|SoftwareUpdateConfigurationRunId     | 実行の一意の ID。        |
|SoftwareUpdateConfigurationSettings     | ソフトウェア更新構成に関連したプロパティのコレクション。         |
|SoftwareUpdateConfigurationSettings.operatingSystem     | 更新プログラムの展開の対象となるオペレーティング システム。         |
|SoftwareUpdateConfigurationSettings.duration     | ISO8601 に従った `PT[n]H[n]M[n]S` として実行される更新プログラムの展開の最大期間。"*メンテナンス期間*" とも呼ばれる。          |
|SoftwareUpdateConfigurationSettings.Windows     | Windows コンピューターに関連したプロパティのコレクション。         |
|SoftwareUpdateConfigurationSettings.Windows.excludedKbNumbers     | 更新プログラムの展開から除外される KB の一覧。        |
|SoftwareUpdateConfigurationSettings.Windows.includedUpdateClassifications     | 更新プログラムの展開のために選択された更新プログラムの分類。        |
|SoftwareUpdateConfigurationSettings.Windows.rebootSetting     | 更新プログラムの展開のための再起動設定。        |
|azureVirtualMachines     | 更新プログラムの展開における Azure VM 用の resourceId の一覧。        |
|nonAzureComputerNames|更新プログラムの展開における Azure 以外のコンピューターの FQDN の一覧。|

次の例は、**SoftwareUpdateConfigurationRunContext** パラメーターに渡される JSON 文字列です。

```json
"SoftwareUpdateConfigurationRunContext":{
      "SoftwareUpdateConfigurationName":"sampleConfiguration",
      "SoftwareUpdateConfigurationRunId":"00000000-0000-0000-0000-000000000000",
      "SoftwareUpdateConfigurationSettings":{
         "operatingSystem":"Windows",
         "duration":"PT2H0M",
         "windows":{
            "excludedKbNumbers":[
               "168934",
               "168973"
            ],
            "includedUpdateClassifications":"Critical",
            "rebootSetting":"IfRequired"
         },
         "azureVirtualMachines":[
            "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myresources/providers/Microsoft.Compute/virtualMachines/vm-01",
            "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myresources/providers/Microsoft.Compute/virtualMachines/vm-02",
            "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myresources/providers/Microsoft.Compute/virtualMachines/vm-03"
         ],
         "nonAzureComputerNames":[
            "box1.contoso.com",
            "box2.contoso.com"
         ]
      }
   }
```

すべてのプロパティの完全な例は、「[Get software update configuration by name (ソフトウェア更新構成を名前別に取得する)](/rest/api/automation/softwareupdateconfigurations/getbyname#examples)」にあります。

> [!NOTE]
> `SoftwareUpdateConfigurationRunContext` オブジェクトには、マシン用の重複するエントリを含めることができます。 これにより、同じマシン上で事前および事後スクリプトが複数回実行される可能性があります。 この動作を回避するには、`Sort-Object -Unique` を使用して、スクリプト内の一意の VM 名だけを選択します。


## <a name="stopping-a-deployment"></a>デプロイの停止

事前スクリプトに基づくデプロイを停止する場合は、例外を[スロー](automation-runbook-execution.md#throw)する必要があります。 そうしないと、デプロイと事後スクリプトが引き続き実行されます。 次のコード スニペットは、例外をスローする方法を示しています。

```powershell
#In this case, we want to terminate the patch job if any run fails.
#This logic might not hold for all cases - you might want to allow success as long as at least 1 run succeeds
foreach($summary in $finalStatus)
{
    if ($summary.Type -eq "Error")
    {
        #We must throw in order to fail the patch deployment.
        throw $summary.Summary
    }
}
```

## <a name="samples"></a>サンプル

事前および事後スクリプトのサンプルは [Script Center Gallery](https://gallery.technet.microsoft.com/scriptcenter/site/search?f%5B0%5D.Type=RootCategory&f%5B0%5D.Value=WindowsAzure&f%5B0%5D.Text=Windows%20Azure&f%5B1%5D.Type=SubCategory&f%5B1%5D.Value=WindowsAzure_automation&f%5B1%5D.Text=Automation&f%5B2%5D.Type=SearchText&f%5B2%5D.Value=update%20management&f%5B3%5D.Type=Tag&f%5B3%5D.Value=Patching&f%5B3%5D.Text=Patching&f%5B4%5D.Type=ProgrammingLanguage&f%5B4%5D.Value=PowerShell&f%5B4%5D.Text=PowerShell) または [PowerShell ギャラリー](https://www.powershellgallery.com/packages?q=Tags%3A%22UpdateManagement%22+Tags%3A%22Automation%22)にあります。または、Azure portal 経由でインポートできます。 Automation アカウントで、 **[プロセス オートメーション]** の下にある **[Runbook ギャラリー]** を選択することで、行うことができます。 フィルターに **[更新管理]** を使用します。

![ギャラリーの一覧](./media/pre-post-scripts/runbook-gallery.png)

または、次の一覧に示すように、スクリプト名で検索できます。

* 更新管理 - VM 有効
* 更新管理 - VM 無効
* 更新管理 - スクリプトをローカルで実行する
* 更新管理 - 事前および事後スクリプトのテンプレート
* 更新管理 - スクリプトを実行コマンドで実行する

> [!IMPORTANT]
> Runbook をインポートした後、使用できるようにするには、発行する必要があります。 それを行うには、Automation アカウントで Runbook を見つけて、 **[編集]** を選択してから **[発行]** を選択します。

サンプルはすべて、次の例で定義されている基本のテンプレートに基づいています。 このテンプレートを使用すると、事前および事後スクリプトで使用する独自の Runbook を作成できます。 Azure に対して認証したり、`SoftwareUpdateConfigurationRunContext` パラメーターを処理したりするために必要なロジックが含まれています。

```powershell
<#
.SYNOPSIS
 Barebones script for Update Management Pre/Post

.DESCRIPTION
  This script is intended to be run as a part of Update Management pre/post-scripts.
  It requires a RunAs account.

.PARAMETER SoftwareUpdateConfigurationRunContext
  This is a system variable which is automatically passed in by Update Management during a deployment.
#>

param(
    [string]$SoftwareUpdateConfigurationRunContext
)
#region BoilerplateAuthentication
#This requires a RunAs account
$ServicePrincipalConnection = Get-AutomationConnection -Name 'AzureRunAsConnection'

Add-AzureRmAccount `
    -ServicePrincipal `
    -TenantId $ServicePrincipalConnection.TenantId `
    -ApplicationId $ServicePrincipalConnection.ApplicationId `
    -CertificateThumbprint $ServicePrincipalConnection.CertificateThumbprint

$AzureContext = Select-AzureRmSubscription -SubscriptionId $ServicePrincipalConnection.SubscriptionID
#endregion BoilerplateAuthentication

#If you wish to use the run context, it must be converted from JSON
$context = ConvertFrom-Json  $SoftwareUpdateConfigurationRunContext
#Access the properties of the SoftwareUpdateConfigurationRunContext
$vmIds = $context.SoftwareUpdateConfigurationSettings.AzureVirtualMachines | Sort-Object -Unique
$runId = $context.SoftwareUpdateConfigurationRunId

Write-Output $context

#Example: How to create and write to a variable using the pre-script:
<#
#Create variable named after this run so it can be retrieved
New-AzureRmAutomationVariable -ResourceGroupName $ResourceGroup –AutomationAccountName $AutomationAccount –Name $runId -Value "" –Encrypted $false
#Set value of variable
Set-AutomationVariable –Name $runId -Value $vmIds
#>

#Example: How to retrieve information from a variable set during the pre-script
<#
$variable = Get-AutomationVariable -Name $runId
#>
```

## <a name="interacting-with-machines"></a>マシンの操作

事前および事後タスクは、デプロイ内のマシン上で直接実行されるのではなく、ご利用の Automation アカウント内で Runbook として実行されます。 また、事前および事後タスクは Azure コンテキストで実行されるため、Azure 以外のマシンにはアクセスできません。 以降のセクションでは、Azure VM であるか Azure 以外のマシンであるかにかかわらず、それらのマシンと直接対話できる方法について説明します。

### <a name="interacting-with-azure-machines"></a>Azure マシンとの対話

事前タスクと事後タスクは、Runbook として実行されます。デプロイ内の Azure VM でネイティブに実行されるわけではありません。 Azure VM と対話するには、次のものが必要です。

* 実行アカウント
* 実行する Runbook

Azure マシンと対話するには、[Invoke-AzureRmVMRunCommand](/powershell/module/azurerm.compute/invoke-azurermvmruncommand) コマンドレットを使用して、目的の Azure VM と対話する必要があります。 この方法を示した例については、「[Update Management - Run Script with Run Command (更新管理 - スクリプトを実行コマンドで実行する)](https://gallery.technet.microsoft.com/Update-Management-Run-40f470dc)」にある Runbook の例を参照してください。

### <a name="interacting-with-non-azure-machines"></a>Azure 以外のマシンとの対話

事前および事後タスクは Azure コンテキストで実行されるため、Azure 以外のマシンにはアクセスできません。 Azure 以外のマシンと対話するには、次のものが必要です。

* 実行アカウント
* コンピューターにインストールされた Hybrid Runbook Worker
* ローカルで実行する Runbook
* 親 Runbook

Azure 以外のマシンと対話するためには、親 Runbook が Azure コンテキストで実行されます。 この Runbook は、[Start-AzureRmAutomationRunbook](/powershell/module/azurerm.automation/start-azurermautomationrunbook) コマンドレットを使用して子 Runbook を呼び出します。 `-RunOn` パラメーターを指定し、スクリプトを実行する Hybrid Runbook Worker の名前を指定する必要があります。 詳細については、「[Update Management - Run Script Locally (Update Management - スクリプトをローカルで実行する)](https://gallery.technet.microsoft.com/Update-Management-Run-6949cc44)」にある Runbook の例を参照してください。

## <a name="abort-patch-deployment"></a>修正プログラムのデプロイを中止する

事前スクリプトからエラーが返されたときに、展開を中止したい場合があります。 それを行うには、スクリプト内で障害を起こしているロジックのエラーを[スロー](/powershell/module/microsoft.powershell.core/about/about_throw)する必要があります。

```powershell
if (<My custom error logic>)
{
    #Throw an error to fail the patch deployment.
    throw "There was an error, abort deployment"
}
```

## <a name="known-issues"></a>既知の問題

* 事前および事後スクリプトを使用している場合は、パラメーターにブール値、オブジェクト、または配列を渡すことができません。 行った場合、Runbook は失敗します。 サポートされている型の完全な一覧については、「[パラメーターを渡す](#passing-parameters)」を参照してください。

## <a name="next-steps"></a>次の手順

使用している Windows 仮想マシンの更新プログラムの管理方法を学習するには、以下のチュートリアルに進みます。

> [!div class="nextstepaction"]
> [Azure Windows VM の更新プログラムとパッチの管理](automation-tutorial-update-management.md)

