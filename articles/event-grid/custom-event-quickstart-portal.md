---
title: クイック スタート:カスタム イベントを Web エンドポイントに送信する - Event Grid、Azure portal
description: クイック スタート:Azure Event Grid と Azure portal を使用して、カスタム トピックを発行したり、そのトピックに対するイベントをサブスクライブしたりします。 イベントは、Web アプリケーションによって処理されます。
ms.date: 07/07/2020
ms.topic: quickstart
ms.openlocfilehash: 592e2d6b7393da8cb55a457b022d6c2358048cfe
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "96013668"
---
# <a name="quickstart-route-custom-events-to-web-endpoint-with-the-azure-portal-and-event-grid"></a>クイック スタート:Azure portal と Event Grid を使ったカスタム イベントの Web エンドポイントへのルーティング

Azure Event Grid は、クラウドのイベント処理サービスです。 この記事では、Azure portal を使用し、カスタム トピックを作成してそのトピックをサブスクライブし、イベントをトリガーして結果を表示します。 通常は、イベント データを処理し、アクションを実行するエンドポイントにイベントを送信します。 ただし、この記事では、単純化するために、メッセージを収集して表示する Web アプリにイベントを送信します。

## <a name="prerequisites"></a>前提条件
[!INCLUDE [quickstarts-free-trial-note.md](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [event-grid-register-provider-portal.md](../../includes/event-grid-register-provider-portal.md)]

## <a name="create-a-custom-topic"></a>カスタム トピックの作成

Event Grid のトピックは、イベントの送信先となるユーザー定義のエンドポイントになります。 

1. [Azure ポータル](https://portal.azure.com/)にサインインします。
2. トピックの検索バーに「**Event Grid トピック**」と入力し、ドロップ ダウン リストから **[Event Grid トピック]** を選択します。 

    :::image type="content" source="./media/custom-event-quickstart-portal/select-event-grid-topics.png" alt-text="&quot;Event Grid トピック&quot; を検索して選択する":::
3. **[Event Grid トピック]** ページで、ツール バーの **[+ 追加]** を選択します。 

    :::image type="content" source="./media/custom-event-quickstart-portal/add-event-grid-topic-button.png" alt-text="Event Grid トピックの追加ボタン":::
4. **[トピックの作成]** ページで、次の手順に従います。
    1. Azure **サブスクリプション** を選択します。
    2. 既存のリソース グループを選択するか、 **[新規作成]** を選択し、**リソース グループ** の **名前** を入力します。
    3. カスタム トピックの一意の **名前** を指定します。 トピック名は、DNS エントリによって表されるため、一意である必要があります。 画像に示されている名前は使用しないでください。 代わりに、必ず 3 - 50 文字以内で、a - z、A - Z、0 - 9、および "-" のみを含む独自の名前を作成します。
    4. イベント グリッド トピックの **場所** を選択します。
    5. ページ下部にある **[確認と作成]** を選択します。 

        :::image type="content" source="./media/custom-event-quickstart-portal/create-custom-topic.png" alt-text="[トピックの作成] ページ":::
    6. **[トピックの作成]** ページの **[確認と作成]** タブで、 **[作成]** を選択します。 
    
        :::image type="content" source="./media/custom-event-quickstart-portal/review-create-page.png" alt-text="設定を確認して作成する":::
5. デプロイが成功したら、もう一度検索バーに「**Event Grid トピック**」と入力し、前と同じようにドロップダウン リストから **[Event Grid トピック]** を選択します。 
6. 一覧から作成したトピックを選択します。 

    :::image type="content" source="./media/custom-event-quickstart-portal/select-event-grid-topic.png" alt-text="一覧からトピックを選択する":::

7. 自分のトピックの **[Event Grid トピック]** ページが表示されます。 このページは開いたままにしておきます。 後ほど、このクイック スタートで使用します。 

    :::image type="content" source="./media/custom-event-quickstart-portal/event-grid-topic-home-page.png" alt-text="Event Grid トピックのホーム ページ":::

## <a name="create-a-message-endpoint"></a>メッセージ エンドポイントの作成
カスタム トピックのサブスクリプションを作成する前に、イベント メッセージのエンドポイントを作成します。 通常、エンドポイントは、イベント データに基づくアクションを実行します。 このクイック スタートを簡素化するために、イベント メッセージを表示する[構築済みの Web アプリ](https://github.com/Azure-Samples/azure-event-grid-viewer)をデプロしします。 デプロイされたソリューションには、App Service プラン、App Service Web アプリ、および GitHub からのソース コードが含まれています。

1. この記事ページで **[Deploy to Azure]\(Azure にデプロイ\)** を選択して、ソリューションを自分のサブスクリプションにデプロイします。 Azure portal で、パラメーターの値を指定します。

   <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2Fazure-event-grid-viewer%2Fmaster%2Fazuredeploy.json" target="_blank"><img src="https://azuredeploy.net/deploybutton.png"  alt="Button to Deploy to Aquent." /></a>
1. デプロイが完了するまでに数分かかる場合があります。 デプロイが成功した後で、Web アプリを表示して、実行されていることを確認します。 Web ブラウザーで `https://<your-site-name>.azurewebsites.net` にアクセスします

    デプロイが失敗した場合は、エラー メッセージを確認してください。 Web サイト名が既に取得されていることが原因である可能性があります。 テンプレートを再度デプロイし、サイトに別の名前を選択します。 
1. サイトは表示されますが、イベントはまだ送信されていません。

   ![新しいサイトを表示する](./media/custom-event-quickstart-portal/view-site.png)

## <a name="subscribe-to-custom-topic"></a>カスタム トピックのサブスクライブ

どのイベントを追跡し、どこにイベントを送信するかは、イベント グリッド トピックをサブスクライブすることによって Event Grid に伝えます。

1. 次に、カスタム トピックの **[Event Grid トピック]** ページで、ツール バーの **+ [イベント サブスクリプション]** を選択します。

    :::image type="content" source="./media/custom-event-quickstart-portal/new-event-subscription.png" alt-text="イベント サブスクリプションの追加ボタン":::
2. **[イベント サブスクリプションの作成]** ページで、次の手順に従います。
    1. イベント サブスクリプションの **名前** を入力します。
    3. **[エンドポイントのタイプ]** には **[Webhook]** を選択します。 
    4. **[エンドポイントの選択]** を選択します。 

        :::image type="content" source="./media/custom-event-quickstart-portal/provide-subscription-values.png" alt-text="イベント サブスクリプションの値を指定する":::
    5. Web hook エンドポイントに対して、Web アプリの URL を入力し、ホーム ページの URL に `api/updates` を追加します。 **[選択の確認]** を選択します。

        :::image type="content" source="./media/custom-event-quickstart-portal/provide-endpoint.png" alt-text="エンドポイントの URL を指定する":::
    6. 再び **[イベント サブスクリプションの作成]** ページで、 **[作成]** を選択します。

3. Web アプリをもう一度表示し、その Web アプリにサブスクリプションの検証イベントが送信されたことに注目します。 目のアイコンを選択してイベント データを展開します。 Event Grid は検証イベントを送信するので、エンドポイントはイベント データを受信することを確認できます。 Web アプリには、サブスクリプションを検証するコードが含まれています。

    ![サブスクリプション イベントの表示](./media/custom-event-quickstart-portal/view-subscription-event.png)

## <a name="send-an-event-to-your-topic"></a>トピックへのイベントの送信

では、イベントをトリガーして、Event Grid がメッセージをエンドポイントに配信するようすを見てみましょう。 Azure CLI または PowerShell を使用して、テスト イベントをカスタム トピックに送信します。 通常はイベント データをアプリケーションまたは Azure サービスから送信することになります。

最初の例では、Azure CLI を使用しています。 この例では、カスタム トピックの URL とキー、およびサンプル イベント データを取得します。 `<topic name>` には、実際のカスタム トピック名を使用してください。 これによりサンプルのイベント データが作成されます。 JSON の `data` 要素がイベントのペイロードです。 このフィールドには、適切な形式の JSON であればどのようなものでも格納することができます。 また、高度なルーティングやフィルタリングを行う場合には、subject フィールドを使用することもできます。 CURL は、HTTP 要求を送信するユーティリティです。


### <a name="azure-cli"></a>Azure CLI
1. Azure portal で、 **[Cloud Shell]** を選択します。 Cloud Shell が Web ブラウザーの下部ペインに表示されます。 

    :::image type="content" source="./media/custom-event-quickstart-portal/select-cloud-shell.png" alt-text="Cloud Shell アイコンを選択する":::
1. Cloud Shell ウィンドウの左上隅の **[Bash]** を選択します。 

    ![Cloud Shell - Bash](./media/custom-event-quickstart-portal/cloud-shell-bash.png)
1. 次のコマンドを実行して、トピックの **エンドポイント** を取得します。コマンドをコピーして貼り付けた後、コマンドを実行する前に **トピック名** と **リソース グループ名** を更新してください。 このトピック エンドポイントにサンプル イベントを発行します。 

    ```azurecli
    endpoint=$(az eventgrid topic show --name <topic name> -g <resource group name> --query "endpoint" --output tsv)
    ```
2. 次のコマンドを実行して、カスタム トピックの **キー** を取得します。コマンドをコピーして貼り付けた後、コマンドを実行する前に **トピック名** と **リソース グループ** 名を更新してください。 これは、Event Grid トピックの主キーです。 このキーを Azure portal から取得するには、 **[Event Grid トピック]** ページの **[アクセス キー]** タブに切り替えます。 カスタム トピックにイベントを投稿できるようにするには、アクセス キーが必要です。 

    ```azurecli
    key=$(az eventgrid topic key list --name <topic name> -g <resource group name> --query "key1" --output tsv)
    ```
3. イベント定義を含む次のステートメントをコピーし、**Enter** キーを押します。 

    ```json
    event='[ {"id": "'"$RANDOM"'", "eventType": "recordInserted", "subject": "myapp/vehicles/motorcycles", "eventTime": "'`date +%Y-%m-%dT%H:%M:%S%z`'", "data":{ "make": "Ducati", "model": "Monster"},"dataVersion": "1.0"} ]'
    ```
4. 次の **Curl** コマンドを実行して、イベントを送信します。このコマンドでは、`aeg-sas-key` ヘッダーは前に取得したアクセス キーに設定されています。 

    ```
    curl -X POST -H "aeg-sas-key: $key" -d "$event" $endpoint
    ```

### <a name="azure-powershell"></a>Azure PowerShell
2 番目の例では、PowerShell を使用して、同様の手順を実行します。

1. Azure portal で **[Cloud Shell]** を選択します (または `https://shell.azure.com/` にアクセスします)。 Cloud Shell が Web ブラウザーの下部ペインに表示されます。 

    :::image type="content" source="./media/custom-event-quickstart-portal/select-cloud-shell.png" alt-text="Cloud Shell アイコンを選択する":::
1. **Cloud Shell** で、Cloud Shell ウィンドウの左上隅の **[PowerShell]** を選択します。 「Azure CLI」セクションにあるサンプルの **Cloud Shell** ウィンドウの画像を参照してください。
2. 次の変数を設定します。 コマンドをコピーして貼り付けた後、**トピック名** と **リソース グループ名** を更新してから、次のコマンドを実行します。

    **[リソース グループ]** :
    ```powershell
    $resourceGroupName = "<resource group name>"
    ```

    **Event Grid トピック名**:    
    ```powershell
    $topicName = "<topic name>"
    ```
3. 次のコマンドを実行して、トピックの **エンドポイント** と **キー** を取得します。

    ```powershell
    $endpoint = (Get-AzEventGridTopic -ResourceGroupName $resourceGroupName -Name $topicName).Endpoint
    $keys = Get-AzEventGridTopicKey -ResourceGroupName $resourceGroupName -Name $topicName
    ```
4. イベントを準備します。 ステートメントをコピーし、Cloud Shell ウィンドウ内で実行します。 

    ```powershell
    $eventID = Get-Random 99999

    #Date format should be SortableDateTimePattern (ISO 8601)
    $eventDate = Get-Date -Format s

    #Construct body using Hashtable
    $htbody = @{
        id= $eventID
        eventType="recordInserted"
        subject="myapp/vehicles/motorcycles"
        eventTime= $eventDate   
        data= @{
            make="Ducati"
            model="Monster"
        }
        dataVersion="1.0"
    }
    
    #Use ConvertTo-Json to convert event body from Hashtable to JSON Object
    #Append square brackets to the converted JSON payload since they are expected in the event's JSON payload syntax
    $body = "["+(ConvertTo-Json $htbody)+"]"
    ```
5. **Invoke-WebRequest** コマンドレットを使用して、イベントを送信します。 

    ```powershell
    Invoke-WebRequest -Uri $endpoint -Method POST -Body $body -Headers @{"aeg-sas-key" = $keys.Key1}
    ```

### <a name="verify-in-the-event-grid-viewer"></a>Event Grid ビューアーでの確認
以上でイベントがトリガーされ、そのメッセージが、Event Grid によってサブスクライブ時に構成したエンドポイントに送信されました。 Web アプリを表示して、送信したイベント確認します。

:::image type="content" source="./media/custom-event-quickstart-portal/event-grid-viewer-end.png" alt-text="Event Grid ビューアー":::

## <a name="clean-up-resources"></a>リソースをクリーンアップする
引き続きこのイベントを使用する場合は、この記事で作成したリソースをクリーンアップしないでください。 それ以外の場合は、この記事で作成したリソースを削除してください。

1. 左側のメニューの **[リソース グループ]** を選択します。 左側のメニューにこれが表示されていない場合は、左側のメニューの **[すべてのサービス]** を選択し、 **[リソース グループ]** を選択します。 

    ![リソース グループ](./media/custom-event-quickstart-portal/delete-resource-groups.png)
1. リソース グループを選択して **[リソース グループ]** ページを開きます。 
1. ツール バーの **[リソース グループの削除]** を選択します。 
1. リソース グループの名前を入力して削除を確認し、 **[削除]** を選択します。 

    画像に表示されている他のリソース グループは、Cloud Shell ウィンドウによって作成および使用されたものです。 後で Cloud Shell ウィンドウを使用しない場合はこれを削除してください。 

## <a name="next-steps"></a>次のステップ

カスタム トピックを作成し、イベントをサブスクライブする方法がわかったら、Event Grid でできることについて、さらに情報を収集しましょう。

- [Event Grid について](overview.md)
- [Blob Storage のイベントをカスタム Web エンドポイントにルーティングする](../storage/blobs/storage-blob-event-quickstart.md?toc=%2fazure%2fevent-grid%2ftoc.json)
- [Azure Event Grid と Logic Apps で仮想マシンの変更を監視する](monitor-virtual-machine-changes-event-grid-logic-app.md)
- [ビッグ データをデータ ウェアハウスにストリーミングする](event-grid-event-hubs-integration.md)
