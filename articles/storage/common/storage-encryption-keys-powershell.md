---
title: PowerShell を使用してカスタマー マネージド キーを構成する
titleSuffix: Azure Storage
description: PowerShell を使用して Azure Storage 暗号化用にカスタマー マネージド キーを構成する方法について説明します。
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 08/24/2020
ms.author: tamram
ms.reviewer: ozgun
ms.subservice: common
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 2beaae3fd6aaae719ac84ef86d7dd7877c94f757
ms.sourcegitcommit: 656c0c38cf550327a9ee10cc936029378bc7b5a2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/28/2020
ms.locfileid: "89076125"
---
# <a name="configure-customer-managed-keys-with-azure-key-vault-by-using-powershell"></a>PowerShell を使用して Azure Key Vault でカスタマー マネージド キーを構成する

[!INCLUDE [storage-encryption-configure-keys-include](../../../includes/storage-encryption-configure-keys-include.md)]

この記事では、PowerShell を使用するカスタマー マネージド キーでAzure Key Vault を構成する方法を説明します。 Azure CLI を使用してキー コンテナーを作成する方法を学習するには、「[クイック スタート: PowerShell を使用して Azure Key Vault との間でシークレットの設定と取得を行う](../../key-vault/secrets/quick-create-powershell.md)」を参照してください。

## <a name="assign-an-identity-to-the-storage-account"></a>ストレージ アカウントに ID を割り当てる

お使いのストレージ アカウントでカスタマー マネージド キーを有効にするには、まず、そのストレージ アカウントにシステム割り当てマネージド ID を割り当てます。 このマネージド ID を使って、キー コンテナーへのアクセス許可をストレージ アカウントに付与します。

PowerShell を使用して、マネージド ID を割り当てるには、[Set-AzStorageAccount](/powershell/module/az.storage/set-azstorageaccount) を呼び出します。 角かっこ内のプレースホルダー値を独自の値で置き換えてください。

```powershell
$storageAccount = Set-AzStorageAccount -ResourceGroupName <resource_group> `
    -Name <storage-account> `
    -AssignIdentity
```

PowerShell を使用してシステム割り当てマネージド ID を構成する方法の詳細については、「[PowerShell を使用して Azure VM 上の Azure リソースのマネージド ID を構成する](../../active-directory/managed-identities-azure-resources/qs-configure-powershell-windows-vm.md)」を参照してください。

## <a name="create-a-new-key-vault"></a>新しいキー コンテナーを作成する

PowerShell を使用して新しいキー コンテナーを作成するには、バージョン 2.0.0 以降の [Az.KeyVault](https://www.powershellgallery.com/packages/Az.KeyVault/2.0.0) PowerShell モジュールをインストールします。 次に、[New-AzKeyVault](/powershell/module/az.keyvault/new-azkeyvault) を呼び出して新しいキー コンテナーを作成します。

Azure Storage 暗号化用のカスタマー マネージド キーの格納に使用するキー コンテナーには、2 つのキー保護設定 ( **[論理的な削除]** と **[Do Not Purge]\(消去しない\)** ) を有効にする必要があります。 バージョン 2.0.0 以降の Az.KeyVault モジュールでは、新しいキー コンテナーを作成するときには、既定で、論理的な削除が有効になります。

次の例では、 **[論理的な削除]** と **[Do Not Purge] (消去しない)** のプロパティを有効にして新しいキー コンテナーを作成しています。 角かっこ内のプレースホルダー値を独自の値で置き換えてください。

```powershell
$keyVault = New-AzKeyVault -Name <key-vault> `
    -ResourceGroupName <resource_group> `
    -Location <location> `
    -EnablePurgeProtection
```

PowerShell を使用して既存のキー コンテナーで **[論理的な削除]** と **[Do Not Purge]\(消去しない\)** を有効にする方法については、[PowerShell で論理的な削除を使用する方法](../../key-vault/general/soft-delete-powershell.md)に関する記事の「**論理的な削除を有効にする**」および「**消去保護を有効にする**」を参照してください。

## <a name="configure-the-key-vault-access-policy"></a>キー コンテナーのアクセス ポリシーを構成する

次に、キー コンテナーのアクセス ポリシーを構成し、ストレージ アカウントからアクセスできるようにします。 この手順では、以前にストレージ アカウントに割り当てたマネージド ID を使用します。

キー コンテナーのアクセス ポリシーを設定するには、[Set-AzKeyVaultAccessPolicy](/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy) を呼び出します。 角かっこ内のプレースホルダー値を独自の値に置き換え、前の例で定義した変数を使用してください。

```powershell
Set-AzKeyVaultAccessPolicy `
    -VaultName $keyVault.VaultName `
    -ObjectId $storageAccount.Identity.PrincipalId `
    -PermissionsToKeys wrapkey,unwrapkey,get
```

## <a name="create-a-new-key"></a>新しいキーを作成する

次に、キー コンテナーに新しいキーを作成します。 新しいキーを作成するには、[Add-AzKeyVaultKey](/powershell/module/az.keyvault/add-azkeyvaultkey) を呼び出します。 角かっこ内のプレースホルダー値を独自の値に置き換え、前の例で定義した変数を使用してください。

```powershell
$key = Add-AzKeyVaultKey -VaultName $keyVault.VaultName -Name <key> -Destination 'Software'
```

Azure Storage の暗号化では、2048 ビット、3072 ビット、および 4096 ビットの RSA キーおよび RSA-HSM キーのみがサポートされています。 キーの詳細については、「[Azure Key Vault のキー、シークレット、証明書について](../../key-vault/about-keys-secrets-and-certificates.md#key-vault-keys)」の「**Key Vault のキー**」を参照してください。

## <a name="configure-encryption-with-customer-managed-keys"></a>カスタマー マネージド キーによる暗号化を構成する

既定で Azure Storage の暗号化には、Microsoft マネージド キーを使用します。 この手順では、Azure Key Vault でカスタマー マネージド キーを使用するように Azure Storage アカウントを構成してから、そのストレージ アカウントに関連付けるキーを指定します。

カスタマー マネージド キーを使用して暗号化を構成する場合は、関連付けられているキー コンテナーでキーのバージョンが変更されたときに、暗号化に使用するキーを自動的に更新することを選択できます。 または、キーのバージョンが手動で更新されるまで、暗号化に使用するキーのバージョンを明示的に指定できます。

> [!NOTE]
> キーを交換するには、Azure Key Vault でキーの新しいバージョンを作成します。 Azure Storage では Azure Key Vault でのキーの交換は処理されないため、キーを手動で交換するか、スケジュールに基づいて交換する関数を作成する必要があります。

### <a name="configure-encryption-to-automatically-update-the-key-version"></a>キーのバージョンを自動的に更新するように暗号化を構成する

キーのバージョンを自動的に更新するようにカスタマー マネージド キーを使用した暗号化を構成するには、[Az.Storage](https://www.powershellgallery.com/packages/Az.Storage) モジュールのバージョン 2.0.0 以降をインストールします。

カスタマー マネージド キーのキー バージョンを自動的に更新するには、ストレージ アカウントでカスタマー マネージド キーを使用した暗号化を構成するときに、キーのバージョンを省略します。 次の例に示すように、[Set-AzStorageAccount](/powershell/module/az.storage/set-azstorageaccount) を呼び出してストレージ アカウントの暗号化設定を更新し、 **-KeyvaultEncryption** オプションを含めてストレージ アカウントでカスタマー マネージド キーを有効にします。 角かっこ内のプレースホルダー値を独自の値に置き換え、前の例で定義した変数を使用してください。

```powershell
Set-AzStorageAccount -ResourceGroupName $storageAccount.ResourceGroupName `
    -AccountName $storageAccount.StorageAccountName `
    -KeyvaultEncryption `
    -KeyName $key.Name `
    -KeyVaultUri $keyVault.VaultUri
```

### <a name="configure-encryption-for-manual-updating-of-key-versions"></a>キー バージョンを手動で更新するように暗号化を構成する

暗号化で使用するキー バージョンを明示的に指定するには、ストレージ アカウント用のカスタマー マネージド キーを指定して暗号化を設定するときに、キー バージョンを指定します。 次の例に示すように、[Set-AzStorageAccount](/powershell/module/az.storage/set-azstorageaccount) を呼び出してストレージ アカウントの暗号化設定を更新し、 **-KeyvaultEncryption** オプションを含めてストレージ アカウントでカスタマー マネージド キーを有効にします。 角かっこ内のプレースホルダー値を独自の値に置き換え、前の例で定義した変数を使用してください。

```powershell
Set-AzStorageAccount -ResourceGroupName $storageAccount.ResourceGroupName `
    -AccountName $storageAccount.StorageAccountName `
    -KeyvaultEncryption `
    -KeyName $key.Name `
    -KeyVersion $key.Version `
    -KeyVaultUri $keyVault.VaultUri
```

キー バージョンを手動で更新する場合は、新しいバージョンを使用するようにストレージ アカウントの暗号化設定を更新する必要があります。 まず [Get-AzKeyVaultKey](/powershell/module/az.keyvault/get-azkeyvaultkey) を呼び出し、キーの最新バージョンを取得します。 次に、前の例で示したように、[Set-AzStorageAccount](/powershell/module/az.storage/set-azstorageaccount) を呼び出して、新しいバージョンのキーを使用するようにストレージ アカウントの暗号化設定を更新します。

## <a name="use-a-different-key"></a>別のキーを使用する

Azure Storage の暗号化に使用するキーを変更するには、「[カスタマー マネージド キーによる暗号化を構成する](#configure-encryption-with-customer-managed-keys)」で示されているように [Set-AzStorageAccount](/powershell/module/az.storage/set-azstorageaccount) を呼び出して、新しいキーの名前とバージョンを指定します。 新しいキーが別のキー コンテナーにある場合は、キー コンテナー URI も更新します。

## <a name="revoke-customer-managed-keys"></a>カスタマー マネージド キーを取り消す

キー コンテナーのアクセス ポリシーを削除することで、カスタマー マネージド キーを取り消すことができます。 カスタマー マネージド キーを取り消すには、次の例に示すように、[Remove-AzKeyVaultAccessPolicy](/powershell/module/az.keyvault/remove-azkeyvaultaccesspolicy) コマンドを呼び出します。 角かっこ内のプレースホルダー値を独自の値に置き換え、前の例で定義した変数を使用してください。

```powershell
Remove-AzKeyVaultAccessPolicy -VaultName $keyVault.VaultName `
    -ObjectId $storageAccount.Identity.PrincipalId `
```

## <a name="disable-customer-managed-keys"></a>カスタマー マネージド キーを無効にする

カスタマー マネージド キーを無効にすると、ストレージ アカウントは、Microsoft が管理するキーを使用して再び暗号化されます。 カスタマー マネージド キーを無効にするには、次の例に示すように、[Set-AzStorageAccount](/powershell/module/az.storage/set-azstorageaccount) を呼び出して `-StorageEncryption` オプションを指定します。 角かっこ内のプレースホルダー値を独自の値に置き換え、前の例で定義した変数を使用してください。

```powershell
Set-AzStorageAccount -ResourceGroupName $storageAccount.ResourceGroupName `
    -AccountName $storageAccount.StorageAccountName `
    -StorageEncryption  
```

## <a name="next-steps"></a>次のステップ

- [保存データに対する Azure Storage 暗号化](storage-service-encryption.md)
- [Azure Key Vault とは](https://docs.microsoft.com/azure/key-vault/key-vault-overview)
