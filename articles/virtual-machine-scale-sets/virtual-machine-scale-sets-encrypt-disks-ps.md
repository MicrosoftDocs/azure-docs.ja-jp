---
title: Azure PowerShell を使用した Azure スケール セットのディスクの暗号化 | Microsoft Docs
description: Azure PowerShell を使用して、Windows 仮想マシン スケール セット内の VM インスタンスと接続ディスクを暗号化する方法について説明します。
services: virtual-machine-scale-sets
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/30/2018
ms.author: cynthn
ms.openlocfilehash: cc1405d2dd972aff6091a9d5b60ff9da18185286
ms.sourcegitcommit: 943af92555ba640288464c11d84e01da948db5c0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/09/2019
ms.locfileid: "55978104"
---
# <a name="encrypt-os-and-attached-data-disks-in-a-virtual-machine-scale-set-with-azure-powershell-preview"></a>Azure PowerShell (プレビュー) を使用した仮想マシン スケール セットの OS および接続されているデータ ディスクの暗号化

業界標準の暗号化テクノロジを使用して保存データを保護および防御するために、仮想マシン スケール セットは Azure Disk Encryption (ADE) をサポートしています。 Windows および Linux の仮想マシン スケール セットで暗号化を有効にすることができます。 詳細については、[Windows 用および Linux 用の Azure Disk Encryption](../security/azure-security-disk-encryption.md) に関するページを参照してください。

> [!NOTE]
>  仮想マシン スケール セット用 Azure Disk Encryption は、現在パブリック プレビュー段階であり、すべての Azure パブリック リージョンで利用できます。

Azure Disk Encryption は次の場合にサポートされます。
- マネージド ディスクで作成されたスケール セット。ネイティブ (または管理対象ではない) ディスク スケール セットの場合はサポートされません。
- Windows スケール セット内の OS とデータ ボリューム。 Windows スケール セットの OS とデータ ボリュームでは、暗号化の無効化がサポートされています。
- Linux スケール セット内のデータ ボリューム。 Linux スケール セット用の現在のプレビューでは、OS ディスクの暗号化はサポートされていません。

現在のプレビューでは、スケール セット VM の再イメージ操作とアップグレード操作はサポートされていません。 仮想マシンのスケール セット プレビューの Azure Disk Encryption は、テスト環境でのみ推奨されます。 プレビューでは、暗号化スケール セット内の OS イメージをアップグレードする必要のある運用環境でディスクの暗号化を有効にしないでください。

[!INCLUDE [updated-for-az-vm.md](../../includes/updated-for-az-vm.md)]

[!INCLUDE [cloud-shell-powershell.md](../../includes/cloud-shell-powershell.md)]


## <a name="register-for-disk-encryption-preview"></a>ディスク暗号化プレビューの登録をする

仮想マシン スケール セットのプレビューのための Azure ディスク暗号化では、[Register-AzProviderFeature](/powershell/module/az.resources/register-azproviderfeature) を使用してサブスクリプションを自己登録する必要があります。 ディスク暗号化のプレビュー機能を初めて利用する場合は、以下の手順を行えばよいだけです｡

```azurepowershell-interactive
Register-AzProviderFeature -ProviderNamespace Microsoft.Compute -FeatureName "UnifiedDiskEncryption"
```


登録要求が伝達されるのに最大 10 分時間がかかることがあります｡ 登録状態は、[Get-AzProviderFeature](/powershell/module/az.resources/Get-AzProviderFeature) を使用して確認できます。 `RegistrationState` から *Registered* と報告されたら、[Register-AzResourceProvider](/powershell/module/az.resources/Register-AzResourceProvider) を使用して *Microsoft.Compute* プロバイダーを再登録します。


```azurepowershell-interactive
Get-AzProviderFeature -ProviderNamespace "Microsoft.Compute" -FeatureName "UnifiedDiskEncryption"
Register-AzResourceProvider -ProviderNamespace Microsoft.Compute
```

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

まず、[Get-Credential](https://msdn.microsoft.com/powershell/reference/5.1/microsoft.powershell.security/Get-Credential) を使用して、VM インスタンスの管理者のユーザー名とパスワードを設定します。

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
    -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $keyVaultResourceId –VolumeType "All"
```

プロンプトが表示されたら、「*y*」と入力して、スケール セット VM インスタンス上のディスク暗号化プロセスを続行します。

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

## <a name="next-steps"></a>次の手順

この記事では、Azure PowerShell を使用して仮想マシン スケール セットを暗号化しました。 また、[Azure CLI](virtual-machine-scale-sets-encrypt-disks-cli.md) や、[Windows](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-vmss-windows-jumpbox) または [Linux](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-vmss-linux-jumpbox) 用のテンプレートを使用することもできます。
