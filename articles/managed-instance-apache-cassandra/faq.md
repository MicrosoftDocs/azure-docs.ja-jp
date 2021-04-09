---
title: Azure Managed Instance for Apache Cassandra について Azure portal からよく寄せられる質問
description: Azure Managed Instance for Apache Cassandra についてよく寄せられる質問。 この記事では、マネージド インスタンスを使用すべき場面、利点、スループットの制限、サポート対象のリージョン、その他構成の詳細に関する質問を取り上げます。
author: TheovanKraay
ms.author: thvankra
ms.service: managed-instance-apache-cassandra
ms.topic: quickstart
ms.date: 03/02/2021
ms.openlocfilehash: 1ba2b7d648c86912118b83a566bf2eb0800baee2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "101747640"
---
# <a name="frequently-asked-questions-about-azure-managed-instance-for-apache-cassandra-preview"></a>Azure Managed Instance for Apache Cassandra についてよく寄せられる質問 (プレビュー)

この記事では、Azure Managed Instance for Apache Cassandra についてよく寄せられる質問について取り上げます。 マネージド インスタンスを使用すべき場面とその利点、スループットの制限、サポート対象のリージョン、構成の詳細について説明します。

> [!IMPORTANT]
> Azure Managed Instance for Apache Cassandra は現在、パブリック プレビューの段階にあります。
> このプレビュー バージョンはサービス レベル アグリーメントなしで提供されています。運用環境のワークロードに使用することはお勧めできません。 特定の機能はサポート対象ではなく、機能が制限されることがあります。
> 詳しくは、[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)に関するページをご覧ください。

## <a name="general-faq"></a>一般的な FAQ

### <a name="what-are-the-benefits-azure-managed-instance-for-apache-cassandra"></a>Azure Managed Instance for Apache Cassandra の利点は何ですか?

Apache Cassandra データベースは、パフォーマンスを犠牲にすることなくスケーラビリティと高可用性が要求される場面で適した選択肢となります。 汎用的なハードウェアやクラウド インフラストラクチャにおけるその線形のスケーラビリティと実績のあるフォールトトレランスから、ミッションクリティカルなデータのための優れたプラットフォームとなっています。 Azure Managed Instance for Apache Cassandra は、Azure にデプロイされたオープンソースの Apache Cassandra データセンターのインスタンスを管理するためのサービスです。

完全にクラウドで使用できるほか、クラウドとオンプレミスのハイブリッド クラスターの構成要素として使用することができます。 メンテナンスのオーバーヘッドなしで、オープンソースの Apache Cassandra におけるきめ細かな構成と制御が必要な場合は、このサービスが最適です。

### <a name="why-should-i-use-this-service-instead-of-azure-cosmos-db-cassandra-api"></a>Azure Cosmos DB Cassandra API ではなくこのサービスを使用すべき理由は何ですか?

Azure Managed Instance for Apache Cassandra は、Azure Cosmos DB チームによって提供されています。 これは、オープンソースの Apache Cassandra データセンターとクラスターをデプロイ、保守、スケーリングするためのスタンドアロンのマネージド サービスです。 一方、[Azure Cosmos DB Cassandra API](../cosmos-db/cassandra-introduction.md) はサービスとしてのプラットフォームであり、Apache Cassandra のワイヤ プロトコル向けに相互運用性レイヤーを提供するものです。 Apache Cassandra クラスターとまったく同じように動作することをこのプラットフォームに求めるなら、マネージド インスタンス サービスを選ぶ必要があります。 詳細については、[Azure Managed Instance for Apache Cassandra と Azure Cosmos DB Cassandra API の違い](compare-cosmosdb-managed-instance.md)に関する記事を参照してください。

### <a name="is-azure-managed-instance-for-apache-cassandra-dependent-on-azure-cosmos-db"></a>Azure Managed Instance for Apache Cassandra は Azure Cosmos DB に依存していますか?

いいえ。Azure Managed Instance for Apache Cassandra と Azure Cosmos DB バックエンドとの間にアーキテクチャ上の依存関係はありません。 

#### <a name="can-i-deploy-azure-managed-instance-for-apache-cassandra-in-any-region"></a>Azure Managed Instance for Apache Cassandra はどのリージョンにでもデプロイできますか?

プレビュー期間中、マネージド インスタンスを利用できるのは限られたリージョンのみとなります。

### <a name="what-are-the-storage-and-throughput-limits-of-azure-managed-instance-for-apache-cassandra"></a>Azure Managed Instance for Apache Cassandra のストレージとスループットの制限を教えてください

これらの制限は、選択した仮想マシンの SKU によって異なります。

### <a name="what-is-the-cost-of-azure-managed-instance-for-apache-cassandra"></a>Azure Managed Instance for Apache Cassandra のコストを教えてください

マネージド インスタンスの料金は、基になる VM のコストをベースに、わずかな利幅を加えて計算されます。 詳細については、 [価格に関するページ](https://azure.microsoft.com/pricing/details/managed-instance-apache-cassandra/)を参照してください。

### <a name="can-i-use-yaml-file-settings-to-configure-behavior"></a>YAML ファイル設定を使用して動作を構成できますか?

はい。Azure Resource Manager テンプレートのデプロイの一環として YAML ファイルの構成を埋め込むことができます。

### <a name="how-can-i-monitor-infrastructure-along-with-throughput"></a>インフラストラクチャとスループットを監視するにはどうすればよいですか?

[Prometheus](https://prometheus.io/docs/introduction/overview/) サーバーは、クラスター全体のアクティビティを監視する目的でホストされ、エンドポイントを公開します。 データは、10 GB に達するか 10 分が経過するまで (どちらかのしきい値に達するまで) 保持されます。 この監視機能を使用するには、[フェデレーション](https://prometheus.io/docs/prometheus/latest/federation/)と適切なダッシュボード ツール (Grafana など) を設定する必要があります。

### <a name="does-azure-managed-instance-for-apache-cassandra-provide-full-backups"></a>Azure Managed Instance for Apache Cassandra は完全バックアップを提供していますか?

はい。Azure Storage への完全バックアップと新しいクラスターへの復元を提供しています。

### <a name="how-can-i-migrate-data-from-my-existing-apache-cassandra-cluster-to-azure-managed-instance-for-apache-cassandra"></a>既存の Apache Cassandra クラスターから Azure Managed Instance for Apache Cassandra にデータを移行するにはどうすればよいですか?

Azure Managed Instance for Apache Cassandra は、データセンター間でのデータのレプリケートとストリーム配信を行う Apache Cassandra の機能をすべてサポートしています。

### <a name="can-i-pair-an-on-premises-apache-cassandra-cluster-with-the-azure-managed-instance-for-apache-cassandra"></a>オンプレミスの Apache Cassandra クラスターと Azure Managed Instance for Apache Cassandra を組み合わせることはできますか?

はい。このサービスによってデプロイされた Azure 仮想ネットワーク インジェクション型データセンターを使用することで、ハイブリッド クラスターを構成することができます。 マネージド インスタンス データセンターは、同じクラスター リング内のオンプレミス データセンターと通信を行うことができます。

### <a name="where-can-i-give-feedback-on-azure-managed-instance-for-apache-cassandra-features"></a>Azure Managed Instance for Apache Cassandra の機能に関するフィードバックはどこに送ればよいですか?

フィードバックは、[UserVoice のフィードバック](https://feedback.azure.com/forums/263030-azure-cosmos-db?category_id=398548)から、"Managed Apache Cassandra" カテゴリを使用してお寄せください。

アカウントを使用して問題を修正するには、Azure Portal で[サポート要求](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)を提出します。

## <a name="deployment-specific-faq"></a>デプロイ固有の FAQ

### <a name="will-the-managed-instance-support-node-addition-cluster-status-and-node-status-commands"></a>このマネージド インスタンスは、ノードの追加、クラスター状態、ノード状態の各コマンドをサポートしていますか?

"*読み取り専用*" の Nodetool コマンド (`status` など) はすべて Azure CLI からご利用いただけます。 ただし、マネージド インスタンス内のノードの正常性は Microsoft が管理しているため、"*ノードの追加*" などの操作はご利用いただけません。 ハイブリッド モードでは、*Nodetool* を使用してクラスターに接続できます。 ただし、Nodetool の使用は、クラスターが不安定になる可能性があるため推奨されません。 また、クラスター内のマネージド インスタンス データセンターの正常性に関連する運用サポート SLA も無効になる場合があります。

### <a name="what-happens-with-various-settings-for-table-metadata"></a>テーブル メタデータのさまざまな設定を使用するとどうなりますか?

テーブル メタデータの設定 (bloom filter、caching、read repair chance、gc_grace、compression memtable_flush_period など) は、セルフホステッド Apache Cassandra 環境と同様、完全にサポートされています。

## <a name="next-steps"></a>次のステップ

他の API に関してよく寄せられる質問については、以下を参照してください。

* [Azure portal からマネージド インスタンス クラスターを作成する](create-cluster-portal.md)
* [Azure Databricks でマネージド Apache Spark クラスターをデプロイする](deploy-cluster-databricks.md)
* [Azure CLI を利用して Azure Managed Instance for Apache Cassandra リソースを管理する](manage-resources-cli.md)