---
title: Azure DevTest Labs で成果物を VM に追加する | Microsoft Docs
description: Azure DevTest Labs のラボで成果物を仮想マシンを追加する方法を説明します
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: b4772755d8077f7a659c4d403961ffaeb9e1d483
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "85483892"
---
# <a name="add-an-artifact-to-a-vm"></a>成果物を VM に追加する
VM を作成するとき、既存の成果物をその VM に追加できます。 これらの成果物は、[パブリックの DevTest Labs の Git リポジトリ](https://github.com/Azure/azure-devtestlab/tree/master/Artifacts)または独自の Git リポジトリにあります。 この記事では、Azure portal および Azure PowerShell を使用して成果物を追加する方法について説明します。 

Azure DevTest Labs "*アーティファクト*" を使用すると、VM のプロビジョニング時に実行される "*アクション*" (Windows PowerShell スクリプトの実行、Bash コマンドの実行、ソフトウェアのインストールなど) を指定できます。 アーティファクト *パラメーター* を使用すると、特定のシナリオのアーティファクトをカスタマイズできます。

カスタム アーティファクトの作成方法に関する詳細については、[カスタム アーティファクトの作成](devtest-lab-artifact-author.md)に関する記事を参照してください。

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="use-azure-portal"></a>Azure Portal の使用 
1. [Azure portal](https://go.microsoft.com/fwlink/p/?LinkID=525040) にサインインします。
1. **[すべてのサービス]** を選択し、一覧の **[DevTest Labs]** を選択します。
1. ラボの一覧で、使用する VM が含まれるラボを選択します。  
1. **[仮想マシン]** を選択します。
1. 目的の VM を選択します。
1. **[アーティファクトの管理]** を選択します。 
1. **[アーティファクトの適用]** を選択します。
1. **[アーティファクトの適用]** ウィンドウで、VM に追加するアーティファクトを選択します。
1. **[アーティファクトの追加]** ウィンドウで、必須のパラメーター値を入力します。必要に応じて、省略可能なパラメーターも入力します。  
1. **[追加]** を選択してアーティファクトを追加し、**[アーティファクトの適用]** ウィンドウに戻ります。
1. VM に必要なアーティファクトを引き続き追加します。
1. アーティファクトを追加したら、 [アーティファクトの実行順を変更する](#change-the-order-in-which-artifacts-are-run)ことができます。 [アーティファクトの表示または変更](#view-or-modify-an-artifact)に戻ることもできます。
1. アーティファクトの追加が完了したら、**[適用]** を選択します。

### <a name="change-the-order-in-which-artifacts-are-run"></a>アーティファクトの実行順序の変更
既定では、アーティファクトのアクションは VM に追加された順序で実行されます。 次の手順では、アーティファクトの実行順序を変更する方法を示しています。

1. **[アーティファクトの適用]** ウィンドウの上部で、VM に追加されたアーティファクトの数を示すリンクを選択します。
   
    ![Number of artifacts added to VM](./media/devtest-lab-add-vm-with-artifacts/devtestlab-add-artifacts-blade-selected-artifacts.png)
1. **[選択されたアーティファクト]** ウィンドウに、目的の順序になるようにアーティファクトをドラッグ アンド ドロップします。 アーティファクトをドラッグできない場合は、アーティファクトの左側からドラッグしていることを確認してください。 
1. 完了したら、 **[OK]** を選択します。  

### <a name="view-or-modify-an-artifact"></a>アーティファクトの表示または変更
次の手順では、アーティファクトのパラメーターを表示または変更する方法を示しています。

1. **[アーティファクトの適用]** ウィンドウの上部で、VM に追加されたアーティファクトの数を示すリンクを選択します。
   
    ![Number of artifacts added to VM](./media/devtest-lab-add-vm-with-artifacts/devtestlab-add-artifacts-blade-selected-artifacts.png)
1. **[選択されたアーティファクト]** ウィンドウで、表示または編集するアーティファクトを選択します。  
1. **[アーティファクトの追加]** ウィンドウで必要な変更を行ったら、**[OK]** を選択して **[アーティファクトの追加]** ウィンドウを閉じます。
1. **[OK]** を選択して、**[選択されたアーティファクト]** ウィドウを閉じます。

## <a name="use-powershell"></a>PowerShell の使用
次のスクリプトでは、指定された成果物を指定された VM に適用します。 [Invoke-AzResourceAction](/powershell/module/az.resources/invoke-azresourceaction) コマンドは、この操作を実行するコマンドです。  

```powershell
#Requires -Module Az.Resources

param
(
[Parameter(Mandatory=$true, HelpMessage="The ID of the subscription that contains the lab")]
   [string] $SubscriptionId,
[Parameter(Mandatory=$true, HelpMessage="The name of the lab containing the virtual machine")]
   [string] $DevTestLabName,
[Parameter(Mandatory=$true, HelpMessage="The name of the virtual machine")]
   [string] $VirtualMachineName,
[Parameter(Mandatory=$true, HelpMessage="The repository where the artifact is stored")]
   [string] $RepositoryName,
[Parameter(Mandatory=$true, HelpMessage="The artifact to apply to the virtual machine")]
   [string] $ArtifactName,
[Parameter(ValueFromRemainingArguments=$true)]
   $Params
)

# Set the appropriate subscription
Set-AzContext -SubscriptionId $SubscriptionId | Out-Null
 
# Get the lab resource group name
$resourceGroupName = (Get-AzResource -ResourceType 'Microsoft.DevTestLab/labs' | Where-Object { $_.Name -eq $DevTestLabName}).ResourceGroupName
if ($resourceGroupName -eq $null) { throw "Unable to find lab $DevTestLabName in subscription $SubscriptionId." }

# Get the internal repo name
$repository = Get-AzResource -ResourceGroupName $resourceGroupName `
                    -ResourceType 'Microsoft.DevTestLab/labs/artifactsources' `
                    -ResourceName $DevTestLabName `
                    -ApiVersion 2016-05-15 `
                    | Where-Object { $RepositoryName -in ($_.Name, $_.Properties.displayName) } `
                    | Select-Object -First 1

if ($repository -eq $null) { "Unable to find repository $RepositoryName in lab $DevTestLabName." }

# Get the internal artifact name
$template = Get-AzResource -ResourceGroupName $resourceGroupName `
                -ResourceType "Microsoft.DevTestLab/labs/artifactSources/artifacts" `
                -ResourceName "$DevTestLabName/$($repository.Name)" `
                -ApiVersion 2016-05-15 `
                | Where-Object { $ArtifactName -in ($_.Name, $_.Properties.title) } `
                | Select-Object -First 1

if ($template -eq $null) { throw "Unable to find template $ArtifactName in lab $DevTestLabName." }

# Find the virtual machine in Azure
$FullVMId = "/subscriptions/$SubscriptionId/resourceGroups/$resourceGroupName`
                /providers/Microsoft.DevTestLab/labs/$DevTestLabName/virtualmachines/$virtualMachineName"

$virtualMachine = Get-AzResource -ResourceId $FullVMId

# Generate the artifact id
$FullArtifactId = "/subscriptions/$SubscriptionId/resourceGroups/$resourceGroupName`
                        /providers/Microsoft.DevTestLab/labs/$DevTestLabName/artifactSources/$($repository.Name)`
                        /artifacts/$($template.Name)"

# Handle the inputted parameters to pass through
$artifactParameters = @()

# Fill artifact parameter with the additional -param_ data and strip off the -param_
$Params | ForEach-Object {
   if ($_ -match '^-param_(.*)') {
      $name = $_.TrimStart('^-param_')
   } elseif ( $name ) {
      $artifactParameters += @{ "name" = "$name"; "value" = "$_" }
      $name = $null #reset name variable
   }
}

# Create structure for the artifact data to be passed to the action

$prop = @{
artifacts = @(
    @{
        artifactId = $FullArtifactId
        parameters = $artifactParameters
    }
    )
}

# Check the VM
if ($virtualMachine -ne $null) {
   # Apply the artifact by name to the virtual machine
   $status = Invoke-AzResourceAction -Parameters $prop -ResourceId $virtualMachine.ResourceId -Action "applyArtifacts" -ApiVersion 2016-05-15 -Force
   if ($status.Status -eq 'Succeeded') {
      Write-Output "##[section] Successfully applied artifact: $ArtifactName to $VirtualMachineName"
   } else {
      Write-Error "##[error]Failed to apply artifact: $ArtifactName to $VirtualMachineName"
   }
} else {
   Write-Error "##[error]$VirtualMachine was not found in the DevTest Lab, unable to apply the artifact"
}

```

## <a name="next-steps"></a>次のステップ
成果物に関する次の記事をご覧ください。

- [ラボに必須の成果物の指定](devtest-lab-mandatory-artifacts.md)
- [カスタム アーティファクトを作成する](devtest-lab-artifact-author.md)
- [アーティファクト リポジトリをラボに追加する](devtest-lab-artifact-author.md)
- [アーティファクトの失敗の診断](devtest-lab-troubleshoot-artifact-failure.md)
