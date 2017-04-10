---
title: "Script Action を使用した Linux ベースの HDInsight での Solr のインストール | Microsoft Docs"
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
ms.date: 01/17/2017
ms.author: larryfr
translationtype: Human Translation
ms.sourcegitcommit: 4f2230ea0cc5b3e258a1a26a39e99433b04ffe18
ms.openlocfilehash: 2214315f084aec80986fe30ebcaff6813162c5f8
ms.lasthandoff: 03/25/2017


---
# <a name="install-and-use-solr-on-hdinsight-hadoop-clusters"></a>HDInsight Hadoop クラスターに Solr をインストールして使用する
このトピックでは、スクリプト アクションを使用して Azure HDInsight に Solr をインストールする方法について説明します。 Solr は強力な検索プラットフォームで、Hadoop が管理するデータに対してエンタープライズ レベルの検索機能を提供します。 HDInsight クラスターに Solr をインストールした後で、Solr を使用したデータの検索方法についても説明します。

> [!IMPORTANT]
> このドキュメントの手順では、Linux を使用する HDInsight クラスターが必要です。 Linux は、バージョン 3.4 以上の HDInsight で使用できる唯一のオペレーティング システムです。 詳細については、[Window での HDInsight の廃止](hdinsight-component-versioning.md#hdi-version-32-and-33-nearing-deprecation-date)に関する記事を参照してください。

このトピックで使用するサンプル スクリプトは、既定の構成で Solr クラスターを作成します。 この構成とは異なるコレクションやシャード、スキーマ、レプリカなどで Solr クラスターを構成する場合には、それに応じてスクリプトと Solr バイナリを変更する必要があります。

## <a name="whatis"></a>Solr とは何か
[Apache Solr](http://lucene.apache.org/solr/features.html) は、データに対して強力なフルテキスト検索ができるエンタープライズ検索プラットフォームです。 Hadoop が大量のデータの保存と管理を可能にするのに対し、Apache Solr は迅速にデータを取得するための検索機能を提供します。 このトピックでは、HDInsight クラスターをカスタマイズして Solr をインストールする方法を説明します。

> [!WARNING]
> HDInsight クラスターに用意されているコンポーネントは全面的にサポートされており、これらのコンポーネントに関連する問題の分離と解決については、Microsoft サポートが支援します。
> 
> Solr といったカスタム コンポーネントについては、問題のトラブルシューティングを進めるための支援として、商業的に妥当な範囲のサポートを受けることができます。 これにより問題が解決する場合もあれば、オープン ソース テクノロジに関して、深い専門知識が入手できる場所への参加をお願いすることになる場合もあります。 たとえば、[HDInsight についての MSDN フォーラム](https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=hdinsight)や [http://stackoverflow.com](http://stackoverflow.com) などの数多くのコミュニティ サイトを利用できます。 また、Apache プロジェクトには、[http://apache.org](http://apache.org) に [Hadoop](http://hadoop.apache.org/) などのプロジェクト サイトもあります。
> 
> 

## <a name="what-the-script-does"></a>スクリプトの機能
このスクリプトは、HDInsight クラスターに次のような変更を加えます。

* `/usr/hdp/current/solr`
* Solr サービスの実行に使用される新しいユーザー、**solrusr** を作成します
* **solruser** を `/usr/hdp/current/solr` の所有者として設定します
* クラスター ノードの再起動時に Solr を開始する [Upstart](http://upstart.ubuntu.com/) 構成を追加します。 Solr は、インストール後にもクラスター ノード上で自動的に開始されます。

## <a name="install"></a>スクリプト アクションを使用した Solr のインストール
HDInsight クラスターに Solr をインストールするサンプル スクリプトは、次の場所にあります。

    https://hdiconfigactions.blob.core.windows.net/linuxsolrconfigactionv01/solr-installer-v01.sh

このセクションでは、Azure ポータルを使用して新しいクラスターを作成する際に、サンプル スクリプトを使用する方法について説明します。 

> [!NOTE]
> スクリプト アクションは、Azure PowerShell、Azure CLI、HDInsight .NET SDK、または Azure Resource Manager のテンプレートを使用して適用することもできます。 既に実行しているクラスターにスクリプト アクションを適用することもできます。 詳細については、 [スクリプト アクションを使用した HDInsight クラスターのカスタマイズ](hdinsight-hadoop-customize-cluster-linux.md)に関する記事を参照してください。
> 
> 

1. [Linux ベースの HDInsight クラスターのプロビジョニング](hdinsight-hadoop-create-linux-clusters-portal.md)に関するページに記載されている手順を使用して、クラスターのプロビジョニングを開始します。ただし、プロビジョニングを完了しないでください。
2. **[オプションの構成]** ブレードで **[スクリプト アクション]** を選択し、以下の情報を指定します。
   
   * **[名前]**: スクリプト アクションの表示名を入力します。
   * **SCRIPT URI**: https://hdiconfigactions.blob.core.windows.net/linuxsolrconfigactionv01/solr-installer-v01.sh
   * **[ヘッド]**: このオプションをオンにします。
   * **[ワーカー]**: このオプションをオンにします
   * **[Zookeeper]**: Zookeeper ノードにインストールするには、このオプションをオンにします
   * **[パラメーター]**: このフィールドは空のままにします。
3. **[スクリプト アクション]** の下部で、**[選択]** を使用して構成を保存します。 最後に、**[オプションの構成]** ブレードの下部にある **[選択]** を使用して、オプションの構成情報を保存します。
4. 「 [Provision Linux-based HDInsight clusters (Linux ベースの HDInsight クラスターのプロビジョニング)](hdinsight-hadoop-create-linux-clusters-portal.md)」の説明に従って、クラスターのプロビジョニングを続行します。

## <a name="usesolr"></a>HDInsight で Solr を使用する方法
### <a name="indexing-data"></a>データのインデックス作成
最初に、いくつかのデータ ファイルに対し Solr のインデックスを作成する必要があります。 これにより、インデックス付きデータに対して、Solr を使用して検索クエリを実行できます。 以下の手順を使用して Solr にいくつかのサンプル データを追加し、そのデータを照会します。

1. SSH を使用して HDInsight クラスターに接続します。
   
        ssh USERNAME@CLUSTERNAME-ssh.azurehdinsight.net
   
    詳細については、[HDInsight での SSH の使用](hdinsight-hadoop-linux-use-ssh-unix.md)に関するページを参照してください。
     
     > [!IMPORTANT]
     > このドキュメントの後半の手順で、SSL トンネルを使用して Solr Web UI に接続します。 これらの手順を使用するには、SSL トンネルを確立し、それをブラウザーで使用するように構成する必要があります。
     > 
     > 詳細については、 [SSH トンネリングを使用して Ambari Web UI、ResourceManager、JobHistory、NameNode、Oozie、およびその他の Web UI にアクセスする](hdinsight-linux-ambari-ssh-tunnel.md)
     > 
     > 
2. 次のコマンドを使用して、Solr のサンプル データのインデックスを作成します。
   
        cd /usr/hdp/current/solr/example/exampledocs
        java -jar post.jar solr.xml monitor.xml
   
    コンソールで、次の出力が表示します。
   
        POSTing file solr.xml
        POSTing file monitor.xml
        2 files indexed.
        COMMITting Solr index changes to http://localhost:8983/solr/update..
        Time spent: 0:00:01.624
   
    post.jar ユーティリティは、**solr.xml** と **monitor.xml** という 2 つのサンプル ドキュメントで Solr のインデックスを作成します。 これらは、Solr 内の **collection1** に格納されます。
3. 次のコマンドを使用して、Solr によって公開される REST API を照会します。
   
        curl "http://localhost:8983/solr/collection1/select?q=*%3A*&wt=json&indent=true"
   
    これは、**collection1** で **\*:\*** (クエリ文字列では \*%3A\* としてエンコードされます) に一致するすべてのドキュメントに対するクエリを発行します。その応答は JSON として返されます。 応答は次のように表示されます。
   
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
Solr ダッシュボードは、Web ブラウザーを介して Solr を操作できる Web UI です。 Solr ダッシュボードは HDInsight クラスターからインターネットに直接公開されませんが、SSH トンネルを使用してアクセスする必要があります。 SSH トンネルの使用の詳細については、 [SSH トンネリングを使用して Ambari Web UI、ResourceManager、JobHistory、NameNode、Oozie、およびその他の Web UI にアクセスする](hdinsight-linux-ambari-ssh-tunnel.md)

SSH トンネルを確立したら、Solr ダッシュボードを使用するために次の手順を実行します。

1. プライマリ ヘッドノードのホスト名を決定します。
   
   1. ポート 22 で SSH を使用してクラスターに接続します。 たとえば、`ssh USERNAME@CLUSTERNAME-ssh.azurehdinsight.net` を使用して、**USERNAME** に SSH ユーザー名を、**CLUSTERNAME** にクラスターの名前を指定します。
      
       詳細については、[HDInsight での SSH の使用](hdinsight-hadoop-linux-use-ssh-unix.md)に関するページを参照してください。

   2. 次のコマンドを使用して、完全修飾ホスト名を取得します。
      
           hostname -f
      
       次のような名前が返されます。
      
           hn0-myhdi-nfebtpfdv1nubcidphpap2eq2b.ex.internal.cloudapp.net
      
       これが、次の手順で使用するホスト名です。
2. ブラウザーで、**http://HOSTNAME:8983/solr/#/** に接続します。**HOSTNAME** は前の手順で決定した名前です。 
   
    この要求は、SSH トンネルを介して HDInsight クラスターのヘッド ノードにルーティングする必要があります。 次のようなページが表示されます。
   
    ![Solr ダッシュボードのイメージ](./media/hdinsight-hadoop-solr-install-linux/solrdashboard.png)
3. 左側のウィンドウで、**[Core Selector]** ボックスを使用して **[collection1]** を選択します。 いくつかのエントリが **[collection1]** の下に表示されます。
4. **[collection1]** の下に表示されたエントリから、**[クエリ]** を選択します。 次の値を使用して、検索ページに値を入力します。
   
   * **[q]** ボックスに「**\*:**\*」を入力します。 これにより、Solr でインデックス付けされたすべてのドキュメントが返されます。 ドキュメント内の特定の文字列を検索する場合には、ここにその文字列を入力することができます。
   * **[wt]** ボックスでは、出力形式を選択します。 既定値は、 **json**です。
     
     最後に、検索ページの下部にある **[クエリの実行]** をクリックします。
     
     ![スクリプト アクションを使ってクラスターをカスタマイズする](./media/hdinsight-hadoop-solr-install-linux/hdi-solr-dashboard-query.png)
     
     これによる出力は、Solr のインデックス作成のために使用した 2 つのドキュメントを返します。 出力結果は、以下のようになります。
     
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
Solar を手動で停止または開始する必要がある場合は、次のコマンドを使用します。

    sudo stop solr

    sudo start solr

## <a name="backup-indexed-data"></a>インデックス付きデータのバックアップ
インデックス付きデータを Solr のクラスター ノードから Azure BLOB ストレージ にバックアップすることをお勧めします。 そのために、次の手順を実行してください。

1. SSH を使用してクラスターに接続した後、次のコマンドを使用して、ヘッド ノードのホスト名を取得します。
   
        hostname -f
2. 次を使用し、インデックス付きデータのスナップショットを作成します。 **HOSTNAME** を前のコマンドで返された名前に変えます。
   
        curl http://HOSTNAME:8983/solr/replication?command=backup
   
    次のように、応答が表示されます。
   
        <?xml version="1.0" encoding="UTF-8"?>
        <response>
          <lst name="responseHeader">
            <int name="status">0</int>
            <int name="QTime">9</int>
          </lst>
          <str name="status">OK</str>
        </response>
3. 次に、**/usr/hdp/current/solr/example/solr** ディレクトリに移動します。 このディレクトリには、コレクションごとにサブディレクトリがあります。 各コレクションのディレクトリには **data** ディレクトリがあり、そのコレクションのスナップショットが格納されています。
   
    たとえば、これまでに説明した手順を使用してサンプル ドキュメントにインデックスを作成した場合、**/usr/hdp/current/solr/example/solr/collection1/data** ディレクトリには **snapshot.###########** という名前のディレクトリが含まれています。# はスナップショットの日時です。
4. 次のようなコマンドを使用して、スナップショット フォルダーの圧縮アーカイブを作成します。
   
        tar -zcf snapshot.20150806185338855.tgz snapshot.20150806185338855
   
    これにより、**snapshot.20150806185338855.tgz** という名前の新しいアーカイブが作成されます。このアーカイブには、**snapshot.20150806185338855** ディレクトリの内容が含まれています。
5. 次のコマンドを使用して、アーカイブをクラスターのプライマリ ストレージに格納できます。
   
    hadoop fs -copyFromLocal snapshot.20150806185338855.tgz /example/data
   
   > [!NOTE]
   > Solr スナップショットを格納するための専用ディレクトリを作成することもできます。 たとえば、「 `hadoop fs -mkdir /solrbackup`」のように入力します。
   > 
   > 

Solr のバックアップと復元の操作の詳細については、 [SolrCores のバックアップの作成と復元](https://cwiki.apache.org/confluence/display/solr/Making+and+Restoring+Backups+of+SolrCores)に関するページを参照してください。

## <a name="see-also"></a>関連項目
* [HDInsight クラスターに Hue をインストールして使用する](hdinsight-hadoop-hue-linux.md)。 Hue は、Pig ジョブや Hive ジョブの作成、実行、保存や、HDInsight クラスターの既定のストレージの参照を容易にする Web UI です。
* [HDInsight クラスターに R をインストールする][hdinsight-install-r]。 クラスターのカスタマイズを使用して、HDInsight Hadoop クラスターに R をインストールします。 R は、統計計算用のオープン ソースの言語および環境です。 R は、数百の組み込み統計関数と、関数型プログラミングとオブジェクト指向のプログラミングの特徴を結合した独自のプログラミング言語を提供します。 また、広範なグラフィカル機能も提供します。
* [HDInsight クラスターに Giraph をインストールする](hdinsight-hadoop-giraph-install-linux.md)。 クラスターのカスタマイズを使用して、HDInsight Hadoop クラスターに Giraph をインストールします。 Giraph は、Hadoop でグラフの処理を実行するために使用でき、Azure HDInsight で使用できます。
* [HDInsight クラスターに Hue をインストールする](hdinsight-hadoop-hue-linux.md)。 クラスターのカスタマイズを使用して、HDInsight Hadoop クラスターに Hue をインストールします。 Hue は Hadoop クラスターとの情報のやりとりに使用される一連の Web アプリケーションです。

[hdinsight-install-r]: hdinsight-hadoop-r-scripts-linux.md
[hdinsight-cluster-customize]: hdinsight-hadoop-customize-cluster-linux.md

