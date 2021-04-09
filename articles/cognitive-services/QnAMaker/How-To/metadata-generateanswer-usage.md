---
title: メタデータと GenerateAnswer API - QnA Maker
titleSuffix: Azure Cognitive Services
description: QnA Maker では、キーと値のペアの形式で、メタデータを質問と回答のペアに追加することができます。 ユーザー クエリの結果をフィルター処理し、フォローアップ会話で使用できる追加情報を格納できます。
services: cognitive-services
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 11/09/2020
ms.custom: devx-track-js, devx-track-csharp
ms.openlocfilehash: 9d2100dbc2c5f24742a949778a1b7450bf303c5f
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "103232207"
---
# <a name="get-an-answer-with-the-generateanswer-api"></a>GenerateAnswer API を使って回答を取得する

ユーザーの質問に対して予測される回答を取得するには、GenerateAnswer API を使用します。 ナレッジ ベースを公開するときに、 **[公開]** ページにこの API を使用する方法に関する情報が表示されます。 また、メタデータ タグに基づいて回答をフィルター処理するように API を構成し、テスト クエリ文字列パラメーターを使用してエンドポイントからナレッジ ベースをテストすることも可能です。

<a name="generateanswer-api"></a>

## <a name="get-answer-predictions-with-the-generateanswer-api"></a>GenerateAnswer API を使用して回答の予測を取得する

ボットやアプリケーションで [GenerateAnswer API](/rest/api/cognitiveservices/qnamakerruntime/runtime/generateanswer) を使用して、ユーザーの質問についてナレッジ ベースのクエリを実行し、質問と回答のペアから最も一致するものを取得します。

<a name="generateanswer-endpoint"></a>

## <a name="publish-to-get-generateanswer-endpoint"></a>公開して GenerateAnswer エンドポイントを取得する

ナレッジ ベースを公開したら、[QnA Maker ポータル](https://www.qnamaker.ai)から、または [API](/rest/api/cognitiveservices/qnamaker/knowledgebase/publish) を使用して、GenerateAnswer エンドポイントの詳細を取得することができます。

エンドポイントの詳細を取得するには、次のようにします。
1. [https://www.qnamaker.ai](https://www.qnamaker.ai) にサインインします。
1. **[My knowledge bases]** (マイ ナレッジ ベース) で、ご利用のナレッジ ベースの **[コードの表示]** をクリックします。
    ![ナレッジ ベースのスクリーンショット](../media/qnamaker-how-to-metadata-usage/my-knowledge-bases.png)
1. GenerateAnswer エンドポイントの詳細を取得します。

    # <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (安定版リリース)](#tab/v1)

    ![エンドポイントの詳細のスクリーンショット](../media/qnamaker-how-to-metadata-usage/view-code.png)

    # <a name="qna-maker-managed-preview-release"></a>[QnA Maker マネージド (プレビュー リリース)](#tab/v2)

    ![管理対象のエンドポイントの詳細のスクリーンショット](../media/qnamaker-how-to-metadata-usage/view-code-managed.png)

    ---

エンドポイントの詳細は、ナレッジ ベースの **[設定]** タブから取得することもできます。

<a name="generateanswer-request"></a>

## <a name="generateanswer-request-configuration"></a>GenerateAnswer 要求の構成

HTTP POST 要求で GenerateAnswer を呼び出します。 GenerateAnswer を呼び出す方法を示すサンプル コードについては、[クイック スタート](../quickstarts/quickstart-sdk.md#generate-an-answer-from-the-knowledge-base)を参照してください。

POST 要求では以下を使用します。

* 必須の [URI パラメーター](/rest/api/cognitiveservices/qnamakerruntime/runtime/train#uri-parameters)
* セキュリティを確保するために必須のヘッダー プロパティ (`Authorization`)
* 必須の[本文プロパティ](/rest/api/cognitiveservices/qnamakerruntime/runtime/train#feedbackrecorddto)

GenerateAnswer の URL は次の形式になります。

```
https://{QnA-Maker-endpoint}/knowledgebases/{knowledge-base-ID}/generateAnswer
```

文字列 `EndpointKey` の値と末尾のスペースを含めて、HTTP ヘッダー プロパティ `Authorization` を必ず設定してください。エンドポイント キーは、 **[設定]** ページで確認できます。

JSON 本文の例は、次のようになります。

```json
{
    "question": "qna maker and luis",
    "top": 6,
    "isTest": true,
    "scoreThreshold": 30,
    "rankerType": "" // values: QuestionOnly
    "strictFilters": [
    {
        "name": "category",
        "value": "api"
    }],
    "userId": "sd53lsY="
}
```

[rankerType](../concepts/best-practices.md#choosing-ranker-type) の詳細について確認してください。

以前の JSON では、30% のスコアまたはしきい値のスコア以上の回答のみが要求されました。

<a name="generateanswer-response"></a>

## <a name="generateanswer-response-properties"></a>GenerateAnswer の応答プロパティ

[応答](/rest/api/cognitiveservices/qnamakerruntime/runtime/generateanswer#successful-query)は、回答と会話の次のターン (使用可能な場合) を表示するために必要なすべての情報を含む JSON オブジェクトです。

```json
{
    "answers": [
        {
            "score": 38.54820341616869,
            "Id": 20,
            "answer": "There is no direct integration of LUIS with QnA Maker. But, in your bot code, you can use LUIS and QnA Maker together. [View a sample bot](https://github.com/Microsoft/BotBuilder-CognitiveServices/tree/master/Node/samples/QnAMaker/QnAWithLUIS)",
            "source": "Custom Editorial",
            "questions": [
                "How can I integrate LUIS with QnA Maker?"
            ],
            "metadata": [
                {
                    "name": "category",
                    "value": "api"
                }
            ]
        }
    ]
}
```

以前の JSON では、スコアが 38.5% の回答で応答しました。

## <a name="match-questions-only-by-text"></a>テキストによる質問のみの一致

既定では、QnA Maker は質問と回答を検索します。 質問のみを検索して、回答を生成する場合、GenerateAnswer 要求の POST 本文で `RankerType=QuestionOnly` を使用します。

`isTest=false` を使用して公開済みの KB を検索することも、`isTest=true` を使用してテスト KB を検索することもできます。

```json
{
  "question": "Hi",
  "top": 30,
  "isTest": true,
  "RankerType":"QuestionOnly"
}

```
## <a name="use-qna-maker-with-a-bot-in-c"></a>C# のボットで QnA Maker を使用する

Bot Framework では、[getAnswer API](/dotnet/api/microsoft.bot.builder.ai.qna.qnamaker.getanswersasync#Microsoft_Bot_Builder_AI_QnA_QnAMaker_GetAnswersAsync_Microsoft_Bot_Builder_ITurnContext_Microsoft_Bot_Builder_AI_QnA_QnAMakerOptions_System_Collections_Generic_Dictionary_System_String_System_String__System_Collections_Generic_Dictionary_System_String_System_Double__) を使用して QnA Maker のプロパティへのアクセスを提供します。

```csharp
using Microsoft.Bot.Builder.AI.QnA;
var metadata = new Microsoft.Bot.Builder.AI.QnA.Metadata();
var qnaOptions = new QnAMakerOptions();

metadata.Name = Constants.MetadataName.Intent;
metadata.Value = topIntent;
qnaOptions.StrictFilters = new Microsoft.Bot.Builder.AI.QnA.Metadata[] { metadata };
qnaOptions.Top = Constants.DefaultTop;
qnaOptions.ScoreThreshold = 0.3F;
var response = await _services.QnAServices[QnAMakerKey].GetAnswersAsync(turnContext, qnaOptions);
```

以前の JSON では、30% のスコアまたはしきい値のスコア以上の回答のみが要求されました。

## <a name="use-qna-maker-with-a-bot-in-nodejs"></a>Node.js のボットで QnA Maker を使用する

Bot Framework では、[getAnswer API](/javascript/api/botbuilder-ai/qnamaker?preserve-view=true&view=botbuilder-ts-latest#generateanswer-string---undefined--number--number-) を使用して QnA Maker のプロパティへのアクセスを提供します。

```javascript
const { QnAMaker } = require('botbuilder-ai');
this.qnaMaker = new QnAMaker(endpoint);

// Default QnAMakerOptions
var qnaMakerOptions = {
    ScoreThreshold: 0.30,
    Top: 3
};
var qnaResults = await this.qnaMaker.getAnswers(stepContext.context, qnaMakerOptions);
```

以前の JSON では、30% のスコアまたはしきい値のスコア以上の回答のみが要求されました。

## <a name="get-precise-answers-with-generateanswer-api"></a>GenerateAnswer API を使用して正確な回答を得る

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (安定版リリース)](#tab/v1)

正確な回答機能は、QnA Maker マネージド バージョンでのみ提供しています。

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker マネージド (プレビュー リリース)](#tab/v2)

ユーザーは QnA Maker 管理対象リソースを使用するときに、[正確な回答](../reference-precise-answering.md)を有効にすることができます。 同じ目的で answerSpanRequest パラメーターを更新する必要があります。

```json
{
    "question": "How long it takes to charge surface pro 4?",
    "top": 3,
    "answerSpanRequest": {
        "enable": true,
        "topAnswersWithSpan": 1
    }
}
```

同様に、ユーザーは正確な回答を無効にすることを選択できます。そのためには、answerSpanRequest パラメーターを設定しないようにします。

```json
{
    "question": "How long it takes to charge surface pro 4?",
    "top": 3
}
```
### <a name="bot-settings"></a>ボットの設定

ボット サービスに対して正確な回答の設定を構成する場合は、ボットの App Service リソースに移動します。 その後に、次の設定を追加して、構成を更新することが必要です。

- EnablePreciseAnswer
- DisplayPreciseAnswerOnly

|ディスプレイの構成|EnablePreciseAnswer|DisplayPreciseAnswerOnly|
|:--|--|--|
|正確な回答のみ|true|true|
|長い回答のみ|false|false|
|長い回答と正確な回答の両方|true|false|

---

## <a name="common-http-errors"></a>一般的な HTTP エラー

|コード|説明|
|:--|--|
|2xx|Success|
|400|"要求のパラメーターが正しくない" とは、必須パラメーターが見つからない、形式が正しくない、または大きすぎることを意味します|
|400|"要求の本文が正しくない" とは、JSON が見つからない、形式が正しくない、または大きすぎることを意味します|
|401|無効なキー|
|403|禁止されています - アカウントに適切なアクセス許可がありません|
|404|KB が存在しません|
|410|この API は非推奨であり、現在は利用できません|

## <a name="next-steps"></a>次のステップ

**[発行]** ページでは、Postman または cURL を使用して [回答を生成する](../Quickstarts/get-answer-from-knowledge-base-using-url-tool.md)ための情報も提供されます。

> [!div class="nextstepaction"]
> [ナレッジ ベースに関する分析結果の取得](../how-to/get-analytics-knowledge-base.md)
> [!div class="nextstepaction"]
> [信頼度スコア](../Concepts/confidence-score.md)
