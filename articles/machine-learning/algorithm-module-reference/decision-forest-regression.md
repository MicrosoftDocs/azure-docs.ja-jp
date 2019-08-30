---
title: デシジョン フォレスト回帰:モジュール リファレンス
titleSuffix: Azure Machine Learning service
description: Azure Machine Learning service でデシジョン フォレスト回帰モジュールを使用し、デシジョン ツリーの集団に基づく回帰モデルを作成する方法について説明します。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ms.openlocfilehash: b8bb3285aecb6aff399606e6263f014027a86581
ms.sourcegitcommit: 07700392dd52071f31f0571ec847925e467d6795
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/28/2019
ms.locfileid: "70128883"
---
# <a name="decision-forest-regression-module"></a>デシジョン フォレスト回帰モジュール

この記事では、Azure Machine Learning service のビジュアル インターフェイス (プレビュー) のモジュールについて説明します。

デシジョン ツリーの集団に基づく回帰モデルを作成するには、このモジュールを使用します。

モデルの構成後、ラベル付けされたデータセットと[モデルのトレーニング](./train-model.md) モジュールを使用してそのモデルをトレーニングする必要があります。  その後、トレーニング済みのモデルは、予測に使用できます。 

## <a name="how-it-works"></a>動作のしくみ

デシジョン ツリーは、リーフ ノード (決定) に到達するまでバイナリ ツリーのデータ構造を走査しながら、個々の事例について一連の単純なテストを行うノンパラメトリックなモデルです。

デシジョン ツリーには、次の利点があります。

- トレーニング時と予測時における計算とメモリ使用量の両方の点で効率に優れている。

- 非線形の決定境界を表すことができる。

- 統合された特徴選択と分類が行われ、ノイズの多い特徴が存在する状況での回復性がある。

この回帰モデルは、複数のデシジョン ツリーの集団から成ります。 回帰デシジョン フォレストに含まれる各ツリーからは、予測としてガウス分布が出力されます。 ツリーの集団に対して集計 (アグリゲーション) を行うことによって、モデルに含まれる全ツリーの分布の組み合わせに最も近いガウス分布を見つけ出します。

このアルゴリズムの理論に基づくフレームワークとその実装の詳細については、次の記事を参照してください: [Decision Forests: A Unified Framework for Classification, Regression, Density Estimation, Manifold Learning and Semi-Supervised Learning (デシジョン フォレスト: 分類、回帰、密度推定、多様体学習、半教師あり学習のための統一フレームワーク)](https://www.microsoft.com/en-us/research/publication/decision-forests-a-unified-framework-for-classification-regression-density-estimation-manifold-learning-and-semi-supervised-learning/?from=http%3A%2F%2Fresearch.microsoft.com%2Fapps%2Fpubs%2Fdefault.aspx%3Fid%3D158806#)

## <a name="how-to-configure-decision-forest-regression-model"></a>デシジョン フォレスト回帰モデルを構成する方法

1. **デシジョン フォレスト回帰**モジュールを実験に追加します。 このモジュールは、インターフェイスの **[Machine Learning]\(機械学習\)** 、 **[Initialize Model]\(モデルの初期化\)** 、 **[Regression]\(回帰\)** にあります。

2. モジュールのプロパティを開き、 **[Resampling method]\(再サンプリング方法\)** で、個々のツリーの作成に使用する方法を選択します。  **[Bagging]\(バギング\)** または **[レプリケート]** から選択できます。

    - **[Bagging]\(バギング\)** : バギングは、"*ブートストラップ アグリゲーティング*" とも呼ばれます。 回帰デシジョン フォレストに含まれる各ツリーからは、予測の手段としてガウス分布が出力されます。 アグリゲーションとは、個々のツリーから返された全ガウス分布の組み合わせによって得られるガウス分布の融合体のモーメントと最初の 2 つのモーメントが一致するガウス分布を見つけ出すことです。

         詳細については、Wikipedia で[ブートストラップ アグリゲーティング](https://wikipedia.org/wiki/Bootstrap_aggregating)に関する項目を参照してください。

    - **[レプリケート]** : レプリケーションでは、各ツリーがまったく同じ入力データでトレーニングされます。 それぞれのツリー ノードでどの分割述語が使用されるかの決定はランダムなままであり、ツリーは多様になります。

         **[レプリケート]** オプションを使用したトレーニング プロセスの詳細については、次の書籍を参照してください: [『Decision Forests for Computer Vision and Medical Image Analysis』(Criminisi および J. Shotton 共著、Springer 2013)](https://research.microsoft.com/projects/decisionforests/)。

3. **[Create trainer mode]\(トレーナー モードの作成\)** オプションを設定して、モデルのトレーニング方法を指定します。

    - **[Single Parameter]\(単一パラメーター\)**

      モデルの構成方法がわかっている場合、特定の値のセットを引数として渡すことができます。 これらの値は、実験によって得られるか、またはガイダンスとして得られます。



4. 集団として作成するデシジョン ツリーの総数を **[Number of decision trees]\(デシジョン ツリーの数\)** に指定します。 作成するデシジョン ツリーを増やすと、カバレッジが向上する可能性はありますが、トレーニング時間が長くなります。

    > [!TIP]
    > トレーニング済みのモデルを可視化したときに表示されるツリーの数も、この値によって制御されます。 ツリーを 1 つだけ表示または出力したい場合は、この値を「1」に設定してください。ただしその場合、生成されるツリーは 1 つ (最初の一連のパラメーターを持ったツリー) だけであり、それ以上のイテレーションは実行されません。

5. **[Maximum depth of the decision trees]\(デシジョン ツリーの最大深度\)** には、デシジョン ツリーの最大深度を制限する数値を入力します。 ツリーの深度を増やすと、精度が向上する可能性があるものの、オーバーフィットが発生しやすくなり、トレーニング時間が長くなるおそれがあります。

6. **[Number of random splits per node]\(ノードごとのランダム分割数\)** には、ツリーの各ノードを構築するときに使用する分割数を入力します。 "*分割*" とは、ツリー (ノード) の各レベルの特徴がランダムに分割されることを意味します。

7. **[Minimum number of samples per leaf node]\(リーフ ノードごとの最小サンプル数\)** には、ツリーの終端ノード (リーフ) を作成するうえで必要な最小ケース数を指定します。

     この値を増やすと、新しいルールを作成するためのしきい値が大きくなります。 たとえば、既定値の 1 では、ケースが 1 つであっても新しいルールを作成できます。 この値を 5 に増やした場合、同じ条件を満たすケースがトレーニング データに少なくとも 5 つ含まれている必要があります。


9. ラベル付けされたデータセットを接続し、2 個以下の結果を含んだラベル列を 1 つ選択して、[[Train Model]\(モデルのトレーニング\)](./train-model.md) に接続します。

    - **[Create trainer mode]\(トレーナー モードの作成\)** オプションを **[Single Parameter]\(単一パラメーター\)** に設定した場合は、[モデルのトレーニング](./train-model.md) モジュールを使用してモデルをトレーニングします。

   

10. 実験を実行します。

### <a name="results"></a>結果

トレーニングの完了後:

+ それぞれのイテレーションで作成されたツリーを表示するには、トレーニング モジュールの出力を右クリックし、 **[Visualize]\(可視化\)** を選択します。

+ 各ノードのルールを表示するには、各ツリーをクリックして、分割をドリルダウンします。

+ トレーニング済みのモデルのスナップショットを保存するには、トレーニング モジュールの出力を右クリックし、 **[Save As Trained Model]\(トレーニング済みのモデルとして保存する\)** を選択します。 実験を連続実行しても、このモデルのコピーは更新されません。 

## <a name="next-steps"></a>次の手順

Azure Machine Learning service で[使用できる一連のモジュール](module-reference.md)を参照してください。 