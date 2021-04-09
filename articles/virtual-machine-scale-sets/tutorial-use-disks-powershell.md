---
title: チュートリアル - Azure PowerShell を使用してスケール セットのディスクを作成および使用する
description: Azure PowerShell を使用して仮想マシン スケール セットの管理ディスクを作成および使用する方法 (ディスクの追加、準備、一覧表示、切断方法など) を説明します。
author: ju-shim
ms.author: jushiman
ms.topic: tutorial
ms.service: virtual-machine-scale-sets
ms.subservice: disks
ms.date: 03/27/2018
ms.reviewer: mimckitt
ms.custom: mimckitt, devx-track-azurepowershell
ms.openlocfilehash: 9e995e88b80bf14f9c7784f465bcd3d89d0bed65
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "92367960"
---
# <a name="tutorial-create-and-use-disks-with-virtual-machine-scale-set-with-azure-powershell"></a>チュートリアル: Azure PowerShell を使用した仮想マシン スケール セットのディスクの作成および使用

仮想マシン スケール セットでは、VM インスタンスのオペレーティング システム、アプリケーション、およびデータを格納するためにディスクを使用します。 スケール セットを作成および管理するときは、予測されるワークロードに適したディスクのサイズと構成を選択する必要があります。 このチュートリアルでは、VM ディスクの作成方法と管理方法について説明します。 このチュートリアルで学習する内容は次のとおりです。

> [!div class="checklist"]
> * OS ディスクと一時ディスク
> * データ ディスク
> * Standard ディスクと Premium ディスク
> * ディスクのパフォーマンス
> * データ ディスクの接続および準備

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。

[!INCLUDE [updated-for-az.md](../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]


## <a name="default-azure-disks"></a>既定の Azure ディスク
スケール セットが作成またはスケーリングされると、2 つのディスクが各 VM インスタンスに自動的に接続されます。 

**オペレーティング システム ディスク** - オペレーティング システム ディスクは、最大 2 TB までサイズを変更でき、VM インスタンスのオペレーティング システムをホストします。 OS ディスクには既定で */dev/sda* というラベルが付けられています。 OS ディスクのディスク キャッシュ構成は、OS パフォーマンスの向上のために最適化されています。 この構成では、OS ディスクでアプリケーションやデータをホスト **しないでください**。 アプリケーションとデータには、この記事の後半で説明するデータ ディスクを使用してください。 

**一時ディスク** - 一時ディスクは、VM インスタンスと同じ Azure ホストに配置されているソリッド ステート ドライブを使用します。 これらは高パフォーマンスのディスクであり、一時的なデータ処理などの操作に使用される場合があります。 ただし、VM インスタンスを新しいホストに移動すると、一時ディスクに格納されているデータはすべて削除されます。 一時ディスクのサイズは VM インスタンスのサイズによって決まります。 一時ディスクには */dev/sdb* のラベルが付けられており、 */mnt* というマウント ポイントがあります。

### <a name="temporary-disk-sizes"></a>一時ディスクのサイズ
| Type | 一般的なサイズ | 一時ディスクの最大サイズ (GiB) |
|----|----|----|
| [汎用](../virtual-machines/sizes-general.md) | A、B、D シリーズ | 1600 |
| [コンピューティングの最適化](../virtual-machines/sizes-compute.md) | F シリーズ | 576 |
| [メモリの最適化](../virtual-machines/sizes-memory.md) | D、E、G、M シリーズ | 6144 |
| [ストレージの最適化](../virtual-machines/sizes-storage.md) | L シリーズ | 5630 |
| [GPU](../virtual-machines/sizes-gpu.md) | N シリーズ | 1440 |
| [高性能](../virtual-machines/sizes-hpc.md) | A および H シリーズ | 2000 |


## <a name="azure-data-disks"></a>Azure データ ディスク
アプリケーションをインストールしたりデータを保存したりする必要がある場合は、データ ディスクをさらに追加できます。 耐久性と応答性の高いデータ ストレージが望ましい状況では、必ず、データ ディスクを使用する必要があります。 各データ ディスクの最大容量は 4 TB です。 VM インスタンス サイズによって、接続できるデータ ディスクの数が決まります。 各 VM vCPU に、2 つのデータ ディスクを接続できます。

### <a name="max-data-disks-per-vm"></a>VM あたりの最大データ ディスク数
| 種類 | 一般的なサイズ | VM あたりの最大データ ディスク数 |
|----|----|----|
| [汎用](../virtual-machines/sizes-general.md) | A、B、D シリーズ | 64 |
| [コンピューティングの最適化](../virtual-machines/sizes-compute.md) | F シリーズ | 64 |
| [メモリの最適化](../virtual-machines/sizes-memory.md) | D、E、G、M シリーズ | 64 |
| [ストレージの最適化](../virtual-machines/sizes-storage.md) | L シリーズ | 64 |
| [GPU](../virtual-machines/sizes-gpu.md) | N シリーズ | 64 |
| [高性能](../virtual-machines/sizes-hpc.md) | A および H シリーズ | 64 |


## <a name="vm-disk-types"></a>VM ディスクの種類
Azure では、2 種類のディスクを提供しています。

### <a name="standard-disk"></a>Standard ディスク
Standard Storage では、HDD が使用されており、コスト効率とパフォーマンスに優れたストレージを提供します。 Standard ディスクは、コスト効率が重視される、開発およびテストのワークロードに最適です。

### <a name="premium-disk"></a>Premium ディスク
Premium ディスクは、SSD ベースの高パフォーマンスで待機時間の短いディスクによってサポートされています。 これらのディスクは、運用ワークロードを実行している VM に適しています。 Premium Storage は、DS シリーズ、DSv2 シリーズ、GS シリーズ、FS シリーズの VM をサポートしています。 ディスク サイズを選択するときは、値を切り上げて 1 つ上の種類にします。 たとえば、ディスク サイズが 128 GB 未満の場合、ディスクの種類は P10 です。 ディスク サイズが 129 ～ 512 GB の場合、サイズは P20 です。 512 GB を超えた場合、サイズは P30 です。

### <a name="premium-disk-performance"></a>Premium ディスクのパフォーマンス
|Premium Storage ディスクの種類 | P4 | P6 | P10 | P20 | P30 | P40 | P50 |
| --- | --- | --- | --- | --- | --- | --- | --- |
| ディスク サイズ (切り上げ) | 32 GB | 64 GB | 128 GB | 512 GB | 1,024 GB (1 TB) | 2,048 GB (2 TB) | 4,095 GB (4 TB) |
| ディスクあたりの最大 IOPS | 120 | 240 | 500 | 2,300 | 5,000 | 7,500 | 7,500 |
ディスクあたりのスループット | 25 MB/秒 | 50 MB/秒 | 100 MB/秒 | 150 MB/秒 | 200 MB/s | 250 MB/秒 | 250 MB/秒 |

上記の表は、ディスクあたりの最大 IOPS を割り出していますが、複数のデータ ディスクをストライピングすることによって、より高いレベルのパフォーマンスを実現できます。 たとえば、Standard_GS5 VM では、最大 80,000 IOPS を実現できます。 VM あたりの最大 IOPS の詳細については、[ のサイズ](../virtual-machines/sizes.md)に関するページを参照してください。


## <a name="create-and-attach-disks"></a>ディスクを作成して接続する
ディスクは、スケール セットの作成時に作成および接続できます。また、既存のスケール セットに対してディスクを作成および接続することもできます。

API バージョン `2019-07-01` 時点では、[storageProfile.osDisk.diskSizeGb](/rest/api/compute/virtualmachinescalesets/createorupdate#virtualmachinescalesetosdisk) プロパティを使用して、仮想マシン スケール セットの OS ディスクのサイズを設定できます。 プロビジョニング後、領域全体を活用するために、ディスクの拡張またはパーティション再分割を行う必要がある場合があります。 ディスクの拡張の詳細については、[こちら](../virtual-machines/windows/expand-os-disk.md#expand-the-volume-within-the-os)を参照してください。

### <a name="attach-disks-at-scale-set-creation"></a>スケール セットの作成時にディスクを接続する
[New-AzVmss](/powershell/module/az.compute/new-azvmss) を使用して仮想マシン スケール セットを作成します。 メッセージが表示されたら、VM インスタンスのユーザー名とパスワードを入力します。 個々の VM インスタンスにトラフィックを分散するために、ロード バランサーも作成されます。 ロード バランサーには、TCP ポート 80 上のトラフィックを分散するルールだけでなく、TCP ポート 3389 上のリモート デスクトップ トラフィックと TCP ポート 5985 上の PowerShell リモート処理を許可するルールも含まれています。

`-DataDiskSizeGb` パラメーターを使用して 2 つのディスクを作成します。 1 つ目のディスクのサイズは *64* GB、2 つ目のディスクは *128* GB です。 メッセージが表示されたら、スケール セット内の VM インスタンス用の自分の管理者資格情報を指定します。

```azurepowershell-interactive
New-AzVmss `
  -ResourceGroupName "myResourceGroup" `
  -Location "EastUS" `
  -VMScaleSetName "myScaleSet" `
  -VirtualNetworkName "myVnet" `
  -SubnetName "mySubnet" `
  -PublicIpAddressName "myPublicIPAddress" `
  -LoadBalancerName "myLoadBalancer" `
  -UpgradePolicyMode "Automatic" `
  -DataDiskSizeInGb 64,128
```

すべてのスケール セット リソースと VM インスタンスを作成して構成するには数分かかります。

### <a name="attach-a-disk-to-existing-scale-set"></a>既存のスケール セットにディスクを接続する
既存のスケール セットにディスクを接続することもできます。 前の手順で作成したスケール セットを使用して別のディスクを追加するには、[Add-AzVmssDataDisk](/powershell/module/az.compute/add-azvmssdatadisk) を使用します。 次の例では、*128* GB の追加ディスクが既存のスケール セットに接続されます。

```azurepowershell-interactive
# Get scale set object
$vmss = Get-AzVmss `
          -ResourceGroupName "myResourceGroup" `
          -VMScaleSetName "myScaleSet"

# Attach a 128 GB data disk to LUN 2
Add-AzVmssDataDisk `
  -VirtualMachineScaleSet $vmss `
  -CreateOption Empty `
  -Lun 2 `
  -DiskSizeGB 128

# Update the scale set to apply the change
Update-AzVmss `
  -ResourceGroupName "myResourceGroup" `
  -Name "myScaleSet" `
  -VirtualMachineScaleSet $vmss
```


## <a name="prepare-the-data-disks"></a>データ ディスクの準備
作成されてスケール セット VM インスタンスに接続されたディスクは、未フォーマット ディスクです。 これらのディスクをデータおよびアプリケーション用に使用するには、ディスクを準備する必要があります。 ディスクを準備するには、パーティションを作成し、ファイルシステムを作成した後、マウントします。

スケール セット内の複数の VM インスタンスにわたってこのプロセスを自動化するには、Azure カスタム スクリプト拡張機能を使用できます。 この拡張機能を使用すると、各 VM インスタンス上でローカルにスクリプトを実行して、接続されたデータ ディスクの準備などの操作を実行できます。 詳細については、「[Windows のカスタム スクリプト拡張機能](../virtual-machines/extensions/custom-script-windows.md)」を参照してください。


次の例では、すべての未フォーマットの接続されたデータ ディスクを準備する [Add-AzVmssExtension](/powershell/module/az.compute/Add-AzVmssExtension) を使用して、GitHub サンプル リポジトリにあるスクリプトを各 VM インスタンス上で実行します。


```azurepowershell-interactive
# Get scale set object
$vmss = Get-AzVmss `
          -ResourceGroupName "myResourceGroup" `
          -VMScaleSetName "myScaleSet"

# Define the script for your Custom Script Extension to run
$publicSettings = @{
  "fileUris" = (,"https://raw.githubusercontent.com/Azure-Samples/compute-automation-configurations/master/prepare_vm_disks.ps1");
  "commandToExecute" = "powershell -ExecutionPolicy Unrestricted -File prepare_vm_disks.ps1"
}

# Use Custom Script Extension to prepare the attached data disks
Add-AzVmssExtension -VirtualMachineScaleSet $vmss `
  -Name "customScript" `
  -Publisher "Microsoft.Compute" `
  -Type "CustomScriptExtension" `
  -TypeHandlerVersion 1.8 `
  -Setting $publicSettings

# Update the scale set and apply the Custom Script Extension to the VM instances
Update-AzVmss `
  -ResourceGroupName "myResourceGroup" `
  -Name "myScaleSet" `
  -VirtualMachineScaleSet $vmss
```

ディスクが正常に準備されたことを確認するには、VM インスタンスの 1 つに RDP 接続します。 

まず、[Get-AzLoadBalancer](/powershell/module/az.network/Get-AzLoadBalancer) を使用して、ロード バランサー オブジェクトを取得します。 次に、[Get-AzLoadBalancerInboundNatRuleConfig](/powershell/module/az.network/Get-AzLoadBalancerInboundNatRuleConfig) を使用して、インバウンド NAT 規則を表示します。 この NAT 規則により、RDP がリッスンする各 VM インスタンスの *FrontendPort* が一覧表示されます。 最後に、[Get-AzPublicIpAddress](/powershell/module/az.network/Get-AzPublicIpAddress) を使用して、ロード バランサーのパブリック IP アドレスを取得します。


```azurepowershell-interactive
# Get the load balancer object
$lb = Get-AzLoadBalancer -ResourceGroupName "myResourceGroup" -Name "myLoadBalancer"

# View the list of inbound NAT rules
Get-AzLoadBalancerInboundNatRuleConfig -LoadBalancer $lb | Select-Object Name,Protocol,FrontEndPort,BackEndPort

# View the public IP address of the load balancer
Get-AzPublicIpAddress -ResourceGroupName "myResourceGroup" -Name myPublicIPAddress | Select IpAddress
```

VM に接続するには、必要な VM インスタンスの独自のパブリック IP アドレスとポート番号を、前のコマンドで示されたとおりに指定します。 メッセージが表示されたら、スケール セットの作成時に使用した資格情報を入力します。 Azure Cloud Shell を使用する場合は、ローカルの PowerShell プロンプトまたはリモート デスクトップ クライアントからこの手順を実行します。 次の例では、VM インスタンス *1* に接続します。

```powershell
mstsc /v 52.168.121.216:50001
```

VM インスタンスでローカルの PowerShell セッションを開き、[Get-Disk](/powershell/module/storage/get-disk) を使用して接続されているディスクを確認します。

```powershell
Get-Disk
```

次の出力例は、3 つのデータ ディスクが VM インスタンスに接続されていることを示しています。

```powershell
Number  Friendly Name      HealthStatus  OperationalStatus  Total Size  Partition Style
------  -------------      ------------  -----------------  ----------  ---------------
0       Virtual HD         Healthy       Online                 127 GB  MBR
1       Virtual HD         Healthy       Online                  14 GB  MBR
2       Msft Virtual Disk  Healthy       Online                  64 GB  MBR
3       Msft Virtual Disk  Healthy       Online                 128 GB  MBR
4       Msft Virtual Disk  Healthy       Online                 128 GB  MBR
```

次のように、VM インスタンス上のファイルシステムとマウント ポイントを確認します。

```powershell
Get-Partition
```

次の出力例は、3 つのデータ ディスクにドライブ文字が割り当てられていることを示しています。

```powershell
   DiskPath: \\?\scsi#disk&ven_msft&prod_virtual_disk#000001

PartitionNumber  DriveLetter  Offset   Size  Type
---------------  -----------  ------   ----  ----
1                F            1048576  64 GB  IFS

   DiskPath: \\?\scsi#disk&ven_msft&prod_virtual_disk#000002

PartitionNumber  DriveLetter  Offset   Size   Type
---------------  -----------  ------   ----   ----
1                G            1048576  128 GB  IFS

   DiskPath: \\?\scsi#disk&ven_msft&prod_virtual_disk#000003

PartitionNumber  DriveLetter  Offset   Size   Type
---------------  -----------  ------   ----   ----
1                H            1048576  128 GB  IFS
```

スケール内の各 VM インスタンスのディスクは、同じように自動的に準備されます。 スケール セットがスケールアップされると、必要なデータ ディスクが新しい VM インスタンスに接続されます。 さらに、ディスクを準備するためにカスタム スクリプト拡張機能も自動的に実行されます。

VM インスタンスとのリモート デスクトップ接続セッションを閉じます。


## <a name="list-attached-disks"></a>接続されているディスクの一覧表示
スケール セットに接続されているディスクに関する情報を表示するには、次のように [Get-AzVmss](/powershell/module/az.compute/get-azvmss) を使用します。

```azurepowershell-interactive
Get-AzVmss -ResourceGroupName "myResourceGroup" -Name "myScaleSet"
```

*VirtualMachineProfile.StorageProfile* プロパティの下に、*DataDisks* の一覧が表示されます。 ディスク サイズ、ストレージ層、および LUN (論理ユニット番号) に関する情報が表示されます。 次の出力例では、スケール セットに接続されている 3 つのデータ ディスクの詳細を出力します。

```powershell
DataDisks[0]                            :
  Lun                                   : 0
  Caching                               : None
  CreateOption                          : Empty
  DiskSizeGB                            : 64
  ManagedDisk                           :
    StorageAccountType                  : PremiumLRS
DataDisks[1]                            :
  Lun                                   : 1
  Caching                               : None
  CreateOption                          : Empty
  DiskSizeGB                            : 128
  ManagedDisk                           :
    StorageAccountType                  : PremiumLRS
DataDisks[2]                            :
  Lun                                   : 2
  Caching                               : None
  CreateOption                          : Empty
  DiskSizeGB                            : 128
  ManagedDisk                           :
    StorageAccountType                  : PremiumLRS
```


## <a name="detach-a-disk"></a>ディスクを取り外す
特定のディスクが不要になったら、スケール セットから切断することができます。 ディスクは、スケール セット内のすべての VM インスタンスから削除されます。 スケール セットからディスクを切断するには、[Remove-AzVmssDataDisk](/powershell/module/az.compute/remove-azvmssdatadisk) を使用し、ディスクの LUN を指定します。 LUN は、前のセクションの [Get-AzVmss](/powershell/module/az.compute/get-azvmss) の出力に表示されています。 次の例では、スケール セットから LUN *3* を切断します。

```azurepowershell-interactive
# Get scale set object
$vmss = Get-AzVmss `
          -ResourceGroupName "myResourceGroup" `
          -VMScaleSetName "myScaleSet"

# Detach a disk from the scale set
Remove-AzVmssDataDisk `
  -VirtualMachineScaleSet $vmss `
  -Lun 2

# Update the scale set and detach the disk from the VM instances
Update-AzVmss `
  -ResourceGroupName "myResourceGroup" `
  -Name "myScaleSet" `
  -VirtualMachineScaleSet $vmss
```


## <a name="clean-up-resources"></a>リソースをクリーンアップする
スケール セットとディスクを削除するには、[Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) を使用して、リソース グループとそのすべてのリソースを削除します。 `-Force` パラメーターは、追加のプロンプトを表示せずにリソースの削除を確定します。 `-AsJob` パラメーターは、操作の完了を待たずにプロンプトに制御を戻します。

```azurepowershell-interactive
Remove-AzResourceGroup -Name "myResourceGroup" -Force -AsJob
```


## <a name="next-steps"></a>次のステップ
このチュートリアルでは、Azure PowerShell を使用してスケール セットのディスクを作成して使用する方法について学習しました。

> [!div class="checklist"]
> * OS ディスクと一時ディスク
> * データ ディスク
> * Standard ディスクと Premium ディスク
> * ディスクのパフォーマンス
> * データ ディスクの接続および準備

次のチュートリアルに進み、スケール セットの VM インスタンスにカスタム イメージを使用する方法を学習してください。

> [!div class="nextstepaction"]
> [スケール セットの VM インスタンスにカスタム イメージを使用する](tutorial-use-custom-image-powershell.md)