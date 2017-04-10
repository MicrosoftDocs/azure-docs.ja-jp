---
title: "HDInsight で Hive と Java のユーザー定義関数 (UDF) を使用する | Microsoft Docs"
description: "HDInsight で Hive から Java ユーザー定義関数 (UDF) を作成して使用する方法について説明します。"
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
ms.assetid: 8d4f8efe-2f01-4a61-8619-651e873c7982
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: java
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 01/12/2017
ms.author: larryfr
translationtype: Human Translation
ms.sourcegitcommit: 4f2230ea0cc5b3e258a1a26a39e99433b04ffe18
ms.openlocfilehash: c593bca61f8f1cc7c81e4744409f8b4a2c23df64
ms.lasthandoff: 03/25/2017


---
# <a name="use-a-java-udf-with-hive-in-hdinsight"></a>HDInsight で Hive と Java UDF を使用する
Hive は HDInsight でデータを処理する場合にきわめて有益ですが、より汎用的な言語が必要になる場合もあります。 Hive では、さまざまなプログラミング言語を使用してユーザー定義関数 (UDF) を作成できます。 このドキュメントでは、Hive から Java UDF を使用する方法を説明します。

## <a name="requirements"></a>必要条件

* HDInsight クラスター (Windows ベースまたは Linux ベース)
  
  > [!IMPORTANT]
  > Linux は、バージョン 3.4 以上の HDInsight で使用できる唯一のオペレーティング システムです。 詳細については、[Window での HDInsight の廃止](hdinsight-component-versioning.md#hdi-version-32-and-33-nearing-deprecation-date)に関する記事を参照してください。
  
  このドキュメントのほとんどの手順が、両方の種類のクラスターで機能します。ただし、コンパイル済みの UDF をクラスターにアップロードして実行するための手順は、Linux ベースのクラスター固有の内容です。 Windows ベースのクラスターで使用できる情報へのリンクが提供されます。

* [Java JDK](http://www.oracle.com/technetwork/java/javase/downloads/) 7 以降 (または同等の OpenJDK など)
* [Apache Maven](http://maven.apache.org/)
* テキスト エディターまたは Java IDE
  
  > [!IMPORTANT]
  > Linux ベースの HDInsight サーバーを使用している一方で、Windows クライアントで Python ファイルを作成する場合は、行末に LF が用いられているエディターを使用する必要があります。 エディターで LF と CRLF のどちらが使用されているかが不明な場合は、「[トラブルシューティング](#troubleshooting)」セクションで、ユーティリティを使用して HDInsight クラスターで CR 文字を削除する手順をご覧ください。

## <a name="create-an-example-udf"></a>UDF のサンプルを作成する
1. コマンド ラインで、次の手順を使用して新しい Maven プロジェクトを作成します。
   
        mvn archetype:generate -DgroupId=com.microsoft.examples -DartifactId=ExampleUDF -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
   
   > [!NOTE]
   > PowerShell を使用している場合は、パラメーターを引用符で囲む必要があります。 たとえば、「`mvn archetype:generate "-DgroupId=com.microsoft.examples" "-DartifactId=ExampleUDF" "-DarchetypeArtifactId=maven-archetype-quickstart" "-DinteractiveMode=false"`」のように入力します。
   > 
   > 
   
    これによって、**exampleudf** という名前の新しいディレクトリが作成されます。このディレクトリに Maven プロジェクトが格納されます。
2. プロジェクトが作成されたら、プロジェクトの一部として作成された **exampleudf/src/test** ディレクトリを削除します。この例ではこのディレクトリを使用しません。
3. **exampleudf/pom.xml** を開き、既存の `<dependencies>` エントリを次のエントリと置き換えます。
   
        <dependencies>
            <dependency>
                <groupId>org.apache.hadoop</groupId>
                <artifactId>hadoop-client</artifactId>
                <version>2.7.1</version>
                <scope>provided</scope>
            </dependency>
            <dependency>
                <groupId>org.apache.hive</groupId>
                <artifactId>hive-exec</artifactId>
                <version>1.2.1</version>
                <scope>provided</scope>
            </dependency>
        </dependencies>
   
    これらのエントリは、HDInsight 3.3 および 3.4 のクラスターに含まれる Hadoop と Hive のバージョンを指定します。 HDInsight に含まれる Hadoop と Hive のバージョンの情報は、 [HDInsight コンポーネントのバージョン管理](hdinsight-component-versioning.md) に関するドキュメントで確認できます。
   
    ファイルの最後の `</project>` 行の前に `<build>` セクションを追加します。 このセクションには、次の行が含まれる必要があります。
   
        <build>
            <plugins>
                <!-- build for Java 1.7, even if you're on a later version -->
                <plugin>
                    <groupId>org.apache.maven.plugins</groupId>
                    <artifactId>maven-compiler-plugin</artifactId>
                    <version>3.3</version>
                    <configuration>
                        <source>1.7</source>
                        <target>1.7</target>
                    </configuration>
                </plugin>
                <!-- build an uber jar -->
                <plugin>
                    <groupId>org.apache.maven.plugins</groupId>
                    <artifactId>maven-shade-plugin</artifactId>
                    <version>2.3</version>
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
   
    これらのエントリは、プロジェクトのビルド方法を定義します。 具体的に言うと、プロジェクトで使用する Java のバージョンと、クラスターにデプロイするための uberjar の構築方法です。
   
    変更を加えたら、ファイルを保存します。
4. **exampleudf/src/main/java/com/microsoft/examples/App.java** の名前を **ExampleUDF.java** に変更して、このファイルをエディターで開きます。
5. **ExampleUDF.java** ファイルの内容を次のコードに置き換えて、ファイルを保存します。
   
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
   
    こうすることで、文字列値を受け取って、その文字列の小文字のバージョンを返す UDF が実装されます。

## <a name="build-and-install-the-udf"></a>UDF をビルドしてインストールする
1. 次のコマンドを使用して、UDF をコンパイルしパッケージ化します。
   
        mvn compile package
   
    これで、UDF がビルドされ、 **exampleudf/target/ExampleUDF-1.0-SNAPSHOT.jar**にパッケージ化されます。
2. `scp` コマンドを使用して、ファイルを HDInsight クラスターにコピーします。
   
        scp ./target/ExampleUDF-1.0-SNAPSHOT.jar myuser@mycluster-ssh.azurehdinsight
   
    **myuser** をクラスターの SSH ユーザー アカウントに置き換えます。 **mycluster** をクラスター名に置き換えます。 SSH アカウントをセキュリティで保護するためにパスワードを使用している場合は、そのパスワードの入力を求められます。 証明書を使用している場合は、 `-i` パラメーターを使用して、秘密キー ファイルを指定することが必要な場合があります。
3. SSH を使用してクラスターに接続します。 
   
        ssh myuser@mycluster-ssh.azurehdinsight.net
   
    詳細については、[HDInsight での SSH の使用](hdinsight-hadoop-linux-use-ssh-unix.md)に関するページを参照してください。

4. SSH セッションで jar ファイルを HDInsight ストレージにコピーします。
   
        hdfs dfs -put ExampleUDF-1.0-SNAPSHOT.jar /example/jars

## <a name="use-the-udf-from-hive"></a>Hive から UDF を使用する
1. SSH セッションから、次のコマンドを使用して Beelineクライアントを開始します。
   
        beeline -u 'jdbc:hive2://localhost:10001/;transportMode=http' -n admin
   
    このコマンドは、クラスターのログイン アカウントに既定値の **admin** を使用していることを前提としています。
2. `jdbc:hive2://localhost:10001/>` プロンプトが表示されたら、次のように入力して、UDF を Hive に追加し、関数として公開します。
   
        ADD JAR wasbs:///example/jars/ExampleUDF-1.0-SNAPSHOT.jar;
        CREATE TEMPORARY FUNCTION tolower as 'com.microsoft.examples.ExampleUDF';
3. UDF を使用して、テーブルから取得した値を小文字の文字列に変換します。
   
        SELECT tolower(deviceplatform) FROM hivesampletable LIMIT 10;
   
    これによって、テーブルからデバイスのプラットフォーム (Android、Windows、iOS など) が選択され、その文字列が小文字に変換されて表示されます。 出力は次のように表示されます。
   
        +----------+--+
        |   _c0    |
        +----------+--+
        | android  |
        | android  |
        | android  |
        | android  |
        | android  |
        | android  |
        | android  |
        | android  |
        | android  |
        | android  |
        +----------+--+

## <a name="next-steps"></a>次のステップ
Hive の他の使用方法について [HDInsight での Hive の使用](hdinsight-use-hive.md)を参照します。

Hive のユーザー定義関数の詳細について、apache.org で Hive wiki の [Hive 演算子とユーザー定義関数](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+UDF) のセクションを参照します。


