---
title: ブースト デシジョン ツリー回帰:モジュール リファレンス
titleSuffix: Azure Machine Learning
description: Azure Machine Learning のブースト デシジョン ツリー回帰モジュールを使用して、ブーストで回帰ツリーのアンサンブルを作成する方法について説明します。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 08/24/2020
ms.openlocfilehash: 664943fc5535883b3df77b2795383e5c0586a71c
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "94375331"
---
# <a name="boosted-decision-tree-regression-module"></a>ブースト デシジョン ツリー回帰モジュール

この記事では Azure Machine Learning デザイナーのモジュールについて説明します。

このモジュールを使用し、ブーストで回帰ツリーのアンサンブルを作成します。 *ブースティング* は、各ツリーが前のツリーに依存していることを意味します。 このアルゴリズムは、それに先行するツリーの残差をフィッティングにより学習します。 したがって、デシジョン ツリー アンサンブルのブースティングは、適用範囲が狭いという小さなリスクがありますが、精度を上げる傾向があります。  

このモジュールは、LightGBM アルゴリズムに基づいています。
  
この回帰メソッドは教師あり学習メソッドであり、そのため、*ラベル付きのデータセット* を必要とします。 ラベル列には数値を含める必要があります。  

> [!NOTE]
> このモジュールは、数値変数を使用するデータセット以外では使用しません。  

モデルを定義したら、[モデルのトレーニング](./train-model.md)を利用してトレーニングします。

  
## <a name="more-about-boosted-regression-trees"></a>ブースト回帰ツリーの詳細  

ブースティングは、バギングやランダム フォレストなどと共に、アンサンブル モデルを作成するための基本的手法の 1 つです。  Azure Machine Learning では、ブースト デシジョン ツリーで MART 勾配ブースティング アルゴリズムが効率的に実装されます。 勾配ブースティングは、回帰問題のための機械学習手法です。 事前定義された損失関数を使用して各段階のエラーを計測し、次の段階でそれを修正するという方法で各回帰ツリーを段階的に構築します。 したがって、予測モデルは実際、弱い予測モデルのアンサンブルになります。  
  
回帰の問題では、ブースティングによって一連のツリーが段階的に構築され、任意の微分可能な損失関数を使用して最適なツリーが選択されます。  
  
詳細については、以下の記事を参照してください。  
  
+ [https://wikipedia.org/wiki/Gradient_boosting#Gradient_tree_boosting](https://wikipedia.org/wiki/Gradient_boosting)

    勾配ブースティングに関するこの Wikipedia 記事では、ブースト ツリーの背景的情報がいくつかわかります。 
  
-  [https://research.microsoft.com/apps/pubs/default.aspx?id=132652](https://research.microsoft.com/apps/pubs/default.aspx?id=132652)  

    Microsoft Research:From RankNet to LambdaRank to LambdaMART:An Overview (RankNet から LambdaRank、LambdaMART まで: 概要) 作成者: J.C. Burges

勾配ブースティング手法は、適切な損失関数で減らして回帰にすることで分類の問題にも使用できます。 分類タスクにブースト ツリーを実施する方法については、「[Two-Class Boosted Decision Tree](./two-class-boosted-decision-tree.md)」(2 クラス ブースト デシジョン ツリー) を参照してください。  

## <a name="how-to-configure-boosted-decision-tree-regression"></a>ブースト デシジョン ツリー回帰の構成方法

1.  **ブースト デシジョン ツリー** モジュールをパイプラインに追加します。 このモジュールは、 **[Machine Learning]\(機械学習\)** の **[Initialize]\(初期化\)** の順に進み **[Regression]\(回帰\)** カテゴリにあります。 
  
2.  **[Create trainer mode]\(トレーナー モードの作成\)** オプションを設定して、モデルのトレーニング方法を指定します。  
  
    -   **Single Parameter (単一パラメーター)** : モデルの構成方法を決めている場合はこのオプションを選択し、特定の値のセットを引数として渡します。 
     
    -   **[Parameter Range]\(パラメーター範囲\)** : 最適なパラメーターがわからず、パラメーター スイープを実行する場合は、このオプションを選択します。 反復する値の範囲を選択します。[モデルのハイパーパラメーターの調整](tune-model-hyperparameters.md)では、指定した設定の可能なすべての組み合わせに対して反復処理を行い、最適な結果を生成するハイパーパラメーターを決定します。    
   
  
3. **[Maximum number of leaves per tree]\(ツリーあたりの最大リーフ数\)** :ツリーに作成できる終端ノード (リーフ) の最大数を指定します。  

    この値を増やすと、ツリーのサイズが大きくなって精度が上がる反面、オーバーフィットが発生したり、トレーニング時間が長くなったりするおそれがあります。  

4. **[Minimum number of samples per leaf node]\(リーフ ノードごとの最小サンプル数\)** :ツリーの終端ノード (リーフ) を作成するうえで必要なケース数を指定します。

    この値を増やすと、新しいルールを作成するためのしきい値が大きくなります。 たとえば、既定値の 1 では、ケースが 1 つであっても新しいルールを作成できます。 この値を 5 に増やした場合、同じ条件を満たすケースがトレーニング データに少なくとも 5 つ含まれている必要があります。

5. **[Learning rate]\(学習率\)** :学習時のステップ サイズを定義する 0 から 1 までの数値を入力します。 学習器がどの程度の速さ (遅さ) で最適解に収束するかは、学習率によって決まります。 ステップ サイズが大きすぎると、最適解から離れていってしまう可能性があります。 ステップ サイズが小さすぎると、トレーニングが最適解に収束するまでの時間が長くなります。

6. **[Number of trees constructed]\(構築するツリーの数\)** :アンサンブルに作成するデシジョン ツリーの総数を指定します。 作成するデシジョン ツリーを増やすと、カバレッジが向上する可能性はありますが、トレーニング時間が長くなります。

    値に 1 を設定した場合であっても、生成されるツリーは 1 つ (最初の一連のパラメーターを持ったツリー) だけであり、それ以上のイテレーションは実行されません。

7. **Random number seed (乱数シード)** : 乱数シード値として使用する負以外の任意の整数を入力します。 シードを指定することによって、同じデータとパラメーターで繰り返し実行したときの再現性が確保されます。

    ランダム シードは、既定では 0 に設定されます。これは、初期シード値がシステム クロックから取得されることを意味します。
  

9. モデルをトレーニングする:

    + **[Create trainer mode]\(トレーナー モードの作成\)** を **[Single Parameter]\(単一パラメーター\)** に設定した場合は、タグ付けされたデータセットと [モデルのトレーニング](train-model.md) モジュールを接続します。  
  
    + **[Create trainer mode]\(トレーナー モードの作成\)** を **[Parameter Range]\(パラメーター範囲\)** に設定した場合は、[[Tune Model Hyperparameters]\(モデルのハイパーパラメーターの調整\)](tune-model-hyperparameters.md) を使用して、タグ付けしたデータセットを接続してモデルをトレーニングします。  
  
    > [!NOTE]
    > 
    > パラメーター範囲を [[モデルのトレーニング]](train-model.md) に渡すと、単一のパラメーター リストの既定値のみが使用されます。  
    > 
    > [[Tune Model Hyperparameters]\(モデルのハイパーパラメーターの調整\)](tune-model-hyperparameters.md) モジュールによって、パラメーターごとに設定の範囲が求められているとき、それに単一のパラメーター値セットを渡した場合、それらの値は無視され、学習器の既定値が使用されます。  
    > 
    > **[Parameter Range]\(パラメーター範囲\)** オプションを選択し、任意のパラメーターに単一の値を入力した場合、指定した単一の値はスイープ全体で使用されます。これは、他のパラメーターが値の範囲の中で変化する場合でも同様です。
    

10. パイプラインを送信します。  
  
## <a name="results"></a>結果

トレーニングの完了後:

+ スコアリングにモデルを使用するには、[Train Model](train-model.md) を [Score Model](./score-model.md) に接続し、新しい入力例の値を予測します。

+ トレーニング済みのモデルのスナップショットを保存するには、 **[Trained model]\(トレーニング済みモデル\)** の右側のパネルの **[Outputs]\(出力\)** タブを選択し、 **[Register dataset]\(データセットの登録\)** アイコンをクリックします。 トレーニング済みのモデルのコピーはモジュールとしてモジュール ツリーに保存され、パイプラインの後続の実行では更新されません。

## <a name="next-steps"></a>次のステップ

Azure Machine Learning で[使用できる一連のモジュール](module-reference.md)を参照してください。 
