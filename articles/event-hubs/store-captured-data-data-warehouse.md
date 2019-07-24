---
title: イベント データを SQL Data Warehouse に移行する - Azure Event Hubs | Microsoft Docs
description: このチュートリアルでは、Event Grid によってトリガーされる Azure 関数を使用して、イベント ハブからデータを SQL Data Warehouse にキャプチャする方法を示します。
services: event-hubs
author: ShubhaVijayasarathy
manager: ''
ms.author: shvija
ms.custom: seodec18
ms.date: 12/06/2018
ms.topic: tutorial
ms.service: event-hubs
ms.openlocfilehash: 234febe92727e5a47d4cfc5b836cd5593e99b5b5
ms.sourcegitcommit: de81b3fe220562a25c1aa74ff3aa9bdc214ddd65
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/13/2019
ms.locfileid: "56238370"
---
# <a name="migrate-captured-event-hubs-data-to-a-sql-data-warehouse-using-event-grid-and-azure-functions"></a>Event Grid と Azure Functions を使用してキャプチャされた Event Hubs データを SQL Data Warehouse に移行する

Event Hubs [Capture](https://docs.microsoft.com/azure/event-hubs/event-hubs-capture-overview) は、Event Hubs のストリーミング データを Azure Blob Storage アカウントまたは Azure Data Lake Store に自動的に配信するもっとも簡単な方法です。 その後、データを処理して、SQL Data Warehouse や Cosmos DB などの選択した他の宛先ストレージに配信できます。 このチュートリアルでは、[Event Grid](https://docs.microsoft.com/azure/event-grid/overview) によってトリガーされる Azure 関数を使用して、イベント ハブからデータを SQL Data Warehouse にキャプチャする方法を示します。

![Visual Studio](./media/store-captured-data-data-warehouse/EventGridIntegrationOverview.PNG)

*   最初に、**Capture** 機能が有効なイベント ハブを作成し、宛先として Azure Blob Storage を設定します。 WindTurbineGenerator によって生成されたデータがイベント ハブにストリーミングされ、Avro ファイルとして Azure Storage に自動的にキャプチャされます。 
*   次に、Azure Event Grid サブスクリプションで、ソースとしての Event Hubs 名前空間とその宛先としての Azure 関数のエンドポイントを作成します。
*   新しい Avro ファイルが Event Hubs Capture 機能によって Azure Storage Blob に配信されるたびに、Event Grid は、Azure 関数に BLOB URI を通知します。 その後、関数がデータを BLOB から SQL Data Warehouse に移行します。

このチュートリアルでは、次のアクションを実行します。 

> [!div class="checklist"]
> * インフラストラクチャをデプロイする
> * 関数アプリにコードを発行する
> * 関数アプリから Event Grid サブスクリプションを作成する
> * サンプル データをイベント ハブにストリーミングする 
> * SQL Data Warehouse 内にキャプチャされたデータを確認する

## <a name="prerequisites"></a>前提条件

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

- [Visual Studio 2017 バージョン 15.3.2 以上](https://www.visualstudio.com/vs/)。 インストール時に、次のワークロードがインストールされることを確認します。.NET デスクトップ開発、Azure 開発、ASP.NET および Web 開発、Node.js 開発、Python 開発。
- [Git サンプルのダウンロード](https://github.com/Azure/azure-event-hubs/tree/master/samples/e2e/EventHubsCaptureEventGridDemo)。 サンプル ソリューションには、次のコンポーネントが含まれます。
    - *WindTurbineDataGenerator* – 風力タービンのサンプル データを Capture が有効なイベント ハブに送信する単純な発行元。
    - *FunctionDWDumper* – Azure Blob Storage に Avro ファイルがキャプチャされたときに、Event Grid の通知を受信する Azure 関数。 BLOB の URI パスを受信し、その内容を読み取り、データを SQL Data Warehouse にプッシュします。

### <a name="deploy-the-infrastructure"></a>インフラストラクチャをデプロイする
このチュートリアルで必要なインフラストラクチャをデプロイするには、Azure PowerShell または Azure CLI でこちらの[Azure Resource Manager テンプレート](https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/event-grid/EventHubsDataMigration.json)を使用します。 このテンプレートでは、次のリソースを作成します。

-   Capture 機能が有効なイベント ハブ
-   キャプチャされたイベント データ用のストレージ アカウント
-   関数アプリをホストするための Azure App Service プラン
-   ファイルをキャプチャしたイベントを処理するための関数アプリ
-   Data Warehouse をホストするための SQL Server
-   移行後のデータを格納するための SQL Data Warehouse

以降のセクションで、このチュートリアルで必要なインフラストラクチャをデプロイするための Azure CLI と Azure PowerShell のコマンドを示します。 コマンドを実行する前に、次のオブジェクトの名前を更新してください。 

- Azure リソース グループ 
- リソース グループのリージョン
- Event Hubs 名前空間
- イベント ハブ
- Azure SQL Server
- SQL ユーザー (とパスワード)
- Azure SQL データベース
- Azure Storage 
- Azure Functions App

これらのスクリプトが Azure のすべてのアーティファクトを作成するには、しばらく時間がかかります。 スクリプトが完了するまで待ってから、次に進んでください。 何らかの理由でデプロイが失敗した場合は、リソース グループを削除し、報告された問題を修正した後、コマンドを再実行してください。 

#### <a name="azure-cli"></a>Azure CLI
Azure CLI を使用してテンプレートをデプロイするには、次のコマンドを実行します。

```azurecli-interactive
az group create -l westus -n rgDataMigrationSample

az group deployment create \
  --resource-group rgDataMigrationSample \
  --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/event-grid/EventHubsDataMigration.json \
  --parameters eventHubNamespaceName=<event-hub-namespace> eventHubName=hubdatamigration sqlServerName=<sql-server-name> sqlServerUserName=<user-name> sqlServerPassword=<password> sqlServerDatabaseName=<database-name> storageName=<unique-storage-name> functionAppName=<app-name>
```

#### <a name="azure-powershell"></a>Azure PowerShell
PowerShell を使用してテンプレートをデプロイするには、次のコマンドを実行します。

```powershell
New-AzResourceGroup -Name rgDataMigration -Location westcentralus

New-AzResourceGroupDeployment -ResourceGroupName rgDataMigration -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/event-grid/EventHubsDataMigration.json -eventHubNamespaceName <event-hub-namespace> -eventHubName hubdatamigration -sqlServerName <sql-server-name> -sqlServerUserName <user-name> -sqlServerDatabaseName <database-name> -storageName <unique-storage-name> -functionAppName <app-name>
```


### <a name="create-a-table-in-sql-data-warehouse"></a>SQL Data Warehouse でテーブルを作成する 
[Visual Studio](../sql-data-warehouse/sql-data-warehouse-query-visual-studio.md)、[SQL Server Management Studio](../sql-data-warehouse/sql-data-warehouse-query-ssms.md)、またはポータルのクエリ エディターを使用して [CreateDataWarehouseTable.sql](https://github.com/Azure/azure-event-hubs/blob/master/samples/e2e/EventHubsCaptureEventGridDemo/scripts/CreateDataWarehouseTable.sql) スクリプトを実行して、SQL Data Warehouse 内にテーブルを作成します。 

```sql
CREATE TABLE [dbo].[Fact_WindTurbineMetrics] (
    [DeviceId] nvarchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NULL, 
    [MeasureTime] datetime NULL, 
    [GeneratedPower] float NULL, 
    [WindSpeed] float NULL, 
    [TurbineSpeed] float NULL
)
WITH (CLUSTERED COLUMNSTORE INDEX, DISTRIBUTION = ROUND_ROBIN);
```

## <a name="publish-code-to-the-functions-app"></a>関数アプリにコードを発行する

1. Visual Studio 2017 (15.3.2 以上) で *EventHubsCaptureEventGridDemo.sln* ソリューションを開きます。 

1. ソリューション エクスプローラーで、*[FunctionEGDWDumper]* を右クリックし、**[発行]** を選択します。

   ![関数アプリの発行](./media/store-captured-data-data-warehouse/publish-function-app.png)

1. **[Azure 関数アプリ]** を選択して、**[既存のものを選択]** を選択します。 **[発行]** を選択します。

   ![対象の関数アプリ](./media/store-captured-data-data-warehouse/pick-target.png)

1. テンプレートを使ってデプロイした関数アプリを選択します。 **[OK]** を選択します。

   ![関数アプリの選択](./media/store-captured-data-data-warehouse/select-function-app.png)

1. Visual Studio でプロファイルを構成している場合は、**[発行]** を選択します。

   ![発行の選択](./media/store-captured-data-data-warehouse/select-publish.png)

関数を発行すれば、Event Hubs からキャプチャ イベントをサブスクライブする準備が整います。


## <a name="create-an-event-grid-subscription-from-the-functions-app"></a>関数アプリから Event Grid サブスクリプションを作成する
 
1. [Azure ポータル](https://portal.azure.com/)にアクセスします。 リソース グループおよび関数アプリを選択します。

   ![関数アプリの表示](./media/store-captured-data-data-warehouse/view-function-app.png)

1. 関数を選択します。

   ![関数の選択](./media/store-captured-data-data-warehouse/select-function.png)

1. **Event Grid サブスクリプションの追加**を選択します。

   ![[サブスクリプションの追加]](./media/store-captured-data-data-warehouse/add-event-grid-subscription.png)

1. Event Grid サブスクリプションに名前をつけます。 **Event Hubs の名前空間** をイベントの種類として使用します。 Event Hubs 名前空間のインスタンスを選択する値を提供します。 指定された値としてサブスクライバーのエンドポイントのままにします。 **作成**を選択します。

   ![サブスクリプションの作成](./media/store-captured-data-data-warehouse/set-subscription-values.png)

## <a name="generate-sample-data"></a>サンプル データを作成する  
以上で、イベント ハブ、SQL Data Warehouse、Azure Function App、および Event Grid サブスクリプションのセットアップが完了しました。 ソース コードの接続文字列とイベント ハブの名前を更新した後、WindTurbineDataGenerator.exe を実行して、イベント ハブへのデータ ストリームを生成できます。 

1. ポータルで、イベント ハブの名前空間を選択します。 **[接続文字列]** を選択します。

   ![接続文字列の選択](./media/store-captured-data-data-warehouse/event-hub-connection.png)

2. **[RootManageSharedAccessKey]** を選択します。

   ![キーの選択](./media/store-captured-data-data-warehouse/show-root-key.png)

3. **[Connection string - primary Key]\(接続文字列 – 主キー\)** をコピーします

   ![キーのコピー](./media/store-captured-data-data-warehouse/copy-key.png)

4. Visual Studio プロジェクトに戻ります。 *WindTurbineDataGenerator* プロジェクトで、*program.cs* を開きます。

5. **EventHubConnectionString** 値と **EventHubName** 値を、使用するイベント ハブの接続文字列と名前に置き換えます。 

   ```cs
   private const string EventHubConnectionString = "Endpoint=sb://demomigrationnamespace.servicebus.windows.net/...";
   private const string EventHubName = "hubdatamigration";
   ```

6. ソリューションをビルドした後、WindTurbineGenerator.exe アプリケーションを実行します。 

## <a name="verify-captured-data-in-data-warehouse"></a>Data Warehouse 内にキャプチャされたデータを確認する
数分後に、SQL Data Warehouse 内のテーブルのクエリを実行します。 WindTurbineDataGenerator によって生成されたデータがイベント ハブにストリーミングされ、Azure Storage コンテナーにキャプチャされた後、Azure 関数によって SQL Data Warehouse テーブルに移行していることを確認します。  

## <a name="next-steps"></a>次の手順 
Data Warehouse で強力なデータ視覚化ツールを使用して、実用的な分析情報を取得できます。

[SQL Data Warehouse での Power BI の使用方法](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-integrate-power-bi)に関する記事を参照してください。



