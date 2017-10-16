---
title: "Apache Kafka のログ分析 - Azure HDInsight | Microsoft Docs"
description: "Operations Management Suite を使用して、Azure HDInsight 上の Apache Kafka クラスターからログを分析します。"
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: 
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 09/11/2017
ms.author: larryfr
ms.openlocfilehash: be549c8fd072663ae9bbb9441a654a2bf1912198
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="analyze-logs-for-apache-kafka-preview-on-hdinsight"></a>HDInsight で Apache Kafka (プレビュー) のログを分析する

Microsoft Operations Management Suite を使用して、HDInsight 上の Apache Kafka によって生成されたログを分析する方法について説明します。

## <a name="enable-oms-for-kafka"></a>Kafka の OMS を有効にする

HDInsight の Log Analytics を有効にする手順は、すべての HDInsight クラスターで同じです。 必要なサービスを作成して構成する方法を理解するために、次のリンクを使用してください。

1. Log Analytics ワークスペースを作成します。 詳細については、「[Log Analytics ワークスペースを使って作業を開始する](../log-analytics/log-analytics-get-started.md)」をご覧ください。

2. HDInsight クラスターに Kafka を作成します。 詳細については、「[HDInsight での Apache Kafka の開始](hdinsight-apache-kafka-get-started.md)」をご覧ください。

3. ログ分析を使用する Kafka クラスターを構成します。 詳細については、「[Use Log Analytics to monitor HDInsight (Log Analytics を使用した HDInsight の監視)](hdinsight-hadoop-oms-log-analytics-tutorial.md)」をご覧ください。

    > [!NOTE]
    > `Enable-AzureRmHDInsightOperationsManagementSuite` コマンドレットを使って、ログ分析を使用するようにクラスターを構成することもできます。 このコマンドレットを使用するには、次の情報が必要です。
    >
    > * HDInsight クラスター名。
    > * ログ分析のためのワークスペース ID。 OMS ワークスペース内で、お使いのログ分析のワークスペースのワークスペース ID を見つけることができます。
    > * OMS 接続のための主キー。 主キーを検索するには、お使いのログ分析のインスタンスを選択して、__[OMS ポータル]__ をクリックします。 OMS ポータルから、__[設定]__、__[接続されたソース]__、__[Linux サーバー]__の順に選択します。


> [!IMPORTANT]
> データをログ分析に使用できるようになるまで、20 分程度かかる場合があります。

## <a name="query-logs"></a>ログを照会する

1. [Azure ポータル](https://portal.azure.com)から、ログ分析のワークスペースを選択します。

2. __[ログ検索]__ を選択します。 ここでは、Kafka から収集されたデータを検索できます。 次に検索例をいくつか示します。

    * ディスク使用量:`Type=Perf ObjectName="Logical Disk" (CounterName="Free Megabytes")  InstanceName="_Total" Computer='hn*-*' or Computer='wn*-*' | measure avg(CounterValue) by   Computer interval 1HOUR`
    * CPU 使用率: `Type:Perf CounterName="% Processor Time" InstanceName="_Total" Computer='hn*-*' or Computer='wn*-*' | measure avg(CounterValue) by Computer interval 1HOUR`
    * 1 秒あたりの受信メッセージ: `Type=metrics_kafka_CL ClusterName_s="your_kafka_cluster_name" InstanceName_s="kafka-BrokerTopicMetrics-MessagesInPerSec-Count" | measure avg(kafka_BrokerTopicMetrics_MessagesInPerSec_Count_value_d) by HostName_s interval 1HOUR`
    * 1 秒あたりの受信バイト数: `Type=metrics_kafka_CL HostName_s="wn0-kafka" InstanceName_s="kafka-BrokerTopicMetrics-BytesInPerSec-Count" | measure avg(kafka_BrokerTopicMetrics_BytesInPerSec_Count_value_d) interval 1HOUR`
    * 1 秒あたりの送信バイト数: `Type=metrics_kafka_CL ClusterName_s="your_kafka_cluster_name" InstanceName_s="kafka-BrokerTopicMetrics-BytesOutPerSec-Count" |  measure avg(kafka_BrokerTopicMetrics_BytesOutPerSec_Count_value_d) interval 1HOUR`

    > [!IMPORTANT]
    > クエリの値をクラスター固有の情報に置き換えます。 たとえば、`ClusterName_s` には、お使いのクラスターの名前を設定する必要があります。 `HostName_s` には、クラスター内のワーカー ノードのドメイン名を設定する必要があります。

    また、`*` を入力して、記録されているすべてのタイプを検索することもできます。 現在、クエリには、次のログを使用できます。

    | ログのタイプ | Description |
    | ---- | ---- |
    | log\_kafkaserver\_CL | Kafka ブローカーの server.log |
    | log\_kafkacontroller\_CL | Kafka ブローカーの controller.log |
    | metrics\_kafka\_CL | Kafka JMX メトリック |

    ![CPU 使用率検索の画像](./media/apache-kafka-log-analytics-operations-management/kafka-cpu-usage.png)
 
 ## <a name="next-steps"></a>次のステップ

ログ分析の詳細については、「[Log Analytics ワークスペースを使って作業を開始する](../log-analytics/log-analytics-get-started.md)」をご覧ください。

Kafka の操作の詳細については、次のドキュメントを参照してください。

 * [HDInsight クラスター間で Kafka をミラーリングする](hdinsight-apache-kafka-mirroring.md)
 * [HDInsight 上の Kafka の拡張性を改善する](hdinsight-apache-kafka-scalability.md)
 * [Kafka で Spark ストリーミングを (DStream) を使用する](hdinsight-apache-spark-with-kafka.md)
 * [Kafka で Spark 構造化ストリーミングを使用する](hdinsight-apache-kafka-spark-structured-streaming.md)