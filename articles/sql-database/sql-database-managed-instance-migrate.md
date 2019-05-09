---
title: SQL Server インスタンスを Azure SQL Database Managed Instance に移行する | Microsoft Docs
description: SQL Server インスタンスを Azure SQL Database Managed Instance に移行する方法について説明します。
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
ms.openlocfilehash: 1460b595e8887fc932d5be335ae51b07a000b9fb
ms.sourcegitcommit: 39397603c8534d3d0623ae4efbeca153df8ed791
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/12/2019
ms.locfileid: "56098359"
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

## <a name="deploy-to-an-optimally-sized-managed-instance"></a>最適なサイズに設定されたマネージド インスタンスにデプロイする

マネージド インスタンスは、クラウドへの移行を予定しているオンプレミスのワークロード向けに調整されます。 ワークロードのリソースの適切なレベルの選択において高い柔軟性を発揮する[新しい購入モデル](sql-database-service-tiers-vcore.md)が導入されます。 オンプレミスの世界では、物理コアと IO 帯域幅を使用して、これらのワークロードのサイズを設定することがおそらく一般的です。 マネージド インスタンスの購入モデルは仮想コア ("vCore") に基づいており、追加のストレージと IO を個別に使用できます。 仮想コア モデルは、クラウドのコンピューティング要件と現在オンプレミスで使用しているものを把握するためのシンプルな方法です。 この新しいモデルにより、クラウド内の移行先環境を適切にサイズ設定することができます。

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

移行後のアプリケーションの動作とパフォーマンスを追跡します。 マネージド インスタンスでは、一部の変更は[データベースの互換性レベルが変更された](https://docs.microsoft.com/sql/relational-databases/databases/view-or-change-the-compatibility-level-of-a-database)後でのみ有効になります。 Azure SQL Database へのデータベースの移行では、ほとんどの場合に元の互換性レベルが保持されます。 移行前のユーザー データベースの互換性レベルが 100 以上の場合は、移行後も同じままになります。 アップグレードされたデータベースで、ユーザー データベースの互換性レベルが移行の前に 90 であった場合、その互換性レベルは、マネージド インスタンスでサポートされる最も低い互換性レベルである 100 に設定されます。 システム データベースの互換性レベルは 140 です。

移行に伴うリスクを軽減するには、パフォーマンスの監視後にのみ、データベース互換性レベルを変更します。 「[新しい SQL Server にアップグレードするときにパフォーマンスの安定性を維持する](https://docs.microsoft.com/sql/relational-databases/performance/query-store-usage-scenarios#CEUpgrade)」の説明に従って、データベースの互換性レベルの変更前と変更後のワークロードのパフォーマンスに関する情報を取得するための最適なツールとしてクエリ ストアを使用します。

フル マネージドのプラットフォームで、SQL Database サービスの一部として自動的に提供される利点を享受できます。 たとえば、マネージド インスタンスでバックアップを作成する必要はありません。サービスによってバックアップが自動的に実行されます。 バックアップのスケジュール設定、取得、管理について心配する必要はなくなります。 マネージド インスタンスでは、[特定の時点への復旧 (PITR)](sql-database-recovery-using-backups.md#point-in-time-restore) を使用して、この保有期間内の任意の時点に復元する機能が提供されます。 さらに、[高可用性](sql-database-high-availability.md)が組み込まれているため、高可用性の設定について心配する必要はありません。

セキュリティを強化するために、利用可能ないくつかの機能の使用を検討してください。

- データベース レベルでの Azure Active Directory 認証
- [監査](sql-database-managed-instance-auditing.md)、[脅威の検出](sql-database-advanced-data-security.md)、[行レベルのセキュリティ](https://docs.microsoft.com/sql/relational-databases/security/row-level-security)、[動的データ マスク](https://docs.microsoft.com/sql/relational-databases/security/dynamic-data-masking)などの[高度なセキュリティ機能](sql-database-security-overview.md)を使用して、インスタンスをセキュリティで保護します。

## <a name="next-steps"></a>次の手順

- マネージド インスタンスについては、[マネージド インスタンス](sql-database-managed-instance.md)に関するページを参照してください。
- バックアップからの復元を含むチュートリアルについては、[マネージド インスタンスの作成](sql-database-managed-instance-get-started.md)に関するページを参照してください。
- DMS を使用した移行を示すチュートリアルについては、[DMS を使用したオンプレミス データベースのマネージド インスタンスへの移行](../dms/tutorial-sql-server-to-managed-instance.md)に関するページを参照してください。  
