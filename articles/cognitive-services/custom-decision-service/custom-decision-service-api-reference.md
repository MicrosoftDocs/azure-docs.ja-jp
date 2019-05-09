---
title: API リファレンス - Custom Decision Service
titlesuffix: Azure Cognitive Services
description: Custom Decision Service の完全な API ガイド。
services: cognitive-services
author: slivkins
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-decision-service
ms.topic: conceptual
ms.date: 05/11/2018
ms.author: slivkins
ms.openlocfilehash: be9966f5d8e8d94aa3f49aac91b35b105195b108
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/07/2019
ms.locfileid: "57552063"
---
# <a name="api"></a>API

Azure Custom Decision Service では、決定ごとに呼び出される 2 種類の API が提供されています。[Ranking API](#ranking-api) はアクションのランキングを入力し、[Reward API](#reward-api) は報酬を出力します。 さらに、ユーザーは Azure Custom Decision Service に対するアクションを指定する [Action Set API](#action-set-api-customer-provided) を提供します。 この記事では、これら 3 つの API について説明します。 以下では、Custom Decision Service による記事のランキングの最適化を示すために一般的なシナリオを使用します。

## <a name="ranking-api"></a>Ranking API

Ranking API は、標準の [JSONP](https://en.wikipedia.org/wiki/JSONP) スタイルの通信方式を使用して、待ち時間を最適化し、[同一オリジン ポリシー](https://en.wikipedia.org/wiki/Same-origin_policy)をバイパスします。 後者は、JavaScript がページのオリジンの外部からデータをフェッチするのを禁止します。

(記事のカスタマイズされたリストが表示される) フロント ページの HTML の先頭に次のスニペットを挿入します。

```html
// define the "callback function" to render UI
<script> function callback(data) { … } </script>
// "data" provides the ranking of URLs, see example below.

// call to Ranking API
<script src="https://ds.microsoft.com/api/v2/<appId>/rank/<actionSetId>?<parameters>" async></script>
// action set id is the name of the corresponding RSS/Atom feed.

// same call with multiple action sets:
// <script src="https://ds.microsoft.com/api/v2/<appId>/rank/<A1>/<A2>/.../<An>?<parameters>" async></script>
```

> [!IMPORTANT]
> Ranking API を呼び出す前に、コールバック関数を定義する必要があります。

> [!TIP]
> 待ち時間を短縮するため、Ranking API は、`https://ds.microsoft.com/api/v2/<appId>/rank/*` のように、HTTPS ではなく HTTP を使用して公開されます。
> ただし、フロント ページが HTTPS で提供される場合は、HTTPS エンドポイントを使用する必要があります。

パラメーターが使用されない場合、Ranking API からの HTTP 応答は JSONP 形式の文字列です。

```json
callback({
   "ranking":[{"id":"url1"}, {"id":"url2"}, {"id":"url3"}],
   "eventId":"<opaque event string>",
   "appId":"<your app id>",
   "actionSets":[{"id":"<A1>","lastRefresh":"2017-04-29T22:34:25.3401438Z"},
                 {"id":"<A2>","lastRefresh":"2017-04-30T22:34:25.3401438Z"}]});
```

その場合、ブラウザーは `callback()` 関数の呼び出しとしてこの文字列を実行します。

上記の例でのコールバック関数へのパラメーターには、次のスキーマがあります。

- `ranking` は、表示される URL のランキングを提供します。
- `eventId` は、このランキングを対応するクリックと一致させるために、Custom Decision Service が内部的に使用します。
- `appId` により、コールバック関数は同じ Web ページで実行する Custom Decision Service の複数のアプリケーションを区別できます。
- `actionSets` は、Ranking API の呼び出しで使用されている各アクション セットと、最後に成功した更新の UTC タイムスタンプのリストです。 Custom Decision Service は、アクション セットのフィードを定期的に更新します。 たとえば、アクション セットの一部が最新でない場合、コールバック関数は既定のランキングにフォールバックすることが必要な場合があります。

> [!IMPORTANT]
> 指定されたアクション セットが処理され、場合によっては除去されて、記事の既定のランキングが作成されます。 その後、既定のランキングは順序を変更されて、HTTP 応答で返されます。 既定のランキングは次のように定義されます。
>
> - 各アクション セット内で、記事は最新の 15 件に絞り込まれます (15 件より多く返された場合)。
> - 複数のアクション セットが指定されている場合は、API の呼び出しと同じ順序でマージされます。 各アクション セット内では、記事の元の順序が保持されます。 重複は、早いコピーを優先して削除されます。
> - 記事のマージされた一覧から、最初の `n` 件が残されます。既定では `n=20` です。

### <a name="ranking-api-with-parameters"></a>パラメーターのある Ranking API

Ranking API では次のパラメーターを使用できます。

- `details=1` および `details=2` は、`ranking` にリストされている各記事についての詳細を挿入します。
- `limit=<n>` は、既定のランキング内の記事の最大数を指定します。 `n` は、`2` から `30` の範囲内でなければなりません (そうでない場合は、それぞれ `2` または `30` に丸められます)。
- `dnt=1` はユーザー Cookie を無効にします。

パラメーターは、標準のクエリ文字列構文で組み合わせることができます (例: `details=2&dnt=1`)。

> [!IMPORTANT]
> ヨーロッパでの既定の設定は、顧客が Cookie バナーに同意するまでは `dnt=1` でなければなりません。 また、未成年を対象とする Web サイトでもこれを既定の設定にする必要があります。 詳しくは、[使用条件](https://www.microsoft.com/cognitive-services/en-us/legal/CognitiveServicesTerms20160804)に関するページをご覧ください。

各記事の `guid` が Action Set API によって提供されている場合、`details=1` 要素はそれを挿入します。 HTTP 応答:

```json
callback({
   "ranking":[{"id":"url1","details":[{"guid":"123"}]},
              {"id":"url2","details":[{"guid":"456"}]},
              {"id":"url3","details":[{"guid":"789"}]}],
   "eventId":"<opaque event string>",
   "appId":"<your app id>",
   "actionSets":[{"id":"<A1>","lastRefresh":"timeStamp1"},
                 {"id":"<A2>","lastRefresh":"timeStamp2"}]});
```

`details=2` 要素は、Custom Decision Service が記事の SEO メタタグの[特性コード](https://github.com/Microsoft/mwt-ds/tree/master/Crawl)から抽出する場合があるその他の詳細を追加します。

- `<meta property="og:title" content="..." />`、`<meta property="twitter:title" content="..." />`、または `<title>...</title>` からの `title`
- `<meta property="og:description" ... />`、`<meta property="twitter:description" content="..." />`、または `<meta property="description" content="..." />` からの `description`
- `<meta property="og:image" content="..." />` からの `image`
- `<meta name=”microsoft:ds_id” content="..." />` からの `ds_id`

HTTP 応答:

```json
callback({
   "ranking":[{"id":"url1","details":<details>},
              {"id":"url2","details":<details>},
              {"id":"url3","details":<details>}],
   "eventId":"<opaque event string>",
   "appId":"<your app id>",
   "actionSets":[{"id":"<A1>","lastRefresh":"timeStamp1"},
                 {"id":"<A2>","lastRefresh":"timeStamp2"}]});
```

`<details>` 要素:

```json
[{"guid":"123"}, {"description":"some text", "ds_id":"234", "image":"ImageUrl1", "title":"some text"}]
```

## <a name="reward-api"></a>Reward API

Custom Decision Service は、一番上のスロットでのクリックのみを使用します。 1 回のクリックが、1 つの報酬として解釈されます。 クリックされない場合、報酬は 0 と解釈されます。 クリックはイベント ID を使用して対応するランキングと照合されます。イベント ID は、[Ranking API](#ranking-api) の呼び出しによって生成されます。 必要な場合は、セッション Cookie を使用してイベント ID を渡すことができます。

一番上のスロットのクリックを処理するには、フロント ページに次のコードを配置します。

```javascript
$.ajax({
    type: "POST",
    url: '//ds.microsoft.com/api/v2/<appId>/reward/' + data.eventId,
    contentType: "application/json" })
```

ここで、`data` は、前に説明したように、`callback()` 関数への引数です。 クリック処理コードで `data` を使用するには、注意が必要です。 [こちらのチュートリアル](custom-decision-service-tutorial-news.md#use-the-apis)で例が示されています。

テストだけの場合は、[cURL](https://en.wikipedia.org/wiki/CURL) を使用して Reward API を呼び出すことができます。

```sh
curl -v https://ds.microsoft.com/api/v2/<appId>/reward/<eventId> -X POST -d 1 -H "Content-Type: application/json"
```

結果としては、200 (OK) の HTTP 応答が予想されます。 このイベントに対する報酬 1 をログで確認できます (Azure ストレージ アカウント キーがポータルで提供されている場合)。

## <a name="action-set-api-customer-provided"></a>Action Set API (ユーザーが提供)

上位レベルでは、Action Set API は記事 (アクション) のリストを返します。 各記事は、その URL と (必要に応じて) 記事のタイトルと公開日によって指定されます。 ポータルでは複数のアクション セットを指定できます。 アクション セットごとに、個別の URL として、異なる Action Set API を使用する必要があります。

各 Action Set API は、RSS フィードと Atom フィードの 2 つの方法で実装できます。 いずれも標準に準拠し、正しい XML を返す必要があります。 RSS の場合の例を次に示します。

```xml
<rss version="2.0">
<channel>
   <item>
      <title><![CDATA[title (possibly with url) ]]></title>
      <link>url</link>
      <guid>guid (could be a your internal database id)</guid>
      <pubDate>date</pubDate>
    </item>
   <item>
       ....
   </item>
</channel>
</rss>
```

各トップレベルの `<item>` 要素で、アクションが記述されています。

- `<link>` は必須であり、アクション ID として使われます。
- `<date>` は、15 項目以下の場合は無視されます。それ以外の場合は必須です。
  - 15 項目を超える場合は、最新の 15 個が使われます。
  - それぞれ、RSS または Atom の標準形式になっている必要があります。
    - RSS の場合は [RFC 822](https://tools.ietf.org/html/rfc822): たとえば、`"Fri, 28 Apr 2017 18:02:06 GMT"`
    - Atom の場合は [RFC 3339](https://tools.ietf.org/html/rfc3339): たとえば、`"2016-12-19T16:39:57-08:00"`
- `<title>` は省略可能であり、記事を説明する特徴の生成に使用されます。
- `<guid>` は省略可能であり、システム経由でコールバック関数に渡されます (Ranking API の呼び出しで `?details` パラメーターが指定されている場合)。

`<item>` 内の他の要素は無視されます。

Atom フィードのバージョンも、同じ XML 構文と規則を使います。

> [!TIP]
> システムが独自の記事 ID を使っている場合は、`<guid>` を使ってコールバック関数に渡すことができます。
