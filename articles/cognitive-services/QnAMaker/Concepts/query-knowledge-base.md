---
title: ナレッジ ベースに対してクエリを実行する - QnA Maker
description: ナレッジ ベースは公開する必要があります。 公開されると、ナレッジ ベースに対するクエリは、generateAnswer API を使用してランタイム予測エンドポイントで実行されます。
ms.topic: conceptual
ms.date: 11/09/2020
ms.openlocfilehash: c723d1446c90290929bc8cad066b4744e284f3f4
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "103008673"
---
# <a name="query-the-knowledge-base-for-answers"></a>回答を求めてナレッジ ベースに対してクエリを実行する

ナレッジ ベースは公開する必要があります。 公開されると、ナレッジ ベースに対するクエリは、generateAnswer API を使用してランタイム予測エンドポイントで実行されます。 このクエリには、QnA Maker で回答に最適な一致候補を選択できるように、質問のテキストとその他の設定が含まれています。

## <a name="how-qna-maker-processes-a-user-query-to-select-the-best-answer"></a>QnA Maker がユーザー クエリを処理して最適な回答を選択する方法

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (安定版リリース)](#tab/v1)

トレーニングされ、[公開された](../quickstarts/create-publish-knowledge-base.md#publish-the-knowledge-base) QnA Maker ナレッジ ベースは、[GenerateAnswer API](../how-to/metadata-generateanswer-usage.md) で、ボットまたはその他のクライアント アプリケーションからユーザー クエリを受け取ります。 次の図は、ユーザー クエリを受け取ったときのプロセスを示しています。

![ユーザー クエリのランク付けモデル プロセス](../media/qnamaker-concepts-knowledgebase/ranker-v1.png)

### <a name="ranker-process"></a>ランカー プロセス

このプロセスについて次の表で説明します。

|手順|目的|
|--|--|
|1|クライアント アプリケーションがユーザー クエリを [GenerateAnswer API](../how-to/metadata-generateanswer-usage.md) に送信します。|
|2|QnA Maker が、言語検出、スペル チェック プログラム、およびワード ブレーカーを使用してユーザー クエリを前処理します。|
|3|この前処理は、最適な検索結果が得られるようユーザー クエリを変更するために行われます。|
|4|この変更されたクエリが Azure Cognitive Search インデックスに送信され、`top` の数の結果を受け取ります。 正しい回答がこれらの結果にない場合、`top` の値をわずかに増やします。 一般的に、`top` の値が 10 の場合は、90% のクエリで役立ちます。 Azure Search は、この手順で[ストップ ワード](https://github.com/Azure-Samples/azure-search-sample-data/blob/master/STOPWORDS.md)をフィルター処理します。|
|5|QnA Maker が、構文とセマンティック ベースの特性付けを使用して、ユーザー クエリとフェッチされた QnA の結果の間の類似性を判断します。|
|6|機械学習されたランカー モデルによって、手順 5 のさまざまな機能が使用され、信頼スコアと新しいランク付け順序が決定されます。|
|7|新しい結果が、ランク順にクライアント アプリケーションに返されます。|
|||

使用される特徴は、単語レベルのセマンティクス、コーパスの用語レベルの重要性、2 つのテキスト文字列間の類似性と関連性を判別するディープ ラーニング済みのセマンティック モデルなどですが、これらに限定されません。

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker マネージド (プレビュー リリース)](#tab/v2)

トレーニングされ、[公開された](../quickstarts/create-publish-knowledge-base.md#publish-the-knowledge-base) QnA Maker ナレッジ ベースは、[GenerateAnswer API](../how-to/metadata-generateanswer-usage.md) で、ボットまたはその他のクライアント アプリケーションからユーザー クエリを受け取ります。 次の図は、ユーザー クエリを受け取ったときのプロセスを示しています。

![ユーザー クエリ プレビューのランク付けモデル プロセス](../media/qnamaker-concepts-knowledgebase/ranker-v2.png)

### <a name="ranker-process"></a>ランカー プロセス

このプロセスについて次の表で説明します。

|手順|目的|
|--|--|
|1|クライアント アプリケーションがユーザー クエリを [GenerateAnswer API](../how-to/metadata-generateanswer-usage.md) に送信します。|
|2|QnA Maker が、言語検出、スペル チェック プログラム、およびワード ブレーカーを使用してユーザー クエリを前処理します。|
|3|この前処理は、最適な検索結果が得られるようユーザー クエリを変更するために行われます。|
|4|この変更されたクエリが Azure Cognitive Search インデックスに送信され、`top` の数の結果を受け取ります。 正しい回答がこれらの結果にない場合、`top` の値をわずかに増やします。 一般的に、`top` の値が 10 の場合は、90% のクエリで役立ちます。 Azure Search は、この手順で[ストップ ワード](https://github.com/Azure-Samples/azure-search-sample-data/blob/master/STOPWORDS.md)をフィルター処理します。|
|5|QnA Maker によって最新のトランスフォーマー ベースのモデルが使用され、ユーザー クエリと、Azure Cognitive Search からフェッチされた候補 QnA 結果の類似性が判断されます。 トランスフォーマー ベースのモデルはディープラーニングの多言語モデルであり、すべての言語で水平方向に動作し、信頼スコアと新しいランク付け順序を決定します。|
|6|新しい結果が、ランク順にクライアント アプリケーションに返されます。|
|||

ランカーは、ユーザー クエリに対して最も一致する QnA ペアを見つけるために、代替の質問と回答をすべて考慮します。 ランカーのみに質問するよう、ランカーを柔軟に構成できます。 

---

## <a name="http-request-and-response-with-endpoint"></a>エンドポイントの HTTP 要求/応答
ナレッジ ベースを公開すると、アプリケーション (通常はチャット ボット) に統合できる REST ベースの HTTP エンドポイントがサービスによって作成されます。

### <a name="the-user-query-request-to-generate-an-answer"></a>回答を生成するユーザー クエリ要求

ユーザー クエリは、`How do I add a collaborator to my app?` など、エンド ユーザーがナレッジ ベースにたずねる質問です。 クエリは多くの場合、自然言語形式か、質問を表すいくつかのキーワードで行われます。たとえば、`help with collaborators` のようになります。 クエリはクライアント アプリケーションの HTTP 要求からナレッジ ベースに送信されます。

```json
{
    "question": "How do I add a collaborator to my app?",
    "top": 6,
    "isTest": true,
    "scoreThreshold": 20,
    "strictFilters": [
    {
        "name": "QuestionType",
        "value": "Support"
    }],
    "userId": "sd53lsY="
}
```
[scoreThreshold](./confidence-score.md#choose-a-score-threshold)、[top](../how-to/improve-knowledge-base.md#use-the-top-property-in-the-generateanswer-request-to-get-several-matching-answers)、[strictFilters](../how-to/query-knowledge-base-with-metadata.md) などのプロパティを設定することで応答を制御します。

[会話コンテキスト](../how-to/query-knowledge-base-with-metadata.md)を[マルチターン機能](../how-to/multiturn-conversation.md)と共に使用し、正しい最終的な回答が見つかるよう、会話を続けて質問と回答を練り上げます。

### <a name="the-response-from-a-call-to-generate-an-answer"></a>回答を生成する呼び出しからの応答

HTTP 応答は、特定のユーザー クエリの最適な一致に基づいて、ナレッジ ベースから取得された回答です。 応答には回答と予測スコアが含まれます。 `top` プロパティを使用して複数の上位回答を求めた場合、複数の上位回答が得られ、それぞれにスコアが付きます。

```json
{
    "answers": [
        {
            "questions": [
                "How do I add a collaborator to my app?",
                "What access control is provided for the app?",
                "How do I find user management and security?"
            ],
            "answer": "Use the Azure portal to add a collaborator using Access Control (IAM)",
            "score": 100,
            "id": 1,
            "source": "Editorial",
            "metadata": [
                {
                    "name": "QuestionType",
                    "value": "Support"
                },
                {
                    "name": "ToolDependency",
                    "value": "Azure Portal"
                }
            ]
        }
    ]
}
```


## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [信頼度スコア](./confidence-score.md)
