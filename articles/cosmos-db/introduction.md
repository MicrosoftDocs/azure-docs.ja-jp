---
title: Azure Cosmos DB の概要
description: Azure Cosmos DB について説明します。 このグローバルに分散されたマルチモデル データベースは、少ない待ち時間、柔軟なスケーラビリティ、高可用性を実現し、NoSQL データのネイティブ サポートを提供することを目指して構築されています。
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: overview
ms.date: 10/23/2020
ms.openlocfilehash: 9df5c63ef175d0cf736ce16036466158ab6565f9
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102618306"
---
# <a name="welcome-to-azure-cosmos-db"></a>Azure Cosmos DB の概要
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

現在のアプリケーションには、優れた応答性と、常時接続が求められます。 待機時間の短縮と高可用性を実現するには、これらのアプリケーションのインスタンスをそのユーザーの近くにあるデータ センターにデプロイする必要があります。 アプリケーションには、ピーク時の使用量の大きな変化に即応し、増え続けるデータを格納し、ミリ秒単位でユーザーがこうしたデータを利用できるようにすることが求められます。

Azure Cosmos DB は、最新のアプリ開発に対応するフル マネージドの NoSQL データベースです。 数ミリ秒 (1 桁台) の応答時間と、自動および即時のスケーラビリティにより、あらゆるスケールで速度が保証されます。 [SLA に基づいた](https://azure.microsoft.com/support/legal/sla/cosmos-db)可用性とエンタープライズグレードのセキュリティにより、ビジネス継続性が保証されます。 世界中のあらゆる場所でのターンキー マルチ リージョン データ分散と、人気のある言語用のオープン ソース API シリーズと SDK により、アプリの開発をより速く、より生産的に行うことができるようになります。 Azure Cosmos DB では、フル マネージド サービスとして、管理、更新、およびパッチ適用が自動的に行われるため、ユーザー側でのデータベース管理が不要になります。 また、アプリケーションのニーズに対応して需要に容量を一致させるコスト効果の高いサーバーレスおよび自動スケーリング オプションにより、容量管理が処理されます。

> [!NOTE]
> Azure Cosmos DB のドキュメントの品質向上のためのユーザー調査にご協力ください。 こちらの 5 分間の[事前アンケート](https://aka.ms/cosmosdb-documentation-screener-survey)にご記入ください。 条件に一致した方は、スケジューラにリダイレクトされます。このスケジューラで、対話型の調査セッションに参加するスロットを予約できます。 Microsoft [プライバシー ステートメント](https://go.microsoft.com/fwlink/?LinkId=521839)に従い、この過程で個人データが収集されることはありません。

Azure サブスクリプション、課金、契約がなくても [Azure Cosmos DB を無料でお試し](https://azure.microsoft.com/try/cosmosdb/)いただけます。または、[Azure Cosmos DB Free レベル](optimize-dev-test.md#azure-cosmos-db-free-tier)を使用してアカウントを取得し、最初の 400 RU/秒と 5 GB のストレージを無料でご利用いただけます。

> [!div class="nextstepaction"]
> [Azure Cosmos DB を無料で試す](https://azure.microsoft.com/try/cosmosdb/)

:::image type="content" source="./media/introduction/azure-cosmos-db.png" alt-text="Azure Cosmos DB は、最新のアプリ開発に対応するフル マネージドの NoSQL データベースです。" border="false":::

## <a name="key-benefits"></a>主な利点

### <a name="guaranteed-speed-at-any-scale"></a>あらゆるスケールで保証された速度

比類のない [SLA に基づいた](https://azure.microsoft.com/support/legal/sla/cosmos-db)速度とスループット、高速なグローバル アクセス、および即時のエラスティック性を実現します。

- 読み取りと書き込みの待ち時間が短いグローバルなリアルタイム アクセス、スループット、および整合性のすべてが [SLA](https://azure.microsoft.com/support/legal/sla/cosmos-db) に基づきます
- ボタンをクリックするだけで、マルチリージョン書き込みと任意の Azure リージョンへのデータ分散を行うことができます。
- 予測できないトラフィックのバーストが発生している場合でも、任意の Azure リージョンにわたってストレージとスループットを独立してエラスティックにスケーリングできます (世界中で無制限にスケーリング)。

### <a name="simplified-application-development"></a>簡略化されたアプリケーション開発

オープン ソース API シリーズ、複数の SDK、スキーマレス データ、運用データに対する非 ETL 分析を使用して迅速な構築を行うことができます。

- Azure Functions、IoT Hub、AKS (Azure Kubernetes Service)、App Service などの最新の (クラウドネイティブ) アプリ開発で使用される主要な Azure サービスと緊密に統合されます。
- ネイティブ Core (SQL) API、MongoDB 用 API、Cassandra API、Gremlin API、Table API などの複数のデータベース API シリーズから選択できます。
- .NET、Java、Node.js、Python 用の SDK で任意の言語を使用して、Core (SQL) API でアプリを構築できます。 または、他の任意のデータベース API シリーズ用のドライバーを選択できます。
- Azure Synapse Analytics を使用して、Azure Cosmos DB に格納されているほぼリアルタイムの運用データに対して非 ETL 分析を実行できます。
- 変更フィードを使用すると、簡単にデータベース コンテナーに対する変更を追跡および管理し、Azure Functions を使用してトリガー イベントを作成できます。
- Azure Cosmos DB のスキーマレス サービスでは、データ モデルに関係なくすべてのデータに自動的にインデックスを付けて、きわめて高速なクエリを実現します。

### <a name="mission-critical-ready"></a>ミッション クリティカル対応

すべてのアプリケーションについて、ビジネス継続性、99.999% の可用性、およびエンタープライズレベルのセキュリティが保証されます。

- Azure Cosmos DB は、業界をリードする可用性を含む [SLA](https://azure.microsoft.com/support/legal/sla/cosmos-db) の包括的なスイートを全世界で提供します。
- 自動データ レプリケーションにより、任意の Azure リージョンにデータを簡単に分散できます。 厳密な整合性を使用した場合は、マルチリージョン書き込みまたは RPO 0 でゼロのダウンタイムが実現されます。
- 自己管理型のキーにより、エンタープライズグレードの保存時の暗号化が提供されます。
- Azure ロールベースのアクセス制御により、データが保護され、微調整された制御が提供されます。

### <a name="fully-managed-and-cost-effective"></a>フル マネージドかつ高いコスト効果

アプリケーションと TCO のニーズに対応する、サーバーレスおよび自動スケーリングによるエンドツーエンドのデータベース管理

- フルマネージド データベース サービス。 自動、ノータッチ、メンテナンス、パッチの適用、および更新プログラムにより、開発者の時間と費用を節約できます。
- あらゆるサイズまたはスケールの予測不可能または散発的なワークロードに対するコスト効果に優れたオプション。これにより、開発者は、容量を計画または管理することなく簡単に作業を開始できます。
- サーバーレス モデルは、スパイクがよく発生するワークロードに対して自動かつ応答性の高いサービスを提供して、オンデマンドでトラフィック バーストを管理します。
- プロビジョニングされたスループットの自動スケーリングにより、予測できないワークロードに対して、[SLA](https://azure.microsoft.com/support/legal/sla/cosmos-db) を維持しながら、容量が自動的かつ即座にスケーリングされます。

## <a name="solutions-that-benefit-from-azure-cosmos-db"></a>Azure Cosmos DB の恩恵を受けるソリューション

さまざまなデータについて、リアルタイムに近い応答時間と[グローバルな規模](distribute-data-globally.md)で膨大な量のデータや読み書きを処理する必要のある [Web、モバイル、ゲーム、IoT アプリケーション](use-cases.md)は、Cosmos DB の[保証された高可用性](https://azure.microsoft.com/support/legal/sla/cosmos-db/)、高スループット、低待ち時間、調整可能な整合性の恩恵を受けます。 [IoT とテレマティック](use-cases.md#iot-and-telematics)、[小売りとマーケティング](use-cases.md#retail-and-marketing)、[ゲーム](use-cases.md#gaming)、[Web アプリとモバイル アプリ](use-cases.md#web-and-mobile-applications)の作成に Azure Cosmos DB をどのように適用できるかをご確認ください。

## <a name="next-steps"></a>次のステップ

以下のクイック スタートのいずれかに従って、実際に Azure Cosmos DB を使ってみましょう。

- [Azure Cosmos DB SQL API を使ってみる](create-sql-api-dotnet.md)
- [Azure Cosmos DB の MongoDB 用 API の概要](create-mongodb-nodejs.md)
- [Azure Cosmos DB Cassandra API を使ってみる](create-cassandra-dotnet.md)
- [Azure Cosmos DB Gremlin API を使ってみる](create-graph-dotnet.md)
- [Azure Cosmos DB Table API を使ってみる](create-table-dotnet.md)

> [!div class="nextstepaction"]
> [Azure Cosmos DB を無料で試す](https://azure.microsoft.com/try/cosmosdb/)
