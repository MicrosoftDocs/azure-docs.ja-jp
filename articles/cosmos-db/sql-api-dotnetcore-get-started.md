---
title: チュートリアル:Azure Cosmos DB の SQL API アカウントに保存されているデータを管理する .NET Core アプリを構築する
description: このチュートリアルでは、SQL API .NET Core SDK for Azure Cosmos DB を使用してオンライン データベースと C# コンソール アプリケーションを作成します。
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: dotnet
ms.topic: tutorial
ms.date: 03/12/2018
ms.author: sngun
Customer intent: As a developer, I want to build a .NET Core application to access and manage Azure Cosmos DB resources so that customers can utilize the global distribution, elastic scaling, multi-master, and other capabilities that Azure Cosmos DB offers.
ms.openlocfilehash: 81c80dbd7ffa8e5c4e7b2ebb1c9d17eb6007ae9f
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/18/2019
ms.locfileid: "58802353"
---
# <a name="tutorial-build-a-net-core-app-to-manage-data-stored-in-a-sql-api-account"></a>チュートリアル:SQL API アカウントに保存されているデータを管理する .NET Core アプリを構築する

> [!div class="op_single_selector"]
> * [.NET Core](sql-api-dotnetcore-get-started.md)
> * [.NET Core (プレビュー)](sql-api-dotnet-core-get-started-preview.md)
> * [.NET](sql-api-get-started.md)
> * [.NET (プレビュー)](sql-api-dotnet-get-started-preview.md)
> * [Java](sql-api-java-get-started.md)
> * [Async Java](sql-api-async-java-get-started.md)
> * [Node.js](sql-api-nodejs-get-started.md)
> 

開発者であれば、NoSQL ドキュメント データを使用するアプリケーションをお持ちかもしれません。 Azure Cosmos DB の SQL API アカウントを使用して、このドキュメント データを格納し、そのデータにアクセスすることができます。 このチュートリアルでは、Azure Cosmos DB の SQL API アカウントに保存されるデータを作成および照会する .NET Core アプリを構築する方法について説明します。 

このチュートリアルに含まれるタスクは次のとおりです。

> [!div class="checklist"]
> * Azure Cosmos アカウントを作成し、アカウントに接続する
> * Visual Studio ソリューションを構成する
> * オンライン データベースを作成する
> * コレクションを作成する
> * JSON ドキュメントを作成する
> * 項目、コンテナー、およびデータベースに対する CRUD 操作を実行する

アプリケーションを作成する時間がありませんか。 [GitHub](https://github.com/Azure-Samples/documentdb-dotnet-core-getting-started) で完全なソリューションを入手できます。 

## <a name="prerequisites"></a>前提条件

* アクティブな Azure アカウントアカウントがない場合、Azure 試用版にサインアップして、最大 10 件の無料 Mobile Apps を入手できます。 お持ちでない場合は、 [無料アカウント](https://azure.microsoft.com/free/)にサインアップしてください。 

  [!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

* 無料の [Visual Studio 2017 Community Edition](https://www.visualstudio.com/downloads/) をダウンロードして使用してください。 ユニバーサル Windows プラットフォーム (UWP) アプリを開発している場合は、**バージョン 15.4 以降の Visual Studio 2017** を使う必要があります。 Visual Studio のセットアップ中に、必ず **[Azure の開発]** を有効にしてください。

    * MacOS または Linux の場合、選択したプラットフォーム用の [.NET Core SDK](https://www.microsoft.com/net/core#macos) をインストールすることで、コマンド ラインから .NET Core アプリを開発できます。 

    * Windows の場合、[.NET Core SDK](https://www.microsoft.com/net/core#windows) をインストールすることで、コマンド ラインから .NET Core アプリを開発できます。 

## <a name="create-an-azure-cosmos-account"></a>Azure Cosmos アカウントを作成する

次の手順で Azure Cosmos アカウントを作成します。

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

## <a id="SetupVS"></a>Visual Studio ソリューションをセットアップする

1. コンピューターで **Visual Studio 2017** を開きます。

2. **[ファイル]** メニューで、**[新規]**、**[プロジェクト]** の順に選択します。

3. **[新しいプロジェクト]** ダイアログで、**[テンプレート]** > **[Visual C#]** > **[.NET Core]** > **[Console Application (.NET Core)]\(コンソール アプリケーション (.NET Core)\)** の順に選択します。プロジェクトに **DocumentDBGettingStarted** という名前を付け、**[OK]** を選択します。

   ![[新しいプロジェクト] ウィンドウのスクリーンショット](./media/sql-api-dotnetcore-get-started/nosql-tutorial-new-project-2.png)

4. **ソリューション エクスプローラー**で **[DocumentDBGettingStarted]** を右クリックします。

5. 同じメニューで、**[NuGet パッケージの管理]** を選択します。

   ![プロジェクトの右クリック メニューのスクリーンショット](./media/sql-api-dotnetcore-get-started/nosql-tutorial-manage-nuget-pacakges.png)

6. **[NuGet]** タブで、ウィンドウの上部にある **[参照]** を選択し、検索ボックスに「**azure documentdb**」と入力します。 **[プレリリースを含める]** チェック ボックスがオンになっていることを確認します。

7. 結果で **Microsoft.Azure.DocumentDB.Core** を探し、**[インストール]** を選択します。

   ライブラリのパッケージ ID は [Microsoft.Azure.DocumentDB.Core](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB.Core) です。 この .NET Core NuGet パッケージでサポートされていない .NET Framework バージョン (net461 など) を対象とする場合は、.NET Framework 4.5 以降のすべての .NET Framework バージョンをサポートする [Microsoft.Azure.DocumentDB](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB) を使用します。

8. プロンプトが表示されたら、NuGet パッケージのインストールと使用許諾契約に同意します。

セットアップが完了したので、コードの記述を始めましょう。 このチュートリアルの完成したコード プロジェクトは [GitHub](https://github.com/Azure-Samples/documentdb-dotnet-core-getting-started) にあります。

## <a id="Connect"></a>Azure Cosmos アカウントに接続する

必要な依存関係をインポートし、Azure Cosmos に接続します。 依存関係をインポートするには、Program.cs ファイルの先頭に次のコードを追加します。

```csharp
using System;

// ADD THIS PART TO YOUR CODE
using System.Linq;
using System.Threading.Tasks;
using System.Net;
using Microsoft.Azure.Documents;
using Microsoft.Azure.Documents.Client;
using Newtonsoft.Json;
```

次に、これら 2 つの定数と *client* 変数をパブリック クラス *Program* の下に追加します。

```csharp
class Program
{
    // ADD THIS PART TO YOUR CODE
    private const string EndpointUri = "<your endpoint URI>";
    private const string PrimaryKey = "<your key>";
    private DocumentClient client;
```

次に、[Azure portal](https://portal.azure.com) に移動し、URI とプライマリ キーを取得します。 Azure Cosmos DB の URI とプライマリ キーは、アプリケーションが接続先を認識し、Azure Cosmos DB がアプリケーションの接続を信頼するために必要です。

Azure ポータルで Azure Cosmos アカウントに移動し、**[キー]** を選択します。

ポータルから URI をコピーし、program.cs ファイルの `<your endpoint URI>` に貼り付けます。 次に、ポータルからプライマリ キーをコピーし、 `<your key>`に貼り付けます。 エンドポイントとキーを指定する際は、< と > を削除したうえで二重引用符で囲みます。

![Azure portal からキーを取得する][keys]

**DocumentClient**の新しいインスタンスを作成して、作業の開始アプリケーションを起動します。

**Main** メソッドの下に、**GetStartedDemo** という新しい非同期タスクを追加します。このタスクによって、新しい **DocumentClient** がインスタンス化されます。

```csharp
static void Main(string[] args)
{
}

// ADD THIS PART TO YOUR CODE
private async Task GetStartedDemo()
{
    this.client = new DocumentClient(new Uri(EndpointUri), PrimaryKey);
}
```

次のコードを追加して、 **Main** メソッドから非同期タスクを実行します。 **Main** メソッドは例外をキャッチし、コンソールに書き込みます。

```csharp
static void Main(string[] args)
{
        // ADD THIS PART TO YOUR CODE
        try
        {
                Program p = new Program();
                p.GetStartedDemo().Wait();
        }
        catch (DocumentClientException de)
        {
                Exception baseException = de.GetBaseException();
                Console.WriteLine("{0} error occurred: {1}, Message: {2}", de.StatusCode, de.Message, baseException.Message);
        }
        catch (Exception e)
        {
                Exception baseException = e.GetBaseException();
                Console.WriteLine("Error: {0}, Message: {1}", e.Message, baseException.Message);
        }
        finally
        {
                Console.WriteLine("End of demo, press any key to exit.");
                Console.ReadKey();
        }
```

**[DocumentDBGettingStarted]** ボタンを選択して、アプリケーションをビルドして実行します。

## <a id="CreateDatabase"></a>データベースを作成する

データベースを作成するコードを追加する前に、コンソールに書き込むためのヘルパー メソッドを追加します。 **WriteToConsoleAndPromptToContinue** メソッドをコピーして、**GetStartedDemo** メソッドの下に貼り付けます。

```csharp
// ADD THIS PART TO YOUR CODE
private void WriteToConsoleAndPromptToContinue(string format, params object[] args)
{
        Console.WriteLine(format, args);
        Console.WriteLine("Press any key to continue ...");
        Console.ReadKey();
}
```

**DocumentClient** クラスの `CreateDatabaseAsync` メソッドを使用して、Azure Cosmos DB データベースを作成します。 データベースは、コレクションに分割された JSON ドキュメント ストレージの論理上のコンテナーです。 次のコードをコピーし、**GetStartedDemo** メソッドに貼り付けます。クライアントを作成する処理のすぐ下に追加してください。 これで *FamilyDB* というデータベースが作成されます。

```csharp
private async Task GetStartedDemo()
{
    this.client = new DocumentClient(new Uri(EndpointUri), PrimaryKey);

    // ADD THIS PART TO YOUR CODE
    await this.client.CreateDatabaseIfNotExistsAsync(new Database { Id = "FamilyDB_oa" });
```

**[DocumentDBGettingStarted]** ボタンを選択して、アプリケーションを実行します。

## <a id="CreateColl"></a>コレクションを作成する

**DocumentClient** クラスの `CreateDocumentCollectionAsync` メソッドを使用して、コレクションを作成します。 コレクションには、JSON ドキュメントのほか、関連する JavaScript アプリケーション ロジックが格納されます。

> [!WARNING]
> **CreateDocumentCollectionAsync** は、予約済みのスループットで新しいコレクションを作成します。これによって価格に影響があります。 詳細については、[料金に関するページ](https://azure.microsoft.com/pricing/details/cosmos-db/)をご覧ください。

次のコードをコピーし、**GetStartedDemo** メソッドに貼り付けます。データベースを作成する処理のすぐ下に追加してください。 このコードによって、*FamilyCollection_oa* というドキュメント コレクションが作成されます。

```csharp
    this.client = new DocumentClient(new Uri(EndpointUri), PrimaryKey);

    await this.client.CreateDatabaseIfNotExistsAsync(new Database { Id = "FamilyDB_oa" });

    // ADD THIS PART TO YOUR CODE
    await this.client.CreateDocumentCollectionIfNotExistsAsync(UriFactory.CreateDatabaseUri("FamilyDB_oa"), new DocumentCollection { Id = "FamilyCollection_oa" });
```

**[DocumentDBGettingStarted]** ボタンを選択して、アプリケーションを実行します。

## <a id="CreateDoc"></a>JSON ドキュメントを作成する

**DocumentClient** クラスの `CreateDocumentAsync` メソッドを使用してドキュメントを作成します。 ドキュメントは、ユーザー定義の (ユーザーが自由に定義できる) JSON コンテンツです。 ここで 1 つ以上のドキュメントを挿入できます。 

まず、Azure Cosmos DB 内に格納されるオブジェクトを表す **Family** クラスを作成します。 さらに、**Family** 内で使用するサブクラスとして、**Parent**、**Child**、**Pet**、**Address** を作成します。 ドキュメントには、JSON の **id** としてシリアル化される **Id** プロパティが必要です。 **GetStartedDemo** メソッドの後に次の内部サブクラスを追加することで、これらのクラスを作成します。 **Family**、**Parent**、**Child**、**Pet**、**Address** の各クラスをコピーし、**WriteToConsoleAndPromptToContinue** メソッドの下に貼り付けます。

```csharp
private void WriteToConsoleAndPromptToContinue(string format, params object[] args)
{
    Console.WriteLine(format, args);
    Console.WriteLine("Press any key to continue ...");
    Console.ReadKey();
}

// ADD THIS PART TO YOUR CODE
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

**CreateFamilyDocumentIfNotExists** メソッドをコピーし、**CreateDocumentCollectionIfNotExists** メソッドの下に貼り付けます。

```csharp
// ADD THIS PART TO YOUR CODE
private async Task CreateFamilyDocumentIfNotExists(string databaseName, string collectionName, Family family)
{
    try
    {
        await this.client.ReadDocumentAsync(UriFactory.CreateDocumentUri(databaseName, collectionName, family.Id));
        this.WriteToConsoleAndPromptToContinue("Found {0}", family.Id);
    }
    catch (DocumentClientException de)
    {
        if (de.StatusCode == HttpStatusCode.NotFound)
        {
            await this.client.CreateDocumentAsync(UriFactory.CreateDocumentCollectionUri(databaseName, collectionName), family);
            this.WriteToConsoleAndPromptToContinue("Created Family {0}", family.Id);
        }
        else
        {
            throw;
        }
    }
}
```

さらに、2 つのドキュメントを挿入します。1 つは Andersen Family のドキュメント、もう 1 つは Wakefield Family のドキュメントです。 `// ADD THIS PART TO YOUR CODE` に続くコードをコピーし、**GetStartedDemo** メソッドのドキュメント コレクションの作成処理の下に貼り付けます。

```csharp
    await this.client.CreateDatabaseIfNotExistsAsync(new Database { Id = "FamilyDB_oa" });

await this.CreateDocumentCollectionIfNotExists("FamilyDB_oa", "FamilyCollection_oa");

// ADD THIS PART TO YOUR CODE
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

await this.CreateFamilyDocumentIfNotExists("FamilyDB_oa", "FamilyCollection_oa", andersenFamily);

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

await this.CreateFamilyDocumentIfNotExists("FamilyDB_oa", "FamilyCollection_oa", wakefieldFamily);
```

**[DocumentDBGettingStarted]** ボタンを選択して、アプリケーションを実行します。

![アカウント、オンライン データベース、コレクションの間の階層関係](./media/sql-api-dotnetcore-get-started/nosql-tutorial-account-database.png)

## <a id="Query"></a>Azure Cosmos DB リソースにクエリを実行する

Azure Cosmos DB では、各コレクションに格納された JSON ドキュメントに対する豊富なクエリがサポートされています。 次のサンプル コードは、前の手順で挿入したドキュメントに対して実行できる、さまざまなクエリを示しています。Azure Cosmos DB SQL 構文と LINQ の両方が使用されています。

**ExecuteSimpleQuery** メソッドをコピーし、**CreateFamilyDocumentIfNotExists** メソッドの下に貼り付けます。

```csharp
// ADD THIS PART TO YOUR CODE
private void ExecuteSimpleQuery(string databaseName, string collectionName)
{
    // Set some common query options
    FeedOptions queryOptions = new FeedOptions { MaxItemCount = -1 };

        // Here we find the Andersen family via its LastName
        IQueryable<Family> familyQuery = this.client.CreateDocumentQuery<Family>(
                UriFactory.CreateDocumentCollectionUri(databaseName, collectionName), queryOptions)
                .Where(f => f.LastName == "Andersen");

        // The query is executed synchronously here, but can also be executed asynchronously via the IDocumentQuery<T> interface
        Console.WriteLine("Running LINQ query...");
        foreach (Family family in familyQuery)
        {
            Console.WriteLine("\tRead {0}", family);
        }

        // Now execute the same query via direct SQL
        IQueryable<Family> familyQueryInSql = this.client.CreateDocumentQuery<Family>(
                UriFactory.CreateDocumentCollectionUri(databaseName, collectionName),
                "SELECT * FROM Family WHERE Family.LastName = 'Andersen'",
                queryOptions);

        Console.WriteLine("Running direct SQL query...");
        foreach (Family family in familyQueryInSql)
        {
                Console.WriteLine("\tRead {0}", family);
        }

        Console.WriteLine("Press any key to continue ...");
        Console.ReadKey();
}
```

次のコードをコピーし、**GetStartedDemo** メソッドに貼り付けます。2 つ目のドキュメント作成処理のすぐ下に追加してください。

```csharp
await this.CreateFamilyDocumentIfNotExists("FamilyDB_oa", "FamilyCollection_oa", wakefieldFamily);

// ADD THIS PART TO YOUR CODE
this.ExecuteSimpleQuery("FamilyDB_oa", "FamilyCollection_oa");
```

**[DocumentDBGettingStarted]** ボタンを選択して、アプリケーションを実行します。

次の図は、作成したコレクションに対して Azure Cosmos DB SQL クエリ構文がどのように呼び出されるかを示しています。 同じロジックが LINQ クエリに適用されます。

![C# コンソール アプリケーションを作成するために NoSQL チュートリアルで使用されるクエリの範囲と意味を示す図](./media/sql-api-dotnetcore-get-started/nosql-tutorial-collection-documents.png)

Azure Cosmos DB クエリのスコープは既に 1 つのコレクションに設定されているので、クエリでは `FROM` キーワードを省略できます。 したがって、"FROM Families f" は "FROM root r" または他の任意の変数名に置き換えることができます。 Azure Cosmos DB は、Families、root、または任意の変数名が、既定で現在のコレクションを参照しているものと推測します。

## <a id="ReplaceDocument"></a>JSON ドキュメントを置換する

Azure Cosmos DB は、JSON ドキュメントの置換をサポートします。  

**ReplaceFamilyDocument** メソッドをコピーし、**ExecuteSimpleQuery** メソッドの下に貼り付けます。

```csharp
// ADD THIS PART TO YOUR CODE
private async Task ReplaceFamilyDocument(string databaseName, string collectionName, string familyName, Family updatedFamily)
{
    await this.client.ReplaceDocumentAsync(UriFactory.CreateDocumentUri(databaseName, collectionName, familyName), updatedFamily);
    this.WriteToConsoleAndPromptToContinue("Replaced Family {0}", familyName);
}
```

次のコードをコピーし、**GetStartedDemo** メソッドに貼り付けます。クエリを実行する処理のすぐ下に追加してください。 ドキュメントを置換した後に、同じクエリが実行され、変更されたドキュメントが表示されます。

```csharp
await this.CreateFamilyDocumentIfNotExists("FamilyDB_oa", "FamilyCollection_oa", wakefieldFamily);

this.ExecuteSimpleQuery("FamilyDB_oa", "FamilyCollection_oa");

// ADD THIS PART TO YOUR CODE
// Update the Grade of the Andersen Family child
andersenFamily.Children[0].Grade = 6;

await this.ReplaceFamilyDocument("FamilyDB_oa", "FamilyCollection_oa", "Andersen.1", andersenFamily);

this.ExecuteSimpleQuery("FamilyDB_oa", "FamilyCollection_oa");
```

**[DocumentDBGettingStarted]** ボタンを選択して、アプリケーションを実行します。

## <a id="DeleteDocument"></a>JSON ドキュメントを削除する

Azure Cosmos DB は、JSON ドキュメントの削除をサポートします。  

**DeleteFamilyDocument** メソッドをコピーし、**ReplaceFamilyDocument** メソッドの下に貼り付けます。

```csharp
// ADD THIS PART TO YOUR CODE
private async Task DeleteFamilyDocument(string databaseName, string collectionName, string documentName)
{
    await this.client.DeleteDocumentAsync(UriFactory.CreateDocumentUri(databaseName, collectionName, documentName));
    Console.WriteLine("Deleted Family {0}", documentName);
}
```

次のコードをコピーし、**GetStartedDemo** メソッドに貼り付けます。2 回目のクエリ実行のすぐ下に追加してください。

```csharp
await this.ReplaceFamilyDocument("FamilyDB_oa", "FamilyCollection_oa", "Andersen.1", andersenFamily);

this.ExecuteSimpleQuery("FamilyDB_oa", "FamilyCollection_oa");

// ADD THIS PART TO CODE
await this.DeleteFamilyDocument("FamilyDB_oa", "FamilyCollection_oa", "Andersen.1");
```

**[DocumentDBGettingStarted]** ボタンを選択して、アプリケーションを実行します。

## <a id="DeleteDatabase"></a>データベースを削除する

作成したデータベースを削除すると、データベースとすべての子リソース (コレクション、ドキュメントなど) が削除されます。 次のコードをコピーしてドキュメントの下の **GetStartedDemo** メソッドに貼り付け、データベース全体とすべての子リソースを削除します。

```csharp
this.ExecuteSimpleQuery("FamilyDB_oa", "FamilyCollection_oa");

await this.DeleteFamilyDocument("FamilyDB_oa", "FamilyCollection_oa", "Andersen.1");

// ADD THIS PART TO CODE
// Clean up/delete the database
await this.client.DeleteDatabaseAsync(UriFactory.CreateDatabaseUri("FamilyDB_oa"));
```

**[DocumentDBGettingStarted]** ボタンを選択して、アプリケーションを実行します。

## <a id="Run"></a>C# コンソール アプリケーションを実行する

Visual Studio で **[DocumentDBGettingStarted]** ボタンを選択して、アプリケーションをデバッグ モードでビルドします。 開始したアプリケーションの出力がコンソール ウィンドウに表示されます。 出力では追加したクエリの結果が表示されます。次の例のようなものになるはずです。

```bash
Created FamilyDB_oa
Press any key to continue ...
Created FamilyCollection_oa
Press any key to continue ...
Created Family Andersen.1
Press any key to continue ...
Created Family Wakefield.7
Press any key to continue ...
Running LINQ query...
    Read {"id":"Andersen.1","LastName":"Andersen","District":"WA5","Parents":[{"FamilyName":null,"FirstName":"Thomas"},{"FamilyName":null,"FirstName":"Mary Kay"}],"Children":[{"FamilyName":null,"FirstName":"Henriette Thaulow","Gender":"female","Grade":5,"Pets":[{"GivenName":"Fluffy"}]}],"Address":{"State":"WA","County":"King","City":"Seattle"},"IsRegistered":true}
Running direct SQL query...
    Read {"id":"Andersen.1","LastName":"Andersen","District":"WA5","Parents":[{"FamilyName":null,"FirstName":"Thomas"},{"FamilyName":null,"FirstName":"Mary Kay"}],"Children":[{"FamilyName":null,"FirstName":"Henriette Thaulow","Gender":"female","Grade":5,"Pets":[{"GivenName":"Fluffy"}]}],"Address":{"State":"WA","County":"King","City":"Seattle"},"IsRegistered":true}
Replaced Family Andersen.1
Press any key to continue ...
Running LINQ query...
    Read {"id":"Andersen.1","LastName":"Andersen","District":"WA5","Parents":[{"FamilyName":null,"FirstName":"Thomas"},{"FamilyName":null,"FirstName":"Mary Kay"}],"Children":[{"FamilyName":null,"FirstName":"Henriette Thaulow","Gender":"female","Grade":6,"Pets":[{"GivenName":"Fluffy"}]}],"Address":{"State":"WA","County":"King","City":"Seattle"},"IsRegistered":true}
Running direct SQL query...
    Read {"id":"Andersen.1","LastName":"Andersen","District":"WA5","Parents":[{"FamilyName":null,"FirstName":"Thomas"},{"FamilyName":null,"FirstName":"Mary Kay"}],"Children":[{"FamilyName":null,"FirstName":"Henriette Thaulow","Gender":"female","Grade":6,"Pets":[{"GivenName":"Fluffy"}]}],"Address":{"State":"WA","County":"King","City":"Seattle"},"IsRegistered":true}
Deleted Family Andersen.1
End of demo, press any key to exit.
```

これでチュートリアルが完了し、実際に動作する C# コンソール アプリケーションが完成しました。

## <a name="clean-up-resources"></a>リソースのクリーンアップ

必要がなくなったら、リソース グループ、Azure Cosmos アカウント、およびすべての関連リソースを削除できます。 これを行うには、仮想マシン用のリソース グループを選択し、**[削除]** を選択した後、削除するリソース グループの名前を確認します。

## <a name="next-steps"></a>次の手順

このチュートリアルでは、Azure Cosmos DB の SQL API アカウントに保存されているデータを管理する .NET Core アプリを構築する方法について説明しました。 次の記事に進むことができます。

> [!div class="nextstepaction"]
> [Azure Cosmos DB の SQL API アカウントを使用して Java コンソール アプリを構築する](sql-api-java-get-started.md)

[create-sql-api-dotnet.md#create-account]: create-sql-api-dotnet.md#create-account
[keys]: media/sql-api-dotnetcore-get-started/nosql-tutorial-keys.png
