---
title: ".NET を使用して Azure Table Storage を使用する | Microsoft Docs"
description: "NoSQL データ ストアである Azure Table Storage を使用して構造化データをクラウドに格納します。"
services: storage
documentationcenter: .net
author: mmacy
manager: timlt
editor: tysonn
ms.assetid: fe46d883-7bed-49dd-980e-5c71df36adb3
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: hero-article
ms.date: 03/27/2016
ms.author: marsma
translationtype: Human Translation
ms.sourcegitcommit: 6e0ad6b5bec11c5197dd7bded64168a1b8cc2fdd
ms.openlocfilehash: 7a9a28ce8be7587c84a1188d643c990cc4fb7355
ms.lasthandoff: 03/28/2017


---
# <a name="get-started-with-azure-table-storage-using-net"></a>.NET を使用して Azure Table Storage を使用する
[!INCLUDE [storage-selector-table-include](../../includes/storage-selector-table-include.md)]

[!INCLUDE [storage-check-out-samples-dotnet](../../includes/storage-check-out-samples-dotnet.md)]

## <a name="overview"></a>Overview
Azure Table Storage は、NoSQL の構造化データをクラウド内に格納するサービスです。 Table Storage は、スキーマなしの設計によるキーまたは属性ストアです。 Table Storage はスキーマがないため、アプリケーションの進化のニーズに合わせてデータを容易に修正できます。 あらゆる種類のデータに、高速かつ経済的にアクセスできます。 Table Storage は、通常、従来の SQL と比較して、同様の容量のデータをはるかに低コストで保存できます。

Table Storage を使用すると、Web アプリケーションのユーザー データ、アドレス帳、デバイス情報、およびサービスに必要なその他の種類のメタデータなど、柔軟なデータセットを保存できます。 ストレージ アカウントの容量の上限を超えない限り、テーブルには任意の数のエンティティを保存でき、ストレージ アカウントには任意の数のテーブルを含めることができます。

### <a name="about-this-tutorial"></a>このチュートリアルについて
このチュートリアルでは、Azure Table Storage を使用して、テーブルの作成と削除、およびテーブル データの挿入、更新、削除、クエリなどの一般的なシナリオの .NET コードを記述する方法を示します。

**前提条件:**

* [Microsoft Visual Studio](https://www.visualstudio.com/visual-studio-homepage-vs.aspx)
* [.NET 用 Azure Storage クライアント ライブラリ](https://www.nuget.org/packages/WindowsAzure.Storage/)
* [.NET 用 Azure Configuration Manager](https://www.nuget.org/packages/Microsoft.WindowsAzure.ConfigurationManager/)
* [Azure ストレージ アカウント](storage-create-storage-account.md#create-a-storage-account)

[!INCLUDE [storage-dotnet-client-library-version-include](../../includes/storage-dotnet-client-library-version-include.md)]

### <a name="more-samples"></a>その他のサンプル
Table Storage を使用したその他の例については、「 [Getting Started with Azure Table Storage in .NET (.NET での Azure Blob Storage の使用)](https://azure.microsoft.com/documentation/samples/storage-table-dotnet-getting-started/)」を参照してください。 サンプル アプリケーションをダウンロードして実行することも、GitHub でコードを参照することもできます。

[!INCLUDE [storage-table-concepts-include](../../includes/storage-table-concepts-include.md)]

[!INCLUDE [storage-create-account-include](../../includes/storage-create-account-include.md)]

[!INCLUDE [storage-development-environment-include](../../includes/storage-development-environment-include.md)]

### <a name="add-using-directives"></a>using ディレクティブを追加する
次の **using** ディレクティブを `Program.cs` ファイルの先頭に追加します。

```csharp
using Microsoft.Azure; // Namespace for CloudConfigurationManager
using Microsoft.WindowsAzure.Storage; // Namespace for CloudStorageAccount
using Microsoft.WindowsAzure.Storage.Table; // Namespace for Table storage types
```

### <a name="parse-the-connection-string"></a>接続文字列を解析する
[!INCLUDE [storage-cloud-configuration-manager-include](../../includes/storage-cloud-configuration-manager-include.md)]

### <a name="create-the-table-service-client"></a>Table サービス クライアントを作成する
**CloudTableClient** クラスを使用すると、Table Storage に格納されているテーブルとエンティティを取得できます。 サービス クライアントを作成する方法の 1 つを次に示します。

```csharp
// Create the table client.
CloudTableClient tableClient = storageAccount.CreateCloudTableClient();
```

これで、Table Storage に対してデータの読み取りと書き込みを実行するコードを記述する準備が整いました。

## <a name="create-a-table"></a>テーブルを作成する
この例は、テーブルがない場合に、キューを作成する方法を示しています。

```csharp
// Retrieve the storage account from the connection string.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create the table client.
CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

// Retrieve a reference to the table.
CloudTable table = tableClient.GetTableReference("people");

// Create the table if it doesn't exist.
table.CreateIfNotExists();
```

## <a name="add-an-entity-to-a-table"></a>エンティティをテーブルに追加する
エンティティは、**TableEntity** から派生するカスタム クラスを使用して C\# オブジェクトにマップされます。 エンティティをテーブルに追加するには、エンティティのプロパティを定義するクラスを作成します。 次のコードは、ユーザーの名を行キーとして、姓をパーティション キーとしてそれぞれ使用するエンティティ クラスを定義します。 エンティティのパーティション キーと行キーの組み合わせで、テーブル内のエンティティを一意に識別します。 同じパーティション キーを持つエンティティは、異なるパーティション キーを持つエンティティよりも迅速に照会できます。一方、多様なパーティション キーを使用すると、並列操作の拡張性が向上します。 Table service に格納するプロパティは、設定値と取得値の両方を公開する、サポートされている型のパブリック プロパティである必要があります。
また、エンティティ型で、パラメーターのないコンストラクターを "*必ず*" 公開する必要があります。

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

エンティティに関連するテーブル操作は、テーブルの作成に関するセクションで作成した **CloudTable** オブジェクトを使用して実行されます。 実行する操作は、 **TableOperation** オブジェクトによって表されます。  次のコード例では、まず **CloudTable** オブジェクトを作成し、次に **CustomerEntity** オブジェクトを作成します。  その後、操作を準備するために、ユーザー エンティティをテーブルに挿入する **TableOperation** オブジェクトを作成します。  最後に、 **CloudTable.Execute**を呼び出して操作を実行します。

```csharp
// Retrieve the storage account from the connection string.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create the table client.
CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

// Create the CloudTable object that represents the "people" table.
CloudTable table = tableClient.GetTableReference("people");

// Create a new customer entity.
CustomerEntity customer1 = new CustomerEntity("Harp", "Walter");
customer1.Email = "Walter@contoso.com";
customer1.PhoneNumber = "425-555-0101";

// Create the TableOperation object that inserts the customer entity.
TableOperation insertOperation = TableOperation.Insert(customer1);

// Execute the insert operation.
table.Execute(insertOperation);
```

## <a name="insert-a-batch-of-entities"></a>エンティティのバッチを挿入する
1 回の書き込み操作でエンティティのバッチをテーブルに挿入できます。 バッチ操作に関しては、次の事項にも留意してください。

* 更新、削除、および挿入を同じ 1 回のバッチ操作で実行できます。
* 1 つのバッチ操作には、最大 100 個のエンティティを含めることができます。
* 1 つのバッチ操作に含まれるすべてのエンティティのパーティション キーが同じである必要があります。
* クエリをバッチ操作として実行することもできますが、バッチ内の唯一の操作である必要があります。

<!-- -->
次のコード例は、2 つのエンティティ オブジェクトを作成し、**Insert** メソッドを使用して **TableBatchOperation** に追加します。 その後、 **CloudTable.Execute** を呼び出して操作を実行します。

```csharp
// Retrieve the storage account from the connection string.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create the table client.
CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

// Create the CloudTable object that represents the "people" table.
CloudTable table = tableClient.GetTableReference("people");

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
table.ExecuteBatch(batchOperation);
```

## <a name="retrieve-all-entities-in-a-partition"></a>パーティション内のすべてのエンティティを取得する
テーブルに対してパーティション内のすべてのエンティティを照会する場合は、 **TableQuery** オブジェクトを使用します。
次のコード例は、'Smith' がパーティション キーであるエンティティに対してフィルターを指定します。 この例は、クエリ結果の各エンティティのフィールドをコンソールに出力します。

```csharp
// Retrieve the storage account from the connection string.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create the table client.
CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

// Create the CloudTable object that represents the "people" table.
CloudTable table = tableClient.GetTableReference("people");

// Construct the query operation for all customer entities where PartitionKey="Smith".
TableQuery<CustomerEntity> query = new TableQuery<CustomerEntity>().Where(TableQuery.GenerateFilterCondition("PartitionKey", QueryComparisons.Equal, "Smith"));

// Print the fields for each customer.
foreach (CustomerEntity entity in table.ExecuteQuery(query))
{
    Console.WriteLine("{0}, {1}\t{2}\t{3}", entity.PartitionKey, entity.RowKey,
        entity.Email, entity.PhoneNumber);
}
```

## <a name="retrieve-a-range-of-entities-in-a-partition"></a>パーティション内の一定範囲のエンティティを取得する
パーティション内の一部のエンティティのみを照会する場合は、パーティション キー フィルターと行キー フィルターを組み合わせて範囲を指定できます。 次のコード例は、2 つのフィルターを使用して、行キー (名) がアルファベットの 'E' より前の文字で始まる、'Smith' というパーティション内のすべてのエンティティを取得し、クエリ結果を出力します。

```csharp
// Retrieve the storage account from the connection string.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create the table client.
CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

// Create the CloudTable object that represents the "people" table.
CloudTable table = tableClient.GetTableReference("people");

// Create the table query.
TableQuery<CustomerEntity> rangeQuery = new TableQuery<CustomerEntity>().Where(
    TableQuery.CombineFilters(
        TableQuery.GenerateFilterCondition("PartitionKey", QueryComparisons.Equal, "Smith"),
        TableOperators.And,
        TableQuery.GenerateFilterCondition("RowKey", QueryComparisons.LessThan, "E")));

// Loop through the results, displaying information about the entity.
foreach (CustomerEntity entity in table.ExecuteQuery(rangeQuery))
{
    Console.WriteLine("{0}, {1}\t{2}\t{3}", entity.PartitionKey, entity.RowKey,
        entity.Email, entity.PhoneNumber);
}
```

## <a name="retrieve-a-single-entity"></a>単一のエンティティを取得する
単一の特定のエンティティを取得するクエリを記述することができます。 次のコードは、**TableOperation** を使用して、"Ben Smith" というユーザーを指定します。
このメソッドで返されるのは、エンティティのコレクションではなく、単一のエンティティのみです。したがって、**TableResult.Result** の戻り値は **CustomerEntity** オブジェクトです。
クエリでパーティション キーと行キーの両方を指定することが、Table サービスから単一のエンティティを取得するための最速の方法です。

```csharp
// Retrieve the storage account from the connection string.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create the table client.
CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

// Create the CloudTable object that represents the "people" table.
CloudTable table = tableClient.GetTableReference("people");

// Create a retrieve operation that takes a customer entity.
TableOperation retrieveOperation = TableOperation.Retrieve<CustomerEntity>("Smith", "Ben");

// Execute the retrieve operation.
TableResult retrievedResult = table.Execute(retrieveOperation);

// Print the phone number of the result.
if (retrievedResult.Result != null)
{
    Console.WriteLine(((CustomerEntity)retrievedResult.Result).PhoneNumber);
}
else
{
    Console.WriteLine("The phone number could not be retrieved.");
}
```

## <a name="replace-an-entity"></a>エンティティを置換する
エンティティを更新するには、そのエンティティを Table サービスから取得し、エンティティ オブジェクトを変更して、変更を Table サービスに戻して保存します。 次のコードは、既存のユーザーの電話番号を変更します。 このコードでは、**Insert** を呼び出す代わりに **Replace** を使用しています。 これにより、サーバーでエンティティが完全に置換されます。ただし、エンティティを取得した後にサーバーでエンティティが変更された場合は、操作が失敗します。  このエラーは、取得と更新の間にアプリケーションの別のコンポーネントによって行われた変更が意図せず上書きされるのを防ぐためのものです。  このエラーを正しく処理するには、もう一度エンティティを取得し、変更を加えて (その変更がまだ有効な場合)、再び **Replace** 操作を実行します。  次のセクションでは、この動作をオーバーライドする方法を説明します。

```csharp
// Retrieve the storage account from the connection string.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create the table client.
CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

// Create the CloudTable object that represents the "people" table.
CloudTable table = tableClient.GetTableReference("people");

// Create a retrieve operation that takes a customer entity.
TableOperation retrieveOperation = TableOperation.Retrieve<CustomerEntity>("Smith", "Ben");

// Execute the operation.
TableResult retrievedResult = table.Execute(retrieveOperation);

// Assign the result to a CustomerEntity object.
CustomerEntity updateEntity = (CustomerEntity)retrievedResult.Result;

if (updateEntity != null)
{
    // Change the phone number.
    updateEntity.PhoneNumber = "425-555-0105";

    // Create the Replace TableOperation.
    TableOperation updateOperation = TableOperation.Replace(updateEntity);

    // Execute the operation.
    table.Execute(updateOperation);

    Console.WriteLine("Entity updated.");
}
else
{
    Console.WriteLine("Entity could not be retrieved.");
}
```

## <a name="insert-or-replace-an-entity"></a>エンティティを挿入または置換する
**Replace** 操作は、サーバーからエンティティを取得した後にエンティティが変更されていると失敗します。  さらに、 **Replace** 操作を成功させるためには、先にエンティティをサーバーから取得する必要があります。
しかし、サーバーにエンティティが存在するかどうかわからないが、現在格納されている値は不適切である場合があります。 このため、更新ですべての値を上書きする必要があります。  そのような場合は、 **InsertOrReplace** 操作を使用します。  この操作は、最終更新日時に関係なく、エンティティが存在しない場合は挿入し、存在する場合は置換します。  次のコード例では、先ほどと同じように Ben Smith のユーザー エンティティを取得していますが、今度は **InsertOrReplace**を使用してサーバーに保存しています。  そのため、取得操作と更新操作の間に行われたエンティティの更新は上書きされます。

```csharp
// Retrieve the storage account from the connection string.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create the table client.
CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

// Create the CloudTable object that represents the "people" table.
CloudTable table = tableClient.GetTableReference("people");

// Create a retrieve operation that takes a customer entity.
TableOperation retrieveOperation = TableOperation.Retrieve<CustomerEntity>("Smith", "Ben");

// Execute the operation.
TableResult retrievedResult = table.Execute(retrieveOperation);

// Assign the result to a CustomerEntity object.
CustomerEntity updateEntity = (CustomerEntity)retrievedResult.Result;

if (updateEntity != null)
{
    // Change the phone number.
    updateEntity.PhoneNumber = "425-555-1234";

    // Create the InsertOrReplace TableOperation.
    TableOperation insertOrReplaceOperation = TableOperation.InsertOrReplace(updateEntity);

    // Execute the operation.
    table.Execute(insertOrReplaceOperation);

    Console.WriteLine("Entity was updated.");
}
else
{
    Console.WriteLine("Entity could not be retrieved.");
}
```

## <a name="query-a-subset-of-entity-properties"></a>エンティティ プロパティのサブセットを照会する
テーブル クエリでは、エンティティのすべてのプロパティではなくごくわずかのプロパティだけをエンティティから取得できます。 プロジェクションと呼ばれるこの方法では、帯域幅の使用が削減され、クエリのパフォーマンスが向上します。特に、大量のエンティティがある場合に役立ちます。 次のコードのクエリは、テーブル内のエンティティの電子メール アドレスだけを返します。 これは、**DynamicTableEntity** のクエリと **EntityResolver** を使用して行われます。 プロジェクションの詳細については、[アップサートおよびクエリ プロジェクションの概要に関するブログ記事][Introducing Upsert and Query Projection blog post]をご覧ください。 プロジェクションはローカル ストレージ エミュレーターではサポートされていません。したがって、このコードは Table サービスのアカウントを使用している場合にのみ機能します。

```csharp
// Retrieve the storage account from the connection string.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create the table client.
CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

// Create the CloudTable that represents the "people" table.
CloudTable table = tableClient.GetTableReference("people");

// Define the query, and select only the Email property.
TableQuery<DynamicTableEntity> projectionQuery = new TableQuery<DynamicTableEntity>().Select(new string[] { "Email" });

// Define an entity resolver to work with the entity after retrieval.
EntityResolver<string> resolver = (pk, rk, ts, props, etag) => props.ContainsKey("Email") ? props["Email"].StringValue : null;

foreach (string projectedEmail in table.ExecuteQuery(projectionQuery, resolver, null, null))
{
    Console.WriteLine(projectedEmail);
}
```

## <a name="delete-an-entity"></a>エンティティを削除する
エンティティは、取得後に簡単に削除できます。エンティティの更新のときと同じパターンを使用します。  次のコードは、ユーザー エンティティを取得して削除します。

```csharp
// Retrieve the storage account from the connection string.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create the table client.
CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

// Create the CloudTable that represents the "people" table.
CloudTable table = tableClient.GetTableReference("people");

// Create a retrieve operation that expects a customer entity.
TableOperation retrieveOperation = TableOperation.Retrieve<CustomerEntity>("Smith", "Ben");

// Execute the operation.
TableResult retrievedResult = table.Execute(retrieveOperation);

// Assign the result to a CustomerEntity.
CustomerEntity deleteEntity = (CustomerEntity)retrievedResult.Result;

// Create the Delete TableOperation.
if (deleteEntity != null)
{
    TableOperation deleteOperation = TableOperation.Delete(deleteEntity);

    // Execute the operation.
    table.Execute(deleteOperation);

    Console.WriteLine("Entity deleted.");
}
else
{
    Console.WriteLine("Could not retrieve the entity.");
}
```

## <a name="delete-a-table"></a>テーブルを削除する
最後に、次のコード例は、ストレージ アカウントからテーブルを削除します。 削除されたテーブルは、削除後の一定期間は再作成できなくなります。

```csharp
// Retrieve the storage account from the connection string.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create the table client.
CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

// Create the CloudTable that represents the "people" table.
CloudTable table = tableClient.GetTableReference("people");

// Delete the table it if exists.
table.DeleteIfExists();
```

## <a name="retrieve-entities-in-pages-asynchronously"></a>ページ内のエンティティを非同期的に取得する
多数のエンティティを読み取る場合、すべてが返されるまで待機するのではなく取得後に処理または表示するには、セグメント化されたクエリを使用してエンティティを取得できます。 この例は、大きな結果のセットが返されるのを待機している間に実行がブロックされないように、Async-Await パターンを使用して結果をページに返す方法を示しています。 .NET での Async-Await パターンの使用方法の詳細については、 [Async および Await を使用した非同期プログラミング (C# および Visual Basic)](https://msdn.microsoft.com/library/hh191443.aspx)に関するページをご覧ください。

```csharp
// Initialize a default TableQuery to retrieve all the entities in the table.
TableQuery<CustomerEntity> tableQuery = new TableQuery<CustomerEntity>();

// Initialize the continuation token to null to start from the beginning of the table.
TableContinuationToken continuationToken = null;

do
{
    // Retrieve a segment (up to 1,000 entities).
    TableQuerySegment<CustomerEntity> tableQueryResult =
        await table.ExecuteQuerySegmentedAsync(tableQuery, continuationToken);

    // Assign the new continuation token to tell the service where to
    // continue on the next iteration (or null if it has reached the end).
    continuationToken = tableQueryResult.ContinuationToken;

    // Print the number of rows retrieved.
    Console.WriteLine("Rows retrieved {0}", tableQueryResult.Results.Count);

// Loop until a null continuation token is received, indicating the end of the table.
} while(continuationToken != null);
```

## <a name="next-steps"></a>次のステップ
これで、テーブル ストレージの基本を学習できました。さらに複雑なストレージ タスクについては、次のリンク先を参照してください。

* Table Storage のサンプルについては、「 [Getting Started with Azure Table Storage in .NET (.NET での Azure Blob Storage の使用)](https://azure.microsoft.com/documentation/samples/storage-table-dotnet-getting-started/)
* 利用可能な API の詳細については、テーブル サービスのリファレンス ドキュメントを参照してください。
  * [.NET 用ストレージ クライアント ライブラリ リファレンス](http://go.microsoft.com/fwlink/?LinkID=390731&clcid=0x409)
  * [REST API リファレンス](http://msdn.microsoft.com/library/azure/dd179355)
* Azure Storage で作業するために記述したコードを簡略化する方法については、 [Azure WebJobs SDK とは](../app-service-web/websites-dotnet-webjobs-sdk-get-started.md)
* Azure でデータを格納するための追加のオプションについては、他の機能ガイドも参照してください。
  * [.NET を使用して Azure Blob Storage を使用する](storage-dotnet-how-to-use-blobs.md) 」を参照してください。
  * リレーショナル データを格納する方法については、「[.NET (C#) を使用して SQL Database に接続する](../sql-database/sql-database-develop-dotnet-simple.md)」を参照してください。

[Download and install the Azure SDK for .NET]: /develop/net/
[Creating an Azure Project in Visual Studio]: http://msdn.microsoft.com/library/azure/ee405487.aspx

[Blob5]: ./media/storage-dotnet-how-to-use-table-storage/blob5.png
[Blob6]: ./media/storage-dotnet-how-to-use-table-storage/blob6.png
[Blob7]: ./media/storage-dotnet-how-to-use-table-storage/blob7.png
[Blob8]: ./media/storage-dotnet-how-to-use-table-storage/blob8.png
[Blob9]: ./media/storage-dotnet-how-to-use-table-storage/blob9.png

[Introducing Upsert and Query Projection blog post]: http://blogs.msdn.com/b/windowsazurestorage/archive/2011/09/15/windows-azure-tables-introducing-upsert-and-query-projection.aspx
[.NET Client Library reference]: http://go.microsoft.com/fwlink/?LinkID=390731&clcid=0x409
[Azure Storage Team blog]: http://blogs.msdn.com/b/windowsazurestorage/
[Configure Azure Storage connection strings]: http://msdn.microsoft.com/library/azure/ee758697.aspx
[OData]: http://nuget.org/packages/Microsoft.Data.OData/5.0.2
[Edm]: http://nuget.org/packages/Microsoft.Data.Edm/5.0.2
[Spatial]: http://nuget.org/packages/System.Spatial/5.0.2
[How to: Programmatically access Table storage]: #tablestorage

