---
title: '回帰: 価格を予測する'
titleSuffix: Azure Machine Learning service
description: コードを 1 行も書くことなく、自動車の価格を予測する機械学習モデルを構築する方法について説明します。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: article
author: xiaoharper
ms.author: zhanxia
ms.reviewer: sgilley
ms.date: 05/10/2019
ms.openlocfilehash: ddc7ae10581075127f72dd020c59cf28bbfc9ae2
ms.sourcegitcommit: f10ae7078e477531af5b61a7fe64ab0e389830e8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/05/2019
ms.locfileid: "67606134"
---
# <a name="sample-1---regression-predict-price"></a>サンプル 1 - 回帰: 価格を予測する

コードを 1 行も書くことなく、ビジュアル インターフェイスを使用して、機械学習の回帰モデルを構築する方法について説明します。

この実験では、製造元、モデル、馬力、サイズなどの技術的な特徴に基づいて自動車の価格を予測する**デシジョン フォレスト リグレッサー**をトレーニングします。 なぜなら、"いくらにするか" という質問に答えようとしているからです。 これは回帰問題と呼ばれます。 ただし、この実験の同じ基本的な手順を適用して、回帰、分類、クラスタリングなど、あらゆる種類の機械学習問題に対処することができます。

トレーニング機械学習モデルの基本的な手順は次のとおりです。

1. データを取得する
1. データを前処理する
1. モデルをトレーニングする
1. モデルを評価する

これから取り組む実験の最終的な完成したグラフを次に示します。 ご自身で同様の決定を下すことができるように、ここではすべてのモジュールの理論的な根拠を提示します。

![実験のグラフ](media/ui-sample-regression-predict-automobile-price-basic/overall-graph.png)

## <a name="prerequisites"></a>前提条件

[!INCLUDE [aml-ui-prereq](../../../includes/aml-ui-prereq.md)]

4. サンプル 1 実験の **[Open]\(開く\)** ボタンを選択します。

    ![実験を開く](media/ui-sample-regression-predict-automobile-price-basic/open-sample1.png)

## <a name="get-the-data"></a>データを取得する

この実験では、UCI Machine Learning Repository から取得された **Automobile price data (Raw)** データセットを使用します。 このデータセットには、メーカー、モデル、価格、車両特徴 (シリンダー数など)、MPG、保険リスク スコアなど、自動車に関する情報を含む 26 列が含まれています。 この実験の目的は、自動車の価格を予測することです。

## <a name="pre-process-the-data"></a>データを前処理する

主なデータ準備タスクには、データのクリーニング、統合、変換、削減、および離散化または量子化が含まれます。 ビジュアル インターフェイスには、左側のパネルの **[Data Transformation]\(データ変換\)** グループに、これらの操作およびその他のデータ前処理タスクを実行するためのモジュールがあります。

**データセット内の列の選択**モジュールを使用して、欠損値が多い正規化された損失を除外します。 次に、 **[Clean Missing Data]\(欠損データのクリーニング\)** を使用して、欠損値がある行を削除します。 これにより、クリーンなトレーニング データセットを作成できます。

![データの前処理](./media/ui-sample-regression-predict-automobile-price-basic/data-processing.png)

## <a name="train-the-model"></a>モデルをトレーニングする

機械学習の問題はさまざまです。 一般的な機械学習タスクには、分類、クラスタリング、回帰、推奨システムなどがあり、それぞれに異なるアルゴリズムが必要になる場合があります。 アルゴリズムの選択は、多くの場合、ユース ケースの要件によって異なります。 アルゴリズムを選択したら、より正確なモデルをトレーニングするためにそのパラメーターを調整する必要があります。 次に、精度、明瞭度、効率性などのメトリックに基づいて、すべてのモデルを評価する必要があります。

この実験の目的は自動車の価格を予測することであり、またラベル列 (価格) には実数が含まれるため、回帰モデルを選択することをお勧めします。 特徴の数が比較的少なく (100 未満)、これらの特徴が疎でないことを考慮すると、決定境界は非線形である可能性があります。 そのため、この実験では**デシジョン フォレスト回帰**を使用します。

**データの分割**モジュールを使用して、トレーニング データセットが元のデータの 70% を含み、テスト データセットが元のデータの 30% を含むように、入力データをランダムに分割します。

## <a name="test-evaluate-and-compare"></a>テスト、評価、比較を行う

 モデルの評価をより客観的に行うために、データセットを分割し、異なるデータセットを使用してモデルをトレーニングおよびテストします。

モデルのトレーニングが終わったら、**モデルのスコア付け**モジュールと**モデルの評価**モジュールを使用して予測結果を生成し、モデルを評価します。

**モデルのスコア付け**は、トレーニングされたモデルを使用してテスト データセットの予測を生成します。 結果を確認するには、 **[Score Model]\(モデルのスコア付け\)** の出力ポートを選択し、 **[Visualize]\(可視化\)** を選択します。

![スコア付けの結果](./media/ui-sample-regression-predict-automobile-price-basic/score-result.png)

次に、そのスコアを**モデルの評価**モジュールに渡して評価メトリックを生成します。 結果を確認するには、 **[Evaluate Model]\(モデルの評価\)** の出力ポートを選択し、 **[Visualize]\(可視化\)** を選択します。

![評価結果](./media/ui-sample-regression-predict-automobile-price-basic/evaluate-result.png)

## <a name="clean-up-resources"></a>リソースのクリーンアップ

[!INCLUDE [aml-ui-cleanup](../../../includes/aml-ui-cleanup.md)]

## <a name="next-steps"></a>次の手順

ビジュアル インターフェイスで利用できるその他のサンプルを確認します。

- [サンプル 2 - 回帰: 自動車の価格予測のためのアルゴリズムを比較する](ui-sample-regression-predict-automobile-price-compare-algorithms.md)
- [サンプル 3 - 分類: 信用リスクを予測する](ui-sample-classification-predict-credit-risk-basic.md)
- [サンプル 4 - 分類: 信用リスクを予測する (費用重視)](ui-sample-classification-predict-credit-risk-cost-sensitive.md)
- [サンプル 5 - 分類:顧客離れを予測する](ui-sample-classification-predict-churn.md)
- [サンプル 6 - 分類:フライトの遅延を予測する](ui-sample-classification-predict-flight-delay.md)