---
title: "Azure PowerShell を使用する Azure Data Lake Analytics の管理 | Microsoft Docs"
description: "Data Lake Analytics アカウント、データ ソース、ジョブ、カタログ項目を管理する方法について説明します。 "
services: data-lake-analytics
documentationcenter: 
author: matt1883
manager: jhubbard
editor: cgronlun
ms.assetid: ad14d53c-fed4-478d-ab4b-6d2e14ff2097
ms.service: data-lake-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 07/23/2017
ms.author: mahi
ms.openlocfilehash: 65bf5928428b21e98c893a9de8ca596329329411
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="manage-azure-data-lake-analytics-using-azure-powershell"></a>Azure PowerShell を使用する Azure Data Lake Analytics の管理
[!INCLUDE [manage-selector](../../includes/data-lake-analytics-selector-manage.md)]

Azure PowerShell を使用して、Azure Data Lake Analytics のアカウント、データ ソース、ジョブ、カタログ項目を管理する方法について説明します。 

## <a name="prerequisites"></a>前提条件

Data Lake Analytics アカウントを作成するには、次の項目を把握している必要があります。

* **サブスクリプション ID**: Data Lake Analytics アカウントが存在する Azure サブスクリプション ID。
* **リソース グループ**: Data Lake Analytics アカウントが含まれている Azure リソース グループの名前。
* **Data Lake Analytics アカウント名**: アカウント名には小文字と数字のみを含める必要があります。
* **既定の Data Lake Store アカウント**: 各 Data Lake Analytics アカウントには既定の Data Lake Store アカウントがあります。 これらのアカウントは同じ場所に存在する必要があります。
* **場所**: Data Lake Analytics アカウントの場所。"米国東部 2" やサポートされているその他の場所です。 サポートされている場所は、[価格ページ](https://azure.microsoft.com/pricing/details/data-lake-analytics/)で確認できます。

このチュートリアルの PowerShell スニペットでは、以下の変数を使って各情報を格納します。

```powershell
$subId = "<SubscriptionId>"
$rg = "<ResourceGroupName>"
$adla = "<DataLakeAnalyticsAccountName>"
$adls = "<DataLakeStoreAccountName>"
$location = "<Location>"
```

## <a name="log-in"></a>ログイン

サブスクリプション ID を使用してログインします。

```powershell
Login-AzureRmAccount -SubscriptionId $subId
```

サブスクリプション名を使用してログインします。

```
Login-AzureRmAccount -SubscriptionName $subname 
```

`Login-AzureRmAccount` コマンドレットは、常に資格情報を要求します。 次のコマンドレットを使用すると、要求を回避できます。

```powershell
# Save login session information
Save-AzureRmProfile -Path D:\profile.json  

# Load login session information
Select-AzureRmProfile -Path D:\profile.json 
```

## <a name="managing-accounts"></a>アカウントの管理

### <a name="create-a-data-lake-analytics-account"></a>Data Lake Analytics アカウントを作成する

使用する[リソース グループ](../azure-resource-manager/resource-group-overview.md#resource-groups)がまだ存在しない場合は、新たに作成します。 

```powershell
New-AzureRmResourceGroup -Name  $rg -Location $location
```

すべての Data Lake Analytics アカウントに、ログの保存を目的とした既定の Data Lake Store アカウントが必要です。 既存のアカウントを利用することも、アカウントを作成することもできます。 

```powershell
New-AdlStore -ResourceGroupName $rg -Name $adls -Location $location
```

リソース グループと Data Lake Store アカウントが用意できたら、Data Lake Analytics アカウントを作成します。

```powershell
New-AdlAnalyticsAccount -ResourceGroupName $rg -Name $adla -Location $location -DefaultDataLake $adls
```

### <a name="get-information-about-an-account"></a>アカウントに関する情報を取得する

アカウントの詳細を取得します。

```powershell
Get-AdlAnalyticsAccount -Name $adla
```

特定の Data Lake Analytics アカウントの存在を確認します。 コマンドレットは、`True` または `False` のどちらかを返します。

```powershell
Test-AdlAnalyticsAccount -Name $adla
```

特定の Data Lake Store アカウントの存在を確認します。 コマンドレットは、`True` または `False` のどちらかを返します。

```powershell
Test-AdlStoreAccount -Name $adls
```

### <a name="listing-accounts"></a>アカウントを一覧表示する

現在のサブスクリプション内の Data Lake Analytics アカウントを一覧表示します。

```powershell
Get-AdlAnalyticsAccount
```

特定のリソース グループ内の Data Lake Analytics アカウントを一覧表示します。

```powershell
Get-AdlAnalyticsAccount -ResourceGroupName $rg
```

## <a name="managing-firewall-rules"></a>ファイアウォール規則の管理

ファイアウォール規則を一覧表示します。

```powershell
Get-AdlAnalyticsFirewallRule -Account $adla
```

ファイアウォール規則を追加します。

```powershell
$ruleName = "Allow access from on-prem server"
$startIpAddress = "<start IP address>"
$endIpAddress = "<end IP address>"

Add-AdlAnalyticsFirewallRule -Account $adla -Name $ruleName -StartIpAddress $startIpAddress -EndIpAddress $endIpAddress
```

ファイアウォール規則を変更します。

```powershell
Set-AdlAnalyticsFirewallRule -Account $adla -Name $ruleName -StartIpAddress $startIpAddress -EndIpAddress $endIpAddress
```

ファイアウォール規則を削除します。

```powershell
Remove-AdlAnalyticsFirewallRule -Account $adla -Name $ruleName
```



Azure の IP アドレスを許可します。

```powershell
Set-AdlAnalyticsAccount -Name $adla -AllowAzureIpState Enabled
```

```powershell
Set-AdlAnalyticsAccount -Name $adla -FirewallState Enabled
Set-AdlAnalyticsAccount -Name $adla -FirewallState Disabled
```

## <a name="managing-data-sources"></a>データ ソースの管理
Azure Data Lake Analytics では現在、以下のデータ ソースがサポートされています。

* [Azure Data Lake Store](../data-lake-store/data-lake-store-overview.md)
* [Azure Storage](../storage/common/storage-introduction.md)

Analytics アカウントを作成する際には、既定のデータ ソースとして Data Lake Store アカウントを指定する必要があります。 既定の Data Lake Store アカウントは、ジョブ メタデータとジョブ監査ログの格納に使用されます。 Data Lake Analytics アカウントを作成した後で、Data Lake Store アカウントやストレージ アカウントを追加できます。 

### <a name="find-the-default-data-lake-store-account"></a>既定の Data Lake Store アカウントの検索

```powershell
$adla_acct = Get-AdlAnalyticsAccount -Name $adla
$dataLakeStoreName = $adla_acct.DefaultDataLakeAccount
```

データソースの一覧を `IsDefault` プロパティでフィルター処理して既定の Data Lake Store アカウントを検索できます。

```powershell
Get-AdlAnalyticsDataSource -Account $adla  | ? { $_.IsDefault } 
```

### <a name="add-a-data-source"></a>データ ソースの追加

```powershell

# Add an additional Storage (Blob) account.
$AzureStorageAccountName = "<AzureStorageAccountName>"
$AzureStorageAccountKey = "<AzureStorageAccountKey>"
Add-AdlAnalyticsDataSource -Account $adla -Blob $AzureStorageAccountName -AccessKey $AzureStorageAccountKey

# Add an additional Data Lake Store account.
$AzureDataLakeStoreName = "<AzureDataLakeStoreAccountName"
Add-AdlAnalyticsDataSource -Account $adla -DataLakeStore $AzureDataLakeStoreName 
```

### <a name="list-data-sources"></a>データ ソースの一覧表示

```powershell
# List all the data sources
Get-AdlAnalyticsDataSource -Name $adla

# List attached Data Lake Store accounts
Get-AdlAnalyticsDataSource -Name $adla | where -Property Type -EQ "DataLakeStore"

# List attached Storage accounts
Get-AdlAnalyticsDataSource -Name $adla | where -Property Type -EQ "Blob"
```

## <a name="submit-u-sql-jobs"></a>U-SQL ジョブを送信する

### <a name="submit-a-string-as-a-u-sql-script"></a>U-SQL スクリプトとして文字列を送信する

```powershell
$script = @"
@a  = 
    SELECT * FROM 
        (VALUES
            ("Contoso", 1500.0),
            ("Woodgrove", 2700.0)
        ) AS D( customer, amount );
OUTPUT @a
    TO "/data.csv"
    USING Outputters.Csv();
"@

$scriptpath = "d:\test.usql"
$script | Out-File $scriptpath 

Submit-AdlJob -AccountName $adla -Script $script -Name "Demo"
```


### <a name="submit-a-file-as-a-u-sql-script"></a>U-SQL スクリプトとしてファイルを送信する

```powershell
$scriptpath = "d:\test.usql"
$script | Out-File $scriptpath 
Submit-AdlJob -AccountName $adla –ScriptPath $scriptpath -Name "Demo"
```

## <a name="list-jobs-in-an-account"></a>アカウントのジョブを一覧表示する

### <a name="list-all-the-jobs-in-the-account"></a>アカウントに存在するすべてのジョブを一覧表示します。 

出力結果には、現在実行されているジョブと最近完了したジョブが含まれます。

```powershell
Get-AdlJob -Account $adla
```


### <a name="list-a-specific-number-of-jobs"></a>特定の数のジョブを一覧表示します。

既定ではジョブの一覧は送信時に並べ替えられます。 したがって最近送信されたジョブは、先頭に表示されます。 既定では、ADLA アカウントはジョブを 180 日間記憶していますが、Ge-AdlJob コマンドレットは既定で、最初の 500 件のみを返します。 Top パラメーターを使用して、特定の数のジョブを一覧表示します。

```powershell
$jobs = Get-AdlJob -Account $adla -Top 10
```


### <a name="list-jobs-based-on-the-value-of-job-property"></a>ジョブ プロパティの値に基づいてジョブを一覧表示します。

`-State` パラメーターの使用 下記の値を任意に組み合わせることができます。

* `Accepted`
* `Compiling`
* `Ended`
* `New`
* `Paused`
* `Queued`
* `Running`
* `Scheduling`
* `Start`

```powershell
# List the running jobs
Get-AdlJob -Account $adla -State Running

# List the jobs that have completed
Get-AdlJob -Account $adla -State Ended

# List the jobs that have not started yet
Get-AdlJob -Account $adla -State Accepted,Compiling,New,Paused,Scheduling,Start
```

`-Result` パラメーターを使って、終了したジョブが正常完了したかどうかを検出します。 次の値があります。

* キャンセル
* Failed
* なし
* Succeeded

``` powershell
# List Successful jobs.
Get-AdlJob -Account $adla -State Ended -Result Succeeded

# List Failed jobs.
Get-AdlJob -Account $adla -State Ended -Result Failed
```


`-Submitter` パラメーターを使うと、ジョブを送信したユーザーを特定できます。

```powershell
Get-AdlJob -Account $adla -Submitter "joe@contoso.com"
```

`-SubmittedAfter` はフィルタ処理して時間の範囲を特定する際に役に立ちます。


```powershell
# List  jobs submitted in the last day.
$d = [DateTime]::Now.AddDays(-1)
Get-AdlJob -Account $adla -SubmittedAfter $d

# List  jobs submitted in the last seven day.
$d = [DateTime]::Now.AddDays(-7)
Get-AdlJob -Account $adla -SubmittedAfter $d
```

### <a name="analyzing-job-history"></a>ジョブ履歴の分析

Azure PowerShell を使用して、Data Lake Analytics で実行されたジョブ履歴を分析することは、効果的な手法です。 Azure PowerShell を使って使用状況とコストに関する情報を把握することができます。 詳細については、[ジョブ履歴分析のサンプル レポジトリ](https://github.com/Azure-Samples/data-lake-analytics-powershell-job-history-analysis)を参照してください。  

## <a name="get-information-about-pipelines-and-recurrences"></a>パイプラインと繰り返しについての情報を取得する

`Get-AdlJobPipeline` コマンドレットを使って前に送信したジョブのパイプライン情報を確認します。

```powershell
$pipelines = Get-AdlJobPipeline -Account $adla

$pipeline = Get-AdlJobPipeline -Account $adla -PipelineId "<pipeline ID>"
```

`Get-AdlJobRecurrence` コマンドレットを使って前に送信したジョブの繰り返し情報を確認します。

```powershell
$recurrences = Get-AdlJobRecurrence -Account $adla

$recurrence = Get-AdlJobRecurrence -Account $adla -RecurrenceId "<recurrence ID>"
```

## <a name="get-information-about-a-job"></a>ジョブに関する情報の取得

### <a name="get-job-status"></a>ジョブの状態の取得

特定のジョブの状態を取得します。

```powershell
Get-AdlJob -AccountName $adla -JobId $job.JobId
```

### <a name="examine-the-job-outputs"></a>ジョブ出力を確認

ジョブの終了後、フォルダー内のファイルを一覧表示して、出力ファイルが存在するかどうかを確認します。

```powershell
Get-AdlStoreChildItem -Account $adls -Path "/"
```

## <a name="manage-running-jobs"></a>実行中のジョブの管理

### <a name="cancel-a-job"></a>ジョブを取り消す

```powershell
Stop-AdlJob -Account $adls -JobID $jobID
```

### <a name="wait-for-a-job-to-finish"></a>ジョブが終了するまで待機する

ジョブが完了するまで `Get-AdlAnalyticsJob` を繰り返す替わりに、`Wait-AdlJob` コマンドレットを使用してジョブの終了を待機できます。

```powershell
Wait-AdlJob -Account $adla -JobId $job.JobId
```

## <a name="manage-compute-policies"></a>コンピューティング ポリシーを管理する

### <a name="list-existing-compute-policies"></a>既存のコンピューティング ポリシーを一覧表示する

`Get-AdlAnalyticsComputePolicy` コマンドレットは、Data Lake Analytics アカウントのコンピューティング ポリシーの情報を取得します。

```powershell
$policies = Get-AdlAnalyticsComputePolicy -Account $adla
```

### <a name="create-a-compute-policy"></a>コンピューティング ポリシーを作成する

`New-AdlAnalyticsComputePolicy` コマンドレットは、Data Lake Analytics アカウントの新しいコンピューティング ポリシーを作成します。 この例では、指定したユーザーが使用可能な最大 AU を 50、最小のジョブ優先順位 を 250 に設定します。

```powershell
$userObjectId = (Get-AzureRmAdUser -SearchString "garymcdaniel@contoso.com").Id

New-AdlAnalyticsComputePolicy -Account $adla -Name "GaryMcDaniel" -ObjectId $objectId -ObjectType User -MaxDegreeOfParallelismPerJob 50 -MinPriorityPerJob 250
```

## <a name="check-for-the-existence-of-a-file"></a>ファイルの存在を確認します。

```powershell
Test-AdlStoreItem -Account $adls -Path "/data.csv"
```

## <a name="uploading-and-downloading"></a>アップロードとダウンロード

ファイルをアップロードします。

```powershell
Import-AdlStoreItem -AccountName $adls -Path "c:\data.tsv" -Destination "/data_copy.csv" 
```

フォルダー全体を再帰的にアップロードします。

```powershell
Import-AdlStoreItem -AccountName $adls -Path "c:\myData\" -Destination "/myData/" -Recurse
```

ファイルをダウンロードします。

```powershell
Export-AdlStoreItem -AccountName $adls -Path "/data.csv" -Destination "c:\data.csv"
```

フォルダー全体を再帰的にダウンロードします。

```powershell
Export-AdlStoreItem -AccountName $adls -Path "/" -Destination "c:\myData\" -Recurse
```

> [!NOTE]
> アップロードまたはダウンロードのプロセスが中断された場合は、``-Resume`` フラグでコマンドレットを再度実行して、プロセスの再開を試みることができます。

## <a name="manage-catalog-items"></a>カタログ項目の管理

U-SQL カタログを使用して、U-SQL スクリプトで共有できるように、データとコードを構成します。 カタログでは、Azure Data Lake のデータを使用して可能な限り最高のパフォーマンスを実現できます。 詳細については、「 [U-SQL カタログの使用](data-lake-analytics-use-u-sql-catalog.md)」を参照してください。

### <a name="list-items-in-the-u-sql-catalog"></a>U-SQL カタログ内の項目を一覧表示する

```powershell
# List U-SQL databases
Get-AdlCatalogItem -Account $adla -ItemType Database 

# List tables within a database
Get-AdlCatalogItem -Account $adla -ItemType Table -Path "database"

# List tables within a schema.
Get-AdlCatalogItem -Account $adla -ItemType Table -Path "database.schema"
```

ADLA アカウント内すべてのデータベースのすべてのアセンブリを一覧表示します。

```powershell
$dbs = Get-AdlCatalogItem -Account $adla -ItemType Database

foreach ($db in $dbs)
{
    $asms = Get-AdlCatalogItem -Account $adla -ItemType Assembly -Path $db.Name

    foreach ($asm in $asms)
    {
        $asmname = "[" + $db.Name + "].[" + $asm.Name + "]"
        Write-Host $asmname
    }
}
```

### <a name="get-details-about-a-catalog-item"></a>カタログ項目に関する詳細を取得

```powershell
# Get details of a table
Get-AdlCatalogItem  -Account $adla -ItemType Table -Path "master.dbo.mytable"

# Test existence of a U-SQL database.
Test-AdlCatalogItem  -Account $adla -ItemType Database -Path "master"
```

### <a name="create-credentials-in-a-catalog"></a>カタログ内の資格情報を作成

U-SQL データベース内で、Azure でホストされているデータベースの資格情報オブジェクトを作成します。 現時点で U-SQL 資格情報は、PowerShell を使用して作成できるカタログ項目の唯一の種類です。

```powershell
$dbName = "master"
$credentialName = "ContosoDbCreds"
$dbUri = "https://contoso.database.windows.net:8080"

New-AdlCatalogCredential -AccountName $adla `
          -DatabaseName $db `
          -CredentialName $credentialName `
          -Credential (Get-Credential) `
          -Uri $dbUri
```

### <a name="get-basic-information-about-an-adla-account"></a>ADLA アカウントに関する基本情報を取得

アカウント名を指定すると、次のコードを使用してアカウントの基本情報を検索できます

```
$adla_acct = Get-AdlAnalyticsAccount -Name "saveenrdemoadla"
$adla_name = $adla_acct.Name
$adla_subid = $adla_acct.Id.Split("/")[2]
$adla_sub = Get-AzureRmSubscription -SubscriptionId $adla_subid
$adla_subname = $adla_sub.Name
$adla_defadls_datasource = Get-AdlAnalyticsDataSource -Account $adla_name  | ? { $_.IsDefault } 
$adla_defadlsname = $adla_defadls_datasource.Name

Write-Host "ADLA Account Name" $adla_name
Write-Host "Subscription Id" $adla_subid
Write-Host "Subscription Name" $adla_subname
Write-Host "Defautl ADLS Store" $adla_defadlsname
Write-Host 

Write-Host '$subname' " = ""$adla_subname"" "
Write-Host '$subid' " = ""$adla_subid"" "
Write-Host '$adla' " = ""$adla_name"" "
Write-Host '$adls' " = ""$adla_defadlsname"" "
```

## <a name="working-with-azure"></a>Azure の操作

### <a name="get-details-of-azurerm-errors"></a>AzureRm エラーの詳細の取得

```powershell
Resolve-AzureRmError -Last
```

### <a name="verify-if-you-are-running-as-an-administrator"></a>管理者として実行しているかどうかの確認

```powershell
function Test-Administrator  
{  
    $user = [Security.Principal.WindowsIdentity]::GetCurrent();
    $p = New-Object Security.Principal.WindowsPrincipal $user
    $p.IsInRole([Security.Principal.WindowsBuiltinRole]::Administrator)  
}
```

### <a name="find-a-tenantid"></a>TenantID の検索

サブスクリプション名から:

```powershell
function Get-TenantIdFromSubcriptionName( [string] $subname )
{
    $sub = (Get-AzureRmSubscription -SubscriptionName $subname)
    $sub.TenantId
}

Get-TenantIdFromSubcriptionName "ADLTrainingMS"
```

サブスクリプション ID から:

```powershell
function Get-TenantIdFromSubcriptionId( [string] $subid )
{
    $sub = (Get-AzureRmSubscription -SubscriptionId $subid)
    $sub.TenantId
}

$subid = "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
Get-TenantIdFromSubcriptionId $subid
```

"contoso.com" などのドメイン アドレスから:


```powershell
function Get-TenantIdFromDomain( $domain )
{
    $url = "https://login.windows.net/" + $domain + "/.well-known/openid-configuration"
    return (Invoke-WebRequest $url|ConvertFrom-Json).token_endpoint.Split('/')[3]
}

$domain = "contoso.com"
Get-TenantIdFromDomain $domain
```

### <a name="list-all-your-subscriptions-and-tenant-ids"></a>すべてのサブスクリプションとテナント ID を一覧表示する

```powershell
$subs = Get-AzureRmSubscription
foreach ($sub in $subs)
{
    Write-Host $sub.Name "("  $sub.Id ")"
    Write-Host "`tTenant Id" $sub.TenantId
}
```

## <a name="create-a-data-lake-analytics-account-using-a-template"></a>テンプレートを使用して Data Lake Analytics アカウントを作成する

次の PowerShell スクリプトを使用して Azure Resource Group テンプレートを使用することもできます。

```powershell
$subId = "<Your Azure Subscription ID>"

$rg = "<New Azure Resource Group Name>"
$location = "<Location (such as East US 2)>"
$adls = "<New Data Lake Store Account Name>"
$adla = "<New Data Lake Analytics Account Name>"

$deploymentName = "MyDataLakeAnalyticsDeployment"
$armTemplateFile = "<LocalFolderPath>\azuredeploy.json"  # update the JSON template path 

# Log in to Azure
Login-AzureRmAccount -SubscriptionId $subId

# Create the resource group
New-AzureRmResourceGroup -Name $rg -Location $location

# Create the Data Lake Analytics account with the default Data Lake Store account.
$parameters = @{"adlAnalyticsName"=$adla; "adlStoreName"=$adls}
New-AzureRmResourceGroupDeployment -Name $deploymentName -ResourceGroupName $rg -TemplateFile $armTemplateFile -TemplateParameterObject $parameters 
```

詳細については、[Azure Resource Manager テンプレートを使用したアプリケーションのデプロイ](../azure-resource-manager/resource-group-template-deploy.md)に関する記事および「[Authoring Azure Resource Manager templates](../azure-resource-manager/resource-group-authoring-templates.md)」(Azure Resource Manager テンプレートの作成) を参照してください。

**テンプレートの例**

以下のテキストを `.json` ファイルとして保存し、前述の PowerShell スクリプトを使用してテンプレートを使用します。 

```json
{
  "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "adlAnalyticsName": {
      "type": "string",
      "metadata": {
        "description": "The name of the Data Lake Analytics account to create."
      }
    },
    "adlStoreName": {
      "type": "string",
      "metadata": {
        "description": "The name of the Data Lake Store account to create."
      }
    }
  },
  "resources": [
    {
      "name": "[parameters('adlStoreName')]",
      "type": "Microsoft.DataLakeStore/accounts",
      "location": "East US 2",
      "apiVersion": "2015-10-01-preview",
      "dependsOn": [ ],
      "tags": { }
    },
    {
      "name": "[parameters('adlAnalyticsName')]",
      "type": "Microsoft.DataLakeAnalytics/accounts",
      "location": "East US 2",
      "apiVersion": "2015-10-01-preview",
      "dependsOn": [ "[concat('Microsoft.DataLakeStore/accounts/',parameters('adlStoreName'))]" ],
      "tags": { },
      "properties": {
        "defaultDataLakeStoreAccount": "[parameters('adlStoreName')]",
        "dataLakeStoreAccounts": [
          { "name": "[parameters('adlStoreName')]" }
        ]
      }
    }
  ],
  "outputs": {
    "adlAnalyticsAccount": {
      "type": "object",
      "value": "[reference(resourceId('Microsoft.DataLakeAnalytics/accounts',parameters('adlAnalyticsName')))]"
    },
    "adlStoreAccount": {
      "type": "object",
      "value": "[reference(resourceId('Microsoft.DataLakeStore/accounts',parameters('adlStoreName')))]"
    }
  }
}
```

## <a name="next-steps"></a>次のステップ
* [Microsoft Azure Data Lake Analytics の概要](data-lake-analytics-overview.md)
* [Azure Portal](data-lake-analytics-get-started-portal.md) | [Azure PowerShell](data-lake-analytics-get-started-powershell.md) | [CLI 2.0](data-lake-analytics-get-started-cli2.md) で Data Lake Analytics の使用を開始する
* [Azure portal](data-lake-analytics-manage-use-portal.md) | [Azure PowerShell](data-lake-analytics-manage-use-powershell.md) | [CLI](data-lake-analytics-manage-use-cli.md) で Azure Data Lake Analytics を管理する 
