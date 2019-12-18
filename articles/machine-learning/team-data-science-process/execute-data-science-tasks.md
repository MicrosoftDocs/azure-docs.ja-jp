---
title: データ サイエンス タスクを実行する - Team Data Science Process
description: データ サイエンティストがバージョン コントロールされた追跡可能で協調的な方法でデータ サイエンス プロジェクトを実行する方法。
author: marktab
manager: cgronlun
editor: cgronlun
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 11/28/2017
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: a6b463c4a1d0e640cc6c2a380ee978fcafa2877e
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/06/2019
ms.locfileid: "73670340"
---
# <a name="execute-data-science-tasks-exploration-modeling-and-deployment"></a>データ サイエンス タスクの実行: 探索、モデリング、およびデプロイ

通常のデータ サイエンス タスクには、データの探索、モデリング、およびデプロイが含まれます。 この記事では、**インタラクティブなデータ探索、分析、レポート作成 (IDEAR)** と**自動モデリングおよびレポート作成 (AMAR)** のユーティリティを使用して、インタラクティブなデータ探索、データ分析、レポート作成、およびモデル作成などの複数のデータ サイエンス タスクを完了する方法を示します。 また、次のようなさまざまなツールキットやデータ プラットフォームを使用して運用環境にモデルをデプロイするための選択肢を示します。

- [Azure Machine Learning](../index.yml)
- [ML サービスを使用した SQL Server](https://docs.microsoft.com/sql/advanced-analytics/r/r-services)
- [Microsoft Machine Learning Server](https://docs.microsoft.com/machine-learning-server/what-is-machine-learning-server)


## 1.<a name='DataQualityReportUtility-1'></a> 探索 

データ サイエンティストは、Python で利用可能なライブラリとパッケージ (matplotlib など) の使用や、R (ggplot や lattice など) の使用など、さまざまな方法で探索およびレポート作成を実行できます。 データ サイエンティストは、特定のシナリオでのデータ探索のニーズに合うように、これらのコードをカスタマイズできます。 構造化データを処理する場合のニーズは、テキストやイメージなどの非構造化データの場合とは異なります。 

Azure Machine Learning などの製品では、機能作成を含め、データ論争や探索のための[高度なデータ準備](../service/how-to-transform-data.md)も提供しています。 ユーザーは、最もニーズに合うツール、ライブラリ、およびパッケージを決定する必要があります。 

このフェーズの最後には、データ探索レポートが完成します。 レポートでは、モデリングに使用されるデータを総括したビューと、モデリングの手順を進めるうえでそのデータが適しているかどうかの査定を提供します。 以降のセクションで説明する半自動探索、モデリング、レポート作成のための Team Data Science Process (TDSP) ユーティリティでは、標準的なデータ探索やモデリング レポートも提供します。 

### <a name="interactive-data-exploration-analysis-and-reporting-using-the-idear-utility"></a>IDEAR ユーティリティを使用した、インタラクティブなデータ探索、分析、レポート作成

この R Markdown ベースまたは Python ノートブック ベースのユーティリティは、データ セットを評価および調査するための柔軟なインタラクティブ ツールを提供します。 ユーザーは、最小限のコーディングでデータ セットからレポートをすばやく生成できます。 ユーザーは、ボタンをクリックして、インタラクティブ ツールの調査結果を最終レポートにエクスポートできます。このレポートをクライアントに配信したり、それ以降のモデリング手順に含める変数の決定に使用したりできます。

現時点では、ツールはメモリ内のデータ フレームでのみ機能します。 調査するデータ セットのパラメーターを指定するには YAML ファイルが必要です。 詳細については、[TDSP データ サイエンス ユーティリティの IDEAR](https://github.com/Azure/Azure-TDSP-Utilities/tree/master/DataScienceUtilities/DataReport-Utils) に関するページをご覧ください。


## 2.<a name='ModelingUtility-2'></a> モデリング

さまざまな言語でモデルをトレーニングするための、多数のツールキットとパッケージがあります。 精度と待機時間に関するパフォーマンスの考慮事項が、対応するビジネスのユース ケースや生産シナリオを満たしていれば、データ サイエンティストがそれらのうち自分に適したものを自由に使用できる必要があります。

次のセクションでは、半自動化モデリングに R ベースの TDSP ユーティリティを使用する方法を示します。 この AMAR ユーティリティは、より優れたパフォーマンスのモデルを提供するために調整が必要なパラメーターや、ベース ライン モデルを迅速に生成するために使用できます。
次のモデル管理のセクションでは、複数のモデルを登録して管理するためのシステムを保持する方法について説明します。


### <a name="model-training-modeling-and-reporting-using-the-amar-utility"></a>モデル トレーニング: AMAR ユーティリティを使用したモデリングとレポート作成

[Automated Modeling and Reporting (AMAR) ユーティリティ](https://github.com/Azure/Azure-TDSP-Utilities/tree/master/DataScienceUtilities/Modeling)は、ハイパー パラメーター スイープでモデルの作成を実行し、それらのモデルの精度を比較するためのカスタマイズ可能な半自動ツールを提供します。 

モデル作成ユーティリティは、さまざまなセクションを簡単にナビゲートするためのコンテンツのテーブルを含む自己包含 HTML 出力を生成するために実行できる R Markdown ファイルです。 マークダウン ファイルが実行 (Knit) されると、glmnet パッケージを使用した正規化回帰、randomForest パッケージを使用したランダム フォレスト、xgboost パッケージを使用したブースティング ツリーの 3 つのアルゴリズムが実行されます。 これらの各アルゴリズムがトレーニング済みのモデルを生成します。 これらのモデルの精度が比較され、機能の相対的な重要度のプロットがレポートされます。 現時点では、2 つのユーティリティがあります。1 つは二項分類タスク用で、もう 1 つは回帰タスク用です。 これらの主な違いはパラメーターの制御方法で、精度のメトリックがこれらの学習タスクに対して指定されます。 

YAML ファイルを使用して以下を指定します。

- データ入力 (SQL ソースまたは R データ ファイル) 
- トレーニングに使用されるデータの部分とテストに使用される部分
- 実行するアルゴリズム 
- モデルの最適化のための制御パラメーターの選択:
    - クロス検証 
    - ブートストラップ
    - クロス検証のフォールド
- 各アルゴリズムのハイパー パラメーター セット。 

モデルをすばやく実行するために、アルゴリズムの数、最適化のフォールドの数、ハイパー パラメーター、スイープするハイパー パラメーター セットの数も Yaml ファイルで変更できます。 たとえば、より少ない CV フォールド数で実行して、パラメーター セットの数を減らすことができます。 保証されている場合は、より多くの CV フォールド、またはより多くのパラメーター セットでより包括的に実行することもできます。

詳細については、[TDSP データ サイエンス ユーティリティでの自動化されたモデリングおよびユーティリティ](https://github.com/Azure/Azure-TDSP-Utilities/tree/master/DataScienceUtilities/Modeling)に関するページをご覧ください。

### <a name="model-management"></a>モデル管理
複数のモデルが構築されたら、一般的には、モデルを登録して管理するためのシステムが必要になります。 通常は、スクリプトまたは API とバックエンド データベースまたはバージョン管理システムを組み合わせる必要があります。 これらの管理タスクで検討できるいくつかの選択肢は、次のとおりです。

1. [Azure Machine Learning - モデル管理サービス](../index.yml)
2. [MIT からの ModelDB](https://mitdbg.github.io/modeldb/) 
3. [モデル管理システムとしての SQL サーバー](https://blogs.technet.microsoft.com/dataplatforminsider/2016/10/17/sql-server-as-a-machine-learning-model-management-system/)
4. [Microsoft Machine Learning Server](https://docs.microsoft.com/sql/advanced-analytics/r/r-server-standalone)

## 3.<a name='Deployment-3'></a> デプロイ

運用環境へのデプロイによって、モデルがビジネスのアクティブな役割を担うことが可能になります。 デプロイされたモデルからの予測は、ビジネス上の決定に使用できます。

### <a name="production-platforms"></a>運用環境プラットフォーム
モデルを運用環境にデプロイするには、さまざまな方法とプラットフォームがあります。 いくつかの選択肢を次に示します。


- [Azure Machine Learning でのモデルのデプロイ](../service/how-to-deploy-and-where.md)
- [SQL server でのモデルのデプロイ](https://docs.microsoft.com/sql/advanced-analytics/tutorials/sqldev-py6-operationalize-the-model)
- [Microsoft Machine Learning Server](https://docs.microsoft.com/sql/advanced-analytics/r/r-server-standalone)

> [!NOTE]
> デプロイの前に、モデル スコアリングの待機時間が、運用環境で使用できるほど十分に低いことを保証する必要があります。
>
>

**特定のシナリオ**のプロセスの全手順を示したチュートリアルで、他の例も入手できます。 これらは、[サンプル チュートリアル](walkthroughs.md)の記事で簡単な説明と共にリンク付きで紹介されています。 チュートリアルでは、クラウド、オンプレミスのツール、サービスをワークフローまたはパイプラインに組み込んでインテリジェント アプリケーションを作成する方法を説明しています。

> [!NOTE]
> Azure Machine Learning Studio を使用したデプロイについては、「[Azure Machine Learning Web サービスをデプロイする](../studio/deploy-a-machine-learning-web-service.md)」をご覧ください。
>
>

### <a name="ab-testing"></a>A/B テスト
複数のモデルが運用環境にある場合、[A/B テスト](https://en.wikipedia.org/wiki/A/B_testing)を実行してモデルのパフォーマンスを比較することが役立つ可能性があります。 

 
## <a name="next-steps"></a>次の手順

「[Track progress of data science projects (データ サイエンス プロジェクトの進行状況の追跡)](track-progress.md)」に、データ サイエンティストがデータ サイエンス プロジェクトの進行状況を追跡できる方法が示されています。

[モデルの操作および CI/CD](ci-cd-flask.md) に関するページには、開発モデルでの CI/CD の実行方法が示されています。


