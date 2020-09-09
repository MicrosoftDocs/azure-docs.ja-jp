---
title: チュートリアル:問題を見つけるためのバッチ テスト - LUIS
description: このチュートリアルでは、バッチ テストを使用して、Language Understanding (LUIS) アプリの品質を検証する方法について説明します。
ms.topic: tutorial
ms.date: 05/07/2020
ms.openlocfilehash: 09e5ebc9f80eb51175b13e601983690b6229b747
ms.sourcegitcommit: 50673ecc5bf8b443491b763b5f287dde046fdd31
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/20/2020
ms.locfileid: "83681635"
---
# <a name="tutorial-batch-test-data-sets"></a>チュートリアル:バッチ テスト用のデータセット

このチュートリアルでは、バッチ テストを使用して、Language Understanding (LUIS) アプリの品質を検証する方法について説明します。

バッチ テストでは、ラベルの付いた発話とエンティティの既知のセットを使用して、アクティブなトレーニング済みのモデルの状態を検証できます。 JSON 形式のバッチ ファイルで、発話を追加し、その発話内で予測されるようにしたいエンティティ ラベルを設定します。

バッチ テストを実行するための要件:

* テストあたりの発話の最大数は 1000。
* 重複はなし。
* 許可されるエンティティ型: 機械学習エンティティのみ。

このチュートリアル以外のアプリを使用している場合は、アプリに既に追加されている発話の例を使用 "*しないでください*"。

**このチュートリアルで学習する内容は次のとおりです。**

<!-- green checkmark -->
> [!div class="checklist"]
> * サンプル アプリをインポートする
> * バッチ テスト ファイルを作成する
> * バッチ テストを実行する
> * テスト結果を確認する

[!INCLUDE [LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="import-example-app"></a>サンプル アプリをインポートする

ピザの注文 (例: `1 pepperoni pizza on thin crust`) を受け付けるアプリをインポートします。

1.  [アプリの JSON ファイル](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/luis/apps/pizza-with-machine-learned-entity.json?raw=true)をダウンロードして保存します。

1. [LUIS ポータル](https://www.luis.ai)にサインインし、自分の**サブスクリプション**と**作成リソース**を選択して、その作成リソースに割り当てられているアプリを表示します。
1. JSON を新しいアプリにインポートし、アプリに `Pizza app` という名前を付けます。


1. ナビゲーションの右上隅にある **[Train]\(トレーニング\)** を選択して、アプリのトレーニングを行います。

## <a name="what-should-the-batch-file-utterances-include"></a>バッチ ファイルの発話に含めるべき内容

バッチ ファイルには、発話と共に、開始位置と終了位置を含むラベル付けされた機械学習エンティティが最上位に存在する必要があります。 既にアプリに存在する例の一部を発話に使用することは避けてください。 意図やエンティティを肯定的に予測したいと考える発話を使用する必要があります。

テストは意図やエンティティごとに分けてもかまいませんし、すべてのテスト (発話の数は 1,000 個まで) を同じファイルに含めてもかまいません。

## <a name="batch-file"></a>バッチ ファイル

サンプル JSON には、テスト ファイルの体裁を示すために、ラベル付けされたエンティティを含む発話が 1 つ含まれています。 実際のテストでは、適切な意図と機械学習エンティティがラベル付けされた発話を多数含める必要があります。

1. テキスト エディターで `pizza-with-machine-learned-entity-test.json` を作成するか、または[ダウンロード](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/luis/batch-tests/pizza-with-machine-learned-entity-test.json?raw=true)します。

2. JSON 形式のバッチ ファイルで、テストで予測されるようにしたい**意図**を持つ発話を追加します。

   [!code-json[Add the intents to the batch test file](~/samples-cognitive-services-data-files/luis/batch-tests/pizza-with-machine-learned-entity-test.json "Add the intent to the batch test file")]

## <a name="run-the-batch"></a>バッチを実行する

1. 上部のナビゲーション バーの **[Test]\(テスト\)** を選択します。

2. 右側のパネルで、 **[Batch testing panel]\(バッチ テスト パネル\)** を選択します。

3. **[Import dataset]\(データセットのインポート\)** を選択します。

    > [!div class="mx-imgBorder"]
    > ![データセットのインポートが強調表示されている LUIS アプリのスクリーンショット](./media/luis-tutorial-batch-testing/import-dataset-button.png)

4. `pizza-with-machine-learned-entity-test.json` ファイルのファイルの場所を選択します。

5. データセットに `pizza test` という名前を付け、 **[完了]** を選択します。

    > [!div class="mx-imgBorder"]
    > ![ファイルを選択する](./media/luis-tutorial-batch-testing/import-dataset-modal.png)

6. **[実行]** ボタンを選択します。

7. **[See results]\(結果の表示\)** を選択します。

8. グラフと凡例で結果を確認します。

## <a name="review-batch-results-for-intents"></a>意図のバッチ結果を確認する

このテスト結果は、アクティブなバージョンで、テストの発話がどのように予測されるかをグラフィカルに示しています。

バッチのグラフには、結果の 4 つのセクションが表示されます。 グラフの右側にはフィルターがあります。 このフィルターには意図とエンティティが含まれています。 [グラフのセクション](luis-concept-batch-test.md#batch-test-results)またはグラフ内の点を選択すると、関連付けられた発話がグラフの下に表示されます。

グラフの上にカーソルを置いている間は、マウス ホイールでグラフ内の表示を拡大または縮小できます。 これは、まとめて緊密にクラスタ化されたグラフ上に多数の点が存在する場合に役立ちます。

このグラフには 4 つのセクションがあり、そのうちの 2 つのセクションは赤色で表示されます。

1. フィルター一覧の **ModifyOrder** 意図を選択します。

    > [!div class="mx-imgBorder"]
    > ![フィルター一覧から ModifyOrder 意図を選択する](./media/luis-tutorial-batch-testing/select-intent-from-filter-list.png)

    発話は**真陽性**として予測されます。つまり発話が、バッチ ファイルにリストされたその陽性の予測と適切に一致したことを意味します。

    > [!div class="mx-imgBorder"]
    > ![発話がその陽性の予測と適切に一致](./media/luis-tutorial-batch-testing/intent-predicted-true-positive.png)

    フィルター一覧内の緑色のチェックマークでも、それぞれの意図のテストの成功が示されます。 その他の意図はすべて 1/1 の正のスコアで表示されています。これは、それぞれの意図に対する発話のテストが、バッチ テストにリストされていない意図に対する陰性テストとして実行されたためです。

1. **Confirmation** 意図を選択します。 この意図はバッチ テストにはリストされていません。つまりこれは、バッチ テストにリストされた発話の陰性テストとなります。

    > [!div class="mx-imgBorder"]
    > ![バッチ ファイルにリストされていない意図について、発話が正常に陰性と予測された](./media/luis-tutorial-batch-testing/true-negative-intent.png)

    グリッドとフィルター内の緑色のテキストが示すように、陰性テストは成功しています。

## <a name="review-batch-test-results-for-entities"></a>エンティティのバッチ テスト結果を確認する

ModifyOrder エンティティは、サブエンティティを伴ったマシン エンティティとして、最上位のエンティティが一致したかどうかを表示すると共に、サブエンティティがどのように予測されたかを表示します。

1. フィルター一覧の **ModifyOrder** エンティティを選択し、グリッド内の円を選択します。

1. エンティティの予測がグラフの下に表示されます。 期待と一致する予測は実線で、期待と一致しない予測は点線で表示されます。

    > [!div class="mx-imgBorder"]
    > ![バッチ ファイルでエンティティの親が正常に予測された](./media/luis-tutorial-batch-testing/labeled-entity-prediction.png)

## <a name="finding-errors-with-a-batch-test"></a>バッチ テストでエラーを探す

このチュートリアルでは、テストを実行して結果を解釈する方法について説明しました。 テストの原理やテストに失敗した場合の対処方法については取り上げていません。

* テストでは、陽性と陰性の両方の発話をカバーするようにしてください (異なってはいるが関連性がある意図に対して予測される発話も含む)。
* 失敗した発話については、次のタスクを実行してから、再度テストを行ってください。
    * 現在の意図とエンティティの例を確認し、アクティブなバージョンの発話の例が、意図とエンティティのどちらのラベル付けについても適切であることを確認します。
    * アプリによる意図とエンティティの予測を支援する機能を追加します。
    * 陽性の発話の例をさらに追加します。
    * 意図全体が対象となるように発話の例のバランスを確認します。

## <a name="clean-up-resources"></a>リソースをクリーンアップする

[!INCLUDE [LUIS How to clean up resources](./includes/cleanup-resources-preview-portal.md)]

## <a name="next-step"></a>次のステップ

このチュートリアルでは、バッチ テストを使用して、現在のモデルを検証しました。

> [!div class="nextstepaction"]
> [パターンについて](luis-tutorial-pattern.md)

