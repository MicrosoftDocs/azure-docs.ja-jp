---
title: Azure HDInsight の HBase で Apache Phoenix と SQLLine を使用する
description: HDInsight での Apache Phoenix の使用方法について説明します。 また、コンピューターに SQLLine をインストールして設定し、HDInsight の HBase クラスターに接続する方法を説明します。
services: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 01/03/2018
ms.author: hrasheed
ms.openlocfilehash: 38f86bd19c85440fbad0e7fd56a3dd9ba836c7b8
ms.sourcegitcommit: 9f4eb5a3758f8a1a6a58c33c2806fa2986f702cb
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/03/2019
ms.locfileid: "58903304"
---
# <a name="use-apache-phoenix-with-linux-based-apache-hbase-clusters-in-hdinsight"></a>HDInsight 内の Linux ベースの Apache HBase クラスターで Apache Phoenix を使用する
Azure HDInsight での [Apache Phoenix](https://phoenix.apache.org/) の使用方法、およびSQLLine の使用方法について説明します。 Phoenix について詳しくは、「[Apache Phoenix in 15 minutes or less (Apache Phoenix についての簡単な説明)](https://phoenix.apache.org/Phoenix-in-15-minutes-or-less.html)」をご覧ください。 Phoenix の文法については、「[Apache Phoenix grammar (Apache Phoenix の文法)](https://phoenix.apache.org/language/index.html)」をご覧ください。

> [!NOTE]  
> HDInsight での Phoenix のバージョンの情報については、[HDInsight で提供される Apache Hadoop クラスター バージョンの新機能](../hdinsight-component-versioning.md)に関する記事をご覧ください。
>
>

## <a name="use-sqlline"></a>SQLLine の使用
[SQLLine](http://sqlline.sourceforge.net/) は、SQL を実行するためのコマンド ライン ユーティリティです。

### <a name="prerequisites"></a>前提条件
SQLLine を使用するには、以下のものが必要です。

* **HDInsight 内の Apache HBase クラスター**。 作成するには、[HDInsight での Apache HBase の使用](./apache-hbase-tutorial-get-started-linux.md)に関するページを参照してください。

HBase クラスターに接続するときは、いずれかの [Apache ZooKeeper](https://zookeeper.apache.org/) VM に接続する必要があります。 各 HDInsight クラスターには 3 つの ZooKeeper VM があります。

**ZooKeeper のホスト名を取得するには**

1. **https://\<クラスター名\>.azurehdinsight.net** を参照して、[Apache Ambari](https://ambari.apache.org/) を開きます。
2. HTTP (クラスター) ユーザー名とパスワードを入力してサインインします。
3. 左側のメニューで **[ZooKeeper]** を選択します。 3 つの **ZooKeeper サーバー** インスタンスが表示されます。
4. いずれかの **ZooKeeper サーバー** インスタンスを選択します。 **[概要]** ウィンドウで**ホスト名**を確認します。 ホスト名は、*zk1-jdolehb.3lnng4rcvp5uzokyktxs4a5dhd.bx.internal.cloudapp.net* のように表示されます。

**SQLLine を使用するには**

1. SSH を使用したクラスターへの接続。 詳細については、[HDInsight での SSH の使用](../hdinsight-hadoop-linux-use-ssh-unix.md)に関するページを参照してください。

2. SSH で次のコマンドを実行して、SQLLine を実行します。

        cd /usr/hdp/current/phoenix-client/bin
        ./sqlline.py <ZOOKEEPER SERVER FQDN>:2181:/hbase-unsecure
3. HBase テーブルを作成していくつかのデータを挿入するには、次のコマンドを実行します。

        CREATE TABLE Company (COMPANY_ID INTEGER PRIMARY KEY, NAME VARCHAR(225));

        !tables

        UPSERT INTO Company VALUES(1, 'Microsoft');

        SELECT * FROM Company;

        !quit

詳しくは、[SQLLine のマニュアル](http://sqlline.sourceforge.net/#manual)および [Apache Phoenix の文法](https://phoenix.apache.org/language/index.html)に関するページをご覧ください。

## <a name="next-steps"></a>次の手順
この記事では、HDInsight で Apache Phoenix を使用する方法を説明しました。 詳細については、次の記事を参照してください。

* [HDInsight HBase の概要][hdinsight-hbase-overview]。
  Apache HBase は、Apache Hadoop 上に構築された Apache 用のオープン ソースの NoSQL データベースです。大量の非構造化データおよび半構造化データに対するランダム アクセスと強力な一貫性を実現します。
* [Azure 仮想ネットワークで Apache HBase クラスターをプロビジョニングします][hdinsight-hbase-provision-vnet]。
  アプリケーションが Apache HBase と直接通信できるように、仮想ネットワーク統合を使用して、HBase クラスターをアプリケーションと同じ仮想ネットワークにデプロイできます。
* [HDInsight で Apache HBase レプリケーションを構成します](apache-hbase-replication.md)。 2 つの Azure データ センター間に Apache HBase レプリケーションを設定する方法について学習します。


[azure-portal]: https://portal.azure.com
[vnet-point-to-site-connectivity]: https://msdn.microsoft.com/library/azure/09926218-92ab-4f43-aa99-83ab4d355555#BKMK_VNETPT

[hdinsight-hbase-provision-vnet]:apache-hbase-provision-vnet.md
[hdinsight-hbase-overview]:apache-hbase-overview.md


