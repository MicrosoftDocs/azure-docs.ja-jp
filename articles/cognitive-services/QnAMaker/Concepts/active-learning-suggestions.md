---
title: アクティブ ラーニングの提案 - QnA Maker
description: アクティブ ラーニングの提案を使用すると、質問と回答のペアに対して、ユーザーの送信内容に基づく代わりの質問を提案することで、ナレッジ ベースの品質を改善できます。
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 11/09/2020
ms.openlocfilehash: e1a8043912c984be46f85bd384a7049da27028b3
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "96353240"
---
# <a name="active-learning-suggestions"></a>アクティブ ラーニングの提案

"_アクティブ ラーニングの提案_" 機能を使用すると、質問と回答のペアに対して、ユーザーの送信内容に基づく代わりの質問を提案することで、ナレッジ ベースの品質を改善できます。 それらの提案を検討し、既存の質問に追加するか却下します。

ナレッジ ベースが自動的に変更されることはありません。 変更を有効にするためには、提案を受け入れる必要があります。 これらの提案によって質問が追加されますが、既存の質問の変更や削除は行われません。

## <a name="what-is-active-learning"></a>アクティブ ラーニングとは

QnA Maker は、暗黙的および明示的フィードバックによって、新しい質問のバリエーションを学習します。

* [暗黙的フィードバック](#how-qna-makers-implicit-feedback-works) – ランカーは、ユーザーの質問に、スコアが非常に近い回答が複数ある状況を認識して、これをフィードバックと見なします。 これが行われるためにユーザーの操作は必要ありません。
* [明示的フィードバック](#how-you-give-explicit-feedback-with-the-train-api) – ナレッジ ベースから、スコアのバリエーションがほとんどない回答が複数返されると、クライアント アプリケーションはユーザーに、どの質問が正しい質問であるかを尋ねます。 ユーザーの明示的フィードバックは、[Train API](../How-to/improve-knowledge-base.md#train-api) を介して QnA Maker に送信されます。

どちらの方法でも、ランカーにはクラスター化されている類似のクエリが提供されます。

## <a name="how-active-learning-works"></a>アクティブ ラーニングの動作方法

アクティブ ラーニングは、QnA Maker によって返される、上位いくつかの回答のスコアに基づいてトリガーされます。 クエリに一致する QnA ペア間のスコアの違いが狭い範囲内にある場合、そのクエリは、QnA ペアの各候補について考えられる (代替質問としての) 提案であると見なされます。 特定の QnA ペアについて提案された質問をいったん受け入れると、他のペアについての質問は拒否されます。 提案を受け入れた後は、忘れずに保存とトレーニングを行う必要があります。

アクティブ ラーニングでは、エンドポイントが、妥当な量で多様性のある使用状況クエリを受け取っている場合、可能な限り最適な提案が示されます。 類似のクエリが 5 つ以上クラスター化された場合、QnA Maker は 30 分おきに、ユーザー ベースの質問をナレッジ ベース デザイナーに提案し、承認または却下を求めます。 すべての提案は類似度によって一緒にクラスター化され、エンドユーザーによる特定のクエリの頻度に基づいて、代わりの質問に対する上位の提案が表示されます。

QnA Maker ポータルで質問が提案されたら、それらの提案をレビューして、承認または拒否する必要があります。 提案を管理するための API はありません。

## <a name="turn-on-active-learning"></a>アクティブ ラーニングを有効にする

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (安定版リリース)](#tab/v1)

アクティブ ラーニングは、既定では **無効** になっています。
アクティブ ラーニングを使用するには:
* QnA Maker でナレッジ ベース用の代わりの質問を収集できるように、[アクティブ ラーニングを有効にする](../How-To/use-active-learning.md#turn-on-active-learning-for-alternate-questions)必要があります。
* 提案された代わりの質問を表示するには、[編集] ページの [[表示] オプションを使用](../How-To/improve-knowledge-base.md#view-suggested-questions)します。

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker マネージド (プレビュー リリース)](#tab/v2)

QnA Maker マネージド (プレビュー) のアクティブ ラーニングは、既定では **有効** になっています。 提案された代わりの質問を表示するには、[編集] ページの [[表示] オプションを使用](../How-To/improve-knowledge-base.md#view-suggested-questions)します。

---

## <a name="how-qna-makers-implicit-feedback-works"></a>QnA Maker の暗黙的フィードバックの動作方法

QnA Maker の暗黙的フィードバックでは、スコアの近さを判定してからアクティブ ラーニングの提案を行うアルゴリズムが使用されます。 近さを判定するアルゴリズムは、単純な計算ではありません。 次の例の範囲は、固定的なものではなく、アルゴリズムの影響を理解する指針としてのみ使用する必要があります。

質問のスコアの信頼度が高い (80% など) 場合、アクティブ ラーニング用に適すると見なされるスコアの範囲は広く、およそ 10% 以内です。 信頼度スコアが低下すると (40% など)、スコアの範囲も狭まり、約 4% 以内となります。

クエリから QnA Maker の generateAnswer への次の JSON 応答では、A、B、C のスコアは近く、提案として見なされます。

```json
{
  "activeLearningEnabled": true,
  "answers": [
    {
      "questions": [
        "Q1"
      ],
      "answer": "A1",
      "score": 80,
      "id": 15,
      "source": "Editorial",
      "metadata": [
        {
          "name": "topic",
          "value": "value"
        }
      ]
    },
    {
      "questions": [
        "Q2"
      ],
      "answer": "A2",
      "score": 78,
      "id": 16,
      "source": "Editorial",
      "metadata": [
        {
          "name": "topic",
          "value": "value"
        }
      ]
    },
    {
      "questions": [
        "Q3"
      ],
      "answer": "A3",
      "score": 75,
      "id": 17,
      "source": "Editorial",
      "metadata": [
        {
          "name": "topic",
          "value": "value"
        }
      ]
    },
    {
      "questions": [
        "Q4"
      ],
      "answer": "A4",
      "score": 50,
      "id": 18,
      "source": "Editorial",
      "metadata": [
        {
          "name": "topic",
          "value": "value"
        }
      ]
    }
  ]
}
```

QnA Maker では、どの回答が最適な回答であるかはわかりません。 QnA Maker ポータルの提案リストを使用して、最適な回答を選択し、もう一度トレーニングします。


## <a name="how-you-give-explicit-feedback-with-the-train-api"></a>Train API で明示的フィードバックを提供する方法

QnA Maker では、どの回答が最適な回答であったかについて明示的フィードバックが必要です。 最適な回答をどのように決定するかはユーザーの自由で、その方法には以下が含まれる場合があります。

* いずれかの回答を選択することによるユーザーのフィードバック。
* 許容できるスコアの範囲を決定するなどのビジネス ロジック。
* ユーザーのフィードバックとビジネス ロジックの両方の組み合わせ。

[Train API](/rest/api/cognitiveservices/qnamaker4.0/runtime/train) を使用して、ユーザーが選択した後、正しい回答を QnA Maker に送信します。

## <a name="next-step"></a>次のステップ

> [!div class="nextstepaction"]
> [ナレッジ ベースに対してクエリを実行する](query-knowledge-base.md)