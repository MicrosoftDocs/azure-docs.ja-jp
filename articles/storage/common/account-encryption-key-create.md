---
title: テーブルとキューのカスタマーマネージド キーがサポートされるアカウントを作成する
titleSuffix: Azure Storage
description: テーブルとキューのカスタマーマネージド キーの構成がサポートされるストレージ アカウントを作成する方法について説明します。 Azure CLI または Azure Resource Manager テンプレートを使って、Azure Storage 暗号化にアカウント暗号化キーを使用するストレージ アカウントを作成します。 これにより、そのアカウントのカスタマーマネージド キーを構成できるようになります。
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 02/05/2020
ms.author: tamram
ms.reviewer: ozgun
ms.subservice: common
ms.custom: devx-track-azurecli, devx-track-azurepowershell
ms.openlocfilehash: 8150375eff98374e21d200d98c04158b07f1c243
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "92789694"
---
# <a name="create-an-account-that-supports-customer-managed-keys-for-tables-and-queues"></a>テーブルとキューのカスタマーマネージド キーがサポートされるアカウントを作成する

Azure Storage は、保存されているストレージ アカウント内のすべてのデータを暗号化します。 既定では、Queue storage と Table Storage で使用されるキーは、サービスに対してスコープ指定されていて、Microsoft によって管理されます。 また、カスタマーマネージド キーを使用してキューまたはテーブルのデータを暗号化することもできます。 キューとテーブルでカスタマーマネージド キーを使用するには、サービスではなくアカウントに対してスコープ指定された暗号化キーを使用するストレージ アカウントを最初に作成しなければなりません。 キューとテーブルのデータにアカウント暗号化キーを使用するアカウントを作成した後、そのストレージ アカウントのカスタマーマネージド キーを構成できます。

この記事では、アカウントに対してスコープ指定されたキーを使用するストレージ アカウントの作成方法について説明します。 アカウントが作成されると、最初は Microsoft がアカウント キーを使用してそのアカウントのデータを暗号化します。また、キーは Microsoft によって管理されます。 後からアカウントのカスタマーマネージド キーを構成することで、独自のキーの指定、キー バージョンの更新、キーのローテーション、アクセス制御の取り消しを行えるといった利点を活用できます。

## <a name="about-the-feature"></a>機能について

Queue storage と Table Storage でアカウント暗号化キーを使用するストレージ アカウントを作成するには、最初に、Azure でこの機能を使用するための登録を行う必要があります。 容量が限られているため、アクセス権のリクエストが承認されるまでに数か月かかる場合があることにご注意ください。

Queue storage と Table Storage でアカウント暗号化キーを使用するストレージ アカウントを作成できるのは、次のリージョンになります。

- 米国東部
- 米国中南部
- 米国西部 2  

### <a name="register-to-use-the-account-encryption-key"></a>アカウント暗号化キーを使用するための登録を行う

Queue storage または Table storage でアカウント暗号化キーを使用するための登録を行うには、PowerShell または Azure CLI を使用します。

# <a name="powershell"></a>[PowerShell](#tab/powershell)

PowerShell を使用して登録を行うには、[Register-AzProviderFeature](/powershell/module/az.resources/register-azproviderfeature) コマンドを呼び出します。

```powershell
Register-AzProviderFeature -ProviderNamespace Microsoft.Storage `
    -FeatureName AllowAccountEncryptionKeyForQueues
Register-AzProviderFeature -ProviderNamespace Microsoft.Storage `
    -FeatureName AllowAccountEncryptionKeyForTables
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Azure CLI を使用して登録を行うには、[az feature register](/cli/azure/feature#az-feature-register) コマンドを呼び出します。

```azurecli
az feature register --namespace Microsoft.Storage \
    --name AllowAccountEncryptionKeyForQueues
az feature register --namespace Microsoft.Storage \
    --name AllowAccountEncryptionKeyForTables
```

# <a name="template"></a>[テンプレート](#tab/template)

該当なし

---

### <a name="check-the-status-of-your-registration"></a>登録の状態を確認する

Queue storage または Table storage の登録の状態を確認するには、PowerShell または Azure CLI を使用します。

# <a name="powershell"></a>[PowerShell](#tab/powershell)

PowerShell での登録の状態を確認するには、[Get-AzProviderFeature](/powershell/module/az.resources/get-azproviderfeature) コマンドを呼び出します。

```powershell
Get-AzProviderFeature -ProviderNamespace Microsoft.Storage `
    -FeatureName AllowAccountEncryptionKeyForQueues
Get-AzProviderFeature -ProviderNamespace Microsoft.Storage `
    -FeatureName AllowAccountEncryptionKeyForTables
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Azure CLI での登録の状態を確認するには、[az feature](/cli/azure/feature#az-feature-show) コマンドを呼び出します。

```azurecli
az feature show --namespace Microsoft.Storage \
    --name AllowAccountEncryptionKeyForQueues
az feature show --namespace Microsoft.Storage \
    --name AllowAccountEncryptionKeyForTables
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

## <a name="create-an-account-that-uses-the-account-encryption-key"></a>アカウント暗号化キーを使用するアカウントを作成する

新しいストレージ アカウントの作成時に、キューとテーブルにアカウント暗号化キーを使用するようそのストレージ アカウントを構成する必要があります。 アカウントの作成後は、暗号化キーのスコープを変更することはできません。

ストレージ アカウントの種類は汎用 v2 にする必要があります。 Azure CLI または Azure Resource Manager テンプレートのいずれかを使用して、ストレージ アカウントを作成し、アカウント暗号化キーを使用するよう構成できます。

> [!NOTE]
> アカウント暗号化キーを使用してデータを暗号化するための構成を、ストレージ アカウントの作成時に任意で行えるのは、Queue storage と Table Storage だけです。 Blob Storage と Azure Files では、常にアカウント暗号化キーを使用してデータを暗号化します。

# <a name="powershell"></a>[PowerShell](#tab/powershell)

PowerShell を使用して、アカウント暗号化キーを使用するストレージ アカウントを作成するには、Azure PowerShell モジュール バージョン 3.4.0 以降がインストールされていることを確認してください。 詳しくは、[Azure PowerShell モジュールのインストール](/powershell/azure/install-az-ps)に関するページを参照してください。

次に、適切なパラメーターを指定して [New-AzStorageAccount](/powershell/module/az.storage/new-azstorageaccount) コマンドを呼び出し、汎用 v2 ストレージ アカウントを作成します。

- アカウント暗号化キーを使用して Queue storage のデータを暗号化するには、`-EncryptionKeyTypeForQueue` オプションを指定して、その値を `Account` に設定します。
- アカウント暗号化キーを使用して Table Storage のデータを暗号化するには、`-EncryptionKeyTypeForTable` オプションを指定して、その値を `Account` に設定します。

次の例で示すのは、読み取りアクセス geo 冗長ストレージ (RA-GRS) 用に構成されていて、Queue storage と Table Storage の両方でデータの暗号化にアカウント暗号化キーを使用する汎用 v2 ストレージ アカウントを作成する方法です。 かっこ内のプレースホルダー値を独自の値に置き換えることを忘れないでください。

```powershell
New-AzStorageAccount -ResourceGroupName <resource_group> `
    -AccountName <storage-account> `
    -Location <location> `
    -SkuName "Standard_RAGRS" `
    -Kind StorageV2 `
    -EncryptionKeyTypeForTable Account `
    -EncryptionKeyTypeForQueue Account
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Azure CLI を使用して、アカウント暗号化キーを使用するストレージ アカウントを作成するには、Azure CLI バージョン 2.0.80 以降がインストールされていることを確認してください。 詳細については、「 [Azure CLI のインストール](/cli/azure/install-azure-cli)」を参照してください。

次に、適切なパラメーターを指定して [az storage account create](/cli/azure/storage/account#az-storage-account-create) コマンドを呼び出し、汎用 v2 ストレージ アカウントを作成します。

- アカウント暗号化キーを使用して Queue storage のデータを暗号化するには、`--encryption-key-type-for-queue` オプションを指定して、その値を `Account` に設定します。
- アカウント暗号化キーを使用して Table Storage のデータを暗号化するには、`--encryption-key-type-for-table` オプションを指定して、その値を `Account` に設定します。

次の例で示すのは、読み取りアクセス geo 冗長ストレージ (RA-GRS) 用に構成されていて、Queue storage と Table Storage の両方でデータの暗号化にアカウント暗号化キーを使用する汎用 v2 ストレージ アカウントを作成する方法です。 かっこ内のプレースホルダー値を独自の値に置き換えることを忘れないでください。

```azurecli
az storage account create \
    --name <storage-account> \
    --resource-group <resource-group> \
    --location <location> \
    --sku Standard_RAGRS \
    --kind StorageV2 \
    --encryption-key-type-for-table Account \
    --encryption-key-type-for-queue Account
```

# <a name="template"></a>[テンプレート](#tab/template)

次の JSON 例では、読み取りアクセス geo 冗長ストレージ (RA-GRS) 用に構成されていて、Queue storage と Table Storage の両方でデータの暗号化にアカウント暗号化キーを使用する汎用 v2 ストレージ アカウントを作成します。 山かっこ内のプレースホルダー値は、実際の値に置き換えてください。

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
                "services": {
                    "queue": {
                        "keyType": "Account"
                    },
                    "table": {
                        "keyType": "Account"
                    }
                },
                "keySource": "Microsoft.Storage"
            }
        }
    }
],
```

---

アカウントの暗号化キーに依存するアカウントを作成した後、Azure Key Vault または Key Vault Managed Hardware Security Model (HSM) (プレビュー) に格納されているカスタマーマネージド キーを構成できます。 カスタマーマネージド キーをキー コンテナーに格納する方法については、「[Azure Key Vault に格納されているカスタマー マネージド キーによる暗号化を構成する](customer-managed-keys-configure-key-vault.md)」を参照してください。 マネージド HSM にカスタマーマネージド キーを格納する方法については、「[Azure Key Vault Managed HSM (プレビュー) に格納されているカスタマー マネージド キーによる暗号化を構成する](customer-managed-keys-configure-key-vault-hsm.md)」を参照してください。

## <a name="verify-the-account-encryption-key"></a>アカウント暗号化キーを確認する

ストレージ アカウント内のサービスでアカウント暗号化キーが使用されていることを確認するには、Azure CLI の [az storage account](/cli/azure/storage/account#az-storage-account-show) コマンドを呼び出します。 このコマンドによって、ストレージ アカウントのプロパティとその値のセットが返されます。 暗号化プロパティ内で各サービスの `keyType` フィールドを探し、`Account` に設定されていることを確認します。

# <a name="powershell"></a>[PowerShell](#tab/powershell)

ストレージ アカウント内のサービスでアカウント暗号化キーが使用されていることを確認するには、[Get-AzStorageAccount](/powershell/module/az.storage/get-azstorageaccount) コマンドを呼び出します。 このコマンドによって、ストレージ アカウントのプロパティとその値のセットが返されます。 `Encryption` プロパティ内で各サービスの `KeyType` フィールドを探し、`Account` に設定されていることを確認します。

```powershell
$account = Get-AzStorageAccount -ResourceGroupName <resource-group> `
    -StorageAccountName <storage-account>
$account.Encryption.Services.Queue
$account.Encryption.Services.Table
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

ストレージ アカウント内のサービスでアカウント暗号化キーが使用されていることを確認するには、[az storage account show](/cli/azure/storage/account#az-storage-account-show) コマンドを呼び出します。 このコマンドによって、ストレージ アカウントのプロパティとその値のセットが返されます。 暗号化プロパティ内で各サービスの `keyType` フィールドを探し、`Account` に設定されていることを確認します。

```azurecli
az storage account show /
    --name <storage-account> /
    --resource-group <resource-group>
```

# <a name="template"></a>[テンプレート](#tab/template)

該当なし

---

## <a name="next-steps"></a>次のステップ

- [保存データに対する Azure Storage 暗号化](storage-service-encryption.md)
- [Azure Storage の暗号化のためのカスタマー マネージド キー](customer-managed-keys-overview.md)
- [Azure Key Vault とは](../../key-vault/general/overview.md)