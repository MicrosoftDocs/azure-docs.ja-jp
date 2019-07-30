---
title: Azure portal を使用して HDInsight に Apache Kafka を設定する - クイック スタート
description: このクイックス タートでは、Azure portal を使って Azure HDInsight に Apache Kafka クラスターを作成する方法を説明します。 Kafka のトピック、サブスクライバー、およびコンシューマーについても説明します。
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.custom: mvc
ms.topic: quickstart
ms.date: 06/12/2019
ms.openlocfilehash: 61ae6cdf7c31c9a6e40860eb1dc4628bb2d37496
ms.sourcegitcommit: 6e6813f8e5fa1f6f4661a640a49dc4c864f8a6cb
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/17/2019
ms.locfileid: "67150909"
---
# <a name="quickstart-create-apache-kafka-cluster-in-azure-hdinsight-using-azure-portal"></a>クイック スタート:Azure portal を使用して Azure HDInsight 内に Apache Kafka クラスターを作成する

Apache Kafka は、オープンソースの分散ストリーミング プラットフォームです。 発行/サブスクライブ メッセージ キューと同様の機能を備えているため、メッセージ ブローカーとして多く使われています。 

このクイックスタートでは、Azure Portal を使用して [Apache Kafka](https://kafka.apache.org) クラスターを作成する方法について説明します。 Apache Kafka を使って、付属のユーティリティでメッセージを送受信する方法についても説明します。

[!INCLUDE [delete-cluster-warning](../../../includes/hdinsight-delete-cluster-warning.md)]

Apache Kafka API は、同じ仮想ネットワーク内のリソースによってのみアクセスできます。 このクイック スタートでは、SSH を使って直接クラスターにアクセスします。 他のサービス、ネットワーク、または仮想マシンを Apache Kafka に接続するには、まず、仮想ネットワークを作成してから、ネットワーク内にリソースを作成する必要があります。 詳しくは、[仮想ネットワークを使用した Apache Kafka への接続](apache-kafka-connect-vpn-gateway.md)に関するドキュメントをご覧ください。

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。

## <a name="prerequisites"></a>前提条件

SSH クライアント 詳細については、[SSH を使用して HDInsight (Apache Hadoop) に接続する方法](../hdinsight-hadoop-linux-use-ssh-unix.md)に関するページを参照してください。

## <a name="create-an-apache-kafka-cluster"></a>Apache Kafka クラスターを作成する

HDInsight クラスターで Apache Kafka を作成するには、次の手順に従います。

1. [Azure Portal](https://portal.azure.com) にサインインします。

2. 左側のメニューから、 **[+ リソースの作成]**  >  **[Analytics]**  >  **[HDInsight]** に移動します。
   
    ![HDInsight クラスターの作成](./media/apache-kafka-get-started/create-hdinsight.png)

3. **[基本]** から次の情報を入力するか、選択します。

    | Setting | 値 |
    | --- | --- |
    | クラスター名 | HDInsight クラスターの一意の名前。 |
    | サブスクリプション | サブスクリプションを選択します。 |
    
   __[クラスターの種類]__ を選択して **[クラスターの構成]** を表示します。
   
   ![HDInsight 基本構成の Apache Kafka クラスター](./media/apache-kafka-get-started/custom-basics-kafka.png)

4. __[クラスターの構成]__ から、次の値を選択します。

    | Setting | 値 |
    | --- | --- |
    | クラスターの種類 | Kafka |
    | バージョン | Kafka 1.1.0 (HDI 3.6) |

    **[選択]** を選択してクラスターの種類の設定を保存し、 __[基本]__ に戻ります。

    ![クラスターの種類の選択](./media/apache-kafka-get-started/kafka-cluster-type.png)

5. __[基本]__ から次の情報を入力するか、選択します。

    | Setting | 値 |
    | --- | --- |
    | クラスター ログイン ユーザー名 | クラスターでホストされている Web サービスまたは REST API にアクセスするときのログイン名です。 既定値 (admin) を維持します。 |
    | クラスター ログイン パスワード | クラスターでホストされている Web サービスまたは REST API にアクセスするときのログイン パスワードです。 |
    | Secure Shell (SSH) ユーザー名 | SSH 経由でクラスターにアクセスする際に使用されるログイン。 既定では、このパスワードは、クラスター ログイン パスワードと同じです。 |
    | リソース グループ | クラスターが作成されるリソース グループ。 |
    | Location | クラスターが作成される Azure リージョン。 |

    各 Azure リージョン (場所) は "_障害ドメイン_" を提供します。 障害ドメインとは、Azure データ センター内にある基になるハードウェアの論理的なグループです。 各障害ドメインは、一般的な電源とネットワーク スイッチを共有します。 HDInsight クラスター内のノードを実装する仮想マシンと管理ディスクは、これらの障害ドメインに分散されます。 このアーキテクチャにより、物理的なハードウェア障害の潜在的な影響が制限されます。

    データの高可用性を実現するために、__3 つの障害ドメイン__ を含むリージョン (場所) を選択します。 リージョン内の障害ドメインの数については、[Linux 仮想マシンの可用性](../../virtual-machines/windows/manage-availability.md#use-managed-disks-for-vms-in-an-availability-set)に関するトピックを参照してください。

   ![サブスクリプションを選択します。](./media/apache-kafka-get-started/hdinsight-basic-configuration-2.png)

    __[次へ]__ を選択して、基本的な構成を完了します。

6. このクイック スタートでは、既定のセキュリティ設定をそのまま使用します。 Enterprise セキュリティ パッケージの詳細については、「[Azure Active Directory Domain Services を使用して、Enterprise セキュリティ パッケージで HDInsight クラスターを構成する](../domain-joined/apache-domain-joined-configure-using-azure-adds.md)」を参照してください。 Apache Kafka のディスク暗号化に独自のキーを使用する方法については、「[Azure HDInsight で Apache Kafka 用に自分のキーを持ち込む](apache-kafka-byok.md)」を参照してください。

   クラスターを仮想ネットワークに接続したい場合は、 **[仮想ネットワーク]** ボックスの一覧からいずれかの仮想ネットワークを選択します。

   ![仮想ネットワークにクラスターを追加する](./media/apache-kafka-get-started/kafka-security-config.png)

7. **[ストレージ]** で、ストレージ アカウントを選択または作成します。 このドキュメントの手順では、他のフィールドを既定値のままにします。 __[次へ]__ ボタンを使用して、ストレージの構成を保存します。 Data Lake Storage Gen2 の使用の詳細については、「[クイック スタート:HDInsight のクラスターを設定する](../../storage/data-lake-storage/quickstart-create-connect-hdi-cluster.md)」をご覧ください。

   ![HDInsight のストレージ アカウント設定](./media/apache-kafka-get-started/storage-configuration.png)

8. __[アプリケーション (オプション)]__ で __[次へ]__ を選択して、既定の設定で続行します。

9. __[クラスター サイズ]__ で __[次へ]__ を選択して、既定の設定で続行します。

    HDInsight 上の Apache Kafka の可用性を確保するため、__worker ノード数__ エントリを 3 以上に設定する必要があります。 既定値は 4 ですが、

    **ワーカー ノードごとのディスクの数**は、HDInsight における Apache Kafka のスケーラビリティを構成する項目です。 HDInsight 上の Apache Kafka は、クラスターの仮想マシンのローカル ディスクを使って、データを保存します。 Apache Kafka は I/O が多いため、[Azure Managed Disks](../../virtual-machines/windows/managed-disks-overview.md) を使ってノードごとに高いスループットと多くの記憶域を提供します。 マネージド ディスクの種類は、__Standard__ (HDD) または __Premium__ (SSD) です。 ディスクの種類は、ワーカー ノード (Apache Kafka ブローカー) によって使われる VM のサイズによって異なります。 DS および GS シリーズの VM では、Premium ディスクが自動的に使われます。 他の種類の VM はすべて Standard を使います。

   ![Apache Kafka のクラスター サイズの設定](./media/apache-kafka-get-started/kafka-cluster-size.png)

10. __[詳細設定]__ で __[次へ]__ を選択し、既存の設定で続行します。

11. **[概要]** でクラスターの構成を確認します。 間違った設定を変更するには、 __[編集]__ リンクを使用します。 最後に、 **[作成]** を選択してクラスターを作成します。

    ![クラスター構成の概要](./media/apache-kafka-get-started/kafka-configuration-summary.png)

    クラスターの作成には最大で 20 分かかります。

## <a name="connect-to-the-cluster"></a>クラスターへの接続

1. Apache Kafka クラスターのプライマリ ヘッド ノードに接続するには、次のコマンドを使用します。 `sshuser` を SSH ユーザー名で置き換えます。 `mykafka` を実際の Apache Kafka クラスターの名前に置き換えます。

    ```bash
    ssh sshuser@mykafka-ssh.azurehdinsight.net
    ```

2. クラスターに初めて接続すると、ホストの信頼性を確立できないという警告が SSH クライアントに表示されることがあります。 プロンプトが表示されたら、「__yes__」と入力して、__Enter__ キーを押し、SSH クライアントの信頼済みサーバーの一覧にこのホストを追加します。

3. メッセージが表示されたら、SSH ユーザーのパスワードを入力します。

    接続されると、次のテキストのような情報が表示されます。
    
    ```text
    Authorized uses only. All activity may be monitored and reported.
    Welcome to Ubuntu 16.04.4 LTS (GNU/Linux 4.13.0-1011-azure x86_64)
    
     * Documentation:  https://help.ubuntu.com
     * Management:     https://landscape.canonical.com
     * Support:        https://ubuntu.com/advantage
    
      Get cloud support with Ubuntu Advantage Cloud Guest:
        https://www.ubuntu.com/business/services/cloud
    
    83 packages can be updated.
    37 updates are security updates.


    Welcome to Apache Kafka on HDInsight.
    
    Last login: Thu Mar 29 13:25:27 2018 from 108.252.109.241
    ssuhuser@hn0-mykafk:~$
    ```

## <a id="getkafkainfo"></a>Apache Zookeeper およびブローカーのホスト情報を取得する

Kafka を使うときは、*Apache Zookeeper* ホストと "*ブローカー*" ホストについて理解しておく必要があります。 これらのホストは、Apache Kafka の API や、Kafka に付属するユーティリティの多くで使用されます。

このセクションでは、クラスターで Apache Ambari REST API からホスト情報を取得します。

1. コマンド ライン JSON プロセッサの [jq](https://stedolan.github.io/jq/) をインストールします。 このユーティリティは JSON ドキュメントを解析するためのものであり、ホスト情報の解析に役立ちます。 開いた SSH 接続から次のコマンドを入力して、`jq` をインストールします。
   
    ```bash
    sudo apt -y install jq
    ```

2. 環境変数を設定します。 `PASSWORD` と `CLUSTERNAME` をそれぞれクラスター ログイン パスワードとクラスター名に置き換えた後、コマンドを入力します。

    ```bash
    export password='PASSWORD'
    export clusterNameA='CLUSTERNAME'
    ```

3. 大文字と小文字が正しく区別されたクラスター名を抽出します。 クラスターの作成方法によっては、クラスター名の実際の大文字小文字の区別が予想と異なる場合があります。 このコマンドでは、実際の大文字と小文字の使い分けが取得され、変数に格納された後、正しい大文字と小文字の名前と、前に指定した名前が表示されます。 次のコマンドを入力します。

    ```bash
    export clusterName=$(curl -u admin:$password -sS -G "https://$clusterNameA.azurehdinsight.net/api/v1/clusters" | jq -r '.items[].Clusters.cluster_name')
    echo $clusterName, $clusterNameA
    ```

4. Zookeeper ホスト情報で環境変数を設定するには、次のコマンドを使用します。 このコマンドはすべての Zookeeper ホストを取得してから、最初の 2 つのエントリのみを返します。 これは、1 つのホストに到達できない場合に、いくらかの冗長性が必要なためです。

    ```bash
    export KAFKAZKHOSTS=`curl -sS -u admin:$password -G http://headnodehost:8080/api/v1/clusters/$clusterName/services/ZOOKEEPER/components/ZOOKEEPER_SERVER | jq -r '["\(.host_components[].HostRoles.host_name):2181"] | join(",")' | cut -d',' -f1,2`
    ```

    このコマンドは、クラスター ヘッド ノード上の Ambari サービスを直接クエリします。 `https://$CLUSTERNAME.azurehdinsight.net:80/` のパブリック アドレスを使用して Ambari にアクセスすることもできます。 一部のネットワーク構成は、パブリック アドレスへのアクセスを阻止します。 たとえば、ネットワーク セキュリティ グループ (NSG) を使用して、仮想ネットワークで HDInsight へのアクセスを制限します。

5. 環境変数が正しく設定されていることを確認するには、次のコマンドを使用します。

    ```bash
    echo $KAFKAZKHOSTS
    ```

    このコマンドでは、次のテキストのような情報が返されます。

    `zk0-kafka.eahjefxxp1netdbyklgqj5y1ud.ex.internal.cloudapp.net:2181,zk2-kafka.eahjefxxp1netdbyklgqj5y1ud.ex.internal.cloudapp.net:2181`

6. Apache Kafka ブローカー ホスト情報で環境変数を設定するには、次のコマンドを使用します。

    ```bash
    export KAFKABROKERS=`curl -sS -u admin:$password -G http://headnodehost:8080/api/v1/clusters/$clusterName/services/KAFKA/components/KAFKA_BROKER | jq -r '["\(.host_components[].HostRoles.host_name):9092"] | join(",")' | cut -d',' -f1,2`
    ```

7. 環境変数が正しく設定されていることを確認するには、次のコマンドを使用します。

    ```bash   
    echo $KAFKABROKERS
    ```

    このコマンドでは、次のテキストのような情報が返されます。
   
    `wn1-kafka.eahjefxxp1netdbyklgqj5y1ud.cx.internal.cloudapp.net:9092,wn0-kafka.eahjefxxp1netdbyklgqj5y1ud.cx.internal.cloudapp.net:9092`

## <a name="manage-apache-kafka-topics"></a>Apache Kafka トピックの管理

Kafka は、"*トピック*" にデータのストリームを格納します。 `kafka-topics.sh` ユーティリティを使って、トピックを管理できます。

* **トピックを作成するには**、SSH 接続で次のコマンドを使います。

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --create --replication-factor 3 --partitions 8 --topic test --zookeeper $KAFKAZKHOSTS
    ```

    このコマンドを使用すると、`$KAFKAZKHOSTS` に格納されているホスト情報を使用して Zookeeper に接続されます。 次に、**test** という名前の Apache Kafka トピックが作成されます。 

    * このトピックに格納されるデータは、8 つのパーティションに分割されます。

    * 各パーティションは、クラスター内の 3 つのワーカー ノード間でレプリケートされます。

        3 つの障害ドメインを提供する Azure リージョンにクラスターを作成した場合は、3 のレプリケーション係数を使います。 そうでない場合は、4 のレプリケーション係数を使います。
        
        3 つの障害ドメインがあるリージョンでは、3 のレプリケーション係数により、レプリカを障害ドメインに分散できます。 2 つの障害ドメインのリージョンでは、4 のレプリケーション係数により、ドメイン全体に均等にレプリカが分散されます。
        
        リージョン内の障害ドメインの数については、[Linux 仮想マシンの可用性](../../virtual-machines/windows/manage-availability.md#use-managed-disks-for-vms-in-an-availability-set)に関するトピックを参照してください。

        Apache Kafka は、Azure 障害ドメインを認識しません。 トピック用にパーティションのレプリカを作成すると、レプリカが適切に分散されず、高可用性が実現しない場合があります。

        高可用性を確保するには、[Apache Kafka パーティション再調整ツール](https://github.com/hdinsight/hdinsight-kafka-tools)を使用してください。 このツールは、Apache Kafka クラスターのヘッド ノードへの SSH 接続から実行する必要があります。

        Apache Kafka データの最高の可用性のために、次のときにトピックのパーティションのレプリカを再調整する必要があります。

        * 新しいトピックまたはパーティションの作成

        * クラスターのスケールアップ

* **トピックの一覧を表示するには**、次のコマンドを使います。

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --list --zookeeper $KAFKAZKHOSTS
    ```

    このコマンドは、Apache Kafka クラスターで使用可能なトピックの一覧を表示します。

* **トピックを削除するには**、次のコマンドを使います。

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --delete --topic topicname --zookeeper $KAFKAZKHOSTS
    ```

    このコマンドは、`topicname` という名前のトピックを削除します。

    > [!WARNING]  
    > 前に作成した `test` トピックを削除した場合は、再作しなおす必要があります。 このドキュメントの後半で使います。

`kafka-topics.sh` ユーティリティで使用可能なコマンドの詳細については、次のコマンドを使用します。

```bash
/usr/hdp/current/kafka-broker/bin/kafka-topics.sh
```

## <a name="produce-and-consume-records"></a>レコードの生成および消費

Kafka では、トピック内に*レコード*が格納されます。 レコードは、*プロデューサー*によって生成され、*コンシューマー*によって消費されます。 プロデューサーとコンシューマーは "*Kafka ブローカー*" サービスと通信します。 HDInsight クラスターの各ワーカー ノードが、Apache Kafka ブローカー ホストです。

先ほど作成した test トピックにレコードを格納し、コンシューマーを使用してそれらを読み取るには、次の手順に従います。

1. トピックにレコードを書き込むには、SSH 接続から `kafka-console-producer.sh` ユーティリティを使用します。
   
    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-console-producer.sh --broker-list $KAFKABROKERS --topic test
    ```
   
    このコマンドの後ろには空の行があります。

2. 空の行にテキスト メッセージを入力して、Enter キーを押します。 このようにメッセージをいくつか入力してから、**Ctrl + C** キーを使用して通常のプロンプトに戻ります。 各行が、個別のレコードとして Apache Kafka トピックに送信されます。

3. トピックからレコードを読み取るには、SSH 接続から `kafka-console-consumer.sh` ユーティリティを使用します。

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-console-consumer.sh --bootstrap-server $KAFKABROKERS --topic test --from-beginning
    ```

    このコマンドにより、レコードがトピックから取得され、表示されます。 `--from-beginning` を使用することで、すべてのレコードが取得されるように、コンシューマーにストリームの先頭から開始するように指示しています。

    以前のバージョンの Kafka を使っている場合、`--bootstrap-server $KAFKABROKERS` を `--zookeeper $KAFKAZKHOSTS` に置き換えます。

4. __Ctrl+C__ キーを使用してコンシューマーを停止します。

プロデューサーとコンシューマーをプログラムから作成することもできます。 この API の使用例については、[HDInsight における Apache Kafka Producer API と Consumer API](apache-kafka-producer-consumer-api.md) に関するドキュメントを参照してください。

## <a name="clean-up-resources"></a>リソースのクリーンアップ

このクイックスタートで作成したリソースをクリーンアップするために、リソース グループを削除できます。 リソース グループを削除すると、関連付けられている HDInsight クラスター、およびリソース グループに関連付けられているその他のリソースも削除されます。

Azure Portal を使用してリソース グループを削除するには:

1. Azure Portal で左側のメニューを展開してサービスのメニューを開き、 __[リソース グループ]__ を選択して、リソース グループの一覧を表示します。
2. 削除するリソース グループを見つけて、一覧の右側にある __[詳細]__ ボタン ([...]) を右クリックします。
3. __[リソース グループの削除]__ を選択し、確認します。

> [!WARNING]  
> HDInsight クラスターの課金は、クラスターが作成されると開始し、クラスターが削除されると停止します。 課金は分単位なので、クラスターを使わなくなったら必ず削除してください。
> 
> HDInsight クラスター上の Apache Kafka を削除すると、Kafka に格納されているすべてのデータが削除されます。

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [Apache Kafka で Apache Spark を使用する](../hdinsight-apache-kafka-spark-structured-streaming.md)
