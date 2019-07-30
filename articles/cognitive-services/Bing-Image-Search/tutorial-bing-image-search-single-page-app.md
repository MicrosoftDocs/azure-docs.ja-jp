---
title: チュートリアル:単一ページの Web アプリを作成する - Bing Image Search API
titleSuffix: Azure cognitive services
description: Bing Image Search API を使用すると、Web 上で高品質で関連性の高い画像を検索できます。 このチュートリアルを使用して、検索クエリを API に送信し、Web ページ内に結果を表示できる単一ページの Web アプリケーションを構築します。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-image-search
ms.topic: tutorial
ms.date: 07/12/2019
ms.author: aahi
ms.openlocfilehash: 7b530b3d415761956cbdb45fdc92bfed55a1bae5
ms.sourcegitcommit: 10251d2a134c37c00f0ec10e0da4a3dffa436fb3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/13/2019
ms.locfileid: "67868257"
---
# <a name="tutorial-create-a-single-page-app-using-the-bing-image-search-api"></a>チュートリアル:Bing Image Search API を使用して単一ページのアプリを作成する

Bing Image Search API を使用すると、Web 上で高品質で関連性の高い画像を検索できます。 このチュートリアルを使用して、検索クエリを API に送信し、Web ページ内に結果を表示できる単一ページの Web アプリケーションを構築します。 このチュートリアルは、Bing Web Search の[対応するチュートリアル](../Bing-Web-Search/tutorial-bing-web-search-single-page-app.md)と似ています。

このチュートリアル アプリは、次の方法を示しています。

> [!div class="checklist"]
> * Bing Image Search API 呼び出しを JavaScript で実行する
> * 検索オプションを使用して検索結果を改善する
> * 検索結果の表示とページング
> * API サブスクリプション キーと Bing クライアント ID を要求して処理します。

このチュートリアルの完全なソース コードは、[GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/tree/master/Tutorials/Bing-Image-Search) で入手できます。

## <a name="prerequisites"></a>前提条件

* 最新バージョンの [Node.js](https://nodejs.org/)。
* Node.js 用の [Express.js](https://expressjs.com/) フレームワーク。 ソース コードのインストール手順については、GitHub サンプルの readme ファイルを参照してください。

[!INCLUDE [cognitive-services-bing-image-search-signup-requirements](../../../includes/cognitive-services-bing-image-search-signup-requirements.md)]

## <a name="manage-and-store-user-subscription-keys"></a>ユーザー サブスクリプション キーの管理と格納

このアプリケーションは、Web ブラウザーの固定ストレージを使用して API サブスクリプション キーを格納します。 格納されているキーがない場合、この Web ページでは、後で使用できるようにキーの入力と格納をユーザーに求めます。 API によって後でキーが拒否された場合、そのキーはアプリでストレージから削除されます。


`localStorage` オブジェクト (ブラウザーでサポートされている場合) または Cookie のいずれかを使用する `storeValue` 関数と `retrieveValue` 関数を定義します。

```javascript
// Cookie names for data being stored
API_KEY_COOKIE   = "bing-search-api-key";
CLIENT_ID_COOKIE = "bing-search-client-id";
// The Bing Image Search API endpoint
BING_ENDPOINT = "https://api.cognitive.microsoft.com/bing/v7.0/images/search";

try { //Try to use localStorage first
    localStorage.getItem;   

    window.retrieveValue = function (name) {
        return localStorage.getItem(name) || "";
    }
    window.storeValue = function(name, value) {
        localStorage.setItem(name, value);
    }
} catch (e) {
    //If the browser doesn't support localStorage, try a cookie
    window.retrieveValue = function (name) {
        var cookies = document.cookie.split(";");
        for (var i = 0; i < cookies.length; i++) {
            var keyvalue = cookies[i].split("=");
            if (keyvalue[0].trim() === name) return keyvalue[1];
        }
        return "";
    }
    window.storeValue = function (name, value) {
        var expiry = new Date();
        expiry.setFullYear(expiry.getFullYear() + 1);
        document.cookie = name + "=" + value.trim() + "; expires=" + expiry.toUTCString();
    }
}
```

`getSubscriptionKey()` 関数は、`retrieveValue` を使用して以前に格納されたキーの取得を試みます。 見つからない場合は、ユーザーにキーの入力を促し、`storeValue` を使用してキーを格納します。

```javascript

// Get the stored API subscription key, or prompt if it's not found
function getSubscriptionKey() {
    var key = retrieveValue(API_KEY_COOKIE);
    while (key.length !== 32) {
        key = prompt("Enter Bing Search API subscription key:", "").trim();
    }
    // always set the cookie in order to update the expiration date
    storeValue(API_KEY_COOKIE, key);
    return key;
}
```

HTML の `<form>` タグの `onsubmit` は、`bingWebSearch` 関数を呼び出して検索結果を返します。 `bingWebSearch` は `getSubscriptionKey` を使用して各クエリを認証します。 前の定義のとおり、キーが入力されていない場合は、`getSubscriptionKey` はユーザーにキーの入力を求めます。 その後、アプリケーションで引き続き使用できるよう、キーが格納されます。

```html
<form name="bing" onsubmit="this.offset.value = 0; return bingWebSearch(this.query.value,
bingSearchOptions(this), getSubscriptionKey())">
```

## <a name="send-search-requests"></a>検索要求を送信する

このアプリケーションは、HTML `<form>` を使用し、`newBingImageSearch()` を呼び出す `onsubmit` 属性を使用して、まずユーザー検索要求を送信します。

```html
<form name="bing" onsubmit="return newBingImageSearch(this)">
```

既定で `onsubmit` ハンドラーは `false` を返します。フォームは送信されません。

## <a name="select-search-options"></a>検索オプションを選択する

![[Bing Image Search のフォーム]](media/cognitive-services-bing-images-api/image-search-spa-form.png)

Bing Image Search API には、検索結果を絞り込み、フィルター処理するための[フィルター クエリ パラメーター](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#filter-query-parameters)がいくつか用意されています。 このアプリケーションの HTML フォームは、次のパラメーター オプションを使用して表示します。

|              |                                                                                                                                                                                    |
|--------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `where`      | 検索で使用される市場 (場所と言語) を選択するドロップダウン メニュー。                                                                                             |
| `query`      | 検索語句を入力するテキスト フィールド。                                                                                                                                 |
| `aspect`     | 検索結果の画像の形を選択するラジオ ボタン。大まかに分類すると、正方形、ワイド、縦長を選択できます。                                                                                     |
| `color`      |                                                                                                                                                                                    |
| `when`       | オプションとして、最近の日、週、または月に検索を限定するためのドロップダウン メニュー。                                                                                          |
| `safe`       | "成人向け" の結果をフィルターで除外する Bing のセーフサーチ機能を使用するかどうかを指定するチェック ボックス。                                                                                      |
| `count`      | 隠しフィールド。 各要求に対して返される検索結果の数。 変更すると、1 ページあたりの結果の表示数が増減します。                                                            |
| `offset`     | 隠しフィールド。 要求における最初の検索結果のオフセット。ページングに使用されます。 新しい要求では `0` にリセットされます。                                                           |
| `nextoffset` | 隠しフィールド。 検索結果を受け取ると、このフィールドは応答内で `nextOffset` という値に設定されます。 このフィールドを使用すると、連続するページで結果が重複することを回避できます。 |
| `stack`      | 隠しフィールド。 JSON でエンコードされた、検索結果の前のページのオフセットの一覧。前のページに戻るために使用します。                                                      |

`bingSearchOptions()` 関数は、これらのオプションの書式を部分的なクエリ文字列へと設定します。これはアプリの API 要求で使用できます。  

```javascript
// Build query options from the HTML form
function bingSearchOptions(form) {

    var options = [];
    options.push("mkt=" + form.where.value);
    options.push("SafeSearch=" + (form.safe.checked ? "strict" : "off"));
    if (form.when.value.length) options.push("freshness=" + form.when.value);
    var aspect = "all";
    for (var i = 0; i < form.aspect.length; i++) {
        if (form.aspect[i].checked) {
            aspect = form.aspect[i].value;
            break;
        }
    }
    options.push("aspect=" + aspect);
    if (form.color.value) options.push("color=" + form.color.value);
    options.push("count=" + form.count.value);
    options.push("offset=" + form.offset.value);
    return options.join("&");
}
```

## <a name="performing-the-request"></a>要求の実行

検索クエリ、オプション文字列、および API キーを使用して、`BingImageSearch()` 関数は XMLHttpRequest オブジェクトを使用して Bing Image Search エンドポイントに対する要求を実行します。


```javascript
// perform a search given query, options string, and API key
function bingImageSearch(query, options, key) {

    // scroll to top of window
    window.scrollTo(0, 0);
    if (!query.trim().length) return false;     // empty query, do nothing

    showDiv("noresults", "Working. Please wait.");
    hideDivs("results", "related", "_json", "_http", "paging1", "paging2", "error");

    var request = new XMLHttpRequest();
    var queryurl = BING_ENDPOINT + "?q=" + encodeURIComponent(query) + "&" + options;

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

HTTP 要求が正常に完了すると、JavaScript は HTTP GET 要求を正常に処理するために、"読み込み" イベント ハンドラーの `handleBingResponse()` を呼び出します。

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
            if (jsobj._type === "Images") {
                if (jsobj.nextOffset) document.forms.bing.nextoffset.value = jsobj.nextOffset;
                renderSearchResults(jsobj);
            } else {
                renderErrorMessage("No search results in JSON response");
            }
        } else {
            renderErrorMessage("Empty response (are you sending too many requests too quickly?)");
        }
    }

    // Any other HTTP response is an error
    else {
        // 401 is unauthorized; force re-prompt for API key for next request
        if (this.status === 401) invalidateSubscriptionKey();

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
> 成功する HTTP 要求には、失敗した検索情報が含まれる可能性があります。 検索操作でエラーが発生した場合、Bing Image Search API は JSON 応答で 200 以外の HTTP 状態コードとエラー情報を返します。 また、要求のレートが制限されている場合、API は空の応答を返します。

## <a name="display-the-search-results"></a>検索結果を表示する

検索結果は `renderSearchResults()` 関数で表示されます。これは Bing Image Search サービスから返された JSON を受け取り、返された画像や関連する検索で適切なレンダラー関数を呼び出します。

```javascript
function renderSearchResults(results) {

    // add Prev / Next links with result count
    var pagingLinks = renderPagingLinks(results);
    showDiv("paging1", pagingLinks);
    showDiv("paging2", pagingLinks);

    showDiv("results", renderImageResults(results.value));
    if (results.relatedSearches)
        showDiv("sidebar", renderRelatedItems(results.relatedSearches));
}
```

画像の検索結果は、JSON 応答内の最上位の `value` オブジェクトに含まれています。 これらは `renderImageResults()` に渡され、そこで結果が反復処理され、各項目が HTML に変換されます。

```javascript
function renderImageResults(items) {
    var len = items.length;
    var html = [];
    if (!len) {
        showDiv("noresults", "No results.");
        hideDivs("paging1", "paging2");
        return "";
    }
    for (var i = 0; i < len; i++) {
        html.push(searchItemRenderers.images(items[i], i, len));
    }
    return html.join("\n\n");
}
```

Bing Image Search API から、ユーザーの検索エクスペリエンスを支援する 4 種類の検索候補を、最上位のオブジェクトでそれぞれを返すことができます。

| 推奨事項         | 説明                                                                                                                                                                                                         |
|--------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `pivotSuggestions` | 元の検索の軸語を別のものに置き換えるクエリです。 たとえば、"赤い花" を検索した場合、"赤" が軸語で、"黄色い花" が候補になる可能性があります。 |
| `queryExpansions`  | 複数の条件を追加することで、元の検索を絞り込むクエリです。 たとえば、"Microsoft Surface" を検索した場合、クエリが "Microsoft Surface Pro" に拡張される場合があります。                                   |
| `relatedSearches`  | 元の検索を入力した他のユーザーが他に入力したことのあるクエリです。 たとえば、"レーニア山" を検索した場合、関連する検索は "セント ヘレンズ山" となる場合があります。                       |
| `similarTerms`     | 元の検索と似た意味を持つクエリです。 たとえば、"子猫" を検索すると、似た用語として "かわいい" が挙げられる場合があります。                                                                   |

このアプリケーションでは `relatedItems` の提案のみがレンダリングされ、結果のリンクがページのサイドバーに配置されます。

## <a name="rendering-search-results"></a>検索結果のレンダリング

このアプリケーションで、`searchItemRenderers` オブジェクトは、検索結果の種類ごとに HTML を生成するレンダラー関数を含みます。

```javascript
searchItemRenderers = {
    images: function(item, index, count) { ... },
    relatedSearches: function(item) { ... }
}
```

これらのレンダラー関数では、次のパラメーターを受け取ることができます。

| パラメーター         | 説明                                                                                              |
|---------|----------------------------------------------------------------------------------------------|
| `item`  | 項目のプロパティ (URL とその説明など) を含む JavaScript オブジェクト。 |
| `index` | コレクション内の結果項目のインデックス。                                          |
| `count` | 検索結果項目のコレクション内の項目の数。                                  |

`index` および `count` パラメーターは、結果の番号付け、コレクション用の HTML の生成、およびコンテンツの整理に使用されます。 具体的には次のとおりです。

* 画像のサムネイルのサイズを計算します (幅は 120 ピクセル以上の可変ですが、高さは 90 ピクセルに固定されます)。
* 画像のサムネイルを表示する HTML `<img>` タグをビルドします。
* 画像およびその画像が含まれるページにリンクされる HTML `<a>` タグをビルドします。
* 画像およびその画像が存在するサイトに関する情報を示す説明をビルドします。

```javascript
    images: function (item, index, count) {
        var height = 120;
        var width = Math.max(Math.round(height * item.thumbnail.width / item.thumbnail.height), 120);
        var html = [];
        if (index === 0) html.push("<p class='images'>");
        var title = escape(item.name) + "\n" + getHost(item.hostPageDisplayUrl);
        html.push("<p class='images' style='max-width: " + width + "px'>");
        html.push("<img src='"+ item.thumbnailUrl + "&h=" + height + "&w=" + width +
            "' height=" + height + " width=" + width + "'>");
        html.push("<br>");
        html.push("<nobr><a href='" + item.contentUrl + "'>Image</a> - ");
        html.push("<a href='" + item.hostPageUrl + "'>Page</a></nobr><br>");
        html.push(title.replace("\n", " (").replace(/([a-z0-9])\.([a-z0-9])/g, "$1.<wbr>$2") + ")</p>");
        return html.join("");
    }, // relatedSearches renderer omitted
```

サムネイルの画像の `height` と `width` は、サムネイルの URL 内の `<img>` タグに加えて、`h` および `w` フィールドで使用されます。 こうすることで、Bing は正確にそのサイズの[サムネイル](../bing-web-search/resize-and-crop-thumbnails.md)を返すことができます。

## <a name="persisting-client-id"></a>クライアント ID の永続化

Bing Search API からの応答には、`X-MSEdge-ClientID` ヘッダーが含まれることがあり、このヘッダーは後続の要求で API に送り返す必要があります。 複数の Bing Search API を使用している場合、可能であれば、それらすべてで同じクライアント ID を使用する必要があります。

`X-MSEdge-ClientID` ヘッダーを提供すると、Bing API がユーザーのすべての検索を関連付けられるようになります。これは次の場合に便利です。

1 つ目は、Bing 検索エンジンが、検索に過去のコンテキストを適用できるようになることです。これにより、ユーザーがより満足できる結果を見つけることができます。 たとえば、ユーザーが過去に航海に関連する用語を検索したことがあった場合、その後の "ノット" という検索では、航海用語としてのノットに関する情報を優先的に返すことができます。

2 つ目は、Bing でユーザーをランダムに選択し、まだ広く利用されていない新機能を体験してもらえるようになることです。 各要求で同じクライアント ID を提供することで、機能の表示対象として選択されているユーザーには、常にそれが表示されるようになります。 クライアント ID を提供しないと、ユーザーには、検索結果内で機能が見かけ上ランダムに表示されたり消えたりする可能性があります。

ブラウザーのセキュリティ ポリシー (CORS) により、`X-MSEdge-ClientID` ヘッダーを JavaScript で使用できない場合もあります。 この制限は、検索応答のオリジンとその要求元のページとが異なる場合に生じます。 運用環境では、API 呼び出しを実行するサーバー側スクリプトを Web ページと同じドメインに ホストして、このポリシーに対応する必要があります。 スクリプトのオリジンが Web ページと同じになるので、`X-MSEdge-ClientID` ヘッダーを JavaScript で利用できます。

> [!NOTE]
> 運用環境の Web アプリケーションでは、いずれにしても要求サーバー側を実行する必要があります。 そうしないと、Web ページに Bing Search API キーを含める必要があり、ソースを表示した人ならだれでもキーを使用できるようになってしまいます。 権限のない者によって行われた要求であっても、すべての利用に対してユーザーの API サブスクリプション キーに基づいて料金が発生するため、キーは公開しないようにする必要があります。

開発目的の場合は、CORS プロキシを介して Bing Web Search API 要求を行うことができます。 そのようなプロキシからの応答には、応答ヘッダーを許可して JavaScript で使用できるようにする `Access-Control-Expose-Headers` ヘッダーが含まれています。

CORS プロキシをインストールして、チュートリアル アプリがクライアント ID ヘッダーにアクセスできるようにするのは簡単です。 まず、[Node.js をインストールします](https://nodejs.org/en/download/) (まだインストールしていない場合)。 さらに、コマンド ウィンドウで次のコマンドを発行します。

    npm install -g cors-proxy-server

次に、HTML ファイル内の Bing Web Search エンドポイントを次のように変更します。

    http://localhost:9090/https://api.cognitive.microsoft.com/bing/v7.0/search

最後に、次のコマンドを使用して、CORS プロキシを開始します。

    cors-proxy-server

チュートリアル アプリを使用している間はコマンド ウィンドウを開いたままにしておいてください。ウィンドウを閉じるとプロキシが停止します。 検索結果の下の展開可能な HTTP ヘッダー セクションに、`X-MSEdge-ClientID` ヘッダー (など) が表示され、各要求で同じであることを確認できます。

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [Bing Image Search API を使用して画像の詳細情報を抽出する](tutorial-image-post.md)

## <a name="see-also"></a>関連項目

* [Bing Image Search API リファレンス](//docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference)
