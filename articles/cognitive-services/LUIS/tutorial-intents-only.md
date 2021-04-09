---
title: チュートリアル:意図の予測 - LUIS
description: このチュートリアルでは、発話 (テキスト) に基づいてユーザーの意図を予測するカスタム アプリを作成します。
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: tutorial
ms.date: 05/05/2020
ms.openlocfilehash: 04ba7dc79deef2dbad12c3f65a324201d3e9f598
ms.sourcegitcommit: d135e9a267fe26fbb5be98d2b5fd4327d355fe97
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/10/2021
ms.locfileid: "102612764"
---
# <a name="tutorial-build-a-luis-app-to-determine-user-intentions"></a>チュートリアル:ユーザーの意図を特定する LUIS アプリを構築する

このチュートリアルでは、発話 (テキスト) に基づいてユーザーの意図を予測するカスタム アプリを作成します。

**このチュートリアルで学習する内容は次のとおりです。**

> [!div class="checklist"]
> * 新しいアプリの作成
> * 意図の作成
> * 発話の例を追加する
> * アプリのトレーニング
> * アプリの発行
> * エンドポイントから意図の予測を取得する

## <a name="user-intentions-as-intents"></a>意図としてのユーザーの意図

アプリの目的は、会話や自然言語テキストの意図を特定することです。

`I'd like to order a veggie pizza with a salad on the side.`

これらの意図 (intention) は、**意図**(Intent) に分類されます。

|Intent|目的|
|--|--|
|`ModifyOrder`|ユーザーのピザの注文を決定します。|
|`Greeting`|ボットの会話を開始します。|
|`ConfirmOrder`|ピザの注文を確定します。|
|`None`|LUIS アプリによって回答が想定されていないことをユーザーが質問しているかどうかを判定します。 この意図はアプリの作成の一部として提供され、削除することはできません。 |

## <a name="create-a-new-app"></a>新しいアプリの作成

[!INCLUDE [Follow these steps to create a new LUIS app](includes/create-pizza-app.md)]

## <a name="create-a-new-intent"></a>新しい意図を作成する

意図は、ユーザーの意図に基づいて自然言語のテキストからの決定されるユーザー発話を分類するために使用されます。

発話の分類には、その意図に分類されるためのユーザーの発話例が意図に必要です。

1. **[Intents]** \(意図\) ページの **[ビルド]** セクションから **[+ 作成]** を選択して新しい意図を作成します。 新しい意図の名前として「`OrderPizza`」を入力し、 **[完了]** を選択します。

    `OrderPizza` 意図は、ユーザーがピザの注文を希望したときに予測されます。

1. この意図に、ユーザーの質問として予想される発話例をいくつか追加します。

    |`OrderPizza` の発話例|
    |--|
    |`can i get a pepperoni pizza and a can of coke please`|
    |`can i get a small pizza with onions peppers and olives`|
    |`delivery for a small pepperoni pizza`|
    |`pickup a cheddar cheese pizza large with extra anchovies`|
    |`i need 2 large cheese pizzas 6 large pepperoni pizzas and 1 large supreme pizza`|
    |`Order a pizza for me`|

    > [!div class="mx-imgBorder"]
    > ![ LUIS ポータルの [Intents]\(意図\) ページに発話例を追加しているスクリーンショット](media/tutorial-intents-only/add-example-utterances-for-pizza-order.png)

    _発話例_ を指定することで、この意図に予測される種類の発話を、LUIS にトレーニングできます。 これらは、陽性の例です。 他のすべての意図の発話は、この意図にとっては陰性として扱われます。

    [!INCLUDE [Do not use too few utterances](includes/do-not-use-too-few-utterances.md)]

## <a name="create-remaining-intents"></a>残りの意図を作成する

1. `Greeting` 意図を作成して、以下の発話例を追加します。 これは、ユーザーが新しくピザの注文の会話を開始しているかどうかを判断する意図です。

    |`Greeting` の発話例|
    |--|
    |`Hi`|
    |`Hello`|
    |`Hey`|
    |`Start`|
    |`Begin`|

1. `Confirm` 意図を作成して、以下の発話例を追加します。 これは、ユーザーが注文を完了して、注文の詳細を受け入れるかを判定するための意図です。

    |`Confirm` の発話例|
    |--|
    |`Go ahead`|
    |`ok`|
    |`Yes`|
    |`Sure`|


## <a name="none-intent-example-utterances"></a>None 意図の発話の例

[!INCLUDE [Follow these steps to add the None intent to the app](includes/add-example-utterances-none-intent.md)]

## <a name="train-the-app"></a>アプリをトレーニングする

[!INCLUDE [LUIS How to Train steps](includes/howto-train.md)]

## <a name="publish-the-app"></a>アプリの発行

[!INCLUDE [LUIS How to Publish steps](includes/howto-publish.md)]

## <a name="get-intent-prediction"></a>意図の予測を取得する

1. [!INCLUDE [LUIS How to get endpoint first step](includes/howto-get-endpoint.md)]

1. アドレス バー上の URL の末尾に移動して、以下を入力します。

    `get a medium vegetarian pizza for delivery`

  
    クエリ文字列パラメーターは `query` で、発話の **クエリ** は URI で渡されます。 この発話は、どの発話例とも同じではありません。 これは、LUIS が上位スコアの意図として `OrderPizza` 意図を学習および予測するかどうかを確認するための優れたテストとなります。

    ```JSON
    {
        "query": "get a medium vegetarian pizza for delivery",
        "prediction": {
            "topIntent": "OrderPizza",
            "intents": {
                "OrderPizza": {
                    "score": 0.6488959
                },
                "None": {
                    "score": 0.139966831
                },
                "Confirm": {
                    "score": 0.00736504374
                },
                "Greeting": {
                    "score": 0.003970454
                }
            },
            "entities": {}
        }
    }
    ```

    現在このアプリにはまったくエンティティ (抽出する発話内のデータ単位) がないため、エンティティの配列は空になっています。

    JSON の結果では、最上位のスコアリング意図が **`prediction.topIntent`** プロパティとして識別されています。 すべてのスコアは 0 から 1 の範囲であり、1 に近いほどよいスコアです。

1. **Greeting** 意図を対象にするように、URL の **query** パラメーターを変更します。

    `Howdy`

    これは、発話例とまったく同じではないため、この意図によって何を予測するべきかを LUIS が学習できるかどうかを判断するための有用なテストになります。

    ```json
    {
        "query": "howdy",
        "prediction": {
            "topIntent": "Greeting",
            "intents": {
                "Greeting": {
                    "score": 0.446016937
                },
                "Confirm": {
                    "score": 0.2390079
                },
                "None": {
                    "score": 0.09119555
                },
                "OrderPizza": {
                    "score": 0.00109590159
                }
            },
            "entities": {}
        }
    }
    ```

    この予測には、44% の信頼度スコアが付けられています。 信頼度スコアを上げるには、15 から 30 個の発話例を追加します。

## <a name="client-application-next-steps"></a>クライアント アプリケーションの次の手順

このチュートリアルでは、LUIS アプリを作成し、意図を作成して、各意図に発話例を追加しました。また、None 意図への発話例の追加、トレーニング、発行、およびエンドポイントでのテストを行いました。 これらが、LUIS モデルを構築する基本的な手順です。

LUIS は、JSON 応答を返した後は、この要求の処理を終えています。 LUIS は、ユーザーの発話に対する回答は提供しません。自然言語で、どのような種類の情報が質問されているかを識別するだけです。 会話のフォローアップは、Azure ボットなどのクライアント アプリケーションによって提供されます。


[!INCLUDE [LUIS How to clean up resources](includes/quickstart-tutorial-cleanup-resources.md)]

## <a name="related-information"></a>関連情報

* [エンティティの種類](luis-concept-entity-types.md)
* [トレーニング方法](luis-how-to-train.md)
* [発行方法](luis-how-to-publish-app.md)
* [LUIS ポータルでのテスト方法](luis-interactive-test.md)
* [Azure Bot](/azure/bot-service/)


## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [このアプリに分解可能なエンティティを追加する](tutorial-machine-learned-entity.md)