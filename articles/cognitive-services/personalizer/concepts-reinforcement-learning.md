---
title: 強化学習 - Personalizer
titleSuffix: Azure Cognitive Services
description: Personalizer では、より良い順位付けの提案を行う目的で、アクションと現在のコンテキストに関する情報が利用されます。 これらのアクションとコンテキストに関する情報は、特徴と呼ばれる属性またはプロパティです。
services: cognitive-services
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: conceptual
ms.date: 05/07/2019
ms.openlocfilehash: 8b97221de4921e06ddfab610618f37683b990181
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "87132740"
---
# <a name="what-is-reinforcement-learning"></a>強化学習とは

強化学習は、その使用からフィードバックを得ることによって動作を学習する機械学習へのアプローチです。
 
強化学習は、次のように動作します。

* 意思決定や選択など、動作を実行する機会または自由度を提供する。
* 環境と選択肢に関するコンテキスト情報を提供する。
* 動作が特定の目標をどの程度達成しているかについてフィードバックを与える。

強化学習に多くのサブタイプとスタイルがある中で、これは Personalizer の概念のしくみです。

* アプリケーションは、選択肢のリストから 1 つのコンテンツを表示する機会を提供します。
* アプリケーションは、それぞれの選択肢とユーザーのコンテキストに関する情報を提供します。
* アプリケーションは、"_報酬スコア_" を計算します。

強化学習へのいくつかのアプローチとは異なり、Personalizer での作業にはシミュレーションは必要ありません。 その学習アルゴリズムは、(外部の世界を制御するのではなく) 外部の世界に反応するように設計されています。さらに、作成するのに時間と費用がかかる一意の機会であること、および最適状態には及ばないパフォーマンスが発生した場合に後悔がゼロではないこと (報酬の可能性の損失があること) を理解したうえで、各データ ポイントから学習するように設計されています。

## <a name="what-type-of-reinforcement-learning-algorithms-does-personalizer-use"></a>Personalizer で使用される強化学習アルゴリズムの種類

最新のバージョンの Personalizer では **Contextual Bandits** を使用します。これは、特定のコンテキストにおいて個別のアクション間で意思決定や選択を行うことを中心とした強化学習へのアプローチです。

特定の与えられたコンテキストに対して可能な限り最善の決定を取得するようにトレーニングされたモデルである "_デシジョン メモリ_" では、線形モデルのセットを使用します。 1 つにはマルチパス トレーニングを必要とせずに現実世界から非常に迅速に学習することができること、また 1 つには教師付き学習モデルおよびディープ ニューラル ネットワーク モデルを補完できることから、これらはビジネス上の成果を繰り返し収めた、証明されたアプローチです。

探索および活用トラフィック割り当ては、探索用に設定された割合に従ってランダムに行われ、探索の既定のアルゴリズムは Epsilon-Greedy です。

### <a name="history-of-contextual-bandits"></a>Contextual Bandits の歴史

Contextual Bandits は、強化学習の扱いやすいサブセットを記述するために John Langford 氏によって作られた名前です (Langford および Zhang [2007])。氏は、このパラダイムでの学習方法の理解を深めるために五指に余る論文に取り組んできました。

* Beygelzimer et al. [2011]
* Dudík et al. [2011a,b]
* Agarwal et al. [2014, 2012]
* Beygelzimer and Langford [2009]
* Li et al. [2010]

また、John は、Joint Prediction (ICML 2015)、Contextual Bandit Theory (NIPS 2013)、Active Learning (ICML 2009)、Sample Complexity Bounds (ICML 2003) などのトピックに関するいくつかのチュートリアルも以前に作成しました

## <a name="what-machine-learning-frameworks-does-personalizer-use"></a>Personalizer で使用される機械学習フレームワーク

Personalizer では、現在、機械学習の基盤として [Vowpal Wabbit](https://github.com/VowpalWabbit/vowpal_wabbit/wiki) を使用しています。 このフレームワークは、パーソナル化の順位付けを行い、すべてのイベントでモデルをトレーニングするときに、最大のスループットと最小の待ち時間を実現します。

## <a name="references"></a>References

* [Making Contextual Decisions with Low Technical Debt (少ない技術的負債でコンテキストに応じた判断を下す)](https://arxiv.org/abs/1606.03966)
* [A Reductions Approach to Fair Classification (公正な分類への削減アプローチ)](https://arxiv.org/abs/1803.02453)
* [Efficient Contextual Bandits in Non-stationary Worlds (非定常世界における効率的な Contextual Bandits)](https://arxiv.org/abs/1708.01799)
* [Reinforcement: learning With No Incremental Feedback (強化: インクリメンタル フィードバックなしでの学習)](https://openreview.net/pdf?id=HJNMYceCW)
* [Mapping Instructions and Visual Observations to Actions with Reinforcement Learning (強化学習による手順と視覚的観察のアクションへのマッピング)](https://arxiv.org/abs/1704.08795)
* [Learning to Search Better Than Your Teacher (教師よりも優れた検索を行うための学習)](https://arxiv.org/abs/1502.02206)

## <a name="next-steps"></a>次のステップ

[オフライン評価](concepts-offline-evaluation.md) 