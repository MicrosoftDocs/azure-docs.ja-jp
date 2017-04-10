---
title: "手順 1: Machine Learning ワークスペースの作成 | Microsoft Docs"
description: "予測ソリューションの開発に関するチュートリアルの中の、保存されているパブリック データを Azure Machine Learning Studio ワークスペースを設定する方法の 1 番目の手順です。"
services: machine-learning
documentationcenter: 
author: garyericson
manager: jhubbard
editor: cgronlun
ms.assetid: b3c97e3d-16ba-4e42-9657-2562854a1e04
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/23/2017
ms.author: garye
translationtype: Human Translation
ms.sourcegitcommit: 4f2230ea0cc5b3e258a1a26a39e99433b04ffe18
ms.openlocfilehash: 8ca42ef8f5314866301f5c9e93caa90dc837a66e
ms.lasthandoff: 03/25/2017


---
# <a name="walkthrough-step-1-create-a-machine-learning-workspace"></a>チュートリアル手順 1: Machine Learning ワークスペースの作成
これは、「[チュートリアル: 信用リスク評価のための予測分析ソリューションを Azure Machine Learning で開発する](machine-learning-walkthrough-develop-predictive-solution.md)」の最初の手順です。

1. **Machine Learning ワークスペースの作成**
2. [既存のデータをアップロードする](machine-learning-walkthrough-2-upload-data.md)
3. [新しい実験を作成する](machine-learning-walkthrough-3-create-new-experiment.md)
4. [モデルをトレーニングして評価する](machine-learning-walkthrough-4-train-and-evaluate-models.md)
5. [Web サービスをデプロイする](machine-learning-walkthrough-5-publish-web-service.md)
6. [Web サービスにアクセスする](machine-learning-walkthrough-6-access-web-service.md)

- - -
<!-- This needs to be updated to refer to the new way of creating workspaces in the Ibiza portal -->

Machine Learning Studio を使用するには、Microsoft Azure Machine Learning ワークスペースが必要です。 このワークスペースには、実験を管理および公開するのに必要なツールが用意されています。  

<!--
## To create a workspace
1. Sign in to the [Azure classic portal](https://manage.windowsazure.com).
2. In the  Azure services panel, click **MACHINE LEARNING**.  
   ![Create workspace][1]
3. Click **CREATE AN ML WORKSPACE**.
4. On the **QUICK CREATE** page, enter your workspace information and then click **CREATE AN ML WORKSPACE**.
-->

ワークスペースの作成と、所有者または共同作成者としてのユーザーの追加は、Azure サブスクリプションの管理者が実行する必要があります。 詳細については、「[Azure Machine Learning ワークスペースの作成と共有](machine-learning-create-workspace.md)」を参照してください。

ワークスペースが作成された後、Machine Learning Studio ([https://studio.azureml.net/Home](https://studio.azureml.net/Home)) を開きます。 ワークスペースが複数ある場合は、ウィンドウの右上隅のツールバーでワークスペースを選択できます。

![Studio でのワークスペースの選択][2]

> [!TIP]
> 自分がワークスペースの所有者になっている場合は、自分以外のユーザーを後からワークスペースに招待することで、作業中の実験を共有することができます。 これは、Machine Learning Studio の **[設定]** ページから実行でき、 必要な情報は各ユーザーの Microsoft アカウントまたは組織アカウントだけです。
> 
> **[設定]** ページで **[ユーザー]** をクリックします。次に、ウィンドウの下部にある **[INVITE MORE USERS]** をクリックします。
> 
> 

- - -
**次: [既存のデータをアップロードする](machine-learning-walkthrough-2-upload-data.md)**

[1]: ./media/machine-learning-walkthrough-1-create-ml-workspace/create1.png
[2]: ./media/machine-learning-walkthrough-1-create-ml-workspace/open-workspace.png

