---
title: Apache Maven を使用した Java HBase クライアントの構築 - Azure HDInsight
description: Apache Maven を使用して Java ベースの Apache HBase アプリケーションをビルドし、Azure HDInsight での HBase にデプロイする方法について説明します。
services: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive,seodec18
ms.topic: conceptual
ms.date: 11/27/2018
ms.openlocfilehash: fa831ad878d214515849787988ccb32f6c57ce20
ms.sourcegitcommit: 223604d8b6ef20a8c115ff877981ce22ada6155a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/22/2019
ms.locfileid: "58361763"
---
# <a name="build-java-applications-for-apache-hbase"></a>Apache HBase 向けの Java アプリケーションの構築

Java で [Apache HBase](https://hbase.apache.org/) アプリケーションを作成する方法を説明します。 その後、このアプリケーションを Azure HDInsight での HBase で使用します。

このドキュメントの手順では、[Apache Maven](https://maven.apache.org/) を使用して、プロジェクトを作成およびビルドします。 Maven は、Java プロジェクトのソフトウェア、ドキュメント、レポートを作成するためのソフトウェア プロジェクト管理および包含ツールです。

> [!NOTE]  
> このドキュメントの手順のテストは、HDInsight 3.6 で行われたものが最新です。

> [!IMPORTANT]  
> このドキュメントの手順では、Linux を使用する HDInsight クラスターが必要です。 Linux は、バージョン 3.4 以上の HDInsight で使用できる唯一のオペレーティング システムです。 詳細については、[Windows での HDInsight の提供終了](../hdinsight-component-versioning.md#hdinsight-windows-retirement)に関する記事を参照してください。

## <a name="requirements"></a>必要条件

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

* [Java プラットフォーム JDK](https://aka.ms/azure-jdks) 8 以降

    > [!NOTE]  
    > HDInsight 3.5 以降では Java 8 が必要です。 以前のバージョンの HDInsight では、Java 7 が必要です。

* [Apache Maven](https://maven.apache.org/)

* [Linux ベースの Azure HDInsight クラスターと Apache HBase](apache-hbase-tutorial-get-started-linux.md#create-apache-hbase-cluster)

## <a name="create-the-project"></a>プロジェクトを作成する

1. 開発環境のコマンド ラインから、プロジェクトを作成する場所にディレクトリを変更します(例: `cd code\hbase`)。

2. Maven でインストールされた **mvn** コマンドを使用し、プロジェクトのスキャフォールディングを生成します。

    ```bash
    mvn archetype:generate -DgroupId=com.microsoft.examples -DartifactId=hbaseapp -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
    ```

    > [!NOTE]  
    > PowerShell を使用している場合は、`-D` パラメーターを二重引用符で囲む必要があります。
    >
    > `mvn archetype:generate "-DgroupId=com.microsoft.examples" "-DartifactId=hbaseapp" "-DarchetypeArtifactId=maven-archetype-quickstart" "-DinteractiveMode=false"`

    このコマンドにより、**artifactID** パラメーターで指定した名前 (この例では **hbaseapp**) のディレクトリが作成されます。このディレクトリには、次の項目が含まれます。

   * **pom.xml**: プロジェクト オブジェクト モデル ([POM](https://maven.apache.org/guides/introduction/introduction-to-the-pom.html)) には、プロジェクトのビルドに使用される情報と構成の詳細が含まれています。
   * **src**:アプリケーションを作成する **main/java/com/microsoft/examples** ディレクトリに含まれるディレクトリです。

3. `src/test/java/com/microsoft/examples/apptest.java` ファイルを削除します。 この例では使用されません。

## <a name="update-the-project-object-model"></a>プロジェクト オブジェクト モデルを更新する

1. `pom.xml` ファイルを編集し、`<dependencies>` セクション内に次のコードを追加します。

   ```xml
    <dependency>
        <groupId>org.apache.hbase</groupId>
        <artifactId>hbase-client</artifactId>
        <version>1.1.2</version>
    </dependency>
    <dependency>
        <groupId>org.apache.phoenix</groupId>
        <artifactId>phoenix-core</artifactId>
        <version>4.4.0-HBase-1.1</version>
    </dependency>
   ```

    このセクションは、プロジェクトに **hbase-client** および **phoenix-core** コンポーネントが必要であることを示しています。 この依存関係は、コンパイル時に既定の Maven リポジトリからダウンロードされます。 [Maven セントラル リポジトリ検索](https://search.maven.org/#artifactdetails%7Corg.apache.hbase%7Chbase-client%7C0.98.4-hadoop2%7Cjar) を使用して、この依存関係についての詳細を確認できます。

   > [!IMPORTANT]  
   > hbase-client のバージョン番号は、HDInsight クラスターに付属の Apache HBase のバージョンと一致する必要があります。 次の表を使用して、正しいバージョン番号を調べてください。

   | HDInsight クラスターのバージョン | 使用する Apache HBase のバージョン |
   | --- | --- |
   | 3.2 |0.98.4-hadoop2 |
   | 3.3、3.4、3.5、3.6 |1.1.2 |

    HDInsight のバージョンとコンポーネントの詳細については、[HDInsight で使用できるさまざまな Apache Hadoop コンポーネント](../hdinsight-component-versioning.md)に関するページを参照してください。

3. **pom.xml** ファイルに次のコードを追加します。 このテキストは、ファイルの `<project>...</project>` タグ内に配置する必要があります (たとえば `</dependencies>` と `</project>` の間)。

   ```xml
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
                <source>1.8</source>
                <target>1.8</target>
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
   ```

    このセクションにより、HBase の構成情報が含まれたリソース (`conf/hbase-site.xml`) が構成されます。

   > [!NOTE]  
   > コードを介して構成値を設定することもできます。 `CreateTable` サンプル内のコメントをご覧ください。

    このセクションによって、[Apache Maven Compiler Plugin](https://maven.apache.org/plugins/maven-compiler-plugin/) と [Apache Maven Shade Plugin](https://maven.apache.org/plugins/maven-shade-plugin/) も構成されます。 トポロジのコンパイルにはコンパイラ プラグインが使用されます。 シャードのプラグインは、Maven でビルドされる JAR パッケージ内のライセンスの重複を防ぐために使用されます。 このプラグインは、HDInsight クラスターでの実行時に発生する "ライセンス ファイルの重複" エラーを回避するために使用されます。 maven-shade-plugin を `ApacheLicenseResourceTransformer` 実装で使用すると、エラーを回避できます。

    また、maven-shade-plugin は、アプリケーションで必要とされるすべての依存関係を含む uber jar も生成します。

4. `pom.xml` ファイルを保存します。

5. `conf` という名前のディレクトリを `hbaseapp` ディレクトリに作成します。 このディレクトリを使用して、HBase に接続するための構成情報を保持します。

6. 次のコマンドを使用して、HBase クラスターから `conf` ディレクトリに HBase の構成をコピーします。 `USERNAME` を SSH ログインの名前に置き換えます。 `CLUSTERNAME` には、HDInsight クラスター名を指定します。

    ```bash
    scp USERNAME@CLUSTERNAME-ssh.azurehdinsight.net:/etc/hbase/conf/hbase-site.xml ./conf/hbase-site.xml
    ```

   `ssh` および `scp` の使用方法の詳細については、[HDInsight での SSH の使用](../hdinsight-hadoop-linux-use-ssh-unix.md)に関するページをご覧ください。

## <a name="create-the-application"></a>アプリケーションを作成する

1. `hbaseapp/src/main/java/com/microsoft/examples` ディレクトリに移動して、app.java ファイルの名前を `CreateTable.java` に変更します。

2. `CreateTable.java` ファイルを開き、既存の内容を次のテキストで置き換えます。

   ```java
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
        //
        //NOTE: Actual zookeeper host names can be found using Ambari:
        //curl -u admin:PASSWORD -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/hosts"

        //Linux-based HDInsight clusters use /hbase-unsecure as the znode parent
        config.set("zookeeper.znode.parent","/hbase-unsecure");

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
   ```

    このコードは、**people** という名前のテーブルを作成して定義済みユーザーを設定する **CreateTable** クラスです。

3. `CreateTable.java` ファイルを保存します。

4. `hbaseapp/src/main/java/com/microsoft/examples` ディレクトリに `SearchByEmail.java` という名前のファイルを作成します。 このファイルの内容として、次のテキストを使用します。

   ```java
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

        // Create a regex filter
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
   ```

    **SearchByEmail** クラスを使用し、メール アドレスによって行を照会できます。 正規表現フィルターが使用されるため、このクラスを使用するときに文字列または正規表現を指定できます。

5. `SearchByEmail.java` ファイルを保存します。

6. `hbaseapp/src/main/hava/com/microsoft/examples` ディレクトリに `DeleteTable.java` という名前のファイルを作成します。 このファイルの内容として、次のテキストを使用します。

   ```java
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
   ```

    このクラスは、このサンプルの HBase テーブルをクリーンアップするため、最初に `CreateTable` クラスで作成されたテーブルを無効にし、次にそれを削除します。

7. `DeleteTable.java` ファイルを保存します。

## <a name="build-and-package-the-application"></a>アプリケーションをビルドおよびパッケージ化する

1. `hbaseapp` ディレクトリで次のコマンドを使用して、アプリケーションが含まれた JAR ファイルをビルドします。

    ```bash
    mvn clean package
    ```

    このコマンドは、アプリケーションを .jar ファイルにビルドしてパッケージ化します。

2. コマンドが完了すると、`hbaseapp/target` ディレクトリに `hbaseapp-1.0-SNAPSHOT.jar` という名前のファイルが格納されます。

   > [!NOTE]  
   > `hbaseapp-1.0-SNAPSHOT.jar` ファイルは、uber jar です。 これには、アプリケーションを実行するために必要なすべての依存関係が含まれます。


## <a name="upload-the-jar-and-run-jobs-ssh"></a>JAR をアップロードしてジョブを実行する (SSH)

次の手順では、`scp` を使用して、HDInsight クラスターの Apache HBase のプライマリ ヘッドノードに JAR をコピーします。 その後、`ssh` コマンドを使用してクラスターに接続し、ヘッド ノードで例を直接実行します。

1. jar をクラスターにアップロードするには、次のコマンドを使用します。

    ```bash
    scp ./target/hbaseapp-1.0-SNAPSHOT.jar USERNAME@CLUSTERNAME-ssh.azurehdinsight.net:hbaseapp-1.0-SNAPSHOT.jar
    ```

    `USERNAME` を SSH ログインの名前に置き換えます。 `CLUSTERNAME` には、HDInsight クラスター名を指定します。

2. HBase クラスターに接続するには、次のコマンドを使用します。

    ```bash
    ssh USERNAME@CLUSTERNAME-ssh.azurehdinsight.net
    ```

    `USERNAME` を SSH ログインの名前に置き換えます。 `CLUSTERNAME` には、HDInsight クラスター名を指定します。

3. Java アプリケーションを使用する HBase テーブルを作成するには、次のコマンドを使用します。

    ```bash
    yarn jar hbaseapp-1.0-SNAPSHOT.jar com.microsoft.examples.CreateTable
    ```

    このコマンドは、**people** という名前の HBase テーブルを作成してデータを設定します。

4. テーブルに格納されている電子メール アドレスを検索するには、次のコマンドを使用します。

    ```bash
    yarn jar hbaseapp-1.0-SNAPSHOT.jar com.microsoft.examples.SearchByEmail contoso.com
    ```

    次の結果が表示されます。

        Franklin Holtz - ID: 2
        Franklin Holtz - franklin@contoso.com - ID: 2
        Rae Schroeder - ID: 4
        Rae Schroeder - rae@contoso.com - ID: 4
        Gabriela Ingram - ID: 6
        Gabriela Ingram - gabriela@contoso.com - ID: 6

5. テーブルを削除するには、次のコマンドを使用します。

    ```bash
    yarn jar hbaseapp-1.0-SNAPSHOT.jar com.microsoft.examples.DeleteTable
    ```

## <a name="upload-the-jar-and-run-jobs-powershell"></a>JAR をアップロードしてジョブを実行する (PowerShell)

次の手順では、Azure PowerShell を使用して、JAR を Apache HBase クラスターの既定のストレージにアップロードします。 その後、HDInsight コマンドレットを使用して、例をリモートで実行します。

1. Azure PowerShell をインストールし、構成した後で、`hbase-runner.psm1` という名前のファイルを作成します。 このファイルの内容として、次のテキストを使用します。

   ```powershell
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
    $jarFile = "wasb:///example/jars/hbaseapp-1.0-SNAPSHOT.jar"

    # The job definition
    $jobDefinition = New-AzHDInsightMapReduceJobDefinition `
        -JarFile $jarFile `
        -ClassName $className `
        -Arguments $emailRegex

    # Get the job output
    $job = Start-AzHDInsightJob `
        -ClusterName $clusterName `
        -JobDefinition $jobDefinition `
        -HttpCredential $creds
    Write-Host "Wait for the job to complete ..." -ForegroundColor Green
    Wait-AzHDInsightJob `
        -ClusterName $clusterName `
        -JobId $job.JobId `
        -HttpCredential $creds
    if($showErr)
    {
    Write-Host "STDERR"
    Get-AzHDInsightJobOutput `
                -Clustername $clusterName `
                -JobId $job.JobId `
                -HttpCredential $creds `
                -DisplayOutputType StandardError
    }
    Write-Host "Display the standard output ..." -ForegroundColor Green
    Get-AzHDInsightJobOutput `
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
        Set-AzStorageBlobContent -File $localPath `
            -Blob $destinationPath `
            -force:$force `
            -Container $storage.container `
            -Context $storage.context
    }

    function FindAzure {
        # Is there an active Azure subscription?
        $sub = Get-AzSubscription -ErrorAction SilentlyContinue
        if(-not($sub))
        {
            throw "No active Azure subscription found! If you have a subscription, use the Connect-AzAccount cmdlet to login to your subscription."
        }
    }

    function GetStorage {
        param(
            [Parameter(Mandatory = $true)]
            [String]$clusterName
        )
        $hdi = Get-AzHDInsightCluster -ClusterName $clusterName
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
        $storageAccountKey=(Get-AzStorageAccountKey `
            -Name $storageAccountName `
        -ResourceGroupName $resourceGroup)[0].Value
        # Get the resource group, in case we need that
        $return.resourceGroup = $resourceGroup
        # Get the storage context, as we can't depend
        # on using the default storage context
        $return.context = New-AzStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey
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
   ```

    このファイルには 2 つのモジュールが含まれます。

   * **Add-HDInsightFile** - クラスターへのファイルのアップロードに使用されます
   * **Start-HBaseExample** - 前に作成されたクラスの実行に使用されます

2. `hbase-runner.psm1` ファイルを保存します。

3. 新しい Azure PowerShell ウィンドウを開き、ディレクトリを `hbaseapp` ディレクトリに変更して、次のコマンドを実行します。

    ```powershell
    PS C:\ Import-Module c:\path\to\hbase-runner.psm1
    ```

    前に作成した `hbase-runner.psm1` ファイルの場所にパスを変更します。 このコマンドは、モジュールを Azure PowerShell に登録します。

4. 次のコマンドを使用して、クラスターに `hbaseapp-1.0-SNAPSHOT.jar` をアップロードします。

    ```powershell
    Add-HDInsightFile -localPath target\hbaseapp-1.0-SNAPSHOT.jar -destinationPath example/jars/hbaseapp-1.0-SNAPSHOT.jar -clusterName hdinsightclustername
    ```

    `hdinsightclustername` をクラスターの名前に置き換えます。 プロンプトが表示されたら、クラスターのログイン (管理者) 名とパスワードを入力します。 このコマンドは、`hbaseapp-1.0-SNAPSHOT.jar` をクラスターのプライマリ ストレージの `example/jars` の場所にアップロードします。

5. `hbaseapp` を使用してテーブルを作成するには、次のコマンドを使用します。

    ```powershell
    Start-HBaseExample -className com.microsoft.examples.CreateTable -clusterName hdinsightclustername
    ```

    `hdinsightclustername` をクラスターの名前に置き換えます。 プロンプトが表示されたら、クラスターのログイン (管理者) 名とパスワードを入力します。

    このコマンドにより、HDInsight クラスターでの HBase に **people** という名前のテーブルが作成されます。 このコマンドを実行しても、コンソール ウィンドウに出力結果は表示されません。

6. テーブル内のエントリを検索するには、次のコマンドを使用します。

    ```powershell
    Start-HBaseExample -className com.microsoft.examples.SearchByEmail -clusterName hdinsightclustername -emailRegex contoso.com
    ```

    `hdinsightclustername` をクラスターの名前に置き換えます。 プロンプトが表示されたら、クラスターのログイン (管理者) 名とパスワードを入力します。

    このコマンドは `SearchByEmail` クラスを使用して、`contactinformation` の列ファミリの `email` 列に文字列 `contoso.com` が含まれている行を検索します。 次の結果が表示されます。

          Franklin Holtz - ID: 2
          Franklin Holtz - franklin@contoso.com - ID: 2
          Rae Schroeder - ID: 4
          Rae Schroeder - rae@contoso.com - ID: 4
          Gabriela Ingram - ID: 6
          Gabriela Ingram - gabriela@contoso.com - ID: 6

    `-emailRegex` の値に **fabrikam.com** を使用すると、メール フィールドに **fabrikam.com** が含まれているユーザーが返されます。 検索用語に正規表現を使用することもできます。 たとえば、**^r** で検索すると、'r' から始まる電子メール アドレスが返されます。

### <a name="no-results-or-unexpected-results-when-using-start-hbaseexample"></a>Start-HBaseExample を使用したときに、結果が表示されないか、予期しない結果が表示される

`-showErr` パラメーターを使用して、ジョブの実行中に生成された標準エラー (STDERR) を表示します。

## <a name="delete-the-table"></a>テーブルを削除する

例の操作が終了したら、次のコマンドを使用して、この例で使用された **people** テーブルを削除します。

__`ssh` セッションから__:

`yarn jar hbaseapp-1.0-SNAPSHOT.jar com.microsoft.examples.DeleteTable`

__Azure PowerShell から__:

`Start-HBaseExample -className com.microsoft.examples.DeleteTable -clusterName hdinsightclustername`

## <a name="next-steps"></a>次の手順

[Apache HBase と SQuirreL SQL を使用する方法を確認する](apache-hbase-phoenix-squirrel-linux.md)
