---
title: Azure SQL Edge での ONNX を使用した機械学習と AI
description: Azure SQL Edge の機械学習では、Open Neural Network Exchange (ONNX) 形式のモデルがサポートされています。 ONNX とは、さまざまな機械学習フレームワークとツールの間でモデルを交換する場合に使用できるオープン形式です。
keywords: SQL Edge をデプロイする
services: sql-edge
ms.service: sql-edge
ms.subservice: machine-learning
ms.topic: conceptual
author: dphansen
ms.author: davidph
ms.date: 05/19/2020
ms.openlocfilehash: 5dc3d44ac4396897fd43831d51ee628bb06048cb
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "93392063"
---
# <a name="machine-learning-and-ai-with-onnx-in-sql-edge"></a>SQL Edge での ONNX を使用した機械学習と AI

Azure SQL Edge の機械学習では、[Open Neural Network Exchange (ONNX)](https://onnx.ai/) 形式のモデルがサポートされています。 ONNX とは、さまざまな[機械学習フレームワークとツール](https://onnx.ai/supported-tools)の間でモデルを交換する場合に使用できるオープン形式です。

## <a name="overview"></a>概要

Azure SQL Edge で機械学習モデルを推論するには、まずモデルを取得する必要があります。 これは、事前トレーニング済みモデルでも、任意のフレームワークでトレーニングされたカスタム モデルでもかまいません。 Azure SQL Edge では ONNX 形式がサポートされているため、モデルをこの形式に変換する必要があります。 モデルの精度に影響を与えることはありません。ONNX モデルを入手したら、モデルを Azure SQL Edge にデプロイし、[PREDICT T-SQL 関数によるネイティブ スコアリング](/sql/advanced-analytics/sql-native-scoring/)を使用できます。

## <a name="get-onnx-models"></a>ONNX モデルを取得する

ONNX 形式でモデルを取得するには:

- **モデル構築サービス**:[Azure Machine Learning の自動機械学習機能](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/classification-bank-marketing-all-features/auto-ml-classification-bank-marketing-all-features.ipynb)や [Azure Custom Vision Service](../cognitive-services/custom-vision-service/getting-started-build-a-classifier.md) などのサービスが、トレーニング済みモデルの、ONNX 形式での直接のエクスポートをサポートしています。

- [**既存モデルの変換やエクスポート**](https://github.com/onnx/tutorials#converting-to-onnx-format):いくつかのトレーニング フレームワーク ([PyTorch](https://pytorch.org/docs/stable/onnx.html)、Chainer、Caffe2 など) では、ONNX へのネイティブ エクスポート機能がサポートされています。これにより、ご利用のトレーニング済みモデルを特定のバージョンの ONNX 形式 で保存できます。 ネイティブ エクスポートをサポートしていないフレームワークの場合は、スタンドアロンの ONNX コンバーターのインストール可能パッケージがあるため、それらによって、トレーニング済みモデルを、さまざまな機械学習フレームワークから ONNX 形式に変換できます。

     **サポートされているフレームワーク**
   * [PyTorch](http://pytorch.org/docs/master/onnx.html)
   * [Tensorflow](https://github.com/onnx/tensorflow-onnx)
   * [Keras](https://github.com/onnx/keras-onnx)
   * [Scikit-learn](https://github.com/onnx/sklearn-onnx)
   * [CoreML](https://github.com/onnx/onnxmltools)
    
    サポートされているフレームワークと例の完全な一覧については、「[ONNX 形式への変換](https://github.com/onnx/tutorials#converting-to-onnx-format)」を参照してください。

## <a name="limitations"></a>制限事項

現時点では、すべての ONNX モデルが Azure SQL Edge でサポートされているわけではありません。 サポートは、**数値データ型** を持つモデルに限定されています。

- [int および bigint](/sql/t-sql/data-types/int-bigint-smallint-and-tinyint-transact-sql)
- [real および float](/sql/t-sql/data-types/float-and-real-transact-sql)。
  
他の数値型を、サポートされている型に変換するには、[CAST と CONVERT](/sql/t-sql/functions/cast-and-convert-transact-sql) を使用します。

モデルの入力については、モデルへの各入力がテーブル内の 1 つの列に対応するように構造化する必要があります。 たとえば、pandas データフレームを使用してモデルをトレーニングする場合、各入力はモデルに対して個別の列とする必要があります。

## <a name="next-steps"></a>次のステップ

- [Azure portal を使用した SQL Edge のデプロイ](deploy-portal.md)
- [Azure SQL Edge に ONNX モデルをデプロイする](deploy-onnx.md)