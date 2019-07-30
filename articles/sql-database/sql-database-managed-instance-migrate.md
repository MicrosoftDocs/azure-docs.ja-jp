---
title: SQL Server インスタンスから Azure SQL Database マネージド インスタンスにデータベースを移行する | Microsoft Docs
description: SQL Server インスタンスから Azure SQL Database マネージド インスタンスにデータベースを移行する方法を説明します。
services: sql-database
ms.service: sql-database
ms.subservice: migration
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: bonova
ms.author: bonova
ms.reviewer: douglas, carlrab
manager: craigg
ms.date: 02/11/2019
ms.openlocfilehash: 9fe6ab797eaa325ad802702e95f5a0e5b8e4fef4
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/13/2019
ms.locfileid: "67070422"
---
# <a name="sql-server-instance-migration-to-azure-sql-database-managed-instance"></a>Azure SQL Database Managed Instance への SQL Server インスタンスの移行

この記事では、SQL Server 2005 以降のバージョンのインスタンスを [Azure SQL Database Managed Instance](sql-database-managed-instance.md) に移行する方法について説明します。 単一データベースまたはエラスティック プールへの移行については、「[単一データベースまたはプールされたデータベースへの移行する](sql-database-cloud-migrate.md)」を参照してください。 他のプラットフォームからの移行に関する移行の情報については、[Azure データベース移行ガイド](https://datamigration.microsoft.com/)を参照してください。

大まかには、データベースの移行プロセスは次のようになります。

![移行プロセス](./media/sql-database-managed-instance-migration/migration-process.png)

- [マネージド インスタンスの互換性を評価する](#assess-managed-instance-compatibility)
- [アプリの接続性オプションの選択](sql-database-managed-instance-connect-app.md)
- [最適なサイズに設定されたマネージド インスタンスにデプロイする](#deploy-to-an-optimally-sized-managed-instance)
- [移行方法の選択と移行](#select-migration-method-and-migrate)
- [アプリケーションの監視](#monitor-applications)

> [!NOTE]
> 個々のデータベースを単一データベースまたはエラスティック プールに移行するには、[Azure SQL Database への SQL Server データベースの移行](sql-database-single-database-migrate.md)に関する記事をご覧ください。

## <a name="assess-managed-instance-compatibility"></a>マネージド インスタンスの互換性を評価する

まず、マネージド インスタンスがアプリケーションのデータベース要件と互換性があるかどうかを判定します。 マネージド インスタンスのデプロイ オプションは、オンプレミスまたは仮想マシン上で SQL Server を使用するほとんどの既存のアプリケーションに簡単なリフト アンド シフト移行を提供するように設計されています。 ただし、まだサポートされていない機能が必要で、回避策を実装するコストが高すぎる場合があります。

[Data Migration Assistant (DMA)](https://docs.microsoft.com/sql/dma/dma-overview) を使用して、Azure SQL Database のデータベース機能に影響を与える可能性のある互換性の問題を検出します。 DMA はまだ移行先としてマネージド インスタンスをサポートしていませんが、Azure SQL Database に対して評価を実行し、報告された機能パリティと互換性の問題の一覧を製品ドキュメントに対して慎重に確認することをお勧めします。 Azure SQL Database への移行を妨げる、障害となっている問題はほとんどマネージド インスタンスで取り除かれているため、[Azure SQL Database の機能](sql-database-features.md)に関するページを参照して、マネージド インスタンスの問題ではない障害となっている問題がいくつか報告されているかどうかを確認してください。 たとえば、データベース間のクエリ、同じインスタンス内のデータベース間のトランザクション、他の SQL ソースへのリンク サーバー、CLR、グローバル一時テーブル、インスタンス レベルのビュー、Service Broker などの機能はマネージド インスタンスで使用できます。

マネージド インスタンスのデプロイ オプションで取り除かれていない障害となっている問題がいくつか報告されている場合は、[Azure 仮想マシン上の SQL Server](https://azure.microsoft.com/services/virtual-machines/sql-server/) などの代替オプションを考慮することが必要になる可能性があります。 次に例をいくつか示します。

- オペレーティング システムやファイル システムへの直接アクセスが必要な場合、たとえばサード パーティ製のエージェントやカスタム エージェントを SQL Server と同じ仮想マシンにインストールする場合。
- FileStream/FileTable、PolyBase、クロス インスタンス トランザクションなど、まだサポートされていない機能に対する厳密な依存関係がある場合。
- 特定バージョンの SQL Server (たとえば 2012 など) を確実に維持する必要がある場合。
- コンピューティング要件がマネージド インスタンスによって提供されるものよりはるかに低く (1 つの vCore など)、データベース統合が許容可能なオプションではない場合。

すべての識別された移行阻害要因を解決し、マネージド インスタンスへの移行を続行する場合、変更の一部によってワークロードのパフォーマンスが影響を受ける可能性があることに注意してください。
- 定期的に単純/一括ログ モデルを使っている場合、またはオンデマンドでバックアップを停止している場合、必須の完全復旧モデルと定期的な自動バックアップ スケジュールでは、ワークロードまたはメンテナンス/ETL 操作のパフォーマンスに影響する可能性があります。
- トレース フラグや互換性レベルなど、サーバー レベルまたはデータベース レベルの異なる構成
- Transparent Database Encryption (TDE) や自動フェールオーバー グループなどの新しい機能を使うと、CPU と IO の使用率が影響を受ける可能性があります。

マネージド インスタンスでは、重要なシナリオであっても 99.99% の可用性が保証されるので、これらの機能によるオーバーヘッドを無効にすることはできません。 詳しくは、[the root causes that might cause different performance on SQL Server and Managed Instance (SQL Server と、マネージド インスタンスでパフォーマンスが異なる原因)](https://azure.microsoft.com/blog/key-causes-of-performance-differences-between-sql-managed-instance-and-sql-server/) に関するページをご覧ください。

### <a name="create-performance-baseline"></a>パフォーマンスのベースラインを作成する

マネージド インスタンスでのワークロードのパフォーマンスと SQL Server で実行されている元のワークロードのパフォーマンスを比較する必要がある場合は、比較に使われるパフォーマンス ベースラインを作成する必要があります。 SQL Server インスタンスで測定する必要のあるパラメーターの一部を次に示します。 
- [SQL Server インスタンスでの CPU 使用率を監視](https://techcommunity.microsoft.com/t5/Azure-SQL-Database/Monitor-CPU-usage-on-SQL-Server/ba-p/680777#M131)し、平均とピークの CPU 使用率を記録します。
- [SQL Server インスタンスでのメモリ使用量を監視](https://docs.microsoft.com/sql/relational-databases/performance-monitor/monitor-memory-usage)し、バッファー プール、プラン キャッシュ、列ストア プール、[インメモリ OLTP](https://docs.microsoft.com/sql/relational-databases/in-memory-oltp/monitor-and-troubleshoot-memory-usage?view=sql-server-2017) などのさまざまなコンポーネントで使用されるメモリの量を明らかにします。さらに、ページの予測保持期間メモリ パフォーマンス カウンターの平均値とピーク値を調べる必要があります。
- [sys.dm_io_virtual_file_stats](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-io-virtual-file-stats-transact-sql) ビューまたは[パフォーマンス カウンター](https://docs.microsoft.com/sql/relational-databases/performance-monitor/monitor-disk-usage)を使って、ソース SQL Server インスタンスでのディスク IO 使用率を監視ます。
- SQL Server 2016 以降のバージョンから移行する場合は、動的管理ビューまたはクエリ ストアを調べることで、ワークロードとクエリのパフォーマンスまたは SQL Server インスタンスを監視します。 ワークロードで最も重要なクエリの平均継続時間と CPU 使用率を特定し、マネージド インスタンスで実行されているクエリと比較します。

> [!Note]
> 高い CPU 使用率、メモリの常時不足、tempdb またはパラメーター化の問題など、SQL Server のワークロードで何らかの問題が検出された場合は、ベースラインを取得して移行を実行する前に、ソース SQL Server インスタンスでそれらを解決する必要があります。 既知の問題が新しいシステムに移行されると、予期しない結果が発生し、パフォーマンスの比較が無効になる可能性があります。

このアクティビティの結果として、ソース システムでの CPU、メモリ、IO の使用率のピーク値と平均値、およびワークロードでの主要なクエリと最も重要なクエリの平均と最大の継続時間および CPU 使用率を、文書化する必要があります。 後でこれらの値を使って、マネージド インスタンスでのワークロードのパフォーマンスを、ソース SQL Server でのワークロードのベースライン パフォーマンスと比較する必要があります。

## <a name="deploy-to-an-optimally-sized-managed-instance"></a>最適なサイズに設定されたマネージド インスタンスにデプロイする

マネージド インスタンスは、クラウドへの移行を予定しているオンプレミスのワークロード向けに調整されます。 ワークロードのリソースの適切なレベルの選択において高い柔軟性を発揮する[新しい購入モデル](sql-database-service-tiers-vcore.md)が導入されます。 オンプレミスの世界では、物理コアと IO 帯域幅を使用して、これらのワークロードのサイズを設定することがおそらく一般的です。 マネージド インスタンスの購入モデルは仮想コア ("vCore") に基づいており、追加のストレージと IO を個別に使用できます。 仮想コア モデルは、クラウドのコンピューティング要件と現在オンプレミスで使用しているものを把握するためのシンプルな方法です。 この新しいモデルにより、クラウド内の移行先環境を適切にサイズ設定することができます。 適切なサービス レベルと特性を選択するために役立ついくつかの一般的なガイドラインを次に示します。
- [SQL Server インスタンスでの CPU 使用率を監視](https://techcommunity.microsoft.com/t5/Azure-SQL-Database/Monitor-CPU-usage-on-SQL-Server/ba-p/680777#M131)し、現在使っているコンピューティング能力を確認します (動的管理ビュー、SQL Server Management Studio、または他の監視ツールを使用)。 SQL Server で使っているコアの数と一致するマネージド インスタンスをプロビジョニングします。そのとき、[マネージド インスタンスがインストールされている VM の特性](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-resource-limits#hardware-generation-characteristics)と一致するように CPU の特性をスケーリングすることが必要になる場合があることに留意します。
- SQL Server インスタンスで使用可能なメモリの量を確認し、[対応するメモリを備えたサービス レベル](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-resource-limits#hardware-generation-characteristics)を選択します。 SQL Server インスタンスでページの予測保持期間を測定して[メモリの追加が必要かどうか](https://techcommunity.microsoft.com/t5/Azure-SQL-Database/Do-you-need-more-memory-on-Azure-SQL-Managed-Instance/ba-p/563444)を判断すると役に立ちます。
- ファイル サブシステムの IO 待機時間を測定して、General Purpose サービス レベルまたは Business Critical サービス レベルを選択します。

コンピューティング リソースとストレージ リソースをデプロイ時に選択し、後で [Azure portal](sql-database-scale-resources.md) を使用してアプリケーションのダウンタイムなしに変更できます。

![マネージド インスタンスのサイズ設定](./media/sql-database-managed-instance-migration/managed-instance-sizing.png)

VNet インフラストラクチャとマネージド インスタンスを作成する方法については、[マネージド インスタンスの作成](sql-database-managed-instance-get-started.md)に関するページを参照してください。

> [!IMPORTANT]
> 移行先の VNet とサブネットが常に[マネージド インスタンスの VNet 要件](sql-database-managed-instance-connectivity-architecture.md#network-requirements)に従っているようにすることが重要です。 非互換性があると、新しいインスタンスの作成や、既に作成したインスタンスの使用ができなくなることがあります。 詳しくは、[ネットワークの新規作成](sql-database-managed-instance-create-vnet-subnet.md)と[既存のネットワークの構成](sql-database-managed-instance-configure-vnet-subnet.md)に関する記事をご覧ください。

## <a name="select-migration-method-and-migrate"></a>移行方法の選択と移行

マネージド インスタンスのデプロイ オプションは、オンプレミスまたは IaaS データベース実装からの大量のデータベース移行が必要なユーザー シナリオを対象にしています。 これらは、インスタンス レベルの機能や複数データベース間の機能を定期的に使用するアプリケーションのバックエンドのリフト アンド シフトが必要な場合に最適な選択肢です。 このシナリオに該当する場合は、アプリケーションを再設計する必要なしに Azure 内の対応する環境にインスタンスを移行できます。

SQL インスタンスを移行するには、以下を慎重に計画する必要があります。

- 併置する必要のある (同じインスタンスで実行されている) すべてのデータベースの移行
- ログイン、資格情報、SQL エージェント ジョブと演算子、サーバー レベルのトリガーなど、アプリケーションが依存するインスタンス レベルのオブジェクトの移行。

マネージド インスタンスは、通常の DBA アクティビティの一部を組み込み時にユーザーがプラットフォームに委任できるマネージド サービスです。 したがって、[高可用性](sql-database-high-availability.md)が組み込まれているため、定期的なバックアップや Always On 構成のメンテナンス ジョブなど、いくつかのインスタンス レベルのデータは移行する必要がありません。

マネージド インスタンスでは、次のデータベース移行オプションがサポートされます (現在は、これらの移行方法のみがサポートされます)。

- Azure Database Migration Service - ほぼダウンタイムがない移行。
- ネイティブな `RESTORE DATABASE FROM URL` - SQL Server のネイティブ バックアップを使用し、ある程度のダウンタイムが必要。

### <a name="azure-database-migration-service"></a>Azure Database Migration Service

[Azure Database Migration Service (DMS)](../dms/dms-overview.md) は、複数のデータベース ソースから Azure データ プラットフォームへのシームレスな移行を最小限のダウンタイムで実現できるように設計された、フル マネージドのサービスです。 このサービスは、既存のサード パーティ製データベースと SQL Server データベースを Azure に移行するために必要なタスクを効率化します。 パブリック プレビューでのデプロイ オプションには、Azure SQL Database 内のデータベースおよび Azure 仮想マシン内の SQL Server データベースが含まれます。 DMS は、エンタープライズ ワークロードの移行に推奨される方法です。

オンプレミスの SQL Server で SQL Server Integration Services (SSIS) を使用する場合、DMS はまだ SSIS パッケージを格納する SSIS カタログ (SSISDB) の移行をサポートしていませんが、Azure Data Factory (ADF) でマネージド インスタンスに新しい SSISDB を作成する Azure-SSIS 統合ランタイム (IR) をプロビジョニングしてから、そこにパッケージを再デプロイすることができます。[ADF での Azure-SSIS IR の作成](https://docs.microsoft.com/azure/data-factory/create-azure-ssis-integration-runtime)に関するページを参照してください。

このシナリオおよび DMS での構成手順の詳細については、[DMS を使用したオンプレミス データベースのマネージド インスタンスへの移行](../dms/tutorial-sql-server-to-managed-instance.md)に関するページを参照してください。  

### <a name="native-restore-from-url"></a>URL からのネイティブ復元

[Azure Storage](https://azure.microsoft.com/services/storage/) で使用可能なオンプレミスの SQL Server または [SQL Server on Virtual Machines](https://azure.microsoft.com/services/virtual-machines/sql-server/) から取得されたネイティブ バックアップ (.bak ファイル) の復元は、マネージド インスタンスのデプロイ オプションの主な機能の 1 つで、オフラインのデータベース移行を迅速かつ簡単に行うことができます。

次の図は、プロセスの概要を示しています。

![移行フロー](./media/sql-database-managed-instance-migration/migration-flow.png)

次の表は、実行しているソース SQL Server のバージョンに応じて使用できる方法に関する詳細情報を示しています。

|手順|SQL エンジンとバージョン|バックアップ/復元方法|
|---|---|---|
|Azure Storage へのバックアップの格納|SQL 2012 SP1 CU2 より前|Azure Storage に .bak ファイルを直接アップロードする|
||2012 SP1 CU2 - 2016|非推奨の [WITH CREDENTIAL](https://docs.microsoft.com/sql/t-sql/statements/restore-statements-transact-sql) 構文を使用して直接バックアップする|
||2016 以上|[WITH SAS CREDENTIAL](https://docs.microsoft.com/sql/relational-databases/backup-restore/sql-server-backup-to-url) を使用して直接バックアップする|
|Azure Storage からマネージド インスタンスに復元する|[SAS 資格情報での URL からの復元](sql-database-managed-instance-get-started-restore.md)|

> [!IMPORTANT]
> - ネイティブ復元オプションを使用して、[Transparent Data Encryption](transparent-data-encryption-azure-sql.md) によって保護されたデータベースをマネージド インスタンスに移行する場合は、データベースの復元の前に、オンプレミスまたは IaaS の SQL Server の対応する証明書を移行する必要があります。 詳細な手順については、[TDE 証明書のマネージド インスタンスへの移行](sql-database-managed-instance-migrate-tde-certificate.md)に関するページを参照してください。
> - システム データベースの復元はサポートされていません。 (マスターまたは msdb データベースに格納されている) インスタンス レベルのオブジェクトを移行するには、それらのスクリプトを作成し、移行先のインスタンスで T-SQL スクリプトを実行することをお勧めします。

SAS 資格情報を使用してデータベース バックアップをマネージド インスタンスに復元する方法を示すクイック スタートについては、[バックアップからマネージド インスタンスへの復元](sql-database-managed-instance-get-started-restore.md)に関するページを参照してください。

> [!VIDEO https://www.youtube.com/embed/RxWYojo_Y3Q]


## <a name="monitor-applications"></a>アプリケーションの監視

マネージド インスタンスへの移行を完了した後は、アプリケーションの動作とワークロードのパフォーマンスを追跡する必要があります。 このプロセスには、次のアクティビティが含まれます。
- [ソース SQL Server 上で作成したパフォーマンス ベースライン](#create-performance-baseline)と、[マネージド インスタンスで実行されているワークロードのパフォーマンスを比較](#compare-performance-with-the-baseline)します。
- 継続的に[ワークロードのパフォーマンスを監視](#monitor-performance)し、問題と改善の可能性を明らかにします。

### <a name="compare-performance-with-the-baseline"></a>パフォーマンスとベースラインを比較する

移行が成功した後すぐに実行する必要のある最初のアクティビティは、ワークロードのパフォーマンスとベースライン ワークロードのパフォーマンスの比較です。 このアクティビティの目的は、マネージド インスタンスでのワークロードのパフォーマンスがニーズを満たしていることを確認することです。 

マネージド インスタンスへのデータベースの移行では、ほとんどの場合においてデータベースの設定と元の互換性レベルが保持されます。 ソース SQL Server と比較してパフォーマンスが低下するリスクを減らすため、可能な限り元の設定が保持されます。 移行前のユーザー データベースの互換性レベルが 100 以上の場合は、移行後も同じままになります。 アップグレードされたデータベースで、ユーザー データベースの互換性レベルが移行の前に 90 であった場合、その互換性レベルは、マネージド インスタンスでサポートされる最も低い互換性レベルである 100 に設定されます。 システム データベースの互換性レベルは 140 です。 マネージド インスタンスへの移行は、実際には最新の SQL Server データベース エンジンへの移行なので、パフォーマンスの問題に驚かないよう、ワークロードのパフォーマンスの再テストが必要であることに気付くはずです。

前提条件として、次のアクティビティを完了したことを確認します。
- さまざまなインスタンス、データベース、temdb の設定、および構成を調べて、マネージ インスタンスでの設定を、ソース SQL Server インスタンスの設定と一致させます。 最初のパフォーマンス比較を実行する前に互換性レベルや暗号化などの設定が変更されていないことを確認するか、または有効にした新機能によってクエリが影響を受けるリスクを受け入れるます。 移行に伴うリスクを軽減するには、パフォーマンスの監視後にのみ、データベース互換性レベルを変更します。
- 優れたパフォーマンスを実現するためのファイルのサイズの事前割り当てなど、[General Purpose に対するストレージのベスト プラクティスのガイドライン](https://techcommunity.microsoft.com/t5/DataCAT/Storage-performance-best-practices-and-considerations-for-Azure/ba-p/305525)を実装します。
- [マネージド インスタンスと SQL Server でのパフォーマンスの違いを引き起こす可能性のある重要な環境の違い]( https://azure.microsoft.com/blog/key-causes-of-performance-differences-between-sql-managed-instance-and-sql-server/)について学習し、パフォーマンスに影響する可能性のあるリスクを明らかにします。
- マネージド インスタンスでクエリ ストアと自動チューニングが有効になっていることを確認します。 これらの機能を使用すると、ワークロードのパフォーマンスを測定し、可能性があるパフォーマンスの問題を自動的に修正できます。 「[新しい SQL Server にアップグレードするときにパフォーマンスの安定性を維持する](https://docs.microsoft.com/sql/relational-databases/performance/query-store-usage-scenarios#CEUpgrade)」の説明に従って、データベースの互換性レベルの変更前と変更後のワークロードのパフォーマンスに関する情報を取得するための最適なツールとしてクエリ ストアを使用する方法を学習します。
オンプレミスの環境と可能な限り一致するように環境を準備した後は、ワークロードの実行を開始してパフォーマンスを測定できます。 測定プロセスには、[ソース SQL Server でワークロード測定のベースライン パフォーマンスを作成する](#create-performance-baseline)ときに測定したものと同じパラメーターを含める必要があります。
結果として、パフォーマンス パラメーターをベースラインと比較し、重要な違いを特定する必要があります。

> [!NOTE]
> 多くの場合、マネージド インスタンスと SQL Server で完全に一致するパフォーマンスを得ることはできません。 マネージド インスタンスは SQL Server データベース エンジンですが、マネージド インスタンスでのインフラストラクチャと高可用性の構成により、いくつかの相違がもたらされる可能性があります。 速くなるクエリもあれば、遅くなるクエリもあります。 比較の目的は、マネージド インスタンスでのワークロードのパフォーマンスが SQL Server でのパフォーマンスと (平均で) 一致することを確認し、重要なクエリの中にパフォーマンスが元のパフォーマンスに匹敵しないものがあるかどうかを明らかにすることです。

パフォーマンスの比較の結果は次のようになります。
- マネージド インスタンスでのワークロードのパフォーマンスが、SQL Server でのワークロードのパフォーマンと同等かそれより優れています。 この場合、移行が成功したことを正常に確認しました。
- ワークロードのパフォーマンス パラメーターとクエリの多くは問題なく動作していますが、一部の例外でパフォーマンスが低下しています。 この場合、相違と重要度を特定する必要があります。 重要なクエリでパフォーマンスが低下したものがある場合は、基になる SQL プランが変更されたか、またはクエリで何らかのリソースが制限に達しているかを調べる必要があります。 この場合の軽減策としては、重要なクエリ (変更された互換性レベル、レガシ カーディナリティ推定機能など) に対して直接的に、またはプラン ガイドを使ってヒントを適用するか、プランに影響を与える可能性がある統計情報とインデックスを再構築または作成します。 
- ほとんどのクエリは、ソース SQL Server よりマネージド インスタンスでの方が実行速度が遅くなります。 この場合は、IO 制限、メモリ制限、インスタンス ログ レート制限のような[リソース制限への到達]( sql-database-managed-instance-resource-limits.md#instance-level-resource-limits)など、違いの根本原因を明らかにしてみます。違いを引き起こす可能性のあるリソースの制限がない場合は、データベースの互換性レベルを変更したり、レガシ カーディナリティの推定などのデータベース設定を変更して、テストを再度開始します。 マネージド インスタンスまたはクエリ ストア ビューによって提供されるレコメンデーションを確認し、パフォーマンスを低下させたクエリを識別します。

> [!IMPORTANT]
> マネージド インスタンスには、既定で有効になる組み込みの自動プラン修正機能があります。 この機能を使用すると、過去に正常に機能していたクエリのパフォーマンスが将来も低下しないことが保証されます。 この機能が有効になっていること、およびマネージド インスタンスがベースライン パフォーマンスとプランについて学習できるよう、新しい設定に変更する前に、古い設定で十分な時間ワークロードを実行したことを確認します。

ニーズに合ったワークロードのパフォーマンスが得られるまで、パラメーターを変更するか、サービス レベルをアップグレードして、最適な構成に近付けます。

### <a name="monitor-performance"></a>パフォーマンスの監視

フル マネージド プラットフォームに移行して、ワークロードのパフォーマンスが SQL Server のワークロードのパフォーマンスに匹敵することを確認した後、SQL Database サービスの一部として自動的に提供されるものを利用します。 

移行の間にマネージド インスタンスで何も変更しない場合でも、インスタンスを運用するときに新しい機能を有効にして、最新のデータベース エンジンの機能強化を利用する機会があります。 一部の変更は[データベースの互換性レベルを変更した](https://docs.microsoft.com/sql/relational-databases/databases/view-or-change-the-compatibility-level-of-a-database)後でのみ有効になります。


たとえば、マネージド インスタンスでバックアップを作成する必要はありません。サービスによってバックアップが自動的に実行されます。 バックアップのスケジュール設定、取得、管理について心配する必要はなくなります。 マネージド インスタンスでは、[特定の時点への復旧 (PITR)](sql-database-recovery-using-backups.md#point-in-time-restore) を使用して、この保有期間内の任意の時点に復元する機能が提供されます。 さらに、[高可用性](sql-database-high-availability.md)が組み込まれているため、高可用性の設定について心配する必要はありません。

セキュリティを強化するには、[Azure Active Directory 認証](sql-database-security-overview.md)、[監査](sql-database-managed-instance-auditing.md)、[脅威の検出](sql-database-advanced-data-security.md)、[行レベル セキュリティ](https://docs.microsoft.com/sql/relational-databases/security/row-level-security)、[動的データ マスク](https://docs.microsoft.com/sql/relational-databases/security/dynamic-data-masking)の使用を検討します。

高度な管理とセキュリティの機能に加えて、マネージド インスタンスでは[ワークロードの監視とチューニング](sql-database-monitor-tune-overview.md)に役立つ高度なツールのセットが提供されます。 [Azure SQL Analytics](https://docs.microsoft.com/azure/azure-monitor/insights/azure-sql) では、マネージド インスタンスの大規模なセットを監視し、多数のインスタンスとデータベースの監視を一元化することができます。 マネージド インスタンスの[自動チューニング](https://docs.microsoft.com/sql/relational-databases/automatic-tuning/automatic-tuning#automatic-plan-correction)では、SQL プランの実行統計のパフォーマンスが継続的に監視されて、識別されたパフォーマンスの問題が自動的に修正されます。

## <a name="next-steps"></a>次の手順

- マネージド インスタンスについては、[マネージド インスタンス](sql-database-managed-instance.md)に関するページを参照してください。
- バックアップからの復元を含むチュートリアルについては、[マネージド インスタンスの作成](sql-database-managed-instance-get-started.md)に関するページを参照してください。
- DMS を使用した移行を示すチュートリアルについては、[DMS を使用したオンプレミス データベースのマネージド インスタンスへの移行](../dms/tutorial-sql-server-to-managed-instance.md)に関するページを参照してください。  
