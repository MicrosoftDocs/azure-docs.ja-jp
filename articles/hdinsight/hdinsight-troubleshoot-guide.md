---
title: Azure HDInsight トラブルシューティング ガイド
description: Azure HDInsight のトラブルシューティング。 ステップ バイ ステップ ドキュメントには、HDInsight を使用して、Apache Hive、Apache Spark、Apache YARN、Apache HBase、HDFS、Apache Storm の一般的な問題を解決する方法が示されています。
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 08/14/2019
ms.openlocfilehash: e36388fb49ebbcdeee8c5ed046faaf756c65bd60
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/23/2021
ms.locfileid: "104871896"
---
# <a name="troubleshoot-azure-hdinsight"></a>Azure HDInsight のトラブルシューティング

| Apache ワークロード | よくある質問 |
|---|---|
|:::image type="content" source="./media/hdinsight-troubleshoot-guide/hdinsight-apache-hbase.png" alt-text="HDInsight Apache HBase のアイコン" border="false":::<br>[Apache HBase のトラブルシューティング]()|<br>[未割り当てリージョン](hbase/hbase-troubleshoot-unassigned-regions.md#scenario-unassigned-regions)<br><br>[Azure HDInsight での "hbase hbck" コマンド使用時のタイムアウト](hbase/hbase-troubleshoot-timeouts-hbase-hbck.md)<br><br>[Azure HDInsight の Apache Phoenix の接続に関する問題](hbase/hbase-troubleshoot-phoenix-connectivity.md)<br><br>[プライマリ サーバーの起動が失敗する原因](hbase/hbase-troubleshoot-start-fails.md)<br><br>[BindException - 既に使用されているアドレス](hbase/hbase-troubleshoot-bindexception-address-use.md)|
|:::image type="content" source="./media/hdinsight-troubleshoot-guide/hdinsight-apache-hdfs.png" alt-text="HDInsight Apache HDFS のアイコン" border="false":::<br>[Apache Hadoop HDFS のトラブルシューティング](hdinsight-troubleshoot-hdfs.md)|<br>[クラスター内からローカルの HDFS にアクセスする方法](hdinsight-troubleshoot-hdfs.md#how-do-i-access-local-hdfs-from-inside-a-cluster)<br><br>[Azure HDInsight クラスター上のローカル HDFS がセーフ モードでスタックする](hadoop/hdinsight-hdfs-troubleshoot-safe-mode.md)|
|:::image type="content" source="./media/hdinsight-troubleshoot-guide/hdinsight-apache-hive.png" alt-text="HDInsight Apache Hive のアイコン" border="false":::<br>[Apache Hive のトラブルシューティング](hdinsight-troubleshoot-hive.md)|<br>[Hive metastore をエクスポートして別のクラスターにインポートする方法](hdinsight-troubleshoot-hive.md#how-do-i-export-a-hive-metastore-and-import-it-on-another-cluster)<br><br>[クラスターにある Apache Hive のログを特定する方法](hdinsight-troubleshoot-hive.md#how-do-i-locate-hive-logs-on-a-cluster)<br><br>[クラスターで特定の構成を使って Apache Hive シェルを起動する方法](hdinsight-troubleshoot-hive.md#how-do-i-launch-the-hive-shell-with-specific-configurations-on-a-cluster)<br><br>[クラスターのクリティカル パスで Apache Tez DAG データを分析する方法](hdinsight-troubleshoot-hive.md#how-do-i-analyze-tez-dag-data-on-a-cluster-critical-path)<br><br>[クラスターから Apache Tez DAG データをダウンロードする方法](hdinsight-troubleshoot-hive.md#how-do-i-download-tez-dag-data-from-a-cluster)|
|:::image type="content" source="./media/hdinsight-troubleshoot-guide/hdinsight-apache-spark.png" alt-text="HDInsight Apache Spark のアイコン" border="false":::<br>[Apache Spark のトラブルシューティング](./spark/apache-troubleshoot-spark.md)|<br>[クラスター上の Apache Ambari を使用して Apache Spark アプリケーションを構成する方法](spark/apache-troubleshoot-spark.md#how-do-i-configure-an-apache-spark-application-by-using-apache-ambari-on-clusters)<br><br>[クラスター上の Jupyter Notebook を使用して Apache Spark アプリケーションを構成する方法](spark/apache-troubleshoot-spark.md#how-do-i-configure-an-apache-spark-application-by-using-a-jupyter-notebook-on-clusters)<br><br>[クラスター上の Apache Livy を使用して Apache Spark アプリケーションを構成する方法](spark/apache-troubleshoot-spark.md#how-do-i-configure-an-apache-spark-application-by-using-apache-livy-on-clusters)<br><br>[クラスター上の spark-submit を使用して Apache Spark アプリケーションを構成する方法](spark/apache-troubleshoot-spark.md#how-do-i-configure-an-apache-spark-application-by-using-spark-submit-on-clusters)<br><br>[IntelliJ を使用して Apache Spark アプリケーションを構成する方法](spark/apache-spark-intellij-tool-plugin.md)<br><br>[Eclipse を使用して Apache Spark アプリケーションを構成する方法](spark/apache-spark-eclipse-tool-plugin.md)<br><br>[VSCode を使用して Apache Spark アプリケーションを構成する方法](hdinsight-for-vscode.md)<br><br>[Apache Spark の OutOfMemoryError 例外](spark/apache-spark-troubleshoot-outofmemory.md#scenario-outofmemoryerror-exception-for-apache-spark)|
|:::image type="content" source="./media/hdinsight-troubleshoot-guide/hdinsight-apache-storm.png" alt-text="HDInsight Apache Storm のアイコン" border="false":::<br>[Apache Storm のトラブルシューティング](./storm/apache-troubleshoot-storm.md)|<br>[クラスターの Apache Storm UI にアクセスする方法](storm/apache-troubleshoot-storm.md#how-do-i-access-the-storm-ui-on-a-cluster)<br><br>[Apache Storm イベント ハブ スパウトのチェックポイント情報をトポロジ間で転送する方法](storm/apache-troubleshoot-storm.md#how-do-i-transfer-storm-event-hub-spout-checkpoint-information-from-one-topology-to-another)<br><br>[クラスターで Storm バイナリを見つける方法](storm/apache-troubleshoot-storm.md#how-do-i-locate-storm-binaries-on-a-cluster)<br><br>[Storm クラスターのデプロイ トポロジを特定する方法](storm/apache-troubleshoot-storm.md#how-do-i-determine-the-deployment-topology-of-a-storm-cluster)<br><br>[開発用に Apache Storm イベント ハブ スパウト バイナリを見つける方法](storm/apache-troubleshoot-storm.md#how-do-i-locate-storm-event-hub-spout-binaries-for-development)|
|:::image type="content" source="./media/hdinsight-troubleshoot-guide/hdinsight-apache-yarn.png" alt-text="HDInsight Apache YARN のアイコン" border="false":::<br>[Apache Hadoop YARN のトラブルシューティング](hdinsight-troubleshoot-YARN.md)|<br>[クラスターで新しい Apache Hadoop YARN キューを作成する方法](hdinsight-troubleshoot-yarn.md#how-do-i-create-a-new-yarn-queue-on-a-cluster)<br><br>[クラスターから Apache Hadoop YARN ログをダウンロードする方法](hdinsight-troubleshoot-yarn.md#how-do-i-download-yarn-logs-from-a-cluster)|

## <a name="hdinsight-troubleshooting-resources"></a>HDInsight のトラブルシューティング リソース

| 対象 | 参照先 |
| --- | --- |
| Linux 上の HDInsight と最適化 | - [Linux での HDInsight の使用方法](hdinsight-hadoop-linux-information.md)<br>- [Apache Hadoop のメモリとパフォーマンスのトラブルシューティング](hdinsight-hadoop-stack-trace-error-messages.md)<br>- [Apache Hive クエリ パフォーマンスの向上](https://web.archive.org/web/20190217214250/https://blogs.msdn.microsoft.com/bigdatasupport/2015/08/13/troubleshooting-hive-query-performance-in-hdinsight-hadoop-cluster/) |
| ログとダンプ | - [Linux での Apache Hadoop YARN アプリケーション ログへのアクセス](hdinsight-hadoop-access-yarn-app-logs-linux.md)<br>- [Linux での Apache Hadoop サービスのヒープ ダンプの有効化](hdinsight-hadoop-collect-debug-heap-dump-linux.md)|
| エラー | - [WebHCat エラーの説明と解決策](hdinsight-hadoop-templeton-webhcat-debug-errors.md)<br>- [OutofMemory エラーを解決するための Apache Hive 設定](hdinsight-hadoop-hive-out-of-memory-error-oom.md) |
| ツール | - [Apache Hive クエリの最適化](hdinsight-hadoop-optimize-hive-query.md)<br>- [HDInsight IntelliJ ツール](./spark/apache-spark-intellij-tool-plugin.md)<br>- [HDInsight Eclipse ツール](./spark/apache-spark-eclipse-tool-plugin.md)<br>- [HDInsight VSCode ツール](hdinsight-for-vscode.md)<br>- [HDInsight Visual Studio ツール](./hadoop/apache-hadoop-visual-studio-tools-get-started.md) |

## <a name="next-steps"></a>次のステップ

[!INCLUDE [troubleshooting next steps](../../includes/hdinsight-troubleshooting-next-steps.md)]
