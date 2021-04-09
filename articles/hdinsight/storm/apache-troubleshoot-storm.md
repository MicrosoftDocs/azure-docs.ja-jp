---
title: Azure HDInsight を使用した Storm のトラブルシューティング
description: Azure HDInsight での Apache Storm の使用についてよく寄せられる質問とその回答を示します。
keywords: Azure HDInsight, Storm, FAQ, トラブルシューティング ガイド, よくある質問
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 11/08/2019
ms.custom: seodec18
ms.openlocfilehash: c81084c77b355a5d60c72564c58a98e08da14312
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98946304"
---
# <a name="troubleshoot-apache-storm-by-using-azure-hdinsight"></a>Azure HDInsight を使用した Apache Storm のトラブルシューティング

[Apache Ambari](https://ambari.apache.org/) で [Apache Storm](https://storm.apache.org/) ペイロードを操作するときに発生する主な問題とその解決策について説明します。

## <a name="how-do-i-access-the-storm-ui-on-a-cluster"></a>クラスターの Storm UI にアクセスする方法

ブラウザーから Storm UI にアクセスする場合、2 つの方法があります。

### <a name="apache-ambari-ui"></a>Apache Ambari UI

1. Ambari ダッシュボードに移動します。
2. サービスの一覧で **[Storm]** を選択します。
3. **[クイック リンク]** メニューの **[Storm UI]** を選択します。

### <a name="direct-link"></a>直接リンク

次の URL で Storm UI にアクセスできます。

`https://<cluster DNS name>/stormui`

例: `https://stormcluster.azurehdinsight.net/stormui`

## <a name="how-do-i-transfer-storm-event-hub-spout-checkpoint-information-from-one-topology-to-another"></a>Storm イベント ハブ スパウトのチェックポイント情報をトポロジ間で転送する方法

HDInsight Storm イベント ハブ スパウト .jar ファイルを使用して Azure Event Hubs から読み取るトポロジを開発するときは、同じ名前のトポロジを新しいクラスターにデプロイする必要があります。 ただし、以前のクラスターで [Apache ZooKeeper](https://zookeeper.apache.org/) にコミットされていたチェックポイント データを保持する必要があります。

### <a name="where-checkpoint-data-is-stored"></a>チェックポイント データの保存場所

オフセットのチェックポイント データは、イベント ハブによって Zookeeper の次の 2 つのルート パスに保存されます。

- 非トランザクション スパウトのチェックポイントは `/eventhubspout` に格納されます。

- トランザクション スパウトのチェックポイント データは `/transactional` に格納されます。

### <a name="how-to-restore"></a>復元方法

データを Zookeeper からエクスポートし、新しい名前で Zookeeper にインポートして戻すときに使用するスクリプトとライブラリを入手するには、[HDInsight Storm の例](https://github.com/hdinsight/hdinsight-storm-examples/tree/master/tools/zkdatatool-1.0)を参照してください。

lib フォルダーには、エクスポート/インポート操作の実装を含む .jar ファイルがあります。 bash フォルダーには、古いクラスター上の Zookeeper サーバーからデータをエクスポートし、新しいクラスター上の Zookeeper サーバーにデータをインポートして戻す方法を示すサンプル スクリプトがあります。

データをエクスポートし、インポートするには、Zookeeper ノードから [stormmeta.sh](https://github.com/hdinsight/hdinsight-storm-examples/blob/master/tools/zkdatatool-1.0/bash/stormmeta.sh) スクリプトを実行します。 スクリプトを適切な Hortonworks Data Platform (HDP) バージョンに更新します  (Microsoft では、HDInsight でこれらのスクリプトを汎用スクリプトにするよう取り組んでいます。 汎用スクリプトは、ユーザーが変更しなくてもクラスターのどのノードからでも実行できます)。

エクスポート コマンドでは、設定されている場所の Apache Hadoop 分散ファイル システム (HDFS) パス (Azure Blob Storage または Azure Data Lake Storage 内) にメタデータが書き込まれます。

### <a name="examples"></a>例

#### <a name="export-offset-metadata"></a>オフセット メタデータのエクスポート

1. SSH を使用して、チェックポイント オフセットをエクスポートする必要があるクラスター上の Zookeeper クラスターに移動します。
2. (HDP バージョン文字列を更新した後) 次のコマンドを実行して、ZooKeeper オフセット データを HDFS パス `/stormmetadta/zkdata` にエクスポートします。

    ```apache
    java -cp ./*:/etc/hadoop/conf/*:/usr/hdp/2.5.1.0-56/hadoop/*:/usr/hdp/2.5.1.0-56/hadoop/lib/*:/usr/hdp/2.5.1.0-56/hadoop-hdfs/*:/usr/hdp/2.5.1.0-56/hadoop-hdfs/lib/*:/etc/failover-controller/conf/*:/etc/hadoop/* com.microsoft.storm.zkdatatool.ZkdataImporter export /eventhubspout /stormmetadata/zkdata
    ```

#### <a name="import-offset-metadata"></a>オフセット メタデータのインポート

1. SSH を使用して、チェックポイント オフセットをインポートする必要があるクラスター上の Zookeeper クラスターに移動します。
2. (HDP バージョン文字列を更新した後) 次のコマンドを実行して、ZooKeeper オフセット データを HDFS パス `/stormmetadata/zkdata` からターゲット クラスター上の ZooKeeper サーバーにインポートします。

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

現在の HDP スタックの Storm バイナリは `/usr/hdp/current/storm-client` にあります。 ヘッド ノードとワーカー ノードでこの場所は同じです。

/usr/hdp には、特定の HDP バージョンの複数のバイナリが存在する可能性があります (`/usr/hdp/2.5.0.1233/storm` など)。 `/usr/hdp/current/storm-client` フォルダーは、クラスター上で実行されている最新バージョンにシンボリック リンクされます。

詳しくは、[SSH を使用した HDInsight クラスターへの接続](../hdinsight-hadoop-linux-use-ssh-unix.md)に関する記事および [Apache Storm](https://storm.apache.org/) のページをご覧ください。

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

[HDInsight で Apache Storm を使用して Azure Event Hubs のイベントを処理する (Java)](https://github.com/Azure-Samples/hdinsight-java-storm-eventhub)

### <a name="c-based-topology-mono-on-hdinsight-34-linux-storm-clusters"></a>C# ベースのトポロジ (HDInsight 3.4 以降の Linux Storm クラスター上の Mono)

[HDInsight 上の Apache Storm で Azure Event Hubs からのイベントを処理する (C#)](./apache-storm-develop-csharp-event-hub-topology.md)

### <a name="latest-apache-storm-event-hub-spout-binaries-for-hdinsight-35-linux-storm-clusters"></a>HDInsight 3.5 以降の Linux Storm クラスター用の最新の Apache Storm イベント ハブ スパウト バイナリ

HDInsight 3.5 以降の Linux Storm クラスターで動作する最新の Storm イベント ハブ スパウトを使用する方法については、[mvn-repo readme ファイル](https://github.com/hdinsight/mvn-repo/blob/master/README.md)を参照してください。

### <a name="source-code-examples"></a>ソース コードの例

Azure HDInsight クラスターで (Java で記述された) Apache Storm トポロジを使用して、Azure Event Hubs との間で読み取り/書き込みを実行する方法の[例](https://github.com/Azure-Samples/hdinsight-java-storm-eventhub)を参照してください。

## <a name="how-do-i-locate-storm-log4j-2-configuration-files-on-clusters"></a>クラスターで Storm Log4J 2 構成ファイルを見つける方法

Storm サービスの [Apache Log4j 2](https://logging.apache.org/log4j/2.x/) 構成ファイルを特定します。

### <a name="on-head-nodes"></a>ヘッド ノードの場合

Nimbus Log4J 構成は、`/usr/hdp/\<HDP version>/storm/log4j2/cluster.xml` から読み取られます。

### <a name="on-worker-nodes"></a>ワーカー ノードの場合

スーパーバイザーの Log4J 構成は、`/usr/hdp/\<HDP version>/storm/log4j2/cluster.xml` から読み取られます。

ワーカーの Log4J 構成ファイルは、`/usr/hdp/\<HDP version>/storm/log4j2/worker.xml` から読み取られます。

例: `/usr/hdp/2.6.0.2-76/storm/log4j2/cluster.xml`
`/usr/hdp/2.6.0.2-76/storm/log4j2/worker.xml`

---

## <a name="not-a-leader-exception"></a>Not a leader 例外

トポロジを送信するとき、ユーザーに次のようなエラー メッセージが表示されることがあります: `Topology submission exception, cause not a leader, the current leader is NimbusInfo`。

解決するには、ユーザーはノードを再起動または再起動するためにチケットの申請が必要になる場合があります。 詳細については、「[https://community.hortonworks.com/content/supportkb/150287/error-ignoring-exception-while-trying-to-get-leade.html](https://community.hortonworks.com/content/supportkb/150287/error-ignoring-exception-while-trying-to-get-leade.html)」を参照してください。

---

## <a name="next-steps"></a>次のステップ

問題がわからなかった場合、または問題を解決できない場合は、次のいずれかのチャネルでサポートを受けてください。

- [Azure コミュニティのサポート](https://azure.microsoft.com/support/community/)を通じて Azure エキスパートから回答を得る。

- [@AzureSupport](https://twitter.com/azuresupport) (カスタマー エクスペリエンスを向上させるための Microsoft Azure の公式アカウント) に連絡する。 Azure コミュニティで適切なリソース (回答、サポート、エキスパートなど) につながる。

- さらにヘルプが必要な場合は、[Azure portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/) からサポート リクエストを送信できます。 メニュー バーから **[サポート]** を選択するか、 **[ヘルプとサポート]** ハブを開いてください。 詳細については、「[Azure サポート要求を作成する方法](../../azure-portal/supportability/how-to-create-azure-support-request.md)」を参照してください。 サブスクリプション管理と課金サポートへのアクセスは、Microsoft Azure サブスクリプションに含まれていますが、テクニカル サポートはいずれかの [Azure のサポート プラン](https://azure.microsoft.com/support/plans/)を通して提供されます。