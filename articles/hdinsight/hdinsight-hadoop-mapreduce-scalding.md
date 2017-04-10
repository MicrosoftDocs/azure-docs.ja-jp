---
title: "Maven での Scalding MapReduce ジョブの開発 | Microsoft Docs"
description: "Maven を使用して Scalding MapReduce ジョブを作成した後、HDInsight クラスター上の Hadoop にジョブをデプロイして実行する方法を説明します。"
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 26a4d4e8-2623-4fae-a0ca-17792b7a5713
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 01/19/2017
ms.author: larryfr
translationtype: Human Translation
ms.sourcegitcommit: f3be777497d842f019c1904ec1990bd1f1213ba2
ms.openlocfilehash: 166ff7f3586932494984e87fc0df7d3d3d914c82
ms.lasthandoff: 01/20/2017


---
# <a name="develop-scalding-mapreduce-jobs-with-apache-hadoop-on-hdinsight"></a>HDInsight での Apache Hadoop による Scalding MapReduce ジョブの開発
Scalding は、Hadoop MapReduce ジョブの作成を容易にする Scala ライブラリです。 簡潔な構文とともに Scala との緊密な統合を提供します。

このドキュメントでは、Maven を使用して Scalding で記述された基本的なワード カウント MapReduce ジョブを作成する方法について説明します。 その後、HDInsight クラスターにこのジョブをデプロイして実行する方法を学習します。

## <a name="prerequisites"></a>前提条件
* **Azure サブスクリプション**。 [Azure 無料試用版の取得](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/)に関するページを参照してください。

* **HDInsight クラスターの Windows ベースまたは Linux ベースの Hadoop**。 詳細については、「[HDInsight での Linux ベースの Hadoop のプロビジョニング](hdinsight-hadoop-provision-linux-clusters.md)」または「[HDInsight での Windows ベースの Hadoop のプロビジョニング](hdinsight-provision-clusters.md)」を参照してください。

  > [!IMPORTANT]
  > Linux は、バージョン 3.4 以上の HDInsight で使用できる唯一のオペレーティング システムです。 詳細については、[Window での HDInsight の廃止](hdinsight-component-versioning.md#hdi-version-32-and-33-nearing-deprecation-date)に関する記事を参照してください。

* **[Maven](http://maven.apache.org/)**

* **[Java プラットフォーム JDK](http://www.oracle.com/technetwork/java/javase/downloads/index.html) 7 以降**

## <a name="create-and-build-the-project"></a>プロジェクトの作成とビルド

1. 次のコマンドを使用して、新しい Maven プロジェクトを作成します。
   
        mvn archetype:generate -DgroupId=com.microsoft.example -DartifactId=scaldingwordcount -DarchetypeGroupId=org.scala-tools.archetypes -DarchetypeArtifactId=scala-archetype-simple -DinteractiveMode=false
   
    このコマンドは、 **scaldingwordcount**という名前の新しいディレクトリを作成し、Scala アプリケーションのスキャフォールディングを作成します。

2. **scaldingwordcount** ディレクトリで、**pom.xml** ファイルを開いて内容を次のように置き換えます。
   
        <project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/maven-v4_0_0.xsd">
            <modelVersion>4.0.0</modelVersion>
            <groupId>com.microsoft.example</groupId>
            <artifactId>scaldingwordcount</artifactId>
            <version>1.0-SNAPSHOT</version>
            <name>${project.artifactId}</name>
            <properties>
            <maven.compiler.source>1.6</maven.compiler.source>
            <maven.compiler.target>1.6</maven.compiler.target>
            <encoding>UTF-8</encoding>
            </properties>
            <repositories>
            <repository>
                <id>conjars</id>
                <url>http://conjars.org/repo</url>
            </repository>
            <repository>
                <id>maven-central</id>
                <url>http://repo1.maven.org/maven2</url>
            </repository>
            </repositories>
            <dependencies>
            <dependency>
                <groupId>com.twitter</groupId>
                <artifactId>scalding-core_2.11</artifactId>
                <version>0.13.1</version>
            </dependency>
            <dependency>
                <groupId>org.apache.hadoop</groupId>
                <artifactId>hadoop-core</artifactId>
                <version>1.2.1</version>
                <scope>provided</scope>
            </dependency>
            </dependencies>
            <build>
            <sourceDirectory>src/main/scala</sourceDirectory>
            <plugins>
                <plugin>
                <groupId>org.scala-tools</groupId>
                <artifactId>maven-scala-plugin</artifactId>
                <version>2.15.2</version>
                <executions>
                    <execution>
                    <id>scala-compile-first</id>
                    <phase>process-resources</phase>
                    <goals>
                        <goal>add-source</goal>
                        <goal>compile</goal>
                    </goals>
                    </execution>
                </executions>
                </plugin>
                <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-shade-plugin</artifactId>
                <version>2.3</version>
                <configuration>
                    <transformers>
                    <transformer implementation="org.apache.maven.plugins.shade.resource.ApacheLicenseResourceTransformer">
                    </transformer>
                    </transformers>
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
                    <configuration>
                        <transformers>
                        <transformer implementation="org.apache.maven.plugins.shade.resource.ManifestResourceTransformer">
                            <mainClass>com.twitter.scalding.Tool</mainClass>
                        </transformer>
                        </transformers>
                    </configuration>
                    </execution>
                </executions>
                </plugin>
            </plugins>
            </build>
        </project>
   
    このファイルでは、プロジェクト、依存関係、およびプラグインが記述されています。 重要なエントリは次のとおりです。
   
   * **maven.compiler.source** および **maven.compiler.target**: このプロジェクトの Java のバージョンを設定します。

   * **repositories**: このプロジェクトで使用される依存関係ファイルを含むリポジトリです。

   * **scalding-core_2.11** および **hadoop-core**: このプロジェクトは Scalding と Hadoop の両方のコア パッケージに依存します。

   * **maven-scala-plugin**: Scala アプリケーションをコンパイルするためのプラグインです。

   * **maven-shade-plugin**: シェードされた (ファット) Jar を作成するためのプラグインです。 このプラグインはフィルターおよび変換を適用します。具体的には次のとおりです。
     
     * **フィルター**: 適用されたフィルターによって、jar ファイルに組み込まれるメタ情報が変更されます。 実行時に署名の例外が発生しないように、フィルターを使用して、依存関係で組み込まれる可能性のある、さまざまな署名ファイルを除外します。

     * **実行**: パッケージ フェーズ実行構成で、**com.twitter.scalding.Tool** クラスをパッケージのメイン クラスとして指定します。 これを指定しない場合は、hadoop コマンドでジョブを実行するときに、com.twitter.scalding.Tool を、アプリケーション ロジックを含むクラスと共に指定する必要があります。
    
3. この例ではテストを作成しないので、 **src/test** ディレクトリを削除します。

4. **src/main/scala/com/microsoft/example/App.scala** ファイルを開き、内容を次のように置き換えます。
   
        package com.microsoft.example
   
        import com.twitter.scalding._
   
        class WordCount(args : Args) extends Job(args) {
            // 1. Read lines from the specified input location
            // 2. Extract individual words from each line
            // 3. Group words and count them
            // 4. Write output to the specified output location
            TextLine(args("input"))
            .flatMap('line -> 'word) { line : String => tokenize(line) }
            .groupBy('word) { _.size }
            .write(Tsv(args("output")))
   
            //Tokenizer to split sentance into words
            def tokenize(text : String) : Array[String] = {
            text.toLowerCase.replaceAll("[^a-zA-Z0-9\\s]", "").split("\\s+")
            }
        }
   
    これにより、基本的なワード カウント ジョブが実装されます。

5. ファイルを保存して閉じます。

6. **scaldingwordcount** ディレクトリから次のコマンドを使用して、アプリケーションをビルドしてパッケージ化します。
   
        mvn package
   
    このジョブが完了すると、WordCount アプリケーションを含むパッケージが **target/scaldingwordcount-1.0-SNAPSHOT.jar**に作成されます。

## <a name="run-the-job-on-a-linux-based-cluster"></a>Linux ベースのクラスターでのジョブの実行

> [!NOTE]
> 次の手順では、SSH および Hadoop コマンドを使用します。 MapReduce ジョブを実行する他の方法については、「 [HDInsight での Hadoop MapReduce の使用](hdinsight-use-mapreduce.md)」を参照してください。


1. 次のコマンドを使用して、HDInsight クラスターにパッケージをアップロードします。
   
        scp target/scaldingwordcount-1.0-SNAPSHOT.jar username@clustername-ssh.azurehdinsight.net:
   
    これにより、ファイルがローカル システムからヘッド ノードにコピーされます。
   
   > [!NOTE]
   > SSH アカウントのセキュリティ保護にパスワードを使用している場合は、パスワードの入力が求められます。 SSH キーを使用している場合は、 `-i` パラメーターと、秘密キーのパスを使用する必要があることがあります。 たとえば、 `scp -i /path/to/private/key target/scaldingwordcount-1.0-SNAPSHOT.jar username@clustername-ssh.azurehdinsight.net:.`
   > 
   > 
2. 次のコマンドを使用して、クラスターのヘッド ノードに接続します。
   
        ssh username@clustername-ssh.azurehdinsight.net
   
   > [!NOTE]
   > SSH アカウントのセキュリティ保護にパスワードを使用している場合は、パスワードの入力が求められます。 SSH キーを使用している場合は、 `-i` パラメーターと、秘密キーのパスを使用する必要があることがあります。 たとえば、 `ssh -i /path/to/private/key username@clustername-ssh.azurehdinsight.net`

3. ヘッド ノードに接続した後、次のコマンドを使用してワード カウント ジョブを実行します
   
        yarn jar scaldingwordcount-1.0-SNAPSHOT.jar com.microsoft.example.WordCount --hdfs --input wasbs:///example/data/gutenberg/davinci.txt --output wasbs:///example/wordcountout
   
    これにより、前に実装した WordCount クラスが実行されます。 `--hdfs` は、HDFS を使用するようにジョブに指示します。 `--input` は入力テキスト ファイルを指定し、`--output` は出力の場所を指定します。
4. ジョブが完了した後、次の方法で出力を表示します。
   
        hdfs dfs -text wasbs:///example/wordcountout/*
   
    次のような情報が表示されます。
   
        writers 9
        writes  18
        writhed 1
        writing 51
        writings        24
        written 208
        writtenthese    1
        wrong   11
        wrongly 2
        wrongplace      1
        wrote   34
        wrotefootnote   1
        wrought 7

## <a name="run-the-job-on-a-windows-based-cluster"></a>Windows ベースのクラスターでのジョブの実行

次の手順では、Windows PowerShell を使用します。 MapReduce ジョブを実行する他の方法については、「 [HDInsight での Hadoop MapReduce の使用](hdinsight-use-mapreduce.md)」を参照してください。

[!INCLUDE [upgrade-powershell](../../includes/hdinsight-use-latest-powershell.md)]

1. Azure PowerShell を起動し、Azure アカウントにログインします。 資格情報を提供すると、コマンドがアカウントの情報を返します。
   
        Add-AzureRMAccount
   
        Id                             Type       ...
        --                             ----
        someone@example.com            User       ...

2. 複数のサブスクリプションがある場合、デプロイメントに使用するサブスクリプション ID を提供します。
   
        Select-AzureRMSubscription -SubscriptionID <YourSubscriptionId>
   
   > [!NOTE]
   > `Get-AzureRMSubscription` を使用して、アカウントに関連付けられているすべてのサブスクリプションのリストを取得することができます。これには、各サブスクリプション ID が含まれます。

3. WordCount ジョブをアップロードして実行するには、次のスクリプトを使用します。 `CLUSTERNAME` は HDInsight クラスターの名前に置き換え、`$fileToUpload` が **scaldingwordcount-1.0-SNAPSHOT.jar** ファイルへの正しいパスであることを確認します。
   
   ```powershell
    # Login to your Azure subscription
    # Is there an active Azure subscription?
    $sub = Get-AzureRmSubscription -ErrorAction SilentlyContinue
    if(-not($sub))
    {
        Add-AzureRmAccount
    }

    # Get cluster info
    $clusterName = Read-Host -Prompt "Enter the HDInsight cluster name"
    $creds=Get-Credential -Message "Enter the login for the cluster"
    $fileToUpload = Read-Host -Prompt "Enter the path to the scaldingwordcount-1.0-SNAPSHOT.jar file"
    $blobPath = "example/jars/scaldingwordcount-1.0-SNAPSHOT.jar"

    #Get the cluster info so we can get the resource group, storage, etc.
    $clusterInfo = Get-AzureRmHDInsightCluster -ClusterName $clusterName
    $resourceGroup = $clusterInfo.ResourceGroup
    $storageAccountName=$clusterInfo.DefaultStorageAccount.split('.')[0]
    $container=$clusterInfo.DefaultStorageContainer
    $storageAccountKey=(Get-AzureRmStorageAccountKey `
        -Name $storageAccountName `
        -ResourceGroupName $resourceGroup)[0].Value

    #Create a storage content and upload the file
    $context = New-AzureStorageContext `
        -StorageAccountName $storageAccountName `
        -StorageAccountKey $storageAccountKey

    Set-AzureStorageBlobContent `
        -File $fileToUpload `
        -Blob $blobPath `
        -Container $container `
        -Context $context

    #Create a job definition and start the job
    $jobDef=New-AzureRmHDInsightMapReduceJobDefinition `
        -JobName ScaldingWordCount `
        -JarFile wasbs:///example/jars/scaldingwordcount-1.0-SNAPSHOT.jar `
        -ClassName com.microsoft.example.WordCount `
        -arguments "--hdfs", `
                    "--input", `
                    "wasbs:///example/data/gutenberg/davinci.txt", `
                    "--output", `
                    "wasbs:///example/wordcountout"
    $job = Start-AzureRmHDInsightJob `
        -clustername $clusterName `
        -jobdefinition $jobDef `
        -HttpCredential $creds
    Write-Output "Job ID is: $job.JobId"
    Wait-AzureRmHDInsightJob `
        -ClusterName $clusterName `
        -JobId $job.JobId `
        -HttpCredential $creds
    #Download the output of the job
    Get-AzureStorageBlobContent `
        -Blob example/wordcountout/part-00000 `
        -Container $container `
        -Destination output.txt `
        -Context $context
    #Log any errors that occured
    Get-AzureRmHDInsightJobOutput `
        -Clustername $clusterName `
        -JobId $job.JobId `
        -HttpCredential $creds `
        -DisplayOutputType StandardError
   ```
   
     スクリプトの実行時に、HDInsight クラスターの管理者のユーザー名とパスワードを入力するように求められます。 ジョブが実行されているときに発生したエラーは、コンソールに記録されます。

4. ジョブが完了すると、出力は現在のディレクトリにある **output.txt** ファイルにダウンロードされます。 次のコマンドを実行して、結果を表示します。
   
        cat output.txt
   
    ファイルには次のような値が含まれます。
   
        writers 9
        writes  18
        writhed 1
        writing 51
        writings        24
        written 208
        writtenthese    1
        wrong   11
        wrongly 2
        wrongplace      1
        wrote   34
        wrotefootnote   1
        wrought 7

## <a name="next-steps"></a>次のステップ
これで、Scalding を使用して HDInsight 用の MapRedcue ジョブを作成する方法に関する説明は終わりです。次のリンクを使用して、Azure HDInsight を操作するその他の方法について調べることもできます。

* [HDInsight での Hive の使用](hdinsight-use-hive.md)
* [HDInsight の Hadoop での Pig の使用](hdinsight-use-pig.md)
* [HDInsight での MapReduce ジョブの使用](hdinsight-use-mapreduce.md)


