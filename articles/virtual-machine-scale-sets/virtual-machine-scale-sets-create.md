---
title: "Azure 仮想マシン スケール セットを作成する | Microsoft Docs"
description: "Azure CLI、PowerShell、テンプレート、または Visual Studio を使用して、Linux または Windows の Azure 仮想マシン スケール セットを作成し、デプロイします。"
services: virtual-machine-scale-sets
documentationcenter: 
author: Thraka
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machine-scale-sets
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/20/2017
ms.author: adegeo
translationtype: Human Translation
ms.sourcegitcommit: 424d8654a047a28ef6e32b73952cf98d28547f4f
ms.openlocfilehash: 949e59b64557ac3efc07da73a205c808e25aeaab
ms.lasthandoff: 03/22/2017

---

# <a name="create-and-deploy-a-virtual-machine-scale-set"></a>仮想マシン スケール セットの作成とデプロイ
仮想マシン スケール セットを使用すると、まったく同じ仮想マシンを簡単にまとめてデプロイし、管理することができます。 スケール セットは、最高水準のスケーラビリティが要求されるアプリケーションのための、拡張性が高くカスタマイズしやすいコンピューティング レイヤーです。Windows プラットフォーム イメージのほか、Linux プラットフォーム イメージ、カスタム イメージ、拡張機能をサポートしています。 スケール セットの詳細については、「[仮想マシン スケール セットの概要](virtual-machine-scale-sets-overview.md)」を参照してください。

このチュートリアルでは、Azure Portal を**使用せずに**、仮想マシン スケール セットを作成する方法について説明します。 Azure Portal を使用する方法については、「[Azure Portal を使用して仮想マシン スケール セットを作成する方法](virtual-machine-scale-sets-portal-create.md)」をご覧ください。

>[!NOTE]
>Azure Resource Manager のリソースの詳細については、[Azure Resource Manager デプロイとクラシック デプロイ](../azure-resource-manager/resource-manager-deployment-model.md)に関する記事をご覧ください。

## <a name="log-in-to-azure"></a>Azure にログインする

Azure CLI 2.0 または PowerShell を使用してスケール セットを作成する場合、まずサブスクリプションにログインする必要があります。

Azure CLI 2.0 または PowerShell を使用して Azure をインストールおよびセットアップし、Azure にログインする方法の詳細については、[Azure CLI 2.0 の概要](/cli/azure/get-started-with-azure-cli.md)に関する記事または [Azure PowerShell コマンドレットの概要](/powershell/resourcemanager/)に関する記事をご覧ください。

```azurecli
az login
```

```powershell
Login-AzureRmAccount
```

## <a name="prep-create-a-resource-group"></a>準備: リソース グループを作成する

まず、仮想マシン スケール セットを関連付けるリソース グループを作成する必要があります。

```azurecli
az group create --location westus2 --name vmss-test-1
```

```powershell
New-AzureRmResourceGroup -Location westus2 -Name vmss-test-1
```

## <a name="create-from-azure-cli"></a>Azure CLI を使用して作成する

Azure CLI を使用すると、最小限の作業で仮想マシン スケール セットを作成できます。 値を省略すると、既定値が自動的に提供されます。 たとえば、仮想ネットワークの情報を指定していない場合、情報が自動的に作成されます。 指定を省略した場合、ロード バランサー、VNET、パブリック IP アドレスが作成されます。

仮想マシン スケール セットで使用する仮想マシン イメージを選択するときは、いくつかの選択肢があります。

1. URN  
リソースの識別子:  
**Win2012R2Datacenter**

2. URN エイリアス  
URN のフレンドリ名:  
**MicrosoftWindowsServer:WindowsServer:2012-R2-Datacenter:latest**

3. カスタム リソース ID  
Azure リソースのパス:  
**/subscriptions/subscription-guid/resourceGroups/MyResourceGroup/providers/Microsoft.Compute/images/MyImage**

4. Web リソース  
HTTP URI のパス:  
**http://contoso.blob.core.windows.net/vhds/osdiskimage.vhd**

>[!TIP]
>`az vm image list` を使用して、利用可能なイメージの一覧を取得できます。

仮想マシン スケール セットを作成するには、_リソース グループ_、_名前_、_オペレーティング システム イメージ_、_認証情報_を指定する必要があります。 次の例では、基本的な仮想マシン スケール セットを作成します (この手順には数分かかる場合があります)。

```azurecli
az vmss create --resource-group vmss-test-1 --name MyScaleSet --image UbuntuLTS --authentication-type password --admin-username azureuser --admin-password P@ssw0rd!
```

## <a name="create-from-powershell"></a>PowerShell を使用して作成する

PowerShell を使用する場合、Azure CLI よりも複雑になります。 Azure CLI ではネットワーク関連のリソースの既定値 (ロード バランサー、IP アドレス、仮想ネットワーク) が提供されますが、PowerShell では提供されません。 イメージの参照方法も少し複雑になります。 イメージは、次のコマンドレットを使用して取得できます。

1. Get-AzureRMVMImagePublisher
2. Get-AzureRMVMImageOffer
3. Get-AzureRmVMImageSku

これらのコマンドレットの動作は、順番にパイプ処理できます。 発行元の名前が **microsoft** である、**米国西部 2** リージョンのすべてのイメージを取得する方法の例を次に示します。

```powershell
Get-AzureRMVMImagePublisher -Location WestUS2 | Where-Object PublisherName -Like *microsoft* | Get-AzureRMVMImageOffer | Get-AzureRmVMImageSku | Select-Object PublisherName, Offer, Skus
```

```
PublisherName              Offer                    Skus
-------------              -----                    ----
microsoft-ads              linux-data-science-vm    linuxdsvm
microsoft-ads              standard-data-science-vm standard-data-science-vm
MicrosoftAzureSiteRecovery Process-Server           Windows-2012-R2-Datacenter
MicrosoftBizTalkServer     BizTalk-Server           2013-R2-Enterprise
MicrosoftBizTalkServer     BizTalk-Server           2013-R2-Standard
MicrosoftBizTalkServer     BizTalk-Server           2016-Developer
MicrosoftBizTalkServer     BizTalk-Server           2016-Enterprise
...
```

仮想マシン スケール セットを作成するためのワークフローは次のとおりです。

1. スケール セットに関する情報を保持する構成オブジェクトを作成する。
2. ベース OS イメージを参照する。
3. オペレーティング システム設定 (認証、VM 名のプレフィックス、ユーザー名/パスワード) を構成する。
4. ネットワークを構成する。
5. スケール セットを作成する。

次の例では、Windows Server 2016 がインストールされた基本的な 2 インスタンス スケール セットを作成します。

```powershell
# Create a config object
$vmssConfig = New-AzureRmVmssConfig -Location WestUS2 -SkuCapacity 2 -SkuName Standard_A0  -UpgradePolicyMode Automatic

# Reference a virtual machine image from the gallery
Set-AzureRmVmssStorageProfile $vmssConfig -ImageReferencePublisher MicrosoftWindowsServer -ImageReferenceOffer WindowsServer -ImageReferenceSku 2016-Datacenter -ImageReferenceVersion latest

# Setup information about how to authenticate with the virtual machine
Set-AzureRmVmssOsProfile $vmssConfig -AdminUsername azureuser -AdminPassword P@ssw0rd! -ComputerNamePrefix myvmssvm

# Create the virtual network resources
$subnet = New-AzureRmVirtualNetworkSubnetConfig -Name "my-subnet" -AddressPrefix 10.0.0.0/24
$vnet = New-AzureRmVirtualNetwork -Name "my-network" -ResourceGroupName "vmss-test-1" -Location "westus2" -AddressPrefix 10.0.0.0/16 -Subnet $subnet
$ipConfig = New-AzureRmVmssIpConfig -Name "my-ip-address" -LoadBalancerBackendAddressPoolsId $null -SubnetId $vnet.Subnets[0].Id

# Attach the virtual network to the config object
Add-AzureRmVmssNetworkInterfaceConfiguration -VirtualMachineScaleSet $vmssConfig -Name "network-config" -Primary $true -IPConfiguration $ipConfig

# Create the scale set with the config object (this step may take a few minutes)
New-AzureRmVmss -ResourceGroupName vmss-test-1 -Name my-scale-set -VirtualMachineScaleSet $vmssConfig
```

## <a name="create-from-a-template"></a>テンプレートを使用して作成する

Azure Resource Manager テンプレートを使用して、仮想マシン スケール セットをデプロイできます。 独自のテンプレートを作成することも、[テンプレート リポジトリ](https://azure.microsoft.com/resources/templates/?term=vmss)のテンプレートを使用することもできます。 これらのテンプレートは、Azure サブスクリプションに直接デプロイできます。

>[!NOTE]
>独自のテンプレートを作成するには、_.json_ テキスト ファイルを作成します。 テンプレートを作成してカスタマイズする方法の概要については、[Azure Resource Manager テンプレート](../azure-resource-manager/resource-group-authoring-templates.md)に関する記事をご覧ください。

サンプル テンプレートは [GitHub](https://github.com/gatneil/mvss/tree/minimum-viable-scale-set) で入手できます。 サンプルを作成して使用する方法の詳細については、「[実行可能な最小のスケール セット](.\virtual-machine-scale-sets-mvss-start.md)」をご覧ください。

## <a name="create-from-visual-studio"></a>Visual Studio を使用して作成する

Visual Studio を使用して、Azure リソース グループ プロジェクトを作成し、仮想マシン スケール セット テンプレートをそのプロジェクトに追加できます。 GitHub や Azure ギャラリーから、インポートするテンプレートを選択できます。 PowerShell デプロイ スクリプトも生成されます。 詳細については、「[Visual Studio による仮想マシン スケール セットの作成方法](virtual-machine-scale-sets-vs-create.md)」をご覧ください。

## <a name="create-from-the-azure-portal"></a>Azure Portal を使用して作成する

Azure Portal を使用すると、スケール セットをすばやく簡単に作成できます。 詳細については、「[Azure Portal を使用して仮想マシン スケール セットを作成する方法](virtual-machine-scale-sets-portal-create.md)」をご覧ください。

