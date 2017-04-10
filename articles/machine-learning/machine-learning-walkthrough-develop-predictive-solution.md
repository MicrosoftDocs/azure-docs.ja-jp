---
title: "信用リスク評価のための予測分析ソリューションを Machine Learning で開発する | Microsoft Docs"
description: "信用リスク評価のための予測分析ソリューションを Azure Machine Learning で開発する方法を詳しく紹介したチュートリアルです。"
keywords: "信用リスク, 予測分析ソリューション,リスク評価"
services: machine-learning
documentationcenter: 
author: garyericson
manager: jhubbard
editor: cgronlun
ms.assetid: 43300854-a14e-4cd2-9bb1-c55c779e0e93
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 03/23/2017
ms.author: garye
translationtype: Human Translation
ms.sourcegitcommit: b652c0f817e5e56d4ff50701345b03db634f616c
ms.openlocfilehash: 043c54094f53ae539c833eb8f167201781c677a6
ms.lasthandoff: 02/17/2017


---
# <a name="walkthrough-develop-a-predictive-analytics-solution-for-credit-risk-assessment-in-azure-machine-learning"></a>チュートリアル: 信用リスク評価のための予測分析ソリューションを Azure Machine Learning で開発する

このチュートリアルでは、Machine Learning Studio で予測分析ソリューションを開発するプロセスについて詳しく説明します。 Machine Learning Studio でシンプルなモデルを開発し、Machine Learning Web サービスとしてデプロイします。このサービスでは、モデルが新しいデータを使用して予測を行うことができます。 

このチュートリアルでは、ユーザーがこれまでに少なくとも&1; 回は Machine Learning Studio を使用したことがあり、機械学習の概念をある程度理解していることを前提としていますが、 いずれにしても専門家ではないことを想定しています。

**Azure Machine Learning Studio** をまだ使用したことがない場合は、チュートリアル「[Azure Machine Learning Studio で初めてのデータ サイエンス実験を作成する](machine-learning-create-experiment.md)」から始めることをお勧めします。 そのチュートリアルでは、Machine Learning Studio を初めて使用する場合の手順を示しながら、 実験にモジュールをドラッグ アンド ドロップして互いに結び付け、実験を実行して結果を確認する方法の基本について説明します。 また、Machine Learning Studio の機能の概要を示すダイアグラムも、作業を開始するうえで役に立つでしょう。 このダイアグラムは、「[Azure Machine Learning Studio 機能の概要図](machine-learning-studio-overview-diagram.md)」からダウンロードして印刷することができます。
 
機械学習についてよく知らない場合は、まずビデオ シリーズをご覧になることをお勧めします。 これは「[初心者向けデータ サイエンス](machine-learning-data-science-for-beginners-the-5-questions-data-science-answers.md)」と呼ばれるビデオ シリーズで、日常的な言葉と概念が使用されており、機械学習の導入として優れています。


[!INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]
 

## <a name="the-problem"></a>問題

クレジットの申請書に記入する情報に基づいて個人のクレジット リスクを予測する必要があるとします。  

クレジット リスクの評価は複雑な問題ですが、このチュートリアルでは少し簡略化できます。 それを、Microsoft Azure Machine Learning を使用して予測分析ソリューションを作成する方法の例として取り上げます。 これを行うために、Azure Machine Learning Studio と Machine Learning Web サービスを使用します。  

## <a name="the-solution"></a>解決策

この詳細なチュートリアルでは、公開されているクレジット リスク データを使用し、そのデータに基づいた予測モデルを開発してトレーニングします。 その後、他のユーザーがクレジット リスクの評価に使用できるように、開発したモデルを Web サービスとしてデプロイします。

このクレジット リスク評価ソリューションを作成するには、次の手順に従います。  

1. [Machine Learning ワークスペースの作成](machine-learning-walkthrough-1-create-ml-workspace.md)
2. [既存のデータをアップロードする](machine-learning-walkthrough-2-upload-data.md)
3. [実験を作成する](machine-learning-walkthrough-3-create-new-experiment.md)
4. [モデルをトレーニングして評価する](machine-learning-walkthrough-4-train-and-evaluate-models.md)
5. [Web サービスをデプロイする](machine-learning-walkthrough-5-publish-web-service.md)
6. [Web サービスにアクセスする](machine-learning-walkthrough-6-access-web-service.md)

> [!TIP] 
> [Cortana Intelligence ギャラリー](https://gallery.cortanaintelligence.com)には、このチュートリアルで開発する実験の作業用コピーがあります。 「**[Walkthrough - Credit risk prediction (チュートリアル - クレジット リスクの予測)](https://gallery.cortanaintelligence.com/Experiment/Walkthrough-Credit-risk-prediction-1)**」にアクセスし、**[Open in Studio (Studio で開く)]** をクリックして Machine Learning Studio ワークスペースに実験のコピーをダウンロードしてください。
> 
> このチュートリアルは、[ギャラリー](http://gallery.cortanaintelligence.com/)にもある「[Binary Classfication: Credit risk prediction (二項分類: クレジット リスクの予測)](http://go.microsoft.com/fwlink/?LinkID=525270)」のサンプル実験の簡略版に基づいています。

