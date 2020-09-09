---
title: アップロードした汎用化 VHD から VM を作成する
description: 汎用化した VHD を Azure にアップロードし、それを使用して Resource Manager デプロイ モデルで新しい VM を作成します。
author: cynthn
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 12/12/2019
ms.author: cynthn
ms.openlocfilehash: 9d5add1349650663b226023b9f46868f242ac4f9
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/28/2020
ms.locfileid: "87265213"
---
# <a name="upload-a-generalized-vhd-and-use-it-to-create-new-vms-in-azure"></a>汎用化した VHD をアップロードして Azure で新しい VM を作成する

この記事では、汎用化した VM の VHD を PowerShell を使用して Azure にアップロードし、VHD からイメージを作成し、イメージから新しい VM を作成する方法を説明します。 オンプレミスの仮想化ツールや別のクラウドからエクスポートされた VHD をアップロードできます。 新しい VM に [Managed Disks](managed-disks-overview.md) を使用すると VM 管理が簡略化されます。また、VM を可用性セット内に配置したときの可用性が向上します。 

サンプル スクリプトの場合は、[VHD を Azure にアップロードして新しい VM を作成するサンプル スクリプト](../scripts/virtual-machines-windows-powershell-upload-generalized-script.md)を参照してください。

## <a name="before-you-begin"></a>開始する前に

- VHD を Azure にアップロードする前に、「[Prepare a Windows VHD or VHDX to upload to Azure](prepare-for-upload-vhd-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)」(Azure にアップロードする Windows VHD または VHDX を準備する) に従う必要があります。
- [Managed Disks](managed-disks-overview.md) への移行を開始する前に、「[Plan for the migration to Managed Disks (Managed Disks への移行の計画)](on-prem-to-azure.md#plan-for-the-migration-to-managed-disks)」をご確認ください。

 
## <a name="generalize-the-source-vm-by-using-sysprep"></a>Sysprep を使用してソース VM を一般化する

まだ実行していない場合、VHD を Azure にアップロードする前に VM を Sysprep で一般化する必要があります。 特に重要な点は、Sysprep がすべての個人アカウント情報を削除して、マシンをイメージとして使用できるように準備することです。 Sysprep の詳細については、[Sysprep の概要](/windows-hardware/manufacture/desktop/sysprep--system-preparation--overview)に関するページを参照してください。

コンピューター上で実行されるサーバー ロールが Sysprep でサポートされていることを確認します。 詳しくは、「 [Sysprep Support for Server Roles](/windows-hardware/manufacture/desktop/sysprep-support-for-server-roles)」 (サーバー ロールに対する Sysprep サポート) をご覧ください。

> [!IMPORTANT]
> 初めて VHD を Azure にアップロードする前に Sysprep を実行する予定の場合は、[VM の準備](prepare-for-upload-vhd-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)ができていることを確認してください。 
> 
> 

1. Windows 仮想マシンへのサインイン
2. 管理者としてコマンド プロンプト ウィンドウを開きます。 ディレクトリを `%windir%\system32\sysprep` に変更し、`sysprep.exe`を実行します。
3. **[システム準備ツール]** ダイアログ ボックスで **[システムの OOBE (Out-of-Box Experience) に入る]** を選択し、 **[一般化する]** チェック ボックスがオンになっていることを確認します。
4. **[シャットダウン オプション]** の **[シャットダウン]** を選択します。
5. **[OK]** を選択します。
   
    ![Sysprep の開始](./media/upload-generalized-managed/sysprepgeneral.png)
6. Sysprep は完了時に仮想マシンをシャットダウンします。 VM は再起動しないでください。


## <a name="upload-the-vhd"></a>VHD をアップロードする 

VHD をマネージド ディスクに直接アップロードできるようになりました。 手順については、「[Azure PowerShell を使用して Azure に VHD をアップロードする](disks-upload-vhd-to-managed-disk-powershell.md)」を参照してください。



VHD がマネージド ディスクにアップロードされたら、[Get-AzDisk](/powershell/module/az.compute/get-azdisk) を使用し、マネージド ディスクを取得する必要があります。

```azurepowershell-interactive
$disk = Get-AzDisk -ResourceGroupName 'myResourceGroup' -DiskName 'myDiskName'
```

## <a name="create-the-image"></a>イメージの作成
一般化済みの OS マネージド ディスクから管理イメージを作成します。 次の値は実際の値に変更してください。

まず、変数をいくつか設定します。

```powershell
$location = 'East US'
$imageName = 'myImage'
$rgName = 'myResourceGroup'
```

マネージド ディスクを使用してイメージを作成します。

```azurepowershell-interactive
$imageConfig = New-AzImageConfig `
   -Location $location
$imageConfig = Set-AzImageOsDisk `
   -Image $imageConfig `
   -OsState Generalized `
   -OsType Windows `
   -ManagedDiskId $disk.Id
```

イメージを作成します。

```azurepowershell-interactive
$image = New-AzImage `
   -ImageName $imageName `
   -ResourceGroupName $rgName `
   -Image $imageConfig
```

## <a name="create-the-vm"></a>VM の作成

イメージが用意できたので、イメージから新しい VM を 1 つ以上作成できます。 この例では、*myResourceGroup* で *myImage* から *myVM* という名前の VM が作成されます。


```powershell
New-AzVm `
    -ResourceGroupName $rgName `
    -Name "myVM" `
    -Image $image.Id `
    -Location $location `
    -VirtualNetworkName "myVnet" `
    -SubnetName "mySubnet" `
    -SecurityGroupName "myNSG" `
    -PublicIpAddressName "myPIP" `
    -OpenPorts 3389
```


## <a name="next-steps"></a>次のステップ

新しい仮想マシンにサインインします。 詳しくは、「[Windows が実行されている Azure 仮想マシンに接続してログオンする方法](connect-logon.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)」をご覧ください。 
