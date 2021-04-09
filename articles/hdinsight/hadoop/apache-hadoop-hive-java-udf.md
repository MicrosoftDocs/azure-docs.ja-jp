---
title: Apache Hive Azure HDInsight を使用する Java のユーザー定義関数 (UDF)
description: Apache Hive と連携する Java ベースのユーザー定義関数 (UDF) を作成する方法について説明します。 この UDF の例では、テキスト文字列のテーブルを小文字に変換します。
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive,hdiseo17may2017, devx-track-java
ms.date: 11/20/2019
ms.openlocfilehash: bf9c2ea544c6b510a0507b6b020f0eae0b101dc7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98946559"
---
# <a name="use-a-java-udf-with-apache-hive-in-hdinsight"></a>HDInsight 上の Apache Hive で Java UDF を使用する

Apache Hive と連携する Java ベースのユーザー定義関数 (UDF) を作成する方法について説明します。 この例の Java UDF では、テキスト文字列のテーブルをすべて小文字の文字に変換します。

## <a name="prerequisites"></a>前提条件

* HDInsight 上の Hadoop クラスター。 [Linux での HDInsight の概要](./apache-hadoop-linux-tutorial-get-started.md)に関するページを参照してください。
* [Java Developer Kit (JDK) バージョン 8](/azure/developer/java/fundamentals/java-jdk-long-term-support)
* Apache に従って適切に[インストール](https://maven.apache.org/install.html)された [Apache Maven](https://maven.apache.org/download.cgi)。  Maven は Java プロジェクトのプロジェクト ビルド システムです。
* クラスターのプライマリ ストレージの [URI スキーム](../hdinsight-hadoop-linux-information.md#URI-and-scheme)。 Azure Storage では wasb://、Azure Data Lake Storage Gen2 では abfs://、Azure Data Lake Storage Gen1 では adl:// です。 Azure Storage で安全な転送が有効になっている場合、URI は `wasbs://` になります。  [安全な転送](../../storage/common/storage-require-secure-transfer.md)に関するページも参照してください。

* テキスト エディターまたは Java IDE

    > [!IMPORTANT]  
    > Windows クライアントで Python ファイルを作成する場合は、行末に LF が用いられているエディターを使用する必要があります。 エディターで LF と CRLF のどちらが使用されているかが不明な場合は、「[トラブルシューティング](#troubleshooting)」セクションで、CR 文字を削除する手順をご覧ください。

## <a name="test-environment"></a>テスト環境

この記事で使用された環境は、Windows 10 を実行しているコンピューターです。  コマンドはコマンド プロンプトで実行され、さまざまなファイルがメモ帳で編集されています。 ご使用の環境に応じて変更します。

コマンド プロンプトで以下のコマンドを入力して、作業環境を作成を作成します。

```cmd
IF NOT EXIST C:\HDI MKDIR C:\HDI
cd C:\HDI
```

## <a name="create-an-example-java-udf"></a>Java UDF のサンプルを作成する

1. 次のコマンドを入力して、新しい Maven プロジェクトを作成します。

    ```cmd
    mvn archetype:generate -DgroupId=com.microsoft.examples -DartifactId=ExampleUDF -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
    ```

    このコマンドにより、Maven プロジェクトを含む `exampleudf` という名前のディレクトリが作成されます。

2. プロジェクトが作成されたら、次のコマンドを入力し、プロジェクトの一部として作成された `exampleudf/src/test` ディレクトリを削除します。

    ```cmd
    cd ExampleUDF
    rmdir /S /Q "src/test"
    ```

3. 以下のコマンドを入力して `pom.xml` を開きます。

    ```cmd
    notepad pom.xml
    ```

    次に、既存の `<dependencies>` エントリを次の XML で置き換えます。

    ```xml
    <dependencies>
        <dependency>
            <groupId>org.apache.hadoop</groupId>
            <artifactId>hadoop-client</artifactId>
            <version>2.7.3</version>
            <scope>provided</scope>
        </dependency>
        <dependency>
            <groupId>org.apache.hive</groupId>
            <artifactId>hive-exec</artifactId>
            <version>1.2.1</version>
            <scope>provided</scope>
        </dependency>
    </dependencies>
    ```

    これらのエントリは、HDInsight 3.6 に含まれる Hadoop と Hive のバージョンを指定します。 HDInsight に含まれる Hadoop と Hive のバージョンの情報は、 [HDInsight コンポーネントのバージョン管理](../hdinsight-component-versioning.md) に関するドキュメントで確認できます。

    ファイルの最後の `</project>` 行の前に `<build>` セクションを追加します。 このセクションには、次の XML が含まれる必要があります。

    ```xml
    <build>
        <plugins>
            <!-- build for Java 1.8. This is required by HDInsight 3.6  -->
            <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-compiler-plugin</artifactId>
                <version>3.3</version>
                <configuration>
                    <source>1.8</source>
                    <target>1.8</target>
                </configuration>
            </plugin>
            <!-- build an uber jar -->
            <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-shade-plugin</artifactId>
                <version>3.2.1</version>
                <configuration>
                    <!-- Keep us from getting a can't overwrite file error -->
                    <transformers>
                        <transformer
                                implementation="org.apache.maven.plugins.shade.resource.ApacheLicenseResourceTransformer">
                        </transformer>
                        <transformer implementation="org.apache.maven.plugins.shade.resource.ServicesResourceTransformer">
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
        </plugins>
    </build>
    ```

    これらのエントリは、プロジェクトのビルド方法を定義します。 具体的に言うと、プロジェクトで使用する Java のバージョンと、クラスターにデプロイするための uberjar の構築方法です。

    変更を加えたら、ファイルを保存します。

4. 以下のコマンドを入力して、新しいファイル `ExampleUDF.java` を作成して開きます。

    ```cmd
    notepad src/main/java/com/microsoft/examples/ExampleUDF.java
    ```

    次に、以下の Java コードをコピーして新しいファイルに貼り付けます。 その後、ファイルを閉じます。

    ```java
    package com.microsoft.examples;

    import org.apache.hadoop.hive.ql.exec.Description;
    import org.apache.hadoop.hive.ql.exec.UDF;
    import org.apache.hadoop.io.*;

    // Description of the UDF
    @Description(
        name="ExampleUDF",
        value="returns a lower case version of the input string.",
        extended="select ExampleUDF(deviceplatform) from hivesampletable limit 10;"
    )
    public class ExampleUDF extends UDF {
        // Accept a string input
        public String evaluate(String input) {
            // If the value is null, return a null
            if(input == null)
                return null;
            // Lowercase the input string and return it
            return input.toLowerCase();
        }
    }
    ```

    このコードは、文字列値を受け取って、その文字列の小文字のバージョンを返す UDF を実装します。

## <a name="build-and-install-the-udf"></a>UDF をビルドしてインストールする

以下のコマンドでは、`sshuser` を実際のユーザー名と置き換えます (異なる場合)。 `mycluster` を実際のクラスター名に置き換えます。

1. 次のコマンドを入力して、UDF をコンパイルしパッケージ化します。

    ```cmd
    mvn compile package
    ```

    このコマンドで、UDF をビルドして `exampleudf/target/ExampleUDF-1.0-SNAPSHOT.jar` ファイルにパッケージ化します。

2. `scp`コマンドを使用して、HDInsight クラスターにファイルをコピーします。次のコマンドを入力します。

    ```cmd
    scp ./target/ExampleUDF-1.0-SNAPSHOT.jar sshuser@mycluster-ssh.azurehdinsight.net:
    ```

3. 次のコマンドを入力して、SSH を使用してクラスターに接続します。

    ```cmd
    ssh sshuser@mycluster-ssh.azurehdinsight.net
    ```

4. オープン SSH セッションで jar ファイルを HDInsight ストレージにコピーします。

    ```bash
    hdfs dfs -put ExampleUDF-1.0-SNAPSHOT.jar /example/jars
    ```

## <a name="use-the-udf-from-hive"></a>Hive から UDF を使用する

1. 次のコマンドを入力して、SSH セッションから BeeLine クライアントを開始します。

    ```bash
    beeline -u 'jdbc:hive2://localhost:10001/;transportMode=http'
    ```

    このコマンドは、クラスターのログイン アカウントに既定値の **admin** を使用していることを前提としています。

2. `jdbc:hive2://localhost:10001/>` プロンプトが表示されたら、次のように入力して、UDF を Hive に追加し、関数として公開します。

    ```hiveql
    ADD JAR wasbs:///example/jars/ExampleUDF-1.0-SNAPSHOT.jar;
    CREATE TEMPORARY FUNCTION tolower as 'com.microsoft.examples.ExampleUDF';
    ```

3. UDF を使用して、テーブルから取得した値を小文字の文字列に変換します。

    ```hiveql
    SELECT tolower(state) AS ExampleUDF, state FROM hivesampletable LIMIT 10;
    ```

    このクエリは、テーブルから状態を選択し、その文字列と小文字に変換したものを一緒に表示します。 次のテキストのような出力が表示されます。

    ```output
    +---------------+---------------+--+
    |  exampleudf   |     state     |
    +---------------+---------------+--+
    | california    | California    |
    | pennsylvania  | Pennsylvania  |
    | pennsylvania  | Pennsylvania  |
    | pennsylvania  | Pennsylvania  |
    | colorado      | Colorado      |
    | colorado      | Colorado      |
    | colorado      | Colorado      |
    | utah          | Utah          |
    | utah          | Utah          |
    | colorado      | Colorado      |
    +---------------+---------------+--+
    ```

## <a name="troubleshooting"></a>トラブルシューティング

Hive ジョブを実行しているときに、次のテキストようなエラーが発生する場合があります。

```output
Caused by: org.apache.hadoop.hive.ql.metadata.HiveException: [Error 20001]: An error occurred while reading or writing to your custom script. It may have crashed with an error.
```

この問題は、Python ファイルの行末が原因で発生する場合があります。 多くの Windows 版エディターでは行末に既定でCRLF が使用されていますが、Linux アプリケーションでは通常、行末は LF であることを前提としています。

ファイルを HDInsight にアップロードする前に、次の PowerShell ステートメントを使用して CR 文字を削除できます。

```PowerShell
# Set $original_file to the python file path
$text = [IO.File]::ReadAllText($original_file) -replace "`r`n", "`n"
[IO.File]::WriteAllText($original_file, $text)
```

## <a name="next-steps"></a>次のステップ

Hive の他の使用方法については、[HDInsight での Apache Hive の使用](hdinsight-use-hive.md)に関するページを参照してください。

Hive のユーザー定義関数の詳細については、apache.org の Hive wiki で、[Apache Hive の演算子とユーザー定義関数](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+UDF)に関するセクションを参照してください。