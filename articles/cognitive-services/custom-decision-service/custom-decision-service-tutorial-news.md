---
title: チュートリアル:記事のパーソナル化 - Custom Decision Service
titlesuffix: Azure Cognitive Services
description: コンテキストに応じた意思決定のための記事のパーソナル化に関するチュートリアル。
services: cognitive-services
author: slivkins
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-decision-service
ms.topic: tutorial
ms.date: 05/08/2018
ms.author: slivkins
ms.openlocfilehash: d8ddafe20ff93e7ae4d51e2180bbd40447729234
ms.sourcegitcommit: 943af92555ba640288464c11d84e01da948db5c0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/09/2019
ms.locfileid: "55983034"
---
# <a name="tutorial-article-personalization-for-contextual-decision-making"></a>チュートリアル:コンテキストに応じた意思決定のための記事のパーソナル化

このチュートリアルでは、Web サイトのフロント ページにある記事の選択をパーソナル化することに焦点を当てています。 Custom Decision Service は、たとえば、フロント ページの記事の "*複数の*" リストに影響を与えます。 おそらく、このページは、政治とスポーツのみを扱うニュース Web サイトです。 このページには、政治、スポーツ、最新ニュースの、3 つのランク付けされた記事リストが示されます。

## <a name="applications-and-action-sets"></a>アプリケーションとアクション セット

次に、シナリオをフレームワークに適合させる方法を示します。 最適化する各リストに 1 つずつ、app-politics、app-sports、app-recent の 3 つのアプリケーションがあるとします。 各アプリケーションの候補記事を指定するために、政治用とスポーツ用の 2 つのアクション セットがあります。 app-recent のアクション セットは、他の 2 つのセットの和集合として自動的に設定されます。

> [!TIP]
> アクション セットは、Custom Decision Service のアプリケーション間で共有できます。

## <a name="prepare-action-set-feeds"></a>アクション セットのフィードを準備する

Custom Decision Service は、顧客によって提供される RSS フィードまたは Atom フィードを介してアクション セットを使用します。 政治用とスポーツ用の 2 つのフィードを提供します。 これらは `http://www.domain.com/feeds/<feed-name>` から提供されるとします。

各フィードは記事の一覧を提供します。 RSS では、次のようにそれぞれの記事が `<item>` 要素で指定されます。

```xml
<rss version="2.0"><channel>
   <item>
      <title><![CDATA[article title]]></title>
      <link>"article url"</link>
      <pubDate>publication date</pubDate>
    </item>
</channel></rss>
```

記事の順序は重要です。 記事の順序は既定のランキングを指定します。これは、記事の順序に関する最も妥当な推測に基づいて決定します。 この既定のランキングは、ダッシュボードでのパフォーマンス比較に使用されます。

フィード形式の詳細については、[API リファレンス](custom-decision-service-api-reference.md#action-set-api-customer-provided)を参照してください。

## <a name="register-a-new-app"></a>新しいアプリの登録

1. [Microsoft アカウント](https://portal.ds.microsoft.com/)を使用してサインインします。 リボンの **[My Portal]\(マイ ポータル\)** をクリックします。

2. 新しいアプリケーションを登録するために、**[新しいアプリ]** ボタンをクリックします。

    ![Custom Decision Service ポータル](./media/custom-decision-service-tutorial/portal.png)

3. **[アプリ ID]** テキスト ボックスに、アプリケーションの一意の名前を入力します。 この名前が既に別の顧客によって使用されている場合は、別のアプリ ID を選択するように求められます。 **[Advanced]\(詳細\)** チェック ボックスをオンにし、Azure ストレージ アカウントの[接続文字列](../../storage/common/storage-configure-connection-string.md)を入力します。 通常は、すべてのアプリケーションに同じストレージ アカウントを使用します。

    ![[新しいアプリ] ダイアログ ボックス](./media/custom-decision-service-tutorial/new-app-dialog.png)

    上記のシナリオの 3 つのアプリケーションをすべて登録すると、次のように表示されます。

    ![アプリのリスト](./media/custom-decision-service-tutorial/apps.png)

    **[アプリ]** ボタンをクリックすると、この一覧に戻ることができます。

4. **[新しいアプリ]** ダイアログ ボックスで、アクション フィードを指定します。 アクション フィードは、**[フィード]** ボタンをクリックし、**[新しいフィード]** ボタンをクリックすることで指定することもできます。 フィードの**名前**を入力し、提供元の **URL** を入力します。さらに、**更新時間**を入力します。 更新時間は、Custom Decision Service がフィードを更新する頻度を指定します。

    ![[新しいフィード] ダイアログ ボックス](./media/custom-decision-service-tutorial/new-feed-dialog.png)

    アクション フィードは、指定された場所に関係なく、どのアプリでも使用できます。 シナリオの両方のアクション フィードを指定すると、次のように表示されます。

    ![フィードの一覧](./media/custom-decision-service-tutorial/feeds.png)

    **[フィード]** ボタンをクリックすると、この一覧に戻ることができます。

## <a name="use-the-apis"></a>API の使用

Custom Decision Service では、Ranking API を使用して記事を順位付けします。 この API を使用するには、フロント ページの HTML 見出しに次のコードを挿入します。

```html
<!-- Define the "callback function" to render UI -->
<script> function callback(data) { … } </script>

<!--  call Ranking API after callback() is defined, separately for each app -->
<script src="https://ds.microsoft.com/api/v2/app-politics/rank/feed-politics" async></script>
<script src="https://ds.microsoft.com/api/v2/app-sports/rank/feed-sports" async></script>
<script src="https://ds.microsoft.com/api/v2/app-recent/rank/feed-politics/feed-sports" async></script>
<!-- NB: action feeds for 'app-recent' are listed one after another. -->
```

Ranking API からの HTTP 応答は JSONP 形式の文字列です。 たとえば、app-politics の場合、文字列は次のようになります。

```json
callback({
   "ranking":[{"id":"url1"}, {"id":"url2"}, {"id":"url3"}],
   "eventId":"<opaque event string>",
   "appId":"app-politics",
   "actionSets":[{"id":"feed-politics","lastRefresh":"date"}] });
```

その場合、ブラウザーは `callback()` 関数の呼び出しとしてこの文字列を実行します。 `callback()` 関数の `data` 引数には、アプリ ID とレンダリングされる URL のランキングが含まれます。 特に、`callback()` では、`data.appId` を使用して 3 つのアプリケーションを区別する必要があります。 `eventId` は、提供されたランキングを対応するクリックがある場合にこれと一致させるために、Custom Decision Service が内部的に使用します。

> [!TIP]
> `callback()` では、`lastRefresh` フィールドを使用して、各アクション フィードの鮮度を確認する場合があります。 提供されたフィードが十分に最新でない場合、`callback()` は、提供されたランキングを無視してこのフィードを直接呼び出し、フィードによって提供される既定のランキングを使用することがあります。

Ranking API の仕様と提供される追加オプションの詳細については、[API リファレンス](custom-decision-service-api-reference.md)を参照してください。

トップ記事でのユーザーの選択項目は、Reward API を呼び出すことによって取得できます。 トップ記事での選択項目を受け取ったら、フロント ページで次のコードを呼び出す必要があります。

```javascript
$.ajax({
    type: "POST",
    url: '//ds.microsoft.com/api/v2/<appId>/reward/<eventId>',
    contentType: "application/json" })
```

クリック処理コードで `appId` と `eventId` を使用するには、注意が必要です。 たとえば、次のように `callback()` 関数を実装できます。

```javascript
function callback(data) {
    $.map(data.ranking, function (element) {
        //custom rendering function given content info
        render({appId:data.appId,
                article:element,
                onClick: element.id != data.rewardAction ? null :
                   function() {
                       $.ajax({
                       type: "POST",
                       url: '//ds.microsoft.com/api/v2/' + data.appId + '/reward/' + data.eventId,
                       contentType: "application/json" })}
                });
}}
```

この例では、`render()` 関数を実装して、特定のアプリケーションに特定の記事をレンダリングします。 この関数は、アプリ ID と記事を (Ranking API の形式で) 入力します。 `onClick` パラメーターは、クリックを処理するために `render()` から呼び出す必要がある関数です。 この関数は、クリックがトップ スロットに対して行われたかどうかを確認します。 次に、適切なアプリ ID とイベント ID を使用して Reward API を呼び出します。

## <a name="next-steps"></a>次の手順

* 提供される機能の詳細については、[API リファレンス](custom-decision-service-api-reference.md)を参照してください。
