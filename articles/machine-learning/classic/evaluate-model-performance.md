---
title: 'ML Studio (classic): モデルの評価とクロス検証 - Azure'
description: Azure Machine Learning Studio (classic) でモデルのパフォーマンスを監視するために使用できるメトリックについて説明します。
services: machine-learning
ms.service: machine-learning
ms.subservice: studio-classic
ms.topic: how-to
author: likebupt
ms.author: keli19
ms.custom: seodec18, previous-author=heatherbshapiro, previous-ms.author=hshapiro
ms.date: 03/20/2017
ms.openlocfilehash: b2ca78d30659fce6e4246c81216cae94b404955e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "100520019"
---
# <a name="evaluate-model-performance-in-azure-machine-learning-studio-classic"></a>Azure Machine Learning Studio (classic) でモデルのパフォーマンスを評価する

**適用対象:** ![適用対象: ](../../../includes/media/aml-applies-to-skus/yes.png)Machine Learning Studio (classic)   ![適用対象外: ](../../../includes/media/aml-applies-to-skus/no.png)[Azure Machine Learning](../overview-what-is-machine-learning-studio.md#ml-studio-classic-vs-azure-machine-learning-studio)


この記事では、Azure Machine Learning Studio (classic) でモデルのパフォーマンスを監視するために使用できるメトリックについて説明します。  モデルのパフォーマンスの評価は、データ サイエンス プロセスの重要な段階の 1 つです。 その評価は、トレーニングしたモデルによるデータセットのスコア付け (予測) がどれほど成功したかを示す指標になります。 Azure Machine Learning Studio (クラシック) では、 
+ [モデルの評価][evaluate-model] 
+ [モデルのクロス検証][cross-validate-model]

これらのモジュールを使用すれば、機械学習と統計情報でよく使用されるさまざまなメトリックの観点からモデルのパフォーマンスを確認できます。

モデルの評価は、以下と一緒に検討する必要があります。
+ [アルゴリズムのパラメーターの最適化](algorithm-parameters-optimize.md)
+ [モデルの解釈可能性](interpret-model-results.md)

以下の 3 種類の学習のシナリオを取り上げます。 
* 回帰
* 二項分類 
* 多クラス分類


## <a name="evaluation-vs-cross-validation"></a>評価とクロス検証
評価とクロス検証は、モデルのパフォーマンスを測定する標準的な方法です。 どちらの場合も評価メトリックが生成されるので、そのメトリックを確認したり、他のモデルと比較したりできます。

[[モデルの評価]][evaluate-model] では、スコア付けされたデータセットが入力として 1 つ必要になります (2 つのモデルのパフォーマンスを比較する場合は 2 つ必要です)。 そのため、結果を評価する前に、[[モデルのトレーニング]][train-model] モジュールでモデルのトレーニングを実行し、[[モデルのスコア付け]][score-model] モジュールでデータセットの予測を作成しておく必要があります。 この評価は、スコア付けされたラベル/確率と実際のラベルに基づいて行われます。これらはすべて、[[モデルのスコア付け]][score-model] モジュールから出力されます。

あるいは、クロス検証を使用して、入力データの各サブセットに対して 10 分割のトレーニング/スコア付け/評価の操作を自動的に実行することもできます。 その場合、入力データは 10 分割され、1 つはテスト用、残りの 9 つはトレーニング用になります。 このプロセスが 10 回繰り返され、評価メトリックは平均化されます。 そのようにして、モデルが新しいデータセットにどの程度汎用化されるかを確認できます。 [[モデルのクロス検証]][cross-validate-model] モジュールでは、トレーニングをしていないモデルとラベルの付いたデータセットを取り込んで、10 回の処理のそれぞれの評価結果と平均値を出力します。

以下の各セクションでは、シンプルな回帰モデルと分類モデルを作成し、[[モデルの評価]][evaluate-model] モジュールと [[モデルのクロス検証]][cross-validate-model] モジュールを使用してそれぞれのパフォーマンスを評価します。

## <a name="evaluating-a-regression-model"></a>回帰モデルの評価
自動車の大きさ、馬力、エンジンの仕様などの特徴を利用して、価格を予測するとします。 これは、ターゲット変数 (*価格*) が連続数値になる典型的な回帰問題です。 自動車のさまざまな特徴の値に基づいて価格を予測する線形回帰モデルを作成できます。 この回帰モデルを使用して、トレーニングで使用したのと同じデータセットのスコア付けを行うことができます。 自動車の価格を予測したら、その予測と実際の価格の差異の平均値に基づいてモデルのパフォーマンスを評価できます。 その一例として、Machine Learning Studio (クラシック) の *[保存されたデータセット]* セクションにある **自動車価格データ (生データ) データセット** を使用します。

### <a name="creating-the-experiment"></a>実験の作成
Azure Machine Learning Studio (クラシック) で以下のモジュールをワークスペースに追加します。

* 自動車価格データ (生データ)
* [線形回帰][linear-regression]
* [モデルのトレーニング][train-model]
* [モデルのスコア付け][score-model]
* [モデルの評価][evaluate-model]

図 1 のようにポートを接続し、[[モデルのトレーニング]][train-model] モジュールのラベル列を *price* に設定します。

![回帰モデルの評価](./media/evaluate-model-performance/1.png)

図 1. 回帰モデルの評価。

### <a name="inspecting-the-evaluation-results"></a>評価結果の確認
実験を実行したら、[[モデルの評価]][evaluate-model] モジュールの出力ポートをクリックし、"*視覚化*" を選択して評価結果を確認できます。 回帰モデルで使用できる評価メトリックは、"*平均絶対誤差*"、"*二乗平均絶対誤差*"、"*相対絶対誤差*"、"*相対二乗誤差*"、"*決定係数*" です。

ここでは、予測の値と実際の値の差異のことを「誤差」といいます。 予測の値と実際の値の差が負の値になることもあるので、通常は、この差の絶対値または 2 乗が計算され、すべての事例の誤差が全体でどれほどの大きさになっているかを確認します。 誤差のメトリックでは、実際の値に対する予測の値の平均偏差に基づいて回帰モデルの予測パフォーマンスを測定します。 誤差の値が小さければ小さいほど、モデルの予測が正確だということになります。 全体の誤差のメトリックがゼロであれば、そのモデルはデータに完璧に適合しています。

決定係数 (R 2 乗) も、モデルとデータがどれほど適合しているかを測定するための標準的な方法です。 これは、モデルで説明される変動の比率として解釈できます。 この場合は、比率が高いほど良く、1 は完璧に適合している状態です。

![線形回帰の評価メトリック](./media/evaluate-model-performance/2.png)

図 2. 線形回帰の評価メトリック。

### <a name="using-cross-validation"></a>クロス検証の使用
前述のとおり、[[モデルのクロス検証]][cross-validate-model] モジュールを使用すれば、トレーニング/スコア付け/評価の反復処理を自動的に実行できます。 この場合に必要なのは、データセット、トレーニングしていないモデル、および [[モデルのクロス検証]][cross-validate-model] モジュールのみです (下の図をご覧ください)。 [[モデルのクロス検証]][cross-validate-model] モジュールのプロパティで、ラベル列を *price* に設定する必要があります。

![回帰モデルのクロス検証](./media/evaluate-model-performance/3.png)

図 3: 回帰モデルのクロス検証。

実験を実行したら、[[モデルのクロス検証]][cross-validate-model] モジュールの該当する出力ポートをクリックして、評価結果を確認できます。 それぞれの反復処理 (分割処理) の詳細と、各メトリックの結果の平均値が表示されます (図 4)。

![回帰モデルのクロス検証の結果](./media/evaluate-model-performance/4.png)

図 4: 回帰モデルのクロス検証の結果。

## <a name="evaluating-a-binary-classification-model"></a>二項分類モデルの評価
二項分類のシナリオでは、ターゲット変数には 2 つの選択肢しかありません。たとえば、{0, 1}、{偽, 真}、{負, 正} などです。 いくつかの人口統計や雇用の変数が含まれた成人従業員のデータセットが提供され、値 {"<=50 K", ">50 K"} を使った二項変数の収入レベルを予測するように依頼されたとします。 つまり、年収が 5 万ドル以下の従業員を表す負のクラスと、その他の従業員を表す正のクラスです。 回帰のシナリオの場合と同じく、モデルのトレーニング、データのスコア付け、結果の評価を行います。 おもな違いは、Azure Machine Learning Studio (クラシック) で計算されて出力されるメトリックの選択です。 この収入レベルの予測シナリオでは、[Adult](https://archive.ics.uci.edu/ml/datasets/Adult) データセットを使用して Studio (クラシック) の実験を作成し、よく使われている二項分類モデルである 2 クラスのロジスティック回帰モデルのパフォーマンスを評価します。

### <a name="creating-the-experiment"></a>実験の作成
Azure Machine Learning Studio (クラシック) で以下のモジュールをワークスペースに追加します。

* 米国国勢調査局提供の、成人収入に関する二項分類データセット
* [2 クラス ロジスティック回帰][two-class-logistic-regression]
* [モデルのトレーニング][train-model]
* [モデルのスコア付け][score-model]
* [モデルの評価][evaluate-model]

図 5 のようにポートを接続し、[[モデルのトレーニング]][train-model] モジュールのラベル列を *income* に設定します。

![二項分類モデルの評価](./media/evaluate-model-performance/5.png)

図 5: 二項分類モデルの評価。

### <a name="inspecting-the-evaluation-results"></a>評価結果の確認
実験を実行したら、[[モデルの評価]][evaluate-model] モジュールの出力ポートをクリックし、"*視覚化*" を選択して評価結果を確認できます (図 7)。 二項分類モデルで使用できる評価メトリックは、"*精度*"、"*正確度*"、"*再現率*"、"*F1 スコア*"、"*AUC*" です。 さらに、このモジュールは、真陽性、偽陰性、偽陽性、真陰性の数を示す混同行列と "*ROC*"、"*正確度/再現性*"、"*リフト*" の曲線を出力します。

精度とは、簡単に言えば正しく分類された事例の比率です。 分類モデルを評価するときは通常、精度のメトリックに最初に注目します。 しかし、テスト データのバランスが悪い (ほとんどの事例が一方のクラスに属している )場合や、一方のクラスのパフォーマンスに主な関心がある場合には、実際には精度によって分類モデルの有効性が捕捉されるわけではありません。 たとえば、収入レベルの分類シナリオで、99% が年収 5 万ドル以下の層に属するデータをテストしているとしましょう。 どの事例についても "<=50K" の層を予測することで、0.99 の精度を達成することが可能です。 この分類モデルのパフォーマンスは非常に高いように思えるかもしれませんが、実際のところ、高収入の人たち (1%) を正確に分類することはできません。

そのため、多角的に評価するには、さらに別のメトリックを計算する必要があります。 そのようなメトリックを詳しく取り上げる前に、二項分類の評価の混同行列について理解しておくことは重要です。 トレーニング セットのクラス ラベルには 2 つの値しかありません。通常は、正の値と負の値です。 分類モデルが正しく予測した正の事例と負の事例のことを、それぞれ真陽性 (TP) と真陰性 (TN) といいます。 また、間違って分類した事例のことを、それぞれ偽陽性 (FP) と偽陰性 (FN) といいます。 混同行列とは、簡単に言えば、この 4 つの分類に該当する事例の数をまとめた表です。 Azure Machine Learning Studio (クラシック) では、データセット内の 2 つのクラスが正のクラスとして自動的に設定されます。 クラス ラベルがブール値または整数値であれば、「真」または「1」のラベルの事例が正のクラスに割り当てられます。 収入のデータセットの場合のようにラベルが文字列であれば、ラベルがアルファベット順に並べ替えられ、最初に選択されるレベルが負のクラス、2 番目のレベルが正のクラスになります。

![二項分類の混同行列](./media/evaluate-model-performance/6a.png)

図 6: 二項分類の混同行列。

収入の分類問題に戻りましょう。分類モデルのパフォーマンスを評価するために、いくつかのことを確認したいと思います。 まず思い浮かぶのは次のような点です。モデルによって年収が 5 万ドル超えと予測された人 (TP+FP) のうち、その分類が正しかった人 (TP) の割合はどれほどでしょうか。 これを確かめるには、モデルの **精度** (正しく分類された陽性の比率:TP/(TP+FP)) を確認します。 また、次のような疑問も思い浮かびます。年収が 5 万ドルを超える高収入の従業員 (TP+FN) のうち、その分類モデルによって正しく分類された人 (TP) の割合はどれほどでしょうか。 これは実際には **再現率** または真陽性率になります。つまり、分類子の TP/(TP+FN) になります。 お気づきかもしれませんが、精度と再現率はトレードオフの関係になっています。 たとえば、比較的バランスの取れたデータセットの場合、ほとんどを正の事例として予測する分類モデルは、再現率が高くなります。一方、負の事例の多くが間違って分類され、偽陽性の数が多くなるので、精度は低めになります。 評価結果の出力ページ (図 7 の左上の部分) にある **精度/再現率** 曲線をクリックすれば、この 2 つのメトリックがどう変化するかを示すプロットを表示できます。

![二項分類の評価結果](./media/evaluate-model-performance/7.png)

図 7: 二項分類の評価結果。

**F1 スコア** もよく使うメトリックです。この場合は、精度と再現率の両方を考慮に入れます。 つまり、その 2 つのメトリックの調和平均であり、F1 = 2 (精度 x 再現率) / (精度 + 再現率) という計算になります。 F1 スコアは、1 つの数字で評価を要約するための便利な方法ですが、分類モデルの動作の仕組みをより詳しく把握するには、精度と再現率の両方を併せて確認することをお勧めします。

さらに、**受信者操作特性 (ROC)** 曲線とそれに対応する **曲線下面積 (AUC)** 値で真陽性率と偽陽性率の対比を確認できます。 この曲線が左上隅に近いほど、分類モデルのパフォーマンスは良好です (つまり、真陽性率が高く、偽陽性率が低くなります)。 ほぼ当てずっぽうのような予測をする傾向の強い分類モデルでは、プロットの対角線に近い曲線になります。

### <a name="using-cross-validation"></a>クロス検証の使用
回帰の例と同じく、クロス検証を使用して、データの各サブセットのトレーニング、スコア付け、評価を自動的に反復実行できます。 また、[[モデルのクロス検証]][cross-validate-model] モジュールでは、トレーニングしていないロジスティック回帰モデルとデータセットを使用できます。 [[モデルのクロス検証]][cross-validate-model] モジュールのプロパティで、ラベル列を *income* に設定する必要があります。 実験を実行して、[[モデルのクロス検証]][cross-validate-model] モジュールの該当する出力ポートをクリックすれば、各分割処理の二項分類メトリック値とそれぞれの平均偏差と標準偏差を確認できます。 

![二項分類モデルのクロス検証](./media/evaluate-model-performance/8.png)

図 8: 二項分類モデルのクロス検証。

![二項分類モデルのクロス検証の結果](./media/evaluate-model-performance/9.png)

図 9: 二項分類モデルのクロス検証の結果。

## <a name="evaluating-a-multiclass-classification-model"></a>多クラス分類モデルの評価
この実験では、3 種類 (クラス) のあやめの事例が含まれている有名な [Iris](https://archive.ics.uci.edu/ml/datasets/Iris "Iris") データセットを使います。 事例ごとに 4 つの特徴値 (がくの長さ、がくの幅、花弁の長さ、花弁の幅) があります。 前の実験では、同じデータセットを使ってモデルのトレーニングとテストを行いました。 今回は、[[データの分割]][split] モジュールを使ってデータのサブセットを 2 つ作成し、1 つ目でトレーニングを行い、2 つ目でスコア付けと評価を行います。 Iris データセットは [UCI Machine Learning Repository](https://archive.ics.uci.edu/ml/index.html) で公開されており、[[データのインポート]][import-data] モジュールを使ってダウンロードできます。

### <a name="creating-the-experiment"></a>実験の作成
Azure Machine Learning Studio (クラシック) で以下のモジュールをワークスペースに追加します。

* [データのインポート][import-data]
* [多クラス デシジョン フォレスト][multiclass-decision-forest]
* [データの分割][split]
* [モデルのトレーニング][train-model]
* [モデルのスコア付け][score-model]
* [モデルの評価][evaluate-model]

図 10 のようにポートを接続します。

[[モデルのトレーニング]][train-model] モジュールのラベル列のインデックスを 5 に設定します。 このデータセットにはヘッダー行がありませんが、クラス ラベルが第 5 列にあります。

[[データのインポート]][import-data] モジュールをクリックし、"*データ ソース*" プロパティを "*HTTP を使用する Web URL*" に設定し、"*URL*" を http://archive.ics.uci.edu/ml/machine-learning-databases/iris/iris.data に設定します。

[[データの分割]][split] モジュールでトレーニングに使用する事例の割合を設定します (0.7 など)。

![多クラス分類モデルの評価](./media/evaluate-model-performance/10.png)

図 10: 多クラス分類モデルの評価

### <a name="inspecting-the-evaluation-results"></a>評価結果の確認
実験を実行し、[モデルの評価][evaluate-model]の出力ポートをクリックします。 この場合は、評価結果が混同行列の形式で表示されます。 この行列で、3 つのクラスすべての実際の事例と予測の事例を確認できます。

![多クラス分類の評価結果](./media/evaluate-model-performance/11.png)

図 11: 多クラス分類の評価結果。

### <a name="using-cross-validation"></a>クロス検証の使用
前述のとおり、[[モデルのクロス検証]][cross-validate-model] モジュールを使用すれば、トレーニング/スコア付け/評価の反復処理を自動的に実行できます。 データセット、トレーニングしていないモデル、[[モデルのクロス検証]][cross-validate-model] モジュールが必要です (下の図を参照)。 この場合も、[[モデルのクロス検証]][cross-validate-model] モジュールのラベル列を設定しなければなりません (この場合は列のインデックスを 5 にします)。 実験を実行して、[[モデルのクロス検証]][cross-validate-model] の該当する出力ポートをクリックすれば、各分割処理のメトリック値に加え、平均偏差と標準偏差も確認できます。 この場合に表示されるメトリックは、二項分類の例で取り上げたメトリックと同じです。 ただし、多クラス分類では、真陽性と陰性、偽陽性と陰性の計算がクラスごとに行われ、正または負のクラスの全体の値はありません。 たとえば、'Iris-setosa' クラスの精度や再現率を計算する場合は、これが正のクラスで他はすべて負であると仮定されます。

![多クラス分類モデルのクロス検証](./media/evaluate-model-performance/12.png)

図 12. 多クラス分類モデルのクロス検証。

![多クラス分類モデルのクロス検証の結果](./media/evaluate-model-performance/13.png)

図 13. 多クラス分類モデルのクロス検証の結果。

<!-- Module References -->
[cross-validate-model]: /azure/machine-learning/studio-module-reference/cross-validate-model
[evaluate-model]: /azure/machine-learning/studio-module-reference/evaluate-model
[linear-regression]: /azure/machine-learning/studio-module-reference/linear-regression
[multiclass-decision-forest]: /azure/machine-learning/studio-module-reference/multiclass-decision-forest
[import-data]: /azure/machine-learning/studio-module-reference/import-data
[score-model]: /azure/machine-learning/studio-module-reference/score-model
[split]: /azure/machine-learning/studio-module-reference/split-data
[train-model]: /azure/machine-learning/studio-module-reference/train-model
[two-class-logistic-regression]: /azure/machine-learning/studio-module-reference/two-class-logistic-regression