---
title: チュートリアル:.NET コンソール アプリをビルドして Azure Cosmos DB SQL API アカウントのデータを管理する
description: チュートリアル:C# コンソール アプリケーションを使用して Azure Cosmos DB SQL API リソースを作成する方法について説明します。
author: kirankumarkolli
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: dotnet
ms.topic: tutorial
ms.date: 11/05/2019
ms.author: kirankk
ms.custom: devx-track-csharp
ms.openlocfilehash: 2ece9ed5cb6eb6ea706db1ed615a64d001f2b17c
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102423954"
---
# <a name="tutorial-build-a-net-console-app-to-manage-data-in-azure-cosmos-db-sql-api-account"></a>チュートリアル:.NET コンソール アプリをビルドして Azure Cosmos DB SQL API アカウントのデータを管理する
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

> [!div class="op_single_selector"]
> * [.NET](sql-api-get-started.md)
> * [Java](./create-sql-api-java.md)
> * [Async Java](./create-sql-api-java.md)
> * [Node.js](sql-api-nodejs-get-started.md)
>

Azure Cosmos DB SQL API を実際に使ってみるチュートリアルへようこそ。 このチュートリアルに従うことで、Azure Cosmos DB リソースを作成し、クエリするコンソール アプリケーションを準備することができます。

このチュートリアルでは、[Azure Cosmos DB .NET SDK](https://www.nuget.org/packages/Microsoft.Azure.Cosmos) のバージョン 3.0 以降を使用します。 [.NET Framework または .NET Core](https://dotnet.microsoft.com/download) を使用できます。

このチュートリアルの内容:

> [!div class="checklist"]
>
> * Azure Cosmos アカウントを作成し、アカウントに接続する
> * Visual Studio でプロジェクトを構成する
> * データベースとコンテナーを作成する
> * コンテナーに項目を追加する
> * コンテナーにクエリを実行する
> * 項目に対して作成、読み取り、更新、および削除 (CRUD) 操作を実行する
> * データベースを削除する

時間がなくても 心配はありません。 [GitHub](https://github.com/Azure-Samples/cosmos-dotnet-getting-started) で完全なソリューションを入手できます。 簡単な手順については、「[チュートリアル のソリューションの完全版を入手する](#GetSolution)」のセクションを参照してください。

それでは始めましょう。

## <a name="prerequisites"></a>前提条件

* アクティブな Azure アカウントアカウントがない場合、Azure 試用版にサインアップして、最大 10 件の無料 Mobile Apps を入手できます。 お持ちでない場合は、 [無料アカウント](https://azure.microsoft.com/free/)にサインアップしてください。

  [!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

* [!INCLUDE [cosmos-db-emulator-vs](../../includes/cosmos-db-emulator-vs.md)]

## <a name="step-1-create-an-azure-cosmos-db-account"></a>手順 1:Azure Cosmos DB アカウントを作成する

それでは、Azure Cosmos DB アカウントを作成してみましょう。 使用するアカウントが既にある場合は、このセクションをスキップします。 Azure Cosmos DB エミュレーターを使用するには、[Azure Cosmos DB エミュレーター](local-emulator.md)に関する記事に記載されている手順に従ってエミュレーターを設定してください。 その後、「[手順 2:Visual Studio プロジェクトをセットアップする](#SetupVS)」に進みます。

[!INCLUDE [create-dbaccount-preview](../../includes/cosmos-db-create-dbaccount.md)]

## <a name="step-2-set-up-your-visual-studio-project"></a><a id="SetupVS"></a>手順 2:Visual Studio プロジェクトをセットアップする

1. Visual Studio を開き、 **[新しいプロジェクトの作成]** を選択します。
1. **[新しいプロジェクトの作成]** で、C# の **[コンソール アプリ (.NET Framework)]** を選択してから、 **[次へ]** を選択します。
1. プロジェクトに *CosmosGettingStartedTutorial* という名前を付けて、 **[作成]** を選択します。

    :::image type="content" source="./media/sql-api-get-started/configure-cosmos-getting-started-2019.png" alt-text="プロジェクトを構成する":::

1. **ソリューション エクスプローラー** で、Visual Studio ソリューションの下にある新しいコンソール アプリケーションを右クリックし、 **[NuGet パッケージの管理]** を選択します。
1. **[NuGet パッケージ マネージャー]** で **[参照]** を選択し、*Microsoft.Azure.Cosmos* を検索します。 **[Microsoft.Azure.Cosmos]** を選択し、 **[インストール]** を選択します。

   :::image type="content" source="./media/sql-api-get-started/cosmos-getting-started-manage-nuget-2019.png" alt-text="Azure Cosmos DB クライアント SDK 用 NuGet のインストール":::

   Azure Cosmos DB SQL API クライアント ライブラリのパッケージ ID は [Microsoft Azure Cosmos DB クライアント ライブラリ](https://www.nuget.org/packages/Microsoft.Azure.Cosmos/) です。

これでセットアップは終了です。 いくつかのコードの記述を開始しましょう。 このチュートリアルの完全なプロジェクトについては、「[Azure Cosmos DB を使用した .NET コンソール アプリの開発](https://github.com/Azure-Samples/cosmos-dotnet-getting-started)」を参照してください。

## <a name="step-3-connect-to-an-azure-cosmos-db-account"></a><a id="Connect"></a>手順 3:Azure Cosmos DB アカウントに接続する

1. *Program.cs* ファイルで、C# アプリケーションの先頭にある参照を、以下の参照で置き換えます。

   ```csharp
   using System;
   using System.Threading.Tasks;
   using System.Configuration;
   using System.Collections.Generic;
   using System.Net;
   using Microsoft.Azure.Cosmos;
   ```

1. `Program` クラスに次の定数と変数を追加します。

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
        private Database database;

        // The container we will create.
        private Container container;

        // The name of the database and container we will create
        private string databaseId = "FamilyDatabase";
        private string containerId = "FamilyContainer";
    }
    ```

   > [!NOTE]
   > 以前のバージョンの .NET SDK に慣れている方は、"*コレクション*" や "*ドキュメント*" という用語をよく目にしたかと思います。 Azure Cosmos DB では複数の API モデルをサポートしているため、.NET SDK バージョン 3.0 では、"*コンテナー*" と "*項目*" という一般的な用語が使用されています。 "*コンテナー*" は、コレクション、グラフ、またはテーブルを表します。 "*項目*" は、ドキュメント、エッジ/頂点、行など、コンテナー内の内容を表します。 詳細については、「[Azure Cosmos DB のデータベース、コンテナー、項目の操作](account-databases-containers-items.md)」を参照してください。

1. [Azure Portal](https://portal.azure.com)を開きます。 ご使用の Azure Cosmos DB アカウントを探して、 **[キー]** を選択します。

   :::image type="content" source="./media/sql-api-get-started/cosmos-getting-started-portal-keys.png" alt-text="Azure portal から Azure Cosmos DB キーを取得する":::

1. *Program.cs* で、`<your endpoint URL>` を **URI** の値に置き換えます。 `<your primary key>` は **PRIMARY KEY** の値に置き換えます。

1. **Main** メソッドの下に、**GetStartedDemoAsync** という新しい非同期タスクを追加します。これによって新しい `CosmosClient` がインスタンス化されます。

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

    Azure Cosmos DB リソース上で実行するメソッドを呼び出すエントリ ポイントとして **GetStartedDemoAsync** を使用します。

1. 次のコードを追加して、**Main** メソッドから **GetStartedDemoAsync** 非同期タスクを実行します。 **Main** メソッドは例外をキャッチし、コンソールに書き込みます。

    [!code-csharp[](~/cosmos-dotnet-getting-started/CosmosGettingStartedTutorial/Program.cs?name=Main)]

1. F5 キーを押してアプリケーションを実行します。

    コンソールには、次のメッセージが表示されます。**End of demo, press any key to exit. (デモはこれで終わりです。終了するには何かキーを押してください。)** このメッセージによって、アプリケーションが Azure Cosmos DB に接続したことを確認できます。 表示されたら、コンソール ウィンドウを閉じます。

お疲れさまでした。 これで、Azure Cosmos DB アカウントに接続しました。

## <a name="step-4-create-a-database"></a>手順 4:データベースを作成する

データベースは、コンテナーに分割された項目の論理上のコンテナーです。 [CosmosClient](/dotnet/api/microsoft.azure.cosmos.cosmosclient) クラスの `CreateDatabaseIfNotExistsAsync` または `CreateDatabaseAsync` のいずれかのメソッドでデータベースを作成できます。

1. `CreateDatabaseAsync` メソッドをコピーし、`GetStartedDemoAsync` メソッドの下に貼り付けます。

    [!code-csharp[](~/cosmos-dotnet-getting-started/CosmosGettingStartedTutorial/Program.cs?name=CreateDatabaseAsync&highlight=7)]

    `CreateDatabaseAsync` によって、`databaseId` フィールドに指定された ID で新しいデータベース (ID `FamilyDatabase`) が作成されます (このデータベースがまだ存在していない場合)。

1. CosmosClient をインスタンス化したところに、次のコードをコピーして貼り付けて、追加した **CreateDatabaseAsync** メソッドを呼び出します。

    ```csharp
    public async Task GetStartedDemoAsync()
    {
        // Create a new instance of the Cosmos Client
        this.cosmosClient = new CosmosClient(EndpointUri, PrimaryKey);

        //ADD THIS PART TO YOUR CODE
        await this.CreateDatabaseAsync();
    }
    ```

    *Program.cs* は次のようになっているはずです (実際のエンドポイントとプライマリ キーが入力されています)。

    ```csharp
    using System;
    using System.Threading.Tasks;
    using System.Configuration;
    using System.Collections.Generic;
    using System.Net;
    using Microsoft.Azure.Cosmos;

    namespace CosmosGettingStartedTutorial
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
            private Database database;

            // The container we will create.
            private Container container;

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

            /// <summary>
            /// Entry point to call methods that operate on Azure Cosmos DB resources in this sample
            /// </summary>
            public async Task GetStartedDemoAsync()
            {
                // Create a new instance of the Cosmos Client
                this.cosmosClient = new CosmosClient(EndpointUri, PrimaryKey);
                await this.CreateDatabaseAsync();
            }

            /// <summary>
            /// Create the database if it does not exist
            /// </summary>
            private async Task CreateDatabaseAsync()
            {
                // Create a new database
                this.database = await this.cosmosClient.CreateDatabaseIfNotExistsAsync(databaseId);
                Console.WriteLine("Created Database: {0}\n", this.database.Id);
            }
        }
    }
    ```

1. F5 キーを押してアプリケーションを実行します。

   > [!NOTE]
   > "503 service unavailable exception" (503 サービス利用不可の例外) エラーが表示される場合は、直接接続モードに必要な[ポート](sql-sdk-connection-modes.md#service-port-ranges)がファイアウォールによってブロックされている可能性があります。 この問題を解決するには、次のコードに示すように、必要なポートを開くか、ゲートウェイ モード接続を使用します。
   ```csharp
     // Create a new instance of the Cosmos Client in Gateway mode
     this.cosmosClient = new CosmosClient(EndpointUri, PrimaryKey, new CosmosClientOptions()
            {
                ConnectionMode = ConnectionMode.Gateway
            });
   ```

お疲れさまでした。 これで、Azure Cosmos データベースが正常に作成されました。  

## <a name="step-5-create-a-container"></a><a id="CreateColl"></a>手順 5: コンテナーを作成する

> [!WARNING]
> メソッド `CreateContainerIfNotExistsAsync` によって、価格に影響する新しいコンテナーが作成されます。 詳細については、[価格のページ](https://azure.microsoft.com/pricing/details/cosmos-db/)を参照してください。
>
>

コンテナーは、`CosmosDatabase` クラスの [**CreateContainerIfNotExistsAsync**](/dotnet/api/microsoft.azure.cosmos.database.createcontainerifnotexistsasync#Microsoft_Azure_Cosmos_Database_CreateContainerIfNotExistsAsync_Microsoft_Azure_Cosmos_ContainerProperties_System_Nullable_System_Int32__Microsoft_Azure_Cosmos_RequestOptions_System_Threading_CancellationToken_) または [**CreateContainerAsync**](/dotnet/api/microsoft.azure.cosmos.database.createcontainerasync#Microsoft_Azure_Cosmos_Database_CreateContainerAsync_Microsoft_Azure_Cosmos_ContainerProperties_System_Nullable_System_Int32__Microsoft_Azure_Cosmos_RequestOptions_System_Threading_CancellationToken_) メソッドのいずれかを使用して作成できます。 コンテナーは、項目 (SQL API の場合は JSON ドキュメント) および関連する JavaScript サーバー側アプリケーション ロジック (ストアド プロシージャ、ユーザー定義関数、トリガーなど) で構成されます。

1. `CreateContainerAsync` メソッドをコピーし、`CreateDatabaseAsync` メソッドの下に貼り付けます。 `CreateContainerAsync` によって、`LastName` プロパティによってパーティション化されている `containerId` フィールドに指定された ID で新しいコンテナー (ID `FamilyContainer`) が作成されます (このコンテナーがまだ存在していない場合)。

    [!code-csharp[](~/cosmos-dotnet-getting-started/CosmosGettingStartedTutorial/Program.cs?name=CreateContainerAsync&highlight=9)]

1. CosmosClient をインスタンス化したところに、次のコードをコピーして貼り付けて、追加した **CreateContainer** メソッドを呼び出します。

    ```csharp
    public async Task GetStartedDemoAsync()
    {
        // Create a new instance of the Cosmos Client
        this.cosmosClient = new CosmosClient(EndpointUri, PrimaryKey);
        await this.CreateDatabaseAsync();

        //ADD THIS PART TO YOUR CODE
        await this.CreateContainerAsync();
    }
    ```

1. F5 キーを押してアプリケーションを実行します。

お疲れさまでした。 これで、Azure Cosmos コンテナーが作成されました。  

## <a name="step-6-add-items-to-the-container"></a><a id="CreateDoc"></a>手順 6: コンテナーに項目を追加する

`CosmosContainer` クラスの [**CreateItemAsync**](/dotnet/api/microsoft.azure.cosmos.container.createitemasync#Microsoft_Azure_Cosmos_Container_CreateItemAsync__1___0_System_Nullable_Microsoft_Azure_Cosmos_PartitionKey__Microsoft_Azure_Cosmos_ItemRequestOptions_System_Threading_CancellationToken_) メソッドで項目を作成できます。 SQL API を使用すると、項目はドキュメントとして投影されます。これは、ユーザー定義の任意の JSON コンテンツです。 これで、Azure Cosmos コンテナーに項目を挿入できます。

まず、このサンプルの Azure Cosmos DB 内に格納されるオブジェクトを表す `Family` クラスを作成しましょう。 また、`Family` 内で使用されるサブクラス `Parent`、`Child`、`Pet`、`Address` も作成します。 項目には、JSON で `id` としてシリアル化される `Id` プロパティが必要です。

1. Ctrl + Shift + A キーを押して **[新しい項目の追加]** を開きます。 新しいクラス `Family.cs` をプロジェクトに追加します。

    :::image type="content" source="./media/sql-api-get-started/cosmos-getting-started-add-family-class-2019.png" alt-text="プロジェクトに新しい Family.cs クラスを追加する画面のスクリーンショット":::

1. `Family`、`Parent`、`Child`、`Pet`、および `Address` クラスをコピーして `Family.cs` に貼り付けます。

    [!code-csharp[](~/cosmos-dotnet-getting-started/CosmosGettingStartedTutorial/Family.cs)]


1. *Program.cs* に戻り、`AddItemsToContainerAsync` メソッドを `CreateContainerAsync` メソッドの後に追加します。

    [!code-csharp[](~/cosmos-dotnet-getting-started/CosmosGettingStartedTutorial/Program.cs?name=AddItemsToContainerAsync)]


    コードによって、同じ ID を持つ項目が存在していないことが確認されます。 2 つの項目を挿入します。1 つは *Andersen Family* の項目、もう 1 つは *Wakefield Family* の項目です。

1. `GetStartedDemoAsync` メソッドに `AddItemsToContainerAsync` への呼び出しを追加します。

    ```csharp
    public async Task GetStartedDemoAsync()
    {
        // Create a new instance of the Cosmos Client
        this.cosmosClient = new CosmosClient(EndpointUri, PrimaryKey);
        await this.CreateDatabaseAsync();
        await this.CreateContainerAsync();

        //ADD THIS PART TO YOUR CODE
        await this.AddItemsToContainerAsync();
    }
    ```

1. F5 キーを押してアプリケーションを実行します。

お疲れさまでした。 これで、Azure Cosmos の項目が 2 つ作成されました。  

## <a name="step-7-query-azure-cosmos-db-resources"></a><a id="Query"></a>手順 7: Azure Cosmos DB リソースを照会する

Azure Cosmos DB では、各コンテナーに格納された JSON ドキュメントに対する豊富なクエリがサポートされています。 詳細については、「[SQL クエリの使用を開始する](./sql-query-getting-started.md)」をご覧ください。 次のサンプル コードを使用して、前の手順で挿入した項目に対してどのようにクエリを実行するかを説明します。

1. `QueryItemsAsync` メソッドをコピーし、`AddItemsToContainerAsync` メソッドの後に貼り付けます。

    [!code-csharp[](~/cosmos-dotnet-getting-started/CosmosGettingStartedTutorial/Program.cs?name=QueryItemsAsync&highlight=10-11,17-18)]

1. ``GetStartedDemoAsync`` メソッドに ``QueryItemsAsync`` への呼び出しを追加します。

    ```csharp
    public async Task GetStartedDemoAsync()
    {
        // Create a new instance of the Cosmos Client
        this.cosmosClient = new CosmosClient(EndpointUri, PrimaryKey);
        await this.CreateDatabaseAsync();
        await this.CreateContainerAsync();
        await this.AddItemsToContainerAsync();

        //ADD THIS PART TO YOUR CODE
        await this.QueryItemsAsync();
    }
    ```

1. F5 キーを押してアプリケーションを実行します。

お疲れさまでした。 これで、Azure Cosmos コンテナーに対するクエリが実行されました。

## <a name="step-8-replace-a-json-item"></a><a id="ReplaceItem"></a>手順 8: JSON 項目を置換する

ここでは、Azure Cosmos DB 内の項目を更新します。 `Family` の `IsRegistered` プロパティと子どもの 1 人の `Grade` を変更します。

1. `ReplaceFamilyItemAsync` メソッドをコピーし、`QueryItemsAsync` メソッドの後に貼り付けます。

    [!code-csharp[](~/cosmos-dotnet-getting-started/CosmosGettingStartedTutorial/Program.cs?name=ReplaceFamilyItemAsync&highlight=15)]

1. `GetStartedDemoAsync` メソッドに `ReplaceFamilyItemAsync` への呼び出しを追加します。

    ```csharp
    public async Task GetStartedDemoAsync()
    {
        // Create a new instance of the Cosmos Client
        this.cosmosClient = new CosmosClient(EndpointUri, PrimaryKey);
        await this.CreateDatabaseAsync();
        await this.CreateContainerAsync();
        await this.AddItemsToContainerAsync();
        await this.QueryItemsAsync();

        //ADD THIS PART TO YOUR CODE
        await this.ReplaceFamilyItemAsync();
    }
    ```

1. F5 キーを押してアプリケーションを実行します。

お疲れさまでした。 これで、Azure Cosmos 項目が置換されました。

## <a name="step-9-delete-item"></a><a id="DeleteDocument"></a>手順 9: アイテムを削除する

ここでは、Azure Cosmos DB 内の項目を削除します。

1. `DeleteFamilyItemAsync` メソッドをコピーし、`ReplaceFamilyItemAsync` メソッドの後に貼り付けます。

    [!code-csharp[](~/cosmos-dotnet-getting-started/CosmosGettingStartedTutorial/Program.cs?name=DeleteFamilyItemAsync&highlight=10)]

1. `GetStartedDemoAsync` メソッドに `DeleteFamilyItemAsync` への呼び出しを追加します。

    ```csharp
    public async Task GetStartedDemoAsync()
    {
        // Create a new instance of the Cosmos Client
        this.cosmosClient = new CosmosClient(EndpointUri, PrimaryKey);
        await this.CreateDatabaseAsync();
        await this.CreateContainerAsync();
        await this.AddItemsToContainerAsync();
        await this.QueryItemsAsync();
        await this.ReplaceFamilyItemAsync();

        //ADD THIS PART TO YOUR CODE
        await this.DeleteFamilyItemAsync();
    }
    ```

1. F5 キーを押してアプリケーションを実行します。

お疲れさまでした。 これで、Azure Cosmos 項目が削除されました。

## <a name="step-10-delete-the-database"></a><a id="DeleteDatabase"></a>手順 10: データベースを削除する

ここでは、データベースを削除します。 作成したデータベースを削除すると、データベースとすべての子リソースが削除されます。 リソースには、コンテナー、項目、ストアド プロシージャ、ユーザー定義関数、およびトリガーがあります。 また、`CosmosClient` インスタンスも破棄します。

1. `DeleteDatabaseAndCleanupAsync` メソッドをコピーし、`DeleteFamilyItemAsync` メソッドの後に貼り付けます。

    [!code-csharp[](~/cosmos-dotnet-getting-started/CosmosGettingStartedTutorial/Program.cs?name=DeleteDatabaseAndCleanupAsync)]

1. ``GetStartedDemoAsync`` メソッドに ``DeleteDatabaseAndCleanupAsync`` への呼び出しを追加します。

    [!code-csharp[](~/cosmos-dotnet-getting-started/CosmosGettingStartedTutorial/Program.cs?name=GetStartedDemoAsync&highlight=14)]

1. F5 キーを押してアプリケーションを実行します。

お疲れさまでした。 これで、Azure Cosmos データベースが削除されました。

## <a name="step-11-run-your-c-console-application-all-together"></a><a id="Run"></a>手順 11: C# コンソール アプリケーションの全体的な実行の流れ

Visual Studio で F5 キーを押すと、アプリケーションがビルドされデバッグ モードで実行します。

アプリケーション全体の出力がコンソール ウィンドウに表示されます。 出力には、追加したクエリの結果が表示されます。 それは、次のテキスト例のようなものになるはずです。

```cmd
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

## <a name="get-the-complete-tutorial-solution"></a><a id="GetSolution"></a>チュートリアルのソリューションの完全版を入手する

このチュートリアルの手順を実行する時間がない場合や、コード サンプルをダウンロードするだけの場合は、それをダウンロードできます。

`GetStarted` ソリューションを構築するには、次の前提条件を満たしておく必要があります。

* アクティブな Azure アカウントアカウントがない場合、Azure 試用版にサインアップして、最大 10 件の無料 Mobile Apps を入手できます。 お持ちでない場合は、 [無料アカウント](https://azure.microsoft.com/free/)にサインアップしてください。
* [Azure Cosmos DB アカウント][cosmos-db-create-account]。
* GitHub で入手可能な [GetStarted](https://github.com/Azure-Samples/cosmos-dotnet-getting-started) ソリューション。

Visual Studio で Azure Cosmos DB .NET SDK への参照を復元するには、**ソリューション エクスプローラー** でソリューションを右クリックし、 **[NuGet パッケージの復元]** を選択します。 次に、*App.config* ファイルで、`EndPointUri` と `PrimaryKey` の値を更新します。この手順については、「[手順 3:Azure Cosmos DB アカウントに接続する](#Connect)」を参照してください。

以上です。ビルドすれば完了です。

## <a name="next-steps"></a>次のステップ

* さらに詳しい ASP.NET MVC チュートリアルが必要な場合には、 「[チュートリアル:Azure Cosmos DB で .NET SDK を使用して ASP.NET Core MVC Web アプリケーションを開発する](sql-api-dotnet-application.md)」を参照してください。
* Azure Cosmos DB のスケーリングとパフォーマンスをテストする方法については、 「[Azure Cosmos DB のパフォーマンスとスケールのテスト](performance-testing.md)」を参照してください。
* Azure Cosmos DB の要求、使用状況、およびストレージを監視する方法については、「[Azure Cosmos DB でパフォーマンスとストレージのメトリックを監視する](./monitor-cosmos-db.md)」を参照してください。
* サンプル データセットに対してクエリを実行するには、「[Query Playground](https://www.documentdb.com/sql/demo)」を参照してください。
* Azure Cosmos DB の詳細については、「[Azure Cosmos DB の概要](./introduction.md)」を参照してください。

[cosmos-db-create-account]: create-sql-api-java.md#create-a-database-account