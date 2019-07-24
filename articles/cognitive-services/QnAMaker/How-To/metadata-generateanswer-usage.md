---
title: メタデータと GenerateAnswer API - QnA Maker
titleSuffix: Azure Cognitive Services
description: QnA Maker では、キー/値のペアの形式で、メタデータを質問/回答のセットに追加することができます。 この情報を使用して、ユーザー クエリの結果をフィルター処理し、フォローアップ会話で使用できる追加情報を格納できます。
services: cognitive-services
author: tulasim88
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: article
ms.date: 02/21/2019
ms.author: tulasim
ms.openlocfilehash: d14e2897183a97da5e84a76b699def529f1d167e
ms.sourcegitcommit: 48a41b4b0bb89a8579fc35aa805cea22e2b9922c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/15/2019
ms.locfileid: "59579412"
---
# <a name="get-a-knowledge-answer-with-the-generateanswer-api-and-metadata"></a>GenerateAnswer API およびメタデータを使ってナレッジの回答を取得する

ユーザーの質問に対して予測される回答を取得するには、GenerateAnswer API を使用します。 ナレッジ ベースを公開するときに、この API を使用するために、ここに示した情報が公開ページ上に表示されます。 また、メタデータ タグに基づいて回答をフィルター処理するように API を構成し、テスト クエリ文字列パラメーターを使用してエンドポイントからナレッジ ベースをテストすることも可能です。

QnA Maker では、キーと値のペアの形式で、メタデータを質問/回答のセットに追加することができます。 この情報を使用して、ユーザー クエリの結果をフィルター処理し、フォローアップ会話で使用できる追加情報を格納できます。 詳細については、「[Knowledge base](../Concepts/knowledge-base.md)」 (ナレッジ ベース) を参照してください。

<a name="qna-entity"></a>

## <a name="storing-questions-and-answers-with-a-qna-entity"></a>QnA エンティティを使用して質問と回答を保存する

まず、QnA Maker での質問/回答データの格納方法を理解することが重要です。 QnA エンティティを次の図に示します。

![QnA エンティティ](../media/qnamaker-how-to-metadata-usage/qna-entity.png)

QnA エンティティにはそれぞれ一意の永続 ID があります。 ID を使用して、特定の QnA エンティティを更新することができます。

<a name="generateanswer-api"></a>

## <a name="get-answer-predictions-with-the-generateanswer-api"></a>GenerateAnswer API を使用して回答の予測を取得する

ボットやアプリケーションで GenerateAnswer API を使用して、ユーザーの質問についてナレッジ ベースのクエリを実行し、質問/回答のセットから最も一致するものを取得します。

<a name="generateanswer-endpoint"></a>

## <a name="publish-to-get-generateanswer-endpoint"></a>公開して GenerateAnswer エンドポイントを取得する 

ナレッジ ベースを公開したら、[QnA Maker ポータル](https://www.qnamaker.ai)から、または [API](https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/5ac266295b4ccd1554da75ff) を使用して、GenerateAnswer エンドポイントの詳細を取得することができます。

エンドポイントの詳細を取得するには、次のようにします。
1. [https://www.qnamaker.ai](https://www.qnamaker.ai) にログインします。
1. **[My knowledge bases]\(マイ ナレッジ ベース\)** で、ご利用のナレッジ ベースの **[コードの表示]** をクリックします。
    ![マイ ナレッジ ベース](../media/qnamaker-how-to-metadata-usage/my-knowledge-bases.png)
1. GenerateAnswer エンドポイントの詳細を取得します。

    ![エンドポイントの詳細](../media/qnamaker-how-to-metadata-usage/view-code.png)

エンドポイントの詳細は、ナレッジ ベースの **[設定]** タブから取得することもできます。

<a name="generateanswer-request"></a>

## <a name="generateanswer-request-configuration"></a>GenerateAnswer 要求の構成

HTTP POST 要求で GenerateAnswer を呼び出します。 GenerateAnswer を呼び出す方法を示すサンプル コードについては、[クイック スタート](../quickstarts/csharp.md)を参照してください。

**要求 URL** は、次のような形式になります。 

```
https://{QnA-Maker-endpoint}/knowledgebases/{knowledge-base-ID}/generateAnswer?isTest=true
```

|HTTP 要求プロパティ|Name|Type|目的|
|--|--|--|--|
|URL ルート パラメーター|ナレッジ ベース ID|string|ナレッジ ベースの GUID。|
|URL ルート パラメーター|QnAMaker エンドポイントのホスト|string|Azure サブスクリプションにデプロイされているエンドポイントのホスト名。 これは、ナレッジ ベースを公開した後に、[設定] ページで利用できます。 |
|ヘッダー|Content-Type|string|API に送信される本文のメディアの種類。 既定値: `` |
|ヘッダー|Authorization|string|エンドポイント キー (EndpointKey xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx)。|
|Post 本文|JSON オブジェクト|JSON|設定が付与されている質問|


JSON の本文には、次のようないくつかの設定があります。

|JSON 本文のプロパティ|必須|Type|目的|
|--|--|--|--|
|`question`|必須|string|ナレッジ ベースに送信されるユーザーの質問。|
|`top`|省略可能|integer|出力を含めるランク付けされた結果の数。 既定値は 1 です。|
|`userId`|省略可能|string|ユーザーを識別する一意の ID。 この ID はチャット ログに記録されます。|
|`isTest`|省略可能|ブール値|true に設定した場合、公開されたインデックスではなく、`testkb` 検索インデックスから結果が返されます。|
|`strictFilters`|省略可能|string|指定した場合、指定されたメタデータを含む回答のみを返すように QnA Maker に指示します。|

JSON 本文の例は、次のようになります。

```json
{
    "question": "qna maker and luis",
    "top": 6,
    "isTest": true,
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

成功した応答は、ステータス 200 と JSON 応答を返します。 

|回答のプロパティ (スコア順)|目的|
|--|--|
|スコア|0 から 100 のランキング スコア。|
|Id|回答に割り当てられた一意の ID。|
|questions|ユーザーによって提供された質問。|
|応答して|質問への答え。|
|source|ナレッジ ベースで抽出または保存された回答のソースの名前。|
|metadata|回答に関連付けられたメタデータ。|
|metadata.name|メタデータの名前。 (文字列。最大長: 100。必須)|
|metadata.value:メタデータの値。 (文字列。最大長: 100。必須)|


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

<a name="metadata-example"></a>

## <a name="using-metadata-allows-you-to-filter-answers-by-custom-metadata-tags"></a>メタデータを使用してカスタム メタデータ タグによる回答のフィルター処理を行う

メタデータを追加すると、これらのメタデータ タグによって回答をフィルター処理できます。 次の FAQ データがあるとします。 メタデータ アイコンをクリックして、メタデータをナレッジ ベースに追加します。

![メタデータの追加](../media/qnamaker-how-to-metadata-usage/add-metadata.png)

<a name="filter-results-with-strictfilters-for-metadata-tags"></a>

## <a name="filter-results-with-strictfilters-for-metadata-tags"></a>メタデータ タグの strictFilters を使用して結果をフィルター処理する

ユーザーの質問が "When does this hotel close?" (このホテルはいつ閉まりますか?) であるとします。 これには、"Paradise" というレストランがいつ閉まるかという意味が含まれます。

"Paradise" レストランに関する結果のみが必要であるため、次のように、メタデータ "Restaurant Name" に対する GenerateAnswer 呼び出しでフィルターを設定することができます。

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

<name="keep-context"></a>

## <a name="use-question-and-answer-results-to-keep-conversation-context"></a>質問と回答の結果を使用して会話のコンテキストを維持する

GenerateAnswer への応答には、一致した質問/回答セットの対応するメタデータ情報が含まれます。 クライアント アプリケーション内でこの情報を使って、以降の会話で使用するために以前の会話のコンテキストを格納することができます。 

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

## <a name="next-steps"></a>次の手順

[発行] ページでは、[Postman](../Quickstarts/get-answer-from-kb-using-postman.md) と [cURL](../Quickstarts/get-answer-from-kb-using-curl.md) を使用して回答を生成するための情報も指定できます。 

> [!div class="nextstepaction"]
> [ナレッジ ベースの作成](./create-knowledge-base.md)
