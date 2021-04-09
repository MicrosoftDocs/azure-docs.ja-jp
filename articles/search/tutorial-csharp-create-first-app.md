---
title: 最初のアプリを作成する C# チュートリアル
titleSuffix: Azure Cognitive Search
description: 最初の C# 検索アプリを作成する方法を順を追って説明します。 このチュートリアルでは、GitHub にある動作するアプリへのダウンロード リンクと、ゼロからアプリを構築する完全なプロセスの両方を提供します。
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: tutorial
ms.date: 02/26/2021
ms.custom: devx-track-csharp
ms.openlocfilehash: 0a57e45b264badffd0305eb6ac5b3c8f7c42adf3
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "101695126"
---
# <a name="tutorial-create-your-first-search-app-using-the-net-sdk"></a>チュートリアル:.NET SDK を使用して最初の検索アプリを作成する

このチュートリアルでは、Azure Cognitive Search と Visual Studio を使用して、検索インデックスに対してクエリを実行し、結果を返す Web アプリを作成する方法について説明します。

このチュートリアルでは、次の内容を学習します。

> [!div class="checklist"]
> * 開発環境をセットアップする
> * データ構造のモデルを作成する
> * クエリ入力を収集して結果を表示する Web ページを作成する
> * 検索メソッドを定義する
> * アプリケーションをテストする

検索が簡単に呼び出せることも学習します。 開発するコードの重要なステートメントは、次に示す数行の中に含まれています。

```csharp
var options = new SearchOptions()
{
    // The Select option specifies fields for the result set
    options.Select.Add("HotelName");
    options.Select.Add("Description");
};

var searchResult = await _searchClient.SearchAsync<Hotel>(model.searchText, options).ConfigureAwait(false);
model.resultList = searchResult.Value.GetResults().ToList();
```

1 回の呼び出しでインデックスにクエリを実行して、結果を返します。

:::image type="content" source="media/tutorial-csharp-create-first-app/azure-search-pool.png" alt-text="「pool」の検索" border="true":::

## <a name="overview"></a>概要

このチュートリアルでは、[データのインポートに関するクイックスタート](search-get-started-portal.md)の手順を実行して独自の検索サービスにすばやく作成できる hotels-sample-index を使用します。 このインデックスには、すべての検索サービスで組み込みのデータ ソースとして使用できる架空のホテル データが含まれています。

このチュートリアルの最初のレッスンでは基本的なクエリ構造および検索ページを作成し、以降のレッスンではページング、ファセット、入力候補を提示する機能を追加してそのページを強化します。

このコードの完成版は、次のプロジェクトにあります。

* [1-basic-search-page (GitHub)](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/create-first-app/v11/1-basic-search-page)

このチュートリアルは、Azure.Search.Documents (バージョン 11) パッケージを使用するように更新されました。 .NET SDK の以前のバージョンについては、[Microsoft.Azure.Search (バージョン 10) のコード サンプル](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/create-first-app/v10)を参照してください。

## <a name="prerequisites"></a>前提条件

* 検索サービスを[作成](search-create-service-portal.md)するか、[既存の検索サービスを検索](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices)します。

* [検索インデックスの作成に関するクイックスタート](search-get-started-portal.md)の手順を使用して、hotels-sample-index を作成します。

* [Visual Studio](https://visualstudio.microsoft.com/)

* [Azure Cognitive Search クライアント ライブラリ (バージョン 11)](https://www.nuget.org/packages/Azure.Search.Documents/)

### <a name="install-and-run-the-project-from-github"></a>GitHub からプロジェクトをインストールして実行する

動作するアプリをすぐに入手するには、次の手順に従って、完成したコードをダウンロードして実行してください。

1. GitHub で公開されている「[Create first app (初めてのアプリを作成する)](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/create-first-app/v11)」サンプルを開きます。

1. [ルート フォルダー](https://github.com/Azure-Samples/azure-search-dotnet-samples)で、 **[Code]\(コード\)** の後に **[Clone]\(クローン\)** または **[Download ZIP]\(ZIP のダウンロード\)** を選択して、プロジェクトのプライベート ローカル コピーを作成します。

1. Visual Studio を使用して、基本的な検索ページ ("1-basic-search-page") のソリューションに移動して開きます。 **[デバッグなしで開始]** を選択して (または F5 キーを押して) プログラムをビルドして実行します。

1. これは hotels インデックスであるため、ホテルの検索に使用するいくつかの単語 (例、"wifi"、"眺望"、"バー"、"駐車場") を入力し、結果を確認します。

    :::image type="content" source="media/tutorial-csharp-create-first-app/azure-search-wifi.png" alt-text="「wifi」の検索" border="true":::

うまくいけば、プロジェクトがスムーズに実行され、Web アプリが起動します。 より高度な検索で使用する重要なコンポーネントの多くは、この 1 つのアプリに含まれています。そのため、一通り体験し、ステップ バイ ステップで再作成してみるとよいでしょう。 以降のセクションでは、これらの手順について説明します。

## <a name="set-up-a-development-environment"></a>開発環境をセットアップする

このプロジェクトをゼロから作成し、Azure Cognitive Search の概念に関する知識を強化するには、Visual Studio プロジェクトから始めます。

1. Visual Studio で、 **[新規]**  >  **[プロジェクト]** 、 **[ASP.NET Core Web アプリケーション]** の順に選択します。

    :::image type="content" source="media/tutorial-csharp-create-first-app/azure-search-project1.png" alt-text="クラウド プロジェクトを作成する" border="true":::

1. プロジェクトに "FirstSearchApp" などの名前を付け、場所を設定します。 **［作成］** を選択します

1. **[Web アプリケーション (Model-View-Controller)]** プロジェクト テンプレートを選択します。

    :::image type="content" source="media/tutorial-csharp-create-first-app/azure-search-project2.png" alt-text="MVC プロジェクトを作成する" border="true":::

1. クライアント ライブラリをインストールします。 **[ツール]**  >  **[NuGet パッケージ マネージャー]**  >  **[ソリューションの NuGet パッケージの管理]** で、 **[参照]** を選択し、"azure.search.documents" を検索します。 ライセンス契約と依存関係に同意して、**Azure.Search.Documents** (バージョン 11 以降) をインストールします。

    :::image type="content" source="media/tutorial-csharp-create-first-app/azure-search-nuget-azure.png" alt-text="NuGet を使用して Azure ライブラリを追加する" border="true":::

### <a name="initialize-azure-cognitive-search"></a>Azure Cognitive Search を初期化する

このサンプルでは、一般に公開されているホテルのデータを使用します。 このデータは、架空のホテル名と説明を含む 50 個の無作為なコレクションで、デモ データを提供するためにのみ作成されたものです。 このデータにアクセスするには、名前と API キーを指定します。

1. **appsettings.json** を開き、既定の行を検索サービスの URL (`https://<service-name>.search.windows.net` 形式) と、検索サービスの [管理者またはクエリ API キー](search-security-api-keys.md)に置き換えます。 インデックスを作成または更新する必要がないため、このチュートリアルではクエリ キーを使用できます。

    ```csharp
    {
        "SearchServiceName": "<YOUR-SEARCH-SERVICE-URI>",
        "SearchServiceQueryApiKey": "<YOUR-SEARCH-SERVICE-API-KEY>"
    }
    ```

1. ソリューション エクスプローラーでそのファイルを選択し、 **[出力ディレクトリにコピー]** の設定を **[新しい場合はコピーする]** に変更します。

    :::image type="content" source="media/tutorial-csharp-create-first-app/azure-search-copy-if-newer.png" alt-text="アプリの設定を出力にコピーする" border="true":::

## <a name="model-data-structures"></a>データ構造のモデルを作成する

モデル (C# クラス) は、MVC (モデル、ビュー、コントローラー) アーキテクチャを使用してクライアント (ビュー)、サーバー (コントローラー)、および Azure クラウド間でデータ通信を行うために使用します。 通常、このモデルには、アクセス対象となるデータの構造が反映されます。

この手順では、検索インデックスのデータ構造のほか、ビューとコントローラー間の通信で使用される検索文字列をモデル化します。 hotels インデックスでは、各ホテルに多数の部屋があるほか、各ホテルに複数のパーツで構成される住所があります。 全体的に見ると、ホテルの完全な表現は、階層型の入れ子になったデータ構造です。 各コンポーネントを作成するには、3 つのクラスが必要になります。

**Hotel**、**Address**、**Room** クラスのセットは、"[*複合型*](search-howto-complex-data-types.md)" と呼ばれ、Azure Cognitive Search の重要な機能です。 複合型にはいくつもの深さのクラスとサブクラスを持つことができるので、*単純型* (プリミティブ メンバーのみを含むクラス) を使用するよりもはるかに複雑なデータ構造を表現できます。

1. ソリューション エクスプローラーで、 **[モデル]**  >  **[追加]**  >  **[新規項目]** を右クリックします。

1. **[クラス]** を選択し、項目に Hotel.cs という名前を付けます。 Hotel.cs のすべての内容を次のコードに置き換えます。 クラスの **Address** および **Room** メンバーに注意してください。これらのフィールドはそれ自体がクラスなので、それらのモデルも必要になります。

    ```csharp
    using Azure.Search.Documents.Indexes;
    using Azure.Search.Documents.Indexes.Models;
    using Microsoft.Spatial;
    using System;
    using System.Text.Json.Serialization;

    namespace FirstAzureSearchApp.Models
    {
        public partial class Hotel
        {
            [SimpleField(IsFilterable = true, IsKey = true)]
            public string HotelId { get; set; }

            [SearchableField(IsSortable = true)]
            public string HotelName { get; set; }

            [SearchableField(AnalyzerName = LexicalAnalyzerName.Values.EnLucene)]
            public string Description { get; set; }

            [SearchableField(AnalyzerName = LexicalAnalyzerName.Values.FrLucene)]
            [JsonPropertyName("Description_fr")]
            public string DescriptionFr { get; set; }

            [SearchableField(IsFilterable = true, IsSortable = true, IsFacetable = true)]
            public string Category { get; set; }

            [SearchableField(IsFilterable = true, IsFacetable = true)]
            public string[] Tags { get; set; }

            [SimpleField(IsFilterable = true, IsSortable = true, IsFacetable = true)]
            public bool? ParkingIncluded { get; set; }

            [SimpleField(IsFilterable = true, IsSortable = true, IsFacetable = true)]
            public DateTimeOffset? LastRenovationDate { get; set; }

            [SimpleField(IsFilterable = true, IsSortable = true, IsFacetable = true)]
            public double? Rating { get; set; }

            public Address Address { get; set; }

            [SimpleField(IsFilterable = true, IsSortable = true)]
            public GeographyPoint Location { get; set; }

            public Room[] Rooms { get; set; }
        }
    }
    ```

1. 同じ手順を繰り返して **Address** クラスのモデルを作成し、ファイルに Address.cs という名前を付けます。 内容を次のものに置き換えます。

    ```csharp
    using Azure.Search.Documents.Indexes;

    namespace FirstAzureSearchApp.Models
    {
        public partial class Address
        {
            [SearchableField]
            public string StreetAddress { get; set; }

            [SearchableField(IsFilterable = true, IsSortable = true, IsFacetable = true)]
            public string City { get; set; }

            [SearchableField(IsFilterable = true, IsSortable = true, IsFacetable = true)]
            public string StateProvince { get; set; }

            [SearchableField(IsFilterable = true, IsSortable = true, IsFacetable = true)]
            public string PostalCode { get; set; }

            [SearchableField(IsFilterable = true, IsSortable = true, IsFacetable = true)]
            public string Country { get; set; }
        }
    }
    ```

1. 再度同じ手順に従って **Room** クラスを作成します。ファイルの名前は Room.cs とします。

    ```csharp
    using Azure.Search.Documents.Indexes;
    using Azure.Search.Documents.Indexes.Models;
    using System.Text.Json.Serialization;

    namespace FirstAzureSearchApp.Models
    {
        public partial class Room
        {
            [SearchableField(AnalyzerName = LexicalAnalyzerName.Values.EnMicrosoft)]
            public string Description { get; set; }

            [SearchableField(AnalyzerName = LexicalAnalyzerName.Values.FrMicrosoft)]
            [JsonPropertyName("Description_fr")]
            public string DescriptionFr { get; set; }

            [SearchableField(IsFilterable = true, IsFacetable = true)]
            public string Type { get; set; }

            [SimpleField(IsFilterable = true, IsFacetable = true)]
            public double? BaseRate { get; set; }

            [SearchableField(IsFilterable = true, IsFacetable = true)]
            public string BedOptions { get; set; }

            [SimpleField(IsFilterable = true, IsFacetable = true)]
            public int SleepsCount { get; set; }

            [SimpleField(IsFilterable = true, IsFacetable = true)]
            public bool? SmokingAllowed { get; set; }

            [SearchableField(IsFilterable = true, IsFacetable = true)]
            public string[] Tags { get; set; }
        }
    }
    ```

1. このチュートリアルで作成する最後のモデルは、**SearchData** という名前のクラスであり、ユーザーの入力 (**searchText**) と検索の出力 (**resultList**) を表します。 出力の型は重要で、**SearchResults&lt;Hotel&gt;** となります。この型は検索の結果と完全に一致し、この参照をビューに渡す必要があります。 既定のテンプレートを次のコードに置き換えます。

    ```csharp
    using Azure.Search.Documents.Models;

    namespace FirstAzureSearchApp.Models
    {
        public class SearchData
        {
            // The text to search for.
            public string searchText { get; set; }

            // The list of results.
            public SearchResults<Hotel> resultList;
        }
    }
    ```

## <a name="create-a-web-page"></a>Web ページを作成する

プロジェクト テンプレートには多数のクライアント ビューが付属しています。これらは **Views** フォルダーにあります。 厳密なビューは、使用している Core .NET のバージョンによって異なります (このサンプルでは、3.1 が使用されています)。 このチュートリアルでは、検索ページの要素が含まれるように **Index.cshtml** を変更します。

Index.cshtml の内容をすべて削除し、次の手順でファイルを再構築します。

1. このチュートリアルでは、Azure のロゴと検索用の拡大鏡アイコン (azure-logo.png と search.png) という 2 つの小さな画像をビューで使用します。 GitHub プロジェクトから自分のプロジェクトの **wwwroot/images** フォルダーにその画像をコピーします。

1. Index.cshtml の最初の行では、クライアント (ビュー) とサーバー (コントローラー) 間のデータ通信に使用されるモデルを参照する必要があります。これは、先ほど作成した **SearchData** モデルになります。 次の行を Index.cshtml ファイルに追加します。

    ```csharp
    @model FirstAzureSearchApp.Models.SearchData
    ```

1. ビューにはタイトルを入力するのが一般的なので、次の行は以下のようにします。

    ```csharp
    @{
        ViewData["Title"] = "Home Page";
    }
    ```

1. タイトルの下には HTML スタイルシートへの参照を入力します。スタイルシートは後ほど作成します。

    ```csharp
    <head>
        <link rel="stylesheet" href="~/css/hotels.css" />
    </head>
    ```

1. ビューの本文では、2 つのユース ケースを処理します。 まず、検索テキストが入力される前に、初回使用時の空のページを提供する必要があります。 次に、クエリを繰り返せるように、検索テキスト ボックスに加えて結果を処理する必要があります。

   両方のケースを処理するには、ビューに提供されるモデルが null かどうかを確認する必要があります。 モデルが null の場合は、最初のユース ケース (アプリの初回実行) を示します。 Index.cshtml ファイルに次を追加し、コメントを読んでください。

    ```csharp
    <body>
    <h1 class="sampleTitle">
        <img src="~/images/azure-logo.png" width="80" />
        Hotels Search
    </h1>

    @using (Html.BeginForm("Index", "Home", FormMethod.Post))
    {
        // Display the search text box, with the search icon to the right of it.
        <div class="searchBoxForm">
            @Html.TextBoxFor(m => m.searchText, new { @class = "searchBox" }) <input class="searchBoxSubmit" type="submit" value="">
        </div>

        @if (Model != null)
        {
            // Show the result count.
            <p class="sampleText">
                @Model.resultList.Count Results
            </p>

            @for (var i = 0; i < Model.resultList.Count; i++)
            {
                // Display the hotel name and description.
                @Html.TextAreaFor(m => m.resultList[i].Document.HotelName, new { @class = "box1" })
                @Html.TextArea($"desc{i}", Model.resultList[i].Document.Description, new { @class = "box2" })
            }
        }
    }
    </body>
    ```

1. スタイルシートを追加します。 Visual Studio の **[ファイル]** >  **[新規作成]**  >  **[ファイル]** で、( **[全般]** が強調表示された状態で) **[スタイル シート]** を選択します。

   既定のコードを以下で置き換えます。 このファイルについては詳しく説明しませんが、スタイルは標準の HTML です。

    ```html
    textarea.box1 {
        width: 648px;
        height: 30px;
        border: none;
        background-color: azure;
        font-size: 14pt;
        color: blue;
        padding-left: 5px;
    }

    textarea.box2 {
        width: 648px;
        height: 100px;
        border: none;
        background-color: azure;
        font-size: 12pt;
        padding-left: 5px;
        margin-bottom: 24px;
    }

    .sampleTitle {
        font: 32px/normal 'Segoe UI Light',Arial,Helvetica,Sans-Serif;
        margin: 20px 0;
        font-size: 32px;
        text-align: left;
    }

    .sampleText {
        font: 16px/bold 'Segoe UI Light',Arial,Helvetica,Sans-Serif;
        margin: 20px 0;
        font-size: 14px;
        text-align: left;
        height: 30px;
    }

    .searchBoxForm {
        width: 648px;
        box-shadow: 0 0 0 1px rgba(0,0,0,.1), 0 2px 4px 0 rgba(0,0,0,.16);
        background-color: #fff;
        display: inline-block;
        border-collapse: collapse;
        border-spacing: 0;
        list-style: none;
        color: #666;
    }

    .searchBox {
        width: 568px;
        font-size: 16px;
        margin: 5px 0 1px 20px;
        padding: 0 10px 0 0;
        border: 0;
        max-height: 30px;
        outline: none;
        box-sizing: content-box;
        height: 35px;
        vertical-align: top;
    }

    .searchBoxSubmit {
        background-color: #fff;
        border-color: #fff;
        background-image: url(/images/search.png);
        background-repeat: no-repeat;
        height: 20px;
        width: 20px;
        text-indent: -99em;
        border-width: 0;
        border-style: solid;
        margin: 10px;
        outline: 0;
    }
    ```

1. このスタイルシート ファイルを hotels.css という名前で、既定の site.css ファイルと共に **wwwroot/css** フォルダーに保存します。

これでビューは完成です。 この時点で、モデルとビューの両方が完成しました。 残っているのは、すべてを結びつけるコントローラーのみとなりました。

## <a name="define-methods"></a>メソッドを定義する

この手順では、**ホーム コントローラー** の内容を変更します。

1. HomeController.cs ファイルを開き、**using** ステートメントを次で置き換えます。

    ```csharp
    using Azure;
    using Azure.Search.Documents;
    using Azure.Search.Documents.Indexes;
    using FirstAzureSearchApp.Models;
    using Microsoft.AspNetCore.Mvc;
    using Microsoft.Extensions.Configuration;
    using System;
    using System.Diagnostics;
    using System.Linq;
    using System.Threading.Tasks;
    ```

### <a name="add-index-methods"></a>Index メソッドを追加する

MVC アプリでは、**Index()** メソッドがすべてのコントローラーの既定のアクション メソッドです。 これによって、インデックスの HTML ページが開きます。 このチュートリアルでは、アプリケーション起動時のユース ケース (空の検索ページのレンダリング) に、パラメーターを受け取らない既定のメソッドが使用されます。

このセクションでは、もう 1 つのユース ケース (ユーザーが検索テキストを入力したときのページのレンダリング) をサポートするためにこのメソッドを拡張します。 このケースをサポートするために、モデルをパラメーターとして受け取るように index メソッドを拡張します。

1. 既定の **Index()** メソッドの後に、次のメソッドを追加します。

    ```csharp
        [HttpPost]
        public async Task<ActionResult> Index(SearchData model)
        {
            try
            {
                // Ensure the search string is valid.
                if (model.searchText == null)
                {
                    model.searchText = "";
                }

                // Make the Azure Cognitive Search call.
                await RunQueryAsync(model);
            }

            catch
            {
                return View("Error", new ErrorViewModel { RequestId = "1" });
            }
            return View(model);
        }
    ```

    メソッドの **async** 宣言と、**RunQueryAsync** の **await** 呼び出しを行っている点に注意してください。 これらのキーワードによって非同期呼び出しが処理されるため、サーバー上のスレッドはブロックされなくなります。

    **catch** ブロックでは、既定で作成されたエラー モデルを使用しています。

### <a name="note-the-error-handling-and-other-default-views-and-methods"></a>エラー処理とその他の既定のビューやメソッドに注意

使用している .NET Core のバージョンによっては、既定で作成されるビューの種類がわずかに異なる場合があります。 .NET Core 3.1 の場合、既定のビューは Index、Privacy、および Error です。 アプリを実行するとこれらの既定のページを表示することができます。また、これらがコントローラーでどのように処理されるかも確認できます。

このチュートリアルでは、後ほど Error ビューのテストを行います。

GitHub のサンプルでは、未使用のビューとそれに関連付けられたアクションが削除されています。

### <a name="add-the-runqueryasync-method"></a>RunQueryAsync メソッドを追加する

Azure Cognitive Search の呼び出しは、**RunQueryAsync** メソッド内にカプセル化されます。

1. まず、Azure のサービスを設定するいくつかの静的変数を追加し、それらを開始する呼び出しを行います。

    ```csharp
        private static SearchClient _searchClient;
        private static SearchIndexClient _indexClient;
        private static IConfigurationBuilder _builder;
        private static IConfigurationRoot _configuration;

        private void InitSearch()
        {
            // Create a configuration using appsettings.json
            _builder = new ConfigurationBuilder().AddJsonFile("appsettings.json");
            _configuration = _builder.Build();

            // Read the values from appsettings.json
            string searchServiceUri = _configuration["SearchServiceUri"];
            string queryApiKey = _configuration["SearchServiceQueryApiKey"];

            // Create a service and index client.
            _indexClient = new SearchIndexClient(new Uri(searchServiceUri), new AzureKeyCredential(queryApiKey));
            _searchClient = _indexClient.GetSearchClient("hotels");
        }
    ```

2. **RunQueryAsync** メソッドの本体を追加します。

    ```csharp
    private async Task<ActionResult> RunQueryAsync(SearchData model)
    {
        InitSearch();

        var options = new SearchOptions() { };

        // Enter Hotel property names into this list so only these values will be returned.
        // If Select is empty, all values will be returned, which can be inefficient.
        options.Select.Add("HotelName");
        options.Select.Add("Description");

        // For efficiency, the search call should be asynchronous, so use SearchAsync rather than Search.
        var searchResult = await _searchClient.SearchAsync<Hotel>(model.searchText, options).ConfigureAwait(false);
        model.resultList = searchResult.Value.GetResults().ToList();

        // Display the results.
        return View("Index", model);
    }
    ```

    このメソッドでは、まず Azure の構成が開始されていることを確認してから、いくつかの検索オプションを設定します。 **Select** オプションは、結果で返すフィールドを指定するため、**hotel** クラスのプロパティ名と照合します。 **Select** を省略すると、非表示でないフィールドがすべて返されます。これは、使用可能なすべてのフィールドのサブセットにしか関心がない場合に非効率的である可能性があります。

    検索の非同期呼び出しにより、(**searchText** としてモデル化された) 要求と (**searchResult** としてモデル化された) 応答が作成されます。 このコードのデバッグ時に、**model.resultList** の内容を確認する必要がある場合は、**SearchResult** クラスがブレークポイントを設定するのに適しています。 要求したデータが提供され、余分なものはほとんど含まれていないので、直感的だと感じるはずです。

### <a name="test-the-app"></a>アプリケーションをテストする

それでは、アプリが正しく動作するかどうかを確認しましょう。

1. **[デバッグ]**  >  **[デバッグなしで開始]** を選択するか、**F5** キーを押します。 アプリが想定どおりに動作すると、最初の Index ビューが表示されます。

     :::image type="content" source="media/tutorial-csharp-create-first-app/azure-search-index.png" alt-text="アプリを開く" border="true":::

1. "beach" などのクエリ文字列 (または任意のテキスト) を入力し、検索アイコンをクリックして要求を送信します。

     :::image type="content" source="media/tutorial-csharp-create-first-app/azure-search-beach.png" alt-text="「beach」の検索" border="true":::

1. 「five star」と入力してみてください。 このクエリでは結果が返されないことに注意してください。 より高度な検索では、「five star」を「luxury」のシノニムとして扱い、その結果を返します。 [シノニム](search-synonyms.md)のサポートは Azure Cognitive Search で利用できますが、これについてはこのチュートリアル シリーズでは説明していません。

1. 検索テキストとして「hot」を入力してみてください。 「hotel」という単語を含むエントリは _返されません_。 いくつかの結果が返されますが、この検索では単語全体を検索しています。

1. 「pool」、「sunshine」、「view」など、その他の単語を試してみてください。 Azure Cognitive Search は非常に単純な動作をしていますが、それでも納得できるレベルであることがわかります。

## <a name="test-edge-conditions-and-errors"></a>エッジ条件とエラーをテストする

完璧に動作している場合でも、エラー処理機能が想定どおりに動作することを確認しておくのは重要です。 

1. **Index** メソッドの **try {** 呼び出しの後に、**Throw new Exception()** 行を入力します。 この例外により、テキストを検索するとエラーが強制的に発生します。

2. アプリを実行し、検索テキストに「bar」と入力して検索アイコンをクリックします。 例外が発生し、エラー ビューが表示されるはずです。

     :::image type="content" source="media/tutorial-csharp-create-first-app/azure-search-error.png" alt-text="エラーを強制する" border="true":::

    > [!Important]
    > エラー ページで内部エラー番号を返すのは、セキュリティ上のリスクと見なされます。 アプリを一般的な用途に使用する場合は、エラーが発生した場合に返される内容について、安全なベスト プラクティスを調査してください。

3. エラー処理が想定どおりに動作することが確認できたら、**Throw new Exception()** を削除します。

## <a name="takeaways"></a>重要なポイント

このプロジェクトの重要なポイントを示します。

* Azure Cognitive Search の呼び出しは簡潔で、結果の解釈は簡単です。
* 非同期呼び出しを使用すると、コントローラーはわずかに複雑になります。しかし、高品質なアプリを開発する場合は、非同期呼び出しを使用するのがベスト プラクティスです。
* このアプリでは、**searchOptions** に設定された内容によって定義された簡単なテキスト検索を実行しました。 ただし、この 1 つのクラスには、高度な検索を行うことができる多数のメンバーを追加できます。 このアプリを大幅に強化するために、大規模な追加作業は必要ありません。

## <a name="next-steps"></a>次のステップ

ユーザー エクスペリエンスを向上させるには、さらに機能を追加します。中でも重要なのが、ページング (ページ番号または無限スクロールを使用) やオートコンプリートまたは入力候補の機能です。 また、より高度な検索オプション (たとえば、特定の地点から指定した半径内にあるホテルの検索や、検索結果の並び替え) についても検討できます。

これらの次の手順は、残りのチュートリアルの中で説明します。 ページングから見ていきましょう。

> [!div class="nextstepaction"]
> [C# チュートリアル: 検索結果のページング - Azure Cognitive Search](tutorial-csharp-paging.md)