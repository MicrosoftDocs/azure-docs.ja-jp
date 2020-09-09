---
title: コンテナーの論理的な削除 (プレビュー)
titleSuffix: Azure Storage
description: コンテナーの論理的な削除 (プレビュー) では、お客様のデータを保護して、アプリケーションまたは別のストレージ アカウント ユーザーによってデータが誤って変更または削除されたときに簡単に復旧できるようにします。
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 08/25/2020
ms.author: tamram
ms.subservice: blobs
ms.custom: references_regions
ms.openlocfilehash: 60c7ac6a86c963a4a133f06ba6d9d602cb9090d0
ms.sourcegitcommit: b33c9ad17598d7e4d66fe11d511daa78b4b8b330
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/25/2020
ms.locfileid: "88854533"
---
# <a name="soft-delete-for-containers-preview"></a>コンテナーの論理的な削除 (プレビュー)

コンテナーの論理的な削除 (プレビュー) では、お客様のデータが誤って変更または削除されないように保護します。 ストレージ アカウントに対してコンテナーの論理的な削除が有効になっている場合、削除されたコンテナーとその内容は、指定した期間の間 Azure Storage に保持されます。 保持期間中は、以前に削除されたコンテナーとその中の BLOB を復元できます。

BLOB データのエンド ツー エンド保護では、次のデータ保護機能を有効にすることをお勧めします。

- コンテナーの論理的な削除。コンテナーが誤って削除または上書きされるのを防ぎます。 コンテナーの論理的な削除を有効にする方法については、「[コンテナーの論理的な削除を有効にして管理する](soft-delete-container-enable.md)」をご覧ください。
- BLOB の論理的な削除。個々の BLOB が誤って削除または上書きされるのを防ぎます。 BLOB の論理的な削除を有効にする方法については、「[BLOB の論理的な削除](soft-delete-blob-overview.md)」をご覧ください。
- BLOB のバージョン管理 (プレビュー)。以前のバージョンの BLOB が自動的に維持されます。 BLOB のバージョン管理が有効になっている場合は、以前のバージョンの BLOB を復元し、データが誤って変更または削除された場合に復旧することができます。 BLOB のバージョン管理を有効にする方法については、「[BLOB のバージョン管理を有効にして管理する](versioning-enable.md)」をご覧ください。

> [!WARNING]
> ストレージ アカウントの削除を元に戻すことはできません。 論理削除では、ストレージ アカウントの削除から保護することはできません。 ストレージ アカウントが誤って削除されないようにするには、ストレージ アカウント リソースに対して **CannotDelete** ロックを構成します。 Azure リソースのロックの詳細については、「[リソースのロックによる予期せぬ変更の防止](../../azure-resource-manager/management/lock-resources.md)」を参照してください。

## <a name="how-container-soft-delete-works"></a>コンテナーの論理的な削除のしくみ

コンテナーの論理的な削除を有効にすると、削除されたコンテナーの保持期間を 1 - 365 日の範囲で指定できます。 既定の保有期間は 7 日です。 保持期間中は、**コンテナーの削除の取り消し**操作を呼び出せば、削除されたコンテナーを復旧することができます。

コンテナーを復元するときに、元の名前が再利用されていない場合は、その名前で復元することができます。 元のコンテナー名が使用されている場合は、新しい名前でコンテナーを復元できます。

保持期間が経過すると、コンテナーは Azure Storage から完全に削除され、復旧できなくなります。 コンテナーが削除された時点で保持期間のクロックが開始します。 保持期間はいつでも変更できますが、更新された保持期間は、新しく削除されたコンテナーにのみ適用されることに注意してください。 以前に削除されたコンテナーは、そのコンテナーが削除された時点で有効になった保持期間に基づいて完全に削除されます。

コンテナーの論理的な削除を無効にしても、以前に論理的に削除されたコンテナーが完全に削除されることはありません。 論理的に削除されたコンテナーは、そのコンテナーが削除された時点で有効になった保持期間が経過すると完全に削除されます。

## <a name="about-the-preview"></a>プレビューについて

次のリージョンでは、コンテナーの論理的な削除をプレビューでご利用いただけます。

- フランス中部
- カナダ東部
- カナダ中部

> [!IMPORTANT]
> コンテナーの論理的な削除のプレビューは、非運用環境での使用のみを意図しています。 運用環境のサービス レベル契約(SLA) は現在使用できません。

バージョン 2019-12-12 以降の Azure Storage REST API では、コンテナーの論理的な削除がサポートされています。

### <a name="storage-account-support"></a>ストレージ アカウントのサポート

コンテナーの論理的な削除は、次の種類のストレージ アカウントで使用できます。

- 汎用 v2 ストレージ アカウント
- ブロック BLOB ストレージ アカウント
- BLOB ストレージ アカウント

ストレージ アカウントが汎用 v1 アカウントの場合は、Azure portal を使用して、汎用 v2 アカウントにアップグレードします。 ストレージ アカウントについて詳しくは、「 [Azure ストレージ アカウントの概要](../common/storage-account-overview.md)」をご覧ください。

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
- [BLOB のバージョン管理 (プレビュー)](versioning-overview.md)
