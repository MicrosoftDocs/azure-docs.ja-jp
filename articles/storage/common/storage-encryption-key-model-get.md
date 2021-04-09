---
title: ストレージ アカウントに使用されている暗号化キー モデルを確認する
titleSuffix: Azure Storage
description: Azure portal、PowerShell、または Azure CLI を使用して、ストレージアカウントの暗号化キーがどのように管理されているかを確認します。 キーは、Microsoft (既定)、または顧客が管理できます。 顧客が管理するキーは、Azure Key Vault に格納する必要があります。
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 03/13/2020
ms.author: tamram
ms.reviewer: ozgun
ms.subservice: common
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: f59e4238c983cdb336500a68c52730ae5346b1c7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "91612423"
---
# <a name="determine-which-azure-storage-encryption-key-model-is-in-use-for-the-storage-account"></a>ストレージアカウントに使用されている Azure Storage 暗号化キーモデルを決定する

ストレージアカウント内のデータは、Azure Storage によって自動的に暗号化されます。 Azure Storage 暗号化には、ストレージアカウントレベルで暗号化キーを管理するため、次の 2 つのオプションが用意されています。

- **Microsoft マネージド キー** 既定値では、Microsoft がストレージアカウントの暗号化に使用されるキーを管理します。
- **顧客マネージド キー** オプションで、ストレージアカウントの暗号化キーの管理を選択できます。 顧客が管理するキーは、Azure Key Vault に格納する必要があります。

また、一部の Blob ストレージ操作について、個々の要求レベルで暗号化キーを提供することもできます。 要求で暗号化キーを指定すると、そのキーは、ストレージアカウントでアクティブになっている暗号化キーよりも優先されます。 詳細については、「[Blob storage に対する要求で顧客が指定したキーを指定する](../blobs/storage-blob-customer-provided-key.md)」を参照してください。

暗号化キーの詳細については、[保存データの Azure Storage暗号化](storage-service-encryption.md) をご覧ください。

## <a name="check-the-encryption-key-model-for-the-storage-account"></a>ストレージ アカウントの暗号化キーモデルを確認する

ストレージアカウントが Microsoft によって管理されているキーまたはユーザーが管理するキーを暗号化に使用しているかどうかを判断するには、次のいずれかの方法を使用します。

# <a name="azure-portal"></a>[Azure Portal](#tab/portal)

Azure portal を使用してストレージアカウントの暗号化モデルを確認するには、次の手順を実行します。

1. Azure Portal のストレージ アカウントに移動します。
1. **[暗号化]** 設定を選択し、設定を書き留めます。

次の画像は、Microsoft マネージド キーで暗号化されているストレージ アカウントを示しています。

![Microsoft のマネージド キーで暗号化されたアカウントを表示する](media/storage-encryption-key-model-get/microsoft-managed-encryption-key-setting-portal.png)

そして、次の画像は、顧客マネージド キーで暗号化されているストレージ アカウントを示しています。

![Azure portal の暗号化キー設定を示すスクリーンショット](media/storage-encryption-key-model-get/customer-managed-encryption-key-setting-portal.png)

# <a name="powershell"></a>[PowerShell](#tab/powershell)

PowerShell を使用してストレージアカウントの暗号化モデルを確認するには、[Get-AzStorageAccount](/powershell/module/az.storage/get-azstorageaccount) コマンドを呼び出して、そのアカウントの **KeySource** プロパティを確認します。

```powershell
$account = Get-AzStorageAccount -ResourceGroupName <resource-group> `
    -Name <storage-account>
$account.Encryption.KeySource
```

**KeySource** プロパティの値が `Microsoft.Storage` の場合、アカウントは Microsoft マネージド キーで暗号化されます。 **KeySource** プロパティの値が `Microsoft.Keyvault` の場合、アカウントは顧客マネージド キーで暗号化されます。

# <a name="azure-cli"></a>[Azure CLI](#tab/cli)

Azure CLIを使用してストレージアカウントの暗号化モデルを確認するには、[az storage account show](/cli/azure/storage/account#az-storage-account-show) コマンドを呼び出して、アカウントの **keySource** プロパティを確認します。

```azurecli-interactive
key_source=$(az storage account show \
    --name <storage-account> \
    --resource-group <resource-group> \
    --query encryption.keySource \
    --output tsv)
```

**keySource** プロパティの値が `Microsoft.Storage` の場合、アカウントは Microsoft マネージド キーで暗号化されます。 **KeySource** プロパティの値が `Microsoft.Keyvault` の場合、アカウントは顧客マネージド キーで暗号化されます。

---

## <a name="next-steps"></a>次のステップ

- [保存データに対する Azure Storage 暗号化](storage-service-encryption.md)
- [Azure Storage の暗号化のためのカスタマー マネージド キー](customer-managed-keys-overview.md)
