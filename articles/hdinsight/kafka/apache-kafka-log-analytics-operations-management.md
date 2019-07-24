---
title: Apache Kafka の Azure Monitor ログ - Azure HDInsight
description: Azure Monitor ログを使用して、Azure HDInsight 上の Apache Kafka クラスターからログを分析します。
services: hdinsight
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 06/15/2018
ms.openlocfilehash: 5154058bb1562b670e24f8813e979588a536f086
ms.sourcegitcommit: 223604d8b6ef20a8c115ff877981ce22ada6155a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/22/2019
ms.locfileid: "58359859"
---
# <a name="analyze-logs-for-apache-kafka-on-hdinsight"></a>HDInsight で Apache Kafka のログを分析する

Azure Monitor ログを使用して、HDInsight 上の Apache Kafka によって生成されたログを分析します。

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="enable-azure-monitor-logs-for-apache-kafka"></a>Apache Kafka の Azure Monitor ログを有効にする

HDInsight の Azure Monitor ログを有効にする手順は、すべての HDInsight クラスターで同じです。 必要なサービスを作成して構成する方法を理解するために、次のリンクを使用してください。

1. Log Analytics ワークスペースを作成します。 詳細については、「[Log Analytics ワークスペースを使って作業を開始する](https://docs.microsoft.com/azure/log-analytics)」をご覧ください。

2. HDInsight クラスターに Kafka を作成します。 詳細については、「[HDInsight での Apache Kafka の開始](apache-kafka-get-started.md)」をご覧ください。

3. Azure Monitor ログを使用するように Kafka クラスターを構成します。 詳細については、[Azure Monitor ログを使用して HDInsight を監視する方法](../hdinsight-hadoop-oms-log-analytics-tutorial.md)のドキュメントを参照してください。

    > [!NOTE]  
    > `Enable-AzHDInsightOperationsManagementSuite` コマンドレットを使って、Azure Monitor ログを使用するようにクラスターを構成することもできます。 このコマンドレットを使用するには、次の情報が必要です。
    >
    > * HDInsight クラスター名。
    > * Azure Monitor ログのワークスペース ID。 Log Analytics ワークスペースのワークスペース ID を検索することができます。
    > * ログ分析接続のための主キー。 主キーを見つけるには、Azure portal のワークスペースを開き、左側のメニューから __[詳細設定]__ を選択します。 [詳細設定] から __[接続されたソース]__>__[Linux サーバー]__ を選択します。


> [!IMPORTANT]  
> データを Azure Monitor ログに使用できるようになるまで、20 分程度かかる場合があります。

## <a name="query-logs"></a>ログを照会する

1. [Azure Portal](https://portal.azure.com) から、Log Analytics のワークスペースを選択します。

2. __[ログ検索]__ を選択します。 ここでは、Kafka から収集されたデータを検索できます。 次に検索例をいくつか示します。

   * ディスク使用量:`Perf | where ObjectName == "Logical Disk" and CounterName == "Free Megabytes" and InstanceName == "_Total" and ((Computer startswith_cs "hn" and Computer contains_cs "-") or (Computer startswith_cs "wn" and Computer contains_cs "-")) | summarize AggregatedValue = avg(CounterValue) by Computer, bin(TimeGenerated, 1h)`

   * CPU 使用率: `Perf | where CounterName == "% Processor Time" and InstanceName == "_Total" and ((Computer startswith_cs "hn" and Computer contains_cs "-") or (Computer startswith_cs "wn" and Computer contains_cs "-")) | summarize AggregatedValue = avg(CounterValue) by Computer, bin(TimeGenerated, 1h)`

   * 1 秒あたりの受信メッセージ: `metrics_kafka_CL | where ClusterName_s == "your_kafka_cluster_name" and InstanceName_s == "kafka-BrokerTopicMetrics-MessagesInPerSec-Count" | summarize AggregatedValue = avg(kafka_BrokerTopicMetrics_MessagesInPerSec_Count_value_d) by HostName_s, bin(TimeGenerated, 1h)`

   * 1 秒あたりの受信バイト数: `metrics_kafka_CL | where HostName_s == "wn0-kafka" and InstanceName_s == "kafka-BrokerTopicMetrics-BytesInPerSec-Count" | summarize AggregatedValue = avg(kafka_BrokerTopicMetrics_BytesInPerSec_Count_value_d) by bin(TimeGenerated, 1h)`

   * 1 秒あたりの送信バイト数: `metrics_kafka_CL | where ClusterName_s == "your_kafka_cluster_name" and InstanceName_s == "kafka-BrokerTopicMetrics-BytesOutPerSec-Count" | summarize AggregatedValue = avg(kafka_BrokerTopicMetrics_BytesOutPerSec_Count_value_d) by bin(TimeGenerated, 1h)`

     > [!IMPORTANT]  
     > クエリの値をクラスター固有の情報に置き換えます。 たとえば、`ClusterName_s` には、お使いのクラスターの名前を設定する必要があります。 `HostName_s` には、クラスター内のワーカー ノードのドメイン名を設定する必要があります。

     また、`*` を入力して、記録されているすべてのタイプを検索することもできます。 現在、クエリには、次のログを使用できます。

     | ログのタイプ | 説明 |
     | ---- | ---- |
     | log\_kafkaserver\_CL | Kafka ブローカーの server.log |
     | log\_kafkacontroller\_CL | Kafka ブローカーの controller.log |
     | metrics\_kafka\_CL | Kafka JMX メトリック |

     ![CPU 使用率検索の画像](./media/apache-kafka-log-analytics-operations-management/kafka-cpu-usage.png)
 
## <a name="next-steps"></a>次の手順

Azure Monitor の詳細については、[Azure Monitor の概要](../../log-analytics/log-analytics-get-started.md)のドキュメントを参照してください。

Apache Kafka の操作について詳しくは、次のドキュメントをご覧ください。

 * [HDInsight クラスター間で Apache Kafka をミラーリングする](apache-kafka-mirroring.md)
 * [HDInsight 上の Apache Kafka の拡張性を改善する](apache-kafka-scalability.md)
 * [Apache Kafka による Apache Spark ストリーミング (DStream) を使用する](../hdinsight-apache-spark-with-kafka.md)
 * [Apache Kafka による Apache Spark 構造化ストリーミングを使用する](../hdinsight-apache-kafka-spark-structured-streaming.md)
