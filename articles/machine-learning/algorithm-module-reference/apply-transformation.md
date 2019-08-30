---
title: 変換の適用:モジュール リファレンス
titleSuffix: Azure Machine Learning service
description: Azure Machine Learning service で変換の適用モジュールを使用して、事前に計算された変換に基づいて入力データセットを変更する方法について説明します。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ms.openlocfilehash: 8b4ec9e71d9ce932a7b206467db91760f4a4fc47
ms.sourcegitcommit: 07700392dd52071f31f0571ec847925e467d6795
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/28/2019
ms.locfileid: "70128980"
---
# <a name="apply-transformation-module"></a>変換の適用モジュール

この記事では、Azure Machine Learning service 用のビジュアル インターフェイス (プレビュー) のモジュールについて説明します。

このモジュールを使用して、事前に計算された変換に基づいて入力データセットを変更します。  
  
たとえば、**データの正規化**モジュールを使用してトレーニング データを正規化するために z スコアを使用した場合は、スコアリング フェーズ中も、トレーニング用に計算された z スコア値を使用することができます。 Azure Machine Learning では、正規化方法を変換として保存し、**変換の適用**を使用して、スコアリングの前の入力データに z スコアを適用することができます。
  
Azure Machine Learning には、作成に関するサポート、およびさまざまな種類のカスタム変換の適用に関するサポートが用意されています。 たとえば、変換を保存し再使用して、次のことを実行できます。  
  
- **[見つからないデータのクリーンアップ]** を使用して、欠落している値を削除したり置換する
- **[データの正規化]** を使用してデータを正規化する
  

## <a name="how-to-use-apply-transformation"></a>変換の適用の使用方法  
  
1. **変換の適用**モジュールを実験に追加します。 このモジュールは、**Machine Learning** の **[スコア]** カテゴリにあります。 
  
2. 入力として使用する既存の変換を見つけます。  以前に保存した変換は、左側のナビゲーション ウィンドウの **[変換]** グループにあります。  
  
   
  
3. 変換するデータセットを接続します。 そのデータセットには、この変換を最初に設計した対象データセットとまったく同じスキーマ (列数、列名、データ型) があるはずです。  
  
4. 変換を定義しているときにすべてのカスタマイズが行われているため、他のパラメーターを設定する必要はありません。  
  
5. 新しいデータセットに変換を適用するには、実験を実行します。  

## <a name="next-steps"></a>次の手順

Azure Machine Learning service で[使用できる一連のモジュール](module-reference.md)を参照してください。 