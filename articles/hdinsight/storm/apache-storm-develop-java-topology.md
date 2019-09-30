---
title: Apache Storm Java トポロジの例 - Azure HDInsight
description: ワード カウント トポロジの例を作成して、Java で Apache Storm トポロジを作成する方法について説明します。
author: hrasheed-msft
ms.reviewer: jasonh
keywords: apache storm,apache storm の例,storm java,storm トポロジの例
ms.service: hdinsight
ms.topic: conceptual
ms.date: 03/14/2019
ms.author: hrasheed
ms.custom: H1Hack27Feb2017,hdinsightactive,hdiseo17may2017
ms.openlocfilehash: 41ed51fc0cec9843525275613cca211d2e1bf409
ms.sourcegitcommit: 71db032bd5680c9287a7867b923bf6471ba8f6be
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/16/2019
ms.locfileid: "71018607"
---
# <a name="create-an-apache-storm-topology-in-java"></a>Java での Apache Storm トポロジの作成

[Apache Storm](https://storm.apache.org/) の Java ベースのトポロジを作成する方法について説明します。 ここでは、文字カウント アプリケーションを実装する Storm トポロジを作成します。 [Apache Maven](https://maven.apache.org/) を使用して、プロジェクトを構築およびパッケージ化します。 次に、[Apache Storm Flux](https://storm.apache.org/releases/2.0.0/flux.html) フレームワークを使用してトポロジを定義する方法を説明します。

このドキュメントの手順を完了したら、HDInsight で Apache Storm にトポロジをデプロイできます。

> [!NOTE]  
> このドキュメントで作成した Storm トポロジの例の完成版が [https://github.com/Azure-Samples/hdinsight-java-storm-wordcount](https://github.com/Azure-Samples/hdinsight-java-storm-wordcount) で入手できます。

## <a name="prerequisites"></a>前提条件

* [Java Developer Kit (JDK) バージョン 8](https://aka.ms/azure-jdks)

* Apache に従って適切に[インストール](https://maven.apache.org/install.html)された [Apache Maven](https://maven.apache.org/download.cgi)。  Maven は Java プロジェクトのプロジェクト ビルド システムです。

## <a name="test-environment"></a>テスト環境
この記事で使用された環境は、Windows 10 を実行しているコンピューターです。  コマンドはコマンド プロンプトで実行され、さまざまなファイルがメモ帳で編集されています。

コマンド プロンプトで以下のコマンドを入力して、作業環境を作成を作成します。

```cmd
mkdir C:\HDI
cd C:\HDI
```

## <a name="create-a-maven-project"></a>Maven プロジェクトを作成する

次のコマンドを使用して、**WordCount** という名前の Maven プロジェクトを作成します。

```cmd
mvn archetype:generate -DarchetypeArtifactId=maven-archetype-quickstart -DgroupId=com.microsoft.example -DartifactId=WordCount -DinteractiveMode=false

cd WordCount
mkdir resources
```

このコマンドは、基本的な Maven プロジェクトを含む `WordCount` という名前のディレクトリを現在の場所に作成します。 2 番目のコマンドでは、現在の作業ディレクトリを `WordCount` に変更します。 3 番目のコマンドでは、後で使用する新しいディレクトリ (`resources`) を作成します。  `WordCount` ディレクトリには、次の項目が含まれます。

* `pom.xml`:Maven プロジェクトの設定が含まれます。
* `src\main\java\com\microsoft\example`:アプリケーション コードが含まれます。
* `src\test\java\com\microsoft\example`:アプリケーションのテストが含まれます。  

### <a name="remove-the-generated-example-code"></a>生成されたコード例の削除

以下のコマンドを入力して、生成されたテストとアプリケーション ファイル `AppTest.java` と `App.java` を削除します。

```cmd
DEL src\main\java\com\microsoft\example\App.java
DEL src\test\java\com\microsoft\example\AppTest.java
```

## <a name="add-maven-repositories"></a>Maven レポジトリの追加

HDInsight は Hortonworks Data Platform (HDP) を基盤とするため、Hortonworks レポジトリを利用し、Apache Storm プロジェクトの依存関係をダウンロードすることをお勧めします。  

以下のコマンドを入力して `pom.xml` を開きます。

```cmd
notepad pom.xml
```

次に、`<url> https://maven.apache.org</url>` 行の後に以下の XML を追加します。

```xml
<repositories>
    <repository>
        <releases>
            <enabled>true</enabled>
            <updatePolicy>always</updatePolicy>
            <checksumPolicy>warn</checksumPolicy>
        </releases>
        <snapshots>
            <enabled>false</enabled>
            <updatePolicy>never</updatePolicy>
            <checksumPolicy>fail</checksumPolicy>
        </snapshots>
        <id>HDPReleases</id>
        <name>HDP Releases</name>
        <url>https://repo.hortonworks.com/content/repositories/releases/</url>
        <layout>default</layout>
    </repository>
    <repository>
        <releases>
            <enabled>true</enabled>
            <updatePolicy>always</updatePolicy>
            <checksumPolicy>warn</checksumPolicy>
        </releases>
        <snapshots>
            <enabled>false</enabled>
            <updatePolicy>never</updatePolicy>
            <checksumPolicy>fail</checksumPolicy>
        </snapshots>
        <id>HDPJetty</id>
        <name>Hadoop Jetty</name>
        <url>https://repo.hortonworks.com/content/repositories/jetty-hadoop/</url>
        <layout>default</layout>
    </repository>
</repositories>
```

## <a name="add-properties"></a>プロパティの追加

Maven では、プロパティと呼ばれるプロジェクト レベルの値を定義することができます。 `pom.xml` で、`</repositories>` 行の後に次のテキストを追加します。

```xml
<properties>
    <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
    <!--
    This is a version of Storm from the Hortonworks repository that is compatible with HDInsight 3.6.
    -->
    <storm.version>1.1.0.2.6.1.9-1</storm.version>
</properties>
```

これで、`pom.xml` の他のセクションでこの値を使用できるようになりました。 たとえば、Storm コンポーネントのバージョンを指定するときに、値をハードコーディングする代わりに `${storm.version}` を使用することができます。

## <a name="add-dependencies"></a>依存関係を追加する

Storm コンポーネントの依存関係を追加します。 `pom.xml` で、`<dependencies>` セクションの次のテキストを追加します。

```xml
<dependency>
    <groupId>org.apache.storm</groupId>
    <artifactId>storm-core</artifactId>
    <version>${storm.version}</version>
    <!-- keep storm out of the jar-with-dependencies -->
    <scope>provided</scope>
</dependency>
```

コンパイル時に、Maven がこの情報を使用して Maven レポジトリで `storm-core` を検索します。 まず、ローカル コンピューター上のレポジトリを検索します。 ファイルが見つからない場合、Maven はパブリック Maven レポジトリからファイルをダウンロードし、ローカル レポジトリに保存します。

> [!NOTE]  
> このセクションの `<scope>provided</scope>` 行に注目してください。 この設定によって、作成されるすべての JAR ファイルから **storm-core** を除外するよう Maven に指示しています。storm-core はシステムから提供されるためです。

## <a name="build-configuration"></a>ビルド構成

Maven プラグインでは、プロジェクトのビルド ステージをカスタマイズできます。 たとえば、プロジェクトのコンパイル方法や JAR ファイルへのパッケージ化方法をカスタマイズできます。 `pom.xml` で、`</project>` 行のすぐ上に次のテキストを追加します。

```xml
<build>
    <plugins>
    </plugins>
    <resources>
    </resources>
</build>
```

このセクションは、プラグインやリソース、他のビルド構成オプションを追加する際に使用します。 `pom.xml` ファイルの完全なリファレンスについては、[https://maven.apache.org/pom.html](https://maven.apache.org/pom.html) を参照してください。

### <a name="add-plug-ins"></a>プラグインの追加

* **Exec Maven プラグイン**

    Java で実行した Apache Storm トポロジの場合、[Exec Maven プラグイン](https://www.mojohaus.org/exec-maven-plugin/)が便利です。Exec Maven プラグインを使用すると、開発環境でトポロジをローカルに実行することが簡単にできます。 `pom.xml` ファイルの `<plugins>` セクションに次の内容を追加して Exec Maven プラグインを追加します。
    
    ```xml
    <plugin>
        <groupId>org.codehaus.mojo</groupId>
        <artifactId>exec-maven-plugin</artifactId>
        <version>1.6.0</version>
        <executions>
            <execution>
            <goals>
                <goal>exec</goal>
            </goals>
            </execution>
        </executions>
        <configuration>
            <executable>java</executable>
            <includeProjectDependencies>true</includeProjectDependencies>
            <includePluginDependencies>false</includePluginDependencies>
            <classpathScope>compile</classpathScope>
            <mainClass>${storm.topology}</mainClass>
            <cleanupDaemonThreads>false</cleanupDaemonThreads> 
        </configuration>
    </plugin>
    ```

* **Apache Maven Compiler プラグイン**

    別の役立つプラグインとして [Apache Maven Compiler プラグイン](https://maven.apache.org/plugins/maven-compiler-plugin/)があり、コンパイル オプションを変更するために使用します。 Maven によってアプリケーションのソースとターゲットに使用される Java バージョンを変更します。
    
  * HDInsight __3.4 以前__ の場合は、ソースとターゲットの Java バージョンを __1.7__ に設定します。
    
  * HDInsight __3.5__ の場合は、ソースとターゲットの Java バージョンを __1.8__ に設定します。
    
    `pom.xml` ファイルの `<plugins>` セクションに次のテキストを追加して、Apache Maven Compiler プラグインを追加します。 この例では 1.8 を指定しているので、ターゲット HDInsight のバージョンは 3.5 になります。
    
    ```xml
    <plugin>
      <groupId>org.apache.maven.plugins</groupId>
      <artifactId>maven-compiler-plugin</artifactId>
      <version>3.3</version>
      <configuration>
      <source>1.8</source>
      <target>1.8</target>
      </configuration>
    </plugin>
    ```

### <a name="configure-resources"></a>Configure resources

resources セクションには、トポロジ内のコンポーネントに必要な構成ファイルなどの非コード リソースを格納することができます。 この例では、`pom.xml` ファイルの `<resources>` セクションに次のテキストを追加します。

```xml
<resource>
    <directory>${basedir}/resources</directory>
    <filtering>false</filtering>
    <includes>
        <include>log4j2.xml</include>
    </includes>
</resource>
```

この例では、リソースを格納する場所として、プロジェクト (`${basedir}`) のルートに resources ディレクトリを追加し、`log4j2.xml` という名前のファイルを含めます。 このファイルは、トポロジでどの情報をログに記録するかを構成する際に使用されます。

## <a name="create-the-topology"></a>トポロジを作成する

Java ベースの Apache Storm トポロジは、作成か依存関係として参照する必要のある 3 つのコンポーネントで構成されます。

* **スパウト**:外部ソースからデータを読み取り、データのストリームをトポロジに出力します。

* **ボルト**:スパウトや他のボルトから出力されたストリームの処理を実行し、1 つ以上のストリームを出力します。

* **トポロジ**:スパウトとボルトの配置方法を定義し、トポロジのエントリ ポイントを提供します。

### <a name="create-the-spout"></a>スパウトを作成する

外部データソースの設定に必要な要件を軽減するため、次のスパウトは単純にランダムにセンテンスを出力します。 これは、 [Storm-Starter のサンプル](https://github.com/apache/storm/blob/0.10.x-branch/examples/storm-starter/src/jvm/storm/starter)で提供されているスパウトを変更したバージョンです。  このトポロジでは 1 つのスパウトのみを使用していますが、場合によっては異なるソースからトポロジにデータを供給するため複数のスパウトを使用することもあります。

以下のコマンドを入力して、新しいファイル `RandomSentenceSpout.java` を作成して開きます。

```cmd
notepad src\main\java\com\microsoft\example\RandomSentenceSpout.java
```

次に、以下の Java コードをコピーして新しいファイルに貼り付けます。  その後、ファイルを閉じます。

```java
package com.microsoft.example;

import org.apache.storm.spout.SpoutOutputCollector;
import org.apache.storm.task.TopologyContext;
import org.apache.storm.topology.OutputFieldsDeclarer;
import org.apache.storm.topology.base.BaseRichSpout;
import org.apache.storm.tuple.Fields;
import org.apache.storm.tuple.Values;
import org.apache.storm.utils.Utils;

import java.util.Map;
import java.util.Random;

//This spout randomly emits sentences
public class RandomSentenceSpout extends BaseRichSpout {
  //Collector used to emit output
  SpoutOutputCollector _collector;
  //Used to generate a random number
  Random _rand;

  //Open is called when an instance of the class is created
  @Override
  public void open(Map conf, TopologyContext context, SpoutOutputCollector collector) {
  //Set the instance collector to the one passed in
    _collector = collector;
    //For randomness
    _rand = new Random();
  }

  //Emit data to the stream
  @Override
  public void nextTuple() {
  //Sleep for a bit
    Utils.sleep(100);
    //The sentences that are randomly emitted
    String[] sentences = new String[]{ "the cow jumped over the moon", "an apple a day keeps the doctor away",
        "four score and seven years ago", "snow white and the seven dwarfs", "i am at two with nature" };
    //Randomly pick a sentence
    String sentence = sentences[_rand.nextInt(sentences.length)];
    //Emit the sentence
    _collector.emit(new Values(sentence));
  }

  //Ack is not implemented since this is a basic example
  @Override
  public void ack(Object id) {
  }

  //Fail is not implemented since this is a basic example
  @Override
  public void fail(Object id) {
  }

  //Declare the output fields. In this case, an sentence
  @Override
  public void declareOutputFields(OutputFieldsDeclarer declarer) {
    declarer.declare(new Fields("sentence"));
  }
}
```

> [!NOTE]  
> 外部データソースから読み取るスパウトの例を見るには、次の例をご覧ください。
>
> * [TwitterSampleSPout](https://github.com/apache/storm/blob/0.10.x-branch/examples/storm-starter/src/jvm/storm/starter/spout/TwitterSampleSpout.java):Twitter から読み取りを行うスパウトの例。
> * [Storm-Kafka](https://github.com/apache/storm/tree/0.10.x-branch/external/storm-kafka):Kafka から読み取りを行うスパウトの例。


### <a name="create-the-bolts"></a>ボルトを作成する

ボルトは、データの処理を扱います。 ボルトは、たとえば、計算、永続化、外部コンポーネントとの対話など、あらゆる操作が可能です。 このトポロジでは、次の 2 つのボルトを使用します。

* **SplitSentence**:**RandomSentenceSpout** から出力されたセンテンスを個別の単語に分割します。

* **WordCount**:各単語が発生した回数をカウントします。


#### <a name="splitsentence"></a>SplitSentence

以下のコマンドを入力して、新しいファイル `SplitSentence.java` を作成して開きます。

```cmd
notepad src\main\java\com\microsoft\example\SplitSentence.java
```

次に、以下の Java コードをコピーして新しいファイルに貼り付けます。  その後、ファイルを閉じます。

```java
package com.microsoft.example;

import java.text.BreakIterator;

import org.apache.storm.topology.BasicOutputCollector;
import org.apache.storm.topology.OutputFieldsDeclarer;
import org.apache.storm.topology.base.BaseBasicBolt;
import org.apache.storm.tuple.Fields;
import org.apache.storm.tuple.Tuple;
import org.apache.storm.tuple.Values;

//There are a variety of bolt types. In this case, use BaseBasicBolt
public class SplitSentence extends BaseBasicBolt {

  //Execute is called to process tuples
  @Override
  public void execute(Tuple tuple, BasicOutputCollector collector) {
    //Get the sentence content from the tuple
    String sentence = tuple.getString(0);
    //An iterator to get each word
    BreakIterator boundary=BreakIterator.getWordInstance();
    //Give the iterator the sentence
    boundary.setText(sentence);
    //Find the beginning first word
    int start=boundary.first();
    //Iterate over each word and emit it to the output stream
    for (int end=boundary.next(); end != BreakIterator.DONE; start=end, end=boundary.next()) {
      //get the word
      String word=sentence.substring(start,end);
      //If a word is whitespace characters, replace it with empty
      word=word.replaceAll("\\s+","");
      //if it's an actual word, emit it
      if (!word.equals("")) {
        collector.emit(new Values(word));
      }
    }
  }

  //Declare that emitted tuples contain a word field
  @Override
  public void declareOutputFields(OutputFieldsDeclarer declarer) {
    declarer.declare(new Fields("word"));
  }
}
```

#### <a name="wordcount"></a>WordCount

以下のコマンドを入力して、新しいファイル `WordCount.java` を作成して開きます。

```cmd
notepad src\main\java\com\microsoft\example\WordCount.java
```

次に、以下の Java コードをコピーして新しいファイルに貼り付けます。  その後、ファイルを閉じます。

```java
package com.microsoft.example;

import java.util.HashMap;
import java.util.Map;
import java.util.Iterator;

import org.apache.storm.Constants;
import org.apache.storm.topology.BasicOutputCollector;
import org.apache.storm.topology.OutputFieldsDeclarer;
import org.apache.storm.topology.base.BaseBasicBolt;
import org.apache.storm.tuple.Fields;
import org.apache.storm.tuple.Tuple;
import org.apache.storm.tuple.Values;
import org.apache.storm.Config;

// For logging
import org.apache.logging.log4j.Logger;
import org.apache.logging.log4j.LogManager;

//There are a variety of bolt types. In this case, use BaseBasicBolt
public class WordCount extends BaseBasicBolt {
  //Create logger for this class
  private static final Logger logger = LogManager.getLogger(WordCount.class);
  //For holding words and counts
  Map<String, Integer> counts = new HashMap<String, Integer>();
  //How often to emit a count of words
  private Integer emitFrequency;

  // Default constructor
  public WordCount() {
      emitFrequency=5; // Default to 60 seconds
  }

  // Constructor that sets emit frequency
  public WordCount(Integer frequency) {
      emitFrequency=frequency;
  }

  //Configure frequency of tick tuples for this bolt
  //This delivers a 'tick' tuple on a specific interval,
  //which is used to trigger certain actions
  @Override
  public Map<String, Object> getComponentConfiguration() {
      Config conf = new Config();
      conf.put(Config.TOPOLOGY_TICK_TUPLE_FREQ_SECS, emitFrequency);
      return conf;
  }

  //execute is called to process tuples
  @Override
  public void execute(Tuple tuple, BasicOutputCollector collector) {
    //If it's a tick tuple, emit all words and counts
    if(tuple.getSourceComponent().equals(Constants.SYSTEM_COMPONENT_ID)
            && tuple.getSourceStreamId().equals(Constants.SYSTEM_TICK_STREAM_ID)) {
      for(String word : counts.keySet()) {
        Integer count = counts.get(word);
        collector.emit(new Values(word, count));
        logger.info("Emitting a count of " + count + " for word " + word);
      }
    } else {
      //Get the word contents from the tuple
      String word = tuple.getString(0);
      //Have we counted any already?
      Integer count = counts.get(word);
      if (count == null)
        count = 0;
      //Increment the count and store it
      count++;
      counts.put(word, count);
    }
  }

  //Declare that this emits a tuple containing two fields; word and count
  @Override
  public void declareOutputFields(OutputFieldsDeclarer declarer) {
    declarer.declare(new Fields("word", "count"));
  }
}
```

### <a name="define-the-topology"></a>トポロジの定義

トポロジは、コンポーネント間のデータのフローを定義するグラフにスパウトとボルトを結びつけます。 また、クラスター内にコンポーネントのインスタンスを作成するときに Storm が使用する並列処理のヒントも提供します。

次の図は、このトポロジのコンポーネントの基本的なグラフを示しています。

![スパウトとボルトの配置を示すダイアグラム](./media/apache-storm-develop-java-topology/word-count-topology1.png)

トポロジを実装するには、以下のコマンドを入力して、新しいファイル `WordCountTopology.java` を作成して開きます。

```cmd
notepad src\main\java\com\microsoft\example\WordCountTopology.java
```

次に、以下の Java コードをコピーして新しいファイルに貼り付けます。  その後、ファイルを閉じます。

```java
package com.microsoft.example;

import org.apache.storm.Config;
import org.apache.storm.LocalCluster;
import org.apache.storm.StormSubmitter;
import org.apache.storm.topology.TopologyBuilder;
import org.apache.storm.tuple.Fields;

import com.microsoft.example.RandomSentenceSpout;

public class WordCountTopology {

  //Entry point for the topology
  public static void main(String[] args) throws Exception {
  //Used to build the topology
    TopologyBuilder builder = new TopologyBuilder();
    //Add the spout, with a name of 'spout'
    //and parallelism hint of 5 executors
    builder.setSpout("spout", new RandomSentenceSpout(), 5);
    //Add the SplitSentence bolt, with a name of 'split'
    //and parallelism hint of 8 executors
    //shufflegrouping subscribes to the spout, and equally distributes
    //tuples (sentences) across instances of the SplitSentence bolt
    builder.setBolt("split", new SplitSentence(), 8).shuffleGrouping("spout");
    //Add the counter, with a name of 'count'
    //and parallelism hint of 12 executors
    //fieldsgrouping subscribes to the split bolt, and
    //ensures that the same word is sent to the same instance (group by field 'word')
    builder.setBolt("count", new WordCount(), 12).fieldsGrouping("split", new Fields("word"));

    //new configuration
    Config conf = new Config();
    //Set to false to disable debug information when
    // running in production on a cluster
    conf.setDebug(false);

    //If there are arguments, we are running on a cluster
    if (args != null && args.length > 0) {
      //parallelism hint to set the number of workers
      conf.setNumWorkers(3);
      //submit the topology
      StormSubmitter.submitTopology(args[0], conf, builder.createTopology());
    }
    //Otherwise, we are running locally
    else {
      //Cap the maximum number of executors that can be spawned
      //for a component to 3
      conf.setMaxTaskParallelism(3);
      //LocalCluster is used to run locally
      LocalCluster cluster = new LocalCluster();
      //submit the topology
      cluster.submitTopology("word-count", conf, builder.createTopology());
      //sleep
      Thread.sleep(10000);
      //shut down the cluster
      cluster.shutdown();
    }
  }
}
```

### <a name="configure-logging"></a>ログの構成

Storm は、[Apache Log4j 2](https://logging.apache.org/log4j/2.x/) を使用して情報をログに記録します。 ログ記録を構成していない場合、トポロジによって診断情報が出力されます。 ログ記録の内容を制御するには、以下のコマンドを入力して、`resources` ディレクトリに `log4j2.xml` という名前のファイルを作成します。

```cmd
notepad resources\log4j2.xml
```

次に、以下の XML テキストをコピーして新しいファイルに貼り付けます。  その後、ファイルを閉じます。

```xml
<?xml version="1.0" encoding="UTF-8"?>
<Configuration>
<Appenders>
    <Console name="STDOUT" target="SYSTEM_OUT">
        <PatternLayout pattern="%d{HH:mm:ss} [%t] %-5level %logger{36} - %msg%n"/>
    </Console>
</Appenders>
<Loggers>
    <Logger name="com.microsoft.example" level="trace" additivity="false">
        <AppenderRef ref="STDOUT"/>
    </Logger>
    <Root level="error">
        <Appender-Ref ref="STDOUT"/>
    </Root>
</Loggers>
</Configuration>
```

この XML により、`com.microsoft.example` クラスの新しいロガーが構成されます。このクラスには、この例のトポロジのコンポーネントが含まれます。 レベルは、このロガーをトレースするように設定されているため、このトポロジ内のコンポーネントから出力されるすべてのログ情報がキャプチャされます。

`<Root level="error">` セクションは、エラー情報のみを記録するように、ログ記録 (`com.microsoft.example` にないすべて) のルート レベルを構成します。

Log4j 2 のログ記録の構成の詳細については、[https://logging.apache.org/log4j/2.x/manual/configuration.html](https://logging.apache.org/log4j/2.x/manual/configuration.html) を参照してください。

> [!NOTE]  
> Storm バージョン 0.10.0 以降では Log4j 2.x が使用されます。 以前のバージョンの Storm では Log4j 1.x が使用されていました。これには、ログの構成に別の形式が使用されていました。 古い構成については、[https://wiki.apache.org/logging-log4j/Log4jXmlFormat](https://wiki.apache.org/logging-log4j/Log4jXmlFormat)を参照してください。

## <a name="test-the-topology-locally"></a>ローカルでのトポロジのテスト

ファイルを保存したら、次のコマンドを使用してトポロジをローカルでテストします。

```cmd
mvn compile exec:java -Dstorm.topology=com.microsoft.example.WordCountTopology
```

実行中、スタートアップ情報が表示されます。 次のテキストは、ワード カウントの出力例を示しています。

    17:33:27 [Thread-12-count] INFO  com.microsoft.example.WordCount - Emitting a count of 56 for word snow
    17:33:27 [Thread-12-count] INFO  com.microsoft.example.WordCount - Emitting a count of 56 for word white
    17:33:27 [Thread-12-count] INFO  com.microsoft.example.WordCount - Emitting a count of 112 for word seven
    17:33:27 [Thread-16-count] INFO  com.microsoft.example.WordCount - Emitting a count of 195 for word the
    17:33:27 [Thread-30-count] INFO  com.microsoft.example.WordCount - Emitting a count of 113 for word and
    17:33:27 [Thread-30-count] INFO  com.microsoft.example.WordCount - Emitting a count of 57 for word dwarfs
    17:33:27 [Thread-12-count] INFO  com.microsoft.example.WordCount - Emitting a count of 57 for word snow

この例のログは、単語 and が 113 回出力されたことを示しています。 スパウトから同じセンテンスが継続的に出力されるため、トポロジが実行される限り、カウントは上がり続けます。

単語とカウントは 5 秒間隔で出力されます。 **WordCount** コンポーネントは、tick タプルを受信したときにのみ情報を出力するように構成されており、 これらのタプルが 5 秒ごとにしか配信されないことを要求します。

## <a name="convert-the-topology-to-flux"></a>トポロジを Flux に変換する

[Flux](https://storm.apache.org/releases/2.0.0/flux.html) は、構成と実装を分離するために使用できる、Storm 0.10.0 以降で使用可能な新しいフレームワークです。 コンポーネントは現在も Java で定義しますが、トポロジは YAML ファイルを使用して定義します。 プロジェクトと共に既定のトポロジ定義をパッケージ化したり、トポロジの送信時にスタンドアロンのファイルを使用したりすることができます。 トポロジを Storm に送信するときに、環境変数または構成ファイルを使用して、YAML トポロジの定義に値を設定できます。

YAML ファイルは、トポロジと、これらの間のデータ フローに使用するコンポーネントを定義します。 jar ファイルの一部として YAML ファイルを含めることも、外部 YAML ファイルを使用することもできます。

Flux の詳細については、「[Flux フレームワーク (https://storm.apache.org/releases/current/flux.html)](https://storm.apache.org/releases/current/flux.html)」に関するセクションを参照してください。

> [!WARNING]  
> Storm 1.0.1 での[バグ (https://issues.apache.org/jira/browse/STORM-2055)](https://issues.apache.org/jira/browse/STORM-2055) のため、Flux トポロジをローカルに実行するには [Storm 開発環境のインストール](https://storm.apache.org/releases/current/Setting-up-development-environment.html)が必要になる場合があります。

1. 以前は、`WordCountTopology.java` でトポロジを定義していましたが、Flux では必要ありません。 次のコマンドを使用して、このファイルを削除します。

    ```cmd
    DEL src\main\java\com\microsoft\example\WordCountTopology.java
    ```

2. 以下のコマンドを入力して、新しいファイル `topology.yaml` を作成して開きます。

    ```cmd
    notepad resources\topology.yaml
    ```

    次に、以下のテキストをコピーして新しいファイルに貼り付けます。  その後、ファイルを閉じます。

    ```yaml
    name: "wordcount"       # friendly name for the topology

    config:                 # Topology configuration
      topology.workers: 1     # Hint for the number of workers to create

    spouts:                 # Spout definitions
    - id: "sentence-spout"
      className: "com.microsoft.example.RandomSentenceSpout"
      parallelism: 1      # parallelism hint

    bolts:                  # Bolt definitions
    - id: "splitter-bolt"
      className: "com.microsoft.example.SplitSentence"
      parallelism: 1
        
    - id: "counter-bolt"
      className: "com.microsoft.example.WordCount"
      constructorArgs:
        - 10
      parallelism: 1

    streams:                # Stream definitions
    - name: "Spout --> Splitter" # name isn't used (placeholder for logging, UI, etc.)
      from: "sentence-spout"       # The stream emitter
      to: "splitter-bolt"          # The stream consumer
      grouping:                    # Grouping type
        type: SHUFFLE
    
    - name: "Splitter -> Counter"
      from: "splitter-bolt"
      to: "counter-bolt"
      grouping:
        type: FIELDS
        args: ["word"]           # field(s) to group on
    ```

3. 以下のコマンドを入力して `pom.xml` を作成し、以下に示されている変更を加えます。

    ```cmd
    notepad pom.xml
    ```

   * `<dependencies>` セクションに次の新しい依存関係を追加します。

        ```xml
        <!-- Add a dependency on the Flux framework -->
        <dependency>
            <groupId>org.apache.storm</groupId>
            <artifactId>flux-core</artifactId>
            <version>${storm.version}</version>
        </dependency>
        ```

   * `<plugins>` セクションに次のプラグインを追加します。 このプラグインによって、プロジェクトのパッケージ (jar ファイル) が作成されます。また、このパッケージを作成するときに Flux 固有の変換がいくつか適用されます。

        ```xml
        <!-- build an uber jar -->
        <plugin>
            <groupId>org.apache.maven.plugins</groupId>
            <artifactId>maven-shade-plugin</artifactId>
            <version>3.2.1</version>
            <configuration>
                <transformers>
                    <!-- Keep us from getting a "can't overwrite file error" -->
                    <transformer implementation="org.apache.maven.plugins.shade.resource.ApacheLicenseResourceTransformer" />
                    <transformer implementation="org.apache.maven.plugins.shade.resource.ServicesResourceTransformer" />
                    <!-- We're using Flux, so refer to it as main -->
                    <transformer implementation="org.apache.maven.plugins.shade.resource.ManifestResourceTransformer">
                        <mainClass>org.apache.storm.flux.Flux</mainClass>
                    </transformer>
                </transformers>
                <!-- Keep us from getting a bad signature error -->
                <filters>
                    <filter>
                        <artifact>*:*</artifact>
                        <excludes>
                            <exclude>META-INF/*.SF</exclude>
                            <exclude>META-INF/*.DSA</exclude>
                            <exclude>META-INF/*.RSA</exclude>
                        </excludes>
                    </filter>
                </filters>
            </configuration>
            <executions>
                <execution>
                    <phase>package</phase>
                    <goals>
                        <goal>shade</goal>
                    </goals>
                </execution>
            </executions>
        </plugin>
        ```

   * **exec-maven-plugin** の `<configuration>` セクションで、`<mainClass>` の値を `${storm.topology}` から `org.apache.storm.flux.Flux` に変更します。 この設定により、開発中にトポロジのローカルでの実行を Flux で処理できるようになります。

   * `<resources>` セクションの `<includes>` に以下を追加します。 これで XML に、プロジェクトの一部としてトポロジを定義する YAML ファイルがインクルードされます。

        ```xml
        <include>topology.yaml</include>
        ```

## <a name="test-the-flux-topology-locally"></a>ローカルでの Flux トポロジのテスト

1. 次のコマンドを入力し、Maven を使用して Flux トポロジをコンパイルし、実行します。

    ```cmd
    mvn compile exec:java -Dexec.args="--local -R /topology.yaml"
    ```

    > [!WARNING]  
    > トポロジが Storm 1.0.1 ビットを使っている場合、このコマンドは失敗します。 このエラーは[https://issues.apache.org/jira/browse/STORM-2055](https://issues.apache.org/jira/browse/STORM-2055)が原因です。 代わりに、[開発環境に Storm をインストール](https://storm.apache.org/releases/current/Setting-up-development-environment.html)し、次の手順を使ってください。
    >
    > [開発環境に Storm がインストールされている](https://storm.apache.org/releases/current/Setting-up-development-environment.html)場合、代わりに次のコマンドを使うことができます。
    >
    > ```cmd
    > mvn compile package
    > storm jar target/WordCount-1.0-SNAPSHOT.jar org.apache.storm.flux.Flux --local -R /topology.yaml
    > ```

    `--local` パラメーターを指定すると、開発環境でトポロジがローカル モードで実行されます。 `-R /topology.yaml` パラメーターを指定すると、トポロジの定義に jar ファイルの `topology.yaml` ファイル リソースが使用されます。

    実行中、スタートアップ情報が表示されます。 次のテキストは出力例を示しています。

        17:33:27 [Thread-12-count] INFO  com.microsoft.example.WordCount - Emitting a count of 56 for word snow
        17:33:27 [Thread-12-count] INFO  com.microsoft.example.WordCount - Emitting a count of 56 for word white
        17:33:27 [Thread-12-count] INFO  com.microsoft.example.WordCount - Emitting a count of 112 for word seven
        17:33:27 [Thread-16-count] INFO  com.microsoft.example.WordCount - Emitting a count of 195 for word the
        17:33:27 [Thread-30-count] INFO  com.microsoft.example.WordCount - Emitting a count of 113 for word and
        17:33:27 [Thread-30-count] INFO  com.microsoft.example.WordCount - Emitting a count of 57 for word dwarfs

    ログに記録された情報のバッチの間隔が 10 秒遅延します。

2. プロジェクトから新しいトポロジ yaml を作成します。
 
    a. 以下のコマンドを入力して、`topology.xml` を作成します。

    ```cmd
    notepad resources\topology.yaml
    ```

    b. 次のセクションを見つけて、値 `10` を `5` に変更します。 この変更により、単語のカウントの出力バッチの間隔が 10 秒から 5 秒に変更されます。  

    ```yaml
    - id: "counter-bolt"
      className: "com.microsoft.example.WordCount"
      constructorArgs:
        - 5
      parallelism: 1  
    ```  

    c. `newtopology.yaml` という名前を付けてファイルを保存します。

3. トポロジを実行するには、次のコマンドを入力します。

    ```cmd
    mvn exec:java -Dexec.args="--local resources/newtopology.yaml"
    ```

    または、開発環境に Storm がある場合は次のようにします。

    ```cmd
    storm jar target/WordCount-1.0-SNAPSHOT.jar org.apache.storm.flux.Flux --local resources/newtopology.yaml
    ```

     このコマンドでは、トポロジの定義として `newtopology.yaml` を使用します。 `compile` パラメーターを含めなかったため、前の手順でビルドしたプロジェクトのバージョンが使用されます。

    トポロジが開始されると、バッチが出力される時間間隔が変更され、`newtopology.yaml` の値が反映されていることがわかります。 このように、トポロジを再コンパイルしなくても YAML ファイルから構成を変更できることがわかります。

Flux フレームワークのその他の機能の詳細については、 [Flux(https://storm.apache.org/releases/current/flux.html)](https://storm.apache.org/releases/current/flux.html)に関するページをご覧ください。

## <a name="trident"></a>Trident

[Trident](https://storm.apache.org/releases/current/Trident-API-Overview.html) は、Storm によって提供される高レベルの抽象化です。 ステートフルな処理をサポートします。 Trident の主なメリットは、トポロジが受けるすべてのメッセージが一度しか処理されないよう保証できることです。 Trident を使用しないと、トポロジで保証されるのは、メッセージが少なくとも一度は処理されることのみです。 他にも、ボルトを作成する代わりに使える組み込みのコンポーネントがあるなどの違いがあります。 実際には、ボルトはフィルター、プロジェクション、関数などの汎用性の低いコンポーネントに置き換えられます。

Trident アプリケーションは Maven プロジェクトを使用して作成できます。 この記事で前述した同じ基本の手順の、コードのみを変更して作成できます。 Trident も (現在は) Flux フレームワークでは使用できません。

Trident の詳細については、「[Trident API の概要](https://storm.apache.org/releases/current/Trident-API-Overview.html)」のページをご覧ください。

## <a name="next-steps"></a>次の手順

Java を使用して Apache Storm トポロジを作成する方法を学習してきました。 続けて次の記事もご覧ください。

* [HDInsight での Apache Storm トポロジのデプロイと管理](apache-storm-deploy-monitor-topology-linux.md)

* [Visual Studio を使用して HDInsight で Apache Storm の C# トポロジを開発する](apache-storm-develop-csharp-visual-studio-topology.md)

「[HDInsight での Apache Storm のトポロジ例](apache-storm-example-topology.md)」を参照することによって、その他の Apache Storm トポロジの例を見つけることができます。
