---
title: BLOB のバージョン管理を有効にして管理する (プレビュー)
titleSuffix: Azure Storage
description: Azure portal または Azure Resource Manager テンプレートを使用して、BLOB のバージョン管理を有効にする (プレビュー) 方法について説明します。
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 08/10/2020
ms.author: tamram
ms.subservice: blobs
ms.openlocfilehash: 85e8ccd03bd20ed9bb572d482dbc7a06b8af725c
ms.sourcegitcommit: d8b8768d62672e9c287a04f2578383d0eb857950
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/11/2020
ms.locfileid: "88067275"
---
# <a name="enable-and-manage-blob-versioning-preview"></a>BLOB のバージョン管理を有効にして管理する (プレビュー)

BLOB ストレージのバージョン管理 (プレビュー) を有効にし、以前のバージョンのオブジェクトを自動的に維持することができます。  BLOB のバージョン管理が有効になっている場合は、以前のバージョンの BLOB を復元し、データが誤って変更または削除された場合に復旧することができます。

この記事では、Azure portal または Azure Resource Manager テンプレートを使用してストレージ アカウントの BLOB のバージョン管理を有効または無効にする方法について説明します。

BLOB のバージョン管理を有効にする前に、プレビューに登録する必要があります。 プレビューへの登録方法を含め、BLOB のバージョン管理の詳細については、「[BLOB のバージョン管理 (プレビュー)](versioning-overview.md)」を参照してください。

## <a name="enable-blob-versioning"></a>BLOB のバージョン管理を有効にする

# <a name="azure-portal"></a>[Azure Portal](#tab/portal)

Azure portal で BLOB のバージョン管理を有効にするには:

1. ポータルでストレージ アカウントに移動します。
1. **[Blob service]** の下で **[データ保護]** を選択します。
1. **[バージョン管理]** セクションで、 **[有効]** を選択します。

:::image type="content" source="media/versioning-enable/portal-enable-versioning.png" alt-text="Azure portal で BLOB のバージョン管理を有効にする方法を示しているスクリーンショット":::

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

次のコード例は、.NET バージョン [12.5.0-preview.5](https://www.nuget.org/packages/Azure.Storage.Blobs/12.5.0-preview.5) 以降用の Azure Storage クライアント ライブラリを使用して、新しいバージョンの作成をトリガーする方法を示しています。 この例を実行する前に、ストレージ アカウントのバージョン管理が有効になっていることをご確認ください。

この例では、ブロック BLOB を作成してから、BLOB のメタデータを更新します。 BLOB のメタデータを更新すると、新しいバージョンの作成がトリガーされます。 この例では、初期バージョンと現在のバージョンを取得し、現在のバージョンにのみメタデータが含まれていることを示します。

```csharp
public static async Task UpdateVersionedBlobMetadata(string containerName, string blobName)
{
    // Create a new service client from the connection string.
    BlobServiceClient blobServiceClient = new BlobServiceClient(ConnectionString);

    // Create a new container client.
    BlobContainerClient containerClient = blobServiceClient.GetBlobContainerClient(containerName);

    try
    {
        // Create the container.
        await containerClient.CreateIfNotExistsAsync();

        // Upload a block blob.
        BlockBlobClient blockBlobClient = containerClient.GetBlockBlobClient(blobName);

        string blobContents = string.Format("Block blob created at {0}.", DateTime.Now);
        byte[] byteArray = Encoding.ASCII.GetBytes(blobContents);

        string initalVersionId;
        using (MemoryStream stream = new MemoryStream(byteArray))
        {
            Response<BlobContentInfo> uploadResponse = await blockBlobClient.UploadAsync(stream, null, default);

            // Get the version ID for the current version.
            initalVersionId = uploadResponse.Value.VersionId;
        }

        // Update the blob's metadata to trigger the creation of a new version.
        Dictionary<string, string> metadata = new Dictionary<string, string>
        {
            { "key", "value" },
            { "key1", "value1" }
        };

        Response<BlobInfo> metadataResponse = await blockBlobClient.SetMetadataAsync(metadata);

        // Get the version ID for the new current version.
        string newVersionId = metadataResponse.Value.VersionId;

        // Request metadata on the previous version.
        BlockBlobClient initalVersionBlob = blockBlobClient.WithVersion(initalVersionId);
        Response<BlobProperties> propertiesResponse = await initalVersionBlob.GetPropertiesAsync();
        PrintMetadata(propertiesResponse);

        // Request metadata on the current version.
        BlockBlobClient newVersionBlob = blockBlobClient.WithVersion(newVersionId);
        Response<BlobProperties> newPropertiesResponse = await newVersionBlob.GetPropertiesAsync();
        PrintMetadata(newPropertiesResponse);
    }
    catch (RequestFailedException e)
    {
        Console.WriteLine(e.Message);
        Console.ReadLine();
        throw;
    }
    finally
    {
        await containerClient.DeleteAsync();
    }
}

static void PrintMetadata(Response<BlobProperties> propertiesResponse)
{
    if (propertiesResponse.Value.Metadata.Count > 0)
    {
        Console.WriteLine("Metadata values for version {0}:", propertiesResponse.Value.VersionId);
        foreach (var item in propertiesResponse.Value.Metadata)
        {
            Console.WriteLine("Key:{0}  Value:{1}", item.Key, item.Value);
        }
    }
    else
    {
        Console.WriteLine("Version {0} has no metadata.", propertiesResponse.Value.VersionId);
    }
}
```

## <a name="next-steps"></a>次のステップ

- [BLOB のバージョン管理 (プレビュー)](versioning-overview.md)
- [Azure Storage Blob の論理的な削除](soft-delete-overview.md)
