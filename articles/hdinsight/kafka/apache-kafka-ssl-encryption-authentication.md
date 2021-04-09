---
title: Apache Kafka TLS 暗号化および認証 - Azure HDInsight
description: Kafka ブローカー間と、Kafka クライアントと Kafka ブローカー間の通信用に TLS 暗号化を設定します。 クライアントの SSL 認証を設定します。
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 05/01/2019
ms.openlocfilehash: d061832022b983e4d5fd55e72c1d4789b82f6633
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/23/2021
ms.locfileid: "104863226"
---
# <a name="set-up-tls-encryption-and-authentication-for-apache-kafka-in-azure-hdinsight"></a>Azure HDInsight で Apache Kafka 用に TLS 暗号化および認証を設定する

この記事では、Apache Kafka クライアントと Apache Kafka ブローカーの間に、トランスポート層セキュリティ (TLS) 暗号化 (旧称 Secure Sockets Layer (SSL) 暗号化) を設定する方法について説明します。 クライアントの認証を設定する方法についても説明します (双方向 TLS とも呼ばれます)。

> [!Important]
> Kafka アプリケーションでは、Java クライアントとコンソール クライアントの 2 種類のクライアントを使用できます。 生成と使用の両方に TLS を使用できるのは、Java クライアント `ProducerConsumer.java` のみです。 TLS では、コンソール プロデューサー クライアント `console-producer.sh` は機能しません。

> [!Note]
> バージョン 1.1 の HDInsight Kafka コンソール プロデューサーでは、SSL はサポートされていません。

## <a name="apache-kafka-broker-setup"></a>Apache Kafka ブローカーのセットアップ

Kafka TLS ブローカーのセットアップでは、4 つの HDInsight クラスター VM が次のように使用されます。

* ヘッドノード 0 - 証明機関 (CA)
* ワーカー ノード 0、1、2 - ブローカー

> [!Note] 
> このガイドでは自己署名証明書を使用しますが、最も安全なソリューションは、信頼された CA によって発行された証明書を使うことです。

ブローカーのセットアップ プロセスの概要は次のとおりです。

1. 次の手順を 3 つのワーカー ノードのそれぞれで繰り返します。

    1. 証明書を作成します。
    1. 証明書署名要求を作成します。
    1. 証明書署名要求を証明機関 (CA) に送信します。
    1. CA にサインインし、要求に署名します。
    1. 署名済みの証明書をワーカー ノードに SCP で送り返します。
    1. CA の公開証明書をワーカー ノードに SCP で送信します。

1. すべての証明書が揃ったら、証明書を証明書ストアに格納します。
1. Ambari に移動し、構成を変更します。

次の詳細な手順を使って、ブローカーのセットアップを完了します。

> [!Important]
> 次のコード スニペットで、wnX は 3 つのワーカー ノードの 1 つの省略形であり、`wn0`、`wn1`、または `wn2` のいずれか適切なものに置き換える必要があります。 `WorkerNode0_Name` と `HeadNode0_Name` はそれぞれのコンピューターの名前に置き換える必要があります。

1. ヘッド ノード 0 で初期セットアップを実行します。HDInsight の場合は、証明機関 (CA) のロールを設定します。

    ```bash
    # Create a new directory 'ssl' and change into it
    mkdir ssl
    cd ssl
    ```

1. 各ブローカー (ワーカー ノード 0、1、2) で同じ初期セットアップを実行します。

    ```bash
    # Create a new directory 'ssl' and change into it
    mkdir ssl
    cd ssl
    ```

1. 各ワーカー ノードで、次のコード スニペットを使って以下の手順を実行します。
    1. キーストアを作成し、新しいプライベート証明書を設定します。
    1. 証明書署名要求を作成します。
    1. 証明書署名要求を CA (headnode0) に SCP で送信します

    ```bash
    keytool -genkey -keystore kafka.server.keystore.jks -validity 365 -storepass "MyServerPassword123" -keypass "MyServerPassword123" -dname "CN=FQDN_WORKER_NODE" -storetype pkcs12
    keytool -keystore kafka.server.keystore.jks -certreq -file cert-file -storepass "MyServerPassword123" -keypass "MyServerPassword123"
    scp cert-file sshuser@HeadNode0_Name:~/ssl/wnX-cert-sign-request
    ```

1. CA コンピューター上で、次のコマンドを実行して CA 証明書と CA キー ファイルを作成します。

    ```bash
    openssl req -new -newkey rsa:4096 -days 365 -x509 -subj "/CN=Kafka-Security-CA" -keyout ca-key -out ca-cert -nodes
    ```

1. CA のコンピューターに移動し、受け取ったすべての証明書署名要求に署名します。

    ```bash
    openssl x509 -req -CA ca-cert -CAkey ca-key -in wn0-cert-sign-request -out wn0-cert-signed -days 365 -CAcreateserial -passin pass:"MyServerPassword123"
    openssl x509 -req -CA ca-cert -CAkey ca-key -in wn1-cert-sign-request -out wn1-cert-signed -days 365 -CAcreateserial -passin pass:"MyServerPassword123"
    openssl x509 -req -CA ca-cert -CAkey ca-key -in wn2-cert-sign-request -out wn2-cert-signed -days 365 -CAcreateserial -passin pass:"MyServerPassword123"
    ```

1. CA (headnode0) からワーカー ノードに、署名済みの証明書を返送します。

    ```bash
    scp wn0-cert-signed sshuser@WorkerNode0_Name:~/ssl/cert-signed
    scp wn1-cert-signed sshuser@WorkerNode1_Name:~/ssl/cert-signed
    scp wn2-cert-signed sshuser@WorkerNode2_Name:~/ssl/cert-signed
    ```

1. CA の公開証明書を各ワーカー ノードに送信します。

    ```bash
    scp ca-cert sshuser@WorkerNode0_Name:~/ssl/ca-cert
    scp ca-cert sshuser@WorkerNode1_Name:~/ssl/ca-cert
    scp ca-cert sshuser@WorkerNode2_Name:~/ssl/ca-cert
    ```

1. 各ワーカー ノードで、CA の公開証明書をトラストストアとキーストアに追加します。 次に、ワーカー ノード自体の署名済み証明書をキーストアに追加します

    ```bash
    keytool -keystore kafka.server.truststore.jks -alias CARoot -import -file ca-cert -storepass "MyServerPassword123" -keypass "MyServerPassword123" -noprompt
    keytool -keystore kafka.server.keystore.jks -alias CARoot -import -file ca-cert -storepass "MyServerPassword123" -keypass "MyServerPassword123" -noprompt
    keytool -keystore kafka.server.keystore.jks -import -file cert-signed -storepass "MyServerPassword123" -keypass "MyServerPassword123" -noprompt

    ```

## <a name="update-kafka-configuration-to-use-tls-and-restart-brokers"></a>TLS を使用してブローカーを再起動するように Kafka 構成を更新する

キーストアとトラストストアを使用して各 Kafka ブローカーを設定し、適切な証明書をインポートしました。 次に、Ambari を使用して関連の Kafka 構成プロパティを変更して、Kafka ブローカーを再起動します。

構成の変更を完了するために、次の手順を実行します。

1. Azure portal にサインインし、ご利用の Azure HDInsight Apache Kafka クラスターを選択します。
1. **[クラスター ダッシュボード]** の **[Ambari ホーム]** をクリックして、Ambari UI に移動します。
1. **[Kafka Broker]/(Kafka ブローカー/)** で、**リスナー** プロパティを `PLAINTEXT://localhost:9092,SSL://localhost:9093` に設定します。
1. **[Advanced kafka-broker]\(Kafka ブローカーの高度な設定\)** で **security.inter.broker.protocol** プロパティを `SSL` に設定します。

    :::image type="content" source="./media/apache-kafka-ssl-encryption-authentication/editing-configuration-ambari.png" alt-text="Ambari での Kafka ssl 構成プロパティの編集" border="true":::

1. **Custom kafka-broker\(カスタム kafka ブローカー)** で **ssl.client.auth** プロパティを `required` に設定します。 この手順は、認証と暗号化を設定する場合にのみ必要です。

    :::image type="content" source="./media/apache-kafka-ssl-encryption-authentication/editing-configuration-ambari2.png" alt-text="Ambari での kafka ssl 構成プロパティの編集" border="true":::

1. HDI バージョン 3.6 の場合は、Ambari UI にアクセスし、**Advanced kafka-env** と **kafka-env テンプレート** プロパティの下に次の構成を追加します。

    ```bash
    # Configure Kafka to advertise IP addresses instead of FQDN
    IP_ADDRESS=$(hostname -i)
    echo advertised.listeners=$IP_ADDRESS
    sed -i.bak -e '/advertised/{/advertised@/!d;}' /usr/hdp/current/kafka-broker/conf/server.properties
    echo "advertised.listeners=PLAINTEXT://$IP_ADDRESS:9092,SSL://$IP_ADDRESS:9093" >> /usr/hdp/current/kafka-broker/conf/server.properties
    echo "ssl.keystore.location=/home/sshuser/ssl/kafka.server.keystore.jks" >> /usr/hdp/current/kafka-broker/conf/server.properties
    echo "ssl.keystore.password=MyServerPassword123" >> /usr/hdp/current/kafka-broker/conf/server.properties
    echo "ssl.key.password=MyServerPassword123" >> /usr/hdp/current/kafka-broker/conf/server.properties
    echo "ssl.truststore.location=/home/sshuser/ssl/kafka.server.truststore.jks" >> /usr/hdp/current/kafka-broker/conf/server.properties
    echo "ssl.truststore.password=MyServerPassword123" >> /usr/hdp/current/kafka-broker/conf/server.properties
    ```

1. これらの変更を含む Ambari 構成 UI を示すスクリーンショットを次に示します。

    HDI バージョン 3.6 の場合:

    :::image type="content" source="./media/apache-kafka-ssl-encryption-authentication/editing-configuration-kafka-env.png" alt-text="Ambari 上で kafka-env テンプレート プロパティを編集する" border="true":::

    HDI バージョン 4.0 の場合:

     :::image type="content" source="./media/apache-kafka-ssl-encryption-authentication/editing-configuration-kafka-env-four.png" alt-text="Ambari four 上で kafka-env テンプレート プロパティを編集する" border="true":::

1. すべての Kafka ブローカーを再起動します。

## <a name="client-setup-without-authentication"></a>クライアントのセットアップ (認証なし)

認証が必要ない場合に TLS 暗号化のみを設定する手順の概要は次のとおりです。

1. CA (アクティブ ヘッド ノード) にサインインします。
1. CA マシン (wn0) からクライアント マシンに CA 証明書をコピーします。
1. クライアント マシン (hn1) にサインインし、`~/ssl` フォルダーに移動します。
1. トラストストアに CA 証明書をインポートします。
1. キーストアに CA 証明書をインポートします。

これらの手順の詳細については、次のコード スニペットを参考にしてください。

1. CA ノードにサインインします。

    ```bash
    ssh sshuser@HeadNode0_Name
    cd ssl
    ```

1. CA 証明書をクライアント マシンにコピーします。

    ```bash
    scp ca-cert sshuser@HeadNode1_Name:~/ssl/ca-cert
    ```

1. クライアント コンピューター (スタンバイ ヘッド ノード) にサインインします。

    ```bash
    ssh sshuser@HeadNode1_Name
    cd ssl
    ```

1. トラストストアに CA 証明書をインポートします。

    ```bash
    keytool -keystore kafka.client.truststore.jks -alias CARoot -import -file ca-cert -storepass "MyClientPassword123" -keypass "MyClientPassword123" -noprompt
    ```

1. キーストアに CA 証明書をインポートします。
    
    ```bash
    keytool -keystore kafka.client.keystore.jks -alias CARoot -import -file ca-cert -storepass "MyClientPassword123" -keypass "MyClientPassword123" -noprompt
    ```

1. クライアント コンピューター (hn1) でファイル `client-ssl-auth.properties` を作成します。 このファイルには、次の行が含まれています。

    ```config
    security.protocol=SSL
    ssl.truststore.location=/home/sshuser/ssl/kafka.client.truststore.jks
    ssl.truststore.password=MyClientPassword123
    ```

1. プロデューサーとコンシューマーのオプションを使用して管理者クライアントを起動し、プロデューサーとコンシューマーの両方がポート 9093 上で動作していることを確認します。 コンソール プロデューサーまたはコンシューマーを使用してセットアップを確認するために必要な手順については、後述の「[検証](apache-kafka-ssl-encryption-authentication.md#verification)」セクションを参照してください。

## <a name="client-setup-with-authentication"></a>クライアントのセットアップ (認証を使用)

> [!Note]
> 次の手順は、TLS 暗号化と認証の "**両方**" を設定する場合にのみ必要です。 暗号化のみを設定する場合は、「[クライアントのセットアップ (認証なし)](apache-kafka-ssl-encryption-authentication.md#client-setup-without-authentication)」を参照してください。

次の 4 つの手順は、クライアントのセットアップを完了するために必要なタスクをまとめたものです。

1. クライアント コンピューター (スタンバイ ヘッド ノード) にサインインします。
1. java キーストアを作成し、ブローカーの署名証明書を取得します。 証明書を CA が実行されている VM にコピーします。
1. CA コンピューター (アクティブ ヘッド ノード) に切り替えて、クライアント証明書に署名します。
1. クライアント コンピューター (スタンバイ ヘッド ノード) に移動して、`~/ssl` フォルダーに移動します。 署名証明書をクライアント コンピューターにコピーします。

各手順の詳細を次に示します。

1. クライアント コンピューター (スタンバイ ヘッド ノード) にサインインします。

    ```bash
    ssh sshuser@HeadNode1_Name
    ```

1. 既存の SSL ディレクトリを削除します。

    ```bash
    rm -R ~/ssl
    mkdir ssl
    cd ssl
    ```

1. Java キーストアを作成し、証明書署名要求を作成します。 

    ```bash
    keytool -genkey -keystore kafka.client.keystore.jks -validity 365 -storepass "MyClientPassword123" -keypass "MyClientPassword123" -dname "CN=HEADNODE1_FQDN" -storetype pkcs12
    
    keytool -keystore kafka.client.keystore.jks -certreq -file client-cert-sign-request -storepass "MyClientPassword123" -keypass "MyClientPassword123"
    ```

1. 証明書署名要求を CA にコピーします

    ```bash
    scp client-cert-sign-request sshuser@HeadNode0_Name:~/ssl/client-cert-sign-request
    ```

1. CA マシン (アクティブ ヘッド ノード) に切り替えて、クライアント証明書に署名します。

    ```bash
    ssh sshuser@HeadNode0_Name
    cd ssl
    openssl x509 -req -CA ca-cert -CAkey ca-key -in ~/ssl/client-cert-sign-request -out ~/ssl/client-cert-signed -days 365 -CAcreateserial -passin pass:MyClientPassword123
    ```

1. 署名されたクライアント証明書を CA (アクティブ ヘッドノード) からクライアント マシンにコピーします。

    ```bash
    scp client-cert-signed sshuser@HeadNode1_Name:~/ssl/client-signed-cert
    ```

1. CA 証明書をクライアント マシンにコピーします。

    ```bash
    scp ca-cert sshuser@HeadNode1_Name:~/ssl/ca-cert
    ```

    1. クライアント コンピューター (スタンバイ ヘッド ノード) にサインインして、ssl ディレクトリに移動します。

    ```bash
    ssh sshuser@HeadNode1_Name
    cd ssl
    ```

1. 署名された証明書を使用してクライアント ストアを作成し、CA 証明書をクライアント コンピューター (hn1) のキーストアとトラストストアにインポートします。

    ```bash
    keytool -keystore kafka.client.truststore.jks -alias CARoot -import -file ca-cert -storepass "MyClientPassword123" -keypass "MyClientPassword123" -noprompt
    
    keytool -keystore kafka.client.keystore.jks -alias CARoot -import -file ca-cert -storepass "MyClientPassword123" -keypass "MyClientPassword123" -noprompt
    
    keytool -keystore kafka.client.keystore.jks -import -file client-cert-signed -storepass "MyClientPassword123" -keypass "MyClientPassword123" -noprompt
    ```

1. クライアント コンピューター (hn1) でファイル `client-ssl-auth.properties` を作成します。 このファイルには、次の行が含まれています。

    ```bash
    security.protocol=SSL
    ssl.truststore.location=/home/sshuser/ssl/kafka.client.truststore.jks
    ssl.truststore.password=MyClientPassword123
    ssl.keystore.location=/home/sshuser/ssl/kafka.client.keystore.jks
    ssl.keystore.password=MyClientPassword123
    ssl.key.password=MyClientPassword123
    ```

## <a name="verification"></a>検証

クライアント コンピューターで次の手順を行います。

> [!Note]
> HDInsight 4.0 と Kafka 2.1 がインストールされている場合は、コンソール プロデューサー/コンシューマーを使用してセットアップを確認できます。 そうでない場合は、ポート 9092 で Kafka プロデューサーを実行し、トピックにメッセージを送信してから、TLS を使用するポート 9093 で Kafka コンシューマーを使用します。

### <a name="kafka-21-or-above"></a>Kafka 2.1 以上

1. トピックがまだ存在しない場合は作成します。

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --zookeeper <ZOOKEEPER_NODE>:2181 --create --topic topic1 --partitions 2 --replication-factor 2
    ```

1. コンソール プロデューサーを起動し、プロデューサーの構成ファイルとして `client-ssl-auth.properties` へのパスを指定します。

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-console-producer.sh --broker-list <FQDN_WORKER_NODE>:9093 --topic topic1 --producer.config ~/ssl/client-ssl-auth.properties
    ```

1. クライアント マシンへの別の SSH 接続を開き、コンソール コンシューマーを起動して、コンシューマーの構成ファイルとして `client-ssl-auth.properties` へのパスを指定します。

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-console-consumer.sh --bootstrap-server <FQDN_WORKER_NODE>:9093 --topic topic1 --consumer.config ~/ssl/client-ssl-auth.properties --from-beginning
    ```

### <a name="kafka-11"></a>Kafka 1.1

1. トピックがまだ存在しない場合は作成します。

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --zookeeper <ZOOKEEPER_NODE_0>:2181 --create --topic topic1 --partitions 2 --replication-factor 2
    ```

1. コンソール プロデューサーを起動し、プロデューサーの構成ファイルとして client-ssl-auth.properties へのパスを指定します。

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-console-producer.sh --broker-list <FQDN_WORKER_NODE>:9092 --topic topic1 
    ```

1. クライアント マシンへの別の SSH 接続を開き、コンソール コンシューマーを起動して、コンシューマーの構成ファイルとして `client-ssl-auth.properties` へのパスを指定します。

    ```bash
    $ /usr/hdp/current/kafka-broker/bin/kafka-console-consumer.sh --bootstrap-server <FQDN_WORKER_NODE>:9093 --topic topic1 --consumer.config ~/ssl/client-ssl-auth.properties --from-beginning
    ```

## <a name="next-steps"></a>次のステップ

* [HDInsight での Apache Kafka とは](apache-kafka-introduction.md)
