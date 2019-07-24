---
title: Azure SQL Database の単一データベース/プールされたデータベースへの SQL Server データベースの移行 | Microsoft Docs
description: SQL Server データベースを Azure SQL Database の単一データベースまたはエラスティック プールに移行する方法について説明します。
keywords: データベースの移行, SQL Server データベースの移行, データベース移行ツール, データベースを移行する, SQL データベースを移行する
services: sql-database
ms.service: sql-database
ms.subservice: migration
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: CarlRabeler
ms.author: carlrab
ms.reviewer: carlrab
manager: craigg
ms.date: 02/11/2019
ms.openlocfilehash: 2feece21644f8b79b4e5fc74331944cdddbdee4a
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2019
ms.locfileid: "57996418"
---
# <a name="sql-server-database-migration-to-azure-sql-database"></a>SQL Server データベースの Azure SQL Database への移行

この記事では、SQL Server 2005 以降のデータベースを Azure SQL Database の単一データベースまたはプール データベースに移行するための主な方法について説明します。 マネージド インスタンスへの移行については、[Azure SQL Database Managed Instance への SQL Server インスタンスの移行](sql-database-managed-instance-migrate.md)に関するページをご覧ください。 他のプラットフォームからの移行に関する移行の情報については、[Azure データベース移行ガイド](https://datamigration.microsoft.com/)を参照してください。

## <a name="migrate-to-a-single-database-or-a-pooled-database"></a>単一データベースまたはプール データベースに移行する

SQL Server 2005 以降のデータベースを Azure SQL Database の単一データベースまたはプール データベースに移行するには、主に 2 つの方法があります。 1 つ目の方法の方が簡単ですが、移行中にダウンタイムが必要です。これは長時間にわたる可能性があります。 2 つ目の方法はより複雑ですが、移行中のダウンタイムは大幅に解消されます。

どちらの場合も、[Data Migration Assistant (DMA)](https://www.microsoft.com/download/details.aspx?id=53595) を使用してソース データベースに Azure SQL Database との互換性があることを確認する必要があります。 SQL Database V12 は、SQL Server との[機能の同等性](sql-database-features.md)を目指していますが、サーバーレベルの操作とデータベース間の操作に関連した問題は残っています。 データベースやアプリケーションで、[部分的にしかサポートされていない機能またはサポートされていない機能](sql-database-transact-sql-information.md)を使用している場合には、SQL Server データベースの移行前に[リエンジニアリングを実施してこれらの非互換性を修正する](sql-database-single-database-migrate.md#resolving-database-migration-compatibility-issues)必要があります。

> [!NOTE]
> Microsoft Access、Sybase、MySQL Oracle、DB2 などの SQL Server 以外のデータベースを Azure SQL Database に移行する場合は、 [SQL Server Migration Assistant](https://blogs.msdn.microsoft.com/datamigration/2017/09/29/release-sql-server-migration-assistant-ssma-v7-6/)チームのブログ記事を参照してください。

## <a name="method-1-migration-with-downtime-during-the-migration"></a>方法 1:移行中にダウンタイムを伴う移行

 ある程度のダウンタイムが許容される場合、または将来の移行に備えて運用データベースの移行をテストする場合には、この方法を使用して、単一データベースまたはプール データベースを移行します。 チュートリアルについては、[SQL Server データベースを移行する](../dms/tutorial-sql-server-to-azure-sql.md)を参照してください。

次の一覧は、単一データベースまたはプール データベースの SQL Server データベースをこの方法で移行する場合の一般的なワークフローを示します。 Managed Instance への移行については、[マネージド インスタンスへの移行に関するページ](sql-database-managed-instance-migrate.md)を参照してください。

  ![VSSSDT の移行ダイアグラム](./media/sql-database-cloud-migrate/azure-sql-migration-sql-db.png)

1. [Data Migration Assistant (DMA)](https://www.microsoft.com/download/details.aspx?id=53595) の最新バージョンを使用して、データベースの互換性を[評価](https://docs.microsoft.com/sql/dma/dma-assesssqlonprem)します。
2. 必要な修正を Transact-SQL スクリプトとして準備します。
3. 移行するソース データベースについて、トランザクション上一貫性のあるコピーを作成するか、または移行の実行中にソース データベースで新規トランザクションが発生しないようにします。 後者のオプションを実行するには、クライアント接続を無効にする方法と、[データベース スナップショット](https://msdn.microsoft.com/library/ms175876.aspx)を作成する方法があります。 移行が完了した後、トランザクション レプリケーションを使用して、移行したデータベースを更新すると、移行のカットオフ後に発生した変更を反映させることができます。 [トランザクション移行を使用した移行に関するセクション](sql-database-single-database-migrate.md#method-2-use-transactional-replication)を参照してください。  
4. Transact-SQL スクリプトをデプロイして、データベースのコピーに修正を適用します。
5. Data Migration Assistant を使用して、データベースのコピーを新しい Azure SQL Database に[移行](https://docs.microsoft.com/sql/dma/dma-migrateonpremsql)します。

> [!NOTE]
> DMA を使用せずに、BACPAC ファイルを使用することもできます。 「[BACPAC ファイルを新しい Azure SQL Database にインポートする](sql-database-import.md)」を参照してください。

### <a name="optimizing-data-transfer-performance-during-migration"></a>移行中のデータ転送パフォーマンスの最適化

次の一覧は、インポート処理中に最適なパフォーマンスを得るための推奨事項です。

- 転送のパフォーマンスを最大限に高めるために、予算が許す限り最も高いサービス レベルとコンピューティング サイズを選択する。 移行の完了後にスケールダウンすることでコストを削減できます。
- BACPAC ファイルと移行先のデータ センターの間の距離を最短にする。
- 移行中の自動統計を無効にする。
- テーブルとインデックスをパーティション分割する。
- インデックス付きビューを削除し、完了したら作成し直す。
- ほとんど照会されない履歴データを別のデータベースに移動し、この履歴データを別の Azure SQL データベースに移行する。 その後、この履歴データは、[エラスティック クエリ](sql-database-elastic-query-overview.md)を使用して照会できます。

### <a name="optimize-performance-after-the-migration-completes"></a>移行完了後のパフォーマンスの最適化

移行が完了した後に、フル スキャンを実施して[統計を更新](https://msdn.microsoft.com/library/ms187348.aspx)します。

## <a name="method-2-use-transactional-replication"></a>方法 2:トランザクション レプリケーションの使用

移行中、SQL Server データベースを外す余裕がない場合、移行ソリューションとして SQL Server トランザクション レプリケーションを使用できます。 この方法を使用するには、ソース データベースが[トランザクション レプリケーションの要件](https://msdn.microsoft.com/library/mt589530.aspx)を満たしているほか、Azure SQL Database に対する互換性を持っている必要があります。 AlwaysOn を使った SQL レプリケーションの詳細については、「[Always On 可用性グループのレプリケーションの構成 (SQL Server)](/sql/database-engine/availability-groups/windows/configure-replication-for-always-on-availability-groups-sql-server)」をご覧ください。

このソリューションを使用するには、Azure SQL Database を移行対象のSQL Server インスタンスへのサブスクライバーとして構成します。 トランザクション レプリケーション ディストリビューターは、新しいトランザクションが発生し続ける中で、同期対象のデータベース (パブリッシャー) からデータを同期します。

トランザクション レプリケーションでは、データやスキーマのすべての変更が Azure SQL Database に表示されます。 同期が完了し、移行の準備ができたら、アプリケーションの接続文字列を変更し、Azure SQL Database をポイントするようにします。 トランザクション レプリケーションがソース データベースに残っているすべての変更を抜き取り、すべてのアプリケーションが Azure DB をポイントしたら、トランザクション レプリケーションをアンインストールできます。 これで、Azure SQL Database が実稼働システムになります。

 ![SeedCloudTR ダイアグラム](./media/sql-database-cloud-migrate/SeedCloudTR.png)

> [!TIP]
> トランザクション レプリケーションを使用して、ソース データベースの一部を移行することもできます。 Azure SQL Database に複製するパブリケーションは、複製されるデータベースのテーブルの一部に制限できます。 複製されるテーブルごとに、行の一部または列の一部にデータを制限できます。

## <a name="migration-to-sql-database-using-transaction-replication-workflow"></a>トランザクション レプリケーション ワークフローを使用した SQL Database への移行

> [!IMPORTANT]
> 最新バージョンの SQL Server Management Studio を使用して、Microsoft Azure と SQL Database の更新プログラムとの同期を維持します。 以前のバージョンの SQL Server Management Studio では、サブスクライバーとして SQL Database を設定できません。 [SQL Server Management Studio を更新します](https://msdn.microsoft.com/library/mt238290.aspx)。

1. ディストリビューションの設定
   - [SQL Server Management Studio (SSMS) を使用する](https://msdn.microsoft.com/library/ms151192.aspx#Anchor_1)
   - [Transact-SQL を使用する](https://msdn.microsoft.com/library/ms151192.aspx#Anchor_2)

2. パブリケーションの作成
   - [SQL Server Management Studio (SSMS) を使用する](https://msdn.microsoft.com/library/ms151160.aspx#Anchor_1)
   - [Transact-SQL を使用する](https://msdn.microsoft.com/library/ms151160.aspx#Anchor_2)
3. サブスクリプションの作成
   - [SQL Server Management Studio (SSMS) を使用する](https://msdn.microsoft.com/library/ms152566.aspx#Anchor_0)
   - [Transact-SQL を使用する](https://msdn.microsoft.com/library/ms152566.aspx#Anchor_1)

SQL Database への移行に関するヒントと相違点

- ローカル ディストリビューターを使用します。
  - これにより、サーバーのパフォーマンスに影響が生じます。
  - パフォーマンスへの影響を許容できない場合は、別のサーバーを使用できますが、管理がさらに複雑になります。
- スナップショット フォルダーを選択する際は、選択したフォルダーが、レプリケートするすべてのテーブルの BCP を保持するのに十分な大きさであることを確認してください。
- スナップショットの作成では、その処理が完了するまで関連付けられたテーブルがロックされるため、スナップショットは適宜スケジュールしてください。
- Azure SQL Database でサポートされているのは、プッシュ サブスクリプションのみです。 サブスクライバーを追加できるのは、ソース データベースからのみです。

## <a name="resolving-database-migration-compatibility-issues"></a>データベース移行に関する互換性の問題の解決

ソース データベースの SQL Server のバージョンと移行するデータベースの複雑さに応じて、さまざまな互換性の問題が発生する可能性があります。 SQL Server のバージョンが古いほど、互換性の問題が多く発生します。 任意の検索エンジンを使用する対象のインターネット検索に加え、以下のリソースを使用します。

- [SQL Server database features not supported in Azure SQL Database (Azure SQL Database でサポートされない SQL Server データベースの機能)](sql-database-transact-sql-information.md)
- [Discontinued Database Engine Functionality in SQL Server 2016 (SQL Server 2016 で廃止されたデータベース エンジンの機能)](https://msdn.microsoft.com/library/ms144262%28v=sql.130%29)
- [Discontinued Database Engine Functionality in SQL Server 2014 (SQL Server 2014 で廃止されたデータベース エンジンの機能)](https://msdn.microsoft.com/library/ms144262%28v=sql.120%29)
- [Discontinued Database Engine Functionality in SQL Server 2012 (SQL Server 2012 で廃止されたデータベース エンジンの機能)](https://msdn.microsoft.com/library/ms144262%28v=sql.110%29)
- [Discontinued Database Engine Functionality in SQL Server 2008 R2 (SQL Server 2008 R2 で廃止されたデータベース エンジンの機能)](https://msdn.microsoft.com/library/ms144262%28v=sql.105%29)
- [SQL Server 2005 で廃止されたデータベース エンジンの機能](https://msdn.microsoft.com/library/ms144262%28v=sql.90%29)

インターネット検索やこれらのリソースの参照に加えて、[MSDN SQL Server コミュニティ フォーラム](https://social.msdn.microsoft.com/Forums/sqlserver/home?category=sqlserver)や [StackOverflow](https://stackoverflow.com/) も参照してください。

> [!IMPORTANT]
> SQL Database Managed Instance を使用すると、互換性の問題をゼロまたは最小限に抑えながら、既存の SQL Server インスタンスとそのデータベースを移行できます。 「[What is an Managed Instance](sql-database-managed-instance.md)」(マネージド インスタンスとは) を参照してください。

## <a name="next-steps"></a>次の手順

- Azure SQL EMEA Engineers のブログにあるスクリプトを使用して、[移行中の tempdb の使用状況を監視](https://blogs.msdn.microsoft.com/azuresqlemea/2016/12/28/lesson-learned-10-monitoring-tempdb-usage/)します。
- Azure SQL EMEA Engineers のブログにあるスクリプトを使用して、[移行中にデータベースのトランザクション ログ領域を監視](https://blogs.msdn.microsoft.com/azuresqlemea/2016/10/31/lesson-learned-7-monitoring-the-transaction-log-space-of-my-database/0)します。
- BACPAC ファイルを使用した移行に関する SQL Server Customer Advisory Team のブログについては、「[Migrating from SQL Server to Azure SQL Database using BACPAC Files (BACPAC ファイルを使用した SQL Server から Azure SQL Database への移行)](https://blogs.msdn.microsoft.com/sqlcat/2016/10/20/migrating-from-sql-server-to-azure-sql-database-using-bacpac-files/)」を参照してください。
- 移行後に UTC 時間で作業する方法については、[ローカル タイム ゾーンの既定のタイム ゾーンの変更](https://blogs.msdn.microsoft.com/azuresqlemea/2016/07/27/lesson-learned-4-modifying-the-default-time-zone-for-your-local-time-zone/)に関する記事を参照してください。
- 移行後にデータベースの既定の言語を変更する方法については、[Azure SQL Database の既定の言語を変更する方法](https://blogs.msdn.microsoft.com/azuresqlemea/2017/01/13/lesson-learned-16-how-to-change-the-default-language-of-azure-sql-database/)に関する記事を参照してください。
