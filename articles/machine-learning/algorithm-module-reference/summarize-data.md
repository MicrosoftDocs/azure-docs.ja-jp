---
title: データの集計
titleSuffix: Azure Machine Learning
description: Azure Machine Learning の Summarize Data (データの要約) モジュールを使用して、データセット内の列に関する基本的な記述統計レポートを生成する方法について説明します。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 01/27/2020
ms.openlocfilehash: 5206565b85d1551e5e551f1dfe75d28c93bc53f0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "90898206"
---
# <a name="summarize-data"></a>データの集計

この記事では、Azure Machine Learning デザイナーのモジュールについて説明します。

Summarize Data (データの要約) モジュールを使用して、入力テーブル内の各列を記述する一連の標準的な統計的尺度を作成します。

要約統計は、完全なデータセットの特性を理解するのに役立ちます。 たとえば、次のことを知る必要がある場合があります。

- 各列で欠落している値の数はいくつあるか。
- 特徴列にある一意の値の数はいくつか。
- 各列の平均偏差と標準偏差はいくつか。

このモジュールは、各列の重要なスコアを計算し、入力として提供された各変数 (データ列) の要約統計の行を返します。

## <a name="how-to-configure-summarize-data"></a>Summarize Data (データの要約) を構成する方法  

1. **Summarize Data (データの要約)** モジュールをパイプラインに追加します。 このモジュールは、デザイナーの **[Statistical Functions]\(統計関数\)** カテゴリにあります。

1. レポートを生成する対象のデータセットを接続します。

    一部の列のみに関するレポートを作成する場合は、[Select Columns in Dataset (データセット内の列の選択)](select-columns-in-dataset.md) モジュールを使用して、使用する列のサブセットを指定します。

1. 追加のパラメーターは必要ありません。 既定では、モジュールは入力として指定されたすべての列を分析し、列内の値の型に応じて、「[結果](#results)」セクションで説明されているように、関連する統計のセットを出力します。

1. パイプラインを送信します。

## <a name="results"></a>結果

モジュールからのレポートには、次の統計情報を含めることができます。 

|列名|説明|
|------|------|  
|**機能**|列の名前|
|**Count**|すべての行の数|
|**Unique Value Count (一意の値の数)**|列内の一意の値の数|
|**Missing Value Count (欠損値の数)**|列内の一意の値の数|
|**Min (最小値)**|列内の最小値|  
|**Max (最大値)**|列内の最大値|
|**Mean (平均値)**|すべての列値の平均値|
|**Mean Deviation (平均偏差)**|列値の平均偏差|
|**1st Quartile (第 1 四分位数)**|第 1 四分位数の値|
|**中央値**|中央の列値|
|**3rd Quartile (第 3 四分位数)**|第 3 四分位数の値|
|**モード**|列値のモード|
|**Range**|最大値と最小値の間の値の数を表す整数|
|**Sample Variance (サンプル分散)**|列の分散。注を参照。|
|**Sample Standard Deviation (サンプル標準偏差)**|列の標準偏差。注を参照。|
|**Sample Skewness (サンプル歪度)**|列の歪度。注を参照。|
|**Sample Kurtosis (サンプル尖度)**|列の尖度。注を参照。|
|**P0.5**|0.5% パーセンタイル|
|**P1**|1% パーセンタイル|
|**P5**|5% パーセンタイル|
|**P95**|95% パーセンタイル|
|**P99.5**|99.5% パーセンタイル |

## <a name="technical-notes"></a>テクニカル ノート

- 数値以外の列では、数、一意の値の数、欠損値の数の値のみが計算されます。 他の統計では、null 値が返されます。

- ブール値を含む列は、これらのルールを使用して処理されます。

    - 最小の計算時には論理 AND が適用されます。
    
    - 最大の計算時には論理 OR が適用されます。
    
    - 範囲の計算時、モジュールはまず、列の一意の値の数が 2 と等しいかどうかを確認します。
    
    - 浮動小数点演算が必要なすべての統計の計算時には、True の値が 1.0、False の値が 0.0 として扱われます。

## <a name="next-steps"></a>次のステップ

Azure Machine Learning で[使用できる一連のモジュール](module-reference.md)を参照してください。  
