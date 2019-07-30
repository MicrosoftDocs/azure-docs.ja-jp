---
title: Azure で一般化された VM の非管理対象イメージを作成する | Microsoft Docs
description: Azure で VM の複数のコピーを作成するために使用する一般化された Windows VM の非管理対象イメージを作成します。
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: gwallace
editor: tysonn
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 05/23/2017
ms.author: cynthn
ROBOTS: NOINDEX
ms.openlocfilehash: 929dd5bdb01adeaa7b1332bd7a5e6d823edba34a
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/09/2019
ms.locfileid: "67710402"
---
# <a name="how-to-create-an-unmanaged-vm-image-from-an-azure-vm"></a>Azure VM から非管理対象 VM イメージを作成する方法

この記事では、ストレージ アカウントの使用について説明します。 ストレージ アカウントの代わりに、マネージド ディスクおよび管理対象イメージを使用することをお勧めします。 詳細については、「[Azure で一般化された VM の管理対象イメージをキャプチャする](capture-image-resource.md)」を参照してください。

この記事では、ストレージ アカウントを使用し、Azure PowerShell を使用して、一般化された Azure VM のイメージを作成する方法について説明します。 その後、そのイメージを使用して、別の VM を作成できます。 イメージには、仮想マシンに接続された OS ディスクやデータ ディスクが含まれます。 イメージには仮想ネットワーク リソースは含まれないため、新しい VM を作成するときにこれらのリソースを設定する必要があります。 

[!INCLUDE [updated-for-az.md](../../../includes/updated-for-az.md)]

## <a name="generalize-the-vm"></a>VM の汎用化 
このセクションでは、Windows 仮想マシンをイメージとして使用できるように一般化する方法について説明します。 VM の一般化とは、特に重要なこととして、すべての個人アカウント情報を削除して、マシンをイメージとして使用できるように準備することです。 Sysprep の詳細については、「[How to Use Sysprep: An Introduction (Sysprep の使用方法: 紹介)](https://technet.microsoft.com/library/bb457073.aspx)」を参照してください。

コンピューター上で実行されるサーバー ロールが Sysprep でサポートされていることを確認します。 詳しくは、「 [Sysprep Support for Server Roles](https://msdn.microsoft.com/windows/hardware/commercialize/manufacture/desktop/sysprep-support-for-server-roles)

> [!IMPORTANT]
> 初めて VHD を Azure にアップロードする場合は、Sysprep を実行する前に、[VM の準備](prepare-for-upload-vhd-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)ができていることを確認してください。 
> 
> 

`sudo waagent -deprovision+user` を使用して Linux VM を一般化してから、PowerShell を使用して VM をキャプチャすることもできます。 CLI を使用して VM をキャプチャする方法については、[Azure CLI を使用して Linux 仮想マシンを一般化およびキャプチャする方法](../linux/capture-image.md)に関するページを参照してください。


1. Windows 仮想マシンへのサインイン
2. 管理者としてコマンド プロンプト ウィンドウを開きます。 ディレクトリを **%windir%\system32\sysprep** に変更し、`sysprep.exe` を実行します。
3. **[システム準備ツール]** ダイアログ ボックスで **[システムの OOBE (Out-of-Box Experience) に入る]** を選択し、 **[一般化する]** チェック ボックスがオンになっていることを確認します。
4. **[シャットダウン オプション]** の **[シャットダウン]** を選択します。
5. Click **OK**.
   
    ![Sysprep の開始](./media/upload-generalized-managed/sysprepgeneral.png)
6. Sysprep は完了時に仮想マシンをシャットダウンします。 

> [!IMPORTANT]
> Azure に VHD をアップロードするか VM からイメージを作成するまで、VM を再起動しないでください。 誤って VM を再起動した場合は、もう一度 Sysprep を実行して一般化してください。
> 
> 

## <a name="log-in-to-azure-powershell"></a>Azure PowerShell へのログイン
1. Azure PowerShell を開き、Azure アカウントにサインインします。
   
    ```powershell
    Connect-AzAccount
    ```
   
    Azure アカウント資格情報を入力するためのポップアップ ウィンドウが開きます。
2. 使用可能なサブスクリプションのサブスクリプション ID を取得します。
   
    ```powershell
    Get-AzSubscription
    ```
3. このサブスクリプション ID を使用して、適切なサブスクリプションを設定します。
   
    ```powershell
    Select-AzSubscription -SubscriptionId "<subscriptionID>"
    ```

## <a name="deallocate-the-vm-and-set-the-state-to-generalized"></a>VM の割り当てを解除して、一般化状態に設定

> [!IMPORTANT] 
> 一般化済みとしてマークされた VM のタグを、追加、編集、削除することはできません。 VM にタグを追加したい場合は、一般化済みとしてマークする前に行ってください。
> 

1. VM リソースの割り当てを解除します。
   
    ```powershell
    Stop-AzVM -ResourceGroupName <resourceGroup> -Name <vmName>
    ```
   
    Azure Portal で VM の *[状態]* が **[停止済み]** から **[停止済み (割り当て解除)]** に変わります。
2. 仮想マシンの状態を **[一般化]** に設定します。 
   
    ```powershell
    Set-AzVm -ResourceGroupName <resourceGroup> -Name <vmName> -Generalized
    ```
3. VM の状態を確認します。 VM の **[OSState/generalized (OS の状態/一般化)]** セクションの **[DisplayStatus (ステータス表示)]** が **[VM が汎用化されました]** に設定されています。  
   
    ```powershell
    $vm = Get-AzVM -ResourceGroupName <resourceGroup> -Name <vmName> -Status
    $vm.Statuses
    ```

## <a name="create-the-image"></a>イメージの作成

次のコマンドを使用して、非管理対象仮想マシン イメージを宛先のストレージ コンテナー内に作成します。 イメージは、元の仮想マシンと同じストレージ アカウントに作成されます。 `-Path` パラメーターは、ソース VM の JSON テンプレートのコピーをローカル コンピューターに保存します。 `-DestinationContainerName` パラメーターは、イメージを格納するコンテナーの名前です。 コンテナーが存在しない場合、コンテナーは自動的に作成されます。
   
```powershell
Save-AzVMImage -ResourceGroupName <resourceGroupName> -Name <vmName> `
    -DestinationContainerName <destinationContainerName> -VHDNamePrefix <templateNamePrefix> `
    -Path <C:\local\Filepath\Filename.json>
```
   
イメージの URL は、JSON ファイル テンプレートから取得できます。 イメージの完全なパスは、**resources** > **storageProfile** > **osDisk** > **image** > **uri** セクションにあります。 イメージの URL は `https://<storageAccountName>.blob.core.windows.net/system/Microsoft.Compute/Images/<imagesContainer>/<templatePrefix-osDisk>.xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx.vhd`のようになります。
   
ポータルで URI を確認することもできます。 イメージは、ストレージ アカウントの **system** という名前のコンテナーにコピーされます。 

## <a name="create-a-vm-from-the-image"></a>イメージから VM を作成する

これで、非管理対象イメージから 1 つ以上の VM を作成できるようになりました。

### <a name="set-the-uri-of-the-vhd"></a>VHD の URI の設定

使用する VHD の URI は、 https://**mystorageaccount**.blob.core.windows.net/**mycontainer**/**MyVhdName**.vhd という形式になります。 この例では、**myVHD** という名前の VHD がストレージ アカウント **mystorageaccount** のコンテナー **mycontainer** にあります。

```powershell
$imageURI = "https://mystorageaccount.blob.core.windows.net/mycontainer/myVhd.vhd"
```


### <a name="create-a-virtual-network"></a>仮想ネットワークの作成
[仮想ネットワーク](../../virtual-network/virtual-networks-overview.md)の vNet とサブネットを作成します。

1. サブネットを作成します。 次の例では、アドレス プレフィックス **10.0.0.0/24** で **mySubnet** という名前のサブネットをリソース グループ **myResourceGroup** に作成します。  
   
    ```powershell
    $rgName = "myResourceGroup"
    $subnetName = "mySubnet"
    $singleSubnet = New-AzVirtualNetworkSubnetConfig -Name $subnetName -AddressPrefix 10.0.0.0/24
    ```
2. 仮想ネットワークを作成します。 次の例では、アドレス プレフィックス **10.0.0.0/16** で **myVnet** という名前の仮想ネットワークを場所 **West US** に作成します。  
   
    ```powershell
    $location = "West US"
    $vnetName = "myVnet"
    $vnet = New-AzVirtualNetwork -Name $vnetName -ResourceGroupName $rgName -Location $location `
        -AddressPrefix 10.0.0.0/16 -Subnet $singleSubnet
    ```    

### <a name="create-a-public-ip-address-and-network-interface"></a>パブリック IP アドレスとネットワーク インターフェイスの作成
仮想ネットワークでの仮想マシンとの通信を有効にするには、 [パブリック IP アドレス](../../virtual-network/virtual-network-ip-addresses-overview-arm.md) とネットワーク インターフェイスが必要です。

1. パブリック IP アドレスを作成します。 この例では、**myPip** という名前のパブリック IP アドレスを作成します。 
   
    ```powershell
    $ipName = "myPip"
    $pip = New-AzPublicIpAddress -Name $ipName -ResourceGroupName $rgName -Location $location `
        -AllocationMethod Dynamic
    ```       
2. NIC を作成します。 この例では、**myNic** という名前の NIC を作成します。 
   
    ```powershell
    $nicName = "myNic"
    $nic = New-AzNetworkInterface -Name $nicName -ResourceGroupName $rgName -Location $location `
        -SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $pip.Id
    ```

### <a name="create-the-network-security-group-and-an-rdp-rule"></a>ネットワーク セキュリティ グループと RDP 規則の作成
RDP を使用して VM にログインできるようにするには、ポート 3389 で RDP アクセスを許可するセキュリティ規則が必要です。 

この例では、**myNsg** という名前の NSG を作成します。この NSG には、ポート 3389 経由の RDP トラフィックを許可する **myRdpRule** という名前の規則が含まれています。 NSG の詳細については、[PowerShell を使用した Azure の VM へのポートの開放](nsg-quickstart-powershell.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)に関するページを参照してください。

```powershell
$nsgName = "myNsg"

$rdpRule = New-AzNetworkSecurityRuleConfig -Name myRdpRule -Description "Allow RDP" `
    -Access Allow -Protocol Tcp -Direction Inbound -Priority 110 `
    -SourceAddressPrefix Internet -SourcePortRange * `
    -DestinationAddressPrefix * -DestinationPortRange 3389

$nsg = New-AzNetworkSecurityGroup -ResourceGroupName $rgName -Location $location `
    -Name $nsgName -SecurityRules $rdpRule
```


### <a name="create-a-variable-for-the-virtual-network"></a>仮想ネットワーク用の変数の作成
変数を作成して、仮想ネットワークの作成を完了します。 

```powershell
$vnet = Get-AzVirtualNetwork -ResourceGroupName $rgName -Name $vnetName
```

### <a name="create-the-vm"></a>VM の作成
次の PowerShell は仮想マシンの構成を完了し、非管理対象イメージを新規インストールのソースとして使用します。

</br>

```powershell
    # Enter a new user name and password to use as the local administrator account 
    # for remotely accessing the VM.
    $cred = Get-Credential

    # Name of the storage account where the VHD is located. This example sets the 
    # storage account name as "myStorageAccount"
    $storageAccName = "myStorageAccount"

    # Name of the virtual machine. This example sets the VM name as "myVM".
    $vmName = "myVM"

    # Size of the virtual machine. This example creates "Standard_D2_v2" sized VM. 
    # See the VM sizes documentation for more information: 
    # https://azure.microsoft.com/documentation/articles/virtual-machines-windows-sizes/
    $vmSize = "Standard_D2_v2"

    # Computer name for the VM. This examples sets the computer name as "myComputer".
    $computerName = "myComputer"

    # Name of the disk that holds the OS. This example sets the 
    # OS disk name as "myOsDisk"
    $osDiskName = "myOsDisk"

    # Assign a SKU name. This example sets the SKU name as "Standard_LRS"
    # Valid values for -SkuName are: Standard_LRS - locally redundant storage, Standard_ZRS - zone redundant
    # storage, Standard_GRS - geo redundant storage, Standard_RAGRS - read access geo redundant storage,
    # Premium_LRS - premium locally redundant storage. 
    $skuName = "Standard_LRS"

    # Get the storage account where the uploaded image is stored
    $storageAcc = Get-AzStorageAccount -ResourceGroupName $rgName -AccountName $storageAccName

    # Set the VM name and size
    $vmConfig = New-AzVMConfig -VMName $vmName -VMSize $vmSize

    #Set the Windows operating system configuration and add the NIC
    $vm = Set-AzVMOperatingSystem -VM $vmConfig -Windows -ComputerName $computerName `
        -Credential $cred -ProvisionVMAgent -EnableAutoUpdate
    $vm = Add-AzVMNetworkInterface -VM $vm -Id $nic.Id

    # Create the OS disk URI
    $osDiskUri = '{0}vhds/{1}-{2}.vhd' `
        -f $storageAcc.PrimaryEndpoints.Blob.ToString(), $vmName.ToLower(), $osDiskName

    # Configure the OS disk to be created from the existing VHD image (-CreateOption fromImage).
    $vm = Set-AzVMOSDisk -VM $vm -Name $osDiskName -VhdUri $osDiskUri `
        -CreateOption fromImage -SourceImageUri $imageURI -Windows

    # Create the new VM
    New-AzVM -ResourceGroupName $rgName -Location $location -VM $vm
```

### <a name="verify-that-the-vm-was-created"></a>VM 作成の確認
完了したら、[Azure Portal](https://portal.azure.com) で **[参照]**  >  **[仮想マシン]** にアクセスするか、次の PowerShell コマンドを使用して、新しく作成された VM を確認します。

```powershell
    $vmList = Get-AzVM -ResourceGroupName $rgName
    $vmList.Name
```

## <a name="next-steps"></a>次の手順
Azure PowerShell で新しい仮想マシンを管理する方法については、 [Azure Resource Manager と PowerShell を使用した仮想マシンの管理](tutorial-manage-vm.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)に関するページをご覧ください。


