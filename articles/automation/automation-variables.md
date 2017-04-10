---
title: "Azure Automation の変数資産 | Microsoft Docs"
description: "変数アセットとは、Azure Automation のすべての Runbook と DSC 構成に使用できる値です。  この記事では、変数の詳細およびテキスト作成とグラフィカル作成の両方で変数を使用する方法について説明します。"
services: automation
documentationcenter: 
author: mgoedtel
manager: carmonm
editor: tysonn
ms.assetid: b880c15f-46f5-4881-8e98-e034cc5a66ec
ms.service: automation
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/10/2017
ms.author: magoedte;bwren
translationtype: Human Translation
ms.sourcegitcommit: 24d86e17a063164c31c312685c0742ec4a5c2f1b
ms.openlocfilehash: 4c0c4f8c0d6c7cdc98406559f1cd36c87d33bf47
ms.lasthandoff: 03/11/2017


---
# <a name="variable-assets-in-azure-automation"></a>Azure Automation での変数アセット

変数アセットとは、オートメーション アカウント内のすべての Runbook と DSC 構成に使用できる値です。 これらは、Azure ポータル、Windows PowerShell、Runbook または DSC 構成内から作成、変更、取得することができます。 Automation 変数は、次のシナリオで役立ちます。

- 複数の Runbook または DSC 構成で値を共有する。

- 同じ Runbook または DSC 構成の複数のジョブで値を共有する。

- ポータルから値を管理する。または、Runbook や DSC 構成 (たとえば、特定の VM 名リスト、特定のリソース グループ、AD ドメイン名などの一般的な構成項目) で使用される、Windows PowerShell コマンドラインから値を管理する。  

Automation 変数は、Runbook または DSC 構成でエラーが発生した場合でも継続して使用できるように保存されます。  また、1 つの Runbook または DSC 構成で設定された値を他のユーザーが使用できるようにしたり、次回実行時に同じ Runbook で使用したりすることができます。     

変数が作成されると、暗号化して保存するように指定できます。  変数が暗号化されると、Azure Automation に安全に保存され、その値は Azure PowerShell モジュールに含まれている [Get-AzureRmAutomationVariable](https://msdn.microsoft.com/library/mt603849.aspx) コマンドレットからは取得できません。  暗号化された値は、Runbook または DSC 構成の **Get-AutomationVariable** アクティビティからのみ取得できます。

> [!NOTE]
> Azure Automation でセキュリティ保護される資産としては、資格情報、証明書、接続、暗号化された変数などがあります。 これらの資産は、各 Automation アカウント用に生成された一意のキーを使用して暗号化され、Azure Automation に保存されます。 このキーはマスター証明書によって暗号化され、Azure Automation に保存されます。 セキュリティで保護された資産を格納する前に、オートメーション アカウントのキーがマスター証明書を使用して復号化され、資産の暗号化に使用されます。

## <a name="variable-types"></a>変数の型

Azure Portal で変数を作成する場合、変数値を入力するための適切な制御をポータルに表示できるように、ドロップダウン リストからデータ型を指定する必要があります。 変数は、このデータ型に制限されませんが、別の型の値を指定する場合は、Windows PowerShell を使用して変数を設定する必要があります。 **Not defined** を指定した場合、変数の値は **$null** に設定され、[Set-AzureAutomationVariable](http://msdn.microsoft.com/library/dn913767.aspx) コマンドレットまたは **Set-AutomationVariable** アクティビティを使用して値を設定する必要があります。  ポータルでは複雑な変数の型の値を作成したり、変更したりすることはできませんが、Windows PowerShell を使用すれば、任意の型の値を指定することが可能です。 複合型は [PSCustomObject](http://msdn.microsoft.com/library/system.management.automation.pscustomobject.aspx) として返されます。

配列またはハッシュ テーブルを作成し、それを変数に保存することによって、複数の値を&1; つの変数に格納することができます。

Automation で使用できる変数の型の一覧を次に示します。

* String
* Integer
* DateTime
* ブール
* Null

>[!NOTE]
>変数アセットは 1024 文字に制限されています。 

## <a name="cmdlets-and-workflow-activities"></a>コマンドレットとワークフローのアクティビティ

Windows PowerShell で Automation 変数を作成および管理するには、次のテーブルのコマンドレットを使用します。 これらのコマンドレットは、Automation Runbook と DSC 構成に使用できる [Azure PowerShell モジュール](/powershell/azureps-cmdlets-docs)に付属しています。

|コマンドレット|説明|
|:---|:---|
|[Get-AzureRmAutomationVariable](https://msdn.microsoft.com/library/mt603849.aspx)|既存の変数の値を取得します。|
|[New-AzureRmAutomationVariable](https://msdn.microsoft.com/library/mt603613.aspx)|新しい変数を作成し、その値を設定します。|
|[Remove-AzureRmAutomationVariable](https://msdn.microsoft.com/library/mt619354.aspx)|既存の変数を削除します。|
|[Set-AzureRmAutomationVariable](https://msdn.microsoft.com/library/mt603601.aspx)|既存の変数の値を設定します。|

次のテーブルのワークフロー アクティビティは、Runbook で Automation 変数にアクセスするために使用されます。 これらは、Runbook または DSC 構成でのみ使用できるものであり、Azure PowerShell モジュールには付属していません。

|ワークフロー アクティビティ|Description|
|:---|:---|
|Get-AutomationVariable|既存の変数の値を取得します。|
|Set-AutomationVariable|既存の変数の値を設定します。|

> [!NOTE] 
> Runbook または DSC 構成内で **Get-AutomationVariable** の –Name パラメーターに変数を使用すると、設計時に Runbook または DSC 構成と Automation 変数の間の依存関係の検出が複雑になる可能性があるため、使用しないようにする必要があります。

## <a name="creating-an-automation-variable"></a>Automation 変数の作成

### <a name="to-create-a-variable-with-the-azure-portal"></a>Azure Portal で変数を作成するには

1. Automation アカウントから、**[資産]** タイルをクリックして **[資産]** ブレードを開きます。
1. **[変数]** タイルをクリックして **[変数]** ブレードを開きます。
1. ブレード上部の **[変数の追加]** を選択します。
1. フォームに入力し、 **[作成]** をクリックして新しい変数を保存します。


### <a name="to-create-a-variable-with-windows-powershell"></a>Windows PowerShell で変数を作成するには

[New-AzureRmAutomationVariable](https://msdn.microsoft.com/library/mt603613.aspx) コマンドレットは、変数を作成し、その初期値を設定します。 [Get-AzureRmAutomationVariable](https://msdn.microsoft.com/library/mt603849.aspx)を使用して、値を取得することができます。 値が単純型である場合、その同じ型が返されます。 値が複合型である場合、 **PSCustomObject** が返されます。

次のサンプル コマンドは、文字列型の変数を作成してから、その値を返す方法を示しています。

    New-AzureRmAutomationVariable -ResourceGroupName "ResouceGroup01" 
    –AutomationAccountName "MyAutomationAccount" –Name 'MyStringVariable' `
    –Encrypted $false –Value 'My String'
    $string = (Get-AzureRmAutomationVariable -ResourceGroupName "ResouceGroup01" `
    –AutomationAccountName "MyAutomationAccount" –Name 'MyStringVariable').Value

次のサンプル コマンドは、複合型の変数を作成してから、そのプロパティを返す方法を示しています。 この場合、**Get-AzureRmVm** から仮想マシンのオブジェクトが使用されます。

    $vm = Get-AzureRmVm -ResourceGroupName "ResourceGroup01" –Name "VM01"
    New-AzureRmAutomationVariable –AutomationAccountName "MyAutomationAccount" –Name "MyComplexVariable" –Encrypted $false –Value $vm
    
    $vmValue = (Get-AzureRmAutomationVariable -ResourceGroupName "ResourceGroup01" `
    –AutomationAccountName "MyAutomationAccount" –Name "MyComplexVariable").Value
    $vmName = $vmValue.Name
    $vmIpAddress = $vmValue.IpAddress


## <a name="using-a-variable-in-a-runbook-or-dsc-configuration"></a>Runbook または DSC 構成で変数を使用する

**Set-AutomationVariable** アクティビティを使用して、Runbook または DSC 構成の Automation 変数の値を設定し、**Get-AutomationVariable** を使用して Automation 変数の値を取得します。  **Set-AzureAutomationVariable** または **Get-AzureAutomationVariable** コマンドレットはワークフローのアクティビティよりも低効率であるため、Runbook または DSC 構成では使用しないでください。  また、 **Get-AzureAutomationVariable**を使用して、セキュリティで保護された変数の値を取得することもできません。  Runbook または DSC 構成内から変数を作成する唯一の方法は、[New-AzureAutomationVariable](http://msdn.microsoft.com/library/dn913771.aspx) コマンドレットを使用することです。


### <a name="textual-runbook-samples"></a>テキスト形式の Runbook のサンプル

#### <a name="setting-and-retrieving-a-simple-value-from-a-variable"></a>変数からの単純値の設定と取得

次のサンプル コマンドは、テキスト形式の Runbook で変数を設定し取得する方法を示しています。 このサンプルでは、*NumberOfIterations* および *NumberOfRunnings* という整数型の変数と、*SampleMessage* という文字列型の変数が既に作成されていることを前提にしています。

    $NumberOfIterations = Get-AutomationVariable -Name 'NumberOfIterations'
    $NumberOfRunnings = Get-AutomationVariable -Name 'NumberOfRunnings'
    $SampleMessage = Get-AutomationVariable -Name 'SampleMessage'
    
    Write-Output "Runbook has been run $NumberOfRunnings times."
    
    for ($i = 1; $i -le $NumberOfIterations; $i++) {
       Write-Output "$i`: $SampleMessage"
    }
    Set-AutomationVariable –Name NumberOfRunnings –Value ($NumberOfRunnings += 1)

#### <a name="setting-and-retrieving-a-complex-object-in-a-variable"></a>変数での複雑なオブジェクトの設定および取得

次のサンプル コードは、テキスト形式の Runbook で複合値で変数を更新する方法を示しています。 このサンプルでは、Azure 仮想マシンは **Get-AzureVM** で取得され、既存の Automation 変数に保存されます。  [変数の型](#variable-types)で説明したように、これは PSCustomObject として格納されます。

    $vm = Get-AzureVM -ServiceName "MyVM" -Name "MyVM"
    Set-AutomationVariable -Name "MyComplexVariable" -Value $vm


次のコードでは、この値は変数から取得され、仮想マシンを起動するために使用されます。

    $vmObject = Get-AutomationVariable -Name "MyComplexVariable"
    if ($vmObject.PowerState -eq 'Stopped') {
       Start-AzureVM -ServiceName $vmObject.ServiceName -Name $vmObject.Name
    }


#### <a name="setting-and-retrieving-a-collection-in-a-variable"></a>変数でのコレクションの設定および取得

次のサンプル コードは、テキスト形式の Runbook で複合値のコレクションで変数を使用する方法を示しています。 このサンプルでは、複数の Azure 仮想マシンは **Get-AzureVM** で取得され、既存の Automation 変数に保存されます。  [変数の型](#variable-types)で説明したように、これは PSCustomObject のコレクションとして格納されます。

    $vms = Get-AzureVM | Where -FilterScript {$_.Name -match "my"}     
    Set-AutomationVariable -Name 'MyComplexVariable' -Value $vms

次のコードでは、このコレクションは変数から取得され、各仮想マシンを起動するために使用されます。

    $vmValues = Get-AutomationVariable -Name "MyComplexVariable"
    ForEach ($vmValue in $vmValues)
    {
       if ($vmValue.PowerState -eq 'Stopped') {
          Start-AzureVM -ServiceName $vmValue.ServiceName -Name $vmValue.Name
       }
    }

#### <a name="setting-and-retrieving-a-secure-string"></a>セキュリティで保護された文字列の設定と取得

セキュリティで保護された文字列または資格情報を渡す必要がある場合、まずこの資産を資格情報またはセキュリティで保護された変数として作成する必要があります。 

    $securecredential = get-credential

    New-AzureRmAutomationCredential -ResourceGroupName contoso `
    -AutomationAccountName contosoaccount -Name ContosoCredentialAsset -Value $securecredential

次に、以下のサンプル コードに示すように、この資産の名前をパラメーターとして Runbook に渡し、組み込みのアクティビティを使用して、スクリプトで取得および使用することができます。  

    ExampleScript
    Param

      (
         $ContosoCredentialAssetName
      )

    $ContosoCred = Get-AutomationPSCredential -Name $ContosoCredentialAssetName

次の例は、Runbook を呼び出す方法を示しています。  

    $RunbookParams = @{"ContosoCredentialAssetName"="ContosoCredentialAsset"}

    Start-AzureRMAutomationRunbook -ResourceGroupName contoso `
    -AutomationAccountName contosoaccount -Name ExampleScript -Parameters $RunbookParams

### <a name="graphical-runbook-samples"></a>グラフィカルな Runbook のサンプル

グラフィカルな Runbook で、グラフィカル エディターの [ライブラリ] ウィンドウの変数を右クリックして目的のアクティビティを選択することにより、**Get-AutomationVariable** または **Set-AutomationVariable** を追加します。

![キャンバスへの変数の追加](media/automation-variables/runbook-variable-add-canvas.png)

#### <a name="setting-values-in-a-variable"></a>変数での値の設定
次の図は、グラフィカルな Runbook の単純値で変数を更新するサンプル アクティビティを示しています。 このサンプルでは、1 つの Azure 仮想マシンは **Get-AzureRmVM** で取得され、コンピューター名は既存の Automation 変数に文字列型で保存されます。  出力では&1; つのオブジェクトのみが期待されているため、 [リンクがパイプラインかシーケンス](automation-graphical-authoring-intro.md#links-and-workflow) かどうかは関係ありません。

![単純変数の設定](media/automation-variables/runbook-set-simple-variable.png)

## <a name="next-steps"></a>次のステップ

* グラフィカル作成でアクティビティを接続する方法については、 [グラフィカル作成でのリンク](automation-graphical-authoring-intro.md#links-and-workflow)
* グラフィカルな Runbook の使用を開始するには、「 [初めてのグラフィカルな Runbook](automation-first-runbook-graphical.md) 


