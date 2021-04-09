---
title: Personalizer のしくみ - Personalizer
description: Personalizer の "_ループ_" は、機械学習を使用して、コンテンツの最上位のアクションを予測するモデルを構築します。 このモデルは、Rank と Reward の呼び出しを使用して送信したデータでのみトレーニングされます。
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: conceptual
ms.date: 02/18/2020
ms.openlocfilehash: cfbe5cf8c19bfafb38f6149391e09350785ebf9c
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "91303609"
---
# <a name="how-personalizer-works"></a>Personalizer のしくみ

Personalizer のリソースである "_学習ループ_" は、機械学習を使用して、コンテンツの最上位のアクションを予測するモデルを構築します。 このモデルは、**Rank** と **Reward** の呼び出しを使用して送信したデータでのみトレーニングされます。 すべてのループは、互いに完全に独立しています。

## <a name="rank-and-reward-apis-impact-the-model"></a>Rank および Reward の API はモデルに影響を与える

"_特徴のあるアクション_" と "_コンテキストの特徴_" を Rank API に送信します。 **Rank** API では、次のいずれかを使用することを決定できます。

* _悪用_: 過去のデータに基づいて最善のアクションを決定するための現在のモデル。
* _探索_: 最上位のアクションではなく、別のアクションを選択します。 Azure portal で Personalizer リソースに対して[このパーセンテージを構成](how-to-settings.md#configure-exploration-to-allow-the-learning-loop-to-adapt)します。

報酬スコアを判別し、そのスコアを Reward API に送信します。 **Reward** API: 

* 各 Rank 呼び出しの特徴と報酬スコアを記録することによってモデルをトレーニングするためのデータを収集します。
* そのデータを使用して、"_学習ポリシー_" に指定された構成に基づいてモデルを更新します。

## <a name="your-system-calling-personalizer"></a>Personalizer を呼び出すシステム

次の図は、Rank と Reward の呼び出しを呼び出すアーキテクチャの流れを示しています。

![代替テキスト](./media/how-personalizer-works/personalization-how-it-works.png "パーソナル化のしくみ")

1. "_特徴のあるアクション_" と "_コンテキストの特徴_" を Rank API に送信します。

    * Personalizer は、現在のモデルを活用するか、またはモデルの新しい選択肢を探索するかを決定します。
    * 順位付けの結果は EventHub に送信されます。
1. 最上位のランクが、"_報酬アクション ID_" としてシステムに返されます。
    システムはそのコンテンツを表示し、独自のビジネス ルールに基づいて報酬スコアを決定します。
1. システムは学習ループに報酬スコアを返します。
    * Personalizer が報酬を受け取ると、その報酬が EventHub に送信されます。
    * 順位と報酬が関連付けられます。
    * AI モデルが、相関関係の結果に基づいて更新されます。
    * 推論エンジンが、新しいモデルで更新されます。

## <a name="personalizer-retrains-your-model"></a>Personalizer はモデルを再トレーニングする

Personalizer は、Azure portal 内の Personalizer リソースの **モデルの更新頻度** 設定に基づいて、モデルを再トレーニングします。

Personalizer は、Azure portal 内の Personalizer リソースに対する **データ保持** 設定の日数に基づいて、現在保持されているすべてのデータを使用します。

## <a name="research-behind-personalizer"></a>Personalizer の背後にある研究

Personalizer は、論文、研究活動、および現在進行中の Microsoft Research の探索分野を含む、[強化学習](concepts-reinforcement-learning.md)分野の最先端の科学および研究に基づいています。

## <a name="next-steps"></a>次のステップ

Personalizer の[主要なシナリオ](where-can-you-use-personalizer.md)について学習します