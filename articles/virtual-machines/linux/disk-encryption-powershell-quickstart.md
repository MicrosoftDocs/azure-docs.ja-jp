---
title: Azure PowerShell を使用して Linux VM を作成、暗号化する
description: このクイックスタートでは、Azure PowerShell を使用して Linux 仮想マシンを作成および暗号化する方法について説明します
author: msmbaldwin
ms.author: mbaldwin
ms.service: virtual-machines
ms.subservice: disks
ms.collection: linux
ms.topic: quickstart
ms.date: 05/17/2019
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 1833b25554df53a480af9a8fc643755dbc2bbdb8
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102564681"
---
# <a name="quickstart-create-and-encrypt-a-linux-vm-in-azure-with-azure-powershell"></a>クイックスタート: Azure PowerShell を使用して Azure で Linux VM を作成、暗号化する

Azure PowerShell モジュールは、PowerShell コマンド ラインやスクリプトで Azure リソースを作成および管理するために使用します。 このクイックスタートでは、Azure PowerShell モジュールを使用して Linux 仮想マシン (VM) を作成し、暗号化キーを格納するためのキー コンテナーを作成し、VM を暗号化する方法を説明します。 このクイックスタートでは、Canonical の Ubuntu 16.04 LTS マーケットプレース イメージと VM Standard_D2S_V3 サイズを使用します。 

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。

## <a name="create-a-resource-group"></a>リソース グループを作成する

[New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) を使用して Azure リソース グループを作成します。 リソース グループとは、Azure リソースの展開と管理に使用する論理コンテナーです。

```azurepowershell-interactive
New-AzResourceGroup -Name "myResourceGroup" -Location "EastUS"
```

## <a name="create-a-virtual-machine"></a>仮想マシンの作成

[New-AzVM](/powershell/module/az.compute/new-azvm) を使用して Azure 仮想マシンを作成し、前に作成した VM 構成オブジェクトを渡します。

```powershell-interactive
$cred = Get-Credential

New-AzVM -Name MyVm -Credential $cred -ResourceGroupName MyResourceGroup -Image Canonical:UbuntuServer:18.04-LTS:latest -Size Standard_D2S_V3
```

VM がデプロイされるまでに数分かかります。 

## <a name="create-a-key-vault-configured-for-encryption-keys"></a>暗号化キー用に構成されたキー コンテナーの作成

Azure Disk Encryption では、その暗号化キーは Azure キー コンテナーに格納されます。 [New-AzKeyvault](/powershell/module/az.keyvault/new-azkeyvault) を使用して、キー コンテナーを作成します。 キー コンテナーで暗号化キーを格納できるようにするには、-EnabledForDiskEncryption パラメーターを使用します。

> [!Important]
> すべてのキー コンテナーに、Azure 全体で一意の名前を付ける必要があります。 次の例の <your-unique-keyvault-name> を任意の名前に置き換えてください。

```azurepowershell-interactive
New-AzKeyvault -name "<your-unique-keyvault-name>" -ResourceGroupName "myResourceGroup" -Location EastUS -EnabledForDiskEncryption
```

## <a name="encrypt-the-virtual-machine"></a>仮想マシンを暗号化する

[Set-AzVmDiskEncryptionExtension](/powershell/module/az.compute/set-azvmdiskencryptionextension) を使用して、VM を暗号化します。 

Set-AzVmDiskEncryptionExtension では、Key Vault オブジェクトのいくつかの値が必要です。 これらの値を取得するには、[Get-AzKeyvault](/powershell/module/az.keyvault/get-azkeyvault) にキー コンテナーの一意の名前を渡します。

```azurepowershell-interactive
$KeyVault = Get-AzKeyVault -VaultName "<your-unique-keyvault-name>" -ResourceGroupName "MyResourceGroup"

Set-AzVMDiskEncryptionExtension -ResourceGroupName MyResourceGroup -VMName "MyVM" -DiskEncryptionKeyVaultUrl $KeyVault.VaultUri -DiskEncryptionKeyVaultId $KeyVault.ResourceId -SkipVmBackup -VolumeType All
```

数分後に、プロセスから次の内容が返されます。

```
RequestId IsSuccessStatusCode StatusCode ReasonPhrase
--------- ------------------- ---------- ------------
                         True         OK OK
```

暗号化プロセスを検証するには、[Get-AzVmDiskEncryptionStatus](/powershell/module/az.compute/Get-AzVMDiskEncryptionStatus) を実行します。

```azurepowershell-interactive
Get-AzVmDiskEncryptionStatus -VMName MyVM -ResourceGroupName MyResourceGroup
```

暗号化が有効な場合、返された出力には以下が表示されます。

```
OsVolumeEncrypted          : EncryptionInProgress
DataVolumesEncrypted       : NotMounted
OsVolumeEncryptionSettings : Microsoft.Azure.Management.Compute.Models.DiskEncryptionSettings
ProgressMessage            : OS disk encryption started
```

## <a name="clean-up-resources"></a>リソースをクリーンアップする

必要がなくなったら、[Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) コマンドレットを使用して、リソース グループ、VM、およびすべての関連リソースを削除できます。

```azurepowershell-interactive
Remove-AzResourceGroup -Name "myResourceGroup"
```

## <a name="next-steps"></a>次のステップ

このクイックスタートでは、仮想マシンを作成し、暗号化キー用に有効にされたキー コンテナーを作成し、VM を暗号化しました。  次の記事に進み、Linux VM に対する Azure Disk Encryption について詳しく学習してください。

> [!div class="nextstepaction"]
> [Azure Disk Encryption の概要](disk-encryption-overview.md)
