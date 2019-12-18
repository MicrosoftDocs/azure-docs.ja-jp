---
title: Visual Studio を使用して Table Storage の利用を開始する (クラウド サービス)
description: Visual Studio 接続済みサービスを使用してストレージ アカウントに接続した後、Visual Studio のクラウド サービス プロジェクトで Azure Table ストレージの使用を開始する方法について説明します。
services: storage
author: ghogen
manager: jillfra
ms.assetid: a3a11ed8-ba7f-4193-912b-e555f5b72184
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.custom: vs-azure
ms.workload: azure-vs
ms.topic: conceptual
ms.date: 12/02/2016
ms.author: ghogen
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 5c42d65b5e2c46fcdbe1b0725f2ebce881722db3
ms.sourcegitcommit: 8b44498b922f7d7d34e4de7189b3ad5a9ba1488b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/13/2019
ms.locfileid: "72299993"
---
# <a name="getting-started-with-azure-table-storage-and-visual-studio-connected-services-cloud-services-projects"></a>Azure Table ストレージと Visual Studio 接続済みサービスの概要 (クラウド サービス プロジェクト)
[!INCLUDE [storage-try-azure-tools-tables](../../includes/storage-try-azure-tools-tables.md)]

## <a name="overview"></a>概要
この記事では、Visual Studio の **[接続済みサービスの追加]** ダイアログを使用してクラウド サービス プロジェクトで Azure ストレージ アカウントを作成または参照した後に、Visual Studio で Azure Table Storage の使用を開始する方法について説明します。 **接続済みサービスの追加** 操作によって、プロジェクト内の Azure Storage にアクセスする適切な NuGet パッケージがインストールされ、プロジェクトの構成ファイルに、ストレージ アカウントの接続文字列が追加されます。

Azure Table Storage サービスを使用すると、大量の構造化データを格納できるようになります。 このサービスは、Azure クラウドの内部および外部からの認証された呼び出しを受け付ける NoSQL データストアです。 Azure のテーブルは、構造化された非リレーショナル データを格納するのに最適です。

最初に、ストレージ アカウント内にテーブルを作成する必要があります。 コードで Azure テーブルを作成する方法やテーブルとエンティティの基本的な操作について説明します (テーブル エンティティの追加、変更、読み取りなど)。 サンプルは C\# コードで記述され、[NET 用 Microsoft Azure Storage クライアント ライブラリ](https://msdn.microsoft.com/library/azure/dn261237.aspx)を使用しています。

**注:** Azure ストレージへの呼び出しを実行する API の一部は非同期です。 詳細については、 [Async および Await を使用した非同期プログラミング](https://msdn.microsoft.com/library/hh191443.aspx) に関するページをご覧ください。 次のコードでは、非同期のプログラミング方法を使用していることを前提としています。

* プログラムを使用してテーブルを操作する方法の詳細については、「 [.NET を使用して Azure Table Storage を使用する](../storage/storage-dotnet-how-to-use-tables.md) 」をご覧ください。
* Azure Storage の一般情報については、「 [ストレージのドキュメント](https://azure.microsoft.com/documentation/services/storage/) 」をご覧ください。
* Azure Cloud Services の一般情報については、「 [Cloud Services のドキュメント](https://azure.microsoft.com/documentation/services/cloud-services/) 」をご覧ください。
* ASP.NET アプリケーションのプログラミングについての詳細は、「 [ASP.NET](https://www.asp.net) 」をご覧ください。

## <a name="access-tables-in-code"></a>コードでテーブルにアクセスする
クラウド サービス プロジェクト内のテーブルにアクセスするには、Azure Table Storage にアクセスする C# ソース ファイルに、次の項目を含める必要があります。

1. C# ファイル冒頭の名前空間宣言に、次の **using** ステートメントが含まれていることを確認します。
   
        using Microsoft.Framework.Configuration;
        using Microsoft.WindowsAzure.Storage;
        using Microsoft.WindowsAzure.Storage.Table;
        using System.Threading.Tasks;
        using LogLevel = Microsoft.Framework.Logging.LogLevel;
2. ストレージ アカウント情報を表す **CloudStorageAccount** オブジェクトを取得します。 次のコードを使用して、Azure サービス構成からストレージ接続文字列とストレージ アカウント情報を取得できます。
   
         CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
           CloudConfigurationManager.GetSetting("<storage account name>
         _AzureStorageConnectionString"));
   > [!NOTE]
   > 上記のコードはすべて、以下の例に示すコードの前に使用してください。
   > 
   > 
3. ストレージ アカウント内のテーブル オブジェクトを参照する **CloudTableClient** オブジェクトを取得します。
   
         // Create the table client.
         CloudTableClient tableClient = storageAccount.CreateCloudTableClient();
4. 特定のテーブルとエンティティを参照する **CloudTable** 参照オブジェクトを取得します。
   
        // Get a reference to a table named "peopleTable".
        CloudTable peopleTable = tableClient.GetTableReference("peopleTable");

## <a name="create-a-table-in-code"></a>コードでテーブルを作成する
Azure テーブルは、「コードでテーブルにアクセスする」セクションで説明したように、**CloudTable** オブジェクトを取得した後、**CreateIfNotExistsAsync** へ呼び出しを追加するだけで作成できます。

    // Create the CloudTable if it does not exist.
    await peopleTable.CreateIfNotExistsAsync();

## <a name="add-an-entity-to-a-table"></a>エンティティをテーブルに追加する
エンティティをテーブルに追加するには、エンティティのプロパティを定義するクラスを作成します。 次のコードは、ユーザーの名を行キーとし、姓をパーティション キーとして使用する **CustomerEntity** という名前のエンティティ クラスを定義します。

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

エンティティに関連するテーブル操作は、「コードでテーブルにアクセスする」で作成した **CloudTable** オブジェクトを使用して実行されます。 **TableOperation** オブジェクトは、実行する操作を表しています。 次のコード例では、**CloudTable** オブジェクトと **CustomerEntity** オブジェクトを作成します。 その後、操作を準備するために、ユーザー エンティティをテーブルに挿入する **TableOperation** を作成します。 最後に、 **CloudTable.ExecuteAsync**を呼び出して操作を実行します。

    // Create a new customer entity.
    CustomerEntity customer1 = new CustomerEntity("Harp", "Walter");
    customer1.Email = "Walter@contoso.com";
    customer1.PhoneNumber = "425-555-0101";

    // Create the TableOperation that inserts the customer entity.
    TableOperation insertOperation = TableOperation.Insert(customer1);

    // Execute the insert operation.
    await peopleTable.ExecuteAsync(insertOperation);


## <a name="insert-a-batch-of-entities"></a>エンティティのバッチを挿入する
1 回の書き込み操作で複数のエンティティをテーブルに挿入できます。 次のコード例は、2 つのエンティティ オブジェクト ("Jeff Smith" と "Ben Smith") を作成し、Insert メソッドを使用して **TableBatchOperation** オブジェクトにそれらのエンティティ オブジェクトを追加した後、**CloudTable.ExecuteBatchAsync** を呼び出して操作を開始します。

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

## <a name="get-all-of-the-entities-in-a-partition"></a>パーティション内のすべてのエンティティを取得する
テーブルに対してパーティション内のすべてのエンティティを照会する場合は、 **TableQuery** オブジェクトを使用します。 次のコード例は、'Smith' がパーティション キーであるエンティティに対してフィルターを指定します。 この例は、クエリ結果の各エンティティのフィールドをコンソールに出力します。

    // Construct the query operation for all customer entities where PartitionKey="Smith".
    TableQuery<CustomerEntity> query = new TableQuery<CustomerEntity>()
        .Where(TableQuery.GenerateFilterCondition("PartitionKey", QueryComparisons.Equal, "Smith"));

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

    return View();


## <a name="get-a-single-entity"></a>単一のエンティティを取得する
単一の特定のエンティティを取得するクエリを記述することができます。 次のコードは、**TableOperation** オブジェクトを使用して、"Ben Smith" という名前のユーザーを指定します。 このメソッドで返されるのは、エンティティのコレクションではなく、単一のエンティティのみです。したがって、**TableResult.Result** の戻り値は **CustomerEntity** オブジェクトです。 クエリでパーティション キーと行キーの両方を指定することが、**Table** service から単一のエンティティを取得するための最速の方法です。

    // Create a retrieve operation that takes a customer entity.
    TableOperation retrieveOperation = TableOperation.Retrieve<CustomerEntity>("Smith", "Ben");

    // Execute the retrieve operation.
    TableResult retrievedResult = await peopleTable.ExecuteAsync(retrieveOperation);

    // Print the phone number of the result.
    if (retrievedResult.Result != null)
       Console.WriteLine(((CustomerEntity)retrievedResult.Result).PhoneNumber);
    else
       Console.WriteLine("The phone number could not be retrieved.");

## <a name="delete-an-entity"></a>エンティティを削除する
エンティティは、検索して削除できます。 次のコードは、"Ben Smith" という名前のユーザー エンティティを検索し、見つかったら削除します。

    // Create a retrieve operation that expects a customer entity.
    TableOperation retrieveOperation = TableOperation.Retrieve<CustomerEntity>("Smith", "Ben");

    // Execute the operation.
    TableResult retrievedResult = peopleTable.Execute(retrieveOperation);

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

## <a name="next-steps"></a>次の手順
[!INCLUDE [vs-storage-dotnet-tables-next-steps](../../includes/vs-storage-dotnet-tables-next-steps.md)]

