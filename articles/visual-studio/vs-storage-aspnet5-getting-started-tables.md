---
title: Visual Studio を使用して Table Storage の利用を開始する (ASP.NET Core)
description: Visual Studio 接続済みサービスを使用してストレージ アカウントに接続した後で、Visual Studio の ASP.NET Core プロジェクトで Azure Table Storage の使用を開始する方法について説明します。
services: storage
author: ghogen
manager: jillfra
ms.assetid: c3c451d1-71ff-4222-a348-c41c98a02b85
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.custom: vs-azure
ms.workload: azure-vs
ms.topic: conceptual
ms.date: 11/14/2017
ms.author: ghogen
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: d209f8117b1e061877daf2f8d316bd01ed4f84cd
ms.sourcegitcommit: 8b44498b922f7d7d34e4de7189b3ad5a9ba1488b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/13/2019
ms.locfileid: "72298818"
---
# <a name="how-to-get-started-with-azure-table-storage-and-visual-studio-connected-services"></a>Azure Table Storage と Visual Studio 接続済みサービスの概要

[!INCLUDE [storage-try-azure-tools-tables](../../includes/storage-try-azure-tools-tables.md)]

この記事では、Visual Studio の**接続済みサービス**機能を使用して ASP.NET Core プロジェクトで Azure ストレージ アカウントを参照または作成した後に、Visual Studio で Azure Table Storage の使用を開始する方法について説明します。 **接続済みサービス**の操作によって、プロジェクト内の Azure Storage にアクセスする適切な NuGet パッケージがインストールされ、プロジェクトの構成ファイルにストレージ アカウントの接続文字列が追加されます。 (Azure Storage の一般情報については、「[Azure Storage のドキュメント](https://azure.microsoft.com/documentation/services/storage/)」をご覧ください。)

Azure Table Storage サービスを使用すると、大量の構造化データを格納できるようになります。 このサービスは、Azure クラウドの内部および外部からの認証された呼び出しを受け付ける NoSQL データストアです。 Azure のテーブルは、構造化された非リレーショナル データを格納するのに最適です。 Azure Table Storage の使用に関する全般的な情報については、「 [.NET を使用して Azure Table Storage を使用する](../storage/storage-dotnet-how-to-use-tables.md)」をご覧ください。

最初に、ストレージ アカウント内にテーブルを作成します。 この記事ではその後、C# でテーブルを作成する方法と、テーブル エントリの追加、変更、読み取り、および削除などの基本的なテーブル操作を実行する方法を示します。  コードでは .NET 用の Azure Storage クライアント ライブラリを使用します。 ASP.NET の詳細については、 [ASP.NET](https://www.asp.net)に関するページを参照してください。

一部の Azure Storage API は非同期であるため、この記事のコードでは非同期メソッドが使用されている前提とします。 詳細については、[非同期プログラミング](https://docs.microsoft.com/dotnet/csharp/async)に関するページをご覧ください。

## <a name="access-tables-in-code"></a>コードでテーブルにアクセスする

ASP.NET Core プロジェクト内のテーブルにアクセスするには、Azure Table Storage にアクセスする C# ソース ファイルに、次の項目を含める必要があります。

1. 必要な `using` ステートメントを追加します。

    ```csharp
    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Table;
    using System.Threading.Tasks;
    ```

1. お客様のストレージ アカウント情報を表す `CloudStorageAccount` オブジェクトを取得します。 次のコードを使用し、ストレージ アカウントの名前とアカウント キーを使用します。これらは、appSettings.json のストレージ接続文字列で見つけることができます。

    ```csharp
        CloudStorageAccount storageAccount = new CloudStorageAccount(
            new Microsoft.WindowsAzure.Storage.Auth.StorageCredentials(
                "<name>", "<account-key>"), true);
    ```

1. ストレージ アカウント内のテーブル オブジェクトを参照する `CloudTableClient` オブジェクトを取得します。

    ```csharp
    // Create the table client.
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();
    ```

1. 特定のテーブルとエンティティを参照する `CloudTable` 参照オブジェクトを取得します。

    ```csharp
    // Get a reference to a table named "peopleTable"
    CloudTable peopleTable = tableClient.GetTableReference("peopleTable");
    ```

## <a name="create-a-table-in-code"></a>コードでテーブルを作成する

Azure テーブルを作成するには、非同期メソッドを作成して、その中で `CreateIfNotExistsAsync()` を呼び出します。

```csharp
async void CreatePeopleTableAsync()
{
    // Create the CloudTable if it does not exist
    await peopleTable.CreateIfNotExistsAsync();
}
```
    
## <a name="add-an-entity-to-a-table"></a>エンティティをテーブルに追加する

エンティティをテーブルに追加するには、エンティティのプロパティを定義するクラスを作成します。 次のコードは、ユーザーの名を行キーとして、姓をパーティション キーとしてそれぞれ使用する、`CustomerEntity` という名前のエンティティ クラスを定義します。

```csharp
public class CustomerEntity : TableEntity
{
    public CustomerEntity(string lastName, string firstName)
    {
        this.PartitionKey = lastName;
        this.RowKey = firstName;
    }

    public CustomerEntity() { }

    public string Email { get; set; }

    public string PhoneNumber { get; set; }
}
```

エンティティに関連するテーブル操作では、「[コードでテーブルにアクセスする](#access-tables-in-code)」で作成した `CloudTable` オブジェクトを使用します。 `TableOperation` オブジェクトは、実行する操作を表します。 次のコード例は、`CloudTable` オブジェクトと `CustomerEntity` オブジェクトを作成する方法を示しています。 操作を準備するために、ユーザー エンティティをテーブルに挿入する `TableOperation` を作成します。 最後に、`CloudTable.ExecuteAsync` を呼び出して操作を実行します。

```csharp
// Create a new customer entity.
CustomerEntity customer1 = new CustomerEntity("Harp", "Walter");
customer1.Email = "Walter@contoso.com";
customer1.PhoneNumber = "425-555-0101";

// Create the TableOperation that inserts the customer entity.
TableOperation insertOperation = TableOperation.Insert(customer1);

// Execute the insert operation.
await peopleTable.ExecuteAsync(insertOperation);
```

## <a name="insert-a-batch-of-entities"></a>エンティティのバッチを挿入する

1 回の書き込み操作で複数のエンティティをテーブルに挿入できます。 次のコード例は、2 つのエンティティ オブジェクト ("Jeff Smith" と "Ben Smith") を作成し、`Insert` メソッドを使用して `TableBatchOperation` オブジェクトにそれらを追加した後、`CloudTable.ExecuteBatchAsync` を呼び出して操作を開始します。

```csharp
// Create the batch operation.
TableBatchOperation batchOperation = new TableBatchOperation();

// Create a customer entity and add it to the table.
CustomerEntity customer1 = new CustomerEntity("Smith", "Jeff");
customer1.Email = "Jeff@contoso.com";
customer1.PhoneNumber = "425-555-0104";

// Create another customer entity and add it to the table.
CustomerEntity customer2 = new CustomerEntity("Smith", "Ben");
customer2.Email = "Ben@contoso.com";
customer2.PhoneNumber = "425-555-0102";

// Add both customer entities to the batch insert operation.
batchOperation.Insert(customer1);
batchOperation.Insert(customer2);

// Execute the batch operation.
await peopleTable.ExecuteBatchAsync(batchOperation);
```

## <a name="get-all-of-the-entities-in-a-partition"></a>パーティション内のすべてのエンティティを取得する

テーブルに対してパーティション内のすべてのエンティティを照会する場合は、`TableQuery` オブジェクトを使用します。 次のコード例は、'Smith' がパーティション キーであるエンティティに対してフィルターを指定します。 この例は、クエリ結果の各エンティティのフィールドをコンソールに出力します。

```csharp
// Construct the query operation for all customer entities where PartitionKey="Smith".
TableQuery<CustomerEntity> query = new TableQuery<CustomerEntity>().Where(TableQuery.GenerateFilterCondition("PartitionKey", QueryComparisons.Equal, "Smith"));

// Print the fields for each customer.
TableContinuationToken token = null;
do
{
    TableQuerySegment<CustomerEntity> resultSegment = await peopleTable.ExecuteQuerySegmentedAsync(query, token);
    token = resultSegment.ContinuationToken;

    foreach (CustomerEntity entity in resultSegment.Results)
    {
        Console.WriteLine("{0}, {1}\t{2}\t{3}", entity.PartitionKey, entity.RowKey,
        entity.Email, entity.PhoneNumber);
    }
} while (token != null);
```

## <a name="get-a-single-entity"></a>単一のエンティティを取得する

単一の特定のエンティティを取得するクエリを記述することができます。 次のコードは、`TableOperation` オブジェクトを使用して、"Ben Smith" という名前のユーザーを指定します。 このメソッドで返されるのは、エンティティのコレクションではなく、単一のエンティティのみであり、`TableResult.Result` の戻り値は `CustomerEntity` オブジェクトです。 クエリでパーティション キーと行キーの両方を指定することが、`Table` サービスから単一のエンティティを取得する最速の方法です。

```csharp
// Create a retrieve operation that takes a customer entity.
TableOperation retrieveOperation = TableOperation.Retrieve<CustomerEntity>("Smith", "Ben");

// Execute the retrieve operation.
TableResult retrievedResult = await peopleTable.ExecuteAsync(retrieveOperation);

// Print the phone number of the result.
if (retrievedResult.Result != null)
   Console.WriteLine(((CustomerEntity)retrievedResult.Result).PhoneNumber);
else
   Console.WriteLine("The phone number could not be retrieved.");
```

## <a name="delete-an-entity"></a>エンティティを削除する

エンティティは、検索して削除できます。 次のコードは、"Ben Smith" という名前のユーザー エンティティを検索して削除します。

```csharp
// Create a retrieve operation that expects a customer entity.
TableOperation retrieveOperation = TableOperation.Retrieve<CustomerEntity>("Smith", "Ben");

// Execute the operation.
TableResult retrievedResult = await peopleTable.ExecuteAsync(retrieveOperation);

// Assign the result to a CustomerEntity object.
CustomerEntity deleteEntity = (CustomerEntity)retrievedResult.Result;

// Create the Delete TableOperation and then execute it.
if (deleteEntity != null)
{
   TableOperation deleteOperation = TableOperation.Delete(deleteEntity);

   // Execute the operation.
   await peopleTable.ExecuteAsync(deleteOperation);

   Console.WriteLine("Entity deleted.");
}

else
   Console.WriteLine("Couldn't delete the entity.");
```

## <a name="next-steps"></a>次の手順
[!INCLUDE [vs-storage-dotnet-tables-next-steps](../../includes/vs-storage-dotnet-tables-next-steps.md)]
