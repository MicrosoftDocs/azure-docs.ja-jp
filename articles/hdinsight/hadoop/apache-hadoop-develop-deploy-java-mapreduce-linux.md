---
title: Apache Hadoop 用の Java MapReduce を作成する - Azure HDInsight
description: Apache Maven を使用して Java ベースの MapReduce アプリケーションを作成し、Azure HDInsight 上で Hadoop を使用して実行する方法について説明します。
services: hdinsight
ms.reviewer: jasonh
author: hrasheed-msft
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.topic: conceptual
ms.date: 04/23/2018
ms.author: hrasheed
ms.openlocfilehash: e1b4681bc8e42294be6b47a04a24e28f81703e6f
ms.sourcegitcommit: c2e61b62f218830dd9076d9abc1bbcb42180b3a8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/15/2018
ms.locfileid: "53436859"
---
# <a name="develop-java-mapreduce-programs-for-apache-hadoop-on-hdinsight"></a>HDInsight 上の Apache Hadoop 用の Java MapReduce プログラムを開発する

Apache Maven を使用して Java ベースの MapReduce アプリケーションを作成した後、Azure HDInsight 上で Hadoop を使用して実行する方法について説明します。

> [!NOTE]
> この例がテストされた最新の環境は HDInsight 3.6 です。

## <a name="prerequisites"></a>前提条件

* [Java JDK](https://www.oracle.com/technetwork/java/javase/downloads/) 8 以降 (または同等の OpenJDK など)
    
    > [!NOTE]
    > HDInsight 3.4 以前のバージョンでは、Java 7 を使用します。 HDInsight 3.5 以降では、Java 8 を使用します。

* [Apache Maven](https://maven.apache.org/)

## <a name="configure-development-environment"></a>開発環境の設定

Java と JDK をインストールするときに、次のような環境変数が設定される場合があります。 ただし、これらが存在するかどうかや、システムに対して適切な値が含まれているかを確認する必要があります。

* `JAVA_HOME` - Java ランタイム環境 (JRE) がインストールされているディレクトリを指している必要があります。 たとえば、OS X、Unix、Linux システムの場合は、`/usr/lib/jvm/java-7-oracle` のような値になります。 Windows の場合は、 `c:\Program Files (x86)\Java\jre1.7`

* `PATH` - 次のパスを含む必要があります。
  
  * `JAVA_HOME` (または同等のパス)

  * `JAVA_HOME\bin` (または同等のパス)

  * Maven がインストールされているディレクトリ

## <a name="create-a-maven-project"></a>Maven プロジェクトを作成する

1. ターミナル セッションまたは開発環境のコマンド ラインから、このプロジェクトを格納する場所にディレクトリを変更します。

2. Maven でインストールされた `mvn` コマンドを使用し、プロジェクトのスキャフォールディングを生成します。

   ```bash
   mvn archetype:generate -DgroupId=org.apache.hadoop.examples -DartifactId=wordcountjava -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
   ```

    > [!NOTE]
    > PowerShell を使用している場合は、`-D` パラメーターを引用符で囲む必要があります。
    >
    > `mvn archetype:generate "-DgroupId=org.apache.hadoop.examples" "-DartifactId=wordcountjava" "-DarchetypeArtifactId=maven-archetype-quickstart" "-DinteractiveMode=false"`

    このコマンドにより、`artifactID` パラメーターで指定した名前 (この例では **wordcountjava**) のディレクトリが作成されます。このディレクトリには、次の項目が含まれます。

   * `pom.xml` - [プロジェクト オブジェクト モデル (POM)](https://maven.apache.org/guides/introduction/introduction-to-the-pom.html) には、プロジェクトのビルドに使用される情報と構成の詳細が含まれています。

   * `src` - アプリケーションを含むディレクトリ。

3. `src/test/java/org/apache/hadoop/examples/apptest.java` ファイルを削除します。 このファイルは、この例では使用しません。

## <a name="add-dependencies"></a>依存関係を追加する

1. `pom.xml` ファイルを編集し、`<dependencies>` セクション内に次のテキストを追加します。
   
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

2. `pom.xml` ファイルに次のテキストを追加します。 このテキストは、ファイルの `<project>...</project>` タグ内に配置する必要があります (たとえば `</dependencies>` と `</project>` の間)。

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

    最初のプラグインは [Maven Shade Plugin](https://maven.apache.org/plugins/maven-shade-plugin/) を構成します。これは、アプリケーションで必要な依存関係を含む uberjar (fatjar と呼ばれることもある) を作成するために使用されます。 また、一部のシステムで問題が発生する可能性がある jar パッケージ内のライセンスの重複を防止します。

    2 番目のプラグインは、対象の Java バージョンを構成します。

    > [!NOTE]
    > HDInsight 3.4 以前では、Java 7 を使用します。 HDInsight 3.5 以降では、Java 8 を使用します。

3. `pom.xml` ファイルを保存します。

## <a name="create-the-mapreduce-application"></a>MapReduce アプリケーションを作成する

1. `wordcountjava/src/main/java/org/apache/hadoop/examples` ディレクトリに移動して、`App.java` ファイルの名前を `WordCount.java` に変更します。

2. テキスト エディターで `WordCount.java` ファイルを開き、内容を次のテキストに置き換えます。
   
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

3. ファイルを保存します。

## <a name="build-the-application"></a>アプリケーションのビルド

1. `wordcountjava` ディレクトリに移動します (現在、このディレクトリではない場合)。

2. 次のコマンドを使用して、アプリケーションを含む JAR ファイルをビルドします。

   ```
   mvn clean package
   ```

    このコマンドは、前のビルド アーティファクトを整理し、まだインストールされていない依存関係をダウンロードして、アプリケーションをビルドしてパッケージ化します。

3. コマンドが完了すると、`wordcountjava/target` ディレクトリに `wordcountjava-1.0-SNAPSHOT.jar` という名前のファイルが格納されます。
   
   > [!NOTE]
   > `wordcountjava-1.0-SNAPSHOT.jar` ファイルは uber jar です。これには、WordCount ジョブだけでなく、ジョブの実行時に必要な依存関係も含まれます。

## <a id="upload"></a>jar をアップロードする

次のコマンドを使用して、HDInsight ヘッドノードに jar ファイルをアップロードします。

   ```bash
   scp target/wordcountjava-1.0-SNAPSHOT.jar USERNAME@CLUSTERNAME-ssh.azurehdinsight.net:
   ```

__USERNAME__ は、クラスターの SSH ユーザー名に置き換えます。 __CLUSTERNAME__ を HDInsight クラスター名に置き換えます。

このコマンドにより、ファイルがローカル システムからヘッド ノードにコピーされます。 詳細については、[HDInsight での SSH の使用](../hdinsight-hadoop-linux-use-ssh-unix.md)に関するページを参照してください。

## <a name="run"></a>Hadoop で MapReduce ジョブを実行する

1. SSH を使用して HDInsight に接続する。 詳細については、[HDInsight での SSH の使用](../hdinsight-hadoop-linux-use-ssh-unix.md)に関するページを参照してください。

2. SSH セッションから、次のコマンドを使用して MapReduce アプリケーションを実行します。
   
   ```bash
   yarn jar wordcountjava-1.0-SNAPSHOT.jar org.apache.hadoop.examples.WordCount /example/data/gutenberg/davinci.txt /example/data/wordcountout
   ```
   
    このコマンドは、WordCount MapReduce アプリケーションを起動します。 入力ファイルは `/example/data/gutenberg/davinci.txt`、出力ディレクトリは `/example/data/wordcountout` です。 入力ファイルと出力は、両方ともクラスターの既定のストレージに格納されます。

3. ジョブが完了したら、次のコマンドを使用して結果を表示します。
   
   ```bash
   hdfs dfs -cat /example/data/wordcountout/*
   ```

    次のような文字と回数の値の一覧が表示されます。
   
        zeal    1
        zelus   1
        zenith  2

## <a id="nextsteps"></a>次のステップ

このドキュメントでは、Java MapReduce ジョブを作成する方法を説明しました。 HDInsight を使用する他の方法については、次のドキュメントを参照してください。

* [HDInsight での Apache Hive の使用](hdinsight-use-hive.md)
* [HDInsight での Apache Pig の使用](hdinsight-use-pig.md)
* [HDInsight での MapReduce の使用](hdinsight-use-mapreduce.md)

詳細については、 [Java デベロッパー センター](https://azure.microsoft.com/develop/java/)も参照してください。

[azure-purchase-options]: https://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: https://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: https://azure.microsoft.com/pricing/free-trial/

[hdinsight-use-sqoop]:hdinsight-use-sqoop.md
[hdinsight-ODBC]: hdinsight-connect-excel-hive-ODBC-driver.md
[hdinsight-power-query]:apache-hadoop-connect-excel-power-query.md

[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-admin-powershell]: hdinsight-administer-use-powershell.md
[hdinsight-power-query]:apache-hadoop-connect-excel-power-query.md

[powershell-PSCredential]: https://social.technet.microsoft.com/wiki/contents/articles/4546.working-with-passwords-secure-strings-and-credentials-in-windows-powershell.aspx

