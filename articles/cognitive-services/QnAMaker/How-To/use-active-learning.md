---
title: ナレッジ ベースと共にアクティブ ラーニングを使用する - QnA Maker
description: アクティブ ラーニングを使用してナレッジ ベースの質を向上させる方法について説明します。 既存の質問の削除や変更は行わずに、レビュー、承認、却下を行います。
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 03/18/2020
ms.openlocfilehash: 87dde7662050794a24cf976a0bae6237b91d29b2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "102213710"
---
# <a name="active-learning"></a>アクティブ ラーニング

"_アクティブ ラーニングの提案_" 機能を使用すると、質問と回答のペアに対して、ユーザーの送信内容に基づく代わりの質問を提案することで、ナレッジ ベースの品質を改善できます。 それらの提案を検討し、既存の質問に追加するか却下します。

ナレッジ ベースが自動的に変更されることはありません。 変更を有効にするためには、提案を受け入れる必要があります。 これらの提案によって質問が追加されますが、既存の質問の変更や削除は行われません。

## <a name="what-is-active-learning"></a>アクティブ ラーニングとは

QnA Maker は、暗黙的および明示的フィードバックによって、新しい質問のバリエーションを学習します。

* [暗黙的フィードバック](#how-qna-makers-implicit-feedback-works) – ランカーは、ユーザーの質問に、スコアが非常に近い回答が複数ある状況を認識して、これをフィードバックと見なします。 これが行われるためにユーザーの操作は必要ありません。
* [明示的フィードバック](#how-you-give-explicit-feedback-with-the-train-api) – ナレッジ ベースから、スコアのバリエーションがほとんどない回答が複数返されると、クライアント アプリケーションはユーザーに、どの質問が正しい質問であるかを尋ねます。 ユーザーの明示的フィードバックは、[Train API](../How-To/improve-knowledge-base.md#train-api) を介して QnA Maker に送信されます。

どちらの方法でも、ランカーにはクラスター化されている類似のクエリが提供されます。

## <a name="how-active-learning-works"></a>アクティブ ラーニングの動作方法

アクティブ ラーニングは、QnA Maker によって返される、上位いくつかの回答のスコアに基づいてトリガーされます。 クエリに一致する QnA ペア間のスコアの違いが狭い範囲内にある場合、そのクエリは、QnA ペアの各候補について考えられる (代替質問としての) 提案であると見なされます。 特定の QnA ペアについて提案された質問をいったん受け入れると、他のペアについての質問は拒否されます。 提案を受け入れた後は、忘れずに保存とトレーニングを行う必要があります。

アクティブ ラーニングでは、エンドポイントが、妥当な量で多様性のある使用状況クエリを受け取っている場合、可能な限り最適な提案が示されます。 類似のクエリが 5 つ以上クラスター化された場合、QnA Maker は 30 分おきに、ユーザー ベースの質問をナレッジ ベース デザイナーに提案し、承認または却下を求めます。 すべての提案は類似度によって一緒にクラスター化され、エンドユーザーによる特定のクエリの頻度に基づいて、代わりの質問に対する上位の提案が表示されます。

QnA Maker ポータルで質問が提案されたら、それらの提案をレビューして、承認または拒否する必要があります。 提案を管理するための API はありません。

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

## <a name="upgrade-runtime-version-to-use-active-learning"></a>アクティブ ラーニングを使用するためにランタイム バージョンをアップグレードする

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (安定版リリース)](#tab/v1)

アクティブ ラーニングは、ランタイム バージョン 4.4.0 以上でサポートされています。 ナレッジ ベースが以前のバージョンで作成された場合は、この機能を使用するために[ランタイムをアップグレード](configure-QnA-Maker-resources.md#get-the-latest-runtime-updates)します。

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker マネージド (プレビュー リリース)](#tab/v2)

QnA Maker マネージド (プレビュー) では、ランタイムが QnA Maker サービス自体によってホストされるため、ランタイムを手動でアップグレードする必要がありません。

---

## <a name="turn-on-active-learning-for-alternate-questions"></a>代わりの質問のためにアクティブ ラーニングを有効にする

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (安定版リリース)](#tab/v1)

アクティブ ラーニングは、既定では無効になっています。 これを有効にして、提案された質問を表示します。 アクティブ ラーニングを有効にした後は、クライアント アプリから QnA Maker に情報を送信する必要があります。 詳細については、「[ボットから GenerateAnswer および Train API を使用するためのアーキテクチャの流れ](improve-knowledge-base.md#architectural-flow-for-using-generateanswer-and-train-apis-from-a-bot)」を参照してください。

1. **[発行]** を選択してナレッジ ベースを発行します。 アクティブ ラーニング クエリは、GenerateAnswer API 予測エンドポイントからのみ収集されます。 QnA Maker ポータルの [テスト] ウィンドウへのクエリは、アクティブ ラーニングには影響しません。

1. QnA Maker ポータルでアクティブ ラーニングを有効にするには、右上隅に移動して自分の **名前** を選択し、[**Service settings\(サービス設定\)**](https://www.qnamaker.ai/UserSettings) に移動します。

    ![[Service settings]\(サービス設定\) ページで、アクティブ ラーニングの提案された代わりの質問を有効にします。 右上のメニューで自分のユーザー名を選択し、[Service Settings]\(サービス設定\) を選択します。](../media/improve-knowledge-base/Endpoint-Keys.png)


1. QnA Maker サービスを見つけて **[Active Learning] (アクティブ ラーニング)** を切り替えます。

    > [!div class="mx-imgBorder"]
    > [![[Service settings]\(サービス設定\) ページで、アクティブ ラーニング機能をオンに切り替えます。機能を切り替えられないときは、サービスをアップグレードしなければならない場合があります。](../media/improve-knowledge-base/turn-active-learning-on-at-service-setting.png)](../media/improve-knowledge-base/turn-active-learning-on-at-service-setting.png#lightbox)

    > [!Note]
    > 上の画像の具体的なバージョンは、単なる例として表示されています。 実際のバージョンは、異なる場合があります。

    **[Active Learning]\(アクティブ ラーニング\)** が有効になると、ユーザーが送信した質問に基づいて、ナレッジ ベースから定期的に新しい質問が提案されます。 設定を再度切り替えると、 **[Active Learning] (アクティブ ラーニング)** を無効にできます。
    
# <a name="qna-maker-managed-preview-release"></a>[QnA Maker マネージド (プレビュー リリース)](#tab/v2)

QnA Maker マネージド (プレビュー) のアクティブ ラーニングは、既定では **有効** になっています。 提案された代わりの質問を表示するには、[編集] ページの [[表示] オプションを使用](../How-To/improve-knowledge-base.md#view-suggested-questions)します。

---

## <a name="review-suggested-alternate-questions"></a>推奨される代わりの質問を確認する

各ナレッジ ベースの **[編集]** ページで、[代わりに推奨された質問を確認](improve-knowledge-base.md)します。

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [ナレッジ ベースの作成](./manage-knowledge-bases.md)
