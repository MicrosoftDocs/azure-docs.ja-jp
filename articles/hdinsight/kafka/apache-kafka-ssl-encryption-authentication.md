---
title: Azure HDInsight の Apache Kafka 用に SSL 暗号化および認証を設定する
description: Kafka ブローカー間や、Kafka クライアントと Kafka ブローカー間の通信用に SSL 暗号化を設定します。 クライアントの SSL 認証を設定します。
services: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 01/15/2019
ms.author: hrasheed
ms.openlocfilehash: cd850808f656b211bf1fdbfd2fe7c5799150b030
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2019
ms.locfileid: "57839663"
---
# <a name="setup-secure-sockets-layer-ssl-encryption-and-authentication-for-apache-kafka-in-azure-hdinsight"></a>Azure HDInsight の Apache Kafka 用に Secure Sockets Layer (SSL) 暗号化および認証を設定する

この記事では、Apache Kafka クライアントと Apache Kafka ブローカー間の通信に SSL 暗号化を設定する方法について説明します。 クライアントの認証の設定に必要な手順についても説明します (双方向 SSL とも呼ばれます)。

## <a name="server-setup"></a>サーバーのセットアップ

最初の手順は、Kafka ブローカーごとにキーストアとトラストストアを作成することです。 これらの作成後、証明機関 (CA) とブローカーの証明書をこれらのストアにインポートします。

> [!Note] 
> このガイドでは自己署名証明書を使用しますが、最も安全なソリューションは、信頼された CA によって発行された証明書を使うことです。

サーバーのセットアップを完了するために、次の操作を行います。

1. ssl をという名前のフォルダーを作成し、サーバーのパスワードを環境変数としてエクスポートします。 このガイドの残りの部分では、`<server_password>` をサーバーの実際の管理者パスワードに置き換えます。
1. 次に、java キーストア (kafka.server.keystore.jks) と CA 証明書を作成します。
1. その後、署名要求を作成して、前の手順で作成した CA によって署名された証明書を取得します。
1. 署名要求を CA に送信し、この証明書に署名してもらいます。 自己署名証明書を使用するため、`openssl` コマンドを使って CA を使用して証明書に署名します。
1. トラストストアを作成し、CA の証明書をインポートします。
1. 公開 CA 証明書をキーストアにインポートします。
1. 署名証明書をキーストアにインポートします。

これらの手順を完了するためのコマンドは、次のコード スニペットで示されています。

```bash
export SRVPASS=<server_password>
mkdir ssl
cd ssl

# Create a java keystore (kafka.server.keystore.jks) and a CA certificate.

keytool -genkey -keystore kafka.server.keystore.jks -validity 365 -storepass $SRVPASS -keypass $SRVPASS -dname "CN=wn0-umakaf.xvbseke35rbuddm4fyvhm2vz2h.cx.internal.cloudapp.net" -storetype pkcs12

# Create a signing request to get the certificate created in the previous step signed by the CA.

keytool -keystore kafka.server.keystore.jks -certreq -file cert-file -storepass $SRVPASS -keypass $SRVPASS

# Send the signing request to the CA and get this certificate signed.

openssl x509 -req -CA ca-cert -CAkey ca-key -in cert-file -out cert-signed -days 365 -CAcreateserial -passin pass:$SRVPASS

# Create a trust store and import the certificate of the CA.

keytool -keystore kafka.server.truststore.jks -alias CARoot -import -file ca-cert -storepass $SRVPASS -keypass $SRVPASS -noprompt

# Import the public CA certificate into the keystore.

keytool -keystore kafka.server.keystore.jks -alias CARoot -import -file ca-cert -storepass $SRVPASS -keypass $SRVPASS -noprompt

# Import the signed certificate into the keystore.

keytool -keystore kafka.server.keystore.jks -alias CARoot -import -file ca-cert -storepass $SRVPASS -keypass $SRVPASS -noprompt

# The output should say "Certificate reply was added to keystore"
```

最後に、キーストアに署名証明書をインポートします。これは、Kafka ブローカー用のトラストストアとキーストアを構成するために必要です。

## <a name="update-kafka-configuration-to-use-ssl-and-restart-brokers"></a>SSL を使用してブローカーを再起動するように Kafka 構成を更新する

キーストアとトラストストアを使用して各 Kafka ブローカーを設定し、適切な証明書をインポートしました。  次に、Ambari を使用して関連の Kafka 構成プロパティを変更して、Kafka ブローカーを再起動します。 

構成の変更を完了するために、次の手順を実行します。

1. Azure portal にサインインし、ご利用の Azure HDInsight Apache Kafka クラスターを選択します。
1. **[クラスター ダッシュボード]** の **[Ambari ホーム]** をクリックして、Ambari UI に移動します。
1. **[Kafka Broker]/(Kafka ブローカー/)** で、**リスナー** プロパティを `PLAINTEXT://localhost:9092,SSL://localhost:9093` に設定します。
1. **[Advanced kafka-broker]\(Kafka ブローカーの高度な設定\)** で **security.inter.broker.protocol** プロパティを `SSL` に設定します。

    ![Ambari での Kafka ssl 構成プロパティの編集](./media/apache-kafka-ssl-encryption-authentication/editing-configuration-ambari.png)

1. **Custom kafka-broker\(カスタム kafka ブローカー)** で **ssl.client.auth** プロパティを `required` に設定します。 この手順は、認証と暗号化を設定する場合にのみ必要です。

    ![Ambari での kafka ssl 構成プロパティの編集](./media/apache-kafka-ssl-encryption-authentication/editing-configuration-ambari2.png)

1. 完全修飾ドメイン名 (FQDN) ではなく IP アドレスを提供するように、Kafka `server.properties` ファイルに構成プロパティを追加します。

    ```bash
    IP_ADDRESS=$(hostname -i)
    echo advertised.listeners=$IP_ADDRESS
    sed -i.bak -e '/advertised/{/advertised@/!d;}' /usr/hdp/current/kafka-broker/conf/server.properties
    echo "advertised.listeners=PLAINTEXT://$IP_ADDRESS:9092,SSL://$IP_ADDRESS:9093" >> /usr/hdp/current/kafka-broker/conf/server.properties
    echo "ssl.keystore.location=/home/sshuser/ssl/kafka.server.keystore.jks" >> /usr/hdp/current/kafka-broker/conf/server.properties
    echo "ssl.keystore.password=<server_password>" >> /usr/hdp/current/kafka-broker/conf/server.properties
    echo "ssl.key.password=<server_password>" >> /usr/hdp/current/kafka-broker/conf/server.properties
    echo "ssl.truststore.location=/home/sshuser/ssl/kafka.server.truststore.jks" >> /usr/hdp/current/kafka-broker/conf/server.properties
    echo "ssl.truststore.password=<server_password>" >> /usr/hdp/current/kafka-broker/conf/server.properties
    ```

1. 前の変更が正しく反映されていることを確認するには、必要に応じて、次の行が Kafka `server.properties` ファイルに含まれていることを確認できます。

    ```bash
    advertised.listeners=PLAINTEXT://10.0.0.11:9092,SSL://10.0.0.11:9093
    ssl.keystore.location=/home/sshuser/ssl/kafka.server.keystore.jks
    ssl.keystore.password=<server_password>
    ssl.key.password=<server_password>
    ssl.truststore.location=/home/sshuser/ssl/kafka.server.truststore.jks
    ssl.truststore.password=<server_password>
    ```

1. すべての Kafka ブローカーを再起動します。

## <a name="client-setup-with-authentication"></a>クライアントのセットアップ (認証を使用)

> [!Note]
> 次の手順は、SSL 暗号化と認証の "**両方**" を設定する場合にのみ必要です。 暗号化のみを設定する場合は、「[クライアントのセットアップ (認証なし)](apache-kafka-ssl-encryption-authentication.md#client-setup-without-authentication)」に進んでください。

クライアントのセットアップを完了するために、次の手順を実行します。

1. クライアント コンピューター (hn1) にログインします。
1. クライアントのパスワードをエクスポートします。 `<client_password>` を Kafka クライアント コンピューターでの実際の管理者パスワードに置き換えます。
1. java キーストアを作成し、ブローカーの署名証明書を取得します。 証明書を CA が実行されている VM にコピーします。
1. CA コンピューター (wn0) に切り替えて、クライアント証明書に署名します。
1. クライアント コンピューター (hn1) に移動して、`~/ssl` フォルダーに移動します。 署名証明書をクライアント コンピューターにコピーします。

```bash
export CLIPASS=<client_password>
cd ssl

# Create a java keystore and get a signed certificate for the broker. Then copy the certificate to the VM where the CA is running.

keytool -genkey -keystore kafka.client.keystore.jks -validity 365 -storepass $CLIPASS -keypass $CLIPASS -dname "CN=mylaptop1" -alias my-local-pc1 -storetype pkcs12

keytool -keystore kafka.client.keystore.jks -certreq -file client-cert-sign-request -alias my-local-pc1 -storepass $CLIPASS -keypass $CLIPASS

# Copy the cert to the vm where the CA is
scp client-cert-sign-request3 sshuser@wn0-umakaf:~/tmp1/client-cert-sign-request

# Switch to the CA machine (wn0) to sign the client certificate.
cd ssl
openssl x509 -req -CA ca-cert -CAkey ca-key -in /tmp1/client-cert-sign-request -out /tmp1/client-cert-signed -days 365 -CAcreateserial -passin pass:<server_password>

# Return to the client machine (hn1), navigate to ~/ssl folder and copy signed cert to client machine
scp -i ~/kafka-security.pem sshuser@wn0-umakaf:/tmp1/client-cert-signed

# Import CA cert to trust store
keytool -keystore kafka.client.truststore.jks -alias CARoot -import -file ca-cert -storepass $CLIPASS -keypass $CLIPASS -noprompt

# Import CA cert to key store
keytool -keystore kafka.client.keystore.jks -alias CARoot -import -file ca-cert -storepass $CLIPASS -keypass $CLIPASS -noprompt

# Import signed client (cert client-cert-signed1) to keystore
keytool -keystore kafka.client.keystore.jks -import -file client-cert-signed -alias my-local-pc1 -storepass $CLIPASS -keypass $CLIPASS -noprompt
```

最後に、`cat client-ssl-auth.properties` コマンドを使用してファイル `client-ssl-auth.properties` を表示します。 このファイルには、次の行が含まれています。

```bash
security.protocol=SSL
ssl.truststore.location=/home/sshuser/ssl/kafka.client.truststore.jks
ssl.truststore.password=<client_password>
ssl.keystore.location=/home/sshuser/ssl/kafka.client.keystore.jks
ssl.keystore.password=<client_password>
ssl.key.password=<client_password>
```

## <a name="client-setup-without-authentication"></a>クライアントのセットアップ (認証なし)

認証が必要ない場合、次の手順を実行して SSL 暗号化のみ設定します。

1. クライアント コンピューター (hn1) にサインインし、`~/ssl` フォルダーに移動します。
1. クライアントのパスワードをエクスポートします。 `<client_password>` を Kafka クライアント コンピューターでの実際の管理者パスワードに置き換えます。
1. CA コンピューター (wn0) からクライアント コンピューターに署名証明書をコピーします。
1. トラストストアに CA 証明書をインポートします。
1. キーストアに CA 証明書をインポートします。

これらの手順は、次のコード スニペットで示されています。

```bash
export CLIPASS=<client_password>
cd ssl

# Copy signed cert to client machine
scp -i ~/kafka-security.pem sshuser@wn0-umakaf:/home/sshuser/ssl/ca-cert .

# Import CA cert to truststore
keytool -keystore kafka.client.truststore.jks -alias CARoot -import -file ca-cert -storepass $CLIPASS -keypass $CLIPASS -noprompt

# Import CA cert to keystore
keytool -keystore kafka.client.keystore.jks -alias CARoot -import -file cert-signed -storepass $CLIPASS -keypass $CLIPASS -noprompt
```

最後に、`cat client-ssl-auth.properties` コマンドを使用してファイル `client-ssl-auth.properties` を表示します。 このファイルには、次の行が含まれています。

```bash
security.protocol=SSL
ssl.truststore.location=/home/sshuser/ssl/kafka.client.truststore.jks
ssl.truststore.password=<client_password>
```

## <a name="next-steps"></a>次の手順

* [HDInsight での Apache Kafka とは](apache-kafka-introduction.md)
