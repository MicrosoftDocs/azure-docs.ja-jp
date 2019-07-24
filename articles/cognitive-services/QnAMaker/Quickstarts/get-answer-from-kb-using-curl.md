---
title: クイック スタート:cURL を使用してナレッジ ベースから回答を取得する - QnA Maker
titleSuffix: Azure Cognitive Services
description: このクイック スタートでは、cURL を使用して、ナレッジ ベースから回答を取得する手順を紹介しています。
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: article
ms.date: 04/05/2019
ms.author: diberry
ms.openlocfilehash: ce2d39f3941030059cef329aa60e804a44d73a98
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/08/2019
ms.locfileid: "59263096"
---
# <a name="quickstart-get-answer-from-knowledge-base-using-curl"></a>クイック スタート:cURL を使用してナレッジ ベースから回答を取得する

この cURL ベースのクイック スタートでは、ナレッジ ベースから回答を取得する手順を紹介しています。

## <a name="prerequisites"></a>前提条件

* 最新の [**cURL**](https://curl.haxx.se/)。
* [QnA Maker サービス](../How-To/set-up-qnamaker-service-azure.md)と、[質問と回答が含まれるナレッジ ベース](../Tutorials/create-publish-query-in-portal.md)が必要です。

## <a name="publish-to-get-endpoint"></a>発行してエンドポイントを取得する

ナレッジ ベースからの質問に対する回答を生成する準備ができたら、ナレッジ ベースを[発行](../Quickstarts/create-publish-knowledge-base.md#publish-the-knowledge-base)します。

## <a name="use-production-endpoint-with-curl"></a>cURL を使って運用エンドポイントを使用する

ナレッジ ベースが発行されると、**[発行]** ページに、回答を生成するための HTTP 要求の設定が表示されます。 **[CURL]** タブに、[CURL](https://www.getpostman.com) コマンドライン ツールからの回答の生成に必要な設定が表示されます。

[![P結果を発行する](../media/qnamaker-use-to-generate-answer/curl-command-on-publish-page.png)](../media/qnamaker-use-to-generate-answer/curl-command-on-publish-page.png#lightbox)

CURL を使って回答を生成するには、次の手順を実行します。

1. [CURL] タブのテキストをコピーします。 
1. コマンドラインまたはターミナルを開き、テキストを貼り付けます。
1. ナレッジ ベースに関係するように質問を編集します。 質問を囲んでいる包含 JSON を削除しないように注意してください。
1. コマンドを入力します。 
1. 応答には、回答に関する関連情報が含まれます。 

    ```bash
    > curl -X POST https://qnamaker-f0.azurewebsites.net/qnamaker/knowledgebases/1111f8c-d01b-4698-a2de-85b0dbf3358c/generateAnswer -H "Authorization: EndpointKey 111841fb-c208-4a72-9412-03b6f3e55ca1" -H "Content-type: application/json" -d "{'question':'How do I programmatically update my Knowledge Base?'}"
    {
      "answers": [
        {
          "questions": [
            "How do I programmatically update my Knowledge Base?"
          ],
          "answer": "You can use our REST APIs to manage your Knowledge Base. See here for details: https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/5ac266295b4ccd1554da7600",
          "score": 100.0,
          "id": 18,
          "source": "Custom Editorial",
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

## <a name="use-staging-endpoint-with-curl"></a>cURL を使ってステージング エンドポイントを使用する

ステージングのエンドポイントから回答を取得するには、値 `true` を持つクエリ文字列ブール型パラメーター `isTest` を使用します。

`isTest=true`

## <a name="next-steps"></a>次の手順

[発行] ページでは、Postman を使用して[回答を生成](get-answer-from-kb-using-postman.md)するための情報も指定できます。 

> [!div class="nextstepaction"]
> [回答の生成中にメタデータを使用する](../How-to/metadata-generateanswer-usage.md)
