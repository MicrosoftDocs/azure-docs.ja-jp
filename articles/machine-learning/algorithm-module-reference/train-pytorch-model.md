---
title: PyTorch モデルのトレーニング
titleSuffix: Azure Machine Learning
description: Azure Machine Learning デザイナーの Pytorch モデルのトレーニング モジュールを使用して、モデルを最初からトレーニングする、または既存のモデルを微調整します。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 09/26/2020
ms.openlocfilehash: 2d88069f33995bdbe9dd479afe9a4e72ab9939b6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "93420667"
---
# <a name="train-pytorch-model"></a>PyTorch モデルのトレーニング

この記事では、Azure Machine Learning デザイナーで **PyTorch モデルのトレーニング** モジュールを使用して、DenseNet などの Pytorch モデルをトレーニングする方法について説明します。 トレーニングは、モデルを定義してそのパラメーターを設定した後に行なわれ、これにはラベル付けされたデータが必要です。 

## <a name="how-to-use-train-pytorch-model"></a>PyTorch モデルのトレーニングを使用する方法 

1. デザイナーで [DenseNet](densenet.md) モジュールまたは [ResNet](resnet.md) をパイプライン ドラフトに追加します。

2. **PyTorch モデルのトレーニング** モジュールをパイプラインに追加します。 このモジュールは、 **[モデル トレーニング]** カテゴリにあります。 **[Train]\(トレーニング\)** を展開し、**PyTorch モデルのトレーニング** モジュールをパイプラインにドラッグします。

   > [!NOTE]
   > **PyTorch モデルのトレーニング** モジュールは、大規模なデータセットについては **GPU** 型のコンピューティングで実行することが推奨されます。それ以外の場合、パイプラインは失敗します。 モジュールの右側のウィンドウで特定のモジュールを計算するよう選択するには、 **[Use other compute target]\(その他のコンピューティング先を使用する\)** を設定します。

3.  左側の入力に、未トレーニングのモードをアタッチします。 トレーニング データセットと検証データセットを **PyTorch モデルのトレーニング** の中央および右側の入力にアタッチします。

    未トレーニングのモデルでは、DenseNet のような PyTorch モデルを使用する必要があります。それ以外の場合は、'InvalidModelDirectoryError' がスローされます。

    データセットの場合、トレーニング データセットはラベル付けされたイメージ ディレクトリである必要があります。 ラベル付けされたイメージ ディレクトリを取得する方法については、「**イメージ ディレクトリへの変換**」を参照してください。 ラベル付けされていない場合は、'NotLabeledDatasetError' がスローされます。

    トレーニング データセットと検証データセットのラベル カテゴリは同じです。それ以外の場合は、InvalidDatasetError がスローされます。

4.  **[Epochs]\(エポック\)** には、トレーニングするエポック数を指定します。 データセット全体は、エポック毎に反復されます。既定値は 5 です。

5.  **[Batch size]\(バッチ サイズ\)** には、バッチでトレーニングするインスタンスの数を指定します。既定値は 16 です。

6.  **[Learning rate]\(学習速度\)** に *学習速度* の値を指定します。 学習率の値は、モデルがテストされて修正されるたびに、SGD のようなオプティマイザーで使用されるステップのサイズを制御します。

    速度の値を小さくすると、モデルのテストが頻繁に実行されますが、ローカルで停滞する可能性があります。 ステップのサイズを大きくすることで収束速度は速くなりますが、真の極小値から離れていってしまうおそれがあります。 既定値は 0.001 です。

7.  **[Random seed]\(ランダム シード\)** には、必要に応じて、シードとして使用する整数値を入力します。 繰り返し実行したときの実験の再現性を確保したい場合は、シードを使用することをお勧めします。

8.  **[Patience]\(忍耐\)** には、検証の損失が連続して減少しない場合に、エポックがトレーニングを早期に停止する回数を指定します。 既定値は 3 です。

9.  パイプラインを送信します。 データセットのサイズが大きい場合は、しばらく時間がかかり、GPU コンピューティングが推奨されます。

## <a name="results"></a>結果

パイプラインの実行が完了した後、モデルをスコア付けに使用するには、[PyTorch モデルのトレーニング](train-pytorch-model.md)を[画像モデルのスコア付け](score-image-model.md)に接続し、新しい入力例の値を予測します。

## <a name="technical-notes"></a>テクニカル ノート
###  <a name="expected-inputs"></a>想定される入力  

| 名前               | Type                    | 説明                              |
| ------------------ | ----------------------- | ---------------------------------------- |
| 未トレーニング モデル    | UntrainedModelDirectory | 未トレーニング モデル、PyTorch が必要         |
| トレーニング データセット   | ImageDirectory          | トレーニング データセット                         |
| 検証データセット | ImageDirectory          | 各エポックを評価するための検証データセット |

###  <a name="module-parameters"></a>モジュールのパラメーター  

| 名前          | Range            | Type    | Default | 説明                              |
| ------------- | ---------------- | ------- | ------- | ---------------------------------------- |
| Epochs (エポック)        | >0               | Integer | 5       | ラベルまたは結果列を含む列を選択します |
| バッチ サイズ    | >0               | Integer | 16      | バッチでトレーニングするインスタンスの数   |
| Learning rate (学習率) | >=double.Epsilon | Float   | 0.001   | 確率的勾配降下法オプティマイザーの初期学習率。 |
| Random seed (ランダム シード)   | Any              | Integer | 1       | モデルで使用される乱数ジェネレーターのシードです。 |
| Patience (忍耐)      | >0               | Integer | 3       | トレーニングを早期に停止するエポックの数   |

###  <a name="outputs"></a>出力  

| 名前          | Type           | 説明   |
| ------------- | -------------- | ------------- |
| トレーニングされたモデル | ModelDirectory | トレーニングされたモデル |

## <a name="next-steps"></a>次のステップ

Azure Machine Learning で[使用できる一連のモジュール](module-reference.md)を参照してください。 



