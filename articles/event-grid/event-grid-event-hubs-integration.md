---
title: "Azure Event Grid と Event Hubs 統合"
description: "Azure Event Grid と Event Hubs を使用して、SQL Data Warehouse にデータを移行する方法について説明します"
services: event-grid
author: tfitzmac
manager: timlt
ms.service: event-grid
ms.topic: article
ms.date: 09/14/2017
ms.author: tomfitz
ms.translationtype: HT
ms.sourcegitcommit: d24c6777cc6922d5d0d9519e720962e1026b1096
ms.openlocfilehash: 5f35a3bdd97b72acfe06b6bec54d41814e560b1c
ms.contentlocale: ja-jp
ms.lasthandoff: 09/15/2017

---
# <a name="stream-big-data-into-a-data-warehouse"></a>ビッグ データをデータ ウェアハウスにストリーミングする

Azure [Event Grid](overview.md) は、アプリやサービスからの通知に対応するための、インテリジェントなイベント ルーティング サービスです。 [Event Hubs Capture と Event Grid のサンプル](https://github.com/Azure/azure-event-hubs/tree/master/samples/e2e/EventHubsCaptureEventGridDemo)では、Azure Event Hubs Capture と Azure Event Grid を使用して、イベント ハブから SQL Data Warehouse へデータをシームレスに移行する方法を説明しています。

![アプリケーションの概要](media/event-grid-event-hubs-integration/overview.png)

イベント ハブにデータを送信すると、Capture によりストリームからデータがプルされ、Avro 形式のデータを含むストレージ BLOB が生成されます。 Capture が BLOB を生成すると、イベントがトリガーされます。 Event Grid により、イベントに関するデータがサブスクライバーに配信されます。 ここでは、イベント データは Azure Functions エンドポイントに送信されます。 イベント データには、生成された BLOB のパスが含まれます。 関数がその URL を使用してファイルを取得し、データ ウェアハウスに送信します。

この記事では、次の内容について説明します。

* 次のようなインフラストラクチャをデプロイする
  * Capture が有効なイベント ハブ
  * Capture からのファイル用のストレージ アカウント
  * 関数アプリをホストするための Azure App Service プラン
  * イベントを処理するための関数アプリ
  * データ ウェアハウスをホストするための SQL Server
  * 移行後のデータを格納するための SQL Data Warehouse
* データ ウェアハウスのテーブルを作成する
* 関数アプリにコードを追加する
* イベントをサブスクライブする
* イベント ハブにデータを送信するアプリを実行する
* データ ウェアハウスに移行されたデータを表示する

## <a name="about-the-event-data"></a>イベント データについて

Event Grid により、イベント データがサブスクライバーに配信されます。 Capture ファイルを作成するためのイベント データを次に示します。 特に `data` オブジェクトの `fileUrl` プロパティに注意してください。 関数アプリでこの値を取得し、その値を使用して Capture ファイルを取得します。

```json
[
    {
        "topic": "/subscriptions/<guid>/resourcegroups/rgDataMigrationSample/providers/Microsoft.EventHub/namespaces/tfdatamigratens",
        "subject": "eventhubs/hubdatamigration",
        "eventType": "Microsoft.EventHub.CaptureFileCreated",
        "eventTime": "2017-08-31T19:12:46.0498024Z",
        "id": "14e87d03-6fbf-4bb2-9a21-92bd1281f247",
        "data": {
            "fileUrl": "https://tf0831datamigrate.blob.core.windows.net/windturbinecapture/tfdatamigratens/hubdatamigration/1/2017/08/31/19/11/45.avro",
            "fileType": "AzureBlockBlob",
            "partitionId": "1",
            "sizeInBytes": 249168,
            "eventCount": 1500,
            "firstSequenceNumber": 2400,
            "lastSequenceNumber": 3899,
            "firstEnqueueTime": "2017-08-31T19:12:14.674Z",
            "lastEnqueueTime": "2017-08-31T19:12:44.309Z"
        }
    }
]
```

## <a name="prerequisites"></a>前提条件

このチュートリアルを完了するには、以下が必要です。

* Azure サブスクリプション。 Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。
* [Visual Studio 2017 バージョン 15.3.2 以上](https://www.visualstudio.com/vs/)で次のワークロードを使用: .NET デスクトップ開発、Azure 開発、ASP.NET および Web 開発、Node.js 開発、Python 開発。
* お使いのコンピューターにダウンロードされた [EventHubsCaptureEventGridDemo サンプル プロジェクト](https://github.com/Azure/azure-event-hubs/tree/master/samples/e2e/EventHubsCaptureEventGridDemo)。

## <a name="deploy-the-infrastructure"></a>インフラストラクチャをデプロイする

この記事の内容を簡略化するために、Resource Manager テンプレートを使用して必要なインフラストラクチャをデプロイします。 デプロイされるリソースを確認するには、[テンプレート](https://github.com/Azure/azure-docs-json-samples/blob/master/event-grid/EventHubsDataMigration.json)をご覧ください。

Azure CLI では、次を使用します。

```azurecli-interactive
az group create -l westcentralus -n rgDataMigrationSample

az group deployment create \
  --resource-group rgDataMigrationSample \
  --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/event-grid/EventHubsDataMigration.json \
  --parameters eventHubNamespaceName=<event-hub-namespace> eventHubName=hubdatamigration sqlServerName=<sql-server-name> sqlServerUserName=<user-name> sqlServerPassword=<password> sqlServerDatabaseName=<database-name> storageName=<unique-storage-name> functionAppName=<app-name>
```

PowerShell では、次を使用します。

```powershell
New-AzureRmResourceGroup -Name rgDataMigration -Location westcentralus

New-AzureRmResourceGroupDeployment -ResourceGroupName rgDataMigration -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/event-grid/EventHubsDataMigration.json -eventHubNamespaceName <event-hub-namespace> -eventHubName hubdatamigration -sqlServerName <sql-server-name> -sqlServerUserName <user-name> -sqlServerDatabaseName <database-name> -storageName tf08202storage -functionAppName <app-name>
```

入力を求められたら、パスワードを入力します。

## <a name="create-a-table-in-sql-data-warehouse"></a>SQL Data Warehouse でテーブルを作成する

[CreateDataWarehouseTable.sql](https://github.com/Azure/azure-event-hubs/blob/master/samples/e2e/EventHubsCaptureEventGridDemo/scripts/CreateDataWarehouseTable.sql) スクリプトを実行して、データ ウェアハウスにテーブルを追加します。 スクリプトを実行するには、ポータルで Visual Studio またはクエリ エディターを使用します。

実行するスクリプトは次のとおりです。

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

## <a name="publish-the-azure-functions-app"></a>Azure Functions アプリを発行する

1. [EventHubsCaptureEventGridDemo サンプル プロジェクト](https://github.com/Azure/azure-event-hubs/tree/master/samples/e2e/EventHubsCaptureEventGridDemo)を Visual Studio 2017 (15.3.2 以上) で開きます。

2. ソリューション エクスプローラーで、**[FunctionDWDumper]** を右クリックし、**[発行]** を選択します。

   ![関数アプリの発行](media/event-grid-event-hubs-integration/publish-function-app.png)

3. **[Azure 関数アプリ]** を選択して、**[既存のものを選択]** を選択します。 **[OK]**を選択します。

   ![対象の関数アプリ](media/event-grid-event-hubs-integration/pick-target.png)

4. テンプレートを使ってデプロイした関数アプリを選択します。 **[OK]**を選択します。

   ![関数アプリの選択](media/event-grid-event-hubs-integration/select-function-app.png)

5. Visual Studio でプロファイルを構成している場合は、**[発行]** を選択します。

   ![発行の選択](media/event-grid-event-hubs-integration/select-publish.png)

6. 関数を発行したら、[Azure Portal](https://portal.azure.com/) に移動します。 リソース グループおよび関数アプリを選択します。

   ![関数アプリの表示](media/event-grid-event-hubs-integration/view-function-app.png)

7. 関数を選択します。

   ![関数の選択](media/event-grid-event-hubs-integration/select-function.png)

8. 関数の URL を取得します。 イベント サブスクリプションを作成するときにこの URL が必要になります。

   ![関数の URL の取得](media/event-grid-event-hubs-integration/get-function-url.png)

9. 値をコピーします。

   ![URL のコピー](media/event-grid-event-hubs-integration/copy-url.png)

## <a name="subscribe-to-the-event"></a>イベントをサブスクライブする

Azure CLI またはポータルを使用すると、イベントをサブスクライブできます。 このトピックでは、両方の方法を説明します。

### <a name="portal"></a>ポータル

1. Event Hubs 名前空間で、左側の **[Event Grid]** を選択します。

   ![Event Grid の選択](media/event-grid-event-hubs-integration/select-event-grid.png)

2. イベント サブスクリプションを追加します。

   ![イベント サブスクリプションの追加](media/event-grid-event-hubs-integration/add-event-subscription.png)

3. イベント サブスクリプションの値を指定します。 コピーした Azure Functions URL を使用します。 **[作成]**を選択します。

   ![サブスクリプション値の指定](media/event-grid-event-hubs-integration/provide-values.png)

### <a name="azure-cli"></a>Azure CLI

イベントをサブスクライブするには、次のコマンドを実行します。

```azurecli-interactive
az eventgrid resource event-subscription create -g rgDataMigrationSample --provider-namespace Microsoft.EventHub --resource-type namespaces --resource-name <your-EventHubs-namespace> --name captureEventSub --endpoint <your-function-endpoint>
```

## <a name="run-the-app-to-generate-data"></a>データを生成するアプリを実行する

イベント ハブ、SQL Data Warehouse、Azure 関数アプリ、イベント サブスクリプションのセットアップが完了しました。 イベント ハブからデータ ウェアハウスにデータを移行する準備が整いました。 イベント ハブのデータを生成するアプリケーションを実行する前に、いくつかの値を構成する必要があります。

1. ポータルで、イベント ハブの名前空間を選択します。 **[接続文字列]** を選択します。

   ![接続文字列の選択](media/event-grid-event-hubs-integration/event-hub-connection.png)

2. **[RootManageSharedAccessKey]** を選択します。

   ![キーの選択](media/event-grid-event-hubs-integration/show-root-key.png)

3. **[Connection string - Primary Key]\(接続文字列 – 主キー\)** をコピーします。

   ![キーのコピー](media/event-grid-event-hubs-integration/copy-key.png)

4. Visual Studio プロジェクトに戻ります。 WindTurbineDataGenerator プロジェクトで、**program.cs** を開きます。

5. 2 つの定数の値を置き換えます。 **EventHubConnectionString** には、コピーした値を使用します。 **EventHubName** にはイベント ハブ名を使用します。

   ```cs
   private const string EventHubConnectionString = "Endpoint=sb://tfdatamigratens.servicebus.windows.net/...";
   private const string EventHubName = "hubdatamigration";
   ```

6. ソリューションをビルドします。 WindTurbineGenerator.exe アプリケーションを実行します。 数分後に、データ ウェアハウスのテーブルにクエリを実行し、移行されたデータを確認します。

## <a name="next-steps"></a>次のステップ

* Event Grid の概要については、[Event Grid の紹介](overview.md)に関する記事を参照してください。
* Event Hubs Capture の概要については、「[Azure Portal を使用して Event Hubs Capture を有効にする](../event-hubs/event-hubs-capture-enable-through-portal.md)」をご覧ください。
* サンプルの設定と実行に関する詳細については、[Event Hubs Capture と Event Grid のサンプル](https://github.com/Azure/azure-event-hubs/tree/master/samples/e2e/EventHubsCaptureEventGridDemo)に関する記事をご覧ください。
