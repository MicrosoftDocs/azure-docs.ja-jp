---
title: 'チュートリアル: 機械学習エンティティを使用して構造化データを抽出する - LUIS'
titleSuffix: Azure Cognitive Services
description: 機械学習エンティティを使用して発話から構造化データを抽出します。 抽出精度を上げるために、サブコンポーネントとその記述子および制約を追加します。
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: tutorial
ms.date: 12/05/2019
ms.author: diberry
ms.openlocfilehash: 2d3bffd025d484ac928e21003b7cba9c63d2c514
ms.sourcegitcommit: 8bd85510aee664d40614655d0ff714f61e6cd328
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/06/2019
ms.locfileid: "74885908"
---
# <a name="tutorial-extract-structured-data-from-user-utterance-with-machine-learned-entities-in-language-understanding-luis"></a>チュートリアル:Language Understanding (LUIS) で機械学習エンティティを使用して、ユーザーの発話から構造化データを抽出する

このチュートリアルでは、機械学習エンティティを使用して発話から構造化データを抽出します。

機械学習エンティティでは、サブコンポーネント エンティティとその記述子および制約を提供することにより、[モデルの分解の概念](luis-concept-model.md#v3-authoring-model-decomposition)がサポートされます。

[!INCLUDE [Uses preview portal](includes/uses-portal-preview.md)]

**このチュートリアルで学習する内容は次のとおりです。**

> [!div class="checklist"]
> * サンプル アプリをインポートする
> * 機械学習エンティティを追加する
> * サブコンポーネントを追加する
> * サブコンポーネントの記述子を追加する
> * サブコンポーネントの制約を追加する
> * アプリのトレーニング
> * アプリをテストする
> * アプリの発行
> * エンドポイントからエンティティ予測を取得する

[!INCLUDE [LUIS Free account](includes/quickstart-tutorial-use-free-starter-key.md)]


## <a name="why-use-a-machine-learned-entity"></a>機械学習エンティティを使用する理由

このチュートリアルでは、機械学習エンティティを追加して発話からデータを抽出します。

エンティティの目的は、抽出するデータを定義することです。 たとえば、データの名前、型 (可能な場合)、あいまいなデータの解決方法、データを構成する正確なテキストなどを指定します。

エンティティを定義するには、エンティティを作成した後、発話の例でそのエンティティを表すテキストにラベルを付ける必要があります。 このラベル付けされた例から、LUIS はエンティティの内容と、それが発話のどこに存在するかを学習します。

## <a name="entity-decomposability-is-important"></a>エンティティの分解可能性が重要

エンティティの分解可能性は、意図の予測とデータの抽出の両方にとって重要です。

まずは機械学習エンティティから開始します。これは、データ抽出の先頭かつ最上位のエンティティです。 次に、このエンティティを、クライアント アプリケーションによって必要とされるパーツに分解します。

最初はエンティティをどこまで細かくするかわからないかもしれません。ベスト プラクティスとしては、機械学習エンティティから開始し、アプリの成熟に応じてサブコンポーネントに分解することをお勧めします。

ここでは、ピザ アプリの注文を表す機械学習エンティティを作成します。 注文には、注文を満たすために必要なすべてのパーツが含まれている必要があります。 最初に、エンティティは注文に関連したテキストを抽出し、サイズと数量を取り出します。

`Please deliver one large cheese pizza to me` の発話は、注文として `one large cheese pizza` を抽出し、さらに `1` と `large` を抽出します。

トッピングや生地用のサブコンポーネントを作成するなど、さらに分解して追加できます。 このチュートリアルを完了したら、自信を持ってこれらのサブコンポーネントを自身の既存の `Order` エンティティに追加できるようになるはずです。

## <a name="import-example-json-to-begin-app"></a>サンプル .json をインポートしてアプリを開始する

1.  [アプリの JSON ファイル](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/documentation-samples/tutorials/machine-learned-entity/pizza-intents-only.json)をダウンロードして保存します。

[!INCLUDE [Import app steps](includes/import-app-steps.md)]

## <a name="label-text-as-entities-in-example-utterances"></a>発話の例でテキストにエンティティのラベルを付ける

ピザの注文に関する詳細を抽出するには、最上位レベルの機械学習 `Order` エンティティを作成します。

1. **[Intents]\(意図\)** ページで、 **[OrderPizza]** 意図を選択します。

1. 発話の例の一覧で、次の発話を選択します。

    |注文の発話の例|
    |--|
    |`pickup a cheddar cheese pizza large with extra anchovies`|

    最初に左端のテキスト `pickup` の直前を選択し (#1)、次に右端のテキスト `anchovies` の直後に移動します (#2 - これでラベル付けプロセスは終了です)。 ポップアップ メニューが表示されます。 ポップアップ ボックスに、エンティティの名前として `Order` を入力します (#3)。 次に、一覧から `Order - Create new entity` を選択します (#4)。

    ![完全な注文のテキストの先頭と末尾にラベルを付る](media/tutorial-machine-learned-entity/mark-complete-order.png)

    > [!NOTE]
    > エンティティが常に発話全体であるとは限りません。 この特定のケースで、`pickup` は、注文の受け取り方法を示しています。 概念上の観点から、`pickup` は、注文の、ラベル付けされたエンティティの一部である必要があります。

1. **[Choose an entity type]\(エンティティ型の選択\)** ボックスで、 **[Add Structure]\(構造の追加\)** を選択し、 **[次へ]** を選択します。 構造は、サイズや数量などのサブコンポーネントを追加するために必要です。

    ![エンティティに構造を追加する](media/tutorial-machine-learned-entity/add-structure-to-entity.png)

1. **[Create a machine learned entity]\(機械学習エンティティの作成\)** ボックスで、 **[Structure]\(構造\)** ボックスに `Size` を追加し、Enter キーを押します。
1. **記述子**を追加するには、 **[Descriptors for Size]\(Size の記述子\)** 領域で `+` を選択し、 **[Create new phrase list]\(新しい語句一覧の作成\)** を選択します。

1. **[Create new phrase list descriptor]\(新しい語句一覧の記述子の作成\)** ボックスに名前 `SizeDescriptor` を入力し、値 `small`、`medium`、`large` を入力します。 **[Suggestions]\(候補\)** ボックスに候補が表示されたら、`extra large` と `xl` を選択します。 **[完了]** を選択すると、新しい語句一覧が作成されます。

    この語句一覧の記述子によって単語の例が提供されるため、`Size` サブコンポーネントがサイズに関連する単語を見つけやすくなります。 この一覧にサイズに関連するすべての単語が含まれている必要はありませんが、サイズを示すことが想定される単語は含まれている必要があります。

    ![Size サブコンポーネントの記述子を作成する](media/tutorial-machine-learned-entity/size-entity-size-descriptor-phrase-list.png)

1. **[Create a machine learned entity]\(機械学習エンティティの作成\)** ウィンドウで、 **[作成]** を選択して `Size` サブコンポーネントの作成を完了します。

    `Size` コンポーネントを持つ `Order` エンティティが作成されますが、発話に適用されているのは `Order` エンティティのみです。 発話の例で `Size` エンティティのテキストにラベルを付ける必要があります。

1. 同じ発話の例で、`large` に **Size** サブコンポーネントのラベルを付けます。それには、この単語を選択し、ドロップダウン リストから **Size** エンティティを選択します。

    ![発話内のテキストに Size エンティティのラベルを付けます。](media/tutorial-machine-learned-entity/mark-and-create-size-entity.png)

    テキストに下線が付いているのは、明示的にテキストにラベルを付けたためにラベルと予測が一致しているからです。

1. 残りの発話で、Size エンティティと共に `Order` エンティティのラベルを付けます。 角かっこ内のテキストは、ラベル付けされた `Order` エンティティと、その内の `Size` エンティティを示しています。

    |注文の発話の例|
    |--|
    |`can i get [a pepperoni pizza and a can of coke] please`|
    |`can i get [a [small] pizza with onions peppers and olives]`|
    |`[delivery for a [small] pepperoni pizza]`|
    |`i need [2 [large] cheese pizzas 6 [large] pepperoni pizzas and 1 [large] supreme pizza]`|

    ![残りのすべての発話の例で、エンティティとサブコンポーネントを作成します。](media/tutorial-machine-learned-entity/entity-subentity-labeled-not-trained.png)

    > [!CAUTION]
    > 文字 `a` が 1 枚のピザを暗示している場合など、暗黙的なデータはどのように扱えばよいのでしょうか。 または、ピザの受け取り方法を示す `pickup` や `delivery` が不足している場合や、 または既定サイズであるスモールやラージを示すサイズが不足している場合はどうでしょうか。 暗黙的なデータの処理は、LUIS の代わりとして、または追加してではなく、クライアント アプリケーションでビジネス ルールの一部として扱うことを検討してください。

1. アプリをトレーニングするには、 **[トレーニング]** を選択します。 トレーニングにより、新しいエンティティやラベル付けされた発話などの変更が、アクティブなモデルに適用されます。

1. トレーニング後、発話の新しい例を意図に追加して、LUIS が機械学習したエンティティをどのくらい良く理解しているかを確認します。

    |注文の発話の例|
    |--|
    |`pickup XL meat lovers pizza`|

    全体の最上位エンティティである `Order` と共に、`Size` サブコンポーネントも点線でラベル付けされています。 これは正常な予測です。

    ![エンティティを使用して予測された新しい発話の例](media/tutorial-machine-learned-entity/new-example-utterance-predicted-with-entity.png)

    点線は予測を示しています。

1. 予測をラベル付けされたエンティティに変更するには、行を選択し、 **[Confirm entity predictions]\(エンティティ予測を確定\)** を選択します。

    ![[Confirm entity predictions]\(エンティティ予測を確定\) を選択して、予測を受け入れます。](media/tutorial-machine-learned-entity/confirm-entity-prediction-for-new-example-utterance.png)

    この時点で、機械学習エンティティは新しい発話例内でエンティティを見つけることができているため、正常に機能しています。 発話の例を追加したとき、エンティティが正しく予測されない場合は、エンティティとサブコンポーネントにラベルを付けます。 エンティティが正しく予測されている場合は、必ず予測を確定してください。

## <a name="add-prebuilt-number-to-help-extract-data"></a>データ抽出に役立つ事前構築済みの数を追加する

注文情報には、ピザの数など、注文内のアイテムの数も含まれている必要があります。 このデータを抽出するには、新しい機械学習サブコンポーネントを `Order` に追加する必要があります。また、そのコンポーネントには、事前構築済みの数の制約が必要です。 エンティティを事前構築済みの数に制限することにより、テキストが数値の `2` であってもテキストの `two` であっても、エンティティによって数が検出および抽出されます。

まず、事前構築済みの数のエンティティをアプリに追加します。

1. 左側のメニューから **[Entities]\(エンティティ\)** を選択し、 **[Add prebuilt entity]\(事前構築済みのエンティティの追加\)** を選択します。

1. **[Add prebuilt entity]\(事前構築済みのエンティティの追加\)** ボックスで、 **[number]\(数\)** を検索して選択し、 **[完了]** を選択します。

    ![事前構築済みのエンティティを追加する](media/tutorial-machine-learned-entity/add-prebuilt-entity-as-constraint-to-quantity-subcomponent.png)

    事前構築済みのエンティティはアプリに追加されますが、制約はまだありません。

## <a name="create-subcomponent-entity-with-constraint-to-help-extract-data"></a>データ抽出に役立つ制約付きサブコンポーネント エンティティを作成する

`Order` エンティティには、注文内のアイテムの数を決定する `Quantity` サブコンポーネントが必要です。 クライアント アプリケーションが抽出されたデータをすぐに使用できるように、Quantity は数に制限する必要があります。

制約は、完全一致 (リスト エンティティなど) か正規表現 (正規表現エンティティや事前構築済みのエンティティなど) のいずれかを使用し、テキスト一致として適用されます。

制約を使用すると、その制約に一致するテキストのみが抽出されます。

1. **[エンティティ]** を選択し、`Order` エンティティを選択します。
1. **[+ コンポーネントの追加]** を選択して名前 `Quantity` を入力し、Enter キーを押して新しいエンティティをアプリに追加します。
1. 処理が完了したことが通知されたら、`Quantity` サブコンポーネントを選択し、制約の鉛筆アイコンを選択します。
1. ドロップダウン リストで、事前構築済みの数を選択します。

    ![事前構築済みの数を制約として使用して Quantity エンティティを作成します。](media/tutorial-machine-learned-entity/create-constraint-from-prebuilt-number.png)

    `Quantity` エンティティは、事前に構築された数のエンティティに一致するテキストが見つかった場合にのみ適用されます。

    制約付きのエンティティが作成されますが、発話の例にはまだ適用されていません。

    > [!NOTE]
    > サブコンポーネント内にサブコンポーネントを入れ子にする場合は、最大で 5 レベルになるようにします。 この記事では説明しませんが、これはポータルと API から利用できます。

## <a name="label-example-utterance-to-teach-luis-about-the-entity"></a>エンティティについて LUIS に教えるために、発話の例にラベルを付ける

1. 左側のナビゲーションから **[Intents]\(意図\)** を選択し、次に **[OrderPizza]** 意図を選択します。 次の発話の 3 つの数値にはラベルが付けられていますが、`Order` エンティティの線の下に表示されています。 これは、見つかったエンティティが、`Order` エンティティから分離されているとは見なされていないことを意味します。

    ![事前構築済みの数が見つかりましたが、まだ Order エンティティから分離しているとは見なされていません。](media/tutorial-machine-learned-entity/prebuilt-number-not-part-of-order-entity.png)

1. 発話の例で `2` を選択した後、一覧から `Quantity` を選択することで、数に `Quantity` エンティティのラベルを付けます。 同じ発話の例で、`6` と `1` にラベルを付けます。

    ![テキストに Quantity エンティティのラベルを付けます。](media/tutorial-machine-learned-entity/mark-example-utterance-with-quantity-entity.png)

## <a name="train-the-app-to-apply-the-entity-changes-to-the-app"></a>アプリをトレーニングしてエンティティの変更をアプリに適用する

**[トレーニング]** を選択し、これらの新しい発話を使用してアプリをトレーニングします。

![アプリをトレーニングした後、発話の例を確認します。](media/tutorial-machine-learned-entity/trained-example-utterances.png)

この時点で、注文には抽出可能な詳細 (サイズ、数量、完全な注文のテキスト) が含まれています。 `Order` エンティティには、ピザのトッピング、生地の種類、サイド オーダーなど、さらに詳しい情報が追加されていきます。 これらについては、それぞれを `Order` エンティティのサブコンポーネントとして作成する必要があります。

## <a name="test-the-app-to-validate-the-changes"></a>アプリをテストして変更を検証する

対話型の **[テスト]** パネルを使用してアプリをテストします。 このプロセスでは、新しい発話を入力して予測結果を表示し、トレーニングしたアクティブなアプリのパフォーマンスを確認できます。 意図の予測はかなり高い精度 (70% 以上) になり、エンティティの抽出では少なくとも `Order` エンティティが選択されるはずです。 5 件の発話だけですべてのケースを処理することはできないため、Order エンティティの詳細は不足する可能性があります。

1. 上部のナビゲーションの **[Test]\(テスト\)** を選択します。
1. 発話 `deliver a medium veggie pizza` を入力し、Enter キーを押します。 アクティブ モデルは 70% 以上の精度で正しい意図を予測しました。

    ![新しい発話を入力して意図をテストします。](media/tutorial-machine-learned-entity/interactive-test-panel-with-first-utterance.png)

1. **[検査]** を選択してエンティティの予測を表示します。

    ![対話型のテスト パネルでエンティティの予測を表示します。](media/tutorial-machine-learned-entity/interactive-test-panel-with-first-utterance-and-entity-predictions.png)

    サイズが正しく識別されました。 `OrderPizza` 意図の発話の例に `medium` サイズの例はありませんが、medium を含む `SizeDescriptor` 語句リストの記述子が使用されている点に注意してください。

    数量は正しく予測されていません。 これを修正するには、数量を示す単語を使用した発話の例をさらに追加し、その単語に `Quantity` エンティティのラベルを付けます。

## <a name="publish-the-app-to-access-it-from-the-http-endpoint"></a>アプリを発行して HTTP エンドポイントからアクセスする

[!INCLUDE [LUIS How to Publish steps](includes/howto-publish.md)]

## <a name="get-intent-and-entity-prediction-from-http-endpoint"></a>HTTP エンドポイントから意図およびエンティティ予測を取得する

1. [!INCLUDE [LUIS How to get endpoint first step](includes/howto-get-endpoint.md)]

1. アドレスの URL の末尾に移動し、対話型のテスト パネルに入力したものと同じクエリを入力します。

    `deliver a medium veggie pizza`

    最後の querystring パラメーターは `query` です。これは発話の**クエリ**です。

    ```json
    {
        "query": "deliver a medium veggie pizza",
        "prediction": {
            "topIntent": "OrderPizza",
            "intents": {
                "OrderPizza": {
                    "score": 0.7812769
                },
                "None": {
                    "score": 0.0314020254
                },
                "Confirm": {
                    "score": 0.009299271
                },
                "Greeting": {
                    "score": 0.007551549
                }
            },
            "entities": {
                "Order": [
                    {
                        "Size": [
                            "medium"
                        ],
                        "$instance": {
                            "Size": [
                                {
                                    "type": "Size",
                                    "text": "medium",
                                    "startIndex": 10,
                                    "length": 6,
                                    "score": 0.9955588,
                                    "modelTypeId": 1,
                                    "modelType": "Entity Extractor",
                                    "recognitionSources": [
                                        "model"
                                    ]
                                }
                            ]
                        }
                    }
                ],
                "$instance": {
                    "Order": [
                        {
                            "type": "Order",
                            "text": "a medium veggie pizza",
                            "startIndex": 8,
                            "length": 21,
                            "score": 0.7983857,
                            "modelTypeId": 1,
                            "modelType": "Entity Extractor",
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


[!INCLUDE [LUIS How to clean up resources](includes/quickstart-tutorial-cleanup-resources.md)]

## <a name="related-information"></a>関連情報

* [チュートリアル - 意図](luis-quickstart-intents-only.md)
* [概念 - エンティティ](luis-concept-entity-types.md)の概念情報
* [概念 - 機能](luis-concept-feature.md)の概念情報
* [トレーニング方法](luis-how-to-train.md)
* [発行方法](luis-how-to-publish-app.md)
* [LUIS ポータルでのテスト方法](luis-interactive-test.md)

## <a name="next-steps"></a>次の手順

このチュートリアルでは、アプリで機械学習エンティティを使用して、ユーザーの発話の意図を見つけ、その発話から詳細を抽出します。 機械学習エンティティを使用すると、エンティティを詳細に分解できます。

> [!div class="nextstepaction"]
> [事前構築済みの KeyPhrase エンティティを追加する](luis-quickstart-intent-and-key-phrase.md)
