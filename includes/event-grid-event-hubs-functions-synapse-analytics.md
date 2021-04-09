---
title: インクルード ファイル
description: インクルード ファイル
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: include
ms.date: 12/07/2020
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: d6b8b35b36830568d6ff8c46a381fec0cfc57e84
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "96854270"
---
:::image type="content" source="media/event-grid-event-hubs-functions-synapse-analytics/overview.png" alt-text="アプリケーションの概要":::

この図は、このチュートリアルでお客様が作成するソリューションのワークフローを示しています。 

1. Azure イベント ハブに送信されたデータが Azure BLOB ストレージにキャプチャされます。
2. データのキャプチャが完了すると、イベントが生成されて Azure イベント グリッドに送信されます。 
3. イベント グリッドによって、このイベント データが Azure 関数アプリに転送されます。
4. 関数アプリによってイベント データの BLOB URL が使用され、ストレージから BLOB が取得されます。 
5. 関数アプリによって BLOB データが Azure Synapse Analytics に移行されます。 

この記事では、次の手順を実行します。

> [!div class="checklist"]
> - チュートリアルに必要なインフラストラクチャをデプロイする
> - 関数アプリにコードを発行する
> - Event Grid のサブスクリプションを作成する 
> - サンプル データを Event Hubs にストリーム配信する
> - キャプチャされたデータを Azure Synapse Analytics で確認する

## <a name="prerequisites"></a>前提条件
このチュートリアルを完了するには、以下が必要です。

- Azure サブスクリプション。 Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/) を作成してください。
- [Visual Studio 2019](https://www.visualstudio.com/vs/) で次のワークロードを使用: .NET デスクトップ開発、Azure 開発、ASP.NET および Web 開発、Node.js 開発、Python 開発。
- 自分のコンピューターに [EventHubsCaptureEventGridDemo サンプル プロジェクト](https://github.com/Azure/azure-event-hubs/tree/master/samples/e2e/EventHubsCaptureEventGridDemo)をダウンロードします。
    - WindTurbineDataGenerator - 風力タービンのサンプル データをキャプチャが有効なイベント ハブに送信する単純な発行元
    - FunctionDWDumper – Azure Blob Storage に Avro ファイルがキャプチャされたときに、Event Grid の通知を受信する Azure 関数。 BLOB の URI パスを受信し、その内容を読み取り、そのデータを Azure Synapse Analytics (専用 SQL プール) にプッシュします。

## <a name="deploy-the-infrastructure"></a>インフラストラクチャをデプロイする
この手順では、[Resource Manager テンプレート](https://github.com/Azure/azure-docs-json-samples/blob/master/event-grid/EventHubsDataMigration.json)を使用して必要なインフラストラクチャをデプロイします。 テンプレートをデプロイすると、次のリソースが作成されます。

* Capture 機能が有効なイベント ハブ
* キャプチャされたファイル用のストレージ アカウント 
* 関数アプリをホストするための App Service プラン
* イベントを処理するための関数アプリ
* データ ウェアハウスをホストするための SQL Server
* 移行したデータを格納するための Azure Synapse Analytics (専用 SQL プール)

### <a name="use-azure-cli-to-deploy-the-infrastructure"></a>Azure CLI を使用してインフラストラクチャをデプロイする

1. [Azure portal](https://portal.azure.com) にサインインします。 
2. 上部にある **[Cloud Shell]** ボタンを選択します。

    :::image type="content" source="media/event-grid-event-hubs-functions-synapse-analytics/azure-portal.png" alt-text="Azure Portal":::
3. ブラウザーの下部に Cloud Shell が開かれるのがわかります。

    :::image type="content" source="media/event-grid-event-hubs-functions-synapse-analytics/launch-cloud-shell.png" alt-text="Cloud Shell":::
4. Cloud Shell で、 **[Bash]** と **[PowerShell]** のどちらかを選択するオプションが表示されたら、 **[Bash]** を選択します。 
5. Cloud Shell を初めて使用する場合は、 **[ストレージの作成]** を選択してストレージ アカウントを作成します。 Azure Cloud Shell では、一部のファイルを格納するために Azure ストレージ アカウントが必要です。 

    :::image type="content" source="media/event-grid-event-hubs-functions-synapse-analytics/create-storage-cloud-shell.png" alt-text="Cloud Shell のストレージを作成する":::
6. Cloud Shell が初期化されるまで待ちます。 

    :::image type="content" source="media/event-grid-event-hubs-functions-synapse-analytics/cloud-shell-initialized.png" alt-text="Cloud Shell が初期化される":::
1. 次の CLI コマンドを実行して、Azure リソース グループを作成します。 
    1. 次のコマンドをコピーして Cloud Shell ウィンドウに貼り付けます。 必要に応じて、リソース グループの名前と場所を変更します。

        ```azurecli
        az group create -l eastus -n rgDataMigration
        ```
    2. **Enter** キーを押します。 

        たとえば次のようになります。
    
        ```azurecli
        user@Azure:~$ az group create -l eastus -n rgDataMigration
        {
          "id": "/subscriptions/00000000-0000-0000-0000-0000000000000/resourceGroups/rgDataMigration",
          "location": "eastus",
          "managedBy": null,
          "name": "rgDataMigration",
          "properties": {
            "provisioningState": "Succeeded"
          },
          "tags": null
        }
        ```
2. 次の CLI コマンドを実行して、前のセクションで説明したリソース (イベント ハブ、ストレージ アカウント、関数アプリ、Azure Synapse Analytics) をすべてデプロイします。 
    1. コマンドをコピーして Cloud Shell ウィンドウに貼り付けます。 または、お好みのエディターにコピーして貼り付け、値を設定してから、コマンドを Cloud Shell にコピーすることもできます。 

        > [!IMPORTANT]
        > コマンドを実行する前に、次のエンティティの値を指定します。 
        > - お客様が先ほど作成したリソース グループの名前。
        > - イベント ハブ名前空間の名前。 
        > - イベント ハブの名前。 値はそのまま (hubdatamigration) でもかまいません。
        > - SQL サーバーの名前。
        > - SQL ユーザーの名前とパスワード。 
        > - データベースの名前。
        > - ストレージ アカウントの名前。 
        > - 関数アプリの名前。 


        ```azurecli
        az deployment group create \
            --resource-group rgDataMigration \
            --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/event-grid/EventHubsDataMigration.json \
            --parameters eventHubNamespaceName=<event-hub-namespace> eventHubName=hubdatamigration sqlServerName=<sql-server-name> sqlServerUserName=<user-name> sqlServerPassword=<password> sqlServerDatabaseName=<database-name> storageName=<unique-storage-name> functionAppName=<app-name>
        ```
    3.  Cloud Shell ウィンドウで **Enter** キーを押して、コマンドを実行します。 多数のリソースを作成するため、このプロセスには時間がかかる場合があります。 コマンドの結果で、エラーがないことを確認します。 
1. ポータルの **[Cloud Shell]** ボタンまたは Cloud Shell ウィンドウの右上隅にある **[X]** ボタンを選択して Cloud Shell を閉じます。 

### <a name="verify-that-the-resources-are-created"></a>リソースが作成されたことを確認する

1. Azure portal で、左側のメニューにある **[リソース グループ]** を選択します。 
2. お客様のリソース グループの名前を検索ボックスに入力して、リソース グループの一覧をフィルター処理します。 
3. 一覧で、お客様のリソース グループを選択します。

    :::image type="content" source="media/event-grid-event-hubs-functions-synapse-analytics/select-resource-group.png" alt-text="リソース グループを選択します":::
4. 次のリソースがリソース グループに表示されていることを確認します。

    :::image type="content" source="media/event-grid-event-hubs-functions-synapse-analytics/resources-in-resource-group.png" alt-text="リソース グループ内のリソース" lightbox="media/event-grid-event-hubs-functions-synapse-analytics/resources-in-resource-group.png":::

### <a name="create-a-table-in-azure-synapse-analytics"></a>Azure Synapse Analytics でテーブルを作成する
[CreateDataWarehouseTable.sql](https://github.com/Azure/azure-event-hubs/blob/master/samples/e2e/EventHubsCaptureEventGridDemo/scripts/CreateDataWarehouseTable.sql) スクリプトを実行して、お客様のデータ ウェアハウスにテーブルを作成します。 スクリプトを実行するには、Visual Studio を使用できます。または、ポータルのクエリ エディターを使用できます。 次の手順では、クエリ エディターの使用方法について説明します。 

1. リソース グループ内のリソースの一覧で、**専用 SQL プール** を選択します。 
2. **[専用 SQL プール]** ページにある左側のメニューの **[主なタスク]** セクションで、 **[クエリ エディター (プレビュー)]** を選択します。 

    :::image type="content" source="media/event-grid-event-hubs-functions-synapse-analytics/sql-data-warehouse-page.png" alt-text="Azure Synapse Analytics ページ":::
2. SQL サーバーの **ユーザー** の名前と **パスワード** を入力し、 **[OK]** を選択します。 クライアントに SQL サーバーへのアクセスを許可することに関するメッセージが表示された場合は、次の手順に従います。
    1. 次のリンクを選択します: **[Set server firewall]\(サーバー ファイアウォールの設定\)** 。 
    2. **[ファイアウォール設定]** ページで、ツール バーの **[クライアント IP の追加]** を選択し、ツール バーの **[保存]** を選択します。 
    3. 正常に終了したことを知らせるメッセージで **[OK]** を選択します。
    4. **[専用 SQL プール]** ページに戻り、左側のメニューの **[クエリ エディター (プレビュー)]** を選択します。 
    5. **ユーザー** と **パスワード** を入力し、 **[OK]** を選択します。 
1. クエリ ウィンドウに、次の SQL スクリプトをコピーして実行します。 

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

    :::image type="content" source="media/event-grid-event-hubs-functions-synapse-analytics/run-sql-query.png" alt-text="SQL クエリを実行する":::
5. データが作成されたことをチュートリアルの最後に確認できるように、このタブまたはウィンドウを開いたままにしておきます。 

### <a name="update-the-function-runtime-version"></a>関数のランタイム バージョンを更新する

1. Web ブラウザーで別のタブを開き、[Azure portal](https://portal.azure.com) に移動します。
1. Azure portal で、左側のメニューにある **[リソース グループ]** を選択します。
1. 関数アプリが存在するリソース グループを選択します。 
1. リソース グループのリソースの一覧で **[関数アプリ]** を選択します。
1. 左側のメニューの **[設定]** で **[構成]** を選択します。 
1. 右ペインで **[関数のランタイム設定]** タブに切り替えます。 
1. **[ランタイム バージョン]** を **~3** に更新します。 

    :::image type="content" source="media/event-grid-event-hubs-functions-synapse-analytics/function-runtime-version.png" alt-text="関数のランタイム バージョンを更新する":::
6. ツールバーの **[保存]** を選択します。 
1. **[変更を保存]** 確認ポップアップで、 **[続行]** を選択します。 

## <a name="publish-the-azure-functions-app"></a>Azure Functions アプリを発行する

1. Visual Studio を起動します。
2. お客様が前提条件の一環として [GitHub](https://github.com/Azure/azure-event-hubs/tree/master/samples/e2e/EventHubsCaptureEventGridDemo) からダウンロードした **EventHubsCaptureEventGridDemo.sln** ソリューションを開きます。 これは `/samples/e2e/EventHubsCaptureEventGridDemo` フォルダーにあります。 
3. ソリューション エクスプローラーで、 **[FunctionEGDWDumper]** プロジェクトを右クリックし、 **[発行]** を選択します。
4. 次の画面が表示されたら、 **[開始]** を選択します。 

    :::image type="content" source="media/event-grid-event-hubs-functions-synapse-analytics/start-publish-button.png" alt-text="[発行] セクションの [開始] ボタン。":::
5. **[発行]** ダイアログ ボックスの **[対象]** で **[Azure]** を選択し、 **[次へ]** を選択します。 
6. **[Azure Function App (Windows)]** を選択し、 **[次へ]** を選択します。
7. **[Functions インスタンス]** タブで、自分の Azure サブスクリプションを選択し、リソース グループを展開して、使用する関数アプリを選択し、 **[完了]** を選択します。 Azure アカウントにサインインする必要があります (まだ実行していない場合)。 

    :::image type="content" source="media/event-grid-event-hubs-functions-synapse-analytics/publish-select-function-app.png" alt-text="関数アプリを選択する":::
8. **[発行]** ページの **[サービスの依存関係]** セクションで、 **[記憶域]** の **[構成]** を選択します。 

    :::image type="content" source="media/event-grid-event-hubs-functions-synapse-analytics/publish-storage-configure-link.png" alt-text="ストレージ サービスの依存関係の [構成] リンクを選択する":::
1. **[依存関係の構成]** ページで、次の手順に従います。 
    1. 以前に作成した **ストレージ アカウント** を選択し、 **[次へ]** を選択します。 

        :::image type="content" source="media/event-grid-event-hubs-functions-synapse-analytics/select-dependency-storage.png" alt-text="ストレージ アカウントを選択する":::
    10. **接続文字列の名前** を指定し、 **[Save connection string]\(接続文字列の保存\)** オプションで **[なし]** を選択してから、 **[次へ]** を選択します。 
    
        :::image type="content" source="media/event-grid-event-hubs-functions-synapse-analytics/dependency-storage-connection-string.png" alt-text="接続文字列名を指定する":::      
    1. **[C# コード ファイル]** オプションと **[シークレット ストア]** オプションをオフにし、 **[完了]** を選択します。  
    
        :::image type="content" source="media/event-grid-event-hubs-functions-synapse-analytics/dependency-storage-changes-summary.png" alt-text="変更の概要を確認する":::
1. Visual Studio でプロファイルを構成している場合は、 **[発行]** を選択します。

    :::image type="content" source="media/event-grid-event-hubs-functions-synapse-analytics/select-publish.png" alt-text="発行の選択":::
2. **Azure 関数** ページが開いているタブで、左側のメニューの **[Functions]\(関数\)** を選択します。 **EventGridTriggerMigrateData** 関数が一覧に表示されていることを確認します。 表示されない場合は、もう一度 Visual Studio から発行し、ポータルのページを最新の状態に更新してください。 

    :::image type="content" source="media/event-grid-event-hubs-functions-synapse-analytics/confirm-function-creation.png" alt-text="関数の作成を確認する":::    

関数を発行すれば、イベントをサブスクライブする準備が整います。

## <a name="subscribe-to-the-event"></a>イベントをサブスクライブする

1. Web ブラウザーの新しいタブまたはウィンドウで [Azure portal](https://portal.azure.com) に移動します。
2. Azure portal で、左側のメニューにある **[リソース グループ]** を選択します。 
3. お客様のリソース グループの名前を検索ボックスに入力して、リソース グループの一覧をフィルター処理します。 
4. 一覧で、お客様のリソース グループを選択します。
1. リソースの一覧から **[Event Hubs 名前空間]** を選択します。
1. **[Event Hubs 名前空間]** ページで、左側のメニューの **[イベント]** を選択し、ツール バーの **[+ イベント サブスクリプション]** を選択します。 

    :::image type="content" source="media/event-grid-event-hubs-functions-synapse-analytics/event-hub-add-subscription-link.png" alt-text="Event Hubs 名前空間の [イベント] ページにあるイベント サブスクリプションの追加リンク":::
1. **[イベント サブスクリプションの作成]** ページで、次の手順に従います。
    1. **イベント サブスクリプション** の名前を入力します。 
    1. システム トピックの **名前** を入力します。 システム トピックは、送信者がイベントを送信するためのエンドポイントを提供します。 詳細については、[システム トピック](../articles/event-grid/system-topics.md)に関するページを参照してください
    1. **[エンドポイントのタイプ]** で、 **[Azure 関数]** を選択します。
    1. **[エンドポイント]** で、リンクを選択します。
    1. **[Azure 関数の選択]** ページで、次のものが自動的に入力されない場合は、次の手順に従います。
        1. Azure 関数のある Azure サブスクリプションを選択します。 
        1. 関数のリソース グループを選択します。 
        1. 関数アプリを選択します。
        1. デプロイ スロットを選択します。 
        1. **EventGridTriggerMigrateData** 関数を選択します。 
    1. **[Azure 関数の選択]** ページで、 **[選択の確認]** を選択します。
    1. 次に、 **[イベント サブスクリプションの作成]** ページに戻り、 **[作成]** を選択します。 
    
        :::image type="content" source="media/event-grid-event-hubs-functions-synapse-analytics/event-subscription-select-function.png" alt-text="関数を使用してイベント サブスクリプションを作成する" lightbox="media/event-grid-event-hubs-functions-synapse-analytics/event-subscription-select-function.png":::
1. イベント サブスクリプションが作成されていることを確認します。 Event Hubs 名前空間の **[イベント]** ページで **[イベント サブスクリプション]** タブに切り替えます。 
    
    :::image type="content" source="media/event-grid-event-hubs-functions-synapse-analytics/confirm-event-subscription.png" alt-text="イベント サブスクリプションを確認する" lightbox="media/event-grid-event-hubs-functions-synapse-analytics/confirm-event-subscription.png":::
1. リソース グループのリソースの一覧で、(App Service ではなく) App Service プランを選択します。 

## <a name="run-the-app-to-generate-data"></a>データを生成するアプリを実行する
イベント ハブ、専用 SQL プール (以前の SQL Data Warehouse)、Azure 関数アプリ、およびイベント サブスクリプションの設定が完了しました。 イベント ハブのデータを生成するアプリケーションを実行する前に、いくつかの値を構成する必要があります。

1. Azure portal で、先ほど移動したお客様のリソース グループに移動します。 
2. Event Hubs 名前空間を選択します。
3. **[Event Hubs 名前空間]** ページで、左側のメニューの **[共有アクセス ポリシー]** を選択します。
4. ポリシーの一覧で **RootManageSharedAccessKey** を選択します。 

    :::image type="content" source="media/event-grid-event-hubs-functions-synapse-analytics/event-hub-namespace-shared-access-policies.png" alt-text="Event Hubs 名前空間の [共有アクセス ポリシー] ページ":::    
1. **[接続文字列 - 主キー]** テキスト ボックスの隣にあるコピー ボタンを選択します。 
1. お客様の Visual Studio ソリューションに戻ります。 
1. **[WindTurbineDataGenerator]** プロジェクトを右クリックし、 **[スタートアップ プロジェクトとして設定]** を選択します。 
1. WindTurbineDataGenerator プロジェクトで、**program.cs** を開きます。
1. `<EVENT HUBS NAMESPACE CONNECTION STRING>` をポータルからコピーした接続文字列に置き換えます。 
1. `<EVENT HUB NAME>` をイベント ハブの名前に置き換えます。 

   ```cs
   private const string EventHubConnectionString = "Endpoint=sb://demomigrationnamespace.servicebus.windows.net/...";
   private const string EventHubName = "hubdatamigration";
   ```
6. ソリューションをビルドします。 **WindTurbineGenerator.exe** アプリケーションを実行します。 
7. 数分後に、クエリ ウィンドウが開いている他のブラウザー タブで、データ ウェアハウスのテーブルに対してクエリを実行して、移行されたデータを確認します。

    ```sql
    select * from [dbo].[Fact_WindTurbineMetrics]    
    ```

    ![Query results](media/event-grid-event-hubs-functions-synapse-analytics/query-results.png)

## <a name="monitor-the-solution"></a>ソリューションを監視する
このセクションは、ソリューションの監視またはトラブルシューティングに役立ちます。 

### <a name="view-captured-data-in-the-storage-account"></a>ストレージ アカウントでキャプチャされたデータを表示する
1. リソース グループに移動し、イベント データのキャプチャに使用するストレージ アカウントを選択します。 
1. **[ストレージ アカウント]** ページで、左側のメニューの **[Storage Explorer (プレビュー)]** を選択します。
1. **[BLOB コンテナー]** を展開し、 **[windturbinecapture]** を選択します。 
1. 右側のペインで、対象の **Event Hubs 名前空間** と同じ名前のフォルダーを開きます。 
1. 対象のイベント ハブと同じ名前のフォルダー (**hubdatamigration**) を開きます。 
1. フォルダーをドリルスルーすると、AVRO ファイルが表示されます。 次に例を示します。

    :::image type="content" source="media/event-grid-event-hubs-functions-synapse-analytics/storage-captured-file.png" alt-text="ストレージ内のキャプチャされたファイル" lightbox="media/event-grid-event-hubs-functions-synapse-analytics/storage-captured-file.png":::
    

### <a name="verify-that-the-event-grid-trigger-invoked-the-function"></a>Event Grid トリガーによって関数が呼び出されたことを確認する
1. リソース グループに移動し、関数アプリを選択します。 
1. 左側のメニューの **[関数]** を選択します。
1. 一覧から **EventGridTriggerMigrateData** 関数を選択します。 
1. **[関数]** ページで、左側のメニューの **[監視]** を選択します。 
1. 呼び出しログをキャプチャするアプリケーション分析情報を構成するために、 **[構成]** を選択します。 
1. 新しい **Application Insights** リソースを作成するか、既存のリソースを選択します。 
1. 関数の **[監視]** ページに戻ります。 
1. イベントを送信しているクライアント アプリケーション (**WindTurbineDataGenerator**) がまだ実行されていることを確認します。 実行されていない場合は、アプリを実行します。 
1. 数分 (5 分以上) 待ってから、 **[最新の情報に更新]** ボタンを選択して、関数の呼び出しを確認します。    

    :::image type="content" source="media/event-grid-event-hubs-functions-synapse-analytics/function-invocations.png" alt-text="関数の呼び出し":::
1. 呼び出しを選択すると、詳細が表示されます。

    Event Grid により、イベント データがサブスクライバーに配信されます。 次の例は、イベント ハブを介したデータ ストリーミングが BLOB にキャプチャされるときに生成されるデータを示します。 特に、`data` オブジェクトの `fileUrl` プロパティがストレージ内の BLOB を指すことに注意してください。 関数アプリによってこの URL が使用され、キャプチャされたデータを含む BLOB ファイルが取得されます。

    ```json
    {
        "topic": "/subscriptions/<AZURE SUBSCRIPTION ID>/resourcegroups/rgDataMigration/providers/Microsoft.EventHub/namespaces/spehubns1207",
        "subject": "hubdatamigration",
        "eventType": "Microsoft.EventHub.CaptureFileCreated",
        "id": "4538f1a5-02d8-4b40-9f20-36301ac976ba",
        "data": {
            "fileUrl": "https://spehubstorage1207.blob.core.windows.net/windturbinecapture/spehubns1207/hubdatamigration/0/2020/12/07/21/49/12.avro",
            "fileType": "AzureBlockBlob",
            "partitionId": "0",
            "sizeInBytes": 473444,
            "eventCount": 2800,
            "firstSequenceNumber": 55500,
            "lastSequenceNumber": 58299,
            "firstEnqueueTime": "2020-12-07T21:49:12.556Z",
            "lastEnqueueTime": "2020-12-07T21:50:11.534Z"
        },
        "dataVersion": "1",
        "metadataVersion": "1",
        "eventTime": "2020-12-07T21:50:12.7065524Z"
    }
    ```

### <a name="verify-that-the-data-is-stored-in-the-dedicated-sql-pool"></a>データが専用 SQL プールに格納されていることを確認する
クエリ ウィンドウが開いているブラウザー タブで、専用 SQL プールのテーブルに対してクエリを実行して、移行されたデータを確認します。

![Query results](media/event-grid-event-hubs-functions-synapse-analytics/query-results.png)

