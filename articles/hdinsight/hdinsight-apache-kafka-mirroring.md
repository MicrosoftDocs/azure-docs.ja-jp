---
title: "HDInsight クラスターでの Apache Kafka のミラー化 | Microsoft Docs"
description: "Kafka のミラーリング機能を使用してセカンダリ クラスターにトピックをミラーリングすることにより、HDInsight クラスターに Kafka のレプリカを保持する方法について説明します。"
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
ms.assetid: 015d276e-f678-4f2b-9572-75553c56625b
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 02/13/2017
ms.author: larryfr
translationtype: Human Translation
ms.sourcegitcommit: 4f2230ea0cc5b3e258a1a26a39e99433b04ffe18
ms.openlocfilehash: 281901045723266128db9069a6f244acb183ae80
ms.lasthandoff: 03/25/2017

---
# <a name="use-mirrormaker-to-create-a-replica-of-a-kafka-on-hdinsight-cluster-preview"></a>MirrorMaker を使用した HDInsight クラスターでの Kafka のレプリカの作成 (プレビュー)

Apache Kafka にはミラーリング機能が含まれているため、1 つの Kafka クラスターから別の Kafka クラスターにトピックをレプリケートすることができます。 たとえば、異なる Azure リージョンの Kafka クラスター間でレコードをレプリケートできます。

ミラーリングは、継続的なプロセスとして実行できるほか、1 つのクラスターから別のクラスターにデータを移行する方法として断続的に使用することもできます。

> [!WARNING]
> ミラーリングは、フォールト トレランスを実現するための手段として考慮するべきではありません。 トピック内の項目へのオフセットは、移行元クラスターと移行先クラスターによって異なるため、クライアントはこれら 2 つを入れ替えて使用することはできません。
> 
> フォールト トレランスを考慮する場合は、クラスター内のトピックをレプリケートする設定にします。 詳細については、「[Get started with Kafka on HDInsight (HDInsight での Kafka の使用)](hdinsight-apache-kafka-get-started.md)」を参照してください。

## <a name="prerequisites"></a>前提条件

* Azure Virtual Network: 移行元および移行先の Kafka クラスターは、相互に直接通信できる必要があります。 HDInsight では Kafka API をインターネット上で公開していないため、移行元および移行先のクラスターは、同じ Azure Virtual Network に存在する必要があります。

* 2 つの Kafka クラスター: このドキュメントでは、Azure Resource Manager テンプレートを使用して、Azure Virtual Network 内の HDInsight クラスターに 2 つの Kafka を作成します。

## <a name="how-does-mirroring-work"></a>ミラーリングのしくみ

ミラーリングでは、Apache Kafka に含まれるツール MirrorMaker によって移行元クラスターのトピックからレコードが使用され、移行元クラスターにローカル コピーが作成されます。 MirrorMaker では、移行元クラスターから読み取りを行う 1 つ (あるいは複数) の*コンシューマー*と、ローカル (移行先) クラスターへの書き込みを行う*プロデューサー*を使用します。

次の図にミラーリング プロセスを示します。

![ミラーリング プロセスの図](./media/hdinsight-apache-kafka-mirroring/kafka-mirroring.png)

移行元クラスターと移行先クラスターでは、ノードとパーティションの数、およびトピック内のオフセットが異なります。 ミラーリングではパーティション分割に使用するキー値が保持されるため、レコードの順序はキー単位で保存されます。

### <a name="mirroring-between-networks"></a>ネットワーク間のミラーリング

異なるネットワークの Kafka クラスター間でミラーリングの必要がある場合には、次の追加の考慮事項があります。

* **ゲートウェイ**: ネットワークは、TCPIP レベルで通信できる必要があります。

* **名前の解決**: 各ネットワークの Kafka クラスターは、ホスト名を使用して相互に接続できる必要があります。 そのためには、要求を他のネットワークに転送するように構成された各ネットワークに、ドメイン ネーム システム (DNS) サーバーが必要です。 
  
    Azure Virtual Network を作成するときに、ネットワークで自動的に提供される DNS を使用せず、カスタムの DNS サーバーおよびサーバーの IP アドレスを指定する必要があります。 Virtual Network の作成が完了したら、その IP アドレスを使用する Azure Virtual Machine を作成し、そこに DNS ソフトウェアをインストールして構成を行います。
  
    > [!WARNING]
    > カスタム DNS サーバーの作成と構成は、HDInsight を Virtual Network にインストールする前に行うようにします。 HDInsight が Virtual Network 用に構成された DNS サーバーを使用するために必要な、追加の構成はありません。

2 つの Azure Virtual Network を接続する方法の詳細については、[VNet と VNet 間の接続の構成](../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md)に関する記事を参照してください。

## <a name="create-kafka-clusters"></a>Kafka クラスターの作成

HDInsight の Apache Kafka では、パブリック インターネットを介した Kafka サービスへのアクセスを提供されていません。 Kafka と通信するすべてのものは、Kafka クラスター内のノードと同じ Azure 仮想ネットワークに存在している必要があります。 この例では、Kafka の移行元クラスターと移行先クラスターの両方を Azure 仮想ネットワーク内に配置します。 次の図に、クラスター間の通信フローを示します。

![Azure 仮想ネットワークにおける、移行元および移行先の Kafka クラスターの図](./media/hdinsight-apache-kafka-mirroring/spark-kafka-vnet.png)

> [!NOTE]
> Kafka 自体は仮想ネットワーク内の通信に制限されていますが、クラスターの SSH や Ambari などの他のサービスにはインターネット経由でアクセスすることができます。 HDInsight で使用できるパブリック ポートの詳細については、「[HDInsight で使用されるポートと URI](hdinsight-hadoop-port-settings-for-services.md)」を参照してください。

Azure 仮想ネットワークと Kafka クラスターは手動で作成できますが、Azure Resource Manager テンプレートを使用する方が簡単です。 次の手順に従って、Azure 仮想ネットワークと 2 つの Kafka クラスターを Azure サブスクリプションにデプロイします。

1. 次のボタンを使用して Azure にサインインし、Azure Portal でテンプレートを開きます。
   
    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fhditutorialdata.blob.core.windows.net%2Farmtemplates%2Fcreate-linux-based-kafka-mirror-cluster-in-vnet.json" target="_blank"><img src="./media/hdinsight-apache-kafka-mirroring/deploy-to-azure.png" alt="Deploy to Azure"></a>
   
    Azure Resource Manager テンプレートは **https://hditutorialdata.blob.core.windows.net/armtemplates/create-linux-based-kafka-mirror-cluster-in-vnet.json** にあります。

2. 次の情報に従って、**[カスタム デプロイ]** ブレードの各エントリに入力します。
    
    ![HDInsight のカスタム デプロイ](./media/hdinsight-apache-kafka-mirroring/parameters.png)
    
    * **[リソース グループ]**: グループを作成するか、または既存のグループを選択します。 このグループに HDInsight クラスターが含まれます。

    * **[場所]**: 地理的に近い場所を選択します。 この場所は、__[設定]__ セクションの場所と一致する必要があります。
     
    * **[Base Cluster Name] \(ベース クラスター名)**: この値は、Kafka クラスターのベース名として使用されます。 たとえば、「**hdi**」と入力すると、**source-hdi** と、**dest-hdi** という名前のクラスターが作成されます。

    * **[Cluster Login User Name] \(クラスター ログイン ユーザー名)**: 移行元および移行先の Kafka クラスターの管理者のユーザー名。

    * **[クラスター ログイン パスワード]**: 移行元および移行先の Kafka クラスターの管理者のユーザー パスワード。

    * **[SSH ユーザー名]**: 移行元および移行先の Kafka クラスターの作成に使用する SSHユーザー。

    * **[SSH パスワード]**: 移行元および移行先の Kafka クラスター用の SSH ユーザーのパスワード。

    * **[場所]**: クラスターが作成されるリージョン。

3. **使用条件**を読み、**[上記の使用条件に同意する]** をオンにします。

4. 最後に、**[ダッシュボードにピン留めする]** をオンにし、**[購入]** をクリックします。 クラスターの作成には約 20 分かかります。

リソースが作成されると、クラスターと Web ダッシュボードが含まれているリソース グループのブレードにリダイレクトされます。

![Resource group blade for the vnet and clusters](./media/hdinsight-apache-kafka-mirroring/groupblade.png)

> [!IMPORTANT]
> 各 HDInsight クラスターの名前が **source-BASENAME** と **dest-BASENAME** であることに注目してください。BASENAME はテンプレートで指定した名前です。 これらの名前は、後の手順でクラスターに接続するときに使用します。

## <a name="create-topics"></a>トピックの作成

1. SSH を使用して**移行元**クラスターに接続します。
   
        ssh sshuser@source-BASENAME-ssh.azurehdinsight.net
   
    **sshuser** は、クラスターの作成時に使用した SSH ユーザー名に置き換えます。 **BASENAME** は、クラスターの作成時に使用したベース名に置き換えます。
   
    詳細については、[HDInsight での SSH の使用](hdinsight-hadoop-linux-use-ssh-unix.md)に関するページを参照してください。

2. 次のコマンドを使用して Zookeeper ホストを検索し、`SOURCE_ZKHOSTS` 変数を設定して、`testtopic` という名前の新しいトピックをいくつか作成します。
   
    ```bash
    SOURCE_ZKHOSTS=`grep -R zk /etc/hadoop/conf/yarn-site.xml | grep 2181 | grep -oPm1 "(?<=<value>)[^<]+"`
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
   
       zk0-source.aazwc2onlofevkbof0cuixrp5h.gx.internal.cloudapp.net:2181,zk1-source.aazwc2onlofevkbof0cuixrp5h.gx.internal.cloudapp.net:2181,zk6-source.aazwc2onlofevkbof0cuixrp5h.gx.internal.cloudapp.net:2181
   
 この情報は保存してください。 次のセクションで使用します。

## <a name="configure-mirroring"></a>ミラーリングの構成

1. 別の SSH セッションを使用して**移行先**クラスターに接続します。
   
        ssh sshuser@dest-BASENAME-ssh.azurehdinsight.net
   
    **sshuser** は、クラスターの作成時に使用した SSH ユーザー名に置き換えます。 **BASENAME** は、クラスターの作成時に使用したベース名に置き換えます。
   
    詳細については、[HDInsight での SSH の使用](hdinsight-hadoop-linux-use-ssh-unix.md)に関するページを参照してください。

2. 次のコマンドを使用して、**移行元**クラスターとの通信方法を説明する `consumer.properties` ファイルを作成します。
   
    ```bash
    nano consumer.properties
    ```
   
    `consumer.properties` ファイルの内容として、次のテキストを使用します。
   
        zookeeper.connect=SOURCE_ZKHOSTS
        group.id=mirrorgroup
   
    **SOURCE_ZKHOSTS** を、**移行元**クラスターからの Zookeeper ホストの情報に置き換えます。
   
    このファイルでは、移行元の Kafka クラスターからの読み取りに使用するコンシューマー情報について説明します。 コンシューマーの構成の詳細については、「[Consumer Configs (コンシューマーの構成)](https://kafka.apache.org/documentation#consumerconfigs)」(kafka.apache.org) を参照してください。
   
    **Ctrl + X** キーを押した後、**Y** キーと Enter キーを押してファイルを保存します。

3. 移行先のクラスターと通信するプロデューサーを構成する前に、**移行先**クラスターのブローカー ホストを検索する必要があります。 この情報の取得には、次のコマンドを使用します。
   
    ```bash
    sudo apt -y install jq
    DEST_BROKERHOSTS=`sudo bash -c 'ls /var/lib/ambari-agent/data/command-[0-9]*.json' | tail -n 1 | xargs sudo cat | jq -r '["\(.clusterHostInfo.kafka_broker_hosts[]):9092"] | join(",")'`
    echo $DEST_BROKERHOSTS
    ```
   
    これらのコマンドでは、次のような情報が返されます。
   
        wn0-dest.aazwc2onlofevkbof0cuixrp5h.gx.internal.cloudapp.net:9092,wn1-dest.aazwc2onlofevkbof0cuixrp5h.gx.internal.cloudapp.net:9092

4. 次を使用して、**移行先**クラスターとの通信方法を説明する `producer.properties` ファイルを作成します。

    ```bash
    nano producer.properties
    ```

    `producer.properties` ファイルの内容として、次のテキストを使用します。
   
        bootstrap.servers=DEST_BROKERS
        compression.type=none
   
    **DEST_BROKERS** を、前の手順で取得したブローカー情報に置き換えます。 
   
    プロデューサーの構成の詳細については、「[Producer Configs (プロデューサーの構成)](https://kafka.apache.org/documentation#producerconfigs)」(kafka.apache.org) を参照してください。

## <a name="start-mirrormaker"></a>MirrorMaker の開始

1. **移行先**クラスターに SSH で接続してから、次のコマンドを使用して MirrorMaker プロセスを開始します。
   
    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-run-class.sh kafka.tools.MirrorMaker --consumer.config consumer.properties --producer.config producer.properties --whitelist testtopic --num.streams 4
    ```
   
    この例で使用するパラメーターは次のとおりです。
   
    * **--consumer.config**: コンシューマーのプロパティを格納するファイルを指定します。 これらのプロパティは、*移行元*の Kafka クラスターから読み取りを行うコンシューマーの作成に使用します。

    * **--producer.config**: プロデューサーのプロパティを格納するファイルを指定します。 これらのプロパティは、*移行先*の Kafka クラスターへの書き込みを行うプロデューサーの作成に使用します。

    * **--whitelist**: MirrorMaker が移行元クラスターから移行先クラスターにレプリケートするトピックの一覧。
    
    * **--num.streams**: 作成するコンシューマー スレッドの数。
     
    スタートアップ時に、MirrorMaker により次のテキストのような情報が返されます。
        
    ```{metadata.broker.list=wn1-source.aazwc2onlofevkbof0cuixrp5h.gx.internal.cloudapp.net:9092,wn0-source.aazwc2onlofevkbof0cuixrp5h.gx.internal.cloudapp.net:9092, request.timeout.ms=30000, client.id=mirror-group-3, security.protocol=PLAINTEXT}{metadata.broker.list=wn1-source.aazwc2onlofevkbof0cuixrp5h.gx.internal.cloudapp.net:9092,wn0-source.aazwc2onlofevkbof0cuixrp5h.gx.internal.cloudapp.net:9092, request.timeout.ms=30000, client.id=mirror-group-0, security.protocol=PLAINTEXT}  metadata.broker.list=wn1-source.aazwc2onlofevkbof0cuixrp5h.gx.internal.cloudapp.net:9092,wn0-kafka.aazwc2onlofevkbof0cuixrp5h.gx.internal.cloudapp.net:9092, request.timeout.ms=30000, client.id=mirror-group-2, security.protocol=PLAINTEXT}  metadata.broker.list=wn1-source.aazwc2onlofevkbof0cuixrp5h.gx.internal.cloudapp.net:9092,wn0-source.aazwc2onlofevkbof0cuixrp5h.gx.internal.cloudapp.net:9092, request.timeout.ms=30000, client.id=mirror-group-1, security.protocol=PLAINTEXT}
    ```

2. From the SSH connection to the **source** cluster, use the following command to start a producer and send messages to the topic:
    
    ```bash
    sudo apt -y install jq
    SOURCE_BROKERHOSTS=`sudo bash -c 'ls /var/lib/ambari-agent/data/command-[0-9]*.json' | tail -n 1 | xargs sudo cat | jq -r '["\(.clusterHostInfo.kafka_broker_hosts[]):9092"] | join(",")'`
    /usr/hdp/current/kafka-broker/bin/kafka-console-producer.sh --broker-list $SOURCE_BROKERHOSTS --topic testtopic
    ```

 カーソル付きの空白行が表示されたら、テキスト メッセージを数個入力します。 これらのメッセージは、**移行元**クラスター上のトピックに送信されます。 操作が完了したら、**Ctrl + C** キーを使用してプロデューサーのプロセスを終了します。

3. **移行先**クラスターに SSH で接続してから、**Ctrl + C** キーを使用して MirrorMaker プロセスを終了します。 その後、次のコマンドを使用して、`testtopic` トピックが生成されたこと、およびトピック内のデータがこのミラーにレプリケートされたことを確認します。
    
    ```bash
    DEST_ZKHOSTS=`grep -R zk /etc/hadoop/conf/yarn-site.xml | grep 2181 | grep -oPm1 "(?<=<value>)[^<]+"`
    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --list --zookeeper $DEST_ZKHOSTS
    /usr/hdp/current/kafka-broker/bin/kafka-console-consumer.sh --zookeeper $DEST_ZKHOSTS --topic testtopic --from-beginning
    ```
    
  これで、MirrorMaster がトピックを移行元のクラスターから移行先にミラーリングしたときに作成された `testtopic` が、トピックの一覧に含まれるようになりました。 このトピックから取得するメッセージは、送信元クラスターで入力したものと同じです。

## <a name="delete-the-cluster"></a>クラスターを削除する

[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

このドキュメントの手順では両方のクラスターを同じ Azure リソース グループに作成したため、Azure Portal でこのリソース グループを削除するだけで済みます。 リソース グループを削除すると、このドキュメントに従って作成したすべてのリソース、Azure Virtual Network、クラスターで使用したストレージ アカウントが削除されます。

## <a name="next-steps"></a>次のステップ

このドキュメントでは、MirrorMaker を使用して Kafka クラスターのレプリカを作成する方法について説明しました。 次のリンクを使用することで、Kafka のその他の活用方法を知ることができます。

* [Apache Kafka MirrorMaker に関するドキュメント](https://cwiki.apache.org/confluence/pages/viewpage.action?pageId=27846330) (cwiki.apache.org)
* [HDInsight での Apache Kafka の使用](hdinsight-apache-kafka-get-started.md)
* [HDInsight での Kafka に Apache Spark を使用する](hdinsight-apache-spark-with-kafka.md)
* [HDInsight での Kafka に Apache Storm を使用する](hdinsight-apache-storm-with-kafka.md)


