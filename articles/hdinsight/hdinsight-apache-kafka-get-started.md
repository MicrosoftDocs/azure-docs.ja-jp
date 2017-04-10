---
title: "HDInsight での Apache Kafka の概要 | Microsoft Docs"
description: "HDInsight での Kafka の作成と操作の基本について説明します。"
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
ms.assetid: 43585abf-bec1-4322-adde-6db21de98d7f
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: 
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 02/14/2017
ms.author: larryfr
translationtype: Human Translation
ms.sourcegitcommit: 4f2230ea0cc5b3e258a1a26a39e99433b04ffe18
ms.openlocfilehash: 1c7a95354358d91859ce9d2019cef93b6662811f
ms.lasthandoff: 03/25/2017

---
# <a name="get-started-with-apache-kafka-preview-on-hdinsight"></a>HDInsight での Apache Kafka (プレビュー) の概要

[Apache Kafka](https://kafka.apache.org) は、HDInsight で使用可能なオープンソースの分散ストリーミング プラットフォームです。 発行/サブスクライブ メッセージ キューと同様の機能を備えているため、メッセージ ブローカーとして多く使われています。 このドキュメントでは、HDInsight クラスターで Kafka を作成し、Java アプリケーションとの間でデータを送受信する方法について説明します。

> [!NOTE]
> 現時点では、HDInsight で使用可能な Kafka のバージョンには 0.9.0 (HDInsight 3.4) と 0.10.0 (HDInsight 3.5) の 2 つがあります。 このドキュメントの手順では、HDInsight 3.5 で Kafka を使用していることを想定しています。

## <a name="prerequisite"></a>前提条件

[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

Apache Kafka チュートリアルを正常に完了するには、次の条件を満たす必要があります。

* **Azure サブスクリプション**。 [Azure 無料試用版の取得](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/)に関するページを参照してください。

* **SSH と SCP を熟知していること**。 詳細については、[HDInsight での SSH の使用](hdinsight-hadoop-linux-use-ssh-unix.md)に関するページを参照してください。

* [Java JDK 8](http://www.oracle.com/technetwork/java/javase/downloads/index.html) または同等の OpenJDK など。

* [Apache Maven](http://maven.apache.org/) 

### <a name="access-control-requirements"></a>アクセス制御の要件

[!INCLUDE [access-control](../../includes/hdinsight-access-control-requirements.md)]

## <a name="create-a-kafka-cluster"></a>Kafka クラスターの作成

HDInsight で Kafka クラスターを作成するには、次の手順に従います。

1. [Azure Portal](https://portal.azure.com) で、**[+ 新規]**、**[インテリジェンス + 分析]**、**[HDInsight]** の順に選択します。
   
    ![HDInsight クラスターの作成](./media/hdinsight-apache-kafka-get-started/create-hdinsight.png)

2. **[基本]** ブレードで、次の情報を入力します。

    * **[クラスター名]**: HDInsight クラスターの名前。
    * **[サブスクリプション]**: 使用するサブスクリプションを選択します。
    * **[クラスター ログイン ユーザー名]** と **[クラスター ログイン パスワード]**: HTTPS 経由でクラスターにアクセスする場合のログイン。 これらの資格情報を使用して、Ambari Web UI や REST API などのサービスにアクセスします。
    * **[Secure Shell (SSH) username (Secure Shell (SSH) ユーザー名)]**: SSH 経由でクラスターにアクセスする際に使用されるログイン。 既定では、このパスワードは、クラスター ログイン パスワードと同じです。
    * **[リソース グループ]**: クラスターが作成されるリソース グループ。
    * **[場所]**: クラスターが作成される Azure リージョン。
   
    ![サブスクリプションを選択します。](./media/hdinsight-apache-kafka-get-started/hdinsight-basic-configuration.png)

3. **[クラスターの種類]** を選択し、**[クラスターの構成]** ブレードで次の値を設定します。
   
    * **[クラスターの種類]**: Kafka

    * **[バージョン]**: Kafka 0.10.0 (HDI 3.5)

    * **[クラスター レベル]**: Standard
     
    最後に、**[選択]** ボタンをクリックして設定を保存します。
     
    ![クラスターの種類の選択](./media/hdinsight-apache-kafka-get-started/set-hdinsight-cluster-type.png)

    > [!NOTE]
    > Azure サブスクリプションで Kafka プレビューにアクセスできない場合は、このプレビューへのアクセス権を取得する方法に関する指示が表示されます。 次のイメージのような指示が表示されます。
    >
    > ![プレビューに関するメッセージ: 管理される Apache Kafka クラスターを HDInsight にデプロイすることをご希望の場合は、プレビューへのアクセス権を求める電子メールをお送りください](./media/hdinsight-apache-kafka-get-started/no-kafka-preview.png)

4. クラスターの種類を選択したら、__[選択]__ ボタンを使用してクラスターの種類を設定します。 次に、__[次へ]__ ボタンを使用して、基本的な構成を完了します。

5. **[ストレージ]** ブレードで、ストレージ アカウントを選択または作成します。 このドキュメントの手順では、このブレードの他のフィールドを既定値のままにします。 __[次へ]__ ボタンを使用して、ストレージの構成を保存します。

    ![HDInsight のストレージ アカウント設定](./media/hdinsight-apache-kafka-get-started/set-hdinsight-storage-account.png)

6. **[概要]** ブレードで、クラスターの構成を確認します。 間違った設定を変更するには、__[編集]__ リンクを使用します。 最後に、__[作成]__ ボタンを使用してクラスターを作成します。
   
    ![クラスター構成の概要](./media/hdinsight-apache-kafka-get-started/hdinsight-configuration-summary.png)
   
    > [!NOTE]
    > クラスターの作成には最大で 20 分かかります。

## <a name="connect-to-the-cluster"></a>クラスターへの接続

クライアントから、SSH を使用してクラスターに接続します。 Linux、Unix、MacOS を使用している場合、または Windows 10 上で Bash を使用している場合は、次のコマンドを入力します。

```ssh SSHUSER@CLUSTERNAME-ssh.azurehdinsight.net```

**SSHUSER** は、クラスターの作成時に入力した SSH ユーザー名に置き換えます。 **CLUSTERNAME** は、クラスターの名前に置き換えます。

メッセージが表示されたら、SSH アカウントに使用したパスワードを入力します。

詳細については、[HDInsight での SSH の使用](hdinsight-hadoop-linux-use-ssh-unix.md)に関するページを参照してください。

##<a id="getkafkainfo"></a>Zookeeper およびブローカーのホスト情報を取得する

Kafka を使用する場合、*Zookeeper* ホストと*ブローカー* ホストという 2 つのホストの値を知る必要があります。 これらのホストは、Kafka の API や、Kafka に付属するユーティリティの多くで使用されます。

次の手順に従って、ホスト情報が含まれる環境変数を作成します。 このドキュメントの各手順では、これらの環境変数を使用します。

1. クラスターに SSH で接続してから、次のコマンドを使用して `jq` ユーティリティをインストールします。 このユーティリティは JSON ドキュメントを解析するためのものであり、ブローカー ホストの情報取得するのに役立ちます。
   
    ```bash
    sudo apt -y install jq
    ```

2. 次のコマンドを使用して、Ambari から取得した情報で環境変数を設定します。 __KAFKANAME__ は、Kafka クラスターの名前に置き換えます。 __PASSWORD__ は、クラスターの作成時に使用したログイン (管理者) パスワードに置き換えます。

    ```bash
    export KAFKAZKHOSTS=`curl --silent -u admin:'PASSWORD' -G http://headnodehost:8080/api/v1/clusters/KAFKANAME/services/ZOOKEEPER/components/ZOOKEEPER_SERVER | jq -r '["\(.host_components[].HostRoles.host_name):2181"] | join(",")'`

    export KAFKABROKERS=`curl --silent -u admin:'PASSWORD' -G http://headnodehost:8080/api/v1/clusters/KAFKANAME/services/HDFS/components/DATANODE | jq -r '["\(.host_components[].HostRoles.host_name):9092"] | join(",")'`

    echo '$KAFKAZKHOSTS='$KAFKAZKHOSTS
    echo '$KAFKABROKERS='$KAFKABROKERS
    ```

    `$KAFKAZKHOSTS` の内容の例を次のテキストに示します。
   
    `zk0-kafka.eahjefxxp1netdbyklgqj5y1ud.ex.internal.cloudapp.net:2181,zk2-kafka.eahjefxxp1netdbyklgqj5y1ud.ex.internal.cloudapp.net:2181,zk3-kafka.eahjefxxp1netdbyklgqj5y1ud.ex.internal.cloudapp.net:2181`
   
    `$KAFKABROKERS` の内容の例を次のテキストに示します。
   
    `wn1-kafka.eahjefxxp1netdbyklgqj5y1ud.cx.internal.cloudapp.net:9092,wn0-kafka.eahjefxxp1netdbyklgqj5y1ud.cx.internal.cloudapp.net:9092`
   
    > [!WARNING]
    > このセッションで返された情報は、常に正しいわけではありません。 クラスターをスケーリングすると、新しくブローカーが追加されるか削除されます。 障害が発生しノードが交換された場合、そのノードのホスト名が変わる可能性があります。 
    > 
    > 有効な情報を得るために、Zookeeper ホストとブローカー ホストの情報は、使用する直前に取得するようにしてください。

## <a name="create-a-topic"></a>トピックを作成する

Kafka では、*トピック* というカテゴリ内にデータのストリームを格納します。 クラスターのヘッド ノードに SSH で接続し、Kafka に付属のスクリプトを使用してトピックを作成します。

```bash
/usr/hdp/current/kafka-broker/bin/kafka-topics.sh --create --replication-factor 2 --partitions 8 --topic test --zookeeper $KAFKAZKHOSTS
```

このコマンドでは、`$KAFKAZKHOSTS` に格納されているホスト情報を使用して Zookeeper に接続してから、**test** という名前の Kafka トピックを作成します。 トピックを一覧表示する次のスクリプトを使用して、トピックが作成されたことを確認できます。

```bash
/usr/hdp/current/kafka-broker/bin/kafka-topics.sh --list --zookeeper $KAFKAZKHOSTS
```

このコマンドの出力では Kafka トピックが一覧表示され、**test** トピックも表示されます。

## <a name="produce-and-consume-records"></a>レコードの生成および消費

Kafka では、トピック内に*レコード*が格納されます。 レコードは、*プロデューサー*によって生成され、*コンシューマー*によって消費されます。 プロデューサーは、Kafka の*ブローカー*からレコードを取得します。 HDInsight クラスターの各ワーカー ノードが、Kafka のブローカーです。

次の手順に従い、先ほど作成した test トピックにレコードを格納して、コンシューマーを使用してレコードを読み取ります。

1. SSH セッションで、Kafka に付属のスクリプトを使用してトピックにレコードを書き込みます。
   
    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-console-producer.sh --broker-list $KAFKABROKERS --topic test
    ```
   
    このコマンドの後は、プロンプトに戻りません。 代わりに、いくつかのテキスト メッセージを入力してから、**Ctrl+C** キーを使用してトピックへの送信を停止します。 各行が、個別のレコードとして送信されます。

2. Kafka に付属のスクリプトを使用して、トピックからレコードを読み取ります。
   
    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-console-consumer.sh --zookeeper $KAFKAZKHOSTS --topic test --from-beginning
    ```
   
    これにより、レコードがトピックから取得され、表示されます。 `--from-beginning` を使用することで、すべてのレコードが取得されるように、コンシューマーにストリームの先頭から開始するように指示しています。

3. __Ctrl+C__ キーを使用してコンシューマーを停止します。

## <a name="producer-and-consumer-api"></a>プロデューサーとコンシューマーの API

[Kafka の API](http://kafka.apache.org/documentation#api) を使用して、レコードをプログラムにより生成および消費することもできます。 Java ベースのプロデューサーとコンシューマーをダウンロードし構築するには、次の手順に従います。

1. [https://github.com/Azure-Samples/hdinsight-kafka-java-get-started](https://github.com/Azure-Samples/hdinsight-kafka-java-get-started) からサンプルをダウンロードします。 プロデューサーとコンシューマーのサンプルには、`Producer-Consumer` ディレクトリ内のプロジェクトを使用します。 このサンプルには次のクラスが含まれています。
   
    * **Run** - コンシューマーまたはプロデューサーを開始します。

    * **Producer** - 1,000,000 件のレコードをトピックに格納します。

    * **Consumer** - トピックからレコードを読み取ります。

2. 開発環境で、コマンド ラインからサンプルの `Producer-Consumer` ディレクトリの場所に移動し、次のコマンドを使用して jar パッケージを作成します。
   
    ```
    mvn clean package
    ```
   
    このコマンドにより、`kafka-producer-consumer-1.0-SNAPSHOT.jar` というファイルを含む `target` という名前のディレクトリが作成されます。

3. 次のコマンドを使用して、HDInsight クラスターに `kafka-producer-consumer-1.0-SNAPSHOT.jar` ファイルをコピーします。
   
    ```bash
    scp ./target/kafka-producer-consumer-1.0-SNAPSHOT.jar SSHUSER@CLUSTERNAME-ssh.azurehdinsight.net:kafka-producer-consumer.jar
    ```
   
    **SSHUSER** は、クラスターの SSH ユーザーに置き換えます。また、**CLUSTERNAME** はクラスターの名前に置き換えます。 メッセージが表示されたら、SSH ユーザーのパスワードを入力します。

4. `scp` コマンドによるファイルのコピーが完了したら、SSH を使用してクラスターに接続し、次のコマンドを使用して、先ほど作成した test トピックにレコードを書き込みます。
   
    ```bash
    ./kafka-producer-consumer.jar producer $KAFKABROKERS
    ```
   
    このコマンドにより、プロデューサーが開始され、レコードが書き込まれます。 書き込まれたレコードの数を確認できるように、カウンターが表示されます。

    > [!NOTE]
    > アクセス許可の拒否エラーが発生した場合は、```chmod +x kafka-producer-consumer.jar``` コマンドを使用してファイルを実行可能にしてください。

5. プロセスが完了したら、次のコマンドを使用してトピックから読み取りを行います。
   
    ```bash
    ./kafka-producer-consumer.jar consumer $KAFKABROKERS
    ```
   
    読み取られたレコードが、レコードの件数とともに表示されます。 前の手順でスクリプトを使用してトピックに数件のレコードを送信したため、1,000,000 件より少し多いレコードが表示される可能性があります。

6. __Ctrl+C__ キーを使用してコンシューマーを終了します。

### <a name="multiple-consumers"></a>複数のコンシューマー

Kafka の重要なコンセプトとして、コンシューマーはレコードを読み取る場合に (グループ ID で定義された) コンシューマー グループを使用するということがあります。 複数のコンシューマーで同じグループを使用すると、トピックからの読み取りの負荷が分散されます。 グループ内の各コンシューマーは、レコードの一部を受け取ります。 このプロセスを実際に確認するには、次の手順に従います。

1. クラスターへの新しい SSH セッションを開き、2 つのセッションを用意します。 各セッションで次のコマンドを使用して、コンシューマー グループ ID が同じコンシューマーを起動します。
   
    ```bash
    ./kafka-producer-consumer.jar consumer $KAFKABROKERS mygroup
    ```

    > [!NOTE]
    > これは新しい SSH セッションであるため、「[Zookeeper およびブローカーのホスト情報を取得する](#getkafkainfo)」セクションのコマンドを使用して `$KAFKABROKERS` を設定する必要があります。

2. 各セッションでトピックから取得されたレコード数がカウントされる様子を確認します。 両方のセッションの合計は、先ほど 1 つのコンシューマーから取得したレコード数と同じになるはずです。

同じグループ内のクライアントによる消費は、トピックのパーティションを介して処理されます。 先ほど作成した `test` トピックには、8 つのパーティションがあります。 8 個の SSH セッションを開き、すべてのセッションで 1 つのコンシューマーを起動した場合、各コンシューマーはトピックの 1 つのパーティションからレコードを読み取ります。

> [!IMPORTANT]
> コンシューマー グループに、パーティションの数よりも多いコンシューマー インスタンスを含めることはできません。 この例では、トピック内のパーティション数が 8 であるため、1 つのコンシューマー グループに最大 8 個のコンシューマーを含めることができます。 または、それぞれコンシューマーが 8 個以下の複数のコンシューマー グループを存在させることができます。

Kafka に格納されたレコードは、受信した順番でパーティション内に格納されます。 *パーティション内*のレコードの順次配信順を実現するには、コンシューマー インスタンスの数がパーティションの数と同じコンシューマー グループを作成します。 *トピック内*のレコードの順次配信を実現するには、コンシューマー インスタンスが 1 つのみのコンシューマー グループを作成します。

## <a name="streaming-api"></a>ストリーミング API

ストリーミング API は、バージョン 0.10.0 の Kafka から追加されました。以前のバージョンでは、ストリーム処理に Apache Spark または Storm を使用しています。

1. まだサンプルをダウンロードしていない場合は、[https://github.com/Azure-Samples/hdinsight-kafka-java-get-started](https://github.com/Azure-Samples/hdinsight-kafka-java-get-started) から開発環境にダウンロードします。 ストリーミングのサンプルには、`streaming` ディレクトリ内のプロジェクトを使用します。
   
    このプロジェクトには、上記で作成した `test` トピックからレコードを読み取る `Stream` というクラスのみが含まれています。 このクラスは読み取った単語数をカウントしてから、`wordcounts` という名前のトピックに各単語とカウントを出力します。 `wordcounts` トピックは、このセクションの後の手順で作成します。

2. 開発環境で、コマンド ラインから `Streaming` ディレクトリの場所に移動し、次のコマンドを使用して jar パッケージを作成します。
   
    ```
    mvn clean package
    ```
   
    このコマンドにより、`kafka-streaming-1.0-SNAPSHOT.jar` というファイルを含む `target` という名前のディレクトリが作成されます。

3. 次のコマンドを使用して、HDInsight クラスターに `kafka-streaming-1.0-SNAPSHOT.jar` ファイルをコピーします。
   
    ```bash
    scp ./target/kafka-streaming-1.0-SNAPSHOT.jar SSHUSER@CLUSTERNAME-ssh.azurehdinsight.net:kafka-streaming.jar
    ```
   
    **SSHUSER** は、クラスターの SSH ユーザーに置き換えます。また、**CLUSTERNAME** はクラスターの名前に置き換えます。 メッセージが表示されたら、SSH ユーザーのパスワードを入力します。

4. `scp` コマンドによるファイルのコピーが完了したら、SSH を使用してクラスターに接続し、次のコマンドを使用して `wordcounts` トピックを作成します。

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --create --replication-factor 2 --partitions 8 --topic wordcounts --zookeeper $KAFKAZKHOSTS
    ```

5. 次に、次のコマンドを使用してストリーミング プロセスを開始します。
   
    ```bash
    ./kafka-streaming.jar $KAFKABROKERS $KAFKAZKHOSTS 2>/dev/null &
    ```
   
    このコマンドにより、バックグラウンドでストリーミング プロセスが開始されます。

6. 次のコマンドを使用して、`test` トピックにメッセージを送信します。 これらのメッセージはストリーミング サンプルで処理されます。
   
    ```bash
    ./kafka-producer-consumer.jar producer $KAFKABROKERS &>/dev/null &
    ```

7. 次のコマンドを使用して、ストリーミング プロセスによって `wordcounts` トピックに書き込まれた出力を表示します。
   
    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-console-consumer.sh --zookeeper $KAFKAZKHOSTS --topic wordcounts --from-beginning --formatter kafka.tools.DefaultMessageFormatter --property print.key=true --property key.deserializer=org.apache.kafka.common.serialization.StringDeserializer --property value.deserializer=org.apache.kafka.common.serialization.LongDeserializer
    ```
   
    > [!NOTE]
    > データを表示するには、コンシューマーにはキーを出力するよう指示し、デシリアライザーにはそのキーと値を使用するよう指示する必要があります。 キー名は単語で、キー値にはカウントが含まれます。
   
    出力は次のテキストのようになります。
   
        dwarfs  13635
        ago     13664
        snow    13636
        dwarfs  13636
        ago     13665
        a       13803
        ago     13666
        a       13804
        ago     13667
        ago     13668
        jumped  13640
        jumped  13641
        a       13805
        snow    13637
   
    > [!NOTE]
    > 単語が現れるたびにカウントが増加します。

7. __Ctrl+C__ キーを使用してコンシューマーを終了してから、`fg` コマンドを使用してバックグラウンドのストリーミング タスクをフォアグラウンドに移します。 同様に、__Ctrl+C__ キーを使用してこのタスクも終了します。

## <a name="delete-the-cluster"></a>クラスターを削除する

[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

## <a name="next-steps"></a>次のステップ

このドキュメントでは、HDInsight で Apache Kafka を使用する際の基本事項を学習しました。 次の各ドキュメントを参考に、Kafka の使用の詳細を確認してください。

* kafka.apache.org にある [Apache Kafka のドキュメント](http://kafka.apache.org/documentation.html)
* [MirrorMaker を使用した HDInsight での Kafka のレプリカの作成](hdinsight-apache-kafka-mirroring.md)
* [HDInsight での Kafka に Apache Storm を使用する](hdinsight-apache-storm-with-kafka.md)
* [HDInsight での Kafka に Apache Spark を使用する](hdinsight-apache-spark-with-kafka.md)


