---
title: Hadoop の高可用性 - Azure HDInsight
description: HDInsight クラスターで、追加のヘッド ノードを使用することで、信頼性と可用性を改善する方法について説明します。 その結果が Ambari や Hive などの Hadoop サービスに与える影響、SSH を使用して各ヘッド ノードに個別に接続する方法についても説明します。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
keywords: Hadoop の高可用性
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.topic: conceptual
ms.date: 10/28/2019
ms.openlocfilehash: f97b4877d87acec923812fd0289d5161529dbe7a
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/23/2020
ms.locfileid: "87081034"
---
# <a name="availability-and-reliability-of-apache-hadoop-clusters-in-hdinsight"></a>HDInsight における Apache Hadoop クラスターの可用性と信頼性

HDInsight クラスターは 2 つのヘッド ノードを備え、実行中の Apache Hadoop サービスおよびジョブの可用性と信頼性を高めています。

Hadoop は、クラスター内の複数のノードにわたってサービスやデータを複製することにより、高い可用性と信頼性を実現します。 ただし、Hadoop の標準ディストリビューションに含まれるヘッド ノードは、通常 1 つのみです。 この 1 つのヘッド ノードで障害が発生すると、クラスターが動作を停止する可能性があります。 Hadoop の可用性と信頼性を向上させるために、HDInsight では 2 つのヘッド ノードが提供されます。

## <a name="availability-and-reliability-of-nodes"></a>ノードの高可用性と信頼性

HDInsight クラスターのノードは、Azure Virtual Machines を使用して実装します。 次のセクションでは、HDInsight で使用される個々のノード タイプについて説明します。

> [!NOTE]  
> クラスターの種類によっては、一部のノード タイプのみが使用されます。 たとえば、Hadoop クラスターには、Nimbus ノードはありません。 HDInsight クラスターの各種類で使用されるノードの詳細については、[HDInsight での Linux ベースの Hadoop クラスターの作成](hdinsight-hadoop-provision-linux-clusters.md#cluster-type)に関するドキュメントの「クラスターの種類」セクションを参照してください。

### <a name="head-nodes"></a>ヘッド ノード

Hadoop サービスの高可用性を確保するために、HDInsight では 2 つのヘッド ノードが提供されます。 ヘッド ノードはどちらも、HDInsight クラスター内で同時にアクティブに実行されます。 Apache HDFS や Apache Hadoop YARN などの一部のサービスは、常にどちらかのヘッド ノードで "アクティブ" になります。 HiveServer2 や Hive MetaStore などのサービスは、両方のヘッド ノードで同時にアクティブになります。

クラスター内のさまざまなノードの種類のホスト名を取得するには、[Ambari REST API](hdinsight-hadoop-manage-ambari-rest-api.md#get-the-fqdn-of-cluster-nodes) を使用してください。

> [!IMPORTANT]  
> 数値は、ノードがプライマリかセカンダリかということと関連付けないでください。 数値には、各ノードに対して一意の名前を付与する役割しかありません。

### <a name="nimbus-nodes"></a>Nimbus ノード

Nimbus ノードは、Apache Storm クラスターで使用できます。 Nimbus ノードは、ワーカー ノード間で処理を分散および監視して、Hadoop JobTracker と同様の機能を提供します。 HDInsight では、Storm クラスターに対して 2 つの Nimbus ノードが提供されます。

### <a name="apache-zookeeper-nodes"></a>Apache Zookeeper ノード

[ZooKeeper](https://zookeeper.apache.org/) ノードは、ヘッド ノード上のマスター サービスのリーダー選定に使用されます。 これはまた、サービス、データ (ワーカー) ノード、およびゲートウェイが、どのヘッド ノードでマスター サービスがアクティブになっているかを確実に認識できるようにするためにも使用されます。 既定では、HDInsight では 3 つの ZooKeeper ノードが提供されます。

### <a name="worker-nodes"></a>ワーカー ノード

ワーカー ノードは、クラスターにジョブが送信されると実際にデータ分析を実行します。 ワーカー ノードに障害が発生した場合、そのノードが実行していたタスクは別のワーカー ノードに送信されます。 既定では、HDInsight では 4 つのワーカー ノードを作成します。 この数は、お客様のニーズに合うように、クラスターの作成時にも作成後にも変更できます。

### <a name="edge-node"></a>エッジ ノード

エッジ ノードは、クラスター内のデータ分析にアクティブには参加しません。 これは、開発者またはデータ サイエンティストが Hadoop を操作するときに使用します。 エッジ ノードは、クラスター内の他のノードと同じ Azure Virtual Network 内に存在し、他のすべてのノードに直接アクセスできます。 エッジ ノードは、重要な Hadoop サービスまたは分析ジョブからリソースを取り除く心配をせずに使用できます。

現在、HDInsight では、ML Services が既定でエッジ ノードを提供する唯一のクラスターの種類です。 HDInsight の ML Services のエッジ ノードは、R コードを分散処理のためにクラスターに送信する前にローカルでテストするために使用されます。

他のクラスターの種類とエッジ ノードを使用する方法の詳細については、[HDInsight でのエッジ ノードの使用](hdinsight-apps-use-edge-node.md)に関するドキュメントを参照してください。

## <a name="accessing-the-nodes"></a>ノードへのアクセス

インターネットを介したクラスターへのアクセスは、パブリック ゲートウェイを通して提供されます。 アクセスは、ヘッド ノードやエッジ ノード (存在する場合) への接続に制限されます。 ヘッド ノード上で実行されているサービスへのアクセスは、複数のヘッド ノードが存在しても影響を受けません。 パブリック ゲートウェイは、要求されたサービスをホストするヘッド ノードに要求をルーティングします。 たとえば、Apache Ambari がセカンダリ ヘッド ノードで現在ホストされている場合、ゲートウェイは Ambari の受信要求をそのノードにルーティングします。

パブリック ゲートウェイ経由のアクセスは、ポート 443 (HTTPS)、22、および 23 に制限されます。

|Port |説明 |
|---|---|
|443|ヘッド ノードでホストされている Ambari やその他の Web UI または REST API にアクセスするために使用されます。|
|22|SSH でプライマリ ヘッド ノードまたはエッジ ノードにアクセスするために使用されます。|
|23|SSH でセカンダリ ヘッド ノードにアクセスするために使用されます。 たとえば、`ssh username@mycluster-ssh.azurehdinsight.net` は、**mycluster** という名前のクラスターのプライマリ ヘッド ノードに接続します。|

SSH の使用方法の詳細については、[HDInsight での SSH の使用](hdinsight-hadoop-linux-use-ssh-unix.md)に関するドキュメントを参照してください。

### <a name="internal-fully-qualified-domain-names-fqdn"></a>内部完全修飾ドメイン名 (FQDN)

HDInsight クラスター内のノードには、クラスターからのみアクセスできる内部 IP アドレスと FQDN があります。 内部 FQDN または IP アドレスを使用してクラスターのサービスにアクセスする場合は、サービスへのアクセス時に Ambari を使用して IP または FQDN を検証する必要があります。

たとえば、Apache Oozie サービスは 1 つのヘッド ノードでのみ実行でき、SSH セッションからの `oozie` コマンドの使用にはサービスの URL が必要です。 この URL は、次のコマンドを使用して Ambari から取得できます。

```bash
export password='PASSWORD'
export clusterName="CLUSTERNAME"

curl -u admin:$password "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/configurations?type=oozie-site&tag=TOPOLOGY_RESOLVED" | grep oozie.base.url
```

このコマンドは、`oozie` コマンドで使用する内部 URL を含む、次のような値を返します。

```output
"oozie.base.url": "http://<ACTIVE-HEADNODE-NAME>cx.internal.cloudapp.net:11000/oozie"
```

Ambari REST API の使用方法の詳細については、[Apache Ambari REST API を使用した HDInsight の監視および管理](hdinsight-hadoop-manage-ambari-rest-api.md)に関するページを参照してください。

### <a name="accessing-other-node-types"></a>他のノード タイプへのアクセス

インターネット経由で直接アクセスできないノードには、次の方法を使用して接続できます。

|Method |説明 |
|---|---|
|SSH|SSH を使用してヘッド ノードに接続すると、ヘッド ノードから SSH を使用してクラスター内の他のノードに接続できます。 詳細については、[HDInsight での SSH の使用](hdinsight-hadoop-linux-use-ssh-unix.md)に関するドキュメントを参照してください。|
|SSH トンネル|インターネットに公開されていないノードのいずれかでホストされている Web サービスにアクセスする必要がある場合は、SSH トンネルを使用する必要があります。 詳細については、[HDInsight での SSH トンネリングの使用](hdinsight-linux-ambari-ssh-tunnel.md)に関するドキュメントを参照してください。|
|Azure Virtual Network|HDInsight クラスターが Azure Virtual Network に含まれる場合、同じ Virtual Network 上にあるリソースから、クラスター内のすべてのノードに直接アクセスできます。 詳細については、[HDInsight 用の仮想ネットワークの計画](hdinsight-plan-virtual-network-deployment.md)に関するドキュメントを参照してください。|

## <a name="how-to-check-on-a-service-status"></a>サービスの状態を確認する方法

ヘッド ノードで実行されているサービスの状態を確認するには、Ambari Web UI または Ambari REST API を使用してください。

### <a name="ambari-web-ui"></a>Ambari Web UI

Ambari Web UI は `https://CLUSTERNAME.azurehdinsight.net` で表示できます。 **CLUSTERNAME** をクラスターの名前に置き換えます。 メッセージが表示されたら、クラスターの HTTP ユーザーの資格情報を入力します。 既定の HTTP ユーザー名は **admin** であり、パスワードはクラスターを作成するときに入力したパスワードです。

Ambari ページにアクセスすると、インストールされているサービスがページの左側に表示されます。

![Apache Ambari のインストールされたサービス](./media/hdinsight-high-availability-linux/hdinsight-installed-services.png)

状態を示すためにサービスの横に表示されるアイコンがあります。 サービスに関連するアラートは、ページの上部にある **[アラート]** リンクを使用して表示できます。  Ambari には、いくつかの定義済みのアラートが用意されています。

次のアラートは、クラスターの可用性を監視するのに役立ちます。

| アラート名                               | 説明                                                                                                                                                                                  |
|------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| メトリック監視ステータス                    | このアラートは、監視ステータス スクリプトによって算出されるメトリック監視プロセスのステータスを示します。                                                                                   |
| Ambari エージェントのハートビート                   | サーバーがエージェントとの接続を失った場合、このアラートがトリガーされます。                                                                                                                        |
| ZooKeeper サーバー プロセス                 | このホスト レベルのアラートは、ZooKeeper サーバー プロセスが稼働していてネットワークをリッスンしていると確認できない場合にトリガーされます。                                                               |
| IOCache メタデータ サーバーのステータス           | このホスト レベルのアラートは、IOCache メタデータ サーバーが稼働していてクライアント要求に応答していると確認できない場合にトリガーされます。                                                            |
| JournalNode Web UI                       | JournalNode Web UI に到達できない場合に、このホスト レベルのアラートがトリガーされます。                                                                                                                 |
| Spark2 Thrift サーバー                     | このホスト レベルのアラートは、Spark2 Thrift サーバーが稼働していると確認できない場合にトリガーされます。                                                                                                |
| History Server プロセス                   | このホスト レベルのアラートは、History Server プロセスが稼働していてネットワークをリッスンしていると確認できない場合にトリガーされます。                                                                |
| History Server Web UI                    | History Server Web UI に到達できない場合に、このホスト レベルのアラートがトリガーされます。                                                                                                              |
| `ResourceManager` Web UI                   | このホスト レベルのアラートは、`ResourceManager` Web UI に到達できない場合にトリガーされます。                                                                                                             |
| NodeManager ヘルスの概要               | 異常な NodeManager がある場合に、このサービス レベルのアラートがトリガーされます                                                                                                                    |
| App Timeline Web UI                      | App Timeline Server Web UI に到達できない場合に、このホスト レベルのアラートがトリガーされます。                                                                                                         |
| DataNode ヘルスの概要                  | 異常な DataNode がある場合に、このサービス レベルのアラートがトリガーされます                                                                                                                       |
| NameNode Web UI                          | NameNode Web UI に到達できない場合に、このホスト レベルのアラートがトリガーされます。                                                                                                                    |
| ZooKeeper フェールオーバー コントローラー プロセス    | このホスト レベルのアラートは、ZooKeeper フェールオーバー コントローラー プロセスが稼働していてネットワークをリッスンしていると確認できない場合にトリガーされます。                                                   |
| Oozie Server Web UI                      | Oozie Server Web UI に到達できない場合に、このホスト レベルのアラートがトリガーされます。                                                                                                                |
| Oozie Server のステータス                      | このホスト レベルのアラートは、Oozie サーバーが稼働していてクライアント要求に応答していると確認できない場合にトリガーされます。                                                                      |
| Hive metastore プロセス                   | このホスト レベルのアラートは、Hive metastore プロセスが稼働していてネットワークをリッスンしていると確認できない場合にトリガーされます。                                                                 |
| HiveServer2 プロセス                      | このホスト レベルのアラートは、HiveServer が稼働していてクライアント要求に応答していると確認できない場合にトリガーされます。                                                                        |
| WebHCat サーバーのステータス                    | このホスト レベルのアラートは、`templeton` サーバーの状態が正常でない場合にトリガーされます。                                                                                                            |
| 利用可能な ZooKeeper サーバーの割合      | クラスター内でダウンしている ZooKeeper サーバーの数が、設定された重大しきい値を超えている場合は、このアラートがトリガーされます。 これにより、ZooKeeper プロセス チェックの結果が集計されます。     |
| Spark2 Livy サーバー                       | このホスト レベルのアラートは、Livy2 サーバーが稼働していると確認できない場合にトリガーされます。                                                                                                        |
| Spark2 History Server                    | このホスト レベルのアラートは、Spark2 History Server が稼働していると確認できない場合にトリガーされます。                                                                                               |
| メトリック コレクターのプロセス                | このアラートは、しきい値に等しい秒数の間、メトリック コレクターが稼働していて構成されたポートをリッスンしていると確認できない場合にトリガーされます。                                 |
| メトリック コレクター - HBase Master プロセス | このアラートは、構成された重大しきい値 (秒単位) の間、メトリック コレクターの HBase Master プロセスが稼働していてネットワークをリッスンしていると確認できない場合にトリガーされます。 |
| 利用可能なメトリック監視の割合       | このアラートは、構成された警告および重大しきい値の間、一定の割合のメトリック監視プロセスが稼働しておらず、ネットワークをリッスンしていない場合にトリガーされます。                             |
| 利用可能な NodeManager の割合           | クラスター内でダウンしている NodeManager の数が、設定された重大しきい値を超えている場合は、このアラートがトリガーされます。 これにより、NodeManager プロセス チェックの結果が集計されます。        |
| NodeManager ヘルス                       | このホスト レベルのアラートは、NodeManager コンポーネントから利用可能なノードの正常性のプロパティを確認します。                                                                                              |
| NodeManager Web UI                       | NodeManager Web UI に到達できない場合に、このホスト レベルのアラートがトリガーされます。                                                                                                                 |
| NameNode の高可用性のヘルス        | アクティブな NameNode またはスタンバイの NameNode が実行されていない場合に、このサービス レベルのアラートがトリガーされます。                                                                                     |
| DataNode プロセス                         | このホスト レベルのアラートは、個々の DataNode プロセスが稼働していてネットワークをリッスンしていると確認できない場合にトリガーされます。                                                         |
| DataNode Web UI                          | DataNode Web UI に到達できない場合に、このホスト レベルのアラートがトリガーされます。                                                                                                                    |
| 利用可能な JournalNode の割合           | クラスター内でダウンしている JournalNode の数が、設定された重大しきい値を超えている場合は、このアラートがトリガーされます。 これにより、JournalNode プロセス チェックの結果が集計されます。        |
| 利用可能な DataNode の割合              | クラスター内でダウンしている DataNode の数が、設定された重大しきい値を超えている場合は、このアラートがトリガーされます。 これにより、DataNode プロセス チェックの結果が集計されます。              |
| Zeppelin サーバーのステータス                   | このホスト レベルのアラートは、Zeppelin サーバーが稼働していてクライアント要求に応答していると確認できない場合にトリガーされます。                                                                   |
| HiveServer2 Interactive プロセス          | このホスト レベルのアラートは、HiveServerInteractive が稼働していてクライアント要求に応答していると確認できない場合にトリガーされます。                                                             |
| LLAP アプリケーション                         | このアラートは、LLAP アプリケーションが稼働していて要求に応答していると確認できない場合にトリガーされます。                                                                                    |

各サービスを選択して、その詳細を表示できます。

サービス ページには各サービスの状態と構成に関する情報が表示されますが、そのサービスがどのヘッド ノード上で実行されているかに関する情報は表示されません。 この情報を表示するには、ページの上部にある **[ホスト]** リンクを使用します。 このページに、ヘッド ノードを含むクラスター内のホストが表示されます。

![Apache Ambari のヘッドノード ホストの一覧](./media/hdinsight-high-availability-linux/hdinsight-hosts-list.png)

どちらかのヘッド ノードのリンクを選択すると、そのノードで実行されているサービスとコンポーネントが表示されます。

![Apache Ambari のコンポーネントの状態](./media/hdinsight-high-availability-linux/hdinsight-node-services.png)

Ambari の使用について詳しくは、[Apache Ambari Web UI を使用した HDInsight の監視および管理](hdinsight-hadoop-manage-ambari.md)に関するページを参照してください。

### <a name="ambari-rest-api"></a>Ambari REST API

Ambari REST API は、インターネット経由で使用できます。 HDInsight パブリック ゲートウェイが、この REST API を現在ホストしているヘッド ノードへのルーティング要求を処理します。

次のコマンドを使用して、Ambari REST API を通してサービスの状態を確認できます。

```bash
curl -u admin:PASSWORD https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/services/SERVICENAME?fields=ServiceInfo/state
```

* **PASSWORD** を HTTP ユーザー (admin) アカウントのパスワードに置き換えます。
* **CLUSTERNAME** は、クラスターの名前に置き換えます。
* **SERVICENAME** を、その状態を確認するサービスの名前に置き換えます。

たとえば、**mycluster** という名前のクラスターで、**HDFS** サービスの状態を、**password** というパスワードで確認するには、次のコマンドを使用します。

```bash
curl -u admin:password https://mycluster.azurehdinsight.net/api/v1/clusters/mycluster/services/HDFS?fields=ServiceInfo/state
```

応答は次の JSON のようになります。

```json
{
    "href" : "http://mycluster.wutj3h4ic1zejluqhxzvckxq0g.cx.internal.cloudapp.net:8080/api/v1/clusters/mycluster/services/HDFS?fields=ServiceInfo/state",
    "ServiceInfo" : {
    "cluster_name" : "mycluster",
    "service_name" : "HDFS",
    "state" : "STARTED"
    }
}
```

URL から、サービスが現在 **mycluster.wutj3h4ic1zejluqhxzvckxq0g** というヘッド ノードで実行されていることがわかります。

「state」から、サービスが現在実行されている( **STARTED**) ことがわかります。

クラスターにどのようなサービスがインストールされているかがわからない場合は、次のコマンドを使用して一覧を取得できます。

```bash
curl -u admin:PASSWORD https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/services
```

Ambari REST API の使用方法の詳細については、[Apache Ambari REST API を使用した HDInsight の監視および管理](hdinsight-hadoop-manage-ambari-rest-api.md)に関するページを参照してください。

#### <a name="service-components"></a>サービス コンポーネント

サービスの中に、個別に状態を確認できるコンポーネントが含まれている場合があります。 たとえば、HDFS には、NameNode コンポーネントが含まれています。 コンポーネントに関する情報を表示するコマンドは次のようになります。

```bash
curl -u admin:PASSWORD https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/services/SERVICE/components/component
```

サービスによってどのようなコンポーネントが提供されるかがわからない場合は、次のコマンドを使用して一覧を取得できます。

```bash
curl -u admin:PASSWORD https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/services/SERVICE/components/component
```

## <a name="how-to-access-log-files-on-the-head-nodes"></a>ヘッド ノードのログ ファイルへのアクセス方法

### <a name="ssh"></a>SSH

SSH 経由でヘッド ノードに接続している間、ログ ファイルは **/var/log**に保存されます。 たとえば、 **/var/log/hadoop-yarn/yarn** には、YARN のログが含まれます。

各ヘッド ノードは独自のログ エントリを持つ可能性があるため、両方のログを確認する必要があります。

### <a name="sftp"></a>SFTP

SSH ファイル転送プロトコルまたはセキュア ファイル転送プロトコル (SFTP) を使用してヘッド ノードに接続し、ログ ファイルを直接ダウンロードすることもできます。

SSH クライアントを使用するときと同様に、クラスターへの接続時にクラスターの SSH ユーザー アカウント名と SSH アドレスを指定する必要があります。 たとえば、「 `sftp username@mycluster-ssh.azurehdinsight.net` 」のように入力します。 メッセージが表示されたらアカウントのパスワードを入力するか、または `-i` パラメーターを使用して公開キーを指定します。

接続されると、`sftp>` プロンプトが表示されます。 このプロンプトでは、ディレクトリの移動、およびファイルのアップロードとダウンロードを行うことができます。 たとえば、次のコマンドでは、 **/var/log/hadoop/hdfs** ディレクトリに移動し、ディレクトリ内のすべてのファイルをダウンロードします。

```bash
cd /var/log/hadoop/hdfs
get *
```

使用可能なコマンドの一覧を参照するには、`sftp>` プロンプトで「`help`」と入力します。

> [!NOTE]  
> SFTP を使用した接続時にファイル システムを視覚化できるグラフィカル インターフェイスもあります。 たとえば、 [MobaXTerm](https://mobaxterm.mobatek.net/) では、Windows エクスプ ローラーに似たインターフェイスを使用して、ファイル システムを閲覧できます。

### <a name="ambari"></a>Ambari

> [!NOTE]  
> Ambari を使用してログ ファイルにアクセスするには、SSH トンネルを使用する必要があります。 個々のサービスの Web インターフェイスは、インターネットでの一般公開はされていません。 SSH トンネルの使用方法の詳細については、[SSH トンネリングの使用](hdinsight-linux-ambari-ssh-tunnel.md)に関するドキュメントを参照してください。

Ambari Web UI から、ログ (例: YARN) を表示するサービスを選択します。 その後、 **[クイック リンク]** を使用して、ログを表示するヘッド ノードを選択します。

![クイック リンクを使用したログの表示](./media/hdinsight-high-availability-linux/quick-links-view-logs.png)

## <a name="how-to-configure-the-node-size"></a>ノード サイズの構成方法

ノードのサイズを選択できるのは、クラスターの作成中のみです。 「[HDInsight の料金](https://azure.microsoft.com/pricing/details/hdinsight/)」ページで、HDInsight で使用できるまざまな VM サイズの一覧を確認できます。

クラスターを作成するときに、ノードのサイズを指定できます。 次の情報では、[Azure portal](https://portal.azure.com/)、[Azure PowerShell モジュール Az](/powershell/azure/)、[Azure CLI](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest) を使用してサイズを指定する方法について説明します。

* **Azure ポータル**:クラスターを作成するときに、クラスターによって使用されるノードのサイズを設定できます。

    ![ノード サイズの選択画面を示しているクラスター作成ウィザードの画像](./media/hdinsight-high-availability-linux/azure-portal-cluster-configuration-pricing-hadoop.png)

* **Azure CLI**:[`az hdinsight create`](https://docs.microsoft.com/cli/azure/hdinsight?view=azure-cli-latest#az-hdinsight-create) コマンドを使用している場合は、`--headnode-size`、`--workernode-size`、および `--zookeepernode-size` パラメーターを使用して、ヘッド、ワーカー、および ZooKeeper ノードのサイズを設定できます。

* **Azure PowerShell**:[New-AzHDInsightCluster](https://docs.microsoft.com/powershell/module/az.hdinsight/new-azhdinsightcluster) コマンドレットを使用するときに、`-HeadNodeSize`、`-WorkerNodeSize`、および`-ZookeeperNodeSize`のパラメーターを使用してヘッド、ワーカー ノード、および ZooKeeper ノードのサイズを設定できます。

## <a name="next-steps"></a>次のステップ

この記事で説明した項目の詳細については、次を参照してください。

* [Apache Ambari REST リファレンス](https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md)
* [Azure CLI のインストールと構成](https://docs.microsoft.com//cli/azure/install-azure-cli?view=azure-cli-latest)
* [Azure PowerShell モジュール Az をインストールして構成します](/powershell/azure/)。
* [Apache Ambari を使用した HDInsight の管理](hdinsight-hadoop-manage-ambari.md)
* [Provision Linux-based HDInsight clusters (Linux ベースの HDInsight クラスターのプロビジョニング)](hdinsight-hadoop-provision-linux-clusters.md)
