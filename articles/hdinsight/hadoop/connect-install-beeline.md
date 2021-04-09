---
title: Apache Beeline に接続またはインストールする - Azure HDInsight
description: Apache Beeline クライアントに接続して、HDInsight での Hadoop で Hive クエリを実行する方法について説明します。 Beeline は、JDBC を介して HiveServer2 を使用するためのユーティリティです。
ms.service: hdinsight
ms.topic: how-to
ms.custom: contperf-fy21q1
ms.date: 05/27/2020
ms.openlocfilehash: ab5dedf6718dad4f16fde59d905e2e59be5c495f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "98944470"
---
# <a name="connect-to-apache-beeline-on-hdinsight-or-install-it-locally"></a>HDInsight での Apache Beeline に接続するか、ローカルでインストールする

[Apache Beeline](https://cwiki.apache.org/confluence/display/Hive/HiveServer2+Clients#HiveServer2Clients-Beeline–NewCommandLineShell) は、HDInsight クラスターのヘッド ノードに含まれている Hive クライアントです。 この記事では、さまざまな種類の接続で HDInsight クラスターにインストールされている Beeline クライアントに接続する方法について説明します。 [ローカルに Beeline クライアントをインストール](#install-beeline-client)する方法についても説明しています。 

## <a name="types-of-connections"></a>接続の種類

### <a name="from-an-ssh-session"></a>SSH セッションから

SSH セッションからクラスターのヘッドノードに接続するときは、ポート `10001` 上の `headnodehost` のアドレスに接続することができます。

```bash
beeline -u 'jdbc:hive2://headnodehost:10001/;transportMode=http'
```

### <a name="over-an-azure-virtual-network"></a>Azure 仮想ネットワーク経由

Azure 仮想ネットワーク経由でクライアントから HDInsight に接続するときは、クラスターのヘッドノードの完全修飾ドメイン名 (FQDN) を指定する必要があります。 この接続はクラスター ノードに直接行われるため、接続にはポート `10001` が使用されます。

```bash
beeline -u 'jdbc:hive2://<headnode-FQDN>:10001/;transportMode=http'
```

`<headnode-FQDN>` をクラスター ヘッドノードの完全修飾ドメイン名に置き換えます。 ヘッドノードの完全修飾ドメイン名を検索するには、[Apache Ambari REST API を使用した HDInsight の管理](../hdinsight-hadoop-manage-ambari-rest-api.md#get-the-fqdn-of-cluster-nodes)に関するドキュメントの情報を使用してください。

### <a name="to-hdinsight-enterprise-security-package-esp-cluster-using-kerberos"></a>Kerberos を使用して HDInsight Enterprise セキュリティ パッケージ (ESP) クラスターへ

同じクラスター領域にあるコンピューターで、Azure Active Directory (AAD) -DS に参加している Enterprise セキュリティ パッケージ (ESP) クラスターにクライアントから接続するとき、ドメイン名 `<AAD-Domain>` と、クラスター `<username>` へのアクセス許可を付与されているドメイン ユーザー アカウントの名前も指定する必要があります。

```bash
kinit <username>
beeline -u 'jdbc:hive2://<headnode-FQDN>:10001/default;principal=hive/_HOST@<AAD-Domain>;auth-kerberos;transportMode=http' -n <username>
```

`<username>` を、クラスターへのアクセス許可を付与されているドメイン上のアカウントの名前で置き換えます。 `<AAD-DOMAIN>` をクラスターが参加している Azure Active Directory (AAD) の名前に置き換えます。 `<AAD-DOMAIN>` 値には大文字の文字列を使用します。そうしないと、資格情報が見つかりません。 必要に応じて `/etc/krb5.conf` で領域名を確認します。

Ambari から JDBC URL を確認するには:

1. Web ブラウザーから、`https://CLUSTERNAME.azurehdinsight.net/#/main/services/HIVE/summary` に移動します。ここで、`CLUSTERNAME` はクラスターの名前です。 HiveServer2 が実行されていることを確認します。

1. クリップボードを使用して、HiveServer2 JDBC URL をコピーします。

### <a name="over-public-or-private-endpoints"></a>パブリック エンドポイントまたはプライベート エンドポイント経由

パブリック エンドポイントまたはプライベート エンドポイントを使用してクラスターに接続する場合は、クラスター ログイン アカウント名 (既定値 `admin`) とパスワードを指定する必要があります。 たとえば、Beeline を使用してクライアント システムから `clustername.azurehdinsight.net` のアドレスに接続する場合です。 この接続は、ポート `443` を経由し、TLS/SSL を使用して暗号化されます。

`clustername` を、使用する HDInsight クラスターの名前に置き換えます。 `admin` をクラスターのクラスター ログイン アカウントに置き換えます。 ESP クラスターには、完全な UPN (例: user@domain.com) を使用します。 `password` をクラスター ログイン アカウントのパスワードに置き換えます。

```bash
beeline -u 'jdbc:hive2://clustername.azurehdinsight.net:443/;ssl=true;transportMode=http;httpPath=/hive2' -n admin -p 'password'
```

プライベート エンドポイントの場合:

```bash
beeline -u 'jdbc:hive2://clustername-int.azurehdinsight.net:443/;ssl=true;transportMode=http;httpPath=/hive2' -n admin -p 'password'
```

プライベート エンドポイントは、同じリージョンでピアリングされた VNET からのみアクセスできる基本のロード バランサーを指します。 詳細については、[グローバル VNet ピアリングとロード バランサーの制約](../../virtual-network/virtual-networks-faq.md#what-are-the-constraints-related-to-global-vnet-peering-and-load-balancers)に関する記事を参照してください。 `-v` オプションを指定して `curl` コマンドを使用すると、パブリック エンドポイントまたはプライベート エンドポイントに関する接続の問題をトラブルシューティングしてから、Beeline を使用できます。

### <a name="use-beeline-with-apache-spark"></a>Apache Spark での Beeline の使用

Apache Spark は独自の HiveServer2 実装を提供します。これは Spark Thrift サーバーとも呼ばれます。 このサービスは、Hive ではなく、Spark SQL を使用してクエリを解決します。 クエリによっては、パフォーマンスが向上する可能性があります。

#### <a name="through-public-or-private-endpoints"></a>パブリック エンドポイントまたはプライベート エンドポイント経由

使用される接続文字列は少し異なります。 `httpPath=/hive2` が含まるのではなく、`httpPath/sparkhive2`を使用します。 `clustername` を、使用する HDInsight クラスターの名前に置き換えます。 `admin` をクラスターのクラスター ログイン アカウントに置き換えます。 ESP クラスターには、完全な UPN (例: user@domain.com) を使用します。 `password` をクラスター ログイン アカウントのパスワードに置き換えます。

```bash
beeline -u 'jdbc:hive2://clustername.azurehdinsight.net:443/;ssl=true;transportMode=http;httpPath=/sparkhive2' -n admin -p 'password'
```

プライベート エンドポイントの場合:

```bash
beeline -u 'jdbc:hive2://clustername-int.azurehdinsight.net:443/;ssl=true;transportMode=http;httpPath=/sparkhive2' -n admin -p 'password'
```

プライベート エンドポイントは、同じリージョンでピアリングされた VNET からのみアクセスできる基本のロード バランサーを指します。 詳細については、[グローバル VNet ピアリングとロード バランサーの制約](../../virtual-network/virtual-networks-faq.md#what-are-the-constraints-related-to-global-vnet-peering-and-load-balancers)に関する記事を参照してください。 `-v` オプションを指定して `curl` コマンドを使用すると、パブリック エンドポイントまたはプライベート エンドポイントに関する接続の問題をトラブルシューティングしてから、Beeline を使用できます。

#### <a name="from-cluster-head-or-inside-azure-virtual-network-with-apache-spark"></a>Azure Spark でのクラスター ヘッドから、または Azure Virtual Network 内

クラスターのヘッド ノード、または HDInsight クラスターと同じ Azure Virtual Network 内のリソースから直接接続する場合は、`10001` の代わりに、Spark Thrift サーバー用のポート `10002` を使用する必要があります。 次の例は、ヘッド ノードに直接接続する方法を示しています。

```bash
/usr/hdp/current/spark2-client/bin/beeline -u 'jdbc:hive2://headnodehost:10002/;transportMode=http'
```

## <a name="install-beeline-client"></a>Beeline クライアントをインストールする

Beeline はヘッド ノードに含まれていますが、ローカルにインストールしたい場合があります。  ローカル コンピューターにインストールするための手順は、[Linux 用 Windows サブシステム](/windows/wsl/install-win10)に基づいています。

1. パッケージ リストを更新します。 ご利用の Bash シェルで次のコマンドを入力します。

    ```bash
    sudo apt-get update
    ```

1. Java がインストールされていない場合はインストールします。 確認するには、`which java` コマンドを使用します。

    1. Java パッケージがインストールされていない場合は、次のコマンドを入力します。

        ```bash
        sudo apt install openjdk-11-jre-headless
        ```

    1. bashrc ファイルを開きます (通常は ~/.bashrc にあります): `nano ~/.bashrc`。

    1. bashrc ファイルを修正します。 ファイルの末尾に次の行を追加します。

        ```bash
        export JAVA_HOME=/usr/lib/jvm/java-1.11.0-openjdk-amd64
        ```

        次に、**Ctrl + X** キー、**Y** キー、Enter キーの順に押します。

1. Hadoop と Beeline のアーカイブをダウンロードし、次のコマンドを入力します。

    ```bash
    wget https://archive.apache.org/dist/hadoop/core/hadoop-2.7.3/hadoop-2.7.3.tar.gz
    wget https://archive.apache.org/dist/hive/hive-1.2.1/apache-hive-1.2.1-bin.tar.gz
    ```

1. アーカイブをアンパックし、次のコマンドを入力します。

    ```bash
    tar -xvzf hadoop-2.7.3.tar.gz
    tar -xvzf apache-hive-1.2.1-bin.tar.gz
    ```

1. bashrc ファイルをさらに修正します。 アーカイブがアンパックされた場所へのパスを識別する必要があります。 [Linux 用 Windows サブシステム](/windows/wsl/install-win10) を使用し、手順に正確に従った場合、パスは `/mnt/c/Users/user/` になります。ここで、`user` はユーザー名です。

    1. ファイルを開きます: `nano ~/.bashrc`

    1. 次のコマンドを適切なパスで変更して、bashrc ファイルの末尾にそれらを入力します。

        ```bash
        export HADOOP_HOME=/path_where_the_archives_were_unpacked/hadoop-2.7.3
        export HIVE_HOME=/path_where_the_archives_were_unpacked/apache-hive-1.2.1-bin
        PATH=$PATH:$HIVE_HOME/bin
        ```

    1. 次に、**Ctrl + X** キー、**Y** キー、Enter キーの順に押します。

1. ご利用の Bash セッションを閉じてから再度開きます。

1. 接続をテストします。 上記の「[パブリック エンドポイントまたはプライベート エンドポイント経由](#over-public-or-private-endpoints)」での接続形式を使用します。

## <a name="next-steps"></a>次のステップ

* Apache Hive での Beeline クライアントの使用例については、「[Apache Hive で Apache Beeline を使用する](apache-hadoop-use-hive-beeline.md)」をご覧ください
* HDInsight での Hive について詳しくは、[HDInsight 上の Apache Hadoop での Apache Hive の使用](hdinsight-use-hive.md)に関するページをご覧ください。