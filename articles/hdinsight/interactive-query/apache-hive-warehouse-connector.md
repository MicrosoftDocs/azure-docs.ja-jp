---
title: Apache Spark & Hive - Hive Warehouse Connector - Azure HDInsight
description: Azure HDInsight 上で Hive Warehouse Connector を使用して Apache Spark と Apache Hive を統合する方法について説明します。
author: nis-goel
ms.author: nisgoel
ms.service: hdinsight
ms.topic: how-to
ms.date: 05/28/2020
ms.openlocfilehash: 57a3d76f24c33984a883e926a8d4c68736e9f121
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/23/2021
ms.locfileid: "104869890"
---
# <a name="integrate-apache-spark-and-apache-hive-with-hive-warehouse-connector-in-azure-hdinsight"></a>Azure HDInsight で Hive Warehouse Connector を使用して Apache Spark と Apache Hive を統合する

Apache Hive Warehouse Connector (HWC) は、Apache Spark と Apache Hive でより簡単に作業できるようにするライブラリです。 Spark DataFrames と Hive テーブル間でデータを移動するなどのタスクをサポートしています。 また、Spark ストリーミング データを Hive テーブルに転送します。 Hive Warehouse Connector は、Spark と Hive の間で橋渡しのように動作します。 Scala、Java、Python も開発用のプログラミング言語としてサポートされます。

Hive Warehouse Connector を使用すると、Hive および Spark の独自の機能を活用して、強力なビッグデータ アプリケーションを構築できます。

Apache Hive では、ACID (原子性、一貫性、分離性、持続性) なデータベース トランザクションがサポートされています。 Hive における ACID およびトランザクションの詳細については、「[Hive Transactions (Hive トランザクション)](https://cwiki.apache.org/confluence/display/Hive/Hive+Transactions)」を参照してください。 Hive には、Apache Ranger を通じた詳細なセキュリティ コントロールと、Apache Spark では使用できない Low Latency Analytical Processing (LLAP) も備わっています。

Apache Spark には、Apache Hive では使用できないストリーミング機能を提供する Structured Streaming API があります。 HDInsight 4.0 以降、Apache Spark 2.3.1 と Apache Hive 3.1.0 は異なるメタストアになっています。 個別のメタストアによって相互運用性が困難になる可能性があります。 Hive Warehouse Connector によって、Spark と Hive を一緒に使用することが容易になります。 HWC ライブラリは、LLAP デーモンから Spark Executor にデータを並列で読み込みます。 このプロセスにより、Spark から Hive への標準の JDBC 接続よりも効率性と適応性が高まります。

:::image type="content" source="./media/apache-hive-warehouse-connector/hive-warehouse-connector-architecture.png" alt-text="Hive Warehouse Connector のアーキテクチャ" border="true":::

Hive Warehouse Connector でサポートされる操作の一部を次に示します。

* テーブルを記述する
* ORC 形式データ用のテーブルを作成する
* Hive データを選択して DataFrame を取得する
* DataFrame を Hive にバッチで書き込む
* Hive の更新ステートメントを実行する
* Hive からテーブル データを読み取り、Spark で変換し、新しい Hive テーブルに書き込む
* Hive ストリーミングを使用して DataFrame または Spark ストリームを Hive に書き込む

## <a name="hive-warehouse-connector-setup"></a>Hive Warehouse Connector の設定

> [!IMPORTANT]
> - Spark 2.4 Enterprise セキュリティ パッケージ クラスターにインストールされている HiveServer2 Interactive インスタンスは、Hive Warehouse Connector での使用がサポートされていません。 代わりに、HiveServer2 Interactive ワークロードをホストするために、別の HiveServer2 Interactive クラスターを構成する必要があります。 単一の Spark 2.4 クラスターを利用する Hive Warehouse Connector の構成はサポートされていません。
> - Hive Warehouse Connector (HWC) ライブラリは、ワークロード管理 (WLM) 機能が有効にされた Interactive Query クラスターでの使用がサポートされていません。 <br>
Spark ワークロードのみが存在し、HWC ライブラリを使用するシナリオでは、Interactive Query クラスターでワークロード管理機能が有効にされていないことを確認してください (`hive.server2.tez.interactive.queue` 構成は、ハイブ構成で設定されません)。 <br>
Spark ワークロード (HWC) と LLAP ネイティブ ワークロードの両方が存在するシナリオでは、共有メタストア データベースを使用して、2 つの個別の Interactive Query クラスターを作成する必要があります。 必要に応じて WLM 機能を有効にできるネイティブ LLAP ワークロード用の 1 つのクラスターと WLM 機能を構成しない HWC のみのワークロード用の他方のクラスター。
1 つのクラスターでのみ有効にされている場合でも、両方のクラスターから WLM リソース プランを表示できることに注意してください。 WLM 機能が無効にされているクラスターのリソース プランの変更は、他方のクラスターの WLM 機能に影響する可能性があるため、行わないでください。

Hive Warehouse Connector には、Spark ワークロードと Interactive Query ワークロード用に、個別のクラスターが必要です。 次の手順に従って、Azure HDInsight にこれらのクラスターを設定します。

### <a name="create-clusters"></a>クラスターの作成

1. ストレージ アカウントとカスタム Azure 仮想ネットワークを使って HDInsight Spark **4.0** クラスターを作成します。 Azure 仮想ネットワークでのクラスターの作成については、「[既存の仮想ネットワークへの HDInsight の追加](../../hdinsight/hdinsight-plan-virtual-network-deployment.md#existingvnet)」を参照してください。

1. Spark クラスターと同じストレージ アカウントと Azure 仮想ネットワークを使って HDInsight 対話型クエリ (LLAP) **4.0** クラスターを作成します。

### <a name="configure-hwc-settings"></a>HWC 設定の構成

#### <a name="gather-preliminary-information"></a>準備情報の収集

1. Web ブラウザーで `https://LLAPCLUSTERNAME.azurehdinsight.net/#/main/services/HIVE` に移動します。ここで、LLAPCLUSTERNAME は Interactive Query クラスターの名前です。

1. **[概要]**  >  **[HiveServer2 Interactive JDBC URL]** の順に移動し、値を書き留めます。 値は次のようになります。`jdbc:hive2://zk0-iqgiro.rekufuk2y2ce.bx.internal.cloudapp.net:2181,zk1-iqgiro.rekufuk2y2ce.bx.internal.cloudapp.net:2181,zk4-iqgiro.rekufuk2y2ce.bx.internal.cloudapp.net:2181/;serviceDiscoveryMode=zooKeeper;zooKeeperNamespace=hiveserver2-interactive`

1. **[Configs]**  >  **[Advanced]**  >  **[Advanced hive-site]**  >  **[hive.zookeeper.quorum]** の順に移動し、値を書き留めます。 値は次のようになります。`zk0-iqgiro.rekufuk2y2cezcbowjkbwfnyvd.bx.internal.cloudapp.net:2181,zk1-iqgiro.rekufuk2y2cezcbowjkbwfnyvd.bx.internal.cloudapp.net:2181,zk4-iqgiro.rekufuk2y2cezcbowjkbwfnyvd.bx.internal.cloudapp.net:2181`

1. **[Configs]**  >  **[Advanced]**  >  **[General]**  >  **[hive.metastore.uris]** の順に移動し、値を書き留めます。 値は次のようになります。`thrift://iqgiro.rekufuk2y2cezcbowjkbwfnyvd.bx.internal.cloudapp.net:9083,thrift://hn1-iqgiro.rekufuk2y2cezcbowjkbwfnyvd.bx.internal.cloudapp.net:9083`

1. **[Configs]**  >  **[Advanced]**  >  **[Advanced hive-interactive-site]**  >  **[hive.llap.daemon.service.hosts]** の順に移動し、値を書き留めます。 値は次のようになります。`@llap0`

#### <a name="configure-spark-cluster-settings"></a>Spark クラスター設定の構成

1. Web ブラウザーで `https://CLUSTERNAME.azurehdinsight.net/#/main/services/SPARK2/configs` に移動します。ここで、CLUSTERNAME は Apache Spark クラスターの名前です。

1. **[Custom spark2-defaults]** を展開します。

    :::image type="content" source="./media/apache-hive-warehouse-connector/hive-warehouse-connector-spark2-ambari.png" alt-text="Apache Ambari Spark2 の構成" border="true":::

1. **[プロパティの追加]** を選択して、次の構成を追加します。

    | 構成 | 値 |
    |----|----|
    |`spark.datasource.hive.warehouse.load.staging.dir`|`wasbs://STORAGE_CONTAINER_NAME@STORAGE_ACCOUNT_NAME.blob.core.windows.net/tmp`. <br> HDFS と互換性のある適切なステージング ディレクトリに設定します。 2 つの異なるクラスターがある場合、ステージング ディレクトリは、HiveServer2 がそこにアクセスできるように、LLAP クラスターのストレージ アカウントのステージング ディレクトリにあるフォルダーである必要があります。  `STORAGE_ACCOUNT_NAME` をクラスターによって使用されているストレージ アカウントの名前に置き換え、`STORAGE_CONTAINER_NAME` をストレージ コンテナーの名前に置き換えます。 |
    |`spark.sql.hive.hiveserver2.jdbc.url`| **HiveServer2 Interactive JDBC URL** から先ほど取得した値 |
    |`spark.datasource.hive.warehouse.metastoreUri`| **hive.metastore.uris** から先ほど取得した値。 |
    |`spark.security.credentials.hiveserver2.enabled`|YARN クラスター モードの場合は `true`、YARN クライアント モードの場合は `false`。 |
    |`spark.hadoop.hive.zookeeper.quorum`| **hive.zookeeper.quorum** から先ほど取得した値。 |
    |`spark.hadoop.hive.llap.daemon.service.hosts`| **hive.llap.daemon.service.hosts** から先ほど取得した値。 |

1. 変更を保存し、影響を受けるすべてのコンポーネントを再起動します。

### <a name="configure-hwc-for-enterprise-security-package-esp-clusters"></a>Enterprise セキュリティ パッケージ (ESP) クラスター用の HWC の構成

Enterprise セキュリティ パッケージ (ESP) を使用すると、Active Directory ベースの認証、マルチユーザーのサポート、ロールベースのアクセス制御など、エンタープライズレベルの機能を Azure HDInsight の Apache Hadoop クラスターで利用できます。 ESP に関する詳細については、「[HDInsight で Enterprise セキュリティ パッケージを使用する](../domain-joined/apache-domain-joined-architecture.md)」を参照してください。

前のセクションで説明した構成とは別に、ESP クラスターで HWC を使用するための次の構成を追加します。

1. Spark クラスターの Ambari Web UI で、 **[Spark2]**  >  **[CONFIGS]**  >  **[Custom spark2-defaults]** の順に移動します。

1. 次のプロパティを更新します。

    | 構成 | 値 |
    |----|----|
    | `spark.sql.hive.hiveserver2.jdbc.url.principal`    | `hive/<llap-headnode>@<AAD-Domain>` |
    
    * Web ブラウザーで `https://CLUSTERNAME.azurehdinsight.net/#/main/services/HIVE/summary` に移動します。ここで、CLUSTERNAME は Interactive Query クラスターの名前です。 **[HiveServer2 Interactive]** をクリックします。 スクリーンショットに示したように、LLAP を実行しているヘッド ノードの完全修飾ドメイン名 (FQDN) が表示されます。 `<llap-headnode>` は、この値に置き換えます。

        :::image type="content" source="./media/apache-hive-warehouse-connector/head-node-hive-server-interactive.png" alt-text="Hive Warehouse Connector のヘッド ノード" border="true":::

    * [ssh コマンド](../hdinsight-hadoop-linux-use-ssh-unix.md)を使用して Interactive Query クラスターに接続します。 `/etc/krb5.conf` ファイルで `default_realm` パラメーターを探します。 `<AAD-DOMAIN>` は、その値に置き換えます。値の文字列は大文字にしてください。そうしないと、資格情報が見つからなくなります。

        :::image type="content" source="./media/apache-hive-warehouse-connector/aad-domain.png" alt-text="Hive Warehouse Connector の AAD ドメイン" border="true":::

    * たとえば、`hive/hn0-ng36ll.mjry42ikpruuxgs2qy2kpg4q5e.cx.internal.cloudapp.net@PKRSRVUQVMAE6J85.D2.INTERNAL.CLOUDAPP.NET` です。
    
1. 変更を保存し、必要に応じてコンポーネントを再起動します。

## <a name="hive-warehouse-connector-usage"></a>Hive Warehouse Connector の使用

いくつかの異なる方法の中から選択し、Hive Warehouse Connector を使って自分の対話型クエリ クラスターに接続してクエリを実行できます。 サポートされている方法には次のツールがあります。

* [Spark-shell および PySpark](../spark/apache-spark-shell.md)
* [Spark-submit](#spark-submit)
* [Zeppelin](./apache-hive-warehouse-connector-zeppelin.md)


Spark から HWC に接続するいくつかの例を次に示します。

### <a name="spark-shell"></a>Spark-shell

1. [ssh コマンド](../hdinsight-hadoop-linux-use-ssh-unix.md)を使用して Apache Spark クラスターに接続します。 次のコマンドを編集して CLUSTERNAME をクラスターの名前に置き換えてから、そのコマンドを入力します。

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. SSH セッションから次のコマンドを実行して、`hive-warehouse-connector-assembly` のバージョンを確認します。

    ```bash
    ls /usr/hdp/current/hive_warehouse_connector
    ```

1. 上で特定した `hive-warehouse-connector-assembly` のバージョンを使用して、次のコードを編集します。 次のコマンドを実行して、spark シェルを起動します。

    ```bash
    spark-shell --master yarn \
    --jars /usr/hdp/current/hive_warehouse_connector/hive-warehouse-connector-assembly-<VERSION>.jar \
    --conf spark.security.credentials.hiveserver2.enabled=false
    ```

1. spark シェルの起動後、次のコマンドを使用して Hive Warehouse Connector インスタンスを開始できます。

    ```scala
    import com.hortonworks.hwc.HiveWarehouseSession
    val hive = HiveWarehouseSession.session(spark).build()
    ```

### <a name="spark-submit"></a>Spark-submit

scala/java コードを依存関係と共にアセンブリ jar にビルドしたら、次のコマンドを使用して、Spark アプリケーションを起動します。 `<VERSION>` および `<APP_JAR_PATH>` を実際の値に置き換えます。

* YARN クライアント モード
    
    ```scala
    spark-submit \
    --class myHwcApp \
    --master yarn \
    --deploy-mode client \
    --jars /usr/hdp/current/hive_warehouse_connector/hive-warehouse-connector-assembly-<VERSION>.jar \
    --conf spark.security.credentials.hiveserver2.enabled=false
    /<APP_JAR_PATH>/myHwcAppProject.jar
    ```

* YARN クラスター モード
    ```scala
    spark-submit \
    --class myHwcApp \
    --master yarn \
    --deploy-mode cluster \
    --jars /usr/hdp/current/hive_warehouse_connector/hive-warehouse-connector-assembly-<VERSION>.jar \
    --conf spark.security.credentials.hiveserver2.enabled=true
    /<APP_JAR_PATH>/myHwcAppProject.jar
    ```

Python の場合は、次の構成も追加します。 

```python
--py-files /usr/hdp/current/hive_warehouse_connector/pyspark_hwc-<VERSION>.zip
```
    
## <a name="run-queries-on-enterprise-security-package-esp-clusters"></a>Enterprise セキュリティ パッケージ (ESP) クラスターに対するクエリの実行

spark-shell または spark-submit を起動する前に、`kinit` を使用します。 USERNAME を、クラスターへのアクセス許可を持つドメイン アカウントの名前に置き換えます。その後、以下のコマンドを実行します。

```bash
kinit USERNAME
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

    :::image type="content" source="./media/apache-hive-warehouse-connector/hive-warehouse-connector-table-before-ranger-policy.png" alt-text="Ranger ポリシーを適用する前のデモ テーブル" border="true":::

1. 列の最後の 4 文字だけが表示される列マスク ポリシーを適用します。  
    1. `https://LLAPCLUSTERNAME.azurehdinsight.net/ranger/` で Ranger 管理 UI に移動します。
    1. **[Hive]** の下にある自分のクラスターの Hive サービスをクリックします。
        :::image type="content" source="./media/apache-hive-warehouse-connector/hive-warehouse-connector-ranger-service-manager.png" alt-text="Ranger サービス マネージャー" border="true":::
    1. **[Masking]\(マスク\)** タブをクリックし、 **[Add New Policy]\(新しいポリシーの追加\)** をクリックします

        :::image type="content" source="./media/apache-hive-warehouse-connector/hive-warehouse-connector-ranger-hive-policy-list.png" alt-text="Hive Warehouse Connector の Ranger Hive ポリシーの一覧" border="true":::

    1. 目的のポリシー名を入力します。 次のように選択します。データベース: **default**、Hive テーブル: **demo**、Hive 列: **name**、ユーザー: **rsadmin2**、アクセスの種類: **select**、 **[Select Masking Option]\(マスク オプションの選択\)** メニュー: **Partial mask: show last 4**。 **[追加]** をクリックします。
                :::image type="content" source="./media/apache-hive-warehouse-connector/hive-warehouse-connector-ranger-create-policy.png" alt-text="ポリシーの作成" border="true":::
1. テーブルの内容をもう一度表示します。 Ranger ポリシーの適用後は、列の最後の 4 文字だけを確認できます。

    :::image type="content" source="./media/apache-hive-warehouse-connector/hive-warehouse-connector-table-after-ranger-policy.png" alt-text="Ranger ポリシーを適用した後のデモ テーブル" border="true":::

## <a name="next-steps"></a>次のステップ

* [HWC と Apache Spark の操作](./apache-hive-warehouse-connector-operations.md)
* [HDInsight での対話型クエリの使用](./apache-interactive-query-get-started.md)
* [HWC と Apache Zeppelin の統合](./apache-hive-warehouse-connector-zeppelin.md)
* [Zeppelin、Livy、spark-submit、pyspark を使用した Hive Warehouse Connector の操作の例](https://community.hortonworks.com/articles/223626/integrating-apache-hive-with-apache-spark-hive-war.html)
