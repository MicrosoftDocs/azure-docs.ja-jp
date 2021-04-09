---
title: Azure Key Vault Managed HSM (プレビュー) に格納されているカスタマー マネージド キーによる暗号化を構成する
titleSuffix: Azure Storage
description: Azure CLI を使用して、Azure Key Vault Managed HSM (プレビュー) に格納されているカスタマー マネージド キーによる Azure Storage 暗号化を構成する方法について説明します。
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 03/05/2021
ms.author: tamram
ms.reviewer: ozgun
ms.subservice: common
ms.custom: devx-track-azurepowershell, devx-track-azurecli
ms.openlocfilehash: f9be9272a898ad48f3553d4c5e48952e1fcdde81
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102218640"
---
# <a name="configure-encryption-with-customer-managed-keys-stored-in-azure-key-vault-managed-hsm-preview"></a>Azure Key Vault Managed HSM (プレビュー) に格納されているカスタマー マネージド キーによる暗号化を構成する

Azure Storage は、保存されているストレージ アカウント内のすべてのデータを暗号化します。 規定では、データは Microsoft のマネージド キーで暗号化されます。 暗号化キーをさらに制御するために、独自のキーを管理することができます。 カスタマー マネージド キーは、Azure Key Vault または Key Vault Managed Hardware Security Model (HSM) (プレビュー) に格納する必要があります。 Azure Key Vault Managed HSM は、FIPS 140-2 レベル 3 への準拠が検証された HSM です。

この記事では、Azure CLI を使用して、マネージド HSM に格納されているカスタマー マネージド キーによる暗号化を構成する方法について説明します。 キー コンテナーに格納されているカスタマー マネージド キーを使用して暗号化を構成する方法については、「[Azure Key Vault に格納されているカスタマー マネージド キーによる暗号化を構成する](customer-managed-keys-configure-key-vault.md)」を参照してください。

> [!IMPORTANT]
>
> Azure Key Vault Managed HSM に格納されているカスタマー マネージド キーによる暗号化は、現在 **プレビュー** 段階です。 ベータ版、プレビュー版、または一般提供としてまだリリースされていない Azure の機能に適用される法律条項については、「[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)」を参照してください。
>
> Azure Key Vault と Azure Key Vault Managed HSM では、構成用に同じ API と管理インターフェイスがサポートされています。

## <a name="assign-an-identity-to-the-storage-account"></a>ストレージ アカウントに ID を割り当てる

最初に、ストレージ アカウントにシステム割り当てマネージド ID を割り当てます。 このマネージド ID を使って、マネージド HSM へのアクセス許可をストレージ アカウントに付与します。 システム割り当てマネージド ID の詳細については、「[Azure リソースのマネージド ID とは](../../active-directory/managed-identities-azure-resources/overview.md)」を参照してください。

Azure CLI を使用してマネージド ID を割り当てるには、[az storage account update](/cli/azure/storage/account#az-storage-account-update) を呼び出します。 かっこ内のプレースホルダー値を独自の値に置き換えることを忘れないでください。

```azurecli
az storage account update \
    --name <storage-account> \
    --resource-group <resource_group> \
    --assign-identity
```

## <a name="assign-a-role-to-the-storage-account-for-access-to-the-managed-hsm"></a>マネージド HSM にアクセスするためのロールをストレージ アカウントに割り当てる

次に、ストレージ アカウントにマネージド HSM へのアクセス許可が付与されるように、**Managed HSM Crypto Service Encryption** ロールをストレージ アカウントのマネージド ID に割り当てます。 マネージド ID に付与する特権を最小限にするために、ロールの割り当てを個々のキーのレベルに限定することをお勧めします。

ストレージ アカウントのロールの割り当てを作成するには、[az key vault role assignment create](/cli/azure/role/assignment#az_role_assignment_create) を呼び出します。 角かっこ内のプレースホルダー値を独自の値で置き換えてください。
  
```azurecli
storage_account_principal = $(az storage account show \
    --name <storage-account> \
    --resource-group <resource-group> \
    --query identity.principalId \
    --output tsv)

az keyvault role assignment create \
    --hsm-name <hsm-name> \
    --role "Managed HSM Crypto Service Encryption" \
    --assignee $storage_account_principal \
    --scope /keys/<key-name>
```

## <a name="configure-encryption-with-a-key-in-the-managed-hsm"></a>マネージド HSM 内のキーを使用して暗号化を構成する

最後に、マネージド HSM に格納されているキーを使用するように、カスタマー マネージド キーによる Azure Storage 暗号化を構成します。 サポートされているキーの種類には、2,048、3,072、および 4,096 のサイズの RSA-HSM キーが含まれます。 マネージド HSM でカスタマー マネージド キーを使用するように暗号化を構成するには、Azure CLI 2.12.0 以降をインストールします。 詳細については、「 [Azure CLI のインストール](/cli/azure/install-azure-cli)」を参照してください。

カスタマー マネージド キーのキー バージョンを自動的に更新するには、ストレージ アカウントでカスタマー マネージド キーを使用した暗号化を構成するときに、キーのバージョンを省略します。 ストレージ アカウントの暗号化設定を更新するには、次の例に示すように [az storage account update](/cli/azure/storage/account#az_storage_account_update) を呼び出します。 `--encryption-key-source parameter` を含め、それを `Microsoft.Keyvault` に設定して、アカウントのカスタマー マネージド キーを有効にします。 角かっこ内のプレースホルダー値を独自の値で置き換えてください。

```azurecli
hsmurl = $(az keyvault show \
    --hsm-name <hsm-name> \
    --query properties.hsmUri \
    --output tsv)

az storage account update \
    --name <storage-account> \
    --resource-group <resource_group> \
    --encryption-key-name <key> \
    --encryption-key-source Microsoft.Keyvault \
    --encryption-key-vault $hsmurl
```

カスタマー マネージド キーのバージョンを手動で更新するには、ストレージ アカウントの暗号化を構成するときにキーのバージョンを含めます。

```azurecli-interactive
az storage account update
    --name <storage-account> \
    --resource-group <resource_group> \
    --encryption-key-name <key> \
    --encryption-key-version $key_version \
    --encryption-key-source Microsoft.Keyvault \
    --encryption-key-vault $hsmurl
```

キー バージョンを手動で更新する場合は、新しいバージョンを使用するようにストレージ アカウントの暗号化設定を更新する必要があります。 まず、[az keyvault show](/cli/azure/keyvault#az-keyvault-show) を呼び出すことでキー コンテナーの URI を照会し、[az keyvault key list-versions](/cli/azure/keyvault/key#az-keyvault-key-list-versions) を呼び出すことでキーのバージョンを照会します。 次に、[az storage account update](/cli/azure/storage/account#az-storage-account-update) を呼び出して、キーの新しいバージョンを使用するようにストレージ アカウントの暗号化設定を更新します (前の例を参照)。

## <a name="next-steps"></a>次のステップ

- [保存データに対する Azure Storage 暗号化](storage-service-encryption.md)
- [Azure Storage の暗号化のためのカスタマー マネージド キー](customer-managed-keys-overview.md)
