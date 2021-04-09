---
title: Apache Hadoop 用の Java MapReduce を作成する - Azure HDInsight
description: Apache Maven を使用して Java ベースの MapReduce アプリケーションを作成し、Azure HDInsight 上で Hadoop を使用して実行する方法について説明します。
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive,hdiseo17may2017, devx-track-java
ms.date: 01/16/2020
ms.openlocfilehash: 7c1a6623883cbee46ba98982808f3c392dc50ffa
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98946648"
---
# <a name="develop-java-mapreduce-programs-for-apache-hadoop-on-hdinsight"></a>HDInsight 上の Apache Hadoop 用の Java MapReduce プログラムを開発する

Apache Maven を使用して Java ベースの MapReduce アプリケーションを作成した後、Azure HDInsight 上で Hadoop を使用して実行する方法について説明します。

## <a name="prerequisites"></a>前提条件

* [Java Developer キット](/azure/developer/java/fundamentals/java-jdk-long-term-support) (JDK) バージョン 8

* Apache に従って適切に[インストール](https://maven.apache.org/install.html)された [Apache Maven](https://maven.apache.org/download.cgi)。  Maven は Java プロジェクトのプロジェクト ビルド システムです。

## <a name="configure-development-environment"></a>開発環境の設定

この記事で使用された環境は、Windows 10 を実行しているコンピューターです。 コマンドはコマンド プロンプトで実行され、さまざまなファイルがメモ帳で編集されています。 ご使用の環境に応じて変更します。

コマンド プロンプトで以下のコマンドを入力して、作業環境を作成を作成します。

```cmd
IF NOT EXIST C:\HDI MKDIR C:\HDI
cd C:\HDI
```

## <a name="create-a-maven-project"></a>Maven プロジェクトを作成する

1. 次のコマンドを使用して、**wordcountjava** という名前の Maven プロジェクトを作成します。

   ```bash
   mvn archetype:generate -DgroupId=org.apache.hadoop.examples -DartifactId=wordcountjava -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
   ```

    このコマンドにより、`artifactID` パラメーターで指定した名前 (この例では **wordcountjava**) のディレクトリが作成されます。このディレクトリには、次の項目が含まれます。

    * `pom.xml` - [プロジェクト オブジェクト モデル (POM)](https://maven.apache.org/guides/introduction/introduction-to-the-pom.html) には、プロジェクトのビルドに使用される情報と構成の詳細が含まれています。
    * src\main\java\org\apache\hadoop\examples: アプリケーションコードが含まれています。
    * src\test\java\org\apache\hadoop\examples: アプリケーションのテストが含まれています。

1. 生成されたコード例の削除 以下のコマンドを入力して、生成されたテストとアプリケーション ファイル `AppTest.java` と `App.java` を削除します。

    ```cmd
    cd wordcountjava
    DEL src\main\java\org\apache\hadoop\examples\App.java
    DEL src\test\java\org\apache\hadoop\examples\AppTest.java
    ```

## <a name="update-the-project-object-model"></a>プロジェクト オブジェクト モデルを更新する

pom.xml ファイルの完全なリファレンスについては、https://maven.apache.org/pom.htmlを参照してください。 以下のコマンドを入力して `pom.xml` を開きます。

```cmd
notepad pom.xml
```

### <a name="add-dependencies"></a>依存関係を追加する

`pom.xml` で、`<dependencies>` セクションの次のテキストを追加します。

```xml
<dependency>
    <groupId>org.apache.hadoop</groupId>
    <artifactId>hadoop-mapreduce-examples</artifactId>
    <version>2.7.3</version>
    <scope>provided</scope>
</dependency>
<dependency>
    <groupId>org.apache.hadoop</groupId>
    <artifactId>hadoop-mapreduce-client-common</artifactId>
    <version>2.7.3</version>
    <scope>provided</scope>
</dependency>
<dependency>
    <groupId>org.apache.hadoop</groupId>
    <artifactId>hadoop-common</artifactId>
    <version>2.7.3</version>
    <scope>provided</scope>
</dependency>
```

これは、特定のバージョン (&lt;version\> 内に記載) に必要なライブラリ ( &lt;artifactId\> 内に記載) を定義しています。 この依存関係は、コンパイル時に既定の Maven リポジトリからダウンロードされます。 [Maven リポジトリ検索](https://search.maven.org/#artifactdetails%7Corg.apache.hadoop%7Chadoop-mapreduce-examples%7C2.5.1%7Cjar) を使用して、その他の情報を表示できます。

`<scope>provided</scope>` は、これらの依存関係はアプリケーションに付属せず、実行時に HDInsight クラスターによって提供されることを Maven に通知しています。

> [!IMPORTANT]
> 使用されるバージョンは、クラスター上に存在する Hadoop のバージョンと一致する必要があります。 バージョンの詳細については、[HDInsight コンポーネントのバージョン](../hdinsight-component-versioning.md)に関する記事を参照してください。

### <a name="build-configuration"></a>[ビルド構成]

Maven プラグインでは、プロジェクトのビルド ステージをカスタマイズできます。 このセクションは、プラグインやリソース、他のビルド構成オプションを追加する際に使用します。

`pom.xml` ファイルに次のコードを追加し、ファイルを保存し閉じます。 このテキストは、ファイルの `<project>...</project>` タグ内に配置する必要があります (たとえば `</dependencies>` と `</project>` の間)。

```xml
<build>
    <plugins>
    <plugin>
        <groupId>org.apache.maven.plugins</groupId>
        <artifactId>maven-shade-plugin</artifactId>
        <version>2.3</version>
        <configuration>
        <transformers>
            <transformer implementation="org.apache.maven.plugins.shade.resource.ApacheLicenseResourceTransformer">
            </transformer>
        </transformers>
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
    <plugin>
        <groupId>org.apache.maven.plugins</groupId>
        <artifactId>maven-compiler-plugin</artifactId>
        <version>3.6.1</version>
        <configuration>
        <source>1.8</source>
        <target>1.8</target>
        </configuration>
    </plugin>
    </plugins>
</build>
```

このセクションでは、Apache Maven Compiler Plugin と Apache Maven Shade Plugin を構成します。 トポロジのコンパイルにはコンパイラ プラグインが使用されます。 シャードのプラグインは、Maven でビルドされる JAR パッケージ内のライセンスの重複を防ぐために使用されます。 このプラグインは、HDInsight クラスターでの実行時に発生する "ライセンス ファイルの重複" エラーを回避するために使用されます。 maven-shade-plugin を `ApacheLicenseResourceTransformer` 実装で使用すると、エラーを回避できます。

また、maven-shade-plugin は、アプリケーションで必要とされるすべての依存関係を含む uber jar も生成します。

`pom.xml` ファイルを保存します。

## <a name="create-the-mapreduce-application"></a>MapReduce アプリケーションを作成する

1. 以下のコマンドを入力して、新しいファイル `WordCount.java` を作成して開きます。 プロンプトが表示されたら [**YES**] を選択して新しいファイルを作成します。

    ```cmd
    notepad src\main\java\org\apache\hadoop\examples\WordCount.java
    ```

2. 次に、以下の Java コードをコピーして新しいファイルに貼り付けます。 その後、ファイルを閉じます。

    ```java
    package org.apache.hadoop.examples;

    import java.io.IOException;
    import java.util.StringTokenizer;
    import org.apache.hadoop.conf.Configuration;
    import org.apache.hadoop.fs.Path;
    import org.apache.hadoop.io.IntWritable;
    import org.apache.hadoop.io.Text;
    import org.apache.hadoop.mapreduce.Job;
    import org.apache.hadoop.mapreduce.Mapper;
    import org.apache.hadoop.mapreduce.Reducer;
    import org.apache.hadoop.mapreduce.lib.input.FileInputFormat;
    import org.apache.hadoop.mapreduce.lib.output.FileOutputFormat;
    import org.apache.hadoop.util.GenericOptionsParser;

    public class WordCount {

        public static class TokenizerMapper
            extends Mapper<Object, Text, Text, IntWritable>{

        private final static IntWritable one = new IntWritable(1);
        private Text word = new Text();

        public void map(Object key, Text value, Context context
                        ) throws IOException, InterruptedException {
            StringTokenizer itr = new StringTokenizer(value.toString());
            while (itr.hasMoreTokens()) {
            word.set(itr.nextToken());
            context.write(word, one);
            }
        }
    }

    public static class IntSumReducer
            extends Reducer<Text,IntWritable,Text,IntWritable> {
        private IntWritable result = new IntWritable();

        public void reduce(Text key, Iterable<IntWritable> values,
                            Context context
                            ) throws IOException, InterruptedException {
            int sum = 0;
            for (IntWritable val : values) {
            sum += val.get();
            }
            result.set(sum);
            context.write(key, result);
        }
    }

    public static void main(String[] args) throws Exception {
        Configuration conf = new Configuration();
        String[] otherArgs = new GenericOptionsParser(conf, args).getRemainingArgs();
        if (otherArgs.length != 2) {
            System.err.println("Usage: wordcount <in> <out>");
            System.exit(2);
        }
        Job job = new Job(conf, "word count");
        job.setJarByClass(WordCount.class);
        job.setMapperClass(TokenizerMapper.class);
        job.setCombinerClass(IntSumReducer.class);
        job.setReducerClass(IntSumReducer.class);
        job.setOutputKeyClass(Text.class);
        job.setOutputValueClass(IntWritable.class);
        FileInputFormat.addInputPath(job, new Path(otherArgs[0]));
        FileOutputFormat.setOutputPath(job, new Path(otherArgs[1]));
        System.exit(job.waitForCompletion(true) ? 0 : 1);
        }
    }
    ```

    パッケージ名は `org.apache.hadoop.examples` でクラス名は `WordCount` です。 これらの名前は MapReduce ジョブを送信するときに使用します。

## <a name="build-and-package-the-application"></a>アプリケーションをビルドおよびパッケージ化する

`wordcountjava` ディレクトリで次のコマンドを使用して、アプリケーションが含まれた JAR ファイルをビルドします。

```cmd
mvn clean package
```

このコマンドは、前のビルド アーティファクトを整理し、まだインストールされていない依存関係をダウンロードして、アプリケーションをビルドしてパッケージ化します。

コマンドが完了すると、`wordcountjava/target` ディレクトリに `wordcountjava-1.0-SNAPSHOT.jar` という名前のファイルが格納されます。

> [!NOTE]
> `wordcountjava-1.0-SNAPSHOT.jar` ファイルは uber jar です。これには、WordCount ジョブだけでなく、ジョブの実行時に必要な依存関係も含まれます。

## <a name="upload-the-jar-and-run-jobs-ssh"></a>JAR をアップロードしてジョブを実行する (SSH)

次の手順では、`scp` を使用して、HDInsight クラスターの Apache HBase のプライマリ ヘッドノードに JAR をコピーします。 その後、`ssh` コマンドを使用してクラスターに接続し、ヘッド ノードで例を直接実行します。

1. クラスターに jar をアップロードします。 `CLUSTERNAME`を HDInsight クラスター名と置換し、次のコマンドを入力します。

    ```cmd
    scp target/wordcountjava-1.0-SNAPSHOT.jar sshuser@CLUSTERNAME-ssh.azurehdinsight.net:
    ```

1. クラスターに接続します。 `CLUSTERNAME`を HDInsight クラスター名と置換し、次のコマンドを入力します。

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. SSH セッションから、次のコマンドを使用して MapReduce アプリケーションを実行します。

   ```bash
   yarn jar wordcountjava-1.0-SNAPSHOT.jar org.apache.hadoop.examples.WordCount /example/data/gutenberg/davinci.txt /example/data/wordcountout
   ```

    このコマンドは、WordCount MapReduce アプリケーションを起動します。 入力ファイルは `/example/data/gutenberg/davinci.txt`、出力ディレクトリは `/example/data/wordcountout` です。 入力ファイルと出力は、両方ともクラスターの既定のストレージに格納されます。

1. ジョブが完了したら、次のコマンドを使用して結果を表示します。

   ```bash
   hdfs dfs -cat /example/data/wordcountout/*
   ```

    次のような文字と回数の値の一覧が表示されます。

    ```output
    zeal    1
    zelus   1
    zenith  2
    ```

## <a name="next-steps"></a>次のステップ

このドキュメントでは、Java MapReduce ジョブを作成する方法を説明しました。 HDInsight を使用する他の方法については、次のドキュメントを参照してください。

* [HDInsight での Apache Hive の使用](hdinsight-use-hive.md)
* [HDInsight での MapReduce の使用](hdinsight-use-mapreduce.md)
* [Java デベロッパー センター](https://azure.microsoft.com/develop/java/)