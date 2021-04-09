---
title: コンテナーの論理的な削除 (プレビュー)
titleSuffix: Azure Storage
description: コンテナーの論理的な削除 (プレビュー) では、お客様のデータを保護して、アプリケーションまたは別のストレージ アカウント ユーザーによってデータが誤って変更または削除されたときに簡単に復旧できるようにします。
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 03/05/2021
ms.author: tamram
ms.subservice: blobs
ms.custom: references_regions
ms.openlocfilehash: 54e703b096ea4e3572a6fc00aa6b7b2b99c4bcad
ms.sourcegitcommit: ba3a4d58a17021a922f763095ddc3cf768b11336
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/23/2021
ms.locfileid: "104800746"
---
# <a name="soft-delete-for-containers-preview"></a>コンテナーの論理的な削除 (プレビュー)

コンテナーの論理的な削除 (プレビュー) では、お客様のデータが誤ってまたは悪意によって削除されないように保護します。 ストレージ アカウントに対してコンテナーの論理的な削除が有効になっている場合、削除されたコンテナーとその内容は、指定した期間の間 Azure Storage に保持されます。 保持期間中は、以前に削除されたコンテナーを復元できます。 コンテナーを復元すると、コンテナーが削除されたときにその中にあった BLOB が復元されます。

BLOB データのエンド ツー エンド保護では、次のデータ保護機能を有効にすることをお勧めします。

- コンテナーの論理的な削除。削除されたコンテナーを復元します。 コンテナーの論理的な削除を有効にする方法については、「[コンテナーの論理的な削除を有効にして管理する](soft-delete-container-enable.md)」をご覧ください。
- BLOB のバージョン管理。以前のバージョンの BLOB が自動的に維持されます。 BLOB のバージョン管理が有効になっている場合は、以前のバージョンの BLOB を復元し、データが誤って変更または削除された場合に復旧することができます。 BLOB のバージョン管理を有効にする方法については、「[BLOB のバージョン管理を有効にして管理する](versioning-enable.md)」をご覧ください。
- BLOB の論理的な削除。削除された BLOB またはバージョンを復元します。 BLOB の論理的な削除も有効にする方法については、[BLOB の論理的な削除の有効化と管理](soft-delete-blob-enable.md)に関する記事をご覧ください。

> [!IMPORTANT]
> コンテナーの論理的な削除は、現在 "**プレビュー**" 段階です。 ベータ版、プレビュー版、または一般提供としてまだリリースされていない Azure の機能に適用される法律条項については、「[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)」を参照してください。

## <a name="how-container-soft-delete-works"></a>コンテナーの論理的な削除のしくみ

コンテナーの論理的な削除を有効にすると、削除されたコンテナーの保持期間を 1 - 365 日の範囲で指定できます。 既定の保有期間は 7 日です。 保持期間中は、**コンテナーの復元** 操作を呼び出せば、削除されたコンテナーを復旧することができます。

コンテナーを復元すると、コンテナーの BLOB と BLOB バージョンも復元されます。 ただし、コンテナー自体が削除されている場合にのみ、コンテナーの論理的な削除を使用して BLOB を復元できます。 親コンテナーが削除されていないときに削除された BLOB を復元するには、BLOB の論理的な削除または BLOB のバージョン管理を使用する必要があります。

> [!WARNING]
> コンテナーの論理的な削除では、コンテナー全体、および削除時に含まれていた BLOB のみを復元することができます。 コンテナーの論理的な削除を使用して、削除された BLOB をコンテナー内で復元することはできません。

次の図は、コンテナーの論理的な削除が有効になっている場合に、削除されたコンテナーを復元する方法を示しています。

:::image type="content" source="media/soft-delete-container-overview/container-soft-delete-diagram.png" alt-text="論理的に削除されたコンテナーを復元する方法を示す図":::

コンテナーを復元するときに、元の名前が再利用されていない場合は、その名前で復元することができます。 元のコンテナー名が使用されている場合は、新しい名前でコンテナーを復元できます。

保持期間が経過すると、コンテナーは Azure Storage から完全に削除され、復旧できなくなります。 コンテナーが削除された時点で保持期間のクロックが開始します。 保持期間はいつでも変更できますが、更新された保持期間は、新しく削除されたコンテナーにのみ適用されることに注意してください。 以前に削除されたコンテナーは、そのコンテナーが削除された時点で有効になった保持期間に基づいて完全に削除されます。

コンテナーの論理的な削除を無効にしても、以前に論理的に削除されたコンテナーが完全に削除されることはありません。 論理的に削除されたコンテナーは、そのコンテナーが削除された時点で有効になった保持期間が経過すると完全に削除されます。

> [!IMPORTANT]
> コンテナーの論理的な削除では、ストレージ アカウントの削除からは保護されませんが、そのアカウント内のコンテナーの削除からは保護されます。 ストレージ アカウントを削除から保護するには、ストレージ アカウント リソースに対してロックを構成します。 Azure Resource Manager リソースのロックの詳細については、「[リソースのロックによる予期せぬ変更の防止](../../azure-resource-manager/management/lock-resources.md)」を参照してください。

## <a name="about-the-preview"></a>プレビューについて

すべての Azure リージョンで、コンテナーの論理的な削除をプレビューでご利用いただけます。

バージョン 2019-12-12 以降の Azure Storage REST API では、コンテナーの論理的な削除がサポートされています。

### <a name="storage-account-support"></a>ストレージ アカウントのサポート

コンテナーの論理的な削除は、次の種類のストレージ アカウントで使用できます。

- 汎用 v2 および v1 ストレージ アカウント
- ブロック BLOB ストレージ アカウント
- BLOB ストレージ アカウント

階層型名前空間が Azure Data Lake Storage Gen2 で使用可能になっているストレージ アカウントもサポートされています。

### <a name="register-for-the-preview"></a>プレビューに登録する

コンテナーの論理的な削除のプレビューに登録するには、PowerShell または Azure CLI を使用して、サブスクリプションに機能を登録するための要求を送信します。 要求が承認された後、新規または既存の汎用 v2、BLOB ストレージ、または Premium ブロック BLOB ストレージ アカウントを使用して、コンテナーの論理的な削除を有効にすることができます。

# <a name="powershell"></a>[PowerShell](#tab/powershell)

PowerShell を使用して登録を行うには、[Register-AzProviderFeature](/powershell/module/az.resources/register-azproviderfeature) コマンドを呼び出します。

```powershell
# Register for container soft delete (preview)
Register-AzProviderFeature -ProviderNamespace Microsoft.Storage `
    -FeatureName ContainerSoftDelete

# Refresh the Azure Storage provider namespace
Register-AzResourceProvider -ProviderNamespace Microsoft.Storage
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Azure CLI を使用して登録を行うには、[az feature register](/cli/azure/feature#az-feature-register) コマンドを呼び出します。

```azurecli
az feature register --namespace Microsoft.Storage --name ContainerSoftDelete
az provider register --namespace 'Microsoft.Storage'
```

---

### <a name="check-the-status-of-your-registration"></a>登録の状態を確認する

登録の状態を確認するには、PowerShell または Azure CLI を使用します。

# <a name="powershell"></a>[PowerShell](#tab/powershell)

PowerShell での登録の状態を確認するには、[Get-AzProviderFeature](/powershell/module/az.resources/get-azproviderfeature) コマンドを呼び出します。

```powershell
Get-AzProviderFeature -ProviderNamespace Microsoft.Storage -FeatureName ContainerSoftDelete
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Azure CLI での登録の状態を確認するには、[az feature](/cli/azure/feature#az-feature-show) コマンドを呼び出します。

```azurecli
az feature show --namespace Microsoft.Storage --name ContainerSoftDelete
```

---

## <a name="pricing-and-billing"></a>価格と課金

コンテナーの論理的な削除を有効にするための追加料金は発生しません。 論理的に削除されたコンテナー内のデータは、アクティブなデータと同じレートで課金されます。

## <a name="next-steps"></a>次の手順

- [コンテナーの論理的な削除の構成](soft-delete-container-enable.md)
- [BLOB の論理的な削除](soft-delete-blob-overview.md)
- [BLOB バージョン管理](versioning-overview.md)
