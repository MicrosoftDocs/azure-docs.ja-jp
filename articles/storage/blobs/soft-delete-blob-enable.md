---
title: BLOB の論理的な削除の有効化と管理
titleSuffix: Azure Storage
description: BLOB の論理的な削除を有効にすると、誤って変更または削除されたときにデータをより簡単に回復できます。
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 07/15/2020
ms.author: tamram
ms.subservice: blobs
ms.custom: devx-track-azurecli, devx-track-csharp
ms.openlocfilehash: a74860b7adf4dade5aedc71a4960595cbe55eaf0
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "95995303"
---
# <a name="enable-and-manage-soft-delete-for-blobs"></a>BLOB の論理的な削除の有効化と管理

BLOB の論理的な削除により、誤って変更または削除されないようにデータを保護できます。 ストレージ アカウントに対して BLOB の論理的な削除が有効になっている場合、そのストレージ アカウント内の BLOB、BLOB バージョン、およびスナップショットは、指定したリテンション期間内で、削除された後に復旧できます。

アプリケーションまたは別のストレージ アカウントのユーザーによってデータが誤って変更または削除される可能性がある場合、Microsoft では BLOB の論理的な削除を有効にすることをお勧めしています。 この記事では、BLOB の論理的な削除を有効にする方法について説明します。 BLOB の論理的な削除の詳細については、「[BLOB の論理的な削除](soft-delete-blob-overview.md)」を参照してください。

論理的な削除をコンテナーでも有効にする方法については、[コンテナーの論理的な削除の有効化と管理](soft-delete-container-enable.md)に関する記事をご覧ください。

## <a name="enable-blob-soft-delete"></a>BLOB の論理的な削除を有効にする

# <a name="portal"></a>[ポータル](#tab/azure-portal)

Azure portal を使用して、ストレージアカウントの BLOB の論理的な削除を有効にします：

1. [Azure portal](https://portal.azure.com/) のストレージ アカウントに移動します。
1. **[Blob service]** の下の **[データ保護]** オプションを見つけます。
1. **[BLOB の論理的な削除]** プロパティを *[有効]* に設定します。
1. **[保持ポリシー]** で、論理的に削除された BLOB が Azure Storage によって保持される期間を指定します。
1. 変更を保存します。

![Azure portal のスクリーンショット。データ保護 BLOB サービスが選択されています。](media/soft-delete-blob-enable/storage-blob-soft-delete-portal-configuration.png)

論理的に削除された BLOB を表示するには、 **[Show deleted blobs]\(削除された BLOB を表示する\)** チェック ボックスをオンにします。

![データ保護 BLOB サービス ページのスクリーンショット。[Show deleted blobs]\(削除された BLOB を表示する\) オプションが強調表示されています。](media/soft-delete-blob-enable/storage-blob-soft-delete-portal-view-soft-deleted.png)

特定の BLOB の論理的に削除されたスナップショットを表示するには、BLOB を選んで、 **[スナップショットの表示]** をクリックします。

![データ保護 BLOB サービス ページのスクリーンショット。[スナップショットの表示] オプションが強調表示されています。](media/soft-delete-blob-enable/storage-blob-soft-delete-portal-view-soft-deleted-snapshots.png)

**[Show deleted snapshots]\(削除されたスナップショットを表示する\)** チェック ボックスがオンになっていることを確認します。

![[スナップショットの表示] ページのスクリーンショット。[Show deleted blobs]\(削除された BLOB を表示する\) オプションが強調表示されています。](media/soft-delete-blob-enable/storage-blob-soft-delete-portal-view-soft-deleted-snapshots-check.png)

論理的に削除された BLOB またはスナップショットをクリックしたら、新しい BLOB のプロパティを確認します。 プロパティでは、オブジェクトが削除された日時と、BLOB または BLOB のスナップショットが完全に期限切れになるまでの残り日数が示されます。 論理的に削除されたオブジェクトがスナップショットではない場合は、削除を取り消すこともできます。

![論理削除されたオブジェクトの詳細のスクリーンショット。](media/soft-delete-blob-enable/storage-blob-soft-delete-portal-properties.png)

BLOB の削除を取り消すと、関連付けられているすべてのスナップショットの削除も取り消されることに注意してください。 アクティブな BLOB の論理的に削除されたスナップショットの削除を取り消すには、BLOB をクリックして、 **[Undelete all snapshots]\(すべてのスナップショットの削除を取り消す\)** を選びます。

![論理削除された BLOB の詳細のスクリーンショット。](media/soft-delete-blob-enable/storage-blob-soft-delete-portal-undelete-all-snapshots.png)

BLOB のスナップショットの削除を取り消した後は、 **[レベル上げ]** をクリックしてスナップショットをベース BLOB にコピーすることで、BLOB をスナップショットに復元できます。

![[スナップショットの表示] ページのスクリーンショット。[レベル上げ] オプションが強調表示されています。](media/soft-delete-blob-enable/storage-blob-soft-delete-portal-promote-snapshot.png)

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

論理的な削除を有効にするには、BLOB クライアントのサービスのプロパティを更新します。 次の例では、サブスクリプションのアカウントのサブセットで論理的な削除を有効にしています。

```powershell
Set-AzContext -Subscription "<subscription-name>"
$MatchingAccounts = Get-AzStorageAccount | where-object{$_.StorageAccountName -match "<matching-regex>"}
$MatchingAccounts | Enable-AzStorageDeleteRetentionPolicy -RetentionDays 7
```

次のコマンドを使用して、ソフト削除が有効にされたことを確認します。

```powershell
$MatchingAccounts | $account = Get-AzStorageAccount -ResourceGroupName myresourcegroup -Name storageaccount
   Get-AzStorageServiceProperty -ServiceType Blob -Context $account.Context | Select-Object -ExpandProperty DeleteRetentionPolicy
```

誤って削除された BLOB を復旧するには、それらの BLOB で **Undelete Blob** を呼び出します。 アクティブな BLOB と論理的に削除された BLOB の両方で **Undelete Blob** を呼び出すと、関連付けられているすべての論理的に削除されたスナップショットがアクティブとして復元されることに注意してください。 次の例では、コンテナー内にあるすべての論理的に削除された BLOB とアクティブな BLOB で **Undelete Blob** を呼び出しています。

```powershell
# Create a context by specifying storage account name and key
$ctx = New-AzStorageContext -StorageAccountName $StorageAccountName -StorageAccountKey $StorageAccountKey

# Get the blobs in a given container and show their properties
$Blobs = Get-AzStorageBlob -Container $StorageContainerName -Context $ctx -IncludeDeleted
$Blobs.ICloudBlob.Properties

# Undelete the blobs
$Blobs.ICloudBlob.Undelete()
```
現在のソフト削除アイテム保持ポリシーを検索するには、次のコマンドを使用します。

```azurepowershell-interactive
   $account = Get-AzStorageAccount -ResourceGroupName myresourcegroup -Name storageaccount
   Get-AzStorageServiceProperty -ServiceType Blob -Context $account.Context
```

# <a name="cli"></a>[CLI](#tab/azure-CLI)

論理的な削除を有効にするには、BLOB クライアントのサービスのプロパティを更新します。

```azurecli-interactive
az storage blob service-properties delete-policy update --days-retained 7  --account-name mystorageaccount --enable true
```

論理的な削除が有効になっていることを確認するには、次のコマンドを使用します。 

```azurecli-interactive
az storage blob service-properties delete-policy show --account-name mystorageaccount 
```

# <a name="python"></a>[Python](#tab/python)

論理的な削除を有効にするには、BLOB クライアントのサービスのプロパティを更新します。

```python
# Make the requisite imports
from azure.storage.blob import BlockBlobService
from azure.storage.common.models import DeleteRetentionPolicy

# Initialize a block blob service
block_blob_service = BlockBlobService(
    account_name='<enter your storage account name>', account_key='<enter your storage account key>')

# Set the blob client's service property settings to enable soft delete
block_blob_service.set_blob_service_properties(
    delete_retention_policy=DeleteRetentionPolicy(enabled=True, days=7))
```

# <a name="net-v12"></a>[.NET v12](#tab/dotnet)

論理的な削除を有効にするには、BLOB クライアントのサービスのプロパティを更新します。

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/DataProtection.cs" id="Snippet_EnableSoftDelete":::

誤って削除された BLOB を復旧するには、それらの BLOB で Undelete を呼び出します。 アクティブな BLOB と論理的に削除された BLOB の両方で **Undelete** を呼び出すと、関連付けられているすべての論理的に削除されたスナップショットがアクティブとして復元されることに注意してください。 次の例では、コンテナー内にあるすべての論理的に削除された BLOB とアクティブな BLOB で Undelete を呼び出しています。

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/DataProtection.cs" id="Snippet_RecoverDeletedBlobs":::

BLOB の特定のバージョンに復元するには、最初に BLOB で Undelete を呼び出した後、目的のスナップショットを BLOB にコピーします。 次の例では、ブロック BLOB を最後に生成されたスナップショットに復旧しています。

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/DataProtection.cs" id="Snippet_RecoverSpecificBlobVersion":::

# <a name="net-v11"></a>[.NET v11](#tab/dotnet11)

論理的な削除を有効にするには、BLOB クライアントのサービスのプロパティを更新します。

```csharp
// Get the blob client's service property settings
ServiceProperties serviceProperties = blobClient.GetServiceProperties();

// Configure soft delete
serviceProperties.DeleteRetentionPolicy.Enabled = true;
serviceProperties.DeleteRetentionPolicy.RetentionDays = RetentionDays;

// Set the blob client's service property settings
blobClient.SetServiceProperties(serviceProperties);
```

誤って削除された BLOB を復旧するには、それらの BLOB で **Undelete Blob** を呼び出します。 アクティブな BLOB と論理的に削除された BLOB の両方で **Undelete Blob** を呼び出すと、関連付けられているすべての論理的に削除されたスナップショットがアクティブとして復元されることに注意してください。 次の例では、コンテナー内にあるすべての論理的に削除された BLOB とアクティブな BLOB で **Undelete Blob** を呼び出しています。

```csharp
// Recover all blobs in a container
foreach (CloudBlob blob in container.ListBlobs(useFlatBlobListing: true, blobListingDetails: BlobListingDetails.Deleted))
{
       await blob.UndeleteAsync();
}
```

BLOB の特定のバージョンに復元するには、最初に BLOB で **Undelete Blob** 操作を呼び出した後、目的のスナップショットを BLOB にコピーします。 次の例では、ブロック BLOB を最後に生成されたスナップショットに復旧しています。

```csharp
// Undelete
await blockBlob.UndeleteAsync();

// List all blobs and snapshots in the container prefixed by the blob name
IEnumerable<IListBlobItem> allBlobVersions = container.ListBlobs(
    prefix: blockBlob.Name, useFlatBlobListing: true, blobListingDetails: BlobListingDetails.Snapshots);

// Restore the most recently generated snapshot to the active blob
CloudBlockBlob copySource = allBlobVersions.First(version => ((CloudBlockBlob)version).IsSnapshot &&
    ((CloudBlockBlob)version).Name == blockBlob.Name) as CloudBlockBlob;
blockBlob.StartCopy(copySource);
```  

---

## <a name="next-steps"></a>次のステップ

- [BLOB ストレージの論理的な削除](./soft-delete-blob-overview.md)
- [BLOB バージョン管理](versioning-overview.md)