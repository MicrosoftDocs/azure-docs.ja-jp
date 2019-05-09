---
title: Azure Storage のオブジェクトのプロパティおよびメタデータの設定と取得 | Microsoft Docs
description: Azure Storage のオブジェクトにカスタム メタデータを格納して、システムのプロパティを設定、取得します。
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 04/03/2019
ms.author: tamram
ms.openlocfilehash: 86bb7e736754cbc6a93bba5fff5d8d1877b1e3b4
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/18/2019
ms.locfileid: "58916582"
---
# <a name="set-and-retrieve-properties-and-metadata"></a>プロパティおよびメタデータを設定および取得する

Azure Storage のオブジェクトは、含まれるデータのほかにもシステムのプロパティとユーザー定義のメタデータをサポートします。 この記事では、[.NET 用 Azure Storage クライアント ライブラリ](https://www.nuget.org/packages/WindowsAzure.Storage/)でのシステムのプロパティとユーザー定義のメタデータの管理について説明します。

* **システムのプロパティ**: システムのプロパティは、各ストレージ リソースに存在します。 このようなプロパティには、読み取りまたは設定可能なものもありますが、読み取り専用のものもあります。 実際には、システムのプロパティの一部は、特定の標準 HTTP ヘッダーに対応しています。 Azure Storage クライアント ライブラリでは、これらのプロパティが保持されます。

* **ユーザー定義のメタデータ**: ユーザー定義のメタデータは、Azure Storage リソースに対して指定する 1 つまたは複数の名前と値のペアで構成されます。 メタデータを使用して、リソースで追加の値を格納できます。 メタデータ値は独自の目的にのみ使用され、リソースの動作には影響しません。

ストレージ リソースのプロパティとメタデータの値を取得するには、2 つの手順が必要です。 これらの値を読み取るには、**FetchAttributes** または **FetchAttributesAsync** メソッドを呼び出して値を明示的に取得しておく必要があります。 リソース上で **Exists** または **ExistsAsync** メソッドを呼び出している場合は例外です。 これらのメソッドのいずれかを呼び出すと、Azure Storage は、見た目ではわかりませんが **Exists** メソッドの呼び出しの一部として、適切な **FetchAttributes** メソッドを呼び出します。

> [!IMPORTANT]
> 生成されていないストレージ リソースのプロパティまたはメタデータの値がある場合、コードが **FetchAttributes** または **FetchAttributesAsync** メソッドを呼び出すことを確認します。
>
> メタデータ名/値ペアは有効な HTTP ヘッダーです。HTTP ヘッダーに適用されるすべての制約に準拠する必要があります。 メタデータ名は有効な HTTP ヘッダー名でなければならず、ASCII 文字のみを含むことができます。また、大文字と小文字が区別されないものとして扱う必要があります。 非 ASCII 文字を含むメタデータ値は、Base64 エンコードまたは URL エンコードである必要があります。

## <a name="setting-and-retrieving-properties"></a>プロパティの設定と取得
プロパティ値を取得するには、BLOB またはコンテナーで **FetchAttributesAsync** メソッドを呼び出してプロパティを設定した後、値を読み取ります。

オブジェクトでプロパティを設定するには、プロパティ値を指定し、 **SetProperties** メソッドを呼び出します。

次のコード例では、コンテナーを作成し、プロパティ値の一部をコンソール ウィンドウに書き込みます。

```csharp
//Parse the connection string for the storage account.
const string ConnectionString = "DefaultEndpointsProtocol=https;AccountName=account-name;AccountKey=account-key";
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(ConnectionString);

//Create the service client object for credentialed access to the Blob service.
CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

// Retrieve a reference to a container.
CloudBlobContainer container = blobClient.GetContainerReference("mycontainer");

// Create the container if it does not already exist.
container.CreateIfNotExists();

// Fetch container properties and write out their values.
await container.FetchAttributesAsync();
Console.WriteLine("Properties for container {0}", container.StorageUri.PrimaryUri.ToString());
Console.WriteLine("LastModifiedUTC: {0}", container.Properties.LastModified.ToString());
Console.WriteLine("ETag: {0}", container.Properties.ETag);
Console.WriteLine();
```

## <a name="setting-and-retrieving-metadata"></a>メタデータの設定と取得
メタデータは、BLOB またはコンテナーのリソースで 1 つ以上の名前と値のペアとして指定できます。 メタデータを設定するには、リソースの **Metadata** コレクションに名前と値のペアを追加した後、**SetMetadata** または **SetMetadataAsync** メソッドを呼び出して値をサービスに保存します。

> [!NOTE]
> メタデータの名前は、C# 識別子の名前付け規則に従う必要があります。
>
>

次のコード例では、コンテナーでメタデータを設定します。 一方の値は、コレクションの **Add** メソッドを使用して設定されます。 もう一方の値は、暗黙的なキーと値の構文を使用して設定されます。 どちらも有効です。

```csharp
public static async Task AddContainerMetadataAsync(CloudBlobContainer container)
{
    // Add some metadata to the container.
    container.Metadata.Add("docType", "textDocuments");
    container.Metadata["category"] = "guidance";

    // Set the container's metadata.
    await container.SetMetadataAsync();
}
```

メタデータを取得するには、次の例に示すように、BLOB またはコンテナーで **FetchAttributes** または **FetchAttributesAsync** メソッドを呼び出して **Metadata** コレクションを設定した後、値を読み取ります。

```csharp
public static async Task ListContainerMetadataAsync(CloudBlobContainer container)
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
```

## <a name="next-steps"></a>次の手順
* [.NET 用 Azure Storage クライアント ライブラリ リファレンス](/dotnet/api/?term=Microsoft.WindowsAzure.Storage)
* [.NET 用 Azure Storage クライアント ライブラリ NuGet パッケージ](https://www.nuget.org/packages/WindowsAzure.Storage/)
