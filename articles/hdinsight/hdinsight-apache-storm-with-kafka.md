---
title: "HDInsight 上の Storm で Apache Kafka を使用する -Azure | Microsoft Docs"
description: "HDInsight での Apache Storm には、Apache Kafka がインストールされています。 Storm に付属する KafkaBolt コンポーネントと KafkaSpout コンポーネントを使用して、Kafka への書き込みおよび読み取りを行う方法について説明します。 また、Flux フレームワークを使用して Storm トポロジを定義し送信する方法についても説明します。"
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
ms.assetid: e4941329-1580-4cd8-b82e-a2258802c1a7
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: java
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 07/21/2017
ms.author: larryfr
ms.openlocfilehash: e8895ef3c11aea48513e4060a20f5f49b11fc961
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="use-apache-kafka-preview-with-storm-on-hdinsight"></a>HDInsight での Strom に Apache Kafka (プレビュー) を使用する

Apache Storm を使用して Apache Kafka のデータを読み書きする方法を説明します。 この例では、HDInsight で使用される HDFS と互換性のあるファイル システムに Storm トポロジからデータを保存する方法も示します。

> [!NOTE]
> このドキュメントの手順では、HDInsight の Storm クラスターと HDInsight の Kafka クラスターの両方を含む Azure リソース グループを作成します。 これらのクラスターは両方とも、Strom クラスターが Kafka クラスターと直接通信できるように、Azure 仮想ネットワーク内に配置します。
> 
> このドキュメントの手順を完了したら、余分に課金されないようにするためにクラスターは削除してください。

## <a name="get-the-code"></a>コードの入手

このドキュメントで説明する例で使用するコードは、[https://github.com/Azure-Samples/hdinsight-storm-java-kafka](https://github.com/Azure-Samples/hdinsight-storm-java-kafka) で入手できます。

このプロジェクトをコンパイルするには、開発環境に次の構成が必要です。

* [Java JDK 1.8](https://www.oracle.com/technetwork/java/javase/downloads/jdk7-downloads-1880260.html) 以上 HDInsight 3.5 以降には Java 8 が必要です。

* [Maven 3.x](https://maven.apache.org/download.cgi)

* SSH クライアント (`ssh` と `scp` コマンドが必要です) - 詳細については、[HDInsight での SSH の使用](hdinsight-hadoop-linux-use-ssh-unix.md)に関するページをご覧ください。

* テキスト エディターまたは IDE

開発用ワークステーションに Java と JDK をインストールするときに、次のような環境変数が設定される場合があります。 ただし、これらが存在するかどうかや、システムに対して適切な値が含まれているかを確認する必要があります。

* `JAVA_HOME` - JDK がインストールされているディレクトリを指している必要があります。
* `PATH` - 次のパスを含む必要があります。
  
    * `JAVA_HOME` (または同等のパス)。
    * `JAVA_HOME\bin` (または同等のパス)。
    * Maven がインストールされているディレクトリ。

## <a name="create-the-clusters"></a>クラスターの作成

HDInsight の Apache Kafka では、パブリック インターネットを介した Kafka ブローカーへのアクセスは提供されていません。 Kafka と通信するすべてのものは、Kafka クラスター内のノードと同じ Azure 仮想ネットワークに存在している必要があります。 この例では、Kafka クラスターと Storm クラスターの両方を Azure 仮想ネットワーク内に配置します。 次の図に、クラスター間の通信フローを示します。

![Azure 仮想ネットワークにおける Strom クラスターと Kafka クラスターの図](./media/hdinsight-apache-storm-with-kafka/storm-kafka-vnet.png)

> [!NOTE]
> SSH や Ambari など、クラスター上の他のサービスは、インターネット経由でアクセスできます。 HDInsight で使用できるパブリック ポートの詳細については、「[HDInsight で使用されるポートと URI](hdinsight-hadoop-port-settings-for-services.md)」を参照してください。

Azure 仮想ネットワーク、Kafka、および Storm クラスターは手動で作成できますが、Azure Resource Manager テンプレートを使用する方が簡単です。 次の手順に従って、Azure 仮想ネットワーク、Kafka クラスター、および Storm クラスターを Azure サブスクリプションにデプロイします。

1. 次のボタンを使用して Azure にサインインし、Azure Portal でテンプレートを開きます。
   
    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fhditutorialdata.blob.core.windows.net%2Farmtemplates%2Fcreate-linux-based-kafka-storm-cluster-in-vnet-v2.json" target="_blank"><img src="./media/hdinsight-apache-storm-with-kafka/deploy-to-azure.png" alt="Deploy to Azure"></a>
   
    Azure Resource Manager テンプレートは、**https://hditutorialdata.blob.core.windows.net/armtemplates/create-linux-based-kafka-storm-cluster-in-vnet-v1.json** にあります。 このテンプレートは次のリソースを作成します。
    
    * Azure リソース グループ
    * Azure Virtual Network
    * Azure ストレージ アカウント
    * HDInsight バージョン 3.6 上の Kafka (3 ワーカー ノード)
    * HDInsight バージョン 3.6 上の Storm (3 ワーカー ノード)

  > [!WARNING]
  > HDInsight で Kafka の可用性を保証するには、クラスターに少なくとも 3 つのワーカー ノードが必要です。 このテンプレートは、3 つのワーカー ノードが含まれる Kafka クラスターを作成します。

2. 次のガイダンスに従って、**[カスタム デプロイ]** ブレードの各エントリに入力します。
   
    ![HDInsight のカスタム デプロイ](./media/hdinsight-apache-storm-with-kafka/parameters.png)

    * **[リソース グループ]**: グループを作成するか、または既存のグループを選択します。 このグループに HDInsight クラスターが含まれます。
   
    * **[場所]**: 地理的に近い場所を選択します。

    * **[Base Cluster Name] \(ベース クラスター名)**: この値は、Strom クラスターと Kafka クラスターのベース名として使用されます。 たとえば、「**hdi**」と入力すると、**storm-hdi** という名前の Storm クラスターと、**kafka-hdi** という名前の Kafka クラスターが作成されます。
   
    * **[Cluster Login User Name] \(クラスター ログイン ユーザー名)**: Storm クラスターと Kafka クラスターの管理者のユーザー名。
   
    * **[クラスター ログイン パスワード]**: Storm クラスターと Kafka クラスターの管理者のユーザー パスワード。
    
    * **[SSH ユーザー名]**: Storm クラスターおよび Kafka クラスターの作成に使用する SSHユーザー。
    
    * **[SSH パスワード]**: Storm クラスターおよび Kafka クラスター用の SSH ユーザーのパスワード。

3. **使用条件**を読み、**[上記の使用条件に同意する]** をオンにします。

4. 最後に、**[ダッシュボードにピン留めする]** をオンにし、**[購入]** をクリックします。 クラスターの作成には約 20 分かかります。

リソースが作成されると、リソース グループのブレードが表示されます。

![Resource group blade for the vnet and clusters](./media/hdinsight-apache-storm-with-kafka/groupblade.png)

> [!IMPORTANT]
> 各 HDInsight クラスターの名前が **storm-BASENAME** および **kafka-BASENAME** であることに注目してください。BASENAME はテンプレートで指定した名前です。 これらの名前は、後の手順でクラスターに接続するときに使用します。

## <a name="understanding-the-code"></a>コードについて

このプロジェクトには、2 つのトポロジが含まれています。

* **KafkaWriter**: このトポロジは **writer.yaml** で定義されており、Apache Storm に付属の KafkaBolt を使用して Kafka にランダムな文を書き込みます。

    このトポロジでは、ランダムな文の生成に **SentenceSpout** コンポーネントを使用します。

* **KafkaReader**: このトポロジは **reader.yaml** ファイルで定義されており、Apache Strom に付属の KafkaSpout を使用して Kafka のデータを読み取り標準出力に記録します。

    このトポロジでは、Storm HdfsBolt を使用して Storm クラスターの既定のストレージにデータを書き込みます。
### <a name="flux"></a>Flux

これらのトポロジは、[Flux](https://storm.apache.org/releases/1.1.0/flux.html) で定義されています。 Flux は Storm 0.10.x で導入されており、これによりトポロジ構成とコードを切り離すことができます。 Flux フレームワークを使用するトポロジの場合、YAML ファイルでトポロジを定義します。 YAML ファイルはトポロジの一部として含めることができるほか、 トポロジを送信するときに使用するスタンドアロン ファイルにもなります。 Flux では実行時の変数代入もサポートされており、以下の例ではこれを使用しています。

これらのトポロジの実行時に、次のパラメーターが設定されます。

* `${kafka.topic}`: トポロジの読み取り/書き込みの対象になる Kafka トピックの名前。

* `${kafka.broker.hosts}`: Kafka ブローカーが実行されるホスト。 ブローカー情報は、KafkaBolt が Kafka への書き込み時に使用します。

* `${kafka.zookeeper.hosts}`: Kafka クラスター上で Zookeeper が実行されるホスト。

Flux トポロジについて詳しくは、[https://storm.apache.org/releases/1.1.0/flux.html](https://storm.apache.org/releases/1.1.0/flux.html) をご覧ください。

## <a name="download-and-compile-the-project"></a>プロジェクトのダウンロードおよびコンパイル

1. 開発環境で、[https://github.com/Azure-Samples/hdinsight-storm-java-kafka](https://github.com/Azure-Samples/hdinsight-storm-java-kafka) からプロジェクトをダウンロードし、コマンドラインを開いてプロジェクトをダウンロードした場所に移動します。

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

## <a name="configure-the-topology"></a>トポロジの構成

1. 次のいずれかのメソッドを使用して Kafka Broker ホストを検出します。

    ```powershell
    $creds = Get-Credential -UserName "admin" -Message "Enter the HDInsight login"
    $clusterName = Read-Host -Prompt "Enter the Kafka cluster name"
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/KAFKA/components/KAFKA_BROKER" `
        -Credential $creds
    $respObj = ConvertFrom-Json $resp.Content
    $brokerHosts = $respObj.host_components.HostRoles.host_name[0..1]
    ($brokerHosts -join ":9092,") + ":9092"
    ```

    ```bash
    curl -su admin -G "https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/services/KAFKA/components/KAFKA_BROKER" | jq -r '["\(.host_components[].HostRoles.host_name):9092"] | join(",")' | cut -d',' -f1,2
    ```

    > [!IMPORTANT]
    > Bash の例では、`$CLUSTERNAME` に HDInsight クラスターの名前が含まれていること、 および [jq](https://stedolan.github.io/jq/) がインストールされいていることを前提としています。 プロンプトが表示されたら、クラスターのログイン アカウントのパスワードを入力してください。

    次のテキストのような値が返されます。

        wn0-kafka.53qqkiavjsoeloiq3y1naf4hzc.ex.internal.cloudapp.net:9092,wn1-kafka.53qqkiavjsoeloiq3y1naf4hzc.ex.internal.cloudapp.net:9092

    > [!IMPORTANT]
    > クラスターに 2 つ以上のブローカー ホストがある場合でも、すべてのホストの完全な一覧をクライアントに提供する必要はありません。 1 つまたは 2 つで十分です。

2. 次のいずれかのメソッドを使用して Kafka Zookeeper ホストを検出します。

    ```powershell
    $creds = Get-Credential -UserName "admin" -Message "Enter the HDInsight login"
    $clusterName = Read-Host -Prompt "Enter the Kafka cluster name"
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/ZOOKEEPER/components/ZOOKEEPER_SERVER" `
        -Credential $creds
    $respObj = ConvertFrom-Json $resp.Content
    $zookeeperHosts = $respObj.host_components.HostRoles.host_name[0..1]
    ($zookeeperHosts -join ":2181,") + ":2181"
    ```

    ```bash
    curl -su admin -G "https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/services/ZOOKEEPER/components/ZOOKEEPER_SERVER" | jq -r '["\(.host_components[].HostRoles.host_name):2181"] | join(",")' | cut -d',' -f1,2
    ```

    > [!IMPORTANT]
    > Bash の例では、`$CLUSTERNAME` に HDInsight クラスターの名前が含まれていること、 および [jq](https://stedolan.github.io/jq/) がインストールされいていることを前提としています。 プロンプトが表示されたら、クラスターのログイン アカウントのパスワードを入力してください。

    次のテキストのような値が返されます。

        zk0-kafka.53qqkiavjsoeloiq3y1naf4hzc.ex.internal.cloudapp.net:2181,zk2-kafka.53qqkiavjsoeloiq3y1naf4hzc.ex.internal.cloudapp.net:2181

    > [!IMPORTANT]
    > クラスターに 2 つ以上の Zookeeper ホストがある場合でも、すべてのホストの完全な一覧をクライアントに提供する必要はありません。 1 つまたは 2 つで十分です。

    この値を保存します。これは後で使用します。

3. プロジェクトのルートにある `dev.properties` ファイルを編集します。 このファイルの該当する行に、Broker ホストと Zookeeper ホストの情報を追加します。 次の例は、前の手順のサンプルの値を使用して構成されています。

        kafka.zookeeper.hosts: zk0-kafka.53qqkiavjsoeloiq3y1naf4hzc.ex.internal.cloudapp.net:2181,zk2-kafka.53qqkiavjsoeloiq3y1naf4hzc.ex.internal.cloudapp.net:2181
        kafka.broker.hosts: wn0-kafka.53qqkiavjsoeloiq3y1naf4hzc.ex.internal.cloudapp.net:9092,wn1-kafka.53qqkiavjsoeloiq3y1naf4hzc.ex.internal.cloudapp.net:9092
        kafka.topic: stormtopic

4. `dev.properties` ファイルを保存し、次のコマンドを使用して Storm クラスターにアップロードします。

     ```bash
    scp dev.properties USERNAME@storm-BASENAME-ssh.azurehdinsight.net:KafkaTopology-1.0-SNAPSHOT.jar
    ```

    **USERNAME** は、クラスターの SSH ユーザー名に置き換えます。 **BASENAME** は、クラスターの作成時に使用したベース名に置き換えます。

## <a name="start-the-writer"></a>ライターの起動

1. 次のコマンドを使用して、SSH で Storm クラスターに接続します。 **USERNAME** は、クラスターの作成時に使用した SSH ユーザー名に置き換えます。 **BASENAME** は、クラスターの作成時に使用したベース名に置き換えます。

  ```bash
  ssh USERNAME@storm-BASENAME-ssh.azurehdinsight.net
  ```

    メッセージが表示されたら、クラスターの作成時に使用したパスワードを入力します。
   
    詳細については、[HDInsight での SSH の使用](hdinsight-hadoop-linux-use-ssh-unix.md)に関するページを参照してください。

2. SSH 接続から、次のコマンドを使用して、トポロジで使用される Kafka トピックを作成します。

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --create --replication-factor 3 --partitions 8 --topic stormtopic --zookeeper $KAFKAZKHOSTS
    ```

    `$KAFKAZKHOSTS` を、前のセクションで取得した Zookeeper ホスト情報に置き換えます。

2. Storm クラスターに SSH で接続してから、次のコマンドを使用してライター トポロジを起動します。

    ```bash
    storm jar KafkaTopology-1.0-SNAPSHOT.jar org.apache.storm.flux.Flux --remote -R /writer.yaml --filter dev.properties
    ```

    このコマンドで使用されているパラメーターの意味は次のとおりです。

    * `org.apache.storm.flux.Flux`: Flux を使用してこのトポロジを構成および実行します。

    * `--remote`: トポロジを Nimbus に送信します。 トポロジは、クラスター内のワーカー ノード全体に配布されます。

    * `-R /writer.yaml`: `writer.yaml` ファイルを使用して、トポロジを構成します。 `-R` は、このリソースが jar ファイル内に含まれていることを指示しています。 リソースは jar のルートにあるため、`/writer.yaml` がリソースへのパスになります。

    * `--filter`: `dev.properties` ファイルの値を使用して、`writer.yaml` トポロジのエントリを作成します。 たとえば、ファイル内の `kafka.topic` エントリの値を使用して、トポロジの定義内の `${kafka.topic}` エントリを置き換えます。

5. トポロジが起動したら、次のコマンドを使用して、Kafka トピックにデータを書き込んでいることを確認します。

  ```bash
  /usr/hdp/current/kafka-broker/bin/kafka-console-consumer.sh --zookeeper $KAFKAZKHOSTS --from-beginning --topic stormtopic
  ```

    `$KAFKAZKHOSTS` を、前のセクションで取得した Zookeeper ホスト情報に置き換えます。

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
  storm jar KafkaTopology-1.0-SNAPSHOT.jar org.apache.storm.flux.Flux --remote -R /reader.yaml --filter dev.properties
  ```

2. トポロジが起動したら、Storm UI を開きます。 この Web UI は、https://storm-BASENAME.azurehdinsight.net/stormui にあります。 __BASENAME__ は、クラスターの作成時に使用したベース名に置き換えます。 

    メッセージが表示されたら、クラスターの作成時に使用した管理者のログイン名 (既定では `admin`) およびパスワードを入力します。 次の画像のような Web ページが表示されます。

    ![Storm UI](./media/hdinsight-apache-storm-with-kafka/stormui.png)

3. Storm UI で、__[Topology Summary] \(トポロジの概要)__ セクションの __[kafka-reader]__ リンクをクリックして、__kafka-reader__ トポロジの情報を表示します。

    ![Storm Web UI の [Topology Summary] \(トポロジの概要) セクション](./media/hdinsight-apache-storm-with-kafka/topology-summary.png)

4. logger-bolt コンポーネントのインスタンスに関する情報を表示するには、__[Bolts (All time)]\(ボルト (常時)\)__ セクションの __[logger-bolt]__ リンクをクリックします。

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

このドキュメントの手順では両方のクラスターを同じ Azure リソース グループに作成したため、Azure Portal でこのリソース グループを削除するだけで済みます。 リソース グループを削除すると、このドキュメントに従って作成したすべてのリソースが削除されます。

## <a name="next-steps"></a>次のステップ

HDInsight 上の Storm で使用できるトポロジの例については、[Storm のトポロジおよびコンポーネントのサンプル](hdinsight-storm-example-topology.md)に関するページを参照してください。

Linux ベースの HDInsight でトポロジをデプロイおよび監視する方法については、「[Linux ベースの HDInsight での Apache Storm トポロジのデプロイと管理](hdinsight-storm-deploy-monitor-topology-linux.md)」を参照してください。