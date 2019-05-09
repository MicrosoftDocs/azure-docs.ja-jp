---
title: Event Hubs データをデータ ウェアハウスに送信する - Event Grid
description: Azure Event Grid と Event Hubs を使用して、SQL データ ウェアハウスにデータを移行する方法について説明します。 ここでは、Capture ファイルを取得するために Azure 関数が使用されます。
services: event-grid
author: spelluru
manager: timlt
ms.service: event-grid
ms.topic: tutorial
ms.date: 01/13/2019
ms.author: spelluru
ms.openlocfilehash: c2c49563bf505ce70c4900c6c0a8e41c0f6ac9c5
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2019
ms.locfileid: "58176618"
---
# <a name="tutorial-stream-big-data-into-a-data-warehouse"></a>チュートリアル: ビッグ データをデータ ウェアハウスにストリーミングする
Azure [Event Grid](overview.md) は、アプリとサービスからの通知 (イベント) への対応を可能にするインテリジェントなイベント ルーティング サービスです。 たとえば、Azure BLOB ストレージや Azure Data Lake Storage にキャプチャされた Event Hubs データを処理する Azure 関数をトリガーして、データを別のデータ リポジトリに移行できます。 この [Event Hubs と Event Grid の統合のサンプル](https://github.com/Azure/azure-event-hubs/tree/master/samples/e2e/EventHubsCaptureEventGridDemo)では、Event Hubs と Event Grid を使用して、キャプチャされた Event Hubs データを BLOB ストレージから SQL データ ウェアハウスにシームレスに移行する方法を説明しています。

![アプリケーションの概要](media/event-grid-event-hubs-integration/overview.png)

この図は、このチュートリアルでお客様が作成するソリューションのワークフローを示しています。 

1. Azure イベント ハブに送信されたデータが Azure BLOB ストレージにキャプチャされます。
2. データのキャプチャが完了すると、イベントが生成されて Azure イベント グリッドに送信されます。 
3. イベント グリッドによって、このイベント データが Azure 関数アプリに転送されます。
4. 関数アプリによってイベント データの BLOB URL が使用され、ストレージから BLOB が取得されます。 
5. 関数アプリによって BLOB データが Azure SQL データ ウェアハウスに移行されます。 

この記事では、次の手順を実行します。

> [!div class="checklist"]
> * Azure Resource Manager テンプレートを使用して、インフラストラクチャ (イベント ハブ、ストレージ アカウント、関数アプリ、SQL データ ウェアハウス) をデプロイする。
> * データ ウェアハウスのテーブルを作成する。
> * 関数アプリにコードを追加する。
> * イベントをサブスクライブする。 
> * イベント ハブにデータを送信するアプリを実行する。
> * データ ウェアハウスに移行されたデータを表示する。

## <a name="prerequisites"></a>前提条件

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

このチュートリアルを完了するには、以下が必要です。

* Azure サブスクリプション。 Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/) を作成してください。
* [Visual Studio 2017 バージョン 15.3.2 以上](https://www.visualstudio.com/vs/)で次のワークロードを使用: .NET デスクトップ開発、Azure 開発、ASP.NET および Web 開発、Node.js 開発、Python 開発。
* 自分のコンピューターに [EventHubsCaptureEventGridDemo サンプル プロジェクト](https://github.com/Azure/azure-event-hubs/tree/master/samples/e2e/EventHubsCaptureEventGridDemo)をダウンロードします。

## <a name="deploy-the-infrastructure"></a>インフラストラクチャをデプロイする
この手順では、[Resource Manager テンプレート](https://github.com/Azure/azure-docs-json-samples/blob/master/event-grid/EventHubsDataMigration.json)を使用して必要なインフラストラクチャをデプロイします。 テンプレートをデプロイすると、次のリソースが作成されます。

* Capture 機能が有効なイベント ハブ
* キャプチャされたファイル用のストレージ アカウント 
* 関数アプリをホストするための App Service プラン
* イベントを処理するための関数アプリ
* データ ウェアハウスをホストするための SQL Server
* 移行後のデータを格納するための SQL Data Warehouse

### <a name="launch-azure-cloud-shell-in-azure-portal"></a>Azure portal で Azure Cloud Shell を起動する

1. [Azure Portal](https://portal.azure.com) にサインインします。 
2. 上部にある **[Cloud Shell]** ボタンを選択します。

    ![Azure ポータル](media/event-grid-event-hubs-integration/azure-portal.png)
3. ブラウザーの下部に Cloud Shell が開かれるのがわかります。

    ![Cloud Shell](media/event-grid-event-hubs-integration/launch-cloud-shell.png) 
4. Cloud Shell で、**[Bash]** と **[PowerShell]** のどちらかを選択するオプションが表示されたら、**[Bash]** を選択します。 
5. Cloud Shell を初めて使用する場合は、**[ストレージの作成]** を選択してストレージ アカウントを作成します。 Azure Cloud Shell では、一部のファイルを格納するために Azure ストレージ アカウントが必要です。 

    ![Cloud Shell 用のストレージを作成する](media/event-grid-event-hubs-integration/create-storage-cloud-shell.png)
6. Cloud Shell が初期化されるまで待ちます。 

    ![Cloud Shell 用のストレージを作成する](media/event-grid-event-hubs-integration/cloud-shell-initialized.png)


### <a name="use-azure-cli"></a>Azure CLI の使用

1. 次の CLI コマンドを実行して、Azure リソース グループを作成します。 
    1. 次のコマンドをコピーして Cloud Shell ウィンドウに貼り付けます。

        ```azurecli
        az group create -l eastus -n <Name for the resource group>
        ```
    1. **リソース グループ**の名前を指定します。
    2. **Enter**キーを押します。 

        たとえば次のようになります。
    
        ```azurecli
        user@Azure:~$ az group create -l eastus -n ehubegridgrp
        {
          "id": "/subscriptions/00000000-0000-0000-0000-0000000000000/resourceGroups/ehubegridgrp",
          "location": "eastus",
          "managedBy": null,
          "name": "ehubegridgrp",
          "properties": {
            "provisioningState": "Succeeded"
          },
          "tags": null
        }
        ```
2. 次の CLI コマンドを実行して、前のセクションで説明したリソース (イベント ハブ、ストレージ アカウント、関数アプリ、SQL データ ウェアハウス) をすべてデプロイします。 
    1. コマンドをコピーして Cloud Shell ウィンドウに貼り付けます。 または、お好みのエディターにコピーして貼り付け、値を設定してから、コマンドを Cloud Shell にコピーすることもできます。 

        ```azurecli
        az group deployment create \
            --resource-group rgDataMigrationSample \
            --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/event-grid/EventHubsDataMigration.json \
            --parameters eventHubNamespaceName=<event-hub-namespace> eventHubName=hubdatamigration sqlServerName=<sql-server-name> sqlServerUserName=<user-name> sqlServerPassword=<password> sqlServerDatabaseName=<database-name> storageName=<unique-storage-name> functionAppName=<app-name>
        ```
    2. 次のエンティティの値を指定します。
        1. お客様が先ほど作成したリソース グループの名前。
        2. イベント ハブ名前空間の名前。 
        3. イベント ハブの名前。 値はそのまま (hubdatamigration) でもかまいません。
        4. SQL サーバーの名前。
        5. SQL ユーザーの名前とパスワード。 
        6. SQL データ ウェアハウスの名前。
        7. ストレージ アカウントの名前。 
        8. 関数アプリの名前。 
    3.  Cloud Shell ウィンドウで **Enter** キーを押して、コマンドを実行します。 多数のリソースを作成するため、このプロセスには時間がかかる場合があります。 コマンドの結果で、エラーがないことを確認します。 
    

### <a name="use-azure-powershell"></a>Azure PowerShell の使用

1. Azure Cloud Shell で PowerShell モードに切り替えます。 Azure Cloud Shell の左上隅にある下矢印を選択し、**[PowerShell]** を選択します。

    ![PowerShell に切り替える](media/event-grid-event-hubs-integration/select-powershell-cloud-shell.png)
2. 次のコマンドを実行して、Azure リソース グループを作成します。 
    1. 次のコマンドをコピーして Cloud Shell ウィンドウに貼り付けます。

        ```powershell
        New-AzResourceGroup -Name rgDataMigration -Location westcentralus
        ```
    2. **リソース グループ**の名前を指定します。
    3. Enter キーを押します。 
3. 次のコマンドを実行して、前のセクションで説明したリソース (イベント ハブ、ストレージ アカウント、関数アプリ、SQL データ ウェアハウス) をすべてデプロイします。
    1. コマンドをコピーして Cloud Shell ウィンドウに貼り付けます。 または、お好みのエディターにコピーして貼り付け、値を設定してから、コマンドを Cloud Shell にコピーすることもできます。 

        ```powershell
        New-AzResourceGroupDeployment -ResourceGroupName rgDataMigration -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/event-grid/EventHubsDataMigration.json -eventHubNamespaceName <event-hub-namespace> -eventHubName hubdatamigration -sqlServerName <sql-server-name> -sqlServerUserName <user-name> -sqlServerDatabaseName <database-name> -storageName <unique-storage-name> -functionAppName <app-name>
        ```
    2. 次のエンティティの値を指定します。
        1. お客様が先ほど作成したリソース グループの名前。
        2. イベント ハブ名前空間の名前。 
        3. イベント ハブの名前。 値はそのまま (hubdatamigration) でもかまいません。
        4. SQL サーバーの名前。
        5. SQL ユーザーの名前とパスワード。 
        6. SQL データ ウェアハウスの名前。
        7. ストレージ アカウントの名前。 
        8. 関数アプリの名前。 
    3.  Cloud Shell ウィンドウで **Enter** キーを押して、コマンドを実行します。 多数のリソースを作成するため、このプロセスには時間がかかる場合があります。 コマンドの結果で、エラーがないことを確認します。 

### <a name="close-the-cloud-shell"></a>Cloud Shell を閉じる 
ポータルの **[Cloud Shell]** ボタンまたは Cloud Shell ウィンドウの右上隅にある **[X]** ボタンを選択して Cloud Shell を閉じます。 

### <a name="verify-that-the-resources-are-created"></a>リソースが作成されたことを確認する

1. Azure portal で、左側のメニューにある **[リソース グループ]** を選択します。 
2. お客様のリソース グループの名前を検索ボックスに入力して、リソース グループの一覧をフィルター処理します。 
3. 一覧で、お客様のリソース グループを選択します。

    ![リソース グループを選択します](media/event-grid-event-hubs-integration/select-resource-group.png)
4. 次のリソースがリソース グループに表示されていることを確認します。

    ![リソース グループ内のリソース](media/event-grid-event-hubs-integration/resources-in-resource-group.png)

### <a name="create-a-table-in-sql-data-warehouse"></a>SQL Data Warehouse でテーブルを作成する
[CreateDataWarehouseTable.sql](https://github.com/Azure/azure-event-hubs/blob/master/samples/e2e/EventHubsCaptureEventGridDemo/scripts/CreateDataWarehouseTable.sql) スクリプトを実行して、お客様のデータ ウェアハウスにテーブルを作成します。 スクリプトを実行するには、Visual Studio を使用できます。または、ポータルのクエリ エディターを使用できます。 次の手順では、クエリ エディターの使用方法について説明します。 

1. リソース グループ内のリソースの一覧で、お客様の SQL データ ウェアハウスを選択します。 
2. SQL データ ウェアハウスのページで、左側のメニューの **[クエリ エディター (プレビュー)]** を選択します。 

    ![SQL データ ウェアハウスのページ](media/event-grid-event-hubs-integration/sql-data-warehouse-page.png)
2. SQL サーバーの**ユーザー**の名前と**パスワード**を入力し、**[OK]** を選択します。 

    ![SQL Server 認証](media/event-grid-event-hubs-integration/sql-server-authentication.png)
4. クエリ ウィンドウに、次の SQL スクリプトをコピーして実行します。 

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

    ![SQL クエリを実行する](media/event-grid-event-hubs-integration/run-sql-query.png)
5. データが作成されたことをチュートリアルの最後に確認できるように、このタブまたはウィンドウを開いたままにしておきます。 


## <a name="publish-the-azure-functions-app"></a>Azure Functions アプリを発行する

1. Visual Studio 2017 を起動します。 
2. お客様が前提条件の一環として [GitHub](https://github.com/Azure/azure-event-hubs/tree/master/samples/e2e/EventHubsCaptureEventGridDemo) からダウンロードした **EventHubsCaptureEventGridDemo.sln** ソリューションを開きます。
3. ソリューション エクスプローラーで、**[FunctionEGDWDumper]** を右クリックし、**[発行]** を選択します。

   ![関数アプリの発行](media/event-grid-event-hubs-integration/publish-function-app.png)
4. 次の画面が表示されたら、**[開始]** を選択します。 

   ![発行の開始ボタン](media/event-grid-event-hubs-integration/start-publish-button.png) 
5. **[発行先を選択]** ページで、**[既存のものを選択]** オプションを選択し、**[プロファイルの作成]** を選択します。 

   ![発行先の選択](media/event-grid-event-hubs-integration/publish-select-existing.png)
6. [App Service] ページで、お客様の **Azure サブスクリプション**を選択し、お客様のリソース グループ内の**関数アプリ**を選択して、**[OK]** をクリックします。 

   ![[App Service] ページ](media/event-grid-event-hubs-integration/publish-app-service.png) 
1. Visual Studio でプロファイルを構成している場合は、**[発行]** を選択します。

   ![発行の選択](media/event-grid-event-hubs-integration/select-publish.png)

関数を発行すれば、イベントをサブスクライブする準備が整います。

## <a name="subscribe-to-the-event"></a>イベントをサブスクライブする

1. Web ブラウザーの新しいタブまたはウィンドウで [Azure portal](https://portal.azure.com) に移動します。
2. Azure portal で、左側のメニューにある **[リソース グループ]** を選択します。 
3. お客様のリソース グループの名前を検索ボックスに入力して、リソース グループの一覧をフィルター処理します。 
4. 一覧で、お客様のリソース グループを選択します。

    ![リソース グループを選択します](media/event-grid-event-hubs-integration/select-resource-group.png)
4. 一覧で、App Service プランを選択します。 
5. [App Service プラン] ページで、左側のメニューの **[アプリ]** を選択し、関数アプリを選択します。 

    ![関数アプリを選択する](media/event-grid-event-hubs-integration/select-function-app-app-service-plan.png)
6. 関数アプリ、関数の順に展開してから、お客様の関数を選択します。 

    ![Azure 関数を選択](media/event-grid-event-hubs-integration/select-function-add-button.png)
7. ツール バーの **[Event Grid サブスクリプションの追加]** を選択します。 
8. **[Create Event Grid Subscription]\(Event Grid サブスクリプションの作成\)** ページで、次の操作を行います。 
    1. **[トピックの詳細]** セクションで、次の操作を行います。
        1. Azure サブスクリプションを選択します。
        2. Azure リソース グループを選択します。
        3. Event Hubs 名前空間を選択します。
    2. **[イベント サブスクリプションの詳細]** ページで、サブスクリプションの名前 (captureEventSub など) を入力し、**[作成]** を選択します。 

        ![Event Grid サブスクリプションを作成する](media/event-grid-event-hubs-integration/create-event-subscription.png)

## <a name="run-the-app-to-generate-data"></a>データを生成するアプリを実行する
イベント ハブ、SQL Data Warehouse、Azure 関数アプリ、イベント サブスクリプションのセットアップが完了しました。 イベント ハブのデータを生成するアプリケーションを実行する前に、いくつかの値を構成する必要があります。

1. Azure portal で、先ほど移動したお客様のリソース グループに移動します。 
2. Event Hubs 名前空間を選択します。
3. **[Event Hubs 名前空間]** ページで、左側のメニューの **[共有アクセス ポリシー]** を選択します。
4. ポリシーの一覧で **RootManageSharedAccessKey** を選択します。 
5. **[接続文字列 - 主キー]** テキスト ボックスの隣にあるコピー ボタンを選択します。 

    ![イベント ハブ名前空間の接続文字列](media/event-grid-event-hubs-integration/get-connection-string.png)
1. お客様の Visual Studio ソリューションに戻ります。 
2. WindTurbineDataGenerator プロジェクトで、**program.cs** を開きます。
5. 2 つの定数の値を置き換えます。 **EventHubConnectionString** には、コピーした値を使用します。 **hubdatamigration**イベント ハブの名前を使用します。 イベント ハブに別の名前を使用した場合は、その名前を指定します。 

   ```cs
   private const string EventHubConnectionString = "Endpoint=sb://demomigrationnamespace.servicebus.windows.net/...";
   private const string EventHubName = "hubdatamigration";
   ```

6. ソリューションをビルドします。 **WindTurbineGenerator.exe** アプリケーションを実行します。 
7. 数分後に、データ ウェアハウスのテーブルにクエリを実行し、移行されたデータを確認します。

    ![Query results](media/event-grid-event-hubs-integration/query-results.png)

### <a name="event-data-generated-by-the-event-hub"></a>イベント ハブによって生成されたイベント データ
Event Grid により、イベント データがサブスクライバーに配信されます。 次の例は、イベント ハブを介したデータ ストリーミングが BLOB にキャプチャされるときに生成されるデータを示します。 特に、`data` オブジェクトの `fileUrl` プロパティがストレージ内の BLOB を指すことに注意してください。 関数アプリによってこの URL が使用され、キャプチャされたデータを含む BLOB ファイルが取得されます。

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


## <a name="next-steps"></a>次の手順

* Azure メッセージング サービスの違いについては、「[メッセージを配信する Azure サービスの選択](compare-messaging-services.md)」を参照してください。
* Event Grid の概要については、[Event Grid の紹介](overview.md)に関する記事を参照してください。
* Event Hubs Capture の概要については、「[Azure Portal を使用して Event Hubs Capture を有効にする](../event-hubs/event-hubs-capture-enable-through-portal.md)」をご覧ください。
* サンプルの設定と実行に関する詳細については、[Event Hubs Capture と Event Grid のサンプル](https://github.com/Azure/azure-event-hubs/tree/master/samples/e2e/EventHubsCaptureEventGridDemo)に関する記事をご覧ください。
