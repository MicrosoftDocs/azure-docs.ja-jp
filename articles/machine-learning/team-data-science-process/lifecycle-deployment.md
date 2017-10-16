---
title: "Team Data Science Process ライフサイクルのデプロイ ステージ - Azure | Microsoft Docs"
description: "データ サイエンス プロジェクトのデプロイ ステージの目標、タスク、成果物。"
services: machine-learning
documentationcenter: 
author: bradsev
manager: cgronlun
editor: cgronlun
ms.assetid: 
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/02/2017
ms.author: bradsev;
ms.openlocfilehash: b49b3ab696c22928c5f5a4566e059345fd810588
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="deployment"></a>デプロイ

このトピックでは、Team Data Science Process の**デプロイ ステージ**に関連付けられている目標、タスク、成果物のアウトラインを示します。 このプロセスには、データ サイエンス プロジェクトを体系化するために使用できる推奨ライフサイクルが用意されています。 ライフサイクルは、プロジェクトで通常 (多くの場合に繰り返し) 実行される主要なステージのアウトラインを示します。

* **ビジネスの把握**
* **データの取得と理解**
* **モデリング**
* **デプロイ**
* **顧客による受け入れ**

次の図は、**Team Data Science Process ライフサイクル**を視覚的に表したものです。 

![TDSP-Lifecycle2](./media/lifecycle/tdsp-lifecycle2.png) 


## <a name="goal"></a>目標
* データ パイプラインのあるモデルは、最終ユーザーによる受け入れに向けて、運用環境または運用環境に似た環境にデプロイされます。 

## <a name="how-to-do-it"></a>方法
このステージでは、以下に示す主な課題に取り組みます。

* **モデルの運用化**: モデルとパイプラインを、アプリケーションで利用できるように運用環境または運用環境に似た環境にデプロイします。

### <a name="41-operationalize-a-model"></a>4.1 モデルの運用化
うまく機能する一連のモデルが得られたら、他のアプリケーションから利用できるように、それらのモデルを運用できる状態にします。 予測は、ビジネス要件に応じてリアルタイムまたはバッチ処理で行われます。 モデルは、オープン API インターフェイスで公開することでデプロイされます。 インターフェイスにより、オンライン Web サイト、スプレッドシート、ダッシュボード、基幹業務アプリケーション、バックエンド アプリケーションなどの各種のアプリケーションからモデルを利用しやすくなります。 Azure Machine Learning Web サービスによるモデルの運用化の例については、「[Azure Machine Learning Web サービスをデプロイする](../studio/publish-a-machine-learning-web-service.md)」を参照してください。 また、テレメトリと監視をデプロイ済みの運用モデルとデータ パイプラインに組み込むこともベスト プラクティスです。 これは、その後のシステム状態のレポートとトラブルシューティングに役立ちます。  

## <a name="artifacts"></a>アーティファクト
* システムの正常性と主なメトリックについてのステータス ダッシュボード。
* デプロイの詳細を含む最終的なモデリング レポート。
* 最終的なソリューション アーキテクチャ ドキュメント。


## <a name="next-steps"></a>次のステップ

Team Data Science Process のライフサイクルの各ステップへのリンクを次に示します。

* [1.ビジネスの把握](lifecycle-business-understanding.md)
* [2.データの取得と理解](lifecycle-data.md)
* [3.モデリング](lifecycle-modeling.md)
* [4.デプロイ](lifecycle-deployment.md)
* [5.顧客による受け入れ](lifecycle-acceptance.md)

また、 **特定のシナリオ** のプロセスに伴うすべての段階をリハーサル的に最初から最後まで実証することも可能です。 これらは、[サンプル チュートリアル](walkthroughs.md)のトピックで簡単な説明と共にリンク付きで紹介されています。 チュートリアルでは、クラウド、オンプレミスのツール、サービスをワークフローまたはパイプラインに組み込んでインテリジェント アプリケーションを作成する方法を説明しています。 

Azure Machine Learning Studio を使用する Team Data Science Process のステップを実行する例については、[Azure ML の使用](http://aka.ms/datascienceprocess)ラーニング パスをご覧ください。