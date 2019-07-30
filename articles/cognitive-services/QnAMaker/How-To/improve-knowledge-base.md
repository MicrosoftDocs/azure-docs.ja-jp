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
ms.date: 06/19/2019
ms.author: diberry
ms.openlocfilehash: b73884e544ea1b8ee76c8a891048e6a8e17d6ab3
ms.sourcegitcommit: b7a44709a0f82974578126f25abee27399f0887f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/18/2019
ms.locfileid: "67204087"
---
# <a name="use-active-learning-to-improve-your-knowledge-base"></a>アクティブ ラーニングを使用してナレッジ ベースを改善する

アクティブ ラーニングを使用すると、質問と回答のペアに対して、ユーザーの送信内容に基づく代わりの質問を提案することで、ナレッジ ベースの品質を改善できます。 それらの提案を検討し、既存の質問に追加するか却下します。 

ナレッジ ベースが自動的に変更されることはありません。 変更を有効にするためには、提案を受け入れる必要があります。 これらの提案によって質問が追加されますが、既存の質問の変更や削除は行われません。

## <a name="what-is-active-learning"></a>アクティブ ラーニングとは

QnA Maker は、暗黙的および明示的フィードバックによって、新しい質問のバリエーションを学習します。
 
* [暗黙的フィードバック](#how-qna-makers-implicit-feedback-works) – ランカーは、ユーザーの質問に、スコアが非常に近い回答が複数ある状況を認識して、これをフィードバックと見なします。 これが行われるためにユーザーの操作は必要ありません。
* [明示的フィードバック](#how-you-give-explicit-feedback-with-the-train-api) – ナレッジ ベースから、スコアのバリエーションがほとんどない回答が複数返されると、クライアント アプリケーションはユーザーに、どの質問が正しい質問であるかを尋ねます。 ユーザーの明示的フィードバックは、[Train API](#train-api) を介して QnA Maker に送信されます。 

どちらの方法でも、ランカーにはクラスター化されている類似のクエリが提供されます。

## <a name="how-active-learning-works"></a>アクティブ ラーニングの動作方法

アクティブ ラーニングは、QnA Maker によって返される、上位いくつかの回答のスコアに基づいてトリガーされます。 スコアの違いが狭い範囲内にある場合、そのクエリは、QnA ペアの各候補について考えられる (代替質問としての) 提案であると見なされます。 特定の QnA ペアについて提案された質問をいったん受け入れると、他のペアについての質問は拒否されます。 提案を受け入れた後は、忘れずに保存とトレーニングを行う必要があります。

アクティブ ラーニングでは、エンドポイントが、妥当な量で多様性のある使用状況クエリを受け取っている場合、可能な限り最適な提案が示されます。 類似のクエリが 5 つ以上クラスター化された場合、QnA Maker は 30 分おきに、ユーザー ベースの質問をナレッジ ベース デザイナーに提案し、承認または却下を求めます。 すべての提案は類似度によって一緒にクラスター化され、エンドユーザーによる特定のクエリの頻度に基づいて、代わりの質問に対する上位の提案が表示されます。

QnA Maker ポータルで質問が提案されたら、それらの提案をレビューして、承認または拒否する必要があります。 提案を管理するための API はありません。

## <a name="how-qna-makers-implicit-feedback-works"></a>QnA Maker の暗黙的フィードバックの動作方法

QnA Maker の暗黙的フィードバックでは、スコアの近さを判定してからアクティブ ラーニングの提案を行うアルゴリズムが使用されます。 近さを判定するアルゴリズムは、単純な計算ではありません。 次の例の範囲は、固定的なものではなく、アルゴリズムの影響を理解する指針としてのみ使用する必要があります。

質問のスコアの信頼度が高い (80% など) 場合、アクティブ ラーニング用に適すると見なされるスコアの範囲は広く、およそ 10% 以内です。 信頼度スコアが低下すると (40% など)、スコアの範囲も狭まり、約 4% 以内となります。 

## <a name="how-you-give-explicit-feedback-with-the-train-api"></a>Train API で明示的フィードバックを提供する方法

どの回答が最適な回答であったかについて、QnA Maker が明示的フィードバックを得ることが重要です。 最適な回答をどのように決定するかはユーザーの自由で、その方法には以下が含まれる場合があります。

* いずれかの回答を選択することによるユーザーのフィードバック。
* 許容できるスコアの範囲を決定するなどのビジネス ロジック。  
* ユーザーのフィードバックとビジネス ロジックの両方の組み合わせ。

## <a name="upgrade-your-runtime-version-to-use-active-learning"></a>アクティブ ラーニングを使用するためにラインタイム バージョンをアップグレードする

アクティブ ラーニングは、ランタイム バージョン 4.4.0 以上でサポートされています。 ナレッジ ベースが以前のバージョンで作成された場合は、この機能を使用するために[ランタイムをアップグレード](troubleshooting-runtime.md#how-to-get-latest-qnamaker-runtime-updates)します。 

## <a name="turn-on-active-learning-to-see-suggestions"></a>アクティブ ラーニングを有効にして提案を表示する

アクティブ ラーニングは、既定では無効になっています。 これを有効にして、提案された質問を表示します。 アクティブ ラーニングを有効にした後は、クライアント アプリから QnA Maker に情報を送信する必要があります。 詳細については、「[ボットから GenerateAnswer および Train API を使用するためのアーキテクチャの流れ](#architectural-flow-for-using-generateanswer-and-train-apis-from-a-bot)」を参照してください。

1. **[発行]** を選択してナレッジ ベースを発行します。 アクティブ ラーニング クエリは、GenerateAnswer API 予測エンドポイントからのみ収集されます。 QnA Maker ポータルの [テスト] ウィンドウへのクエリは、アクティブ ラーニングには影響しません。

1. QnA Maker ポータルでアクティブ ラーニングを有効にするには、右上隅に移動して自分の**名前**を選択し、[**Service settings\(サービス設定\)** ](https://www.qnamaker.ai/UserSettings) に移動します。  

    ![[Service settings]\(サービス設定\) ページで、アクティブ ラーニングの提案された代わりの質問を有効にします。 右上のメニューで自分のユーザー名を選択し、[Service Settings]\(サービス設定\) を選択します。](../media/improve-knowledge-base/Endpoint-Keys.png)


1. QnA Maker サービスを見つけて **[Active Learning] (アクティブ ラーニング)** を切り替えます。 

    [![[Service settings]\(サービス設定\) ページで、アクティブ ラーニング機能をオンに切り替えます。機能を切り替えられないときは、サービスをアップグレードしなければならない場合があります。](../media/improve-knowledge-base/turn-active-learning-on-at-service-setting.png)](../media/improve-knowledge-base/turn-active-learning-on-at-service-setting.png#lightbox)

    **[Active Learning]\(アクティブ ラーニング\)** が有効になると、ユーザーが送信した質問に基づいて、ナレッジ ベースから定期的に新しい質問が提案されます。 設定を再度切り替えると、 **[Active Learning] (アクティブ ラーニング)** を無効にできます。

## <a name="accept-an-active-learning-suggestion-in-the-knowledge-base"></a>ナレッジ ベースでアクティブ ラーニングの提案を受け入れる

1. 提案された質問を表示するには、ナレッジ ベースの **[Edit]\(編集\)** ページで、 **[View Options]\(オプションの表示\)** 、 **[Show active learning suggestions]\(アクティブ ラーニングの提案を表示\)** の順に選択します。 

    [![アクティブ ラーニングの新しい代わりの質問を表示するためには、ポータルの[Edit]\(編集\) セクションで [Show Suggestions]\(提案の表示\) を選択します。](../media/improve-knowledge-base/show-suggestions-button.png)](../media/improve-knowledge-base/show-suggestions-button.png#lightbox)

1. **[Filter by Suggestions]\(提案別にフィルターを適用\)** を選択し、質問と回答のペアでナレッジ ベースをフィルター処理すると、提案だけが表示されます。

    [![アクティブ ラーニングの代わりの質問のみを表示する場合は、[Filter by Suggestions]\(提案別にフィルターを適用\) トグルを使用します。](../media/improve-knowledge-base/filter-by-suggestions.png)](../media/improve-knowledge-base/filter-by-suggestions.png#lightbox)

1. 各 QnA ペアでは、質問を受け入れるチェック マーク (`✔`) または却下する `x` が付いた新しい代わりの質問が提案されます。 質問を追加するにはチェック マークを選択します。 

    [![緑色のチェック マークまたは赤色の削除マークを選択して、アクティブ ラーニングの提案された代わりの質問を選択または拒否します。](../media/improve-knowledge-base/accept-active-learning-suggestions.png)](../media/improve-knowledge-base/accept-active-learning-suggestions.png#lightbox)

    コンテキスト ツールバーの **[Add all] (すべて追加)** または **[Reject all] (すべて却下)** を選択することで、_すべての提案_ を追加または削除できます。

1. **[Save and train] (保存してトレーニング)** を選択し、ナレッジ ベースに変更を保存します。

1. **[発行]** を選択して、変更を [GenerateAnswer API](metadata-generateanswer-usage.md#generateanswer-request-configuration) から使用できるようにします。

    類似のクエリが 5 つ以上クラスター化された場合、QnA Maker は 30 分ごとに、ユーザーが承認または却下する代わりの質問を提案します。


<a name="#score-proximity-between-knowledge-base-questions"></a>

### <a name="architectural-flow-for-using-generateanswer-and-train-apis-from-a-bot"></a>ボットから GenerateAnswer および Train API を使用するためのアーキテクチャの流れ

ボットやその他のクライアント アプリケーションは、次のアーキテクチャの流れを利用してアクティブ ラーニングを使用する必要があります。

* ボットは、GenerateAnswer API で `top` プロパティを使用していくつかの回答を取得することによって、[ナレッジ ベースから回答を取得](#use-the-top-property-in-the-generateanswer-request-to-get-several-matching-answers)します。
* ボットは次のようにして明示的フィードバックを特定します。
    * ユーザー独自の[カスタム ビジネス ロジック](#use-the-score-property-along-with-business-logic-to-get-list-of-answers-to-show-user)を使用して、低いスコアを除外します。
    * ボットまたはクライアント アプリケーションでは、回答候補の一覧をユーザーに表示し、ユーザーが選択した回答を取得します。
* ボットは [Train API](#train-api) によって、[選択された回答を QnA Maker に送り返します](#bot-framework-sample-code)。


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

## <a name="client-application-follow-up-when-questions-have-similar-scores"></a>質問のスコアが類似しているときのクライアント アプリケーションのフォロー アップ

クライアント アプリケーションでは、意図を最も適切に表している _1 つの質問_をユーザーが選択するためのオプションを示して、質問が表示されます。 

ユーザーが既存の質問の 1 つを選択すると、QnA Maker の Train API を使用して、ユーザーの選択がフィードバックとしてクライアント アプリケーションから送信されます。 このフィードバックにより、アクティブ ラーニングのフィードバック ループが完了します。 

## <a name="train-api"></a>Train API

アクティブ ラーニングのフィードバックは、Train API POST 要求を使用して QnA Maker に送信されます。 API シグネチャは次のとおりです。

```http
POST https://<QnA-Maker-resource-name>.azurewebsites.net/qnamaker/knowledgebases/<knowledge-base-ID>/train
Authorization: EndpointKey <endpoint-key>
Content-Type: application/json
{"feedbackRecords": [{"userId": "1","userQuestion": "<question-text>","qnaId": 1}]}
```

|HTTP 要求プロパティ|Name|Type|目的|
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

[Azure Bot のサンプル](https://aka.ms/activelearningsamplebot)では、これらのアクティビティの両方がプログラムされています。 

### <a name="example-c-code-for-train-api-with-bot-framework-4x"></a>Bot Framework 4.x での Train API のための C# コードの例

次のコードは、Train API を使用して情報を QnA Maker に送り返す方法を示しています。 この[完全なコード サンプル](https://github.com/microsoft/BotBuilder-Samples/tree/master/experimental/qnamaker-activelearning/csharp_dotnetcore)は GitHub で入手できます。

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
/// <param name="host">Endpoint host of the runtime</param>
/// <param name="FeedbackRecords">Feedback records train API</param>
/// <param name="kbId">Knowledgebase Id</param>
/// <param name="key">Endpoint key</param>
/// <param name="cancellationToken"> Cancellation token</param>
public async static void CallTrain(string host, FeedbackRecords feedbackRecords, string kbId, string key, CancellationToken cancellationToken)
{
    var uri = host + "/knowledgebases/" + kbId + "/train/";

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

次のコードは、Train API を使用して情報を QnA Maker に送り返す方法を示しています。 この[完全なコード サンプル](https://github.com/microsoft/BotBuilder-Samples/blob/master/experimental/qnamaker-activelearning/javascript_nodejs)は GitHub で入手できます。

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

## <a name="best-practices"></a>ベスト プラクティス

アクティブ ラーニングを使用する場合のベスト プラクティスについては、[ベスト プラクティス](../Concepts/best-practices.md#active-learning)に関するページを参照してください。

## <a name="next-steps"></a>次の手順
 
> [!div class="nextstepaction"]
> [メタデータと GenerateAnswer API の使用](metadata-generateanswer-usage.md)
