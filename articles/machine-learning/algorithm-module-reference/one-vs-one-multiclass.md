---
title: One-vs-One Multiclass
titleSuffix: Azure Machine Learning
description: Azure Machine Learning で One-vs-One Multiclass モジュールを使用して、二項分類モデルのアンサンブルから多クラス分類モデルを作成する方法を説明します。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 11/13/2020
ms.openlocfilehash: 92ef5b372113d0d226335b8ec464a75a5a684bc3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "94592908"
---
# <a name="one-vs-one-multiclass"></a>One-vs-One Multiclass

この記事では、Azure Machine Learning デザイナーで One-vs-One Multiclass モジュールを使用する方法について説明します。 目標は、*one-versus-one* アプローチを使用して、複数のクラスを予測できる分類モデルを作成することです。

このモジュールは、結果が連続またはカテゴリ予測変数に依存する場合に、考えられる 3 つ以上の結果を予測するモデルを作成する際に役立ちます。 また、この方法では、複数の出力クラスを必要とする問題に二項分類法を使用することもできます。

### <a name="more-about-one-versus-one-models"></a>one-versus-one モデルの詳細

分類アルゴリズムには、3 つ以上のクラスの使用が仕様で許可されているものもあります。 考えられる結果を 2 つの値のいずれかに制限するもの (バイナリ (2 クラス) モデル) もあります。 ただし、二項分類アルゴリズムでも、さまざまな戦略を使用して多クラス分類タスクに適合させることができます。 

このモジュールは、クラスのペアに応じてバイナリ モデルが作成される one-versus-one 法を実装しています。 予測時に、最も多く投票を受けたクラスが選択されます。 ここでは、`n_classes * (n_classes - 1) / 2` 分類子に適合する必要があるため、この方法は通常、O(n_classes^2) が複雑であるという理由で、one-versus-all より低速になります。 ただし、この方法は、`n_samples` を使用したスケーリングが適切に行われない kernel アルゴリズムなどのアルゴリズムでは、便利な場合があります。 これは、個々の学習の問題にはデータの小さなサブセットのみが関係している一方で、one-versus-all の場合は、完全なデータセットが `n_classes` の回数使用されるためです。

このモジュールでは、実質的に、個々のモデルのアンサンブルが作成され、結果がマージされて、すべてのクラスを予測する単一のモデルが作成されます。 任意の二項分類子を one-versus-one モデルの基礎として使用できます。  

たとえば、[2 クラス サポート ベクター マシン](two-class-support-vector-machine.md) モデルを構成し、それを One-vs-One Multiclass モジュールへの入力として提供するとします。 モジュールにより、出力クラスのすべてのメンバーに対して 2 クラス サポート ベクター マシン モデルが作成されます。 その後、one-versus-one 法を適用して、すべてのクラスの結果が結合されます。  

このモジュールでは sklearn の OneVsOneClassifier が使用されます。詳細については、[こちら](https://scikit-learn.org/stable/modules/generated/sklearn.multiclass.OneVsOneClassifier.html)を参照してください。

## <a name="how-to-configure-the-one-vs-one-multiclass-classifier"></a>One-vs-One Multiclass 分類子を構成する方法  

このモジュールでは、複数のクラスを分析するための二項分類モデルのアンサンブルが作成されます。 このモジュールを使用するには、まず "*二項分類*" モデルを構成してトレーニングする必要があります。 

バイナリ モデルを One-vs-One Multiclass モジュールに接続します。 次に、ラベル付きトレーニング データセットに対して[モデルのトレーニング](train-model.md)を使用してモデルのアンサンブルをトレーニングします。

モデルを組み合わせると、One-vs-One Multiclass によって複数の二項分類モデルが作成され、クラスごとにアルゴリズムが最適化されて、モデルがマージされます。 このモジュールは、トレーニング データセットに複数のクラス値がある場合でも、これらのタスクを実行します。

1. デザイナーで One-vs-One Multiclass モジュールを自分のパイプラインに追加します。 このモジュールは、 **[Machine Learning] - [初期化]** の **[分類]** カテゴリにあります。

   One-vs-One Multiclass 分類子には、独自の構成可能なパラメーターがありません。 カスタマイズは、入力として提供される二項分類モデルで行う必要があります。

2. 二項分類モデルをパイプラインに追加し、そのモデルを構成します。 たとえば、[2 クラス サポート ベクター マシン](two-class-support-vector-machine.md)または [2 クラス ブースト デシジョン ツリー](two-class-boosted-decision-tree.md)を使用できます。

3. [モデルのトレーニング](train-model.md) モジュールを自分のパイプラインに追加します。 One-vs-One Multiclass の出力である、トレーニングされていない分類子を接続します。

4. [モデルのトレーニング](train-model.md)のもう一方の入力で、複数のクラス値を含むラベル付きトレーニング データセットを接続します。

5. パイプラインを送信します。

## <a name="results"></a>結果

トレーニングが完了したら、モデルを使用して多クラス予測を行うことができます。

または、トレーニングされていない分類子を [Cross-Validate Model (モデルのクロス検証)](cross-validate-model.md) に渡して、ラベル付き検証データセットに対するクロス検証を行うこともできます。


## <a name="next-steps"></a>次のステップ

Azure Machine Learning で[使用できる一連のモジュール](module-reference.md)を参照してください。 
