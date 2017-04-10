---
title: "AWS VM から Azure への移行 | Microsoft Docs"
description: "アマゾン ウェブ サービス (AWS) EC2 インスタンスを Azure Virtual Machines に移行します。 このシナリオでは、クラウド ストレージを簡略化するために Managed Disks を使用します。"
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
ms.date: 02/08/2017
ms.author: cynthn
translationtype: Human Translation
ms.sourcegitcommit: 204fa369dd6db618ec5340317188681b0a2988e3
ms.openlocfilehash: 6854ebe781220dfdf957fa1d89ec19d32e6febea
ms.lasthandoff: 02/11/2017


---

# <a name="migrate-from-amazon-web-services-aws-to-azure-managed-disks"></a>アマゾン ウェブ サービス (AWS) から Azure Managed Disks に移行する

アマゾン ウェブ サービス (AWS) EC2 インスタンスを、VHD をアップロードすることで Azure Virtual Machines に移行できます。 Azure で同一のイメージから複数の VM を作成するには、まず VM を一般化してから、一般化済みの VHD をローカル ディレクトリにエクスポートする必要があります。 VHD のアップロード後、ストレージに [Managed Disks](../storage/storage-managed-disks-overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) を使用する新しい Azure VM を作成できます。 Azure Managed Disks を使用すると、Azure IaaS VM のストレージ アカウントを管理する必要がなくなります。 必要なディスクの種類 (Premium または Standard) とサイズを指定するだけでよく、ディスクは Azureによって作成および管理されます。 

このプロセスを開始する前に、[Managed Disks への移行の計画](virtual-machines-windows-on-prem-to-azure.md#plan-for-the-migration-to-managed-disks)に関するページをご覧ください。

VHD を Azure にアップロードする前に、「[Prepare a Windows VHD or VHDX to upload to Azure](virtual-machines-windows-prepare-for-upload-vhd-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)」(Azure にアップロードする Windows VHD または VHDX を準備する) に従う必要があります。

## <a name="before-you-begin"></a>開始する前に
PowerShell を使用する場合は、AzureRM.Compute PowerShell モジュールの最新バージョンがあることを確認してください。 インストールするには次のコマンドを実行します。

```powershell
Install-Module AzureRM.Compute -MinimumVersion 2.6.0
```
詳細については、[Azure PowerShell のバージョン管理に関するページ](https://docs.microsoft.com/powershell/azureps-cmdlets-docs/#azure-powershell-versioning)をご覧ください。


## <a name="generalize-the-windows-vm-using-sysprep"></a>Sysprep を使用して Windows VM を一般化する

Sysprep を使用して VM を一般化すると、VHD からマシン固有の情報と個人アカウント情報が削除され、マシンをイメージとして使用できるようになります。 Sysprep の詳細については、「 [Sysprep の使用方法: 紹介](http://technet.microsoft.com/library/bb457073.aspx)」を参照してください。

コンピューター上で実行されるサーバー ロールが Sysprep でサポートされていることを確認します。 詳しくは、「 [Sysprep Support for Server Roles](https://msdn.microsoft.com/windows/hardware/commercialize/manufacture/desktop/sysprep-support-for-server-roles)

> [!IMPORTANT]
> 初めて VHD を Azure にアップロードする前に Sysprep を実行する場合、Sysprep の実行前に [VM の準備](virtual-machines-windows-prepare-for-upload-vhd-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)ができていることを確認してください。 
> 
> 

1. Windows 仮想マシンへのサインイン
2. 管理者としてコマンド プロンプト ウィンドウを開きます。 ディレクトリを **%windir%\system32\sysprep** に変更し、`sysprep.exe` を実行します。
3. **[システム準備ツール]** ダイアログ ボックスで **[システムの OOBE (Out-of-Box Experience) に入る]** を選択し、**[一般化する]** チェック ボックスがオンになっていることを確認します。
4. **[シャットダウン オプション]** の **[シャットダウン]** を選択します。
5. **[OK]**をクリックします。
   
    ![Sysprep の開始](./media/virtual-machines-windows-upload-image/sysprepgeneral.png)
6. Sysprep は完了時に仮想マシンをシャットダウンします。 VM は再起動しないでください。



## <a name="export-the-vhd-from-an-ec2-instance"></a>EC2 インスタンスから VHD をエクスポートする

1.    アマゾン ウェブ サービス (AWS) を使用している場合は、Amazon S3 バケット内の VHD に EC2 インスタンスをエクスポートします。 Amazon EC2 インスタンスのエクスポートに関する Amazon ドキュメントで説明されている手順に従って、Amazon EC2 コマンド ライン インターフェイス (CLI) ツールをインストールし、create-instance-export-task コマンドを実行して EC2 インスタンスを VHD ファイルにエクスポートします。 create-instance-export-task コマンドを実行するときは、DISK_IMAGE_FORMAT 変数に必ず VHD を使用してください。 エクスポートされた VHD ファイルは、その処理中に指定した Amazon S3 バケットに保存されます。

    ```
    aws ec2 create-instance-export-task --instance-id ID --target-environment TARGET_ENVIRONMENT '
    --export-to-s3-task DiskImageFormat=DISK_IMAGE_FORMAT,ContainerFormat=ova,S3Bucket=BUCKET,S3Prefix=PREFIX
    ```

2.    VHD ファイルを S3 バケットからダウンロードします。 VHD ファイルを選択し、**[Actions] \(操作)** > **[Download] \(ダウンロード)** の順に選択します。



## <a name="log-in-to-azure"></a>Azure へのログイン
PowerShell をまだインストールしていない場合は、[Azure PowerShell のインストールおよび構成方法](/powershell/azureps-cmdlets-docs)に関するページをご覧ください。

1. Azure PowerShell を開き、Azure アカウントにサインインします。 Azure アカウント資格情報を入力するためのポップアップ ウィンドウが開きます。
   
    ```powershell
    Login-AzureRmAccount
    ```
2. 使用可能なサブスクリプションのサブスクリプション ID を取得します。
   
    ```powershell
    Get-AzureRmSubscription
    ```
3. このサブスクリプション ID を使用して、適切なサブスクリプションを設定します。 `<subscriptionID>` を適切なサブスクリプションの ID と置き換えます。
   
    ```powershell
    Select-AzureRmSubscription -SubscriptionId "<subscriptionID>"
    ```

## <a name="get-the-storage-account"></a>ストレージ アカウントを取得する
アップロードした VM イメージを格納するには、Azure にストレージ アカウントが必要です。 既存のストレージ アカウントを選択することも、新しいストレージ アカウントを作成することもできます。 

VHD を使用して VM の管理ディスクを作成する場合は、ストレージ アカウントの場所を、VM を作成する場所と同じにする必要があります。

使用できるストレージ アカウントを表示するには、次のように入力します。

```powershell
Get-AzureRmStorageAccount
```

既存のストレージ アカウントを使用する場合は、「[VM イメージのアップロード](#upload-the-vm-vhd-to-your-storage-account)」セクションに進みます。

ストレージ アカウントを作成する場合は、次の手順に従います。

1. ストレージ アカウントを作成するリソース グループ名が必要です。 サブスクリプションのすべてのリソース グループを検索するには、次のように入力します。
   
    ```powershell
    Get-AzureRmResourceGroup
    ```

    **myResourceGroup** という名前のリソース グループを**米国西部**リージョンで作成するには、次のように入力します。

    ```powershell
    New-AzureRmResourceGroup -Name myResourceGroup -Location "West US"
    ```

2. [New-AzureStorageAccount](https://msdn.microsoft.com/library/mt607148.aspx) コマンドレットを使用して、このリソース グループに **mystorageaccount** というストレージ アカウントを作成します。
   
    ```powershell
    New-AzureRmStorageAccount -ResourceGroupName myResourceGroup -Name mystorageaccount -Location "West US" `
        -SkuName "Standard_LRS" -Kind "Storage"
    ```
   
    -SkuName の値が次のとおりであることを確認します。
   
   * **Standard_LRS** - ローカル冗長ストレージ。 
   * **Standard_ZRS** - ゾーン冗長ストレージ。
   * **Standard_GRS** - geo 冗長ストレージ。 
   * **Standard_RAGRS** - 読み取りアクセス geo 冗長ストレージ。 
   * **Premium_LRS** - Premium ローカル冗長ストレージ。 

## <a name="upload-the-vhd-to-your-storage-account"></a>ストレージ アカウントに VHD をアップロードする

[Add-AzureRmVhd](https://msdn.microsoft.com/library/mt603554.aspx) コマンドレットを使用して、ストレージ アカウント内のコンテナーに VHD をアップロードします。 この例では、ファイル **myVHD.vhd** を`"C:\Users\Public\Documents\Virtual hard disks\"`から **myResourceGroup** リソース グループの **mystorageaccount** というストレージ アカウントにアップロードします。 ファイルは **mycontainer** というコンテナーに配置され、新しいファイル名は **myUploadedVHD.vhd** になります。

```powershell
$rgName = "myResourceGroup"
$urlOfUploadedImageVhd = "https://mystorageaccount.blob.core.windows.net/mycontainer/myUploadedVHD.vhd"
Add-AzureRmVhd -ResourceGroupName $rgName -Destination $urlOfUploadedImageVhd `
    -LocalFilePath "C:\Users\Public\Documents\Virtual hard disks\myVHD.vhd"
```


成功した場合、次のような応答を取得します。

```powershell
MD5 hash is being calculated for the file C:\Users\Public\Documents\Virtual hard disks\myVHD.vhd.
MD5 hash calculation is completed.
Elapsed time for the operation: 00:03:35
Creating new page blob of size 53687091712...
Elapsed time for upload: 01:12:49

LocalFilePath           DestinationUri
-------------           --------------
C:\Users\Public\Doc...  https://mystorageaccount.blob.core.windows.net/mycontainer/myUploadedVHD.vhd
```

このコマンドは、ネットワーク接続や VHD ファイルのサイズによっては、完了に時間がかかることがあります。

アップロードした VHD を使用して管理ディスクまたは新しい VM を作成する場合は、後で使用するために**送信先 URI** のパスを保存します。

### <a name="other-options-for-uploading-a-vhd"></a>VHD をアップロードするためのその他のオプション

次のいずれかの方法を使用して、ストレージ アカウントに VHD をアップロードすることもできます。

-   [Azure Storage Copy Blob API](https://msdn.microsoft.com/library/azure/dd894037.aspx)

-   [Azure ストレージ エクスプローラーでの BLOB のアップロード](https://azurestorageexplorer.codeplex.com/)

-   [Storage Import/Export Service REST API リファレンス](https://msdn.microsoft.com/library/dn529096.aspx)

    推定アップロード時間が 7 日より長い場合は、Import/Export サービスを使うことをお勧めします。 [DataTransferSpeedCalculator](https://github.com/Azure-Samples/storage-dotnet-import-export-job-management/blob/master/DataTransferSpeedCalculator.html) を使うと、データ サイズと転送単位から時間を推定できます。 

    Import/Export は、Standard Storage アカウントへのコピーに使うことができます。 Standard Storage アカウントから Premium Storage アカウントにコピーするには、AzCopy などのツールを使用する必要があります。

## <a name="create-a-managed-image-from-the-uploaded-vhd"></a>アップロードした VHD から管理イメージを作成する 

一般化済みの OS VHD を使って管理イメージを作成します。


1.  最初に、共通のパラメーターを設定します。

    ```powershell
    $rgName = "myResourceGroupName"
    $vmName = "myVM"
    $location = "West Central US" 
    $imageName = "yourImageName"
    $osVhdUri = "https://storageaccount.blob.core.windows.net/vhdcontainer/osdisk.vhd"
    ```

4.  一般化した OS VHD を使ってイメージを作成します。

    ```powershell
    $imageConfig = New-AzureRmImageConfig -Location $location
    $imageConfig = Set-AzureRmImageOsDisk -Image $imageConfig -OsType Windows -OsState Generalized -BlobUri $osVhdUri
    $image = New-AzureRmImage -ImageName $imageName -ResourceGroupName $rgName -Image $imageConfig
    ```

## <a name="setup-some-variables-for-the-image"></a>イメージの変数を設定する

まず、イメージに関する基本的な情報を収集し、イメージの変数を作成する必要があります。 この例では、**West Central US** という場所のリソース グループ **myResourceGroup** にある **myImage** という名前の管理 VM イメージを使用しています。 

```powershell
$rgName = "myResourceGroup"
$location = "West Central US"
$imageName = "myImage"
$image = Get-AzureRMImage -ImageName $imageName -ResourceGroupName $rgName
```

## <a name="create-a-virtual-network"></a>仮想ネットワークの作成
[仮想ネットワーク](../virtual-network/virtual-networks-overview.md)の vNet とサブネットを作成します。

1. サブネットを作成します。 次の例では、**10.0.0.0/24** というアドレス プレフィックスを持つ **mySubnet** という名前のサブネットを作成します。  
   
    ```powershell
    $subnetName = "mySubnet"
    $singleSubnet = New-AzureRmVirtualNetworkSubnetConfig -Name $subnetName -AddressPrefix 10.0.0.0/24
    ```
2. 仮想ネットワークを作成する。 次の例では、**10.0.0.0/16** というアドレス プレフィックスを持つ **myVnet** という名前の仮想ネットワークを作成します。  
   
    ```powershell
    $vnetName = "myVnet"
    $vnet = New-AzureRmVirtualNetwork -Name $vnetName -ResourceGroupName $rgName -Location $location `
        -AddressPrefix 10.0.0.0/16 -Subnet $singleSubnet
    ```    

## <a name="create-a-public-ip-address-and-network-interface"></a>パブリック IP アドレスとネットワーク インターフェイスの作成

仮想ネットワークでの仮想マシンとの通信を有効にするには、 [パブリック IP アドレス](../virtual-network/virtual-network-ip-addresses-overview-arm.md) とネットワーク インターフェイスが必要です。

1. パブリック IP アドレスを作成します。 この例では、**myPip** という名前のパブリック IP アドレスを作成します。 
   
    ```powershell
    $ipName = "myPip"
    $pip = New-AzureRmPublicIpAddress -Name $ipName -ResourceGroupName $rgName -Location $location `
        -AllocationMethod Dynamic
    ```       
2. NIC を作成します。 この例では、**myNic** という名前の NIC を作成します。 
   
    ```powershell
    $nicName = "myNic"
    $nic = New-AzureRmNetworkInterface -Name $nicName -ResourceGroupName $rgName -Location $location `
        -SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $pip.Id
    ```

## <a name="create-the-network-security-group-and-an-rdp-rule"></a>ネットワーク セキュリティ グループと RDP 規則の作成

RDP を使用して VM にログインできるようにするには、ポート 3389 に対する RDP アクセスを許可するネットワーク セキュリティの規則 (NSG) が必要です。 

この例では、**myNsg** という名前の NSG を作成します。この NSG には、ポート 3389 経由の RDP トラフィックを許可する **myRdpRule** という名前の規則が含まれています。 NSG の詳細については、[PowerShell を使用した Azure の VM へのポートの開放](virtual-machines-windows-nsg-quickstart-powershell.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)に関するページを参照してください。

```powershell
$nsgName = "myNsg"
$ruleName = "myRdpRule"
$rdpRule = New-AzureRmNetworkSecurityRuleConfig -Name $ruleName -Description "Allow RDP" `
    -Access Allow -Protocol Tcp -Direction Inbound -Priority 110 `
    -SourceAddressPrefix Internet -SourcePortRange * `
    -DestinationAddressPrefix * -DestinationPortRange 3389

$nsg = New-AzureRmNetworkSecurityGroup -ResourceGroupName $rgName -Location $location `
    -Name $nsgName -SecurityRules $rdpRule
```


## <a name="create-a-variable-for-the-virtual-network"></a>仮想ネットワーク用の変数の作成

変数を作成して、仮想ネットワークの作成を完了します。 

```powershell
$vnet = Get-AzureRmVirtualNetwork -ResourceGroupName $rgName -Name $vnetName

```

## <a name="get-the-credentials-for-the-vm"></a>VM の資格情報の取得

次のコマンドレットを実行するとウィンドウが開きます。そこで、VM へのリモート アクセスでローカル管理者アカウントとして使用する、新しいユーザー名とパスワードを入力します。 

```powershell
$cred = Get-Credential
```

## <a name="set-variables-for-the-vm-name-computer-name-and-the-size-of-the-vm"></a>VM 名、コンピューター名、および VM のサイズの変数の設定

1. VM 名とコンピューター名の変数を作成します。 この例では、VM 名として **myVM** を、コンピューター名として **myComputer** を設定します。

    ```powershell
    $vmName = "myVM"
    $computerName = "myComputer"
    ```
2. 仮想マシンのサイズを設定します。 次の例では、サイズが **Standard_DS1_v2** に設定された VM を作成します。 詳細については、[VM のサイズ](https://azure.microsoft.com/documentation/articles/virtual-machines-windows-sizes/)に関するドキュメントを参照してください。

    ```powershell
    $vmSize = "Standard_DS1_v2"
    ```

3. VM の名前とサイズを VM 構成に追加します。

```powershell
$vm = New-AzureRmVMConfig -VMName $vmName -VMSize $vmSize
```

## <a name="set-the-vm-image-as-source-image-for-the-new-vm"></a>新しい VM のソース イメージとしての VM イメージの設定

管理 VM イメージの ID を使用してソース イメージを設定します。

```powershell
$vm = Set-AzureRmVMSourceImage -VM $vm -Id $image.Id
```

## <a name="set-the-os-configuration-and-add-the-nic"></a>OS 構成の設定と NIC の追加

ストレージの種類 (PremiumLRS または StandardLRS) と OS ディスクのサイズを入力します。 この例では、アカウントの種類を **PremiumLRS** に、ディスク サイズを **128 GB**に、ディスク キャッシュを **ReadWrite** に設定します。

```powershell
$vm = Set-AzureRmVMOSDisk -VM $vm  -ManagedDiskStorageAccountType PremiumLRS -DiskSizeInGB 128 `
-CreateOption FromImage -Caching ReadWrite

$vm = Set-AzureRmVMOperatingSystem -VM $vm -Windows -ComputerName $computerName `
-Credential $cred -ProvisionVMAgent -EnableAutoUpdate

$vm = Add-AzureRmVMNetworkInterface -VM $vm -Id $nic.Id
```

## <a name="create-the-vm"></a>VM の作成

先ほど作成し **$vm** 変数に格納した構成を使用して、新しい VM を作成します。

```powershell
New-AzureRmVM -VM $vm -ResourceGroupName $rgName -Location $location
```

## <a name="verify-that-the-vm-was-created"></a>VM 作成の確認
完了したら、[Azure Portal](https://portal.azure.com) で **[参照]** > **[仮想マシン]** にアクセスするか、次の PowerShell コマンドを使用して、新しく作成された VM を確認します。

```powershell
    $vmList = Get-AzureRmVM -ResourceGroupName $rgName
    $vmList.Name
```

## <a name="next-steps"></a>次のステップ

新しい仮想マシンにサインインするには、 [ポータル](https://portal.azure.com)で VM を参照し、 **[接続]**をクリックして、リモート デスクトップ RDP ファイルを開きます。 元の仮想マシンのアカウント資格情報を使用して、新しい仮想マシンにサインインします。 詳しくは、「[Windows が実行されている Azure 仮想マシンに接続してログオンする方法](virtual-machines-windows-connect-logon.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)」をご覧ください。 
 

