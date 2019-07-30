---
title: チュートリアル:Bing Entity Search シングルページ Web アプリ
titleSuffix: Azure Cognitive Services
description: 単一ページの Web アプリで Bing Entity Search API を使用する方法を説明します。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-entity-search
ms.topic: tutorial
ms.date: 07/15/2019
ms.author: aahi
ms.openlocfilehash: 5a8276f06207eb69ffec0e21c6d92794973f3b83
ms.sourcegitcommit: 198c3a585dd2d6f6809a1a25b9a732c0ad4a704f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/23/2019
ms.locfileid: "68423974"
---
# <a name="tutorial-single-page-web-app"></a>チュートリアル:単一ページの Web アプリ

Bing Entity Search API を使用すると、Web で*エンティティ*と*場所*に関する情報を検索できます。 特定のクエリで、いずれかの結果または両方の結果を要求することができます。 場所とエンティティの定義を以下に示します。

|||
|-|-|
|エンティティ|名前で検索する有名な人物、場所、もの|
|場所|名前*または*種類 (イタリアン レストランなど) で検索するレストラン、ホテル、その他の地元企業|

このチュートリアルでは、Bing Entity Search API を使用する単一ページの Web アプリケーションをビルドして、検索結果をページ内に表示できるようにします。 このアプリケーションには、HTML、CSS、JavaScript のコンポーネントが含まれます。

API を使用すると、場所によって結果を優先順位付けすることができます。 モバイル アプリでは、デバイスにその場所を要求することができます。 Web アプリでは、`getPosition()` 関数を使用することができます。 ただし、この呼び出しは、セキュリティで保護されたコンテキストでのみ動作し、正確な位置が提供されない場合があります。 また、ユーザーが自分の場所以外の場所の近くのエンティティを検索する場合があります。

そのため、このアプリでは、ユーザーが入力した場所から緯度と経度を取得することを Bing Maps サービスに求めます。 そして、ユーザーがランドマークの名前 ("スペース ニードル") や、完全な住所または部分的な住所 ("ニューヨーク市") を入力すると、Bing Maps API によって座標が提供されます。

<!-- Remove until we can replace with a sanitized version.
![[Single-page Bing Entity Search app]](media/entity-search-spa-demo.png)
-->

> [!NOTE]
> ページの下部にある JSON と HTTP のヘッダーをクリックすると、JSON 応答と HTTP 要求の情報が表示されます。 これらの詳細情報は、サービスの詳細を調べるのに役立ちます。

このチュートリアルのアプリは、次の方法を示しています。

> [!div class="checklist"]
> * Bing Entity Search API 呼び出しを JavaScript で実行する
> * Bing Maps `locationQuery` API 呼び出しを JavaScript で実行する
> * 検索オプションを API 呼び出しに渡す
> * 検索結果を表示する
> * Bing のクライアント ID と API のサブスクリプション キーを処理する
> * 発生する可能性のあるエラーを処理する

このチュートリアルのページでは、すべてが自己完結しています。外部のフレームワーク、スタイル シートはもちろん、画像ファイルすら使用することはありません。 広くサポートされている JavaScript 言語機能のみを使用し、現在のバージョンの主要な Web ブラウザーすべてで動作します。

このチュートリアルでは、ソース コードの一部についてのみ説明します。 完全なソース コードは[別のページ](tutorial-bing-entities-search-single-page-app-source.md)から入手できます。 このコードをコピーしてテキスト エディターに貼り付け、`bing.html` として保存します。

> [!NOTE]
> このチュートリアルは、[単一ページの Bing Web Search アプリのチュートリアル](../Bing-Web-Search/tutorial-bing-web-search-single-page-app.md)と実質的に同じものですが、エンティティの検索結果についてのみ説明しています。

## <a name="app-components"></a>アプリのコンポーネント

他のシングルページ Web アプリと同様に、このチュートリアル用アプリケーションには 3 つのパーツが含まれています。

> [!div class="checklist"]
> * HTML - ページの構造と内容を定義します
> * CSS - ページの外観を定義します
> * JavaScript - ページの動作を定義します

このチュートリアルでは、HTML と CSS の大部分について詳しく取り上げません。HTML と CSS はわかりやすいためです。

HTML には検索フォームが含まれ、ユーザーはこれにクエリを入力して検索オプションを選択します。 フォームは JavaScript に接続されます。JavaScript は、`<form>` タグの `onsubmit` 属性を使用して、実際に検索を実行します。

```html
<form name="bing" onsubmit="return newBingEntitySearch(this)">
```

`onsubmit` ハンドラーは `false` を返すことで、フォームがサーバーに送信されないようにします。 JavaScript コードは、フォームから重要な情報を実際に収集し、検索を実行します。

検索は、2 つのフェーズで行われます。 最初に、ユーザーが場所の制限を入力すると、それを座標に変換するために Bing Maps クエリが実行されます。 その後、このクエリのコールバックにより Bing Entity Search クエリが開始されます。

HTML には、検索結果が表示される区分 (HTML `<div>` タグ) も含まれます。

## <a name="managing-subscription-keys"></a>サブスクリプション キーの管理

> [!NOTE]
> このアプリでは、Bing Search API と Bing Maps API の両方のサブスクリプション キーが必要です。 [試用版の Bing Search キー](https://azure.microsoft.com/try/cognitive-services/?api=bing-web-search-api)と[基本の Bing Maps キー](https://www.microsoft.com/maps/create-a-bing-maps-key)を使用できます。

コードに Bing Search と Bing Maps API のサブスクリプション キーを含めずに済むように、ブラウザーの永続的ストレージを使用してキーを格納します。 いずれのキーも保存されていない場合は、保存するように促し、後で使用するために保存します。 後でキーが API によって拒否された場合、格納されたキーは無効になり、ユーザーは次回の検索時にキーを求められます。

`localStorage` オブジェクト (ブラウザーでサポートされている場合) または Cookie のいずれかを使用する `storeValue` 関数と `retrieveValue` 関数を定義します。 `getSubscriptionKey()` 関数は、これらの関数を使用してユーザーのキーを格納、取得します。

```javascript
// cookie names for data we store
SEARCH_API_KEY_COOKIE = "bing-search-api-key";
MAPS_API_KEY_COOKIE   = "bing-maps-api-key";
CLIENT_ID_COOKIE      = "bing-search-client-id";

// API endpoints
SEARCH_ENDPOINT = "https://api.cognitive.microsoft.com/bing/v7.0/entities";
MAPS_ENDPOINT   = "https://dev.virtualearth.net/REST/v1/Locations";

// ... omitted definitions of storeValue() and retrieveValue()

// get stored API subscription key, or prompt if it's not found
function getSubscriptionKey(cookie_name, key_length, api_name) {
    var key = retrieveValue(cookie_name);
    while (key.length !== key_length) {
        key = prompt("Enter " + api_name + " API subscription key:", "").trim();
    }
    // always set the cookie in order to update the expiration date
    storeValue(cookie_name, key);
    return key;
}

function getMapsSubscriptionKey() {
    return getSubscriptionKey(MAPS_API_KEY_COOKIE, 64, "Bing Maps");
}

function getSearchSubscriptionKey() {
    return getSubscriptionKey(SEARCH_API_KEY_COOKIE, 32, "Bing Search");
}
```

HTML `<body>` タグには、ページの読み込みが完了したときに `getSearchSubscriptionKey()` と `getMapsSubscriptionKey()` を呼び出す `onload` 属性が含まれます。 これらの呼び出しは、ユーザーがキーをまだ入力していない場合に、キーの使用をすぐに要求するために機能します。

```html
<body onload="document.forms.bing.query.focus(); getSearchSubscriptionKey(); getMapsSubscriptionKey();">
```

## <a name="selecting-search-options"></a>検索オプションの選択

![[Bing Entity Search フォーム]](media/entity-search-spa-form.png)

HTML のフォームには、次のコントロールが含まれます。

| | |
|-|-|
|`where`|検索で使用される市場 (場所と言語) を選択するドロップダウン メニュー。|
|`query`|検索語句を入力するテキスト フィールド。|
|`safe`|セーフサーチが有効になっているかどうか示すチェック ボックス ("アダルト" の結果を制限する)|
|`what`|エンティティ、場所、またはその両方を検索することを選択するためのメニュー。|
|`mapquery`|Bing Entity Search でより関連性の高い結果を返せるように、ユーザーが完全な住所または部分的な住所、ランドマークなどを入力できるテキスト フィールド。|

> [!NOTE]
> 場所の結果は、現在、米国でのみ使用できます。 `where` メニューと `what` メニューには、この制限を適用するコードがあります。 `what` メニューで Places が選択されているときに米国以外の市場を選択すると、`what` は Anything に変わります。 `where` メニューで米国以外の市場が選択されているときに Places を選択すると、`where` は US に変わります。

JavaScript 関数 `bingSearchOptions()` は、これらのフィールドを Bing Search API の部分的なクエリ文字列に変換します。

```javascript
// build query options from the HTML form
function bingSearchOptions(form) {

    var options = [];
    options.push("mkt=" + form.where.value);
    options.push("SafeSearch=" + (form.safe.checked ? "strict" : "off"));
    if (form.what.selectedIndex) options.push("responseFilter=" + form.what.value);
    return options.join("&");
}
```

たとえば、`moderate` が既定になっていると、セーフサーチ機能は、`strict`、`moderate`、`off` のいずれかにできます。 ただし、このフォームでは、2 つの状態しかないチェック ボックスを使用します。 JavaScript コードにより、この設定が `strict` または `off` のいずれかに変換されます (`moderate` は使用しません)。

`mapquery` フィールドは、Bing Entity Search ではなく、Bing Maps の位置クエリで使用されるため、`bingSearchOptions()` では処理されません。

## <a name="obtaining-a-location"></a>場所の取得

Bing Maps API では、[ `locationQuery`メソッド](//msdn.microsoft.com/library/ff701711.aspx)を提供しています。これを使用して、ユーザーが入力した位置の緯度と経度を検索します。 これらの座標は、ユーザーの要求と共に Bing Entity Search API に渡されます。 検索結果では、指定した位置情報に近い順にエンティティと場所が優先度付けされます。

Web アプリでは、通常の `XMLHttpRequest` クエリを使用して Bing Maps API にアクセスできません。これはサービスでクロス オリジン クエリをサポートしていないからです。 ただし、JSONP ("P" は "padded" (埋め込み)) はサポートされています。 JSONP 応答は、関数呼び出しでラップされる通常の JSON 応答です。 要求は `<script>` タグを使用してドキュメントに挿入することで作成されます (スクリプトの読み込みは、ブラウザーのセキュリティ ポリシーの対象外です)。

`bingMapsLocate()` 関数は、クエリの `<script>` タグを作成および挿入します。 クエリ文字列の `jsonp=bingMapsCallback` セグメントは、応答で呼び出される関数の名前を指定します。

```javascript
function bingMapsLocate(where) {

    where = where.trim();
    var url = MAPS_ENDPOINT + "?q=" + encodeURIComponent(where) + 
                "&jsonp=bingMapsCallback&maxResults=1&key=" + getMapsSubscriptionKey();

    var script = document.getElementById("bingMapsResult")
    if (script) script.parentElement.removeChild(script);

    // global variable holds reference to timer that will complete the search if the maps query fails
    timer = setTimeout(function() {
        timer = null;
        var form = document.forms.bing;
        bingEntitySearch(form.query.value, "", bingSearchOptions(form), getSearchSubscriptionKey());
    }, 5000);

    script = document.createElement("script");
    script.setAttribute("type", "text/javascript");
    script.setAttribute("id", "bingMapsResult");
    script.setAttribute("src", url);
    script.setAttribute("onerror", "BingMapsCallback(null)");
    document.body.appendChild(script);

    return false;
}
```

> [!NOTE]
> Bing Maps API が応答しない場合、`bingMapsCallBack()` 関数は呼び出されません。 通常、これは `bingEntitySearch()` が呼び出されず、エンティティの検索結果が表示されないことを意味します。 このシナリオを回避するため、`bingMapsLocate()` はタイマーを設定して 5 秒後に `bingEntitySearch()` を呼び出します。 コールバック関数には、エンティティの検索が 2 回実行されるのを回避するためのロジックがあります。

クエリが完了すると、要求に従って `bingMapsCallback()` 関数が呼び出されます。

```javascript
function bingMapsCallback(response) {

    if (timer) {    // we beat the timer; stop it from firing
        clearTimeout(timer);
        timer = null;
    } else {        // the timer beat us; don't do anything
        return; 
    }

    var location = "";
    var name = "";
    var radius = 1000;

    if (response) {
        try {
            if (response.statusCode === 401) {
                invalidateMapsKey();
            } else if (response.statusCode === 200) {
                var resource = response.resourceSets[0].resources[0];
                var coords   = resource.point.coordinates;
                name         = resource.name;

                // the radius is the largest of the distances between the location and the corners
                // of its bounding box (in case it's not in the center) with a minimum of 1 km
                try {
                    var bbox    = resource.bbox;
                    radius  = Math.max(haversineDistance(bbox[0], bbox[1], coords[0], coords[1]),
                                       haversineDistance(coords[0], coords[1], bbox[2], bbox[1]),
                                       haversineDistance(bbox[0], bbox[3], coords[0], coords[1]),
                                       haversineDistance(coords[0], coords[1], bbox[2], bbox[3]), 1000);
                } catch(e) {  }
                var location = "lat:" + coords[0] + ";long:" + coords[1] + ";re:" + Math.round(radius);
            }
        }
        catch (e) { }   // response is unexpected. this isn't fatal, so just don't provide location
    }

    var form = document.forms.bing;
    if (name) form.mapquery.value = name;
    bingEntitySearch(form.query.value, location, bingSearchOptions(form), getSearchSubscriptionKey());

}
```

Bing Entity Search クエリでは、緯度と経度の他に、位置情報の精度を示す*半径*が必要です。 Bing Maps 応答で提供される*境界ボックス*を使用して、半径を計算します。 境界ボックスは、位置全体を囲む四角形です。 たとえば、ユーザーが `NYC` と入力した場合、結果にはニューヨーク市のおよその中心の座標と、市を取り囲む境界ボックスが含まれます。 

最初に、関数 `haversineDistance()` (表示されていません) を使用して、プライマリ座標から境界ボックスの 4 つのコーナーまでのそれぞれの距離を計算します。 これら 4 つの距離の最大値を半径として使用します。 最小半径は 1 キロメートルです。 応答で境界ボックスが提供されていない場合、この値は既定値としても使用されます。

座標と半径を取得したら、次に `bingEntitySearch()` を呼び出して実際の検索を実行します。

## <a name="performing-the-search"></a>検索の実行

クエリ、位置情報、オプションの文字列、API キーを指定すると、`BingEntitySearch()` 関数により Bing Entity Search 要求が作成されます。

```javascript
// perform a search given query, location, options string, and API keys
function bingEntitySearch(query, latlong, options, key) {

    // scroll to top of window
    window.scrollTo(0, 0);
    if (!query.trim().length) return false;     // empty query, do nothing

    showDiv("noresults", "Working. Please wait.");
    hideDivs("pole", "mainline", "sidebar", "_json", "_http", "error");

    var request = new XMLHttpRequest();
    var queryurl = SEARCH_ENDPOINT + "?q=" + encodeURIComponent(query) + "&" + options;

    // open the request
    try {
        request.open("GET", queryurl);
    } 
    catch (e) {
        renderErrorMessage("Bad request (invalid URL)\n" + queryurl);
        return false;
    }

    // add request headers
    request.setRequestHeader("Ocp-Apim-Subscription-Key", key);
    request.setRequestHeader("Accept", "application/json");

    var clientid = retrieveValue(CLIENT_ID_COOKIE);
    if (clientid) request.setRequestHeader("X-MSEdge-ClientID", clientid);

    if (latlong) request.setRequestHeader("X-Search-Location", latlong);

    // event handler for successful response
    request.addEventListener("load", handleBingResponse);
    
    // event handler for erorrs
    request.addEventListener("error", function() {
        renderErrorMessage("Error completing request");
    });

    // event handler for aborted request
    request.addEventListener("abort", function() {
        renderErrorMessage("Request aborted");
    });

    // send the request
    request.send();
    return false;
}
```

HTTP 要求が正常に完了すると、JavaScript は API への HTTP GET 要求を正常に処理するために、`load` イベント ハンドラーと `handleBingResponse()` 関数を呼び出します。 

```javascript
// handle Bing search request results
function handleBingResponse() {
    hideDivs("noresults");

    var json = this.responseText.trim();
    var jsobj = {};

    // try to parse JSON results
    try {
        if (json.length) jsobj = JSON.parse(json);
    } catch(e) {
        renderErrorMessage("Invalid JSON response");
    }

    // show raw JSON and HTTP request
    showDiv("json", preFormat(JSON.stringify(jsobj, null, 2)));
    showDiv("http", preFormat("GET " + this.responseURL + "\n\nStatus: " + this.status + " " + 
        this.statusText + "\n" + this.getAllResponseHeaders()));

    // if HTTP response is 200 OK, try to render search results
    if (this.status === 200) {
        var clientid = this.getResponseHeader("X-MSEdge-ClientID");
        if (clientid) retrieveValue(CLIENT_ID_COOKIE, clientid);
        if (json.length) {
            if (jsobj._type === "SearchResponse") {
                renderSearchResults(jsobj);
            } else {
                renderErrorMessage("No search results in JSON response");
            }
        } else {
            renderErrorMessage("Empty response (are you sending too many requests too quickly?)");
        }
    if (divHidden("pole") && divHidden("mainline") && divHidden("sidebar")) 
        showDiv("noresults", "No results.<p><small>Looking for restaurants or other local businesses? Those currently areen't supported outside the US.</small>");
    }

    // Any other HTTP status is an error
    else {
        // 401 is unauthorized; force re-prompt for API key for next request
        if (this.status === 401) invalidateSearchKey();

        // some error responses don't have a top-level errors object, so gin one up
        var errors = jsobj.errors || [jsobj];
        var errmsg = [];

        // display HTTP status code
        errmsg.push("HTTP Status " + this.status + " " + this.statusText + "\n");

        // add all fields from all error responses
        for (var i = 0; i < errors.length; i++) {
            if (i) errmsg.push("\n");
            for (var k in errors[i]) errmsg.push(k + ": " + errors[i][k]);
        }

        // also display Bing Trace ID if it isn't blocked by CORS
        var traceid = this.getResponseHeader("BingAPIs-TraceId");
        if (traceid) errmsg.push("\nTrace ID " + traceid);

        // and display the error message
        renderErrorMessage(errmsg.join("\n"));
    }
}
```

> [!IMPORTANT]
> HTTP 要求が成功しても、必ずしも検索が成功したとは*限りません*。 検索操作でエラーが発生した場合、Bing Entity Search API は 200 以外の HTTP 状態コードを返し、JSON 応答内にエラー情報を含めます。 また、要求のレートが制限されている場合は、API は空の応答を返します。

先ほどの両方の関数内にあるコードの多くは、エラー処理専用のものです。 エラーは次の段階で発生する場合があります。

|段階|起こり得るエラー|処理元|
|-|-|-|
|JavaScript 要求オブジェクトのビルド|無効な URL|`try`/`catch` ブロック|
|要求の実行|ネットワーク エラー、接続の中止|`error` および `abort` イベント ハンドラー|
|検索の実行|無効な要求、無効な JSON、レート制限|`load` イベント ハンドラー内のテスト|

エラーは、`renderErrorMessage()` とエラーに関する既知の情報を呼び出すことで処理されます。 応答がエラー テストにすべてパスしたら、`renderSearchResults()` を呼び出して、ページ内に検索結果を表示します。

## <a name="displaying-search-results"></a>検索結果の表示

Bing Entity Search API では、[指定した順序で結果を表示する必要があります](use-display-requirements.md)。 API は 2 つの異なる種類の応答を返す可能性があるため、JSON 応答内の最上位の `Entities` または `Places` コレクションを反復処理してそれらの結果を表示するだけでは不十分です (1 種類の結果だけが必要な場合は、`responseFilter` クエリ パラメーターを使用します)。

代わりに、検索結果で `rankingResponse` コレクションを使用して、表示の結果を順序付けします。 このオブジェクトは、`Entitiess` と `Places` の両方またはいずれかのコレクション内の項目を参照します。

`rankingResponse` には、検索結果の最大 3 つのコレクション (指定された `pole`、`mainline`、`sidebar`) を含めることができます。 

`pole` (存在する場合) は最も関連性の高い検索結果であり、目立つように表示する必要があります。 `mainline` は検索結果の大部分を参照します。 メインラインの結果は、`pole` の直後に表示される必要があります (`pole` が存在しない場合は、最初に表示されます)。 

最後に、 `sidebar` は、補助検索結果を参照します。 これらは、実際のサイド バーに表示するか、または単にメインラインの結果の後に表示することができます。 このチュートリアル アプリでは、後者を選択しました。

`rankingResponse` コレクション内の各項目は、2 つの異なる (ただし同等の) 方法で実際の検索結果の項目を参照します。

| | |
|-|-|
|`id`|`id` は URL に似ていますが、リンクには使用できません。 `id` 型のランキング結果は、応答コレクション内の検索結果項目、"*または*" 応答コレクション全体 (`Entities` など) のいずれかの `id` と一致します。
|`answerType`<br>`resultIndex`|`answerType` は、結果を含む最上位の応答コレクション (`Entities` など) を参照します。 `resultIndex` は、そのコレクション内の結果のインデックスを参照します。 `resultIndex` を省略すると、ランキング結果はコレクション全体を参照したものとなります。

> [!NOTE]
> 検索応答のこの部分の詳細については、[ランキング結果](rank-results.md)に関するページを参照してください。

どちらの方法も、アプリケーションで参照先の検索結果項目を特定するうえで非常に便利です。 このチュートリアルのコードでは、`answerType` と `resultIndex` を使用して各検索結果を特定します。

最後に、`renderSearchResults()` 関数について説明します。 この関数は、検索結果の 3 つのセクションを表す 3 つの `rankingResponse` コレクションを反復処理します。 セクションごとに `renderResultsItems()` を呼び出して、そのセクションの結果をレンダリングします。

```javascript
// render the search results given the parsed JSON response
function renderSearchResults(results) {

    // if spelling was corrected, update search field
    if (results.queryContext.alteredQuery) 
        document.forms.bing.query.value = results.queryContext.alteredQuery;

    // for each possible section, render the results from that section
    for (section in {pole: 0, mainline: 0, sidebar: 0}) {
        if (results.rankingResponse[section])
            showDiv(section, renderResultsItems(section, results));
    }
}
```

## <a name="rendering-result-items"></a>結果項目のレンダリング

JavaScript コードにはオブジェクト `searchItemRenderers` が存在します。これには *renderers:* 関数が含まれ、各種類の検索結果ごとに HTML を生成します。

```javascript
searchItemRenderers = { 
    entities: function(item) { ... },
    places: function(item) { ... }
}
```

レンダラー関数では、次のパラメーターを受け取ることができます。

| | |
|-|-|
|`item`|項目のプロパティ (URL とその説明など) を含む JavaScript オブジェクト。|
|`index`|コレクション内の結果項目のインデックス。|
|`count`|検索結果項目のコレクション内の項目の数。|

`index` パラメーターと `count` パラメーターは、結果のカウント、コレクションの先頭または末尾の特別な HTML の生成、特定の数の項目の後への改行の挿入などに使用できます。 レンダラーにこの機能が必要がない場合は、これら 2 つのパラメーターを受け入れる必要はありません。 実際、このチュートリアル アプリのレンダラーには、これらは使用しません。

`entities` レンダラーについて詳しく見ていきます。

```javascript
    entities: function(item) {
        var html = [];
        html.push("<p class='entity'>");
        if (item.image) {
            var img = item.image;
            if (img.hostPageUrl) html.push("<a href='" + img.hostPageUrl + "'>");
            html.push("<img src='" + img.thumbnailUrl +  "' title='" + img.name + "' height=" + img.height + " width= " + img.width + ">");
            if (img.hostPageUrl) html.push("</a>");
            if (img.provider) {
                var provider = img.provider[0];
                html.push("<small>Image from ");
                if (provider.url) html.push("<a href='" + provider.url + "'>");
                html.push(provider.name ? provider.name : getHost(provider.url));
                if (provider.url) html.push("</a>");
                html.push("</small>");
            }
        }
        html.push("<p>");
        if (item.entityPresentationInfo) {
            var pi = item.entityPresentationInfo;
            if (pi.entityTypeHints || pi.entityTypeDisplayHint) {
                html.push("<i>");
                if (pi.entityTypeDisplayHint) html.push(pi.entityTypeDisplayHint);
                else if (pi.entityTypeHints) html.push(pi.entityTypeHints.join("/"));
                html.push("</i> - ");
            }
        }
        html.push(item.description);
        if (item.webSearchUrl) html.push("&nbsp;<a href='" + item.webSearchUrl + "'>More</a>")
        if (item.contractualRules) {
            html.push("<p><small>");
            var rules = [];
            for (var i = 0; i < item.contractualRules.length; i++) {
                var rule = item.contractualRules[i];
                var link = [];
                if (rule.license) rule = rule.license;
                if (rule.url) link.push("<a href='" + rule.url + "'>");
                link.push(rule.name || rule.text || rule.targetPropertyName + " source");
                if (rule.url) link.push("</a>");
                rules.push(link.join(""));
            }
            html.push("License: " + rules.join(" - "));
            html.push("</small>");
        }
        return html.join("");
    }, // places renderer omitted
```

エンティティ レンダラーの機能は次のとおりです。

> [!div class="checklist"]
> * HTML `<img>` タグをビルドして画像のサムネイルを表示する (ある場合)。 
> * イメージを含むページにリンクする HTML `<a>` タグをビルドする。
> * イメージとそれが存在するサイトに関する情報を表示する説明をビルドする。
> * 表示ヒント (ある場合) を使用して、エンティティの分類を組み込む。
> * エンティティに関する追加情報を取得するため、Bing 検索へのリンクを含める。
> * データ ソースに必要なライセンスまたは属性の情報を表示する。

## <a name="persisting-client-id"></a>クライアント ID の永続化

Bing Search API からの応答には、`X-MSEdge-ClientID` ヘッダーが含まれることがあり、このヘッダーは後続の要求で API に送り返す必要があります。 複数の Bing Search API を使用している場合、可能であれば、それらすべてで同じクライアント ID を使用する必要があります。

`X-MSEdge-ClientID` ヘッダーを提供すると、Bing API がユーザーのすべての検索を関連付けられるようになります。これには、2 つの重要な利点があります。

1 つ目は、Bing 検索エンジンが、検索時に過去のコンテキストを適用できるようになることです。これにより、ユーザーがより満足できる結果を見つけることができます。 たとえば、ユーザーが航海に関連する用語を検索したことがあると、その後の "ドック" という検索では、ヨットをドッキングする場所に関する情報が優先的に返される可能性があります。

2 つ目は、まだ広く利用されていない新機能を Bing でランダムに選択し、体験用としてユーザーに提供できるようになります。 各要求で同じクライアント ID を提供することで、機能の表示対象として選択されているユーザーには、常にそれが表示されるようになります。 クライアント ID を提供しないと、ユーザーには、検索結果内で機能が見かけ上ランダムに表示されたり消えたりする可能性があります。

ブラウザーのセキュリティ ポリシー (CORS) により、`X-MSEdge-ClientID` ヘッダーを JavaScript で使用できない場合もあります。 この制限は、検索応答のオリジンとその要求元のページとが異なる場合に生じます。 運用環境では、API 呼び出しを実行するサーバー側スクリプトを Web ページと同じドメインに ホストして、このポリシーに対応する必要があります。 スクリプトのオリジンが Web ページと同じになるので、`X-MSEdge-ClientID` ヘッダーを JavaScript で利用できます。

> [!NOTE]
> 運用環境の Web アプリケーションでは、いずれにしても要求サーバー側を実行する必要があります。 そうしないと、Web ページに Bing Search API キーを含める必要があり、ソースを表示した人ならだれでもキーを使用できるようになってしまいます。 権限のない者によって行われた要求であっても、すべての利用に対してユーザーの API サブスクリプション キーに基づいて料金が発生するため、キーは公開しないようにする必要があります。

開発目的の場合は、CORS プロキシを介して Bing Web Search API 要求を行うことができます。 このようなプロキシからの応答には、応答ヘッダーをホワイトリストに登録して JavaScript で使用できるようにする `Access-Control-Expose-Headers` ヘッダーが含まれています。

CORS プロキシをインストールして、チュートリアル アプリがクライアント ID ヘッダーにアクセスできるようにするのは簡単です。 まず、[Node.js をインストールします](https://nodejs.org/en/download/) (まだインストールしていない場合)。 さらに、コマンド ウィンドウで次のコマンドを発行します。

    npm install -g cors-proxy-server

次に、HTML ファイル内の Bing Web Search エンドポイントを次のように変更します。

    http://localhost:9090/https://api.cognitive.microsoft.com/bing/v7.0/search

最後に、次のコマンドを使用して、CORS プロキシを開始します。

    cors-proxy-server

チュートリアル アプリを使用している間はコマンド ウィンドウを開いたままにしておいてください。ウィンドウを閉じるとプロキシが停止します。 検索結果の下の展開可能な HTTP ヘッダー セクションに、`X-MSEdge-ClientID` ヘッダー (など) が表示され、各要求で同じであることを確認できます。

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [Bing Entity Search API リファレンス](//docs.microsoft.com/rest/api/cognitiveservices/bing-entities-api-v7-reference)

> [!div class="nextstepaction"]
> [Bing Maps API ドキュメント](//msdn.microsoft.com/library/dd877180.aspx)
