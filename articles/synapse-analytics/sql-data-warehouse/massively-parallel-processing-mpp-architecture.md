---
title: Azure Synapse Analytics (旧称 SQL DW) アーキテクチャ
description: Azure Synapse Analytics (旧称 SQL DW) が並列処理 (MPP) と Azure Storage を結合して、ハイ パフォーマンスとスケーラビリティを実現する方法を説明します。
services: synapse-analytics
author: mlee3gsd
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 11/04/2019
ms.author: martinle
ms.reviewer: igorstan
ms.openlocfilehash: cde6cb514b6f87315400b3c40d8b86bcb7ff0adb
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/02/2020
ms.locfileid: "85210968"
---
# <a name="azure-synapse-analytics-formerly-sql-dw-architecture"></a>Azure Synapse Analytics (旧称 SQL DW) アーキテクチャ

Azure Synapse は、エンタープライズ データ ウェアハウスとビッグ データ分析がまとめられた無制限の分析サービスです。 サーバーレスのオンデマンド リソースまたはプロビジョニング済みのリソースを使用しながら大規模に、各自の条件で自由にデータを照会することができます。 Azure Synapse では、これら 2 つの環境を 1 つにした統合エクスペリエンスを使用して、データの取り込み、準備、管理、提供を行い、BI と機械学習の差し迫ったニーズに対応できます。

 Azure Synapse には、次の 4 つのコンポーネントがあります。

- Synapse SQL:完全な T-SQL ベースの分析

  - SQL プール (プロビジョニング済み DWU での従量課金) – 一般公開
  - SQL オンデマンド (処理された TB 単位の課金) – (プレビュー)
- Spark:緊密に統合された Apache Spark (プレビュー)
- データ統合:ハイブリッド データ統合 (プレビュー)
- Studio: 統一ユーザー エクスペリエンス。  (プレビュー)

> [!VIDEO https://www.youtube.com/embed/PlyQ8yOb8kc]

## <a name="synapse-sql-mpp-architecture-components"></a>Synapse SQL MPP アーキテクチャ コンポーネント

[Synapse SQL](sql-data-warehouse-overview-what-is.md#synapse-sql-pool-in-azure-synapse) では、スケールアウト アーキテクチャを活用して、複数のノードにデータの演算処理を分散します。 スケール単位は、[データ ウェアハウス ユニット](what-is-a-data-warehouse-unit-dwu-cdwu.md)と呼ばれるコンピューティング能力の抽象化です。 コンピューティングをストレージから切り離すことで、システム内のデータとは無関係に、コンピューティングをスケーリングできるようになります。

![Synapse SQL アーキテクチャ](./media/massively-parallel-processing-mpp-architecture/massively-parallel-processing-mpp-architecture.png)

Synapse SQL は、ノードベースのアーキテクチャを使用します。 アプリケーションでは T-SQL コマンドに接続し、これを Synapse SQL の単一のエントリ ポイントである制御ノードに発行します。 制御ノードは、並列処理のためにクエリを最適化する MPP エンジンを実行し、操作をコンピューティング ノードに渡して作業を並行して行います。

コンピューティング ノードはすべてのユーザー データを Azure Storage に保存し、並行クエリを実行します。 Data Movement Service (DMS) はシステム レベルの内部サービスで、必要に応じて複数のノードにデータを移動し、クエリを並列に実行して、正確な結果を返します。

分離されたストレージとコンピューティングでは、Synapse SQL プールを使用する場合、次のことが可能です。

- 記憶域のニーズに関係なく、コンピューティング能力を計算する。
- データを移動せずに、SQL プール (データ ウェアハウス) 内のコンピューティング能力を拡大または縮小します。
- データをそのままの状態で保持しながら、コンピューティング能力を一時停止するため、支払いをストレージの分だけにする。
- 稼働時間中にコンピューティング能力を再開する。

### <a name="azure-storage"></a>Azure Storage

Synapse SQL では、ユーザー データを安全に保つために Azure Storage を使用します。  データは Azure Storage によって保存、管理されるため、ストレージの使用量が別途課金されます。 データは、システムのパフォーマンスの最適化のため、**ディストリビューション**にシャード化されます。 どのシャーディング パターンを使用して、テーブルを定義するときにデータを分散するかを選択できます。 次の 2 つのシャーディング パターンがサポートされています。

- ハッシュ インデックス
- ラウンド ロビン
- レプリケート

### <a name="control-node"></a>制御ノード

制御ノードは、アーキテクチャの脳です。 すべてのアプリケーションおよび接続と対話するフロントエンドです。 MPP エンジンは制御ノードで実行され、並列クエリを最適化および調整します。 T-SQL クエリを送信すると、それが制御ノードによって、各ディストリビューションに対して並列で実行されるクエリに変換されます。

### <a name="compute-nodes"></a>コンピューティング ノード

コンピューティング ノードは計算能力を提供します。 ディストリビューションは、処理のためにコンピューティング ノードにマップされます。 追加のコンピューティング リソースの料金を支払うと、ディストリビューションが使用可能なコンピューティング ノードに再マップされます。 コンピューティング ノード数の範囲は 1 から 60 までで、Synapse SQL のサービス レベルによって決定されます。

各コンピューティング ノードにはノード ID があり、システム ビューで確認できます。 名前が sys.pdw_nodes で始まるシステム ビューで node_id 列を検索することにより、コンピューティング ノード ID を見ることができます。 これらのシステム ビューの一覧については、[MPP のシステム ビューに関する記事](/sql/relational-databases/system-catalog-views/sql-data-warehouse-and-parallel-data-warehouse-catalog-views?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)をご覧ください。

### <a name="data-movement-service"></a>データ移動サービス

データ移動サービス (DMS) は、コンピューティング ノード間のデータ移動を調整するデータ転送テクノロジです。 一部のクエリでは、並列クエリで正確な結果が返されるためにデータ移動が必要です。 データ移動が必要な場合は、DMS により、適切なデータが適切な場所に移動する必要があります。

## <a name="distributions"></a>ディストリビューション

ディストリビューションは、分散データで実行される並列クエリの保存および処理の基本的な単位です。 Synapse SQL でクエリを実行する場合、作業は並列で実行される 60 の小さなクエリに分割されます。

60 の小さいクエリそれぞれは、いずれかのデータ ディストリビューションで実行されます。 各コンピューティング ノードでは、60 ディストリビューションの 1 つまたは複数が管理されます。 最大コンピューティング リソース数を持つ 1 つの SQL プールでは、1 コンピューティング ノードあたりのディストリビューション数は 1 です。 最小コンピューティング リソース数を持つ 1 つの SQL プールでは、1 つのコンピューティング ノードにすべてのディストリビューションがあります。  

## <a name="hash-distributed-tables"></a>ハッシュ分散テーブル

ハッシュ分散テーブルでは、大きなテーブルでの結合と集計用に最高のクエリ パフォーマンスを実現できます。

ハッシュ分散テーブルにデータをシャードするために、ハッシュ関数を使用して各行が 1 つのディストリビューションに確実に割り当てられます。 テーブルの定義では、1 つの列をディストリビューション列として指定します。 ハッシュ関数は、ディストリビューション列の値を使用してディストリビューションに各行を割り当てます。

次の図は、完全な (分散していない) テーブルがハッシュ分散テーブルとして保存されるしくみを示したものです。

![分散テーブル](./media/massively-parallel-processing-mpp-architecture/hash-distributed-table.png "分散テーブル")  

- 各行は、1 つのディストリビューションに属しています。  
- 確定ハッシュ アルゴリズムが、各行を 1 つのディストリビューションに割り当てます。  
- ディストリビューションごとのテーブル行の数は、異なるテーブル サイズによって示されるように異なります。

ディストリビューション列の選択については、差異性、データ スキュー、およびシステムで実行されるクエリの種類など、パフォーマンスに関する考慮事項があります。

## <a name="round-robin-distributed-tables"></a>ラウンドロビン分散テーブル

ラウンド ロビン テーブルは作成が最も簡単なテーブルで、読み込み用のステージング テーブルとして使用した場合、高速なパフォーマンスを実現します。

ラウンドロビン分散テーブルでは、テーブル間にデータが均等に配布されますが、最適化は行われません。 1 つのディストリビューションがまずランダムに選択されたら、行のバッファーが順次ディストリビューションに割り当てられます。 ラウンド ロビン テーブルではデータは素早く読み込まれますが、通常、ハッシュ分散テーブルの方が高いクエリ パフォーマンスが見込まれます。 ラウンド ロビン テーブルの結合では、データの再シャッフルが必要になり、追加の時間がかかります。

## <a name="replicated-tables"></a>レプリケート テーブル

レプリケート テーブルは、小さなテーブル用に最も高速なクエリ パフォーマンスを実現します。

レプリケートされたテーブルは、各コンピューティング ノードにテーブルの完全なコピーをキャッシュします。 その結果、テーブルをレプリケートすると、結合または集計の前に、コンピューティング ノード間にデータを転送する必要がなくなります。 レプリケート テーブルは小さいテーブルに最適です。 大きいテーブルの使用が非現実的なデータを書き込む際には、追加ストレージが必要になり、追加のオーバーヘッドが発生します。  

次の図は、各コンピューティング ノードの最初のディストリビューションにキャッシュされたレプリケート テーブルを示しています。  

![レプリケート テーブル](./media/massively-parallel-processing-mpp-architecture/replicated-table.png "レプリケート テーブル")

## <a name="next-steps"></a>次のステップ

Azure Synapse の概要について学習したので、次はすばやく [SQL プールを作成](create-data-warehouse-portal.md)し、[サンプル データを読み込む](load-data-from-azure-blob-storage-using-polybase.md)方法について学習してください。 Azure に慣れていない場合に新しい用語を調べるには、 [Azure 用語集](../../azure-glossary-cloud-terminology.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) が役立ちます。 または、次の Azure Synapse リソースも確認できます。  

- [顧客の成功事例](https://azure.microsoft.com/case-studies/?service=sql-data-warehouse)
- [ブログ](https://azure.microsoft.com/blog/tag/azure-sql-data-warehouse/)
- [機能に関する要求](https://feedback.azure.com/forums/307516-sql-data-warehouse)
- [ビデオ](https://azure.microsoft.com/documentation/videos/index/?services=sql-data-warehouse)
- [サポート チケットを作成する](sql-data-warehouse-get-started-create-support-ticket.md)
- [Microsoft Q&A 質問ページ](https://docs.microsoft.com/answers/topics/azure-synapse-analytics.html)
- [Stack Overflow フォーラム](https://stackoverflow.com/questions/tagged/azure-sqldw)
- [Twitter](https://twitter.com/hashtag/SQLDW)
