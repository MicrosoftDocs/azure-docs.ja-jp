---
title: .NET を使用して BLOB コンテナーのプロパティとメタデータを管理する - Azure Storage
description: .NET クライアント ライブラリを使用して、システム プロパティを設定および取得したり、Azure Storage アカウントの BLOB コンテナーにカスタム メタデータを格納したりする方法について説明します。
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 07/10/2019
ms.author: tamram
ms.openlocfilehash: bd745c54fb659729b1e31f9975b2e4e0b6a97a83
ms.sourcegitcommit: b2db98f55785ff920140f117bfc01f1177c7f7e2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/16/2019
ms.locfileid: "68235185"
---
# <a name="manage-container-properties-and-metadata-with-net"></a>.NET を使用してコンテナーのプロパティとメタデータを管理する

BLOB コンテナーは、そこに含まれているデータに加えて、システム プロパティとユーザー定義メタデータをサポートしています。 この記事では、[.NET 用 Azure Storage クライアント ライブラリ](/dotnet/api/overview/azure/storage/client)を使用して、システム プロパティとユーザー定義メタデータを管理する方法について説明します。

## <a name="about-properties-and-metadata"></a>プロパティとメタデータについて

- **システムのプロパティ**:システム プロパティは、各 BLOB ストレージ リソース上に存在します。 このようなプロパティには、読み取りまたは設定可能なものもありますが、読み取り専用のものもあります。 実際には、システムのプロパティの一部は、特定の標準 HTTP ヘッダーに対応しています。 .NET 用 Azure Storage クライアント ライブラリは、これらのプロパティをユーザーに代わって保持します。

- **ユーザー定義のメタデータ**: ユーザー定義メタデータは、BLOB ストレージ リソースに対して指定された 1 つ以上の名前と値のペアで構成されます。 メタデータを使用すると、リソースに関する追加の値を格納できます。 メタデータ値は独自の目的にのみ使用され、リソースの動作には影響しません。

BLOB ストレージ リソースのプロパティおよびメタデータ値の取得は、2 つの手順から成るプロセスです。 これらの値を読み取るには、**FetchAttributes** または **FetchAttributesAsync** メソッドを呼び出して値を明示的に取得しておく必要があります。 この規則の例外は、**Exists** および **ExistsAsync** メソッドが、内部で適切な **FetchAttributes** メソッドを呼び出すことです。 これらのメソッドのいずれかを呼び出す場合、**FetchAttributes** は呼び出す必要がありません。

> [!IMPORTANT]
> 生成されていないストレージ リソースのプロパティまたはメタデータの値がある場合、コードが **FetchAttributes** または **FetchAttributesAsync** メソッドを呼び出すことを確認します。

メタデータ名/値ペアは有効な HTTP ヘッダーです。HTTP ヘッダーに適用されるすべての制約に準拠する必要があります。 メタデータ名は有効な HTTP ヘッダー名および有効な C# 識別子でなければならず、ASCII 文字のみを含むことができます。また、大文字と小文字が区別されないものとして扱う必要があります。 非 ASCII 文字を含むメタデータ値は、Base64 エンコードまたは URL エンコードである必要があります。

## <a name="retrieve-container-properties"></a>コンテナーのプロパティを取得する

コンテナーのプロパティを取得するには、次のメソッドのいずれかを呼び出します。

- [FetchAttributes](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.fetchattributes)
- [FetchAttributesAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.fetchattributesasync)

次のコード例では、コンテナーのシステム プロパティをフェッチし、一部のプロパティ値をコンソール ウィンドウに書き込みます。

```csharp
private static async Task ReadContainerPropertiesAsync(CloudBlobContainer container)
{
    try
    {
        // Fetch some container properties and write out their values.
        await container.FetchAttributesAsync();
        Console.WriteLine("Properties for container {0}", container.StorageUri.PrimaryUri);
        Console.WriteLine("Public access level: {0}", container.Properties.PublicAccess);
        Console.WriteLine("Last modified time in UTC: {0}", container.Properties.LastModified);
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

## <a name="set-and-retrieve-metadata"></a>メタデータを設定および取得する

メタデータは、BLOB またはコンテナーのリソースで 1 つ以上の名前と値のペアとして指定できます。 メタデータを設定するには、リソース上の **Metadata** コレクションに名前と値のペアを追加した後、次のメソッドのいずれかを呼び出して値を書き込みます。

- [SetMetadata](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.setmetadata)
- [SetMetadataAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.setmetadataasync)

メタデータの名前は、C# 識別子の名前付け規則に従う必要があります。 メタデータ名では、それが作成されたときの大文字と小文字の区別が保持されますが、設定または読み取り時には大文字と小文字が区別されません。 同じ名前を持つ 2 つ以上のメタデータ ヘッダーがリソースに送信された場合、BLOB ストレージは HTTP エラー コード 400 (正しくない要求) を返します。

次のコード例では、コンテナーでメタデータを設定します。 一方の値は、コレクションの **Add** メソッドを使用して設定されます。 もう一方の値は、暗黙的なキーと値の構文を使用して設定されます。 どちらも有効です。

```csharp
public static async Task AddContainerMetadataAsync(CloudBlobContainer container)
{
    try
    {
        // Add some metadata to the container.
        container.Metadata.Add("docType", "textDocuments");
        container.Metadata["category"] = "guidance";

        // Set the container's metadata.
        await container.SetMetadataAsync();
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

メタデータを取得するには、次の例に示すように、BLOB またはコンテナーで **FetchAttributes** または **FetchAttributesAsync** メソッドを呼び出して **Metadata** コレクションを設定した後、値を読み取ります。

```csharp
public static async Task ReadContainerMetadataAsync(CloudBlobContainer container)
{
    try
    {
        // Fetch container attributes in order to populate the container's properties and metadata.
        await container.FetchAttributesAsync();

        // Enumerate the container's metadata.
        Console.WriteLine("Container metadata:");
        foreach (var metadataItem in container.Metadata)
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

[!INCLUDE [storage-blob-dotnet-resources](../../../includes/storage-blob-dotnet-resources.md)]

## <a name="see-also"></a>関連項目

- [Get Container Properties 操作](/rest/api/storageservices/get-container-properties)
- [Set Container Metadata 操作](/rest/api/storageservices/set-container-metadata)
- [Get Container Metadata 操作](/rest/api/storageservices/set-container-metadata)
