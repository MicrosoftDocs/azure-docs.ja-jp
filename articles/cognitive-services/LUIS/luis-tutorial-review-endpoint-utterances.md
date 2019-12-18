---
title: チュートリアル:エンドポイントの発話の確認 - LUIS
titleSuffix: Azure Cognitive Services
description: LUIS HTTP エンドポイント経由で受け取った LUIS にとって不確かな発話を確認または修正することによって、アプリの予測精度を高めます。 いくつかの発話についての意図を確認したり、その他の発話のエンティティを確認したりすることが必要な場合もあります。
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: tutorial
ms.date: 12/05/2019
ms.author: diberry
ms.openlocfilehash: 0a4d2a3345ce4f69d4492d1a782b778b1ee3bf4c
ms.sourcegitcommit: 8bd85510aee664d40614655d0ff714f61e6cd328
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/06/2019
ms.locfileid: "74895669"
---
# <a name="tutorial-fix-unsure-predictions-by-reviewing-endpoint-utterances"></a>チュートリアル:エンドポイントの発話を確認して不確かな予測を修正する
このチュートリアルでは、LUIS HTTPS エンドポイント経由で受け取った LUIS にとって不確かな発話を確認または修正することによって、アプリの予測精度を高めます。 LUIS の定期メンテナンスの通常の部分としてエンドポイントの発話を確認する必要があります。

このレビュー プロセスによって、LUIS はユーザーのアプリ ドメインを学習することができます。 レビュー リストにある発話が LUIS によって選択されます。 このリストには、次の特徴があります。

* アプリに固有である。
* アプリの予測精度を高めることを意図している。
* 定期的にレビューする必要がある。

エンドポイントの発話をレビューすることによって、その発話から予測される意図を確認または修正することになります。

[!INCLUDE [Uses preview portal](includes/uses-portal-preview.md)]

**このチュートリアルで学習する内容は次のとおりです。**

<!-- green checkmark -->
> [!div class="checklist"]
> * サンプル アプリをインポートする
> * エンドポイントの発言の確認
> * アプリをトレーニングして公開する
> * アプリのエンドポイントをクエリして LUIS JSON の応答を表示する

[!INCLUDE [LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="import-example-app"></a>サンプル アプリをインポートする

アプリをインポートするには、次の手順に従います。

1.  [アプリの JSON ファイル](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/documentation-samples/tutorials/custom-domain-sentiment-HumanResources.json)をダウンロードして保存します。

1. [プレビューの LUIS ポータル](https://preview.luis-ai)で、新しいアプリに .json ファイルをインポートします。

1. **[管理]** セクションの **[バージョン]** タブで、バージョンを複製し、それに `review` という名前を付けます。

    > [!TIP]
    > アプリを変更する前に、新しいバージョンにクローンすることをお勧めします。 あるバージョンが完成したら、そのバージョンを (.json または .lu ファイル) としてエクスポートし、お使いのソース管理システムにファイルをチェックインします。


1. アプリをトレーニングするには、 **[トレーニング]** を選択します。

## <a name="publish-the-app-to-access-it-from-the-http-endpoint"></a>アプリを発行して HTTP エンドポイントからアクセスする

[!INCLUDE [LUIS How to Publish steps](includes/howto-publish.md)]

## <a name="add-utterances-at-the-endpoint"></a>エンドポイントに発話を追加する

このアプリには、意図とエンティティはありますが、エンドポイントの使用実績がありません。 エンドポイントの発話を確認することによってアプリの精度を改善するためには、このエンドポイントが使用されることが必要です。

1. [!INCLUDE [LUIS How to get endpoint first step](includes/howto-get-endpoint.md)]

1. エンドポイントを使用して次の発話を追加します。

    |エンドポイントの発話|対応する意図|
    |--|--|
    |`I'm looking for a job with Natural Language Processing`|`GetJobInformation`|
    |`I want to cancel on March 3`|`Utilities.Cancel`|
    |`When were HRF-123456 and hrf-234567 published in the last year?`|`FindForm`|
    |`shift 123-45-6789 from Z-1242 to T-54672`|`MoveEmployee`|
    |`Please relocation jill-jones@mycompany.com from x-2345 to g-23456`|`MoveEmployee`|
    |`Here is my c.v. for the programmer job`|`ApplyForJob`|
    |`This is the lead welder paperwork.`|`ApplyForJob`|
    |`does form hrf-123456 cover the new dental benefits and medical plan`|`FindForm`|
    |`Jill Jones work with the media team on the public portal was amazing`|`EmployeeFeedback`|

    どのバージョンをアクティブに編集しているか、またはどのバージョンのアプリがエンドポイントで発行されたかには関係なく、確認すべき発話のプールが 1 つあります。

## <a name="review-endpoint-utterances"></a>エンドポイントの発言の確認

エンドポイントの発話を確認して、意図を正しく対応付けます。 確認すべき発話のプールはすべてのバージョンについて 1 つですが、意図を正しく対応付けるプロセスによって発話の例が追加されるのは、最新の "_アクティブ モデル_" だけです。

1. ポータルの **[Build]\(ビルド\)** セクションで、左側のナビゲーションから **[Review endpoint utterances]\(エンドポイントの発話の確認\)** を選択します。 このリストは、**ApplyForJob** という意図がフィルターで抽出されています。

    > [!div class="mx-imgBorder"]
    > ![左側のナビゲーションにある [Review endpoint utterances]\(エンドポイントの発話の確認\) ボタンのスクリーンショット](./media/luis-tutorial-review-endpoint-utterances/review-endpoint-utterances-with-entity-view.png)

    この発話 `I'm looking for a job with Natural Language Processing` は、適切な意図内にありません。

1.  この発話を対応付けるには、発話行で `GetJobInformation` という **[Aligned Intent]\(対応する意図\)** を正しく選択します。 チェックマークを選択すると、変更した発話がアプリに追加されます。

    > [!div class="mx-imgBorder"]
    > ![左側のナビゲーションにある [Review endpoint utterances]\(エンドポイントの発話の確認\) ボタンのスクリーンショット](./media/luis-tutorial-review-endpoint-utterances/select-correct-aligned-intent-for-endpoint-utterance.png)

    この意図に関して、残りの発話を確認します。必要に応じて対応する意図を修正してください。 対応する意図を確認するには、このチュートリアルの最初の発話テーブルを使用します。

    修正済みの発話は **[Review endpoint utterances]\(エンドポイントの発話の確認\)** リストから消えます。 さらに発話が表示された場合は、引き続きリストの項目を処理します。リストが空になるまで意図の対応を修正してください。

    エンティティのラベル付けに対する修正は、意図の対応付けが済んだ後に、[Intent details]\(意図の詳細\) ページから行います。

1. アプリを再度トレーニングして発行します。

## <a name="get-intent-prediction-from-endpoint"></a>エンドポイントから意図の予測を取得する

発話の例を正しく対応付けることによってアプリの予測精度が向上したことを確認するために、修正した発話に近い発話を試してみましょう。

1. [!INCLUDE [LUIS How to get endpoint first step](../../../includes/cognitive-services-luis-tutorial-how-to-get-endpoint.md)]

1. アドレスの URL の末尾に移動し、「`Are there any natural language processing jobs in my department right now?`」と入力します。 最後の querystring パラメーターは `q` です。これは発話の**クエリ**です。

   ```json
    {
        "query": "Are there any natural language processing jobs in my department right now?",
        "prediction": {
            "topIntent": "GetJobInformation",
            "intents": {
                "GetJobInformation": {
                    "score": 0.903607249
                },
                "EmployeeFeedback": {
                    "score": 0.0312187821
                },
                "ApplyForJob": {
                    "score": 0.0230276529
                },
                "MoveEmployee": {
                    "score": 0.008322801
                },
                "Utilities.Stop": {
                    "score": 0.004480808
                },
                "FindForm": {
                    "score": 0.00425248267
                },
                "Utilities.StartOver": {
                    "score": 0.004224336
                },
                "Utilities.Help": {
                    "score": 0.00373591436
                },
                "None": {
                    "score": 0.0034621188
                },
                "Utilities.Cancel": {
                    "score": 0.00230977475
                },
                "Utilities.Confirm": {
                    "score": 0.00112078607
                }
            },
            "entities": {
                "keyPhrase": [
                    "natural language processing jobs",
                    "department"
                ],
                "datetimeV2": [
                    {
                        "type": "datetime",
                        "values": [
                            {
                                "timex": "PRESENT_REF",
                                "resolution": [
                                    {
                                        "value": "2019-12-05 23:23:53"
                                    }
                                ]
                            }
                        ]
                    }
                ],
                "$instance": {
                    "keyPhrase": [
                        {
                            "type": "builtin.keyPhrase",
                            "text": "natural language processing jobs",
                            "startIndex": 14,
                            "length": 32,
                            "modelTypeId": 2,
                            "modelType": "Prebuilt Entity Extractor",
                            "recognitionSources": [
                                "model"
                            ]
                        },
                        {
                            "type": "builtin.keyPhrase",
                            "text": "department",
                            "startIndex": 53,
                            "length": 10,
                            "modelTypeId": 2,
                            "modelType": "Prebuilt Entity Extractor",
                            "recognitionSources": [
                                "model"
                            ]
                        }
                    ],
                    "datetimeV2": [
                        {
                            "type": "builtin.datetimeV2.datetime",
                            "text": "right now",
                            "startIndex": 64,
                            "length": 9,
                            "modelTypeId": 2,
                            "modelType": "Prebuilt Entity Extractor",
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

   不確かな発話が正しく対応付けられたので、**高いスコア**で正しい意図が予測されています。

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
