---
title: Azure HDInsight での Apache Spark クラスターのリソースの管理
description: Azure HDInsight で Spark クラスターのリソースを管理してパフォーマンスを向上させる方法について説明します。
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 01/12/2021
ms.openlocfilehash: 7027e4af9c11db628990d42430c6877340de3534
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/23/2021
ms.locfileid: "104868445"
---
# <a name="manage-resources-for-apache-spark-cluster-on-azure-hdinsight"></a>Azure HDInsight での Apache Spark クラスターのリソースの管理

[Apache Spark](https://spark.apache.org/) クラスターに関連付けられた [Apache Ambari](https://ambari.apache.org/) UI、[Apache Hadoop YARN](https://hadoop.apache.org/docs/current/hadoop-yarn/hadoop-yarn-site/YARN.html) UI、[Spark History Server](./apache-azure-spark-history-server.md) などのインターフェイスにアクセスする方法、および最適なパフォーマンスが得られるようにクラスター構成を調整する方法について説明します。

## <a name="open-the-spark-history-server"></a>Spark History Server を開く

Spark History Server は、完了および実行中の Spark アプリケーションの Web UI です。 Spark の Web UI の拡張機能です。 詳細については、[Spark History Server](./apache-azure-spark-history-server.md) に関する記事を参照してください。

## <a name="open-the-yarn-ui"></a>Yarn UI を開く

Spark クラスターで現在実行されているアプリケーションを監視するには、YARN UI を使用することができます。

1. [Azure Portal](https://portal.azure.com/) で Spark クラスターを開きます。 詳細については、「[クラスターの一覧と表示](../hdinsight-administer-use-portal-linux.md#showClusters)」を参照してください。

2. **クラスター ダッシュボード** 上で **[Yarn]** を選択します。 入力を求められたら、Spark クラスターの管理者資格情報を入力します。

    :::image type="content" source="./media/apache-spark-resource-manager/azure-portal-dashboard-yarn.png" alt-text="Launch YARN UI" border="true":::

   > [!TIP]  
   > Ambari UI から YARN UI を起動してもかまいません。 Ambari UI で **[YARN]**  >  **[Quick Links]\(クイック リンク\)**  >  **[Active]\(アクティブ\)**  >  **[Resource Manager UI]** に移動します。

## <a name="optimize-clusters-for-spark-applications"></a>Spark アプリケーション用にクラスターを最適化する

アプリケーションの要件に応じて Spark を構成するための主要なパラメーターは、`spark.executor.instances`、`spark.executor.cores`、`spark.executor.memory` の 3 つです。 Executor は、Spark アプリケーション用に起動されるプロセスです。 ワーカー ノードで動作し、アプリケーションのタスクを実行する役割を担います。 それぞれのクラスターで使用される Executor の既定の数とサイズは、ワーカー ノードの数とワーカー ノードのサイズに基づいて計算され、 この情報はクラスターのヘッド ノード上の `spark-defaults.conf` に保存されます。

3 つの構成パラメーターは、クラスター レベルで (クラスター上で動作するすべてのアプリケーションに対して) 構成できるほか、個々のアプリケーションに対して指定することもできます。

### <a name="change-the-parameters-using-ambari-ui"></a>Ambari UI を使用したパラメーターの変更

1. Ambari UI で **[Spark2]**  >  **[Configs]\(構成\)**  >  **[Custom spark2-defaults]\(Spark2 既定値のカスタマイズ\)** に移動します。

    :::image type="content" source="./media/apache-spark-resource-manager/ambari-ui-spark2-configs.png " alt-text="Ambari カスタムを使用したパラメーターの設定" border="true":::

1. 一連の既定値は、クラスター上で 4 つの Spark アプリケーションを同時実行することを想定して決められています。 次のスクリーンショットに示すように、これらの値をユーザー インターフェイスから変更できます。

    :::image type="content" source="./media/apache-spark-resource-manager/ambari-ui-spark2-defaults.png " alt-text="Ambari を使用したパラメーターの設定" border="true":::

1. **[Save]\(保存\)** を選択して構成の変更を保存します。 変更に関係したサービスをすべて再開するよう求めるメッセージがページの上部に表示されます。 **[Restart]\(再起動\)** をクリックします。

    :::image type="content" source="./media/apache-spark-resource-manager/apache-ambari-restart-services.png" alt-text="Restart services" border="true":::

### <a name="change-the-parameters-for-an-application-running-in-jupyter-notebook"></a>Jupyter Notebook で実行するアプリケーションのパラメーター変更

Jupyter Notebook で実行しているアプリケーションについては、`%%configure` マジックを使用して構成に変更を加えることができます。 そのような変更は、できればアプリケーションの冒頭で、1 つ目のコード セルを実行する前に記述してください。 これを行うと、Livy セッションの作成時に、確実に構成が適用されます。 アプリケーションの終盤で構成の変更が生じた場合は、 `-f` パラメーターを使用する必要があります。 ただしその場合、アプリケーションのすべての進捗が失われます。

次のスニペットは、Jupyter で実行しているアプリケーションの構成を変更する方法を示しています。

```scala
%%configure
{"executorMemory": "3072M", "executorCores": 4, "numExecutors":10}
```

例の列で示されているように、構成パラメーターは JSON 文字列として渡し、マジックの後の次の行に置く必要があります。

### <a name="change-the-parameters-for-an-application-submitted-using-spark-submit"></a>spark-submit を使用して送信されたアプリケーションのパラメーター変更

次のコマンドは、 `spark-submit`を使用して送信されたバッチ アプリケーションの構成パラメーターを変更する例です。

```scala
spark-submit --class <the application class to execute> --executor-memory 3072M --executor-cores 4 –-num-executors 10 <location of application jar file> <application parameters>
```

### <a name="change-the-parameters-for-an-application-submitted-using-curl"></a>cURL を使用して送信されたアプリケーションのパラメーター変更

次のコマンドは、cURL を使用して送信されたバッチ アプリケーションの構成パラメーターを変更する例です。

```bash
curl -k -v -H 'Content-Type: application/json' -X POST -d '{"file":"<location of application jar file>", "className":"<the application class to execute>", "args":[<application parameters>], "numExecutors":10, "executorMemory":"2G", "executorCores":5' localhost:8998/batches
```

> [!Note]
> JAR ファイルをご自分のクラスター ストレージ アカウントにコピーします。 JAR ファイルをヘッド ノードに直接コピーしないでください。

### <a name="change-these-parameters-on-a-spark-thrift-server"></a>これらのパラメーターを Spark Thrift サーバーで変更する

Spark Thrift サーバーを使用すると、Spark クラスターに JDBC/ODBC でアクセスし、Spark SQL クエリを実行することができます。 Power BI や Tableau などのツールは、ODBC プロトコルを使用して Spark Thrift サーバーとやり取りし、Spark アプリケーションとして Spark SQL クエリを実行します。 Spark クラスターを作成すると、Spark Thrift サーバーの 2 つのインスタンスが起動されます (ヘッド ノードごとに 1 つ)。 YARN UI には、各 Spark Thrift サーバーが Spark アプリケーションとして表示されます。

Spark Thrift サーバーでは、Spark の Dynamic Executor Allocation が使用されるため、`spark.executor.instances` は使用されません。 代わりに、Executor 数の指定に `spark.dynamicAllocation.maxExecutors` と `spark.dynamicAllocation.minExecutors` が使用されます。 Executor のサイズ変更には、構成パラメーターとして `spark.executor.cores` と `spark.executor.memory` が使用されます。 次の手順に示すように、これらのパラメーターは変更できます。

* `spark.dynamicAllocation.maxExecutors`、`spark.dynamicAllocation.minExecutors` の各パラメーターを更新するには、**Advanced spark2-thrift-sparkconf** カテゴリを展開します。

    :::image type="content" source="./media/apache-spark-resource-manager/ambari-ui-advanced-thrift-sparkconf.png " alt-text="Spark Thrift サーバーの構成" border="true":::

* パラメーター `spark.executor.cores` および `spark.executor.memory` を更新するには、**Custom spark2-thrift-sparkconf** カテゴリを展開します。

    :::image type="content" source="./media/apache-spark-resource-manager/ambari-ui-custom-thrift-sparkconf.png " alt-text="Spark Thrift サーバーのパラメーターの構成" border="true":::

### <a name="change-the-driver-memory-of-the-spark-thrift-server"></a>Spark Thrift サーバーのドライバーのメモリを変更する

ヘッド ノードの RAM の合計サイズが 14 GB を超える場合、Spark Thrift サーバーのドライバーのメモリはヘッド ノードの RAM サイズの 25% に構成されます。 ドライバーのメモリ構成は、次のスクリーンショットのように Ambari UI を使用して変更できます。

Ambari UI で **[Spark2]**  >  **[Configs]\(構成\)**  >  **[Advanced spark2.x-env]** に移動します。 次に、**spark_thrift_cmd_opts** の値を指定します。

## <a name="reclaim-spark-cluster-resources"></a>Spark クラスター リソースを解放する

Spark の動的割り当てにより、Thrift サーバーから利用できるリソースは、2 つのアプリケーション マスターのリソースのみです。 これらのリソースの領域を解放するには、クラスター上で実行されている Thrift サーバー サービスを停止する必要があります。

1. Ambari UI の左ペインで **[Spark2]** を選択します。

2. 次のページで、 **[Spark2 Thrift Servers]** を選択します。

    :::image type="content" source="./media/apache-spark-resource-manager/ambari-ui-spark2-thrift-servers.png " alt-text="Thrift サーバーの再起動 1" border="true":::

3. Spark2 Thrift サーバーが実行されている 2 つのヘッド ノードが表示されます。 いずれかのヘッド ノードを選択してください。

    :::image type="content" source="./media/apache-spark-resource-manager/restart-thrift-server-2.png " alt-text="Thrift サーバーの再起動 2" border="true":::

4. そのヘッド ノードで実行されているすべてのサービスが次のページに一覧表示されます。 一覧から、Spark2 Thrift Server の横にあるドロップダウン ボタンを選択し、 **[Stop]\(停止\)** を選択します。

    :::image type="content" source="./media/apache-spark-resource-manager/ambari-ui-spark2-thriftserver-restart.png " alt-text="Thrift サーバーの再起動 3" border="true":::
5. もう一方のヘッド ノードについても同じ手順を繰り返します。

## <a name="restart-the-jupyter-service"></a>Jupyter サービスを再起動する

この記事の最初で示したように、Ambari Web UI を起動します。 左側のナビゲーション ウィンドウで、 **[Jupyter]** 、 **[Service Actions]\(サービス アクション\)** 、 **[Restart All]\(すべて再起動\)** の順に選択します。 これで、すべてのヘッドノードで Jupyter サービスが開始されます。

:::image type="content" source="./media/apache-spark-resource-manager/apache-ambari-restart-jupyter.png " alt-text="Jupyter の再起動" border="true":::

## <a name="monitor-resources"></a>リソースの監視

この記事の最初で示したように、Yarn Web UI を起動します。 画面上部の [Cluster Metrics] テーブルで、 **[Memory Used]** と **[Memory Total]** の列の値を確認します。 2 つの値が近い場合は、次のアプリケーションを開始するためのリソースが十分でない可能性があります。 **[VCores Used]** と **[VCores Total]** の列でも同じことが言えます。 また、メイン ビューに、 **[ACCEPTED]** の状態のまま、 **[RUNNING]** にも **[FAILED]** の状態にも移行していないアプリケーションがある場合も、開始するためのリソースが十分でないことを示している可能性があります。

:::image type="content" source="./media/apache-spark-resource-manager/apache-ambari-resource-limit.png " alt-text="リソースの制限" border="true":::

## <a name="kill-running-applications"></a>実行中のアプリケーションを強制終了する

1. Yarn UI の左側のパネルで **[Running]\(実行中\)** をクリックします。 実行中のアプリケーションの一覧から、強制終了するアプリケーションを決定し、 **[ID]** を選択します。

    :::image type="content" source="./media/apache-spark-resource-manager/apache-ambari-kill-app1.png " alt-text="アプリの強制終了 1" border="true":::

2. 右上隅の **[Kill Application]\(アプリの強制終了\)** をクリックして、 **[OK]** をクリックします。

    :::image type="content" source="./media/apache-spark-resource-manager/apache-ambari-kill-app2.png " alt-text="アプリの強制終了 2" border="true":::

## <a name="see-also"></a>参照

* [HDInsight の Apache Spark クラスターで実行されるジョブの追跡とデバッグ](apache-spark-job-debugging.md)

### <a name="for-data-analysts"></a>データ アナリスト向け

* [Apache Spark と Machine Learning: HDInsight で Spark を使用して、HVAC データを使用して建物の温度を分析する](apache-spark-ipython-notebook-machine-learning.md)
* [Apache Spark と Machine Learning:HDInsight で Spark を使用して食品の検査結果を予測する](apache-spark-machine-learning-mllib-ipython.md)
* [HDInsight 上での Apache Spark を使用した Web サイト ログ分析](apache-spark-custom-library-website-log-analysis.md)
* [HDInsight での Apache Spark を使用した Application Insight テレメトリ データ分析](apache-spark-analyze-application-insight-logs.md)

### <a name="for-apache-spark-developers"></a>Apache Spark 開発者向け

* [Scala を使用してスタンドアロン アプリケーションを作成する](apache-spark-create-standalone-application.md)
* [Apache Livy を使用して Apache Spark クラスターでジョブをリモートから実行する](apache-spark-livy-rest-interface.md)
* [IntelliJ IDEA 用の HDInsight Tools プラグインを使用して Spark Scala アプリケーションを作成し、送信する](apache-spark-intellij-tool-plugin.md)
* [IntelliJ IDEA 用の HDInsight Tools プラグインを使用して Apache Spark アプリケーションをリモートでデバッグする](apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [HDInsight 上の Apache Spark クラスターで Apache Zeppelin Notebook を使用する](apache-spark-zeppelin-notebook.md)
* [HDInsight 用の Apache Spark クラスター内の Jupyter Notebook で使用可能なカーネル](apache-spark-jupyter-notebook-kernels.md)
* [Jupyter Notebook で外部のパッケージを使用する](apache-spark-jupyter-notebook-use-external-packages.md)
* [Jupyter をコンピューターにインストールして HDInsight Spark クラスターに接続する](apache-spark-jupyter-notebook-install-locally.md)
