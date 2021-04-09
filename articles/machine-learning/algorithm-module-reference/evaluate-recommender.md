---
title: 'Evaluate Recommender (レコメンダーの評価): モジュール参照'
titleSuffix: Azure Machine Learning
description: Azure Machine Learning で Evaluate Recommender (レコメンダーの評価) モジュールを使用して、レコメンダー モデル予測の正確性を評価する方法について学習します。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 10/10/2019
ms.openlocfilehash: 0890e13acbba8dae31de28d7c78a81bd9b516853
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "76312262"
---
# <a name="evaluate-recommender"></a>レコメンダーの評価

この記事では、Azure Machine Learning デザイナーで Evaluate Recommender (レコメンダーの評価) モジュールを使用する方法について説明します。 目標は、レコメンデーション モデルによって行われた予測の正確性を測定することです。 このモジュールを使用すると、さまざまな種類のレコメンデーションを評価できます。  
  
-   ユーザーと項目に対して予測された評価    
-   ユーザーに対して推奨された項目  
  
レコメンデーション モデルを使用して予測を作成した場合、サポートされているこれらの予測の種類ごとに、返される結果が若干異なります。 Evaluate Recommender (レコメンダーの評価) モジュールでは、スコア付けされたデータセットの列形式から、予測の種類が推測されます。 たとえば、スコア付けされたデータセットには、次のものが含まれる可能性があります。

- ユーザー、項目、評価を表す 3 つの要素
- ユーザーと、そのユーザーに対して推奨された項目

また、このモジュールでは、実行される予測の種類に基づいて、適切なパフォーマンス メトリックが適用されます。 

  
## <a name="how-to-configure-evaluate-recommender"></a>Evaluate Recommender を構成する方法

Evaluate Recommender (レコメンダーの評価) モジュールでは、レコメンデーション モデルを使用して出力された予測が、対応する "グラウンド トゥルース" データと比較されます。 たとえば、[Score SVD Recommender (SVD レコメンダーのスコア付け)](score-svd-recommender.md) モジュールでは、Evaluate Recommender (レコメンダーの評価) を使用して分析できるスコア付けされたデータセットが生成されます。

### <a name="requirements"></a>必要条件

Evaluate Recommender (レコメンダーの評価) では、入力として次のデータセットが必要になります。 
  
#### <a name="test-dataset"></a>テスト データセット

テスト データセットには、ユーザー、項目、評価を表す 3 要素の形式で "グラウンド トゥルース" データが含まれています。  

#### <a name="scored-dataset"></a>スコア付けされたデータセット

スコア付けされたデータセットには、レコメンデーション モデルによって生成された予測が含まれています。  
  
この 2 つ目のデータセットに含まれる列は、スコアリング プロセス時に実行した予測の種類によって決まります。 たとえば、スコア付けされたデータセットには、次のいずれかが含まれている可能性があります。

- ユーザー、項目、およびユーザーが項目に対して与えると思われる評価
- 推奨されるユーザーと項目の一覧 

### <a name="metrics"></a>メトリック

モデルのパフォーマンス メトリックは、入力の種類に基づいて生成されます。 以降のセクションでは、詳細を説明します。

## <a name="evaluate-predicted-ratings"></a>予測された評価を評価する  

予測された評価を評価する場合、スコア付けされたデータセット (Evaluate Recommender (レコメンダーの評価) への 2 番目の入力) には、次の要件を満たす、ユーザー、項目、評価を表す 3 つの要素が含まれている必要があります。
  
-   データセットの最初の列に、ユーザー識別子が含まれている。    
-   2 番目の列に、項目の識別子が含まれている。  
-   3 番目の列に、対応するユーザーと項目の評価が含まれている。  
  
> [!IMPORTANT] 
> 評価を成功させるには、列名をそれぞれ `User`、`Item`、`Rating` とする必要があります。  
  
Evaluate Recommender (レコメンダーの評価) は、"グラウンド トゥルース" データセット内の評価を、スコア付けされたデータセットの予測済み評価と比較します。 その後、平均絶対誤差 (MAE) と二乗平均平方根誤差 (RMSE) を計算します。



## <a name="evaluate-item-recommendations"></a>項目のレコメンデーションを評価する

項目のレコメンデーションを評価する際には、各ユーザーに対して推奨された項目が含まれている、スコア付けされたデータセットを使用します。
  
-   データセットの最初の列には、ユーザー識別子が含まれている必要があります。    
-   後続のすべての列には、対応する推奨項目識別子が、項目とユーザーの関連度順で含まれている必要があります。 

このデータセットを接続する前に、最も関連度の高い項目が先頭に表示されるように、データセットを並べ替えることをお勧めします。  

> [!IMPORTANT] 
> Evaluate Recommender (レコメンダーの評価) を機能させるには、列名を `User`、`Item 1`、`Item 2`、`Item 3` のようにする必要があります。  
  
Evaluate Recommender (レコメンダーの評価) は、平均の正規化減損累積利得 (NDCG) を計算し、それを出力データセットに返します。  
  
推奨された項目に対する実際の "グラウンド トゥルース" を知ることはできないため、Evaluate Recommender (レコメンダーの評価) では NDCG を計算する際、テスト データセットに含まれるユーザーと項目の評価が利得として使用されます。 評価を実行するには、レコメンダーのスコアリング モジュールで、(テスト データセット内に) "グラウンド トゥルース" の評価がある項目についてのみ、レコメンデーションを生成する必要があります。  
  

## <a name="next-steps"></a>次のステップ

Azure Machine Learning で[使用できる一連のモジュール](module-reference.md)を参照してください。 
