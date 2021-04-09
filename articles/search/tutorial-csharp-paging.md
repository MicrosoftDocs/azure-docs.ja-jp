---
title: 検索結果のページングに関する C# チュートリアル
titleSuffix: Azure Cognitive Search
description: 既存のホテル プロジェクト上で検索結果にページ分割ボタンとナビゲーション ボタンを追加して、"最初"、"次へ"、"前へ"、"最後"、および番号付きの各ボタンを追加します。 2 番目のページング システムでは、垂直スクロール バーをその下限に移動することによってトリガーされる無限スクロールを使用します。
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: tutorial
ms.date: 01/26/2021
ms.custom: devx-track-js, devx-track-csharp
ms.openlocfilehash: bad4bc4d0016b2898b315bfb9799dc8972be7b12
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "98785872"
---
# <a name="tutorial-add-paging-to-search-results-using-the-net-sdk"></a>チュートリアル:.NET SDK を使用して検索結果にページングを追加する

2 つの異なるページング システムを実装する方法について説明します。1 つ目はページ番号に基づき、2 つ目は無限スクロールに基づきます。 両方のページング システムが広く使用されており、どちらを選択するかは結果を処理するユーザー エクスペリエンスによります。 

このチュートリアルで学習する内容は次のとおりです。
> [!div class="checklist"]
> * 番号付きのページングでアプリを拡張する
> * 無限スクロールでアプリを拡張する

## <a name="overview"></a>概要

このチュートリアルでは、[最初の検索アプリの作成](tutorial-csharp-create-first-app.md)に関するチュートリアルで説明した、以前に作成したプロジェクトにページング システムをオーバーレイします。

このチュートリアルで開発するコードの完成版は、次のプロジェクトにあります。

* [2a-add-paging (GitHub)](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/create-first-app/v11/2a-add-paging)

* [2b-add-infinite-scroll (GitHub)](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/create-first-app/v11/2b-add-infinite-scroll)

## <a name="prerequisites"></a>前提条件

* [1-basic-search-page (GitHub)](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/create-first-app/v11/1-basic-search-page) プロジェクト。 このプロジェクトは、前のチュートリアルで作成した独自のバージョンでも、GitHub からコピーしたものでもかまいません。

このチュートリアルは、[Azure.Search.Documents (バージョン 11)](https://www.nuget.org/packages/Azure.Search.Documents/) パッケージを使用するように更新されました。 .NET SDK の以前のバージョンについては、[Microsoft.Azure.Search (バージョン 10) のコード サンプル](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/create-first-app/v10)を参照してください。

## <a name="extend-your-app-with-numbered-paging"></a>番号付きのページングでアプリを拡張する

番号付きページングは、主要な商用 Web 検索エンジンやその他の多くの検索 Web サイトで選択されているページング システムです。 通常、番号付きページングには、実際のページ番号の範囲だけでなく、[次へ] と [前へ] オプションが含まれています。 [最初のページ] と [最後のページ] オプションも使用できる場合があります。 これらのオプションにより、ユーザーはページベースの結果内を移動できるようになります。

このチュートリアルでは、"最初"、"前へ"、"次へ"、および "最後" オプションと、ページ番号が含まれるシステムを追加します。ページ番号は 1 から始まるのではなく、ユーザーの現在のページの前後のページ番号が表示されます (たとえば、ユーザーが 10 ページを見ている場合、表示されるページ番号は 8、9、10、11、および 12 のようになります)。

システムは柔軟なので、表示されるページ番号の数は、グローバル変数で設定できます。

システムでは、左端と右端のページ番号ボタンが特別なボタンとして扱われます。つまり、それらのボタンは、表示されるページ番号の範囲の変更をトリガーします。 たとえば、ページ番号 8、9、10、11、および 12 が表示されていて、ユーザーが 8 をクリックすると、表示されるページ番号の範囲は 6、7、8、9、および 10 に変わります。 また、12 が選択された場合は、同様に右にシフトします。

### <a name="add-paging-fields-to-the-model"></a>モデルにページング フィールドを追加する

基本的な検索ページのソリューションを開いておきます。

1. SearchData.cs モデル ファイルを開きます。

1. 改ページ位置の自動修正をサポートするグローバル変数を追加します。 MVC では、グローバル変数は独自の静的クラスで宣言されます。 **ResultsPerPage** は、ページあたりの結果数を設定します。 **MaxPageRange** は、ビューに表示されるページ番号の数を決定します。 **PageRangeDelta** は、左端または右端のページ番号が選択されたときに左または右に何ページ分シフトするかを決定します。 通常、後者の数は、**MaxPageRange** の約半分です。 次のコードを名前空間に追加します。

    ```csharp
    public static class GlobalVariables
    {
        public static int ResultsPerPage
        {
            get
            {
                return 3;
            }
        }
        public static int MaxPageRange
        {
            get
            {
                return 5;
            }
        }

        public static int PageRangeDelta
        {
            get
            {
                return 2;
            }
        }
    }
    ```

    >[!Tip]
    >ノート PC など、画面が小さいデバイスでこのプロジェクトを実行している場合は、**ResultsPerPage** を 2 に変更することを検討してください。

1. **SearchData** クラスの **searchText** プロパティの後に、ページング プロパティを追加します。

    ```csharp
    // The current page being displayed.
    public int currentPage { get; set; }

    // The total number of pages of results.
    public int pageCount { get; set; }

    // The left-most page number to display.
    public int leftMostPage { get; set; }

    // The number of page numbers to display - which can be less than MaxPageRange towards the end of the results.
    public int pageRange { get; set; }

    // Used when page numbers, or next or prev buttons, have been selected.
    public string paging { get; set; }
    ```

### <a name="add-a-table-of-paging-options-to-the-view"></a>ビューにページング オプションのテーブルを追加する

1. index.cshtml ファイルを開き、終了 &lt;/body&gt; タグの直前に次のコードを追加します。 この新しいコードは、ページング オプション ("最初"、"前へ"、1、2、3、4、5、"次へ"、"最後") のテーブルを表します。

    ```html
    @if (Model != null && Model.pageCount > 1)
    {
    // If there is more than one page of results, show the paging buttons.
    <table>
        <tr>
            <td>
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

            <td>
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
                <td>
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

            <td>
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

            <td>
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
    ```

    HTML テーブルを使用すると、ものをきちんと揃えることができます。 すべてのアクションは @Html.ActionLink ステートメントから行われますが、**new** モデルのコントローラーによって作成される各呼び出しは、前に追加した **paging** プロパティへの異なるエントリを持ちます。

    "最初のページ" と "最後のページ" オプションは、"first" や "last" などの文字列を送信するのではなく、正しいページ番号を送信します。

1. hotels.css ファイル内の HTML スタイルの一覧に、ページング クラスを追加します。 **pageSelected** クラスの目的は、ページ番号の一覧内での現在のページを (ページ番号に太字書式を適用することで) 識別することにあります。

    ```html
    .pageButton {
        border: none;
        color: darkblue;
        font-weight: normal;
        width: 50px;
    }

    .pageSelected {
        border: none;
        color: black;
        font-weight: bold;
        width: 50px;
    }

    .pageButtonDisabled {
        border: none;
        color: lightgray;
        font-weight: bold;
        width: 50px;
    }
    ```

### <a name="add-a-page-action-to-the-controller"></a>コントローラーに Page アクションを追加する

1. HomeController.cs ファイルを開き、**PageAsync** アクションを追加します。 このアクションは、選択された任意のページ オプションに応答します。

    ```csharp
    public async Task<ActionResult> PageAsync(SearchData model)
    {
        try
        {
            int page;

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

            // Recover the search text and search for the data for the new page.
            model.searchText = TempData["searchfor"].ToString();

            await RunQueryAsync(model, page, leftMostPage);

            // Ensure Temp data is stored for next call, as TempData only stores for one call.
            TempData["page"] = (object)page;
            TempData["searchfor"] = model.searchText;
            TempData["leftMostPage"] = model.leftMostPage;
        }

        catch
        {
            return View("Error", new ErrorViewModel { RequestId = "2" });
        }
        return View("Index", model);
    }
    ```

    **RunQueryAsync** メソッドが、3 つ目のパラメーターのために構文エラーを表示するようになります。これについては、すぐ後で対処します。

    > [!Note]
    > **TempData** の呼び出しにより、一時ストレージに値 (**オブジェクト**) が格納されますが、このストレージは 1 回の呼び出しの間 "_のみ_" 保持されます。 一時データに何かを格納した場合、次回のコントローラー アクションへの呼び出しでは利用できますが、そのデータはその後の呼び出しによって確実に失われます。 有効期間がこのように短いため、**PageAsync** の呼び出しのたびに、検索テキストとページングのプロパティを一時ストレージに格納し直します。

1. 一時変数を格納し、**RunQueryAsync** 呼び出しの左端のページ パラメーターを追加するように、**Index(model)** アクションを更新します。

    ```csharp
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
            await RunQueryAsync(model, 0, 0);

            // Ensure temporary data is stored for the next call.
            TempData["page"] = 0;
            TempData["leftMostPage"] = 0;
            TempData["searchfor"] = model.searchText;
        }

        catch
        {
            return View("Error", new ErrorViewModel { RequestId = "1" });
        }
        return View(model);
    }
    ```

1. 前のレッスンで紹介した **RunQueryAsync** メソッドは、構文エラーを解決するために変更する必要があります。 [**SearchOptions**](/dotnet/api/azure.search.documents.searchoptions) クラスの **Skip**、**Size**、および **IncludeTotalCount** フィールドを使用して、**Skip** 設定で始まる 1 ページ分だけの結果を要求します。 また、ビューのためにページング変数を計算する必要もあります。 メソッド全体を次のコードに置き換えます。

    ```csharp
    private async Task<ActionResult> RunQueryAsync(SearchData model, int page, int leftMostPage)
    {
        InitSearch();

        var options = new SearchOptions
        {
            // Skip past results that have already been returned.
            Skip = page * GlobalVariables.ResultsPerPage,

            // Take only the next page worth of results.
            Size = GlobalVariables.ResultsPerPage,

            // Include the total number of results.
            IncludeTotalCount = true
        };

        // Add fields to include in the search results.
        options.Select.Add("HotelName");
        options.Select.Add("Description");

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

        return View("Index", model);
    }
    ```

1. 最後に、ビューに小さな変更を加えます。 変数 **resultList.Results.TotalCount** には、結果の合計数ではなく、1 つのページで返される結果の数 (この例では 3) が含まれるようになります。 **IncludeTotalCount** を true に設定したため、変数 **resultList.TotalCount** には、結果の合計数が含まれるようになっています。 そのため、ビューで結果の数が表示される場所を探し、次のコードに変更します。

    ```csharp
    // Show the result count.
    <p class="sampleText">
        @Model.resultList.TotalCount Results
    </p>

    var results = Model.resultList.GetResults().ToList();

    @for (var i = 0; i < results.Count; i++)
    {
        // Display the hotel name and description.
        @Html.TextAreaFor(m => results[i].Document.HotelName, new { @class = "box1" })
        @Html.TextArea($"desc{1}", results[i].Document.Description, new { @class = "box2" })
    }
    ```

    > [!Note]
    > **IncludeTotalCount** を true に設定すると、この合計を Azure Cognitive Search で計算する必要があるため、パフォーマンスにわずかな影響があります。 複雑なデータセットでは、返される値が "_近似値_" であるという警告が表示されます。 ホテルの検索コーパスは小さいため、正確になります。

### <a name="compile-and-run-the-app"></a>アプリをコンパイルして実行する

**[デバッグなしで開始]** を選択します (または、F5 キーを押します)。

1. 多数の結果が返されるテキストを検索します ("wifi" など)。 結果をきちんとページングできますか?

    !["pool" の結果に対する番号付きページング](./media/tutorial-csharp-create-first-app/azure-search-numbered-paging.png)

1. 右端のページ番号、その後、左端のページ番号をクリックしてみます。 表示しているページが中央になるように、ページ番号が適切に調整されますか。

1. "最初" と "最後" オプションは使用できますか。 いくつかの商用検索エンジンではこれらのオプションが使用されていますが、使用されていない商用検索エンジンもあります。

1. 結果の最後のページに移動します。 最後のページは、含まれている結果の数が **ResultsPerPage** 未満である可能性がある唯一のページです。

    !["wifi" の最後のページの調査](./media/tutorial-csharp-create-first-app/azure-search-pool-last-page.png)

1. 「town」と入力し、検索アイコンをクリックします。 結果が 1 ページ分より少ない場合、ページング オプションは表示されません。

    !["town" の検索](./media/tutorial-csharp-create-first-app/azure-search-town.png)

このプロジェクトを保存し、別の形式のページングのために次のセクションに進みます。

## <a name="extend-your-app-with-infinite-scrolling"></a>無限スクロールでアプリを拡張する

無限スクロールは、ユーザーが垂直スクロール バーを、表示されている結果の最後までスクロールしたときにトリガーされます。 このイベントでは、結果の次のページのために、検索サービスへの呼び出しが行われます。 結果が残っていない場合は、何も返されず、垂直スクロール バーは変更されません。 さらに結果がある場合は、現在のページに追加され、スクロール バーが、より多くの結果を表示できるように変更されます。

注意すべき重要な点は、現在のページは、置き換えられる代わりに、追加の結果が表示されるように拡張されることです。 ユーザーは、常に検索の最初の結果にスクロールで戻ることができます。

無限スクロールを実装するには、ページ番号のスクロール要素を追加した時点より前からプロジェクトを始めましょう。 GitHub では、これは [FirstAzureSearchApp](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/create-first-app/v11/1-basic-search-page) ソリューションです。

### <a name="add-paging-fields-to-the-model"></a>モデルにページング フィールドを追加する

1. まず、**paging** プロパティを **SearchData** クラス (SearchData.cs モデル ファイル内) に追加します。

    ```csharp
    // Record if the next page is requested.
    public string paging { get; set; }
    ```

    この変数は文字列であり、結果の次のページを送信する必要がある場合は "next" を保持し、検索の最初のページの場合は null です。

1. 同じファイルの名前空間内で、1 つのプロパティを持つグローバル変数クラスを追加します。 MVC では、グローバル変数は独自の静的クラスで宣言されます。 **ResultsPerPage** は、ページあたりの結果数を設定します。 

    ```csharp
    public static class GlobalVariables
    {
        public static int ResultsPerPage
        {
            get
            {
                return 3;
            }
        }
    }
    ```

### <a name="add-a-vertical-scroll-bar-to-the-view"></a>ビューに垂直スクロール バーを追加する

1. 結果を表示する、index.cshtml ファイルのセクションを見つけます ( **@if (Model != null)** で始まります)。

1. セクションを以下のコードに置き換えます。 新しい **&lt;div&gt;** セクションはスクロール可能な領域の周囲にあり、次のように、**overflow-y** 属性と、"scrolled()" と呼ばれる **onscroll** 関数の呼び出しの両方が追加されます。

    ```csharp
    @if (Model != null)
    {
        // Show the result count.
        <p class="sampleText">
            @Model.resultList.TotalCount Results
        </p>

        var results = Model.resultList.GetResults().ToList();

        <div id="myDiv" style="width: 800px; height: 450px; overflow-y: scroll;" onscroll="scrolled()">

            <!-- Show the hotel data. -->
            @for (var i = 0; i < results.Count; i++)
            {
                // Display the hotel name and description.
                @Html.TextAreaFor(m => results[i].Document.HotelName, new { @class = "box1" })
                @Html.TextArea($"desc{i}", results[i].Document.Description, new { @class = "box2" })
            }
    ```

1. ループのすぐ下、&lt;/div&gt; タグの後に、**scrolled** 関数を追加します。

    ```javascript
    <script>
        function scrolled() {
            if (myDiv.offsetHeight + myDiv.scrollTop >= myDiv.scrollHeight) {
                $.getJSON("/Home/NextAsync", function (data) {
                    var div = document.getElementById('myDiv');

                    // Append the returned data to the current list of hotels.
                    for (var i = 0; i < data.length; i += 2) {
                        div.innerHTML += '\n<textarea class="box1">' + data[i] + '</textarea>';
                        div.innerHTML += '\n<textarea class="box2">' + data[i + 1] + '</textarea>';
                    }
                });
            }
        }
    </script>
    ```

    上のスクリプトの **if** ステートメントは、ユーザーが垂直スクロール バーの一番下までスクロールしたかどうかをテストします。 した場合は、**Home** コントローラーの呼び出しが **NextAsync** と呼ばれるアクションに対して行われます。 コントローラーは、他の情報を必要とせず、データの次のページを返します。 その後、このデータは、元のページと同じ HTML スタイルを使用して書式設定されます。 結果が返されない場合は、何も追加されず、その状態のままです。

### <a name="handle-the-next-action"></a>Next アクションを処理する

コントローラーに送信する必要があるアクションは 3 つだけです。1 つ目はアプリケーションの最初の実行であり、**Index ()** を呼び出します。2 つ目はユーザーによる最初の検索であり、**Index(model)** を呼び出します。3 つ目は **Next(model)** を介してより多くの結果を求める、その後の呼び出しです。

1. home コントローラー ファイルを開き、元のチュートリアルから **RunQueryAsync** メソッドを削除します。

1. **Index(model)** アクションを以下のコードに置き換えます。 これで、**paging** フィールドが null の場合、または "next" に設定されている場合はこれを処理し、Azure Cognitive Search の呼び出しを処理するようになりました。

    ```csharp
    public async Task<ActionResult> Index(SearchData model)
    {
        try
        {
            InitSearch();

            int page;

            if (model.paging != null && model.paging == "next")
            {
                // Increment the page.
                page = (int)TempData["page"] + 1;

                // Recover the search text.
                model.searchText = TempData["searchfor"].ToString();
            }
            else
            {
                // First call. Check for valid text input.
                if (model.searchText == null)
                {
                    model.searchText = "";
                }
                page = 0;
            }

            // Setup the search parameters.
            var options = new SearchOptions
            {
                SearchMode = SearchMode.All,

                // Skip past results that have already been returned.
                Skip = page * GlobalVariables.ResultsPerPage,

                // Take only the next page worth of results.
                Size = GlobalVariables.ResultsPerPage,

                // Include the total number of results.
                IncludeTotalCount = true
            };

            // Specify which fields to include in results.
            options.Select.Add("HotelName");
            options.Select.Add("Description");

            // For efficiency, the search call should be asynchronous, so use SearchAsync rather than Search.
            model.resultList = await _searchClient.SearchAsync<Hotel>(model.searchText, options).ConfigureAwait(false);               

            // Ensure TempData is stored for the next call.
            TempData["page"] = page;
            TempData["searchfor"] = model.searchText;
        }
        catch
        {
            return View("Error", new ErrorViewModel { RequestId = "1" });
        }

        return View("Index", model);
    }
    ```

    番号付きページング方法と同様に、**Skip** および **Size** 検索設定を使用して、必要なデータだけが返されるように要求します。

1. home コントローラーに **NextAsync** アクションを追加します。 どのように一覧が返されるかに注意してください。各ホテルについて、2 つの要素 (ホテル名とホテルの説明) が一覧に追加されます。 この形式は、返されたデータをビューで **scrolled** 関数が使用するときの形式と一致するように設定されています。

    ```csharp
    public async Task<ActionResult> NextAsync(SearchData model)
    {
        // Set the next page setting, and call the Index(model) action.
        model.paging = "next";
        await Index(model).ConfigureAwait(false);

        // Create an empty list.
        var nextHotels = new List<string>();

        // Add a hotel name, then description, to the list.
        await foreach (var searchResult in model.resultList.GetResultsAsync())
        {
            nextHotels.Add(searchResult.Document.HotelName);
            nextHotels.Add(searchResult.Document.Description);
        }

        // Rather than return a view, return the list of data.
        return new JsonResult(nextHotels);
    }
    ```

1. **List&lt;string&gt;** で構文エラーが発生する場合は、コントローラー ファイルの先頭に次の **using** ディレクティブを追加します。

    ```csharp
    using System.Collections.Generic;
    ```

### <a name="compile-and-run-your-project"></a>プロジェクトをコンパイルして実行する

**[デバッグなしで開始]** を選択します (または、F5 キーを押します)。

1. 十分な数の結果が得られるような用語 ("pool" など) を入力してから、垂直スクロール バーをテストします。 結果の新しいページがトリガーされますか。

    !["pool" の結果に対する無限スクロール](./media/tutorial-csharp-create-first-app/azure-search-infinite-scroll.png)

    > [!Tip]
    > 最初のページにスクロール バーが表示されるようにするには、結果の最初のページが、表示されている領域の高さを少しでも超えている必要があります。 この例では、 **.box1** の高さは 30 ピクセルであり、 **.box2** の高さは 100 ピクセルであって、"_さらに_" 下余白が 24 ピクセルです。 つまり、各エントリは 154 ピクセルを使用します。 3 エントリでは、3 x 154 = 462 ピクセルになります。 確実に垂直スクロール バーが表示されるようにするには、表示領域の高さを 462 ピクセル未満に設定する必要があります (461 ピクセルでもかまいません)。 この問題は、最初のページのみで発生します。その後は、スクロール バーが確実に表示されます。 更新する行は、 **&lt;div id="myDiv" style="width: 800px; height: 450px; overflow-y: scroll;" onscroll="scrolled()"&gt;** です。

1. 結果の一番下までスクロールします。 すべての情報が 1 つのビュー ページに表示されるようになったことに注意してください。 サーバー呼び出しをトリガーすることなく、一番上までスクロールして戻ることができます。

より洗練された無限スクロール システムでは、マウス ホイールや、同様の他のメカニズムを使用して、結果の新しいページの読み込みをトリガーすることがあります。 このチュートリアルでは、これ以上は無限スクロールの説明をしませんが、これには余分なマウス クリックをしなくて済む利点もあるため、他のオプションをさらに調査することをお勧めします。

## <a name="takeaways"></a>重要なポイント

このプロジェクトの重要なポイントを示します。

* 番号付きページングは、結果の順序がやや恣意的な検索に適しています。つまり、ユーザーが関心を持つものが、後の方のページにありそうな場合です。
* 無限スクロールは、結果の順序が特に重要な場合に適しています。 たとえば、行き先の都市の中心からの距離に基づいて結果が並べ替えられるような場合です。
* 番号付きページングの方が、より優れたナビゲーションを実現できます。 たとえば、ユーザーは興味深い結果が 6 ページにあったことを覚えているかもしれませんが、無限スクロールではそのような簡単な参照方法はありません。
* 無限スクロールは簡単であることが利点であり、ページ番号をクリックすることなく上下にスクロールできます。
* 無限スクロールの主な特長は、結果が既存のページに置き換わるのではなく、既存のページに追加されることです。これは効率的です。
* 一時ストレージは 1 回の呼び出しの間のみ保持され、後の呼び出しのときまで保持するには、再設定する必要があります。

## <a name="next-steps"></a>次のステップ

ページングは、検索エクスペリエンスの基礎です。 ページングについて十分に理解したら、次のステップは、先行入力検索を追加して、さらにユーザー エクスペリエンスを向上させることです。

> [!div class="nextstepaction"]
> [C# チュートリアル: オートコンプリートと検索候補を追加する - Azure Cognitive Search](tutorial-csharp-type-ahead-and-suggestions.md)