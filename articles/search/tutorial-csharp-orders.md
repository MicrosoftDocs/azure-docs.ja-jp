---
title: 結果の並べ替えに関する C# チュートリアル
titleSuffix: Azure Cognitive Search
description: この C# チュートリアルでは、検索結果を並べ替える方法について説明します。 これは、前のホテル プロジェクトに基づいています。プライマリ プロパティとセカンダリ プロパティによる並べ替えが追加され、引き上げ基準を追加するためのスコアリング プロファイルが含まれています。
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: tutorial
ms.date: 01/26/2021
ms.custom: devx-track-js, devx-track-csharp
ms.openlocfilehash: 1f8100dd6340383eadec5d10b7f23db59ba0ebdf
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "98786387"
---
# <a name="tutorial-order-search-results-using-the-net-sdk"></a>チュートリアル:.NET SDK を使用して検索結果を並べ替える

このチュートリアル シリーズ全体を通して、結果が返され、[既定の順序](index-add-scoring-profiles.md#what-is-default-scoring)で表示されています。 このチュートリアルでは、プライマリおよびセカンダリの並べ替え条件を追加します。 最後の例では、数値に基づく並べ替えの代わりに、カスタム スコアリング プロファイルに基づいて結果を順位付けする方法を示します。 _複合型_ の表示についても少し踏み込んで説明します。

このチュートリアルでは、以下の内容を学習します。
> [!div class="checklist"]
> * 1 つのプロパティに基づいて結果を並べ替える
> * 複数のプロパティに基づいて結果を並べ替える
> * スコアリング プロファイルに基づいて結果を並べ替える

## <a name="overview"></a>概要

このチュートリアルでは、[検索結果へのページングの追加](tutorial-csharp-paging.md)に関するチュートリアルで作成した無限スクロール プロジェクトを拡張します。

このチュートリアルのコードの完成版は、次のプロジェクトにあります。

* [5-order-results (GitHub)](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/create-first-app/v11/5-order-results)

## <a name="prerequisites"></a>前提条件

* [2b-add-infinite-scroll (GitHub)](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/create-first-app/v11/2b-add-infinite-scroll) ソリューション。 このプロジェクトは、前のチュートリアルで作成した独自のバージョンでも、GitHub からコピーしたものでもかまいません。

このチュートリアルは、[Azure.Search.Documents (バージョン 11)](https://www.nuget.org/packages/Azure.Search.Documents/) パッケージを使用するように更新されました。 .NET SDK の以前のバージョンについては、[Microsoft.Azure.Search (バージョン 10) のコード サンプル](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/create-first-app/v10)を参照してください。

## <a name="order-results-based-on-one-property"></a>1 つのプロパティに基づいて結果を並べ替える

ホテルの評価のように、1 つのプロパティに基づいて結果を並べ替える場合、並べ替えた結果が必要なだけでなく、順序が正しいことの確証も必要です。 結果に Rating フィールドを追加すると、結果が正しく並べ替えられていることを確認できます。

この演習では、結果の表示に少し情報を追加します。各ホテルの最も安い宿泊料金、最も高い宿泊料金です。

並べ替えを有効にするために修正が必要なモデルはありません。 更新が必要なのは、ビューとコントローラーのみです。 まず、ホーム コントローラーを開きます。

### <a name="add-the-orderby-property-to-the-search-parameters"></a>OrderBy プロパティを検索パラメーターに追加する

1. HomeController.cs で、**OrderBy** オプションを追加し、降順の並べ替え順序を指定した Rating プロパティを含めます。 **Index(SearchData model)** メソッドで、次の行を検索パラメーターに追加します。

    ```cs
    options.OrderBy.Add("Rating desc");
    ```

    >[!Note]
    > 既定の順序は昇順ですが、`asc` をプロパティに追加してこれを明確化できます。 降順は `desc` を追加することで指定されます。

1. ここでアプリを実行し、何か一般的な検索語を入力します。 開発者もユーザーも、結果を検証する簡単な方法を持ち合わせていないため、結果が正しい順序がどうかはわかりません。

1. 結果が評価に基づいて並べ替えられていることを明確にしましょう。 まず、hotels.css ファイルの **box1** および **box2** クラスを次のクラスに置き換えます (これらのクラスはすべて、このチュートリアルに必要な新しいクラスです)。

    ```html
    textarea.box1A {
        width: 324px;
        height: 32px;
        border: none;
        background-color: azure;
        font-size: 14pt;
        color: blue;
        padding-left: 5px;
        text-align: left;
    }

    textarea.box1B {
        width: 324px;
        height: 32px;
        border: none;
        background-color: azure;
        font-size: 14pt;
        color: blue;
        text-align: right;
        padding-right: 5px;
    }

    textarea.box2A {
        width: 324px;
        height: 32px;
        border: none;
        background-color: azure;
        font-size: 12pt;
        color: blue;
        padding-left: 5px;
        text-align: left;
    }

    textarea.box2B {
        width: 324px;
        height: 32px;
        border: none;
        background-color: azure;
        font-size: 12pt;
        color: blue;
        text-align: right;
        padding-right: 5px;
    }

    textarea.box3 {
        width: 648px;
        height: 100px;
        border: none;
        background-color: azure;
        font-size: 12pt;
        padding-left: 5px;
        margin-bottom: 24px;
    }
    ```

    > [!Tip]
    > 通常、ブラウザーは css ファイルをキャッシュしますが、これによって古い css ファイルが使用され、編集が無視される可能性があります。 これを回避するための良い方法は、バージョン パラメーターを持つクエリ文字列をリンクに追加することです。 次に例を示します。
    >
    >```html
    >   <link rel="stylesheet" href="~/css/hotels.css?v1.1" />
    >```
    >
    > ブラウザーで古い css ファイルが使用されていると思われる場合、バージョン番号を更新します。

1. **Index(SearchData model)** メソッドで、**Select** パラメーターに **Rating** プロパティを追加して、結果に次の 3 つのフィールドが含まれるようにします。

    ```cs
    options.Select.Add("HotelName");
    options.Select.Add("Description");
    options.Select.Add("Rating");
    ```

1. ビュー (index.cshtml) を開き、レンダリング ループ ( **&lt;!-- Show the hotel data. --&gt;** ) を次のコードに置き換えます。

    ```cs
    <!-- Show the hotel data. -->
    @for (var i = 0; i < result.Count; i++)
    {
        var ratingText = $"Rating: {result[i].Document.Rating}";

        // Display the hotel details.
        @Html.TextArea($"name{i}", result[i].Document.HotelName, new { @class = "box1A" })
        @Html.TextArea($"rating{i}", ratingText, new { @class = "box1B" })
        @Html.TextArea($"desc{i}", fullDescription, new { @class = "box3" })
    }
    ```

1. 最初に表示されるページと、無限スクロールを介して呼び出される後続のページのどちらでも、評価が確認できる必要があります。 これら 2 つの状況のうち後者では、コントローラーの **Next** アクションとビューの **scrolled** 関数の両方を更新する必要があります。 コントローラーから始めて、**Next** メソッドを次のコードに変更します。 このコードは評価テキストを作成および伝達します。

    ```cs
    public async Task<ActionResult> Next(SearchData model)
    {
        // Set the next page setting, and call the Index(model) action.
        model.paging = "next";
        await Index(model);

        // Create an empty list.
        var nextHotels = new List<string>();

        // Add a hotel details to the list.
        await foreach (var result in model.resultList.GetResultsAsync())
        {
            var ratingText = $"Rating: {result.Document.Rating}";
            var rateText = $"Rates from ${result.Document.cheapest} to ${result.Document.expensive}";
    
            string fullDescription = result.Document.Description;
    
            // Add strings to the list.
            nextHotels.Add(result.Document.HotelName);
            nextHotels.Add(ratingText);
            nextHotels.Add(fullDescription);
        }

        // Rather than return a view, return the list of data.
        return new JsonResult(nextHotels);
    }
    ```

1. 次に、評価テキストを表示するために、ビューの **scrolled** 関数を更新します。

    ```javascript
    <script>
        function scrolled() {
            if (myDiv.offsetHeight + myDiv.scrollTop >= myDiv.scrollHeight) {
                $.getJSON("/Home/Next", function (data) {
                    var div = document.getElementById('myDiv');

                    // Append the returned data to the current list of hotels.
                    for (var i = 0; i < data.length; i += 3) {
                        div.innerHTML += '\n<textarea class="box1A">' + data[i] + '</textarea>';
                        div.innerHTML += '\n<textarea class="box1B">' + data[i + 1] + '</textarea>';
                        div.innerHTML += '\n<textarea class="box3">' + data[i + 2] + '</textarea>';
                    }
                });
            }
        }
    </script>
    ```

1. ここで、アプリをもう一度実行します。 "wifi" などの一般的な言葉で検索し、結果がホテルの評価の降順で並べ替えられていることを確認します。

    ![評価に基づいた並べ替え](./media/tutorial-csharp-create-first-app/azure-search-orders-rating.png)

    複数のホテルの評価が同じであり、それらはデータが見つかった順という不定の順序で表示されます。

    並べ替えの 2 番目のレベルを追加することを検討する前に、宿泊料金の範囲を表示するためのコードを追加しましょう。 このコードを追加することで、_複合型_ からデータを抽出する方法に加えて、料金に基づいた結果の並べ替え (安い順) について論じることができます。

### <a name="add-the-range-of-room-rates-to-the-view"></a>宿泊料金の範囲をビューに追加する

1. 最低および最高の宿泊料金を含むプロパティを Hotel.cs モデルに追加します。

    ```cs
    // Room rate range
    public double cheapest { get; set; }
    public double expensive { get; set; }
    ```

1. ホーム コントローラーで、**Index(SearchData model)** アクションの最後に宿泊料金を計算します。 一時データの保存よりも後に計算を追加してください。

    ```cs
    // Ensure TempData is stored for the next call.
    TempData["page"] = page;
    TempData["searchfor"] = model.searchText;

    // Calculate the room rate ranges.
    await foreach (var result in model.resultList.GetResultsAsync())
    {
        var cheapest = 0d;
        var expensive = 0d;

        foreach (var room in result.Document.Rooms)
        {
            var rate = room.BaseRate;
            if (rate < cheapest || cheapest == 0)
            {
                cheapest = (double)rate;
            }
            if (rate > expensive)
            {
                expensive = (double)rate;
            }
        }
        model.resultList.Results[n].Document.cheapest = cheapest;
        model.resultList.Results[n].Document.expensive = expensive;
    }
    ```

1. コントローラーの **Index(SearchData model)** アクション メソッドの **Select** パラメーターに **Rooms** プロパティを追加します。

    ```cs
    options.Select.Add("Rooms");
    ```

1. ビューのレンダリング ループを変更して、結果の 1 ページ目に料金範囲を表示するようにします。

    ```cs
    <!-- Show the hotel data. -->
    @for (var i = 0; i < result.Count; i++)
    {
        var rateText = $"Rates from ${result[i].Document.cheapest} to ${result[i].Document.expensive}";
        var ratingText = $"Rating: {result[i].Document.Rating}";

        string fullDescription = result[i].Document.Description;

        // Display the hotel details.
        @Html.TextArea($"name{i}", result[i].Document.HotelName, new { @class = "box1A" })
        @Html.TextArea($"rating{i}", ratingText, new { @class = "box1B" })
        @Html.TextArea($"rates{i}", rateText, new { @class = "box2A" })
        @Html.TextArea($"desc{i}", fullDescription, new { @class = "box3" })
    }
    ```

1. ホーム コントローラーの **Next** メソッドを変更して、結果の後続ページの料金範囲を伝達するようにします。

    ```cs
    public async Task<ActionResult> Next(SearchData model)
    {
        // Set the next page setting, and call the Index(model) action.
        model.paging = "next";
        await Index(model);

        // Create an empty list.
        var nextHotels = new List<string>();

        // Add a hotel details to the list.
        await foreach (var result in model.resultList.GetResultsAsync())
        {
            var ratingText = $"Rating: {result.Document.Rating}";
            var rateText = $"Rates from ${result.Document.cheapest} to ${result.Document.expensive}";

            string fullDescription = result.Document.Description;

            // Add strings to the list.
            nextHotels.Add(result.Document.HotelName);
            nextHotels.Add(ratingText);
            nextHotels.Add(rateText);
            nextHotels.Add(fullDescription);
        }

        // Rather than return a view, return the list of data.
        return new JsonResult(nextHotels);
    }
    ```

1. ビューの **scrolled** 関数を更新して、宿泊料金のテキストを処理するようにします。

    ```javascript
    <script>
        function scrolled() {
            if (myDiv.offsetHeight + myDiv.scrollTop >= myDiv.scrollHeight) {
                $.getJSON("/Home/Next", function (data) {
                    var div = document.getElementById('myDiv');

                    // Append the returned data to the current list of hotels.
                    for (var i = 0; i < data.length; i += 4) {
                        div.innerHTML += '\n<textarea class="box1A">' + data[i] + '</textarea>';
                        div.innerHTML += '\n<textarea class="box1B">' + data[i + 1] + '</textarea>';
                        div.innerHTML += '\n<textarea class="box2A">' + data[i + 2] + '</textarea>';
                        div.innerHTML += '\n<textarea class="box3">' + data[i + 4] + '</textarea>';
                    }
                });
            }
        }
    </script>
    ```

1. アプリを実行し、宿泊料金の範囲が表示されていることを確認します。

    ![宿泊料金範囲の表示](./media/tutorial-csharp-create-first-app/azure-search-orders-rooms.png)

検索パラメーターの **OrderBy** プロパティは、客室が既に料金順で並べ替えられている場合でも、最安の宿泊料金を提供するための **Rooms.BaseRate** のようなエントリを受け付けません。 このケースでは、客室が料金で並べ替えられることはありません。 サンプル データ セットのホテルを宿泊料金で並べ替えて表示するためには、ホーム コントローラーで結果を並べ替えて、これらの結果を希望の順序でビューに送信する必要があります。

## <a name="order-results-based-on-multiple-values"></a>複数の値に基づいて結果を並べ替える

ここでの問題は、同じ評価のホテルをどのように区別するかです。 1 つの方法として、ホテルが最後に改装された時期に基づいて二次並べ替えを実行し、最近改装されたホテルが結果の上位に表示されるようにします。

1. 並べ替えの第 2 レベルを追加するには、**Index(SearchData model)** メソッドで、検索結果と **OrderBy** に **LastRenovationDate** を追加します。

    ```cs
    options.Select.Add("LastRenovationDate");

    options.OrderBy.Add("LastRenovationDate desc");
    ```

    >[!Tip]
    > **OrderBy** リストにはプロパティをいくつでも入力できます。 ホテルの評価と改装日が同じである場合に、それらを区別するための 3 番目のプロパティを入力できます。

1. ここでも、並べ替えが正しいことを確認するためだけに、改装日をビューに表示する必要があります。 改装のような事象については、年だけで十分でしょう。 ビューのレンダリング ループを次のコードに変更します。

    ```cs
    <!-- Show the hotel data. -->
    @for (var i = 0; i < result.Count; i++)
    {
        var rateText = $"Rates from ${result[i].Document.cheapest} to ${result[i].Document.expensive}";
        var lastRenovatedText = $"Last renovated: { result[i].Document.LastRenovationDate.Value.Year}";
        var ratingText = $"Rating: {result[i].Document.Rating}";

        string fullDescription = result[i].Document.Description;

        // Display the hotel details.
        @Html.TextArea($"name{i}", result[i].Document.HotelName, new { @class = "box1A" })
        @Html.TextArea($"rating{i}", ratingText, new { @class = "box1B" })
        @Html.TextArea($"rates{i}", rateText, new { @class = "box2A" })
        @Html.TextArea($"renovation{i}", lastRenovatedText, new { @class = "box2B" })
        @Html.TextArea($"desc{i}", fullDescription, new { @class = "box3" })
    }
    ```

1. ホーム コントローラーの **Next** メソッドを変更して、最終改装日の年の部分を転送するようにします。

    ```cs
        public async Task<ActionResult> Next(SearchData model)
        {
            // Set the next page setting, and call the Index(model) action.
            model.paging = "next";
            await Index(model);

            // Create an empty list.
            var nextHotels = new List<string>();

            // Add a hotel details to the list.
            await foreach (var result in model.resultList.GetResultsAsync())
            {
                var ratingText = $"Rating: {result.Document.Rating}";
                var rateText = $"Rates from ${result.Document.cheapest} to ${result.Document.expensive}";
                var lastRenovatedText = $"Last renovated: {result.Document.LastRenovationDate.Value.Year}";

                string fullDescription = result.Document.Description;

                // Add strings to the list.
                nextHotels.Add(result.Document.HotelName);
                nextHotels.Add(ratingText);
                nextHotels.Add(rateText);
                nextHotels.Add(lastRenovatedText);
                nextHotels.Add(fullDescription);
            }

            // Rather than return a view, return the list of data.
            return new JsonResult(nextHotels);
        }
    ```

1. ビューの **scrolled** 関数を変更して、改装テキストを表示するようにします。

    ```javascript
    <script>
        function scrolled() {
            if (myDiv.offsetHeight + myDiv.scrollTop >= myDiv.scrollHeight) {
                $.getJSON("/Home/Next", function (data) {
                    var div = document.getElementById('myDiv');

                    // Append the returned data to the current list of hotels.
                    for (var i = 0; i < data.length; i += 5) {
                        div.innerHTML += '\n<textarea class="box1A">' + data[i] + '</textarea>';
                        div.innerHTML += '\n<textarea class="box1B">' + data[i + 1] + '</textarea>';
                        div.innerHTML += '\n<textarea class="box2A">' + data[i + 2] + '</textarea>';
                        div.innerHTML += '\n<textarea class="box2B">' + data[i + 3] + '</textarea>';
                        div.innerHTML += '\n<textarea class="box3">' + data[i + 4] + '</textarea>';
                    }
                });
            }
        }
    </script>
    ```

1. アプリケーションを実行します。 "pool" (プール) や "view" (眺望) などの一般的な言葉を検索し、同じ評価のホテルが改装日の降順で表示されていることを確認します。

    ![改装日での並べ替え](./media/tutorial-csharp-create-first-app/azure-search-orders-renovation.png)

## <a name="order-results-based-on-a-scoring-profile"></a>スコアリング プロファイルに基づいて結果を並べ替える

このチュートリアルでこれまで示した例は、数値 (評価と改装日) で並べ替える方法を示しており、"_正確な_" 並べ替えシーケンスを提供します。 ただし、検索やデータによっては、2 つのデータ要素の比較がこれまでのように簡単にはいかない場合があります。 フルテキスト検索クエリの場合、Cognitive Search には、"_ランク付け_" の概念があります。 結果をランク付けする方法に影響を与える "_スコアリング プロファイル_" を指定すると、より複雑な定性的比較を提供できます。

[スコアリング プロファイル](index-add-scoring-profiles.md)は、インデックス スキーマで定義されます。 hotels データに対して複数のスコアリング プロファイルがセットアップされています。 スコアリング プロファイルの定義方法を見てから、それに基づいた検索を行うためのコードを記述してみましょう。

### <a name="how-scoring-profiles-are-defined"></a>スコアリング プロファイルの定義方法

スコアリング プロファイルは、設計時に検索インデックスで定義されます。 Microsoft がホストする読み取り専用の hotels インデックスには、3 つのスコアリング プロファイルがあります。 このセクションでは、スコアリング プロファイルについて説明し、コードでの使用方法を示します。

1. hotels データ セットの既定のスコアリング プロファイルを次に示します。これは、**OrderBy** または **ScoringProfile** パラメーターを指定しない場合に使用されます。 このプロファイルは、検索テキストがホテル名、説明、またはタグ (アメニティ) のリストに含まれている場合、ホテルの _スコア_ を上げます。 スコアリングの重みが特定のフィールドを優遇するしくみに注目してください。 検索テキストが、下のリストにない別のフィールドに含まれている場合、その重みは 1 になります。 言うまでもなく、スコアが高いほど、結果がビューで上位に表示されます。

     ```cs
    {
        "name": "boostByField",
        "text": {
            "weights": {
                "Tags": 3,
                "HotelName": 2,
                "Description": 1.5,
                "Description_fr": 1.5,
            }
        }
    }
    ```

1. 次に示す別のスコアリング プロファイルでは、指定されたパラメーターに、タグ (ここでは "アメニティ" と呼びます) のリストが 1 つ以上含まれている場合に、スコアが大幅に上がります。 このプロファイルの重要な点は、テキストを含むパラメーターの指定が _必須_ であることです。 パラメーターが空、または指定されていない場合、エラーが発生します。

    ```cs
    {
        "name":"boostAmenities",
        "functions":[
            {
            "fieldName":"Tags",
            "freshness":null,
            "interpolation":"linear",
            "magnitude":null,
            "distance":null,
            "tag":{
                "tagsParameter":"amenities"
            },
            "type":"tag",
            "boost":5
            }
        ],
        "functionAggregation":0
    },
    ```

1. この 3 番目のプロファイルでは、ホテルの評価によってスコアが大幅に上がります。 最終改装日もスコアを上げますが、そのデータが現在の日付から 730 日 (2 年) 以内の場合に限られます。

    ```cs
    {
        "name":"renovatedAndHighlyRated",
        "functions":[
            {
            "fieldName":"Rating",
            "freshness":null,
            "interpolation":"linear",
            "magnitude":{
                "boostingRangeStart":0,
                "boostingRangeEnd":5,
                "constantBoostBeyondRange":false
            },
            "distance":null,
            "tag":null,
            "type":"magnitude",
            "boost":20
            },
            {
            "fieldName":"LastRenovationDate",
            "freshness":{
                "boostingDuration":"P730D"
            },
            "interpolation":"quadratic",
            "magnitude":null,
            "distance":null,
            "tag":null,
            "type":"freshness",
            "boost":10
            }
        ],
        "functionAggregation":0
    }
    ```

    それでは、これらのプロファイルが想定どおりに機能するかどうかを確認しましょう。

### <a name="add-code-to-the-view-to-compare-profiles"></a>プロファイルを比較するためのコードをビューに追加する

1. index.cshtml ファイルを開き、&lt;body&gt; セクションを次のコードに置き換えます。

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
                        Hotels Search - Order Results
                    </h1>
                </td>
            </tr>
            <tr>
                <td></td>
                <td>
                    <!-- Display the search text box, with the search icon to the right of it. -->
                    <div class="searchBoxForm">
                        @Html.TextBoxFor(m => m.searchText, new { @class = "searchBox" }) <input class="searchBoxSubmit" type="submit" value="">
                    </div>

                    <div class="searchBoxForm">
                        <b>&nbsp;Order:&nbsp;</b>
                        @Html.RadioButtonFor(m => m.scoring, "Default") Default&nbsp;&nbsp;
                        @Html.RadioButtonFor(m => m.scoring, "RatingRenovation") By numerical Rating&nbsp;&nbsp;
                        @Html.RadioButtonFor(m => m.scoring, "boostAmenities") By Amenities&nbsp;&nbsp;
                        @Html.RadioButtonFor(m => m.scoring, "renovatedAndHighlyRated") By Renovated date/Rating profile&nbsp;&nbsp;
                    </div>
                </td>
            </tr>

            <tr>
                <td valign="top">
                    <div id="facetplace" class="facetchecks">

                        @if (Model != null && Model.facetText != null)
                        {
                            <h5 class="facetheader">Amenities:</h5>
                            <ul class="facetlist">
                                @for (var c = 0; c < Model.facetText.Length; c++)
                                {
                                    <li> @Html.CheckBoxFor(m => m.facetOn[c], new { @id = "check" + c.ToString() }) @Model.facetText[c] </li>
                                }

                            </ul>
                        }
                    </div>
                </td>
                <td>
                    @if (Model != null && Model.resultList != null)
                    {
                        // Show the total result count.
                        <p class="sampleText">
                            @Html.DisplayFor(m => m.resultList.Count) Results <br />
                        </p>

                        <div id="myDiv" style="width: 800px; height: 450px; overflow-y: scroll;" onscroll="scrolled()">

                            <!-- Show the hotel data. -->
                            @for (var i = 0; i < Model.resultList.Results.Count; i++)
                            {
                                var rateText = $"Rates from ${Model.resultList.Results[i].Document.cheapest} to ${Model.resultList.Results[i].Document.expensive}";
                                var lastRenovatedText = $"Last renovated: { Model.resultList.Results[i].Document.LastRenovationDate.Value.Year}";
                                var ratingText = $"Rating: {Model.resultList.Results[i].Document.Rating}";

                                string amenities = string.Join(", ", Model.resultList.Results[i].Document.Tags);
                                string fullDescription = Model.resultList.Results[i].Document.Description;
                                fullDescription += $"\nAmenities: {amenities}";

                                // Display the hotel details.
                                @Html.TextArea($"name{i}", Model.resultList.Results[i].Document.HotelName, new { @class = "box1A" })
                                @Html.TextArea($"rating{i}", ratingText, new { @class = "box1B" })
                                @Html.TextArea($"rates{i}", rateText, new { @class = "box2A" })
                                @Html.TextArea($"renovation{i}", lastRenovatedText, new { @class = "box2B" })
                                @Html.TextArea($"desc{i}", fullDescription, new { @class = "box3" })
                            }
                        </div>

                        <script>
                            function scrolled() {
                                if (myDiv.offsetHeight + myDiv.scrollTop >= myDiv.scrollHeight) {
                                    $.getJSON("/Home/Next", function (data) {
                                        var div = document.getElementById('myDiv');

                                        // Append the returned data to the current list of hotels.
                                        for (var i = 0; i < data.length; i += 5) {
                                            div.innerHTML += '\n<textarea class="box1A">' + data[i] + '</textarea>';
                                            div.innerHTML += '<textarea class="box1B">' + data[i + 1] + '</textarea>';
                                            div.innerHTML += '\n<textarea class="box2A">' + data[i + 2] + '</textarea>';
                                            div.innerHTML += '<textarea class="box2B">' + data[i + 3] + '</textarea>';
                                            div.innerHTML += '\n<textarea class="box3">' + data[i + 4] + '</textarea>';
                                        }
                                    });
                                }
                            }
                        </script>
                    }
                </td>
            </tr>
        </table>
    }
    </body>
    ```

1. SearchData.cs ファイルを開き、**SearchData** クラスを次のコードに置き換えます。

    ```cs
    public class SearchData
    {
        public SearchData()
        {
        }

        // Constructor to initialize the list of facets sent from the controller.
        public SearchData(List<string> facets)
        {
            facetText = new string[facets.Count];

            for (int i = 0; i < facets.Count; i++)
            {
                facetText[i] = facets[i];
            }
        }

        // Array to hold the text for each amenity.
        public string[] facetText { get; set; }

        // Array to hold the setting for each amenitity.
        public bool[] facetOn { get; set; }

        // The text to search for.
        public string searchText { get; set; }

        // Record if the next page is requested.
        public string paging { get; set; }

        // The list of results.
        public DocumentSearchResult<Hotel> resultList;

        public string scoring { get; set; }       
    }
    ```

1. hotels.css ファイルを開き、次の HTML クラスを追加します。

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

### <a name="add-code-to-the-controller-to-specify-a-scoring-profile"></a>スコアリング プロファイルを指定するためのコードをコントローラーに追加する

1. ホーム コントローラー ファイルを開きます。 (リストの作成を補助するために) 次の **using** ステートメントを追加します。

    ```cs
    using System.Linq;
    ```

1. この例では、初期ビューの取得に加えて追加処理を実行するために、**Index** の初回呼び出しが必要です。 このメソッドは、ビューに表示する最大 20 のアメニティを検索します。

    ```cs
        public async Task<ActionResult> Index()
        {
            InitSearch();

            // Set up the facets call in the search parameters.
            SearchOptions options = new SearchOptions();
            // Search for up to 20 amenities.
            options.Facets.Add("Tags,count:20");

            SearchResults<Hotel> searchResult = await _searchClient.SearchAsync<Hotel>("*", options);

            // Convert the results to a list that can be displayed in the client.
            List<string> facets = searchResult.Facets["Tags"].Select(x => x.Value.ToString()).ToList();

            // Initiate a model with a list of facets for the first view.
            SearchData model = new SearchData(facets);

            // Save the facet text for the next view.
            SaveFacets(model, false);

            // Render the view including the facets.
            return View(model);
        }
    ```

1. ファセットを一時ストレージに保存し、それらを一時ストレージから復旧してモデルにデータを入力するために、2 つのプライベート メソッドが必要です。

    ```cs
        // Save the facet text to temporary storage, optionally saving the state of the check boxes.
        private void SaveFacets(SearchData model, bool saveChecks = false)
        {
            for (int i = 0; i < model.facetText.Length; i++)
            {
                TempData["facet" + i.ToString()] = model.facetText[i];
                if (saveChecks)
                {
                    TempData["faceton" + i.ToString()] = model.facetOn[i];
                }
            }
            TempData["facetcount"] = model.facetText.Length;
        }

        // Recover the facet text to a model, optionally recoving the state of the check boxes.
        private void RecoverFacets(SearchData model, bool recoverChecks = false)
        {
            // Create arrays of the appropriate length.
            model.facetText = new string[(int)TempData["facetcount"]];
            if (recoverChecks)
            {
                model.facetOn = new bool[(int)TempData["facetcount"]];
            }

            for (int i = 0; i < (int)TempData["facetcount"]; i++)
            {
                model.facetText[i] = TempData["facet" + i.ToString()].ToString();
                if (recoverChecks)
                {
                    model.facetOn[i] = (bool)TempData["faceton" + i.ToString()];
                }
            }
        }
    ```

1. 必要に応じて、**OrderBy** および **ScoringProfile** パラメーターを設定する必要があります。 既存の **Index(SearchData model)** メソッドを次のものに置き換えます。

    ```cs
    public async Task<ActionResult> Index(SearchData model)
    {
        try
        {
            InitSearch();

            int page;

            if (model.paging != null && model.paging == "next")
            {
                // Recover the facet text, and the facet check box settings.
                RecoverFacets(model, true);

                // Increment the page.
                page = (int)TempData["page"] + 1;

                // Recover the search text.
                model.searchText = TempData["searchfor"].ToString();
            }
            else
            {
                // First search with text. 
                // Recover the facet text, but ignore the check box settings, and use the current model settings.
                RecoverFacets(model, false);

                // First call. Check for valid text input, and valid scoring profile.
                if (model.searchText == null)
                {
                    model.searchText = "";
                }
                if (model.scoring == null)
                {
                    model.scoring = "Default";
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
                IncludeTotalCount = true,
            };
            // Select the data properties to be returned.
            options.Select.Add("HotelName");
            options.Select.Add("Description");
            options.Select.Add("Tags");
            options.Select.Add("Rooms");
            options.Select.Add("Rating");
            options.Select.Add("LastRenovationDate");

            List<string> parameters = new List<string>();
            // Set the ordering based on the user's radio button selection.
            switch (model.scoring)
            {
                case "RatingRenovation":
                    // Set the ordering/scoring parameters.
                    options.OrderBy.Add("Rating desc");
                    options.OrderBy.Add("LastRenovationDate desc");
                    break;

                case "boostAmenities":
                    {
                        options.ScoringProfile = model.scoring;

                        // Create a string list of amenities that have been clicked.
                        for (int a = 0; a < model.facetOn.Length; a++)
                        {
                            if (model.facetOn[a])
                            {
                                parameters.Add(model.facetText[a]);
                            }
                        }

                        if (parameters.Count > 0)
                        {
                            options.ScoringParameters.Add($"amenities-{ string.Join(',', parameters)}");
                        }
                        else
                        {
                            // No amenities selected, so set profile back to default.
                            options.ScoringProfile = "";
                        }
                    }
                    break;

                case "renovatedAndHighlyRated":
                    options.ScoringProfile = model.scoring;
                    break;

                default:
                    break;
            }

            // For efficiency, the search call should be asynchronous, so use SearchAsync rather than Search.
            model.resultList = await _searchClient.SearchAsync<Hotel>(model.searchText, options);

            // Ensure TempData is stored for the next call.
            TempData["page"] = page;
            TempData["searchfor"] = model.searchText;
            TempData["scoring"] = model.scoring;
            SaveFacets(model, true);

            // Calculate the room rate ranges.
            await foreach (var result in model.resultList.GetResultsAsync())
            {
                var cheapest = 0d;
                var expensive = 0d;

                foreach (var room in result.Document.Rooms)
                {
                    var rate = room.BaseRate;
                    if (rate < cheapest || cheapest == 0)
                    {
                        cheapest = (double)rate;
                    }
                    if (rate > expensive)
                    {
                        expensive = (double)rate;
                    }
                }

                result.Document.cheapest = cheapest;
                result.Document.expensive = expensive;
            }
        }
        catch
        {
            return View("Error", new ErrorViewModel { RequestId = "1" });
        }

        return View("Index", model);
    }
    ```

    それぞれの **switch** 選択のコメントを最後まで読んでください。

1. 複数のプロパティに基づいた並べ替えに関する前のセクションの追加コードを完了した場合、**Next** アクションに変更を加える必要はありません。

### <a name="run-and-test-the-app"></a>アプリを実行してテストする

1. アプリケーションを実行します。 すべてのアメニティがビューに表示されます。

1. 並べ替える場合、"By numerical Rating" (評価の数字順) を選択すると、このチュートリアルで既に実装した数値による並べ替えを実行できます。このとき、評価が同じホテルの表示順は改装日によって決まります。

   ![評価に基づいた "beach" の並べ替え](./media/tutorial-csharp-create-first-app/azure-search-orders-beach.png)

1. ここで "By amenities" (アメニティ順) プロファイルを試してみましょう。 さまざまなアメニティを選択し、結果リストで、それらのアメニティを備えたホテルの表示順が上がることを確認します。

   ![プロファイルに基づいた "beach" の並べ替え](./media/tutorial-csharp-create-first-app/azure-search-orders-beach-profile.png)

1. "By Renovated date/Rating profile" (改装日/評価順プロファイル) を試して、期待する結果が得られるかどうか確認します。 最近改装されたホテルのみ、_freshness_ (新しさ) が上昇するはずです。

### <a name="resources"></a>リソース

詳細については、次の「[スコアリング プロファイルを Azure Cognitive Search のインデックスに追加する](./index-add-scoring-profiles.md)」を参照してください。

## <a name="takeaways"></a>重要なポイント

このプロジェクトの重要なポイントを示します。

* ユーザーが期待するのは、最も関連性の高いものが最初に来るように検索結果が並べ替えられることです。
* 並べ替えが簡単になるようにデータを構造化する必要があります。 追加コードなしで並べ替えを実行できるほどデータが構造化されていないため、"最も安い" ものを最初に表示する並べ替えが簡単にはできませんでした。
* より上のレベルでの並べ替えで値が同じである結果を差別化するために、並べ替えを複数のレベルにすることができます。
* 当然ながら、(ある地点からの距離のように) 昇順で並べ替える結果もあれば、(宿泊客の評価のように) 降順で並べ替える結果もあります。
* スコアリング プロファイルは、データ セットに対して数値での比較が利用できない、またはそれによって適切な結果が得られない場合に定義できます。 個々の結果にスコアを付けると、結果をインテリジェントに並べ替えて表示するために役立ちます。

## <a name="next-steps"></a>次のステップ

この一連の C# チュートリアルを完了しました。Azure Cognitive Search API に関する貴重な知識が得られたことでしょう。

さらなるリファレンスやチュートリアルについては、[Microsoft Learn](/learn/browse/?products=azure) や、[Azure Cognitive Search ドキュメント](./index.yml)のその他のチュートリアルをぜひ参考にしてください。