---
title: チュートリアル:正規表現エンティティ - LUIS
titleSuffix: Azure Cognitive Services
description: 正規表現エンティティを使用して一貫した形式のデータを発話から抽出します。
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: tutorial
ms.date: 12/04/2019
ms.author: diberry
ms.openlocfilehash: 8da47899e2d3d2c1d04e9cf4768a968e7893ce96
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/05/2019
ms.locfileid: "74852447"
---
# <a name="tutorial-get-well-formatted-data-from-the-utterance"></a>チュートリアル:発話から適切な形式のデータを取得する
このチュートリアルでは、一貫した形式のデータを発話から抽出する正規表現エンティティを作成します。

[!INCLUDE [Uses preview portal](includes/uses-portal-preview.md)]

**このチュートリアルで学習する内容は次のとおりです。**

<!-- green checkmark -->
> [!div class="checklist"]
> * アプリをインポートする
> * 意図を追加する
> * 正規表現エンティティを追加する
> * アプリをトレーニング、発行し、クエリを実行して抽出データを取得する

[!INCLUDE [LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="regular-expression-entities"></a>正規表現エンティティ

発話から適切な形式のテキストを取り出すには、正規表現エンティティを使用します。 発話の意図は常に機械学習によって決定されますが、この特定のエンティティ型は機械学習されません。 正規表現エンティティは、[正規表現](https://docs.microsoft.com/dotnet/standard/base-types/regular-expression-language-quick-reference)のパターンで表すことができるテキストで効果的に使用できます。

`Send pizza delivery time to x123432`

この例では、テキスト メッセージの送信に "_ショート コード_" を使用しています。 このショート コードは 5 桁または 6 桁の数値コードにプレフィックスとして x を付けたもので、`x\d{5,6}` という正規表現で表すことができます。

LUIS アプリに正規表現エンティティを追加する場合には、テキストに正規表現エンティティの[ラベル](label-entity-example-utterance.md)を付ける必要はありません。 すべての意図のすべての発話が適用の対象となります。

## <a name="import-example-json-to-begin-app"></a>サンプル .json をインポートしてアプリを開始する

1.  [アプリの JSON ファイル](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/documentation-samples/tutorials/machine-learned-entity/pizza-tutorial-with-entities.json)をダウンロードして保存します。

[!INCLUDE [Import app steps](includes/import-app-steps.md)]

## <a name="create-intent-for-sending-confirmation-text-messages"></a>確認のテキスト メッセージを送信する意図を作成する

1. **[+ 作成]** を選択して、確認のテキストを送信するための発話の意図を分類する新しい意図を作成します。

1. ポップアップ ダイアログ ボックスに「`ConfirmationText`」と入力して、 **[完了]** を選択します。

1. 発話の例を意図に追加します。

    |発話の例|
    |--|
    |Send pizza delivery time to x123432|
    |Txt x234567 for time|
    |x23987 for the notice|

    機械学習エンティティを抽出するためには、さまざまな発話の中にエンティティを含んだサンプルを入力する必要があります。しかし、この非機械学習エンティティでは、そうしたバリエーションは重要ではありません。 テキストは、正規表現に一致しさえすれば抽出されます。

## <a name="use-the-regular-expression-entity-for-well-formatted-data"></a>適切な形式のデータを取得するために正規表現エンティティを使用する
テキストの数値と一致する正規表現エンティティを作成します。 この正規表現はテキストと一致しますが、大文字小文字およびカルチャのバリアントは無視します。

1. 左のパネルで **[エンティティ]** を選びます。

1. [Entities list]\(エンティティ リスト\) ページで **[+ 作成]** を選択します。

1. ポップアップ ダイアログで新しいエンティティ名 `ConfirmationTextRegEx` を入力し、エンティティの種類として **RegEx** を選択して、 **[次へ]** を選択します。

    > [!div class="mx-imgBorder"]
    > ![正規表現エンティティの作成手順を開始する](./media/luis-quickstart-intents-regex-entity/pizza-create-new-entity.png)

1. **[Create a regex entity]\(正規表現エンティティの作成\)** で、 **[Regex]\(正規表現\)** の値として「`x\d{5,6}`」を入力し、 **[作成]** を選択します。

    > [!div class="mx-imgBorder"]
    > ![発話の例からデータを抽出する正規表現を入力する](./media/luis-quickstart-intents-regex-entity/pizza-set-regular-expression-for-new-entity.png)

1. 左側のメニューから **[Intents]\(意図\)** 、 **[ConfirmationText]** 意図の順に選択して、発話に正規表現がラベル付けされていることを確認します。

    > [!div class="mx-imgBorder"]
    > ![発話の例に正規表現がラベル付けされていることを確認する](./media/luis-quickstart-intents-regex-entity/pizza-reg-ex-entity-shown-example-utterances-intent.png)

    エンティティは機械学習エンティティではないため、エンティティは、作成されるとすぐに発話に適用されて LUIS ポータルに表示されます。

## <a name="train-the-app-before-testing-or-publishing"></a>テストまたは発行する前に、アプリをトレーニングする

[!INCLUDE [LUIS How to Train steps](includes/howto-train.md)]

## <a name="publish-the-app-to-query-from-the-endpoint"></a>アプリを発行してエンドポイントからクエリを実行する

[!INCLUDE [LUIS How to Publish steps](includes/howto-publish.md)]

## <a name="get-intent-and-entity-prediction-from-endpoint"></a>エンドポイントから意図およびエンティティ予測を取得する

1. [!INCLUDE [LUIS How to get endpoint first step](includes/howto-get-endpoint.md)]

2. アドレスの URL の末尾に移動し、次の発話を入力します。

    `Text my pizza delivery to x23456 x234567 x12345`

    最後の querystring パラメーターは `query` です。これは発話の**クエリ**です。

    ```json
    {
        "query": "Text my pizza delivery to x23456 x234567 x12345",
        "prediction": {
            "topIntent": "ConfirmationText",
            "intents": {
                "ConfirmationText": {
                    "score": 0.7061845
                },
                "ModifyOrder": {
                    "score": 0.196021989
                },
                "None": {
                    "score": 0.02342912
                },
                "Test-Pizza": {
                    "score": 0.01213586
                },
                "CancelOrder": {
                    "score": 0.0063042324
                },
                "Confirmation": {
                    "score": 0.0058615827
                },
                "Greetings": {
                    "score": 0.00398947531
                }
            },
            "entities": {
                "ConfirmationTextRegEx": [
                    "x23456",
                    "x234567",
                    "x12345"
                ],
                "$instance": {
                    "ConfirmationTextRegEx": [
                        {
                            "type": "ConfirmationTextRegEx",
                            "text": "x23456",
                            "startIndex": 26,
                            "length": 6,
                            "modelTypeId": 8,
                            "modelType": "Regex Entity Extractor",
                            "recognitionSources": [
                                "model"
                            ]
                        },
                        {
                            "type": "ConfirmationTextRegEx",
                            "text": "x234567",
                            "startIndex": 33,
                            "length": 7,
                            "modelTypeId": 8,
                            "modelType": "Regex Entity Extractor",
                            "recognitionSources": [
                                "model"
                            ]
                        },
                        {
                            "type": "ConfirmationTextRegEx",
                            "text": "x12345",
                            "startIndex": 41,
                            "length": 6,
                            "modelTypeId": 8,
                            "modelType": "Regex Entity Extractor",
                            "recognitionSources": [
                                "model"
                            ]
                        }
                    ]
                }
            }
        }
    }
    ```

    正規表現のエンティティを使用することで、LUIS は名前付けされたデータを抽出します。このデータは、JSON 応答を受信するクライアント アプリケーションにとって、プログラムでより有益です。


[!INCLUDE [LUIS How to clean up resources](includes/quickstart-tutorial-cleanup-resources.md)]

## <a name="related-information"></a>関連情報

* [概念 - エンティティ](luis-concept-entity-types.md)
* [正規表現エンティティの JSON リファレンス](reference-entity-regular-expression.md?tabs=V3)
* [データを抽出するエンティティを追加する方法](luis-how-to-add-entities.md)

## <a name="next-steps"></a>次の手順
このチュートリアルでは、新しい意図を作成し、発話例を追加し、正しい形式のデータを発話から抽出するための正規表現エンティティを作成しました。 トレーニングおよびアプリの発行後、エンドポイントのクエリによって意図を識別し、抽出されたデータを取得しました。

> [!div class="nextstepaction"]
> [リスト エンティティについて学習する](tutorial-list-entity.md)

