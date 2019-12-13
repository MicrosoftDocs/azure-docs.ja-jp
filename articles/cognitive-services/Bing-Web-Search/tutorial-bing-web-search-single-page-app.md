---
title: チュートリアル:単一ページの Web アプリを作成する - Bing Web Search API
titleSuffix: Azure Cognitive Services
description: この単一ページのアプリは、Bing Web Search API を使用して、単一ページのアプリで関連する検索結果を取得、解析、および表示する方法を示しています。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-web-search
ms.topic: tutorial
ms.date: 12/09/2019
ms.author: aahi
ms.openlocfilehash: ec6c1ef31b6cf92629be600b3b139bb2e1a0d3ce
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/10/2019
ms.locfileid: "74977252"
---
# <a name="tutorial-create-a-single-page-app-using-the-bing-web-search-api"></a>チュートリアル:Bing Web Search API を使用して単一ページの Web アプリを作成する

この単一ページのアプリは、Bing Web Search API から検索結果を取得、解析、および表示する方法を示しています。 このチュートリアルでは、定型の HTML と CSS を使用し、JavaScript コードに焦点を当てています。 HTML、CSS、および JS ファイルとクイック スタート手順は、[GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/tree/master/Tutorials/Bing-Web-Search) に掲載されています。

このサンプル アプリでは以下を実行できます。

> [!div class="checklist"]
> * 検索オプションを使用して Bing Web Search API を呼び出す
> * Web、画像、ニュース、および動画の結果を表示する
> * 結果を改ページする
> * サブスクリプション キーを管理する
> * エラーを処理する

このアプリを使用するには、[Azure Cognitive Services アカウント](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account)と Bing Search API が必要です。 アカウントを持っていない場合は、[無料試用版](https://azure.microsoft.com/try/cognitive-services/?api=bing-web-search-api)を使用してサブスクリプション キーを取得できます。

## <a name="prerequisites"></a>前提条件

アプリを実行するために必要なものを次に示します。

* Node.js 8 以降
* サブスクリプション キー

## <a name="get-the-source-code-and-install-dependencies"></a>ソース コードを入手して依存関係をインストールする

最初の手順は、サンプル アプリのソース コードを使用してリポジトリを複製することです。

```console
git clone https://github.com/Azure-Samples/cognitive-services-REST-api-samples.git
```

次に、`npm install` を実行します。 このチュートリアルでは、Express.js のみが依存関係です。

```console
cd <path-to-repo>/cognitive-services-REST-api-samples/Tutorials/Bing-Web-Search
npm install
```

## <a name="app-components"></a>アプリのコンポーネント

ここで構築するサンプル アプリは、4 つの部分で構成されています。

* `bing-web-search.js` - この Express.js アプリ。 要求/応答ロジックとルーティングを処理します。
* `public/index.html` - アプリのスケルトン。データをユーザーに表示する方法を定義します。
* `public/css/styles.css` - フォント、色、テキスト サイズなどのページ スタイルを定義します。
* `public/js/scripts.js` - Bing Web Search API に対して要求を行い、サブスクリプション キーを管理し、応答を処理して解析し、結果を表示するロジックが含まれています。

このチュートリアルでは、`scripts.js` と、Bing Web Search API を呼び出して応答を処理するために必要なロジックに焦点を当てます。

## <a name="html-form"></a>HTML フォーム

`index.html` には、ユーザーが検索オプションを検索して選択できるフォームが含まれています。 フォームが送信され、`scripts.js` に定義されている `bingWebSearch()` メソッドが呼び出されると、`onsubmit` 属性が呼び出されます。 このスクリプトは次の 3 つの引数を受け取ります。

* Search query (検索クエリ)
* 選択されるオプション
* Subscription key

```html
<form name="bing" onsubmit="return bingWebSearch(this.query.value,
    bingSearchOptions(this), getSubscriptionKey())">
```

## <a name="query-options"></a>クエリ オプション

HTML フォームには、[Bing Web Search API v7](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#query-parameters) のクエリ パラメーターにマップされるオプションが含まれています。 この表は、サンプル アプリを使用してユーザーが検索結果をフィルター処理する方法の内訳を示しています。

| パラメーター | 説明 |
|-----------|-------------|
| `query` | クエリ文字列を入力するテキスト フィールド。 |
| `where` | 市場 (場所と言語) を選択するドロップダウン メニュー。 |
| `what` | 特定の結果の種類のレベルを上げるチェックボックス。 たとえば、画像のレベルを上げると、検索結果の画像の順位が上がります。 |
| `when` | ユーザーが検索結果を今日、今週、今月に制限できるドロップダウン メニュー。 |
| `safe` | 成人向けコンテンツを除外する Bing SafeSearch を有効にするチェックボックス。 |
| `count` | 隠しフィールド。 各要求に対して返される検索結果の数。 この値を変更すると、1 ページあたりの結果の表示数が増減します。 |
| `offset` | 隠しフィールド。 要求における最初の検索結果のオフセット。ページングに使用されます。 新しい要求ごとに `0` にリセットされます。 |

> [!NOTE]
> Bing Web Search API には、検索結果を絞り込むための追加のクエリ パラメーターが用意されています。 このサンプルでは、ごく一部のみを使用しています。 使用できるパラメーターの詳細な一覧については、[Bing Web Search API v7 リファレンス](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#query-parameters)を参照してください。

`bingSearchOptions()` 関数は、Bing Search API で必要な形式にこれらのオプションを変換します。

```javascript
// Build query options from selections in the HTML form.
function bingSearchOptions(form) {

    var options = [];
    // Where option.
    options.push("mkt=" + form.where.value);
    // SafeSearch option.
    options.push("SafeSearch=" + (form.safe.checked ? "strict" : "off"));
    // Freshness option.
    if (form.when.value.length) options.push("freshness=" + form.when.value);
    var what = [];
    for (var i = 0; i < form.what.length; i++)
        if (form.what[i].checked) what.push(form.what[i].value);
    // Promote option.
    if (what.length) {
        options.push("promote=" + what.join(","));
        options.push("answerCount=9");
    }
    // Count option.
    options.push("count=" + form.count.value);
    // Offset option.
    options.push("offset=" + form.offset.value);
    // Hardcoded text decoration option.
    options.push("textDecorations=true");
    // Hardcoded text format option.
    options.push("textFormat=HTML");
    return options.join("&");
}
```

`SafeSearch` は `strict`、`moderate`、または `off` に設定できます。`moderate` は Bing Web Search の既定の設定です。 このフォームでは、2 つの状態があるチェックボックスを使用します。 このスニペットでは、SafeSearch は `strict` または `off` に設定され、`moderate` は使用されていません。

**[レベル上げ]** チェックボックスがオンの場合、`answerCount` パラメーターがクエリに追加されます。 `answerCount` は `promote` パラメーターを使用する際に必要です。 このスニペットでは、使用できるすべての結果の種類を返す `9` に値が設定されています。
> [!NOTE]
> 結果の種類のレベルを上げても、検索結果に含まれることは*保証*されません。 レベル上げの効果は、その種類の結果の順位を、通常の順位よりも上げるというものです。 検索結果を特定の種類だけに制限するには、`responseFilter` クエリ パラメーターを使用するか、Bing Image Search や Bing News Search など、より具体的なエンドポイントを呼び出します。

`textDecoration` および `textFormat` クエリ パラメーターをスクリプトにハードコーディングして、検索用語が検索結果内で太字で表示されるようにします。 これらのパラメーターは必須ではありません。

## <a name="manage-subscription-keys"></a>サブスクリプション キーを管理する

Bing Search API サブスクリプション キーのハードコーディングを避けるため、このサンプル アプリでは、ブラウザーの固定ストレージを使用してサブスクリプション キーを保存しています。 サブスクリプション キーが格納されていない場合、ユーザーには入力が求められます。 サブスクリプション キーが API によって拒否された場合、ユーザーにはサブスクリプション キーを再入力するように求められます。

`getSubscriptionKey()` 関数は、`storeValue` および `retrieveValue` 関数を使用してユーザーのサブスクリプション キーを格納および取得します。 これらの関数は、`localStorage` オブジェクト (サポートされている場合) または Cookie を使用します。

```javascript
// Cookie names for stored data.
API_KEY_COOKIE   = "bing-search-api-key";
CLIENT_ID_COOKIE = "bing-search-client-id";

BING_ENDPOINT = "https://api.cognitive.microsoft.com/bing/v7.0/search";

// See source code for storeValue and retrieveValue definitions.

// Get stored subscription key, or prompt if it isn't found.
function getSubscriptionKey() {
    var key = retrieveValue(API_KEY_COOKIE);
    while (key.length !== 32) {
        key = prompt("Enter Bing Search API subscription key:", "").trim();
    }
    // Always set the cookie in order to update the expiration date.
    storeValue(API_KEY_COOKIE, key);
    return key;
}
```

前述のように、フォームが送信されると、`onsubmit` が呼び出され、`bingWebSearch` が呼び出されます。 この関数は要求を初期化して送信します。 要求を認証する送信ごとに `getSubscriptionKey` が呼び出されます。

## <a name="call-bing-web-search"></a>Bing Web Search を呼び出す

クエリ、オプションの文字列、およびサブスクリプション キーを指定すると、`BingWebSearch` 関数で Bing Web Search エンドポイントを呼び出す `XMLHttpRequest` オブジェクトが作成されます。

```javascript
// Perform a search constructed from the query, options, and subscription key.
function bingWebSearch(query, options, key) {
    window.scrollTo(0, 0);
    if (!query.trim().length) return false;

    showDiv("noresults", "Working. Please wait.");
    hideDivs("pole", "mainline", "sidebar", "_json", "_http", "paging1", "paging2", "error");

    var request = new XMLHttpRequest();
    var queryurl = BING_ENDPOINT + "?q=" + encodeURIComponent(query) + "&" + options;

    // Initialize the request.
    try {
        request.open("GET", queryurl);
    }
    catch (e) {
        renderErrorMessage("Bad request (invalid URL)\n" + queryurl);
        return false;
    }

    // Add request headers.
    request.setRequestHeader("Ocp-Apim-Subscription-Key", key);
    request.setRequestHeader("Accept", "application/json");
    var clientid = retrieveValue(CLIENT_ID_COOKIE);
    if (clientid) request.setRequestHeader("X-MSEdge-ClientID", clientid);

    // Event handler for successful response.
    request.addEventListener("load", handleBingResponse);

    // Event handler for errors.
    request.addEventListener("error", function() {
        renderErrorMessage("Error completing request");
    });

    // Event handler for an aborted request.
    request.addEventListener("abort", function() {
        renderErrorMessage("Request aborted");
    });

    // Send the request.
    request.send();
    return false;
}
```

要求が成功すると、`load` イベント ハンドラーが起動し、`handleBingResponse` 関数が呼び出されます。 `handleBingResponse` で結果オブジェクトが解析され、結果が表示され、失敗した要求のエラー ロジックが含まれます。

```javascript
function handleBingResponse() {
    hideDivs("noresults");

    var json = this.responseText.trim();
    var jsobj = {};

    // Try to parse results object.
    try {
        if (json.length) jsobj = JSON.parse(json);
    } catch(e) {
        renderErrorMessage("Invalid JSON response");
        return;
    }

    // Show raw JSON and the HTTP request.
    showDiv("json", preFormat(JSON.stringify(jsobj, null, 2)));
    showDiv("http", preFormat("GET " + this.responseURL + "\n\nStatus: " + this.status + " " +
        this.statusText + "\n" + this.getAllResponseHeaders()));

    // If the HTTP response is 200 OK, try to render the results.
    if (this.status === 200) {
        var clientid = this.getResponseHeader("X-MSEdge-ClientID");
        if (clientid) retrieveValue(CLIENT_ID_COOKIE, clientid);
        if (json.length) {
            if (jsobj._type === "SearchResponse" && "rankingResponse" in jsobj) {
                renderSearchResults(jsobj);
            } else {
                renderErrorMessage("No search results in JSON response");
            }
        } else {
            renderErrorMessage("Empty response (are you sending too many requests too quickly?)");
        }
    }

    // Any other HTTP response is considered an error.
    else {
        // 401 is unauthorized; force a re-prompt for the user's subscription
        // key on the next request.
        if (this.status === 401) invalidateSubscriptionKey();

        // Some error responses don't have a top-level errors object, if absent
        // create one.
        var errors = jsobj.errors || [jsobj];
        var errmsg = [];

        // Display the HTTP status code.
        errmsg.push("HTTP Status " + this.status + " " + this.statusText + "\n");

        // Add all fields from all error responses.
        for (var i = 0; i < errors.length; i++) {
            if (i) errmsg.push("\n");
            for (var k in errors[i]) errmsg.push(k + ": " + errors[i][k]);
        }

        // Display Bing Trace ID if it isn't blocked by CORS.
        var traceid = this.getResponseHeader("BingAPIs-TraceId");
        if (traceid) errmsg.push("\nTrace ID " + traceid);

        // Display the error message.
        renderErrorMessage(errmsg.join("\n"));
    }
}
```

> [!IMPORTANT]
> HTTP 要求が成功しても、検索自体が成功したとは*限りません*。 検索操作でエラーが発生した場合、Bing Web Search API は 200 以外の HTTP 状態コードを返し、JSON 応答にエラー情報を含めます。 要求のレートが制限されている場合、API は空の応答を返します。

先ほどの両方の関数内にあるコードの多くは、エラー処理専用のものです。 エラーは次の段階で発生する場合があります。

| 段階 | 起こり得るエラー | 処理される場所 |
|-------|--------------------|------------|
| 要求オブジェクトを構築する | 無効な URL | `try` / `catch` ブロック |
| 要求の実行 | ネットワーク エラー、接続の中止 | `error` および `abort` イベント ハンドラー |
| 検索の実行 | 無効な要求、無効な JSON、レート制限 | `load` イベント ハンドラー内のテスト |

エラーを処理するには、`renderErrorMessage()` を呼び出します。 応答ですべてのエラー テストが渡されると、検索結果を表示する `renderSearchResults()` が呼び出されます。

## <a name="display-search-results"></a>検索結果を表示する

Bing Web Search API から返される結果の[使用と表示の要件](useanddisplayrequirements.md)があります。 応答にはさまざまな結果の種類が含まれている可能性があるため、最上位レベルの `WebPages` コレクションを反復処理するだけでは不十分です。 その代わり、このサンプル アプリでは `RankingResponse` を使用して仕様に合わせて結果を並べ替えます。

> [!NOTE]
> 1 つの結果の種類のみが必要な場合は、クエリ パラメーター `responseFilter` を使用します。また、Bing Image Search など、他の Bing Search エンドポイントのいずれかを使用することを検討します。

各応答には `RankingResponse` オブジェクトがあり、このオブジェクトには `pole`、`mainline`、`sidebar` というコレクションを 3 つまで含めることができます。 `pole` (存在する場合) は最も関連性の高い検索結果であり、目立つように表示する必要があります。 `mainline` には検索結果の大部分が含まれ、`pole` の直後に表示されます。 `sidebar` には補助的な検索結果が含まれます。 可能であれば、これらの結果はサイドバーに表示するようにします。 画面に制限があり、サイドバーが実用的ではない場合は、`mainline` の結果の後に表示するようにします。

各 `RankingResponse` には、結果の並べ替え方法を指定する `RankingItem` 配列が含まれます。 このサンプル アプリでは、結果を特定するために `answerType` および `resultIndex` パラメーターを使用します。

> [!NOTE]
> 結果を特定し、ランクを付けるにはその他の方法もあります。 詳細については、[ランキングを使用して結果を表示する](rank-results.md)方法に関するページを参照してください。

コードについて見てみましょう。

```javascript
// Render the search results from the JSON response.
function renderSearchResults(results) {

    // If spelling was corrected, update the search field.
    if (results.queryContext.alteredQuery)
        document.forms.bing.query.value = results.queryContext.alteredQuery;

    // Add Prev / Next links with result count.
    var pagingLinks = renderPagingLinks(results);
    showDiv("paging1", pagingLinks);
    showDiv("paging2", pagingLinks);

    // Render the results for each section.
    for (section in {pole: 0, mainline: 0, sidebar: 0}) {
        if (results.rankingResponse[section])
            showDiv(section, renderResultsItems(section, results));
    }
}
```

`renderResultsItems()` 関数は各 `RankingResponse` コレクション内の項目を反復処理し、`answerType` および `resultIndex` 値を使用して各ランキング結果を検索結果にマッピングします。さらに、適切なレンダリング関数を呼び出して、HTML を生成します。 項目に `resultIndex` が指定されていない場合は、`renderResultsItems()` がその種類の結果すべてを反復処理し、各項目でレンダリング関数を呼び出します。 結果の HTML は、`index.html` 内の適切な `<div>` 要素に挿入されます。

```javascript
// Render search results from the RankingResponse object per rank response and
// use and display requirements.
function renderResultsItems(section, results) {

    var items = results.rankingResponse[section].items;
    var html = [];
    for (var i = 0; i < items.length; i++) {
        var item = items[i];
        // Collection name has lowercase first letter while answerType has uppercase
        // e.g. `WebPages` RankingResult type is in the `webPages` top-level collection.
        var type = item.answerType[0].toLowerCase() + item.answerType.slice(1);
        if (type in results && type in searchItemRenderers) {
            var render = searchItemRenderers[type];
            // This ranking item refers to ONE result of the specified type.
            if ("resultIndex" in item) {
                html.push(render(results[type].value[item.resultIndex], section));
            // This ranking item refers to ALL results of the specified type.
            } else {
                var len = results[type].value.length;
                for (var j = 0; j < len; j++) {
                    html.push(render(results[type].value[j], section, j, len));
                }
            }
        }
    }
    return html.join("\n\n");
}
```

## <a name="review-renderer-functions"></a>レンダラー関数を確認する

このサンプル アプリで、`searchItemRenderers` オブジェクトには、検索結果の種類ごとに HTML を生成する関数が含まれています。

```javascript
// Render functions for each result type.
searchItemRenderers = {
    webPages: function(item) { ... },
    news: function(item) { ... },
    images: function(item, section, index, count) { ... },
    videos: function(item, section, index, count) { ... },
    relatedSearches: function(item, section, index, count) { ... }
}
```

> [!IMPORTANT]
> このサンプル アプリには、Web ページ、ニュース、画像、動画、関連する検索用のレンダラーがあります。 このアプリケーションでは、受け取る可能性のあるすべての種類の結果に対するレンダラーが必要です。結果の種類には、計算、スペル修正候補、エンティティ、タイム ゾーン、定義などがあります。

一部のレンダリング関数は `item` パラメーターのみを受け取ります。 コンテキストに基づいて項目を別の方法でレンダリングするために使用できる他のパラメーターを受け取るものもあります (この情報を使わないレンダラーは、これらのパラメーターを受け取る必要はありません)。

コンテキスト引数は次のとおりです。

| パラメーター  | 説明 |
|------------|-------------|
| `section` | 項目が表示される結果のセクション (`pole`、`mainline`、`sidebar`) です。 |
| `index`<br>`count` | 特定のコレクション内のすべての結果が表示されるように `RankingResponse` 項目で指定されている場合に使用可能です。それ以外の場合は `undefined` を使用します。 コレクション内の項目のインデックスと、そのコレクション内の項目の総数。 この情報を使用すると、結果の数をカウントしたり、最初または最後の結果について別の HTML を生成したりすることなどができます。 |

このサンプル アプリでは、`images` および `relatedSearches` レンダラーの両方でコンテキスト引数を使用して、生成された HTML をカスタマイズします。 `images` レンダラーについて詳しく見ていきましょう。

```javascript
searchItemRenderers = {
    // Render image result with thumbnail.
    images: function(item, section, index, count) {
        var height = 60;
        var width = Math.round(height * item.thumbnail.width / item.thumbnail.height);
        var html = [];
        if (section === "sidebar") {
            if (index) html.push("<br>");
        } else {
            if (!index) html.push("<p class='images'>");
        }
        html.push("<a href='" + item.hostPageUrl + "'>");
        var title = escape(item.name) + "\n" + getHost(item.hostPageDisplayUrl);
        html.push("<img src='"+ item.thumbnailUrl + "&h=" + height + "&w=" + width +
            "' height=" + height + " width=" + width + " title='" + title + "' alt='" + title + "'>");
        html.push("</a>");
        return html.join("");
    },
    // Other renderers are omitted from this sample...
}
```

画像レンダラー:

* 画像のサムネイルのサイズを計算します (高さは 60 ピクセルに固定されます)。
* コンテキストに基づいて、画像の結果の前に HTML を挿入します。
* イメージを含むページにリンクする HTML `<a>` タグをビルドする。
* 画像のサムネイルを表示する HTML `<img>` タグをビルドします。

画像レンダラーは、`section` および `index` 変数を使用して、表示場所によって異なる方法で結果を表示します。 改行 (`<br>` タグ) はサイドバー内の画像の結果の間に挿入されます。これにより、サイドバーに画像が列になって表示されます。 他のセクション内では、最初の画像の結果である `(index === 0)` は `<p>` タグの前に挿入されます。

サムネイルのサイズは、サムネイルの URL 内の `<img>` タグに加えて、`h` および `w` フィールドで使用されます。 `title` および `alt` 属性 (画像の説明テキスト) は、画像の名前と URL のホスト名で構成されます。

サンプル アプリで画像が表示される例を次に示します。

![[Bing の画像の結果]](media/cognitive-services-bing-web-api/web-search-spa-images.png)

## <a name="persist-the-client-id"></a>クライアント ID を保持する

Bing Search API からの応答には、`X-MSEdge-ClientID` ヘッダーが含まれることがあり、このヘッダーは後続の要求の度に API に送り返す必要があります。 アプリで複数の Bing Search API を使用している場合は、サービス全体で要求ごとに同じクライアント ID が送信されていることを確認してください。

`X-MSEdge-ClientID` ヘッダーを指定すると、Bing API でユーザーの検索を関連付けることができます。 1 つ目は、Bing 検索エンジンが、検索に過去のコンテキストを適用できるようになることです。これにより、要求をより満たす結果を見つけることができます。 たとえば、ユーザーが過去に航海に関連する用語を検索したことがあった場合、その後の "ノット" という検索では、航海用語としてのノットに関する情報を優先的に返すことができます。 2 つ目は、Bing でユーザーをランダムに選択し、まだ広く利用されていない新機能を体験してもらえるようになることです。 各要求で同じクライアント ID を提供することで、機能の表示対象として選択されているユーザーには、常にそれが表示されるようになります。 クライアント ID を提供しないと、ユーザーには、検索結果内で機能が見かけ上ランダムに表示されたり消えたりする可能性があります。

クロスオリジン リソース共有 (CORS) などのブラウザー セキュリティ ポリシーで、サンプル アプリが `X-MSEdge-ClientID` ヘッダーにアクセスできなくなることがあります。 この制限は、検索応答のオリジンとその要求元のページとが異なる場合に生じます。 運用環境では、API 呼び出しを実行するサーバー側スクリプトを Web ページと同じドメインに ホストして、このポリシーに対応する必要があります。 スクリプトのオリジンが Web ページと同じになるので、`X-MSEdge-ClientID` ヘッダーを JavaScript で利用できます。

> [!NOTE]
> 運用環境の Web アプリケーションでは、いずれにしても要求サーバー側を実行する必要があります。 そうしないと、Web ページに Bing Search API サブスクリプション キーを含める必要があり、ソースを表示した人ならだれでもキーを使用できるようになってしまいます。 権限のない者によって行われた要求であっても、すべての利用に対してユーザーの API サブスクリプション キーに基づいて料金が発生するため、キーは公開しないようにする必要があります。

開発目的の場合は、CORS プロキシを介して要求を行うことができます。 このようなプロキシからの応答には、応答ヘッダーをホワイトリストに登録して JavaScript で使用できるようにする `Access-Control-Expose-Headers` ヘッダーが含まれています。

CORS プロキシをインストールして、サンプル アプリがクライアント ID ヘッダーにアクセスできるようにするのは簡単です。 次のコマンドを実行します。

```console
npm install -g cors-proxy-server
```

次に、`script.js` 内の Bing Web Search エンドポイントを次のように変更します。

```javascript
http://localhost:9090/https://api.cognitive.microsoft.com/bing/v7.0/search
```

次のコマンドを使用して CORS プロキシを起動します。

```console
cors-proxy-server
```

サンプル アプリを使用している間はコマンド ウィンドウを開いたままにしておいてください。ウィンドウを閉じるとプロキシが停止します。 検索結果の下にある展開可能な [HTTP ヘッダー] セクションには、`X-MSEdge-ClientID` ヘッダーが表示されるはずです。 これが各要求で同じであることを確認します。

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [Bing Web Search API v7 リファレンス](//docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference)
