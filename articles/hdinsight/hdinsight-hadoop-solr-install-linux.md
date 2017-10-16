---
title: "スクリプト アクションを使用した Linux ベースの HDInsight での Solr のインストール - Azure | Microsoft Docs"
description: "Script Action を使用して Linux ベースの HDInsight Hadoop クラスターに Solr をインストールする方法について説明します。"
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: cc93ed5c-a358-456a-91a4-f179185c0e98
ms.service: hdinsight
ms.custom: hdinsightactive
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/28/2017
ms.author: larryfr
ms.openlocfilehash: c7a911474d6fb90f45565c90a72bfd407898ceba
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="install-and-use-solr-on-hdinsight-hadoop-clusters"></a>HDInsight Hadoop クラスターに Solr をインストールして使用する

スクリプト アクションを使用して Azure HDInsight に Solr をインストールする方法について説明します。 Solr は強力な検索プラットフォームで、Hadoop が管理するデータに対してエンタープライズ レベルの検索機能を提供します。

> [!IMPORTANT]
    > このドキュメントの手順では、Linux を使用する HDInsight クラスターが必要です。 Linux は、バージョン 3.4 以上の HDInsight で使用できる唯一のオペレーティング システムです。 詳細については、[Windows での HDInsight の提供終了](hdinsight-component-versioning.md#hdinsight-windows-retirement)に関する記事を参照してください。

> [!IMPORTANT]
> このドキュメントで使用するサンプル スクリプトは、特定の構成で Solr 4.9 をインストールします。 この構成とは異なるコレクションやシャード、スキーマ、レプリカなどで Solr クラスターを構成する場合には、スクリプトと Solr バイナリを変更する必要があります。

## <a name="whatis"></a>Solr とは何か

[Apache Solr](http://lucene.apache.org/solr/features.html) は、データに対して強力なフルテキスト検索ができるエンタープライズ検索プラットフォームです。 Hadoop が大量のデータの保存と管理を可能にするのに対し、Apache Solr は迅速にデータを取得するための検索機能を提供します。

> [!WARNING]
> HDInsight クラスターに付属のコンポーネントは、Microsoft によって完全にサポートされています。
>
> Solr といったカスタム コンポーネントについては、問題のトラブルシューティングを進めるための支援として、商業的に妥当な範囲のサポートを受けることができます。 Microsoft サポートでは、カスタム コンポーネントの問題を解決できないことがあります。 支援を受けるため、オープン ソース コミュニティに参加することが必要になる場合があります。 たとえば、[HDInsight についての MSDN フォーラム](https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=hdinsight)や [http://stackoverflow.com](http://stackoverflow.com) などの数多くのコミュニティ サイトを利用できます。また、Apache プロジェクトには、[http://apache.org](http://apache.org) に [Hadoop](http://hadoop.apache.org/) などのプロジェクト サイトもあります。

## <a name="what-the-script-does"></a>スクリプトの機能

このスクリプトは、HDInsight クラスターに次のような変更を加えます。

* Solr 4.9 を `/usr/hdp/current/solr` にインストールします
* Solr サービスの実行に使用されるユーザー **solrusr** を作成します
* **solruser** を `/usr/hdp/current/solr` の所有者として設定します
* Solr を自動的に起動する [Upstart](http://upstart.ubuntu.com/) 構成を追加します。

## <a name="install"></a>スクリプト アクションを使用した Solr のインストール

HDInsight クラスターに Solr をインストールするサンプル スクリプトは、次の場所にあります。

    https://hdiconfigactions.blob.core.windows.net/linuxsolrconfigactionv01/solr-installer-v01.sh

Solr がインストールされているクラスターを作成するには、[HDInsight クラスターの作成](hdinsight-hadoop-create-linux-clusters-portal.md)に関するドキュメントの手順を使用します。 作成プロセスの間に、次の手順を使用して Solr をインストールします。

1. __[クラスターの概要]__ セクションで、[詳細設定] を選択し、__[スクリプト アクション]__ を選択します。 次の情報を使用して、フォームに記入します。

   * **[名前]**: スクリプト アクションの表示名を入力します。
   * **SCRIPT URI**: https://hdiconfigactions.blob.core.windows.net/linuxsolrconfigactionv01/solr-installer-v01.sh
   * **[ヘッド]**: このオプションをオンにします。
   * **[ワーカー]**: このオプションをオンにします
   * **[Zookeeper]**: Zookeeper ノードにインストールするには、このオプションをオンにします
   * **[パラメーター]**: このフィールドは空のままにします。

2. **[スクリプト アクション]** セクションの下部で、**[選択]** を使用して構成を保存します。 最後に、**[次へ]** ボタンを使用して __[クラスターの概要]__ に戻ります。

3. __[クラスターの概要__] ページで、__[作成]__ を選択してクラスターを作成します。

## <a name="usesolr"></a>HDInsight で Solr を使用する方法

> [!IMPORTANT]
> このセクションの手順で、Solr の基本的な機能を示します。 Solr の使用方法の詳細については、[Apache Solr サイト](http://lucene.apache.org/solr/)を参照してください。

### <a name="index-data"></a>データのインデックス付け

以下の手順を使用して Solr にサンプル データを追加し、そのデータを照会します。

1. SSH を使用して HDInsight クラスターに接続します。

    ```bash
    ssh USERNAME@CLUSTERNAME-ssh.azurehdinsight.net
    ```

    詳細については、[HDInsight での SSH の使用](hdinsight-hadoop-linux-use-ssh-unix.md)に関するページを参照してください。

     > [!IMPORTANT]
     > このドキュメントの後半の手順では、SSL トンネルを使用して Solr Web UI に接続します。 これらの手順を使用するには、SSL トンネルを確立し、それを使用するようにブラウザーを構成する必要があります。
     >
     > 詳細については、[HDInsight での SSH トンネリングの使用](hdinsight-linux-ambari-ssh-tunnel.md)に関するドキュメントを参照してください。

2. 次のコマンドを使用して、Solr のサンプル データのインデックスを作成します。

    ```bash
    cd /usr/hdp/current/solr/example/exampledocs
    java -jar post.jar solr.xml monitor.xml
    ```

    コンソールに次の出力が返されます。

        POSTing file solr.xml
        POSTing file monitor.xml
        2 files indexed.
        COMMITting Solr index changes to http://localhost:8983/solr/update..
        Time spent: 0:00:01.624

    `post.jar` ユーティリティは、**solr.xml** ドキュメントと **monitor.xml** ドキュメントをインデックスに追加します。
  
3. 次のコマンドを使用して、Solr REST API クエリを発行します。

    ```bash
    curl "http://localhost:8983/solr/collection1/select?q=*%3A*&wt=json&indent=true"
    ```

    このコマンドは、**collection1** で、**\*:\*** (クエリ文字列内では \*%3A\* としてエンコードされます) に一致するすべてのドキュメントを検索します。 次の JSON ドキュメントは、応答の例です。

            "response": {
                "numFound": 2,
                "start": 0,
                "maxScore": 1,
                "docs": [
                  {
                    "id": "SOLR1000",
                    "name": "Solr, the Enterprise Search Server",
                    "manu": "Apache Software Foundation",
                    "cat": [
                      "software",
                      "search"
                    ],
                    "features": [
                      "Advanced Full-Text Search Capabilities using Lucene",
                      "Optimized for High Volume Web Traffic",
                      "Standards Based Open Interfaces - XML and HTTP",
                      "Comprehensive HTML Administration Interfaces",
                      "Scalability - Efficient Replication to other Solr Search Servers",
                      "Flexible and Adaptable with XML configuration and Schema",
                      "Good unicode support: héllo (hello with an accent over the e)"
                    ],
                    "price": 0,
                    "price_c": "0,USD",
                    "popularity": 10,
                    "inStock": true,
                    "incubationdate_dt": "2006-01-17T00:00:00Z",
                    "_version_": 1486960636996878300
                  },
                  {
                    "id": "3007WFP",
                    "name": "Dell Widescreen UltraSharp 3007WFP",
                    "manu": "Dell, Inc.",
                    "manu_id_s": "dell",
                    "cat": [
                      "electronics and computer1"
                    ],
                    "features": [
                      "30\" TFT active matrix LCD, 2560 x 1600, .25mm dot pitch, 700:1 contrast"
                    ],
                    "includes": "USB cable",
                    "weight": 401.6,
                    "price": 2199,
                    "price_c": "2199,USD",
                    "popularity": 6,
                    "inStock": true,
                    "store": "43.17614,-90.57341",
                    "_version_": 1486960637584081000
                  }
                ]
              }

### <a name="using-the-solr-dashboard"></a>Solr ダッシュボードの使用

Solr ダッシュボードは、Web ブラウザーを介して Solr を操作できる Web UI です。 Solr ダッシュボードは HDInsight クラスターからインターネットに直接公開されません。 SSH トンネルを使用してアクセスすることができます。 SSH トンネルの使用方法の詳細については、[HDInsight での SSH トンネリングの使用](hdinsight-linux-ambari-ssh-tunnel.md)に関するドキュメントを参照してください。

SSH トンネルを確立したら、Solr ダッシュボードを使用するために次の手順を実行します。

1. プライマリ ヘッドノードのホスト名を決定します。

   1. SSH を使用してクラスターのヘッド ノードに接続します。 たとえば、「 `ssh USERNAME@CLUSTERNAME-ssh.azurehdinsight.net`」のように入力します。

       SSH の使用方法の詳細については、[HDInsight での SSH の使用](hdinsight-hadoop-linux-use-ssh-unix.md)に関するページを参照してください。

   2. 次のコマンドを使用して、完全修飾ホスト名を取得します。

        ```bash
        hostname -f
        ```

        このコマンドにより、次のホスト名のような値が返されます。

            hn0-myhdi-nfebtpfdv1nubcidphpap2eq2b.ex.internal.cloudapp.net

        後で使用するため、返された値を保存してください。

2. ブラウザーで、**http://HOSTNAME:8983/solr/#/** に接続します。**HOSTNAME** は前の手順で決定した名前です。

    要求は、SSH トンネル経由でクラスター上の Solr Web UI にルーティングされます。 ページは次のイメージのようになります。

    ![Solr ダッシュボードのイメージ](./media/hdinsight-hadoop-solr-install-linux/solrdashboard.png)

3. 左側のウィンドウで、**[Core Selector]** ボックスを使用して **[collection1]** を選択します。 いくつかのエントリが **[collection1]** の下に表示されます。

4. **[collection1]** の下に表示されたエントリから、**[クエリ]** を選択します。 次の値を使用して、検索ページに値を入力します。

   * **[q]** ボックスに「**\*:**\*」を入力します。 このクエリにより、Solr でインデックス付けされたすべてのドキュメントが返されます。 ドキュメント内の特定の文字列を検索する場合には、ここにその文字列を入力することができます。
   * **[wt]** ボックスでは、出力形式を選択します。 既定値は、 **json**です。

     最後に、検索ページの下部にある **[クエリの実行]** をクリックします。

     ![スクリプト アクションを使ってクラスターをカスタマイズする](./media/hdinsight-hadoop-solr-install-linux/hdi-solr-dashboard-query.png)

     出力では、前にインデックスに追加した 2 つのドキュメントが返されます。 出力は、次の JSON ドキュメントのようになります。

           "response": {
               "numFound": 2,
               "start": 0,
               "maxScore": 1,
               "docs": [
                 {
                   "id": "SOLR1000",
                   "name": "Solr, the Enterprise Search Server",
                   "manu": "Apache Software Foundation",
                   "cat": [
                     "software",
                     "search"
                   ],
                   "features": [
                     "Advanced Full-Text Search Capabilities using Lucene",
                     "Optimized for High Volume Web Traffic",
                     "Standards Based Open Interfaces - XML and HTTP",
                     "Comprehensive HTML Administration Interfaces",
                     "Scalability - Efficient Replication to other Solr Search Servers",
                     "Flexible and Adaptable with XML configuration and Schema",
                     "Good unicode support: héllo (hello with an accent over the e)"
                   ],
                   "price": 0,
                   "price_c": "0,USD",
                   "popularity": 10,
                   "inStock": true,
                   "incubationdate_dt": "2006-01-17T00:00:00Z",
                   "_version_": 1486960636996878300
                 },
                 {
                   "id": "3007WFP",
                   "name": "Dell Widescreen UltraSharp 3007WFP",
                   "manu": "Dell, Inc.",
                   "manu_id_s": "dell",
                   "cat": [
                     "electronics and computer1"
                   ],
                   "features": [
                     "30\" TFT active matrix LCD, 2560 x 1600, .25mm dot pitch, 700:1 contrast"
                   ],
                   "includes": "USB cable",
                   "weight": 401.6,
                   "price": 2199,
                   "price_c": "2199,USD",
                   "popularity": 6,
                   "inStock": true,
                   "store": "43.17614,-90.57341",
                   "_version_": 1486960637584081000
                 }
               ]
             }

### <a name="starting-and-stopping-solr"></a>Solr の開始と停止

Solar を手動で停止または開始するには、次のコマンドを使用します。

```bash
sudo stop solr
sudo start solr
```

## <a name="backup-indexed-data"></a>インデックス付きデータのバックアップ

クラスターの既定のストレージに Solr データをバックアップするには、次の手順を使用します。

1. SSH を使用してクラスターに接続した後、次のコマンドを使用して、ヘッド ノードのホスト名を取得します。

    ```bash
    hostname -f
    ```

2. 次のコマンドを使用して、インデックス付きデータのスナップショットを作成します。 **HOSTNAME** を前のコマンドで返された名前に変えます。

    ```bash
    curl http://HOSTNAME:8983/solr/replication?command=backup
    ```

    応答は次の XML のようになります。

        <?xml version="1.0" encoding="UTF-8"?>
        <response>
          <lst name="responseHeader">
            <int name="status">0</int>
            <int name="QTime">9</int>
          </lst>
          <str name="status">OK</str>
        </response>

3. ディレクトリを `/usr/hdp/current/solr/example/solr` に変更します。 このディレクトリには、コレクションごとにサブディレクトリがあります。 各コレクションのディレクトリには、そのコレクションのスナップショットが格納されている `data` ディレクトリがあります。

4. 次のコマンドを使用して、スナップショット フォルダーの圧縮アーカイブを作成します。

    ```bash
    tar -zcf snapshot.20150806185338855.tgz snapshot.20150806185338855
    ```

    `snapshot.20150806185338855` の値、実際のコレクションのスナップショットの名前で置き換えます。

    このコマンドにより、**snapshot.20150806185338855.tgz** という名前のアーカイブが作成されます。このアーカイブには、**snapshot.20150806185338855** ディレクトリの内容が含まれています。

5. 次のコマンドを使用して、アーカイブをクラスターのプライマリ ストレージに格納できます。

    ```bash
    hdfs dfs -put snapshot.20150806185338855.tgz /example/data
    ```

Solr のバックアップと復元の操作の詳細については、 [https://cwiki.apache.org/confluence/display/solr/Making+and+Restoring+Backups](https://cwiki.apache.org/confluence/display/solr/Making+and+Restoring+Backups)をご覧ください。

## <a name="next-steps"></a>次のステップ

* [HDInsight クラスターでの Giraph のインストール](hdinsight-hadoop-giraph-install-linux.md) クラスターのカスタマイズを使用して、HDInsight Hadoop クラスターに Giraph をインストールします。 Giraph は、Hadoop でグラフの処理を実行するために使用でき、Azure HDInsight で使用できます。

* [HDInsight クラスターに Hue をインストールする](hdinsight-hadoop-hue-linux.md)。 クラスターのカスタマイズを使用して、HDInsight Hadoop クラスターに Hue をインストールします。 Hue は Hadoop クラスターとの情報のやりとりに使用される一連の Web アプリケーションです。

[hdinsight-cluster-customize]: hdinsight-hadoop-customize-cluster-linux.md
