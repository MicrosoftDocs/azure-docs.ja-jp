---
title: クラスターへのデータの割り当て:モジュール リファレンス
titleSuffix: Azure Machine Learning service
description: Azure Machine Learning service で Assign Data to Cluster (クラスターへのデータの割り当て) モジュールを使用して、クラスタリング モデルをスコア付けする方法について説明します。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/06/2019
ms.openlocfilehash: 56f608044716ea3655576c11aa7a62343215f508
ms.sourcegitcommit: 07700392dd52071f31f0571ec847925e467d6795
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/28/2019
ms.locfileid: "70128988"
---
# <a name="module-assign-data-to-clusters"></a>モジュール:クラスターへのデータの割り当て

この記事では、Azure Machine Learning のビジュアル インターフェイスで *Assign Data to Clusters (データのクラスターへの割り当て)* モジュールを使用する方法について説明します。 このモジュールでは、*K-Means クラスタリング* アルゴリズムでトレーニングされたクラスタリング モデルを介して予測を生成します。

Assign Data to Clusters (データのクラスターへの割り当て) モジュールは、新しい各データ ポイントの推定の割り当てを含むデータセットを返します。 


## <a name="how-to-use-assign-data-to-clusters"></a>クラスターへのデータの割り当てを使用する方法
  
1. Azure Machine Learning のビジュアル インターフェイスで、以前にトレーニングしたクラスタリング モデルを見つけます。 以下のいずれかの方法を使用して、クラスタリング モデルを作成およびトレーニングできます。  
  
    - [K-Means Clustering (K-Means クラスタリング)](k-means-clustering.md) モジュールを使用して K-Means クラスタリング アルゴリズムを構成し、データセットと Train Clustering Model (クラスタリング モデルのトレーニング) モジュール (この記事) を使用してモデルをトレーニングします。  
  
    - また、トレーニング済みの既存のクラスタリング モデルを、ワークスペースの **[Saved Models]\(保存済みのモデル\)** グループから追加することもできます。

2. トレーニング済みのモデルを **Assign Data to Clusters** (クラスターへのデータの割り当て) の左側の入力ポートにアタッチします。  

3. 新しいデータセットを入力としてアタッチします。 

   このデータセットでは、ラベルは省略可能です。 一般に、クラスタリングは教師なしの学習方法です。 事前にカテゴリを知ることは想定されていません。 ただし、入力列はクラスタリング モデルのトレーニングで使用された列と同じである必要があり、同じでないとエラーが発生します。

    > [!TIP]
    > クラスター予測からインターフェイスに書き込まれる列数を減らすには、[[Select columns in the dataset]\(データセット内の列の選択\)](select-columns-in-dataset.md) を使用して列の一部を選択します。 
    
4. 結果 (クラスターの割り当て) を表示する列を含め、入力データセット全体を結果に含める場合は、 **[Check for Append or Uncheck for Result Only]\(追加についてはオン、または結果のみについてはオフにする\)** チェック ボックスをオンのままにします。
  
    このチェック ボックスをオフにすると、結果のみが返されます。 このオプションは、Web サービスの一部として予測を作成するときに役立ちます。
  
5.  実験を実行します。  
  
### <a name="results"></a>結果

+  データセットの値を表示するには、モジュールを右クリックし、 **[Result datasets]\(結果データセット\)** を選択してから **[視覚化]** を選択します。

