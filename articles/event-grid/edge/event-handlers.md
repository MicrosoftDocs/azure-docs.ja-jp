---
title: イベント ハンドラーと送信先 - Azure Event Grid IoT Edge |Microsoft Docs
description: Edge の Event Grid でのイベント ハンドラーと送信先
ms.date: 07/08/2020
ms.topic: article
ms.openlocfilehash: 18e2b944f60ebdf8a1b0c60ba446a13df13134c5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "86171586"
---
# <a name="event-handlers-and-destinations-in-event-grid-on-edge"></a>Edge の Event Grid でのイベント ハンドラーと送信先

イベント ハンドラーは、イベントのさらなるアクションを行ったり、イベントを処理したりする場所です。 Edge モジュールの Event Grid を使用すると、イベント ハンドラーを同じエッジ デバイス、別のデバイス、またはクラウドに配置できます。 任意の WebHook を使用してイベントを処理したり、Azure Event Grid のようないずれかのネイティブ ハンドラーにイベントを送信したりできます。

この記事では、それぞれの構成方法について説明します。

## <a name="webhook"></a>WebHook

WebHook エンドポイントに発行するには、`endpointType` を `WebHook` に設定し、以下を指定します。

* endpointUrl:WebHook エンドポイント URL

    ```json
        {
          "properties": {
            "destination": {
              "endpointType": "WebHook",
              "properties": {
                "endpointUrl": "<your-webhook-endpoint>"
              }
            }
          }
        }
    ```

## <a name="azure-event-grid"></a>Azure Event Grid

Azure Event Grid クラウド エンドポイントに発行するには、`endpointType` を `eventGrid` に設定し、以下を指定します。

* endpointUrl:クラウド内の Event Grid トピック URL
* sasKey:Event Grid トピックの SAS キー
* topicName:Event Grid へのすべての送信イベントにスタンプする名前。 トピック名は、Event Grid ドメイン トピックに投稿するときに役立ちます。

   ```json
        {
          "properties": {
            "destination": {
              "endpointType": "eventGrid",
              "properties": {
                 "endpointUrl": "<your-event-grid-cloud-topic-endpoint-url>?api-version=2018-01-01",
                 "sasKey": "<your-event-grid-topic-saskey>",
                 "topicName": null
              }
            }
          }
    }
   ```

## <a name="iot-edge-hub"></a>IoT Edge ハブ

Edge ハブ モジュールに発行するには、`endpointType` を `edgeHub` に設定し、以下を指定します。

* outputName:Event Grid モジュールがこのサブスクリプションに一致するイベントを edgeHub にルーティングする出力。 たとえば、次のサブスクリプションに一致するイベントは、/messages/modules/eventgridmodule/outputs/sampleSub4 に書き込まれます。

    ```json
        {
          "properties": {
            "destination": {
              "endpointType": "edgeHub",
              "properties": {
                "outputName": "sampleSub4"
              }
            }
          }
        }
    ```

## <a name="event-hubs"></a>Event Hubs

イベント ハブに発行するには、`endpointType` を `eventHub` に設定し、以下を指定します。

* connectionString:共有アクセス ポリシーを介して生成された、対象となる特定のイベント ハブの接続文字列。

    >[!NOTE]
    > 接続文字列はエンティティ固有である必要があります。 名前空間の接続文字列を使用すると機能しません。 エンティティ固有の接続文字列を生成するには、Azure portal で発行する特定のイベント ハブに移動し、 **[Shared access policies]/(共有アクセス ポリシー/)** をクリックして、新しいエンティティ固有の接続文字列を生成します。

    ```json
        {
          "properties": {
            "destination": {
              "endpointType": "eventHub",
              "properties": {
                "connectionString": "<your-event-hub-connection-string>"
              }
            }
          }
        }
    ```

## <a name="service-bus-queues"></a>Service Bus キュー

Service Bus キューに発行するには、`endpointType` を `serviceBusQueue` に設定し、以下を指定します。

* connectionString:共有アクセス ポリシーを介して生成された、対象となる特定の Service Bus キューの接続文字列。

    >[!NOTE]
    > 接続文字列はエンティティ固有である必要があります。 名前空間の接続文字列を使用すると機能しません。 エンティティ固有の接続文字列を生成するには、Azure portal で発行する特定の Service Bus キューに移動し、 **[Shared access policies]/(共有アクセス ポリシー/)** をクリックして、新しいエンティティ固有の接続文字列を生成します。

    ```json
        {
          "properties": {
            "destination": {
              "endpointType": "serviceBusQueue",
              "properties": {
                "connectionString": "<your-service-bus-queue-connection-string>"
              }
            }
          }
        }
    ```

## <a name="service-bus-topics"></a>Service Bus トピック

Service Bus トピックに発行するには、`endpointType` を `serviceBusTopic` に設定し、以下を指定します。

* connectionString:共有アクセス ポリシーを介して生成された、対象となる特定の Service Bus トピックの接続文字列。

    >[!NOTE]
    > 接続文字列はエンティティ固有である必要があります。 名前空間の接続文字列を使用すると機能しません。 エンティティ固有の接続文字列を生成するには、Azure portal で発行する特定の Service Bus トピックに移動し、 **[Shared access policies]/(共有アクセス ポリシー/)** をクリックして、新しいエンティティ固有の接続文字列を生成します。

    ```json
        {
          "properties": {
            "destination": {
              "endpointType": "serviceBusTopic",
              "properties": {
                "connectionString": "<your-service-bus-topic-connection-string>"
              }
            }
          }
        }
    ```

## <a name="storage-queues"></a>ストレージ キュー

ストレージ キューに発行するには、`endpointType` を `storageQueue` に設定し、以下を指定します。

* queueName:発行先のストレージ キューの名前。
* connectionString:ストレージ キューが存在するストレージ アカウントの接続文字列。

    >[!NOTE]
    > Event Hubs、Service Bus キュー、および Service Bus トピックとは異なり、ストレージ キューに使用される接続文字列はエンティティ固有ではありません。 その代わり、ストレージ アカウントの接続文字列である必要があります。

    ```json
        {
          "properties": {
            "destination": {
              "endpointType": "storageQueue",
              "properties": {
                "queueName": "<your-storage-queue-name>",
                "connectionString": "<your-storage-account-connection-string>"
              }
            }
          }
        }
    ```