---
title: Azure Cosmos DB を使用した ASP.NET Core MVC Web アプリのチュートリアル
description: Azure Cosmos DB を使用して MVC Web アプリケーションを作成するための ASP.NET Core MVC チュートリアル。 JSON を格納し、Azure App Service 上でホストされている ToDo アプリからデータにアクセスします - ASP.NET Core MVC チュートリアル ステップ バイ ステップ。
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: dotnet
ms.topic: tutorial
ms.date: 05/08/2020
ms.author: sngun
ms.custom: devx-track-dotnet
ms.openlocfilehash: 528cab915a1ac3918146e428e9ae6b3c401324c8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "96010359"
---
# <a name="tutorial-develop-an-aspnet-core-mvc-web-application-with-azure-cosmos-db-by-using-net-sdk"></a>チュートリアル:Azure Cosmos DB で .NET SDK を使用して ASP.NET Core MVC Web アプリケーションを開発する
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

> [!div class="op_single_selector"]
> * [.NET](sql-api-dotnet-application.md)
> * [Java](sql-api-java-application.md)
> * [Node.js](sql-api-nodejs-application.md)
> * [Python](./create-sql-api-python.md)
> * [Xamarin](mobile-apps-with-xamarin.md)

このチュートリアルでは、Azure Cosmos DB を使用してデータを保存し、Azure でホストされている ASP.NET MVC アプリケーションからアクセスする方法について説明します。 このチュートリアルでは、.NET SDK V3 を使用します。 次の図は、この記事のサンプルを使用してビルドする Web ページを示しています。

:::image type="content" source="./media/sql-api-dotnet-application/asp-net-mvc-tutorial-image01.png" alt-text="このチュートリアルで作成された、ToDo リスト MVC Web アプリケーションのスクリーンショット - ASP NET Core MVC チュートリアル ステップ バイ ステップ":::

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

## <a name="prerequisites"></a><a name="prerequisites"></a>前提条件

この記事の手順を実行する前に、以下のリソースがあることを確認してください。

* アクティブな Azure アカウントアカウントがない場合、Azure 試用版にサインアップして、最大 10 件の無料 Mobile Apps を入手できます。 Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。

  [!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

* Visual Studio 2019。 [!INCLUDE [cosmos-db-emulator-vs](../../includes/cosmos-db-emulator-vs.md)]  

この記事のすべてのスクリーンショットは、Microsoft Visual Studio Community 2019 のものです。 別のバージョンを使用すると、画面とオプションが完全に一致しなくなる可能性があります。 このソリューションは、前提条件を満たしている場合に機能します。

## <a name="step-1-create-an-azure-cosmos-account"></a><a name="create-an-azure-cosmos-account"></a>手順 1:Azure Cosmos アカウントを作成する

まず Azure Cosmos アカウントを作成します。 Azure Cosmos DB SQL API アカウントが既にある場合、または Azure Cosmos DB エミュレーターを使用する場合は、スキップして「[手順 2: 新しい ASP.NET MVC アプリケーションを作成する](#create-a-new-mvc-application)」に進みます。

[!INCLUDE [create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

[!INCLUDE [keys](../../includes/cosmos-db-keys.md)]

次のセクションでは、新しい ASP.NET Core MVC アプリケーションを作成します。

## <a name="step-2-create-a-new-aspnet-core-mvc-application"></a><a name="create-a-new-mvc-application"></a>手順 2:新しい ASP.NET Core MVC アプリケーションを作成する

1. Visual Studio を開き、 **[新しいプロジェクトの作成]** を選択します。

1. **[新しいプロジェクトの作成]** で、C# の **[ASP.NET Core Web アプリケーション]** を探して選択します。 **[次へ]** をクリックして続行します。

   :::image type="content" source="./media/sql-api-dotnet-application/asp-net-mvc-tutorial-new-project-dialog.png" alt-text="新しい ASP.NET Core Web アプリケーション プロジェクトを作成する":::

1. **[新しいプロジェクトの構成]** で、プロジェクトに *todo* という名前を付けて、 **[作成]** を選択します。

1. **[新しい ASP.NET Core Web アプリケーションの作成]** で、 **[Web アプリケーション (Model-View-Controller)]** を選択します。 **[作成]** をクリックして続行します。

   Visual Studio で空の MVC アプリケーションが作成されます。

1. **[デバッグ]**  >  **[デバッグの開始]** を選択するか F5 キーを押して、ASP.NET アプリケーションをローカルで実行します。

## <a name="step-3-add-azure-cosmos-db-nuget-package-to-the-project"></a><a name="add-nuget-packages"></a>手順 3:Azure Cosmos DB NuGet パッケージをプロジェクトに追加する

これで、このソリューションに必要な ASP.NET Core MVC フレームワーク コードのほとんどを準備できました。次は Azure Cosmos DB に接続するために必要な NuGet パッケージを追加しましょう。

1. **ソリューション エクスプローラー** で、プロジェクトを右クリックし、 **[NuGet パッケージの管理]** を選択します。

1. **[NuGet パッケージ マネージャー]** で、**Microsoft.Azure.Cosmos** を検索して選択します。 **[インストール]** を選択します。

   :::image type="content" source="./media/sql-api-dotnet-application/asp-net-mvc-tutorial-nuget.png" alt-text="NuGet パッケージのインストール":::

   Visual Studio により、Azure Cosmos DB パッケージとその依存関係がダウンロードされ、インストールされます。

   **パッケージ マネージャー コンソール** を使用して NuGet パッケージをインストールすることもできます。 そのためには、 **[ツール]**  >  **[NuGet パッケージ マネージャー]**  >  **[パッケージ マネージャー コンソール]** の順に選択します。 プロンプトで、次のコマンドを入力します。

   ```ps
   Install-Package Microsoft.Azure.Cosmos
   ```
  
## <a name="step-4-set-up-the-aspnet-core-mvc-application"></a><a name="set-up-the-mvc-application"></a>手順 4:ASP.NET Core MVC アプリケーションをセットアップする

次に、モデル、ビュー、およびコントローラーをこの MVC アプリケーションに追加してみましょう。

### <a name="add-a-model"></a><a name="add-a-model"></a> モデルを追加する

1. **ソリューション エクスプローラー** で、**Models** フォルダーを右クリックし、 **[追加]**  >  **[クラス]** を選択します。

1. **[新しい項目の追加]** で、新しいクラスに *Item.cs* という名前を付けて、 **[追加]** を選択します。

1. *Item.cs* クラスの内容を次のコードに置き換えます。

   :::code language="csharp" source="~/samples-cosmosdb-dotnet-core-web-app/src/Models/Item.cs":::

Azure Cosmos DB では、データの移動と格納に JSON が使用されます。 `JsonProperty` 属性を使って、JSON でのオブジェクトのシリアル化と逆シリアル化の方法を制御できます。 `Item` クラスは、`JsonProperty` 属性を示します。 このコードによって、JSON に入るプロパティ名の形式が制御されます。 また、.NET プロパティ `Completed` の名前が変更されます。

### <a name="add-views"></a><a name="add-views"></a>ビューを追加する

次に、次のビューを追加します。

* 項目の作成ビュー
* 項目の削除ビュー
* 項目の詳細を取得するビュー
* 項目の編集ビュー
* すべての項目を一覧表示するビュー

#### <a name="create-item-view"></a><a name="AddNewIndexView"></a>項目の作成ビュー

1. **ソリューション エクスプローラー** で、**Views** フォルダーを右クリックし、 **[追加]**  >  **[新しいフォルダー]** を選択します。 フォルダーに *Item* という名前を付けます。

1. 空の **Item** フォルダーを右クリックし、 **[追加]**  >  **[ビュー]** を選択します。

1. **[Add MVC View]\(MVC ビューの追加\)** で、次の変更を行います。

   * **[ビュー名]** で、「*Create*」と入力します。
   * **[テンプレート]** で、 **[Create]** を選択します。
   * **[モデル クラス]** で、 **[Item (todo.Models)]** を選択します。
   * **[Use a layout page]\(レイアウト ページを使用する\)** をオンにして、「 *~/Views/Shared/_Layout.cshtml*」と入力します。
   * **[追加]** を選択します。

   :::image type="content" source="./media/sql-api-dotnet-application/asp-net-mvc-tutorial-add-mvc-view.png" alt-text="[Add MVC View]\(MVC ビューの追加\) ダイアログ ボックスのスクリーンショット":::

1. 次に **[追加]** を選択すると、Visual Studio で新しいテンプレート ビューが作成されます。 生成されたファイル内のコードを次の内容に置き換えます。

   :::code language="csharp" source="~/samples-cosmosdb-dotnet-core-web-app/src/Views/Item/Create.cshtml":::

#### <a name="delete-item-view"></a><a name="AddEditIndexView"></a>項目の削除ビュー

1. **ソリューション エクスプローラー** で、**Item** フォルダーをもう一度右クリックし、 **[追加]**  >  **[ビュー]** を選択します。

1. **[Add MVC View]\(MVC ビューの追加\)** で、次の変更を行います。

   * **[ビュー名]** ボックスに「*Delete*」と入力します。
   * **[テンプレート]** ボックスで、 **[Delete]** を選択します。
   * **[モデル クラス]** ボックスで、 **[Item (todo.Models)]** を選択します。
   * **[Use a layout page]\(レイアウト ページを使用する\)** をオンにして、「 *~/Views/Shared/_Layout.cshtml*」と入力します。
   * **[追加]** を選択します。

1. 次に **[追加]** を選択すると、Visual Studio で新しいテンプレート ビューが作成されます。 生成されたファイル内のコードを次の内容に置き換えます。

   :::code language="csharp" source="~/samples-cosmosdb-dotnet-core-web-app/src/Views/Item/Delete.cshtml":::

#### <a name="add-a-view-to-get-an-item-details"></a><a name="AddItemIndexView"></a>項目の詳細を取得するビューを追加する

1. **ソリューション エクスプローラー** で、**Item** フォルダーをもう一度右クリックし、 **[追加]**  >  **[ビュー]** を選択します。

1. **[Add MVC View]\(MVC ビューの追加\)** で、次の値を指定します。

   * **[ビュー名]** に、「*Details*」と入力します。
   * **[テンプレート]** で、 **[Details]** を選択します。
   * **[モデル クラス]** で、 **[Item (todo.Models)]** を選択します。
   * **[Use a layout page]\(レイアウト ページを使用する\)** をオンにして、「 *~/Views/Shared/_Layout.cshtml*」と入力します。

1. 次に **[追加]** を選択すると、Visual Studio で新しいテンプレート ビューが作成されます。 生成されたファイル内のコードを次の内容に置き換えます。

   :::code language="csharp" source="~/samples-cosmosdb-dotnet-core-web-app/src/Views/Item/Details.cshtml":::

#### <a name="add-an-edit-item-view"></a><a name="AddEditIndexView"></a>項目を編集するためのビューを追加する

1. **ソリューション エクスプローラー** で、**Item** フォルダーをもう一度右クリックし、 **[追加]**  >  **[ビュー]** を選択します。

1. **[Add MVC View]\(MVC ビューの追加\)** で、次の変更を行います。

   * **[ビュー名]** ボックスに、「*Edit*」と入力します。
   * **[テンプレート]** ボックスで、 **[編集]** を選択します。
   * **[モデル クラス]** ボックスで、 **[Item (todo.Models)]** を選択します。
   * **[Use a layout page]\(レイアウト ページを使用する\)** をオンにして、「 *~/Views/Shared/_Layout.cshtml*」と入力します。
   * **[追加]** を選択します。

1. 次に **[追加]** を選択すると、Visual Studio で新しいテンプレート ビューが作成されます。 生成されたファイル内のコードを次の内容に置き換えます。

   :::code language="csharp" source="~/samples-cosmosdb-dotnet-core-web-app/src/Views/Item/Edit.cshtml":::

#### <a name="add-a-view-to-list-all-the-items"></a><a name="AddEditIndexView"></a>すべての項目を一覧表示するビューを追加する

最後に、次の手順ですべての項目を取得するビューを追加します。

1. **ソリューション エクスプローラー** で、**Item** フォルダーをもう一度右クリックし、 **[追加]**  >  **[ビュー]** を選択します。

1. **[Add MVC View]\(MVC ビューの追加\)** で、次の変更を行います。

   * **[ビュー名]** ボックスに、「*Index*」と入力します。
   * **[テンプレート]** ボックスで、**[一覧]** を選択します。
   * **[モデル クラス]** ボックスで、 **[Item (todo.Models)]** を選択します。
   * **[Use a layout page]\(レイアウト ページを使用する\)** をオンにして、「 *~/Views/Shared/_Layout.cshtml*」と入力します。
   * **[追加]** を選択します。

1. 次に **[追加]** を選択すると、Visual Studio で新しいテンプレート ビューが作成されます。 生成されたファイル内のコードを次の内容に置き換えます。

   :::code language="csharp" source="~/samples-cosmosdb-dotnet-core-web-app/src/Views/Item/Index.cshtml":::

これらの手順が完了したら、Visual Studio ですべての *cshtml* ドキュメントを閉じます。

### <a name="declare-and-initialize-services"></a><a name="initialize-services"></a>サービスを宣言して初期化する

最初に、Azure Cosmos DB に接続して使用するためのロジックを含むクラスを追加します。 このチュートリアルでは、このロジックを `CosmosDbService` というクラス、および `ICosmosDbService` というインターフェイス内にカプセル化します。 このサービスによって、CRUD 操作が実行されます。 また、不完全な項目の一覧表示、項目の作成、編集、削除などのフィード読み取り操作も実行されます。

1. **ソリューション エクスプローラー** で、プロジェクトを右クリックして **[追加]**  >  **[新しいフォルダー]** を選択します。 フォルダーに *Services* という名前を付けます。

1. **Services** フォルダー右クリックし、 **[追加]**  >  **[クラス]** を選択します。 新しいクラスに *CosmosDbService* という名前を付け、 **[追加]** を選択します。

1. *CosmosDbService.cs* の内容を次のコードに置き換えます。

   :::code language="csharp" source="~/samples-cosmosdb-dotnet-core-web-app/src/Services/CosmosDbService.cs":::

1. **Services** フォルダー右クリックし、 **[追加]**  >  **[クラス]** を選択します。 新しいクラスに *ICosmosDbService* という名前を付け、 **[追加]** を選択します。

1. 次のコードを *ICosmosDbService* クラスに追加します。

   :::code language="csharp" source="~/samples-cosmosdb-dotnet-core-web-app/src/Services/ICosmosDbService.cs":::

1. ソリューションの *Startup.cs* ファイルを開き、次の **InitializeCosmosClientInstanceAsync** メソッドを追加します。これは、構成を読み取ってクライアントを初期化するメソッドです。

   :::code language="csharp" source="~/samples-cosmosdb-dotnet-core-web-app/src/Startup.cs" id="InitializeCosmosClientInstanceAsync" :::

1. 同じファイルの `ConfigureServices` メソッドを次のように置き換えます。

   :::code language="csharp" source="~/samples-cosmosdb-dotnet-core-web-app/src/Startup.cs" id="ConfigureServices":::

   この手順のコードでは、構成に基づいて、[ASP.NET Core の依存関係挿入](/aspnet/core/fundamentals/dependency-injection)を通じて挿入されるシングルトン インスタンスとして、クライアントを初期化します。

   さらに、既定の MVC コントローラーを `Item` に変更します。同じファイルの `Configure` メソッドで次のようにルートを編集してください。

   ```csharp
    app.UseEndpoints(endpoints =>
          {
                endpoints.MapControllerRoute(
                   name: "default",
                   pattern: "{controller=Item}/{action=Index}/{id?}");
          });
   ```


1. 次のスニペットに示すように、プロジェクトの *appsettings.json* ファイルで構成を定義します。

   :::code language="json" source="~/samples-cosmosdb-dotnet-core-web-app/src/appsettings.json":::

### <a name="add-a-controller"></a><a name="add-a-controller"></a>コントローラーを追加する

1. **ソリューション エクスプローラー** で、**Controllers** フォルダーを右クリックし、 **[追加]**  >  **[コントローラー]** を選択します。

1. **[スキャフォールディングの追加]** で **[MVC コントローラー - 空]** を選択し、 **[追加]** を選択します。

   :::image type="content" source="./media/sql-api-dotnet-application/asp-net-mvc-tutorial-controller-add-scaffold.png" alt-text="[スキャフォールディングの追加] で [MVC コントローラー - 空] を選択する":::

1. 新しいコントローラーに *ItemController* という名前を付けます。

1. *ItemController.cs* の内容を次のコードに置き換えます。

   :::code language="csharp" source="~/samples-cosmosdb-dotnet-core-web-app/src/Controllers/ItemController.cs":::

**ValidateAntiForgeryToken** 属性は、クロスサイト リクエスト フォージェリ攻撃に対してこのアプリケーションを保護するためにここで使用されます。 ビューもまた、この偽造防止トークンで使用されます。 詳細と例については、「[ASP.NET MVC アプリケーションでのクロスサイト リクエスト フォージェリ (CSRF) 攻撃の防止][Preventing Cross-Site Request Forgery]」をご覧ください。 [GitHub][GitHub] で提供されるソース コードには、完全な実装が組み込まれています。

メソッド パラメーターの **Bind** 属性も使用して、オーバーポスティング攻撃から保護します。 詳細については、「[チュートリアル:ASP.NET MVC の Entity Framework を使用して CRUD 機能を実装する][Basic CRUD Operations in ASP.NET MVC]」を参照してください。

## <a name="step-5-run-the-application-locally"></a><a name="run-the-application"></a>手順 5: ローカルでアプリケーションを実行する

ローカル コンピューターでアプリケーションをテストするには、次の手順を実行します。

1. Visual Studio で F5 キーを押して、デバッグ モードでアプリケーションをビルドします。 すると、アプリケーションがビルドされてブラウザーが起動し、先ほど見た空のグリッド ページが表示されます。

   :::image type="content" source="./media/sql-api-dotnet-application/asp-net-mvc-tutorial-create-an-item-a.png" alt-text="このチュートリアルで作成された、ToDo リスト Web アプリケーションのスクリーンショット":::
   
   アプリケーションでホーム ページを開くようにするには、URL に `/Item` を追加します。

1. **[Create New]** リンクをクリックし、 **[Name]** フィールドと **[Description]** フィールドに値を追加します。 **[Completed]** チェック ボックスはオフのままにします。 これを選択すると、アプリによって新しい項目が完了状態で追加されます。 この項目は、初期の一覧に表示されなくなります。

1. **［作成］** を選択します アプリによって **[Index]** ビューにリダイレクトされ、追加した項目が一覧に表示されます。 **To-Do** リストに他にもいくつか項目を追加してみてください。

    :::image type="content" source="./media/sql-api-dotnet-application/asp-net-mvc-tutorial-create-an-item.png" alt-text="[Index] ビューのスクリーンショット":::
  
1. 一覧の **項目** の横にある **[Edit]** を選択します。 **[Edit]** ビューが表示され、対象オブジェクトのプロパティを更新することができます。**Completed** フラグもこのビューで更新できます。 **[Completed]** を選択して **[Save]** を選択した場合、アプリでは一覧の **項目** が完了として表示されます。

   :::image type="content" source="./media/sql-api-dotnet-application/asp-net-mvc-tutorial-completed-item.png" alt-text="[Completed] ボックスがオンになっている [Index] ビューのスクリーンショット":::

1. Azure Cosmos DB サービス内で [Cosmos Explorer](https://cosmos.azure.com) または Azure Cosmos DB エミュレーターのデータ エクスプローラーを使用して、データの状態を検証します。

1. アプリケーションのテストが完了したら、Ctrl キーを押しながら F5 キーを押してアプリケーションのデバッグを中止します。 これで、アプリケーションをデプロイする準備が整いました。

## <a name="step-6-deploy-the-application"></a><a name="deploy-the-application-to-azure"></a>手順 6: アプリケーションの配置

以上で、Azure Cosmos DB と連携するアプリケーションが完成しました。今度は、この Web アプリを Azure App Service にデプロイします。  

1. このアプリケーションを発行するために、**ソリューション エクスプローラー** でプロジェクトを右クリックし、 **[発行]** を選択します。

1. **[発行先を選択]** で、 **[App Service]** を選択します。

1. 既存の App Service プロファイルを使用するには、 **[既存のものを選択]** を選んで **[発行]** を選択します。

1. **[App Service]** で、 **[サブスクリプション]** を選択します。 **[表示]** フィルターを使用して、リソース グループまたはリソースの種類ごとに並べ替えます。

1. 自分のプロファイルを見つけて **[OK]** を選択します。 次に、必要な Azure App Service を検索し、 **[OK]** を選択します。

   :::image type="content" source="./media/sql-api-dotnet-application/asp-net-mvc-tutorial-app-service-2019.png" alt-text="Visual Studio の [App Service] ダイアログ ボックス":::

別の方法として、新しいプロファイルを作成することもできます。

1. 前の手順と同様に、**ソリューション エクスプローラー** で、プロジェクトを右クリックし、 **[発行]** を選択します。
  
1. **[発行先を選択]** で、 **[App Service]** を選択します。

1. **[発行先を選択]** で、 **[新規作成]** を選択して **[発行]** を選択します。

1. **[App Service]** で、Web アプリ名と適切なサブスクリプション、リソース グループ、およびホスティング プランを入力し、 **[作成]** を選択します。

   :::image type="content" source="./media/sql-api-dotnet-application/asp-net-mvc-tutorial-create-app-service-2019.png" alt-text="Visual Studio の [App Service の作成] ダイアログ ボックス":::

数秒すると、Visual Studio で Web アプリケーションが発行され、ブラウザーが起動されます。ここで、プロジェクトが Azure で実行されている様子を確認できます。

## <a name="next-steps"></a>次のステップ

このチュートリアルでは、ASP.NET Core MVC Web アプリケーションを構築する方法について説明しました。 このアプリケーションから Azure Cosmos DB に格納されているデータにアクセスできます。 これで、次のリソースに進めるようになりました。

* [Azure Cosmos DB でのパーティション分割](./partitioning-overview.md)
* [SQL クエリの使用を開始する](./sql-query-getting-started.md)
* [現実の例を使用して Azure Cosmos DB のデータをモデル化およびパーティション分割する方法](./how-to-model-partition-example.md)

[Visual Studio Express]: https://www.visualstudio.com/products/visual-studio-express-vs.aspx
[Microsoft Web Platform Installer]: https://www.microsoft.com/web/downloads/platform.aspx
[Preventing Cross-Site Request Forgery]: /aspnet/web-api/overview/security/preventing-cross-site-request-forgery-csrf-attacks
[Basic CRUD Operations in ASP.NET MVC]: /aspnet/mvc/overview/getting-started/getting-started-with-ef-using-mvc/implementing-basic-crud-functionality-with-the-entity-framework-in-asp-net-mvc-application
[GitHub]: https://github.com/Azure-Samples/cosmos-dotnet-core-todo-app
