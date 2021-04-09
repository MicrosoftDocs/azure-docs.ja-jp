---
title: ファセットを使用してナビゲーションを支援する C# チュートリアル
titleSuffix: Azure Cognitive Search
description: "\"ページング結果\" から続けて、ファセット ナビゲーションを追加します。 ファセットを使用して簡単に検索を絞り込む方法について説明します。"
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: tutorial
ms.date: 01/26/2021
ms.custom: devx-track-csharp
ms.openlocfilehash: 44a05ed5b4386f2787a1c84dfeb61a5d539c7cb5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98789805"
---
# <a name="tutorial-add-faceted-navigation-using-the-net-sdk"></a>チュートリアル:.NET SDK を使用してファセット ナビゲーションを追加する

ファセットを使用すると、結果をフィルター処理するための一連のリンクを提供することによって、自己誘導型のナビゲーションを実現できます。 このチュートリアルでは、ファセット ナビゲーション構造をページの左側に配置し、ラベルとクリック可能なテキストを使用して結果をトリミングします。

このチュートリアルでは、以下の内容を学習します。
> [!div class="checklist"]
> * モデルのプロパティに _IsFacetable_ を設定する
> * アプリにファセット ナビゲーションを追加する

## <a name="overview"></a>概要

ファセットは、検索インデックスのフィールドに基づきます。 facet=[文字列] を含むクエリ要求によって、ファセットの対象となるフィールドが提供されます。 `&facet=category&facet=amenities` のように複数のファセットをアンパサンド (&) 文字で区切って指定するのが一般的です。 ファセット ナビゲーション構造を実装するには、ファセットとフィルターの両方を指定する必要があります。 フィルターは、結果を絞り込むためにクリック イベントで使用されます。 たとえば、[予算] をクリックすると、その条件に基づいて結果がフィルター処理されます。

このチュートリアルでは、[検索結果へのページングの追加](tutorial-csharp-paging.md)に関するチュートリアルで作成したページング プロジェクトを拡張します。

このチュートリアルのコードの完成版は、次のプロジェクトにあります。

* [4-add-facet-navigation (GitHub)](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/create-first-app/v11/4-add-facet-navigation)

## <a name="prerequisites"></a>前提条件

* [2a-add-paging (GitHub)](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/create-first-app/v11/2a-add-paging) ソリューション。 このプロジェクトは、前のチュートリアルで作成した独自のバージョンでも、GitHub からコピーしたものでもかまいません。

このチュートリアルは、[Azure.Search.Documents (バージョン 11)](https://www.nuget.org/packages/Azure.Search.Documents/) パッケージを使用するように更新されました。 .NET SDK の以前のバージョンについては、[Microsoft.Azure.Search (バージョン 10) のコード サンプル](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/create-first-app/v10)を参照してください。

## <a name="set-model-properties-as-isfacetable"></a>モデルのプロパティに IsFacetable を設定する

ファセット検索でモデルのプロパティを検索できるようにするには、**IsFacetable** タグを付ける必要があります。

1. **Hotel** クラスを確認します。 **Category** や **Tags** などには **IsFacetable** タグが付けられていますが、**HotelName** や **Description** には付けられていません。 

    ```cs
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
    ```

1. このチュートリアルではタグの変更は行わないので、何も変更せずに hotel.cs ファイルを閉じます。

    > [!Note]
    > ファセット検索で要求されたフィールドが適切にタグ付けされていない場合、検索時にエラーがスローされます。

## <a name="add-facet-navigation-to-your-app"></a>アプリにファセット ナビゲーションを追加する

この例では、結果の左側に表示されるリンクの一覧を使って、ユーザーがホテルのカテゴリまたはアメニティを 1 つ選択できるようにします。 ユーザーは、検索テキストを入力した後、カテゴリまたはアメニティを選択して検索結果を段階的に絞り込みます。

ファセットのリストをビューに渡すのはコントローラーの仕事です。 検索の進行中に応じてユーザーの選択を維持するために、状態を保持するメカニズムとして一時ストレージを使用します。

![ファセット ナビゲーションを使用して「pool」の検索を絞り込む](./media/tutorial-csharp-create-first-app/azure-search-facet-nav.png)

### <a name="add-filter-strings-to-the-searchdata-model"></a>SearchData モデルにフィルター文字列を追加する

1. SearchData.cs ファイルを開き、**SearchData** クラスに文字列プロパティを追加します。これは、ファセット フィルター文字列を保持するために使用されます。

    ```cs
    public string categoryFilter { get; set; }
    public string amenityFilter { get; set; }
    ```

### <a name="add-the-facet-action-method"></a>ファセット アクション メソッドを追加する

home コントローラーには、1 つの新しい **Facet** アクションが必要です。また、既存の **Index** および **Page** アクション、さらに **RunQueryAsync** メソッドを更新する必要があります。

<!-- 1. Open the home controller file, and add the **using** statement, to enable the **List&lt;string&gt;** construct.

    ```cs
    using System.Collections.Generic; 
    ```-->

1. **Index(SearchData model)** アクション メソッドを置き換えます。

    ```cs
    public async Task<ActionResult> Index(SearchData model)
    {
        try
        {
            // Ensure the search string is valid.
            if (model.searchText == null)
            {
                model.searchText = "";
            }

            // Make the search call for the first page.
            await RunQueryAsync(model, 0, 0, "", "").ConfigureAwait(false);
        }
        catch
        {
            return View("Error", new ErrorViewModel { RequestId = "1" });
        }

        return View(model);
    }
    ```

1. **PageAsync(SearchData model)** アクション メソッドを置き換えます。

    ```cs
    public async Task<ActionResult> PageAsync(SearchData model)
    {
        try
        {
            int page;

            // Calculate the page that should be displayed.
            switch (model.paging)
            {
                case "prev":
                    page = (int)TempData["page"] - 1;
                    break;

                case "next":
                    page = (int)TempData["page"] + 1;
                    break;

                default:
                    page = int.Parse(model.paging);
                    break;
            }

            // Recover the leftMostPage.
            int leftMostPage = (int)TempData["leftMostPage"];

            // Recover the filters.
            string catFilter = TempData["categoryFilter"].ToString();
            string ameFilter = TempData["amenityFilter"].ToString();

            // Recover the search text.
            model.searchText = TempData["searchfor"].ToString();

            // Search for the new page.
            await RunQueryAsync(model, page, leftMostPage, catFilter, ameFilter);
        }

        catch
        {
            return View("Error", new ErrorViewModel { RequestId = "2" });
        }
        return View("Index", model);
    }
    ```

1. **FacetAsync(SearchData model)** アクション メソッドを追加します。これは、ユーザーがファセット リンクをクリックしたときにアクティブになります。 モデルには、カテゴリまたはアメニティ検索フィルターのどちらかが含まれます。 これを **PageAsync** アクションの後に追加します。

    ```cs
    public async Task<ActionResult> FacetAsync(SearchData model)
    {
        try
        {
            // Filters set by the model override those stored in temporary data.
            string catFilter;
            string ameFilter;
            if (model.categoryFilter != null)
            {
                catFilter = model.categoryFilter;
            } else
            {
                catFilter = TempData["categoryFilter"].ToString();
            }

            if (model.amenityFilter != null)
            {
                ameFilter = model.amenityFilter;
            } else
            {
                ameFilter = TempData["amenityFilter"].ToString();
            }

            // Recover the search text.
            model.searchText = TempData["searchfor"].ToString();

            // Initiate a new search.
            await RunQueryAsync(model, 0, 0, catFilter, ameFilter).ConfigureAwait(false);
        }
        catch
        {
            return View("Error", new ErrorViewModel { RequestId = "2" });
        }

        return View("Index", model);
    }
    ```

### <a name="set-up-the-search-filter"></a>検索フィルターを設定する

**Resort and Spa** カテゴリをクリックするなど、ユーザーがあるファセットを選択すると、そのカテゴリに該当するホテルのみが結果として返されます。 この方法で検索を絞り込むには、_フィルター_ を設定する必要があります。

1. **RunQueryAsync** メソッドを次のコードに置き換えます。 ここで主に行っているのは、カテゴリ フィルター文字列とアメニティ フィルター文字列を受け取って **SearchOptions** の **Filter** パラメーターを設定することです。

    ```cs
    private async Task<ActionResult> RunQueryAsync(SearchData model, int page, int leftMostPage, string catFilter, string ameFilter)
    {
        InitSearch();

        string facetFilter = "";

        if (catFilter.Length > 0 && ameFilter.Length > 0)
        {
            // Both facets apply.
            facetFilter = $"{catFilter} and {ameFilter}"; 
        } else
        {
            // One, or zero, facets apply.
            facetFilter = $"{catFilter}{ameFilter}";
        }

        var options = new SearchOptions
        {
            Filter = facetFilter,

            SearchMode = SearchMode.All,

            // Skip past results that have already been returned.
            Skip = page * GlobalVariables.ResultsPerPage,

            // Take only the next page worth of results.
            Size = GlobalVariables.ResultsPerPage,

            // Include the total number of results.
            IncludeTotalCount = true,
        };

        // Return information on the text, and number, of facets in the data.
        options.Facets.Add("Category,count:20");
        options.Facets.Add("Tags,count:20");

        // Enter Hotel property names into this list, so only these values will be returned.
        options.Select.Add("HotelName");
        options.Select.Add("Description");
        options.Select.Add("Category");
        options.Select.Add("Tags");

        // For efficiency, the search call should be asynchronous, so use SearchAsync rather than Search.
        model.resultList = await _searchClient.SearchAsync<Hotel>(model.searchText, options).ConfigureAwait(false);

        // This variable communicates the total number of pages to the view.
        model.pageCount = ((int)model.resultList.TotalCount + GlobalVariables.ResultsPerPage - 1) / GlobalVariables.ResultsPerPage;

        // This variable communicates the page number being displayed to the view.
        model.currentPage = page;

        // Calculate the range of page numbers to display.
        if (page == 0)
        {
            leftMostPage = 0;
        }
        else if (page <= leftMostPage)
        {
            // Trigger a switch to a lower page range.
            leftMostPage = Math.Max(page - GlobalVariables.PageRangeDelta, 0);
        }
        else if (page >= leftMostPage + GlobalVariables.MaxPageRange - 1)
        {
            // Trigger a switch to a higher page range.
            leftMostPage = Math.Min(page - GlobalVariables.PageRangeDelta, model.pageCount - GlobalVariables.MaxPageRange);
        }
        model.leftMostPage = leftMostPage;

        // Calculate the number of page numbers to display.
        model.pageRange = Math.Min(model.pageCount - leftMostPage, GlobalVariables.MaxPageRange);

        // Ensure Temp data is stored for the next call.
        TempData["page"] = page;
        TempData["leftMostPage"] = model.leftMostPage;
        TempData["searchfor"] = model.searchText;
        TempData["categoryFilter"] = catFilter;
        TempData["amenityFilter"] = ameFilter;

        // Return the new view.
        return View("Index", model);
    }
    ```

    **Category** および **Tags** プロパティが、返される **Select** 項目のリストに追加されていることに注意してください。 この追加は、ファセット ナビゲーションが動作するための要件ではありませんが、この情報を使用してフィルターが正常に機能していることを確認します。

### <a name="add-lists-of-facet-links-to-the-view"></a>ビューにファセット リンクのリストを追加する

ビューには、いくつかの大幅な変更が必要です。 

1. まず、hotels.css ファイル (wwwroot/css フォルダーにあります) を開き、次のクラスを追加します。

    ```html
    .facetlist {
        list-style: none;
    }

    .facetchecks {
        width: 250px;
        display: normal;
        color: #666;
        margin: 10px;
        padding: 5px;
    }

    .facetheader {
        font-size: 10pt;
        font-weight: bold;
        color: darkgreen;
    }
    ```

1. ビューでは、ファセット リストが左に、結果が右にきちんと整列するように、出力をテーブルに編成します。 index.cshtml ファイルを開きます。 HTML の &lt;body&gt; タグの内容全体を次のコードに置き換えます。

    ```html
    <body>
        @using (Html.BeginForm("Index", "Home", FormMethod.Post))
        {
            <table>
                <tr>
                    <td></td>
                    <td>
                        <h1 class="sampleTitle">
                            <img src="~/images/azure-logo.png" width="80" />
                            Hotels Search - Facet Navigation
                        </h1>
                    </td>
                </tr>

                <tr>
                    <td></td>
                    <td>
                        <!-- Display the search text box, with the search icon to the right of it.-->
                        <div class="searchBoxForm">
                            @Html.TextBoxFor(m => m.searchText, new { @class = "searchBox" }) <input value="" class="searchBoxSubmit" type="submit">
                        </div>
                    </td>
                </tr>

                <tr>
                    <td valign="top">
                        <div id="facetplace" class="facetchecks">

                            @if (Model != null && Model.resultList != null)
                            {
                                List<string> categories = Model.resultList.Facets["Category"].Select(x => x.Value.ToString()).ToList();

                                if (categories.Count > 0)
                                {
                                    <h5 class="facetheader">Category:</h5>
                                    <ul class="facetlist">
                                        @for (var c = 0; c < categories.Count; c++)
                                        {
                                            var facetLink = $"{categories[c]} ({Model.resultList.Facets["Category"][c].Count})";
                                            <li>
                                                @Html.ActionLink(facetLink, "FacetAsync", "Home", new { categoryFilter = $"Category eq '{categories[c]}'" }, null)
                                            </li>
                                        }
                                    </ul>
                                }

                                List<string> tags = Model.resultList.Facets["Tags"].Select(x => x.Value.ToString()).ToList();

                                if (tags.Count > 0)
                                {
                                    <h5 class="facetheader">Amenities:</h5>
                                    <ul class="facetlist">
                                        @for (var c = 0; c < tags.Count; c++)
                                        {
                                            var facetLink = $"{tags[c]} ({Model.resultList.Facets["Tags"][c].Count})";
                                            <li>
                                                @Html.ActionLink(facetLink, "FacetAsync", "Home", new { amenityFilter = $"Tags/any(t: t eq '{tags[c]}')" }, null)
                                            </li>
                                        }
                                    </ul>
                                }
                            }
                        </div>
                    </td>
                    <td valign="top">
                        <div id="resultsplace">
                            @if (Model != null && Model.resultList != null)
                            {
                                // Show the result count.
                                <p class="sampleText">
                                    @Model.resultList.TotalCount Results
                                </p>

                                var results = Model.resultList.GetResults().ToList();

                                @for (var i = 0; i < results.Count; i++)
                                {
                                    string amenities = string.Join(", ", results[i].Document.Tags);

                                    string fullDescription = results[i].Document.Description;
                                    fullDescription += $"\nCategory: {results[i].Document.Category}";
                                    fullDescription += $"\nAmenities: {amenities}";


                                    // Display the hotel name and description.
                                    @Html.TextAreaFor(m => results[i].Document.HotelName, new { @class = "box1" })
                                    @Html.TextArea($"desc{i}", fullDescription, new { @class = "box2" })
                                }
                            }
                        </div>
                    </td>
                </tr>

                <tr>
                    <td></td>
                    <td valign="top">
                        @if (Model != null && Model.pageCount > 1)
                        {
                            // If there is more than one page of results, show the paging buttons.
                            <table>
                                <tr>
                                    <td class="tdPage">
                                        @if (Model.currentPage > 0)
                                        {
                                            <p class="pageButton">
                                                @Html.ActionLink("|<", "PageAsync", "Home", new { paging = "0" }, null)
                                            </p>
                                        }
                                        else
                                        {
                                            <p class="pageButtonDisabled">|&lt;</p>
                                        }
                                    </td>

                                    <td class="tdPage">
                                        @if (Model.currentPage > 0)
                                        {
                                            <p class="pageButton">
                                                @Html.ActionLink("<", "PageAsync", "Home", new { paging = "prev" }, null)
                                            </p>
                                        }
                                        else
                                        {
                                            <p class="pageButtonDisabled">&lt;</p>
                                        }
                                    </td>

                                    @for (var pn = Model.leftMostPage; pn < Model.leftMostPage + Model.pageRange; pn++)
                                    {
                                        <td class="tdPage">
                                            @if (Model.currentPage == pn)
                                            {
                                                // Convert displayed page numbers to 1-based and not 0-based.
                                                <p class="pageSelected">@(pn + 1)</p>
                                            }
                                            else
                                            {
                                                <p class="pageButton">
                                                    @Html.ActionLink((pn + 1).ToString(), "PageAsync", "Home", new { paging = @pn }, null)
                                                </p>
                                            }
                                        </td>
                                    }

                                    <td class="tdPage">
                                        @if (Model.currentPage < Model.pageCount - 1)
                                        {
                                            <p class="pageButton">
                                                @Html.ActionLink(">", "PageAsync", "Home", new { paging = "next" }, null)
                                            </p>
                                        }
                                        else
                                        {
                                            <p class="pageButtonDisabled">&gt;</p>
                                        }
                                    </td>

                                    <td class="tdPage">
                                        @if (Model.currentPage < Model.pageCount - 1)
                                        {
                                            <p class="pageButton">
                                                @Html.ActionLink(">|", "PageAsync", "Home", new { paging = Model.pageCount - 1 }, null)
                                            </p>
                                        }
                                        else
                                        {
                                            <p class="pageButtonDisabled">&gt;|</p>
                                        }
                                    </td>
                                </tr>
                            </table>
                        }
                    </td>
                </tr>
            </table>
        }
    </body>
    ```

    **Html.ActionLink** 呼び出しを使用している点に注意してください。 ユーザーがファセット リンクをクリックすると、これが呼び出されて有効なフィルター文字列がコントローラーに送られます。 

### <a name="run-and-test-the-app"></a>アプリを実行してテストする

ユーザーにとってのファセット ナビゲーションの利点は、1 回のクリックで検索を絞り込めることです。以下の手順では、それをお見せします。

1. アプリを実行し、検索テキストとして「airport」と入力します。 左側にファセットのリストがきれいに表示されていることを確認します。 これらのファセットは、テキスト データに「airport」が含まれるホテルに適用されるもので、その数とともに表示されています。

    ![ファセット ナビゲーションを使用して「airport」の検索を絞り込む](./media/tutorial-csharp-create-first-app/azure-search-facet-airport.png)

1. **Resort and Spa** カテゴリをクリックします。 すべての結果がこのカテゴリに属することを確認します。

    ![「Resort and Spa」で検索を絞り込む](./media/tutorial-csharp-create-first-app/azure-search-facet-airport-ras.png)

1. **continental breakfast** アメニティをクリックします。 すべての結果がまだ「Resort and Spa」カテゴリに属しており、選択されたアメニティが付属していることを確認します。

    ![「continental breakfast」で検索を絞り込む](./media/tutorial-csharp-create-first-app/azure-search-facet-airport-ras-cb.png)

1. その他のカテゴリを選択した後、1 つのアメニティを選択し、絞り込まれた結果を表示してみてください。 逆に、1 つのアメニティを選択した後、1 つのカテゴリを選択してみてください。 空の検索を送信してページをリセットします。

    >[!Note]
    > ファセット リストで 1 つ (カテゴリなど) を選択すると、カテゴリのリストの以前の選択内容は上書きされます。

## <a name="takeaways"></a>重要なポイント

このプロジェクトの重要なポイントを示します。

* ファセット ナビゲーションに含めるには、各ファセット可能フィールドを **IsFacetable** プロパティでマークする必要があります。
* ファセットをフィルターと組み合わせて、結果を減らします。
* ファセットは累積的であり、各選択が前の選択に基づくことにより、結果がさらに絞り込まれます。

## <a name="next-steps"></a>次のステップ

次のチュートリアルでは、結果の並べ替えに注目します。 ここまでの結果は、単にデータベースに格納されている順番で表示されています。

> [!div class="nextstepaction"]
> [C# のチュートリアル: 結果の並べ替え - Azure Cognitive Search](tutorial-csharp-orders.md)
