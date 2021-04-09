---
title: ローカルでイベントを発行してサブスクライブする - Azure Event Grid IoT Edge | Microsoft Docs
description: Event Grid on IoT Edge で Webhook を使用し、ローカルでイベントを発行してサブスクライブします
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: spelluru
ms.date: 07/08/2020
ms.topic: article
ms.openlocfilehash: 2a7cc864366bd9a35c96dd453c0dc68f77d8abd9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "86171450"
---
# <a name="tutorial-publish-subscribe-to-events-locally"></a>チュートリアル: ローカルでイベントを発行してサブスクライブする

この記事では、Event Grid on IoT Edge を使用してイベントを発行およびサブスクライブするために必要なすべての手順について説明します。

> [!NOTE]
> Azure Event Grid のトピックとサブスクリプションについては、「[Event Grid の概念](concepts.md)」を参照してください。

## <a name="prerequisites"></a>前提条件 
このチュートリアルを完了するには、以下が必要になります。

* **Azure サブスクリプション**: まだお持ちでない場合は、[無料アカウント](https://azure.microsoft.com/free)を作成してください。 
* **Azure IoT Hub および IoT Edge デバイス**: まだお持ちでない場合は、[Linux](../../iot-edge/quickstart-linux.md) または [Windows デバイス](../../iot-edge/quickstart.md)のクイックスタートの手順に従ってください。

## <a name="deploy-event-grid-iot-edge-module"></a>Event Grid IoT Edge モジュールをデプロイする

モジュールを IoT Edge デバイスにデプロイするにはいくつかの方法があり、そのうちどれでも Azure Event Grid on IoT Edge で使用できます。 この記事では、Azure portal で Event Grid on IoT Edge をデプロイする手順について説明します。

>[!NOTE]
> このチュートリアルでは、Event Grid モジュールを永続化せずにデプロイします。 つまり、このチュートリアルで作成するすべてのトピックとサブスクリプションは、モジュールを再デプロイすると削除されます。 永続化を設定する方法の詳細については、次の記事を参照してください。「[Linux で状態を永続化する](persist-state-linux.md)」または「[Windows で状態を永続化する](persist-state-windows.md)」。 運用環境のワークロードの場合は、永続化を使用して Event Grid モジュールをインストールすることをお勧めします。


### <a name="select-your-iot-edge-device"></a>IoT Edge デバイスを選択する

1. [Azure ポータル](https://portal.azure.com)
1. IoT Hub に移動します。
1. **[デバイスの自動管理]** セクションのメニューで **[IoT Edge]** を選択します。 
1. デバイスの一覧でターゲット デバイスの ID をクリックします
1. **[Set Modules] \(モジュールの設定)** を選択します。 ページは開いたままにしておきます。 引き続き次のセクションの手順で使用します。

### <a name="configure-a-deployment-manifest"></a>配置マニフェストを構成する

配置マニフェストは、デプロイするモジュール、モジュール間でのデータ フロー、およびモジュール ツインの目的のプロパティを記述した JSON ドキュメントです。 Azure portal には、JSON ドキュメントを手動で作成する代わりに配置マニフェストを作成する手順を示すウィザードがあります。  3 つのステップがあります。**モジュールの追加**、**ルートの指定**、および **デプロイの確認**。

### <a name="add-modules"></a>モジュールを追加する

1. **[デプロイ モジュール]** セクションで、 **[追加]** を選択します
1. モジュールの種類のドロップダウン リストから、 **[IoT Edge モジュール]** を選択します
1. コンテナーの名前、イメージ、およびコンテナー作成オプションを指定します。

   * **[名前]** : eventgridmodule
   * **[イメージの URI]** : `mcr.microsoft.com/azure-event-grid/iotedge:latest`
   * **[コンテナーの作成オプション]** :

   [!INCLUDE [event-grid-edge-module-version-update](../../../includes/event-grid-edge-module-version-update.md)]

    ```json
        {
          "Env": [
            "inbound__clientAuth__clientCert__enabled=false"
          ],
          "HostConfig": {
            "PortBindings": {
              "4438/tcp": [
                {
                    "HostPort": "4438"
                }
              ]
            }
          }
        }
    ```    
 1. **[保存]**
 1. 次のセクションに進み、Azure Event Grid Subscriber モジュールを追加してから一緒にデプロイします。

    >[!IMPORTANT]
    > このチュートリアルでは、クライアント認証が無効になっている Event Grid モジュールをデプロイしします。 運用環境のワークロードでは、クライアント認証を有効にすることをお勧めします。 Event Grid モジュールを安全に構成する方法の詳細については、「[セキュリティと認証](security-authentication.md)」を参照してください。
    > 
    > Azure VM をエッジ デバイスとして使用している場合は、ポート 4438 で受信トラフィックを許可する受信ポート規則を追加します。 規則を追加する手順については、[VM に対してポートを開く方法](../../virtual-machines/windows/nsg-quickstart-portal.md)に関するページを参照してください。
    

## <a name="deploy-event-grid-subscriber-iot-edge-module"></a>Event Grid Subscriber IoT Edge モジュールをデプロイする

このセクションでは、イベントの配信先となるイベント ハンドラーとして機能する別の IoT モジュールをデプロイする方法を示します。

### <a name="add-modules"></a>モジュールを追加する

1. **[デプロイ モジュール]** セクションで、もう一度 **[追加]** を選択します。 
1. モジュールの種類のドロップダウン リストから、 **[IoT Edge モジュール]** を選択します
1. コンテナーの名前、イメージ、およびコンテナー作成オプションを指定します。

   * **名前**: subscriber
   * **[イメージの URI]** : `mcr.microsoft.com/azure-event-grid/iotedge-samplesubscriber:latest`
   * **[コンテナーの作成オプション]** : なし
1. **[保存]**
1. **[次へ]** をクリックして、ルートのセクションに進みます

 ### <a name="setup-routes"></a>ルートを設定する

既定のルートを保持し、 **[次へ]** を選択して確認のセクションに進みます

### <a name="submit-the-deployment-request"></a>デプロイ要求を送信する

1. 確認のセクションには、前のセクションの選択項目に基づいて作成された JSON 配置マニフェストが表示されます。 **eventgridmodule** と **subscriber** の両方のモジュールが JSON に表示されていることを確認します。 
1. デプロイ情報を確認し、 **[送信]** を選択します。 デプロイを送信すると、**デバイス** のページに戻ります。
1. **[モジュール] セクション** に、**eventgrid** と **subscriber** の両方のモジュールが表示されていることを確認します。 また、**[デプロイで指定]** および **[デバイス別に報告]** の列が **[はい]** に設定されていることも確認します。

    モジュールがデバイス上で開始してから IoT Hub にレポートされるまでしばらく時間がかかる場合があります。 ページを更新して、最新の状態を表示します。

## <a name="create-a-topic"></a>トピックを作成する

イベントの発行元として、イベント グリッド トピックを作成する必要があります。 Azure Event Grid では、トピックは発行元がイベントの送信先にできるエンドポイントを指します。

1. 次の内容を含む topic.json を作成します。 ペイロードの詳細については、[API のドキュメント](api.md)を参照してください。

    ```json
        {
          "name": "sampleTopic1",
          "properties": {
            "inputschema": "eventGridSchema"
          }
        }
    ```

1. 次のコマンドを実行して、イベント グリッド トピックを作成します。 HTTP 状態コードが `200 OK` になることを確認します。

    ```sh
    curl -k -H "Content-Type: application/json" -X PUT -g -d @topic.json https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic1?api-version=2019-01-01-preview
    ```

1. 次のコマンドを実行して、トピックが正常に作成されたことを確認します。 HTTP 状態コード 200 OK が返される必要があります。

    ```sh
    curl -k -H "Content-Type: application/json" -X GET -g https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic1?api-version=2019-01-01-preview
    ```

   サンプル出力:

   ```json
        [
          {
            "id": "/iotHubs/eg-iot-edge-hub/devices/eg-edge-device/modules/eventgridmodule/topics/sampleTopic1",
            "name": "sampleTopic1",
            "type": "Microsoft.EventGrid/topics",
            "properties": {
              "endpoint": "https://<edge-vm-ip>:4438/topics/sampleTopic1/events?api-version=2019-01-01-preview",
              "inputSchema": "EventGridSchema"
            }
          }
        ]
   ```

## <a name="create-an-event-subscription"></a>イベント サブスクリプションの作成

サブスクライバーは、トピックに発行されたイベントの受信登録ができます。 イベントを受信するには、関心のあるトピックの Event Grid サブスクリプションを作成する必要があります。

[!INCLUDE [event-grid-deploy-iot-edge](../../../includes/event-grid-edge-persist-event-subscriptions.md)]

1. 次の内容を含む subscription.json を作成します。 ペイロードの詳細については、[API のドキュメント](api.md)を参照してください

    ```json
        {
          "properties": {
            "destination": {
              "endpointType": "WebHook",
              "properties": {
                "endpointUrl": "https://subscriber:4430"
              }
            }
          }
        }
    ```

    >[!NOTE]
    > **endpointType** プロパティによって、サブスクライバーが **Webhook** であることが指定されます。  **endpointUrl** によって、サブスクライバーがイベントをリッスンしている URL が指定されます。 この URL は、先ほどデプロイした Azure Subscriber のサンプルに対応しています。
2. 次のコマンドを実行して、トピックのサブスクリプションを作成します。 HTTP 状態コードが `200 OK` になることを確認します。

    ```sh
    curl -k -H "Content-Type: application/json" -X PUT -g -d @subscription.json https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic1/eventSubscriptions/sampleSubscription1?api-version=2019-01-01-preview
    ```
3. 次のコマンドを実行して、サブスクリプションが正常に作成されたことを確認します。 HTTP 状態コード 200 OK が返される必要があります。

    ```sh
    curl -k -H "Content-Type: application/json" -X GET -g https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic1/eventSubscriptions/sampleSubscription1?api-version=2019-01-01-preview
    ```

    サンプル出力:

   ```json
        {
          "id": "/iotHubs/eg-iot-edge-hub/devices/eg-edge-device/modules/eventgridmodule/topics/sampleTopic1/eventSubscriptions/sampleSubscription1",
          "type": "Microsoft.EventGrid/eventSubscriptions",
          "name": "sampleSubscription1",
          "properties": {
            "Topic": "sampleTopic1",
            "destination": {
              "endpointType": "WebHook",
              "properties": {
                "endpointUrl": "https://subscriber:4430"
              }
            }
          }
        }
    ```

## <a name="publish-an-event"></a>イベントを発行する

1. 次の内容を含む event.json を作成します。 ペイロードの詳細については、[API のドキュメント](api.md)を参照してください。

    ```json
        [
          {
            "id": "eventId-func-0",
            "eventType": "recordInserted",
            "subject": "myapp/vehicles/motorcycles",
            "eventTime": "2019-07-28T21:03:07+00:00",
            "dataVersion": "1.0",
            "data": {
              "make": "Ducati",
              "model": "Monster"
            }
          }
        ]
    ```
1. 次のコマンドを実行して、イベントを発行します。

    ```sh
    curl -k -H "Content-Type: application/json" -X POST -g -d @event.json https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic1/events?api-version=2019-01-01-preview
    ```

## <a name="verify-event-delivery"></a>イベントの配信を確認する

1. SSH または RDP で IoT Edge VM に接続します。
1. サブスクライバーのログを確認します。

    Windows の場合は、次のコマンドを実行します。

    ```sh
    docker -H npipe:////./pipe/iotedge_moby_engine container logs subscriber
    ```

   Linux の場合は、次のコマンドを実行します。

    ```sh
    sudo docker logs subscriber
    ```

    サンプル出力:

    ```sh
        Received Event:
            {
              "id": "eventId-func-0",
              "topic": "sampleTopic1",
              "subject": "myapp/vehicles/motorcycles",
              "eventType": "recordInserted",
              "eventTime": "2019-07-28T21:03:07+00:00",
              "dataVersion": "1.0",
              "metadataVersion": "1",
              "data": {
                "make": "Ducati",
                "model": "Monster"
              }
            }
    ```

## <a name="cleanup-resources"></a>リソースをクリーンアップする

* 次のコマンドを実行して、トピックとそのサブスクリプションすべてを削除します。

    ```sh
    curl -k -H "Content-Type: application/json" -X DELETE https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic1?api-version=2019-01-01-preview
    ```
* 自分の IoT Edge デバイスからサブスクライバー モジュールを削除します。


## <a name="next-steps"></a>次のステップ
このチュートリアルでは、イベント グリッド トピックとサブスクリプションを作成し、イベントを発行しました。 基本的な手順が理解できたら、次の記事を参照してください。 

- Azure Event Grid on IoT Edge の使用に伴う問題のトラブルシューティングについては、[トラブルシューティング ガイド](troubleshoot.md)を参照してください。
- [フィルター](advanced-filtering.md)を使用してサブスクリプションを作成または更新します。
- [Linux](persist-state-linux.md) または [Windows](persist-state-windows.md) で Event Grid モジュールの永続化を有効にする
- [ドキュメント](configure-client-auth.md)に従って、クライアント認証を構成します
- この[チュートリアル](pub-sub-events-webhook-cloud.md)に従って、クラウド内の Azure Functions にイベントを転送する
- [IoT Edge で Blob Storage イベントに対応する](react-blob-storage-events-locally.md)
- [Edge でのトピックとサブスクリプションの監視](monitor-topics-subscriptions.md)

