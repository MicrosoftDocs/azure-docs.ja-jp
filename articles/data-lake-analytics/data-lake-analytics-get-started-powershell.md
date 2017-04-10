---
title: "Azure PowerShell で Azure Data Lake Analytics の使用を開始する | Microsoft Docs"
description: "Azure PowerShell を使用して Data Lake Analytics アカウントを作成し、U-SQL で Data Lake Analytics ジョブを作成して、ジョブを送信する方法について説明します。 "
services: data-lake-analytics
documentationcenter: 
author: edmacauley
manager: jhubbard
editor: cgronlun
ms.assetid: 8a4e901e-9656-4a60-90d0-d78ff2f00656
ms.service: data-lake-analytics
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 09/21/2016
ms.author: edmaca
translationtype: Human Translation
ms.sourcegitcommit: 0994765e37dd8ee1fa6a639a2ed60c913cb170fe
ms.openlocfilehash: a1244ee080d333338b1f0d9e49d1776822f535ed


---
# <a name="tutorial-get-started-with-azure-data-lake-analytics-using-azure-powershell"></a>チュートリアル: Azure PowerShell で Azure Data Lake Analytics の使用を開始する
[!INCLUDE [get-started-selector](../../includes/data-lake-analytics-selector-get-started.md)]

Azure PowerShell を使用して、Azure Data Lake Analytics アカウントを作成し、 [U-SQL](data-lake-analytics-u-sql-get-started.md)で Data Lake Analytics ジョブを定義して、Data Lake Analytics アカウントにジョブを送信する方法について説明します。 Data Lake Analytics の詳細については、[Azure Data Lake Analytics の概要](data-lake-analytics-overview.md)に関する記事をご覧ください。

このチュートリアルでは、タブ区切り値 (TSV) ファイルを読み取り、それをコンマ区切り値 (CSV) ファイルに変換するジョブを開発します。 サポートされている他のツールを使用する同じチュートリアルを読み進めるには、このセクションの上部にあるタブをクリックします。

## <a name="prerequisites"></a>前提条件
このチュートリアルを読み始める前に、次の項目を用意する必要があります。

* **Azure サブスクリプション**。 [Azure 無料試用版の取得](https://azure.microsoft.com/pricing/free-trial/)に関するページを参照してください。
* **Azure PowerShell を実行できるワークステーション**。 「 [Azure PowerShell のインストールと構成の方法](/powershell/azureps-cmdlets-docs)」を参照してください。

## <a name="create-data-lake-analytics-account"></a>Data Lake Analytics アカウントを作成する
ジョブを実行するには、Data Lake Analytics アカウントが必要です。 Data Lake Analytics アカウントを作成するには、以下を指定する必要があります。

* **Azure リソース グループ**: Data Lake Analytics アカウントは、Azure リソース グループ内に作成する必要があります。 [Azure Resource Manager](../azure-resource-manager/resource-group-overview.md) を使用すると、アプリケーション内の複数リソースを 1 つのグループとして操作できます。 アプリケーションのこれらすべてのリソースを、1 回の連携した操作でデプロイ、更新、または削除できます。  

    サブスクリプションのリソース グループを列挙するには:

        Get-AzureRmResourceGroup

    新しいリソース グループを作成するには:

        New-AzureRmResourceGroup `
            -Name "<Your resource group name>" `
            -Location "<Azure Data Center>" # For example, "East US 2"
* **Data Lake Analytics アカウント名**
* **場所**: Data Lake Analytics をサポートするいずれかの Azure データ センター。
* **既定の Data Lake アカウント**: 各 Data Lake Analytics アカウントには既定の Data Lake アカウントがあります。

    新しい Data Lake アカウントを作成するには:

        New-AzureRmDataLakeStoreAccount `
            -ResourceGroupName "<Your Azure resource group name>" `
            -Name "<Your Data Lake account name>" `
            -Location "<Azure Data Center>"  # For example, "East US 2"

  > [!NOTE]
  > Data Lake アカウント名には小文字と数字のみを含める必要があります。
  >
  >

**Data Lake Analytics アカウントを作成するには**

1. Windows ワークステーションで、PowerShell ISE を開きます。
2. 次のスクリプトを実行します。

        $resourceGroupName = "<ResourceGroupName>"
        $dataLakeStoreName = "<DataLakeAccountName>"
        $dataLakeAnalyticsName = "<DataLakeAnalyticsAccountName>"
        $location = "East US 2"

        Write-Host "Create a resource group ..." -ForegroundColor Green
        New-AzureRmResourceGroup `
            -Name  $resourceGroupName `
            -Location $location

        Write-Host "Create a Data Lake account ..."  -ForegroundColor Green
        New-AzureRmDataLakeStoreAccount `
            -ResourceGroupName $resourceGroupName `
            -Name $dataLakeStoreName `
            -Location $location

        Write-Host "Create a Data Lake Analytics account ..."  -ForegroundColor Green
        New-AzureRmDataLakeAnalyticsAccount `
            -Name $dataLakeAnalyticsName `
            -ResourceGroupName $resourceGroupName `
            -Location $location `
            -DefaultDataLake $dataLakeStoreName

        Write-Host "The newly created Data Lake Analytics account ..."  -ForegroundColor Green
        Get-AzureRmDataLakeAnalyticsAccount `
            -ResourceGroupName $resourceGroupName `
            -Name $dataLakeAnalyticsName  

## <a name="upload-data-to-data-lake"></a>Data Lake へのデータのアップロード
このチュートリアルでは、いくつかの検索ログを処理します。  検索ログは、Data Lake Store または Azure Blob Storage に格納できます。

サンプルの検索ログ ファイルは、パブリック Azure BLOB コンテナーにコピーされました。 次の PowerShell スクリプトを使用して、ファイルをワークステーションにダウンロードしてから、Data Lake Analytics アカウントの既定の Data Lake Store アカウントにファイルをアップロードします。

    $dataLakeStoreName = "<The default Data Lake Store account name>"

    $localFolder = "C:\Tutorials\Downloads\" # A temp location for the file.
    $storageAccount = "adltutorials"  # Don't modify this value.
    $container = "adls-sample-data"  #Don't modify this value.

    # Create the temp location    
    New-Item -Path $localFolder -ItemType Directory -Force

    # Download the sample file from Azure Blob storage
    $context = New-AzureStorageContext -StorageAccountName $storageAccount -Anonymous
    $blobs = Azure\Get-AzureStorageBlob -Container $container -Context $context
    $blobs | Get-AzureStorageBlobContent -Context $context -Destination $localFolder

    # Upload the file to the default Data Lake Store account    
    Import-AzureRmDataLakeStoreItem -AccountName $dataLakeStoreName -Path $localFolder"SearchLog.tsv" -Destination "/Samples/Data/SearchLog.tsv"

次の PowerShell スクリプトは、Data Lake Analytics アカウントの既定の Data Lake Store 名を取得する方法を示しています。

    $resourceGroupName = "<ResourceGroupName>"
    $dataLakeAnalyticsName = "<DataLakeAnalyticsAccountName>"
    $dataLakeStoreName = (Get-AzureRmDataLakeAnalyticsAccount -ResourceGroupName $resourceGroupName -Name $dataLakeAnalyticsName).Properties.DefaultDataLakeAccount
    echo $dataLakeStoreName

> [!NOTE]
> Azure ポータルでは、既定の Data Lake Store アカウントにサンプル データ ファイルをコピーするためのユーザー インターフェイスが提供されます。 手順については、「 [Azure ポータルで Azure Data Lake Analytics の使用を開始する](data-lake-analytics-get-started-portal.md#prepare-source-data)」を参照してください。
>
>

Data Lake Analytics は、Azure BLOB ストレージにもアクセスできます。  Azure BLOB ストレージへのデータのアップロードについては、「 [Azure Storage での Azure PowerShell の使用](../storage/storage-powershell-guide-full.md)」を参照してください。

## <a name="submit-data-lake-analytics-jobs"></a>Data Lake Analytics ジョブを送信する
Data Lake Analtyics ジョブは U-SQL 言語で記述されます。 U-SQL の詳細については、[U-SQL 言語の使用](data-lake-analytics-u-sql-get-started.md)に関する記事および「[U-SQL Language Reference (U-SQL 言語リファレンス)](http://go.microsoft.com/fwlink/?LinkId=691348)」をご覧ください。

**Data Lake Analytics ジョブ スクリプトを作成するには**

* 次の U-SQL スクリプトでテキスト ファイルを作成し、ワークステーションにテキスト ファイルを保存します。

        @searchlog =
            EXTRACT UserId          int,
                    Start           DateTime,
                    Region          string,
                    Query           string,
                    Duration        int?,
                    Urls            string,
                    ClickedUrls     string
            FROM "/Samples/Data/SearchLog.tsv"
            USING Extractors.Tsv();

        OUTPUT @searchlog   
            TO "/Output/SearchLog-from-Data-Lake.csv"
        USING Outputters.Csv();

    この U-SQL スクリプトでは、**Extractors.Tsv()** を使用してソース データ ファイルを読み取ってから、**Outputters.Csv()** を使用して csv ファイルを作成します。

    ソース ファイルを別の場所にコピーしない限り、2 つのパスを変更しないでください。  存在しない場合、Data Lake Analytics は出力フォルダーを作成します。

    既定の Data Lake アカウントに格納されたファイルの相対パスを使用する方が簡単です。 絶対パスを使用することもできます。  たとえば、次のように入力します。

        adl://<Data LakeStorageAccountName>.azuredatalakestore.net:443/Samples/Data/SearchLog.tsv

    リンクされたストレージ アカウント内のファイルへのアクセスには、絶対パスを使用する必要があります。  リンクされた Azure Storage アカウントに格納されているファイルの構文は以下のとおりです。

        wasb://<BlobContainerName>@<StorageAccountName>.blob.core.windows.net/Samples/Data/SearchLog.tsv

  > [!NOTE]
  > パブリック BLOB またはパブリック コンテナーのアクセス許可を持つ Azure BLOB コンテナーは、現在サポートされていません。    
  >
  >

**ジョブを送信するには**

1. Windows ワークステーションで、PowerShell ISE を開きます。
2. 次のスクリプトを実行します。

        $dataLakeAnalyticsName = "<DataLakeAnalyticsAccountName>"
        $usqlScript = "c:\tutorials\data-lake-analytics\copyFile.usql"

        $job = Submit-AzureRmDataLakeAnalyticsJob -Name "convertTSVtoCSV" -AccountName $dataLakeAnalyticsName –ScriptPath $usqlScript

        Wait-AdlJob -Account $dataLakeAnalyticsName -JobId $job.JobId

        Get-AzureRmDataLakeAnalyticsJob -AccountName $dataLakeAnalyticsName -JobId $job.JobId

    このスクリプトでは、U-SQL スクリプト ファイルが c:\tutorials\data-lake-analytics\copyFile.usql に格納されます。 適宜、ファイル パスを更新してください。

ジョブが完了したら、以下のコマンドレットを使用し、ファイルをリストしてダウンロードできます。

    $resourceGroupName = "<Resource Group Name>"
    $dataLakeAnalyticName = "<Data Lake Analytic Account Name>"
    $destFile = "C:\tutorials\data-lake-analytics\SearchLog-from-Data-Lake.csv"

    $dataLakeStoreName = (Get-AzureRmDataLakeAnalyticsAccount -ResourceGroupName $resourceGroupName -Name $dataLakeAnalyticName).Properties.DefaultDataLakeAccount

    Get-AzureRmDataLakeStoreChildItem -AccountName $dataLakeStoreName -path "/Output"

    Export-AzureRmDataLakeStoreItem -AccountName $dataLakeStoreName -Path "/Output/SearchLog-from-Data-Lake.csv" -Destination $destFile

## <a name="see-also"></a>関連項目
* 他のツールを使用する同じチュートリアルを表示するには、ページの上部にあるタブ セレクターをクリックします。
* より複雑なクエリを表示する場合は、「 [チュートリアル: Azure Data Lake Analytics を使用して Web サイトのログを分析する](data-lake-analytics-analyze-weblogs.md)」をご覧ください。
* U-SQL アプリケーションの開発を開始する場合は、「 [チュートリアル: Data Lake Tools for Visual Studio を使用する U-SQL スクリプトの開発](data-lake-analytics-data-lake-tools-get-started.md)」をご覧ください。
* U-SQL の詳細については、「 [Azure Data Lake Analytics U-SQL 言語の使用](data-lake-analytics-u-sql-get-started.md)」を参照してください。
* 管理タスクについては、「 [Azure ポータルを使用する Azure Data Lake Analytics の管理](data-lake-analytics-manage-use-portal.md)」をご覧ください。
* Data Lake Analytics の概要については、「 [Microsoft Azure Data Lake Analytics の概要](data-lake-analytics-overview.md)」を参照してください。



<!--HONumber=Dec16_HO2-->


