---
title: クラスター サイズのスケーリング - Azure HDInsight
description: Azure HDInsight でワークロードに合わせて Apache Hadoop クラスターをエラスティックにスケーリングする
ms.author: ashish
ms.service: hdinsight
ms.topic: how-to
ms.custom: seoapr2020
ms.date: 04/29/2020
ms.openlocfilehash: 6e6c692e8fc13d1703df44c99e9969ba4db5f119
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/23/2021
ms.locfileid: "104872100"
---
# <a name="scale-azure-hdinsight-clusters"></a>Azure HDInsight クラスターのスケーリング

HDInsight は柔軟性を備えており、クラスター内のワーカー ノード数をスケールアップおよびスケールダウンするオプションがあります。 この柔軟性により、数時間後や週末にクラスターを縮小することができます。 また、ビジネス需要のピーク時に拡張できます。

定期的なバッチ プロセスの前には、クラスターをスケールアップしてクラスターに十分なリソースを確保するようにします。   プロセスが完了し、使用量が減少したら、HDInsight クラスターをスケールダウンしてワーカー ノードを減らします。

次に示す方法のいずれかを使用して、クラスターを手動でスケーリングできます。 また、[自動スケーリング](hdinsight-autoscale-clusters.md) オプションを使用して、特定のメトリックに応じて自動的にスケールアップおよびスケールダウンすることもできます。

> [!NOTE]  
> HDInsight バージョン 3.1.3 以降を使用しているクラスターのみがサポートされます。 クラスターのバージョンがわからない場合、[プロパティ] ページを確認できます。

## <a name="utilities-to-scale-clusters"></a>クラスターをスケーリングするユーティリティ

Microsoft では、クラスターをスケーリングするための次のユーティリティを提供しています。

|ユーティリティ | 説明|
|---|---|
|[PowerShell Az](/powershell/azure)|[`Set-AzHDInsightClusterSize`](/powershell/module/az.hdinsight/set-azhdinsightclustersize) `-ClusterName CLUSTERNAME -TargetInstanceCount NEWSIZE`|
|[PowerShell AzureRM](/powershell/azure/azurerm) |[`Set-AzureRmHDInsightClusterSize`](/powershell/module/azurerm.hdinsight/set-azurermhdinsightclustersize) `-ClusterName CLUSTERNAME -TargetInstanceCount NEWSIZE`|
|[Azure CLI](/cli/azure/) | [`az hdinsight resize`](/cli/azure/hdinsight#az-hdinsight-resize) `--resource-group RESOURCEGROUP --name CLUSTERNAME --workernode-count NEWSIZE`|
|[Azure クラシック CLI](hdinsight-administer-use-command-line.md)|`azure hdinsight cluster resize CLUSTERNAME NEWSIZE` |
|[Azure Portal](https://portal.azure.com)|HDInsight クラスターのウィンドウを開き、左側のメニューの **[クラスター サイズ]** を選択し、[クラスター サイズ] ウィンドウでワーカー ノードの数を入力して、[保存] を選択します。|  

:::image type="content" source="./media/hdinsight-scaling-best-practices/azure-portal-settings-nodes.png" alt-text="Azure portal のスケール クラスター オプション":::

これらの方法のいずれかを使用すると、HDInsight クラスターを数分以内にスケールアップまたはスケールダウンできます。

> [!IMPORTANT]  
> * Azure クラシック CLI は非推奨です。クラシック デプロイ モデルでのみ使用してください。 その他すべてのデプロイについては、[Azure CLI](/cli/azure/) を使用してください。
> * PowerShell AzureRM モジュールは非推奨です。  可能な限り、[Az モジュール](/powershell/azure/new-azureps-module-az)を使用してください。

## <a name="impact-of-scaling-operations"></a>スケーリング操作の影響

実行中の HDInsight クラスターにノードを **追加** (スケールアップ) しても、ジョブは影響を受けません。 スケーリング処理の実行中に新しいジョブを安全に送信できます。 スケーリング操作が失敗した場合、その失敗によってクラスターは機能状態のままになります。

ノードを **削除** (スケールダウン) すると、保留中または実行中のジョブは、スケーリング操作の完了時に失敗します。 この失敗の原因は、スケーリング処理中にいくつかのサービスが再起動されることにあります。 手動によるスケーリング操作中に、クラスターがセーフ モードで停止することがあります。

データ ノード数を変更した場合の影響は、HDInsight でサポートされているクラスターの種類ごとに異なります。

* Apache Hadoop

    ジョブに影響を与えることなく、実行中の Hadoop クラスター内のワーカー ノード数をシームレスに増やすことができます。 処理の進行中に新しいジョブを送信することもできます。 スケーリング操作の失敗は正常に処理されます。 クラスターは常に機能状態のままになります。

    Hadoop クラスターのデータ ノード数を減らしてスケールダウンすると、一部のサービスは再起動されます。 この動作により、スケール設定処理の完了時に、実行中および保留中のすべてのジョブが失敗します。 ただし、処理が完了した後にジョブを再送信できます。

* Apache HBase

    実行中の HBase クラスターに対して、ノードの追加または削除をシームレスに実行できます。 地域サーバーは、スケール設定処理の完了の数分以内に自動的に分散されます。 ただし、リージョン サーバーの負荷分散を手動で調整することができます。 クラスター ヘッドノードにログインし、次のコマンドを実行します。

    ```bash
    pushd %HBASE_HOME%\bin
    hbase shell
    balancer
    ```

    HBase シェルの使用の詳細については、「[HDInsight で Apache HBase の例を使用する](hbase/apache-hbase-tutorial-get-started-linux.md)」を参照してください。

* Apache Storm

    Storm の実行中にデータ ノードをシームレスに追加または削除できます。 ただし、スケール設定処理が正常に完了した後、トポロジのバランス再調整が必要になります。 これにより、トポロジがクラスター内のノードの新しい数に基づいて[並列処理の設定](https://storm.apache.org/documentation/Understanding-the-parallelism-of-a-Storm-topology.html)を再調整できます。 実行中のトポロジを再調整するには、次のオプションのいずれかを使用します。

  * Storm Web UI

    次の手順により、Storm UI を使用してトポロジを再調整します。

    1. Web ブラウザーで `https://CLUSTERNAME.azurehdinsight.net/stormui` を開きます。ここで、`CLUSTERNAME` は実行中の Storm クラスターの名前です。 メッセージが表示されたら、HDInsight クラスター管理者 (admin) の名前と、クラスターの作成時に指定したパスワードを入力します。

    1. 再調整するトポロジを選択し、 **[Rebalance]** \(再調整) ボタンをクリックします。 再調整の操作が実行されるまでの待ち時間を入力します。

        :::image type="content" source="./media/hdinsight-scaling-best-practices/hdinsight-portal-scale-cluster-storm-rebalance.png" alt-text="HDInsight Storm のスケールのバランス調整":::

  * コマンド ライン インターフェイス (CLI) ツール

    サーバーに接続し、次のコマンドを使用してトポロジを再調整します。

    ```bash
     storm rebalance TOPOLOGYNAME
    ```

    また、トポロジによって最初に提供された並列処理のヒントをオーバーライドするパラメーターも指定できます。 たとえば、以下のコードは、`mytopology` トポロジを再構成して、5 個の worker プロセス、blue-spout コンポーネント用の 3 個の実行プログラム、yellow-bolt コンポーネント用の 10 個の実行プログラムにします。

    ```bash
    ## Reconfigure the topology "mytopology" to use 5 worker processes,
    ## the spout "blue-spout" to use 3 executors, and
    ## the bolt "yellow-bolt" to use 10 executors
    $ storm rebalance mytopology -n 5 -e blue-spout=3 -e yellow-bolt=10
    ```

* Kafka

    スケーリング操作の後で、パーティションのレプリカを再調整する必要があります。 詳しくは、「[HDInsight 上の Apache Kafka によるデータの高可用性](./kafka/apache-kafka-high-availability.md)」をご覧ください。

* Apache Hive LLAP

    `N` 個のワーカー ノードにスケーリングした後、HDInsight は自動的に次の構成を設定し、Hive を再起動します。

  * 同時実行クエリの最大合計数: `hive.server2.tez.sessions.per.default.queue = min(N, 32)`
  * Hive の LLAP で使用されるノードの数: `num_llap_nodes  = N`
  * Hive LLAP デーモンを実行するためのノードの数: `num_llap_nodes_for_llap_daemons = N`

## <a name="how-to-safely-scale-down-a-cluster"></a>クラスターを安全にスケールする方法

### <a name="scale-down-a-cluster-with-running-jobs"></a>ジョブを実行してクラスターをスケールダウンする

スケールダウン操作中に実行中のジョブが失敗するのを回避するため、次の 3 つのことを試すことができます。

1. ジョブが完了するのを待ってから、クラスターをスケールダウンする。
1. ジョブを手動で終了する。
1. スケーリング操作の完了後に、ジョブを再送信する。

保留中または実行中のジョブの一覧を表示するには、次の手順に従って YARN **Resource Manager UI** を使用できます。

1. [Azure portal](https://portal.azure.com/) でご自身のクラスターを選択します。  このクラスターは、新しいポータル ページで開かれます。
2. メイン ビューから、 **[クラスター ダッシュボード]**  >  **[Ambari ホーム]** に移動します。 クラスターの資格情報を入力します。
3. Ambari UI から、左側のメニューにあるサービスの一覧で **[YARN]** を選択します。  
4. [YARN] ページから **[クイック リンク]** を選択し、アクティブなヘッド ノードにポインターを置き、 **[Resource Manager UI]** を選択します。

    :::image type="content" source="./media/hdinsight-scaling-best-practices/resource-manager-ui1.png" alt-text="Apache Ambari クイック リンク Resource Manager UI":::

`https://<HDInsightClusterName>.azurehdinsight.net/yarnui/hn/cluster` を使用すると Resource Manager UI に直接アクセスできます。

ジョブの一覧が、現在の状態と共に表示されます。 このスクリーンショットでは、現在実行中のジョブが 1 つあります。

:::image type="content" source="./media/hdinsight-scaling-best-practices/resourcemanager-ui-applications.png" alt-text="Resource Manager UI アプリケーション":::

その実行中のアプリケーションを手動で中止するには、SSH シェルから次のコマンドを実行します。

```bash
yarn application -kill <application_id>
```

次に例を示します。

```bash
yarn application -kill "application_1499348398273_0003"
```

### <a name="getting-stuck-in-safe-mode"></a>セーフ モードでスタックする

クラスターをスケールダウンすると、まず余分なワーカー ノードの使用を停止するために、HDInsight では Apache Ambari 管理インターフェイスが使用されます。 ノードによって、その HDFS ブロックが他のオンライン ワーカー ノードにレプリケートされます。 その後、HDInsight によってクラスターが安全にスケールダウンされます。 スケーリング操作時に HDFS はセーフ モードに切り替わります。 スケーリングが完了すると HDFS は復帰するはずです。 ただし、場合によっては、レプリケーション中のファイル ブロックが原因で、スケーリング操作中に HDFS がセーフ モードでスタックします。

既定では、HDFS は 1 の `dfs.replication` 設定 (各ファイル ブロックの使用可能なコピーの数を制御する) で構成されます。 ファイル ブロックの各コピーは、クラスターの別々のノードに格納されます。

想定されている数のブロックのコピーが使用できない場合、HDFS はセーフ モードになり、Ambari によってアラートが生成されます。 HDFS は、スケーリング操作のためにセーフ モードに切り替わる場合があります。 必要な数のノードがレプリケーション用に検出されない場合、クラスターはセーフ モードで停止する可能性があります。

### <a name="example-errors-when-safe-mode-is-turned-on"></a>セーフ モードがオンになっている場合のエラーの例

```output
org.apache.hadoop.hdfs.server.namenode.SafeModeException: Cannot create directory /tmp/hive/hive/819c215c-6d87-4311-97c8-4f0b9d2adcf0. Name node is in safe mode.
```

```output
org.apache.http.conn.HttpHostConnectException: Connect to active-headnode-name.servername.internal.cloudapp.net:10001 [active-headnode-name.servername. internal.cloudapp.net/1.1.1.1] failed: Connection refused
```

`/var/log/hadoop/hdfs/` フォルダーから、クラスターがスケーリングされた時刻付近の名前ノード ログを確認すると、セーフ モードに入った時刻がわかります。 ログ ファイルの名前は `Hadoop-hdfs-namenode-<active-headnode-name>.*` です。

根本的な原因は、クエリの実行中に Hive が HDFS の一時ファイルに依存していることでした。 HDFS がセーフ モードになると、Hive は HDFS に書き込めなくなるため、クエリを実行できません。 HDFS 内の一時ファイルは、個々のワーカー ノード VM にマウントされているローカル ドライブに配置されます。 これらのファイルは、他のワーカー ノードの少なくとも 3 つのレプリカにレプリケートされます。

### <a name="how-to-prevent-hdinsight-from-getting-stuck-in-safe-mode"></a>HDInsight がセーフ モードでスタックするのを防ぐ方法

HDInsight がセーフ モードのままにならないようにする方法はいくつかあります。

* HDInsight をスケールダウンする前にすべての Hive ジョブを停止します。 または、実行中の Hive ジョブとの競合を回避するようにスケールダウン処理をスケジュールします。
* スケールダウンする前に、HDFS 内にある Hive のスクラッチ `tmp` ディレクトリ ファイルを手動でクリーンアップします。
* HDInsight を最低 3 つのワーカー ノードにスケールダウンするだけです。 ワーカー ノードが 1 つにならないようにします。
* 必要に応じて、セーフ モードを終了するコマンドを実行します。

以下のセクションでは、これらのオプションについて説明します。

#### <a name="stop-all-hive-jobs"></a>すべての Hive ジョブを停止する

すべての Hive ジョブを停止してから、1 つのワーカー ノードにスケールダウンします。 ワークロードがスケジュールされている場合は、Hive の処理が完了した後でスケールダウンします。

スケーリングの前に Hive ジョブを停止することで、tmp フォルダー内のスクラッチ ファイルの数を最小限に抑えることができます (存在する場合)。

#### <a name="manually-clean-up-hives-scratch-files"></a>Hive のスクラッチ ファイルを手動でクリーンアップする

Hive で一時ファイルが残っている場合は、これらのファイルを手動でクリーンアップしてからスケールダウンすることで、セーフ モードを回避できます。

1. `hive.exec.scratchdir` 構成プロパティを見て、Hive の一時ファイルに使用されている場所を確認します。 このパラメーターは `/etc/hive/conf/hive-site.xml` 内で設定されます。

    ```xml
    <property>
        <name>hive.exec.scratchdir</name>
        <value>hdfs://mycluster/tmp/hive</value>
    </property>
    ```

1. Hive サービスを停止し、すべてのクエリとジョブが完了していることを確認します。

1. 上記で見つかったスクラッチ ディレクトリ `hdfs://mycluster/tmp/hive/` のコンテンツを一覧表示して、ファイルが含まれているかどうかを確認します。

    ```bash
    hadoop fs -ls -R hdfs://mycluster/tmp/hive/hive
    ```

    ファイルが存在する場合の出力例は次のとおりです。

    ```output
    sshuser@scalin:~$ hadoop fs -ls -R hdfs://mycluster/tmp/hive/hive
    drwx------   - hive hdfs          0 2017-07-06 13:40 hdfs://mycluster/tmp/hive/hive/4f3f4253-e6d0-42ac-88bc-90f0ea03602c
    drwx------   - hive hdfs          0 2017-07-06 13:40 hdfs://mycluster/tmp/hive/hive/4f3f4253-e6d0-42ac-88bc-90f0ea03602c/_tmp_space.db
    -rw-r--r--   3 hive hdfs         27 2017-07-06 13:40 hdfs://mycluster/tmp/hive/hive/4f3f4253-e6d0-42ac-88bc-90f0ea03602c/inuse.info
    -rw-r--r--   3 hive hdfs          0 2017-07-06 13:40 hdfs://mycluster/tmp/hive/hive/4f3f4253-e6d0-42ac-88bc-90f0ea03602c/inuse.lck
    drwx------   - hive hdfs          0 2017-07-06 20:30 hdfs://mycluster/tmp/hive/hive/c108f1c2-453e-400f-ac3e-e3a9b0d22699
    -rw-r--r--   3 hive hdfs         26 2017-07-06 20:30 hdfs://mycluster/tmp/hive/hive/c108f1c2-453e-400f-ac3e-e3a9b0d22699/inuse.info
    ```

1. Hive がこれらのファイルの処理を終了していることがわかっている場合は、これらを削除できます。 Yarn Resource Manager UI のページを見て、Hive で実行中のクエリがないことを確認してください。

    HDFS からファイルを削除するコマンドラインの例:

    ```bash
    hadoop fs -rm -r -skipTrash hdfs://mycluster/tmp/hive/
    ```

#### <a name="scale-hdinsight-to-three-or-more-worker-nodes"></a>HDInsight を 3 つ以上のワーカー ノードにスケーリングする

ワーカー ノードを 3 つ未満にスケールダウンすると、クラスターがセーフ モードで頻繁に停止する場合は、3 つ以上のワーカー ノードを保持します。

3 つのワーカー ノードを持つことは、1 つのワーカー ノードのみにスケールダウンするよりもコストがかかります。 ただし、この操作により、クラスターがセーフ モードで停止するのを防ぐことができます。

### <a name="scale-hdinsight-down-to-one-worker-node"></a>HDInsight を 1 つのワーカー ノードにスケール ダウンする

クラスターのノード数を 1 ノードにスケール ダウンしても、ワーカー ノード 0 は引き続き存続します。 ワーカー ノード 0 を使用停止にすることはできません。

#### <a name="run-the-command-to-leave-safe-mode"></a>セーフ モードを終了するコマンドを実行する

最後のオプションは、セーフ モードを終了するコマンドを実行することです。 レプリケーション対象の Hive ファイルがあるために HDFS がセーフ モードに切り替わった場合は、次のコマンドを実行してセーフ モードを終了します。

```bash
hdfs dfsadmin -D 'fs.default.name=hdfs://mycluster/' -safemode leave
```

### <a name="scale-down-an-apache-hbase-cluster"></a>Apache HBase クラスターをスケールダウンする

リージョン サーバーは、スケーリング操作の完了から数分以内に自動的に調整されます。 リージョン サーバーを手動で調整するには、次の手順を完了します。

1. SSH を使用して HDInsight クラスターに接続します。 詳細については、[HDInsight での SSH の使用](hdinsight-hadoop-linux-use-ssh-unix.md)に関するページを参照してください。

2. HBase シェルを起動します。

    ```bash
    hbase shell
    ```

3. 次のコマンドを使用して、リージョン サーバーを手動で調整します。

    ```bash
    balancer
    ```

## <a name="next-steps"></a>次のステップ

* [Azure HDInsight クラスターを自動的にスケーリングする](hdinsight-autoscale-clusters.md)

HDInsight クラスターのスケーリングに関する具体的な情報については、以下を参照してください。

* [Azure portal を使用して HDInsight の Apache Hadoop クラスターを管理する](hdinsight-administer-use-portal-linux.md#scale-clusters)
* [Azure CLI を使用して HDInsight の Apache Hadoop クラスターを管理する](hdinsight-administer-use-command-line.md#scale-clusters)