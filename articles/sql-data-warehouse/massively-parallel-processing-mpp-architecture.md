---
title: Azure SQL Data Warehouse - MPP アーキテクチャ | Microsoft Docs
description: Azure SQL Data Warehouse が超並列処理 (MPP) と Azure ストレージを結合して、高いパフォーマンスとスケーラビリティを実現する方法を説明します。
services: sql-data-warehouse
author: ronortloff
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: design
ms.date: 04/17/2018
ms.author: rortloff
ms.reviewer: igorstan
ms.openlocfilehash: 0c2ad7e5a707c20db2773324e8047eedaad1a48b
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2019
ms.locfileid: "57835035"
---
# <a name="azure-sql-data-warehouse---massively-parallel-processing-mpp-architecture"></a>Azure SQL Data Warehouse - 超並列処理 (MPP) アーキテクチャ
Azure SQL Data Warehouse が超並列処理 (MPP) と Azure ストレージを結合して、高いパフォーマンスとスケーラビリティを実現する方法を説明します。 

> [!VIDEO https://www.youtube.com/embed/PlyQ8yOb8kc]

## <a name="mpp-architecture-components"></a>MPP アーキテクチャ コンポーネント
SQL Data Warehouse では、スケールアウト アーキテクチャを活用して、複数のノードにデータの演算処理を分散します。 スケール単位は、[データ ウェアハウス ユニット](what-is-a-data-warehouse-unit-dwu-cdwu.md)と呼ばれるコンピューティング能力の抽象化です。 SQL Data Warehouse がコンピューティングをストレージから切り離すことで、システム内のデータとは無関係に、コンピューティングをスケーリングできるようになります。

![SQL Data Warehouse のアーキテクチャ](media/massively-parallel-processing-mpp-architecture/massively-parallel-processing-mpp-architecture.png)

SQL Data Warehouse では、ノードベースのアーキテクチャを使用します。 アプリケーションは T-SQL コマンドに接続し、これをデータ ウェアハウスの単一のポイントである制御ノードに発行します。 制御ノードは、並列処理のためにクエリを最適化する MPP エンジンを実行し、操作をコンピューティング ノードに渡して作業を並行して行います。 コンピューティング ノードはすべてのユーザー データを Azure Storage に保存し、並行クエリを実行します。 Data Movement Service (DMS) はシステム レベルの内部サービスで、必要に応じて複数のノードにデータを移動し、クエリを並列に実行して、正確な結果を返します。 

ストレージとコンピューティングを分離することで、SQL Data Warehouse は次のことができます。

* 記憶域のニーズに関係なく、コンピューティング能力を計算する。
* データを移動せずに、コンピューティング能力を拡大または縮小する。
* データをそのままの状態で保持しながら、コンピューティング能力を一時停止するため、支払いをストレージの分だけにする。
* 稼働時間中にコンピューティング能力を再開する。

### <a name="azure-storage"></a>Azure Storage
SQL Data Warehouse では、ユーザー データを安全に保つために Azure ストレージを使用します。  データは Azure ストレージによって保存、管理されるため、SQL Data Warehouse によってストレージの使用量が別途課金されます。 データ自体は、システムのパフォーマンスの最適化のため、**ディストリビューション**にシャードされます。 どのシャーディング パターンを使用して、テーブルを定義するときにデータを分散するかを選択できます。 SQL Data Warehouse でサポートされているシャーディング パターンは次のとおりです。

* Hash
* ラウンド ロビン
* レプリケート

### <a name="control-node"></a>制御ノード

制御ノードは、データ ウェアハウスの脳です。 すべてのアプリケーションおよび接続と対話するフロントエンドです。 MPP エンジンは制御ノードで実行され、並列クエリを最適化および調整します。 SQL Data Warehouse に T-SQL クエリを送信すると、制御ノードはそれを、各ディストリビューションに対して並列で実行されるクエリに変換します。

### <a name="compute-nodes"></a>コンピューティング ノード

コンピューティング ノードは計算能力を提供します。 ディストリビューションは、処理のためにコンピューティング ノードにマップされます。 追加のコンピューティング リソースの料金を支払うと、SQL Data Warehouse はディストリビューションを使用可能なコンピューティング ノードに再マップします。 コンピューティング ノード数の範囲は 1 ～ 60 で、データ ウェアハウスのサービス レベルによって決定されます。

各コンピューティング ノードにはノード ID があり、システム ビューで確認できます。 名前が sys.pdw_nodes で始まるシステム ビューで node_id 列を検索することにより、コンピューティング ノード ID を見ることができます。 これらのシステム ビューの一覧については、[MPP のシステム ビューに関する記事](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sql-data-warehouse-and-parallel-data-warehouse-catalog-views?view=aps-pdw-2016-au7)をご覧ください。

### <a name="data-movement-service"></a>データ移動サービス
データ移動サービス (DMS) は、コンピューティング ノード間のデータ移動を調整するデータ転送テクノロジです。 一部のクエリでは、並列クエリで正確な結果が返されるためにデータ移動が必要です。 データ移動が必要な場合は、DMS により、適切なデータが適切な場所に移動する必要があります。 

## <a name="distributions"></a>ディストリビューション

ディストリビューションは、分散データで実行される並列クエリの保存および処理の基本的な単位です。 SQL Data Warehouse によるクエリの実行時に、作業は並列で実行される 60 の小さいクエリに分割されます。 60 の小さいクエリそれぞれは、いずれかのデータ ディストリビューションで実行されます。 各コンピューティング ノードでは、60 ディストリビューションの 1 つまたは複数が管理されます。 最大コンピューティング リソース数を持つ1 つのデータ ウェアハウスは、1 コンピューティング ノードあたり 1 ディストリビューションを持ちます。 最小コンピューティング リソース数を持つ 1 つのデータ ウェアハウスは、1 コンピューティング ノードあたりすべてのディストリビューションを持ちます。  

## <a name="hash-distributed-tables"></a>ハッシュ分散テーブル
ハッシュ分散テーブルでは、大きなテーブルでの結合と集計用に最高のクエリ パフォーマンスを実現できます。 

ハッシュ分散テーブルにデータをシャードするために、SQL Data Warehouse はハッシュ関数を使用して各行を 1 つのディストリビューションに確実に割り当てます。 テーブルの定義では、1 つの列をディストリビューション列として指定します。 ハッシュ関数は、ディストリビューション列の値を使用してディストリビューションに各行を割り当てます。

次の図は、完全な (分散していない) テーブルがハッシュ分散テーブルとして保存されるしくみを示したものです。 

![分散テーブル](media/sql-data-warehouse-distributed-data/hash-distributed-table.png "分散テーブル")  

* 各行は、1 つのディストリビューションに属しています。  
* 確定ハッシュ アルゴリズムが、各行を 1 つのディストリビューションに割り当てます。  
* ディストリビューションごとのテーブル行の数は、異なるテーブル サイズによって示されるように異なります。

ディストリビューション列の選択については、差異性、データ スキュー、およびシステムで実行されるクエリの種類など、パフォーマンスに関する考慮事項があります。

## <a name="round-robin-distributed-tables"></a>ラウンドロビン分散テーブル
ラウンド ロビン テーブルは作成が最も簡単なテーブルで、読み込み用のステージング テーブルとして使用した場合、高速なパフォーマンスを実現します。

ラウンドロビン分散テーブルでは、テーブル間にデータが均等に配布されますが、最適化は行われません。 1 つのディストリビューションがまずランダムに選択されたら、行のバッファーが順次ディストリビューションに割り当てられます。 ラウンド ロビン テーブルではデータは素早く読み込まれますが、通常、ハッシュ分散テーブルの方が高いクエリ パフォーマンスが見込まれます。 ラウンド ロビン テーブルの結合では、データの再シャッフルが必要になり、追加の時間がかかります。


## <a name="replicated-tables"></a>レプリケート テーブル
レプリケート テーブルは、小さなテーブル用に最も高速なクエリ パフォーマンスを実現します。

レプリケートされたテーブルは、各コンピューティング ノードにテーブルの完全なコピーをキャッシュします。 その結果、テーブルをレプリケートすると、結合または集計の前に、コンピューティング ノード間にデータを転送する必要がなくなります。 レプリケート テーブルは小さいテーブルに最適です。 大きいテーブルの使用が非現実的なデータを書き込む際には、追加ストレージが必要になり、追加のオーバーヘッドが発生します。  

次の図に、レプリケート テーブルを示します。 SQL Data Warehouse の場合は、レプリケート テーブルは各コンピューティング ノードの最初のディストリビューションにキャッシュされます。  

![レプリケート テーブル](media/sql-data-warehouse-distributed-data/replicated-table.png "レプリケート テーブル") 

## <a name="next-steps"></a>次の手順
SQL Data Warehouse の概要については学習したので、次はすばやく [SQL Data Warehouse を作成][create a SQL Data Warehouse]し、[サンプル データを読み込む][load sample data]方法について学習してください。 Azure に慣れていない場合に新しい用語を調べるには、[Azure 用語集][Azure glossary]が役立ちます。 または、次の SQL Data Warehouse リソースも確認できます。  

* [顧客の成功事例]
* [ブログ]
* [機能に関する要求]
* [ビデオ]
* [Customer Advisory Team のブログ]
* [サポート チケットを作成する]
* [MSDN フォーラム]
* [Stack Overflow フォーラム]
* [Twitter]

<!--Image references-->
[1]: ./media/sql-data-warehouse-overview-what-is/dwarchitecture.png

<!--Article references-->
[サポート チケットを作成する]: ./sql-data-warehouse-get-started-create-support-ticket.md
[load sample data]: ./sql-data-warehouse-load-sample-databases.md
[create a SQL Data Warehouse]: ./sql-data-warehouse-get-started-provision.md
[Migration documentation]: ./sql-data-warehouse-overview-migrate.md
[SQL Data Warehouse solution partners]: ./sql-data-warehouse-partner-business-intelligence.md
[Integrated tools overview]: ./sql-data-warehouse-overview-integrate.md
[Backup and restore overview]: ./sql-data-warehouse-restore-database-overview.md
[Azure glossary]: ../azure-glossary-cloud-terminology.md

<!--MSDN references-->

<!--Other Web references-->
[顧客の成功事例]: https://azure.microsoft.com/case-studies/?service=sql-data-warehouse
[ブログ]: https://azure.microsoft.com/blog/tag/azure-sql-data-warehouse/
[Customer Advisory Team のブログ]: https://blogs.msdn.microsoft.com/sqlcat/tag/sql-dw/
[機能に関する要求]: https://feedback.azure.com/forums/307516-sql-data-warehouse
[MSDN フォーラム]: https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureSQLDataWarehouse
[Stack Overflow フォーラム]: https://stackoverflow.com/questions/tagged/azure-sqldw
[Twitter]: https://twitter.com/hashtag/SQLDW
[ビデオ]: https://azure.microsoft.com/documentation/videos/index/?services=sql-data-warehouse
[SLA for SQL Data Warehouse]: https://azure.microsoft.com/support/legal/sla/sql-data-warehouse/v1_0/
[Volume Licensing]: https://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=37
[Service Level Agreements]: https://azure.microsoft.com/support/legal/sla/
