---
title: Apache Kafka のミラーリングに関するトピック - Azure HDInsight
description: Apache Kafka のミラーリング機能を使用してセカンダリ クラスターにトピックをミラーリングすることにより、HDInsight クラスターに Kafka のレプリカを保持する方法について説明します。
services: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 05/01/2018
ms.openlocfilehash: 0c37ad6de867c4abe4ebf0e6c7a40b5cf27c4541
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2019
ms.locfileid: "58079641"
---
# <a name="use-mirrormaker-to-replicate-apache-kafka-topics-with-kafka-on-hdinsight"></a>MirrorMaker を使用して HDInsight 上の Kafka に Apache Kafka トピックをレプリケートする

Apache Kafka のミラーリング機能を使用して、セカンダリ クラスターにトピックをレプリケートする方法について説明します。 ミラーリングは、継続的なプロセスとして実行できるほか、1 つのクラスターから別のクラスターにデータを移行する方法として断続的に使用することもできます。

この例では、ミラーリングを使用して、2 つの HDInsight クラスター間でトピックをレプリケートします。 両方のクラスターは同じリージョンの Azure Virtual Network に存在します。

> [!WARNING]  
> ミラーリングは、フォールト トレランスを実現するための手段として考慮するべきではありません。 トピック内の項目へのオフセットは、移行元クラスターと移行先クラスターによって異なるため、クライアントはこれら 2 つを入れ替えて使用することはできません。
>
> フォールト トレランスを考慮する場合は、クラスター内のトピックをレプリケートする設定にします。 詳しくは、「[HDInsight での Apache Kafka の概要](apache-kafka-get-started.md)」をご覧ください。

## <a name="how-apache-kafka-mirroring-works"></a>Apache Kafka のミラーリングのしくみ

ミラーリングでは、Apache Kafka に含まれるツール [MirrorMaker](https://cwiki.apache.org/confluence/pages/viewpage.action?pageId=27846330) によって移行元クラスターのトピックからレコードが使用され、移行元クラスターにローカル コピーが作成されます。 MirrorMaker では、移行元クラスターから読み取りを行う 1 つ (あるいは複数) の*コンシューマー*と、ローカル (移行先) クラスターへの書き込みを行う*プロデューサー*を使用します。

次の図にミラーリング プロセスを示します。

![ミラーリング プロセスの図](./media/apache-kafka-mirroring/kafka-mirroring.png)

HDInsight の Apache Kafka では、パブリック インターネットを介した Kafka サービスへのアクセスを提供されていません。 Kafka のプロデューサーまたはコンシューマーは、Kafka クラスター内のノードと同じ Azure 仮想ネットワークに存在している必要があります。 この例では、Kafka の移行元クラスターと移行先クラスターの両方を Azure 仮想ネットワーク内に配置します。 次の図に、クラスター間の通信フローを示します。

![Azure 仮想ネットワークにおける、移行元および移行先の Kafka クラスターの図](./media/apache-kafka-mirroring/spark-kafka-vnet.png)

移行元クラスターと移行先クラスターでは、ノードとパーティションの数、およびトピック内のオフセットが異なります。 ミラーリングではパーティション分割に使用するキー値が保持されるため、レコードの順序はキー単位で保存されます。

### <a name="mirroring-across-network-boundaries"></a>ネットワーク境界を超えたミラーリング

異なるネットワークの Kafka クラスター間でミラーリングの必要がある場合には、次の追加の考慮事項があります。

* **ゲートウェイ**:ネットワークは、TCPIP レベルで通信できる必要があります。

* **名前解決**:各ネットワークの Kafka クラスターは、ホスト名を使用して相互に接続できる必要があります。 そのためには、要求を他のネットワークに転送するように構成された各ネットワークに、ドメイン ネーム システム (DNS) サーバーが必要です。

    Azure Virtual Network を作成するときに、ネットワークで自動的に提供される DNS を使用せず、カスタムの DNS サーバーおよびサーバーの IP アドレスを指定する必要があります。 Virtual Network の作成が完了したら、その IP アドレスを使用する Azure Virtual Machine を作成し、そこに DNS ソフトウェアをインストールして構成を行います。

    > [!WARNING]  
    > カスタム DNS サーバーの作成と構成は、HDInsight を Virtual Network にインストールする前に行うようにします。 HDInsight が Virtual Network 用に構成された DNS サーバーを使用するために必要な、追加の構成はありません。

2 つの Azure Virtual Network を接続する方法の詳細については、[VNet と VNet 間の接続の構成](../../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md)に関する記事を参照してください。

## <a name="create-apache-kafka-clusters"></a>Apache Kafka クラスターを作成する

Azure 仮想ネットワークと Kafka クラスターは手動で作成できますが、Azure Resource Manager テンプレートを使用する方が簡単です。 次の手順に従って、Azure 仮想ネットワークと 2 つの Kafka クラスターを Azure サブスクリプションにデプロイします。

1. 次のボタンを使用して Azure にサインインし、Azure Portal でテンプレートを開きます。
   
    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fhditutorialdata.blob.core.windows.net%2Farmtemplates%2Fcreate-linux-based-kafka-mirror-cluster-in-vnet-v2.1.json" target="_blank"><img src="./media/apache-kafka-mirroring/deploy-to-azure.png" alt="Deploy to Azure"></a>
   
    Azure Resource Manager テンプレートは、**https://hditutorialdata.blob.core.windows.net/armtemplates/create-linux-based-kafka-mirror-cluster-in-vnet-v2.1.json** にあります。

    > [!WARNING]  
    > HDInsight で Kafka の可用性を保証するには、クラスターに少なくとも 3 つのワーカー ノードが必要です。 このテンプレートは、3 つのワーカー ノードが含まれる Kafka クラスターを作成します。

2. 次の情報に従って、**[カスタム デプロイ]** ブレードの各エントリに入力します。
    
    ![HDInsight のカスタム デプロイ](./media/apache-kafka-mirroring/parameters.png)
    
    * **リソース グループ**:グループを作成するか、または既存のグループを選択します。 このグループに HDInsight クラスターが含まれます。

    * **場所**: 地理的に近い場所を選択します。
     
    * **Base Cluster Name (ベース クラスター名)**:この値は、Kafka クラスターのベース名として使用されます。 たとえば、「**hdi**」と入力すると、**source-hdi** と、**dest-hdi** という名前のクラスターが作成されます。

    * **Cluster Login User Name (クラスター ログイン ユーザー名)**:移行元および移行先の Kafka クラスターの管理者ユーザー名。

    * **クラスター ログイン パスワード**:移行元および移行先の Kafka クラスターの管理者ユーザー パスワード。

    * **SSH ユーザー名**:移行元および移行先の Kafka クラスターの作成に使用する SSHユーザー。

    * **SSH パスワード**:移行元および移行先の Kafka クラスター用の SSH ユーザーのパスワード。

3. **使用条件**を読み、**[上記の使用条件に同意する]** をオンにします。

4. 最後に、**[ダッシュボードにピン留めする]** をオンにし、**[購入]** をクリックします。 クラスターの作成には約 20 分かかります。

> [!IMPORTANT]  
> 各 HDInsight クラスターの名前は、**source-BASENAME** と **dest-BASENAME** です。BASENAME はテンプレートで指定した名前です。 これらの名前は、後の手順でクラスターに接続するときに使用します。

## <a name="create-topics"></a>トピックの作成

1. SSH を使用して**移行元**クラスターに接続します。

    ```bash
    ssh sshuser@source-BASENAME-ssh.azurehdinsight.net
    ```

    **sshuser** は、クラスターの作成時に使用した SSH ユーザー名に置き換えます。 **BASENAME** は、クラスターの作成時に使用したベース名に置き換えます。

    詳細については、[HDInsight での SSH の使用](../hdinsight-hadoop-linux-use-ssh-unix.md)に関するページを参照してください。

2. 次のコマンドを使用して、移行元クラスターの Apache Zookeeper ホストを見つけます。

    ```bash
    # Install jq if it is not installed
    sudo apt -y install jq
    # get the zookeeper hosts for the source cluster
    export SOURCE_ZKHOSTS=`curl -sS -u admin -G https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/services/ZOOKEEPER/components/ZOOKEEPER_SERVER | jq -r '["\(.host_components[].HostRoles.host_name):2181"] | join(",")' | cut -d',' -f1,2`
    ```

    `$CLUSTERNAME` を移行元クラスターの名前に置き換えます。 プロンプトが表示されたら、クラスターのログイン (管理者) アカウントのパスワードを入力します。

3. `testtopic` という名前のトピックを作成するには、次のコマンドを使います。

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --create --replication-factor 2 --partitions 8 --topic testtopic --zookeeper $SOURCE_ZKHOSTS
    ```

3. トピックが作成されたことを確認するには、次のコマンドを使用します。

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --list --zookeeper $SOURCE_ZKHOSTS
    ```

    応答には `testtopic` が含まれます。

4. 次を使用して、この (**移行元**) クラスターの Zookeeper ホスト情報を表示します。

    ```bash
    echo $SOURCE_ZKHOSTS
    ```

    次のテキストのような情報が返されます。

    `zk0-source.aazwc2onlofevkbof0cuixrp5h.gx.internal.cloudapp.net:2181,zk1-source.aazwc2onlofevkbof0cuixrp5h.gx.internal.cloudapp.net:2181`

    この情報は保存してください。 次のセクションで使用します。

## <a name="configure-mirroring"></a>ミラーリングの構成

1. 別の SSH セッションを使用して**移行先**クラスターに接続します。

    ```bash
    ssh sshuser@dest-BASENAME-ssh.azurehdinsight.net
    ```

    **sshuser** は、クラスターの作成時に使用した SSH ユーザー名に置き換えます。 **BASENAME** は、クラスターの作成時に使用したベース名に置き換えます。

    詳細については、[HDInsight での SSH の使用](../hdinsight-hadoop-linux-use-ssh-unix.md)に関するページを参照してください。

2. `consumer.properties` ファイルは、**移行元**クラスターとの通信を構成するために使われます。 ファイルを作成するには、次のコマンドを使います。

    ```bash
    nano consumer.properties
    ```

    `consumer.properties` ファイルの内容として、次のテキストを使用します。

    ```yaml
    zookeeper.connect=SOURCE_ZKHOSTS
    group.id=mirrorgroup
    ```

    **SOURCE_ZKHOSTS** を、**移行元**クラスターからの Zookeeper ホストの情報に置き換えます。

    このファイルでは、移行元の Kafka クラスターからの読み取りに使用するコンシューマー情報について説明します。 コンシューマーの構成の詳細については、「[Consumer Configs (コンシューマーの構成)](https://kafka.apache.org/documentation#consumerconfigs)」(kafka.apache.org) を参照してください。

    ファイルを保存するには、**Ctrl + X** キー、**Y** キー、**Enter** キーの順に押します。

3. 移行先のクラスターと通信するプロデューサーを構成する前に、**移行先**クラスターのブローカー ホストを検索する必要があります。 この情報の取得には、次のコマンドを使用します。

    ```bash
    sudo apt -y install jq
    DEST_BROKERHOSTS=`curl -sS -u admin -G https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/services/KAFKA/components/KAFKA_BROKER | jq -r '["\(.host_components[].HostRoles.host_name):9092"] | join(",")' | cut -d',' -f1,2`
    echo $DEST_BROKERHOSTS
    ```

    `$CLUSTERNAME` を移行先クラスターの名前に置き換えます。 プロンプトが表示されたら、クラスターのログイン (管理者) アカウントのパスワードを入力します。

    `echo` コマンドでは、次のテキストのような情報が返されます。

        wn0-dest.aazwc2onlofevkbof0cuixrp5h.gx.internal.cloudapp.net:9092,wn1-dest.aazwc2onlofevkbof0cuixrp5h.gx.internal.cloudapp.net:9092

4. `producer.properties` ファイルは、__移行先__ クラスターとの通信に使われます。 ファイルを作成するには、次のコマンドを使います。

    ```bash
    nano producer.properties
    ```

    `producer.properties` ファイルの内容として、次のテキストを使用します。

    ```yaml
    bootstrap.servers=DEST_BROKERS
    compression.type=none
    ```

    **DEST_BROKERS** を、前の手順で取得したブローカー情報に置き換えます。

    プロデューサーの構成の詳細については、「[Producer Configs (プロデューサーの構成)](https://kafka.apache.org/documentation#producerconfigs)」(kafka.apache.org) を参照してください。

5. 次のコマンドを使用して、移行先クラスターの Zookeeper ホストを見つけます。

    ```bash
    # Install jq if it is not installed
    sudo apt -y install jq
    # get the zookeeper hosts for the source cluster
    export DEST_ZKHOSTS=`curl -sS -u admin -G https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/services/ZOOKEEPER/components/ZOOKEEPER_SERVER | jq -r '["\(.host_components[].HostRoles.host_name):2181"] | join(",")' | cut -d',' -f1,2`
    ```

    `$CLUSTERNAME` を移行先クラスターの名前に置き換えます。 プロンプトが表示されたら、クラスターのログイン (管理者) アカウントのパスワードを入力します。

7. HDInsight の Kafka に使用される既定の構成では、トピックの自動作成が許可されません。 ミラーリング プロセスを開始する前に、次のいずれかの方法を選択する必要があります。

    * **移行先クラスターにトピックを作成する**:この方法を選択した場合、パーティション数とレプリケーション係数も設定することができます。

        次のコマンドを使用して事前にトピックを作成することができます。

        ```bash
        /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --create --replication-factor 2 --partitions 8 --topic testtopic --zookeeper $DEST_ZKHOSTS
        ```

        `testtopic` は、作成するトピックの名前に置き換えます。

    * **トピックを自動的に作成するようにクラスターを構成する**:この方法を選択した場合、MirrorMaker によって自動的にトピックが作成されます。ただし、トピックと共に作成されるパーティションの数またはレプリケーション係数は、移行元のトピックと異なる場合があります。

        トピックを自動的に作成するように移行先クラスターを構成するには、次の手順を実行します。

        1. [Azure Portal](https://portal.azure.com) から、移行先の Kafka クラスターを選択します。
        2. クラスターの概要から __[クラスター ダッシュボード]__ を選択します。 次に、__[HDInsight クラスター ダッシュボード]__ を選択します。 認証を求められたら、クラスターのログイン (管理者) 資格情報を使用して認証を行います。
        3. ページの左側にあるリストから __Kafka__ サービスを選択します。
        4. ページ中央の __[Configs]\(構成\)__ を選択します。
        5. __[フィルター]__ フィールドに `auto.create` の値を入力します。 プロパティの一覧にフィルターが適用されて `auto.create.topics.enable` 設定が表示されます。
        6. `auto.create.topics.enable` の値を true に変更して __[保存]__ を選択します。 ノートを追加して、もう一度 __[保存]__ を選択します。
        7. __Kafka__ サービスを選択し、__[Restart]\(再起動\)__ を選択して、__[Restart all affected]\(影響を受けるものをすべて再起動\)__ を選択します。 メッセージが表示されたら、__[Confirm restart all]\(すべて再起動\)__ を選択します。

## <a name="start-mirrormaker"></a>MirrorMaker の開始

1. **移行先**クラスターに SSH で接続してから、次のコマンドを使用して MirrorMaker プロセスを開始します。

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-run-class.sh kafka.tools.MirrorMaker --consumer.config consumer.properties --producer.config producer.properties --whitelist testtopic --num.streams 4
    ```

    この例で使用するパラメーターは次のとおりです。

    * **--consumer.config**:コンシューマーのプロパティを格納するファイルを指定します。 これらのプロパティは、*移行元*の Kafka クラスターから読み取りを行うコンシューマーの作成に使用します。

    * **--producer.config**:プロデューサーのプロパティを格納するファイルを指定します。 これらのプロパティは、*移行先*の Kafka クラスターへの書き込みを行うプロデューサーの作成に使用します。

    * **--whitelist**:MirrorMaker が移行元クラスターから移行先クラスターにレプリケートするトピックの一覧。

    * **--num.streams**:作成するコンシューマー スレッドの数。

   スタートアップ時に、MirrorMaker により次のテキストのような情報が返されます。

    ```json
    {metadata.broker.list=wn1-source.aazwc2onlofevkbof0cuixrp5h.gx.internal.cloudapp.net:9092,wn0-source.aazwc2onlofevkbof0cuixrp5h.gx.internal.cloudapp.net:9092, request.timeout.ms=30000, client.id=mirror-group-3, security.protocol=PLAINTEXT}{metadata.broker.list=wn1-source.aazwc2onlofevkbof0cuixrp5h.gx.internal.cloudapp.net:9092,wn0-source.aazwc2onlofevkbof0cuixrp5h.gx.internal.cloudapp.net:9092, request.timeout.ms=30000, client.id=mirror-group-0, security.protocol=PLAINTEXT}
    metadata.broker.list=wn1-source.aazwc2onlofevkbof0cuixrp5h.gx.internal.cloudapp.net:9092,wn0-kafka.aazwc2onlofevkbof0cuixrp5h.gx.internal.cloudapp.net:9092, request.timeout.ms=30000, client.id=mirror-group-2, security.protocol=PLAINTEXT}
    metadata.broker.list=wn1-source.aazwc2onlofevkbof0cuixrp5h.gx.internal.cloudapp.net:9092,wn0-source.aazwc2onlofevkbof0cuixrp5h.gx.internal.cloudapp.net:9092, request.timeout.ms=30000, client.id=mirror-group-1, security.protocol=PLAINTEXT}
    ```

2. **ソース** クラスターに SSH で接続してから、次のコマンドを使用して、プロデューサーを起動し、トピックにメッセージを送信します。

    ```bash
    SOURCE_BROKERHOSTS=`curl -sS -u admin -G https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/services/KAFKA/components/KAFKA_BROKER | jq -r '["\(.host_components[].HostRoles.host_name):9092"] | join(",")' | cut -d',' -f1,2`
    /usr/hdp/current/kafka-broker/bin/kafka-console-producer.sh --broker-list $SOURCE_BROKERHOSTS --topic testtopic
    ```

    `$CLUSTERNAME` を移行元クラスターの名前に置き換えます。 プロンプトが表示されたら、クラスターのログイン (管理者) アカウントのパスワードを入力します。

     カーソル付きの空白行が表示されたら、テキスト メッセージを数個入力します。 これらのメッセージは、**移行元**クラスター上のトピックに送信されます。 操作が完了したら、**Ctrl + C** キーを使用してプロデューサーのプロセスを終了します。

3. **移行先**クラスターに SSH で接続してから、**Ctrl + C** キーを使用して MirrorMaker プロセスを終了します。 このプロセスは、終了までに数秒かかる場合があります。 メッセージが移行先にレプリケートされたことを確認するには、次のコマンドを使います。

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-console-consumer.sh --zookeeper $DEST_ZKHOSTS --topic testtopic --from-beginning
    ```

    `$CLUSTERNAME` を移行先クラスターの名前に置き換えます。 プロンプトが表示されたら、クラスターのログイン (管理者) アカウントのパスワードを入力します。

    これで、MirrorMaster がトピックを移行元のクラスターから移行先にミラーリングしたときに作成された `testtopic` が、トピックの一覧に含まれるようになりました。 このトピックから取得するメッセージは、送信元クラスターで入力したものと同じです。

## <a name="delete-the-cluster"></a>クラスターを削除する

[!INCLUDE [delete-cluster-warning](../../../includes/hdinsight-delete-cluster-warning.md)]

このドキュメントの手順では両方のクラスターを同じ Azure リソース グループに作成したため、Azure Portal でこのリソース グループを削除するだけで済みます。 リソース グループを削除すると、このドキュメントに従って作成したすべてのリソース、Azure Virtual Network、クラスターで使用したストレージ アカウントが削除されます。

## <a name="next-steps"></a>次の手順

このドキュメントでは、[MirrorMaker](https://cwiki.apache.org/confluence/pages/viewpage.action?pageId=27846330) を使用して [Apache Kafka](https://kafka.apache.org/) クラスターのレプリカを作成する方法について説明しました。 次のリンクを使用することで、Kafka のその他の活用方法を知ることができます。

* [Apache Kafka MirrorMaker に関するドキュメント](https://cwiki.apache.org/confluence/pages/viewpage.action?pageId=27846330) (cwiki.apache.org)
* [HDInsight での Apache Kafka の使用](apache-kafka-get-started.md)
* [HDInsight 上の Apache Kafka で Apache Spark を使用する](../hdinsight-apache-spark-with-kafka.md)
* [HDInsight 上の Apache Kafka で Apache Storm を使用する](../hdinsight-apache-storm-with-kafka.md)
* [Azure Virtual Network 経由で Apache Kafka に接続する](apache-kafka-connect-vpn-gateway.md)
