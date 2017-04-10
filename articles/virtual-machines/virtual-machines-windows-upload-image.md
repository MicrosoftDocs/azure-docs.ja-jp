---
title: "リソース マネージャー向けに Windows VM をアップロードする | Microsoft Docs"
description: "Resource Manager デプロイメント モデルを使用して、Windows の仮想マシンのVHD をオンプレミスから Azure へアップロードする方法を説明します。 VHD は、一般化された VM または特殊化された VM からアップロードできます。"
services: virtual-machines-windows
documentationcenter: 
author: cynthn
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: 192c8e5a-3411-48fe-9fc3-526e0296cf4c
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 03/06/2017
ms.author: cynthn
translationtype: Human Translation
ms.sourcegitcommit: 24d86e17a063164c31c312685c0742ec4a5c2f1b
ms.openlocfilehash: 28a6858729adbef439625864abc2fd7aa77fab35
ms.lasthandoff: 03/11/2017


---
# <a name="upload-a-windows-vhd-from-an-on-premises-vm-to-azure"></a>Windows VHD をオンプレミスの VM から Azure にアップロードする
この記事では、Azure VM を作成するときに使用する Windows の仮想ハード ディスク (VHD) を作成してアップロードする方法を示します。 VHD は、一般化された VM からでも特殊化された VM からでもアップロードできます。 

管理ディスクを使用して新しい VM の準備、アップロード、作成を行う方法の詳しいチュートリアルについては、「[Azure にアップロードされた一般化 VHD から Managed Disks を使用する新しい VM を作成する](virtual-machines-windows-upload-generalized-managed.md)」または[特殊化した VHD をアップロードして Azure に VM を作成する方法](virtual-machines-windows-upload-specialized.md)に関するページを参照してください。

Azure でのディスクと VHD の詳細については、「[仮想マシン用のディスクと VHD について](../storage/storage-about-disks-and-vhds-windows.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)」をご覧ください。

## <a name="prepare-the-vm"></a>VM を準備する
一般化された VHD と特殊化された VHD のいずれも Azure にアップロードできます。 どちらのタイプも、始める前に VM を準備する必要があります。

* **一般化された VHD** - 一般化した VHD では、Sysprep を使用して個人アカウント情報がすべて削除されています。 新しい VM を作成するためのイメージとして VHD を使用する場合、以下を行う必要があります。
  
  * [Windows VHD の Azure へのアップロードの準備](virtual-machines-windows-prepare-for-upload-vhd-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)。 
  * [Sysprep を使用した仮想マシンの一般化](virtual-machines-windows-generalize-vhd.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)。 
* **特殊化された VHD** - 特殊化された VHD では、ユーザーアカウント、アプリケーション、その他のステート データが元の VM から保持されます。 新しい VM を作成するために VHD を使用する場合、以下のステップが完了していることを確認します。 
  
  * [Windows VHD の Azure へのアップロードの準備](virtual-machines-windows-prepare-for-upload-vhd-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)。 Sysprep を使用して VM を一般化**しないでください**。
  * VM にインストールされたゲストの仮想化ツールやエージェント (VMware tools) の削除。
  * IP アドレスと DNS 設定を DHCP で取得するよう VM が構成されていることを確認。 これにより、サーバーが起動時に VNet 内の IP アドレスを取得します。 

## <a name="log-in-to-azure"></a>Azure へのログイン
PowerShell バージョン 1.4 以降がまだインストールされていない場合は、「[Azure PowerShell のインストールおよび構成方法](/powershell/azureps-cmdlets-docs)」をご覧ください。

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
[Add-AzureRmVhd](https://msdn.microsoft.com/library/mt603554.aspx) コマンドレットを使用して、ストレージ アカウント内のコンテナーにイメージをアップロードします。 この例では、ファイル **myVHD.vhd** を`"C:\Users\Public\Documents\Virtual hard disks\"`から **myResourceGroup** リソース グループの **mystorageaccount** というストレージ アカウントにアップロードします。 ファイルは **mycontainer** というコンテナーに配置され、新しいファイル名は **myUploadedVHD.vhd** になります。

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

## <a name="next-steps"></a>次のステップ
* [一般化された VHD から Azure で VM を作成する](virtual-machines-windows-create-vm-generalized.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* 新しい VM を作成するときに VM をOS ディスクとして添付して[特殊化された VHD から Azure で VM を作成する](virtual-machines-windows-create-vm-specialized.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)


