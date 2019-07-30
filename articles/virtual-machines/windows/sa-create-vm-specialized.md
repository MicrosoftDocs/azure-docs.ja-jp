---
title: Azure で特殊化したディスクから VM を作成する | Microsoft Docs
description: Resource Manager デプロイ モデルで、特殊化された非管理ディスクを接続して新しい VM を作成します。
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: 3b7d3cd5-e3d7-4041-a2a7-0290447458ea
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 05/23/2017
ms.author: cynthn
ROBOTS: NOINDEX
ms.openlocfilehash: 8833ddf487c36446b5e5b4ce1d6cfc6363d3ceeb
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/09/2019
ms.locfileid: "67710389"
---
# <a name="create-a-vm-from-a-specialized-vhd-in-a-storage-account"></a>ストレージ アカウントでの特殊化された VHD からの VM の作成

Powershell を使用して、OS ディスクとして特殊化された非管理ディスクを接続することにより新しい VM を作成します。 特殊化されたディスクは、元の VM のユーザーアカウント、アプリケーション、その他の状態データを維持する、既存の VM の VHD のコピーです。 

2 つのオプションがあります。
* [VHD をアップロードする](sa-create-vm-specialized.md#option-1-upload-a-specialized-vhd)
* [既存の Azure VM の VHD をコピーする](sa-create-vm-specialized.md#option-2-copy-the-vhd-from-an-existing-azure-vm)

[!INCLUDE [updated-for-az.md](../../../includes/updated-for-az.md)]


## <a name="option-1-upload-a-specialized-vhd"></a>オプション 1:特殊化された VHD をアップロードする

Hyper-V などのオンプレミスの仮想化ツールを使用して作成された特殊化された VM、または別のクラウドからエクスポートされた VM から VHD をアップロードできます。

### <a name="prepare-the-vm"></a>VM を準備する
オンプレミス VM を使用して作成された VHD または別のクラウドからエクスポートされた VHD をアップロードできます。 特殊化された VHD では、ユーザーアカウント、アプリケーション、その他のステート データが元の VM から保持されます。 新しい VM を作成するために VHD を使用する場合、以下のステップが完了していることを確認します。 
  
  * [Windows VHD の Azure へのアップロードの準備](prepare-for-upload-vhd-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)。 Sysprep を使用して VM を一般化**しないでください**。
  * VM にインストールされたゲストの仮想化ツールやエージェント (VMware tools) の削除。
  * IP アドレスと DNS 設定を DHCP で取得するよう VM が構成されていることを確認。 これにより、サーバーが起動時に VNet 内の IP アドレスを取得します。 


### <a name="get-the-storage-account"></a>ストレージ アカウントを取得する
アップロードした VM イメージを格納するには、Azure にストレージ アカウントが必要です。 既存のストレージ アカウントを選択することも、新しいストレージ アカウントを作成することもできます。 

使用できるストレージ アカウントを表示するには、次のように入力します。

```powershell
Get-AzStorageAccount
```

既存のストレージ アカウントを使用する場合は、「VM イメージのアップロード」セクションに進みます。

ストレージ アカウントを作成する場合は、次の手順に従います。

1. ストレージ アカウントを作成するリソース グループ名が必要です。 サブスクリプションのすべてのリソース グループを検索するには、次のように入力します。
   
    ```powershell
    Get-AzResourceGroup
    ```

    **myResourceGroup** という名前のリソース グループを**米国西部**リージョンで作成するには、次のように入力します。

    ```powershell
    New-AzResourceGroup -Name myResourceGroup -Location "West US"
    ```

2. [New-AzStorageAccount](https://docs.microsoft.com/powershell/module/az.storage/new-azstorageaccount) コマンドレットを使用して、このリソース グループに **mystorageaccount** というストレージ アカウントを作成します。
   
    ```powershell
    New-AzStorageAccount -ResourceGroupName myResourceGroup -Name mystorageaccount -Location "West US" `
        -SkuName "Standard_LRS" -Kind "Storage"
    ```
   
### <a name="upload-the-vhd-to-your-storage-account"></a>ストレージ アカウントに VHD をアップロードする
[Add-AzVhd](https://docs.microsoft.com/powershell/module/az.compute/add-azvhd) コマンドレットを使用して、ストレージ アカウント内のコンテナーにイメージをアップロードします。 この例では、ファイル **myVHD.vhd** を`"C:\Users\Public\Documents\Virtual hard disks\"`から **myResourceGroup** リソース グループの **mystorageaccount** というストレージ アカウントにアップロードします。 ファイルは **mycontainer** というコンテナーに配置され、新しいファイル名は **myUploadedVHD.vhd** になります。

```powershell
$rgName = "myResourceGroup"
$urlOfUploadedImageVhd = "https://mystorageaccount.blob.core.windows.net/mycontainer/myUploadedVHD.vhd"
Add-AzVhd -ResourceGroupName $rgName -Destination $urlOfUploadedImageVhd `
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


## <a name="option-2-copy-the-vhd-from-an-existing-azure-vm"></a>オプション 2:既存の Azure VM から VHD をコピーする

別のストレージ アカウントに VHD をコピーして、新しい重複 VM を作成するときに使用できます。

### <a name="before-you-begin"></a>開始する前に
次のことを確認してください。

* **コピー元とコピー先のストレージ アカウント**に関する情報があること。 コピー元の VM については、ストレージ アカウント名とコンテナー名が必要です。 通常、コンテナー名は **vhds** になります。 また、コピー先のストレージ アカウントも持っている必要があります。 まだ持っていない場合は、ポータルを使用する ( **[すべてのサービス]** 、[ストレージ アカウント]、[追加] の順に選択する) か [New-AzStorageAccount](https://docs.microsoft.com/powershell/module/az.storage/new-azstorageaccount) コマンドレットを使用して、作成できます。 
* [AzCopy ツール](../../storage/common/storage-use-azcopy.md)をダウンロードしてインストール済みであること。 

### <a name="deallocate-the-vm"></a>VM の割り当てを解除する
VM の割り当てを解除して、コピーする VHD を解放します。 

* **ポータル**: **[仮想マシン]**  >  **[myVM]** > [停止] の順にクリックします。
* **Powershell**:[Stop-AzVM](https://docs.microsoft.com/powershell/module/az.compute/stop-azvm) を使用して、リソース グループ **myResourceGroup** 内にある **myVM** という名前の VM を停止 (割り当て解除) します。

```powershell
Stop-AzVM -ResourceGroupName myResourceGroup -Name myVM
```

Azure Portal で VM の **[状態]** が **[停止済み]** から **[停止済み (割り当て解除)]** に変わります。

### <a name="get-the-storage-account-urls"></a>ストレージ アカウントの URL を取得する
コピー元とコピー先のストレージ アカウントの URL が必要です。 URL は `https://<storageaccount>.blob.core.windows.net/<containerName>/` のようになります。 ストレージ アカウント名とコンテナー名が既にわかっている場合は、かっこで囲まれた情報を置き換えるだけで、URL を作成できます。 

Azure Portal または Azure PowerShell を使用して URL を取得できます。

* **ポータル**: **>** **[すべてのサービス]**  >  **[ストレージ アカウント]**  >  *[ストレージ アカウント]*  >  **[BLOB]** の順にクリックすると、コピー元の VHD ファイルはおそらく **vhds** コンテナー内にあります。 コンテナーの **[プロパティ]** をクリックし、**URL** というラベルのテキストをコピーします。 コピー元およびコピー先の両方のコンテナーの URL が必要です。 
* **Powershell**:[Get-AzVM](https://docs.microsoft.com/powershell/module/az.compute/get-azvm) を使用して、リソース グループ **myResourceGroup** 内にある **myVM** という名前の VM に関する情報を取得します。 その結果の **Storage profile** セクションで **Vhd Uri** を探します。 URI の最初の部分はコンテナーの URL、最後の部分は VM の OS VHD 名です。

```powershell
Get-AzVM -ResourceGroupName "myResourceGroup" -Name "myVM"
``` 

## <a name="get-the-storage-access-keys"></a>ストレージ アクセス キーを取得する
コピー元とコピー先のストレージ アカウントのアクセス キーを探します。 アクセス キーの詳細については、「 [Azure ストレージ アカウントについて](../../storage/common/storage-create-storage-account.md)」を参照してください。

* **ポータル**: **[すべてのサービス]**  >  **[ストレージ アカウント]**  >  *[ストレージ アカウント]*  >  **[アクセス キー]** の順にクリックします。 **key1**としてラベル付されているキーをコピーします。
* **Powershell**:[Get-AzStorageAccountKey](https://docs.microsoft.com/powershell/module/az.storage/get-azstorageaccountkey) を使用して、リソース グループ **myResourceGroup** 内にあるストレージ アカウント **mystorageaccount** のストレージ キーを取得します。 **key1** のラベルが付いているキーをコピーします。

```powershell
Get-AzStorageAccountKey -Name mystorageaccount -ResourceGroupName myResourceGroup
```

### <a name="copy-the-vhd"></a>VHD をコピーする
AzCopy を使用して、ストレージ アカウント間でファイルをコピーすることができます。 コピー先のコンテナーについては、指定されたコンテナーが存在しない場合に作成されます。 

AzCopy を使用するには、ローカル コンピューターでコマンド ウィンドウを開き、AzCopy がインストールされているフォルダーに移動します。 これは、*C:\Program Files (x86)\Microsoft SDKs\Azure\AzCopy* のようになります。 

コンテナー内のすべてのファイルをコピーするには、 **/S** スイッチを使用します。 OS VHD とすべてのデータ ディスクが同じコンテナーにある場合は、これを使用してそれらをコピーできます。 この例では、**mysourcestorageaccount** ストレージ アカウントのコンテナー **mysourcecontainer** 内にあるすべてのファイルを、**mydestinationstorageaccount** ストレージ アカウントのコンテナー **mydestinationcontainer** にコピーする方法を示します。 ストレージ アカウントとコンテナーの名前は、実際の名前に置き換えてください。 `<sourceStorageAccountKey1>` と `<destinationStorageAccountKey1>` は、実際のキーに置き換えてください。

```
AzCopy /Source:https://mysourcestorageaccount.blob.core.windows.net/mysourcecontainer `
    /Dest:https://mydestinationatorageaccount.blob.core.windows.net/mydestinationcontainer `
    /SourceKey:<sourceStorageAccountKey1> /DestKey:<destinationStorageAccountKey1> /S
```

複数のファイルが含まれているコンテナー内にある特定の VHD のみをコピーする場合は、/Pattern スイッチを使用してファイル名を指定することもできます。 この例では、**myFileName.vhd** という名前のファイルのみがコピーされます。

```
AzCopy /Source:https://mysourcestorageaccount.blob.core.windows.net/mysourcecontainer `
  /Dest:https://mydestinationatorageaccount.blob.core.windows.net/mydestinationcontainer `
  /SourceKey:<sourceStorageAccountKey1> /DestKey:<destinationStorageAccountKey1> `
  /Pattern:myFileName.vhd
```


完了すると、次のようなメッセージが表示されます。

```
Finished 2 of total 2 file(s).
[2016/10/07 17:37:41] Transfer summary:
-----------------
Total files transferred: 2
Transfer successfully:   2
Transfer skipped:        0
Transfer failed:         0
Elapsed time:            00.00:13:07
```

### <a name="troubleshooting"></a>トラブルシューティング
* AZCopy の使用時に、[サーバーが要求の認証に失敗しました] というエラーが発生した場合は、Authorization ヘッダーの値が署名を含む正しい形式であることを確認します。 Key 2 (セカンダリ ストレージ キー) を使用している場合は、プライマリ ストレージ キー (最初のストレージ キー) を使ってみてください。

## <a name="create-the-new-vm"></a>新しい VM を作成する 

新しい VM によって使用されるネットワークおよびその他の VM のリソースを作成する必要があります。

### <a name="create-the-subnet-and-vnet"></a>サブネットと vNet の作成

[仮想ネットワーク](../../virtual-network/virtual-networks-overview.md)の vNet とサブネットを作成します。

1. サブネットを作成します。 この例では、**mySubNet** という名前のサブネットをリソース グループ **myResourceGroup** に作成し、サブネットのアドレス プレフィックスを **10.0.0.0/24** に設定します。
   
    ```powershell
    $rgName = "myResourceGroup"
    $subnetName = "mySubNet"
    $singleSubnet = New-AzVirtualNetworkSubnetConfig -Name $subnetName -AddressPrefix 10.0.0.0/24
    ```
2. vNet を作成します。 この例では、仮想ネットワーク名を **myVnetName**、場所を **米国西部**、仮想ネットワークのアドレス プレフィックスを **10.0.0.0/16** に設定します。 
   
    ```powershell
    $location = "West US"
    $vnetName = "myVnetName"
    $vnet = New-AzVirtualNetwork -Name $vnetName -ResourceGroupName $rgName -Location $location `
        -AddressPrefix 10.0.0.0/16 -Subnet $singleSubnet
    ```    
   ### <a name="create-the-network-security-group-and-an-rdp-rule"></a>ネットワーク セキュリティ グループと RDP 規則の作成
   RDP を使用して VM にログインできるようにするには、ポート 3389 で RDP アクセスを許可するセキュリティ規則が必要です。 新しい VM の VHD は既存の特殊化された VM から作成されたため、VM が作成された後は、RDP を使用してログオンするアクセス許可が付与されていたソースの仮想マシンから既存のアカウントを使用できます。
   関連付けられるネットワーク インターフェイスを作成する前に、これを完了する必要があります。  
   この例では、NSG の名前を **myNsg** に設定し、RDP 規則の名前を **myRdpRule** に設定します。

```powershell
$nsgName = "myNsg"

$rdpRule = New-AzNetworkSecurityRuleConfig -Name myRdpRule -Description "Allow RDP" `
    -Access Allow -Protocol Tcp -Direction Inbound -Priority 110 `
    -SourceAddressPrefix Internet -SourcePortRange * `
    -DestinationAddressPrefix * -DestinationPortRange 3389
$nsg = New-AzNetworkSecurityGroup -ResourceGroupName $rgName -Location $location `
    -Name $nsgName -SecurityRules $rdpRule
    
```

エンドポイントと NSG の規則について詳しくは、[PowerShell を使用した Azure の VM へのポートの開放](nsg-quickstart-powershell.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)に関するページをご覧ください。

### <a name="create-a-public-ip-address-and-nic"></a>パブリック IP アドレスと NIC の作成
仮想ネットワークでの仮想マシンとの通信を有効にするには、 [パブリック IP アドレス](../../virtual-network/virtual-network-ip-addresses-overview-arm.md) とネットワーク インターフェイスが必要です。

1. パブリック IP を作成します。 この例では、パブリック IP アドレス名を **myIP** に設定しています。
   
    ```powershell
    $ipName = "myIP"
    $pip = New-AzPublicIpAddress -Name $ipName -ResourceGroupName $rgName -Location $location `
        -AllocationMethod Dynamic
    ```       
2. NIC を作成します。 この例では、NIC 名を **myNicName** に設定しています。 また、この手順では、この NIC で以前に作成したネットワーク セキュリティ グループを利用します。
   
    ```powershell
    $nicName = "myNicName"
    $nic = New-AzNetworkInterface -Name $nicName -ResourceGroupName $rgName `
    -Location $location -SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $pip.Id -NetworkSecurityGroupId $nsg.Id
    ```

### <a name="set-the-vm-name-and-size"></a>VM の名前とサイズの設定

この例では、VM 名を "myVM" に、VM のサイズを "Standard_A2" に設定します。
```powershell
$vmName = "myVM"
$vmConfig = New-AzVMConfig -VMName $vmName -VMSize "Standard_A2"
```

### <a name="add-the-nic"></a>NIC の追加
    
```powershell
$vm = Add-AzVMNetworkInterface -VM $vmConfig -Id $nic.Id
```
    
    
### <a name="configure-the-os-disk"></a>OS ディスクの構成

1. アップロードまたはコピーする VHD の URI を設定します。 この例では、**myOsDisk.vhd** という名前の VHD ファイルが **myContainer** というコンテナー内の **myStorageAccount** というストレージ アカウントに保持されています。

    ```powershell
    $osDiskUri = "https://myStorageAccount.blob.core.windows.net/myContainer/myOsDisk.vhd"
    ```
2. OS ディスクを追加します。 この例では、OS ディスクの作成時に、"osDisk" という語句を VM 名に追加して OS ディスクの名前にしています。 また、この例では、この Windows ベース VHD を OS ディスクとして VM に接続するように指定しています。
    
    ```powershell
    $osDiskName = $vmName + "osDisk"
    $vm = Set-AzVMOSDisk -VM $vm -Name $osDiskName -VhdUri $osDiskUri -CreateOption attach -Windows
    ```

省略可能:VM に接続する必要のあるデータ ディスクがある場合は、データ VHD の URL と、適切な論理ユニット番号 (Lun) を使用してデータ ディスクを追加します。

```powershell
$dataDiskName = $vmName + "dataDisk"
$vm = Add-AzVMDataDisk -VM $vm -Name $dataDiskName -VhdUri $dataDiskUri -Lun 1 -CreateOption attach
```

ストレージ アカウントを使用する場合、データ ディスクおよびオペレーティング システム ディスクの URL は `https://StorageAccountName.blob.core.windows.net/BlobContainerName/DiskName.vhd` のようになります。 ポータルでこれを見つけるには、ターゲット ストレージ コンテナーを参照し、コピーしたオペレーティング システムまたはデータ VHD をクリックして、URL の内容をコピーします。


### <a name="complete-the-vm"></a>VM を完了する 

先ほど作成した構成を使用して VM を作成します。

```powershell
#Create the new VM
New-AzVM -ResourceGroupName $rgName -Location $location -VM $vm
```

このコマンドが成功した場合は、次のような出力が表示されます。

```powershell
RequestId IsSuccessStatusCode StatusCode ReasonPhrase
--------- ------------------- ---------- ------------
                         True         OK OK   

```

### <a name="verify-that-the-vm-was-created"></a>VM 作成の確認
新しく作成された VM は、[Azure Portal](https://portal.azure.com)の **[すべてのサービス]** の >  **[仮想マシン]** 、または次の PowerShell コマンドで確認できます。

```powershell
$vmList = Get-AzVM -ResourceGroupName $rgName
$vmList.Name
```

## <a name="next-steps"></a>次の手順
新しい仮想マシンにサインインします。 詳しくは、「[Windows が実行されている Azure 仮想マシンに接続してログオンする方法](connect-logon.md)」をご覧ください。

