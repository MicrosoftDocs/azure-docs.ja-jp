---
title: 専用 SQL プール (旧称 SQL DW) アーキテクチャ
description: Azure Synapse Analytics の専用 SQL プール (旧称 SQL DW) で分散クエリ処理機能と Azure Storage を組み合わせて、ハイ パフォーマンスとスケーラビリティを実現する方法について説明します。
services: synapse-analytics
author: mlee3gsd
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 11/04/2019
ms.author: martinle
ms.reviewer: igorstan
ms.openlocfilehash: 0e87451531750e502f67dc30e6fbd26c8c944d22
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98678595"
---
# <a name="dedicated-sql-pool-formerly-sql-dw-architecture-in-azure-synapse-analytics"></a>Azure Synapse Analytics での専用 SQL プール (旧称 SQL DW) のアーキテクチャ

Azure Synapse Analytics は、エンタープライズ データ ウェアハウスとビッグ データ分析がまとめられた分析サービスです。 これにより、条件に基づいてデータのクエリを自由に行うことができます。

> [!NOTE]
>詳細については、[Azure Synapse Analytics のドキュメント](../overview-what-is.md)を参照してください。
>


> [!VIDEO https://www.youtube.com/embed/PlyQ8yOb8kc]

## <a name="synapse-sql-architecture-components"></a>Synapse SQL アーキテクチャのコンポーネント

[専用 SQL プール (旧称 SQL DW)](sql-data-warehouse-overview-what-is.md) では、スケールアウト アーキテクチャを活用して、複数のノードにデータの計算処理を分散します。 スケール単位は、[データ ウェアハウス ユニット](what-is-a-data-warehouse-unit-dwu-cdwu.md)と呼ばれるコンピューティング能力の抽象化です。 コンピューティングをストレージから切り離すことで、システム内のデータとは無関係に、コンピューティングをスケーリングできるようになります。

![専用 SQL プール (旧称 SQL DW) アーキテクチャ](./media/massively-parallel-processing-mpp-architecture/massively-parallel-processing-mpp-architecture.png)

専用 SQL プール (旧称 SQL DW) は、ノードに基づくアーキテクチャを使用します。 アプリケーションは、制御ノードに接続して、T-SQL コマンドを発行します。 制御ノードでは、分散クエリ エンジンがホストされ、それによって並列処理のためにクエリが最適化された後、作業を並行して行うために操作がコンピューティング ノードに渡されます。

コンピューティング ノードはすべてのユーザー データを Azure Storage に保存し、並行クエリを実行します。 データ移動サービス (DMS) はシステム レベルの内部サービスで、必要に応じて複数のノードにデータを移動し、クエリを並列に実行して、正確な結果を返します。

分離されたストレージとコンピューティングでは、専用 SQL プール (旧称 SQL DW) を使用する場合、次のことが可能です。

- 記憶域のニーズに関係なく、コンピューティング能力を計算する。
- データを移動することなく、専用 SQL プール (旧称 SQL DW) 内でコンピューティング能力を拡大または縮小する。
- データをそのままの状態で保持しながら、コンピューティング能力を一時停止するため、支払いがストレージの分だけで済む。
- 稼働時間中にコンピューティング能力を再開する。

### <a name="azure-storage"></a>Azure Storage

専用 SQL プール (旧称 SQL DW) は、Azure Storage を活用して、ユーザー データを安全に保ちます。  データは Azure Storage によって保存、管理されるため、ストレージの使用量が別途課金されます。 データは、システムのパフォーマンスの最適化のため、**ディストリビューション** にシャード化されます。 どのシャーディング パターンを使用して、テーブルを定義するときにデータを分散するかを選択できます。 次の 2 つのシャーディング パターンがサポートされています。

- ハッシュ インデックス
- ラウンド ロビン
- レプリケート

### <a name="control-node"></a>制御ノード

制御ノードは、アーキテクチャの脳です。 すべてのアプリケーションおよび接続と対話するフロントエンドです。 制御ノードで分散クエリ エンジンが実行され、並列クエリのための最適化と調整が行われます。 T-SQL クエリを送信すると、それが制御ノードによって、各ディストリビューションに対して並列で実行されるクエリに変換されます。

### <a name="compute-nodes"></a>コンピューティング ノード

コンピューティング ノードは計算能力を提供します。 ディストリビューションは、処理のためにコンピューティング ノードにマップされます。 追加のコンピューティング リソースの料金を支払うと、ディストリビューションが使用可能なコンピューティング ノードに再マップされます。 コンピューティング ノード数の範囲は 1 から 60 までで、Synapse SQL のサービス レベルによって決定されます。

各コンピューティング ノードにはノード ID があり、システム ビューで確認できます。 名前が sys.pdw_nodes で始まるシステム ビューで node_id 列を検索することにより、コンピューティング ノード ID を見ることができます。 これらのシステム ビューの一覧については、[Synapse SQL のシステム ビュー](/sql/relational-databases/system-catalog-views/sql-data-warehouse-and-parallel-data-warehouse-catalog-views?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)に関する記事をご覧ください。

### <a name="data-movement-service"></a>データ移動サービス

データ移動サービス (DMS) は、コンピューティング ノード間のデータ移動を調整するデータ転送テクノロジです。 一部のクエリでは、並列クエリで正確な結果が返されるためにデータ移動が必要です。 データ移動が必要な場合は、DMS により、適切なデータが適切な場所に移動する必要があります。

## <a name="distributions"></a>ディストリビューション

ディストリビューションは、分散データで実行される並列クエリの保存および処理の基本的な単位です。 Synapse SQL でクエリを実行する場合、作業は並列で実行される 60 の小さなクエリに分割されます。

60 の小さいクエリそれぞれは、いずれかのデータ ディストリビューションで実行されます。 各コンピューティング ノードでは、60 ディストリビューションの 1 つまたは複数が管理されます。 コンピューティング リソースの数が最大の専用 SQL プール (旧称 SQL DW) では、コンピューティング ノードごとに 1 つのディストリビューションがあります。 コンピューティング リソースの数が最小の専用 SQL プール (旧称 SQL DW) では、1 つのコンピューティング ノードにすべてのディストリビューションがあります。  

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

Azure Synapse の概要について学習したので、次は、すばやく[専用 SQL プール (旧称 SQL DW) を作成し](create-data-warehouse-portal.md)、[サンプル データを読み込む](./load-data-from-azure-blob-storage-using-copy.md)方法について学習します。 Azure に慣れていない場合に新しい用語を調べるには、 [Azure 用語集](../../azure-glossary-cloud-terminology.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) が役立ちます。 または、次の Azure Synapse リソースも確認できます。  

- [顧客の成功事例](https://azure.microsoft.com/case-studies/?service=sql-data-warehouse)
- [ブログ](https://azure.microsoft.com/blog/tag/azure-sql-data-warehouse/)
- [機能に関する要求](https://feedback.azure.com/forums/307516-sql-data-warehouse)
- [ビデオ](https://azure.microsoft.com/documentation/videos/index/?services=sql-data-warehouse)
- [サポート チケットを作成する](sql-data-warehouse-get-started-create-support-ticket.md)
- [Microsoft Q&A 質問ページ](/answers/topics/azure-synapse-analytics.html)
- [Stack Overflow フォーラム](https://stackoverflow.com/questions/tagged/azure-sqldw)
- [Twitter](https://twitter.com/hashtag/SQLDW)