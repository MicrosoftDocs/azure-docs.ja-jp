---
title: Azure Key Vault に格納されているカスタマー マネージド キーによる暗号化を構成する
titleSuffix: Azure Storage
description: Azure portal、PowerShell、または Azure CLI を使用して、Azure Key Vault に格納されているカスタマー マネージド キーによる Azure Storage 暗号化を構成する方法について説明します。
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 02/16/2021
ms.author: tamram
ms.reviewer: ozgun
ms.subservice: common
ms.custom: devx-track-azurepowershell, devx-track-azurecli
ms.openlocfilehash: 24fbe843986b732a04c9e356c54f3d768d6739be
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "100558182"
---
# <a name="configure-encryption-with-customer-managed-keys-stored-in-azure-key-vault"></a>Azure Key Vault に格納されているカスタマー マネージド キーによる暗号化を構成する

Azure Storage は、保存されているストレージ アカウント内のすべてのデータを暗号化します。 規定では、データは Microsoft のマネージド キーで暗号化されます。 暗号化キーをさらに制御するために、独自のキーを管理することができます。 カスタマー マネージド キーは、Azure Key Vault または Key Vault Managed Hardware Security Model (HSM) (プレビュー) に格納する必要があります。

この記事では、Azure portal、PowerShell、または Azure CLI を使用して、キー コンテナーに格納されているカスタマー マネージド キーによる暗号化を構成する方法について説明します。 マネージド HSM に格納されているカスタマー マネージド キーを使用して暗号化を構成する方法については、「[Azure Key Vault Managed HSM (プレビュー) に格納されているカスタマー マネージド キーによる暗号化を構成する](customer-managed-keys-configure-key-vault-hsm.md)」を参照してください。

> [!NOTE]
> Azure Key Vault と Azure Key Vault Managed HSM では、構成用に同じ API と管理インターフェイスがサポートされています。

## <a name="configure-a-key-vault"></a>キー コンテナーを構成する

新規または既存のキー コンテナーを使用して、カスタマー マネージド キーを格納することができます。 ストレージ アカウントとキー コンテナーは同じリージョンに存在していることが必要です。ただし、サブスクリプションは異なっていてもかまいません。

Azure Storage 暗号化でカスタマー マネージド キーを使うには、キー コンテナーに対して論理的な削除と消去保護の両方を有効にする必要があります。 新しいキー コンテナーを作成すると、論理的な削除は既定で有効になり、無効にすることはできません。 消去保護は、キー コンテナーの作成時または作成後に有効にすることができます。

# <a name="azure-portal"></a>[Azure Portal](#tab/portal)

Azure portal を使ってキー コンテナーを作成する方法を学習するには、「[クイック スタート: Azure portal を使用してキー コンテナーを作成する](../../key-vault/general/quick-create-portal.md)」を参照してください。 キー コンテナーを作成するときに、次の図に示すように、 **[消去保護を有効にします]** を選択します。

:::image type="content" source="media/customer-managed-keys-configure-key-vault/configure-key-vault-portal.png" alt-text="キー コンテナーを作成するときに消去保護を有効にする方法を示すスクリーンショット":::

既存のキー コンテナーで消去保護を有効にするには、次の手順を実行します。

1. Azure portal で、ご自身のキー コンテナーに移動します。
1. **[設定]** の **[プロパティ]** を選択します。
1. **[消去保護]** セクションで、 **[消去保護を有効にします]** を選択します。

# <a name="powershell"></a>[PowerShell](#tab/powershell)

PowerShell を使用して新しいキー コンテナーを作成するには、バージョン 2.0.0 以降の [Az.KeyVault](https://www.powershellgallery.com/packages/Az.KeyVault/2.0.0) PowerShell モジュールをインストールします。 次に、[New-AzKeyVault](/powershell/module/az.keyvault/new-azkeyvault) を呼び出して新しいキー コンテナーを作成します。 バージョン 2.0.0 以降の Az.KeyVault モジュールでは、新しいキー コンテナーを作成するときには、既定で、論理的な削除が有効になります。

次の例では、論理的な削除と消去保護の両方を有効にして新しいキー コンテナーを作成しています。 角かっこ内のプレースホルダー値を独自の値で置き換えてください。

```powershell
$keyVault = New-AzKeyVault -Name <key-vault> `
    -ResourceGroupName <resource_group> `
    -Location <location> `
    -EnablePurgeProtection
```

PowerShell を使用して既存のキー コンテナーに対する消去保護を有効にする方法については、「[PowerShell で論理的な削除を使用する方法](../../key-vault/general/key-vault-recovery.md)」を参照してください。

次に、ストレージ アカウントにシステム割り当てマネージド ID を割り当てます。 このマネージド ID を使って、キー コンテナーへのアクセス許可をストレージ アカウントに付与します。 システム割り当てマネージド ID の詳細については、「[Azure リソースのマネージド ID とは](../../active-directory/managed-identities-azure-resources/overview.md)」を参照してください。

PowerShell を使用してマネージド ID を割り当てるには、[Set-AzStorageAccount](/powershell/module/az.storage/set-azstorageaccount) を呼び出します。

```powershell
$storageAccount = Set-AzStorageAccount -ResourceGroupName <resource_group> `
    -Name <storage-account> `
    -AssignIdentity
```

最後に、キー コンテナーのアクセス ポリシーを構成し、ストレージ アカウントからアクセスできるようにします。 この手順では、以前にストレージ アカウントに割り当てたマネージド ID を使用します。

キー コンテナーのアクセス ポリシーを設定するには、[Set-AzKeyVaultAccessPolicy](/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy) を呼び出します。

```powershell
Set-AzKeyVaultAccessPolicy `
    -VaultName $keyVault.VaultName `
    -ObjectId $storageAccount.Identity.PrincipalId `
    -PermissionsToKeys wrapkey,unwrapkey,get
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Azure CLI を使用して新しいキー コンテナーを作成するには、[az keyvault create](/cli/azure/keyvault#az-keyvault-create) を呼び出します。 かっこ内のプレースホルダー値を独自の値に置き換えることを忘れないでください。

```azurecli-interactive
az keyvault create \
    --name <key-vault> \
    --resource-group <resource_group> \
    --location <region> \
    --enable-purge-protection
```

Azure CLI を使用して既存のキー コンテナーに対する消去保護を有効にする方法については、「[CLI で論理的な削除を使用する方法](../../key-vault/general/key-vault-recovery.md)」を参照してください。

次に、ストレージ アカウントにシステム割り当てマネージド ID を割り当てます。 このマネージド ID を使って、キー コンテナーへのアクセス許可をストレージ アカウントに付与します。 システム割り当てマネージド ID の詳細については、「[Azure リソースのマネージド ID とは](../../active-directory/managed-identities-azure-resources/overview.md)」を参照してください。

Azure CLI を使用してマネージド ID を割り当てるには、[az storage account update](/cli/azure/storage/account#az-storage-account-update) を呼び出します。

```azurecli-interactive
az storage account update \
    --name <storage-account> \
    --resource-group <resource_group> \
    --assign-identity
```

最後に、キー コンテナーのアクセス ポリシーを構成し、ストレージ アカウントからアクセスできるようにします。 この手順では、以前にストレージ アカウントに割り当てたマネージド ID を使用します。

キー コンテナーのアクセス ポリシーを設定するには、[az keyvault set-policy](/cli/azure/keyvault#az-keyvault-set-policy) を呼び出します。

```azurecli-interactive
storage_account_principal=$(az storage account show \
    --name <storage-account> \
    --resource-group <resource-group> \
    --query identity.principalId \
    --output tsv)
az keyvault set-policy \
    --name <key-vault> \
    --resource-group <resource_group>
    --object-id $storage_account_principal \
    --key-permissions get unwrapKey wrapKey
```

---

## <a name="add-a-key"></a>キーを追加する

次に、キー コンテナーにキーを追加します。

Azure Storage の暗号化では、2,048、3,072、および 4,096 のサイズの RSA キーと RSA-HSM キーがサポートされています。 キーの詳細については、「[キーについて](../../key-vault/keys/about-keys.md)」を参照してください。

# <a name="azure-portal"></a>[Azure Portal](#tab/portal)

Azure portal を使ってキーを追加する方法を学習するには、「[クイック スタート: Azure portal を使用して Azure Key Vault との間でキーの設定と取得を行う](../../key-vault/keys/quick-create-portal.md)」を参照してください。

# <a name="powershell"></a>[PowerShell](#tab/powershell)

PowerShell を使用してキーを追加するには、[Add-AzKeyVaultKey](/powershell/module/az.keyvault/add-azkeyvaultkey) を呼び出します。 角かっこ内のプレースホルダー値を独自の値に置き換え、前の例で定義した変数を使用してください。

```powershell
$key = Add-AzKeyVaultKey -VaultName $keyVault.VaultName `
    -Name <key> `
    -Destination 'Software'
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Azure CLI を使用してキーを追加するには、[az keyvault key create](/cli/azure/keyvault/key#az-keyvault-key-create) を呼び出します。 角かっこ内のプレースホルダー値を独自の値で置き換えてください。

```azurecli-interactive
az keyvault key create \
    --name <key> \
    --vault-name <key-vault>
```

---

## <a name="configure-encryption-with-customer-managed-keys"></a>カスタマー マネージド キーによる暗号化を構成する

次に、Azure Key Vault でカスタマー マネージド キーを使用するように Azure Storage アカウントを構成してから、そのストレージ アカウントに関連付けるキーを指定します。

カスタマー マネージド キーを使用して暗号化を構成する場合は、関連付けられているキー コンテナーで新しいバージョンが使用可能になるたびに、Azure Storage 暗号化に使用するキーのバージョンを自動的に更新することを選択できます。 または、キーのバージョンが手動で更新されるまで暗号化に使用するキーのバージョンを明示的に指定できます。

> [!NOTE]
> キーを交換するには、Azure Key Vault でキーの新しいバージョンを作成します。 Azure Storage では Azure Key Vault でのキーの交換は処理されないため、キーを手動で交換するか、スケジュールに基づいて交換する関数を作成する必要があります。

### <a name="configure-encryption-for-automatic-updating-of-key-versions"></a>キー バージョンの自動更新の暗号化を構成する

Azure Storage では、暗号化に使用されているカスタマー マネージド キーを、最新のキーのバージョンを使用するように自動的に更新できます。 Azure Key Vault でカスタマー マネージド キーが交換されると、Azure Storage では、最新バージョンのキーを使用した暗号化が自動的に開始されます。

# <a name="azure-portal"></a>[Azure Portal](#tab/portal)

Azure portal でキーのバージョンを自動更新するようにカスタマー マネージド キーを構成するには、次の手順を実行します。

1. ストレージ アカウントに移動します。
1. ストレージ アカウントの **[設定]** ブレードで、 **[暗号化]** をクリックします。 次の図に示すように、既定ではキー管理は **[Microsoft マネージド キー]** に設定されています。

    ![暗号化オプションが表示されているポータルのスクリーンショット](./media/customer-managed-keys-configure-key-vault/portal-configure-encryption-keys.png)

1. **[カスタマー マネージド キー]** オプションを選択します。
1. **[Select from Key Vault]\(キー コンテナーから選択\)** オプションを選択します。
1. **[Select a key vault and key] (キー コンテナーとキーを選択する)** を選択します。
1. 使用するキーを含むキー コンテナーを選択します。
1. キー コンテナーからキーを選択します。

   ![キー コンテナーとキーを選択する方法を示すスクリーンショット](./media/customer-managed-keys-configure-key-vault/portal-select-key-from-key-vault.png)

1. 変更を保存します。

キーを指定した後は、Azure portal に、キーのバージョンの自動更新が有効になっていることが示されて、現在暗号化に使用されているキーのバージョンが表示されます。

:::image type="content" source="media/customer-managed-keys-configure-key-vault/portal-auto-rotation-enabled.png" alt-text="キーのバージョンの自動更新が有効になっていることを示すスクリーンショット":::

# <a name="powershell"></a>[PowerShell](#tab/powershell)

PowerShell を使用してキーのバージョンを自動的に更新するようにカスタマー マネージド キーを構成するには、[Az.Storage](https://www.powershellgallery.com/packages/Az.Storage) モジュールのバージョン 2.0.0 以降をインストールします。

カスタマー マネージド キーのキー バージョンを自動的に更新するには、ストレージ アカウントでカスタマー マネージド キーを使用した暗号化を構成するときに、キーのバージョンを省略します。 次の例に示すように、[Set-AzStorageAccount](/powershell/module/az.storage/set-azstorageaccount) を呼び出してストレージ アカウントの暗号化設定を更新し、 **-KeyvaultEncryption** オプションを含めてストレージ アカウントでカスタマー マネージド キーを有効にします。

角かっこ内のプレースホルダー値を独自の値に置き換え、前の例で定義した変数を使用してください。

```powershell
Set-AzStorageAccount -ResourceGroupName $storageAccount.ResourceGroupName `
    -AccountName $storageAccount.StorageAccountName `
    -KeyvaultEncryption `
    -KeyName $key.Name `
    -KeyVaultUri $keyVault.VaultUri
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Azure CLI を使用してキーのバージョンを自動的に更新するようにカスタマー マネージド キーを構成するには、[Azure CLI バージョン 2.4.0](/cli/azure/release-notes-azure-cli#april-21-2020) 以降をインストールします。 詳細については、「 [Azure CLI のインストール](/cli/azure/install-azure-cli)」を参照してください。

カスタマー マネージド キーのキー バージョンを自動的に更新するには、ストレージ アカウントでカスタマー マネージド キーを使用した暗号化を構成するときに、キーのバージョンを省略します。 ストレージ アカウントの暗号化設定を更新するには、次の例に示すように [az storage account update](/cli/azure/storage/account#az-storage-account-update) を呼び出します。 `--encryption-key-source` パラメーターを含め、それを `Microsoft.Keyvault` に設定して、アカウントのカスタマー マネージド キーを有効にします。

角かっこ内のプレースホルダー値を独自の値で置き換えてください。

```azurecli-interactive
key_vault_uri=$(az keyvault show \
    --name <key-vault> \
    --resource-group <resource_group> \
    --query properties.vaultUri \
    --output tsv)
az storage account update
    --name <storage-account> \
    --resource-group <resource_group> \
    --encryption-key-name <key> \
    --encryption-key-source Microsoft.Keyvault \
    --encryption-key-vault $key_vault_uri
```

---

### <a name="configure-encryption-for-manual-updating-of-key-versions"></a>キー バージョンを手動で更新するように暗号化を構成する

キーのバージョンを手動で更新する場合は、カスタマー マネージド キーを使用して暗号化を構成するときに、そのバージョンを明示的に指定します。 この場合、新しいバージョンがキー コンテナーに作成されても、Azure Storage でキーのバージョンが自動的に更新されることはありません。新しいキーのバージョンを使用するには、Azure Storage 暗号化に使用するバージョンを手動で更新する必要があります。

# <a name="azure-portal"></a>[Azure Portal](#tab/portal)

Azure portal でキーのバージョンを手動更新するようにカスタマー マネージド キーを構成するには、バージョンを含むキー URI を指定します。 URI としてキーを指定するには、次の手順のようにします。

1. Azure portal でキーの URI を調べるには、キー コンテナーに移動して、 **[キー]** 設定を選択します。 目的のキーを選択し、キーをクリックしてそのバージョンを表示します。 そのバージョンの設定を表示するには、キーのバージョンを選択します。
1. URI を示している **[キー識別子]** フィールドの値をコピーします。

    ![キー コンテナーのキー URI が表示されているスクリーンショット](media/customer-managed-keys-configure-key-vault/portal-copy-key-identifier.png)

1. ストレージ アカウントの **[暗号化キー]** の設定で、 **[キー URI を入力]** オプションを選択します。
1. コピーした URI を **[キー URI]** フィールドに貼り付けます。 URI からキーのバージョンを省略して、キーのバージョンの自動更新を有効にします。

   ![キー URI の入力方法が示されているスクリーンショット](./media/customer-managed-keys-configure-key-vault/portal-specify-key-uri.png)

1. キー コンテナーを含むサブスクリプションを指定します。
1. 変更を保存します。

# <a name="powershell"></a>[PowerShell](#tab/powershell)

キーのバージョンを手動更新するようにカスタマー マネージド キーを構成するには、ストレージ アカウントの暗号化を設定するときに、キーのバージョンを明示的に指定します。 次の例に示すように、[Set-AzStorageAccount](/powershell/module/az.storage/set-azstorageaccount) を呼び出してストレージ アカウントの暗号化設定を更新し、 **-KeyvaultEncryption** オプションを含めてストレージ アカウントでカスタマー マネージド キーを有効にします。

角かっこ内のプレースホルダー値を独自の値に置き換え、前の例で定義した変数を使用してください。

```powershell
Set-AzStorageAccount -ResourceGroupName $storageAccount.ResourceGroupName `
    -AccountName $storageAccount.StorageAccountName `
    -KeyvaultEncryption `
    -KeyName $key.Name `
    -KeyVersion $key.Version `
    -KeyVaultUri $keyVault.VaultUri
```

キー バージョンを手動で更新する場合は、新しいバージョンを使用するようにストレージ アカウントの暗号化設定を更新する必要があります。 まず [Get-AzKeyVaultKey](/powershell/module/az.keyvault/get-azkeyvaultkey) を呼び出し、キーの最新バージョンを取得します。 次に、前の例で示したように、[Set-AzStorageAccount](/powershell/module/az.storage/set-azstorageaccount) を呼び出して、新しいバージョンのキーを使用するようにストレージ アカウントの暗号化設定を更新します。

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

キーのバージョンを手動更新するようにカスタマー マネージド キーを構成するには、ストレージ アカウントの暗号化を設定するときに、キーのバージョンを明示的に指定します。 ストレージ アカウントの暗号化設定を更新するには、次の例に示すように [az storage account update](/cli/azure/storage/account#az-storage-account-update) を呼び出します。 `--encryption-key-source` パラメーターを含め、それを `Microsoft.Keyvault` に設定して、アカウントのカスタマー マネージド キーを有効にします。

角かっこ内のプレースホルダー値を独自の値で置き換えてください。

```azurecli-interactive
key_vault_uri=$(az keyvault show \
    --name <key-vault> \
    --resource-group <resource_group> \
    --query properties.vaultUri \
    --output tsv)
key_version=$(az keyvault key list-versions \
    --name <key> \
    --vault-name <key-vault> \
    --query [-1].kid \
    --output tsv | cut -d '/' -f 6)
az storage account update
    --name <storage-account> \
    --resource-group <resource_group> \
    --encryption-key-name <key> \
    --encryption-key-version $key_version \
    --encryption-key-source Microsoft.Keyvault \
    --encryption-key-vault $key_vault_uri
```

キー バージョンを手動で更新する場合は、新しいバージョンを使用するようにストレージ アカウントの暗号化設定を更新する必要があります。 まず、[az keyvault show](/cli/azure/keyvault#az-keyvault-show) を呼び出すことでキー コンテナーの URI を照会し、[az keyvault key list-versions](/cli/azure/keyvault/key#az-keyvault-key-list-versions) を呼び出すことでキーのバージョンを照会します。 次に、[az storage account update](/cli/azure/storage/account#az-storage-account-update) を呼び出して、キーの新しいバージョンを使用するようにストレージ アカウントの暗号化設定を更新します (前の例を参照)。

---

## <a name="change-the-key"></a>キーを変更する

Azure Storage 暗号化に使用しているキーは、いつでも変更できます。

# <a name="azure-portal"></a>[Azure Portal](#tab/portal)

Azure portal でキーを変更するには、次の手順を実行します。

1. お使いのストレージ アカウントに移動し、 **[暗号化]** の設定を表示します。
1. キー コンテナーを選択し、新しいキーを選択します。
1. 変更を保存します。

# <a name="powershell"></a>[PowerShell](#tab/powershell)

PowerShell を使用してキーを変更するには、「[カスタマー マネージド キーによる暗号化を構成する](#configure-encryption-with-customer-managed-keys)」で示されているように [Set-AzStorageAccount](/powershell/module/az.storage/set-azstorageaccount) を呼び出して、新しいキーの名前とバージョンを指定します。 新しいキーが別のキー コンテナーにある場合は、キー コンテナー URI も更新する必要があります。

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Azure CLI を使用してキーを変更するには、「[カスタマー マネージド キーによる暗号化を構成する](#configure-encryption-with-customer-managed-keys)」で示しているように [az storage account update](/cli/azure/storage/account#az-storage-account-update) を呼び出して、新しいキーの名前とバージョンを指定します。 新しいキーが別のキー コンテナーにある場合は、キー コンテナー URI も更新する必要があります。

---

## <a name="revoke-customer-managed-keys"></a>カスタマー マネージド キーを取り消す

カスタマー マネージド キーを取り消すと、ストレージ アカウントとキー コンテナーの関連付けが削除されます。

# <a name="azure-portal"></a>[Azure Portal](#tab/portal)

Azure portal を使用してカスタマー マネージド キーを取り消すには、「[カスタマー マネージド キーを無効にする](#disable-customer-managed-keys)」の説明に従ってキーを無効にします。

# <a name="powershell"></a>[PowerShell](#tab/powershell)

キー コンテナーのアクセス ポリシーを削除すると、カスタマー マネージド キーを取り消すことができます。 PowerShell を使用してカスタマー マネージド キーを取り消すには、次の例に示すように、[Remove-AzKeyVaultAccessPolicy](/powershell/module/az.keyvault/remove-azkeyvaultaccesspolicy) コマンドを呼び出します。 角かっこ内のプレースホルダー値を独自の値に置き換え、前の例で定義した変数を使用してください。

```powershell
Remove-AzKeyVaultAccessPolicy -VaultName $keyVault.VaultName `
    -ObjectId $storageAccount.Identity.PrincipalId `
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

キー コンテナーのアクセス ポリシーを削除すると、カスタマー マネージド キーを取り消すことができます。 Azure CLI を使用してカスタマー マネージド キーを取り消すには、次の例に示すように、[az keyvault delete-policy](/cli/azure/keyvault#az-keyvault-delete-policy) コマンドを呼び出します。 角かっこ内のプレースホルダー値を独自の値に置き換え、前の例で定義した変数を使用してください。

```azurecli-interactive
az keyvault delete-policy \
    --name <key-vault> \
    --object-id $storage_account_principal
```

---

## <a name="disable-customer-managed-keys"></a>カスタマー マネージド キーを無効にする

カスタマー マネージド キーを無効にすると、ストレージ アカウントは、Microsoft が管理するキーを使用して再び暗号化されます。

# <a name="azure-portal"></a>[Azure Portal](#tab/portal)

Azure portal でカスタマー マネージド キーを無効にするには、次の手順のようにします。

1. お使いのストレージ アカウントに移動し、 **[暗号化]** の設定を表示します。
1. **[独自のキーを使用する]** 設定の横にあるチェック ボックスをオフにします。

# <a name="powershell"></a>[PowerShell](#tab/powershell)

PowerShell を使用してカスタマー マネージド キーを無効にするには、次の例に示すように、[Set-AzStorageAccount](/powershell/module/az.storage/set-azstorageaccount) を呼び出して `-StorageEncryption` オプションを指定します。 角かっこ内のプレースホルダー値を独自の値に置き換え、前の例で定義した変数を使用してください。

```powershell
Set-AzStorageAccount -ResourceGroupName $storageAccount.ResourceGroupName `
    -AccountName $storageAccount.StorageAccountName `
    -StorageEncryption  
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Azure CLI を使用してカスタマー マネージド キーを無効にするには、次の例に示すように、[az storage account update](/cli/azure/storage/account#az-storage-account-update) を呼び出して、`--encryption-key-source parameter` を `Microsoft.Storage` に設定します。 角かっこ内のプレースホルダー値を独自の値に置き換え、前の例で定義した変数を使用してください。

```azurecli-interactive
az storage account update
    --name <storage-account> \
    --resource-group <resource_group> \
    --encryption-key-source Microsoft.Storage
```

---

## <a name="next-steps"></a>次のステップ

- [保存データに対する Azure Storage 暗号化](storage-service-encryption.md)
- [Azure Storage の暗号化のためのカスタマー マネージド キー](customer-managed-keys-overview.md)
- [Azure Key Vault Managed HSM (プレビュー) に格納されているカスタマー マネージド キーによる暗号化を構成する](customer-managed-keys-configure-key-vault-hsm.md)
