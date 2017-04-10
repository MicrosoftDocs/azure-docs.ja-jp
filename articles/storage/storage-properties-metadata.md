---
title: "Azure Storage のオブジェクトのプロパティおよびメタデータの設定と取得 | Microsoft Docs"
description: "Azure Storage のオブジェクトにカスタム メタデータを格納して、システムのプロパティを設定、取得します。"
services: storage
documentationcenter: 
author: mmacy
manager: timlt
editor: tysonn
ms.assetid: 036f9006-273e-400b-844b-3329045e9e1f
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/13/2017
ms.author: marsma
translationtype: Human Translation
ms.sourcegitcommit: 3868d36948342739eb78b013bb4b466df4381b4f
ms.openlocfilehash: 7c1ca950c3ab1b8ffb754a74597d45b82777838c
ms.lasthandoff: 02/15/2017


---
# <a name="set-and-retrieve-properties-and-metadata"></a>プロパティおよびメタデータの設定と取得
## <a name="overview"></a>概要
Azure Storage のオブジェクトは、含まれるデータのほかにもシステムのプロパティとユーザー定義のメタデータをサポートします。

* **システムのプロパティ。** システムのプロパティは、各ストレージ リソースに存在します。 このようなプロパティには、読み取りまたは設定可能なものもありますが、読み取り専用のものもあります。 実際には、システムのプロパティの一部は、特定の標準 HTTP ヘッダーに対応しています。 これらは Azure ストレージ クライアント ライブラリで管理されます。
* **ユーザー定義のメタデータ。** ユーザー定義のメタデータは、名前と値のペアの形式で、特定のリソースで指定したメタデータです。 メタデータを使用すると、ストレージ リソースで追加の値を格納できます。つまり、これらの値は、自身の目的のためだけに存在するため、リソースの動作には影響しません。

ストレージ リソースのプロパティとメタデータの値を取得するには、2 つの手順が必要です。 これらの値を読み取るには、 **FetchAttributes** メソッドを呼び出して値を明示的に取得しておく必要があります。

> [!IMPORTANT]
> ストレージ リソースのプロパティとメタデータの値は、いずれかの **FetchAttributes** メソッドを呼び出さない限り、設定されません。
>
> 名前/値ペアに非 ASCII 文字が含まれていると、`400 Bad Request` を受け取ります。 メタデータ名/値ペアは有効な HTTP ヘッダーです。HTTP ヘッダーを管理するすべての制約に準拠する必要があります。 このため、非 ASCII 文字を含む名前と値には URL エンコードまたは Base64 エンコードを使用することをお勧めします。
>

## <a name="setting-and-retrieving-properties"></a>プロパティの設定と取得
プロパティ値を取得するには、BLOB またはコンテナーで **FetchAttributes** メソッドを呼び出してプロパティを設定した後、値を読み取ります。

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
container.FetchAttributes();
Console.WriteLine("Properties for container {0}", container.StorageUri.PrimaryUri.ToString());
Console.WriteLine("LastModifiedUTC: {0}", container.Properties.LastModified.ToString());
Console.WriteLine("ETag: {0}", container.Properties.ETag);
Console.WriteLine();
```

## <a name="setting-and-retrieving-metadata"></a>メタデータの設定と取得
メタデータは、BLOB またはコンテナーのリソースで&1; つ以上の名前と値のペアとして指定できます。 メタデータを設定するには、リソースの **Metadata** コレクションに名前と値のペアを追加した後、**SetMetadata** メソッドを呼び出して値をサービスに保存します。

> [!NOTE]
> メタデータの名前は、C# 識別子の名前付け規則に従う必要があります。
>
>

次のコード例では、コンテナーでメタデータを設定します。 一方の値は、コレクションの **Add** メソッドを使用して設定されます。 もう一方の値は、暗黙的なキーと値の構文を使用して設定されます。 どちらも有効です。

```csharp
public static void AddContainerMetadata(CloudBlobContainer container)
{
    //Add some metadata to the container.
    container.Metadata.Add("docType", "textDocuments");
    container.Metadata["category"] = "guidance";

    //Set the container's metadata.
    container.SetMetadata();
}
```

メタデータを取得するには、次の例に示すように、BLOB またはコンテナーで **FetchAttributes** メソッドを呼び出して **Metadata** コレクションを設定した後、値を読み取ります。

```csharp
public static void ListContainerMetadata(CloudBlobContainer container)
{
    //Fetch container attributes in order to populate the container's properties and metadata.
    container.FetchAttributes();

    //Enumerate the container's metadata.
    Console.WriteLine("Container metadata:");
    foreach (var metadataItem in container.Metadata)
    {
        Console.WriteLine("\tKey: {0}", metadataItem.Key);
        Console.WriteLine("\tValue: {0}", metadataItem.Value);
    }
}
```

## <a name="see-also"></a>関連項目
* [.NET 用 Azure Storage クライアント ライブラリ リファレンス](http://msdn.microsoft.com/library/azure/wa_storage_30_reference_home.aspx)
* [.NET 用 Azure Storage クライアント ライブラリ パッケージ](https://www.nuget.org/packages/WindowsAzure.Storage/)


