---
title: Apache Kafka の Azure Monitor ログ - Azure HDInsight
description: Azure Monitor ログを使用して、Azure HDInsight 上の Apache Kafka クラスターからログを分析します。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 12/04/2019
ms.openlocfilehash: d4263b8b338f057893c9dfcda1541fc338c2577f
ms.sourcegitcommit: 8bd85510aee664d40614655d0ff714f61e6cd328
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/06/2019
ms.locfileid: "74894275"
---
# <a name="analyze-logs-for-apache-kafka-on-hdinsight"></a>HDInsight で Apache Kafka のログを分析する

Azure Monitor ログを使用して、HDInsight 上の Apache Kafka によって生成されたログを分析します。

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="enable-azure-monitor-logs-for-apache-kafka"></a>Apache Kafka の Azure Monitor ログを有効にする

HDInsight の Azure Monitor ログを有効にする手順は、すべての HDInsight クラスターで同じです。 必要なサービスを作成して構成する方法を理解するために、次のリンクを使用してください。

1. Log Analytics ワークスペースを作成します。 詳細については、「[Azure Monitor のログ](../../azure-monitor/platform/data-platform-logs.md)」ドキュメントをご覧ください。

2. HDInsight クラスターに Kafka を作成します。 詳細については、「[HDInsight での Apache Kafka の開始](apache-kafka-get-started.md)」をご覧ください。

3. Azure Monitor ログを使用するように Kafka クラスターを構成します。 詳細については、[Azure Monitor ログを使用して HDInsight を監視する方法](../hdinsight-hadoop-oms-log-analytics-tutorial.md)のドキュメントを参照してください。

> [!IMPORTANT]  
> データを Azure Monitor ログに使用できるようになるまで、20 分程度かかる場合があります。

## <a name="query-logs"></a>ログを照会する

1. [Azure Portal](https://portal.azure.com) から、Log Analytics のワークスペースを選択します。

2. 左側のメニューの **[全般]** で、 **[ログ]** を選択します。 ここでは、Kafka から収集されたデータを検索できます。 クエリ ウィンドウでクエリを入力し、 **[実行]** を選択します。 次に検索例をいくつか示します。

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

* 1 秒あたりの受信メッセージ:(`your_kafka_cluster_name` をご使用のクラスター名に置き換えます。)

    ```kusto
    metrics_kafka_CL 
    | where ClusterName_s == "your_kafka_cluster_name" and InstanceName_s == "kafka-BrokerTopicMetrics-MessagesInPerSec-Count" 
    | summarize AggregatedValue = avg(kafka_BrokerTopicMetrics_MessagesInPerSec_Count_value_d) by HostName_s, bin(TimeGenerated, 1h)
    ```

* 1 秒あたりの受信バイト数:(`wn0-kafka` をワーカー ノードのホスト名に置き換えます。)

    ```kusto
    metrics_kafka_CL 
    | where HostName_s == "wn0-kafka" and InstanceName_s == "kafka-BrokerTopicMetrics-BytesInPerSec-Count" 
    | summarize AggregatedValue = avg(kafka_BrokerTopicMetrics_BytesInPerSec_Count_value_d) by bin(TimeGenerated, 1h)
    ```

* 1 秒あたりの送信バイト数:(`your_kafka_cluster_name` をご使用のクラスター名に置き換えます。)

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

    ![Apache Kafka の Log Analytics の CPU 使用率](./media/apache-kafka-log-analytics-operations-management/apache-kafka-cpu-usage.png)

## <a name="next-steps"></a>次の手順

Azure Monitor の詳細については、「[Azure Monitor の概要](../../log-analytics/log-analytics-get-started.md)」および「[Azure Monitor ログでクエリを実行して HDInsight クラスターを監視する](../hdinsight-hadoop-oms-log-analytics-use-queries.md)」をご覧ください。

Apache Kafka の操作について詳しくは、次のドキュメントをご覧ください。

* [HDInsight クラスター間で Apache Kafka をミラーリングする](apache-kafka-mirroring.md)
* [HDInsight 上の Apache Kafka のスケールを拡大する](apache-kafka-scalability.md)
* [Apache Kafka による Apache Spark ストリーミング (DStream) を使用する](../hdinsight-apache-spark-with-kafka.md)
* [Apache Kafka による Apache Spark 構造化ストリーミングを使用する](../hdinsight-apache-kafka-spark-structured-streaming.md)
