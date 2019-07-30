---
title: オンプレミスの Apache Hadoop クラスターの Azure HDInsight への移行 - インフラストラクチャのベスト プラクティス
description: オンプレミスの Apache Hadoop クラスターを Azure HDInsight に移行することについてのインフラストラクチャのベスト プラクティスについて説明します。
author: hrasheed-msft
ms.reviewer: jasonwhowell
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 04/05/2019
ms.author: hrasheed
ms.openlocfilehash: 0707f08d7c1447ff9aaae919cabfe1a668b25e3d
ms.sourcegitcommit: 9dc7517db9c5817a3acd52d789547f2e3efff848
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/23/2019
ms.locfileid: "68404375"
---
# <a name="migrate-on-premises-apache-hadoop-clusters-to-azure-hdinsight---infrastructure-best-practices"></a>オンプレミスの Apache Hadoop クラスターの Azure HDInsight への移行 - インフラストラクチャのベスト プラクティス

この記事では、Azure HDInsight クラスターのインフラストラクチャを管理するための推奨事項について説明します。 これは、オンプレミスの Apache Hadoop システムを Azure HDInsight に移行する際に役立つベスト プラクティスを紹介するシリーズの一部です。

## <a name="plan-for-hdinsight-cluster-capacity"></a>HDInsight クラスターの容量計画

HDInsight クラスターの容量計画で行うべき重要な選択内容は次のとおりです。

- **リージョンを選択する**: Azure リージョンによって、クラスターを物理的にプロビジョニングする場所が決まります。 読み取りと書き込みの待機時間を最小限に抑えるには、クラスターをデータと同じリージョンに配置する必要があります。
- **ストレージの場所とサイズを選択する**: 既定のストレージはクラスターと同じリージョンにある必要があります。 48 ノードのクラスターの場合は、4 つから 8 つのストレージ アカウントを持つことをお勧めします。 既に十分な数のストレージが存在する場合もありますが、各ストレージ アカウントはコンピューティング ノードに追加のネットワーク帯域幅を提供します。 複数のストレージ アカウントがある場合は、各ストレージ アカウントにプレフィックスなしのランダムな名前を使用します。 ランダムな名前付けの目的は、すべてのアカウント間でストレージのボトルネック (調整) や共通モード エラーが発生する可能性を低減することです。 パフォーマンスを向上させるために、ストレージ アカウントごとにコンテナーを 1 つだけ使用します。
- **VM のサイズと種類を選択する (現在は G シリーズをサポート)** :各クラスターの種類には一連のノード タイプがあり、各ノード タイプには VM のサイズと種類の固有のオプションがあります。 VM のサイズと種類は、CPU の処理能力、RAM サイズ、ネットワーク待ち時間によって決まります。 シミュレートされたワークロードを使用して、ノードの種類ごとに最適な VM サイズと種類を決定することができます。
- **ワーカー ノードの数を選択する**: シミュレートされたワークロードを使用して、ワーカー ノードの最初の数を決定できます。 ピーク時の負荷要求を満たすようにワーカー ノードを追加することによって、クラスターを後でスケールアップできます。 追加のワーカー ノードが不要な場合、クラスターは後で元のスケールに戻すことができます。

詳細については、「[HDInsight クラスターの容量計画](../hdinsight-capacity-planning.md)」の記事を参照してください。

## <a name="use-recommended-virtual-machine-type-for-cluster"></a>推奨される仮想マシンの種類をクラスターに使用する

HDInsight クラスターの種類ごとに推奨される仮想マシンの種類については、[クラスターの既定のノード構成と仮想マシン サイズ](../hdinsight-component-versioning.md#default-node-configuration-and-virtual-machine-sizes-for-clusters)を参照してください。

## <a name="check-hadoop-components-availability-in-hdinsight"></a>HDInsight での Hadoop コンポーネントの可用性を確認する

HDInsight の各バージョンは、一連の Hadoop エコシステム コンポーネントのクラウド ディストリビューションです。 HDInsight のすべてのコンポーネントとその現在のバージョンの詳細については、[HDInsight のコンポーネントのバージョン管理](../hdinsight-component-versioning.md)を参照してください。

また、Apache Ambari UI または Ambari REST API を使用して、HDInsight での Hadoop コンポーネントとバージョンを確認することもできます。

オンプレミス クラスター上で使用していたが、HDInsight クラスターの一部ではないアプリケーションまたはコンポーネントは、エッジ ノード上または HDInsight クラスターと同じ VNet 内の VM 上に追加できます。 Azure HDInsight では利用できないサード パーティ製 Hadoop アプリケーションは、HDInsight クラスターの「アプリケーション」オプションを使用してインストールできます。 カスタム Hadoop アプリケーションは、「スクリプト アクション」を使用して HDInsight クラスターにインストールできます。 次の表に、いくつかの一般的なアプリケーションと、それらの HDInsight との統合オプションを示します。

|**Application**|**統合**
|---|---|
|エアフロー|IaaS または HDInsight のエッジ ノード
|Alluxio|IaaS  
|Arcadia|IaaS 
|Atlas|なし (HDP のみ)
|Datameer|HDInsight のエッジ ノード
|Datastax (Cassandra)|IaaS (Azure での代替の CosmosDB)
|DataTorrent|IaaS 
|Drill|IaaS 
|Ignite|IaaS
|Jethro|IaaS 
|Mapador|IaaS 
|Mongo|IaaS (Azure での代替の CosmosDB)
|NiFi|IaaS 
|Presto|IaaS または HDInsight のエッジ ノード
|Python 2|PaaS 
|Python 3|PaaS 
|R|PaaS 
|SAS|IaaS 
|Vertica|IaaS (Azure での代替の SQLDW)
|Tableau|IaaS 
|Waterline|HDInsight のエッジ ノード
|StreamSets|HDInsight のエッジ 
|Palantir|IaaS 
|Sailpoint|IaaS 

詳細については、[HDInsight の各バージョンで使用できる Apache Hadoop コンポーネント](../hdinsight-component-versioning.md#apache-hadoop-components-available-with-different-hdinsight-versions)を参照してください

## <a name="customize-hdinsight-clusters-using-script-actions"></a>スクリプト アクションを使って HDInsight をカスタマイズする

HDInsight には、**スクリプト アクション**と呼ばれるクラスター構成のメソッドが提供されています。 スクリプト アクションは HDInsight クラスターのノードで実行される Bash スクリプトで、追加コンポーネントのインストールおよび構成設定を変更するために使用できます。

スクリプト アクションは、HDInsight クラスターからアクセスできる URI に保存されている必要があります。 これらはクラスターの作成中または作成後に使用でき、特定のノード型でのみ実行するように制限することもできます。

スクリプトは保存済みスクリプトにしたり、 1 回実行させたりすることができます。 保存済みスクリプトは、スケーリング操作でクラスターに追加される新しいワーカー ノードをカスタマイズするために使われます。 スケーリング操作が発生したとき、保存済みスクリプトによって、ヘッド ノードなどの別のタイプのノードに変更が適用されることもあります。

HDInsight は、HDInsight クラスターで、次のコンポーネントをインストールするためのあらかじめ作成されているスクリプトを提供します。

- Azure Storage アカウントの追加
- Hue のインストール
- Presto のインストール
- Solr のインストール
- Giraph のインストール
- Hive ライブラリの事前読み込み
- Mono のインストールまたは更新

> [!Note]  
> HDInsight には、カスタム Hadoop コンポーネントまたはスクリプト アクションを使用してインストールされるコンポーネントのための直接サポートが提供されていません。

スクリプト アクションは、HDInsight アプリケーションとして Azure Marketplace にも発行することができます。

詳細については、次の記事を参照してください。

- [HDInsight にサードパーティ製 Apache Hadoop アプリケーションをインストールする](../hdinsight-apps-install-applications.md)
- [スクリプト アクションを使って HDInsight をカスタマイズする](../hdinsight-hadoop-customize-cluster-linux.md)
- [Azure Marketplace への HDInsight アプリケーションの発行](../hdinsight-apps-publish-applications.md)

## <a name="customize-hdinsight-configs-using-bootstrap"></a>ブートストラップを使って HDInsight 構成をカスタマイズする

`core-site.xml`、`hive-site.xml`、`oozie-env.xml` などの構成ファイルの構成の変更は、ブートス トラップを使って実行できます。 次のスクリプトは、Powershell の [AZ モジュール](https://docs.microsoft.com/powershell/azure/new-azureps-module-az) コマンドレットである [New-AzHDInsightClusterConfig](https://docs.microsoft.com/powershell/module/az.hdinsight/new-azhdinsightcluster) を使用している例です。

```powershell
# hive-site.xml configuration
$hiveConfigValues = @{"hive.metastore.client.socket.timeout"="90"}

$config = New—AzHDInsightClusterConfig '
    | Set—AzHDInsightDefaultStorage
    —StorageAccountName "$defaultStorageAccountName.blob. core.windows.net" `
    —StorageAccountKey "defaultStorageAccountKey " `
    | Add—AzHDInsightConfigValues `
        —HiveSite $hiveConfigValues

New—AzHDInsightCluster `
    —ResourceGroupName $existingResourceGroupName `
    —Cluster-Name $clusterName `
    —Location $location `
    —ClusterSizeInNodes $clusterSizeInNodes `
    —ClusterType Hadoop `
    —OSType Linux `
    —Version "3.6" `
    —HttpCredential $httpCredential `
    —Config $config
```

詳細については、「[ブートストラップを使って HDInsight クラスターをカスタマイズする](../hdinsight-hadoop-customize-cluster-bootstrap.md)」の記事を参照してください。  「[Apache Ambari REST API を使用した HDInsight クラスターの管理](../hdinsight-hadoop-manage-ambari-rest-api.md)」もご覧ください。

## <a name="access-client-tools-from-hdinsight-hadoop-cluster-edge-nodes"></a>HDInsight Hadoop クラスターのエッジ ノードからクライアント ツールにアクセスする

空のエッジ ノードは、ヘッド ノードの場合と同じクライアント ツールがインストールされ、構成された Linux 仮想マシンですが、Hadoop サービスは実行されていません。 エッジ ノードは次の目的で使用できます。

- クラスターへのアクセス
- クライアント アプリケーションのテスト
- クライアント アプリケーションのホスト

エッジ ノードは Azure portal から作成および削除でき、クラスターの作成中またはクラスターの作成後に使用できます。 エッジ ノードを作成した後、SSH を使用してエッジ ノードに接続し、クライアント ツールを実行して HDInsight の Hadoop クラスターにアクセスすることができます。 エッジ ノードの SSH エンドポイントは `<EdgeNodeName>.<ClusterName>-ssh.azurehdinsight.net:22` です。


詳細については、「[HDInsight の Apache Hadoop クラスターで空のエッジ ノードを使用する](../hdinsight-apps-use-edge-node.md)」の記事を参照してください。


## <a name="use-scale-up-and-scale-down-feature-of-clusters"></a>クラスターのスケールアップおよびスケールダウン機能を使用する

HDInsight では、クラスター内のワーカー ノードの数をスケールアップおよびスケールダウンできるようにすることで、柔軟性が提供されます。 この機能により、クラスターを数時間後または週末に縮小したり、ビジネスの需要のピーク時に拡張したりできます。 詳細については、次を参照してください。

* [HDInsight クラスターのスケーリング](../hdinsight-scaling-best-practices.md)。
* [クラスターのスケーリング](../hdinsight-administer-use-portal-linux.md#scale-clusters)。

## <a name="use-hdinsight-with-azure-virtual-network"></a>Azure Virtual Network で HDInsight を使用する

Azure Virtual Network では、Azure Virtual Machine などの Azure リソースが、ネットワーク トラフィックをフィルタリングおよびルーティングすることによって、他の Azure リソース、インターネット、およびオンプレミスのネットワークと安全に通信することができます。

HDInsight で Azure Virtual Network を使用すると、次のシナリオが可能になります。

- オンプレミス ネットワークから HDInsight へ直接接続する。
- HDInsight を Azure Virtual Network 内のデータ ストアに接続する。
- インターネット公開されていない Hadoop サービスに直接アクセスする。 たとえば、Kafka Api や HBase Java API にアクセスできます。

HDInsight は、新規または既存の Azure Virtual Network に追加できます。 HDInsight を既存の仮想ネットワークに追加する場合は、既存のネットワーク セキュリティ グループとユーザー定義のルートを更新して、Azure データ センター内の[いくつかの IP アドレス](../hdinsight-management-ip-addresses.md)に無制限にアクセスできるようにする必要があります。 また、HDInsight サービスによって使用中の[ポート](../hdinsight-plan-virtual-network-deployment.md#hdinsight-ports)へのトラフィックをブロックしないようにしてください。

> [!Note]  
> HDInsight は強制トンネリングを現在サポートしていません。 強制トンネリングとは、ログ記録と検査を目的として、送信インターネット トラフィックをデバイスに強制的に向かわせるサブネット設定です。 HDInsight をサブネットにインストールする前に強制トンネリングを削除するか、HDInsight 用の新しいサブネットを作成してください。 また、HDInsight は発信ネットワーク接続を制限できません。

詳細については、次の記事を参照してください。

- [Azure 仮想ネットワークの概要](../../virtual-network/virtual-networks-overview.md)
- [Azure Virtual Network を使用した Azure HDInsight の拡張](../hdinsight-plan-virtual-network-deployment.md)

## <a name="securely-connect-to-azure-services-with-azure-virtual-network-service-endpoints"></a>Azure 仮想ネットワーク サービス エンドポイントを使用して Azure サービスに安全に接続する

HDInsight では、Azure Blob Storage、Azure Data Lake Storage Gen2、Cosmos DB、および SQL データベースに安全に接続できる[仮想ネットワーク サービス エンドポイント](../../virtual-network/virtual-network-service-endpoints-overview.md)をサポートします。 Azure HDInsight のサービス エンドポイントを有効にすると、トラフィックは Azure データ センター内からセキュリティで保護されたルートを経由します。 ネットワーク レイヤーでセキュリティをこのレベルに高めることにより、ビッグ データのストレージ アカウントを指定された仮想ネットワーク (Vnet) にロックし、HDInsight クラスターをシームレスに使用してデータにアクセスして処理することができます。

詳細については、次の記事を参照してください。

- [仮想ネットワーク サービス エンドポイント](../../virtual-network/virtual-network-service-endpoints-overview.md)
- [サービス エンドポイントによる HDInsight のセキュリティの強化](https://azure.microsoft.com/blog/enhance-hdinsight-security-with-service-endpoints/)

## <a name="connect-hdinsight-to-the-on-premises-network"></a>オンプレミス ネットワークへの HDInsight の接続

Azure Virtual Network と VPN Gateway を使用して、HDInsight をオンプレミス ネットワークに接続することができます。 接続を確立するには次の手順を使用することができます。

- オンプレミス ネットワークに接続している Azure Virtual Network で HDInsight を使用する。
- 仮想ネットワークとオンプレミス ネットワークの間の DNS 名前解決を構成する。
- ネットワーク セキュリティ グループまたはユーザー定義のルート (UDR) を構成してネットワーク トラフィックを制御する。

詳細は、「[Connect HDInsight to your on-premises network](../connect-on-premises-network.md)」(オンプレミス ネットワークへの HDInsight の接続) を参照してください

## <a name="next-steps"></a>次の手順

このシリーズの次の記事をお読みください。

- [オンプレミスから Azure HDInsight Hadoop への移行のためのストレージのベスト プラクティス](apache-hadoop-on-premises-migration-best-practices-storage.md)
