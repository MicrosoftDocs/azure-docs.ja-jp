---
title: コマンドライン ツールを使用した Azure DevTest Labs VM の開始と停止
description: コマンドライン ツールを使用して、Azure DevTest Labs 内の仮想マシンを開始および停止する方法について説明します。
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 2ddc1620cf86fa203b2f0e31359f9fd262df8916
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102499545"
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

> [!NOTE]
> 次のスクリプトは Azure PowerShell Az モジュールを使用します。 

次の PowerShell スクリプトは、ラボ内の VM を開始します。 [Invoke-AzResourceAction](/powershell/module/az.resources/invoke-azresourceaction) が、このスクリプトの主な目的です。 **ResourceId** パラメーターは、ラボ内の VM の完全修飾リソース ID です。 **Action** パラメーターには、**Start** または **Stop** オプションが必要に応じて設定されます。

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
$devTestLab = Get-AzResource -ResourceType 'Microsoft.DevTestLab/labs' -ResourceName $devTestLabName

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
[Azure CLI](/cli/azure/get-started-with-azure-cli) は、DevTest Labs の VM を自動的に開始および停止するための、もう 1 つの方法です。 Azure CLI は別のオペレーティング システムに[インストール](/cli/azure/install-azure-cli)することができます。 次のスクリプトは、ラボ内の VM を開始および停止するコマンドを示しています。 

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


## <a name="next-steps"></a>次のステップ
Azure portal を使用してこれらの操作を行う方法については、[VM の再起動](devtest-lab-restart-vm.md)に関する記事を参照してください。
