---
title: "Azure HDInsight (Hadoop) での Apache Sqoop ジョブの実行 | Microsoft Docs"
description: "コンピューターから Azure PowerShell を使用して、Hadoop クラスターと Azure SQL データベース間で Sqoop インポートとエクスポートを実行する方法について説明します。"
editor: cgronlun
manager: jhubbard
services: hdinsight
documentationcenter: 
tags: azure-portal
author: mumian
ms.assetid: 2fdcc6b7-6ad5-4397-a30b-e7e389b66c7a
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
ms.sourcegitcommit: bb700c7de96712666bc4be1f8e430a2e94761f69
ms.openlocfilehash: 3f053d4c94d48630252f7c80fa8077c8ae5feb2d
ms.lasthandoff: 01/24/2017


---
# <a name="use-sqoop-with-hadoop-in-hdinsight"></a>HDInsight の Hadoop での Sqoop の使用
[!INCLUDE [sqoop-selector](../../includes/hdinsight-selector-use-sqoop.md)]

HDInsight で Sqoop を使用して、HDInsight クラスターと Azure SQL Database または SQL Server データベース間でインポートとエクスポートを実行する方法について説明します。

Hadoop はログやファイルなどの非構造化データおよび半構造化データを処理する場合に自然な選択ですが、リレーショナル データベースに格納された構造化データを処理する必要が生じることもあります。

[Sqoop][sqoop-user-guide-1.4.4] は、Hadoop クラスターとリレーショナル データベース間でデータを転送するためのツールです。 このツールを使用して、SQL Server、MySQL、Oracle などのリレーショナル データベース管理システム (RDBMS) から Hadoop 分散ファイル システム (HDFS) へデータをインポートしたり、MapReduce または Hive を使用して Hadoop のデータを変換してから、そのデータを RDBMS にエクスポートして戻したりできます。 このチュートリアルでは、リレーショナル データベースとして SQL Server データベースを使用します。

HDInsight クラスターでサポートされている Sqoop のバージョンについては、「[HDInsight で提供されるクラスター バージョンの新機能][hdinsight-versions]」を参照してください。

## <a name="understand-the-scenario"></a>シナリオの理解
HDInsight クラスターにはサンプル データがいくつか付属しています。 そのうちの以下の&2; つを使用します。

* log4j ログ ファイル ( */example/data/sample.log*)。 次のようなログがファイルから抽出されます。
  
        2012-02-03 18:35:34 SampleClass6 [INFO] everything normal for id 577725851
        2012-02-03 18:35:34 SampleClass4 [FATAL] system problem at id 1991281254
        2012-02-03 18:35:34 SampleClass3 [DEBUG] detail for id 1304807656
        ...
* *hivesampletable* という名前の Hive テーブル。*/hive/warehouse/hivesampletable* にあるデータ ファイルを参照しています。 テーブルはモバイル デバイスのデータを含んでいます。 
  
  | フィールド | データ型 |
  | --- | --- |
  | clientid |string |
  | querytime |string |
  | market |string |
  | deviceplatform |string |
  | devicemake |string |
  | devicemodel |string |
  | state |string |
  | country |string |
  | querydwelltime |double |
  | sessionid |bigint |
  | sessionpagevieworder |bigint |

まず、*sample.log* と *hivesampletable* を Azure SQL データベースまたは SQL Server にエクスポートしてから、次のパスを使用して、モバイル デバイスのデータを含むテーブルを HDInsight にインポートして戻します。

    /tutorials/usesqoop/importeddata

## <a name="create-cluster-and-sql-database"></a>クラスターと SQL Database を作成します。
ここでは、Azure Portal と Azure Resource Manager テンプレートを使用してチュートリアルを実行するために、クラスター、SQL Database、および SQL データベース スキーマを作成する方法を説明します。 テンプレートは [Azure クイック スタート テンプレート](https://azure.microsoft.com/resources/templates/101-hdinsight-linux-with-sql-database/) にあります。 Resource Manager テンプレートでは、SQL Database にテーブル スキーマをデプロイするために bacpac パッケージを呼び出します。  bacpac パッケージはパブリック BLOB コンテナー内にあります (https://hditutorialdata.blob.core.windows.net/usesqoop/SqoopTutorial-2016-2-23-11-2.bacpac)。 bacpac ファイルのプライベート コンテナーを使用する場合は、テンプレートで、次の値を使用します。
   
        "storageKeyType": "Primary",
        "storageKey": "<TheAzureStorageAccountKey>",

Azure PowerShell を使用してクラスターと SQL Database を作成する場合は、「[付録 A](#appendix-a---a-powershell-sample)」を参照してください。

1. 次の画像をクリックして、Azure Portal で Resource Manager テンプレートを開きます。         
   
    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-hdinsight-linux-with-sql-database%2Fazuredeploy.json" target="_blank"><img src="./media/hdinsight-use-sqoop/deploy-to-azure.png" alt="Deploy to Azure"></a>
   

2. 次のプロパティを入力します。

    - **サブスクリプション**： Azure のサブスクリプションを入力します。
    - **[リソース グループ]**： 新しい Azure リソース グループを作成するか、既存のリソース グループを選択します。  リソース グループは、管理目的で使用します。  これは、オブジェクトのコンテナーです。
    - **[場所]**: リージョンを選択します。
    - **ClusterName**: 作成する Hadoop クラスターの名前を入力します。
    - **クラスターのログイン名とパスワード**: 既定のログイン名は admin です。
    - **SSH のユーザー名とパスワード**。
    - **SQL Database サーバーのログイン名とパスワード**。
    - **_artifacts Location**: 別の場所で独自の bacpac ファイルを使用する場合を除き、既定値を使用します。
    - **_artifacts Location Sas Token**: 空白のままにします。
    - **Bacpac ファイル名**: 独自の bacpac ファイルを使用する場合を除き、既定値を使用します。
     
     次の値は、変数セクションにハードコードされています。
     
     | 既定のストレージ アカウント名 | <CluterName>store |
     | --- | --- |
     | Azure SQL データベース サーバー名 |<ClusterName>dbserver |
     | Azure SQL データベース名 |<ClusterName>db |
     
     これらの値を書き留めておいてください。  この情報は後で必要になります。

3. **[OK]** をクリックしてパラメーターを保存します。

4. **[カスタム デプロイ]** ブレードで **[リソース グループ]** ボックスをクリックし、**[新規]** をクリックして新しいリソース グループを作成します。 リソース グループとは、クラスター、依存するストレージ アカウント、その他のリンクされたリソースをグループ化しているコンテナーです。

5. **[法律条項]** をクリックし、**[作成]** をクリックします。

6. **[作成]** をクリックします。 "Submitting deployment for Template deployment" という新しいタイルが表示されます。 クラスターと SQL Database の作成には約 20 分かかります。

既存の Azure SQL Database または Microsoft SQL Server を使用する場合

* **Azure SQL データベース**: ワークステーションから Azure SQL データベース サーバーに対するアクセスを許可するようにファイアウォール ルールを構成する必要があります。 Azure SQL データベースを作成して、ファイアウォールを構成する手順については、「[Azure SQL データベースの概要][sqldatabase-get-started]」を参照してください。 
  
  > [!NOTE]
  > 既定では、Azure SQL データベースは Azure HDinsight などの Azure サービスからの接続を許可します。 このファイアウォール設定が無効になっている場合は、Azure ポータルから有効にする必要があります。 Azure SQL データベースの作成方法とファイアウォール ルールの構成方法については、「[SQL データベースの作成と構成][sqldatabase-create-configue]」を参照してください。
  > 
  > 
* **SQL Server**: HDInsight クラスターが SQL Server と同じ Azure の仮想ネットワーク上にある場合は、この記事の手順を使用して、SQL Server データベースとの間でデータをインポートおよびエクスポートできます。
  
  > [!NOTE]
  > HDInsight は場所ベースの仮想ネットワークのみをサポートし、アフィニティ グループ ベースの仮想ネットワークは現在扱っていません。
  > 
  > 
  
  * 仮想ネットワークの作成と構成については、「[Azure ポータルを使用した仮想ネットワークの作成](../virtual-network/virtual-networks-create-vnet-arm-pportal.md)」をご覧ください。
    
    * SQL Server をデータセンター内で使っている場合は、仮想ネットワークを*サイト間*または*ポイント対サイト*として構成する必要があります。
      
      > [!NOTE]
      > **ポイント対サイト**仮想ネットワークの場合、SQL Server が VPN クライアント構成アプリケーションを実行している必要があります。このアプリケーションは、Azure 仮想ネットワーク構成の**ダッシュボード**から入手できます。
      > 
      > 
    * SQL Server を Azure 仮想マシンで使用する際に、SQL Server をホストする仮想マシンが HDInsight と同じ仮想ネットワークに属している場合は、任意の仮想ネットワーク構成を使用できます。
  * 仮想ネットワークに HDInsight クラスターを作成する場合は、 [カスタム オプションを使用した HDInsight での Hadoop クラスターの作成](hdinsight-provision-clusters.md)
    
    > [!NOTE]
    > SQL Server では認証を許可する必要もあります。 この記事の手順を実行するには、SQL Server ログインを使用する必要があります。
    > 
    > 

## <a name="run-sqoop-jobs"></a>Sqoop ジョブの実行
HDInsight では、さまざまな方法を使用して Sqoop ジョブを実行できます。 次の表を使用して、適切な方法を判別してから、該当するチュートリアルのリンクをクリックしてください。

| **方法**  | **対話型** シェルの有無 | **バッチ** 処理の有無 | 使用する **クラスターのオペレーティング システム** | 使用元の **クライアントのオペレーティング システム** |
|:--- |:---:|:---:|:--- |:--- |
| [SSH](hdinsight-use-sqoop-mac-linux.md) |✔ |✔ |Linux |Linux、Unix、Mac OS X、または Windows |
| [.NET SDK for Hadoop](hdinsight-hadoop-use-sqoop-dotnet-sdk.md) |&nbsp; |✔ |Linux または Windows |Windows (現時点) |
| [Azure PowerShell](hdinsight-hadoop-use-sqoop-powershell.md) |&nbsp; |✔ |Linux または Windows |Windows |

## <a name="limitations"></a>制限事項
* 一括エクスポート - Linux ベースの HDInsight では、Microsoft SQL Server または Azure SQL Database にデータをエクスポートするために使用する Sqoop コネクタは、一括挿入を現在サポートしていません。
* バッチ処理 - Linux ベースの HDInsight で、挿入処理実行時に `-batch` スイッチを使用すると、Sqoop は挿入操作をバッチ処理するのではなく、複数の挿入処理を実行します。

## <a name="next-steps"></a>次のステップ
ここでは Sqoop の使用方法を説明しました。 詳細については、次を参照してください。

* [HDInsight での Hive の使用](hdinsight-use-hive.md)
* [HDInsight の Hadoop での Pig の使用](hdinsight-use-pig.md)
* [HDInsight での Oozie の使用][hdinsight-use-oozie]: Oozie ワークフローで Sqoop アクションを使用します。
* [HDInsight を使用したフライト遅延データの分析][hdinsight-analyze-flight-data]: Hive を使用してフライト遅延データを分析し、Sqoop を使用して Azure SQL Database にデータをエクスポートします。
* [HDInsight へのデータのアップロード][hdinsight-upload-data]: HDInsight/Azure Blob torage にデータをアップロードするその他の方法を説明します。

## <a name="appendix-a---a-powershell-sample"></a>付録 A - PowerShell サンプル
PowerShell のサンプルでは、次の手順を実行します。

1. Azure に接続します。
2. Azure リソース グループを作成します。 詳細については、 [Azure リソース マネージャーでの Windows PowerShell の使用](../powershell-azure-resource-manager.md)
3. Azure SQL Database サーバー、Azure SQL Database、および&2; つのテーブルを作成します。 
   
    SQL Server を使用する場合は、次のステートメントを使用してテーブルを作成します。
   
        CREATE TABLE [dbo].[log4jlogs](
         [t1] [nvarchar](50),
         [t2] [nvarchar](50),
         [t3] [nvarchar](50),
         [t4] [nvarchar](50),
         [t5] [nvarchar](50),
         [t6] [nvarchar](50),
         [t7] [nvarchar](50))
   
        CREATE TABLE [dbo].[mobiledata](
         [clientid] [nvarchar](50),
         [querytime] [nvarchar](50),
         [market] [nvarchar](50),
         [deviceplatform] [nvarchar](50),
         [devicemake] [nvarchar](50),
         [devicemodel] [nvarchar](50),
         [state] [nvarchar](50),
         [country] [nvarchar](50),
         [querydwelltime] [float],
         [sessionid] [bigint],
         [sessionpagevieworder][bigint])
   
    データベースとテーブルを確認する最も簡単な方法は、Visual Studio を使用することです。 データベース サーバーとデータベースは、Azure ポータルを使用して確認できます。
4. HDInsight クラスターを作成します。
   
    クラスターを確認するには、Azure ポータルまたは Azure PowerShell を使用します。
5. ソース データ ファイルを前処理します。
   
    このチュートリアルでは、log4j ログ ファイル (区切られたファイル) と Hive テーブルを Azure SQL データベースにエクスポートします。 区切られたファイルは */example/data/sample.log*と呼ばれます。 チュートリアルの前半で、log4j ログのサンプルをいくつか示しました。 ログ ファイルには空白行がいくつかあり、その他の行は次のような内容です。
   
        java.lang.Exception: 2012-02-03 20:11:35 SampleClass2 [FATAL] unrecoverable system problem at id 609774657
            at com.osa.mocklogger.MockLogger$2.run(MockLogger.java:83)
   
    これは、このデータを使用する他の例に適していますが、Azure SQL データベースまたは SQL Server にインポートする前にこれらの例外を削除する必要があります。 空の文字列がある場合、または Azure SQL データベース テーブルで定義されているフィールドの数よりも要素の数が少ない行がある場合、Sqoop エクスポートは失敗します。 log4jlogs テーブルには 7 個の文字列型のフィールドがあります。
   
    この手順で、クラスター上に新しいファイル (tutorials/usesqoop/data/sample.log) が作成されます。 変更されたデータ ファイルを確認する場合は、Azure ポータル、Azure Storage エクスプローラー ツール、または Azure PowerShell を使用できます。 「[Azure HDInsight の概要][hdinsight-get-started]」に、Azure PowerShell を使用してファイルをダウンロードし、ファイルの内容を表示するサンプル コードが示されています。
6. Azure SQL Database にデータ ファイルをエクスポートします。
   
    ソース ファイルは、tutorials/usesqoop/data/sample.log です。 データのエクスポート先のテーブルは log4jlogs です。
   
   > [!NOTE]
   > 接続文字列情報を除き、このセクションの手順は、Azure SQL データベースまたは SQL Server で動作します。 これらの手順は次の構成を使用してテスト済みです。
   > 
   > * **Azure Virtual Network ポイント対サイト構成**: HDInsight クラスターをプライベート データセンター内の SQL Server に接続する仮想ネットワーク。 詳細については、「 [管理ポータルでのポイント対サイト VPN の構成](../vpn-gateway/vpn-gateway-point-to-site-create.md) 」を参照してください。
   > * **Azure HDInsight 3.1**: 仮想ネットワークでのクラスターの作成については、 [カスタム オプションを使用した HDInsight での Hadoop クラスターの作成](hdinsight-provision-clusters.md) に関するページをご覧ください。
   > * **SQL Server 2014**: 仮想ネットワークに安全に接続するために、認証と、VPN クライアント構成パッケージの実行を許可する構成。
   > 
   > 
7. Azure SQL Database に Hive テーブルをエクスポートします。
8. HDInsight クラスターに mobiledata テーブルをインポートします。
   
    変更されたデータ ファイルを確認する場合は、Azure ポータル、Azure Storage エクスプローラー ツール、または Azure PowerShell を使用できます。  「[HDInsight の概要][hdinsight-get-started]」に、Azure PowerShell を使用してファイルをダウンロードし、ファイルの内容を表示するサンプル コードが示されています。

### <a name="the-powershell-sample"></a>PowerShell サンプル
    # Prepare an Azure SQL database to be used by the Sqoop tutorial

    #region - provide the following values

    $subscriptionID = "<Enter your Azure Subscription ID>"

    $sqlDatabaseLogin = "<Enter a SQL Database Login name>" #SQL Database server login
    $sqlDatabasePassword = "<Enter a Password>"

    $httpUserName = "admin"  #HDInsight cluster username
    $httpPassword = "<Enter a Password>"

    # used for creating Azure service names
    $nameToken = "<Enter an alias>" 
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

    # Used for creating tables and clustered indexes
    $cmdCreateLog4jTable = "CREATE TABLE [dbo].[log4jlogs](
        [t1] [nvarchar](50),
        [t2] [nvarchar](50),
        [t3] [nvarchar](50),
        [t4] [nvarchar](50),
        [t5] [nvarchar](50),
        [t6] [nvarchar](50),
        [t7] [nvarchar](50))"

    $cmdCreateLog4jClusteredIndex = "CREATE CLUSTERED INDEX log4jlogs_clustered_index on log4jlogs(t1)"

    $cmdCreateMobileTable = " CREATE TABLE [dbo].[mobiledata](
    [clientid] [nvarchar](50),
    [querytime] [nvarchar](50),
    [market] [nvarchar](50),
    [deviceplatform] [nvarchar](50),
    [devicemake] [nvarchar](50),
    [devicemodel] [nvarchar](50),
    [state] [nvarchar](50),
    [country] [nvarchar](50),
    [querydwelltime] [float],
    [sessionid] [bigint],
    [sessionpagevieworder][bigint])"

    $cmdCreateMobileDataClusteredIndex = "CREATE CLUSTERED INDEX mobiledata_clustered_index on mobiledata(clientid)"

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
    catch{Login-AzureRmAccount}
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
        $credential = New-Object System.Management.Automation.PSCredential($sqlDatabaseLogin,$sqlDatabasePW)

        $sqlDatabaseServerName = (New-AzureRmSqlServer `
                                    -ResourceGroupName $resourceGroupName `
                                    -ServerName $sqlDatabaseServerName `
                                    -SqlAdministratorCredentials $credential `
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
    Write-Host "`nCreating an Azure SQL database ..." -ForegroundColor Green

    try {
        Get-AzureRmSqlDatabase `
            -ResourceGroupName $resourceGroupName `
            -ServerName $sqlDatabaseServerName `
            -DatabaseName $sqlDatabaseName
    }
    catch {
        Write-Host "`nCreating SQL Database, $sqlDatabaseName ..."  -ForegroundColor Green
        New-AzureRMSqlDatabase `
            -ResourceGroupName $resourceGroupName `
            -ServerName $sqlDatabaseServerName `
            -DatabaseName $sqlDatabaseName `
            -Edition "Standard" `
            -RequestedServiceObjectiveName "S1"
    }

    #endregion

    #region - Create tables
    Write-Host "Creating the log4jlogs table and the mobiledata table ..." -ForegroundColor Green

    $conn = New-Object System.Data.SqlClient.SqlConnection
    $conn.ConnectionString = $sqlDatabaseConnectionString
    $conn.Open()

    # Create the log4jlogs table and index
    $cmd = New-Object System.Data.SqlClient.SqlCommand
    $cmd.Connection = $conn
    $cmd.CommandText = $cmdCreateLog4jTable
    $ret = $cmd.ExecuteNonQuery()
    $cmd.CommandText = $cmdCreateLog4jClusteredIndex
    $cmd.ExecuteNonQuery()

    # Create the mobiledata table and index
    $cmd.CommandText = $cmdCreateMobileTable
    $cmd.ExecuteNonQuery()
    $cmd.CommandText = $cmdCreateMobileDataClusteredIndex
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

    #region - pre-process the source file

    Write-Host "Preprocessing the source file ..." -ForegroundColor Green

    # This procedure creates a new file with $destBlobName
    $sourceBlobName = "example/data/sample.log"
    $destBlobName = "tutorials/usesqoop/data/sample.log"

    # Define the connection string
    $storageConnectionString = "DefaultEndpointsProtocol=https;AccountName=$defaultStorageAccountName;AccountKey=$defaultStorageAccountKey"

    # Create block blob objects referencing the source and destination blob.
    $storageAccount = [Microsoft.WindowsAzure.Storage.CloudStorageAccount]::Parse($storageConnectionString)
    $storageClient = $storageAccount.CreateCloudBlobClient();
    $storageContainer = $storageClient.GetContainerReference($defaultBlobContainerName)
    $sourceBlob = $storageContainer.GetBlockBlobReference($sourceBlobName)
    $destBlob = $storageContainer.GetBlockBlobReference($destBlobName)

    # Define a MemoryStream and a StreamReader for reading from the source file
    $stream = New-Object System.IO.MemoryStream
    $stream = $sourceBlob.OpenRead()
    $sReader = New-Object System.IO.StreamReader($stream)

    # Define a MemoryStream and a StreamWriter for writing into the destination file
    $memStream = New-Object System.IO.MemoryStream
    $writeStream = New-Object System.IO.StreamWriter $memStream

    # Pre-process the source blob
    $exString = "java.lang.Exception:"
    while(-Not $sReader.EndOfStream){
        $line = $sReader.ReadLine()
        $split = $line.Split(" ")

        # remove the "java.lang.Exception" from the first element of the array
        # for example: java.lang.Exception: 2012-02-03 19:11:02 SampleClass8 [WARN] problem finding id 153454612
        if ($split[0] -eq $exString){
            #create a new ArrayList to remove $split[0]
            $newArray = [System.Collections.ArrayList] $split
            $newArray.Remove($exString)

            # update $split and $line
            $split = $newArray
            $line = $newArray -join(" ")
        }

        # remove the lines that has less than 7 elements
        if ($split.count -ge 7){
            write-host $line
            $writeStream.WriteLine($line)
        }
    }

    # Write to the destination blob
    $writeStream.Flush()
    $memStream.Seek(0, "Begin")
    $destBlob.UploadFromStream($memStream)

    #endregion

    #region - export a log file from the cluster to the SQL database

    Write-Host "Preprocessing the source file ..." -ForegroundColor Green

    $tableName_log4j = "log4jlogs"

    # Connection string for Azure SQL Database.
    # Comment if using SQL Server
    $connectionString = "jdbc:sqlserver://$sqlDatabaseServerName.database.windows.net;user=$sqlDatabaseLogin@$sqlDatabaseServerName;password=$sqlDatabasePassword;database=$sqlDatabaseName"
    # Connection string for SQL Server.
    # Uncomment if using SQL Server.
    #$connectionString = "jdbc:sqlserver://$sqlDatabaseServerName;user=$sqlDatabaseLogin;password=$sqlDatabasePassword;database=$sqlDatabaseName"

    $exportDir_log4j = "/tutorials/usesqoop/data"

    # Submit a Sqoop job
    $sqoopDef = New-AzureRmHDInsightSqoopJobDefinition `
        -Command "export --connect $connectionString --table $tableName_log4j --export-dir $exportDir_log4j --input-fields-terminated-by \0x20 -m 1"
    $sqoopJob = Start-AzureRmHDInsightJob `
                    -ClusterName $hdinsightClusterName `
                    -HttpCredential $httpCredential `
                    -JobDefinition $sqoopDef #-Debug -Verbose
    Wait-AzureRmHDInsightJob `
        -ResourceGroupName $resourceGroupName `
        -ClusterName $hdinsightClusterName `
        -HttpCredential $httpCredential `
        -JobId $sqoopJob.JobId

    Write-Host "Standard Error" -BackgroundColor Green
    Get-AzureRmHDInsightJobOutput -ResourceGroupName $resourceGroupName -ClusterName $hdinsightClusterName -DefaultStorageAccountName $defaultStorageAccountName -DefaultStorageAccountKey $defaultStorageAccountKey -DefaultContainer $defaultBlobContainerName -HttpCredential $httpCredential -JobId $sqoopJob.JobId -DisplayOutputType StandardError
    Write-Host "Standard Output" -BackgroundColor Green
    Get-AzureRmHDInsightJobOutput -ResourceGroupName $resourceGroupName -ClusterName $hdinsightClusterName -DefaultStorageAccountName $defaultStorageAccountName -DefaultStorageAccountKey $defaultStorageAccountKey -DefaultContainer $defaultBlobContainerName -HttpCredential $httpCredential -JobId $sqoopJob.JobId -DisplayOutputType StandardOutput

    #endregion

    #region - export a Hive table

    $tableName_mobile = "mobiledata"
    $exportDir_mobile = "/hive/warehouse/hivesampletable"

    $sqoopDef = New-AzureRmHDInsightSqoopJobDefinition `
        -Command "export --connect $connectionString --table $tableName_mobile --export-dir $exportDir_mobile --fields-terminated-by \t -m 1"
    $sqoopJob = Start-AzureRmHDInsightJob `
                    -ClusterName $hdinsightClusterName `
                    -HttpCredential $httpCredential `
                    -JobDefinition $sqoopDef #-Debug -Verbose

    Wait-AzureRmHDInsightJob `
        -ResourceGroupName $resourceGroupName `
        -ClusterName $hdinsightClusterName `
        -HttpCredential $httpCredential `
        -JobId $sqoopJob.JobId

    Write-Host "Standard Error" -BackgroundColor Green
    Get-AzureRmHDInsightJobOutput `
        -ResourceGroupName $resourceGroupName `
        -ClusterName $hdinsightClusterName `
        -DefaultStorageAccountName $defaultStorageAccountName `
        -DefaultStorageAccountKey $defaultStorageAccountKey `
        -DefaultContainer $defaultBlobContainerName `
        -HttpCredential $httpCredential `
        -JobId $sqoopJob.JobId `
        -DisplayOutputType StandardError

    Write-Host "Standard Output" -BackgroundColor Green
    Get-AzureRmHDInsightJobOutput `
        -ResourceGroupName $resourceGroupName `
        -ClusterName $hdinsightClusterName `
        -DefaultStorageAccountName $defaultStorageAccountName `
        -DefaultStorageAccountKey $defaultStorageAccountKey `
        -DefaultContainer $defaultBlobContainerName `
        -HttpCredential $httpCredential `
        -JobId $sqoopJob.JobId `
        -DisplayOutputType StandardOutput

    #endregion

    #region - import a database

    $targetDir_mobile = "/tutorials/usesqoop/importeddata/"

    $sqoopDef = New-AzureRmHDInsightSqoopJobDefinition `
        -Command "import --connect $connectionString --table $tableName_mobile --target-dir $targetDir_mobile --fields-terminated-by \t --lines-terminated-by \n -m 1"

    $sqoopJob = Start-AzureRmHDInsightJob `
                    -ClusterName $hdinsightClusterName `
                    -HttpCredential $httpCredential `
                    -JobDefinition $sqoopDef #-Debug -Verbose

    Wait-AzureRmHDInsightJob `
        -ResourceGroupName $resourceGroupName `
        -ClusterName $hdinsightClusterName `
        -HttpCredential $httpCredential `
        -JobId $sqoopJob.JobId

    Write-Host "Standard Error" -BackgroundColor Green
    Get-AzureRmHDInsightJobOutput `
        -ResourceGroupName $resourceGroupName `
        -ClusterName $hdinsightClusterName `
        -DefaultStorageAccountName $defaultStorageAccountName `
        -DefaultStorageAccountKey $defaultStorageAccountKey `
        -DefaultContainer $defaultBlobContainerName `
        -HttpCredential $httpCredential `
        -JobId $sqoopJob.JobId `
        -DisplayOutputType StandardError

    Write-Host "Standard Output" -BackgroundColor Green
    Get-AzureRmHDInsightJobOutput `
        -ResourceGroupName $resourceGroupName `
        -ClusterName $hdinsightClusterName `
        -DefaultStorageAccountName $defaultStorageAccountName `
        -DefaultStorageAccountKey $defaultStorageAccountKey `
        -DefaultContainer $defaultBlobContainerName `
        -HttpCredential $httpCredential `
        -JobId $sqoopJob.JobId `
        -DisplayOutputType StandardOutput

    #endregion



[azure-management-portal]: https://portal.azure.com/

[hdinsight-versions]:  hdinsight-component-versioning.md
[hdinsight-provision]: hdinsight-provision-clusters.md
[hdinsight-get-started]: hdinsight-hadoop-linux-tutorial-get-started.md
[hdinsight-storage]: ../hdinsight-hadoop-use-blob-storage.md
[hdinsight-analyze-flight-data]: hdinsight-analyze-flight-delay-data.md
[hdinsight-use-oozie]: hdinsight-use-oozie.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-submit-jobs]: hdinsight-submit-hadoop-jobs-programmatically.md

[sqldatabase-get-started]: ../sql-database/sql-database-get-started.md
[sqldatabase-create-configue]: ../sql-database/sql-database-get-started.md

[powershell-start]: http://technet.microsoft.com/library/hh847889.aspx
[powershell-install]: /powershell/azureps-cmdlets-docs
[powershell-script]: http://technet.microsoft.com/library/ee176949.aspx

[sqoop-user-guide-1.4.4]: https://sqoop.apache.org/docs/1.4.4/SqoopUserGuide.html

