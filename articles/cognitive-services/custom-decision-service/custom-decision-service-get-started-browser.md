---
title: ブラウザーから API を呼び出す - Custom Decision Service
titlesuffix: Azure Cognitive Services
description: ブラウザーから直接 Azure Custom Decision Service に API を呼び出すことで、Web ページを最適化する方法。
services: cognitive-services
author: slivkins
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-decision-service
ms.topic: conceptual
ms.date: 05/09/2018
ms.author: slivkins
ms.openlocfilehash: 2b356e2f0fe9235d49dffa7417cd3894059f9caf
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/07/2019
ms.locfileid: "55867564"
---
# <a name="call-api-from-a-browser"></a>ブラウザーから API を呼び出す

この記事は、ブラウザーから直接 Azure Custom Decision Service API を呼び出す場合に役立ちます。

必ず最初に[アプリケーションを登録](custom-decision-service-get-started-register.md)してください。

それでは始めましょう。 アプリケーションは、いくつかの記事のページにリンクする、フロント ページが示されるようにモデル化されています。 フロント ページでは Custom Decision Service を使用して、その記事のページの順序を指定します。 フロント ページの HTML 見出しに次のコードを挿入します。

```html
// Define the "callback function" to render UI
<script> function callback(data) { … } </script>

// call Ranking API, after callback() is defined
<script src="https://ds.microsoft.com/api/v2/<appId>/rank/<actionSetId>" async></script>
```

`data` 引数には、レンダリングされる URL のランキングが含まれます。 詳細については、[API](custom-decision-service-api-reference.md) リファレンスに関するページを参照してください。

トップ記事でのユーザーのクリックを処理するには、フロント ページで次のコードを呼び出します。

```javascript
// call Reward API to report a click
$.ajax({
    type: "POST",
    url: '//ds.microsoft.com/api/v2/<appId>/reward/' + data.eventId,
    contentType: "application/json" })
```

ここでは、`data` は `callback()` 関数の引数となります。 実装例については、こちらの[チュートリアル](custom-decision-service-tutorial-news.md#use-the-apis)を参照してください。

最後に、Action Set API を提供する必要があります。この API は、Custom Decision Service で考慮される記事 (アクション) のリストを返します。 次に示すように、この API を RSS フィードとして実装します。

```xml
<rss version="2.0">
<channel>
   <item>
      <title><![CDATA[title (possibly with url) ]]></title>
      <link>url</link>
      <pubDate>Thu, 27 Apr 2017 16:30:52 GMT</pubDate>
    </item>
   <item>
       ....
   </item>
</channel>
</rss>
```

ここでは、各トップレベルの `<item>` 要素で記事が記述されます。 `<link>` は必須であり、Custom Decision Service でアクション ID として使用されます。 15 件を超える記事がある場合は、(標準の RSS 形式で) `<date>` を指定します。 15 件の最新の記事が使用されます。 `<title>` は省略可能であり、記事の特に注目すべき内容を伝えるために使用されます。

## <a name="next-steps"></a>次の手順

* [チュートリアル](custom-decision-service-tutorial-news.md)に従って、より詳細な例を見ていきます。
* 提供される機能の詳細については、[API](custom-decision-service-api-reference.md) リファレンスに関するページを参照してください。
