---
title: ナレッジ ベース - QnA Maker
titleSuffix: Azure Cognitive Services
description: QnA Maker のナレッジ ベースは、一連の質問と回答 (QnA) のペアと、各 QnA ペアに関連付けられている省略可能なメタデータで構成されます。
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 08/26/2019
ms.author: diberry
ms.custom: seodec18
ms.openlocfilehash: 78fc9fe34eb3463021dae69990fe1d30668d453f
ms.sourcegitcommit: 29880cf2e4ba9e441f7334c67c7e6a994df21cfe
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/26/2019
ms.locfileid: "71300505"
---
# <a name="what-is-a-qna-maker-knowledge-base"></a>QnA Maker ナレッジ ベースとは

QnA Maker のナレッジ ベースは、一連の質問と回答 (QnA) のペアと、各 QnA ペアに関連付けられている省略可能なメタデータで構成されます。

## <a name="key-knowledge-base-concepts"></a>ナレッジ ベースの重要な概念

* **質問**: 質問には、ユーザーのクエリを最も適切に表すテキストが含まれます。 
* **回答**: 回答は、ユーザーのクエリが関連付けられている質問と一致するときに返される応答です。  
* **Metadata**:メタデータは、QnA ペアに関連付けられたタグであり、キーと値のペアとして表されます。 メタデータ タグは、QnA ペアをフィルター処理してクエリとのマッチングを行う対象を制限するために使用されます。

数値の QnA ID で表される 1 つの QnA には、さまざまなバリエーションの質問 (代替の質問) が含まれ、そのすべてが 1 つの回答にマッピングされています。 さらに、各ペアには複数のメタデータ フィールドを関連付けることができます (1 つのキーと 1 つの値)。

![QnA Maker ナレッジ ベース](../media/qnamaker-concepts-knowledgebase/knowledgebase.png) 

## <a name="knowledge-base-content-format"></a>ナレッジ ベースのコンテンツ形式

リッチ コンテンツをナレッジ ベースに取り込むと、QnA Maker はコンテンツを markdown に変換しようとします。 ほとんどのチャット クライアントが解釈できるマークダウン形式について確認するには、[こちらのブログ](https://aka.ms/qnamaker-docs-markdown-support)をお読みください。

メタデータ フィールドは、コロンで区切られたキーと値のペアで構成されます (Product:Shredder など)。 キーと値は、どちらもテキストである必要があります。 メタデータのキーにスペースを含めることはできません。 メタデータでは、1 つのキーにつき 1 つの値だけがサポートされています。

## <a name="how-qna-maker-processes-a-user-query-to-select-the-best-answer"></a>QnA Maker がユーザー クエリを処理して最適な回答を選択する方法

トレーニングされ、[公開された](/azure/cognitive-services/qnamaker/quickstarts/create-publish-knowledge-base#publish-the-knowledge-base) QnA Maker ナレッジ ベースは、[GenerateAnswer API](/azure/cognitive-services/qnamaker/how-to/metadata-generateanswer-usage) で、ボットまたはその他のクライアント アプリケーションからユーザー クエリを受け取ります。 次の図は、ユーザー クエリを受け取ったときのプロセスを示しています。

![ユーザー クエリのランク付けプロセス](../media/qnamaker-concepts-knowledgebase/rank-user-query-first-with-azure-search-then-with-qna-maker.png)

### <a name="ranker-process"></a>ランカー プロセス

このプロセスについて次の表で説明します。

|手順|目的|
|--|--|
|1|クライアント アプリケーションがユーザー クエリを [GenerateAnswer API](/azure/cognitive-services/qnamaker/how-to/metadata-generateanswer-usage) に送信します。|
|2|QnA Maker が、言語検出、スペル チェック プログラム、およびワード ブレーカーを使用してユーザー クエリを前処理します。|
|3|この前処理は、最適な検索結果が得られるようユーザー クエリを変更するために行われます。|
|4|この変更されたクエリが Azure Search インデックスに送信され、`top` の数の結果を受け取ります。 正しい回答がこれらの結果にない場合、`top` の値をわずかに増やします。 一般的に、`top` の値が 10 の場合は、90% のクエリで役立ちます。|
|5|QnA Maker は、高度な特徴抽出を適用して、ユーザー クエリに対して取得された Azure Search の結果の正確性を判別します。 |
|6|トレーニング済みのランカー モデルにより、手順 5 からの特徴スコアを使用して、Azure Search の結果にランクが付けられます。|
|7|新しい結果が、ランク順にクライアント アプリケーションに返されます。|
|||

使用される特徴は、単語レベルのセマンティクス、コーパスの用語レベルの重要性、2 つのテキスト文字列間の類似性と関連性を判別するディープ ラーニング済みのセマンティック モデルなどですが、これらに限定されません。

## <a name="http-request-and-response-with-endpoint"></a>エンドポイントの HTTP 要求/応答
ナレッジ ベースを公開すると、アプリケーション (通常はチャット ボット) に統合できる REST ベースの HTTP エンドポイントがサービスによって作成されます。 

### <a name="the-user-query-request-to-generate-an-answer"></a>回答を生成するユーザー クエリ要求

ユーザー クエリは、`How do I add a collaborator to my app?` など、エンド ユーザーがナレッジ ベースにたずねる質問です。 クエリは多くの場合、自然言語形式か、質問を表すいくつかのキーワードで行われます。たとえば、`help with collaborators` のようになります。 クエリはクライアント アプリケーションの HTTP 要求からナレッジ ベースに送信されます。

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

[scoreThreshold](./confidence-score.md#choose-a-score-threshold)、[top](../how-to/improve-knowledge-base.md#use-the-top-property-in-the-generateanswer-request-to-get-several-matching-answers)、[strictFilters](../how-to/metadata-generateanswer-usage.md#filter-results-with-strictfilters-for-metadata-tags) などのプロパティを設定することで応答を制御します。

[会話コンテキスト](../how-to/metadata-generateanswer-usage.md#use-question-and-answer-results-to-keep-conversation-context)を[マルチターン機能](../how-to/multiturn-conversation.md)と共に使用し、正しい最終的な回答が見つかるよう、会話を続けて質問と回答を練り上げます。

### <a name="the-response-from-a-call-to-generate-an-answer"></a>回答を生成する呼び出しからの応答

HTTP 応答は、特定のユーザー クエリの最適な一致に基づいて、ナレッジ ベースから取得された回答です。 応答には回答と予測スコアが含まれます。 `top` プロパティを使用して複数の上位回答を求めた場合、複数の上位回答が得られ、それぞれにスコアが付きます。 

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

### <a name="test-and-production-knowledge-base"></a>テストと運用環境のナレッジ ベース
ナレッジ ベースは、QnA Maker を通じて作成、管理、使用された質問と回答のリポジトリです。 各 QnA Maker レベルは、複数のナレッジ ベースに使用できます。

ナレッジ ベースには、"*テスト*" と "*公開*" という 2 つの状態があります。

"*テスト ナレッジ ベース*" は、応答の精度と完全性について編集、保存、テストが実行されるバージョンです。 テスト ナレッジ ベースに加えられた変更は、アプリケーションまたはチャット ボットのエンド ユーザーに影響しません。 テスト ナレッジ ベースは HTTP 要求では `test` になります。 

"*公開ナレッジ ベース*" は、チャット ボットまたはアプリケーションで使用されるバージョンです。 ナレッジ ベースを公開するアクションにより、テスト ナレッジ ベースの内容が、ナレッジ ベースの公開バージョン内に配置されます。 公開ナレッジ ベースは、アプリケーションがエンドポイントを介して使用するバージョンであるため、その内容が正確であり、十分にテストされていることを確認してください。 公開ナレッジ ベースは HTTP 要求では `prod` になります。

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [ナレッジ ベースの開発ライフサイクル](./development-lifecycle-knowledge-base.md)

## <a name="see-also"></a>関連項目

[QnA Maker の概要](../Overview/overview.md)

次を使用してナレッジ ベースを作成、編集します。 
* [REST API](https://docs.microsoft.com/en-us/rest/api/cognitiveservices/qnamaker/knowledgebase)
* [.NET SDK](https://docs.microsoft.com/en-us/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.knowledgebase?view=azure-dotnet)

次を使用して回答を生成します。 
* [REST API](https://docs.microsoft.com/en-us/rest/api/cognitiveservices/qnamakerruntime/runtime/generateanswer)
* [.NET SDK](https://docs.microsoft.com/en-us/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.runtime?view=azure-dotnet)
