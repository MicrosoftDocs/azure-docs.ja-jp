---
title: "Azure Machine Learning でモデルをデバッグする | Microsoft Docs"
description: "モデルのトレーニングとモデルのスコア付け モジュールによって生成されたエラーを Azure Machine Learning でデバッグする方法。"
services: machine-learning
documentationcenter: 
author: garyericson
manager: jhubbard
editor: cgronlun
ms.assetid: 629dc45e-ac1e-4b7d-b120-08813dc448be
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/14/2017
ms.author: bradsev;garye
translationtype: Human Translation
ms.sourcegitcommit: afe143848fae473d08dd33a3df4ab4ed92b731fa
ms.openlocfilehash: d4cc94a6395ea45bccf65d9a9f3118ec98cb258d
ms.lasthandoff: 03/17/2017


---
# <a name="debug-your-model-in-azure-machine-learning"></a>Azure Machine Learning でモデルをデバッグする

この記事では、モデルを実行したときに次の 2 つの障害が発生する場合の潜在的な理由について説明します。

* [[モデルのトレーニング]][train-model] モジュールがエラーを生成する 
* [[モデルのスコア付け]][score-model] モジュールから生成される結果が間違っている 

[!INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

## <a name="train-model-module-produces-an-error"></a>[モデルのトレーニング] モジュールがエラーを生成する

![Image1](./media/machine-learning-debug-models/train_model-1.png)

[[モデルのトレーニング]][train-model] モジュールでは、次の 2 つを入力する必要があります。

1. Azure Machine Learning によって提供されるモデルのコレクションからの機械学習モデルの種類。
2. 指定したラベル列のあるトレーニング データ。[ラベル] 列は予測変数を示します (データに含まれる残りの列は、[特徴] と見なされます)。

このモジュールは、次のような場合にエラーを発生します。

1. [ラベル] 列が正しく指定されていない。 これは、ラベルとして複数の列が選択されている場合か、正しくない列のインデックスが選択されている場合のいずれかに発生します。 たとえば、25 列だけ格納されている入力データセットに対して列インデックス 30 を使用した場合は、2 番目のケースが当てはまります。

2. データセットに [特徴] 列が含まれていない。 たとえば、入力データセットに列が 1 つだけ含まれていて、その列が [ラベル] 列とマークされていれば、モデルの作成に使用する特徴がないことになります。 この場合、[[モデルのトレーニング]][train-model] モジュールがエラーを生成します。

3. 入力データセット ([特徴] または [ラベル]) の値として無限の値が含まれている。

## <a name="score-model-module-produces-incorrect-results"></a>[モデルのスコア付け] モジュールから生成される結果が間違っている

![Image2](./media/machine-learning-debug-models/train_test-2.png)

教師あり学習の場合の一般的なトレーニング/テストの実験では、[[データの分割]][split] モジュールを使用して元のデータセットを 2 つの部分に分割します。つまり、モデルのトレーニングのために使用する部分と、トレーニング済みのモデルの実行の適合度のスコア付けに使用する部分に分割します。 その後、トレーニング済みのモデルを使用してテスト データにスコアを付け、その結果を評価してモデルの確度を判定します。

[[モデルのスコア付け]][score-model] モジュールでは、次の 2 つを入力する必要があります。

1. [[モデルのトレーニング]][train-model] モジュールからのトレーニング済みのモデル出力。
2. モデルのトレーニングに使用されたデータセットとは別の、スコア付け用のデータセット。

実験が成功した場合でも、[[モデルのスコア付け]][score-model] モジュールから正しくない結果が生成されることがあります。 これは、次のようないくつかのシナリオで発生することがあります。

1. 指定されたラベルがカテゴリ別であり、そのデータに対して回帰モデルのトレーニングが行われた場合は、[[モデルのスコア付け]][score-model] モジュールから正しくない出力が生成されます。 これは、回帰には連続応答変数が必要だからです。 この場合は、分類モデルを使用したほうが適切でした。 

2. 同様に、浮動小数点数のラベル列を持つデータセットに対して分類モデルのトレーニングを実行した場合も、期待した結果が生成されないことがあります。 これは、分類モデルには離散的な応答変数が必要であり、有限の範囲で、通常は少なめの数のクラスが含まれる変数でなければならないためです。

3. スコア付け用のデータセットに、モデルのトレーニングに使用した特徴の一部しか含まれていない場合は、[[モデルのスコア付け]][score-model] モジュールがエラーを生成します。

4. スコア付け用のデータセットの行に欠落値やいずれかの特徴について無限の値が含まれている場合、[[モデルのスコア付け] ][score-model]モジュールは、その行に対する出力を生成しません。

5. [[モデルのスコア付け]][score-model] モジュールは、スコア付け用のデータセットのすべての行に対して同一の出力を生成することがあります。 これが発生するのは、たとえば、デシジョン フォレストを使用して分類を行おうとする場合に、リーフ ノードあたりのサンプルの最低数が、利用できるトレーニング例の数より多い場合です。

<!-- Module References -->
[score-model]: https://msdn.microsoft.com/library/azure/401b4f92-e724-4d5a-be81-d5b0ff9bdb33/
[split]: https://msdn.microsoft.com/library/azure/70530644-c97a-4ab6-85f7-88bf30a8be5f/
[train-model]: https://msdn.microsoft.com/library/azure/5cc7053e-aa30-450d-96c0-dae4be720977/


