---
title: "SQL Database のパフォーマンス: サービス レベル | Microsoft Docs"
description: "SQL Database のサービス レベルを比較します。"
keywords: "データベース オプション, データベース パフォーマンス"
services: sql-database
documentationcenter: 
author: janeng
manager: jhubbard
editor: 
ms.assetid: f5c5c596-cd1e-451f-92a7-b70d4916e974
ms.service: sql-database
ms.custom: overview
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-management
wms.date: 03/06/2017
ms.author: janeng
translationtype: Human Translation
ms.sourcegitcommit: 07635b0eb4650f0c30898ea1600697dacb33477c
ms.openlocfilehash: ab637f9910523cc8d8967dd1507dbcfad9f7ae88
ms.lasthandoff: 03/28/2017


---
# <a name="sql-database-options-and-performance-understand-whats-available-in-each-service-tier"></a>SQL Database のオプションとパフォーマンス: 各サービス レベルで使用できる内容について理解する

[Azure SQL Database](sql-database-technical-overview.md) には、**Basic**、**Standard**、**Premium**、**Premium RS** の 4 つのサービス レベルが用意されています。 各サービス レベルには、さまざまなワークロードに対応するために、いくつかのパフォーマンス レベルが用意されています。 パフォーマンス レベルが高くなるほど提供されるリソースが追加され、スループットが段階的に高くなるように設計されています。 ダウンタイムを発生させることなく、サービス レベルとパフォーマンス レベルは動的に変更できます。 Basic、Standard、Premium のサービス レベルは、いずれも稼働時間の SLA が 99.99% で、柔軟なビジネス継続性のオプション、セキュリティ機能、時間単位の課金体系が用意されています。 Premium RS レベルでは、SLA が少ないにも関わらず、Premium レベルと同じレベルのパフォーマンス、セキュリティ機能およびビジネス継続性機能が用意されています。

> [!IMPORTANT]
> Premium RS のデータベースは、Premium または Standard の各データベースよりも少ない冗長コピーで実行されます。 そのため、サービスで障害が発生した場合は、5 分以内にバックアップからデータベースを復旧する必要があります。
>

あるサービス レベルの特定の[パフォーマンス レベル](sql-database-service-tiers.md#single-database-service-tiers-and-performance-levels)で、専用リソースを含む単一データベースを作成できます。 また、リソースが複数のデータベース間で共有される[エラスティック プール](sql-database-service-tiers.md#elastic-pool-service-tiers-and-performance-in-edtus)内にデータベースを作成することもできます。 単一データベースで利用できるリソースは、データベース トランザクション ユニット (DTU) を単位として表現されます。これに対して、エラスティック プールで利用できるリソースは、エラスティック データベース トランザクション ユニット (eDTU) を単位として表現されます。 DTU と eDTU の詳細については、[DTU の概要](sql-database-what-is-a-dtu.md)に関する記事を参照してください。 

## <a name="choosing-a-service-tier"></a>サービス階層の選択
次の表では、各種のアプリケーション ワークロードに最適なサービス階層の例を示します。

| サービス階層 | 対象のワークロード |
| :--- | --- |
| **Basic** | 小規模なデータベースに最適です。通常は、一度に 1 つのアクティブな操作をサポートします。 たとえば、開発やテスト、使用頻度の低い小規模なアプリケーションなどに使用するデータベースがこれに該当します。 |
| **Standard** |IO パフォーマンス要件が低～中程度のクラウド アプリケーションに適しています。複数の同時クエリをサポートします。 たとえば、ワークグループや Web アプリケーションなどです。 |
| **Premium** | IO パフォーマンス要件が高く、トランザクション量が膨大な場合に合わせて設計されています。多数の同時ユーザーをサポートしています。 たとえば、ミッション クリティカルなアプリケーションをサポートするデータベースなどです。 |
| **Premium RS** | 最高レベルの可用性の保証を必要としない I/O 集中型のワークロード向けに設計されています。 例としては、高パフォーマンス ワークロードまたはデータベースがレコードのシステムではない分析ワークロードのテストが含まれます。 |
|||

まず、定義済みの容量を備えた専用リソースを持つ単一データベースを実行するか、データベースのグループ間でリソースのプールを共有するかを決定します。 [エラスティック プールの考慮事項](sql-database-elastic-pool-guidance.md)を確認してください。 サービス レベルを決定するには、まず、最低限必要なデータベース機能を確認します。

| **サービス レベルの機能** | **Basic** | **Standard** | **Premium** | **Premium RS**|
| :-- | --: | --: | --: | --: |
| 各データベースの最大サイズ | 2 GB | 250 GB | 4 TB*  | 500 GB  |
| エラスティック プール内の最大合計ストレージ | 117 GB | 1200 GB | 750 GB | 750 GB |
| プールあたりのデータベースの最大数 | 400  | 400 | 50 | 50 |
| データベース バックアップのリテンション期間 | 7 日 | 35 日 | 35 日 | 35 日 |
||||||

> [!IMPORTANT]
> P11 と P15 のパフォーマンス レベルを使用しているお客様は、追加料金なしで最大 4 TB の付属のストレージを使用できます。 この 4 TB のオプションは現在、米国東部 2、米国西部、西ヨーロッパ、東南アジア、東日本、オーストラリア東部、カナダ中部、カナダ東部の各リージョンで、パプリック プレビューの段階にあります。 現在の制限については、[現在の 4 TB の制限事項](sql-database-service-tiers.md#current-limitations-of-p11-and-p15-databases-with-4-tb-maxsize)に関するトピックを参照してください。
>

最小限のサービス レベルを決定したら、データベースのパフォーマンス レベル (DTU 数) を決定できるようになります。 多くの場合、出発点として Standard S2 および S3 というパフォーマンス レベルが適しています。 CPU または IO の要件が高いデータベースの場合は、Premium のパフォーマンス レベルが出発点として適しています。 Premium では、より多くの CPU が提供されるため、最も高い Standard パフォーマンス レベルと比較して 10 倍超える IO から始まります。

## <a name="single-database-service-tiers-and-performance-levels"></a>Single Database サービス階層とパフォーマンス レベル
単一データベースでは、各サービス レベル内に複数のパフォーマンス レベルがあります。 [Azure Portal](sql-database-manage-single-databases-portal.md)、[PowerShell](scripts/sql-database-monitor-and-scale-database-powershell.md)、[Transact-SQL](sql-database-manage-single-databases-tsql.md)、C#、および REST API を使用して、ワークロードの需要に最も合ったレベルを選択できる柔軟性があります。 

ホストされるデータベースの数にかかわらず、データベースは所定のリソースを取得します。データベースの期待されるパフォーマンス特性には影響しません。

[!INCLUDE [SQL DB service tiers table](../../includes/sql-database-service-tiers-table.md)]

> [!NOTE]
> このサービス層テーブルのその他すべての行の詳細については、「 [サービス層の機能と制限](sql-database-performance-guidance.md#service-tier-capabilities-and-limits)」を参照してください。
> 

## <a name="scaling-up-or-scaling-down-a-single-database"></a>1 つのデータベースのスケールアップとスケールダウン

最初にサービス レベルとパフォーマンス レベルを選んだ後、実際の使用感に基づいて、1 つのデータベースを動的にスケールアップまたはスケールダウンすることができます。 スケールアップまたはスケールダウンする必要がある場合は、[Azure Portal](sql-database-manage-single-databases-portal.md)、[PowerShell](scripts/sql-database-monitor-and-scale-database-powershell.md)、[Transact-SQL](sql-database-manage-single-databases-tsql.md)、C#、および REST API を使用してデータベースの階層を簡単に変更することができます。 

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Azure-SQL-Database-dynamically-scale-up-or-scale-down/player]
>

データベースのサービス レベルやパフォーマンス レベルを変更すると、新しいパフォーマンス レベルで元のデータベースのレプリカが作成され、接続先がそのレプリカに切り替えられます。 このプロセスでデータが失われることはありませんが、レプリカに切り替えるほんの少しの間、データベースに接続できなくなるため、実行中の一部トランザクションがロールバックされる場合があります。 この時間はさまざまですが、平均 4 秒以内であり、99% 以上が 30 秒未満です。 接続が無効になった時点で多数のトランザクションが実行中の場合、この時間が長引くことがあります。  

スケールアップ プロセス全体の継続時間は、変更前後のデータベースのサイズとサービス レベルによって異なります。 たとえば、250 GB のデータベースを Standard サービス レベルとの間または Standard サービス レベル内で変更する場合は、6 時間以内に完了します。 Premium サービス レベル内で同じサイズのデータベースのパフォーマンス レベルを変更する場合は、3 時間以内で完了します。

* データベースをダウングレードするには、データベースがダウングレード後のサービス階層で許可されている最大サイズより小さい必要があります。 
* [geo レプリケーション](sql-database-geo-replication-portal.md) を有効にしてデータベースをアップグレードする場合、まず、そのセカンダリ データベースを目的のパフォーマンス レベルにアップグレードしてから、プライマリ データベースをアップグレードする必要があります。
* Premium サービス階層からダウングレードするときは、最初に geo レプリケーション リレーションシップをすべて終了する必要があります。 「 [Azue SQL Database を障害から回復する](sql-database-disaster-recovery.md) 」に記載されている手順に従って、プライマリ データベースとアクティブなセカンダリ データベース間のレプリケーション プロセスを停止できます。
* サービス階層によって、提供されている復元サービスは異なります。 ダウングレードすると、特定の時点に復元する機能を使えなくなったり、バックアップの保存期間が短くなったりする可能性があります。 詳細については、「 [Azure SQL Database のバックアップと復元](sql-database-business-continuity.md)」を参照してください。
* データベースに対する新しいプロパティは、変更が完了するまで適用されません。

> [!IMPORTANT]
> 詳細な手順については、[Azure Portal での単一データベースの管理](sql-database-manage-single-databases-portal.md)、[PowerShell を使用した単一データベースの管理](scripts/sql-database-monitor-and-scale-database-powershell.md)、[Transact-SQL を使用した単一のデータベースの管理](sql-database-manage-single-databases-tsql.md)に関する記事をそれぞれ参照してください。
>

## <a name="elastic-pool-service-tiers-and-performance-in-edtus"></a>エラスティック プール サービス階層と eDTU のパフォーマンス

プールでは、データベースが eDTU リソースを共有して使用することができ、プール内の各データベースに特定のパフォーマンス レベルを割り当てる必要はありません。 たとえば、Standard プール内の単一データベースで使用できるのは、0 eDTU から、プールの構成時に設定した最大データベース eDTU までとなります。 プールでは、多様なワークロードを持つ複数のデータベースが、プール全体で利用可能な eDTU リソースを効率的に使用できます。 詳細については、 [エラスティック プールの価格およびパフォーマンスに関する考慮事項](sql-database-elastic-pool-guidance.md) に関する記事を参照してください。

次の表では、プールのサービス階層の特性について説明します。

[!INCLUDE [SQL DB service tiers table for elastic pools](../../includes/sql-database-service-tiers-table-elastic-pools.md)]

プール内の各データベースは、該当するレベルにおける単一データベースの特性にも従います。 たとえば、Basic プールでは、プールあたりの最大セッション数が 4,800 ～ 28,800 の範囲に制限されます。ただし、Basic プール内の個々のデータベースでは、データベースあたりのセッション数が 300 に制限されます。

## <a name="scaling-up-or-scaling-down-an-elastic-pool"></a>エラスティック プールのスケールアップとスケールダウン

最初にサービス レベルとパフォーマンス レベルを選んだ後、実際の使用感に基づいて、エラスティック プールを動的にスケールアップまたはスケールダウンすることができます。 

* 通常、データベースあたりの最小 eDTU またはデータベースあたりの最大 eDTU の変更は、5 分以内で完了します。
* プール サイズ (eDTU) の変更時間は、プール内のすべてのデータベースを結合したサイズに依存します。 変更の平均時間は、100 GB あたり 90 分以下です。 たとえば、プール内のすべてのデータベースの合計領域が 200 GB の場合、プールあたりの eDTU の変更にかかる想定待機時間は、3 時間以下になります。

詳細な手順については、[Azure Portal でのエラスティック プールの管理](sql-database-elastic-pool-manage-portal.md)、[PowerShell を使用したエラスティック プールの管理](scripts/sql-database-monitor-and-scale-pool-powershell.md)、[Transact-SQL を使用したエラスティック プールの管理](sql-database-elastic-pool-manage-tsql.md)、または [C# を使用したエラスティック プールの管理](sql-database-elastic-pool-manage-csharp.md)に関する記事をそれぞれ参照してください。

## <a name="creating-or-upgrading-to-4tb"></a>4 TB の作成またはアップグレード

以下のセクションでは、4 TB のオプションの実装について詳しく説明します。

### <a name="creating-in-the-azure-portal"></a>Azure Portal での作成

P11/P15 を作成するとき、既定で 1 TB のストレージ オプションがあらかじめ選択されています。 サポート対象のリージョンにあるデータベースでは、ストレージの最大値を 4 TB に増やすことができます。 それ以外のすべてのリージョンでは、ストレージのスライダーを変更できません。 4 TB の付属のストレージを選択しても、価格は変わりません。

### <a name="creating-using-powershell-or-transact-sql"></a>PowerShell または Transact-SQL を使用した作成

P11/P15 データベースを作成するときに、1 TB (既定値) または 4 TB を最大サイズの値として設定できます。 "1024 GB" と "4096 GB" の値も入力できます。 4 TB の最大サイズのオプションを選択した場合、サポートされていないリージョンでデータベースをプロビジョニングすると、エラーのため、create コマンドが失敗します。

### <a name="upgrading-to-4tb"></a>4 TB へのアップグレード 

サポート対象のリージョンにある既存の P11 と P15 のデータベースでは、ストレージの最大サイズを 4 TB に増やすことができます。 この操作は、Azure Portal、PowerShell、または Transact-SQL で実行できます。 次のコードは、ALTER DATABASE コマンドを使用した最大サイズの変更の例を示しています。

```ALTER DATABASE <DatabaseName> MODIFY (MAXSIZE = 4096 GB);
```

Upgrading an existing P11 or P15 database can only be performed by a server-level principal login or by members of the dbmanager database role. 
If executed in a supported region the configuration will be updated immediately. This can be checked using the [SELECT DATABASEPROPERTYEX](https://msdn.microsoft.com/library/ms186823.aspx) or by inspecting the database size in the Azure portal. The database will remain online during the upgrade process. However, you will not be able to utilize the full 4 TB of storage until the actual database files have been upgraded to the new maxsize. The length of time required depends upon on the size of the database being upgraded.  

### Error messages
When creating or upgrading an P11/P15 database in an unsupported region, the create or upgrade operation will fail with the following error message: **P11 and P15 database with up to 4TB of storage are available in US East 2, West US, South East Asia, West Europe, Canada East, Canada Central, Japan East, and Australia East.**

## Current limitations of P11 and P15 databases with 4 TB maxsize

- When creating or updating a P11 or P15 database, you can only chose between 1 TB and 4 TB maxsize. Intermediate storage sizes are not currently supported.
- The 4 TB database maxsize cannot be changed to 1 TB even if the actual storage used is below 1 TB. Thus, you cannot downgrade a P11-4TB/P15-4TB to a P11-1TB/P15-1TB or a lower performance tier (e.g., to P1-P6) until we are providing additional storage options for the rest of the performance tiers. This restriction also applies to the restore and copy scenarios including point-in-time, geo-restore, long-term-backup-retention, and database copy. Once a database is configured with the 4 TB option, all restore operations of this database must be into a P11/P15 with 4 TB maxsize.
- For Active Geo-Replication scenarios:
   - Setting up a geo-replication relationship: If the primary database is P11 or P15, the secondary(ies) must also be P11 or P15; lower performance tiers will be rejected as secondaries since they are not capable of supporting 4 TB.
   - Upgrading the primary database in a geo-replication relationship: Changing the maxsize to 4 TB on a primary database will trigger the same change on the secondary database. Both upgrades must be successful for the change on the primary to take effect. Region limitations for the 4TB option apply (see above). If the secondary is in a region that does not support 4 TB, the primary will not be upgraded.
- Using the Import/Export service for loading P11-4TB/P15-4TB databases is not supported. Use SqlPackage.exe to [import](sql-database-import-sqlpackage.md) and [export](sql-database-export-sqlpackage.md) data.

## Next steps

* Learn the details of [elastic pools](sql-database-elastic-pool-guidance.md) and [price and performance considerations for elastic pools](sql-database-elastic-pool-guidance.md).
* Learn how to [Monitor, manage, and resize elastic pools](sql-database-elastic-pool-manage-portal.md) and [Monitor the performance of single databases](sql-database-single-database-monitor.md).
* Now that you know about the SQL Database tiers, try them out with a [free account](https://azure.microsoft.com/pricing/free-trial/) and learn [how to create your first SQL database](sql-database-get-started.md).
* For migration scenarios, use the [DTU Calculator](http://dtucalculator.azurewebsites.net/) to approximate the number of DTUs needed. 


