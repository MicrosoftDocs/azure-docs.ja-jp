---
title: アプリから API を呼び出す - Custom Decision Service
titlesuffix: Azure Cognitive Services
description: スマートフォン アプリから Custom Decision Service API を呼び出す方法。
services: cognitive-services
author: slivkins
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-decision-service
ms.topic: conceptual
ms.date: 05/10/2018
ms.author: slivkins
ms.openlocfilehash: 0e5c99aae61fb927ea7f101bab74d661a747f88b
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/07/2019
ms.locfileid: "55870012"
---
# <a name="call-api-from-an-app"></a>アプリから API を呼び出す

スマートフォン アプリから Azure Custom Decision Service API を呼び出します。 この記事では、いくつかの基本的なオプションについて概要を説明します。

必ず最初に[アプリケーションを登録](custom-decision-service-get-started-register.md)してください。

スマートフォン アプリから Custom Decision Service API を呼び出すとき、2 つの呼び出しがあります。Ranking API を呼び出してコンテンツの順位付き一覧を取得する場合と Reward API を呼び出してリワードを報告する場合です。 [cURL](https://en.wikipedia.org/wiki/CURL) にサンプル呼び出しがあります。

詳細については、「[API](custom-decision-service-api-reference.md)」リファレンスを参照してください。

Ranking API の呼び出しから始めます。 ファイル `<request.json>` を作成します。このファイルにはアクション セット ID が入ります。 この ID は、ポータルで入力した、該当する RSS または Atom フィードの名前になります。

```json
{"decisions":
     [{ "actionSets":[{"id":{"id":"<actionSetId>"}}] }]}
```

アクション セットの多くは次のように指定できます。

```json
{"decisions":
    [{ "actionSets":[{"id":{"id":"<actionSetId1>"}},
                     {"id":{"id":"<actionSetId2>"}}] }]}
```

この JSON ファイルは、順位付け要求の一部として送信されます。

```shell
curl -d @<request.json> -X POST https://ds.microsoft.com/api/v2/<appId>/rank --header "Content-Type: application/json"
```

ここで、`<appId>` は、ポータルで登録されている、アプリケーションの名前になります。 順序付けられた一連のコンテンツ アイテムが届くはずです。これはアプリケーションで表示できます。 戻り値の例は次のようになります。

```json
[{ "ranking":[{"id":"actionId3"}, {"id":"actionId1"}, {"id":"actionId2"}],
   "eventId":"<opaque event string>",
   "appId":"<your app id>",
   "rewardAction":"actionId3",
   "actionSets":[{"id":"<actionSetId1>","lastRefresh":"2017-04-29T22:34:25.3401438Z"},
                 {"id":"<actionSetId2>","lastRefresh":"2017-04-30T22:34:25.3401438Z"}]}]
```

戻り値の最初の部分は、順序付けられたアクションのリストになっています。そのアクション ID で指定されています。 記事の場合、アクション ID は URL です。 要求全体には一意の `<eventId>` も与えられます。これはシステム生成されるものです。

後で、このイベントから最初のコンテンツ アイテム (`<actionId3>`) にクリックが観察されたかどうかを指定できます。 それから、Reward API を介して次のような別の要求で Custom Decision Service にこの `<eventId>` のリワードを報告できます。

```shell
curl -v https://ds.microsoft.com/api/v2/<appId>/reward/<eventId> -X POST
```

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
