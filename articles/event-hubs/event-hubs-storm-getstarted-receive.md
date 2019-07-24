---
title: Apache Storm を使用してイベントを受信する - Azure Event Hubs | Microsoft Docs
description: この記事では、Apache Storm を使用して Azure Event Hubs からイベントを受信する方法について説明します。
services: event-hubs
documentationcenter: ''
author: ShubhaVijayasarathy
manager: timlt
editor: ''
ms.assetid: ''
ms.service: event-hubs
ms.workload: na
ms.tgt_pltfrm: java
ms.devlang: multiple
ms.topic: article
ms.custom: seodec18
ms.date: 12/06/2018
ms.author: shvija
ms.openlocfilehash: 3c50916f648a2bce634f7aeb109147a873de1de6
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/08/2018
ms.locfileid: "53094612"
---
# <a name="receive-events-from-event-hubs-using-apache-storm"></a>Apache Storm を使用して Azure Event Hubs からイベントを受信する

[Apache Storm](https://storm.incubator.apache.org) は、境界のないデータ ストリームを確実かつ容易に処理する分散型リアルタイム計算システムです。 このセクションでは、Azure Event Hubs Storm スパウトを使用して、Event Hub からイベントを受信する方法を示します。 Apache Storm を使用して、別々のノードにホストされている複数のプロセスでイベントを分割することができます。 Storm と Event Hub の統合は、Storm の Zookeeper のインストールを使用して、進行状況への透過的なチェックポイントの設定、永続的なチェックポイントの管理、並行した Event Hub からの受信によって、イベントの利用を簡略化します。

Event Hub の受信パターンの詳細については、「[Event Hub の概要][Event Hubs overview]」を参照してください。

## <a name="create-project-and-add-code"></a>プロジェクトの作成とコードの追加

このチュートリアルでは、[HDInsight Storm][HDInsight Storm] インストールを使用します。これは、Event Hubs スパウトと共に既に利用できます。

1. [HDInsight Storm の使用](../hdinsight/storm/apache-storm-overview.md) の手順に従って、新しい HDInsight クラスターを作成し、リモート デスクトップを介して接続します。
2. `%STORM_HOME%\examples\eventhubspout\eventhubs-storm-spout-0.9-jar-with-dependencies.jar` ファイルをローカル開発環境にコピーします。 これには events-storm-spout が含まれています。
3. 次のコマンドを使用して Maven のローカル ストアにパッケージをインストールします。 これにより、後の手順で Storm プロジェクトに参照として追加できます。

    ```shell
    mvn install:install-file -Dfile=target\eventhubs-storm-spout-0.9-jar-with-dependencies.jar -DgroupId=com.microsoft.eventhubs -DartifactId=eventhubs-storm-spout -Dversion=0.9 -Dpackaging=jar
    ```
4. Eclipse で、新しい Maven プロジェクトを作成します (**[ファイル]**、**[新規作成]**、**[プロジェクト]** の順にクリックします)。
   
    ![[ファイル] > [新規作成] > [プロジェクト]][12]
5. **[既定のワークスペースの場所を使用する]** を選択し、**[次へ]** をクリックします。
6. **maven-archetype-quickstart** アーキタイプを選択し、**[次へ]** をクリックします。
7. **GroupID** と **ArtifactID** を挿入し、**[完了]** をクリックします。
8. **pom.xml** で、`<dependency>` ノードに次の依存関係を追加します。

    ```xml  
    <dependency>
        <groupId>org.apache.storm</groupId>
        <artifactId>storm-core</artifactId>
        <version>0.9.2-incubating</version>
        <scope>provided</scope>
    </dependency>
    <dependency>
        <groupId>com.microsoft.eventhubs</groupId>
        <artifactId>eventhubs-storm-spout</artifactId>
        <version>0.9</version>
    </dependency>
    <dependency>
        <groupId>com.netflix.curator</groupId>
        <artifactId>curator-framework</artifactId>
        <version>1.3.3</version>
        <exclusions>
            <exclusion>
                <groupId>log4j</groupId>
                <artifactId>log4j</artifactId>
            </exclusion>
            <exclusion>
                <groupId>org.slf4j</groupId>
                <artifactId>slf4j-log4j12</artifactId>
            </exclusion>
        </exclusions>
        <scope>provided</scope>
    </dependency>
    ```

9. **src** フォルダー内に **Config.properties** という名前のファイルを作成します。次の内容をコピーし、`receive rule key` と `event hub name` の値を置き換えます。

    ```java
    eventhubspout.username = ReceiveRule
    eventhubspout.password = {receive rule key}
    eventhubspout.namespace = ioteventhub-ns
    eventhubspout.entitypath = {event hub name}
    eventhubspout.partitions.count = 16
       
    # if not provided, will use storm's zookeeper settings
    # zookeeper.connectionstring=localhost:2181
       
    eventhubspout.checkpoint.interval = 10
    eventhub.receiver.credits = 10
    ```
    **eventhub.receiver.credits** の値によって、Storm パイプラインにリリースする前にバッチ処理されるイベントの数が決まります。 わかりやすくするため、この例ではこの値を 10 に設定しています。 運用環境では通常、1024 などの大きい値を設定します。
10. 次のコードで **LoggerBolt** という新しいクラスを作成します。
    
    ```java
    import java.util.Map;
    import org.slf4j.Logger;
    import org.slf4j.LoggerFactory;
    import backtype.storm.task.OutputCollector;
    import backtype.storm.task.TopologyContext;
    import backtype.storm.topology.OutputFieldsDeclarer;
    import backtype.storm.topology.base.BaseRichBolt;
    import backtype.storm.tuple.Tuple;
    
    public class LoggerBolt extends BaseRichBolt {
        private OutputCollector collector;
        private static final Logger logger = LoggerFactory
                  .getLogger(LoggerBolt.class);
    
        @Override
        public void execute(Tuple tuple) {
            String value = tuple.getString(0);
            logger.info("Tuple value: " + value);
   
            collector.ack(tuple);
        }
   
        @Override
        public void prepare(Map map, TopologyContext context, OutputCollector collector) {
            this.collector = collector;
            this.count = 0;
        }
        
        @Override
        public void declareOutputFields(OutputFieldsDeclarer declarer) {
            // no output fields
        }
    
    }
    ```
    
    この Storm ボルトは、受信したイベントの内容を記録します。 これを容易に拡張して、ストレージ サービスにタプルを格納できます。 [イベント ハブでの HDInsight Storm の例]に関するページでは、これと同じアプローチを使用して、Azure Storage と Power BI にデータを格納します。
11. 次のコードで **LogTopology** という新しいクラスを作成します。
    
    ```java
    import java.io.FileReader;
    import java.util.Properties;
    import backtype.storm.Config;
    import backtype.storm.LocalCluster;
    import backtype.storm.StormSubmitter;
    import backtype.storm.generated.StormTopology;
    import backtype.storm.topology.TopologyBuilder;
    import com.microsoft.eventhubs.samples.EventCount;
    import com.microsoft.eventhubs.spout.EventHubSpout;
    import com.microsoft.eventhubs.spout.EventHubSpoutConfig;
        
    public class LogTopology {
        protected EventHubSpoutConfig spoutConfig;
        protected int numWorkers;
        
        protected void readEHConfig(String[] args) throws Exception {
            Properties properties = new Properties();
            if (args.length > 1) {
                properties.load(new FileReader(args[1]));
            } else {
                properties.load(EventCount.class.getClassLoader()
                        .getResourceAsStream("Config.properties"));
            }
        
            String username = properties.getProperty("eventhubspout.username");
            String password = properties.getProperty("eventhubspout.password");
            String namespaceName = properties
                    .getProperty("eventhubspout.namespace");
            String entityPath = properties.getProperty("eventhubspout.entitypath");
            String zkEndpointAddress = properties
                    .getProperty("zookeeper.connectionstring"); // opt
            int partitionCount = Integer.parseInt(properties
                    .getProperty("eventhubspout.partitions.count"));
            int checkpointIntervalInSeconds = Integer.parseInt(properties
                    .getProperty("eventhubspout.checkpoint.interval"));
            int receiverCredits = Integer.parseInt(properties
                    .getProperty("eventhub.receiver.credits")); // prefetch count
                                                                // (opt)
            System.out.println("Eventhub spout config: ");
            System.out.println("  partition count: " + partitionCount);
            System.out.println("  checkpoint interval: "
                    + checkpointIntervalInSeconds);
            System.out.println("  receiver credits: " + receiverCredits);
     
            spoutConfig = new EventHubSpoutConfig(username, password,
                    namespaceName, entityPath, partitionCount, zkEndpointAddress,
                    checkpointIntervalInSeconds, receiverCredits);
        
            // set the number of workers to be the same as partition number.
            // the idea is to have a spout and a logger bolt co-exist in one
            // worker to avoid shuffling messages across workers in storm cluster.
            numWorkers = spoutConfig.getPartitionCount();
        
            if (args.length > 0) {
                // set topology name so that sample Trident topology can use it as
                // stream name.
                spoutConfig.setTopologyName(args[0]);
            }
        }
        
        protected StormTopology buildTopology() {
            TopologyBuilder topologyBuilder = new TopologyBuilder();
       
            EventHubSpout eventHubSpout = new EventHubSpout(spoutConfig);
            topologyBuilder.setSpout("EventHubsSpout", eventHubSpout,
                    spoutConfig.getPartitionCount()).setNumTasks(
                    spoutConfig.getPartitionCount());
            topologyBuilder
                    .setBolt("LoggerBolt", new LoggerBolt(),
                            spoutConfig.getPartitionCount())
                    .localOrShuffleGrouping("EventHubsSpout")
                    .setNumTasks(spoutConfig.getPartitionCount());
            return topologyBuilder.createTopology();
        }
        
        protected void runScenario(String[] args) throws Exception {
            boolean runLocal = true;
            readEHConfig(args);
            StormTopology topology = buildTopology();
            Config config = new Config();
            config.setDebug(false);
        
            if (runLocal) {
                config.setMaxTaskParallelism(2);
                LocalCluster localCluster = new LocalCluster();
                localCluster.submitTopology("test", config, topology);
                Thread.sleep(5000000);
                localCluster.shutdown();
            } else {
                config.setNumWorkers(numWorkers);
                StormSubmitter.submitTopology(args[0], config, topology);
            }
        }
        
        public static void main(String[] args) throws Exception {
            LogTopology topology = new LogTopology();
            topology.runScenario(args);
        }
    }
    ```

    このクラスは、新しい Event Hub スパウトを作成して、これをインスタンス化するために構成ファイルのプロパティを使用します。 この例では、Event Hub で許可されている最大の数の並列処理を使用するために、その Event Hub のパーティションの数と同数のスパウト タスクを作成することが重要です。

## <a name="next-steps"></a>次の手順
Event Hubs の詳細については、次のリンク先を参照してください:

* [Event Hubs の概要][Event Hubs overview]
* [イベント ハブの作成](event-hubs-create.md)
* [Event Hubs の FAQ](event-hubs-faq.md)

<!-- Links -->
[Event Hubs overview]: event-hubs-what-is-event-hubs.md
[HDInsight Storm]: ../hdinsight/storm/apache-storm-overview.md
[イベント ハブでの HDInsight Storm の例]: https://azure.microsoft.com/resources/samples/hdinsight-java-storm-eventhub/

<!-- Images -->

[12]: ./media/event-hubs-get-started-receive-storm/create-storm1.png
