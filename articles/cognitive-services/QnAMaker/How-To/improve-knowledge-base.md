---
title: アクティブ ラーニングの提案される質問 - QnA Maker
description: アクティブ ラーニングを使用してナレッジ ベースの質を向上させます。 既存の質問の削除や変更は行わずに、レビュー、承認、却下を行います。
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 04/06/2020
ms.custom: devx-track-js, devx-track-csharp
ms.openlocfilehash: 3fe6ee8336872c04e85b732713494adf0fefa28a
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "103011444"
---
# <a name="accept-active-learning-suggested-questions-in-the-knowledge-base"></a>ナレッジ ベースでアクティブ ラーニングにより提案された質問を受け入れる


<a name="accept-an-active-learning-suggestion-in-the-knowledge-base"></a>

提案を承認し、保存してトレーニングした後、アクティブラーニングによりナレッジ ベースまたは Search Service が変更されます。 提案を承認すると、代わりの質問として追加されます。

## <a name="turn-on-active-learning"></a>アクティブ ラーニングを有効にする

提案された質問を表示するには、QnA Maker リソースに対して[アクティブ ラーニングを有効にする](../concepts/active-learning-suggestions.md)必要があります。

## <a name="view-suggested-questions"></a>提案された質問を表示する

1. 提案された質問を表示するには、ナレッジ ベースの **[Edit]\(編集\)** ページで、 **[View Options]\(オプションの表示\)** 、 **[Show active learning suggestions]\(アクティブ ラーニングの提案を表示\)** の順に選択します。 質問と回答のペアのいずれにも提案がない場合、このオプションは無効になります。  

    [![アクティブ ラーニングの新しい代わりの質問を表示するためには、ポータルの[Edit]\(編集\) セクションで [Show Suggestions]\(提案の表示\) を選択します。](../media/improve-knowledge-base/show-suggestions-button.png)](../media/improve-knowledge-base/show-suggestions-button.png#lightbox)

1. **[Filter by Suggestions]\(提案別にフィルターを適用\)** を選択し、質問と回答のペアでナレッジ ベースをフィルター処理すると、提案だけが表示されます。

    [![アクティブ ラーニングの代わりの質問のみを表示する場合は、[Filter by Suggestions]\(提案別にフィルターを適用\) トグルを使用します。](../media/improve-knowledge-base/filter-by-suggestions.png)](../media/improve-knowledge-base/filter-by-suggestions.png#lightbox)

1. 各 QnA ペアでは、質問を受け入れるチェック マーク (`✔`) または却下する `x` が付いた新しい代わりの質問が提案されます。 質問を追加するにはチェック マークを選択します。

    [![緑色のチェック マークまたは赤色の削除マークを選択して、アクティブ ラーニングの提案された代わりの質問を選択または拒否します。](../media/improve-knowledge-base/accept-active-learning-suggestions-small.png)](../media/improve-knowledge-base/accept-active-learning-suggestions.png#lightbox)

    コンテキスト ツールバーの **[Add all] (すべて追加)** または **[Reject all] (すべて却下)** を選択することで、_すべての提案_ を追加または削除できます。

1. **[Save and train] (保存してトレーニング)** を選択し、ナレッジ ベースに変更を保存します。

1. **[発行]** を選択して、変更を [GenerateAnswer API](metadata-generateanswer-usage.md#generateanswer-request-configuration) から使用できるようにします。

    類似のクエリが 5 つ以上クラスター化された場合、QnA Maker は 30 分ごとに、ユーザーが承認または却下する代わりの質問を提案します。


<a name="#score-proximity-between-knowledge-base-questions"></a>

## <a name="active-learning-suggestions-are-saved-in-the-exported-knowledge-base"></a>アクティブ ラーニングの提案は、エクスポートされたナレッジ ベースに保存されます

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


### <a name="architectural-flow-for-using-generateanswer-and-train-apis-from-a-bot"></a>ボットから GenerateAnswer および Train API を使用するためのアーキテクチャの流れ

ボットやその他のクライアント アプリケーションは、次のアーキテクチャの流れを利用してアクティブ ラーニングを使用する必要があります。

1. ボットは、GenerateAnswer API で `top` プロパティを使用していくつかの回答を取得することによって、[ナレッジ ベースから回答を取得](#use-the-top-property-in-the-generateanswer-request-to-get-several-matching-answers)します。

2. ボットは次のようにして明示的フィードバックを特定します。
    * ユーザー独自の[カスタム ビジネス ロジック](#use-the-score-property-along-with-business-logic-to-get-list-of-answers-to-show-user)を使用して、低いスコアを除外します。
    * ボットまたはクライアント アプリケーションでは、回答候補の一覧をユーザーに表示し、ユーザーが選択した回答を取得します。
3. ボットは [Train API](#train-api) によって、[選択された回答を QnA Maker に送り返します](#bot-framework-sample-code)。

### <a name="use-the-top-property-in-the-generateanswer-request-to-get-several-matching-answers"></a>GenerateAnswer 要求で top プロパティを使用して、一致する回答をいくつか取得する

回答を求めて QnA Maker に質問を送信すると、JSON 本文の `top` プロパティにより、返す回答の数が設定されます。

```json
{
    "question": "wi-fi",
    "isTest": false,
    "top": 3
}
```

### <a name="use-the-score-property-along-with-business-logic-to-get-list-of-answers-to-show-user"></a>ビジネス ロジックと一緒に score プロパティを使用して、ユーザーに表示する回答の一覧を取得する

チャット ボットなどのクライアント アプリケーションが応答を受け取るときには、上位 3 つの質問が返されます。 `score` プロパティを使用して、スコア間の近さを分析します。 この近さの範囲は、ユーザー独自のビジネス ロジックによって判定されます。

```json
{
    "answers": [
        {
            "questions": [
                "Wi-Fi Direct Status Indicator"
            ],
            "answer": "**Wi-Fi Direct Status Indicator**\n\nStatus bar icons indicate your current Wi-Fi Direct connection status:  \n\nWhen your device is connected to another device using Wi-Fi Direct, '$  \n\n+ *+ ' Wi-Fi Direct is displayed in the Status bar.",
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

## <a name="client-application-follow-up-when-questions-have-similar-scores"></a>質問のスコアが類似しているときのクライアント アプリケーションのフォロー アップ

クライアント アプリケーションでは、意図を最も適切に表している _1 つの質問_ をユーザーが選択するためのオプションを示して、質問が表示されます。

ユーザーが既存の質問の 1 つを選択すると、QnA Maker の Train API を使用して、ユーザーの選択がフィードバックとしてクライアント アプリケーションから送信されます。 このフィードバックにより、アクティブ ラーニングのフィードバック ループが完了します。

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
|カスタム サブドメイン|QnAMaker リソース名|string|リソース名は、QnA Maker のカスタム サブドメインとして使用されます。 これは、ナレッジ ベースを公開した後に、[設定] ページで利用できます。 これは `host` として表示されます。|
|ヘッダー|Content-Type|string|API に送信される本文のメディアの種類。 既定値は `application/json` です。|
|ヘッダー|承認|string|エンドポイント キー (EndpointKey xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx)。|
|Post 本文|JSON オブジェクト|JSON|トレーニングのフィードバック|

JSON の本文には、次のようないくつかの設定があります。

|JSON 本文のプロパティ|Type|目的|
|--|--|--|--|
|`feedbackRecords`|array|フィードバックの一覧。|
|`userId`|string|提案された質問を受け入れるユーザーのユーザー ID。 ユーザー ID の形式は、ユーザーが決定します。 たとえば、電子メール アドレスをアーキテクチャ内の有効なユーザー ID とすることができます。 省略可能。|
|`userQuestion`|string|ユーザーのクエリの正確なテキスト。 必須。|
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

### <a name="batch-many-feedback-records-into-a-single-call"></a>多くのフィードバック レコードを単一呼び出しにバッチ処理する

ボットなどのクライアント側アプリケーションでは、データを格納してから、多くのレコードを 1 つの JSON 本文の `feedbackRecords` 配列に入れて送信できます。

JSON 本文の例は、次のようになります。

```json
{
    "feedbackRecords": [
        {
            "userId": "1",
            "userQuestion": "How do I ...",
            "qnaId": 1
        },
        {
            "userId": "2",
            "userQuestion": "Where is ...",
            "qnaId": 40
        },
        {
            "userId": "3",
            "userQuestion": "When do I ...",
            "qnaId": 33
        }
    ]
}
```



<a name="active-learning-is-saved-in-the-exported-apps-tsv-file"></a>

## <a name="bot-framework-sample-code"></a>ボット フレームワークのサンプル コード

ボット フレームワークのコードでは、ユーザーのクエリをアクティブ ラーニングに使用する必要がある場合は Train API を呼び出す必要があります。 記述するコードは 2 種類あります。

* クエリをアクティブ ラーニングに使用するかどうかを指定する
* アクティブ ラーニングのためにクエリを QnA Maker の Train API に送り返す

[Azure Bot のサンプル](https://github.com/microsoft/BotBuilder-Samples)では、これらのアクティビティの両方がプログラムされています。

### <a name="example-c-code-for-train-api-with-bot-framework-4x"></a>Bot Framework 4.x での Train API のための C# コードの例

次のコードは、Train API を使用して情報を QnA Maker に送り返す方法を示しています。

```csharp
public class FeedbackRecords
{
    // <summary>
    /// List of feedback records
    /// </summary>
    [JsonProperty("feedbackRecords")]
    public FeedbackRecord[] Records { get; set; }
}

/// <summary>
/// Active learning feedback record
/// </summary>
public class FeedbackRecord
{
    /// <summary>
    /// User id
    /// </summary>
    public string UserId { get; set; }

    /// <summary>
    /// User question
    /// </summary>
    public string UserQuestion { get; set; }

    /// <summary>
    /// QnA Id
    /// </summary>
    public int QnaId { get; set; }
}

/// <summary>
/// Method to call REST-based QnAMaker Train API for Active Learning
/// </summary>
/// <param name="endpoint">Endpoint URI of the runtime</param>
/// <param name="FeedbackRecords">Feedback records train API</param>
/// <param name="kbId">Knowledgebase Id</param>
/// <param name="key">Endpoint key</param>
/// <param name="cancellationToken"> Cancellation token</param>
public async static void CallTrain(string endpoint, FeedbackRecords feedbackRecords, string kbId, string key, CancellationToken cancellationToken)
{
    var uri = endpoint + "/knowledgebases/" + kbId + "/train/";

    using (var client = new HttpClient())
    {
        using (var request = new HttpRequestMessage())
        {
            request.Method = HttpMethod.Post;
            request.RequestUri = new Uri(uri);
            request.Content = new StringContent(JsonConvert.SerializeObject(feedbackRecords), Encoding.UTF8, "application/json");
            request.Headers.Add("Authorization", "EndpointKey " + key);

            var response = await client.SendAsync(request, cancellationToken);
            await response.Content.ReadAsStringAsync();
        }
    }
}
```

### <a name="example-nodejs-code-for-train-api-with-bot-framework-4x"></a>Bot Framework 4.x での Train API のための Node.js コードの例

次のコードは、Train API を使用して情報を QnA Maker に送り返す方法を示しています。

```javascript
async callTrain(stepContext){

    var trainResponses = stepContext.values[this.qnaData];
    var currentQuery = stepContext.values[this.currentQuery];

    if(trainResponses.length > 1){
        var reply = stepContext.context.activity.text;
        var qnaResults = trainResponses.filter(r => r.questions[0] == reply);

        if(qnaResults.length > 0){

            stepContext.values[this.qnaData] = qnaResults;

            var feedbackRecords = {
                FeedbackRecords:[
                    {
                        UserId:stepContext.context.activity.id,
                        UserQuestion: currentQuery,
                        QnaId: qnaResults[0].id
                    }
                ]
            };

            // Call Active Learning Train API
            this.activeLearningHelper.callTrain(this.qnaMaker.endpoint.host, feedbackRecords, this.qnaMaker.endpoint.knowledgeBaseId, this.qnaMaker.endpoint.endpointKey);

            return await stepContext.next(qnaResults);
        }
        else{

            return await stepContext.endDialog();
        }
    }

    return await stepContext.next(stepContext.result);
}
```

## <a name="best-practices"></a>ベスト プラクティス

アクティブ ラーニングを使用する場合のベスト プラクティスについては、[ベスト プラクティス](../Concepts/best-practices.md#active-learning)に関するページを参照してください。

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [メタデータと GenerateAnswer API の使用](metadata-generateanswer-usage.md)
