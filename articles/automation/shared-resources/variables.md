---
title: Azure Automation で変数を管理する
description: この記事では、Runbook および DSC 構成内の変数を操作する方法について説明します。
services: automation
ms.service: automation
ms.subservice: shared-capabilities
author: mgoedtel
ms.author: magoedte
ms.date: 05/14/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: ee49ae905622b4b76d782f6a31e0c2333b6d54be
ms.sourcegitcommit: 269da970ef8d6fab1e0a5c1a781e4e550ffd2c55
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/10/2020
ms.locfileid: "88055294"
---
# <a name="manage-variables-in-azure-automation"></a>Azure Automation で変数を管理する

変数アセットとは、Automation アカウント内のすべての Runbook と DSC 構成に使用できる値です。 それらの管理は、Azure portal または PowerShell を使用して行うか、Runbook または DSC 構成内で行うことができます。

Automation 変数は、次のシナリオで役立ちます。

- 複数の Runbook または DSC 構成で値を共有する。

- 同じ Runbook または DSC 構成の複数のジョブで値を共有する。

- Runbook または DSC 構成によって使用される値を、ポータルまたは PowerShell コマンド ラインから管理する。 例としては、共通する構成アイテムのセット (VM 名の詳細なリスト、特定のリソース グループ、AD ドメイン名など) があります。  

Azure Automation では、変数が保存されるので、Runbook または DSC 構成が失敗した場合でも使用できます。 この動作により、ある Runbook または DSC 構成で設定した値を、別の Runbook で使用したり、次の実行時に同じ Runbook または DSC 構成で使用したりすることができます。

Azure Automation では、暗号化された各変数を安全に保存します。 変数の作成時に、Azure Automation を使用して、セキュリティで保護された資産となるようにその変数の暗号化とストレージを指定できます。 変数を作成した後は、変数を再作成せずにその暗号化の状態を変更することはできません。 Azure Security Center の推奨事項は、「[Automation アカウント変数は、暗号化する必要がある](../../security-center/recommendations-reference.md#recs-computeapp)」で説明されているように、すべての Azure Automation 変数を暗号化することです。 

>[!NOTE]
>Azure Automation でセキュリティ保護される資産としては、資格情報、証明書、接続、暗号化された変数などがあります。 これらの資産は、各 Automation アカウント用に生成された一意のキーを使って暗号化され、Azure Automation に保存されます。 Azure Automation では、キーはシステムによって管理される Key Vault に格納されます。 セキュリティで保護された資産を保存する前に、Automation によって Key Vault からキーが読み込まれ、それを使用して資産が暗号化されます。 

## <a name="variable-types"></a>変数の型

Azure portal を使用して変数を作成する場合、変数値を入力するための適切なコントロールをポータルに表示できるように、ドロップダウン ボックスの一覧からデータ型を指定する必要があります。 Azure Automation で使用できる変数の型を次に示します。

* String
* Integer
* DateTime
* Boolean
* [Null]

変数は、指定されているデータ型に限定されません。 別の型の値を指定する場合は、Windows PowerShell を使用して変数を設定する必要があります。 `Not defined` を指定した場合、変数の値は Null に設定されます。 [Set-AzAutomationVariable](/powershell/module/az.automation/set-azautomationvariable?view=azps-3.5.0) コマンドレットまたは内部 `Set-AutomationVariable` コマンドレットを使用して値を設定する必要があります。

Azure portal を使用して、複合型の変数の値を作成したり変更したりすることはできません。 ただし、Windows PowerShell を使用すると、任意の型の値を指定できます。 複合型は [PSCustomObject](/dotnet/api/system.management.automation.pscustomobject) として取得されます。

配列またはハッシュ テーブルを作成し、それを変数に保存することによって、複数の値を 1 つの変数に格納することができます。

>[!NOTE]
>VM 名の変数に使用できる文字数は最大 80 文字です。 リソース グループの変数には最大 90 文字まで使用できます。 「[Azure リソースの名前付け規則と制限事項](../../azure-resource-manager/management/resource-name-rules.md)」を参照してください。

## <a name="powershell-cmdlets-to-access-variables"></a>変数にアクセスするための PowerShell コマンドレット

PowerShell を使用して Automation 変数を作成および管理するためのコマンドレットを次の表に示します。 これらは、[Az モジュール](modules.md#az-modules)の一部として出荷されます。

| コマンドレット | 説明 |
|:---|:---|
|[Get-AzAutomationVariable](/powershell/module/az.automation/get-azautomationvariable?view=azps-3.5.0) | 既存の変数の値を取得します。 値が単純型である場合、その同じ型が取得されます。 それが複合型の場合は、`PSCustomObject` 型が取得されます。 <br>**注:** 暗号化された変数の値を取得するために、このコマンドレットを使用することはできません。 それを行う唯一の方法は、Runbook または DSC 構成で内部 `Get-AutomationVariable` コマンドレットを使用することです。 「[変数にアクセスするための内部コマンドレット](#internal-cmdlets-to-access-variables)」を参照してください。 |
|[New-AzAutomationVariable](/powershell/module/az.automation/new-azautomationvariable?view=azps-3.5.0) | 新しい変数を作成し、その値を設定します。|
|[Remove-AzAutomationVariable](/powershell/module/az.automation/remove-azautomationvariable?view=azps-3.5.0)| 既存の変数を削除します。|
|[Set-AzAutomationVariable](/powershell/module/az.automation/set-azautomationvariable?view=azps-3.5.0)| 既存の変数の値を設定します。 |

## <a name="internal-cmdlets-to-access-variables"></a>変数にアクセスするための内部コマンドレット

Runbook および DSC 構成内の変数にアクセスするための内部コマンドレットを次の表に示します。 これらのコマンドレットには、グローバル モジュール `Orchestrator.AssetManagement.Cmdlets` が付属しています。 詳細については、「[内部コマンドレット](modules.md#internal-cmdlets)」を参照してください。

| 内部コマンドレット | 説明 |
|:---|:---|
|`Get-AutomationVariable`|既存の変数の値を取得します。|
|`Set-AutomationVariable`|既存の変数の値を設定します。|

> [!NOTE]
> Runbook または DSC 構成で、`Get-AutomationVariable` の `Name` パラメーターに変数を使用することは避けてください。 これらの変数を使用すると、デザイン時に、Runbook と Automation 変数との間の依存関係の検出が複雑になる可能性があります。

`Get-AutomationVariable` は、PowerShell では機能せず、Runbook または DSC 構成でのみ機能します。 たとえば、暗号化された変数の値を表示するには、Runbook を作成して変数を取得し、それを出力ストリームに書き込むことができます。
 
```powershell
$mytestencryptvar = Get-AutomationVariable -Name TestVariable
Write-output "The encrypted value of the variable is: $mytestencryptvar"
```

## <a name="python-2-functions-to-access-variables"></a>変数にアクセスするための Python 2 関数

Python 2 Runbook の変数にアクセスするには、次の表の関数を使用します。

|Python 2 関数|説明|
|:---|:---|
|`automationassets.get_automation_variable`|既存の変数の値を取得します。 |
|`automationassets.set_automation_variable`|既存の変数の値を設定します。 |

> [!NOTE]
> 資産関数にアクセスするには、お使いの Python Runbook の上部にある `automationassets` モジュールをインポートする必要があります。

## <a name="create-and-get-a-variable"></a>変数を作成して取得する

>[!NOTE]
>変数の暗号化を削除する場合は、変数を削除し、暗号化せずに再作成する必要があります。

### <a name="create-and-get-a-variable-using-the-azure-portal"></a>Azure portal を使用して変数を作成して取得する

1. Automation アカウントから、 **[資産]** タイルをクリックして **[資産]** ブレードで、 **[変数]** を選択します。
2. **[変数]** タイルで、 **[変数の追加]** を選びます。
3. **[新しい変数]** ブレードでオプションを設定してから、 **[作成]** をクリックして新しい変数を保存します。

> [!NOTE]
> 暗号化された変数を保存したら、ポータルでそれを表示することはできません。 更新のみ可能です。

### <a name="create-and-get-a-variable-in-windows-powershell"></a>Windows PowerShell で変数を作成して取得する

Runbook または DSC 構成では、`New-AzAutomationVariable` コマンドレットを使用して新しい変数を作成し、その初期値を設定します。 変数が暗号化されている場合、呼び出しで `Encrypted` パラメーターを使用する必要があります。 スクリプトでは、`Get-AzAutomationVariable` を使用して変数の値を取得できます。 

>[!NOTE]
>PowerShell スクリプトでは、暗号化された値を取得できません。 これを行う唯一の方法は、内部 `Get-AutomationVariable` コマンドレットを使用することです。

次の例は、文字列型の変数を作成してから、その値を返す方法を示しています。

```powershell
New-AzAutomationVariable -ResourceGroupName "ResourceGroup01" 
–AutomationAccountName "MyAutomationAccount" –Name 'MyStringVariable' `
–Encrypted $false –Value 'My String'
$string = (Get-AzAutomationVariable -ResourceGroupName "ResourceGroup01" `
–AutomationAccountName "MyAutomationAccount" –Name 'MyStringVariable').Value
```

次の例は、複合型の変数を作成してから、そのプロパティを取得する方法を示しています。 ここでは、[Get-AzVM](/powershell/module/Az.Compute/Get-AzVM?view=azps-3.5.0) の仮想マシン オブジェクトが使用されます。

```powershell
$vm = Get-AzVM -ResourceGroupName "ResourceGroup01" –Name "VM01"
New-AzAutomationVariable –AutomationAccountName "MyAutomationAccount" –Name "MyComplexVariable" –Encrypted $false –Value $vm

$vmValue = (Get-AzAutomationVariable -ResourceGroupName "ResourceGroup01" `
–AutomationAccountName "MyAutomationAccount" –Name "MyComplexVariable").Value
$vmName = $vmValue.Name
$vmIpAddress = $vmValue.IpAddress
```

## <a name="textual-runbook-examples"></a>テキスト形式の Runbook の例

### <a name="retrieve-and-set-a-simple-value-from-a-variable"></a>変数の単純値を取得して設定する

次の例は、テキスト形式の Runbook で変数を設定し取得する方法を示しています。 この例では、`NumberOfIterations` と `NumberOfRunnings` という名前の整数型の変数と、`SampleMessage` という名前の文字列型の変数を作成することを想定しています。

```powershell
$NumberOfIterations = Get-AzAutomationVariable -ResourceGroupName "ResourceGroup01" –AutomationAccountName "MyAutomationAccount" -Name 'NumberOfIterations'
$NumberOfRunnings = Get-AzAutomationVariable -ResourceGroupName "ResourceGroup01" –AutomationAccountName "MyAutomationAccount" -Name 'NumberOfRunnings'
$SampleMessage = Get-AutomationVariable -Name 'SampleMessage'

Write-Output "Runbook has been run $NumberOfRunnings times."

for ($i = 1; $i -le $NumberOfIterations; $i++) {
    Write-Output "$i`: $SampleMessage"
}
Set-AzAutomationVariable -ResourceGroupName "ResourceGroup01" –AutomationAccountName "MyAutomationAccount" –Name NumberOfRunnings –Value ($NumberOfRunnings += 1)
```

### <a name="retrieve-and-set-a-variable-in-a-python-2-runbook"></a>Python 2 Runbook で変数を取得して設定する

次のサンプルは、Python 2 Runbook の変数の取得方法、変数の設定方法、および存在しない変数の例外処理方法を示しています。

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
    value = automationassets.get_automation_variable("nonexisting variable")
except AutomationAssetNotFound:
    print "variable not found"
```

## <a name="graphical-runbook-examples"></a>グラフィカルな Runbook の例

グラフィカルな Runbook では、内部コマンドレット `Get-AutomationVariable` または `Set-AutomationVariable` のアクティビティを追加できます。 グラフィカル エディターの [ライブラリ] ペインで各変数を右クリックし、目的のアクティビティを選択するだけです。

![キャンバスへの変数の追加](../media/variables/runbook-variable-add-canvas.png)

次の図は、グラフィカルな Runbook で変数を単純な値で更新するサンプル アクティビティを示しています。 この例では、`Get-AzVM` のアクティビティによって 1 つの Azure 仮想マシンが取得され、そのコンピューター名が既存の Automation の文字列型の変数に保存されます。 このコードでは出力に 1 つのオブジェクトのみが含まれると予期されるため、[リンクがパイプラインとシーケンス](../automation-graphical-authoring-intro.md#use-links-for-workflow)のいずれであっても関係ありません。

![単純変数の設定](../media/variables/runbook-set-simple-variable.png)

## <a name="next-steps"></a>次のステップ

* 変数にアクセスするためのコマンドレットの詳細については、「[Azure Automation でモジュールを管理する](modules.md)」を参照してください。
* Runbook の一般的な情報については、「[Azure Automation での Runbook の実行](../automation-runbook-execution.md)」を参照してください。
* DSC 構成の詳細については、「[Azure Automation State Configuration の概要](../automation-dsc-overview.md)」を参照してください。
