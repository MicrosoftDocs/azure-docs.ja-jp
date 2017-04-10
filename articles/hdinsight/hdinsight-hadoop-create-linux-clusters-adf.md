---
title: "Data Factory を使用して Azure HDInsight (Hadoop) を作成する | Microsoft Docs"
description: "Azure Data Factory を使用して HDInsight でオンデマンドの Hadoop クラスターを作成する方法について説明します。"
services: hdinsight
documentationcenter: 
tags: azure-portal
author: spelluru
manager: jhubbard
editor: cgronlun
ms.assetid: 1f3b3a78-4d16-4d99-ba6e-06f7bb185d6a
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 02/23/2017
ms.author: spelluru
translationtype: Human Translation
ms.sourcegitcommit: 4f2230ea0cc5b3e258a1a26a39e99433b04ffe18
ms.openlocfilehash: a77aa4a44bbb7dafffa4269c3713153df9bbced9
ms.lasthandoff: 03/25/2017


---
# <a name="create-on-demand-hadoop-clusters-in-hdinsight-using-azure-data-factory"></a>Azure Data Factory を使用して HDInsight でオンデマンドの Hadoop クラスターを作成する
[!INCLUDE [selector](../../includes/hdinsight-create-linux-cluster-selector.md)]

[Azure Data Factory](../data-factory/data-factory-introduction.md) は、データの移動や変換を調整および自動化する、クラウドベースのデータ統合サービスです。 Azure Data Factory を使用すると、入力データ スライスを処理するために HDInsight Hadoop クラスターを Just-In-Time 方式で作成し、処理が完了した時点でクラスターを削除することができます。 オンデマンドの HDInsight Hadoop クラスターを使用するメリットをいくつか次に示します。

- 課金の対象になるのは、HDInsight Hadoop クラスターでジョブが実行されている時間 (と構成可能な短いアイドル時間) のみです。 料金は、HDInsight クラスターを使用しているかどうかに関係なく、分単位で課金されます。 Data Factory でオンデマンドの HDInsight のリンクされたサービスを使用すると、クラスターはオンデマンドで作成されます。 さらに、クラスターは、ジョブの完了時に自動的に削除されます。 そのため、ジョブの実行時間と短いアイドル時間 (time-to-live 設定) のみが課金の対象となります。 
- Data Factory パイプラインを使用してワークフローを作成できます。 たとえば、パイプラインを使用して、オンプレミスの SQL Server から Azure BLOB ストレージにデータをコピーし、オンデマンドの HDInsight Hadoop クラスターで Hive スクリプトと Pig スクリプトを実行してデータを処理することができます。 次に、BI アプリケーションで使用するために、結果データを Azure SQL Data Warehouse にコピーします。
- 定期的 (毎時、毎日、毎週、毎月など) に実行されるように、ワークフローのスケジュールを設定することができます。

Azure Data Factory では、データ ファクトリに 1 つまたは複数のデータ パイプラインを設定できます。 データ パイプラインには、1 つ以上のアクティビティがあります。 アクティビティには、[データ移動アクティビティ](../data-factory/data-factory-data-movement-activities.md)と[データ変換アクティビティ](../data-factory/data-factory-data-transformation-activities.md)の 2 種類があります。 データ移動アクティビティ (現在はコピー アクティビティのみ) は、ソース データ ストアからコピー先データ ストアにデータを移動するために使用します。 データ変換アクティビティは、データを変換/処理するために使用します。 HDInsight Hive アクティビティは、Data Factory でサポートされるデータ変換アクティビティの 1 つです。 このチュートリアルでは、Hive 変換アクティビティを使用します。 

Hive アクティビティを構成して、独自の HDInsight Hadoop クラスターまたはオンデマンドの HDInsight Hadoop クラスターを使用することができます。 このチュートリアルでは、データ ファクトリ パイプラインの Hive アクティビティが、オンデマンドの HDInsight クラスターを使用するように構成されています。 したがって、アクティビティを実行してデータ スライスを処理するとき、次の処理が実行されます。 

1. スライスを処理するために、HDInsight Hadoop クラスターが Just-In-Time 方式で自動的に作成されます。  
2. クラスター上で HiveQL スクリプトを実行することによって入力データが処理されます。 
3. HDInsight Hadoop クラスターは、処理が完了し、(TimeToLive 設定で) 構成された時間アイドル状態になると、削除されます。 この TimeToLive アイドル時間内に次のデータ スライスを処理できる場合、スライスを処理するために同じクラスターが使用されます。  
      
このチュートリアルの Hive アクティビティに関連付けられた HiveQL スクリプトは、次のアクションを実行します。 

1. Azure BLOB ストレージに格納されている生の Web ログ データを参照する外部テーブルを作成します。
2. 生データを年と月で分割します。
3. 分割したデータを Azure BLOB ストレージに格納します。 

このチュートリアルの Hive アクティビティに関連付けられた HiveQL スクリプトは、Azure BLOB ストレージに格納されている生の Web ログ データを参照する外部テーブルを作成します。 入力ファイル内の各月のサンプル行を次に示します。

```
2014-01-01,02:01:09,SAMPLEWEBSITE,GET,/blogposts/mvc4/step2.png,X-ARR-LOG-ID=2ec4b8ad-3cf0-4442-93ab-837317ece6a1,80,-,1.54.23.196,Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36,-,http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx,\N,200,0,0,53175,871
2014-02-01,02:01:10,SAMPLEWEBSITE,GET,/blogposts/mvc4/step7.png,X-ARR-LOG-ID=d7472a26-431a-4a4d-99eb-c7b4fda2cf4c,80,-,1.54.23.196,Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36,-,http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx,\N,200,0,0,30184,871
2014-03-01,02:01:10,SAMPLEWEBSITE,GET,/blogposts/mvc4/step7.png,X-ARR-LOG-ID=d7472a26-431a-4a4d-99eb-c7b4fda2cf4c,80,-,1.54.23.196,Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36,-,http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx,\N,200,0,0,30184,871
```

この HiveQL スクリプトは、生データを年と月で分割します。 このスクリプトは、前の入力に基づいて 3 つの出力フォルダーを作成します。 各フォルダーには、各月のエントリを含むファイルが格納されます。

```
adfgetstarted/partitioneddata/year=2014/month=1/000000_0
adfgetstarted/partitioneddata/year=2014/month=2/000000_0
adfgetstarted/partitioneddata/year=2014/month=3/000000_0
```

Hive アクティビティと Data Factory データ変換アクティビティの一覧については、「[Azure Data Factory を使用した変換と分析](../data-factory/data-factory-data-transformation-activities.md)」を参照してください。

> [!NOTE]
> 現時点では、Azure Data Factory からは HDInsight クラスター バージョン 3.2 のみを作成できます。

## <a name="prerequisites"></a>前提条件
この記事の手順を開始する前に、次の項目を用意する必要があります。

* [Azure サブスクリプション](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/)。
* Azure PowerShell。

[!INCLUDE [use-latest-version](../../includes/hdinsight-use-latest-powershell.md)]

### <a name="prepare-storage-account"></a>ストレージ アカウントの準備
このシナリオでは、最大 3 つのストレージ アカウントを使用できます。

- HDInsight クラスターの既定のストレージ アカウント
- 入力データ用のストレージ アカウント
- 出力データ用のストレージ アカウント

ここでは、このチュートリアルを簡単にするために、この 3 つの目的に 1 つのストレージ アカウントを使用します。 このセクションで紹介する Azure PowerShell のサンプル スクリプトでは、次のタスクを実行します。

1. Azure にログインします。
2. Azure リソース グループを作成します。
3. Azure ストレージ アカウントを作成します。
4. ストレージ アカウントに BLOB コンテナーを作成します。
5. BLOB コンテナーに次の 2 つのファイルをコピーします。

   * 入力データ ファイル: [https://hditutorialdata.blob.core.windows.net/adfhiveactivity/inputdata/input.log](https://hditutorialdata.blob.core.windows.net/adfhiveactivity/inputdata/input.log)
   * HiveQL スクリプト: [https://hditutorialdata.blob.core.windows.net/adfhiveactivity/script/partitionweblogs.hql](https://hditutorialdata.blob.core.windows.net/adfhiveactivity/script/partitionweblogs.hql)

     どちらのファイルも、パブリック BLOB コンテナーに格納されます。


**Azure PowerShell を使用してストレージを準備し、ファイルをコピーするには:**
> [!IMPORTANT]
> スクリプトを使って作成する Azure リソース グループと Azure ストレージ アカウントの名前を指定します。
> スクリプトによって出力された**リソース グループ名**、**ストレージ アカウント名**、**ストレージ アカウント キー**を書き留めます。 これらは、次のセクションで必要になります。

```powershell
$resourceGroupName = "<Azure Resource Group Name>"
$storageAccountName = "<Azure Storage Account Name>"
$location = "East US 2"

$sourceStorageAccountName = "hditutorialdata"  
$sourceContainerName = "adfhiveactivity"

$destStorageAccountName = $storageAccountName
$destContainerName = "adfgetstarted" # don't change this value.

####################################
# Connect to Azure
####################################
#region - Connect to Azure subscription
Write-Host "`nConnecting to your Azure subscription ..." -ForegroundColor Green
try{Get-AzureRmContext}
catch{Login-AzureRmAccount}
#endregion

####################################
# Create a resource group, storage, and container
####################################

#region - create Azure resources
Write-Host "`nCreating resource group, storage account and blob container ..." -ForegroundColor Green

New-AzureRmResourceGroup -Name $resourceGroupName -Location $location
New-AzureRmStorageAccount `
    -ResourceGroupName $resourceGroupName `
    -Name $destStorageAccountName `
    -type Standard_LRS `
    -Location $location

$destStorageAccountKey = (Get-AzureRmStorageAccountKey `
    -ResourceGroupName $resourceGroupName `
    -Name $destStorageAccountName)[0].Value

$sourceContext = New-AzureStorageContext `
    -StorageAccountName $sourceStorageAccountName `
    -Anonymous
$destContext = New-AzureStorageContext `
    -StorageAccountName $destStorageAccountName `
    -StorageAccountKey $destStorageAccountKey

New-AzureStorageContainer -Name $destContainerName -Context $destContext
#endregion

####################################
# Copy files
####################################
#region - copy files
Write-Host "`nCopying files ..." -ForegroundColor Green

$blobs = Get-AzureStorageBlob `
    -Context $sourceContext `
    -Container $sourceContainerName

$blobs|Start-AzureStorageBlobCopy `
    -DestContext $destContext `
    -DestContainer $destContainerName

Write-Host "`nCopied files ..." -ForegroundColor Green
Get-AzureStorageBlob -Context $destContext -Container $destContainerName
#endregion

Write-host "`nYou will use the following values:" -ForegroundColor Green
write-host "`nResource group name: $resourceGroupName"
Write-host "Storage Account Name: $destStorageAccountName"
write-host "Storage Account Key: $destStorageAccountKey"

Write-host "`nScript completed" -ForegroundColor Green
```

この PowerShell スクリプトに関してサポートが必要な場合は、「[Azure Storage での Azure PowerShell の使用](../storage/storage-powershell-guide-full.md)」を参照してください。 Azure CLI を代わりに使用する場合は、Azure CLI スクリプトの「[付録](#appendix)」セクションを参照してください。

**ストレージ アカウントとその内容を確認するには**

1. [Azure ポータル](https://portal.azure.com)にサインオンします。
2. 左側のウィンドウの **[リソース グループ]** をクリックします。
3. PowerShell スクリプトで作成したリソース グループの名前をダブルクリックします。 一覧表示されるリソース グループが多すぎる場合は、フィルターを使用します。
4. **[リソース]** タイルには、リソース グループを他のプロジェクトと共有する場合を除き、リソースが 1 つだけ表示されています。 このリソースが、前の手順で指定した名前のストレージ アカウントです。 このストレージ アカウント名をクリックします。
5. **[BLOB]** タイルをクリックします。
6. **adfgetstarted** コンテナーをクリックします。 **inputdata** と **script** の 2 つのフォルダーが表示されます。 
7. フォルダーを開き、フォルダー内のファイルを確認します。 inputdata には入力データを含む input.log ファイルが含まれ、script フォルダーには HiveQL スクリプト ファイルが含まれています。 

## <a name="create-a-data-factory-using-resource-manager-template"></a>Resource Manager テンプレートを使用したデータ ファクトリの作成
ストレージ アカウント、入力データ、および HiveQL スクリプトの準備ができたら、いつでも Azure データ ファクトリを作成できます。 データ ファクトリを作成する方法はいくつかあります。 このチュートリアルでは、Azure Portal を使用して Azure Resource Manager テンプレートをデプロイすることで、データ ファクトリを作成します。 Resource Manager テンプレートは、[Azure CLI](../azure-resource-manager/resource-group-template-deploy-cli.md) と [Azure PowerShell](../azure-resource-manager/resource-group-template-deploy.md#deploy) を使用してデプロイすることもできます。 他のデータ ファクトリの作成方法については、「[Azure Data Factory を使ってみる](../data-factory/data-factory-build-your-first-pipeline.md)」を参照してください。

1. 次の画像をクリックして Azure にサインインし、Azure Portal で Resource Manager テンプレートを開きます。 テンプレートは https://hditutorialdata.blob.core.windows.net/adfhiveactivity/data-factory-hdinsight-on-demand.json にあります。 テンプレートで定義されるエンティティの詳細については、「[テンプレートの Data Factory エンティティ](#data-factory-entities-in-the-template)」セクションを参照してください。 

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fhditutorialdata.blob.core.windows.net%2Fadfhiveactivity%2Fdata-factory-hdinsight-on-demand.json" target="_blank"><img src="./media/hdinsight-hadoop-create-linux-clusters-adf/deploy-to-azure.png" alt="Deploy to Azure"></a>
2. **[リソース グループ]** 設定の **[既存のものを使用]** オプションを選択し、前の手順で (PowerShell スクリプトを使用して) 作成したリソース グループの名前を選択します。 
3. データ ファクトリの名前を入力します (**[データ ファクトリ名]**)。 この名前はグローバルに一意である必要があります。
4. 前の手順で書き留めた**ストレージ アカウント名**と**ストレージ アカウント キー**を入力します。
5. **使用条件**を読み、**[上記の使用条件に同意する]** をオンにします。
6. **[ダッシュボードにピン留めする]** オプションをオンにします。 
6. **[Purchase/Create (購入/作成)]** をクリックします。 ダッシュボードに "**テンプレートのデプロイのデプロイ中**" というタイルが表示されます。 対象のリソース グループの**リソース グループ** ブレードが開くまで待ちます。 また、対象のリソース グループの名前が付けられたタイルをクリックしてリソース グループ ブレードを開くこともできます。 
6. リソース グループ ブレードがまだ開いていない場合は、タイルをクリックしてリソース グループを開きます。 これで、ストレージ アカウント リソースのほかに、もう 1 つデータ ファクトリ リソースが表示されます。
7. データ ファクトリの名前 (**[データ ファクトリ名]** パラメーターに指定した値) をクリックします。
8. [Data Factory] ブレードで、**[ダイアグラム]** タイルをクリックします。 このダイアグラムには、1 つの入力データセットと 1 つの出力データセットを持つ 1 つのアクティビティが示されます。

    ![Azure Data Factory HDInsight on-demand Hive activity pipeline diagram](./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-adf-pipeline-diagram.png)

    名前は Resource Manager テンプレートで定義されます。
9. **[AzureBlobOutput]**をダブルクリックします。
10. **[最近更新されたスライス]**に、1 つのスライスが表示されます。 状態が **[処理中]** の場合は、**[準備完了]** に変わるまで待ちます。 通常、HDInsight クラスターの作成には約 **20 分**かかります。 

### <a name="check-the-data-factory-output"></a>データ ファクトリの出力を確認する

1. 前のセクションと同じ手順を使用して、adfgetstarted コンテナーのコンテナーを確認します。 **adfgetsarted**に加えて、次の 2 つの新しいコンテナーがあります。

   * `adf<yourdatafactoryname>-linkedservicename-datetimestamp` 形式の名前が付いたコンテナー。 このコンテナーは、HDInsight クラスターの既定のコンテナーです。 
   * adfjobs: このコンテナーは、ADF ジョブ ログのコンテナーです。

     データ ファクトリの出力は、Resource Manager テンプレートに構成されている **afgetstarted** に格納されます。
2. **[adfgetstarted]**をクリックします。
3. **[partitioneddata]** をダブルクリックします。 **year=2014** フォルダーが表示されます。これは、すべての Web ログの日付が 2014 年のものであるためです。

    ![Azure Data Factory HDInsight on-demand Hive activity pipeline output](./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-adf-output-year.png)

    一覧を展開すると、1 月、2 月、3 月に対応する 3 つのフォルダーが表示されます。 さらに、月ごとのログがあります。

    ![Azure Data Factory HDInsight on-demand Hive activity pipeline output](./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-adf-output-month.png)

## <a name="data-factory-entities-in-the-template"></a>テンプレートの Data Factory エンティティ
データ ファクトリの大まかな Resource Manager テンプレートは次のようになります。 

```json
{
    "contentVersion": "1.0.0.0",
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "parameters": { ...
    },
    "variables": { ...
    },
    "resources": [
        {
            "name": "[parameters('dataFactoryName')]",
            "apiVersion": "[variables('apiVersion')]",
            "type": "Microsoft.DataFactory/datafactories",
            "location": "westus",
            "resources": [
                { ... },
                { ... },
                { ... },
                { ... }
            ]
        }
    ]
}
```

### <a name="define-data-factory"></a>データ ファクトリの定義
次のサンプルに示されているように、Resource Manager テンプレートにデータ ファクトリを定義します。  

```json
"resources": [
{
    "name": "[parameters('dataFactoryName')]",
    "apiVersion": "[variables('apiVersion')]",
    "type": "Microsoft.DataFactory/datafactories",
    "location": "westus",
}
```
dataFactoryName は、テンプレートをデプロイするときに指定するデータ ファクトリの名前です。 データ ファクトリは現在、米国東部、米国西部、北ヨーロッパ リージョンでのみサポートされています。
   
### <a name="defining-entities-within-the-data-factory"></a>データ ファクトリ内のエンティティの定義
JSON テンプレートには、次の Data Factory エンティティが定義されています。 

* [Azure Storage のリンクされたサービス](#azure-storage-linked-service)
* [HDInsight のオンデマンドのリンクされたサービス](#hdinsight-on-demand-linked-service)
* [Azure BLOB の入力データセット](#azure-blob-input-dataset)
* [Azure BLOB の出力データセット](#azure-blob-output-dataset)
* [コピー アクティビティを含むデータ パイプライン](#data-pipeline)

#### <a name="azure-storage-linked-service"></a>Azure Storage のリンクされたサービス
Azure Storage のリンクされたサービスでは、Azure ストレージ アカウントをデータ ファクトリにリンクします。 このチュートリアルでは、同じストレージ アカウントが、既定の HDInsight ストレージ アカウント、入力データ ストレージ、出力データ ストレージとして使用されます。 そのため、Azure Storage のリンクされたサービスを 1 つだけ定義します。 リンクされたサービスの定義では、Azure ストレージ アカウントの名前とキーを指定します。 Azure Storage のリンクされたサービスの定義に使用する JSON プロパティの詳細については、「[Azure Storage のリンクされたサービス](../data-factory/data-factory-azure-blob-connector.md#azure-storage-linked-service)」をご覧ください。 

```json
{
    "name": "[variables('storageLinkedServiceName')]",
    "type": "linkedservices",
    "dependsOn": [ "[concat('Microsoft.DataFactory/dataFactories/', parameters('dataFactoryName'))]" ],
    "apiVersion": "[variables('apiVersion')]",
    "properties": {
        "type": "AzureStorage",
        "typeProperties": {
            "connectionString": "[concat('DefaultEndpointsProtocol=https;AccountName=',parameters('storageAccountName'),';AccountKey=',parameters('storageAccountKey'))]"
        }
    }
}
```
**connectionString** では、storageAccountName パラメーターと storageAccountKey パラメーターを使用しています。 これらのパラメーターの値は、テンプレートをデプロイするときに指定します。  

#### <a name="hdinsight-on-demand-linked-service"></a>HDInsight のオンデマンドのリンクされたサービス
HDInsight のオンデマンドのリンクされたサービスの定義で、実行時に HDInsight Hadoop クラスターを作成するために Data Factory サービスによって使用される構成パラメーターの値を指定します。 HDInsight のオンデマンドのリンクされたサービスを定義するときに使用する JSON プロパティの詳細については、「[コンピューティングのリンクされたサービス](../data-factory/data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service)」を参照してください。  

```json

{
    "type": "linkedservices",
    "name": "[variables('hdInsightOnDemandLinkedServiceName')]",
    "dependsOn": [
        "[concat('Microsoft.DataFactory/dataFactories/', parameters('dataFactoryName'))]",
        "[concat('Microsoft.DataFactory/dataFactories/', parameters('dataFactoryName'), '/linkedservices/', variables('storageLinkedServiceName'))]"
    ],
    "apiVersion": "[variables('apiVersion')]",
    "properties": {
        "type": "HDInsightOnDemand",
        "typeProperties": {
            "osType": "linux",
            "version": "3.2",
            "clusterSize": 1,
            "sshUserName": "myuser",                            
            "sshPassword": "MyPassword!",
            "timeToLive": "00:30:00",
            "linkedServiceName": "[variables('storageLinkedServiceName')]"
        }
    }
}
```
以下の点に注意してください。 

* Data Factory によって、**Linux ベース**の HDInsight クラスターが自動的に作成されます。
* HDInsight Hadoop クラスターは、ストレージ アカウントと同じリージョンに作成されます。
* *timeToLive* 設定に注目してください。 データ ファクトリは、クラスターがアイドル状態になってから 30 分経過するとそのクラスターを自動的に削除します。
* HDInsight クラスターは、JSON (**linkedServiceName**) で指定した BLOB ストレージに**既定のコンテナー**を作成します。 クラスターを削除しても、HDInsight はこのコンテナーを削除しません。 この動作は仕様です。 オンデマンド HDInsight のリンクされたサービスでは、既存のライブ クラスター (**timeToLive**) がある場合を除き、スライスを処理する必要があるたびに HDInsight クラスターが作成され、処理が終了すると削除されます。

詳細については、 [オンデマンド HDInsight のリンクされたサービス](../data-factory/data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service) に関するセクションを参照してください。

> [!IMPORTANT]
> 処理されるスライスが多いほど、Azure BLOB ストレージ内のコンテナーも増えます。 ジョブのトラブルシューティングのためにコンテナーが必要ない場合、コンテナーを削除してストレージ コストを削減できます。 これらのコンテナーの名前は、"adf**データ ファクトリ名**-**リンクされたサービス名**-日時スタンプ" というパターンに従います。 Azure BLOB ストレージ内のコンテナーを削除するには、 [Microsoft ストレージ エクスプローラー](http://storageexplorer.com/) などのツールを使用します。

#### <a name="azure-blob-input-dataset"></a>Azure BLOB の入力データセット
入力データセット定義では、入力データを格納する BLOB コンテナー、フォルダー、ファイルの名前を指定します。 Azure BLOB データセットの定義に使用する JSON プロパティの詳細については、[Azure BLOB データセットのプロパティ](../data-factory/data-factory-azure-blob-connector.md#dataset-properties)に関するセクションをご覧ください。 

```json

{
    "type": "datasets",
    "name": "[variables('blobInputDatasetName')]",
    "dependsOn": [
        "[concat('Microsoft.DataFactory/dataFactories/', parameters('dataFactoryName'))]",
        "[concat('Microsoft.DataFactory/dataFactories/', parameters('dataFactoryName'), '/linkedServices/', variables('storageLinkedServiceName'))]"
    ],
    "apiVersion": "[variables('apiVersion')]",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "[variables('storageLinkedServiceName')]",
        "typeProperties": {
            "fileName": "input.log",
            "folderPath": "adfgetstarted/inputdata",
            "format": {
                "type": "TextFormat",
                "columnDelimiter": ","
            }
        },
        "availability": {
            "frequency": "Month",
            "interval": 1
        },
        "external": true,
        "policy": {}
    }
}

```

JSON 定義内の次の設定に注目してください。 

```json
"fileName": "input.log",
"folderPath": "adfgetstarted/inputdata",
```

#### <a name="azure-blob-output-dataset"></a>Azure BLOB の出力データセット
出力データセット定義では、出力データを格納する BLOB コンテナーとフォルダーの名前を指定します。 Azure BLOB データセットの定義に使用する JSON プロパティの詳細については、[Azure BLOB データセットのプロパティ](../data-factory/data-factory-azure-blob-connector.md#dataset-properties)に関するセクションをご覧ください。  

```json

{
    "type": "datasets",
    "name": "[variables('blobOutputDatasetName')]",
    "dependsOn": [
        "[concat('Microsoft.DataFactory/dataFactories/', parameters('dataFactoryName'))]",
        "[concat('Microsoft.DataFactory/dataFactories/', parameters('dataFactoryName'), '/linkedServices/', variables('storageLinkedServiceName'))]"
    ],
    "apiVersion": "[variables('apiVersion')]",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "[variables('storageLinkedServiceName')]",
        "typeProperties": {
            "folderPath": "adfgetstarted/partitioneddata",
            "format": {
                "type": "TextFormat",
                "columnDelimiter": ","
            }
        },
        "availability": {
            "frequency": "Month",
            "interval": 1,
            "style": "EndOfInterval"
        }
    }
}
```

folderPath は、出力データを格納するフォルダーへのパスを指定します。 

```json
"folderPath": "adfgetstarted/partitioneddata",
```

[データセットの可用性](../data-factory/data-factory-create-datasets.md#Availability) の設定は次のとおりです。

```json
"availability": {
    "frequency": "Month",
    "interval": 1,
    "style": "EndOfInterval"
},
```

Azure Data Factory では、出力データセットの可用性によってパイプラインが動作するようになります。 この例では、毎月の最終日 (EndOfInterval) にスライスが生成されます。 詳細については、「[Data Factory を使用したスケジュール設定と実行](../data-factory/data-factory-scheduling-and-execution.md)」を参照してください。

#### <a name="data-pipeline"></a>データ パイプライン
オンデマンドの Azure HDInsight クラスターで Hive スクリプトを実行してデータを変換するパイプラインを定義します。 この例のパイプラインの定義に使用されている JSON 要素については、「[パイプライン JSON](../data-factory/data-factory-create-pipelines.md#pipeline-json)」をご覧ください。 

```json
{
    "type": "datapipelines",
    "name": "[parameters('dataFactoryName')]",
    "dependsOn": [
        "[concat('Microsoft.DataFactory/dataFactories/', parameters('dataFactoryName'))]",
        "[concat('Microsoft.DataFactory/dataFactories/', parameters('dataFactoryName'), '/linkedServices/', variables('storageLinkedServiceName'))]",
        "[concat('Microsoft.DataFactory/dataFactories/', parameters('dataFactoryName'), '/linkedServices/', variables('hdInsightOnDemandLinkedServiceName'))]",
        "[concat('Microsoft.DataFactory/dataFactories/', parameters('dataFactoryName'), '/datasets/', variables('blobInputDatasetName'))]",
        "[concat('Microsoft.DataFactory/dataFactories/', parameters('dataFactoryName'), '/datasets/', variables('blobOutputDatasetName'))]"
    ],
    "apiVersion": "[variables('apiVersion')]",
    "properties": {
        "description": "Azure Data Factory pipeline with an Hadoop Hive activity",
        "activities": [
            {
                "type": "HDInsightHive",
                "typeProperties": {
                    "scriptPath": "adfgetstarted/script/partitionweblogs.hql",
                    "scriptLinkedService": "[variables('storageLinkedServiceName')]",
                    "defines": {
                        "inputtable": "[concat('wasb://adfgetstarted@', parameters('storageAccountName'), '.blob.core.windows.net/inputdata')]",
                        "partitionedtable": "[concat('wasb://adfgetstarted@', parameters('storageAccountName'), '.blob.core.windows.net/partitioneddata')]"
                    }
                },
                "inputs": [
                    {
                        "name": "AzureBlobInput"
                    }
                ],
                "outputs": [
                    {
                        "name": "AzureBlobOutput"
                    }
                ],
                "policy": {
                    "concurrency": 1,
                    "retry": 3
                },
                "name": "RunSampleHiveActivity",
                "linkedServiceName": "HDInsightOnDemandLinkedService"
            }
        ],
        "start": "2016-01-01T00:00:00Z",
        "end": "2016-01-31T00:00:00Z",
        "isPaused": false
    }
}
```

このパイプラインには、1 つのアクティビティ (HDInsightHive アクティビティ) が含まれています。 開始日と終了日の両方が 2016 年 1 月の日付であるため、この 1 か月のデータ (スライス) のみが処理されます。 アクティビティの *start* と *end* の両方に過去の日付が指定されているため、Data Factory によってこの月のデータが即座に処理されます。 end に将来の日付が設定されている場合は、その日付にデータ ファクトリが新たなスライスを作成します。 詳細については、「[Data Factory を使用したスケジュール設定と実行](../data-factory/data-factory-scheduling-and-execution.md)」を参照してください。

## <a name="clean-up-the-tutorial"></a>このチュートリアルの仕上げ

### <a name="delete-the-blob-containers-created-by-on-demand-hdinsight-cluster"></a>オンデマンドの HDInsight クラスターによって作成された BLOB コンテナーを削除する
オンデマンド HDInsight のリンクされたサービスでは、既存のライブ クラスター (timeToLive) がある場合を除き、スライスを処理する必要があるたびに HDInsight クラスターが作成され、処理が終了するとそのクラスターが削除されます。 Azure Data Factory は、クラスターごとに、クラスターの既定のストレージ アカウントとして使用される Azure BLOB ストレージ内に BLOB コンテナーを作成します。 HDInsight クラスターが削除されても、既定の BLOB ストレージ コンテナーとそれに関連付けられたストレージ アカウントは削除されません。 この動作は仕様です。 処理されるスライスが多いほど、Azure BLOB ストレージ内のコンテナーも増えます。 ジョブのトラブルシューティングのためにコンテナーが必要ない場合、コンテナーを削除してストレージ コストを削減できます。 これらのコンテナーの名前は、`adfyourdatafactoryname-linkedservicename-datetimestamp` 形式になります。

**adfjobs** フォルダーと **adfyourdatafactoryname-linkedservicename-datetimestamp** フォルダーを削除します。 adfjobs コンテナーには、Azure Data Factory からのジョブのログが含まれています。 

### <a name="delete-the-resource-group"></a>リソース グループを削除します
[Azure Resource Manager](../azure-resource-manager/resource-group-overview.md) は、ソリューションをグループとしてデプロイ、管理、監視するために使用します。  リソース グループを削除すると、そのグループ内のコンポーネントがすべて削除されます。  

1. [Azure ポータル](https://portal.azure.com)にサインオンします。
2. 左側のウィンドウの **[リソース グループ]** をクリックします。
3. PowerShell スクリプトで作成したリソース グループの名前をクリックします。 一覧表示されるリソース グループが多すぎる場合は、フィルターを使用します。 新しいブレードでリソース グループが開かれます。
4. **[リソース]** タイルには、リソース グループを他のプロジェクトと共有する場合を除き、既定のストレージ アカウントとデータ ファクトリが表示されます。
5. ブレードの上部で **[削除]** をクリックします。 この操作を実行すると、ストレージ アカウントと、そのストレージ アカウントに格納されているデータが削除されます。
6. リソース グループ名を入力して削除を確認し、**[削除]** をクリックします。

リソース グループを削除するときにストレージ アカウントを削除したくない場合は、ビジネス データを既定のストレージ アカウントから切り離した、次のアーキテクチャを検討してください。 この場合、ビジネス データを含むストレージ アカウント用のリソース グループのほかに、HDInsight のリンクされたサービス用の既定のストレージ アカウントとデータ ファクトリ用のリソース グループを用意することになります。 2 番目のリソース グループを削除しても、ビジネス データのストレージ アカウントには影響しません。 そのためには、次の手順を実行します。

* Resource Manager テンプレートの最上位レベルのリソース グループに Microsoft.DataFactory/datafactories リソースと共に次の内容を追加します。 これにより、ストレージ アカウントが作成されます。

    ```json
    {
        "name": "[parameters('defaultStorageAccountName')]",
        "type": "Microsoft.Storage/storageAccounts",
        "location": "[parameters('location')]",
        "apiVersion": "[variables('defaultApiVersion')]",
        "dependsOn": [ ],
        "tags": {

        },
        "properties": {
            "accountType": "Standard_LRS"
        }
    },
    ```
* 新しいストレージ アカウントに新しいリンクされたサービス ポイントを追加します。

    ```json
    {
        "dependsOn": [ "[concat('Microsoft.DataFactory/dataFactories/', parameters('dataFactoryName'))]" ],
        "type": "linkedservices",
        "name": "[variables('defaultStorageLinkedServiceName')]",
        "apiVersion": "[variables('apiVersion')]",
        "properties": {
            "type": "AzureStorage",
            "typeProperties": {
                "connectionString": "[concat('DefaultEndpointsProtocol=https;AccountName=',parameters('defaultStorageAccountName'),';AccountKey=',listKeys(resourceId('Microsoft.Storage/storageAccounts', variables('defaultStorageAccountName')), variables('defaultApiVersion')).key1)]"
            }
        }
    },
    ```
* 追加の dependsOn と additionalLinkedServiceNames を使用して、HDInsight のオンデマンドのリンクされたサービスを構成します。

    ```json
    {
        "dependsOn": [
            "[concat('Microsoft.DataFactory/dataFactories/', parameters('dataFactoryName'))]",
            "[concat('Microsoft.DataFactory/dataFactories/', parameters('dataFactoryName'), '/linkedservices/', variables('defaultStorageLinkedServiceName'))]",
            "[concat('Microsoft.DataFactory/dataFactories/', parameters('dataFactoryName'), '/linkedservices/', variables('storageLinkedServiceName'))]"

        ],
        "type": "linkedservices",
        "name": "[variables('hdInsightOnDemandLinkedServiceName')]",
        "apiVersion": "[variables('apiVersion')]",
        "properties": {
            "type": "HDInsightOnDemand",
            "typeProperties": {
                "osType": "linux",
                "version": "3.2",
                "clusterSize": 1,
                "sshUserName": "myuser",                            
                "sshPassword": "MyPassword!",
                "timeToLive": "00:30:00",
                "linkedServiceName": "[variables('storageLinkedServiceName')]",
                "additionalLinkedServiceNames": "[variables('defaultStorageLinkedServiceName')]"
            }
        }
    },            
    ```
## <a name="next-steps"></a>次のステップ
この記事では、Azure Data Factory を使用して Hive ジョブを処理するオンデマンドの HDInsight クラスターを作成する方法について説明しました。 詳細については、以下のリンク先を参照してください。

* [Hadoop チュートリアル: HDInsight で Linux ベースの Hadoop を使用する](hdinsight-hadoop-linux-tutorial-get-started.md)
* [HDInsight での Linux ベースの Hadoop クラスターの作成](hdinsight-hadoop-provision-linux-clusters.md)
* [HDInsight のドキュメント](https://azure.microsoft.com/documentation/services/hdinsight/)
* [データ ファクトリのドキュメント](https://azure.microsoft.com/documentation/services/data-factory/)

## <a name="appendix"></a>付録

### <a name="azure-cli-script"></a>Azure CLI スクリプト
Azure PowerShell を使用する代わりに、Azure CLI を使用してチュートリアルを実行できます。 Azure CLI を使用するには、最初に次の手順に従って Azure CLI をインストールします。[!INCLUDE [use-latest-version](../../includes/hdinsight-use-latest-cli.md)]

#### <a name="use-azure-cli-to-prepare-the-storage-and-copy-the-files"></a>Azure CLI を使用してストレージを準備し、ファイルをコピーする

```
azure login

azure config mode arm

azure group create --name "<Azure Resource Group Name>" --location "East US 2"

azure storage account create --resource-group "<Azure Resource Group Name>" --location "East US 2" --type "LRS" <Azure Storage Account Name>

azure storage account keys list --resource-group "<Azure Resource Group Name>" "<Azure Storage Account Name>"
azure storage container create "adfgetstarted" --account-name "<Azure Storage AccountName>" --account-key "<Azure Storage Account Key>"

azure storage blob copy start "https://hditutorialdata.blob.core.windows.net/adfhiveactivity/inputdata/input.log" --dest-account-name "<Azure Storage Account Name>" --dest-account-key "<Azure Storage Account Key>" --dest-container "adfgetstarted"
azure storage blob copy start "https://hditutorialdata.blob.core.windows.net/adfhiveactivity/script/partitionweblogs.hql" --dest-account-name "<Azure Storage Account Name>" --dest-account-key "<Azure Storage Account Key>" --dest-container "adfgetstarted"
```

コンテナー名は *adfgetstarted*です。 この名前はそのままにしておいてください。 そうしないと、Resource Manager テンプレートの更新が必要になります。 この CLI スクリプトに関してサポートが必要な場合は、「[Azure Storage での Azure CLI の使用](../storage/storage-azure-cli.md)」を参照してください。


