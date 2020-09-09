---
title: データの二重暗号化のためにインフラストラクチャ暗号化を有効にしてストレージ アカウントを作成する
titleSuffix: Azure Storage
description: データのセキュリティ保護についてより高いレベルの保証が必要なお客様は、Azure Storage インフラストラクチャ レベルで 256 ビットの AES 暗号化を有効にすることもできます。 インフラストラクチャ暗号化が有効な場合、ストレージ アカウントのデータは、2 つの異なる暗号化アルゴリズムと 2 つの異なるキーを使用して 2 回暗号化されます。
services: storage
author: tamram
ms.service: storage
ms.date: 07/08/2020
ms.topic: conceptual
ms.author: tamram
ms.reviewer: ozgun
ms.subservice: common
ms.custom: references_regions
ms.openlocfilehash: edeb184af1c1260a456ed3de7064805526629de8
ms.sourcegitcommit: f844603f2f7900a64291c2253f79b6d65fcbbb0c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/10/2020
ms.locfileid: "86225025"
---
# <a name="create-a-storage-account-with-infrastructure-encryption-enabled-for-double-encryption-of-data"></a>データの二重暗号化のためにインフラストラクチャ暗号化を有効にしてストレージ アカウントを作成する

Azure Storage では、使用可能なブロック暗号の中でも最強クラスの 1 つである 256 ビット AES 暗号化を使用して、サービス レベルでストレージ アカウントのすべてのデータが自動的に暗号化されています。また、FIPS 140-2 に準拠しています。 データのセキュリティ保護についてより高いレベルの保証が必要なお客様は、Azure Storage インフラストラクチャ レベルで 256 ビットの AES 暗号化を有効にすることもできます。 インフラストラクチャ暗号化が有効な場合、ストレージ アカウントのデータは、2 つの異なる暗号化アルゴリズムと 2 つの異なるキーを使用して、2 回 &mdash; サービス レベルで 1 回、インフラストラクチャ レベルで 1 回 &mdash; 暗号化されます。 Azure Storage データの二重暗号化を使用すると、暗号化アルゴリズムまたはキーのいずれかが侵害される可能性があるシナリオから保護されます。 このシナリオでは、追加の暗号化レイヤーによって引き続きデータが保護されます。

サービスレベルの暗号化では、Azure Key Vault での Microsoft マネージド キーまたはカスタマー マネージド キーの使用をサポートしています。 インフラストラクチャレベルの暗号化は Microsoft マネージド キーに依存し、常に別のキーが使用されます。 Azure Storage 暗号化によるキー管理の詳細については、「[暗号化キーの管理について](storage-service-encryption.md#about-encryption-key-management)」を参照してください。

データを二重に暗号化するには、まずインフラストラクチャ暗号化用に構成されたストレージ アカウントを作成する必要があります。 この記事では、インフラストラクチャ暗号化を有効にするストレージ アカウントを作成する方法について説明します。

## <a name="about-the-feature"></a>機能について

インフラストラクチャ暗号化が有効なストレージ アカウントを作成するには、まずこの特徴を Azure で使用するために登録する必要があります。 容量が限られているため、アクセス権のリクエストが承認されるまでに数か月かかる場合があることにご注意ください。

次のリージョンでは、インフラストラクチャ暗号化を有効にしてストレージ アカウントを作成することができます。

- 米国東部
- 米国中南部
- 米国西部 2

### <a name="register-to-use-infrastructure-encryption"></a>インフラストラクチャ暗号化を使用するための登録

Azure Storage でインフラストラクチャ暗号化を使用するために登録するには、PowerShell または Azure CLI を使用します。

# <a name="powershell"></a>[PowerShell](#tab/powershell)

PowerShell を使用して登録を行うには、[Register-AzProviderFeature](/powershell/module/az.resources/register-azproviderfeature) コマンドを呼び出します。

```powershell
Register-AzProviderFeature -ProviderNamespace Microsoft.Storage `
    -FeatureName AllowRequireInfraStructureEncryption
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Azure CLI を使用して登録を行うには、[az feature register](/cli/azure/feature#az-feature-register) コマンドを呼び出します。

```azurecli
az feature register --namespace Microsoft.Storage \
    --name AllowRequireInfraStructureEncryption
```

# <a name="template"></a>[テンプレート](#tab/template)

該当なし

---

### <a name="check-the-status-of-your-registration"></a>登録の状態を確認する

インフラストラクチャ暗号化の登録の状態を確認するには、PowerShell または Azure CLI を使用します。

# <a name="powershell"></a>[PowerShell](#tab/powershell)

PowerShell での登録の状態を確認するには、[Get-AzProviderFeature](/powershell/module/az.resources/get-azproviderfeature) コマンドを呼び出します。

```powershell
Get-AzProviderFeature -ProviderNamespace Microsoft.Storage `
    -FeatureName AllowRequireInfraStructureEncryption
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Azure CLI での登録の状態を確認するには、[az feature](/cli/azure/feature#az-feature-show) コマンドを呼び出します。

```azurecli
az feature show --namespace Microsoft.Storage \
    --name AllowRequireInfraStructureEncryption
```

# <a name="template"></a>[テンプレート](#tab/template)

該当なし

---

### <a name="re-register-the-azure-storage-resource-provider"></a>Azure Storage リソース プロバイダーを再登録する

登録が承認されたら、Azure Storage リソース プロバイダーを再登録する必要があります。 PowerShell または Azure CLI を使用して、リソース プロバイダーを再登録します。

# <a name="powershell"></a>[PowerShell](#tab/powershell)

PowerShell でリソース プロバイダーを再登録するには、[Register-AzResourceProvider](/powershell/module/az.resources/register-azresourceprovider) コマンドを呼び出します。

```powershell
Register-AzResourceProvider -ProviderNamespace 'Microsoft.Storage'
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Azure CLI でリソース プロバイダーを再登録するには、[az provider register](/cli/azure/provider#az-provider-register) コマンドを呼び出します。

```azurecli
az provider register --namespace 'Microsoft.Storage'
```

# <a name="template"></a>[テンプレート](#tab/template)

該当なし

---

## <a name="create-an-account-with-infrastructure-encryption-enabled"></a>インフラストラクチャ暗号化を有効にしてアカウントを作成する

アカウントの作成時に、インフラストラクチャ暗号化を使用するようにストレージ アカウントを構成する必要があります。 アカウントの作成後にインフラストラクチャ暗号化を有効または無効にすることはできません。

ストレージ アカウントの種類は汎用 v2 にする必要があります。 ストレージ アカウントを作成し、PowerShell、Azure CLI、または Azure Resource Manager テンプレートを使用してインフラストラクチャ暗号化を有効にするように構成することができます。

# <a name="powershell"></a>[PowerShell](#tab/powershell)

PowerShell を使用してインフラストラクチャ暗号化を有効にしたストレージ アカウントを作成するには、[Az.Storage PowerShell モジュール](https://www.powershellgallery.com/packages/Az.Storage) バージョン 2.2.0 以降がインストールされていることを確認します。 詳しくは、[Azure PowerShell のインストール](/powershell/azure/install-az-ps)に関する記事をご覧ください。

次に、[New-AzStorageAccount](/powershell/module/az.storage/new-azstorageaccount) コマンドを呼び出して、汎用 v2 ストレージ アカウントを作成します。 インフラストラクチャ暗号化を有効にするには、`-RequireInfrastructureEncryption` オプションを含めます。

次の例は、読み取りアクセス geo 冗長ストレージ (RA-GRS) 用に構成され、データの二重暗号化のためにインフラストラクチャ暗号化が有効な汎用 v2 ストレージ アカウントを作成する方法を示しています。 かっこ内のプレースホルダー値を独自の値に置き換えることを忘れないでください。

```powershell
New-AzStorageAccount -ResourceGroupName <resource_group> `
    -AccountName <storage-account> `
    -Location <location> `
    -SkuName "Standard_RAGRS" `
    -Kind StorageV2 `
    -RequireInfrastructureEncryption
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Azure CLI を使用して、インフラストラクチャ暗号化が有効なストレージ アカウントを作成するには、Azure CLI バージョン 2.8.0 以降がインストールされていることを確認します。 詳細については、「 [Azure CLI のインストール](/cli/azure/install-azure-cli)」を参照してください。

次に、[az storage account create](/cli/azure/storage/account#az-storage-account-create) コマンドを呼び出して汎用 v2 ストレージ アカウントを作成し、`--require-infrastructure-encryption option` を含めてインフラストラクチャ暗号化を有効にします。

次の例は、読み取りアクセス geo 冗長ストレージ (RA-GRS) 用に構成され、データの二重暗号化のためにインフラストラクチャ暗号化が有効な汎用 v2 ストレージ アカウントを作成する方法を示しています。 かっこ内のプレースホルダー値を独自の値に置き換えることを忘れないでください。

```azurecli-interactive
az storage account create \
    --name <storage-account> \
    --resource-group <resource-group> \
    --location <location> \
    --sku Standard_RAGRS \
    --kind StorageV2 \
    --require-infrastructure-encryption
```

# <a name="template"></a>[テンプレート](#tab/template)

次の JSON の例では、読み取りアクセス geo 冗長ストレージ (RA-GRS) 用に構成され、データの二重暗号化のためにインフラストラクチャ暗号化が有効な汎用 v2 ストレージ アカウントを作成します。 かっこ内のプレースホルダー値を独自の値に置き換えることを忘れないでください。

```json
"resources": [
    {
        "type": "Microsoft.Storage/storageAccounts",
        "apiVersion": "2019-06-01",
        "name": "[parameters('<storage-account>')]",
        "location": "[parameters('<location>')]",
        "dependsOn": [],
        "tags": {},
        "sku": {
            "name": "[parameters('Standard_RAGRS')]"
        },
        "kind": "[parameters('StorageV2')]",
        "properties": {
            "accessTier": "[parameters('<accessTier>')]",
            "supportsHttpsTrafficOnly": "[parameters('supportsHttpsTrafficOnly')]",
            "largeFileSharesState": "[parameters('<largeFileSharesState>')]",
            "encryption": {
                "keySource": "Microsoft.Storage",
                "requireInfrastructureEncryption": true,
                "services": {
                    "blob": { "enabled": true },
                    "file": { "enabled": true }
              }
            }
        }
    }
],
```

---

## <a name="verify-that-infrastructure-encryption-is-enabled"></a>インフラストラクチャ暗号化が有効であることを確認します

# <a name="powershell"></a>[PowerShell](#tab/powershell)

ストレージ アカウントでインフラストラクチャ暗号化が有効なことを確認するには、[Get-AzStorageAccount](/powershell/module/az.storage/get-azstorageaccount) コマンドを呼び出します。 このコマンドによって、ストレージ アカウントのプロパティとその値のセットが返されます。 `Encryption` プロパティ内の `RequireInfrastructureEncryption` フィールドを取得し、それが `True` に設定されていることを確認します。

次の例では、`RequireInfrastructureEncryption` プロパティの値を取得します。 山かっこ内のプレースホルダー値は、実際の値に置き換えてください。

```powershell
$account = Get-AzStorageAccount -ResourceGroupName <resource-group> `
    -StorageAccountName <storage-account>
$account.Encryption.RequireInfrastructureEncryption
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

ストレージ アカウントでインフラストラクチャ暗号化が有効なことを確認するには、[az storage account show](/cli/azure/storage/account#az-storage-account-show) コマンドを呼び出します。 このコマンドによって、ストレージ アカウントのプロパティとその値のセットが返されます。 `encryption` プロパティ内の `requireInfrastructureEncryption` フィールドを探し、それが `true` に設定されていることを確認します。

次の例では、`requireInfrastructureEncryption` プロパティの値を取得します。 山かっこ内のプレースホルダー値は、実際の値に置き換えてください。

```azurecli-interactive
az storage account show /
    --name <storage-account> /
    --resource-group <resource-group>
```

# <a name="template"></a>[テンプレート](#tab/template)

該当なし

---

## <a name="next-steps"></a>次のステップ

- [保存データに対する Azure Storage 暗号化](storage-service-encryption.md)
- [Azure Key Vault で顧客マネージド キーを使用して Azure Storage の暗号化を管理する](encryption-customer-managed-keys.md)