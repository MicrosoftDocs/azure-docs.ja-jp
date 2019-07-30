---
title: PowerShell を使用した Log Analytics ワークスペースの作成と構成 | Microsoft Docs
description: Azure Monitor の Log Analytics ワークスペースは、オンプレミスまたはクラウド インフラストラクチャのサーバーのデータを格納します。 Azure Diagnostics によって生成された場合は、Azure Storage からマシンのデータを収集できます。
services: log-analytics
author: bwren
ms.service: log-analytics
ms.devlang: powershell
ms.topic: conceptual
ms.date: 05/19/2019
ms.author: bwren
ms.openlocfilehash: 4faa58536d6458b01adbb7dab60bfd10be18275b
ms.sourcegitcommit: b2db98f55785ff920140f117bfc01f1177c7f7e2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/16/2019
ms.locfileid: "68234794"
---
# <a name="manage-log-analytics-workspace-in-azure-monitor-using-powershell"></a>PowerShell を使用して Azure Monitor の Log Analytics ワークスペースを管理する

Azure Monitor の Log Analytics ワークスペースでは、コマンド ラインから、またはスクリプトの一部として、[Log Analytics の PowerShell コマンドレット](https://docs.microsoft.com/powershell/module/az.operationalinsights/) を使用してさまざまな機能を実行できます。  PowerShell で実行できる作業の例を次に挙げます。

* ワークスペースの作成
* ソリューションの追加と削除
* 保存されている検索のインポートとエクスポート
* コンピューター グループの作成
* Windows エージェントがインストールされているコンピューターでの IIS ログのコレクションの有効化
* Linux および Windows コンピューターからのパフォーマンス カウンターの収集
* Linux コンピューターの syslog からのイベントの収集
* Windows イベント ログからのイベントの収集
* カスタム イベント ログの収集
* Azure 仮想マシンへのログ分析エージェントの追加
* Azure Diagnostics を使用して収集されたデータをログ分析でインデックスするための構成

この記事の 2 つのコード サンプルで紹介しているのは、PowerShell から実行できる機能の一部です。  その他の機能については、 [Log Analytics の PowerShell コマンドレット リファレンス](https://docs.microsoft.com/powershell/module/az.operationalinsights/) を参照してください。

> [!NOTE]
> Log Analytics は以前、オペレーション インサイトと呼ばれていました。そのため、コマンドレットにはその旧称が使用されています。

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>前提条件
これらの例は、Az.OperationalInsights モジュールのバージョン 1.0.0 以降で動作します。


## <a name="create-and-configure-a-log-analytics-workspace"></a>Log Analytics ワークスペースを作成して構成する
以下のサンプル スクリプトは、次の作業の方法を紹介したものです。

1. ワークスペースの作成
2. 利用可能なソリューションをリストする
3. ソリューションをワークスペースに追加する
4. 保存した検索条件をインポートする
5. 保存した検索条件をエクスポートする
6. コンピューター グループの作成
7. Windows エージェントがインストールされているコンピューターでの IIS ログのコレクションの有効化
8. Linux コンピューターからの Logical Disk パフォーマンス カウンター (% Used Inodes、Free Megabytes、% Used Space、Disk Transfers/sec、Disk Reads/sec、Disk Writes/sec) の収集
9. Linux コンピューターからの syslog イベントの収集
10. Windows コンピューターのアプリケーション イベント ログからのエラーおよび警告のイベントの収集
11. Windows コンピューターからの Memory Available Mbytes パフォーマンス カウンターの収集
12. カスタム ログの収集

```powershell

$ResourceGroup = "oms-example"
$WorkspaceName = "log-analytics-" + (Get-Random -Maximum 99999) # workspace names need to be unique - Get-Random helps with this for the example code
$Location = "westeurope"

# List of solutions to enable
$Solutions = "Security", "Updates", "SQLAssessment"

# Saved Searches to import
$ExportedSearches = @"
[
    {
        "Category":  "My Saved Searches",
        "DisplayName":  "WAD Events (All)",
        "Query":  "Type=Event SourceSystem:AzureStorage ",
        "Version":  1
    },
    {
        "Category":  "My Saved Searches",
        "DisplayName":  "Current Disk Queue Length",
        "Query":  "Perf | where ObjectName == "LogicalDisk" and CounterName == "Current Disk Queue Length" and InstanceName == "C:",
        "Version":  1
    }
]
"@ | ConvertFrom-Json

# Custom Log to collect
$CustomLog = @"
{
    "customLogName": "sampleCustomLog1",
    "description": "Example custom log datasource",
    "inputs": [
        {
            "location": {
            "fileSystemLocations": {
                "windowsFileTypeLogPaths": [ "e:\\iis5\\*.log" ],
                "linuxFileTypeLogPaths": [ "/var/logs" ]
                }
            },
        "recordDelimiter": {
            "regexDelimiter": {
                "pattern": "\\n",
                "matchIndex": 0,
                "matchIndexSpecified": true,
                "numberedGroup": null
                }
            }
        }
    ],
    "extractions": [
        {
            "extractionName": "TimeGenerated",
            "extractionType": "DateTime",
            "extractionProperties": {
                "dateTimeExtraction": {
                    "regex": null,
                    "joinStringRegex": null
                    }
                }
            }
        ]
    }
"@

# Create the resource group if needed
try {
    Get-AzResourceGroup -Name $ResourceGroup -ErrorAction Stop
} catch {
    New-AzResourceGroup -Name $ResourceGroup -Location $Location
}

# Create the workspace
New-AzOperationalInsightsWorkspace -Location $Location -Name $WorkspaceName -Sku Standard -ResourceGroupName $ResourceGroup

# List all solutions and their installation status
Get-AzOperationalInsightsIntelligencePacks -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName

# Add solutions
foreach ($solution in $Solutions) {
    Set-AzOperationalInsightsIntelligencePack -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName -IntelligencePackName $solution -Enabled $true
}

# List enabled solutions
(Get-AzOperationalInsightsIntelligencePacks -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName).Where({($_.enabled -eq $true)})

# Import Saved Searches
foreach ($search in $ExportedSearches) {
    $id = $search.Category + "|" + $search.DisplayName
    New-AzOperationalInsightsSavedSearch -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName -SavedSearchId $id -DisplayName $search.DisplayName -Category $search.Category -Query $search.Query -Version $search.Version
}

# Export Saved Searches
(Get-AzOperationalInsightsSavedSearch -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName).Value.Properties | ConvertTo-Json

# Create Computer Group based on a query
New-AzOperationalInsightsComputerGroup -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName -SavedSearchId "My Web Servers" -DisplayName "Web Servers" -Category "My Saved Searches" -Query "Computer=""web*"" | distinct Computer" -Version 1

# Enable IIS Log Collection using agent
Enable-AzOperationalInsightsIISLogCollection -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName

# Linux Perf
New-AzOperationalInsightsLinuxPerformanceObjectDataSource -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName -ObjectName "Logical Disk" -InstanceName "*"  -CounterNames @("% Used Inodes", "Free Megabytes", "% Used Space", "Disk Transfers/sec", "Disk Reads/sec", "Disk Reads/sec", "Disk Writes/sec") -IntervalSeconds 20  -Name "Example Linux Disk Performance Counters"
Enable-AzOperationalInsightsLinuxPerformanceCollection -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName

# Linux Syslog
New-AzOperationalInsightsLinuxSyslogDataSource -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName -Facility "kern" -CollectEmergency -CollectAlert -CollectCritical -CollectError -CollectWarning -Name "Example kernel syslog collection"
Enable-AzOperationalInsightsLinuxSyslogCollection -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName

# Windows Event
New-AzOperationalInsightsWindowsEventDataSource -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName -EventLogName "Application" -CollectErrors -CollectWarnings -Name "Example Application Event Log"

# Windows Perf
New-AzOperationalInsightsWindowsPerformanceCounterDataSource -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName -ObjectName "Memory" -InstanceName "*" -CounterName "Available MBytes" -IntervalSeconds 20 -Name "Example Windows Performance Counter"

# Custom Logs

New-AzOperationalInsightsCustomLogDataSource -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName -CustomLogRawJson "$CustomLog" -Name "Example Custom Log Collection"

```
前述の例では、regexDelimiter は改行のために "\\n" と定義されていました。 ログの区切り記号はタイムスタンプにすることもできます。  サポートされる形式は次のとおりです。

| 形式 | Json RegEx 形式では、標準の RegEx 内のすべての \ に 2 つの \\ が使用されるので、RegEx アプリでテストする場合は \\ を \ に減らします | | |
| --- | --- | --- | --- |
| `YYYY-MM-DD HH:MM:SS` | `((\\d{2})|(\\d{4}))-([0-1]\\d)-(([0-3]\\d)|(\\d))\\s((\\d)|([0-1]\\d)|(2[0-4])):[0-5][0-9]:[0-5][0-9]` | | |
| `M/D/YYYY HH:MM:SS AM/PM` | `(([0-1]\\d)|[0-9])/(([0-3]\\d)|(\\d))/((\\d{2})|(\\d{4}))\\s((\\d)|([0-1]\\d)|(2[0-4])):[0-5][0-9]:[0-5][0-9]\\s(AM|PM|am|pm)` | | |
| `dd/MMM/yyyy HH:MM:SS` | `(([0-2][1-9]|[3][0-1])\\/(Jan|Feb|Mar|May|Apr|Jul|Jun|Aug|Oct|Sep|Nov|Dec|jan|feb|mar|may|apr|jul|jun|aug|oct|sep|nov|dec)\\/((19|20)[0-9][0-9]))\\s((\\d)|([0-1]\\d)|(2[0-4])):[0-5][0-9]:[0-5][0-9])` |
| `MMM dd yyyy HH:MM:SS` | `(((?:Jan(?:uary)?|Feb(?:ruary)?|Mar(?:ch)?|Apr(?:il)?|May|Jun(?:e)?|Jul(?:y)?|Aug(?:ust)?|Sep(?:tember)?|Sept|Oct(?:ober)?|Nov(?:ember)?|Dec(?:ember)?)).*?((?:(?:[0-2]?\\d{1})|(?:[3][01]{1})))(?![\\d]).*?((?:(?:[1]{1}\\d{1}\\d{1}\\d{1})|(?:[2]{1}\\d{3})))(?![\\d]).*?((?:(?:[0-1][0-9])|(?:[2][0-3])|(?:[0-9])):(?:[0-5][0-9])(?::[0-5][0-9])?(?:\\s?(?:am|AM|pm|PM))?))` | | |
| `yyMMdd HH:mm:ss` | `([0-9]{2}([0][1-9]|[1][0-2])([0-2][0-9]|[3][0-1])\\s\\s?([0-1]?[0-9]|[2][0-3]):[0-5][0-9]:[0-5][0-9])` | | |
| `ddMMyy HH:mm:ss` | `(([0-2][0-9]|[3][0-1])([0][1-9]|[1][0-2])[0-9]{2}\\s\\s?([0-1]?[0-9]|[2][0-3]):[0-5][0-9]:[0-5][0-9])` | | |
| `MMM d HH:mm:ss` | `(Jan|Feb|Mar|Apr|May|Jun|Jul|Aug|Sep|Oct|Nov|Dec)\\s\\s?([0]?[1-9]|[1-2][0-9]|[3][0-1])\\s([0-1]?[0-9]|[2][0-3]):([0-5][0-9]):([0-5][0-9])` | | |
| `MMM  d HH:mm:ss` <br> MMM の後に 2 つのスペース | `(Jan|Feb|Mar|Apr|May|Jun|Jul|Aug|Sep|Oct|Nov|Dec)\\s\\s([0]?[1-9]|[1-2][0-9]|[3][0-1])\\s([0][0-9]|[1][0-2]):([0-5][0-9]):([0-5][0-9])` | | |
| `MMM d HH:mm:ss` | `(Jan|Feb|Mar|Apr|May|Jun|Jul|Aug|Sep|Oct|Nov|Dec)\\s([0]?[1-9]|[1-2][0-9]|[3][0-1])\\s([0][0-9]|[1][0-2]):([0-5][0-9]):([0-5][0-9])` | | |
| `dd/MMM/yyyy:HH:mm:ss +zzzz` <br> この + は + または - です <br> zzz は時刻のオフセットです | `(([0-2][1-9]|[3][0-1])\\/(Jan|Feb|Mar|Apr|May|Jun|Jul|Aug|Sep|Oct|Nov|Dec)\\/((19|20)[0-9][0-9]):([0][0-9]|[1][0-2]):([0-5][0-9]):([0-5][0-9])\\s[\\+|\\-][0-9]{4})` | | |
| `yyyy-MM-ddTHH:mm:ss` <br> T はリテラル文字の T です | `((\\d{2})|(\\d{4}))-([0-1]\\d)-(([0-3]\\d)|(\\d))T((\\d)|([0-1]\\d)|(2[0-4])):[0-5][0-9]:[0-5][0-9]` | | |

## <a name="configuring-log-analytics-to-send-azure-diagnostics"></a>Azure Diagnostics を送信するための Log Analytics の構成
エージェントを介さずに Azure リソースを監視するには、リソースで Azure Diagnostics を有効にし、Log Analytics ワークスペースへの書き込みを構成する必要があります。 この方法では、データがワークスペースに直接送信され、ストレージ アカウントにデータを書き込む必要がありません。 サポートされているリソースは次のとおりです。

| リソースの種類 | ログ | メトリック |
| --- | --- | --- |
| Application Gateway    | はい | はい |
| Automation アカウント     | はい | |
| Batch アカウント          | はい | はい |
| Data Lake Analytics     | はい | |
| Data Lake Store         | はい | |
| Elastic SQL Pool        |     | はい |
| Event Hub 名前空間     |     | はい |
| IoT Hub                |     | はい |
| Key Vault               | はい | |
| ロード バランサー          | はい | |
| Logic Apps              | はい | はい |
| ネットワーク セキュリティ グループ | はい | |
| Azure Cache for Redis             |     | はい |
| Search サービス         | はい | はい |
| Service Bus 名前空間   |     | はい |
| SQL (v12)               |     | はい |
| Web サイト               |     | はい |
| Web サーバー ファーム        |     | はい |

使用可能なメトリックの詳細については、「[Azure Monitor のサポートされるメトリック](../../azure-monitor/platform/metrics-supported.md)」を参照してください。

利用できるログの詳細については、「[診断ログでサポートされているサービスとスキーマ](../../azure-monitor/platform/diagnostic-logs-schema.md)」を参照してください。

```powershell
$workspaceId = "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxx/resourcegroups/oi-default-east-us/providers/microsoft.operationalinsights/workspaces/rollingbaskets"

$resourceId = "/SUBSCRIPTIONS/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxx/RESOURCEGROUPS/DEMO/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/DEMO"

Set-AzDiagnosticSetting -ResourceId $resourceId -WorkspaceId $workspaceId -Enabled $true
```

前述のコマンドレットを使用して、異なるサブスクリプションに含まれるリソースからログを収集することもできます。 ログを作成するリソースとログが送信されるワークスペースの両方の ID が指定されているため、このコマンドレットは複数のサブスクリプションにまたがって動作します。


## <a name="configuring-log-analytics-workspace-to-collect-azure-diagnostics-from-storage"></a>ストレージから Azure 診断を収集するための Log Analytics ワークスペースの構成
従来のクラウド サービスまたは Service Fabric クラスターの実行中のインスタンスからログ データを収集するには、まず Azure ストレージにデータを書き込む必要があります。 その後、ストレージ アカウントからログを収集するように Log Analytics ワークスペースを構成します。 サポートされているリソースは次のとおりです。

* クラシック クラウド サービス (Web ロールと worker ロール)
* Service Fabric クラスター

以下の例では、次のことを行っています。

1. 既存のストレージ アカウントと、ワークスペースでインデックスを付けるデータの収集元の場所をリストする
2. ストレージ アカウントからデータを読み取るための構成を作成する
3. 新しく作成した構成に変更を加え、インデックスの作成対象となるデータの収集元を追加する
4. 新しく作成した構成を削除する

```powershell
# validTables = "WADWindowsEventLogsTable", "LinuxsyslogVer2v0", "WADServiceFabric*EventTable", "WADETWEventTable"
$workspace = (Get-AzOperationalInsightsWorkspace).Where({$_.Name -eq "your workspace name"})

# Update these two lines with the storage account resource ID and the storage account key for the storage account you want the workspace to index
$storageId = "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxx/resourceGroups/demo/providers/Microsoft.Storage/storageAccounts/wadv2storage"
$key = "abcd=="

# List existing insights
Get-AzOperationalInsightsStorageInsight -ResourceGroupName $workspace.ResourceGroupName -WorkspaceName $workspace.Name

# Create a new insight
New-AzOperationalInsightsStorageInsight -ResourceGroupName $workspace.ResourceGroupName -WorkspaceName $workspace.Name -Name "newinsight" -StorageAccountResourceId $storageId -StorageAccountKey $key -Tables @("WADWindowsEventLogsTable") -Containers @("wad-iis-logfiles")

# Update existing insight
Set-AzOperationalInsightsStorageInsight -ResourceGroupName $workspace.ResourceGroupName -WorkspaceName $workspace.Name -Name "newinsight" -Tables @("WADWindowsEventLogsTable", "WADETWEventTable") -Containers @("wad-iis-logfiles")

# Remove the insight
Remove-AzOperationalInsightsStorageInsight -ResourceGroupName $workspace.ResourceGroupName -WorkspaceName $workspace.Name -Name "newinsight"

```

前述のスクリプトを使用して、異なるサブスクリプションに含まれるストレージ アカウントからログを収集することもできます。 ストレージ アカウントのリソース ID と対応するアクセス キーが指定されているため、このスクリプトは複数のサブスクリプションにまたがって動作します。 アクセス キーを変更した場合は、ストレージ情報を更新して新しいキーを反映する必要があります。


## <a name="next-steps"></a>次の手順
* [Log Analytics の PowerShell コマンドレットを参照](https://docs.microsoft.com/powershell/module/az.operationalinsights/) し、Log Analytics を構成するための PowerShell の使い方について詳しく調べる。

