---
title: " シングルページ Web アプリを構築する - Bing Visual Search"
titleSuffix: Azure Cognitive Services
description: Bing Visual Search API をシングルページ Web アプリケーションに統合する方法を説明します。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-visual-search
ms.topic: article
ms.date: 04/05/2019
ms.author: aahi
ms.openlocfilehash: 084aad5540a2bd56d98e343639a45c16f786e599
ms.sourcegitcommit: 1a19a5845ae5d9f5752b4c905a43bf959a60eb9d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/11/2019
ms.locfileid: "59496558"
---
# <a name="create-a-visual-search-single-page-web-app"></a>Visual Search のシングルページ Web アプリを作成する

Bing Visual Search API は、画像に関する分析情報を返します。 画像をアップロードするか、画像の URL を指定することができます。 分析情報とは、視覚的に似ている画像、ショッピング ソース、その画像が含まれる Web ページなどです。 Bing Visual Search API によって返される分析情報は、Bing.com/images に表示される情報と似ています。

このチュートリアルでは、Bing Image Search API 用にシングルページ Web アプリを拡張する方法について説明します。 そのチュートリアルを見たり、ここで使用されるソース コードを取得したりするには、「[チュートリアル:Bing Image Search API を使用して単一ページのアプリを作成する](../Bing-Image-Search/tutorial-bing-image-search-single-page-app.md)」をご覧ください。

このアプリケーションの完全なソース コード (Bing Visual Search API を使用するように拡張した後のもの) は、[GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/Tutorials/Bing-Visual-Search/BingVisualSearchApp.html) で入手できます。

## <a name="prerequisites"></a>前提条件

[!INCLUDE [cognitive-services-bing-visual-search-signup-requirements](../../../includes/cognitive-services-bing-visual-search-signup-requirements.md)]

## <a name="call-the-bing-visual-search-api-and-handle-the-response"></a>Bing Visual Search API を呼び出して応答を処理する

Bing Image Search のチュートリアルを編集し、`<script>` 要素の最後 (終了 `</script>` タグの前) に、次のコードを追加します。 次のコードでは、API からの Visual Search の応答を処理し、結果を反復処理して、それらを表示します。

``` javascript
function handleVisualSearchResponse(){
    if(this.status !== 200){
        console.log(this.responseText);
        return;
    }
    let json = this.responseText;
    let response = JSON.parse(json);
    for (let i =0; i < response.tags.length; i++) {
        let tag = response.tags[i];
        if (tag.displayName === '') {
            for (let j = 0; j < tag.actions.length; j++) {
                let action = tag.actions[j];
                if (action.actionType === 'VisualSearch') {
                    let html = '';
                    for (let k = 0; k < action.data.value.length; k++) {
                        let item = action.data.value[k];
                        let height = 120;
                        let width = Math.max(Math.round(height * item.thumbnail.width / item.thumbnail.height), 120);
                        html += "<img src='"+ item.thumbnailUrl + "&h=" + height + "&w=" + width + "' height=" + height + " width=" + width + "'>";
                    }
                    showDiv("insights", html);
                    window.scrollTo({top: document.getElementById("insights").getBoundingClientRect().top, behavior: "smooth"});
                }
            }
        }
    }
}
```

次のコードでは、検索要求を API に送信し、イベント リスナーを使用して `handleVisualSearchResponse()` を呼び出します。

```javascript
function bingVisualSearch(insightsToken){
    let visualSearchBaseURL = 'https://api.cognitive.microsoft.com/bing/v7.0/images/visualsearch',
        boundary = 'boundary_ABC123DEF456',
        startBoundary = '--' + boundary,
        endBoundary = '--' + boundary + '--',
        newLine = "\r\n",
        bodyHeader = 'Content-Disposition: form-data; name="knowledgeRequest"' + newLine + newLine;

    let postBody = {
        imageInfo: {
            imageInsightsToken: insightsToken
        }
    }
    let requestBody = startBoundary + newLine;
    requestBody += bodyHeader;
    requestBody += JSON.stringify(postBody) + newLine + newLine;
    requestBody += endBoundary + newLine;

    let request = new XMLHttpRequest();

    try {
        request.open("POST", visualSearchBaseURL);
    } 
    catch (e) {
        renderErrorMessage("Error performing visual search: " + e.message);
    }
    request.setRequestHeader("Ocp-Apim-Subscription-Key", getSubscriptionKey());
    request.setRequestHeader("Content-Type", "multipart/form-data; boundary=" + boundary);
    request.addEventListener("load", handleVisualSearchResponse);
    request.send(requestBody);
}
```

## <a name="capture-insights-token"></a>分析情報トークンをキャプチャする

次のコードを `searchItemsRenderer` オブジェクトに追加します。 このコードは、クリックされたときに `bingVisualSearch` 関数を呼び出す **find similar** リンクを追加します。 この関数は引数として `imageInsightsToken` を受け取ります。

``` javascript
html.push("<a href='javascript:bingVisualSearch(\"" + item.imageInsightsToken + "\");'>find similar</a><br>");
```

## <a name="display-similar-images"></a>類似した画像を表示する

次の HTML コードを 601 行目に追加します。 このマークアップ コードでは、Bing Visual Search API 呼び出しの結果を表示するための要素が追加されます。

``` html
<div id="insights">
    <h3><a href="#" onclick="return toggleDisplay('_insights')">Similar</a></h3>
    <div id="_insights" style="display: none"></div>
</div>
```

新しい JavaScript コードと HTML 要素をすべて配置すると、検索結果が **find similar** リンクと共に表示されます。 このリンクをクリックすると、選択した画像に類似した画像が **[類似]** セクションに表示されます。 場合によっては、画像を表示するために **[類似]** セクションを展開する必要があります。

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [チュートリアル:C# 用の Bing Visual Search SDK で画像をトリミングする](tutorial-visual-search-crop-area-results.md)
