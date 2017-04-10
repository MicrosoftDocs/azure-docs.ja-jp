---
title: "(非推奨) R で構築された Machine Learning Web サービスの例 - Azure | Microsoft Docs"
description: "(非推奨) R コードと Machine Learning を使用して作成され、Microsoft Azure Marketplace に発行された便利な Web サービスの例を参照できます。"
keywords: "csharp、r コード、Web サービスの例"
services: machine-learning
documentationcenter: 
author: jaymathe
manager: jhubbard
editor: cgronlun
ms.assetid: 97d66cb7-6a84-4ae9-8095-0b5f5ba82d7f
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: deprecated
ms.date: 01/06/2017
ms.author: jaymathe
ROBOTS: NOINDEX, NOFOLLOW
translationtype: Human Translation
ms.sourcegitcommit: f6ad106e769c807d1c281c8d19127eabc2048f30
ms.openlocfilehash: 303fb71faf73d4a1b6d17b4ca66b83b15157332b
ms.lasthandoff: 01/11/2017


---
# <a name="deprecated-web-services-examples-using-r-code-on-azure-machine-learning-and-published-to-microsoft-azure-marketplace"></a>(非推奨) Azure Machine Learning で R コードを使用して構築され、Microsoft Azure Marketplace に発行されたサンプル Web サービス

> [!NOTE]
> Microsoft DataMarket は間もなく提供終了となる予定です。これらの API は推奨されていません。 
> 
> [Cortana Intelligence ギャラリー](http://gallery.cortanaintelligence.com)では、便利なサンプル実験や API が提供されています。 ギャラリーについて詳しくは、「 [Cortana Intelligence ギャラリーでリソースを共有および発見する](machine-learning-gallery-how-to-use-contribute-publish.md)」をご覧ください。

この記事では、Azure Machine Learning を使用して作成され、Microsoft Azure Marketplace に発行された Web サービスの例を参照できます。 各 Web サービスの例には、サービスをテストするためのサンプル データ セットが添付され、同様のサービスをユーザーが作成するための方法を説明した包括的なドキュメントが用意されています。 

Azure Machine Learning Studio では、ユーザーが R コードを記述し、数回クリックするだけで、そのコードを Web サービスとして発行でき、世界中の他のユーザーがさまざまなアプリケーションやデバイスで使用できるようになります。 

[!INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

統計機能を提供する簡単な計算の生成からカスタム テキスト マイニングのセンチメント分析の予測まで、Azure Machine Learning のユーザーが R コードを操作できることは、未経験の R ユーザーと経験のある R ユーザーの両方にとってメリットがあります。 これらの Web サービスは、モバイル アプリまたは Web サイトからユーザーが使用できますが、これらの Web サービスには、Azure Machine Learning が分析目的でどのように R スクリプトを操作し、R コードでどのように Web サービスを作成するかの例を示すという目的もあります。

各例には Web サービスを使用する C# コードが含まれます。

![Azure Machine Learning の R コードの図: 独占使用または Azure Marketplace に発行される R ソリューション][1]

次のシナリオで考えてみましょう。

## <a name="scenario-1-generic-model"></a>シナリオ 1: 汎用モデル
ユーザーは、時系列データの基本的な予測、高度な分析を行うカスタム ビルドの R メソッドなど、新しいユーザーのデータに適用できる汎用モデルを使用できます。 このユーザーは、他のユーザーが自分のデータで使用できるように Web サービスとしてモデルを公開します。

* [バイナリ分類器](machine-learning-r-csharp-binary-classifier.md)
* [クラスター モデル](machine-learning-r-csharp-cluster-model.md)
* [多変量線形回帰](machine-learning-r-csharp-multivariate-linear-regression.md)
* [予測指数平滑法](machine-learning-r-csharp-forecasting-exponential-smoothing.md)
* [ETS + STL 予測](machine-learning-r-csharp-retail-demand-forecasting.md)
* [予測 - 自己回帰統合の移動平均 (ARIMA)](machine-learning-r-csharp-arima.md)
* [生存分析](machine-learning-r-csharp-survival-analysis.md)

## <a name="scenario-2-trained-model--specific-data"></a>シナリオ 2: トレーニング済みのモデル – 特定のデータ
ユーザーは、ユーザーの性格のタイプを予測するための k 平均法アルゴリズムからクラスター化した多数の性格アンケートの例、個々の肺がんリスクを予測する健康調査データなど、R コードによる有用な予測を提供するデータを持っています。 ユーザーは、新しいユーザーの結果を予測する Web サービスを介してデータを公開します。

## <a name="scenario-3-trained-model--generic-data"></a>シナリオ 3: トレーニング済みのモデル – 汎用データ
ユーザーには、さまざまな使用例やシナリオに適用できる Web サービスを構築できる汎用データ (テキスト コーパスなど) があります。

* [辞書ベースのセンチメント分析](machine-learning-r-csharp-lexicon-based-sentiment-analysis.md)

## <a name="scenario-4-advanced-calculator"></a>シナリオ 4: 高度な計算
ユーザーは、任意のトレーニング済みのモデルやユーザー データへのモデルの適合を必要としない、高度な計算またはシミュレーションを提供します。

* [比率検定での相違](machine-learning-r-csharp-difference-in-two-proportions.md)
* [正規分布スイート](machine-learning-r-csharp-normal-distribution.md)
* [二項分布スイート](machine-learning-r-csharp-binomial-distribution.md)

## <a name="faq"></a>FAQ
Web サービスの使用や Marketplace への発行に関するよく寄せられる質問については、 [ここ](machine-learning-marketplace-faq.md)をご覧ください。

[1]: ./media/machine-learning-r-csharp-web-service-examples/machine-learning-r-code-options-for-using-and-sharing-cloud.png




