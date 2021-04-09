---
title: Azure マネージド ディスクに対して共有ディスクを有効にする
description: 複数の VM 間で共有できるように、共有ディスクを使用して Azure マネージド ディスクを構成する
author: roygara
ms.service: virtual-machines
ms.topic: how-to
ms.date: 09/30/2020
ms.author: rogarana
ms.subservice: disks
ms.custom: references_regions, devx-track-azurecli
ms.openlocfilehash: e8bb97196fcceea0c86f58fe4a63146e681c427e
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "96500735"
---
# <a name="enable-shared-disk"></a>共有ディスクを有効にする

この記事では、Azure マネージド ディスクに対して共有ディスク機能を有効にする方法について説明します。 Azure 共有ディスクは、マネージド ディスクを複数の仮想マシン (VM) に同時に接続できるようにする Azure マネージド ディスクの新機能です。 マネージド ディスクを複数の VM に接続すると、新規にデプロイするか、既存のクラスター化されたアプリケーションを Azure に移行することができます。 

共有ディスクが有効になっているマネージド ディスクの概念的な情報については、[Azure 共有ディスク](disks-shared.md)に関する記事を参照してください。

## <a name="limitations"></a>制限事項

[!INCLUDE [virtual-machines-disks-shared-limitations](../../includes/virtual-machines-disks-shared-limitations.md)]

## <a name="supported-operating-systems"></a>サポートされるオペレーティング システム

共有ディスクは、複数のオペレーティング システムでサポートされます。 サポートされているオペレーティング システムについては、概念に関する記事の [Windows](./disks-shared.md#windows) と [Linux](./disks-shared.md#linux) のセクションを参照してください。

## <a name="disk-sizes"></a>ディスク サイズ

[!INCLUDE [virtual-machines-disks-shared-sizes](../../includes/virtual-machines-disks-shared-sizes.md)]

## <a name="deploy-shared-disks"></a>共有ディスクのデプロイ

### <a name="deploy-a-premium-ssd-as-a-shared-disk"></a>Premium SSD を共有ディスクとしてデプロイする

共有ディスク機能が有効になっているマネージド ディスクをデプロイするには、新しいプロパティ `maxShares` を使用し、1 より大きい値を定義します。 これにより、複数の VM 間でディスクを共有できるようになります。

> [!IMPORTANT]
> `maxShares` の値は、ディスクがすべての VM からマウント解除されている場合にのみ設定または変更できます。 `maxShares`に使用できる値については、[ディスクのサイズ](#disk-sizes) を参照してください。

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
az disk create -g myResourceGroup -n mySharedDisk --size-gb 1024 -l westcentralus --sku Premium_LRS --max-shares 2
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
$dataDiskConfig = New-AzDiskConfig -Location 'WestCentralUS' -DiskSizeGB 1024 -AccountType Premium_LRS -CreateOption Empty -MaxSharesCount 2

New-AzDisk -ResourceGroupName 'myResourceGroup' -DiskName 'mySharedDisk' -Disk $dataDiskConfig
```

# <a name="resource-manager-template"></a>[Resource Manager テンプレート](#tab/azure-resource-manager)

次のテンプレートを使用する前に、`[parameters('dataDiskName')]`、`[resourceGroup().location]`、`[parameters('dataDiskSizeGB')]`、および `[parameters('maxShares')]` を実際の値に置き換えてください。

[Premium SSD 共有ディスク テンプレート](https://aka.ms/SharedPremiumDiskARMtemplate)

---

### <a name="deploy-an-ultra-disk-as-a-shared-disk"></a>Ultra ディスクを共有ディスクとしてデプロイする

共有ディスク機能が有効になっているマネージド ディスクをデプロイするには、`maxShares` パラメーターを 1 より大きい値に変更します。 これにより、複数の VM 間でディスクを共有できるようになります。

> [!IMPORTANT]
> `maxShares` の値は、ディスクがすべての VM からマウント解除されている場合にのみ設定または変更できます。 `maxShares`に使用できる値については、[ディスクのサイズ](#disk-sizes) を参照してください。


# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

##### <a name="regional-disk-example"></a>リージョン ディスクの例

```azurecli
#Creating an Ultra shared Disk 
az disk create -g rg1 -n clidisk --size-gb 1024 -l westus --sku UltraSSD_LRS --max-shares 5 --disk-iops-read-write 2000 --disk-mbps-read-write 200 --disk-iops-read-only 100 --disk-mbps-read-only 1

#Updating an Ultra shared Disk 
az disk update -g rg1 -n clidisk --disk-iops-read-write 3000 --disk-mbps-read-write 300 --set diskIopsReadOnly=100 --set diskMbpsReadOnly=1

#Show shared disk properties:
az disk show -g rg1 -n clidisk
```

##### <a name="zonal-disk-example"></a>ゾーン ディスクの例

この例は、前のものとほぼ同じですが、可用性ゾーン 1 にディスクを作成する点が異なります。

```azurecli
#Creating an Ultra shared Disk 
az disk create -g rg1 -n clidisk --size-gb 1024 -l westus --sku UltraSSD_LRS --max-shares 5 --disk-iops-read-write 2000 --disk-mbps-read-write 200 --disk-iops-read-only 100 --disk-mbps-read-only 1 --zone 1

#Updating an Ultra shared Disk 
az disk update -g rg1 -n clidisk --disk-iops-read-write 3000 --disk-mbps-read-write 300 --set diskIopsReadOnly=100 --set diskMbpsReadOnly=1

#Show shared disk properties:
az disk show -g rg1 -n clidisk
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

##### <a name="regional-disk-example"></a>リージョン ディスクの例

```azurepowershell-interactive
$datadiskconfig = New-AzDiskConfig -Location 'WestCentralUS' -DiskSizeGB 1024 -AccountType UltraSSD_LRS -CreateOption Empty -DiskIOPSReadWrite 2000 -DiskMBpsReadWrite 200 -DiskIOPSReadOnly 100 -DiskMBpsReadOnly 1 -MaxSharesCount 5

New-AzDisk -ResourceGroupName 'myResourceGroup' -DiskName 'mySharedDisk' -Disk $datadiskconfig
```

##### <a name="zonal-disk-example"></a>ゾーン ディスクの例

この例は、前のものとほぼ同じですが、可用性ゾーン 1 にディスクを作成する点が異なります。

```azurepowershell-interactive
$datadiskconfig = New-AzDiskConfig -Location 'WestCentralUS' -DiskSizeGB 1024 -AccountType UltraSSD_LRS -CreateOption Empty -DiskIOPSReadWrite 2000 -DiskMBpsReadWrite 200 -DiskIOPSReadOnly 100 -DiskMBpsReadOnly 1 -MaxSharesCount 5 -Zone 1

New-AzDisk -ResourceGroupName 'myResourceGroup' -DiskName 'mySharedDisk' -Disk $datadiskconfig
```

# <a name="resource-manager-template"></a>[Resource Manager テンプレート](#tab/azure-resource-manager)

##### <a name="regional-disk-example"></a>リージョン ディスクの例

次のテンプレートを使用する前に、`[parameters('dataDiskName')]`、`[resourceGroup().location]`、`[parameters('dataDiskSizeGB')]`、`[parameters('maxShares')]`、`[parameters('diskIOPSReadWrite')]`、`[parameters('diskMBpsReadWrite')]`、`[parameters('diskIOPSReadOnly')]`、`[parameters('diskMBpsReadOnly')]` を実際の値に置き換えてください。

[リージョン共有 Ultra ディスク テンプレート](https://aka.ms/SharedUltraDiskARMtemplateRegional)

##### <a name="zonal-disk-example"></a>ゾーン ディスクの例

次のテンプレートを使用する前に、`[parameters('dataDiskName')]`、`[resourceGroup().location]`、`[parameters('dataDiskSizeGB')]`、`[parameters('maxShares')]`、`[parameters('diskIOPSReadWrite')]`、`[parameters('diskMBpsReadWrite')]`、`[parameters('diskIOPSReadOnly')]`、`[parameters('diskMBpsReadOnly')]` を実際の値に置き換えてください。

[ゾーン共有 Ultra ディスク テンプレート](https://aka.ms/SharedUltraDiskARMtemplateZonal)

---

## <a name="using-azure-shared-disks-with-your-vms"></a>VM での Azure 共有ディスクの使用

`maxShares>1` を使用して共有ディスクをデプロイしたら、そのディスクを1つ以上の VM にマウントできます。

> [!NOTE]
> Ultra ディスクをデプロイする場合は、必要な要件を満たしていることを確認してください。 詳細については、「[Azure Ultra ディスクの使用](disks-enable-ultra-ssd.md)」を参照してください。

```azurepowershell-interactive

$resourceGroup = "myResourceGroup"
$location = "WestCentralUS"

$vm = New-AzVm -ResourceGroupName $resourceGroup -Name "myVM" -Location $location -VirtualNetworkName "myVnet" -SubnetName "mySubnet" -SecurityGroupName "myNetworkSecurityGroup" -PublicIpAddressName "myPublicIpAddress"

$dataDisk = Get-AzDisk -ResourceGroupName $resourceGroup -DiskName "mySharedDisk"

$vm = Add-AzVMDataDisk -VM $vm -Name "mySharedDisk" -CreateOption Attach -ManagedDiskId $dataDisk.Id -Lun 0

update-AzVm -VM $vm -ResourceGroupName $resourceGroup
```

## <a name="supported-scsi-pr-commands"></a>サポートされている SCSI PR コマンド

クラスター内の VM に共有ディスクをマウントしたら、SCSI PR を使用して、クォーラムを確立し、ディスクに読み取り/書き込みを行うことができます。 Azure 共有ディスクを使用する場合は、次の PR コマンドを使用できます。

ディスクを操作するには、次のようにして、「永続的な予約アクション」リストから開始します。

```
PR_REGISTER_KEY 

PR_REGISTER_AND_IGNORE 

PR_GET_CONFIGURATION 

PR_RESERVE 

PR_PREEMPT_RESERVATION 

PR_CLEAR_RESERVATION 

PR_RELEASE_RESERVATION 
```

PR_RESERVE、PR_PREEMPT_RESERVATION、または PR_RELEASE_RESERVATION を使用する場合は、次のいずれかの永続的な予約の種類を指定します。

```
PR_NONE 

PR_WRITE_EXCLUSIVE 

PR_EXCLUSIVE_ACCESS 

PR_WRITE_EXCLUSIVE_REGISTRANTS_ONLY 

PR_EXCLUSIVE_ACCESS_REGISTRANTS_ONLY 

PR_WRITE_EXCLUSIVE_ALL_REGISTRANTS 

PR_EXCLUSIVE_ACCESS_ALL_REGISTRANTS 
```

PR_RESERVE、PR_REGISTER_AND_IGNORE、PR_REGISTER_KEY、PR_PREEMPT_RESERVATION、PR_CLEAR_RESERVATION、または PR_RELEASE-RESERVATION を使用する場合は、永続的な予約キーを指定する必要もあります。


## <a name="next-steps"></a>次のステップ

Azure Resource Manager テンプレートを使用してディスクをデプロイすることを希望する場合は、次のサンプル テンプレートを使用できます。
- [Premium SSD](https://aka.ms/SharedPremiumDiskARMtemplate)
- [リージョン Ultra ディスク](https://aka.ms/SharedUltraDiskARMtemplateRegional)
- [ゾーン Ultra ディスク](https://aka.ms/SharedUltraDiskARMtemplateZonal)