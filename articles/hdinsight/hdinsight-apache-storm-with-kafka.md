---
title: "HDInsight での Strom に Apache Kafka を使用する | Microsoft Docs"
description: "HDInsight での Apache Storm には、Apache Kafka がインストールされています。 Storm に付属する KafkaBolt コンポーネントと KafkaSpout コンポーネントを使用して、Kafka への書き込みおよび読み取りを行う方法について説明します。 また、Flux フレームワークを使用して Storm トポロジを定義し送信する方法についても説明します。"
services: hdinsight
documentationcenter: 
author: Blackmist
manager: paulettm
editor: cgronlun
ms.assetid: e4941329-1580-4cd8-b82e-a2258802c1a7
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: java
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 03/20/2017
ms.author: larryfr
translationtype: Human Translation
ms.sourcegitcommit: 4f2230ea0cc5b3e258a1a26a39e99433b04ffe18
ms.openlocfilehash: dcda5e27cbcadff054c8085b72a1b6fb1c07b889
ms.lasthandoff: 03/25/2017

---
# <a name="use-apache-kafka-preview-with-storm-on-hdinsight"></a>HDInsight での Strom に Apache Kafka (プレビュー) を使用する

Apache Kafka は、HDInsight で利用可能な発行/サブスクライブ メッセージング ソリューションです。 Apache Storm は、リアルタイムでのデータ分析に使用可能な分散システムです。 このドキュメントでは、HDInsight の Storm を使用して、HDInsight の Kafka からデータを読み取って処理する方法を示します。 このドキュメントのサンプルでは、Apache Storm で利用可能な Kafka のスパウト コンポーネントとボルト コンポーネントに依存した Java ベースの Strom トポロジを使用します。

> [!NOTE]
> このドキュメントの手順では、HDInsight の Storm クラスターと HDInsight の Kafka クラスターの両方を含む Azure リソース グループを作成します。 これらのクラスターは両方とも、Strom クラスターが Kafka クラスターと直接通信できるように、Azure 仮想ネットワーク内に配置します。
> 
> このドキュメントの手順を完了したら、余分に課金されないようにするためにクラスターは削除してください。

## <a name="prerequisites"></a>前提条件

* [Java JDK](http://www.oracle.com/technetwork/java/javase/downloads/index.html) 1.8 以上。 または同等の [OpenJDK](http://openjdk.java.net/) など。
  
    > [!NOTE]
    > このドキュメントでは、Java 8 を使用する HDInsight 3.5 クラスターを使います。

* [Maven 3.x](http://maven.apache.org/) - Java アプリケーション用のビルド管理パッケージです。

* テキスト エディターまたは Java IDE

* SSH クライアント (`ssh` と `scp` コマンドが必要です) - 詳細については、[HDInsight での SSH の使用](hdinsight-hadoop-linux-use-ssh-unix.md)に関するページをご覧ください。

## <a name="create-the-clusters"></a>クラスターの作成

HDInsight の Apache Kafka では、パブリック インターネットを介した Kafka ブローカーへのアクセスは提供されていません。 Kafka と通信するすべてのものは、Kafka クラスター内のノードと同じ Azure 仮想ネットワークに存在している必要があります。 この例では、Kafka クラスターと Storm クラスターの両方を Azure 仮想ネットワーク内に配置します。 次の図に、クラスター間の通信フローを示します。

![Azure 仮想ネットワークにおける Strom クラスターと Kafka クラスターの図](./media/hdinsight-apache-storm-with-kafka/storm-kafka-vnet.png)

> [!NOTE]
> Kafka 自体は仮想ネットワーク内の通信に制限されていますが、クラスターの SSH や Ambari などの他のサービスにはインターネット経由でアクセスすることができます。 HDInsight で使用できるパブリック ポートの詳細については、「[HDInsight で使用されるポートと URI](hdinsight-hadoop-port-settings-for-services.md)」を参照してください。


Azure 仮想ネットワーク、Kafka、および Storm クラスターは手動で作成できますが、Azure Resource Manager テンプレートを使用する方が簡単です。 次の手順に従って、Azure 仮想ネットワーク、Kafka クラスター、および Storm クラスターを Azure サブスクリプションにデプロイします。

1. 次のボタンを使用して Azure にサインインし、Azure Portal でテンプレートを開きます。
   
    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fhditutorialdata.blob.core.windows.net%2Farmtemplates%2Fcreate-linux-based-kafka-storm-cluster-in-vnet.json" target="_blank"><img src="./media/hdinsight-apache-storm-with-kafka/deploy-to-azure.png" alt="Deploy to Azure"></a>
   
    Azure Resource Manager テンプレートは、**https://hditutorialdata.blob.core.windows.net/armtemplates/create-linux-based-kafka-storm-cluster-in-vnet.json** にあります。

2. 次のガイダンスに従って、**[カスタム デプロイ]** ブレードの各エントリに入力します。
   
    ![HDInsight のカスタム デプロイ](./media/hdinsight-apache-storm-with-kafka/parameters.png)

    * **[リソース グループ]**: グループを作成するか、または既存のグループを選択します。 このグループに HDInsight クラスターが含まれます。
   
    * **[場所]**: 地理的に近い場所を選択します。 この場所は、__[設定]__ セクションの場所と一致する必要があります。

    * **[Base Cluster Name] \(ベース クラスター名)**: この値は、Strom クラスターと Kafka クラスターのベース名として使用されます。 たとえば、「**hdi**」と入力すると、**storm-hdi** という名前の Storm クラスターと、**kafka-hdi** という名前の Kafka クラスターが作成されます。
   
    * **[Cluster Login User Name] \(クラスター ログイン ユーザー名)**: Storm クラスターと Kafka クラスターの管理者のユーザー名。
   
    * **[クラスター ログイン パスワード]**: Storm クラスターと Kafka クラスターの管理者のユーザー パスワード。
    
    * **[SSH ユーザー名]**: Storm クラスターおよび Kafka クラスターの作成に使用する SSHユーザー。
    
    * **[SSH パスワード]**: Storm クラスターおよび Kafka クラスター用の SSH ユーザーのパスワード。
    
    * **[場所]**: クラスターが作成されるリージョン。

3. **使用条件**を読み、**[上記の使用条件に同意する]** をオンにします。

4. 最後に、**[ダッシュボードにピン留めする]** をオンにし、**[購入]** をクリックします。 クラスターの作成には約 20 分かかります。

リソースが作成されると、クラスターと Web ダッシュボードが含まれているリソース グループのブレードにリダイレクトされます。

![Resource group blade for the vnet and clusters](./media/hdinsight-apache-storm-with-kafka/groupblade.png)

> [!IMPORTANT]
> 各 HDInsight クラスターの名前が **storm-BASENAME** および **kafka-BASENAME** であることに注目してください。BASENAME はテンプレートで指定した名前です。 これらの名前は、後の手順でクラスターに接続するときに使用します。

## <a name="get-the-code"></a>コードの入手

このドキュメントで説明する例で使用するコードは、[https://github.com/Azure-Samples/hdinsight-storm-java-kafka](https://github.com/Azure-Samples/hdinsight-storm-java-kafka) で入手できます。

## <a name="understanding-the-code"></a>コードについて

このプロジェクトには、2 つのトポロジが含まれています。

* **KafkaWriter**: このトポロジは **writer.yaml** で定義されており、Apache Storm に付属の KafkaBolt を使用して Kafka にランダムな文を書き込みます。
  
    このトポロジでは、ランダムな文の生成に **SentenceSpout** コンポーネントを使用します。

* **KafkaReader**: このトポロジは **reader.yaml** ファイルで定義されており、Apache Strom に付属の KafkaSpout を使用して Kafka のデータを読み取り標準出力に記録します。
  
    このトポロジでは、Kafka から読み取ったデータのログ記録に **PrinterBolt** コンポーネントを使用します。

### <a name="flux"></a>Flux

これらのトポロジは、[Flux](https://storm.apache.org/releases/1.0.1/flux.html) で定義されています。 Flux は Storm 0.10.x で導入されており、これによりトポロジ構成とコードを切り離すことができます。 Flux フレームワークを使用するトポロジの場合、YAML ファイルでトポロジを定義します。 YAML ファイルは、トポロジの一部に含めることも、Strom サーバーへのトポロジの送信時に指定することもできます。 Flux では実行時の変数代入もサポートされており、以下の例ではこれを使用しています。

両方のトポロジには、次の環境変数が必要です。

* **KAFKATOPIC**: トポロジの読み取り/書き込みの対象になる Kafka トピックの名前。

* **KAFKABROKERS**: Kafka ブローカーが実行されるホスト。 ブローカー情報は、KafkaBolt が Kafka への書き込み時に使用します。

* **KAFKAZKHOSTS**: Zookeeper が実行されるホスト。

これらの環境変数の設定方法は、このドキュメントの各手順で示します。

## <a name="create-a-kafka-topic"></a>Kafka トピックの作成

1. SSH を使用して Kafka クラスターに接続します。 `USERNAME` は、クラスターの作成時に使用した SSH ユーザー名に置き換えます。 `BASENAME` は、クラスターの作成時に使用したベース名に置き換えます。
   
        ssh USERNAME@kafka-BASENAME-ssh.azurehdinsight.net
   
    メッセージが表示されたら、クラスターの作成時に使用したパスワードを入力します。
   
    詳細については、[HDInsight での SSH の使用](hdinsight-hadoop-linux-use-ssh-unix.md)に関するページを参照してください。

2. Kafka クラスターに SSH で接続した状態で、次のコマンドを使用して HTTP ログインとクラスター名に変数を設定します。 これらの値は、このセクションの他の手順で使用します。

  ```bash
  ADMIN='admin' #replace with the name of the admin account for the cluster
  PASSWORD='password' #replace with the password for the admin account
  ```

3. 次のコマンドを使用して、`jq` ユーティリティをインストールし、クラスター名を取得して、`KAFKAZKHOSTS` 変数を設定します。

  ```bash
  sudo apt -y install jq
  CLUSTERNAME=`curl -u $ADMIN:$PASSWORD -G "http://headnodehost:8080/api/v1/clusters" | jq -r '.items[].Clusters.cluster_name'`
  KAFKAZKHOSTS=`curl -u $ADMIN:$PASSWORD -G "http://headnodehost:8080/api/v1/clusters/$CLUSTERNAME/services/ZOOKEEPER/components/ZOOKEEPER_SERVER" | jq -r '["\(.host_components[].HostRoles.host_name):2181"] | join(",")'`
  ```

    次のコマンドを使用して、クラスター名を取得します。

  ```bash
  echo $CLUSTERNAME
  ```

    このコマンドの出力は次の例のようになります。

  ```bash
  kafka-myhdi
  ```

    `KAFKAZKHOSTS` が正しく設定されていることを確認するには、次のコマンドを使用します。

  ```bash
  echo $KAFKAZKHOSTS
  ```

    このコマンドの出力は次の例のようになります。

  ```bash
  zk0-kafka.eahjefxxp1netdbyklgqj5y1ud.ex.internal.cloudapp.net:2181,zk2-kafka.eahjefxxp1netdbyklgqj5y1ud.ex.internal.cloudapp.net:2181,zk3-kafka.eahjefxxp1netdbyklgqj5y1ud.ex.internal.cloudapp.net:2181
  ```

    Kafka クラスター名と Zookeeper ホスト情報を保存します。これらの値は、Storm クラスターでトポロジを起動するときに使用します。

    > [!NOTE]
    > 上記のコマンドでは、__http://headnodehost:8080/__ を使用して Ambari に直接接続しています。 前記の情報をクラスターの外部からインターネット経由で取得する必要がある場合は、代わりに __https://kafka-BASENAME.azurehdinsight.net/__ を使用してください。

4. 次のコマンドを使用して、Kafka にトピックを作成します。

  ```bash
  /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --create --replication-factor 2 --partitions 8 --topic stormtest --zookeeper $KAFKAZKHOSTS
  ```

    このコマンドでは、`$KAFKAZKHOSTS` に格納されているホスト情報を使用して Zookeeper に接続してから、**stormtest** という名前の Kafka トピックを作成します。 トピックを一覧表示する次のコマンドを使用して、トピックが作成されたことを確認できます。

  ```bash
  /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --list --zookeeper $KAFKAZKHOSTS
  ```

    このコマンドの出力では Kafka トピックが一覧表示され、**stromtest** トピックも表示されます。

Kafka クラスターへの SSH 接続は、Storm トポロジでトピックへのメッセージの書き込みが行われていることを確認するために使用するので、アクティブなままにしてください。

## <a name="download-and-compile-the-project"></a>プロジェクトのダウンロードおよびコンパイル

1. 開発環境で、[https://github.com/Azure-Samples/hdinsight-storm-java-kafka](https://github.com/Azure-Samples/hdinsight-storm-java-kafka) からプロジェクトをダウンロードし、コマンドラインを開いてプロジェクトをダウンロードした場所に移動します。

    少し時間を取ってコードに目を通し、プロジェクトの仕組みを理解してください。

2. **hdinsight-storm-java-kafka** ディレクトリで次のコマンドを使用して、プロジェクトをコンパイルしデプロイ用のパッケージを作成します。

  ```bash
  mvn clean package
  ```

    このパッケージ処理では、`target` ディレクトリに `KafkaTopology-1.0-SNAPSHOT.jar` という名前のファイルが作成されます。

3. 次のコマンドを使用して、HDInsight の Storm クラスターにパッケージをコピーします。 **USERNAME** は、クラスターの SSH ユーザー名に置き換えます。 **BASENAME** は、クラスターの作成時に使用したベース名に置き換えます。

  ```bash
  scp ./target/KafkaTopology-1.0-SNAPSHOT.jar USERNAME@storm-BASENAME-ssh.azurehdinsight.net:KafkaTopology-1.0-SNAPSHOT.jar
  ```

    メッセージが表示されたら、クラスターの作成時に使用したパスワードを入力します。

4. 次のコマンドを使用して、`set-env-variables.sh` ファイルをプロジェクトの `scripts` ディレクトリから Storm クラスターにコピーします。

  ```bash
  scp ./scripts/set-env-variables.sh USERNAME@storm-BASENAME-ssh.azurehdinsight.net:set-env-variables.sh
  ```

    このスクリプトは、Kafka クラスターとの通信に Strom トポロジが用いる環境変数を設定するために使用します。

## <a name="start-the-writer"></a>ライターの起動

1. 次のコマンドを使用して、SSH で Storm クラスターに接続します。 **USERNAME** は、クラスターの作成時に使用した SSH ユーザー名に置き換えます。 **BASENAME** は、クラスターの作成時に使用したベース名に置き換えます。

  ```bash
  ssh USERNAME@storm-BASENAME-ssh.azurehdinsight.net
  ```

    メッセージが表示されたら、クラスターの作成時に使用したパスワードを入力します。
   
    詳細については、[HDInsight での SSH の使用](hdinsight-hadoop-linux-use-ssh-unix.md)に関するページを参照してください。

2. Storm クラスターに SSH で接続してから、次のコマンドを使用して `set-env-variables.sh` スクリプトを実行します。

  ```bash
  chmod +x set-env-variables.sh
  . ./set-env-variables.sh KAFKACLUSTERNAME PASSWORD
  ```

    __KAFKACLUSTERNAME__ は、Kafka クラスターの名前に置き換えます。 __PASSWORD__ は、Kafka クラスターの管理者のログイン パスワードに置き換えます。

    このスクリプトでは、Kafka クラスターに接続し、Kafka ブローカーと Zookeeper ホストの一覧を取得します。 取得した情報は、Strom トポロジが使用する環境変数に格納されます。

    スクリプトの出力は次の例のようになります。

        Checking for jq: install ok installed
        Exporting variables:
        $KAFKATOPIC=stormtest
        $KAFKABROKERS=wn0-storm.4rf4ncirvydube02fuj0gpxp4e.ex.internal.cloudapp.net:9092,wn1-storm.4rf4ncirvydube02fuj0gpxp4e.ex.internal.cloudapp.net:9092
        $KAFKAZKHOSTS=zk1-storm.4rf4ncirvydube02fuj0gpxp4e.ex.internal.cloudapp.net:2181,zk3-storm.4rf4ncirvydube02fuj0gpxp4e.ex.internal.cloudapp.net:2181,zk5-storm.4rf4ncirvydube02fuj0gpxp4e.ex.internal.cloudapp.net:2181

3. Storm クラスターに SSH で接続してから、次のコマンドを使用してライター トポロジを起動します。

        storm jar KafkaTopology-1.0-SNAPSHOT.jar org.apache.storm.flux.Flux --remote -R /writer.yaml -e

    このコマンドで使用されているパラメーターの意味は次のとおりです。

    * `org.apache.storm.flux.Flux`: Flux を使用してこのトポロジを構成および実行します。

    * `--remote`: トポロジを Nimbus に送信します。 トポロジは、クラスター内のワーカー ノード全体に配布されます。

    * `-R /writer.yaml`: `writer.yaml` ファイルを使用して、トポロジを構成します。 `-R` は、このリソースが jar ファイル内に含まれていることを指示しています。 リソースは jar のルートにあるため、`/writer.yaml` がリソースへのパスになります。

    * `-e`: 環境変数の代入を使用します。 Flux では、先ほど設定した $KAFKABROKERS と $KAFKATOPIC の値を取得し、reader.yaml ファイル内でこれらの値を `${ENV-KAFKABROKER}` エントリおよび `${ENV-KAFKATOPIC}` エントリの代わりとして使用します。

5. トポロジが起動したら、Kafka クラスターへの SSH 接続に切り替えて次のコマンドを使用し、**stormtest** トピックに書き込まれたメッセージを表示します。

  ```bash
  /usr/hdp/current/kafka-broker/bin/kafka-console-consumer.sh --zookeeper $KAFKAZKHOSTS --from-beginning --topic stormtest
  ```

    このコマンドでは、Kafka に付属のスクリプトを使用してトピックを監視します。 少ししてから、トピックに書き込まれたランダムな文が返され始めます。 出力は次の例のようになります。

        i am at two with nature             
        an apple a day keeps the doctor away
        snow white and the seven dwarfs     
        the cow jumped over the moon        
        an apple a day keeps the doctor away
        an apple a day keeps the doctor away
        the cow jumped over the moon        
        an apple a day keeps the doctor away
        an apple a day keeps the doctor away
        four score and seven years ago      
        snow white and the seven dwarfs     
        snow white and the seven dwarfs     
        i am at two with nature             
        an apple a day keeps the doctor away

    スクリプトを停止するには Ctrl+C キーを押します。

## <a name="start-the-reader"></a>リーダーの起動

1. Storm クラスターへの SSH セッションで、次のコマンドを使用してリーダー トポロジを起動します。

  ```bash
  storm jar KafkaTopology-1.0-SNAPSHOT.jar org.apache.storm.flux.Flux --remote -R /reader.yaml -e
  ```

2. トポロジが起動したら、Storm UI を開きます。 この Web UI は、https://storm-BASENAME.azurehdinsight.net/stormui にあります。 __BASENAME__ は、クラスターの作成時に使用したベース名に置き換えます。 

    メッセージが表示されたら、クラスターの作成時に使用した管理者のログイン名 (既定では `admin`) およびパスワードを入力します。 次の画像のような Web ページが表示されます。

    ![Storm UI](./media/hdinsight-apache-storm-with-kafka/stormui.png)

3. Storm UI で、__[Topology Summary] \(トポロジの概要)__ セクションの __[kafka-reader]__ リンクをクリックして、__kafka-reader__ トポロジの情報を表示します。

    ![Storm Web UI の [Topology Summary] \(トポロジの概要) セクション](./media/hdinsight-apache-storm-with-kafka/topology-summary.png)

4. __[Bolts (All time)] \(ボルト (常時))__ セクションの __[logger-bolt]__ リンクをクリックして、logger-bolt コンポーネントのインスタンスに関する情報を表示します。

    ![ボルト セクションの logger-bolt のリンク](./media/hdinsight-apache-storm-with-kafka/bolts.png)

5. __[Executors] \(エグゼキュータ)__ セクションで、__[Port] \(ポート)__ 列のリンクをクリックして、コンポーネントのこのインスタンスに関するログ情報を表示します。

    ![エグゼキュータのリンク](./media/hdinsight-apache-storm-with-kafka/executors.png)

    このログには、Kafka トピックから読み取られたデータのログが含まれています。 ログの情報は次のテキストのようなものです。

        2016-11-04 17:47:14.907 c.m.e.LoggerBolt [INFO] Received data: four score and seven years ago
        2016-11-04 17:47:14.907 STDIO [INFO] the cow jumped over the moon
        2016-11-04 17:47:14.908 c.m.e.LoggerBolt [INFO] Received data: the cow jumped over the moon
        2016-11-04 17:47:14.911 STDIO [INFO] snow white and the seven dwarfs
        2016-11-04 17:47:14.911 c.m.e.LoggerBolt [INFO] Received data: snow white and the seven dwarfs
        2016-11-04 17:47:14.932 STDIO [INFO] snow white and the seven dwarfs
        2016-11-04 17:47:14.932 c.m.e.LoggerBolt [INFO] Received data: snow white and the seven dwarfs
        2016-11-04 17:47:14.969 STDIO [INFO] an apple a day keeps the doctor away
        2016-11-04 17:47:14.970 c.m.e.LoggerBolt [INFO] Received data: an apple a day keeps the doctor away

## <a name="stop-the-topologies"></a>トポロジの停止

Storm クラスターへの SSH セッションで、次のコマンドを使用して Storm の各トポロジを停止します。

  ```bash
  storm kill kafka-writer
  storm kill kafka-reader
  ```

## <a name="delete-the-cluster"></a>クラスターを削除する

[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

このドキュメントの手順では両方のクラスターを同じ Azure リソース グループに作成したため、Azure Portal でこのリソース グループを削除するだけで済みます。 これにより、このドキュメントに従って作成したすべてのリソース、Azure 仮想ネットワーク、クラスターで使用したストレージ アカウントが削除されます。

## <a name="next-steps"></a>次のステップ

HDInsight 上の Storm で使用できるトポロジの例については、[Storm のトポロジおよびコンポーネントのサンプル](hdinsight-storm-example-topology.md)に関するページを参照してください。

Linux ベースの HDInsight でトポロジをデプロイおよび監視する方法については、「[Linux ベースの HDInsight での Apache Storm トポロジのデプロイと管理](hdinsight-storm-deploy-monitor-topology-linux.md)」を参照してください。


