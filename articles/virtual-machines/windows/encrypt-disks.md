---
title: Azure で Windows VM のディスクを暗号化する | Microsoft Docs
description: セキュリティを強化するために Azure PowerShell を使用して Windows VM の仮想ディスクを暗号化する
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 10/30/2018
ms.author: cynthn
ms.openlocfilehash: cc0eee9dc36878f7a02b97453c859d94ea99b901
ms.sourcegitcommit: c712cb5c80bed4b5801be214788770b66bf7a009
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/01/2019
ms.locfileid: "57217140"
---
# <a name="encrypt-virtual-disks-on-a-windows-vm"></a>Windows VM の仮想ディスクを暗号化する
仮想マシン (VM) のセキュリティとコンプライアンスを強化するために、Azure の仮想ディスクを暗号化できます。 ディスクは、Azure Key Vault で保護されている暗号化キーを使って暗号化されます。 これらの暗号化キーを制御し、その使用を監査することができます。 この記事では、Azure PowerShell を使用して Windows VM の仮想ディスクを暗号化する方法について説明します。 [Azure CLI を使って Linux VM のディスクを暗号化する](../linux/encrypt-disks.md)こともできます。

[!INCLUDE [updated-for-az-vm.md](../../../includes/updated-for-az-vm.md)]

## <a name="overview-of-disk-encryption"></a>ディスク暗号化の概要
Windows VM の仮想ディスクは、BitLocker を使って保存時に暗号化されます。 Azure の仮想ディスクを暗号化するための料金はかかりません。 暗号化キーは、ソフトウェア保護を使って Azure Key Vault に格納されます。または、FIPS 140-2 レベル 2 標準に認定された Hardware Security Module (HSM) でキーをインポートまたは生成することもできます。 暗号化キーは、VM に接続された仮想ディスクの暗号化/暗号化解除に使われます。 これらの暗号化キーの制御を維持し、その使用を監査することができます。 

VM 暗号化のプロセスは次のとおりです。

1. Azure Key Vault で暗号化キーを作成します。
1. ディスクの暗号化に使うことができるように暗号化キーを構成します。
1. 仮想ディスクのディスク暗号化を有効にします。
1. Azure Key Vault から必要な暗号化キーが要求されます。
1. 提供された暗号化キーを使って仮想ディスクが暗号化されます。


## <a name="requirements-and-limitations"></a>要件と制限

ディスク暗号化のサポートされるシナリオと要件:

* Azure Marketplace イメージまたはカスタムの VHD イメージから新しい Windows VM の暗号化を有効にします。
* Azure で既存の Windows VM の暗号化を有効にします。
* ストレージ スペースを使用して構成されている Windows VM の暗号化を有効にします。
* Windows VM の OS およびデータ ドライブの暗号化を無効にします。
* A、D、DS、G、G シリーズ VM などのStandard レベルの VM。

    > [!NOTE]
    > すべてのリソース (Key Vault、ストレージ アカウント、VM など) は、同じ Azure リージョンとサブスクリプションに存在している必要があります。

現在、ディスクの暗号化は次のシナリオではサポートされていません。

* Basic レベルの VM。
* クラシック デプロイ モデルを使用して作成された VM。
* 既に暗号化されている VM での暗号化キーの更新。
* オンプレミス キー管理サービスとの統合。


## <a name="create-an-azure-key-vault-and-keys"></a>Azure Key Vault とキーを作成する
開始する前に、最新バージョンの Azure PowerShell モジュールがインストールされていることを確認してください。 詳細については、「 [Azure PowerShell のインストールと構成の方法](/powershell/azure/overview)」を参照してください。 次のコマンド例のすべてのパラメーター例は、*myResourceGroup*、*myKeyVault*、*myVM* など、独自の名前、場所、およびキーの値に置き換えます。

最初に、暗号化キーを格納する Azure Key Vault を作成します。 Azure Key Vault は、キー、シークレット、パスワードを格納して、アプリケーションとサービスに安全に実装できるようにします。 仮想ディスクの暗号化では、仮想ディスクの暗号化または暗号化解除に使われる暗号化キーを格納するためにキー コンテナーを作成します。 

[Register-AzResourceProvider](https://docs.microsoft.com/powershell/module/az.resources/register-azresourceprovider) を使用して Azure サブスクリプションで Azure Key Vault プロバイダーを有効にし、[New-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup) を使用してリソース グループを作成します。 次の例では、リソース グループ名 *myResourceGroup* を場所 *East US* に作成します。

```azurepowershell-interactive
$rgName = "myResourceGroup"
$location = "East US"

Register-AzResourceProvider -ProviderNamespace "Microsoft.KeyVault"
New-AzResourceGroup -Location $location -Name $rgName
```

暗号化キーおよびストレージや VM 自体などの関連するコンピューティング リソースを格納する Azure Key Vault は、すべて同じリージョンに存在する必要があります。 [New-AzKeyVault](https://docs.microsoft.com/powershell/module/az.keyvault/new-azkeyvault) を使用して Azure Key Vault を作成し、ディスクの暗号化で使用するために Key Vault を有効にします。 次のように、*keyVaultName* に一意の Key Vault 名を指定します。

```azurepowershell-interactive
$keyVaultName = "myKeyVault$(Get-Random)"
New-AzKeyVault -Location $location `
    -ResourceGroupName $rgName `
    -VaultName $keyVaultName `
    -EnabledForDiskEncryption
```

ソフトウェアまたはハードウェア セキュリティ モデル (HSM) の保護を使って、暗号化キーを格納できます。  Standard Key Vault には、ソフトウェアで保護されたキーのみが格納されます。 HSM を使うには、追加料金で Premium Key Vault を作成する必要があります。 Premium Key Vault を作成するには、前の手順で *-Sku "Premium"* パラメーターを追加します。 ここでは Standard Key Vault を作成したので、次の例ではソフトウェアで保護されたキーを使います。 

どちらの保護モデルでも、VM が起動して仮想ディスクを復号化するときに、Azure プラットフォームは暗号化キーを要求するためのアクセスを許可される必要があります。 [Add-AzureKeyVaultKey](https://docs.microsoft.com/powershell/module/az.keyvault/add-azkeyvaultkey) を使用して、Key Vault に暗号化キーを作成します。 次の例では、*myKey* という名前のキーを作成します。

```azurepowershell-interactive
Add-AzureKeyVaultKey -VaultName $keyVaultName `
    -Name "myKey" `
    -Destination "Software"
```

## <a name="create-a-virtual-machine"></a>仮想マシンの作成
暗号化プロセスをテストするには、[New-AzVm](https://docs.microsoft.com/powershell/module/az.compute/new-azvm) で VM を作成します。 次の例では、*Windows Server 2016 Datacenter* イメージを使用して、*myVM* という名前の VM を作成します。 資格情報を求められたら、VM に使用するユーザー名とパスワードを入力します。

```azurepowershell-interactive
$cred = Get-Credential

New-AzVm `
    -ResourceGroupName $rgName `
    -Name "myVM" `
    -Location $location `
    -VirtualNetworkName "myVnet" `
    -SubnetName "mySubnet" `
    -SecurityGroupName "myNetworkSecurityGroup" `
    -PublicIpAddressName "myPublicIpAddress" `
    -Credential $cred
```


## <a name="encrypt-a-virtual-machine"></a>仮想マシンの暗号化
[Set-AzVMDiskEncryptionExtension](https://docs.microsoft.com/powershell/module/az.compute/set-azvmdiskencryptionextension) を使用し、Azure Key Vault キーを指定して VM を暗号化します。 次の例は、すべてのキー情報を取得した後、*myVM* という名前の VM を暗号化します。

```azurepowershell-interactive
$keyVault = Get-AzKeyVault -VaultName $keyVaultName -ResourceGroupName $rgName;
$diskEncryptionKeyVaultUrl = $keyVault.VaultUri;
$keyVaultResourceId = $keyVault.ResourceId;
$keyEncryptionKeyUrl = (Get-AzKeyVaultKey -VaultName $keyVaultName -Name myKey).Key.kid;

Set-AzVMDiskEncryptionExtension -ResourceGroupName $rgName `
    -VMName "myVM" `
    -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl `
    -DiskEncryptionKeyVaultId $keyVaultResourceId `
    -KeyEncryptionKeyUrl $keyEncryptionKeyUrl `
    -KeyEncryptionKeyVaultId $keyVaultResourceId
```

VM の暗号化を続行するプロンプトを受け入れます。 この処理中に VM が再起動します。 暗号化処理が完了し、VM が再起動されたら、[Get-AzVmDiskEncryptionStatus](https://docs.microsoft.com/powershell/module/az.compute/get-azvmdiskencryptionstatus) を使用して暗号化の状態を確認します。

```azurepowershell-interactive
Get-AzVmDiskEncryptionStatus  -ResourceGroupName $rgName -VMName "myVM"
```

出力は次の例のようになります。

```azurepowershell-interactive
OsVolumeEncrypted          : Encrypted
DataVolumesEncrypted       : Encrypted
OsVolumeEncryptionSettings : Microsoft.Azure.Management.Compute.Models.DiskEncryptionSettings
ProgressMessage            : OsVolume: Encrypted, DataVolumes: Encrypted
```

## <a name="next-steps"></a>次の手順
* Azure Key Vault の管理の詳細については、[仮想マシンの Key Vault の設定](key-vault-setup.md)に関するページを参照してください。
* 暗号化されたカスタム VM を Azure にアップロードするための準備など、ディスクの暗号化について詳しくは、「[Azure Disk Encryption](../../security/azure-security-disk-encryption.md)」をご覧ください。
