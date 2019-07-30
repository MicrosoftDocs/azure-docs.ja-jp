---
title: ナビゲーションをサポートするファセットの使用に関する C# チュートリアル - Azure Search
description: このチュートリアルでは、「検索結果のページング - Azure Search」プロジェクトにファセット ナビゲーションを追加します。 ファセットを使用すると、簡単に検索を絞り込めることについて説明します。
services: search
ms.service: search
ms.topic: tutorial
ms.author: v-pettur
author: PeterTurcan
ms.date: 06/20/2019
ms.openlocfilehash: 62326ad3bc5f2d740ce744819df559bce8658eb7
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/28/2019
ms.locfileid: "67443777"
---
# <a name="c-tutorial-use-facets-to-aid-navigation---azure-search"></a>C# のチュートリアル: ナビゲーションをサポートするファセットの使用 - Azure Search

ファセットを使用すると、絞り込み検索を行うことができる一連のリンクをユーザーに提供することで、ナビゲーションをサポートすることができます。 ファセットは、データの属性 (サンプル データでは、ホテルのカテゴリや具体的特徴など) です。

このチュートリアルは、「[C# チュートリアル: 検索結果のページング- Azure Search](tutorial-csharp-paging.md)」チュートリアルで作成したページング プロジェクトを基にしています。

このチュートリアルでは、以下の内容を学習します。
> [!div class="checklist"]
> * モデルのプロパティに _IsFacetable_ を設定する
> * アプリにファセット ナビゲーションを追加する

## <a name="prerequisites"></a>前提条件

このチュートリアルを完了するには、以下を実行する必要があります。

「[C# チュートリアル: 検索結果のページング- Azure Search](tutorial-csharp-paging.md)」プロジェクトを実行できるようにします。 このプロジェクトは、独自のバージョンのものでも、GitHub の「[Create first app (初めてのアプリを作成する)](https://github.com/Azure-Samples/azure-search-dotnet-samples)」からインストールしたものでも構いません。

## <a name="set-model-properties-as-isfacetable"></a>モデルのプロパティに IsFacetable を設定する

ファセット検索でモデルのプロパティを検索できるようにするには、**IsFacetable** タグを付ける必要があります。

1. **Hotel** クラスを確認します。 **Category** や **Tags** などには **IsFacetable** タグが付けられていますが、**HotelName** や **Description** には付けられていません。 

    ```cs
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
    ```

2. このチュートリアルではタグの変更は行わないので、何も変更せずに hotel.cs ファイルを閉じます。

    > [!Note]
    > ファセット検索で要求されたフィールドが適切にタグ付けされていない場合、検索時にエラーがスローされます。


## <a name="add-facet-navigation-to-your-app"></a>アプリにファセット ナビゲーションを追加する

この例では、結果の左側に表示されるリンクの一覧を使って、ユーザーがホテルのカテゴリまたはアメニティを 1 つ選択できるようにします。 ユーザーは、まず検索テキストを入力します。その後、カテゴリを選択して検索結果を絞り込み、アメニティを選択してさらに絞り込むことができます。または、最初にアメニティを選択することもできます (順番は重要ではありません)。

ここでは、コントローラーがファセットのリストをビューに渡す必要があります。 検索の進行状況に応じてユーザーの選択を維持する必要があるので、ここでもデータを保持するメカニズムとして一時ストレージを使用します。

![ファセット ナビゲーションを使用して「pool」の検索を絞り込む](./media/tutorial-csharp-create-first-app/azure-search-facet-nav.png)

### <a name="add-filter-strings-to-the-searchdata-model"></a>SearchData モデルにフィルター文字列を追加する

1. SearchData.cs ファイルを開き、**SearchData** クラスに文字列プロパティを追加します。これは、ファセット フィルター文字列を保持するために使用されます。

    ```cs
        public string categoryFilter { get; set; }
        public string amenityFilter { get; set; }
    ```

### <a name="add-the-facet-action-method"></a>ファセット アクション メソッドを追加する

ホーム コントローラーには、1 つの新しいアクション **Facet** が必要です。また、既存の **Index** および **Page** アクション、さらに **RunQueryAsync** メソッドを更新する必要があります。

1. ホーム コントローラーのファイルを開き、**using** ステートメントを追加して **List&lt;string&gt;** コンストラクトを有効にします。

    ```cs
    using System.Collections.Generic;
    ```

2. **Index(SearchData model)** アクション メソッドを置き換えます。

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
                await RunQueryAsync(model, 0, 0, "", "");
            }

            catch
            {
                return View("Error", new ErrorViewModel { RequestId = "1" });
            }
            return View(model);
        }
    ```

3. **Page(SearchData model)** アクション メソッドを置き換えます。

    ```cs
        public async Task<ActionResult> Page(SearchData model)
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

4. **Facet(SearchData model)** アクション メソッドを追加します。これは、ユーザーがファセット リンクをクリックしたときにアクティブになります。 モデルには、カテゴリ検索フィルターかアメニティ検索フィルターのどちらかが含まれます。 **Page** アクションの下に追加するとよいかもしれません。

    ```cs
        public async Task<ActionResult> Facet(SearchData model)
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
                await RunQueryAsync(model, 0, 0, catFilter, ameFilter);
            }

            catch
            {
                return View("Error", new ErrorViewModel { RequestId = "2" });
            }
            return View("Index", model);
        }
    ```

### <a name="set-up-the-search-filter"></a>検索フィルターを設定する

**Resort and Spa** カテゴリをクリックするなど、ユーザーがあるファセットを選択すると、そのカテゴリに該当するホテルのみが結果として返されます。 この方法で検索を絞り込むには、_フィルター_を設定する必要があります。

1. **RunQueryAsync** メソッドを次のコードに置き換えます。 ここで主に行っているのは、カテゴリとアメニティのフィルター文字列を受け取って **SearchParameters** の **Filter** パラメーターを設定することです。

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

            var parameters = new SearchParameters
            {
                Filter = facetFilter,

                // Return information on the text, and number, of facets in the data.
                Facets = new List<string> { "Category,count:20", "Tags,count:20" },

                // Enter Hotel property names into this list, so only these values will be returned.
                Select = new[] { "HotelName", "Description", "Category", "Tags" },

                SearchMode = SearchMode.All,

                // Skip past results that have already been returned.
                Skip = page * GlobalVariables.ResultsPerPage,

                // Take only the next page worth of results.
                Top = GlobalVariables.ResultsPerPage,

                // Include the total number of results.
                IncludeTotalResultCount = true,
            };

            // For efficiency, the search call should be asynchronous, so use SearchAsync rather than Search.
            model.resultList = await _indexClient.Documents.SearchAsync<Hotel>(model.searchText, parameters);

            // This variable communicates the total number of pages to the view.
            model.pageCount = ((int)model.resultList.Count + GlobalVariables.ResultsPerPage - 1) / GlobalVariables.ResultsPerPage;

            // This variable communicates the page number being displayed to the view.
            model.currentPage = page;

            // Calculate the range of page numbers to display.
            if (page == 0)
            {
                leftMostPage = 0;
            }
            else
               if (page <= leftMostPage)
            {
                // Trigger a switch to a lower page range.
                leftMostPage = Math.Max(page - GlobalVariables.PageRangeDelta, 0);
            }
            else
            if (page >= leftMostPage + GlobalVariables.MaxPageRange - 1)
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

    返される **Select** 項目のリストに、**Category** および **Tags** プロパティを追加しています。 この追加は、ファセット ナビゲーションが動作するための要件ではありませんが、この情報を使用して正しくフィルター処理が行われていることを確認します。

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

2. ビューでは、ファセット リストが左に、結果が右にきれいに整列するように、出力をテーブルに編成します。 index.cshtml ファイルを開きます。 HTML の &lt;body&gt; タグの内容全体を次のコードに置き換えます。

    ```cs
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
                                            @Html.ActionLink(facetLink, "Facet", "Home", new { categoryFilter = $"Category eq '{categories[c]}'" }, null)
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
                                            @Html.ActionLink(facetLink, "Facet", "Home", new { amenityFilter = $"Tags/any(t: t eq '{tags[c]}')" }, null)
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
                                @Html.DisplayFor(m => m.resultList.Count) Results
                            </p>

                            @for (var i = 0; i < Model.resultList.Results.Count; i++)
                            {
                                string amenities = string.Join(", ", Model.resultList.Results[i].Document.Tags);

                                string fullDescription = Model.resultList.Results[i].Document.Description;
                                fullDescription += $"\nCategory: {Model.resultList.Results[i].Document.Category}";
                                fullDescription += $"\nAmenities: {amenities}";

                                // Display the hotel name and description.
                                @Html.TextAreaFor(m => Model.resultList.Results[i].Document.HotelName, new { @class = "box1" })
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
                                            @Html.ActionLink("|<", "Page", "Home", new { paging = "0" }, null)
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
                                            @Html.ActionLink("<", "Page", "Home", new { paging = "prev" }, null)
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
                                                @Html.ActionLink((pn + 1).ToString(), "Page", "Home", new { paging = @pn }, null)
                                            </p>
                                        }
                                    </td>
                                }

                                <td class="tdPage">
                                    @if (Model.currentPage < Model.pageCount - 1)
                                    {
                                        <p class="pageButton">
                                            @Html.ActionLink(">", "Page", "Home", new { paging = "next" }, null)
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
                                            @Html.ActionLink(">|", "Page", "Home", new { paging = Model.pageCount - 1 }, null)
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

2. **Resort and Spa** カテゴリをクリックします。 すべての結果がこのカテゴリに属することを確認します。

    ![「Resort and Spa」で検索を絞り込む](./media/tutorial-csharp-create-first-app/azure-search-facet-airport-ras.png)

3. **continental breakfast** アメニティをクリックします。 すべての結果がまだ「Resort and Spa」カテゴリに属しており、選択されたアメニティが付属していることを確認します。

    ![「continental breakfast」で検索を絞り込む](./media/tutorial-csharp-create-first-app/azure-search-facet-airport-ras-cb.png)

4. その他のカテゴリを選択した後、1 つのアメニティを選択し、絞り込まれた結果を表示してみてください。 逆に、1 つのアメニティを選択した後、1 つのカテゴリを選択してみてください。

    >[!Note]
    > ファセット リストで 1 つ (カテゴリなど) を選択すると、カテゴリのリストの以前の選択内容は上書きされます。

## <a name="takeaways"></a>重要なポイント

このプロジェクトの重要なポイントを示します。

* ファセット ナビゲーションに含めるプロパティは、それぞれ **IsFacetable** とマークする必要があります。
* ファセット ナビゲーションでは、ユーザーが簡単かつ直感的に検索を絞り込む方法を提供します。
* ファセット ナビゲーションは、セクション (ホテルのカテゴリ、ホテルのアメニティ、価格帯、評価の範囲など) に分け、各セクションに適切なヘッダーを付けるとよいでしょう。

## <a name="next-steps"></a>次の手順

次のチュートリアルでは、結果の並べ替えに注目します。 ここまでの結果は、単にデータベースに格納されている順番で表示されています。

> [!div class="nextstepaction"]
> [C# のチュートリアル: 結果の並び替え - Azure Search ](tutorial-csharp-orders.md)
