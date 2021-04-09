---
title: One-vs-All Multiclass
titleSuffix: Azure Machine Learning
description: Azure Machine Learning デザイナーで One-vs-All Multiclass モジュールを使用して二項分類モデルのアンサンブルを作成する方法について説明します。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 11/13/2020
ms.openlocfilehash: 4dfe284a00052cbd1915d62355e1d7772f3712ab
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "94591871"
---
# <a name="one-vs-all-multiclass"></a>One-vs-All Multiclass

この記事では、Azure Machine Learning デザイナーで One-vs-All Multiclass モジュールを使用する方法について説明します。 目標は、*one-versus-all* アプローチを使って複数のクラスを予測できる分類モデルを作成することです。

このモジュールは、結果が連続またはカテゴリ予測変数に依存する場合に、考えられる 3 つ以上の結果を予測するモデルを作成する際に役立ちます。 また、この方法では、複数の出力クラスを必要とする問題に二項分類法を使用することもできます。

### <a name="more-about-one-versus-all-models"></a>one-versus-all モデルの詳細

分類アルゴリズムには、3 つ以上のクラスの使用が仕様で許可されているものもあります。 考えられる結果を 2 つの値のいずれかに制限するもの (バイナリ (2 クラス) モデル) もあります。 ただし、二項分類アルゴリズムでも、さまざまな戦略を使用して多クラス分類タスクに適合させることができます。 

このモジュールは、複数の出力クラスのそれぞれに対してバイナリ モデルが作成される one-versus-all 法を実装しています。 このモジュールでは、個々のクラスのこれらの各バイナリ モデルが、二項分類の問題と同様に、その補集合 (モデルの他のすべてのクラス) に対して評価されます。 その計算効率 (`n_classes` 分類子のみが必要) に加え、このアプローチの利点の 1 つに、解釈能力があります。 各クラスは、分類子別でのみ表されるため、対応する分類子を調べることによって、クラスに関する知識を得ることができます。 これは、多クラス分類で最も一般的に使用される方法であり、これは公正な既定の選択肢です。 次に、これらの二項分類子を実行し、信頼度スコアが最も高い予測を選択することで、予測が行われます。 

このモジュールでは、実質的に、個々のモデルのアンサンブルが作成され、結果がマージされて、すべてのクラスを予測する単一のモデルが作成されます。 任意の二項分類子を one-versus-all モデルの基礎として使用できます。  

たとえば、[2 クラス サポート ベクター マシン](two-class-support-vector-machine.md) モデルを構成し、それを One-vs-All Multiclass モジュールへの入力として提供するとします。 モジュールにより、出力クラスのすべてのメンバーに対して 2 クラス サポート ベクター マシン モデルが作成されます。 その後、one-versus-all 法を適用してすべてのクラスの結果が結合されます。  

このモジュールでは sklearn の OneVsRestClassifier が使用されます。詳細については、[こちら](https://scikit-learn.org/stable/modules/generated/sklearn.multiclass.OneVsRestClassifier.html)を参照してください。

## <a name="how-to-configure-the-one-vs-all-multiclass-classifier"></a>One-vs-All Multiclass 分類子を構成する方法  

このモジュールでは、複数のクラスを分析するための二項分類モデルのアンサンブルが作成されます。 このモジュールを使用するには、まず "*二項分類*" モデルを構成してトレーニングする必要があります。 

バイナリ モデルを One-vs-All Multiclass モジュールに接続します。 次に、ラベル付きトレーニング データセットに対して[モデルのトレーニング](train-model.md)を使用してモデルのアンサンブルをトレーニングします。

モデルを組み合わせると、One-vs-All Multiclass によって複数の二項分類モデルが作成され、クラスごとにアルゴリズムが最適化されて、モデルがマージされます。 このモジュールは、トレーニング データセットに複数のクラス値がある場合でも、これらのタスクを実行します。

1. デザイナーで One-vs-All Multiclass モジュールを自分のパイプラインに追加します。 このモジュールは、 **[Machine Learning] - [初期化]** の **[分類]** カテゴリにあります。

   One-vs-All Multiclass 分類子には、独自の構成可能なパラメーターはありません。 カスタマイズは、入力として提供される二項分類モデルで行う必要があります。

2. 二項分類モデルをパイプラインに追加し、そのモデルを構成します。 たとえば、[2 クラス サポート ベクター マシン](two-class-support-vector-machine.md)または [2 クラス ブースト デシジョン ツリー](two-class-boosted-decision-tree.md)を使用できます。

3. [モデルのトレーニング](train-model.md) モジュールを自分のパイプラインに追加します。 One-vs-All Multiclass の出力であるトレーニングされていない分類子を接続します。

4. [モデルのトレーニング](train-model.md)のもう一方の入力で、複数のクラス値を含むラベル付きトレーニング データセットを接続します。

5. パイプラインを送信します。

## <a name="results"></a>結果

トレーニングが完了したら、モデルを使用して多クラス予測を行うことができます。

または、トレーニングされていない分類子を [Cross-Validate Model (モデルのクロス検証)](cross-validate-model.md) に渡して、ラベル付き検証データセットに対するクロス検証を行うこともできます。


## <a name="next-steps"></a>次のステップ

Azure Machine Learning で[使用できる一連のモジュール](module-reference.md)を参照してください。 
