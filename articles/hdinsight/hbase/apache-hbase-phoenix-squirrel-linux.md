---
title: クイック スタート:Azure HDInsight で Apache HBase にクエリを実行する - Apache Phoenix
description: このクイックスタートでは、HDInsight 内で Apache Phoenix を使用する方法を説明します。 また、コンピューターに SQLLine をインストールして設定し、HDInsight の HBase クラスターに接続する方法を説明します。
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: quickstart
ms.date: 06/12/2019
ms.author: hrasheed
ms.openlocfilehash: 20af6d32d03ae5d4fe37b1a37198ef1f2c50ec95
ms.sourcegitcommit: e5dcf12763af358f24e73b9f89ff4088ac63c6cb
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/14/2019
ms.locfileid: "67137416"
---
# <a name="quickstart-query-apache-hbase-in-azure-hdinsight-with-apache-phoenix"></a>クイック スタート:Apache Phoenix を使用して Azure HDInsight で Apache HBase にクエリを実行する

このクイック スタートでは、Apache Phoenix を使用して Azure HDInsight で HBase クエリを実行する方法を学習します。 Apache Phoenix は、Apache HBase 用の SQL クエリ エンジンです。 SQL を使用して HBase テーブルの照会と管理ができます。 [SQLLine](http://sqlline.sourceforge.net/) は、SQL を実行するためのコマンド ライン ユーティリティです。

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。

## <a name="prerequisites"></a>前提条件

* Apache HBase クラスター。 HDInsight クラスターの作成については、[クラスターの作成](../hadoop/apache-hadoop-linux-tutorial-get-started.md#create-cluster)に関するセクションを参照してください。  **HBase** のクラスターの種類を選択するようにしてください。

* SSH クライアント 詳細については、[SSH を使用して HDInsight (Apache Hadoop) に接続する方法](../hdinsight-hadoop-linux-use-ssh-unix.md)に関するページを参照してください。

## <a name="identify-a-zookeeper-node"></a>ZooKeeper ノードを識別する

HBase クラスターに接続する場合は、いずれかの Apache ZooKeeper ノードに接続する必要があります。 各 HDInsight クラスターには 3 つの ZooKeeper ノードがあります。 Curl を使用すると、ZooKeeper ノードをすばやく識別できます。 次の curl コマンドを編集して `PASSWORD` と `CLUSTERNAME` を関連する値に置き換えてから、そのコマンドをコマンド プロンプトに入力します。

```cmd
curl -u admin:PASSWORD -sS -G https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/services/ZOOKEEPER/components/ZOOKEEPER_SERVER
```

出力の一部は次のようになります。

```output
    {
      "href" : "http://hn1-brim.432dc3rlshou3ocf251eycoapa.bx.internal.cloudapp.net:8080/api/v1/clusters/myCluster/hosts/zk0-brim.432dc3rlshou3ocf251eycoapa.bx.internal.cloudapp.net/host_components/ZOOKEEPER_SERVER",
      "HostRoles" : {
        "cluster_name" : "myCluster",
        "component_name" : "ZOOKEEPER_SERVER",
        "host_name" : "zk0-brim.432dc3rlshou3ocf251eycoapa.bx.internal.cloudapp.net"
      }
```

`host_name` の値を後で使用するためにメモしておきます。

## <a name="create-a-table-and-manipulate-data"></a>テーブルを作成してデータを操作する

SSH を使用して HBase クラスターに接続した後、Apache Phoenix を使用して HBase テーブルを作成したり、データを挿入したり、データにクエリを実行したりすることができます。

1. `ssh` コマンドを使用して HBase クラスターに接続します。 次のコマンドを編集して `CLUSTERNAME` をクラスターの名前に置き換えてから、そのコマンドを入力します。

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

2. ディレクトリを Phoenix クライアントに変更します。 次のコマンドを入力します。

    ```bash
    cd /usr/hdp/current/phoenix-client/bin
    ```

3. [SQLLine](http://sqlline.sourceforge.net/) を起動します。 次のコマンドを編集して `ZOOKEEPER` を前に識別された ZooKeeper ノードに置き換えてから、そのコマンドを入力します。

    ```bash
    ./sqlline.py ZOOKEEPER:2181:/hbase-unsecure
    ```

4. HBase テーブルを作成します。 次のコマンドを入力します。

    ```sql
    CREATE TABLE Company (company_id INTEGER PRIMARY KEY, name VARCHAR(225));
    ```

5. SQLLine の `!tables` コマンドを使用して HBase 内のすべてのテーブルを一覧表示します。 次のコマンドを入力します。

    ```sqlline
    !tables
    ```

6. テーブルに値を挿入します。 次のコマンドを入力します。

    ```sql
    UPSERT INTO Company VALUES(1, 'Microsoft');
    UPSERT INTO Company VALUES(2, 'Apache');
    ```

7. テーブルにクエリを実行します。 次のコマンドを入力します。

    ```sql
    SELECT * FROM Company;
    ```

8. レコードを削除します。 次のコマンドを入力します。

    ```sql
    DELETE FROM Company WHERE COMPANY_ID=1;
    ```

9. テーブルを削除します。 次のコマンドを入力します。

    ```hbase
    DROP TABLE Company;
    ```

10. SQLLine の `!quit` コマンドを使用して SQLLine を終了します。 次のコマンドを入力します。

    ```sqlline
    !quit
    ```

## <a name="clean-up-resources"></a>リソースのクリーンアップ

このクイックスタートを完了したら、必要に応じてクラスターを削除できます。 HDInsight を使用すると、データは Azure Storage に格納されるため、クラスターは、使用されていない場合に安全に削除できます。 また、HDInsight クラスターは、使用していない場合でも課金されます。 クラスターの料金は Storage の料金の何倍にもなるため、クラスターを使用しない場合は削除するのが経済的にも合理的です。

クラスターを削除するには、「[ブラウザー、PowerShell、または Azure CLI を使用して HDInsight クラスターを削除する](../hdinsight-delete-cluster.md)」を参照してください。

## <a name="next-steps"></a>次の手順

このクイック スタートでは、Apache Phoenix を使用して Azure HDInsight で HBase クエリを実行する方法を学習しました。 Apache Phoenix の詳細を学習するには、次の記事にさらに詳しい説明が含まれています。

> [!div class="nextstepaction"]
> [HDInsight の Apache Phoenix](../hdinsight-phoenix-in-hdinsight.md)
