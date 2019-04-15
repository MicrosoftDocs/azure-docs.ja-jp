---
title: Azure で管理対象イメージを作成する | Microsoft Docs
description: Azure で一般化された VM または VHD の管理対象イメージを作成します。 イメージを使って、マネージド ディスクを使う複数の VM を作成することができます。
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 09/27/2018
ms.author: cynthn
ms.openlocfilehash: aa1858a27d4df413deb562391251a523c28673ad
ms.sourcegitcommit: 43b85f28abcacf30c59ae64725eecaa3b7eb561a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/09/2019
ms.locfileid: "59359168"
---
# <a name="create-a-managed-image-of-a-generalized-vm-in-azure"></a>Azure で一般化された VM の管理対象イメージを作成する

ストレージ アカウントにマネージド ディスクまたは非管理対象ディスクとして格納されている一般化された VM から管理対象イメージ リソースを作成できます。 イメージは複数の VM の作成に使用できます。 マネージド イメージの課金方法については、「[Managed Disks の価格](https://azure.microsoft.com/pricing/details/managed-disks/)」をご覧ください。 

[!INCLUDE [updated-for-az-vm.md](../../../includes/updated-for-az-vm.md)]

## <a name="generalize-the-windows-vm-using-sysprep"></a>Sysprep を使用して Windows VM を一般化する

Sysprep はすべての個人アカウント情報とセキュリティ情報を削除して、マシンをイメージとして使用できるように準備します。 Sysprep については、[Sysprep の概要](https://docs.microsoft.com/windows-hardware/manufacture/desktop/sysprep--system-preparation--overview)を参照してください。

コンピューター上で実行されるサーバー ロールが Sysprep でサポートされていることを確認します。 詳しくは、「[Sysprep Support for Server Roles (サーバー ロールの sysprep サポート)](https://docs.microsoft.com/windows-hardware/manufacture/desktop/sysprep-support-for-server-roles)」を参照してください。

> [!IMPORTANT]
> VM で Sysprep を実行すると、その VM は*一般化されている*と見なされ、再起動できなくなります。 VM の一般化プロセスは元に戻せません。 元の VM の機能を保持する場合は、[VM のコピー](create-vm-specialized.md#option-3-copy-an-existing-azure-vm)を作成し、そのコピーを一般化してください。 
>
> 初めて VHD を Azure に仮想ハード ディスク (VHD) にアップロードする前に Sysprep を実行する予定の場合は、[VM の準備](prepare-for-upload-vhd-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)ができていることを確認してください。  
> 
> 

Windows VM を一般化するには、次の手順に従います。

1. Windows VM にサインインします。
   
2. 管理者としてコマンド プロンプト ウィンドウを開きます。 ディレクトリを `sysprep.exe`%windir%\system32\sysprep に変更し、を実行します。
   
3. **[システム準備ツール]** ダイアログ ボックスで **[システムの OOBE (Out-of-Box Experience) に入る]** を選択し、**[一般化する]** チェック ボックスを選択します。
   
4. **[シャットダウン オプション]** の **[シャットダウン]** を選択します。
   
5. **[OK]** を選択します。
   
    ![Sysprep の開始](./media/upload-generalized-managed/sysprepgeneral.png)

6. Sysprep は完了時に VM をシャットダウンします。 VM は再起動しないでください。


## <a name="create-a-managed-image-in-the-portal"></a>ポータルで管理対象イメージを作成する 

1. [Azure Portal](https://portal.azure.com)を開きます。

2. 左側のメニューで **[仮想マシン]** を選択し､一覧から VM を選択します。

3. VM の **[仮想マシン]** ページの上部のメニューで、**[キャプチャ]** を選択します。

   **[イメージの作成]** ページが表示されます。

4. **[名前]** で、あらかじめ設定されている名前を受け入れるか、イメージに使用する名前を入力します。

5. **[リソース グループ]** で、**[新規作成]** を選択して名前を入力するか、または **[Use existing\(既存の値を使用する\)]** を選択してドロップダウン リストから使うリソース グループを選びます。

6. イメージが作成された後にソース VM を削除する場合、**[イメージの作成後、この仮想マシンを自動的に削除します]** を選択します。

7. いずれかの[可用性ゾーン](../../availability-zones/az-overview.md)でイメージを使用する機能が必要な場合、**[Zone resiliency (ゾーンの回復性)]** に **[オン]** を選択します。

8. **[作成]** を選択してイメージを作成します。

9. 作成したイメージは、リソース グループのリソースの一覧に **[イメージ]** リソースとして表示されます。



## <a name="create-an-image-of-a-vm-using-powershell"></a>Powershell を使って VM のイメージを作成する

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

VM からイメージを直接作成すると、OS ディスクやすべてのデータ ディスクなど、VM に関連付けられているすべてのディスクが、イメージに確実に含まれます。 この例では、マネージド ディスクを使用する VM から管理対象イメージを作成する方法を示します。

始める前に、Azure PowerShell モジュールの最新バージョンがあることを確認してください。 バージョンを確認するには、PowerShell で `Get-Module -ListAvailable Az` を実行してください。 アップグレードが必要な場合は、「[PowerShellGet を使用した Windows への Azure PowerShell のインストール](/powershell/azure/install-az-ps)」を参照してください。 PowerShell をローカルで実行している場合、`Connect-AzAccount` を実行して Azure との接続を作成します。


> [!NOTE]
> イメージをゾーン冗長ストレージに格納する場合は、[可用性ゾーン](../../availability-zones/az-overview.md)をサポートするリージョンにストレージを作成し、イメージの構成に `-ZoneResilient` パラメーターを含める必要があります (`New-AzImageConfig` コマンド)。

VM イメージを作成するには、次の手順を実行します。

1. 変数をいくつか作成します。

    ```azurepowershell-interactive
    $vmName = "myVM"
    $rgName = "myResourceGroup"
    $location = "EastUS"
    $imageName = "myImage"
    ```
2. VM の割り当てが解除されていることを確認します。

    ```azurepowershell-interactive
    Stop-AzVM -ResourceGroupName $rgName -Name $vmName -Force
    ```
    
3. 仮想マシンの状態を **[一般化]** に設定します。 
   
    ```azurepowershell-interactive
    Set-AzVm -ResourceGroupName $rgName -Name $vmName -Generalized
    ```
    
4. 仮想マシンを取得します。 

    ```azurepowershell-interactive
    $vm = Get-AzVM -Name $vmName -ResourceGroupName $rgName
    ```

5. イメージの設定を作成します。

    ```azurepowershell-interactive
    $image = New-AzImageConfig -Location $location -SourceVirtualMachineId $vm.Id 
    ```
6. イメージを作成します。

    ```azurepowershell-interactive
    New-AzImage -Image $image -ImageName $imageName -ResourceGroupName $rgName
    ``` 

## <a name="create-an-image-from-a-managed-disk-using-powershell"></a>Powershell を使ってマネージド ディスクからイメージを作成する

OS ディスクのみのイメージを作成する場合は、OS ディスクとしてマネージド ディスクの ID を指定します。

    
1. 変数をいくつか作成します。 

    ```azurepowershell-interactive
    $vmName = "myVM"
    $rgName = "myResourceGroup"
    $location = "EastUS"
    $imageName = "myImage"
    ```

2. VM を取得します。

   ```azurepowershell-interactive
   $vm = Get-AzVm -Name $vmName -ResourceGroupName $rgName
   ```

3. マネージド ディスクの ID を取得します。

    ```azurepowershell-interactive
    $diskID = $vm.StorageProfile.OsDisk.ManagedDisk.Id
    ```
   
3. イメージの設定を作成します。

    ```azurepowershell-interactive
    $imageConfig = New-AzImageConfig -Location $location
    $imageConfig = Set-AzImageOsDisk -Image $imageConfig -OsState Generalized -OsType Windows -ManagedDiskId $diskID
    ```
    
4. イメージを作成します。

    ```azurepowershell-interactive
    New-AzImage -ImageName $imageName -ResourceGroupName $rgName -Image $imageConfig
    ``` 


## <a name="create-an-image-from-a-snapshot-using-powershell"></a>Powershell を使ってスナップショットからイメージを作成する

次の手順に従って、一般化された VM のスナップショットから管理対象イメージを作成できます。

    
1. 変数をいくつか作成します。 

    ```azurepowershell-interactive
    $rgName = "myResourceGroup"
    $location = "EastUS"
    $snapshotName = "mySnapshot"
    $imageName = "myImage"
    ```

2. スナップショットを取得します。

   ```azurepowershell-interactive
   $snapshot = Get-AzSnapshot -ResourceGroupName $rgName -SnapshotName $snapshotName
   ```
   
3. イメージの構成を作成します。

    ```azurepowershell-interactive
    $imageConfig = New-AzImageConfig -Location $location
    $imageConfig = Set-AzImageOsDisk -Image $imageConfig -OsState Generalized -OsType Windows -SnapshotId $snapshot.Id
    ```
4. イメージを作成します。

    ```azurepowershell-interactive
    New-AzImage -ImageName $imageName -ResourceGroupName $rgName -Image $imageConfig
    ``` 


## <a name="create-an-image-from-a-vhd-in-a-storage-account"></a>ストレージ アカウント内の VHD からイメージを作成します

ストレージ アカウントで、一般化された OS の VHD から管理対象イメージを作成します。 ストレージ アカウントでは、 https://*mystorageaccount*.blob.core.windows.net/*vhdcontainer*/*vhdfilename.vhd* という形式の VHD の URI が必要です。 この例では、VHD は *vhdcontainer* という名前のコンテナーの *mystorageaccount* 内にあり、VHD ファイル名は *vhdfilename.vhd* です。


1.  変数をいくつか作成します。

    ```azurepowershell-interactive
    $vmName = "myVM"
    $rgName = "myResourceGroup"
    $location = "EastUS"
    $imageName = "myImage"
    $osVhdUri = "https://mystorageaccount.blob.core.windows.net/vhdcontainer/vhdfilename.vhd"
    ```
2. VM を停止するか、VM の割り当てを解除します。

    ```azurepowershell-interactive
    Stop-AzVM -ResourceGroupName $rgName -Name $vmName -Force
    ```
    
3. VM を一般化としてマークします。

    ```azurepowershell-interactive
    Set-AzVm -ResourceGroupName $rgName -Name $vmName -Generalized  
    ```
4.  一般化した OS VHD を使ってイメージを作成します。

    ```azurepowershell-interactive
    $imageConfig = New-AzImageConfig -Location $location
    $imageConfig = Set-AzImageOsDisk -Image $imageConfig -OsType Windows -OsState Generalized -BlobUri $osVhdUri
    $image = New-AzImage -ImageName $imageName -ResourceGroupName $rgName -Image $imageConfig
    ```

    
## <a name="next-steps"></a>次の手順
- [管理イメージから VM を作成する](create-vm-generalized-managed.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)。    

