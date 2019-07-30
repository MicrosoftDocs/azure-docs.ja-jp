---
title: Apache Kafka のミラーリングに関するトピック - Azure HDInsight
description: Apache Kafka のミラーリング機能を使用してセカンダリ クラスターにトピックをミラーリングすることにより、HDInsight クラスターに Kafka のレプリカを保持する方法について説明します。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 05/24/2019
ms.openlocfilehash: bdc393d041bd40fd27493ccc8f3c4f39adfa35b2
ms.sourcegitcommit: cf438e4b4e351b64fd0320bf17cc02489e61406a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/08/2019
ms.locfileid: "67657128"
---
# <a name="use-mirrormaker-to-replicate-apache-kafka-topics-with-kafka-on-hdinsight"></a>MirrorMaker を使用して HDInsight 上の Kafka に Apache Kafka トピックをレプリケートする

Apache Kafka のミラーリング機能を使用して、セカンダリ クラスターにトピックをレプリケートする方法について説明します。 ミラーリングは、継続的なプロセスとして実行できるほか、1 つのクラスターから別のクラスターにデータを移行する方法として断続的に使用することもできます。

この例では、ミラーリングを使用して、2 つの HDInsight クラスター間でトピックをレプリケートします。 どちらのクラスターも、異なるデータセンター内の異なる仮想ネットワーク内にあります。

> [!WARNING]  
> ミラーリングは、フォールト トレランスを実現するための手段として考慮するべきではありません。 トピック内の項目へのオフセットは、プライマリ クラスターとセカンダリ クラスターによって異なるため、クライアントはこれら 2 つを入れ替えて使用することはできません。
>
> フォールト トレランスを考慮する場合は、クラスター内のトピックをレプリケートする設定にします。 詳しくは、「[HDInsight での Apache Kafka の概要](apache-kafka-get-started.md)」をご覧ください。

## <a name="how-apache-kafka-mirroring-works"></a>Apache Kafka のミラーリングのしくみ

ミラーリングでは、Apache Kafka に含まれるツール [MirrorMaker](https://cwiki.apache.org/confluence/pages/viewpage.action?pageId=27846330) によってプライマリ クラスターのトピックからレコードが使用され、セカンダリ クラスターにローカル コピーが作成されます。 MirrorMaker では、プライマリ クラスターから読み取りを行う 1 つ (あるいは複数) の*コンシューマー*と、ローカル (セカンダリ) クラスターへの書き込みを行う*プロデューサー*を使用します。

ディザスター リカバリーに最も役立つミラーリングの設定では、異なる Azure リージョンの Kafka クラスターを使用します。 これを実現するには、クラスターが存在する仮想ネットワークをピアリングします。

次の図は、ミラーリング プロセスとクラスター間で通信がどのように流れるかを示しています。

![ミラーリング プロセスの図](./media/apache-kafka-mirroring/kafka-mirroring-vnets2.png)

プライマリ クラスターとセカンダリ クラスターでは、ノードとパーティションの数、およびトピック内のオフセットが異なります。 ミラーリングではパーティション分割に使用するキー値が保持されるため、レコードの順序はキー単位で保存されます。

### <a name="mirroring-across-network-boundaries"></a>ネットワーク境界を超えたミラーリング

異なるネットワークの Kafka クラスター間でミラーリングの必要がある場合には、次の追加の考慮事項があります。

* **ゲートウェイ**:ネットワークは、TCP/IP レベルで通信できる必要があります。

* **サーバー アドレス指定**:IP アドレスまたは完全修飾ドメイン名を使用して、クラスター ノードのアドレスを指定することができます。

    * **IP アドレス**:IP アドレスのアドバタイジングを使用するように Kafka クラスターを構成する場合は、ブローカー ノードと Zookeeper ノードの IP アドレスを使用してミラーリングのセットアップを続行することができます。
    
    * **ドメイン名**:IP アドレスのアドバタイジングを行うように Kafka クラスターを構成しない場合、クラスターは完全修飾ドメイン名 (FQDN) を使用して相互に接続できる必要があります。 そのためには、要求を他のネットワークに転送するように構成された各ネットワークに、ドメイン ネーム システム (DNS) サーバーが必要です。 Azure Virtual Network を作成するときに、ネットワークで自動的に提供される DNS を使用せず、カスタムの DNS サーバーおよびサーバーの IP アドレスを指定する必要があります。 Virtual Network の作成が完了したら、その IP アドレスを使用する Azure Virtual Machine を作成し、そこに DNS ソフトウェアをインストールして構成を行います。

    > [!WARNING]  
    > カスタム DNS サーバーの作成と構成は、HDInsight を Virtual Network にインストールする前に行うようにします。 HDInsight が Virtual Network 用に構成された DNS サーバーを使用するために必要な、追加の構成はありません。

2 つの Azure Virtual Network を接続する方法の詳細については、[VNet と VNet 間の接続の構成](../../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md)に関する記事を参照してください。

## <a name="mirroring-architecture"></a>ミラーリング アーキテクチャ

このアーキテクチャの特徴は、リソース グループと仮想ネットワークが異なる 2 つのクラスター、すなわち**プライマリ**と**セカンダリ**があることです。

### <a name="creation-steps"></a>作成手順

1. 2 つの新しいリソース グループを作成します。

    |リソース グループ | Location |
    |---|---|
    | kafka-primary-rg | 米国中部 |
    | kafka-secondary-rg | 米国中北部 |

1. **kafka-primary-rg** に新しい仮想ネットワーク **kafka-primary-vnet** を作成します。 既定の設定のままにします。
1. **kafka-secondary-rg** に新しい仮想ネットワーク **kafka-secondary-vnet** を作成します。こちらも既定の設定を使用します。

1. 2 つの新しい Kafka クラスターを作成します。

    | クラスター名 | リソース グループ | Virtual Network | ストレージ アカウント |
    |---|---|---|---|
    | kafka-primary-cluster | kafka-primary-rg | kafka-primary-vnet | kafkaprimarystorage |
    | kafka-secondary-cluster | kafka-secondary-rg | kafka-secondary-vnet | kafkasecondarystorage |

1. 仮想ネットワークのピアリングを作成します。 この手順では、2 つのピアリングを作成します。1 つは **kafka-primary-vnet** から **kafka-secondary-vnet** に向かうもの、もう 1 つは **kafka-secondary-vnet** から **kafka-primary-vnet** に戻るものです。
    1. **kafka-primary-vnet** 仮想ネットワークを選択します。
    1. **[設定]** で **[ピアリング]** をクリックします。
    1. **[追加]** をクリックします。
    1. **[ピアリングの追加]** 画面で、次のスクリーンショットに示すように詳細を入力します。

        ![vnet ピアリングの追加](./media/apache-kafka-mirroring/add-vnet-peering.png)

1. IP アドバタイズを構成します。
    1. `https://PRIMARYCLUSTERNAME.azurehdinsight.net` からプライマリ クラスターの Ambari ダッシュボードに移動します。
    1. **[サービス]**  >  **[Kafka]** をクリックします。 **[Configs]** タブをクリックします。
    1. **kafka env テンプレート** セクションの一番下に次の構成行を追加します。 **[Save]** をクリックします。
    
        ```
        # Configure Kafka to advertise IP addresses instead of FQDN
        IP_ADDRESS=$(hostname -i)
        echo advertised.listeners=$IP_ADDRESS
        sed -i.bak -e '/advertised/{/advertised@/!d;}' /usr/hdp/current/kafka-broker/conf/server.properties
        echo "advertised.listeners=PLAINTEXT://$IP_ADDRESS:9092" >> /usr/hdp/current/kafka-broker/conf/server.properties
        ```

    1. **[構成の保存]** 画面でメモを入力し、 **[保存]** をクリックします。
    1. 構成の警告が表示されたら、 **[Proceed Anyway] (警告を無視して続行)** をクリックします。
    1. **[Save Configuration Changes] (構成の変更を保存)** の **[Ok]** をクリックします。
    1. **再起動が必要**通知の **[再起動]**  >  **[すべて再起動]** をクリックします。 **[Confirm Restart All] (すべて再起動)** をクリックします。

        ![kafka ノードの再起動](./media/apache-kafka-mirroring/ambari-restart-notification.png)

1. すべてのネットワーク インターフェイスをリッスンするように Kafka を構成します。
    1. **[サービス]**  >  **[Kafka]** の **[構成]** タブにとどまります。 **[Kafka Broker] (Kafka ブローカー)** セクションで、**リスナー** プロパティを `PLAINTEXT://0.0.0.0:9092` に設定します。
    1. **[Save]** をクリックします。
    1. **[再起動]** 、 **[Confirm Restart All] (すべて再起動)** をクリックします。

1. プライマリ クラスターのブローカーの IP アドレスと Zookeeper アドレスを記録します。
    1. Ambari ダッシュボードの **[ホスト]** をクリックします。
    1. ブローカーと Zookeeper の IP アドレスをメモしておきます。 ブローカー ノードのホスト名の最初の 2 文字は **wn**、Zookeeper ノードのホスト名の最初 2 文字は **zk** です。

        ![IP アドレスの表示](./media/apache-kafka-mirroring/view-node-ip-addresses2.png)

1. 2 番目のクラスター **kafka-secondary-cluster** に対して前の 3 つの手順を繰り返します。IP アドバタイズを構成し、リスナーを設定して、ブローカーと Zookeeper の IP アドレスをメモします。

## <a name="create-topics"></a>トピックの作成

1. **SSH** を使用してプライマリ クラスターに接続します。

    ```bash
    ssh sshuser@PRIMARYCLUSTER-ssh.azurehdinsight.net
    ```

    **sshuser** は、クラスターの作成時に使用した SSH ユーザー名に置き換えます。 **BASENAME** は、クラスターの作成時に使用したベース名に置き換えます。

    詳細については、[HDInsight での SSH の使用](../hdinsight-hadoop-linux-use-ssh-unix.md)に関するページを参照してください。

2. プライマリ クラスターの Apache Zookeeper ホストを格納した変数を作成するには、次のコマンドを使用します。 `ZOOKEEPER_IP_ADDRESS1` などの文字列は、`10.23.0.11` や `10.23.0.7` など、先ほど記録した実際の IP アドレスに置き換える必要があります。 カスタム DNS サーバーで FQDN 解決を使用している場合は、[こちらの手順](apache-kafka-get-started.md#getkafkainfo)に従ってブローカーと Zookeeper の名前を取得します。

    ```bash
    # get the zookeeper hosts for the primary cluster
    export PRIMARY_ZKHOSTS='ZOOKEEPER_IP_ADDRESS1:2181, ZOOKEEPER_IP_ADDRESS2:2181, ZOOKEEPER_IP_ADDRESS3:2181'
    ```

3. `testtopic` という名前のトピックを作成するには、次のコマンドを使います。

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --create --replication-factor 2 --partitions 8 --topic testtopic --zookeeper $PRIMARY_ZKHOSTS
    ```

3. トピックが作成されたことを確認するには、次のコマンドを使用します。

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --list --zookeeper $PRIMARY_ZKHOSTS
    ```

    応答には `testtopic` が含まれます。

4. 次を使用して、この (**プライマリ**) クラスターの Zookeeper ホスト情報を表示します。

    ```bash
    echo $PRIMARY_ZKHOSTS
    ```

    次のテキストのような情報が返されます。

    `10.23.0.11:2181,10.23.0.7:2181,10.23.0.9:2181`

    この情報は保存してください。 次のセクションで使用します。

## <a name="configure-mirroring"></a>ミラーリングの構成

1. 別の SSH セッションを使用して**セカンダリ** クラスターに接続します。

    ```bash
    ssh sshuser@SECONDARYCLUSTER-ssh.azurehdinsight.net
    ```

    **sshuser** は、クラスターの作成時に使用した SSH ユーザー名に置き換えます。 **SECONDARYCLUSTER** は、クラスターの作成時に使用した名前に置き換えます。

    詳細については、[HDInsight での SSH の使用](../hdinsight-hadoop-linux-use-ssh-unix.md)に関するページを参照してください。

2. `consumer.properties` ファイルは、**プライマリ** クラスターとの通信を構成するために使われます。 ファイルを作成するには、次のコマンドを使います。

    ```bash
    nano consumer.properties
    ```

    `consumer.properties` ファイルの内容として、次のテキストを使用します。

    ```yaml
    zookeeper.connect=PRIMARY_ZKHOSTS
    group.id=mirrorgroup
    ```

    **PRIMARY_ZKHOSTS** は、**プライマリ** クラスターの Zookeeper の IP アドレスで置き換えます。

    このファイルには、プライマリ Kafka クラスターからの読み取りに使用するコンシューマー情報を記述します。 コンシューマーの構成の詳細については、「[Consumer Configs (コンシューマーの構成)](https://kafka.apache.org/documentation#consumerconfigs)」(kafka.apache.org) を参照してください。

    ファイルを保存するには、**Ctrl + X** キー、**Y** キー、**Enter** キーの順に押します。

3. セカンダリ クラスターと通信するプロデューサーを構成する前に、**セカンダリ** クラスターのブローカーの IP アドレスに使用する変数を設定します。 以下のコマンドを使用して、この変数を作成します。

    ```bash
    export SECONDARY_BROKERHOSTS='BROKER_IP_ADDRESS1:9092,BROKER_IP_ADDRESS2:9092,BROKER_IP_ADDRESS2:9092'
    ```

    コマンド `echo $SECONDARY_BROKERHOSTS` は、次のテキストのような情報を返します。

    `10.23.0.14:9092,10.23.0.4:9092,10.23.0.12:9092`

4. `producer.properties` ファイルは、**セカンダリ** クラスターとの通信に使われます。 ファイルを作成するには、次のコマンドを使います。

    ```bash
    nano producer.properties
    ```

    `producer.properties` ファイルの内容として、次のテキストを使用します。

    ```yaml
    bootstrap.servers=SECONDARY_BROKERHOSTS
    compression.type=none
    ```

    前の手順で使用したブローカーの IP アドレスで **SECONDARY_BROKERHOSTS** を置き換えます。

    プロデューサーの構成の詳細については、「[Producer Configs (プロデューサーの構成)](https://kafka.apache.org/documentation#producerconfigs)」(kafka.apache.org) を参照してください。

5. セカンダリ クラスターの Zookeeper ホストの IP アドレスを格納する環境変数を作成するには、次のコマンドを使用します。

    ```bash
    # get the zookeeper hosts for the secondary cluster
    export SECONDARY_ZKHOSTS='ZOOKEEPER_IP_ADDRESS1:2181,ZOOKEEPER_IP_ADDRESS2:2181,ZOOKEEPER_IP_ADDRESS3:2181'
    ```

7. HDInsight の Kafka に使用される既定の構成では、トピックの自動作成が許可されません。 ミラーリング プロセスを開始する前に、次のいずれかの方法を選択する必要があります。

    * **セカンダリ クラスターにトピックを作成する**:この方法を選択した場合、パーティション数とレプリケーション係数も設定することができます。

        次のコマンドを使用して事前にトピックを作成することができます。

        ```bash
        /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --create --replication-factor 2 --partitions 8 --topic testtopic --zookeeper $SECONDARY_ZKHOSTS
        ```

        `testtopic` は、作成するトピックの名前に置き換えます。

    * **トピックを自動的に作成するようにクラスターを構成する**:この方法を選択した場合、MirrorMaker によって自動的にトピックが作成されます。ただし、トピックと共に作成されるパーティションの数またはレプリケーション係数は、プライマリのトピックと異なる場合があります。

        トピックを自動的に作成するようにセカンダリ クラスターを構成するには、次の手順を実行します。

        1. `https://SECONDARYCLUSTERNAME.azurehdinsight.net` からセカンダリ クラスターの Ambari ダッシュボードに移動します。
        1. **[サービス]**  >  **[Kafka]** をクリックします。 **[Configs]** タブをクリックします。
        5. __[フィルター]__ フィールドに `auto.create` の値を入力します。 プロパティの一覧にフィルターが適用されて `auto.create.topics.enable` 設定が表示されます。
        6. `auto.create.topics.enable` の値を true に変更して __[保存]__ を選択します。 ノートを追加して、もう一度 __[保存]__ を選択します。
        7. __Kafka__ サービスを選択し、 __[Restart]\(再起動\)__ を選択して、 __[Restart all affected]\(影響を受けるものをすべて再起動\)__ を選択します。 メッセージが表示されたら、 __[Confirm restart all]\(すべて再起動\)__ を選択します。

        ![トピックの自動作成の構成](./media/apache-kafka-mirroring/kafka-enable-auto-create-topics.png)

## <a name="start-mirrormaker"></a>MirrorMaker の開始

1. **セカンダリ** クラスターに SSH で接続してから、次のコマンドを使用して MirrorMaker プロセスを開始します。

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-run-class.sh kafka.tools.MirrorMaker --consumer.config consumer.properties --producer.config producer.properties --whitelist testtopic --num.streams 4
    ```

    この例で使用するパラメーターは次のとおりです。

    * **--consumer.config**:コンシューマーのプロパティを格納するファイルを指定します。 これらのプロパティは、*プライマリ* Kafka クラスターから読み取りを行うコンシューマーの作成に使用します。

    * **--producer.config**:プロデューサーのプロパティを格納するファイルを指定します。 これらのプロパティは、*セカンダリ* Kafka クラスターへの書き込みを行うプロデューサーの作成に使用します。

    * **--whitelist**:MirrorMaker がプライマリ クラスターからセカンダリ クラスターにレプリケートするトピックの一覧。

    * **--num.streams**:作成するコンシューマー スレッドの数。

    現在、セカンダリ ノードのコンシューマーは、メッセージの受信を待機しています。

2. **プライマリ** クラスターに SSH で接続してから、次のコマンドを使用して、プロデューサーを起動し、トピックにメッセージを送信します。

    ```bash
    export PRIMARY_BROKERHOSTS=BROKER_IP_ADDRESS1:9092,BROKER_IP_ADDRESS2:9092,BROKER_IP_ADDRESS2:9092
    /usr/hdp/current/kafka-broker/bin/kafka-console-producer.sh --broker-list $SOURCE_BROKERHOSTS --topic testtopic
    ```

     カーソル付きの空白行が表示されたら、テキスト メッセージを数個入力します。 これらのメッセージは、**プライマリ** クラスター上のトピックに送信されます。 操作が完了したら、**Ctrl + C** キーを使用してプロデューサーのプロセスを終了します。

3. **セカンダリ** クラスターに SSH で接続してから、**Ctrl + C** キーを使用して MirrorMaker プロセスを終了します。 このプロセスは、終了までに数秒かかる場合があります。 メッセージがセカンダリにレプリケートされたことを確認するには、次のコマンドを使います。

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-console-consumer.sh --bootstrap-server $SECONDARY_ZKHOSTS --topic testtopic --from-beginning
    ```

    これで、MirrorMaster がトピックをプライマリ クラスターからセカンダリにミラーリングしたときに作成された `testtopic` が、トピックの一覧に含まれるようになりました。 このトピックから取得するメッセージは、プライマリ クラスターで入力したものと同じです。

## <a name="delete-the-cluster"></a>クラスターを削除する

[!INCLUDE [delete-cluster-warning](../../../includes/hdinsight-delete-cluster-warning.md)]

このドキュメントの手順では、別の Azure リソース グループにクラスターを作成しました。 作成されたすべてのリソースを削除するには、作成した 2 つのリソース グループ **kafka-primary-rg** および **kafka-secondary_rg** を削除します。 リソース グループを削除すると、このドキュメントに従って作成したすべてのリソースが削除されます。これには、クラスター、仮想ネットワーク、ストレージ アカウントなどが含まれます。

## <a name="next-steps"></a>次の手順

このドキュメントでは、[MirrorMaker](https://cwiki.apache.org/confluence/pages/viewpage.action?pageId=27846330) を使用して [Apache Kafka](https://kafka.apache.org/) クラスターのレプリカを作成する方法について説明しました。 次のリンクを使用することで、Kafka のその他の活用方法を知ることができます。

* [Apache Kafka MirrorMaker に関するドキュメント](https://cwiki.apache.org/confluence/pages/viewpage.action?pageId=27846330) (cwiki.apache.org)
* [HDInsight での Apache Kafka の使用](apache-kafka-get-started.md)
* [HDInsight 上の Apache Kafka で Apache Spark を使用する](../hdinsight-apache-spark-with-kafka.md)
* [HDInsight 上の Apache Kafka で Apache Storm を使用する](../hdinsight-apache-storm-with-kafka.md)
* [Azure Virtual Network 経由で Apache Kafka に接続する](apache-kafka-connect-vpn-gateway.md)
