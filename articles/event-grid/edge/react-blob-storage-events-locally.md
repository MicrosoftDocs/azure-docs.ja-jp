---
title: Blob Storage モジュールのイベントに対応する - Azure Event Grid IoT Edge | Microsoft Docs
description: Blob Storage モジュールのイベントに対応する
author: arduppal
manager: brymat
ms.author: arduppal
ms.reviewer: spelluru
ms.date: 07/08/2020
ms.topic: article
ms.openlocfilehash: 230e158a970f8c815b1575403c013e30749124c5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "96005063"
---
# <a name="tutorial-react-to-blob-storage-events-on-iot-edge-preview"></a>チュートリアル:IoT Edge で Blob Storage イベントに対応する (プレビュー)
この記事では、AAzure Blob Storage を IoT モジュールにデプロイする方法について説明します。これは、BLOB の作成および削除時に Event Grid にイベントを送信する Event Grid パブリッシャーとして機能します。  

Azure Blob Storage on IoT Edge の概要については、[Azure Blob Storage on IoT Edge](../../iot-edge/how-to-store-data-blob.md) とその機能に関するページを参照してください。

> [!WARNING]
> Azure Blob Storage on IoT Edge と Event Grid の統合はプレビュー段階です

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

    ```json
        {
          "Env": [
           "inbound__serverAuth__tlsPolicy=enabled",
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
    > このチュートリアルでは、Event Grid モジュールをデプロイして、HTTP 要求と HTTPS 要求の両方を許可し、クライアント認証を無効にする方法を学習します。 運用環境のワークロードについては、HTTPS 要求のみを許可し、サブスクライバーではクライアント認証を有効にすることをお勧めします。 Event Grid モジュールを安全に構成する方法の詳細については、「[セキュリティと認証](security-authentication.md)」を参照してください。
    

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
1. 次のセクションに進み、Azure Blob Storage モジュールを追加します

## <a name="deploy-azure-blob-storage-module"></a>Azure Blob Storage モジュールをデプロイする

このセクションでは、Azure Blob Storage モジュールをデプロイする方法について説明します。これは、BLOB の作成および削除イベントを発行する Event Grid パブリッシャーとして機能します。

### <a name="add-modules"></a>モジュールを追加する

1. **[デプロイ モジュール]** セクションで、 **[追加]** を選択します
2. モジュールの種類のドロップダウン リストから、 **[IoT Edge モジュール]** を選択します
3. コンテナーの名前、イメージ、およびコンテナー作成オプションを指定します。

   * **名前**: azureblobstorageoniotedge
   * **イメージの URI**: mcr.microsoft.com/azure-blob-storage:latest
   * **[コンテナーの作成オプション]** :

   ```json
       {
         "Env":[
           "LOCAL_STORAGE_ACCOUNT_NAME=<your storage account name>",
           "LOCAL_STORAGE_ACCOUNT_KEY=<your storage account key>",
           "EVENTGRID_ENDPOINT=http://<event grid module name>:5888"
         ],
         "HostConfig":{
           "Binds":[
               "<storage mount>"
           ],
           "PortBindings":{
             "11002/tcp":[{"HostPort":"11002"}]
           }
         }
       }
   ```

   > [!IMPORTANT]
   > - Blob Storage モジュールでは、HTTPS と HTTP の両方を使用してイベントを発行できます。 
   > - EventGrid 用のクライアント ベースの認証を有効にしている場合は、必ず https を許可するように EVENTGRID_ENDPOINT の値を更新します (例: `EVENTGRID_ENDPOINT=https://<event grid module name>:4438`)。
   > - さらに、上記の JSON にもう 1 つの環境変数 `AllowUnknownCertificateAuthority=true` を追加します。 HTTPS 経由で EventGrid を使用する場合、**AllowUnknownCertificateAuthority** によって、ストレージ モジュールで自己署名済みの EventGrid サーバー証明書を信頼することができます。

4. コピーした JSON を次の情報で更新します。

   - `<your storage account name>` を覚えやすい名前に置き換えます。 アカウント名は、小文字と数字の 3 文字から 24 文字の長さにする必要があります。 スペースは含めません。

   - `<your storage account key>` を 64 バイトの Base64 キーに置き換えます。 [GeneratePlus](https://generate.plus/en/base64?gp_base64_base[length]=64) などのツールを使ってキーを生成できます。 他のモジュールから BLOB ストレージにアクセスするには、これらの資格情報を使用します。

   - `<event grid module name>` は実際の Event Grid モジュールの名前で置き換えます。
   - コンテナーのオペレーティング システムに応じて `<storage mount>` を置き換えます。
     - Linux コンテナーの場合は、**my volume:/blobroot**
     - Windows コンテナーの場合は、**my-volume:C:/BlobRoot**

5. **[保存]**
6. **[次へ]** をクリックして、ルートのセクションに進みます

    > [!NOTE]
    > Azure VM をエッジ デバイスとして使用している場合は、このチュートリアルで使用している次のホスト ポートで受信トラフィックを許可する受信ポート規則を追加します: 4438、5888、8080、および 11002。 規則を追加する手順については、[VM に対してポートを開く方法](../../virtual-machines/windows/nsg-quickstart-portal.md)に関するページを参照してください。

### <a name="setup-routes"></a>ルートを設定する

既定のルートを保持し、 **[次へ]** を選択して確認のセクションに進みます

### <a name="review-deployment"></a>デプロイを確認する

1. 確認のセクションには、前のセクションの選択項目に基づいて作成された JSON 配置マニフェストが表示されます。 5 つのモジュール ( **$edgeAgent**、 **$edgeHub**、**eventgridmodule**、**subscriber**、**azureblobstorageoniotedge**) が表示され、すべてがデプロイされていることを確認します。
2. デプロイ情報を確認し、 **[送信]** を選択します。

## <a name="verify-your-deployment"></a>デプロイを確認する

1. デプロイを送信すると、自分の IoT ハブの IoT Edge ページに戻ります。
2. デプロイで対象にした **IoT Edge デバイス** を選択して、その詳細を開きます。
3. デバイスの詳細で、eventgridmodule、subscriber、azureblobstorageoniotedge の各モジュールが、 **[デプロイで指定]** と **[デバイス別に報告]** の両方に表示されていることを確認します。

   モジュールがデバイス上で開始してから IoT Hub にレポートされるまでしばらく時間がかかる場合があります。 ページを更新して、最新の状態を表示します。

## <a name="publish-blobcreated-and-blobdeleted-events"></a>BlobCreated および BlobDeleted イベントを発行する

1. このモジュールによって、**MicrosoftStorage** トピックが自動的に作成されます。 これが存在することを確認します
    ```sh
    curl -k -H "Content-Type: application/json" -X GET -g https://<your-edge-device-public-ip-here>:4438/topics/MicrosoftStorage?api-version=2019-01-01-preview
    ```

    サンプル出力:

    ```json
        [
          {
            "id": "/iotHubs/eg-iot-edge-hub/devices/eg-edge-device/modules/eventgridmodule/topics/MicrosoftStorage",
            "name": "MicrosoftStorage",
            "type": "Microsoft.EventGrid/topics",
            "properties": {
              "endpoint": "https://<edge-vm-ip>:4438/topics/MicrosoftStorage/events?api-version=2019-01-01-preview",
              "inputSchema": "EventGridSchema"
            }
          }
        ]
    ```

    > [!IMPORTANT]
    > - HTTPS のフローでは、SAS キーによってクライアント認証が有効になっている場合、前に指定した SAS キーをヘッダーとして追加する必要があります。 そのため、curl 要求は次のようになります: `curl -k -H "Content-Type: application/json" -H "aeg-sas-key: <your SAS key>" -X GET -g https://<your-edge-device-public-ip-here>:4438/topics/MicrosoftStorage?api-version=2019-01-01-preview`
    > - HTTPS のフローでは、証明書によってクライアント認証が有効になっている場合、curl 要求は次のようになります: `curl -k -H "Content-Type: application/json" --cert <certificate file> --key <certificate private key file> -X GET -g https://<your-edge-device-public-ip-here>:4438/topics/MicrosoftStorage?api-version=2019-01-01-preview`

2. サブスクライバーは、トピックに発行されたイベントの受信登録ができます。 イベントを受信するには、**MicrosoftStorage** トピックの Event Grid サブスクリプションを作成する必要があります。
    1. 次の内容を含む blobsubscription.json を作成します。 ペイロードの詳細については、[API のドキュメント](api.md)を参照してください

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
       > **endpointType** プロパティによって、サブスクライバーが **Webhook** であることが指定されます。  **endpointUrl** によって、サブスクライバーがイベントをリッスンしている URL が指定されます。 この URL は、先ほどデプロイした Azure Functions のサンプルに対応しています。

    2. 次のコマンドを実行して、トピックのサブスクリプションを作成します。 HTTP 状態コードが `200 OK` になることを確認します。

       ```sh
       curl -k -H "Content-Type: application/json" -X PUT -g -d @blobsubscription.json https://<your-edge-device-public-ip-here>:4438/topics/MicrosoftStorage/eventSubscriptions/sampleSubscription5?api-version=2019-01-01-preview
       ```

       > [!IMPORTANT]
       > - HTTPS のフローでは、SAS キーによってクライアント認証が有効になっている場合、前に指定した SAS キーをヘッダーとして追加する必要があります。 そのため、curl 要求は次のようになります: `curl -k -H "Content-Type: application/json" -H "aeg-sas-key: <your SAS key>" -X PUT -g -d @blobsubscription.json https://<your-edge-device-public-ip-here>:4438/topics/MicrosoftStorage/eventSubscriptions/sampleSubscription5?api-version=2019-01-01-preview` 
       > - HTTPS のフローでは、証明書によってクライアント認証が有効になっている場合、curl 要求は次のようになります: `curl -k -H "Content-Type: application/json" --cert <certificate file> --key <certificate private key file> -X PUT -g -d @blobsubscription.json https://<your-edge-device-public-ip-here>:4438/topics/MicrosoftStorage/eventSubscriptions/sampleSubscription5?api-version=2019-01-01-preview`

    3. 次のコマンドを実行して、サブスクリプションが正常に作成されたことを確認します。 HTTP 状態コード 200 OK が返される必要があります。

       ```sh
       curl -k -H "Content-Type: application/json" -X GET -g https://<your-edge-device-public-ip-here>:4438/topics/MicrosoftStorage/eventSubscriptions/sampleSubscription5?api-version=2019-01-01-preview
       ```

       サンプル出力:

       ```json
        {
          "id": "/iotHubs/eg-iot-edge-hub/devices/eg-edge-device/modules/eventgridmodule/topics/MicrosoftStorage/eventSubscriptions/sampleSubscription5",
          "type": "Microsoft.EventGrid/eventSubscriptions",
          "name": "sampleSubscription5",
          "properties": {
            "Topic": "MicrosoftStorage",
            "destination": {
              "endpointType": "WebHook",
              "properties": {
                "endpointUrl": "https://subscriber:4430"
              }
            }
          }
        }
       ```

       > [!IMPORTANT]
       > - HTTPS のフローでは、SAS キーによってクライアント認証が有効になっている場合、前に指定した SAS キーをヘッダーとして追加する必要があります。 そのため、curl 要求は次のようになります: `curl -k -H "Content-Type: application/json" -H "aeg-sas-key: <your SAS key>" -X GET -g https://<your-edge-device-public-ip-here>:4438/topics/MicrosoftStorage/eventSubscriptions/sampleSubscription5?api-version=2019-01-01-preview`
       > - HTTPS のフローでは、証明書によってクライアント認証が有効になっている場合、curl 要求は次のようになります: `curl -k -H "Content-Type: application/json" --cert <certificate file> --key <certificate private key file> -X GET -g https://<your-edge-device-public-ip-here>:4438/topics/MicrosoftStorage/eventSubscriptions/sampleSubscription5?api-version=2019-01-01-preview`

3. [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/) をダウンロードし、[お使いのローカル ストレージにそれを接続します](../../iot-edge/how-to-store-data-blob.md#connect-to-your-local-storage-with-azure-storage-explorer)

## <a name="verify-event-delivery"></a>イベントの配信を確認する

### <a name="verify-blobcreated-event-delivery"></a>BlobCreated イベントの配信を確認する

1. ファイルをブロック BLOB として Azure Storage Explorer からローカル ストレージにアップロードすると、モジュールによって自動的に作成イベントが発行されます。 
2. サブスクライバー ログで作成イベントを確認します。 手順に従って、[イベント配信を確認します](pub-sub-events-webhook-local.md#verify-event-delivery)

    サンプル出力:

    ```json
            Received Event:
            {
              "id": "d278f2aa-2558-41aa-816b-e6d8cc8fa140",
              "topic": "MicrosoftStorage",
              "subject": "/blobServices/default/containers/cont1/blobs/Team.jpg",
              "eventType": "Microsoft.Storage.BlobCreated",
              "eventTime": "2019-10-01T21:35:17.7219554Z",
              "dataVersion": "1.0",
              "metadataVersion": "1",
              "data": {
                "api": "PutBlob",
                "clientRequestId": "00000000-0000-0000-0000-000000000000",
                "requestId": "ef1c387b-4c3c-4ac0-8e04-ff73c859bfdc",
                "eTag": "0x8D746B740DA21FB",
                "url": "http://azureblobstorageoniotedge:11002/myaccount/cont1/Team.jpg",
                "contentType": "image/jpeg",
                "contentLength": 858129,
                "blobType": "BlockBlob"
              }
            }
    ```

### <a name="verify-blobdeleted-event-delivery"></a>BlobDeleted イベントの配信を確認する

1. Azure Storage Explorer を使用してローカル ストレージから BLOB を削除すると、モジュールによって自動的に削除イベントが発行されます。 
2. サブスクライバー ログで削除イベントを確認します。 手順に従って、[イベント配信を確認します](pub-sub-events-webhook-local.md#verify-event-delivery)

    サンプル出力:
    
    ```json
            Received Event:
            {
              "id": "ac669b6f-8b0a-41f3-a6be-812a3ce6ac6d",
              "topic": "MicrosoftStorage",
              "subject": "/blobServices/default/containers/cont1/blobs/Team.jpg",
              "eventType": "Microsoft.Storage.BlobDeleted",
              "eventTime": "2019-10-01T21:36:09.2562941Z",
              "dataVersion": "1.0",
              "metadataVersion": "1",
              "data": {
                "api": "DeleteBlob",
                "clientRequestId": "00000000-0000-0000-0000-000000000000",
                "requestId": "2996bbfb-c819-4d02-92b1-c468cc67d8c6",
                "eTag": "0x8D746B740DA21FB",
                "url": "http://azureblobstorageoniotedge:11002/myaccount/cont1/Team.jpg",
                "contentType": "image/jpeg",
                "contentLength": 858129,
                "blobType": "BlockBlob"
              }
            }
    ```

お疲れさまでした。 これでチュートリアルは完了です。 次のセクションでは、イベントのプロパティについて詳しく説明します。

### <a name="event-properties"></a>イベントのプロパティ

サポートされているイベント プロパティと、その種類および説明の一覧を次に示します。 

| プロパティ | 種類 | 説明 |
| -------- | ---- | ----------- |
| topic | string | イベント ソースの完全なリソース パス。 このフィールドは書き込み可能ではありません。 この値は Event Grid によって指定されます。 |
| subject | string | 発行元が定義したイベントの対象のパス。 |
| eventType | string | このイベント ソース用に登録されたイベントの種類のいずれか。 |
| eventTime | string | プロバイダーの UTC 時刻に基づくイベントの生成時刻。 |
| id | string | イベントの一意識別子。 |
| data | object | Blob Storage イベントのデータ。 |
| dataVersion | string | データ オブジェクトのスキーマ バージョン。 スキーマ バージョンは発行元によって定義されます。 |
| metadataVersion | string | イベント メタデータのスキーマ バージョン。 最上位プロパティのスキーマは Event Grid によって定義されます。 この値は Event Grid によって指定されます。 |

データ オブジェクトには、次のプロパティがあります。

| プロパティ | 種類 | 説明 |
| -------- | ---- | ----------- |
| api | string | イベントのトリガーとなった操作。 次のいずれかの値を指定できます。 <ul><li>BlobCreated: 使用できる値は、`PutBlob` と `PutBlockList` です</li><li>BlobDeleted: 使用できる値は、`DeleteBlob`、`DeleteAfterUpload`、および `AutoDelete` です。 <p>DeleteAfterUpload の必要なプロパティが true に設定されているために BLOB が自動的に削除されると、`DeleteAfterUpload` イベントが生成されます。 </p><p>deleteAfterMinutes の必要なプロパティ値の期限が過ぎたために BLOB が自動的に削除されると、`AutoDelete` イベントが生成されます。</p></li></ul>|
| clientRequestId | string | ストレージ API 操作に対するクライアントで提供された要求 ID です。 この ID は、ログの "client-request-id" フィールドを使って Azure Storage 診断ログに関連付けるために使うことができ、クライアント要求で "x-ms-client-request-id" ヘッダーを使って提供できます。 詳細については、[ログの形式](/rest/api/storageservices/storage-analytics-log-format)に関するページを参照してください。 |
| requestId | string | ストレージ API 操作に対するサービスで生成された要求 ID です。 ログの "request-id-header" フィールドを使って Azure Storage 診断ログに関連付けるために使うことができ、開始 API 呼び出しから "x-ms-request-id" ヘッダーで返されます。 「[Storage Analytics のログの形式](/rest/api/storageservices/storage-analytics-log-format)」をご覧ください。 |
| eTag | string | この値を使用することで、条件に応じて操作を実行することができます。 |
| contentType | string | BLOB に関して指定されたコンテンツの種類。 |
| contentLength | 整数 (integer) | BLOB のサイズ (単位: バイト)。 |
| blobType | string | BLOB の種類。 有効な値は "BlockBlob" または "PageBlob" です。 |
| url | string | BLOB へのパス。 <br>クライアントが BLOB REST API を使用する場合、url の構造は *\<storage-account-name\>.blob.core.windows.net/\<container-name\>/\<file-name\>* です。 <br>クライアントが Data Lake Storage REST API を使用する場合、url の構造は *\<storage-account-name\>.dfs.core.windows.net/\<file-system-name\>/\<file-name\>* です。 |


## <a name="next-steps"></a>次のステップ

Blob Storage のドキュメントのうち、次の記事をご覧ください。

- [Blob Storage イベントのフィルター処理](../../storage/blobs/storage-blob-event-overview.md#filtering-events)
- [Blob Storage イベントを使用する際の推奨プラクティス](../../storage/blobs/storage-blob-event-overview.md#practices-for-consuming-events)

このチュートリアルでは、Azure Blob Storage で BLOB を作成または削除することでイベントを発行しました。 クラウド (Azure Event Hubs または Azure IoT Hub) にイベントを転送する方法については、他のチュートリアルを参照してください。 

- [イベントを Azure Event Grid に転送する](forward-events-event-grid-cloud.md)
- [イベントを Azure IoT Hub に転送する](forward-events-iothub.md)
