---
title: Azure PowerShell を使用して Azure 専用ホストにデプロイする
description: Azure PowerShell を使用して専用ホストに VM をデプロイします。
author: cynthn
ms.service: virtual-machines
ms.subservice: dedicated-hosts
ms.topic: how-to
ms.workload: infrastructure
ms.date: 11/12/2020
ms.author: cynthn
ms.reviewer: zivr
ms.openlocfilehash: ed6319d5374db56cfe85e7ef9413480e523d9a34
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102050887"
---
# <a name="deploy-vms-to-dedicated-hosts-using-the-azure-powershell"></a>Azure PowerShell を使用して専用ホストに VM をデプロイする

この記事では、仮想マシン (VM) をホストするための Azure [専用ホスト](../dedicated-hosts.md)を作成する方法について説明します。 

Azure PowerShell バージョン 2.8.0 以降がインストールされていて、`Connect-AzAccount` を使用して Azure アカウントにサインインしていることを確認します。 

## <a name="limitations"></a>制限事項

- 専用ホストで使用できるサイズとハードウェアの種類は、リージョンによって異なります。 詳しくは、ホストの[価格のページ](https://aka.ms/ADHPricing) を参照してください。

## <a name="create-a-host-group"></a>ホスト グループを作成する

**ホスト グループ** は、専用ホストのコレクションを表すリソースです。 リージョンと可用性ゾーンにホスト グループを作成し、それにホストを追加します。 高可用性を計画する場合は、追加のオプションがあります。 専用ホストでは、次のいずれかまたは両方のオプションを使用できます。 
- 複数の可用性ゾーンにまたがります。 この場合は、使用する各ゾーンにホスト グループを用意する必要があります。
- 物理ラックにマップされる複数の障害ドメインにまたがります。 
 
どちらの場合も、ホスト グループに対して障害ドメイン数を指定する必要があります。 グループ内で障害ドメインをまたがりたくない場合は、障害ドメインの数を 1 にします。 

可用性ゾーンと障害ドメインの両方を使用することもできます。 この例では、ゾーン 1 の 1 つのホスト グループと 2 つの障害ドメインを作成します。 


```azurepowershell-interactive
$rgName = "myDHResourceGroup"
$location = "EastUS"

New-AzResourceGroup -Location $location -Name $rgName
$hostGroup = New-AzHostGroup `
   -Location $location `
   -Name myHostGroup `
   -PlatformFaultDomain 2 `
   -ResourceGroupName $rgName `
   -Zone 1
```


`-SupportAutomaticPlacement true` パラメーターを追加すると、VM とスケール セット インスタンスがホスト グループ内のホストに自動的に配置されるようになります。 詳しくは、[手動による配置と自動配置](../dedicated-hosts.md#manual-vs-automatic-placement)に関するページをご覧ください。


## <a name="create-a-host"></a>ホストを作成する

次に、ホスト グループに専用ホストを作成してみましょう。 ホストの名前に加えて、ホストの SKU を指定する必要があります。 ホスト SKU では、専用ホストに対してサポートされている VM シリーズとハードウェアの世代がキャプチャされます。

ホスト SKU の詳細と価格については、「[Azure 専用ホストの価格](https://aka.ms/ADHPricing)」を参照してください。

ホスト グループの障害ドメイン数を設定した場合は、ホストの障害ドメインを指定するように求められます。 この例では、ホストの障害ドメインを 1 に設定します。


```azurepowershell-interactive
$dHost = New-AzHost `
   -HostGroupName $hostGroup.Name `
   -Location $location -Name myHost `
   -ResourceGroupName $rgName `
   -Sku DSv3-Type1 `
   -AutoReplaceOnFailure 1 `
   -PlatformFaultDomain 1
```

## <a name="create-a-vm"></a>VM の作成

専用ホストに仮想マシンを作成します。 

ホスト グループを作成するときに可用性ゾーンを指定した場合は、仮想マシンを作成するときに同じゾーンを使用する必要があります。 この例では、ホスト グループがゾーン 1 にあるため、ゾーン 1 に VM を作成する必要があります。  


```azurepowershell-interactive
$cred = Get-Credential
New-AzVM `
   -Credential $cred `
   -ResourceGroupName $rgName `
   -Location $location `
   -Name myVM `
   -HostId $dhost.Id `
   -Image Win2016Datacenter `
   -Zone 1 `
   -Size Standard_D4s_v3
```

> [!WARNING]
> 十分なリソースがないホストに仮想マシンを作成すると、仮想マシンは FAILED 状態で作成されます。 

## <a name="check-the-status-of-the-host"></a>ホストの状態を確認する

[GetAzHost](/powershell/module/az.compute/get-azhost) と `-InstanceView` パラメーターを使用して、ホストの正常性状態と、ホストにデプロイできる仮想マシンの数を確認できます。

```azurepowershell-interactive
Get-AzHost `
   -ResourceGroupName $rgName `
   -Name myHost `
   -HostGroupName $hostGroup.Name `
   -InstanceView
```

出力は次のようになります。

```
ResourceGroupName      : myDHResourceGroup
PlatformFaultDomain    : 1
AutoReplaceOnFailure   : True
HostId                 : 12345678-1234-1234-abcd-abc123456789
ProvisioningTime       : 7/28/2019 5:31:01 PM
ProvisioningState      : Succeeded
InstanceView           : 
  AssetId              : abc45678-abcd-1234-abcd-123456789abc
  AvailableCapacity    : 
    AllocatableVMs[0]  : 
      VmSize           : Standard_D2s_v3
      Count            : 32
    AllocatableVMs[1]  : 
      VmSize           : Standard_D4s_v3
      Count            : 16
    AllocatableVMs[2]  : 
      VmSize           : Standard_D8s_v3
      Count            : 8
    AllocatableVMs[3]  : 
      VmSize           : Standard_D16s_v3
      Count            : 4
    AllocatableVMs[4]  : 
      VmSize           : Standard_D32-8s_v3
      Count            : 2
    AllocatableVMs[5]  : 
      VmSize           : Standard_D32-16s_v3
      Count            : 2
    AllocatableVMs[6]  : 
      VmSize           : Standard_D32s_v3
      Count            : 2
    AllocatableVMs[7]  : 
      VmSize           : Standard_D64-16s_v3
      Count            : 1
    AllocatableVMs[8]  : 
      VmSize           : Standard_D64-32s_v3
      Count            : 1
    AllocatableVMs[9]  : 
      VmSize           : Standard_D64s_v3
      Count            : 1
  Statuses[0]          : 
    Code               : ProvisioningState/succeeded
    Level              : Info
    DisplayStatus      : Provisioning succeeded
    Time               : 7/28/2019 5:31:01 PM
  Statuses[1]          : 
    Code               : HealthState/available
    Level              : Info
    DisplayStatus      : Host available
Sku                    : 
  Name                 : DSv3-Type1
Id                     : /subscriptions/10101010-1010-1010-1010-101010101010/re
sourceGroups/myDHResourceGroup/providers/Microsoft.Compute/hostGroups/myHostGroup/hosts
/myHost
Name                   : myHost
Location               : eastus
Tags                   : {}
```

## <a name="create-a-scale-set"></a>スケール セットを作成する 

スケール セットをデプロイするときは、ホスト グループを指定します。

```azurepowershell-interactive
New-AzVmss `
  -ResourceGroupName "myResourceGroup" `
  -Location "EastUS" `
  -VMScaleSetName "myDHScaleSet" `
  -VirtualNetworkName "myVnet" `
  -SubnetName "mySubnet" `
  -PublicIpAddressName "myPublicIPAddress" `
  -LoadBalancerName "myLoadBalancer" `
  -UpgradePolicyMode "Automatic"`
  -HostGroupId $hostGroup.Id
```

スケール セットをデプロイするホストを手動で選択する場合は、`--host` とそのホストの名前を追加します。



## <a name="add-an-existing-vm"></a>既存の VM を追加する 

既存の VM を専用のホストに追加することはできますが、最初に VM を Stop\Deallocated とする必要があります。 VM を専用のホストに移動する前に、その VM 構成がサポートされていることを確認してください。

- VM サイズは、専用ホストと同じサイズ ファミリである必要があります。 たとえば、専用のホストが DSv3 の場合、VM のサイズは Standard_D4s_v3 になる可能性がありますが、Standard_A4_v2 となる可能性はありません。 
- VM は、専用ホストと同じリージョンに配置する必要があります。
- VM は、近接通信配置グループの一部になることはありません。 専用のホストに移動する前に、近接通信配置グループから VM を削除してください。 詳細については、「[近接配置グループからの VM の移動](./proximity-placement-groups.md#move-an-existing-vm-out-of-a-proximity-placement-group)」を参照してください
- VM は、可用性セット内に置くことはできません。
- VM が可用性ゾーン内にある場合は、ホスト グループと同じ可用性ゾーンである必要があります。 VM とホスト グループの可用性ゾーンの設定が一致している必要があります。

変数の値を実際の情報に置き換えます。

```azurepowershell-interactive
$vmRGName = "movetohost"
$vmName = "myVMtoHost"
$dhRGName = "myDHResourceGroup"
$dhGroupName = "myHostGroup"
$dhName = "myHost"

$myDH = Get-AzHost `
   -HostGroupName $dhGroupName `
   -ResourceGroupName $dhRGName `
   -Name $dhName
   
$myVM = Get-AzVM `
   -ResourceGroupName $vmRGName `
   -Name $vmName
   
$myVM.Host = New-Object Microsoft.Azure.Management.Compute.Models.SubResource

$myVM.Host.Id = "$myDH.Id"

Stop-AzVM `
   -ResourceGroupName $vmRGName `
   -Name $vmName -Force
   
Update-AzVM `
   -ResourceGroupName $vmRGName `
   -VM $myVM -Debug
   
Start-AzVM `
   -ResourceGroupName $vmRGName `
   -Name $vmName
```


## <a name="clean-up"></a>クリーンアップ

仮想マシンがデプロイされていない場合でも、専用ホストに対して課金されます。 コストを節約するには、現在使用していないすべてのホストを削除する必要があります。  

ホストを削除できるのは、それを使用している仮想マシンがなくなった場合のみです。 [Remove-AzVM](/powershell/module/az.compute/remove-azvm) を使用して VM を削除します。

```azurepowershell-interactive
Remove-AzVM -ResourceGroupName $rgName -Name myVM
```

VM を削除した後、[Remove-AzHost](/powershell/module/az.compute/remove-azhost) を使用してホストを削除できます。

```azurepowershell-interactive
Remove-AzHost -ResourceGroupName $rgName -Name myHost
```

すべてのホストを削除したら、[Remove-AzHostGroup](/powershell/module/az.compute/remove-azhostgroup) を使用してホスト グループを削除できます。 

```azurepowershell-interactive
Remove-AzHost -ResourceGroupName $rgName -Name myHost
```

また、1 つのコマンド [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) を使用して、リソース グループ全体を削除することもできます。 これにより、すべての VM、ホスト、ホスト グループを含めて、グループ内に作成されたすべてのリソースが削除されます。
 
```azurepowershell-interactive
Remove-AzResourceGroup -Name $rgName
```


## <a name="next-steps"></a>次のステップ

- [こちら](https://github.com/Azure/azure-quickstart-templates/blob/master/201-vm-dedicated-hosts/README.md)には、リージョン内の回復性を最大にするためにゾーンと障害ドメインの両方を使用するサンプル テンプレートがあります。

- また、[Azure portal](../dedicated-hosts-portal.md) を使用して専用ホストをデプロイすることもできます。
