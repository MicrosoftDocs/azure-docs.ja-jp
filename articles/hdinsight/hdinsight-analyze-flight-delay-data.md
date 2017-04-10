---
title: "HDInsight の Hadoop を使用したフライトの遅延データの分析 | Microsoft Docs"
description: "1 つの Windows PowerShell スクリプトを使用して、HDInsight クラスターの作成、Hive ジョブの実行、Sqoop ジョブの実行、クラスターの削除を行う方法について説明します。"
services: hdinsight
documentationcenter: 
author: mumian
manager: jhubbard
editor: cgronlun
ms.assetid: 00e26aa9-82fb-4dbe-b87d-ffe8e39a5412
ms.service: hdinsight
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/06/2017
ms.author: jgao
ROBOTS: NOINDEX
translationtype: Human Translation
ms.sourcegitcommit: afe143848fae473d08dd33a3df4ab4ed92b731fa
ms.openlocfilehash: 05e8c264376b976bcdc8f3e1f03ed79cb712991e
ms.lasthandoff: 03/17/2017


---
# <a name="analyze-flight-delay-data-by-using-hive-in-hdinsight"></a>HDInsight での Hive を使用したフライト遅延データの分析
Hive では、*[HiveQL][hadoop-hiveql]* と呼ばれる SQL に似たスクリプト言語を使用して Hadoop MapReduce ジョブを実行します。大規模なデータの集約、照会、分析に Hive を利用できます。

> [!IMPORTANT]
> このドキュメントの手順では、Windows ベースの HDInsight クラスターが必要です。 Linux は、バージョン 3.4 以上の HDInsight で使用できる唯一のオペレーティング システムです。 詳細については、[Window での HDInsight の廃止](hdinsight-component-versioning.md#hdi-version-32-and-33-nearing-deprecation-date)に関する記事を参照してください。 Linux ベースのクラスターでの手順については、「 [HDInsight での Hive を使用したフライト遅延データの分析 (Linux)](hdinsight-analyze-flight-delay-data-linux.md)」を参照してください。

Azure HDInsight の大きな利点の&1; つに、データ ストレージとコンピューティングの分離があります。 HDInsight は、データ ストレージとして Azure BLOB ストレージを使用します。 標準的なジョブは&3; つの部分で構成されます。

1. **Azure BLOB ストレージにデータを保存する。**  たとえば、気象データ、センサー データ、Web ログを Azure BLOB ストレージに保存できます。ここではフライトの遅延データが保存対象となります。
2. **ジョブを実行する。** データを処理する段階になったら、Windows PowerShell スクリプト (またはクライアント アプリケーション) を実行して HDInsight クラスターを作成し、ジョブを実行して、クラスターを削除します。 このジョブによって、出力データが Azure BLOB ストレージに保存されます。 出力データは、クラスターの削除後も維持されます。 こうして、実際に消費した分だけが課金されることとなります。
3. **Azure BLOB ストレージから出力結果を取り出す。**ここでは、Azure SQL データベースにデータをエクスポートする過程がこれに相当します。

このチュートリアルのシナリオと構成を示したのが次の図です。

![HDI.FlightDelays.flow][img-hdi-flightdelays-flow]

図中の番号は、セクション タイトルに対応します。 **M** はメイン (main) プロセスを表します。 **A** は付録 (appendix) の内容を表します。

このチュートリアルでは、主に&1; つの Windows PowerShell スクリプトを使用して次のタスクを実行する方法を示します。

* HDInsight クラスターを作成します。
* クラスターで Hive ジョブを実行し、空港での平均遅延時間を計算する。 フライトの遅延データは Azure BLOB ストレージ アカウントに保存されます。
* Sqoop ジョブを実行し、Hive ジョブの出力結果を Azure SQL Database にエクスポートする。
* HDInsight クラスターを削除する。

フライト遅延データのアップロード手順、Hive クエリ文字列の作成とアップロード手順、および Sqoop ジョブのための Azure SQL Database の準備手順については、付録を参照してください。

> [!NOTE]
> このドキュメントの手順は、Windows ベースの HDInsight クラスターに固有のものです。 Linux ベースのクラスターでの手順については、「[HDInsight での Hive を使用したフライト遅延データの分析 (Linux)](hdinsight-analyze-flight-delay-data-linux.md)」を参照してください。

### <a name="prerequisites"></a>前提条件
このチュートリアルを開始する前に、次の項目を用意する必要があります。

* **Azure サブスクリプション**。 [Azure 無料試用版の取得](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/)に関するページを参照してください。
* **Azure PowerShell を実行できるワークステーション**。

    > [!IMPORTANT]
    > Azure サービス マネージャーを使用した HDInsight リソースの管理に関する Azure PowerShell のサポートは、2017 年 1 月 1 日までに**廃止**される予定です。 このドキュメントの手順では、Azure Resource Manager で機能する新しい HDInsight コマンドレットを使用します。
    >
    > [Azure PowerShell のインストールと構成](/powershell/azureps-cmdlets-docs) に関するページの手順に従い、Azure PowerShell の最新バージョンをインストールしてください。 Azure Resource Manager で機能する新しいコマンドレットを使用するようにスクリプトを変更する必要がある場合、詳細については、「 [Migrating to Azure Resource Manager-based development tools for HDInsight clusters (HDInsight クラスターの Azure Resource Manager ベースの開発ツールへの移行)](hdinsight-hadoop-development-using-azure-resource-manager.md) 」をご覧ください。

**このチュートリアルで使用するファイル**

このチュートリアルでは、[米国運輸省研究・革新技術庁/運輸統計局][rita-website]が公開している航空便の定時運航データをダウンロードして使用します。
データのコピーは、パブリック BLOB アクセス権限が設定された Azure BLOB ストレージ コンテナーにアップロード済みです。
PowerShell スクリプトの一部は、パブリック BLOB コンテナーからクラスターの既定の BLOB コンテナーにデータをコピーします。 HiveQL スクリプトも同じ BLOB コンテナーにコピーされます。
独自のストレージ アカウントにデータを取得/アップロードする方法や、HiveQL スクリプト ファイルを作成/アップロードする方法については、[付録 A](#appendix-a) および[付録 B](#appendix-b) を参照してください。

このチュートリアルで使用するファイルを次の表に示します。

<table border="1">
<tr><th>ファイル</th><th>説明</th></tr>
<tr><td>wasbs://flightdelay@hditutorialdata.blob.core.windows.net/flightdelays.hql</td><td>Hive ジョブで使用する HiveQL スクリプト ファイル。 このスクリプトは、パブリック アクセス権限の設定された Azure BLOB ストレージ アカウントにアップロード済みです。 このファイルの準備と Azure BLOB ストレージ アカウントへのアップロードの手順については、<a href="#appendix-b">付録 B</a> を参照してください。</td></tr>
<tr><td>wasbs://flightdelay@hditutorialdata.blob.core.windows.net/2013Data</td><td>Hive ジョブの入力データ。 このデータは、パブリック アクセス権限の設定された Azure BLOB ストレージ アカウントにアップロード済みです。 このデータの取得と Azure BLOB ストレージ アカウントへのアップロードの手順については、<a href="#appendix-a">付録 A</a> を参照してください。</td></tr>
<tr><td>\tutorials\flightdelays\output</td><td>Hive ジョブの出力パス。 出力データの保存には、既定のコンテナーを使用します。</td></tr>
<tr><td>\tutorials\flightdelays\jobstatus</td><td>既定のコンテナーにある Hive ジョブのステータス フォルダー。</td></tr>
</table>

## <a name="create-cluster-and-run-hivesqoop-jobs"></a>クラスターを作成して Hive/Sqoop ジョブを実行する
Hadoop MapReduce はバッチ処理です。 Hive ジョブを実行する最もコスト効率の良い方法は、ジョブに使用するクラスターを作成し、ジョブの完了後、ジョブを削除することです。 以下のスクリプトは、その全過程をカバーしています。
HDInsight クラスターの作成と Hive ジョブの実行の詳細については、[HDInsight での Hadoop クラスターの作成][hdinsight-provision]に関するページ、および [HDInsight での Hive の使用][hdinsight-use-hive]に関するページをご覧ください。

**Azure PowerShell で Hive クエリを実行するには**

1. [付録 C](#appendix-c)の手順に従い、Azure SQL データベースと Sqoop ジョブ出力用のテーブルを作成します。
2. Windows PowerShell ISE を開き、次のスクリプトを実行します。

    ```powershell
    $subscriptionID = "<Azure Subscription ID>"
    $nameToken = "<Enter an Alias>"

    ###########################################
    # You must configure the follwing variables
    # for an existing Azure SQL Database
    ###########################################
    $existingSqlDatabaseServerName = "<Azure SQL Database Server>"
    $existingSqlDatabaseLogin = "<Azure SQL Database Server Login>"
    $existingSqlDatabasePassword = "<Azure SQL Database Server login password>"
    $existingSqlDatabaseName = "<Azure SQL Database name>"

    $localFolder = "E:\Tutorials\Downloads\" # A temp location for copying files.
    $azcopyPath = "C:\Program Files (x86)\Microsoft SDKs\Azure\AzCopy" # depends on the version, the folder can be different

    ###########################################
    # (Optional) configure the following variables
    ###########################################

    $namePrefix = $nameToken.ToLower() + (Get-Date -Format "MMdd")

    $resourceGroupName = $namePrefix + "rg"
    $location = "EAST US 2"

    $HDInsightClusterName = $namePrefix + "hdi"
    $httpUserName = "admin"
    $httpPassword = "<Enter the Password>"

    $defaultStorageAccountName = $namePrefix + "store"
    $defaultBlobContainerName = $HDInsightClusterName # use the cluster name

    $existingSqlDatabaseTableName = "AvgDelays"
    $sqlDatabaseConnectionString = "jdbc:sqlserver://$existingSqlDatabaseServerName.database.windows.net;user=$existingSqlDatabaseLogin@$existingSqlDatabaseServerName;password=$existingSqlDatabaseLogin;database=$existingSqlDatabaseName"

    $hqlScriptFile = "/tutorials/flightdelays/flightdelays.hql"

    $jobStatusFolder = "/tutorials/flightdelays/jobstatus"

    ###########################################
    # Login
    ###########################################
    try{
        $acct = Get-AzureRmSubscription
    }
    catch{
        Login-AzureRmAccount
    }
    Select-AzureRmSubscription -SubscriptionID $subscriptionID

    ###########################################
    # Create a new HDInsight cluster
    ###########################################

    # Create ARM group
    New-AzureRmResourceGroup -Name $resourceGroupName -Location $location

    # Create the default storage account
    New-AzureRmStorageAccount -ResourceGroupName $resourceGroupName -Name $defaultStorageAccountName -Location $location -Type Standard_LRS

    # Create the default Blob container
    $defaultStorageAccountKey = (Get-AzureRmStorageAccountKey -ResourceGroupName $resourceGroupName -Name $defaultStorageAccountName)[0].Value
    $defaultStorageAccountContext = New-AzureStorageContext -StorageAccountName $defaultStorageAccountName -StorageAccountKey $defaultStorageAccountKey
    New-AzureStorageContainer -Name $defaultBlobContainerName -Context $defaultStorageAccountContext

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
        -DefaultStorageContainer $existingDefaultBlobContainerName

    ###########################################
    # Prepare the HiveQL script and source data
    ###########################################

    # Create the temp location
    New-Item -Path $localFolder -ItemType Directory -Force

    # Download the sample file from Azure Blob storage
    $context = New-AzureStorageContext -StorageAccountName "hditutorialdata" -Anonymous
    $blobs = Get-AzureStorageBlob -Container "flightdelay" -Context $context
    #$blobs | Get-AzureStorageBlobContent -Context $context -Destination $localFolder

    # Upload data to default container

    $azcopycmd = "cmd.exe /C '$azcopyPath\azcopy.exe' /S /Source:'$localFolder' /Dest:'https://$defaultStorageAccountName.blob.core.windows.net/$defaultBlobContainerName/tutorials/flightdelays' /DestKey:$defaultStorageAccountKey"

    Invoke-Expression -Command:$azcopycmd

    ###########################################
    # Submit the Hive job
    ###########################################
    Use-AzureRmHDInsightCluster -ClusterName $HDInsightClusterName -HttpCredential $httpCredential
    $response = Invoke-AzureRmHDInsightHiveJob `
                    -Files $hqlScriptFile `
                    -DefaultContainer $defaultBlobContainerName `
                    -DefaultStorageAccountName $defaultStorageAccountName `
                    -DefaultStorageAccountKey $defaultStorageAccountKey `
                    -StatusFolder $jobStatusFolder

    write-Host $response

    ###########################################
    # Submit the Sqoop job
    ###########################################
    $exportDir = "wasbs://$defaultBlobContainerName@$defaultStorageAccountName.blob.core.windows.net/tutorials/flightdelays/output"

    $sqoopDef = New-AzureRmHDInsightSqoopJobDefinition `
                    -Command "export --connect $sqlDatabaseConnectionString --table $sqlDatabaseTableName --export-dir $exportDir --fields-terminated-by \001 "
    $sqoopJob = Start-AzureRmHDInsightJob `
                    -ResourceGroupName $resourceGroupName `
                    -ClusterName $hdinsightClusterName `
                    -HttpCredential $httpCredential `
                    -JobDefinition $sqoopDef #-Debug -Verbose

    Wait-AzureRmHDInsightJob `
        -ResourceGroupName $resourceGroupName `
        -ClusterName $HDInsightClusterName `
        -HttpCredential $httpCredential `
        -WaitTimeoutInSeconds 3600 `
        -Job $sqoopJob.JobId

    Get-AzureRmHDInsightJobOutput `
        -ResourceGroupName $resourceGroupName `
        -ClusterName $hdinsightClusterName `
        -HttpCredential $httpCredential `
        -DefaultContainer $existingDefaultBlobContainerName `
        -DefaultStorageAccountName $defaultStorageAccountName `
        -DefaultStorageAccountKey $defaultStorageAccountKey `
        -JobId $sqoopJob.JobId `
        -DisplayOutputType StandardError

    ###########################################
    # Delete the cluster
    ###########################################
    Remove-AzureRmHDInsightCluster -ResourceGroupName $resourceGroupName -ClusterName $hdinsightClusterName
    ```
3. SQL Database に接続し、AvgDelays テーブルに保存されている都市別平均遅延を表示します。

    ![HDI.FlightDelays.AvgDelays.Dataset][image-hdi-flightdelays-avgdelays-dataset]

- - -

## <a id="appendix-a"></a>付録 A: フライト遅延データを Azure BLOB ストレージにアップロードする
データ ファイルと HiveQL スクリプト ファイルをアップロード ( [付録 B](#appendix-b)参照) する前に、いくつか計画を立てる必要があります。 データ ファイルと HiveQL ファイルを保存してから HDInsight クラスターを作成し、Hive ジョブを実行するという考え方です。 2 つのオプションがあります。

* **HDInsight クラスターと既定のファイル システムで、使用する Azure ストレージ アカウントを同じものにする。** HDInsight クラスターにはストレージ アカウントのアクセス キーがあるため、追加の変更は不要です。
* **HDInsight クラスターと既定のファイル システムで、使用する Azure ストレージ アカウントを別のものにする。** この場合、ストレージ アカウントを追加のストレージ アカウントとしてリンクするために、「 [HDInsight クラスターを作成して Hive/Sqoop ジョブを実行する](#runjob) 」に記載されている Windows PowerShell スクリプトの作成に関する部分を変更する必要があります。 手順については、[HDInsight での Hadoop クラスターの作成][hdinsight-provision]に関するページをご覧ください。 ストレージ アカウントのアクセス キーが HDInsight クラスターで認識されるようになります。

> [!NOTE]
> データ ファイルの BLOB ストレージ パスは、HiveQL スクリプト ファイルでハードコーディングされます。 このパスは適宜更新する必要があります。

**フライト データをダウンロードするには**

1. [米国運輸省研究・革新技術庁/運輸統計局][rita-website]のページに移動します。
2. このページで、次の値を選択します。

    <table border="1">
    <tr><th>名前</th><th>値</th></tr>
    <tr><td>Filter Year</td><td>2013 </td></tr>
    <tr><td>Filter Period</td><td>January</td></tr>
    <tr><td>フィールド</td><td>*Year*、*FlightDate*、*UniqueCarrier*、*Carrier*、*FlightNum*、*OriginAirportID*、*Origin*、*OriginCityName*、*OriginState*、*DestAirportID*、*Dest*、*DestCityName*、*DestState*、*DepDelayMinutes*、*ArrDelay*、*ArrDelayMinutes*、*CarrierDelay*、*WeatherDelay*、*NASDelay*、*SecurityDelay*、*LateAircraftDelay* (その他のフィールドはすべてオフにする)</td></tr>
    </table>
3. **[Download]**をクリックします。
4. ファイルを **C:\Tutorials\FlightDelay\2013Data** フォルダーに解凍します。 ファイルはそれぞれ CSV ファイルで、サイズは約 60 GB です。
5. ファイルの名前を、データを含む月の名前に変更します。 たとえば、1 月のデータを含むファイルの場合、 *January.csv*という名前にします。
6. 手順 2. ～ 5. を繰り返して、2013 年の 12 か月分のファイルをダウンロードします。 チュートリアルを実行するには、月別のファイルが少なくとも&1; つ必要です。

**フライト遅延データを Azure BLOB ストレージにアップロードするには**

1. 次のパラメーターを準備します。

    <table border="1">
    <tr><th>変数名</th><th>メモ</th></tr>
    <tr><td>$storageAccountName</td><td>データのアップロード先となる Azure ストレージ アカウント。</td></tr>
    <tr><td>$blobContainerName</td><td>データのアップロード先となる BLOB コンテナー。</td></tr>
    </table>
2. Azure PowerShell ISE を開きます。
3. 次のスクリプトをスクリプト ウィンドウに貼り付けます。

    ```powershell
    [CmdletBinding()]
    Param(

        [Parameter(Mandatory=$True,
                    HelpMessage="Enter the Azure storage account name for creating a new HDInsight cluster. If the account doesn't exist, the script will create one.")]
        [String]$storageAccountName,

        [Parameter(Mandatory=$True,
                    HelpMessage="Enter the Azure blob container name for creating a new HDInsight cluster. If not specified, the HDInsight cluster name will be used.")]
        [String]$blobContainerName
    )

    #Region - Variables
    $localFolder = "C:\Tutorials\FlightDelay\2013Data"  # The source folder
    $destFolder = "tutorials/flightdelay/2013data"     #The blob name prefix for the files to be uploaded
    #EndRegion

    #Region - Connect to Azure subscription
    Write-Host "`nConnecting to your Azure subscription ..." -ForegroundColor Green
    try{Get-AzureRmContext}
    catch{Login-AzureRmAccount}
    #EndRegion

    #Region - Validate user input
    Write-Host "`nValidating the Azure Storage account and the Blob container..." -ForegroundColor Green
    # Validate the Storage account
    if (-not (Get-AzureRmStorageAccount|Where-Object{$_.StorageAccountName -eq $storageAccountName}))
    {
        Write-Host "The storage account, $storageAccountName, doesn't exist." -ForegroundColor Red
        exit
    }
    else{
        $resourceGroupName = (Get-AzureRmStorageAccount|Where-Object{$_.StorageAccountName -eq $storageAccountName}).ResourceGroupName
    }

    # Validate the container
    $storageAccountKey = (Get-AzureRmStorageAccountKey -StorageAccountName $storageAccountName -ResourceGroupName $resourceGroupName)[0].Value
    $storageContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey

    if (-not (Get-AzureStorageContainer -Context $storageContext |Where-Object{$_.Name -eq $blobContainerName}))
    {
        Write-Host "The Blob container, $blobContainerName, doesn't exist" -ForegroundColor Red
        Exit
    }
    #EngRegion

    #Region - Copy the file from local workstation to Azure Blob storage
    if (test-path -Path $localFolder)
    {
        foreach ($item in Get-ChildItem -Path $localFolder){
            $fileName = "$localFolder\$item"
            $blobName = "$destFolder/$item"

            Write-Host "Copying $fileName to $blobName" -ForegroundColor Green

            Set-AzureStorageBlobContent -File $fileName -Container $blobContainerName -Blob $blobName -Context $storageContext
        }
    }
    else
    {
        Write-Host "The source folder on the workstation doesn't exist" -ForegroundColor Red
    }

    # List the uploaded files on HDInsight
    Get-AzureStorageBlob -Container $blobContainerName  -Context $storageContext -Prefix $destFolder
    #EndRegion
    ```
4. **F5** キーを押して、スクリプトを実行します。

ファイルのアップロード方法として別の方法を選択した場合は、ファイル パスが tutorials/flightdelay/data であることを確認してください。 ファイルにアクセスする構文は次のとおりです。

    wasbs://<ContainerName>@<StorageAccountName>.blob.core.windows.net/tutorials/flightdelay/data

パス tutorials/flightdelay/data は、ファイルのアップロード時に作成した仮想フォルダーです。 月ごとに 1 つ、合計 12 個のファイルがあることを確認します。

> [!NOTE]
> 新しい場所からファイルを読み取るには、Hive クエリを更新する必要があります。
>
> コンテナーのアクセス権限をパブリックに設定するか、またはストレージ アカウントを HDInsight クラスターにバインドする必要があります。 そうしないと、Hive クエリからデータ ファイルにアクセスできません。

- - -

## <a id="appendix-b"></a>付録 B: HiveQL スクリプトを作成してアップロードする
Azure PowerShell を使用して、複数の HiveQL ステートメントを一度に実行することも、HiveQL ステートメントをスクリプト ファイルにまとめることもできます。 このセクションでは、HiveQL スクリプトを作成し、Azure PowerShell を使用して Azure BLOB ストレージにアップロードする方法を説明します。 Hive を利用するには、HiveQL スクリプトが Azure BLOB ストレージに格納されている必要があります。

HiveQL スクリプトは、次の作業を実行します。

1. **delays_raw テーブルを削除します** (テーブルが既に存在する場合)。
2. **delays_raw 外部 Hive テーブルを作成します**。このテーブルはフライト遅延ファイルのある BLOB ストレージの場所を指しています。 このクエリでは、フィールドがコンマ (,) 区切りで、行末が "\n" であることを指定しています。 この場合、フィールド値にコンマが "含まれている" と問題が発生します。Hive は、フィールド区切りのコンマとフィールド値の一部であるコンマを識別できません (ORIGIN\_CITY\_NAME フィールドや DEST\_CITY\_NAME フィールドの値など)。 これに対処するため、クエリでは、間違って複数の列に分割されるデータを格納する TEMP 列を作成します。
3. **delays テーブルを削除します**(テーブルが既に存在する場合)。
4. **delays テーブルを作成します**。 次へ進む前にデータをクリーンアップしておくと面倒がありません。 次のクエリは、delays_raw テーブルを基にして、新しい *delays* テーブルを作成します。 (前述のように) TEMP 列はコピーしません。**substring** 関数を使用してデータから引用符を削除します。
5. **悪天候による平均遅延を計算し、その結果を都市名ごとにグループ化します。** さらに、結果を BLOB ストレージに出力します。 このクエリは、データからアポストロフィを削除し、**weather_delay** の値が null の行を除外します。 このチュートリアルで後ほど使用する Sqoop ではこれらの値が既定では適切に処理されないため、この処理が必要です。

HiveQL コマンドの完全な一覧については、「[Hive Data Definition Language (Hive データ定義言語)][hadoop-hiveql]」を参照してください。 各 HiveQL コマンドは、セミコロンで終了します。

**HiveQL スクリプト ファイルを作成するには**

1. 次のパラメーターを準備します。

    <table border="1">
    <tr><th>変数名</th><th>メモ</th></tr>
    <tr><td>$storageAccountName</td><td>HiveQL スクリプトのアップロード先となる Azure ストレージ アカウント。</td></tr>
    <tr><td>$blobContainerName</td><td>HiveQL スクリプトのアップロード先となる BLOB コンテナー。</td></tr>
    </table>
2. Azure PowerShell ISE を開きます。
3. 次のスクリプトをスクリプト ウィンドウにコピーして貼り付けます。

    ```powershell
    [CmdletBinding()]
    Param(

        # Azure Blob storage variables
        [Parameter(Mandatory=$True,
                    HelpMessage="Enter the Azure storage account name for creating a new HDInsight cluster. If the account doesn't exist, the script will create one.")]
        [String]$storageAccountName,

        [Parameter(Mandatory=$True,
                    HelpMessage="Enter the Azure blob container name for creating a new HDInsight cluster. If not specified, the HDInsight cluster name will be used.")]
        [String]$blobContainerName
    )

    #region - Define variables
    # Treat all errors as terminating
    $ErrorActionPreference = "Stop"

    # The HiveQL script file is exported as this file before it's uploaded to Blob storage
    $hqlLocalFileName = "e:\tutorials\flightdelay\flightdelays.hql"

    # The HiveQL script file will be uploaded to Blob storage as this blob name
    $hqlBlobName = "tutorials/flightdelay/flightdelays.hql"

    # These two constants are used by the HiveQL script file
    #$srcDataFolder = "tutorials/flightdelay/data"
    $dstDataFolder = "/tutorials/flightdelay/output"
    #endregion

    #Region - Connect to Azure subscription
    Write-Host "`nConnecting to your Azure subscription ..." -ForegroundColor Green
    try{Get-AzureRmContext}
    catch{Login-AzureRmAccount}
    #EndRegion

    #Region - Validate user input
    Write-Host "`nValidating the Azure Storage account and the Blob container..." -ForegroundColor Green
    # Validate the Storage account
    if (-not (Get-AzureRmStorageAccount|Where-Object{$_.StorageAccountName -eq $storageAccountName}))
    {
        Write-Host "The storage account, $storageAccountName, doesn't exist." -ForegroundColor Red
        exit
    }
    else{
        $resourceGroupName = (Get-AzureRmStorageAccount|Where-Object{$_.StorageAccountName -eq $storageAccountName}).ResourceGroupName
    }

    # Validate the container
    $storageAccountKey = (Get-AzureRmStorageAccountKey -StorageAccountName $storageAccountName -ResourceGroupName $resourceGroupName)[0].Value
    $storageContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey

    if (-not (Get-AzureStorageContainer -Context $storageContext |Where-Object{$_.Name -eq $blobContainerName}))
    {
        Write-Host "The Blob container, $blobContainerName, doesn't exist" -ForegroundColor Red
        Exit
    }
    #EngRegion

    #region - Validate the file and file path

    # Check if a file with the same file name already exists on the workstation
    Write-Host "`nvalidating the folder structure on the workstation for saving the HQL script file ..."  -ForegroundColor Green
    if (test-path $hqlLocalFileName){

        $isDelete = Read-Host 'The file, ' $hqlLocalFileName ', exists.  Do you want to overwirte it? (Y/N)'

        if ($isDelete.ToLower() -ne "y")
        {
            Exit
        }
    }

    # Create the folder if it doesn't exist
    $folder = split-path $hqlLocalFileName
    if (-not (test-path $folder))
    {
        Write-Host "`nCreating folder, $folder ..." -ForegroundColor Green

        new-item $folder -ItemType directory
    }
    #end region

    #region - Write the Hive script into a local file
    Write-Host "`nWriting the Hive script into a file on your workstation ..." `
                -ForegroundColor Green

    $hqlDropDelaysRaw = "DROP TABLE delays_raw;"

    $hqlCreateDelaysRaw = "CREATE EXTERNAL TABLE delays_raw (" +
            "YEAR string, " +
            "FL_DATE string, " +
            "UNIQUE_CARRIER string, " +
            "CARRIER string, " +
            "FL_NUM string, " +
            "ORIGIN_AIRPORT_ID string, " +
            "ORIGIN string, " +
            "ORIGIN_CITY_NAME string, " +
            "ORIGIN_CITY_NAME_TEMP string, " +
            "ORIGIN_STATE_ABR string, " +
            "DEST_AIRPORT_ID string, " +
            "DEST string, " +
            "DEST_CITY_NAME string, " +
            "DEST_CITY_NAME_TEMP string, " +
            "DEST_STATE_ABR string, " +
            "DEP_DELAY_NEW float, " +
            "ARR_DELAY_NEW float, " +
            "CARRIER_DELAY float, " +
            "WEATHER_DELAY float, " +
            "NAS_DELAY float, " +
            "SECURITY_DELAY float, " +
            "LATE_AIRCRAFT_DELAY float) " +
        "ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' " +
        "LINES TERMINATED BY '\n' " +
        "STORED AS TEXTFILE " +
        "LOCATION 'wasbs://flightdelay@hditutorialdata.blob.core.windows.net/2013Data';"

    $hqlDropDelays = "DROP TABLE delays;"

    $hqlCreateDelays = "CREATE TABLE delays AS " +
        "SELECT YEAR AS year, " +
            "FL_DATE AS flight_date, " +
            "substring(UNIQUE_CARRIER, 2, length(UNIQUE_CARRIER) -1) AS unique_carrier, " +
            "substring(CARRIER, 2, length(CARRIER) -1) AS carrier, " +
            "substring(FL_NUM, 2, length(FL_NUM) -1) AS flight_num, " +
            "ORIGIN_AIRPORT_ID AS origin_airport_id, " +
            "substring(ORIGIN, 2, length(ORIGIN) -1) AS origin_airport_code, " +
            "substring(ORIGIN_CITY_NAME, 2) AS origin_city_name, " +
            "substring(ORIGIN_STATE_ABR, 2, length(ORIGIN_STATE_ABR) -1)  AS origin_state_abr, " +
            "DEST_AIRPORT_ID AS dest_airport_id, " +
            "substring(DEST, 2, length(DEST) -1) AS dest_airport_code, " +
            "substring(DEST_CITY_NAME,2) AS dest_city_name, " +
            "substring(DEST_STATE_ABR, 2, length(DEST_STATE_ABR) -1) AS dest_state_abr, " +
            "DEP_DELAY_NEW AS dep_delay_new, " +
            "ARR_DELAY_NEW AS arr_delay_new, " +
            "CARRIER_DELAY AS carrier_delay, " +
            "WEATHER_DELAY AS weather_delay, " +
            "NAS_DELAY AS nas_delay, " +
            "SECURITY_DELAY AS security_delay, " +
            "LATE_AIRCRAFT_DELAY AS late_aircraft_delay " +
        "FROM delays_raw;"

    $hqlInsertLocal = "INSERT OVERWRITE DIRECTORY '$dstDataFolder' " +
        "SELECT regexp_replace(origin_city_name, '''', ''), " +
            "avg(weather_delay) " +
        "FROM delays " +
        "WHERE weather_delay IS NOT NULL " +
        "GROUP BY origin_city_name;"

    $hqlScript = $hqlDropDelaysRaw + $hqlCreateDelaysRaw + $hqlDropDelays + $hqlCreateDelays + $hqlInsertLocal

    $hqlScript | Out-File $hqlLocalFileName -Encoding ascii -Force
    #endregion

    #region - Upload the Hive script to the default Blob container
    Write-Host "`nUploading the Hive script to the default Blob container ..." -ForegroundColor Green

    # Create a storage context object
    $storageAccountKey = (Get-AzureRmStorageAccountKey -StorageAccountName $storageAccountName -ResourceGroupName $resourceGroupName)[0].Value
    $destContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey

    # Upload the file from local workstation to Blob storage
    Set-AzureStorageBlobContent -File $hqlLocalFileName -Container $blobContainerName -Blob $hqlBlobName -Context $destContext
    #endregion

    Write-host "`nEnd of the PowerShell script" -ForegroundColor Green
    ```

    スクリプトには次の変数が使用されています。

   * **$hqlLocalFileName** - HiveQL スクリプト ファイルは、BLOB ストレージにアップロードされる前に、いったんローカルに保存されます。 その際に、このファイル名が使用されます。 既定値は、<u>C:\tutorials\flightdelay\flightdelays.hql</u> です。
   * **$hqlBlobName** - Azure BLOB ストレージで使用される HiveQL スクリプト ファイルの BLOB 名。 既定値は、tutorials/flightdelay/flightdelays.hql です。 ファイルは直接 Azure BLOB ストレージに書き込まれるため、BLOB 名の先頭に "/" はありません。 BLOB ストレージからファイルにアクセスする場合は、ファイル名の先頭に "/" を追加する必要があります。
   * **$srcDataFolder** と **$dstDataFolder** はそれぞれ "tutorials/flightdelay/data" と "tutorials/flightdelay/output" です。

- - -
## <a id="appendix-c"></a>付録 C - Sqoop ジョブを出力するための Azure SQL Database の準備
**SQL データベースを準備するには (Sqoop スクリプトとマージ)**

1. 次のパラメーターを準備します。

    <table border="1">
    <tr><th>変数名</th><th>メモ</th></tr>
    <tr><td>$sqlDatabaseServerName</td><td>Azure SQL Database サーバーの名前。 新しいサーバーを作成する場合は、何も入力しないでください。</td></tr>
    <tr><td>$sqlDatabaseUsername</td><td>Azure SQL Database サーバーのログイン名。 $sqlDatabaseServerName が既存のサーバーである場合、ログイン名とログイン パスワードを使用してサーバーを認証します。 $sqlDatabaseServerName が既存のサーバーでない場合、ログイン名とログイン パスワードを使用して新しいサーバーを作成します。</td></tr>
    <tr><td>$sqlDatabasePassword</td><td>Azure SQL Database サーバーのログイン パスワード。</td></tr>
    <tr><td>$sqlDatabaseLocation</td><td>この値は、新しい Azure データベース サーバーを作成するときにのみ使用されます。</td></tr>
    <tr><td>$sqlDatabaseName</td><td>Sqoop ジョブ用の AvgDelays テーブルの作成に使用する SQL Database。 空白のままにすると、"HDISqoop" というデータベースが作成されます。 Sqoop ジョブ出力用のテーブル名は AvgDelays です。 </td></tr>
    </table>
2. Azure PowerShell ISE を開きます。
3. 次のスクリプトをスクリプト ウィンドウにコピーして貼り付けます。

    ```powershell
    [CmdletBinding()]
    Param(

        # Azure Resource group variables
        [Parameter(Mandatory=$True,
                HelpMessage="Enter the Azure resource group name. It will be created if it doesn't exist.")]
        [String]$resourceGroupName,

        # SQL database server variables
        [Parameter(Mandatory=$True,
                HelpMessage="Enter the Azure SQL Database Server Name. It will be created if it doesn't exist.")]
        [String]$sqlDatabaseServer,

        [Parameter(Mandatory=$True,
                HelpMessage="Enter the Azure SQL Database admin user.")]
        [String]$sqlDatabaseLogin,

        [Parameter(Mandatory=$True,
                HelpMessage="Enter the Azure SQL Database admin user password.")]
        [String]$sqlDatabasePassword,

        [Parameter(Mandatory=$True,
                HelpMessage="Enter the region to create the Database in.")]
        [String]$sqlDatabaseLocation,   #For example, West US.

        # SQL database variables
        [Parameter(Mandatory=$True,
                HelpMessage="Enter the database name. It will be created if it doesn't exist.")]
        [String]$sqlDatabaseName # specify the database name if you have one created. Otherwise use "" to have the script create one for you.
    )

    # Treat all errors as terminating
    $ErrorActionPreference = "Stop"

    #region - Constants and variables

    # IP address REST service used for retrieving external IP address and creating firewall rules
    [String]$ipAddressRestService = "http://bot.whatismyipaddress.com"
    [String]$fireWallRuleName = "FlightDelay"

    # SQL database variables
    [String]$sqlDatabaseMaxSizeGB = 10

    #SQL query string for creating AvgDelays table
    [String]$sqlDatabaseTableName = "AvgDelays"
    [String]$sqlCreateAvgDelaysTable = " CREATE TABLE [dbo].[$sqlDatabaseTableName](
                [origin_city_name] [nvarchar](50) NOT NULL,
                [weather_delay] float,
            CONSTRAINT [PK_$sqlDatabaseTableName] PRIMARY KEY CLUSTERED
            (
                [origin_city_name] ASC
            )
            )"
    #endregion

    #Region - Connect to Azure subscription
    Write-Host "`nConnecting to your Azure subscription ..." -ForegroundColor Green
    try{Get-AzureRmContext}
    catch{Login-AzureRmAccount}
    #EndRegion

    #region - Create and validate Azure resouce group
    try{
        Get-AzureRmResourceGroup -Name $resourceGroupName
    }
    catch{
        New-AzureRmResourceGroup -Name $resourceGroupName -Location $sqlDatabaseLocation
    }

    #EndRegion

    #region - Create and validate Azure SQL database server
    try{
        Get-AzureRmSqlServer -ServerName $sqlDatabaseServer -ResourceGroupName $resourceGroupName}
    catch{
        Write-Host "`nCreating SQL Database server ..."  -ForegroundColor Green

        $sqlDatabasePW = ConvertTo-SecureString -String $sqlDatabasePassword -AsPlainText -Force
        $credential = New-Object System.Management.Automation.PSCredential($sqlDatabaseLogin,$sqlDatabasePW)

        $sqlDatabaseServer = (New-AzureRmSqlServer -ResourceGroupName $resourceGroupName -ServerName $sqlDatabaseServer -SqlAdministratorCredentials $credential -Location $sqlDatabaseLocation).ServerName
        Write-Host "`tThe new SQL database server name is $sqlDatabaseServer." -ForegroundColor Cyan

        Write-Host "`nCreating firewall rule, $fireWallRuleName ..." -ForegroundColor Green
        $workstationIPAddress = Invoke-RestMethod $ipAddressRestService
        New-AzureRmSqlServerFirewallRule -ResourceGroupName $resourceGroupName -ServerName $sqlDatabaseServer -FirewallRuleName "$fireWallRuleName-workstation" -StartIpAddress $workstationIPAddress -EndIpAddress $workstationIPAddress

        #To allow other Azure services to access the server add a firewall rule and set both the StartIpAddress and EndIpAddress to 0.0.0.0. Note that this allows Azure traffic from any Azure subscription to access the server.
        New-AzureRmSqlServerFirewallRule -ResourceGroupName $resourceGroupName -ServerName $sqlDatabaseServer -FirewallRuleName "$fireWallRuleName-Azureservices" -StartIpAddress "0.0.0.0" -EndIpAddress "0.0.0.0"
    }

    #endregion

    #region - Create and validate Azure SQL database

    try {
        Get-AzureRmSqlDatabase -ResourceGroupName $resourceGroupName -ServerName $sqlDatabaseServer -DatabaseName $sqlDatabaseName
    }
    catch {
        Write-Host "`nCreating SQL Database, $sqlDatabaseName ..."  -ForegroundColor Green
        New-AzureRMSqlDatabase -ResourceGroupName $resourceGroupName -ServerName $sqlDatabaseServer -DatabaseName $sqlDatabaseName -Edition "Standard" -RequestedServiceObjectiveName "S1"
    }

    #endregion

    #region -  Execute an SQL command to create the AvgDelays table

    Write-Host "`nCreating SQL Database table ..."  -ForegroundColor Green
    $conn = New-Object System.Data.SqlClient.SqlConnection
    $conn.ConnectionString = "Data Source=$sqlDatabaseServer.database.windows.net;Initial Catalog=$sqlDatabaseName;User ID=$sqlDatabaseLogin;Password=$sqlDatabasePassword;Encrypt=true;Trusted_Connection=false;"
    $conn.open()
    $cmd = New-Object System.Data.SqlClient.SqlCommand
    $cmd.connection = $conn
    $cmd.commandtext = $sqlCreateAvgDelaysTable
    $cmd.executenonquery()

    $conn.close()

    Write-host "`nEnd of the PowerShell script" -ForegroundColor Green
    ```

   > [!NOTE]
   > このスクリプトは、Representational State Transfer (REST) サービス (http://bot.whatismyipaddress.com) を使用して外部 IP アドレスを取得します。 SQL Database サーバーのファイアウォール ルールを作成する際に、その IP アドレスを使用します。

    スクリプトには次のいくつかの変数が使用されています。

   * **$ipAddressRestService** - 既定値は http://bot.whatismyipaddress.com です。 外部 IP アドレスを取得するためのパブリック IP アドレス (REST サービス) です。 必要に応じて他のサービスを使用することもできます。 このサービスを使用して取得した外部 IP アドレスは、Azure SQL Database サーバーのファイアウォール ルールを作成する際に使用され、ご利用のワークステーションから (Windows PowerShell スクリプトを使用して) データベースへのアクセスが許可されます。
   * **$fireWallRuleName** - Azure SQL Database サーバーのファイアウォール ルールの名前です。 既定の名前は <u>FlightDelay</u> です。 この名前は必要に応じて変更できます。
   * **$sqlDatabaseMaxSizeGB** - この値は、新しい Azure SQL Database サーバーを作成するときにのみ使用されます。 既定値は 10 GB です。 このチュートリアルにはこれで十分です。
   * **$sqlDatabaseName** - この値は、新しい Azure SQL データベースを作成するときにのみ使用されます。 既定値は HDISqoop です。 この名前を変更した場合は、Sqoop Windows PowerShell スクリプトにも反映する必要があります。
4. **F5** キーを押して、スクリプトを実行します。
5. スクリプトの出力結果を検証します。 スクリプトが正常に実行されたことを確認してください。

## <a id="nextsteps"></a> 次のステップ
ここでは、ファイルを Azure BLOB ストレージにアップロードする方法、Azure BLOB ストレージのデータを Hive テーブルに取り込む方法、Hive クエリの実行方法、Sqoop を使用して HDFS から Azure SQL Database にデータをエクスポートする方法を学習しました。 詳細については、次の記事を参照してください。

* [HDInsight の概要][hdinsight-get-started]
* [HDInsight での Hive の使用][hdinsight-use-hive]
* [HDInsight での Oozie の使用][hdinsight-use-oozie]
* [HDInsight での Sqoop の使用][hdinsight-use-sqoop]
* [HDInsight での Pig の使用][hdinsight-use-pig]
* [HDInsight 用 Java MapReduce プログラムの開発][hdinsight-develop-mapreduce]

[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/

[rita-website]: http://www.transtats.bts.gov/DL_SelectFields.asp?Table_ID=236&DB_Short_Name=On-Time
[powershell-install-configure]: /powershell/azureps-cmdlets-docs

[hdinsight-use-oozie]: hdinsight-use-oozie.md
[hdinsight-use-hive]: hdinsight-use-hive.md
[hdinsight-provision]: hdinsight-provision-clusters.md
[hdinsight-storage]: hdinsight-hadoop-use-blob-storage.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-get-started]: hdinsight-hadoop-linux-tutorial-get-started.md
[hdinsight-use-sqoop]: hdinsight-use-sqoop.md
[hdinsight-use-pig]: hdinsight-use-pig.md
[hdinsight-develop-mapreduce]: hdinsight-develop-deploy-java-mapreduce-linux.md

[hadoop-hiveql]: https://cwiki.apache.org/confluence/display/Hive/LanguageManual+DDL
[hadoop-shell-commands]: http://hadoop.apache.org/docs/r0.18.3/hdfs_shell.html

[technetwiki-hive-error]: http://social.technet.microsoft.com/wiki/contents/articles/23047.hdinsight-hive-error-unable-to-rename.aspx

[image-hdi-flightdelays-avgdelays-dataset]: ./media/hdinsight-analyze-flight-delay-data/HDI.FlightDelays.AvgDelays.DataSet.png
[img-hdi-flightdelays-run-hive-job-output]: ./media/hdinsight-analyze-flight-delay-data/HDI.FlightDelays.RunHiveJob.Output.png
[img-hdi-flightdelays-flow]: ./media/hdinsight-analyze-flight-delay-data/HDI.FlightDelays.Flow.png

