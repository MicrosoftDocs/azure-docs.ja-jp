---
title: チュートリアル:シングルページ Bing Video Search アプリを構築する
titleSuffix: Azure Cognitive Services
description: シングルページ Web アプリケーションで Bing Video Search API を使用する方法を説明します。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-video-search
ms.topic: tutorial
ms.date: 07/12/2019
ms.author: aahi
ms.openlocfilehash: d2cd3d37801fc1a42a9bcbd5f70a6a55e78aaf08
ms.sourcegitcommit: a0b37e18b8823025e64427c26fae9fb7a3fe355a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/25/2019
ms.locfileid: "68500075"
---
# <a name="tutorial-single-page-video-search-app"></a>チュートリアル:シングルページ Video Search アプリ
Bing Video Search API を使うと、Web を検索して、検索クエリに関連するビデオの結果を取得できます。 このチュートリアルでは、Bing Search API を使用して検索結果をページに表示する単一ページの Web アプリケーションを構築します。 このアプリケーションには、HTML、CSS、JavaScript のコンポーネントが含まれます。

<!-- Remove until it can be replaced with a sanitized version.
![Single-page Bing Video Search app](./media/video-search-singlepage.png)
-->

> [!NOTE]
> ページの下部にある JSON と HTTP の見出しをクリックすると、JSON 応答と HTTP 要求の情報が表示されます。 これらの詳細情報は、サービスの詳細を調べるのに役立ちます。

![JSON、HTTP の手を加えていない結果](./media/json-http-raw-results.png)

このチュートリアル アプリは、次の方法を示します。
> [!div class="checklist"]
> * Bing Video Search API の呼び出しを JavaScript で実行する
> * Bing Search API に検索オプションを渡す
> * ビデオの検索結果を表示するか、または必要に応じて Web ページ、ニュース、画像を含める
> * 24 時間、過去 1 週間、過去 1 か月、または使用可能な全期間の時間範囲で検索する
> * 検索結果をページングする
> * Bing のクライアント ID と API サブスクリプション キーを処理する
> * 発生する可能性のあるエラーを処理する

このチュートリアルのページでは、すべてが自己完結しています。外部のフレームワーク、スタイル シート、画像ファイルを使用することはありません。 広くサポートされている JavaScript 言語機能のみを使用し、現在のバージョンの主要な Web ブラウザーすべてで動作します。

このチュートリアルでは、ソース コードの一部を選択して説明します。 完全な[ソース コード](tutorial-bing-video-search-single-page-app-source.md)を入手できます。 例を実行するには、ソース コードをコピーしてテキスト エディターに貼り付け、`bing.html` として保存します。

## <a name="app-components"></a>アプリのコンポーネント
他の単一ページ Web アプリと同様に、このチュートリアルのアプリケーションには 3 つのパーツが含まれています。

> [!div class="checklist"]
> * HTML - ページの構造と内容を定義します
> * CSS - ページの外観を定義します
> * JavaScript - ページの動作を定義します

ほとんどの HTML と CSS は決まりきったものなので、このチュートリアルでは説明しません。 HTML には検索フォームが含まれ、ユーザーはこれにクエリを入力して検索オプションを選択します。 フォームは、`<form>` タグの `onsubmit` 属性を使って検索を行う JavaScript に接続されています。

```html
<form name="bing" onsubmit="return bingWebSearch(this)">
```
`onsubmit` ハンドラーは `false` を返すことで、フォームがサーバーに送信されないようにします。 JavaScript コードはフォームから必要な情報を収集し、検索を実行する処理を行います。

HTML には、検索結果が表示されるブロック (HTML `<div>` タグ) も含まれます。

## <a name="managing-subscription-key"></a>サブスクリプション キーの管理

コードに Bing Search API サブスクリプション キーを含めなくてもよいように、ブラウザーの永続的ストレージを使用してキーを格納します。 キーを格納する前に、ユーザーにキーの入力を求めます。 後でキーが API によって拒否された場合は、格納されたキーを無効にし、ユーザーに再度操作を求めます。

`localStorage` オブジェクト (サポートしていないブラウザーもあります) または Cookie のいずれかを使用する `storeValue` 関数と `retrieveValue` 関数を定義します。 `getSubscriptionKey()` 関数は、これらの関数を使用してユーザーのキーを格納、取得します。

``` javascript
// Cookie names for data we store
API_KEY_COOKIE   = "bing-search-api-key";
CLIENT_ID_COOKIE = "bing-search-client-id";

// ... omitted definitions of store value and retrieve value
// Browsers differ in their support for persistent storage by 
// local HTML files. See the source code for browser-specific
// options.

// Get stored API subscription key, or prompt if it's not found.
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
HTML の `<form>` タグの `onsubmit` は、`bingWebSearch` 関数を呼び出して検索結果を返します。 `bingWebSearch` は `getSubscriptionKey()` を使用して各クエリを認証します。 前の定義のとおり、キーが入力されていない場合は、`getSubscriptionKey` はユーザーにキーの入力を求めます。 その後、アプリケーションで引き続き使用できるよう、キーが格納されます。

```html
<form name="bing" onsubmit="this.offset.value = 0; return bingWebSearch(this.query.value, 
    bingSearchOptions(this), getSubscriptionKey())">
```
## <a name="selecting-search-options"></a>検索オプションの選択
次の図では、クエリのテキスト ボックスと、検索条件を定義するオプションを示します。

![Bing News Search のオプション](media/video-search-options.png)

この HTML のフォームには、次の名前の付いた要素が含まれます。

|要素|説明|
|-|-|
| `where` | 検索で使用される市場 (場所と言語) を選択するドロップダウン メニュー。 |
| `query` | 検索語句を入力するテキスト フィールド。 |
| `modules` | 結果の特定のモジュール、すべての結果、または関連するビデオをプロモーションするためのチェック ボックス。 |
| `when` | オプションとして、最近の日、週、または月に検索を限定するためのドロップダウン メニュー。 |
| `safe` | "成人向け" の結果をフィルターで除外する Bing のセーフサーチ機能を使用するかどうかを指定するチェック ボックス。 |
| `count` | 隠しフィールド。 各要求に対して返される検索結果の数。 変更すると、1 ページあたりの結果の表示数が増減します。 |
| `offset`|  隠しフィールド。 要求における最初の検索結果のオフセット。ページングに使用されます。 新しい要求では `0` にリセットされます。 |

> [!NOTE]
> Bing Web Search には、他にもクエリ パラメーターがあります。 ここではその一部のみを使用しています。

``` javascript
// build query options from the HTML form
// build query options from the HTML form
function bingSearchOptions(form) {

    var options = [];
    options.push("mkt=" + form.where.value);
    options.push("SafeSearch=" + (form.safe.checked ? "strict" : "off"));

    if (form.when.value.length) options.push("freshness=" + form.when.value);
    var what = [];
    for (var i = 0; i < form.what.length; i++) 
        if (form.what[i].checked) what.push(form.what[i].value);
    if (what.length) {
        options.push("modules=" + what.join(","));
        options.push("answerCount=9");
    }
    options.push("count=" + form.count.value);
    options.push("offset=" + form.offset.value);
    options.push("textDecorations=true");
    options.push("textFormat=HTML");
    return options.join("&");
}
```

たとえば、実際の API 呼び出し内の `SafeSearch` パラメーターは、`strict`、`moderate`、`off` のいずれかにできます。既定値は `moderate` です。 ただし、フォームでは、2 つの状態しかないチェック ボックスを使用します。 JavaScript コードにより、この設定が `strict` または `off` のいずれかに変換されます (`moderate` は使用しません)。

## <a name="performing-the-request"></a>要求の実行
クエリ、オプションの文字列、および API キーを指定すると、`BingWebSearch` 関数は `XMLHttpRequest` オブジェクトを使用して Bing Search エンドポイントに要求を送信します。

```javascript
// Search on the query, using search options, authenticated by the key.
function bingWebSearch(query, options, key) {

    // scroll to top of window
    window.scrollTo(0, 0);
    if (!query.trim().length) return false;     // empty query, do nothing

    showDiv("noresults", "Working. Please wait.");
    hideDivs("pole", "mainline", "sidebar", "_json", "_headers", "paging1", "paging2", "error");

    var endpoint = "https://api.cognitive.microsoft.com/bing/v7.0/videos/search";
    var request = new XMLHttpRequest();
    var queryurl = endpoint + "?q=" + encodeURIComponent(query) + "&" + options;

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
    request.addEventListener("load", handleOnLoad);

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
HTTP 要求が正常に完了すると、JavaScript は API への HTTP GET 要求を正常に処理するために、`load` イベント ハンドラーの `handleOnLoad()` を呼び出します。 

```javascript
// handle Bing search request results
function handleOnLoad() {
    hideDivs("noresults");

    var json = this.responseText.trim();
    var jsobj = {};

    // try to parse JSON results
    try {
        if (json.length) jsobj = JSON.parse(json);
    } catch(e) {
        renderErrorMessage("Invalid JSON response");
    }

    // show raw JSON and headers
    showDiv("json", preFormat(JSON.stringify(jsobj, null, 2)));
    showDiv("http", preFormat("GET " + this.responseURL + "\n\nStatus: " + this.status + " " + 
        this.statusText + "\n" + this.getAllResponseHeaders()));

    // if HTTP response is 200 OK, try to render search results
    if (this.status === 200) {
        var clientid = this.getResponseHeader("X-MSEdge-ClientID");
        if (clientid) retrieveValue(CLIENT_ID_COOKIE, clientid);
        if (json.length) {
            if (jsobj._type === "Videos") {//"SearchResponse" && "rankingResponse" in jsobj) {
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
> 検索操作でエラーが発生した場合、Bing News Search API は 200 以外の HTTP 状態コードを返し、JSON 応答にエラー情報を含めます。 また、要求のレートが制限されている場合、API は空の応答を返します。
HTTP 要求が成功しても、必ずしも検索自体が成功したとは "*限りません*"。 

先ほどの両方の関数内にあるコードの多くは、エラー処理専用のものです。 エラーは次の段階で発生する場合があります。

|段階|起こり得るエラー|処理される場所|
|-|-|-|
|JavaScript 要求オブジェクトの作成|無効な URL|`try`/`catch` ブロック|
|要求の実行|ネットワーク エラー、接続の中止|`error` および `abort` イベント ハンドラー|
|検索の実行|無効な要求、無効な JSON、レート制限|`load` イベント ハンドラー内のテスト|

エラーは、`renderErrorMessage()` とエラーに関する既知の情報を呼び出すことで処理されます。 応答がエラー テストにすべてパスしたら、`renderSearchResults()` を呼び出して、ページ内に検索結果を表示します。

## <a name="displaying-search-results"></a>検索結果の表示
検索結果を表示するための主な関数は `renderSearchResults()` です。 この関数は、Bing News Search サービスによって返される JSON を受け取り、ニュース結果および関連する検索がある場合はそれらをレンダリングします。

```javascript
// render the search results given the parsed JSON response
function renderSearchResults(results) {

    // add Prev / Next links with result count
    var pagingLinks = renderPagingLinks(results);
    showDiv("paging1", pagingLinks);
    showDiv("paging2", pagingLinks);

    // Render the results to the mainline section
    for (section in { mainline: 0 }) {
         showDiv(section, renderResultsItems(section, results));
    }
}
```
検索結果は、JSON 応答で最上位の `value` オブジェクトとして返されます。 `renderResultsItems()` 関数は、これを受け取って反復処理した後、関数を呼び出して各項目を HTML としてレンダリングします。 結果の HTML は、`renderSearchResults()` に返されてから、ページ内の `results` ブロックに挿入されます。

```javascript
// render search results
    function renderResultsItems(section, results) {   

        var items = results.value;
        var html = [];
        for (var i = 0; i < items.length; i++) { 
            var item = items[i];
            // collection name has lowercase first letter
            var type = "videos";
            var render = searchItemRenderers[type];
            html.push(render(item, section));  
        }
    return html.join("\n\n");
}
```

Bing News Search API は、関連する結果を最大で 4 種類返します。これらの各結果は、それ自体の最上位のオブジェクトに含まれます。 次に例を示します。

|関係|説明|
|-|-|
|`pivotSuggestions`|元の検索の軸語を別のものに置き換えるクエリです。 たとえば、"赤い花" を検索した場合、"赤" が軸語で、"黄色い花" が候補になる可能性があります。|
|`queryExpansions`|複数の条件を追加することで、元の検索を絞り込むクエリです。 たとえば、"Microsoft Surface" を検索した場合、クエリが "Microsoft Surface Pro" に拡張される場合があります。|
|`relatedSearches`|元の検索を入力した他のユーザーが他に入力したことのあるクエリです。 たとえば、"レーニア山" を検索した場合、関連する検索は "セント ヘレンズ山" となる場合があります。|
|`similarTerms`|元の検索と似た意味を持つクエリです。 たとえば、"学校" を検索すると、似た用語として "教育" が挙げられる場合があります。|

`renderSearchResults()` で確認したように、`relatedItems` の候補だけがレンダリングされ、結果のリンクはページのサイドバーに配置されます。

## <a name="rendering-result-items"></a>結果項目のレンダリング

JavaScript コードのオブジェクト `searchItemRenderers` は、検索結果の種類ごとに HTML を生成する *renderers:* 関数を含むことができます。 ビデオ検索ページでは、`videos` だけを使います。 さまざまな種類のレンダラーについては、他のチュートリアルをご覧ください。

```javascript
searchItemRenderers = {
    news: function(item) { ... },
    webPages: function (item) { ... }, 
    images: function(item, index, count) { ... },
    videos: function (item, section, index, count) { ... },
    relatedSearches: function(item) { ... }
}
```
レンダラー関数では、次のパラメーターを受け取ることができます。

|パラメーター|説明|
|-|-|
|`item`| 項目のプロパティ (URL とその説明など) を含む JavaScript オブジェクト。|
|`index`| コレクション内の結果項目のインデックス。|
|`count`| 検索結果項目のコレクション内の項目の数。|

`index` パラメーターと `count` パラメーターは、結果のカウント、コレクションの先頭または末尾の特別な HTML の生成、特定の数の項目の後への改行の挿入などに使用できます。 レンダラーにこの機能が必要ない場合は、これら 2 つのパラメーターを受け取る必要はありません。

次の JavaScript の抜粋には、`video` レンダラーが示されています。 Videos エンドポイントを使用すると、すべての結果は `Videos` 型になります。 次のコード セグメントでは、`searchItemRenderers` が示されています。

```javascript
// render functions for various types of search results
    searchItemRenderers = {

    videos: function (item, section, index, count) {
        var height = 60;
        var width = Math.round(height * item.thumbnail.width / item.thumbnail.height);
        var html = [];

        html.push("<p class='images'>");
        html.push("<a href='" + item.hostPageUrl + "'>");
        var title = escapeQuotes(item.name) + "\n" + getHost(item.hostPageDisplayUrl);
        html.push("<img src='" + item.thumbnailUrl + "&h=" + height + "&w=" + width +
            "' height=" + height + " width=" + width + " title='" + title + "' alt='" + title + "'>");
        html.push("</a>");
        html.push("<br>");
        html.push("<nobr><a href='" + item.contentUrl + "'>Video page source</a> - ");
        html.push(title.replace("\n", " (").replace(/([a-z0-9])\.([a-z0-9])/g, "$1.<wbr>$2") + ")</p>");
        return html.join("");
    }
}
```

レンダラー関数は次の処理を行います。
> [!div class="checklist"]
> * 段落タグを作成し、それを `images` クラスに割り当てて、html 配列にそれをプッシュします。
> * 画像のサムネイルのサイズを計算します (幅は 60 ピクセル固定、高さは比例して計算)。
> * 画像のサムネイルを表示する HTML `<img>` タグをビルドします。 
> * 画像およびその画像が含まれるページにリンクされる HTML `<a>` タグをビルドします。
> * 画像およびその画像が存在するサイトに関する情報を示す説明をビルドします。

サムネイルのサイズは、サムネイルの URL 内の `<img>` タグに加えて、`h` および `w` フィールドで使用されます。 Bing では、正確にそのサイズの[サムネイル](../bing-web-search/resize-and-crop-thumbnails.md)が返されます。

## <a name="persisting-client-id"></a>クライアント ID の永続化
Bing Search API からの応答には、`X-MSEdge-ClientID` ヘッダーが含まれることがあり、このヘッダーは後続の要求で API に送り返す必要があります。 複数の Bing Search API を使用している場合、可能であれば、それらすべてで同じクライアント ID を使用する必要があります。

`X-MSEdge-ClientID` ヘッダーを提供すると、Bing API がユーザーのすべての検索を関連付けられるようになります。これには、2 つの重要な利点があります。

1 つ目は、Bing 検索エンジンが、検索に過去のコンテキストを適用できるようになることです。これにより、ユーザーがより満足できる結果を見つけることができます。 たとえば、ユーザーが過去に航海に関連する用語を検索したことがあった場合、その後の "ノット" という検索では、航海用語としてのノットに関する情報を優先的に返すことができます。

2 つ目は、Bing でユーザーをランダムに選択し、まだ広く利用されていない新機能を体験してもらえるようになることです。 各要求で同じクライアント ID を提供することで、その機能を見るユーザーには常にそれが表示されます。 クライアント ID を提供しないと、ユーザーには、検索結果内で機能が見かけ上ランダムに表示されたり消えたりする可能性があります。

ブラウザーのセキュリティ ポリシー (CORS) により、`X-MSEdge-ClientID` ヘッダーを JavaScript で使用できない場合もあります。 この制限は、検索応答のオリジンとその要求元のページとが異なる場合に生じます。 運用環境では、API 呼び出しを実行するサーバー側スクリプトを Web ページと同じドメインに ホストして、このポリシーに対応する必要があります。 スクリプトのオリジンが Web ページと同じになるので、`X-MSEdge-ClientID` ヘッダーを JavaScript で利用できます。

> [!NOTE]
> 運用環境の Web アプリケーションでは、要求サーバー側を実行する必要があります。 そうしないと、Web ページに Bing Search API キーを含める必要があり、ソースを表示した人ならだれでもキーを使用できるようになってしまいます。 権限のない者によって行われた要求であっても、すべての利用に対してユーザーの API サブスクリプション キーに基づいて料金が発生するため、キーは公開しないようにする必要があります。

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
> [Bing Video Search API リファレンス](//docs.microsoft.com/rest/api/cognitiveservices/bing-video-api-v7-reference)
