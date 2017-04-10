---
title: "VM を非管理対象ディスクから管理ディスクに変換する - Azure | Microsoft Docs"
description: "Resource Manager デプロイメント モデルで PowerShell を使用して VM を非管理対象ディスクから管理ディスクに変換します"
services: virtual-machines-windows
documentationcenter: 
author: cynthn
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 02/22/2017
ms.author: cynthn
translationtype: Human Translation
ms.sourcegitcommit: 4f2230ea0cc5b3e258a1a26a39e99433b04ffe18
ms.openlocfilehash: 96137d49d3caa0444400edcfcfaa097dc71ba8e3
ms.lasthandoff: 03/25/2017


---
# <a name="convert-a-vm-from-unmanaged-disks-to-managed-disks"></a>VM を非管理対象ディスクから管理ディスクに変換する

ストレージ アカウントの非管理対象ディスクを使っている既存の Azure VM があり、その VM で [Managed Disks](../storage/storage-managed-disks-overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) を利用できるようにしたい場合は、VM を変換できます。 このプロセスでは、OS ディスクだけでなく、アタッチされたデータ ディスクも、ストレージ アカウント内の非管理対象ディスクの利用から管理ディスクの利用に変換します。 対象の VM をシャットダウンして割り当てを解除した後で、Powershell を使って、管理ディスクを利用するよう VM を変換します。 変換後、VM を再起動すると、管理ディスクが利用されます。

作業を開始する前に、「[Plan for the migration to Managed Disks (Managed Disks への移行の計画)](virtual-machines-windows-on-prem-to-azure.md#plan-for-the-migration-to-managed-disks)」をご覧ください。
移行プロセスは取り消すことができないため、運用環境で移行を実行する前にテスト仮想マシンを移行して、プロセスをテストしてください。


> [!IMPORTANT] 
> 変換中に、VM の割り当てを解除します。 VM の割り当てを解除すると、変換後に起動したときに、VM に新しい IP アドレスが設定されます。 固定 IP に対して依存関係がある場合は、予約済み IP を使用してください。


## <a name="managed-disks-and-azure-storage-service-encryption-sse"></a>Managed Disks と Azure Storage Service Encryption (SSE)

Resource Manager デプロイメント モデルで作成された非管理対象 VM は、アタッチされた非管理対象ディスクが [Azure Storage Service Encryption (SSE)](../storage/storage-service-encryption.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) で暗号化されているストレージ アカウント内、または過去に暗号化されたことのあるストレージ アカウント内にある場合は Managed Disks に変換できません。 次の手順では、暗号化されたストレージ アカウントに配置されているか配置されたことがある非管理対象ディスクを変換する方法について詳しく説明します。

**データ ディスク**:
1.    データ ディスクを仮想マシンからデタッチします。
2.    SSE が有効化されたことのないストレージ アカウントに VHD をコピーします。 ディスクを別のストレージ アカウントにコピーするには、[AzCopy](../storage/storage-use-azcopy.md) を使用します。`AzCopy /Source:https://sourceaccount.blob.core.windows.net/mycontainer1 /Dest:https://destaccount.blob.core.windows.net/mycontainer2 /SourceKey:key1 /DestKey:key2 /Pattern:myDataDisk.vhd`
3.    コピーされたディスクを VM に接続し、VM を変換します。

**OS ディスク**:
1.    割り当てが解除された VM を停止します。 必要に応じて、VM 構成を保存します。
2.    SSE が有効化されたことのないストレージ アカウントに OS VHD をコピーします。 ディスクを別のストレージ アカウントにコピーするには、[AzCopy](../storage/storage-use-azcopy.md) を使用します。`AzCopy /Source:https://sourceaccount.blob.core.windows.net/mycontainer1 /Dest:https://destaccount.blob.core.windows.net/mycontainer2 /SourceKey:key1 /DestKey:key2 /Pattern:myVhd.vhd`
3.    管理ディスクを使用する VM を作成します。作成時にその VHD ファイルを OS ディスクとしてアタッチします。

## <a name="convert-vms-in-an-availability-set-to-managed-disks-in-a-managed-availability-set"></a>可用性セット内の VM を管理対象の可用性セット内の管理ディスクに変換する

管理ディスクに変換する VM が可用性セット内にある場合は、最初に可用性セットを管理対象の可用性セットに変換する必要があります。

次のスクリプトでは、可用性セットを管理対象の可用性セットに更新した後、割り当てを解除し、ディスクを変換して、可用性セット内の各 VM を再起動します。

```powershell
$rgName = 'myResourceGroup'
$avSetName = 'myAvailabilitySet'

$avSet =  Get-AzureRmAvailabilitySet -ResourceGroupName $rgName -Name $avSetName

Update-AzureRmAvailabilitySet -AvailabilitySet $avSet -Managed

foreach($vmInfo in $avSet.VirtualMachinesReferences)
    {
   $vm =  Get-AzureRmVM -ResourceGroupName $rgName | Where-Object {$_.Id -eq $vmInfo.id}

   Stop-AzureRmVM -ResourceGroupName $rgName -Name  $vm.Name -Force

   ConvertTo-AzureRmVMManagedDisk -ResourceGroupName $rgName -VMName $vm.Name
   
    }
```

## <a name="convert-existing-azure-vms-to-managed-disks-of-the-same-storage-type"></a>既存の Azure VM を同じストレージの種類の管理ディスクに変換する

> [!IMPORTANT]
> 以下の手順を実行した後、既定の vhds コンテナーには BLOB が 1 つ残ります。 ファイルの名前は "VMName.xxxxxxx.status" です。 このファイルは、VM に [VM 拡張機能](windows/classic/agents-and-extensions.md)をインストールした場合にのみ、Azure によって作成されます。 この残っているステータス オブジェクトを削除しないでください。 この問題には今後の作業で対処されます。

このセクションでは、既存の Azure VM をストレージ アカウント内の非管理対象ディスクから管理ディスクに変換する方法 (両者が同じストレージの種類を使用している場合) について説明します。 このプロセスを使って、Premium (SSD) の非管理対象ディスクから Premium 管理ディスクに、または Standard (HDD) の非管理対象ディスクから Standard 管理ディスクに移行できます。 

1. 変数を作成し、VM の割り当てを解除します。 この例では、リソース グループの名前を **myResourceGroup** に、VM の名前を **myVM** に設定します。

    ```powershell
    $rgName = "myResourceGroup"
    $vmName = "myVM"
    Stop-AzureRmVM -ResourceGroupName $rgName -Name $vmName -Force
    ```
   
    Azure Portal で VM の *[状態]* が **[停止済み]** から **[停止済み (割り当て解除)]** に変わります。
    
2. OS ディスクやデータ ディスクなど、VM に関連付けられているすべてのディスクを変換します。

    ```powershell
    ConvertTo-AzureRmVMManagedDisk -ResourceGroupName $rgName -VMName $vmName
    ```


## <a name="migrate-existing-azure-vms-using-standard-unmanaged-disks-to-premium-managed-disks"></a>Standard の非管理対象ディスクを使用している既存の Azure VM を Premium 管理ディスクに移行する

このセクションでは、Standard の非管理対象ディスクにある既存の Azure VM を Premium 管理ディスクに変換する方法について説明します。 Premium Managed Disks を使用するには、Premium Storage に対応している [VM のサイズ](virtual-machines-windows-sizes.md)を使用している必要があります。


1.  最初に、共通のパラメーターを設定します。 選択した [VM のサイズ](virtual-machines-windows-sizes.md)が Premium Storage に対応していることを確認します。

    ```powershell
    $resourceGroupName = 'YourResourceGroupName'
    $vmName = 'YourVMName'
    $size = 'Standard_DS2_v2'
    ```
1.  非管理対象ディスクを使用している VM を取得します。

    ```powershell
    $vm = Get-AzureRmVM -Name $vmName -ResourceGroupName $resourceGroupName
    ```
    
1.  VM を停止 (割り当て解除) します。

    ```powershell
    Stop-AzureRmVM -ResourceGroupName $resourceGroupName -Name $vmName -Force
    ```

1.  VM のサイズを、VM が配置されているリージョンで利用可能な Premium Storage 対応のサイズに更新します。

    ```powershell
    $vm.HardwareProfile.VmSize = $size
    Update-AzureRmVM -VM $vm -ResourceGroupName $resourceGroupName
    ```

1.  非管理対象ディスクを使用している仮想マシンを Managed Disks に変換します。 

    内部サーバー エラーが発生した場合は、サポート チームに問い合わせる前に、2、3 回やり直してみてください。

    ```powershell
    ConvertTo-AzureRmVMManagedDisk -ResourceGroupName $resourceGroupName -VMName $vmName
    ```
1. VM を停止 (割り当て解除) します。

    ```powershell
    Stop-AzureRmVM -ResourceGroupName $resourceGroupName -Name $vmName -Force
    ```
2.  すべてのディスクを Premium Storage にアップグレードします。

    ```powershell
    $vmDisks = Get-AzureRmDisk -ResourceGroupName $resourceGroupName 
    foreach ($disk in $vmDisks) 
        {
        if($disk.OwnerId -eq $vm.Id)
            {
             $diskUpdateConfig = New-AzureRmDiskUpdateConfig –AccountType PremiumLRS
             Update-AzureRmDisk -DiskUpdate $diskUpdateConfig -ResourceGroupName $resourceGroupName `
             -DiskName $disk.Name
            }
        }
    ```
1. VM を起動します。

    ```powershell
    Start-AzureRmVM -ResourceGroupName $resourceGroupName -Name $vmName
    ```
    
Standard Storage を使用するディスクと Premium Storage を使用するディスクは混在させることができます。
    

## <a name="next-steps"></a>次のステップ

[スナップショット](virtual-machines-windows-snapshot-copy-managed-disk.md)を使用して、VM の読み取り専用コピーを取得します。


