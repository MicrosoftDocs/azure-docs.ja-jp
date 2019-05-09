---
title: Azure HDInsight 仮想ネットワーク アーキテクチャ
description: Azure Virtual Network に HDInsight クラスターを作成するときに使用可能なリソースについて説明します。
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 03/26/2019
ms.author: hrasheed
ms.openlocfilehash: 6d92273298c0448d7377acab6f3b8ea1cc1ed908
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/18/2019
ms.locfileid: "58762924"
---
# <a name="azure-hdinsight-virtual-network-architecture"></a>Azure HDInsight 仮想ネットワーク アーキテクチャ

この記事では、HDInsight クラスターをカスタム Azure Virtual Network にデプロイするときに存在するリソースについて説明します。 この情報は、オンプレミスのリソースを Azure の HDInsight クラスターに接続するのに役立ちます。 Azure Virtual Network の詳細については、「[Azure Virtual Network とは](../virtual-network/virtual-networks-overview.md)」をご覧ください。

## <a name="resource-types-in-azure-hdinsight-clusters"></a>Azure HDInsight クラスターでのリソースの種類

Azure HDInsight クラスターには、さまざまな種類の仮想マシン、つまりノードがあります。 それぞれのノードの種類が、システムの運用において役割を果たしています。 次の表は、これらのノードの種類とクラスターでのそれぞれの役割をまとめたものです。

| Type | 説明 |
| --- | --- |
| ヘッド ノード |  Apache Storm を除くすべてのクラスターの種類で、ヘッド ノードは、分散アプリケーションの実行を管理するプロセスをホストします。 ヘッド ノードはまた、SSH 接続して、複数のクラスター リソースで実行するように調整されるアプリケーションを実行できるノードでもあります。 ヘッド ノードの数は、すべてのクラスターの種類で 2 台に固定されています。 |
| ZooKeeper ノード | Zookeeper は、データ処理を実行しているノード間でタスクを調整します。 また、ヘッド ノードのリーダー選定を行って、どのノードが特定のマスター サービスを実行しているかを追跡します。 ZooKeeper ノードの数は、2 台に固定されています。 |
| ワーカー ノード | データ処理機能をサポートするノードを表します。 ワーカー ノードをクラスターに追加したりクラスターから削除したりすることで、コンピューティング機能をスケーリングしてコストを管理することができます。 |
| R Server エッジ ノード | R Server エッジ ノードは、SSH 接続して、クラスター リソース全体で実行するように調整されるアプリケーションを実行できるノードを表します。 エッジ ノードは、クラスター内のデータ分析には参加しません。 このノードは R Studio Server もホストしているため、ブラウザーを使用して R アプリケーションを実行することができます。 |
| リージョン ノード | HBase のクラスターの種類では、リージョン ノード (データ ノードとも呼ばれる) はリージョン サーバーを実行します。 リージョン サーバーは、HBase によって管理されるデータの一部を提供および管理します。 リージョン ノードをクラスターに追加したりクラスターから削除したりすることで、コンピューティング機能をスケーリングしてコストを管理できます。|
| Nimbus ノード | Storm のクラスターの種類では、Nimbus ノードは、ヘッド ノードと同様の機能を提供します。 Nimbus ノードは、Storm トポロジの実行を調整する Zookeeper を介してクラスター内の他のノードにタスクを割り当てます。 |
| スーパーバイザー ノード | Storm のクラスターの種類では、スーパーバイザー ノードは、Nimbus ノードによって提供された命令を実行して、必要な処理を行います。 |

## <a name="basic-virtual-network-resources"></a>基本的な仮想ネットワーク リソース

次の図は、Azure での HDInsight ノードとネットワーク リソースの配置を示しています。

![Azure のカスタム VNET に作成された HDInsight のエンティティの図](./media/hdinsight-virtual-network-architecture/vnet-diagram.png)

HDInsight が Azure Virtual Network にデプロイされるときに存在する既定のリソースには、前の表で述べたクラスター ノードの種類、および仮想ネットワークと外部のネットワークとの間の通信をサポートするネットワーク デバイスが含まれます。

次の表は、HDInsight をカスタムの Azure Virtual Network にデプロイするときに作成される 9 台のクラスター ノードをまとめたものです。

| リソースの種類 | 存在する数 | 詳細 |
| --- | --- | --- |
|ヘッド ノード | two |    |
|Zookeeper ノード | three | |
|ワーカー ノード | two | この数は、クラスターの構成とスケーリングによって異なる場合があります。 Apache Kafka には、最小で 3 台のワーカー ノードが必要です。  |
|ゲートウェイ ノード | two | ゲートウェイ ノードは、Azure に作成される Azure Virtual Machine ですが、サブスクリプションには表示されません。 これらのノードを再起動する必要がある場合は、サポートにお問い合わせください。 |

次に提示するネットワーク リソースは、HDInsight で使用される仮想ネットワーク内に自動的に作成されます。

| ネットワーク リソース | 存在する数 | 詳細 |
| --- | --- | --- |
|Load Balancer | three | |
|ネットワーク インターフェイス | 9 | この値は、各ノードが独自のネットワーク インターフェイスを持つ、通常のクラスターに基づきます。 この 9 個のインターフェイスは、2 台のヘッド ノード、3 台の Zookeeper ノード、2 台のワーカー ノード、および前の表で述べられた 2 台のゲートウェイ ノード用です。 |
|パブリック IP アドレス | two |    |

## <a name="endpoints-for-connecting-to-hdinsight"></a>HDInsight に接続するためのエンドポイント

HDInsight クラスターには次の 3 つの方法でアクセスできます。

- 仮想ネットワークの外の HTTP エンドポイント `CLUSTERNAME.azurehdinsight.net`。
- ヘッド ノードに直接接続するための SSH エンドポイント `CLUSTERNAME-ssh.azurehdinsight.net`。
- 仮想ネットワーク内の HTTP エンドポイント `CLUSTERNAME-int.azurehdinsight.net`。 この URL に含まれている "-int" に注目してください。 このエンドポイントは、その仮想ネットワーク内のプライベート IP に解決され、パブリック インターネットからはアクセスできません。

これら 3 つのエンドポイントにはそれぞれロード バランサーが割り当てられています。

仮想ネットワーク外部からの接続を許可する 2 つのエンドポイントにはパブリック IP アドレスも提供されています。

1. 1 つのパブリック IP は、インターネットからクラスターに接続するときに使用する完全修飾ドメイン名 (FQDN) のために、ロード バランサーに割り当てられています `CLUSTERNAME.azurehdinsight.net`。
1. 2 つ目のパブリック IP アドレスは、SSH 専用のドメイン名に使用されます `CLUSTERNAME-ssh.azurehdinsight.net`。

## <a name="next-steps"></a>次の手順

* [Secure incoming traffic to HDInsight clusters in a virtual network with private endpoint](https://azure.microsoft.com/blog/secure-incoming-traffic-to-hdinsight-clusters-in-a-vnet-with-private-endpoint/) (プライベート エンドポイントを使用した、仮想ネットワーク内の HDInsight クラスターへのセキュリティで保護された着信トラフィック)
