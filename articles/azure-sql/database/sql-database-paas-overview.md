---
title: Azure SQL Database サービスとは
description: 'SQL Database の概要: クラウド内の Microsoft のリレーショナル データベース管理システム (RDBMS) の技術の詳細と機能について説明します。'
keywords: sql の概要,sql の紹介,sql database とは
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: sqldbrb=3
ms.devlang: ''
ms.topic: overview
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 09/21/2020
ms.openlocfilehash: 65710ae40a971d214068f0e2686f78fb994a967e
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "104601399"
---
# <a name="what-is-azure-sql-database"></a>Azure SQL Database とは
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Azure SQL Database は、アップグレード、修正プログラムの適用、バックアップ、監視などのほとんどのデータベース管理機能をユーザーの介入なしで処理する、フル マネージドの PaaS (サービスとしてのプラットフォーム) データベース エンジンです。 Azure SQL Database は常に、最新の安定したバージョンの SQL Server データベース エンジンおよびパッチが適用された OS 上で実行され、99.99% の可用性を備えています。 Azure SQL Database に組み込まれた PaaS 機能により、お客様は、ビジネスにとって不可欠なドメイン固有のデータベース管理や最適化アクティビティに集中することができます。

Azure SQL Database を使用すると、Azure のアプリケーションやソリューションのための高可用性かつ高パフォーマンスのデータ ストレージ層を作成できます。 SQL Database を使用することで、グラフ、JSON、空間、XML などのリレーショナル データと[非リレーショナル構造](../multi-model-features.md)の両方を処理できるようになるため、さまざまな最新のクラウド アプリケーションに適しています。

Azure SQL Database は、[Microsoft SQL Server データベース エンジン](/sql/sql-server/sql-server-technical-documentation?toc=%2fazure%2fsql-database%2ftoc.json)の最新の安定したバージョンに基づいています。 [高パフォーマンスのメモリ内テクノロジ](../in-memory-oltp-overview.md)や[インテリジェントなクエリ処理](/sql/relational-databases/performance/intelligent-query-processing?toc=%2fazure%2fsql-database%2ftoc.json)など、高度なクエリ処理機能を使用できます。 実際、SQL Server の最新機能のリリースは SQL Database から始まり、その後 SQL Server 自体に対してリリースされます。 修正プログラムの適用やアップグレードのオーバーヘッドなしで SQL Server の最新の機能を取得して、数百万のデータベースでテストすることができます。 

SQL Database を使用すると、2 つの異なる購入モデル ([仮想コアベースの購入モデル](service-tiers-vcore.md)と [DTU ベースの購入モデル](service-tiers-dtu.md)) の中でパフォーマンスを簡単に定義してスケールすることができます。 SQL Database は、高可用性、バックアップ、その他の一般的なメンテナンス操作が組み込まれた完全なマネージド サービスです。 SQL とオペレーティング システムのコードの修正プログラムの適用と更新は、すべて Microsoft で処理されます。 ユーザーが基になるインフラストラクチャを管理する必要はありません。

Azure SQL Database を初めてお使いの方は、[Azure SQL ビデオ シリーズ](https://channel9.msdn.com/Series/Azure-SQL-for-Beginners?WT.mc_id=azuresql4beg_azuresql-ch9-niner)の "*Azure SQL データベースの概要*" に関するビデオをご覧ください。
> [!VIDEO https://channel9.msdn.com/Series/Azure-SQL-for-Beginners/Azure-SQL-Database-Overview-7-of-61/player]

> [!TIP]
> Azure SQL を改善するために、 [アンケートにご協力ください。](https://microsoft.qualtrics.com/jfe/form/SV_ePOznHhP4gDKfGu?channel=456)

## <a name="deployment-models"></a>デプロイ モデル

Azure SQL Database には、データベースのデプロイに関して次の選択肢があります。

- [単一データベース](single-database-overview.md)は、フル マネージドの分離されたデータベースを表します。 このオプションは、信頼性の高い 1 つのデータ ソースを必要とする最新のクラウド アプリケーションとマイクロサービスがある場合に使用できます。 単一データベースは、[SQL Server データベース エンジン](/sql/sql-server/sql-server-technical-documentation?toc=%2fazure%2fsql-database%2ftoc.json)内の[包含データベース](/sql/relational-databases/databases/contained-databases?toc=%2fazure%2fsql-database%2ftoc.json)に似ています。
- [エラスティック プール](elastic-pool-overview.md)は、CPU やメモリなどのリソースの共有セットを含む[単一データベース](single-database-overview.md)のコレクションです。 単一データベースはエラスティック プールの内外に移動できます。

> [!IMPORTANT]
> SQL Database と SQL Server の機能面の違い、また Azure SQL Database の各種オプション間の違いについては、[SQL Database の機能](features-comparison.md)に関するページを参照してください。

SQL Database では、複数のリソースの種類、サービス レベル、コンピューティング サイズを使用して予測可能なパフォーマンスが提供されます。 ダウンタイムのない動的スケーラビリティ、組み込みのインテリジェントな最適化、グローバルなスケーラビリティと可用性、高度なセキュリティ オプションが提供されます。 これらの機能を使用すると、仮想マシンとインフラストラクチャの管理ではなく、迅速なアプリケーション開発や、製品化に要する時間の短縮化に専念することができます。 SQL Database は現在、世界中の 38 のデータセンターにあるため、お近くのデータセンターでご自分のデータベースを実行できます。

## <a name="scalable-performance-and-pools"></a>スケーラブルなパフォーマンスとプール

割り当てられるリソースの量を定義できます。 
- 単一データベースでは、各データベースが他のデータベースから分離されており、移植することができます。 それぞれが独自に、コンピューティング、メモリ、およびストレージ リソースの保証された量を持ちます。 データベースに割り当てられているリソースの量は、そのデータベース専用であり、Azure 内の他のデータベースとは共有されません。 [単一データベース リソースを動的にスケールアップおよびスケールダウン](single-database-scale.md)することができます。 単一データベース オプションでは、さまざまなニーズに応じて、さまざまなコンピューティング、メモリ、およびストレージ リソースが提供されます。 たとえば、1 から 80 の仮想コアや、32 GB から 4 TB を取得できます。 単一データベース用の[ハイパースケール サービス レベル](service-tier-hyperscale.md)は 100 TB へのスケーリングが可能であり、高速バックアップと復元機能を備えています。
- エラスティック プールを使用すると、プール内のすべてのデータベースによって共有されるリソースを割り当てることができます。 リソース プールにデータベースを新規作成または既存の単一データベースを移動することで、リソースの使用を最大化してコストを削減できます。 このオプションにより、[エラスティック プール リソースを動的にスケールアップおよびスケールダウン](elastic-pool-scale.md)することもできます。

汎用サービス レベルで、1 か月あたりのコストが低い小規模な単一データベースに最初のアプリを作成することができます。 サービス レベルは、ソリューションのニーズを満たすため、手動またはプログラムによってビジネスクリティカルなサービス レベルにいつでも変更できます。 アプリにも顧客にもダウンタイムを発生させずにパフォーマンスを調整することができます。 動的なスケーラビリティにより、データベースは変化の激しいリソース要件に透過的に対処することができます。 必要なときに必要な分のリソースにのみ課金されます。

*動的スケーラビリティ* は *自動スケール* とは異なります。 自動スケールは、基準に基づいてサービスが自動的にスケールされるのに対し、動的スケーラビリティでは、ダウンタイムなしで手動スケールすることができます。 単一データベース オプションでは、手動の動的スケーラビリティはサポートされていますが、自動スケールはサポートされていません。 自動操作を増やすには、エラスティック プールの使用を検討してください。これにより、データベースが個々のデータベースのニーズに基づいてプール内のリソースを共有できます。 もう 1 つのオプションは、単一データベースのスケーラビリティを自動化するのに役立つスクリプトを使用することです。 例については、「[PowerShell を使用して単一の SQL データベースを監視およびスケーリングする](scripts/monitor-and-scale-database-powershell.md)」を参照してください。

### <a name="purchasing-models"></a>モデルの購入

SQL Database には、次の購入モデルが用意されています。
- [仮想コアベースの購入モデル](service-tiers-vcore.md)では、仮想コアの数、メモリの量、およびストレージの容量と速度を選択できます。 また、仮想コアベースの購入モデルでは、[SQL Server 向けの Azure ハイブリッド特典](https://azure.microsoft.com/pricing/hybrid-benefit/)を使用して、コストを削減することもできます。 Azure ハイブリッド特典の詳細については、この記事で後述する「よく寄せられる質問」セクションを参照してください。
- [DTU ベースの購入モデル](service-tiers-dtu.md)では、データベースの軽量ワークロードから重量ワークロードまでをサポートするため、コンピューティング リソース、メモリ リソース、I/O リソースの組み合わせが、3 つのサービス レベルで提供されています。 各レベルにおけるコンピューティング サイズでは、これらのリソースのさまざまな組み合わせが提供され、ストレージ リソースを追加することができます。
- [サーバーレス モデル](serverless-tier-overview.md)では、ワークロードの需要に基づいてコンピューティングを自動的にスケーリングし、1 秒あたりのコンピューティング使用量に対して請求を行います。 またサーバーレス コンピューティング レベルでは、アイドル期間にデータベースを自動的に一時停止します。このときはストレージのみに課金され、再びアクティブになると自動的にデータベースが再開されます。

### <a name="service-tiers"></a>サービス階層

Azure SQL Database には、さまざまな種類のアプリケーション向けに設計された 3 つのサービス レベルが用意されています。
- 一般的なワークロード向けに設計された、[General Purpose/Standard](service-tier-general-purpose.md) サービス レベル。 予算重視のバランスの取れたコンピューティングおよびストレージ オプションが提供されます。
- トランザクション レートが高く、待ち時間 I/O が最低の OLTP アプリケーション向けに設計された、[Business Critical/Premium](service-tier-business-critical.md) サービス レベル。 複数の分離されたレプリカを使用して、障害に対する最も高い回復力を提供します。
- 非常に大規模な OLTP データベースや、流動的にストレージを自動スケーリングしたり、コンピューティングをスケーリングしたりする機能向けに設計された、[Hyperscale](service-tier-hyperscale.md) サービス レベル。    

### <a name="elastic-pools-to-maximize-resource-utilization"></a>リソース使用率を最大化するためのエラスティック プール

多くのビジネスとアプリケーションにとって、特に使用パターンが比較的予測可能である場合、単一データベースを作成し、要求に応じてパフォーマンスを調整することができれば、それで十分です。 予測できない使用パターンにより、コストおよびビジネス モデルを管理するのが難しくなる可能性があります。 [エラスティック プール](elastic-pool-overview.md)は、この問題を解決するように設計されています。 パフォーマンス リソースは、個々のデータベースではなくプールに割り当てます。 単一データベースのパフォーマンスに対してではなく、プールの全体的なパフォーマンス リソースに対して課金されます。

   ![Basic、Standard、および Premium エディションのエラスティック プールを示す図](./media/sql-database-paas-overview/sqldb_elastic_pools.png)

エラスティック プールを使用すると、リソースの需要が変動しても、データベース パフォーマンスの調整に集中する必要はありません。 プールされたデータベースは、必要に応じて、エラスティック プールのパフォーマンス リソースを使用します。 プールされたデータベースが使用しても、プールの制限を超えることはないため、個々のデータベース使用率が予測できない場合でも、コストは引き続き予測可能です。

[プールに対してデータベースの追加および削除を行う](elastic-pool-overview.md)ことで、完全に設定予算内で、アプリケーションを数個のデータベースから何千ものデータベースに及ぶ範囲でスケーリングすることができます。 また、プール内のデータベースが使用できるリソースの下限と上限を制御して、プール内のいずれかのデータベースがプールのすべてのリソースを使用してしまわないようにしたり、すべてのプールされたデータベースに最小限のリソースが確保されるようにしたりすることもできます。 エラスティック プールを使用するサービスとしてのソフトウェア (SaaS) アプリケーションの設計パターンの詳細については、「[SQL Database を使用したマルチテナント SaaS アプリケーションの設計パターン](saas-tenancy-app-design-patterns.md)」を参照してください。

スクリプトは、エラスティック プールの監視とスケールに役立ちます。 例については、「[PowerShell を使用して Azure SQL Database のエラスティック プールを監視およびスケールする](scripts/monitor-and-scale-pool-powershell.md)」を参照してください。


### <a name="blend-single-databases-with-pooled-databases"></a>単一データベースとプールされたデータベースの組み合わせ

単一データベースをエラスティック プールと組み合わせると、状況に合わせて単一データベースとエラスティック プールのサービス レベルを変更することができます。 また、他の Azure サービスを SQL Database とうまく組み合わせることにより、独自のアプリ設計のニーズを満たし、コストとリソースの効率性を向上させ、新たなビジネス チャンスを開くことができます。

## <a name="extensive-monitoring-and-alerting-capabilities"></a>広範囲に及ぶ監視とアラートの機能

Azure SQL Database には、ワークロードの特性に関する詳細な分析情報を得るのに役立つ高度な監視機能とトラブルシューティング機能が用意されています。 これらの機能とツールには、次のものがあります。
 - 最新バージョンの SQL Server データベース エンジンによって提供される組み込みの監視機能。 これによりリアルタイムのパフォーマンスに関する分析情報を得ることができます。 
 - 多数のデータベース インスタンスの監視とトラブルシューティングを可能にする、Azure によって提供される PaaS 監視機能。

[クエリ ストア](/sql/relational-databases/performance/best-practice-with-the-query-store)は、組み込みの SQL Server 監視機能です。これはクエリのパフォーマンスをリアルタイムで記録して、潜在的なパフォーマンスの問題と上位のリソース コンシューマーを特定できるようにします。 自動チューニングとレコメンデーションでは、低下したパフォーマンスと欠落または重複したインデックスがあるクエリに関するアドバイスが提供されます。 SQL Database で自動チューニングを使用すると、問題を解決できるスクリプトを手動で適用したり、SQL Database に修正プログラムを適用させたりすることができます。 また、SQL Database では、修正プログラムによって何らかのベネフィットが得られるかどうかをテストおよび検証し、結果に応じて変更を保持するか、元に戻すことができます。 クエリ ストアと自動チューニング機能に加えて、標準の [DMV と XEvent](monitoring-with-dmvs.md) を使用して、ワークロードのパフォーマンスを監視することができます。

Azure には、パフォーマンス評価と組み合わせた[組み込みのパフォーマンス監視](performance-guidance.md)と[アラート](alerts-insights-configure-portal.md)のツールが用意されています。これにより、何千ものデータベースの状態を監視できます。 これらのツールを使用すると、現在または今後のパフォーマンスのニーズに基づいて、スケールアップとスケールダウンの影響をすばやく評価することができます。 さらに、SQL Database では、監視を容易にするために[メトリックとリソース ログを出力する](metrics-diagnostic-telemetry-logging-streaming-export-configure.md)ことができます。 リソース使用率、ワーカーとセッション、および接続性を次の Azure リソースのいずれかに格納するように SQL Database を構成することができます。

- **Azure Storage**:大量の利用統計情報を低価格でアーカイブします。
- **Azure Event Hubs**:SQL Database の利用統計情報を、カスタム監視ソリューションまたはホット パイプラインと統合します。
- **Azure Monitor ログ**: レポート機能、アラート機能、および緩和機能を備えた組み込みの監視ソリューション用です。

![Azure の監視アーキテクチャの図](./media/sql-database-paas-overview/architecture.png)

## <a name="availability-capabilities"></a>可用性に関する機能

Azure SQL Database を使用すると、中断している間も企業は業務を続行できます。 従来の SQL Server 環境では、通常、少なくとも 2 台のマシンがローカルに設定されています。 これらのマシンには、1 台のマシンまたはコンポーネントの障害から保護するために、同期的に保持されるデータの完全なコピーが保持されます。 この環境では、高可用性が提供されますが、データ センターが破壊されるような自然災害からは保護されません。

ディザスター リカバリーでは、壊滅的なイベントは地理的に十分に局所的であり、データのコピーが保持されている別のマシンまたはマシンのセットから遠く離れているものと想定されています。 SQL Server では、非同期モードで実行されている Always On 可用性グループを使用して、この機能を実現できます。 通常、ユーザーはトランザクションをコミットする前に遠く離れたところでレプリケーションが発生するのを待ちたくはないので、計画外のフェールオーバーを行うときはデータ損失の可能性があることを意味します。

Premium および Business Critical サービス レベルのデータベースでは既に、可用性グループの同期と[同様の何かが行われて](high-availability-sla.md#premium-and-business-critical-service-tier-locally-redundant-availability)います。 低いサービス レベルのデータベースでは、[異なりますが同等のメカニズム](high-availability-sla.md#basic-standard-and-general-purpose-service-tier-locally-redundant-availability)を使用するストレージによって、冗長性が提供されます。 組み込みのロジックは、1 台のマシンの障害から保護するのに役立ちます。 アクティブ geo レプリケーション機能では、リージョン全体が破壊される災害から保護することができます。

Azure Availability Zones は、1 つのリージョン内の 1 つのデータセンター ビルでの障害に対する保護を試みます。 これはビルの電源やネットワークの損失からの保護に役立ちます。 SQL Database では、可用性ゾーン (実質的には異なる建物) ごとに異なるレプリカを配置します。

実際、Microsoft が管理するデータセンターのグローバル ネットワークによって強化された、Azure のサービス レベル アグリーメント [(SLA)](https://azure.microsoft.com/support/legal/sla/) は、アプリの 24 時間 365 日の継続的な稼働に役立ちます。 すべてのデータベースは Azure プラットフォームによって完全に管理され、データ損失ゼロおよび高いデータ可用性 (%) が保証されます。 基になるハードウェア、ソフトウェア、ネットワークの障害リスクへの対応や、パッチの適用、バックアップ、レプリケーション、障害検出、バグ修正、フェールオーバー、データベースのアップグレードなど、各種メンテナンス タスクは、Azure によって自動的に処理されます。 Standard の可用性は、計算レイヤーとストレージ レイヤーを分離することで得られます。 Premium の可用性は、計算とストレージを単一ノードに統合してパフォーマンスを確保し、そのうえで、Always On 可用性グループのようなテクノロジを実装することによって得られます。 Azure SQL Database の高可用性機能の詳細については、[SQL Database の可用性](high-availability-sla.md)に関するページをご覧ください。 

さらに、SQL Database には、組み込みの[ビジネス継続性とグローバルなスケーラビリティ](business-continuity-high-availability-disaster-recover-hadr-overview.md)の機能が用意されています。 これには以下が含まれます。

- [自動バックアップ](automated-backups-overview.md):

  SQL Database では、データベースのフル、差分、トランザクション ログの各バックアップを自動的に実行することで、任意の時点への復旧を可能にしています。 単一データベースおよびプールされたデータベースについては、長期的なバックアップ保有期間のために、フル データベース バックアップを Azure Storage に保存するように SQL Database を構成することができます。 マネージド インスタンスについては、長期的なバックアップ保有期間のためにコピーのみのバックアップを実行することもできます。

- [ポイントインタイム リストア](recovery-using-backups.md):

  どの SQL Database デプロイ オプションも、すべてのデータベースについて、自動バックアップのリテンション期間内の任意の時点への復旧をサポートします。
- [アクティブ geo レプリケーション](active-geo-replication-overview.md):

  単一データベースおよびプールされたデータベースのオプションでは、同じ Azure データ センターまたは世界各地に分散された Azure データセンター内に、最大 4 つの読み取り可能なセカンダリ データベースを構成することができます。 たとえば、カタログ データベースを使用する SaaS アプリケーションで大量の同時実行の読み取り専用トランザクションが行われる場合は、アクティブ geo レプリケーションを使用してグローバル読み取りスケールを有効にします。 これにより、読み取りワークロードによるプライマリ上のボトルネックを取り除くことができます。 マネージド インスタンスについては、自動フェールオーバー グループを使用します。
- [自動フェールオーバー グループ](auto-failover-group-overview.md):

  すべての SQL Database デプロイ オプションでは、フェールオーバー グループを使用して、グローバル規模で高可用性と負荷分散を有効にすることができます。 これには、透過的な geo レプリケーションと、大規模なデータベース、エラスティック プール、およびマネージド インスタンスのフェールオーバーが含まれます。 フェールオーバー グループを使用すると、管理オーバーヘッドを最小限に抑えながら、グローバルに分散した SaaS アプリケーションを作成できます。 これにより、すべての複雑な監視、ルーティング、およびフェールオーバー オーケストレーションが SQL Database で行われます。
- [ゾーン冗長データベース](high-availability-sla.md):

  SQL Database では、Premium または Business Critical のデータベースまたはエラスティック プールを複数の可用性ゾーンにわたってプロビジョニングすることができます。 これらのデータベースとエラスティック プールには高可用性を目的とする複数の冗長レプリカがあるため、これらのレプリカを複数の可用性ゾーンに配置することで、回復性が向上します。 これには、データ損失を発生させることなく、データセンター規模の障害から自動的に復旧する機能が含まれます。

## <a name="built-in-intelligence"></a>組み込みのインテリジェンス

SQL Database では、データベースの実行と管理にかかるコストを大幅に削減し、アプリケーションのパフォーマンスとセキュリティの両方を最大限に高めるのに役立つ、組み込みのインテリジェンスを利用できます。 数百万の顧客のワークロードを 24 時間実行する SQL Database は、顧客のプライバシーを完全に尊重しながら、膨大な量の利用統計情報を収集して処理します。 アプリケーションの状況をサービスが学習し、状況に適応することができるように、さまざまなアルゴリズムによって利用統計情報が継続的に評価されます。

### <a name="automatic-performance-monitoring-and-tuning"></a>自動パフォーマンス監視とチューニング

SQL Database は、監視する必要があるクエリの詳細な洞察を提供します。 SQL Database は、データベースのパターンについて学習し、データベース スキーマをワークロードに適応させることができます。 SQL Database は[パフォーマンスのチューニングに関する推奨事項](database-advisor-implement-performance-recommendations.md)を提示します。チューニング アクションを確認してそれらを適用できます。

ただし、特に多数のデータベースに対処している場合、データベースの常時監視は厄介で、かつ面倒なタスクです。 [Intelligent Insights](intelligent-insights-overview.md) は、SQL Database のパフォーマンスを大規模に自動的に監視することで、ユーザーに代わってこのジョブを実行します。 これは、パフォーマンス低下の問題をユーザーに通知し、各問題の根本原因を識別して、可能な場合はパフォーマンスの向上に関する推奨事項を提供します。

膨大な数のデータベースを管理することは、SQL Database と Azure が提供するすべての使用可能なツールとレポートを使用しても、効率的に実行するのは不可能な場合があります。 データベースの監視とチューニングを手動で行う代わりに、[自動チューニング](automatic-tuning-overview.md)を使用して、監視とチューニング アクションの一部を SQL Database に委任することも検討できます。 SQL Database は、推奨事項を自動的に適用し、テストを行い、パフォーマンスが確実に向上し続けるように、各チューニング アクションを検証します。 このように、SQL Database は、制御された安全な方法で、ワークロードに自動的に適応します。 自動チューニングは、データベースのパフォーマンスを注意深く監視し、すべてのチューニング アクションの実行前と実行後のパフォーマンスを比較します。 パフォーマンスが向上しない場合、チューニング アクションは元に戻されます。

SQL Database 上で [SaaS マルチテナント アプリ](saas-tenancy-app-design-patterns.md)を実行する Microsoft のパートナーの多くは、パフォーマンスの自動チューニングを信頼して、アプリケーションが常に安定した予測可能なパフォーマンスで実行されるようにしています。 パートナーにとって、この機能は、真夜中にパフォーマンス上の問題が発生するリスクを大幅に軽減するものです。 さらに、顧客ベースの一部では、SQL Server も使用しているため、SQL Database が提示するのと同じインデックスに関する推奨事項を使用して、SQL Server を使用している顧客を支援しています。

[SQL Database では](automatic-tuning-overview.md)、次の 2 つの自動チューニングを使用できます。

- **インデックスの自動管理**:データベースに追加するインデックスと削除するインデックスを識別します。
- **プランの自動修正**:問題のあるプランを識別し、SQL プランのパフォーマンスに関する問題を修正します。

### <a name="adaptive-query-processing"></a>アダプティブ クエリ処理

複数ステートメントのテーブル値関数のインターリーブ実行、バッチ モードのメモリ許可のフィードバック、バッチ モードの適応型結合などの[アダプティブ クエリ処理](/sql/relational-databases/performance/intelligent-query-processing)を使用できます。 これらの各アダプティブ クエリ処理機能には、従来から解決困難なクエリの最適化問題に関連したパフォーマンスの問題にさらに対処するために役立つ、同様の "学習して適応する" 手法が適用されます。

## <a name="advanced-security-and-compliance"></a>高度なセキュリティとコンプライアンス

SQL Database は、アプリケーションがさまざまなセキュリティとコンプライアンスの要件を満たすために役立つ、幅広い[組み込みのセキュリティ機能とコンプライアンス機能](../../active-directory/identity-protection/concept-identity-protection-security-overview.md)を備えています。

> [!IMPORTANT]
> Microsoft では、さまざまなコンプライアンス基準に対して Azure SQL Database (すべてのデプロイ オプション) を認定しています。 詳細については、[Microsoft Azure セキュリティ センター](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942)に関するページを参照してください。ここから最新の SQL Database コンプライアンス証明書の一覧を入手できます。

### <a name="advance-threat-protection"></a>高度な脅威保護

Azure Defender for SQL は、高度な SQL セキュリティ機能のための統合パッケージです。 この機能には、データベースの脆弱性の管理、データベースへの脅威を示す可能性がある異常なアクティビティの検出などが含まれます。 これらの機能を 1 つの場所で有効にして管理できます。

- [脆弱性評価](sql-vulnerability-assessment.md):

  このサービスは、データベースの潜在的な脆弱性を検出、追跡し、その修復を支援するものです。 セキュリティの状態を表示することができ、セキュリティの問題を解決して、データベースのセキュリティを強化するために実行可能な手順が含まれます。
- [脅威検出](threat-detection-configure.md):

  この機能では、データベースにアクセスしたりデータベースを悪用したりしようとする、通常とは異なる、害を及ぼす可能性のある試行を示す異常なアクティビティが検出されます。 データベースでの不審なアクティビティを継続的に監視し、潜在的な脆弱性、SQL インジェクション攻撃、および異常なデータベース アクセス パターンが見つかるとすぐにセキュリティ通知を提供します。 脅威の検出によるアラートでは、不審なアクティビティの詳細と、脅威の調査や危険性の軽減のために推奨される対処方法が提供されます。

### <a name="auditing-for-compliance-and-security"></a>コンプライアンスとセキュリティの監査

[監査](../../azure-sql/database/auditing-overview.md)では、データベース イベントを追跡し、Azure Storage アカウントの監査ログにイベントを書き込みます。 監査により、規定遵守の維持、データベース活動の理解、およびビジネス上の懸念やセキュリティ違犯の疑いを示す差異や異常に対する洞察が容易になります。

### <a name="data-encryption"></a>データの暗号化

SQL Database は、次の暗号化を提供することでデータを保護します。 移動中のデータには、[トランスポート層セキュリティ](https://support.microsoft.com/kb/3135244)が使用されます。 保存データには、[Transparent Data Encryption](/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql) が使用されます。 使用中のデータには、[Always Encrypted](/sql/relational-databases/security/encryption/always-encrypted-database-engine) が使用されます。

### <a name="data-discovery-and-classification"></a>データの検出と分類

[データの検出と分類](data-discovery-and-classification-overview.md)では、Azure SQL Database に組み込まれる、データベースの機密データの検出、分類、ラベル付け、および保護を行う機能が用意されます。 これによりデータベースの分類状態の可視性が提供され、データベース内やその境界を越えて機密データへのアクセスが追跡されます。

### <a name="azure-active-directory-integration-and-multi-factor-authentication"></a>Azure Active Directory との統合と多要素認証

SQL Database では、[Azure Active Directory との統合](authentication-aad-overview.md)によって、データベース ユーザーの ID とその他の Microsoft サービスを一元的に管理できます。 この機能は、アクセス許可の管理を簡略化し、セキュリティを強化します。 Azure Active Directory では、シングル サインオン プロセスをサポートすると同時に、データとアプリケーションのセキュリティが強化するため、[多要素認証](authentication-mfa-ssms-overview.md)がサポートされています。

## <a name="easy-to-use-tools"></a>使いやすいツール

SQL Database は、アプリケーションの開発と管理をより簡単で生産的にします。 SQL Database を使用すると、優れたアプリの構築に注力することができます。 既に所有しているツールとスキルを使用して、SQL Database で管理と開発を行うことができます。

|ツール|説明|
|:---|:---|
|[Azure ポータル](https://portal.azure.com/)|すべての Azure サービスを管理するための Web ベースのアプリケーションです。|
|[Azure Data Studio](/sql/azure-data-studio/)|Windows、macOS、Linux で実行されるクロスプラットフォーム データベース ツールです。|
|[SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms)|SQL Server から SQL Database まで、あらゆる SQL インフラストラクチャを管理するための、無料でダウンロードできるクライアント アプリケーションです。|
|[Visual Studio の SQL Server Data Tools](/sql/ssdt/download-sql-server-data-tools-ssdt)|SQL Server リレーショナル データベース、Azure SQL Database 内のデータベース、Integration Services パッケージ、Analysis Services データ モデル、Reporting Services レポートを開発するための、ダウンロード可能な無料のクライアント アプリケーションです。|
|[Visual Studio Code](https://code.visualstudio.com/docs)|Windows、macOS、Linux 向けの無料でダウンロードできるオープンソースのコード エディターです。 Microsoft SQL Server、Azure SQL Database、Azure Synapse Analytics にクエリを実行するための [mssql 拡張機能](https://aka.ms/mssql-marketplace)を含む拡張機能をサポートします。|

SQL Database は、macOS、Linux、および Windows での Python、Java、Node.js、PHP、Ruby、および .NET によるアプリケーションの構築をサポートします。 SQL Database は、SQL Server と同じ[接続ライブラリ](connect-query-content-reference-guide.md#libraries)をサポートします。

[!INCLUDE [sql-database-create-manage-portal](../includes/sql-database-create-manage-portal.md)]

## <a name="sql-database-frequently-asked-questions"></a>SQL Database に関してよく寄せられる質問

### <a name="can-i-control-when-patching-downtime-occurs"></a>いつ修正プログラムの適用によるダウンタイムを発生させるかを制御できますか?

いいえ。 修正プログラムの適用の影響は、アプリに[再試行ロジックを採用](develop-overview.md#resiliency)していれば、通常は顕著なものではありません。 詳細については、「[Azure SQL Database での Azure メンテナンス イベントの計画](planned-maintenance.md)」を参照してください。



## <a name="engage-with-the-sql-server-engineering-team"></a>SQL Server エンジニアリング チームとの交流

- [DBA Stack Exchange](https://dba.stackexchange.com/questions/tagged/sql-server):データベースの管理に関するご質問はこちらへ
- [Stack Overflow](https://stackoverflow.com/questions/tagged/sql-server):開発に関する質問はこちらへ
- [Microsoft Q&A 質問ページ](/answers/topics/azure-synapse-analytics.html): 技術的なご質問はこちらへ
- [フィードバック](https://aka.ms/sqlfeedback):バグの報告や機能要求
- [Reddit](https://www.reddit.com/r/SQLServer/):SQL Server についての意見交換

## <a name="next-steps"></a>次のステップ

- 単一データベースとエラスティック プールに関するコストの比較と計算については、[価格に関するページ](https://azure.microsoft.com/pricing/details/sql-database/)を参照してください。
- すぐに始めるには、次のクイックスタートをご覧ください。

  - [Azure portal でデータベースを作成する](single-database-create-quickstart.md)  
  - [Azure CLI でデータベースを作成する](az-cli-script-samples-content-guide.md)
  - [PowerShell を使用してデータベースを作成する](powershell-script-content-guide.md)

- Azure CLI と PowerShell の各種サンプルについては、以下のページをご覧ください。
  - [Azure SQL Database 用の Azure CLI サンプル](az-cli-script-samples-content-guide.md)
  - [Azure SQL Database 用の Azure PowerShell サンプル](powershell-script-content-guide.md)

- 発表される新機能の詳細については、[SQL Database の Azure ロードマップ](https://azure.microsoft.com/roadmap/?category=databases)を参照してください。
- [Azure SQL Database のブログ](https://azure.microsoft.com/blog/topics/database)を参照してください。これは、SQL Database に関するニュースと機能に関する SQL Server 製品チームのメンバーのブログです。