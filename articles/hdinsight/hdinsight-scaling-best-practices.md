---
title: クラスター サイズのスケーリング - Azure HDInsight
description: ワークロードに合わせて HDInsight クラスターをスケーリングします。
services: hdinsight
author: ashishthaps
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 02/26/2019
ms.author: ashish
ms.openlocfilehash: cae0c97cb3084b0578f277852d646c199d1e2313
ms.sourcegitcommit: 90dcc3d427af1264d6ac2b9bde6cdad364ceefcc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/21/2019
ms.locfileid: "58316254"
---
# <a name="scale-hdinsight-clusters"></a>HDInsight クラスターのスケーリング

HDInsight では、クラスター内のワーカー ノードの数をスケールアップおよびスケールダウンできるようにすることで、柔軟性が提供されます。 これにより、クラスターを数時間後または週末に縮小したり、ビジネスの需要のピーク時に拡張したりできます。

たとえば、1 日 1 回または月 1 回に発生するバッチ処理がある場合、そのスケジュールされたイベントの数分前に HDInsight クラスターをスケールアップして、十分な量のメモリと CPU のコンピューティング能力を確保できます。  その後、処理が完了し、使用量が再び減少したら、HDInsight クラスターをスケールダウンしてワーカー ノードの数を減らすことができます。

## <a name="utilities-to-scale-clusters"></a>クラスターをスケーリングするユーティリティ

Microsoft では、クラスターをスケーリングするための次のユーティリティを提供しています。

|ユーティリティ | 説明|
|---|---|
|[PowerShell Az](https://docs.microsoft.com/powershell/azure/new-azureps-module-az)|[Set-AzHDInsightClusterSize](https://docs.microsoft.com/powershell/module/az.hdinsight/set-azhdinsightclustersize) -ClusterName \<Cluster Name> -TargetInstanceCount \<NewSize>|
|[PowerShell AzureRM](https://docs.microsoft.com/powershell/azure/azurerm/overview) |[Set-AzureRmHDInsightClusterSize](https://docs.microsoft.com/powershell/module/azurerm.hdinsight/set-azurermhdinsightclustersize) -ClusterName \<Cluster Name> -TargetInstanceCount \<NewSize>|
|[Azure CLI](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest)|[az hdinsight resize](https://docs.microsoft.com/cli/azure/hdinsight?view=azure-cli-latest#az-hdinsight-resize) --resource-group \<Resource group> --name \<Cluster Name> --target-instance-count \<NewSize>|
|[Azure クラシック CLI](hdinsight-administer-use-command-line.md)|azure hdinsight cluster resize \<clusterName> \<Target Instance Count>|
|[Azure Portal](https://portal.azure.com)|HDInsight クラスターのウィンドウを開き、左側のメニューの **[クラスター サイズ]** を選択し、[クラスター サイズ] ウィンドウでワーカー ノードの数を入力して、[保存] を選択します。|  

![クラスターのスケーリング](./media/hdinsight-scaling-best-practices/scale-cluster-blade.png)

これらの方法のいずれかを使用すると、HDInsight クラスターを数分以内にスケールアップまたはスケールダウンできます。

> [!IMPORTANT]  
> * Azure クラシック CLI は非推奨です。クラシック デプロイ モデルでのみ使用してください。 その他すべてのデプロイについては、[Azure CLI](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest) を使用してください。  
> * PowerShell AzureRM モジュールは非推奨です。  可能な限り、[Az モジュール](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-1.4.0)を使用してください。

## <a name="scaling-impacts-on-running-jobs"></a>実行中のジョブに対するスケーリングの影響

実行中の HDInsight クラスターにノードを**追加**する場合、保留中または実行中のジョブに影響はありません。 さらに、新しいジョブも、スケーリング処理の実行中に安全に送信できます。 スケーリング操作が何らかの理由で失敗した場合、その失敗は適切に処理され、クラスターの機能状態は維持されます。

ただし、ノードを**削除**してクラスターをスケールダウンしている場合、保留中または実行中のジョブは、スケーリング操作の完了時に失敗します。 この失敗の原因は、処理中にいくつかのサービスが再起動されることにあります。

この問題に対処するには、ジョブが完了するまで待ってからクラスターをスケールダウンするか、ジョブを手動で終了させるか、スケーリング操作の完了後にジョブを再送信します。

保留中または実行中のジョブの一覧を表示するには、次の手順に従って YARN ResourceManager UI を使用できます。

1. [Azure ポータル](https://portal.azure.com)にサインインします。
2. 左側から、**[すべてのサービス]** > **[分析]** > **[HDInsight クラスター]** に移動し、クラスターを選択します。
3. メイン ビューから、 **[クラスター ダッシュボード]** > **[Ambari ホーム]** に移動します。 クラスターのログイン資格情報を入力します。
4. Ambari UI から、左側のメニューにあるサービスの一覧で **[YARN]** を選択します。  
5. [YARN] ページから **[クイック リンク]** を選択し、アクティブなヘッド ノードにポインターを置き、**[ResourceManager UI]** を選択します。

    ![ResourceManager UI](./media/hdinsight-scaling-best-practices/resourcemanager-ui.png)

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

## <a name="rebalancing-an-apache-hbase-cluster"></a>Apache HBase クラスターの再調整

リージョン サーバーは、スケーリング操作の完了から数分以内に自動的に調整されます。 リージョン サーバーを手動で調整するには、次の手順を使用します。

1. SSH を使用して HDInsight クラスターに接続します。 詳細については、[HDInsight での SSH の使用](hdinsight-hadoop-linux-use-ssh-unix.md)に関するページを参照してください。

2. HBase シェルを起動します。

        hbase shell

3. 次のコマンドを使用して、リージョン サーバーを手動で調整します。

        balancer

## <a name="scale-down-implications"></a>スケールダウンの影響

前述のように、保留中または実行中のジョブはすべて、スケールダウン操作の完了時に終了します。 ただし、スケールダウンが発生した場合は他にも影響が生じる可能性があります。

## <a name="hdinsight-name-node-stays-in-safe-mode-after-scaling-down"></a>HDInsight の名前ノードはスケールダウン後もセーフ モードを維持する

ワーカー ノードが最低限の 1 つになるようにクラスターを縮小した場合、パッチの適用が原因でワーカー ノードが再起動されたときまたはスケーリング操作の直後に、Apache HDFS がセーフ モードのままになることがあります。

この主な原因は、Hive がいくつかの `scratchdir` ファイルを使用していて、既定で各ブロックの 3 つのレプリカを必要としていることです。ただし、ワーカー ノードが最低限の 1 つになるようにスケールダウンした場合は、1 つのレプリカしか作成できません。 結果として、`scratchdir` 内のファイルは "*レプリケーション数が不足*" します。 これが原因で、スケール操作後にサービスが再起動したときに HDFS はセーフ モードのままになります。

スケールダウンが試行されると、HDInsight は Apache Ambari 管理インターフェイスを使用してまず不要なワーカー ノードを削除します。これにより、それらの HDFS ブロックが他のオンライン ワーカー ノードにレプリケートされます。その後、クラスターを安全にスケールダウンします。 HDFS は、メンテナンス期間中はセーフ モードになり、スケーリングが終了すると元の状態に戻ります。 このときに、HDFS がセーフ モードのままになる場合があります。

HDFS では、`dfs.replication` が 3 に設定されています。 したがって、オンラインのワーカー ノードが 3 つ未満の場合は常にスクラッチ ファイルのブロックのレプリケーション数が不足します。これは、各ファイル ブロックの予想される 3 つのコピーを利用できないためです。

HDFS のセーフ モードを終了するためのコマンドを実行できます。 たとえば、セーフ モードになっている唯一の原因が一時ファイルのレプリケーション数不足であることがわかっている場合は、安全にセーフ モードを終了できます。 これは、レプリケーション数が不足しているファイルが Hive の一時スクラッチ ファイルであるためです。

```bash
hdfs dfsadmin -D 'fs.default.name=hdfs://mycluster/' -safemode leave
```

セーフ モードを終了すると、手動で一時ファイルを削除するか、Hive が最終的にそれらを自動クリーンアップするまで待つことができます。

### <a name="example-errors-when-safe-mode-is-turned-on"></a>セーフ モードがオンになっている場合のエラーの例

* H070 Unable to open Hive session. org.apache.hadoop.ipc.RemoteException(org.apache.hadoop.ipc.RetriableException): org.apache.hadoop.hdfs.server.namenode.SafeModeException:**Cannot create directory** /tmp/hive/hive/819c215c-6d87-4311-97c8-4f0b9d2adcf0. **Name node is in safe mode**. The reported blocks 75 needs additional 12 blocks to reach the threshold 0.9900 of total blocks 87. The number of live datanodes 10 has reached the minimum number 0. Safe mode will be turned off automatically once the thresholds have been reached.

* H100 Unable to submit statement show databases: org.apache.thrift.transport.TTransportException: org.apache.http.conn.HttpHostConnectException:Connect to hn0-clustername.servername.internal.cloudapp.net:10001 [hn0-clustername.servername. internal.cloudapp.net/1.1.1.1] failed:**Connection refused**

* H020 Could not establish connection to hn0-hdisrv.servername.bx.internal.cloudapp.net:10001: org.apache.thrift.transport.TTransportException:Could not create http connection to http:\//hn0-hdisrv.servername.bx.internal.cloudapp.net:10001/. org.apache.http.conn.HttpHostConnectException:Connect to hn0-hdisrv.servername.bx.internal.cloudapp.net:10001 [hn0-hdisrv.servername.bx.internal.cloudapp.net/10.0.0.28] failed:Connection refused: org.apache.thrift.transport.TTransportException:Could not create http connection to http:\//hn0-hdisrv.servername.bx.internal.cloudapp.net:10001/. org.apache.http.conn.HttpHostConnectException:Connect to hn0-hdisrv.servername.bx.internal.cloudapp.net:10001 [hn0-hdisrv.servername.bx.internal.cloudapp.net/10.0.0.28] failed:**Connection refused**

* From the Hive logs:WARN [main]: server.HiveServer2 (HiveServer2.java:startHiveServer2(442)) – Error starting HiveServer2 on attempt 21, will retry in 60 seconds java.lang.RuntimeException:Error applying authorization policy on hive configuration: org.apache.hadoop.ipc.RemoteException(org.apache.hadoop.ipc.RetriableException): org.apache.hadoop.hdfs.server.namenode.SafeModeException:**Cannot create directory** /tmp/hive/hive/70a42b8a-9437-466e-acbe-da90b1614374. **Name node is in safe mode**.
    The reported blocks 0 needs additional 9 blocks to reach the threshold 0.9900 of total blocks 9.
    The number of live datanodes 10 has reached the minimum number 0. **Safe mode will be turned off automatically once the thresholds have been reached**.
    at org.apache.hadoop.hdfs.server.namenode.FSNamesystem.checkNameNodeSafeMode(FSNamesystem.java:1324)

`/var/log/hadoop/hdfs/` フォルダーから、クラスターがスケーリングされた時刻付近の名前ノード ログを確認すると、セーフ モードに入った時刻がわかります。 ログ ファイルの名前は `Hadoop-hdfs-namenode-hn0-clustername.*` です。

上記のエラーの根本原因は、Hive がクエリの実行中に HDFS 内の一時ファイルを利用することです。 HDFS がセーフ モードになると、Hive は HDFS に書き込めなくなるため、クエリを実行できません。 HDFS 内の一時ファイルは、各ワーカー ノード VM にマウントされたローカル ドライブにあり、最低 3 つのレプリカにあるその他のワーカー ノード間でレプリケートされます。

Hive の `hive.exec.scratchdir` パラメーターは `/etc/hive/conf/hive-site.xml` 内で構成されます。

```xml
<property>
    <name>hive.exec.scratchdir</name>
    <value>hdfs://mycluster/tmp/hive</value>
</property>
```

### <a name="view-the-health-and-state-of-your-hdfs-file-system"></a>HDFS ファイル システムの正常性と状態の表示

各名前ノードから状態レポートを表示して、ノードがセーフ モードになっているかどうかを確認できます。 レポートを表示するには、各ヘッド ノードに SSH で接続して次のコマンドを実行します。

```
hdfs dfsadmin -D 'fs.default.name=hdfs://mycluster/' -safemode get
```

![セーフ モード オフ](./media/hdinsight-scaling-best-practices/safe-mode-off.png)

> [!NOTE]  
> HDInsight の既定のファイル システムは Azure Storage または Azure Data Lake Storage のどちらかであるため、`-D` スイッチが必要です。 `-D` は、ローカルの HDFS ファイル システムに対してコマンドが実行されるように指定します。

次に、HDFS の状態の詳細を示すレポートを表示できます。

```
hdfs dfsadmin -D 'fs.default.name=hdfs://mycluster/' -report
```

このコマンドを実行すると、すべてのブロックが期待される範囲にレプリケートされている正常な状態のクラスターでは、次のような結果が表示されます。

![セーフ モード オフ](./media/hdinsight-scaling-best-practices/report.png)

HDFS は、欠落しているファイル ブロックやレプリケーション数が不足しているブロックなど、さまざまなファイルでの不整合をチェックする `fsck` コマンドをサポートしています。 `fsck` コマンドを `scratchdir` (一時スクラッチ ディスク) ファイルに対して実行するには、次のように入力します。

```
hdfs fsck -D 'fs.default.name=hdfs://mycluster/' /tmp/hive/hive
```

レプリケーション数が不足しているブロックがない正常な HDFS ファイル システムに対して実行すると、次のような出力が表示されます。

```
Connecting to namenode via http://hn0-scalin.name.bx.internal.cloudapp.net:30070/fsck?ugi=sshuser&path=%2Ftmp%2Fhive%2Fhive
FSCK started by sshuser (auth:SIMPLE) from /10.0.0.21 for path /tmp/hive/hive at Thu Jul 06 20:07:01 UTC 2017
..Status: HEALTHY
 Total size:    53 B
 Total dirs:    5
 Total files:   2
 Total symlinks:                0 (Files currently being written: 2)
 Total blocks (validated):      2 (avg. block size 26 B)
 Minimally replicated blocks:   2 (100.0 %)
 Over-replicated blocks:        0 (0.0 %)
 Under-replicated blocks:       0 (0.0 %)
 Mis-replicated blocks:         0 (0.0 %)
 Default replication factor:    3
 Average block replication:     3.0
 Corrupt blocks:                0
 Missing replicas:              0 (0.0 %)
 Number of data-nodes:          4
 Number of racks:               1
FSCK ended at Thu Jul 06 20:07:01 UTC 2017 in 3 milliseconds


The filesystem under path '/tmp/hive/hive' is HEALTHY
```

これに対し、レプリケーション数が不足しているブロックがある HDFS ファイル システムに対して `fsck` コマンドを実行した場合は、次のような出力が表示されます。

```
Connecting to namenode via http://hn0-scalin.name.bx.internal.cloudapp.net:30070/fsck?ugi=sshuser&path=%2Ftmp%2Fhive%2Fhive
FSCK started by sshuser (auth:SIMPLE) from /10.0.0.21 for path /tmp/hive/hive at Thu Jul 06 20:13:58 UTC 2017
.
/tmp/hive/hive/4f3f4253-e6d0-42ac-88bc-90f0ea03602c/inuse.info:  Under replicated BP-1867508080-10.0.0.21-1499348422953:blk_1073741826_1002. Target Replicas is 3 but found 1 live replica(s), 0 decommissioned replica(s) and 0 decommissioning replica(s).
.
/tmp/hive/hive/e7c03964-ff3a-4ee1-aa3c-90637a1f4591/inuse.info: CORRUPT blockpool BP-1867508080-10.0.0.21-1499348422953 block blk_1073741825

/tmp/hive/hive/e7c03964-ff3a-4ee1-aa3c-90637a1f4591/inuse.info: MISSING 1 blocks of total size 26 B.Status: CORRUPT
 Total size:    53 B
 Total dirs:    5
 Total files:   2
 Total symlinks:                0 (Files currently being written: 2)
 Total blocks (validated):      2 (avg. block size 26 B)
  ********************************
  UNDER MIN REPL'D BLOCKS:      1 (50.0 %)
  dfs.namenode.replication.min: 1
  CORRUPT FILES:        1
  MISSING BLOCKS:       1
  MISSING SIZE:         26 B
  CORRUPT BLOCKS:       1
  ********************************
 Minimally replicated blocks:   1 (50.0 %)
 Over-replicated blocks:        0 (0.0 %)
 Under-replicated blocks:       1 (50.0 %)
 Mis-replicated blocks:         0 (0.0 %)
 Default replication factor:    3
 Average block replication:     0.5
 Corrupt blocks:                1
 Missing replicas:              2 (33.333332 %)
 Number of data-nodes:          1
 Number of racks:               1
FSCK ended at Thu Jul 06 20:13:58 UTC 2017 in 28 milliseconds


The filesystem under path '/tmp/hive/hive' is CORRUPT
```

HDFS の状態は Ambari UI 上でも確認できます。左側の **[HDFS]** サービスを選択するか、`https://<HDInsightClusterName>.azurehdinsight.net/#/main/services/HDFS/summary` を使用します。

![Ambari での HDFS の状態](./media/hdinsight-scaling-best-practices/ambari-hdfs.png)

アクティブまたはスタンバイ状態の NameNode で 1 つ以上の重大なエラーが見つかる場合もあります。 NameNode ブロックの正常性を確認するには、アラートの横にある NameNode リンクを選択します。

![NameNode ブロックの正常性](./media/hdinsight-scaling-best-practices/ambari-hdfs-crit.png)

スクラッチ ファイルをクリーンアップして、ブロックのレプリケーション エラーを解消するには、各ヘッド ノードに SSH で接続して次のコマンドを実行します。

```
hadoop fs -rm -r -skipTrash hdfs://mycluster/tmp/hive/
```

> [!NOTE]  
> このコマンドにより、一部のジョブがまだ実行中であっても、Hive が中断される場合があります。

### <a name="how-to-prevent-hdinsight-from-getting-stuck-in-safe-mode-due-to-under-replicated-blocks"></a>レプリケーション数が足りないブロックが原因で HDInsight がセーフ モードのままにならないようにする方法

HDInsight がセーフ モードのままにならないようにする方法はいくつかあります。

* HDInsight をスケールダウンする前にすべての Hive ジョブを停止します。 または、実行中の Hive ジョブとの競合を回避するようにスケールダウン処理をスケジュールします。
* スケールダウンする前に、HDFS 内にある Hive のスクラッチ `tmp` ディレクトリ ファイルを手動でクリーンアップします。
* HDInsight を最低 3 つのワーカー ノードにスケールダウンするだけです。 ワーカー ノードが 1 つにならないようにします。
* 必要に応じて、セーフ モードを終了するコマンドを実行します。

以下のセクションでは、これらのオプションについて説明します。

#### <a name="stop-all-hive-jobs"></a>すべての Hive ジョブを停止する

すべての Hive ジョブを停止してから、1 つのワーカー ノードにスケールダウンします。 ワークロードがスケジュールされている場合は、Hive の処理が完了した後でスケールダウンします。

これにより、tmp フォルダー内のスクラッチ ファイルの数を最小限に抑えることができます (存在する場合)。

#### <a name="manually-clean-up-hives-scratch-files"></a>Hive のスクラッチ ファイルを手動でクリーンアップする

Hive で一時ファイルが残っている場合は、これらのファイルを手動でクリーンアップしてからスケールダウンすることで、セーフ モードを回避できます。

1. Hive サービスを停止し、すべてのクエリとジョブが完了していることを確認します。

2. `hdfs://mycluster/tmp/hive/` ディレクトリのコンテンツを一覧表示して、ファイルが含まれているかどうかを確認します。

    ```
    hadoop fs -ls -R hdfs://mycluster/tmp/hive/hive
    ```
    
    ファイルが存在する場合の出力例は次のとおりです。

    ```
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

    ```
    hadoop fs -rm -r -skipTrash hdfs://mycluster/tmp/hive/
    ```
    
#### <a name="scale--hdinsight-to-three-worker-nodes"></a>HDInsight を 3 つのワーカー ノードにスケーリングする

セーフ モードのままになる問題が解決せず、上記の手順を実行できない場合は、3 つのワーカー ノードにスケールダウンするだけで、この問題を回避できます。 1 つのワーカー ノードにスケールダウンすることと比較すると、コスト的な制限により、最適ではない可能性があります。 しかし、ワーカー ノードが 1 つのみの場合、HDFS では、データの 3 つのレプリカをクラスターで利用できることは保証できません。

#### <a name="run-the-command-to-leave-safe-mode"></a>セーフ モードを終了するコマンドを実行する

最後のオプションでは、HDFS がセーフ モードになるまれなケースを監視し、セーフ モードを終了するコマンドを実行します。 HDFS がセーフ モードになった原因が Hive ファイルのレプリケーション数の不足であることがわかったら、次のコマンドを実行してセーフ モードを終了します。

* Linux の HDInsight:

    ```bash
    hdfs dfsadmin -D 'fs.default.name=hdfs://mycluster/' -safemode leave
    ```
    
* Windows の HDInsight:

    ```bash
    hdfs dfsadmin -fs hdfs://headnodehost:9000 -safemode leave
    ```
    
## <a name="next-steps"></a>次の手順

* [Azure HDInsight の概要](hadoop/apache-hadoop-introduction.md)
* [クラスターのスケーリング](hdinsight-administer-use-portal-linux.md#scale-clusters)
* [Ambari Web UI を使用した HDInsight クラスターの管理](hdinsight-hadoop-manage-ambari.md)
