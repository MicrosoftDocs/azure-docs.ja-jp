---
title: "Transact-SQL を使用して Azure SQL Database の geo レプリケーションを構成する | Microsoft Docs"
description: "Transact-SQL を使用した Azure SQL Database の geo レプリケーションの構成"
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: d94d89a6-3234-46c5-8279-5eb8daad10ac
ms.service: sql-database
ms.custom: business continuity
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 10/13/2016
ms.author: carlrab
translationtype: Human Translation
ms.sourcegitcommit: 8d988aa55d053d28adcf29aeca749a7b18d56ed4
ms.openlocfilehash: 07593e7f1d92a9a5943714f662568fec10a8886a
ms.lasthandoff: 02/16/2017


---
# <a name="configure-active-geo-replication-for-azure-sql-database-with-transact-sql"></a>Transact-SQL を使用して Azure SQL Database のアクティブ geo レプリケーションを構成する

この記事では、Transact-SQL を使用して Azure SQL Database のアクティブ geo レプリケーションを構成する方法について説明します。

Transact-SQL を使用してフェールオーバーを開始するには、「 [Transact-SQL を使用した Azure SQL Database の計画されたフェールオーバーまたは計画されていないフェールオーバーの開始](sql-database-geo-replication-failover-transact-sql.md)」を参照してください。

> [!NOTE]
> すべてのサービス レベルのすべてのデータベースでアクティブ geo レプリケーション (読み取り可能なセカンダリ) を使用できるようになりました。 2017 年 4 月に、読み取り不能なタイプのセカンダリが廃止され、既存の読み取り不能なデータベースは読み取り可能なセカンダリに自動的にアップグレードされます。
> 
> 

Transact-SQL を使用してアクティブ geo レプリケーションを構成するには、次のものが必要です。

* Azure サブスクリプション。
* Azure SQL Database 論理サーバー <MyLocalServer> と SQL Database <MyDB> - レプリケートするプライマリ データベースです。
* 1 つ以上の Azure SQL Database 論理サーバー <MySecondaryServer(n)> - セカンダリ データベースの作成先であるパートナー サーバーとなる論理サーバーです。
* プライマリ上の DBManager であるログイン。geo レプリケートするローカル データベースの db_ownership を所有し、geo レプリケーションを構成するパートナー サーバー上の DBManager になります。
* SQL Server Management Studio (SSMS)

> [!IMPORTANT]
> 常に最新バージョンの Management Studio を使用して、Microsoft Azure と SQL Database の更新プログラムとの同期を維持することをお勧めします。 [SQL Server Management Studio を更新します](https://msdn.microsoft.com/library/mt238290.aspx)。
> 
> 

## <a name="add-secondary-database"></a>セカンダリ データベースの追加
**ALTER DATABASE** ステートメントを使用して、geo レプリケートされたセカンダリ データベースをパートナー サーバー上に作成できます。 このステートメントは、レプリケートされるデータベースが含まれているサーバーの master データベースに対して実行します。 geo レプリケートされたデータベース ("プライマリ データベース") は、レプリケートされているデータベースと同じ名前になります。また、既定では、サービス レベルがプライマリ データベースと同じになります。 セカンダリ データベースは、読み取り可能または読み取り不可とすることができるほか、単一データベースとして、またはエラスティック プールに作成することができます。 詳細については、[ALTER DATABASE (Transact-SQL)](https://msdn.microsoft.com/library/mt574871.aspx) に関するページと[サービス階層](sql-database-service-tiers.md)に関するページをご覧ください。
セカンダリ データベースが作成され、シード処理が行われると、データはプライマリ データベースから非同期にレプリケートを開始します。 以下の手順では、Management Studio を使用して geo レプリケーションを構成する方法について説明します。 単一データベースとして、またはエラスティック プールで、読み取り不可のセカンダリと読み取り可能なセカンダリを作成する手順について説明します。

> [!NOTE]
> 指定したパートナー サーバーにプライマリ データベースと同じ名前のデータベースが存在する場合、コマンドは失敗します。
> 
> 

### <a name="add-non-readable-secondary-single-database"></a>読み取り不可のセカンダリ (単一データベース) の追加
読み取り不可のセカンダリを単一データベースとして作成するには、次の手順に従います。

1. バージョン 13.0.600.65 以降の SQL Server Management Studio を使用します。
   
   > [!IMPORTANT]
   > [最新](https://msdn.microsoft.com/library/mt238290.aspx) バージョンの SQL Server Management Studio をダウンロードします。 常に最新バージョンの Management Studio を使用して、Azure ポータルの更新プログラムとの同期を維持することをお勧めします。
   > 
   > 
2. [データベース] フォルダーを開き、**[システム データベース]** フォルダーを展開し、**[master]** を右クリックして、**[新しいクエリ]** をクリックします。
3. 次の **ALTER DATABASE** ステートメントを使用して、ローカル データベースを、MySecondaryServer1 上に読み取り不可のセカンダリ データベースを持つ geo レプリケーション プライマリにします (MySecondaryServer1 は、サーバーのフレンドリ名です)。
   
        ALTER DATABASE <MyDB>
           ADD SECONDARY ON SERVER <MySecondaryServer1> WITH (ALLOW_CONNECTIONS = NO);
4. **[実行]** をクリックしてクエリを実行します。

### <a name="add-readable-secondary-single-database"></a>読み取り可能なセカンダリ (単一データベース) の追加
読み取り可能なセカンダリを単一データベースとして作成するには、次の手順に従います。

1. Management Studio で Azure SQL Database 論理サーバーに接続します。
2. [データベース] フォルダーを開き、**[システム データベース]** フォルダーを展開し、**[master]** を右クリックして、**[新しいクエリ]** をクリックします。
3. 次の **ALTER DATABASE** ステートメントを使用して、ローカル データベースを、セカンダリ サーバー上に読み取り可能なセカンダリ データベースを持つ geo レプリケーション プライマリにします。
   
        ALTER DATABASE <MyDB>
           ADD SECONDARY ON SERVER <MySecondaryServer2> WITH (ALLOW_CONNECTIONS = ALL);
4. **[実行]** をクリックしてクエリを実行します。

### <a name="add-non-readable-secondary-elastic-pool"></a>読み取り不可のセカンダリ (エラスティック プール) の追加
読み取り不可のセカンダリをエラスティック プールに作成するには、次の手順に従います。

1. Management Studio で Azure SQL Database 論理サーバーに接続します。
2. [データベース] フォルダーを開き、**[システム データベース]** フォルダーを展開し、**[master]** を右クリックして、**[新しいクエリ]** をクリックします。
3. 次の **ALTER DATABASE** ステートメントを使用して、ローカル データベースを、セカンダリ サーバー上のエラスティック プール内に読み取り不可のセカンダリ データベースを持つ geo レプリケーション プライマリにします。
   
        ALTER DATABASE <MyDB>
           ADD SECONDARY ON SERVER <MySecondaryServer3> WITH (ALLOW_CONNECTIONS = NO
           , SERVICE_OBJECTIVE = ELASTIC_POOL (name = MyElasticPool1));
4. **[実行]** をクリックしてクエリを実行します。

### <a name="add-readable-secondary-elastic-pool"></a>読み取り可能なセカンダリ (エラスティック プール) の追加
読み取り可能なセカンダリをエラスティック プールに作成するには、次の手順に従います。

1. Management Studio で Azure SQL Database 論理サーバーに接続します。
2. [データベース] フォルダーを開き、**[システム データベース]** フォルダーを展開し、**[master]** を右クリックして、**[新しいクエリ]** をクリックします。
3. 次の **ALTER DATABASE** ステートメントを使用して、ローカル データベースを、セカンダリ サーバー上のエラスティック プール内に読み取り可能なセカンダリ データベースを持つ geo レプリケーション プライマリにします。
   
        ALTER DATABASE <MyDB>
           ADD SECONDARY ON SERVER <MySecondaryServer4> WITH (ALLOW_CONNECTIONS = ALL
           , SERVICE_OBJECTIVE = ELASTIC_POOL (name = MyElasticPool2));
4. **[実行]** をクリックしてクエリを実行します。

## <a name="remove-secondary-database"></a>セカンダリ データベースを削除する
**ALTER DATABASE** を使用して、セカンダリ データベースとそのプライマリの間のレプリケーション パートナーシップを完全に終了させることができます。 このステートメントは、プライマリ データベースが存在する master データベースに対して実行されます。 リレーションシップの終了後、セカンダリ データベースは通常の読み取り/書き込みデータベースになります。 セカンダリ データベースへの接続が切断された場合、コマンドは成功します。ただし、接続が復元されると、セカンダリ データベースは読み取り/書き込みデータベースになります。 詳細については、[ALTER DATABASE (Transact-SQL)](https://msdn.microsoft.com/library/mt574871.aspx) に関するページと[サービス階層](sql-database-service-tiers.md)に関するページをご覧ください。

geo レプリケートされたセカンダリを geo レプリケーション パートナーシップから削除するには、次の手順に従います。

1. Management Studio で Azure SQL Database 論理サーバーに接続します。
2. [データベース] フォルダーを開き、**[システム データベース]** フォルダーを展開し、**[master]** を右クリックして、**[新しいクエリ]** をクリックします。
3. 次の **ALTER DATABASE** ステートメントを使用して、geo レプリケートされたセカンダリを削除します。
   
        ALTER DATABASE <MyDB>
           REMOVE SECONDARY ON SERVER <MySecondaryServer1>;
4. **[実行]** をクリックしてクエリを実行します。

## <a name="monitor-active-geo-replication-configuration-and-health"></a>アクティブ geo レプリケーションの構成と正常性を監視する

監視タスクには、geo レプリケーションの構成に関する監視と、データ レプリケーションの正常性に関する監視が含まれます。  master データベースの **sys.dm_geo_replication_links** 動的管理ビューを使用すると、Azure SQL Database 論理サーバー上の各データベースについて、既存のレプリケーション リンクすべてに関する情報が返されます。 このビューでは、プライマリ データベースとセカンダリ データベースの間の各レプリケーション リンクについて&1; 行表示されます。 **sys.dm_replication_link_status** 動的管理ビューを使用すると、レプリケーション リンクに現在関係している Azure SQL Database ごとに行が返されます。 これには、プライマリ データベースとセカンダリ データベースの両方が含まれます。 特定のプライマリ データベースについて複数の連続レプリケーション リンクが存在する場合、このテーブルには、各リレーションシップについて&1; 行が含まれます。 このビューは、すべてのデータベース (論理 master データベースを含む) で作成されます。 ただし、論理 master データベースでこのビューにクエリを実行しても、空のセットが返されます。 **sys.dm_operation_status** 動的管理ビューを使用すると、レプリケーション リンクの状態など、すべてのデータベース操作の状態を表示できます。 詳細については、[sys.geo_replication_links (Azure SQL Database)](https://msdn.microsoft.com/library/mt575501.aspx)、[sys.dm_geo_replication_link_status (Azure SQL Database)](https://msdn.microsoft.com/library/mt575504.aspx)、[sys.dm_operation_status (Azure SQL Database)](https://msdn.microsoft.com/library/dn270022.aspx) に関するページをご覧ください。

アクティブ geo レプリケーション パートナーシップを監視するには、次の手順に従います。

1. Management Studio で Azure SQL Database 論理サーバーに接続します。
2. [データベース] フォルダーを開き、**[システム データベース]** フォルダーを展開し、**[master]** を右クリックして、**[新しいクエリ]** をクリックします。
3. 次のステートメントを使用して、geo レプリケーション リンクと共にすべてのデータベースを表示します。
   
        SELECT database_id, start_date, modify_date, partner_server, partner_database, replication_state_desc, role, secondary_allow_connections_desc FROM [sys].geo_replication_links;
4. **[実行]** をクリックしてクエリを実行します。
5. [データベース] フォルダーを開き、**[システム データベース]** フォルダーを展開し、**[MyDB]** を右クリックして、**[新しいクエリ]** をクリックします。
6. 次のステートメントを使用して、MyDB のセカンダリ データベースのレプリケーション ラグと前回のレプリケーション時刻を表示します。
   
        SELECT link_guid, partner_server, last_replication, replication_lag_sec FROM sys.dm_geo_replication_link_status
7. **[実行]** をクリックしてクエリを実行します。
8. 次のステートメントを使用して、MyDB データベースに関連付けられた最近の geo レプリケーション操作を表示します。
   
        SELECT * FROM sys.dm_operation_status where major_resource_id = 'MyDB'
        ORDER BY start_time DESC
9. **[実行]** をクリックしてクエリを実行します。

## <a name="upgrade-a-non-readable-secondary-to-readable"></a>読み取り可能なセカンダリへの読み取り不能なセカンダリのアップグレード
2017 年 4 月に、読み取り不能なタイプのセカンダリが廃止され、既存の読み取り不能なデータベースは読み取り可能なセカンダリに自動的にアップグレードされます。 読み取り不能なセカンダリを使用している場合、読み取り可能なセカンダリにアップグレードするには、各セカンダリで次の簡単な手順を実行します。

> [!IMPORTANT]
> 読み取り不能なセカンダリを読み取り可能なセカンダリにアップグレードする、インプレース アップグレードのセルフ サービスは用意されていません。 セカンダリだけを削除した場合、新しいセカンダリが完全に同期されるまで、プライマリ データベースは保護されていない状態で残ります。 アプリケーションの SLA によりプライマリを常に保護することが求められている場合は、上記のアップグレード手順を適用する前に、別のサーバーで並列セカンダリを作成することを検討してください。 各プライマリで使用できるセカンダリ データベースの数は最大 4 つです。
> 
> 

1. 最初に、"セカンダリ" サーバーに接続し、読み取り不能なセカンダリ データベースを削除します。**  
   
        DROP DATABASE <MyNonReadableSecondaryDB>;
2. これで、"プライマリ" サーバーに接続して、新しい読み取り可能なセカンダリを追加できます**
   
        ALTER DATABASE <MyDB>
            ADD SECONDARY ON SERVER <MySecondaryServer> WITH (ALLOW_CONNECTIONS = ALL);

## <a name="next-steps"></a>次のステップ
* アクティブ geo レプリケーションの詳細については、[アクティブ geo レプリケーション](sql-database-geo-replication-overview.md)に関する記事を参照してください。
* ビジネス継続性の概要およびシナリオについては、 [ビジネス継続性の概要](sql-database-business-continuity.md)


