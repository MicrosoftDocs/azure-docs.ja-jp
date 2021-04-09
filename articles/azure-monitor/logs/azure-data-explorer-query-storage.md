---
title: Azure Data Explorer を使用して Azure Monitor からエクスポートされたデータのクエリを実行する (プレビュー)
description: Azure Data Explorer を使用して、Log Analytics ワークスペースから Azure ストレージ アカウントへエクスポートされたデータのクエリを実行します。
author: osalzberg
ms.author: bwren
ms.reviewer: bwren
ms.topic: conceptual
ms.date: 10/13/2020
ms.openlocfilehash: 5eff593075db118b23d74147e33b40eb4402193c
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102031159"
---
# <a name="query-exported-data-from-azure-monitor-using-azure-data-explorer-preview"></a>Azure Data Explorer を使用して Azure Monitor からエクスポートされたデータのクエリを実行する (プレビュー)
Azure Monitor から Azure ストレージ アカウントにデータをエクスポートすると、低コストのリテンション期間が有効になり、ログを別のリージョンに再割り当てすることができます。 Azure Data Explorer を使用して、Log Analytics ワークスペースからエクスポートされたデータのクエリを実行します。 構成されると、ワークスペースから Azure ストレージ アカウントに送信されるサポートされているテーブルが、Azure Data Explorer のデータ ソースとして使用できるようになります。

プロセス フローは次のとおりです。 

1.  Log Analytics ワークスペースから Azure ストレージ アカウントにデータをエクスポートします。
2.  Azure Data Explorer クラスターに外部テーブルを作成し、データ型にマッピングします。
3.  Azure Data Explorer からデータのクエリを実行する

:::image type="content" source="media/azure-data-explorer-query-storage/exported-data-query.png" alt-text="Azure Data Explorer のエクスポートされたデータのクエリ フロー。":::



## <a name="send-data-to-azure-storage"></a>Azure ストレージにデータを送信する
Azure Monitor ログは、次のいずれかのオプションを使用して Azure ストレージ アカウントにエクスポートできます。

- Log Analytics ワークスペースのすべてのデータを Azure ストレージ アカウントまたはイベント ハブにエクスポートするには、Azure Monitor ログの Log Analytics ワークスペース データ エクスポート機能を使用します。 「[Azure Monitor の Log Analytics ワークスペース データ エクスポート (プレビュー)](./logs-data-export.md)」を参照してください。
- ロジック アプリを使用したログ クエリからのスケジュールされたエクスポート。 これはデータ エクスポート機能に似ていますが、フィルター処理または集計されたデータを Azure ストレージに送信できます。 ただし、この方法には[ログ クエリの制限](../service-limits.md#log-analytics-workspaces)が適用されます。「[ロジック アプリを使用して Log Analytics ワークスペースから Azure ストレージへデータをアーカイブする](./logs-export-logic-app.md)」を参照してください。
- ロジック アプリを使用したワンタイム エクスポート。 「[Logic Apps および Power Automate の Azure Monitor Logs コネクタ](./logicapp-flow-connector.md)」を参照してください。
- PowerShell スクリプトを使用したローカル コンピューターへのワンタイム エクスポート。 「[Invoke-AzOperationalInsightsQueryExport](https://www.powershellgallery.com/packages/Invoke-AzOperationalInsightsQueryExport)」を参照してください。

> [!TIP]
> 既存の Azure Data Explorer クラスターを使用することも、必要な構成で新しい専用クラスターを作成することもできます。

## <a name="create-an-external-table-located-in-azure-blob-storage"></a>Azure Blob Storage に配置された外部テーブルを作成する
[外部テーブル](/azure/data-explorer/kusto/query/schema-entities/externaltables)を使用して、Azure Data Explorer を Azure ストレージ アカウントにリンクします。 外部テーブルは、Kusto データベースの外部に格納されているデータを参照する Kusto スキーマ エンティティです。 外部テーブルには、テーブルと同様に、適切に定義されたスキーマがあります。 データは、テーブルとは異なり、Kusto クラスターの外部に格納され管理されます。 前のセクションからエクスポートされたデータは、JSON 行に保存されます。

参照を作成するには、エクスポートされたテーブルのスキーマが必要です。 Log Analytics から [getschema](/azure/data-explorer/kusto/query/getschemaoperator) 演算子を使用して、テーブルの列とそのデータ型を含むこの情報を取得します。

:::image type="content" source="media\azure-data-explorer-query-storage\exported-data-map-schema.jpg" alt-text="Log Analytics テーブル スキーマ。":::

これで、出力を使用して、外部テーブルを構築するための Kusto クエリを作成できました。
「[Azure Storage または Azure Data Lake の外部テーブルを作成および変更する](/azure/data-explorer/kusto/management/external-tables-azurestorage-azuredatalake)」のガイダンスに従って、JSON 形式で外部テーブルを作成してから、Azure Data Explorer データベースからクエリを実行します。

>[!NOTE]
>外部テーブルの作成は、2 つのプロセスから構成されています。 最初に外部テーブルが作成され、次にマッピングが作成されます。

次の PowerShell スクリプトは、テーブルとマッピングに対して [create](/azure/data-explorer/kusto/management/external-tables-azurestorage-azuredatalake#create-external-table-mapping)コマンドを作成します。

```powershell
PARAM(
    $resourcegroupname, #The name of the Azure resource group
    $TableName, # The Log Analytics table you wish to convert to external table
    $MapName, # The name of the map
    $subscriptionId, #The ID of the subscription
    $WorkspaceId, # The Log Analytics WorkspaceId
    $WorkspaceName, # The Log Analytics workspace name
    $BlobURL, # The Blob URL where the data is saved
    $ContainerAccessKey, # The blob container Access Key (Option to add a SAS url)
    $ExternalTableName = $null # The External Table name, null to use the same name
)

if($null -eq $ExternalTableName) {
    $ExternalTableName = $TableName
}

$query = $TableName + ' | getschema | project ColumnName, DataType'

$output = (Invoke-AzOperationalInsightsQuery -WorkspaceId $WorkspaceId -Query $query).Results

$FirstCommand = @()
$SecondCommand = @()

foreach ($record in $output) {
    if ($record.DataType -eq 'System.DateTime') {
        $dataType = 'datetime'
    } else {
        $dataType = 'string'
    }
    $FirstCommand += $record.ColumnName + ":" + "$dataType" + ","
    $SecondCommand += "{`"column`":" + "`"" + $record.ColumnName + "`"," + "`"datatype`":`"$dataType`",`"path`":`"$." + $record.ColumnName + "`"},"
}
$schema = ($FirstCommand -join '') -replace ',$'
$mapping = ($SecondCommand -join '') -replace ',$'

$CreateExternal = @'
.create external table {0} ({1})
kind=blob
partition by (TimeGeneratedPartition:datetime = bin(TimeGenerated, 1min))
pathformat = (datetime_pattern("'y='yyyy'/m='MM'/d='dd'/h='HH'/m='mm", TimeGeneratedPartition))
dataformat=multijson
(
   h@'{2}/WorkspaceResourceId=/subscriptions/{4}/resourcegroups/{6}/providers/microsoft.operationalinsights/workspaces/{5};{3}'
)
with
(
   docstring = "Docs",
   folder = "ExternalTables"
)
'@ -f $TableName, $schema, $BlobURL, $ContainerAccessKey, $subscriptionId, $WorkspaceName, $resourcegroupname,$WorkspaceId

$createMapping = @'
.create external table {0} json mapping "{1}" '[{2}]'
'@ -f $ExternalTableName, $MapName, $mapping

Write-Host -ForegroundColor Red 'Copy and run the following commands (one by one), on your Azure Data Explorer cluster query window to create the external table and mappings:'
write-host -ForegroundColor Green $CreateExternal
Write-Host -ForegroundColor Green $createMapping
```

次の図に、出力の例を示します。

:::image type="content" source="media/azure-data-explorer-query-storage/external-table-create-command-output.png" alt-text="ExternalTable create コマンドの出力。":::

[![出力例](media/azure-data-explorer-query-storage/external-table-create-command-output.png)](media/azure-data-explorer-query-storage/external-table-create-command-output.png#lightbox)

>[!TIP]
>* Azure Data Explorer クライアント ツールにスクリプトの出力をコピーして貼り付けてから、実行して、テーブルとマッピングを作成します。
>* コンテナー内のすべてのデータを使用する場合は、スクリプトを変更し、URL を 'https://your.blob.core.windows.net/containername;SecKey' に変更します

## <a name="query-the-exported-data-from-azure-data-explorer"></a>Azure Data Explorer からエクスポートされたデータのクエリを実行する 

マッピングを構成した後、Azure Data Explorer からエクスポートされたデータに対してクエリを実行できます。 クエリには、次の例のように、[external_table](/azure/data-explorer/kusto/query/externaltablefunction) 関数が必要です。

```kusto
external_table("HBTest","map") | take 10000
```

[![Log Analytics のエクスポートされたデータのクエリを実行する](media/azure-data-explorer-query-storage/external-table-query.png)](media/azure-data-explorer-query-storage/external-table-query.png#lightbox)

## <a name="next-steps"></a>次のステップ

- [Azure Data Explorer のクエリの記述](/azure/data-explorer/write-queries)に関するページを確認してください。