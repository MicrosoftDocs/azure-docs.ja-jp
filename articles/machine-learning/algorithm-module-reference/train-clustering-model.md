---
title: クラスタリング モデルのトレーニング:モジュール リファレンス
titleSuffix: Azure Machine Learning service
description: Azure Machine Learning service で Train Clustering Model (クラスタリング モデルのトレーニング) モジュールを使用して、クラスタリング モデルをトレーニングする方法について説明します。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/06/2019
ms.openlocfilehash: 4883b1420913eb4e5f3bd5f13a95e410370d9184
ms.sourcegitcommit: 07700392dd52071f31f0571ec847925e467d6795
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/28/2019
ms.locfileid: "70128475"
---
# <a name="train-clustering-model"></a>クラスタリング モデルのトレーニング

この記事では、Azure Machine Learning service 用のビジュアル インターフェイス (プレビュー) のモジュールについて説明します。

このモジュールを使用してクラスタリング モデルをトレーニングします。

このモジュールは、[K-Means Clustering (K-Means クラスタリング)](k-means-clustering.md) モジュールを使用して既に構成済みのトレーニングされていないクラスタリング モデルを取得し、ラベル付きまたはラベルなしデータセットを使用してそのモデルをトレーニングします。 このモジュールは、予測に使用できるトレーニング済みモデルと、トレーニング データの各ケースに対するクラスター割り当てセットの両方を作成します。

> [!NOTE]
> 機械学習モデルをトレーニングするための汎用モジュールである [Train Model (モデルのトレーニング)](train-model.md) モジュールを使用して、クラスタリング モデルをトレーニングすることはできません。 これは、[Train Model (モデルのトレーニング)](train-model.md) が教師あり学習アルゴリズムでのみ動作するためです。 K 平均法と他のクラスタリング アルゴリズムは教師なし学習を可能にします。これは、アルゴリズムがラベルなしデータから学習できることを意味します。  
  
## <a name="how-to-use-train-clustering-model"></a>クラスタリング モデルのトレーニングの使用方法  
  
1.  **Train Clustering Model (クラスタリング モデルのトレーニング)** モジュールを Studio の自分の実験に追加します。 このモジュールは、**Machine Learning モジュール**の **[トレーニング]** カテゴリにあります。  
  
2. [K-Means Clustering (K-Means クラスタリング)](k-means-clustering.md) モジュール、または互換性のあるクラスタリング モデルを作成する別のカスタム モジュールを追加し、クラスタリング モデルのパラメーターを設定します。  
    
3.  **[Train Clustering Model]\(クラスタリング モデルのトレーニング\)** の右側の入力にトレーニング データセットをアタッチします。
  
5.  **[Column Set]\(列セット\)** で、クラスターの構築に使用するデータセットの列を選択します。 適切な特徴となる列を必ず選択してください。たとえば、一意の値を持つ ID やその他の列、またはすべて同じ値を持つ列は使用しないようにします。

    ラベルが使用可能な場合は、特徴として使用することも、除外することもできます。  
  
6. トレーニング データを新しいクラスター ラベルとともに出力する場合は、 **[Check for Append or Uncheck for Result Only]\(追加をチェックまたは結果のみをチェック解除\)** オプションを選択します。

    このオプションの選択を解除すると、クラスターの割り当てだけが出力されます。 

7. 実験を実行するか、**Train Clustering Model (クラスタリング モデルのトレーニング)** モジュールをクリックして **[選択した項目を実行]** を選択します。  
  
### <a name="results"></a>結果

トレーニングの完了後、次の作業を行います。


+  データセットの値を表示するには、モジュールを右クリックし、 **[Result datasets]\(結果データセット\)** を選択して **[可視化]** をクリックします。

+ トレーニング済みのモデルを保存して後で再利用できるようにするには、モジュールを右クリックし、 **[Trained model]\(トレーニング済みのモデル\)** を選択して **[Save As Trained Model]\(トレーニング済みのモデルとして保存する\)** をクリックします。

+ モデルからスコアを生成するには、「[クラスターへのデータの割り当て](assign-data-to-clusters.md)」を使用します。



## <a name="next-steps"></a>次の手順

Azure Machine Learning service で[使用できる一連のモジュール](module-reference.md)を参照してください。 