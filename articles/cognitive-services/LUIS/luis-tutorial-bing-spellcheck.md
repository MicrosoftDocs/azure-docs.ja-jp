---
title: 単語のスペルミスの修正 - LUIS
titleSuffix: Azure Cognitive Services
description: Bing Spell Check API V7 を LUIS エンドポイント クエリに追加して、発話の単語のスペルミスを修正します。
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: how-to
ms.date: 11/19/2019
ms.author: diberry
ms.openlocfilehash: ee0c659cf510eb0a438ae0c78a554c98dccffa89
ms.sourcegitcommit: 5cace04239f5efef4c1eed78144191a8b7d7fee8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/08/2020
ms.locfileid: "86144271"
---
# <a name="correct-misspelled-words-with-bing-spell-check"></a>Bing Spell Check を使用した単語のスペルミスの修正

LUIS アプリと [Bing Spell Check API V7](https://azure.microsoft.com/services/cognitive-services/spell-check/) を統合すると、LUIS によって発話のスコアとエンティティが予測される前に、発話の単語のスペル ミスを修正できます。

[!INCLUDE [Not supported in V3 API prediction endpoint](./includes/v2-support-only.md)]

## <a name="create-endpoint-key"></a>エンドポイント キーの作成

Azure portal で Bing Spell Check リソースを作成するには、次の手順に従います。

1. [Azure Portal](https://portal.azure.com) にログインします。

2. 左上隅の **[リソースの作成]** を選択します。

3. 検索ボックスに「 `Bing Spell Check API V7`」と入力します。

    ![Bing Spell Check API V7 の検索](./media/luis-tutorial-bing-spellcheck/portal-search.png)

4. サービスを選択します。

5. 右側に情報パネルが表示され、法的通知などの情報が示されます。 **[作成]** を選択して、サブスクリプション作成プロセスを開始します。

6. 次のパネルで、サービス設定を入力します。 サービス作成プロセスが完了するまで待ちます。

    ![サービス設定の入力](./media/luis-tutorial-bing-spellcheck/subscription-settings.png)

7. 左側のナビゲーションの **[お気に入り]** の下で、 **[すべてのリソース]** を選択します。

8. 新しいサービスを選択します。 種類は **[Cognitive Services]** で、場所は **[グローバル]** です。

9. メイン パネルで **[キー]** を選択して、新しいキーを確認します。

    ![キーの取得](./media/luis-tutorial-bing-spellcheck/grab-keys.png)

10. 最初のキーをコピーします。 必要なのは 2 つのキーのうち 1 つだけです。

<!--
## Using the key in LUIS test panel
There are two places in LUIS to use the key. The first is in the [test panel](luis-interactive-test.md#view-bing-spell-check-corrections-in-test-panel). The key isn't saved into LUIS but instead is a session variable. You need to set the key every time you want the test panel to apply the Bing Spell Check API v7 service to the utterance. See [instructions](luis-interactive-test.md#view-bing-spell-check-corrections-in-test-panel) in the test panel for setting the key.
-->
## <a name="adding-the-key-to-the-endpoint-url"></a>エンドポイント URL へのキーの追加
エンドポイント クエリでは、スペルの修正を適用するクエリごとに、キーがクエリ文字列パラメーターに渡される必要があります。 LUIS を呼び出すチャットボットを使用することも、LUIS エンドポイント API を直接呼び出すこともできます。 エンドポイントがどのように呼び出されたかに関係なく、すべての呼び出しに、スペルの修正が適切に動作するうえで必要な情報が含まれていなければなりません。

エンドポイント URL には、適切に渡す必要がある値が複数含まれます。 Bing Spell Check API v7 キーは、その 1 つに過ぎません。 **spellCheck** パラメーターを true に設定し、**bing-spell-check-subscription-key** の値をキーの値に設定する必要があります。

`https://{region}.api.cognitive.microsoft.com/luis/v2.0/apps/{appID}?subscription-key={luisKey}&spellCheck=true&bing-spell-check-subscription-key={bingKey}&verbose=true&timezoneOffset=0&q={utterance}`

## <a name="send-misspelled-utterance-to-luis"></a>LUIS への発話のスペルミスの送信
1. Web ブラウザーで、上記の文字列をコピーして、`region`、`appId`、`luisKey`、および `bingKey` をご自身の値に置き換えます。 エンドポイント リージョンが公開[リージョン](luis-reference-regions.md)と異なる場合は、必ずエンドポイント リージョンを使用します。

2. 発話のスペルミスを追加します ("How far is the mountainn?" など)。 英語では、`n` が 1 つの `mountain` が正しいスペルです。

3. Enter キーを押して、クエリを LUIS に送信します。

4. LUIS が、JSON の結果 `How far is the mountain?` で応答します。 Bing Spell Check API v7 によってスペル ミスが検出された場合、LUIS アプリの JSON 応答の `query` フィールドには元のクエリが、`alteredQuery` フィールドには、LUIS に送信された修正済みクエリが含まれています。

```json
{
  "query": "How far is the mountainn?",
  "alteredQuery": "How far is the mountain?",
  "topScoringIntent": {
    "intent": "Concierge",
    "score": 0.183866
  },
  "entities": []
}
```

## <a name="ignore-spelling-mistakes"></a>スペル ミスの無視

Bing Spell Check API v7 サービスを使用したくない場合は、正しいスペルと正しくないスペルを追加する必要があります。

次の 2 つの解決策があります。

* LUIS が正しいスペルと入力ミスを学習できるように、さまざまなスペルを含んだ発話の例にラベルを付ける。 このラベル付けオプションは、スペル チェックを使用するよりも手間がかかります。
* さまざまなバリエーションの単語を含んだ語句一覧を作成する。 この解決策では、発話の例に含まれる単語のバリエーションにラベルを付ける必要はありません。

## <a name="publishing-page"></a>ページの公開
[公開](luis-how-to-publish-app.md)ページには、 **[Enable Bing spell checker]\(Bing スペル チェックを有効にする\)** チェック ボックスがあります。 これは、キーを作成し、エンドポイント URL がどのように変わったかを解釈するうえで便利です。 発話ごとにスペルが修正されるようにするには、引き続き正しいエンドポイント パラメーターを使用する必要があります。

> [!div class="nextstepaction"]
> [発話の例の詳細](luis-how-to-add-example-utterances.md)
