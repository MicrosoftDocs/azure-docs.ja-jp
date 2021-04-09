---
title: 高密度エラスティック プールでのリソース管理
description: 多くのデータベースが含まれる Azure SQL Database エラスティック プールでコンピューティング リソースを管理します。
services: sql-database
ms.service: sql-database
ms.subservice: elastic-pools
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: dimitri-furman
ms.author: dfurman
ms.reviewer: sstein
ms.date: 09/16/2020
ms.openlocfilehash: 40b6c5a86184860cf3e7a9840f980706485ae977
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "100572236"
---
# <a name="resource-management-in-dense-elastic-pools"></a>高密度エラスティック プールでのリソース管理
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Azure SQL Database の[エラスティック プール](./elastic-pool-overview.md)は、リソース使用量が異なる多数のデータベースを管理するためのコスト効果の高いソリューションです。 特定の時点では **プール内のデータベースのサブセットのみがコンピューティング リソースを使用する** という前提で、エラスティック プール内のすべてのデータベースにより、CPU、メモリ、ワーカー スレッド、ストレージ スペース、tempdb などのリソースの同じ割り当てが共有されます。 この前提により、エラスティック プールのコスト効率が高くなります。 お客様は、個々のデータベースで必要になる可能性のあるすべてのリソースに対して料金を支払うのではなく、プール内のすべてのデータベース間で共有される、はるかに少ないリソースのセットに対して支払います。

## <a name="resource-governance"></a>リソース管理

リソースを共有するには、リソース消費量の多いデータベースによって同じエラスティック プール内の他のデータベースが影響を受ける "迷惑な隣人" 効果を最小限にするため、システムでリソースの使用量を慎重に制御する必要があります。 同時に、高可用性とディザスター リカバリー (HADR)、バックアップと復元、監視、クエリ ストア、自動チューニングなどの機能が確実に機能するよう、システムで十分なリソースを提供する必要があります。

Azure SQL Database では、これらの目標を達成するために、複数のリソース ガバナンス メカニズムが使用されます。これには、プロセス レベルのリソース ガバナンスのための Windows の[ジョブ オブジェクト](/windows/win32/procthread/job-objects)、ストレージ クォータ管理のための Windows の[ファイル サーバー リソース マネージャー (FSRM)](/windows-server/storage/fsrm/fsrm-overview)、Azure SQL Database 内にリソース ガバナンスを実装するための SQL Server の [Resource Governor](/sql/relational-databases/resource-governor/resource-governor) が変更および拡張されたバージョンが含まれます。

エラスティック プールの主な設計目標は、優れたコスト効率です。 このため、システムは意図的に、"_高密度_" のプールを作成できるようになっています。これは、データベースの数が許容される上限に近いか達していても、コンピューティング リソースの割り当てが適度であるようなプールです。 同じ理由から、システムでは、必要なすべてのリソースが内部プロセス用に予約されるのではなく、内部プロセスとユーザー ワークロードでリソースを共有できるようになっています。

この方法により、お客様は、高密度エラスティック プールを使用して、適切なパフォーマンスと大幅なコスト削減を実現できます。 ただし、高密度プール内の多数のデータベースに対するワークロードにかなりの負荷がかかっている場合、リソースの競合が大きくなります。 リソースが競合すると、ユーザー ワークロードのパフォーマンスが低下し、内部プロセスに悪影響を及ぼす可能性があります。

> [!IMPORTANT]
> アクティブなデータベースが多数ある高密度プールでは、プール内のデータベースの数を、[DTU](resource-limits-dtu-elastic-pools.md) および[仮想コア](resource-limits-vcore-elastic-pools.md)のエラスティック プールについて記載された最大数まで増やすことができない可能性があります。
>
> リソースの競合やパフォーマンスの問題を発生させることなく高密度プールに配置できるデータベースの数は、同時にアクティブになっているデータベースの数と、各データベースのユーザー ワークロードによるリソース消費量によって決まります。 この数は、ユーザー ワークロードの変化に応じて変わる可能性があります。

高密度のプールでリソースの競合が発生する場合、お客様は次の 1 つまたは複数の操作を選択して軽減できます。

- クエリ ワークロードを調整してリソースの消費量を減らすか、時間の経過に応じて複数のデータベース間でリソース消費を分散します。
- 一部のデータベースを別のプールに移動するか、スタンドアロン データベースにすることにより、プールの密度を下げます。
- プールをスケールアップしてリソースを増やします。

最後の 2 つの操作の実装方法については、この記事の後半にある「[運用に関する推奨事項](#operational-recommendations)」を参照してください。 リソースの競合を減らすと、ユーザーのワークロードと内部プロセスの両方にメリットがあり、システムで予想されるサービス レベルを確実に維持できます。

## <a name="monitoring-resource-consumption"></a>リソース消費量の監視

高密度エラスティック プールを使用しているお客様は、リソースの競合によるパフォーマンスの低下を回避するため、リソースの消費を予防的に監視し、リソースの競合の増加がワークロードに影響を与え始めたらタイムリーに対処する必要があります。 ユーザーのワークロードの変化、データの量と分散の変化、プールの密度の変更、Azure SQL Database サービスの変更などにより、プール内のリソースの使用量は時間と共に変化するため、継続的に監視することが重要です。

Azure SQL Database では、この種の監視に関連する複数のメトリックが提供されています。 メトリックごとに推奨されている平均値を超えた場合は、プール内のリソースの競合を示しており、前述の操作のいずれかを使用して対処する必要があります。

|メトリックの名前|説明|推奨される平均値|
|----------|--------------------------------|------------|
|`avg_instance_cpu_percent`|エラスティック プールに関連付けられている SQL プロセスの CPU 使用率。基になるオペレーティング システムによって測定されます。 すべてのデータベースの [sys.dm_db_resource_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database) ビュー、および `master` データベースの [sys.elastic_pool_resource_stats](/sql/relational-databases/system-catalog-views/sys-elastic-pool-resource-stats-azure-sql-database) ビューで使用できます。 このメトリックは Azure Monitor にも出力され (`sqlserver_process_core_percent` という[名前](../../azure-monitor/essentials/metrics-supported.md#microsoftsqlserverselasticpools)で)、Azure portal で表示できます。 この値は、同じエラスティック プール内のすべてのデータベースについて同じです。|70% 未満。 90% までの短時間の急増がときどき発生するのは、許容される場合があります。|
|`max_worker_percent`|[ワーカー スレッド]( https://docs.microsoft.com/sql/relational-databases/thread-and-task-architecture-guide)の使用率。 プール内の各データベースおよびプール自体に対して提供されます。 ワーカー スレッド数の制限はデータベース レベルとプール レベルで異なるため、両方のレベルでこのメトリックを監視することをお勧めします。 すべてのデータベースの [sys.dm_db_resource_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database) ビュー、および `master` データベースの [sys.elastic_pool_resource_stats](/sql/relational-databases/system-catalog-views/sys-elastic-pool-resource-stats-azure-sql-database) ビューで使用できます。 このメトリックは Azure Monitor にも出力され (`workers_percent` という[名前](../../azure-monitor/essentials/metrics-supported.md#microsoftsqlserverselasticpools)で)、Azure portal で表示できます。|80% 未満。 100% まで急増すると、接続の試行とクエリが失敗します。|
|`avg_data_io_percent`|読み取りおよび書き込みの物理 IO に対する IOPS 使用率。 プール内の各データベースおよびプール自体に対して提供されます。 IOPS の値の制限はデータベース レベルとプール レベルで異なるため、両方のレベルでこのメトリックを監視することをお勧めします。 すべてのデータベースの [sys.dm_db_resource_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database) ビュー、および `master` データベースの [sys.elastic_pool_resource_stats](/sql/relational-databases/system-catalog-views/sys-elastic-pool-resource-stats-azure-sql-database) ビューで使用できます。 このメトリックは Azure Monitor にも出力され (`physical_data_read_percent` という[名前](../../azure-monitor/essentials/metrics-supported.md#microsoftsqlserverselasticpools)で)、Azure portal で表示できます。|80% 未満。 100% までの短時間の急増がときどき発生するのは、許容される場合があります。|
|`avg_log_write_percent`|トランザクション ログ書き込み IO に対するスループットの使用率。 プール内の各データベースおよびプール自体に対して提供されます。 ログ スループットの制限はデータベース レベルとプール レベルで異なるため、両方のレベルでこのメトリックを監視することをお勧めします。 すべてのデータベースの [sys.dm_db_resource_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database) ビュー、および `master` データベースの [sys.elastic_pool_resource_stats](/sql/relational-databases/system-catalog-views/sys-elastic-pool-resource-stats-azure-sql-database) ビューで使用できます。 このメトリックは Azure Monitor にも出力され (`log_write_percent` という[名前](../../azure-monitor/essentials/metrics-supported.md#microsoftsqlserverselasticpools)で)、Azure portal で表示できます。 このメトリックが 100% に近づくと、すべてのデータベースの変更 (INSERT、UPDATE、DELETE、MERGE ステートメント、SELECT... INTO、BULK INSERT など) が遅くなります。|90% 未満。 100% までの短時間の急増がときどき発生するのは、許容される場合があります。|
|`oom_per_second`|エラスティック プールでのメモリ不足 (OOM) エラーの割合。これは、メモリ負荷のインジケーターです。 [sys.dm_resource_governor_resource_pools_history_ex](/sql/relational-databases/system-dynamic-management-views/sys-dm-resource-governor-resource-pools-history-ex-azure-sql-database) ビューで使用できます。 このメトリックを計算するサンプル クエリについては、「[例](#examples)」を参照してください。|0|
|`avg_storage_percent`|エラスティック プール内のすべてのデータベースのデータによって使用される記憶領域の合計。 データベース ファイル内の空き領域は含まれません。 `master` データベースの [sys.elastic_pool_resource_stats](/sql/relational-databases/system-catalog-views/sys-elastic-pool-resource-stats-azure-sql-database) ビューで使用できます。 このメトリックは Azure Monitor にも出力され (`storage_percent` という[名前](../../azure-monitor/essentials/metrics-supported.md#microsoftsqlserverselasticpools)で)、Azure portal で表示できます。|80% 未満。 データが増加しないプールの場合、100% に近くなってもかまいません。|
|`avg_allocated_storage_percent`|エラスティック プール内のすべてのデータベースに格納されているデータベース ファイルによって使用される記憶領域の合計。 データベース ファイル内の空き領域が含まれます。 `master` データベースの [sys.elastic_pool_resource_stats](/sql/relational-databases/system-catalog-views/sys-elastic-pool-resource-stats-azure-sql-database) ビューで使用できます。 このメトリックは Azure Monitor にも出力され (`allocated_data_storage_percent` という[名前](../../azure-monitor/essentials/metrics-supported.md#microsoftsqlserverselasticpools)で)、Azure portal で表示できます。|90% 未満。 データが増加しないプールの場合、100% に近くなってもかまいません。|
|`tempdb_log_used_percent`|`tempdb` データベースのトランザクション ログ領域使用率。 1 つのデータベースに作成される一時オブジェクトは同じエラスティック プール内の他のデータベースに認識されませんが、`tempdb` は同じプール内のすべてのデータベースの共有リソースです。 プール内の 1 つのデータベースから `tempdb` で開始された、実行時間の長いトランザクションまたは孤立したトランザクションにより、トランザクション ログの大部分が消費され、同じプール内の他のデータベースのクエリが失敗する可能性があります。 [sys.dm_db_log_space_usage](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-log-space-usage-transact-sql) ビュー、および [sys.database_files](/sql/relational-databases/system-catalog-views/sys-database-files-transact-sql) ビューから派生します。 このメトリックは Azure Monitor にも出力され、Azure portal で表示できます。 このメトリックの現在の値を返すサンプル クエリについては、「[例](#examples)」を参照してください。|50% 未満。 散発的な 80% までの急増が許容されます。|
|||

これらのメトリックに加えて、Azure SQL Database には、実際のリソース ガバナンスの制限を返すビューや、リソース プール レベルおよびワークロード グループ レベルでのリソース使用率の統計を返す追加のビューが用意されています。

|ビューの名前|説明|  
|-----------------|--------------------------------|  
|[sys.dm_user_db_resource_governance](/sql/relational-databases/system-dynamic-management-views/sys-dm-user-db-resource-governor-azure-sql-database)|現在のデータベースまたはエラスティック プールのリソース ガバナンス メカニズムによって使用されている実際の構成と容量の設定が返されます。|
|[sys.dm_resource_governor_resource_pools](/sql/relational-databases/system-dynamic-management-views/sys-dm-resource-governor-resource-pools-transact-sql)|現在のリソース プールの状態、リソース プールの現在の構成、およびリソース プールの累積統計に関する情報が返されます。|
|[sys.dm_resource_governor_workload_groups](/sql/relational-databases/system-dynamic-management-views/sys-dm-resource-governor-workload-groups-transact-sql)|ワークロード グループの累積統計と、ワークロード グループの現在の構成が返されます。 このビューを `pool_id` 列の sys.dm_resource_governor_resource_pools と結合して、リソース プールの情報を取得できます。|
|[sys.dm_resource_governor_resource_pools_history_ex](/sql/relational-databases/system-dynamic-management-views/sys-dm-resource-governor-resource-pools-history-ex-azure-sql-database)|過去 32 分間のリソース プール使用率の統計が返されます。 各行は 20 秒間隔を表します。 `delta_` 列では、間隔の間の各統計の変化が返されます。|
|[sys.dm_resource_governor_workload_groups_history_ex](/sql/relational-databases/system-dynamic-management-views/sys-dm-resource-governor-workload-groups-history-ex-azure-sql-database)|過去 32 分間のワークロード グループ使用率の統計が返されます。 各行は 20 秒間隔を表します。 `delta_` 列では、間隔の間の各統計の変化が返されます。|
|||

これらのビューを使って、リソースの使用率を監視し、ほぼリアルタイムでリソースの競合のトラブルシューティングを行うことができます。 プライマリ レプリカおよび読み取り可能なセカンダリ レプリカ (geo レプリカを含む) のユーザー ワークロードは、`SloSharedPool1` リソース プールと `UserPrimaryGroup.DBId[N]` ワークロード グループに分類されます。`N` はデータベース ID の値を表します。

高密度プールを使用しているお客様は、現在のリソース使用率を監視するだけでなく、リソース使用率の履歴データを別のデータ ストアに保持することもできます。 このデータを予測分析に使用し、過去および季節の傾向に基づいてリソース使用率を事前に管理できます。

## <a name="operational-recommendations"></a>運用に関する推奨事項

**リソースに十分な余裕を残しておく**。 リソースの競合とパフォーマンスの低下が発生した場合の軽減策としては、前に説明したように、影響を受けているエラスティック プールからの一部のデータベースの移動や、プールのスケールアップなどがあります。 ただし、これらの操作を完了するには、追加のコンピューティング リソースが必要です。 特に、Premium プールと Business Critical プールでこれらの操作を行うと、移動するデータベースのすべてのデータを転送したり、プールをスケールアップする場合はエラスティック プール内のすべてのデータベースを転送したりする必要があります。 データ転送は、実行時間が長く、リソースを大量に消費する操作です。 既にプールに高いリソース負荷がかかっている場合は、軽減操作自体によってパフォーマンスがさらに低下します。 極端なケースでは、必要なリソースを使用できないために、データベースの移動やプールのスケールアップではリソースの競合を解決できない場合さえあります。 このような状況では、影響を受けているエラスティック プールのクエリ ワークロードを一時的に減らすことが、唯一の解決策になる場合があります。

高密度プールを使用するお客様は、前に説明したように、リソース使用率の傾向を厳密に監視し、メトリックが推奨範囲内に留まっていて、エラスティック プールに十分なリソースがまだ残っている間に、対策を講じる必要があります。

リソース使用率は、各データベースと各エラスティック プールでの、時間と共に変化する複数の要因に依存します。 高密度プールで最適な価格/パフォーマンス比を実現するには、継続的な監視と再調整が必要です。調整では、より使用率の高いプールから使用率の低いプールにデータベースを移動し、ワークロードの増加に対応するために必要に応じて新しいプールを作成します。

**"ホット" データベースを移動しない**。 プール レベルでのリソース競合の主な原因が、使用率が高い少数のデータベースである場合、これらのデータベースを使用率の低いプールに移動したり、スタンドアロン データベースにしたりすることがあります。 しかし、データベースの使用率が高い状態の間にこの操作を行うことはお勧めできません。移動操作によって、移動対象のデータベースとプール全体のパフォーマンスがさらに低下するためです。 代わりに、使用率が低下するまで待つか、または使用率が低いデータベースを移動して、プール レベルでリソースの負荷を軽減します。 ただし、使用率が非常に低いデータベースを移動しても、プール レベルでのリソースの使用率はあまり変わらないため、このケースではメリットはありません。

**"検査" プールに新しいデータベースを作成する**。 データベースごとのテナント モデルを使用するアプリケーションなど、新しいデータベースが頻繁に作成されるシナリオでは、既存のエラスティック プールに配置された新しいデータベースにより、予期せず、大量のリソースが消費され、プール内の他のデータベースや内部プロセスに影響する可能性があります。 このリスクを軽減するには、リソースが十分に割り当てられた "検査" プールを別に作成します。 リソース消費パターンがまだ不明な新しいデータベースには、このプールを使用します。 1 週間や 1 か月など、1 ビジネス サイクルだけデータベースをこのプールに保持すると、そのリソース消費量がわかり、この追加リソース使用量に対応できる十分な容量を持つプールに移動することができます。

**使用済み領域と割り当て済み領域の両方を監視する**。 割り当て済みのプール領域 (プール内のすべてのデータベースに格納されているすべてのデータベース ファイルの合計サイズ) が最大プール サイズに達すると、領域不足エラーが発生することがあります。 割り当て済み領域が高い傾向にあり、最大プール サイズに達しようとしている場合は、軽減策として次のような選択肢があります。
- プールから一部のデータベースを移動して、割り当て済み領域の合計を減らす
- データベース ファイルを[圧縮](file-space-manage.md)して、ファイルに割り当てられた空き領域を削減する
- 最大プール サイズをより大きくしたサービス目標にプールをスケールアップする

使用済みのプール領域 (ファイル内の空き領域を除く、プール内のすべてのデータベースのデータの合計サイズ) が高い傾向にあり、最大プール サイズに達しようとしている場合は、軽減策として次のような選択肢があります。
- プールから一部のデータベースを移動して、使用済み領域の合計を減らす
- データベースの外部にデータを移動 (アーカイブ) するか、必要なくなったデータを削除する
- [データ圧縮](/sql/relational-databases/data-compression/data-compression)を実施する
- 最大プール サイズをより大きくしたサービス目標にプールをスケールアップする

**サーバーを過度に高密度にするのを避ける**。 Azure SQL Database では、サーバーあたり最大 5,000 個のデータベースが[サポート](./resource-limits-logical-server.md)されています。 何千ものデータベースを含むエラスティック プールを使用しているお客様は、データベースの総数をサポートされている上限以下にして、1 台のサーバーに複数のエラスティック プールを配置することを検討できます。 しかし、何千ものデータベースが含まれるサーバーでは、運用上の課題が生じます。 たとえば、ポータルでのデータベースの表示など、サーバー上のすべてのデータベースを列挙する必要がある操作は遅くなります。 サーバー レベルのログインやファイアウォール規則の正しくない変更などの操作エラーは、多くのデータベースに影響します。 サーバーを誤って削除すると、削除したサーバー上のデータベースを復旧するために Microsoft サポートによる支援が必要になり、影響を受けるすべてのデータベースで長時間の停止が発生します。

サーバーあたりのデータベースの数を、サポートされている最大数より少ない数に制限することをお勧めします。 多くのシナリオでは、サーバーあたり最大 1,000 から 2,000 個のデータベースを使用するのが最適です。 サーバーを誤って削除する可能性を減らすために、サーバーまたはそのリソース グループに[削除ロック](../../azure-resource-manager/management/lock-resources.md)を設定することをお勧めします。

以前は、同じサーバー上でエラスティック プール内へ、エラスティック プール外へ、またはエラスティック プール間でデータベースを移動する操作を含む特定のシナリオは、サーバー間でデータベースを移動するより高速でした。 現在では、すべてのデータベースの移動は、転送元と転送先のサーバーに関係なく、同じ速度で実行されます。

## <a name="examples"></a>例

### <a name="monitoring-memory-utilization"></a>メモリ使用率の監視

このクエリでは、過去 32 分間の各リソース プールの `oom_per_second` メトリックが計算されます。 このクエリは、エラスティック プール内の任意のデータベースで実行できます。

```sql
SELECT pool_id,
       name AS resource_pool_name,
       IIF(name LIKE 'SloSharedPool%' OR name LIKE 'UserPool%', 'user', 'system') AS resource_pool_type,
       SUM(CAST(delta_out_of_memory_count AS decimal))/(SUM(duration_ms)/1000.) AS oom_per_second
FROM sys.dm_resource_governor_resource_pools_history_ex
GROUP BY pool_id, name
ORDER BY pool_id;
```

### <a name="monitoring-tempdb-log-space-utilization"></a>`tempdb` ログ領域の使用率の監視

このクエリは、`tempdb_log_used_percent` メトリックの現在の値を返し、その最大許容サイズを基準として `tempdb` トランザクション ログの相対的な使用率を示します。 このクエリは、エラスティック プール内の任意のデータベースで実行できます。

```sql
SELECT (lsu.used_log_space_in_bytes / df.log_max_size_bytes) * 100 AS tempdb_log_space_used_percent
FROM tempdb.sys.dm_db_log_space_usage AS lsu
CROSS JOIN (
           SELECT SUM(CAST(max_size AS bigint)) * 8 * 1024. AS log_max_size_bytes
           FROM tempdb.sys.database_files
           WHERE type_desc = N'LOG'
           ) AS df
;
```

## <a name="next-steps"></a>次のステップ

- エラスティック プールの概要については、「[Azure SQL Database におけるエラスティック プールを利用した複数のデータベースの管理およびスケーリング](./elastic-pool-overview.md)」をご覧ください。
- リソース使用率を減らすためのクエリ ワークロードのチューニングについては、「[監視とチューニング]( https://docs.microsoft.com/azure/sql-database/sql-database-monitoring-tuning-index)」および「[監視とパフォーマンスのチューニング](./monitor-tune-overview.md)」をご覧ください。