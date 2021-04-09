---
title: 異常検出モデルのトレーニング:モジュール リファレンス
titleSuffix: Azure Machine Learning
description: 異常検出モデルのトレーニング モジュールを使用して、トレーニング済みの異常検出モデルを作成する方法について説明します。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 02/22/2020
ms.openlocfilehash: edf35fada4233fbe43bc7f859c2414bfb8130714
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "90905734"
---
# <a name="train-anomaly-detection-model-module"></a>異常検出モデルのトレーニング モジュール

この記事では、Azure Machine Learning デザイナーで異常検出モデルのトレーニング モジュールを使用して、トレーニングされた異常検出モデルを作成する方法について説明します。

このモジュールでは、異常検出モデル用のパラメーター セットとラベル付けされていないデータセットを入力として受け取ります。 トレーニング済みの異常検出モデルがトレーニング データのラベル セットと共に返されます。  

デザイナーに用意されている異常検出アルゴリズムの詳細については、「[PCA ベースの異常検出](pca-based-anomaly-detection.md)」を参照してください。  

## <a name="how-to-configure-train-anomaly-detection-model"></a>異常検出モデルのトレーニングを構成する方法 

1.  デザイナーで、**異常検出モデルのトレーニング** モジュールをパイプラインに追加します。 このモジュールは、 **[異常検出]** カテゴリにあります。

2. [PCA ベースの異常検出](pca-based-anomaly-detection.md)など、異常検出用に設計されたモジュールのいずれかに接続します。

    他の種類のモデルはサポートされていません。 パイプラインを実行すると、"All models must have the same learner type (すべてのモデルは学習器の種類が同じでなければなりません)" というエラーが表示されます。  

3.  ラベル列を選択し、アルゴリズム固有のその他のパラメーターを設定することで、異常検出モジュールを構成します。  

4.  **[異常検出モデルのトレーニング]** の右側の入力にトレーニング データセットをアタッチします。  

5.  パイプラインを送信します。  

## <a name="results"></a>結果

トレーニングの完了後:

+ モデルのパラメーターを表示するには、モデルを右クリックし、 **[Visualize]\(可視化\)** を選択します。 

+ 予測を作成するには、[モデルのスコア付け](score-model.md)モジュールを新しい入力データと共に使用します。

+ トレーニング済みのモデルのスナップショットを保存するには、モジュールを選択します。 次に、右側のパネルの **[出力 + ログ]** タブにある **[データセットの登録]** アイコンをクリックします。   

 
## <a name="next-steps"></a>次のステップ

Azure Machine Learning で[使用できる一連のモジュール](module-reference.md)を参照してください。 

デザイナー モジュール固有のエラーの一覧については、「[デザイナーの例外とエラー コード](designer-error-codes.md)」を参照してください。
'