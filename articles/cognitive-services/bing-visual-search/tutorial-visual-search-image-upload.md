---
title: 'チュートリアル: 画像をアップロードする方法 - Bing Visual Search'
titleSuffix: Azure Cognitive Services
description: Bing に画像をアップロードしてその画像の分析情報を取得してから、応答を解析および表示するプロセスを解説します。
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-visual-search
ms.topic: tutorial
ms.date: 04/03/2019
ms.author: scottwhi
ms.openlocfilehash: 0963c61027358c2c8e971533052631de28994b57
ms.sourcegitcommit: 1a19a5845ae5d9f5752b4c905a43bf959a60eb9d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/11/2019
ms.locfileid: "59491085"
---
# <a name="tutorial-upload-images-to-the-bing-visual-search-api"></a>チュートリアル:Bing Visual Search API に画像をアップロードする

Bing Visual Search API を使用すると、アップロードする画像と似ている画像を Web で検索することができます。 このチュートリアルを使用して、画像を API に送信し、返される分析情報を Web ページ内に表示できる Web アプリケーションを構築します。 このアプリケーションは、API を使用するための [Bing の利用と表示の要件](../bing-web-search/use-display-requirements.md)すべてに準拠していないことに注意してください。

このサンプルのソース コード全体は、追加のエラー処理と注釈を含め、[GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/Tutorials/Bing-Visual-Search/BingVisualSearchUploadImage.html) で入手できます。

このチュートリアル アプリは、次の方法を示しています。

> [!div class="checklist"]
> * Bing Visual Search API に画像をアップロードします。
> * Web アプリケーションの画像検索結果を表示します。
> * API によって提供されるさまざまな分析情報を探索します。

## <a name="prerequisites"></a>前提条件

[!INCLUDE [cognitive-services-bing-image-search-signup-requirements](../../../includes/cognitive-services-bing-visual-search-signup-requirements.md)]

## <a name="create-and-structure-the-webpage"></a>Web ページを作成して構造を設定する

Bing Visual Search API に画像を送信し、分析情報を受信して表示する HTML ページを作成します。 任意のエディターまたは IDE で、"uploaddemo.html" という名前のファイルを作成します。 次の基本的な HTML 構造をファイルに追加します。

```html
<!DOCTYPE html>
<html xmlns="http://www.w3.org/1999/xhtml">
    <head>
        <title>Visual Search Upload Demo</title>
    </head>

    <body>
    </body>
</html>
```

要求に必要なすべての情報をユーザーが提供する要求セクションと、分析情報が表示される応答セクションにページを分けます。 次の `<div>` タグを `<body>` に追加します。 `<hr>` タグによって、要求セクションと応答セクションが視覚的に区切られます。

```html
<div id="requestSection"></div>
<hr />
<div id="responseSection"></div>
```

`<script>` タグを `<head>` タグに追加して、アプリケーションの JavaScript を含めます。

```html
<script>
<\script>
```

## <a name="get-the-upload-file"></a>アップロード ファイルを取得する

アップロードする画像をユーザーに選択させるために、アプリケーションでは type 属性を `file` に設定した `<input>` タグを使用しています。 アプリケーションで Bing を使用して検索結果を取得することを UI で明確にする必要があります。

次の `<div>` を `requestSection` `<div>` に追加します。 ファイル入力は、任意の画像タイプ (.jpg、.gif、.png など) の 1 つのファイルを受け入れます。 `onchange` イベントは、ユーザーがファイルを選択したときに呼び出されるハンドラーを指定します。

`<output>` タグは、選択された画像のサムネイルを表示するために使用されます。

```html
<div>
    <p>Select image to get insights from Bing:
        <input type="file" accept="image/*" id="uploadImage" name="files[]" size=40 onchange="handleFileSelect('uploadImage')" />
    </p>

    <output id="thumbnail"></output>
</div>
```

## <a name="create-a-file-handler"></a>ファイル ハンドラーを作成する

アップロードする画像内で読み取りを行うことができるハンドラー関数を作成します。 ハンドラーは、`FileList` オブジェクト内のファイルを反復処理するときに、選択されたファイルが画像ファイルであり、サイズが 1 MB 以下であることを確認する必要があります。 画像が大きい場合は、アップロードする前にサイズを減らす必要があります。 最後に、ハンドラーによって画像のサムネイルが表示されます。

```javascript
function handleFileSelect(selector) {

    var files = document.getElementById(selector).files; // A FileList object

    for (var i = 0, f; f = files[i]; i++) {

        // Ensure the file is an image file.
        if (!f.type.match('image.*')) {
            alert("Selected file must be an image file.");
            document.getElementById("uploadImage").value = null;
            continue;
        }

        // Image must be <= 1 MB and should be about 1500px.
        if (f.size > 1000000) {
            alert("Image must be less than 1 MB.");
            document.getElementById("uploadImage").value = null;
            continue;
        }

        var reader = new FileReader();

        // Capture the file information.
        reader.onload = (function(theFile) {
            return function(e) {
                var fileOutput = document.getElementById('thumbnail');

                if (fileOutput.childElementCount > 0) {
                    fileOutput.removeChild(fileOutput.lastChild);  // Remove the current pic, if it exists
                }

                // Render thumbnail.
                var span = document.createElement('span');
                span.innerHTML = ['<img class="thumb" src="', e.target.result,
                                    '" title="', escape(theFile.name), '"/>'].join('');
                fileOutput.insertBefore(span, null);
            };
        })(f);

        // Read in the image file as a data URL.
        reader.readAsDataURL(f);
    }
}
```

## <a name="add-and-store-a-subscription-key"></a>サブスクリプション キーを追加して格納する

アプリケーションには、Bing Visual Search API を呼び出すサブスクリプション キーが必要です。 このチュートリアルの場合、これを UI に用意します。 `<body>` 内で、(type 属性を text に設定した) 次の `<input>` タグを、ファイルの `<output>` タグのすぐ下に追加します。

```html
    <div>
        <p>Subscription key: 
            <input type="text" id="key" name="subscription" size=40 maxlength="32" />
        </p>
    </div>
```

画像とサブスクリプション キーがあれば、Bing Visual Search を呼び出して画像の分析情報を取得することができます。 このチュートリアルの呼び出しでは、既定の市場 (`en-us`) とセーフ サーチ値 (`moderate`) を使用します。

このアプリケーションには、これらの値を変更するオプションがあります。 次の `<div>` をサブスクリプション キーの `<div>` の下に追加します。 アプリケーションでは `<select>` タグを使用して、市場とセーフ サーチの値のドロップダウン リストを提供します。 どちらのリストにも既定値が表示されます。

```html
<div>
    <p><a href="#" onclick="expandCollapse(options.id)">Query options</a></p>

    <div id="options" style="display:none">
        <p style="margin-left: 20px">Market: 
            <select id="mkt">
                <option value="es-AR">Argentina (Spanish)</option>
                <option value="en-AU">Australia (English)</option>
                <option value="de-AT">Austria (German)</option>
                <option value="nl-BE">Belgium (Dutch)</option>
                <option value="fr-BE">Belgium (French)</option>
                <option value="pt-BR">Brazil (Portuguese)</option>
                <option value="en-CA">Canada (English)</option>
                <option value="fr-CA">Canada (French)</option>
                <option value="es-CL">Chile (Spanish)</option>
                <option value="da-DK">Denmark (Danish)</option>
                <option value="fi-FI">Finland (Finnish)</option>
                <option value="fr-FR">France (French)</option>
                <option value="de-DE">Germany (German)</option>
                <option value="zh-HK">Hong Kong (Traditional Chinese)</option>
                <option value="en-IN">India (English)</option>
                <option value="en-ID">Indonesia (English)</option>
                <option value="it-IT">Italy (Italian)</option>
                <option value="ja-JP">Japan (Japanese)</option>
                <option value="ko-KR">Korea (Korean)</option>
                <option value="en-MY">Malaysia (English)</option>
                <option value="es-MX">Mexico (Spanish)</option>
                <option value="nl-NL">Netherlands (Dutch)</option>
                <option value="en-NZ">New Zealand (English)</option>
                <option value="no-NO">Norway (Norwegian)</option>
                <option value="zh-CN">People's Republic of China (Chinese)</option>
                <option value="pl-PL">Poland (Polish)</option>
                <option value="pt-PT">Portugal (Portuguese)</option>
                <option value="en-PH">Philippines (English)</option>
                <option value="ru-RU">Russia (Russian)</option>
                <option value="ar-SA">Saudi Arabia (Arabic)</option>
                <option value="en-ZA">South Africa (English)</option>
                <option value="es-ES">Spain (Spanish)</option>
                <option value="sv-SE">Sweden (Swedish)</option>
                <option value="fr-CH">Switzerland (French)</option>
                <option value="de-CH">Switzerland (German)</option>
                <option value="zh-TW">Taiwan (Traditional Chinese)</option>
                <option value="tr-TR">Turkey (Turkish)</option>
                <option value="en-GB">United Kingdom (English)</option>
                <option value="en-US" selected>United States (English)</option>
                <option value="es-US">United States (Spanish)</option>
            </select>
        </p>
        <p style="margin-left: 20px">Safe search: 
            <select id="safesearch">
                <option value="moderate" selected>Moderate</option>
                <option value="strict">Strict</option>
                <option value="off">off</option>
            </select>
        </p>
    </div>
</div>
```

## <a name="add-search-options-to-the-webpage"></a>検索オプションを Web ページに追加する

アプリケーションでは、[Query options] リンクによって制御される折りたたみ可能 `<div>` でリストを非表示にします。 [Query options] リンクをクリックすると `<div>` が展開し、クエリ オプションの確認と変更ができます。 [Query options] リンクをもう一度クリックすると、`<div>` が折りたたまれて非表示になります。 次のスニペットは、[Query options] リンクの `onclick` ハンドラーを示しています。 ハンドラーは、`<div>` が展開されているか、それとも折りたたまれているかを制御します。 このハンドラーを `<script>` セクションに追加します。 ハンドラーはデモ内のすべての折りたたみ可能 `<div>` セクションで使用されます。

```javascript
// Contains the toggle state of divs.
var divToggleMap = {};  // divToggleMap['foo'] = 0;  // 1 = show, 0 = hide


// Toggles between showing and hiding the specified div.
function expandCollapse(divToToggle) {
    var div = document.getElementById(divToToggle);

    if (divToggleMap[divToToggle] == 1) {   // if div is expanded
        div.style.display = "none";
        divToggleMap[divToToggle] = 0;
    }
    else {                                  // if div is collapsed
        div.style.display = "inline-block";
        divToggleMap[divToToggle] = 1;
    }
}
```

## <a name="call-the-onclick-handler"></a>`onclick` ハンドラーを呼び出す

次の `"Get insights"` ボタンを、本文内の options `<div>` の下に追加します。 このボタンを使用して呼び出しを開始できます。 ボタンがクリックされると、カーソルが待機状態を示す回転カーソルに変化し、`onclick` ハンドラーが呼び出されます。

```html
<p><input type="button" id="query" value="Get insights" onclick="document.body.style.cursor='wait'; handleQuery()" /></p>
```

ボタンの `onclick` ハンドラー `handleQuery()` を `<script>` タグに追加します。

## <a name="handle-the-query"></a>クエリを処理する

ハンドラー `handleQuery()` によって、サブスクリプション キーの存在と 32 文字の長さが確保され、画像が確実に選択されます。 また、以前のクエリからの分析情報があれば消去します。 その後、`sendRequest()` 関数を呼び出して、呼び出しを行います。

```javascript
function handleQuery() {
    var subscriptionKey = document.getElementById('key').value;

    // Make sure user provided a subscription key and image.
    // For this demo, the user provides the key but typically you'd
    // get it from secured storage.
    if (subscriptionKey.length !== 32) {
        alert("Subscription key length is not valid. Enter a valid key.");
        document.getElementById('key').focus();
        return;
    }

    var imagePath = document.getElementById('uploadImage').value;

    if (imagePath.length === 0)
    {
        alert("Please select an image to upload.");
        document.getElementById('uploadImage').focus();
        return;
    }

    var responseDiv = document.getElementById('responseSection');

    // Clear out the response from the last query.
    while (responseDiv.childElementCount > 0) {
        responseDiv.removeChild(responseDiv.lastChild);
    }

    // Send the request to Bing to get insights about the image.
    var f = document.getElementById('uploadImage').files[0];
    sendRequest(f, subscriptionKey);
}
```

## <a name="send-the-search-request"></a>検索要求を送信する

`sendRequest()` 関数では、エンドポイント URL をフォーマットし、`Ocp-Apim-Subscription-Key` ヘッダーをサブスクリプション キーに設定し、アップロードする画像のバイナリを追加し、応答ハンドラーを指定し、呼び出しを実行します。

```javascript
function sendRequest(file, key) {
    var market = document.getElementById('mkt').value;
    var safeSearch = document.getElementById('safesearch').value;
    var baseUri = `https://api.cognitive.microsoft.com/bing/v7.0/images/visualsearch?mkt=${market}&safesearch=${safeSearch}`;

    var form = new FormData();
    form.append("image", file);

    var request = new XMLHttpRequest();

    request.open("POST", baseUri);
    request.setRequestHeader('Ocp-Apim-Subscription-Key', key);
    request.addEventListener('load', handleResponse);
    request.send(form);
}
```

## <a name="get-and-handle-the-api-response"></a>API の応答を取得して処理する

`handleResponse()` 関数は Bing Visual Search の呼び出しからの応答を処理します。 呼び出しが成功した場合、この関数は、分析情報を含む個別のタグに JSON 応答を解析します。 次に、検索結果をページに追加します。 その後、アプリケーションによって、データが表示される量を管理するために、折りたたみ可能 `<div>` がタグごとに作成されます。 ハンドラーを `<script>` セクションに追加します。

```javascript
function handleResponse() {
    if(this.status !== 200){
        alert("Error calling Bing Visual Search. See console log for details.");
        console.log(this.responseText);
        return;
    }

    var tags = parseResponse(JSON.parse(this.responseText));
    var h4 = document.createElement('h4');
    h4.textContent = 'Bing internet search results';
    document.getElementById('responseSection').appendChild(h4);
    buildTagSections(tags);

    document.body.style.cursor = 'default'; // reset the wait cursor set by query insights button
}
```

### <a name="parse-the-response"></a>応答を解析する

`parseResponse` 関数により、`json.tags` が反復処理されて、JSON 応答がディクショナリ オブジェクトに変換されます。

```javascript
function parseResponse(json) {
    var dict = {};

    for (var i =0; i < json.tags.length; i++) {
        var tag = json.tags[i];

        if (tag.displayName === '') {
            dict['Default'] = JSON.stringify(tag);
        }
        else {
            dict[tag.displayName] = JSON.stringify(tag);
        }
    }

    return(dict);
}
```

### <a name="build-a-tag-section"></a>タグ セクションを構築する

`buildTagSections()` 関数では、解析された JSON タグを反復処理し、`buildDiv()` 関数を呼び出して各タグの `<div>` を構築します。 各タグはリンクとして表示されます。 リンクがクリックされると、タグが展開し、そのタグに関連付けられた分析情報が表示されます。 リンクをもう一度クリックすると、そのセクションは折りたたまれます。

```javascript
function buildTagSections(tags) {
    for (var tag in tags) {
        if (tags.hasOwnProperty(tag)) {
            var tagSection = buildDiv(tags, tag);
            document.getElementById('responseSection').appendChild(tagSection);
        }
    }  
}

function buildDiv(tags, tag) {
    var tagSection = document.createElement('div');
    tagSection.setAttribute('class', 'subSection');

    var link = document.createElement('a');
    link.setAttribute('href', '#');
    link.setAttribute('style', 'float: left;')
    link.text = tag;
    tagSection.appendChild(link);

    var contentDiv = document.createElement('div');
    contentDiv.setAttribute('id', tag);
    contentDiv.setAttribute('style', 'clear: left;')
    contentDiv.setAttribute('class', 'section');
    tagSection.appendChild(contentDiv);

    link.setAttribute('onclick', `expandCollapse("${tag}")`);
    divToggleMap[tag] = 0;  // 1 = show, 0 = hide

    addDivContent(contentDiv, tag, tags[tag]);

    return tagSection;
}
```

## <a name="display-the-search-results-in-the-webpage"></a>検索結果を Web ページに表示する

`buildDiv()` 関数では `addDivContent` 関数を呼び出して、各タグの折りたたみ可能 `<div>` の内容を構築します。

タグの内容には、そのタグに対する応答からの JSON が含まれます。 最初は JSON の先頭から 100 文字だけが表示されますが、JSON 文字列をクリックしてすべての JSON を表示することができます。 もう一度クリックすると、JSON 文字列は再び 100 文字に折りたたまれます。

次に、タグに見つかったアクション タイプを追加します。 アクション タイプごとに適切な関数を呼び出して、その分析情報を追加します。

```javascript
function addDivContent(div, tag, json) {

    // Adds the first 100 characters of the json that contains
    // the tag's data. The user can click the text to show the
    // full json. They can click it again to collapse the json.
    var para = document.createElement('p');
    para.textContent = String(json).substr(0, 100) + '...';
    para.setAttribute('title', 'click to expand');
    para.setAttribute('style', 'cursor: pointer;')
    para.setAttribute('data-json', json);
    para.addEventListener('click', function(e) {
        var json = e.target.getAttribute('data-json');

        if (e.target.textContent.length <= 103) {  // 100 + '...'
            e.target.textContent = json;
            para.setAttribute('title', 'click to collapse');
        }
        else {
            para.textContent = String(json).substr(0, 100) + '...';
            para.setAttribute('title', 'click to expand');
        }
    });
    div.appendChild(para); 

    var parsedJson = JSON.parse(json);

    // Loop through all the actions in the tag and display them.
    for (var j = 0; j < parsedJson.actions.length; j++) {
        var action = parsedJson.actions[j];

        var subSectionDiv = document.createElement('div');
        subSectionDiv.setAttribute('class', 'subSection');
        div.appendChild(subSectionDiv);

        var h4 = document.createElement('h4');
        h4.innerHTML = action.actionType;
        subSectionDiv.appendChild(h4);

        if (action.actionType === 'ImageResults') {
            addImageWithWebSearchUrl(subSectionDiv, parsedJson.image, action);
        }
        else if (action.actionType === 'DocumentLevelSuggestions') {
            addRelatedSearches(subSectionDiv, action.data.value);
        }
        else if (action.actionType === 'RelatedSearches') {
            addRelatedSearches(subSectionDiv, action.data.value);
        }
        else if (action.actionType === 'PagesIncluding') {
            addPagesIncluding(subSectionDiv, action.data.value);
        }
        else if (action.actionType === 'VisualSearch') {
            addRelatedImages(subSectionDiv, action.data.value);
        }
        else if (action.actionType === 'Recipes') {
            addRecipes(subSectionDiv, action.data.value);
        }
        else if (action.actionType === 'ShoppingSources') {
            addShopping(subSectionDiv, action.data.offers);
        }
        else if (action.actionType === 'ProductVisualSearch') {
            addProducts(subSectionDiv, action.data.value);
        }
        else if (action.actionType === 'TextResults') {
            addTextResult(subSectionDiv, action);
        }
        else if (action.actionType === 'Entity') {
            addEntity(subSectionDiv, action);
        }
    }
}
```

## <a name="display-insights-for-different-actions"></a>さまざまなアクションの分析情報を表示する

次の関数では、さまざまなアクションの分析情報が表示されます。 関数によって、クリックできる画像またはクリックできるリンクが提供され、クリックすると画像の詳細情報が含まれる Web ページが表示されます。 このページは、Bing.com または画像の元の Web サイトでホストされています。 分析情報のすべてはこのアプリケーションでは表示されません。 分析情報のすべての利用可能フィールドを確認するには、「[Images - Visual Search (画像 - Visual Search)](https://aka.ms/bingvisualsearchreferencedoc)」リファレンスを参照してください。

> [!NOTE]
> ページには最低限の分析情報を表示する必要があります。 詳細については、「[Bing Search API の利用と表示の要件](../bing-web-search/use-display-requirements.md)」を参照してください。

### <a name="relatedimages-insights"></a>RelatedImages 分析情報

`addRelatedImages()` 関数では、`RelatedImages` アクションの一覧を反復処理し、それぞれについて `<img>` タグを外部の `<div>` に追加することで、関連する画像をホストする各 Web サイトのタイトルが作成されます。

```javascript
    function addRelatedImages(div, images) {
        var length = (images.length > 10) ? 10 : images.length;

        // Set the title to the website that hosts the image. The title displays
        // when the user hovers over the image.

        // Make the image clickable. If the user clicks the image, they're taken
        // to the image in Bing.com.

        for (var j = 0; j < length; j++) {
            var img = document.createElement('img');
            img.setAttribute('src', images[j].thumbnailUrl + '&w=120&h=120');
            img.setAttribute('style', 'margin: 20px 20px 0 0; cursor: pointer;');
            img.setAttribute('title', images[j].hostPageDisplayUrl);
            img.setAttribute('data-webSearchUrl', images[j].webSearchUrl)

            img.addEventListener('click', function(e) {
                var url = e.target.getAttribute('data-webSearchUrl');
                window.open(url, 'foo');
            })

            div.appendChild(img);
        }
    }
```

### <a name="pagesincluding-insights"></a>PagesIncluding 分析情報

`addPagesIncluding()` 関数では、`PagesIncluding` アクションの一覧を反復処理し、それぞれについて `<img>` タグを外部の `<div>` に追加することで、アップロードされた画像をホストする各 Web サイトのリンクが作成されます。

```javascript

    // Display links to the first 5 webpages that include the image.
    // TODO: Add 'more' link in case the user wants to see all of them.
    function addPagesIncluding(div, pages) {
        var length = (pages.length > 5) ? 5 : pages.length;

        for (var j = 0; j < length; j++) {
            var page = document.createElement('a');
            page.text = pages[j].name;
            page.setAttribute('href', pages[j].hostPageUrl);
            page.setAttribute('style', 'margin: 20px 20px 0 0');
            page.setAttribute('target', '_blank')
            div.appendChild(page);

            div.appendChild(document.createElement('br'));
        }
    }
```

### <a name="relatedsearches-insights"></a>RelatedSearches 分析情報

`addRelatedSearches()` 関数では、`RelatedSearches` アクションの一覧を反復処理し、それぞれについて `<img>` タグを外部の `<div>` に追加することで、画像をホストする Web サイトのリンクが作成されます。

```javascript

    // Display the first 10 related searches. Include a link with the image
    // that when clicked, takes the user to Bing.com and displays the 
    // related search results.
    // TODO: Add 'more' link in case the user wants to see all of them.
    function addRelatedSearches(div, relatedSearches) {
        var length = (relatedSearches.length > 10) ? 10 : relatedSearches.length;

        for (var j = 0; j < length; j++) {
            var childDiv = document.createElement('div');
            childDiv.setAttribute('class', 'stackLink');
            div.appendChild(childDiv);

            var img = document.createElement('img');
            img.setAttribute('src', relatedSearches[j].thumbnail.url + '&w=120&h=120');
            img.setAttribute('style', 'margin: 20px 20px 0 0;');
            childDiv.appendChild(img);

            var relatedSearch = document.createElement('a');
            relatedSearch.text = relatedSearches[j].displayText;
            relatedSearch.setAttribute('href', relatedSearches[j].webSearchUrl);
            relatedSearch.setAttribute('target', '_blank');
            childDiv.appendChild(relatedSearch);

        }
    }
```

### <a name="recipes-insights"></a>Recipes 分析情報

`addRecipes()` 関数では、`Recipes` アクションの一覧を反復処理し、それぞれについて `<img>` タグを外部の `<div>` に追加することで、返される各レシピのリンクが作成されます。

```javascript
    // Display links to the first 10 recipes. Include the recipe's rating,
    // if available.
    // TODO: Add 'more' link in case the user wants to see all of them.
    function addRecipes(div, recipes) {
        var length = (recipes.length > 10) ? 10 : recipes.length;

        for (var j = 0; j < length; j++) {
            var para = document.createElement('p');

            var recipe = document.createElement('a');
            recipe.text = recipes[j].name;
            recipe.setAttribute('href', recipes[j].url);
            recipe.setAttribute('style', 'margin: 20px 20px 0 0');
            recipe.setAttribute('target', '_blank')
            para.appendChild(recipe);

            if (recipes[j].hasOwnProperty('aggregateRating')) {
                var span = document.createElement('span');
                span.textContent = 'rating: ' + recipes[j].aggregateRating.text;
                para.appendChild(span);
            }

            div.appendChild(para);
        }
    }
```

### <a name="shopping-insights"></a>Shopping 分析情報

`addShopping()` 関数では、`RelatedImages` アクションの一覧を反復処理し、それぞれについて `<img>` タグを外部の `<div>` に追加することで、返されるショッピング結果のリンクが作成されます。

```javascript
    // Display links for the first 10 shopping offers.
    // TODO: Add 'more' link in case the user wants to see all of them.
    function addShopping(div, offers) {
        var length = (offers.length > 10) ? 10 : offers.length;

        for (var j = 0; j < length; j++) {
            var para = document.createElement('p');

            var offer = document.createElement('a');
            offer.text = offers[j].name;
            offer.setAttribute('href', offers[j].url);
            offer.setAttribute('style', 'margin: 20px 20px 0 0');
            offer.setAttribute('target', '_blank')
            para.appendChild(offer);

            var span = document.createElement('span');
            span.textContent = 'by ' + offers[j].seller.name + ' | ' + offers[j].price + ' ' + offers[j].priceCurrency;
            para.appendChild(span);

            div.appendChild(para);
        }
    }
```

### <a name="products-insights"></a>Products 分析情報

`addProducts()` 関数では、`Products` アクションの一覧を反復処理し、それぞれについて `<img>` タグを外部の `<div>` に追加することで、返される製品の結果のリンクが作成されます。

```javascript

    // Display the first 10 related products. Display a clickable image of the
    // product that takes the user to Bing.com search results for the product.
    // If there are any offers associated with the product, provide links to the offers.
    // TODO: Add 'more' link in case the user wants to see all of them.
    function addProducts(div, products) {
        var length = (products.length > 10) ? 10 : products.length;

        for (var j = 0; j < length; j++) {
            var childDiv = document.createElement('div');
            childDiv.setAttribute('class', 'stackLink');
            div.appendChild(childDiv);

            var img = document.createElement('img');
            img.setAttribute('src', products[j].thumbnailUrl + '&w=120&h=120');
            img.setAttribute('title', products[j].name);
            img.setAttribute('style', 'margin: 20px 20px 0 0; cursor: pointer;');
            img.setAttribute('data-webSearchUrl', products[j].webSearchUrl)
            img.addEventListener('click', function(e) {
                var url = e.target.getAttribute('data-webSearchUrl');
                window.open(url, 'foo');
            })
            childDiv.appendChild(img);

            if (products[j].insightsMetadata.hasOwnProperty('aggregateOffer')) {
                if (products[j].insightsMetadata.aggregateOffer.offerCount > 0) {
                    var offers = products[j].insightsMetadata.aggregateOffer.offers;

                    // Show all the offers. Not all markets provide links to offers.
                    for (var i = 0; i < offers.length; i++) {  
                        var para = document.createElement('p');

                        var offer = document.createElement('a');
                        offer.text = offers[i].name;
                        offer.setAttribute('href', offers[i].url);
                        offer.setAttribute('style', 'margin: 20px 20px 0 0');
                        offer.setAttribute('target', '_blank')
                        para.appendChild(offer);

                        var span = document.createElement('span');
                        span.textContent = 'by ' + offers[i].seller.name + ' | ' + offers[i].price + ' ' + offers[i].priceCurrency;
                        para.appendChild(span);

                        childDiv.appendChild(para);
                    }
                }
                else {  // Otherwise, just show the lowest price that Bing found.
                    var offer = products[j].insightsMetadata.aggregateOffer;

                    var para = document.createElement('p');
                    para.textContent = `${offer.name} | ${offer.lowPrice} ${offer.priceCurrency}`; 

                    childDiv.appendChild(para);
                }
            }
        }
    }
```

### <a name="textresult-insights"></a>TextResult 分析情報

`addTextResult()` 関数では、画像内で認識されたすべてのテキストが表示されます。

```javascript

    function addTextResult(div, action) {
        var text = document.createElement('p');
        text.textContent = action.displayName;
        div.appendChild(text);
    }
```

`addEntity()` 関数では、ユーザーが画像内のエンティティ型 (検出された場合) の詳細を確認できる Bing.com へのリンクが表示されます。

```javascript
    // If the image is of a person, the tag might include an entity
    // action type. Display a link that takes the user to Bing.com
    // where they can get details about the entity.
    function addEntity(div, action) {
        var entity = document.createElement('a');
        entity.text = action.displayName;
        entity.setAttribute('href', action.webSearchUrl);
        entity.setAttribute('style', 'margin: 20px 20px 0 0');
        entity.setAttribute('target', '_blank');
        div.appendChild(entity);
    }
```

`addImageWithWebSearchUrl()` 関数では、`<div>` のクリックできる画像が表示され、ユーザーがクリックすると Bing.com 上の検索結果が表示されます。

```javascript
    function addImageWithWebSearchUrl(div, image, action) {
        var img = document.createElement('img');
        img.setAttribute('src', image.thumbnailUrl + '&w=120&h=120');
        img.setAttribute('style', 'margin: 20px 20px 0 0; cursor: pointer;');
        img.setAttribute('data-webSearchUrl', action.webSearchUrl);
        img.addEventListener('click', function(e) {
            var url = e.target.getAttribute('data-webSearchUrl');
            window.open(url, 'foo');
        })
        div.appendChild(img);
    }

```

## <a name="add-a-css-style"></a>CSS スタイルを追加する

次の `<style>` セクションを `<head>` タグに追加して、Web ページのレイアウトを整理します。

```html
        <style>

            .thumb {
                height: 75px;
                border: 1px solid #000;
            }

            .stackLink {
                width:180px;
                min-height:210px;
                display:inline-block;
            }
            .stackLink a {
                float:left;
                clear:left;
            }

            .section {
                float:left;
                display:none;
            }

            .subSection {
                clear:left;
                float:left;
            }

        </style>
```

## <a name="next-steps"></a>次の手順

>[!div class="nextstepaction"]
> [チュートリアル:ImageInsightsToken を使用して以前の検索から似た画像を見つける](./tutorial-visual-search-insights-token.md)
