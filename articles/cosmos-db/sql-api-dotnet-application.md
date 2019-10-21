---
title: Azure Cosmos DB 向けの ASP.NET Core MVC チュートリアル:Web アプリケーション開発
description: Azure Cosmos DB を使用して MVC Web アプリケーションを作成するための ASP.NET Core MVC チュートリアル。 JSON を格納し、Azure App Service 上でホストされている ToDo アプリからデータにアクセスします - ASP.NET Core MVC チュートリアル ステップ バイ ステップ。
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: dotnet
ms.topic: tutorial
ms.date: 09/24/2019
ms.author: sngun
ms.openlocfilehash: abff58be25a23c783f476dc849e0d6fa97e9eed2
ms.sourcegitcommit: 29880cf2e4ba9e441f7334c67c7e6a994df21cfe
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/26/2019
ms.locfileid: "71299320"
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
>
> * Azure Cosmos アカウントを作成する
> * ASP.NET Core MVC アプリを作成する
> * Azure Cosmos DB にアプリを接続する
> * データに対して作成、読み取り、更新、および削除 (CRUD) 操作を実行する

> [!TIP]
> このチュートリアルでは、ASP.NET Core MVC と Azure App Service の使用経験がある読者を想定しています。 ASP.NET Core や[前提条件となるツール](#prerequisites)を初めて扱う場合は、[GitHub][GitHub] から完成したサンプル プロジェクトをダウンロードし、必要な NuGet パッケージを追加して実行することをお勧めします。 プロジェクトをビルドした後でこの記事を見直すと、プロジェクトのコンテキストのコードについての洞察を得ることができます。

## <a name="prerequisites"></a>前提条件

この記事の手順を実行する前に、以下のリソースがあることを確認してください。

* アクティブな Azure アカウントアカウントがない場合、Azure 試用版にサインアップして、最大 10 件の無料 Mobile Apps を入手できます。 Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。

  [!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

* Visual Studio 2019。 [!INCLUDE [cosmos-db-emulator-vs](../../includes/cosmos-db-emulator-vs.md)]  

この記事のすべてのスクリーンショットは、Microsoft Visual Studio Community 2019 のものです。 別のバージョンを使用すると、画面とオプションが完全に一致しなくなる可能性があります。 このソリューションは、前提条件を満たしている場合に機能します。

## <a name="create-an-azure-cosmos-account"></a>手順 1:Azure Cosmos アカウントを作成する

まず Azure Cosmos アカウントを作成します。 Azure Cosmos DB SQL API アカウントが既にある場合、または Azure Cosmos DB エミュレーターを使用する場合は、スキップして「[手順 2: 新しい ASP.NET MVC アプリケーションを作成する](#create-a-new-mvc-application)」に進みます。

[!INCLUDE [create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

[!INCLUDE [keys](../../includes/cosmos-db-keys.md)]

次のセクションでは、新しい ASP.NET Core MVC アプリケーションを作成します。

## <a name="create-a-new-mvc-application"></a>手順 2:新しい ASP.NET Core MVC アプリケーションを作成する

1. Visual Studio を開き、 **[新しいプロジェクトの作成]** を選択します。

1. **[新しいプロジェクトの作成]** で、C# の **[ASP.NET Core Web アプリケーション]** を探して選択します。 **[次へ]** をクリックして続行します。

   ![新しい ASP.NET Core Web アプリケーション プロジェクトを作成する](./media/sql-api-dotnet-application/asp-net-mvc-tutorial-new-project-dialog.png)

1. **[新しいプロジェクトの構成]** で、プロジェクトに *todo* という名前を付けて、 **[作成]** を選択します。

1. **[新しい ASP.NET Core Web アプリケーションの作成]** で、 **[Web アプリケーション (Model-View-Controller)]** を選択します。 **[作成]** をクリックして続行します。

   Visual Studio で空の MVC アプリケーションが作成されます。

1. **[デバッグ]**  >  **[デバッグの開始]** を選択するか F5 キーを押して、ASP.NET アプリケーションをローカルで実行します。

## <a name="add-nuget-packages"></a>手順 3:Azure Cosmos DB NuGet パッケージをプロジェクトに追加する

これで、このソリューションに必要な ASP.NET Core MVC フレームワーク コードのほとんどを準備できました。次は Azure Cosmos DB に接続するために必要な NuGet パッケージを追加しましょう。

1. **ソリューション エクスプローラー**で、プロジェクトを右クリックし、 **[NuGet パッケージの管理]** を選択します。

1. **[NuGet パッケージ マネージャー]** で、**Microsoft.Azure.Cosmos** を検索して選択します。 **[インストール]** を選択します。

   ![NuGet パッケージのインストール](./media/sql-api-dotnet-application/asp-net-mvc-tutorial-nuget.png)

   Visual Studio により、Azure Cosmos DB パッケージとその依存関係がダウンロードされ、インストールされます。

   **パッケージ マネージャー コンソール**を使用して NuGet パッケージをインストールすることもできます。 そのためには、 **[ツール]**  >  **[NuGet パッケージ マネージャー]**  >  **[パッケージ マネージャー コンソール]** の順に選択します。 プロンプトで、次のコマンドを入力します。

   ```ps
   Install-Package Microsoft.Azure.Cosmos
   ```
  
## <a name="set-up-the-mvc-application"></a>手順 4:ASP.NET Core MVC アプリケーションをセットアップする

次に、モデル、ビュー、およびコントローラーをこの MVC アプリケーションに追加してみましょう。

### <a name="add-a-model"></a> モデルを追加する

1. **ソリューション エクスプローラー**で、**Models** フォルダーを右クリックし、 **[追加]**  >  **[クラス]** を選択します。

1. **[新しい項目の追加]** で、新しいクラスに *Item.cs* という名前を付けて、 **[追加]** を選択します。

1. *Item.cs* クラスの内容を次のコードに置き換えます。

   [!code-csharp[Main](~/samples-cosmosdb-dotnet-core-web-app/src/Models/Item.cs)]

Azure Cosmos DB では、データの移動と格納に JSON が使用されます。 `JsonProperty` 属性を使って、JSON でのオブジェクトのシリアル化と逆シリアル化の方法を制御できます。 `Item` クラスは、`JsonProperty` 属性を示します。 このコードによって、JSON に入るプロパティ名の形式が制御されます。 また、.NET プロパティ `Completed` の名前が変更されます。

### <a name="add-views"></a>ビューを追加する

次は、次の 3 つのビューを作成しましょう。

* 項目を一覧表示するためのビューを追加する
* 新しい項目を作成するためのビューを追加する
* 項目を編集するためのビューを追加する

#### <a name="AddItemIndexView"></a>リスト項目ビューを追加する

1. **ソリューション エクスプローラー**で、**Views** フォルダーを右クリックし、 **[追加]**  >  **[新しいフォルダー]** を選択します。 フォルダーに *Item* という名前を付けます。

1. 空の **Item** フォルダーを右クリックし、 **[追加]**  >  **[ビュー]** を選択します。

1. **[Add MVC View]\(MVC ビューの追加\)** で、次の値を指定します。

   * **[ビュー名]** で、「*Index*」と入力します。
   * **[テンプレート]** で、 **[List]** を選択します。
   * **[モデル クラス]** で、 **[Item (todo.Models)]** を選択します。
   * **[Use a layout page]\(レイアウト ページを使用する\)** をオンにして、「 *~/Views/Shared/_Layout.cshtml*」と入力します。

   ![[Add MVC View]\(MVC ビューの追加\) ダイアログ ボックスのスクリーンショット](./media/sql-api-dotnet-application/asp-net-mvc-tutorial-add-mvc-view.png)

1. これらの値を追加して **[追加]** を選択すると、Visual Studio で新しいテンプレート ビューが作成されます。

完了すると、Visual Studio によって作成された *cshtml* ファイルが開きます。 Visual Studio でそのファイルを閉じることができます。 これは後で使用します。

#### <a name="AddNewIndexView"></a>新しい項目を作成するためのビューを追加する

項目を一覧表示するビューの作成方法と同様に、次の手順を使用して項目を作成する新しいビューを作成します。

1. **ソリューション エクスプローラー**で、**Item** フォルダーをもう一度右クリックし、 **[追加]**  >  **[ビュー]** を選択します。

1. **[Add MVC View]\(MVC ビューの追加\)** で、次の変更を行います。

   * **[ビュー名]** で、「*Create*」と入力します。
   * **[テンプレート]** で、 **[Create]** を選択します。
   * **[モデル クラス]** で、 **[Item (todo.Models)]** を選択します。
   * **[Use a layout page]\(レイアウト ページを使用する\)** をオンにして、「 *~/Views/Shared/_Layout.cshtml*」と入力します。
   * **[追加]** を選択します。

#### <a name="AddEditIndexView"></a>項目を編集するためのビューを追加する

最後に、次の手順で項目を編集するビューを追加します。

1. **ソリューション エクスプローラー**で、**Item** フォルダーをもう一度右クリックし、 **[追加]**  >  **[ビュー]** を選択します。

1. **[Add MVC View]\(MVC ビューの追加\)** で、次の変更を行います。

   * **[ビュー名]** ボックスに、「*Edit*」と入力します。
   * **[テンプレート]** ボックスで、 **[編集]** を選択します。
   * **[モデル クラス]** ボックスで、 **[Item (todo.Models)]** を選択します。
   * **[Use a layout page]\(レイアウト ページを使用する\)** をオンにして、「 *~/Views/Shared/_Layout.cshtml*」と入力します。
   * **[追加]** を選択します。

これらの手順が完了したら、Visual Studio に表示されている *cshtml* ドキュメントをすべて閉じてください。これらのビューは後で使用します。

### <a name="add-a-controller"></a>コントローラーを追加する

1. **ソリューション エクスプローラー**で、**Controllers** フォルダーを右クリックし、 **[追加]**  >  **[コントローラー]** を選択します。

1. **[スキャフォールディングの追加]** で **[MVC コントローラー - 空]** を選択し、 **[追加]** を選択します。

   ![[スキャフォールディングの追加] で [MVC コントローラー - 空] を選択する](./media/sql-api-dotnet-application/asp-net-mvc-tutorial-controller-add-scaffold.png)

1. 新しいコントローラーに *ItemController* という名前を付けます。

1. *ItemController.cs* の内容を次のコードに置き換えます。

   [!code-csharp[Main](~/samples-cosmosdb-dotnet-core-web-app/src/Controllers/ItemController.cs)]

**ValidateAntiForgeryToken** 属性は、クロスサイト リクエスト フォージェリ攻撃に対してこのアプリケーションを保護するためにここで使用されます。 ビューもまた、この偽造防止トークンで使用されます。 詳細と例については、「[ASP.NET MVC アプリケーションでのクロスサイト リクエスト フォージェリ (CSRF) 攻撃の防止][Preventing Cross-Site Request Forgery]」をご覧ください。 [GitHub][GitHub] で提供されるソース コードには、完全な実装が組み込まれています。

メソッド パラメーターの **Bind** 属性も使用して、オーバーポスティング攻撃から保護します。 詳細については、[チュートリアル: ASP.NET MVC の Entity Framework を使用して CRUD 機能を実装する][Basic CRUD Operations in ASP.NET MVC]」を参照してください。

## <a name="connect-to-cosmosdb"></a>手順 5: Azure Cosmos DB への接続

MVC の標準的な構成要素を準備できたので、次は Azure Cosmos DB に接続するコードを追加し、CRUD 操作を実行しましょう。

### <a name="perform-crud-operations"></a>データに対して CRUD 操作を実行する

最初に、Azure Cosmos DB に接続して使用するためのロジックを含むクラスを追加します。 このチュートリアルでは、このロジックを `CosmosDBService` というクラス、および `ICosmosDBService` というインターフェイス内にカプセル化します。 このサービスによって、CRUD 操作が実行されます。 また、不完全な項目の一覧表示、項目の作成、編集、削除などのフィード読み取り操作も実行されます。

1. **ソリューション エクスプローラー**で、プロジェクトを右クリックして **[追加]**  >  **[新しいフォルダー]** を選択します。 フォルダーに *Services* という名前を付けます。

1. **Services** フォルダー右クリックし、 **[追加]**  >  **[クラス]** を選択します。 新しいクラスに *CosmosDBService* という名前を付け、 **[追加]** を選択します。

1. *CosmosDBService.cs* の内容を次のコードに置き換えます。

   [!code-csharp[Main](~/samples-cosmosdb-dotnet-core-web-app/src/Services/CosmosDbService.cs)]

1. 前の 2 つの手順を繰り返しますが、今回は *ICosmosDBService* という名前を使用し、次のコードを使用します。

   [!code-csharp[Main](~/samples-cosmosdb-dotnet-core-web-app/src/Services/ICosmosDbService.cs)]

1. **ConfigureServices** ハンドラーに、次の行を追加します。

    ```csharp
    services.AddSingleton<ICosmosDbService>(InitializeCosmosClientInstanceAsync(Configuration.GetSection("CosmosDb")).GetAwaiter().GetResult());
    ```

    前の手順のコードは、コンストラクターの一部として `CosmosClient` を受け取ります。 ASP.NET Core パイプラインに従って、プロジェクトの *Startup.cs* ファイルにアクセスする必要があります。 この手順のコードでは、構成に基づいて、[ASP.NET Core の依存関係挿入](https://docs.microsoft.com/aspnet/core/fundamentals/dependency-injection)を通じて挿入されるシングルトン インスタンスとして、クライアントを初期化します。

1. 同じファイル内で、次のメソッド **InitializeCosmosClientInstanceAsync** を追加します。このメソッドにより、構成が読み取られ、クライアントが初期化されます。

    [!code-csharp[](~/samples-cosmosdb-dotnet-core-web-app/src/Startup.cs?name=InitializeCosmosClientInstanceAsync)]

1. この構成は、プロジェクトの *appsettings.json* ファイル内で定義します。 ファイルを開き、**CosmosDb** という名前のセクションを追加します。

   ```csharp
     "CosmosDb": {
        "Account": "<enter the URI from the Keys blade of the Azure Portal>",
        "Key": "<enter the PRIMARY KEY, or the SECONDARY KEY, from the Keys blade of the Azure  Portal>",
        "DatabaseName": "Tasks",
        "ContainerName": "Items"
      }
   ```

アプリケーションを実行すると、ASP.NET Core のパイプラインによって **CosmosDbService** がインスタンス化され、単一のインスタンスがシングルトンとして保持されます。 **ItemController** によってクライアント側の要求が処理されると、この単一のインスタンスが受信され、これが CRUD 操作に使用されます。

このプロジェクトをビルドして実行すると、次のように表示されます。

![このデータベース チュートリアルで作成された、ToDo リスト Web アプリケーションのスクリーンショット](./media/sql-api-dotnet-application/build-and-run-the-project-now.png)

## <a name="run-the-application"></a>手順 6: ローカルでアプリケーションを実行する

ローカル コンピューターでアプリケーションをテストするには、次の手順を実行します。

1. Visual Studio で F5 キーを押して、デバッグ モードでアプリケーションをビルドします。 すると、アプリケーションがビルドされてブラウザーが起動し、先ほど見た空のグリッド ページが表示されます。

   ![このチュートリアルで作成された、ToDo リスト Web アプリケーションのスクリーンショット](./media/sql-api-dotnet-application/asp-net-mvc-tutorial-create-an-item-a.png)

1. **[Create New]** リンクをクリックし、 **[Name]** フィールドと **[Description]** フィールドに値を追加します。 **[Completed]** チェック ボックスはオフのままにします。 これを選択すると、アプリによって新しい項目が完了状態で追加されます。 この項目は、初期の一覧に表示されなくなります。

1. **作成** を選択します。 アプリによって **[Index]** ビューにリダイレクトされ、追加した項目が一覧に表示されます。 **To-Do** リストに他にもいくつか項目を追加してみてください。

    ![[Index] ビューのスクリーンショット](./media/sql-api-dotnet-application/asp-net-mvc-tutorial-create-an-item.png)
  
1. 一覧の**項目**の横にある **[Edit]** を選択します。 **[Edit]** ビューが表示され、対象オブジェクトのプロパティを更新することができます。**Completed** フラグもこのビューで更新できます。 **[Completed]** を選択して **[Save]** を選択した場合、アプリでは一覧の**項目**が完了として表示されます。

   ![[Completed] ボックスがオンになっている [Index] ビューのスクリーンショット](./media/sql-api-dotnet-application/asp-net-mvc-tutorial-completed-item.png)

1. Azure Cosmos DB サービス内で [Cosmos Explorer](https://cosmos.azure.com) または Azure Cosmos DB エミュレーターのデータ エクスプローラーを使用して、データの状態を検証します。

1. アプリケーションのテストが完了したら、Ctrl キーを押しながら F5 キーを押してアプリケーションのデバッグを中止します。 これで、アプリケーションをデプロイする準備が整いました。

## <a name="deploy-the-application-to-azure"></a>手順 7: アプリケーションのデプロイ

以上で、Azure Cosmos DB と連携するアプリケーションが完成しました。今度は、この Web アプリを Azure App Service にデプロイします。  

1. このアプリケーションを発行するために、**ソリューション エクスプローラー**でプロジェクトを右クリックし、 **[発行]** を選択します。

1. **[発行先を選択]** で、 **[App Service]** を選択します。

1. 既存の App Service プロファイルを使用するには、 **[既存のものを選択]** を選んで **[発行]** を選択します。

1. **[App Service]** で、 **[サブスクリプション]** を選択します。 **[表示]** フィルターを使用して、リソース グループまたはリソースの種類ごとに並べ替えます。

1. 自分のプロファイルを見つけて **[OK]** を選択します。 次に、必要な Azure App Service を検索し、 **[OK]** を選択します。

   ![Visual Studio の [App Service] ダイアログ ボックス](./media/sql-api-dotnet-application/asp-net-mvc-tutorial-app-service-2019.png)

別の方法として、新しいプロファイルを作成することもできます。

1. 前の手順と同様に、**ソリューション エクスプローラー**で、プロジェクトを右クリックし、 **[発行]** を選択します。
  
1. **[発行先を選択]** で、 **[App Service]** を選択します。

1. **[発行先を選択]** で、 **[新規作成]** を選択して **[発行]** を選択します。

1. **[App Service]** で、Web アプリ名と適切なサブスクリプション、リソース グループ、およびホスティング プランを入力し、 **[作成]** を選択します。

   ![Visual Studio の [App Service の作成] ダイアログ ボックス](./media/sql-api-dotnet-application/asp-net-mvc-tutorial-create-app-service-2019.png)

数秒すると、Visual Studio で Web アプリケーションが発行され、ブラウザーが起動されます。ここで、プロジェクトが Azure で実行されている様子を確認できます。

## <a name="next-steps"></a>次の手順

このチュートリアルでは、ASP.NET Core MVC Web アプリケーションを構築する方法について説明しました。 このアプリケーションから Azure Cosmos DB に格納されているデータにアクセスできます。 これで、次のリソースに進めるようになりました。

* [Azure Cosmos DB でのパーティション分割](./partitioning-overview.md)
* [SQL クエリの使用を開始する](./how-to-sql-query.md)
* [現実の例を使用して Azure Cosmos DB のデータをモデル化およびパーティション分割する方法](./how-to-model-partition-example.md)

[Visual Studio Express]: https://www.visualstudio.com/products/visual-studio-express-vs.aspx
[Microsoft Web Platform Installer]: https://www.microsoft.com/web/downloads/platform.aspx
[Preventing Cross-Site Request Forgery]: https://docs.microsoft.com/aspnet/web-api/overview/security/preventing-cross-site-request-forgery-csrf-attacks
[Basic CRUD Operations in ASP.NET MVC]: https://go.microsoft.com/fwlink/?LinkId=317598
[GitHub]: https://github.com/Azure-Samples/cosmos-dotnet-core-todo-app
