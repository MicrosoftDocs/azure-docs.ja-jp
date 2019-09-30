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
ms.topic: conceptual
ms.date: 09/05/2019
ms.author: diberry
ms.openlocfilehash: 43cb2f0c3ef0f845afcd309dccb35bc632ffd324
ms.sourcegitcommit: 88ae4396fec7ea56011f896a7c7c79af867c90a1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/06/2019
ms.locfileid: "70390960"
---
# <a name="correct-misspelled-words-with-bing-spell-check"></a>Bing Spell Check を使用した単語のスペルミスの修正

LUIS アプリと [Bing Spell Check API V7](https://azure.microsoft.com/services/cognitive-services/spell-check/) を統合すると、LUIS によって発話のスコアとエンティティが予測される前に、発話の単語のスペル ミスを修正できます。 

## <a name="create-first-key-for-bing-spell-check-v7"></a>最初の Bing Spell Check V7 キーの作成
[最初の Bing Spell Check API v7 キー](https://azure.microsoft.com/try/cognitive-services/?api=spellcheck-api)は無料です。 

![無料のキーの作成](./media/luis-tutorial-bing-spellcheck/free-key.png)

<a name"create-subscription-key"></a>
## <a name="create-endpoint-key"></a>エンドポイント キーの作成
無料のキーの期限が切れた場合は、エンドポイント キーを作成します。

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

## <a name="using-the-key-in-luis-test-panel"></a>LUIS テスト パネルでのキーの使用
LUIS では 2 つの場所でキーを使用します。 1 つ目は[テスト パネル](luis-interactive-test.md#view-bing-spell-check-corrections-in-test-panel)です。 キーは LUIS には保存されず、セッションごとに変わります。 テスト パネルで Bing Spell Check API v7 サービスを発話に適用するには、そのたびにキーを設定する必要があります。 キーの設定については、テスト パネルの[説明](luis-interactive-test.md#view-bing-spell-check-corrections-in-test-panel)に従ってください。

## <a name="adding-the-key-to-the-endpoint-url"></a>エンドポイント URL へのキーの追加
エンドポイント クエリでは、スペルの修正を適用するクエリごとに、キーがクエリ文字列パラメーターに渡される必要があります。 LUIS を呼び出すチャットボットを使用することも、LUIS エンドポイント API を直接呼び出すこともできます。 エンドポイントがどのように呼び出されたかに関係なく、すべての呼び出しに、スペルの修正が適切に動作するうえで必要な情報が含まれていなければなりません。

エンドポイント URL には、適切に渡す必要がある値が複数含まれます。 Bing Spell Check API v7 キーは、その 1 つに過ぎません。 **spellCheck** パラメーターを true に設定し、**bing-spell-check-subscription-key** の値をキーの値に設定する必要があります。

`https://{region}.api.cognitive.microsoft.com/luis/v2.0/apps/{appID}?subscription-key={luisKey}&spellCheck=**true**&bing-spell-check-subscription-key=**{bingKey}**&verbose=true&timezoneOffset=0&q={utterance}`

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
Bing Spell Check API v7 サービスを使用したくない場合は、LUIS が正しいスペルと入力ミスを学習できるように、スペル ミスを含む発話にラベルを付けることができます。 このラベル付けオプションは、スペル チェックを使用するよりも手間がかかります。

## <a name="publishing-page"></a>ページの公開
[公開](luis-how-to-publish-app.md)ページには、 **[Enable Bing spell checker]\(Bing スペル チェックを有効にする\)** チェック ボックスがあります。 これは、キーを作成し、エンドポイント URL がどのように変わったかを解釈するうえで便利です。 発話ごとにスペルが修正されるようにするには、引き続き正しいエンドポイント パラメーターを使用する必要があります。 

> [!div class="nextstepaction"]
> [発話の例の詳細](luis-how-to-add-example-utterances.md)
