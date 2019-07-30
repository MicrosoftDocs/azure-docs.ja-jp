---
title: Azure Cosmos DB 向けの ASP.NET Core MVC チュートリアル:Web アプリケーション開発
description: Azure Cosmos DB を使用して MVC Web アプリケーションを作成するための ASP.NET Core MVC チュートリアル。 JSON を格納し、Azure App Service 上でホストされている ToDo アプリからデータにアクセスします - ASP.NET Core MVC チュートリアル ステップ バイ ステップ。
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: dotnet
ms.topic: tutorial
ms.date: 06/24/2019
ms.author: sngun
ms.openlocfilehash: 85d9cbe7d0807ca0e7951e1e12d1edbbf7c921db
ms.sourcegitcommit: 6b41522dae07961f141b0a6a5d46fd1a0c43e6b2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/15/2019
ms.locfileid: "67985829"
---
# <a name="tutorial-develop-an-aspnet-core-mvc-web-application-with-azure-cosmos-db-by-using-net-sdk"></a>チュートリアル:Azure Cosmos DB で .NET SDK を使用して ASP.NET Core MVC Web アプリケーションを開発する 

> [!div class="op_single_selector"]
> * [.NET](sql-api-dotnet-application.md)
> * [Java](sql-api-java-application.md)
> * [Node.js](sql-api-nodejs-application.md)
> * [Python](sql-api-python-application.md)
> * [Xamarin](mobile-apps-with-xamarin.md)


このチュートリアルでは、Azure Cosmos DB を使用してデータを保存し、Azure でホストされている ASP.NET MVC アプリケーションからアクセスする方法について説明します。 このチュートリアルでは、.NET SDK V3 を使用します。 次の図は、この記事のサンプルを使用してビルドする Web ページを示しています。
 
![このチュートリアルで作成された、ToDo リスト MVC Web アプリケーションのスクリーンショット - ASP NET Core MVC チュートリアル ステップ バイ ステップ](./media/sql-api-dotnet-application/asp-net-mvc-tutorial-image01.png)

チュートリアルを完了する時間がない場合は、完成したサンプル プロジェクトを [GitHub][GitHub] からダウンロードできます。

このチュートリアルの内容:

> [!div class="checklist"]
> * Azure Cosmos アカウントを作成する
> * ASP.NET Core MVC アプリを作成する
> * Azure Cosmos DB にアプリを接続する 
> * データに対して CRUD 操作を実行する

> [!TIP]
> このチュートリアルでは、ASP.NET Core MVC と Azure App Service の使用経験がある読者を想定しています。 ASP.NET Core や[前提条件となるツール](#prerequisites)を初めて扱う場合は、[GitHub][GitHub] から完成したサンプル プロジェクトをダウンロードし、必要な NuGet パッケージを追加して実行することをお勧めします。 プロジェクトをビルドした後でこの記事を見直すと、プロジェクトのコンテキストのコードについての洞察を得ることができます。

## <a name="prerequisites"></a>前提条件 

この記事の手順を実行する前に、以下のリソースがあることを確認してください。

* **アクティブな Azure アカウント:** Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。 

  [!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

* [!INCLUDE [cosmos-db-emulator-vs](../../includes/cosmos-db-emulator-vs.md)]  

この記事のすべてのスクリーンショットは、Microsoft Visual Studio Community 2019 を使用して取得されています。 ご利用のシステムに構成されているバージョンと異なる場合、画面やオプション設定が一部異なる可能性がありますが、上記の前提条件を満たしていれば、ソリューションの動作に支障はありません。

## <a name="create-an-azure-cosmos-account"></a>手順 1: Azure Cosmos アカウントを作成する

まず Azure Cosmos アカウントを作成します。 Azure Cosmos DB SQL API アカウントが既にある場合、またはこのチュートリアルで Azure Cosmos DB エミュレーターを使用する場合は、「[新しい ASP.NET MVC アプリケーションを作成する](#create-a-new-mvc-application)」セクションに進むことができます。

[!INCLUDE [create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

[!INCLUDE [keys](../../includes/cosmos-db-keys.md)]

次のセクションでは、新しい ASP.NET Core MVC アプリケーションを作成します。 

## <a name="create-a-new-mvc-application"></a>手順 2:新しい ASP.NET Core MVC アプリケーションを作成する

1. Visual Studio で **[ファイル]** メニューから **[新規作成]** 、 **[プロジェクト]** の順に選択します。 **[新しいプロジェクト]** ダイアログ ボックスが表示されます。

2. **[新しいプロジェクト]** ウィンドウで、 **[テンプレートの検索]** 入力ボックスを使用して "Web" を検索し、 **[ASP.NET Core Web アプリケーション]** を選択します。 

   ![新しい ASP.NET Core Web アプリケーション プロジェクトを作成する](./media/sql-api-dotnet-application/asp-net-mvc-tutorial-new-project-dialog.png)

3. **[名前]** ボックスに、プロジェクトの名前を入力します。 このチュートリアルでは、"todo" という名前を使用します。 これ以外の名前を使用する場合は、このチュートリアルで todo 名前空間について言及されているすべての場所で、提供されているコード サンプルを、ここでアプリケーションに対して指定した名前に変更します。 

4. **[参照]** を選択して、プロジェクトを作成するフォルダーに移動します。 **作成** を選択します。 

5. **[新しい ASP.NET Core Web アプリケーションの作成]** ダイアログ ボックスが表示されます。 テンプレートリストで **[Web アプリケーション (モデル ビュー コントローラー)]** を選択します。

6. **[作成]** を選択すると、Visual Studio で空の ASP.NET Core MVC テンプレートに対してスキャフォールディング機能が実行されます。 

7. Visual Studio によってスケルトン MVC アプリケーションが作成されると、空の ASP.NET アプリケーションをローカルに実行できる状態となります。

## <a name="add-nuget-packages"></a>手順 3: Azure Cosmos DB NuGet パッケージをプロジェクトに追加する

これで、このソリューションに必要な ASP.NET Core MVC フレームワーク コードのほとんどを準備できました。次は Azure Cosmos DB に接続するために必要な NuGet パッケージを追加しましょう。

1. Azure Cosmos DB .NET SDK は、NuGet パッケージの形式で配布されています。 Visual Studio で NuGet パッケージを取得するには、**ソリューション エクスプローラー**でプロジェクトを右クリックし、 **[NuGet パッケージの管理]** を選択して表示される Visual Studio の NuGet パッケージ マネージャーを使用します。
   
   ![[NuGet パッケージの管理] が強調表示されている、ソリューション エクスプローラーでの Web アプリケーション プロジェクトの右クリック オプションのスクリーンショット](./media/sql-api-dotnet-application/asp-net-mvc-tutorial-manage-nuget.png)
   
2. **[NuGet パッケージの管理]** ダイアログ ボックスが表示されます。 NuGet の **[参照]** ボックスに「**Microsoft.Azure.Cosmos**」と入力します。 結果から、**Microsoft.Azure.Cosmos** パッケージをインストールします。 これにより、Azure Cosmos DB パッケージとその依存関係がダウンロードされ、インストールされます。 **[ライセンスの同意]** ウィンドウで **[同意する]** を選択して、インストールを完了します。
   
   または、パッケージ マネージャー コンソールを使用して NuGet パッケージをインストールすることもできます。 これを実行するには、 **[ツール]** メニューの **[NuGet パッケージ マネージャー]** 、 **[パッケージ マネージャー コンソール]** を順に選択します。 プロンプトで、次のコマンドを入力します。
   
   ```bash
   Install-Package Microsoft.Azure.Cosmos
   ```        

3. パッケージがインストールされた後、Visual Studio プロジェクトに Microsoft.Azure.Cosmos へのライブラリ参照を含める必要があります。
  
## <a name="set-up-the-mvc-application"></a>手順 4:ASP.NET Core MVC アプリケーションをセットアップする

次に、モデル、ビュー、およびコントローラーをこの MVC アプリケーションに追加してみましょう。

* [モデルを追加します](#add-a-model)。
* [コントローラーを追加します](#add-a-controller)。
* [ビューを追加します](#add-views)。

### <a name="add-a-model"></a> モデルを追加する

1. **ソリューション エクスプローラー**から、**Models** フォルダーを右クリックし、 **[追加]** 、 **[クラス]** の順に選択します。 **[新しい項目の追加]** ダイアログ ボックスが表示されます。

1. 新しいクラスに **Item.cs** と名前を付け、 **[追加]** を選択します。 

1. "Item.cs" クラス内のコードを次のコードに置き換えます。

   [!code-csharp[Main](~/samples-cosmosdb-dotnet-core-web-app/src/Models/Item.cs)]
   
   Azure Cosmos DB に保存されているデータは、JSON 形式でネットワーク越しに渡され、保存されます。 JSON.NET によってオブジェクトをシリアル化または逆シリアル化する方法を制御するには、作成した **Item** クラスで示したとおり、**JsonProperty** 属性を使用します。 JSON に渡されるプロパティ名の形式を制御できるだけでなく、**Completed** プロパティに対して行ったように、.NET プロパティの名前を変更することもできます。 

### <a name="add-a-controller"></a>コントローラーを追加する

1. **ソリューション エクスプローラー**から、**Controllers** フォルダーを右クリックし、 **[追加]** 、 **[コントローラー]** の順に選択します。 **[スキャフォールディングの追加]** ダイアログ ボックスが表示されます。

1. **[MVC コントローラー - 空]** を選択し、 **[追加]** を選択します。

   ![[MVC コントローラー - 空] オプションが強調表示されている [スキャフォールディングの追加] ダイアログ ボックスのスクリーンショット](./media/sql-api-dotnet-application/asp-net-mvc-tutorial-controller-add-scaffold.png)

1. 新しいコントローラーに **ItemController** という名前を付け、そのファイル内のコードを次のコードに置き換えます。

   [!code-csharp[Main](~/samples-cosmosdb-dotnet-core-web-app/src/Controllers/ItemController.cs)]

   **ValidateAntiForgeryToken** 属性は、クロスサイト リクエスト フォージェリ攻撃に対してこのアプリケーションを保護するためにここで使用されます。 この属性を追加するだけでなく、偽造防止トークンもビューで処理する必要があります。 この詳細と正しい実装方法の例については、[クロスサイト リクエスト フォージェリの防止][Preventing Cross-Site Request Forgery]に関するページを参照してください。. The source code provided on [GitHub][GitHub]には、完全な実装が含まれています。

   メソッド パラメーターの **Bind** 属性も使用して、オーバーポスティング攻撃から保護します。 詳細については、[ASP.NET MVC での基本的な CRUD 操作][Basic CRUD Operations in ASP.NET MVC]に関するページを参照してください。

### <a name="add-views"></a>ビューを追加する

次は、次の 3 つのビューを作成しましょう。 

* [リスト項目ビューを追加する](#AddItemIndexView)。
* [新しい項目ビューを追加する](#AddNewIndexView)。
* [項目を編集するためのビューを追加する](#AddEditIndexView)。

#### <a name="AddItemIndexView"></a>リスト項目ビューを追加する

1. **ソリューション エクスプローラー**で、**Views** フォルダーを展開します。先ほど **ItemController** を追加したときに Visual Studio によって作成された空の **Item** フォルダーを右クリックし、 **[追加]** をクリックします。次に、 **[ビュー]** をクリックします。
   
   ![[ビューの追加] コマンドが強調表示された状態の、Visual Studio で作成された Item フォルダーが示されているソリューション エクスプローラーのスクリーンショット](./media/sql-api-dotnet-application/asp-net-mvc-tutorial-add-view.png)

2. **[ビューの追加]** ダイアログ ボックスで、次の値を更新します。
   
   * **[ビュー名]** ボックスに、「***Index***」と入力します。
   * **[テンプレート]** ボックスで、***[一覧]*** を選択します。
   * **[モデル クラス]** ボックスで、***[Item (todo.Models)]*** を選択します。
   * レイアウト ページ ボックスに、「***~/Views/Shared/_Layout.cshtml***」と入力します。
     
   ![[ビューの追加] ダイアログ ボックスのスクリーンショット](./media/sql-api-dotnet-application/asp-net-mvc-tutorial-add-view-dialog.png)

3. これらの値を追加して **[追加]** を選択すると、Visual Studio で新しいテンプレート ビューが作成されます。 完了すると、作成された cshtml ファイルが開きます。 Visual Studio でこのファイルは閉じて、後で再度使用することができます。

#### <a name="AddNewIndexView"></a>新しい項目を作成するためのビューを追加する

項目を一覧表示するビューの作成方法と同様に、次の手順を使用して項目を作成する新しいビューを作成します。

1. **ソリューション エクスプローラー**から、**Item** フォルダーをもう一度右クリックし、 **[追加]** 、 **[表示]** の順に選択します。

1. **[ビューの追加]** ダイアログ ボックスで、次の値を更新します。
   
   * **[ビュー名]** ボックスに、「***Create***」と入力します。
   * **[テンプレート]** ボックスで、***[作成]*** を選択します。
   * **[モデル クラス]** ボックスで、***[Item (todo.Models)]*** を選択します。
   * レイアウト ページ ボックスに、「***~/Views/Shared/_Layout.cshtml***」と入力します。
   * **[追加]** を選択します。
   
#### <a name="AddEditIndexView"></a>項目を編集するためのビューを追加する

最後に、次の手順で項目を編集するビューを追加します。

1. **ソリューション エクスプローラー**から、**Item** フォルダーをもう一度右クリックし、 **[追加]** 、 **[表示]** の順に選択します。

1. **[ビューの追加]** ダイアログ ボックスで、次の操作を行います。
   
   * **[ビュー名]** ボックスに、「***Edit***」と入力します。
   * **[テンプレート]** ボックスで、***[編集]*** を選択します。
   * **[モデル クラス]** ボックスで、***[Item (todo.Models)]*** を選択します。
   * レイアウト ページ ボックスに、「***~/Views/Shared/_Layout.cshtml***」と入力します。
   * **[追加]** を選択します。

この作業が済んだら、Visual Studio に表示されている cshtml ドキュメントをすべて閉じてください。これらのビューは後で使用します。

## <a name="connect-to-cosmosdb"></a>手順 5: Azure Cosmos DB への接続 

MVC の標準的な構成要素を準備できたので、次に Azure Cosmos DB に接続するコードを追加し、CRUD 操作を実行します。 

### <a name="perform-crud-operations"></a> データに対して CRUD 操作を実行する

最初に、Azure Cosmos DB に接続して使用するためのロジックを含むクラスを追加します。 このチュートリアルでは、このロジックを `CosmosDBService` というクラス、および `ICosmosDBService` というインターフェイス内にカプセル化します。 このサービスでは、不完全な項目の一覧表示、項目の作成、編集、削除などの CRUD 操作およびフィード読み取り操作が実行されます。 

1. **ソリューション エクスプローラー**から、**Services** というプロジェクトの下に新しいフォルダーを作成します。

1. **Services** フォルダー右クリックし、 **[追加]** を選択し、次に **[クラス]** を選択します。 新しいクラスに **CosmosDBService** という名前を付け、 **[追加]** を選択します。

1. 次のコードを **CosmosDBService** クラスに追加し、そのファイル内のコードを次のコードに置き換えます。

   [!code-csharp[Main](~/samples-cosmosdb-dotnet-core-web-app/src/Services/CosmosDbService.cs)]

1. 今度は **ICosmosDBService** という名前のクラスで手順 2-3 を繰り返し、次のコードを追加します。

   [!code-csharp[Main](~/samples-cosmosdb-dotnet-core-web-app/src/Services/ICosmosDbService.cs)]
 
1. 前のコードは、コンストラクターの一部として `CosmosClient` を受け取ります。 ASP.NET Core パイプラインに従ってプロジェクトの **Startup.cs** にアクセスし、設定に基づいて、[依存関係挿入](https://docs.microsoft.com/aspnet/core/fundamentals/dependency-injection)を通じて挿入されるシングルトン インスタンスとして、クライアントを初期化する必要があります。 **ConfigureServices**ハンドラー内で、次のように定義します。

    ```csharp
    services.AddSingleton<ICosmosDbService>(InitializeCosmosClientInstanceAsync(Configuration.GetSection("CosmosDb")).GetAwaiter().GetResult());
    ```

1. 同じファイル内で、ヘルパー メソッド **InitializeCosmosClientInstanceAsync** を定義します。このメソッドは、構成を読み取り、クライアントを初期化します。

    ```csharp
    private static async Task<CosmosDbService> InitializeCosmosClientInstanceAsync(IConfigurationSection configurationSection)
    {
        string databaseName = configurationSection.GetSection("DatabaseName").Value;
        string containerName = configurationSection.GetSection("ContainerName").Value;
        string account = configurationSection.GetSection("Account").Value;
        string key = configurationSection.GetSection("Key").Value;
        CosmosClientBuilder clientBuilder = new CosmosClientBuilder(account, key);
        CosmosClient client = clientBuilder
                            .WithConnectionModeDirect()
                            .Build();
        CosmosDbService cosmosDbService = new CosmosDbService(client, databaseName, containerName);
        Database database = await client.CreateDatabaseIfNotExistsAsync(databaseName);
        await database.CreateContainerIfNotExistsAsync(containerName, "/id");

        return cosmosDbService;
    }
    ```

1. この構成は、プロジェクトの **appsettings.json** ファイル内で定義されています。 それを開き、**CosmosDb** という名前のセクションを追加します。

   ```csharp
     "CosmosDb": {
        "Account": "<enter the URI from the Keys blade of the Azure Portal>",
        "Key": "<enter the PRIMARY KEY, or the SECONDARY KEY, from the Keys blade of the Azure  Portal>",
        "DatabaseName": "Tasks",
        "ContainerName": "Items"
      }
   ```
 
これで、アプリケーションを実行すると ASP.NET Core のパイプラインによって **CosmosDbService** がインスタンス化され、単一のインスタンスがシングルトンとして保持されます。**ItemController** を使用してクライアント側の要求を処理する場合は、この単一のインスタンスを受け取った後、それを使用して CRUD 操作が実行できるようになります。

このプロジェクトをビルドして実行すると、次のように表示されます。

![このデータベース チュートリアルで作成された、ToDo リスト Web アプリケーションのスクリーンショット](./media/sql-api-dotnet-application/build-and-run-the-project-now.png)


## <a name="run-the-application"></a>手順 6: ローカルでアプリケーションを実行する

ローカル コンピューターでアプリケーションをテストするには、次の手順を実行します。

1. Visual Studio で F5 キーを押して、デバッグ モードでアプリケーションをビルドします。 すると、アプリケーションがビルドされてブラウザーが起動し、先ほど見た空のグリッド ページが表示されます。
   
   ![このチュートリアルで作成された、ToDo リスト Web アプリケーションのスクリーンショット](./media/sql-api-dotnet-application/asp-net-mvc-tutorial-create-an-item-a.png)
       
2. **[Create New]** リンクをクリックし、 **[Name]** フィールドと **[Description]** フィールドに値を追加します。 **[完了済み]** チェック ボックスはオフのままにします。オンにした場合、新しい項目が完了済みの状態で追加されるため、最初の一覧に表示されません。
   
3. **[作成]** をクリックすると、 **[インデックス]** ビューにリダイレクトされ、追加した項目が一覧に表示されます。 Todo リストに他にもいくつか項目を追加してみてください。

    ![[Index] ビューのスクリーンショット](./media/sql-api-dotnet-application/asp-net-mvc-tutorial-create-an-item.png)
  
4. リストの **Item** の横にある **[Edit]** をクリックすると、 **[Edit]** ビューが表示され、対象オブジェクトのプロパティを更新することができます。**Completed** フラグもこのビューで更新できます。 **[完了]** フラグをマークして **[保存]** をクリックすると、その **[項目]** が完了したものとしてリストに表示されます。
   
   ![[Completed] ボックスがオンになっている [Index] ビューのスクリーンショット](./media/sql-api-dotnet-application/asp-net-mvc-tutorial-completed-item.png)

5. Azure Cosmos DB サービス内で [Cosmos Explorer](https://cosmos.azure.com) または Azure Cosmos DB Emulator のデータ エクスプローラーを使用すれば、データの状態をいつでも検査できます。

6. アプリケーションのテストが完了したら、Ctrl キーを押しながら F5 キーを押してアプリケーションのデバッグを中止します。 これで、アプリケーションをデプロイする準備が整いました。

## <a name="deploy-the-application-to-azure"></a>手順 7: アプリケーションのデプロイ 
以上で、Azure Cosmos DB と連携するアプリケーションが完成しました。今度は、この Web アプリを Azure App Service にデプロイします。  

1. アプリケーションを発行するために、**ソリューション エクスプローラー**のプロジェクトを右クリックし、 **[発行]** を選択します。
   
2. **[発行]** ダイアログ ボックスで、 **[App Service]** を選択します。次に、 **[新規作成]** を選択して App Service プロファイルを作成するか、 **[既存の選択]** を選択して既存のプロファイルを使用します。

3. 既存の Azure App Service プロファイルがある場合は、ドロップダウンから **[サブスクリプション]** を選択します。 **[表示]** フィルターを使用して、リソース グループまたはリソースの種類ごとに並べ替えます。 次に、必要な Azure App Service を検索し、 **[OK]** を選択します。
   
   ![Visual Studio の [App Service] ダイアログ ボックス](./media/sql-api-dotnet-application/asp-net-mvc-tutorial-app-service.png)

4. 新しい Azure App Service プロファイルを作成するには、 **[発行]** ダイアログ ボックスで **[新規作成]** をクリックします。 **[App Service の作成]** ダイアログで、Web アプリ名と適切なサブスクリプション、リソース グループ、および App Service プランを入力し、 **[作成]** を選択します。

   ![Visual Studio の [App Service の作成] ダイアログ ボックス](./media/sql-api-dotnet-application/asp-net-mvc-tutorial-create-app-service.png)

数秒すると、Visual Studio で Web アプリケーションが発行され、ブラウザーが起動されます。ここで、プロジェクトが Azure で実行されている様子を確認できます。

## <a name="next-steps"></a>次の手順
このチュートリアルでは、Azure Cosmos DB に格納されたデータにアクセスできる ASP.NET Core MVC Web アプリケーションを構築する方法について説明しました。 次の記事に進むことができます。

* [Azure Cosmos DB でのデータのパーティション分割について説明します。](./partitioning-overview.md)
* [Azure Cosmos DB でより高度なクエリを実行する方法について説明します。](./how-to-sql-query.md)
* [より高度なシナリオでデータをモデル化する方法について説明します。](./how-to-model-partition-example.md)


[Visual Studio Express]: https://www.visualstudio.com/products/visual-studio-express-vs.aspx
[Microsoft Web Platform Installer]: https://www.microsoft.com/web/downloads/platform.aspx
[Preventing Cross-Site Request Forgery]: https://go.microsoft.com/fwlink/?LinkID=517254
[Basic CRUD Operations in ASP.NET MVC]: https://go.microsoft.com/fwlink/?LinkId=317598
[GitHub]: https://github.com/Azure-Samples/cosmos-dotnet-core-todo-app
