---
title: Azure SQL データにインデックスを付ける C# チュートリアル
titleSuffix: Azure Cognitive Search
description: この C# チュートリアルでは、Azure SQL Database に接続して検索可能なデータを抽出し、Azure Cognitive Search インデックスにそれを読み込みます。
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: tutorial
ms.date: 01/23/2021
ms.custom: devx-track-csharp
ms.openlocfilehash: e2ca5f42120661b887d07e697596f41cb7a7fce4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98745769"
---
# <a name="tutorial-index-azure-sql-data-using-the-net-sdk"></a>チュートリアル:.NET SDK を使用して Azure SQL データにインデックスを付ける

Azure SQL Database から検索可能なデータを抽出し、それを Azure Cognitive Search の検索インデックスに送信するように[インデクサー](search-indexer-overview.md)を構成します。 

このチュートリアルでは、C# と [.NET SDK](/dotnet/api/overview/azure/search) を使用して次のタスクを実行します。

> [!div class="checklist"]
> * Azure SQL Database に接続するデータ ソースを作成する
> * インデクサーの作成
> * インデクサーを実行してインデックスにデータを読み込む
> * 検証ステップとしてインデックスを照会する

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。

## <a name="prerequisites"></a>前提条件

+ [Azure SQL Database](https://azure.microsoft.com/services/sql-database/)
+ [Visual Studio](https://visualstudio.microsoft.com/downloads/)
+ [作成](search-create-service-portal.md)または[既存の検索サービスの用意](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) 

> [!Note]
> このチュートリアルには無料のサービスを使用できます。 無料の検索サービスでは、3 つのインデックス、3 つのインデクサー、3 つのデータ ソースという制限があります。 このチュートリアルでは、それぞれ 1 つずつ作成します。 開始する前に、ご利用のサービスに新しいリソースを受け入れる余地があることを確認してください。

## <a name="download-files"></a>ファイルのダウンロード

このチュートリアルのソース コードは、[Azure-Samples/search-dotnet-getting-started](https://github.com/Azure-Samples/search-dotnet-getting-started) GitHub リポジトリ内の [DotNetHowToIndexer](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowToIndexers) フォルダーにあります。

## <a name="1---create-services"></a>1 - サービスを作成する

このチュートリアルでは、インデックス作成とクエリに Azure Cognitive Search を使用し、外部データ ソースとして Azure SQL Database を使用します。 可能である場合は、近接性と管理性を高めるために、両方のサービスを同じリージョンおよびリソース グループ内に作成してください。 実際には、Azure SQL Database の場所はどのリージョンでもかまいません。

### <a name="start-with-azure-sql-database"></a>最初に Azure SQL Database で行う作業

この手順では、インデクサーがクロールできる外部データ ソースを Azure SQL Database に作成します。 Azure portal とサンプル ダウンロードの *hotels.sql* ファイルを使用して、Azure SQL Database にデータセットを作成することができます。 Azure Cognitive Search で使用されるのは、ビューやクエリから生成されるようなフラット化された行セットです。 サンプル ソリューションの SQL ファイルでは、単一のテーブルを作成してデータを投入します。

既に Azure SQL Database リソースがある場合は、hotels テーブルをそこに追加して、手順 4. から始めてください。

1. [Azure portal](https://portal.azure.com/)にサインインします。

1. **SQL データベース** を検索または作成します。 既定値および一番低い価格レベルを使用してかまいません。 サーバーを作成する利点は、後の手順でテーブルを作成して読み込むために必要な管理者ユーザー名とパスワードを指定できることです。

   :::image type="content" source="media/search-indexer-tutorial/indexer-new-sqldb.png" alt-text="[新しいデータベース] ページ" border="false":::

1. **[確認と作成]** をクリックして、新しいサーバーとデータベースをデプロイします。 サーバーとデータベースがデプロイされるまで待ちます。

1. ナビゲーション ペインで **[クエリ エディター (プレビュー)]** をクリックし、サーバー管理者のユーザー名とパスワードを入力します。 

   アクセスが拒否された場合は、エラー メッセージからクライアントの IP アドレスをコピーし、 **[サーバー ファイアウォールの設定]** リンクをクリックしてください。範囲にご自分のクライアント IP を使用して、クライアント コンピューターからのアクセスを許可するルールを追加します。 ルールが反映されるまでに数分かかる場合があります。

1. クエリ エディターで **[クエリを開く]** をクリックし、ローカル コンピューター上の *hotels.sql* ファイルの場所に移動します。 

1. ファイルを選択し、 **[開く]** をクリックします。 このスクリプトは次のスクリーンショットのようになります。

   :::image type="content" source="media/search-indexer-tutorial/sql-script.png" alt-text="SQL スクリプト" border="false":::

1. **[実行]** をクリックしてクエリを実行します。 3 つの行について、クエリが正常に実行されたことを示すメッセージが [結果] ウィンドウに表示されます。

1. このテーブルから行セットが返されるようにするには、検証ステップとして次のクエリを実行します。

    ```sql
    SELECT * FROM Hotels
    ```

1. データベース用の ADO.NET 接続文字列をコピーします。 **[設定]**  >  **[接続文字列]** で、下の例に示すような ADO.NET の接続文字列をコピーします。

    ```sql
    Server=tcp:{your_dbname}.database.windows.net,1433;Initial Catalog=hotels-db;Persist Security Info=False;User ID={your_username};Password={your_password};MultipleActiveResultSets=False;Encrypt=True;TrustServerCertificate=False;Connection Timeout=30;
    ```

この接続文字列は、次の演習で環境を設定するときに必要になります。

### <a name="azure-cognitive-search"></a>Azure Cognitive Search

次のコンポーネントは Azure Cognitive Search であり、[ポータルで作成](search-create-service-portal.md)できます。 このチュートリアルは Free レベルを使用して完了できます。 

### <a name="get-an-admin-api-key-and-url-for-azure-cognitive-search"></a>Azure Cognitive Search のための管理者 API キーと URL を取得する

API 呼び出しには、サービス URL とアクセス キーが必要です。 両方を使用して検索サービスが作成されるので、Azure Cognitive Search をサブスクリプションに追加した場合は、次の手順に従って必要な情報を入手してください。

1. [Azure portal にサインイン](https://portal.azure.com/)し、ご使用の検索サービスの **[概要]** ページで、URL を入手します。 たとえば、エンドポイントは `https://mydemo.search.windows.net` のようになります。

1. **[設定]**  >  **[キー]** で、サービスに対する完全な権限の管理キーを取得します。 管理キーをロールオーバーする必要がある場合に備えて、2 つの交換可能な管理キーがビジネス継続性のために提供されています。 オブジェクトの追加、変更、および削除の要求には、主キーまたはセカンダリ キーのどちらかを使用できます。

   :::image type="content" source="media/search-get-started-rest/get-url-key.png" alt-text="HTTP エンドポイントとアクセス キーを取得する" border="false":::

## <a name="2---set-up-your-environment"></a>2 - 環境を設定する

1. Visual Studio を起動し、**DotNetHowToIndexers.sln** を開きます。

1. ソリューション エクスプローラーで **appsettings.json** を開き、接続情報を指定します。

1. `SearchServiceEndPoint` については、サービスの概要ページの完全な URL が "https://my-demo-service.search.windows.net" である場合は、指定する値はその URL となります。

1. `AzureSqlConnectionString` には、次のような文字列形式を指定します: `"Server=tcp:{your_dbname}.database.windows.net,1433;Initial Catalog=hotels-db;Persist Security Info=False;User ID={your_username};Password={your_password};MultipleActiveResultSets=False;Encrypt=True;TrustServerCertificate=False;Connection Timeout=30;"`

    ```json
    {
      "SearchServiceEndPoint": "<placeholder-search-url>",
      "SearchServiceAdminApiKey": "<placeholder-admin-key-for-search-service>",
      "AzureSqlConnectionString": "<placeholder-ADO.NET-connection-string",
    }
    ```

1. 接続文字列に有効なパスワードが含まれていることを確認してください。 データベース名とユーザー名はコピーされますが、パスワードは手動で入力する必要があります。

## <a name="3---create-the-pipeline"></a>3 - パイプラインを作成する

インデクサーには、データ ソース オブジェクトとインデックスが必要です。 関連するコードは 2 つのファイルにあります。

  + **hotel.cs**: インデックスを定義するスキーマが含まれています。
  + **Program.cs**: サービスの構造を作成したり管理したりするための関数が含まれています。

### <a name="in-hotelcs"></a>hotel.cs の内容

インデックス スキーマは、フィールドのコレクションを定義します。この定義には、次の HotelName のフィールドの定義に見られるように、フィールドがフルテキスト検索可能かどうか、フィルター処理可能かどうか、並べ替え可能かどうかなど、許可される操作を指定する属性も含まれています。 [SearchableField](/dotnet/api/azure.search.documents.indexes.models.searchablefield) は、定義によるフルテキスト検索が可能です。 その他の属性は明示的に割り当てます。

```csharp
. . . 
[SearchableField(IsFilterable = true, IsSortable = true)]
[JsonPropertyName("hotelName")]
public string HotelName { get; set; }
. . .
```

その他にも、スキーマにはさまざまな要素が存在します。検索スコアを高めるためのスコア付けプロファイルやカスタム アナライザーなど、各種コンストラクトがその例です。 ただし、このドキュメントの目的上、スキーマに含まれている定義はごくわずかで、サンプル データセットに含まれているフィールドのみがその構成要素となります。

### <a name="in-programcs"></a>Program.cs の内容

メイン プログラムには、[インデクサー クライアント](/dotnet/api/azure.search.documents.indexes.models.searchindexer)、インデックス、データ ソース、インデクサーを作成するためのロジックが含まれます。 このコードは、読者がこのプログラムを繰り返し実行する可能性を考慮し、同じ名前のリソースが既に存在しているかどうかを調べて、削除します。

データ ソース オブジェクトの構成には、Azure SQL に組み込まれている[変更検出機能](/sql/relational-databases/track-changes/about-change-tracking-sql-server)を活用するために、[部分インデックス作成または増分インデックス作成](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md#capture-new-changed-and-deleted-rows)を含め、Azure SQL Database リソースに固有の設定が使用されます。 Azure SQL にあるソースのデモ hotels データベースには、**IsDeleted** という名前の "論理的な削除" 列があります。 データベースでこの列を true に設定すると、インデクサーによって、Azure Cognitive Search インデックスから対応するドキュメントが削除されます。

```csharp
Console.WriteLine("Creating data source...");

var dataSource =
      new SearchIndexerDataSourceConnection(
         "hotels-sql-ds",
         SearchIndexerDataSourceType.AzureSql,
         configuration["AzureSQLConnectionString"],
         new SearchIndexerDataContainer("hotels"));

indexerClient.CreateOrUpdateDataSourceConnection(dataSource);
```

インデクサー オブジェクトはプラットフォームに依存しません。構成、スケジュール、起動は、ソースに関係なく同じです。 この例のインデクサーには、スケジュールのほか、インデクサーの履歴を消去するリセット オプション、インデクサーを直ちに作成して実行するためのメソッドの呼び出しが含まれています。 インデクサーを作成または更新するには、[CreateOrUpdateIndexerAsync](/dotnet/api/azure.search.documents.indexes.searchindexerclient.createorupdateindexerasync) を使用します。

```csharp
Console.WriteLine("Creating Azure SQL indexer...");

var schedule = new IndexingSchedule(TimeSpan.FromDays(1))
{
      StartTime = DateTimeOffset.Now
};

var parameters = new IndexingParameters()
{
      BatchSize = 100,
      MaxFailedItems = 0,
      MaxFailedItemsPerBatch = 0
};

// Indexer declarations require a data source and search index.
// Common optional properties include a schedule, parameters, and field mappings
// The field mappings below are redundant due to how the Hotel class is defined, but 
// we included them anyway to show the syntax 
var indexer = new SearchIndexer("hotels-sql-idxr", dataSource.Name, searchIndex.Name)
{
      Description = "Data indexer",
      Schedule = schedule,
      Parameters = parameters,
      FieldMappings =
      {
         new FieldMapping("_id") {TargetFieldName = "HotelId"},
         new FieldMapping("Amenities") {TargetFieldName = "Tags"}
      }
};

await indexerClient.CreateOrUpdateIndexerAsync(indexer);
```

通常、インデクサーの実行はスケジュール設定されますが、開発時には [RunIndexerAsync](/dotnet/api/azure.search.documents.indexes.searchindexerclient.runindexerasync) を使用してすぐにインデクサーを実行することもできます。

```csharp
Console.WriteLine("Running Azure SQL indexer...");

try
{
      await indexerClient.RunIndexerAsync(indexer.Name);
}
catch (CloudException e) when (e.Response.StatusCode == (HttpStatusCode)429)
{
      Console.WriteLine("Failed to run indexer: {0}", e.Response.Content);
}
```

## <a name="4---build-the-solution"></a>4 - ソリューションをビルドする

F5 キーを押して、ソリューションをビルドおよび実行します。 プログラムがデバッグ モードで実行されます。 コンソール ウィンドウで各操作の状態が報告されます。

   :::image type="content" source="media/search-indexer-tutorial/console-output.png" alt-text="コンソール出力" border="false":::

コードは Visual Studio でローカルに実行され、Azure 上の検索サービスに接続されます。次に、それが Azure SQL Database に接続され、データセットが取得されます。 このように多くの操作が伴うため、障害が発生し得るポイントがいくつも存在します。 エラーが発生した場合は、まず次の条件を確認してください。

+ 指定する検索サービスの接続情報は完全な URL です。 サービス名のみを入力した場合、インデックスの作成時に接続エラーが発生して操作が停止します。

+ **appsettings.json** のデータベース接続情報。 これは、ポータルから取得した ADO.NET 接続文字列に、実際のデータベースの有効なユーザー名とパスワードを反映したものであることが必要です。 ユーザー アカウントには、データを取得するためのアクセス許可が必要です。 ローカル クライアントの IP アドレスには、ファイアウォールを介したインバウンド アクセスが許可されている必要があります。

+ リソース制限。 既に述べたように、Free レベルは、インデックス、インデクサー、データ ソースがいずれも 3 つまでに制限されています。 上限に達した場合、新しいオブジェクトを作成できなくなります。

## <a name="5---search"></a>5 - 検索する

Azure portal を使用してオブジェクトの作成を検証し、**検索エクスプローラー** を使用してインデックスを照会します。

1. [Azure portal にサインイン](https://portal.azure.com/)し、ご使用の検索サービスの **[概要]** ページで、それぞれのリストを順に開き、オブジェクトが作成されていることを確認します。 **インデックス**、**インデクサー**、**データ ソース** の名前は、それぞれ "hotels"、"azure-sql-indexer"、"azure-sql" です。

   :::image type="content" source="media/search-indexer-tutorial/tiles-portal.png" alt-text="インデクサーとデータ ソースのタイル" border="false":::

1. hotels インデックスを選択します。 hotels ページの先頭のタブは **[検索エクスプローラー]** です。 

1. **[検索]** をクリックして空のクエリを発行します。 

   インデックスの 3 つのエントリが JSON ドキュメントとして返されます。 Search エクスプローラーは、構造全体が見えるようにドキュメントを JSON 形式で返します。

   :::image type="content" source="media/search-indexer-tutorial/portal-search.png" alt-text="インデックスのクエリ" border="false":::
   
1. 次に、検索文字列として「`search=river&$count=true`」を入力します。 

   このクエリは、`river` という語についてフルテキスト検索を呼び出すもので、その結果には、一致したドキュメントの件数が含まれます。 一致したドキュメントの件数は、インデックスが大きく数千から数百万のドキュメントが含まれている場合のテストで役立ちます。 今回のケースで、このクエリに一致するドキュメントは 1 件だけです。

1. 最後に、JSON 出力を必要なフィールドに限定するため、検索文字列として「`search=river&$count=true&$select=hotelId, baseRate, description`」を入力します。 

   クエリの応答が選択フィールドに制限され、より簡潔な出力内容が得られます。

## <a name="reset-and-rerun"></a>リセットして再実行する

開発の初期の実験的な段階では、設計反復のための最も実用的なアプローチは、Azure Cognitive Search からオブジェクトを削除してリビルドできるようにすることです。 リソース名は一意です。 オブジェクトを削除すると、同じ名前を使用して再作成することができます。

このチュートリアルのサンプル コードでは、コードを再実行できるよう、既存のオブジェクトをチェックしてそれらを削除しています。

ポータルを使用して、インデックス、インデクサー、およびデータ ソースを削除することもできます。

## <a name="clean-up-resources"></a>リソースをクリーンアップする

所有するサブスクリプションを使用している場合は、プロジェクトの終了時に、不要になったリソースを削除することをお勧めします。 リソースを実行したままにすると、お金がかかる場合があります。 リソースは個別に削除することも、リソース グループを削除してリソースのセット全体を削除することもできます。

ポータルの左側のナビゲーション ペインにある [すべてのリソース] または [リソース グループ] リンクを使って、リソースを検索および管理できます。

## <a name="next-steps"></a>次のステップ

SQL Database のインデックス作成の基礎を理解したら、インデクサーの構成について詳しく見てみましょう。

> [!div class="nextstepaction"]
> [SQL Database のインデクサーを構成する](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md)