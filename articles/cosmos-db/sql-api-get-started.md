---
title: .NET コンソール アプリをビルドして Azure Cosmos DB SQL API アカウントのデータを管理する
description: SQL API を使用してオンライン データベースと C# コンソール アプリを作成するチュートリアル。
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: dotnet
ms.topic: tutorial
ms.date: 04/15/2019
ms.author: sngun
ms.openlocfilehash: a8d144b2cb8ee18c69dc4c4768b09422d44bade2
ms.sourcegitcommit: fec96500757e55e7716892ddff9a187f61ae81f7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/16/2019
ms.locfileid: "59617322"
---
# <a name="build-a-net-console-app-to-manage-data-in-azure-cosmos-db-sql-api-account"></a>.NET コンソール アプリをビルドして Azure Cosmos DB SQL API アカウントのデータを管理する

> [!div class="op_single_selector"]
> * [.NET](sql-api-get-started.md)
> * [.NET (プレビュー)](sql-api-dotnet-get-started-preview.md)
> * [.NET Core](sql-api-dotnetcore-get-started.md)
> * [.NET Core (プレビュー)](sql-api-dotnet-core-get-started-preview.md)
> * [Java](sql-api-java-get-started.md)
> * [Async Java](sql-api-async-java-get-started.md)
> * [Node.js](sql-api-nodejs-get-started.md)
> 

Azure Cosmos DB SQL API を実際に使ってみるチュートリアルへようこそ。 このチュートリアルを完了すると、Azure Cosmos DB リソースを作成し、クエリを実行するコンソール アプリが完成します。

このチュートリアルでは、次の操作方法について説明します。

> [!div class="checklist"]
>
> - Azure Cosmos DB アカウントを作成して接続する
> - Visual Studio ソリューションを構成する
> - データベースを作成する
> - コレクションの作成
> - JSON ドキュメントの作成
> - コレクションのクエリを実行する
> - JSON ドキュメントを更新する
> - ドキュメントの削除
> - データベースを削除する

## <a name="prerequisites"></a>前提条件

Azure 開発ワークフローがインストールされている Visual Studio 2017:
- **無料**の [Visual Studio 2017 Community Edition](https://www.visualstudio.com/downloads/) をダウンロードして使用できます。 Visual Studio のセットアップ中に、必ず **[Azure の開発]** を有効にしてください。 

Azure サブスクリプションまたは Cosmos DB の無料試用版アカウント:
- [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)] 
  
- [!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]  
  
- Azure Cosmos DB Emulator を使用する場合は、[Azure Cosmos DB Emulator](local-emulator.md) に関する記事に記載されている手順に従ってエミュレーターを設定してください。 その後、「[Visual Studio ソリューションを設定する](#SetupVS)」からチュートリアルを始めます。
  
## <a name="get-the-completed-solution"></a>完成したソリューションを入手する

このチュートリアルを完了する時間がない場合や、コード サンプルが必要なだけの場合は、[GitHub](https://github.com/Azure-Samples/documentdb-dotnet-getting-started) から完全なソリューションをダウンロードできます。 

ダウンロードした完全なソリューションを実行するには: 

1. [前提条件](#prerequisites)がインストールされていることを確認します。 
1. ダウンロードした *GetStarted.sln* ソリューション ファイルを Visual Studio で開きます。
1. **ソリューション エクスプローラー**で **GetStarted** プロジェクトを右クリックし、**[NuGet パッケージの管理]** を選択します。
1. **[NuGet]** タブの **[復元]** を選択して、Azure Cosmos DB .NET SDK への参照を復元します。
1. 「[Azure Cosmos DB アカウントに接続する](#Connect)」セクションの説明に従って、*App.config* ファイルの `EndpointUrl` と `PrimaryKey` の値を更新します。
1. **[デバッグ]** > **[デバッグなしで開始]** を選択するか、**Ctrl**+**F5** キーを押して、アプリをビルドし、実行します。

## <a name="create-an-azure-cosmos-db-account"></a>Azure Cosmos DB アカウントを作成する

次の手順に従って、Azure portal 内で Azure Cosmos DB アカウントを作成します。 使用する Azure Cosmos DB アカウントが既にある場合は、「[Visual Studio ソリューションを設定する](#SetupVS)」に進んでください。 

[!INCLUDE [create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

## <a id="SetupVS"></a>Visual Studio ソリューションを設定する

1. Visual Studio 2017 で、**[ファイル]** > **[新規]** > **[プロジェクト]** の順に選択します。
   
1. **[新しいプロジェクト]** ダイアログで、**[Visual C#]** > **[コンソール アプリ (.NET Framework)]** の順に選択し、ご自分のプロジェクトに *AzureCosmosDBApp* という名前を付け、**[OK]** を選択します。
   
   ![[新しいプロジェクト] ウィンドウのスクリーンショット](./media/sql-api-get-started/nosql-tutorial-new-project-2.png)
   
1. **ソリューション エクスプローラー**で **AzureCosmosDBApp** プロジェクトを右クリックし、**[NuGet パッケージの管理]** を選択します。
   
   ![プロジェクト コンテキスト メニュー](./media/sql-api-get-started/nosql-tutorial-manage-nuget-pacakges.png)
   
1. **[NuGet]** タブの **[参照]** を選択し、検索ボックスに「*azure documentdb*」と入力します。
   
1. **Microsoft.Azure.DocumentDB** を検索して選択し、**[インストール]** を選択します (まだインストールしていない場合)。
   
   Azure Cosmos DB SQL API クライアント ライブラリのパッケージ ID は [Microsoft Azure Cosmos DB クライアント ライブラリ](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/) です。
   
   ![Azure Cosmos DB クライアント SDK を見つける NuGet メニューのスクリーンショット](./media/sql-api-get-started/nosql-tutorial-manage-nuget-pacakges-2.png)
   
   ソリューションの変更のプレビューに関するメッセージが表示されたら、**[OK]** を選択します。 ライセンスの同意に関するメッセージが表示されたら、**[同意する]** を選択します。

## <a id="Connect"></a>Azure Cosmos DB アカウントに接続する

次に、コード作成を開始します。 このチュートリアルの完全な *Project.cs* ファイルは、[GitHub](https://github.com/Azure-Samples/documentdb-dotnet-getting-started/blob/master/src/Program.cs) 上にあります。

1. **ソリューション エクスプローラー**で *Program.cs* を選択し、コード エディター内でファイルの先頭に次の参照を追加します。
   
   ```csharp
   using System.Net;
   using Microsoft.Azure.Documents;
   using Microsoft.Azure.Documents.Client;
   using Newtonsoft.Json;
   ```
   
1. 次に、以下の 2 つの定数と `client` 変数を `public class Program` に追加します。
   
   ```csharp
   
   public class Program
   {
      private const string EndpointUrl = "<your endpoint URL>";
      private const string PrimaryKey = "<your primary key>";
      private DocumentClient client;
   ```
   
1. エンドポイント URL とプライマリ キーにより、ご自分のアプリがご使用の Azure Cosmos DB アカウントに接続し、Azure Cosmos DB アカウントがその接続を信頼できます。 [Azure portal](https://portal.azure.com) からキーをコピーし、ご自分のコードに貼り付けます。 

   
   1. ご使用の Azure Cosmos DB アカウントの左側のナビゲーションにある **[キー]** を選択します。
      
      ![Azure portal 内でアクセス キーを表示およびコピーする](./media/sql-api-get-started/nosql-tutorial-keys.png)
      
   1. **[読み取り/書き込みキー]** の下にある **[URI]** の値を右側にあるコピー ボタンを使用してコピーし、*Program.cs* 内の `<your endpoint URL>` に貼り付けます。 例:  
      
      `private const string EndpointUrl = "https://mysqlapicosmosdb.documents.azure.com:443/";`
      
   1. **[プライマリ キー]** の値をコピーし、*Program.cs* 内の `<your primary key>` に貼り付けます。 例:  
      
      `private const string PrimaryKey = "19ZDNJAiYL26tmnRvoez6hmtIfBGwjun50PWRjNYMC2ig8Ob9hYk7Fq1RYSv8FcIYnh1TdBISvCh7s6yyb0000==";`
   
1. `Main` メソッドの後に、`GetStartedDemo` という新しい非同期タスクを追加します。このタスクにより、`client` という新しい `DocumentClient` がインスタンス化されます。
   
   ```csharp
      private async Task GetStartedDemo()
      {
        client = new DocumentClient(new Uri(EndpointUrl), PrimaryKey);
      }
   ```
   
1. `Main` メソッドに、`GetStartedDemo` タスクを実行する次のコードを追加します。 `Main` メソッドは例外をキャッチし、コンソールに書き込みます。
   
   ```csharp
      static void Main(string[] args)
      {
        try
        {
           Program p = new Program();
           p.GetStartedDemo().Wait();
        }
        catch (DocumentClientException de)
        {
           Exception baseException = de.GetBaseException();
           Console.WriteLine($"{de.StatusCode} error occurred: {de.Message}, Message: {baseException.Message}");
        }
        catch (Exception e)
        {
           Exception baseException = e.GetBaseException();
           Console.WriteLine($"Error: {e.Message}, Message: {baseException.Message}");
        }
        finally
        {
           Console.WriteLine("End of demo, press any key to exit.");
           Console.ReadKey();
        }
      }
   ```
   
1. **F5** キーを押して、ご自分のアプリを実行します。 
   
1. "**End of demo, press any key to exit**" というメッセージがコンソール ウィンドウに表示されたら、接続が成功したことを意味します。 任意のキーを押して、コンソール ウィンドウを閉じます。 

これで、ご使用の Azure Cosmos DB アカウントに正常に接続しました。 次は、いくつかの Azure Cosmos DB リソースを操作します。  

## <a name="create-a-database"></a>データベースを作成する

Azure Cosmos DB [データベース](databases-containers-items.md#azure-cosmos-databases)は、コレクションに分割された JSON ドキュメント ストレージの論理コンテナーです。 データベースは、`DocumentClient` クラスの [CreateDatabaseIfNotExistsAsync](/dotnet/api/microsoft.azure.documents.client.documentclient.createdatabaseifnotexistsasync) メソッドを使用して作成します。 

1. データベースを作成するコードを追加する前に、コンソールに書き込むためのヘルパー メソッドを追加します。 次の `WriteToConsoleAndPromptToContinue` メソッドをコピーし、ご自分のコード内の `GetStartedDemo` メソッドの後に貼り付けます。
   
   ```csharp
   private void WriteToConsoleAndPromptToContinue(string format, params object[] args)
   {
      Console.WriteLine(format, args);
      Console.WriteLine("Press any key to continue...");
      Console.ReadKey();
   }
   ```
   
1. 次の行をコピーし、ご自分の `GetStartedDemo` メソッドの `client = new DocumentClient(new Uri(EndpointUrl), PrimaryKey);` という行の後に貼り付けます。 このコードにより、`FamilyDB` というデータベースが作成されます。
   
   ```csharp
      await client.CreateDatabaseIfNotExistsAsync(new Database { Id = "FamilyDB" });
   ```
   
1. **F5** キーを押して、ご自分のアプリを実行します。

これで、Azure Cosmos DB データベースが正常に作成されました。 [Azure portal](https://portal.azure.com) 内でご使用の Azure Cosmos DB アカウントの左側のナビゲーションにある **[データ エクスプローラー]** を選択すると、データベースを確認できます。 

## <a id="CreateColl"></a>コレクションを作成する

コレクションには、JSON ドキュメントのほか、関連する JavaScript アプリケーション ロジックが格納されます。 コレクションは、`DocumentClient` クラスの [CreateDocumentCollectionIfNotExistsAsync](/dotnet/api/microsoft.azure.documents.client.documentclient.createdocumentcollectionifnotexistsasync#overloads) メソッドを使用して作成します。 

> [!IMPORTANT]
> **CreateDocumentCollectionIfNotExistsAsync** は、スループットが予約済みの新しいコレクションを作成します。これによって価格に影響があります。 詳細については、[価格のページ](https://azure.microsoft.com/pricing/details/cosmos-db/)を参照してください。
> 

1. 次のコードをコピーし、ご自分の `GetStartedDemo` メソッドの `await client.CreateDatabaseIfNotExistsAsync(new Database { Id = "FamilyDB" });` という行の後に貼り付けます。 このコードにより、`FamilyCollection` というドキュメント コレクションが作成されます。
   
   ```csharp
      await client.CreateDocumentCollectionIfNotExistsAsync(UriFactory.CreateDatabaseUri("FamilyDB"), new DocumentCollection { Id = "FamilyCollection" });
   ```
   
1. **F5** キーを押して、ご自分のアプリを実行します。

これで、Azure Cosmos DB ドキュメント コレクションが正常に作成されました。 Azure portal 内の**データ エクスプローラー**で、ご自分の **FamilyDB** データベースの下にコレクションを確認できます。  

## <a id="CreateDoc"></a>JSON ドキュメントを作成する

ドキュメントは、ユーザー定義の任意の JSON コンテンツです。 ドキュメントには、JSON の `id` としてシリアル化される ID プロパティが必要です。 ドキュメントは、`DocumentClient` クラスの [CreateDocumentAsync](/dotnet/api/microsoft.azure.documents.client.documentclient.createdocumentasync#overloads) メソッドを使用して作成します。 

> [!TIP]
> ご自分のデータベースに保存するデータが既にある場合には、Azure Cosmos DB の[データ移行ツール](import-data.md)を使用して、そのデータをインポートできます。
>

次のコードにより、2 つのドキュメントが作成され、ご自分のデータベース コレクションに挿入されます。 最初に、`Family` クラスと、`Family` 内で使用する `Parent`、`Child`、`Pet`、および `Address` サブクラスを作成します。 次に、`CreateFamilyDocumentIfNotExists` メソッドを作成して、2 つのドキュメントを作成して挿入します。 

1. 次の `Family`、`Parent`、`Child`、`Pet`、および `Address` クラスをコピーし、ご自分のコード内の `WriteToConsoleAndPromptToContinue` メソッドの後に貼り付けます。
   
   ```csharp
    public class Family
    {
        [JsonProperty(PropertyName = "id")]
        public string Id { get; set; }
        public string LastName { get; set; }
        public Parent[] Parents { get; set; }
        public Child[] Children { get; set; }
        public Address Address { get; set; }
        public bool IsRegistered { get; set; }
        public override string ToString()
        {
            return JsonConvert.SerializeObject(this);
        }
    }

    public class Parent
    {
        public string FamilyName { get; set; }
        public string FirstName { get; set; }
    }

    public class Child
    {
        public string FamilyName { get; set; }
        public string FirstName { get; set; }
        public string Gender { get; set; }
        public int Grade { get; set; }
        public Pet[] Pets { get; set; }
    }

    public class Pet
    {
        public string GivenName { get; set; }
    }

    public class Address
    {
        public string State { get; set; }
        public string County { get; set; }
        public string City { get; set; }
    }
   ```
   
1. 次の `CreateFamilyDocumentIfNotExists` メソッドをコピーし、先ほど追加した `Address` クラスの後に貼り付けます。
   
   ```csharp
    private async Task CreateFamilyDocumentIfNotExists(string databaseName, string collectionName, Family family)
    {
        try
        {
            await client.ReadDocumentAsync(UriFactory.CreateDocumentUri(databaseName, collectionName, family.Id));
            WriteToConsoleAndPromptToContinue($"Found {family.Id}");
        }
        catch (DocumentClientException de)
        {
            if (de.StatusCode == HttpStatusCode.NotFound)
            {
                await client.CreateDocumentAsync(UriFactory.CreateDocumentCollectionUri(databaseName, collectionName), family);
                WriteToConsoleAndPromptToContinue($"Created Family {family.Id}");
            }
            else
            {
                throw;
            }
        }
    }
   ```
   
1. 次のコードをコピーし、ご自分の `GetStartedDemo` メソッドの末尾にある `await client.CreateDocumentCollectionIfNotExistsAsync` という行の後に貼り付けます。 このコードにより、2 つのドキュメント (Andersen 一家と Wakefield 一家について 1 つずつ) が作成され、挿入されます。
   
   ```csharp
    Family andersenFamily = new Family
    {
        Id = "AndersenFamily",
        LastName = "Andersen",
        Parents = new Parent[]
        {
            new Parent { FirstName = "Thomas" },
            new Parent { FirstName = "Mary Kay" }
        },
        Children = new Child[]
        {
            new Child
            {
                FirstName = "Henriette Thaulow",
                Gender = "female",
                Grade = 5,
                Pets = new Pet[]
                {
                    new Pet { GivenName = "Fluffy" }
                }
            }
        },
        Address = new Address { State = "WA", County = "King", City = "Seattle" },
        IsRegistered = true
    };

    await CreateFamilyDocumentIfNotExists("FamilyDB", "FamilyCollection", andersenFamily);

    Family wakefieldFamily = new Family
    {
        Id = "WakefieldFamily",
        LastName = "Wakefield",
        Parents = new Parent[]
        {
            new Parent { FamilyName = "Wakefield", FirstName = "Robin" },
            new Parent { FamilyName = "Miller", FirstName = "Ben" }
        },
        Children = new Child[]
        {
            new Child
            {
                FamilyName = "Merriam",
                FirstName = "Jesse",
                Gender = "female",
                Grade = 8,
                Pets = new Pet[]
                {
                    new Pet { GivenName = "Goofy" },
                    new Pet { GivenName = "Shadow" }
                }
            },
            new Child
            {
                FamilyName = "Miller",
                FirstName = "Lisa",
                Gender = "female",
                Grade = 1
            }
        },
        Address = new Address { State = "NY", County = "Manhattan", City = "NY" },
        IsRegistered = false
    };

    await CreateFamilyDocumentIfNotExists("FamilyDB", "FamilyCollection", wakefieldFamily);
   ```
   
1. **F5** キーを押して、ご自分のアプリを実行します。

これで、Azure Cosmos DB ドキュメントが 2 つ正常に作成されました。 Azure portal 内の**データ エクスプローラー**で、ご自分の **FamilyDB** データベースと **FamilyCollection** コレクションの下にドキュメントを確認できます。   

![アカウント、オンライン データベース、コレクション、およびドキュメントの間の階層関係を示す図](./media/sql-api-get-started/nosql-tutorial-account-database.png)

## <a id="Query"></a>Azure Cosmos DB リソースにクエリを実行する

Azure Cosmos DB では、コレクションに格納された JSON ドキュメントに対する豊富な[クエリ](how-to-sql-query.md)がサポートされています。 次のサンプル コードでは、LINQ と Azure Cosmos DB SQL 構文を使用して、サンプル ドキュメントに対してクエリを実行します。

1. 次の `ExecuteSimpleQuery` メソッドをコピーし、ご自分のコード内の `CreateFamilyDocumentIfNotExists` メソッドの後に貼り付けます。
   
   ```csharp
    private void ExecuteSimpleQuery(string databaseName, string collectionName)
    {
        // Set some common query options.
        FeedOptions queryOptions = new FeedOptions { MaxItemCount = -1 };

        // Find the Andersen family by its LastName.
        IQueryable<Family> familyQuery = client.CreateDocumentQuery<Family>(
            UriFactory.CreateDocumentCollectionUri(databaseName, collectionName), queryOptions)
            .Where(f => f.LastName == "Andersen");

        // Execute the query synchronously. 
        // You could also execute it asynchronously using the IDocumentQuery<T> interface.
        Console.WriteLine("Running LINQ query...");
        foreach (Family family in familyQuery)
        {
            Console.WriteLine($"\tRead {family}");
        }

        // Now execute the same query using direct SQL.
        IQueryable<Family> familyQueryInSql = client.CreateDocumentQuery<Family>(
            UriFactory.CreateDocumentCollectionUri(databaseName, collectionName),
            "SELECT * FROM Family WHERE Family.LastName = 'Andersen'",
            queryOptions);

        Console.WriteLine("Running direct SQL query...");
        foreach (Family family in familyQueryInSql)
        {
            Console.WriteLine($"\tRead {family}");
        }

        Console.WriteLine("Press any key to continue ...");
        Console.ReadKey();
    }
   ```
   
1. 次のコードをコピーし、ご自分の `GetStartedDemo` メソッドの末尾にある `await CreateFamilyDocumentIfNotExists("FamilyDB", "FamilyCollection", wakefieldFamily);` という行の後に貼り付けます。
   
   ```csharp
      ExecuteSimpleQuery("FamilyDB", "FamilyCollection");
   ```
   
1. **F5** キーを押して、ご自分のアプリを実行します。

上記のクエリでは、Andersen 一家の完全な項目が返されます。 これで、Azure Cosmos DB コレクションに対するクエリが正常に実行されました。

次の図は、コレクションに対して Azure Cosmos DB SQL クエリ構文がどのように呼び出されるかを示しています。 同じロジックが LINQ クエリに適用されます。

![C# コンソール アプリケーションを作成するために NoSQL チュートリアルで使用されるクエリの範囲と意味を示す図](./media/sql-api-get-started/nosql-tutorial-collection-documents.png)

Azure Cosmos DB クエリのスコープは既に 1 つのコレクションに設定されているので、SQL クエリでは [FROM](how-to-sql-query.md#FromClause) キーワードを省略できます。 `FROM Families f` を `FROM root r` や、任意の他の変数名に交換できます。 Azure Cosmos DB では、`Families`、`root`、または任意の変数名が現在のコレクションを参照しているものと推測されます。

## <a id="ReplaceDocument"></a>JSON ドキュメントを更新する

Azure Cosmos DB SQL API では、JSON ドキュメントの更新と置換がサポートされています。  

1. 次の `ReplaceFamilyDocument` メソッドをコピーし、ご自分のコード内の `ExecuteSimpleQuery` メソッドの後に貼り付けます。
   
   ```csharp
    private async Task ReplaceFamilyDocument(string databaseName, string collectionName, string familyName, Family updatedFamily)
    {
       await client.ReplaceDocumentAsync(UriFactory.CreateDocumentUri(databaseName, collectionName, familyName), updatedFamily);
       WriteToConsoleAndPromptToContinue($"Replaced Family {familyName}");
    }
   ```
   
1. 次のコードをコピーし、ご自分の `GetStartedDemo` メソッドの末尾にある `ExecuteSimpleQuery("FamilyDB", "FamilyCollection");` という行の後に貼り付けます。 このコードにより、1 つのドキュメント内のデータが更新され、もう一度クエリが実行されて、変更されたドキュメントが表示されます。
   
   ```csharp
   // Update the Grade of the Andersen Family child
   andersenFamily.Children[0].Grade = 6;
   await ReplaceFamilyDocument("FamilyDB", "FamilyCollection", "AndersenFamily", andersenFamily);
   ExecuteSimpleQuery("FamilyDB", "FamilyCollection");
   ```
   
1. **F5** キーを押して、ご自分のアプリを実行します。

クエリの出力には、Andersen 一家の子の `Grade` が `5` から `6` に更新されたことが示されます。 これで、Azure Cosmos DB ドキュメントが正常に更新され、置換されました。 

## <a id="DeleteDocument"></a>JSON ドキュメントを削除する

Azure Cosmos DB SQL API では、JSON ドキュメントの削除がサポートされています。  

1. 次の `DeleteFamilyDocument` メソッドをコピーし、`ReplaceFamilyDocument` メソッドの後に貼り付けます。
   
   ```csharp
    private async Task DeleteFamilyDocument(string databaseName, string collectionName, string documentName)
    {
        await client.DeleteDocumentAsync(UriFactory.CreateDocumentUri(databaseName, collectionName, documentName));
        Console.WriteLine($"Deleted Family {documentName}");
    }
   ```
   
1. 次のコードをコピーし、ご自分の `GetStartedDemo` メソッドの末尾にある 2 つ目の `ExecuteSimpleQuery("FamilyDB", "FamilyCollection");` という行の後に貼り付けます。
   
   ```csharp
   await DeleteFamilyDocument("FamilyDB", "FamilyCollection", "AndersenFamily");
   ```
   
1. **F5** キーを押して、ご自分のアプリを実行します。

これで、Azure Cosmos DB ドキュメントが正常に削除されました。 

## <a id="DeleteDatabase"></a>データベースを削除する

作成したデータベースを削除して、データベースとそのすべての子リソース (コレクションとドキュメントを含む) を削除します。 

1. 次のコードをコピーし、ご自分の `GetStartedDemo` メソッドの末尾にある `await DeleteFamilyDocument("FamilyDB", "FamilyCollection", "AndersenFamily");` という行の後に貼り付けます。 
   
   ```csharp
   // Clean up - delete the database
   await client.DeleteDatabaseAsync(UriFactory.CreateDatabaseUri("FamilyDB"));
   ```
   
1. **F5** キーを押して、ご自分のアプリを実行します。

これで、Azure Cosmos DB データベースが正常に削除されました。 ご使用の Azure Cosmos DB アカウントの**データ エクスプローラー**で FamilyDB データベースが削除されたことを確認できます。 

## <a id="Run"></a>C# コンソール アプリ全体を実行する

Visual Studio で **F5** キーを押して、完成した C# コンソール アプリをビルドし、デバッグ モードで実行します。 コンソール ウィンドウに次の出力が表示されます。

```bash
Created Family AndersenFamily
Press any key to continue ...
 Created Family WakefieldFamily
Press any key to continue ...
 Running LINQ query...
        Read {"id":"AndersenFamily","LastName":"Andersen","Parents":[{"FamilyName":null,"FirstName":"Thomas"},{"FamilyName":null,"FirstName":"Mary Kay"}],"Children":[{"FamilyName":null,"FirstName":"Henriette Thaulow","Gender":"female","Grade":5,"Pets":[{"GivenName":"Fluffy"}]}],"Address":{"State":"WA","County":"King","City":"Seattle"},"IsRegistered":true}
Running direct SQL query...
        Read {"id":"AndersenFamily","LastName":"Andersen","Parents":[{"FamilyName":null,"FirstName":"Thomas"},{"FamilyName":null,"FirstName":"Mary Kay"}],"Children":[{"FamilyName":null,"FirstName":"Henriette Thaulow","Gender":"female","Grade":5,"Pets":[{"GivenName":"Fluffy"}]}],"Address":{"State":"WA","County":"King","City":"Seattle"},"IsRegistered":true}
Press any key to continue ...
 Replaced Family AndersenFamily
Press any key to continue ...
 Running LINQ query...
        Read {"id":"AndersenFamily","LastName":"Andersen","Parents":[{"FamilyName":null,"FirstName":"Thomas"},{"FamilyName":null,"FirstName":"Mary Kay"}],"Children":[{"FamilyName":null,"FirstName":"Henriette Thaulow","Gender":"female","Grade":6,"Pets":[{"GivenName":"Fluffy"}]}],"Address":{"State":"WA","County":"King","City":"Seattle"},"IsRegistered":true}
Running direct SQL query...
        Read {"id":"AndersenFamily","LastName":"Andersen","Parents":[{"FamilyName":null,"FirstName":"Thomas"},{"FamilyName":null,"FirstName":"Mary Kay"}],"Children":[{"FamilyName":null,"FirstName":"Henriette Thaulow","Gender":"female","Grade":6,"Pets":[{"GivenName":"Fluffy"}]}],"Address":{"State":"WA","County":"King","City":"Seattle"},"IsRegistered":true}
Press any key to continue ...
 Deleted Family AndersenFamily
End of demo, press any key to exit.
```

お疲れさまでした。 このチュートリアルが完了し、Azure Cosmos DB リソースの作成、クエリ、更新、削除を実行する、実際に動作する C# コンソール アプリが完成しました。  

## <a name="next-steps"></a>次の手順
* Azure Cosmos DB の詳細については、「[Azure Cosmos DB の概要](introduction.md)」を参照してください。
* より複雑な ASP.NET MVC チュートリアルについては、[ASP.NET MVC のチュートリアル:Azure Cosmos DB を使用した Web アプリケーションの開発](sql-api-dotnet-application.md)に関するページを参照してください。
* Azure Cosmos DB に関するスケールとパフォーマンスのテストを実行するには、「[Azure Cosmos DB のパフォーマンスとスケールのテスト](performance-testing.md)」を参照してください。
* Azure Cosmos DB の要求、使用状況、およびストレージを監視する方法については、[アカウントの監視](monitor-accounts.md)に関するページを参照してください。
* [クエリのプレイ グラウンド](https://www.documentdb.com/sql/demo)でサンプル データセットに対してクエリを実行します。

