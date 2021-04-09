---
title: 学習ポリシー - Personalizer
description: 学習の設定では、モデル トレーニングの "*ハイパーパラメーター*" が決定されます。 同じデータの 2 つのモデルが、異なる学習設定でトレーニングされると、最終的には別のものになります。
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: conceptual
ms.date: 02/20/2020
ms.openlocfilehash: 1d2038e3796df843736eb80f7e5645f4141c82f4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "91253634"
---
# <a name="learning-policy-and-settings"></a>学習ポリシーと設定

学習の設定では、モデル トレーニングの "*ハイパーパラメーター*" が決定されます。 同じデータの 2 つのモデルが、異なる学習設定でトレーニングされると、最終的には別のものになります。

[学習ポリシーと設定](how-to-settings.md#configure-rewards-for-the-feedback-loop)は、Azure portal で Personalizer リソースに対して設定されます。

## <a name="import-and-export-learning-policies"></a>学習ポリシーのインポートとエクスポート

学習ポリシーのファイルは、Azure portal からインポートおよびエクスポートできます。 この方法を使用して、将来的な参照と監査のために、既存のポリシーをソース コード管理で成果物として保存、テスト、置換、アーカイブします。

Azure portal で Personalizer リソースの学習ポリシーをインポートおよびエクスポートする[方法](how-to-manage-model.md#import-a-new-learning-policy)をご覧ください。

## <a name="understand-learning-policy-settings"></a>学習ポリシーの設定について

学習ポリシーの設定は、変更されることが想定されていません。 Personalizer に対する影響がわかっている場合にのみ、設定を変更してください。 これがわかっていないと、Personalizer モデルの無効化など、問題が発生する可能性があります。

Personalizer は、[vowpalwabbit](https://github.com/VowpalWabbit) を使用してイベントのトレーニングとスコア付けを行います。 vowpalwabbit を使用して学習設定を編集する方法については、[vowpalwabbit のドキュメント](https://github.com/VowpalWabbit/vowpal_wabbit/wiki/Command-line-arguments)を参照してください。 正しいコマンド ライン引数を指定したら、以下の形式 (arguments プロパティの値は、目的のコマンドに置き換えてください) でコマンドをファイルに保存します。Azure portal から Personalizer リソースの **[モデルと学習設定]** ペインで、そのファイルをアップロードし、学習設定をインポートしてください。

次の `.json` は、学習ポリシーの例です。

```json
{
  "name": "new learning settings",
  "arguments": " --cb_explore_adf --epsilon 0.2 --power_t 0 -l 0.001 --cb_type mtr -q ::"
}
```

## <a name="compare-learning-policies"></a>学習ポリシーを比較する

[オフライン評価](concepts-offline-evaluation.md)を実施することで、Personalizer ログの過去のデータに対してさまざまな学習ポリシーを適用した場合の差を比較できます。

現在の学習ポリシーと比較するには、[独自の学習ポリシーをアップロード](how-to-manage-model.md)します。

## <a name="optimize-learning-policies"></a>学習ポリシーを最適化する

Personalizer では、[オフライン評価](how-to-offline-evaluation.md)に最適化された学習ポリシーを作成できます。 オフライン評価での報酬がよくなるように最適化された学習ポリシーほど、Personalizer でオンライン使用したときの結果がよくなります。

学習ポリシーを最適化した後は、Personalizer にそれを直接適用し、現在のポリシーをすぐに置き換えることができます。 または、最適化されたポリシーをさらに評価するために保存しておき、後でそのポリシーを破棄、保存、適用するかどうかを決定することもできます。

## <a name="next-steps"></a>次のステップ

* [アクティブおよび非アクティブなイベント](concept-active-inactive-events.md)について学習します。
