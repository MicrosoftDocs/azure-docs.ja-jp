---
title: 'Train SVD Recommender (SVD レコメンダーのトレーニング): モジュール リファレンス'
titleSuffix: Azure Machine Learning
description: Azure Machine Learning で SVD レコメンダーのトレーニング モジュールと SVD アルゴリズムを使用してベイジアン レコメンダーをトレーニングする方法について説明します。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 03/17/2021
ms.openlocfilehash: 77407f253bb347160ea331bd7384d8085f21b040
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "104654460"
---
# <a name="train-svd-recommender"></a>SVD レコメンダーのトレーニング

この記事では、Azure Machine Learning デザイナーで Train SVD Recommender (SVD レコメンダーのトレーニング) モジュールを使用する方法について説明します。 このモジュールを使用すると、単一値分解 (SVD) アルゴリズムに基づいてレコメンデーション モデルをトレーニングできます。  

Train SVD Recommender (SVD レコメンダーのトレーニング) モジュールは、ユーザー、項目、評価を表す 3 つの要素からなるデータセットを読み取ります。 これにより、トレーニング済みの SVD レコメンダーが返されます。 その後、トレーニング済みのモデルを使用して、[Score SVD Recommender (SVD レコメンダーのスコア付け)](score-svd-recommender.md) モジュールを接続することで、評価の予測やレコメンデーションの生成ができます。  


  
## <a name="more-about-recommendation-models-and-the-svd-recommender"></a>推奨モデルと SVD レコメンダーの詳細  

レコメンデーション システムの主な目的は、システムの *ユーザー* に対して 1 つ以上の *項目* を推奨することです。 項目の例として、映画、レストラン、本、曲などがあります。 ユーザーは、個人、個人のグループ、または項目の好みがあるその他のエンティティにすることができます。  

レコメンダー システムには、主に 2 つのアプローチがあります。 

+ **コンテンツベース** のアプローチは、ユーザーと項目の両方の特徴を利用します。 ユーザーは、年齢や性別などのプロパティによって記述される場合があります。 項目は、作成者や製造元などのプロパティによって記述される場合があります。 コンテンツベースのレコメンデーション システムの一般的な例は、出会い系のサイトです。 
+ **協調フィルタリング** では、ユーザーと項目の識別子のみを使用します。 ユーザーが項目に対して指定した (疎な) 評価マトリックスから、これらのエンティティについての暗黙的な情報を取得します。 ユーザーについての情報は、そのユーザーが評価した項目や、同じ項目を評価した他のユーザーから学ぶことができます。  

SVD レコメンダーは、ユーザーと項目の識別子、およびユーザーが項目に対して指定した評価マトリックスを使用します。 つまり、これは "*協調レコメンダー*" です。 

SVD レコメンダーの詳細については、関連するリサーチ ペーパー「[レコメンダー システムの行列因子分解手法](https://datajobs.com/data-science-repo/Recommender-Systems-[Netflix].pdf)」を参照してください。


## <a name="how-to-configure-train-svd-recommender"></a>SVD レコメンダーのトレーニングを構成する方法  

### <a name="prepare-data"></a>データを準備する

このモジュールを使用する前に、実際の入力データがレコメンデーション モデルで想定している形式である必要があります。 ユーザー、項目、評価を表す 3 つの要素のトレーニング データセットが必要です。

+ 1 番目の列には、ユーザー識別子が含まれています。
+ 2 番目の列には、項目の識別子が含まれています。
+ 3 番目の列には、ユーザーと項目のペアの評価が含まれています。 評価の値は数値型にする必要があります。  

Azure Machine Learning デザイナーの **Movie Ratings** (映画の評価) データセット ( **[Datasets]\(データセット\)** 、 **[サンプル]** の順に選択) では、想定される形式を示しています。

![映画の評価](media/module/movie-ratings-dataset.png)

このサンプルから、1 人のユーザーが複数の映画を評価したことがわかります。 

### <a name="train-the-model"></a>モデルをトレーニングする

1.  デザイナー内で Train SVD Recommender (SVD レコメンダーのトレーニング) モジュールを自分のパイプラインに追加し、トレーニング データに接続します。  
   
2.  **[Number of factors]\(因子の数\)** には、レコメンダーで使用する因子の数を指定します。  
    
    各因子は、その項目にユーザーがどの程度関係しているかを測定します。 因子の数は、潜在因子空間の次元でもあります。 ユーザーと項目の数が増加する場合、より多くの因子を設定することをお勧めします。 ただし、数が大きすぎると、パフォーマンスが低下する可能性があります。
    
3.  **[Number of recommendation algorithm iterations]\(レコメンデーション アルゴリズム イテレーション数\)** は、アルゴリズムが入力データを処理する回数を示します。 この数値が大きいほど、予測が正確になります。 ただし、数値が大きくなると、トレーニングは遅くなります。 既定値は 30 です。

4.  学習時のステップ サイズを定義する 0.0 から 2.0 までの数値を **[Learning rate]\(学習率\)** に入力します。

    学習率は、各イテレーションのステップのサイズを決定します。 ステップ サイズが大きすぎると、最適解から離れていってしまう可能性があります。 ステップ サイズが小さすぎると、トレーニングで最適解を見つけ出すまでの時間が長くなります。 
  
5.  パイプラインを送信します。  

## <a name="results"></a>結果

パイプラインの実行が完了した後、モデルをスコアリングに使用するには、[Train SVD Recommender (SVD レコメンダーのトレーニング)](train-svd-recommender.md) を [Score SVD Recommender (SVD レコメンダーのスコア付け)](score-svd-recommender.md) に接続し、新しい入力例の値を予測します。

## <a name="next-steps"></a>次のステップ

Azure Machine Learning で[使用できる一連のモジュール](module-reference.md)を参照してください。 
