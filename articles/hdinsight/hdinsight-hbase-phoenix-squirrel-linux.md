---
title: "Azure HDInsight の HBase で Apache Phoenix と SQLLine を使用する | Microsoft Docs"
description: "HDInsight での Apache Phoenix の使用方法について説明します。 また、コンピューターに SQLLine をインストールして設定し、HDInsight の HBase クラスターに接続する方法を説明します。"
services: hdinsight
documentationcenter: 
author: mumian
manager: jhubbard
editor: cgronlun
ms.assetid: cda0f33b-a2e8-494c-972f-ae0bb482b818
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 09/22/2017
ms.author: jgao
ms.openlocfilehash: 41bfef95059b7c5343d952b4fe372e3baae6f9d7
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="use-apache-phoenix-with-linux-based-hbase-clusters-in-hdinsight"></a>HDInsight での Linux ベースの HBase クラスターによる Apache Phoenix の使用
Azure HDInsight での [Apache Phoenix](http://phoenix.apache.org/) の使用方法、およびSQLLine の使用方法について説明します。 Phoenix の詳細については、 [Phoenix についての簡単な説明](http://phoenix.apache.org/Phoenix-in-15-minutes-or-less.html)を参照してください。 Phoenix の文法については、[Phoenix の文法](http://phoenix.apache.org/language/index.html)に関するページを参照してください。

> [!NOTE]
> HDInsight での Phoenix のバージョンの情報については、「[HDInsight で提供される Hadoop クラスター バージョンの新機能](hdinsight-component-versioning.md)」を参照してください。
>
>

## <a name="use-sqlline"></a>SQLLine の使用
[SQLLine](http://sqlline.sourceforge.net/) は、SQL を実行するためのコマンド ライン ユーティリティです。

### <a name="prerequisites"></a>前提条件
SQLLine を使用するには、以下のものが必要です。

* **HDInsight 環境の HBase クラスター**。 作成するには、[HDInsight での Apache HBase の使用](./hdinsight-hbase-tutorial-get-started.md)に関するページを参照してください。

HBase クラスターに接続するときは、いずれかの ZooKeeper VM に接続する必要があります。 各 HDInsight クラスターには 3 つの ZooKeeper VM があります。

**ZooKeeper のホスト名を確認するには**

1. **https://\<cluster name\>.azurehdinsight.net** を参照して Ambari を開きます。
2. HTTP (クラスター) ユーザー名とパスワードを入力してサインインします。
3. 左側のメニューで **[ZooKeeper]** を選択します。 3 つの **ZooKeeper サーバー** インスタンスが表示されます。
4. いずれかの **ZooKeeper サーバー** インスタンスを選択します。 **[概要]** ウィンドウで**ホスト名**を確認します。 ホスト名は、*zk1-jdolehb.3lnng4rcvp5uzokyktxs4a5dhd.bx.internal.cloudapp.net* のように表示されます。

**SQLLine を使用するには**

1. SSH を使用したクラスターへの接続。 詳細については、[HDInsight での SSH の使用](hdinsight-hadoop-linux-use-ssh-unix.md)に関するページを参照してください。

2. SSH で次のコマンドを実行して、SQLLine を実行します。

        cd /usr/hdp/2.2.9.1-7/phoenix/bin
        ./sqlline.py <ZOOKEEPER SERVER FQDN>:2181:/hbase-unsecure
3. HBase テーブルを作成していくつかのデータを挿入するには、次のコマンドを実行します。

        CREATE TABLE Company (COMPANY_ID INTEGER PRIMARY KEY, NAME VARCHAR(225));

        !tables

        UPSERT INTO Company VALUES(1, 'Microsoft');

        SELECT * FROM Company;

        !quit

詳細については、[SQLLine のマニュアル](http://sqlline.sourceforge.net/#manual)および [Phoenix の文法](http://phoenix.apache.org/language/index.html)に関するページを参照してください。

## <a name="next-steps"></a>次のステップ
この記事では、HDInsight で Apache Phoenix を使用する方法を説明しました。 詳細については、次の記事を参照してください。

* [HDInsight HBase の概要][hdinsight-hbase-overview]。
  HBase は、Hadoop 上に構築された Apache オープン ソースの NoSQL データベースです。大量の非構造化データおよび半構造化データに対するランダム アクセスと強力な一貫性を実現します。
* [Azure Virtual Network での HBase クラスターのプロビジョニング][hdinsight-hbase-provision-vnet]。
  アプリケーションが HBase と直接通信できるように、仮想ネットワーク統合を使用して、HBase クラスターをアプリケーションと同じ仮想ネットワークにデプロイできます。
* [HDInsight での HBase レプリケーションの構成](hdinsight-hbase-replication.md)。 2 つの Azure データ センター間に HBase レプリケーションを設定する方法について説明します。


[azure-portal]: https://portal.azure.com
[vnet-point-to-site-connectivity]: https://msdn.microsoft.com/library/azure/09926218-92ab-4f43-aa99-83ab4d355555#BKMK_VNETPT

[hdinsight-manage-portal]: hdinsight-administer-use-management-portal.md#connect-to-clusters-using-rdp
[hdinsight-hbase-provision-vnet]: hdinsight-hbase-provision-vnet.md
[hdinsight-hbase-overview]: hdinsight-hbase-overview.md

[hdinsight-hbase-phoenix-sqlline]: ./media/hdinsight-hbase-phoenix-squirrel/hdinsight-hbase-phoenix-sqlline.png
[img-certificate]: ./media/hdinsight-hbase-phoenix-squirrel/hdinsight-hbase-vpn-certificate.png
[img-vnet-diagram]: ./media/hdinsight-hbase-phoenix-squirrel/hdinsight-hbase-vnet-point-to-site.png
[img-squirrel-driver]: ./media/hdinsight-hbase-phoenix-squirrel/hdinsight-hbase-squirrel-driver.png
[img-squirrel-alias]: ./media/hdinsight-hbase-phoenix-squirrel/hdinsight-hbase-squirrel-alias.png
[img-squirrel]: ./media/hdinsight-hbase-phoenix-squirrel/hdinsight-hbase-squirrel.png
[img-squirrel-sql]: ./media/hdinsight-hbase-phoenix-squirrel/hdinsight-hbase-squirrel-sql.png
