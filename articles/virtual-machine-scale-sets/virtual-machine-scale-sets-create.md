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
ms.devlang: azurecli
ms.topic: article
ms.date: 07/21/2017
ms.author: adegeo
ms.openlocfilehash: 32af01aa545c541688128a7ae6bbb82a0e046f2d
ms.sourcegitcommit: 02e69c4a9d17645633357fe3d46677c2ff22c85a
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/03/2017
---
# <a name="create-and-deploy-a-virtual-machine-scale-set"></a>仮想マシン スケール セットの作成とデプロイ
仮想マシン スケール セットを使用すると、まったく同じ仮想マシンを簡単にまとめてデプロイし、管理することができます。 スケール セットは、最高水準のスケーラビリティが要求されるアプリケーションのための、拡張性が高くカスタマイズしやすいコンピューティング レイヤーです。Windows プラットフォーム イメージのほか、Linux プラットフォーム イメージ、カスタム イメージ、拡張機能をサポートしています。 スケール セットの詳細については、「[仮想マシン スケール セットの概要](virtual-machine-scale-sets-overview.md)」を参照してください。

このチュートリアルでは、Azure Portal を**使用せずに**、仮想マシン スケール セットを作成する方法について説明します。 Azure Portal を使用する方法については、「[Azure Portal を使用して仮想マシン スケール セットを作成する方法](virtual-machine-scale-sets-portal-create.md)」をご覧ください。

>[!NOTE]
>Azure Resource Manager のリソースの詳細については、[Azure Resource Manager デプロイとクラシック デプロイ](../azure-resource-manager/resource-manager-deployment-model.md)に関する記事をご覧ください。

## <a name="sign-in-to-azure"></a>Azure へのサインイン

Azure CLI 2.0 または Azure PowerShell を使用してスケール セットを作成する場合、まずサブスクリプションにサインインする必要があります。

Azure CLI または PowerShell を使用して Azure をインストールおよびセットアップし、Azure にサインインする方法の詳細については、[Azure CLI 2.0 の概要](/cli/azure/get-started-with-azure-cli.md)に関する記事または [Azure PowerShell コマンドレットの概要](/powershell/azure/overview)に関する記事をご覧ください。

```azurecli
az login
```

```powershell
Login-AzureRmAccount
```

## <a name="create-a-resource-group"></a>リソース グループの作成

まず、仮想マシン スケール セットを関連付けるリソース グループを作成する必要があります。

```azurecli
az group create --location westus2 --name MyResourceGroup1
```

```powershell
New-AzureRmResourceGroup -Location westus2 -Name MyResourceGroup1
```

## <a name="create-from-azure-cli"></a>Azure CLI を使用して作成する

Azure CLI を使用すると、最小限の作業で仮想マシン スケール セットを作成できます。 既定値を省略すると、自動的に提供されます。 たとえば、仮想ネットワークの情報を指定していない場合、仮想ネットワークが自動的に作成されます。 次の部分を省略すると、自動的に提供されます。 
- ロード バランサー
- 仮想ネットワーク
- パブリック IP アドレス

仮想マシン スケール セットで使用する仮想マシン イメージを選択するときは、いくつかの選択肢があります。

- URN  
リソースの識別子:  
**Win2012R2Datacenter**

- URN エイリアス  
URN のフレンドリ名:  
**MicrosoftWindowsServer:WindowsServer:2012-R2-Datacenter:latest**

- カスタム リソース ID  
Azure リソースのパス:  
**/subscriptions/subscription-guid/resourceGroups/MyResourceGroup/providers/Microsoft.Compute/images/MyImage**

- Web リソース  
HTTP URI のパス:  
**http://contoso.blob.core.windows.net/vhds/osdiskimage.vhd**

>[!TIP]
>`az vm image list` を使用して、利用可能なイメージの一覧を取得できます。

仮想マシン スケール セットを作成するには、次を指定する必要があります。

- リソース グループ 
- 名前
- オペレーティング システム イメージ
- 認証情報 
 
次の例では、基本的な仮想マシン スケール セットを作成します (この手順には数分かかる場合があります)。

```azurecli
az vmss create --resource-group MyResourceGroup1 --name MyScaleSet --image UbuntuLTS --authentication-type password --admin-username azureuser --admin-password P@ssw0rd!
```

コマンドが完了すると、仮想マシン スケール セットが作成されます。 仮想マシンに接続するには、IP アドレスを取得する必要がある場合があります。 次のコマンドを実行すると、仮想マシンに関する各種情報 (IP アドレスを含む) を取得できます。 

```azurecli
az vmss list-instance-connection-info --resource-group MyResourceGroup1 --name MyScaleSet
```

## <a name="create-from-powershell"></a>PowerShell を使用して作成する

PowerShell を使用する場合、Azure CLI よりも複雑になります。 Azure CLI ではネットワーク関連のリソースの既定値 (ロード バランサー、IP アドレス、仮想ネットワークなど) が提供されますが、PowerShell では提供されません。 PowerShell ではイメージの参照方法も少し複雑になります。 イメージは、次のコマンドレットを使用して取得できます。

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

この例では、Windows Server 2016 がインストールされたコンピューターの基本的な 2 インスタンス スケール セットを作成します。

```powershell
# Resource group name from above
$rg = "MyResourceGroup1"
$location = "WestUS2"

# Create a config object
$vmssConfig = New-AzureRmVmssConfig -Location $location -SkuCapacity 2 -SkuName Standard_A0  -UpgradePolicyMode Automatic

# Reference a virtual machine image from the gallery
Set-AzureRmVmssStorageProfile $vmssConfig -ImageReferencePublisher MicrosoftWindowsServer -ImageReferenceOffer WindowsServer -ImageReferenceSku 2016-Datacenter -ImageReferenceVersion latest

# Set up information for authenticating with the virtual machine
Set-AzureRmVmssOsProfile $vmssConfig -AdminUsername azureuser -AdminPassword P@ssw0rd! -ComputerNamePrefix myvmssvm

# Create the virtual network resources

## Basics
$subnet = New-AzureRmVirtualNetworkSubnetConfig -Name "my-subnet" -AddressPrefix 10.0.0.0/24
$vnet = New-AzureRmVirtualNetwork -Name "my-network" -ResourceGroupName $rg -Location $location -AddressPrefix 10.0.0.0/16 -Subnet $subnet

## Load balancer
$publicIP = New-AzureRmPublicIpAddress -Name "PublicIP" -ResourceGroupName $rg -Location $location -AllocationMethod Static -DomainNameLabel "myuniquedomain"
$frontendIP = New-AzureRmLoadBalancerFrontendIpConfig -Name "LB-Frontend" -PublicIpAddress $publicIP
$backendPool = New-AzureRmLoadBalancerBackendAddressPoolConfig -Name "LB-backend"
$probe = New-AzureRmLoadBalancerProbeConfig -Name "HealthProbe" -Protocol Tcp -Port 80 -IntervalInSeconds 15 -ProbeCount 2
$inboundNATRule1= New-AzureRmLoadBalancerRuleConfig -Name "webserver" -FrontendIpConfiguration $frontendIP -Protocol Tcp -FrontendPort 80 -BackendPort 80 -IdleTimeoutInMinutes 15 -Probe $probe -BackendAddressPool $backendPool
$inboundNATPool1 = New-AzureRmLoadBalancerInboundNatPoolConfig -Name "RDP" -FrontendIpConfigurationId $frontendIP.Id -Protocol TCP -FrontendPortRangeStart 53380 -FrontendPortRangeEnd 53390 -BackendPort 3389

New-AzureRmLoadBalancer -ResourceGroupName $rg -Name "LB1" -Location $location -FrontendIpConfiguration $frontendIP -LoadBalancingRule $inboundNATRule1 -InboundNatPool $inboundNATPool1 -BackendAddressPool $backendPool -Probe $probe

## IP address config
$ipConfig = New-AzureRmVmssIpConfig -Name "my-ipaddress" -LoadBalancerBackendAddressPoolsId $backendPool.Id -SubnetId $vnet.Subnets[0].Id -LoadBalancerInboundNatPoolsId $inboundNATPool1.Id

# Attach the virtual network to the IP object
Add-AzureRmVmssNetworkInterfaceConfiguration -VirtualMachineScaleSet $vmssConfig -Name "network-config" -Primary $true -IPConfiguration $ipConfig

# Create the scale set with the config object (this step might take a few minutes)
New-AzureRmVmss -ResourceGroupName $rg -Name "MyScaleSet1" -VirtualMachineScaleSet $vmssConfig
```

### <a name="using-a-custom-virtual-machine-image"></a>カスタムの仮想マシン イメージを使用します。
スケール、ギャラリーから仮想マシン イメージを参照するのではなく、独自のカスタム イメージからセットを作成する場合は、_セット AzureRmVmssStorageProfile_コマンドは次のようになります。
```PowerShell
Set-AzureRmVmssStorageProfile -OsDiskCreateOption FromImage -ManagedDisk PremiumLRS -OsDiskCaching "None" -OsDiskOsType Linux -ImageReferenceId (Get-AzureRmImage -ImageName $VMImage -ResourceGroupName $rg).id
```

## <a name="create-from-a-template"></a>テンプレートを使用して作成する

Azure Resource Manager テンプレートを使用して、仮想マシン スケール セットをデプロイできます。 独自のテンプレートを作成することも、[テンプレート リポジトリ](https://azure.microsoft.com/resources/templates/?term=vmss)のテンプレートを使用することもできます。 これらのテンプレートは、Azure サブスクリプションに直接デプロイできます。

>[!NOTE]
>独自のテンプレートを作成するには、JSON テキスト ファイルを作成します。 テンプレートを作成してカスタマイズする方法の概要については、[Azure Resource Manager テンプレート](../azure-resource-manager/resource-group-authoring-templates.md)に関する記事をご覧ください。

サンプル テンプレートは [GitHub](https://github.com/gatneil/mvss/tree/minimum-viable-scale-set) で入手できます。 サンプルを作成して使用する方法の詳細については、「[実行可能な最小のスケール セット](.\virtual-machine-scale-sets-mvss-start.md)」をご覧ください。

## <a name="create-from-visual-studio"></a>Visual Studio を使用して作成する

Visual Studio を使用して、Azure リソース グループ プロジェクトを作成し、仮想マシン スケール セット テンプレートをそのプロジェクトに追加できます。 GitHub または Azure Web アプリケーション ギャラリーからインポートできます。 PowerShell デプロイ スクリプトも生成されます。 詳細については、「[Visual Studio による仮想マシン スケール セットの作成方法](virtual-machine-scale-sets-vs-create.md)」をご覧ください。

## <a name="create-from-the-azure-portal"></a>Azure Portal を使用して作成する

Azure Portal を使用すると、スケール セットをすばやく簡単に作成できます。 詳細については、「[Azure Portal を使用して仮想マシン スケール セットを作成する方法](virtual-machine-scale-sets-portal-create.md)」をご覧ください。

## <a name="next-steps"></a>次のステップ

[データ ディスク](virtual-machine-scale-sets-attached-disks.md)についてさらに理解を深める。

[アプリを管理する](virtual-machine-scale-sets-deploy-app.md)方法について学習する。
