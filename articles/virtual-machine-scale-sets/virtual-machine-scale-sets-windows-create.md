---
title: "PowerShell を使用して Azure 仮想マシン スケール セットを作成する | Microsoft Docs"
description: "PowerShell を使用して Azure 仮想マシン スケール セットを作成する"
services: virtual-machine-scale-sets
documentationcenter: 
author: Thraka
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 7bb03323-8bcc-4ee4-9a3e-144ca6d644e2
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 02/21/2017
ms.author: adegeo
translationtype: Human Translation
ms.sourcegitcommit: 1f8e66fac5b82698525794f0486dd0432c7421a7
ms.openlocfilehash: 7286fed39839675eb960b749f3235f83e36c5e9a
ms.lasthandoff: 02/22/2017


---
# <a name="create-a-windows-virtual-machine-scale-set-using-azure-powershell"></a>Azure PowerShell を使用した Windows 仮想マシン スケール セットの作成
以下の手順では、空白に記入する方式に従って Azure 仮想マシン スケール セットを作成します。 スケール セットについて詳しくは、「 [仮想マシン スケール セットの概要](virtual-machine-scale-sets-overview.md) 」をご覧ください。

この記事の手順を実行するには約 30 分かかります。

## <a name="step-1-install-azure-powershell"></a>手順 1: Azure PowerShell をインストールする
最新バージョンの Azure PowerShell をインストールし、サブスクリプションを選択して、ご利用のアカウントにサインインする方法については、「[Azure PowerShell のインストールおよび構成方法](/powershell/azureps-cmdlets-docs)」を参照してください。

## <a name="step-2-create-resources"></a>手順 2: リソースを作成する
新しいスケール セットに必要なリソースを作成します。

### <a name="resource-group"></a>リソース グループ
仮想マシン スケール セットは、リソース グループに含める必要があります。

1. リソースを配置できる場所の一覧を取得します。
   
        Get-AzureLocation | Sort Name | Select Name
2. 最適な場所を選択し、 **$locName** の値を場所の名前に置き換えた後、変数を作成します。
   
        $locName = "location name from the list, such as Central US"
3. **$rgName** の値を新しいリソース グループに使用する名前に置き換えた後、変数を作成します。 
   
        $rgName = "resource group name"
4. リソース グループを作成します。
   
        New-AzureRmResourceGroup -Name $rgName -Location $locName
   
    次のような結果が表示されます。
   
        ResourceGroupName : myrg1
        Location          : centralus
        ProvisioningState : Succeeded
        Tags              :
        ResourceId        : /subscriptions/########-####-####-####-############/resourceGroups/myrg1

### <a name="virtual-network"></a>Virtual Network
仮想ネットワークは、スケール セット内の仮想マシンのために必要です。

1. **$subnetName** の値を、仮想ネットワーク内のサブネットに使用する名前に置き換えた後、変数を作成します。 
   
        $subnetName = "subnet name"
2. サブネット構成を作成します。
   
        $subnet = New-AzureRmVirtualNetworkSubnetConfig -Name $subnetName -AddressPrefix 10.0.0.0/24
   
    お使いの仮想ネットワークではアドレス プレフィックスが異なる場合があります。
3. **$netName** の値を、仮想ネットワークに使用する名前に置き換えた後、変数を作成します。 
   
        $netName = "virtual network name"
4. 仮想ネットワークを作成します。
   
        $vnet = New-AzureRmVirtualNetwork -Name $netName -ResourceGroupName $rgName -Location $locName -AddressPrefix 10.0.0.0/16 -Subnet $subnet

### <a name="configuration-of-the-scale-set"></a>スケール セットの構成
スケール セットの構成に必要なリソースはすべて揃っているため、作成を開始しましょう。  

1. **$ipName** の値を、IP 構成に使用する名前に置き換えた後、変数を作成します。 
   
        $ipName = "IP configuration name"
2. IP 構成を作成します。
   
        $ipConfig = New-AzureRmVmssIpConfig -Name $ipName -LoadBalancerBackendAddressPoolsId $null -SubnetId $vnet.Subnets[0].Id
3. **$vmssConfig** の値を、スケール セットの構成に使用する名前に置き換えた後、変数を作成します。   
   
        $vmssConfig = "Scale set configuration name"
4. スケール セットの構成を作成します。
   
        $vmss = New-AzureRmVmssConfig -Location $locName -SkuCapacity 3 -SkuName "Standard_A0" -UpgradePolicyMode "manual"
   
    この例では、3 つの仮想マシンを使用して作成されるスケール セットを示しています。 スケール セットの容量について詳しくは、「 [仮想マシン スケール セットの概要](virtual-machine-scale-sets-overview.md) 」をご覧ください。 この手順では、セット内の仮想マシンのサイズ (SkuName と呼ばれます) も設定します。 自分のニーズに応じたサイズを見つけるには、[仮想マシンのサイズ](../virtual-machines/virtual-machines-windows-sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)に関するページを参照してください。
5. ネットワーク インターフェイス構成をスケール セット構成に追加します。
   
        Add-AzureRmVmssNetworkInterfaceConfiguration -VirtualMachineScaleSet $vmss -Name $vmssConfig -Primary $true -IPConfiguration $ipConfig
   
    次のような結果が表示されます。
   
        Sku                   : Microsoft.Azure.Management.Compute.Models.Sku
        UpgradePolicy         : Microsoft.Azure.Management.Compute.Models.UpgradePolicy
        VirtualMachineProfile : Microsoft.Azure.Management.Compute.Models.VirtualMachineScaleSetVMProfile
        ProvisioningState     :
        OverProvision         :
        Id                    :
        Name                  :
        Type                  :
        Location              : Central US
        Tags                  :

#### <a name="operating-system-profile"></a>オペレーティング システム プロファイル
1. **$computerName** の値を、使用するコンピューター名プレフィックスに置き換えた後、変数を作成します。 
   
        $computerName = "computer name prefix"
2. **$adminName** の値を、仮想マシンの管理者アカウント名に置き換えた後、変数を作成します。
   
        $adminName = "administrator account name"
3. **$adminPassword** の値を、アカウント パスワードに置き換えた後、変数を作成します。
   
        $adminPassword = "password for administrator accounts"
4. オペレーティング システム プロファイルを作成します。
   
        Set-AzureRmVmssOsProfile -VirtualMachineScaleSet $vmss -ComputerNamePrefix $computerName -AdminUsername $adminName -AdminPassword $adminPassword

#### <a name="storage-profile"></a>ストレージ プロファイル
1. **$storageProfile** の値を、ストレージ プロファイルに使用する名前に置き換えた後、変数を作成します。  
   
        $storageProfile = "storage profile name"
2. 使用するイメージを定義する変数を作成します。  
   
        $imagePublisher = "MicrosoftWindowsServer"
        $imageOffer = "WindowsServer"
        $imageSku = "2012-R2-Datacenter"
   
    使用する他のイメージについて詳しくは、「[PowerShell または CLI を使用した Azure での Windows 仮想マシン イメージへの移動と選択](../virtual-machines/virtual-machines-windows-cli-ps-findimage.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)」をご覧ください。

3. ストレージ プロファイルを作成します。
   
        Set-AzureRmVmssStorageProfile -VirtualMachineScaleSet $vmss -ImageReferencePublisher $imagePublisher -ImageReferenceOffer $imageOffer -ImageReferenceSku $imageSku -ImageReferenceVersion "latest" -OsDiskCreateOption "FromImage" -OsDiskCaching "None"  

### <a name="virtual-machine-scale-set"></a>仮想マシン スケール セット
ここでようやく、スケール セットを作成できます。

1. **$vmssName** の値を、仮想マシン スケール セットの名前に置き換えた後、変数を作成します。
   
        $vmssName = "scale set name"
2. スケール セットを作成します。
   
        New-AzureRmVmss -ResourceGroupName $rgName -Name $vmssName -VirtualMachineScaleSet $vmss
   
    正常なデプロイを示している次の例のように表示されるはずです。
   
        Sku                   : Microsoft.Azure.Management.Compute.Models.Sku
        UpgradePolicy         : Microsoft.Azure.Management.Compute.Models.UpgradePolicy
        VirtualMachineProfile : Microsoft.Azure.Management.Compute.Models.VirtualMachineScaleSetVMProfile
        ProvisioningState     : Updating
        OverProvision         :
        Id                    : /subscriptions/########-####-####-####-############/resourceGroups/myrg1/providers/Microso
                                ft.Compute/virtualMachineScaleSets/myvmss1
        Name                  : myvmss1
        Type                  : Microsoft.Compute/virtualMachineScaleSets
        Location              : centralus
        Tags                  :

## <a name="step-3-explore-resources"></a>手順 3: リソースを調査する
次のリソースを使って、作成した仮想マシン スケール セットを調査します。

* Azure ポータル - ポータルを使用して入手できる情報の量は限られています。
* [Azure リソース エクスプローラー](https://resources.azure.com/) - このツールは、スケール セットの現在の状態を調査するうえで最適なツールです。
* Azure PowerShell - 次のコマンドを使用して情報を得ることができます。
  
        Get-AzureRmVmss -ResourceGroupName "resource group name" -VMScaleSetName "scale set name"
  
        Or 
  
        Get-AzureRmVmssVM -ResourceGroupName "resource group name" -VMScaleSetName "scale set name"

## <a name="next-steps"></a>次のステップ
* 「 [仮想マシン スケール セットで仮想マシンを管理する](virtual-machine-scale-sets-windows-manage.md)
* 「 [自動スケールと仮想マシン スケール セット](virtual-machine-scale-sets-autoscale-overview.md)
* 「 [仮想マシン スケール セットを使用した垂直方向の自動スケール](virtual-machine-scale-sets-vertical-scale-reprovision.md)


