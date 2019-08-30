---
title: Azure SQL Database サービスとは | Microsoft Docs
description: 'SQL Database の概要: クラウド内の Microsoft のリレーショナル データベース管理システム (RDBMS) の技術の詳細と機能について説明します。'
keywords: sql の概要,sql の紹介,sql database とは
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: carlrab
ms.date: 04/08/2019
ms.openlocfilehash: 1c4fae5c41f4f23c4a7fe3135b602133aa69aacd
ms.sourcegitcommit: b3bad696c2b776d018d9f06b6e27bffaa3c0d9c3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/21/2019
ms.locfileid: "69873716"
---
# <a name="what-is-azure-sql-database-service"></a>Azure SQL Database サービスとは

Azure SQL Database は、Microsoft Azure クラウドのアプリケーションとソリューションに対して高可用性で高パフォーマンスのデータ ストレージ層を作成できるようにする汎用リレーショナル データベースのマネージド サービスです。 SQL Database を使用することで、グラフ、JSON、空間、XML などのリレーショナル データと[非リレーショナル構造](sql-database-multi-model-features.md)の両方を処理するための強力な機能が使用できるようになるため、さまざまな最新のクラウド アプリケーションに適しています。 これは、[Microsoft SQL Server データベース エンジン](https://docs.microsoft.com/sql/sql-server/sql-server-technical-documentation?toc=/azure/sql-database/toc.json)の最新の安定バージョンに基づいており、[ハイ パフォーマンスのインメモリ テクノロジ](sql-database-in-memory.md)や[インテリジェントなクエリ処理](https://docs.microsoft.com/sql/relational-databases/performance/intelligent-query-processing?toc=/azure/sql-database/toc.json)などの高度なクエリ処理機能の豊富なセットを使用できます。
Microsoft のクラウド優先戦略に基づく SQL Server の最新機能のリリースは SQL Database から始まり、その後 SQL Server 自体に対してリリースされます。 この方法によって、修正プログラムの適用やアップグレードなしで SQL Server の最新の機能をユーザーに提供することができ、数百万のデータベースでこれらの新機能をテストすることができます。 SQL Database を使用すると、2 つの異なる購入モデル ([仮想コアベースの購入モデル](sql-database-service-tiers-vcore.md)と [DTU ベースの購入モデル](sql-database-service-tiers-dtu.md)) の中でパフォーマンスを簡単に定義してスケールすることができます。 SQL Database は、可用性、バックアップ、その他の一般的なメンテナンス操作が組み込まれた完全なマネージド サービスです。 SQL と OS コードに対するパッチの適用と更新を Microsoft がすべてシームレスで処理するため、基になるインフラストラクチャの管理はすべて不要になります。

> [!NOTE]
> Azure SQL Database の用語集については、「[SQL Database 用語集](sql-database-glossary-terms.md)」をご覧ください

Azure SQL データベースには、そのデータベースのデプロイに関して次の選択肢があります。

![デプロイの選択肢](./media/sql-database-technical-overview/deployment-options.png)

- [単一データベース](sql-database-single-database.md)は、完全に管理された分離データベースを表します。これは、信頼性の高い 1 つのデータ ソースを必要とする最新のクラウド アプリケーションとマイクロサービスに最適な選択肢です。 単一データベースは [Microsoft SQL Server データベース エンジン](https://docs.microsoft.com/sql/sql-server/sql-server-technical-documentation?toc=/azure/sql-database/toc.json)の[包含データベース](https://docs.microsoft.com/sql/relational-databases/databases/contained-databases?toc=/azure/sql-database/toc.json)に似ています。
- [マネージド インスタンス](sql-database-managed-instance.md)は、[Microsoft SQL Server データベース エンジン](https://docs.microsoft.com/sql/sql-server/sql-server-technical-documentation?toc=/azure/sql-database/toc.json)の完全なマネージド インスタンスで、一緒に使用できる一連のデータベースが含まれています。 これは、オンプレミスの SQL Server データベースを Azure クラウドに簡単に移行するため、および SQL Server データベース エンジンが提供する強力なデータベース機能を利用する必要があるアプリケーションにとって最適な選択肢です。
- [エラスティック プール](sql-database-elastic-pool.md)は、CPU やメモリなどのリソースの共有セットを含む[単一データベース](sql-database-single-database.md)のコレクションです。 単一データベースはエラスティック プールの内外に移動できます。

> [!IMPORTANT]
> SQL Database と SQL Server の機能面の違い、また Azure SQL Database の各種デプロイ オプション間の違いについては、[SQL 機能](sql-database-features.md)に関するページを参照してください。

SQL Database は、パフォーマンスを予測できる複数のリソースの種類、サービス レベル、およびコンピューティング サイズで提供され、ダウンタイムがない動的なスケーラビリティ、組み込みのインテリジェントな最適化、グローバルなスケーラビリティと可用性、および高度なセキュリティ オプションを備えています。それらはすべてほぼ管理する必要がありません。 これらの機能を使用すると、貴重な時間とリソースを仮想マシンとインフラストラクチャの管理に奪われることなく、迅速なアプリケーション開発や、製品化に要する時間の短縮化に専念することができます。 SQL Database サービスは、現在、世界中の 38 か所のデータ センターで提供されていますが、新しいデータ センターが定期的に開設されているため、ご利用のデータベースを最寄りのデータ センターで実行することができます。

## <a name="scalable-performance-and-pools"></a>スケーラブルなパフォーマンスとプール

どの SQL Database でも、割り当てられるリソースの量を定義できます。 
- 単一データベースでは、各データベースは互いに分離され、可搬性があり、コンピューティング、メモリ、およびストレージ リソースの容量が独自に保証されます。 データベースに割り当てられているリソースの量は、そのデータベース専用であり、Azure クラウド内の他のデータベースとは共有されません。 また、[単一データベース リソースのスケーリング](sql-database-single-database-scale.md) (アップおよびダウン) を動的に行うこともできます。 単一データベースは、さまざまなニーズ (1 から 80 の仮想コア、32 GB から 4 TB など) に応じて、さまざまなコンピューティング、メモリ、およびストレージ リソースを提供します。単一データベース用の[ハイパースケール サービス レベル](sql-database-service-tier-hyperscale.md)では、100 TB までのスケーリングが可能であり、高速バックアップおよび復元機能を備えています。
- エラスティック プールを使用すると、プール内のすべてのデータベースによって共有されるリソースを割り当てることができます。 リソース プールにデータベースを新規作成または既存の単一データベースを移動することで、リソースの使用を最大化してコストを削減できます。また、[エラスティック プール リソースのスケーリング](sql-database-elastic-pool-scale.md) (アップおよびダウン) を動的に実行できます。
- マネージド インスタンスでは、各マネージド インスタンスが他のインスタンスから分離され、リソースが保証されます。 マネージド インスタンス内で、インスタンス データベースはリソースのセットを共有します。また、[マネージド インスタンス リソースのスケーリング](sql-database-managed-instance-resource-limits.md) (アップおよびダウン) を動的に実行できます。

最初に General Purpose サービス レベルで月額の安い小さなシングル データベースにアプリをビルドし、後でいつでもソリューションのニーズに合わせて手動またはプログラムでサービス レベルを Business Critical サービス レベルに変更できます。 アプリにも顧客にもダウンタイムを発生させずにパフォーマンスを調整することができます。 動的なスケーラビリティにより、データベースは変化の激しいリソース要件に透過的に対処することができ、必要なときに必要な分のリソースにのみ課金されます。

動的スケーラビリティは自動スケールとは異なります。 自動スケールは、基準に基づいてサービスが自動的にスケールされるのに対し、動的スケーラビリティでは、ダウンタイムなしで手動スケールすることができます。 単一データベースにより、手動の動的スケーラビリティはサポートされますが、自動スケールはサポートされていません。 *自動*操作を増やすには、エラスティック プールの使用を検討してください。エラスティック プールを使用すると、データベースが個々のデータベースのニーズに基づいてプール内のリソースを共有できます。 ただし、単一データベースのスケーラビリティを自動化できるスクリプトがあります。 例については、「[PowerShell を使用して単一の SQL データベースを監視およびスケーリングする](scripts/sql-database-monitor-and-scale-database-powershell.md)」を参照してください。

### <a name="purchasing-models-service-tiers-compute-sizes-and-storage-amounts"></a>購入モデル、サービス レベル、コンピューティング サイズ、ストレージ容量

SQL Database には、2 つの購入モデルが用意されています。
- [仮想コアベースの購入モデル](sql-database-service-tiers-vcore.md)では、仮想コアの数、メモリの量、およびストレージの容量と速度を選択できます。 また、仮想コアベースの購入モデルでは、[SQL Server 向けの Azure ハイブリッド特典](https://azure.microsoft.com/pricing/hybrid-benefit/)を使用して、コストを削減することもできます。 Azure ハイブリッド特典の詳細については、[よく寄せられる質問](#sql-database-frequently-asked-questions-faq)を参照してください。
- [DTU ベースの購入モデル](sql-database-service-tiers-dtu.md)では、軽量から重量までのデータベース ワークロードをサポートするために、コンピューティング、メモリ、IO の各リソースの組み合わせを 3 つのサービス レベルで提供します。 各レベルにおけるコンピューティング サイズでは、これらのリソースのさまざまな組み合わせが提供され、ストレージ リソースを追加することができます。

### <a name="elastic-pools-to-maximize-resource-utilization"></a>リソース使用率を最大化するためのエラスティック プール

多くのビジネスとアプリケーションにとって、特に使用パターンが比較的予測可能である場合、単一データベースを作成し、要求に応じてパフォーマンスを調整することができれば、それで十分です。 しかし、使用パターンが予測できない場合、コストおよびビジネス モデルを管理するのが難しくなる可能性があります。 [エラスティック プール](sql-database-elastic-pool.md)は、この問題を解決するように設計されています。 概念は単純です。 パフォーマンス リソースを個々のデータベースではなくプールに割り当て、課金は単一のデータベースのパフォーマンスではなくプールの全体的なパフォーマンス リソースに対して行われます。

   ![エラスティック プール](./media/sql-database-what-is-a-dtu/sqldb_elastic_pools.png)

エラスティック プールを使用すると、リソースの需要が変動しても、データベース パフォーマンスの調整に気を配る必要がなくなります。 プールされたデータベースは、必要に応じて、エラスティック プールのパフォーマンス リソースを使用します。 しかし、プールされたデータベースの使用は、プールの上限を超えることはありません。したがって、個々のデータベースの使用状況が予測できなくても、コストが予測可能なことに変わりはありません。 さらに、[プールに対してデータベースの追加および削除を行う](sql-database-elastic-pool-manage-portal.md)ことで、完全に設定予算内で、アプリケーションを数個のデータベースから何千ものデータベースに及ぶ範囲でスケーリングすることができます。 また、プール内のデータベースが使用できるリソースの下限と上限を制御して、プール内のいずれかのデータベースがプールのすべてのリソースを使用してしまわないようにしたり、すべてのプールされたデータベースに最小限のリソースが保証されるようにしたりすることもできます。 エラスティック プールを使用する SaaS アプリケーションの設計パターンの詳細については、「[マルチテナント SaaS アプリケーションと Azure SQL Database の設計パターン](sql-database-design-patterns-multi-tenancy-saas-applications.md)」を参照してください。

スクリプトは、エラスティック プールの監視とスケールに役立ちます。 例については、「[PowerShell を使用し、Azure SQL Database の SQL エラスティック プールを監視し、スケーリングする](scripts/sql-database-monitor-and-scale-pool-powershell.md)」を参照してください。

> [!IMPORTANT]
> マネージド インスタンスでは、エラスティック プールはサポートされません。 正確には、マネージド インスタンスは、マネージド インスタンス リソースを共有するインスタンス データベースのコレクションです。

### <a name="blend-single-databases-with-pooled-databases"></a>単一データベースとプールされたデータベースの組み合わせ

単一データベースをエラスティック プールと組み合わせると、状況に合わせて単一データベースとエラスティック プールのサービス レベルをすばやく簡単に変更することができます。 Azure の強力さと幅広さを利用して、他の Azure サービスを SQL Database とうまく組み合わせることにより、独自のアプリ設計のニーズを満たし、コストとリソースの効率性を向上させ、新たなビジネス チャンスを開くことができます。

## <a name="extensive-monitoring-and-alerting-capabilities"></a>広範囲に及ぶ監視とアラートの機能

Azure SQL Database には、ワークロードの特性についての完全な分析情報を得るのに役立つ高度な監視機能とトラブルシューティング機能が用意されています。 機能とツールは次のように分類される場合があります。
 - 最新バージョンの SQL Server データベース エンジンによって提供される組み込みの監視機能により、リアルタイムのパフォーマンスに関する分析情報を得ることができます。 
 - Azure プラットフォームによって提供される PaaS 監視機能により、多数のデータベース インスタンスを簡単に監視できます。また、パフォーマンスの問題を解決するのに役立つトラブルシューティングのアドバイスも提供されます。

データ ベースエンジンに組み込まれた活用すべき最も重要な監視機能は、[クエリ ストア](sql-database-operate-query-store.md) コンポーネントです。これはクエリのパフォーマンスをリアルタイムで記録して、潜在的なパフォーマンスの問題と上位のリソース コンシューマーを特定できるようにします。 自動チューニングとレコメンデーションでは、低下したパフォーマンスと欠落または重複したインデックスがあるクエリに関するアドバイスが提供されます。 Azure SQL Database で自動チューニングを使用すると、問題を修正できるスクリプトを手動で適用するか、Azure SQL Database で修正を適用して、何らかのメリットが得られるかどうかの検証を行い、結果に応じて変更を保持するか元に戻すことができます。 クエリ ストアと自動チューニング機能に加えて、標準の [DMV と XEvent](sql-database-monitoring-with-dmvs.md) を使用して、ワークロードのパフォーマンスを監視することもできます。

Azure プラットフォームには、パフォーマンス評価と組み合わせた[組み込みのパフォーマンス監視](sql-database-performance.md)と[アラート](sql-database-insights-alerts-portal.md)のツールが用意されています。これにより、何千ものデータベースの状態を簡単に監視できます。 これらのツールを使用すると、現在または今後のパフォーマンスのニーズに基づいて、スケールアップとスケールダウンの影響をすばやく評価することができます。 さらに、SQL Database では、監視を容易にするための[メトリックと診断ログを出力](sql-database-metrics-diag-logging.md)することができます。 リソース使用率、ワーカーとセッション、および接続性を次の Azure リソースのいずれかに格納するように SQL Database を構成することができます。

- **Azure Storage**:大量の利用統計情報を低価格でアーカイブします
- **Azure Event Hub**:SQL Database の利用統計情報を、カスタム監視ソリューションまたはホット パイプラインと統合します
- **Azure Monitor ログ**: レポート機能、アラート機能、および緩和機能を備えた組み込みの監視ソリューション用です。

    ![アーキテクチャ](./media/sql-database-metrics-diag-logging/architecture.png)

## <a name="availability-capabilities"></a>可用性に関する機能

従来の SQL Server 環境では、一般に、(少なくとも) 2 つのコンピューターをローカル環境にセットアップし、(AlwaysOn 可用性グループやフェールオーバー クラスター インスタンスなどの機能を使用して) データの正確な (同期的に維持される) コピーを使用して、1 つのコンピューター/コンポーネントの障害から保護します。 これにより、高可用性が提供されますが、データ センターが破壊されるような自然災害からは保護されません。

ディザスター リカバリーでは、壊滅的なイベントは地理的に十分に局所的であり、データのコピーが保持されている別のコンピューター/コンピューターのセットから遠く離れているものと想定されています。  SQL Server では、非同期モードで実行されている Always On 可用性グループを使用して、この機能を実現できます。  光の速さの問題は、通常、ユーザーはトランザクションをコミットする前に遠く離れたところでレプリケーションが発生するのを待ちたくはないので、計画外のフェールオーバーを行うときはデータ損失の可能性があることを意味します。

Premium および Business Critical のサービス レベルのデータベースでは、既に、可用性グループの同期に[よく似たことが行われて](sql-database-high-availability.md#premium-and-business-critical-service-tier-availability)います。 低いサービス レベルのデータベースでは、[異なりますが同等のメカニズム](sql-database-high-availability.md#basic-standard-and-general-purpose-service-tier-availability)を使用するストレージによって、冗長性が提供されます。 1 台のコンピューターの障害から保護するロジックがあります。  アクティブ geo レプリケーション機能では、リージョン全体が破壊される災害から保護することができます。

Azure Availability Zones は、高可用性の問題で機能します。  1 つのリージョン内の 1 つのデータ センター ビルでの障害に対して保護します。  そのため、ビルの電源やネットワークの損失を防ぎます。 SQL Azure では、これは異なる可用性ゾーン (実質的には異なるビル) に異なるレプリカを配置することによって動作し、それ以外については前と同じように動作します。

実際、Microsoft が管理するデータセンターのグローバル ネットワークによって強化された、Azure の業界をリードする可用性 99.99% のサービス レベル アグリーメント [(SLA)](https://azure.microsoft.com/support/legal/sla/) により、アプリケーションの 24 時間 365 日の継続的な稼働が可能になります。 すべてのデータベースは Azure プラットフォームによって完全に管理され、データ損失ゼロおよび高いデータ可用性 (%) が保証されます。 基になるハードウェア、ソフトウェア、ネットワークの障害リスクへの対応や、パッチの適用、バックアップ、レプリケーション、障害検出、バグ修正、フェールオーバー、データベースのアップグレードなど、各種メンテナンス タスクは、Azure によって自動的に処理されます。 Standard の可用性は、計算レイヤーとストレージ レイヤーを分離することで得られます。 Premium の可用性は、計算とストレージを単一ノードに統合してパフォーマンスを確保し、そのうえで、Always On 可用性グループのようなテクノロジを導入することによって得られます。 Azure SQL Database の高可用性機能の詳細については、[SQL Database の可用性](sql-database-high-availability.md)に関するページをご覧ください。 さらに、SQL Database には、次のような、組み込みの[ビジネス継続性とグローバルなスケーラビリティ](sql-database-business-continuity.md)の機能を備えています。

- **[自動バックアップ](sql-database-automated-backups.md)** :

  SQL Database では、Azure SQL データベースのフル、差分、トランザクション ログの各バックアップを自動的に実行することで、任意の時点への復旧を可能にしています。 単一データベースおよびプールされたデータベースについては、長期的なバックアップ保有期間のために、フル データベース バックアップを Azure ストレージに保存するように SQL Database を構成することができます。 マネージド インスタンスについては、長期的なバックアップ保有期間のためにコピーのみのバックアップを実行することもできます。

- **[ポイントインタイム リストア](sql-database-recovery-using-backups.md)** :

  すべての SQL Database デプロイ オプションは、あらゆる Azure SQL データベースについて、自動バックアップのリテンション期間内の任意の時点への復旧をサポートします。
- **[アクティブ geo レプリケーション](sql-database-active-geo-replication.md)** :

  単一データベースおよびプールされたデータベースでは、同じ Azure データ センターまたは世界各地に分散された Azure データ センター内に、最大 4 つの読み取り可能なセカンダリ データベースを構成することができます。  たとえば、カタログ データベースを使用する SaaS アプリケーションで大量の同時実行の読み取り専用トランザクションが行われる場合は、アクティブ geo レプリケーションを使用してグローバル スケールの読み取りを有効することで、読み取りワークロードによるプライマリ上のボトルネックを取り除くことができます。 マネージド インスタンスについては、自動フェールオーバー グループを使用します。
- **[自動フェールオーバー グループ](sql-database-auto-failover-group.md)** :

  すべての SQL Database デプロイ オプションでは、フェールオーバー グループを使用して、高可用性と負荷分散をグローバル スケールで有効にすることができます。これには、データベース、エラスティック プール、マネージド インスタンスの大規模セットの透過的な geo レプリケーションおよびフェールオーバーが含まれます。 フェールオーバー グループを使用すると、グローバルに分散された SaaS アプリケーションを最小限の管理オーバーヘッドで作成することができ、複雑な監視、ルーティング、およびフェールオーバーのオーケストレーションは、すべて SQL Database にまかせることができます。
- **[ゾーン冗長データベース](sql-database-high-availability.md)** :

  SQL Database では、プレミアムまたはビジネス クリティカルのデータベースまたはエラスティック プールを複数の可用性ゾーンにわたってプロビジョニングすることができます。 これらのデータベースとエラスティック プールには、高可用性を目的とする複数の冗長レプリカが存在します。それらのレプリカを複数の可用性ゾーンに配置することで回復性を高め、たとえばデータの損失を生じさせずにデータセンター規模の障害から自動的に復旧することができます。

## <a name="built-in-intelligence"></a>組み込みのインテリジェンス

SQL Database には、インテリジェンスが組み込まれています。このインテリジェンスによって、データベースの運用コストと管理コストは大幅に削減され、アプリケーションのパフォーマンスとセキュリティの両方が最大化されます。 数百万の顧客のワークロードを 24 時間実行する SQL Database は、水面下で顧客のプライバシーを 100% 尊重しながら、膨大な量のテレメトリ データを収集して処理します。 アプリケーションの状況をサービスが学習し、状況に適応することができるように、さまざまなアルゴリズムによってテレメトリ データが継続的に評価されます。 この分析に基づいて、サービスは、特定のワークロードに合わせたパフォーマンスの向上に関する推奨事項を考え出すことができます。

### <a name="automatic-performance-monitoring-and-tuning"></a>自動パフォーマンス監視とチューニング

SQL Database は、監視する必要があるクエリの詳細な洞察を提供します。 SQL Database は、データベースのパターンについて学習し、データベース スキーマをワークロードに適応させることができます。 SQL Database は[パフォーマンスのチューニングに関する推奨事項](sql-database-advisor.md)を提示します。チューニング アクションを確認してそれらを適用できます。

ただし、データベースを常に監視することは厄介で面倒なタスクであり、多数のデータベースを処理する場合は特にそうなります。 [Intelligent Insights](sql-database-intelligent-insights.md) は、スケールで SQL Database のパフォーマンスを自動的に監視することによってこのジョブを行い、パフォーマンスの低下の問題を通知し、問題の根本原因を特定し、可能な場合にはパフォーマンスの向上の推奨事項を提供します。

膨大な数のデータベースを管理することは、SQL Database と Azure ポータルが備えているすべての使用可能なツールとレポートを使用しても、効率的に実行するのは不可能な場合があります。 データベースの監視とチューニングを手動で行う代わりに、[自動チューニング](sql-database-automatic-tuning.md)を使用して、監視とチューニング アクションの一部を SQL Database に委任することを検討できます。 SQL Database は、推奨事項を自動的に適用し、テストを行い、パフォーマンスが確実に向上し続けるように、各チューニング アクションを検証します。 このように、SQL Database は、制御された安全な方法で、ワークロードに自動的に適応します。 自動チューニングは、データベースのパフォーマンスを注意深く監視し、すべてのチューニング アクションの実行前と実行後のパフォーマンスを比較し、パフォーマンスが向上していない場合はチューニング アクションを取り消すことを意味します。

今日、SQL Database 上で [SaaS マルチテナント アプリ](sql-database-design-patterns-multi-tenancy-saas-applications.md)を実行している当社の多数のパートナーは、パフォーマンスの自動チューニングを信頼して、アプリケーションが常に安定した予測可能なパフォーマンスで実行されるようにしています。 パートナーにとって、この機能は、真夜中にパフォーマンス上の問題が発生するリスクを大幅に軽減するものです。 さらに、顧客ベースの一部では、SQL Server も使用しているため、SQL Database が提示するのと同じインデックスに関する推奨事項を使用して、SQL Server を使用している顧客を支援しています。

[SQL Database では](sql-database-automatic-tuning.md)、次の 2 つの自動チューニングを使用できます。

- **インデックスの自動管理**:データベースに追加するインデックスと削除するインデックスを識別します。
- **プランの自動修正**:問題のあるプランを識別し、SQL プランのパフォーマンスに関する問題を修正します (近日公開予定。SQL Server 2017 では既に利用可能)。

### <a name="adaptive-query-processing"></a>アダプティブ クエリ処理

SQL Database には、[クエリの適応処理機能](/sql/relational-databases/performance/intelligent-query-processing)ファミリも追加されています。この中には、複数ステートメントのテーブル値関数のインターリーブ実行、バッチ モードのメモリ許可のフィードバック、バッチ モードの適応型結合が含まれています。 これらのクエリの適応処理機能には類似の "学習して適応する" 手法が適用されています。この手法は、解決困難なクエリの最適化問題に関連するパフォーマンス問題にさらに深く取り組むために役立っています。

## <a name="advanced-security-and-compliance"></a>高度なセキュリティとコンプライアンス

SQL Database は、アプリケーションがさまざまなセキュリティとコンプライアンスの要件を満たすために役立つ、幅広い[組み込みのセキュリティ機能とコンプライアンス機能](sql-database-security-overview.md)を備えています。

> [!IMPORTANT]
> Azure SQL Database (すべてのデプロイ オプション) は、さまざまなコンプライアンス標準に対して認定されています。 詳細については、[Microsoft Azure セキュリティ センター](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942)に関するページを参照してください。ここから最新の SQL Database コンプライアンス証明書の一覧を入手できます。

### <a name="advance-threat-protection"></a>高度な脅威保護

Advanced Data Security は、高度な SQL セキュリティ機能のための統合パッケージです。 その機能には、機密データの探索と分類、データベースの脆弱性の管理、データベースへの脅威を示す可能性がある異常なアクティビティの検出などが含まれます。 これらの機能を 1 つの場所で有効にして管理できます。

- [データの検出と分類](sql-database-data-discovery-and-classification.md):

  データの検出と分類 (現在プレビュー段階) では、Azure SQL Database に組み込まれる、データベースの機微なデータの検出、分類、ラベル付と保護を行う機能が用意されています。 データベースの分類の状態を把握し、データベース内やその境界を越えて機密データへのアクセスを追跡するために使用できます。
- [脆弱性評価](sql-vulnerability-assessment.md):

  このサービスは、データベースの潜在的な脆弱性を検出、追跡し、その修復を支援するものです。 セキュリティの状態を表示することができ、セキュリティの問題を解決して、データベースのセキュリティを強化するために実行可能な手順が含まれます。
- [脅威検出](sql-database-threat-detection.md):

  この機能では、データベースにアクセスしたりデータベースを悪用したりしようとする、通常とは異なる、害を及ぼす可能性のある試行を示す異常なアクティビティが検出されます。 データベースでの不審なアクティビティを継続的に監視し、潜在的な脆弱性、SQL インジェクション攻撃、および異常なデータベース アクセス パターンが見つかるとすぐにセキュリティ通知を提供します。 脅威検出によるアラートは、不審なアクティビティの詳細と、脅威の調査や危険性の軽減のために推奨される対処方法を提供します。

### <a name="auditing-for-compliance-and-security"></a>コンプライアンスとセキュリティの監査

[監査](sql-database-auditing.md)では、データベース イベントを追跡し、Azure Storage アカウントの監査ログにイベントを書き込みます。 監査により、規定遵守の維持、データベース活動の理解、およびビジネス上の懸念やセキュリティ違犯の疑いを示す差異や異常に対する洞察が容易になります。

### <a name="data-encryption"></a>データの暗号化

SQL Database は、暗号化によってデータのセキュリティを保護します。移動中のデータには[トランスポート層セキュリティ](https://support.microsoft.com/kb/3135244)を使用し、保存データには[透過的なデータ暗号化](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql)を使用し、使用中のデータには[常に暗号化](https://docs.microsoft.com/sql/relational-databases/security/encryption/always-encrypted-database-engine)を使用します。

### <a name="azure-active-directory-integration-and-multi-factor-authentication"></a>Azure Active Directory との統合と多要素認証

SQL Database では、[Azure Active Directory との統合](sql-database-aad-authentication.md)によって、データベース ユーザーの ID とその他の Microsoft サービスを一元的に管理できます。 この機能は、アクセス許可の管理を簡略化し、セキュリティを強化します。 Azure Active Directory は、[多要素認証](sql-database-ssms-mfa-authentication.md) (MFA) をサポートしています。これにより、シングル サインオン プロセスをサポートすると同時に、データとアプリケーションのセキュリティが強化されます。

### <a name="compliance-certification"></a>コンプライアンス認証

SQL Database は、定期監査に参加し、さまざまなコンプライアンス基準に対する認証を受けています。 詳細については、[Microsoft Azure セキュリティ センター](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942)に関するページを参照してください。ここから最新の SQL Database コンプライアンス証明書の一覧を入手できます。

## <a name="easy-to-use-tools"></a>使いやすいツール

SQL Database は、アプリケーションの開発と管理をより簡単で生産的にします。 SQL Database を使用すると、優れたアプリの構築に注力することができます。 既に所有しているツールとスキルを使用して、SQL Database で管理と開発を行うことができます。

- **[Azure portal](https://portal.azure.com/)** :

  すべての Azure サービスを管理するための Web ベースのアプリケーションです
- **[SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms)** :

  SQL Server から SQL Database まで、あらゆる SQL インフラストラクチャを管理するための、無料でダウンロードできるクライアント アプリケーションです
- **[Visual Studio での SQL Server Data Tools](https://docs.microsoft.com/sql/ssdt/download-sql-server-data-tools-ssdt)** :

  SQL Server リレーショナル データベース、Azure SQL データベース、Integration Services パッケージ、Analysis Services データ モデル、および Reporting Services レポートを開発するための、無料でダウンロードできるクライアント アプリケーションです。
- **[Visual Studio Code](https://code.visualstudio.com/docs)** :

  Linux、macOS、および Windows 用の無料でダウンロードできるオープン ソースのコード エディターです。Microsoft SQL Server、Azure SQL Database、および SQL Data Warehouse のデータを照会するための [mssql 拡張機能](https://aka.ms/mssql-marketplace)を含む拡張機能をサポートします。

SQL Database は、MacOS、Linux、および Windows での Python、Java、Node.js、PHP、Ruby、および .NET によるアプリケーションの構築をサポートします。 SQL Database は、SQL Server と同じ[接続ライブラリ](sql-database-libraries.md)をサポートします。

[!INCLUDE [sql-database-create-manage-portal](includes/sql-database-create-manage-portal.md)]

## <a name="sql-database-frequently-asked-questions-faq"></a>SQL Database に関してよく寄せられる質問 (FAQ)

### <a name="what-is-the-current-version-of-sql-database"></a>SQL Database の現在のバージョンは何ですか

SQL Database の現在のバージョンは V12 です。 バージョン V11 は廃止されました。

### <a name="can-i-control-when-patching-downtime-occurs"></a>いつ修正プログラムの適用によるダウンタイムを発生させるかを制御できますか。

いいえ。 修正プログラムの適用の影響は、アプリに[再試行ロジックを採用](sql-database-develop-overview.md#resiliency)していれば、通常は顕著なものではありません。 Azure SQL データベースの計画メンテナンス イベントに備える方法の詳細については、「[Azure SQL Database での Azure メンテナンス イベントの計画](sql-database-planned-maintenance.md)」を参照してください。

### <a name="azure-hybrid-benefit-questions"></a>Azure ハイブリッド特典に関する質問

#### <a name="are-there-dual-use-rights-with-azure-hybrid-benefit-for-sql-server"></a>SQL Server 向け Azure ハイブリッド特典では、二重使用権がサポートされていますか

確実かつシームレスに移行を行うために、180 日間はライセンスの二重使用権があります。 この 180 日を過ぎると、SQL Server ライセンスは、SQL Database のクラウドでしか使用できません。オンプレミスとクラウドでの二重使用権はなくなります。

#### <a name="how-does-azure-hybrid-benefit-for-sql-server-differ-from-license-mobility"></a>SQL Server 向け Azure ハイブリッド特典とライセンス モビリティの違いは何ですか

現在、Microsoft では、SQL Server のお客様に、ソフトウェア アシュアランスのライセンス モビリティ特典を提供しています。これにより、サード パーティ共有サーバーへのライセンスの再割り当てが可能になります。 この特典は、Azure IaaS および AWS EC2 でご利用いただけます。
SQL Server 向け Azure ハイブリッド特典は、次の 2 つの重要な点において、ライセンス モビリティとは異なります。

- 高度に仮想化されたワークロードを Azure に移動することで、コスト面でメリットがもたらされます。 SQL EE のお客様は、高度に仮想化されたアプリケーション用にオンプレミスで所有するコアごとに、Azure の General Purpose SKU で 4 つのコアを取得できます。 ライセンス モビリティの場合、仮想化されたワークロードをクラウドに移動しても、コスト上のメリットは特にありません。
- オンプレミスの SQL Server と高い互換性がある PaaS 移行先を Azure (SQL Database マネージド インスタンス) に提供します。

#### <a name="what-are-the-specific-rights-of-the-azure-hybrid-benefit-for-sql-server"></a>SQL Server 向け Azure ハイブリッド特典では、具体的にはどのような権限が付与されますか

SQL Database のお客様には、SQL Server 向け Azure ハイブリッド特典によって次の権限が付与されます。

|ライセンス フットプリント|SQL Server 向け Azure ハイブリッド特典の内容|
|---|---|
|SA を含む SQL Server Enterprise Edition の中核的なお客様|<li>General Purpose SKU または Business Critical SKU のいずれかで基本料金を支払うことができます</li><br><li>オンプレミスの 1 コア = General Purpose SKU の 4 コア</li><br><li>オンプレミスの 1 コア = Business Critical SKU の 1 コア</li>|
|SA を含む SQL Server Standard Edition の中核的なお客様|<li>General Purpose SKU のみで基本料金を支払うことができます</li><br><li>オンプレミスの 1 コア = General Purpose SKU の 1 コア</li>|
|||

## <a name="engage-with-the-sql-server-engineering-team"></a>SQL Server エンジニアリング チームとの交流

- [DBA Stack Exchange](https://dba.stackexchange.com/questions/tagged/sql-server):データベースの管理に関するご質問はこちらへ
- [Stack Overflow](https://stackoverflow.com/questions/tagged/sql-server):開発に関する質問はこちらへ
- [MSDN フォーラム](https://social.msdn.microsoft.com/Forums/home?category=sqlserver):技術的なご質問はこちらへ
- [フィードバック](https://aka.ms/sqlfeedback):バグの報告や機能要求
- [Reddit](https://www.reddit.com/r/SQLServer/):SQL Server についての意見交換

## <a name="next-steps"></a>次の手順

- 単一データベースとエラスティック プールのコストの比較と計算ツールについては、[価格に関するページ](https://azure.microsoft.com/pricing/details/sql-database/)を参照してください。
- すぐに始めるには、次のクイック スタートをご覧ください。

  - [Azure Portal で SQL データベースを作成する](sql-database-single-database-get-started.md)  
  - [Azure CLI で SQL データベースを作成する](sql-database-get-started-cli.md)
  - [PowerShell を使用して SQL データベースを作成する](sql-database-get-started-powershell.md)

- Azure CLI と PowerShell の各種サンプルについては、以下のページをご覧ください。
  - [Azure SQL Database 用の Azure CLI サンプル](sql-database-cli-samples.md)
  - [Azure SQL Database 用の Azure PowerShell サンプル](sql-database-powershell-samples.md)

 - 発表される新しい機能の詳細については、以下を参照してください。 
   - **[SQL Database に関する Azure ロードマップ](https://azure.microsoft.com/roadmap/?category=databases)** : 最新情報や今後の予定を知ることができます。
  - **[Azure SQL Database のブログ](https://azure.microsoft.com/blog/topics/database)** : SQL Database に関するニュースと機能についての SQL Server 製品チームのメンバーのブログです。

