---
title: "Azure で管理対象イメージを作成する | Microsoft Docs"
description: "Azure で一般化された VM または VHD の管理対象イメージを作成します。 イメージを使って、管理対象ディスクを使う複数の VM を作成することができます。"
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
ms.date: 02/27/2017
ms.author: cynthn
translationtype: Human Translation
ms.sourcegitcommit: 0f6bd85668533ee79700caead2edc6bc6c7d70de
ms.openlocfilehash: 45383cb3b8238d72bf9ba398a22fe0a005941c3f
ms.lasthandoff: 03/01/2017


---
# <a name="capture-a-managed-image-of-a-generalized-vm-in-azure"></a>Azure で一般化された VM の管理対象イメージをキャプチャする

ストレージ アカウントに管理対象ディスクまたは非管理対象ディスクとして格納されている一般化された VM から管理対象イメージ リソースを作成できます。 その後、イメージを使って、記憶域として管理対象ディスクを使う複数の VM を作成することができます。 


## <a name="prerequisites"></a>前提条件
既に [VM を一般化](virtual-machines-windows-generalize-vhd.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)してあり、VM を停止して割り当て解除してある必要があります。 VM の一般化とは、特に重要なこととして、すべての個人アカウント情報を削除して、マシンをイメージとして使用できるように準備することです。



## <a name="create-a-managed-image-in-the-portal"></a>ポータルで管理対象イメージを作成する 

1. [ポータル](https://portal.azure.com)を開きます。
2. プラス記号をクリックして、新しいリソースを作成します。
3. フィルター検索で、「**イメージ**」と入力します。
4. 結果で "**イメージ**" を選びます。
5. **[イメージ]** ブレードで、**[作成]** をクリックします。
6. **[名前]** に、イメージの名前を入力します。
7. 複数のサブスクリプションがある場合は、**[サブスクリプション]** ドロップダウンから正しいサブスクリプションを選択します。
7. **[リソース グループ]** で、**[新規作成]** を選んで名前を入力するか、または **[From existing (既存から)]** を選び、ドロップダウン リストから使うリソース グループを選びます。
8. **[場所]** で、リソース グループの場所を選びます。
9. **[OS の種類]** で、オペレーティング システムの種類を選びます (Windows または Linux)。
11. **[ストレージ BLOB]** で、**[参照]** をクリックしてご使用の Azure Storage で VHD を探します。
12. **[アカウントの種類]** で、Standard_LRS または Premium_LRS を選びます。 Standard はハード ディスク ドライブを使い、Premium はソリッド ステート ドライブを使います。 どちらもローカル冗長ストレージを使います。
13. **[Disk caching (ディスク キャッシュ)]** で、適切なディスク キャッシュ オプションを選びます。 オプションは、**[なし]**、**[読み取り専用]**、**[Read\write (読み取り\書き込み)]** です。
14. 省略可能: **[+ Add data disk (+ データ ディスクの追加)]** をクリックして既存のデータ ディスクをイメージに追加してもかまいません。  
15. 選択が終了したら、**[作成]** をクリックします。
16. 作成したイメージは、選んだリソース グループのリソースの一覧に **[イメージ]** リソースとして表示されます。



## <a name="create-a-managed-image-of-a-vm-using-powershell"></a>Powershell を使って VM の管理対象イメージを作成する

VM からイメージを直接作成すると、OS ディスクやすべてのデータ ディスクなど、VM に関連付けられているすべてのディスクが、イメージに確実に含まれます。


始める前に、AzureRM.Compute PowerShell モジュールの最新バージョンがあることを確認してください。 インストールするには次のコマンドを実行します。

```powershell
Install-Module AzureRM.Compute -RequiredVersion 2.6.0
```
詳しくは、[Azure PowerShell のバージョン管理に関するページ](https://docs.microsoft.com/powershell/azureps-cmdlets-docs/#azure-powershell-versioning)をご覧ください。


1. 変数をいくつか作成します。 
    ```powershell
    $vmName = "myVM"
    $rgName = "myResourceGroup"
    $location = "EastUS"
    $imageName = "myImage"
    ```
2. VM の割り当てが解除されていることを確認します。

    ```powershell
    Stop-AzureRmVM -ResourceGroupName $rgName -Name $vmName -Force
    ```
    
3. 仮想マシンの状態を **[一般化]**に設定します。 
   
    ```powershell
    Set-AzureRmVm -ResourceGroupName $rgName -Name $vmName -Generalized
    ```
    
4. 仮想マシンを取得します。 

    ```powershell
    $vm = Get-AzureRmVM -Name $vmName -ResourceGroupName $rgName
    ```

5. イメージの構成を作成します。

    ```powershell
    $image = New-AzureRmImageConfig -Location $location -SourceVirtualMachineId $vm.ID 
    ```
6. イメージを作成します。

    ```powershell
    New-AzureRmImage -Image $image -ImageName $imageName -ResourceGroupName $rgName
    ```    



## <a name="create-a-managed-image-of-a-vhd-in-powershell"></a>Powershell で VHD の管理対象イメージを作成する

アップロードした OS VHD を使って管理対象イメージを作成します。


1.  最初に、共通のパラメーターを設定します。

    ```powershell
    $rgName = "myResourceGroupName"
    $vmName = "myVM"
    $location = "West Central US" 
    $imageName = "yourImageName"
    $osVhdUri = "https://storageaccount.blob.core.windows.net/vhdcontainer/osdisk.vhd"
    ```
2. VM を停止し、割り当てを解除します。

    ```powershell
    Stop-AzureRmVM -ResourceGroupName $rgName -Name $vmName -Force
    ```
    
3. VM を一般化としてマークします。

    ```powershell
    Set-AzureRmVm -ResourceGroupName $rgName -Name $vmName -Generalized    
    ```
4.  一般化した OS VHD を使ってイメージを作成します。

    ```powershell
    $imageConfig = New-AzureRmImageConfig -Location $location
    $imageConfig = Set-AzureRmImageOsDisk -Image $imageConfig -OsType Windows -OsState Generalized -BlobUri $osVhdUri
    $image = New-AzureRmImage -ImageName $imageName -ResourceGroupName $rgName -Image $imageConfig
    ```


## <a name="create-a-managed-image-from-a-snapshot-using-powershell"></a>Powershell を使ってスナップショットから管理対象イメージを作成する

一般化された VM の VHD のスナップショットから、管理対象イメージを作成することもできます。

    
1. 変数をいくつか作成します。 

    ```powershell
    $rgName = "myResourceGroup"
    $location = "EastUS"
    $snapshotName = "mySnapshot"
    $imageName = "myImage"
    ```

2. スナップショットを取得します。

   ```powershell
   $snapshot = Get-AzureRmSnapshot -ResourceGroupName $rgName -SnapshotName $snapshotName
   ```
   
3. イメージの構成を作成します。

    ```powershell
    $imageConfig = New-AzureRmImageConfig -Location $location
    $imageConfig = Set-AzureRmImageOsDisk -Image $imageConfig -OsState Generalized -OsType Windows -SnapshotId $snapshot.Id
    ```
4. イメージを作成します。

    ```powershell
    New-AzureRmImage -ImageName $imageName -ResourceGroupName $rgName -Image $imageConfig
    ```    
    

## <a name="next-steps"></a>次のステップ
- これで、[一般化された管理対象イメージから VM を作成する](virtual-machines-windows-create-vm-generalized-managed.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)ことができるようになりました。    


