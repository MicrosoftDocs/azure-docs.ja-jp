---
title: Team Data Science Process の特徴選択
description: 特徴選択の目的について説明し、機械学習のデータ強化プロセスにおけるその役割の例を示します。
services: machine-learning
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 940d4c465acb5e8478d2b204ab5b9b18c79254f0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "93321320"
---
# <a name="feature-selection-in-the-team-data-science-process-tdsp"></a>Team Data Science Process (TDSP) の特徴選択
この記事では、特徴選択の目的について説明し、機械学習のデータ強化プロセスにおける特徴選択の役割の例を示します。 例は、Azure Machine Learning Studio で描画しています。

特徴のエンジニアリングと選択は Team Data Science Process (TDSP) の一部です。TDSP の概要については、「[Team Data Science Process とは](overview.md)」を参照してください。 特徴エンジニアリングと特徴選択は、TDSP の **特徴の開発** ステップの一部です。

* **特徴エンジニアリング**: このプロセスは、データ内の既存の生の特徴から、関連する特徴を作成し、学習アルゴリズムの予測力を高めようとします。
* **特徴選択**: このプロセスは、トレーニング問題の次元を削減するために、元のデータの特徴のキーのサブセットを選択します。

通常、**特徴エンジニアリング** は追加の特徴を生成するために最初に適用され、その後、無関係な特徴、重複した特徴、関連性の高い特徴を排除するために **特徴選択** の手順が実行されます。

## <a name="filter-features-from-your-data---feature-selection"></a>データから特徴をフィルター処理する - 特徴選択
特徴の選択は、分類または回帰タスクに使用できます。 目標は、最小限の特徴セットを使用してデータ内の最大量の分散を表すことで、元のデータセットからその次元を削減する特徴のサブセットを選択することです。 この特徴のサブセットは、モデルのトレーニングに使用されます。 特徴選択は、次の 2 つの主な目的を果たします。

* まず、特徴選択は多くの場合、無関係な特徴、重複した特徴、関連性の高い特徴を排除することで、分類の精度を向上させます。
* 次に、特徴の数を減らすことで、モデルのトレーニング プロセスの効率を高めます。 効率性は、サポート ベクター マシンなどのトレーニングに費用がかかる学習者にとって重要です。

特徴選択は、モデルのトレーニングに使用されるデータセット内の特徴の数を減らそうと努めていますが、"次元削減" とは呼ばれません。 特徴選択メソッドは、データ内の元の特徴のサブセットを変更せずに抽出します。  次元削減メソッドは、元の機能を変換し、さらに変更できるようにエンジニアリングされた特徴を使用します。 次元削減メソッドの例には、主成分分析、正準相関分析、特異値分解が含まれます。

なかでも、教師ありコンテキストの特徴選択メソッドの 1 つとして広く適用されているカテゴリは、"フィルターに基づく特徴選択" と呼ばれます。 各特徴と対象の属性間の相関関係を評価することで、これらのメソッドは、統計的尺度を適用して各特徴にスコアを割り当てます。 特徴は、スコアによって順位付けされます。これは、しきい値を設定して特定の特徴を保持や削除する場合に役立ちます。 これらのメソッドで使用される統計的尺度の例には、ユーザーの相関関係、相互情報、カイ二乗検定が含まれます。

Azure Machine Learning Studio には、特徴選択に提供されるモジュールがあります。 次の図に示すように、これらのモジュールには、[フィルターに基づく特徴選択][filter-based-feature-selection]と [Fisher 線形判別分析][fisher-linear-discriminant-analysis]が含まれます。

![特徴選択モジュール](./media/select-features/feature-Selection.png)

たとえば、[フィルターに基づく特徴選択][filter-based-feature-selection]モジュールの使用を考えてみます。 便宜上、テキスト マイニングの例を引き続き使用します。 [特徴ハッシュ][feature-hashing] モジュールから 256 の特徴セットが作成された後、回帰モデルを構築する必要があり、応答変数は "Col1" で、これには書籍レビューの評価 1 から 5 が含まれると想定します。 応答変数は "Col1" で、書籍レビューの評価を 1 ～ 5 で表すこととします。 [フィルターに基づく特徴の選択][filter-based-feature-selection]モジュールによって、"Col1" というターゲット属性を持つ 50 の特徴を含むデータセットが生成されます。 次の図に、この実験のフローと入力パラメーターを示します。

![フィルターに基づく特徴選択モジュールのプロパティ](./media/select-features/feature-Selection1.png)

次の図は、結果として得られるデータセットを示します。

![フィルターに基づく特徴選択モジュールの結果のデータセット](./media/select-features/feature-Selection2.png)

それぞれの特徴は、それ自体と対象の属性 "Col1" 間のピアソンの相関関係に基づいて評価されます。 上位のスコアを持つ特徴は保持されます。

次の図は、選択した特徴に対応するスコアを示します。

![フィルターに基づく特徴選択モジュールのスコア](./media/select-features/feature-Selection3.png)

この[フィルターに基づく特徴選択][filter-based-feature-selection]を適用すると、256 のうち 50 の特徴が選択されます。これらの特性は、"ピアソンの相関関係" スコアリング メソッドに基づいて、対象の属性 "Col1" を持つ最も関連性が高い特徴であるためです。

## <a name="conclusion"></a>まとめ
特徴エンジニアリングと特徴選択は、2 つの一般的にエンジニアリングされ、選択された特徴です。データに含まれるキー情報の抽出を試みるトレーニング プロセスの効率を高めます。 また、入力データを正確に分類して、関心のある結果をより確実に予測するために、これらのモデルのパワーを向上させます。 特徴エンジニアリングと特徴選択は、学習を計算的により扱いやすくするために組み合わせることもできます。 これは、強化した後、モデルの調整やトレーニングに必要な特徴の数を減らすことによって行われます。 数学的に言うと、モデルのトレーニングに選択される特徴は、データのパターンを説明し、正常に結果を予測する独立変数の最小セットです。

必ずしも特徴エンジニアリングや特徴選択を実行する必要はありません。 必要があるかどうかは、収集されたデータ、選択されているアルゴリズム、および実験の目的によって異なります。

<!-- Module References -->
[feature-hashing]: /azure/machine-learning/studio-module-reference/feature-hashing
[filter-based-feature-selection]: /previous-versions/azure/dn905854(v=azure.100)
[fisher-linear-discriminant-analysis]: /azure/machine-learning/studio-module-reference/fisher-linear-discriminant-analysis