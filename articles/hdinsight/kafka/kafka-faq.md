---
title: Azure HDInsight での Apache Kafka に関する FAQ
description: 管理された Hadoop クラウド サービスである Azure HDInsight での Apache Kafka についての一般的な質問の回答を表示します。
ms.service: hdinsight
ms.topic: conceptual
ms.date: 08/14/2019
ms.openlocfilehash: 851d7ceb44d2466ed31e26c1442fde9acea9fd22
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "98939104"
---
# <a name="frequently-asked-questions-about-apache-kafka-in-azure-hdinsight"></a>Azure HDInsight での Apache Kafka に関してよく寄せられる質問

この記事では、Azure HDInsight での Apache Kafka の使用についての一般的な質問にお応えします。

## <a name="what-kafka-versions-are-supported-by-hdinsight"></a>Kafka のどのバージョンが HDInsight でサポートされていますか?

HDInsight で公式にサポートされているコンポーネントのバージョンに関する詳細情報については、「[HDInsight で使用可能な Apache Hadoop コンポーネントおよびバージョンとは](../hdinsight-component-versioning.md#supported-hdinsight-versions)」を参照してください。 最適なパフォーマンスとユーザー エクスペリエンスを確保するには、常に最新バージョンを使用することをお勧めします。

## <a name="what-resources-are-provided-in-an-hdinsight-kafka-cluster-and-what-resources-am-i-charged-for"></a>HDInsight Kafka クラスターにはどのようなリソースが用意されており、どのようなリソースが課金されますか?

HDInsight Kafka クラスターには、次のリソースが含まれています。

* ヘッド ノード
* Zookeeper ノード
* ブローカー (worker) ノード 
* ブローカー ノードに接続されている Azure Managed Disks
* ゲートウェイ ノード

ゲートウェイ ノードを除き、これらのリソースはすべて、[HDInsight の価格モデル](https://azure.microsoft.com/pricing/details/hdinsight/)に基づいて課金されます。 ゲートウェイ ノードに対する課金はありません。

さまざまなノードの種類の詳細については、「[Azure HDInsight 仮想ネットワーク アーキテクチャ](../hdinsight-virtual-network-architecture.md)」を参照してください。 価格は 1 分あたりのノード使用量に基づいています。 価格は、ノードのサイズ、ノードの数、使用されているマネージド ディスクの種類、リージョンによって異なります。

## <a name="do-apache-kafka-apis-work-with-hdinsight"></a>Apache Kafka API は HDInsight と連携しますか?

はい。HDInsight ではネイティブ Kafka API を使用します。 クライアント アプリケーションのコードを変更する必要はありません。 「[チュートリアル:Apache Kafka Producer および Consumer API の使用](./apache-kafka-producer-consumer-api.md)」を参照して、クラスターで Java ベースの Producer API と Consumer API を使用する方法を確認します。

## <a name="can-i-change-cluster-configurations"></a>クラスター構成を変更できますか?

はい、Ambari ポータルを使用します。 ポータルの各コンポーネントには、**構成** セクションがあり、コンポーネントの構成を変更するために使用できます。 一部の変更では、ブローカーの再起動が必要な場合があります。

## <a name="what-type-of-authentication-does-hdinsight-support-for-apache-kafka"></a>HDInsight で Apache Kafka に対してサポートされる認証の種類はどれですか?

[Enterprise セキュリティ パッケージ (ESP)](../domain-joined/apache-domain-joined-architecture.md) を使用すると、Kafka クラスター用のトピックレベルのセキュリティを取得できます。 「[チュートリアル:Enterprise セキュリティ パッケージを使用して HDInsight に Apache Kafka ポリシーを構成する (プレビュー)](../domain-joined/apache-domain-joined-run-kafka.md)」を参照してください。

## <a name="is-my-data-encrypted-can-i-use-my-own-keys"></a>データは暗号化されますか? 独自のキーを使用できますか?

マネージド ディスク上の Kafka メッセージはすべて、[Azure Storage Service Encryption (SSE)](../../storage/common/storage-service-encryption.md) を使用して暗号化されます。 既定では、転送中のデータ (たとえば、クライアントからブローカーに送信されるデータとその反対) は、暗号化されません。 [独自の TLS を設定する](./apache-kafka-ssl-encryption-authentication.md)ことによって、このようなトラフィックを暗号化できます。 さらに、HDInsight では、保存データを暗号化するために、独自のキーを管理することができます。 詳細については、「[お客様が管理するキー ディスクの暗号化](../disk-encryption.md)」を参照してください。

## <a name="how-do-i-connect-clients-to-my-cluster"></a>クライアントを自分のクラスターに接続する方法

Kafka クライアントで Kafka ブローカーと通信するには、ネットワーク経由でブローカーに到達できる必要があります。 HDInsight クラスターでは、Virtual Network (VNet) がセキュリティの境界です。 このため、クライアントをご利用の HDInsight クラスターに接続する最も簡単な方法は、クラスターと同じ VNet 内にクライアントを作成することです。 その他のシナリオには、次のようなものがあります。

* 別の Azure VNet のクライアントに接続する – クラスター VNet とクライアント VNet をピアリングし、[IP アドバタイズ](apache-kafka-connect-vpn-gateway.md#configure-kafka-for-ip-advertising)用にクラスターを構成します。 IP アドバタイズを使用する場合、Kafka クライアントは、完全修飾ドメイン名 (FQDN) ではなく、ブローカー IP アドレスを使用してブローカーに接続する必要があります。

* オンプレミスのクライアントに接続する – 「[Azure HDInsight 用の仮想ネットワークを計画する](../hdinsight-plan-virtual-network-deployment.md)」に説明されているように、VPN ネットワークを使用して、カスタム DNS サーバーを設定します。

* Kafka サービスのパブリック エンドポイントを作成する – エンタープライズ セキュリティ要件で許可されている場合は、Kafka ブローカー用のパブリック エンドポイント、またはパブリック エンドポイントを持つ自己管理型のオープンソースの REST エンドポイントをデプロイできます。

## <a name="can-i-add-more-disk-space-on-an-existing-cluster"></a>既存のクラスターに対してディスク領域をさらに追加することはできますか?

Kafka メッセージで使用できる領域を増やすには、ノードの数を増やすことができます。 現在、既存のクラスターに対してディスクをさらに追加することは、サポートされていません。

## <a name="can-a-kafka-cluster-work-with-databricks"></a>Kafka クラスターを Databricks で動作させることはできますか? 

はい。 Kafka クラスターは、同じ VNet 内にある限り、Databricks で動作できます。 Databricks で Kafka クラスターを使用するには、HDInsight Kafka クラスターが含まれる VNet を作成し、Databricks ワークスペースを作成して VNet インジェクションを使用するときにその VNet を指定します。 詳細については、「[Azure Virtual Network に Azure Databricks をデプロイする (VNet インジェクション)](/azure/databricks/administration-guide/cloud-configurations/azure/vnet-inject)」を参照してください。 Databricks ワークスペースの作成時に、Kafka クラスターのブートストラップ ブローカー名を指定する必要があります。 Kafka ブローカー名の取得方法の詳細については、「[Apache Zookeeper およびブローカーのホスト情報を取得する](./apache-kafka-get-started.md#getkafkainfo)」を参照してください。

## <a name="how-can-i-have-maximum-data-durability"></a>最大限のデータの持続性を達成するにはどうすればよいですか?

データの持続性を使用すると、メッセージが失われるリスクを最小限に抑えることができます。 最大限のデータの持続性を達成するために、次の設定をお勧めします。

* ほとんどのリージョンで最小レプリケーション係数の 3 を使用する
* 障害ドメインが 2 つだけのリージョンで最小レプリケーション係数の 4 を使用する
* 不完全なリーダーの選択を無効にする
* **min.insync.replicas** を 2 以上に設定する - これにより、書き込みを続行する前に、リーダーと完全に同期する必要があるレプリカの数が変更されます
* **acks** プロパティを **all** に設定する - このプロパティでは、すべてのレプリカでメッセージをすべて確認する必要があります

データの一貫性を高めるために Kafka を構成すると、要求を生成するブローカーの可用性に影響します。

## <a name="can-i-replicate-my-data-to-multiple-clusters"></a>データを複数のクラスターにレプリケートすることはできますか?

はい。Kafka MirrorMaker を使用して複数のクラスターにデータをレプリケートできます。 詳細については、[Mirror Apache Kafka のトピック](apache-kafka-mirroring.md)で見つけることができる MirrorMaker の設定を参照してください。 さらに、その他の自己管理型のオープンソース テクノロジとベンダーがあり、[Brooklin](https://github.com/linkedin/Brooklin/) などの複数のクラスターへのレプリケーションを実現するのに役立ちます。

## <a name="can-i-upgrade-my-cluster-how-should-i-upgrade-my-cluster"></a>クラスターをアップグレードできますか? クラスターをアップグレードするにはどうすればよいですか?

現在、インプレース クラスターのバージョンのアップグレードはサポートされていません。 ご利用のクラスターを新しいバージョンの Kafka に更新するには、必要なバージョンで新しいクラスターを作成し、Kafka クライアントを移行して新しいクラスターを使用します。

## <a name="how-do-i-monitor-my-kafka-cluster"></a>自分の Kafka クラスターを監視するにはどうしたらよいですか?

Azure Monitor を使用して [Kafka ログ](./apache-kafka-log-analytics-operations-management.md)を分析します。

## <a name="next-steps"></a>次のステップ

* [Azure HDInsight の Apache Kafka 用に TLS 暗号化および認証を設定する](./apache-kafka-ssl-encryption-authentication.md)
* [MirrorMaker を使用して HDInsight 上の Kafka に Apache Kafka トピックをレプリケートする](./apache-kafka-mirroring.md)