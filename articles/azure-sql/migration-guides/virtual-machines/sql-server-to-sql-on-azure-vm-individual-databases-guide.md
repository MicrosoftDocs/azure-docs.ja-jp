---
title: 'SQL Server から Azure VM 上の SQL Server へ: 移行ガイド'
description: このガイドでは、個々の SQL Server データベースを Azure VM 上の SQL Server に移行する方法について説明します。
ms.custom: ''
ms.service: virtual-machines-sql
ms.subservice: migration-guide
ms.devlang: ''
ms.topic: how-to
author: markjones-msft
ms.author: markjon
ms.reviewer: mathoma
ms.date: 03/19/2021
ms.openlocfilehash: 0e1b44667a5ff42978b22ab9450d6a8e9870960b
ms.sourcegitcommit: 18a91f7fe1432ee09efafd5bd29a181e038cee05
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/16/2021
ms.locfileid: "103563217"
---
# <a name="migration-guide-sql-server-to-sql-server-on-azure-vms"></a>移行ガイド:SQL Server から Azure VM 上の SQL Server 
[!INCLUDE[appliesto--sqlmi](../../includes/appliesto-sqlvm.md)]

この移行ガイドでは、ユーザー データベースの **検出** と **評価** を行い、バックアップ、復元、ログ配布を使用して SQL Server から Azure Virtual Machines (VM) 上の SQL Server インスタンスに **移行** する方法について説明します。評価には、[Database Migration Assistant (DMA)](/sql/dma/dma-overview) を利用します。 

オンプレミスまたは次で動作している SQL Server を移行できます。

- SQL Server on Virtual Machines  
- アマゾン ウェブ サービス (AWS) EC2 
- Amazon Relational Database Service (AWS RDS) 
- Compute Engine (Google Cloud Platform - GCP)

その他の移行戦略の詳細については、[SQL Server VM 移行の概要](sql-server-to-sql-on-azure-vm-migration-overview.md)に関するページを参照してください。

:::image type="content" source="media/sql-server-to-sql-on-azure-vm-migration-overview/migration-process-flow-small.png" alt-text="移行プロセスのフロー":::

## <a name="prerequisites"></a>前提条件

Azure VM 上の SQL Server への移行には、次が必要です。 

- [Database Migration Assistant (DMA)](https://www.microsoft.com/download/details.aspx?id=53595)。
- [Azure Migrate プロジェクト](../../../migrate/create-manage-projects.md)。
- [Azure VM 上のターゲット SQL Server](../../virtual-machines/windows/create-sql-vm-portal.md) の準備 (バージョンはソース SQL Server と同じかそれ以上)。
- [Azure とオンプレミスの間の接続](/azure/architecture/reference-architectures/hybrid-networking)。
- [適切な移行戦略の選択](sql-server-to-sql-on-azure-vm-migration-overview.md#migrate)。

## <a name="pre-migration"></a>移行前

移行を開始する前に、自分の SQL 環境のトポロジを検出し、目的とする移行の実現可能性を評価します。 

### <a name="discover"></a>発見

Azure Migrate では、オンプレミス コンピューターの移行適合性を評価し、パフォーマンスに基づくサイズ設定を行い、オンプレミスでの実行にかかるコストを見積もります。 移行の計画を立てるために、Azure Migrate を使って、自分の SQL Server インスタンスで使用される[既存のデータ ソースと機能の詳細を特定](../../../migrate/concepts-assessment-calculation.md)します。 このプロセスには、ネットワークをスキャンし、組織内のすべての SQL Server インスタンス、さらにバージョンと使用中の機能を特定することが含まれます。 

> [!IMPORTANT]
> SQL Server インスタンスのターゲット Azure 仮想マシンを選択する際は、[Azure VM 上の SQL Server のパフォーマンス ガイドライン](../../virtual-machines/windows/performance-guidelines-best-practices.md)を考慮するようにしてください。

その他の検出ツールについては、「データ移行のシナリオで利用できる[サービスとツール](../../../dms/dms-tools-matrix.md#business-justification-phase)」を参照してください。


### <a name="assess"></a>アクセス

[!INCLUDE [assess-estate-with-azure-migrate](../../../../includes/azure-migrate-to-assess-sql-data-estate.md)]

データ ソースをすべて検出した後、[Data Migration Assistant (DMA)](/sql/dma/dma-overview) を使用して、オンプレミスの SQL Server インスタンスから Azure VM 上の SQL Server インスタンスへの移行を評価します。これにより、ソースとターゲットのインスタンス間のギャップを把握できます。 


> [!NOTE]
> SQL Server のバージョンをアップグレード "_しない_" 場合は、この手順をスキップして「[移行](#migrate)」セクションに進みます。 


#### <a name="assess-user-databases"></a>ユーザー データベースへのアクセス 

Data Migration Assistant (DMA) は、新しいバージョンの SQL Server のデータベース機能に影響するおそれがある互換性の問題を検出することで、最新のデータ プラットフォームへの移行を支援します。 DMA では、ターゲット環境のパフォーマンスと信頼性を高めるための推奨事項が得られるほか、ソース サーバーからターゲット サーバーにスキーマ、データ、ログイン オブジェクトを移動できます。

詳細については、[評価](/sql/dma/dma-migrateonpremsql)に関するページを参照してください。 


> [!IMPORTANT]
>評価の種類によっては、ソース SQL Server で必要となるアクセス許可が異なる場合があります。 
   > - **機能パリティ** アドバイザーの場合、ソース SQL Server データベースに接続するために提供される資格情報は、*sysadmin* サーバー ロールのメンバーである必要があります。
   > - 互換性の問題アドバイザーの場合、提供される資格情報は、少なくとも `CONNECT SQL`、`VIEW SERVER STATE`、`VIEW ANY DEFINITION` のアクセス許可を備えている必要があります。
   > - 評価を実行する前に、選択したアドバイザーに必要なアクセス許可が DMA によって強調表示されます。


#### <a name="assess-applications"></a>アプリケーションの評価

通常、データの保持と変更のためにユーザー データベースにアクセスするのはアプリケーション レイヤーです。  DMA は、アプリケーションのデータ アクセス レイヤーに 2 つの方法でアクセスします。 

- ユーザー データベースの[拡張イベント](/sql/relational-databases/extended-events/extended-events)または [SQL Server Profiler トレース](/sql/tools/sql-server-profiler/create-a-trace-sql-server-profiler)をキャプチャして使用する。 また、[Database Experimentation Assistant](/sql/dea/database-experimentation-assistant-capture-trace) を使用して、A/B テストにも使用できるトレース ログを作成することができます。

- [Data Access Migration Toolkit (プレビュー)](https://marketplace.visualstudio.com/items?itemName=ms-databasemigration.data-access-migration-toolkit) (DAMT) を使用する。これは、コード内の SQL クエリの検出と評価に役立ちます。また、一方から他方のデータベース プラットフォームにアプリケーション ソース コードを移行するために使用されます。 このツールでは、C#、Java、XML、プレーン テキストなど、よく使われるさまざまなファイルの種類がサポートされます。 DAMT の評価を実行する方法のガイドについては、[DAMT の使用](https://techcommunity.microsoft.com/t5/microsoft-data-migration/using-data-migration-assistant-to-assess-an-application-s-data/ba-p/990430)に関するブログを参照してください。

DMA を使用し、ユーザー データベースの評価中にキャプチャされたトレース ファイルまたは DAMT ファイルを[インポート](/sql/dma/dma-assesssqlonprem#add-databases-and-extended-events-trace-to-assess)します。 


#### <a name="scale-assessments"></a>評価のスケーリング

DMA の評価に複数のサーバーが必要な場合は、[コマンド ライン インターフェイス](/sql/dma/dma-commandline)を通じてプロセスを自動化できます。 インターフェイスを使用して、移行の対象となる SQL Server インスタンスごとに評価コマンドを事前に準備できます。 

大規模な資産全体の概要をレポートする場合は、Data Migration Assistant (DMA) 評価を [Azure Migrate に統合](/sql/dma/dma-assess-sql-data-estate-to-sqldb)できるようになっています。

#### <a name="refactor-databases-with-dma"></a>DMA によるデータベースのリファクター

DMA の評価の結果によっては、移行後にユーザー データベースが正常に動作して機能するようにするための一連の推奨事項があります。 DMA から、影響を受けるオブジェクトの詳細と、それぞれの問題を解決する方法のリソースが示されます。 すべての破壊的変更と動作変更は、運用環境への移行の前に解決しておくことをお勧めします。

これらの変更を回避して移行を高速化したい場合には、非推奨の機能について、本来の[互換性](/sql/t-sql/statements/alter-database-transact-sql-compatibility-level)モードでユーザー データベースを実行することを選択できます。 ただし、そうすると、非推奨の項目が解決されるまで[データベース互換性をアップグレード](/sql/database-engine/install-windows/compatibility-certification#compatibility-levels-and-database-engine-upgrades)できなくなります。

[移行後](#post-migration)の期間に DMA の修正プログラムをすべてスクリプト化し、ターゲット SQL Server データベースに適用することを強くお勧めします。

> [!CAUTION]
> すべての SQL Server バージョンで互換性モードがサポートされているわけではありません。 ご自分の[ターゲット SQL Server バージョン](/sql/t-sql/statements/alter-database-transact-sql-compatibility-level)で、選択したデータベース互換性がサポートされていることをご確認ください。 たとえば、SQL Server 2019 では、互換性レベルが 90 (SQL Server 2005) のデータベースはサポートされていません。 これらのデータベースでは、少なくとも互換性レベル 100 へのアップグレードが必要です。
>

## <a name="migrate"></a>移行

移行前の手順を完了したら、ユーザー データベースとコンポーネントを移行する準備が整います。 お望みの[移行方法](sql-server-to-sql-on-azure-vm-migration-overview.md#migrate)を使用して、データベースを移行します。  

以下では、バックアップと復元を使用した移行、またはログ配布と共にバックアップと復元を使用した最小限のダウンタイムでの移行を実行する手順について説明します。 

### <a name="backup-and-restore"></a>バックアップと復元

バックアップと復元を使用した標準の移行を実行するには、これらの手順に従います。 

1. 要件に基づいて、Azure VM 上のターゲット SQL Server への接続を設定します。 「 [Connect to a SQL Server Virtual Machine on Azure (Resource Manager) (Azure での SQL Server 仮想マシンへの接続 (Resource Manager))](../../virtual-machines/windows/ways-to-connect-to-sql.md)」をご覧ください。
1. 移行の対象となるデータベースを使用しているアプリケーションを一時停止または停止します。 
1. [シングル ユーザー モード](/sql/relational-databases/databases/set-a-database-to-single-user-mode)を使用して、ユーザー データベースが非アクティブであることを確認します。 
1. オンプレミスの場所へのデータベースの完全バックアップを実行します。
1. リモート デスクトップ、[Azure Data Explorer](/azure/data-explorer/data-explorer-overview)、または [AzCopy コマンド ライン ユーティリティ](../../../storage/common/storage-use-azcopy-v10.md) (バックアップが 2 TB を超える場合に推奨) を使用して、オンプレミスのバックアップ ファイルを自分の VM にコピーします。
1. データベースの完全バックアップを Azure VM 上の SQL Server に復元します。

### <a name="log-shipping--minimize-downtime"></a>ログ配布 (ダウンタイムの最小化)

バックアップ、復元、ログ配布を使用した最小限のダウンタイムでの移行を実行するには、これらの手順に従います。 

1. 要件に基づいて、Azure VM 上のターゲット SQL Server への接続を設定します。 「 [Connect to a SQL Server Virtual Machine on Azure (Resource Manager) (Azure での SQL Server 仮想マシンへの接続 (Resource Manager))](../../virtual-machines/windows/ways-to-connect-to-sql.md)」をご覧ください。
1. 移行されるオンプレミスのユーザー データベースが、完全または一括ログ復旧モデルであることを確認します。
1. オンプレミスの保存先へのデータベースの完全バックアップを実行します。また、[COPY_ONLY](/sql/relational-databases/backup-restore/copy-only-backups-sql-server) キーワードを使用してログ チェーンを保持するよう、データベースの完全バックアップの既存ジョブを変更します。
1. リモート デスクトップ、[Azure Data Explorer](/azure/data-explorer/data-explorer-overview)、または [AzCopy コマンド ライン ユーティリティ](../../../storage/common/storage-use-azcopy-v10.md) (バックアップが 1 TB を超える場合に推奨) を使用して、オンプレミスのバックアップ ファイルを自分の VM にコピーします。
1. データベースの完全バックアップを Azure VM 上の SQL Server に復元します。
1. オンプレミス データベースと Azure VM 上のターゲット SQL Server の間で[ログ配布](/sql/database-engine/log-shipping/configure-log-shipping-sql-server)を設定します。 前の手順で既に完了しているため、データベースは再初期化しないでください。
1. ターゲット サーバーに **カットオーバー** します。 
   1. 移行対象のデータベースを使用しているアプリケーションを一時停止または停止します。 
   1. [シングル ユーザー モード](/sql/relational-databases/databases/set-a-database-to-single-user-mode)を使用して、ユーザー データベースが非アクティブであることを確認します。 
   1. 準備が整ったら、ログ配布によって[制御されたフェイルオーバー](/sql/database-engine/log-shipping/fail-over-to-a-log-shipping-secondary-sql-server)を実行して、オンプレミスのデータベースを Azure VM 上のターゲット SQL Server に移行します。



### <a name="migrating-objects-outside-user-databases"></a>ユーザー データベース外のオブジェクトの移行

ユーザー データベースの移行後のシームレスな運用には、その他の SQL Server オブジェクトが必要な場合があります。 

次の表では、コンポーネントと (ユーザー データベースの移行の前後に完了できる) 推奨の移行方法の一覧を示します。 


| **機能** | **コンポーネント** | **移行方法** |
| --- | --- | --- |
| **データベース** | モデル  | SQL Server Management Studio でスクリプトを使用します |
|| TempDB | 最善のパフォーマンスが得られるように、[Azure VM 一時ディスク (SSD](../../virtual-machines/windows/performance-guidelines-best-practices.md#temporary-disk)) への tempdb の移行を計画します。 自分の tempdb に合わせて、十分なローカル SSD を備えた VM サイズを選択してください。 |
|| Filestream を使用したユーザー データベース |  [バックアップと復元](../../virtual-machines/windows/migrate-to-vm-from-sql-server.md#back-up-and-restore)の方法を使用して移行します。 DMA では、Filestream を使用したデータベースはサポートされていません。 |
| **Security** | SQL Server と Windows のログイン | DMA を使用して[ユーザー ログインを移行](/sql/dma/dma-migrateserverlogins)します。 |
|| SQL Server ロール | SQL Server Management Studio でスクリプトを使用します |
|| 暗号化プロバイダー | [Azure Key Vault サービスの使用に切り替える](../../virtual-machines/windows/azure-key-vault-integration-configure.md)ことをお勧めします。 この手順では、[SQL VM リソース プロバイダー](../../virtual-machines/windows/sql-agent-extension-manually-register-single-vm.md)を使用します。 |
| **サーバー オブジェクト** | バックアップ デバイス | [Azure Backup サービス](../../../backup/backup-sql-server-database-azure-vms.md)を使用してデータベース バックアップを置換します。または、[Azure Storage](../../virtual-machines/windows/azure-storage-sql-server-backup-restore-use.md) にバックアップを書き込みます (SQL Server 2012 SP1 CU2 +)。 この手順では、[SQL VM リソース プロバイダー](../../virtual-machines/windows/sql-agent-extension-manually-register-single-vm.md)を使用します。|
|| リンク サーバー | SQL Server Management Studio でスクリプトを使用します。 |
|| サーバー トリガー | SQL Server Management Studio でスクリプトを使用します。 |
| **レプリケーション** | ローカル パブリケーション | SQL Server Management Studio でスクリプトを使用します。 |
|| ローカル サブスクライバー | SQL Server Management Studio でスクリプトを使用します。 |
| **Polybase** | PolyBase | SQL Server Management Studio でスクリプトを使用します。 |
| **管理** | データベース メール | SQL Server Management Studio でスクリプトを使用します。 |
| **SQL Server エージェント** | ジョブ | SQL Server Management Studio でスクリプトを使用します。 |
|| 警告 | SQL Server Management Studio でスクリプトを使用します。 |
|| オペレーター | SQL Server Management Studio でスクリプトを使用します。 |
|| プロキシ | SQL Server Management Studio でスクリプトを使用します。 |
| **オペレーティング システム** | ファイル、ファイル共有 | SQL Server で使用されるその他のファイルまたはファイル共有をメモしておいて、Azure VM ターゲットにレプリケートします。 |


## <a name="post-migration"></a>移行後

移行段階が正常に完了したら、移行後の一連のタスクを実行します。それにより、可能な限り円滑かつ効率的にすべてが機能するようにします。

### <a name="remediate-applications"></a>アプリケーションを修復する

データがターゲット環境に移行された後、以前にソースを使用していたすべてのアプリケーションは、ターゲットの使用を開始する必要があります。 これを実現するには、アプリケーションの変更が必要な場合があります。

Database Migration Assistant から推奨される修正プログラムをユーザー データベースに適用します。 一貫性を確保し、自動化を可能にするために、これらはスクリプト化することをお勧めします。

### <a name="perform-tests"></a>テストを実行する

データベース移行のテスト アプローチは、次のアクティビティの実行で構成されています。

1. **検証テストを作成する。**  SQL クエリを使用してデータベースの移行をテストします。 ソースとターゲットの両方のデータベースに対して実行する検証クエリを作成します。 検証クエリには、定義したスコープが含まれている必要があります。
2. **テスト環境を設定する。**  テスト環境には、ソース データベースとターゲット データベースのコピーが含まれている必要があります。 必ずテスト環境を分離してください。
3. **検証テストを実行する。**  ソースとターゲットに対して検証テストを実行してから、結果を分析します。
4. **パフォーマンス テストを実行する。**  ソースとターゲットに対してパフォーマンス テストを実行し、結果を分析して比較します。

> [!TIP]
> ターゲット SQL Server パフォーマンスを評価するには、[Database Experimentation Assistant (DEA)](/sql/dea/database-experimentation-assistant-overview) を使用するのが便利です。


### <a name="optimize"></a>最適化

移行後のフェーズは、データの正確性と完全性の問題を調整したり、ワークロードの潜在的なパフォーマンス問題に対応したりするうえで非常に重要です。

それらの問題とそれらを軽減する具体的な手順の詳細については、次のリソースを参照してください。

- [移行後の検証および最適化ガイド。](/sql/relational-databases/post-migration-validation-and-optimization-guide)
- [Azure SQL Virtual Machines におけるパフォーマンスのチューニング](../../virtual-machines/windows/performance-guidelines-best-practices.md)。
- [Azure コストの最適化センター](https://azure.microsoft.com/overview/cost-optimization/)。

## <a name="next-steps"></a>次のステップ

- SQL Server に適用可能なサービスの可用性を確認するには、[Azure グローバル インフラストラクチャ センター](https://azure.microsoft.com/global-infrastructure/services/?regions=all&amp;products=synapse-analytics,virtual-machines,sql-database)を参照してください

- さまざまなデータベースとデータの移行シナリオ、および特殊なタスクを支援する Microsoft とサードパーティ製のサービスとツールの表については、[データ移行のためのサービスとツール](../../../dms/dms-tools-matrix.md)に関する記事を参照してください。

- Azure SQL の詳細については、以下を参照してください。
   - [デプロイ オプション](../../azure-sql-iaas-vs-paas-what-is-overview.md)
   - [Azure VM での SQL Server](../../virtual-machines/windows/sql-server-on-azure-vm-iaas-what-is-overview.md)
   - [Azure 総保有コスト計算ツール](https://azure.microsoft.com/pricing/tco/calculator/) 


- クラウド移行のためのフレームワークと導入サイクルの詳細については、以下を参照してください
   -  [Azure 向けのクラウド導入フレームワーク](/azure/cloud-adoption-framework/migrate/azure-best-practices/contoso-migration-scale)
   -  [Azure に移行するワークロードの料金計算とサイズ設定のベスト プラクティス](/azure/cloud-adoption-framework/migrate/azure-best-practices/migrate-best-practices-costs) 

- ライセンスの詳細については、以下を参照してください
   - [Azure ハイブリッド特典を使用するライセンス持ち込み](../../virtual-machines/windows/licensing-model-azure-hybrid-benefit-ahb-change.md)
   - [SQL Server 2008 および SQL Server 2008 R2 の延長サポートの無料利用](../../virtual-machines/windows/sql-server-2008-extend-end-of-support.md)


- アプリケーション アクセス レイヤーを評価するには、「[Data Access Migration Toolkit (プレビュー)](https://marketplace.visualstudio.com/items?itemName=ms-databasemigration.data-access-migration-toolkit)」を参照してください
- データ アクセス レイヤーの A/B テストの実行方法について詳しくは、[Database Experimentation Assistant](/sql/dea/database-experimentation-assistant-overview) についてのページを参照してください。