---
title: ナレッジ ベースを改善する - QnA Maker
titleSuffix: Azure Cognitive Services
description: アクティブ ラーニングを使用すると、質問と回答のペアに対して、ユーザーの送信内容に基づく代わりの質問を提案することで、ナレッジ ベースの品質を改善できます。 それらの提案を検討し、既存の質問に追加するか却下します。 ナレッジ ベースが自動的に変更されることはありません。 変更を有効にするためには、提案を受け入れる必要があります。 これらの提案によって質問が追加されますが、既存の質問の変更や削除は行われません。
author: diberry
manager: nitinme
services: cognitive-services
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: article
ms.date: 03/21/2019
ms.author: diberry
ms.openlocfilehash: dbdd0165e276e5c82f8d4c15ef70d3a541d76bc0
ms.sourcegitcommit: 6da4959d3a1ffcd8a781b709578668471ec6bf1b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/27/2019
ms.locfileid: "58522198"
---
# <a name="use-active-learning-to-improve-your-knowledge-base"></a>アクティブ ラーニングを使用してナレッジ ベースを改善する

アクティブ ラーニングを使用すると、質問と回答のペアに対して、ユーザーの送信内容に基づく代わりの質問を提案することで、ナレッジ ベースの品質を改善できます。 それらの提案を検討し、既存の質問に追加するか却下します。 

ナレッジ ベースが自動的に変更されることはありません。 変更を有効にするためには、提案を受け入れる必要があります。 これらの提案によって質問が追加されますが、既存の質問の変更や削除は行われません。

## <a name="what-is-active-learning"></a>アクティブ ラーニングとは

QnA Maker は、暗黙的および明示的フィードバックによって、新しい質問のバリエーションを学習します。
 
* 暗黙的フィードバック – ランカーは、ユーザーの質問に、スコアが非常に近い回答が複数ある状況を認識して、これをフィードバックと見なします。 
* 明示的フィードバック – ナレッジ ベースから、スコアのバリエーションがほとんどない回答が複数返されると、クライアント アプリケーションはユーザーに、どの質問が正しい質問であるかを尋ねます。 ユーザーの明示的フィードバックは、Train API を使用して QnA Maker に送信されます。 

どちらの方法でも、クラスター化されている類似のクエリがランカーに提供されます。

## <a name="how-active-learning-works"></a>アクティブ ラーニングの動作方法

アクティブ ラーニングは、特定のクエリに対して QnA Maker によって返される、上位いくつかの回答のスコアに基づいてトリガーされます。 スコアの違いが狭い範囲内にある場合、そのクエリは、回答の各候補についてあり得る _提案_ であると見なされます。 

すべての提案は類似度によって一緒にクラスター化され、エンドユーザーによる特定のクエリの頻度に基づいて、代わりの質問に対する上位の提案が表示されます。 アクティブ ラーニングでは、エンドポイントが、妥当な量で多様性のある使用状況クエリを受け取っている場合、可能な限り最適な提案が示されます。

類似のクエリが 5 つ以上クラスター化された場合、QnA Maker は 30 分おきに、ユーザー ベースの質問をナレッジ ベース デザイナーに提案し、承認または却下を求めます。

QnA Maker ポータルで質問が提案されたら、それらの提案をレビューして、承認または拒否する必要があります。 

## <a name="upgrade-your-version-to-use-active-learning"></a>アクティブ ラーニングを使用するバージョンへのアップグレード

アクティブ ラーニングは、ランタイム バージョン 4.4.0 以上でサポートされています。 ナレッジ ベースが以前のバージョンで作成された場合は、この機能を使用するために[ランタイムをアップグレード](troubleshooting-runtime.md#how-to-get-latest-qnamaker-runtime-updates)します。 

## <a name="best-practices"></a>ベスト プラクティス

アクティブ ラーニングを使用する場合のベスト プラクティスについては、[ベスト プラクティス](../Concepts/best-practices.md#active-learning)に関するページを参照してください。

## <a name="score-proximity-between-knowledge-base-questions"></a>ナレッジ ベースの質問間の近さのスコア付け

質問のスコアの信頼度が高い (80% など) 場合、アクティブ ラーニング用に適すると見なされるスコアの範囲は広く、およそ 10% 以内です。 信頼度スコアが低下すると (40% など)、スコアの範囲も狭まり、約 4% 以内となります。 

近さを判定するアルゴリズムは、単純な計算ではありません。 前述の例の範囲は、固定的なものではなく、アルゴリズムの影響を理解する指針としてのみ使用する必要があります。

## <a name="turn-on-active-learning"></a>アクティブ ラーニングを有効にする

アクティブ ラーニングは、既定では無効になっています。 これを有効にして、提案された質問を表示します。 

1. **[発行]** を選択してナレッジ ベースを発行します。 アクティブ ラーニング クエリは、GenerateAnswer API 予測エンドポイントからのみ収集されます。 Qna Maker ポータルの [テスト] ウィンドウへのクエリは、アクティブ ラーニングには影響しません。

1. アクティブ ラーニングを有効にするには、自分の**名前**をクリックし、QnA Maker ポータルの右上隅にある [**[Service Settings]\(サービス設定\)**](https://www.qnamaker.ai/UserSettings) に移動します。  

    ![[Service settings]\(サービス設定\) ページで、アクティブ ラーニングの提案された代わりの質問を有効にします。 右上のメニューで自分のユーザー名を選択し、[Service Settings]\(サービス設定\) を選択します。](../media/improve-knowledge-base/Endpoint-Keys.png)


1. QnA Maker サービスを見つけて **[Active Learning] (アクティブ ラーニング)** を切り替えます。 

    [![[Service settings]\(サービス設定\) ページで、アクティブ ラーニング機能をオンに切り替えます。機能を切り替えられないときは、サービスをアップグレードしなければならない場合があります。](../media/improve-knowledge-base/turn-active-learning-on-at-service-setting.png)](../media/improve-knowledge-base/turn-active-learning-on-at-service-setting.png#lightbox)

    **[Active Learning] (アクティブ ラーニング)** が有効になると、ユーザーが送信した質問に基づいて、ナレッジから定期的に新しい質問が提案されます。 設定を再度切り替えると、**[Active Learning] (アクティブ ラーニング)** を無効にできます。

## <a name="add-active-learning-suggestion-to-knowledge-base"></a>アクティブ ラーニングの提案をナレッジ ベースに追加する

1. 提案された質問を表示するには、ナレッジ ベースの **[Edit] (編集)** ページで **[Show Suggestions] (提案の表示)** を選択します。 

    [![アクティブ ラーニングの新しい代わりの質問を表示するためには、ポータルの[Edit]\(編集\) セクションで [Show Suggestions]\(提案の表示\) を選択します。](../media/improve-knowledge-base/show-suggestions-button.png)](../media/improve-knowledge-base/show-suggestions-button.png#lightbox)

1. **[Filter by Suggestions]\(提案別にフィルターを適用\)** を選択し、質問と回答のペアでナレッジ ベースをフィルター処理すると、提案だけが表示されます。

    [![アクティブ ラーニングの代わりの質問のみを表示する場合は、[Filter by Suggestions]\(提案別にフィルターを適用\) トグルを使用します。](../media/improve-knowledge-base/filter-by-suggestions.png)](../media/improve-knowledge-base/filter-by-suggestions.png#lightbox)

1.  提案を含む各質問のセクションには、質問を受け入れる `✔` チェックマークまたは却下する `x` チェック マークが付いた新しい質問が表示されます。 質問を追加するにはチェック マークを選択します。 

    [![緑色のチェック マークまたは赤色の削除マークを選択して、アクティブ ラーニングの提案された代わりの質問を選択または拒否します。](../media/improve-knowledge-base/accept-active-learning-suggestions.png)](../media/improve-knowledge-base/accept-active-learning-suggestions.png#lightbox)

    **[Add all] (すべて追加)** または **[Reject all] (すべて却下)** を選択し、_すべての提案_ を追加または削除できます。

1. **[Save and train] (保存してトレーニング)** を選択し、ナレッジ ベースに変更を保存します。

1. **[発行]** を選択して、変更を GenerateAnswer API から使用できるようにします。

    類似のクエリが 5 つ以上クラスター化された場合、QnA Maker は 30 分おきに、ユーザー ベースの質問をナレッジ ベース デザイナーに提案し、承認または却下を求めます。

## <a name="determine-best-choice-when-several-questions-have-similar-scores"></a>似たスコアを持つ質問が複数ある場合に最適な選択肢を特定する

ある質問のスコアが別の質問のスコアに近すぎる場合、クライアント アプリケーション開発者は、明確化を求めることを選択できます。

### <a name="use-the-top-property-in-the-generateanswer-request"></a>GenerateAnswer 要求で最上位のプロパティを使用する

回答のために QnA Maker に質問が送信されたときには、JSON 本文の一部で、上位の回答を複数返すことができます。

```json
{
    "question": "wi-fi",
    "isTest": false,
    "top": 3
}
```

チャット ボットなどのクライアント アプリケーションが応答を受け取ったときに、上位 3 つの質問が返されます。

```json
{
    "answers": [
        {
            "questions": [
                "Wi-Fi Direct Status Indicator"
            ],
            "answer": "**Wi-Fi Direct Status Indicator**\n\nStatus bar icons indicate your current Wi-Fi Direct connection status:  \n\nWhen your device is connected to another device using Wi-Fi Direct, '$  \n\n+ •+ ' Wi-Fi Direct is displayed in the Status bar.",
            "score": 74.21,
            "id": 607,
            "source": "Bugbash KB.pdf",
            "metadata": []
        },
        {
            "questions": [
                "Wi-Fi - Connections"
            ],
            "answer": "**Wi-Fi**\n\nWi-Fi is a term used for certain types of Wireless Local Area Networks (WLAN). Wi-Fi communication requires access to a wireless Access Point (AP).",
            "score": 74.15,
            "id": 599,
            "source": "Bugbash KB.pdf",
            "metadata": []
        },
        {
            "questions": [
                "Turn Wi-Fi On or Off"
            ],
            "answer": "**Turn Wi-Fi On or Off**\n\nTurning Wi-Fi on makes your device able to discover and connect to compatible in-range wireless APs.  \n\n1.  From a Home screen, tap ::: Apps > e Settings .\n2.  Tap Connections > Wi-Fi , and then tap On/Off to turn Wi-Fi on or off.",
            "score": 69.99,
            "id": 600,
            "source": "Bugbash KB.pdf",
            "metadata": []
        }
    ]
}
```

### <a name="client-application-follow-up-when-questions-have-similar-scores"></a>質問のスコアが類似しているときのクライアント アプリケーションのフォロー アップ

クライアント アプリケーションは、ユーザーが自分の意図を最も適切に表している質問を選択するためのオプションを示して、すべての質問を表示します。 

ユーザーが既存の質問の 1 つを選択すると、QnA Maker の Train API を使用して、ユーザーの選択がフィードバックとしてクライアント アプリケーションから送信されます。 このフィードバックにより、アクティブ ラーニングのフィードバック ループが完了します。 

エンド ツー エンドのシナリオでのアクティブ ラーニングについて確認するには、[Azure Bot C# の例](https://github.com/Microsoft/BotBuilder-Samples/tree/master/experimental/csharp_dotnetcore/qnamaker-activelearning-bot) を使用してください。

## <a name="train-api"></a>Train API

アクティブ ラーニングのフィードバックは、Train API POST 要求を使用して QnA Maker に送信されます。 API シグネチャは次のとおりです。

```http
POST https://<QnA-Maker-resource-name>.azurewebsites.net/qnamaker/knowledgebases/<knowledge-base-ID>/train
Authorization: EndpointKey <endpoint-key>
Content-Type: application/json
{"feedbackRecords": [{"userId": "1","userQuestion": "<question-text>","qnaId": 1}]}
```

|HTTP 要求プロパティ|名前|Type|目的|
|--|--|--|--|
|URL ルート パラメーター|ナレッジ ベース ID|string|ナレッジ ベースの GUID。|
|ホストのサブドメイン|QnAMaker リソース名|string|Azure サブスクリプション内の QnA Maker のホスト名。 これは、ナレッジ ベースを公開した後に、[設定] ページで利用できます。 |
|ヘッダー|Content-Type|string|API に送信される本文のメディアの種類。 既定値は `application/json` です。|
|ヘッダー|Authorization|string|エンドポイント キー (EndpointKey xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx)。|
|Post 本文|JSON オブジェクト|JSON|トレーニングのフィードバック|

JSON の本文には、次のようないくつかの設定があります。

|JSON 本文のプロパティ|Type|目的|
|--|--|--|--|
|`feedbackRecords`|array|フィードバックの一覧。|
|`userId`|string|提案された質問を受け入れるユーザーのユーザー ID。 ユーザー ID の形式は、ユーザーが決定します。 たとえば、電子メール アドレスをアーキテクチャ内の有効なユーザー ID とすることができます。 省略可能。|
|`userQuestion`|string|質問の正確なテキスト。 必須。|
|`qnaID`|number|質問の ID。[GenerateAnswer 応答](metadata-generateanswer-usage.md#generateanswer-response-properties)内にあります。 |

JSON 本文の例は、次のようになります。

```json
{
    "feedbackRecords": [
        {
            "userId": "1",
            "userQuestion": "<question-text>",
            "qnaId": 1
        }
    ]
}
```

成功した応答は、ステータス 204 を返し、JSON 応答の本文は返しません。 

<a name="active-learning-is-saved-in-the-exported-apps-tsv-file"></a>

## <a name="active-learning-is-saved-in-the-exported-knowledge-base"></a>アクティブ ラーニングはエクスポートされたナレッジ ベースに保存されます

アプリでアクティブ ラーニングが有効になっている場合、アプリをエクスポートすると、tsv ファイル内の `SuggestedQuestions` 列にアクティブ ラーニング データが保持されます。 

`SuggestedQuestions` 列は、暗黙的 (`autosuggested`) および明示的 (`usersuggested`) なフィードバック情報の JSON オブジェクトです。 次に示すのは、ユーザーが送信した 1 つの `help` の質問に対応する、この JSON オブジェクトの例です。

```JSON
[
    {
        "clusterHead": "help",
        "totalAutoSuggestedCount": 1,
        "totalUserSuggestedCount": 0,
        "alternateQuestionList": [
            {
                "question": "help",
                "autoSuggestedCount": 1,
                "userSuggestedCount": 0
            }
        ]
    }
]
```

このアプリを再インポートすると、アクティブ ラーニングは引き続き情報を収集し、ナレッジ ベースに対する提案を提示します。 

## <a name="next-steps"></a>次の手順
 
> [!div class="nextstepaction"]
> [メタデータと GenerateAnswer API の使用](metadata-generateanswer-usage.md)
