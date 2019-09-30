---
title: Azure HDInsight で Apache HBase Master を開始できない
description: Azure HDInsight で Apache HBase Master (HMaster) を開始できない
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 08/14/2019
ms.openlocfilehash: d994fe1501dedf6a8ea2c3366f6559c7abac0892
ms.sourcegitcommit: c79aa93d87d4db04ecc4e3eb68a75b349448cd17
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/18/2019
ms.locfileid: "71091614"
---
# <a name="apache-hbase-master-hmaster-fails-to-start-in-azure-hdinsight"></a>Azure HDInsight で Apache HBase Master (HMaster) を開始できない

この記事では、Azure HDInsight クラスターと対話するときの問題のトラブルシューティング手順と可能な解決策について説明します。

## <a name="scenario-atomic-renaming-failure"></a>シナリオ: Atomic renaming failure (アトミックな名前変更の失敗)

### <a name="issue"></a>問題

スタートアップ プロセス中に予期しないファイルが識別されました。

### <a name="cause"></a>原因

HMaster は、起動プロセス中、データをスクラッチ (.tmp) フォルダーからデータ フォルダーに移動するなど、多くの初期化手順を実行します。 HMaster では、先書きログ (WAL) フォルダーを調べて、応答していないリージョン サーバーが存在するかどうかも確認されます。

HMaster では、WAL フォルダーに対して基本的な list コマンドが実行されます。 いずれかのフォルダーに予期しないファイルが見つかると、例外がスローされ、HMaster は起動しません。

### <a name="resolution"></a>解決策

呼び出し履歴を確認し、問題の原因となっているフォルダーを特定します (たとえば、WAL フォルダーや .tmp フォルダーが原因の場合があります)。 次に、Cloud Explorer または HDFS コマンドを使用して問題のファイルを特定します。 通常、これは `*-renamePending.json` ファイルです (`*-renamePending.json` ファイルは、WASB ドライバーでアトミックな名前変更操作を実装するために使用されるジャーナル ファイルです。 この実装のバグが原因で、プロセスのクラッシュ後にこれらのファイルが残されることがあります)。Cloud Explorer または HDFS コマンドを使用して、このファイルを強制的に削除します。

この場所に `$$$.$$$` のような名前の一時ファイルが存在する場合もあります。 このファイルを表示するには、HDFS `ls` コマンドを使用する必要があります。Cloud Explorer で表示することはできません。 このファイルを削除するには、HDFS コマンド `hdfs dfs -rm /\<path>\/\$\$\$.\$\$\$` を使用します。

これらのコマンドを実行したら、HMaster はすぐに起動します。

---

## <a name="scenario-no-server-address-listed"></a>シナリオ: サーバー アドレスがリストされない

### <a name="issue"></a>問題

`hbase: meta` テーブルがオンラインになっていないことを示すメッセージが表示される場合があります。 `hbck` を実行すると、`hbase: meta table replicaId 0 is not found on any region.` であることが報告される場合があります。HMaster ログに、次のメッセージが表示されることがあります`No server address listed in hbase: meta for region hbase: backup <region name>`。  

### <a name="cause"></a>原因

HBase を再起動した後、HMaster を初期化できませんでした。

### <a name="resolution"></a>解決策

1. HBase シェルで次のコマンドを入力します (適宜、実際の値に置き換えてください)。

    ```hbase
    scan 'hbase:meta'
    delete 'hbase:meta','hbase:backup <region name>','<column name>'
    ```

1. `hbase: namespace` エントリを削除します。 このエントリがあると、`hbase: namespace` テーブルをスキャンしたときに同じエラーが報告されます。

1. Ambari UI からアクティブ HMaster を再起動し、HBase を実行状態にします。

1. HBase シェルで次のコマンドを実行して、すべてのオフライン テーブルを起動します。

    ```hbase
    hbase hbck -ignorePreCheckPermission -fixAssignments
    ```

---

## <a name="scenario-javaioioexception-timedout"></a>シナリオ: java.io.IOException:Timedout

### <a name="issue"></a>問題

HMaster は、`java.io.IOException: Timedout 300000ms waiting for namespace table to be assigned` のような致命的例外によってタイムアウトになりました。

### <a name="cause"></a>原因

この問題は、HMaster サービスを再起動したときに、フラッシュされていない多数のテーブルとリージョンが存在する場合に発生します。 タイムアウトは、HMaster の既知の欠陥です。 一般的なクラスター スタートアップ タスクは時間がかかることがあります。 namespace テーブルがまだ割り当てられていない場合、HMaster はシャットダウンします。 時間のかかるスタートアップ タスクが発生するのは、フラッシュされていないデータが大量に存在し、5 分のタイムアウトでは間に合わない場合です。

### <a name="resolution"></a>解決策

1. Apache Ambari UI 内で、 **[HBase]**  >  **[Configs]\(構成\)** に移動します。 カスタム `hbase-site.xml` ファイルに、次の設定を追加します。

    ```
    Key: hbase.master.namespace.init.timeout Value: 2400000  
    ```

1. 必要なサービス (HMaster と、場合によっては他の HBase サービス) を再起動します。

---

## <a name="scenario-frequent-region-server-restarts"></a>シナリオ: リージョン サーバーの頻繁な再起動

### <a name="issue"></a>問題

ノードが定期的に再起動します。 リージョン サーバー ログに、次のようなエントリが表示される場合があります。

```
2017-05-09 17:45:07,683 WARN  [JvmPauseMonitor] util.JvmPauseMonitor: Detected pause in JVM or host machine (eg GC): pause of approximately 31000ms
2017-05-09 17:45:07,683 WARN  [JvmPauseMonitor] util.JvmPauseMonitor: Detected pause in JVM or host machine (eg GC): pause of approximately 31000ms
2017-05-09 17:45:07,683 WARN  [JvmPauseMonitor] util.JvmPauseMonitor: Detected pause in JVM or host machine (eg GC): pause of approximately 31000ms
```

### <a name="cause"></a>原因

`regionserver` JVM GC の長時間の一時停止が発生しています。 この一時停止により、`regionserver` が応答しなくなり、zk セッション タイムアウトの 40 秒以内に HMaster にハートビートを送信できなくなります。 HMaster では、`regionserver` が停止しているとみなされ、`regionserver` の中止と再起動が行われます。

### <a name="resolution"></a>解決策

Zookeeper のセッション タイムアウトを変更します。`hbase-site` サイトの設定 `zookeeper.session.timeout` だけでなく、Zookeeper の `zoo.cfg` 設定 `maxSessionTimeout` も変更する必要があります。

1. Ambari UI にアクセスし、 **[HBase] -> [Configs]\(構成\) -> [Settings]\(設定\)** に移動し、[Timeouts]\(タイムアウト\) セクションで Zookeeper セッション タイムアウトの値を変更します。

1. Ambari UI にアクセスし、 **[Zookeeper] -> [Configs]\(構成\) -> [Custom]\(カスタム\)** `zoo.cfg` に移動し、次の設定を追加/変更します。 値が HBase の `zookeeper.session.timeout` と同じであることを確認します。

    ```
    Key: maxSessionTimeout Value: 120000  
    ```

1. 必要なサービスを再起動します。

---

## <a name="scenario-log-splitting-failure"></a>シナリオ: ログの分割エラー

### <a name="issue"></a>問題

HMasters が HBase クラスターで起動できませんでした。

### <a name="cause"></a>原因

セカンダリ ストレージ アカウントに対して HDFS と HBase の設定が正しく構成されていません。

### <a name="resolution"></a>解決策

hbase.rootdir: wasb://@.blob.core.windows.net/hbase を設定し、Ambari でサービスを再起動します。

---

## <a name="next-steps"></a>次の手順

問題がわからなかった場合、または問題を解決できない場合は、次のいずれかのチャネルでサポートを受けてください。

* [Azure コミュニティのサポート](https://azure.microsoft.com/support/community/)を通じて Azure エキスパートから回答を得る。

* [@AzureSupport](https://twitter.com/azuresupport) (カスタマー エクスペリエンスを向上させるための Microsoft Azure の公式アカウント) に連絡する。 Azure コミュニティで適切なリソース (回答、サポート、エキスパートなど) につながる。

* さらにヘルプが必要な場合は、[Azure portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/) からサポート リクエストを送信できます。 メニュー バーから **[サポート]** を選択するか、 **[ヘルプとサポート]** ハブを開いてください。 詳細については、「[Azure サポート要求を作成する方法](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request)」を参照してください。 サブスクリプション管理と課金サポートへのアクセスは、Microsoft Azure サブスクリプションに含まれていますが、テクニカル サポートはいずれかの [Azure のサポート プラン](https://azure.microsoft.com/support/plans/)を通して提供されます。
