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
ms.date: 02/09/2017
ms.author: larryfr
translationtype: Human Translation
ms.sourcegitcommit: 46bc5b3b70120cd631523fd2b27ad8b9a47e3c6d
ms.openlocfilehash: 21c25e4579a440b13c3201ee8f6c4e1db7a2aa46
ms.lasthandoff: 11/17/2016


---
# <a name="example-storm-toplogies-and-components-for-apache-storm-on-hdinsight"></a>HDInsight での Apache Storm の Storm トポロジとコンポーネントの例

HDInsight での Apache Storm の使用に関してマイクロソフトが作成して管理している例の一覧を次に示します。 これらの例では、基本的な C# および Java トポロジの作成から、Event Hubs、DocumentDB、Power BI、SQL Database、HDInsight での HBase、Azure Storage などの Azure サービスの使用まで、さまざまなトピックがカバーされています。 一部の例では、SignalR や Socket.IO など、Azure 以外、さらにはマイクロソフト以外のテクノロジを使用する方法についても説明されています。

| Description | 対象 | 言語/フレームワーク |
|:--- |:--- |:--- |
| [Apache Storm から Azure Data Lake Store への書き込み](hdinsight-storm-write-data-lake-store.md) |Azure Data Lake Store への書き込み |Java |
| [Event Hub のスパウトとボルトのソース](https://github.com/apache/storm/tree/master/external/storm-eventhubs) |Event Hub のスパウトとボルトのソース |Java |
| [HDInsight での Apache Storm の Java ベース トポロジの開発][5797064f] |Maven |Java |
| [Visual Studio を使用して HDInsight で Apache Storm の C# トポロジを開発する][16fce2d1] |HDInsight Tools for Visual Studio |C#、Java |
| [C# Storm トポロジでの複数のデータ ストリームの作成][ec5a4064] |複数のストリーム |C# |
| [HDInsight での Storm を使用した Twitter のトレンディング トピック][3c86c7c8] |Trident |Java、Trident |
| [HDInsight で Storm を使用して Azure Event Hubs のイベントを処理する (＃C)][844d1d81] |Event Hubs |C#、Java |
| [Process events from Azure Event Hubs with Storm on HDInsight (Java) (HDInsight で Storm を使用して Azure Event Hubs のイベントを処理する (Java))](hdinsight-storm-develop-java-event-hub-topology.md) |Event Hubs |Java |
| [Power BI を使用した Storm トポロジのデータの視覚化][94d15238] |Power BI |C# |
| [HDInsight (Hadoop) での Storm と HBase を使用したセンサー データの分析][ab894747] |Event Hubs、HBase、Socket.IO、Web ダッシュボード |C#、Java、JavaScript、HTML |
| [Process vehicle sensor data from Event Hubs using Storm on HDInsight (HDInsight での Storm を使用した Event Hubs からの車両センサー データの処理)][246ee964] |Event Hubs、DocumentDb、Azure Storage Blob (WASB) |C#、Java |
| [HDInsight での Storm を使用した Azure Event Hubs から HBase への抽出、変換、ロード (ETL)][b4b68194] |Event Hubs、HBase |C# |
| [HDInsight の Storm から Azure サービスを使用するためのテンプレート C# Storm トポロジ プロジェクト][ce0c02a2] |Event Hubs、DocumentDb、SQL Database、HBase、SignalR |C#、Java |
| [HDInsight の Storm を使用して Azure Event Hubs から読み取る場合の拡張性ベンチマーク][d6c540e3] |メッセージ スループット、Event Hubs、SQL Database |C#、Java |
| [HDInsight での Storm および HBase を使用した関連付け](hdinsight-storm-correlation-topology.md) |HBase |C# |
| [HDInsight の Storm での Python の使用](hdinsight-storm-develop-python-topology.md) |Java および Clojure ベースの Storm トポロジによる Python コンポーネント |Python |

## <a name="next-steps"></a>次のステップ

* [HDInsight での Apache Storm の使用][2b8c3488]
* [HDInsight での Apache Storm のデプロイと管理][6eb0d3b8]

[2b8c3488]: hdinsight-apache-storm-tutorial-get-started-linux.md "HDInsight クラスターで Storm を作成し、Storm ダッシュボードを使用してトポロジの例をデプロイする方法について説明します。"
[6eb0d3b8]: hdinsight-storm-deploy-monitor-topology.md "Web ベースの Storm ダッシュボードと Storm UI または HDInsight Tools for Visual Studio を使用してトポロジをデプロイおよび管理する方法を説明します。"
[16fce2d1]: hdinsight-storm-develop-csharp-visual-studio-topology.md "HDInsight Tools for Visual Studio を使用して C# Storm トポロジを作成する方法を説明します。"
[5797064f]: hdinsight-storm-develop-java-topology.md "基本的なワード カウント トポロジを作成し、Maven を使用して Java で Storm トポロジを作成する方法について説明します。"
[94d15238]: hdinsight-storm-power-bi-topology.md "C# トポロジから Power BI にデータを書き込み、データからグラフとダッシュボードを作成する方法を示します。"
[ec5a4064]: https://github.com/Blackmist/csharp-storm-example "C# で実装されたワード カウントを実行する基本的な Storm トポロジを示します。これは、C# トポロジ内で複数のデータ ストリームを作成する方法も示します。"
[844d1d81]: hdinsight-storm-develop-csharp-event-hub-topology.md "HDInsight の Storm でAzure Event Hubs のデータを読み書きする方法を説明します。"
[ab894747]: hdinsight-storm-sensor-data-analysis.md "HDInsight の Apache Storm を使用して、Azure Event Hubs からのセンサー データを処理し、D3.js を使用して表示し、HBase に格納する (オプション) 方法を説明します。"
[3c86c7c8]: hdinsight-storm-twitter-trending.md "Trident を使用して、Twitter で (ハッシュタグに基づいて) トレンディング トピックを見つける Storm トポロジを作成する方法について説明します。"
[246ee964]: hdinsight-storm-iot-eventhub-documentdb.md "Storm トポロジを使用して、Azure Event Hubs からメッセージを読み取り、データ参照用に Azure DocumentDB からドキュメントを読み取り、データを Azure Storage に保存する方法を説明します。"
[d6c540e3]: https://github.com/hdinsight/hdinsight-storm-examples/blob/master/EventCountExample "HDInsight の Apache Storm を使用して Azure Event Hubs から読み取って SQL Database に格納するときのスループットを示す複数のトポロジです。"
[b4b68194]: https://github.com/hdinsight/hdinsight-storm-examples/blob/master/RealTimeETLExample "Azure Event Hubs からデータを読み取り、データを集計および変換し、HDInsight の HBase に格納する方法を説明します。"
[ce0c02a2]: https://github.com/hdinsight/hdinsight-storm-examples/tree/master/templates/HDInsightStormExamples "このプロジェクトには、Event Hubs、DocumentDB、SQL Database などの Azure サービスと対話するためのスパウト、ボルト、トポロジのテンプレートが含まれます。"


