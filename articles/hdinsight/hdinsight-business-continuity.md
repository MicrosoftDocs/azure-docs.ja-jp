---
title: Azure HDInsight のビジネス継続性
description: この記事では、Azure HDInsight のビジネス継続性計画のベスト プラクティス、単一リージョンの可用性、および最適化オプションの概要について説明します。
keywords: Hadoop の高可用性
ms.service: hdinsight
ms.topic: conceptual
ms.date: 10/08/2020
ms.openlocfilehash: 74f8bdd26e000b89bfae84102077c241f85abf7e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98933320"
---
# <a name="azure-hdinsight-business-continuity"></a>Azure HDInsight のビジネス継続性

Azure HDInsight クラスターは、ストレージ、データベース、Active Directory、Active Directory Domain Services、ネットワーク、Key Vault などの多くの Azure サービスに依存しています。 適切に設計された、可用性の高いフォールト トレラントな分析アプリケーションは、これらのサービスの 1 つまたは複数でのリージョンまたはローカルにおける中断に耐えられるように、十分な冗長性を備えた設計にする必要があります。 この記事では、ビジネス継続性計画のベスト プラクティス、単一リージョンの可用性、および最適化オプションの概要について説明します。

## <a name="general-best-practices"></a>全般的なベスト プラクティス

このセクションでは、ビジネス継続性の計画時に考慮する必要があるベスト プラクティスについて説明します。

* 障害が発生した場合に必要とする最小限のビジネス機能とその理由を決定します。 たとえば、データ変換層 (黄色で示されています) "*および*" データ サービス層 (青色で表示) にフェールオーバー機能が必要か、またはデータ サービス層にだけフェールオーバーが必要かを評価します。

   :::image type="content" source="media/hdinsight-business-continuity/data-layers.png" alt-text="データ変換とデータ サービスの層":::

* ワークロード、開発ライフサイクル、および部門に基づいてクラスターを分割します。 クラスターを複数にすることで、単一の大きな障害によって複数の異なるビジネス プロセスが影響を受ける可能性が減少します。

* セカンダリ リージョンを読み取り専用にします。 フェールオーバー リージョンに読み取りと書き込みの両方の機能があると、アーキテクチャが複雑になる可能性があります。

* 障害が発生したとき、一時的なクラスターの方が管理が容易です。 クラスターの循環ができるように、また、状態がクラスターに保持されないように、ワークロードを設計します。

* 障害が発生し、新しいリージョンで再起動する必要がある場合、ワークロードが未完了のままになることがよくあります。 ワークロードを本質的にべき等になるように設計します。

* 障害が発生した場合に迅速かつ完全に自動化されたデプロイを実現するために、クラスターのデプロイ時に自動化を使用し、クラスター構成設定を可能な限りスクリプト化します。

* HDInsight で Azure 監視ツールを使用して、クラスター内の異常な動作を検出し、対応するアラート通知を設定します。 特定のクラスターの種類についての重要なパフォーマンス メトリックを収集する、構成済みの HDInsight クラスター固有の管理ソリューションをデプロイできます。 詳細については、[HDInsight 用の Azure Monitoring](./hdinsight-hadoop-oms-log-analytics-tutorial.md) に関するページを参照してください。  

* Azure の正常性アラートをサブスクライブして、サービスの問題と計画メンテナンスについて、および、サブスクリプション、サービス、またはリージョンの正常性とセキュリティに関するアドバイザリについての通知を受け取ります。 問題の原因と確定 ETA を含む正常性の通知は、フェールオーバーとフェールバックをより適切に実行するのに役立ちます。 詳細については、[Azure Service Health のドキュメント](../service-health/index.yml)を参照してください。

## <a name="single-region-availability"></a>単一リージョンの可用性

基本的な HDInsight システムには、次のコンポーネントがあります。 すべてのコンポーネントに、それら独自の単一リージョン フォールト トレランス メカニズムがあります。

* コンピューティング (仮想マシン): Azure HDInsight クラスター
* メタストア: Azure SQL データベース
* ストレージ:Azure Data Lake Gen2 または Blob ストレージ
* 認証: Azure Active Directory、Azure Active Directory Domain Services、Enterprise セキュリティ パッケージ
* ドメイン名の解決: Azure DNS

Azure Key Vault や Azure Data Factory など、使用できるオプションのサービスが他にもあります。

:::image type="content" source="media/hdinsight-business-continuity/hdinsight-components.png" alt-text="HDInsight のコンポーネント":::

### <a name="azure-hdinsight-cluster-compute"></a>Azure HDInsight クラスター (コンピューティング)

HDInsight によって 99.9% の可用性 SLA が提供されます。 単一のデプロイで高可用性を実現するために、HDInsight には、既定で高可用性モードになっている多くのサービスが付属しています。 HDInsight のフォールト トレランス メカニズムは、Microsoft と Apache OSS エコシステムの両方の高可用性サービスによって提供されます。

次のサービスは、高可用性を実現するように設計されています。

#### <a name="infrastructure"></a>インフラストラクチャ

* アクティブおよびスタンバイ ヘッドノード
* 複数のゲートウェイ ノード
* 3 つの Zookeeper Quorum ノード
* 障害と更新の各ドメイン別に分散されたワーカー ノード

#### <a name="service"></a>サービス

* Apache Ambari Server
* YARN 用アプリケーション タイムライン サーバー
* Hadoop MapReduce 用ジョブ履歴サーバー
* Apache Livy
* HDFS
* YARN Resource Manager
* HBase Master

詳細については、[Azure HDInsight でサポートされている高可用性サービス](./hdinsight-high-availability-components.md)に関するドキュメントを参照してください。

ビジネス機能に影響を与えるのは、必ずしも致命的なイベントとは限りません。 1 つのリージョンにある次の 1 つ以上のサービスでのサービス インシデントによって、想定されているビジネス機能が失われる可能性もあります。

### <a name="hdinsight-metastore"></a>HDInsight のメタストア

HDInsight には、99.99% の SLA を提供するメタストアとして [Azure SQL Database](https://azure.microsoft.com/support/legal/sla/sql-database/v1_4/) が使用されています。 同期レプリケーションを使用して、1 つのデータ センター内にデータのレプリカが 3 つ保持されます。 1 つのレプリカが失われた場合、代替レプリカがシームレスに提供されます。 [アクティブ geo レプリケーション](../azure-sql/database/active-geo-replication-overview.md)は、最大 4 つのデータ センターで、すぐに使用できます。 手動またはデータ センターのいずれかのフェールオーバーが発生すると、階層内の最初のレプリカが自動的に読み取り書き込み可能になります。 詳細については、[Azure SQL Database によるビジネス継続性](../azure-sql/database/business-continuity-high-availability-disaster-recover-hadr-overview.md)に関する記事を参照してください。

### <a name="hdinsight-storage"></a>HDInsight ストレージ

HDInsight の基になるストレージ層として推奨されるのは、Azure Data Lake Storage Gen2 です。 Azure Data Lake Storage Gen2 を含め、[Azure Storage](https://azure.microsoft.com/support/legal/sla/storage/v1_5/) によって 99.9% の SLA が提供されます。 HDInsight には、1 つのデータ センター内にデータのレプリカが 3 つ保持される LRS サービスが使用され、レプリケーションは同期的に行われます。 1 つのレプリカが失われた場合、1 つのレプリカがシームレスに提供されます。

### <a name="azure-active-directory"></a>Azure Active Directory

[Azure Active Directory](https://azure.microsoft.com/support/legal/sla/active-directory/v1_0/) によって 99.9% の SLA が提供されます。 Active Directory は、複数レベルの内部冗長性と自動回復性を備えたグローバル サービスです。 詳細については、どのように [Microsoft が Azure Active Directory の信頼性を継続的に向上させているか](https://azure.microsoft.com/blog/advancing-azure-active-directory-availability/)をご覧ください。

### <a name="azure-active-directory-domain-services-ad-ds"></a>Azure Active Directory Domain Services (AD DS)

[Azure Active Directory Domain Services](https://azure.microsoft.com/support/legal/sla/active-directory-ds/v1_0/) によって、99.9% の SLA が提供されます。 Azure AD DS は、グローバルに分散されたデータ センターでホストされる可用性の高いサービスです。 レプリカ セットは Azure AD DS のプレビュー機能であり、Azure リージョンがオフラインになった場合に、地理的なディザスター リカバリーを実現します。 詳細については、「[Azure Active Directory Domain Services のレプリカ セットの概念と機能](../active-directory-domain-services/concepts-replica-sets.md)」を参照してください。  

### <a name="azure-dns"></a>Azure DNS

[Azure DNS](https://azure.microsoft.com/support/legal/sla/dns/v1_1/) によって 100% の SLA が提供されます。 HDInsight には、ドメイン名を解決するためにさまざまな場所で Azure DNS が使用されています。

## <a name="multi-region-cost-and-complexity-optimizations"></a>複数リージョンのコストと複雑さの最適化

複数リージョンにまたがる高可用性ディザスター リカバリーを使用してビジネス継続性を向上させるには、さらに複雑でコストの高いアーキテクチャ設計が必要とされます。 次の表に、総保有コストが増える可能性のあるいくつかの技術的な領域について詳しく説明します。

### <a name="cost-optimizations"></a>コストの最適化

|領域|コスト上昇の原因|最適化の戦略|
|----|------------------------|-----------------------|
|データ ストレージ|セカンダリ リージョンでのプライマリ データおよびテーブルの複製|選別されたデータのみをレプリケートします|
|データ エグレス|複数リージョンにまたがる送信データ転送は高くつきます。 帯域幅の料金ガイドラインを確認してください|選別されたデータのみをレプリケートしてリージョンのエグレス フットプリントを削減します|
|クラスター コンピューティング|セカンダリ リージョンでの追加の HDInsight クラスター|プライマリの障害後にセカンダリ コンピューティングをデプロイするために、自動スクリプトを使用します。 セカンダリ クラスターのサイズを最小限に抑えるために、自動スケーリングを使用します。 低コストの VM SKU を使用します。 VM SKU が割引される可能性のあるリージョンにセカンダリを作成します。|
|認証 |セカンダリ リージョンのマルチユーザー シナリオの場合、追加の Azure AD DS セットアップが発生します|セカンダリ リージョンでのマルチユーザー設定は避けてください。|

### <a name="complexity-optimizations"></a>複雑さの最適化

|領域|複雑さの上昇の原因|最適化の戦略|
|----|------------------------|-----------------------|
|読み取り書き込みのパターン |プライマリとセカンダリの両方で読み取りと書き込みを有効にする必要がある |セカンダリを読み取り専用になるように設計します|
|ゼロ RPO および RTO |データ損失ゼロ (RPO = 0) とダウンタイム ゼロ (RTO = 0) にする必要がある |フェールオーバーする必要があるコンポーネントの数が減るように RPO と RTO を設計します。|
|ビジネス機能 |セカンダリでプライマリの完全なビジネス機能が必要 |セカンダリのビジネス機能の最小限の重要なサブセットを使用して実行できるかどうかを評価します。|
|接続 |すべてのアップストリームおよびダウンストリーム システムがプライマリからセカンダリにも接続する必要がある|セカンダリ接続を最小限の重要なサブセットに制限します。|

## <a name="next-steps"></a>次のステップ

この記事で説明した項目の詳細については、次を参照してください。

* [Azure HDInsight のビジネス継続性アーキテクチャ](./hdinsight-business-continuity-architecture.md)
* [Azure HDInsight の高可用性ソリューション アーキテクチャのケース スタディ](./hdinsight-high-availability-case-study.md)
* [Azure HDInsight における Apache Hive と HiveQL](./hadoop/hdinsight-use-hive.md)