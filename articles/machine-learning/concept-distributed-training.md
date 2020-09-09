---
title: 分散トレーニングとは
titleSuffix: Azure Machine Learning
description: 分散トレーニングと、Azure Machine Learning がどのようにこれをサポートするかについて説明します。
services: machine-learning
ms.service: machine-learning
author: nibaccam
ms.author: nibaccam
ms.subservice: core
ms.topic: conceptual
ms.date: 03/27/2020
ms.openlocfilehash: 52716e070437dd7a6b3b880a5a7f3a4afafe8738
ms.sourcegitcommit: 309a9d26f94ab775673fd4c9a0ffc6caa571f598
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/09/2020
ms.locfileid: "82995038"
---
# <a name="distributed-training-with-azure-machine-learning"></a>Azure Machine Learning を使用した分散トレーニング

この記事では、分散トレーニングと、ディープ ラーニング モデルにおいて Azure Machine Learning がどのようにこれをサポートするかについて説明します。 

分散トレーニングでは、モデルをトレーニングするためのワークロードが分割され、ワーカー ノードと呼ばれる複数のミニ プロセッサ間で共有されます。 これらのワーカー ノードは並行して動作し、モデルのトレーニングを高速化します。 分散トレーニングは従来の ML モデルに使用できますが、[ディープ ラーニング](concept-deep-learning-vs-machine-learning.md)など、深層ニューラル ネットワークのトレーニングのためのコンピューティングや時間のかかるタスクにより適しています。 

## <a name="deep-learning-and-distributed-training"></a>ディープ ラーニングと分散トレーニング 

分散トレーニングには主に、[データ並列](#data-parallelism)と[モデル並列](#model-parallelism)の 2 つの種類があります。 ディープ ラーニング モデルにおける分散トレーニングでは、[Azure Machine Learning SDK in Python](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py) は、人気のあるフレームワークの PyTorch および TensorFlow との統合をサポートしています。 どちらのフレームワークも、分散トレーニングにおいてデータ並列を採用しており、コンピューティング速度を最適化するために [horovod](https://horovod.readthedocs.io/en/latest/summary_include.html) を活用できます。 

* [PyTorch を使用した分散トレーニング](how-to-train-pytorch.md#distributed-training)

* [TensorFlow を使用した分散トレーニング](how-to-train-tensorflow.md#distributed-training)

分散トレーニングを必要としない ML モデルについては、Python SDK を使用してモデルをトレーニングするさまざまな方法について、「[Azure Machine Learning を使用してモデルをトレーニングする](concept-train-machine-learning-model.md#python-sdk)」を参照してください。

## <a name="data-parallelism"></a>データ並列

データ並列は、2 つの分散トレーニングの内、より実装が簡単な方法で、ほとんどのユース ケースに対応できます。

この方法では、データはパーティションに分割されます。パーティションの数は、コンピューティング クラスター内の使用可能なノードの合計数と同じになります。 モデルはこれらの各ワーカー ノードにコピーされ、各ワーカーはデータのサブセットを操作します。 各ノードにはトレーニング対象のモデルをサポートするための容量が必要であることに注意してください。つまり、モデルは各ノードに完全に適合している必要があります。 次の図は、このアプローチの視覚的なデモンストレーションを示しています。

![データ並列の概念の図](./media/concept-distributed-training/distributed-training.svg)

各ノードは、それぞれのトレーニング サンプルとラベル付き出力の予測の間のエラーを個別に計算します。 各ノードは、エラーに基づいてモデルを更新し、対応するモデルを更新するためにそのすべての変更を他のノードに伝達する必要があります。 つまり、ワーカー ノードは、一貫したモデルがトレーニングされるよう、バッチ計算の最後にモデル パラメーター (グラデーション) を同期する必要があります。 

## <a name="model-parallelism"></a>モデル並列

モデル並列 (ネットワーク並列とも呼ばれます) では、モデルは異なるノードで同時に実行できるさまざまな部分に分割され、各ノードは同じデータで実行されます。 この方法のスケーラビリティは、アルゴリズムのタスクの並列の程度によって異なり、データ並列よりも実装が複雑になります。 

モデル並列の場合、ワーカー ノードは共有パラメーターを同期するだけでよく、通常は、順伝播法または誤差逆伝播法のステップごとに 1 回です。 また、各ノードは同じトレーニング データのモデルのサブセクションで動作するため、大規模なモデルは問題になりません。

## <a name="next-steps"></a>次のステップ

* Python SDK を使用して[トレーニング環境をセットアップする](how-to-set-up-training-targets.md)方法を確認します。
* 技術的な例については、[リファレンス アーキテクチャ シナリオ](https://docs.microsoft.com/azure/architecture/reference-architectures/ai/training-deep-learning)を参照してください。
* [TensorFlow を使用して ML モデルをトレーニングする](how-to-train-tensorflow.md)。
* [PyTorch を使用してモデルをトレーニングする](how-to-train-pytorch.md)。 