---
title: ResNet
titleSuffix: Azure Machine Learning
description: Azure Machine Learning デザイナーで ResNet アルゴリズムを使用してイメージ分類モデルを作成する方法について説明します。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 09/26/2020
ms.openlocfilehash: 88a820d0f1fa9515b4f2992a8305a2d1065e0987
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "93421211"
---
# <a name="resnet"></a>ResNet

この記事では、Azure Machine Learning デザイナーの **ResNet** モジュールを使用し、ResNet アルゴリズムを使ってイメージ分類モデルを作成する方法について説明します。  

この分類アルゴリズムは、教師あり学習手法であり、ラベル付きデータセットが必要です。 
> [!NOTE]
> このモジュールは、Studio で *[データのラベル付け]* から生成されたラベル付きデータセットはサポートしておらず、[[イメージ ディレクトリへの変換]](convert-to-image-directory.md) モジュールから生成されたラベル付きイメージ ディレクトリのみサポートしています。 

モデルとラベル付きイメージ ディレクトリを [Train Pytorch Model](train-pytorch-model.md) への入力として提供することにより、モデルをトレーニングすることができます。 その後、トレーニングされたモデルを使用して、[Score Image Model](score-image-model.md) を使用した新しい入力例の値を予測することができます。

### <a name="more-about-resnet"></a>ResNet の詳細

ResNet の詳細については、[こちらの資料](https://pytorch.org/docs/stable/torchvision/models.html?highlight=resnext101_32x8d#torchvision.models.resnext101_32x8d)を参照してください。

## <a name="how-to-configure-resnet"></a>ResNet を構成する方法

1.  デザイナーで、**ResNet** モジュールをパイプラインに追加します。  

2.  **Model name** には、特定の ResNet 構造体の名前を指定します。サポートされている次の resnet から選択できます: "resnet18"、"resnet34"、"resnet50"、"resnet101"、"resnet152"、"resnet152"、"resnext50\_32x4d"、"resnext101\_32x8d"、"wide_resnet50\_2"、"wide_resnet101\_2"。

3.  **Pretrained** には、ImageNet で事前トレーニングされているモデルを使用するかどうかを指定します。 選択した場合は、選択した事前トレーニング済みモデルに基づいてモデルを微調整できます。選択を解除した場合は、ゼロからトレーニングできます。

4.  トレーニングと検証のイメージ データセット モジュールである **DenseNet** モジュールの出力を [Train Pytorch Model](train-pytorch-model.md) に接続します。 

5. パイプラインを送信します。

## <a name="results"></a>結果

パイプラインの実行が完了した後、モデルをスコア付けに使用するには、[PyTorch モデルのトレーニング](train-pytorch-model.md)を[画像モデルのスコア付け](score-image-model.md)に接続し、新しい入力例の値を予測します。

## <a name="technical-notes"></a>テクニカル ノート  

###  <a name="module-parameters"></a>モジュールのパラメーター  

| 名前       | Range | Type    | Default           | 説明                              |
| ---------- | ----- | ------- | ----------------- | ---------------------------------------- |
| モデル名 | Any   | モード    | resnext101\_32x8d | 特定の ResNet 構造体の名前       |
| Pretrained | Any   | Boolean | True              | ImageNet で事前トレーニング済みモデルを使用するかどうか |
|            |       |         |                   |                                          |

###  <a name="output"></a>出力  

| 名前            | Type                    | 説明                              |
| --------------- | ----------------------- | ---------------------------------------- |
| 未トレーニング モデル | UntrainedModelDirectory | Train Pytorch Model に接続できる未トレーニングの ResNet モデル。 |

## <a name="next-steps"></a>次のステップ

Azure Machine Learning で[使用できる一連のモジュール](module-reference.md)を参照してください。 