---
title: Azure SQL Database ハイパースケールに関する FAQ
description: ハイパースケール サービス レベルの SQL Database のデータベース (通常はハイパースケール データベースと呼ばれる) に関する顧客からの一般的な質問への回答。
services: sql-database
ms.service: sql-database
ms.subservice: ''
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: dimitri-furman
ms.author: dfurman
ms.reviewer: ''
ms.date: 02/03/2021
ms.openlocfilehash: c6faa1703b0935d66d291281f33027b3a66a59d4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "99526824"
---
# <a name="azure-sql-database-hyperscale-faq"></a>Azure SQL Database ハイパースケールに関する FAQ
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

この記事では、Azure SQL Database ハイパースケール サービス レベル (以降、この FAQ ではハイパースケールと呼びます) のデータベースをご検討中のお客様向けに、よく寄せられる質問の回答を紹介します。 この記事では、ハイパースケールでサポートするシナリオと、ハイパースケールと互換性がある機能について説明します。

- この FAQ は、ハイパースケール サービス レベルの概要を理解し、具体的な質問や懸案事項の回答を求める読者を対象としています。
- この FAQ はガイドブックではなく、ハイパースケール データベースの使用方法に関する質問に回答するものでもありません。 ハイパースケールの概要については、[Azure SQL Database ハイパースケール](service-tier-hyperscale.md)に関する記事をご覧ください。

## <a name="general-questions"></a>一般的な質問

### <a name="what-is-a-hyperscale-database"></a>ハイパースケール データベースとは

ハイパースケール データベースとは、ハイパースケール スケールアウト ストレージ テクノロジによって支えられている、ハイパースケール サービス レベルの SQL Database データベースです。 ハイパースケール データベースは最大 100 TB のデータをサポートし、高いスループットとパフォーマンスを実現すると同時に、ワークロード要件に適応するために迅速にスケーリングできます。 スケーリングは、アプリケーションにとって透過的に行われます。接続やクエリ処理などは他のすべての Azure SQL Database のデータベースと変わりません。

### <a name="what-resource-types-and-purchasing-models-support-hyperscale"></a>ハイパースケールをサポートするリソースの種類と購入モデル

ハイパースケール サービス レベルを利用できるのは、Azure SQL データベース において仮想コアベースの購入モデルを使用する単一のデータベースのみです。  

### <a name="how-does-the-hyperscale-service-tier-differ-from-the-general-purpose-and-business-critical-service-tiers"></a>ハイパースケール サービス レベルと General Purpose サービス レベルおよび Business Critical サービス レベルとの違い

次の表で説明するように、仮想コアベースのサービス レベルは、データベースの可用性とストレージの種類、パフォーマンス、および最大サイズに基づいて区別されます。

| | リソースの種類 | General Purpose |  ハイパースケール | Business Critical |
|:---:|:---:|:---:|:---:|:---:|
| **最適な用途** |All|予算重視のバランスの取れたコンピューティングおよびストレージ オプションを提供します。|ほとんどのビジネス ワークロード。 最大 100 TB のストレージ サイズの自動スケーリング、垂直および水平方向への高速コンピューティング スケーリング、データベースの高速復元。|トランザクション レートが高く IO 待ち時間が低い OLTP アプリケーション。 同期的に更新された複数のレプリカを使用して、最高の耐障害性と高速フェールオーバーを提供します。|
|  **リソースの種類** ||SQL Database/SQL Managed Instance | 単一データベース | SQL Database/SQL Managed Instance |
| **コンピューティング サイズ**|SQL Database* | 1 - 80 の仮想コア | 1 - 80 の仮想コア * | 1 - 80 の仮想コア |
| **コンピューティング サイズ**|SQL Managed Instance | 8、16、24、32、40、64、80 の仮想コア | 該当なし | 8、16、24、32、40、64、80 の仮想コア |
| **ストレージの種類** | All |Premium リモート ストレージ (インスタンスあたり) | ローカル SSD キャッシュを使用して切り離したストレージ (インスタンスあたり) | 超高速ローカル SSD ストレージ (インスタンスあたり) |
| **ストレージ サイズ** | SQL Database *| 5 GB – 4 TB | 最大 100 TB | 5 GB – 4 TB |
| **ストレージ サイズ** | SQL Managed Instance  | 32 GB – 8 TB | 該当なし | 32 GB – 4 TB |
| **IOPS** | 単一データベース | 仮想コアあたり 500 IOPS (最大 7000 IOPS) | Hyperscale は、複数のレベルのキャッシュが存在する複数レベル アーキテクチャです。 有効な IOPS はワークロードによって異なります。 | 5000 IOPS (最大 200,000 IOPS)|
| **IOPS** | SQL Managed Instance | ファイル サイズによって異なる | 該当なし | 1375 IOPS/仮想コア |
|**可用性**|All|1 レプリカ、読み取りスケールアウトなし、ローカル キャッシュなし | 複数のレプリカ、最大 4 読み取りスケールアウト、部分的ローカル キャッシュ | 3 レプリカ、1 読み取りスケールアウト、ゾーン冗長 HA、完全ローカル ストレージ |
|**バックアップ**|All|RA-GRS、7 から 35 日のリテンション期間 (既定では 7 日)| RA-GRS、7 日のリテンション期間、一定時間で特定の時点に復旧 (PITR) | RA-GRS、7 から 35 日のリテンション期間 (既定では 7 日) |

\* エラスティック プールはハイパースケール サービス レベルではサポートされていません

### <a name="who-should-use-the-hyperscale-service-tier"></a>ハイパースケール サービス レベルを使用する必要があるユーザー

Hyperscale サービス レベルの対象として意図されているのは、大規模なオンプレミスの SQL Server データベースを備えていて、クラウドに移行することによりアプリケーションを最新化することを考えているお客様、または既に Azure SQL Database を使用しており、データベースのサイズを増大するために大幅な拡張を望むお客様です。 ハイパースケールでは、高いパフォーマンスと高いスケーラビリティを必要としているお客様も対象になります。 ハイパースケールで得られるのは次のとおりです。

- 最大 100 TB のデータベース サイズ
- データベース サイズにかかわらないデータベースの高速バックアップ (ストレージ スナップショットに基づいたバックアップ)
- データベース サイズにかかわらないデータベースの高速復元 (ストレージ スナップショットに基づいた復元)
- データベースのサイズと仮想コアの数にかかわらないログ スループットの向上
- 1 つ以上の読み取り専用レプリカを使用した読み取りスケールアウト (読み取りオフロードやホット スタンバイに対応)。
- コンピューティングの一定時間での迅速なスケールアップ (重いワークロードに対応するために性能を向上) と、その後の一定時間でのスケールダウン。 これは、たとえば P6 と P11 の間のスケールアップとスケール ダウンに似ていますが、さらに迅速に行われます。データ操作の規模ではないためです。

### <a name="what-regions-currently-support-hyperscale"></a>現在ハイパースケールをサポートしているリージョン

ハイパースケール サービス レベルは現在、[Azure SQL Database のハイパースケールの概要](service-tier-hyperscale.md#regions)のページに示されているリージョンで利用可能です。

### <a name="can-i-create-multiple-hyperscale-databases-per-server"></a>サーバーごとに複数のハイパースケール データベースを作成できるか

はい。 サーバーあたりのハイパースケール データベース数の詳細や制約について詳しくは、[サーバー上の単一データベースおよびプールされたデータベースの SQL Database のリソース制限](resource-limits-logical-server.md)に関するページをご覧ください。

### <a name="what-are-the-performance-characteristics-of-a-hyperscale-database"></a>ハイパースケール データベースのパフォーマンス特性とは

ハイパースケール アーキテクチャは、大きなデータベース サイズをサポートする一方でハイ パフォーマンスとスループットを提供します。

### <a name="what-is-the-scalability-of-a-hyperscale-database"></a>ハイパースケール データベースのスケーラビリティとは

ハイパースケールでは、ワークロードの需要に基づいて迅速なスケーラビリティを提供します。

- **スケールアップ/スケールダウン**

  ハイパースケールでは、CPU やメモリなどのリソースの観点で主要なコンピューティング サイズをスケールアップしてから、一定時間でスケールダウンできます。 ストレージは共有されるため、スケールアップとスケールダウンはデータ操作の規模ではありません。  
- **スケールイン/スケールアウト**

  ハイパースケールでは、読み取り要求を処理するために使用できる 1 つ以上の追加計算レプリカをプロビジョニングする機能も提供されます。 つまり、これらの追加計算レプリカを読み取り専用レプリカとして使用し、主なコンピューティングから読み取りワークロードをオフロードできます。 読み取り専用だけでなく、これらのレプリカは、プライマリからのフェールオーバーの場合にホット スタンバイとしても機能します。

  これらの追加計算レプリカそれぞれのプロビジョニングは、オンライン操作であり、一定時間で行うことができます。 これらの追加読み取り専用計算レプリカに接続するには、接続文字列の `ApplicationIntent` 引数を `ReadOnly` に設定してください。 `ReadOnly` アプリケーション インテントを持つ接続はすべて、追加読み取り専用計算レプリカのいずれかに自動的にルーティングされます。

## <a name="deep-dive-questions"></a>具体的な質問

### <a name="can-i-mix-hyperscale-and-single-databases-in-a-single-server"></a>1 つのサーバー上でハイパースケールと単一データベースを混在できるか

はい、できます。

### <a name="does-hyperscale-require-my-application-programming-model-to-change"></a>ハイパースケールのためにアプリケーション プログラミング モデルを変更する必要があるか

いいえ。アプリケーション プログラミング モデルはそのままでかまいません。 いつもの接続文字列とその他の通常の方法を使用して、ハイパースケール データベースを操作します。

### <a name="what-transaction-isolation-level-is-the-default-in-a-hyperscale-database"></a>どのトランザクション分離レベルがハイパースケール データベースで既定になるか

プライマリ レプリカでは、既定のトランザクション分離レベルは RCSI (Read Committed スナップショット分離) です。 読み取りスケールアウトのセカンダリ レプリカでは、既定の分離レベルはスナップショットです。

### <a name="can-i-bring-my-on-premises-or-iaas-sql-server-license-to-hyperscale"></a>オンプレミスまたは IaaS SQL Server ライセンスをハイパースケールで使用できるか

はい。[Azure ハイブリッド特典](https://azure.microsoft.com/pricing/hybrid-benefit/)をハイパースケールで利用できます。 SQL Server Standard コアそれぞれを 1 つのハイパースケール仮想コアにマップできます。 SQL Server Enterprise コアそれぞれを 4 つのハイパースケール仮想コアにマップできます。 セカンダリ レプリカの SQL ライセンスは不要です。 Azure ハイブリッド特典の価格が読み取りスケールアウト (セカンダリ) レプリカに自動的に適用されます。

### <a name="what-kind-of-workloads-is-hyperscale-designed-for"></a>ハイパースケールはどのようなワークロードを対象としているか

ハイパースケールはすべての SQL Server ワークロードをサポートしますが、主に OLTP 用に最適化されています。 ハイブリッド (HTAP) および分析 (データ マート) のワークロードにも対応できます。

### <a name="how-can-i-choose-between-azure-synapse-analytics-and-azure-sql-database-hyperscale"></a>Azure Synapse Analytics と Azure SQL Database ハイパースケールのどちらを選ぶべきか

現在、SQL Server をデータ ウェアハウスとして使用して対話型分析クエリを実行している場合、ハイパースケールがオプションとして優れています。小規模および中規模データ ウェアハウス (数 TB から最大 100 TB) をホスティングするコストを抑えることができ、最小限の T-SQL コードの変更で SQL Server データ ウェアハウス ワークロードをハイパースケールに移行できます。

複雑なクエリおよび 100 MB/s を超える持続的なインジェスト速度で大規模に Data Analytics を実行しているか、Parallel Data Warehouse (PDW)、Teradata、またはその他の超並列処理 (MPP) データ ウェアハウスを使用している場合は、Azure Synapse Analytics が最適な選択肢である可能性があります。
  
## <a name="hyperscale-compute-questions"></a>ハイパースケールのコンピューティングに関する質問

### <a name="can-i-pause-my-compute-at-any-time"></a>コンピューティングをいつでも一時停止できるか

現時点ではできませんが、コンピューティングとレプリカの数をスケールダウンして、ピーク時以外のコストを削減することができます。

### <a name="can-i-provision-a-compute-replica-with-extra-ram-for-my-memory-intensive-workload"></a>メモリ集中型ワークロードのために RAM を増やして計算レプリカをプロビジョニングできるか

いいえ。 RAM を増やすには、コンピューティング サイズをアップグレードして上げる必要があります。 詳しくは、[ハイパースケールのストレージ サイズおよびコンピューティング サイズ](resource-limits-vcore-single-databases.md#hyperscale---provisioned-compute---gen5)をご覧ください。

### <a name="can-i-provision-multiple-compute-replicas-of-different-sizes"></a>サイズが違う複数の計算レプリカをプロビジョニングできるか

いいえ。

### <a name="how-many-read-scale-out-replicas-are-supported"></a>サポートされる読み取りスケールアウト レプリカの数

ハイパースケール データベースは、既定で 1 つの読み取りスケールアウト レプリカを含むように作成されます (プライマリを含めて 2 つのレプリカ)。 [Azure portal](https://portal.azure.com) または [REST API](/rest/api/sql/databases/createorupdate) を使用して、読み取り専用レプリカの数を 0 から 4 の間でスケールすることができます。

### <a name="for-high-availability-do-i-need-to-provision-additional-compute-replicas"></a>高可用性のために追加の計算レプリカをプロビジョニングできるか

ハイパースケール データベースでは、データ回復性はストレージ レベルで提供されます。 回復性を実現するために必要なのは 1 つのレプリカだけです。 コンピューティング レプリカが停止すると、新しいレプリカが自動的に作成されるためデータは失われません。

ただし、レプリカが 1 つしかない場合は、フェールオーバー後に新しいレプリカにローカル キャッシュを構築するために少し時間がかかることがあります。 キャッシュ再構築フェーズで、データベースはページ サーバーからデータを直接フェッチするため、ストレージの待機時間が長くなり、クエリのパフォーマンスが低下します。

高可用性を必要とするミッション クリティカル アプリケーションに対するフェールオーバーの影響を最小限に抑えるため、プライマリ計算レプリカを含めて少なくとも 2 つの計算レプリカをプロビジョニングする必要があります。 これは既定の構成です。 そうすることでフェールオーバー ターゲットとして機能するホットスタンバイ レプリカを用意できます。

## <a name="data-size-and-storage-questions"></a>データ サイズとストレージに関する質問

### <a name="what-is-the-maximum-database-size-supported-with-hyperscale"></a>ハイパースケールでサポートされる最大データベース サイズとは

100 TB。

### <a name="what-is-the-size-of-the-transaction-log-with-hyperscale"></a>ハイパースケールでのトランザクション ログのサイズとは

ハイパースケールでのトランザクション ログは実質的に無制限です。 ログ スループットが高いシステム上では、ログ領域の不足を心配する必要はありません。 ただし、高い書き込みワークロードが継続する場合にはログ生成速度が調整される可能性があります。 ピークが持続するログ生成速度は、100 MB/秒です。

### <a name="does-my-tempdb-scale-as-my-database-grows"></a>データベースのサイズ増大につれて `tempdb` をスケーリングできるか

`tempdb` データベースはローカル SSD ストレージに配置され、プロビジョニングするコンピューティング サイズに比例してサイズが調整されます。 `tempdb` は、最大のパフォーマンス メリットを得られるように最適化されています。 `tempdb` サイズを構成することはできません。これは自動的に管理されます。

### <a name="does-my-database-size-automatically-grow-or-do-i-have-to-manage-the-size-of-data-files"></a>データベース サイズが自動的に拡張されるか、データ ファイルのサイズを自分で管理する必要があるか

データベースのサイズは、データの挿入/取り込みに応じて自動的に拡大します。

### <a name="what-is-the-smallest-database-size-that-hyperscale-supports-or-starts-with"></a>ハイパースケールでサポートされる最小データベース サイズ (ハイパースケールを使用開始できる最小サイズ) とは

40 GB です。 ハイパースケール データベースは、標準サイズである 10 GB で作成されます。 その後、10 分ごとに 10 GB ずつ拡大し、最終的には 40 GB に達します。 IOPS と I/O 並列処理を向上させるために、これらの 10 GB チャンクはそれぞれ別のページ サーバーに割り当てられます。 この最適化により、初期データベースのサイズとして 40 GB 未満を選択しても、データベースは少なくとも 40 GB までは自動的に拡大します。

### <a name="in-what-increments-does-my-database-size-grow"></a>データベースのサイズが拡張される単位とは

各データ ファイルは 10 GB ずつ拡張されます。 複数のデータ ファイルが同時に拡張される可能性があります。

### <a name="is-the-storage-in-hyperscale-local-or-remote"></a>ハイパースケールのストレージはローカルかリモートか

ハイパースケールでは、データ ファイルは Azure Standard ストレージに格納されます。 計算レプリカに近いページ サーバーでは、データは完全にローカル SSD ストレージにキャッシュされます。 さらに、計算レプリカは、ローカル SSD とメモリ内にデータ キャッシュを持ち、リモート ページ サーバーからデータをフェッチする回数を減らします。

### <a name="can-i-manage-or-define-files-or-filegroups-with-hyperscale"></a>ハイパースケールを使用してファイルまたはファイルグループを管理または定義できるか

いいえ。 データ ファイルは自動的に追加されます。 追加のファイル グループを作成する一般的な理由は、ハイパースケール ストレージ アーキテクチャには適用されません。

### <a name="can-i-provision-a-hard-cap-on-the-data-growth-for-my-database"></a>データベースのデータ増加にハード キャップをプロビジョニングできるか

いいえ。

### <a name="how-are-data-files-laid-out-with-hyperscale"></a>ハイパースケールではどのようにデータ ファイルがレイアウトされるか

データ ファイルはページ サーバーによって制御されます。ページ サーバーはデータ ファイルごとに 1 つあります。 データ サイズが大きくなると、データ ファイルと関連するページ サーバーが追加されます。

### <a name="is-database-shrink-supported"></a>データベースの縮小はサポートされるか

いいえ。

### <a name="is-data-compression-supported"></a>データ圧縮はサポートされるか

はい (行、ページ、列ストア圧縮を含む)。

### <a name="if-i-have-a-huge-table-does-my-table-data-get-spread-out-across-multiple-data-files"></a>大きなテーブルのデータを複数のデータ ファイルに分散できるか

はい。 特定のテーブルに関連付けられたデータ ページを、同じファイルグループに含まれる複数のデータ ファイルに分散することができます。 SQL Server は、[比例配分方式](/sql/relational-databases/databases/database-files-and-filegroups#file-and-filegroup-fill-strategy)を使用してデータをデータ ファイルに均等配置します。

## <a name="data-migration-questions"></a>データの移行に関する質問

### <a name="can-i-move-my-existing-databases-in-azure-sql-database-to-the-hyperscale-service-tier"></a>既存の Azure SQL Database のデータベースをハイパースケール サービス レベルに移行できるか

はい。 ハイパースケールには既存の Azure SQL Database データベースを移行できます。 これは一方向にしか移行できません。 ハイパースケールから他のサービス レベルにデータベースを移行することはできません。 概念実証 (POC) のために、データベースのコピーを作成して、そのコピーをハイパースケールに移行することをお勧めします。 

既存のデータベースを Hyperscale に移動するのに必要な時間は、データのコピーにかかる時間と、データのコピー中にソース データベースに加えられた変更を再生する時間で構成されます。 データのコピー時間は、データのサイズに比例します。 書き込み操作が少ない期間に移動が行われた場合、変更の再生にかかる時間は短くなります。
  
### <a name="can-i-move-my-hyperscale-databases-to-other-service-tiers"></a>ハイパースケール データベースを他のサービス レベルに移行できるか

いいえ。 現時点では、ハイパースケール データベースを別のサービス レベルに移行することはできません。

### <a name="do-i-lose-any-functionality-or-capabilities-after-migration-to-the-hyperscale-service-tier"></a>ハイパースケール サービス レベルに移行した後で使えなくなる機能があるか

はい。 長期的なバックアップ リテンション期間を始めとする Azure SQL Database の一部の機能は、まだハイパースケールではサポートされていません。 ハイパースケールにデータベースを移行した後、これらの機能は動作を停止します。  これらの制限は一時的なものであると想定しています。

### <a name="can-i-move-my-on-premises-sql-server-database-or-my-sql-server-database-in-a-cloud-virtual-machine-to-hyperscale"></a>オンプレミスの SQL Server データベース、またはクラウドの仮想マシンにある自分の SQL Server データベースースをハイパースケールに移行できるか

はい。 既存のすべての移行テクノロジを使用して、トランザクション レプリケーションやその他のデータ移動テクノロジ (一括コピー、Azure Data Factory、Azure Databricks、SSIS) などの Hyperscale に移行することができます。 [Azure Database Migration Service](../../dms/dms-overview.md) に関するページもご覧ください。このサービスでは、多くの移行シナリオがサポートされています。

### <a name="what-is-my-downtime-during-migration-from-an-on-premises-or-virtual-machine-environment-to-hyperscale-and-how-can-i-minimize-it"></a>オンプレミスまたは仮想マシン環境からハイパースケールへの移行時のダウンタイム、および最小限にするための方法

ハイパースケールへの移行のダウンタイムは、ご自身のデータベースを他の Azure SQL Database サービス レベルに移行する際のダウンタイムと同じです。 [トランザクション レプリケーション](replication-to-sql-database.md#data-migration-scenario
)を使用すると、サイズが数 TB までのデータベースについて移行時のダウンタイムを最小限に抑えることができます。 特に大規模なデータベース (10 TB 超) の場合は、ADF、Spark、またはその他のデータ移動方法を使用してデータを移行することを検討してください。

### <a name="how-much-time-would-it-take-to-bring-in-x-amount-of-data-to-hyperscale"></a>容量 X のデータをハイパースケールに移行するためにかかる時間

ハイパースケールは、新規または変更されたデータを 100 MB/秒で利用できますが、Azure SQL Database のデータベースにデータを移動するために必要な時間は、使用可能なネットワーク スループット、ソースの読み取り速度、ターゲット データベースのサービス レベル目標の影響も受けます。

### <a name="can-i-read-data-from-blob-storage-and-do-fast-load-like-polybase-in-azure-synapse-analytics"></a>BLOB ストレージからデータを読み取って高速読み込みできるか (Azure Synapse Analytics の Polybase のように)

クライアント アプリケーションで Azure Storage からデータを読み取り、ハイパースケール データベースに読み込むことができます (他の Azure SQL Database データベースの場合と同様です)。 Polybase は現在 Azure SQL Database でサポートされていません。 高速読み込みを提供する代替手段として、[Azure Data Factory](../../data-factory/index.yml) を使用するか、[SQL 用 Spark コネクタ](spark-connector.md)で Spark ジョブを [Azure Databricks](/azure/azure-databricks/) で使用できます。 SQL 用の Spark コネクタでは一括挿入がサポートされます。

BULK INSERT または OPENROWSET を使用して、Azure BLOB ストアからデータを一括で読み取ることもできます。[Azure Blob Storage のデータに一括アクセスする例](/sql/relational-databases/import-export/examples-of-bulk-access-to-data-in-azure-blob-storage#accessing-data-in-a-csv-file-referencing-an-azure-blob-storage-location)。

単純復旧モデルまたは一括ログ記録モデルはハイパースケールではサポートされていません。 高可用性を実現して特定の時点に復旧するには、完全復旧モデルが必要です。 ただし、ハイパースケール ログ アーキテクチャでは、他の Azure SQL Database サービス レベルと比較してデータ取り込み率が向上しています。

### <a name="does-hyperscale-allow-provisioning-multiple-nodes-for-parallel-ingesting-of-large-amounts-of-data"></a>ハイパースケールでは大量のデータを並列で取り込むために複数のノードをプロビジョニングできるか

いいえ。 ハイパースケールは対称型マルチプロセッシング (SMP) アーキテクチャです。超並列処理アーキテクチャ (MPP) またはマルチマスター アーキテクチャではありません。 読み取り専用ワークロードをスケールアウトするためには複数のレプリカを作成するしかありません。

### <a name="what-is-the-oldest-sql-server-version-supported-for-migration-to-hyperscale"></a>ハイパースケールへの移行でサポートされる最も古い SQL Server バージョンとは

SQL Server 2005。 詳しくは、「[単一データベースまたはプールされたデータベースに移行する](migrate-to-database-from-sql-server.md#migrate-to-a-single-database-or-a-pooled-database)」をご覧ください。 互換性の問題について詳しくは、「[データベース移行に関する互換性の問題の解決](migrate-to-database-from-sql-server.md#resolving-database-migration-compatibility-issues)」をご覧ください。

### <a name="does-hyperscale-support-migration-from-other-data-sources-such-as-amazon-aurora-mysql-postgresql-oracle-db2-and-other-database-platforms"></a>ハイパースケールで他のデータ ソース (Amazon Aurora、MySQL、PostgreSQL、Oracle、DB2、その他のデータベース プラットフォーム) からの移行がサポートされるか

はい。 [Azure Database Migration Service](../../dms/dms-overview.md) では多くの移行シナリオがサポートされています。

## <a name="business-continuity-and-disaster-recovery-questions"></a>ビジネス継続性とディザスター リカバリーに関する質問

### <a name="what-slas-are-provided-for-a-hyperscale-database"></a>ハイパースケール データベースではどのような SLA が提供されるか

「[Azure SQL Database の SLA](https://azure.microsoft.com/support/legal/sla/sql-database/v1_4/)」を参照してください。 セカンダリ計算レプリカを追加することで可用性が上がります。複数のセカンダリ計算レプリカを持つデータベースでは最大 99.99% になります。

### <a name="are-the-database-backups-managed-for-me-by-azure-sql-database"></a>Azure SQL Database によってデータベース バックアップが管理されるか

はい。

### <a name="how-often-are-the-database-backups-taken"></a>データベース バックアップの実行頻度

ハイパースケール データベースでは、従来の完全バックアップ、差分バックアップ、およびログ バックアップは存在しません。 代わりに、データ ファイルの定期ストレージ スナップショットがあります。 生成されたログは、構成された保持期間中はそのまま単純に保存され、その保持期間内の任意の時点への復元が可能です。

### <a name="does-hyperscale-support-point-in-time-restore"></a>ハイパースケールで特定の時点への復元がサポートされるか

はい。

### <a name="what-is-the-recovery-point-objective-rporecovery-time-objective-rto-for-database-restore-in-hyperscale"></a>ハイパースケールのデータベース復元における目標復旧ポイント (RPO)/目標復旧時間 (RTO) とは

RPO は 0 分です。ほとんどの復元操作は、データベース サイズにかかわらず、60 分以内に完了します。 大規模なデータベースの場合、および復元ポイントの前とそこに至るまでの時間に大量の書き込みアクティビティが発生した場合は、復元時間が長くなる可能性があります。

### <a name="does-database-backup-affect-compute-performance-on-my-primary-or-secondary-replicas"></a>データベース バックアップは、プライマリ レプリカまたはセカンダリ レプリカのコンピューティング パフォーマンスに影響するか

いいえ。 バックアップはストレージ サブシステムによって管理され、ストレージ スナップショットを利用します。 ユーザー ワークロードには影響しません。

### <a name="can-i-perform-geo-restore-with-a-hyperscale-database"></a>ハイパースケール データベースを使用して geo リストアを実行できるか

はい。 geo リストアは完全にサポートされています。 ポイントインタイム リストアとは異なり、geo リストアでは、データ サイズに応じた操作が必要です。 データ ファイルは並行してコピーされるため、この操作の実行時間は、データベースの合計サイズではなく、データベース内の最大ファイルのサイズによって主に決まります。 ソース データベースのリージョンと[ペアリングされた](../../best-practices-availability-paired-regions.md) Azure リージョンでデータベースを復元した場合、Geo リストアの時間は大幅に短くなります。

### <a name="can-i-set-up-geo-replication-with-hyperscale-database"></a>ハイパースケール データベースを使用して geo レプリケーションを設定できるか

現時点ではありません。

### <a name="can-i-take-a-hyperscale-database-backup-and-restore-it-to-my-on-premises-server-or-on-sql-server-in-a-vm"></a>ハイパースケール データベースのバックアップを使って、それをオンプレミス サーバーまたは VM の SQL Server に復元できるか

いいえ。 ハイパースケール データベースのストレージ形式は、リリース バージョンの SQL Server とは異なります。バックアップの管理またはバックアップへのアクセスはできません。 ハイパースケール データベースからデータを取り出すには、Azure Data Factory、Azure Databricks、SSIS などのデータ移動テクノロジを使用して、データを抽出できます。

## <a name="cross-feature-questions"></a>移行前後の機能に関する質問

### <a name="do-i-lose-any-functionality-or-capabilities-after-migration-to-the-hyperscale-service-tier"></a>ハイパースケール サービス レベルに移行した後で使えなくなる機能があるか

はい。 長期的なバックアップ リテンション期間を始めとする Azure SQL Database の一部の機能は、ハイパースケールではサポートされていません。 ハイパースケールにデータベースを移行した後、これらの機能は動作を停止します。

### <a name="will-polybase-work-with-hyperscale"></a>Polybase はハイパースケールで作動するか

いいえ。 Polybase は Azure SQL Database でサポートされていません。

### <a name="does-hyperscale-have-support-for-r-and-python"></a>ハイパースケールで R と Python がサポートされるか

現時点ではありません。

### <a name="are-compute-nodes-containerized"></a>計算ノードはコンテナー化されるか

いいえ。 ハイパースケール プロセスは、コンテナーではなく [Service Fabric](https://azure.microsoft.com/services/service-fabric/) ノード (VM) で実行されます。

## <a name="performance-questions"></a>パフォーマンスに関する質問

### <a name="how-much-write-throughput-can-i-push-in-a-hyperscale-database"></a>ハイパースケール データベースでどれくらい書き込みスループットをプッシュできるか

すべてのハイパースケール コンピューティング サイズについて、トランザクション ログ スループットの制限は 100 MB/秒に設定されています。 この速度を達成する能力は、ワークロードの種類やクライアントの構成など、さまざまな要因によって異なります。また、この速度でログを生成できるだけのコンピューティング容量がプライマリ計算レプリカにあるかにも影響されます。

### <a name="how-many-iops-do-i-get-on-the-largest-compute"></a>最大のコンピューティングにおいて得られる IOPS 数

IOPS と IO 待ち時間は、ワークロードのパターンによって異なります。 アクセス対象のデータが計算レプリカでキャッシュされている場合、IO パフォーマンスはローカル SSD と似ています。

### <a name="does-my-throughput-get-affected-by-backups"></a>スループットがバックアップの影響を受けるか

いいえ。 コンピューティングはストレージ レイヤーから切り離されます。 これによりパフォーマンスへのバックアップの影響がなくなります。

### <a name="does-my-throughput-get-affected-as-i-provision-additional-compute-replicas"></a>追加の計算レプリカをプロビジョニングするとスループットが影響を受けるか

ストレージが共有されており、プライマリ計算レプリカとセカンダリ計算レプリカの間では物理的なレプリケーションが直接発生しないため、セカンダリ レプリカを追加しても、プライマリ レプリカ上のスループットが直接影響を受けることはありません。 ただし、プライマリ継続する活発な書き込みワークロードを調整して、セカンダリ レプリカとページ サーバー上でログを適用できます。これで遅れを取り戻し、セカンダリ レプリカ上での読み取りパフォーマンスの悪化を回避できます。

### <a name="how-do-i-diagnose-and-troubleshoot-performance-problems-in-a-hyperscale-database"></a>ハイパースケール データベースでのパフォーマンスの問題はどのように診断およびトラブルシューティングするか

パフォーマンスに関する問題のほとんど、特にストレージ パフォーマンスに根差していない問題については、一般的な SQL の診断とトラブルシューティングの手順が適用されます。 ハイパースケール固有のストレージ診断については、「[SQL Hyperscale のパフォーマンスのトラブルシューティング診断](hyperscale-performance-diagnostics.md)」を参照してください。

## <a name="scalability-questions"></a>スケーラビリティに関する質問

### <a name="how-long-would-it-take-to-scale-up-and-down-a-compute-replica"></a>計算レプリカのスケールアップとスケールダウンにかかる時間

コンピューティングのスケールアップまたはスケールダウンには、データ サイズに関係なく、通常、最大 2 分かかります。

### <a name="is-my-database-offline-while-the-scaling-updown-operation-is-in-progress"></a>スケールアップ/スケールダウン操作の進行中にデータベースはオフラインになるか

いいえ。 スケールアップ/スケールダウンはオンラインで行われます。

### <a name="should-i-expect-connection-drop-when-the-scaling-operations-are-in-progress"></a>スケール操作が進行中に接続が切断されるか

スケールアップまたはスケールダウンによって既存の接続が切断されるのは、スケーリング操作の終了時にフェールオーバーが発生したときです。 セカンダリ レプリカの追加では接続は切断されません。

### <a name="is-the-scaling-up-and-down-of-compute-replicas-automatic-or-end-user-triggered-operation"></a>計算レプリカのスケールアップとスケールダウンは自動で行われるか (エンドユーザーが開始する操作か)

エンドユーザー。 自動ではありません。  

### <a name="does-the-size-of-my-tempdb-database-and-rbpex-cache-also-grow-as-the-compute-is-scaled-up"></a>コンピューティングがスケールアップされると `tempdb` データベースと RBPEX キャッシュのサイズも増大するか

はい。 コンピューティング ノードの `tempdb` データベースと [RBPEX キャッシュ](service-tier-hyperscale.md#distributed-functions-architecture)のサイズは、コア数の増加に応じて自動的にスケールアップされます。

### <a name="can-i-provision-multiple-primary-compute-replicas-such-as-a-multi-master-system-where-multiple-primary-compute-heads-can-drive-a-higher-level-of-concurrency"></a>複数のプライマリ コンピューティング ヘッドでより高レベルの同時実行を推進できる、複数のプライマリ計算レプリカ (マルチマスター システムなど) をプロビジョニングできるか

いいえ。 読み取り/書き込み要求を受け入れるのはプライマリ計算レプリカのみです。 セカンダリ計算レプリカは、読み取り専用要求のみを受け入れます。

## <a name="read-scale-out-questions"></a>読み取りスケールアウトに関する質問

### <a name="how-many-secondary-compute-replicas-can-i-provision"></a>セカンダリ計算レプリカをいくつプロビジョニングできるか

既定では、ハイパースケール データベース用にセカンダリ レプリカが 1 つ作成されます。 レプリカの数を調整する場合は、[Azure portal](https://portal.azure.com) または [REST API](/rest/api/sql/databases/createorupdate) を使用します。

### <a name="how-do-i-connect-to-these-secondary-compute-replicas"></a>これらのセカンダリ計算レプリカにどのように接続するか

これらの追加読み取り専用計算レプリカに接続するには、接続文字列の `ApplicationIntent` 引数を `ReadOnly` に設定してください。 `ReadOnly` がマークされたすべての接続は、追加読み取り専用計算レプリカのいずれかに自動的にルーティングされます。 詳細については、「[読み取り専用レプリカを使用して読み取り専用クエリ ワークロードをオフロードする](read-scale-out.md)」を参照してください。

### <a name="how-do-i-validate-if-i-have-successfully-connected-to-secondary-compute-replica-using-ssms-or-other-client-tools"></a>SSMS やその他のクライアント ツールを使用してセカンダリ計算レプリカに正常に接続したかどうかをどのように確認するか

T-SQL クエリ `SELECT DATABASEPROPERTYEX ('<database_name>', 'Updateability')` を実行できます。
結果は、読み取り専用セカンダリ レプリカに接続されている場合は `READ_ONLY`、プライマリ レプリカに接続されている場合は `READ_WRITE` になります。 データベース コンテキストは、`master` データベースではなく、ハイパースケール データベースの名前に設定する必要があることに注意してください。

### <a name="can-i-create-a-dedicated-endpoint-for-a-read-scale-out-replica"></a>読み取りスケールアウト レプリカ専用のエンドポイントを作成できるか

いいえ。 `ApplicationIntent=ReadOnly` を指定した場合にのみ、読み取りスケールアウト レプリカに接続できます。

### <a name="does-the-system-do-intelligent-load-balancing-of-the-read-workload"></a>読み取りワークロードのインテリジェントな負荷分散がシステムで行われるか

いいえ。 読み取り専用の意図との新しい接続は、任意の読み取りスケールアウト レプリカにリダイレクトされます。

### <a name="can-i-scale-updown-the-secondary-compute-replicas-independently-of-the-primary-replica"></a>セカンダリ計算レプリカをプライマリ レプリカと別にスケールアップ/スケールダウンできるか

いいえ。 セカンダリ計算レプリカは高可用性フェールオーバー ターゲットとしても使用されるため、フェールオーバ後、期待されるパフォーマンスを提供できるように、プライマリと同じ構成にする必要があります。

### <a name="do-i-get-different-tempdb-sizing-for-my-primary-compute-and-my-additional-secondary-compute-replicas"></a>プライマリ計算レプリカと追加のセカンダリ計算レプリカで異なる `tempdb` サイズを設定できるか

いいえ。 `tempdb` データベースはコンピューティング サイズのプロビジョニングに基づいて構成され、セカンダリ計算レプリカはプライマリ計算レプリカと同じサイズです。

### <a name="can-i-add-indexes-and-views-on-my-secondary-compute-replicas"></a>セカンダリ計算レプリカにインデックスとビューを追加できるか

いいえ。 ハイパースケール データベースではストレージが共有されます。つまり、すべての計算レプリカは同じテーブル、インデックス、ビューを見ることができます。 読み取りに対して最適化したインデックスをセカンダリに追加したい場合は、プライマリに追加する必要があります。

### <a name="how-much-delay-is-there-going-to-be-between-the-primary-and-secondary-compute-replicas"></a>プライマリ計算レプリカとセカンダリ計算レプリカの間の遅延はどれくらいか

トランザクションがプライマリ上でコミットされてからセカンダリで読み取り可能になるまでのデータ待機時間は、現在のログ生成速度、トランザクション サイズ、レプリカへの負荷、およびその他の要因によって異なります。 小規模なトランザクションの一般的なデータ待機時間は数十ミリ秒ですが、データ待機時間に上限はありません。 特定のセカンダリ レプリカのデータは、トランザクションに対して常に一貫性があります。 ただし、特定の時点でのデータの待機時間は、セカンダリ レプリカによって異なる場合があります。 コミットされたデータの読み取りが直ちに必要なワークロードは、プライマリ レプリカで実行される必要があります。

## <a name="next-steps"></a>次のステップ

ハイパースケール サービス レベルについて詳しくは、[ハイパースケール サービス レベル](service-tier-hyperscale.md)に関するページをご覧ください。