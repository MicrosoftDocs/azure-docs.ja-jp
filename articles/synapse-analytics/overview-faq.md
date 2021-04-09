---
title: FAQ - Azure Synapse Analytics
description: Azure Synapse Analytics に関する FAQ
services: synapse-analytics
author: saveenr
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: overview
ms.date: 10/25/2020
ms.author: saveenr
ms.reviewer: jrasnick
ms.openlocfilehash: 9a0fb8ed8ac54fa866b6db7d8f808c011c0c6758
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "101695823"
---
# <a name="azure-synapse-analytics-frequently-asked-questions"></a>Azure Synapse Analytics についてよく寄せられる質問

このガイドでは、Azure Synapse Analytics についてよく寄せられる質問を紹介します。

## <a name="general"></a>全般

### <a name="q-how-can-i-use-rbac-roles-to-secure-my-workspace"></a>Q:RBAC ロールを使用してワークスペースをセキュリティで保護するにはどうすればよいですか。

A:Azure Synapse では、複数のロールとスコープが導入されています。これらを割り当てることで、ワークスペースを容易にセキュリティで保護できます。

Synapse RBAC ロール:
* Synapse 管理者
* Synapse SQL 管理者
* Apache Spark 管理者
* Synapse 共同作成者 (プレビュー)
* Synapse 成果物発行元 (プレビュー)
* Synapse 成果物ユーザー (プレビュー)
* Synapse コンピューティング オペレーター (プレビュー)
* Synapse 資格情報ユーザー (プレビュー)

Synapse ワークスペースをセキュリティで保護するには、RBAC ロールを次の RBAC スコープに割り当てます。
* Workspaces
* Spark プール
* 統合ランタイム
* リンクされたサービス
* 資格情報

さらに、専用 SQL プールでは、使い慣れたすべてのセキュリティ機能を使用できます。

### <a name="q-how-do-i-control-dedicated-sql-pools-serverless-sql-pools-and-serverless-spark-pools"></a>Q:専用 SQL プール、サーバーレス SQL プール、サーバーレス Spark プールを管理するにはどうすればよいですか。

A:Azure Synapse では、出発点として、Azure サブスクリプション レベルで利用可能な組み込みのコスト分析とコスト アラートを使用します。

- 専用 SQL プール - お客様が専用 SQL プールを作成し、そのサイズを指定するので、コストを直接可視化して管理できます。 Azure RBAC ロールを使用することで、専用 SQL プールを作成またはスケーリングできるユーザーをさらに制御できます。

- サーバーレス SQL プール - 監視とコスト管理の制御により、毎日、毎週、および毎月のレベルで支出の上限を設定できます。 詳細については、[サーバーレス SQL プールのコスト管理](./sql/data-processed.md)に関するページを参照してください。 

- サーバーレス Spark プール - Synapse RBAC ロールを使用して Spark プールを作成できるユーザーを制限できます。  

### <a name="q-will-synapse-workspace-support-folder-organization-of-objects-and-granularity-at-ga"></a>Q:一般提供の Synapse ワークスペースでは、オブジェクトのフォルダー編成と粒度がサポートされていますか。

A:Synapse ワークスペースでは、ユーザー定義のフォルダーがサポートされています。

### <a name="q-can-i-link-more-than-one-power-bi-workspace-to-a-single-azure-synapse-workspace"></a>Q:複数の Power BI ワークスペースを 1 つの Azure Synapse ワークスペースにリンクすることはできますか。
    
A:現在、1 つの Azure Synapse ワークスペースにリンクできる Power BI ワークスペースは 1 つのみです。 

### <a name="q-is-synapse-link-to-cosmos-db-ga"></a>Q:Cosmos DB への Synapse Link は一般提供されていますか。

A:Apache Spark の Synapse Link が一般提供されています。 サーバーレス SQL プールの Synapse Link はパブリック プレビューの段階にあります。

### <a name="q-does-azure-synapse-workspace-support-cicd"></a>Q:Azure Synapse ワークスペースでは CI/CD がサポートされていますか。 

A:はい。 すべてのパイプライン成果物、ノートブック、SQL スクリプト、および Spark ジョブ定義が、Git に存在します。 プール定義はすべて、ARM テンプレートとして Git に格納されます。 専用 SQL プールのオブジェクト (スキーマ、テーブル、ビューなど) は、CI/CD がサポートされたデータベース プロジェクトで管理されます。

## <a name="pipelines"></a>パイプライン

### <a name="q-how-do-i-ensure-i-know-what-credential-is-being-used-to-run-a-pipeline"></a>Q:パイプラインの実行に使用されている資格情報を確認するにはどうすればよいですか。 

A:Synapse パイプラインでの各アクティビティは、リンクされたサービス内で指定された資格情報を使用して実行されます。

### <a name="q-are-ssis-irs-supported-in-synapse-integrate"></a>Q:SSIS IR は Synapse 統合でサポートされていますか。

A: 現時点ではありません。 

### <a name="q-how-do-i-migrate-existing-pipelines-from-azure-data-factory-to-an-azure-synapse-workspace"></a>Q:既存のパイプラインを Azure Data Factory から Azure Synapse ワークスペースに移行するにはどうすればよいですか。

A:現時点では、元のパイプラインから JSON をエクスポートし、それを Synapse ワークスペースにインポートして、Azure Data Factory のパイプラインおよび関連する成果物を手動で再作成する必要があります。

## <a name="apache-spark"></a>Apache Spark

### <a name="q-what-is-the-difference-between-apache-spark-for-synapse-and-apache-spark"></a>Q:Synapse 用 Apache Spark と Apache Spark の違いは何ですか。

A:Synapse 用 Apache Spark は、Apache Spark に、他のサービス (AAD、Azure ML など) との統合サポート、追加のライブラリ (mssparktuils、Hummingbird)、および事前調整されたパフォーマンス構成が追加されたものです。

現在 Apache Spark 上で実行されているワークロードはすべて、変更することなく Azure Synapse Apache Spark 上で実行できます。 

### <a name="q-what-versions-of-spark-are-available"></a>Q:Spark のどのバージョンを使用できますか。

A:Azure Synapse Apache Spark では、Spark 2.4 が完全にサポートされています。 コア コンポーネントと現在サポートされているバージョンの完全な一覧については、[Apache Spark でサポートされるバージョン](./spark/apache-spark-version-support.md)に関するページを参照してください。

### <a name="q-is-there-an-equivalent-of-dbutils-in-azure-synapse-spark"></a>Q:Azure Synapse Spark には、DButils に相当するものがありますか。

A:はい、Azure Synapse Apache Spark には **mssparkutils** ライブラリが用意されています。 このユーティリティに関する詳しいドキュメントについては、「[Microsoft Spark Utilities の概要](./spark/microsoft-spark-utilities.md)」を参照してください。

### <a name="q-how-do-i-set-session-parameters-in-apache-spark"></a>Q:Apache Spark でセッション パラメーターを設定するにはどうすればよいですか。

A:セッション パラメーターを設定するには、%%configure マジックを使用します。 パラメーターを有効にするには、セッションの再起動が必要です。 

### <a name="q-how-do-i-set-cluster-level-parameters-in-a-serverless-spark-pool"></a>Q:サーバーレス Spark プールでクラスター レベルのパラメーターを設定するにはどうすればよいですか。

A:クラスター レベルのパラメーターを設定するには、Spark プールに spark.conf ファイルを指定します。 これにより、そのプールは、構成ファイルに渡されたパラメーターに従います。 

### <a name="q-can-i-run-a-multi-user-spark-cluster-in-azure-synapse-analytics"></a>Q:Azure Synapse Analytics でマルチユーザー Spark クラスターを実行することは可能ですか。
 
A:Azure Synapse には、特定のユースケースに対応した専用のエンジンが用意されています。 Synapse 用 Apache Spark は、クラスター モデルではなく、ジョブ サービスとして設計されています。 ユーザーがマルチユーザー クラスター モデルに対して求めるシナリオは 2 つあります。

**シナリオ 1: 多くのユーザーが、BI のためのデータを提供する 1 つのクラスターにアクセスする。**

このタスクを実行する最も簡単な方法は、Spark を使用してデータを作成し、Synapse SQL のサーバー機能を利用してそれらのデータセットに Power BI を接続できるようにします。

**シナリオ 2:1 つのクラスターに複数の開発者を配置して、コストを節約する。**
 
このシナリオを満たすには、少数の Spark リソースを使用するように設定されたサーバーレス Spark プールを各開発者に提供する必要があります。 サーバーレス Spark プールはコストがかからないため、積極的に使用しない限り、複数の開発者がいる場合のコストを最小限に抑えることができます。 容易に相互連携できるように、プールではメタデータ (Spark テーブル) を共有します。

### <a name="q-how-do-i-include-manage-and-install-libraries"></a>Q:ライブラリを追加、管理、インストールする方法を教えてください。

A:外部パッケージは、Spark プールの作成時に requirements.txt ファイルを介してインストールできるほか、Synapse ワークスペースまたは Azure portal からインストールできます。 「[Azure Synapse Analytics で Apache Spark 用のライブラリを管理する](./spark/apache-spark-azure-portal-add-libraries.md)」を参照してください。

## <a name="dedicated-sql-pools"></a>専用 SQL プール

### <a name="q-what-are-the-functional-differences-between-dedicated-sql-pools-and-serverless-pools"></a>Q:専用 SQL プールとサーバーレス プールの機能にはどのような違いがありますか。

A:違いの完全な一覧については、[Synapse SQL での T-SQL 機能の違い](./sql/overview-features.md)に関するページをご確認ください。

### <a name="q-now-that-azure-synapse-is-ga-how-do-i-move-my-dedicated-sql-pools-that-were-previously-standalone-into-azure-synapse"></a>Q:Azure Synapse の一般提供が開始されましたが、これまでスタンドアロンであった専用 SQL プールを Azure Synapse に移動するにはどうすればよいですか。 

A:"移動" や "移行" は不要です。 既存のプールで新しいワークスペース機能を有効にすることができます。 そうした場合、破壊的変更は発生せず、Synapse Studio、Spark、サーバーレス SQL プールなどの新機能を使用できるようになります。

### <a name="q-what-is-the-default-deployment-of-dedicated-sql-pools-now"></a>Q:現在、専用 SQL プールの既定のデプロイはどのようになりますか。 

A:既定では、すべての新しい専用 SQL プールがワークスペースにデプロイされます。ただし、必要に応じて、スタンドアロンのフォーム ファクターで専用 SQL プール (以前の SQL DW) を作成することもできます。 

## <a name="next-steps"></a>次のステップ

* [Azure Synapse Analytics の使用を開始する](get-started.md)
* [ワークスペースを作成する](quickstart-create-workspace.md)
* [サーバーレス SQL プールを使用する](quickstart-sql-on-demand.md)
