---
title: チュートリアル:リスト エンティティ - LUIS
titleSuffix: Azure Cognitive Services
description: 事前に定義された項目の一覧に一致するデータを取得します。 一覧の各項目には、同様に完全に一致するシノニムを含めることができます。
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: tutorial
ms.date: 12/04/2019
ms.author: diberry
ms.openlocfilehash: 83644ac7144adaa664b13b6e7d7993c50da0e779
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/05/2019
ms.locfileid: "74852471"
---
# <a name="tutorial-get-exact-text-matched-data-from-an-utterance-with-list-entity"></a>チュートリアル:リスト エンティティを使って発話から完全一致のテキスト データを取得する

このチュートリアルでは、事前に定義された項目の一覧に厳密に一致するデータを取得する方法について説明します。

[!INCLUDE [Uses preview portal](includes/uses-portal-preview.md)]

**このチュートリアルで学習する内容は次のとおりです。**

<!-- green checkmark -->
> [!div class="checklist"]
> * アプリをインポートして既存の意図を使用する
> * リスト エンティティを追加する
> * アプリをトレーニング、発行し、クエリを実行して抽出データを取得する

[!INCLUDE [LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="what-is-a-list-entity"></a>リスト エンティティとは

リスト エンティティは、発話内の単語に対する完全なテキストの一致です。 一覧の各項目には、シノニムの一覧を含めることができます。 完全一致が必要な場合には、リスト エンティティを使用してください。

今回はインポートするピザ注文アプリケーションのために、さまざまな種類のピザ生地を列挙したリスト エンティティを作成します。

リスト エンティティがこのデータの種類に適しているのは次の場合です。

* データ値が既知のセットである。
* セットがこのエンティティ型の最大 LUIS [境界](luis-boundaries.md)を超えていない。
* 発話内のテキストがシノニムまたは正規名に完全に一致している。 LUIS では、完全なテキストの一致以外にリストは使用されません。 語幹抽出、複数形、その他のバリエーションは、リスト エンティティだけでは解決されません。 バリエーションを管理するには、オプションのテキスト構文で[パターン](reference-pattern-syntax.md#syntax-to-mark-optional-text-in-a-template-utterance)を使用することを検討します。

> [!CAUTION]
> リスト エンティティとフレーズ リストを記述子とする機械学習済みエンティティのどちらが必要であるかがわからない場合には、柔軟性の点で優れた後者を使用することをお勧めします。 この方法なら、LUIS が抽出対象とするデータの値を学習したり、拡張したりすることができます。

## <a name="import-example-json-and-add-utterances"></a>.json サンプルをインポートして発話を追加する

1.  [アプリの JSON ファイル](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/documentation-samples/tutorials/machine-learned-entity/pizza-tutorial-with-entities.json)をダウンロードして保存します。

    [!INCLUDE [Import app steps](includes/import-app-steps.md)]

1. インポートしたアプリには `OrderPizza` という意図があります。 その意図を選択し、新種の生地について言及した発話をいくつか追加します。

    |新しい発話|
    |--|--|
    |パン生地で S サイズのペペローニ ピザを注文してください|
    |薄生地のハワイアン ピザを 3 つ|
    |具入り生地を 2 枚にグリッシーニを付けて、宅配で|
    |厚生地のピザを持ち帰りで 1 枚|
    |ディープ ディッシュのペペローニ ピザを 1 枚|

## <a name="crust-list-entity"></a>生地のリスト エンティティ

意図 **OrderPizza** にピザ生地に言及したサンプルの発話を追加できたので、次は LUIS が生地の種類を表す単語がどれであるかを把握できるようにする必要があります。

正式名称と同意語の例はそれぞれ次のとおりです。

|正規名|シノニム|
|--|--|
|ディープ ディッシュ|ディープ<br>ディープ ディッシュ生地<br>厚型<br>厚生地|
|パン|レギュラー<br>original<br>普通<br>レギュラー生地<br>オリジナル生地<br>普通の生地|
|具入り|具入り生地|
|薄型|薄生地<br>スキニー<br>スキニー生地|

1. 左のパネルで **[エンティティ]** を選びます。

1. **[+ 作成]** を選択します。

1. エンティティ ポップアップ ダイアログで、エンティティ名として「`CrustList`」を入力し、エンティティ型として **[リスト]** を指定します。 **[次へ]** を選択します。

    > [!div class="mx-imgBorder"]
    > ![新しいエンティティを作成するポップアップ ダイアログのスクリーンショット](media/luis-quickstart-intent-and-list-entity/create-pizza-crust-list-entity.png)

1. **[Create a list entity]\(リスト エンティティの作成\)** ページで正規名と各正規名の同意語を入力して、 **[作成]** を選択します。

    > [!div class="mx-imgBorder"]
    > ![リスト エンティティに項目を追加する場面のスクリーンショット](media/luis-quickstart-intent-and-list-entity/add-pizza-crust-items-list-entity.png)

    LUIS アプリにリスト エンティティを追加する場合には、テキストにリスト エンティティの[ラベル](label-entity-example-utterance.md)を付ける必要はありません。 すべての意図のすべての発話が適用の対象となります。

## <a name="train-the-app-before-testing-or-publishing"></a>テストまたは発行する前に、アプリをトレーニングする

[!INCLUDE [LUIS How to Train steps](includes/howto-train.md)]

## <a name="publish-the-app-to-query-from-the-endpoint"></a>アプリを発行してエンドポイントからクエリを実行する

[!INCLUDE [LUIS How to Publish steps](includes/howto-publish.md)]

## <a name="get-intent-and-entity-prediction-from-endpoint"></a>エンドポイントから意図およびエンティティ予測を取得する

1. [!INCLUDE [LUIS How to get endpoint first step](includes/howto-get-endpoint.md)]

2. アドレスの URL の末尾に移動し、次の発話を入力します。

    `Deliver 2 deep dish hawaiian pizzas and a thin pepperoni`

    最後の querystring パラメーターは `query` です。これは発話の**クエリ**です。


    ```json
    {
        "query": "Deliver 2 deep dish hawaiian pizzas and a thin pepperoni",
        "prediction": {
            "topIntent": "OrderPizza",
            "intents": {
                "OrderPizza": {
                    "score": 0.9957229
                },
                "None": {
                    "score": 0.016832687
                },
                "Confirm": {
                    "score": 0.0015708931
                },
                "Greeting": {
                    "score": 0.00057060417
                }
            },
            "entities": {
                "number": [
                    2
                ],
                "CrustList": [
                    [
                        "Deep dish"
                    ],
                    [
                        "Thin"
                    ]
                ],
                "$instance": {
                    "number": [
                        {
                            "type": "builtin.number",
                            "text": "2",
                            "startIndex": 8,
                            "length": 1,
                            "modelTypeId": 2,
                            "modelType": "Prebuilt Entity Extractor",
                            "recognitionSources": [
                                "model"
                            ]
                        }
                    ],
                    "CrustList": [
                        {
                            "type": "CrustList",
                            "text": "deep dish",
                            "startIndex": 10,
                            "length": 9,
                            "modelTypeId": 5,
                            "modelType": "List Entity Extractor",
                            "recognitionSources": [
                                "model"
                            ]
                        },
                        {
                            "type": "CrustList",
                            "text": "thin",
                            "startIndex": 42,
                            "length": 4,
                            "modelTypeId": 5,
                            "modelType": "List Entity Extractor",
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

    完全一致として生地の種類が検出され、JSON で応答として返されました。 この情報は、クライアント アプリケーションが注文を処理する際に使用されます。

[!INCLUDE [LUIS How to clean up resources](includes/quickstart-tutorial-cleanup-resources.md)]

## <a name="related-information"></a>関連情報

* [リスト エンティティ](luis-concept-entity-types.md#list-entity)の概念に関する情報
* [トレーニング方法](luis-how-to-train.md)
* [発行方法](luis-how-to-publish-app.md)
* [LUIS ポータルでのテスト方法](luis-interactive-test.md)
* [概念 - エンティティ](luis-concept-entity-types.md)
* [正規表現エンティティの JSON リファレンス](reference-entity-regular-expression.md?tabs=V3)
* [データを抽出するエンティティを追加する方法](luis-how-to-add-entities.md)

## <a name="next-steps"></a>次の手順
このチュートリアルでは、発話の例を追加した後、発話からテキストの完全一致を抽出するためのリスト エンティティを作成しました。 トレーニングおよびアプリの発行後、エンドポイントのクエリによって意図を識別し、抽出されたデータを取得しました。

> [!div class="nextstepaction"]
> [ロールを備え、あらかじめ構築されているエンティティを追加する](tutorial-entity-roles.md)

