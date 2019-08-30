---
title: Azure で Windows VM の OS ドライブを展開する | Microsoft Docs
description: Resource Manager デプロイ モデルでの Azure PowerShell を使用した仮想マシンの OS ドライブ サイズの展開
services: virtual-machines-windows
documentationcenter: ''
author: kirpasingh
manager: roshar
editor: ''
tags: azure-resource-manager
ms.assetid: d9edfd9f-482f-4c0b-956c-0d2c2c30026c
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 07/05/2018
ms.author: kirpas
ms.subservice: disks
ms.openlocfilehash: b22507796a9e614da780d25795bb7edf7094e935
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/28/2019
ms.locfileid: "70103199"
---
# <a name="how-to-expand-the-os-drive-of-a-virtual-machine"></a>仮想マシンの OS ドライブを展開する方法

[Azure Marketplace](https://azure.microsoft.com/marketplace/) からイメージをデプロイすることによってリソース グループに新しい仮想マシン (VM) を作成するとき、多くの場合、既定の OS ドライブは 127 GB です (一部のイメージでは既定の OS ディスク サイズが小さくなります)。 VM にデータ ディスクを追加でき (数は選択されている SKU に依存)、アプリケーションおよび CPU 多用ワークロードはこれらの追加ディスクにインストールすることが推奨されますが、次のような特定のシナリオをサポートするために OS ドライブの拡張が必要になることがあります。

- OS ドライブにコンポーネントをインストールするレガシ アプリケーションをサポートする場合。
- オンプレミスから OS ドライブの大きい物理 PC または仮想マシンを移行する場合。


> [!IMPORTANT]
> Azure Virtual Machine の OS ディスクのサイズを変更すると、Azure Virtual Machine が再起動します。
>
> ディスクを拡張した後、大きいディスクを活用するには、[OS 内のボリュームを拡張する](#expand-the-volume-within-the-os)必要があります。
> 


[!INCLUDE [updated-for-az.md](../../../includes/updated-for-az.md)]


## <a name="resize-a-managed-disk"></a>マネージド ディスクのサイズを変更する

管理者モードで Powershell ISE または Powershell ウィンドウを開き、次の手順に従います。

1. リソース管理モードで Microsoft Azure アカウントにサインインし、次のようにサブスクリプションを選択します。
   
   ```powershell
   Connect-AzAccount
   Select-AzSubscription –SubscriptionName 'my-subscription-name'
   ```
2. リソース グループ名と VM 名を次のように設定します。
   
   ```powershell
   $rgName = 'my-resource-group-name'
   $vmName = 'my-vm-name'
   ```
3. 次のように、VM への参照を取得します。
   
   ```powershell
   $vm = Get-AzVM -ResourceGroupName $rgName -Name $vmName
   ```
4. 次のように、ディスクのサイズを変更する前に VM を停止します。
   
    ```Powershell
    Stop-AzVM -ResourceGroupName $rgName -Name $vmName
    ```
5. マネージド OS ディスクへの参照を取得します。 マネージド OS ディスクのサイズを目的の値に設定し、次のようにディスクを更新します。
   
   ```Powershell
   $disk= Get-AzDisk -ResourceGroupName $rgName -DiskName $vm.StorageProfile.OsDisk.Name
   $disk.DiskSizeGB = 1023
   Update-AzDisk -ResourceGroupName $rgName -Disk $disk -DiskName $disk.Name
   ```   
   > [!WARNING]
   > 新しいサイズには、既存のディスク サイズより大きい値を指定する必要があります。 OS ディスクで許可される最大値は、2,048 GB です (VHD BLOB はこのサイズよりも大きくすることができます。ただし、OS の動作に使用できるのは、最初の 2,048 GB の領域のみです)。
   > 
   > 
6. VM の更新には数秒かかる可能性があります。 コマンドの実行が完了した後、VM を再起動します。
   
   ```Powershell
   Start-AzVM -ResourceGroupName $rgName -Name $vmName
   ```

これで終了です。 VM に RDP で接続し、[コンピューターの管理] \(または [ディスクの管理]) を開いて、新しく割り当てた領域を使用してドライブを拡張します。

## <a name="resize-an-unmanaged-disk"></a>アンマネージド ディスクのサイズを変更する

管理者モードで Powershell ISE または Powershell ウィンドウを開き、次の手順に従います。

1. リソース管理モードで Microsoft Azure アカウントにサインインし、次のようにサブスクリプションを選択します。
   
   ```Powershell
   Connect-AzAccount
   Select-AzSubscription –SubscriptionName 'my-subscription-name'
   ```
2. リソース グループ名と VM 名を次のように設定します。
   
   ```Powershell
   $rgName = 'my-resource-group-name'
   $vmName = 'my-vm-name'
   ```
3. 次のように、VM への参照を取得します。
   
   ```Powershell
   $vm = Get-AzVM -ResourceGroupName $rgName -Name $vmName
   ```
4. 次のように、ディスクのサイズを変更する前に VM を停止します。
   
    ```Powershell
    Stop-AzVM -ResourceGroupName $rgName -Name $vmName
    ```
5. アンマネージド OS ディスクのサイズを目的の値に設定し、次のように VM を更新します。
   
   ```Powershell
   $vm.StorageProfile.OSDisk.DiskSizeGB = 1023
   Update-AzVM -ResourceGroupName $rgName -VM $vm
   ```
   
   > [!WARNING]
   > 新しいサイズには、既存のディスク サイズより大きい値を指定する必要があります。 OS ディスクで許可される最大値は、2,048 GB です (VHD BLOB はこのサイズよりも大きくすることができます。ただし、OS の動作に使用できるのは、最初の 2,048 GB の領域のみです)。
   > 
   > 
   
6. VM の更新には数秒かかる可能性があります。 コマンドの実行が完了した後、VM を再起動します。
   
   ```Powershell
   Start-AzVM -ResourceGroupName $rgName -Name $vmName
   ```


## <a name="scripts-for-os-disk"></a>OS ディスク用のスクリプト

参考のために、マネージド ディスクとアンマネージド ディスクの両方について完全なスクリプトを以下に示します。


**マネージド ディスク**

```Powershell
Connect-AzAccount
Select-AzSubscription -SubscriptionName 'my-subscription-name'
$rgName = 'my-resource-group-name'
$vmName = 'my-vm-name'
$vm = Get-AzVM -ResourceGroupName $rgName -Name $vmName
Stop-AzVM -ResourceGroupName $rgName -Name $vmName
$disk= Get-AzDisk -ResourceGroupName $rgName -DiskName $vm.StorageProfile.OsDisk.Name
$disk.DiskSizeGB = 1023
Update-AzDisk -ResourceGroupName $rgName -Disk $disk -DiskName $disk.Name
Start-AzVM -ResourceGroupName $rgName -Name $vmName
```

**非管理対象ディスク**

```powershell
Connect-AzAccount
Select-AzSubscription -SubscriptionName 'my-subscription-name'
$rgName = 'my-resource-group-name'
$vmName = 'my-vm-name'
$vm = Get-AzVM -ResourceGroupName $rgName -Name $vmName
Stop-AzVM -ResourceGroupName $rgName -Name $vmName
$vm.StorageProfile.OSDisk.DiskSizeGB = 1023
Update-AzVM -ResourceGroupName $rgName -VM $vm
Start-AzVM -ResourceGroupName $rgName -Name $vmName
```

## <a name="resizing-data-disks"></a>データ ディスクのサイズを変更する

この記事では VM の OS ディスクの拡張に主に重点を置きましたが、スクリプトは VM に接続されたデータ ディスクの拡張にも使用できます。 たとえば、VM に接続されている最初のデータ ディスクを拡張するには、`StorageProfile` の `OSDisk` オブジェクトを `DataDisks` 配列に置き換え、数値インデックスを使用して接続されている最初のデータ ディスクへの参照を取得します。次に例を示します。

**マネージド ディスク**

```powershell
$disk= Get-AzDisk -ResourceGroupName $rgName -DiskName $vm.StorageProfile.DataDisks[0].Name
$disk.DiskSizeGB = 1023
```


**アンマネージド ディスク**

```powershell
$vm.StorageProfile.DataDisks[0].DiskSizeGB = 1023
```



同様に、上記のようにインデックスまたはディスクの **Name** プロパティのいずれかを使用して、VM に接続されている他のデータ ディスクを参照できます。


**マネージド ディスク**

```powershell
(Get-AzDisk -ResourceGroupName $rgName -DiskName ($vm.StorageProfile.DataDisks | Where ({$_.Name -eq 'my-second-data-disk'})).Name).DiskSizeGB = 1023
```

**アンマネージド ディスク**

```powershell
($vm.StorageProfile.DataDisks | Where ({$_.Name -eq 'my-second-data-disk'}).DiskSizeGB = 1023
```

## <a name="expand-the-volume-within-the-os"></a>OS 内のボリュームを拡張する

VM 用にディスクを拡張したら、OS に移動して、新しいスペースを包含するようにボリュームを拡張する必要があります。 パーティションを拡張するには、いくつかの方法があります。 このセクションには、**DiskPart** を使用してパーティションを拡張するために、RDP コネクションを使用した VM への接続が含まれています。

1. VM への RDP 接続を開きます。

2.  コマンド プロンプトを開き、「**diskpart**」と入力します。

2.  **DISKPART** プロンプトで「`list volume`」と入力します。 拡張するボリュームをメモします。

3.  **DISKPART** プロンプトで「`select volume <volumenumber>`」と入力します。 これにより、同じディスク上の連続した空の領域に拡張する必要があるボリューム *volumenumber* が選択されます。

4.  **DISKPART** プロンプトで「`extend [size=<size>]`」と入力します。 これにより、選択したボリュームがメガバイト (MB) 単位の *size* で拡張されます。


## <a name="next-steps"></a>次の手順

また、[Azure portal](attach-managed-disk-portal.md) を使用してディスクを接続することもできます。
