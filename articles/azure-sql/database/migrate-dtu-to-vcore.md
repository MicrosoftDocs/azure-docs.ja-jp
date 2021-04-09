---
title: DTU から仮想コアに移行する
description: Azure SQL Database 内のデータベースを、DTU モデルから仮想コア モデルに移行します。 仮想コアへの移行は、Standard レベルと Premium レベルの間でのアップグレードまたはダウングレードに似ています。
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.topic: conceptual
ms.custom: sqldbrb=1
author: stevestein
ms.author: sstein
ms.reviewer: sashan, moslake
ms.date: 02/09/2021
ms.openlocfilehash: 332a2273a377268a425619a0cdaa5f4780b46e73
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "100361657"
---
# <a name="migrate-azure-sql-database-from-the-dtu-based-model-to-the-vcore-based-model"></a>Azure SQL Database を DTU ベースのモデルから仮想コア ベースのモデルに移行する
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

この記事では、[DTU ベースの購入モデル](service-tiers-dtu.md)から[仮想コアベースの購入モデル](service-tiers-vcore.md)に Azure SQL Database 内のデータベースを移行する方法について説明します。 

## <a name="migrate-a-database"></a>データベースの移行

DTU ベースの購入モデルから仮想コア ベースの購入モデルにデータベースを移行することは、[期間](single-database-scale.md#latency)と、移行プロセスの終了時の[最小限のダウンタイム](scale-resources.md)が似ている、Basic、Standard、および Premium サービス レベルのサービス目標間のスケーリングに似ています。 仮想コアベースの購入モデルに移行されたデータベースは、同じようにいつでも DTU ベースの購入モデルに再び移行することができます。ただし、[Hyperscale](service-tier-hyperscale.md) サービス レベルに移行されたデータベースは除きます。 

## <a name="choose-the-vcore-service-tier-and-service-objective"></a>仮想コア サービス レベルとサービス目標を選択する

DTU から仮想コアへの移行のほとんどのシナリオでは、Basic および Standard サービス レベルのデータベースとエラスティック プールは、[General Purpose](service-tier-general-purpose.md) サービス レベルにマップされます。 Premium サービス レベルのデータベースとエラスティック プールは、[Business Critical](service-tier-business-critical.md) サービス レベルにマップされます。 アプリケーションのシナリオや要件に応じて、[Hyperscale](service-tier-hyperscale.md) サービス レベルは、多くの場合、すべての DTU サービス レベルで単一データベースの移行先として使用できます。

仮想コア モデル内の移行されたデータベースのサービス目標 (コンピューティング サイズ) を選択するために、シンプルではあるが、大まかな目安を使用できます。Basic または Standard レベルでは 100 DTU ごとに "*少なくとも*" 1 つの仮想コア、Premium レベルでは 125 DTU ごとに "*少なくとも*" 1 つの仮想コアが必要になります。 

> [!TIP]
> DTU データベースまたはエラスティック プールに使用されるハードウェアの世代は考慮されないため、この目安は大まかなものです。 

DTU モデルでは、使用可能な任意の[ハードウェアの世代](purchasing-models.md#hardware-generations-in-the-dtu-based-purchasing-model)をデータベースやエラスティック プールに使用できます。 さらに、より高いまたは低い DTU あるいは eDTU 値を選択することで、仮想コア (論理 CPU) の数を間接的にのみ制御できます。 

仮想コア モデルでは、お客様はハードウェアの世代と仮想コア (論理 CPU) の数の両方を明示的に選択する必要があります。 DTU モデルでは、このような選択肢は提供されませんが、ハードウェアの世代、およびすべてのデータベースとエラスティック プールに使用される論理 CPU の数は、動的管理ビューを介して公開されます。 これにより、一致する仮想コア サービスの目標をより正確に特定することができます。 

次の方法では、この情報を使用して、仮想コア モデルへの移行後に同様のレベルのパフォーマンスを得るために、リソースの割り当てが同様の仮想コア サービス目標を特定します。

### <a name="dtu-to-vcore-mapping"></a>DTU から仮想コアへのマッピング

以下の T-SQL クエリでは、移行される DTU データベースのコンテキストで実行した場合、仮想コア モデルのハードウェア世代ごとの仮想コアの一致する数 (場合によっては分数) を返します。 仮想コア モデルの各ハードウェア世代では、[データベース](resource-limits-vcore-single-databases.md)と[エラスティック プール](resource-limits-vcore-elastic-pools.md)で使用可能な仮想コアの最も近い数にこの数を丸めることによって、お客様は DTU データベースまたはエラスティック プールに最も近い一致である仮想コア サービス目標を選択できます。 

この方法を使用するサンプル移行シナリオについては、[例](#dtu-to-vcore-migration-examples)に関するセクションを参照してください。

このクエリは、`master` データベースではなく、移行されるデータベースのコンテキストで実行します。 エラスティック プールを移行する場合は、プール内の任意のデータベースのコンテキストでクエリを実行します。

```SQL
WITH dtu_vcore_map AS
(
SELECT rg.slo_name,
       DATABASEPROPERTYEX(DB_NAME(), 'Edition') AS dtu_service_tier,
       CASE WHEN rg.slo_name LIKE '%SQLG4%' THEN 'Gen4'
            WHEN rg.slo_name LIKE '%SQLGZ%' THEN 'Gen4'
            WHEN rg.slo_name LIKE '%SQLG5%' THEN 'Gen5'
            WHEN rg.slo_name LIKE '%SQLG6%' THEN 'Gen5'
            WHEN rg.slo_name LIKE '%SQLG7%' THEN 'Gen5'
       END AS dtu_hardware_gen,
       s.scheduler_count * CAST(rg.instance_cap_cpu/100. AS decimal(3,2)) AS dtu_logical_cpus,
       CAST((jo.process_memory_limit_mb / s.scheduler_count) / 1024. AS decimal(4,2)) AS dtu_memory_per_core_gb
FROM sys.dm_user_db_resource_governance AS rg
CROSS JOIN (SELECT COUNT(1) AS scheduler_count FROM sys.dm_os_schedulers WHERE status = 'VISIBLE ONLINE') AS s
CROSS JOIN sys.dm_os_job_object AS jo
WHERE dtu_limit > 0
      AND
      DB_NAME() <> 'master'
      AND
      rg.database_id = DB_ID()
)
SELECT dtu_logical_cpus,
       dtu_hardware_gen,
       dtu_memory_per_core_gb,
       dtu_service_tier,
       CASE WHEN dtu_service_tier = 'Basic' THEN 'General Purpose'
            WHEN dtu_service_tier = 'Standard' THEN 'General Purpose or Hyperscale'
            WHEN dtu_service_tier = 'Premium' THEN 'Business Critical or Hyperscale'
       END AS vcore_service_tier,
       CASE WHEN dtu_hardware_gen = 'Gen4' THEN dtu_logical_cpus
            WHEN dtu_hardware_gen = 'Gen5' THEN dtu_logical_cpus * 0.7
       END AS Gen4_vcores,
       7 AS Gen4_memory_per_core_gb,
       CASE WHEN dtu_hardware_gen = 'Gen4' THEN dtu_logical_cpus * 1.7
            WHEN dtu_hardware_gen = 'Gen5' THEN dtu_logical_cpus
       END AS Gen5_vcores,
       5.05 AS Gen5_memory_per_core_gb,
       CASE WHEN dtu_hardware_gen = 'Gen4' THEN dtu_logical_cpus
            WHEN dtu_hardware_gen = 'Gen5' THEN dtu_logical_cpus * 0.8
       END AS Fsv2_vcores,
       1.89 AS Fsv2_memory_per_core_gb,
       CASE WHEN dtu_hardware_gen = 'Gen4' THEN dtu_logical_cpus * 1.4
            WHEN dtu_hardware_gen = 'Gen5' THEN dtu_logical_cpus * 0.9
       END AS M_vcores,
       29.4 AS M_memory_per_core_gb
FROM dtu_vcore_map;
```

### <a name="additional-factors"></a>その他の要因

仮想コア (論理 CPU) の数とハードウェアの世代以外にも、仮想コア サービス目標の選択に影響する可能性がある他の要因がいくつかあります。

- マッピング T-SQL クエリでは、CPU 容量に関して DTU と仮想コア サービスの目標を照合するため、CPU にバインドされたワークロードの結果がより正確なものになります。
- 同じハードウェアの世代および同じ数の仮想コアについては、多くの場合、仮想コア データベースの IOPS とトランザクション ログのスループット リソース制限が DTU データベースよりも高くなります。 IO にバインドされたワークロードでは、同じレベルのパフォーマンスを実現するために、仮想コア モデルの仮想コア数を減らせる場合があります。 DTU および仮想コア データベースの絶対値のリソース制限は、[sys. dm_user_db_resource_governance](/sql/relational-databases/system-dynamic-management-views/sys-dm-user-db-resource-governor-azure-sql-database) ビューで公開されています。 ほぼ一致するサービス目標を使用する仮想コア データベースと、移行される DTU データベースとの間でのこれらの値の比較は、仮想コア サービスの目標をより正確に選択するのに役立ちます。
- また、マッピング クエリでは、移行される DTU データベースまたはエラスティック プール、および仮想コア モデルの各ハードウェアの、コアあたりのメモリ量を返します。 十分なパフォーマンスを実現するために大量のメモリ データ キャッシュを必要とするワークロード、またはクエリ処理に大量のメモリ許可を必要とするワークロードでは、仮想コアへの移行後に、同様のあるいはそれ以上の合計メモリを確保することが重要です。 このようなワークロードでは、実際のパフォーマンスに応じて、十分な合計メモリを得るために仮想コアの数を増やすことが必要になる場合があります。
- 仮想コア サービスの目標を選択する際には、DTU データベースの[リソース使用率の履歴](/sql/relational-databases/system-catalog-views/sys-resource-stats-azure-sql-database)を考慮する必要があります。 CPU リソースの使用率が常に低い DTU データベースでは、マッピング クエリで返されるよりも少ない仮想コアが必要になることがあります。 逆に、CPU 使用率が常に高いためにワークロードのパフォーマンスが不十分になる DTU データベースでは、クエリで返されるよりも多い仮想コアが必要になることがあります。
- 使用パターンが間欠的または予測できないデータベースを移行する場合は、[サーバーレス](serverless-tier-overview.md) コンピューティング レベルの使用を検討してください。 サーバーレスでの同時実行ワーカー (要求) の最大数は、構成されている同じ最大仮想コア数に対してプロビジョニングされたコンピューティングの上限の 75% であることに注意してください。 また、サーバーレスで使用できる最大メモリは、構成されている最大仮想コア数に 3 GB を乗算したものになります。たとえば、構成されている最大コア数が 40 のとき、最大メモリは 120 GB になります。   
- 仮想コア モデルでは、サポートされるデータベースの最大サイズが、ハードウェアの世代によって異なる場合があります。 大規模なデータベースの場合は、[単一データベース](resource-limits-vcore-single-databases.md)と[エラスティック プール](resource-limits-vcore-elastic-pools.md)の仮想コア モデルでサポートされる最大サイズを確認してください。
- エラスティック プールの場合、[DTU](resource-limits-dtu-elastic-pools.md) および[仮想コア](resource-limits-vcore-elastic-pools.md) モデルでは、プールあたりのデータベースの最大サポート数が異なります。 多くのデータベースがあるエラスティック プールを移行する場合は、このことを考慮する必要があります。
- ハードウェアの世代によっては、すべてのリージョンで使用できないものもあります。 「[ハードウェアの世代](service-tiers-vcore.md#hardware-generations)」で使用できるかどうかを確認してください。

> [!IMPORTANT]
> 上記の DTU から仮想コアへのサイズ変更のガイドラインは、ターゲット データベース サービス目標の最初の見積もりに役立つように提供されています。
>
> ターゲット データベースの最適な構成はワークロードに依存します。 したがって、移行後に価格とパフォーマンスの最適な比率を実現するには、仮想コア モデルの柔軟性を活用し、仮想コアの数、[ハードウェアの世代](service-tiers-vcore.md#hardware-generations)、[サービス](service-tiers-vcore.md#service-tiers)および[コンピューティング](service-tiers-vcore.md#compute-tiers) レベルを調整し、また、[並列処理の最大限度](/sql/relational-databases/query-processing-architecture-guide#parallel-query-processing)など、データベース構成のその他のパラメーターを調整する必要がある場合があります。
> 

### <a name="dtu-to-vcore-migration-examples"></a>DTU から仮想コアへの移行例

> [!NOTE]
> 次の例の値は、説明のみを目的としています。 説明されているシナリオで返される実際の値は異なる場合があります。
> 

**Standard S9 データベースの移行**

マッピング クエリでは次の結果が返されます (簡潔にするために一部の列は表示されていません)。

|dtu_logical_cpus|dtu_hardware_gen|dtu_memory_per_core_gb|Gen4_vcores|Gen4_memory_per_core_gb|Gen5_vcores|Gen5_memory_per_core_gb|
|----------------|----------------|----------------------|-----------|-----------------------|-----------|-----------------------|
|24.00|第 5 世代|5.40|16.800|7|24.000|5.05|

DTU データベースには 24 個の論理 CPU (仮想コア) があり、仮想コアあたり 5.4 GB のメモリが備わっており、Gen5 ハードウェアが使用されていることがわかります。 その直接一致は、Gen5 ハードウェア上の General Purpose の 24 個の仮想コア データベース、つまり、**GP_Gen5_24** 仮想コア サービス目標です。

**Standard S0 データベースの移行**

マッピング クエリでは次の結果が返されます (簡潔にするために一部の列は表示されていません)。

|dtu_logical_cpus|dtu_hardware_gen|dtu_memory_per_core_gb|Gen4_vcores|Gen4_memory_per_core_gb|Gen5_vcores|Gen5_memory_per_core_gb|
|----------------|----------------|----------------------|-----------|-----------------------|-----------|-----------------------|
|0.25|Gen4|0.42|0.250|7|0.425|5.05|

DTU データベースには 0.25 個に相当する論理 CPU (仮想コア) があり、仮想コアあたり 0.42 GB のメモリが備わっており、Gen4 ハードウェアが使用されていることがわかります。 Gen4 および Gen5 ハードウェア世代の最小の仮想コア サービス目標 (**GP_Gen4_1** および **GP_Gen5_2**) では、Standard S0 データベースよりも多くのコンピューティング リソースが提供されるため、直接一致させることはできません。 Gen4 ハードウェアは [使用停止](https://azure.microsoft.com/updates/gen-4-hardware-on-azure-sql-database-approaching-end-of-life-in-2020/)になるため、**GP_Gen5_2** オプションをお勧めします。 さらに、ワークロードが [サーバーレス](serverless-tier-overview.md) コンピューティング レベルに最適である場合は、**GP_S_Gen5_1** がより近い一致になります。

**Premium P15 データベースの移行**

マッピング クエリでは次の結果が返されます (簡潔にするために一部の列は表示されていません)。

|dtu_logical_cpus|dtu_hardware_gen|dtu_memory_per_core_gb|Gen4_vcores|Gen4_memory_per_core_gb|Gen5_vcores|Gen5_memory_per_core_gb|
|----------------|----------------|----------------------|-----------|-----------------------|-----------|-----------------------|
|42.00|第 5 世代|4.86|29.400|7|42.000|5.05|

DTU データベースには 42 個の論理 CPU (仮想コア) があり、仮想コアあたり 4.86 GB のメモリが備わっており、Gen5 ハードウェアが使用されていることがわかります。 42 コアの仮想コア サービス目標はありませんが、**BC_Gen5_40** サービス目標は、CPU とメモリ容量の両方において非常に近いものであり、適切な一致となります。

**Basic 200 eDTU エラスティック プールの移行**

マッピング クエリでは次の結果が返されます (簡潔にするために一部の列は表示されていません)。

|dtu_logical_cpus|dtu_hardware_gen|dtu_memory_per_core_gb|Gen4_vcores|Gen4_memory_per_core_gb|Gen5_vcores|Gen5_memory_per_core_gb|
|----------------|----------------|----------------------|-----------|-----------------------|-----------|-----------------------|
|4.00|第 5 世代|5.40|2.800|7|4.000|5.05|

DTU エラスティック プールには 4 個の論理 CPU (仮想コア) があり、仮想コアあたり 5.4 GB のメモリが備わっており、Gen5 ハードウェアが使用されていることがわかります。 仮想コア モデルでの直接一致は、**GP_Gen5_4** エラスティック プールです。 しかし、このサービス目標ではプールあたり最大 200 個のデータベースがサポートされますが、Basic 200 eDTU エラスティック プールでは 500 個までのデータベースがサポートされます。 移行されるエラスティック プールに 200 個を超えるデータベースがある場合、一致する仮想コア サービスの目標は **GP_Gen5_6** である必要があります。これにより、500 個までのデータベースがサポートされます。

## <a name="migrate-geo-replicated-databases"></a>geo レプリケーション対応データベースを移行する

DTU ベースのモデルから仮想コアベースの購入モデルへの移行は、Standard および Premium サービス レベルでのデータベース間の geo レプリケーションのリレーションシップのアップグレードまたはダウングレードに似ています。 移行中に geo レプリケーションを停止する必要はありませんが、次のシーケンス処理のルールに従う必要があります。

- アップグレードの場合は、最初にセカンダリ データベースをアップグレードしてから、プライマリをアップグレードする必要があります。
- ダウングレードは逆の順序で行います。つまり、最初にプライマリ データベースをダウングレードしてから、セカンダリをダウングレードします。

2 つのエラスティック プール間で geo レプリケーションを使用している場合は、1 つのプールをプライマリとして、もう一方をセカンダリとして指定することをお勧めします。 その場合、エラスティック プールを移行するときは、同じシーケンス処理のガイダンスを使用する必要があります。 ただし、プライマリ データベースとセカンダリ データベースの両方を含むエラスティック プールがある場合は、使用率が高い方のプールをプライマリとして扱い、それに応じてシーケンス処理のルールに従ってください。  

次の表は、特定の移行シナリオのためのガイダンスを示しています。

|現在のサービス レベル|移行先のサービス レベル|移行の種類|ユーザー操作|
|---|---|---|---|
|Standard|汎用|ラテラル|任意の順序で移行できますが、前述のように適切な仮想コア サイズを確保する必要があります|
|Premium|Business Critical|ラテラル|任意の順序で移行できますが、前述のように適切な仮想コア サイズを確保する必要があります|
|Standard|Business Critical|アップグレード|セカンダリを最初に移行する必要があります|
|Business Critical|Standard|ダウングレード|プライマリを最初に移行する必要があります|
|Premium|汎用|ダウングレード|プライマリを最初に移行する必要があります|
|汎用|Premium|アップグレード|セカンダリを最初に移行する必要があります|
|Business Critical|汎用|ダウングレード|プライマリを最初に移行する必要があります|
|汎用|Business Critical|アップグレード|セカンダリを最初に移行する必要があります|
||||

## <a name="migrate-failover-groups"></a>フェールオーバー グループを移行する

複数のデータベースが含まれるフェールオーバー グループの移行では、プライマリ データベースとセカンダリ データベースを個別に移行する必要があります。 その処理中は、同じ考慮事項とシーケンス処理ルールが適用されます。 データベースが仮想コアベースの購入モデルに変換された後も、フェールオーバー グループでは同じポリシー設定が引き続き有効です。

### <a name="create-a-geo-replication-secondary-database"></a>geo レプリケーションのセカンダリ データベースを作成する

geo レプリケーションのセカンダリ データベース (geo セカンダリ) は、プライマリ データベースに対して使用したのと同じサービス レベルを使用してのみ作成できます。 ログ生成速度が速いデータベースの場合は、プライマリと同じコンピューティング サイズを持つ geo セカンダリを作成することをお勧めします。

geo セカンダリを単一プライマリ データベースのエラスティック プール内に作成している場合は、そのプールの `maxVCore` 設定がプライマリ データベースのコンピューティング サイズに一致していることを確認してください。 プライマリの geo セカンダリを別のエラスティック プール内に作成している場合は、そのプールに同じ `maxVCore` 設定を割り当てることをお勧めします。

## <a name="use-database-copy-to-migrate-from-dtu-to-vcore"></a>データベース コピーを使用して DTU から仮想コアに移行する

DTU ベース コンピューティング サイズのデータベースを、仮想コアベース コンピューティング サイズのデータベースにコピーする場合、コピー先のコンピューティング サイズが、コピー元データベースの最大データベース サイズをサポートしている限り、制限や特別なシーケンス処理は伴いません。 データベースのコピーでは、コピー操作が開始された時点のデータのスナップショットが作成され、ソースとターゲットの間でデータは同期されません。

## <a name="next-steps"></a>次のステップ

- 単一データベースに対して使用できる特定のコンピューティング サイズとストレージ サイズの選択については、[単一データベースに対する SQL Database の仮想コア ベースのリソース制限](resource-limits-vcore-single-databases.md)に関するページを参照してください。
- エラスティック プールに対して使用できる特定のコンピューティング サイズとストレージ サイズの選択については、[エラスティック プールに対する SQL Database の仮想コア ベースのリソース制限](resource-limits-vcore-elastic-pools.md)に関するページを参照してください。