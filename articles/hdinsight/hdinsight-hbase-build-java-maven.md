---
title: "Windows ベースの Azure HDInsight での Java HBase アプリケーションのビルド | Microsoft Docs"
description: "Apache Maven を使用して Java ベースの Apache HBase アプリケーションをビルドし、Windows ベースの Azure HDInsight クラスターにデプロイする方法について説明します。"
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 7f4a4e02-45ab-40dd-842b-3ec034f256c9
ms.service: hdinsight
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/05/2017
ms.author: larryfr
ROBOTS: NOINDEX
translationtype: Human Translation
ms.sourcegitcommit: afe143848fae473d08dd33a3df4ab4ed92b731fa
ms.openlocfilehash: e7a2fef8860984c2e4e3749202868940b836ca9f
ms.lasthandoff: 03/17/2017


---
# <a name="use-maven-to-build-java-applications-that-use-hbase-with-windows-based-hdinsight-hadoop"></a>Windows ベースの HDInsight (Hadoop) 環境の HBase を使用する Java アプリケーションを Maven で構築する
Apache Maven を使用して Java で [Apache HBase](http://hbase.apache.org/) アプリケーションを作成し、ビルドする方法について説明します。 その後、このアプリケーションを Azure HDInsight (Hadoop) で使用します。

[Maven](http://maven.apache.org/) は、Java プロジェクトのソフトウェア、ドキュメント、レポートを作成するためのソフトウェア プロジェクト管理および包含ツールです。 この記事では、このツールを使用して、Azure HDInsight クラスターでの HBase テーブルの作成、クエリ、および削除を実行する基本的な Java アプリケーションを作成する方法について説明します。

> [!IMPORTANT]
> このドキュメントの手順では、Windows を使用する HDInsight クラスターが必要です。 Linux は、バージョン 3.4 以上の HDInsight で使用できる唯一のオペレーティング システムです。 詳細については、[Window での HDInsight の廃止](hdinsight-component-versioning.md#hdi-version-32-and-33-nearing-deprecation-date)に関する記事を参照してください。

## <a name="requirements"></a>必要条件
* [Java プラットフォーム JDK](http://www.oracle.com/technetwork/java/javase/downloads/index.html) 7 以降
* [Maven](http://maven.apache.org/)
* Windows ベースの HDInsight クラスターと HBase

    > [!NOTE] 
    > このドキュメントの手順は、HDInsight クラスター バージョン 3.2、3.3、3.4 でテスト済みです。 例で指定される既定値は、HDInsight 3.3 クラスターに対応しています。

## <a name="create-the-project"></a>プロジェクトを作成する
1. 開発環境のコマンド ラインから、プロジェクトを作成する場所にディレクトリを変更します(例: `cd code\hdinsight`)。
2. Maven でインストールされた **mvn** コマンドを使用し、プロジェクトのスキャフォールディングを生成します。
   
        mvn archetype:generate -DgroupId=com.microsoft.examples -DartifactId=hbaseapp -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
   
    このコマンドにより、**artifactID** パラメーターで指定した名前 (この例では **hbaseapp**) のディレクトリが現在の場所に作成されます。このディレクトリには、次の項目が含まれます。
   
   * **pom.xml**: プロジェクト オブジェクト モデル ([POM](http://maven.apache.org/guides/introduction/introduction-to-the-pom.html)) には、プロジェクトのビルドに使用される情報と構成の詳細が含まれています。
   * **src**: アプリケーションを作成する **main\java\com\microsoft\examples** ディレクトリに含まれるディレクトリです。
3. **src\test\java\com\microsoft\examples\apptest.java** ファイルはこの例では使用しないため、削除します。

## <a name="update-the-project-object-model"></a>プロジェクト オブジェクト モデルを更新する
1. **pom.xml** ファイルを編集し、`<dependencies>` セクション内に次のコードを追加します。
   
        <dependency>
          <groupId>org.apache.hbase</groupId>
          <artifactId>hbase-client</artifactId>
          <version>1.1.2</version>
        </dependency>
   
    このセクションは Maven に対して、**hbase-client** のバージョン **1.1.2** がプロジェクトに必要であることを伝えます。 この依存関係は、コンパイル時に既定の Maven リポジトリからダウンロードされます。 [Maven セントラル リポジトリ検索](http://search.maven.org/#artifactdetails%7Corg.apache.hbase%7Chbase-client%7C0.98.4-hadoop2%7Cjar) を使用して、この依存関係についての詳細を確認できます。
   
   > [!IMPORTANT]
   > バージョン番号は、HDInsight クラスターに付属の HBase のバージョンと一致する必要があります。 次の表を使用して、正しいバージョン番号を調べてください。
   > 
   > 
   
   | HDInsight クラスターのバージョン | 使用する HBase のバージョン |
   | --- | --- |
   | 3.2 |0.98.4-hadoop2 |
   | 3.3 |1.1.2 |
   
    HDInsight のバージョンとコンポーネントの詳細については、「 [HDInsight で使用できる Hadoop コンポーネントの種類を教えてください](hdinsight-component-versioning.md)」を参照してください。
2. HDInsight 3.3 クラスターを使用している場合は、 `<dependencies>` セクションに次のコードも追加する必要があります。
   
        <dependency>
            <groupId>org.apache.phoenix</groupId>
            <artifactId>phoenix-core</artifactId>
            <version>4.4.0-HBase-1.1</version>
        </dependency>
   
    この依存関係により、Hbase Version 1.1.x で使用される phoenix コア コンポーネントが読み込まれます。
3. **pom.xml** ファイルに次のコードを追加します。 このセクションは、ファイルの `<project>...</project>` タグ内に配置する必要があります (たとえば `</dependencies>` と `</project>` の間)。
   
        <build>
          <sourceDirectory>src</sourceDirectory>
          <resources>
              <resource>
                <directory>${basedir}/conf</directory>
                <filtering>false</filtering>
                <includes>
                  <include>hbase-site.xml</include>
                </includes>
              </resource>
            </resources>
          <plugins>
            <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-compiler-plugin</artifactId>
                <version>3.3</version>
                <configuration>
                    <source>1.7</source>
                    <target>1.7</target>
                </configuration>
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
   
    `<resources>` セクションにより、HBase の構成情報が含まれたリソース (**conf\hbase-site.xml**) が構成されます。
   
   > [!NOTE]
   > コードを介して構成値を設定することもできます。 その方法については、**CreateTable** サンプル内のコメントをご覧ください。
   > 
   > 
   
    この `<plugins>` セクションによって、[Maven Compiler Plugin](http://maven.apache.org/plugins/maven-compiler-plugin/) と [Maven Shade Plugin](http://maven.apache.org/plugins/maven-shade-plugin/) も構成されます。 トポロジのコンパイルにはコンパイラ プラグインが使用されます。 シャードのプラグインは、Maven でビルドされる JAR パッケージ内のライセンスの重複を防ぐために使用されます。 ライセンス ファイルの重複は、HDInsight クラスターでの実行時に発生するエラーの原因となるためです。 maven-shade-plugin を `ApacheLicenseResourceTransformer` 実装で使用すると、エラーを回避できます。
   
    また、maven-shade-plugin は、アプリケーションで必要とされるすべての依存関係を含む uber jar (または fat jar) も生成します。
4. **pom.xml** ファイルを保存します。
5. **conf** という名前の新しいディレクトリを **hbaseapp** ディレクトリ内に作成します。 **conf** ディレクトリに、**hbase-site.xml** という名前のファイルを作成します。 このファイルの内容として、次のコードを使用します。
   
        <?xml version="1.0"?>
        <?xml-stylesheet type="text/xsl" href="configuration.xsl"?>
        <!--
        /**
          * Copyright 2010 The Apache Software Foundation
          *
          * Licensed to the Apache Software Foundation (ASF) under one
          * or more contributor license agreements.  See the NOTICE file
          * distributed with this work for additional information
          * regarding copyright ownership.  The ASF licenses this file
          * to you under the Apache License, Version 2.0 (the
          * "License"); you may not use this file except in compliance
          * with the License.  You may obtain a copy of the License at
          *
          *     http://www.apache.org/licenses/LICENSE-2.0
          *
          * Unless required by applicable law or agreed to in writing, software
          * distributed under the License is distributed on an "AS IS" BASIS,
          * WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
          * See the License for the specific language governing permissions and
          * limitations under the License.
          */
        -->
        <configuration>
          <property>
            <name>hbase.cluster.distributed</name>
            <value>true</value>
          </property>
          <property>
            <name>hbase.zookeeper.quorum</name>
            <value>zookeeper0,zookeeper1,zookeeper2</value>
          </property>
          <property>
            <name>hbase.zookeeper.property.clientPort</name>
            <value>2181</value>
          </property>
        </configuration>
   
    このファイルは、HDInsight クラスター用の HBase 構成の読み込みに使用されます。
   
   > [!NOTE]
   > これは、HDInsight クラスター用の最小限の設定が含まれた最小限の hbase-site.xml ファイルです。

6. **hbase-site.xml** ファイルを保存します。

## <a name="create-the-application"></a>アプリケーションを作成する
1. **hbaseapp\src\main\java\com\microsoft\examples** ディレクトリに移動し、app.java ファイルの名前を **CreateTable.java** に変更します。
2. **CreateTable.java** ファイルを開き、既存の内容を次のコードで置き換えます。
   
        package com.microsoft.examples;
        import java.io.IOException;
   
        import org.apache.hadoop.conf.Configuration;
        import org.apache.hadoop.hbase.HBaseConfiguration;
        import org.apache.hadoop.hbase.client.HBaseAdmin;
        import org.apache.hadoop.hbase.HTableDescriptor;
        import org.apache.hadoop.hbase.TableName;
        import org.apache.hadoop.hbase.HColumnDescriptor;
        import org.apache.hadoop.hbase.client.HTable;
        import org.apache.hadoop.hbase.client.Put;
        import org.apache.hadoop.hbase.util.Bytes;
   
        public class CreateTable {
          public static void main(String[] args) throws IOException {
            Configuration config = HBaseConfiguration.create();
   
            // Example of setting zookeeper values for HDInsight
            // in code instead of an hbase-site.xml file
            //
            // config.set("hbase.zookeeper.quorum",
            //            "zookeepernode0,zookeepernode1,zookeepernode2");
            //config.set("hbase.zookeeper.property.clientPort", "2181");
            //config.set("hbase.cluster.distributed", "true");
            // The following sets the znode root for Linux-based HDInsight
            //config.set("zookeeper.znode.parent","/hbase-unsecure");
   
            // create an admin object using the config
            HBaseAdmin admin = new HBaseAdmin(config);
   
            // create the table...
            HTableDescriptor tableDescriptor = new HTableDescriptor(TableName.valueOf("people"));
            // ... with two column families
            tableDescriptor.addFamily(new HColumnDescriptor("name"));
            tableDescriptor.addFamily(new HColumnDescriptor("contactinfo"));
            admin.createTable(tableDescriptor);
   
            // define some people
            String[][] people = {
                { "1", "Marcel", "Haddad", "marcel@fabrikam.com"},
                { "2", "Franklin", "Holtz", "franklin@contoso.com" },
                { "3", "Dwayne", "McKee", "dwayne@fabrikam.com" },
                { "4", "Rae", "Schroeder", "rae@contoso.com" },
                { "5", "Rosalie", "burton", "rosalie@fabrikam.com"},
                { "6", "Gabriela", "Ingram", "gabriela@contoso.com"} };
   
            HTable table = new HTable(config, "people");
   
            // Add each person to the table
            //   Use the `name` column family for the name
            //   Use the `contactinfo` column family for the email
            for (int i = 0; i< people.length; i++) {
              Put person = new Put(Bytes.toBytes(people[i][0]));
              person.add(Bytes.toBytes("name"), Bytes.toBytes("first"), Bytes.toBytes(people[i][1]));
              person.add(Bytes.toBytes("name"), Bytes.toBytes("last"), Bytes.toBytes(people[i][2]));
              person.add(Bytes.toBytes("contactinfo"), Bytes.toBytes("email"), Bytes.toBytes(people[i][3]));
              table.put(person);
            }
            // flush commits and close the table
            table.flushCommits();
            table.close();
          }
        }
   
    これは、**people** という名前のテーブルを作成して定義済みユーザーを設定する **CreateTable** クラスです。
3. **CreateTable.java** ファイルを保存します。
4. **hbaseapp\src\main\java\com\microsoft\examples** ディレクトリに、**SearchByEmail.java** という名前の新しいファイルを作成します。 このファイルの内容として、以下のコードを使用します。
   
        package com.microsoft.examples;
        import java.io.IOException;
   
        import org.apache.hadoop.conf.Configuration;
        import org.apache.hadoop.hbase.HBaseConfiguration;
        import org.apache.hadoop.hbase.client.HTable;
        import org.apache.hadoop.hbase.client.Scan;
        import org.apache.hadoop.hbase.client.ResultScanner;
        import org.apache.hadoop.hbase.client.Result;
        import org.apache.hadoop.hbase.filter.RegexStringComparator;
        import org.apache.hadoop.hbase.filter.SingleColumnValueFilter;
        import org.apache.hadoop.hbase.filter.CompareFilter.CompareOp;
        import org.apache.hadoop.hbase.util.Bytes;
        import org.apache.hadoop.util.GenericOptionsParser;
   
        public class SearchByEmail {
          public static void main(String[] args) throws IOException {
            Configuration config = HBaseConfiguration.create();
   
            // Use GenericOptionsParser to get only the parameters to the class
            // and not all the parameters passed (when using WebHCat for example)
            String[] otherArgs = new GenericOptionsParser(config, args).getRemainingArgs();
            if (otherArgs.length != 1) {
              System.out.println("usage: [regular expression]");
              System.exit(-1);
            }
   
            // Open the table
            HTable table = new HTable(config, "people");
   
            // Define the family and qualifiers to be used
            byte[] contactFamily = Bytes.toBytes("contactinfo");
            byte[] emailQualifier = Bytes.toBytes("email");
            byte[] nameFamily = Bytes.toBytes("name");
            byte[] firstNameQualifier = Bytes.toBytes("first");
            byte[] lastNameQualifier = Bytes.toBytes("last");
   
            // Create a new regex filter
            RegexStringComparator emailFilter = new RegexStringComparator(otherArgs[0]);
            // Attach the regex filter to a filter
            //   for the email column
            SingleColumnValueFilter filter = new SingleColumnValueFilter(
              contactFamily,
              emailQualifier,
              CompareOp.EQUAL,
              emailFilter
            );
   
            // Create a scan and set the filter
            Scan scan = new Scan();
            scan.setFilter(filter);
   
            // Get the results
            ResultScanner results = table.getScanner(scan);
            // Iterate over results and print  values
            for (Result result : results ) {
              String id = new String(result.getRow());
              byte[] firstNameObj = result.getValue(nameFamily, firstNameQualifier);
              String firstName = new String(firstNameObj);
              byte[] lastNameObj = result.getValue(nameFamily, lastNameQualifier);
              String lastName = new String(lastNameObj);
              System.out.println(firstName + " " + lastName + " - ID: " + id);
              byte[] emailObj = result.getValue(contactFamily, emailQualifier);
              String email = new String(emailObj);
              System.out.println(firstName + " " + lastName + " - " + email + " - ID: " + id);
            }
            results.close();
            table.close();
          }
        }
   
    **SearchByEmail** クラスを使用し、メール アドレスによって行を照会できます。 正規表現フィルターが使用されるため、このクラスを使用するときに文字列または正規表現を指定できます。
5. **SearchByEmail.java** ファイルを保存します。
6. **hbaseapp\src\main\hava\com\microsoft\examples** ディレクトリに、**DeleteTable.java** という名前の新しいファイルを作成します。 このファイルの内容として、以下のコードを使用します。
   
        package com.microsoft.examples;
        import java.io.IOException;
   
        import org.apache.hadoop.conf.Configuration;
        import org.apache.hadoop.hbase.HBaseConfiguration;
        import org.apache.hadoop.hbase.client.HBaseAdmin;
   
        public class DeleteTable {
          public static void main(String[] args) throws IOException {
            Configuration config = HBaseConfiguration.create();
   
            // Create an admin object using the config
            HBaseAdmin admin = new HBaseAdmin(config);
   
            // Disable, and then delete the table
            admin.disableTable("people");
            admin.deleteTable("people");
          }
        }
   
    このクラスは、このサンプルのクリーンアップに使用するため、最初に **CreateTable** クラスで作成されたテーブルを無効にし、次にそれを削除します。
7. **DeleteTable.java** ファイルを保存します。

## <a name="build-and-package-the-application"></a>アプリケーションをビルドおよびパッケージ化する
1. コマンド プロンプトを開き、ディレクトリを **hbaseapp** ディレクトリに変更します。
2. 次のコマンドを使用し、アプリケーションを含む JAR ファイルをビルドします。
   
        mvn clean package
   
    これにより、前のビルド アーティファクトを整理し、まだインストールされていない依存関係をダウンロードして、アプリケーションをビルドしてパッケージ化します。
3. コマンドが完了すると、**hbaseapp\target** ディレクトリに **hbaseapp-1.0-SNAPSHOT.jar** という名前のファイルが格納されます。
   
   > [!NOTE]
   > **hbaseapp-1.0-SNAPSHOT.jar** ファイルは、アプリケーションの実行に必要なすべての依存関係を含む uber jar (fat jar とも呼ばれる) です。

## <a name="upload-the-jar-file-and-start-a-job"></a>JAR ファイルをアップロードしてジョブを開始する
「 [HDInsight での Hadoop ジョブ用データのアップロード](hdinsight-upload-data.md)」で説明されているように、ファイルを HDInsight にアップロードするには多くの方法があります。 次の手順では、Azure PowerShell を使用します。

[!INCLUDE [upgrade-powershell](../../includes/hdinsight-use-latest-powershell.md)]

1. Azure PowerShell をインストールし、構成した後で、**hbase-runner.psm1** という名前の新しいファイルを作成します。 このファイルの内容として以下を使用します。
   
        <#
        .SYNOPSIS
        Copies a file to the primary storage of an HDInsight cluster.
        .DESCRIPTION
        Copies a file from a local directory to the blob container for
        the HDInsight cluster.
        .EXAMPLE
        Start-HBaseExample -className "com.microsoft.examples.CreateTable"
        -clusterName "MyHDInsightCluster"
   
        .EXAMPLE
        Start-HBaseExample -className "com.microsoft.examples.SearchByEmail"
        -clusterName "MyHDInsightCluster"
        -emailRegex "contoso.com"
   
        .EXAMPLE
        Start-HBaseExample -className "com.microsoft.examples.SearchByEmail"
        -clusterName "MyHDInsightCluster"
        -emailRegex "^r" -showErr
        #>
   
        function Start-HBaseExample {
        [CmdletBinding(SupportsShouldProcess = $true)]
        param(
        #The class to run
        [Parameter(Mandatory = $true)]
        [String]$className,
   
        #The name of the HDInsight cluster
        [Parameter(Mandatory = $true)]
        [String]$clusterName,
   
        #Only used when using SearchByEmail
        [Parameter(Mandatory = $false)]
        [String]$emailRegex,
   
        #Use if you want to see stderr output
        [Parameter(Mandatory = $false)]
        [Switch]$showErr
        )
   
        Set-StrictMode -Version 3
   
        # Is the Azure module installed?
        FindAzure
   
        # Get the login for the HDInsight cluster
        $creds=Get-Credential -Message "Enter the login for the cluster" -UserName "admin"
   
        # The JAR
        $jarFile = "wasbs:///example/jars/hbaseapp-1.0-SNAPSHOT.jar"
   
        # The job definition
        $jobDefinition = New-AzureRmHDInsightMapReduceJobDefinition `
            -JarFile $jarFile `
            -ClassName $className `
            -Arguments $emailRegex
   
        # Get the job output
        $job = Start-AzureRmHDInsightJob `
            -ClusterName $clusterName `
            -JobDefinition $jobDefinition `
            -HttpCredential $creds
        Write-Host "Wait for the job to complete ..." -ForegroundColor Green
        Wait-AzureRmHDInsightJob `
            -ClusterName $clusterName `
            -JobId $job.JobId `
            -HttpCredential $creds
        if($showErr)
        {
        Write-Host "STDERR"
        Get-AzureRmHDInsightJobOutput `
                    -Clustername $clusterName `
                    -JobId $job.JobId `
                    -HttpCredential $creds `
                    -DisplayOutputType StandardError
        }
        Write-Host "Display the standard output ..." -ForegroundColor Green
        Get-AzureRmHDInsightJobOutput `
                    -Clustername $clusterName `
                    -JobId $job.JobId `
                    -HttpCredential $creds
        }
   
        <#
        .SYNOPSIS
        Copies a file to the primary storage of an HDInsight cluster.
        .DESCRIPTION
        Copies a file from a local directory to the blob container for
        the HDInsight cluster.
        .EXAMPLE
        Add-HDInsightFile -localPath "C:\temp\data.txt"
        -destinationPath "example/data/data.txt"
        -ClusterName "MyHDInsightCluster"
        .EXAMPLE
        Add-HDInsightFile -localPath "C:\temp\data.txt"
        -destinationPath "example/data/data.txt"
        -ClusterName "MyHDInsightCluster"
        -Container "MyContainer"
        #>
   
        function Add-HDInsightFile {
            [CmdletBinding(SupportsShouldProcess = $true)]
            param(
                #The path to the local file.
                [Parameter(Mandatory = $true)]
                [String]$localPath,
   
                #The destination path and file name, relative to the root of the container.
                [Parameter(Mandatory = $true)]
                [String]$destinationPath,
   
                #The name of the HDInsight cluster
                [Parameter(Mandatory = $true)]
                [String]$clusterName,
   
                #If specified, overwrites existing files without prompting
                [Parameter(Mandatory = $false)]
                [Switch]$force
            )
   
            Set-StrictMode -Version 3
   
            # Is the Azure module installed?
            FindAzure
   
            # Get authentication for the cluster
            $creds=Get-Credential
   
            # Does the local path exist?
            if (-not (Test-Path $localPath))
            {
                throw "Source path '$localPath' does not exist."
            }
   
            # Get the primary storage container
            $storage = GetStorage -clusterName $clusterName
   
            # Upload file to storage, overwriting existing files if -force was used.
            Set-AzureStorageBlobContent -File $localPath `
                -Blob $destinationPath `
                -force:$force `
                -Container $storage.container `
                -Context $storage.context
        }
   
        function FindAzure {
            # Is there an active Azure subscription?
            $sub = Get-AzureRmSubscription -ErrorAction SilentlyContinue
            if(-not($sub))
            {
                throw "No active Azure subscription found! If you have a subscription, use the Login-AzureRmAccount cmdlet to login to your subscription."
            }
        }
   
        function GetStorage {
            param(
                [Parameter(Mandatory = $true)]
                [String]$clusterName
            )
            $hdi = Get-AzureRmHDInsightCluster -ClusterName $clusterName
            # Does the cluster exist?
            if (!$hdi)
            {
                throw "HDInsight cluster '$clusterName' does not exist."
            }
            # Create a return object for context & container
            $return = @{}
            $storageAccounts = @{}
   
            # Get storage information
            $resourceGroup = $hdi.ResourceGroup
            $storageAccountName=$hdi.DefaultStorageAccount.split('.')[0]
            $container=$hdi.DefaultStorageContainer
            $storageAccountKey=(Get-AzureRmStorageAccountKey `
                -Name $storageAccountName `
            -ResourceGroupName $resourceGroup)[0].Value
            # Get the resource group, in case we need that
            $return.resourceGroup = $resourceGroup
            # Get the storage context, as we can't depend
            # on using the default storage context
            $return.context = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey
            # Get the container, so we know where to
            # find/store blobs
            $return.container = $container
            # Return storage accounts to support finding all accounts for
            # a cluster
            $return.storageAccount = $storageAccountName
            $return.storageAccountKey = $storageAccountKey
   
            return $return
        }
        # Only export the verb-phrase things
        export-modulemember *-*
   
    このファイルには 2 つのモジュールが含まれます。
   
   * **Add-HDInsightFile** - HDInsight へのファイルのアップロードに使用されます
   * **Start-HBaseExample** - 前に作成されたクラスの実行に使用されます
2. **hbase-runner.psm1** ファイルを保存します。
3. 新しい Azure PowerShell ウィンドウを開き、ディレクトリを **hbaseapp** ディレクトリに変更して、次のコマンドを実行します。
   
        PS C:\ Import-Module c:\path\to\hbase-runner.psm1
   
    前に作成した **hbase-runner.psm1** ファイルの場所にパスを変更します。 これにより、この Azure PowerShell セッションのモジュールが登録されます。
4. 次のコマンドを使用して、**hbaseapp-1.0-SNAPSHOT.jar** を HDInsight クラスターにアップロードします。
   
        Add-HDInsightFile -localPath target\hbaseapp-1.0-SNAPSHOT.jar -destinationPath example/jars/hbaseapp-1.0-SNAPSHOT.jar -clusterName hdinsightclustername
   
    **hdinsightclustername** を、使用する HDInsight クラスターの名前で置き換えます。 このコマンドは、HDInsight クラスターのプライマリ ストレージの **example/jars** の場所に **hbaseapp-1.0-SNAPSHOT.jar** をアップロードします。
5. ファイルをアップロードした後で、次のコードを使用し、**hbaseapp** を使用してテーブルを作成します。
   
        Start-HBaseExample -className com.microsoft.examples.CreateTable -clusterName hdinsightclustername
   
    **hdinsightclustername** を、使用する HDInsight クラスターの名前で置き換えます。
   
    このコマンドにより、HDInsight クラスターに **people** という名前の新しいテーブルが作成されます。 このコマンドを実行しても、コンソール ウィンドウに出力結果は表示されません。
6. テーブル内のエントリを検索するには、次のコマンドを使用します。
   
        Start-HBaseExample -className com.microsoft.examples.SearchByEmail -clusterName hdinsightclustername -emailRegex contoso.com
   
    **hdinsightclustername** を、使用する HDInsight クラスターの名前で置き換えます。
   
    このコマンドは **SearchByEmail** クラスを使用して、**contactinformation** の列ファミリの **email** 列に文字列 **contoso.com** が含まれている行を検索します。 次の結果が表示されます。
   
          Franklin Holtz - ID: 2
          Franklin Holtz - franklin@contoso.com - ID: 2
          Rae Schroeder - ID: 4
          Rae Schroeder - rae@contoso.com - ID: 4
          Gabriela Ingram - ID: 6
          Gabriela Ingram - gabriela@contoso.com - ID: 6
   
    `-emailRegex` の値に **fabrikam.com** を使用すると、メール フィールドに **fabrikam.com** が含まれているユーザーが返されます。 この検索は正規表現ベースのフィルターを使用して実装されるため、メールが "r" という文字で始まるエントリを返す **^r** などの正規表現を使用することもできます。

## <a name="delete-the-table"></a>テーブルを削除する
サンプルの操作が終了したら、Azure PowerShell セッションから次のコマンドを使用し、このサンプルで使用された **people** テーブルを削除します。

    Start-HBaseExample -className com.microsoft.examples.DeleteTable -clusterName hdinsightclustername

**hdinsightclustername** を、使用する HDInsight クラスターの名前で置き換えます。

## <a name="troubleshooting"></a>トラブルシューティング
### <a name="no-results-or-unexpected-results-when-using-start-hbaseexample"></a>Start-HBaseExample を使用したときに、結果が表示されないか、予期しない結果が表示される
`-showErr` パラメーターを使用して、ジョブの実行中に生成された標準エラー (STDERR) を表示します。


