---
title: "Azure HDInsight トラブルシューティング ガイド | Microsoft Docs"
description: "Azure HDInsight を使用して、Hadoop ワークロードのトラブルシューティングを行います。 ステップ バイ ステップ ドキュメントには、HDInsight を使用して、Hive、Spark、YARN、HBase、HDFS、Storm の一般的な問題を解決する方法が示されています。"
services: hdinsight
author: arijitt
manager: arijitt
layout: LandingPage
ms.assetid: 
ms.service: hdinsight
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: landing - page
ms.date: 7/06/2017
ms.author: arijitt
ms.openlocfilehash: f246c6d1355220314a2f56aa91634625ec352c69
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="troubleshoot-by-using-azure-hdinsight"></a>Azure HDInsight を使用したトラブルシューティング

| Apache ワークロード | よくある質問 |
|---|---|
|![HBase](./media/hdinsight-troubleshoot-guide/HBASE.png)<br>[HBase のトラブルシューティング](hdinsight-troubleshoot-hbase.md)|<br>[複数の未割り当てリージョンで hbck コマンド レポートを実行する方法](hdinsight-troubleshoot-hbase.md#how-do-i-run-hbck-command-reports-with-multiple-unassigned-regions)<br><br>[hbck コマンドを使用してリージョンを割り当てるときのタイムアウトの問題を解決する方法](hdinsight-troubleshoot-hbase.md#how-do-i-fix-timeout-issues-with-hbck-commands-for-region-assignments)<br><br>[クラスターで HDFS のセーフ モードを強制的に無効にする方法](hdinsight-troubleshoot-hbase.md#how-do-i-force-disable-hdfs-safe-mode-in-a-cluster)<br><br>[Apache Phoenix との JDBC 接続または SQLLine 接続の問題を解決する方法](hdinsight-troubleshoot-hbase.md#how-do-i-fix-jdbc-or-sqlline-connectivity-issues-with-apache-phoenix)<br><br>[マスター サーバーの起動が失敗する原因](hdinsight-troubleshoot-hbase.md#what-causes-a-master-server-to-fail-to-start)<br><br>[リージョン サーバーでの再起動が失敗する原因](hdinsight-troubleshoot-hbase.md#what-causes-a-restart-failure-on-a-region-server)|
|![HDFS](./media/hdinsight-troubleshoot-guide/HDFS.png)<br>[HDFS のトラブルシューティング](hdinsight-troubleshoot-hdfs.md)|<br>[クラスター内からローカルの HDFS にアクセスする方法](hdinsight-troubleshoot-hdfs.md#how-do-i-access-local-hdfs-from-inside-a-cluster)<br><br>[クラスターで HDFS のセーフ モードを強制的に無効にする方法](hdinsight-troubleshoot-hdfs.md#how-do-i-force-disable-hdfs-safe-mode-in-a-cluster)|
|![Hive](./media/hdinsight-troubleshoot-guide/HIVE.png)<br>[HBase のトラブルシューティング](hdinsight-troubleshoot-hive.md)|<br>[Hive metastore をエクスポートして別のクラスターにインポートする方法](hdinsight-troubleshoot-hive.md#how-do-i-export-a-hive-metastore-and-import-it-on-another-cluster)<br><br>[クラスターで Hive ログを見つける方法](hdinsight-troubleshoot-hive.md#how-do-i-locate-hive-logs-on-a-cluster)<br><br>[クラスターで特定の構成を使って Hive シェルを起動する方法](hdinsight-troubleshoot-hive.md#how-do-i-launch-the-hive-shell-with-specific-configurations-on-a-cluster)<br><br>[クラスターのクリティカル パスで Tez DAG データを分析する方法](hdinsight-troubleshoot-hive.md#how-do-i-analyze-tez-dag-data-on-a-cluster-critical-path)<br><br>[クラスターから Tez DAG データをダウンロードする方法](hdinsight-troubleshoot-hive.md#how-do-i-download-tez-dag-data-from-a-cluster)|
|![Spark](./media/hdinsight-troubleshoot-guide/SPARK.png)<br>[Spark のトラブルシューティング](hdinsight-troubleshoot-SPARK.md)|<br>[クラスターで Ambari を使用して Spark アプリケーションを構成する方法](hdinsight-troubleshoot-spark.md#how-do-i-configure-a-spark-application-by-using-ambari-on-clusters)<br><br>[クラスターで Jupyter Notebook を使用して Spark アプリケーションを構成する方法](hdinsight-troubleshoot-spark.md#how-do-i-configure-a-spark-application-by-using-a-jupyter-notebook-on-clusters)<br><br>[クラスターで Livy を使用して Spark アプリケーションを構成する方法](hdinsight-troubleshoot-spark.md#how-do-i-configure-a-spark-application-by-using-livy-on-clusters)<br><br>[クラスターで spark-submit を使用して Spark アプリケーションを構成する方法](hdinsight-troubleshoot-spark.md#how-do-i-configure-a-spark-application-by-using-spark-submit-on-clusters)<br><br>[Spark アプリケーションの OutOfMemoryError 例外の原因は何ですか](hdinsight-troubleshoot-spark.md#what-causes-a-spark-application-outofmemoryerror-exception)|
|![Storm](./media/hdinsight-troubleshoot-guide/STORM.png)<br>[Storm のトラブルシューティング](hdinsight-troubleshoot-STORM.md)|<br>[クラスターの Storm UI にアクセスする方法](hdinsight-troubleshoot-storm.md#how-do-i-access-the-storm-ui-on-a-cluster)<br><br>[Storm イベント ハブ スパウトのチェックポイント情報をトポロジ間で転送する方法](hdinsight-troubleshoot-storm.md#how-do-i-transfer-storm-event-hub-spout-checkpoint-information-from-one-topology-to-another)<br><br>[クラスターで Storm バイナリを見つける方法](hdinsight-troubleshoot-storm.md#how-do-i-locate-storm-binaries-on-a-cluster)<br><br>[Storm クラスターのデプロイ トポロジを特定する方法](hdinsight-troubleshoot-storm.md#how-do-i-determine-the-deployment-topology-of-a-storm-cluster)<br><br>[開発用に Storm イベント ハブ スパウト バイナリを見つける方法](hdinsight-troubleshoot-storm.md#how-do-i-locate-storm-event-hub-spout-binaries-for-development)<br><br>[クラスターで Storm Log4J 構成ファイルを見つける方法](hdinsight-troubleshoot-storm.md#how-do-i-locate-storm-log4j-configuration-files-on-clusters)|
|![YARN](./media/hdinsight-troubleshoot-guide/YARN.png)<br>[YARN のトラブルシューティング](hdinsight-troubleshoot-YARN.md)|<br>[クラスターで新しい YARN キューを作成する方法](hdinsight-troubleshoot-yarn.md#how-do-i-create-a-new-yarn-queue-on-a-cluster)<br><br>[クラスターから YARN ログをダウンロードする方法](hdinsight-troubleshoot-yarn.md#how-do-i-download-yarn-logs-from-a-cluster)|

## <a name="hdinsight-troubleshooting-resources"></a>HDInsight のトラブルシューティング リソース

| 対象 | 参照先 |
| --- | --- |
| Linux 上の HDInsight と最適化 | - [Linux での HDInsight の使用方法](hdinsight-hadoop-linux-information.md)<br>- [Hadoop のメモリとパフォーマンスのトラブルシューティング](hdinsight-hadoop-stack-trace-error-messages.md)<br>- [Hive クエリのパフォーマンス](https://blogs.msdn.microsoft.com/bigdatasupport/2015/08/13/troubleshooting-hive-query-performance-in-hdinsight-hadoop-cluster/) |
| ログとダンプ | - [Linux での Hadoop YARN アプリケーション ログへのアクセス](hdinsight-hadoop-access-yarn-app-logs-linux.md)<br>- [Linux での Hadoop サービスのヒープ ダンプの有効化](hdinsight-hadoop-collect-debug-heap-dump-linux.md)<br>- [HDInsight ログの分析](hdinsight-debug-jobs.md)|
| Errors | - [WebHCat エラーの説明と解決策](hdinsight-hadoop-templeton-webhcat-debug-errors.md)<br>- [OutofMemory エラーを解決するための Hive 設定](hdinsight-hadoop-hive-out-of-memory-error-oom.md) |
| ツール | - [Ambari ビューを使用した Tez ジョブのデバッグ](hdinsight-debug-ambari-tez-view.md)<br>- [Hive クエリの最適化](hdinsight-hadoop-optimize-hive-query.md) |
