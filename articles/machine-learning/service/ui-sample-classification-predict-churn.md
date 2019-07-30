---
title: '分類: 顧客離れ、強い欲求、アップセルを予測する '
titleSuffix: Azure Machine Learning service
description: このビジュアル インターフェイス サンプルの実験では、カスタマー リレーションシップ マネジメント (CRM) の一般的なタスクである顧客離れのバイナリ分類器予測を示します。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: article
author: xiaoharper
ms.author: zhanxia
ms.reviewer: sgilley
ms.date: 05/10/2019
ms.openlocfilehash: 7d10d996febd0e31c9085bf5cb82324cce101c80
ms.sourcegitcommit: f10ae7078e477531af5b61a7fe64ab0e389830e8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/05/2019
ms.locfileid: "67606152"
---
# <a name="sample-5---classification-predict-churn-appetency-and-up-selling"></a>サンプル 5 - 分類: 顧客離れ、強い欲求、アップセルを予測する 

コードを 1 行も書くことなく、ビジュアル インターフェイスを使用して、複雑な機械学習実験を構築する方法について説明します。

この実験では、顧客関係管理 (CRM) システムの一般的なタスクである顧客離れ、強い欲求、アップセルを予測するために、3 つの **2 クラス ブースト デシジョン ツリー**分類子をトレーニングします。 データ値とラベルは複数のデータ ソースに分割され、顧客情報を匿名化するためにスクランブルがかけられますが、それでもビジュアル インターフェイスを使用し、データ セットを組み合わせ、スクランブルがかけられた値を使用してモデルをトレーニングすることができます。

なぜなら、"どれにするか" という質問に答えようとしているからです。 これは分類問題と呼ばれます。 ただし、この実験の同じ手順を適用して、回帰、分類、クラスタリングなど、あらゆる種類の機械学習問題に対処することができます。

この実験の完成したグラフを次に示します。

![実験グラフ](./media/ui-sample-classification-predict-churn/experiment-graph.png)

## <a name="prerequisites"></a>前提条件

[!INCLUDE [aml-ui-prereq](../../../includes/aml-ui-prereq.md)]

4. サンプル 5 実験の **[Open]\(開く\)** ボタンを選択します。

    ![実験を開く](media/ui-sample-classification-predict-churn/open-sample5.png)

## <a name="data"></a>データ

この実験で使用するデータは、KDD Cup 2009 のものです。 データセットには、50,000 行と 230 の特徴列が含まれます。 タスクは、これらの特徴を使用する顧客の顧客離れ、強い欲求、アップセルを予測することです。 データとタスクについて詳細については、[KDD の Web サイト](https://www.kdd.org/kdd-cup/view/kdd-cup-2009)を参照してください。

## <a name="experiment-summary"></a>実験の概要

このビジュアル インターフェイス サンプルの実験では、カスタマー リレーションシップ マネジメント (CRM) の一般的なタスクである顧客離れ、強い欲求、アップセルのバイナリ分類器予測を示します。

最初に、簡単なデータ処理をいくつか行います。

- 生のデータセットには、欠損値が多く含まれています。 **見つからないデータのクリーンアップ** モジュールを使用して、欠損値を 0 に置き換えます。

    ![データセットをクリーンアップする](./media/ui-sample-classification-predict-churn/cleaned-dataset.png)

- 特徴と、それに対応する顧客離れ、強い欲求、アップセルのラベルは、異なるデータセットに含まれます。 **列の追加**モジュールを使用して、ラベル列を特徴列に追加します。 最初の列 **Col1** はラベル列です。 残りの列 **Var1**、**Var2** などは、特徴列です。

    ![列のデータセットを追加する](./media/ui-sample-classification-predict-churn/added-column1.png)

- **データの分割**モジュールを使用して、データセットをトレーニング セットとテスト セットに分割します。

    その後、ブースト デシジョン ツリー バイナリ分類器と既定のパラメーターを使用して、予測モデルを構築します。 タスクごとに 1 つのモデル、つまりアップセル、強い欲求、顧客離れの予測ごとに 1 つのモデルを構築します。

## <a name="results"></a>結果

**モデルの評価**モジュールの出力を視覚化し、テスト セットでのモデルのパフォーマンスを確認します。 アップセル タスクについては、このモデルの方がランダム モデルより優れていることが ROC 曲線で示されています。 曲線下面積 (AUC) は 0.857 です。 0\.5 のしきい値では、精度は 0.7、再現率は 0.463、F1 スコアは 0.545 です。

![結果を評価](./media/ui-sample-classification-predict-churn/evaluate-result.png)

 **[しきい値]** スライダーを動かして、バイナリ分類器タスクのメトリックの変化を確認できます。

## <a name="clean-up-resources"></a>リソースのクリーンアップ

[!INCLUDE [aml-ui-cleanup](../../../includes/aml-ui-cleanup.md)]

## <a name="next-steps"></a>次の手順

ビジュアル インターフェイスで利用できるその他のサンプルを確認します。

- [サンプル 1 - 回帰: 自動車の価格を予測する](ui-sample-regression-predict-automobile-price-basic.md)
- [サンプル 2 - 回帰: 自動車の価格予測のためのアルゴリズムを比較する](ui-sample-regression-predict-automobile-price-compare-algorithms.md)
- [サンプル 3 - 分類: 信用リスクを予測する](ui-sample-classification-predict-credit-risk-basic.md)
- [サンプル 4 - 分類: 信用リスクを予測する (費用重視)](ui-sample-classification-predict-credit-risk-cost-sensitive.md)
- [サンプル 6 - 分類:フライトの遅延を予測する](ui-sample-classification-predict-flight-delay.md)