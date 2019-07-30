---
title: Azure CLI から Azure Storage 暗号化用にカスタマー マネージド キーを構成する
description: Azure CLI を使用して Azure Storage 暗号化用にカスタマー マネージド キーを構成する方法について説明します。 カスタマー マネージド キーを使用すると、アクセス制御の作成、ローテーション、無効化、および取り消しを行うことができます。
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 06/24/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: 925b69e064e260a78a102a068f052ad7d396c380
ms.sourcegitcommit: a7ea412ca4411fc28431cbe7d2cc399900267585
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/25/2019
ms.locfileid: "67357053"
---
# <a name="configure-customer-managed-keys-for-azure-storage-encryption-from-azure-cli"></a>Azure CLI から Azure Storage 暗号化用にカスタマー マネージド キーを構成する

[!INCLUDE [storage-encryption-configure-keys-include](../../../includes/storage-encryption-configure-keys-include.md)]

この記事では、Azure CLI を使用して、カスタマー マネージド キーを使用するキー コンテナーを構成する方法について説明します。

## <a name="assign-an-identity-to-the-storage-account"></a>ストレージ アカウントに ID を割り当てる

お使いのストレージ アカウントでカスタマー マネージド キーを有効にするには、まず、そのストレージ アカウントにシステム割り当てマネージド ID を割り当てます。 このマネージド ID を使って、キー コンテナーへのアクセス許可をストレージ アカウントに付与します。

Azure CLI を使用してマネージド ID を割り当てるには、[az storage account update](/cli/azure/storage/account#az-storage-account-update) を呼び出します。 山かっこ内のプレースホルダーをご自分の値に置き換えることを忘れないようにしてください。

```azurecli-interactive
az account set --subscription <subscription-id>

az storage account update \
    --name <storage-account> \
    --resource-group <resource_group> \
    --assign-identity
```

Azure CLI を使用してシステム割り当てマネージド ID を構成する方法について詳しくは、「[Azure CLI を使用して Azure VM 上に Azure リソースのマネージド ID を構成する](../../active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vm.md)」をご覧ください。

## <a name="create-a-new-key-vault"></a>新しいキー コンテナーを作成する

Azure Storage 暗号化用にカスタマー マネージド キーを格納するために使用するキー コンテナーでは、キーを保護するための 2 つの設定、 **[論理的な削除]** と **[Do Not Purge]\(消去しない\)** を有効にする必要があります。 これらの設定を有効にした状態で、PowerShell または Azure CLI を使用して新しいキー コンテナーを作成するには、次のコマンドを実行します。 山かっこ内のプレースホルダーをご自分の値に置き換えることを忘れないようにしてください。 

Azure CLI を使用して新しいキー コンテナーを作成するには、[az keyvault create](/cli/azure/keyvault#az-keyvault-create) を呼び出します。 山かっこ内のプレースホルダーをご自分の値に置き換えることを忘れないようにしてください。

```azurecli-interactive
az keyvault create \
    --name <key-vault> \
    --resource-group <resource_group> \
    --location <region> \
    --enable-soft-delete \
    --enable-purge-protection
```

## <a name="configure-the-key-vault-access-policy"></a>キー コンテナーのアクセス ポリシーを構成する

次に、キー コンテナーのアクセス ポリシーを構成し、ストレージ アカウントからアクセスできるようにします。 この手順では、前の手順でストレージ アカウントに割り当てたマネージド ID を使用します。

キー コンテナーのアクセス ポリシーを設定するには、[az keyvault set-policy](/cli/azure/keyvault#az-keyvault-set-policy) を呼び出します。 山かっこ内のプレースホルダーをご自分の値に置き換えることを忘れないようにしてください。

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
    --key-permissions get recover unwrapKey wrapKey
```

## <a name="create-a-new-key"></a>新しいキーを作成する

次に、キー コンテナーにキーを作成します。 キーを作成するには、[az keyvault key create](/cli/azure/keyvault/key#az-keyvault-key-create) を呼び出します。 山かっこ内のプレースホルダーをご自分の値に置き換えることを忘れないようにしてください。

```azurecli-interactive
az keyvault key create
    --name <key> \
    --vault-name <key-vault>
```

## <a name="configure-encryption-with-customer-managed-keys"></a>カスタマー マネージド キーによる暗号化を有効にする

既定では、Azure Storage の暗号化には Microsoft マネージド キーを使用します。 カスタマー マネージド キーを使うように Azure ストレージ アカウントを構成し、そのストレージ アカウントに関連付けるキーを指定します。

ストレージ アカウントの暗号化設定を更新するには、[az storage account update](/cli/azure/storage/account#az-storage-account-update) を呼び出します。 この例では、キー コンテナーの URI とキーのバージョンの照会も行います。どちらの値も、キーをストレージ アカウントに関連付けるために必要です。 山かっこ内のプレースホルダーをご自分の値に置き換えることを忘れないようにしてください。

```azurecli-interactive
key_vault_uri=$(az keyvault show \
    --name <key-vault> \
    --resource-group <resource_group> \
    --query properties.vaultUri \
    --output tsv)
key_version=$(az keyvault key list-versions \
    --name <key> \
    --vault-name <key-vault> \
    --query [].kid \
    --output tsv | cut -d '/' -f 6)
az storage account update 
    --name <storage-account> \
    --resource-group <resource_group> \
    --encryption-key-name <key> \
    --encryption-key-version $key_version \
    --encryption-key-source Microsoft.Keyvault \
    --encryption-key-vault $key_vault_uri
```

## <a name="update-the-key-version"></a>キーのバージョンを更新する

キーの新しいバージョンを作成した場合、その新しいバージョンを使用するには、ストレージ アカウントを更新する必要があります。 まず、[az keyvault show](/cli/azure/keyvault#az-keyvault-show) を呼び出すことでキー コンテナーの URI を照会し、[az keyvault key list-versions](/cli/azure/keyvault/key#az-keyvault-key-list-versions) を呼び出すことでキーのバージョンを照会します。 次に、[az storage account update](/cli/azure/storage/account#az-storage-account-update) を呼び出して、キーの新しいバージョンを使用するようにストレージ アカウントの暗号化設定を更新します (前のセクションを参照)。

## <a name="next-steps"></a>次の手順

- [保存データに対する Azure Storage 暗号化](storage-service-encryption.md) 
- [Azure Key Vault とは](https://docs.microsoft.com/azure/key-vault/key-vault-whatis)
