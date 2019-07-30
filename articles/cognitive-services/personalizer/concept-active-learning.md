---
title: アクティブ ラーニング - Personalizer
titleSuffix: Azure Cognitive Services
description: ''
services: cognitive-services
author: edjez
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: conceptual
ms.date: 05/30/2019
ms.author: edjez
ms.openlocfilehash: c44afc81a7ec9d05cdc04cc8bc46c77cd51ceaf8
ms.sourcegitcommit: dad277fbcfe0ed532b555298c9d6bc01fcaa94e2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/10/2019
ms.locfileid: "67722516"
---
# <a name="active-learning-and-learning-policies"></a>アクティブ ラーニングと学習ポリシー 

アプリケーションが Rank API を呼び出すと、コンテンツの順位が通知されます。 ビジネス ロジックは、この順位を使用して、そのコンテンツをユーザーに表示するかどうかを決定します。 順位付けされたコンテンツが表示された場合、それは_アクティブな_順位イベントです。 その順位付けされたコンテンツがアプリケーションで表示されなかった場合、それは_非アクティブな_順位イベントです。 

アクティブな順位イベントの情報は Personalizer に返されます。 この情報は、現在の学習ポリシーに基づいてモデルのトレーニングを続けるために使用されます。

## <a name="active-events"></a>アクティブなイベント

アクティブなイベントは必ずユーザーに表示される必要があり、報酬呼び出しは学習ループを閉じるために返される必要があります。 

### <a name="inactive-events"></a>非アクティブなイベント 

非アクティブなイベントによって基になるモデルが変更されないようにしてください。順位付けされたコンテンツをユーザー自ら選べたわけではないからです。

## <a name="dont-train-with-inactive-rank-events"></a>非アクティブな順位イベントを使ったトレーニングは行わないようにする 

一部のアプリケーションでは、アプリケーションでユーザーに結果が表示されるかどうかが不明な状態のままで、Rank API を呼び出さなければならない場合があります。 

これは次の場合に発生します。

* ユーザーに対して表示されるかどうかに関係なく、一部の UI を事前レンダリングしている可能性がある。 
* アプリケーションが、リアルタイム性の低いコンテキストを使用して Rank 呼び出しが行われる予測パーソナル化を実行していて、その出力がアプリケーションで使用される場合もあれば、使用されない場合もある。 

### <a name="disable-active-learning-for-inactive-rank-events-during-rank-call"></a>Rank 呼び出し中に非アクティブな順位イベントのアクティブ ラーニングを無効にする

自動ラーニングを無効にするには、`learningEnabled = False` を指定して Rank を呼び出します。

非アクティブなイベントのラーニングは、順位の報酬を送信した場合、暗黙的にアクティブになります。

## <a name="learning-policies"></a>学習ポリシー

学習ポリシーは、モデル トレーニングの特定の "*ハイパーパラメーター*" を決めるものです。 同じデータを使用して、異なる学習ポリシーに基づいてトレーニングされた 2 つのモデルは、動作が異なります。

### <a name="importing-and-exporting-learning-policies"></a>学習ポリシーのインポートとエクスポート

学習ポリシーのファイルは、Azure portal からインポートおよびエクスポートできます。 これにより、将来的な参照と監査のために、既存のポリシーをソース コード管理で成果物として保存、テスト、置換、アーカイブできるようになります。

### <a name="learning-policy-settings"></a>学習ポリシーの設定

**学習ポリシー**の設定は、変更が想定されていません。 Personalizer にどのような影響があるかを把握している場合にのみ、設定を変更してください。 この知識のない状態で設定を変更すると、Personalizer モデルの無効化などの副作用が発生します。

### <a name="comparing-effectiveness-of-learning-policies"></a>学習ポリシーの効果を比較する

[オフライン評価](concepts-offline-evaluation.md)を実施することで、Personalizer ログの過去のデータに対してさまざまな学習ポリシーを適用した場合の差を比較できます。

現在の学習ポリシーと比較するには、[独自の学習ポリシーをアップロード](how-to-offline-evaluation.md)します。

### <a name="discovery-of-optimized-learning-policies"></a>最適化された学習ポリシーの特定

Personalizer は、[オフライン評価](how-to-offline-evaluation.md)の実施時に、より最適化された学習ポリシーを作成できます。 より最適化された学習ポリシーとは、オフライン評価においてより良い報酬を獲得したもののことです。そのポリシーを Personalizer でオンラインで使用すると、より良い結果が得られます。

最適化された学習ポリシーが作成されたら、Personalizer に直接適用して現在のポリシーの代わりに使用できるほか、今後の評価のために現在のポリシーを保存しておいて、破棄、保存、適用の判断は将来行うこともできます。