---
title: バッチ テストの実行方法 - LUIS
titleSuffix: Azure Cognitive Services
description: Language Understanding (LUIS) バッチ テスト セットを使用して、不適切な意図とエンティティを含む発話を見つけます。
services: cognitive-services
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: how-to
ms.date: 12/29/2020
ms.openlocfilehash: b297330f3562babf9e83d36934827f7b92d5ea35
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "98787014"
---
# <a name="batch-testing-with-a-set-of-example-utterances"></a>発話の例のセットを使用したバッチ テスト

バッチ テストでは、アクティブなトレーニング済みバージョンを検証して、その予測精度を測定します。 バッチ テストは、アクティブなバージョンに含まれる各意図やエンティティの精度を確認するのに役立ちます。 バッチ テストの結果を確認して、アプリが正しい意図の識別に頻繁に失敗する場合は意図により多くの発話の例を追加したり、発話内のエンティティをラベル付けしたりするなど、精度を向上させるための適切なアクションを実行します。

## <a name="group-data-for-batch-test"></a>バッチ テスト用のグループ データ

バッチ テストに使用される発話が、LUIS にとって初めてであることが重要です。 発話のデータ セットがある場合は、発話を 3 つのセットに分割します。意図に追加されるサンプルの発話、公開されているエンドポイントから受信した発話、およびトレーニング後に LUIS をバッチ テストするために使用される発話です。

使用するバッチ JSON ファイルには、発話と共に、開始位置と終了位置を含むラベル付けされた機械学習エンティティが最上位に存在する必要があります。 既にアプリに存在する例の一部を発話に使用することは避けてください。 意図やエンティティを肯定的に予測したいと考える発話を使用する必要があります。

テストは意図やエンティティごとに分けてもかまいませんし、すべてのテスト (発話の数は 1,000 個まで) を同じファイルに含めてもかまいません。 

### <a name="common-errors-importing-a-batch"></a>バッチ インポートでの一般的なエラー

バッチ ファイルを LUIS にアップロードするときにエラーが発生した場合は、次の一般的な問題について確認してください。

* 1 つのバッチ ファイルに 1,000 を超える発話がある。
* エンティティ プロパティがない発話 JSON オブジェクト。 プロパティは空の配列でもかまいません。
* 複数のエンティティにラベルが付いている単語
* 先頭または末尾がスペースであるエンティティ ラベル。

## <a name="fixing-batch-errors"></a>バッチのエラーの修正

バッチ テストでエラーがある場合は、より多くの発話を意図に追加したり、LUIS が意図を区別する助けとなるエンティティを使用して、より多くの発話にラベル付けしたりできます。 発話を追加し、それらにラベル付けしてもバッチ テストで予測エラーが発生する場合は、LUIS がより迅速に学習する助けになるように、ドメイン固有のボキャブラリを含む[語句一覧](luis-concept-feature.md)機能を追加することを検討してください。


<a name="batch-testing"></a>

## <a name="batch-testing-using-the-luis-portal"></a>LUIS ポータルを使用したバッチ テスト 

### <a name="import-and-train-an-example-app"></a>サンプル アプリをインポートしてトレーニングする

ピザの注文 (例: `1 pepperoni pizza on thin crust`) を受け付けるアプリをインポートします。

1.  [アプリの JSON ファイル](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/luis/apps/pizza-with-machine-learned-entity.json?raw=true)をダウンロードして保存します。

1. [LUIS ポータル](https://www.luis.ai)にサインインし、自分の **サブスクリプション** と **作成リソース** を選択して、その作成リソースに割り当てられているアプリを表示します。
1. **[新しいアプリ]** の横にある矢印を選択し、 **[JSON としてインポート]** をクリックして新しいアプリに JSON をインポートします。 アプリに `Pizza app` という名前を付けます。


1. ナビゲーションの右上隅にある **[Train]\(トレーニング\)** を選択して、アプリのトレーニングを行います。


[!INCLUDE [Entity roles in batch testing - currently not supported](../../../includes/cognitive-services-luis-roles-not-supported-in-batch-testing.md)]

### <a name="batch-test-file"></a>バッチ テスト ファイル

サンプル JSON には、テスト ファイルの体裁を示すために、ラベル付けされたエンティティを含む発話が 1 つ含まれています。 実際のテストでは、適切な意図と機械学習エンティティがラベル付けされた発話を多数含める必要があります。

1. テキスト エディターで `pizza-with-machine-learned-entity-test.json` を作成するか、または[ダウンロード](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/luis/batch-tests/pizza-with-machine-learned-entity-test.json?raw=true)します。

2. JSON 形式のバッチ ファイルで、テストで予測されるようにしたい **意図** を持つ発話を追加します。

   [!code-json[Add the intents to the batch test file](~/samples-cognitive-services-data-files/luis/batch-tests/pizza-with-machine-learned-entity-test.json "Add the intent to the batch test file")]

## <a name="run-the-batch"></a>バッチを実行する

1. 上部のナビゲーション バーの **[Test]\(テスト\)** を選択します。

2. 右側のパネルで、 **[Batch testing panel]\(バッチ テスト パネル\)** を選択します。

    ![バッチ テストのリンク](./media/luis-how-to-batch-test/batch-testing-link.png)

3. **[インポート]** を選択します。 表示されるダイアログ ボックスで、 **[ファイルの選択]** を選択し、テスト対象の "*1,000 個以下*" の発話を含む、正しい JSON 形式の JSON ファイルを見つけます。

    インポート エラーは、ブラウザーの上部にある赤い通知バーで報告されます。 インポートでエラーが発生した場合、データセットは作成されません。 詳細については、[一般的なエラー](#common-errors-importing-a-batch)に関するトピックをご覧ください。

4. `pizza-with-machine-learned-entity-test.json` ファイルのファイルの場所を選択します。

5. データセットに `pizza test` という名前を付け、 **[完了]** を選択します。

6. **[実行]** ボタンを選択します。 バッチ テストが実行された後、 **[結果を表示する]** を選択します。 

    > [!TIP]
    > * **[ダウンロード]** を選択すると、アップロードしたものと同じファイルがダウンロードされます。
    > * バッチ テストが失敗したと表示された場合は、少なくとも 1 つの発話の意図が予測と一致していません。

<a name="access-batch-test-result-details-in-a-visualized-view"></a>

### <a name="review-batch-results-for-intents"></a>意図のバッチ結果を確認する

バッチ テストの結果を確認するには、 **[See results]\(結果の表示\)** を選択します。 このテスト結果は、アクティブなバージョンで、テストの発話がどのように予測されるかをグラフィカルに示しています。

バッチのグラフには、結果の 4 つのセクションが表示されます。 グラフの右側にはフィルターがあります。 このフィルターには意図とエンティティが含まれています。 [グラフのセクション](#review-batch-results-for-intents)またはグラフ内の点を選択すると、関連付けられた発話がグラフの下に表示されます。

グラフの上にカーソルを置いている間は、マウス ホイールでグラフ内の表示を拡大または縮小できます。 これは、まとめて緊密にクラスタ化されたグラフ上に多数の点が存在する場合に役立ちます。

このグラフには 4 つのセクションがあり、そのうちの 2 つのセクションは赤色で表示されます。

1. フィルター一覧の **ModifyOrder** 意図を選択します。 発話は **真陽性** として予測されます。つまり発話が、バッチ ファイルにリストされたその陽性の予測と適切に一致したことを意味します。

    > [!div class="mx-imgBorder"]
    > ![発話がその陽性の予測と適切に一致](./media/luis-tutorial-batch-testing/intent-predicted-true-positive.png)

    フィルター一覧内の緑色のチェックマークでも、それぞれの意図のテストの成功が示されます。 その他の意図はすべて 1/1 の正のスコアで表示されています。これは、それぞれの意図に対する発話のテストが、バッチ テストにリストされていない意図に対する陰性テストとして実行されたためです。

1. **Confirmation** 意図を選択します。 この意図はバッチ テストにはリストされていません。つまりこれは、バッチ テストにリストされた発話の陰性テストとなります。

    > [!div class="mx-imgBorder"]
    > ![バッチ ファイルにリストされていない意図について、発話が正常に陰性と予測された](./media/luis-tutorial-batch-testing/true-negative-intent.png)

    グリッドとフィルター内の緑色のテキストが示すように、陰性テストは成功しています。

### <a name="review-batch-test-results-for-entities"></a>エンティティのバッチ テスト結果を確認する

ModifyOrder エンティティは、サブエンティティを伴ったマシン エンティティとして、最上位のエンティティが一致したかどうかを表示すると共に、サブエンティティがどのように予測されたかを表示します。

1. フィルター一覧の **ModifyOrder** エンティティを選択し、グリッド内の円を選択します。

1. エンティティの予測がグラフの下に表示されます。 期待と一致する予測は実線で、期待と一致しない予測は点線で表示されます。

    > [!div class="mx-imgBorder"]
    > ![バッチ ファイルでエンティティの親が正常に予測された](./media/luis-tutorial-batch-testing/labeled-entity-prediction.png)

<a name="filter-chart-results-by-intent-or-entity"></a>

#### <a name="filter-chart-results"></a>グラフの結果のフィルター処理

特定の意図またはエンティティでグラフをフィルター処理するには、右側のフィルター処理パネルで意図またはエンティティを選択します。 グラフのデータ ポイントとその分布が、選択した内容に応じて更新されます。

![視覚化されたバッチ テストの結果](./media/luis-how-to-batch-test/filter-by-entity.png)

### <a name="chart-result-examples"></a>グラフ結果の例

LUIS ポータルのグラフでは、次の操作を実行できます。
 
#### <a name="view-single-point-utterance-data"></a>単一ポイントの発話データの表示

グラフで、データ ポイントの上にポインターを置くと、その予測の確実性を示すスコアが表示されます。 データ ポイントを選択すると、ページ下部にある発話一覧で、そのデータ ポイントに対応する発話を確認できます。

![選択した発話](./media/luis-how-to-batch-test/selected-utterance.png)


<a name="relabel-utterances-and-retrain"></a>
<a name="false-test-results"></a>

#### <a name="view-section-data"></a>セクション データの表示

4 つのセクションで構成されるグラフで、セクション名を選択します。たとえば、グラフの右上のセクションの **[False Positive]\(誤検知\)** を選択します。 そのセクションのすべての発話が、グラフの下に一覧表示されます。

![セクションで選択した発話](./media/luis-how-to-batch-test/selected-utterances-by-section.png)

前の画像では、発話 `switch on` に TurnAllOn 意図のラベルが付いていますが、None 意図の予測を受け取りました。 つまり、想定される予測を得るには、TurnAllOn 意図に発話の例がさらに必要であるということです。

グラフ上で赤で示されている 2 つのセクションは、想定される予測と一致しなかった発話を示します。 これらのセクションが示す発話については、LUIS でのトレーニングがさらに必要であることを示します。

グラフ上で緑色で示されている 2 つのセクションは、想定される予測と一致しました。

## <a name="batch-testing-using-the-rest-api"></a>REST API を使用したバッチ テスト 

LUIS では、LUIS ポータルと REST API を使用してバッチ テストを行うことができます。 REST API のエンドポイントを下に示します。 LUIS ポータルを使用したバッチ テストの詳細については、[チュートリアル: バッチ テストのデータ セット]()に関する記事を参照してください。 下の完全な URL を使用して、プレースホルダーの値を独自の LUIS 予測キーとエンドポイントに置き換えます。 

必ずヘッダーで LUIS キーを `Ocp-Apim-Subscription-Key` に追加し、さらに `Content-Type` を `application/json` に設定してください。

### <a name="start-a-batch-test"></a>バッチ テストを開始する

アプリのバージョン ID または発行スロットのいずれかを使用して、バッチ テストを開始します。 **POST** 要求を、次のいずれかのエンドポイント形式に送信します。 要求の本文にバッチ ファイルを含めます。

発行スロット
* `<YOUR-PREDICTION-ENDPOINT>/luis/prediction/v3.0-preview/apps/<YOUR-APP-ID>/slots/<YOUR-SLOT-NAME>/evaluations`

アプリのバージョン ID
* `<YOUR-PREDICTION-ENDPOINT>/luis/prediction/v3.0-preview/apps/<YOUR-APP-ID>/versions/<YOUR-APP-VERSION-ID>/evaluations`

これらのエンドポイントは、状態の確認と結果の取得に使用する操作 ID を返します。 


### <a name="get-the-status-of-an-ongoing-batch-test"></a>進行中のバッチ テストの状態を取得する

開始したバッチ テストの操作 ID を使用して、次のエンドポイント形式からその状態を取得します。 

発行スロット
* `<YOUR-PREDICTION-ENDPOINT>/luis/prediction/v3.0-preview/apps/<YOUR-APP-ID>/slots/<YOUR-SLOT-ID>/evaluations/<YOUR-OPERATION-ID>/status`

アプリのバージョン ID
* `<YOUR-PREDICTION-ENDPOINT>/luis/prediction/v3.0-preview/apps/<YOUR-APP-ID>/versions/<YOUR-APP-VERSION-ID>/evaluations/<YOUR-OPERATION-ID>/status`

### <a name="get-the-results-from-a-batch-test"></a>バッチ テストの結果を取得する

開始したバッチ テストの操作 ID を使用して、次のエンドポイント形式からその結果を取得します。 

発行スロット
* `<YOUR-PREDICTION-ENDPOINT>/luis/prediction/v3.0-preview/apps/<YOUR-APP-ID>/slots/<YOUR-SLOT-ID>/evaluations/<YOUR-OPERATION-ID>/result`

アプリのバージョン ID
* `<YOUR-PREDICTION-ENDPOINT>/luis/prediction/v3.0-preview/apps/<YOUR-APP-ID>/versions/<YOUR-APP-VERSION-ID>/evaluations/<YOUR-OPERATION-ID>/result`


### <a name="batch-file-of-utterances"></a>発話のバッチ ファイル

バッチ テストのために、*データ セット* と呼ばれる発話のバッチ ファイルを送信します。 データ セットは JSON 形式のファイルで、ラベル付きの発話が最大 1,000 件含まれます。 アプリでは、最大 10 個のデータ セットをテストできます。 さらにテストする必要がある場合は、データ セットを削除し、新しいものを追加します。 バッチ ファイル データ内に対応するエンティティが存在しない場合でも、モデル内のすべてのカスタム エンティティがバッチ テストのエンティティのフィルターに表示されます。

バッチ ファイルは、発話で構成されます。 各発話には、検出されると予期されている[機械学習によるエンティティ](luis-concept-entity-types.md#types-of-entities)すべてと共に、予期される意図の予測が必要です。

### <a name="batch-syntax-template-for-intents-with-entities"></a>エンティティがある意図のバッチ構文テンプレート

次のテンプレートを使用してバッチ ファイルを開始します。

```JSON
{
    "LabeledTestSetUtterances": [
        {
            "text": "play a song",
            "intent": "play_music",
            "entities": [
                {
                    "entity": "song_parent",
                    "startPos": 0,
                    "endPos": 15,
                    "children": [
                        {
                            "entity": "pre_song",
                            "startPos": 0,
                            "endPos": 3
                        },
                        {
                            "entity": "song_info",
                            "startPos": 5,
                            "endPos": 15
                        }
                    ]
                }
            ]
        }
    ]
}

```

バッチ ファイルは、**startPos** および **endPos** プロパティを使用して、エンティティの開始と終了をメモします。 これらの値は 0 から始まり、スペースで開始または終了してはいけません。 これは、startIndex および endIndex プロパティを使用するクエリ ログとは異なります。

エンティティをテストしないようにする場合は、`entities` プロパティを含め、値を空の配列 `[]` として設定します。

### <a name="rest-api-batch-test-results"></a>REST API バッチ テストの結果

API によって返されるオブジェクトはいくつかあります。

* 精度、再現率、F スコアなど、意図とエンティティのモデルに関する情報。
* 各エンティティのエンティティ モデルに関する情報 (精度、再現率、F スコアなど)。 
  * `verbose` フラグを使用すると、`entityTextFScore` や `entityTypeFScore` など、エンティティに関する詳細情報を取得できます。
* 提供された発話と、予測されラベルが付けられた意図名。
* 偽陽性のエンティティの一覧と、偽陰性のエンティティの一覧。

## <a name="next-steps"></a>次のステップ

ご自身の LUIS アプリで正しい意図とエンティティが認識されないことがテストによって示されている場合、LUIS アプリのパフォーマンスを向上させるには、発話にさらに多くのラベルを付けるか、機能を追加します。

* [LUIS で推奨される発話にラベルを付ける](luis-how-to-review-endpoint-utterances.md)
* [LUIS アプリのパフォーマンスを向上させる機能を使用する](luis-how-to-add-features.md)
