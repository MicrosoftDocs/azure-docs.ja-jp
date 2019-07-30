---
title: Hive Warehouse Connector を使用して Apache Spark と Apache Hive を統合する
description: Azure HDInsight 上で Hive Warehouse Connector を使用して Apache Spark と Apache Hive を統合する方法について説明します。
ms.service: hdinsight
author: nakhanha
ms.author: nakhanha
ms.reviewer: hrasheed
ms.topic: conceptual
ms.date: 04/29/2019
ms.openlocfilehash: b245661ab8f26c1f529a049d326d2c72838c7a17
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/13/2019
ms.locfileid: "67056730"
---
# <a name="integrate-apache-spark-and-apache-hive-with-the-hive-warehouse-connector"></a>Hive Warehouse Connector を使用して Apache Spark と Apache Hive を統合する

Apache Hive Warehouse Connector (HWC) は、Apache Spark と Apache Hive をより簡単に連携できるライブラリです。Spark DataFrame と Hive テーブルの間のデータ移動などのタスクをサポートするほか、Spark ストリーミング データを Hive テーブルに転送します。 Hive Warehouse Connector は、Spark と Hive の間で橋渡しのように動作します。 Scala、Java、Python が開発用にサポートされます。

Hive Warehouse Connector を使用すると、Hive および Spark の独自の機能を活用して、強力なビッグデータ アプリケーションを構築できます。 Apache Hive では、ACID (原子性、一貫性、分離性、持続性) なデータベース トランザクションがサポートされています。 Hive における ACID およびトランザクションの詳細については、「[Hive Transactions (Hive トランザクション)](https://cwiki.apache.org/confluence/display/Hive/Hive+Transactions)」を参照してください。 Hive には、Apache Ranger を通じた詳細なセキュリティ コントロールと Apache Spark では使用できない Low Latency Analytical Processing も備わっています。

Apache Spark には、Apache Hive では使用できないストリーミング機能を提供する Structured Streaming API があります。 HDInsight 4.0 以降、Apache Spark 2.3.1 と Apache Hive 3.1.0 は異なる metastore になっており、相互運用性が困難になることがあります。 Hive Warehouse Connector によって、Spark と Hive を一緒に使用することが容易になります。 HWC ライブラリによって LLAP デーモンから Spark Executor にデータが並列で読み込まれるため、Spark から Hive への標準的な JDBC 接続を使用するよりも効率的でスケーラブルになります。

![アーキテクチャ](./media/apache-hive-warehouse-connector/hive-warehouse-connector-architecture.png)

Hive Warehouse Connector でサポートされる操作の一部を次に示します。

* テーブルを記述する
* ORC 形式データ用のテーブルを作成する
* Hive データを選択して DataFrame を取得する
* DataFrame を Hive にバッチで書き込む
* Hive の更新ステートメントを実行する
* Hive からテーブル データを読み取り、Spark で変換し、新しい Hive テーブルに書き込む
* Hive ストリーミングを使用して DataFrame または Spark ストリームを Hive に書き込む

## <a name="hive-warehouse-connector-setup"></a>Hive Warehouse Connector の設定

次の手順に従って、Azure HDInsight の Spark および対話型クエリ クラスターの間で Hive Warehouse Connector を設定します。

1. Azure portal を使用し、ストレージ アカウントとカスタム Azure 仮想ネットワークを使って HDInsight Spark 4.0 クラスターを作成します。 Azure 仮想ネットワークでのクラスターの作成については、「[既存の仮想ネットワークへの HDInsight の追加](../../hdinsight/hdinsight-extend-hadoop-virtual-network.md#existingvnet)」を参照してください。
1. Azure portal を使用し、Spark クラスターと同じストレージ アカウントと Azure 仮想ネットワークを使って HDInsight 対話型クエリ (LLAP) 4.0 クラスターを作成します。
1. 自分の対話型クエリ クラスターの headnode0 にある `/etc/hosts` ファイルの内容を、自分の Spark クラスターの headnode0 にある `/etc/hosts` ファイルにコピーします。 この手順によって、自分の Spark クラスターが対話型クエリ クラスター内のノードの IP アドレスを解決できるようになります。 `cat /etc/hosts` を使用して、更新されたファイルの内容を表示します。 出力は、次のスクリーンショットに示されている内容のようになります。

    ![hosts ファイルの表示](./media/apache-hive-warehouse-connector/hive-warehouse-connector-hosts-file.png)

1. 次の手順に従って、Spark クラスターの設定を行います。 
    1. Azure portal に移動し、HDInsight クラスターを選択してから、自分のクラスターの名前をクリックします。
    1. 右側で**クラスター ダッシュボード**の下にある **[Ambari ホーム]** を選択します。
    1. Ambari Web UI で、 **[SPARK2]**  >  **[CONFIGS]\(構成\)**  >  **[Custom spark2-defaults]\(カスタム spark2 既定値\)** の順にクリックします。

        ![Spark2 Ambari の構成](./media/apache-hive-warehouse-connector/hive-warehouse-connector-spark2-ambari.png)

    1. `spark.hadoop.hive.llap.daemon.service.hosts` を、**Advanced hive-interactive-env** のプロパティ **LLAP app name** と同じ値に設定します。 たとえば、`@llap0` のように指定します。

    1. `spark.sql.hive.hiveserver2.jdbc.url` を JDBC 接続文字列に設定します。これにより、対話型クエリ クラスター上の Hiveserver2 に接続されます。 クラスターの接続文字列は、次の URI のようになります。 `CLUSTERNAME` は自分の Spark クラスターの名前であり、`user` および `password` パラメーターは自分のクラスターに対して適切な値に設定されます。

        ```
        jdbc:hive2://LLAPCLUSTERNAME.azurehdinsight.net:443/;user=admin;password=PWD;ssl=true;transportMode=http;httpPath=/hive2
        ```

        >[!Note] 
        > JDBC URL には、ユーザー名とパスワードを含め、Hiveserver2 に接続するための資格情報が含まれている必要があります。

    1. `spark.datasource.hive.warehouse.load.staging.dir` を、HDFS と互換性のある適切なステージング ディレクトリに設定します。 2 つの異なるクラスターがある場合、ステージング ディレクトリは、HiveServer2 がそこにアクセスできるよう、LLAP クラスターのストレージ アカウントのステージング ディレクトリにあるフォルダーである必要があります。 たとえば `wasb://STORAGE_CONTAINER_NAME@STORAGE_ACCOUNT_NAME.blob.core.windows.net/tmp` だと、`STORAGE_ACCOUNT_NAME` はクラスターによって使用されているストレージ アカウントの名前で、`STORAGE_CONTAINER_NAME` はストレージ コンテナーの名前です。

    1. 対話型クエリ クラスターの metastore URI の値を使用して、`spark.datasource.hive.warehouse.metastoreUri` を設定します。 自分の LLAP クラスターの metastoreUri を見つけるには、Ambari UI で、 **[Hive]**  >  **[ADVANCED]\(詳細設定\)**  >  **[General]\(全般\)** にある LLAP クラスターの **hive.metastore.uris** プロパティを探します。 この値は次の URI のようになります。

        ```
        thrift://hn0-hwclla.0iv2nyrmse1uvp2caa4e34jkmf.cx.internal.cloudapp.net:9083,
        thrift://hn1-hwclla.0iv2nyrmse1uvp2caa4e34jkmf.cx.internal.cloudapp.net:9083
        ```

    1. YARN クライアント デプロイ モードについて、`spark.security.credentials.hiveserver2.enabled` を `false` に設定します。
    1. `spark.hadoop.hive.zookeeper.quorum` を自分の LLAP クラスターの Zookeeper クォーラムに設定します。 自分の LLAP クラスターの Zookeeper クォーラムを見つけるには、Ambari UI で、 **[Hive]**  >  **[ADVANCED]\(詳細設定\)**  >  **[Advanced hive-site]\(高度な hive-site\)** にある LLAP クラスターの **hive.zookeeper.quorum** プロパティを探します。 この値は次の文字列のようになります。

        ```
        zk1-nkhvne.0iv2nyrmse1uvp2caa4e34jkmf.cx.internal.cloudapp.net:2181,
        zk4-nkhvne.0iv2nyrmse1uvp2caa4e34jkmf.cx.internal.cloudapp.net:2181,
        zk6-nkhvne.0iv2nyrmse1uvp2caa4e34jkmf.cx.internal.cloudapp.net:2181
        ```

Hive Warehouse Connector の構成をテストするには、「[クエリの接続と実行](#connecting-and-running-queries)」セクションの手順に従います。

## <a name="using-the-hive-warehouse-connector"></a>Hive Warehouse Connector の使用

### <a name="connecting-and-running-queries"></a>クエリの接続と実行

いくつかの異なる方法の中から選択し、Hive Warehouse Connector を使って自分の対話型クエリ クラスターに接続してクエリを実行できます。 サポートされている方法には次のツールがあります。

* [spark-shell](../spark/apache-spark-shell.md)
* PySpark
* spark-submit
* [Zeppelin](../spark/apache-spark-zeppelin-notebook.md)
* [Livy](../spark/apache-spark-livy-rest-interface.md)

この記事で提供されるすべての例は、spark-shell を通じて実行されます。

spark-shell セッションを開始するには、次の手順を実行します。

1. 自分のクラスターのヘッドノードに SSH 接続します。 クラスターへの SSH 接続の詳細については、「[SSH を使用して HDInsight (Apache Hadoop) に接続する](../../hdinsight/hdinsight-hadoop-linux-use-ssh-unix.md)」を参照してください。
1. 「`cd /usr/hdp/current/hive_warehouse_connector`」を入力して適切なディレクトリに変更するか、spark-shell コマンドでパラメーターとして使用されるすべての jar ファイルへの完全なパスを指定します。
1. 次のコマンドを入力して、spark-shell を起動します。

    ```bash
    spark-shell --master yarn \
    --jars /usr/hdp/3.0.1.0-183/hive_warehouse_connector/hive-warehouse-connector-assembly-1.0.0.3.0.1.0-183.jar \
    --conf spark.security.credentials.hiveserver2.enabled=false
    ```

1. ウェルカム メッセージと `scala>` プロンプトが表示されます。ここで、コマンドを入力することができます。

1. spark-shell の起動後、次のコマンドを使用して Hive Warehouse Connector インスタンスを開始できます。

    ```scala
    import com.hortonworks.hwc.HiveWarehouseSession
    val hive = HiveWarehouseSession.session(spark).build()
    ```

### <a name="connecting-and-running-queries-on-enterprise-security-package-esp-clusters"></a>Enterprise セキュリティ パッケージ (ESP) クラスター上でのクエリの接続と実行

Enterprise セキュリティ パッケージ (ESP) を使用すると、Active Directory ベースの認証、マルチユーザーのサポート、ロールベースのアクセス制御など、エンタープライズレベルの機能を Azure HDInsight の Apache Hadoop クラスターで利用できます。 ESP の詳細については、「[Enterprise セキュリティ パッケージを使用する Apache Hadoop セキュリティの概要](../domain-joined/apache-domain-joined-introduction.md)」を参照してください。

1. 「[クエリの接続と実行](#connecting-and-running-queries)」の最初の手順 1 と 2 に従います。
1. 「`kinit`」と入力して、ドメイン ユーザーでログインします。
1. 次に示すように、構成パラメーターの完全なリストを使用して、spark-shell を起動します。 山かっこ内のすべてが大文字の値は全部、自分のクラスターに基づいて指定する必要があります。 下のいずれかのパラメーターに入力する値を確認する必要がある場合は、「[Hive Warehouse Connector の設定](#hive-warehouse-connector-setup)」のセクションを参照してください。

    ```bash
    spark-shell --master yarn \
    --jars /usr/hdp/3.0.1.0-183/hive_warehouse_connector/hive-warehouse-connector-assembly-1.0.0.3.0.1.0-183.jar \
    --conf spark.security.credentials.hiveserver2.enabled=false
    --conf spark.hadoop.hive.llap.daemon.service.hosts='<LLAP_APP_NAME>'
    --conf spark.sql.hive.hiveserver2.jdbc.url='jdbc:hive2://<ZOOKEEPER_QUORUM>;serviceDiscoveryMode=zookeeper;zookeeperNamespace=hiveserver2-interactive'
    --conf spark.datasource.hive.warehouse.load.staging.dir='<STAGING_DIR>'
    --conf spark.datasource.hive.warehouse.metastoreUri='<METASTORE_URI>'
    --conf spark.hadoop.hive.zookeeper.quorum='<ZOOKEEPER_QUORUM>'
   ```

### <a name="creating-spark-dataframes-from-hive-queries"></a>Hive クエリからの Spark DataFrame の作成

HWC ライブラリを使用したすべてのクエリの結果は、DataFrame として返されます。 次の例は、基本的なクエリを作成する方法を示しています。

```scala
hive.setDatabase("default")
val df = hive.executeQuery("select * from hivesampletable")
df.filter("state = 'Colorado'").show()
```

クエリの結果は Spark DataFrame です。これは、MLIB や SparkSQL のような Spark ライブラリと共に使用できます。

### <a name="writing-out-spark-dataframes-to-hive-tables"></a>Spark DataFrame から Hive テーブルへの書き出し

Spark は、Hive によって管理される ACID テーブルへの書き込みをネイティブにサポートしていません。 しかし、HWC を使用することで、どの DataFrame も Hive テーブルに書き出すことができます。 次の例では、この機能の動作を確認できます。

1. 次のコマンドを使用し、`sampletable_colorado` というテーブルを作成してその列を指定します。

    ```scala
    hive.createTable("sampletable_colorado").column("clientid","string").column("querytime","string").column("market","string").column("deviceplatform","string").column("devicemake","string").column("devicemodel","string").column("state","string").column("country","string").column("querydwelltime","double").column("sessionid","bigint").column("sessionpagevieworder","bigint").create()
    ```

2. テーブル `hivesampletable` をフィルターします (列 `state` = `Colorado`)。 Hive テーブルのこのクエリは Spark DataFrame として返されます。 その後 DataFrame は、`write` 関数を使用して Hive テーブル `sampletable_colorado` に保存されます。
    
    ```scala
    hive.table("hivesampletable").filter("state = 'Colorado'").write.format(HiveWarehouseSession.HIVE_WAREHOUSE_CONNECTOR).option("table","sampletable_colorado").save()
    ```

次のスクリーンショットで結果のテーブルを確認できます。

![結果のテーブルの表示](./media/apache-hive-warehouse-connector/hive-warehouse-connector-show-hive-table.png)

### <a name="structured-streaming-writes"></a>構造化ストリーミングの書き込み

Hive Warehouse Connector を使用すると、Spark ストリーミングを使って Hive テーブルにデータを書き込むことができます。

下の手順に従って、localhost ポート 9999 上の Spark ストリームから Hive テーブルにデータを取り込む Hive Warehouse Connector サンプルを作成します。

1. 自分の Spark クラスターでターミナルを開きます。
1. 次のコマンドを使用して、Spark ストリームを開始します。

    ```scala
    val lines = spark.readStream.format("socket").option("host", "localhost").option("port",9988).load()
    ```

1. 次の手順を実行して、作成した Spark ストリームのためのデータを生成します。
    1. 同じ Spark クラスターで別のターミナルを開きます。
    1. コマンド プロンプトで「 `nc -lk 9999`」と入力します。 このコマンドでは、netcat ユーティリティを使用して、コマンド ラインから指定のポートにデータを送信します。
    1. Spark ストリームに取り込みたい語句を入力してから、キャリッジ リターンを入力します。
        ![Spark ストリームへの入力データ](./media/apache-hive-warehouse-connector/hive-warehouse-connector-spark-stream-data-input.png)
1. ストリーミング データを保持する新しい Hive テーブルを作成します。 spark-shell で、次のコマンドを入力します。

    ```scala
    hive.createTable("stream_table").column("value","string").create()
    ```

1. 次のコマンドを使用して、新しく作成したテーブルにストリーミング データを書き込みます。

    ```scala
    lines.filter("value = 'HiveSpark'").writeStream.format(HiveWarehouseSession.STREAM_TO_STREAM).option("database", "default").option("table","stream_table").option("metastoreUri",spark.conf.get("spark.datasource.hive.warehouse.metastoreUri")).option("checkpointLocation","/tmp/checkpoint1").start()
    ```

    >[!Important]
    > 現在、`metastoreUri` および `database` オプションは、Apache Spark の既知の問題のため、手動で設定する必要があります。 この問題の詳細については、[SPARK-25460](https://issues.apache.org/jira/browse/SPARK-25460) を参照してください。

1. 次のコマンドを使用して、テーブルに挿入されたデータを表示することができます。

    ```scala
    hive.table("stream_table").show()
    ```

### <a name="securing-data-on-spark-esp-clusters"></a>Spark ESP クラスター上のデータのセキュリティ保護

1. 次のコマンドを入力して、一定のサンプル データが含まれるテーブル `demo` を作成します。

    ```scala
    create table demo (name string);
    INSERT INTO demo VALUES ('HDinsight');
    INSERT INTO demo VALUES ('Microsoft');
    INSERT INTO demo VALUES ('InteractiveQuery');
    ```

1. 次のコマンドを使用して、テーブルの内容を表示します。 ポリシーを適用する前、`demo` テーブルにはすべての列が表示されます。

    ```scala
    hive.executeQuery("SELECT * FROM demo").show()
    ```

    ![Ranger ポリシーを適用する前のデモ テーブル](./media/apache-hive-warehouse-connector/hive-warehouse-connector-table-before-ranger-policy.png)

1. 列の最後の 4 文字だけが表示される列マスク ポリシーを適用します。  
    1. `https://CLUSTERNAME.azurehdinsight.net/ranger/` で Ranger 管理 UI に移動します。
    1. **[Hive]** の下にある自分のクラスターの Hive サービスをクリックします。
        ![Ranger ポリシーを適用する前のデモ テーブル](./media/apache-hive-warehouse-connector/hive-warehouse-connector-ranger-service-manager.png)
    1. **[Masking]\(マスク\)** タブ、 **[Add New Policy]\(新しいポリシーの追加\)** の順にクリックします。![ポリシーの一覧](./media/apache-hive-warehouse-connector/hive-warehouse-connector-ranger-hive-policy-list.png)
    1. 目的のポリシー名を入力します。 次のように選択します。データベース: **default**、Hive テーブル: **demo**、Hive 列: **name**、ユーザー: **rsadmin2**、アクセスの種類: **select**、 **[Select Masking Option]\(マスク オプションの選択\)** メニュー: **Partial mask: show last 4**。 **[追加]** をクリックします。
                ![ポリシー一覧](./media/apache-hive-warehouse-connector/hive-warehouse-connector-ranger-create-policy.png)
1. テーブルの内容をもう一度表示します。 Ranger ポリシーの適用後は、列の最後の 4 文字だけを確認できます。

    ![Ranger ポリシーを適用した後のデモ テーブル](./media/apache-hive-warehouse-connector/hive-warehouse-connector-table-after-ranger-policy.png)

## <a name="next-steps"></a>次の手順

* [HDInsight での対話型クエリの使用](https://docs.microsoft.com/azure/hdinsight/interactive-query/apache-interactive-query-get-started)
* [Zeppelin、Livy、spark-submit、pyspark を使用した Hive Warehouse Connector の操作の例](https://community.hortonworks.com/articles/223626/integrating-apache-hive-with-apache-spark-hive-war.html)
