---
title: "Team Data Science Process ライフサイクルの顧客による受け入れステージ - Azure | Microsoft Docs"
description: "データ サイエンス プロジェクトの顧客による受け入れステージの目標、タスク、成果物。"
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
ms.openlocfilehash: 953f090f775da5e48b2f4ed36550a5624ae4596b
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="customer-acceptance"></a>顧客による受け入れ

このトピックでは、Team Data Science Process の**顧客による受け入れステージ**に関連付けられている目標、タスク、成果物のアウトラインを示します。 このプロセスには、データ サイエンス プロジェクトを体系化するために使用できる推奨ライフサイクルが用意されています。 ライフサイクルは、プロジェクトで通常 (多くの場合に繰り返し) 実行される主要なステージのアウトラインを示します。

* **ビジネスの把握**
* **データの取得と理解**
* **モデリング**
* **デプロイ**
* **顧客による受け入れ**

次の図は、**Team Data Science Process ライフサイクル**を視覚的に表したものです。 

![TDSP-Lifecycle2](./media/lifecycle/tdsp-lifecycle2.png) 


## <a name="goal"></a>目標
* **プロジェクトの成果物を完成させる**: パイプライン、モデル、およびそれらの運用環境でのデプロイが顧客の目標を満たしていることを確認します。

## <a name="how-to-do-it"></a>方法
このステージでは、以下に示す 2 つの主な課題に取り組みます。

* **システムの検証**: デプロイ済みのモデルとパイプラインが顧客のニーズを満たしていることを確認します。
* **プロジェクトのハンドオフ**: 運用環境でシステムを運用する組織にハンドオフします。

顧客は、システムを運用環境にデプロイして独自のクライアント アプリケーションで利用できるようにするため、そのシステムがビジネス ニーズを満たし、許容可能な精度で質問に回答できるかどうかを検証します。 すべてのドキュメントに対して最終処理とレビューが行われます。 運用を担う組織へのプロジェクトのハンドオフが、これで完了します。 この場合の組織とは、たとえば運用環境でのシステム運用を担当する IT 部門、顧客のデータ サイエンス チーム、顧客の代理業者などです。 

## <a name="artifacts"></a>アーティファクト
この最終ステージで生成される主なアーティファクトは、**顧客向けプロジェクトの終了レポート**です。 これは技術レポートであり、システムの学習と運用に役立つプロジェクトのすべての詳細が含まれます。 TDSP によって提供されている[終了レポート](https://github.com/Azure/Azure-TDSP-ProjectTemplate/blob/master/Docs/Project/Exit%20Report.md) テンプレートは、そのまま使用することも、クライアント固有のニーズに合わせてカスタマイズすることもできます。 


## <a name="next-steps"></a>次のステップ

Team Data Science Process のライフサイクルの各ステップへのリンクを次に示します。

* [1.ビジネスの把握](lifecycle-business-understanding.md)
* [2.データの取得と理解](lifecycle-data.md)
* [3.モデリング](lifecycle-modeling.md)
* [4.デプロイ](lifecycle-deployment.md)
* [5.顧客による受け入れ](lifecycle-acceptance.md)

また、 **特定のシナリオ** のプロセスに伴うすべての段階をリハーサル的に最初から最後まで実証することも可能です。 これらは、[サンプル チュートリアル](walkthroughs.md)のトピックで簡単な説明と共にリンク付きで紹介されています。 チュートリアルでは、クラウド、オンプレミスのツール、サービスをワークフローまたはパイプラインに組み込んでインテリジェント アプリケーションを作成する方法を説明しています。 

Azure Machine Learning Studio を使用する Team Data Science Process のステップを実行する例については、[Azure ML の使用](http://aka.ms/datascienceprocess)ラーニング パスをご覧ください。