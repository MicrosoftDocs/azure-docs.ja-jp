---
title: Azure HDInsight での Apache Storm トポロジの例
description: 基本的な C# や Java トポロジなど、HDInsight で Apache Storm を使用して作成、テストされる Storm トポロジの例の一覧と、Event Hubs の使用。
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 05/30/2018
ms.openlocfilehash: a535510ce56147f708f1cd8219cf898e6e0df36b
ms.sourcegitcommit: 9b80d1e560b02f74d2237489fa1c6eb7eca5ee10
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/01/2019
ms.locfileid: "67483790"
---
# <a name="example-apache-storm-topologies-and-components-for-apache-storm-on-hdinsight"></a>HDInsight 上の Apache Storm 用 Apache Storm トポロジとコンポーネントの例

HDInsight 上の [Apache Storm](https://storm.apache.org/) で使用するために Microsoft によって作成および管理されている例の一覧を次に示します。 これらの例には、基本的な C# および Java トポロジの作成から、Event Hubs、Cosmos DB、SQL Database、HDInsight 上の [Apache HBase](https://hbase.apache.org/)、Azure Storage などの Azure サービスの使用までの、さまざまなトピックが含まれています。 また一部の例には、Azure 以外のテクノロジや、さらには SignalR や Socket.IO などのマイクロソフト以外のテクノロジを使用する方法についても示されています。

| 説明 | 対象 | 言語/フレームワーク |
|:--- |:--- |:--- |
| [Apache Storm から Azure Data Lake Storage への書き込み](apache-storm-write-data-lake-store.md) |Azure Data Lake Storage への書き込み |Java |
| [Event Hub のスパウトとボルトのソース](https://github.com/apache/storm/tree/master/external/storm-eventhubs) |Event Hub のスパウトとボルトのソース |Java |
| [HDInsight での Apache Storm の Java ベース トポロジの開発][5797064f] |Maven |Java |
| [Visual Studio を使用して HDInsight で Apache Storm の C# トポロジを開発する][16fce2d1] |HDInsight Tools for Visual Studio |C#、Java |
| [HDInsight 上の Apache Storm で Azure Event Hubs からのイベントを処理する (C#)][844d1d81] |Event Hubs |C#、Java |
| [Process events from Azure Event Hubs with Storm on HDInsight (Java) (HDInsight で Storm を使用して Azure Event Hubs のイベントを処理する (Java))](https://azure.microsoft.com/resources/samples/hdinsight-java-storm-eventhub/) |Event Hubs |Java |
| [HDInsight 上の Apache Storm を使用して Event Hubs からの車両センサー データを処理する][246ee964] |Event Hubs、Cosmos DB、Azure Storage Blob (WASB) |C#、Java |
| [HDInsight 上の Apache Storm を使用した Azure Event Hubs から Apache HBase への抽出、変換、読み込み (ETL)][b4b68194] |Event Hubs、HBase |C# |
| [HDInsight 上の Apache Storm から Azure サービスを使用するためのテンプレート C# Storm トポロジ プロジェクト][ce0c02a2] |Event Hubs、Cosmos DB、SQL Database、HBase、SignalR |C#、Java |
| [HDInsight 上の Apache Storm を使用して Azure Event Hubs から読み取るためのスケーラビリティ ベンチマーク][d6c540e3] |メッセージ スループット、Event Hubs、SQL Database |C#、Java |
| [HDInsight 上の Apache Storm で Python を使用する](apache-storm-develop-python-topology.md) |Flux トポロジを使用する Python コンポーネント |Python |
| [HDInsight 上の Apache Storm で Apache Kafka を使用する](../hdinsight-apache-storm-with-kafka.md) | Apache Storm を使用した Apache Kafka の読み取りと書き込み | Java |

> [!WARNING]  
> この一覧の C# の例は、当初、Windows ベースの HDInsight で作成されたので、Linux ベースの HDInsight クラスターでは正しく動作しない可能性があります。 Linux ベースのクラスターでは、Mono を使用して .NET コードを実行するため、この例で使用されているフレームワークとパッケージでは互換性の問題がある場合があります。
>
> Linux は、バージョン 3.4 以降の HDInsight で使用できる唯一のオペレーティング システムです。

### <a name="next-steps"></a>次の手順

* [Azure HDInsight で Apache Storm トポロジを作成、監視する](./apache-storm-quickstart.md)
* [HDInsight 上の Apache Storm で Apache Storm トポロジをデプロイおよび管理する方法について][6eb0d3b8]


[6eb0d3b8]:apache-storm-deploy-monitor-topology-linux.md "Web ベースの Apache Storm ダッシュボードと Storm UI または HDInsight Tools for Visual Studio を使用してトポロジをデプロイおよび管理する方法について説明します。"
[16fce2d1]:apache-storm-develop-csharp-visual-studio-topology.md "HDInsight Tools for Visual Studio を使用して C# Storm トポロジを作成する方法を説明します。"
[5797064f]:apache-storm-develop-java-topology.md "基本的なワードカウント トポロジを作成し、Maven を使用して Java で Storm トポロジを作成する方法について説明します。"
[844d1d81]:apache-storm-develop-csharp-event-hub-topology.md "HDInsight の Storm でAzure Event Hubs のデータを読み書きする方法を説明します。"
[246ee964]: https://github.com/hdinsight/hdinsight-storm-examples/blob/master/IotExample/README.md "Storm トポロジを使用して、Azure Event Hubs からメッセージを読み取り、データ参照用に Azure Cosmos DB からドキュメントを読み取り、データを Azure Storage に保存する方法を説明します。"
[d6c540e3]: https://github.com/hdinsight/hdinsight-storm-examples/blob/master/EventCountExample "HDInsight の Apache Storm を使用して Azure Event Hubs から読み取って SQL Database に格納するときのスループットを示す複数のトポロジです。"
[b4b68194]: https://github.com/hdinsight/hdinsight-storm-examples/blob/master/RealTimeETLExample "Azure Event Hubs からデータを読み取り、データを集計および変換し、HDInsight の HBase に格納する方法を説明します。"
[ce0c02a2]: https://github.com/hdinsight/hdinsight-storm-examples/tree/master/templates/HDInsightStormExamples "このプロジェクトには、Event Hubs、Cosmos DB、SQL Database などの Azure サービスと対話するためのスパウト、ボルト、トポロジのテンプレートが含まれます。"

