---
title: "Apache Kafka の開始 - Azure HDInsight | Microsoft Docs"
description: "Azure HDInsight で Apache Kafka クラスターを作成する方法について説明します。 トピック、サブスクライバー、コンシューマーの作成方法について説明します。"
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
ms.date: 09/20/2017
ms.author: larryfr
ms.openlocfilehash: 1e51f546d6c256e1d8f1a1be50c6a2102fe26529
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="start-with-apache-kafka-preview-on-hdinsight"></a>HDInsight での Apache Kafka (プレビュー) の開始

Azure HDInsight で [Apache Kafka](https://kafka.apache.org) クラスターを作成および使用する方法について説明します。 Apache Kafka は、HDInsight で利用できるオープンソースの分散ストリーミング プラットフォームです。 発行/サブスクライブ メッセージ キューと同様の機能を備えているため、メッセージ ブローカーとして多く使われています。

> [!NOTE]
> 現時点では、HDInsight で使用可能な Kafka のバージョンには 0.9.0 (HDInsight 3.4) と 0.10.0 (HDInsight 3.5 および 3.6) の 2 つがあります。 このドキュメントの手順では、HDInsight 3.6 で Kafka を使用していることを想定しています。

[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

## <a name="create-a-kafka-cluster"></a>Kafka クラスターの作成

HDInsight で Kafka クラスターを作成するには、次の手順に従います。

1. [Azure Portal](https://portal.azure.com) で、**[+ 新規]**、**[インテリジェンス + 分析]**、**[HDInsight]** の順に選択します。
   
    ![HDInsight クラスターの作成](./media/hdinsight-apache-kafka-get-started/create-hdinsight.png)

2. **[基本]** から次の情報を入力します。

    * **[クラスター名]**: HDInsight クラスターの名前。
    * **[サブスクリプション]**: 使用するサブスクリプションを選択します。
    * **[クラスター ログイン ユーザー名]** と **[クラスター ログイン パスワード]**: HTTPS 経由でクラスターにアクセスする場合のログイン。 これらの資格情報を使用して、Ambari Web UI や REST API などのサービスにアクセスします。
    * **[Secure Shell (SSH) username (Secure Shell (SSH) ユーザー名)]**: SSH 経由でクラスターにアクセスする際に使用されるログイン。 既定では、このパスワードは、クラスター ログイン パスワードと同じです。
    * **[リソース グループ]**: クラスターが作成されるリソース グループ。
    * **[場所]**: クラスターが作成される Azure リージョン。

        > [!IMPORTANT]
        > データの高可用性を実現するために、__3 つの障害ドメイン__を含む場所 (リージョン) を選択することをお勧めします。 詳細については、「[データの高可用性](#data-high-availability)」のセクションを参照してください。
   
 ![サブスクリプションを選択します。](./media/hdinsight-apache-kafka-get-started/hdinsight-basic-configuration.png)

3. **[クラスターの種類]** を選択し、**[クラスターの構成]** から次の値を設定します。
   
    * **[クラスターの種類]**: Kafka

    * **[バージョン]**: Kafka 0.10.0 (HDI 3.6)

    * **[クラスター レベル]**: Standard
     
 最後に、**[選択]** ボタンをクリックして設定を保存します。
     
 ![クラスターの種類の選択](./media/hdinsight-apache-kafka-get-started/set-hdinsight-cluster-type.png)

4. クラスターの種類を選択したら、__[選択]__ ボタンを使用してクラスターの種類を設定します。 次に、__[次へ]__ ボタンを使用して、基本的な構成を完了します。

5. **[ストレージ]** で、ストレージ アカウントを選択または作成します。 このドキュメントの手順では、他のフィールドを既定値のままにします。 __[次へ]__ ボタンを使用して、ストレージの構成を保存します。

    ![HDInsight のストレージ アカウント設定](./media/hdinsight-apache-kafka-get-started/set-hdinsight-storage-account.png)

6. __[アプリケーション (オプション)]__ で __[次へ]__ を選んで続行します。 この例ではアプリケーションは必要ありません。

7. __[クラスター サイズ]__ で __[次へ]__ を選んで続行します。

    > [!WARNING]
    > HDInsight で Kafka の可用性を保証するには、クラスターに少なくとも 3 つのワーカー ノードが必要です。 詳細については、「[データの高可用性](#data-high-availability)」のセクションを参照してください。

    ![Kafka のクラスター サイズの設定](./media/hdinsight-apache-kafka-get-started/kafka-cluster-size.png)

    > [!IMPORTANT]
    > **ワーカー ノード エントリごとのディスクの数**は、HDInsight での Kafka のスケーラビリティを制御します。 HDInsight 上の Kafka は、クラスターの仮想マシンのローカル ディスクを使います。 Kafka は I/O が多いため、[Azure Managed Disks](../virtual-machines/windows/managed-disks-overview.md) を使ってノードごとに高いスループットと多くの記憶域を提供します。 管理ディスクの種類は、__Standard__ (HDD) または __Premium__ (SSD) です。 Premium ディスクは、DS および GS シリーズの VM で使われます。 他の種類の VM はすべて Standard を使います。

8. __[詳細設定]__ で __[次へ]__ を選んで続行します。

9. **[概要]** でクラスターの構成を確認します。 間違った設定を変更するには、__[編集]__ リンクを使用します。 最後に、__[作成]__ ボタンを使用してクラスターを作成します。
   
    ![クラスター構成の概要](./media/hdinsight-apache-kafka-get-started/hdinsight-configuration-summary.png)
   
    > [!NOTE]
    > クラスターの作成には最大で 20 分かかります。

## <a name="connect-to-the-cluster"></a>クラスターへの接続

> [!IMPORTANT]
> 以下の手順を実行するときは、SSH クライアントを使用する必要があります。 詳細については、[HDInsight での SSH の使用](hdinsight-hadoop-linux-use-ssh-unix.md)に関するドキュメントを参照してください。

クライアントから、SSH を使用してクラスターに接続します。

```ssh SSHUSER@CLUSTERNAME-ssh.azurehdinsight.net```

**SSHUSER** は、クラスターの作成時に入力した SSH ユーザー名に置き換えます。 **CLUSTERNAME** は、クラスターの名前に置き換えます。

メッセージが表示されたら、SSH アカウントに使用したパスワードを入力します。

詳細については、[HDInsight での SSH の使用](hdinsight-hadoop-linux-use-ssh-unix.md)に関するページを参照してください。

## <a id="getkafkainfo"></a>Zookeeper およびブローカーのホスト情報を取得する

Kafka を使用する場合、*Zookeeper* ホストと*ブローカー* ホストという 2 つのホストの値を知る必要があります。 これらのホストは、Kafka の API や、Kafka に付属するユーティリティの多くで使用されます。

次の手順に従って、ホスト情報が含まれる環境変数を作成します。 このドキュメントの各手順では、これらの環境変数を使用します。

1. クラスターに SSH で接続してから、次のコマンドを使用して `jq` ユーティリティをインストールします。 このユーティリティは JSON ドキュメントを解析するためのものであり、ブローカー ホストの情報取得するのに役立ちます。
   
    ```bash
    sudo apt -y install jq
    ```

2. Ambari から取得した情報で環境変数を設定するには、次のコマンドを使用します。

    ```bash
    CLUSTERNAME='your cluster name'
    PASSWORD='your cluster password'
    export KAFKAZKHOSTS=`curl -sS -u admin:$PASSWORD -G https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/services/ZOOKEEPER/components/ZOOKEEPER_SERVER | jq -r '["\(.host_components[].HostRoles.host_name):2181"] | join(",")' | cut -d',' -f1,2`

    export KAFKABROKERS=`curl -sS -u admin:$PASSWORD -G https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/services/KAFKA/components/KAFKA_BROKER | jq -r '["\(.host_components[].HostRoles.host_name):9092"] | join(",")' | cut -d',' -f1,2`

    echo '$KAFKAZKHOSTS='$KAFKAZKHOSTS
    echo '$KAFKABROKERS='$KAFKABROKERS
    ```

    > [!IMPORTANT]
    > `CLUSTERNAME=` には、Kafka クラスターの名前を設定します。 `PASSWORD=`PASSWORD には、クラスターの作成時に使用したログイン (管理者) パスワードを設定します。

    `$KAFKAZKHOSTS` の内容の例を次のテキストに示します。
   
    `zk0-kafka.eahjefxxp1netdbyklgqj5y1ud.ex.internal.cloudapp.net:2181,zk2-kafka.eahjefxxp1netdbyklgqj5y1ud.ex.internal.cloudapp.net:2181`
   
    `$KAFKABROKERS` の内容の例を次のテキストに示します。
   
    `wn1-kafka.eahjefxxp1netdbyklgqj5y1ud.cx.internal.cloudapp.net:9092,wn0-kafka.eahjefxxp1netdbyklgqj5y1ud.cx.internal.cloudapp.net:9092`

    > [!NOTE]
    > ホストのリストを 2 つのホスト エントリにトリミングするために、`cut` コマンドを使用しています。 Kafka のコンシューマーまたはプロデューサーを作成する際に、全ホストのリストを提供する必要はありません。
   
    > [!WARNING]
    > このセッションで返された情報は、常に正しいわけではありません。 クラスターをスケーリングすると、新しくブローカーが追加されるか削除されます。 障害が発生しノードが交換された場合、そのノードのホスト名が変わる可能性があります。
    >
    > 有効な情報を得るために、Zookeeper ホストとブローカー ホストの情報は、使用する直前に取得するようにしてください。

## <a name="create-a-topic"></a>トピックを作成する

Kafka では、*トピック* というカテゴリ内にデータのストリームを格納します。 クラスターのヘッド ノードに SSH で接続し、Kafka に付属のスクリプトを使用してトピックを作成します。

```bash
/usr/hdp/current/kafka-broker/bin/kafka-topics.sh --create --replication-factor 3 --partitions 8 --topic test --zookeeper $KAFKAZKHOSTS
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
    /usr/hdp/current/kafka-broker/bin/kafka-console-consumer.sh --bootstrap-server $KAFKABROKERS --topic test --from-beginning
    ```
   
    このコマンドにより、レコードがトピックから取得され、表示されます。 `--from-beginning` を使用することで、すべてのレコードが取得されるように、コンシューマーにストリームの先頭から開始するように指示しています。

3. __Ctrl+C__ キーを使用してコンシューマーを停止します。

## <a name="producer-and-consumer-api"></a>プロデューサーとコンシューマーの API

[Kafka の API](http://kafka.apache.org/documentation#api) を使用して、レコードをプログラムにより生成および消費することもできます。 Java のプロデューサーとコンシューマーをビルドするには、開発環境から次の手順を実行します。

> [!IMPORTANT]
> 開発環境に、次のコンポーネントがインストールされている必要があります。
>
> * [Java JDK 8](http://www.oracle.com/technetwork/java/javase/downloads/index.html) または同等の OpenJDK など。
>
> * [Apache Maven](http://maven.apache.org/)
>
> * SSH クライアントと `scp` コマンド。 詳細については、[HDInsight での SSH の使用](hdinsight-hadoop-linux-use-ssh-unix.md)に関するドキュメントを参照してください。

1. [https://github.com/Azure-Samples/hdinsight-kafka-java-get-started](https://github.com/Azure-Samples/hdinsight-kafka-java-get-started) からサンプルをダウンロードします。 プロデューサーとコンシューマーのサンプルには、`Producer-Consumer` ディレクトリ内のプロジェクトを使用します。 このサンプルには次のクラスが含まれています。
   
    * **Run** - コンシューマーまたはプロデューサーを開始します。

    * **Producer** - 1,000,000 件のレコードをトピックに格納します。

    * **Consumer** - トピックからレコードを読み取ります。

2. jar パッケージを作成するには、`Producer-Consumer` ディレクトリに移動し、次のコマンドを使用します。

    ```
    mvn clean package
    ```

    このコマンドにより、`kafka-producer-consumer-1.0-SNAPSHOT.jar` というファイルを含む `target` という名前のディレクトリが作成されます。

3. 次のコマンドを使用して、HDInsight クラスターに `kafka-producer-consumer-1.0-SNAPSHOT.jar` ファイルをコピーします。
   
    ```bash
    scp ./target/kafka-producer-consumer-1.0-SNAPSHOT.jar SSHUSER@CLUSTERNAME-ssh.azurehdinsight.net:kafka-producer-consumer.jar
    ```
   
    **SSHUSER** は、クラスターの SSH ユーザーに置き換えます。また、**CLUSTERNAME** はクラスターの名前に置き換えます。 メッセージが表示されたら、SSH ユーザーのパスワードを入力します。

4. `scp` コマンドによるファイルのコピーが完了したら、SSH を使用してクラスターに接続します。 次のコマンドを使用し、テスト トピックにレコードを書き込みます。

    ```bash
    java -jar kafka-producer-consumer.jar producer $KAFKABROKERS
    ```

5. プロセスが完了したら、次のコマンドを使用してトピックから読み取りを行います。
   
    ```bash
    java -jar kafka-producer-consumer.jar consumer $KAFKABROKERS
    ```
   
    読み取られたレコードが、レコードの件数とともに表示されます。 前の手順でスクリプトを使用してトピックに数件のレコードを送信したため、1,000,000 件より少し多いレコードが表示される可能性があります。

6. __Ctrl+C__ キーを使用してコンシューマーを終了します。

### <a name="multiple-consumers"></a>複数のコンシューマー

Kafka コンシューマーは、レコードを読み取る際に、コンシューマー グループを使用します。 複数のコンシューマーで同じグループを使用すると、トピックからの読み取りの負荷が分散されます。 グループ内の各コンシューマーは、レコードの一部を受け取ります。 このプロセスを実際に確認するには、次の手順に従います。

1. クラスターへの新しい SSH セッションを開き、2 つのセッションを用意します。 各セッションで次のコマンドを使用して、コンシューマー グループ ID が同じコンシューマーを起動します。
   
    ```bash
    java -jar kafka-producer-consumer.jar consumer $KAFKABROKERS mygroup
    ```

    このコマンドは、グループ ID `mygroup` を使用してコンシューマーを起動します。

    > [!NOTE]
    > 「[Zookeeper およびブローカーのホスト情報を取得する](#getkafkainfo)」セクションのコマンドを使用して、この SSH セッションの `$KAFKABROKERS` を設定してください。

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

    ```bash
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
    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --create --replication-factor 3 --partitions 8 --topic wordcounts --zookeeper $KAFKAZKHOSTS
    ```

5. 次に、次のコマンドを使用してストリーミング プロセスを開始します。
   
    ```bash
    java -jar kafka-streaming.jar $KAFKABROKERS $KAFKAZKHOSTS 2>/dev/null &
    ```
   
    このコマンドにより、バックグラウンドでストリーミング プロセスが開始されます。

6. 次のコマンドを使用して、`test` トピックにメッセージを送信します。 これらのメッセージはストリーミング サンプルで処理されます。
   
    ```bash
    java -jar kafka-producer-consumer.jar producer $KAFKABROKERS &>/dev/null &
    ```

7. 次のコマンドを使用して、ストリーミング プロセスによって `wordcounts` トピックに書き込まれた出力を表示します。
   
    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-console-consumer.sh --bootstrap-server $KAFKABROKERS --topic wordcounts --from-beginning --formatter kafka.tools.DefaultMessageFormatter --property print.key=true --property key.deserializer=org.apache.kafka.common.serialization.StringDeserializer --property value.deserializer=org.apache.kafka.common.serialization.LongDeserializer
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

## <a name="data-high-availability"></a>データの高可用性

各 Azure リージョン (場所) は "_障害ドメイン_" を提供します。 障害ドメインとは、Azure データ センター内にある基になるハードウェアの論理的なグループです。 各障害ドメインは、一般的な電源とネットワーク スイッチを共有します。 HDInsight クラスター内のノードを実装する仮想マシンと管理ディスクは、これらの障害ドメインに分散されます。 このアーキテクチャにより、物理的なハードウェア障害の潜在的な影響が制限されます。

リージョン内の障害ドメインの数については、[Linux 仮想マシンの可用性](../virtual-machines/linux/manage-availability.md#use-managed-disks-for-vms-in-an-availability-set)に関するトピックを参照してください。

> [!IMPORTANT]
> 3 つの障害ドメインを含む Azure リージョンを使用することと、レプリケーション係数として 3 を使用することをお勧めします。

障害ドメインが 2 つだけのリージョンを使用する必要がある場合は、レプリケーション係数として 4 を使用して、レプリカを 2 つの障害ドメインに均等に分散します。

### <a name="kafka-and-fault-domains"></a>Kafka と障害ドメイン

Kafka は、障害ドメインを認識しません。 トピック用にパーティションのレプリカを作成すると、レプリカが適切に分散されず、高可用性が実現しない場合があります。 高可用性を確保するには、[Kafka パーティション再調整ツール](https://github.com/hdinsight/hdinsight-kafka-tools)を使用してください。 このツールは、SSH セッションから Kafka クラスターのヘッド ノードに対して実行する必要があります。

Kafka データの最高レベルの可用性を確保するには、次のタイミングでトピックのパーティションのレプリカを再調整する必要があります。

* 新しいトピックまたはパーティションが作成されたとき

* クラスターをスケールアップするとき

## <a name="delete-the-cluster"></a>クラスターを削除する

[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

## <a name="troubleshoot"></a>トラブルシューティング

HDInsight クラスターの作成で問題が発生した場合は、「[アクセス制御の要件](hdinsight-administer-use-portal-linux.md#create-clusters)」を参照してください。

## <a name="next-steps"></a>次のステップ

このドキュメントでは、HDInsight で Apache Kafka を使用する際の基本事項を学習しました。 次の各ドキュメントを参考に、Kafka の使用の詳細を確認してください。

* [Kafka ログの分析](apache-kafka-log-analytics-operations-management.md)
* [Kafka クラスター間でデータをレプリケートする](hdinsight-apache-kafka-mirroring.md)
* [HDInsight 上で Kafka を用いて Apache Spark ストリーミング (DStream) を使用する](hdinsight-apache-spark-with-kafka.md)
* [HDInsight 上で Kafka を用いて Apache Spark 構造化ストリーミングを使用する](hdinsight-apache-kafka-spark-structured-streaming.md)
* [HDInsight での Kafka に Apache Storm を使用する](hdinsight-apache-storm-with-kafka.md)
* [Azure Virtual Network 経由で Kafka に接続する](hdinsight-apache-kafka-connect-vpn-gateway.md)
