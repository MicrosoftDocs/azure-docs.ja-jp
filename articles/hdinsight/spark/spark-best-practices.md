---
title: Azure HDInsight での Apache Spark のガイドライン
description: Azure HDInsight で Apache Spark を使用する際のガイドラインを紹介します。
ms.service: hdinsight
ms.topic: conceptual
ms.date: 04/28/2020
ms.openlocfilehash: f81435abbedec25cb8abe0b958065a493c15ce4c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98938808"
---
# <a name="apache-spark-guidelines"></a>Apache Spark のガイドライン

この記事では、Azure HDInsight で Apache Spark を使用する際のさまざまなガイドラインを紹介します。

## <a name="how-do-i-run-or-submit-spark-jobs"></a>Spark ジョブを実行または送信する方法

| オプション | Documents |
|---|---|
| VS Code | [Spark & Hive Tools for Visual Studio Code を使用する](../hdinsight-for-vscode.md) |
| Jupyter Notebooks | [チュートリアル:Azure HDInsight での Apache Spark クラスターへのデータの読み込みとクエリの実行](./apache-spark-load-data-run-query.md) |
| IntelliJ | [チュートリアル:Azure Toolkit for IntelliJ を使用して HDInsight クラスター向けの Apache Spark アプリケーションを作成する](./apache-spark-intellij-tool-plugin.md) |
| IntelliJ | [チュートリアル:IntelliJ を使用した HDInsight での Apache Spark の Scala Maven アプリケーションの作成](./apache-spark-create-standalone-application.md) |
| Zeppelin Notebook | [Azure HDInsight 上の Apache Spark クラスターで Apache Zeppelin Notebook を使用する](./apache-spark-zeppelin-notebook.md) |
| Livy を使用したリモート ジョブの送信 | [Apache Spark REST API を使用してリモート ジョブを HDInsight Spark クラスターに送信する](./apache-spark-livy-rest-interface.md) |
|[Apache Oozie](../hdinsight-use-oozie-linux-mac.md)|Oozie は Hadoop ジョブを管理するワークフローおよび調整システムです。|
|[Apache Livy](./apache-spark-livy-rest-interface.md)|Livy を使用すると、対話型の Spark シェルを実行したり、Spark で実行されるバッチ ジョブを送信したりすることができます。|
|[Apache Spark 向け Azure Data Factory](../../data-factory/transform-data-using-spark.md)|Data Factory パイプラインの Spark アクティビティでは、独自のまたはオンデマンドの HDInsight クラスターで Spark プログラムを実行します。|
|[Apache Hive 向け Azure Data Factory](../../data-factory/transform-data-using-hadoop-hive.md)|Data Factory パイプラインの HDInsight Hive アクティビティでは、独自またはオンデマンドの HDInsight クラスターで Hive クエリを実行します。|

## <a name="how-do-i-monitor-and-debug-spark-jobs"></a>Spark ジョブを監視およびデバッグする方法

| オプション | Documents |
|---|---|
| Azure Toolkit for IntelliJ | [Azure Toolkit for IntelliJ を使用した失敗した Spark ジョブのデバッグ (プレビュー)](apache-spark-intellij-tool-failure-debug.md) |
| SSH 経由の Azure Toolkit for IntelliJ | [ローカルまたはリモートから SSH 経由で Azure Toolkit for IntelliJ を使用して HDInsight クラスター上の Apache Spark アプリケーションをデバッグする](apache-spark-intellij-tool-debug-remotely-through-ssh.md) |
| VPN 経由の Azure Toolkit for IntelliJ | [Azure Toolkit for IntelliJ を使用して HDInsight 上で VPN を介して Apache Spark アプリケーションをリモートでデバッグする](apache-spark-intellij-tool-plugin-debug-jobs-remotely.md) |
| Apache Spark History Server のジョブ グラフ | [拡張された Apache Spark History Server を使用して Apache Spark アプリケーションのデバッグと診断を行う](./apache-azure-spark-history-server.md) |

## <a name="how-do-i-make-my-spark-jobs-run-more-efficiently"></a>Spark ジョブの実行効率を高める方法

| オプション | Documents |
|---|---|
| IO キャッシュ | [Azure HDInsight IO キャッシュ (プレビュー) を使用して Apache Spark のワークロードのパフォーマンスを改善する](./apache-spark-improve-performance-iocache.md) |
| 構成オプション | [Apache Spark ジョブを最適化する](./apache-spark-perf.md) |

## <a name="how-do-i-connect-to-other-azure-services"></a>他の Azure サービスに接続する方法

| オプション | Documents |
|---|---|
| HDInsight 上の Apache Hive | [Hive Warehouse Connector を使用して Apache Spark と Apache Hive を統合する](../interactive-query/apache-hive-warehouse-connector.md) |
| HDInsight での Apache HBase | [Apache Spark を使用した Apache HBase データの読み取り/書き込み](../hdinsight-using-spark-query-hbase.md) |
| HDInsight での Apache Kafka | [チュートリアル:HDInsight で Apache Kafka による Apache Spark 構造化ストリーミングを使用する](../hdinsight-apache-kafka-spark-structured-streaming.md) |
| Azure Cosmos DB | [Azure Synapse Link for Azure Cosmos DB](../../cosmos-db/synapse-link.md) |

## <a name="what-are-my-storage-options"></a>ストレージ オプションについて

| オプション | Documents |
|---|---|
| Azure Data Lake Storage Gen2 | [Azure HDInsight クラスターで Azure Data Lake Storage Gen2 を使用する](../hdinsight-hadoop-use-data-lake-storage-gen2.md) |
| Azure Data Lake Storage Gen1 | [Azure HDInsight クラスターで Azure Data Lake Storage Gen1 を使用する](../hdinsight-hadoop-use-data-lake-storage-gen1.md) |
| Azure Blob Storage | [Azure HDInsight クラスターで Azure Storage を使用する](../hdinsight-hadoop-use-blob-storage.md) |

## <a name="next-steps"></a>次のステップ

* [Apache Spark の設定を構成する](apache-spark-settings.md)
* [HDInsight で Apache Spark ジョブを最適化する](apache-spark-perf.md)
