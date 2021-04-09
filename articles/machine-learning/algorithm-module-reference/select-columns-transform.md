---
title: Select Columns Transform (列変換の選択):モジュール リファレンス
titleSuffix: Azure Machine Learning
description: Azure Machine Learning デザイナーで Select Columns Transform (列変換の選択) モジュールを使用して、選択変換を実行する方法について説明します。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 10/10/2020
ms.openlocfilehash: f03840e55366d7f105ca4b57bd60061c82833e72
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "93420718"
---
# <a name="select-columns-transform"></a>Select Columns Transform (列変換の選択)

この記事では、Azure Machine Learning デザイナーで Select Columns Transform (列変換の選択) モジュールを使用する方法について説明します。 Select Columns Transform (列変換の選択) モジュールの目的は、予測可能で一貫性のある一連の列がダウンストリームの機械学習操作で使用されるようにすることです。

このモジュールは、特定の列を必要とするスコアリングなどのタスクに役立ちます。 使用可能な列を変更すると、パイプラインが中断されたり、結果が変わったりする可能性があります。

Select Columns Transform (列変換の選択) を使用して、一連の列を作成して保存します。 次に、[Apply Transformation](apply-transformation.md) (変換の適用) モジュールを使用してそれらの選択を新しいデータに適用します。

## <a name="how-to-use-select-columns-transform"></a>Select Columns Transform (列変換の選択) の使用方法

このシナリオでは、特徴選択を使用して、モデルのトレーニングに使用する列の動的セットを生成することを前提としています。 列の選択がスコアリング プロセスで同じになるように、Select Columns Transform (列変換の選択) モジュールを使用して列の選択をキャプチャし、パイプラインの他の場所にそれらを適用します。

1. デザイナーでパイプラインに入力データセットを追加します。

2. [Filter Based Feature Selection](filter-based-feature-selection.md) (フィルターに基づく特徴選択) のインスタンスを追加します。

3. モジュールを接続し、特徴選択モジュールを構成して、入力データセット内でいくつかの最適な特徴を自動的に検出します。

4. [Train Model](train-model.md) (モデルのトレーニング) のインスタンスを追加し、[Filter Based Feature Selection](filter-based-feature-selection.md) (フィルターに基づく特徴選択) の出力をトレーニングの入力として使用します。

    > [!IMPORTANT]
    > 特徴量の重要度は列の値に基づくため、[Train Model](train-model.md) (モデルのトレーニング) への入力に使用できる列を事前に把握することはできません。  
5. Select Columns Transform (列変換の選択) モジュールのインスタンスを接続します。 

    この手順により、他のデータセットに保存または適用できる変換として列の選択が生成されます。 この手順により、特徴選択で識別された列が、他のモジュールで再利用できるように保存されます。

6. [Score Model](score-model.md) (モデルのスコア付け) モジュールを追加します。 

   *入力データセットは接続しないでください。* 代わりに、[Apply Transformation](apply-transformation.md) (変換の適用) モジュールを追加し、特徴選択変換の出力を接続します。

   パイプライン構造は次のようになります。

   > [!div class="mx-imgBorder"]
   > ![サンプル パイプライン](media/module/filter-based-feature-selection-score.png)

   > [!IMPORTANT]
   > [Filter Based Feature Selection](filter-based-feature-selection.md) (フィルターに基づく特徴選択) をスコアリング データセットに適用しても、同じ結果が得られるわけではありません。 特徴選択は値に基づくため、異なる列セットが選択される可能性があります。その場合、スコアリング操作は失敗します。
    
7. パイプラインを送信します。

列選択を保存してから適用するこのプロセスにより、同じデータ スキーマをトレーニングとスコアリングに使用することができます。


## <a name="next-steps"></a>次のステップ

Azure Machine Learning で[使用できる一連のモジュール](module-reference.md)を参照してください。 
