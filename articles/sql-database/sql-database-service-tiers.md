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
ms.custom: resources
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-management
wms.date: 04/14/2017
ms.author: janeng
translationtype: Human Translation
ms.sourcegitcommit: abdbb9a43f6f01303844677d900d11d984150df0
ms.openlocfilehash: 884cd19bdfb1bf53d75cb27e840c448eff8bc991
ms.lasthandoff: 04/21/2017


---
# <a name="sql-database-options-and-performance-understand-whats-available-in-each-service-tier"></a>SQL Database のオプションとパフォーマンス: 各サービス レベルで使用できる内容について理解する

[Azure SQL Database](sql-database-technical-overview.md) には、**Basic**、**Standard**、**Premium**、**Premium RS** の 4 つのサービス レベルが用意されています。 各サービス レベルには、さまざまなワークロードに対応するために、いくつかのパフォーマンス レベルが用意されています。 パフォーマンス レベルが高くなるほど提供されるリソースが追加され、スループットが段階的に高くなるように設計されています。 ダウンタイムを発生させることなく、サービス レベルとパフォーマンス レベルは動的に変更できます。 Basic、Standard、Premium のサービス レベルは、いずれも稼働時間の SLA が 99.99% で、柔軟なビジネス継続性のオプション、セキュリティ機能、時間単位の課金体系が用意されています。 Premium RS レベルでは、SLA が少ないにも関わらず、Premium レベルと同じレベルのパフォーマンス、セキュリティ機能およびビジネス継続性機能が用意されています。

> [!IMPORTANT]
> Premium RS のデータベースは、Premium または Standard の各データベースよりも少ない冗長コピーで実行されます。 そのため、サービスで障害が発生した場合は、最大 5 分間の遅延があるバックアップからデータベースを復旧する必要があります。
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

まず、定義済みの容量を備えた専用リソースを持つ単一データベースを実行するか、データベースのグループ間でリソースのプールを共有するかを決定します。 [エラスティック プールの考慮事項](sql-database-elastic-pool.md)を確認してください。 サービス レベルを決定するには、まず、最低限必要なデータベース機能を確認します。

| **サービス レベルの機能** | **Basic** | **Standard** | **Premium** | **Premium RS**|
| :-- | --: | --: | --: | --: |
| 単一データベースの最大サイズ | 2 GB | 250 GB | 4 TB*  | 500 GB  |
| エラスティック プール内のデータベースの最大サイズ | 156 GB | 2.9 TB | 500 GB | 500 GB |
| プールあたりのデータベースの最大数 | 500  | 500 | 100 | 100 |
| データベース バックアップのリテンション期間 | 7 日 | 35 日 | 35 日 | 35 日 |
||||||

> [!IMPORTANT]
> 最大 4 TB のデータベースは、P11 と P15 のパフォーマンス レベルを使用しているお客様に追加料金なしで提供されるパブリック プレビューです。 750 GB 以上のストレージが付属した Premium プールも提供されています。 これらの追加ストレージ オプションは現在、米国東部 2、米国西部、西ヨーロッパ、東南アジア、東日本、オーストラリア東部、カナダ中部、カナダ東部の各リージョンで利用できます。 詳細については、[4 TB のデータベースの現時点での制限事項](sql-database-service-tiers.md#current-limitations-of-p11-and-p15-databases-with-4-tb-maxsize)に関するセクションを参照してください。
>

最小限のサービス レベルを決定したら、データベースのパフォーマンス レベル (DTU 数) を決定できるようになります。 多くの場合、出発点として Standard S2 および S3 というパフォーマンス レベルが適しています。 CPU または IO の要件が高いデータベースの場合は、Premium のパフォーマンス レベルが出発点として適しています。 Premium では、より多くの CPU が提供されるため、最も高い Standard パフォーマンス レベルと比較して 10 倍超える IO から始まります。

## <a name="single-database-service-tiers-and-performance-levels"></a>単一データベース サービス階層とパフォーマンス レベル
単一データベースでは、各サービス レベル内に複数のパフォーマンス レベルがあります。 Azure Portal、[PowerShell](scripts/sql-database-monitor-and-scale-database-powershell.md)、[Transact-SQL](https://docs.microsoft.com/sql/t-sql/statements/alter-database-azure-sql-database)、C#、および REST API を使用して、ワークロードの需要に最も合ったレベルを選択できる柔軟性があります。  

ホストされるデータベースの数にかかわらず、データベースは所定のリソースを取得します。データベースの期待されるパフォーマンス特性には影響しません。

[!INCLUDE [SQL DB service tiers table](../../includes/sql-database-service-tiers-table.md)]

> [!NOTE]
> このサービス層テーブルのその他すべての行の詳細については、「 [サービス層の機能と制限](sql-database-performance-guidance.md#service-tier-capabilities-and-limits)」を参照してください。
> 

## <a name="scaling-up-or-scaling-down-a-single-database"></a>単一データベースのスケールアップとスケールダウン

最初にサービス レベルとパフォーマンス レベルを選んだ後、実際の使用感に基づいて、単一データベースを動的にスケールアップまたはスケールダウンすることができます。 スケールアップまたはスケールダウンする必要がある場合は、[Azure Portal](sql-database-manage-single-databases-portal.md)、[PowerShell](sql-database-manage-single-databases-powershell.md)、[Transact-SQL](sql-database-manage-single-databases-tsql.md)、C#、および REST API を使用してデータベースの階層を簡単に変更することができます。  

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Azure-SQL-Database-dynamically-scale-up-or-scale-down/player]
>

データベースのサービス レベルやパフォーマンス レベルを変更すると、新しいパフォーマンス レベルで元のデータベースのレプリカが作成され、接続先がそのレプリカに切り替えられます。 このプロセスでデータが失われることはありませんが、レプリカに切り替えるほんの少しの間、データベースに接続できなくなるため、実行中の一部トランザクションがロールバックされる場合があります。 この時間はさまざまですが、平均 4 秒以内であり、99% 以上が 30 秒未満です。 接続が無効になった時点で多数のトランザクションが実行中の場合、この時間が長引くことがあります。  

スケールアップ プロセス全体の継続時間は、変更前後のデータベースのサイズとサービス レベルによって異なります。 たとえば、250 GB のデータベースを Standard サービス レベルとの間または Standard サービス レベル内で変更する場合は、6 時間以内に完了します。 Premium サービス レベル内で同じサイズのデータベースのパフォーマンス レベルを変更する場合は、3 時間以内で完了します。

* データベースをダウングレードするには、データベースがダウングレード後のサービス階層で許可されている最大サイズより小さい必要があります。 
* [geo レプリケーション](sql-database-geo-replication-portal.md) を有効にしてデータベースをアップグレードする場合、まず、そのセカンダリ データベースを目的のパフォーマンス レベルにアップグレードしてから、プライマリ データベースをアップグレードする必要があります。
* Premium サービス階層からダウングレードするときは、最初に geo レプリケーション リレーションシップをすべて終了する必要があります。 「 [Azue SQL Database を障害から回復する](sql-database-disaster-recovery.md) 」に記載されている手順に従って、プライマリ データベースとアクティブなセカンダリ データベース間のレプリケーション プロセスを停止できます。
* サービス階層によって、提供されている復元サービスは異なります。 ダウングレードすると、特定の時点に復元する機能を使えなくなったり、バックアップの保存期間が短くなったりする可能性があります。 詳細については、「 [Azure SQL Database のバックアップと復元](sql-database-business-continuity.md)」を参照してください。
* データベースに対する新しいプロパティは、変更が完了するまで適用されません。

## <a name="elastic-pool-service-tiers-and-performance-in-edtus"></a>エラスティック プール サービス階層と eDTU のパフォーマンス

プールでは、データベースが eDTU リソースを共有して使用することができ、プール内の各データベースに特定のパフォーマンス レベルを割り当てる必要はありません。 たとえば、Standard プール内の単一データベースで使用できるのは、0 eDTU から、プールの構成時に設定した最大データベース eDTU までとなります。 プールでは、多様なワークロードを持つ複数のデータベースが、プール全体で利用可能な eDTU リソースを効率的に使用できます。 詳細については、 [エラスティック プールの価格およびパフォーマンスに関する考慮事項](sql-database-elastic-pool.md) に関する記事を参照してください。

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

 ```sql
ALTER DATABASE <myDatabaseName> 
   MODIFY (MAXSIZE = 4096 GB);
```

既存の P11 または P15 のデータベースのアップグレードは、サーバーレベル プリンシパルのログインまたは dbmanager データベース ロールのメンバーのみが実行できます。 サポートされているリージョンで実行されると、すぐに構成が更新されます。 これを確認するには、[SELECT DATABASEPROPERTYEX](https://msdn.microsoft.com/library/ms186823.aspx) を使用するか、Azure Portal でデータベース サイズを調べます。 データベースは、アップグレード処理中もオンラインのままになります。 ただし、実際のデータベース ファイルが新しい最大サイズにアップグレードされるまで、4 TB のストレージを十分に利用することはできません。 必要な時間は、アップグレードされるデータベースのサイズに応じて異なります。  

### <a name="error-messages"></a>エラー メッセージ
サポートされていないリージョンで P11/P15 データベースを作成またはアップグレードすると、アップグレード操作が失敗し、"**P11 and P15 database with up to 4TB of storage are available in US East 2, West US, South East Asia, West Europe, Canada East, Canada Central, Japan East, and Australia East (最大 4 TB のストレージを使用する P11 および P15 データベースは、米国東部 2、米国西部、東南アジア、西ヨーロッパ、カナダ東部、カナダ中部、東日本、オーストラリア東部で利用可能です)**" というエラー メッセージが表示されます。

## <a name="current-limitations-of-p11-and-p15-databases-with-4-tb-maxsize"></a>最大サイズ 4 TB の P11 および P15 データベースの現時点での制限事項

- P11 または P15 データベースを作成または更新する際に、最大サイズとして 1 TB と 4 TB のいずれかのみを選択できます。 現在、中間のストレージ サイズはサポートされていません。
- 実際に使用されるストレージが 1 TB 未満の場合でも、データベースの最大サイズ 4 TB を 1 TB に変更することはできません。 したがって、他のパフォーマンス レベルに追加のストレージ オプションが提供されない限り、P11-4TB/P15-4TB を P11-1TB/P15-1TB またはさらに下位のパフォーマンス レベル (たとえば、P1-P6) にダウングレードすることはできません。 この制限は、ポイントインタイム リストア、geo リストア、長期的なバックアップ保有期間、データベース コピーなど、復元とコピーのシナリオにも適用されます。 データベースを 4 TB のオプションを使用して構成すると、このデータベースのすべての復元操作により、最大サイズ 4 TB の P11/P15 になります。
- アクティブ geo レプリケーションのシナリオの場合:
   - geo レプリケーションのリレーションシップの設定: プライマリ データベースが P11 または P15 の場合は、セカンダリも P11 または P15 である必要があります。つまり、下位のパフォーマンス レベルは、4 TB をサポートできないため、セカンダリとして拒否されます。
   - geo レプリケーションのリレーションシップでのプライマリ データベースのアップグレード: プライマリ データベースで最大サイズを 4 TB に変更すると、セカンダリ データベースでも同じ変更がトリガーされます。 プライマリに対する変更を有効にするには、両方のアップグレードが正常に完了する必要があります。 4 TB のオプションに関するリージョンの制限が適用されます (上記参照)。 4 TB をサポートしていないリージョンにセカンダリが存在する場合、プライマリはアップグレードされません。
- P11-4TB/P15-4TB データベースの読み込みに Import/Export サービスを使用することはサポートされていません。 SqlPackage.exe を使用して、データを[インポート](sql-database-import.md)および[エクスポート](sql-database-export.md)してください。

## <a name="next-steps"></a>次のステップ

* [エラスティック プール](sql-database-elastic-pool.md)と、[エラスティック プールの価格とパフォーマンスに関する考慮事項](sql-database-elastic-pool.md)の詳細を確認してください。
* [エラスティック プールの監視、管理、およびサイズ変更](sql-database-elastic-pool-manage-portal.md)と[単一データベースのパフォーマンスの監視](sql-database-single-database-monitor.md)を行う方法を確認してください。
* SQL Database の階層について理解できたら、[無料アカウント](https://azure.microsoft.com/pricing/free-trial/)で実際に試してみてください。また、[初めて SQL データベースを作成するにあたっての方法](sql-database-get-started-portal.md)を確認してください。
* 移行のシナリオの場合は、[DTU Calculator](http://dtucalculator.azurewebsites.net/) を使用して必要な DTU 数を見積もります。 


