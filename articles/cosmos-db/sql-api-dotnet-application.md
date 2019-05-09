---
title: Azure Cosmos DB 向けの ASP.NET MVC チュートリアル:Web アプリケーション開発
description: Azure Cosmos DB を使用して MVC Web アプリケーションを作成するための ASP.NET MVC チュートリアル。 JSON を格納し、Azure Websites でホストされている ToDo アプリからデータにアクセスします - ASP.NET MVC チュートリアル ステップ バイ ステップ
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: dotnet
ms.topic: tutorial
ms.date: 08/03/2017
ms.author: sngun
ms.openlocfilehash: 3f19c442d0f5806147ee05b3f0d2d32740a8ecdd
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2019
ms.locfileid: "58121742"
---
# <a name="_Toc395809351"></a>ASP.NET MVC のチュートリアル: Azure Cosmos DB を使用した Web アプリケーションの開発

> [!div class="op_single_selector"]
> * [.NET](sql-api-dotnet-application.md)
> * [Java](sql-api-java-application.md)
> * [Node.js](sql-api-nodejs-application.md)
> * [Python](sql-api-python-application.md)
> * [Xamarin](mobile-apps-with-xamarin.md)
> 

この記事では、Azure Cosmos DB を効果的に活用して、JSON ドキュメントの保存とクエリを行う方法を強調するために、ToDo アプリを Azure Cosmos DB を使って構築するエンド ツー エンドの手順を説明します。 対象となるタスクは、JSON ドキュメントとして Azure Cosmos DB に保存するものとします。

![このチュートリアルで作成された、ToDo リスト MVC Web アプリケーションのスクリーンショット - ASP NET MVC チュートリアル ステップ バイ ステップ](./media/sql-api-dotnet-application/asp-net-mvc-tutorial-image01.png)

このチュートリアルでは、Azure Cosmos DB サービスを使用して、Azure でホストされている ASP.NET MVC Web アプリケーションからデータを保存したりデータにアクセスしたりする方法を説明します。 ASP.NET MVC コンポーネントに関する説明が不要で、Azure Cosmos DB のみを重点的に取り上げた解説をお探しの方は、[ コンソール アプリケーションの作成](sql-api-get-started.md)に関する記事を参照してください。

> [!TIP]
> このチュートリアルでは、ASP.NET MVC と Azure Websites の使用経験がある読者を想定しています。 ASP.NET や[前提条件となるツール](#_Toc395637760)を初めて扱う方は、完全なサンプル プロジェクトを [GitHub][GitHub] からダウンロードし、この例の指示に従うことをお勧めします。 プロジェクトをビルドした後でこの記事を見直すと、プロジェクトのコンテキストのコードについての洞察を得ることができます。
> 
> 

## <a name="_Toc395637760"></a>このデータベース チュートリアルの前提条件
この記事の手順を実行する前に、次のソフトウェアがインストールされていることを確認してください。

* アクティブな Azure アカウントアカウントがない場合、Azure 試用版にサインアップして、最大 10 件の無料 Mobile Apps を入手できます。  Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。 

  [!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

* [!INCLUDE [cosmos-db-emulator-vs](../../includes/cosmos-db-emulator-vs.md)]  
* Microsoft Azure SDK for .NET for Visual Studio 2017。Visual Studio インストーラーを通して入手できます。

この記事のすべてのスクリーンショットは、Microsoft Visual Studio Community 2017 を使用して取得されています。 ご利用のシステムに構成されているバージョンと異なる場合、画面やオプション設定が一部異なる可能性がありますが、上記の前提条件を満たしていれば、ソリューションの動作に支障はありません。

## <a name="_Toc395637761"></a>手順 1: Azure Cosmos DB データベース アカウントを作成する
まず最初に、Azure Cosmos DB アカウントを作成します。 Azure Cosmos DB 用の SQL アカウントが既にある場合、またはこのチュートリアルで Azure Cosmos DB Emulator を使用する場合は、「[新しい ASP.NET MVC アプリケーションを作成する](#_Toc395637762)」に進むことができます。

[!INCLUDE [create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

[!INCLUDE [keys](../../includes/cosmos-db-keys.md)]

<br/>
次のセクションで、新しい ASP.NET MVC アプリケーションをゼロから作成する方法について説明します。 

## <a name="_Toc395637762"></a>手順 2: 新しい ASP.NET MVC アプリケーションを作成する

1. Visual Studio で、**[ファイル]** メニューの **[新規作成]** をポイントし、**[プロジェクト]** をクリックします。 **[新しいプロジェクト]** ダイアログ ボックスが表示されます。

2. **[プロジェクトの種類]** ウィンドウで、**[テンプレート]**、**[Visual C#]**、**[Web]** の順に展開し、**[ASP.NET Web アプリケーション]** を選択します。

      ![ASP.NET Web アプリケーション プロジェクトの種類が強調表示されている [新しいプロジェクト] ダイアログ ボックスのスクリーンショット](./media/sql-api-dotnet-application/asp-net-mvc-tutorial-new-project-dialog.png)

3. **[名前]** ボックスに、プロジェクトの名前を入力します。 このチュートリアルでは、"todo" という名前を使用します。 これ以外の名前を使用する場合は、このチュートリアルで todo 名前空間について言及されているすべての場所で、提供されているコード サンプルを、ここでアプリケーションに対して指定した名前に変更する必要があります。 
4. **[参照]** をクリックして、プロジェクトを作成するフォルダーに移動し、**[OK]** をクリックします。
   
      **[新しい ASP.NET Web アプリケーション]** ダイアログ ボックスが表示されます。
   
    ![MVC アプリケーション テンプレートが強調表示されている [新しい ASP.NET Web アプリケーション] ダイアログ ボックスのスクリーンショット](./media/sql-api-dotnet-application/asp-net-mvc-tutorial-MVC.png)
5. テンプレート ウィンドウで、 **[MVC]** を選択します。

6. **[OK]** をクリックすると、Visual Studio のスキャフォールディング機能によって空の ASP.NET MVC テンプレートが作成されます。 

          
7. Visual Studio によってスケルトン MVC アプリケーションが作成されると、空の ASP.NET アプリケーションをローカルに実行できる状態となります。
   
    読者の皆さんは、ASP.NET の "Hello World" アプリケーションで体験済みだと思いますので、プロジェクトをローカルに実行する手順は省略します。 早速このプロジェクトに Azure Cosmos DB を追加し、アプリケーションを構築しましょう。

## <a name="_Toc395637767"></a>手順 3: Azure Cosmos DB を MVC Web アプリケーション プロジェクトに追加する
このソリューションに必要な ASP.NET MVC のほとんどの構成要素が揃ったので、このチュートリアルの本来の目的である MVC Web アプリケーションへの Azure Cosmos DB の追加に移ります。

1. Azure Cosmos DB .NET SDK は、NuGet パッケージの形式で配布されています。 Visual Studio で NuGet パッケージを取得するには、**ソリューション エクスプローラー**でプロジェクトを右クリックし、**[NuGet パッケージの管理]** をクリックして表示される Visual Studio の NuGet パッケージ マネージャーを使用します。
   
    ![[NuGet パッケージの管理] が強調表示されている、ソリューション エクスプローラーでの Web アプリケーション プロジェクトの右クリック オプションのスクリーンショット](./media/sql-api-dotnet-application/asp-net-mvc-tutorial-manage-nuget.png)
   
    **[NuGet パッケージの管理]** ダイアログ ボックスが表示されます。
2. NuGet の **[参照]** ボックスに「***Azure DocumentDB***」と入力します  (パッケージ名は Azure Cosmos DB に更新されていません)。
   
    結果から、**Microsoft.Azure.DocumentDB by Microsoft** パッケージをインストールします。 これにより、Azure Cosmos DB パッケージだけでなく、依存関係のあるすべてのコンポーネント (Newtonsoft.Json など) がダウンロードされてインストールされます。 **[プレビュー]** ウィンドウで **[OK]** をクリックし、**[ライセンスへの同意]** ウィンドウの **[同意する]** をクリックしてインストールを実行します。
   
    ![Microsoft Azure Cosmos DB クライアント ライブラリが強調表示されている [NuGet パッケージの管理] ウィンドウのスクリーンショット](./media/sql-api-dotnet-application/asp-net-mvc-tutorial-install-nuget.png)
   
      または、パッケージ マネージャー コンソールを使用してパッケージをインストールすることもできます。 そのためには、**[ツール]** メニューで **[NuGet パッケージ マネージャー]**、**[パッケージ マネージャー コンソール]** の順にクリックします。 プロンプトで、次のように入力します。
   
        Install-Package Microsoft.Azure.DocumentDB
        
3. パッケージがインストールされると、次のように、Microsoft.Azure.Documents.Client と Newtonsoft.Json の 2 つの新しい参照が Visual Studio ソリューションに追加されます。
   
    ![ソリューション エクスプローラーで JSON データ プロジェクトに追加された 2 つの参照のスクリーンショット](./media/sql-api-dotnet-application/asp-net-mvc-tutorial-added-references.png)

## <a name="_Toc395637763"></a>手順 4:ASP.NET MVC アプリケーションをセットアップする
次に、モデル、ビュー、およびコントローラーをこの MVC アプリケーションに追加します。

* [モデルを追加します](#_Toc395637764)。
* [コントローラーを追加します](#_Toc395637765)。
* [ビューを追加します](#_Toc395637766)。

### <a name="_Toc395637764"></a>JSON データ モデルを追加する
最初に作成するのは、MVC の " **M** " (モデル) です。 

1. **ソリューション エクスプローラー**で、**Models** フォルダーを右クリックし、**[追加]**、**[クラス]** の順にクリックします。
   
      **[新しい項目の追加]** ダイアログ ボックスが表示されます。
2. 新しいクラスに **Item.cs** という名前を設定して、**[追加]** をクリックします。 
3. この新しい **Item.cs** ファイルで、最後の *using ステートメント*の後に次のコードを追加します。
   
        using Newtonsoft.Json;
4. 次のコード 
   
        public class Item
        {
        }
   
    を、以下のコードに置き換えます。
   
        public class Item
        {
            [JsonProperty(PropertyName = "id")]
            public string Id { get; set; }
   
            [JsonProperty(PropertyName = "name")]
            public string Name { get; set; }
   
            [JsonProperty(PropertyName = "description")]
            public string Description { get; set; }
   
            [JsonProperty(PropertyName = "isComplete")]
            public bool Completed { get; set; }
        }
   
    Azure Cosmos DB のすべてのデータは、JSON 形式でネットワーク越しに渡され、保存されます。 JSON.NET によってオブジェクトをシリアル化または逆シリアル化する方法を制御するには、作成した **Item** クラスで示したとおり、**JsonProperty** 属性を使用できます。 これを行う**必要**はありませんが、プロパティが JSON camelCase 名前付け規則に従っていることを確認しておきましょう。 
   
    JSON に渡されるプロパティ名の形式を制御できるだけでなく、 **Description** プロパティに対して行ったように、.NET プロパティの名前全体を変更することもできます。 

### <a name="_Toc395637765"></a>コントローラーを追加する
以上が、**M** (モデル) に相当する部分です。今度は、MVC の **C** に相当するコントローラー クラスを作成しましょう。

1. **ソリューション エクスプローラー**で、**Controllers** フォルダーを右クリックし、**[追加]**、**[コントローラー]** の順にクリックします。
   
    **[スキャフォールディングの追加]** ダイアログ ボックスが表示されます。
2. **[MVC 5 コントローラー - 空]** を選択し、**[追加]** をクリックします。
   
    ![[MVC 5 コントローラー - 空] オプションが強調表示されている [スキャフォールディングの追加] ダイアログ ボックスのスクリーンショット](./media/sql-api-dotnet-application/asp-net-mvc-tutorial-controller-add-scaffold.png)
3. 新しいコントローラーに **ItemController**
   
    ![[コントローラーの追加] ダイアログ ボックスのスクリーンショット](./media/sql-api-dotnet-application/asp-net-mvc-tutorial-add-controller.png)
   
    ファイルが作成されると、Visual Studio ソリューションに新しい ItemController.cs ファイルが追加され、 **ソリューション エクスプローラー**に次のように表示されます。 前に作成した新しい Item.cs ファイルも表示されます。
   
    ![Visual Studio ソリューションのスクリーンショット - 新しい ItemController.cs ファイルと Item.cs ファイルが強調表示されているソリューション エクスプローラー](./media/sql-api-dotnet-application/asp-net-mvc-tutorial-new-item-solution-explorer.png)
   
    ItemController.cs は閉じてもかまいません。後でまた使用します。 

### <a name="_Toc395637766"></a>ビューを追加する
今度は、MVC の **V** (ビュー) を作成します。

* [項目のインデックス ビューを追加します](#AddItemIndexView)。
* [新しい項目を作成するためのビューを追加します](#AddNewIndexView)。
* [項目を編集するためのビューを追加します](#_Toc395888515)。

#### <a name="AddItemIndexView"></a>項目のインデックス ビューを追加する
1. **ソリューション エクスプローラー**で、**Views** フォルダーを展開します。先ほど **ItemController** を追加したときに Visual Studio によって作成された空の **Item** フォルダーを右クリックし、**[追加]** をクリックします。次に、**[ビュー]** をクリックします。
   
    ![[ビューの追加] コマンドが強調表示された状態の、Visual Studio で作成された Item フォルダーが示されているソリューション エクスプローラーのスクリーンショット](./media/sql-api-dotnet-application/asp-net-mvc-tutorial-add-view.png)
2. **[ビューの追加]** ダイアログ ボックスで、次の操作を行います。
   
   * **[ビュー名]** ボックスに、「***Index***」と入力します。
   * **[テンプレート]** ボックスで、***[一覧]*** を選択します。
   * **[モデル クラス]** ボックスで、***[Item (todo.Models)]*** を選択します。
   * レイアウト ページ ボックスに、「***~/Views/Shared/_Layout.cshtml***」と入力します。
     
   ![[ビューの追加] ダイアログ ボックスのスクリーンショット](./media/sql-api-dotnet-application/asp-net-mvc-tutorial-add-view-dialog.png)
3. 以上の値をすべて設定し、**[追加]** をクリックすると、Visual Studio で新しいテンプレート ビューが作成されます。 完了すると、作成された cshtml ファイルが表示されます。 このファイルは閉じてください。後で再度使用します。

#### <a name="AddNewIndexView"></a>新しい項目を作成するためのビューを追加する
**項目のインデックス** ビューの作成と同様に、**項目**を新規作成するためのビューを新たに作成します。

1. **ソリューション エクスプローラー**で、**Item** フォルダーを再び右クリックし、**[追加]**、**[ビュー]** の順にクリックします。
2. **[ビューの追加]** ダイアログ ボックスで、次の操作を行います。
   
   * **[ビュー名]** ボックスに、「***Create***」と入力します。
   * **[テンプレート]** ボックスで、***[作成]*** を選択します。
   * **[モデル クラス]** ボックスで、***[Item (todo.Models)]*** を選択します。
   * レイアウト ページ ボックスに、「***~/Views/Shared/_Layout.cshtml***」と入力します。
   * **[追加]** をクリックします。
   
#### <a name="_Toc395888515"></a>項目を編集するためのビューを追加する
最後にもう 1 つ、 **Item** を編集するためのビューを同じように作成します。

1. **ソリューション エクスプローラー**で、**Item** フォルダーを再び右クリックし、**[追加]**、**[ビュー]** の順にクリックします。
2. **[ビューの追加]** ダイアログ ボックスで、次の操作を行います。
   
   * **[ビュー名]** ボックスに、「***Edit***」と入力します。
   * **[テンプレート]** ボックスで、***[編集]*** を選択します。
   * **[モデル クラス]** ボックスで、***[Item (todo.Models)]*** を選択します。
   * レイアウト ページ ボックスに、「***~/Views/Shared/_Layout.cshtml***」と入力します。
   * **[追加]** をクリックします。

この作業が済んだら、Visual Studio に表示されている cshtml ドキュメントをすべて閉じてください。これらのビューは後で使用します。

## <a name="_Toc395637769"></a>手順 5: Azure Cosmos DB を接続する
MVC の標準的な構成要素を準備できたので、次に Azure Cosmos DB 用のコードを追加します。 

このセクションでは、次の処理を行うコードを追加します。

* [未完了項目の一覧表示](#_Toc395637770)。
* [項目の追加](#_Toc395637771)。
* [項目の編集](#_Toc395637772)。

### <a name="_Toc395637770"></a>MVC Web アプリケーションの未完了項目の一覧表示
最初に、Azure Cosmos DB に接続して使用するためのすべてのロジックを含むクラスを追加します。 このチュートリアルでは、このすべてのロジックを DocumentDBRepository という名前のリポジトリ クラスにカプセル化します。 

1. **ソリューション エクスプローラー**で、プロジェクトを右クリックし、**[追加]**、**[クラス]** の順にクリックします。 新しいクラスの名前として「**DocumentDBRepository**」と入力し、**[追加]** をクリックします。
2. 新しく作成した **DocumentDBRepository** クラスで、"*名前空間*" の宣言の上に次の "*using ステートメント*" を追加します。
   
        using Microsoft.Azure.Documents; 
        using Microsoft.Azure.Documents.Client; 
        using Microsoft.Azure.Documents.Linq; 
        using System.Configuration;
        using System.Linq.Expressions;
        using System.Threading.Tasks;
        using System.Net;
        
    次のコード 
   
        public class DocumentDBRepository
        {
        }
   
    を、以下のコードに置き換えます。
   
        public static class DocumentDBRepository<T> where T : class
        {
            private static readonly string DatabaseId = ConfigurationManager.AppSettings["database"];
            private static readonly string CollectionId = ConfigurationManager.AppSettings["collection"];
            private static DocumentClient client;
   
            public static void Initialize()
            {
                client = new DocumentClient(new Uri(ConfigurationManager.AppSettings["endpoint"]), ConfigurationManager.AppSettings["authKey"]);
                CreateDatabaseIfNotExistsAsync().Wait();
                CreateCollectionIfNotExistsAsync().Wait();
            }
   
            private static async Task CreateDatabaseIfNotExistsAsync()
            {
                try
                {
                    await client.ReadDatabaseAsync(UriFactory.CreateDatabaseUri(DatabaseId));
                }
                catch (DocumentClientException e)
                {
                    if (e.StatusCode == System.Net.HttpStatusCode.NotFound)
                    {
                        await client.CreateDatabaseAsync(new Database { Id = DatabaseId });
                    }
                    else
                    {
                        throw;
                    }
                }
            }
   
            private static async Task CreateCollectionIfNotExistsAsync()
            {
                try
                {
                    await client.ReadDocumentCollectionAsync(UriFactory.CreateDocumentCollectionUri(DatabaseId, CollectionId));
                }
                catch (DocumentClientException e)
                {
                    if (e.StatusCode == System.Net.HttpStatusCode.NotFound)
                    {
                        await client.CreateDocumentCollectionAsync(
                            UriFactory.CreateDatabaseUri(DatabaseId),
                            new DocumentCollection { Id = CollectionId },
                            new RequestOptions { OfferThroughput = 1000 });
                    }
                    else
                    {
                        throw;
                    }
                }
            }
        }
   
    
3. 構成からいくつかの値を読み取るので、アプリケーションの **Web.config** ファイルを開き、以下の行を `<AppSettings>` セクションの下に追加します。
   
        <add key="endpoint" value="enter the URI from the Keys blade of the Azure Portal"/>
        <add key="authKey" value="enter the PRIMARY KEY, or the SECONDARY KEY, from the Keys blade of the Azure  Portal"/>
        <add key="database" value="ToDoList"/>
        <add key="collection" value="Items"/>
4. 次に、Azure Portal の [キー] ブレードを使用して、*endpoint* と *authKey* の値を更新します。 [キー] ブレードの **[URI]** を endpoint 設定の値として使用し、[キー] ブレードの **[プライマリ キー]** または **[セカンダリ キー]** を authKey 設定の値として使用します。

    これで Azure Cosmos DB リポジトリは接続できるので、アプリケーション ロジックを追加してみましょう。

1. 最初に、この todo リスト アプリケーションに、未完了の項目を表示する機能を追加します。  次のコード スニペットをコピーし、 **DocumentDBRepository** クラス内のどこかに貼り付けます。
   
        public static async Task<IEnumerable<T>> GetItemsAsync(Expression<Func<T, bool>> predicate)
        {
            IDocumentQuery<T> query = client.CreateDocumentQuery<T>(
                UriFactory.CreateDocumentCollectionUri(DatabaseId, CollectionId))
                .Where(predicate)
                .AsDocumentQuery();
   
            List<T> results = new List<T>();
            while (query.HasMoreResults)
            {
                results.AddRange(await query.ExecuteNextAsync<T>());
            }
   
            return results;
        }
2. 前に追加した **ItemController** を開き、名前空間の宣言の上に次の "*using ステートメント*" を追加します。
   
        using System.Net;
        using System.Threading.Tasks;
        using todo.Models;
   
    プロジェクトの名前が "todo" ではない場合は、using "todo.Models"; をプロジェクトの名前に合わせて更新する必要があります。
   
    次のコード
   
        //GET: Item
        public ActionResult Index()
        {
            return View();
        }
   
    を、以下のコードに置き換えます。
   
        [ActionName("Index")]
        public async Task<ActionResult> IndexAsync()
        {
            var items = await DocumentDBRepository<Item>.GetItemsAsync(d => !d.Completed);
            return View(items);
        }
3. **Global.asax.cs** を開いて **Application_Start** メソッドに次の行を追加します。 
   
        DocumentDBRepository<todo.Models.Item>.Initialize();

この時点で、エラーなくソリューションをビルドすることは可能です。

ここで仮にアプリケーションを実行した場合は、**HomeController** とそのコントローラーの **Index** ビューが表示されます。 これは、作業の開始時に選択した MVC テンプレート プロジェクトの既定の動作であって、期待していた動作ではありません。 この MVC アプリケーションのルーティングに手を加えて、この動作を変更しましょう。

***App\_Start\RouteConfig.cs*** を開いて、"defaults:" で始まる行を見つけ、以下と同様にその行を変更します。

        defaults: new { controller = "Item", action = "Index", id = UrlParameter.Optional }

ここでは、ルーティングの動作を制御するための値を URL に指定していない場合、**Home** の代わりに **Item** をコント ローラーとして、ユーザー **インデックス**をビューとして使用するように ASP.NET MVC に指示しています。

ここでアプリケーションを実行したとすると、**ItemController** が呼び出され、さらにリポジトリ クラスが呼び出されて、GetItems メソッドを使用して **Views**\\**Item**\\**Index** ビューに対するすべての不完全な項目が返されます。 

このプロジェクトをビルドして実行すると、次のように表示されます。    

![このデータベース チュートリアルで作成された、ToDo リスト Web アプリケーションのスクリーンショット](./media/sql-api-dotnet-application/build-and-run-the-project-now.png)

### <a name="_Toc395637771"></a>項目の追加
空のグリッドでは物足りないので、データベースにいくつか項目を追加してみましょう。

Azure Cosmos DBRepository および ItemController にコードを追加して、Azure Cosmos DB にレコードを保存します。

1. 次のメソッドを **DocumentDBRepository** クラスに追加します。
   
       public static async Task<Document> CreateItemAsync(T item)
       {
           return await client.CreateDocumentAsync(UriFactory.CreateDocumentCollectionUri(DatabaseId, CollectionId), item);
       }
   
   このメソッドは単に、渡されたオブジェクトを受け取って、Azure Cosmos DB に保存します。
2. ItemController.cs ファイルを開き、クラス内に次のコード スニペットを追加します。 これにより、ASP.NET MVC は **Create** アクションに対して実行することを認識します。 このケースでは、前に作成した関連する Create.cshtml ビューを指定します。
   
        [ActionName("Create")]
        public async Task<ActionResult> CreateAsync()
        {
            return View();
        }
   
    このコントローラーには、 **[Create]** ビューから送信された内容を受け取るためのコードを追加する必要があります。
3. 次のコード ブロックを ItemController.cs クラスに追加します。このコードは、このコントローラーに対するフォームの POST で行う処理を ASP.NET MVC に伝えます。
   
        [HttpPost]
        [ActionName("Create")]
        [ValidateAntiForgeryToken]
        public async Task<ActionResult> CreateAsync([Bind(Include = "Id,Name,Description,Completed")] Item item)
        {
            if (ModelState.IsValid)
            {
                await DocumentDBRepository<Item>.CreateItemAsync(item);
                return RedirectToAction("Index");
            }
   
            return View(item);
        }
   
    このコードは DocumentDBRepository を呼び出し、CreateItemAsync メソッドを使用して新しい todo 項目をデータベースに保存します。 
   
    **セキュリティ上の注意**: **ValidateAntiForgeryToken** 属性は、クロスサイト リクエスト フォージェリ攻撃に対してこのアプリケーションを保護するためにここで使用されます。 この属性を追加するだけでなく、偽造防止トークンもビューで処理する必要があります。 この詳細と正しい実装方法については、[クロスサイト リクエスト フォージェリの防止][Preventing Cross-Site Request Forgery]に関するページを参照してください。 [GitHub][GitHub] で提供されるソース コードには、完全な実装が組み込まれています。
   
    **セキュリティ上の注意**: メソッド パラメーターの **Bind** 属性も使用して、オーバーポスティング攻撃から保護します。 詳細については、[ASP.NET MVC での基本的な CRUD 操作][Basic CRUD Operations in ASP.NET MVC]に関するページを参照してください。

データベースに新しい項目を追加するために必要なコードは以上です。

### <a name="_Toc395637772"></a>項目の編集
最後に、データベース内の **Item** を編集する機能と、それらを完了済みとしてマークするための機能を実装します。 編集のためのビューは既にプロジェクトに追加されています。後は、コントローラーと **DocumentDBRepository** クラスに少しのコードを追加するだけです。

1. **DocumentDBRepository** クラスに次のコードを追加します。
   
        public static async Task<Document> UpdateItemAsync(string id, T item)
        {
            return await client.ReplaceDocumentAsync(UriFactory.CreateDocumentUri(DatabaseId, CollectionId, id), item);
        }
   
        public static async Task<T> GetItemAsync(string id)
        {
            try
            {
                Document document = await client.ReadDocumentAsync(UriFactory.CreateDocumentUri(DatabaseId, CollectionId, id));
                return (T)(dynamic)document;
            }
            catch (DocumentClientException e)
            {
                if (e.StatusCode == HttpStatusCode.NotFound)
                {
                    return null;
                }
                else
                {
                    throw;
                }
            }
        }
   
    1 つ目のメソッド **GetItem** は、Azure Cosmos DB から Item を取得します。この Item が **ItemController** に戻された後、**[Edit]** ビューに渡されます。
   
    2 つ目のメソッドは、Azure Cosmos DB 内の **Document** を、**ItemController** から渡された **Document** に置き換えます。
2. **ItemController** クラスに次のコードを追加します。
   
        [HttpPost]
        [ActionName("Edit")]
        [ValidateAntiForgeryToken]
        public async Task<ActionResult> EditAsync([Bind(Include = "Id,Name,Description,Completed")] Item item)
        {
            if (ModelState.IsValid)
            {
                await DocumentDBRepository<Item>.UpdateItemAsync(item.Id, item);
                return RedirectToAction("Index");
            }
   
            return View(item);
        }
   
        [ActionName("Edit")]
        public async Task<ActionResult> EditAsync(string id)
        {
            if (id == null)
            {
                return new HttpStatusCodeResult(HttpStatusCode.BadRequest);
            }
   
            Item item = await DocumentDBRepository<Item>.GetItemAsync(id);
            if (item == null)
            {
                return HttpNotFound();
            }
   
            return View(item);
        }
   
    1 つ目のメソッドは、ユーザーが **[Index]** ビューから **[Edit]** リンクをクリックしたときに発生する Http GET を処理します。 このメソッドによって、Azure Cosmos DB から [**Document**](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.document.aspx) が取得されて、**[Edit]** ビューに渡されます。
   
    **[Edit]** ビューは、**IndexController** に Http POST を行います。 
   
    追加した 2 つ目のメソッドは、更新されたオブジェクトをデータベースに保存するために、Azure Cosmos DB への引き渡しを処理します。

以上で、アプリケーションを実行するために必要な要素はすべて整いました。不完全な **Item** を一覧表示したり、新しい **Item** を追加したり、**Item** を編集したりすることができます。

## <a name="_Toc395637773"></a>手順 6: ローカルでアプリケーションを実行する
ローカル コンピューターでアプリケーションをテストするには、次の操作を行います。

1. Visual Studio で F5 キーを押して、デバッグ モードでアプリケーションをビルドします。 すると、アプリケーションがビルドされてブラウザーが起動し、先ほど見た空のグリッド ページが表示されます。
   
    ![このデータベース チュートリアルで作成された、ToDo リスト Web アプリケーションのスクリーンショット](./media/sql-api-dotnet-application/asp-net-mvc-tutorial-create-an-item-a.png)
   
     
2. **[Create New]** リンクをクリックし、**[Name]** フィールドと **[Description]** フィールドに値を追加します。 **[Completed]** チェック ボックスはオフのままとします。オンにした場合、新しい **Item** が完了済みの状態で追加されるため、最初のリストに表示されません。
   
    ![[作成] ビューのスクリーンショット](./media/sql-api-dotnet-application/asp-net-mvc-tutorial-create-new-item.png)
3. **[Create]** をクリックすると、**[Index]** ビューにリダイレクトされ、追加した **Item** がリストに表示されます。
   
    ![[Index] ビューのスクリーンショット](./media/sql-api-dotnet-application/asp-net-mvc-tutorial-create-an-item.png)
   
    Todo リストに他にもいくつか **Item** を追加してみてください。
    
4. リストの **Item** の横にある **[Edit]** をクリックすると、**[Edit]** ビューが表示され、対象オブジェクトのプロパティを更新することができます。**Completed** フラグもこのビューで更新できます。 **Complete** フラグをマークして **[Save]** を保存すると、**Item** は未完了タスクのリストから削除されます。
   
    ![[Completed] ボックスがオンになっている [Index] ビューのスクリーンショット](./media/sql-api-dotnet-application/asp-net-mvc-tutorial-completed-item.png)
5. アプリケーションのテストが完了したら、Ctrl キーを押しながら F5 キーを押してアプリケーションのデバッグを中止します。 これで、アプリケーションをデプロイする準備が整いました。

## <a name="_Toc395637774"></a>手順 7: Azure App Service にアプリケーションをデプロイする 
以上で、Azure Cosmos DB と連携するアプリケーションが完成しました。今度は、この Web アプリを Azure App Service にデプロイします。  

1. このアプリケーションを発行するために必要な操作は、**ソリューション エクスプローラー**でプロジェクトを右クリックし、**[発行]** を選択することだけです。
   
    ![ソリューション エクスプローラーの [発行] オプションのスクリーンショット](./media/sql-api-dotnet-application/asp-net-mvc-tutorial-publish.png)

2. **[発行]** ダイアログ ボックスで、**[Microsoft Azure App Service]** をクリックします。次に、**[新規作成]** を選択して App Service プロファイルを作成するか、**[既存のものを選択]** をクリックして既存のプロファイルを使用します。

    ![Visual Studio の [発行] ダイアログ ボックス](./media/sql-api-dotnet-application/asp-net-mvc-tutorial-publish-to-existing.png)

3. 既存の Azure App Service プロファイルがある場合は、サブスクリプション名を入力します。 **ビュー** フィルターでリソース グループまたはリソースの種類で並べ替えを行った後、Azure App Service を選択します。 
   
    ![Visual Studio の [App Service] ダイアログ ボックス](./media/sql-api-dotnet-application/asp-net-mvc-tutorial-app-service.png)

4. 新しい Azure App Service プロファイルを作成するには、**[発行]** ダイアログ ボックスで **[新規作成]** をクリックします。 **[App Service の作成]** ダイアログで、Web アプリ名と適切なサブスクリプション、リソース グループ、および App Service プランを入力し、**[作成]** をクリックします。

    ![Visual Studio の [App Service の作成] ダイアログ ボックス](./media/sql-api-dotnet-application/asp-net-mvc-tutorial-create-app-service.png)

数秒すると、Web アプリケーションの発行が完了し、ブラウザーが起動されます。作成したアプリケーションが Azure で実行されている様子を確認できます。



## <a name="_Toc395637775"></a>次のステップ
お疲れさまでした。 ここでは初めての方を対象に、Azure Cosmos DB を使用した ASP.NET MVC Web アプリケーションを作成し、Azure に発行する方法を説明しました。 このチュートリアルに含まれていない詳細や削除の機能など、完全なアプリケーションのソース コードは、[GitHub][GitHub] からダウンロードまたは複製できます。 これらの機能を自分のアプリケーションに追加する場合は、該当するコードを入手してアプリケーションに追加してください。

アプリケーションに機能を追加する場合は、[Azure Cosmos DB .NET ライブラリ](/dotnet/api/overview/azure/cosmosdb?view=azure-dotnet)から入手できる API を参考にしてください。また、[GitHub][GitHub] の Azure Cosmos DB .NET ライブラリにも気軽に投稿してください。 

[Visual Studio Express]: https://www.visualstudio.com/products/visual-studio-express-vs.aspx
[Microsoft Web Platform Installer]: https://www.microsoft.com/web/downloads/platform.aspx
[Preventing Cross-Site Request Forgery]: https://go.microsoft.com/fwlink/?LinkID=517254
[Basic CRUD Operations in ASP.NET MVC]: https://go.microsoft.com/fwlink/?LinkId=317598
[GitHub]: https://github.com/Azure-Samples/documentdb-net-todo-app
