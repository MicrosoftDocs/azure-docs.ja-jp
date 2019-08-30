---
title: チュートリアル - Azure PowerShell を使用して Azure ディスクを管理する | Microsoft Docs
description: このチュートリアルでは、Azure PowerShell を使用して、仮想マシン用の Azure ディスクの作成と管理を行う方法について説明します
services: virtual-machines-windows
documentationcenter: virtual-machines
author: cynthn
manager: gwallace
editor: tysonn
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.topic: tutorial
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 11/29/2018
ms.author: cynthn
ms.custom: mvc
ms.subservice: disks
ms.openlocfilehash: f44ea76ed22ab4feacc6ee37b165bbc33f7ddafc
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/28/2019
ms.locfileid: "70101594"
---
# <a name="tutorial---manage-azure-disks-with-azure-powershell"></a>チュートリアル - Azure PowerShell を使用して Azure ディスクを管理する

Azure Virtual Machines では、VM のオペレーティング システム、アプリケーション、およびデータを格納するためにディスクを使用します。 VM を作成するときは、予測されるワークロードに適したディスクのサイズと構成を選択することが重要です。 このチュートリアルでは、VM ディスクのデプロイと管理について説明します。 内容は次のとおりです。

> [!div class="checklist"]
> * OS ディスクと一時ディスク
> * データ ディスク
> * Standard ディスクと Premium ディスク
> * ディスクのパフォーマンス
> * データ ディスクの接続と準備

## <a name="launch-azure-cloud-shell"></a>Azure Cloud Shell を起動する

Azure Cloud Shell は無料のインタラクティブ シェルです。この記事の手順は、Azure Cloud Shell を使って実行することができます。 一般的な Azure ツールが事前にインストールされており、アカウントで使用できるように構成されています。 

Cloud Shell を開くには、コード ブロックの右上隅にある **[使ってみる]** を選択します。 [https://shell.azure.com/powershell](https://shell.azure.com/powershell) に移動して、別のブラウザー タブで Cloud Shell を起動することもできます。 **[コピー]** を選択してコードのブロックをコピーし、Cloud Shell に貼り付けてから、Enter キーを押して実行します。

## <a name="default-azure-disks"></a>既定の Azure ディスク

Azure 仮想マシンを作成すると、2 つのディスクが仮想マシンに自動的に接続されます。 

**オペレーティング システム ディスク** - オペレーティング システム ディスクは、最大 4 TB までサイズを変更でき、VM のオペレーティング システムをホストします。  OS ディスクには、既定でドライブ文字 *C:* が割り当てられます。 OS ディスクのディスク キャッシュ構成は、OS パフォーマンスの向上のために最適化されています。 OS ディスクでアプリケーションやデータをホスト**しないでください**。 アプリケーションとデータには、この記事の後半で説明するデータ ディスクを使用してください。

**一時ディスク** - 一時ディスクは、VM と同じ Azure ホストに配置されているソリッド ステート ドライブを使用します。 一時ディスクは、パフォーマンスが高く、一時的なデータ処理などの操作に使用される場合があります。 ただし、VM を新しいホストに移動すると、一時ディスクに格納されているデータは削除されます。 一時ディスクのサイズは [VM のサイズ](sizes.md)によって決まります。 一時ディスクには、既定でドライブ文字 *D:* が割り当てられます。

## <a name="azure-data-disks"></a>Azure データ ディスク

アプリケーションのインストールやデータの保存のために、データ ディスクをさらに追加できます。 耐久性と応答性の高いデータ ストレージが必要な状況では、必ず、データ ディスクを使用する必要があります。 仮想マシンのサイズによって、VM に接続できるデータ ディスクの数が決まります。 各 VM vCPU に、4 つのデータ ディスクを接続できます。

## <a name="vm-disk-types"></a>VM ディスクの種類

Azure では、2 種類のディスクを提供しています。

**Standard ディスク** - HDD が使用されており、高パフォーマンスでありながらコスト効率にも優れたストレージを提供します。 Standard ディスクは、コスト効率が重視される、開発およびテストのワークロードに最適です。

**Premium ディスク** - SSD ベースの高性能で待ち時間の短いディスクが使用されています。 実稼働ワークロードを実行する VM に最適です。 Premium Storage は、DS シリーズ、DSv2 シリーズ、GS シリーズ、FS シリーズの VM をサポートしています。 Premium ディスクは、5 種類 (P10、P20、P30、P40、P50) に分類され、ディスク サイズによってディスクの種類が決まります。 ディスク サイズを選択するときは、値を切り上げて 1 つ上の種類にします。 たとえば、サイズが 128 GB 未満の場合、ディスクの種類は P10 で、サイズが 129 GB から 512 GB までの場合、ディスクの種類は P20 です。

### <a name="premium-disk-performance"></a>Premium ディスクのパフォーマンス
[!INCLUDE [disk-storage-premium-ssd-sizes](../../../includes/disk-storage-premium-ssd-sizes.md)]

上記の表は、ディスクあたりの最大 IOPS を割り出していますが、複数のデータ ディスクをストライピングすることによって、より高いレベルのパフォーマンスを実現できます。 たとえば、64 のデータ ディスクを Standard_GS5 VM に接続することができます。 これらの各ディスクのサイズが P30 である場合、最大 80,000 IOPS を実現できます。 VM あたりの最大 IOPS について詳しくは、「[VM の種類とサイズ](./sizes.md)」をご覧ください。

## <a name="create-and-attach-disks"></a>ディスクを作成して接続する

このチュートリアルの例を完了するには、既存の仮想マシンが必要です。 必要に応じて、次のコマンドを使用して仮想マシンを作成します。

[Get-Credential](https://msdn.microsoft.com/powershell/reference/5.1/microsoft.powershell.security/Get-Credential) を使用して、仮想マシンの管理者アカウントに必要なユーザー名とパスワードを設定します。


[New-AzVM](https://docs.microsoft.com/powershell/module/az.compute/new-azvm) を使用して、仮想マシンを作成します。 VM の管理者アカウントのユーザー名とパスワードを入力するよう求められます。

```azurepowershell-interactive
New-AzVm `
    -ResourceGroupName "myResourceGroupDisk" `
    -Name "myVM" `
    -Location "East US" `
    -VirtualNetworkName "myVnet" `
    -SubnetName "mySubnet" `
    -SecurityGroupName "myNetworkSecurityGroup" `
    -PublicIpAddressName "myPublicIpAddress" 
```


[New-AzDiskConfig](https://docs.microsoft.com/powershell/module/az.compute/new-azdiskconfig) を使用して、初期構成を作成します。 次の例では、サイズが 128 ギガバイトのディスクを構成します。

```azurepowershell-interactive
$diskConfig = New-AzDiskConfig `
    -Location "EastUS" `
    -CreateOption Empty `
    -DiskSizeGB 128
```

[New-AzDisk](https://docs.microsoft.com/powershell/module/az.compute/new-Azdisk) コマンドを使用して、データ ディスクを作成します。

```azurepowershell-interactive
$dataDisk = New-AzDisk `
    -ResourceGroupName "myResourceGroupDisk" `
    -DiskName "myDataDisk" `
    -Disk $diskConfig
```

[Get-AzVM](https://docs.microsoft.com/powershell/module/az.compute/get-azvm) コマンドを使用して、データ ディスクを追加する仮想マシンを取得します。

```azurepowershell-interactive
$vm = Get-AzVM -ResourceGroupName "myResourceGroupDisk" -Name "myVM"
```

[Add-AzVMDataDisk](https://docs.microsoft.com/powershell/module/az.compute/add-azvmdatadisk) コマンドを使用して、データ ディスクを仮想マシンの構成に追加します。

```azurepowershell-interactive
$vm = Add-AzVMDataDisk `
    -VM $vm `
    -Name "myDataDisk" `
    -CreateOption Attach `
    -ManagedDiskId $dataDisk.Id `
    -Lun 1
```

[Update-AzVM](https://docs.microsoft.com/powershell/module/az.compute/add-azvmdatadisk) コマンドを使用して、仮想マシンを更新します。

```azurepowershell-interactive
Update-AzVM -ResourceGroupName "myResourceGroupDisk" -VM $vm
```

## <a name="prepare-data-disks"></a>データ ディスクを準備する

ディスクが仮想マシンに接続されたら、そのディスクを使用するようにオペレーティング システムを構成する必要があります。 次の例では、最初に VM に追加されたディスクを手動で構成する方法を示します。 この処理は、[カスタム スクリプト拡張機能](./tutorial-automate-vm-deployment.md)を使用して自動化することもできます。

### <a name="manual-configuration"></a>手動構成

仮想マシンとの RDP 接続を作成します。 PowerShell を開き、次のスクリプトを実行します。

```azurepowershell
Get-Disk | Where partitionstyle -eq 'raw' |
    Initialize-Disk -PartitionStyle MBR -PassThru |
    New-Partition -AssignDriveLetter -UseMaximumSize |
    Format-Volume -FileSystem NTFS -NewFileSystemLabel "myDataDisk" -Confirm:$false
```

## <a name="verify-the-data-disk"></a>データ ディスクを確認する

データ ディスクが接続されていることを確認するには、接続されている `DataDisks` の `StorageProfile` を表示します。

```azurepowershell-interactive
$vm.StorageProfile.DataDisks
```

出力は次の例のようになります。

```
Name            : myDataDisk
DiskSizeGB      : 128
Lun             : 1
Caching         : None
CreateOption    : Attach
SourceImage     :
VirtualHardDisk :
```


## <a name="next-steps"></a>次の手順

このチュートリアルでは、VM ディスクについて、次のようなトピックを学習しました。

> [!div class="checklist"]
> * OS ディスクと一時ディスク
> * データ ディスク
> * Standard ディスクと Premium ディスク
> * ディスクのパフォーマンス
> * データ ディスクの接続と準備

次のチュートリアルに進み、VM 構成を自動化する方法について学習してください。

> [!div class="nextstepaction"]
> [VM 構成の自動化](./tutorial-automate-vm-deployment.md)
