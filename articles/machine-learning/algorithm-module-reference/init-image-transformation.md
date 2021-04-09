---
title: イメージ変換の初期化イメージ変換
titleSuffix: Azure Machine Learning
description: Azure Machine Learning デザイナーの Init Image Transformation (イメージ変換の初期化) モジュールを使用して、イメージ変換を初期化する方法について説明します。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 05/26/2020
ms.openlocfilehash: fc0eb196ed24e413c35d64f0571ff29dc3725032
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "93421279"
---
# <a name="init-image-transformation"></a>イメージ変換の初期化

この記事では、Azure Machine Learning デザイナーの **イメージ変換の初期化** モジュールを使用して、イメージ変換を初期化し、イメージを変換する方法を指定する方法について説明します。

## <a name="how-to-configure-init-image-transformation"></a>イメージ変換の初期化の構成方法

1.  デザイナーで **イメージ変換の初期化** モジュールをパイプラインに追加します。 

2.  **[サイズ変更]** では、入力 PIL イメージのサイズを、指定したサイズに変更するかどうかを指定します。 'True' を選択した場合は、目的の出力イメージのサイズを **[サイズ]** で指定できます (既定値は 256)。 

3.  **[Center crop]\(センター クロップ\)** では、指定した PIL イメージを中央でクロップするかどうかを指定します。 'True' を選択した場合、 **[クロップ サイズ]** でクロップの出力イメージ サイズを指定できます (既定値は 224)。  

4.  **[パディング]** では、指定した PIL イメージのすべての側面をパディング値 0 でパディングするかどうかを指定します。 'True' を選択した場合、 **[パディング]** で各境界線へのパディング (追加するピクセル数) を指定できます。

5.  **[Color jitter]\(色ジッター\)** では、イメージの輝度、コントラスト、彩度をランダムに変更するかどうかを指定します。

6.  **[グレースケール]** では、イメージをグレースケールに変換するかどうかを指定します。

7.  **[Random resized crop]\(ランダム サイズ変更クロップ\)** では、指定した PIL イメージをランダムなサイズと縦横比にクロップするかどうかを指定します。 元のサイズのランダム サイズ (0.08 から1.0 の範囲) と元の縦横比のランダムな縦横比 (3/4 から4/3 の範囲) のクロップが行われます。 このクロップは、最終的に指定したサイズに変更されます。
    これは、Inception ネットワークのトレーニングでよく使われます。 'True' を選択した場合、 **[Random size]\(ランダム サイズ\)** で各エッジの期待される出力サイズを指定できます (既定値は 256)。

8.  **[Random crop]\(ランダム クロップ\)** では、指定した PIL イメージをランダムな位置でクロップするかどうかを指定します。 'True' を選択した場合、 **[Random crop size]\(ランダム クロップ サイズ\)** でクロップの目的の出力サイズを指定できます (既定値は 224)。

9.  **[Random horizontal flip]\(ランダム左右反転\)** では、指定した PIL イメージを確率 0.5 でランダムに左右反転するかどうかを指定します。

10.  **[Random vertical flip]\(ランダム上下反転\)** では、指定した PIL イメージを確率 0.5 でランダムに上下反転するかどうかを指定します。

11.  **[Random rotation]\(ランダム回転\)** では、角度でイメージを回転させるかどうかを指定します。 'True' を選択した場合、 **[Random rotation degrees]\(ランダム回転角度\)** 、つまり (-degrees, +degrees) を設定して、度数の範囲で指定できます (既定値は 0)。

12.  **[Random affine]\(ランダム アフィン\)** では、中心を不変に維持するイメージのランダムなアフィン変換を行うかどうかを指定します。 'True' を選択した場合、 **[Random affine degrees]\(ランダム アフィン角度\)** 、つまり (-degrees, +degrees) で、選択する度数の範囲で指定できます (既定値は 0)。

13.  **[Random grayscale]\(ランダム グレースケール\)** では、イメージを確率 0.1 でランダムにグレースケールに変換するかどうかを指定します。

14.  **[Random perspective]\(ランダム パースペクティブ\)** では、指定した PIL イメージのパースペクティブ変換を確率 0.5 でランダムに実行するかどうかを指定します。


16.  上で指定した変換を入力イメージ データセットに適用するには、[イメージ変換の適用](apply-image-transformation.md)モジュールに接続します。

17. パイプラインを送信します。

## <a name="results"></a>結果

変換が完了したら、変換されたイメージは、[イメージ変換の適用](apply-image-transformation.md)モジュールの出力に表示されます。


## <a name="technical-notes"></a>テクニカル ノート  

イメージ変換の詳細については、[https://pytorch.org/docs/stable/torchvision/transforms.html](https://pytorch.org/docs/stable/torchvision/transforms.html) を参照してください。

###  <a name="module-parameters"></a>モジュールのパラメーター  

| 名前                    | Range   | Type    | Default | 説明                              |
| ----------------------- | ------- | ------- | ------- | ---------------------------------------- |
| サイズ変更                  | Any     | Boolean | True    | 入力 PIL イメージのサイズを指定したサイズに変更します |
| サイズ                    | >=1     | Integer | 256     | 目的の出力サイズを指定します          |
| センター クロップ             | Any     | Boolean | True    | 指定した PIL イメージを中央でクロップします  |
| クロップ サイズ               | >=1     | Integer | 224     | クロップの目的の出力サイズを指定します |
| Pad                     | Any     | Boolean | False   | 指定した "パディング" 値で指定した PIL イメージのすべての側面をパディングします |
| パディング                 | >=0     | Integer | 0       | 各境界線へのパディング                   |
| 色ジッター            | Any     | Boolean | False   | イメージの輝度、コントラスト、彩度をランダムに変更します |
| グレースケール               | Any     | Boolean | False   | イメージをグレースケールに変換します               |
| ランダム サイズ変更クロップ     | Any     | Boolean | False   | 指定した PIL イメージをランダムなサイズと縦横比にクロップします |
| ランダム サイズ             | >=1     | Integer | 256     | 各エッジの期待される出力サイズ        |
| ランダム クロップ             | Any     | Boolean | False   | 指定した PIL イメージをランダムな位置でクロップします |
| ランダム クロップ サイズ        | >=1     | Integer | 224     | クロップの目的の出力サイズ          |
| ランダム左右反転  | Any     | Boolean | True    | 指定した PIL イメージを指定した確率でランダムに左右反転します |
| ランダム上下反転    | Any     | Boolean | False   | 指定した PIL イメージを指定した確率でランダムに上下反転します |
| ランダム回転         | Any     | Boolean | False   | 角度でイメージを回転させます                |
| ランダム回転角度 | [0,180] | Integer | 0       | 選択する度数の範囲          |
| ランダム アフィン           | Any     | Boolean | False   | 中心を不変に維持するイメージのランダムなアフィン変換 |
| ランダム アフィン角度   | [0,180] | Integer | 0       | 選択する度数の範囲          |
| ランダム グレースケール        | Any     | Boolean | False   | イメージを確率 0.1 でランダムにグレースケールに変換します |
| ランダム パースペクティブ      | Any     | Boolean | False   | 指定した PIL イメージのパースペクティブ変換を、確率 0.5 でランダムに実行します |
| ランダム消去          | Any     | Boolean | False   | 確率 0.5 でイメージ内の四角形領域をランダムに選択し、そのピクセルを消去します |

###  <a name="output"></a>出力  

| 名前                        | Type                    | 説明                              |
| --------------------------- | ----------------------- | ---------------------------------------- |
| イメージ変換を出力します | TransformationDirectory | **イメージ変換の適用** モジュールに接続できるイメージ変換を出力します。 |

## <a name="next-steps"></a>次のステップ

Azure Machine Learning で[使用できる一連のモジュール](module-reference.md)を参照してください。 