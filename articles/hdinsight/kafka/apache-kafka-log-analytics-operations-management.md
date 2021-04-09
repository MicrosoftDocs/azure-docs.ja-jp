---
title: Apache Kafka の Azure Monitor ログ - Azure HDInsight
description: Azure Monitor ログを使用して、Azure HDInsight 上の Apache Kafka クラスターからログを分析します。
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 02/17/2020
ms.openlocfilehash: 74db121c33864ee72ad984b49c8fa43afa0f598c
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/23/2021
ms.locfileid: "104870519"
---
# <a name="analyze-logs-for-apache-kafka-on-hdinsight"></a>HDInsight で Apache Kafka のログを分析する

Azure Monitor ログを使用して、HDInsight 上の Apache Kafka によって生成されたログを分析します。

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="logs-location"></a>ログの場所

クラスター内の Apache Kafka ログは `/var/log/kafka` にあります。 マネージド ディスクが使用されているかどうかに関係なく、Kafka ログは、クラスターのライフサイクルを超えて保存されることも保持されることもありません。 次の表に、使用可能なログを示します。

|ログ |説明 |
|---|---|
|kafka.out|Kafka プロセスの stdout と stderr。 Kafka のスタートアップ ログとシャットダウン ログは、このファイル内にあります。|
|server.log|メインの Kafka サーバー ログ。 すべての Kafka ブローカー ログは、最終的にここに記録されます。|
|controller.log|ブローカーがコントローラーとして機能している場合のコントローラー ログ。|
|statechange.log|ブローカーに対するすべての状態変更イベントは、このファイルに記録されます。|
|kafka-gc.log|Kafka ガベージ コレクションの統計。|

## <a name="enable-azure-monitor-logs-for-apache-kafka"></a>Apache Kafka の Azure Monitor ログを有効にする

HDInsight の Azure Monitor ログを有効にする手順は、すべての HDInsight クラスターで同じです。 必要なサービスを作成して構成する方法を理解するために、次のリンクを使用してください。

1. Log Analytics ワークスペースを作成します。 詳細については、「[Azure Monitor のログ](../../azure-monitor/logs/data-platform-logs.md)」ドキュメントをご覧ください。

2. HDInsight クラスターに Kafka を作成します。 詳細については、「[HDInsight での Apache Kafka の開始](apache-kafka-get-started.md)」をご覧ください。

3. Azure Monitor ログを使用するように Kafka クラスターを構成します。 詳細については、[Azure Monitor ログを使用して HDInsight を監視する方法](../hdinsight-hadoop-oms-log-analytics-tutorial.md)のドキュメントを参照してください。

> [!IMPORTANT]  
> データを Azure Monitor ログに使用できるようになるまで、20 分程度かかる場合があります。

## <a name="query-logs"></a>ログを照会する

1. [Azure Portal](https://portal.azure.com) から、Log Analytics のワークスペースを選択します。

2. 左側のメニューの **[全般]** で、**[ログ]** を選択します。 ここでは、Kafka から収集されたデータを検索できます。 クエリ ウィンドウでクエリを入力し、**[実行]** を選択します。 次に検索例をいくつか示します。

* ディスク使用量:

    ```kusto
    Perf
    | where ObjectName == "Logical Disk" and CounterName == "Free Megabytes" and InstanceName == "_Total" and ((Computer startswith_cs "hn" and Computer contains_cs "-") or (Computer startswith_cs "wn" and Computer contains_cs "-")) 
    | summarize AggregatedValue = avg(CounterValue) by Computer, bin(TimeGenerated, 1h)
    ```

* CPU 使用率:

    ```kusto
    Perf 
    | where CounterName == "% Processor Time" and InstanceName == "_Total" and ((Computer startswith_cs "hn" and Computer contains_cs "-") or (Computer startswith_cs "wn" and Computer contains_cs "-")) 
    | summarize AggregatedValue = avg(CounterValue) by Computer, bin(TimeGenerated, 1h)
    ```

* 1 秒あたりの受信メッセージ数: (`your_kafka_cluster_name` をクラスター名に置き換えます。)

    ```kusto
    metrics_kafka_CL 
    | where ClusterName_s == "your_kafka_cluster_name" and InstanceName_s == "kafka-BrokerTopicMetrics-MessagesInPerSec-Count" 
    | summarize AggregatedValue = avg(kafka_BrokerTopicMetrics_MessagesInPerSec_Count_value_d) by HostName_s, bin(TimeGenerated, 1h)
    ```

* 1 秒あたりの受信バイト数: (`wn0-kafka` ワーカー ノードのホスト名に置き換えます。)

    ```kusto
    metrics_kafka_CL 
    | where HostName_s == "wn0-kafka" and InstanceName_s == "kafka-BrokerTopicMetrics-BytesInPerSec-Count" 
    | summarize AggregatedValue = avg(kafka_BrokerTopicMetrics_BytesInPerSec_Count_value_d) by bin(TimeGenerated, 1h)
    ```

* 1 秒あたりの送信バイト数: (`your_kafka_cluster_name` をクラスター名に置き換えます。)

    ```kusto
    metrics_kafka_CL 
    | where ClusterName_s == "your_kafka_cluster_name" and InstanceName_s == "kafka-BrokerTopicMetrics-BytesOutPerSec-Count" 
    | summarize AggregatedValue = avg(kafka_BrokerTopicMetrics_BytesOutPerSec_Count_value_d) by bin(TimeGenerated, 1h)
    ```

    また、`*` を入力して、記録されているすべてのタイプを検索することもできます。 現在、クエリには、次のログを使用できます。

    | ログのタイプ | 説明 |
    | ---- | ---- |
    | log\_kafkaserver\_CL | Kafka ブローカーの server.log |
    | log\_kafkacontroller\_CL | Kafka ブローカーの controller.log |
    | metrics\_kafka\_CL | Kafka JMX メトリック |

    :::image type="content" source="./media/apache-kafka-log-analytics-operations-management/apache-kafka-cpu-usage.png" alt-text="Apache Kafka の Log Analytics の CPU 使用率" border="true":::

## <a name="next-steps"></a>次のステップ

Azure Monitor の詳細については、「[Azure Monitor の概要](../../azure-monitor/overview.md)」および「[Azure Monitor ログでクエリを実行して HDInsight クラスターを監視する](../hdinsight-hadoop-oms-log-analytics-use-queries.md)」をご覧ください。

Apache Kafka の操作について詳しくは、次のドキュメントをご覧ください。

* [HDInsight クラスター間で Apache Kafka をミラーリングする](apache-kafka-mirroring.md)
* [HDInsight 上の Apache Kafka のスケールを拡大する](apache-kafka-scalability.md)
* [Apache Kafka による Apache Spark ストリーミング (DStream) を使用する](../hdinsight-apache-spark-with-kafka.md)
* [Apache Kafka による Apache Spark 構造化ストリーミングを使用する](../hdinsight-apache-kafka-spark-structured-streaming.md)