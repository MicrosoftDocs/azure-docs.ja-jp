---
title: "HDInsight での Hadoop Oozie の使用 | Microsoft Docs"
description: "ビッグ データ サービスとして HDInsight で Hadoop Oozie を使用します。 Oozie ワークフローを定義し、Oozie ジョブを送信する方法について説明します。"
services: hdinsight
documentationcenter: 
tags: azure-portal
author: mumian
manager: jhubbard
editor: cgronlun
ms.assetid: 870098f0-f416-4491-9719-78994bf4a369
ms.service: hdinsight
ms.custom: hdinsightactive
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/22/2017
ms.author: jgao
ROBOTS: NOINDEX
translationtype: Human Translation
ms.sourcegitcommit: 0587dfcd6079fc8df91bad5a5f902391d3657a6b
ms.openlocfilehash: e6749bdf73acc9c05e71c85410bb3d95c57a0a9f
ms.lasthandoff: 12/08/2016


---
# <a name="use-oozie-with-hadoop-to-define-and-run-a-workflow-in-hdinsight"></a>HDInsight での Oozie と Hadoop を使用したワークフローの定義と実行
[!INCLUDE [oozie-selector](../../includes/hdinsight-oozie-selector.md)]

HDInsight で Apache Oozie を使用して、ワークフローを定義する方法と実行する方法について説明します。 Oozie コーディネーターについては、「[HDInsight での時間ベースの Hadoop Oozie コーディネーターの使用][hdinsight-oozie-coordinator-time]」を参照してください。 Azure Data Factory については、「[Data Factory で Pig および Hive を使用する][azure-data-factory-pig-hive]」を参照してください。

Apache Oozie は Hadoop ジョブを管理するワークフローおよび調整システムです。 Hadoop スタックと統合されていて、Apache MapReduce、Apache Pig、Apache Hive、Apache Sqoop の Hadoop ジョブをサポートしています。 Java プログラムやシェル スクリプトのような、システム特有のジョブのスケジュールを設定するのに使用することもできます。

このチュートリアルの手順に従って実装するワークフローには次の 2 つのアクションが含まれます。

![ワークフロー図][img-workflow-diagram]

1. Hive アクションは、HiveQL スクリプトを実行して、log4j ファイルのログ レベル タイプごとの出現回数をカウントします。 各 log4j ファイルは、タイプと重要度を表す [LOG LEVEL] フィールドを含むフィールド行で構成されています。以下に例を示します。
   
        2012-02-03 18:35:34 SampleClass6 [INFO] everything normal for id 577725851
        2012-02-03 18:35:34 SampleClass4 [FATAL] system problem at id 1991281254
        2012-02-03 18:35:34 SampleClass3 [DEBUG] detail for id 1304807656
        ...
   
    Hive スクリプトの出力は次のようになります。
   
        [DEBUG] 434
        [ERROR] 3
        [FATAL] 1
        [INFO]  96
        [TRACE] 816
        [WARN]  4
   
    Hive の詳細については、[HDInsight での Hive の使用][hdinsight-use-hive]に関するページを参照してください。
2. Sqoop アクションは、Azure SQL データベースのテーブルに HiveQL 出力をエクスポートします。 Sqoop の詳細については、「[HDInsight での Hadoop Sqoop の使用][hdinsight-use-sqoop]」を参照してください。

> [!NOTE]
> HDInsight クラスターでサポートされている Oozie のバージョンについては、「[HDInsight で提供される Hadoop クラスター バージョンの新機能][hdinsight-versions]」を参照してください。
> 
> 

### <a name="prerequisites"></a>前提条件
このチュートリアルを読み始める前に、次の項目を用意する必要があります。

* **Azure PowerShell を実行できるワークステーション**。 
  

[!INCLUDE [upgrade-powershell](../../includes/hdinsight-use-latest-powershell.md)]
  

## <a name="define-oozie-workflow-and-the-related-hiveql-script"></a>Oozie ワークフローと関連 HiveQL スクリプトを定義する
Oozie ワークフロー定義は hPDL (XML プロセス定義言語) で書かれています。 既定のワークフロー ファイル名は *workflow.xml*です。 このチュートリアルで使用するワークフロー ファイルを次に示します。

    <workflow-app name="useooziewf" xmlns="uri:oozie:workflow:0.2">
        <start to = "RunHiveScript"/>

        <action name="RunHiveScript">
            <hive xmlns="uri:oozie:hive-action:0.2">
                <job-tracker>${jobTracker}</job-tracker>
                <name-node>${nameNode}</name-node>
                <configuration>
                    <property>
                        <name>mapred.job.queue.name</name>
                        <value>${queueName}</value>
                    </property>
                </configuration>
                <script>${hiveScript}</script>
                <param>hiveTableName=${hiveTableName}</param>
                <param>hiveDataFolder=${hiveDataFolder}</param>
                <param>hiveOutputFolder=${hiveOutputFolder}</param>
            </hive>
            <ok to="RunSqoopExport"/>
            <error to="fail"/>
        </action>

        <action name="RunSqoopExport">
            <sqoop xmlns="uri:oozie:sqoop-action:0.2">
                <job-tracker>${jobTracker}</job-tracker>
                <name-node>${nameNode}</name-node>
                <configuration>
                    <property>
                        <name>mapred.compress.map.output</name>
                        <value>true</value>
                    </property>
                </configuration>
            <arg>export</arg>
            <arg>--connect</arg>
            <arg>${sqlDatabaseConnectionString}</arg>
            <arg>--table</arg>
            <arg>${sqlDatabaseTableName}</arg>
            <arg>--export-dir</arg>
            <arg>${hiveOutputFolder}</arg>
            <arg>-m</arg>
            <arg>1</arg>
            <arg>--input-fields-terminated-by</arg>
            <arg>"\001"</arg>
            </sqoop>
            <ok to="end"/>
            <error to="fail"/>
        </action>

        <kill name="fail">
            <message>Job failed, error message[${wf:errorMessage(wf:lastErrorNode())}] </message>
        </kill>

        <end name="end"/>
    </workflow-app>

ワークフローでは 2 つのアクションが定義されています。 start-to アクションは *RunHiveScript*です。 このアクションが正常に実行された場合、次のアクションは *RunSqoopExport*です。

RunHiveScript には、変数がいくつかあります。 その値は、Azure PowerShell を使用してワークステーションから Oozie ジョブを送信するときに渡します。

<table border = "1">
<tr><th>ワークフローの変数</th><th>Description</th></tr>
<tr><td>${jobTracker}</td><td>Hadoop ジョブ トラッカーの URL を指定します。 HDInsight バージョン 3.0 と 2.1 では <strong>jobtrackerhost:9010</strong> を使用します。</td></tr>
<tr><td>${nameNode}</td><td>Hadoop 名前ノードの URL を指定します。 既定のファイル システムのアドレス (たとえば、<i>wasbs://&lt;containerName&gt;@&lt;storageAccountName&gt;.blob.core.windows.net</i>) を使用します。</td></tr>
<tr><td>${queueName}</td><td>ジョブの送信先になるキュー名を指定します。 <strong>既定値</strong>を使用します。</td></tr>
</table>

<table border = "1">
<tr><th>Hive アクションの変数</th><th>Description</th></tr>
<tr><td>${hiveDataFolder}</td><td>Hive の CREATE TABLE コマンドのソース ディレクトリを指定します。</td></tr>
<tr><td>${hiveOutputFolder}</td><td>INSERT OVERWRITE ステートメントの出力フォルダーを指定します。</td></tr>
<tr><td>${hiveTableName}</td><td>log4j データ ファイルを参照する Hive テーブルの名前を指定します。</td></tr>
</table>

<table border = "1">
<tr><th>Sqoop アクションの変数</th><th>Description</th></tr>
<tr><td>${sqlDatabaseConnectionString}</td><td>Azure SQL データベースの接続文字列を指定します。</td></tr>
<tr><td>${sqlDatabaseTableName}</td><td>データのエクスポート先となる Azure SQL データベース テーブルを指定します。</td></tr>
<tr><td>${hiveOutputFolder}</td><td>Hive の INSERT OVERWRITE ステートメントの出力フォルダーを指定します。 これは Sqoop エクスポート (export-dir) と同じフォルダーです。</td></tr>
</table>

Oozie ワークフローとワークフロー アクションの使用の詳細については、[Apache Oozie 4.0 のドキュメント][apache-oozie-400] (HDInsight バージョン 3.0 の場合) または [Apache Oozie 3.3.2 のドキュメント][apache-oozie-332] (HDInsight バージョン 2.1 の場合) を参照してください。

ワークフローの Hive アクションは、HiveQL スクリプト ファイルを呼び出します。 このスクリプト ファイルは HiveQL ステートメントを 3 つ含んでいます。

    DROP TABLE ${hiveTableName};
    CREATE EXTERNAL TABLE ${hiveTableName}(t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string) ROW FORMAT DELIMITED FIELDS TERMINATED BY ' ' STORED AS TEXTFILE LOCATION '${hiveDataFolder}';
    INSERT OVERWRITE DIRECTORY '${hiveOutputFolder}' SELECT t4 AS sev, COUNT(*) AS cnt FROM ${hiveTableName} WHERE t4 LIKE '[%' GROUP BY t4;

1. **DROP TABLE ステートメント** は、log4j Hive テーブルが存在する場合、削除します。
2. **CREATE TABLE ステートメント** は、log4j ログ ファイルの場所を指す log4j Hive 外部テーブルを作成します。 フィールド区切り記号はコンマ (,) です。 既定の行区切り記号は "\n" です。 Hive 外部テーブルは、Oozie ワークフローを複数回実行する場合に、データ ファイルが元の場所から削除されないようにするために使用されています。
3. **INSERT OVERWRITE ステートメント** は、log4j Hive テーブルの各ログ レベル タイプの出現回数をカウントし、その出力を Azure Storage の BLOB に保存します。

スクリプトでは 3 つの変数が使用されています。

* ${hiveTableName}
* ${hiveDataFolder}
* ${hiveOutputFolder}

ワークフロー定義ファイル (このチュートリアルでは workflow.xml) は、実行時にこの HiveQL スクリプトにこれらの値を渡します。

ワークフロー ファイルと HiveQL ファイルは、どちらも BLOB コンテナーに格納されます。  このチュートリアルで後ほど使用する PowerShell スクリプトでは、両方のファイルを既定のストレージ アカウントにコピーします。 

## <a name="submit-oozie-jobs-using-powershell"></a>PowerShell を使用して Oozie ジョブを送信する
現在、Azure PowerShell には Oozie ジョブを定義するコマンドレットが用意されていません。 **Invoke-RestMethod** コマンドレットを使用して Oozie Web サービスを呼び出すことができます。 Oozie Web サービス API は、HTTP REST JSON API です。 Oozie Web サービス API の詳細については、[Apache Oozie 4.0 のドキュメント][apache-oozie-400] (HDInsight クラスター バージョン 3.0 の場合) または [Apache Oozie 3.3.2 のドキュメント][apache-oozie-332] (HDInsight クラスター バージョン 2.1 の場合) を参照してください。

このセクションの PowerShell スクリプトでは、次の手順を実行します。

1. Azure に接続します。
2. Azure リソース グループを作成します。 詳細については、「[Azure リソース マネージャーでの Azure PowerShell の使用](../powershell-azure-resource-manager.md)」をご覧ください。
3. Azure SQL Database サーバー、Azure SQL Database、および 2 つのテーブルを作成します。 これらは、ワークフローで Sqoop アクションによって使用されます。
   
    テーブル名は *log4jLogCount*です。
4. Oozie ジョブの実行に使用する HDInsight クラスターを作成します。
   
    クラスターを確認するには、Azure ポータルまたは Azure PowerShell を使用します。
5. Oozie ワークフロー ファイルと HiveQL スクリプト ファイルを既定のファイル システムにコピーします。
   
    どちらのファイルも、パブリック BLOB コンテナーに格納されます。
   
   * HiveQL スクリプト (useoozie.hql) を Azure Storage (wasbs:///tutorials/useoozie/useoozie.hql) にコピーします。
   * workflow.xml を wasbs:///tutorials/useoozie/workflow.xml にコピーします。
   * データ ファイル (/example/data/sample.log) を wasbs:///tutorials/useoozie/data/sample.log にコピーします。
6. Oozie ジョブを送信します。
   
    OOzie ジョブの結果を確認するには、Visual Studio または他のツールを使用して Azure SQL Database に接続します。

このスクリプトを次に示します。  スクリプトは、Windows PowerShell ISE から実行できます。 構成する必要があるのは、最初の 7 つの変数だけです。

    #region - provide the following values

    $subscriptionID = "<Enter your Azure subscription ID>"

    # SQL Database server login credentials used for creating and connecting
    $sqlDatabaseLogin = "<Enter SQL Database Login Name>"
    $sqlDatabasePassword = "<Enter SQL Database Login Password>"

    # HDInsight cluster HTTP user credential used for creating and connectin
    $httpUserName = "admin"  # The default name is "admin"
    $httpPassword = "<Enter HDInsight Cluster HTTP User Password>"

    # Used for creating Azure service names
    $nameToken = "<Enter an Alias>"
    $namePrefix = $nameToken.ToLower() + (Get-Date -Format "MMdd")
    #endregion

    #region - variables

    # Resource group variables
    $resourceGroupName = $namePrefix + "rg"
    $location = "East US 2" # used by all Azure services defined in this tutorial

    # SQL database varialbes
    $sqlDatabaseServerName = $namePrefix + "sqldbserver"
    $sqlDatabaseName = $namePrefix + "sqldb"
    $sqlDatabaseConnectionString = "Data Source=$sqlDatabaseServerName.database.windows.net;Initial Catalog=$sqlDatabaseName;User ID=$sqlDatabaseLogin;Password=$sqlDatabasePassword;Encrypt=true;Trusted_Connection=false;"
    $sqlDatabaseMaxSizeGB = 10

    # Used for retrieving external IP address and creating firewall rules
    $ipAddressRestService = "http://bot.whatismyipaddress.com"
    $fireWallRuleName = "UseSqoop"

    # HDInsight variables
    $hdinsightClusterName = $namePrefix + "hdi"
    $defaultStorageAccountName = $namePrefix + "store"
    $defaultBlobContainerName = $hdinsightClusterName
    #endregion

    # Treat all errors as terminating
    $ErrorActionPreference = "Stop"

    #region - Connect to Azure subscription
    Write-Host "`nConnecting to your Azure subscription ..." -ForegroundColor Green
    try{Get-AzureRmContext}
    catch{
        Login-AzureRmAccount
        Select-AzureRmSubscription -SubscriptionId $subscriptionID
    }
    #endregion

    #region - Create Azure resouce group
    Write-Host "`nCreating an Azure resource group ..." -ForegroundColor Green
    try{
        Get-AzureRmResourceGroup -Name $resourceGroupName
    }
    catch{
        New-AzureRmResourceGroup -Name $resourceGroupName -Location $location
    }
    #endregion

    #region - Create Azure SQL database server
    Write-Host "`nCreating an Azure SQL Database server ..." -ForegroundColor Green
    try{
        Get-AzureRmSqlServer -ServerName $sqlDatabaseServerName -ResourceGroupName $resourceGroupName}
    catch{
        Write-Host "`nCreating SQL Database server ..."  -ForegroundColor Green

        $sqlDatabasePW = ConvertTo-SecureString -String $sqlDatabasePassword -AsPlainText -Force
        $sqlLoginCredentials = New-Object System.Management.Automation.PSCredential($sqlDatabaseLogin,$sqlDatabasePW)

        $sqlDatabaseServerName = (New-AzureRmSqlServer `
                                    -ResourceGroupName $resourceGroupName `
                                    -ServerName $sqlDatabaseServerName `
                                    -SqlAdministratorCredentials $sqlLoginCredentials `
                                    -Location $location).ServerName
        Write-Host "`tThe new SQL database server name is $sqlDatabaseServerName." -ForegroundColor Cyan

        Write-Host "`nCreating firewall rule, $fireWallRuleName ..." -ForegroundColor Green
        $workstationIPAddress = Invoke-RestMethod $ipAddressRestService
        New-AzureRmSqlServerFirewallRule `
            -ResourceGroupName $resourceGroupName `
            -ServerName $sqlDatabaseServerName `
            -FirewallRuleName "$fireWallRuleName-workstation" `
            -StartIpAddress $workstationIPAddress `
            -EndIpAddress $workstationIPAddress

        #To allow other Azure services to access the server add a firewall rule and set both the StartIpAddress and EndIpAddress to 0.0.0.0. 
        #Note that this allows Azure traffic from any Azure subscription to access the server.
        New-AzureRmSqlServerFirewallRule `
            -ResourceGroupName $resourceGroupName `
            -ServerName $sqlDatabaseServerName `
            -FirewallRuleName "$fireWallRuleName-Azureservices" `
            -StartIpAddress "0.0.0.0" `
            -EndIpAddress "0.0.0.0"
    }
    #endregion

    #region - Create and validate Azure SQL database
    Write-Host "`nCreating SQL Database, $sqlDatabaseName ..."  -ForegroundColor Green

    try {
        Get-AzureRmSqlDatabase `
            -ResourceGroupName $resourceGroupName `
            -ServerName $sqlDatabaseServerName `
            -DatabaseName $sqlDatabaseName
    }
    catch {
        New-AzureRMSqlDatabase `
            -ResourceGroupName $resourceGroupName `
            -ServerName $sqlDatabaseServerName `
            -DatabaseName $sqlDatabaseName `
            -Edition "Standard" `
            -RequestedServiceObjectiveName "S1"
    }
    #endregion

    #region - Create SQL database tables
    Write-Host "Creating the log4jlogs table  ..." -ForegroundColor Green

    $sqlDatabaseTableName = "log4jLogsCount"
    $cmdCreateLog4jCountTable = " CREATE TABLE [dbo].[$sqlDatabaseTableName](
            [Level] [nvarchar](10) NOT NULL,
            [Total] float,
        CONSTRAINT [PK_$sqlDatabaseTableName] PRIMARY KEY CLUSTERED
        (
        [Level] ASC
        )
        )"

    $conn = New-Object System.Data.SqlClient.SqlConnection
    $conn.ConnectionString = $sqlDatabaseConnectionString
    $conn.Open()

    # Create the log4jlogs table and index
    $cmd = New-Object System.Data.SqlClient.SqlCommand
    $cmd.Connection = $conn
    $cmd.CommandText = $cmdCreateLog4jCountTable
    $cmd.ExecuteNonQuery()

    $conn.close()
    #endregion

    #region - Create HDInsight cluster

    Write-Host "Creating the HDInsight cluster and the dependent services ..." -ForegroundColor Green

    # Create the default storage account
    New-AzureRmStorageAccount `
        -ResourceGroupName $resourceGroupName `
        -Name $defaultStorageAccountName `
        -Location $location `
        -Type Standard_LRS

    # Create the default Blob container
    $defaultStorageAccountKey = (Get-AzureRmStorageAccountKey `
                                    -ResourceGroupName $resourceGroupName `
                                    -Name $defaultStorageAccountName)[0].Value
    $defaultStorageAccountContext = New-AzureStorageContext `
                                        -StorageAccountName $defaultStorageAccountName `
                                        -StorageAccountKey $defaultStorageAccountKey 
    New-AzureStorageContainer `
        -Name $defaultBlobContainerName `
        -Context $defaultStorageAccountContext 

    # Create the HDInsight cluster
    $pw = ConvertTo-SecureString -String $httpPassword -AsPlainText -Force
    $httpCredential = New-Object System.Management.Automation.PSCredential($httpUserName,$pw)

    New-AzureRmHDInsightCluster `
        -ResourceGroupName $resourceGroupName `
        -ClusterName $HDInsightClusterName `
        -Location $location `
        -ClusterType Hadoop `
        -OSType Windows `
        -ClusterSizeInNodes 2 `
        -HttpCredential $httpCredential `
        -DefaultStorageAccountName "$defaultStorageAccountName.blob.core.windows.net" `
        -DefaultStorageAccountKey $defaultStorageAccountKey `
        -DefaultStorageContainer $defaultBlobContainerName 

    # Validate the cluster
    Get-AzureRmHDInsightCluster -ClusterName $hdinsightClusterName
    #endregion

    #region - copy Oozie workflow and HiveQL files

    Write-Host "Copy workflow definition and HiveQL script file ..." -ForegroundColor Green

    # Both files are stored in a public Blob
    $publicBlobContext = New-AzureStorageContext -StorageAccountName "hditutorialdata" -Anonymous

    # WASB folder for storing the Oozie tutorial files.
    $destFolder = "tutorials/useoozie"  # Do NOT use the long path here

    Start-CopyAzureStorageBlob `
        -Context $publicBlobContext `
        -SrcContainer "useoozie" `
        -SrcBlob "useooziewf.hql"  `
        -DestContext $defaultStorageAccountContext `
        -DestContainer $defaultBlobContainerName `
        -DestBlob "$destFolder/useooziewf.hql" `
        -Force

    Start-CopyAzureStorageBlob `
        -Context $publicBlobContext `
        -SrcContainer "useoozie" `
        -SrcBlob "workflow.xml"  `
        -DestContext $defaultStorageAccountContext `
        -DestContainer $defaultBlobContainerName `
        -DestBlob "$destFolder/workflow.xml" `
        -Force

    #validate the copy
    Get-AzureStorageBlob `
        -Context $defaultStorageAccountContext `
        -Container $defaultBlobContainerName `
        -Blob $destFolder/workflow.xml

    Get-AzureStorageBlob `
        -Context $defaultStorageAccountContext `
        -Container $defaultBlobContainerName `
        -Blob $destFolder/useooziewf.hql

    #endregion

    #region - copy the sample.log file

    Write-Host "Make a copy of the sample.log file ... " -ForegroundColor Green

    Start-CopyAzureStorageBlob `
        -Context $defaultStorageAccountContext `
        -SrcContainer $defaultBlobContainerName `
        -SrcBlob "example/data/sample.log"  `
        -DestContext $defaultStorageAccountContext `
        -DestContainer $defaultBlobContainerName `
        -destBlob "$destFolder/data/sample.log" 

    #validate the copy
    Get-AzureStorageBlob `
        -Context $defaultStorageAccountContext `
        -Container $defaultBlobContainerName `
        -Blob $destFolder/data/sample.log

    #endregion

    #region - submit Oozie job

    $storageUri="wasbs://$defaultBlobContainerName@$defaultStorageAccountName.blob.core.windows.net"

    $oozieJobName = $namePrefix + "OozieJob"

    #Oozie WF variables
    $oozieWFPath="$storageUri/tutorials/useoozie"  # The default name is workflow.xml. And you don't need to specify the file name.
    $waitTimeBetweenOozieJobStatusCheck=10

    #Hive action variables
    $hiveScript = "$storageUri/tutorials/useoozie/useooziewf.hql"
    $hiveTableName = "log4jlogs"
    $hiveDataFolder = "$storageUri/tutorials/useoozie/data"
    $hiveOutputFolder = "$storageUri/tutorials/useoozie/output"

    #Sqoop action variables
    $sqlDatabaseConnectionString = "jdbc:sqlserver://$sqlDatabaseServerName.database.windows.net;user=$sqlDatabaseLogin@$sqlDatabaseServerName;password=$sqlDatabasePassword;database=$sqlDatabaseName"

    $OoziePayload =  @"
    <?xml version="1.0" encoding="UTF-8"?>
    <configuration>

    <property>
        <name>nameNode</name>
        <value>$storageUrI</value>
    </property>

    <property>
        <name>jobTracker</name>
        <value>jobtrackerhost:9010</value>
    </property>

    <property>
        <name>queueName</name>
        <value>default</value>
    </property>

    <property>
        <name>oozie.use.system.libpath</name>
        <value>true</value>
    </property>

    <property>
        <name>hiveScript</name>
        <value>$hiveScript</value>
    </property>

    <property>
        <name>hiveTableName</name>
        <value>$hiveTableName</value>
    </property>

    <property>
        <name>hiveDataFolder</name>
        <value>$hiveDataFolder</value>
    </property>

    <property>
        <name>hiveOutputFolder</name>
        <value>$hiveOutputFolder</value>
    </property>

    <property>
        <name>sqlDatabaseConnectionString</name>
        <value>&quot;$sqlDatabaseConnectionString&quot;</value>
    </property>

    <property>
        <name>sqlDatabaseTableName</name>
        <value>$SQLDatabaseTableName</value>
    </property>

    <property>
        <name>user.name</name>
        <value>$httpUserName</value>
    </property>

    <property>
        <name>oozie.wf.application.path</name>
        <value>$oozieWFPath</value>
    </property>

    </configuration>
    "@

    Write-Host "Checking Oozie server status..." -ForegroundColor Green
    $clusterUriStatus = "https://$hdinsightClusterName.azurehdinsight.net:443/oozie/v2/admin/status"
    $response = Invoke-RestMethod -Method Get -Uri $clusterUriStatus -Credential $httpCredential -OutVariable $OozieServerStatus

    $jsonResponse = ConvertFrom-Json (ConvertTo-Json -InputObject $response)
    $oozieServerSatus = $jsonResponse[0].("systemMode")
    Write-Host "Oozie server status is $oozieServerSatus."

    # create Oozie job
    Write-Host "Sending the following Payload to the cluster:" -ForegroundColor Green
    Write-Host "`n--------`n$OoziePayload`n--------"
    $clusterUriCreateJob = "https://$hdinsightClusterName.azurehdinsight.net:443/oozie/v2/jobs"
    $response = Invoke-RestMethod -Method Post -Uri $clusterUriCreateJob -Credential $httpCredential -Body $OoziePayload -ContentType "application/xml" -OutVariable $OozieJobName #-debug

    $jsonResponse = ConvertFrom-Json (ConvertTo-Json -InputObject $response)
    $oozieJobId = $jsonResponse[0].("id")
    Write-Host "Oozie job id is $oozieJobId..."

    # start Oozie job
    Write-Host "Starting the Oozie job $oozieJobId..." -ForegroundColor Green
    $clusterUriStartJob = "https://$hdinsightClusterName.azurehdinsight.net:443/oozie/v2/job/" + $oozieJobId + "?action=start"
    $response = Invoke-RestMethod -Method Put -Uri $clusterUriStartJob -Credential $httpCredential | Format-Table -HideTableHeaders #-debug

    # get job status
    Write-Host "Sleeping for $waitTimeBetweenOozieJobStatusCheck seconds until the job metadata is populated in the Oozie metastore..." -ForegroundColor Green
    Start-Sleep -Seconds $waitTimeBetweenOozieJobStatusCheck

    Write-Host "Getting job status and waiting for the job to complete..." -ForegroundColor Green
    $clusterUriGetJobStatus = "https://$hdinsightClusterName.azurehdinsight.net:443/oozie/v2/job/" + $oozieJobId + "?show=info"
    $response = Invoke-RestMethod -Method Get -Uri $clusterUriGetJobStatus -Credential $httpCredential
    $jsonResponse = ConvertFrom-Json (ConvertTo-Json -InputObject $response)
    $JobStatus = $jsonResponse[0].("status")

    while($JobStatus -notmatch "SUCCEEDED|KILLED")
    {
        Write-Host "$(Get-Date -format 'G'): $oozieJobId is in $JobStatus state...waiting $waitTimeBetweenOozieJobStatusCheck seconds for the job to complete..."
        Start-Sleep -Seconds $waitTimeBetweenOozieJobStatusCheck
        $response = Invoke-RestMethod -Method Get -Uri $clusterUriGetJobStatus -Credential $httpCredential
        $jsonResponse = ConvertFrom-Json (ConvertTo-Json -InputObject $response)
        $JobStatus = $jsonResponse[0].("status")
        $jobStatus
    }

    Write-Host "$(Get-Date -format 'G'): $oozieJobId is in $JobStatus state!" -ForegroundColor Green

    #endregion


**チュートリアルを再実行するには**

ワークフローを再実行するには、以下のものを削除する必要があります。

* Hive スクリプトの出力ファイル
* log4jLogsCount テーブル内のデータ

使用できる PowerShell スクリプトのサンプルを次に示します。

    $resourceGroupName = "<AzureResourceGroupName>"

    $defaultStorageAccountName = "<AzureStorageAccountName>"
    $defaultBlobContainerName = "<ContainerName>"

    #SQL database variables
    $sqlDatabaseServerName = "<SQLDatabaseServerName>"
    $sqlDatabaseLogin = "<SQLDatabaseLoginName>"
    $sqlDatabasePassword = "<SQLDatabaseLoginPassword>"
    $sqlDatabaseName = "<SQLDatabaseName>"
    $sqlDatabaseTableName = "log4jLogsCount"

    Write-host "Delete the Hive script output file ..." -ForegroundColor Green
    $defaultStorageAccountKey = (Get-AzureRmStorageAccountKey `
                                -ResourceGroupName $resourceGroupName `
                                -Name $defaultStorageAccountName)[0].Value
    $destContext = New-AzureStorageContext -StorageAccountName $defaultStorageAccountName -StorageAccountKey $defaultStorageAccountKey
    Remove-AzureStorageBlob -Context $destContext -Blob "tutorials/useoozie/output/000000_0" -Container $defaultBlobContainerName

    Write-host "Delete all the records from the log4jLogsCount table ..." -ForegroundColor Green
    $conn = New-Object System.Data.SqlClient.SqlConnection
    $conn.ConnectionString = "Data Source=$sqlDatabaseServerName.database.windows.net;Initial Catalog=$sqlDatabaseName;User ID=$sqlDatabaseLogin;Password=$sqlDatabasePassword;Encrypt=true;Trusted_Connection=false;"
    $conn.open()
    $cmd = New-Object System.Data.SqlClient.SqlCommand
    $cmd.connection = $conn
    $cmd.commandtext = "delete from $sqlDatabaseTableName"
    $cmd.executenonquery()

    $conn.close()

## <a name="next-steps"></a>次のステップ
このチュートリアルでは、Oozie ワークフローを定義する方法と、PowerShell を使用して Oozie ジョブを実行する方法について説明しました。 詳細については、次の記事を参照してください。

* [HDInsight での時間ベースの Oozie コーディネーターの使用][hdinsight-oozie-coordinator-time]
* [HDInsight で Hive と Hadoop を使用し、モバイル ハンドセットの使用状況を分析する][hdinsight-get-started]
* [HDInsight での Azure Blob Storage の使用][hdinsight-storage]
* [PowerShell を使用した HDInsight の管理][hdinsight-admin-powershell]
* [HDInsight での Hadoop ジョブ用データのアップロード][hdinsight-upload-data]
* [HDInsight の Hadoop での Sqoop の使用][hdinsight-use-sqoop]
* [HDInsight での Hive と Hadoop の使用][hdinsight-use-hive]
* [HDInsight での Pig と Hadoop の使用][hdinsight-use-pig]
* [HDInsight 用 Java MapReduce プログラムの開発][hdinsight-develop-mapreduce]

[hdinsight-cmdlets-download]: http://go.microsoft.com/fwlink/?LinkID=325563



[azure-data-factory-pig-hive]: ../data-factory/data-factory-data-transformation-activities.md
[hdinsight-oozie-coordinator-time]: hdinsight-use-oozie-coordinator-time.md
[hdinsight-versions]:  hdinsight-component-versioning.md
[hdinsight-storage]: hdinsight-hadoop-use-blob-storage.md
[hdinsight-get-started]: hdinsight-hadoop-linux-tutorial-get-started.md
[hdinsight-admin-portal]: hdinsight-administer-use-management-portal.md


[hdinsight-use-sqoop]: hdinsight-use-sqoop.md
[hdinsight-provision]: hdinsight-provision-clusters.md
[hdinsight-admin-powershell]: hdinsight-administer-use-powershell.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-use-mapreduce]: hdinsight-use-mapreduce.md
[hdinsight-use-hive]: hdinsight-use-hive.md
[hdinsight-use-pig]: hdinsight-use-pig.md
[hdinsight-storage]: hdinsight-hadoop-use-blob-storage.md

[hdinsight-develop-mapreduce]: hdinsight-develop-deploy-java-mapreduce-linux.md

[sqldatabase-create-configue]: ../sql-database-create-configure.md
[sqldatabase-get-started]: ../sql-database-get-started.md

[azure-management-portal]: https://portal.azure.com/
[azure-create-storageaccount]: ../storage-create-storage-account.md

[apache-hadoop]: http://hadoop.apache.org/
[apache-oozie-400]: http://oozie.apache.org/docs/4.0.0/
[apache-oozie-332]: http://oozie.apache.org/docs/3.3.2/

[powershell-download]: http://azure.microsoft.com/downloads/
[powershell-about-profiles]: http://go.microsoft.com/fwlink/?LinkID=113729
[powershell-install-configure]: /powershell/azureps-cmdlets-docs
[powershell-start]: http://technet.microsoft.com/library/hh847889.aspx
[powershell-script]: https://technet.microsoft.com/en-us/library/ee176961.aspx

[cindygross-hive-tables]: http://blogs.msdn.com/b/cindygross/archive/2013/02/06/hdinsight-hive-internal-and-external-tables-intro.aspx

[img-workflow-diagram]: ./media/hdinsight-use-oozie/HDI.UseOozie.Workflow.Diagram.png
[img-preparation-output]: ./media/hdinsight-use-oozie/HDI.UseOozie.Preparation.Output1.png  
[img-runworkflow-output]: ./media/hdinsight-use-oozie/HDI.UseOozie.RunWF.Output.png

[technetwiki-hive-error]: http://social.technet.microsoft.com/wiki/contents/articles/23047.hdinsight-hive-error-unable-to-rename.aspx

