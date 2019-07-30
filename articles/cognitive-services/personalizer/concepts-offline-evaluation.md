---
title: オフライン評価 - Personalizer
titleSuffix: Azure Cognitive Services
description: Personalizer サービスを使用するこの C# のクイック スタートではフィードバック ループを作成します。
services: cognitive-services
author: edjez
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: conceptual
ms.date: 05/07/2019
ms.author: edjez
ms.openlocfilehash: 3fdedd1af9b683b221dfa4aebad7a30559b7abff
ms.sourcegitcommit: dad277fbcfe0ed532b555298c9d6bc01fcaa94e2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/10/2019
ms.locfileid: "67722493"
---
# <a name="offline-evaluation"></a>オフライン評価

オフライン評価は、コードを変更したりユーザー エクスペリエンスに影響を与えたりすることなく、Personalizer サービスの有効性をテストおよび評価できるようにする方法です。 オフライン評価では、アプリケーションから Rank API に送信された過去のデータを使用して、さまざまな順位のパフォーマンスを比較します。

オフライン評価は、日付の範囲で実行されます。 設定できる範囲の終わりは、現在の時間までです。 範囲の始まりは、[データ保持期間](how-to-settings.md)として指定された日数を超えてはいけません。

オフライン評価は、次の質問に回答するのに役立ちます。

* Personalizer の順位はパーソナル化を成功させるためにどの程度効果的か。
    * Personalizer オンライン機械学習ポリシーによって達成される平均的な報酬はどれくらいか。
    * Personalizer の有効性とアプリケーションの既定の動作で得られる有効性にどれだけの違いがあるか。
    * パーソナル化のためのランダムな選択の比較有効性はどうだったか。
    * 手動で指定されたさまざまな学習ポリシーの比較有効性はどうだったか。
* パーソナル化の成功に多少とも貢献しているのはコンテキストのどの特徴か。
* パーソナル化の成功に多少とも貢献しているのはアクションのどの特徴か。

さらに、オフライン評価を使用して、Personalizer が将来の結果を改善するために使用できる、より最適化された学習ポリシーを発見することができます。

オフライン評価では、探索に使用するイベントの割合についてのガイダンスは提供されません。

## <a name="prerequisites-for-offline-evaluation"></a>オフライン評価の前提条件

次に、代表的なオフライン評価のための重要な考慮事項を示します。

* 十分なデータがあること。 推奨される最小値は少なくとも 50,000 イベントです。
* 代表的なユーザー動作とトラフィックのある期間からデータを収集する。

## <a name="discovering-the-optimized-learning-policy"></a>最適化された学習ポリシーの発見

Personalizer では、オフライン評価プロセスを使用して、最適な学習ポリシーを自動的に発見できます。

オフライン評価を実行した後に、現在のオンライン ポリシーと比較したその新しいポリシーを使用する Personalizer の比較有効性を確認できます。 その後、その学習ポリシーを適用して Personalizer ですぐに有効にすることも、将来の分析または使用のためにダウンロードすることもできます。

## <a name="understanding-the-relevance-of-offline-evaluation-results"></a>オフライン評価の結果の関連性を理解する

オフライン評価を実行するときは、結果の "_信頼境界_" を分析することが非常に重要です。 これが広い場合、報酬予測を正確または重要なものにするのに十分なデータをアプリケーションが受け取っていないことを意味します。 システムがより多くのデータを蓄積し、長期間にわたってオフライン評価を実行すると、信頼区間は狭くなります。

## <a name="how-offline-evaluations-are-done"></a>オフライン評価の実行方法

オフライン評価は、**反事実的評価**と呼ばれる方法を使用して行われます。 

Personalizer は、ユーザーの動作 (および報酬) が遡及的に予測することが不可能である (ユーザーが自分の見たものと異なる何かを見せられた場合に何が起こったのかを知ることができない) という仮定に基づき、測定された報酬からのみ学習します。 

これは、評価に使用される概念的なプロセスです。

```
[For a given _learning policy), such as the online learning policy, uploaded learning policies, or optimized candidate policies]:
{
    Initialize a virtual instance of Personalizer with that policy and a blank model;

    [For every chronological event in the logs]
    {
        - Perform a Rank call
    
        - Compare the reward of the results against the logged user behavior.
            - If they match, train the model on the observed reward in the logs.
            - If they don't match, then what the user would have done is unknown, so the event is discarded and not used for training or measurement.
        
    }

    Add up the rewards and statistics that were predicted, do some aggregation to aid visualizations, and save the results.
}
```

オフライン評価では、観察されたユーザー動作のみが使用されます。 このプロセスは、特にアプリケーションが大量のアクションに対して Rank 呼び出しを行う場合に大量のデータを破棄します。


## <a name="evaluation-of-features"></a>特徴の評価

オフライン評価では、アクションまたはコンテキストの特定の特徴のうちのどれだけ多くのものにより高い報酬が獲得できる重みが付けられているかに関する情報を提供できます。 この情報は、与えられた期間とデータに対する評価を使用して計算され、時間と共に変化する可能性があります。

特徴評価を見て、次の点を確認することをお勧めします。

* アプリケーションまたはシステムは、より効果的な特徴に従って他のどのような追加特徴を提供することができますか。
* 有効性が低いために削除できる特徴は何ですか。 有効性の低い特徴により、機械学習に "_ノイズ_" が追加されます。
* 誤って含まれている特徴がありますか。 たとえば、個人を特定できる情報 (PII)、重複する ID などです。
* 規制上または責任ある使用上の考慮事項のためにパーソナル化に使用すべきではない、望ましくない特徴はありますか。 望ましくない特徴の代用となる特徴 (よく似ている特徴、関連がある特徴など) はありますか。


## <a name="next-steps"></a>次の手順

[Personalizer を構成する](how-to-settings.md)
