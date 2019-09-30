---
title: チュートリアル:正規表現エンティティ - LUIS
titleSuffix: Azure Cognitive Services
description: 正規表現エンティティを使用して一貫した形式のデータを発話から抽出する
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: tutorial
ms.date: 09/03/2019
ms.author: diberry
ms.openlocfilehash: db60d87b441836f38309166318dd1778ce828483
ms.sourcegitcommit: f176e5bb926476ec8f9e2a2829bda48d510fbed7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/04/2019
ms.locfileid: "70307599"
---
# <a name="tutorial-get-well-formatted-data-from-the-utterance"></a>チュートリアル:発話から適切な形式のデータを取得する
このチュートリアルでは、**正規表現**エンティティを使用して、発話から一貫した形式のデータを抽出するアプリを作成します。

**このチュートリアルで学習する内容は次のとおりです。**

<!-- green checkmark -->
> [!div class="checklist"]
> * 新しいアプリの作成 
> * 意図を追加する
> * 正規表現エンティティを追加する 
> * トレーニング
> * 発行
> * エンドポイントから意図とエンティティを取得する

[!INCLUDE [LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="regular-expression-entities"></a>正規表現エンティティ

このアプリでは正規表現エンティティを使用して、適切な形式の人事 (HR) のフォーム番号を発話から抽出します。 発話の意図は常に機械学習によって決定されますが、この特定のエンティティ型は機械学習されません。 

**発話の例を次に示します。**

|発話の例|
|--|
|HRF-123456 はどこにありますか?|
|誰が HRF-123234 を作成しましたか?|
|HRF-456098 はフランス語で発行されますか?|
|HRF-456098|
|HRF-456098 の日付は?|
 
正規表現がこのデータの種類に適しているのは次の場合です。

* データが正しい形式である。


## <a name="create-a-new-app"></a>新しいアプリの作成

[!INCLUDE [Follow these steps to create a new LUIS app](../../../includes/cognitive-services-luis-create-new-app-steps.md)]

## <a name="create-intent-for-finding-form"></a>フォームを検索するための意図を作成する

1. [!INCLUDE [Start in Build section](../../../includes/cognitive-services-luis-tutorial-build-section.md)]

1. **[Create new intent]\(意図の新規作成\)** を選択します。 

1. ポップアップ ダイアログ ボックスに「`FindForm`」と入力して、 **[完了]** を選択します。 

    ![検索ボックスにユーティリティが表示されている [Create new intent]\(意図の新規作成\) ダイアログのスクリーンショット](./media/luis-quickstart-intents-regex-entity/create-new-intent-ddl.png)

1. 発話の例を意図に追加します。

    |発話の例|
    |--|
    |hrf-123456 の URL は何ですか。|
    |hrf-345678 の場所はどこですか。|
    |hrf-456098 はいつ更新されましたか。|
    |John Smith は先週、hrf-234639 を更新しましたか。|
    |hrf-345123 のバージョン数はいくつですか。|
    |フォーム hrf-123456 を誰が承認する必要がありますか。|
    |hrf 345678 をサインオフする必要があるユーザー数はいくつですか。|
    |hrf-234123 の日付はいつですか。|
    |hrf 546234 の作成者は誰ですか。|
    |hrf-456234 のタイトルは何ですか。|

    [![新しい発話が強調表示されている [Intents]\(意図\) ページのスクリーンショット](./media/luis-quickstart-intents-regex-entity/findform-intent.png)](./media/luis-quickstart-intents-regex-entity/findform-intent.png#lightbox)

    [!INCLUDE [Do not use too few utterances](../../../includes/cognitive-services-luis-too-few-example-utterances.md)]  

## <a name="use-the-regular-expression-entity-for-well-formatted-data"></a>適切な形式のデータを取得するために正規表現エンティティを使用する
フォーム番号と一致する正規表現のエンティティは `hrf-[0-9]{6}` です。 この正規表現はリテラル文字の `hrf-` と一致しますが、大文字小文字およびカルチャのバリアントは無視します。 0 ～ 9 の 6 桁の数字と正確に一致します。

HRF は `human resources form` の略です。

LUIS では、発話が意図に追加されるときに、発話をトークン化します。 これらの発話のトークン化では、`Where is HRF - 123456?` のように、ハイフンの前後にスペースを追加します。トークン化される前に、正規表現が raw 形式の発話に適用されます。 _raw_ 形式に適用されているため、正規表現で単語の境界を処理する必要はありません。 

次の手順で、正規表現エンティティを作成して、HRF 番号形式を LUIS に認識させます。

1. 左のパネルで **[エンティティ]** を選びます。

1. [エンティティ] ページで **[新しいエンティティの作成]** ボタンを選択します。 

1. ポップアップ ダイアログで、新しいエンティティ名 `HRF-number` を入力して、エンティティ型として **RegEx** を選択し、**Regex** の値に「`hrf-[0-9]{6}`」と入力してから、 **[完了]** を選択します。

    ![ポップアップ ダイアログ設定の新しいエンティティ プロパティのスクリーン ショット](./media/luis-quickstart-intents-regex-entity/create-regex-entity.png)

1. 左側のメニューから **[Intents]\(意図\)** 、 **[FindForm]** 意図の順に選択して、発話に正規表現のラベルが付与されていることを確認します。 

    [![既存のエンティティと正規パターンでのラベル発話のスクリーンショット](./media/luis-quickstart-intents-regex-entity/labeled-utterances-for-entity.png)](./media/luis-quickstart-intents-regex-entity/labeled-utterances-for-entity.png#lightbox)

    エンティティは機械学習エンティティではないため、エンティティは、作成されるとすぐに発話に適用されて LUIS Web サイトに表示されます。

## <a name="add-example-utterances-to-the-none-intent"></a>発話の例を None 意図に追加する 

[!INCLUDE [Follow these steps to add the None intent to the app](../../../includes/cognitive-services-luis-create-the-none-intent.md)]

## <a name="train-the-app-before-testing-or-publishing"></a>テストまたは発行する前に、アプリをトレーニングする

[!INCLUDE [LUIS How to Train steps](../../../includes/cognitive-services-luis-tutorial-how-to-train.md)]

## <a name="publish-the-app-to-query-from-the-endpoint"></a>アプリを発行してエンドポイントからクエリを実行する

[!INCLUDE [LUIS How to Publish steps](../../../includes/cognitive-services-luis-tutorial-how-to-publish.md)]

## <a name="get-intent-and-entity-prediction-from-endpoint"></a>エンドポイントから意図およびエンティティ予測を取得する

1. [!INCLUDE [LUIS How to get endpoint first step](../../../includes/cognitive-services-luis-tutorial-how-to-get-endpoint.md)]

2. アドレスの URL の末尾に移動し、次の発話を入力します。

    `When were HRF-123456 and hrf-234567 published in the last year?`

    最後の querystring パラメーターは `q` です。これは発話の**クエリ**です。 この発話はラベル付けされたどの発話とも同じではないので、よいテストであり、`FindForm` 意図と 2 つのフォーム番号 `HRF-123456` と `hrf-234567` が返される必要があります。

    ```json
    {
      "query": "When were HRF-123456 and hrf-234567 published in the last year?",
      "topScoringIntent": {
        "intent": "FindForm",
        "score": 0.9988884
      },
      "intents": [
        {
          "intent": "FindForm",
          "score": 0.9988884
        },
        {
          "intent": "None",
          "score": 0.00204812363
        }
      ],
      "entities": [
        {
          "entity": "hrf-123456",
          "type": "HRF-number",
          "startIndex": 10,
          "endIndex": 19
        },
        {
          "entity": "hrf-234567",
          "type": "HRF-number",
          "startIndex": 25,
          "endIndex": 34
        }
      ]
    }
    ```

    正規表現のエンティティを使用することで、LUIS は名前付けされたデータを抽出します。このデータは、JSON 応答を受信するクライアント アプリケーションにとって、プログラムでより有益です。


## <a name="clean-up-resources"></a>リソースのクリーンアップ

[!INCLUDE [LUIS How to clean up resources](../../../includes/cognitive-services-luis-tutorial-how-to-clean-up-resources.md)]

## <a name="related-information"></a>関連情報

* [正規表現](luis-concept-entity-types.md#regular-expression-entity)エンティティの概念
* [トレーニング方法](luis-how-to-train.md)
* [発行方法](luis-how-to-publish-app.md)
* [LUIS ポータルでのテスト方法](luis-interactive-test.md)

## <a name="next-steps"></a>次の手順
このチュートリアルでは、新しい意図を作成し、発話例を追加し、正しい形式のデータを発話から抽出するための正規表現エンティティを作成しました。 トレーニングおよびアプリの発行後、エンドポイントのクエリによって意図を識別し、抽出されたデータを取得しました。

> [!div class="nextstepaction"]
> [リスト エンティティについて学習する](luis-quickstart-intent-and-list-entity.md)

