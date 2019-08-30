---
title: 'C# のチュートリアル: Azure SQL データベースのデータにインデックスを付ける - Azure Search'
description: C# コード例で、Azure SQL データベースに接続し、検索可能なデータを抽出して、それを Azure Search インデックスに読み込む方法を示します。
author: HeidiSteen
manager: nitinme
services: search
ms.service: search
ms.topic: tutorial
ms.date: 05/02/2019
ms.author: heidist
ms.openlocfilehash: d0f0abade5d1eea952c5abde293ae90745ee9b04
ms.sourcegitcommit: 36e9cbd767b3f12d3524fadc2b50b281458122dc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/20/2019
ms.locfileid: "69640648"
---
# <a name="c-tutorial-crawl-an-azure-sql-database-using-azure-search-indexers"></a>C# のチュートリアル: Azure Search インデクサーを使用して Azure SQL データベースをクロールする

検索可能なデータをサンプル Azure SQL データベースから抽出するためのインデクサーを構成する方法について説明します。 [インデクサー](search-indexer-overview.md)は、外部データ ソースをクロールして[検索インデックス](search-what-is-an-index.md)にデータを投入する Azure Search のコンポーネントです。 Azure SQL Database のインデクサーは、すべてのインデクサーの中で最も広く使用されています。 

インデクサーを構成することによって、記述すべきコードや保守すべきコードの量が少なくて済むため、そのスキルは身に付けておいて損はありません。 スキーマに準拠した JSON データセットを作成して投入する代わりに、インデクサーをデータ ソースにアタッチし、データをインデクサーで抽出してインデックスに挿入することができるほか、必要に応じてインデクサーを定期実行し、基になるソースの変更を反映することもできます。

このチュートリアルでは、[Azure Search .NET クライアント ライブラリ](https://aka.ms/search-sdk)と .NET Core コンソール アプリケーションを使用して次のタスクを実行します。

> [!div class="checklist"]
> * Search サービスの情報をアプリケーション設定に追加する
> * 外部データセットを Azure SQL データベースに用意する 
> * サンプル コードでインデックスとインデクサーの定義を確認する
> * インデクサーのコードを実行してデータをインポートする
> * インデックスを検索する
> * インデクサーの構成をポータルで確認する

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。

## <a name="prerequisites"></a>前提条件

このクイック スタートでは、次のサービス、ツール、およびデータを使用します。 

[Azure Search サービスを作成](search-create-service-portal.md)するか、現在のサブスクリプションから[既存のサービスを見つけます](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices)。 このチュートリアル用には、無料のサービスを使用できます。

[Azure SQL Database](https://azure.microsoft.com/services/sql-database/) は、インデクサーによって使用される外部データ ソースを格納します。 サンプル ソリューションでは、SQL データ ファイルを入力することにってテーブルを作成しています。 このチュートリアルでは、サービスとデータベースを作成するための手順を説明しています。

[Visual Studio 2017](https://visualstudio.microsoft.com/downloads/)。サンプル ソリューションを実行するために、任意のエディションを使用できます。 サンプル コードと手順については、無料の Community エディションでテストされています。

Azure サンプル GitHub リポジトリの [Azure-Samples/search-dotnet-getting-started](https://github.com/Azure-Samples/search-dotnet-getting-started) に、サンプル ソリューションが用意されています。 そのソリューションをダウンロードして展開します。 既定では、ソリューションは読み取り専用です。 ソリューションを右クリックし、読み取り専用属性をオフにして、ファイルを変更できるようにします。

> [!Note]
> 無料の Azure Search サービスを使用している場合、インデックス、インデクサー、データ ソースの数は、いずれも 3 つまでに制限されます。 このチュートリアルでは、それぞれ 1 つずつ作成します。 ご利用のサービスに、新しいリソースを作成できるだけの空き領域があることを確認してください。

## <a name="get-a-key-and-url"></a>キーと URL を入手する

REST 呼び出しには、要求ごとにサービス URL とアクセス キーが必要です。 両方を使用して検索サービスが作成されるので、Azure Search をサブスクリプションに追加した場合は、次の手順に従って必要な情報を入手してください。

1. [Azure portal にサインイン](https://portal.azure.com/)し、ご使用の検索サービスの **[概要]** ページで、URL を入手します。 たとえば、エンドポイントは `https://mydemo.search.windows.net` のようになります。

1. **[設定]**  >  **[キー]** で、サービスに対する完全な権限の管理者キーを取得します。 管理キーをロールオーバーする必要がある場合に備えて、2 つの交換可能な管理キーがビジネス継続性のために提供されています。 オブジェクトの追加、変更、および削除の要求には、主キーまたはセカンダリ キーのどちらかを使用できます。

![HTTP エンドポイントとアクセス キーを取得する](media/search-get-started-postman/get-url-key.png "HTTP エンドポイントとアクセス キーを取得する")

すべての要求では、サービスに送信されるすべての要求に API キーが必要です。 有効なキーがあれば、要求を送信するアプリケーションとそれを処理するサービスの間で、要求ごとに信頼を確立できます。

## <a name="set-up-connections"></a>接続を設定する
必要なサービスへの接続情報は、ソリューションの **appsettings.json** ファイルで指定します。 

1. Visual Studio で、**DotNetHowToIndexers.sln** ファイルを開きます。

1. 各設定を指定できるように、ソリューション エクスプローラーで **appsettings.json** を開きます。  

最初の 2 つのエントリは、Azure Search サービスの URL と管理者キーを使用して、今すぐ指定できます。 `https://mydemo.search.windows.net` のエンドポイントの場合、指定するサービス名は `mydemo` です。

```json
{
  "SearchServiceName": "Put your search service name here",
  "SearchServiceAdminApiKey": "Put your primary or secondary API key here",
  "AzureSqlConnectionString": "Put your Azure SQL database connection string here",
}
```

最後のエントリには、既存のデータベースを指定する必要があります。 これは、次の手順で作成します。

## <a name="prepare-sample-data"></a>サンプル データの準備

この手順では、インデクサーがクロールできる外部データ ソースを作成します。 Azure Portal とサンプルの *hotels.sql* ファイルを使用して、Azure SQL Database にデータセットを作成することができます。 Azure Search で使用されるのは、ビューやクエリから生成されるようなフラット化された行セットです。 サンプル ソリューションの SQL ファイルでは、単一のテーブルを作成してデータを投入します。

次の演習では、サーバーもデータベースも存在していないことを想定しています。どちらも手順 2. で作成することになります。 既にリソースがある場合には、hotels テーブルをそこに追加して、手順 4. から始めることができます。

1. [Azure portal](https://portal.azure.com/) にサインインします。 

2. データベース、サーバー、およびリソース グループを作成する **Azure SQL Database** を見つけるか作成します。 既定値および一番低い価格レベルを使用してかまいません。 サーバーを作成する利点は、後の手順でテーブルを作成して読み込むために必要な管理者ユーザー名とパスワードを指定できることです。

   ![[新しいデータベース] ページ](./media/search-indexer-tutorial/indexer-new-sqldb.png)

3. **[作成]** をクリックして、新しいサーバーとデータベースをデプロイします。 サーバーとデータベースがデプロイされるまで待ちます。

4. 新しいデータベースの [SQL Database] ページをまだ開いていない場合は開きます。 リソース名は *SQL Server* ではなく "*SQL データベース*" になっている必要があります。

   ![[SQL Database] ページ](./media/search-indexer-tutorial/hotels-db.png)

4. ナビゲーション ウィンドウで、 **[クエリ エディター (プレビュー)]** をクリックします。

5. **[ログイン]** をクリックし、サーバー管理者のユーザー名とパスワードを入力します。

6. **[クエリを開く]** をクリックし、*hotels.sql* の場所に移動します。 

7. ファイルを選択し、 **[開く]** をクリックします。 このスクリプトは次のスクリーンショットのようになります。

   ![SQL スクリプト](./media/search-indexer-tutorial/sql-script.png)

8. **[実行]** をクリックしてクエリを実行します。 3 つの行について、クエリが正常に実行されたことを示すメッセージが [結果] ウィンドウに表示されます。

9. このテーブルから行セットが返されるようにするには、検証ステップとして次のクエリを実行します。

    ```sql
    SELECT HotelId, HotelName, Tags FROM Hotels
    ```
    クエリ エディターでは、典型的なクエリである `SELECT * FROM Hotels` が正しく動作しません。 サンプル データでは、Location フィールドに地理座標が格納されていますが、このフィールドが現時点のエディターでは処理されません。 その他の一連の列を照会するには、`SELECT * FROM sys.columns WHERE object_id = OBJECT_ID('dbo.Hotels')` ステートメントを実行できます。

10. これで外部データセットが揃ったので、データベースの ADO.NET 接続文字列をコピーします。 データベースの [SQL Database] ページで **[設定]**  >  **[接続文字列]** に移動し、ADO.NET 接続文字列をコピーします。
 
    ADO.NET 接続文字列は次のようになります。有効なデータベース名、ユーザー名、パスワードに置き換えて使用してください。

    ```sql
    Server=tcp:hotels-db.database.windows.net,1433;Initial Catalog=hotels-db;Persist Security Info=False;User ID={your_username};Password={your_password};MultipleActiveResultSets=False;Encrypt=True;TrustServerCertificate=False;Connection Timeout=30;
    ```
11. Visual Studio で **appsettings.json** ファイルの 3 つ目のエントリとして、接続文字列を "AzureSqlConnectionString" に貼り付けます。

    ```json
    {
      "SearchServiceName": "<placeholder-Azure-Search-service-name>",
      "SearchServiceAdminApiKey": "<placeholder-admin-key-for-Azure-Search>",
      "AzureSqlConnectionString": "Server=tcp:hotels-db.database.windows.net,1433;Initial Catalog=hotels-db;Persist Security  Info=False;User ID={your_username};Password={your_password};MultipleActiveResultSets=False;Encrypt=True;TrustServerCertificate=False;Connection Timeout=30;",
    }
    ```

## <a name="understand-the-code"></a>コードの理解

データと構成設定が済んだら、**DotNetHowToIndexers.sln** 内のサンプル プログラムをビルドして実行する準備は完了です。 その前に、このサンプルで使用するインデックスとインデクサーの定義を詳しく見てみましょう。 関連するコードは次の 2 つのファイルにあります。

  + **hotel.cs**: インデックスを定義するスキーマが含まれています。
  + **Program.cs**: サービスの構造を作成したり管理したりするための関数が含まれています。

### <a name="in-hotelcs"></a>hotel.cs の内容

インデックス スキーマは、フィールドのコレクションを定義します。この定義には、次の HotelName のフィールドの定義に見られるように、フィールドがフルテキスト検索可能かどうか、フィルター処理可能かどうか、並べ替え可能かどうかなど、許可される操作を指定する属性も含まれています。 

```csharp
. . . 
[IsSearchable, IsFilterable, IsSortable]
public string HotelName { get; set; }
. . .
```

その他にも、スキーマにはさまざまな要素が存在します。検索スコアを高めるためのスコア付けプロファイルやカスタム アナライザーなど、各種コンストラクトがその例です。 ただし、このドキュメントの目的上、スキーマに含まれている定義はごくわずかで、サンプル データセットに含まれているフィールドのみがその構成要素となります。

このチュートリアルでは、インデクサーで単一のデータ ソースからデータをプルしています。 実際には、同じインデックスに複数のインデクサーをアタッチして、さまざまなデータ ソースから統合された検索可能なインデックスを作成することができます。 必要に応じて柔軟に、データ ソースだけを変えながら同じインデックス/インデクサー ペアを使用したり、インデクサーとデータ ソースの組み合わせを変えながら 1 つのインデックスを使用したりすることができます。

### <a name="in-programcs"></a>Program.cs の内容

メイン プログラムには、クライアント、インデックス、データ ソース、インデクサーを作成するためのロジックが含まれます。 このコードは、読者がこのプログラムを繰り返し実行する可能性を考慮し、同じ名前のリソースが既に存在しているかどうかを調べて、削除します。

データ ソース オブジェクトの構成には、Azure SQL に組み込まれている[変更検出機能](https://docs.microsoft.com/sql/relational-databases/track-changes/about-change-tracking-sql-server)を活用するために、[インデックスの増分作成](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md#capture-new-changed-and-deleted-rows)を含め、Azure SQL データベース リソースに固有の設定が使用されます。 Azure SQL にあるデモ hotels データベースには、**IsDeleted** という名前の "論理的な削除" 列があります。 データベースでこの列を true に設定すると、インデクサーによって、Azure Search インデックスから対応するドキュメントが削除されます。

  ```csharp
  Console.WriteLine("Creating data source...");

  DataSource dataSource = DataSource.AzureSql(
      name: "azure-sql",
      sqlConnectionString: configuration["AzureSQLConnectionString"],
      tableOrViewName: "hotels",
      deletionDetectionPolicy: new SoftDeleteColumnDeletionDetectionPolicy(
          softDeleteColumnName: "IsDeleted",
          softDeleteMarkerValue: "true"));
  dataSource.DataChangeDetectionPolicy = new SqlIntegratedChangeTrackingPolicy();

  searchService.DataSources.CreateOrUpdateAsync(dataSource).Wait();
  ```

インデクサー オブジェクトはプラットフォームに依存しません。構成、スケジュール、起動は、ソースに関係なく同じです。 この例のインデクサーには、スケジュールのほか、インデクサーの履歴を消去するリセット オプション、インデクサーを直ちに作成して実行するためのメソッドの呼び出しが含まれています。

  ```csharp
  Console.WriteLine("Creating Azure SQL indexer...");
  Indexer indexer = new Indexer(
      name: "azure-sql-indexer",
      dataSourceName: dataSource.Name,
      targetIndexName: index.Name,
      schedule: new IndexingSchedule(TimeSpan.FromDays(1)));
  // Indexers contain metadata about how much they have already indexed
  // If we already ran the sample, the indexer will remember that it already
  // indexed the sample data and not run again
  // To avoid this, reset the indexer if it exists
  exists = await searchService.Indexers.ExistsAsync(indexer.Name);
  if (exists)
  {
      await searchService.Indexers.ResetAsync(indexer.Name);
  }

  await searchService.Indexers.CreateOrUpdateAsync(indexer);

  // We created the indexer with a schedule, but we also
  // want to run it immediately
  Console.WriteLine("Running Azure SQL indexer...");

  try
  {
      await searchService.Indexers.RunAsync(indexer.Name);
  }
  catch (CloudException e) when (e.Response.StatusCode == (HttpStatusCode)429)
  {
      Console.WriteLine("Failed to run indexer: {0}", e.Response.Content);
  }
  ```



## <a name="run-the-indexer"></a>インデクサーを実行する

この手順では、プログラムをコンパイルして実行します。 

1. ソリューション エクスプローラーで **[DotNetHowToIndexers]** を右クリックし、 **[ビルド]** を選択します。
2. もう一度 **[DotNetHowToIndexers]** を右クリックし、 **[デバッグ]**  >  **[新しいインスタンスを開始]** の順に選択します。

プログラムがデバッグ モードで実行されます。 コンソール ウィンドウで各操作の状態が報告されます。

  ![SQL スクリプト](./media/search-indexer-tutorial/console-output.png)

コードは、Visual Studio からローカルで実行され、Azure 上の Search サービスに接続します。次に、このサービスが接続文字列を使用して Azure SQL Database に接続し、データセットを取得します。 このように多くの操作が伴うため、障害点となり得る箇所は複数存在しますが、エラーが発生した場合は、まず次の状況を確認してください。

+ 指定する Search サービスの接続情報は、このチュートリアルのサービス名に限定されます。 完全な URL を入力した場合、インデックスの作成時に接続エラーで操作が停止します。

+ **appsettings.json** のデータベース接続情報。 これは、ポータルから取得した ADO.NET 接続文字列に、実際のデータベースの有効なユーザー名とパスワードを反映したものであることが必要です。 ユーザー アカウントには、データを取得するためのアクセス許可が必要です。

+ リソース制限。 既に述べたように、共有 (無料) のサービスは、インデックス、インデクサー、データ ソースがいずれも 3 つまでに制限されています。 上限に達した場合、新しいオブジェクトを作成できなくなります。

## <a name="search-the-index"></a>インデックスを検索する 

Azure Portal にアクセスし、Search サービスの [概要] ページで、一番上の **[Search エクスプローラー]** をクリックし、新しいインデックスに対するクエリをいくつか送信します。

1. 一番上にある **[インデックスの変更]** をクリックして *hotels* インデックスを選択します。

2. **[検索]** ボタンをクリックして空の検索を実行します。 

   インデックスの 3 つのエントリが JSON ドキュメントとして返されます。 Search エクスプローラーは、構造全体が見えるようにドキュメントを JSON 形式で返します。

3. 次に、検索文字列として「`search=river&$count=true`」を入力します。 

   このクエリは、`river` という語についてフルテキスト検索を呼び出すもので、その結果には、一致したドキュメントの件数が含まれます。 一致したドキュメントの件数は、インデックスが大きく数千から数百万のドキュメントが含まれている場合のテストで役立ちます。 今回のケースで、このクエリに一致するドキュメントは 1 件だけです。

4. 最後に、JSON 出力を必要なフィールドに限定するため、検索文字列として「`search=river&$count=true&$select=hotelId, baseRate, description`」を入力します。 

   クエリの応答が選択フィールドに制限され、より簡潔な出力内容が得られます。

## <a name="view-indexer-configuration"></a>インデクサーの構成を確認する

ポータルには、先ほどプログラムで作成したものも含むすべてのインデクサーが一覧表示されます。 インデクサーの定義を開いてそのデータ ソースを確認したり、新たに追加された行や変更された行を取得するための更新スケジュールを構成したりすることができます。

1. [Azure portal にサインイン](https://portal.azure.com/)し、検索サービスの **[概要]** ページで、 **[インデックス]** 、 **[インデクサー]** 、 **[データ ソース]** のリンクをクリックします。
3. 各オブジェクトを選択して構成設定を表示または変更します。

   ![インデクサーとデータ ソースのタイル](./media/search-indexer-tutorial/tiles-portal.png)

## <a name="clean-up-resources"></a>リソースのクリーンアップ

チュートリアルの後で最も速くクリーンアップする方法は、Azure Search サービスが含まれているリソース グループを削除することです。 リソース グループを削除することで、そのすべての内容を完全に削除することができます。 ポータルでは、リソース グループ名は Azure Search サービスの [概要] ページに表示されます。

## <a name="next-steps"></a>次の手順

インデクサー パイプラインには、AI エンリッチメント アルゴリズムをアタッチすることができます。 引き続き次のチュートリアルに進んでください。

> [!div class="nextstepaction"]
> [Azure Blob Storage 内ドキュメントのインデックスを Azure Search で作成する](search-howto-indexing-azure-blob-storage.md)