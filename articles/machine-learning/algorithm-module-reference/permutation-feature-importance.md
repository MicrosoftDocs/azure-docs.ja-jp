---
title: 順列の特徴量の重要度:モジュール リファレンス
titleSuffix: Azure Machine Learning
description: デザイナーで順列の特徴量の重要度モジュールを使用して、特徴変数についての順列の特徴量の重要度スコアを計算する方法について説明します。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 02/24/2020
ms.openlocfilehash: 8ae1e79922cc0f34e8b2d1f253fce5078df286d2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "93421245"
---
# <a name="permutation-feature-importance"></a>順列の特徴量の重要度

この記事では、Azure Machine Learning デザイナーで順列の特徴量の重要度モジュールを使用して、実際のデータセットに対する一連の特徴量の重要度スコアを計算する方法について説明します。 これらのスコアは、モデルで使用する最適な特徴を決定するために使用します。

このモジュールでは、特徴の値は一度に 1 列ずつランダムにシャッフルされます。 その前後のモデルのパフォーマンスが測定されます。 標準メトリックの 1 つを選択してパフォーマンスを測定できます。

モジュールから返されるスコアは、順列の後のトレーニング済みモデルのパフォーマンスの *変化* を表しています。 重要な特徴は、通常、シャッフル プロセスの影響を受けやすいため、重要度スコアが高くなります。 

この記事では、順列の特徴量、その理論上の基礎、および機械学習でのその応用について概要を示します。[順列の特徴量の重要度](/archive/blogs/machinelearning/permutation-feature-importance)。  

## <a name="how-to-use-permutation-feature-importance"></a>順列の特徴量の重要度の使用方法

特徴スコアのセットを生成するには、テスト データセットだけでなくトレーニング済みのモデルが必要です。  

1.  Permutation Feature Importance (順列の特徴量の重要度) モジュールを自分のパイプラインに追加します。 このモジュールは、 **[Feature Selection]/(特徴選択/)** カテゴリにあります。 

2.  トレーニング済みのモデルを左側の入力に接続します。 モデルは回帰モデルまたは分類モデルである必要があります。  

3.  右側の入力で、データセットを接続します。 可能であれば、モデルのトレーニングに使用したデータセットとは異なるものを選択します。 このデータセットは、トレーニング済みのモデルに基づくスコアリングに使用されます。 また、特徴の値が変更された後のモデルの評価にも使用されます。  

4.  **[Random seed]\(ランダム シード\)** には、ランダム化のシードとして使用する値を入力します。 0 (既定値) を指定すると、システム クロックに基づいて数値が生成されます。

     シード値は省略可能ですが、同じパイプラインの実行間で再現性を向上させるには、値を指定する必要があります。  

5.  **[Metric for measuring performance]/(パフォーマンスを測定するためのメトリック/)** については、順列後にモデルの品質を計算するときに使用する 1 つのメトリックを選択します。  

     Azure Machine Learning デザイナーでは、分類モデルと回帰モデルのどちらを評価するかに応じて、次のメトリックがサポートされます。  

    -   **分類**

        正確性、精度、リコール  

    -   **Regression** (回帰)

        正確度、リコール、平均絶対誤差、二乗平均平方根誤差、相対絶対誤差、相対二乗誤差、決定係数  

     これらの評価メトリックとその計算方法の詳細については、[モデルの評価](evaluate-model.md)に関する記事を参照してください。  

6.  パイプラインを送信します。  

7.  このモジュールは、特徴列とそれらに関連付けられているスコアの一覧を出力します。 この一覧はスコアの降順でランク付けされます。  


##  <a name="technical-notes"></a>テクニカル ノート

Permutation Feature Importance (順列の特徴量の重要度) は、各特徴列の値を一度に 1 列ずつランダムに変更することによって機能します。 その後、モデルを評価します。 

このモジュールによって提供される順位付けは、[Filter Based Feature Selection (フィルターに基づく特徴選択)](filter-based-feature-selection.md) から得られるものとは異なることがよくあります。 Filter Based Feature Selection (フィルターに基づく特徴選択) では、モデルの作成 "*前*" にスコアが計算されます。 

この違いの理由は、Permutation Feature Importance (順列の特徴量の重要度) では、特徴量と目標値の間の関連付けが測定されないためです。 代わりに、モデルからの予測に対して各特徴量がどの程度の影響を与えるかをキャプチャします。
  
## <a name="next-steps"></a>次のステップ

Azure Machine Learning で[使用できる一連のモジュール](module-reference.md)を参照してください。