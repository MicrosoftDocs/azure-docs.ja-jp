---
title: Azure PowerShell - 保存時の二重暗号化を有効にする - マネージド ディスク
description: Azure PowerShell を使用して、マネージド ディスク データに対して保存時の二重暗号化を有効にします。
author: roygara
ms.date: 08/24/2020
ms.topic: how-to
ms.author: rogarana
ms.service: virtual-machines-windows
ms.subservice: disks
ms.custom: references_regions
ms.openlocfilehash: 6cb0bf9ae12169f9431cf221431f442b5870d932
ms.sourcegitcommit: d39f2cd3e0b917b351046112ef1b8dc240a47a4f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/25/2020
ms.locfileid: "88816509"
---
# <a name="use-the-azure-powershell-module-to-enable-double-encryption-at-rest-for-managed-disks"></a>Azure PowerShell モジュールを使用して、マネージド ディスクの保存時の二重暗号化を有効にします。

Azure Disk Storage は、マネージド ディスクに対する保存時の二重暗号化をサポートしています。 保存時の二重暗号化とその他のマネージド ディスクの暗号化の概念については、ディスクの暗号化の記事の[保存時の暗号化](disk-encryption.md#double-encryption-at-rest)に関するセクションを参照してください。

## <a name="supported-regions"></a>サポートされているリージョン

[!INCLUDE [virtual-machines-disks-double-encryption-at-rest-regions](../../../includes/virtual-machines-disks-double-encryption-at-rest-regions.md)]

## <a name="prerequisites"></a>前提条件

最新の [Azure PowerShell バージョン](/powershell/azure/install-az-ps)をインストールし、[Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount?view=azps-4.3.0) を使用して Azure アカウントにサインインします。

## <a name="getting-started"></a>作業の開始

1. Azure Key Vault と暗号化キーのインスタンスを作成します。

    Key Vault インスタンスを作成する場合、論理的な削除と消去保護を有効にする必要があります。 論理的な削除では、Key Vault は削除されたキーを特定の保持期間 (既定では90日) にわたって保持します。 消去保護では、保持期間が経過するまで、削除されたキーを完全に削除できないようになります。 これらの設定は、誤って削除したためにデータが失われるのを防ぎます。 これらの設定は、Key Vault を使用してマネージド ディスクを暗号化する場合は必須です。
    
    ```powershell
    $ResourceGroupName="yourResourceGroupName"
    $LocationName="westus2"
    $keyVaultName="yourKeyVaultName"
    $keyName="yourKeyName"
    $keyDestination="Software"
    $diskEncryptionSetName="yourDiskEncryptionSetName"

    $keyVault = New-AzKeyVault -Name $keyVaultName -ResourceGroupName $ResourceGroupName -Location $LocationName -EnableSoftDelete -EnablePurgeProtection

    $key = Add-AzKeyVaultKey -VaultName $keyVaultName -Name $keyName -Destination $keyDestination  
    ```

1.  EncryptionAtRestWithPlatformAndCustomerKeys として encryptionType を設定して、DiskEncryptionSet を作成します。 Azure Resource Manager (ARM) テンプレートで API バージョン **2020-05-01** を使用します。 
    
    ```powershell
    New-AzResourceGroupDeployment -ResourceGroupName $ResourceGroupName `
    -TemplateUri "https://raw.githubusercontent.com/Azure-Samples/managed-disks-powershell-getting-started/master/DoubleEncryption/CreateDiskEncryptionSetForDoubleEncryption.json" `
    -diskEncryptionSetName $diskEncryptionSetName `
    -keyVaultId $keyVault.ResourceId `
    -keyVaultKeyUrl $key.Key.Kid `
    -encryptionType "EncryptionAtRestWithPlatformAndCustomerKeys" `
    -region $LocationName
    ```

1. DiskEncryptionSet リソースに Key Vault へのアクセス権を付与します。

    > [!NOTE]
    > Azure がお使いの Azure Active Directory にご自分の DiskEncryptionSet の ID を作成するのには数分かかる場合があります。 次のコマンドを実行しているときに "Active Directory オブジェクトが見つかりません" のようなエラーが表示された場合は、数分待ってから再試行してください。

    ```powershell  
    $des=Get-AzDiskEncryptionSet -name $diskEncryptionSetName -ResourceGroupName $ResourceGroupName
    Set-AzKeyVaultAccessPolicy -VaultName $keyVaultName -ObjectId $des.Identity.PrincipalId -PermissionsToKeys wrapkey,unwrapkey,get
    ```

## <a name="next-steps"></a>次のステップ

これらのリソースを作成し、構成したので、これらを使用してマネージド ディスクをセキュリティで保護することができます。 次のリンクには、個々のシナリオごとにスクリプトの例が含まれています。これは、マネージド ディスクをセキュリティで保護するために使用できます。

- [Azure PowerShell - サーバー側の暗号化でカスタマー マネージド キーを有効にする - マネージド ディスク](disks-enable-customer-managed-keys-powershell.md)
- [Azure Resource Manager テンプレートのサンプル](https://github.com/Azure-Samples/managed-disks-powershell-getting-started/tree/master/DoubleEncryption)