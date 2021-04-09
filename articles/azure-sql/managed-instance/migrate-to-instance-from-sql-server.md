---
title: SQL Server から Azure SQL Managed Instance に移行する
description: SQL Server から Azure SQL Managed Instance にデータベースを移行する方法を説明します。
services: sql-database
ms.service: sql-managed-instance
ms.subservice: migration
ms.custom: seo-lt-2019, sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: bonova
ms.author: bonova
ms.reviewer: ''
ms.date: 07/11/2019
ms.openlocfilehash: 49d37a5537ada260eae453bbb5f81716d42657a5
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102565824"
---
# <a name="sql-server-instance-migration-to-azure-sql-managed-instance"></a>Azure SQL Managed Instance への SQL Server インスタンスの移行
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

この記事では、SQL Server 2005 以降のバージョンのインスタンスを [Azure SQL Managed Instance](sql-managed-instance-paas-overview.md) に移行する方法について説明します。 単一データベースまたはエラスティック プールへの移行については、「[移行の概要:SQL Server から SQL Database](../migration-guides/database/sql-server-to-sql-database-overview.md)」を参照してください。 他のプラットフォームからの移行、およびツールとオプションに関するガイダンスについては、「[Azure SQL への移行](../migration-guides/index.yml)」を参照してください。

> [!NOTE]
> Azure SQL Managed Instance をすぐに開始して試す場合は、このページではなく[クイックスタート ガイド](quickstart-content-reference-guide.md)に関するページをご覧ください。

大まかには、データベースの移行プロセスは次のようになります。

![移行プロセス](./media/migrate-to-instance-from-sql-server/migration-process.png)

- [Assess SQL Managed Instance の互換性を評価する](#assess-sql-managed-instance-compatibility)。ここでは、移行を妨げる可能性のある、障害となっている問題がないことを確認する必要があります。
  
  この手順には、ソース SQL Server インスタンスのリソース使用率を判断するための[パフォーマンス ベースライン](#create-a-performance-baseline)の作成も含まれます。 この手順は、適切なサイズに設定された Managed Instance をデプロイし、移行後のパフォーマンスに影響がないことを確認する場合に必要です。
- [アプリの接続性オプションを選択する](connect-application-instance.md)。
- [最適なサイズに設定されたマネージド インスタンスにデプロイする](#deploy-to-an-optimally-sized-managed-instance)。ここでは、マネージド インスタンスの技術的特性 (仮想コア数、メモリ容量) とパフォーマンス レベル (Business Critical、General Purpose) を選択します。
- [移行方法の選択と移行](#select-a-migration-method-and-migrate)。ここでは、オフライン移行 (ネイティブ バックアップ/復元、データベースのインポート/エクスポート) またはオンライン移行 (Azure Data Migration Service、トランザクション レプリケーション) を使用してデータベースを移行します。
- [アプリケーションの監視](#monitor-applications)を行い、期待されるパフォーマンスが得られることを確認します。

> [!NOTE]
> 個々のデータベースを単一データベースまたはエラスティック プールに移行するには、[Azure SQL Database への SQL Server データベースの移行](../database/migrate-to-database-from-sql-server.md)に関する記事をご覧ください。

## <a name="assess-sql-managed-instance-compatibility"></a>SQL Managed Instance の互換性を評価する

まず、SQL Managed Instance がアプリケーションのデータベース要件と互換性があるかどうかを判定します。 SQL Managed Instance は、SQL Server を使用する既存のアプリケーションの大部分について簡単なリフト アンド シフト移行を提供するように設計されています。 ただし、まだサポートされていない機能が必要で、回避策を実装するコストが高すぎる場合があります。

[Data Migration Assistant](/sql/dma/dma-overview) を使用して、Azure SQL Database のデータベース機能に影響を与える可能性のある互換性の問題を検出します。 障害となっている問題が報告されている場合、[Azure VM 上の SQL Server](https://azure.microsoft.com/services/virtual-machines/sql-server/) などの代替オプションを考慮することが必要な場合があります。 次に例をいくつか示します。

- オペレーティング システムやファイル システムへの直接アクセスが必要な場合、たとえばサードパーティ製のエージェントやカスタム エージェントを SQL Server と同じ仮想マシンにインストールする場合。
- FileStream/FileTable、PolyBase、クロス インスタンス トランザクションなど、まだサポートされていない機能に対する厳密な依存関係がある場合。
- 特定バージョンの SQL Server (たとえば 2012 など) を確実に維持する必要がある場合。
- コンピューティング要件がマネージド インスタンスによって提供されるものよりはるかに低く (1 つの仮想コアなど)、データベース統合が許容可能なオプションではない場合。

すべての識別された移行阻害要因を解決し、SQL Managed Instance への移行を続行する場合、変更の一部によってワークロードのパフォーマンスが影響を受ける可能性があることに注意してください。

- 定期的に単純/一括ログ モデルを使っている場合、またはオンデマンドでバックアップを停止している場合、必須の完全復旧モデルと定期的な自動バックアップ スケジュールでは、ワークロードまたはメンテナンス/ETL 操作のパフォーマンスに影響する可能性があります。
- トレース フラグや互換性レベルなど、サーバー レベルまたはデータベース レベルの異なる構成。
- Transparent Database Encryption (TDE) や自動フェールオーバー グループなどの新しい機能を使うと、CPU と IO の使用率が影響を受ける可能性があります。

SQL Managed Instance では、重要なシナリオであっても 99.99% の可用性が保証されるので、これらの機能によるオーバーヘッドを無効にすることはできません。 詳しくは、[SQL Server と、Azure SQL Managed Instance でパフォーマンスが異なる原因](https://azure.microsoft.com/blog/key-causes-of-performance-differences-between-sql-managed-instance-and-sql-server/)に関するページをご覧ください。

#### <a name="in-memory-oltp-memory-optimized-tables"></a>インメモリ OLTP (メモリ最適化テーブル)

SQL Server にはインメモリ OLTP 機能があります。これを使用すると、メモリ最適化テーブル、メモリ最適化テーブル型、ネイティブにコンパイルされた SQL モジュールなどを利用して、高いスループットと短い待機時間というトランザクション処理要件を持つワークロードを実行することができます。 

> [!IMPORTANT]
> インメモリ OLTP は、Azure SQL Managed Instance の Business Critical レベルでのみサポートされています (General Purpose レベルではサポートされていません)。

オンプレミスの SQL Server にメモリ最適化テーブルまたはメモリ最適化テーブル型がある場合、Azure SQL Managed Instance に移行する際には、次のいずれかを行う必要があります。

- インメモリ OLTP がサポートされているターゲット Azure SQL Managed Instance の Business Critical レベルを選択します。または
- Azure SQL Managed Instance の General Purpose レベルに移行する場合は、データベースを移行する前に、メモリ最適化テーブル、メモリ最適化テーブル型、およびメモリ最適化オブジェクトと対話する、ネイティブにコンパイルされた SQL モジュールを削除します。 次の T-SQL クエリを使用すると、General Purpose レベルに移行する前に削除する必要があるすべてのオブジェクトを識別できます。

```tsql
SELECT * FROM sys.tables WHERE is_memory_optimized=1
SELECT * FROM sys.table_types WHERE is_memory_optimized=1
SELECT * FROM sys.sql_modules WHERE uses_native_compilation=1
```

インメモリ テクノロジの詳細については、「[Azure SQL Database と Azure SQL Managed Instance でインメモリ テクノロジを使用してパフォーマンスを最適化する](https://docs.microsoft.com/azure/azure-sql/in-memory-oltp-overview)」を参照してください。

### <a name="create-a-performance-baseline"></a>パフォーマンスのベースラインを作成する

マネージド インスタンスでのワークロードのパフォーマンスと SQL Server で実行されている元のワークロードのパフォーマンスを比較する必要がある場合は、比較に使われるパフォーマンス ベースラインを作成する必要があります。

パフォーマンス ベースラインは、平均/最大 CPU 使用率、平均/最大ディスク IO 待機時間、スループット、IOPS、ページの平均/最大予測保持期間、tempdb の平均最大サイズなどのパラメーターのセットです。 移行後に、同等またはさらに優れたパラメーターが必要になるため、これらのパラメーターのベースライン値を測定して記録することが重要です。 システム パラメーターに加えて、ワークロード内の代表的なクエリまたは最も重要なクエリのセットを選択し、選択したクエリの最小/平均/最大期間と CPU 使用率を測定する必要があります。 これらの値を使用すると、マネージド インスタンスで実行されているワークロードのパフォーマンスをソース SQL Server インスタンスの元の値と比較できます。

SQL Server インスタンスで測定する必要のあるパラメーターの一部を次に示します。

- [SQL Server インスタンスでの CPU 使用率を監視](https://techcommunity.microsoft.com/t5/Azure-SQL-Database/Monitor-CPU-usage-on-SQL-Server/ba-p/680777#M131)し、平均とピークの CPU 使用率を記録します。
- [SQL Server インスタンスでのメモリ使用量を監視](/sql/relational-databases/performance-monitor/monitor-memory-usage)し、バッファー プール、プラン キャッシュ、列ストア プール、[インメモリ OLTP](/sql/relational-databases/in-memory-oltp/monitor-and-troubleshoot-memory-usage?view=sql-server-2017) などのさまざまなコンポーネントで使用されるメモリの量を明らかにします。さらに、ページの予測保持期間メモリ パフォーマンス カウンターの平均値とピーク値を調べる必要があります。
- [sys.dm_io_virtual_file_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-io-virtual-file-stats-transact-sql) ビューまたは[パフォーマンス カウンター](/sql/relational-databases/performance-monitor/monitor-disk-usage)を使って、ソース SQL Server インスタンスでのディスク IO 使用率を監視ます。
- SQL Server 2016 以降のバージョンから移行する場合は、動的管理ビューまたはクエリ ストアを調べることで、ワークロードとクエリのパフォーマンスまたは SQL Server インスタンスを監視します。 ワークロードで最も重要なクエリの平均継続時間と CPU 使用率を特定し、マネージド インスタンスで実行されているクエリと比較します。

> [!Note]
> 高い CPU 使用率、メモリの常時不足、tempdb またはパラメーター化の問題など、SQL Server のワークロードで何らかの問題が検出された場合は、ベースラインを取得して移行を実行する前に、ソース SQL Server インスタンスでそれらを解決する必要があります。 既知の問題が新しいシステムに移行されると、予期しない結果が発生し、パフォーマンスの比較が無効になる可能性があります。

このアクティビティの結果として、ソース システムでの CPU、メモリ、IO の使用率のピーク値と平均値、およびワークロードでの主要なクエリと最も重要なクエリの平均と最大の継続時間および CPU 使用率を、文書化する必要があります。 後でこれらの値を使って、マネージド インスタンスでのワークロードのパフォーマンスを、ソース SQL Server インスタンスでのワークロードのベースライン パフォーマンスと比較する必要があります。

## <a name="deploy-to-an-optimally-sized-managed-instance"></a>最適なサイズに設定されたマネージド インスタンスにデプロイする

SQL Managed Instance は、クラウドへの移行を予定しているオンプレミスのワークロード向けに調整されます。 ワークロードのリソースの適切なレベルの選択において高い柔軟性を発揮する[新しい購入モデル](../database/service-tiers-vcore.md)が導入されます。 オンプレミスの世界では、物理コアと IO 帯域幅を使用して、これらのワークロードのサイズを設定することがおそらく一般的です。 マネージド インスタンスの購入モデルは仮想コア ("vCore") に基づいており、追加のストレージと IO を個別に使用できます。 仮想コア モデルは、クラウドのコンピューティング要件と現在オンプレミスで使用しているものを把握するためのシンプルな方法です。 この新しいモデルにより、クラウド内の移行先環境を適切にサイズ設定することができます。 適切なサービス レベルと特性を選択するために役立ついくつかの一般的なガイドラインを次に示します。

- ベースラインの CPU 使用率に基づいて、SQL Server で使っているコアの数と一致するマネージド インスタンスをプロビジョニングできます。そのとき、[マネージド インスタンスがインストールされている VM の特性](resource-limits.md#hardware-generation-characteristics)と一致するように CPU の特性をスケーリングすることが必要になる場合があることに留意します。
- ベースラインのメモリ使用量に基づいて、[対応するメモリを備えたサービス レベル](resource-limits.md#hardware-generation-characteristics)を選択します。 メモリの量は直接選択できないため、一致するメモリ (Gen5 の 5.1 GB/仮想コアなど) を備えた仮想コアの容量を持つマネージド インスタンスを選択する必要があります。
- ファイル サブシステムのベースライン IO 待機時間に基づいて、General Purpose (5 ミリ秒を超える待機時間) と Business Critical (3 ミリ秒未満の待機時間) のサービス レベルのいずれかを選択します。
- 予期される IO パフォーマンスを得るために、ベースラインのスループットに基づいて、データ ファイルまたはログ ファイルのサイズを事前に割り当てます。

コンピューティング リソースとストレージ リソースをデプロイ時に選択し、後で [Azure portal](../database/scale-resources.md) を使用してアプリケーションのダウンタイムなしに変更できます。

![マネージド インスタンスのサイズ設定](./media/migrate-to-instance-from-sql-server/managed-instance-sizing.png)

VNet インフラストラクチャとマネージド インスタンスを作成する方法については、[マネージド インスタンスの作成](instance-create-quickstart.md)に関するページを参照してください。

> [!IMPORTANT]
> 移行先の VNet とサブネットが[マネージド インスタンスの VNet 要件](connectivity-architecture-overview.md#network-requirements)に従っているようにすることが重要です。 非互換性があると、新しいインスタンスの作成や、既に作成したインスタンスの使用ができなくなることがあります。 詳しくは、[ネットワークの新規作成](virtual-network-subnet-create-arm-template.md)と[既存のネットワークの構成](vnet-existing-add-subnet.md)に関する記事をご覧ください。

## <a name="select-a-migration-method-and-migrate"></a>移行方法の選択と移行

SQL Managed Instance のターゲットは、オンプレミスまたは Azure VM データベース実装からのデータベースの一括移行を必要とするユーザー シナリオです。 これらは、インスタンス レベルの機能や複数データベース間の機能を定期的に使用するアプリケーションのバックエンドのリフト アンド シフトが必要な場合に最適な選択肢です。 このシナリオに該当する場合は、アプリケーションを再設計する必要なしに Azure 内の対応する環境にインスタンスを移行できます。

SQL インスタンスを移行するには、以下を慎重に計画する必要があります。

- 併置する必要のある (同じインスタンスで実行されている) すべてのデータベースの移行。
- ログイン、資格情報、SQL エージェント ジョブと演算子、サーバー レベルのトリガーなど、アプリケーションが依存するインスタンス レベルのオブジェクトの移行。

SQL Managed Instance は、通常の DBA アクティビティの一部を組み込み時にユーザーがプラットフォームに委任できるマネージド サービスです。 したがって、[高可用性](../database/high-availability-sla.md)が組み込まれているため、定期的なバックアップや Always On 構成のメンテナンス ジョブなど、いくつかのインスタンス レベルのデータは移行する必要がありません。

SQL Managed Instance では、次のデータベース移行オプションがサポートされます (現在は、これらの移行方法のみがサポートされます)。

- Azure Database Migration Service - ほぼダウンタイムがない移行。
- ネイティブな `RESTORE DATABASE FROM URL` - SQL Server のネイティブ バックアップを使用し、ある程度のダウンタイムが必要。

### <a name="azure-database-migration-service"></a>Azure Database Migration Service

[Azure Database Migration Service](../../dms/dms-overview.md) は、複数のデータベース ソースから Azure データ プラットフォームへのシームレスな移行を最小限のダウンタイムで実現できるように設計された、フル マネージドのサービスです。 このサービスは、既存のサード パーティ製データベースと SQL Server データベースを Azure に移行するために必要なタスクを効率化します。 パブリック プレビューでのデプロイ オプションには、Azure SQL Database 内のデータベースおよび Azure 仮想マシン内の SQL Server データベースが含まれます。 Database Migration Service は、エンタープライズ ワークロードの移行に推奨される方法です。

オンプレミスの SQL Server で SQL Server Integration Services (SSIS) を使用する場合、Database Migration Service はまだ SSIS パッケージを格納する SSIS カタログ (SSISDB) の移行をサポートしていませんが、Azure Data Factory でマネージド インスタンスに新しい SSISDB を作成する Azure-SSIS 統合ランタイム (IR) をプロビジョニングし、そこにパッケージを再デプロイすることができます。 [Azure Data Factory での Azure-SSIS IR の作成](../../data-factory/create-azure-ssis-integration-runtime.md)に関するページを参照してください。

このシナリオおよび Database Migration Service での構成手順の詳細については、[Database Migration Service を使用したオンプレミス データベースのマネージド インスタンスへの移行](../../dms/tutorial-sql-server-to-managed-instance.md)に関するページを参照してください。  

### <a name="native-restore-from-url"></a>URL からのネイティブ復元

[Azure Storage](https://azure.microsoft.com/services/storage/) で使用可能な SQL Server インスタンスから取得されたネイティブ バックアップ (.bak ファイル) の復元は、SQL Managed Instance の主要機能の 1 つで、オフラインのデータベース移行を迅速かつ簡単に行うことができます。

次の図は、プロセスの概要を示しています。

![SQL Server、Azure Storage に向かう "BACKUP / Upload to URL" というラベルの矢印、Azure Storage から SQL の Managed Instance に向かう "RESTORE from URL" というラベルの 2 つ目の矢印を示す図。](./media/migrate-to-instance-from-sql-server/migration-flow.png)

次の表は、実行しているソース SQL Server のバージョンに応じて使用できる方法に関する詳細情報を示しています。

|手順|SQL エンジンとバージョン|バックアップ/復元方法|
|---|---|---|
|Azure Storage へのバックアップの格納|2012 SP1 CU2 より前|Azure Storage に .bak ファイルを直接アップロードする|
||2012 SP1 CU2 - 2016|非推奨の [WITH CREDENTIAL](/sql/t-sql/statements/restore-statements-transact-sql) 構文を使用して直接バックアップする|
||2016 以上|[WITH SAS CREDENTIAL](/sql/relational-databases/backup-restore/sql-server-backup-to-url) を使用して直接バックアップする|
|Azure Storage からマネージド インスタンスに復元する|[SAS 資格情報での URL からの復元](restore-sample-database-quickstart.md)|

> [!IMPORTANT]
>
> - ネイティブ復元オプションを使用して、[Transparent Data Encryption](../database/transparent-data-encryption-tde-overview.md) によって保護されたデータベースをマネージド インスタンスに移行する場合は、データベースの復元の前に、オンプレミスまたは Azure VM SQL Server の対応する証明書を移行する必要があります。 詳細な手順については、[TDE 証明書のマネージド インスタンスへの移行](tde-certificate-migrate.md)に関するページを参照してください。
> - システム データベースの復元はサポートされていません。 (マスターまたは msdb データベースに格納されている) インスタンス レベルのオブジェクトを移行するには、それらのスクリプトを作成し、移行先のインスタンスで T-SQL スクリプトを実行することをお勧めします。

SAS 資格情報を使用してデータベース バックアップをマネージド インスタンスに復元する方法を示すクイック スタートについては、[バックアップからマネージド インスタンスへの復元](restore-sample-database-quickstart.md)に関するページを参照してください。

> [!VIDEO https://www.youtube.com/embed/RxWYojo_Y3Q]

## <a name="monitor-applications"></a>アプリケーションの監視

マネージド インスタンスへの移行を完了した後は、アプリケーションの動作とワークロードのパフォーマンスを追跡する必要があります。 このプロセスには、次のアクティビティが含まれます。

- [ソース SQL Server インスタンス上で作成したパフォーマンス ベースライン](#create-a-performance-baseline)と、[マネージド インスタンスで実行されているワークロードのパフォーマンスを比較](#compare-performance-with-the-baseline)します。
- 継続的に[ワークロードのパフォーマンスを監視](#monitor-performance)し、問題と改善の可能性を明らかにします。

### <a name="compare-performance-with-the-baseline"></a>パフォーマンスとベースラインを比較する

移行が成功した後すぐに実行する必要のある最初のアクティビティは、ワークロードのパフォーマンスとベースライン ワークロードのパフォーマンスの比較です。 このアクティビティの目的は、マネージド インスタンスでのワークロードのパフォーマンスがニーズを満たしていることを確認することです。

マネージド インスタンスへのデータベースの移行では、ほとんどの場合においてデータベースの設定と元の互換性レベルが保持されます。 ソース SQL Server インスタンスと比較してパフォーマンスが低下するリスクを減らすため、可能な限り元の設定が保持されます。 移行前のユーザー データベースの互換性レベルが 100 以上の場合は、移行後も同じままになります。 アップグレードされたデータベースで、ユーザー データベースの互換性レベルが移行の前に 90 であった場合、その互換性レベルは、マネージド インスタンスでサポートされる最も低い互換性レベルである 100 に設定されます。 システム データベースの互換性レベルは 140 です。 マネージド インスタンスへの移行は、実際には最新の SQL Server データベース エンジンへの移行であるため、意外なパフォーマンスの問題を回避するため、ワークロードのパフォーマンスの再テストが必要であることに注意する必要があります。

前提条件として、次のアクティビティを完了したことを確認します。

- さまざまなインスタンス、データベース、tempdb の設定、および構成を調べて、マネージド インスタンスでの設定を、ソース SQL Server インスタンスの設定と一致させます。 最初のパフォーマンス比較を実行する前に互換性レベルや暗号化などの設定が変更されていないことを確認するか、または有効にした新機能によってクエリが影響を受けるリスクを受け入れるます。 移行に伴うリスクを軽減するには、パフォーマンスの監視後にのみ、データベース互換性レベルを変更します。
- 優れたパフォーマンスを実現するためのファイルのサイズの事前割り当てなど、[General Purpose に対するストレージのベスト プラクティスのガイドライン](https://techcommunity.microsoft.com)を実装します。
- [マネージド インスタンスと SQL Server でのパフォーマンスの違いを引き起こす可能性のある重要な環境の違い](https://azure.microsoft.com/blog/key-causes-of-performance-differences-between-sql-managed-instance-and-sql-server/)について学習し、パフォーマンスに影響する可能性のあるリスクを明らかにします。
- マネージド インスタンスでクエリ ストアと自動チューニングが有効になっていることを確認します。 これらの機能を使用すると、ワークロードのパフォーマンスを測定し、可能性があるパフォーマンスの問題を自動的に修正できます。 「[新しい SQL Server にアップグレードするときにパフォーマンスの安定性を維持する](/sql/relational-databases/performance/query-store-usage-scenarios#CEUpgrade)」の説明に従って、データベースの互換性レベルの変更前と変更後のワークロードのパフォーマンスに関する情報を取得するための最適なツールとしてクエリ ストアを使用する方法を学習します。
オンプレミスの環境と可能な限り一致するように環境を準備した後は、ワークロードの実行を開始してパフォーマンスを測定できます。 測定プロセスには、[ソース SQL Server インスタンスでワークロード測定のベースライン パフォーマンスを作成する](#create-a-performance-baseline)ときに測定したものと同じパラメーターを含める必要があります。
結果として、パフォーマンス パラメーターをベースラインと比較し、重要な違いを特定する必要があります。

> [!NOTE]
> 多くの場合、マネージド インスタンスと SQL Server で完全に一致するパフォーマンスを得ることはできません。 Azure SQL Managed Instance は SQL Server データベース エンジンですが、マネージド インスタンスでのインフラストラクチャと高可用性の構成により、いくつかの相違がもたらされる可能性があります。 速くなるクエリもあれば、遅くなるクエリもあります。 比較の目的は、マネージド インスタンスでのワークロードのパフォーマンスが SQL Server でのパフォーマンスと (平均で) 一致することを確認し、重要なクエリの中にパフォーマンスが元のパフォーマンスに匹敵しないものがあるかどうかを明らかにすることです。

パフォーマンスの比較の結果は次のようになります。

- マネージド インスタンスでのワークロードのパフォーマンスが、SQL Server でのワークロードのパフォーマンスと同等かそれより優れています。 この場合、移行が成功したことを正常に確認しました。
- ワークロードのパフォーマンス パラメーターとクエリの多くは問題なく動作していますが、一部の例外でパフォーマンスが低下しています。 この場合、相違と重要度を特定する必要があります。 重要なクエリでパフォーマンスが低下したものがある場合は、基になる SQL プランが変更されたか、またはクエリで何らかのリソースが制限に達しているかを調べる必要があります。 この場合の軽減策としては、重要なクエリ (変更された互換性レベル、レガシ カーディナリティ推定機能など) に対して直接的に、またはプラン ガイドを使ってヒントを適用するか、プランに影響を与える可能性がある統計情報とインデックスを再構築または作成します。
- ほとんどのクエリは、ソース SQL Server インスタンスよりマネージド インスタンスでの方が実行速度が遅くなります。 この場合は、IO 制限、メモリ制限、インスタンス ログ レート制限のような[リソース制限への到達](resource-limits.md#service-tier-characteristics)など、違いの根本原因を明らかにしてみます。違いを引き起こす可能性のあるリソースの制限がない場合は、データベースの互換性レベルを変更したり、レガシ カーディナリティの推定などのデータベース設定を変更して、テストを再度開始します。 マネージド インスタンスまたはクエリ ストア ビューによって提供されるレコメンデーションを確認し、パフォーマンスを低下させたクエリを識別します。

> [!IMPORTANT]
> Azure SQL Managed Instance には、既定で有効になる組み込みの自動プラン修正機能があります。 この機能を使用すると、過去に正常に機能していたクエリのパフォーマンスが将来も低下しないことが保証されます。 この機能が有効になっていること、およびマネージド インスタンスがベースライン パフォーマンスとプランについて学習できるよう、新しい設定に変更する前に、古い設定で十分な時間ワークロードを実行したことを確認します。

ニーズに合ったワークロードのパフォーマンスが得られるまで、パラメーターを変更するか、サービス レベルをアップグレードして、最適な構成に近付けます。

### <a name="monitor-performance"></a>パフォーマンスの監視

SQL Managed Instance には、監視とトラブルシューティングのための高度なツールが多数用意されており、それらを使用してインスタンスのパフォーマンスを監視する必要があります。 監視が必要なパラメーターには、次のようなものがあります。

- プロビジョニングした仮想コアの数がワークロードに適したものであるかどうかを判断するための、インスタンスの CPU 使用率。
- [メモリの追加が必要かどうか](https://techcommunity.microsoft.com/t5/Azure-SQL-Database/Do-you-need-more-memory-on-Azure-SQL-Managed-Instance/ba-p/563444)を判断するための、マネージド インスタンスのページの予測保持期間。
- ストレージ IO の問題が発生しているかどうかを示す `INSTANCE_LOG_GOVERNOR` や `PAGEIOLATCH` などの統計。特に、IO パフォーマンスを向上させるためにファイルの事前割り当てが必要になる可能性がある General Purpose レベルで当てはまります。

## <a name="leverage-advanced-paas-features"></a>高度な PaaS 機能を活用する

フル マネージド プラットフォームに移行して、ワークロードのパフォーマンスが SQL Server のワークロードのパフォーマンスに匹敵することを確認した後、サービスの一部として自動的に提供されるものを利用します。

移行の間にマネージド インスタンスで何も変更しない場合でも、インスタンスを運用するときに新しい機能を有効にして、最新のデータベース エンジンの機能強化を利用する機会があります。 一部の変更は[データベースの互換性レベルを変更した](/sql/relational-databases/databases/view-or-change-the-compatibility-level-of-a-database)後でのみ有効になります。

たとえば、マネージド インスタンスでバックアップを作成する必要はありません。サービスによってバックアップが自動的に実行されます。 バックアップのスケジュール設定、取得、管理について心配する必要はなくなります。 SQL Managed Instance では、[特定の時点への復旧 (PITR)](../database/recovery-using-backups.md#point-in-time-restore) を使用して、この保有期間内の任意の時点に復元する機能が提供されます。 さらに、[高可用性](../database/high-availability-sla.md)が組み込まれているため、高可用性の設定について心配する必要はありません。

セキュリティを強化するには、[Azure Active Directory 認証](../database/security-overview.md)、[監査](auditing-configure.md)、[脅威の検出](../database/azure-defender-for-sql.md)、[行レベル セキュリティ](/sql/relational-databases/security/row-level-security)、[動的データ マスク](/sql/relational-databases/security/dynamic-data-masking)の使用を検討します。

高度な管理とセキュリティの機能に加えて、マネージド インスタンスでは[ワークロードの監視とチューニング](../database/monitor-tune-overview.md)に役立つ高度なツールのセットが提供されます。 [Azure SQL Analytics](../../azure-monitor/insights/azure-sql.md) では、マネージド インスタンスの大規模なセットを監視し、多数のインスタンスとデータベースの監視を一元化することができます。 マネージド インスタンスの[自動チューニング](/sql/relational-databases/automatic-tuning/automatic-tuning#automatic-plan-correction)では、SQL プランの実行統計のパフォーマンスが継続的に監視されて、識別されたパフォーマンスの問題が自動的に修正されます。

## <a name="next-steps"></a>次のステップ

- マネージド インスタンスについては、「[Azure SQL Managed Instance とは](sql-managed-instance-paas-overview.md)」をご覧ください。
- バックアップからの復元を含むチュートリアルについては、[マネージド インスタンスの作成](instance-create-quickstart.md)に関するページを参照してください。
- Database Migration Service を使用した移行を示すチュートリアルについては、[Database Migration Service を使用したオンプレミス データベースの Azure SQL Managed Instance への移行](../../dms/tutorial-sql-server-to-managed-instance.md)に関するページをご覧ください。