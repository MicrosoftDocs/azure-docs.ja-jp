---
title: 初めてのアプリを作成する C# チュートリアル - Azure Search
description: このチュートリアルでは、Azure Search の最初のアプリを構築するためのステップバイステップ ガイドを提供します。 このチュートリアルでは、GitHub で公開されている動作するアプリへのリンクと、ゼロからアプリを構築する完全なプロセスの両方を提供します。 Azure Search の重要なコンポーネントについて説明します。
services: search
ms.service: search
ms.topic: tutorial
ms.author: v-pettur
author: peterturcan
ms.date: 05/01/2019
ms.openlocfilehash: d569437a3e6f6f05ddb9c6fa85f62c77ac51f72b
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/28/2019
ms.locfileid: "67443818"
---
# <a name="c-tutorial-create-your-first-app---azure-search"></a>C# のチュートリアル: 初めてのアプリを作成する - Azure Search

Azure Search を使用してクエリを実行し、インデックスから検索結果を表示する Web インターフェイスを作成する方法について説明します。 このチュートリアルは、検索ページの構築に集中することができるように、ホストされている既存のインデックスを使用して始めます。 インデックスには、架空のホテルのデータが含まれています。 基本的なページができた後は、以降のレッスンでページング、ファセット、および入力候補を提示する機能を追加し、ページを強化できます。

このチュートリアルでは、以下の内容を学習します。
> [!div class="checklist"]
> * 開発環境をセットアップする
> * データ構造のモデルを作成する
> * Web ページを作成する
> * メソッドを定義する
> * アプリケーションをテストする

検索が簡単に呼び出せることも学習します。 開発するコードの重要なステートメントは、次に示す数行の中に含まれています。

```cs
var parameters = new SearchParameters
{
    // Enter Hotel property names into this list, so only these values will be returned.
    Select = new[] { "HotelName", "Description" }
};

DocumentSearchResult<Hotel> results  = await _indexClient.Documents.SearchAsync<Hotel>("search text", parameters);
```

この 1 回の呼び出しで、Azure データの検索を開始して結果を返します。

![「pool」の検索](./media/tutorial-csharp-create-first-app/azure-search-pool.png)


## <a name="prerequisites"></a>前提条件

このチュートリアルを完了するには、以下を実行する必要があります。

IDE として使用するために [Visual Studio をインストール](https://visualstudio.microsoft.com/)します。

### <a name="install-and-run-the-project-from-github"></a>GitHub からプロジェクトをインストールして実行する

1. GitHub で公開されている「[Create first app (初めてのアプリを作成する)](https://github.com/Azure-Samples/azure-search-dotnet-samples)」サンプルを開きます。
1. **[Clone or download]** を選択し、プロジェクトのプライベート ローカル コピーを作成します。
1. Visual Studio を使用し、基本的な検索ページ (basic-search-page) のソリューションに移動して開きます。続いて **[デバッグなしで開始]** を選択します (または F5 キーを押します)。
1. いくつかの単語 (たとえば「wifi」、「view」、「bar」、「parking」) を入力し、結果を確認します。

    ![「wifi」の検索](./media/tutorial-csharp-create-first-app/azure-search-wifi.png)

うまくいけば、プロジェクトがスムーズに実行され、Azure アプリが起動します。 より高度な検索で使用する重要なコンポーネントの多くは、この 1 つのアプリに含まれています。そのため、一通り体験し、ステップ バイ ステップで再作成してみるとよいでしょう。

ゼロからこのプロジェクトを作成し、Azure Search のコンポーネントに関する知識を強化するには、次の手順を実行します。

## <a name="set-up-a-development-environment"></a>開発環境をセットアップする

1. Visual Studio 2017 以降では、 **[新規/プロジェクト]** 、 **[ASP.NET Core Web アプリケーション]** の順に選択します。 プロジェクトには、「FirstAzureSearchApp」などの名前を付けます。

    ![クラウド プロジェクトを作成する](./media/tutorial-csharp-create-first-app/azure-search-project1.png)

2. このプロジェクトの種類で **[OK]** をクリックすると、プロジェクトに適用する 2 番目のオプション セットが表示されます。 **[Web アプリケーション (モデル ビュー コントローラー)]** を選択します。

    ![MVC プロジェクトを作成する](./media/tutorial-csharp-create-first-app/azure-search-project2.png)

3. 次に、 **[ツール]** メニューから、 **[Nuget パッケージ マネージャー]** 、 **[ソリューションの NuGet パッケージの管理]** の順に選択します。1 つのパッケージをインストールする必要があります。 **[参照]** タブを選択し、検索ボックスに「Azure Search」と入力します。 **[Microsoft.Azure.Search]** が一覧に表示されたら、それをインストールします (バージョン 9.0.1、またはそれ以降)。 インストールを完了するには、他にいくつかのダイアログ ボックスをクリックする必要があります。

    ![NuGet を使用して Azure ライブラリを追加する](./media/tutorial-csharp-create-first-app/azure-search-nuget-azure.png)

### <a name="initialize-azure-search"></a>Azure Search を初期化する

このサンプルでは、一般に公開されているホテルのデータを使用します。 このデータは、架空のホテル名と説明を含む 50 個の無作為なコレクションで、デモ データを提供するためにのみ作成されたものです。 このデータにアクセスするには、名前とキーを指定する必要があります。

1. 新しいプロジェクトで appsettings.json ファイルを開き、次の名前とキーを使って既定の行を置き換えます。 ここに示す API キーはキーの例ではありません。API キーは、ホテルのデータにアクセスするために必要になる_正確な_キーです。 appsettings.json ファイルは、次のようになります。

    ```cs
    {
        "SearchServiceName": "azs-playground",
        "SearchServiceQueryApiKey": "EA4510A6219E14888741FCFC19BFBB82"
    }
    ```

2. このファイルはまだ完成してはいません。このファイルのプロパティを選択し、 **[出力ディレクトリにコピー]** 設定を **[新しい場合はコピーする]** に変更します。

    ![アプリの設定を出力にコピーする](./media/tutorial-csharp-create-first-app/azure-search-copy-if-newer.png)

## <a name="model-data-structures"></a>データ構造のモデルを作成する

モデル (C# クラス) は、MVC (モデル、ビュー、コントローラー) アーキテクチャを使用してクライアント (ビュー)、サーバー (コントローラー)、および Azure クラウド間でデータ通信を行うために使用します。 通常、このモデルには、アクセス対象となるデータの構造が反映されます。 また、ビューとコントローラー間の通信処理にもモデルが必要です。

1. ソリューション エクスプローラーを使用してプロジェクトの **Models** フォルダーを開くと、1 つの既定のモデル **ErrorViewModel.cs** が表示されます。

2. **Models** フォルダーを右クリックし、 **[追加]** 、 **[新しい項目]** の順に選択します。 次に、表示されるダイアログ ボックスで、 **[ASP.NET Core]** 、最初のオプションである **[クラス]** の順に選択します。 .cs ファイルの名前を Hotel.cs に変更し、 **[追加]** をクリックします。 Hotel.cs のすべての内容を次のコードに置き換えます。 クラスの **Address** および **Room** メンバーに注意してください。これらのフィールドはそれ自体がクラスなので、モデルが必要になります。

    ```cs
    using System;
    using Microsoft.Azure.Search;
    using Microsoft.Azure.Search.Models;
    using Microsoft.Spatial;
    using Newtonsoft.Json;

    namespace FirstAzureSearchApp.Models
    {
        public partial class Hotel
        {
            [System.ComponentModel.DataAnnotations.Key]
            [IsFilterable]
            public string HotelId { get; set; }

            [IsSearchable, IsSortable]
            public string HotelName { get; set; }

            [IsSearchable]
            [Analyzer(AnalyzerName.AsString.EnLucene)]
            public string Description { get; set; }

            [IsSearchable]
            [Analyzer(AnalyzerName.AsString.FrLucene)]
            [JsonProperty("Description_fr")]
            public string DescriptionFr { get; set; }

            [IsSearchable, IsFilterable, IsSortable, IsFacetable]
            public string Category { get; set; }

            [IsSearchable, IsFilterable, IsFacetable]
            public string[] Tags { get; set; }

            [IsFilterable, IsSortable, IsFacetable]
            public bool? ParkingIncluded { get; set; }

            [IsFilterable, IsSortable, IsFacetable]
            public DateTimeOffset? LastRenovationDate { get; set; }

            [IsFilterable, IsSortable, IsFacetable]
            public double? Rating { get; set; }

            public Address Address { get; set; }

            [IsFilterable, IsSortable]
            public GeographyPoint Location { get; set; }

            public Room[] Rooms { get; set; }
        }
    }
    ```

3. 同じ手順に従い、**Address** クラスのモデルを作成します。ただし、ファイルの名前は Address.cs とします。 内容を次のものに置き換えます。

    ```cs
    using Microsoft.Azure.Search;

    namespace FirstAzureSearchApp.Models
    {
        public partial class Address
        {
            [IsSearchable]
            public string StreetAddress { get; set; }

            [IsSearchable, IsFilterable, IsSortable, IsFacetable]
            public string City { get; set; }

            [IsSearchable, IsFilterable, IsSortable, IsFacetable]
            public string StateProvince { get; set; }

            [IsSearchable, IsFilterable, IsSortable, IsFacetable]
            public string PostalCode { get; set; }

            [IsSearchable, IsFilterable, IsSortable, IsFacetable]
            public string Country { get; set; }
        }
    }
    ```

4. 再度同じ手順に従って **Room** クラスを作成します。ファイルの名前は Room.cs とします。 ここでも、内容を次のものに置き換えます。

    ```cs
    using Microsoft.Azure.Search;
    using Microsoft.Azure.Search.Models;
    using Newtonsoft.Json;

    namespace FirstAzureSearchApp.Models
    {
        public partial class Room
        {
            [IsSearchable]
            [Analyzer(AnalyzerName.AsString.EnMicrosoft)]

            public string Description { get; set; }

            [IsSearchable]
            [Analyzer(AnalyzerName.AsString.FrMicrosoft)]
            [JsonProperty("Description_fr")]
            public string DescriptionFr { get; set; }

            [IsSearchable, IsFilterable, IsFacetable]
            public string Type { get; set; }

            [IsFilterable, IsFacetable]
            public double? BaseRate { get; set; }

            [IsSearchable, IsFilterable, IsFacetable]
            public string BedOptions { get; set; }

            [IsFilterable, IsFacetable]

            public int SleepsCount { get; set; }

            [IsFilterable, IsFacetable]
            public bool? SmokingAllowed { get; set; }

            [IsSearchable, IsFilterable, IsFacetable]
            public string[] Tags { get; set; }
        }
    }
    ```

5. **Hotel**、**Address**、および **Room** の各クラスは、Azure で[_複合型_](search-howto-complex-data-types.md)と呼ばれています。これは、Azure Search の重要な機能です。 複合型にはいくつもの深さのクラスとサブクラスを持つことができるので、_単純型_ (プリミティブ メンバーのみを含むクラス) を使用するよりもはるかに複雑なデータ構造を表現できます。 もう 1 つモデルが必要なので、再度新しいモデル クラスを作成する手順を実行します。今回はクラスを SearchData.cs と呼び、既定のコードを以下で置き換えます。

    ```cs
    using Microsoft.Azure.Search.Models;

    namespace FirstAzureSearchApp.Models
    {
        public class SearchData
        {
            // The text to search for.
            public string searchText { get; set; }

            // The list of results.
            public DocumentSearchResult<Hotel> resultList;
        }
    }
    ```

    このクラスには、ユーザーの入力 (**searchText**) と検索の出力 (**resultList**) が含まれています。 出力の型は重要で、**DocumentSearchResult&lt;Hotel&gt;** となります。この型は検索からの結果と完全に一致し、この型への参照をビューに渡す必要があります。



## <a name="create-a-web-page"></a>Web ページを作成する

既定の状態では、作成したプロジェクトはたくさんのクライアント ビューを作成します。 厳密なビューの数は、使用する Core .NET のバージョンに依存します (このサンプルでは、2.1 を使用しています)。 これらは、すべてプロジェクトの **Views** フォルダーに格納されます。 変更する必要があるのは、Index.cshtml ファイル (**Views/Home** フォルダーにあります) のみです。

Index.cshtml の内容をすべて削除し、次の手順でファイルを再構築します。

1. ビューの中では、2 つの小さいイメージを使用します。 独自のイメージ使用することも、GitHub プロジェクトからイメージ azure logo.png および search.png をコピーすることもできます。 これら 2 つのイメージは、**wwwroot/images** フォルダーに配置する必要があります。

2. Index.cshtml の最初の行では、クライアント (ビュー) とサーバー (コントローラー) との間でデータ通信を行うモデルを参照する必要があります。これは、先ほど作成した **SearchData** モデルになります。 次の行を Index.cshtml ファイルに追加します。

    ```cs
    @model FirstAzureSearchApp.Models.SearchData
    ```

3. ビューにはタイトルを入力するのが一般的なので、次の行は以下のようにします。

    ```cs
    @{
        ViewData["Title"] = "Home Page";
    }
    ```

4. タイトルの下には HTML スタイルシートへの参照を入力します。スタイルシートは後ほど作成します。

    ```cs
    <head>
        <link rel="stylesheet" href="~/css/hotels.css" />
    </head>
    ```

5. ビューの本体に取りかかります。 覚えておくべき重要な点は、ビューが 2 つの状況を処理しなければならないことです。 まず、アプリが最初に起動され、ユーザーが検索テキストを何も入力していないときの表示に対応する必要があります。 次に、ユーザーが再利用できるように、検索テキスト ボックスと結果の表示に対応する必要があります。 これら 2 つの状況に対応するには、ビューに提供されるモデルが null かどうかを確認する必要があります。 モデルが null の場合、2 つの状況のうち 1 つ目にあることを示しています (アプリの最初の実行)。 Index.cshtml ファイルに次を追加し、コメントを読んでください。

    ```cs
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
                @Html.DisplayFor(m => m.resultList.Results.Count) Results
            </p>

            @for (var i = 0; i < Model.resultList.Results.Count; i++)
            {
                // Display the hotel name and description.
                @Html.TextAreaFor(m => Model.resultList.Results[i].Document.HotelName, new { @class = "box1" })
                @Html.TextArea($"desc{i}", Model.resultList.Results[i].Document.Description, new { @class = "box2" })
            }
        }
    }
    </body>
    ```

6. 最後に、スタイルシートを追加します。 Visual Studio の **[ファイル]** メニューで、 **[新規/ファイル]** 、 **[スタイル シート]** ( **[General] (汎用)** が強調表示された状態) の順に選択します。 既定のコードを以下で置き換えます。 このファイルについては詳しく説明しませんが、スタイルは標準の HTML です。

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

7. スタイルシートのファイルを、既定の site.css ファイルがある wwwroot/css フォルダーに hotels.css という名前で保存します。

これでビューは完成です。 作業はかなり進んでいます。 モデルとビューが完成し、すべてを結びつけるコントローラーを残すのみとなりました。

## <a name="define-methods"></a>メソッドを定義する

1 つのコントローラーの内容を変更する必要があります (**Home Controller**)。これは既定で作成されています。

1. HomeController.cs ファイルを開き、**using** ステートメントを次で置き換えます。

    ```cs
    using System;
    using System.Diagnostics;
    using System.Threading.Tasks;
    using Microsoft.AspNetCore.Mvc;
    using FirstAzureSearchApp.Models;
    using Microsoft.Extensions.Configuration;
    using Microsoft.Azure.Search;
    using Microsoft.Azure.Search.Models;
    ```

### <a name="add-index-methods"></a>Index メソッドを追加する

2 つの **Index** メソッドが必要です。1 つはパラメーターがないもの (アプリを初めて開いた場合用)、もう 1 つはパラメーターとしてモデルを受け取るもの (ユーザーが検索テキストを入力した場合用) です。 1 つ目のメソッドは、既定で作成されています。 

1. 既定の **Index()** メソッドの後に、次のメソッドを追加します。

    ```cs
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

                // Make the Azure Search call.
                await RunQueryAsync(model);
            }

            catch
            {
                return View("Error", new ErrorViewModel { RequestId = "1" });
            }
            return View(model);
        }
    ```

    メソッドの **async** 宣言と、**RunQueryAsync** の **await** 呼び出しを行っている点に注意してください。 これらのキーワードを使うと、呼び出しが非同期で行われるので、サーバー上のスレッドがブロックされることを回避できます。

    **catch** ブロックには、既定で作成されたエラー モデルを使用しています。

### <a name="note-the-error-handling-and-other-default-views-and-methods"></a>エラー処理とその他の既定のビューやメソッドに注意

使用している .NET Core のバージョンによっては、既定で作成されるビューの種類がわずかに異なる場合があります。 .NET Core 2.1 の既定のビューは、Index、About、Contact、Privacy、および Error です。 たとえば .NET Core 2.2 では、既定のビューは Index、Privacy、および Error です。 どちらの場合も、アプリを実行するとこれらの既定のページを表示することができます。また、これらがコントローラーでどのように処理されるかも確認できます。

このチュートリアルでは、後ほど Error ビューのテストを行います。

GitHub のサンプルでは、未使用のビューとそれに関連する操作を削除しています。

### <a name="add-the-runqueryasync-method"></a>RunQueryAsync メソッドを追加する

Azure Search の呼び出しは、**RunQueryAsync** メソッド内にカプセル化されます。

1. まず、Azure のサービスを設定するいくつかの静的変数を追加し、それらを開始する呼び出しを行います。

    ```cs
        private static SearchServiceClient _serviceClient;
        private static ISearchIndexClient _indexClient;
        private static IConfigurationBuilder _builder;
        private static IConfigurationRoot _configuration;

        private void InitSearch()
        {
            // Create a configuration using the appsettings file.
            _builder = new ConfigurationBuilder().AddJsonFile("appsettings.json");
            _configuration = _builder.Build();

            // Pull the values from the appsettings.json file.
            string searchServiceName = _configuration["SearchServiceName"];
            string queryApiKey = _configuration["SearchServiceQueryApiKey"];

            // Create a service and index client.
            _serviceClient = new SearchServiceClient(searchServiceName, new SearchCredentials(queryApiKey));
            _indexClient = _serviceClient.Indexes.GetClient("hotels");
        }
    ```

2. **RunQueryAsync** メソッドの本体を追加します。

    ```cs
        private async Task<ActionResult> RunQueryAsync(SearchData model)
        {
            InitSearch();

            var parameters = new SearchParameters
            {
                // Enter Hotel property names into this list so only these values will be returned.
                // If Select is empty, all values will be returned, which can be inefficient.
                Select = new[] { "HotelName", "Description" }
            };

            // For efficiency, the search call should be asynchronous, so use SearchAsync rather than Search.
            model.resultList = await _indexClient.Documents.SearchAsync<Hotel>(model.searchText, parameters);

            // Display the results.
            return View("Index", model);
        }
    ```

    このメソッドでは、まず Azure の構成が開始されていることを確認し、次にいくつかの検索パラメーターを設定します。 **Select** パラメーター内のフィールドの名前は、**Hotel** クラスのプロパティ名に正確に一致しています。 **Select** パラメーターは省略することも可能です。その場合、すべてのプロパティが返されます。 ただし、データのサブセットのみに関心がある場合、**Select** パラメーターを設定しないのは効率的ではありません。 関心のあるプロパティを指定すると、そのプロパティのみが返されます。

    検索の非同期呼び出し (**model.resultList = await _indexClient.Documents.SearchAsync&lt;Hotel&gt;(model.searchText, parameters);** ) は、このチュートリアルとアプリの根幹をなす部分です。 **DocumentSearchResult** は興味深いクラスです。(アプリが実行されているときに) ここにブレークポイントを設定し、デバッガーを使用して **model.resultList** の内容を調べてみることをお勧めします。 要求したデータが提供され、余分なものはほとんど含まれていないので、直感的だと感じるはずです。

ここからが正念場です。

### <a name="test-the-app"></a>アプリケーションをテストする

では、アプリが正しく実行できるかを確認しましょう。

1. **[デバッグ/デバッグなしで開始]** を選択するか、F5 キーを押します。 正しくコーディングされている場合は、初期状態の Index ビューが表示されます。

     ![アプリを開く](./media/tutorial-csharp-create-first-app/azure-search-index.png)

2. 「beach」などのテキスト (または任意のテキスト) を入力し、検索アイコンをクリックします。 いくつかの結果が返されるはずです。

     ![「beach」の検索](./media/tutorial-csharp-create-first-app/azure-search-beach.png)

3. 「five star」と入力してみてください。 結果が返されないことに注意してください。 より高度な検索では、「five star」を「luxury」のシノニムとして扱い、その結果を返します。 Azure Search ではシノニムを使用することもできますが、最初のチュートリアルでは説明しません。
 
4. 検索テキストとして「hot」を入力してみてください。 「hotel」という単語を含むエントリは_返されません_。 いくつかの結果が返されますが、この検索では単語全体を検索しています。

5. 「pool」、「sunshine」、「view」など、その他の単語を試してみてください。 Azure Search は非常に単純な動作をしていますが、それでも納得できるレベルであることがわかります。

## <a name="test-edge-conditions-and-errors"></a>エッジ条件とエラーをテストする

完璧に動作している場合でも、エラー処理機能が想定どおりに動作することを確認しておくのは重要です。 

1. **Index** メソッドの **try {** 呼び出しの後に、**Throw new Exception()** 行を入力します。 テキストを検索すると、この例外によって強制的にエラーが発生します。

2. アプリを実行し、検索テキストに「bar」と入力して検索アイコンをクリックします。 例外が発生し、エラー ビューが表示されるはずです。

     ![エラーを強制する](./media/tutorial-csharp-create-first-app/azure-search-error.png)

    > [!Important]
    > エラー ページで内部エラー番号を返すのは、セキュリティ上のリスクと見なされます。 アプリを一般的な用途に使用する場合は、エラーが発生した場合に返される内容について、安全なベスト プラクティスを調査してください。

3. エラー処理が想定どおりに動作することが確認できたら、**Throw new Exception()** を削除します。

## <a name="takeaways"></a>重要なポイント

このプロジェクトの重要なポイントを示します。

* Azure Search の呼び出しは簡潔で、結果の解釈は簡単です。
* 非同期呼び出しを使用すると、コントローラーはわずかに複雑になります。しかし、高品質なアプリを開発する場合は、非同期呼び出しを使用するのがベスト プラクティスです。
* このアプリでは、**searchParameters** に設定された内容に応じて、簡単なテキスト検索を実行しました。 ただし、この 1 つのクラスには、高度な検索を行うことができる多数のメンバーを追加できます。 このアプリを大幅に強化するために、大規模な追加作業は必要ありません。

## <a name="next-steps"></a>次の手順

Azure Search を使用して最適なユーザー エクスペリエンスを提供するためには、さらに機能を追加する必要があります。中でも重要なのが、ページング (ページ番号または無限スクロールを使用) やオートコンプリート/入力候補の機能です。 高度な検索パラメーター (たとえば、特定の地点から指定した半径内にあるホテルの検索や、検索結果の並び替え) についても、検討する必要があります。

これらの次の手順は、一連のチュートリアルの中で説明します。 ページングから見ていきましょう。

> [!div class="nextstepaction"]
> [C# のチュートリアル: 検索結果のページング- Azure Search](tutorial-csharp-paging.md)


