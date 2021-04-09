---
title: 行の追加:モジュール リファレンス
titleSuffix: Azure Machine Learning
description: Azure Machine Learning デザイナーで行の追加モジュールを使用して、2 つのデータセットを連結する方法について説明します。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 02/22/2020
ms.openlocfilehash: 2c2a74ee88e7161ab8dd1701ef4d105df67c0938
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "93421942"
---
# <a name="add-rows-module"></a>Add Rows (行の追加) モジュール

この記事では Azure Machine Learning デザイナーのモジュールについて説明します。

このモジュールを使用して、2 つのデータセットを連結します。 連結では、2 番目のデータセットの行が最初のデータセットの末尾に追加されます。  
  
行の連結は、次のようなシナリオで役立ちます。  
  
+ 一連の評価統計を生成した後、レポート作成を容易にするためにそれらを 1 つの表に結合する。  
  
+ さまざまなデータセットを使用していて、それらのデータセットを結合して最終的なデータセットを作成する。  

## <a name="how-to-use-add-rows"></a>行の追加の使用方法  

2 つのデータセットの行を連結するには、それぞれの行のスキーマがまったく同じである必要があります。 つまり、列の数と列内のデータの型が同じである必要があります。

1.  **Add Rows (行の追加)** モジュールをパイプラインにドラッグします。これは **[データ変換]** の下で見つけることができます。

2. データセットを 2 つの入力ポートに接続します。 追加するデータセットは、2 番目 (右) のポートに接続する必要があります。 
  
3.  パイプラインを送信します。 出力データセットの行数は、両方の入力データセットの行の合計に等しくなければなりません。

    **Add Rows (行の追加)** モジュールの両方の入力に同じデータセットを追加すると、データセットは複製されます。 

## <a name="next-steps"></a>次のステップ

Azure Machine Learning で[使用できる一連のモジュール](module-reference.md)を参照してください。 