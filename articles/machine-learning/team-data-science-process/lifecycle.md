---
title: "Team Data Science Process ライフサイクル - Azure | Microsoft Docs"
description: "データ サイエンス プロジェクトを実行するために必要な手順について説明します。"
services: machine-learning
documentationcenter: 
author: bradsev
manager: cgronlun
editor: cgronlun
ms.assetid: b1f677bb-eef5-4acb-9b3b-8a5819fb0e78
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/02/2017
ms.author: bradsev;
ms.openlocfilehash: 264386b527626f3241966bfdb2fb061781121be1
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="team-data-science-process-lifecycle"></a>Team Data Science Process ライフサイクル

Team Data Science Process (TDSP) には、データ サイエンス プロジェクトを体系化するために使用できる推奨ライフサイクルが用意されています。 このライフサイクルは、プロジェクトを実行する際に、その開始から終了までにわたって進められる通常のステップを大まかにまとめたものです。 [CRISP-DM](https://wikipedia.org/wiki/Cross_Industry_Standard_Process_for_Data_Mining)、[KDD](https://wikipedia.org/wiki/Data_mining#Process)、所属する組織独自のカスタム プロセスなど、別のデータ サイエンス ライフサイクルを利用している場合は、それらの開発ライフサイクルにタスクベースの TDSP を組み込んで使用することもできます。 

このライフサイクルは、インテリジェント アプリケーションに付属することを意図したデータ サイエンス プロジェクト向けに設計されています。 このようなアプリケーションでは、機械学習モデルや人工知能モデルをデプロイして予測分析に使用します。 このプロセスは、探索的データ サイエンス プロジェクトや、アドホック分析プロジェクトに利用しても効果的ですが、 そのような場合、ここで説明する一部のステップは不要になります。    

TDSP ライフサイクルは、繰り返し実行される 5 つの主なステージで構成されています。 チェックの内容は次のとおりです

* [1. ビジネスの把握](lifecycle-business-understanding.md)
* [2. データの取得と理解](lifecycle-data.md)
* [3. モデリング](lifecycle-modeling.md)
* [4. デプロイ](lifecycle-deployment.md)
* [5. 顧客による受け入れ](lifecycle-acceptance.md)

次の図は、**Team Data Science Process ライフサイクル**を視覚的に表したものです。 

![TDSP ライフサイクル](./media/lifecycle/tdsp-lifecycle2.png) 


Team Data Science Process ライフサイクルは、繰り返し行う一連のステップとしてモデル化されており、予測モデルを使用するために必要なタスクのガイダンスとして役立ちます。 これらのモデルは運用環境にデプロイして、インテリジェントなアプリケーションの構築に活用できます。 このプロセス ライフサイクルの目的は、データ サイエンス プロジェクトを契約上の明確な終着点へと継続的に導くことです。 データ サイエンスとは調査と探索の作業ですが、標準化されたテンプレートで明確に定義した一連のアーティファクトを利用し、そのタスクをチームと顧客に対してはっきり伝えることができれば、誤解が生じる危険を回避し、複雑なデータ サイエンス プロジェクトをより高い確率で成功に導くことができます。

各ステージで、以下のような情報を提供します。

* **目標**: 具体的な目標。
* **方法**: 具体的な課題についての概説と、その達成方法に関するガイダンス。
* **アーティファクト**: 成果物と、その生成方法に関するサポート。

## <a name="next-steps"></a>次のステップ
また、 **特定のシナリオ** のプロセスに伴うすべての段階をリハーサル的に最初から最後まで実証することも可能です。 これらは、[サンプル チュートリアル](walkthroughs.md)のトピックで簡単な説明と共にリンク付きで紹介されています。 チュートリアルでは、クラウド、オンプレミスのツール、サービスをワークフローまたはパイプラインに組み込んでインテリジェント アプリケーションを作成する方法を説明しています。 

Azure Machine Learning Studio を使用する Team Data Science Process のステップを実行する例については、[Azure ML の使用](http://aka.ms/datascienceprocess)ラーニング パスをご覧ください。

