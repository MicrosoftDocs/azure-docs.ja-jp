---
title: SQL Server から SQL Database へ:移行の概要
description: SQL Server データベースを Azure SQL Database に移行するために使用できるさまざまなツールとオプションについて説明します。
ms.service: sql-database
ms.subservice: migration-guide
ms.custom: ''
ms.devlang: ''
ms.topic: how-to
author: mokabiru
ms.author: mokabiru
ms.reviewer: MashaMSFT
ms.date: 11/06/2020
ms.openlocfilehash: 733b2375a26b0157f88bc148b52932e2f3e3f2e2
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102488275"
---
# <a name="migration-overview-sql-server-to-sql-database"></a>移行の概要: SQL Server から SQL Database
[!INCLUDE[appliesto--sqldb](../../includes/appliesto-sqldb.md)]

SQL Server を Azure SQL Database に移行するためのさまざまな移行オプションと考慮事項について説明します。 

オンプレミスまたは以下で実行されている SQL Server を移行できます。 

- SQL Server on Virtual Machines  
- アマゾン ウェブ サービス (AWS) EC2 
- Amazon Relational Database Service (AWS RDS) 
- Compute Engine (Google Cloud Platform - GCP)  
- Cloud SQL for SQL Server (Google Cloud Platform – GCP) 

その他のシナリオについては、[データベース移行ガイド](https://datamigration.microsoft.com/)を参照してください。 

## <a name="overview"></a>概要

[Azure SQL Database](../../database/sql-database-paas-overview.md) は、フル マネージドのサービスとしてのプラットフォーム (PaaS) を必要とする SQL Server ワークロードで推奨されるターゲット オプションです。 SQL Database では、さまざまなアプリケーションの種類に対応するために組み込まれている高可用性、インテリジェントなクエリ処理、スケーラビリティ、パフォーマンス機能と共に、ほとんどのデータベース管理機能が処理されます。 

SQL Database では、さまざまな種類のアプリケーションやワークロードに対応する複数の[デプロイ モデル](../../database/sql-database-paas-overview.md#deployment-models)と[サービス レベル](../../database/service-tiers-vcore.md#service-tiers)によって柔軟性が提供されます。

SQL Database に移行する主な利点の 1 つは、PaaS 機能を活用してアプリケーションを最新化し、SQL Agent ジョブなどのインスタンス レベルでスコープ設定されている技術コンポーネントに対する依存関係を排除できることです。

また、[仮想コア ベースの購入モデル](../../database/service-tiers-vcore.md)を選択した場合は、SQL Server の [Azure ハイブリッド特典](https://azure.microsoft.com/pricing/hybrid-benefit/)を使用して、SQL Server のオンプレミス ライセンスを Azure SQL Database に移行することでコストを節約することもできます。

このガイドは、SQL Server データベースを Azure SQL Database に移行する準備を行う際の移行オプションと考慮事項を明確にすることを目的としています。  

## <a name="considerations"></a>考慮事項 

移行オプションを評価する際に考慮する必要がある主な要因は、次のとおりです。 

- サーバーとデータベースの数
- データベースのサイズ
- 移行プロセス中の許容可能なビジネス ダウンタイム 

このガイドに記載されている移行オプションでは、これらの要因が考慮されています。 Azure SQL Database に論理データを移行する場合、移行に要する時間は、データベース内のオブジェクトの数とデータベースのサイズの両方によって決まります。 

ワークロードとユーザー設定に応じて、さまざまなツールを使用できます。 UI ベースのツールを使用して 1 つのデータベースを迅速に移行できるツールもあれば、大規模な移行処理を自動化して複数のデータベースを移行できるツールもあります。 

## <a name="choose-appropriate-target"></a>適切なターゲットを選択する

Azure SQL Database の適切なデプロイ モデルとサービス レベルを選択する際に役立つ一般的なガイドラインを検討してください。 デプロイ時にコンピューティングとストレージのリソースを選択し、後で [Azure portal](../../database/scale-resources.md) を使用して、アプリケーションのダウンタイムを発生させずにそれらを変更することができます。


**デプロイ モデル**: アプリケーション ワークロードと使用パターンを把握して、単一データベースとエラスティック プールのどちらにするかを決定します。 

- [単一データベース](../../database/single-database-overview.md)は、ほとんどの最新クラウド アプリケーションやマイクロサービスに適したフル マネージド データベースです。
- [エラスティック プール](../../database/elastic-pool-overview.md)は、CPU やメモリなどの共有リソース セットを使用する単一データベースのコレクションです。これは、プール内のデータベースを、予測可能な使用パターンと結合するのに適しています。それによって、同じリソース セットを効果的に共有することができます。

**モデルの購入**: 仮想コア、DTU、またはサーバーレスのいずれかの購入モデルを選択します。 

- [仮想コア モデル](../../database/service-tiers-vcore.md)を使用すると、Azure SQL Database の仮想コアの数を選択できます。これは、オンプレミスの SQL Server から変換する場合の最も簡単な選択肢となります。 これは、[Azure ハイブリッド特典](https://azure.microsoft.com/pricing/hybrid-benefit/)を使用したライセンス コストの節約がサポートされる唯一のオプションです。 
- [DTU モデル](../../database/service-tiers-dtu.md)では、組み合わされた DTU を提供するために、基になるコンピューティング、メモリ、IO リソースが抽象化されます。 
- [サーバーレス モデル](../../database/serverless-tier-overview.md)は、1 秒あたりの使用量で課金されるコンピューティング リソースを使用した自動オンデマンド スケーリングを必要とするワークロードを対象としています。 サーバーレス コンピューティング レベルでは、アイドル期間中はデータベースが自動的に一時停止され (この場合、ストレージのみが課金されます)、再びアクティブになると自動的にデータベースが再開されます。 

**サービス レベル**: 異なる種類のアプリケーション用に設計された 3 つのサービス レベルから選択します。

- [General Purpose / Standard サービス レベル](../../database/service-tier-general-purpose.md)では、中間から下位レベルのアプリケーションの配信に適したコンピューティングとストレージを備えた、バランスの取れた予算重視のオプションが提供されます。これには、障害から復旧するための冗長性がストレージ レイヤーに組み込まれています。 ほとんどのデータベース ワークロード向けに設計されています。 
- [Business Critical / Premium サービス レベル](../../database/service-tier-business-critical.md)は、高いトランザクション レート、低遅延の IO、および (フェールオーバーと、読み取りワークロードのオフロードの両方に使用できるセカンダリ レプリカを使用した) 高レベルの回復性を必要とする上位レベル アプリケーション用です。
- [Hyperscale サービス レベル](../../database/service-tier-hyperscale.md)は、データ ボリュームが増大しており、100 TB のデータベース サイズまで自動的にスケールアップする必要があるデータベース用です。 非常に大規模なデータベース向けに設計されています。 

> [!IMPORTANT]
> 高いインジェスト率を制限するために、Azure SQL Database では[トランザクション ログ速度が管理](../../database/resource-limits-logical-server.md#transaction-log-rate-governance)されています。 そのため、移行中に、CPU やスループットに対する負荷を軽減するために、ターゲット データベース リソース (仮想コア/DTU) のスケーリングが必要になる場合があります。 適切なサイズのターゲット データベースを選択してください。ただし、必要に応じて、移行のためにリソースをスケールアップすることを計画しておいてください。 


### <a name="sql-server-on-azure-vm-alternative"></a>Azure VM 上の SQL Server の代替

お客様のビジネスによっては、[Azure Virtual Machines上の SQL Server](../../virtual-machines/windows/sql-server-on-azure-vm-iaas-what-is-overview.md) の方が、Azure SQL Database よりも適切なターゲットである場合があります。 

お客様のビジネスに次が該当する場合は、代わりに Azure VM 上の SQL Server に移行することを検討してください。 

- サードパーティ製やカスタム エージェントを SQL Server と同じ仮想マシンにインストールするためなど、オペレーティング システムやファイル システムへの直接アクセスが必要な場合。 
- FileStream/FileTable、PolyBase、クロス インスタンス トランザクションなど、まだサポートされていない機能に対する厳密な依存関係がある場合。 
- 特定バージョンの SQL Server (たとえば 2012 など) を確実に維持する必要がある場合。 
- コンピューティング要件がマネージド インスタンスによって提供されるものよりはるかに低く (1 つの仮想コアなど)、データベース統合が許容可能なオプションではない場合。 


## <a name="migration-tools"></a>移行ツール 

移行のための推奨ツールは、Data Migration Assistant と Azure Database Migration Service です。 使用できる代替移行オプションが他にもあります。 

### <a name="recommended-tools"></a>推奨されるツール

次の表に、推奨される移行ツールを示します。 

|テクノロジ | 説明|
|---------|---------|
| [Azure Migrate](/azure/migrate/how-to-create-azure-sql-assessment) | VMware の場合、Azure SQL 用の Azure Migrate を使用すると、SQL データ資産を大規模に検出して評価することができます。これにより、Azure SQL デプロイに関する推奨事項、ターゲットのサイズ設定、月単位の見積もりが提供されます。 | 
|[Data Migration Assistant (DMA)](/sql/dma/dma-migrateonpremsqltosqldb)|Data Migration Assistant は、シームレスな SQL Server の評価と Azure SQL Database への移行 (スキーマとデータの両方) を提供するデスクトップ ツールです。 このツールは、オンプレミスのサーバー、またはソース データベースに接続できるローカル コンピューターにインストールできます。 移行プロセスは、ソースとターゲットのデータベース内のオブジェクト間で行われる論理的なデータ移動です。 </br> - 単一データベース (スキーマとデータの両方) を移行する|
|[Azure Database Migration Service (DMS)](../../../dms/tutorial-sql-server-to-azure-sql.md)|Azure portal を使用するか、PowerShell を使って自動化して SQL Server データベースを Azure SQL Database に移行できるファースト パーティの Azure サービス。 Azure DMS では、優先する Azure Virtual Network (VNet) をプロビジョニング時に選択して、ソース SQL Server データベースに接続できることを確認する必要があります。 </br> - 単一データベースまたは大規模な移行を行う。 |
| | |


### <a name="alternative-tools"></a>代替ツール

次の表に、代替移行ツールを示します。 

|テクノロジ |説明  |
|---------|---------|
|[トランザクション レプリケーション](../../database/replication-to-sql-database.md)|トランザクションの一貫性を維持しながら、パブリッシャーとサブスクライバーの種類の移行オプションを指定することによって、ソース SQL Server データベース テーブルから SQL Database にデータをレプリケートします。 データの増分変更は、パブリッシャーで発生したときにサブスクライバーに反映されます。|
|[インポートまたはエクスポート サービス / BACPAC](../../database/database-import.md)|[BACPAC](/sql/relational-databases/data-tier-applications/data-tier-applications#bacpac) は `.bacpac` 拡張子を持つ Windows ファイルで、データベースのスキーマとデータがカプセル化されています。 BACPAC を使用して、ソース SQL Server からデータをエクスポートし、Azure SQL Database にデータをインポートすることができます。 BACPAC ファイルは、Azure portal を使用して新しい Azure SQL Database にインポートできます。 </br></br> サイズが大きいデータベースまたは数が多いデータベースのスケールとパフォーマンスについては、[SqlPackage](../../database/database-import.md#using-sqlpackage) コマンドライン ユーティリティを使用してデータベースのエクスポートとインポートを行うことを検討してください。|
|[一括コピー](/sql/relational-databases/import-export/import-and-export-bulk-data-by-using-the-bcp-utility-sql-server)|[一括コピー プログラム (bcp) ユーティリティ](/sql/tools/bcp-utility)では、SQL Server のインスタンスからデータ ファイルにデータがコピーされます。 BCP ユーティリティを使用して、ソースからデータをエクスポートし、ターゲット SQL Database にデータ ファイルをインポートします。 </br></br> Azure SQL Database にデータを移動する高速一括コピー操作の場合、[スマート一括コピー ツール](/samples/azure-samples/smartbulkcopy/smart-bulk-copy/)を使用し、並列コピー タスクを利用して転送速度を最大化できます。|
|[Azure Data Factory (ADF)](../../../data-factory/connector-azure-sql-database.md)|Azure Data Factory の[コピー アクティビティ](../../../data-factory/copy-activity-overview.md)では、組み込みコネクタと [Integration Runtime](../../../data-factory/concepts-integration-runtime.md) を使用して、ソース SQL Server データベースから SQL Database にデータが移行されます。</br> </br> ADF では、SQL Server ソースから SQL Database にデータを移動するためのさまざまな[コネクタ](../../../data-factory/connector-overview.md)がサポートされています。|
|[SQL データ同期](../../database/sql-data-sync-data-sql-server-sql-database.md)|SQL データ同期は、Azure SQL Database 上に構築されているサービスであり、選択したデータをオンプレミスおよびクラウドの複数のデータベース間で双方向に同期させることができます。</br>データ同期は、Azure SQL Database または SQL Server の複数のデータベースにわたってデータを更新し続ける必要がある場合に便利です。|
| | |

## <a name="compare-migration-options"></a>移行オプションを比較する

移行オプションを比較して、ビジネス ニーズに合ったパスを選択します。 

### <a name="recommended-options"></a>推奨されるオプション

次の表では、推奨される移行オプションを比較しています。 

|移行オプション  |使用する場合  |考慮事項  |
|---------|---------|---------|
|[Data Migration Assistant (DMA)](/sql/dma/dma-migrateonpremsqltosqldb) | - 単一データベース (スキーマとデータの両方) を移行する。  </br> - データ移行プロセス中のダウンタイムを許容できる。 </br> </br> サポートされているソース: </br> - オンプレミスの SQL Server (2005 - 2019) または Azure VM </br> - AWS EC2 </br> - AWS RDS </br> - GCP コンピューティングの SQL Server VM | - 移行アクティビティでは、データベース オブジェクト間のデータ移動 (ソースからターゲット) が実行されるため、オフピーク時に実行することをお勧めします。 </br> - DMA によって、移行された行の数など、データベース オブジェクトごとの移行の状態が報告されます。  </br> - 大規模な移行 (データベースの数 / データベースのサイズ) では、以下に示す Azure Database Migration Service を使用します。|
|[Azure Database Migration Service (DMS)](../../../dms/tutorial-sql-server-to-azure-sql.md)| - 単一データベースまたは大規模な移行を行う。 </br> - 移行プロセス中のダウンタイムを許容できる。 </br> </br> サポートされるソース: </br> - オンプレミスの SQL Server (2005 - 2019) または Azure VM </br> - AWS EC2 </br> - AWS RDS </br> - GCP コンピューティングの SQL Server VM | - 大規模な移行は、[PowerShell](../../../dms/howto-sql-server-to-azure-sql-powershell.md) を使用して自動化できます。 </br> - 移行が完了するまでの時間は、データベースのサイズとデータベース内のオブジェクトの数によって異なります。 </br> - ソース データベースは、読み取り専用として設定する必要があります。 |
| | | |

### <a name="alternative-options"></a>代替オプション

次の表では、代替移行オプションを比較しています。 

|方法/テクノロジ |使用する場合    |考慮事項  |
|---------|---------|---------|
|[トランザクション レプリケーション](../../database/replication-to-sql-database.md)| - ソース データベース テーブルからターゲット SQL Database テーブルに変更を継続的にパブリッシュすることによって移行する。 </br> - データベースを完全に、または選択したテーブル (データベースのサブセット) について部分的に移行する。  </br> </br> サポートされているソース: </br> - [SQL Server (2016 - 2019) (一部制限あり)](/sql/relational-databases/replication/replication-backward-compatibility) </br> - AWS EC2  </br> - GCP コンピューティングの SQL Server VM  | - セットアップは、他の移行オプションと比べて比較的複雑です。   </br> - (データベースをオフラインにせずに) データを移行するための継続的レプリケーション オプションが提供されます。  </br> - トランザクション レプリケーションには、ソース SQL Server にパブリッシャーを設定するときに考慮べきいくつかの制限があります。 詳細については、「[オブジェクトのパブリッシュに関する制限事項](/sql/relational-databases/replication/publish/publish-data-and-database-objects#limitations-on-publishing-objects)」を参照してください。 </br>- [レプリケーション アクティビティを監視](/sql/relational-databases/replication/monitor/monitoring-replication)することができます。    |
|[インポートまたはエクスポート サービス / BACPAC](../../database/database-import.md)| - 個々の基幹業務アプリケーション データベースを移行する。 </br>- 比較的小規模なデータベースに適している。  </br>  - 個別の移行サービスやツールを必要としない。 </br> </br> サポートされているソース: </br> - オンプレミスの SQL Server (2005 - 2019) または Azure VM </br> - AWS EC2 </br> - AWS RDS </br> - GCP コンピューティングの SQL Server VM  |  - データをソースでエクスポートし、転送先でインポートする必要があるため、ダウンタイムが必要です。   </br> - 切り捨てやデータ型不一致のエラーを回避するために、エクスポート/インポートで使用されるファイル形式とデータ型は、テーブル スキーマと一致している必要があります。  </br> - 多数のオブジェクトを含むデータベースをエクスポートするために要する時間は、大幅に長くなる可能性があります。       |
|[一括コピー](/sql/relational-databases/import-export/import-and-export-bulk-data-by-using-the-bcp-utility-sql-server)| - 完全または部分的にデータを移行する。 </br> - ダウンタイムを許容できる。 </br> </br> サポートされるソース: </br> - オンプレミスの SQL Server (2005 - 2019) または Azure VM </br> - AWS EC2 </br> - AWS RDS </br> - GCP コンピューティングの SQL Server VM   | - データをソースからエクスポートするため、およびターゲットにインポートするためのダウンタイムが必要です。 </br> - エクスポート/インポートで使用されるファイル形式とデータ型は、テーブル スキーマと一致している必要があります。 |
|[Azure Data Factory (ADF)](../../../data-factory/connector-azure-sql-database.md)| - ソース SQL Server データベースからのデータの移行や変換を行う。 </br> - 通常、ビジネス インテリジェンス (BI) ワークロードのために、複数のデータ ソースから Azure SQL Database にデータをマージする。  |  - ソースからターゲットにデータを移動するために、ADF でデータ移動パイプラインを作成する必要があります。   </br> - [コスト](https://azure.microsoft.com/pricing/details/data-factory/data-pipeline/)が重要な考慮事項であり、パイプライン トリガー回数、アクティビティの実行数、データ移動の期間などに応じて変わる。 |
|[SQL データ同期](../../database/sql-data-sync-data-sql-server-sql-database.md)| - ソースとターゲットのデータベース間でデータを同期する。</br> - 双方向のフローで Azure SQL Database とオンプレミスの SQL Server 間の継続的同期を実行するのに適している。 | - Azure SQL Database をハブ データベースとし、オンプレミスの SQL Server データベースをメンバー データベースとして同期を行う必要があります。</br> - トランザクション レプリケーションと比較した場合、SQL データ同期では、オンプレミスと Azure SQL Database 間の双方向データ同期がサポートされます。 </br> - ワークロードによっては、パフォーマンスに大きな影響を与える可能性があります。|
| | | |

## <a name="feature-interoperability"></a>機能の相互運用性 

他の SQL Server 機能に依存するワークロードを移行する場合は、追加の考慮事項があります。

#### <a name="sql-server-integration-services"></a>SQL Server Integration Services
SQL Server Integration Services (SSIS) パッケージを [Azure Data Factory](../../../data-factory/introduction.md) で Azure-SSIS ランタイムに再デプロイして、パッケージを Azure に移行します。 Azure Data Factory では、Azure で SSIS パッケージを実行するために構築されたランタイムを提供することによって、[SSIS パッケージの移行がサポート](../../../data-factory/scenario-ssis-migration-overview.md#azure-sql-database-as-database-workload-destination)されています。 または、[データフロー](../../../data-factory/concepts-data-flow-overview.md)を使用して、ADF でネイティブに SSIS ETL ロジックを書き換えることもできます。


#### <a name="sql-server-reporting-services"></a>SQL Server Reporting Services
SQL Server Reporting Services (SSRS) のレポートを、Power BI のページ分割されたレポートに移行します。  [RDL 移行ツール](https://github.com/microsoft/RdlMigration)を使用すると、レポートの準備と移行に役立ちます。 このツールは、ユーザーが SSRS サーバーから Power BI に RDL レポートを移行するのを助けるため、Microsoft によって開発されました。 これは GitHub で入手でき、移行シナリオのエンドツーエンドのチュートリアル文書も使用できます。 

#### <a name="high-availability"></a>高可用性
高可用性アーキテクチャは [General Purpose (Standard 可用性モデル)](../../database/high-availability-sla.md#basic-standard-and-general-purpose-service-tier-locally-redundant-availability) と [Business Critical (Premium 可用性モデル)](../../database/high-availability-sla.md#premium-and-business-critical-service-tier-locally-redundant-availability) の SQL Database に既に組み込まれているため、SQL Server 高可用性機能 ( Always On フェールオーバー クラスター インスタンスや Always On 可用性グループなど) の手動の設定は、ターゲット Azure SQL Database では廃止されます。 また、Bus Critical または Premium サービス レベルでは、読み取り専用の目的でいずれかのセカンダリ ノードに接続できる読み取りスケールアウトも提供されています。 

SQL Database に含まれている高可用性アーキテクチャに加えて、[自動フェールオーバー グループ](../../database/auto-failover-group-overview.md)機能もあります。これを使用すると、マネージド インスタンス内のデータベースの別リージョンへのレプリケーションとフェールオーバーを管理できます。 

#### <a name="sql-agent-jobs"></a>SQL Agent ジョブ
SQL Agent ジョブは、Azure SQL Database では直接サポートされていないため、[Elastic Database ジョブ (プレビュー)](../../database/job-automation-overview.md) にデプロイする必要があります。

#### <a name="logins-and-groups"></a>ログインとグループ
Database Migration Service (DMS) をオフライン モードで使用して、SQL ログインをソースの SQL Server から Azure SQL Database に移動します。  **移行ウィザード** の **[Selected logins]\(選択したログイン\)** ブレードを使用して、ログインをターゲットの SQL Database に移行します。 

Windows ユーザーとグループは、DMS 構成ページで対応するトグル ボタンを有効にすることにより、DMS を使用して移行することもできます。 

または、Microsoft データ移行アーキテクトが特別に設計した [PowerShell ユーティリティ ツール](https://github.com/microsoft/DataMigrationTeam/tree/master/IP%20and%20Scripts/MoveLogins)を使用できます。 このユーティリティでは、ログインを再作成し、ソースからターゲットへのデータベース ユーザーを選択するために、PowerShell を使用して Transact-SQL (T-SQL) スクリプトが作成されます。 このツールでは、Windows AD アカウントが Azure AD アカウントに自動的にマップされ、ソース Active Directory に対してログインごとに UPN 参照を実行できます。 このツールでは、カスタム サーバーとデータベースのロール、およびロール メンバーシップ、データベース ロール、ユーザーのアクセス許可のスクリプトが作成されます。 包含データベースはまだサポートされていません。使用可能な SQL Server アクセス許可のサブセットのみがスクリプト化されます。 


#### <a name="system-databases"></a>システム データベース
Azure SQL Database の場合、適用できるシステム データベースは、[master](/sql/relational-databases/databases/master-database) と tempdb のみになります。 詳細については、[Azure SQL Database での tempdb](/sql/relational-databases/databases/tempdb-database#tempdb-database-in-sql-database) に関する記事を参照してください。

## <a name="leverage-advanced-features"></a>高度な機能を活用する 

SQL Database によって提供されるクラウドベースの高度な機能をぜひ活用してください。 たとえば、バックアップの管理はサービスによって行われるため、もう心配する必要はありません。 [保持期間内の任意の特定の時点](../../database/recovery-using-backups.md#point-in-time-restore)に復元できます。 

セキュリティを強化するために、 [Azure Active Directory 認証](../../database/authentication-aad-overview.md)、[監査](../../database/auditing-overview.md)、 [脅威の検出](../../database/azure-defender-for-sql.md)、 [行レベル セキュリティ](/sql/relational-databases/security/row-level-security)、 [動的データ マスク](/sql/relational-databases/security/dynamic-data-masking)の使用を検討してください。

高度な管理とセキュリティの機能に加えて、SQL Database には[ワークロードの監視とチューニング](../../database/monitor-tune-overview.md)に役立つ高度なツール セットが用意されています。 [Azure SQL Analytics (プレビュー)](../../../azure-monitor/insights/azure-sql.md) は、Azure SQL Database のすべてのデータベースのパフォーマンスを、大規模に、かつ複数のサブスクリプションにわたって 1 つのビューで監視するための先進的クラウド監視ソリューションです。 Azure SQL Analytics で組み込みのインテリジェンスを使用して重要なパフォーマンス メトリックを収集し、視覚化することによって、パフォーマンスのトラブルシューティングを行うことができます。

[自動チューニング](/sql/relational-databases/automatic-tuning/automatic-tuning#automatic-plan-correction) では、SQL 実行プラン統計のパフォーマンスが継続的に監視され、特定されたパフォーマンスの問題が自動的に修正されます。 


## <a name="migration-assets"></a>移行資産 

さらに支援が必要な場合は、実際の移行プロジェクトのために開発された次のリソースを参照してください。

|Asset  |説明  |
|---------|---------|
|[データ ワークロード評価モデルとツール](https://github.com/Microsoft/DataMigrationTeam/tree/master/Data%20Workload%20Assessment%20Model%20and%20Tool)| このツールを使用すると、特定のワークロードに対して、推奨される "最適な" ターゲット プラットフォーム、クラウドの準備状況、アプリケーションとデータベースの修復レベルがわかります。 シンプルなワンクリックの計算とレポート生成機能があり、自動化された均一なターゲット プラットフォームの決定プロセスが用意されているので、大規模な不動産評価を加速させることができます。|
|[DBLoader ユーティリティ](https://github.com/microsoft/DataMigrationTeam/tree/master/DBLoader%20Utility)|DBLoader を使用すると、区切りテキスト ファイルから SQL Server にデータを読み込むことができます。 この Windows コンソール ユーティリティでは、SQL Database を含むすべてのバージョンの SQL Server で機能する、SQL Server のネイティブ クライアント一括読み込みインターフェイスが使用されます。|
|[PowerShell を使用したデータベースの一括作成](https://github.com/Microsoft/DataMigrationTeam/tree/master/Bulk%20Database%20Creation%20with%20PowerShell)|これには、リソース グループ (create_rg.ps1)、[Azure の論理サーバー](../../database/logical-servers.md) (create_sqlserver.ps1)、および Azure SQL Database (create_sqldb.ps1) を作成する 3 つの PowerShell スクリプトのセットが含まれます。 スクリプトにはループ機能が含まれているので、必要に応じて反復処理を行って、必要な数のサーバーとデータベースを作成できます。|
|[MSSQL-Scripter と PowerShell を使用したスキーマの一括デプロイ](https://github.com/Microsoft/DataMigrationTeam/tree/master/Bulk%20Schema%20Deployment%20with%20MSSQL-Scripter%20&%20PowerShell)|この資産では、リソース グループ (Azure SQL Database をホストする 1 つまたは複数の [Azure 内の論理サーバー](../../database/logical-servers.md)) が作成され、オンプレミスの SQL Server (または複数の SQL Server (2005 以降)) からすべてのスキーマがエクスポートされ、Azure SQL Database にインポートされます。|
|[SQL Serverエージェント ジョブを Elastic Database ジョブに変換する](https://github.com/microsoft/DataMigrationTeam/tree/master/IP%20and%20Scripts/Convert%20SQL%20Server%20Agent%20Jobs%20into%20Elastic%20Database%20Jobs)|このスクリプトにより、ソースの SQL Server エージェント ジョブが Elastic Database ジョブに移行されます。|
|[Azure SQL Database からメールを送信する](https://github.com/microsoft/DataMigrationTeam/tree/master/IP%20and%20Scripts/AF%20SendMail)|これにより、オンプレミスの SQL Server で使用できる SendMail 機能の代替手段としてソリューションが提供されます。 このソリューションでは、Azure Functions と Azure SendGrid サービスを使用して Azure SQL Database から電子メールが送信されます。|
|[オンプレミスの SQL Server ログインを Azure SQL Database に移動するユーティリティ](https://github.com/microsoft/DataMigrationTeam/tree/master/IP%20and%20Scripts/MoveLogins)|ログインを再作成し、オンプレミス SQL Server から Azure SQL Database に移行するデータベース ユーザーを選択するための T-SQL コマンド スクリプトを作成する PowerShell スクリプト。 このツールを使用すると、Windows AD アカウントを Azure AD アカウントに自動的にマッピングできるだけでなく、必要に応じて SQL Server のネイティブ ログインを移行することもできます。|
|[Logman を使用した PerfMon データ コレクションの自動化](https://github.com/microsoft/DataMigrationTeam/tree/master/IP%20and%20Scripts/Perfmon%20Data%20Collection%20Automation%20Using%20Logman)|ベースライン パフォーマンスを把握するための PerMon データを収集し、移行ターゲットの推奨事項を支援するツール。 このツールでは、logman.exe を使用して、リモート SQL Server に設定されたパフォーマンス カウンターを作成、開始、停止、削除するコマンドが作成されます。|
|[ホワイトペーパー - BACPAC を使用した Azure SQL DB へのデータベースの移行](https://github.com/microsoft/DataMigrationTeam/blob/master/Whitepapers/Database%20migrations%20-%20Benchmarks%20and%20Steps%20to%20Import%20to%20Azure%20SQL%20DB%20Single%20Database%20from%20BACPAC.pdf)|このホワイトペーパーには、BACPAC ファイルを使用して SQL Server から Azure SQL Database への移行を加速させるためのガイダンスと手順が示されています。|

これらのリソースは、Azure Data Group エンジニアリング チームがスポンサーである Data SQL Ninja プログラムの一部として開発されました。 Data SQL Ninja プログラムの中核となるのは、複雑なモダン化のブロックを解除して加速し、データ プラットフォームを Microsoft の Azure Data プラットフォームに移行する機会を獲得することです。 組織が Data SQL Ninja プログラムへの参加に関心があると思われる場合は、アカウント チームに連絡し、申請を提出するよう依頼してください。


## <a name="next-steps"></a>次の手順

Azure SQL Database への SQL Server の移行を開始するには、[SQL Server から SQL Database への移行ガイド](sql-server-to-sql-database-guide.md)を参照してください。

- さまざまなデータベースとデータの移行シナリオ、および特殊なタスクに役立つ Microsoft とサードパーティ製のサービスとツールの一覧については、[データ移行のためのサービスとツール](../../../dms/dms-tools-matrix.md)に関するページを参照してください。

- SQL Database の詳細については、以下を参照してください。
   - [Azure SQL Database の概要](../../database/sql-database-paas-overview.md)
   - [Azure 総保有コスト計算ツール](https://azure.microsoft.com/pricing/tco/calculator/) 

- クラウド移行のためのフレームワークと導入サイクルの詳細については、以下を参照してください
   -  [Azure 向けのクラウド導入フレームワーク](/azure/cloud-adoption-framework/migrate/azure-best-practices/contoso-migration-scale)
   -  [Azure に移行するワークロードの料金計算とサイズ設定のベスト プラクティス](/azure/cloud-adoption-framework/migrate/azure-best-practices/migrate-best-practices-costs) 


- アプリケーション アクセス層を評価するには、「[Data Access Migration Toolkit (プレビュー)](https://marketplace.visualstudio.com/items?itemName=ms-databasemigration.data-access-migration-toolkit)」を参照してください。
- データ アクセス レイヤーの A/B テストの実行方法について詳しくは、[Database Experimentation Assistant](/sql/dea/database-experimentation-assistant-overview) についてのページを参照してください。