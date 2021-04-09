---
title: Azure PowerShell による Azure スケール セットのディスクの暗号化
description: Azure PowerShell を使用して、Windows 仮想マシン スケール セット内の VM インスタンスと接続ディスクを暗号化する方法について説明します。
author: ju-shim
ms.author: jushiman
ms.topic: how-to
ms.service: virtual-machine-scale-sets
ms.subservice: disks
ms.date: 10/15/2019
ms.reviewer: mimckitt
ms.custom: mimckitt, devx-track-azurepowershell
ms.openlocfilehash: 59b015232fcfe2fd98e1b9f28c2eb4fa86606049
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102519573"
---
# <a name="encrypt-os-and-attached-data-disks-in-a-virtual-machine-scale-set-with-azure-powershell"></a>Azure PowerShell を使用した仮想マシン スケール セットの OS および接続されているデータ ディスクの暗号化

Azure PowerShell モジュールは、PowerShell コマンド ラインやスクリプトで Azure リソースを作成および管理するために使用します。  この記事では、Azure PoweShell を使用して、仮想マシン スケール セットを作成し、暗号化する方法について説明します。 仮想マシン スケール セットへの Azure Disk Encryption の適用の詳細については、「[仮想マシン スケール セットの Azure Disk Encryption](disk-encryption-overview.md)」を参照してください。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-an-azure-key-vault-enabled-for-disk-encryption"></a>ディスクの暗号化を有効にした Azure Key Vault を作成する

Azure Key Vault は、キー、シークレット、パスワードを格納して、アプリケーションとサービスに安全に実装できるようにします。 暗号化キーは、ソフトウェア保護を使って Azure Key Vault に格納されます。または、FIPS 140-2 レベル 2 標準に認定された Hardware Security Module (HSM) でキーをインポートまたは生成することもできます。 これらの暗号化キーは、VM に接続された仮想ディスクの暗号化/暗号化解除に使われます。 これらの暗号化キーの制御を維持し、その使用を監査することができます。

[New-AzKeyVault](/powershell/module/az.keyvault/new-azkeyvault) を使用して Key Vault を作成します。 ディスク暗号化に Key Vault を使用できるようにするには、*EnabledForDiskEncryption* パラメーターを設定します。 次の例では、リソース グループ名、Key Vault 名、および場所の変数も定義しています。 独自の Key Vault 名を指定します。

```azurepowershell-interactive
$rgName="myResourceGroup"
$vaultName="myuniquekeyvault"
$location = "EastUS"

New-AzResourceGroup -Name $rgName -Location $location
New-AzKeyVault -VaultName $vaultName -ResourceGroupName $rgName -Location $location -EnabledForDiskEncryption
```

### <a name="use-an-existing-key-vault"></a>既存の Key Vault を使用する

この手順は、ディスク暗号化で使用する既存の Key Vault がある場合にのみ必要です。 前のセクションで Key Vault を作成した場合は、この手順をスキップしてください。

[Set-AzKeyVaultAccessPolicy](/powershell/module/az.keyvault/Set-AzKeyVaultAccessPolicy) を使用して、ディスク暗号化のスケール セットと同じサブスクリプションとリージョン内の既存の Key Vault を有効にできます。 次のように、*$vaultName* 変数で既存の Key Vault 名を定義します。


```azurepowershell-interactive
$vaultName="myexistingkeyvault"
Set-AzKeyVaultAccessPolicy -VaultName $vaultName -EnabledForDiskEncryption
```

## <a name="create-a-scale-set"></a>スケール セットを作成する

まず、[Get-Credential](/powershell/module/microsoft.powershell.security/get-credential) を使用して、VM インスタンスの管理者のユーザー名とパスワードを設定します。

```azurepowershell-interactive
$cred = Get-Credential
```

次に、[New-AzVmss](/powershell/module/az.compute/new-azvmss) を使用して仮想マシン スケール セットを作成します。 個々の VM インスタンスにトラフィックを分散するために、ロード バランサーも作成されます。 ロード バランサーには、TCP ポート 80 上のトラフィックを分散するルールだけでなく、TCP ポート 3389 上のリモート デスクトップ トラフィックと TCP ポート 5985 上の PowerShell リモート処理を許可するルールも含まれています。

```azurepowershell-interactive
$vmssName="myScaleSet"

New-AzVmss `
    -ResourceGroupName $rgName `
    -VMScaleSetName $vmssName `
    -Location $location `
    -VirtualNetworkName "myVnet" `
    -SubnetName "mySubnet" `
    -PublicIpAddressName "myPublicIPAddress" `
    -LoadBalancerName "myLoadBalancer" `
    -UpgradePolicy "Automatic" `
    -Credential $cred
```

## <a name="enable-encryption"></a>暗号化を有効にする

スケール セット内の VM インスタンスを暗号化するには、まず [Get-AzKeyVault](/powershell/module/az.keyvault/Get-AzKeyVault) を使用して Key Vault URI とリソース ID に関する一部の情報を取得します。 次に、[Set-AzVmssDiskEncryptionExtension](/powershell/module/az.compute/Set-AzVmssDiskEncryptionExtension) でこれらの変数を使用して、暗号化プロセスを開始します。


```azurepowershell-interactive
$diskEncryptionKeyVaultUrl=(Get-AzKeyVault -ResourceGroupName $rgName -Name $vaultName).VaultUri
$keyVaultResourceId=(Get-AzKeyVault -ResourceGroupName $rgName -Name $vaultName).ResourceId

Set-AzVmssDiskEncryptionExtension -ResourceGroupName $rgName -VMScaleSetName $vmssName `
    -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $keyVaultResourceId -VolumeType "All"
```

プロンプトが表示されたら、「*y*」と入力して、スケール セット VM インスタンス上のディスク暗号化プロセスを続行します。

### <a name="enable-encryption-using-kek-to-wrap-the-key"></a>キーをラップする KEK を使用して暗号化を有効にします。

仮想マシン スケール セットを暗号化するときに、セキュリティ強化のためキーの暗号化キーを使用することもできます。

```azurepowershell-interactive
$diskEncryptionKeyVaultUrl=(Get-AzKeyVault -ResourceGroupName $rgName -Name $vaultName).VaultUri
$keyVaultResourceId=(Get-AzKeyVault -ResourceGroupName $rgName -Name $vaultName).ResourceId
$keyEncryptionKeyUrl = (Get-AzKeyVaultKey -VaultName $vaultName -Name $keyEncryptionKeyName).Key.kid;

Set-AzVmssDiskEncryptionExtension -ResourceGroupName $rgName -VMScaleSetName $vmssName `
    -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $keyVaultResourceId `
    -KeyEncryptionKeyUrl $keyEncryptionKeyUrl -KeyEncryptionKeyVaultId $keyVaultResourceId -VolumeType "All"
```

> [!NOTE]
>  disk-encryption-keyvault パラメーターの値の構文は、完全な識別子の文字列です。</br>
/subscriptions/[subscription-id-guid]/resourceGroups/[resource-group-name]/providers/Microsoft.KeyVault/vaults/[keyvault-name]</br></br>
> disk-encryption-keyvault パラメーターの値の構文は、以下のように KEK までのフル URI です。</br>
https://[keyvault-name].vault.azure.net/keys/[kekname]/[kek-unique-id]

## <a name="check-encryption-progress"></a>暗号化の進行状況を確認する

ディスク暗号化の状態を確認するには、[Get-AzVmssDiskEncryption](/powershell/module/az.compute/Get-AzVmssDiskEncryption) を使用します。


```azurepowershell-interactive
Get-AzVmssDiskEncryption -ResourceGroupName $rgName -VMScaleSetName $vmssName
```

VM インスタンスが暗号化されると、*EncryptionSummary* コードで、次の出力例のように *ProvisioningState/succeeded* コードが報告されます。

```powershell
ResourceGroupName            : myResourceGroup
VmScaleSetName               : myScaleSet
EncryptionSettings           :
  KeyVaultURL                : https://myuniquekeyvault.vault.azure.net/
  KeyEncryptionKeyURL        :
  KeyVaultResourceId         : /subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.KeyVault/vaults/myuniquekeyvault
  KekVaultResourceId         :
  KeyEncryptionAlgorithm     :
  VolumeType                 : All
  EncryptionOperation        : EnableEncryption
EncryptionSummary[0]         :
  Code                       : ProvisioningState/succeeded
  Count                      : 2
EncryptionEnabled            : True
EncryptionExtensionInstalled : True
```

## <a name="disable-encryption"></a>暗号化を無効にする

暗号化された VM インスタンス ディスクを使用しない場合は、次のように [Disable-AzVmssDiskEncryption](/powershell/module/az.compute/Disable-AzVmssDiskEncryption) を使用して暗号化を無効にすることができます。


```azurepowershell-interactive
Disable-AzVmssDiskEncryption -ResourceGroupName $rgName -VMScaleSetName $vmssName
```

## <a name="next-steps"></a>次のステップ

- この記事では、Azure PowerShell を使用して仮想マシン スケール セットを暗号化しました。 また、[Azure CLI](disk-encryption-cli.md) や [Azure Resource Manager テンプレート](disk-encryption-azure-resource-manager.md)を使用することもできます。
- 別の拡張機能がプロビジョニングされた後で Azure Disk Encryption を適用する場合、[extension sequencing](virtual-machine-scale-sets-extension-sequencing.md)を使用できます。
