---
title: 探索 - Personalizer
titleSuffix: Azure Cognitive Services
description: 探索により、Personalizer は、ユーザー動作が変更しても良好な結果を提供し続けることができます。 探索設定の選択は、モデルを改善するために、探索に使用するユーザー操作の比率に関するビジネス上の意思決定です。
services: cognitive-services
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: conceptual
ms.date: 10/23/2019
ms.openlocfilehash: 00b9cc881d9414e957d7503bb6156095bb821c06
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "87128150"
---
# <a name="exploration-and-exploitation"></a>探索とエクスプロイト

探索により、Personalizer は、ユーザー動作が変更しても良好な結果を提供し続けることができます。

Personalizer は Rank 呼び出しを受信すると、次のどちらかの RewardActionID を返します。
* 現在の機械学習モデルに基づいて最も可能性の高いユーザー動作を照合するためにエクスプロイトを使用する。
* ランクで最も可能性が高いアクションを照合しない検索を使用する。

Personalizer は現在、*epsilon greedy* と呼ばれる検索アルゴリズムを使用しています。 

## <a name="choosing-an-exploration-setting"></a>探索設定の選択

Azure portal の Personalizer 用の **[構成]** ページで、探索に使用するトラフィックの割合を構成します。 この設定は、探索を実行する Rank 呼び出しの割合を決定します。 

Personalizer は、Rank 呼び出しごとに、この確率で探索するかエクスプロイトするかを判断します。 これは、特定のユーザー ID の取り扱いをロックする一部の A/B フレームワークにおける動作とは異なります。

## <a name="best-practices-for-choosing-an-exploration-setting"></a>探索設定の選択のベストプラクティス

探索設定の選択は、モデルを改善するために、探索に使用するユーザー操作の比率に関するビジネス上の意思決定です。 

ゼロに設定すると Personalizer の多くの利点が損なわれます。 この設定では、Personalizer はより優れたユーザー操作を検出するためにユーザー操作を使用しません。 これは、モデルの停滞、ドリフト、最終的にはパフォーマンスの低下を招きます。

高すぎる値に設定すると、ユーザーの動作から学習する利点が損なわれます。 100% に設定することは定数のランダム化を意味し、ユーザーから学習した動作は結果に影響しません。

Personalizer が探索しているかエクスプロイトしているかに基づいてアプリケーションの動作を変更しないことが重要です。 これは、最終的には潜在的なパフォーマンスを低下させる学習バイアスを招きます。

## <a name="next-steps"></a>次のステップ

[強化学習](concepts-reinforcement-learning.md) 