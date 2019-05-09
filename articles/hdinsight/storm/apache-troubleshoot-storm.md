---
title: Azure HDInsight を使用した Storm のトラブルシューティング
description: Azure HDInsight での Apache Storm の使用についてよく寄せられる質問とその回答を示します。
keywords: Azure HDInsight, Storm, FAQ, トラブルシューティング ガイド, よくある質問
services: hdinsight
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.topic: conceptual
ms.date: 12/06/2018
ms.custom: seodec18
ms.openlocfilehash: 5634d812b3fbd4e904516767b008f63104b3d7b7
ms.sourcegitcommit: c94cf3840db42f099b4dc858cd0c77c4e3e4c436
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/19/2018
ms.locfileid: "53632740"
---
# <a name="troubleshoot-apache-storm-by-using-azure-hdinsight"></a>Azure HDInsight を使用した Apache Storm のトラブルシューティング

[Apache Ambari](https://ambari.apache.org/) で [Apache Storm](https://storm.apache.org/) ペイロードを操作するときに発生する主な問題とその解決策について説明します。

## <a name="how-do-i-access-the-storm-ui-on-a-cluster"></a>クラスターの Storm UI にアクセスする方法
ブラウザーから Storm UI にアクセスする場合、2 つの方法があります。

### <a name="ambari-ui"></a>Ambari UI
1. Ambari ダッシュボードに移動します。
2. サービスの一覧で **[Storm]** を選択します。
3. **[クイック リンク]** メニューの **[Storm UI]** を選択します。

### <a name="direct-link"></a>直接リンク
次の URL で Storm UI にアクセスできます。

https://\<クラスター DNS 名\>/stormui

例:

 https://stormcluster.azurehdinsight.net/stormui

## <a name="how-do-i-transfer-storm-event-hub-spout-checkpoint-information-from-one-topology-to-another"></a>Storm イベント ハブ スパウトのチェックポイント情報をトポロジ間で転送する方法

HDInsight Storm イベント ハブ スパウト .jar ファイルを使用して Azure Event Hubs から読み取るトポロジを開発するときは、同じ名前のトポロジを新しいクラスターにデプロイする必要があります。 ただし、以前のクラスターで [Apache ZooKeeper](https://zookeeper.apache.org/) にコミットされていたチェックポイント データを保持する必要があります。

### <a name="where-checkpoint-data-is-stored"></a>チェックポイント データの保存場所
オフセットのチェックポイント データは、イベント ハブによって Zookeeper の次の 2 つのルート パスに保存されます。
- 非トランザクション スパウトのチェックポイントは、/eventhubspout に保存されます。
- トランザクション スパウトのチェックポイント データは、/transactional に保存されます。

### <a name="how-to-restore"></a>復元方法
データを Zookeeper からエクスポートし、新しい名前で Zookeeper にインポートして戻すときに使用するスクリプトとライブラリを入手するには、[HDInsight Storm の例](https://github.com/hdinsight/hdinsight-storm-examples/tree/master/tools/zkdatatool-1.0)を参照してください。

lib フォルダーには、エクスポート/インポート操作の実装を含む .jar ファイルがあります。 bash フォルダーには、古いクラスター上の Zookeeper サーバーからデータをエクスポートし、新しいクラスター上の Zookeeper サーバーにデータをインポートして戻す方法を示すサンプル スクリプトがあります。

データをエクスポートし、インポートするには、Zookeeper ノードから [stormmeta.sh](https://github.com/hdinsight/hdinsight-storm-examples/blob/master/tools/zkdatatool-1.0/bash/stormmeta.sh) スクリプトを実行します。 スクリプトを適切な Hortonworks Data Platform (HDP) バージョンに更新します  (Microsoft では、HDInsight でこれらのスクリプトを汎用スクリプトにするよう取り組んでいます。 汎用スクリプトは、ユーザーが変更しなくてもクラスターのどのノードからでも実行できます)。

エクスポート コマンドでは、設定されている場所の Apache Hadoop 分散ファイル システム (HDFS) パス (Azure Blob Storage または Azure Data Lake Storage 内) にメタデータが書き込まれます。

### <a name="examples"></a>例

#### <a name="export-offset-metadata"></a>オフセット メタデータのエクスポート
1. SSH を使用して、チェックポイント オフセットをエクスポートする必要があるクラスター上の Zookeeper クラスターに移動します。
2. (HDP バージョン文字列の更新後) 次のコマンドを実行して、Zookeeper オフセット データを HDFS パス (/stormmetadta/zkdata) にエクスポートします。

    ```apache   
    java -cp ./*:/etc/hadoop/conf/*:/usr/hdp/2.5.1.0-56/hadoop/*:/usr/hdp/2.5.1.0-56/hadoop/lib/*:/usr/hdp/2.5.1.0-56/hadoop-hdfs/*:/usr/hdp/2.5.1.0-56/hadoop-hdfs/lib/*:/etc/failover-controller/conf/*:/etc/hadoop/* com.microsoft.storm.zkdatatool.ZkdataImporter export /eventhubspout /stormmetadata/zkdata
    ```

#### <a name="import-offset-metadata"></a>オフセット メタデータのインポート
1. SSH を使用して、チェックポイント オフセットをインポートする必要があるクラスター上の Zookeeper クラスターに移動します。
2. (HDP バージョン文字列の更新後) 次のコマンドを実行して、Zookeeper オフセット データを HDFS パス (/stormmetadata/zkdata) からターゲット クラスターの Zookeeper サーバーにインポートします。

    ```apache
    java -cp ./*:/etc/hadoop/conf/*:/usr/hdp/2.5.1.0-56/hadoop/*:/usr/hdp/2.5.1.0-56/hadoop/lib/*:/usr/hdp/2.5.1.0-56/hadoop-hdfs/*:/usr/hdp/2.5.1.0-56/hadoop-hdfs/lib/*:/etc/failover-controller/conf/*:/etc/hadoop/* com.microsoft.storm.zkdatatool.ZkdataImporter import /eventhubspout /home/sshadmin/zkdata
    ```
   
#### <a name="delete-offset-metadata-so-that-topologies-can-start-processing-data-from-the-beginning-or-from-a-timestamp-that-the-user-chooses"></a>トポロジが、先頭またはユーザーが選択したタイムスタンプからデータの処理を開始できるように、オフセット メタデータを削除します。
1. SSH を使用して、チェックポイント オフセットを削除する必要があるクラスター上の Zookeeper クラスターに移動します。
2. (HDP バージョン文字列の更新後) 次のコマンドを実行して、現在のクラスターのすべての Zookeeper オフセット データを削除します。

    ```apache
    java -cp ./*:/etc/hadoop/conf/*:/usr/hdp/2.5.1.0-56/hadoop/*:/usr/hdp/2.5.1.0-56/hadoop/lib/*:/usr/hdp/2.5.1.0-56/hadoop-hdfs/*:/usr/hdp/2.5.1.0-56/hadoop-hdfs/lib/*:/etc/failover-controller/conf/*:/etc/hadoop/* com.microsoft.storm.zkdatatool.ZkdataImporter delete /eventhubspout
    ```

## <a name="how-do-i-locate-storm-binaries-on-a-cluster"></a>クラスターで Storm バイナリを見つける方法
現在の HDP スタックの Storm バイナリは、/usr/hdp/current/storm-client にあります。 ヘッド ノードとワーカー ノードでこの場所は同じです。
 
/usr/hdp には、特定の HDP バージョンの複数のバイナリが存在する場合があります (例: /usr/hdp/2.5.0.1233/storm)。 /usr/hdp/current/storm-client フォルダーは、クラスターで実行されている最新バージョンにシンボリック リンクされます。

詳しくは、[SSH を使用した HDInsight クラスターへの接続](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-linux-use-ssh-unix)に関する記事および [Apache Storm](https://storm.apache.org/) のページをご覧ください。
 
## <a name="how-do-i-determine-the-deployment-topology-of-a-storm-cluster"></a>Storm クラスターのデプロイ トポロジを特定する方法
まず、HDInsight Storm でインストールされているすべてのコンポーネントを特定します。 Storm クラスターは、次の 4 つのノード カテゴリで構成されます。

* ゲートウェイ ノード
* ヘッド ノード
* Zookeeper ノード
* ワーカー ノード
 
### <a name="gateway-nodes"></a>ゲートウェイ ノード
ゲートウェイ ノードは、アクティブな Ambari 管理サービスへのパブリック アクセスを可能にするゲートウェイであり、リバース プロキシ サービスです。 また、Ambari リーダーの選択も処理します。
 
### <a name="head-nodes"></a>ヘッド ノード
Storm ヘッド ノードは、次のサービスを実行します。
* Nimbus
* Ambari サーバー
* Ambari メトリック サーバー
* Ambari メトリック コレクター
 
### <a name="zookeeper-nodes"></a>Zookeeper ノード
HDInsight には、3 ノードの Zookeeper クォーラムがあります。 クォーラム サイズは固定されており、再構成することはできません。
 
クラスターの Storm サービスは、Zookeeper クォーラムを自動的に使用するように構成されています。
 
### <a name="worker-nodes"></a>ワーカー ノード
Storm ワーカー ノードは、次のサービスを実行します。
* Supervisor
* トポロジを実行するためのワーカーの Java 仮想マシン (JVM)
* Ambari エージェント
 
## <a name="how-do-i-locate-storm-event-hub-spout-binaries-for-development"></a>開発用に Storm イベント ハブ スパウト バイナリを見つける方法
 
トポロジで Storm イベント ハブ スパウト .jar ファイルを使用する方法の詳細については、以下のリソースを参照してください。
 
### <a name="java-based-topology"></a>Java ベースのトポロジ
[HDInsight で Apache Storm を使用して Azure Event Hubs のイベントを処理する (Java)](https://docs.microsoft.com/azure/hdinsight/hdinsight-storm-develop-java-event-hub-topology)
 
### <a name="c-based-topology-mono-on-hdinsight-34-linux-storm-clusters"></a>C# ベースのトポロジ (HDInsight 3.4 以降の Linux Storm クラスター上の Mono)
[HDInsight 上の Apache Storm で Azure Event Hubs からのイベントを処理する (C#)](https://docs.microsoft.com/azure/hdinsight/hdinsight-storm-develop-csharp-event-hub-topology)
 
### <a name="latest-apache-storm-event-hub-spout-binaries-for-hdinsight-35-linux-storm-clusters"></a>HDInsight 3.5 以降の Linux Storm クラスター用の最新の Apache Storm イベント ハブ スパウト バイナリ
HDInsight 3.5 以降の Linux Storm クラスターで動作する最新の Storm イベント ハブ スパウトの使用方法については、mvn-repo の [readme ファイル](https://github.com/hdinsight/mvn-repo/blob/master/README.md)をご覧ください。
 
### <a name="source-code-examples"></a>ソース コードの例
Azure HDInsight クラスターで (Java で記述された) Apache Storm トポロジを使用して、Azure Event Hubs との間で読み取り/書き込みを実行する方法の[例](https://github.com/Azure-Samples/hdinsight-java-storm-eventhub)を参照してください。
 
## <a name="how-do-i-locate-storm-log4j-2-configuration-files-on-clusters"></a>クラスターで Storm Log4J 2 構成ファイルを見つける方法
 
Storm サービスの [Apache Log4j 2](https://logging.apache.org/log4j/2.x/) 構成ファイルを特定します。
 
### <a name="on-head-nodes"></a>ヘッド ノードの場合
Nimbus の Log4J 構成は、/usr/hdp/\<HDP バージョン\>/storm/log4j2/cluster.xml から読み取られます。
 
### <a name="on-worker-nodes"></a>ワーカー ノードの場合
スーパーバイザーの Log4J 構成は、/usr/hdp/\<HDP バージョン\>/storm/log4j2/cluster.xml から読み取られます。
 
ワーカーの Log4J 構成ファイルは、/usr/hdp/\<HDP バージョン\>/storm/log4j2/cluster.xml から読み取られます。
 
例: /usr/hdp/2.6.0.2-76/storm/log4j2/cluster.xml /usr/hdp/2.6.0.2-76/storm/log4j2/worker.xml

### <a name="see-also"></a>関連項目
[Azure HDInsight を使用したトラブルシューティング](../../hdinsight/hdinsight-troubleshoot-guide.md)
