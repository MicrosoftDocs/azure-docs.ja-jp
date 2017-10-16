---
title: "Team Data Science Process ライフサイクルのビジネスの把握ステージ - Azure | Microsoft Docs"
description: "データ サイエンス プロジェクトのビジネスの把握ステージの目標、タスク、成果物。"
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
ms.openlocfilehash: 2adce61f8185bd86a6a870bb5752fe936701b0af
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="business-understanding"></a>ビジネスの把握

このトピックでは、Team Data Science Process の**ビジネスの把握ステージ**に関連付けられている目標、タスク、成果物のアウトラインを示します。 このプロセスには、データ サイエンス プロジェクトを体系化するために使用できる推奨ライフサイクルが用意されています。 ライフサイクルは、プロジェクトで通常 (多くの場合に繰り返し) 実行される主要なステージのアウトラインを示します。

* **ビジネスの把握**
* **データの取得と理解**
* **モデリング**
* **デプロイ**
* **顧客による受け入れ**

次の図は、**Team Data Science Process ライフサイクル**を視覚的に表したものです。 

![TDSP-Lifecycle2](./media/lifecycle/tdsp-lifecycle2.png) 


## <a name="goals"></a>目標
* **モデル ターゲット**としての機能を果たし、その関連メトリックがプロジェクトの成功を評価するために使用される変数を、**主要な変数**に指定する。
* ビジネスがアクセスできるまたは取得する必要がある、関連性のある**データ ソース**が特定される。

## <a name="how-to-do-it"></a>方法
このステージでは、以下に示す 2 つの主な課題に取り組みます。 

* **目標を定義する**: 顧客およびその他の利害関係者と連携し、ビジネス上の問題を把握し、特定します。 データ サイエンス手法のターゲットにできるような、ビジネスの目標を定義付ける質問を考案します。
* **データ ソースを特定する**: プロジェクトの目標を定義付ける質問に対して答えを出すために利用できる関連データを見つけます。

### <a name="11-define-objectives"></a>1.1 目標を定義する

1. このステップの中心となる目標は、分析によって予測する必要がある、主要な**ビジネス変数**を特定することです。 この変数は**モデル ターゲット**と呼ばれ、これに関連付けられているメトリックはプロジェクトの成功を評価するために使用されます。 このようなターゲットの 2 つの例は、売上予測とある注文が不正な注文である確率です。

2. 関連性があり、具体的かつ明確な "鋭い" 質問の問いかけと改善を行うことで、**プロジェクトの目標**を定義します。 データ サイエンスは、名前と数字を使ってこのような質問に回答するプロセスです。 鋭い質問の問いかけについて詳しくは、ブログ「[How to do Data Science (データ サイエンスの実施方法)](https://blogs.technet.microsoft.com/machinelearning/2016/03/28/how-to-do-data-science/)」をご覧ください。 データ サイエンスや機械学習は、一般的には以下の 5 種類の質問に回答するために使用されます。
 
   * どのくらいの量または数か  (回帰)
   * どのカテゴリか  (分類)
   * どのグループか  (クラスタリング)
   * 異常か (異常の検出)
   * どの選択肢を選ぶべきか  (推奨)

    これらの質問のうちのどれについて質問し、どのような回答がビジネス上の目標の達成に役立つかを判断します。

3. メンバーの役割と責任を定めることにより、**プロジェクト チーム**を定義します。 見つかる情報の増加に伴い繰り返し実行する、大まかなマイルストーン プランを作成します。  

4. **成功のメトリックを定義します**。 例: 顧客離れを低減するためのプロモーションを提供できるように、3 か月間のプロジェクトが終了するまでに顧客離れの予測精度 X% を達成する。 **SMART (スマート)** なメトリックを定義する必要があります。 
   * **S**pecific (具体的) 
   * **M**easurable (測定可能)
   * **A**chievable (達成可能) 
   * **R**elevant (関連性がある) 
   * **T**ime-bound (期限付き) 

### <a name="12-identify-data-sources"></a>1.2 データ ソースを特定する
鋭い質問への既知の回答例を含むデータ ソースを特定します。 以下のようなデータを探します。

* 質問に対して**関連性のある**データ。 ターゲットの測定値と、ターゲットに関連する特徴があるか。
* モデル ターゲットの**正確な測定値**であるデータと、対象とする特徴。

たとえば、既存のシステムで問題を解決し、プロジェクトの目標を達成するために、従来とは異なる種類のデータを収集、記録しなければならないことはよくあります。 このような場合は、外部のデータ ソースを探すか、新しいデータを収集するようにシステムを更新することをお勧めします。

## <a name="artifacts"></a>アーティファクト
このステージでの成果物を以下に示します。

* [**憲章ドキュメント**](https://github.com/Azure/Azure-TDSP-ProjectTemplate/blob/master/Docs/Project/Charter.md): TDSP プロジェクト構造定義で標準テンプレートが提供されています。 これは、新しいデータの検出やビジネス要件の変更に応じてプロジェクト全体で更新される、常に最新のドキュメントです。 検出プロセスの進行に合わせてこのドキュメントに詳細を追加し、更新を繰り返すことが重要です。 変更を加える際は、顧客およびその他の利害関係者が常に関与するようにし、変更の理由を明確に伝えます。  
* [**データ ソース**](https://github.com/Azure/Azure-TDSP-ProjectTemplate/blob/master/Docs/DataReport/Data%20Defintion.md#raw-data-sources): これは、TDSP プロジェクトの**データ レポート** フォルダーにある**データ定義**レポートの「**Raw Data Sources**」(生データ ソース) セクションです。 生データの元の場所と移動先の場所を指定します。 後のステージで、分析環境にデータを移動するスクリプトなどの詳細を追加します。  
* [**データ辞書**](https://github.com/Azure/Azure-TDSP-ProjectTemplate/tree/master/Docs/DataDictionaries): このドキュメントはクライアントによって提供されているデータの説明を提供します。 これらの説明にはスキーマ (データ型、検証ルールに関する情報 (ある場合)) とエンティティ関連の図 (ある場合) に関する情報が含まれています。

## <a name="next-steps"></a>次のステップ

Team Data Science Process のライフサイクルの各ステップへのリンクを次に示します。

* [1.ビジネスの把握](lifecycle-business-understanding.md)
* [2.データの取得と理解](lifecycle-data.md)
* [3.モデリング](lifecycle-modeling.md)
* [4.デプロイ](lifecycle-deployment.md)
* [5.顧客による受け入れ](lifecycle-acceptance.md)

また、 **特定のシナリオ** のプロセスに伴うすべての段階をリハーサル的に最初から最後まで実証することも可能です。 これらは、[サンプル チュートリアル](walkthroughs.md)のトピックで簡単な説明と共にリンク付きで紹介されています。 チュートリアルでは、クラウド、オンプレミスのツール、サービスをワークフローまたはパイプラインに組み込んでインテリジェント アプリケーションを作成する方法を説明しています。 

Azure Machine Learning Studio を使用する Team Data Science Process のステップを実行する例については、[Azure ML の使用](http://aka.ms/datascienceprocess)ラーニング パスをご覧ください。