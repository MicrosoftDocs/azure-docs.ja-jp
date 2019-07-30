---
title: エンドポイントの発話の確認
titleSuffix: Azure Cognitive Services
description: LUIS HTTP エンドポイント経由で受け取った LUIS にとって不確かな発話を確認または修正することによって、アプリの予測精度を高めます。 いくつかの発話についての意図を確認したり、その他の発話のエンティティを確認したりすることが必要な場合もあります。
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: tutorial
ms.date: 07/16/2019
ms.author: diberry
ms.openlocfilehash: 2994f7b19d5a104b129dc4d7aff29dabbc89f0f4
ms.sourcegitcommit: 9a699d7408023d3736961745c753ca3cec708f23
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/16/2019
ms.locfileid: "68276001"
---
# <a name="tutorial-fix-unsure-predictions-by-reviewing-endpoint-utterances"></a>チュートリアル:エンドポイントの発話を確認して不確かな予測を修正する
このチュートリアルでは、LUIS HTTPS エンドポイント経由で受け取った LUIS にとって不確かな発話を確認または修正することによって、アプリの予測精度を高めます。 いくつかの発話についての意図を確認したり、その他の発話のエンティティを確認したりすることが必要な場合もあります。 LUIS の定期メンテナンスの通常の部分としてエンドポイントの発話を確認する必要があります。 

このレビュー プロセスも、LUIS がユーザーのアプリ ドメインを学習する方法の 1 つとなります。 LUIS は、レビュー リストにある発話を選択しました。 このリストには、次の特徴があります。

* アプリに固有である。
* アプリの予測精度を高めることを意図している。 
* 定期的にレビューする必要がある。 

エンドポイントの発話をレビューすることによって、その発話から予測される意図を確認または修正することになります。 また、予測されなかったか予測が誤っていたカスタム エンティティへのラベル付けも行います。 

**このチュートリアルで学習する内容は次のとおりです。**

<!-- green checkmark -->
> [!div class="checklist"]
> * サンプル アプリをインポートする
> * エンドポイントの発言の確認
> * フレーズ リストの更新
> * アプリのトレーニング
> * アプリの発行
> * アプリのエンドポイントをクエリして LUIS JSON の応答を表示する

[!INCLUDE [LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="import-example-app"></a>サンプル アプリをインポートする

最後のチュートリアルで作成した、**HumanResources** という名前のアプリを引き続き使用します。 

次の手順に従います。

1.  [アプリの JSON ファイル](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/documentation-samples/tutorials/custom-domain-sentiment-HumanResources.json)をダウンロードして保存します。

1. JSON を新しいアプリにインポートします。

1. **[管理]** セクションの **[バージョン]** タブで、バージョンを複製し、それに `review` という名前を付けます。 複製は、元のバージョンに影響を及ぼさずに LUIS のさまざまな機能を使用するための優れた方法です。 バージョン名は URL ルートの一部として使用されるため、URL 内で有効ではない文字を名前に含めることはできません。

1. 新しいアプリをトレーニングして発行します。

1. エンドポイントを使用して次の発話を追加します。 これは[スクリプト](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/examples/demo-upload-endpoint-utterances/endpoint.js)を使用するか、またはブラウザーでエンドポイントから実行できます。 追加する発話は次のとおりです。

   [!code-nodejs[Node.js code showing endpoint utterances to add](~/samples-luis/examples/demo-upload-endpoint-utterances/endpoint.js?range=15-26)]

    このチュートリアル シリーズで使用したアプリのバージョンをすべてお持ちの方は、 **[Review endpoint utterances]\(エンドポイントの発話の確認\)** リストがどのバージョンも変わらないことに気付いて意外に感じるかもしれません。 どのバージョンをアクティブに編集しているか、またはどのバージョンのアプリがエンドポイントで発行されたかには関係なく、確認すべき発話のプールが 1 つあります。 

## <a name="review-endpoint-utterances"></a>エンドポイントの発言の確認

1. [!INCLUDE [Start in Build section](../../../includes/cognitive-services-luis-tutorial-build-section.md)]

1. 左側のナビゲーションから **[Review endpoint utterances]\(エンドポイントの発話の確認\)** を選択します。 このリストは、**ApplyForJob** という意図がフィルターで抽出されています。 

    [![左側のナビゲーションにある [Review endpoint utterances]\(エンドポイントの発話の確認\) ボタンのスクリーンショット](./media/luis-tutorial-review-endpoint-utterances/review-endpoint-utterances-with-entity-view.png)](./media/luis-tutorial-review-endpoint-utterances/review-endpoint-utterances-with-entity-view.png#lightbox)

1. **[エンティティの表示]** を切り替えて、ラベル付けされたエンティティを表示します。 
    
    [![[エンティティの表示] トグルを強調表示した [Review endpoint utterances]\(エンドポイントの発話の確認\) のスクリーンショット](./media/luis-tutorial-review-endpoint-utterances/review-endpoint-utterances-with-token-view.png)](./media/luis-tutorial-review-endpoint-utterances/review-endpoint-utterances-with-token-view.png#lightbox)


    この発話 `I'm looking for a job with Natural Language Processing` は、適切な意図内にありません。 

    発話が適切に予測されなかった理由は、**GetJobInformation** の 7 個の発話と比較して、**ApplyForJob** の意図に 21 個の発話があるためです。 発話数が多い意図の方が、より高い予測が得られます。 意図の全体で発話の量と品質のバランスが取れていることが重要です。

1.  この発話を調整するには、適切な意図を選択し、その中のジョブ エンティティをマークします。 緑色のチェックボックスをオンにして、変更した発話をアプリに追加します。 

    |発話|正しい意図|エンティティが不明|
    |:--|:--|:--|
    |`I'm looking for a job with Natural Language Processing`|GetJobInfo|Job - "Natural Language Process"|

    発話を追加すると、発話が **[Review endpoint utterances]\(エンドポイントの発話の確認\)** から **[GetJobInformation]** の意図に移されます。 これでエンドポイントの発話が、適切な意図の発話例になりました。 

    この発話を正しく配置すると共に、**GetJobInformation** 意図には、もっと発話を追加する必要があります。 この作業については各自が演習として行ってください。 それぞれの意図に割り当てられる発話の例は、**None** を除き、ほぼ同数となるようにする必要があります。 **None** 意図は、アプリ内の発話全体の 10% にすることをお勧めします。 

1. この意図に関して、残りの発話をレビューします。発話のラベル付けを行い、 **[Aligned intent]\(アラインメントされた意図\)** が誤っていれば修正してください。

1. 前出の発話はリストから消えます。 さらに発話が表示された場合は、引き続きリストの項目を処理します。リストが空になるまで意図を修正し、不明なエンティティのラベル付けを行ってください。 

1. フィルター リストから次の意図を選択し、続けて発話の修正とエンティティのラベル付けを行います。 意図ごとの最後の手順で必ず、発話行の **[Add to aligned intent]\(アラインメント済みの意図に追加\)** を選択するか、または、各意図のチェック ボックスをオンにして、表の上にある **[Add selected]\(選択項目の追加\)** を選択してください。

    フィルター リスト内のすべての意図およびエンティティが空になるまで続行します。 これはきわめて小さなアプリです。 レビュー プロセスの所要時間は数分程度です。 

## <a name="update-phrase-list"></a>フレーズ リストの更新
フレーズ リストは、新たに検出されたジョブ名で最新の状態に維持してください。 

1. 左側のナビゲーションから **[Phrase lists]\(フレーズ リスト\)** を選択します。

2. **[Jobs]** フレーズ リストを選択します。

3. `Natural Language Processing` を値として追加し、 **[保存]** を選択します。 

## <a name="train"></a>トレーニング

LUIS は、トレーニングされてはじめて変更を認識します。 

[!INCLUDE [LUIS How to Train steps](../../../includes/cognitive-services-luis-tutorial-how-to-train.md)]

## <a name="publish"></a>発行

このアプリをインポートした場合は、 **[感情分析]** を選択する必要があります。

[!INCLUDE [LUIS How to Publish steps](../../../includes/cognitive-services-luis-tutorial-how-to-publish.md)]

## <a name="get-intent-and-entities-from-endpoint"></a>エンドポイントから意図とエンティティを取得する

修正後の発話に近い発話を試してみましょう。 

1. [!INCLUDE [LUIS How to get endpoint first step](../../../includes/cognitive-services-luis-tutorial-how-to-get-endpoint.md)]

2. アドレスの URL の末尾に移動し、「`Are there any natural language processing jobs in my department right now?`」と入力します。 最後の querystring パラメーターは `q` です。これは発話の**クエリ**です。 

   ```json
   {
    "query": "are there any natural language processing jobs in my department right now?",
    "topScoringIntent": {
      "intent": "GetJobInformation",
      "score": 0.9247605
    },
    "intents": [
      {
        "intent": "GetJobInformation",
        "score": 0.9247605
      },
      {
        "intent": "ApplyForJob",
        "score": 0.129989788
      },
      {
        "intent": "FindForm",
        "score": 0.006438211
      },
      {
        "intent": "EmployeeFeedback",
        "score": 0.00408575451
      },
      {
        "intent": "Utilities.StartOver",
        "score": 0.00194211153
      },
      {
        "intent": "None",
        "score": 0.00166400627
      },
      {
        "intent": "Utilities.Help",
        "score": 0.00118593348
      },
      {
        "intent": "MoveEmployee",
        "score": 0.0007885918
      },
      {
        "intent": "Utilities.Cancel",
        "score": 0.0006373631
      },
      {
        "intent": "Utilities.Stop",
        "score": 0.0005980781
      },
      {
        "intent": "Utilities.Confirm",
        "score": 3.719905E-05
      }
    ],
    "entities": [
      {
        "entity": "right now",
        "type": "builtin.datetimeV2.datetime",
        "startIndex": 64,
        "endIndex": 72,
        "resolution": {
          "values": [
            {
              "timex": "PRESENT_REF",
              "type": "datetime",
              "value": "2018-07-05 15:23:18"
            }
          ]
        }
      },
      {
        "entity": "natural language processing",
        "type": "Job",
        "startIndex": 14,
        "endIndex": 40,
        "score": 0.9869922
      },
      {
        "entity": "natural language processing jobs",
        "type": "builtin.keyPhrase",
        "startIndex": 14,
        "endIndex": 45
      },
      {
        "entity": "department",
        "type": "builtin.keyPhrase",
        "startIndex": 53,
        "endIndex": 62
      }
    ],
    "sentimentAnalysis": {
      "label": "positive",
      "score": 0.8251864
    }
   }
   }
   ```

   正しい意図が高いスコアで予測され、**Job** エンティティが `natural language processing` として検出されます。 

## <a name="can-reviewing-be-replaced-by-adding-more-utterances"></a>レビューの代わりに発話をもっと追加したらいいのではないでしょうか? 
発話例をもっと追加してみたらどうだろう、と思われるかもしれません。 エンドポイントの発話をレビューする目的は何でしょうか。 実世界の LUIS アプリでは、エンドポイントの発話がユーザーから与えられるため、単語の選び方や並べ方が未知のものになります。 ユーザーと同じ単語の選び方や並べ方を使用すれば、元の予測のパーセンテージはもっと高い値になるでしょう。 

## <a name="why-is-the-top-intent-on-the-utterance-list"></a>最上位の意図が発話リストにあるのはなぜでしょうか? 
エンドポイントの発話の中には、レビュー リストの中で高い予測スコアが割り当てられるものがあります。 それらの発話も、レビューして確認する必要があります。 これらがリストに表示されるのは、次に高い意図のスコアが、最上位の意図のスコアと近すぎるためです。 上位の 2 つの意図の間には約 15% の差が必要です。

## <a name="clean-up-resources"></a>リソースのクリーンアップ

[!INCLUDE [LUIS How to clean up resources](../../../includes/cognitive-services-luis-tutorial-how-to-clean-up-resources.md)]

## <a name="next-steps"></a>次の手順
このチュートリアルでは、エンドポイントで送信された、LUIS にとって不確かな発話が確認されました。 これら発話が検証され、発話の例として正しい意図に移動されると、LUIS の予測精度が向上します。

> [!div class="nextstepaction"]
> [パターンの使用方法を確認する](luis-tutorial-pattern.md)
