---
title: Azure Cosmos DB の Table API アカウントに既存のデータを移行する
description: Azure Cosmos DB の Azure Table API アカウントに、オンプレミスまたはクラウドのデータを移行またはインポートする方法について説明します。
author: snehagunda
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.topic: tutorial
ms.date: 12/07/2017
ms.author: sngun
ms.custom: seodec18
ms.openlocfilehash: 5c828644cb03d83df38265719cd8afabc24cf739
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/27/2019
ms.locfileid: "66242583"
---
# <a name="migrate-your-data-to-azure-cosmos-db-table-api-account"></a>Azure Cosmos DB Table API アカウントにデータを移行する

このチュートリアルでは、Azure Cosmos DB [Table API](table-introduction.md) で使用するデータをインポートする手順を示します。 Azure Table Storage に格納されたデータがある場合は、データ移行ツールまたは AzCopy を使用してデータを Azure Cosmos DB Table API にインポートできます。 Azure Cosmos DB Table API (プレビュー) アカウントに格納されたデータがある場合、データの移行にはデータ移行ツールを使用する必要があります。 

このチュートリアルに含まれるタスクは次のとおりです。

> [!div class="checklist"]
> * データ移行ツールを使用したデータのインポート
> * AzCopy を使用したデータのインポート
> * Table API (プレビュー) から Table API への移行 

## <a name="prerequisites"></a>前提条件

* **スループットを上げる:** データの移行にかかる時間は、個別のコンテナーまたは一連のコンテナーに対して設定したスループットの量に依存します。 大規模なデータ移行では、スループットが上がっていることを確認します。 移行が完了したら、コストを節約するためにスループットを下げます。 Azure Portal でスループットを上げることの詳細については、Azure Cosmos DB のパフォーマンス レベルと価格レベルに関するページを参照してください。

* **Azure Cosmos DB リソースを作成する:** データの移行を開始する前に、Azure portal からすべてのテーブルを事前に作成します。 データベース レベルのスループットがある Azure Cosmos DB アカウントに移行しようとしている場合は、Azure Cosmos DB テーブルの作成時に必ずパーティション キーを提供するようにしてください。

## <a name="data-migration-tool"></a>データ移行ツール

コマンド ラインの Azure Cosmos DB データ移行ツール (dt.exe) を使用すると、既存の Azure Table ストレージ データを Table API GA アカウントにインポートしたり、Table API (プレビュー) アカウントのデータを Table API GA アカウントに移行したりすることができます。 その他のソースは現在はサポートされていません。 現在、UI ベースのデータ移行ツール (dtui.exe) は、テーブル API アカウントに対してはサポートされていません。 

テーブル データの移行を実行するには、次のタスクを実行します。

1. 移行ツールを [GitHub](https://github.com/azure/azure-documentdb-datamigrationtool) からダウンロードします。
2. 実際のシナリオに合ったコマンド ライン引数を使用して `dt.exe` を実行します。 `dt.exe` は次の形式のコマンドを受け取ります。

   ```bash
    dt.exe [/<option>:<value>] /s:<source-name> [/s.<source-option>:<value>] /t:<target-name> [/t.<target-option>:<value>] 
   ```

コマンドのオプションは次のとおりです。

    /ErrorLog: Optional. Name of the CSV file to redirect data transfer failures
    /OverwriteErrorLog: Optional. Overwrite error log file
    /ProgressUpdateInterval: Optional, default is 00:00:01. Time interval to refresh on-screen data transfer progress
    /ErrorDetails: Optional, default is None. Specifies that detailed error information should be displayed for the following errors: None, Critical, All
    /EnableCosmosTableLog: Optional. Direct the log to a cosmos table account. If set, this defaults to destination account connection string unless /CosmosTableLogConnectionString is also provided. This is useful if multiple instances of DT are being run simultaneously.
    /CosmosTableLogConnectionString: Optional. ConnectionString to direct the log to a remote cosmos table account. 

### <a name="command-line-source-settings"></a>コマンドラインのソース設定

移行のソースとして Azure Table Storage または Table API プレビュー を定義する場合は、次のソース オプションを使用します。

    /s:AzureTable: Reads data from Azure Table storage
    /s.ConnectionString: Connection string for the table endpoint. This can be retrieved from the Azure portal
    /s.LocationMode: Optional, default is PrimaryOnly. Specifies which location mode to use when connecting to Azure Table storage: PrimaryOnly, PrimaryThenSecondary, SecondaryOnly, SecondaryThenPrimary
    /s.Table: Name of the Azure Table
    /s.InternalFields: Set to All for table migration as RowKey and PartitionKey are required for import.
    /s.Filter: Optional. Filter string to apply
    /s.Projection: Optional. List of columns to select

Azure Table Storage からインポートする場合にソースの接続文字列を取得するには、Azure Portal を開き、 **[ストレージ アカウント]**  >  **[アカウント]**  >  **[アクセス キー]** の順にクリックし、[コピー] ボタンを使用して **[接続文字列]** をコピーします。

![HBase のソース オプションのスクリーンショット](./media/table-import/storage-table-access-key.png)

Azure Cosmos DB Table API (プレビュー) からインポートする場合にソースの接続文字列を取得するには、Azure Portal を開き、 **[Azure Cosmos DB]**  >  **[アカウント]**  >  **[接続文字列]** の順にクリックし、[コピー] ボタンを使用して **[接続文字列]** をコピーします。

![HBase のソース オプションのスクリーンショット](./media/table-import/cosmos-connection-string.png)

[Azure Table Storage のサンプル コマンド](#azure-table-storage)

[Azure Cosmos DB Table API (プレビュー) のサンプル コマンド](#table-api-preview)

### <a name="command-line-target-settings"></a>コマンドラインのターゲット設定

移行のターゲットとして Azure Cosmos DB Table API を定義する場合は、次のターゲット オプションを使用します。

    /t:TableAPIBulk: Uploads data into Azure CosmosDB Table in batches
    /t.ConnectionString: Connection string for the table endpoint
    /t.TableName: Specifies the name of the table to write to
    /t.Overwrite: Optional, default is false. Specifies if existing values should be overwritten
    /t.MaxInputBufferSize: Optional, default is 1GB. Approximate estimate of input bytes to buffer before flushing data to sink
    /t.Throughput: Optional, service defaults if not specified. Specifies throughput to configure for table
    /t.MaxBatchSize: Optional, default is 2MB. Specify the batch size in bytes

<a id="azure-table-storage"></a>
### <a name="sample-command-source-is-azure-table-storage"></a>サンプル コマンド:ソースが Azure Table Storage

Azure Table Storage から Table API にインポートするコマンドライン サンプルを以下に示します。

```
dt /s:AzureTable /s.ConnectionString:DefaultEndpointsProtocol=https;AccountName=<Azure Table storage account name>;AccountKey=<Account Key>;EndpointSuffix=core.windows.net /s.Table:<Table name> /t:TableAPIBulk /t.ConnectionString:DefaultEndpointsProtocol=https;AccountName=<Azure Cosmos DB account name>;AccountKey=<Azure Cosmos DB account key>;TableEndpoint=https://<Account name>.table.cosmosdb.azure.com:443 /t.TableName:<Table name> /t.Overwrite
```
<a id="table-api-preview"></a>
### <a name="sample-command-source-is-azure-cosmos-db-table-api-preview"></a>サンプル コマンド:ソースが Azure Cosmos DB Table API (プレビュー)

Table API プレビュー から Table API GA にインポートするコマンドライン サンプルを以下に示します。

```
dt /s:AzureTable /s.ConnectionString:DefaultEndpointsProtocol=https;AccountName=<Table API preview account name>;AccountKey=<Table API preview account key>;TableEndpoint=https://<Account Name>.documents.azure.com; /s.Table:<Table name> /t:TableAPIBulk /t.ConnectionString:DefaultEndpointsProtocol=https;AccountName=<Azure Cosmos DB account name>;AccountKey=<Azure Cosmos DB account key>;TableEndpoint=https://<Account name>.table.cosmosdb.azure.com:443 /t.TableName:<Table name> /t.Overwrite
```

## <a name="migrate-data-by-using-azcopy"></a>AzCopy を使用してデータを移行する

Azure Table Sorage から Azure Cosmos DB Table API にデータを移行するには、AzCopy コマンドライン ユーティリティを使用する方法もあります。 AzCopy を使用するには、まず「[Table Storage からデータをエクスポートする](https://docs.microsoft.com/previous-versions/azure/storage/storage-use-azcopy#export-data-from-table-storage)」の説明に従ってデータをエクスポートし、[Azure Cosmos DB Table API](https://docs.microsoft.com/previous-versions/azure/storage/storage-use-azcopy#import-data-into-table-storage) に関するページの説明に従ってデータを Azure Cosmos DB にインポートします。

Azure Cosmos DB へのインポートを実行する場合は、次のサンプルを参照してください。 /Dest 値には、core ではなく cosmosdb を使用してください。

インポート コマンド例:

```
AzCopy /Source:C:\myfolder\ /Dest:https://myaccount.table.cosmosdb.windows.net/mytable1/ /DestKey:key /Manifest:"myaccount_mytable_20140103T112020.manifest" /EntityOperation:InsertOrReplace
```

## <a name="migrate-from-table-api-preview-to-table-api"></a>Table API (プレビュー) から Table API への移行

> [!WARNING]
> 一般公開のテーブルを活用したい場合は、このセクションの説明に従って既存のプレビュー テーブルを移行してください。手動で移行しない場合、今後数週間以内に既存のプレビュー ユーザーについて自動移行が実行される予定です。ただし、自動移行されたプレビュー テーブルには、新規作成のテーブルにはない特定の制限が課せられます。
> 

Table API は一般公開 (GA) されました。 プレビュー バージョンと GA バージョンのテーブルには違いがあります。違いは、クラウドで実行されるコードだけでなく、クライアントで実行されるコードにもあります。 そのため、プレビューの SDK クライアントと GA Table API アカウントを混在させることはお勧めしません。 Table API プレビューを使用していて、既存のテーブルを運用環境でも引き続き使用するには、プレビューから GA 環境に移行するか、自動移行を待つ必要があります。 自動移行を待つ場合、移行されたテーブルに課せられる制限が通知されます。 移行後は、既存のアカウントを使用して制限なしで新しいテーブルを作成できるようになります (移行されたテーブルのみに制限が課せられます)。

Table API (プレビュー) から一般公開の Table API に移行するには、次の手順を実行します。

1. 「[データベース アカウントの作成](create-table-dotnet.md#create-a-database-account)」の説明に従って新しい Azure Cosmos DB アカウントを作成し、API の種類を Azure Table に設定します。

2. [Table API SDK](table-sdk-dotnet.md) の GA リリースを使用するようにクライアントを変更します。

3. データ移行ツールを使用して、プレビュー テーブルのクライアント データを GA テーブルに移行します。 この目的でデータ移行ツールを使用する手順については、「[データ移行ツール](#data-migration-tool)」を参照してください。 

## <a name="next-steps"></a>次の手順

このチュートリアルで学習した内容は次のとおりです。

> [!div class="checklist"]
> * データ移行ツールを使用したデータのインポート
> * AzCopy を使用したデータのインポート
> * Table API (プレビュー) から Table API への移行

次のチュートリアルに進み、Azure Cosmos DB Table API を使用してデータにクエリを実行する方法を学ぶことができます。 

> [!div class="nextstepaction"]
>[データにクエリを実行する方法](../cosmos-db/tutorial-query-table.md)
