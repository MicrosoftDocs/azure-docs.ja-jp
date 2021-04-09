---
title: Azure HDInsight の高可用性コンポーネント
description: HDInsight クラスターで使用されるさまざまな高可用性コンポーネントの概要。
ms.service: hdinsight
ms.topic: conceptual
ms.date: 10/07/2020
ms.openlocfilehash: 93d2317c85f93ce8a22f2d434fbc081a88265a74
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/23/2021
ms.locfileid: "104863719"
---
# <a name="high-availability-services-supported-by-azure-hdinsight"></a>Azure HDInsight でサポートされている高可用性サービス

分析コンポーネントに最適な可用性レベルを提供するために、HDInsight は、重要なサービスの高可用性 (HA) を保証するための独自のアーキテクチャを使用して開発されました。 このアーキテクチャの一部のコンポーネントは、自動フェールオーバーを提供するために Microsoft によって開発されました。 その他のコンポーネントは、特定のサービスをサポートするためにデプロイされる標準の Apache コンポーネントです。 この記事では、HDInsight の HA サービス モデルのアーキテクチャ、HDInsight で HA サービスのフェールオーバーをサポートする方法、および他のサービス中断から復旧するためのベストプラクティスについて説明します。

> [!NOTE]
> この記事には、Microsoft が使用しなくなった "*スレーブ*" という用語への言及が含まれています。 ソフトウェアからこの用語が削除された時点で、この記事から削除します。

## <a name="high-availability-infrastructure"></a>高可用性インフラストラクチャ

HDInsight では、次の 4 つのプライマリ サービスが、自動フェールオーバー機能を備えた高可用性となるように、カスタマイズされたインフラストラクチャが提供されます。

- Apache Ambari サーバー
- Apache YARN 用アプリケーション タイムライン サーバー
- Hadoop MapReduce 用ジョブ履歴サーバー
- Apache Livy

このインフラストラクチャは、いくつかのサービスとソフトウェア コンポーネントで構成され、その一部は Microsoft によって設計されています。 次のコンポーネントは、HDInsight プラットフォームに固有です。

- スレーブ フェールオーバー コントローラー
- マスター フェールオーバー コントローラー
- スレーブ高可用性サービス
- マスター高可用性サービス

:::image type="content" source="./media/hdinsight-high-availability-components/high-availability-architecture.png" alt-text="高可用性インフラストラクチャ" border="false":::

また、他の高可用性サービスもあり、これらはオープンソースの Apache 信頼性コンポーネントによりサポートされています。 次のコンポーネントも HDInsight クラスターに存在します。

- Hadoop ファイル システム (HDFS) の NameNode
- YARN ResourceManager
- HBase Master

以下のセクションでは、これらのサービスがどのように連携するかについて詳しく説明します。

## <a name="hdinsight-high-availability-services"></a>HDInsight の高可用性サービス

Microsoft では、HDInsight クラスターの次の表に示す 4 つの Apache サービスのサポートを提供しています。 Apache のコンポーネントでサポートされている高可用性サービスと区別するために、これらは "*HDInsight HA サービス*" と呼ばれます。

| サービス | クラスター ノード | クラスターの種類 | 目的 |
|---|---|---|---|
| Apache Ambari サーバー| アクティブなヘッドノード | All | クラスターを監視および管理します。|
| Apache YARN 用アプリケーション タイムライン サーバー | アクティブなヘッドノード | Kafka を除くすべて | クラスターで実行されている YARN ジョブに関するデバッグ情報を保持します。|
| Hadoop MapReduce 用ジョブ履歴サーバー | アクティブなヘッドノード | Kafka を除くすべて | MapReduce ジョブのデバッグ データを保持します。|
| Apache Livy | アクティブなヘッドノード | Spark | REST インターフェイスを介して Spark クラスターと簡単に対話できるようにします |

>[!Note]
> 現在、HDInsight Enterprise セキュリティ パッケージ (ESP) クラスターでは、Ambari サーバーの高可用性のみが提供されています。 アプリケーション タイムライン サーバー、ジョブ履歴サーバー、Livy はすべて headnode0 でのみ実行され、Ambari のフェールオーバー時に headnode1 にフェールオーバーされません。 アプリケーション タイムライン データベースも、Ambari SQL Server ではなく headnode0 上にあります。

### <a name="architecture"></a>アーキテクチャ

各 HDInsight クラスターには、アクティブ モードとスタンバイ モードそれぞれに 2 つのヘッドノードがあります。 HDInsight HA サービスはヘッドノードでのみ実行されます。 これらのサービスは、常にアクティブなヘッドノードで実行され、スタンバイ ヘッドノードで停止されメンテナンス モードにされる必要があります。

HA サービスの正しい状態を維持し、高速フェールオーバーを実現するために、HDInsight では Apache ZooKeeper を利用しています。これは分散型アプリケーションの調整サービスであり、アクティブなヘッドノードの選択を行います。 HDInsight では、いくつかのバックグラウンド Java プロセスもプロビジョニングされます。これにより、HDInsight HA サービスのフェールオーバー手順が調整されます。 これらのサービスは、マスター フェールオーバー コントローラー、スレーブ フェールオーバー コントローラー、つまり "*master-ha-service*"、および "*slave-ha-service*" です。

### <a name="apache-zookeeper"></a>Apache ZooKeeper

Apache ZooKeeper は、分散型アプリケーション用の高パフォーマンスの調整サービスです。 実稼働環境では、ZooKeeper は通常、レプリケート モードで実行されます。このモードでは、ZooKeeper サーバーのレプリケートされたグループがクォーラムを形成します。 各 HDInsight クラスターには、3 つの ZooKeeper サーバーがクォーラムを形成できるようにする 3 つの ZooKeeper ノードがあります。 HDInsight の 2 つの ZooKeeper クォーラムが互いに並列に実行されています。 一方のクォーラムによって、HDInsight HA サービスを実行するクラスター内のアクティブなヘッドノードが決定されます。 もう一方のクォーラムを使用して、Apache によって提供される HA サービスが調整されます。詳細については、後のセクションで説明します。

### <a name="slave-failover-controller"></a>スレーブ フェールオーバー コントローラー

スレーブ フェールオーバー コントローラーは、HDInsight クラスター内のすべてのノードで実行されます。 このコントローラーは、各ノードで Ambari エージェントと "*slave-ha-service*" を起動する役割を担います。 アクティブなヘッドノードに関して最初の ZooKeeper クォーラムが定期的に照会されます。 アクティブなヘッドノードとスタンバイ ヘッドノードが変更されると、スレーブ フェールオーバー コントローラーは次の処理を実行します。

1. ホスト構成ファイルを更新します。
1. Ambari エージェントを再起動します。

"*slave-ha-service*" は、スタンバイ ヘッドノード上の HDInsight HA サービス (Ambari サーバーを除く) を停止する役割を担います。

### <a name="master-failover-controller"></a>マスター フェールオーバー コントローラー

マスター フェールオーバー コントローラーは、両方のヘッドノードで実行されます。 どちらのマスター フェールオーバー コントローラーも、最初の ZooKeeper クォーラムと通信して、アクティブなヘッドノードとして実行されているヘッドノードを指名します。

たとえば、ヘッドノード 0 のマスター フェールオーバー コントローラーが選択される場合、次の変更が行われます。

1. ヘッドノード 0 がアクティブになります。
1. マスター フェールオーバー コントローラーにより、ヘッドノード 0 で Ambari サーバーと "*master-ha-service*" が起動されます。
1. その他のマスター フェールオーバー コントローラーにより、ヘッドノード 1 で Ambari サーバーと "*master-ha-service*" が停止されます。

master-ha-service は、アクティブなヘッドノードでのみ実行されます。HDInsight HA サービス (Ambari サーバーを除く) がスタンバイ ヘッドノードで停止され、アクティブなヘッドノードで起動されます。

### <a name="the-failover-process"></a>フェールオーバー プロセス

:::image type="content" source="./media/hdinsight-high-availability-components/failover-steps.png" alt-text="フェールオーバー プロセス" border="false":::

ヘルス モニターは、マスター フェールオーバー コントローラーと共に各ヘッドノードで実行され、ハートビート通知が ZooKeeper クォーラムに送信されます。 このシナリオでは、ヘッドノードは HA サービスと見なされます。 ヘルス モニターにより、各高可用性サービスが正常であるかどうか、およびリーダーシップの選択に参加する準備ができているかどうかが確認されます。 もしできているなら、このヘッドノードは選択に加えられます。 そうでない場合は、再度準備が整うまで選択を停止します。

スタンバイ ヘッドノードが選択されてアクティブになる (前のアクティブ ノードで障害が発生した場合など) と、そのマスター フェールオーバー コントローラーにより、その上のすべての HDInsight HA サービスが開始されます。 マスター フェールオーバー コントローラーにより、他のヘッドノードでこれらのサービスの停止も行われます。

サービスがダウンしている、異常な状態など、HDInsight HA サービスのエラーの場合、マスター フェールオーバー コントローラーでは、ヘッドノードの状態に応じてサービスを自動的に再起動または停止する必要があります。 ユーザーが、両方のヘッド ノードで HDInsight HA サービスを手動で起動することはできません。 代わりに、自動または手動のフェールオーバーを許可して、サービスを回復できるようにします。

### <a name="inadvertent-manual-intervention"></a>不注意による手動介入

HDInsight HA サービスは、アクティブなヘッドノードでのみ実行する必要があり、必要に応じて自動的に再起動されます。 個々の HA サービスには独自のヘルス モニターがないため、個々のサービスのレベルでフェールオーバーをトリガーすることはできません。 フェールオーバーは、サービス レベルではなく、ノード レベルで保証されます。

### <a name="some-known-issues"></a>既知の問題

- スタンバイ ヘッドノードで HA サービスを手動で開始すると、次のフェールオーバーが発生するまで停止しません。 HA サービスが両方のヘッドノードで実行されている場合に発生する可能性がある問題には次のものがあります: Ambari UI にアクセスできない、Ambari でエラーが発生する、YARN、Spark、Oozie のジョブが停止する可能性がある。

- アクティブなヘッドノードで HA サービスが停止すると、次のフェールオーバーが発生するか、マスター フェールオーバー コントローラー/master-ha-service が再開されるまで、再起動されません。 アクティブなヘッドノードで 1 つ以上の HA サービスが停止した場合、特に Ambari サーバーが停止した場合は、Ambari UI にアクセスできず、YARN、Spark、Oozie のジョブ エラーなど、他の問題が発生する可能性があります。

## <a name="apache-high-availability-services"></a>Apache の高可用性サービス

Apache では、HDFS NameNode、YARN ResourceManager、および HBase Master の高可用性を提供し、これは HDInsight クラスターでも使用できます。 HDInsight HA サービスとは異なり、これらは ESP クラスターでサポートされています。 Apache HA サービスでは、2 番目の ZooKeeper クォーラム (前のセクションで説明) と通信して、アクティブ/スタンバイ状態を選択し、自動フェールオーバーを実行します。 以下のセクションでは、これらのサービスのしくみについて詳しく説明します。

### <a name="hadoop-distributed-file-system-hdfs-namenode"></a>Hadoop 分散ファイル システム (HDFS) の NameNode

Apache Hadoop 2.0 以降に基づく HDInsight クラスターでは、NameNode 高可用性を提供します。 ヘッドノードで実行されている NameNode は 2 つあり、自動フェールオーバー用に構成されています。 NameNode では、"*ZKFailoverController*" を使用して、ZooKeeper と通信し、アクティブ/スタンバイ状態を選択します。 両方のヘッドノードで "*ZKFailoverController*" が実行され、上記のマスター フェールオーバー コントローラーと同じように動作します。

2 番目の ZooKeeper クォーラムは、最初のクォーラムから独立しているため、アクティブな NameNode が、アクティブなヘッドノードで実行されない可能性があります。 アクティブな NameNode が動作していない、または異常な状態にある場合は、スタンバイ NameNode が選択され、アクティブになります。

### <a name="yarn-resourcemanager"></a>YARN ResourceManager

Apache Hadoop 2.4 以降に基づく HDInsight クラスターでは、YARN ResourceManager の高可用性をサポートします。 rm1 と rm2 の 2 つの ResourceManager があり、それぞれヘッドノード 0 とヘッドノード 1 で実行されています。 NameNode と同様、YARN ResourceManager も自動フェールオーバー用に構成されています。 現在のアクティブな ResourceManager がダウンしたり応答しなくなったりすると、別の ResourceManager がアクティブになるように自動的に選択されます。

YARN ResourceManager では、その埋め込み "*ActiveStandbyElector*" がエラー検出とリーダー選択に使用されます。 HDFS NameNode とは異なり、YARN ResourceManager は個別の ZKFC デーモンを必要としません。 アクティブな ResourceManager により、その状態が Apache ZooKeeper に書き込まれます。

YARN ResourceManager の高可用性は、NameNode やその他の HDInsight HA サービスからは独立しています。 アクティブな ResourceManager がアクティブなヘッドノード、またはアクティブな NameNode が実行されているヘッドノードで実行されない可能性があります。 YARN ResourceManager の高可用性の詳細については、「[ResourceManager の高可用性](https://hadoop.apache.org/docs/current/hadoop-yarn/hadoop-yarn-site/ResourceManagerHA.html)」を参照してください。

### <a name="hbase-master"></a>HBase Master

HDInsight HBase クラスターでは、HBase Master 高可用性がサポートされます。 ヘッドノードで実行される他の HA サービスとは異なり、HBase Master は 3 つの ZooKeeper ノードで実行されます。そのうちの 1 つがアクティブなマスター、他の 2 つがスタンバイです。 NameNode と同様に、HBase Master では、リーダー選択のために Apache ZooKeeper との調整が行われ、現在のアクティブなマスターに問題がある場合は自動フェールオーバーが行われます。 アクティブな HBase Master は、常に 1 つだけ存在します。

## <a name="next-steps"></a>次のステップ

- [HDInsight における Apache Hadoop クラスターの可用性と信頼性](./hdinsight-business-continuity.md)
- [Azure HDInsight 仮想ネットワーク アーキテクチャ](hdinsight-virtual-network-architecture.md)