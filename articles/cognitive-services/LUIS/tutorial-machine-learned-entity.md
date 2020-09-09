---
title: 'チュートリアル: 機械学習エンティティを使用して構造化データを抽出する - LUIS'
description: 機械学習エンティティを使用して発話から構造化データを抽出します。 抽出精度を高めるには、特徴量を含むサブエンティティを追加します。
ms.topic: tutorial
ms.date: 05/08/2020
ms.openlocfilehash: eb9761a3d3a98a3318fe0adc6fa170652639a9a1
ms.sourcegitcommit: e132633b9c3a53b3ead101ea2711570e60d67b83
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/07/2020
ms.locfileid: "86045605"
---
# <a name="tutorial-extract-structured-data-from-user-utterance-with-machine-learning-entities-in-language-understanding-luis"></a>チュートリアル:Language Understanding (LUIS) で機械学習エンティティを使用して、ユーザーの発話から構造化データを抽出する

このチュートリアルでは、機械学習エンティティを使用して発話から構造化データを抽出します。

機械学習エンティティでは、[特徴量](luis-concept-feature.md)を含むサブエンティティ エンティティを提供することにより、[モデルの分解の概念](luis-concept-model.md#v3-authoring-model-decomposition)がサポートされます。

**このチュートリアルで学習する内容は次のとおりです。**

> [!div class="checklist"]
> * サンプル アプリをインポートする
> * 機械学習エンティティを追加する
> * サブエンティティと特徴量を追加する
> * アプリのトレーニング、テスト、発行を行う
> * エンドポイントからエンティティ予測を取得する

[!INCLUDE [LUIS Free account](includes/quickstart-tutorial-use-free-starter-key.md)]


## <a name="why-use-a-machine-learning-entity"></a>機械学習エンティティを使用する理由

このチュートリアルでは、機械学習エンティティを追加してユーザーの発話からデータを抽出します。

このエンティティは、発話内から抽出するデータを定義します。 たとえば、データの名前、型 (可能な場合)、あいまいなデータの解決方法、データを構成する正確なテキストなどを指定します。

データを定義するには、次のことを行う必要があります。
* エンティティの作成
* 例の発話内でテキストをラベル付けして、エンティティを表します。 このラベル付けされた例から、LUIS はエンティティの内容と、それが発話のどこに存在するかを学習します。

## <a name="entity-decomposability-is-important"></a>エンティティの分解可能性が重要

エンティティの分解可能性は、そのエンティティを使用した意図の予測とデータの抽出の両方にとって重要です。

まずは機械学習エンティティから開始します。これは、データ抽出用の最初かつ最上位のエンティティです。 次に、エンティティをサブエンティティに分解します。

最初はエンティティをどこまで細かくするかわからないかもしれません。ベスト プラクティスとしては、機械学習エンティティから開始し、アプリの成熟に合わせてサブエンティティに分解することをお勧めします。

このチュートリアルでは、ピザ アプリの注文を表す機械学習エンティティを作成します。 エンティティは注文に関連したテキストを抽出し、サイズと数量を取り出します。

`Please deliver one large cheese pizza to me` の発話は、注文として `one large cheese pizza` を抽出し、さらに数量として `1` とサイズとして `large` を抽出します。

## <a name="download-json-file-for-app"></a>アプリの JSON ファイルをダウンロードする

[アプリの JSON ファイル](https://raw.githubusercontent.com/Azure-Samples/cognitive-services-language-understanding/master/documentation-samples/tutorials/machine-learned-entity/pizza-intents-only.json)をダウンロードして保存します。

## <a name="import-json-file-for-app"></a>アプリの JSON ファイルをインポートする

[!INCLUDE [Import app steps](includes/import-app-steps.md)]

## <a name="create-machine-learned-entity"></a>機械学習済みエンティティを作成する

ピザの注文に関する詳細を抽出するには、最上位の機械学習エンティティ `Order` を作成します。

1. **[Intents]\(意図\)** ページで、 **[OrderPizza]** 意図を選択します。

1. 発話の例の一覧で、次の発話を選択します。

    |注文の発話の例|
    |--|
    |`pickup a cheddar cheese pizza large with extra anchovies`|

    最初に左端のテキスト `pickup` の直前を選択し (#1)、次に右端のテキスト `anchovies` の直後に移動します (#2 - これでラベル付けプロセスは終了です)。 ポップアップ メニューが表示されます。 ポップアップ ボックスに、エンティティの名前として `Order` を入力します (#3)。 次に、一覧から `Order Create new entity` を選択します (#4)。

    ![完全な注文のテキストの先頭と末尾にラベルを付る](media/tutorial-machine-learned-entity/mark-complete-order.png)

    > [!NOTE]
    > エンティティが常に発話全体であるとは限りません。 この特定のケースで、`pickup` は、注文の受け取り方法を示しています。 概念上の観点から、`pickup` は、注文の、ラベル付けされたエンティティの一部である必要があります。

1. **[Choose an entity type]\(エンティティ型の選択\)** ボックスで、 **[Add Structure]\(構造の追加\)** を選択し、 **[次へ]** を選択します。 構造は、サイズや数量などのサブエンティティを追加するために必要です。

    ![エンティティに構造を追加する](media/tutorial-machine-learned-entity/add-structure-to-entity.png)

1. **[Add subentities (optional)]\(サブエンティティの追加 (省略可能)\)** ボックスで、`Order` 行の **+** を選択し、次に、`Size` と `Quantity` をサブエンティティとして追加してから、 **[作成]** を選択します。

    > [!div class="mx-imgBorder"]
    > ![エンティティに構造を追加する](media/tutorial-machine-learned-entity/add-subentities-when-creating-machine-learned-entity.png)

## <a name="edit-subentities-to-improve-extraction"></a>サブエンティティを編集して抽出を改善する

前の手順で、エンティティとサブエンティティが作成されています。 抽出を改善するには、サブエンティティに特徴量を追加します。

### <a name="improve-size-extraction-with-phrase-list"></a>語句一覧を使用してサイズの抽出を改善する

1. 左側のメニューから **[エンティティ]** を選択して、**Order** エンティティを選択します。

1. **[Schema and features]\(スキーマと特徴量\)** タブで、**Size** サブエンティティを選択して、 **[+ Add feature]\(+ 特徴量の追加\)** を選択します。

1. ドロップダウン メニューで **[Create new phrase list]\(新しい語句一覧の作成\)** を選択します。

1. **[Create new phrase list]\(新しい語句一覧の作成\)** ボックスに名前「`SizePhraselist`」を入力し、値 `small`、`medium`、`large` を入力します。 **[Suggestions]\(候補\)** ボックスに候補が表示されたら、`extra large` と `xl` を選択します。 **[作成]** を選択すると、新しい語句一覧が作成されます。

    この語句一覧の特徴量によって単語の例が提供されるため、`Size` サブエンティティがサイズに関連する単語を見つけやすくなります。 この語句一覧にサイズに関連するすべての単語が含まれている必要はありませんが、サイズを示すことが想定される単語は含まれている必要があります。

### <a name="add-sizelist-entity"></a>SizeList エンティティを追加する

クライアント アプリケーションで認識される既知のサイズの一覧を追加することも、抽出に役立ちます。

1. 左側のメニューから **[エンティティ]** を選択して、 **[+ 作成]** を選択します。

1. エンティティ名を `SizeListentity` とし、型を **[リスト]** として設定します。これにより、前のセクションで作成した `SizePhraselist` と比べて容易に識別することができます。

1. クライアント アプリケーションで想定されるサイズ (`Small`、`Medium`、`Large`、および `XLarge`) を追加してから、それぞれにシノニムを追加します。 シノニムは、ユーザーがチャット ボットに入力する用語とする必要があります。 エンティティは、正規化された値またはシノニムと正確に一致すると、リスト エンティティと共に抽出されます。

    |正規化された値|シノニム|
    |--|--|
    |Small|sm、sml、tiny、smallest|
    |Medium|md、mdm、regular、average、middle|
    |Large|lg、lrg、big|
    |XLarge|xl、biggest、giant|


    > [!div class="mx-imgBorder"]
    > ![エンティティに構造を追加する](media/tutorial-machine-learned-entity/size-list-entity-with-synonyms.png)

### <a name="add-feature-of-sizelist-entity"></a>SizeList エンティティの特徴量を追加する

1. 左側のメニューから **[エンティティ]** を選択して、エンティティの一覧に戻ります。

1. エンティティの一覧から **Order** を選択します。

1. **[Schema and features]\(スキーマと特徴量\)** タブで、**Size** エンティティを選択して、 **[+ Add feature]\(+ 特徴量の追加\)** を選択します。

1. ドロップダウン リストから **[@ SizeListentity]** を選択します。

<a name="add-prebuilt-number-entity-to-app"></a>

### <a name="add-prebuilt-number-entity"></a>事前構築済みの number エンティティを追加する

事前構築済みの number エンティティを追加することも抽出に役立ちます。

1. 左側のメニューから **[エンティティ]** を選択して、 **[事前構築済みのエンティティの追加]** を選択します。

1. その一覧から **[Number]** を選択し、 **[完了]** を選択します。

1. 左側のメニューから **[エンティティ]** を選択して、エンティティの一覧に戻ります。

### <a name="add-feature-of-prebuilt-number-entity"></a>事前構築済みの number エンティティの特徴量を追加する

1. エンティティの一覧から **Order** を選択します。

1. **[Schema and features]\(スキーマと特徴量\)** タブで、**Quantity** エンティティを選択して、 **[+ Add feature]\(+ 特徴量の追加\)** を選択します。

1. ドロップダウン リストから **@ number** を選択します。

<a name="create-subcomponent-entity-with-constraint-to-help-extract-data"></a>

## <a name="configure-required-features"></a>必須の特徴量を構成する

**Order** エンティティに関するエンティティ詳細ページで、 **@ SizeList** 特徴量と **@ number** 特徴量の両方に対して、アスタリスク `*` を選択します。 アスタリスクは、機能名と同じラベルに表示されます。

> [!div class="mx-imgBorder"]
> ![エンティティに構造を追加する](media/tutorial-machine-learned-entity/set-required-feature-on-subentity.png)

<a name="label-text-as-entities-in-example-utterances"></a>
<a name="label-example-utterance-to-teach-luis-about-the-entity"></a>

## <a name="label-example-utterances"></a>ラベルの発話の例

機械学習済みエンティティが作成されると、そのサブエンティティには特徴量が与えられます。 抽出の改善を完了するには、発話の例にサブエンティティでラベルを付ける必要があります。

1. 左側のナビゲーションから **[Intents]\(意図\)** を選択し、次に **[OrderPizza]** 意図を選択します。

1. **[エンティティ パレット]** を開くには、コンテキスト ツールバーで **@** 記号を選択します。

1. パレットで各エンティティ行を選択してから、パレット カーソルを使用して発話の各例のエンティティを選択します。 完了すると、エンティティの一覧は次の図のようになります。

    > [!div class="mx-imgBorder"]
    > ![必須の特徴量を構成しているところを示す部分的なスクリーンショット](media/tutorial-machine-learned-entity/labeled-example-utterances-for-machine-learned-entity.png)

## <a name="train-the-app"></a>アプリをトレーニングする

アプリをトレーニングするには、 **[トレーニング]** を選択します。 トレーニングにより、新しいエンティティやラベル付けされた発話などの変更が、アクティブなモデルに適用されます。

## <a name="add-a-new-example-utterance"></a>新しい発話の例を追加する

1. トレーニング後、発話の新しい例を意図 `OrderPizza` に追加して、LUIS で機械学習エンティティがどのくらい良く理解されているかを確認します。

    |注文の発話の例|
    |--|
    |`I need a large pepperoni pizza`|

    全体の最上位エンティティである `Order` と共に、`Size` サブエンティティも点線でラベル付けされています。

    > [!div class="mx-imgBorder"]
    > ![エンティティで予測される新しい発話の例の部分的なスクリーンショット](media/tutorial-machine-learned-entity/new-example-utterance-predicted-with-entity.png)

    点線は、現在のトレーニング済みアプリに基づく予測を示しています。

1. 予測をラベル付きエンティティに変更するには、同じ行にあるチェック マークを選択します。

    > [!div class="mx-imgBorder"]
    > ![エンティティで予測される新しい発話の例の部分的なスクリーンショット](media/tutorial-machine-learned-entity/confirm-entity-prediction-for-new-example-utterance-added.png)

    この時点で、機械学習エンティティは新しい発話例内でエンティティを見つけることができているため、正常に機能しています。 発話の例を追加したとき、エンティティが正しく予測されない場合は、エンティティとサブエンティティにラベルを付けます。 エンティティが正しく予測されている場合は、必ず予測を確定してください。


## <a name="train-the-app-to-apply-the-entity-changes-to-the-app"></a>アプリをトレーニングしてエンティティの変更をアプリに適用する

**[トレーニング]** を選択し、この新しい発話を使用してアプリをトレーニングします。

この時点で、注文には抽出可能な詳細 (サイズ、数量、完全な注文のテキスト) が含まれています。 `Order` エンティティには、ピザのトッピング、生地の種類、サイド オーダーなど、さらに詳しい情報が追加されていきます。 これらについては、それぞれを `Order` エンティティのサブエンティティとして作成する必要があります。

## <a name="test-the-app-to-validate-the-changes"></a>アプリをテストして変更を検証する

対話型の **[テスト]** パネルを使用してアプリをテストします。 このプロセスでは、新しい発話を入力して予測結果を表示し、トレーニングしたアクティブなアプリのパフォーマンスを確認できます。 意図の予測はかなり高い精度 (60% 以上) になり、エンティティの抽出では少なくとも `Order` エンティティが選択されるはずです。 このような少数の発話だけですべてのケースを処理することはできないため、Order エンティティの詳細は不足する可能性があります。

1. 上部のナビゲーションの **[Test]\(テスト\)** を選択します。
1. 発話 `2 small cheese pizzas for pickup` を入力し、Enter キーを押します。 アクティブ モデルでは 60% 以上の精度で正しい意図が予測されました。


1. **[検査]** を選択してエンティティの予測を表示します。

    > [!div class="mx-imgBorder"]
    > ![対話型のテスト パネルでエンティティの予測が表示されている部分的なスクリーンショット](media/tutorial-machine-learned-entity/interactive-test-panel-with-first-utterance-and-entity-predictions.png)

<a name="publish-with-sentiment-analysis"></a>

## <a name="publish-the-app-to-access-it-from-the-http-endpoint"></a>アプリを発行して HTTP エンドポイントからアクセスする

チャットボットや他のクライアント アプリケーションで LUIS の予測を受け取るには、アプリをエンドポイントに公開する必要があります。

1. 右上のナビゲーションで **[公開]** を選択します。

    ![LUIS の右上のメニューにあるエンドポイントへの公開ボタンのスクリーンショット](./media/howto-publish/publish-button.png)

1. **[運用]** スロットを選択して、 **[設定の変更]** を選択します。 **[感情分析]** を選択して、 **[完了]** を選択します。

    > [!div class="mx-imgBorder"]
    > ![LUIS のエンドポイントへの公開のスクリーンショット](./media/tutorial-machine-learned-entity/publish-with-sentiment-analysis.png)

1. 通知の **[Access your endpoint URLs]\(エンドポイントの URL にアクセス\)** リンクを選択して **[Azure リソース]** ページに移動します。 エンドポイントの URL が **[Example Query]\(サンプル クエリ\)** として一覧表示されます。

## <a name="get-intent-and-entity-prediction-from-http-endpoint"></a>HTTP エンドポイントから意図およびエンティティ予測を取得する

1. [!INCLUDE [LUIS How to get endpoint first step](includes/howto-get-endpoint.md)]

1. アドレス バーで URL の末尾に移動し、_YOUR_QUERY_HERE_ を、対話型テスト パネルで入力したクエリに置き換えます。

    `2 small cheese pizzas for pickup`

    最後の querystring パラメーターは `query` です。これは発話の**クエリ**です。

    ```json
    {
        "query": "2 small cheese pizzas for pickup",
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
                            "small"
                        ],
                        "Quantity": [
                            2
                        ]
                    }
                ]
            }
        },
        "sentimentAnalysis":{
            "label":"neutral",
            "score":0.98
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

## <a name="next-steps"></a>次のステップ

このチュートリアルでは、アプリで機械学習エンティティを使用して、ユーザーの発話の意図を見つけ、その発話から詳細を抽出します。 機械学習エンティティを使用すると、エンティティを詳細に分解できます。

> [!div class="nextstepaction"]
> [事前構築済みの KeyPhrase エンティティを追加する](luis-quickstart-intent-and-key-phrase.md)
