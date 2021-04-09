---
title: Azure で Windows VM の OS ドライブを展開する
description: Resource Manager デプロイ モデルで Azure PowerShell を使用して仮想マシンの OS ドライブのサイズを拡張します。
services: virtual-machines
documentationcenter: ''
author: kirpasingh
manager: roshar
editor: ''
tags: azure-resource-manager
ms.assetid: d9edfd9f-482f-4c0b-956c-0d2c2c30026c
ms.service: virtual-machines
ms.collection: windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 09/02/2020
ms.author: kirpas
ms.subservice: disks
ms.openlocfilehash: af57bc396349286c3edcaefc8385ddbaec066226
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "103199447"
---
# <a name="how-to-expand-the-os-drive-of-a-virtual-machine"></a>仮想マシンの OS ドライブを展開する方法

[Azure Marketplace](https://azure.microsoft.com/marketplace/) からイメージをデプロイしてリソース グループに新しい仮想マシン (VM) を作成するとき、多くの場合、既定の OS ドライブは 127 GB です (一部のイメージでは既定の OS ディスク サイズが小さくなります)。 VM にデータ ディスクを追加することは可能であり (数は、選択されている SKU によって決まります)、また、アプリケーションと、CPU を負荷の高いワークロードはこれらの追加ディスクにインストールすることが推奨されていますが、多くの場合、次のような特定のシナリオをサポートするために OS ドライブの拡張が必要になります。

- OS ドライブにコンポーネントをインストールするレガシ アプリケーションをサポートする場合。
- オンプレミスから OS ドライブの大きい物理 PC または VM を移行する場合。

> [!IMPORTANT]
> Azure 仮想マシンの OS またはデータ ディスクのサイズを変更するには、仮想マシンの割り当てを解除する必要があります。
>
> 既存のディスクの縮小はサポートされておらず、データ損失に至る可能性があります。
> 
> ディスクを拡張した後、大きいディスクを活用するには、[OS 内のボリュームを拡張する](#expand-the-volume-within-the-os)必要があります。

## <a name="resize-a-managed-disk-in-the-azure-portal"></a>Azure portal でマネージド ディスクのサイズを変更する

1. [Azure portal](https://portal.azure.com) で、ディスクを拡張する仮想マシンに移動します。 **[停止]** を選択して VM の割り当てを解除します。
2. VM が停止したら、左側のメニューの **[設定]** で **[ディスク]** を選択します。

    :::image type="content" source="./media/expand-os-disk/select-disks.png" alt-text="メニューの [設定] セクションで [ディスク] オプションが選択されているのを示すスクリーンショット。":::

 
3. **[ディスク名]** で、サイズを変更するディスクを選択します。

    :::image type="content" source="./media/expand-os-disk/disk-name.png" alt-text="ディスク名が選択されている [ディスク] ウィンドウを示すスクリーンショット。":::

4. 左側のメニューの **[設定]** で、 **[Size + performance]\(サイズおよびパフォーマンス\)** を選択します。

    :::image type="content" source="./media/expand-os-disk/configuration.png" alt-text="メニューの [設定] セクションで [サイズおよびパフォーマンス] オプションが選択されているのを示すスクリーンショット。":::

5. **[Size + performance]\(サイズおよびパフォーマンス\)** で、必要なディスク サイズを選択します。
   
   > [!WARNING]
   > 新しいサイズには、既存のディスク サイズより大きい値を指定する必要があります。 OS ディスクで許可される最大値は、2,048 GB です。 (VHD BLOB はこのサイズよりも大きくすることができます。ただし、OS の実行に使用できるのは、最初の 2,048 GB の領域のみです。)
   > 

    :::image type="content" source="./media/expand-os-disk/size.png" alt-text="ディスク サイズが選択された[Size + performance]\(サイズおよびパフォーマンス\) ペインを示すスクリーンショット。":::

6. ページの下部にある **[サイズ変更]** を選択します。

    :::image type="content" source="./media/expand-os-disk/save.png" alt-text="[サイズ変更] ボタンが選択された [Size + performance]\(サイズおよびパフォーマンス\) ペインを示すスクリーンショット。":::


## <a name="resize-a-managed-disk-by-using-powershell"></a>PowerShell を使用してマネージド ディスクのサイズを変更する

管理者モードで PowerShell ISE または PowerShell ウィンドウを開き、次の手順に従います。

1. リソース管理モードで Microsoft Azure アカウントにサインインし、サブスクリプションを選択します。
   
    ```powershell
    Connect-AzAccount
    Select-AzSubscription –SubscriptionName 'my-subscription-name'
    ```

2. リソース グループ名と VM 名を設定します。
   
    ```powershell
    $rgName = 'my-resource-group-name'
    $vmName = 'my-vm-name'
    ```

3. VM への参照を取得します。
   
    ```powershell
    $vm = Get-AzVM -ResourceGroupName $rgName -Name $vmName
    ```

4. ディスクのサイズを変更する前に VM を停止します。
   
    ```powershell
    Stop-AzVM -ResourceGroupName $rgName -Name $vmName
    ```

5. マネージド OS ディスクへの参照を取得します。 マネージド OS ディスクのサイズを目的の値に設定し、ディスクを更新します。
   
    ```powershell
    $disk= Get-AzDisk -ResourceGroupName $rgName -DiskName $vm.StorageProfile.OsDisk.Name
    $disk.DiskSizeGB = 1023
    Update-AzDisk -ResourceGroupName $rgName -Disk $disk -DiskName $disk.Name
    ```   
    > [!WARNING]
    > 新しいサイズには、既存のディスク サイズより大きい値を指定する必要があります。 OS ディスクで許可される最大値は、2,048 GB です。 (VHD BLOB はこのサイズよりも大きくすることができます。ただし、OS の実行に使用できるのは、最初の 2,048 GB の領域のみです。)
    > 
         
6. VM の更新には数秒かかる可能性があります。 コマンドの実行が完了したら、VM を再起動します。
   
    ```powershell
    Start-AzVM -ResourceGroupName $rgName -Name $vmName
    ```

これで終了です。 VM に RDP で接続し、[コンピューターの管理] \(または [ディスクの管理]) を開いて、新しく割り当てた領域を使用してドライブを拡張します。

## <a name="resize-an-unmanaged-disk-by-using-powershell"></a>PowerShell を使用してアンマネージド ディスクのサイズを変更する

管理者モードで PowerShell ISE または PowerShell ウィンドウを開き、次の手順に従います。

1. リソース管理モードで Microsoft Azure アカウントにサインインし、サブスクリプションを選択します。
   
    ```powershell
    Connect-AzAccount
    Select-AzSubscription –SubscriptionName 'my-subscription-name'
    ```

2. リソース グループ名と VM 名を設定します。
   
    ```powershell
    $rgName = 'my-resource-group-name'
    $vmName = 'my-vm-name'
    ```

3. VM への参照を取得します。
   
    ```powershell
    $vm = Get-AzVM -ResourceGroupName $rgName -Name $vmName
    ```

4. ディスクのサイズを変更する前に VM を停止します。
   
    ```powershell
    Stop-AzVM -ResourceGroupName $rgName -Name $vmName
    ```

5. アンマネージド OS ディスクのサイズを目的の値に設定し、VM を更新します。
   
    ```powershell
    $vm.StorageProfile.OSDisk.DiskSizeGB = 1023
    Update-AzVM -ResourceGroupName $rgName -VM $vm
    ```
   
    > [!WARNING]
    > 新しいサイズには、既存のディスク サイズより大きい値を指定する必要があります。 OS ディスクで許可される最大値は、2,048 GB です。 (VHD BLOB はこのサイズよりも大きくすることができます。ただし、OS の実行に使用できるのは、最初の 2,048 GB の領域のみです。)
    > 
    > 
   
6. VM の更新には数秒かかる可能性があります。 コマンドの実行が完了したら、VM を再起動します。
   
    ```powershell
    Start-AzVM -ResourceGroupName $rgName -Name $vmName
    ```


## <a name="scripts-for-os-disk"></a>OS ディスク用のスクリプト

参考のために、マネージド ディスクとアンマネージド ディスクの両方について完全なスクリプトを以下に示します。


**マネージド ディスク**

```powershell
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
($vm.StorageProfile.DataDisks | Where ({$_.Name -eq 'my-second-data-disk'})).DiskSizeGB = 1023
```

## <a name="expand-the-volume-within-the-os"></a>OS 内のボリュームを拡張する

VM のディスクを拡張したら、OS に移動して、新しい領域を含めるようにボリュームを拡張する必要があります。 パーティションを拡張するには、いくつかの方法があります。 このセクションには、**DiskPart** を使用してパーティションを拡張するために、RDP コネクションを使用した VM への接続が含まれています。

1. VM への RDP 接続を開きます。

2. コマンド プロンプトを開き、「**diskpart**」と入力します。

3. **DISKPART** プロンプトで「`list volume`」と入力します。 拡張するボリュームをメモします。

4. **DISKPART** プロンプトで「`select volume <volumenumber>`」と入力します。 これにより、同じディスク上の連続した空の領域に拡張する必要があるボリューム *volumenumber* が選択されます。

5. **DISKPART** プロンプトで「`extend [size=<size>]`」と入力します。 これにより、選択したボリュームがメガバイト (MB) 単位の *size* で拡張されます。


## <a name="next-steps"></a>次のステップ

また、[Azure portal](attach-managed-disk-portal.md) を使用してディスクを接続することもできます。
