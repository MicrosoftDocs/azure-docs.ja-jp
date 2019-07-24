---
title: バッチ テスト
titleSuffix: Azure Cognitive Services
description: このチュートリアルでは、バッチ テストを使用してアプリでの発話予測の問題を検出し、修正する方法を説明します。
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: article
ms.date: 03/29/2019
ms.author: diberry
ms.openlocfilehash: 391a5386a5ecc144b15c35a85d501dfb5ce2d172
ms.sourcegitcommit: 1c2cf60ff7da5e1e01952ed18ea9a85ba333774c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/12/2019
ms.locfileid: "59523139"
---
# <a name="tutorial-batch-test-data-sets"></a>チュートリアル:バッチ テスト用のデータセット

このチュートリアルでは、バッチ テストを使用してアプリでの発話予測の問題を検出し、修正する方法を説明します。  

バッチ テストでは、ラベルの付いた発話とエンティティの既知のセットを使用して、アクティブなトレーニング済みのモデルの状態を検証できます。 JSON 形式のバッチ ファイルで、発話を追加し、その発話内で予測されるようにしたいエンティティ ラベルを設定します。 

バッチ テストを実行するための要件:

* テストあたりの発話の最大数は 1000。 
* 重複はなし。 
* 許可されるエンティティ型: シンプル、階層構造 (親のみ)、および複合の機械学習されたエンティティのみ。 バッチ テストは、機械学習された意図とエンティティに対してのみ有効です。

このチュートリアル以外のアプリを使用している場合は、意図に既に追加されている発話の例を使用*しないでください*。 

**このチュートリアルで学習する内容は次のとおりです。**

<!-- green checkmark -->
> [!div class="checklist"]
> * サンプル アプリをインポートする
> * バッチ テスト ファイルを作成する 
> * バッチ テストを実行する
> * テスト結果を確認する
> * エラーを修正する 
> * バッチを再テストする

[!INCLUDE [LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="import-example-app"></a>サンプル アプリをインポートする

最後のチュートリアルで作成した、**HumanResources** という名前のアプリを引き続き使用します。 

次の手順に従います。

1.  [アプリの JSON ファイル](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/documentation-samples/tutorials/custom-domain-review-HumanResources.json)をダウンロードして保存します。

2. JSON を新しいアプリにインポートします。

3. **[管理]** セクションの **[バージョン]** タブで、バージョンを複製し、それに `batchtest` という名前を付けます。 複製は、元のバージョンに影響を及ぼさずに LUIS のさまざまな機能を使用するための優れた方法です。 バージョン名は URL ルートの一部として使用されるため、URL 内で有効ではない文字を名前に含めることはできません。 

4. アプリをトレーニングします。

## <a name="batch-file"></a>バッチ ファイル

1. テキスト エディターで `HumanResources-jobs-batch.json` を作成するか、または[ダウンロード](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/documentation-samples/tutorials/HumanResources-jobs-batch.json)します。 

2. JSON 形式のバッチ ファイルで、テストで予測されるようにしたい**意図**を持つ発話を追加します。 

   [!code-json[Add the intents to the batch test file](~/samples-luis/documentation-samples/tutorials/HumanResources-jobs-batch.json "Add the intents to the batch test file")]

## <a name="run-the-batch"></a>バッチを実行する

1. 上部のナビゲーション バーの **[Test]\(テスト\)** を選択します。 

2. 右側のパネルで、**[Batch testing panel]\(バッチ テスト パネル\)** を選択します。 

    [![バッチ テスト パネルが強調表示されている LUIS アプリのスクリーンショット](./media/luis-tutorial-batch-testing/hr-batch-testing-panel-link.png)](./media/luis-tutorial-batch-testing/hr-batch-testing-panel-link.png#lightbox)

3. **[Import dataset]\(データセットのインポート\)** を選択します。

    [![データセットのインポートが強調表示されている LUIS アプリのスクリーンショット](./media/luis-tutorial-batch-testing/hr-import-dataset-button.png)](./media/luis-tutorial-batch-testing/hr-import-dataset-button.png#lightbox)

4. `HumanResources-jobs-batch.json` ファイルのファイルの場所を選択します。

5. データセットに `intents only` という名前を付け、**[完了]** を選択します。

    ![ファイルの選択](./media/luis-tutorial-batch-testing/hr-import-new-dataset-ddl.png)

6. **[実行]** ボタンを選択します。 

7. **[See results]\(結果の表示\)** を選択します。

8. グラフと凡例で結果を確認します。

    [![バッチ テストの結果を含む LUIS アプリのスクリーンショット](./media/luis-tutorial-batch-testing/hr-intents-only-results-1.png)](./media/luis-tutorial-batch-testing/hr-intents-only-results-1.png#lightbox)

## <a name="review-batch-results"></a>バッチ結果を確認する

バッチのグラフには、結果の 4 つのセクションが表示されます。 グラフの右側にはフィルターがあります。 既定では、フィルターは一覧内の最初の意図に設定されています。 このフィルターには、すべての意図と、シンプル、階層構造 (親のみ)、および複合エンティティのみが含まれています。 [グラフのセクション](luis-concept-batch-test.md#batch-test-results)またはグラフ内の点を選択すると、関連付けられた発話がグラフの下に表示されます。 

グラフの上にカーソルを置いている間は、マウス ホイールでグラフ内の表示を拡大または縮小できます。 これは、まとめて緊密にクラスタ化されたグラフ上に多数の点が存在する場合に役立ちます。 

このグラフには 4 つのセクションがあり、そのうちの 2 つのセクションは赤色で表示されます。 **これらは注目すべきセクションです**。 

### <a name="getjobinformation-test-results"></a>GetJobInformation のテスト結果

フィルターで表示されている **GetJobInformation** のテスト結果は、4 つの予測のうちの 2 つが成功したことを示しています。 グラフの下に発話を表示するには、右上のセクションにある名前 **[False positive] (誤検知)** を選択します。 

![LUIS バッチ テストの発話](./media/luis-tutorial-batch-testing/hr-applyforjobs-false-positive-results.png)

発話のうちの 2 つが正しい意図 **GetJobInformation** ではなく、**ApplyForJob** として予測されているのはなぜでしょうか。 これらの 2 つの意図は、単語の選択や単語の配置の点から見て、非常に密接に関連しています。 さらに、**ApplyForJob** の例は **GetJobInformation** の例のほぼ 3 倍あります。 この発話の例のばらつきが **ApplyForJob** 意図に有利に働きます。 

両方の意図に同じエラーの数があることに注意してください。 1 つの意図での正しくない予測が、もう一方の意図にも影響を与えます。 1 つの意図で発話が誤って予測され、もう 1 つの意図でも誤って予測されなかったため、これらはどちらもエラーになります。 

![LUIS バッチ テストのフィルター エラー](./media/luis-tutorial-batch-testing/hr-intent-error-count.png)

**[False positive] (誤検知)** セクション内の一番上の点に対応する発話は `Can I apply for any database jobs with this resume?` と `Can I apply for any database jobs with this resume?` です。 最初の発話の場合、単語 `resume` は **ApplyForJob** でのみ使用されました。 2 番目の発話の場合、単語 `apply` は **ApplyForJob** 意図でのみ使用されました。

## <a name="fix-the-app"></a>アプリを修正する

このセクションの目的は、アプリを修正することによって、**GetJobInformation** ですべての発話が正しく予測されるようにすることです。 

一見すばやい修正として、これらのバッチ ファイルの発話を正しい意図に追加する方法があります。 ただし、それは本来の方法ではありません。 それらを例として追加せずに、LUIS にこれらの発話を正しく予測させる必要があります。 

また、**ApplyForJob** の発話を、発話の量が **GetJobInformation** と同じになるまで削除する方法も考えられます。 それにより、テスト結果は修正される可能性がありますが、次回 LUIS がその意図を正確に予測することができなくなります。 

最初の修正では、**GetJobInformation** にさらに発話を追加します。 2 番目の修正では、`resume` や `apply` などの単語の重みを **ApplyForJob** 意図に近づくように減らします。 

### <a name="add-more-utterances"></a>さらに発話を追加する

1. 上部のナビゲーション パネルにある **[テスト]** ボタンを選択することによってバッチ テスト パネルを閉じます。 

2. 意図の一覧から **[GetJobInformation]** を選択します。 

3. 長さ、単語の選択、単語の配置などがさまざまなさらに多くの発話を追加して、用語 `resume`、`c.v.`、および `apply` が確実に含まれるようにします。

    |**GetJobInformation** 意図の発話の例|
    |--|
    |若い食肉牛の倉庫での新しい仕事には履歴書で申し込む必要がありますか?|
    |今日、屋根ふきの仕事はどこにありますか?|
    |履歴書が必要な医療コーディングの仕事があったと聞きました。|
    |大学生が自分の履歴書を書くのを手伝う仕事をしたいと思います。 |
    |ここに、コンピューターを使用するコミュニティ大学の新しいポストを探している私の履歴書があります。|
    |児童保護および在宅介護では、どのような地位を得られますか?|
    |新聞社に見習いデスクはありますか?|
    |私の履歴書は、 私が調達、予算、および赤字の分析を得意としていることを示しています。 この種類の仕事は何かありますか?|
    |現時点で、地球掘削の仕事はどこにありますか?|
    |私は EMS ドライバーとして 8 年間働いてきました。 新しい仕事は何かありますか?|
    |新しい食品取り扱いの仕事には申し込みが必要ですか?|
    |獲得できる新しい庭仕事はいくつありますか?|
    |労使関係および労使交渉の新しい人事ポストはありますか?|
    |私は図書館およびアーカイブ管理の修士号を持っています。 新しい地位は何かありますか?|
    |現在、市場に 13 歳のための子守の仕事は何かありますか?|

    発話内の **[Job] (仕事)** エンティティにラベルを付けないでください。 チュートリアルのこのセクションは、意図の予測にのみ焦点を絞っています。

4. 右上のナビゲーションにある **[トレーニング]** を選択することによってアプリをトレーニングします。

## <a name="verify-the-new-model"></a>新しいモデルを確認する

バッチ テスト内の発話が正しく予測されていることを確認するために、バッチ テストを再度実行します。

1. 上部のナビゲーション バーの **[Test]\(テスト\)** を選択します。 バッチの結果がまだ開いている場合は、**[Back to list] (一覧に戻る)** を選択します。  

2. バッチ名の右にある省略記号 (***...***) ボタンを選択し、**[Run Dataset] (データセットの実行)** を選択します。 バッチ テストが完了するまで待ちます。 **[See results] (結果の表示)** ボタンが緑色になっていることに注意してください。 これは、バッチ全体が正常に実行されたことを示します。

3. **[See results]\(結果の表示\)** を選択します。 どの意図も、意図名の左側に緑色のアイコンが表示されています。 

    ![バッチの結果のボタンが強調表示されている LUIS のスクリーンショット](./media/luis-tutorial-batch-testing/hr-batch-test-intents-no-errors.png)

## <a name="create-batch-file-with-entities"></a>エンティティを含むバッチ ファイルを作成する 

バッチ テストでエンティティを確認するために、バッチ JSON ファイル内でエンティティにラベルを付ける必要があります。 機械学習されたエンティティ、つまり、シンプル、階層構造 (親のみ)、および複合エンティティのみが使用されます。 機械学習されていないエンティティは、正規表現または明示的なテキスト一致のどちらかで常に見つかるため、追加しないでください。

単語の合計 ([トークン](luis-glossary.md#token)) 数に関するエンティティの変動が予測品質に影響を与える場合があります。 ラベルの付いた発話を含む意図に指定されるトレーニング データに、さまざまな長さのエンティティが含まれていることを確認してください。 

バッチ ファイルを初めて記述してテストする場合は、機能することがわかっているいくつかの発話とエンティティ、および誤って予測される可能性があると思われるいくつかの発話とエンティティで開始することが最善です。 これは、問題領域にすばやく焦点を絞るのに役立ちます。 いくつかの異なる仕事名 (これは予測されませんでした) を使用して **GetJobInformation** および **ApplyForJob** 意図をテストした後、**[Job] (仕事)** エンティティの特定の値に予測の問題があるかどうかを確認するためにこのバッチ テスト ファイルが開発されました。 

テストの発話で指定される **[Job] (仕事)** エンティティの値は通常、1 つまたは 2 つの単語であり、いくつかの例ではそれ以上の単語数になります。 _独自の_ 人事アプリに一般に多数の単語の仕事名が存在する場合、このアプリで **[Job] (仕事)** エンティティのラベルが付けられている発話の例はうまく機能しません。

1. [VSCode](https://code.visualstudio.com/) などのテキスト エディターで `HumanResources-entities-batch.json` を作成するか、または[ダウンロード](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/documentation-samples/tutorials/HumanResources-entities-batch.json)します。


2. JSON 形式のバッチ ファイルで、テストで予測されるようにしたい**意図**を持つ発話や、その発話内の任意のエンティティの場所を含むオブジェクトの配列を追加します。 エンティティはトークン ベースであるため、各エンティティを文字で開始および終了するようにしてください。 発話をスペースで開始または終了しないでください。 これにより、バッチ ファイルのインポート中にエラーが発生します。  

   [!code-json[Add the intents and entities to the batch test file](~/samples-luis/documentation-samples/tutorials/HumanResources-entities-batch.json "Add the intents and entities to the batch test file")]


## <a name="run-the-batch-with-entities"></a>エンティティを含むバッチを実行する

1. 上部のナビゲーション バーの **[Test]\(テスト\)** を選択します。 

2. 右側のパネルで、**[Batch testing panel]\(バッチ テスト パネル\)** を選択します。 

3. **[Import dataset]\(データセットのインポート\)** を選択します。

4. `HumanResources-entities-batch.json` ファイルのファイル システムの場所を選択します。

5. データセットに `entities` という名前を付け、**[完了]** を選択します。

6. **[実行]** ボタンを選択します。 テストが完了するまで待ちます。

7. **[See results]\(結果の表示\)** を選択します。

[!INCLUDE [Entity roles in batch testing - currently not supported](../../../includes/cognitive-services-luis-roles-not-supported-in-batch-testing.md)]

## <a name="review-entity-batch-results"></a>エンティティのバッチの結果を確認する

すべての意図が正しく予測されたグラフが開きます。 右側のフィルターを下へスクロールして、エラーになっているエンティティの予測を見つけます。 

1. フィルター内の **[Job] (仕事)** エンティティを選択します。

    ![フィルター内のエラーになっているエンティティの予測](./media/luis-tutorial-batch-testing/hr-entities-filter-errors.png)

    グラフが変化して、エンティティの予測が表示されます。 

2. グラフの左下のセクションで **[False Negative] (検知漏れ)** を選択します。 次に、キーボードの組み合わせ Ctrl + E を使用して、トークン ビューに切り替えます。 

    [![エンティティの予測のトークン ビュー](./media/luis-tutorial-batch-testing/token-view-entities.png)](./media/luis-tutorial-batch-testing/token-view-entities.png#lightbox)
    
    グラフの下の発話を確認すると、仕事名に `SQL` が含まれていると常にエラーが発生することがわかります。 発話の例と [Job] (仕事) フレーズ リストを確認すると、SQL は 1 回だけ、より大きな仕事名 `sql/oracle database administrator` の一部としてのみ使用されています。

## <a name="fix-the-app-based-on-entity-batch-results"></a>エンティティのバッチの結果に基づいてアプリを修正する

アプリを修正するには、LUIS が SQL ジョブの変動を正しく判定することが必要です。 その修正には、いくつかのオプションがあります。 

* SQL を使用し、これらの単語を [Job] (仕事) エンティティとしてラベル付けする、さらに多くの発話の例を明示的に追加する。 
* フレーズ リストにさらに多くの SQL ジョブを明示的に追加する。

これらのタスクは、ユーザーに実行が任されます。

エンティティが正しく予測される前に[パターン](luis-concept-patterns.md)を追加しても、この問題は修正されません。 これは、パターンが、そのパターン内のすべてのエンティティが検出されるまで一致しないためです。 

## <a name="clean-up-resources"></a>リソースのクリーンアップ

[!INCLUDE [LUIS How to clean up resources](../../../includes/cognitive-services-luis-tutorial-how-to-clean-up-resources.md)]

## <a name="next-steps"></a>次の手順

チュートリアルでは、バッチ テストを使用して、現在のモデルの問題を検出しました。 モデルを修正し、バッチ ファイルを使用して再テストを行い、変更が正しいことを確認しました。

> [!div class="nextstepaction"]
> [パターンについて](luis-tutorial-pattern.md)

