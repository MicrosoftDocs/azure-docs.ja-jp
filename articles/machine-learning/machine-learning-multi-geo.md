---
title: "複数の地理的リージョンに関するヘルプ ドキュメント | Microsoft Docs"
description: "ワークスペースを作成し、米国中南部 (SCUS) Azure リージョン以外の Azure リージョンに Web サービスを発行する方法について学習します。"
services: machine-learning
documentationcenter: 
author: tedway
manager: jhubbard
editor: rmca14
tags: 
ms.assetid: ed0ca8a8-fa53-4e56-b824-2d7e44641967
ms.service: machine-learning
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 4/6/2017
ms.author: tedway; neerajkh
translationtype: Human Translation
ms.sourcegitcommit: ec9d29701915d237686625fbc3abec9827fda467
ms.openlocfilehash: 22c30bfea89e3c650d81e1ebae20c9d151e6f36c
ms.lasthandoff: 12/10/2016


---
# <a name="multi-geo-help-documentation"></a>複数の地理的リージョンに関するヘルプ ドキュメント
この記事では、ワークスペースを作成し、別の Azure リージョンに Web サービスを発行する方法について説明します。  [[リージョン別の Azure 製品] ページ](https://azure.microsoft.com/en-us/regions/services/)には、Azure Machine Learning を利用できるリージョンが一覧表示されます。

## <a name="create-a-workspace"></a>ワークスペースの作成
1. Azure クラシック ポータルにサインインします。
2. **[+ 新規]** > **[DATA SERVICES]** > **[MACHINE LEARNING]** > **[簡易作成]** をクリックします。  **[場所]** で、**[東南アジア]** など別のリージョンを選択します。
   ![複数の地理的リージョンに関するヘルプ 画像 1][1]
3. ワークスペースを選択し、 **[ML Studio にサインイン]**をクリックします。
   ![複数の地理的リージョンに関するヘルプ 画像 2][2]
4. これで別のリージョンにワークスペースが作成され、他のワークスペースと同じように使用できます。 ワークスペースは画面の右上で切り替えます。 ドロップダウンをクリックし、リージョンを選択して、ワークスペースを選択します。 すべてのものはワークスペース リージョンのローカルに置かれます。  たとえば、ワークスペースから作成されたすべての Web サービスは、ワークスペースと同じリージョンに置かれます。
   ![複数の地理的リージョンに関するヘルプ画像 3][3]

## <a name="open-an-experiment-from-gallery"></a>ギャラリーから実験を開く
ギャラリーから実験を開く場合、その実験をコピーするリージョンを選択することもできます。

![複数の地理的リージョンに関するヘルプ 画像 4][4a]

## <a name="web-service-management"></a>Web サービスの管理
再トレーニング用のサービスなどの Web サービスをプログラムによって管理するには、リージョン固有のアドレスを使用します。

* https://asiasoutheast.management.azureml.net
* https://europewest.management.azureml.net

### <a name="things-to-note"></a>注意する点
1. 同じリージョンに属しているワークスペース間でのみ実験をコピーできます。 リージョンが異なるワークスペース間で実験をコピーする必要がある場合は、[PowerShell](http://aka.ms/amlps) コマンドレットの [*Copy-AmlExperiment*](https://github.com/hning86/azuremlps/blob/master/README.md#copy-amlexperiment) を使用してこれを行うことができます。 別の方法として、一覧にないモードで実験をギャラリーに発行し、他のリージョンのワークスペースで実験を開きます。
2. リージョン セレクターには、一度に&1; つのリージョンのワークスペースのみが表示されます。  
3. フリー ワークスペース、またはゲスト アクセス (匿名の) ワークスペースは米国中南部に作成およびホストされます。  
4. 東南アジアのワークスペースからデプロイされた Web サービスは、東南アジアにもホストされます。  

## <a name="more-information"></a>詳細情報
質問がある場合は [Azure Machine Learning のフォーラム](https://social.msdn.microsoft.com/Forums/azure/home?forum=MachineLearning)に投稿してください。

<!--Image references-->
[1]: ./media/machine-learning-multi-geo/multi-geo_1.png
[2]: ./media/machine-learning-multi-geo/multi-geo_2.png
[3]: ./media/machine-learning-multi-geo/multi-geo_3.png
[4a]: ./media/machine-learning-multi-geo/multi-geo_4a.png

