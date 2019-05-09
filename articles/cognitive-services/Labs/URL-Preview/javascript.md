---
title: クイック スタート:Project URL Preview (JavaScript)
titlesuffix: Azure Cognitive Services
description: JavaScript で Bing URL Preview API の使用をすぐに開始するためのスクリプト サンプル。
services: cognitive-services
author: mikedodaro
manager: nitinme
ms.service: cognitive-services
ms.subservice: url-preview
ms.topic: quickstart
ms.date: 03/16/2018
ms.author: rosh
ms.openlocfilehash: 1f3c37e770c2edd76bd299771648b2de29d42dd9
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/07/2019
ms.locfileid: "55873650"
---
# <a name="quickstart-url-preview-in-javascript"></a>クイック スタート:JavaScript による URL Preview 

次のシングルページ アプリケーションでは、JavaScript を使用して SwiftKey サイト (https://swiftkey.com/en) の URL プレビューを作成します。 

## <a name="prerequisites"></a>前提条件

[Cognitive Services Labs](https://labs.cognitive.microsoft.com/en-us/project-url-preview) で無料試用版のアクセス キーを取得します

## <a name="code-scenario"></a>シナリオのコードを書く
次の JavaScript の例には、プレビューする URL をユーザーが入力するためのテキスト ボックス入力オブジェクトが含まれています。  ユーザーが **[Preview]** ボタンをクリックすると、onclick メソッドが、**UrlPreview** エンドポイントへの Web 要求を生成する `getPreview` にルーティングされます。

このコードでは、*XMLHttpRequest* を作成し、*Ocp-Apim-Subscription-Key* ヘッダーとキーを追加し、要求を送信します。  さらに、応答を処理する非同期イベント ハンドラーを追加します。

応答が正常に返された場合、ハンドラーは、応答の JSON テキストをページの `demo` 段落に割り当てます。 その他の応答要素は、表示のために後続の段落に設定されます。

**生の JSON 応答**

```
{
  "_type": "WebPage",
  "name": "SwiftKey - Smart prediction technology for easier mobile typing",
  "url": "https://swiftkey.com/en",
  "description": "Discover the best Android and iPhone and iPad apps for faster, easier typing with emoji, colorful themes and more - download SwiftKey Keyboard free today.",
  "isFamilyFriendly": true,
  "primaryImageOfPage": {
    "contentUrl": "https://swiftkey.com/images/og/default.jpg"
  }
}

```

**実行中のデモ**

![JavaScript の URL プレビューの例](./media/java-script-demo.png)

## <a name="running-the-application"></a>アプリケーションの実行

アプリケーションを実行するには:

1. `YOUR-SUBSCRIPTION-KEY` 値を、お使いのサブスクリプションで有効なアクセス キーに置き換えます。
2. HTML とスクリプトを、拡張子が .html のファイルに保存します。
3. ブラウザーで Web ページを実行します。
4. 既存の URL を使用するか、テキスト ボックスに別の URL を入力します。
5. **[Preview]** ボタンをクリックします。

**ソース コード:**

```
<!DOCTYPE html>

<html lang="en" xmlns="http://www.w3.org/1999/xhtml">
<head>
    <meta charset="utf-8" />
    <title>urlPreview Demo</title>
</head>
<body>
    <h3>URL Preview Demo</h3>

    Page to preview: <input type="url" id="myURL" value="https://swiftkey.com/en">
    <button onclick="getPreview()">Preview</button>

    <p id="demo"></p>
    <br />
    <p id="jsonDesc"></p>
    <p><a id="familyFriendly"></a></p>
    <a id="contentUrl"></a>
    <p />
    <img id="jsonImage" />

    <script>

        var BING_ENDPOINT = "https://api.labs.cognitive.microsoft.com/urlpreview/v7.0/search"; 
        var key = "YOUR-SUBSCRIPTION-KEY";
        var xhr;

        function getPreview() {
            xhr = new XMLHttpRequest();

            var queryUrl = BING_ENDPOINT + "?q=" +
                encodeURIComponent(document.getElementById("myURL").value);
            xhr.open('GET', queryUrl, true);
            xhr.setRequestHeader("Ocp-Apim-Subscription-Key", key);

            xhr.send();

            xhr.addEventListener("readystatechange", processRequest, false);
        }

        function processRequest(e) {

            if (xhr.readyState == 4 && xhr.status == 200) {
                document.getElementById("demo").innerHTML = xhr.responseText;
                var obj = JSON.parse(xhr.responseText);
                document.getElementById("jsonDesc").innerHTML = obj.description;
                document.getElementById("familyFriendly").innerHTML = "Family Friendly: " + obj.isFamilyFriendly;
                document.getElementById("contentUrl").innerHTML = obj.url;
                document.getElementById("contentUrl").href = obj.url;
                document.getElementById("jsonImage").width = 350;
                document.getElementById("jsonImage").src = obj.primaryImageOfPage.contentUrl;

            }

        }

    </script>

</body>
</html>

```

## <a name="next-steps"></a>次の手順
- [C# のクイック スタート](csharp.md)
- [Java のクイック スタート](java-quickstart.md)
- [Node のクイック スタート](node-quickstart.md)
- [Python のクイック スタート](python-quickstart.md)
