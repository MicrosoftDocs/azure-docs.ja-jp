---
title: メタデータと GenerateAnswer API - QnA Maker
titleSuffix: Azure Cognitive Services
description: QnA Maker では、キーと値のペアの形式で、メタデータを質問と回答のペアに追加することができます。 ユーザー クエリの結果をフィルター処理し、フォローアップ会話で使用できる追加情報を格納できます。
services: cognitive-services
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 07/16/2020
ms.custom: devx-track-javascript, devx-track-csharp
ms.openlocfilehash: d1258786ec6f611bea5f73f3cb1c176738733acd
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/26/2020
ms.locfileid: "88919065"
---
# <a name="get-an-answer-with-the-generateanswer-api-and-metadata"></a>GenerateAnswer API およびメタデータを使って回答を取得する

ユーザーの質問に対して予測される回答を取得するには、GenerateAnswer API を使用します。 ナレッジ ベースを公開するときに、 **[公開]** ページにこの API を使用する方法に関する情報が表示されます。 また、メタデータ タグに基づいて回答をフィルター処理するように API を構成し、テスト クエリ文字列パラメーターを使用してエンドポイントからナレッジ ベースをテストすることも可能です。

QnA Maker では、キーと値のペアの形式で、メタデータを質問と回答のペアに追加することができます。 この情報を使用して、ユーザー クエリの結果をフィルター処理し、フォローアップ会話で使用できる追加情報を格納できます。 詳細については、「[Knowledge base](../Concepts/knowledge-base.md)」 (ナレッジ ベース) を参照してください。

<a name="qna-entity"></a>

## <a name="store-questions-and-answers-with-a-qna-entity"></a>QnA エンティティを使用して質問と回答を保存する

まず、QnA Maker での質問と回答のデータの格納方法を理解することが重要です。 QnA エンティティを次の図に示します。

![QnA エンティティのイラスト](../media/qnamaker-how-to-metadata-usage/qna-entity.png)

QnA エンティティにはそれぞれ一意の永続 ID があります。 ID を使用して、特定の QnA エンティティを更新することができます。

<a name="generateanswer-api"></a>

## <a name="get-answer-predictions-with-the-generateanswer-api"></a>GenerateAnswer API を使用して回答の予測を取得する

ボットやアプリケーションで [GenerateAnswer API](https://docs.microsoft.com/rest/api/cognitiveservices/qnamakerruntime/runtime/generateanswer) を使用して、ユーザーの質問についてナレッジ ベースのクエリを実行し、質問と回答のペアから最も一致するものを取得します。

<a name="generateanswer-endpoint"></a>

## <a name="publish-to-get-generateanswer-endpoint"></a>公開して GenerateAnswer エンドポイントを取得する

ナレッジ ベースを公開したら、[QnA Maker ポータル](https://www.qnamaker.ai)から、または [API](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/publish) を使用して、GenerateAnswer エンドポイントの詳細を取得することができます。

エンドポイントの詳細を取得するには、次のようにします。
1. [https://www.qnamaker.ai](https://www.qnamaker.ai) にサインインします。
1. **[My knowledge bases]** (マイ ナレッジ ベース) で、ご利用のナレッジ ベースの **[コードの表示]** をクリックします。
    ![ナレッジ ベースのスクリーンショット](../media/qnamaker-how-to-metadata-usage/my-knowledge-bases.png)
1. GenerateAnswer エンドポイントの詳細を取得します。

    ![エンドポイントの詳細のスクリーンショット](../media/qnamaker-how-to-metadata-usage/view-code.png)

エンドポイントの詳細は、ナレッジ ベースの **[設定]** タブから取得することもできます。

<a name="generateanswer-request"></a>

## <a name="generateanswer-request-configuration"></a>GenerateAnswer 要求の構成

HTTP POST 要求で GenerateAnswer を呼び出します。 GenerateAnswer を呼び出す方法を示すサンプル コードについては、[クイック スタート](../quickstarts/quickstart-sdk.md#generate-an-answer-from-the-knowledge-base)を参照してください。

POST 要求では以下を使用します。

* 必須の [URI パラメーター](https://docs.microsoft.com/rest/api/cognitiveservices/qnamakerruntime/runtime/train#uri-parameters)
* セキュリティを確保するために必須のヘッダー プロパティ (`Authorization`)
* 必須の[本文プロパティ](https://docs.microsoft.com/rest/api/cognitiveservices/qnamakerruntime/runtime/train#feedbackrecorddto)

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

[応答](https://docs.microsoft.com/rest/api/cognitiveservices/qnamakerruntime/runtime/generateanswer#successful-query)は、回答と会話の次のターン (使用可能な場合) を表示するために必要なすべての情報を含む JSON オブジェクトです。

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

## <a name="use-qna-maker-with-a-bot-in-c"></a>C# のボットで QnA Maker を使用する

Bot Framework では、[getAnswer API](https://docs.microsoft.com/dotnet/api/microsoft.bot.builder.ai.qna.qnamaker.getanswersasync?view=botbuilder-dotnet-stable#Microsoft_Bot_Builder_AI_QnA_QnAMaker_GetAnswersAsync_Microsoft_Bot_Builder_ITurnContext_Microsoft_Bot_Builder_AI_QnA_QnAMakerOptions_System_Collections_Generic_Dictionary_System_String_System_String__System_Collections_Generic_Dictionary_System_String_System_Double__) を使用して QnA Maker のプロパティへのアクセスを提供します。

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

Bot Framework では、[getAnswer API](https://docs.microsoft.com/javascript/api/botbuilder-ai/qnamaker?view=botbuilder-ts-latest#generateanswer-string---undefined--number--number-) を使用して QnA Maker のプロパティへのアクセスを提供します。

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

<a name="metadata-example"></a>

## <a name="use-metadata-to-filter-answers-by-custom-metadata-tags"></a>メタデータを使用してカスタム メタデータ タグによる回答のフィルター処理を行う

メタデータを追加すると、これらのメタデータ タグによって回答をフィルター処理できます。 **[表示のオプション]** メニューからメタデータ列を追加します。 メタデータ **+** アイコンを選択してメタデータ ペアを追加して、メタデータをナレッジ ベースに追加します。 このペアは、1 つのキーと 1 つの値で構成されます。

![メタデータの追加のスクリーンショット](../media/qnamaker-how-to-metadata-usage/add-metadata.png)

<a name="filter-results-with-strictfilters-for-metadata-tags"></a>

## <a name="filter-results-with-strictfilters-for-metadata-tags"></a>メタデータ タグの strictFilters を使用して結果をフィルター処理する

ユーザーの質問が "When does this hotel close?" (このホテルはいつ閉まりますか?) であるとします。これには、"Paradise" というレストランがいつ閉まるかという意味が含まれます。

"Paradise" レストランに関する結果のみが必要であるため、メタデータ "Restaurant Name" に対する GenerateAnswer 呼び出しでフィルターを設定することができます。 次の例はこのことを示します。

```json
{
    "question": "When does this hotel close?",
    "top": 1,
    "strictFilters": [ { "name": "restaurant", "value": "paradise"}]
}
```

### <a name="logical-and-by-default"></a>既定での論理 AND

クエリで複数のメタデータ フィルターを組み合わせるには、`strictFilters` プロパティの配列に追加のメタデータ フィルターを付け加えます。 既定で、値が論理的に組み合わせられます (AND)。 論理的に組み合わせられる場合に、回答内で該当のペアが返されるためには、すべてのフィルターが QnA のペアと一致する必要があります。

これは、`AND` の値を指定して `strictFiltersCompoundOperationType` プロパティを使用することと同じです。

### <a name="logical-or-using-strictfilterscompoundoperationtype-property"></a>strictFiltersCompoundOperationType プロパティを使用する論理 OR

複数のメタデータ フィルターを組み合わせるときに、1 つまたは一部のフィルター一致のみに関心がある場合は、`OR` の値を指定して `strictFiltersCompoundOperationType` プロパティを使用します。

これにより、いずれかのフィルターが一致した場合にナレッジ ベースによって回答が返されるようになりますが、メタデータが含まれていない回答は返されません。

```json
{
    "question": "When do facilities in this hotel close?",
    "top": 1,
    "strictFilters": [
      { "name": "type","value": "restaurant"},
      { "name": "type", "value": "bar"},
      { "name": "type", "value": "poolbar"}
    ],
    "strictFiltersCompoundOperationType": "OR"
}
```

### <a name="metadata-examples-in-quickstarts"></a>クイックスタートのメタデータの例

メタデータに関する以下の QnA Maker ポータルのクイックスタートで、メタデータの詳細について確認します。
* [作成 - QnA ペアにメタデータを追加する](../quickstarts/add-question-metadata-portal.md#add-metadata-to-filter-the-answers)
* [クエリ予測 - メタデータによる回答のフィルター処理を行う](../quickstarts/get-answer-from-knowledge-base-using-url-tool.md)

<a name="keep-context"></a>

## <a name="use-question-and-answer-results-to-keep-conversation-context"></a>質問と回答の結果を使用して会話のコンテキストを維持する

GenerateAnswer への応答には、一致した質問と回答のペアの対応するメタデータ情報が含まれます。 クライアント アプリケーション内でこの情報を使って、以降の会話で使用するために以前の会話のコンテキストを格納することができます。

```json
{
    "answers": [
        {
            "questions": [
                "What is the closing time?"
            ],
            "answer": "10.30 PM",
            "score": 100,
            "id": 1,
            "source": "Editorial",
            "metadata": [
                {
                    "name": "restaurant",
                    "value": "paradise"
                },
                {
                    "name": "location",
                    "value": "secunderabad"
                }
            ]
        }
    ]
}
```

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

**[発行]** ページでは、Postman または cURL を使用して[回答を生成する](../Quickstarts/get-answer-from-knowledge-base-using-url-tool.md)ための情報も提供されます。

> [!div class="nextstepaction"]
> [ナレッジ ベースに関する分析結果の取得](../how-to/get-analytics-knowledge-base.md)
