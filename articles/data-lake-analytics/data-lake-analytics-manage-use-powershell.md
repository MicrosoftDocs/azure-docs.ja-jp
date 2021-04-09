---
title: Azure PowerShell を使用する Azure Data Lake Analytics の管理
description: この記事では、Azure PowerShell を使用して、Azure Data Lake Analytics のアカウント、データ ソース、ユーザー、およびジョブを管理する方法について説明します。
ms.service: data-lake-analytics
ms.reviewer: jasonh
ms.topic: how-to
ms.date: 06/29/2018
ms.openlocfilehash: f1d5d732433a78b8e3e7ff94bd6aaa2eea8f4a87
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102051788"
---
# <a name="manage-azure-data-lake-analytics-using-azure-powershell"></a>Azure PowerShell を使用する Azure Data Lake Analytics の管理

[!INCLUDE [manage-selector](../../includes/data-lake-analytics-selector-manage.md)]

この記事では、Azure PowerShell を使用して、Azure Data Lake Analytics のアカウント、データ ソース、ユーザー、およびジョブを管理する方法について説明します。

## <a name="prerequisites"></a>前提条件

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Data Lake Analytics で PowerShell を使用するには、次の情報を収集します。

* **サブスクリプション ID**: Data Lake Analytics アカウントを含む Azure サブスクリプションの ID。
* **リソース グループ**: Data Lake Analytics アカウントが含まれている Azure リソース グループの名前。
* **Data Lake Analytics アカウント名**: Data Lake Analytics アカウントの名前。
* **既定の Data Lake Store アカウント名**: 各 Data Lake Analytics アカウントには既定の Data Lake Store アカウントがあります。
* **場所**: Data Lake Analytics アカウントの場所。"米国東部 2" やサポートされているその他の場所です。

このチュートリアルの PowerShell スニペットでは、以下の変数を使って各情報を格納します。

```powershell
$subId = "<SubscriptionId>"
$rg = "<ResourceGroupName>"
$adla = "<DataLakeAnalyticsAccountName>"
$adls = "<DataLakeStoreAccountName>"
$location = "<Location>"
```

## <a name="log-in-to-azure"></a>Azure にログインする

### <a name="log-in-using-interactive-user-authentication"></a>対話型ユーザー認証を使用したログイン

サブスクリプション ID またはサブスクリプション名を使用してログインします。

```powershell
# Using subscription id
Connect-AzAccount -SubscriptionId $subId

# Using subscription name
Connect-AzAccount -SubscriptionName $subname
```

## <a name="saving-authentication-context"></a>認証コンテキストの保存

`Connect-AzAccount` コマンドレットは、常に資格情報を要求します。 次のコマンドレットを使用すると、要求を回避できます。

```powershell
# Save login session information
Save-AzAccounts -Path D:\profile.json  

# Load login session information
Select-AzAccounts -Path D:\profile.json
```

### <a name="log-in-using-a-service-principal-identity-spi"></a>サービス プリンシパル ID (SPI) を使用したログイン

```powershell
$tenantid = "XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX"  
$spi_appname = "appname"
$spi_appid = "XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX"
$spi_secret = "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX"

$pscredential = New-Object System.Management.Automation.PSCredential ($spi_appid, (ConvertTo-SecureString $spi_secret -AsPlainText -Force))
Login-AzAccount -ServicePrincipal -TenantId $tenantid -Credential $pscredential -Subscription $subid
```

## <a name="manage-accounts"></a>アカウントの管理

### <a name="list-accounts"></a>アカウントの一覧表示

```powershell
# List Data Lake Analytics accounts within the current subscription.
Get-AdlAnalyticsAccount

# List Data Lake Analytics accounts within a specific resource group.
Get-AdlAnalyticsAccount -ResourceGroupName $rg
```

### <a name="create-an-account"></a>アカウントを作成する

すべての Data Lake Analytics アカウントに、ログの保存を目的とした既定の Data Lake Store アカウントが必要です。 既存のアカウントを利用することも、アカウントを作成することもできます。

```powershell
# Create a data lake store if needed, or you can re-use an existing one
New-AdlStore -ResourceGroupName $rg -Name $adls -Location $location
New-AdlAnalyticsAccount -ResourceGroupName $rg -Name $adla -Location $location -DefaultDataLake $adls
```

### <a name="get-account-information"></a>アカウント情報の取得

アカウントの詳細を取得します。

```powershell
Get-AdlAnalyticsAccount -Name $adla
```

### <a name="check-if-an-account-exists"></a>アカウントが存在するかどうかを確認する

```powershell
Test-AdlAnalyticsAccount -Name $adla
```

## <a name="manage-data-sources"></a>データ ソースを管理する

Azure Data Lake Analytics では現在、以下のデータ ソースがサポートされています。

* [Azure Data Lake Store](../data-lake-store/data-lake-store-overview.md)
* [Azure ストレージ](../storage/common/storage-introduction.md)

すべての Data Lake Analytics アカウントには、既定の Data Lake Store アカウントがあります。 既定の Data Lake Store アカウントは、ジョブ メタデータとジョブ監査ログの格納に使用されます。

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
Get-AdlAnalyticsDataSource -Account $adla

# List attached Data Lake Store accounts
Get-AdlAnalyticsDataSource -Account $adla | where -Property Type -EQ "DataLakeStore"

# List attached Storage accounts
Get-AdlAnalyticsDataSource -Account $adla | where -Property Type -EQ "Blob"
```

## <a name="submit-u-sql-jobs"></a>U-SQL ジョブを送信する

### <a name="submit-a-string-as-a-u-sql-job"></a>文字列を U-SQL ジョブとして送信する

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

### <a name="submit-a-file-as-a-u-sql-job"></a>ファイルを U-SQL ジョブとして送信する

```powershell
$scriptpath = "d:\test.usql"
$script | Out-File $scriptpath
Submit-AdlJob -AccountName $adla –ScriptPath $scriptpath -Name "Demo"
```

### <a name="list-jobs"></a>ジョブのリスト

出力結果には、現在実行されているジョブと最近完了したジョブが含まれます。

```powershell
Get-AdlJob -Account $adla
```

### <a name="list-the-top-n-jobs"></a>上位 n 個のジョブを一覧表示する

既定ではジョブの一覧は送信時に並べ替えられます。 したがって最近送信されたジョブは、先頭に表示されます。 既定では、ADLA アカウントはジョブを 180 日間記憶していますが、Get-AdlJob コマンドレットは既定で、最初の 500 件のみを返します。 Top パラメーターを使用して、特定の数のジョブを一覧表示します。

```powershell
$jobs = Get-AdlJob -Account $adla -Top 10
```

### <a name="list-jobs-by-job-state"></a>ジョブの状態別にジョブを一覧表示する

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

### <a name="list-jobs-by-job-result"></a>ジョブの結果別にジョブを一覧表示する

`-Result` パラメーターを使って、終了したジョブが正常完了したかどうかを検出します。 次の値があります。

* キャンセル
* 失敗
* なし
* 成功

``` powershell
# List Successful jobs.
Get-AdlJob -Account $adla -State Ended -Result Succeeded

# List Failed jobs.
Get-AdlJob -Account $adla -State Ended -Result Failed
```

### <a name="list-jobs-by-job-submitter"></a>ジョブの送信元別にジョブを一覧表示する

`-Submitter` パラメーターを使うと、ジョブを送信したユーザーを特定できます。

```powershell
Get-AdlJob -Account $adla -Submitter "joe@contoso.com"
```

### <a name="list-jobs-by-submission-time"></a>ジョブの送信時刻別にジョブを一覧表示する

`-SubmittedAfter` はフィルタ処理して時間の範囲を特定する際に役に立ちます。

```powershell
# List  jobs submitted in the last day.
$d = [DateTime]::Now.AddDays(-1)
Get-AdlJob -Account $adla -SubmittedAfter $d

# List  jobs submitted in the last seven day.
$d = [DateTime]::Now.AddDays(-7)
Get-AdlJob -Account $adla -SubmittedAfter $d
```

### <a name="get-job-status"></a>ジョブの状態の取得

特定のジョブの状態を取得します。

```powershell
Get-AdlJob -AccountName $adla -JobId $job.JobId
```

### <a name="cancel-a-job"></a>ジョブを取り消す

```powershell
Stop-AdlJob -Account $adla -JobID $jobID
```

### <a name="wait-for-a-job-to-finish"></a>ジョブが終了するまで待機する

ジョブが完了するまで `Get-AdlAnalyticsJob` を繰り返す替わりに、`Wait-AdlJob` コマンドレットを使用してジョブの終了を待機できます。

```powershell
Wait-AdlJob -Account $adla -JobId $job.JobId
```


## <a name="list-job-pipelines-and-recurrences"></a>ジョブのパイプラインと反復を一覧表示する

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

## <a name="manage-compute-policies"></a>コンピューティング ポリシーを管理する

### <a name="list-existing-compute-policies"></a>既存のコンピューティング ポリシーを一覧表示する

`Get-AdlAnalyticsComputePolicy` コマンドレットは、Data Lake Analytics アカウントのコンピューティング ポリシーの情報を取得します。

```powershell
$policies = Get-AdlAnalyticsComputePolicy -Account $adla
```

### <a name="create-a-compute-policy"></a>コンピューティング ポリシーを作成する

`New-AdlAnalyticsComputePolicy` コマンドレットは、Data Lake Analytics アカウントの新しいコンピューティング ポリシーを作成します。 この例では、指定したユーザーが使用可能な最大 AU を 50 に、最小のジョブ優先順位を 250 に設定します。

```powershell
$userObjectId = (Get-AzAdUser -SearchString "garymcdaniel@contoso.com").Id

New-AdlAnalyticsComputePolicy -Account $adla -Name "GaryMcDaniel" -ObjectId $objectId -ObjectType User -MaxDegreeOfParallelismPerJob 50 -MinPriorityPerJob 250
```

## <a name="manage-files"></a>ファイルを管理する

### <a name="check-for-the-existence-of-a-file"></a>ファイルの存在を確認する

```powershell
Test-AdlStoreItem -Account $adls -Path "/data.csv"
```

### <a name="uploading-and-downloading"></a>アップロードとダウンロード

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

## <a name="manage-the-u-sql-catalog"></a>U-SQL カタログを管理する

U-SQL カタログを使用して、U-SQL スクリプトで共有できるように、データとコードを構成します。 カタログでは、Azure Data Lake のデータを使用して可能な限り最高のパフォーマンスを実現できます。 詳細については、「 [U-SQL カタログの使用](./data-lake-analytics-u-sql-get-started.md)」を参照してください。

### <a name="list-items-in-the-u-sql-catalog"></a>U-SQL カタログ内の項目を一覧表示する

```powershell
# List U-SQL databases
Get-AdlCatalogItem -Account $adla -ItemType Database

# List tables within a database
Get-AdlCatalogItem -Account $adla -ItemType Table -Path "database"

# List tables within a schema.
Get-AdlCatalogItem -Account $adla -ItemType Table -Path "database.schema"
```

### <a name="list-all-the-assemblies-the-u-sql-catalog"></a>すべてのアセンブリと U-SQL カタログを一覧表示する

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

### <a name="store-credentials-in-the-catalog"></a>資格情報をカタログに格納する

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

## <a name="manage-firewall-rules"></a>ファイアウォール規則の管理

### <a name="list-firewall-rules"></a>ファイアウォール規則の一覧表示

```powershell
Get-AdlAnalyticsFirewallRule -Account $adla
```

### <a name="add-a-firewall-rule"></a>ファイアウォール規則の追加

```powershell
$ruleName = "Allow access from on-prem server"
$startIpAddress = "<start IP address>"
$endIpAddress = "<end IP address>"

Add-AdlAnalyticsFirewallRule -Account $adla -Name $ruleName -StartIpAddress $startIpAddress -EndIpAddress $endIpAddress
```

### <a name="modify-a-firewall-rule"></a>ファイアウォール規則の変更

```powershell
Set-AdlAnalyticsFirewallRule -Account $adla -Name $ruleName -StartIpAddress $startIpAddress -EndIpAddress $endIpAddress
```

### <a name="remove-a-firewall-rule"></a>ファイアウォール規則の削除

```powershell
Remove-AdlAnalyticsFirewallRule -Account $adla -Name $ruleName
```

### <a name="allow-azure-ip-addresses"></a>Azure の IP アドレスを許可する

```powershell
Set-AdlAnalyticsAccount -Name $adla -AllowAzureIpState Enabled
```

```powershell
Set-AdlAnalyticsAccount -Name $adla -FirewallState Enabled
Set-AdlAnalyticsAccount -Name $adla -FirewallState Disabled
```

## <a name="working-with-azure"></a>Azure の操作

### <a name="get-error-details"></a>エラー詳細の取得

```powershell
Resolve-AzError -Last
```

### <a name="verify-if-you-are-running-as-an-administrator-on-your-windows-machine"></a>Windows マシン上で管理者として実行しているかどうかを確認する

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
function Get-TenantIdFromSubscriptionName( [string] $subname )
{
    $sub = (Get-AzSubscription -SubscriptionName $subname)
    $sub.TenantId
}

Get-TenantIdFromSubscriptionName "ADLTrainingMS"
```

サブスクリプション ID から:

```powershell
function Get-TenantIdFromSubscriptionId( [string] $subid )
{
    $sub = (Get-AzSubscription -SubscriptionId $subid)
    $sub.TenantId
}

$subid = "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
Get-TenantIdFromSubscriptionId $subid
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
$subs = Get-AzSubscription
foreach ($sub in $subs)
{
    Write-Host $sub.Name "("  $sub.Id ")"
    Write-Host "`tTenant Id" $sub.TenantId
}
```

## <a name="create-a-data-lake-analytics-account-using-a-template"></a>テンプレートを使用して Data Lake Analytics アカウントを作成する

次のサンプル「[テンプレートを使用して Data Lake Analytics アカウントを作成する](https://github.com/Azure-Samples/data-lake-analytics-create-account-with-arm-template)」を使用して、Azure リソース グループ テンプレートを使用することもできます。

## <a name="next-steps"></a>次のステップ
* [Microsoft Azure Data Lake Analytics の概要](data-lake-analytics-overview.md)
* [Azure portal](data-lake-analytics-get-started-portal.md) | [Azure PowerShell](data-lake-analytics-get-started-powershell.md) | [Azure CLI](data-lake-analytics-get-started-cli.md) で Data Lake Analytics の使用を開始する
* [Azure portal](data-lake-analytics-manage-use-portal.md) | [Azure PowerShell](data-lake-analytics-manage-use-powershell.md) | [CLI](data-lake-analytics-manage-use-cli.md) で Azure Data Lake Analytics を管理する
