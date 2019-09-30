---
title: Azure HDInsight 上の Apache Spark のベスト プラクティス
description: Azure HDInsight 上で Apache Spark を使用する際のベスト プラクティスを紹介します。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 09/18/2019
ms.openlocfilehash: 20033e52e862f086d1491c06d38cdf4f2c57ba8d
ms.sourcegitcommit: 1c9858eef5557a864a769c0a386d3c36ffc93ce4
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/18/2019
ms.locfileid: "71106883"
---
# <a name="apache-spark-best-practices"></a>Apache Spark のベスト プラクティス

この記事では、Azure HDInsight 上で Apache Spark を使用する際のさまざまなベスト プラクティスを紹介します。

## <a name="how-do-i-run-or-submit-spark-jobs"></a>Spark ジョブを実行または送信する方法

| オプション | Documents |
|---|---|
| VS Code | [Spark & Hive Tools for Visual Studio Code を使用する](../hdinsight-for-vscode.md) |
| Jupyter Notebooks | [チュートリアル:Azure HDInsight での Apache Spark クラスターへのデータの読み込みとクエリの実行](./apache-spark-load-data-run-query.md) |
| IntelliJ | [チュートリアル:Azure Toolkit for IntelliJ を使用して HDInsight クラスター向けの Apache Spark アプリケーションを作成する](./apache-spark-intellij-tool-plugin.md) |
| IntelliJ | [チュートリアル:IntelliJ を使用した HDInsight での Apache Spark の Scala Maven アプリケーションの作成](./apache-spark-create-standalone-application.md) |
| Zeppelin Notebook | [Azure HDInsight 上の Apache Spark クラスターで Apache Zeppelin Notebook を使用する](./apache-spark-zeppelin-notebook.md) |
| Livy を使用したリモート ジョブの送信 | [Apache Spark REST API を使用してリモート ジョブを HDInsight Spark クラスターに送信する](./apache-spark-livy-rest-interface.md) |

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
| Azure Cosmos DB | [Azure Cosmos DB:Azure プラットフォームでラムダ アーキテクチャを実装する](../../cosmos-db/lambda-architecture.md) |

## <a name="what-are-my-storage-options"></a>ストレージ オプションについて

| オプション | Documents |
|---|---|
| Data Lake Storage Gen2 | [Azure HDInsight クラスターで Azure Data Lake Storage Gen2 を使用する](../hdinsight-hadoop-use-data-lake-storage-gen2.md) |
| Data Lake Storage Gen1 | [Azure HDInsight クラスターで Data Lake Storage Gen1 を使用する](../hdinsight-hadoop-use-data-lake-store.md) |
| Azure Blob Storage | [Azure HDInsight クラスターで Azure Storage を使用する](../hdinsight-hadoop-use-blob-storage.md) |

## <a name="next-steps"></a>次の手順

* [Apache Spark の設定を構成する](apache-spark-settings.md)
* [HDInsight で Apache Spark ジョブを最適化する](apache-spark-perf.md)
