---
title: Azure PowerShell での Windows トラブルシューティング VM の使用 | Microsoft Docs
description: Azure PowerShell で OS ディスクを復旧 VM に接続して、Azure で Windows VM の問題をトラブルシュートする方法を説明します。
services: virtual-machines-windows
documentationCenter: ''
author: genlin
manager: gwallace
editor: ''
ms.service: virtual-machines-windows
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 08/09/2018
ms.author: genli
ms.openlocfilehash: 265a4f58fc383519246d8d69d6cf7407b92e4728
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/28/2019
ms.locfileid: "70089784"
---
# <a name="troubleshoot-a-windows-vm-by-attaching-the-os-disk-to-a-recovery-vm-using-azure-powershell"></a>Azure PowerShell で OS ディスクを復旧 VM に接続して Windows VM のトラブルシューティングを行う
Azure の Windows 仮想マシン (VM) で起動エラーまたはディスク エラーが発生した場合は、ディスク自体にトラブルシューティング手順を実行する必要がある可能性があります。 一般的な例として、VM の正常な起動を妨げる失敗したアプリケーション更新が挙げられます。 この記事では、Azure PowerShell を使用してディスクを別の Windows VM に接続してエラーを修正した後、元の VM を修復する方法について詳しく説明します。 

> [!Important]
> この記事のスクリプトは、[マネージド ディスク](../windows/managed-disks-overview.md)を使用している VM にのみ適用されます。 

[!INCLUDE [updated-for-az.md](../../../includes/updated-for-az.md)]

## <a name="recovery-process-overview"></a>回復プロセスの概要
Azure PowerShell を使用して、VM の OS ディスクを変更できるようになりました。 VM を削除して再作成する必要はもうありません。

トラブルシューティングのプロセスは次のとおりです。

1. 影響を受けている VM を停止します。
2. VM の OS ディスクからスナップショットを作成します。
3. OS ディスクのスナップショットからディスクを作成します。
4. ディスクをデータ ディスクとして復旧 VM にアタッチします。
5. 復旧 VM に接続します。 ファイルを編集するか任意のツールを実行して、コピー元の OS ディスクの問題を解決します。
6. 復旧 VM からディスクのマウントを解除してデタッチします。
7. 影響を受けている VM の OS ディスクを変更します。

手順 1、2、3、4、6、7 は、VM 復旧スクリプトを使用して自動化することができます。 詳しいドキュメントと手順については、[Resource Manager VM の復旧スクリプト](https://github.com/Azure/azure-support-scripts/tree/master/VMRecovery/ResourceManager)を参照してください。

[最新の Azure PowerShell](/powershell/azure/overview) がインストールされ、サブスクリプションにログインしていることを確認します。

```powershell
Connect-AzAccount
```

以下の例では、パラメーター名を独自の値に置き換えてください。 

## <a name="determine-boot-issues"></a>起動の問題を特定する
起動の問題のトラブルシューティングに役立つ VM のスクリーンショットを Azure で表示できます。 このスクリーンショットは、VM が起動に失敗した理由の特定に役立ちます。 次の例では、`myResourceGroup` という名前のリソース グループの `myVM` という名前の Windows VM からスクリーンショットを取得します。

```powershell
Get-AzVMBootDiagnosticsData -ResourceGroupName myResourceGroup `
    -Name myVM -Windows -LocalPath C:\Users\ops\
```

スクリーンショットを調べて、VM が起動できない理由を特定します。 特定のエラー メッセージまたはエラー コードが表示されていないかどうか確認してください。

## <a name="stop-the-vm"></a>VM を停止する

次の例では、`myResourceGroup` という名前のリソース グループから `myVM` という名前の VM を停止します。

```powershell
Stop-AzVM -ResourceGroupName "myResourceGroup" -Name "myVM"
```

VMの削除が完了するまで待機した後で次の手順に進んでください。


## <a name="create-a-snapshot-from-the-os-disk-of-the-vm"></a>VM の OS ディスクからスナップショットを作成する

次の例は、'myVM' という名前の VM の OS ディスクから `mySnapshot` という 名前のスナップショットを作成します。 

```powershell
$resourceGroupName = 'myResourceGroup' 
$location = 'eastus' 
$vmName = 'myVM'
$snapshotName = 'mySnapshot'  

#Get the VM
$vm = get-azvm `
-ResourceGroupName $resourceGroupName `
-Name $vmName

#Create the snapshot configuration for the OS disk
$snapshot =  New-AzSnapshotConfig `
-SourceUri $vm.StorageProfile.OsDisk.ManagedDisk.Id `
-Location $location `
-CreateOption copy

#Take the snapshot
New-AzSnapshot `
   -Snapshot $snapshot `
   -SnapshotName $snapshotName `
   -ResourceGroupName $resourceGroupName 
```

スナップショットは、VHD の完全な読み取り専用コピーです。 これを VM にアタッチすることはできません。 次の手順で、このスナップショットからディスクを作成します。

## <a name="create-a-disk-from-the-snapshot"></a>スナップショットからディスクを作成する

このスクリプトは、`mysnapshot` という名前のスナップショットから `newOSDisk` という名前のマネージド ディスクを作成します。  

```powershell
#Set the context to the subscription Id where Managed Disk will be created
#You can skip this step if the subscription is already selected

$subscriptionId = 'yourSubscriptionId'

Select-AzSubscription -SubscriptionId $SubscriptionId

#Provide the name of your resource group
$resourceGroupName ='myResourceGroup'

#Provide the name of the snapshot that will be used to create Managed Disks
$snapshotName = 'mySnapshot' 

#Provide the name of the Managed Disk
$diskName = 'newOSDisk'

#Provide the size of the disks in GB. It should be greater than the VHD file size.
$diskSize = '128'

#Provide the storage type for Managed Disk. Premium_LRS or Standard_LRS.
$storageType = 'Standard_LRS'

#Provide the Azure region (e.g. westus) where Managed Disks will be located.
#This location should be same as the snapshot location
#Get all the Azure location using command below:
#Get-AzLocation
$location = 'eastus'

$snapshot = Get-AzSnapshot -ResourceGroupName $resourceGroupName -SnapshotName $snapshotName 
 
$diskConfig = New-AzDiskConfig -AccountType $storageType -Location $location -CreateOption Copy -SourceResourceId $snapshot.Id
 
New-AzDisk -Disk $diskConfig -ResourceGroupName $resourceGroupName -DiskName $diskName
```
これで、元の OS ディスクのコピーが用意できました。 トラブルシューティングのために、このディスクを別の Windows VM にマウントできます。

## <a name="attach-the-disk-to-another-windows-vm-for-troubleshooting"></a>トラブルシューティングのためにディスクを別の Windows VM にアタッチする

次に、元の OS ディスクのコピーを、データ ディスクとして VM にアタッチします。 このプロセスにより、構成エラーを修正したり、ディスク内の他のアプリケーションやシステムのログ ファイルをレビューしたりできます。 次の例では、`newOSDisk` という名前のディスクを `RecoveryVM` という名前の VM にアタッチします。

> [!NOTE]
> ディスクをアタッチするには、元の OS ディスクのコピーと復旧 VM が同じ場所に存在する必要があります。

```powershell
$rgName = "myResourceGroup"
$vmName = "RecoveryVM"
$location = "eastus" 
$dataDiskName = "newOSDisk"
$disk = Get-AzDisk -ResourceGroupName $rgName -DiskName $dataDiskName 

$vm = Get-AzVM -Name $vmName -ResourceGroupName $rgName 

$vm = Add-AzVMDataDisk -CreateOption Attach -Lun 0 -VM $vm -ManagedDiskId $disk.Id

Update-AzVM -VM $vm -ResourceGroupName $rgName
```

## <a name="connect-to-the-recovery-vm-and-fix-issues-on-the-attached-disk"></a>復旧 VM に接続してアタッチされたディスクの問題を修正する

1. 適切な資格情報を使用して、復旧 VM に RDP 接続します。 次の例では、`myResourceGroup` という名前のリソース グループの `RecoveryVM` という名前の VM の RDP 接続ファイルを `C:\Users\ops\Documents` にダウンロードします。

    ```powershell
    Get-AzRemoteDesktopFile -ResourceGroupName "myResourceGroup" -Name "RecoveryVM" `
        -LocalPath "C:\Users\ops\Documents\myVMRecovery.rdp"
    ```

2. データ ディスクが自動的に検出されてアタッチされます。 接続されたボリュームの一覧を表示して、次のようなドライブ文字を特定します。

    ```powershell
    Get-Disk
    ```

    次の出力例は、ディスク **2** に接続されたディスクを示しています。 (`Get-Volume` を使用して、ドライブ文字を表示することもできます):

    ```powershell
    Number   Friendly Name   Serial Number   HealthStatus   OperationalStatus   Total Size   Partition
                                                                                             Style
    ------   -------------   -------------   ------------   -----------------   ----------   ----------
    0        Virtual HD                                     Healthy             Online       127 GB MBR
    1        Virtual HD                                     Healthy             Online       50 GB MBR
    2        newOSDisk                                  Healthy             Online       127 GB MBR
    ```

元の OS ディスクのコピーがマウントされた後で、必要に応じてメンテナンスやトラブルシューティングの手順を実行できます。 問題に対処したら、次の手順に進みます。

## <a name="unmount-and-detach-original-os-disk"></a>元の OS ディスクのマウントを解除してデタッチする
エラーが解決したら、既存のディスクのマウントを解除して復旧 VM からデタッチします。 復旧 VM へのディクスのアタッチを解放するまで、ディスクを他の VM で使用することはできません。

1. RDP セッション内から、復旧 VM でデータ ディスクのマウントを解除します。 前の `Get-Disk` コマンドレットで取得したディスク番号が必要です。 次に、`Set-Disk` を使用して、ディスクをオフラインとして設定します。

    ```powershell
    Set-Disk -Number 2 -IsOffline $True
    ```

    `Get-Disk` をもう一度使用して、ディスクがオフラインとして設定されていることを確認します。 次の出力例は、ディスクがオフラインとして設定されていることを示しています。

    ```powershell
    Number   Friendly Name   Serial Number   HealthStatus   OperationalStatus   Total Size   Partition
                                                                                             Style
    ------   -------------   -------------   ------------   -----------------   ----------   ----------
    0        Virtual HD                                     Healthy             Online       127 GB MBR
    1        Virtual HD                                     Healthy             Online       50 GB MBR
    2        Msft Virtu...                                  Healthy             Offline      127 GB MBR
    ```

2. RDP セッションを終了します。 Azure PowerShell セッションで、'RecoveryVM' という名前の VM から `newOSDisk` という名前のディスクを削除します。

    ```powershell
    $myVM = Get-AzVM -ResourceGroupName "myResourceGroup" -Name "RecoveryVM"
    Remove-AzVMDataDisk -VM $myVM -Name "newOSDisk"
    Update-AzVM -ResourceGroup "myResourceGroup" -VM $myVM
    ```

## <a name="change-the-os-disk-for-the-affected-vm"></a>影響を受けている VM の OS ディスクを変更する

Azure PowerShell を使用して、OS ディスクをスワップできます。 VM を削除して再作成する必要はありません。

この例では、`myVM` という名前の VM を停止し、`newOSDisk` という名前のディスクを新しい OS ディスクとして割り当てます。 

```powershell
# Get the VM 
$vm = Get-AzVM -ResourceGroupName myResourceGroup -Name myVM 

# Make sure the VM is stopped\deallocated
Stop-AzVM -ResourceGroupName myResourceGroup -Name $vm.Name -Force

# Get the new disk that you want to swap in
$disk = Get-AzDisk -ResourceGroupName myResourceGroup -Name newDisk

# Set the VM configuration to point to the new disk  
Set-AzVMOSDisk -VM $vm -ManagedDiskId $disk.Id -Name $disk.Name  -sto

# Update the VM with the new OS disk. Possible values of StorageAccountType include: 'Standard_LRS' and 'Premium_LRS'
Update-AzVM -ResourceGroupName myResourceGroup -VM $vm -StorageAccountType <Type of the storage account >

# Start the VM
Start-AzVM -Name $vm.Name -ResourceGroupName myResourceGroup
```

## <a name="verify-and-enable-boot-diagnostics"></a>ブート診断を確認して有効にする

次の例では、`myResourceGroup` という名前のリソース グループの `myVMDeployed` という名前の VM で診断拡張機能を有効にします。

```powershell
$myVM = Get-AzVM -ResourceGroupName "myResourceGroup" -Name "myVMDeployed"
Set-AzVMBootDiagnostics -ResourceGroupName myResourceGroup -VM $myVM -enable
Update-AzVM -ResourceGroup "myResourceGroup" -VM $myVM
```

## <a name="next-steps"></a>次の手順
VM への接続の問題が発生した場合は、[Azure VM への RDP 接続のトラブルシューティング](troubleshoot-rdp-connection.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)に関する記事をご覧ください。 VM で実行されているアプリケーションへのアクセスに関する問題については、[Windows VM でのアプリケーションの接続の問題のトラブルシューティング](troubleshoot-app-connection.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)に関する記事をご覧ください。

Resource Manager の使用方法の詳細については、「[Azure Resource Manager の概要](../../azure-resource-manager/resource-group-overview.md)」をご覧ください。
