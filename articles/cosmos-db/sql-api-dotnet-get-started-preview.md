---
title: .NET コンソール アプリケーションをビルドして Azure Cosmos DB SQL API アカウントのデータを管理する (SDK バージョン 3 プレビュー)
description: SQL API を使用してオンライン データベースと C# コンソール アプリケーションを作成するチュートリアル。
author: deborahc
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: dotnet
ms.topic: tutorial
ms.date: 12/01/2018
ms.author: dech
ms.openlocfilehash: 27686adb7ae20e50ce822f83002135844f9f93bb
ms.sourcegitcommit: 8330a262abaddaafd4acb04016b68486fba5835b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/04/2019
ms.locfileid: "54037023"
---
# <a name="build-a-net-console-app-to-manage-data-in-azure-cosmos-db-sql-api-account-sdk-version-3-preview"></a>.NET コンソール アプリケーションをビルドして Azure Cosmos DB SQL API アカウントのデータを管理する (SDK バージョン 3 プレビュー)

> [!div class="op_single_selector"]
> * [.NET (プレビュー)](sql-api-dotnet-get-started-preview.md)
> * [.NET](sql-api-get-started.md)
> * [.NET Core](sql-api-dotnetcore-get-started.md)
> * [.NET Core (プレビュー)](sql-api-dotnet-core-get-started-preview.md)
> * [Java](sql-api-java-get-started.md)
> * [Async Java](sql-api-async-java-get-started.md)
> * [Node.js](sql-api-nodejs-get-started.md)
> 

Azure Cosmos DB SQL API を実際に使ってみるチュートリアルへようこそ。 このチュートリアルに従うことで、Azure Cosmos DB リソースを作成し、クエリするコンソール アプリケーションを準備することができます。 このチュートリアルでは、[.NET Standard 2.0.](https://docs.microsoft.com/dotnet/standard/net-standard) をターゲットとする Azure Cosmos DB .NET SDK の[バージョン 3.0 以降](https://www.nuget.org/packages/Microsoft.Azure.Cosmos)を使用します。

このチュートリアルの内容:

> [!div class="checklist"]
> * Azure Cosmos アカウントを作成し、アカウントに接続する
> * Visual Studio でプロジェクトを構成する
> * データベースとコンテナーを作成する
> * コンテナーに項目を追加する
> * コンテナーにクエリを実行する
> * 項目に対する CRUD 操作
> * データベースを削除する

時間がなくても 心配はありません。 [GitHub](https://github.com/Azure-Samples/cosmos-dotnet-getting-started) で完全なソリューションを入手できます。 簡単な手順については、「[チュートリアル のソリューションの完全版を入手する](#GetSolution)」のセクションを参照してください。

それでは始めましょう。

## <a name="prerequisites"></a>前提条件

* アクティブな Azure アカウントアカウントがない場合、Azure 試用版にサインアップして、最大 10 件の無料 Mobile Apps を入手できます。 お持ちでない場合は、 [無料アカウント](https://azure.microsoft.com/free/)にサインアップしてください。 

  [!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

* [!INCLUDE [cosmos-db-emulator-vs](../../includes/cosmos-db-emulator-vs.md)]

## <a name="step-1-create-an-azure-cosmos-db-account"></a>手順 1: Azure Cosmos DB アカウントを作成する
それでは、Azure Cosmos DB アカウントを作成してみましょう。 使用するアカウントが既にある場合は、「 [Visual Studio ソリューションをセットアップする](#SetupVS)」に進んでかまいません。 Azure Cosmos DB Emulator を使用する場合は、[Azure Cosmos DB Emulator](local-emulator.md) に関する記事に記載されている手順に従ってエミュレーターをセットアップし、「[Visual Studio プロジェクトをセットアップする](#SetupVS)」に進んでください。

[!INCLUDE [create-dbaccount-preview](../../includes/cosmos-db-create-dbaccount-preview.md)]

## <a id="SetupVS"></a>手順 2: Visual Studio プロジェクトをセットアップする
1. コンピューターで **Visual Studio 2017** を開きます。
1. **[ファイル]** メニューで、**[新規]**、**[プロジェクト]** の順に選択します。
1. **[新しいプロジェクト]** ダイアログで、**[Visual C#]** / **[コンソール アプリ (.NET Framework)]** の順に選択し、プロジェクトの名前を指定して、**[OK]** をクリックします。
    ![[新しいプロジェクト] ウィンドウのスクリーン ショット](./media/sql-api-get-started/dotnet-tutorial-visual-studio-new-project.png)
1. **ソリューション エクスプローラー**で、Visual Studio ソリューションの下にある新しいコンソール アプリケーションを右クリックし、**[NuGet パッケージの管理]** をクリックします。
    
    ![プロジェクトの右クリック メニューのスクリーン ショット](./media/sql-api-get-started/dotnet-tutorial-visual-studio-manage-nuget.png)
1. **[NuGet]** タブの **[参照]** をクリックし、検索ボックスに「**Microsoft.Azure.Cosmos**」と入力します。 プレビューを検索できるように、必ず *[プレリリースを含める]*  を選択してください。
1. 結果で **Microsoft.Azure.Cosmos** を探し、**[インストール]** をクリックします。
   Azure Cosmos DB SQL API クライアント ライブラリのパッケージ ID は [Microsoft Azure Cosmos DB クライアント ライブラリ](https://www.nuget.org/packages/Microsoft.Azure.Cosmos/) です。
   ![Azure Cosmos DB クライアント SDK を見つける NuGet メニューのスクリーン ショット](./media/sql-api-get-started/dotnet-tutorial-visual-studio-manage-nuget-2.png)

    ソリューションの変更の確認に関するメッセージが表示されたら、**[OK]** をクリックします。 ライセンスの同意に関するメッセージが表示されたら、**[同意する]** をクリックします。

これでセットアップは終了です。 いくつかのコードの記述を開始しましょう。 このチュートリアルの完成したコード プロジェクトは [GitHub](https://github.com/Azure-Samples/cosmos-dotnet-getting-started)にあります。

## <a id="Connect"></a>手順 3: Azure Cosmos DB アカウントに接続する
1. まず、**Program.cs** ファイルで、C# アプリケーションの先頭にある参照を、以下の参照で置き換えます。
    ```csharp
    using System;
    using System.Threading.Tasks;
    using System.Configuration;
    using Microsoft.Azure.Cosmos;
    using System.Collections.Generic;
    using System.Net;
    ```
1. 次に、パブリック クラス ``Program`` に次の定数と変数を追加します。
    ```csharp
    public class Program
    {
        // ADD THIS PART TO YOUR CODE

        // The Azure Cosmos DB endpoint for running this sample.
        private static readonly string EndpointUri = "<your endpoint here>";
        // The primary key for the Azure Cosmos account.
        private static readonly string PrimaryKey = "<your primary key>";

        // The Cosmos client instance
        private CosmosClient cosmosClient;

        // The database we will create
        private CosmosDatabase database;

        // The container we will create.
        private CosmosContainer container;

        // The name of the database and container we will create
        private string databaseId = "FamilyDatabase";
        private string containerId = "FamilyContainer";
    }
    ```
    以前のバージョンの .NET SDK に精通している場合は、"コレクション" や "ドキュメント" という用語をよく目にしたことに注意してください。 Azure Cosmos DB では複数の API モデルをサポートしているため、バージョン 3.0 以降の .NET SDK では、"コンテナー" と "項目" という一般的な用語が使用されています。 コンテナーは、コレクション、グラフ、またはテーブルを表します。 項目は、ドキュメント、エッジ/頂点、行など、コンテナー内の内容を表します。 [データベース、コンテナー、項目についてはこちらを参照してください。](databases-containers-items.md)

1. エンドポイント URL とプライマリ キーを [Azure portal](https://portal.azure.com) で取得します。

    Azure Portal で Azure Cosmos DB アカウントに移動し、**[キー]** をクリックします。

    ポータルから URI をコピーし、```Program.cs``` ファイルの `<your endpoint URL>` に貼り付けます。 ポータルからプライマリ キーをコピーし、`<your primary key>` に貼り付けます。

   ![Azure portal から Azure Cosmos DB キーを取得するスクリーン ショット](./media/sql-api-get-started/dotnet-tutorial-portal-keys.png)

1. 次に、```CosmosClient``` の新しいインスタンスを作成し、プログラムのためにいくつかのスキャフォールディングを設定します。

    **Main** メソッドの下に、**GetStartedDemoAsync** という新しい非同期タスクを追加します。これによって新しい ```CosmosClient``` はインスタンス化されます。 Azure Cosmos DB リソース上で実行するメソッドを呼び出すエントリ ポイントとして **GetStartedDemoAsync** を使用します。

    ```csharp
    public static async Task Main(string[] args)
    {
    }

    // ADD THIS PART TO YOUR CODE
    /*
        Entry point to call methods that operate on Azure Cosmos DB resources in this sample
    */   
    public async Task GetStartedDemoAsync()
    {
        // Create a new instance of the Cosmos Client
        this.cosmosClient = new CosmosClient(EndpointUri, PrimaryKey);
    }
    ```

1. 次のコードを追加して、**Main** メソッドから **GetStartedDemoAsync** 非同期タスクを実行します。 **Main** メソッドは例外をキャッチし、コンソールに書き込みます。
    ```csharp
    public static async Task Main(string[] args)
    {
        // ADD THIS PART TO YOUR CODE
        try
        {
            Console.WriteLine("Beginning operations...\n");
            Program p = new Program();
            await p.GetStartedDemoAsync();
        }
        catch (CosmosException de)
        {
            Exception baseException = de.GetBaseException();
            Console.WriteLine("{0} error occurred: {1}\n", de.StatusCode, de);
        }
        catch (Exception e)
        {
            Console.WriteLine("Error: {0}\n", e);
        }
        finally
        {
            Console.WriteLine("End of demo, press any key to exit.");
            Console.ReadKey();
        }
    }
    ```

1. **F5** キーを押してアプリケーションを実行します。 コンソール ウィンドウの出力には、Azure Cosmos DB との接続が確立されたことを示す、`End of demo, press any key to exit.` というメッセージが表示されます。 表示されたら、コンソール ウィンドウを閉じます。 

お疲れさまでした。 これで、Azure Cosmos DB アカウントに接続しました。 

## <a name="step-4-create-a-database"></a>手順 4: データベースを作成する
データベースは、``CosmosDatabases`` クラスの [**CreateDatabaseIfNotExistsAsync**](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.cosmosdatabases) または [**CreateDatabaseAsync**](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.cosmosdatabases) 関数を使用して作成できます。 データベースは、コンテナーに分割された項目の論理上のコンテナーです。
    
1. **CreateDatabase** メソッドをコピーして、**GetStartedDemoAsync** メソッドの下に貼り付けます。 **CreateDatabase** によって、``databaseId`` フィールドに指定された id で新しいデータベース ``FamilyDatabase`` が作成されます (このデータベースがまだ存在していない場合)。 

    ```csharp
    /*
        Create the database if it does not exist
    */    
    private async Task CreateDatabase()
    {
        // Create a new database
        this.database = await this.cosmosClient.Databases.CreateDatabaseIfNotExistsAsync(databaseId);
        Console.WriteLine("Created Database: {0}\n", this.database.Id);
    }
    ```

1. CosmosClient をインスタンス化したところに、次のコードをコピーして貼り付けて、追加した **CreateDatabase** メソッドを呼び出します。

    ```csharp
    public async Task GetStartedDemoAsync()
    {
        // Create a new instance of the Cosmos Client
        this.cosmosClient = new CosmosClient(EndpointUri, PrimaryKey);

        //ADD THIS PART TO YOUR CODE
        await this.CreateDatabase(); 
    }
    ```

    この時点でコードは次のようになっているはずです。ここに実際のエンドポイントとプライマリ キーを入力します。
    ```csharp
    using System;
    using System.Threading.Tasks;
    using System.Configuration;
    using Microsoft.Azure.Cosmos;
    using System.Collections.Generic;
    using System.Net;

    namespace CosmosGettingStarted
    {
        class Program
        {
            // The Azure Cosmos DB endpoint for running this sample.
            private static readonly string EndpointUri = "<your endpoint here>";
            // The primary key for the Azure Cosmos account.
            private static readonly string PrimaryKey = "<your primary key>";

            // The Cosmos client instance
            private CosmosClient cosmosClient;
        
            // The database we will create
            private CosmosDatabase database;

            // The container we will create.
            private CosmosContainer container;

            // The name of the database and container we will create
            private string databaseId = "FamilyDatabase";
            private string containerId = "FamilyContainer";

            public static async Task Main(string[] args)
            {
                try
                {
                    Console.WriteLine("Beginning operations...");
                    Program p = new Program();
                    await p.GetStartedDemoAsync();
                }
                catch (CosmosException de)
                {
                    Exception baseException = de.GetBaseException();
                    Console.WriteLine("{0} error occurred: {1}\n", de.StatusCode, de);
                }
                catch (Exception e)
                {
                    Console.WriteLine("Error: {0}\n", e);
                }
                finally
                {
                    Console.WriteLine("End of demo, press any key to exit.");
                    Console.ReadKey();
                }
            }

            /*
                Entry point to call methods that operate on Azure Cosmos DB resources in this sample
            */
            public async Task GetStartedDemoAsync()
            {
                // Create a new instance of the Cosmos Client
                this.cosmosClient = new CosmosClient(EndpointUri, PrimaryKey);
                await this.CreateDatabase();
            }

            /*
                Create the database if it does not exist
            */    
            private async Task CreateDatabase()
            {
                // Create a new database
                this.database = await this.cosmosClient.Databases.CreateDatabaseIfNotExistsAsync(databaseId);
                Console.WriteLine("Created Database: {0}\n", this.database.Id);
            }
        }
    }
    ```

**F5** キーを押してアプリケーションを実行します。

お疲れさまでした。 これで、Azure Cosmos DB データベースが作成されました。  

## <a id="CreateColl"></a>手順 5: コンテナーを作成する
> [!WARNING]
> **CreateContainerIfNotExistsAsync** メソッドを呼び出すと、価格に影響する新しいコンテナーが作成されます。 詳細については、[価格のページ](https://azure.microsoft.com/pricing/details/cosmos-db/)を参照してください。
> 
> 

コンテナーは、**CosmosContainers** クラスの [**CreateContainerIfNotExistsAsync**](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.cosmoscontainers) または [**CreateContainerAsync**](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.cosmoscontainers) 関数のいずれかを使用して作成できます。 コンテナーは、項目 (SQL API の場合は JSON ドキュメント) および関連する JavaScript サーバー側アプリケーション ロジック (ストアド プロシージャ、ユーザー定義関数、トリガーなど) で構成されます。

1. **CreateContainer** メソッドをコピーして、**CreateDatabase** メソッドの下に貼り付けます。 **CreateContainer** によって、``containerId`` フィールドに指定された id で新しいコンテナー ``FamilyContainer`` が作成されます (このコンテナーがまだ存在していない場合)。 

    ```csharp
    /*
        Create the container if it does not exist. 
        Specifiy "/LastName" as the partition key since we're storing family information, to ensure good distribution of requests and storage.
    */
    private async Task CreateContainer()
    {
        // Create a new container
        this.container = await this.database.Containers.CreateContainerIfNotExistsAsync(containerId, "/LastName");
        Console.WriteLine("Created Container: {0}\n", this.container.Id);
    }
    ```

1. CosmosClient をインスタンス化したところに、次のコードをコピーして貼り付けて、追加した **CreateContainer** メソッドを呼び出します。

    ```csharp
    public async Task GetStartedDemoAsync()
    {
        // Create a new instance of the Cosmos Client
        this.cosmosClient = new CosmosClient(EndpointUri, PrimaryKey);
        await this.CreateDatabase(); 

        //ADD THIS PART TO YOUR CODE
        await this.CreateContainer();
    }

**F5** キーを押してアプリケーションを実行します。

お疲れさまでした。 これで、Azure Cosmos DB コンテナーが作成されました。  

## <a id="CreateDoc"></a>手順 6: コンテナーに項目を追加する

項目は、 **CosmosItems** クラスの [**CreateItemAsync**](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.cosmositems) 関数を使用して作成できます。SQL API を使用すると、項目はドキュメントとして投影されます。これは、ユーザー定義の (任意の) JSON コンテンツです。 これで、Azure Cosmos DB コンテナーに項目を挿入できます。

まず、この例の Azure Cosmos DB に格納するオブジェクトの **Family** クラスを作成する必要があります。 さらに、**Family** 内で使用するサブクラスとして、**Parent**、**Child**、**Pet**、**Address** を作成します。ドキュメントには、JSON で **id** としてシリアル化される **Id** プロパティが必要であることに注意してください。

1.  **Ctrl + Shift + A** キーを押して **[新しい項目の追加]** ダイアログを開きます。新しいクラス **Family.cs** をプロジェクトに追加します。

    ![プロジェクトに新しい Family.cs クラスを追加する画面のスクリーン ショット](./media/sql-api-get-started/dotnet-tutorial-visual-studio-add-family-class.png)

1.  **Family**、**Parent**、**Child**、**Pet**、および **Address** クラスをコピーして、**Family.cs** に貼り付けます。

    ```csharp
    using Newtonsoft.Json;

    namespace CosmosGettingStarted
    {
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
    }
    ```
1. **Program.cs** に戻り、**CreateContainer** メソッドの下に **AddItemsToContainer** メソッドを追加します。 コードによって、同じ ID を持つ項目が存在していないことが項目の作成前に確認されます。 2 つの項目を挿入します。1 つは Andersen Family のドキュメント、もう 1 つは Wakefield Family のドキュメントです。

    ```csharp
    /*
        Add Family items to the container
    */
    private async Task AddItemsToContainer()
    {
        // Create a family object for the Andersen family
        Family andersenFamily = new Family
        {
            Id = "Andersen.1",
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

        // Read the item to see if it exists. Note ReadItemAsync will not throw an exception if an item does not exist. Instead, we check the StatusCode property off the response object. 
        CosmosItemResponse<Family> andersenFamilyResponse = await this.container.Items.ReadItemAsync<Family>(andersenFamily.LastName, andersenFamily.Id);

        if (andersenFamilyResponse.StatusCode == HttpStatusCode.NotFound)
        {
            // Create an item in the container representing the Andersen family. Note we provide the value of the partition key for this item, which is "Andersen"
            andersenFamilyResponse = await this.container.Items.CreateItemAsync<Family>(andersenFamily.LastName, andersenFamily);

            // Note that after creating the item, we can access the body of the item with the Resource property off the CosmosItemResponse. 
            //We can also access the RequestCharge property to see the amount of RUs consumed on this request.
            Console.WriteLine("Created item in database with id: {0} Operation consumed {1} RUs.\n", andersenFamilyResponse.Resource.Id, andersenFamilyResponse.RequestCharge);
        }
        else
        {
            Console.WriteLine("Item in database with id: {0} already exists\n", andersenFamilyResponse.Resource.Id);
        }

        // Create a family object for the Wakefield family
        Family wakefieldFamily = new Family
        {
            Id = "Wakefield.7",
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

        // Read the item to see if it exists
        CosmosItemResponse<Family> wakefieldFamilyResponse = await this.container.Items.ReadItemAsync<Family>(wakefieldFamily.LastName, wakefieldFamily.Id);

        if (wakefieldFamilyResponse.StatusCode == HttpStatusCode.NotFound)
        {
            // Create an item in the container representing the Wakefield family. Note we provide the value of the partition key for this item, which is "Wakefield"
            wakefieldFamilyResponse = await this.container.Items.CreateItemAsync<Family>(wakefieldFamily.LastName, wakefieldFamily);

            // Note that after creating the item, we can access the body of the item with the Resource property off the CosmosItemResponse. 
            //We can also access the RequestCharge property to see the amount of RUs consumed on this request.
            Console.WriteLine("Created item in database with id: {0} Operation consumed {1} RUs.\n", wakefieldFamilyResponse.Resource.Id, wakefieldFamilyResponse.RequestCharge);
        }
        else
        {
            Console.WriteLine("Item in database with id: {0} already exists\n", wakefieldFamilyResponse.Resource.Id);
        }
    }
    ```
1. ``GetStartedDemoAsync`` メソッドに ``AddItemsToContainer`` への呼び出しを追加します。

    ```csharp
    public async Task GetStartedDemoAsync()
    {
        // Create a new instance of the Cosmos Client
        this.cosmosClient = new CosmosClient(EndpointUri, PrimaryKey);
        await this.CreateDatabase(); 
        await this.CreateContainer();

        //ADD THIS PART TO YOUR CODE
        await this.AddItemsToContainer();
    }
    ```

**F5** キーを押してアプリケーションを実行します。

お疲れさまでした。 これで、Azure Cosmos DB の項目が 2 つ作成されました。  


## <a id="Query"></a>手順 7: Azure Cosmos DB リソースを照会する
Azure Cosmos DB では、各コレクションに格納された JSON ドキュメントに対する豊富な[クエリ](sql-api-sql-query.md)がサポートされています。 次のサンプル コードを使用して、前の手順で挿入した項目に対してどのようにクエリを実行するかを説明します。

1. **RunQuery** メソッドをコピーして、**AddItemsToContainer** メソッドの下に貼り付けます。

    ```csharp
    /*
        Run a query (using Azure Cosmos DB SQL syntax) against the container
    */
    private async Task RunQuery()
    {
        var sqlQueryText = "SELECT * FROM c WHERE c.LastName = 'Andersen'";
        var partitionKeyValue = "Andersen";

        Console.WriteLine("Running query: {0}\n", sqlQueryText);

        CosmosSqlQueryDefinition queryDefinition = new CosmosSqlQueryDefinition(sqlQueryText);
        CosmosResultSetIterator<Family> queryResultSetIterator = this.container.Items.CreateItemQuery<Family>(queryDefinition, partitionKeyValue);

        List<Family> families = new List<Family>();

        while (queryResultSetIterator.HasMoreResults)
        {
            CosmosQueryResponse<Family> currentResultSet = await queryResultSetIterator.FetchNextSetAsync();
            foreach (Family family in currentResultSet)
            {
                families.Add(family);
                Console.WriteLine("\tRead {0}\n", family);
            }
        }
    }
    ```
1. ``GetStartedDemoAsync`` メソッドに ``RunQuery`` への呼び出しを追加します。

    ```csharp
    public async Task GetStartedDemoAsync()
    {
        // Create a new instance of the Cosmos Client
        this.cosmosClient = new CosmosClient(EndpointUri, PrimaryKey);
        await this.CreateDatabase(); 
        await this.CreateContainer();
        await this.AddItemsToContainer();

        //ADD THIS PART TO YOUR CODE
        await this.RunQuery();
    }
    ```

**F5** キーを押してアプリケーションを実行します。

お疲れさまでした。 これで、Azure Cosmos DB コンテナーに対するクエリが実行されました。

## <a id="ReplaceItem"></a>手順 8: JSON 項目を置換する
ここでは、Azure Cosmos DB 内の項目を更新します。

1. **ReplaceFamilyItem** メソッドをコピーして、**RunQuery** メソッドの下に貼り付けます。 家族の ``IsRegistered`` プロパティと子どもの 1 人の ``Grade`` を変更していることに注意してください。
    ```csharp
    /*
    Update an item in the container
    */
    private async Task ReplaceFamilyItem()
    {
        CosmosItemResponse<Family> wakefieldFamilyResponse = await this.container.Items.ReadItemAsync<Family>("Wakefield", "Wakefield.7");
        var itemBody = wakefieldFamilyResponse.Resource;
        
        // update registration status from false to true
        itemBody.IsRegistered = true;
        // update grade of child
        itemBody.Children[0].Grade = 6;

        // replace the item with the updated content
        wakefieldFamilyResponse = await this.container.Items.ReplaceItemAsync<Family>(itemBody.LastName, itemBody.Id, itemBody);
        Console.WriteLine("Updated Family [{0},{1}]\n. Body is now: {2}\n", itemBody.LastName, itemBody.Id, wakefieldFamilyResponse.Resource);
    }
    ```
1. ``GetStartedDemo`` メソッドに ``ReplaceFamilyItem`` への呼び出しを追加します。

    ```csharp
    public async Task GetStartedDemoAsync()
    {
        // Create a new instance of the Cosmos Client
        this.cosmosClient = new CosmosClient(EndpointUri, PrimaryKey);
        await this.CreateDatabase(); 
        await this.CreateContainer();
        await this.AddItemsToContainer();
        await this.RunQuery();

        //ADD THIS PART TO YOUR CODE
        await this.ReplaceFamilyItem();
    }

**F5** キーを押してアプリケーションを実行します。

お疲れさまでした。 これで、Azure Cosmos DB 項目が置換されました。

## <a id="DeleteDocument"></a>手順 9: 項目を削除する

ここでは、 Azure Cosmos DB 内の項目を削除します。

1. **DeleteFamilyItem** メソッドをコピーして、**ReplaceFamilyItem** メソッドの下に貼り付けます。

    ```csharp
    /*
    Delete an item in the container
    */
    private async Task DeleteFamilyItem()
    {
        var partitionKeyValue = "Wakefield";
        var familyId = "Wakefield.7";

        // Delete an item. Note we must provide the partition key value and id of the item to delete
        CosmosItemResponse<Family> wakefieldFamilyResponse = await this.container.Items.DeleteItemAsync<Family>(partitionKeyValue, familyId);
        Console.WriteLine("Deleted Family [{0},{1}]\n", partitionKeyValue, familyId);
    }
    ```
1. ``GetStartedDemo`` メソッドに ``DeleteFamilyItem`` への呼び出しを追加します。

    ```csharp
    public async Task GetStartedDemoAsync()
    {
        // Create a new instance of the Cosmos Client
        this.cosmosClient = new CosmosClient(EndpointUri, PrimaryKey);
        await this.CreateDatabase(); 
        await this.CreateContainer();
        await this.AddItemsToContainer();
        await this.RunQuery();
        await this.ReplaceFamilyItem();

        //ADD THIS PART TO YOUR CODE
        await this.DeleteFamilyItem();
    }
    ```

**F5** キーを押してアプリケーションを実行します。

お疲れさまでした。 これで、Azure Cosmos DB 項目が削除されました。

## <a id="DeleteDatabase"></a>手順 10: データベースを削除する
ここでは、データベースを削除します。 作成したデータベースを削除すると、データベースとすべての子リソース (コンテナー、項目、ストアド プロシージャ、ユーザー定義関数、トリガーなど) が削除されます。 ここでは、**CosmosClient** インスタンスも破棄します。

1. **DeleteDatabaseAndCleanup** メソッドをコピーして、**DeleteFamilyItem** メソッドの下に貼り付けます。
    ```csharp
    /*
    Delete the database and dispose of the Cosmos Client instance
    */
    private async Task DeleteDatabaseAndCleanup()
    {
        CosmosDatabaseResponse databaseResourceResponse = await this.database.DeleteAsync();
        // Also valid: await this.cosmosClient.Databases["FamilyDatabase"].DeleteAsync();

        Console.WriteLine("Deleted Database: {0}\n", this.databaseId);

        //Dispose of CosmosClient
        this.cosmosClient.Dispose();
    }
    ```
1. ``GetStartedDemo`` メソッドに ``DeleteDatabaseAndCleanup`` への呼び出しを追加します。
    
    ```csharp
    public async Task GetStartedDemoAsync()
    {
        // Create a new instance of the Cosmos Client
        this.cosmosClient = new CosmosClient(EndpointUri, PrimaryKey);
        await this.CreateDatabase(); 
        await this.CreateContainer();
        await this.AddItemsToContainer();
        await this.RunQuery();
        await this.ReplaceFamilyItem();
        await this.DeleteFamilyItem();

        //ADD THIS PART TO YOUR CODE        
        await this.DeleteDatabaseAndCleanup();
    }
    ```

**F5** キーを押してアプリケーションを実行します。

お疲れさまでした。 これで、Azure Cosmos DB データベースが削除されました。

## <a id="Run"></a>手順 11: C# コンソール アプリケーションの全体的な実行の流れ
Visual Studio で F5 キーを押すと、アプリケーションがビルドされデバッグ モードで実行します。

アプリケーション全体の出力がコンソール ウィンドウに表示されます。 出力では追加したクエリの結果が表示されます。次の例のようなものになるはずです。

```
Beginning operations...

Created Database: FamilyDatabase

Created Container: FamilyContainer

Created item in database with id: Andersen.1 Operation consumed 11.43 RUs.

Created item in database with id: Wakefield.7 Operation consumed 14.29 RUs.

Running query: SELECT * FROM c WHERE c.LastName = 'Andersen'

        Read {"id":"Andersen.1","LastName":"Andersen","Parents":[{"FamilyName":null,"FirstName":"Thomas"},{"FamilyName":null,"FirstName":"Mary Kay"}],"Children":[{"FamilyName":null,"FirstName":"Henriette Thaulow","Gender":"female","Grade":5,"Pets":[{"GivenName":"Fluffy"}]}],"Address":{"State":"WA","County":"King","City":"Seattle"},"IsRegistered":false}

Updated Family [Wakefield,Wakefield.7].
        Body is now: {"id":"Wakefield.7","LastName":"Wakefield","Parents":[{"FamilyName":"Wakefield","FirstName":"Robin"},{"FamilyName":"Miller","FirstName":"Ben"}],"Children":[{"FamilyName":"Merriam","FirstName":"Jesse","Gender":"female","Grade":6,"Pets":[{"GivenName":"Goofy"},{"GivenName":"Shadow"}]},{"FamilyName":"Miller","FirstName":"Lisa","Gender":"female","Grade":1,"Pets":null}],"Address":{"State":"NY","County":"Manhattan","City":"NY"},"IsRegistered":true}

Deleted Family [Wakefield,Wakefield.7]

Deleted Database: FamilyDatabase

End of demo, press any key to exit.
```

お疲れさまでした。 チュートリアルが完了し、実際に動作する C# コンソール アプリケーションが完成しました。

## <a id="GetSolution"></a>チュートリアルのソリューションの完全版を入手する
このチュートリアルの手順を実行する時間がない場合や、コード サンプルをダウンロードするだけの場合は、[GitHub](https://github.com/Azure-Samples/cosmos-dotnet-getting-started) から入手できます。 

GetStarted ソリューションをビルドするには、以下のものが必要です。

* アクティブな Azure アカウントアカウントがない場合、Azure 試用版にサインアップして、最大 10 件の無料 Mobile Apps を入手できます。 お持ちでない場合は、 [無料アカウント](https://azure.microsoft.com/free/)にサインアップしてください。
* [Azure Cosmos DB アカウント][cosmos-db-create-account]。
* GitHub で入手可能な [GetStarted](https://github.com/Azure-Samples/cosmos-dotnet-getting-started) ソリューション。

Visual Studio で Azure Cosmos DB .NET SDK への参照を復元するには、ソリューション エクスプローラーで **GetStarted** ソリューションを右クリックし、**[NuGet パッケージの復元]** をクリックします。 次に、「[Azure Cosmos DB アカウントに接続する](#Connect)」の説明に従って、App.config ファイルの EndPointUri と PrimaryKey の値を更新します。

以上です。ビルドすれば完了です。


## <a name="next-steps"></a>次の手順
* さらに詳しい ASP.NET MVC チュートリアルが必要な場合には、 [ASP.NET MVC チュートリアルの Azure Cosmos DB を使用した Web アプリケーションの開発](sql-api-dotnet-application-preview.md)に関するページを参照してください。
* Azure Cosmos DB のスケールとパフォーマンスをテストする場合には、 「[Azure Cosmos DB のパフォーマンスとスケールのテスト](performance-testing.md)」を参照してください。
* [Azure Cosmos DB の要求、使用状況、およびストレージを監視する](monitor-accounts.md)方法を確認します。
* [クエリのプレイ グラウンド](https://www.documentdb.com/sql/demo)でサンプル データセットに対してクエリを実行します。
* Azure Cosmos DB の詳細については、「[Azure Cosmos DB の概要](https://docs.microsoft.com/azure/cosmos-db/introduction)」を参照してください。

[cosmos-db-create-account]: create-sql-api-dotnet-preview.md#create-account
