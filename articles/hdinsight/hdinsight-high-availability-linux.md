---
title: "HDInsight (Hadoop) の高可用性機能 | Microsoft Docs"
description: "Linux ベースの HDInsight クラスターで、追加のヘッド ノードを使用することで、信頼性と可用性を改善する方法について説明します。 また、その結果が Ambari や Hive などの Hadoop サービスに与える影響、SSH を使用して各ヘッド ノードに個別に接続する方法についても説明します。"
services: hdinsight
editor: cgronlun
manager: jhubbard
author: Blackmist
documentationcenter: 
tags: azure-portal
ms.assetid: 99c9f59c-cf6b-4529-99d1-bf060435e8d4
ms.service: hdinsight
ms.custom: hdinsightactive
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 01/12/2017
ms.author: larryfr
translationtype: Human Translation
ms.sourcegitcommit: 0d8472cb3b0d891d2b184621d62830d1ccd5e2e7
ms.openlocfilehash: c4003809decfb83c08e808f4bd7a14c1ecf05a55
ms.lasthandoff: 03/21/2017


---
# <a name="availability-and-reliability-of-hadoop-clusters-in-hdinsight"></a>HDInsight における Hadoop クラスターの可用性と信頼性

Hadoop は、クラスター内のノード間でサービスやデータの冗長コピーを分散することにより、高い可用性と信頼性を実現します。 ただし、Hadoop の標準ディストリビューションに含まれるヘッド ノードは、通常&1; つのみです。 この&1; つのヘッド ノードで障害が発生すると、クラスターが動作を停止する可能性があります。

このような問題に対処するために、Azure の HDInsight クラスターは&2; つのヘッド ノードを備え、実行中の Hadoop サービスおよびジョブの可用性と信頼性を高めています。

> [!IMPORTANT]
> Linux は、バージョン 3.4 以上の HDInsight で使用できる唯一のオペレーティング システムです。 詳細については、[Window での HDInsight の廃止](hdinsight-component-versioning.md#hdi-version-32-and-33-nearing-deprecation-date)に関する記事を参照してください。

## <a name="understanding-the-nodes"></a>ノードについて
HDInsight クラスターのノードは、Azure Virtual Machines を使用して実装します。 ノードに障害が発生した場合はオフラインになり、新しいノードが作成され、障害が発生したノードと置換されます。 ノードがオフラインの間は、新しいノードがオンラインに戻るまで、同じ種類の別のノードが使用されます。

> [!NOTE]
> 障害発生時にノードがデータ分析を行っていた場合、ジョブの進捗状況は失われます。 障害が発生したノードで実行されていたジョブは、別のノードに再送信されます。
> 
> 

次のセクションでは、HDInsight で使用される個々のノード タイプについて説明します。 クラスターの種類によっては、一部のノード タイプのみが使用されます。 たとえば、Hadoop クラスターには、Nimbus ノードはありません。 HDInsight クラスターの種類ごとの使用されるノードの詳細については、「[HDInsight での Linux ベースの Hadoop クラスターの作成](hdinsight-hadoop-provision-linux-clusters.md#cluster-types)」の「クラスターの種類」セクションを参照してください。

### <a name="head-nodes"></a>ヘッド ノード
一部の Hadoop の実装には、ワーカー ノードの障害をスムーズに管理するサービスとコンポーネントをホストする、1 つのヘッド ノードがあります。 ただし、ヘッド ノードで実行されているマスター サービスが停止すると、クラスターは動作を停止します。

HDInsight クラスターには、セカンダリ ヘッド ノードが用意されています。これにより、プライマリ ノードで障害が発生した場合に、マスター サービスとコンポーネントの実行をセカンダリ ノードで継続できます。

> [!IMPORTANT]
> 両方のヘッド ノードは、クラスター内で同時にアクティブに実行されます。 HDFS や YARN などの一部のサービスは、常にどちらかのヘッド ノードで「アクティブ」になります (他方のヘッド ノードでは「スタンバイ」になります)。 HiveServer2 や Hive MetaStore などのサービスは、両方のヘッド ノードで同時にアクティブになります。
> 
> 

ヘッド ノード (および HDInsight の他のノード) では、ノードのホスト名の一部として数値が使用されます。 たとえば、`hn0-CLUSTERNAME` または `hn4-CLUSTERNAME` です。 

> [!IMPORTANT]
> ノードがプライマリとセカンダリのどちらであるかと、数値を関連付けないでください。数値は、各ノードに一意の名前を指定するためにのみ存在します。
> 
> 

### <a name="nimbus-nodes"></a>Nimbus ノード
Storm クラスターの場合、Nimbus ノードは、ワーカー ノード間で処理を分散および監視して、Hadoop JobTracker と同様の機能を提供します。 HDInsight では、Storm クラスターに対して 2 つの Nimbus ノードが提供されます。

### <a name="zookeeper-nodes"></a>Zookeeper ノード
[ZooKeeper](http://zookeeper.apache.org/) ノード (ZK) が使用され、データ (ワーカー) ノードとゲートウェイはどちらのヘッド ノードでマスター サービスがアクティブであるかがわかります。 既定では、HDInsight では 3 つの ZooKeeper ノードが提供されます。

### <a name="worker-nodes"></a>ワーカー ノード
ワーカー ノードは、クラスターにジョブが送信されると実際にデータ分析を実行します。 ワーカー ノードに障害が発生した場合、そのノードが実行していたタスクは別のワーカー ノードに送信されます。 既定では、HDInsight では 4 つのワーカー ノードが作成されます。ただし、クラスターの作成時とクラスターの作成後の両方で、ニーズに合わせてこの数を変更できます。

### <a name="edge-node"></a>エッジ ノード
エッジ ノードは、クラスター内のデータの分析にはアクティブに関与しませんが、開発者やデータ サイエンティストが Hadoop を操作するときに使用されます。 エッジ ノードは、クラスター内の他のノードと同じ Azure Virtual Network 内に存在し、他のすべてのノードに直接アクセスできます。 クラスターのデータ分析には関係しないため、重要な Hadoop サービスまたは分析ジョブからリソースを取り除く心配をせずに使用できます。

現在、HDInsight では、R Server が既定でエッジ ノードを提供する唯一のクラスターの種類です。 HDInsight の R Server のエッジ ノードは、R コードを分散処理のためにクラスターに送信する前にローカルでテストするために使用されます。

[Create a Linux-based HDInsight cluster with Hue on an Edge Node (エッジ ノードで Hue を使用して Linux ベースの HDInsight クラスターを作成する) (エッジ ノードで Hue を使用して Linux ベースの HDInsight クラスターを作成する)](https://azure.microsoft.com/documentation/templates/hdinsight-linux-with-hue-on-edge-node/)」は、エッジ ノードがある Hadoop クラスターの種類を作成するために使用できるサンプル テンプレートです。

## <a name="accessing-the-nodes"></a>ノードへのアクセス
インターネット経由でのクラスターへのアクセスはパブリック ゲートウェイを通して提供されおり、接続先はヘッド ノードと (HDInsight クラスターの R Server の場合は) エッジ ノードに制限されています。 パブリック ゲートウェイは、要求されたサービスをホストするヘッド ノードに要求をルーティングするため、ヘッド ノードで実行されているサービスへのアクセスは、ヘッド ノードが複数あっても影響を受けません。 たとえば、Ambari がセカンダリ ヘッド ノードで現在ホストされている場合、ゲートウェイは Ambari の受信要求をそのノードにルーティングします。

SSH を使用してクラスターにアクセスする場合は、ポート 22 (SSH の既定値) 経由で接続するとプライマリ ヘッド ノードに接続され、ポート 23 経由で接続するとセカンダリ ヘッド ノードに接続されます。 たとえば、`ssh username@mycluster-ssh.azurehdinsight.net` は、**mycluster** という名前のクラスターのプライマリ ヘッド ノードに接続します。

> [!NOTE]
> これは、SSH ファイル転送プロトコル (SFTP) などの SSH に基づくプロトコルにも当てはまります。
> 
> 

HDInsight クラスターの R Server に付属するエッジ ノードにも、ポート 22 経由で SSH を使用して直接アクセスできます。 たとえば、`ssh username@RServer.mycluster.ssh.azurehdinsight.net` は、**mycluster** という名前の HDInsight クラスターの R Server のエッジ ノードに接続します。 

### <a name="internal-fully-qualified-domain-names-fqdn"></a>内部完全修飾ドメイン名 (FQDN)
HDInsight クラスター内のノードには、クラスターからのみアクセスできる内部 IP アドレスと FQDN があります (ヘッド ノードまたはクラスターで実行されているジョブに対する SSH セッションなど)。内部 FQDN または IP アドレスを使用してクラスターのサービスにアクセスする場合は、サービスへのアクセス時に Ambari を使用して IP または FQDN を検証する必要があります。

たとえば、Oozie サービスは&1; つのヘッド ノードでのみ実行でき、SSH セッションからの `oozie` コマンドの使用にはサービスの URL が必要です。 これは、次のコマンドを使用して Ambari から取得できます。

    curl -u admin:PASSWORD "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/configurations?type=oozie-site&tag=TOPOLOGY_RESOLVED" | grep oozie.base.url

これにより、 `oozie` コマンドで使用する内部 URL を含む、次に似た値が返ります。

    "oozie.base.url": "http://hn0-CLUSTERNAME-randomcharacters.cx.internal.cloudapp.net:11000/oozie"

### <a name="accessing-other-node-types"></a>他のノード タイプへのアクセス
インターネット経由で直接アクセスできないノードに接続するには、次の方法を使用します。

* **SSH**: SSH を使用してヘッド ノードに接続すると、ヘッド ノードから SSH を使用してクラスター内の他のノードに接続できます。
* **SSH トンネル**: インターネットに公開されていないいずれかのノードのでホストされている Web サービスにアクセスするには、[SSH トンネルを使用](hdinsight-linux-ambari-ssh-tunnel.md)する必要があります。
* **Azure Virtual Network**: HDInsight クラスターが Azure Virtual Network に含まれる場合、同じ Virtual Network 上にあるリソースから、クラスター内のすべてのノードに直接アクセスできます。

## <a name="how-to-check-on-a-service-status"></a>サービスの状態を確認する方法
Ambari Web UI または Ambari REST API のいずれかを使用して、ヘッド ノードで実行されているサービスの状態を確認できます。

### <a name="ambari-web-ui"></a>Ambari Web UI
https://クラスター名>.azurehdinsight.net で Ambari Web UI を表示できます。 **CLUSTERNAME** をクラスターの名前に置き換えます。 メッセージが表示されたら、クラスターの HTTP ユーザーの資格情報を入力します。 既定の HTTP ユーザー名は **admin** であり、パスワードはクラスターを作成するときに入力したパスワードです。

Ambari ページにアクセスすると、インストールされているサービスがページの左側に表示されます。

![インストールされているサービス](./media/hdinsight-high-availability-linux/services.png)

状態を示すためにサービスの横に表示されるアイコンがあります。 サービスに関連するアラートは、ページの上部にある **[アラート]** リンクを使用して表示できます。 各サービスを選択して、その詳細を表示できます。

サービス ページには、各サービスの状態と構成に関する情報が表示されますが、サービスが実行されているヘッド ノードの情報は表示されません。 この情報を表示するには、ページの上部にある **[ホスト]** リンクを使用します。 これにより、ヘッド ノードを含むクラスター内のホストが表示されます。

![ホストの一覧](./media/hdinsight-high-availability-linux/hosts.png)

どちらかのヘッド ノードのリンクを選択すると、そのノードで実行されているサービスとコンポーネントが表示されます。

![コンポーネントの状態](./media/hdinsight-high-availability-linux/nodeservices.png)

### <a name="ambari-rest-api"></a>Ambari REST API
Ambari REST API はインターネット経由で使用でき、パブリック ゲートウェイが、この REST API を現在ホストしているヘッド ノードへのルーティング要求を処理します。

次のコマンドを使用して、Ambari REST API を通してサービスの状態を確認できます。

    curl -u admin:PASSWORD https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/services/SERVICENAME?fields=ServiceInfo/state

* **PASSWORD** を HTTP ユーザー (admin) アカウントのパスワードに置き換えます。
* **CLUSTERNAME** をクラスターの名前に置き換えます。
* **SERVICENAME** を、その状態を確認するサービスの名前に置き換えます。

たとえば、**mycluster** という名前のクラスターで、**HDFS** サービスの状態を、**password** というパスワードで確認するには、次のコマンドを使用します。

    curl -u admin:password https://mycluster.azurehdinsight.net/api/v1/clusters/mycluster/services/HDFS?fields=ServiceInfo/state

応答は次のようになります。

    {
      "href" : "http://hn0-CLUSTERNAME.randomcharacters.cx.internal.cloudapp.net:8080/api/v1/clusters/mycluster/services/HDFS?fields=ServiceInfo/state",
      "ServiceInfo" : {
        "cluster_name" : "mycluster",
        "service_name" : "HDFS",
        "state" : "STARTED"
      }
    }

URL から、サービスが現在 **hn0-CLUSTERNAME** というヘッド ノードで実行されていることがわかります。

「state」から、サービスが現在実行されている( **STARTED**) ことがわかります。

どのようなサービスがクラスターにインストールされているかわからない場合は、次のコマンドを使用して一覧を取得できます。

    curl -u admin:PASSWORD https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/services

#### <a name="service-components"></a>サービス コンポーネント
サービスの中に、個別に状態を確認できるコンポーネントが含まれている場合があります。 たとえば、HDFS には、NameNode コンポーネントが含まれています。 コンポーネントに関する情報を表示するコマンドは次のようになります。

    curl -u admin:PASSWORD https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/services/SERVICE/components/component

サービスがどのようなコンポーネントを備えているかわからない場合は、次のコマンドを使用して一覧を取得できます。

    curl -u admin:PASSWORD https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/services/SERVICE/components/component

## <a name="how-to-access-log-files-on-the-head-nodes"></a>ヘッド ノードのログ ファイルへのアクセス方法
### <a name="ssh"></a>SSH
SSH 経由でヘッド ノードに接続している間、ログ ファイルは **/var/log**に保存されます。 たとえば、 **/var/log/hadoop-yarn/yarn** には、YARN のログが含まれます。

各ヘッド ノードは独自のログ エントリを持つ可能性があるため、両方のログを確認する必要があります。

### <a name="sftp"></a>SFTP
SSH ファイル転送プロトコルまたはセキュア ファイル転送プロトコル (SFTP) を使用してヘッド ノードに接続し、ログ ファイルを直接ダウンロードすることもできます。

SSH クライアントを使用するときと同様に、クラスターへの接続時にクラスターの SSH ユーザー アカウント名と SSH アドレスを指定する必要があります。 たとえば、 `sftp username@mycluster-ssh.azurehdinsight.net`です。 また、メッセージが表示されたらアカウントのパスワードを入力するか、または `-i` パラメーターを使用して公開キーを指定する必要があります。

接続されると、 `sftp>` プロンプトが表示されます。 このプロンプトでは、ディレクトリの移動、およびファイルのアップロードとダウンロードを行うことができます。 たとえば、次のコマンドでは、 **/var/log/hadoop/hdfs** ディレクトリに移動し、ディレクトリ内のすべてのファイルをダウンロードします。

    cd /var/log/hadoop/hdfs
    get *

使用可能なコマンドの一覧を参照するには、`sftp>` プロンプトで「`help`」と入力します。

> [!NOTE]
> SFTP を使用した接続時にファイル システムを視覚化できるグラフィカル インターフェイスもあります。 たとえば、 [MobaXTerm](http://mobaxterm.mobatek.net/) では、Windows エクスプ ローラーに似たインターフェイスを使用して、ファイル システムを閲覧できます。

### <a name="ambari"></a>Ambari
> [!NOTE]
> Ambari を通してログ ファイルにアクセスするには、SSH トンネルが必要です。これは、個々のサービスの Web サイトはインターネット上に公開されていないためです。 SSH トンネルの使用の詳細については、「[SSH トンネリングを使用して Ambari Web UI、ResourceManager、JobHistory、NameNode、Oozie、およびその他の Web UI にアクセスする](hdinsight-linux-ambari-ssh-tunnel.md)」を参照してください。

Ambari Web UI から、ログを表示するサービス (例: YARN) を選択し、 **[クイック リンク]** を使用してログを表示するヘッド ノードを選択します。

![クイック リンクを使用したログの表示](./media/hdinsight-high-availability-linux/viewlogs.png)

## <a name="how-to-configure-the-node-size"></a>ノード サイズの構成方法
ノードのサイズを構成できるのは、クラスターの作成中のみです。 「[HDInsight の料金](https://azure.microsoft.com/pricing/details/hdinsight/)」ページで、HDInsight で使用できる、コア、メモリ、ローカル ストレージを含むさまざまな VM サイズの一覧を確認できます。

新しいクラスターを作成するときに、ノードのサイズを指定できます。 この後、[Azure Portal][preview-portal]、[Azure PowerShell][azure-powershell]、[Azure CLI][azure-cli] を使用してサイズを指定する方法についての情報を示します。

* **Azure ポータル**: 新しいクラスターを作成するときに、クラスターのヘッド ノード、ワーカー ノード、および (クラスターの種類で使用されている場合) ZooKeeper ノードのサイズ (価格レベル) を設定できます。
  
    ![ノード サイズの選択画面を示しているクラスター作成ウィザードの画像](./media/hdinsight-high-availability-linux/headnodesize.png)
* **Azure CLI**: `azure hdinsight cluster create` コマンドを使用するときに、`--headNodeSize`、`--workerNodeSize`、および `--zookeeperNodeSize` パラメーターを使用してヘッド ノード、ワーカー ノード、および ZooKeeper ノードのサイズを設定できます。
* **Azure PowerShell**: `New-AzureRmHDInsightCluster` コマンドレットを使用するときに、`-HeadNodeVMSize`、`-WorkerNodeSize`、および `-ZookeeperNodeSize` パラメーターを使用してヘッド ノード、ワーカー ノード、および ZooKeeper ノードのサイズを設定できます。

## <a name="next-steps"></a>次のステップ
このドキュメントでは、Azure HDInsight での Hadoop の高可用性の実現方法を説明しました。 このドキュメントに記載された事柄の詳細については、次の記事を参照してください。

* [Ambari REST リファレンス](https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md)
* [Azure CLI のインストールと構成](../cli-install-nodejs.md)
* [Azure PowerShell のインストールおよび構成](/powershell/azureps-cmdlets-docs)
* [Ambari を使用した HDInsight の管理](hdinsight-hadoop-manage-ambari.md)
* [Provision Linux-based HDInsight clusters (Linux ベースの HDInsight クラスターのプロビジョニング)](hdinsight-hadoop-provision-linux-clusters.md)

[preview-portal]: https://portal.azure.com/
[azure-powershell]: /powershell/azureps-cmdlets-docs
[azure-cli]: ../cli-install-nodejs.md

