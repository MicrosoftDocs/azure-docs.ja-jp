---
title: メタデータを使用してコンテキストでフィルター処理する
titleSuffix: Azure Cognitive Services
description: QnA Maker は、メタデータによって QnA のペアをフィルター処理します。
services: cognitive-services
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 11/09/2020
ms.custom: devx-track-js, devx-track-csharp
ms.openlocfilehash: 8f65ca9386963824f0cb740f587de83c9dec7f78
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "103017425"
---
# <a name="filter-responses-with-metadata"></a>メタデータを使用して応答をフィルター処理する

QnA Maker では、キーと値のペアの形式で、メタデータを質問と回答のペアに追加することができます。 この情報を使用して、ユーザー クエリの結果をフィルター処理し、フォローアップ会話で使用できる追加情報を格納できます。

<a name="qna-entity"></a>

## <a name="store-questions-and-answers-with-a-qna-entity"></a>QnA エンティティを使用して質問と回答を保存する

まず、QnA Maker での質問と回答のデータの格納方法を理解することが重要です。 QnA エンティティを次の図に示します。

![QnA エンティティのイラスト](../media/qnamaker-how-to-metadata-usage/qna-entity.png)

QnA エンティティにはそれぞれ一意の永続 ID があります。 ID を使用して、特定の QnA エンティティを更新することができます。

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

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [ナレッジベースの分析](../How-to/get-analytics-knowledge-base.md)
