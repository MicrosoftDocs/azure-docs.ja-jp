---
title: "HDInsight での Apache Storm トポロジの例 | Microsoft Docs"
description: "基本的な C# や Java トポロジなど、HDInsight で Apache Storm を使用して作成、テストされる Storm トポロジの例の一覧と、Event Hubs の使用。"
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: f9b1bdff-5928-4705-a76d-52fd200917cb
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 08/07/2017
ms.author: larryfr
ms.translationtype: HT
ms.sourcegitcommit: 760543dc3880cb0dbe14070055b528b94cffd36b
ms.openlocfilehash: 71482e24e519319d506d61e0582420e35f1cae70
ms.contentlocale: ja-jp
ms.lasthandoff: 08/10/2017

---
# <a name="example-storm-topologies-and-components-for-apache-storm-on-hdinsight"></a>HDInsight 上の Apache Storm 用 Storm トポロジとコンポーネントの例

HDInsight での Apache Storm の使用に関してマイクロソフトが作成して管理している例の一覧を次に示します。 これらの例では、基本的な C# および Java トポロジの作成から、Event Hubs、Cosmos DB、Power BI、SQL Database、HDInsight での HBase、Azure Storage などの Azure サービスの使用まで、さまざまなトピックがカバーされています。 また一部の例には、Azure 以外のテクノロジや、さらには SignalR や Socket.IO などのマイクロソフト以外のテクノロジを使用する方法についても示されています。

| Description | 対象 | 言語/フレームワーク |
|:--- |:--- |:--- |
| [Apache Storm から Azure Data Lake Store への書き込み](hdinsight-storm-write-data-lake-store.md) |Azure Data Lake Store への書き込み |Java |
| [Event Hub のスパウトとボルトのソース](https://github.com/apache/storm/tree/master/external/storm-eventhubs) |Event Hub のスパウトとボルトのソース |Java |
| [HDInsight での Apache Storm の Java ベース トポロジの開発][5797064f] |Maven |Java |
| [Visual Studio を使用して HDInsight で Apache Storm の C# トポロジを開発する][16fce2d1] |HDInsight Tools for Visual Studio |C#、Java |
| [C# Storm トポロジでの複数のデータ ストリームの作成][ec5a4064] |複数のストリーム |C# |
| [HDInsight で Storm を使用して Azure Event Hubs のイベントを処理する (C#)][844d1d81] |Event Hubs |C#、Java |
| [Process events from Azure Event Hubs with Storm on HDInsight (Java) (HDInsight で Storm を使用して Azure Event Hubs のイベントを処理する (Java))](hdinsight-storm-develop-java-event-hub-topology.md) |Event Hubs |Java |
| [Power BI を使用した Storm トポロジのデータの視覚化][94d15238] |Power BI |C# |
| [HDInsight での Storm と HBase を使用したセンサー データの分析][ab894747] |Event Hubs、HBase、Socket.IO、Web ダッシュボード |C#、Java、JavaScript、HTML |
| [HDInsight 上の Storm を使用した Event Hubs からの車両センサー データの処理][246ee964] |Event Hubs、Cosmos DB、Azure Storage Blob (WASB) |C#、Java |
| [HDInsight での Storm を使用した Azure Event Hubs から HBase への抽出、変換、ロード (ETL)][b4b68194] |Event Hubs、HBase |C# |
| [HDInsight の Storm から Azure サービスを使用するためのテンプレート C# Storm トポロジ プロジェクト][ce0c02a2] |Event Hubs、Cosmos DB、SQL Database、HBase、SignalR |C#、Java |
| [HDInsight の Storm を使用して Azure Event Hubs から読み取る場合の拡張性ベンチマーク][d6c540e3] |メッセージ スループット、Event Hubs、SQL Database |C#、Java |
| [HDInsight での Storm および HBase を使用した関連付け](hdinsight-storm-correlation-topology.md) |HBase |C# |
| [HDInsight の Storm での Python の使用](hdinsight-storm-develop-python-topology.md) |Flux トポロジを使用する Python コンポーネント |Python |
| [HDInsight の Storm での Kafka の使用](hdinsight-apache-storm-with-kafka.md) | Apache Storm を使用した Apache Kafka の読み取りと書き込み | Java |

### <a name="next-steps"></a>次のステップ

* [HDInsight での Apache Storm の使用][2b8c3488]
* [HDInsight での Apache Storm のデプロイと管理][6eb0d3b8]

[2b8c3488]: hdinsight-apache-storm-tutorial-get-started-linux.md "HDInsight クラスターで Storm を作成し、Storm ダッシュボードを使用してトポロジの例をデプロイする方法について説明します。"
[6eb0d3b8]: hdinsight-storm-deploy-monitor-topology.md "Web ベースの Storm ダッシュボードと Storm UI または HDInsight Tools for Visual Studio を使用してトポロジをデプロイおよび管理する方法を説明します。"
[16fce2d1]: hdinsight-storm-develop-csharp-visual-studio-topology.md "HDInsight Tools for Visual Studio を使用して C# Storm トポロジを作成する方法を説明します。"
[5797064f]: hdinsight-storm-develop-java-topology.md "基本的なワードカウント トポロジを作成し、Maven を使用して Java で Storm トポロジを作成する方法について説明します。"
[94d15238]: hdinsight-storm-power-bi-topology.md "C# トポロジから Power BI にデータを書き込み、データからグラフとダッシュボードを作成する方法を示します。"
[ec5a4064]: https://github.com/Blackmist/csharp-storm-example "C# で実装されたワード カウントを実行する基本的な Storm トポロジを示します。これは、C# トポロジ内で複数のデータ ストリームを作成する方法も示します。"
[844d1d81]: hdinsight-storm-develop-csharp-event-hub-topology.md "HDInsight の Storm でAzure Event Hubs のデータを読み書きする方法を説明します。"
[ab894747]: hdinsight-storm-sensor-data-analysis.md "HDInsight の Apache Storm を使用して、Azure Event Hubs からのセンサー データを処理し、D3.js を使用して表示し、HBase に格納する (オプション) 方法を説明します。"
[246ee964]: https://github.com/hdinsight/hdinsight-storm-examples/blob/master/IotExample/README.md "Storm トポロジを使用して、Azure Event Hubs からメッセージを読み取り、データ参照用に Azure Cosmos DB からドキュメントを読み取り、データを Azure Storage に保存する方法を説明します。"
[d6c540e3]: https://github.com/hdinsight/hdinsight-storm-examples/blob/master/EventCountExample "HDInsight の Apache Storm を使用して Azure Event Hubs から読み取って SQL Database に格納するときのスループットを示す複数のトポロジです。"
[b4b68194]: https://github.com/hdinsight/hdinsight-storm-examples/blob/master/RealTimeETLExample "Azure Event Hubs からデータを読み取り、データを集計および変換し、HDInsight の HBase に格納する方法を説明します。"
[ce0c02a2]: https://github.com/hdinsight/hdinsight-storm-examples/tree/master/templates/HDInsightStormExamples "このプロジェクトには、Event Hubs、Cosmos DB、SQL Database などの Azure サービスと対話するためのスパウト、ボルト、トポロジのテンプレートが含まれます。"


