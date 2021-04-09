---
title: Hyperscale でのパフォーマンス診断
description: この記事では、Azure SQL Database での Hyperscale のパフォーマンスの問題をトラブルシューティングする方法について説明します。
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: seo-lt-2019 sqldbrb=1
ms.topic: troubleshooting
author: denzilribeiro
ms.author: denzilr
ms.reviewer: sstein
ms.date: 10/18/2019
ms.openlocfilehash: ed31ff5d77b258d141a77fc174c2d5452adf7d01
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "92791717"
---
# <a name="sql-hyperscale-performance-troubleshooting-diagnostics"></a>SQL Hyperscale のパフォーマンスのトラブルシューティング診断
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Hyperscale データベースでのパフォーマンスの問題のトラブルシューティングを行うには、Azure SQL Database の計算ノードに対する[一般的なパフォーマンスのチューニング方法](monitor-tune-overview.md)が、パフォーマンス調査の開始点となります。 ただし、Hyperscale の[分散アーキテクチャ](service-tier-hyperscale.md#distributed-functions-architecture)を考慮して、役立つ診断がさらに追加されています。 この記事では、Hyperscale 固有の診断データについて説明します。

## <a name="log-rate-throttling-waits"></a>ログ速度調整の待機

すべての Azure SQL Database サービス レベルには、[ログ速度ガバナンス](resource-limits-logical-server.md#transaction-log-rate-governance)によって適用されるログ生成速度制限があります。 Hyperscale では、サービス レベルに関係なく、ログ生成の制限は現在 100 MB/秒に設定されています。 ただし、復元可能性 SLA を維持するために、プライマリ コンピューティング レプリカのログ生成速度を調整する必要がある場合もあります。 この調整は、[ページ サーバーまたは別のコンピューティング レプリカ](service-tier-hyperscale.md#distributed-functions-architecture)が、ログ サービスからの新しいログ レコードの適用で大幅に遅れている場合に発生します。

次の待機の種類 ([sys.dm_os_wait_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-os-wait-stats-transact-sql/) 内) は、プライマリ コンピューティング レプリカでログ速度を調整できる理由を示しています。

|待機の種類    |説明                         |
|-------------          |------------------------------------|
|RBIO_RG_STORAGE        | ページ サーバーでのログ使用の遅延が原因で Hyperscale データベースのプライマリ 計算ノードのログ生成速度が調整されているときに発生します。         |
|RBIO_RG_DESTAGE        | 長期ログ ストレージによるログ使用の遅延が原因で Hyperscale データベースの計算ノードのログ生成速度が調整されているときに発生します。         |
|RBIO_RG_REPLICA        | 読み取り可能なセカンダリ レプリカによるログ使用の遅延が原因で、Hyperscale データベースの計算ノードのログ生成速度が調整されているときに発生します。         |
|RBIO_RG_LOCALDESTAGE   | ログ サービスによるログ使用の遅延が原因で Hyperscale データベースの計算ノードのログ生成速度が調整されているときに発生します。         |

## <a name="page-server-reads"></a>ページ サーバーの読み取り

コンピューティング レプリカでは、データベースの完全なコピーがローカルにキャッシュされません。 コンピューティング レプリカにローカルなデータはバッファー プール (メモリ内) と、データ ページの部分的な (カバーされていない) キャッシュであるローカル RBPEX (弾性バッファー プール拡張機能) キャッシュに格納されます。 このローカル RBPEX キャッシュは、コンピューティング サイズに比例してサイズが調整され、コンピューティング レベルのメモリの 3 倍となります。 RBPEX は、最も頻繁にアクセスされるデータが含まれているという点でバッファー プールに似ています。 一方、各ページ サーバーには、保持するデータベースの部分をカバーする RBPEX キャッシュがあります。

コンピューティング レプリカに対して読み取りが発行されると、バッファー プールまたはローカル RBPEX キャッシュにデータが存在しない場合は、getPage(pageId, LSN) 関数呼び出しが発行され、対応するページ サーバーからそのページがフェッチされます。 ページ サーバーからの読み取りはリモート読み取りであるため、ローカル RBPEX からの読み取りよりも低速です。 IO 関連のパフォーマンスの問題のトラブルシューティングを行うときは、比較的低速なリモート ページ サーバー読み取りによって IO が何回実行されたのかを把握できる必要があります。

いくつかの DMV (Dynamic Managed View) および拡張イベントには、ページ サーバーからのリモート読み取りの数を指定する列とフィールドがあり、この数を合計読み取り数と比較できます。 クエリ ストアでは、クエリ実行時間の統計の一部として、リモート読み取りもキャプチャされます。

- ページ サーバー読み取りをレポートする列は、実行 DMV およびカタログ ビューで利用でき、次のようなものがあります。

  - [sys.dm_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-requests-transact-sql/)
  - [sys.dm_exec_query_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-query-stats-transact-sql/)
  - [sys.dm_exec_procedure_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-procedure-stats-transact-sql/)
  - [sys.dm_exec_trigger_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-trigger-stats-transact-sql/)
  - [sys.query_store_runtime_stats](/sql/relational-databases/system-catalog-views/sys-query-store-runtime-stats-transact-sql/)
- ページ サーバー読み取りは、次の拡張イベントに追加されます。
  - sql_statement_completed
  - sp_statement_completed
  - sql_batch_completed
  - rpc_completed
  - scan_stopped
  - query_store_begin_persist_runtime_stat
  - query-store_execution_runtime_info
- ActualPageServerReads/ActualPageServerReadAheads が、実際のプランのクエリ プラン XML に追加されます。 次に例を示します。

`<RunTimeCountersPerThread Thread="8" ActualRows="90466461" ActualRowsRead="90466461" Batches="0" ActualEndOfScans="1" ActualExecutions="1" ActualExecutionMode="Row" ActualElapsedms="133645" ActualCPUms="85105" ActualScans="1" ActualLogicalReads="6032256" ActualPhysicalReads="0" ActualPageServerReads="0" ActualReadAheads="6027814" ActualPageServerReadAheads="5687297" ActualLobLogicalReads="0" ActualLobPhysicalReads="0" ActualLobPageServerReads="0" ActualLobReadAheads="0" ActualLobPageServerReadAheads="0" />`

> [!NOTE]
> クエリプランのプロパティウィンドウでこれらの属性を表示するには、SSMS 18.3 以降が必要です。

## <a name="virtual-file-stats-and-io-accounting"></a>仮想ファイルの統計と IO アカウンティング

Azure SQL Database では、SQL Database IO を監視する主な方法は、[sys.dm_io_virtual_file_stats()](/sql/relational-databases/system-dynamic-management-views/sys-dm-io-virtual-file-stats-transact-sql/) DMF です。 Hyperscale の IO 特性は、その [分散アーキテクチャ](service-tier-hyperscale.md#distributed-functions-architecture) によって異なります。 このセクションでは、この DMF で表示されるデータ ファイルへの IO (読み取りと書き込み) に焦点を当てます。 Hyperscale では、この DMF で表示される各データ ファイルは、1 つのリモート ページ サーバーに対応します。 ここで説明する RBPEX キャッシュは、コンピューティング レプリカ上でカバーされていないキャッシュである SSD ベースのローカル キャッシュです。

### <a name="local-rbpex-cache-usage"></a>ローカル RBPEX キャッシュの使用

ローカル RBPEX キャッシュは、ローカル SSD ストレージの計算レプリカ上に存在します。 そのため、このキャッシュの IO は、リモート ページ サーバーの IO より高速です。 現在、Hyperscale データベースの [sys.dm_io_virtual_file_stats()](/sql/relational-databases/system-dynamic-management-views/sys-dm-io-virtual-file-stats-transact-sql/) には、コンピューティング レプリカのローカル RBPEX キャッシュに対して実行された IO を報告する特別な行があります。 この行の `database_id` と `file_id` の両方の列の値は 0 です。 たとえば、次のクエリでは、データベースの起動以降の RBPEX 使用状況の統計が返されます。

`select * from sys.dm_io_virtual_file_stats(0,NULL);`

RBPEX で実行された読み取りと、他のすべてのデータ ファイルに対して行われた集約読み取りとの比率から、RBPEX キャッシュ ヒット率が得られます。

### <a name="data-reads"></a>データ読み取り

- コンピューティング レプリカ上の SQL Server データベース エンジンによって読み取りが発行されると、それらの読み取りは、ローカル RBPEX キャッシュかリモート ページ サーバー、または複数のページから読み取る場合はこの 2 つの組み合わせによって処理される可能性があります。
- コンピューティング レプリカが特定のファイル (file_id 1 など) から一部のページを読み取るとき、このデータがローカル RBPEX キャッシュにのみ存在する場合、この読み取りのすべての IO は file_id 0 (RBPEX) に対するものと見なされます。 そのデータの一部がローカル RBPEX キャッシュにあり、一部がリモート ページ サーバーにある場合、IO は、RBPEX から提供される部分については file_id 0 に対するものと見なされ、リモート ページ サーバーから提供される部分については file_id 1 に対するものと見なされます。
- コンピューティング レプリカがページ サーバーから特定の [LSN](/sql/relational-databases/sql-server-transaction-log-architecture-and-management-guide/) でページを要求したときに、ページ サーバーが、要求された LSN に追いついていない場合、コンピューティング レプリカでの読み取りは、ページ サーバーが追いつくまで待機してから、そのページがコンピューティング レプリカに返されます。 コンピューティング レプリカでのページ サーバーからの読み取りでは、その IO で待機している場合、PAGEIOLATCH_* の待機の種類が表示されます。 Hyperscale では、この待機時間には、ページサーバー上の要求されたページを必要なLSNに追いつくための時間と、ページサーバーからコンピューティング レプリカにページを転送するために必要な時間の両方が含まれます。
- 先読みなどの大規模な読み取りは、多くの場合、["スキャッター/ギャザー" 読み取り](/sql/relational-databases/reading-pages/)を使用して行われます。 これにより、一度に最大 4 MB のページの読み取りが可能になります。これは、SQL Server データベース エンジンでの 1 回の読み取りと見なされます。 ただし、読み取り中のデータが RBPEX に存在する場合、バッファー プールと RBPEX で常に 8 KB のページが使用されるため、これらの読み取りは複数の個別の 8 KB の読み取りと見なされます。 その結果、RBPEX に対して表示される読み取り IO の数が、エンジンによって実行された実際の IO 数よりも大きくなる場合があります。

### <a name="data-writes"></a>データ書き込み

- プライマリ コンピューティング レプリカは、ページ サーバーに直接書き込みません。 代わりに、ログ サービスからのログ レコードは対応するページ サーバーで再生されます。
- コンピューティング レプリカで発生する書き込みは、主にローカル RBPEX (file_id 0) に書き込まれます。 8 KB を超える論理ファイルの書き込み (つまり、[ギャザー書込み](/sql/relational-databases/writing-pages/)を使用して実行されるもの) では、バッファー プールと RBPEX は常に 8 KB のページを使用するため、各書き込み操作は RBPEX への複数の 8 KB の個別書き込みに変換されます。 その結果、RBPEX に対して表示される書き込み IO の数が、エンジンによって実行された実際の IO 数よりも大きくなる場合があります。
- RBPEX 以外のファイル、またはページ サーバーに対応する file_id 0 以外のデータ ファイルにも、書き込み回数が表示されます。 Hyperscale サービス レベルでは、コンピューティング レプリカがページ サーバーに直接書き込むことはないため、これらの書き込みはシミュレートされます。 書き込み IOPS とスループットは、コンピューティング レプリカで発生すると見なされますが、file_id 0 以外のデータ ファイルの待機時間は、ページ サーバーの書き込みの実際の待機時間を反映しません。

### <a name="log-writes"></a>ログ書き込み

- プライマリ コンピューティングでは、ログ書き込みは sys.dm_io_virtual_file_stats の file_id 2 に相当します。 プライマリ コンピューティングでのログ書き込みは、ログ ランディング ゾーンへの書き込みです。
- ログ レコードは、セカンダリ レプリカにはコミット時に書き込まれません。 Hyperscale では、ログは、ログ サービスによってセカンダリ レプリカに非同期的に適用されます。 セカンダリ レプリカではログ書き込みが実際には発生しないため、セカンダリ レプリカでのログ IO のアカウンティングはすべて、追跡のみを目的にしています。

## <a name="data-io-in-resource-utilization-statistics"></a>リソース使用率の統計でのデータ IO

ハイパースケール以外のデータベースでは、データファイルに対する合計読み取り/書き込み IOPS は、[リソース ガバナンス](./resource-limits-logical-server.md#resource-governance) データの IOPS 制限を基準として、`avg_data_io_percent` 列の [dm_db_resource_stats](/sql/relational-databases/system-catalog-views/sys-resource-stats-azure-sql-database) および [sys.resource_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database) ビューで報告されます。 Azure portal では、同じ値が _データ IO の割合_ として報告されます。

Hyperscale データベースでは、この列は、データ IOPS の使用率を、コンピューティング レプリカのみのローカル ストレージの制限 (特に RBPEX および `tempdb` に対する IO) を基準として報告します。 この列の100% 値は、リソース ガバナンスがローカル ストレージの IOPS を制限していることを示します。 これがパフォーマンスの問題に関連付けられている場合は、負荷の少ない IO を生成するようにワークロードを調整するか、データベースサービスの目標値を増やして、リソース ガバナンス _最大データ IOPS_ [制限](resource-limits-vcore-single-databases.md) を増やします。 RBPEX の読み取りと書き込みのリソース ガバナンスでは、SQL Server データベース エンジンによって発行される可能性のあるより大きな IO ではなく、個々の 8 KB の IO がカウントされます。

リモートページサーバーに対するデータ IO は、リソース使用率ビューまたはポータルでは報告されませんが、前述のように、[sys.dm_io_virtual_file_stats()](/sql/relational-databases/system-dynamic-management-views/sys-dm-io-virtual-file-stats-transact-sql/) DMF で報告されます。

## <a name="additional-resources"></a>その他のリソース

- Hyperscale の単一データベースに対する仮想コア リソースの制限については、[Hyperscale サービス レベルの仮想コアの制限](resource-limits-vcore-single-databases.md#hyperscale---provisioned-compute---gen5) に関する記事を参照してください
- Azure SQL Database のパフォーマンスのチューニングについては、[Azure SQL Database でのクエリのパフォーマンス](performance-guidance.md)に関する記事を参照してください
- クエリ ストアを使用したパフォーマンスのチューニングについては、[クエリ ストアを使用したパフォーマンス監視](/sql/relational-databases/performance/monitoring-performance-by-using-the-query-store/)に関する記事を参照してください
- DMV の監視スクリプトについては、「[動的管理ビューを使用して Azure SQL Database のパフォーマンスを監視する](monitoring-with-dmvs.md)」を参照してください