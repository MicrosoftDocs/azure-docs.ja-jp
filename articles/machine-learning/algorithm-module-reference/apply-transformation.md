---
title: 変換の適用:モジュール リファレンス
titleSuffix: Azure Machine Learning
description: Azure Machine Learning で変換の適用モジュールを使用して、事前に計算された変換に基づいて入力データセットを変更する方法について説明します。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 10/26/2020
ms.openlocfilehash: a5db3935ae445ee7dcf8129eb1d4c75fcb64302f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "96003907"
---
# <a name="apply-transformation-module"></a>変換の適用モジュール

この記事では Azure Machine Learning デザイナーのモジュールについて説明します。

このモジュールを使用して、事前に計算された変換に基づいて入力データセットを変更します。 このモジュールは、推論パイプラインで変換を更新する必要がある場合に必要になります。

たとえば、**データの正規化** モジュールを使用してトレーニング データを正規化するために z スコアを使用した場合は、スコアリング フェーズ中も、トレーニング用に計算された z スコア値を使用することができます。 Azure Machine Learning では、正規化方法を変換として保存し、**変換の適用** を使用して、スコアリングの前の入力データに z スコアを適用することができます。

## <a name="how-to-save-transformations"></a>変換を保存する方法

デザイナーを使用すると、データ変換を **データセット** として保存し、他のパイプラインで使用できるようになります。

1. 正常に実行されたデータ変換モジュールを選択します。

1. **[出力 + ログ]** タブを選択します。

1. 変換出力を検索し、 **[データセットの登録]** を選択して、モジュール パレットの **[データセット]** カテゴリにモジュールとして保存します。

## <a name="how-to-use-apply-transformation"></a>変換の適用の使用方法  
  
1. **Apply Transformation (変換の適用)** モジュールをパイプラインに追加します。 このモジュールは、モジュール パレットの **[モデルのスコアリングと評価]** セクションにあります。 
  
1. モジュール パレットの **[データセット]** で使用する保存済みの変換を検出します。

1. 保存した変換の出力を **Apply Transformation (変換の適用)** の左側の入力ポートに接続します。

    そのデータセットには、この変換を最初に設計した対象データセットとまったく同じスキーマ (列数、列名、データ型) があるはずです。  
  
1. 目的のモジュールのデータセット出力を **Apply Transformation (変換の適用)** モジュールの右側の入力ポートに接続します。
  
1. 新しいデータセットに変換を適用するには、パイプラインを送信します。  

> [!IMPORTANT]
> トレーニング パイプラインで更新された変換が推論パイプラインでも適用可能であることを確認するには、トレーニング パイプラインで更新された変換が発生するたびに、次の手順に従う必要があります。
> 1. トレーニング パイプラインで、[[Select Columns Transform]\(列変換の選択)](select-columns-transform.md) の出力をデータセットとして登録します。
> ![モジュール出力のデータセットの登録](media/module/select-columns-transform-register-dataset.png)
> 1. 推論パイプラインで、**TD-** モジュールを削除し、前の手順で登録したデータセットで置き換えます。
> ![TD モジュールの置換](media/module/replace-tranformation-directory.png)

## <a name="next-steps"></a>次のステップ

Azure Machine Learning で[使用できる一連のモジュール](module-reference.md)を参照してください。 