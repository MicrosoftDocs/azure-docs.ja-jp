---
title: メタデータと GenerateAnswer API - QnA Maker
titleSuffix: Azure Cognitive Services
description: QnA Maker では、キー/値のペアの形式で、メタデータを質問/回答のセットに追加することができます。 ユーザー クエリの結果をフィルター処理し、フォローアップ会話で使用できる追加情報を格納できます。
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: article
ms.date: 06/27/2019
ms.author: diberry
ms.openlocfilehash: 6bfcb531d0e4e8073a5553f7bc84a25e4f8a92a9
ms.sourcegitcommit: 1572b615c8f863be4986c23ea2ff7642b02bc605
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/10/2019
ms.locfileid: "67785677"
---
# <a name="get-an-answer-with-the-generateanswer-api-and-metadata"></a>GenerateAnswer API およびメタデータを使って回答を取得する

ユーザーの質問に対して予測される回答を取得するには、GenerateAnswer API を使用します。 ナレッジ ベースを公開するときに、 **[公開]** ページにこの API を使用する方法に関する情報が表示されます。 また、メタデータ タグに基づいて回答をフィルター処理するように API を構成し、テスト クエリ文字列パラメーターを使用してエンドポイントからナレッジ ベースをテストすることも可能です。

QnA Maker では、キーと値のペアの形式で、メタデータを質問と回答のセットに追加することができます。 この情報を使用して、ユーザー クエリの結果をフィルター処理し、フォローアップ会話で使用できる追加情報を格納できます。 詳細については、「[Knowledge base](../Concepts/knowledge-base.md)」 (ナレッジ ベース) を参照してください。

<a name="qna-entity"></a>

## <a name="store-questions-and-answers-with-a-qna-entity"></a>QnA エンティティを使用して質問と回答を保存する

まず、QnA Maker での質問と回答のデータの格納方法を理解することが重要です。 QnA エンティティを次の図に示します。

![QnA エンティティのイラスト](../media/qnamaker-how-to-metadata-usage/qna-entity.png)

QnA エンティティにはそれぞれ一意の永続 ID があります。 ID を使用して、特定の QnA エンティティを更新することができます。

<a name="generateanswer-api"></a>

## <a name="get-answer-predictions-with-the-generateanswer-api"></a>GenerateAnswer API を使用して回答の予測を取得する

ボットやアプリケーションで [GenerateAnswer API](https://docs.microsoft.com/rest/api/cognitiveservices/qnamakerruntime/runtime/generateanswer) を使用して、ユーザーの質問についてナレッジ ベースのクエリを実行し、質問と回答のセットから最も一致するものを取得します。

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

HTTP POST 要求で GenerateAnswer を呼び出します。 GenerateAnswer を呼び出す方法を示すサンプル コードについては、[クイック スタート](../quickstarts/csharp.md)を参照してください。 

POST 要求では以下を使用します。

* 必須の [URI パラメーター](https://docs.microsoft.com/rest/api/cognitiveservices/qnamakerruntime/runtime/train#uri-parameters)
* セキュリティを確保するために必須の[ヘッダー プロパティ](https://docs.microsoft.com/azure/cognitive-services/qnamaker/quickstarts/get-answer-from-knowledge-base-nodejs#add-a-post-request-to-send-question-and-get-an-answer) (`Authorization`)
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
    "scoreThreshold": 20,
    "strictFilters": [
    {
        "name": "category",
        "value": "api"
    }],
    "userId": "sd53lsY="
}
```

<a name="generateanswer-response"></a>

## <a name="generateanswer-response-properties"></a>GenerateAnswer の応答プロパティ

[応答](https://docs.microsoft.com/rest/api/cognitiveservices/qnamakerruntime/runtime/generateanswer#successful-query)は、回答と会話の次のターン (使用可能な場合) を表示するために必要なすべての情報を含む JSON オブジェクトです。

```json
{
    "answers": [
        {
            "score": 28.54820341616869,
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

## <a name="use-qna-maker-with-a-bot-in-c"></a>C# のボットで QnA Maker を使用する

Bot Framework では、QnA Maker のプロパティへのアクセスを提供します。

```csharp
using Microsoft.Bot.Builder.AI.QnA;
var metadata = new Microsoft.Bot.Builder.AI.QnA.Metadata();
var qnaOptions = new QnAMakerOptions();

qnaOptions.Top = Constants.DefaultTop;
qnaOptions.ScoreThreshold = 0.3F;
var response = await _services.QnAServices[QnAMakerKey].GetAnswersAsync(turnContext, qnaOptions);
```

サポート ボットに、このコードを使用した[サンプル](https://github.com/microsoft/BotBuilder-Samples/blob/master/experimental/qnamaker-support/csharp_dotnetcore/Service/SupportBotService.cs#L418)があります。

## <a name="use-qna-maker-with-a-bot-in-nodejs"></a>Node.js のボットで QnA Maker を使用する

Bot Framework では、QnA Maker のプロパティへのアクセスを提供します。

```javascript
const { QnAMaker } = require('botbuilder-ai');
this.qnaMaker = new QnAMaker(endpoint);

// Default QnAMakerOptions
var qnaMakerOptions = {
    ScoreThreshold: 0.03,
    Top: 3
};
var qnaResults = await this.qnaMaker.getAnswers(stepContext.context, qnaMakerOptions);
```

サポート ボットに、このコードを使用した[サンプル](https://github.com/microsoft/BotBuilder-Samples/blob/master/experimental/qnamaker-activelearning/javascript_nodejs/Helpers/dialogHelper.js#L36)があります。

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
    "strictFilters": [
      {
        "name": "restaurant",
        "value": "paradise"
      }]
}
```

<a name="keep-context"></a>

## <a name="use-question-and-answer-results-to-keep-conversation-context"></a>質問と回答の結果を使用して会話のコンテキストを維持する

GenerateAnswer への応答には、一致した質問と回答のセットの対応するメタデータ情報が含まれます。 クライアント アプリケーション内でこの情報を使って、以降の会話で使用するために以前の会話のコンテキストを格納することができます。 

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

## <a name="next-steps"></a>次の手順

**[発行]** ページでは、[Postman](../Quickstarts/get-answer-from-kb-using-postman.md) と [cURL](../Quickstarts/get-answer-from-kb-using-curl.md) を使用して回答を生成するための情報も指定できます。 

> [!div class="nextstepaction"]
> [ナレッジ ベースの作成](./create-knowledge-base.md)
