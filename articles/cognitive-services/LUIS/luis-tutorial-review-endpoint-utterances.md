---
title: チュートリアル:エンドポイントの発話の確認 - LUIS
description: このチュートリアルでは、LUIS HTTP エンドポイント経由で受け取った LUIS にとって不確かな発話を確認または修正することによって、アプリの予測精度を高めます。 いくつかの発話についての意図を確認したり、その他の発話のエンティティを確認したりすることが必要な場合もあります。
services: cognitive-services
ms.topic: tutorial
ms.date: 07/02/2020
ms.openlocfilehash: 082e625efeeb4764aaa1ac5101eb2b0013348b19
ms.sourcegitcommit: 845a55e6c391c79d2c1585ac1625ea7dc953ea89
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/05/2020
ms.locfileid: "85959038"
---
# <a name="tutorial-fix-unsure-predictions-by-reviewing-endpoint-utterances"></a>チュートリアル:エンドポイントの発話を確認して不確かな予測を修正する
このチュートリアルでは、LUIS HTTPS エンドポイント経由で受け取った LUIS にとって不確かな発話を確認または修正することによって、アプリの予測精度を高めます。 LUIS の定期メンテナンスの通常の部分としてエンドポイントの発話を確認する必要があります。

このレビュー プロセスによって、LUIS はユーザーのアプリ ドメインを学習することができます。 レビュー リストにある発話が LUIS によって選択されます。 このリストには、次の特徴があります。

* アプリに固有である。
* アプリの予測精度を高めることを意図している。
* 定期的にレビューする必要がある。

エンドポイントの発話をレビューすることによって、その発話から予測される意図を確認または修正することになります。

**このチュートリアルで学習する内容は次のとおりです。**

<!-- green checkmark -->
> [!div class="checklist"]
> * サンプル アプリをインポートする
> * エンドポイントの発言の確認
> * アプリをトレーニングして公開する
> * アプリのエンドポイントをクエリして LUIS JSON の応答を表示する

[!INCLUDE [LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="download-json-file-for-app"></a>アプリの JSON ファイルをダウンロードする

[アプリの JSON ファイル](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/luis/apps/tutorial-fix-unsure-predictions.json?raw=true)をダウンロードして保存します。

## <a name="import-json-file-for-app"></a>アプリの JSON ファイルをインポートする


1. [LUIS ポータル](https://www.luis.ai)の **[マイ アプリ]** ページで、 **[+ New app for conversation]\(+ 会話用の新しいアプリ\)** を選択し、次に **[Import as JSON]\(JSON としてインポート\)** を選択します。 前の手順で保存した JSON ファイルを見つけます。 アプリの名前を変更する必要はありません。 **[完了]** を選択します

1. **[ビルド]** を選択してから **[Intents]\(意図\)** を選択して、LUIS アプリのメインの構成要素である意図を確認します。

    :::image type="content" source="media/luis-tutorial-review-endpoint-utterances/initial-intents-in-app.png" alt-text="[バージョン] ページから [Intents]\(意図\) ページに移動する。":::

## <a name="train-the-app-to-apply-the-entity-changes-to-the-app"></a>アプリをトレーニングしてエンティティの変更をアプリに適用する

[!INCLUDE [LUIS How to Train steps](includes/howto-train.md)]

## <a name="publish-the-app-to-access-it-from-the-http-endpoint"></a>アプリを発行して HTTP エンドポイントからアクセスする

[!INCLUDE [LUIS How to Publish steps](includes/howto-publish.md)]

## <a name="add-utterances-at-the-endpoint"></a>エンドポイントに発話を追加する

このアプリには、意図とエンティティはありますが、エンドポイントの使用実績がありません。 エンドポイントの発話を確認することによってアプリの精度を改善するためには、このエンドポイントが使用されることが必要です。

1. [!INCLUDE [LUIS How to get endpoint first step](includes/howto-get-endpoint.md)]

1. アドレス バーの URL の末尾に移動して、_YOUR_QUERY_HERE_ を以下の表の発話に置き換えます。 それぞれの発話について、発話を送信してその結果を取得します。 次に、末尾の発話を次の発話に置き換えます。

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

    :::image type="content" source="./media/luis-tutorial-review-endpoint-utterances/review-endpoint-utterances-with-entity-view.png" alt-text="左側のナビゲーションにある [Review endpoint utterances]\(エンドポイントの発話の確認\) ボタンのスクリーンショット。":::

    この `I'm looking for a job with Natural Language Processing` という発話は、適切な意図 _GetJobInformation_ 内にはありません。 2 つの意図内のジョブ名と動詞が似ているために、誤って _ApplyForJob_ として予測されています。

1.  この発話を対応付けるには、`GetJobInformation` という適切な **[Aligned Intent]\(対応する意図\)** を選択します。 チェックマークを選択すると、変更した発話がアプリに追加されます。

    この意図に関して、残りの発話を確認します。必要に応じて対応する意図を修正してください。 対応する意図を確認するには、このチュートリアルの最初の発話テーブルを使用します。

    修正済みの発話は **[Review endpoint utterances]\(エンドポイントの発話の確認\)** リストから消えます。 さらに発話が表示された場合は、引き続きリストの項目を処理します。リストが空になるまで意図の対応を修正してください。

    エンティティのラベル付けに対する修正は、意図の対応付けが済んだ後に、[Intent details]\(意図の詳細\) ページから行います。

1. アプリを再度トレーニングして発行します。

## <a name="get-intent-prediction-from-endpoint"></a>エンドポイントから意図の予測を取得する

発話の例を正しく対応付けることによってアプリの予測精度が向上したことを確認するために、修正した発話に近い発話を試してみましょう。

1. [!INCLUDE [LUIS How to get endpoint first step](includes/howto-get-endpoint.md)]

1. アドレス バーの URL の末尾に移動して、_YOUR_QUERY_HERE_ を `Are there any natural language processing jobs in my department right now?` に置き換えます。

   ```json
    {
        "query": "Are there any natural language processing jobs in my department right now?",
        "prediction": {
            "topIntent": "GetJobInformation",
            "intents": {
                "GetJobInformation": {
                    "score": 0.901367366
                },
                "ApplyForJob": {
                    "score": 0.0307973567
                },
                "EmployeeFeedback": {
                    "score": 0.0296942145
                },
                "MoveEmployee": {
                    "score": 0.00739785144
                },
                "FindForm": {
                    "score": 0.00449316856
                },
                "Utilities.Stop": {
                    "score": 0.00417657848
                },
                "Utilities.StartOver": {
                    "score": 0.00407167152
                },
                "Utilities.Help": {
                    "score": 0.003662492
                },
                "None": {
                    "score": 0.00335733569
                },
                "Utilities.Cancel": {
                    "score": 0.002225436
                },
                "Utilities.Confirm": {
                    "score": 0.00107437756
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
                                        "value": "2020-07-02 21:45:50"
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

## <a name="clean-up-resources"></a>リソースをクリーンアップする

[!INCLUDE [LUIS How to clean up resources](../../../includes/cognitive-services-luis-tutorial-how-to-clean-up-resources.md)]

## <a name="next-steps"></a>次のステップ

このチュートリアルでは、エンドポイントで送信された、LUIS にとって不確かな発話が確認されました。 これら発話が検証され、発話の例として正しい意図に移動されると、LUIS の予測精度が向上します。

> [!div class="nextstepaction"]
> [パターンの使用方法を確認する](luis-tutorial-pattern.md)
