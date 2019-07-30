---
title: Azure Automation での変数アセット
description: 変数アセットとは、Azure Automation のすべての Runbook と DSC 構成に使用できる値です。  この記事では、変数の詳細およびテキスト作成とグラフィカル作成の両方で変数を使用する方法について説明します。
services: automation
ms.service: automation
ms.subservice: shared-capabilities
author: bobbytreed
ms.author: robreed
ms.date: 05/14/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 39282e816be875e598d7e0599eeb358a79941be7
ms.sourcegitcommit: f811238c0d732deb1f0892fe7a20a26c993bc4fc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/29/2019
ms.locfileid: "67478061"
---
# <a name="variable-assets-in-azure-automation"></a>Azure Automation での変数アセット

変数アセットとは、オートメーション アカウント内のすべての Runbook と DSC 構成に使用できる値です。 それらは、Azure portal、PowerShell、Runbook 内、または DSC 構成から管理できます。 Automation 変数は、次のシナリオで役立ちます。

- 複数の Runbook または DSC 構成で値を共有する。

- 同じ Runbook または DSC 構成の複数のジョブで値を共有する。

- Runbook や DSC 構成で使用される値 (たとえば、特定の VM 名リスト、特定のリソース グループ、AD ドメイン名などの一般的な構成項目) を、ポータルまたは PowerShell コマンド ラインから管理する。  

Automation 変数は保存されるので、Runbook または DSC 構成でエラーが発生した場合でも使用できます。 この動作により、ある Runbook で設定された値を、別の Runbook で使用したり、同じ Runbook や DSC 構成を次に実行するときに使用したりすることができます。

変数が作成されると、暗号化して保存するように指定できます。 暗号化された変数は、Azure Automation に安全に保存され、その値は Azure PowerShell モジュールに含まれている [Get-AzureRmAutomationVariable](/powershell/module/AzureRM.Automation/Get-AzureRmAutomationVariable) コマンドレットからは取得できません。 暗号化された値は、Runbook または DSC 構成の **Get-AutomationVariable** アクティビティからのみ取得できます。 暗号化された変数を暗号化されていない変数に変更するには、その変数を削除して暗号化されていない変数として再作成する必要があります。

>[!NOTE]
>Azure Automation でセキュリティ保護される資産としては、資格情報、証明書、接続、暗号化された変数などがあります。 これらの資産は、各 Automation アカウント用に生成された一意のキーを使って暗号化され、Azure Automation に保存されます。 このキーは、システムで管理されたキー コンテナーに格納されます。 セキュリティで保護された資産を保存する前に、キーが Key Vault から読み込まれ、資産の暗号化に使われます。 このプロセスは、Azure Automation によって管理されます。

## <a name="variable-types"></a>変数の型

Azure Portal で変数を作成する場合、変数値を入力するための適切な制御をポータルに表示できるように、ドロップダウン リストからデータ型を指定する必要があります。 変数は、このデータ型に制限されません。 別の型の値を指定する場合は、Windows PowerShell を使用して変数を設定する必要があります。 **[未定義]** を指定した場合、変数の値は **$null** に設定され、[Set-AzureRMAutomationVariable](/powershell/module/AzureRM.Automation/Set-AzureRmAutomationVariable) コマンドレットまたは **Set-AutomationVariable** アクティビティを使用して値を設定する必要があります。 ポータルでは複雑な変数の型の値を作成したり、変更したりすることはできませんが、Windows PowerShell を使用すれば、任意の型の値を指定することが可能です。 複合型は [PSCustomObject](/dotnet/api/system.management.automation.pscustomobject) として返されます。

配列またはハッシュ テーブルを作成し、それを変数に保存することによって、複数の値を 1 つの変数に格納することができます。

Automation で使用できる変数の型の一覧を次に示します。

* string
* 整数
* DateTime
* ブール
* Null

## <a name="azurerm-powershell-cmdlets"></a>AzureRM PowerShell コマンドレット

AzureRM の場合、Windows PowerShell で Automation 資格情報資産を作成および管理するには、次の表のコマンドレットを使用します。 これらのコマンドレットは、Automation Runbook と DSC 構成に使用できる [AzureRM.Automation モジュール](/powershell/azure/overview)に付属しています。

| コマンドレット | 説明 |
|:---|:---|
|[Get-AzureRmAutomationVariable](/powershell/module/AzureRM.Automation/Get-AzureRmAutomationVariable)|既存の変数の値を取得します。|
|[New-AzureRmAutomationVariable](/powershell/module/AzureRM.Automation/New-AzureRmAutomationVariable)|新しい変数を作成し、その値を設定します。|
|[Remove-AzureRmAutomationVariable](/powershell/module/AzureRM.Automation/Remove-AzureRmAutomationVariable)|既存の変数を削除します。|
|[Set-AzureRmAutomationVariable](/powershell/module/AzureRM.Automation/Set-AzureRmAutomationVariable)|既存の変数の値を設定します。|

## <a name="activities"></a>Activities

次の表のアクティビティは、Runbook および DSC 構成で資格情報にアクセスするために使用されます。

| Activities | 説明 |
|:---|:---|
|Get-AutomationVariable|既存の変数の値を取得します。|
|Set-AutomationVariable|既存の変数の値を設定します。|

> [!NOTE]
> Runbook または DSC 構成内で **Get-AutomationVariable** の –Name パラメーターに変数を使用すると、設計時に Runbook または DSC 構成と Automation 変数の間の依存関係の検出が複雑になる可能性があるため、使用しないようにする必要があります。

次の表の関数を使用して、Python2 Runbook の変数にアクセスしてそれを取得します。

|Python2 関数|説明|
|:---|:---|
|automationassets.get_automation_variable|既存の変数の値を取得します。 |
|automationassets.set_automation_variable|既存の変数の値を設定します。 |

> [!NOTE]
> 資産関数にアクセスするには、お使いの Python Runbook の上部にある "automationassets" モジュールをインポートする必要があります。

## <a name="creating-a-new-automation-variable"></a>新しい Automation 変数の作成

### <a name="to-create-a-new-variable-with-the-azure-portal"></a>Azure ポータルで新しい変数を作成するには

1. Automation アカウントから、 **[資産]** タイルをクリックして **[資産]** ブレードで、 **[変数]** を選びます。
2. **[変数]** タイルで、 **[変数の追加]** を選びます。
3. **[新しい変数]** ブレードでオプションを設定し、 **[作成]** をクリックして新しい変数を保存します。

### <a name="to-create-a-new-variable-with-windows-powershell"></a>Windows PowerShell で新しい変数を作成するには

[New-AzureRmAutomationVariable](/powershell/module/AzureRM.Automation/New-AzureRmAutomationVariable) コマンドレットは、新しい変数を作成し、その初期値を設定します。 [Get-AzureRmAutomationVariable](/powershell/module/AzureRM.Automation/Get-AzureRmAutomationVariable)を使用して、値を取得することができます。 値が単純型である場合、その同じ型が返されます。 値が複合型である場合、 **PSCustomObject** が返されます。

次のサンプル コマンドは、文字列型の変数を作成してから、その値を返す方法を示しています。

```powershell
New-AzureRmAutomationVariable -ResourceGroupName "ResourceGroup01" 
–AutomationAccountName "MyAutomationAccount" –Name 'MyStringVariable' `
–Encrypted $false –Value 'My String'
$string = (Get-AzureRmAutomationVariable -ResourceGroupName "ResourceGroup01" `
–AutomationAccountName "MyAutomationAccount" –Name 'MyStringVariable').Value
```

次のサンプル コマンドは、複合型の変数を作成してから、そのプロパティを返す方法を示しています。 この場合、**Get-AzureRmVm** から仮想マシンのオブジェクトが使用されます。

```powershell
$vm = Get-AzureRmVm -ResourceGroupName "ResourceGroup01" –Name "VM01"
New-AzureRmAutomationVariable –AutomationAccountName "MyAutomationAccount" –Name "MyComplexVariable" –Encrypted $false –Value $vm

$vmValue = (Get-AzureRmAutomationVariable -ResourceGroupName "ResourceGroup01" `
–AutomationAccountName "MyAutomationAccount" –Name "MyComplexVariable").Value
$vmName = $vmValue.Name
$vmIpAddress = $vmValue.IpAddress
```

## <a name="using-a-variable-in-a-runbook-or-dsc-configuration"></a>Runbook または DSC 構成で変数を使用する

**Set-AutomationVariable** アクティビティを使用して、PowerShell Runbook または DSC 構成の Automation 変数の値を設定し、**Get-AutomationVariable** を使用して Automation 変数の値を取得します。 **Set-AzureRMAutomationVariable** または **Get-AzureRMAutomationVariable** コマンドレットはワークフローのアクティビティよりも低効率であるため、Runbook または DSC 構成では使用しないでください。 また、**Get-AzureRMAutomationVariable** を使用して、セキュリティで保護された変数の値を取得することもできません。 Runbook または DSC 構成内から新しい変数を作成する唯一の方法は、[New-AzureRMAutomationVariable](/powershell/module/AzureRM.Automation/New-AzureRmAutomationVariable) コマンドレットを使用することです。

### <a name="textual-runbook-samples"></a>テキスト形式の Runbook のサンプル

#### <a name="setting-and-retrieving-a-simple-value-from-a-variable"></a>変数からの単純値の設定と取得

次のサンプル コマンドは、テキスト形式の Runbook で変数を設定し取得する方法を示しています。 このサンプルでは、*NumberOfIterations* および *NumberOfRunnings* という整数型の変数と、*SampleMessage* という文字列型の変数が作成されていることを前提にしています。

```powershell
$NumberOfIterations = Get-AzureRmAutomationVariable -ResourceGroupName "ResourceGroup01" –AutomationAccountName "MyAutomationAccount" -Name 'NumberOfIterations'
$NumberOfRunnings = Get-AzureRmAutomationVariable -ResourceGroupName "ResourceGroup01" –AutomationAccountName "MyAutomationAccount" -Name 'NumberOfRunnings'
$SampleMessage = Get-AutomationVariable -Name 'SampleMessage'

Write-Output "Runbook has been run $NumberOfRunnings times."

for ($i = 1; $i -le $NumberOfIterations; $i++) {
    Write-Output "$i`: $SampleMessage"
}
Set-AzureRmAutomationVariable -ResourceGroupName "ResourceGroup01" –AutomationAccountName "MyAutomationAccount" –Name NumberOfRunnings –Value ($NumberOfRunnings += 1)
```

#### <a name="setting-and-retrieving-a-variable-in-python2"></a>Python2 の変数の設定および取得

次のサンプル コードは、変数の使用方法、変数の設定方法、および Python2 Runbook に存在しない変数の例外処理方法を示します。

```python
import automationassets
from automationassets import AutomationAssetNotFound

# get a variable
value = automationassets.get_automation_variable("test-variable")
print value

# set a variable (value can be int/bool/string)
automationassets.set_automation_variable("test-variable", True)
automationassets.set_automation_variable("test-variable", 4)
automationassets.set_automation_variable("test-variable", "test-string")

# handle a non-existent variable exception
try:
    value = automationassets.get_automation_variable("non-existing variable")
except AutomationAssetNotFound:
    print "variable not found"
```

### <a name="graphical-runbook-samples"></a>グラフィカルな Runbook のサンプル

グラフィカルな Runbook で、グラフィカル エディターの [ライブラリ] ウィンドウの変数を右クリックして目的のアクティビティを選択することにより、**Get-AutomationVariable** または **Set-AutomationVariable** を追加します。

![キャンバスへの変数の追加](../media/variables/runbook-variable-add-canvas.png)

#### <a name="setting-values-in-a-variable"></a>変数での値の設定

次の図は、グラフィカルな Runbook の単純値で変数を更新するサンプル アクティビティを示しています。 このサンプルでは、**Get-AzureRmVM** によって 1 つの Azure 仮想マシンが取得され、コンピューター名が既存の Automation 変数に文字列型で保存されます。 出力では 1 つのオブジェクトのみが期待されているため、[リンクがパイプラインかシーケンス](../automation-graphical-authoring-intro.md#links-and-workflow)かどうかは関係ありません。

![単純変数の設定](../media/variables/runbook-set-simple-variable.png)

## <a name="next-steps"></a>次の手順

- グラフィカル作成でアクティビティを接続する方法については、 [グラフィカル作成でのリンク](../automation-graphical-authoring-intro.md#links-and-workflow)
- グラフィカルな Runbook の使用を開始するには、「 [初めてのグラフィカルな Runbook](../automation-first-runbook-graphical.md)
