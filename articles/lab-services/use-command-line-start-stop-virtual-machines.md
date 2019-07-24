---
title: コマンドライン ツールを使用した Azure DevTest Labs VM の開始と停止 | Microsoft Docs
description: コマンドライン ツールを使用して、Azure DevTest Labs 内の仮想マシンを開始および停止する方法について説明します。
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/25/2019
ms.author: spelluru
ms.openlocfilehash: a8132735d1af08055e9341608dcac0564ed4b927
ms.sourcegitcommit: bf509e05e4b1dc5553b4483dfcc2221055fa80f2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/22/2019
ms.locfileid: "59996460"
---
# <a name="use-command-line-tools-to-start-and-stop-azure-devtest-labs-virtual-machines"></a>コマンドライン ツールを使用した Azure DevTest Labs 仮想マシンの開始と停止
この記事では、Azure PowerShell または Azure CLI を使用して、Azure DevTest Labs 内のラボにある仮想マシンを開始または停止する方法について説明します。 PowerShell/CLI スクリプトを作成してこの操作を自動化することができます。 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="overview"></a>概要
Azure DevTest Labs を使用すると、高速で簡単な無駄のない開発/テスト環境を作成できます。 コストを管理し、VM を迅速にプロビジョニングし、無駄を最小限に抑えることができるようになります。  Azure portal の組み込み機能を使用すると、ラボ内の VM を指定の時間に自動的に開始および停止するよう構成することができます。 

ただし、シナリオによっては、VM の自動開始と停止を PowerShell/CLI スクリプトから行うこともできます。 この場合、指定の時間ではなく任意の時間に個々のマシンを開始および停止できるという柔軟性が得られます。 スクリプトを使用してタスクを実行した方が便利なケースを、次にいくつか示します。

- 3 層アプリケーションをテスト環境の一部として使用している場合、各層を順番に開始する必要があります。 
- コスト削減のため、カスタム条件が満たされたときに VM をオフにします。 
- CI/CD ワークフロー内のタスクとして使用して、フローの最初に開始し、VM をビルド マシン、テスト マシン、またはインフラストラクチャとして使用し、プロセスの完了時に VM を停止します。 たとえば、Azure DevTest Labs を使用したカスタム イメージ ファクトリなどです。  

## <a name="azure-powershell"></a>Azure PowerShell
次の PowerShell スクリプトは、ラボ内の VM を開始します。 [Invoke-AzResourceAction](/powershell/module/az.resources/invoke-azresourceaction?view=azps-1.7.0) が、このスクリプトの主な目的です。 **ResourceId** パラメーターは、ラボ内の VM の完全修飾リソース ID です。 **Action** パラメーターには、**Start** または **Stop** オプションが必要に応じて設定されます。

```powershell
# The id of the subscription
$subscriptionId = "111111-11111-11111-1111111"

# The name of the lab
$devTestLabName = "yourlabname"

# The name of the virtual machine to be started
$vMToStart = "vmname"

# The action on the virtual machine (Start or Stop)
$vmAction = "Start"

# Select the Azure subscription
Select-AzSubscription -SubscriptionId $subscriptionId

# Get the lab information
if ($(Get-Module -Name AzureRM).Version.Major -eq 6) {
    $devTestLab = Get-AzResource -ResourceType 'Microsoft.DevTestLab/labs' -Name $devTestLabName
} else {
    $devTestLab = Find-AzResource -ResourceType 'Microsoft.DevTestLab/labs' -ResourceNameEquals $devTestLabName
}

# Start the VM and return a succeeded or failed status
$returnStatus = Invoke-AzResourceAction `
                    -ResourceId "$($devTestLab.ResourceId)/virtualmachines/$vMToStart" `
                    -Action $vmAction `
                    -Force

if ($returnStatus.Status -eq 'Succeeded') {
    Write-Output "##[section] Successfully updated DTL machine: $vMToStart, Action: $vmAction"
}
else {
    Write-Error "##[error]Failed to update DTL machine: $vMToStart, Action: $vmAction"
}
```


## <a name="azure-cli"></a>Azure CLI
[Azure CLI](/cli/azure/get-started-with-azure-cli?view=azure-cli-latest) は、DevTest Labs の VM を自動的に開始および停止するための、もう 1 つの方法です。 Azure CLI は別のオペレーティング システムに[インストール](/cli/azure/install-azure-cli?view=azure-cli-latest)することができます。 次のスクリプトは、ラボ内の VM を開始および停止するコマンドを示しています。 

```azurecli
# Sign in to Azure
az login 

## Get the name of the resource group that contains the lab
az resource list --resource-type "Microsoft.DevTestLab/labs" --name "yourlabname" --query "[0].resourceGroup" 

## Start the VM
az lab vm start --lab-name yourlabname --name vmname --resource-group labResourceGroupName

## Stop the VM
az lab vm stop --lab-name yourlabname --name vmname --resource-group labResourceGroupName
```


## <a name="next-steps"></a>次の手順
次の記事で、これらの操作を Azure portal を使用して行う方法について確認します: [VM の再起動](devtest-lab-restart-vm.md)に関する記事。
