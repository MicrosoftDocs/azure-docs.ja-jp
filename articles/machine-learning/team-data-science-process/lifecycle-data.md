---
title: "Team Data Science Process ライフサイクルのデータの取得と理解のステージ - Azure | Microsoft Docs"
description: "データ サイエンス プロジェクトのデータの取得と理解のステージの目標、タスク、成果物。"
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
ms.openlocfilehash: eea3c357ebf6ad920c0ddebdb979aa07aece4794
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="data-acquisition-and-understanding"></a>データの取得と理解

このトピックでは、Team Data Science Process の**データの取得と理解のステージ**に関連付けられている目標、タスク、成果物のアウトラインを示します。 このプロセスには、データ サイエンス プロジェクトを体系化するために使用できる推奨ライフサイクルが用意されています。 ライフサイクルは、プロジェクトで通常 (多くの場合に繰り返し) 実行される主要なステージのアウトラインを示します。

* **ビジネスの把握**
* **データの取得と理解**
* **モデリング**
* **デプロイ**
* **顧客による受け入れ**

次の図は、**Team Data Science Process ライフサイクル**を視覚的に表したものです。 

![TDSP-Lifecycle2](./media/lifecycle/tdsp-lifecycle2.png) 


## <a name="goals"></a>目標
* ターゲット変数との関係が把握されているクリーンな高品質データセットを、すぐにモデル化できる状態で適切な分析環境に配置する。
* データを定期的に更新およびスコア付けするデータ パイプラインのソリューション アーキテクチャを開発する。

## <a name="how-to-do-it"></a>方法
このステージでは、以下に示す 3 つの主な課題に取り組みます。

* ターゲットの分析環境に**データを取り込む**。
* データの品質が質問に回答するのに十分かどうかを判断するために、**データを調べる**。 
* 新しいデータや定期的に更新されるデータをスコア付けするための**データ パイプラインを設定する**。

### <a name="21-ingest-the-data"></a>2.1 データを取り込む
トレーニングや予測などの分析操作が実行されるターゲットの場所に、ソースの場所からデータを移動するプロセスを設定します。 この設定を各種 Azure データ サービスで行う方法についての技術的な詳細とオプションは、「[分析用のストレージ環境にデータを読み込む](ingest-data.md)」を参照してください。 

### <a name="22-explore-the-data"></a>2.2 データを調べる
モデルのトレーニングを行う前に、データを正しく理解する必要があります。 実際のデータセットは、ノイズが多かったり、値が欠落していたり、その他の不一致が数多く見つかったりすることがよくあります。 データの要約と視覚化を行うことで、データをモデリングに使用する前に、そのデータの品質を監査し、処理に必要な情報を得ることができます。 このプロセスは、通常は繰り返し実行されます。

TDSP には、データの視覚化と概要レポートの準備に役立つ、[IDEAR](https://github.com/Azure/Azure-TDSP-Utilities/blob/master/DataScienceUtilities/DataReport-Utils) と呼ばれる自動化されたユーティリティが用意されています。 最初は IDEAR を使ってデータを調べることで、コーディングを行わずに対話形式でデータへの理解を深めてから、データの調査および視覚化用のカスタム コードの作成に進むことをお勧めします。 データのクリーニングに関するガイダンスについては、「[機械学習を強化するためのデータを準備するタスク](prepare-data.md)」を参照してください。  

クリーニング済みデータの品質が十分であると確認できたら、次のステップは、ターゲットに適した予測モデルを選択して開発できるように、データに固有のパターンをよく理解することです。 データとターゲットのつながりの程度や、次のモデリング ステップに進むうえで十分なデータがあるかどうかを示す証拠を探します。 このプロセスも、通常は繰り返し実行されます。 場合によっては、前のステージで最初に特定したデータセットを強化するために、より正確なデータや関連性の高いデータを持つ新しいデータ ソースを探すことが必要になります。  

### <a name="23-set-up-a-data-pipeline"></a>2.3 データ パイプラインを設定する
最初に行うデータの取り込みとクリーニングに加え、通常は、新しいデータをスコア付けするプロセスやデータを定期的に更新するプロセスを、継続的な学習プロセスの一部として設定する必要があります。 この設定は、データ パイプラインまたはワークフローの設定によって行うことができます。 [こちらの例](move-sql-azure-adf.md)では、[Azure Data Factory](https://azure.microsoft.com/services/data-factory/) でパイプラインを設定する方法について説明しています。 

データ パイプラインのソリューション アーキテクチャの開発は、このステージで行います。 また、以降のデータ サイエンス プロジェクトのステージでも、パイプラインの開発は並行して行います。 パイプラインは、ビジネス ニーズと、このソリューションの統合先とする既存システムの制約に応じて、バッチベース、ストリーミング/リアルタイム、ハイブリッドのいずれかに決まります。 

## <a name="artifacts"></a>アーティファクト
このステージでの成果物を以下に示します。

* [**データ品質レポート**](https://github.com/Azure/Azure-TDSP-ProjectTemplate/blob/master/Docs/DataReport/DataSummaryReport.md): このレポートには、データの概要、各属性とターゲットの関係、変数のランクなどが含まれます。TDSP の一部として提供される [IDEAR](https://github.com/Azure/Azure-TDSP-Utilities/blob/master/DataScienceUtilities/DataReport-Utils) ツールを使用すると、CSV ファイルやリレーショナル テーブルなどの任意の表形式データセットで、このレポートをすばやく生成できます。 
* **ソリューション アーキテクチャ**: これは、モデルの構築後に、新しいデータでのスコア付けや予測の実行に使用されるデータ パイプラインのダイアグラムまたは説明です。 また、新しいデータに基づきモデルを再トレーニングするためのパイプラインも、これに含まれます。 TDSP ディレクトリ構造テンプレートを使用する場合、ドキュメントは[プロジェクト](https://github.com/Azure/Azure-TDSP-ProjectTemplate/tree/master/Docs/Project) ディレクトリに格納されています。
* **チェックポイント判定**: 特徴エンジニアリングとモデルの構築を本格的に開始する前に、プロジェクトを再評価し、期待される値がそのプロジェクトを続行するために十分であるかどうかを判断できます。 たとえば、先に進む準備ができている、さらに多くのデータを収集する必要がある、質問に回答するためのデータが存在しないためプロジェクトを破棄するといった判断ができます。

## <a name="next-steps"></a>次のステップ

Team Data Science Process のライフサイクルの各ステップへのリンクを次に示します。

* [1.ビジネスの把握](lifecycle-business-understanding.md)
* [2.データの取得と理解](lifecycle-data.md)
* [3.モデリング](lifecycle-modeling.md)
* [4.デプロイ](lifecycle-deployment.md)
* [5.顧客による受け入れ](lifecycle-acceptance.md)

また、 **特定のシナリオ** のプロセスに伴うすべての段階をリハーサル的に最初から最後まで実証することも可能です。 これらは、[サンプル チュートリアル](walkthroughs.md)のトピックで簡単な説明と共にリンク付きで紹介されています。 チュートリアルでは、クラウド、オンプレミスのツール、サービスをワークフローまたはパイプラインに組み込んでインテリジェント アプリケーションを作成する方法を説明しています。  

Azure Machine Learning Studio を使用する Team Data Science Process のステップを実行する例については、[Azure ML の使用](http://aka.ms/datascienceprocess)ラーニング パスをご覧ください。