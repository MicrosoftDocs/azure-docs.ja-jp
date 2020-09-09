---
title: Azure CLI を使用し、顧客が管理するキーを構成する
titleSuffix: Azure Storage
description: Azure CLI を使用して、Azure Storage 暗号化用に Azure Key Vault でカスタマー マネージド キーを構成する方法について説明します。
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 08/24/2020
ms.author: tamram
ms.reviewer: ozgun
ms.subservice: common
ms.custom: devx-track-azurecli
ms.openlocfilehash: 25ee5d389bc70d82730c7056c752de393a6bf4c5
ms.sourcegitcommit: c5021f2095e25750eb34fd0b866adf5d81d56c3a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/25/2020
ms.locfileid: "88799147"
---
# <a name="configure-customer-managed-keys-with-azure-key-vault-by-using-azure-cli"></a>Azure CLI を使用して Azure Key Vault でカスタマー マネージド キーを構成する

[!INCLUDE [storage-encryption-configure-keys-include](../../../includes/storage-encryption-configure-keys-include.md)]

この記事では、Azure CLI を使用して、カスタマー マネージド キーを使用する Azure Key Vault を構成する方法について説明します。 Azure CLI を使用してキー コンテナーを作成する方法を学習するには、「[クイック スタート: Azure CLI を使用して Azure Key Vault との間でシークレットの設定と取得を行う](../../key-vault/secrets/quick-create-cli.md)」を参照してください。

## <a name="assign-an-identity-to-the-storage-account"></a>ストレージ アカウントに ID を割り当てる

お使いのストレージ アカウントでカスタマー マネージド キーを有効にするには、まず、そのストレージ アカウントにシステム割り当てマネージド ID を割り当てます。 このマネージド ID を使って、キー コンテナーへのアクセス許可をストレージ アカウントに付与します。

Azure CLI を使用してマネージド ID を割り当てるには、[az storage account update](/cli/azure/storage/account#az-storage-account-update) を呼び出します。 角かっこ内のプレースホルダー値を独自の値で置き換えてください。

```azurecli-interactive
az login
az account set --subscription <subscription-id>

az storage account update \
    --name <storage-account> \
    --resource-group <resource_group> \
    --assign-identity
```

Azure CLI を使用してシステム割り当てマネージド ID を構成する方法について詳しくは、「[Azure CLI を使用して Azure VM 上に Azure リソースのマネージド ID を構成する](../../active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vm.md)」をご覧ください。

## <a name="create-a-new-key-vault"></a>新しいキー コンテナーを作成する

Azure Storage 暗号化用のカスタマー マネージド キーの格納に使用するキー コンテナーには、2 つのキー保護設定 ( **[論理的な削除]** と **[Do Not Purge]\(消去しない\)** ) を有効にする必要があります。 これらの設定を有効にした状態で、PowerShell または Azure CLI を使用して新しいキー コンテナーを作成するには、次のコマンドを実行します。 角かっこ内のプレースホルダー値を独自の値で置き換えてください。

Azure CLI を使用して新しいキー コンテナーを作成するには、[az keyvault create](/cli/azure/keyvault#az-keyvault-create) を呼び出します。 角かっこ内のプレースホルダー値を独自の値で置き換えてください。

```azurecli-interactive
az keyvault create \
    --name <key-vault> \
    --resource-group <resource_group> \
    --location <region> \
    --enable-soft-delete \
    --enable-purge-protection
```

Azure CLI を使用して既存のキー コンテナーで **[論理的な削除]** と **[Do Not Purge]\(消去しない\)** を有効にする方法については、[CLI で論理的な削除を使用する方法](../../key-vault/general/soft-delete-cli.md)に関する記事の「**論理的な削除を有効にする**」および「**消去保護を有効にする**」を参照してください。

## <a name="configure-the-key-vault-access-policy"></a>キー コンテナーのアクセス ポリシーを構成する

次に、キー コンテナーのアクセス ポリシーを構成し、ストレージ アカウントからアクセスできるようにします。 この手順では、以前にストレージ アカウントに割り当てたマネージド ID を使用します。

キー コンテナーのアクセス ポリシーを設定するには、[az keyvault set-policy](/cli/azure/keyvault#az-keyvault-set-policy) を呼び出します。 角かっこ内のプレースホルダー値を独自の値で置き換えてください。

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

## <a name="create-a-new-key"></a>新しいキーを作成する

次に、キー コンテナーにキーを作成します。 キーを作成するには、[az keyvault key create](/cli/azure/keyvault/key#az-keyvault-key-create) を呼び出します。 角かっこ内のプレースホルダー値を独自の値で置き換えてください。

```azurecli-interactive
az keyvault key create \
    --name <key> \
    --vault-name <key-vault>
```

Azure Storage の暗号化では、2048、3072、および 4096 のサイズの RSA および RSA-HSM キーがサポートされています。 キーの詳細については、「[Azure Key Vault のキー、シークレット、証明書について](../../key-vault/about-keys-secrets-and-certificates.md#key-vault-keys)」の「**Key Vault のキー**」を参照してください。

## <a name="configure-encryption-with-customer-managed-keys"></a>カスタマー マネージド キーによる暗号化を構成する

既定で Azure Storage の暗号化には、Microsoft マネージド キーを使用します。 この手順では、Azure Key Vault でカスタマー マネージド キーを使用するように Azure Storage アカウントを構成してから、そのストレージ アカウントに関連付けるキーを指定します。

カスタマー マネージド キーを使用して暗号化を構成する場合は、関連付けられているキー コンテナーでキーのバージョンが変更されたときに、暗号化に使用するキーを自動的に更新することを選択できます。 または、キーのバージョンが手動で更新されるまで暗号化に使用するキーのバージョンを明示的に指定できます。

> [!NOTE]
> キーを交換するには、Azure Key Vault でキーの新しいバージョンを作成します。 Azure Storage では Azure Key Vault でのキーの交換は処理されないため、キーを手動で交換するか、スケジュールに基づいて交換する関数を作成する必要があります。

### <a name="configure-encryption-to-automatically-update-the-key-version"></a>キーのバージョンを自動的に更新するように暗号化を構成する

キーのバージョンを自動的に更新するようにカスタマー マネージド キーを使用した暗号化を構成するには、[Azure CLI バージョン 2.4.0](/cli/azure/release-notes-azure-cli#april-21-2020) 以降をインストールします。 詳細については、「 [Azure CLI のインストール](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)」を参照してください。

カスタマー マネージド キーのキー バージョンを自動的に更新するには、ストレージ アカウントでカスタマー マネージド キーを使用した暗号化を構成するときに、キーのバージョンを省略します。 ストレージ アカウントの暗号化設定を更新するには、次の例に示すように [az storage account update](/cli/azure/storage/account#az-storage-account-update) を呼び出します。 `--encryption-key-source` パラメーターを含め、それを `Microsoft.Keyvault` に設定して、アカウントのカスタマー マネージド キーを有効にします。 角かっこ内のプレースホルダー値を独自の値で置き換えてください。

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

### <a name="configure-encryption-for-manual-updating-of-key-versions"></a>キー バージョンを手動で更新するように暗号化を構成する

暗号化で使用するキー バージョンを明示的に指定するには、ストレージ アカウント用のカスタマー マネージド キーを指定して暗号化を設定するときに、キー バージョンを指定します。 ストレージ アカウントの暗号化設定を更新するには、次の例に示すように [az storage account update](/cli/azure/storage/account#az-storage-account-update) を呼び出します。 `--encryption-key-source` パラメーターを含め、それを `Microsoft.Keyvault` に設定して、アカウントのカスタマー マネージド キーを有効にします。 角かっこ内のプレースホルダー値を独自の値で置き換えてください。

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

## <a name="use-a-different-key"></a>別のキーを使用する

Azure Storage の暗号化に使用するキーを変更するには、「[カスタマー マネージド キーによる暗号化を構成する](#configure-encryption-with-customer-managed-keys)」で示しているように [az storage account update](/cli/azure/storage/account#az-storage-account-update) を呼び出して、新しいキーの名前とバージョンを指定します。 新しいキーが別のキー コンテナーにある場合は、キー コンテナー URI も更新します。

## <a name="revoke-customer-managed-keys"></a>カスタマー マネージド キーを取り消す

キー コンテナーのアクセス ポリシーを削除すると、カスタマー マネージド キーを取り消すことができます。 カスタマー マネージド キーを取り消すには、次の例に示すように、[az keyvault delete-policy](/cli/azure/keyvault#az-keyvault-delete-policy) コマンドを呼び出します。 角かっこ内のプレースホルダー値を独自の値に置き換え、前の例で定義した変数を使用してください。

```azurecli-interactive
az keyvault delete-policy \
    --name <key-vault> \
    --object-id $storage_account_principal
```

## <a name="disable-customer-managed-keys"></a>カスタマー マネージド キーを無効にする

カスタマー マネージド キーを無効にすると、ストレージ アカウントは、Microsoft が管理するキーを使用して再び暗号化されます。 カスタマー マネージド キーを無効にするには、次の例に示すように、[az storage account update](/cli/azure/storage/account#az-storage-account-update) を呼び出して、`--encryption-key-source parameter` を `Microsoft.Storage` に設定します。 角かっこ内のプレースホルダー値を独自の値に置き換え、前の例で定義した変数を使用してください。

```azurecli-interactive
az storage account update
    --name <storage-account> \
    --resource-group <resource_group> \
    --encryption-key-source Microsoft.Storage
```

## <a name="next-steps"></a>次のステップ

- [保存データに対する Azure Storage 暗号化](storage-service-encryption.md) 
- [Azure Key Vault とは](https://docs.microsoft.com/azure/key-vault/key-vault-overview)
