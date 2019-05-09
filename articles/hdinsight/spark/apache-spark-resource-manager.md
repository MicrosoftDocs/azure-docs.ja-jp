---
title: Azure HDInsight での Apache Spark クラスターのリソースの管理
description: Azure HDInsight で Spark クラスターのリソースを管理してパフォーマンスを向上させる方法について説明します。
services: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 01/23/2018
ms.author: hrasheed
ms.openlocfilehash: 023fd8267a557fa57e98a6a57785fb9ebfcb12ab
ms.sourcegitcommit: 1c2cf60ff7da5e1e01952ed18ea9a85ba333774c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/12/2019
ms.locfileid: "59523972"
---
# <a name="manage-resources-for-apache-spark-cluster-on-azure-hdinsight"></a>Azure HDInsight での Apache Spark クラスターのリソースの管理 

[Apache Spark](https://spark.apache.org/) クラスターに関連付けられた [Apache Ambari](https://ambari.apache.org/) UI、[Apache Hadoop YARN](https://hadoop.apache.org/docs/current/hadoop-yarn/hadoop-yarn-site/YARN.html) UI、Spark History Server などのインターフェイスにアクセスする方法、および最適なパフォーマンスが得られるようにクラスター構成を調整する方法について説明します。

**前提条件:**

* HDInsight での Apache Spark クラスター。 手順については、「 [Create Apache Spark clusters in Azure HDInsight (Azure HDInsight での Apache Spark クラスターの作成)](apache-spark-jupyter-spark-sql.md)」を参照してください。

## <a name="open-the-ambari-web-ui"></a>Ambari Web UI を開く

Apache Ambari を使用して、クラスターを監視し、構成を変更します。 詳細については、[Azure Portal を使用した HDInsight での Apache Hadoop クラスターの管理](../hdinsight-administer-use-portal-linux.md#open-the-apache-ambari-web-ui)に関するページを参照してください。

## <a name="open-the-spark-history-server"></a>Spark History Server を開く

Spark History Server は、完了および実行中の Spark アプリケーションの Web UI です。 Spark の Web UI の拡張機能です。

**Spark History Server Web UI を開くには**

1. [Azure Portal](https://portal.azure.com/) で Spark クラスターを開きます。 詳細については、「[クラスターの一覧と表示](../hdinsight-administer-use-portal-linux.md#showClusters)」を参照してください。
2. **クイック リンク**で **[クラスター ダッシュボード]** をクリックし、**[Spark History Server]** をクリックします。

    ![Spark History Server](./media/apache-spark-resource-manager/launch-history-server.png "Spark History Server")

    入力を求められたら、Spark クラスターの管理者資格情報を入力します。 次の URL を参照して Spark History Server を開くこともできます。

    ```
    https://<ClusterName>.azurehdinsight.net/sparkhistory
    ```

    `<ClusterName>` を Spark クラスター名で置き換えます。

Spark History Server Web UI は次のようになります。

![HDInsight Spark History Server](./media/apache-spark-resource-manager/hdinsight-spark-history-server.png)

## <a name="open-the-yarn-ui"></a>Yarn UI を開く
Spark クラスターで現在実行されているアプリケーションを監視するには、YARN UI を使用することができます。

1. [Azure Portal](https://portal.azure.com/) で Spark クラスターを開きます。 詳細については、「[クラスターの一覧と表示](../hdinsight-administer-use-portal-linux.md#showClusters)」を参照してください。
2. **クイック リンク**で **[クラスター ダッシュボード]** をクリックし、**[YARN]** をクリックします。

    ![Launch YARN UI](./media/apache-spark-resource-manager/launch-yarn-ui.png)

   > [!TIP]  
   > Ambari UI から YARN UI を起動してもかまいません。 Ambari UI を起動するには、**[クラスター ダッシュボード]** をクリックし、**[HDInsight クラスター ダッシュボード]** をクリックします。 Ambari UI から **[YARN]**、**[クイック リンク]** の順にクリックし、アクティブな Resource Manager をクリックして、**[Resource Manager UI]** をクリックします。

## <a name="optimize-clusters-for-spark-applications"></a>Spark アプリケーション用にクラスターを最適化する
アプリケーションの要件に応じて Spark を構成するための主要なパラメーターは、`spark.executor.instances`、`spark.executor.cores`、`spark.executor.memory` の 3 つです。 Executor は、Spark アプリケーション用に起動されるプロセスです。 ワーカー ノードで動作し、アプリケーションのタスクを実行する役割を担います。 それぞれのクラスターで使用される Executor の既定の数とサイズは、ワーカー ノードの数とワーカー ノードのサイズに基づいて計算され、 この情報はクラスターのヘッド ノード上の `spark-defaults.conf` に保存されます。

3 つの構成パラメーターは、クラスター レベルで (クラスター上で動作するすべてのアプリケーションに対して) 構成できるほか、個々のアプリケーションに対して指定することもできます。

### <a name="change-the-parameters-using-ambari-ui"></a>Ambari UI を使用したパラメーターの変更
1. Ambari UI から **[Spark]**、**[Configs (構成)]** の順にクリックし、**[Custom spark-defaults]** を展開します。

    ![Set parameters using Ambari](./media/apache-spark-resource-manager/set-parameters-using-ambari.png)
2. 一連の既定値は、クラスター上で 4 つの Spark アプリケーションを同時実行することを想定して決められています。 次のスクリーンショットに示すように、これらの値をユーザー インターフェイスから変更できます。

    ![Set parameters using Ambari](./media/apache-spark-resource-manager/set-executor-parameters.png)
3. **[保存]** をクリックして構成の変更を保存します。 変更に関係したサービスをすべて再開するよう求めるメッセージがページの上部に表示されます。 **[Restart (再開)]** をクリックします。

    ![Restart services](./media/apache-spark-resource-manager/restart-services.png)

### <a name="change-the-parameters-for-an-application-running-in-jupyter-notebook"></a>Jupyter Notebook で実行するアプリケーションのパラメーター変更
Jupyter Notebook で実行しているアプリケーションについては、 `%%configure` マジックを使用して構成に変更を加えることができます。 そのような変更は、できればアプリケーションの冒頭で、1 つ目のコード セルを実行する前に記述してください。 これを行うと、Livy セッションの作成時に、確実に構成が適用されます。 アプリケーションの終盤で構成の変更が生じた場合は、 `-f` パラメーターを使用する必要があります。 ただしその場合、アプリケーションのすべての進捗が失われます。

次のスニペットは、Jupyter で実行しているアプリケーションの構成を変更する方法を示しています。

    %%configure
    {"executorMemory": "3072M", "executorCores": 4, "numExecutors":10}

例の列で示されているように、構成パラメーターは JSON 文字列として渡し、マジックの後の次の行に置く必要があります。

### <a name="change-the-parameters-for-an-application-submitted-using-spark-submit"></a>spark-submit を使用して送信されたアプリケーションのパラメーター変更
次のコマンドは、 `spark-submit`を使用して送信されたバッチ アプリケーションの構成パラメーターを変更する例です。

    spark-submit --class <the application class to execute> --executor-memory 3072M --executor-cores 4 –-num-executors 10 <location of application jar file> <application parameters>

### <a name="change-the-parameters-for-an-application-submitted-using-curl"></a>cURL を使用して送信されたアプリケーションのパラメーター変更
次のコマンドは、cURL を使用して送信されたバッチ アプリケーションの構成パラメーターを変更する例です。

    curl -k -v -H 'Content-Type: application/json' -X POST -d '{"file":"<location of application jar file>", "className":"<the application class to execute>", "args":[<application parameters>], "numExecutors":10, "executorMemory":"2G", "executorCores":5' localhost:8998/batches

### <a name="change-these-parameters-on-a-spark-thrift-server"></a>これらのパラメーターを Spark Thrift サーバーで変更する
Spark Thrift サーバーを使用すると、Spark クラスターに JDBC/ODBC でアクセスし、Spark SQL クエリを実行することができます。 Power BI や Tableau などのツールは、 ODBC プロトコルを使用して Spark Thrift サーバーとやり取りし、Spark アプリケーションとして Spark SQL クエリを実行します。 Spark クラスターを作成すると、Spark Thrift サーバーの 2 つのインスタンスが起動されます (ヘッド ノードごとに 1 つ)。 YARN UI には、各 Spark Thrift サーバーが Spark アプリケーションとして表示されます。

Spark Thrift サーバーでは、Spark の Dynamic Executor Allocation が使用されるため、 `spark.executor.instances` は使用されません。 代わりに、Executor 数の指定に `spark.dynamicAllocation.minExecutors` と `spark.dynamicAllocation.maxExecutors` が使用されます。 Executor のサイズ変更には、構成パラメーターとして `spark.executor.cores` と `spark.executor.memory` が使用されます。 次の手順に示すように、これらのパラメーターは変更できます。

* `spark.dynamicAllocation.minExecutors`、`spark.dynamicAllocation.maxExecutors`、`spark.executor.memory` の各パラメーターを更新するには、**Advanced spark-thrift-sparkconf** カテゴリを展開します。

    ![Configure Spark thrift server](./media/apache-spark-resource-manager/spark-thrift-server-1.png)    
* `spark.executor.cores` パラメーターを更新するには、**Custom spark-thrift-sparkconf** カテゴリを展開します。

    ![Configure Spark thrift server](./media/apache-spark-resource-manager/spark-thrift-server-2.png)

### <a name="change-the-driver-memory-of-the-spark-thrift-server"></a>Spark Thrift サーバーのドライバーのメモリを変更する
ヘッド ノードの RAM の合計サイズが 14 GB を超える場合、Spark Thrift サーバーのドライバーのメモリはヘッド ノードの RAM サイズの 25% に構成されます。 ドライバーのメモリ構成は、次のスクリーンショットのように Ambari UI を使用して変更できます。

* Ambari UI から **[Spark]**、**[Configs (構成)]** の順にクリックし、**[Advanced spark-env]** を展開して、**[spark_thrift_cmd_opts]** の値を指定します。

    ![Configure Spark thrift server RAM](./media/apache-spark-resource-manager/spark-thrift-server-ram.png)

## <a name="reclaim-spark-cluster-resources"></a>Spark クラスター リソースを解放する
Spark の動的割り当てにより、Thrift サーバーから利用できるリソースは、2 つのアプリケーション マスターのリソースのみです。 これらのリソースの領域を解放するには、クラスター上で実行されている Thrift サーバー サービスを停止する必要があります。

1. Ambari UI の左ペインで **[Spark]** をクリックします。
2. 次のページで、 **[Spark Thrift Servers]** をクリックします。

    ![Restart thrift server](./media/apache-spark-resource-manager/restart-thrift-server-1.png)
3. Spark Thrift サーバーが実行されている 2 つのヘッド ノードが表示されます。 いずれかのヘッド ノードをクリックしてください。

    ![Restart thrift server](./media/apache-spark-resource-manager/restart-thrift-server-2.png)
4. そのヘッド ノードで実行されているすべてのサービスが次のページに一覧表示されます。 一覧内の Spark Thrift サーバーの横にあるドロップダウン ボタンをクリックし、 **[Stop (停止)]** をクリックします。

    ![Restart thrift server](./media/apache-spark-resource-manager/restart-thrift-server-3.png)
5. もう一方のヘッド ノードについても同じ手順を繰り返します。

## <a name="restart-the-jupyter-service"></a>Jupyter サービスを再起動する
この記事の最初で示したように、Ambari Web UI を起動します。 左側のナビゲーション ウィンドウで、**[Jupyter]**、**[Service Actions]**、**[Restart All]** の順にクリックします。 これで、すべてのヘッドノードで Jupyter サービスが開始されます。

![Jupyter の再起動](./media/apache-spark-resource-manager/restart-jupyter.png "Jupyter の再起動")

## <a name="monitor-resources"></a>リソースの監視
この記事の最初で示したように、Yarn Web UI を起動します。 画面上部の [Cluster Metrics] テーブルで、**[Memory Used]** と **[Memory Total]** の列の値を確認します。 2 つの値が近い場合は、次のアプリケーションを開始するためのリソースが十分でない可能性があります。 **[VCores Used]** と **[VCores Total]** の列でも同じことが言えます。 また、メイン ビューに、**[ACCEPTED]** の状態のまま、**[RUNNING]** にも **[FAILED]** の状態にも移行していないアプリケーションがある場合も、開始するためのリソースが十分でないことを示している可能性があります。

![リソースの制限](./media/apache-spark-resource-manager/resource-limit.png "リソースの制限")

## <a name="kill-running-applications"></a>実行中のアプリケーションを強制終了する
1. Yarn UI の左側のパネルで **[Running]** をクリックします。 実行中のアプリケーションの一覧から、強制終了するアプリケーションを決定し、**[ID]** をクリックします。

    ![App1 の強制終了](./media/apache-spark-resource-manager/kill-app1.png "App1 の強制終了")

2. 右上隅の **[Kill Application]** をクリックして、**[OK]** をクリックします。

    ![App2 の強制終了](./media/apache-spark-resource-manager/kill-app2.png "App2 の強制終了")

## <a name="see-also"></a>関連項目
* [HDInsight の Apache Spark クラスターで実行されるジョブの追跡とデバッグ](apache-spark-job-debugging.md)

### <a name="for-data-analysts"></a>データ アナリスト向け

* [Apache Spark と Machine Learning:HDInsight で Spark を使用して、HVAC データを使用して建物の温度を分析する](apache-spark-ipython-notebook-machine-learning.md)
* [Apache Spark と Machine Learning:HDInsight で Spark を使用して食品の検査結果を予測する](apache-spark-machine-learning-mllib-ipython.md)
* [HDInsight 上での Apache Spark を使用した Web サイト ログ分析](apache-spark-custom-library-website-log-analysis.md)
* [HDInsight での Apache Spark を使用した Application Insight テレメトリ データ分析](apache-spark-analyze-application-insight-logs.md)
* [分散型深層学習用に Azure HDInsight Spark で Caffe を使用する](apache-spark-deep-learning-caffe.md)

### <a name="for-apache-spark-developers"></a>Apache Spark 開発者向け

* [Scala を使用してスタンドアロン アプリケーションを作成する](apache-spark-create-standalone-application.md)
* [Apache Livy を使用して Apache Spark クラスターでジョブをリモートから実行する](apache-spark-livy-rest-interface.md)
* [IntelliJ IDEA 用の HDInsight Tools プラグインを使用して Spark Scala アプリケーションを作成し、送信する](apache-spark-intellij-tool-plugin.md)
* [IntelliJ IDEA 用の HDInsight Tools プラグインを使用して Apache Spark アプリケーションをリモートでデバッグする](apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [HDInsight 上の Apache Spark クラスターで Apache Zeppelin Notebook を使用する](apache-spark-zeppelin-notebook.md)
* [HDInsight 用の Apache Spark クラスター内の Jupyter Notebook で使用可能なカーネル](apache-spark-jupyter-notebook-kernels.md)
* [Jupyter Notebook で外部のパッケージを使用する](apache-spark-jupyter-notebook-use-external-packages.md)
* [Jupyter をコンピューターにインストールして HDInsight Spark クラスターに接続する](apache-spark-jupyter-notebook-install-locally.md)
