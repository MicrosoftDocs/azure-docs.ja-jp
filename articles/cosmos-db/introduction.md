---
title: Azure Cosmos DB の概要
description: Azure Cosmos DB について説明します。 このグローバルに分散されたマルチモデル データベースは、少ない待ち時間、柔軟なスケーラビリティ、高可用性を実現し、NoSQL データのネイティブ サポートを提供することを目指して構築されています。
author: rimman
ms.service: cosmos-db
ms.topic: overview
ms.date: 07/23/2019
ms.author: rimman
ms.openlocfilehash: ee863b6003444ec099753a6488f3fe4078338e60
ms.sourcegitcommit: 04ec7b5fa7a92a4eb72fca6c6cb617be35d30d0c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/22/2019
ms.locfileid: "68384124"
---
# <a name="welcome-to-azure-cosmos-db"></a>Azure Cosmos DB の概要

現在のアプリケーションには、優れた応答性と、常時接続が求められます。 待機時間の短縮と高可用性を実現するには、これらのアプリケーションのインスタンスをそのユーザーの近くにあるデータ センターにデプロイする必要があります。 アプリケーションには、ピーク時の使用量の大きな変化に即応し、増え続けるデータを格納し、ミリ秒単位でユーザーがこうしたデータを利用できるようにすることが求められます。

Azure Cosmos DB は、Microsoft によってグローバルに配布されるマルチモデル データベース サービスです。 Cosmos DB では、ボタンを 1 つクリックするだけで、世界中の Azure リージョンをいくつでもまたいでスループットとストレージをエラスティックかつ個別にスケーリングできます。 スループットとストレージをエラスティックにスケーリングし、SQL、MongoDB、Cassandra、Tables、Gremlin などの好みの API を使用して、数ミリ秒 (1 桁) の高速データ アクセスを利用できます。 Cosmos DB では、スループット、待ち時間、可用性、および整合性の保証について包括的な[サービス レベル アグリーメント](https://aka.ms/acdbsla) (SLA) が提供されています。この点は、他のどのデータベース サービスにもないメリットです。

Azure サブスクリプションを必要とせず、課金や契約もなく [Azure Cosmos DB を無料で試す](https://azure.microsoft.com/try/cosmosdb/)ことができます。

> [!div class="nextstepaction"]
> [Azure Cosmos DB を無料で試す](https://azure.microsoft.com/try/cosmosdb/)

![Azure Cosmos DB は世界規模で分散される Microsoft のデータベース サービスであり、柔軟なスケールアウト、短い待ち時間の保証、5 つの整合性モデル、充実した保証を提供する SLA を特徴としています](./media/introduction/azure-cosmos-db.png)

## <a name="key-benefits"></a>主な利点

### <a name="turnkey-global-distribution"></a>ターンキー グローバル分散

Cosmos DB を使用すると、応答性と可用性が高いアプリケーションを世界的な規模で構築できます。 Cosmos DB では、ユーザーがどこにいてもデータが透過的にレプリケートされるので、ユーザーは最も近い場所にあるデータのレプリカと対話できます。

Cosmos DB では、ボタンをクリックするだけで、いつでも Cosmos アカウントに Azure リージョンを追加したり、アカウントからリージョンを削除したりできます。 Cosmos アカウントに関連付けられたすべてのリージョンにデータがシームレスにレプリケートされ、アプリケーションではサービスの "*マルチホーム*" 機能により高可用性を維持できます。 詳しくは、[グローバル分散](distribute-data-globally.md)に関する記事をご覧ください。

### <a name="always-on"></a>常時接続

Azure インフラストラクチャとの緊密な統合と、[透過的なマルチマスター レプリケーション](global-dist-under-the-hood.md)により、Cosmos DB では読み取りと書き込みの両方に対して [99.999% の高可用性](high-availability.md)が実現されます。 Cosmos DB では、Cosmos アカウントのリージョン間フェールオーバーをプログラムで (またはポータルを使用して) 呼び出す機能も提供されています。 この機能は、リージョン規模の災害が発生した場合にアプリケーションがフェールオーバーするよう設計するのに役立ちます。

### <a name="elastic-scalability-of-throughput-and-storage-worldwide"></a>世界規模でのスループットとストレージのエラスティック スケーラビリティ

透過的な行方向のパーティション分割とマルチマスター レプリケーションを使用して設計された Cosmos DB では、書き込みと読み取りの比類ないエラスティック スケーラビリティが世界規模で実現されます。 世界中のどこでも 1 回の API 呼び出しで 1 秒あたりの要求数を数千から数十億にエラスティックにスケールアップでき、必要な分のスループット (およびストレージ) に対してのみ料金が発生します。 この機能は、ピークに対して過剰なプロビジョニングを行うことなく、ワークロードの予期しないスパイクに対処するのに役立ちます。 詳細については、[Cosmos DB でのパーティション分割](partitioning-overview.md)、[コンテナーとデータベースでのプロビジョニング済みスループット](set-throughput.md)、[プロビジョニング済みスループットのグローバルなスケーリング](scaling-throughput.md)に関する記事を参照してください。

### <a name="guaranteed-low-latency-at-99th-percentile-worldwide"></a>世界規模で保証される 99 パーセンタイルの低待機時間

Cosmos DB を使用すると、応答性に優れた世界規模のアプリケーションを構築できます。 その斬新なマルチマスター レプリケーション プロトコル、およびラッチフリーでかつ[書き込みに最適化されたデータベース エンジン](index-policy.md)により、Cosmos DB では、世界中で読み取り (インデックス付き) と書き込みの両方に対して 99 パーセンタイルでの 10 ミリ秒未満の待機時間が保証されます。 この機能により、データのインジェストの維持と超高速のクエリが可能になり、アプリの応答性が向上します。

### <a name="precisely-defined-multiple-consistency-choices"></a>明確に定義された複数の整合性の選択肢

Cosmos DB でグローバルに分散されたアプリケーションを構築する際に、極端な[整合性、可用性、待ち時間、スループットのトレードオフ](consistency-levels-tradeoffs.md)を行う必要がなくなりました。 Cosmos DB のマルチマスター レプリケーション プロトコルは、"*強固*"、"*有界整合性制約*"、"*セッション*"、"*一貫性のあるプレフィックス*"、"*最終*" という [5 つの明確な整合性の選択肢](consistency-levels.md) -  を提供するよう慎重に設計されており、グローバル分散アプリケーションでも、待ち時間が短く可用性に優れた直感的なプログラミング モデルを実現できます。

### <a name="no-schema-or-index-management"></a>スキーマやインデックスの管理が不要

グローバル分散型アプリでは、データベースのスキーマやインデックスとアプリケーションのスキーマの同期に特に手間がかかります。 Cosmos DB では、スキーマやインデックスの管理に対応する必要はありません。 データベース エンジンがスキーマにまったく依存しないためです。  スキーマとインデックスの管理が必要ないため、スキーマ移行中のアプリケーションのダウンタイムを心配する必要もありません。 Cosmos DB では、[すべてのデータのインデックス付けが自動的に行われ](index-policy.md)、高速クエリが提供されます。

### <a name="battle-tested-database-service"></a>厳しくテストされたデータベース サービス

Cosmos DB は、Azure の基盤サービスです。 10 年近くにわたり、Cosmos DB は Skype、Xbox、Office 365、Azure など、Microsoft の多くの製品により、世界的な規模でミッション クリティカルなアプリケーションに使用されてきました。 今日では、Cosmos DB は、読み取りと書き込み両方の低待ち時間と高可用性のためにエラスティックなスケーリング、ターンキー グローバル分散、マルチマスター レプリケーションを必要とする多くの外部の顧客やミッション クリティカルなアプリケーションによって使用される、Azure で最も急速に成長しているサービスの 1 つになっています。

### <a name="ubiquitous-regional-presence"></a>ユビキタスなリージョン プレゼンス

Cosmos DB は、パブリック クラウドの 54 以上のリージョン、[Azure China 21Vianet](https://www.azure.cn/en-us/)、Azure Germany、Azure Government、および Azure Government for Department of Defense (DoD) など、世界中のすべての Azure リージョンで利用できます。 「[Azure Cosmos DB のリージョン プレゼンス](regional-presence.md)」をご覧ください。

### <a name="secure-by-default-and-enterprise-ready"></a>既定でのセキュリティ保護とエンタープライズ対応

Cosmos DB は、[さまざまなコンプライアンス基準](compliance.md)によって認定されています。 さらに、Cosmos DB のすべてのデータは、保管中も移動中も暗号化されています。 Cosmos DB では、行レベルの承認が提供され、厳密なセキュリティ標準に準拠します。

### <a name="significant-tco-savings"></a>TCO の大幅な削減

Cosmos DB はフル マネージド サービスであるため、複雑なマルチ データセンターのデプロイやデータベース ソフトウェアのアップグレードを管理および運用したり、サポート、ライセンス、運用の料金を支払ったりする必要はありません。また、ピーク ワークロードに合わせてデータベースをプロビジョニングする必要もありません。 詳細については、[Cosmos DB を使用したコストの最適化](total-cost-ownership.md)に関するページを参照してください。

### <a name="industry-leading-comprehensive-slas"></a>業界をリードする包括的な SLA

Cosmos DB は、99.999% の高可用性、99 パーセンタイルでの読み取りと書き込みの待機時間、確実なスループット、整合性を実現する[業界トップの包括的な SLA](https://azure.microsoft.com/support/legal/sla/cosmos-db/) が用意された史上初のサービスです。

### <a name="globally-distributed-operational-analytics-and-ai-with-natively-built-in-apache-spark"></a>Apache Spark がネイティブで組み込まれたグローバル分散型の運用分析と AI

Cosmos DB の格納データに対して [Spark](spark-connector.md) を直接実行することができます。 この機能を使用すると、Cosmos DB を直接使用するトランザクション ワークロードに影響を与えることなく、世界的な規模で低遅延の運用分析を行うことができます。 詳細については、[グローバルに分散された運用分析](lambda-architecture.md)に関するページを参照してください。

### <a name="develop-applications-on-cosmos-db-using-popular-open-source-software-oss-apis"></a>人気のあるオープン ソース ソフトウェア (OSS) API を使用した Cosmos DB でのアプリケーションの開発

Cosmos DB では、Cosmos データベースに格納されているデータを操作するための API を選択できます。 既定では、Cosmos データベースに対するクエリを実行するために [SQL](how-to-sql-query.md) (コア API) を使用することができます。 Cosmos DB では、[Cassandra](cassandra-introduction.md)、[MongoDB](mongodb-introduction.md)、[Gremlin](graph-introduction.md)、および [Azure Table Storage](table-introduction.md) の API も実装されています。 よく使用される NoSQL (MongoDB、Cassandra、Gremlin など) のクライアント ドライバー (およびツール) で Cosmos データベースを直接参照することができます。 よく使用される NoSQL API のワイヤ プロトコルがサポートされているため、Cosmos DB では次のことが可能です。

* アプリケーション ロジックの重要な部分を保持しながら、Cosmos DB にアプリケーションを簡単に移行できます。
* アプリケーションの移植性を保持して、クラウド ベンダーに非依存な状態を維持できます。
* 一般的な NoSQL API 向けに、業界最高レベルの、ご利用料金に基づく SLA が提供されるフルマネージド クラウド サービスが手に入ります。 
* データベースにプロビジョニング済みのスループットと容量を、ニーズに応じて弾力的にスケーリングでき、支払いは必要なスループットとストレージの分のみとなります。 これにより、大幅にコストを削減できます。

## <a name="solutions-that-benefit-from-azure-cosmos-db"></a>Azure Cosmos DB の恩恵を受けるソリューション

さまざまなデータについて、リアルタイムに近い応答時間と[グローバルな規模](distribute-data-globally.md)で膨大な量のデータや読み書きを処理する必要のある [Web、モバイル、ゲーム、IoT アプリケーション](use-cases.md)は、Cosmos DB の[保証された高可用性](https://azure.microsoft.com/support/legal/sla/cosmos-db/)、高スループット、低待ち時間、調整可能な整合性の恩恵を受けます。 [IoT とテレマティック](use-cases.md#iot-and-telematics)、[小売りとマーケティング](use-cases.md#retail-and-marketing)、[ゲーム](use-cases.md#gaming)、[Web アプリとモバイル アプリ](use-cases.md#web-and-mobile-applications)の作成に Azure Cosmos DB をどのように適用できるかをご確認ください。

## <a name="next-steps"></a>次の手順

Cosmos DB のコア概念である[ターンキー グローバル分散](distribute-data-globally.md)、[パーティション分割](partitioning-overview.md)、および[プロビジョニング済みのスループット](request-units.md)に関する記事を参照してください。

以下のクイック スタートのいずれかに従って、実際に Azure Cosmos DB を使ってみましょう。

* [Azure Cosmos DB SQL API を使ってみる](create-sql-api-dotnet.md)
* [Azure Cosmos DB の MongoDB 用 API の概要](create-mongodb-nodejs.md)
* [Azure Cosmos DB Cassandra API を使ってみる](create-cassandra-dotnet.md)
* [Azure Cosmos DB Gremlin API を使ってみる](create-graph-dotnet.md)
* [Azure Cosmos DB Table API を使ってみる](create-table-dotnet.md)

> [!div class="nextstepaction"]
> [Azure Cosmos DB を無料で試す](https://azure.microsoft.com/try/cosmosdb/)
