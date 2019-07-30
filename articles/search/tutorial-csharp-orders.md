---
title: 結果の並べ替えに関する C# のチュートリアル - Azure Search
description: このチュートリアルでは、「検索結果のページング - Azure Search」プロジェクトに検索結果の並べ替えを追加します。 プライマリ プロパティで結果を並べ替える方法、および、プライマリ プロパティが同じである結果をセカンダリ プロパティで並べ替える方法を学びます。 最後に、スコアリング プロファイルに基づいて結果を並べ替える方法を学びます。
services: search
ms.service: search
ms.topic: tutorial
ms.author: v-pettur
author: PeterTurcan
ms.date: 06/21/2019
ms.openlocfilehash: 32e253b4e131d753ab6937d0aa2a49bda471e091
ms.sourcegitcommit: c63e5031aed4992d5adf45639addcef07c166224
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/28/2019
ms.locfileid: "67466546"
---
# <a name="c-tutorial-order-the-results---azure-search"></a>C# のチュートリアル: 結果の並べ替え - Azure Search

これまでのチュートリアルでは、結果は既定の順序で返され、表示されてきました。 これはデータの配置順である場合もあれば、並べ替えパラメーターが指定されていないときに使用される既定の_スコアリング プロファイル_が定義されている場合もあります。 このチュートリアルでは、プライマリ プロパティに基づいて結果を並べ替える方法について、次に、結果のプライマリ プロパティが同じである場合にセカンダリ プロパティでその選択を並べ替える方法について説明します。 最後の例では、数値に基づいた並べ替えの代わりに、カスタム スコアリング プロファイルに基づいて並べ替える方法を示しています。 _複合型_の表示についても少し踏み込んで説明します。

返された結果を比較しやすくするため、このプロジェクトは 「[C# のチュートリアル: 検索結果のページング - Azure Search](tutorial-csharp-paging.md)」チュートリアルで作成した無限スクロール プロジェクトを基にしています。

このチュートリアルでは、以下の内容を学習します。
> [!div class="checklist"]
> * 1 つのプロパティに基づいて結果を並べ替える
> * 複数のプロパティに基づいて結果を並べ替える
> * ある地理的位置からの距離に基づいて結果をフィルター処理する
> * スコアリング プロファイルに基づいて結果を並べ替える

## <a name="prerequisites"></a>前提条件

このチュートリアルを完了するには、以下を実行する必要があります。

無限スクロール バージョンの「[C# のチュートリアル: 検索結果のページング - Azure Search](tutorial-csharp-paging.md)」プロジェクトを実行できるようにします。 このプロジェクトは、独自のバージョンのものでも、GitHub の「[Create first app (初めてのアプリを作成する)](https://github.com/Azure-Samples/azure-search-dotnet-samples)」からインストールしたものでも構いません。

## <a name="order-results-based-on-one-property"></a>1 つのプロパティに基づいて結果を並べ替える

ホテルの評価のように、1 つのプロパティに基づいて結果を並べ替える場合、並べ替えた結果が必要なだけでなく、順序が正しいことの確証も必要です。 つまり、評価に基づいて並べ替える場合、評価をビューに表示する必要があります。

このチュートリアルでは、結果の表示に少し情報を追加します。各ホテルの最も安い宿泊料金、最も高い宿泊料金です。 値を追加したり、並べ替えの基準もビューに表示したりして、並べ替えをより高度なものにしていきます。

並べ替えを有効にするために修正が必要なモデルはありません。 ビューとコントローラーは更新する必要があります。 まず、ホーム コントローラーを開きます。

### <a name="add-the-orderby-property-to-the-search-parameters"></a>OrderBy プロパティを検索パラメーターに追加する

1. 単一の数値プロパティに基づいて結果を並べ替えるために必要なのは、**OrderBy** パラメーターをプロパティの名前に設定することだけです。 **Index(SearchData model)** メソッドで、次の行を検索パラメーターに追加します。

    ```cs
        OrderBy = new[] { "Rating desc" },
    ```

    >[!Note]
    > 既定の順序は昇順ですが、**asc** をプロパティに追加してこれを明確化できます。 降順は **desc** を追加することで指定されます。

2. ここでアプリを実行し、何か一般的な検索語を入力します。 開発者もユーザーも、結果を検証する簡単な方法を持ち合わせていないため、結果が正しい順序がどうかはわかりません。

3. 結果が評価に基づいて並べ替えられていることを明確にしましょう。 まず、hotels.css ファイルの **box1** および **box2** クラスを次のクラスに置き換えます (これらのクラスはすべて、このチュートリアルに必要な新しいクラスです)。

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

    >[!Tip]
    >通常、ブラウザーは css ファイルをキャッシュしますが、これによって古い css ファイルが使用され、編集が無視される可能性があります。 これを回避するための良い方法は、バージョン パラメーターを持つクエリ文字列をリンクに追加することです。 例:
    >
    >```html
    >   <link rel="stylesheet" href="~/css/hotels.css?v1.1" />
    >```
    >
    >ブラウザーで古い css ファイルが使用されていると思われる場合、バージョン番号を更新します。

4. **Index(SearchData model)** メソッドで、**Rating** プロパティを **Select** パラメーターに追加します。

    ```cs
    Select = new[] { "HotelName", "Description", "Rating"},
    ```

5. ビュー (index.cshtml) を開き、レンダリング ループ ( **&lt;!-- Show the hotel data. --&gt;** ) を次のコードに置き換えます。

    ```cs
                <!-- Show the hotel data. -->
                @for (var i = 0; i < Model.resultList.Results.Count; i++)
                {
                    var ratingText = $"Rating: {Model.resultList.Results[i].Document.Rating}";

                    // Display the hotel details.
                    @Html.TextArea($"name{i}", Model.resultList.Results[i].Document.HotelName, new { @class = "box1A" })
                    @Html.TextArea($"rating{i}", ratingText, new { @class = "box1B" })
                    @Html.TextArea($"desc{i}", Model.resultList.Results[i].Document.Description, new { @class = "box3" })
                }
    ```

6. 最初に表示されるページと、無限スクロールを介して呼び出される後続のページのどちらでも、評価が確認できる必要があります。 これら 2 つの状況のうち後者では、コントローラーの **Next** アクションとビューの **scrolled** 関数の両方を更新する必要があります。 コントローラーから始めて、**Next** メソッドを次のコードに変更します。 このコードは評価テキストを作成および伝達します。

    ```cs
        public async Task<ActionResult> Next(SearchData model)
        {
            // Set the next page setting, and call the Index(model) action.
            model.paging = "next";
            await Index(model);

            // Create an empty list.
            var nextHotels = new List<string>();

            // Add a hotel details to the list.
            for (int n = 0; n < model.resultList.Results.Count; n++)
            {
                var ratingText = $"Rating: {model.resultList.Results[n].Document.Rating}";

                // Add three strings to the list.
                nextHotels.Add(model.resultList.Results[n].Document.HotelName);
                nextHotels.Add(ratingText);
                nextHotels.Add(model.resultList.Results[n].Document.Description);
            }

            // Rather than return a view, return the list of data.
            return new JsonResult(nextHotels);
        }
    ```

7. 次に、評価テキストを表示するために、ビューの **scrolled** 関数を更新します。

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

8. ここで、アプリをもう一度実行します。 "wifi" などの一般的な言葉で検索し、結果がホテルの評価の降順で並べ替えられていることを確認します。

    ![評価に基づいた並べ替え](./media/tutorial-csharp-create-first-app/azure-search-orders-rating.png)

    複数のホテルの評価が同じであり、それらはデータが見つかった順という不定の順序で表示されます。

    並べ替えの 2 番目のレベルを追加することを検討する前に、宿泊料金の範囲を表示するためのコードを追加しましょう。 このコードを追加することで、_複合型_からデータを抽出する方法に加えて、料金に基づいた結果の並べ替え (安い順) について論じることができます。

### <a name="add-the-range-of-room-rates-to-the-view"></a>宿泊料金の範囲をビューに追加する

1. 最低および最高の宿泊料金を含むプロパティを Hotel.cs モデルに追加します。

    ```cs
        // Room rate range
        public double cheapest { get; set; }
        public double expensive { get; set; }
    ```

2. ホーム コントローラーで、**Index(SearchData model)** アクションの最後に宿泊料金を計算します。 一時データの保存よりも後に計算を追加してください。

    ```cs
                // Ensure TempData is stored for the next call.
                TempData["page"] = page;
                TempData["searchfor"] = model.searchText;

                // Calculate the room rate ranges.
                for (int n = 0; n < model.resultList.Results.Count; n++)
                {
                    // Calculate room rates.
                    var cheapest = 0d;
                    var expensive = 0d;

                    for (var r = 0; r < model.resultList.Results[n].Document.Rooms.Length; r++)
                    {
                        var rate = model.resultList.Results[n].Document.Rooms[r].BaseRate;
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

3. コントローラーの **Index(SearchData model)** アクション メソッドの **Select** パラメーターに **Rooms** プロパティを追加します。

    ```cs
     Select = new[] { "HotelName", "Description", "Rating", "Rooms" },
    ```

4. ビューのレンダリング ループを変更して、結果の 1 ページ目に料金範囲を表示するようにします。

    ```cs
                <!-- Show the hotel data. -->
                @for (var i = 0; i < Model.resultList.Results.Count; i++)
                {
                    var rateText = $"Rates from ${Model.resultList.Results[i].Document.cheapest} to ${Model.resultList.Results[i].Document.expensive}";
                    var ratingText = $"Rating: {Model.resultList.Results[i].Document.Rating}";

                    // Display the hotel details.
                    @Html.TextArea($"name{i}", Model.resultList.Results[i].Document.HotelName, new { @class = "box1A" })
                    @Html.TextArea($"rating{i}", ratingText, new { @class = "box1B" })
                    @Html.TextArea($"rates{i}" , rateText, new { @class = "box2A" })
                    @Html.TextArea($"desc{i}", Model.resultList.Results[i].Document.Description, new { @class = "box3" })
                }
    ```

5. ホーム コントローラーの **Next** メソッドを変更して、結果の後続ページの料金範囲を伝達するようにします。

    ```cs
        public async Task<ActionResult> Next(SearchData model)
        {
            // Set the next page setting, and call the Index(model) action.
            model.paging = "next";
            await Index(model);

            // Create an empty list.
            var nextHotels = new List<string>();

            // Add a hotel details to the list.
            for (int n = 0; n < model.resultList.Results.Count; n++)
            {
                var ratingText = $"Rating: {model.resultList.Results[n].Document.Rating}";
                var rateText = $"Rates from ${model.resultList.Results[n].Document.cheapest} to ${model.resultList.Results[n].Document.expensive}";

                // Add strings to the list.
                nextHotels.Add(model.resultList.Results[n].Document.HotelName);
                nextHotels.Add(ratingText);
                nextHotels.Add(rateText);
                nextHotels.Add(model.resultList.Results[n].Document.Description);
            }

            // Rather than return a view, return the list of data.
            return new JsonResult(nextHotels);
        }
    ```

6. ビューの **scrolled** 関数を更新して、宿泊料金のテキストを処理するようにします。

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

7. アプリを実行し、宿泊料金の範囲が表示されていることを確認します。

    ![宿泊料金範囲の表示](./media/tutorial-csharp-create-first-app/azure-search-orders-rooms.png)

検索パラメーターの **OrderBy** プロパティは、客室が既に料金順で並べ替えられている (そうでない) 場合でも、最安の宿泊料金を提供するための **Rooms.BaseRate** のようなエントリを受け付けません。 サンプル データ セットのホテルを宿泊料金で並べ替えて表示するためには、ホーム コントローラーで結果を並べ替えて、これらの結果を希望の順序でビューに送信する必要があります。

## <a name="order-results-based-on-multiple-values"></a>複数の値に基づいて結果を並べ替える

ここでの問題は、同じ評価のホテルをどのように区別するかです。 1 つの良い方法は、ホテルが最後に改装された時期に基づいて並べ替えることです。 つまり、最も新しく改装されたホテルが、結果の一番上に表示されるようにします。

1. 並べ替えの 2 番目のレベルを追加するには、**Index(SearchData model)** メソッドの **OrderBy** および **Select** プロパティに、**LastRenovationDate** プロパティが含まれるように変更します。

    ```cs
    OrderBy = new[] { "Rating desc", "LastRenovationDate desc" },
    Select = new[] { "HotelName", "Description", "Rating", "Rooms", "LastRenovationDate" },
    ```

    >[!Tip]
    >**OrderBy** リストにはプロパティをいくつでも入力できます。 ホテルの評価と改装日が同じである場合に、それらを区別するための 3 番目のプロパティを入力できます。

2. ここでも、並べ替えが正しいことを確認するためだけに、改装日をビューに表示する必要があります。 改装のような事象については、年だけで十分でしょう。 ビューのレンダリング ループを次のコードに変更します。

    ```cs
                <!-- Show the hotel data. -->
                @for (var i = 0; i < Model.resultList.Results.Count; i++)
                {
                    var rateText = $"Rates from ${Model.resultList.Results[i].Document.cheapest} to ${Model.resultList.Results[i].Document.expensive}";
                    var lastRenovatedText = $"Last renovated: { Model.resultList.Results[i].Document.LastRenovationDate.Value.Year}";
                    var ratingText = $"Rating: {Model.resultList.Results[i].Document.Rating}";

                    // Display the hotel details.
                    @Html.TextArea($"name{i}", Model.resultList.Results[i].Document.HotelName, new { @class = "box1A" })
                    @Html.TextArea($"rating{i}", ratingText, new { @class = "box1B" })
                    @Html.TextArea($"rates{i}" , rateText, new { @class = "box2A" })
                    @Html.TextArea($"renovation{i}", lastRenovatedText, new { @class = "box2B" })
                    @Html.TextArea($"desc{i}", Model.resultList.Results[i].Document.Description, new { @class = "box3" })
                }
    ```

3. ホーム コントローラーの **Next** メソッドを変更して、最終改装日の年の部分を転送するようにします。

    ```cs
        public async Task<ActionResult> Next(SearchData model)
        {
            // Set the next page setting, and call the Index(model) action.
            model.paging = "next";
            await Index(model);

            // Create an empty list.
            var nextHotels = new List<string>();

            // Add a hotel details to the list.
            for (int n = 0; n < model.resultList.Results.Count; n++)
            {
                var ratingText = $"Rating: {model.resultList.Results[n].Document.Rating}";
                var rateText = $"Rates from ${model.resultList.Results[n].Document.cheapest} to ${model.resultList.Results[n].Document.expensive}";
                var lastRenovatedText = $"Last renovated: {model.resultList.Results[n].Document.LastRenovationDate.Value.Year}";

                // Add strings to the list.
                nextHotels.Add(model.resultList.Results[n].Document.HotelName);
                nextHotels.Add(ratingText);
                nextHotels.Add(rateText);
                nextHotels.Add(lastRenovatedText);
                nextHotels.Add(model.resultList.Results[n].Document.Description);
            }

            // Rather than return a view, return the list of data.
            return new JsonResult(nextHotels);
        }
    ```

4. ビューの **scrolled** 関数を変更して、改装テキストを表示するようにします。

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

5. アプリケーションを実行します。 "pool" (プール) や "view" (眺望) などの一般的な言葉を検索し、同じ評価のホテルが改装日の降順で表示されていることを確認します。

    ![改装日での並べ替え](./media/tutorial-csharp-create-first-app/azure-search-orders-renovation.png)

## <a name="filter-results-based-on-a-distance-from-a-geographical-point"></a>ある地理的位置からの距離に基づいて結果をフィルター処理する

評価や改装日は、降順で表示するのが最適なプロパティの例です。 アルファベット順の一覧は昇順の使用が適している例です (たとえば、**OrderBy** プロパティが 1 つだけあり、それが **HotelName** に設定されている場合、アルファベット順の並びで表示されます)。 ただし、今回のサンプル データについては、地理的位置からの距離のほうが適切です。

地理的な距離に基づいて結果を表示するには、いくつかの手順が必要です。

1. 経度、緯度、半径のパラメーターを持つフィルターを入力して、特定の地点とそこを中心にした半径で指定された範囲の外にあるすべてのホテルを除外します。 まず、POINT 関数に経度が与えられます。 半径はキロメートル単位です。

    ```cs
        // "Location" must match the field name in the Hotel class.
        // Distance (the radius) is in kilometers.
        // Point order is Longitude then Latitude.
        Filter = $"geo.distance(Location, geography'POINT({model.lon} {model.lat})') le {model.radius}",
    ```

2. 上記のフィルターは、距離に基づいて結果を並べ替える_のではなく_、外れ値を削除するだけです。 結果を並べ替えるには、geoDistance メソッドを指定する **OrderBy** 設定を入力します。

    ```cs
    OrderBy = new[] { $"geo.distance(Location, geography'POINT({model.lon} {model.lat})') asc" },
    ```

3. Azure Search によって距離フィルターを使用して結果が返されましたが、データと指定された地点の間の計算された距離は_返されません_。 この値を結果に表示したい場合、ビューまたはコントローラーで値を再計算します。

    次のコードは、2 つの緯度/経度点の間で距離を計算します。

    ```cs
        const double EarthRadius = 6371;

        public static double Degrees2Radians(double deg)
        {
            return deg * Math.PI / 180;
        }

        public static double DistanceInKm( double lat1,  double lon1, double lat2, double lon2)
        {
            double dlon = Degrees2Radians(lon2 - lon1);
            double dlat = Degrees2Radians(lat2 - lat1);

            double a = (Math.Sin(dlat / 2) * Math.Sin(dlat / 2)) + Math.Cos(Degrees2Radians(lat1)) * Math.Cos(Degrees2Radians(lat2)) * (Math.Sin(dlon / 2) * Math.Sin(dlon / 2));
            double angle = 2 * Math.Atan2(Math.Sqrt(a), Math.Sqrt(1 - a));
            return angle * EarthRadius;
        }
    ```

4. ここで、これらの概念を結び付ける必要があります。 ただし、これらのコード スニペットはチュートリアルの範囲内ですので、地図ベースのアプリの作成は読者向けの演習として残っています。 この例をさらに発展させるには、都市名を半径と共に入力するか、または地図上の地点を探して半径を選択することを検討してください。 これらのオプションの詳細については、次のリソースを参照してください。

* [Azure Maps のドキュメント](https://docs.microsoft.com/azure/azure-maps/)
* [Azure Maps Search サービスを使用して住所を検索する](https://docs.microsoft.com/azure/azure-maps/how-to-search-for-address)

## <a name="order-results-based-on-a-scoring-profile"></a>スコアリング プロファイルに基づいて結果を並べ替える

これまでのチュートリアルで示した例は、数値 (評価、改装日、地理的距離) で並べ替える方法を示しており、_正確な_並べ替えプロセスを提供します。 ただし、検索やデータによっては、2 つのデータ要素の比較がこれまでのように簡単にはいかない場合があります。 Azure Search には_スコアリング_の概念が含まれています。 _スコアリング プロファイル_は、データのセットに対して指定できます。テキストベースのデータを比較して最初に表示するものを決める場合などに真価を発揮する、より複雑で定性的な比較を提供するために使用できます。

スコアリング プロファイルはユーザーが定義するのではなく、通常はデータ セットの管理者が定義します。 hotels データに対して複数のスコアリング プロファイルがセットアップされています。 スコアリング プロファイルの定義方法を見てから、それに基づいた検索を行うためのコードを記述してみましょう。

### <a name="how-scoring-profiles-are-defined"></a>スコアリング プロファイルの定義方法

スコアリング プロファイルの 3 つの例を見て、それぞれが結果の順序にどのように影響する_可能性が高い_かを検討しましょう。 これらのプロファイルを記述するのは読者であるアプリ開発者ではなくデータ管理者ですが、構文を知っておくことは有益です。

1. これは hotels データ セットの既定のスコアリング プロファイルであり、**OrderBy** または **ScoringProfile** パラメーターを指定しない場合に使用されます。 このプロファイルは、検索テキストがホテル名、説明、またはタグ (アメニティ) のリストに含まれている場合、ホテルの_スコア_を上げます。 スコアリングの重みが特定のフィールドを優遇するしくみに注目してください。 検索テキストが、下のリストにない別のフィールドに含まれている場合、その重みは 1 になります。 当然ながら、スコアが高いほど、ビューで上のほうに結果が表示されます。

     ```cs
    {
            "name": "boostByField",
            "text": {
                "weights": {
                    "HotelName": 2,
                    "Description": 1.5,
                    "Description_fr": 1.5,
                    "Tags": 3
                }
            }
        }

    ```

2. 次のスコアリング プロファイルでは、与えられたパラメーターに、リスト内のタグ (ここでは "アメニティ"と呼んでいます) の 1 つ以上が含まれている場合、スコアが大きく上がります。 このプロファイルの重要な点は、テキストを含むパラメーターの指定が_必須_であることです。 パラメーターが空、または指定されていない場合、エラーが発生します。
 
    ```cs
            {
            "name": "boostAmenities",
            "functions": [
                {
                    "type": "tag",
                    "fieldName": "Tags",
                    "boost": 5,
                    "tag": {
                        "tagsParameter": "amenities"
                    }
                }
            ]
        }
    ```

3. この 3 番目の例では、評価がスコアを大きく上げます。 最終改装日もスコアを上げますが、そのデータが現在の日付から 730 日 (2 年) 以内の場合に限られます。

    ```cs
            {
            "name": "renovatedAndHighlyRated",
            "functions": [
                {
                    "type": "magnitude",
                    "fieldName": "Rating",
                    "boost": 20,
                    "interpolation": "linear",
                    "magnitude": {
                        "boostingRangeStart": 0,
                        "boostingRangeEnd": 5,
                        "constantBoostBeyondRange": false
                    }
                },
                {
                    "type": "freshness",
                    "fieldName": "LastRenovationDate",
                    "boost": 10,
                    "interpolation": "quadratic",
                    "freshness": {
                        "boostingDuration": "P730D"
                    }
                }
            ]
        }

    ```

    それでは、これらのプロファイルが想定どおりに機能するかどうか確認しましょう。

### <a name="add-code-to-the-view-to-compare-profiles"></a>プロファイルを比較するためのコードをビューに追加する

1. index.cshtml ファイルを開き、&lt;body&gt; セクションを次のコードに置き換えます。

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

2. SearchData.cs ファイルを開き、**SearchData** クラスを次のコードに置き換えます。

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

3. hotels.css ファイルを開き、次の HTML クラスを追加します。

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

2.  この例では、初期ビューの取得に加えて追加処理を実行するために、**Index** の初回呼び出しが必要です。 このメソッドは、ビューに表示する最大 20 のアメニティを検索します。

    ```cs
        public async Task<ActionResult> Index()
        {
            InitSearch();

            // Set up the facets call in the search parameters.
            SearchParameters sp = new SearchParameters()
            {
                // Search for up to 20 amenities.
                Facets = new List<string> { "Tags,count:20" },
            };

            DocumentSearchResult<Hotel> searchResult = await _indexClient.Documents.SearchAsync<Hotel>("*", sp);

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

3. ファセットを一時ストレージに保存し、それらを一時ストレージから復旧してモデルにデータを入力するために、2 つのプライベート メソッドが必要です。

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

4. 必要に応じて、**OrderBy** および **ScoringProfile** パラメーターを設定する必要があります。 既存の **Index(SearchData model)** メソッドを次のものに置き換えます。

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
                    RecoverFacets(model,false);

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

                // Set empty defaults for ordering and scoring parameters.
                var orderby = new List<string>();
                string profile = "";
                var scoringParams = new List<ScoringParameter>();

                // Set the ordering based on the user's radio button selection.
                switch (model.scoring)
                {
                    case "RatingRenovation":
                        orderby.Add("Rating desc");
                        orderby.Add("LastRenovationDate desc");
                        break;

                    case "boostAmenities":
                        {
                            profile = model.scoring;
                            var setAmenities = new List<string>();

                            // Create a string list of amenities that have been clicked.
                            for (int a = 0; a < model.facetOn.Length; a++)
                            {
                                if (model.facetOn[a])
                                {
                                    setAmenities.Add(model.facetText[a]);
                                }
                            }
                            if (setAmenities.Count > 0)
                            {
                                // Only set scoring parameters if there are any.
                                var sp = new ScoringParameter("amenities", setAmenities);
                                scoringParams.Add(sp);
                            }
                            else
                            {
                                // No amenities selected, so set profile back to default.
                                profile = "";
                            }
                        }
                        break;

                    case "renovatedAndHighlyRated":
                        profile = model.scoring;
                        break;

                    default:
                        break;
                }

                // Setup the search parameters.
                var parameters = new SearchParameters
                {
                    // Set the ordering/scoring parameters.
                    OrderBy = orderby,
                    ScoringProfile = profile,
                    ScoringParameters = scoringParams,

                    // Select the data properties to be returned.
                    Select = new[] { "HotelName", "Description", "Tags", "Rooms", "Rating", "LastRenovationDate" },
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

                // Ensure TempData is stored for the next call.
                TempData["page"] = page;
                TempData["searchfor"] = model.searchText;
                TempData["scoring"] = model.scoring;
                SaveFacets(model,true);

                // Calculate the room rate ranges.
                for (int n = 0; n < model.resultList.Results.Count; n++)
                {
                    var cheapest = 0d;
                    var expensive = 0d;

                    for (var r = 0; r < model.resultList.Results[n].Document.Rooms.Length; r++)
                    {
                        var rate = model.resultList.Results[n].Document.Rooms[r].BaseRate;
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
            }
            catch
            {
                return View("Error", new ErrorViewModel { RequestId = "1" });
            }
            return View("Index", model);
        }
    ```

    それぞれの **switch** 選択のコメントを最後まで読んでください。

5. 複数のプロパティに基づいた並べ替えに関する前のセクションの追加コードを完了した場合、**Next** アクションに変更を加える必要はありません。

### <a name="run-and-test-the-app"></a>アプリを実行してテストする

1. アプリケーションを実行します。 すべてのアメニティがビューに表示されます。

2. 並べ替える場合、"By numerical Rating" (評価の数字順) を選択すると、このチュートリアルで既に実装した数値による並べ替えを実行できます。このとき、評価が同じホテルの表示順は改装日によって決まります。

![評価に基づいた "beach" の並べ替え](./media/tutorial-csharp-create-first-app/azure-search-orders-beach.png)

3. ここで "By amenities" (アメニティ順) プロファイルを試してみましょう。 さまざまなアメニティを選択し、結果リストで、それらのアメニティを備えたホテルの表示順が上がることを確認します。

![プロファイルに基づいた "beach" の並べ替え](./media/tutorial-csharp-create-first-app/azure-search-orders-beach-profile.png)

4. "By Renovated date/Rating profile" (改装日/評価順プロファイル) を試して、期待する結果が得られるかどうか確認します。 最近改装されたホテルのみ、_freshness_ (新しさ) が上昇するはずです。

### <a name="resources"></a>リソース

詳細については、次の「[スコアリング プロファイルを Azure Search のインデックスに追加する](https://docs.microsoft.com/azure/search/index-add-scoring-profiles)」を参照してください。

## <a name="takeaways"></a>重要なポイント

このプロジェクトの重要なポイントを示します。

* ユーザーが期待するのは、最も関連性の高いものが最初に来るように検索結果が並べ替えられることです。
* 並べ替えが簡単になるようにデータを構造化する必要があります。 追加コードなしで並べ替えを実行できるほどデータが構造化されていないため、"最も安い" ものを最初に表示する並べ替えが簡単にはできませんでした。
* より上のレベルでの並べ替えで値が同じである結果を差別化するために、並べ替えを複数のレベルにすることができます。
* 当然ながら、(ある地点からの距離のように) 昇順で並べ替える結果もあれば、(宿泊客の評価のように) 降順で並べ替える結果もあります。
* スコアリング プロファイルは、データ セットに対して数値での比較が利用できない、またはそれによって適切な結果が得られない場合に定義できます。 個々の結果にスコアを付けると、結果をインテリジェントに並べ替えて表示するために役立ちます。

## <a name="next-steps"></a>次の手順

この一連の C# チュートリアルを完了しました。Azure Search API に関する貴重な知識が得られたことでしょう。

さらなるリファレンスやチュートリアルについては、[Microsoft Learn](https://docs.microsoft.com/learn/browse/?products=azure) や、[Azure Search ドキュメント](https://docs.microsoft.com/azure/search/)のその他のチュートリアルをぜひ参考にしてください。
