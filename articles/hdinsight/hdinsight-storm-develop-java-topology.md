---
title: "Apache Storm の Java ベースのトポロジの開発 | Microsoft Docs"
description: "簡単なワード カウント トポロジを作成して、Java で Storm トポロジを作成する方法について説明します。"
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: a8838f29-9c08-4fd9-99ef-26655d1bf6d7
ms.service: hdinsight
ms.devlang: java
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 03/21/2017
ms.author: larryfr
ms.custom: H1Hack27Feb2017,hdinsightactive
translationtype: Human Translation
ms.sourcegitcommit: 6d749e5182fbab04adc32521303095dab199d129
ms.openlocfilehash: 183425e296f91bba47094c9b35be67fb6299c569
ms.lasthandoff: 03/22/2017

---
# <a name="use-maven-to-develop-a-java-based-word-count-topology-for-storm-on-hdinsight"></a>Maven を使用して HDInsight で Storm の Java ベースのワード カウント トポロジを開発する

Maven を使用して HDInsight で Apache Storm の Java ベース トポロジを作成する方法を説明します。 Maven と Java を使用して基本的なワード カウント アプリケーションを作成します (ここでは、トポロジは Java で定義します)。 次に、Flux フレームワークを使用してトポロジを定義する方法を説明します。

> [!NOTE]
> Flux フレームワークは、Storm 0.10.0 以降で利用可能です。 Storm 0.10.0 は、HDInsight 3.3 および 3.4 と使用できます。


このドキュメントの手順を完了したら、HDInsight で Apache Storm にトポロジをデプロイできます。

> [!NOTE]
> このドキュメントで作成するトポロジの完全バージョンは、 [https://github.com/Azure-Samples/hdinsight-java-storm-wordcount](https://github.com/Azure-Samples/hdinsight-java-storm-wordcount)で入手できます。

## <a name="prerequisites"></a>前提条件

* [Java Developer Kit (JDK) バージョン 7](https://www.oracle.com/technetwork/java/javase/downloads/jdk7-downloads-1880260.html)

* [Maven (https://maven.apache.org/download.cgi)](https://maven.apache.org/download.cgi): Maven は Java プロジェクトのプロジェクト ビルド システムです。

* テキスト エディターまたは IDE

## <a name="configure-environment-variables"></a>環境変数を構成する

Java と JDK をインストールするときに、次のような環境変数が設定される場合があります。 ただし、これらが存在するかどうかや、システムに対して適切な値が含まれているかを確認する必要があります。

* **JAVA_HOME** - Java ランタイム環境 (JRE) がインストールされているディレクトリを指している必要があります。 たとえば、Unix や Linux ディストリビューションの場合は、 `/usr/lib/jvm/java-7-oracle`のような値になります。 Windows の場合は、 `c:\Program Files (x86)\Java\jre1.7`

* **PATH** - 次のパスを含む必要があります。

  * **JAVA_HOME** または同等のパス

  * **JAVA_HOME\bin** または同等のパス

  * Maven がインストールされているディレクトリ

## <a name="create-a-maven-project"></a>Maven プロジェクトを作成する

コマンド ラインで、次のコマンドを使用して **WordCount** という名前の Maven プロジェクトを作成します。

    mvn archetype:generate -DarchetypeArtifactId=maven-archetype-quickstart -DgroupId=com.microsoft.example -DartifactId=WordCount -DinteractiveMode=false

> [!NOTE]
> PowerShell を使用している場合は、`-D` パラメーターを引用符で囲む必要があります。
>
> `mvn archetype:generate "-DarchetypeArtifactId=maven-archetype-quickstart" "-DgroupId=com.microsoft.example" "-DartifactId=WordCount" "-DinteractiveMode=false"`

このコマンドは、基本的な Maven プロジェクトを含む `WordCount` という名前のディレクトリを現在の場所に作成します。

`WordCount` ディレクトリには、次の項目が含まれます。

* `pom.xml`: Maven プロジェクトの設定が含まれます。
* `src\main\java\com\microsoft\example`: アプリケーション コードが含まれます。
* `src\test\java\com\microsoft\example`: アプリケーションのテストが含まれます。 

### <a name="remove-the-example-code"></a>サンプル コードを削除する

生成されたテストとアプリケーション ファイルを削除します。

* **src\test\java\com\microsoft\example\AppTest.java**
* **src\main\java\com\microsoft\example\App.java**

## <a name="add-properties"></a>プロパティの追加

Maven では、プロパティと呼ばれるプロジェクト レベルの値を定義することができます。 `<url>http://maven.apache.org</url>` 行の後に次のテキストを追加します。

```xml
<properties>
    <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
    <!--
    Storm 0.10.0 is for HDInsight 3.3 and 3.4.
    To find the version information for earlier HDInsight cluster
    versions, see https://azure.microsoft.com/en-us/documentation/articles/hdinsight-component-versioning/
    -->
    <storm.version>0.10.0</storm.version>
</properties>
```

これで、`pom.xml` の他のセクションでこれらの値を使用できるようになりました。 たとえば、Storm コンポーネントのバージョンを指定するときに、値をハードコーディングする代わりに `${storm.version}` を使用することができます。

## <a name="add-dependencies"></a>依存関係を追加する

Storm コンポーネントの依存関係を追加する必要があります。 `pom.xml` ファイルを開き、`<dependencies>` セクションで次のコードを追加します。

```xml
<dependency>
    <groupId>org.apache.storm</groupId>
    <artifactId>storm-core</artifactId>
    <version>${storm.version}</version>
    <!-- keep storm out of the jar-with-dependencies -->
    <scope>provided</scope>
</dependency>
```

コンパイル時に、Maven がこの情報を使用して Maven レポジトリで **storm-core** を検索します。 まず、ローカル コンピューター上のレポジトリを検索します。 ファイルが見つからない場合、Maven はパブリック Maven リポジトリからファイルをダウンロードし、ローカル リポジトリに保存します。

> [!NOTE]
> このセクションの `<scope>provided</scope>` 行に注目してください。 この設定によって、作成されるすべての JAR ファイルから **storm-core** を除外するよう Maven に指示しています。storm-core はシステムから提供されるためです。

## <a name="build-configuration"></a>ビルド構成

Maven プラグインでは、プロジェクトのコンパイル方法や、JAR ファイルへのパッケージ方法といったプロジェクトのビルド ステージをカスタマイズできます。 `pom.xml` ファイルを開いて、`</project>` 行のすぐ上に次のコードを追加します。

```xml
<build>
    <plugins>
    </plugins>
    <resources>
    </resources>
</build>
```

このセクションは、プラグインやリソース、他のビルド構成オプションを追加する際に使用します。 **pom.xml** ファイルの詳細については、[http://maven.apache.org/pom.html](http://maven.apache.org/pom.html) をご覧ください。

### <a name="add-plug-ins"></a>プラグインの追加

Storm トポロジの場合、[Exec Maven プラグイン](http://mojo.codehaus.org/exec-maven-plugin/)が便利です。Exec Maven プラグインを使用すると、開発環境でトポロジをローカルに実行することが簡単にできます。 `pom.xml` ファイルの `<plugins>` セクションに次の内容を追加して Exec Maven プラグインを追加します。

```xml
<plugin>
    <groupId>org.codehaus.mojo</groupId>
    <artifactId>exec-maven-plugin</artifactId>
    <version>1.4.0</version>
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
    </configuration>
</plugin>
```

別の役立つプラグインとして [Apache Maven Compiler プラグイン](http://maven.apache.org/plugins/maven-compiler-plugin/)があり、コンパイル オプションを変更するために使用します。 Maven がアプリケーションのソースとターゲットに使用する Java バージョンが変更されます。

* HDInsight __3.4 以前__の場合は、ソースとターゲットの Java バージョンを __1.7__ に設定します。

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

resources セクションには、トポロジ内のコンポーネントに必要な構成ファイルなどの非コード リソースを格納することができます。 この例では、`pom.xml ファイルの `<resources>` セクションに次のテキストを追加します。

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

Java ベースの Storm トポロジは、作成か依存関係として参照する必要のある 3 つのコンポーネントで構成されます。

* **スパウト**: 外部ソースからデータを読み取り、データのストリームをトポロジに出力します。

* **ボルト**: スパウトや他のボルトから出力されたストリームの処理を実行し、1 つ以上のストリームを出力します。

* **トポロジ**: スパウトとボルトの配置方法を定義し、トポロジのエントリ ポイントを提供します。

### <a name="create-the-spout"></a>スパウトを作成する

外部データソースの設定に必要な要件を軽減するため、次のスパウトは単純にランダムにセンテンスを出力します。 これは、 [Storm-Starter のサンプル](https://github.com/apache/storm/blob/0.10.x-branch/examples/storm-starter/src/jvm/storm/starter)で提供されているスパウトを変更したバージョンです。

> [!NOTE]
> 外部データソースから読み取るスパウトの例を見るには、次の例をご覧ください。
> 
> * [TwitterSampleSpout](https://github.com/apache/storm/blob/0.10.x-branch/examples/storm-starter/src/jvm/storm/starter/spout/TwitterSampleSpout.java): Twitter から読み取りを行うスパウトの例
> * [Storm Kafka](https://github.com/apache/storm/tree/0.10.x-branch/external/storm-kafka): Kafka から読み取りを行うスパウトの例

スパウトでは、`src\main\java\com\microsoft\example` ディレクトリに `RandomSentenceSpout.java` という名前のファイルを作成し、次のテキストをコンテンツとして使用します。

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

コードのコメントに目を通して、スパウトの仕組みを理解してください。

> [!NOTE]
> このトポロジでは 1 つのスパウトのみを使用していますが、場合によっては異なるソースからトポロジにデータを供給するため複数のスパウトを使用することもあります。

### <a name="create-the-bolts"></a>ボルトを作成する

ボルトは、データの処理を扱います。 このトポロジでは、次の 2 つのボルトを使用します。

* **SplitSentence**: **RandomSentenceSpout** から出力されたセンテンスを個別の単語に分割します。

* **WordCount**: 各単語が発生した回数をカウントします。

> [!NOTE]
> ボルトは、たとえば、計算、永続化、外部コンポーネントとの対話など、実にあらゆる操作が可能です。

`src\main\java\com\microsoft\example` ディレクトリに、`SplitSentence.java` と `WordCount.Java` という 2 つの新しいファイルを作成します。 ファイルの内容として、次のテキストを使用します。

**SplitSentence**

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

**ワードカウント**

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

コードのコメントに目を通して、ボルトの仕組みを理解してください。

### <a name="define-the-topology"></a>トポロジの定義

トポロジは、コンポーネント間のデータのフローを定義するグラフにスパウトとボルトを結びつけます。 また、クラスター内にコンポーネントのインスタンスを作成するときに Storm が使用する並列処理のヒントも提供します。

次の図は、このトポロジのコンポーネントの基本的なグラフを示しています。

![スパウトとボルトの配置を示すダイアグラム](./media/hdinsight-storm-develop-java-topology/wordcount-topology.png)

トポロジを実装するには、`src\main\java\com\microsoft\example` ディレクトリに `WordCountTopology.java` という名前のファイルを作成します。 ファイルの内容として、次のテキストを使用します。

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

コードのコメントに目を通して、トポロジがどのように定義されて、クラスターに送信されているかを理解してください。

### <a name="configure-logging"></a>ログの構成

Storm では、Apache Log4j を使用して情報をログに記録します。 ログ記録を構成していない場合、トポロジによって診断情報が出力されます。 記録内容を制御するには、`resources` ディレクトリに `log4j2.xml` という名前のファイルを作成します。 ファイルの内容として、次のテキストを使用します。

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

これにより、`com.microsoft.example` クラスの新しいロガーが構成されます。このクラスには、この例のトポロジのコンポーネントが含まれます。 レベルは、このロガーをトレースするように設定されているため、このトポロジ内のコンポーネントから出力されるすべてのログ情報がキャプチャされます。

`<Root level="error">` セクションは、エラー情報のみを記録するように、ログ記録 (`com.microsoft.example` にないすべて) のルート レベルを構成します。

Log4j のログの記録を構成する方法の詳細については、 [http://logging.apache.org/log4j/2.x/manual/configuration.html](http://logging.apache.org/log4j/2.x/manual/configuration.html)をご覧ください。

> [!NOTE]
> Storm バージョン 0.10.0 以降では Log4j 2.x が使用されます。 以前のバージョンの Storm では Log4j 1.x が使用されていました。これには、ログの構成に別の形式が使用されていました。 以前の構成については、[http://wiki.apache.org/logging-log4j/Log4jXmlFormat](http://wiki.apache.org/logging-log4j/Log4jXmlFormat) をご覧ください。

## <a name="test-the-topology-locally"></a>ローカルでのトポロジのテスト

ファイルを保存したら、次のコマンドを使用してトポロジをローカルでテストします。

    mvn compile exec:java -Dstorm.topology=com.microsoft.example.WordCountTopology

実行中、スタートアップ情報が表示されます。 次のテキストは、ワード カウントの出力例を示しています。

    17:33:27 [Thread-12-count] INFO  com.microsoft.example.WordCount - Emitting a count of 56 for word snow
    17:33:27 [Thread-12-count] INFO  com.microsoft.example.WordCount - Emitting a count of 56 for word white
    17:33:27 [Thread-12-count] INFO  com.microsoft.example.WordCount - Emitting a count of 112 for word seven
    17:33:27 [Thread-16-count] INFO  com.microsoft.example.WordCount - Emitting a count of 195 for word the
    17:33:27 [Thread-30-count] INFO  com.microsoft.example.WordCount - Emitting a count of 113 for word and
    17:33:27 [Thread-30-count] INFO  com.microsoft.example.WordCount - Emitting a count of 57 for word dwarfs
    17:33:27 [Thread-12-count] INFO  com.microsoft.example.WordCount - Emitting a count of 57 for word snow

WordCount ボルトで出力されたログを見ると、and が 113 回出力されたことがわかります。 スパウトから同じセンテンスが継続的に出力されるため、トポロジが実行される限り、カウントは上がり続けます。

単語とカウントは 5 秒間隔で出力されます。 **WordCount** コンポーネントは、tick タプルを受信したときにのみ情報を出力するように構成されており、これらのタプルが既定で 5 秒ごとにしか配信されないことを要求します。

## <a name="convert-the-topology-to-flux"></a>トポロジを Flux に変換する

Flux は、構成と実装が分離可能な、Storm 0.10.0 以降で使用できる新しいフレームワークです。 コンポーネント (ボルトとスパウト) は現在も Java で定義しますが、トポロジは YAML ファイルを使用して定義します。

YAML ファイルでは、トポロジに使用するコンポーネント、そのコンポーネント間でデータをやり取りする方法、コンポーネントの初期化の際に使用する値を定義します。 jar ファイルの一部として YAML ファイルを含めることも、外部 YAML ファイルを使用することもできます。

> [!WARNING]
> Storm 1.0.1 での[バグ (https://issues.apache.org/jira/browse/STORM-2055)](https://issues.apache.org/jira/browse/STORM-2055) のため、Flux トポロジをローカルに実行するには [Storm 開発環境](https://storm.apache.org/releases/1.0.1/Setting-up-development-environment.html)のインストールが必要になる場合があります。

1. `WordCountTopology.java` ファイルをプロジェクトの外部に移動します。 以前は、このファイルでトポロジを定義していましたが、Flux では不要です。

2. `resources` ディレクトリに `topology.yaml` という名前のファイルを作成します。 このファイルの内容として、次のテキストを使用します。

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

3. `pom.xml` ファイルに次の変更を加えます。
   
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
            <version>2.3</version>
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

   * **exec-maven-plugin** の `<configuration>` セクションで、`<mainClass>` の値を `org.apache.storm.flux.Flux` に変更します。 この設定により、開発中にトポロジのローカルでの実行を Flux で処理できるようになります。

   * `<resources>` セクションの `<includes>` に次のコードを追加します。 これで、プロジェクトの一部としてトポロジを定義する YAML ファイルがインクルードされます。
     
        ```xml
        <include>topology.yaml</include>
        ```

## <a name="test-the-flux-topology-locally"></a>ローカルでの Flux トポロジのテスト

1. 次のコードを使用して、Maven で Flux トポロジをコンパイルし、実行します。
   
        mvn compile exec:java -Dexec.args="--local -R /topology.yaml"
   
    PowerShell を使用している場合は、次のコマンドを使用します。
   
        mvn compile exec:java "-Dexec.args=--local -R /topology.yaml"

    > [!WARNING]
    > トポロジが Storm 1.0.1 を使っている場合、このコマンドは失敗します。 これは、[https://issues.apache.org/jira/browse/STORM-2055](https://issues.apache.org/jira/browse/STORM-2055) によるものです。 代わりに、[開発環境に Storm をインストール](http://storm.apache.org/releases/0.10.0/Setting-up-development-environment.html)し、次の情報を使ってください。
   
    [開発環境に Storm がインストールされている](http://storm.apache.org/releases/0.10.0/Setting-up-development-environment.html)場合、代わりに次のコマンドを使うことができます。
   
        mvn compile package
        storm jar target/WordCount-1.0-SNAPSHOT.jar org.apache.storm.flux.Flux --local -R /topology.yaml
   
    `--local` パラメーターを指定すると、開発環境でトポロジがローカル モードで実行されます。 `-R /topology.yaml` パラメーターを指定すると、トポロジの定義に jar ファイルの `topology.yaml` ファイル リソースが使用されます。
   
    実行中、スタートアップ情報が表示されます。 次のテキストは出力例を示しています。
   
        17:33:27 [Thread-12-count] INFO  com.microsoft.example.WordCount - Emitting a count of 56 for word snow
        17:33:27 [Thread-12-count] INFO  com.microsoft.example.WordCount - Emitting a count of 56 for word white
        17:33:27 [Thread-12-count] INFO  com.microsoft.example.WordCount - Emitting a count of 112 for word seven
        17:33:27 [Thread-16-count] INFO  com.microsoft.example.WordCount - Emitting a count of 195 for word the
        17:33:27 [Thread-30-count] INFO  com.microsoft.example.WordCount - Emitting a count of 113 for word and
        17:33:27 [Thread-30-count] INFO  com.microsoft.example.WordCount - Emitting a count of 57 for word dwarfs
   
    ログに記録された情報のバッチの間隔が 10 秒遅延します。

2. プロジェクトの `topology.yaml` ファイルをコピーします。 新しいファイルに `newtopology.yaml` という名前を付けます。 `newtopology.yaml` ファイルで次のセクションを見つけて、値 `10` を `5` に変更します。 これにより、単語のカウントの出力バッチの間隔が 10 秒から 5 秒に変更されます。
   
        - id: "counter-bolt"
        className: "com.microsoft.example.WordCount"
        constructorArgs:
        - 5
        parallelism: 1

3. トポロジを実行するには、次のコマンドを使用します。
   
        mvn exec:java -Dexec.args="--local /path/to/newtopology.yaml"
   
    または、開発環境に Storm がある場合は次のようにします。
   
        storm jar target/WordCount-1.0-SNAPSHOT.jar org.apache.storm.flux.Flux --local /path/to/newtopology.yaml
   
    `/path/to/newtopology.yaml` を、前の手順で作成した newtopology.yaml ファイルのパスに変更します。 このコマンドでは、トポロジの定義として newtopology.yaml を使用します。 `compile` パラメーターを含めなかったため、前の手順でビルドしたプロジェクトのバージョンが使用されます。
   
    トポロジが開始されると、バッチが出力される時間間隔が変更され、newtopology.yaml の値が反映されていることがわかります。 このように、トポロジを再コンパイルしなくても YAML ファイルから構成を変更できることがわかります。

Flux フレームワークのその他の機能の詳細については、 [Flux (https://storm.apache.org/releases/0.10.0/flux.html)](https://storm.apache.org/releases/0.10.0/flux.html)に関するページをご覧ください。

## <a name="trident"></a>Trident

Trident は Storm から提供される大枠の抽象概念です。 ステートフルな処理をサポートします。 Trident の主なメリットは、トポロジが受けるすべてのメッセージが一度しか処理されないよう保証できることです。 メッセージが少なくとも一度は処理されることを保証する Java トポロジでこれを実現するのは困難です。 他にも、ボルトを作成する代わりに使える組み込みのコンポーネントがあるなどの違いがあります。 実際には、ボルトはフィルター、プロジェクション、関数などの汎用性の低いコンポーネントに置き換えられます。

Trident アプリケーションは Maven プロジェクトを使用して作成できます。 この記事で前述した同じ基本の手順の、コードのみを変更して作成できます。 Trident も (現在は) Flux フレームワークでは使用できません。

Trident の詳細については、「[Trident API の概要](http://storm.apache.org/documentation/Trident-API-Overview.html)」のページをご覧ください。

Trident アプリケーションの例については、「 [HDInsight での Apache Storm を使用した Twitter のトレンディング トピック](hdinsight-storm-twitter-trending.md)」をご覧ください。

## <a name="next-steps"></a>次のステップ

ここまでで、Java を使用して Storm トポロジを作成する方法を説明しました。 続けて次の記事もご覧ください。

* [HDInsight での Apache Storm トポロジのデプロイと管理](hdinsight-storm-deploy-monitor-topology.md)

* [Visual Studio を使用して HDInsight で Apache Storm の C# トポロジを開発する](hdinsight-storm-develop-csharp-visual-studio-topology.md)

Storm トポロジ例をさらにご覧になる場合、「 [HDInsight での Storm トポロジの例](hdinsight-storm-example-topology.md)」をご確認ください。


