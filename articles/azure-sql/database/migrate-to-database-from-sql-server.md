---
title: SQL Server データベースの Azure SQL Database への移行
description: SQL Server データベースの Azure SQL Database への移行について説明します。
keywords: データベースの移行, SQL Server データベースの移行, データベース移行ツール, データベースを移行する, SQL データベースを移行する
services: sql-database
ms.service: sql-database
ms.subservice: migration
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: how-to
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 02/11/2019
ms.openlocfilehash: fab52fcea03a2f65c868cfac27f8a8cef115b2be
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "94917649"
---
# <a name="sql-server-database-migration-to-azure-sql-database"></a>SQL Server データベースの Azure SQL Database への移行
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

この記事では、SQL Server 2005 以降のデータベースを Azure SQL Database に移行するための主な方法について説明します。 Azure SQL Managed Instance への移行に関する詳細については、[Azure SQL Managed Instance に SQL Server インスタンスを移行する](../migration-guides/managed-instance/sql-server-to-managed-instance-overview.md)方法に関するページをご覧ください。 Azure SQL に移行するための移行オプションとツールの選択に関するガイダンスについては、「[Azure SQL への移行](../migration-guides/index.yml)」を参照してください。


## <a name="migrate-to-a-single-database-or-a-pooled-database"></a>単一データベースまたはプールされたデータベースに移行する

SQL Server 2005 以降のデータベースを Azure SQL Database に移行する場合、主に 2 つの方法があります。 1 つ目の方法の方が簡単ですが、移行中にダウンタイムが必要です。これは長時間にわたる可能性があります。 2 つ目の方法はより複雑ですが、移行中のダウンタイムは大幅に解消されます。

どちらの場合も、[Data Migration Assistant (DMA)](https://www.microsoft.com/download/details.aspx?id=53595) を使用してソース データベースに Azure SQL Database との互換性があることを確認する必要があります。 SQL Database は、SQL Server との[機能の同等性](features-comparison.md)を目指していますが、サーバーレベルの操作とデータベース間の操作に関連した問題は残っています。 データベースやアプリケーションで、[部分的にしかサポートされていない機能またはサポートされていない機能](transact-sql-tsql-differences-sql-server.md)を使用している場合には、SQL Server データベースの移行前に[リエンジニアリングを実施してこれらの非互換性を修正する](migrate-to-database-from-sql-server.md#resolving-database-migration-compatibility-issues)必要があります。

> [!NOTE]
> Microsoft Access、Sybase、MySQL Oracle、DB2 などの SQL Server 以外のデータベースを Azure SQL Database に移行する場合は、 [SQL Server Migration Assistant](https://blogs.msdn.microsoft.com/datamigration/2017/09/29/release-sql-server-migration-assistant-ssma-v7-6/)チームのブログ記事を参照してください。

## <a name="method-1-migration-with-downtime-during-the-migration"></a>方法 1:移行中にダウンタイムを伴う移行

 ある程度のダウンタイムが許容される場合、または将来の移行に備えて運用データベースの移行をテストする場合には、この方法を使用して、単一データベースまたはプールされたデータベースを移行します。 チュートリアルについては、[SQL Server データベースを移行する](../../dms/tutorial-sql-server-to-azure-sql.md)を参照してください。

次の一覧は、単一データベースまたはプールされたデータベースの SQL Server データベースをこの方法で移行する場合の一般的なワークフローを示します。 SQL Managed Instance への移行に関する詳細については、[SQL Managed Instance に移行する](../managed-instance/migrate-to-instance-from-sql-server.md)方法に関するページを参照してください。

  ![VSSSDT の移行ダイアグラム](./media/migrate-to-database-from-sql-server/azure-sql-migration-sql-db.png)

1. [Data Migration Assistant (DMA)](https://www.microsoft.com/download/details.aspx?id=53595) の最新バージョンを使用して、データベースの互換性を[評価](/sql/dma/dma-assesssqlonprem)します。
2. 必要な修正を Transact-SQL スクリプトとして準備します。
3. 移行するソース データベースについて、トランザクション上一貫性のあるコピーを作成するか、または移行の実行中にソース データベースで新規トランザクションが発生しないようにします。 後者のオプションを実行するには、クライアント接続を無効にする方法と、[データベース スナップショット](/sql/relational-databases/databases/create-a-database-snapshot-transact-sql)を作成する方法があります。 移行が完了した後、トランザクション レプリケーションを使用して、移行したデータベースを更新すると、移行のカットオフ後に発生した変更を反映させることができます。 [トランザクション移行を使用した移行に関するセクション](migrate-to-database-from-sql-server.md#method-2-use-transactional-replication)を参照してください。  
4. Transact-SQL スクリプトをデプロイして、データベースのコピーに修正を適用します。
5. Data Migration Assistant を使用し、Azure SQL Database の新しいデータベースにデータベースのコピーを[移行](/sql/dma/dma-migrateonpremsql)します。

> [!NOTE]
> DMA を使用せずに、BACPAC ファイルを使用することもできます。 [BACPAC ファイルを Azure SQL Database の新しいデータベースにインポートする](database-import.md)方法に関するページを参照してください。

### <a name="optimizing-data-transfer-performance-during-migration"></a>移行中のデータ転送パフォーマンスの最適化

次の一覧は、インポート処理中に最適なパフォーマンスを得るための推奨事項です。

- 転送のパフォーマンスを最大限に高めるために、予算が許す限り最も高いサービス レベルとコンピューティング サイズを選択する。 移行の完了後にスケールダウンすることでコストを削減できます。
- BACPAC ファイルと移行先のデータ センターの間の距離を最短にする。
- 移行中の自動統計を無効にする
- パーティション テーブルとパーティション インデックス
- インデックス付きビューを削除し、完了したら作成し直す。
- ほとんど照会されない履歴データを別のデータベースに移動し、この履歴データを Azure SQL Database の別のデータベースに移行する。 その後、この履歴データは、[エラスティック クエリ](elastic-query-overview.md)を使用して照会できます。

### <a name="optimize-performance-after-the-migration-completes"></a>移行完了後のパフォーマンスの最適化

移行が完了した後に、フル スキャンを実施して[統計を更新](/sql/t-sql/statements/update-statistics-transact-sql)します。

## <a name="method-2-use-transactional-replication"></a>方法 2:トランザクション レプリケーションの使用

移行中、SQL Server データベースを運用から外す余裕がない場合、移行ソリューションとして SQL Server トランザクション レプリケーションを使用できます。 この方法を使用するには、ソース データベースが[トランザクション レプリケーションの要件](./replication-to-sql-database.md)を満たしているほか、Azure SQL Database に対する互換性を持っている必要があります。 AlwaysOn を使った SQL レプリケーションの詳細については、「[Always On 可用性グループのレプリケーションの構成 (SQL Server)](/sql/database-engine/availability-groups/windows/configure-replication-for-always-on-availability-groups-sql-server)」をご覧ください。

このソリューションを使用するには、Azure SQL Database のデータベースを、移行対象の SQL Server インスタンスへのサブスクライバーとして構成します。 トランザクション レプリケーション ディストリビューターは、新しいトランザクションが発生し続ける中で、同期対象のデータベース (パブリッシャー) からデータを同期します。

トランザクション レプリケーションでは、データやスキーマのすべての変更が Azure SQL Database のデータベースに表示されます。 同期が完了し、移行の準備ができたら、アプリケーションの接続文字列を変更し、データベースをポイントするようにします。 トランザクション レプリケーションがソース データベースに残っているすべての変更を抜き取り、すべてのアプリケーションが Azure DB をポイントしたら、トランザクション レプリケーションをアンインストールできます。 これで、Azure SQL Database のデータベースが実稼働システムになります。

 ![SeedCloudTR ダイアグラム](./media/migrate-to-database-from-sql-server/SeedCloudTR.png)

> [!TIP]
> トランザクション レプリケーションを使用して、ソース データベースの一部を移行することもできます。 Azure SQL Database に複製するパブリケーションは、複製されるデータベースのテーブルの一部に制限できます。 複製されるテーブルごとに、行の一部または列の一部にデータを制限できます。

## <a name="migration-to-sql-database-using-transaction-replication-workflow"></a>トランザクション レプリケーション ワークフローを使用した SQL Database への移行

> [!IMPORTANT]
> 最新バージョンの SQL Server Management Studio を使用して、Azure と SQL Database の更新プログラムとの同期を維持します。 以前のバージョンの SQL Server Management Studio では、サブスクライバーとして SQL Database を設定できません。 [SQL Server Management Studio を更新します](/sql/ssms/download-sql-server-management-studio-ssms)。

1. ディストリビューションの設定
   - [SQL Server Management Studio (SSMS) を使用する](/sql/relational-databases/replication/configure-publishing-and-distribution/)
   - [Transact-SQL の使用](/sql/relational-databases/replication/configure-publishing-and-distribution/)

2. パブリケーションの作成
   - [SQL Server Management Studio (SSMS) を使用する](/sql/relational-databases/replication/configure-publishing-and-distribution/)
   - [Transact-SQL の使用](/sql/relational-databases/replication/configure-publishing-and-distribution/)
3. サブスクリプションの作成
   - [SQL Server Management Studio (SSMS) を使用する](/sql/relational-databases/replication/create-a-push-subscription/)
   - [Transact-SQL の使用](/sql/relational-databases/replication/create-a-push-subscription/)

SQL Database への移行に関するヒントと相違点

- ローカル ディストリビューターを使用します。
  - これにより、サーバーのパフォーマンスに影響が生じます。
  - パフォーマンスへの影響を許容できない場合は、別のサーバーを使用できますが、管理がさらに複雑になります。
- スナップショット フォルダーを選択する際は、選択したフォルダーが、レプリケートするすべてのテーブルの BCP を保持するのに十分な大きさであることを確認してください。
- スナップショットの作成では、その処理が完了するまで関連付けられたテーブルがロックされるため、スナップショットは適宜スケジュールしてください。
- Azure SQL Database でサポートされているのは、プッシュ サブスクリプションのみです。 サブスクライバーを追加できるのは、ソース データベースからのみです。

## <a name="resolving-database-migration-compatibility-issues"></a>データベース移行に関する互換性の問題の解決

ソース データベースの SQL Server のバージョンと移行するデータベースの複雑さに応じて、さまざまな互換性の問題が発生する可能性があります。 SQL Server のバージョンが古いほど、互換性の問題が多く発生します。 任意の検索エンジンを使用する対象のインターネット検索に加え、以下のリソースを使用します。

- [SQL Server database features not supported in Azure SQL Database (Azure SQL Database でサポートされない SQL Server データベースの機能)](transact-sql-tsql-differences-sql-server.md)
- [SQL Server 2016 で廃止されたデータベース エンジンの機能](/sql/database-engine/discontinued-database-engine-functionality-in-sql-server)
- [SQL Server 2014 で廃止されたデータベース エンジンの機能](/sql/database-engine/discontinued-database-engine-functionality-in-sql-server?viewFallbackFrom=sql-server-2014)
- [SQL Server 2012 で廃止されたデータベース エンジンの機能](/previous-versions/sql/sql-server-2012/ms144262(v=sql.110))
- [Discontinued Database Engine Functionality in SQL Server 2008 R2 (SQL Server 2008 R2 で廃止されたデータベース エンジンの機能)](/previous-versions/sql/sql-server-2008-r2/ms144262(v=sql.105))
- [SQL Server 2005 で廃止されたデータベース エンジンの機能](/previous-versions/sql/sql-server-2005/ms144262(v=sql.90))

インターネット検索および、これらのリソースを使用するだけでなく、[Azure SQL Database に関する Microsoft Q&A 質問ページ](/answers/topics/azure-sql-database.html)または [StackOverflow](https://stackoverflow.com/) をご利用ください。

> [!IMPORTANT]
> Azure SQL Managed Instance を使用すると、互換性の問題をゼロまたは最小限に抑えながら、既存の SQL Server インスタンスとそのデータベースを移行できます。 [マネージド インスタンスの概要](../managed-instance/sql-managed-instance-paas-overview.md)に関するページを参照してください。

## <a name="next-steps"></a>次のステップ

- Azure SQL EMEA Engineers のブログにあるスクリプトを使用して、[移行中の tempdb の使用状況を監視](/archive/blogs/azuresqlemea/lesson-learned-10-monitoring-tempdb-usage)します。
- Azure SQL EMEA Engineers のブログにあるスクリプトを使用して、[移行中にデータベースのトランザクション ログ領域を監視](/archive/blogs/azuresqlemea/lesson-learned-7-monitoring-the-transaction-log-space-of-my-database)します。
- BACPAC ファイルを使用した移行に関する SQL Server Customer Advisory Team のブログについては、「[Migrating from SQL Server to Azure SQL Database using BACPAC Files (BACPAC ファイルを使用した SQL Server から Azure SQL Database への移行)](/archive/blogs/sqlcat/migrating-from-sql-server-to-azure-sql-database-using-bacpac-files)」を参照してください。
- 移行後に UTC 時間で作業する方法については、[ローカル タイム ゾーンの既定のタイム ゾーンの変更](/archive/blogs/azuresqlemea/lesson-learned-4-modifying-the-default-time-zone-for-your-local-time-zone)に関する記事を参照してください。
- 移行後にデータベースの既定の言語を変更する方法については、[Azure SQL Database の既定の言語を変更する方法](/archive/blogs/azuresqlemea/lesson-learned-16-how-to-change-the-default-language-of-azure-sql-database)に関する記事を参照してください。
