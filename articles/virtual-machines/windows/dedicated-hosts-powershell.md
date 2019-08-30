---
title: Azure PowerShell を使用して Azure 専用ホストをデプロイする | Microsoft Docs
description: Azure PowerShell を使用して専用ホストに VM をデプロイします。
services: virtual-machines-windows
author: cynthn
manager: gwallace
editor: tysonn
tags: azure-resource-manager
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 08/01/2019
ms.author: cynthn
ms.openlocfilehash: 2482fb4ab74a3c1e032a32890c3dc2c3920b5e6b
ms.sourcegitcommit: 85b3973b104111f536dc5eccf8026749084d8789
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/01/2019
ms.locfileid: "68725621"
---
# <a name="preview-deploy-vms-to-dedicated-hosts-using-the-azure-powershell"></a>更新:Azure PowerShell を使用して専用ホストに VM をデプロイする

この記事では、仮想マシン (VM) をホストするための Azure [専用ホスト](dedicated-hosts.md)を作成する方法について説明します。 

Azure PowerShell バージョン 2.4.2 以降がインストールされ、`Connect-AzAccount` を使用して Azure アカウントにサインインしていることを確認します。 バージョン2.4.2 をインストールするには、PowerShell プロンプトを開き、次のように入力します。

```powershell
Install-Module -Name Az.Compute -Repository PSGallery -RequiredVersion 2.4.2-preview -AllowPrelease
```

PowerShell でプレビュー モジュールの機能を有効にするには、PowerShellGet モジュールのバージョン 1.6.0 以降が必要です。 PowerShell Core の最新バージョンにはこの機能が自動的に組み込まれていますが、前のバージョンの PowerShell では、次のコマンドを実行して最新バージョンに更新することができます。

```powershell
Install-Module -Name PowerShellGet -Repository PSGallery -Force
```


> [!IMPORTANT]
> 現在、専用ホストはパブリック プレビュー段階にあります。
> このプレビュー バージョンはサービス レベル アグリーメントなしで提供されています。運用環境のワークロードに使用することはお勧めできません。 特定の機能はサポート対象ではなく、機能が制限されることがあります。 詳しくは、[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)に関するページをご覧ください。
>
> **プレビューに関する既知の制限事項**
> - 仮想マシン スケール セットは、現在、専用ホストではサポートされていません。
> - プレビューの初期リリースでは、次の VM シリーズがサポートされています: DSv3 と ESv3。 



## <a name="create-a-host-group"></a>ホスト グループを作成する

**ホスト グループ**は、専用ホストのコレクションを表すリソースです。 リージョンと可用性ゾーンにホスト グループを作成し、それにホストを追加します。 高可用性を計画する場合は、追加のオプションがあります。 専用ホストでは、次のいずれかまたは両方のオプションを使用できます。 
- 複数の可用性ゾーンにまたがります。 この場合は、使用する各ゾーンにホスト グループを用意する必要があります。
- 物理ラックにマップされる複数の障害ドメインにまたがります。 
 
どちらの場合も、ホスト グループに対して障害ドメイン数を指定する必要があります。 グループ内で障害ドメインをまたがりたくない場合は、障害ドメインの数を 1 にします。 

可用性ゾーンと障害ドメインの両方を使用することもできます。 この例では、ゾーン 1 の 1 つのホスト グループと 2 つの障害ドメインを作成します。 


```powershell
$rgName = "myDHResourceGroup"
$location = "East US"

New-AzResourceGroup -Location $location -Name $rgName
$hostGroup = New-AzHostGroup `
   -Location $location `
   -Name myHostGroup `
   -PlatformFaultDomain 2 `
   -ResourceGroupName $rgName `
   -Zone 1
```

## <a name="create-a-host"></a>ホストを作成する

次に、ホスト グループに専用ホストを作成してみましょう。 ホストの名前に加えて、ホストの SKU を指定する必要があります。 ホスト SKU では、専用ホストに対してサポートされている VM シリーズとハードウェアの世代がキャプチャされます。  プレビュー期間中は、次のホスト SKU 値がサポートされます: DSv3_Type1 と ESv3_Type1。


ホスト SKU の詳細と価格については、「[Azure 専用ホストの価格](https://aka.ms/ADHPricing)」を参照してください。

ホスト グループの障害ドメイン数を設定した場合は、ホストの障害ドメインを指定するように求められます。 この例では、ホストの障害ドメインを 1 に設定します。


```powershell
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


```powershell
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

```
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

## <a name="clean-up"></a>クリーンアップ

仮想マシンがデプロイされていない場合でも、専用ホストに対して課金されます。 コストを節約するには、現在使用していないすべてのホストを削除する必要があります。  

ホストを削除できるのは、それを使用している仮想マシンがなくなった場合のみです。 [Remove-AzVM](/powershell/module/az.compute/remove-azvm) を使用して VM を削除します。

```powershell
Remove-AzVM -ResourceGroupName $rgName -Name myVM
```

VM を削除した後、[Remove-AzHost](/powershell/module/az.compute/remove-azhost) を使用してホストを削除できます。

```powershell
Remove-AzHost -ResourceGroupName $rgName -Name myHost
```

すべてのホストを削除したら、[Remove-AzHostGroup](/powershell/module/az.compute/remove-azhostgroup) を使用してホスト グループを削除できます。 

```powershell
Remove-AzHost -ResourceGroupName $rgName -Name myHost
```

また、1 つのコマンド [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) を使用して、リソース グループ全体を削除することもできます。 これにより、すべての VM、ホスト、ホスト グループを含めて、グループ内に作成されたすべてのリソースが削除されます。
 
```azurepowershell-interactive
Remove-AzResourceGroup -Name $rgName
```


## <a name="next-steps"></a>次の手順

- [こちら](https://github.com/Azure/azure-quickstart-templates/blob/master/201-vm-dedicated-hosts/README.md)には、リージョン内の回復性を最大にするためにゾーンと障害ドメインの両方を使用するサンプル テンプレートがあります。

- また、[Azure portal](dedicated-hosts-portal.md) を使用して専用ホストをデプロイすることもできます。
