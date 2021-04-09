---
title: BLOB のバージョン管理を有効にして管理する
titleSuffix: Azure Storage
description: Azure portal または Azure Resource Manager テンプレートを使用して、BLOB のバージョン管理を有効にする方法について説明します。
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 02/09/2021
ms.author: tamram
ms.subservice: blobs
ms.custom: devx-track-csharp
ms.openlocfilehash: 5b6bd16eacf4b1bbb7b93f5500813e7fa9dc7eef
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "100095845"
---
# <a name="enable-and-manage-blob-versioning"></a>BLOB のバージョン管理を有効にして管理する

BLOB ストレージのバージョン管理を有効にすると、BLOB が変更または削除されたときに、以前のバージョンを自動的に維持できます。 BLOB のバージョン管理が有効になっていると、データが誤って変更または削除された場合に、以前のバージョンの BLOB を復元して復旧できます。

この記事では、Azure portal または Azure Resource Manager テンプレートを使用してストレージ アカウントの BLOB のバージョン管理を有効または無効にする方法について説明します。 BLOB のバージョン管理については、[BLOB のバージョン管理](versioning-overview.md)に関するページをご覧ください。

[!INCLUDE [storage-data-lake-gen2-support](../../../includes/storage-data-lake-gen2-support.md)]

## <a name="enable-blob-versioning"></a>BLOB のバージョン管理を有効にする

# <a name="azure-portal"></a>[Azure Portal](#tab/portal)

Azure portal でストレージ アカウントの BLOB のバージョン管理を有効にするには、次のようにします。

1. ポータルでストレージ アカウントに移動します。
1. **[Blob service]** の下で **[データ保護]** を選択します。
1. **[バージョン管理]** セクションで、 **[有効]** を選択します。

:::image type="content" source="media/versioning-enable/portal-enable-versioning.png" alt-text="Azure portal で BLOB のバージョン管理を有効にする方法を示しているスクリーンショット":::

# <a name="powershell"></a>[PowerShell](#tab/powershell)

PowerShell を使用してストレージ アカウントの BLOB のバージョン管理を有効にするには、まず、[Az.Storage](https://www.powershellgallery.com/packages/Az.Storage) モジュールのバージョン 2.3.0 以降をインストールします。 その後、次の例に示すように、[Update-AzStorageBlobServiceProperty](/powershell/module/az.storage/update-azstorageblobserviceproperty) コマンドを呼び出して、バージョン管理を有効にします。 山かっこ内の値は、実際の値に置き換えてください。

```powershell
# Set resource group and account variables.
$rgName = "<resource-group>"
$accountName = "<storage-account>"

# Enable versioning.
Update-AzStorageBlobServiceProperty -ResourceGroupName $rgName `
    -StorageAccountName $accountName `
    -IsVersioningEnabled $true
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Azure CLI を使用してストレージ アカウントの BLOB のバージョン管理を有効にするには、まず、Azure CLI バージョン 2.2.0 以降をインストールします。 その後、次の例に示すように、[az storage account blob-service-properties update](/cli/azure/ext/storage-blob-preview/storage/account/blob-service-properties#ext_storage_blob_preview_az_storage_account_blob_service_properties_update) コマンドを呼び出して、バージョン管理を有効にします。 山かっこ内の値は、実際の値に置き換えてください。

```azurecli
az storage account blob-service-properties update \
    --resource-group <resource_group> \
    --account-name <storage-account> \
    --enable-versioning true
```

# <a name="template"></a>[テンプレート](#tab/template)

テンプレートを使用して BLOB のバージョン管理を有効にするには、**IsVersioningEnabled** プロパティを **true** にしてテンプレートを作成します。 次の手順は、Azure portal でテンプレートを作成する方法について説明しています。

1. Azure portal で、 **[リソースの作成]** を選択します。
1. **[Marketplace を検索]** で「**template deployment**」と入力し、**Enter** キーを押します。
1. **[テンプレートのデプロイ]** 、 **[作成]** 、 **[エディターで独自のテンプレートを作成する]** の順に選択します。
1. テンプレート エディターで、次の JSON を貼り付けます。 `<accountName>` プレースホルダーは、実際のストレージ アカウントの名前に置き換えます。
1. テンプレートを保存します。
1. アカウントのリソース グループを指定してから **[購入]** ボタンを選択して、テンプレートをデプロイし、BLOB のバージョン管理を有効にします。

    ```json
    {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {},
        "variables": {},
        "resources": [
            {
                "type": "Microsoft.Storage/storageAccounts/blobServices",
                "apiVersion": "2019-06-01",
                "name": "<accountName>/default",
                "properties": {
                    "IsVersioningEnabled": true
                }
            }
        ]
    }
    ```

Azure portal でテンプレートを使用してリソースをデプロイする方法の詳細については、[Azure portal を使用したリソースのデプロイ](../../azure-resource-manager/templates/deploy-portal.md)に関するページを参照してください。

---

## <a name="modify-a-blob-to-trigger-a-new-version"></a>BLOB を変更して新しいバージョンをトリガーする

次のコード例は、.NET バージョン [12.5.1](https://www.nuget.org/packages/Azure.Storage.Blobs/12.5.1) 以降用の Azure Storage クライアント ライブラリを使用して、新しいバージョンの作成をトリガーする方法を示しています。 この例を実行する前に、ストレージ アカウントのバージョン管理が有効になっていることをご確認ください。

この例では、ブロック BLOB を作成してから、BLOB のメタデータを更新します。 BLOB のメタデータを更新すると、新しいバージョンの作成がトリガーされます。 この例では、初期バージョンと現在のバージョンを取得し、現在のバージョンにのみメタデータが含まれていることを示します。

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/CRUD.cs" id="Snippet_UpdateVersionedBlobMetadata":::

## <a name="list-blob-versions"></a>BLOB バージョンをリストに表示する

.NET v12 クライアント ライブラリを使用して BLOB のバージョンまたはスナップショットをリストに表示するには、 **[バージョン]** フィールドで [[BlobStates]](/dotnet/api/azure.storage.blobs.models.blobstates) パラメーターを指定します。

次のコード例は、.NET バージョン [12.5.1](https://www.nuget.org/packages/Azure.Storage.Blobs/12.5.1) 以降用の Azure Storage クライアント ライブラリを使用して、BLOB のバージョンをリストに表示する方法を示しています。 この例を実行する前に、ストレージ アカウントのバージョン管理が有効になっていることをご確認ください。

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/CRUD.cs" id="Snippet_ListBlobVersions":::

## <a name="next-steps"></a>次のステップ

- [BLOB バージョン管理](versioning-overview.md)
- [Azure Storage Blob の論理的な削除](./soft-delete-blob-overview.md)