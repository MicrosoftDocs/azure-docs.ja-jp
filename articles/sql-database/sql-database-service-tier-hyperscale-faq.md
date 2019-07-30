---
title: Azure SQL Database ハイパースケールに関する FAQ | Microsoft Docs
description: ハイパースケール サービス レベルの Azure SQL データベース (通常はハイパースケール データベースと呼ばれる) に関する顧客からの一般的な質問への回答。
services: sql-database
ms.service: sql-database
ms.subservice: ''
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
manager: craigg
ms.date: 05/06/2019
ms.openlocfilehash: 49d1e171d4d4b2210a98c59332f4842e23a2f2b9
ms.sourcegitcommit: 084630bb22ae4cf037794923a1ef602d84831c57
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/03/2019
ms.locfileid: "67537850"
---
# <a name="faq-about-azure-sql-hyperscale-databases"></a>Azure SQL ハイパースケール データベースに関する FAQ

この記事では、Azure SQL Database ハイパースケール サービス レベルのデータベース (通常はハイパースケール データベースと呼ばれる) をご検討中のお客様向けに、よく寄せられる質問の回答を紹介します。 この記事では、ハイパースケールでサポートするシナリオを示し、一般的に機能間サービスが SQL Database ハイパースケールと互換性があることを説明します。

- この FAQ は、ハイパースケール サービス レベルの概要を理解し、具体的な質問や懸案事項の回答を求める読者を対象としています。
- この FAQ はガイドブックではなく、SQL Database ハイパースケールの使用方法に関する質問に回答するものでもありません。 そのような場合は、[Azure SQL Database ハイパースケール](sql-database-service-tier-hyperscale.md)に関する記事をご覧ください。

## <a name="general-questions"></a>一般的な質問

### <a name="what-is-a-hyperscale-database"></a>ハイパースケール データベースとは

ハイパースケール データベースとは、ハイパースケール スケールアウト ストレージ テクノロジによって支えられている、ハイパースケール サービス レベルの Azure SQL データベースです。 ハイパースケール データベースは最大 100 TB のデータをサポートし、高いスループットとパフォーマンスを実現すると同時に、ワークロード要件に適応するために迅速にスケーリングできます。 スケーリングは、アプリケーションにとって透過的に行われます。接続やクエリ処理などは他のすべての SQL データベースと変わりません。

### <a name="what-resource-types-and-purchasing-models-support-hyperscale"></a>ハイパースケールをサポートするリソースの種類と購入モデル

ハイパースケール サービス レベルを利用できるのは、Azure SQL データベース において仮想コアベースの購入モデルを使用する単一のデータベースのみです。  

### <a name="how-does-the-hyperscale-service-tier-differ-from-the-general-purpose-and-business-critical-service-tiers"></a>ハイパースケール サービス レベルと General Purpose サービス レベルおよび Business Critical サービス レベルとの違い

仮想コアベースのサービス レベルは、主に、可用性、ストレージの種類、IOP に基づいて区別されます。

- General Purpose サービス レベルは、ほとんどのビジネス ワークロードに適しています。IO 待ち時間またはフェールオーバー回数が最優先ではない場合に、コンピューティングとストレージのオプションのバランスが取れた組み合わせを提供します。
- ハイパースケール サービス レベルは、非常に大規模なデータベース ワークロードに対して最適化されています。
- Business Critical サービス レベルは、IO 待ち時間が最優先されるビジネス ワークロードに適しています。

| | リソースの種類 | 汎用 |  ハイパースケール | Business Critical |
|:---|:---:|:---:|:---:|:---:|:---:|
| **最適な用途** |All|  ほとんどのビジネス ワークロード。 予算重視のバランスの取れたコンピューティングおよびストレージ オプションを提供します。 | 大きなデータ容量要件のデータ アプリケーション、および柔軟性の高いストレージの自動スケーリングとコンピューティングのスケーリングの機能。 | トランザクション レートが高く、待ち時間 IO が最低のOLTP アプリケーション。 分離された複数のレプリカを使用して、最高の耐障害性が提供されます。|
|  **リソースの種類** ||単一データベース/エラスティック プール/マネージド インスタンス | 単一データベース | 単一データベース/エラスティック プール/マネージド インスタンス |
| **コンピューティング サイズ**|単一データベース/エラスティック プール * | 1 - 80 の仮想コア | 1 - 80 の仮想コア * | 1 - 80 の仮想コア |
| |マネージド インスタンス | 8、16、24、32、40、64、80 の仮想コア | 該当なし | 8、16、24、32、40、64、80 の仮想コア |
| **ストレージの種類** | All |Premium リモート ストレージ (インスタンスあたり) | ローカル SSD キャッシュを使用して切り離したストレージ (インスタンスあたり) | 超高速ローカル SSD ストレージ (インスタンスあたり) |
| **ストレージ サイズ** | 単一データベース/エラスティック プール | 5 GB – 4 TB | 最大 100 TB | 5 GB – 4 TB |
| | マネージド インスタンス  | 32 GB – 8 TB | 該当なし | 32 GB – 4 TB |
| **IO スループット** | 単一データベース ** | 仮想コアあたり 500 IOPS (最大 7000 IOPS) | Hyperscale は、複数のレベルのキャッシュが存在する複数レベル アーキテクチャです。 有効な IOPS はワークロードによって異なります。 | 5000 IOPS (最大 200,000 IOPS)|
| | マネージド インスタンス | ファイル サイズに依存 | 該当なし | Managed Instance:ファイル サイズに依存|
|**可用性**|All|1 レプリカ、読み取りスケールなし、ローカル キャッシュなし | 複数のレプリカ、最大 15 の読み取りスケール、部分的ローカル キャッシュ | 3 レプリカ、1 読み取りスケール、ゾーン冗長 HA、完全ローカル キャッシュ |
|**バックアップ**|All|RA-GRS、7 ～ 35 日 (既定では 7 日)| RA-GRS、7 から 35 日 (既定では 7 日)、一定時間で特定の時点に復旧 (PITR) | RA-GRS、7 ～ 35 日 (既定では 7 日) |

\*エラスティック プールはハイパースケール サービス レベルではサポートされていません。

### <a name="who-should-use-the-hyperscale-service-tier"></a>ハイパースケール サービス レベルを使用する必要があるユーザー

ハイパースケール サービス レベルの対象として主に意図されているのは、大規模なオンプレミスの SQL Server データベースを持っていて、クラウドに移行することによってアプリケーションを最新化することを考えているお客様、または既に Azure SQL Database を使用しており、データベースのサイズを増大するために大幅な拡張を望むお客様です。 ハイパースケールでは、高いパフォーマンスと高いスケーラビリティを必要としているお客様も対象になります。 ハイパースケールで得られるのは次のとおりです。

- 最大 100 TB のデータベース サイズのサポート
- データベース サイズにかかわらないデータベースの高速バックアップ (ファイル スナップショットに基づいたバックアップ)
- データベース サイズにかかわらないデータベースの高速復元 (ファイル スナップショットに基づいた復元)
- ログ スループットの向上によるデータベースのサイズにかかわらないトランザクション コミット時間の短縮
- 1 つ以上の読み取り専用ノードへの読み取りスケールアウト (読み取りワークロードのオフロードやホット スタンバイに対応)
- コンピューティングの一定時間での迅速なスケールアップ (重いワークロードに対応するために性能を向上) と、その後の一定時間でのスケールダウン。 これは、たとえば P6 から P11 でのスケール アップとスケール ダウンに似ていますが、さらに迅速に行われます。データ操作の規模ではないためです。

### <a name="what-regions-currently-support-hyperscale"></a>現在ハイパースケールをサポートしているリージョン

Azure SQL Database の Hyperscale レベルは現在、[Azure SQL Database の Hyperscale の概要](sql-database-service-tier-hyperscale.md#regions)に関するページに列挙されているリージョンで利用可能です。

### <a name="can-i-create-multiple-hyperscale-databases-per-logical-server"></a>論理サーバーごとに複数のハイパースケール データベースを作成できるか

はい。 論理サーバーあたりのハイパースケール データベース数の詳細や制約について詳しくは、「[SQL Database resource limits for single and pooled databases on a logical server](sql-database-resource-limits-logical-server.md)」(論理サーバー上の単一データベースおよびプールされたデータベースの SQL Database のリソース制限) をご覧ください。

### <a name="what-are-the-performance-characteristics-of-a-hyperscale-database"></a>ハイパースケール データベースのパフォーマンス特性とは

SQL Database ハイパースケール アーキテクチャは、大きなデータベース サイズをサポートする一方で高いパフォーマンスとスループットを提供します。 

### <a name="what-is-the-scalability-of-a-hyperscale-database"></a>ハイパースケール データベースのスケーラビリティとは

SQL Database ハイパースケールでは、ワークロードの需要に基づいて迅速なスケーラビリティを提供します。

- **スケールアップ/スケールダウン**

  ハイパースケールでは、CPU やメモリなどリソースの観点で主要なコンピューティング サイズをスケールアップしてから、一定時間でスケールダウンできます。 ストレージは共有されるため、スケールアップとスケールダウンはデータ操作の規模ではありません。  
- **スケールイン/スケールアウト**

  ハイパースケールでは、読み取り要求を処理するために使用できる 1 つ以上の追加計算ノードをプロビジョニングする機能も提供されます。 つまり、これらの追加計算ノードを読み取り専用ノードとして使用し、主なコンピューティングから読み取りワークロードをオフロードできます。 読み取り専用だけでなく、これらのノードは、プライマリからのフェールオーバーの場合にホット スタンバイとしても機能します。

  これらの追加計算ノードそれぞれのプロビジョニングは、オンライン操作であり、一定時間で行うことができます。 これらの追加読み取り専用計算ノードに接続するには、接続文字列の `ApplicationIntent` 引数を `readonly` に設定してください。 `readonly` がマークされたすべての接続は、追加読み取り専用計算ノードのいずれかに自動的にルーティングされます。

## <a name="deep-dive-questions"></a>具体的な質問

### <a name="can-i-mix-hyperscale-and-single-databases-in-a-single-logical-server"></a>1 つの論理サーバー上でハイパースケールと単一データベースを混在できるか

はい、できます。

### <a name="does-hyperscale-require-my-application-programming-model-to-change"></a>ハイパースケールのためにアプリケーション プログラミング モデルを変更する必要があるか

いいえ。アプリケーション プログラミング モデルはそのままでかまいません。 いつもの接続文字列とその他の通常モードを使用して、Azure SQL データベースを操作します。

### <a name="what-transaction-isolation-levels-are-going-to-be-default-on-sql-database-hyperscale-database"></a>どのトランザクション分離レベルが SQL Database ハイパースケール データベースで既定になるか

プライマリ ノードでは、トランザクション分離レベルは RCSI (Read Committed スナップショット分離) です。 読み取りスケールのセカンダリ ノードでは、分離レベルはスナップショットです。

### <a name="can-i-bring-my-on-premises-or-iaas-sql-server-license-to-sql-database-hyperscale"></a>オンプレミスまたは IaaS SQL Server ライセンスを SQL Database ハイパースケールで使用できるか

はい。[Azure ハイブリッド特典](https://azure.microsoft.com/pricing/hybrid-benefit/)をハイパースケールで利用できます。 SQL Server Standard コアそれぞれを 1 つのハイパースケール仮想コアにマップできます。 SQL Server Enterprise コアそれぞれを 4 つのハイパースケール仮想コアにマップできます。 セカンダリ レプリカの SQL ライセンスは不要です。 Azure ハイブリッド特典の価格が読み取りスケール (セカンダリ) レプリカに自動的に適用されます。

### <a name="what-kind-of-workloads-is-sql-database-hyperscale-designed-for"></a>SQL Database ハイパースケールはどのようなワークロードを対象としているか

SQL Database ハイパースケールはすべての SQL Server ワークロードをサポートしますが、主に OLTP 用に最適化されています。 ハイブリッド (HTAP) および分析 (データ マート) のワークロードにも対応できます。

### <a name="how-can-i-choose-between-azure-sql-data-warehouse-and-sql-database-hyperscale"></a>Azure SQL Data Warehouse と SQL Database ハイパースケールのどちらを選ぶべきか

現在、SQL Server をデータ ウェアハウスとして使用して対話型分析クエリを実行している場合、SQL Database ハイパースケールがオプションとして優れています。比較的小さなデータ ウェアハウス (数 TB から最大でも数十 TB) をより低コストでホストでき、T-SQL コードを変更せずにデータ ウェアハウス ワークロードを SQL Database ハイパースケールに移行できるためです。

Parallel Data Warehouse (PDW)、Teradata、またはその他の超並列プロセッサー (MPP) データ ウェアハウスを使用して、複雑なクエリで大規模にデータ分析を実行している場合は、SQL Data Warehouse が最適な選択肢となるでしょう。
  
## <a name="sql-database-hyperscale-compute-questions"></a>SQL Database ハイパースケールのコンピューティングに関する質問

### <a name="can-i-pause-my-compute-at-any-time"></a>コンピューティングをいつでも一時停止できるか

現時点ではできませんが、コンピューティングとレプリカの数をスケールダウンして、ピーク時以外のコストを削減することができます。

### <a name="can-i-provision-a-compute-with-extra-ram-for-my-memory-intensive-workload"></a>メモリ集中型ワークロードのために RAM を増やしてコンピューティングをプロビジョニングできるか

いいえ。 RAM を増やすには、コンピューティング サイズをアップグレードして上げる必要があります。 詳しくは、[ハイパースケールのストレージ サイズおよびコンピューティング サイズ](sql-database-vcore-resource-limits-single-databases.md#hyperscale-service-tier)をご覧ください。

### <a name="can-i-provision-multiple-compute-nodes-of-different-sizes"></a>サイズが違う複数の計算ノードをプロビジョニングできるか

いいえ。

### <a name="how-many-read-scale-replicas-are-supported"></a>サポートされる読み取りスケール レプリカの数

ハイパースケール データベースは、既定で 1 つの読み取りスケール レプリカを含むように作成されます (合計 2 つのレプリカ)。 [Azure portal](https://portal.azure.com)、[T-SQL](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql?view=azuresqldb-current)、[PowerShell](https://docs.microsoft.com/powershell/module/azurerm.sql/set-azurermsqldatabase) または [CLI](https://docs.microsoft.com/cli/azure/sql/db#az-sql-db-update) を使用して、読み取り専用レプリカの数を 0 から 4 の間でスケールすることができます。

### <a name="for-high-availability-do-i-need-to-provision-additional-compute-nodes"></a>高可用性のために追加の計算ノードをプロビジョニングできるか

ハイパースケール データベースでは、回復性はストレージ レベルで提供されます。 回復性を実現するために必要なのは 1 つのレプリカだけです。 コンピューティング レプリカが停止すると、新しいレプリカが自動的に作成されるためデータは失われません。

ただし、レプリカが 1 つしかない場合は、フェールオーバー後に新しいレプリカにローカル キャッシュを構築するために少し時間がかかることがあります。 キャッシュ再構築フェーズで、データベースはページ サーバーからデータを直接フェッチするため、IOPS とクエリのパフォーマンスが低下します。

高可用性を必要とするミッション クリティカル アプリケーションでは、プライマリ計算ノード (既定) を含めて少なくとも 2 つの計算ノードをプロビジョニングする必要があります。 そうすることで、フェールオーバー時のホットスタンバイを用意できます。

## <a name="data-size-and-storage-questions"></a>データ サイズとストレージに関する質問

### <a name="what-is-the-max-db-size-supported-with-sql-database-hyperscale"></a>SQL Database ハイパースケールでサポートされる最大データベース サイズとは

100 TB

### <a name="what-is-the-size-of-the-transaction-log-with-hyperscale"></a>ハイパースケールでのトランザクション ログのサイズとは

ハイパースケールでのトランザクション ログは実質的に無制限です。 ログ スループットが高いシステム上では、ログ領域の不足を心配する必要はありません。 ただし、高いワークロードが継続する場合にはログ生成速度が調整される可能性があります。 ピークが持続するログ生成速度は、約 100 MB/秒です。

### <a name="does-my-temp-db-scale-as-my-database-grows"></a>データベースのサイズ増大につれて一時データベースをスケーリングできるか

`tempdb` データベースはローカル SSD ストレージに配置され、プロビジョニングするコンピューティング サイズに基づいて構成されます。 `tempdb` は、最大のパフォーマンス メリットを得られるように最適化およびレイアウトされています。 `tempdb` サイズを構成することはできません。これは記憶域サブシステムによって管理されます。

### <a name="does-my-database-size-automatically-grow-or-do-i-have-to-manage-the-size-of-the-data-files"></a>データベース サイズが自動的に拡張されるか、データ ファイルのサイズを自分で管理する必要があるか

データベースのサイズは、データの挿入/取り込みに応じて自動的に拡大します。

### <a name="what-is-the-smallest-database-size-that-sql-database-hyperscale-supports-or-starts-with"></a>SQL Database ハイパースケールでサポートされる最小データベース サイズ (ハイパースケールを使用開始できる最小サイズ) とは

10 GB

### <a name="in-what-increments-does-my-database-size-grow"></a>データベースのサイズが拡張される単位とは

1 GB

### <a name="is-the-storage-in-sql-database-hyperscale-local-or-remote"></a>SQL Database ハイパースケールのストレージはローカルかリモートか

ハイパースケールでは、データ ファイルは Azure Standard ストレージに格納されます。 計算ノードに近いマシンでは、データは重点的にローカル SSD ストレージにキャッシュされます。 さらに、計算ノードは、ローカル SSD とメモリ内 (バッファ プールなど) にキャッシュを持ち、リモート ノードからデータをフェッチする回数を減らします。

### <a name="can-i-manage-or-define-files-or-filegroups-with-hyperscale"></a>ハイパースケールを使用してファイルまたはファイルグループを管理または定義できるか

いいえ
  
### <a name="can-i-provision-a-hard-cap-on-the-data-growth-for-my-database"></a>データベースのデータ増加にハード キャップをプロビジョニングできるか

いいえ

### <a name="how-are-data-files-laid-out-with-sql-database-hyperscale"></a>SQL Database ハイパースケールではどのようにデータ ファイルがレイアウトされるか

データ ファイルはページ サーバーによって制御されます。 データ サイズが大きくなると、データ ファイルと関連するページ サーバー ノードが追加されます。

### <a name="is-database-shrink-supported"></a>データベースの縮小はサポートされるか

いいえ

### <a name="is-database-compression-supported"></a>データベースの圧縮はサポートされるか

はい

### <a name="if-i-have-a-huge-table-does-my-table-data-get-spread-out-across-multiple-data-files"></a>大きなテーブルのデータを複数のデータ ファイルに分散できるか

はい。 特定のテーブルに関連付けられたデータ ページを、同じファイルグループに含まれる複数のデータ ファイルに分散することができます。 SQL Server は、[比例配分方式](https://docs.microsoft.com/sql/relational-databases/databases/database-files-and-filegroups#file-and-filegroup-fill-strategy)を使用してデータをデータ ファイルに均等配置します。

## <a name="data-migration-questions"></a>データの移行に関する質問

### <a name="can-i-move-my-existing-azure-sql-databases-to-the-hyperscale-service-tier"></a>既存の Azure SQL データベースをハイパースケール サービス レベルに移行できるか

はい。 ハイパースケールには既存の Azure SQL データベースを移行できます。 これは一方向にしか移行できません。 ハイパースケールから他のサービス レベルにデータベースを移行することはできません。 運用データベースのコピーを作成して、概念実証 (POC) のためにハイパースケールに移行することをお勧めします。
  
### <a name="can-i-move-my-hyperscale-databases-to-other-editions"></a>ハイパースケール データベースを他のエディションに移行できるか

いいえ。 現時点では、ハイパースケール データベースを別のサービス レベルに移行することはできません。

### <a name="do-i-lose-any-functionality-or-capabilities-after-migration-to-the-hyperscale-service-tier"></a>ハイパースケール サービス レベルに移行した後で使えなくなる機能があるか

はい。 長期的な保有期間のバックアップを始めとする Azure SQL Database の一部の機能は、まだハイパースケールではサポートされていません。 ハイパースケールにデータベースを移行した後、これらの機能は動作を停止します。  これらの制限は一時的なものであると想定しています。

### <a name="can-i-move-my--on-premises-sql-server-database-or-my-sql-server-virtual-machine-database-to-hyperscale"></a>オンプレミスの SQL Server データベースまたは my SQL Server 仮想マシン データベースをハイパースケールに移行できるか

はい。 BACPAC、トランザクション レプリケーション、論理データ読み込みなど、既存のすべての移行方法を使用して、ハイパースケールに移行できます。 「[Azure Database Migration Service とは](../dms/dms-overview.md)」もご覧ください。

### <a name="what-is-my-downtime-during-migration-from-an-on-premises-or-virtual-machine-environment-to-hyperscale-and-how-can-i-minimize-it"></a>オンプレミスまたは仮想マシン環境からハイパースケールへの移行時のダウンタイム、および最小限にするための方法

ダウンタイムは、データベースを Azure SQL Database 内の単一データベースに移行する際のダウンタイムと同じです。 [トランザクション レプリケーション](replication-to-sql-database.md#data-migration-scenario
)を使用すると、サイズが数 TB までのデータベースについて移行時のダウンタイムを最小限に抑えることができます。 特に大規模なデータベース (10 TB 超) の場合は、ADF、Spark、またはその他のデータ移動方法を使用してデータを移行することを検討してください。

### <a name="how-much-time-would-it-take-to-bring-in-x-amount-of-data-to-sql-database-hyperscale"></a>容量 X のデータを SQL Database ハイパースケールに移行するためにかかる時間

ハイパースケールでは、新しいデータまたは変更されたデータを 100 MB/秒使用できます。

### <a name="can-i-read-data-from-blob-storage-and-do-fast-load-like-polybase-and-sql-data-warehouse"></a>BLOB ストレージからデータを読み取って高速読み込みできるか (Polybase や SQL Data Warehouse のように)

Azure Storage からデータを読み取り、ハイパースケール データベースに読み込むことができます (通常の単一データベースの場合と同様です)。 Polybase は現在 Azure SQL Database でサポートされていません。 Polybase を実行するには、[Azure Data Factory](https://docs.microsoft.com/azure/data-factory/) を使用するか、[SQL 用 Spark コネクタ](sql-database-spark-connector.md)を使用して Spark ジョブを [Azure Databricks](https://docs.microsoft.com/azure/azure-databricks/) で実行します。 SQL 用の Spark コネクタでは一括挿入がサポートされます。

単純復旧モデルまたは一括ログ記録モデルはハイパースケールではサポートされていません。 高可用性を実現するには完全復旧モデルが必要です。 ただし、ハイパースケールでは、単一の Azure SQL データベースと比較して、新しいログ アーキテクチャのためにデータ取り込み率が向上しています。

### <a name="does-sql-database-hyperscale-allow-provisioning-multiple-nodes-for-ingesting-large-amounts-of-data"></a>SQL Database ハイパースケールは取り込むデータ容量を増やすために複数のノードをプロビジョニングできるか

いいえ。 SQL Database ハイパースケールは SMP アーキテクチャです。非対称マルチプロセッシングまたはマルチマスター アーキテクチャではありません。 読み取り専用ワークロードをスケールアウトするためには複数のレプリカを作成するしかありません。

### <a name="what-is-the-oldest-sql-server-version-will-sql-database-hyperscale-support-migration-from"></a>SQL Database ハイパースケールへの移行がサポートされる最も古い SQL Server バージョンとは

SQL Server 2005。 詳しくは、「[単一データベースまたはプールされたデータベースに移行する](sql-database-single-database-migrate.md#migrate-to-a-single-database-or-a-pooled-database)」をご覧ください。 互換性の問題について詳しくは、「[データベース移行に関する互換性の問題の解決](sql-database-single-database-migrate.md#resolving-database-migration-compatibility-issues)」をご覧ください。

### <a name="does-sql-database-hyperscale-support-migration-from-other-data-sources-such-as-aurora-mysql-oracle-db2-and-other-database-platforms"></a>SQL Database ハイパースケールで他のデータ ソース (Aurora、MySQL、Oracle、DB2、その他のデータベース プラットフォーム) からの移行がサポートされるか

はい。 SQL Server 以外のさまざまなデータ ソースから移行するには、論理的な移行が必要です。 論理的な移行では [Azure Database Migration Service](../dms/dms-overview.md) を使用できます。

## <a name="business-continuity-and-disaster-recovery-questions"></a>ビジネス継続性とディザスター リカバリーに関する質問

### <a name="what-slas-are-provided-for-a-hyperscale-database"></a>ハイパースケール データベースではどのような SLA が提供されるか

既定のプライマリと 1 つの読み取り可能なセカンダリでは、SLA は 99.95% の可用性です。  複数のレプリカでは、SLA は 99.99% まで上がります。  

### <a name="are-the-database-backups-managed-for-me-by-the-azure-sql-database-service"></a>Azure SQL Database サービスによってデータベース バックアップが管理されるか

はい

### <a name="how-often-are-the-database-backups-taken"></a>データベース バックアップの実行頻度

SQL Database ハイパースケール データベースでは、従来の完全バックアップ、差分バックアップ、およびログ バックアップは存在しません。 代わりに、データ ファイルの定期スナップショットと生成されるログがあり、構成した保存期間中はそのまま単純に保存され、ユーザーが利用できます。

### <a name="does-sql-database-hyperscale-support-point-in-time-restore"></a>SQL Database ハイパースケールで特定の時点への復元はサポートされるか

はい

### <a name="what-is-the-recovery-point-objective-rporecovery-time-objective-rto-with-backuprestore-in-sql-database-hyperscale"></a>SQL Database ハイパースケールにおいてバックアップ/復元の回復ポイントの目標 (RPO)/回復時刻の目標 (RTO) とは

RPO は 0 分です。RTO はデータベース サイズにかかわらず 10 分未満です。 

### <a name="do-backups-of-large-databases-affect-compute-performance-on-my-primary"></a>大規模データベースのバックアップがプライマリ上のコンピューティング パフォーマンスに影響するか

いいえ。 バックアップは記憶域サブシステムによって管理され、ファイル スナップショットを利用します。 プライマリ上のユーザー ワークロードには影響しません。

### <a name="can-i-perform-geo-restore-with-a-sql-database-hyperscale-database"></a>SQL Database ハイパースケール データベースを使用して geo リストアを実行できるか

はい。  geo リストアは完全にサポートされています。

### <a name="can-i-setup-geo-replication-with-sql-database-hyperscale-database"></a>SQL Database ハイパースケール データベースを使用して geo レプリケーションを設定できるか

現時点ではありません。

### <a name="do-my-secondary-compute-nodes-get-geo-replicated-with-sql-database-hyperscale"></a>SQL Database ハイパースケールを使用してセカンダリ計算ノードの geo レプリケーションを実行できるか

現時点ではありません。

### <a name="can-i-take-a-sql-database-hyperscale-database-backup-and-restore-it-to-my-on-premises-server-or-sql-server-in-vm"></a>SQL Database ハイパースケール データベースのバックアップを使ってオンプレミス サーバーまたは VM の SQL Server を復元できるか

いいえ。 ハイパースケール データベースのストレージ形式は、従来の SQL Server とは異なります。バックアップの管理またはバックアップへのアクセスはできません。 SQL Database ハイパースケール データベースからデータを取り出すには、エクスポート サービスを使用するか、スクリプトと BCP を使用します。

## <a name="cross-feature-questions"></a>移行前後の機能に関する質問

### <a name="do-i-lose-any-functionality-or-capabilities-after-migration-to-the-hyperscale-service-tier"></a>ハイパースケール サービス レベルに移行した後で使えなくなる機能があるか

はい。 長期的な保有期間のバックアップを始めとする Azure SQL Database の一部の機能は、ハイパースケールではサポートされていません。 ハイパースケールにデータベースを移行した後、これらの機能は動作を停止します。

### <a name="will-polybase-work-with-sql-database-hyperscale"></a>Polybase は SQL Database ハイパースケールで作動するか

いいえ。 Polybase は Azure SQL Database でサポートされていません。

### <a name="does-the-compute-have-support-for-r-and-python"></a>コンピューティングで R と Python がサポートされるか

いいえ。 R と Python は Azure SQL Database でサポートされていません。

### <a name="are-the-compute-nodes-containerized"></a>コンピューティング ノードはコンテナー化されるか

いいえ。 データベースは、コンテナー上ではなくコンピューティング VM 上に存在しています。

## <a name="performance-questions"></a>パフォーマンスに関する質問

### <a name="how-much-throughput-can-i-push-on-the-largest-sql-database-hyperscale-compute"></a>最大の SQL Database ハイパースケール コンピューティングでどれくらいスループットを向上できるか

一貫した 100 MB/秒のデータの変更 (トランザクション ログ データの生成) が見られます

### <a name="how-many-iops-do-i-get-on-the-largest-sql-database-hyperscale-compute"></a>最大の SQL Database ハイパースケール コンピューティングにおいて得られる IOPS 数

IOPS と IO 待ち時間は、ワークロードのパターンによって異なります。  アクセスされる必要があるデータがコンピューティングのキャッシュに対してローカルの場合、ローカル SSD と同じ IO パターンになります。   

### <a name="does-my-throughput-get-affected-by-backups"></a>スループットがバックアップの影響を受けるか

いいえ。 コンピューティングへの影響を避けるために、コンピューティングはストレージ レイヤーから切り離されます。

### <a name="does-my-throughput-get-affected-as-i-provision-additional-compute-nodes"></a>追加の計算ノードをプロビジョニングするとスループットが影響を受けるか

ストレージが共有されており、プライマリ計算ノードとセカンダリ計算ノードの間では物理的なレプリケーションが直接発生しないために、技術的には、プライマリ ノード上のスループットは読み取りスケール ノードを追加することによる影響を受けません。 ただし、継続する活発なワークロードを調整して、セカンダリ ノードとページ サーバー上でログを適用できます。これで遅れを取り戻し、セカンダリ ノード上での読み取りパフォーマンスの悪化を回避できます。

## <a name="scalability-questions"></a>スケーラビリティに関する質問

### <a name="how-long-would-it-take-to-scale-up-and-down-a-compute-node"></a>計算ノードのスケールアップとスケールダウンにかかる時間

コンピューティングのスケールアップまたはスケールダウンには、データ サイズに関係なく、5 分から 10 分かかります。

### <a name="is-my-database-offline-while-the-scaling-updown-operation-is-in-progress"></a>スケールアップ/スケールダウン操作の進行中にデータベースはオフラインになるか

いいえ。 スケールアップ/スケールダウンはオンラインで行われます。

### <a name="should-i-expect-connection-drop-when-the-scaling-operations-are-in-progress"></a>スケール操作が進行中に接続が切断されるか

スケールアップまたはスケールダウンによって既存の接続が切断されるのは、目標サイズの計算ノードへのフェールオーバーが発生したときです。 読み取りレプリカの追加では接続は切断されません。

### <a name="is-the-scaling-up-and-down-of-compute-nodes-automatic-or-end-user-triggered-operation"></a>計算ノードのスケールアップとスケールダウンは自動で行われるか (エンドユーザーが開始する操作か)

エンドユーザー。 自動ではありません。  

### <a name="does-my-tempb-also-grow-as-the-compute-is-scaled-up"></a>コンピューティングがスケールアップされると `tempb` も拡張されるか

はい。 一時 db は、コンピューティングの拡張に合わせて自動的にスケールアップされます。  

### <a name="can-i-provision-multiple-primary-compute-nodes-such-as-a-multi-master-system-where-multiple-primary-compute-heads-can-drive-a-higher-level-of-concurrency"></a>複数のプライマリ コンピューティング ヘッドでより高レベルの同時実行を推進できる、複数のプライマリ コンピューティング ノード (マルチマスター システムなど) をプロビジョニングできるか

いいえ。 読み取り/書き込み要求を受け入れるのはプライマリ計算ノードのみです。 セカンダリ計算ノードは、読み取り専用要求のみを受け入れます。

## <a name="read-scale-questions"></a>読み取りスケールに関する質問

### <a name="how-many-secondary-compute-nodes-can-i-provision"></a>セカンダリ計算ノードをいくつプロビジョニングできるか

既定では、ハイパースケール データベース用にレプリカを 2 つ作成します。 レプリカの数を調整する場合は、[Azure portal](https://portal.azure.com) を使用して行うことができます。

### <a name="how-do-i-connect-to-these-secondary-compute-nodes"></a>これらのセカンダリ コンピューティング ノードにどのように接続するか

これらの追加読み取り専用計算ノードに接続するには、接続文字列の `ApplicationIntent` 引数を `readonly` に設定してください。 `readonly` がマークされたすべての接続は、追加読み取り専用計算ノードのいずれかに自動的にルーティングされます。  

### <a name="can-i-create-a-dedicated-endpoint-for-the-read-scale-replica"></a>読み取りスケール レプリカ専用のエンドポイントを作成できるか

いいえ。 `ApplicationIntent=ReadOnly` と指定した場合のみ、読み取りスケール レプリカに接続できます。

### <a name="does-the-system-do-intelligent-load-balancing-of-the-read-workload"></a>読み取りワークロードのインテリジェントな負荷分散がシステムで行われるか

いいえ。 読み取り専用ワークロードは、ランダムな読み取りスケール レプリカにリダイレクトされます。

### <a name="can-i-scale-updown-the-secondary-compute-nodes-independently-of-the-primary-compute"></a>セカンダリ計算ノードをプライマリ コンピューティングと別にスケールアップ/スケールダウンできるか

いいえ。 セカンダリ コンピューティング ノードは HA のためにも使用されるため、フェールオーバーの場合は、プライマリと同じ構成にする必要があります。

### <a name="do-i-get-different-temp-db-sizing-for-my-primary-compute-and-my-additional-secondary-compute-nodes"></a>プライマリ計算ノードと追加のセカンダリ計算ノードで異なる一時 db サイズを設定できるか

いいえ。 `tempdb` はコンピューティング サイズのプロビジョニングに基づいて構成され、セカンダリ計算ノードはプライマリ コンピューティングと同じサイズです。

### <a name="can-i-add-indexes-and-views-on-my-secondary-compute-nodes"></a>セカンダリ計算ノードにインデックスとビューを追加できるか

いいえ。 ハイパースケール データベースではストレージが共有されます。つまり、すべての計算ノードは同じテーブル、インデックス、ビューを見ることができます。 読み取りに対して最適化したインデックスをセカンダリに追加したい場合は、まずプライマリに追加する必要があります。

### <a name="how-much-delay-is-there-going-to-be-between-the-primary-and-secondary-compute-node"></a>プライマリ計算ノードとセカンダリ計算ノードの間の遅延はどれくらいか

ログ生成速度によって異なりますが、トランザクションがプライマリ上でコミットされた時点から、すぐ、または数ミリ秒です。

## <a name="next-steps"></a>次の手順

ハイパースケール サービス レベルについて詳しくは、[ハイパースケール サービス レベル](sql-database-service-tier-hyperscale.md)に関するページをご覧ください。
