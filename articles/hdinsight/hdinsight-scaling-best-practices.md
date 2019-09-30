---
title: クラスター サイズのスケーリング - Azure HDInsight
description: Azure HDInsight でワークロードに合わせて Apache Hadoop クラスターをエラスティックにスケーリングする
author: ashishthaps
ms.author: ashish
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 06/10/2019
ms.openlocfilehash: dad796c8a7a34a782a4f78260ac38bd966eddde9
ms.sourcegitcommit: 1c9858eef5557a864a769c0a386d3c36ffc93ce4
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/18/2019
ms.locfileid: "71105390"
---
# <a name="scale-azure-hdinsight-clusters"></a>Azure HDInsight クラスターのスケーリング

HDInsight では、クラスター内のワーカー ノードの数をスケールアップおよびスケールダウンできるようにすることで、柔軟性が提供されます。 この柔軟性により、クラスターを数時間後または週末に縮小したり、ビジネスの需要のピーク時に拡張したりできます。

定期的なバッチ処理がある場合は、操作の数分前に HDInsight クラスターをスケールアップして、クラスターに十分なメモリと CPU パワーを持たせることができます。  その後、処理が完了し、使用量が再び減少したら、HDInsight クラスターをスケールダウンしてワーカー ノードの数を減らすことができます。

以下に示す方法のいずれかを使用して、クラスターを手動でスケーリングできます。または、[自動スケーリング](hdinsight-autoscale-clusters.md) オプションを使用して、CPU、メモリ、およびその他のメトリックに応じてシステムを自動的にスケールアップ/ダウンします。

> [!NOTE]  
> HDInsight バージョン 3.1.3 以降を使用しているクラスターのみがサポートされます。 クラスターのバージョンがわからない場合、[プロパティ] ページを確認できます。

## <a name="utilities-to-scale-clusters"></a>クラスターをスケーリングするユーティリティ

Microsoft では、クラスターをスケーリングするための次のユーティリティを提供しています。

|ユーティリティ | 説明|
|---|---|
|[PowerShell Az](https://docs.microsoft.com/powershell/azure)|[Set-AzHDInsightClusterSize](https://docs.microsoft.com/powershell/module/az.hdinsight/set-azhdinsightclustersize) -ClusterName \<Cluster Name> -TargetInstanceCount \<NewSize>|
|[PowerShell AzureRM](https://docs.microsoft.com/powershell/azure/azurerm) |[Set-AzureRmHDInsightClusterSize](https://docs.microsoft.com/powershell/module/azurerm.hdinsight/set-azurermhdinsightclustersize) -ClusterName \<Cluster Name> -TargetInstanceCount \<NewSize>|
|[Azure CLI](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest)| [az hdinsight resize](https://docs.microsoft.com/cli/azure/hdinsight?view=azure-cli-latest#az-hdinsight-resize) --resource-group \<Resource group> --name \<Cluster Name> --target-instance-count \<NewSize>|
|[Azure CLI](hdinsight-administer-use-command-line.md)|azure hdinsight cluster resize \<clusterName> \<Target Instance Count> |
|[Azure Portal](https://portal.azure.com)|HDInsight クラスターのウィンドウを開き、左側のメニューの **[クラスター サイズ]** を選択し、[クラスター サイズ] ウィンドウでワーカー ノードの数を入力して、[保存] を選択します。|  

![Azure portal のスケール クラスター オプション](./media/hdinsight-scaling-best-practices/scale-cluster-blade1.png)

これらの方法のいずれかを使用すると、HDInsight クラスターを数分以内にスケールアップまたはスケールダウンできます。

> [!IMPORTANT]  
> * Azure クラシック CLI は非推奨です。クラシック デプロイ モデルでのみ使用してください。 その他すべてのデプロイについては、[Azure CLI](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest) を使用してください。  
> * PowerShell AzureRM モジュールは非推奨です。  可能な限り、[Az モジュール](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-1.4.0)を使用してください。

## <a name="impact-of-scaling-operations"></a>スケーリング操作の影響

実行中の HDInsight クラスターにノードを**追加** (スケールアップ) するときに、保留中または実行中のジョブが影響を受けることはありません。 スケーリング処理の実行中に新しいジョブを安全に送信できます。 スケーリング操作が何らかの理由で失敗した場合、クラスターの機能状態が維持されるように失敗が処理されます。

ノードを**削除** (スケールダウン) すると、保留中または実行中のジョブは、スケーリング操作の完了時に失敗します。 この失敗の原因は、スケーリング処理中にいくつかのサービスが再起動されることにあります。 手動のスケーリング操作中に、クラスターがセーフ モードでスタックするリスクもあります。

データ ノード数を変更した場合の影響は、HDInsight でサポートされているクラスターの種類ごとに異なります。

* Apache Hadoop

    保留中または実行中のジョブに影響を与えることなく、実行中の Hadoop クラスター内の worker ノードの数をシームレスに増加できます。 処理の進行中に新しいジョブを送信することもできます。 スケール設定処理の失敗は正常に処理され、クラスターは常に機能状態になります。

    データ ノードの数を減らして Hadoop クラスターのスケールを小さくした場合、クラスター内の一部のサービスが再起動されます。 この動作により、スケール設定処理の完了時に、実行中および保留中のすべてのジョブが失敗します。 ただし、処理が完了した後にジョブを再送信できます。

* Apache HBase

    実行中の HBase クラスターに対して、ノードの追加または削除をシームレスに実行できます。 地域サーバーは、スケール設定処理の完了の数分以内に自動的に分散されます。 ただし、クラスターのヘッドノードにログインし、コマンド プロンプト ウィンドウから次のコマンドを実行して、地域サーバーを手動で分散することもできます。

    ```bash
    pushd %HBASE_HOME%\bin
    hbase shell
    balancer
    ```

    HBase シェルの使用の詳細については、「[HDInsight で Apache HBase の例を使用する](hbase/apache-hbase-tutorial-get-started-linux.md)」を参照してください。

* Apache Storm

    実行中の Storm クラスターに対して、データ ノードの追加または削除をシームレスに実行できます。 ただし、スケール設定処理が正常に完了した後、トポロジのバランス再調整が必要になります。

    バランス再調整は、次の 2 つの方法で実行できます。

  * Storm Web UI
  * コマンド ライン インターフェイス (CLI) ツール

    詳細については、[Apache Storm に関するドキュメント](https://storm.apache.org/documentation/Understanding-the-parallelism-of-a-Storm-topology.html)を参照してください。

    Storm Web UI は、HDInsight クラスターで使用できます。

    ![HDInsight Storm のスケールのバランス調整](./media/hdinsight-scaling-best-practices/hdinsight-portal-scale-cluster-storm-rebalance.png)

    Storm トポロジのバランスを再調整する CLI コマンドの例を次に示します。

    ```cli
    ## Reconfigure the topology "mytopology" to use 5 worker processes,
    ## the spout "blue-spout" to use 3 executors, and
    ## the bolt "yellow-bolt" to use 10 executors
    $ storm rebalance mytopology -n 5 -e blue-spout=3 -e yellow-bolt=10
    ```

## <a name="how-to-safely-scale-down-a-cluster"></a>クラスターを安全にスケールする方法

### <a name="scale-down-a-cluster-with-running-jobs"></a>ジョブを実行してクラスターをスケールダウンする

スケールダウン操作中に実行中のジョブが失敗するのを回避するため、次の 3 つのことを試すことができます。

1. ジョブが完了するのを待ってから、クラスターをスケールダウンする。
1. ジョブを手動で終了する。
1. スケーリング操作の完了後に、ジョブを再送信する。

保留中または実行中のジョブの一覧を表示するには、次の手順に従って YARN **Resource Manager UI** を使用できます。

1. [Azure portal](https://portal.azure.com/) でご自身のクラスターを選択します。  手順については、「[クラスターの一覧と表示](./hdinsight-administer-use-portal-linux.md#showClusters)」を参照してください。 クラスターは新しいポータル ページで開きます。
2. メイン ビューから、 **[クラスター ダッシュボード]**  >  **[Ambari ホーム]** に移動します。 クラスターの資格情報を入力します。
3. Ambari UI から、左側のメニューにあるサービスの一覧で **[YARN]** を選択します。  
4. [YARN] ページから **[クイック リンク]** を選択し、アクティブなヘッド ノードにポインターを置き、 **[ResourceManager UI]** を選択します。

    ![Apache Ambari クイック リンク ResourceManager UI](./media/hdinsight-scaling-best-practices/resource-manager-ui1.png)

`https://<HDInsightClusterName>.azurehdinsight.net/yarnui/hn/cluster` を使用すると ResourceManager UI に直接アクセスできます。

ジョブの一覧が、現在の状態と共に表示されます。 このスクリーンショットでは、現在実行中のジョブが 1 つあります。

![ResourceManager UI アプリケーション](./media/hdinsight-scaling-best-practices/resourcemanager-ui-applications.png)

その実行中のアプリケーションを手動で中止するには、SSH シェルから次のコマンドを実行します。

```bash
yarn application -kill <application_id>
```

例:

```bash
yarn application -kill "application_1499348398273_0003"
```

### <a name="getting-stuck-in-safe-mode"></a>セーフ モードでスタックする

クラスターをスケールダウンすると、HDInsight は Apache Ambari 管理インターフェイスを使用して、まず不要なワーカー ノードを削除します。これにより、それらの HDFS ブロックが他のオンライン ワーカー ノードにレプリケートされます。 その後、HDInsight によってクラスターが安全にスケールダウンされます。 スケーリング操作中は、HDFS がセーフ モードになり、スケーリングが終了すると元の状態に戻ります。 ただし、場合によっては、レプリケーション中のファイル ブロックが原因で、スケーリング操作中に HDFS がセーフ モードでスタックします。

既定では、HDFS は 3 の `dfs.replication` 設定 (各ファイル ブロックの使用可能なコピーの数を制御する) で構成されます。 ファイル ブロックの各コピーは、クラスターの別々のノードに格納されます。

HDFS で想定されている数のブロックのコピーが使用できないことが検出されると、HDFS はセーフ モードになり、Ambari によってアラートが生成されます。 スケーリング操作中に HDFS がセーフ モードになったが、その後、レプリケーションに必要な数のノードが検出されないためにセーフ モードを終了できない場合は、そのクラスターがセーフ モードでスタックする場合があります。

### <a name="example-errors-when-safe-mode-is-turned-on"></a>セーフ モードがオンになっている場合のエラーの例

```
org.apache.hadoop.hdfs.server.namenode.SafeModeException: Cannot create directory /tmp/hive/hive/819c215c-6d87-4311-97c8-4f0b9d2adcf0. Name node is in safe mode.
```

```
org.apache.http.conn.HttpHostConnectException: Connect to hn0-clustername.servername.internal.cloudapp.net:10001 [hn0-clustername.servername. internal.cloudapp.net/1.1.1.1] failed: Connection refused
```

`/var/log/hadoop/hdfs/` フォルダーから、クラスターがスケーリングされた時刻付近の名前ノード ログを確認すると、セーフ モードに入った時刻がわかります。 ログ ファイルの名前は `Hadoop-hdfs-namenode-hn0-clustername.*` です。

上記のエラーの根本原因は、Hive がクエリの実行中に HDFS 内の一時ファイルを利用することです。 HDFS がセーフ モードになると、Hive は HDFS に書き込めなくなるため、クエリを実行できません。 HDFS 内の一時ファイルは、各ワーカー ノード VM にマウントされたローカル ドライブにあり、最低 3 つのレプリカにあるその他のワーカー ノード間でレプリケートされます。

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
2. 上記で見つかったスクラッチ ディレクトリ `hdfs://mycluster/tmp/hive/` のコンテンツを一覧表示して、ファイルが含まれているかどうかを確認します。

    ```bash
    hadoop fs -ls -R hdfs://mycluster/tmp/hive/hive
    ```

    ファイルが存在する場合の出力例は次のとおりです。

    ```output
    sshuser@hn0-scalin:~$ hadoop fs -ls -R hdfs://mycluster/tmp/hive/hive
    drwx------   - hive hdfs          0 2017-07-06 13:40 hdfs://mycluster/tmp/hive/hive/4f3f4253-e6d0-42ac-88bc-90f0ea03602c
    drwx------   - hive hdfs          0 2017-07-06 13:40 hdfs://mycluster/tmp/hive/hive/4f3f4253-e6d0-42ac-88bc-90f0ea03602c/_tmp_space.db
    -rw-r--r--   3 hive hdfs         27 2017-07-06 13:40 hdfs://mycluster/tmp/hive/hive/4f3f4253-e6d0-42ac-88bc-90f0ea03602c/inuse.info
    -rw-r--r--   3 hive hdfs          0 2017-07-06 13:40 hdfs://mycluster/tmp/hive/hive/4f3f4253-e6d0-42ac-88bc-90f0ea03602c/inuse.lck
    drwx------   - hive hdfs          0 2017-07-06 20:30 hdfs://mycluster/tmp/hive/hive/c108f1c2-453e-400f-ac3e-e3a9b0d22699
    -rw-r--r--   3 hive hdfs         26 2017-07-06 20:30 hdfs://mycluster/tmp/hive/hive/c108f1c2-453e-400f-ac3e-e3a9b0d22699/inuse.info
    ```

3. Hive がこれらのファイルの処理を終了していることがわかっている場合は、これらを削除できます。 YARN ResourceManager UI のページを見て、Hive で実行中のクエリがないことを確認してください。

    HDFS からファイルを削除するコマンドラインの例:

    ```bash
    hadoop fs -rm -r -skipTrash hdfs://mycluster/tmp/hive/
    ```

#### <a name="scale-hdinsight-to-three-or-more-worker-nodes"></a>HDInsight を 3 つ以上のワーカー ノードにスケーリングする

2 つ以下のワーカー ノードにスケールダウンしたときに、クラスターがセーフ モードで頻繁にスタックし、前述の手順が機能しない場合は、3 つ以上のワーカー ノードを保持することで、クラスターがすべてセーフ モードになるのを回避できます。

3 つのワーカー ノードを保持するのは 1 つだけのワーカー ノードにスケールダウンするよりも高コストですが、クラスターがセーフ モードでスタックするのを防ぐことができます。

#### <a name="run-the-command-to-leave-safe-mode"></a>セーフ モードを終了するコマンドを実行する

最後のオプションは、セーフ モードを終了するコマンドを実行することです。 HDFS がセーフ モードになる原因が、レプリケーション中の Hive ファイルだということがわかっている場合は、次のコマンドを実行してセーフ モードを終了することができます。

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

## <a name="next-steps"></a>次の手順

* [Azure HDInsight クラスターを自動的にスケーリングする](hdinsight-autoscale-clusters.md)
* [Azure HDInsight の概要](hadoop/apache-hadoop-introduction.md)
