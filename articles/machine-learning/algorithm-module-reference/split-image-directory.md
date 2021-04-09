---
title: イメージ ディレクトリの分割
titleSuffix: Azure Machine Learning
description: デザイナーのイメージ ディレクトリの分割モジュールを使用して、イメージ ディレクトリのイメージを 2 つの異なるセットに分割する方法について説明します。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 09/26/2020
ms.openlocfilehash: 3ee4dd9b2e344ecb3e1a6424ce7310270e7cd076
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "93421194"
---
# <a name="split-image-directory"></a>イメージ ディレクトリの分割

このトピックでは、Azure Machine Learning デザイナーのイメージ ディレクトリの分割モジュールを使用して、画像ディレクトリの画像を 2 つの異なるセットに分割する方法について説明します。

このモジュールは、画像データをトレーニング セットとテスト セットに分割する必要がある場合に特に便利です。 

## <a name="how-to-configure-split-image-directory"></a>Split Image Directory を構成する方法

1. **Split Image Directory** モジュールをパイプラインに追加します。 このモジュールは [Computer Vision/Image Data Transformation]\(Computer Vision/イメージ データ変換\) カテゴリにあります。

2. それを、出力が画像ディレクトリであるモジュールに接続します。

3. **[Fraction of images in the first output]\(最初の出力に含まれる画像の割合\)** を入力し、左の分割に入れるデータの割合を指定します。既定値は 0.9 です。 割合の結果が整数でない場合、モジュールでは小さい方の近い整数が使用されます。


## <a name="technical-notes"></a>テクニカル ノート

### <a name="expected-inputs"></a>想定される入力

| 名前                  | Type           | 説明              |
| --------------------- | -------------- | ------------------------ |
| Input image directory (入力画像ディレクトリ) | ImageDirectory | 分割する画像ディレクトリ |

### <a name="module-parameters"></a>モジュールのパラメーター

| 名前                                   | Type  | Range | 省略可能 | 説明                            | Default |
| -------------------------------------- | ----- | ----- | -------- | -------------------------------------- | ------- |
| Fraction of images in the first output (最初の出力に含まれる画像の割合) | Float | 0-1   | 必須 | Fraction of images in the first output (最初の出力に含まれる画像の割合) | 0.9     |

### <a name="outputs"></a>出力

| 名前                    | Type           | 説明                              |
| ----------------------- | -------------- | ---------------------------------------- |
| Output image directory1 (出力画像ディレクトリ 1) | ImageDirectory | 選択された画像が格納される画像ディレクトリ |
| Output image directory2 (出力画像ディレクトリ 2) | ImageDirectory | 他のすべての画像が格納される画像ディレクトリ |

## <a name="next-steps"></a>次のステップ

Azure Machine Learning で[使用できる一連のモジュール](module-reference.md)を参照してください。 

