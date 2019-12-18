---
title: 自動 ML 結果の概要
titleSuffix: Azure Machine Learning
description: 自動機械学習の実行ごとに、グラフとメトリックを確認し、把握する方法について説明します。
services: machine-learning
author: RachelKellam
ms.author: rakellam
ms.reviewer: sgilley
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.date: 12/05/2019
ms.openlocfilehash: 81f17de7627658b756edd19438a80fb32add859d
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/10/2019
ms.locfileid: "74974089"
---
# <a name="understand-automated-machine-learning-results"></a>自動化機械学習の結果の概要
[!INCLUDE [applies-to-skus](../../../includes/aml-applies-to-basic-enterprise-sku.md)]

この記事では、自動機械学習の実行ごとに、グラフとメトリックを確認し、把握する方法について説明します。 

各項目の詳細情報
+ [分類モデルのメトリック、グラフ、曲線](#classification)
+ [回帰モデルのメトリック、チャート、グラフ](#regression)
+ [モデルの解釈可能性と機能の重要性](#explain-model)

## <a name="prerequisites"></a>前提条件

* Azure サブスクリプション。 Azure サブスクリプションをお持ちでない場合は、開始する前に無料アカウントを作成してください。 [無料版または有料版の Azure Machine Learning](https://aka.ms/AMLFree) を今すぐお試しください。

* SDK を使用するか Azure Machine Learning Studio (プレビュー) で、自動機械学習の実行用の実験を作成します。

    * SDK を使用し、[分類モデル](how-to-auto-train-remote.md)または[回帰モデル](tutorial-auto-train-models.md)を作成する
    * [Azure Machine Learning Studio](how-to-create-portal-experiments.md) を使用して、適切なデータをアップロードすることで分類または回帰モデルを作成します。

## <a name="view-the-run"></a>実行を確認する

自動機械学習の実験を実行した後、機械学習ワークスペースで実行の履歴を確認できます。 

1. ワークスペースに移動します。

1. ワークスペースの左側のパネルで、 **[実験]** を選択します。

   ![実験メニューのスクリーンショット](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-experiment-menu.png)

1. 実験の一覧で、探索する項目を選択します。

   [![実験リスト](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-experiment-list.png)](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-experiment-list-expanded.png)

1. 一番下のテーブルで、 **[実行]** を選択します。

   [![実験の実行](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-experiment-run.png)](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-experiment-run-expanded.png))

1. [モデル] で、詳しく調べるモデルの **[アルゴリズム名]** を選択します。

   [![実験モデル](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-experiment-model.png)](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-experiment-model-expanded.png)

`RunDetails`[Jupyter ウィジェット](https://docs.microsoft.com/python/api/azureml-widgets/azureml.widgets?view=azure-ml-py)を使用した場合も、実行中に同じ結果が表示されます。

## <a name="classification"></a> 分類の結果

Azure Machine Learning の自動機械学習機能を使用して構築するすべての分類モデルについて、次のようなメトリックとグラフを使用できます

+ [メトリック](#classification-metrics)
+ [混同行列](#confusion-matrix)
+ [精度/再現率グラフ](#precision-recall-chart)
+ [受信者操作特性 (ROC)](#roc)
+ [リフト曲線](#lift-curve)
+ [ゲイン曲線](#gains-curve)
+ [調整プロット](#calibration-plot)

### <a name="classification-metrics"></a>分類メトリック

分類タスクの実行イテレーションごとに、次のメトリックが保存されます。

|メトリック|説明|計算|追加のパラメーター
--|--|--|--|
AUC_Macro| AUC は、受信者操作特徴曲線の下の領域です。 Macro は、クラスごとの AUC の算術平均です。  | [計算](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.roc_auc_score.html) | average="macro"|
AUC_Micro| AUC は、受信者操作特徴曲線の下の領域です。 Micro は、各クラスの真陽性と偽陽性を組み合わせることでグローバルに計算されます。| [計算](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.roc_auc_score.html) | average="micro"|
AUC_Weighted  | AUC は、受信者操作特徴曲線の下の領域です。 重み付けは各クラスのスコアの算術平均で、各クラス内の true インスタンスの数によって重み付けされます。| [計算](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.roc_auc_score.html)|average="weighted"
accuracy|精度は、true ラベルと正確に一致する予測ラベルの割合です。 |[計算](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.accuracy_score.html) |なし|
average_precision_score_macro|平均適合率は、各しきい値で達成した適合率の加重平均として適合率-再現率曲線をまとめたもので、前のしきい値より増加した再現率を重みとして使用します。 Macro は、各クラスの平均適合率スコアの算術平均です。|[計算](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.average_precision_score.html)|average="macro"|
average_precision_score_micro|平均適合率は、各しきい値で達成した適合率の加重平均として適合率-再現率曲線をまとめたもので、前のしきい値より増加した再現率を重みとして使用します。 Micro は、各カットオフの真陽性と偽陽性を組み合わせることでグローバルに計算されます。|[計算](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.average_precision_score.html)|average="micro"|
average_precision_score_weighted|平均適合率は、各しきい値で達成した適合率の加重平均として適合率-再現率曲線をまとめたもので、前のしきい値より増加した再現率を重みとして使用します。 重み付けは各クラスの平均適合率スコアの算術平均で、各クラス内の true インスタンスの数によって重み付けされます。|[計算](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.average_precision_score.html)|average="weighted"|
balanced_accuracy|バランスの取れた精度は、各クラスの再現率の算術平均です。|[計算](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.recall_score.html)|average="macro"|
f1_score_macro|F1 スコアは、適合率と再現率の調和平均です。 Macro は、各クラスの F1 スコアの算術平均です。|[計算](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.f1_score.html)|average="macro"|
f1_score_micro|F1 スコアは、適合率と再現率の調和平均です。 Micro は、真陽性、偽陰性、偽陽性の合計をカウントすることによって、グローバルに計算されます。|[計算](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.f1_score.html)|average="micro"|
f1_score_weighted|F1 スコアは、適合率と再現率の調和平均です。 各クラスの F1 スコアのクラスごとの頻度の加重平均|[計算](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.f1_score.html)|average="weighted"|
log_loss|これは、(多項) ロジスティック回帰とその拡張機能 (ニューラル ネットワークなど) で使用される損失関数で、確率的分類法の予測を前提として、true ラベルの負の対数尤度として定義されます。 {0,1} の true ラベル yt と yt = 1 の推定確率 yp の 1 つの例として、log loss は -log P(yt&#124;yp) = -(yt log(yp) + (1 - yt) log(1 - yp))。|[計算](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.log_loss.html)|なし|
norm_macro_recall|正規化されたマクロ再現率は、ランダムなパフォーマンスでのスコアが 0、最適なパフォーマンスでのスコアが 1 になるように正規化されたマクロ再現率です。 これは、norm_macro_recall := (recall_score_macro - R)/(1 - R) で実現されます。R はランダム予測の recall_score_macro で期待される値 (つまり、二項分類では R=0.5、C クラス分類問題では R=(1/C))。|[計算](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.recall_score.html)|average = "macro" |
precision_score_macro|適合率は、正しくラベル付けされた要素のうち陽性として予測された要素の割合です。 Macro は、各クラスの適合率の算術平均です。|[計算](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.precision_score.html)|average="macro"|
precision_score_micro|適合率は、正しくラベル付けされた要素のうち陽性として予測された要素の割合です。 Micro は、真陽性と偽陽性の合計をカウントすることによって、グローバルに計算されます。|[計算](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.precision_score.html)|average="micro"|
precision_score_weighted|適合率は、正しくラベル付けされた要素のうち陽性として予測された要素の割合です。 重み付けは各クラスの適合率の算術平均で、各クラス内の true インスタンスの数によって重み付けされます。|[計算](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.precision_score.html)|average="weighted"|
recall_score_macro|再現率は、特定のクラスのうち、正しくラベル付けされた要素の割合です。 Macro は、各クラスの再現率の算術平均です。|[計算](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.recall_score.html)|average="macro"|
recall_score_micro|再現率は、特定のクラスのうち、正しくラベル付けされた要素の割合です。 Micro は、真陽性、偽陰性、偽陽性の合計をカウントすることによって、グローバルに計算されます|[計算](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.recall_score.html)|average="micro"|
recall_score_weighted|再現率は、特定のクラスのうち、正しくラベル付けされた要素の割合です。 重み付けは各クラスの再現率の算術平均で、各クラス内の true インスタンスの数によって重み付けされます。|[計算](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.recall_score.html)|average="weighted"|
weighted_accuracy|重み付けされた精度は、それぞれの例に対して指定された重みが、その例の true クラス内の true インスタンスの割合に一致する精度です。|[計算](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.accuracy_score.html)|sample_weight は、ターゲット内の各要素に対して、そのクラスの割合が等しいベクターです|
<a name="confusion-matrix"></a>
### <a name="confusion-matrix"></a>混同行列
#### <a name="what-is-a-confusion-matrix"></a>混同行列とは何ですか？
混同行列は、分類モデルのパフォーマンスの記述に使用します。 各行には、true のインスタンス、またはデータセット内の実際のクラスが表示され、各列には、モデルによって予測されたクラスのインスタンスが表示されます。 

#### <a name="what-does-automated-ml-do-with-the-confusion-matrix"></a>自動 ML は、混同行列で何をしますか？
分類問題の場合、Azure Machine Learning では作成された各モデルに対して混同行列が自動的に提供されます。 各混同行列では、自動 ML は、予測された各ラベル（列）の頻度を true ラベル（行）と比較して表示します。 色が濃いほど、行列の特定の部分のカウント数が大きくなります。 

#### <a name="what-does-a-good-model-look-like"></a>適切なモデルはどのようなものでしょうか？
データセットの実際の値と、モデルによって指定された予測値とを比較しています。 このため、モデルの値のほとんどが対角線に沿っている場合には、機械学習モデルの精度が高くなります。これは、モデルでは、正しい値が予測されていることを意味します。 モデルにクラス不均衡がある場合、混同行列は、バイアス モデルを検出するのに役立ちます。

##### <a name="example-1-a-classification-model-with-poor-accuracy"></a>例 1:精度のよくない分類モデル
![精度のよくない分類モデル](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-confusion-matrix1.png)

##### <a name="example-2-a-classification-model-with-high-accuracy"></a>例 2:精度の高い分類モデル 
![精度の高い分類モデル](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-confusion-matrix2.png)

##### <a name="example-3-a-classification-model-with-high-accuracy-and-high-bias-in-model-predictions"></a>例 3:モデル予測の精度が高く、高バイアスの分類モデル
![モデル予測の精度が高く、高バイアスの分類モデル](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-biased-model.png)

<a name="precision-recall-chart"></a>
### <a name="precision-recall-chart"></a>精度/再現率グラフ
#### <a name="what-is-a-precision-recall-chart"></a>精密/再現率グラフとは何ですか？
精度/再現率曲線は、モデルの精度と再現率の関係を示します。 精度という用語は、モデルがすべてのインスタンスに正しくラベルを付ける能力を表します。 再現率は、分類子が特定のラベルのすべてのインスタンスを見つける能力を表します。

#### <a name="what-does-automated-ml-do-with-the-precision-recall-chart"></a>自動 ML では、精度/再現率グラフを使用するとどうなりますか？

このグラフでは、各モデルの精度/再現率曲線を比較して、特定のビジネスの問題に対して精度と再現率の間の関係が許容できるモデルを特定できます。 このグラフでは、マクロ平均精度/再現率、ミクロ平均精度/再現率、およびモデルのすべてのクラスに関連付けられた精度/再現率が示されます。 

マクロ平均では、各クラスとは無関係にメトリックを計算して、平均値を計算し、すべてのクラスを同等に扱います。 ただし、マイクロ平均は、すべてのクラスのコントリビューションを集計して平均を算出します。 データセットにクラスの不均衡が存在する場合、マイクロ平均が推奨されます。

#### <a name="what-does-a-good-model-look-like"></a>適切なモデルはどのようなものでしょうか？
ビジネス上の問題の目標によっては、理想的な精度の高い再現率曲線が異なる可能性があります。 いくつかの例を、次に示す

##### <a name="example-1-a-classification-model-with-low-precision-and-low-recall"></a>例 1:低精度で再現率が低い分類モデル
![低精度で再現率が低い分類モデル](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-precision-recall1.png)

##### <a name="example-2-a-classification-model-with-100-precision-and-100-recall"></a>例 2:最大 100% の精度と最大 100% の再現率を含む分類モデル 
![高精度で高再現率の分類モデル](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-precision-recall2.png)
<a name="roc"></a>
### <a name="roc-chart"></a>ROC グラフ

#### <a name="what-is-a-roc-chart"></a>ROC グラフとは何ですか？
受信者操作特性 (ROC) は、特定のモデルについて正しく分類されたラベルと間違って分類されたラベルを対比したプロットです。 ROC 曲線は、誤検知のラベルが示されないため、高バイアスのデータセットでモデルをトレーニングするときは、あまり役に立たないことがあります。

#### <a name="what-does-automated-ml-do-with-the-roc-chart"></a>自動 ML は、ROC グラフで何をしますか？
自動 ML は、マクロ平均精度/再現率、マイクロ平均精度/再現率、およびモデルのすべてのクラスに関連付けられた精度/再現率を生成します。 

マクロ平均では、各クラスとは無関係にメトリックを計算して、平均値を計算し、すべてのクラスを同等に扱います。 ただし、マイクロ平均は、すべてのクラスのコントリビューションを集計して平均を算出します。 データセットにクラスの不均衡が存在する場合、マイクロ平均が推奨されます。

#### <a name="what-does-a-good-model-look-like"></a>適切なモデルはどのようなものでしょうか？
理想的なモデルは、 100％ に近い真の陽性率を示し、 0％ に近い誤検出率を示します。 

##### <a name="example-1-a-classification-model-with-low-true-labels-and-high-false-labels"></a>例 1:低い true ラベルと高い false ラベルを持つ分類モデル
![低い true ラベルと高い false ラベルを持つ分類モデル](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-roc-1.png)

##### <a name="example-2-a-classification-model-with-high-true-labels-and-low-false-labels"></a>例 2:高い true ラベルと低い false ラベルを持つ分類モデル
![高い true ラベルと低い false ラベルを持つ分類モデル](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-roc-2.png)
<a name="lift-curve"></a>
### <a name="lift-chart"></a>リフト チャート
#### <a name="what-is-a-lift-chart"></a>リフト チャートとは何ですか？
リフト チャートは、分類モデルのパフォーマンスの評価に使用されます。 精度の観点では、モデルを使用しない場合と比較して、生成されたモデルでどの程度の改善が期待されるかを示します。
#### <a name="what-does-automated-ml-do-with-the-lift-chart"></a>自動 ML は、リフト チャートで何をしますか？
Azure Machine Learning で自動的に作成されたモデルのリフトとベースラインを比較して、その特定のモデルでの値のゲインを確認できます。
#### <a name="what-does-a-good-model-look-like"></a>適切なモデルはどのようなものでしょうか？

##### <a name="example-1-a-classification-model-that-does-worse-than-a-random-selection-model"></a>例 1:ランダム選択モデルよりも悪い分類モデル
![ランダム選択モデルよりも悪い分類モデル](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-lift-curve1.png)
##### <a name="example-2-a-classification-model-that-performs-better-than-a-random-selection-model"></a>例 2:ランダムに選択されたモデルよりパフォーマンスがよい分類モデル
![より良いパフォーマンスの分類モデル](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-lift-curve2.png)
<a name="gains-curve"></a>
### <a name="gains-chart"></a>ゲイン チャート
#### <a name="what-is-a-gains-chart"></a>ゲイン チャートとは何ですか？

ゲイン チャートでは、データの各部分によって分類モデルのパフォーマンスが評価されます。 データ セットの各パーセンタイルについて、ランダムに選択されたモデルと比較してパフォーマンスがどの程度向上すると予想されるかがわかります。

#### <a name="what-does-automated-ml-do-with-the-gains-chart"></a>自動 ML は、ゲイン チャートで何をしますか？
モデルからの望ましいゲインに対応するパーセンテージを使用して分類カットオフを選択するには、累積ゲイン チャートを使用します。 この情報では、付随するリフト チャートの結果を調べる別の方法が提供されます。

#### <a name="what-does-a-good-model-look-like"></a>適切なモデルはどのようなものでしょうか？
##### <a name="example-1-a-classification-model-with-minimal-gain"></a>例 1:最小ゲイン チャートの分類モデル
![最小ゲイン チャートの分類モデル](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-gains-curve1.png)

##### <a name="example-2-a-classification-model-with-significant-gain"></a>例 2:大きなゲイン チャートの分類モデル
![大きなゲイン チャートの分類モデル](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-gains-curve2.png)
<a name="calibration-plot"></a>
### <a name="calibration-chart"></a>調整グラフ

#### <a name="what-is-a-calibration-chart"></a>調整グラフとは何ですか？
調整プロットは、予測モデルの信頼度を示すために使用されます。 これを行うため、予測される確率と実際の確率の間の関係が示されます。"確率" は、特定のインスタンスがいくつかのラベルに属する可能性を表します。
#### <a name="what-does-automated-ml-do-with-the-calibration-chart"></a>自動 ML は、調整グラフで何をしますか？
すべての分類の問題について、マイクロ平均、マクロ平均、および特定の予測モデルの各クラスに対する調整ラインを確認できます。

マクロ平均では、各クラスとは無関係にメトリックを計算して、平均値を計算し、すべてのクラスを同等に扱います。 ただし、マイクロ平均は、すべてのクラスのコントリビューションを集計して平均を算出します。 
#### <a name="what-does-a-good-model-look-like"></a>適切なモデルはどのようなものでしょうか？
 適切に調整されたモデルは、y=x の線と一致し、予測の信頼性は妥当です。 過剰信頼モデルは y=0 の線と一致し、予測確率はありますが、実際の確率はありません。 


##### <a name="example-1-a-well-calibrated-model"></a>例 1:適切に調整されたモデル
![ より適切に調整されたモデル](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-calib-curve1.png)

##### <a name="example-2-an-over-confident-model"></a>例 2:過剰信頼モデル
![過剰信頼モデル](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-calib-curve2.png)

## <a name="regression"></a> 回帰結果

Azure Machine Learning の自動機械学習機能を使用して構築するすべての回帰モデルについて、次のようなメトリックとグラフを使用できます

+ [メトリック](#reg-metrics)
+ [予測とTrue](#pvt)
+ [残差のヒストグラム](#histo)


### <a name="reg-metrics"></a> 回帰メトリック

回帰タスクまたは予測タスクの実行イテレーションごとに、次のメトリックが保存されます。

|メトリック|説明|計算|追加のパラメーター
--|--|--|--|
explained_variance|説明分散は、ある数学的モデルが、提供されたデータ セットのバリエーションに占める割合です。 エラーの分散に対する元データの分散の減少の割合です。 エラーの平均が 0 の場合は、説明分散と同じです。|[計算](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.explained_variance_score.html)|なし|
r2_score|R2 は、平均を出力するベースライン モデルと比較した、決定係数または二乗誤差の減少の割合です。 |[計算](https://scikit-learn.org/0.16/modules/generated/sklearn.metrics.r2_score.html)|なし|
spearman_correlation|スピアマンの相関は、2 つのデータセット間の関係の単調性に対するノンパラメトリック測定です。 ピアソンの相関とは異なり、スピアマンの相関は両方のデータセットが正規分布していることを想定しません。 他の相関係数と同様に、-1 と +1 の間で変化し、0 は相関関係がないことを示します。 相関係数が -1 または +1 の場合は、完全に単調な関係であることを示します。 正の相関関係は、x、y ともに増加することを示します。 負の相関関係は、x が増加すると y は減少することを示します。|[計算](https://docs.scipy.org/doc/scipy-0.16.1/reference/generated/scipy.stats.spearmanr.html)|なし|
mean_absolute_error|平均絶対誤差は、ターゲットと予測の間における差異の絶対値について予期される値です|[計算](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.mean_absolute_error.html)|なし|
normalized_mean_absolute_error|正規化された平均絶対誤差は、データの範囲で除算した平均絶対誤差です|[計算](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.mean_absolute_error.html)|データの範囲で除算します|
median_absolute_error|中央絶対誤差は、ターゲットと予測の間におけるすべての絶対差の中央値です。 この損失は外れ値に対してロバストです。|[計算](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.median_absolute_error.html)|なし|
normalized_median_absolute_error|正規化された中央絶対誤差は、データの範囲で除算した中央絶対誤差です|[計算](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.median_absolute_error.html)|データの範囲で除算します|
root_mean_squared_error|平均平方二乗誤差は、ターゲットと予測の間における予期される二乗誤差の平方根です|[計算](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.mean_squared_error.html)|なし|
normalized_root_mean_squared_error|正規化された平均平方二乗誤差は、データの範囲で除算した平均平方二乗誤差です|[計算](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.mean_squared_error.html)|データの範囲で除算します|
root_mean_squared_log_error|対数平均平方二乗誤差は、予期される対数二乗誤差の平方根です|[計算](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.mean_squared_log_error.html)|なし|
normalized_root_mean_squared_log_error|正規化された対数平均平方二乗誤差は、データの範囲で除算した対数平均平方二乗誤差です|[計算](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.mean_squared_log_error.html)|データの範囲で除算します|

### <a name="pvt"></a> 予測とtrue グラフ
#### <a name="what-is-a-predicted-vs-true-chart"></a>予測 VS とは何ですか。true グラフ？
予測とTrue では、回帰問題に対する予測値とそれに相関する True 値の間の関係が示されます。 このグラフを使用してモデルのパフォーマンスを測定できます。予測値が y=x の線に近いほど、予測モデルの精度が高いことを示します。

#### <a name="what-does-automated-ml-do-with-the-predicted-vs-true-chart"></a>自動 ML は、予測 VS で何をしますか。true グラフ？
各実行の後で、各回帰モデルの予測と True のグラフを表示できます。 データのプライバシーを保護するため、値はビンにまとめられており、各ビンのサイズはグラフ領域の下部に棒グラフとして示されます。 許容誤差を示す明るい網掛け領域で、予測モデルをモデルの理想値と比較できます。

#### <a name="what-does-a-good-model-look-like"></a>適切なモデルはどのようなものでしょうか？
##### <a name="example-1-a-classification-model-with-low-accuracy"></a>例 1:精度の低い分類モデル
![予測精度の低い回帰モデル](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-regression1.png)

##### <a name="example-2-a-regression-model-with-high-accuracy"></a>例 2:精度の高い回帰モデル 
[![予測精度が高い回帰モデル](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-regression2.png)](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-regression2-expanded.png)



### <a name="histo"></a>残差グラフのヒストグラム
#### <a name="what-is-a-residuals-chart"></a>残差グラフとは何ですか？
残差は、観察された y から予測された y を引いた値を表します。 低バイアスでの許容誤差を示すため、残差のヒストグラムは 0 を中心とするベル曲線として成形する必要があります。 
#### <a name="what-does-automated-ml-do-with-the-residuals-chart"></a>自動 ML は、残渣チャートで何をしますか？
自動 ML は、予測内のエラーの分布を示す残差グラフを自動的に提供します。
#### <a name="what-does-a-good-model-look-like"></a>適切なモデルはどのようなものでしょうか？
適切なモデルには、通常、ベル曲線またはゼロ付近の誤差があります。

##### <a name="example-1-a-regression-model-with-bias-in-its-errors"></a>例 1:エラーにバイアスを持つ回帰モデル
![エラーにバイアスを持つ SA 回帰モデル](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-regression3.png)

##### <a name="example-2-a-regression-model-with-more-even-distribution-of-errors"></a>例 2:エラーがより均等に配分される回帰モデル
![エラーがより均等に配分される回帰モデル](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-regression4.png)

## <a name="explain-model"></a> モデルの解釈可能性と機能の重要性
自動 ML は、実行に対する機械学習の解釈可能性ダッシュボードを提供します。
解釈可能性機能の有効化の詳細については、自動 ML 実験で解釈可能性を有効にする[方法](how-to-machine-learning-interpretability-automl.md)を参照してください。

## <a name="next-steps"></a>次の手順

+ Azure Machine Learning の[自動機械学習](concept-automated-ml.md)について学習します。
+ [自動機械学習モデルの説明](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/explain-model) のサンプル ノートブックを試してください。
