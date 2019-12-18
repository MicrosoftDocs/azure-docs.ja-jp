---
title: .NET Standard SDK を使用した Azure Cosmos DB Table API の概要
description: Azure Cosmos DB Table API アカウントに構造化データを格納して照会する方法について説明します
author: wmengmsft
ms.author: wmeng
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.devlang: dotnet
ms.topic: sample
ms.date: 12/03/2019
ms.openlocfilehash: 0a1ba00d2d24664590f76438a90f651e0826d419
ms.sourcegitcommit: 9405aad7e39efbd8fef6d0a3c8988c6bf8de94eb
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/05/2019
ms.locfileid: "74870549"
---
# <a name="get-started-with-azure-cosmos-db-table-api-and-azure-table-storage-using-the-net-sdk"></a>.NET SDK を使用した Azure Cosmos DB Table API と Azure Table Storage の概要

[!INCLUDE [storage-selector-table-include](../../includes/storage-selector-table-include.md)]

[!INCLUDE [storage-table-applies-to-storagetable-and-cosmos](../../includes/storage-table-applies-to-storagetable-and-cosmos.md)]

Azure Cosmos DB Table API または Azure Table Storage を使用すると、NoSQL の構造化データをクラウドに格納し、スキーマレスのデザインでキー/属性ストアを実現できます。 Azure Cosmos DB Table API と Table Storage にはスキーマがないため、アプリケーションの進化のニーズに合わせてデータを容易に適応させることができます。 Azure Cosmos DB Table API または Table Storage を使用すると、Web アプリケーションのユーザー データ、アドレス帳、デバイス情報、サービスに必要なその他の種類のメタデータなど、柔軟なデータセットを格納できます。 

このチュートリアルでは、Azure Cosmos DB Table API と Azure Table Storage のシナリオで [.NET 用 Microsoft Azure Cosmos DB Table ライブラリ](https://www.nuget.org/packages/Microsoft.Azure.Cosmos.Table)を使用する方法を紹介したサンプルについて説明します。 Azure サービス専用の接続を使用する必要があります。 テーブルの作成、データの挿入/更新、データの照会、テーブルの削除の方法を示した C# の例を使用して、これらのシナリオを考察しています。

## <a name="prerequisites"></a>前提条件

このサンプルの作業を行うためには、次のものが必要になります。

* [Microsoft Visual Studio](https://www.visualstudio.com/downloads/)

* [.NET 用 Microsoft Azure CosmosDB Table ライブラリ](https://www.nuget.org/packages/Microsoft.Azure.Cosmos.Table) - このライブラリは現在、.NET Standard と .NET Framework で利用できます。 

* [Azure Cosmos DB Table API アカウント](create-table-dotnet.md#create-a-database-account)。

## <a name="create-an-azure-cosmos-db-table-api-account"></a>Azure Cosmos DB Table API アカウントを作成する

[!INCLUDE [cosmos-db-create-dbaccount-table](../../includes/cosmos-db-create-dbaccount-table.md)]

## <a name="create-a-net-console-project"></a>.NET コンソール プロジェクトを作成する

Visual Studio で、新しい .NET コンソール アプリケーションを作成します。 次の手順では、Visual Studio 2019 でコンソール アプリケーションを作成する方法を説明します。 Azure クラウド サービスまたは Web アプリ、デスクトップ アプリケーション、モバイル アプリケーションなど、どの種類の .NET アプリケーションでも Azure Cosmos DB Table ライブラリを使用できます。 このガイドでは、わかりやすくするためにコンソール アプリケーションを使用します。

1. **[ファイル]**  >  **[新規作成]**  >  **[プロジェクト]** の順に選択します。

1. **[コンソール アプリ (.NET Core)]** を選択し、 **[次へ]** を選択します。

1. **[プロジェクト名]** フィールドに、**CosmosTableSamples** のようなアプリケーションの名前を入力します。 必要に応じて、別の名前を指定できます。

1. **作成** を選択します。

このサンプルのすべてのコード例は、コンソール アプリケーションの **Program.cs** ファイルの Main() メソッドに追加できます。

## <a name="install-the-required-nuget-package"></a>必須 NuGet パッケージをインストールする

NuGet パッケージを取得するには、次の手順に従います。

1. **ソリューション エクスプローラー**でプロジェクトを右クリックし、 **[NuGet パッケージの管理]** をクリックします。

1. `Microsoft.Azure.Cosmos.Table`、`Microsoft.Extensions.Configuration`、`Microsoft.Extensions.Configuration.Json`、`Microsoft.Extensions.Configuration.Binder` をオンラインで検索し、 **[インストール]** を選択して Microsoft Azure Cosmos DB Table ライブラリをインストールします。

## <a name="configure-your-storage-connection-string"></a>ストレージ接続文字列の構成

1. [Azure portal](https://portal.azure.com/) で、Azure Cosmos アカウントまたは Table Storage アカウントに移動します。 

1. **[接続文字列]** または **[アクセス キー]** ウィンドウを開きます。 ウィンドウの右側にある [コピー] ボタンを使って**プライマリ接続文字列**をコピーします。

   ![[接続文字列] ウィンドウでプライマリ接続文字列を確認してコピーする](./media/create-table-dotnet/connection-string.png)
   
1. 接続文字列を構成するには、Visual Studio から対象のプロジェクト **[CosmosTableSamples]** を右クリックします。

1. **[追加]** を選択し、 **[新しい項目]** を選択します。 ファイルの種類を **[TypeScript JSON 構成ファイル]** として新しいファイル **Settings.json** を作成します。 

1. Settings.json ファイル内のコードを次のコードに置き換え、実際のプライマリ接続文字列を割り当てます。

   ```csharp
   {
   "StorageConnectionString": <Primary connection string of your Azure Cosmos DB account>
   }
   ```

1. 対象のプロジェクトである **[CosmosTableSamples]** を右クリックします。 **[追加]** 、 **[新しい項目]** の順に選択し、**AppSettings.cs** という名前のクラスを追加します。

1. AppSettings.cs ファイルに次のコードを追加します。 このファイルは Settings.json ファイルから接続文字列を読み取り、それを構成パラメーターに割り当てます。

   ```csharp
   namespace CosmosTableSamples
   {
    using Microsoft.Extensions.Configuration;
    public class AppSettings
    {
        public string StorageConnectionString { get; set; }
        public static AppSettings LoadAppSettings()
        {
            IConfigurationRoot configRoot = new ConfigurationBuilder()
                .AddJsonFile("Settings.json")
                .Build();
            AppSettings appSettings = configRoot.Get<AppSettings>();
            return appSettings;
        }
    }
   }
   ```

## <a name="parse-and-validate-the-connection-details"></a>接続の詳細を解析して検証する 

1. 対象のプロジェクトである **[CosmosTableSamples]** を右クリックします。 **[追加]** 、 **[新しい項目]** の順に選択し、 **[Common.cs]** という名前のクラスを追加します。 このクラス内に、接続の詳細を検証してテーブルを作成するコードを記述します。

1. 以下のように、`CreateStorageAccountFromConnectionString` というメソッドを定義します。 このメソッドは、接続文字列の詳細を解析し、"Settings.json" ファイルに入力されているアカウント名とアカウント キーの詳細が有効であることを確認するものです。 

 ```csharp
using System;

namespace CosmosTableSamples
{
    using System.Threading.Tasks;
    using Microsoft.Azure.Cosmos.Table;
    using Microsoft.Azure.Documents;

    public class Common
    {
        public static CloudStorageAccount CreateStorageAccountFromConnectionString(string storageConnectionString)
        {
            CloudStorageAccount storageAccount;
            try
            {
                storageAccount = CloudStorageAccount.Parse(storageConnectionString);
            }
            catch (FormatException)
            {
                Console.WriteLine("Invalid storage account information provided. Please confirm the AccountName and AccountKey are valid in the app.config file - then restart the application.");
                throw;
            }
            catch (ArgumentException)
            {
                Console.WriteLine("Invalid storage account information provided. Please confirm the AccountName and AccountKey are valid in the app.config file - then restart the sample.");
                Console.ReadLine();
                throw;
            }

            return storageAccount;
        }
    }
}
   ```


## <a name="create-a-table"></a>テーブルを作成する 

[CloudTableClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.table.cloudtableclient) クラスを使用すると、Table Storage に格納されているテーブルとエンティティを取得できます。 Cosmos DB Table API アカウントにはテーブルが 1 つもないので、テーブルを作成するための `CreateTableAsync` メソッドを **Common.cs** クラスに追加しましょう。

```csharp
public static async Task<CloudTable> CreateTableAsync(string tableName)
  {
    string storageConnectionString = AppSettings.LoadAppSettings().StorageConnectionString;

    // Retrieve storage account information from connection string.
    CloudStorageAccount storageAccount = CreateStorageAccountFromConnectionString(storageConnectionString);

    // Create a table client for interacting with the table service
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient(new TableClientConfiguration());

    Console.WriteLine("Create a Table for the demo");

    // Create a table client for interacting with the table service 
    CloudTable table = tableClient.GetTableReference(tableName);
    if (await table.CreateIfNotExistsAsync())
    {
      Console.WriteLine("Created Table named: {0}", tableName);
    }
    else
    {
      Console.WriteLine("Table {0} already exists", tableName);
    }

    Console.WriteLine();
    return table;
}
```

## <a name="define-the-entity"></a>エンティティを定義する 

エンティティは、[TableEntity](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.table.tableentity) から派生するカスタム クラスを使用して C# オブジェクトにマップされます。 エンティティをテーブルに追加するには、エンティティのプロパティを定義するクラスを作成します。

対象のプロジェクトである **[CosmosTableSamples]** を右クリックします。 **[追加]** 、 **[新しいフォルダー]** の順に選択し、**Model** という名前を付けます。 Model フォルダーに **CustomerEntity.cs** という名前のクラスを追加し、そこに次のコードを追加します。

```csharp
namespace CosmosTableSamples.Model
{
    using Microsoft.Azure.Cosmos.Table;
    public class CustomerEntity : TableEntity
    {
        public CustomerEntity()
        {
        }

        public CustomerEntity(string lastName, string firstName)
        {
            PartitionKey = lastName;
            RowKey = firstName;
        }

        public string Email { get; set; }
        public string PhoneNumber { get; set; }
    }
}
```

このコードは、顧客の名を行キーとして、姓をパーティション キーとしてそれぞれ使用するエンティティ クラスを定義します。 エンティティのパーティション キーと行キーの組み合わせで、テーブル内のエンティティを一意に識別します。 同じパーティション キーを持つエンティティは、異なるパーティション キーを持つエンティティよりも迅速に照会できます。一方、多様なパーティション キーを使用すると、並列操作のスケーラビリティが向上します。 テーブルに格納するエンティティは、[TableEntity](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.table.tableentity) クラスから派生した型など、サポートされている型である必要があります。 テーブルに格納するエンティティのプロパティは、その型のパブリック プロパティであること、また、値の取得と設定の両方に対応していることが必要です。 また、エンティティ型で、パラメーターのないコンストラクターを必ず公開する必要があります。

## <a name="insert-or-merge-an-entity"></a>エンティティを挿入またはマージする

次のコード例では、エンティティ オブジェクトを作成して、それをテーブルに追加します。 エンティティの挿入とマージには、[TableOperation](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.table.tableoperation) クラス内の InsertOrMerge メソッドが使用されます。 その操作は、[CloudTable.ExecuteAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.table.cloudtable.executeasync?view=azure-dotnet) メソッドを呼び出すことによって実行されます。 

対象のプロジェクトである **[CosmosTableSamples]** を右クリックします。 **[追加]** 、 **[新しい項目]** の順に選択し、 **[SamplesUtils.cs]** という名前のクラスを追加します。 エンティティに対する CRUD 操作を実行するために必要なすべてのコードが、このクラスに格納されます。 

```csharp
 public static async Task<CustomerEntity> InsertOrMergeEntityAsync(CloudTable table, CustomerEntity entity)
 {
     if (entity == null)
     {
         throw new ArgumentNullException("entity");
     }
     try
     {
         // Create the InsertOrReplace table operation
         TableOperation insertOrMergeOperation = TableOperation.InsertOrMerge(entity);

         // Execute the operation.
         TableResult result = await table.ExecuteAsync(insertOrMergeOperation);
         CustomerEntity insertedCustomer = result.Result as CustomerEntity;

         // Get the request units consumed by the current operation. RequestCharge of a TableResult is only applied to Azure Cosmos DB
         if (result.RequestCharge.HasValue)
         {
             Console.WriteLine("Request Charge of InsertOrMerge Operation: " + result.RequestCharge);
         }

         return insertedCustomer;
     }
     catch (StorageException e)
     {
         Console.WriteLine(e.Message);
         Console.ReadLine();
         throw;
     }
 }
```

### <a name="get-an-entity-from-a-partition"></a>パーティションからエンティティを取得する

[TableOperation](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.table.tableoperation) クラスの Retrieve メソッドを使用してパーティションからエンティティを取得できます。 次のコード例では、顧客エンティティのパーティション キー、行キー、メール、電話番号を取得しています。 また、この例では、エンティティのクエリで消費される要求ユニットも出力されます。 エンティティを照会するには、**SamplesUtils.cs** ファイルに次のコードを追加します。 

```csharp
public static async Task<CustomerEntity> RetrieveEntityUsingPointQueryAsync(CloudTable table, string partitionKey, string rowKey)
    {
      try
      {
        TableOperation retrieveOperation = TableOperation.Retrieve<CustomerEntity>(partitionKey, rowKey);
        TableResult result = await table.ExecuteAsync(retrieveOperation);
        CustomerEntity customer = result.Result as CustomerEntity;
        if (customer != null)
        {
          Console.WriteLine("\t{0}\t{1}\t{2}\t{3}", customer.PartitionKey, customer.RowKey, customer.Email, customer.PhoneNumber);
        }

        // Get the request units consumed by the current operation. RequestCharge of a TableResult is only applied to Azure CosmoS DB 
        if (result.RequestCharge.HasValue)
        {
           Console.WriteLine("Request Charge of Retrieve Operation: " + result.RequestCharge);
        }

        return customer;
        }
        catch (StorageException e)
        {
           Console.WriteLine(e.Message);
           Console.ReadLine();
           throw;
        }
    }
```

## <a name="delete-an-entity"></a>エンティティを削除する

エンティティは、取得後に簡単に削除できます。エンティティの更新のときと同じパターンを使用します。 次のコードは、ユーザー エンティティを取得して削除します。 エンティティを削除するには、**SamplesUtils.cs** ファイルに次のコードを追加します。 

```csharp
public static async Task DeleteEntityAsync(CloudTable table, CustomerEntity deleteEntity)
   {
     try
     {
        if (deleteEntity == null)
     {
        throw new ArgumentNullException("deleteEntity");
     }

    TableOperation deleteOperation = TableOperation.Delete(deleteEntity);
    TableResult result = await table.ExecuteAsync(deleteOperation);

    // Get the request units consumed by the current operation. RequestCharge of a TableResult is only applied to Azure CosmoS DB 
    if (result.RequestCharge.HasValue)
    {
       Console.WriteLine("Request Charge of Delete Operation: " + result.RequestCharge);
    }

    }
    catch (StorageException e)
    {
        Console.WriteLine(e.Message);
        Console.ReadLine();
        throw;
    }
}
```

## <a name="execute-the-crud-operations-on-sample-data"></a>サンプル データに対して CRUD 操作を実行する

テーブルの作成、エンティティの挿入、エンティティのマージを行うための各メソッドを定義したら、それらのメソッドをサンプル データに対して実行します。 そのためには、対象のプロジェクトである **[CosmosTableSamples]** を右クリックします。 **[追加]** 、 **[新しい項目]** の順に選択し、**BasicSamples.cs** という名前のクラスを追加して、そこに以下のコードを追加してください。 このコードによってテーブルが作成されてエンティティが追加されます。 プロジェクトの最後にエンティティとテーブルを削除したい場合は、次のコードの `table.DeleteIfExistsAsync()` メソッドと `SamplesUtils.DeleteEntityAsync(table, customer)` メソッドのコメントを解除してください。

```csharp
using System;
namespace CosmosTableSamples
{
    using System.Threading.Tasks;
    using Microsoft.Azure.Cosmos.Table;
    using Model;

    class BasicSamples
    {
        public async Task RunSamples()
        {
            Console.WriteLine("Azure Cosmos DB Table - Basic Samples\n");
            Console.WriteLine();

            string tableName = "demo" + Guid.NewGuid().ToString().Substring(0, 5);

            // Create or reference an existing table
            CloudTable table = await Common.CreateTableAsync(tableName);

            try
            {
                // Demonstrate basic CRUD functionality 
                await BasicDataOperationsAsync(table);
            }
            finally
            {
                // Delete the table
                // await table.DeleteIfExistsAsync();
            }
        }

        private static async Task BasicDataOperationsAsync(CloudTable table)
        {
            // Create an instance of a customer entity. See the Model\CustomerEntity.cs for a description of the entity.
            CustomerEntity customer = new CustomerEntity("Harp", "Walter")
            {
                Email = "Walter@contoso.com",
                PhoneNumber = "425-555-0101"
            };

            // Demonstrate how to insert the entity
            Console.WriteLine("Insert an Entity.");
            customer = await SamplesUtils.InsertOrMergeEntityAsync(table, customer);

            // Demonstrate how to Update the entity by changing the phone number
            Console.WriteLine("Update an existing Entity using the InsertOrMerge Upsert Operation.");
            customer.PhoneNumber = "425-555-0105";
            await SamplesUtils.InsertOrMergeEntityAsync(table, customer);
            Console.WriteLine();

            // Demonstrate how to Read the updated entity using a point query 
            Console.WriteLine("Reading the updated Entity.");
            customer = await SamplesUtils.RetrieveEntityUsingPointQueryAsync(table, "Harp", "Walter");
            Console.WriteLine();

            // Demonstrate how to Delete an entity
            //Console.WriteLine("Delete the entity. ");
            //await SamplesUtils.DeleteEntityAsync(table, customer);
            //Console.WriteLine();
        }
    }
}
```

前出のコードでは、"demo" で始まるテーブルを作成し、生成された GUID をそのテーブル名に追加しています。 その後、名と姓が "Harp Walter" である顧客エンティティを追加した後、このユーザーの電話番号を更新します。 

このチュートリアルでは、Table API アカウントに格納されたデータに対して基本的な CRUD 操作を実行するコードを作成しました。 それ以外にも、データのバッチ挿入、特定のパーティション内の全データの照会、特定のパーティション内の一連のデータの照会、名前が特定のプレフィックスで始まるアカウント内テーブルのリストなど、高度な操作を実行することもできます。 その完全なサンプルは、GitHub リポジトリ [azure-cosmos-table-dotnet-core-getting-started](https://github.com/Azure-Samples/azure-cosmos-table-dotnet-core-getting-started) からダウンロードできます。 データに対して実行できるさまざまな操作が、[AdvancedSamples.cs](https://github.com/Azure-Samples/azure-cosmos-table-dotnet-core-getting-started/blob/master/CosmosTableSamples/AdvancedSamples.cs) クラスに含まれています。  

## <a name="run-the-project"></a>プロジェクトを実行する

対象のプロジェクトである **[CosmosTableSamples]** を選択します。 **Program.cs** という名前のクラスを開き、プロジェクトの実行時に BasicSamples を呼び出すために、次のコードを追加します。

```csharp
using System;

namespace CosmosTableSamples
{
    class Program
    {
        public static void Main(string[] args)
        {
            Console.WriteLine("Azure Cosmos Table Samples");
            BasicSamples basicSamples = new BasicSamples();
            basicSamples.RunSamples().Wait();
           
            Console.WriteLine();
            Console.WriteLine("Press any key to exit");
            Console.Read();
        }
    }
}
```

ここでソリューションをビルドし、F5 キーを押してプロジェクトを実行します。 プロジェクトを実行すると、コマンド プロンプトに次の出力内容が表示されます。

![コマンド プロンプトからの出力](./media/tutorial-develop-table-standard/output-from-sample.png)

プロジェクトを実行するときに Settings.json ファイルが見つからないというエラーが発生した場合は、次の XML エントリをプロジェクトの設定に追加することで解決できます。 CosmosTableSamples を右クリックし、[CosmosTableSamples.csproj の編集] を選択して、次の itemGroup を追加します。 

```csharp
  <ItemGroup>
    <None Update="Settings.json">
      <CopyToOutputDirectory>PreserveNewest</CopyToOutputDirectory>
    </None>
  </ItemGroup>
```
これで Azure portal にサインインして、テーブルにデータが存在することを確認できます。 

![ポータルでの結果](./media/tutorial-develop-table-standard/results-in-portal.png)

## <a name="next-steps"></a>次の手順

次のチュートリアルに進んで、Azure Cosmos DB Table API アカウントにデータを移行する方法を学びましょう。 

> [!div class="nextstepaction"]
>[データを照会する方法](../cosmos-db/table-import.md)
