---
title: データ サイエンス タスクを実行する - Team Data Science Process
description: データ サイエンティストがバージョン コントロールされた追跡可能で協調的な方法でデータ サイエンス プロジェクトを実行する方法。
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 11/17/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: e180ecbf5c68dbd9c179244083a641ac6ed42de0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "100371959"
---
# <a name="execute-data-science-tasks-exploration-modeling-and-deployment"></a>データ サイエンス タスクの実行: 探索、モデリング、およびデプロイ

通常のデータ サイエンス タスクには、データの探索、モデリング、およびデプロイが含まれます。 この記事では、対話型データ探索、データ分析、レポート作成、モデル作成などの複数の共通データ サイエンス タスクを完了するタスクについて説明します。 運用環境にモデルをデプロイするためのオプションには、次のものがあります。

- [Azure Machine Learning](../index.yml)
- [ML サービスを使用した SQL Server](/sql/advanced-analytics/r/r-services)
- [Microsoft Machine Learning Server](/machine-learning-server/what-is-machine-learning-server)


## <a name="1--exploration"></a>1.<a name='DataQualityReportUtility-1'></a> 探索 

データ サイエンティストは、Python で利用可能なライブラリとパッケージ (matplotlib など) の使用や、R (ggplot や lattice など) の使用など、さまざまな方法で探索およびレポート作成を実行できます。 データ サイエンティストは、特定のシナリオでのデータ探索のニーズに合うように、これらのコードをカスタマイズできます。 構造化データを処理する場合のニーズは、テキストやイメージなどの非構造化データの場合とは異なります。 

Azure Machine Learning などの製品では、機能作成を含め、データ論争や探索のための[高度なデータ準備](../how-to-create-register-datasets.md)も提供しています。 ユーザーは、最もニーズに合うツール、ライブラリ、およびパッケージを決定する必要があります。 

このフェーズの最後には、データ探索レポートが完成します。 レポートでは、モデリングに使用されるデータを総括したビューと、モデリングの手順を進めるうえでそのデータが適しているかどうかの査定を提供します。 

## <a name="2--modeling"></a>2.<a name='ModelingUtility-2'></a> モデリング

さまざまな言語でモデルをトレーニングするための、多数のツールキットとパッケージがあります。 精度と待機時間に関するパフォーマンスの考慮事項が、対応するビジネスのユース ケースや生産シナリオを満たしていれば、データ サイエンティストがそれらのうち自分に適したものを自由に使用できる必要があります。

### <a name="model-management"></a>モデル管理
複数のモデルが構築されたら、一般的には、モデルを登録して管理するためのシステムが必要になります。 通常は、スクリプトまたは API とバックエンド データベースまたはバージョン管理システムを組み合わせる必要があります。 これらの管理タスクで検討できるいくつかの選択肢は、次のとおりです。

1. [Azure Machine Learning - モデル管理サービス](../index.yml)
2. [MIT からの ModelDB](https://people.csail.mit.edu/mvartak/papers/modeldb-hilda.pdf) 
3. [モデル管理システムとしての SQL サーバー](https://blogs.technet.microsoft.com/dataplatforminsider/2016/10/17/sql-server-as-a-machine-learning-model-management-system/)
4. [Microsoft Machine Learning Server](/sql/advanced-analytics/r/r-server-standalone)

## <a name="3--deployment"></a>3.<a name='Deployment-3'></a> デプロイ

運用環境へのデプロイによって、モデルがビジネスのアクティブな役割を担うことが可能になります。 デプロイされたモデルからの予測は、ビジネス上の決定に使用できます。

### <a name="production-platforms"></a>運用環境プラットフォーム
モデルを運用環境にデプロイするには、さまざまな方法とプラットフォームがあります。 いくつかの選択肢を次に示します。


- [Azure Machine Learning でのモデルのデプロイ](../how-to-deploy-and-where.md)
- [SQL server でのモデルのデプロイ](/sql/advanced-analytics/tutorials/sqldev-py6-operationalize-the-model)
- [Microsoft Machine Learning Server](/sql/advanced-analytics/r/r-server-standalone)

> [!NOTE]
> デプロイの前に、モデル スコアリングの待機時間が、運用環境で使用できるほど十分に低いことを保証する必要があります。
>
>

**特定のシナリオ** のプロセスの全手順を示したチュートリアルで、他の例も入手できます。 これらは、[サンプル チュートリアル](walkthroughs.md)の記事で簡単な説明と共にリンク付きで紹介されています。 チュートリアルでは、クラウド、オンプレミスのツール、サービスをワークフローまたはパイプラインに組み込んでインテリジェント アプリケーションを作成する方法を説明しています。

> [!NOTE]
> Azure Machine Learning Studio を使用したデプロイについては、「[Azure Machine Learning Web サービスをデプロイする](../classic/deploy-a-machine-learning-web-service.md)」をご覧ください。
>
>

### <a name="ab-testing"></a>A/B テスト
複数のモデルが運用環境にある場合、[A/B テスト](https://en.wikipedia.org/wiki/A/B_testing)を実行してモデルのパフォーマンスを比較することが役立つ可能性があります。 

 
## <a name="next-steps"></a>次のステップ

「[Track progress of data science projects (データ サイエンス プロジェクトの進行状況の追跡)](track-progress.md)」に、データ サイエンティストがデータ サイエンス プロジェクトの進行状況を追跡できる方法が示されています。

[モデルの操作および CI/CD](ci-cd-flask.md) に関するページには、開発モデルでの CI/CD の実行方法が示されています。
