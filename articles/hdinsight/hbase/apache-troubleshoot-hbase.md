---
title: Azure HDInsight を使用した HBase のトラブルシューティング
description: HBase と Azure HDInsight の操作についてよく寄せられる質問とその回答を示します。
services: hdinsight
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.custom: hdinsightactive, seodec18
ms.topic: conceptual
ms.date: 12/06/2018
ms.openlocfilehash: 37a8882653ffede121d2e2cd3f3357741d8d641a
ms.sourcegitcommit: 223604d8b6ef20a8c115ff877981ce22ada6155a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/22/2019
ms.locfileid: "58361933"
---
# <a name="troubleshoot-apache-hbase-by-using-azure-hdinsight"></a>Azure HDInsight を使用した Apache HBase のトラブルシューティング

Apache Ambari で Apache HBase ペイロードを操作するときに発生する主な問題とその解決策について説明します。

## <a name="how-do-i-run-hbck-command-reports-with-multiple-unassigned-regions"></a>複数の未割り当てリージョンで hbck コマンド レポートを実行する方法

`hbase hbck` コマンドを実行したときに、"複数のリージョンが未割り当てである (リージョン チェーンに穴がある)" ことを示すエラー メッセージが表示されることがよくあります。

HBase Master UI に、すべてのリージョン サーバーのバランスが取れていないリージョンの数が表示されます。 この場合、`hbase hbck` コマンドを実行して、リージョン チェーンの穴を確認できます。

これらの穴はオフラインのリージョンが原因と考えられるので、まず割り当てを修正します。 

未割り当てのリージョンを通常の状態に戻すには、次の手順を実行します。

1. SSH を使用して HDInsight HBase クラスターにサインインします。
2. `hbase zkcli` コマンドを実行して Apache ZooKeeper シェルに接続します。
3. `rmr /hbase/regions-in-transition` コマンドまたは `rmr /hbase-unsecure/regions-in-transition` コマンドを実行します。
4. `exit` コマンドを使用して `hbase zkcli` シェルを終了します。
5. Apache Ambari UI を開き、Active HBase Master サービスを再起動します。
6. `hbase hbck` コマンドを (オプションを指定せずに) もう一度実行します。 このコマンドの出力で、すべてのリージョンが割り当てられていることを確認します。


## <a name="how-do-i-fix-timeout-issues-with-hbck-commands-for-region-assignments"></a>hbck コマンドを使用してリージョンを割り当てるときのタイムアウトの問題を解決する方法

### <a name="issue"></a>問題

`hbck` コマンドを使用したときのタイムアウトの問題の原因として、複数のリージョンが長時間にわたって "移行中" 状態にあることが考えられます。 これらのリージョンは、HBase Master UI にオフラインとして表示されていることがわかります。 多数のリージョンが移行を試行中であるために、HBase Master がタイムアウトし、それらのリージョンをオンラインに戻すことができなくなります。

### <a name="resolution-steps"></a>解決手順

1. SSH を使用して HDInsight HBase クラスターにサインインします。
2. `hbase zkcli` コマンドを実行して Apache ZooKeeper シェルに接続します。
3. `rmr /hbase/regions-in-transition` コマンドまたは `rmr /hbase-unsecure/regions-in-transition` コマンドを実行します。
4. `exit` コマンドを使用して `hbase zkcli` シェルを終了します。
5. Ambari UI で、Active HBase Master サービスを再起動します。
6. `hbase hbck -fixAssignments` コマンドをもう一度実行します。

## <a name="how-do-i-force-disable-hdfs-safe-mode-in-a-cluster"></a>クラスターで HDFS のセーフ モードを強制的に無効にする方法

### <a name="issue"></a>問題

HDInsight クラスター上でローカル Apache Hadoop 分散ファイル システム (HDFS) のセーフ モードが解除されない。

### <a name="detailed-description"></a>詳しい説明

このエラーは、次の HDFS コマンドの実行に失敗したことが原因で発生する可能性があります。

```apache
hdfs dfs -D "fs.default.name=hdfs://mycluster/" -mkdir /temp
```

コマンドを実行しようとしたときに、次のようなエラーが発生します。

```apache
hdiuser@hn0-spark2:~$ hdfs dfs -D "fs.default.name=hdfs://mycluster/" -mkdir /temp
17/04/05 16:20:52 WARN retry.RetryInvocationHandler: Exception while invoking ClientNamenodeProtocolTranslatorPB.mkdirs over hn0-spark2.2oyzcdm4sfjuzjmj5dnmvscjpg.dx.internal.cloudapp.net/10.0.0.22:8020. Not retrying because try once and fail.
org.apache.hadoop.ipc.RemoteException(org.apache.hadoop.hdfs.server.namenode.SafeModeException): Cannot create directory /temp. Name node is in safe mode.
It was turned on manually. Use "hdfs dfsadmin -safemode leave" to turn safe mode off.
        at org.apache.hadoop.hdfs.server.namenode.FSNamesystem.checkNameNodeSafeMode(FSNamesystem.java:1359)
        at org.apache.hadoop.hdfs.server.namenode.FSNamesystem.mkdirs(FSNamesystem.java:4010)
        at org.apache.hadoop.hdfs.server.namenode.NameNodeRpcServer.mkdirs(NameNodeRpcServer.java:1102)
        at org.apache.hadoop.hdfs.protocolPB.ClientNamenodeProtocolServerSideTranslatorPB.mkdirs(ClientNamenodeProtocolServerSideTranslatorPB.java:630)
        at org.apache.hadoop.hdfs.protocol.proto.ClientNamenodeProtocolProtos$ClientNamenodeProtocol$2.callBlockingMethod(ClientNamenodeProtocolProtos.java)
        at org.apache.hadoop.ipc.ProtobufRpcEngine$Server$ProtoBufRpcInvoker.call(ProtobufRpcEngine.java:640)
        at org.apache.hadoop.ipc.RPC$Server.call(RPC.java:982)
        at org.apache.hadoop.ipc.Server$Handler$1.run(Server.java:2313)
        at org.apache.hadoop.ipc.Server$Handler$1.run(Server.java:2309)
        at java.security.AccessController.doPrivileged(Native Method)
        at javax.security.auth.Subject.doAs(Subject.java:422)
        at org.apache.hadoop.security.UserGroupInformation.doAs(UserGroupInformation.java:1724)
        at org.apache.hadoop.ipc.Server$Handler.run(Server.java:2307)
        at org.apache.hadoop.ipc.Client.getRpcResponse(Client.java:1552)
        at org.apache.hadoop.ipc.Client.call(Client.java:1496)
        at org.apache.hadoop.ipc.Client.call(Client.java:1396)
        at org.apache.hadoop.ipc.ProtobufRpcEngine$Invoker.invoke(ProtobufRpcEngine.java:233)
        at com.sun.proxy.$Proxy10.mkdirs(Unknown Source)
        at org.apache.hadoop.hdfs.protocolPB.ClientNamenodeProtocolTranslatorPB.mkdirs(ClientNamenodeProtocolTranslatorPB.java:603)
        at sun.reflect.NativeMethodAccessorImpl.invoke0(Native Method)
        at sun.reflect.NativeMethodAccessorImpl.invoke(NativeMethodAccessorImpl.java:62)
        at sun.reflect.DelegatingMethodAccessorImpl.invoke(DelegatingMethodAccessorImpl.java:43)
        at java.lang.reflect.Method.invoke(Method.java:498)
        at org.apache.hadoop.io.retry.RetryInvocationHandler.invokeMethod(RetryInvocationHandler.java:278)
        at org.apache.hadoop.io.retry.RetryInvocationHandler.invoke(RetryInvocationHandler.java:194)
        at org.apache.hadoop.io.retry.RetryInvocationHandler.invoke(RetryInvocationHandler.java:176)
        at com.sun.proxy.$Proxy11.mkdirs(Unknown Source)
        at org.apache.hadoop.hdfs.DFSClient.primitiveMkdir(DFSClient.java:3061)
        at org.apache.hadoop.hdfs.DFSClient.mkdirs(DFSClient.java:3031)
        at org.apache.hadoop.hdfs.DistributedFileSystem$24.doCall(DistributedFileSystem.java:1162)
        at org.apache.hadoop.hdfs.DistributedFileSystem$24.doCall(DistributedFileSystem.java:1158)
        at org.apache.hadoop.fs.FileSystemLinkResolver.resolve(FileSystemLinkResolver.java:81)
        at org.apache.hadoop.hdfs.DistributedFileSystem.mkdirsInternal(DistributedFileSystem.java:1158)
        at org.apache.hadoop.hdfs.DistributedFileSystem.mkdirs(DistributedFileSystem.java:1150)
        at org.apache.hadoop.fs.FileSystem.mkdirs(FileSystem.java:1898)
        at org.apache.hadoop.fs.shell.Mkdir.processNonexistentPath(Mkdir.java:76)
        at org.apache.hadoop.fs.shell.Command.processArgument(Command.java:273)
        at org.apache.hadoop.fs.shell.Command.processArguments(Command.java:255)
        at org.apache.hadoop.fs.shell.FsCommand.processRawArguments(FsCommand.java:119)
        at org.apache.hadoop.fs.shell.Command.run(Command.java:165)
        at org.apache.hadoop.fs.FsShell.run(FsShell.java:297)
        at org.apache.hadoop.util.ToolRunner.run(ToolRunner.java:76)
        at org.apache.hadoop.util.ToolRunner.run(ToolRunner.java:90)
        at org.apache.hadoop.fs.FsShell.main(FsShell.java:350)
mkdir: Cannot create directory /temp. Name node is in safe mode.
```

### <a name="probable-cause"></a>考えられる原因

HDInsight クラスターが少数のノードにスケールダウンされています。 ノード数が HDFS レプリケーション係数未満か、それに近い数になっています。

### <a name="resolution-steps"></a>解決手順 

1. 次のコマンドを実行して、HDInsight クラスター上の HDFS の状態を取得します。

   ```apache
   hdfs dfsadmin -D "fs.default.name=hdfs://mycluster/" -report
   ```

   ```apache
   hdiuser@hn0-spark2:~$ hdfs dfsadmin -D "fs.default.name=hdfs://mycluster/" -report
   Safe mode is ON
   Configured Capacity: 3372381241344 (3.07 TB)
   Present Capacity: 3138625077248 (2.85 TB)
   DFS Remaining: 3102710317056 (2.82 TB)
   DFS Used: 35914760192 (33.45 GB)
   DFS Used%: 1.14%
   Under replicated blocks: 0
   Blocks with corrupt replicas: 0
   Missing blocks: 0
   Missing blocks (with replication factor 1): 0

   -------------------------------------------------
   Live datanodes (8):

   Name: 10.0.0.17:30010 (10.0.0.17)
   Hostname: 10.0.0.17
   Decommission Status : Normal
   Configured Capacity: 421547655168 (392.60 GB)
   DFS Used: 5288128512 (4.92 GB)
   Non DFS Used: 29087272960 (27.09 GB)
   DFS Remaining: 387172253696 (360.58 GB)
   DFS Used%: 1.25%
   DFS Remaining%: 91.85%
   Configured Cache Capacity: 0 (0 B)
   Cache Used: 0 (0 B)
   Cache Remaining: 0 (0 B)
   Cache Used%: 100.00%
   Cache Remaining%: 0.00%
   Xceivers: 2
   Last contact: Wed Apr 05 16:22:00 UTC 2017
   ...

   ```
2. 次のコマンドを使用して、HDInsight クラスター上の HDFS の整合性をチェックすることもできます。

   ```apache
   hdiuser@hn0-spark2:~$ hdfs fsck -D "fs.default.name=hdfs://mycluster/" /
   ```

   ```apache
   Connecting to namenode via http://hn0-spark2.2oyzcdm4sfjuzjmj5dnmvscjpg.dx.internal.cloudapp.net:30070/fsck?ugi=hdiuser&path=%2F
   FSCK started by hdiuser (auth:SIMPLE) from /10.0.0.22 for path / at Wed Apr 05 16:40:28 UTC 2017
   ....................................................................................................

   ....................................................................................................
   ..................Status: HEALTHY
   Total size:    9330539472 B
   Total dirs:    37
   Total files:   2618
   Total symlinks:                0 (Files currently being written: 2)
   Total blocks (validated):      2535 (avg. block size 3680686 B)
   Minimally replicated blocks:   2535 (100.0 %)
   Over-replicated blocks:        0 (0.0 %)
   Under-replicated blocks:       0 (0.0 %)
   Mis-replicated blocks:         0 (0.0 %)
   Default replication factor:    3
   Average block replication:     3.0
   Corrupt blocks:                0
   Missing replicas:              0 (0.0 %)
   Number of data-nodes:          8
   Number of racks:               1
   FSCK ended at Wed Apr 05 16:40:28 UTC 2017 in 187 milliseconds

   The filesystem under path '/' is HEALTHY
   ```

3. 欠落しているブロック、破損しているブロック、またはレプリケーション数が足りないブロックが存在しないことがわかった場合や、それらのブロックを無視できると判断した場合は、次のコマンドを実行してネーム ノードのセーフ モードを解除します。

   ```apache
   hdfs dfsadmin -D "fs.default.name=hdfs://mycluster/" -safemode leave
   ```


## <a name="how-do-i-fix-jdbc-or-sqlline-connectivity-issues-with-apache-phoenix"></a>Apache Phoenix との JDBC 接続または SQLLine 接続の問題を解決する方法

### <a name="resolution-steps"></a>解決手順

Apache Phoenix に接続するには、アクティブな Apache ZooKeeper ノードの IP アドレスを指定する必要があります。 sqlline.py が接続しようとしている Zookeeper サービスが稼働していることを確認します。
1. SSH を使用して HDInsight クラスターにサインインします。
2. 次のコマンドを入力します。
                
   ```apache
           "/usr/hdp/current/phoenix-client/bin/sqlline.py <IP of machine where Active Zookeeper is running"
   ```

   > [!Note] 
   > アクティブな Zookeeper ノードの IP アドレスは、Ambari UI から取得できます。 **[HBase]** > **[Quick Links]\(クイック リンク\)** > **[ZK\* (Active)]\(ZK (アクティブ)\)** > **[Zookeeper Info]\(Zookeeper 情報\)** に移動します。 

3. sqlline.py が Phoenix に接続し、タイムアウトしていない場合は、次のコマンドを実行して Phoenix の可用性と正常性を検証します。

   ```apache
           !tables
           !quit
   ```      
4. 上記のコマンドが動作すれば、問題はありません。 ユーザーが指定した IP アドレスに誤りがあると考えられます。 ただし、コマンドが長時間一時停止した後で次のエラーが表示された場合は、手順 5. に進んでください。

   ```apache
           Error while connecting to sqlline.py (Hbase - phoenix) Setting property: [isolation, TRANSACTION_READ_COMMITTED] issuing: !connect jdbc:phoenix:10.2.0.7 none none org.apache.phoenix.jdbc.PhoenixDriver Connecting to jdbc:phoenix:10.2.0.7 SLF4J: Class path contains multiple SLF4J bindings. 
   ```

5. ヘッド ノード (hn0) から次のコマンドを実行して、Phoenix の SYSTEM.CATALOG テーブルの状態を診断します。

   ```apache
            hbase shell
                
           count 'SYSTEM.CATALOG'
   ```

   このコマンドは次のようなエラーを返します。 

   ```apache
           ERROR: org.apache.hadoop.hbase.NotServingRegionException: Region SYSTEM.CATALOG,,1485464083256.c0568c94033870c517ed36c45da98129. is not online on 10.2.0.5,16020,1489466172189) 
   ```
6. Apache Ambari UI で、次の手順に従って、すべての ZooKeeper ノード上で HMaster サービスを再起動します。

    1. HBase の **[Summary]\(概要\)** セクションで、**[HBase]** > **[Active HBase Master]** に移動します。 
    2. **[Components]\(コンポーネント\)** セクションで、HBase Master サービスを再起動します。
    3. 残りのすべての **Standby HBase Master** サービスに対して、上記の手順を繰り返します。 

HBase Master サービスの動作が安定し、回復プロセスが完了するまでに最大 5 分かかることがあります。 数分後、sqlline.py コマンドを繰り返して、SYSTEM.CATALOG テーブルが正常に機能していることと、このテーブルに対してクエリを実行できることを確認します。 

SYSTEM.CATALOG テーブルが正常な状態に戻れば、Phoenix への接続の問題は自動的に解決されます。


## <a name="what-causes-a-master-server-to-fail-to-start"></a>マスター サーバーの起動が失敗する原因

### <a name="error"></a>Error 

An atomic renaming failure occurs. (アトミックな名前変更エラーが発生しました。)

### <a name="detailed-description"></a>詳しい説明

スタートアップ プロセス中に、HMaster はさまざまな初期化手順を実行します。 これらの手順には、スクラッチ (.tmp) フォルダーからデータ フォルダーへのデータの移動が含まれます。 また、HMaster は先書きログ (WALs) フォルダーを参照して、応答しないリージョン サーバーが存在するかどうかも確認します。 

起動中に、HMaster はこれらのフォルダーに対して基本的な `list` コマンドを実行します。 いずれかのフォルダーに予期しないファイルが見つかると、例外がスローされ、HMaster は起動しません。  

### <a name="probable-cause"></a>考えられる原因

リージョン サーバー ログで、問題のファイルが作成された時間帯を特定し、ファイルの作成前後にプロセスのクラッシュが発生していないかどうかを確認します  (この作業で支援が必要な場合は、HBase サポートに連絡してください)。このバグを回避し、プロセスを正常にシャットダウンできるように、より堅牢なメカニズムを提供するためにこの情報を活用させていただきます。

### <a name="resolution-steps"></a>解決手順

呼び出し履歴を確認し、問題の原因となっていフォルダーを特定します (たとえば、WALs フォルダーや .tmp フォルダーが原因の場合があります)。 次に、Cloud Explorer または HDFS コマンドを使用して問題のファイルを特定します。 通常、これは \*-renamePending.json ファイルです  (\*-renamePending.json ファイルは、WASB ドライバーでアトミックな名前変更操作を実装するために使用されるジャーナル ファイルです。 この実装のバグが原因で、プロセスのクラッシュ後にこれらのファイルが残されることがあります)。Cloud Explorer または HDFS コマンドを使用して、このファイルを強制的に削除します。 

また、この場所に *$$$.$$$* のような名前の一時ファイルが存在する場合もあります。 このファイルを表示するには、HDFS `ls` コマンドを使用する必要があります。Cloud Explorer で表示することはできません。 このファイルを削除するには、HDFS コマンド `hdfs dfs -rm /\<path>\/\$\$\$.\$\$\$` を使用します。  

これらのコマンドを実行したら、HMaster はすぐに起動します。 

### <a name="error"></a>Error

No server address listed in *hbase: meta* for region xxx. (リージョン xxx の hbase: meta にサーバー アドレスがありません。)

### <a name="detailed-description"></a>詳しい説明

Linux クラスターで、*hbase: meta* テーブルがオンラインになっていないことを示すメッセージが表示される場合があります。 `hbck` を実行すると、"hbase: meta table replicaId 0 is not found on any region"\(hbase: meta テーブルの replicaId 0 がどのリージョンでも見つかりません\) と報告されます。 HBase の再起動後に、HMaster が初期化できなかったことが問題と考えられます。 HMaster のログに、次のメッセージが示される場合があります: "No server address listed in hbase: meta for region hbase: backup \<region name\>" (リージョン hbase: backup \<リージョン名\> の hbase: meta にサーバー アドレスがありません)。  

### <a name="resolution-steps"></a>解決手順

1. HBase シェルで次のコマンドを入力します (適宜、実際の値に置き換えてください)。  

   ```apache
   > scan 'hbase:meta'  
   ```

   ```apache
   > delete 'hbase:meta','hbase:backup <region name>','<column name>'  
   ```

2. *hbase: namespace* エントリを削除します。 このエントリがあると、*hbase: namespace* テーブルをスキャンしたときに同じエラーが報告されます。

3. HBase を実行状態にするために、Ambari UI で Active HMaster サービスを再起動します。  

4. HBase シェルで次のコマンドを実行して、すべてのオフライン テーブルを起動します。

   ```apache 
   hbase hbck -ignorePreCheckPermission -fixAssignments 
   ```

### <a name="additional-reading"></a>その他の情報

[HBase テーブルを処理できない](https://stackoverflow.com/questions/4794092/unable-to-access-hbase-table)


### <a name="error"></a>Error

HMaster times out with a fatal exception similar to "java.io.IOException: Timedout 300000ms waiting for namespace table to be assigned." ("java.io.IOException: namespace テーブルの割り当てを 300000 ms 待機してタイムアウトしました" に類似した致命的な例外によって HMaster がタイムアウトしました。)

### <a name="detailed-description"></a>詳しい説明

この問題は、HMaster サービスを再起動したときに、フラッシュされていない多数のテーブルとリージョンが存在する場合に発生します。 再起動に失敗すると、上記のエラー メッセージが表示されます。  

### <a name="probable-cause"></a>考えられる原因

これは HMaster サービスの既知の問題です。 一般的なクラスター スタートアップ タスクは時間がかかることがあります。 namespace テーブルがまだ割り当てられていないために HMaster がシャットダウンします。 この状況が発生するのは、フラッシュされていないデータが大量に存在し、5 分のタイムアウトでは間に合わない場合に限られます。
  
### <a name="resolution-steps"></a>解決手順

1. Apache Ambari UI 内で、**[HBase]** > **[Configs]\(構成\)** に移動します。 カスタム hbase-site.xml ファイルに次の設定を追加します。 

   ```apache
   Key: hbase.master.namespace.init.timeout Value: 2400000  
   ```

2. 必要なサービス (HMaster と、場合によっては他の HBase サービス) を再起動します。  


## <a name="what-causes-a-restart-failure-on-a-region-server"></a>リージョン サーバーでの再起動が失敗する原因

### <a name="issue"></a>問題

リージョン サーバーでの再起動の失敗は、ベスト プラクティスに従うことで防ぐことができます。 HBase リージョン サーバーの再起動を予定しているときは、負荷の大きいワークロード アクティビティを一時停止することをお勧めします。 シャットダウンの進行中に、アプリケーションが引き続きリージョン サーバーに接続していると、リージョン サーバーの再起動操作が数分遅れます。 また、最初にすべてのテーブルをフラッシュしておくことをお勧めします。 テーブルをフラッシュする方法については、[HDInsight HBase:テーブルをフラッシュして Apache HBase クラスターの再起動時間を短縮する方法](https://web.archive.org/web/20190112153155/https://blogs.msdn.microsoft.com/azuredatalake/2016/09/19/hdinsight-hbase-how-to-improve-hbase-cluster-restart-time-by-flushing-tables/) に関するページを参照してください。

Apache Ambari UI から HBase リージョン サーバー上での再起動操作を開始すると、リージョン サーバーが停止しても、すぐには再起動していないことがすぐにわかります。 

バックグラウンドで次のことが行われています。 

1. Ambari エージェントがリージョン サーバーに停止要求を送信します。
2. Ambari エージェントは、リージョン サーバーが正常にシャットダウンするのを 30 秒間待ちます。 
3. アプリケーションが引き続きリージョン サーバーに接続していると、サーバーはすぐにはシャットダウンしません。 シャットダウンが発生する前に 30 秒が経過してタイムアウトになります。 
4. 30 秒が経過すると、Ambari エージェントは強制終了 (`kill -9`) コマンドをリージョン サーバーに送信します。 これは、(各ワーカー ノードの /var/log/ ディレクトリにある) Ambari エージェント ログで確認できます。

   ```apache
           2017-03-21 13:22:09,171 - Execute['/usr/hdp/current/hbase-regionserver/bin/hbase-daemon.sh --config /usr/hdp/current/hbase-regionserver/conf stop regionserver'] {'only_if': 'ambari-sudo.sh  -H -E t
           est -f /var/run/hbase/hbase-hbase-regionserver.pid && ps -p `ambari-sudo.sh  -H -E cat /var/run/hbase/hbase-hbase-regionserver.pid` >/dev/null 2>&1', 'on_timeout': '! ( ambari-sudo.sh  -H -E test -
           f /var/run/hbase/hbase-hbase-regionserver.pid && ps -p `ambari-sudo.sh  -H -E cat /var/run/hbase/hbase-hbase-regionserver.pid` >/dev/null 2>&1 ) || ambari-sudo.sh -H -E kill -9 `ambari-sudo.sh  -H 
           -E cat /var/run/hbase/hbase-hbase-regionserver.pid`', 'timeout': 30, 'user': 'hbase'}
           2017-03-21 13:22:40,268 - Executing '! ( ambari-sudo.sh  -H -E test -f /var/run/hbase/hbase-hbase-regionserver.pid && ps -p `ambari-sudo.sh  -H -E cat /var/run/hbase/hbase-hbase-regionserver.pid` >
           /dev/null 2>&1 ) || ambari-sudo.sh -H -E kill -9 `ambari-sudo.sh  -H -E cat /var/run/hbase/hbase-hbase-regionserver.pid`'. Reason: Execution of 'ambari-sudo.sh su hbase -l -s /bin/bash -c 'export  
           PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/usr/games:/usr/local/games:/var/lib/ambari-agent ; /usr/hdp/current/hbase-regionserver/bin/hbase-daemon.sh --config /usr/hdp/curre
           nt/hbase-regionserver/conf stop regionserver was killed due timeout after 30 seconds
           2017-03-21 13:22:40,285 - File['/var/run/hbase/hbase-hbase-regionserver.pid'] {'action': ['delete']}
           2017-03-21 13:22:40,285 - Deleting File['/var/run/hbase/hbase-hbase-regionserver.pid']
   ```
   強制シャットダウンにより、リージョン サーバー プロセスが停止されても、プロセスに関連付けられているポートが解放されない場合があります。 この状況により、次のログに示すように、リージョン サーバーの起動時に AddressBindException が発生する可能性があります。 これは、リージョン サーバーが起動に失敗したワーカー ノードの /var/log/hbase ディレクトリにある region-server.log で確認できます。 

   ```apache

   2017-03-21 13:25:47,061 ERROR [main] regionserver.HRegionServerCommandLine: Region server exiting
   java.lang.RuntimeException: Failed construction of Regionserver: class org.apache.hadoop.hbase.regionserver.HRegionServer
   at org.apache.hadoop.hbase.regionserver.HRegionServer.constructRegionServer(HRegionServer.java:2636)
   at org.apache.hadoop.hbase.regionserver.HRegionServerCommandLine.start(HRegionServerCommandLine.java:64)
   at org.apache.hadoop.hbase.regionserver.HRegionServerCommandLine.run(HRegionServerCommandLine.java:87)
   at org.apache.hadoop.util.ToolRunner.run(ToolRunner.java:76)
   at org.apache.hadoop.hbase.util.ServerCommandLine.doMain(ServerCommandLine.java:126)
   at org.apache.hadoop.hbase.regionserver.HRegionServer.main(HRegionServer.java:2651)
        
   Caused by: java.lang.reflect.InvocationTargetException
   at sun.reflect.NativeConstructorAccessorImpl.newInstance0(Native Method)
   at sun.reflect.NativeConstructorAccessorImpl.newInstance(NativeConstructorAccessorImpl.java:57)
   at sun.reflect.DelegatingConstructorAccessorImpl.newInstance(DelegatingConstructorAccessorImpl.java:45)
   at java.lang.reflect.Constructor.newInstance(Constructor.java:526)
   at org.apache.hadoop.hbase.regionserver.HRegionServer.constructRegionServer(HRegionServer.java:2634)
   ... 5 more
        
   Caused by: java.net.BindException: Problem binding to /10.2.0.4:16020 : Address already in use
   at org.apache.hadoop.hbase.ipc.RpcServer.bind(RpcServer.java:2497)
   at org.apache.hadoop.hbase.ipc.RpcServer$Listener.<init>(RpcServer.java:580)
   at org.apache.hadoop.hbase.ipc.RpcServer.<init>(RpcServer.java:1982)
   at org.apache.hadoop.hbase.regionserver.RSRpcServices.<init>(RSRpcServices.java:863)
   at org.apache.hadoop.hbase.regionserver.HRegionServer.createRpcServices(HRegionServer.java:632)
   at org.apache.hadoop.hbase.regionserver.HRegionServer.<init>(HRegionServer.java:532)
   ... 10 more
        
   Caused by: java.net.BindException: Address already in use
   at sun.nio.ch.Net.bind0(Native Method)
   at sun.nio.ch.Net.bind(Net.java:463)
   at sun.nio.ch.Net.bind(Net.java:455)
   at sun.nio.ch.ServerSocketChannelImpl.bind(ServerSocketChannelImpl.java:223)
   at sun.nio.ch.ServerSocketAdaptor.bind(ServerSocketAdaptor.java:74)
   at org.apache.hadoop.hbase.ipc.RpcServer.bind(RpcServer.java:2495)
   ... 15 more
   ```

### <a name="resolution-steps"></a>解決手順

1. 再起動を開始する前に、HBase リージョン サーバーの負荷を軽減してみます。 
2. 代わりに (手順 1. では効果がない場合)、次のコマンドを使用して、ワーカー ノードでリージョン サーバーを手動で再起動してみます。

   ```apache
   sudo su - hbase -c "/usr/hdp/current/hbase-regionserver/bin/hbase-daemon.sh stop regionserver"
   sudo su - hbase -c "/usr/hdp/current/hbase-regionserver/bin/hbase-daemon.sh start regionserver"   
   ```

### <a name="see-also"></a>関連項目
[Azure HDInsight を使用したトラブルシューティング](../../hdinsight/hdinsight-troubleshoot-guide.md)
