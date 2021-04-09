---
title: SQL Server から SQL Database へ:移行ガイド
description: SQL Server データベースを Azure SQL Database に移行するには、このガイドのようにします。
ms.service: sql-database
ms.subservice: migration-guide
ms.custom: ''
ms.devlang: ''
ms.topic: how-to
author: mokabiru
ms.author: mokabiru
ms.reviewer: MashaMSFT
ms.date: 03/19/2021
ms.openlocfilehash: 9205301cb77941e4ea7ca026710d44ba82f6a937
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "103563846"
---
# <a name="migration-guide-sql-server-to-sql-database"></a>移行ガイド:SQL Server から SQL Database
[!INCLUDE[appliesto--sqldb](../../includes/appliesto-sqldb.md)]

このガイドでは、SQL Server インスタンスを Azure SQL Database に移行する方法について説明します。 

オンプレミスまたは以下で実行されている SQL Server を移行できます。 

- SQL Server on Virtual Machines  
- アマゾン ウェブ サービス (AWS) EC2 
- Amazon Relational Database Service (AWS RDS) 
- Compute Engine (Google Cloud Platform - GCP)  
- Cloud SQL for SQL Server (Google Cloud Platform – GCP) 

移行の詳細については、[移行の概要](sql-server-to-sql-database-overview.md)に関するページを参照してください。 その他のシナリオについては、[データベース移行ガイド](https://datamigration.microsoft.com/)を参照してください。

:::image type="content" source="media/sql-server-to-database-overview/migration-process-flow-small.png" alt-text="移行プロセス フロー":::

## <a name="prerequisites"></a>前提条件 

SQL Server を Azure SQL Database に移行するには、次の前提条件が満たされていることを確認します。 

- [移行方法](sql-server-to-sql-database-overview.md#compare-migration-options)および対応するツールが選択されている 
- ソースの SQL Server に接続できるコンピューターに [Data Migration Assistant (DMA)](https://www.microsoft.com/download/details.aspx?id=53595) がインストールされている
- ターゲットの [Azure SQL Database](../../database/single-database-create-quickstart.md)


## <a name="pre-migration"></a>移行前

ソース環境がサポートされていることを確認した後、移行前ステージから開始します。 既存のデータ ソースをすべて検出し、移行が可能かどうかを評価し、移行の阻害要素となる可能性のある問題を特定します。 

### <a name="discover"></a>発見

検出フェーズでは、ネットワークをスキャンして、組織で使用されているすべての SQL Server インスタンスと機能を特定します。 

[Azure Migrate](../../../migrate/migrate-services-overview.md) を使用して、オンプレミス サーバーの移行の適合性を評価し、パフォーマンスに基づくサイズ変更を行い、Azure でそれらのサーバーを実行するためのコストを見積もります。 

または、 [Microsoft Assessment and Planning Toolkit ("MAP Toolkit")](https://www.microsoft.com/download/details.aspx?id=7826) を使用して、現在の IT インフラストラクチャを評価します。 ツールキットには、移行計画のプロセスを簡略化するための強力なインベントリ、評価、およびレポート ツールが用意されています。 

検出フェーズで使用できるツールの詳細については、「[データ移行のシナリオで利用できるサービスとツール](../../../dms/dms-tools-matrix.md)」を参照してください。 

### <a name="assess"></a>アクセス 

[!INCLUDE [assess-estate-with-azure-migrate](../../../../includes/azure-migrate-to-assess-sql-data-estate.md)]

データ ソースが検出された後、Azure SQL Database に移行できるオンプレミスの SQL Server データベースを評価して、移行の阻害要素や互換性の問題を明らかにします。 

Data Migration Assistant (バージョン 4.1 以降) を使用してデータベースを評価し、以下を得ることができます。 

- [Azure のターゲットに関する推奨事項](/sql/dma/dma-assess-sql-data-estate-to-sqldb)
- [Azure の SKU に関する推奨事項](/sql/dma/dma-sku-recommend-sql-db)

Database Migration Assessment を使用して環境を評価するには、次の手順に従います。 

1. [Data Migration Assistant (DMA)](https://www.microsoft.com/download/details.aspx?id=53595) を開きます。 
1. **[ファイル]** を選択し、 **[新しい評価]** を選択します。 
1. プロジェクト名を指定し、ソース サーバーの種類として SQL Server を選択し、ターゲット サーバーの種類として Azure SQL Database を選択します。 
1. 生成する評価レポートの種類を選択します。 たとえば、データベースの互換性、機能パリティなどがあります。 評価の種類によっては、ソース SQL Server で必要となるアクセス許可が異なる場合があります。  この評価を実行する前に、選択したアドバイザーに必要なアクセス許可が DMA によって強調表示されます。
    - **[機能パリティ]** カテゴリで、包括的な一連の推奨事項、Azure で使用可能な代替手段、および移行プロジェクトの計画に役立つ軽減手順を確認できます。 (sysadmin 権限が必要)
    - **[互換性の問題]** カテゴリでは、移行を阻害する可能性のある、部分的にサポートされている機能またはサポートされていない機能の互換性の問題を確認でき、さらにそれらに対処するための推奨事項を確認できます (`CONNECT SQL`、`VIEW SERVER STATE`、および `VIEW ANY DEFINITION` 権限が必要)。
1. SQL Server のソース接続の詳細を指定し、ソース データベースに接続します。
1. **[評価の開始]** を選択します。 
1. プロセスが完了した後、移行の阻害要素と機能パリティの問題に関する評価レポートを選択し、確認します。 評価レポートはファイルにエクスポートすることもできます。このファイルを、組織内の他のチームや担当者と共有できます。 
1. 移行後の作業が最小限になるようなデータベース互換性レベルを特定します。  
1. オンプレミスのワークロードに最適な Azure SQL Database SKU を特定します。 

詳細については、「[Data Migration Assistant を使用した SQL Server 移行評価の実行](/sql/dma/dma-assesssqlonprem)」を参照してください。

データベースが Azure SQL Database に移行できる状態ではないことを示す複数の阻害要素が評価で検出された場合は、代わりに次のことを検討します。

- インスタンス スコープの依存関係が複数ある場合は、[Azure SQL Managed Instance](../managed-instance/sql-server-to-managed-instance-overview.md)
- SQL Database と SQL Managed Instance の両方が適切なターゲットにならない場合は、[Azure Virtual Machines 上の SQL Server](../virtual-machines/sql-server-to-sql-on-azure-vm-migration-overview.md)。 



#### <a name="scaled-assessments-and-analysis"></a>スケーリングされた評価と分析
Data Migration Assistant では、スケーリングされた評価の実行と、分析用の評価レポートの統合がサポートされています。 

複数のサーバーとデータベースがあり、それらを大規模に評価および分析して、データ資産を広範囲に確認する必要がある場合は、以下のリンクを参照して詳細を確認してください。

- [PowerShell を使用したスケーリングされた評価の実行](/sql/dma/dma-consolidatereports)
- [Power BI を使用した評価レポートの分析](/sql/dma/dma-consolidatereports#dma-reports)

> [!IMPORTANT]
> 複数のデータベースの大規模な評価 (特に大規模なもの) も、[DMA のコマンド ライン ユーティリティ](/sql/dma/dma-commandline)を使用して自動化し、さらに分析を行ってターゲットを準備するために [Azure Migrate](/sql/dma/dma-assess-sql-data-estate-to-sqldb#view-target-readiness-assessment-results) にアップロードできます。

## <a name="migrate"></a>移行

移行前ステージの関連タスクを完了したら、スキーマとデータの移行を実行する準備は完了です。 

選択した[移行方法](sql-server-to-sql-database-overview.md#compare-migration-options)を使用してデータを移行します。 

このガイドでは、最も一般的な 2 つのオプションである Data Migration Assistant と Azure Database Migration Service について説明します。 

### <a name="data-migration-assistant-dma"></a>Data Migration Assistant (DMA)

DMA を使用して SQL Server から Azure SQL Database にデータベースを移行するには、次の手順のようにします。 

1. [Database Migration Assistant](https://www.microsoft.com/download/details.aspx?id=53595) をダウンロードしてインストールします。
1. 新しいプロジェクトを作成し、プロジェクトの種類として **[移行]** を選択します。
1. ソース サーバーの種類を **[SQL Server]** に設定し、ターゲット サーバーの種類を **[Azure SQL Database]** に設定し、移行スコープとして **[スキーマとデータ]** を選択して、 **[作成]** を選択します。
1. 移行プロジェクトで、サーバー名、サーバーに接続するための資格情報、移行するソース データベースなど、ソース サーバーの詳細を指定します。
1. ターゲット サーバーの詳細で、Azure SQL Database サーバー名、サーバーに接続するための資格情報、移行先のターゲット データベースを指定します。
1. スキーマ オブジェクトを選択し、ターゲット Azure SQL Database に配置します。
1. 最後に、 **[Start data migration]\(データ移行の開始\)** を選択して、移行の進行状況を監視します。

詳細なチュートリアルについては、「[Data Migration Assistant を使用して、オンプレミスの SQL Server または Azure VM 上の SQL Server を Azure SQL Database に移行する](/sql/dma/dma-migrateonpremsqltosqldb)」を参照してください。 


> [!NOTE]
> - より多くのリソースを提供することでインポート速度を最大にするには、インポート プロセスの間だけ、データベースをより高いサービス レベルとコンピューティング サイズにスケーリングします。 インポートが正常に完了した後でスケールダウンすることができます。</br>
> - インポートされたデータベースの互換性レベルは、ソース データベースの互換性レベルに基づきます。 


### <a name="azure-database-migration-service-dms"></a>Azure Database Migration Service (DMS)

DMS を使用して SQL Server から Azure SQL Database にデータベースを移行するには、次の手順のようにします。

1. まだ行っていない場合は、**Microsoft.DataMigration** リソース プロバイダーをサブスクリプションに登録します。 
1. 任意の場所 (できれば、ターゲットの Azure SQL Database と同じリージョン) に Azure Database Migration Service インスタンスを作成します。 DMS インスタンスをホストするために、既存の仮想ネットワークを選択するか、新しく作成します。
1. DMS インスタンスを作成した後、新しい移行プロジェクトを作成し、ソース サーバーの種類として **[SQL Server]** を、ターゲット サーバーの種類として **[Azure SQL Database ]** を指定します。 移行プロジェクト作成ブレードでアクティビティの種類として **[オフラインデータの移行]** を選択します。
1. **[移行ソースの詳細]** ページでソース SQL Server の詳細を指定し、 **[移行のターゲットの詳細]** ページでターゲット Azure SQL Database の詳細を指定します。
1. 移行のソースおよびターゲットのデータベースをマップし、移行するテーブルを選択します。
1. 移行の概要を確認し、 **[移行の実行]** を選択します。 その後、移行アクティビティを監視すること、およびデータベースの移行の進行状況を確認することができます。

詳細なチュートリアルについては、[DMS を使用した Azure SQL Database への SQL Server の移行](../../../dms/tutorial-sql-server-to-azure-sql.md)に関するページを参照してください。 

## <a name="data-sync-and-cutover"></a>データの同期と切り替え

データの変更をソースからターゲットに継続的にレプリケートおよび同期する移行オプションを使用している場合、ソースのデータとスキーマが変更され、ターゲットと食い違う可能性があります。 データの同期中にソースのすべての変更がキャプチャされ、移行プロセス中にターゲットに適用されることを確認します。 

ソースとターゲットの両方でデータが同じであることを確認した後、ソース環境からターゲット環境に切り替えることができます。 切り替え中の最小限の中断が業務の継続性に影響を与えないようにするために、ビジネス チームやアプリケーション チームと共に切り替えプロセスを計画することが重要です。 

> [!IMPORTANT]
> DMS を使用した移行の一環として切り替えを実行する場合、それに関連する特定の手順について詳しくは、[移行カットオーバーの実行](../../../dms/tutorial-sql-server-azure-sql-online.md#perform-migration-cutover)に関するページを参照してください。

## <a name="migration-recommendations"></a>移行の推奨事項

Azure SQL Database への移行を高速化するには、次の推奨事項を考慮する必要があります。

|  | リソースの競合 | 推奨 |
|--|--|--|
| **ソース (通常はオンプレミス)** |ソースでの移行時の主なボトルネックは、慎重に監視する必要があるデータ ファイルのデータ I/O と待機時間です。  |データ IO とデータ ファイルの待機時間に基づき、仮想マシンと物理サーバーのどちらであるかに応じて、ストレージ管理者と連携してボトルネックを軽減するためのオプションを検討する必要があります。 |
|**ターゲット (Azure SQL Database)**|最大の制限要因は、ログ ファイルのログ生成速度と待機時間です。 Azure SQL Database では、最大 96 MB/秒のログ生成速度を実現できます。 | 移行を高速化するには、ターゲット SQL DB を Business Critical Gen5 8 仮想コアにスケールアップして、ログの最大生成速度である 96 MB/秒を実現し、ログ ファイルの待機時間短縮を達成します。 [Hyperscale](../../database/service-tier-hyperscale.md) サービス レベルでは、選択したサービス レベルに関係なく、100 MB/秒のログ速度が提供されます。 |
|**Network** |必要なネットワーク帯域幅は、ログ取り込み速度の最大値 96 MB/秒 (768 Mb/秒) と同じです。 |オンプレミスのデータ センターから Azure へのネットワーク接続に応じて、ネットワーク帯域幅 (通常は [Azure ExpressRoute](../../../expressroute/expressroute-introduction.md#bandwidth-options)) を確認し、ログの最大取り込み速度に対応します。 |
|**Data Migration Assistant (DMA) に使用される仮想マシン** |CPU が DMA を実行する仮想マシンにとって主なボトルネックです。 |次を使用してデータ移行を高速化するための考慮事項 </br>- Azure コンピューティング集中型 VM </br>- DMA を実行するために少なくとも F8s_v2 (8 仮想コア) VM を使用します </br>- ターゲットと同じ Azure リージョンで VM が実行されていることを確認します |
|**Azure Database Migration Service (DMS)** |DMS のコンピューティング リソースの競合とデータベース オブジェクトの考慮事項 |Premium 4 仮想コアを使用します。 DMS は、外部キー、トリガー、制約、非クラスター化インデックスなどのデータベース オブジェクトを自動的に処理するため、手動介入は必要ありません。  |


## <a name="post-migration"></a>移行後

移行ステージが正常に完了した後、移行後の一連のタスクを実行し、すべてが円滑かつ効率的に機能することを確認します。 

移行後フェーズは、データの精度の問題を調整するため、完全性を確認するため、およびワークロードのパフォーマンスの問題に対処するために非常に重要です。 

### <a name="remediate-applications"></a>アプリケーションを修復する 

データがターゲット環境に移行された後、以前にソースを使用していたすべてのアプリケーションは、ターゲットの使用を開始する必要があります。 これを実現するために、場合によってはアプリケーションの変更が必要です。

### <a name="perform-tests"></a>テストを実行する

データベース移行のテストア プローチは、次のアクティビティで構成されています。

1. **検証テストを作成する**: データベースの移行をテストするには、SQL クエリを使用する必要があります。 ソース データベースとターゲット データベースの両方に対して実行する検証クエリを作成する必要があります。 検証クエリには、定義したスコープが含まれている必要があります。
1. **テスト環境を設定する**: テスト環境には、ソース データベースとターゲット データベースのコピーが含まれている必要があります。 必ずテスト環境を分離してください。
1. **検証テストを実行する**: ソースとターゲットに対して検証テストを実行してから、結果を分析します。
1. **パフォーマンス テストを実行する**: ソースとターゲットに対してパフォーマンス テストを実行し、結果を分析して比較します。


## <a name="leverage-advanced-features"></a>高度な機能を活用する 

[組み込みの高可用性](../../database/high-availability-sla.md)、[脅威検出](../../database/azure-defender-for-sql.md)、[ワークロードの監視と調整](../../database/monitor-tune-overview.md)など、SQL Database によって提供されるクラウドベースの高度な機能を利用してください。 

一部の SQL Server 機能は、[データベース互換性レベル](/sql/relational-databases/databases/view-or-change-the-compatibility-level-of-a-database)を最新の互換性レベル (150) に変更した場合にのみ使用できます。 

詳細については、[移行後の Azure SQL Database の管理](../../database/manage-data-after-migrating-to-database.md)に関するページを参照してください


## <a name="next-steps"></a>次のステップ

- さまざまなデータベースとデータの移行シナリオ、および特殊なタスクを支援するために使用できる Microsoft とサードパーティのサービスとツールのマトリックスについては、[データ移行のサービスとツール](../../../dms/dms-tools-matrix.md)に関するページを参照してください。

- SQL Database の詳細については、以下を参照してください。
    - [Azure SQL Database の概要](../../database/sql-database-paas-overview.md)
   - [Azure 総保有コスト計算ツール](https://azure.microsoft.com/pricing/tco/calculator/) 


- クラウド移行のためのフレームワークと導入サイクルの詳細については、以下を参照してください
   -  [Azure 向けのクラウド導入フレームワーク](/azure/cloud-adoption-framework/migrate/azure-best-practices/contoso-migration-scale)
   -  [Azure に移行するワークロードの料金計算とサイズ設定のベスト プラクティス](/azure/cloud-adoption-framework/migrate/azure-best-practices/migrate-best-practices-costs) 

- アプリケーション アクセス層を評価するには、「[Data Access Migration Toolkit (プレビュー)](https://marketplace.visualstudio.com/items?itemName=ms-databasemigration.data-access-migration-toolkit)」を参照してください。
- データ アクセス レイヤーの A/B テストの実行方法について詳しくは、[Database Experimentation Assistant](/sql/dea/database-experimentation-assistant-overview) についてのページを参照してください。