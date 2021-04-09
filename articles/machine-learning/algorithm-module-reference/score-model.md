---
title: モデルのスコア付け:モジュール リファレンス
titleSuffix: Azure Machine Learning
description: Azure Machine Learning でモデルのスコア付けモジュールを使用して、トレーニングされた分類または回帰モデルにより予測を生成する方法について説明します。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 02/11/2020
ms.openlocfilehash: 67d17af4f615907ca50b27ce8fa26973e5869608
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "93314242"
---
# <a name="score-model"></a>モデルのスコア付け

この記事では Azure Machine Learning デザイナーのモジュールについて説明します。

トレーニングされた分類または回帰モデルを使用して予測を生成するには、このモデルを使用します。

## <a name="how-to-use"></a>使用方法

1. **モデルのスコア付け** モジュールをパイプラインに追加します。

2. トレーニング済みモデルと新しい入力データを含むデータセットを接続します。 

    データは、使用するトレーニング済みモデルの種類と互換性のある形式でなければなりません。 また、一般に、入力データセットのスキーマはモデルのトレーニングに使用されたデータのスキーマと一致している必要があります。

3. パイプラインを送信します。

## <a name="results"></a>結果

[モデルのスコア付け](./score-model.md)を使用してスコアのセットを生成したら、次の操作を実行します。

+ モデルの精度 (パフォーマンス) を評価するために使用される一連のメトリックを生成するには、スコア付けされたデータセットを[モデルの評価](./evaluate-model.md)に接続できます。 
+ モジュールを右クリックして **[Visualize]\(視覚化\)** を選択し、結果のサンプルを表示します。
<!-- + To Save the results to a dataset. -->

スコア (予測値) は、モデルと入力データに応じて、さまざまな形式になります。

- 分類モデルの場合、[モデルのスコア付け](./score-model.md)は、クラスの予測値および予測値の確率を出力します。
- 回帰モデルの場合、[モデルのスコア付け](./score-model.md)は予測される数値のみを生成します。


## <a name="publish-scores-as-a-web-service"></a>Web サービスとしてスコアを公開する

スコア付けの一般的な用途は、予測 Web サービスの一部として出力を返すことです。 詳細については、Azure Machine Learning デザイナーでのパイプラインに基づいたリアルタイム エンドポイントのデプロイ方法に関する[このチュートリアル](../tutorial-designer-automobile-price-deploy.md)を参照してください。

## <a name="next-steps"></a>次のステップ

Azure Machine Learning で[使用できる一連のモジュール](module-reference.md)を参照してください。