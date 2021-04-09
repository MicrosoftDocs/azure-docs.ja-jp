---
title: .NET を使用して BLOB のプロパティとメタデータを管理する - Azure Storage
description: .NET クライアント ライブラリを使用して、システム プロパティを設定および取得したり、Azure Storage アカウントの BLOB にカスタム メタデータを格納したりする方法について説明します。
services: storage
author: mhopkins-msft
ms.author: mhopkins
ms.date: 09/25/2020
ms.service: storage
ms.subservice: blobs
ms.topic: how-to
ms.custom: devx-track-csharp
ms.openlocfilehash: 984fb00e163a090534da1fb41850dcfef6c5d516
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "95521531"
---
# <a name="manage-blob-properties-and-metadata-with-net"></a>.NET を使用した BLOB プロパティとメタデータの管理

BLOB コンテナーは、そこに含まれているデータに加えて、システム プロパティとユーザー定義メタデータをサポートしています。 この記事では、[.NET 用 Azure Storage クライアント ライブラリ](/dotnet/api/overview/azure/storage)を使用して、システム プロパティとユーザー定義メタデータを管理する方法について説明します。

## <a name="about-properties-and-metadata"></a>プロパティとメタデータについて

- **システムのプロパティ**:システム プロパティは、各 BLOB ストレージ リソース上に存在します。 このようなプロパティには、読み取りまたは設定可能なものもありますが、読み取り専用のものもあります。 実際には、システムのプロパティの一部は、特定の標準 HTTP ヘッダーに対応しています。 .NET 用 Azure Storage クライアント ライブラリは、これらのプロパティをユーザーに代わって保持します。

- **ユーザー定義のメタデータ**: ユーザー定義メタデータは、BLOB ストレージ リソースに対して指定された 1 つ以上の名前と値のペアで構成されます。 メタデータを使用すると、リソースに関する追加の値を格納できます。 メタデータ値は独自の目的にのみ使用され、リソースの動作には影響しません。

> [!NOTE]
> また、BLOB インデックス タグを使用して、ユーザー定義の任意のキーまたは値の属性を Azure BLOB ストレージ リソースと共に格納することもできます。 メタデータに似ていますが、BLOB インデックス タグにのみ自動的にインデックスが付けられて、ネイティブの BLOB サービスによって検索可能になります。 Azure Search などの別のサービスを使用する場合を除き、メタデータにインデックスを付けてクエリを実行することはできません。
>
> この機能の詳細については、「[BLOB インデックスを使用して Azure Blob Storage でデータを管理および検索する (プレビュー)](storage-manage-find-blobs.md)」を参照してください。

## <a name="set-and-retrieve-properties"></a>プロパティを設定および取得する

次のコード例では、BLOB で `ContentType` および `ContentLanguage` システム プロパティを設定します。

# <a name="net-v12"></a>[.NET v12](#tab/dotnet)

BLOB にプロパティを設定するには、[SetHttpHeaders](/dotnet/api/azure.storage.blobs.specialized.blobbaseclient.sethttpheaders) または [SetHttpHeadersAsync](/dotnet/api/azure.storage.blobs.specialized.blobbaseclient.sethttpheadersasync) を呼び出します。 明示的に設定されていないプロパティは消去されます。 次のコード例ではまず、BLOB の既存プロパティが取得します。次にそれを使用し、更新されていないヘッダーにデータを入力します。

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/Metadata.cs" id="Snippet_SetBlobProperties":::

# <a name="net-v11"></a>[.NET v11](#tab/dotnet11)

```csharp
public static async Task SetBlobPropertiesAsync(CloudBlob blob)
{
    try
    {
        Console.WriteLine("Setting blob properties.");

        // You must explicitly set the MIME ContentType every time
        // the properties are updated or the field will be cleared.
        blob.Properties.ContentType = "text/plain";
        blob.Properties.ContentLanguage = "en-us";

        // Set the blob's properties.
        await blob.SetPropertiesAsync();
    }
    catch (StorageException e)
    {
        Console.WriteLine("HTTP error code {0}: {1}",
                            e.RequestInformation.HttpStatusCode,
                            e.RequestInformation.ErrorCode);
        Console.WriteLine(e.Message);
        Console.ReadLine();
    }
}
```
---

次のコード例では、BLOB のシステム プロパティを取得し、値の一部を表示します。

# <a name="net-v12"></a>[.NET v12](#tab/dotnet)

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/Metadata.cs" id="Snippet_ReadBlobProperties":::

# <a name="net-v11"></a>[.NET v11](#tab/dotnet11)

BLOB ストレージ リソースのメタデータとプロパティの値の取得は、2 つの手順から成るプロセスです。 これらの値を読み取るには、`FetchAttributes` または `FetchAttributesAsync` メソッドを呼び出して値を明示的に取得しておく必要があります。 この規則の例外は、`Exists` および `ExistsAsync` メソッドが、内部で適切な `FetchAttributes` メソッドを呼び出すことです。 これらのメソッドのいずれかを呼び出す場合、`FetchAttributes` は呼び出す必要がありません。

> [!IMPORTANT]
> 生成されていないストレージ リソースのプロパティまたはメタデータの値がある場合、コードが `FetchAttributes` または `FetchAttributesAsync` メソッドを呼び出すことを確認します。

BLOB のプロパティを取得するには、BLOB で `FetchAttributes` または `FetchAttributesAsync` メソッドを呼び出して、`Properties` プロパティに値を設定します。

```csharp
private static async Task GetBlobPropertiesAsync(CloudBlob blob)
{
    try
    {
        // Fetch the blob properties.
        await blob.FetchAttributesAsync();

        // Display some of the blob's property values.
        Console.WriteLine(" ContentLanguage: {0}", blob.Properties.ContentLanguage);
        Console.WriteLine(" ContentType: {0}", blob.Properties.ContentType);
        Console.WriteLine(" Created: {0}", blob.Properties.Created);
        Console.WriteLine(" LastModified: {0}", blob.Properties.LastModified);
    }
    catch (StorageException e)
    {
        Console.WriteLine("HTTP error code {0}: {1}",
                            e.RequestInformation.HttpStatusCode,
                            e.RequestInformation.ErrorCode);
        Console.WriteLine(e.Message);
        Console.ReadLine();
    }
}
```
---

## <a name="set-and-retrieve-metadata"></a>メタデータを設定および取得する

メタデータは、BLOB またはコンテナーのリソースで 1 つ以上の名前と値のペアとして指定できます。 メタデータを設定するには、名前と値のペアをリソースの `Metadata` コレクションに追加します。 その後、次のいずれかのメソッドを呼び出して、値を書き込みます。

# <a name="net-v12"></a>[.NET v12](#tab/dotnet)

- [SetMetadata](/dotnet/api/azure.storage.blobs.specialized.blobbaseclient.setmetadata)
- [SetMetadataAsync](/dotnet/api/azure.storage.blobs.specialized.blobbaseclient.setmetadataasync)

# <a name="net-v11"></a>[.NET v11](#tab/dotnet11)

- [SetMetadata](/dotnet/api/microsoft.azure.storage.blob.cloudblob.setmetadata)
- [SetMetadataAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblob.setmetadataasync)
---

メタデータ名/値ペアは有効な HTTP ヘッダーであり、HTTP ヘッダーに適用されるすべての制約に準拠する必要があります。 メタデータ名は有効な HTTP ヘッダー名および有効な C# 識別子でなければならず、ASCII 文字のみを含むことができます。また、大文字と小文字が区別されないものとして扱う必要があります。 非 ASCII 文字を含む [Base64 エンコード](/dotnet/api/system.convert.tobase64string)または [URL エンコード](/dotnet/api/system.web.httputility.urlencode)のメタデータ値。

メタデータの名前は、C# 識別子の名前付け規則に従う必要があります。 メタデータ名では、それが作成されたときに使用された大文字と小文字の区別が維持されますが、設定または読み取り時には大文字と小文字が区別されません。 同じ名前を使用する 2 つ以上のメタデータ ヘッダーがリソースに送信された場合、Azure BLOB ストレージは HTTP エラー コード 400 (正しくない要求) を返します。

次のコード例では、BLOB でメタデータを設定します。 一方の値は、コレクションの `Add` メソッドを使用して設定されます。 もう一方の値は、暗黙的なキーと値の構文を使用して設定されます。

# <a name="net-v12"></a>[.NET v12](#tab/dotnet)

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/Metadata.cs" id="Snippet_AddBlobMetadata":::

# <a name="net-v11"></a>[.NET v11](#tab/dotnet11)

```csharp
public static async Task AddBlobMetadataAsync(CloudBlob blob)
{
    try
    {
        // Add metadata to the blob by calling the Add method.
        blob.Metadata.Add("docType", "textDocuments");

        // Add metadata to the blob by using key/value syntax.
        blob.Metadata["category"] = "guidance";

        // Set the blob's metadata.
        await blob.SetMetadataAsync();
    }
    catch (StorageException e)
    {
        Console.WriteLine("HTTP error code {0}: {1}",
                            e.RequestInformation.HttpStatusCode,
                            e.RequestInformation.ErrorCode);
        Console.WriteLine(e.Message);
        Console.ReadLine();
    }
}
```
---

次のコード例では、BLOB でメタデータを読み取ります。

# <a name="net-v12"></a>[.NET v12](#tab/dotnet)

メタデータを取得するには、次の例に示すように、BLOB またはコンテナーで [GetProperties](/dotnet/api/azure.storage.blobs.specialized.blobbaseclient.getproperties) または [GetPropertiesAsync](/dotnet/api/azure.storage.blobs.specialized.blobbaseclient.getpropertiesasync) メソッドを呼び出して [Metadata](/dotnet/api/azure.storage.blobs.models.blobproperties.metadata) コレクションを設定した後、値を読み取ります。 **GetProperties** メソッドでは、1 回の呼び出しで BLOB のプロパティとメタデータが取得されます。 これは、[BLOB のプロパティの取得](/rest/api/storageservices/get-blob-properties)と [BLOB のメタデータの取得](/rest/api/storageservices/get-blob-metadata)を個別に呼び出す必要がある REST API と異なり ます。

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/Metadata.cs" id="Snippet_ReadBlobMetadata":::

# <a name="net-v11"></a>[.NET v11](#tab/dotnet11)

メタデータを取得するには、次の例に示すように、BLOB またはコンテナーで `FetchAttributes` または `FetchAttributesAsync` メソッドを呼び出して `Metadata` コレクションを設定した後、値を読み取ります。

```csharp
public static async Task ReadBlobMetadataAsync(CloudBlob blob)
{
    try
    {
        // Fetch blob attributes in order to populate 
        // the blob's properties and metadata.
        await blob.FetchAttributesAsync();

        Console.WriteLine("Blob metadata:");

        // Enumerate the blob's metadata.
        foreach (var metadataItem in blob.Metadata)
        {
            Console.WriteLine("\tKey: {0}", metadataItem.Key);
            Console.WriteLine("\tValue: {0}", metadataItem.Value);
        }
    }
    catch (StorageException e)
    {
        Console.WriteLine("HTTP error code {0}: {1}",
                            e.RequestInformation.HttpStatusCode,
                            e.RequestInformation.ErrorCode);
        Console.WriteLine(e.Message);
        Console.ReadLine();
    }
}
```
---

[!INCLUDE [storage-blob-dotnet-resources-include](../../../includes/storage-blob-dotnet-resources-include.md)]

## <a name="see-also"></a>関連項目

- [Set Blob Properties 操作](/rest/api/storageservices/set-blob-properties)
- [Get Blob Properties 操作](/rest/api/storageservices/get-blob-properties)
- [Set Blob Metadata 操作](/rest/api/storageservices/set-blob-metadata)
- [Get Blob Metadata 操作](/rest/api/storageservices/get-blob-metadata)