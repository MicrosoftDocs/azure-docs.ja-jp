---
title: "Azure IoT Hub 操作の監視 | Microsoft Docs"
description: "Azure IoT Hub 操作の監視を使用して、IoT Hub に対する操作の状態をリアルタイムで監視する方法。"
services: iot-hub
documentationcenter: 
author: nberdy
manager: timlt
editor: 
ms.assetid: a299f3a5-b14d-4586-9c3b-44aea14ed013
ms.service: iot-hub
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/13/2016
ms.author: nberdy
translationtype: Human Translation
ms.sourcegitcommit: 094729399070a64abc1aa05a9f585a0782142cbf
ms.openlocfilehash: 796bf9b1219b7f0e2c68688c5f5b51163ef4b49b
ms.lasthandoff: 03/07/2017


---
# <a name="iot-hub-operations-monitoring"></a>IoT Hub 操作の監視
IoT Hub の操作の監視では、IoT Hub に対する操作の状態をリアルタイムで監視することができます。 IoT Hub は、複数のカテゴリにまたがる操作のイベントを追跡します。 1 つ以上のカテゴリから IoT ハブのエンドポイントにイベントを送信して処理するように選択することができます。 データを監視してエラーがないか確認したり、データ パターンに基づいてより複雑な処理をセットアップしたりできます。

IoT Hub では、次の&6; つのカテゴリのイベントを監視します。

* デバイス ID の操作
* デバイス テレメトリ
* クラウドからデバイスへのメッセージ
* 接続
* ファイルのアップロード
* メッセージ ルーティング

## <a name="how-to-enable-operations-monitoring"></a>操作の監視を有効にする方法
1. IoT Hub を作成します。 IoT ハブの作成方法の手順については、[使用開始][lnk-get-started]に関するガイドを参照してください。
2. IoT Hub のブレードを開きます。 このブレードで、 **[操作の監視]**をクリックします。
   
    ![][1]
3. 監視する監視カテゴリを選択し、 **[保存]**をクリックします。 イベントは、 **[監視の設定]**に一覧表示された Event Hub 対応のエンドポイントから読み取ることができます。 IoT Hub エンドポイントの名前は `messages/operationsmonitoringevents`です。
   
    ![][2]

> [!NOTE]
> **[接続]** カテゴリに対して **[詳細]** 監視を選ぶと、IoT Hub は追加の診断メッセージを生成します。 他のすべてのカテゴリでは、**[詳細]** 設定を選ぶと、IoT Hub が個々のエラー メッセージに含める情報の量が変わります。

## <a name="event-categories-and-how-to-use-them"></a>イベント カテゴリとその使用方法
操作監視の各カテゴリでは、IoT Hub との各種のやり取りを追跡します。各監視カテゴリは、カテゴリ内のイベントの構成方法を定義するスキーマを備えています。

### <a name="device-identity-operations"></a>デバイス ID の操作
デバイス ID の操作のカテゴリでは、IoT Hub の ID レジストリ内でエントリの作成、更新、または削除を試みたときに発生するエラーを追跡します。 このカテゴリの追跡は、プロビジョニングのシナリオで便利です。

    {
        "time": "UTC timestamp",
         "operationName": "create",
         "category": "DeviceIdentityOperations",
         "level": "Error",
         "statusCode": 4XX,
         "statusDescription": "MessageDescription",
         "deviceId": "device-ID",
         "durationMs": 1234,
         "userAgent": "userAgent",
         "sharedAccessPolicy": "accessPolicy"
    }

### <a name="device-telemetry"></a>デバイス テレメトリ
デバイス テレメトリのカテゴリでは、IoT Hub で発生し、かつテレメトリ パイプラインに関連しているエラーを追跡します。 このカテゴリーには、テレメトリ イベントの送信時に発生したエラー (スロットルなど) やテレメトリ イベントの受信時に発生したエラー (許可されていないリーダーなど) が含まれます。 このカテゴリでは、デバイス自体で実行されているコードに起因するエラーをキャッチできないので注意してください。

    {
         "messageSizeInBytes": 1234,
         "batching": 0,
         "protocol": "Amqp",
         "authType": "{\"scope\":\"device\",\"type\":\"sas\",\"issuer\":\"iothub\"}",
         "time": "UTC timestamp",
         "operationName": "ingress",
         "category": "DeviceTelemetry",
         "level": "Error",
         "statusCode": 4XX,
         "statusType": 4XX001,
         "statusDescription": "MessageDescription",
         "deviceId": "device-ID",
         "EventProcessedUtcTime": "UTC timestamp",
         "PartitionId": 1,
         "EventEnqueuedUtcTime": "UTC timestamp"
    }

### <a name="cloud-to-device-commands"></a>クラウドからデバイスへのコマンド
C2D コマンド カテゴリでは、IoT Hub で発生し、かつクラウドからデバイスへのメッセージ パイプラインに関連しているエラーを追跡します。 このカテゴリには、クラウドからデバイスへのメッセージの送信時のエラー (許可されていない送信者など)、クラウドからデバイスへのメッセージの受信時のエラー (配信数が上限を超えているなど)、クラウドからデバイスへのメッセージ フィードバックの受信時のエラー (フィードバックの有効期限切れなど) が含まれます。 このカテゴリでは、クラウドからデバイスへのメッセージが正常に配信されてもクラウドからデバイスへのメッセージを適切に処理しないデバイスのエラーはキャッチしません。

    {
         "messageSizeInBytes": 1234,
         "authType": "{\"scope\":\"hub\",\"type\":\"sas\",\"issuer\":\"iothub\"}",
         "deliveryAcknowledgement": 0,
         "protocol": "Amqp",
         "time": " UTC timestamp",
         "operationName": "ingress",
         "category": "C2DCommands",
         "level": "Error",
         "statusCode": 4XX,
         "statusType": 4XX001,
         "statusDescription": "MessageDescription",
         "deviceId": "device-ID",
         "EventProcessedUtcTime": "UTC timestamp",
         "PartitionId": 1,
         "EventEnqueuedUtcTime": “UTC timestamp"
    }

### <a name="connections"></a>接続
接続のカテゴリでは、デバイスが IoT Hub に接続したときに発生する、または IoT Hub から切断したときのエラーを追跡します。 このカテゴリの追跡は、許可されていない接続の試行を識別する場合、および接続状態が悪い領域内で接続が失われたタイミングを突き止める場合に便利です

    {
         "durationMs": 1234,
         "authType": "{\"scope\":\"hub\",\"type\":\"sas\",\"issuer\":\"iothub\"}",
         "protocol": "Amqp",
         "time": " UTC timestamp",
         "operationName": "deviceConnect",
         "category": "Connections",
         "level": "Error",
         "statusCode": 4XX,
         "statusType": 4XX001,
         "statusDescription": "MessageDescription",
         "deviceId": "device-ID"
    }

### <a name="file-uploads"></a>ファイルのアップロード

ファイルのアップロード カテゴリでは、IoT Hub で発生し、かつファイルのアップロード機能に関連しているエラーを追跡します。 このカテゴリには、次のエラーが含まれます。

- SAS URI で発生したエラー (デバイスがアップロード完了をハブに通知する前に期限切れになった、など)。
- デバイスによって報告されたアップロード エラー。
- IoT Hub 通知メッセージの作成中にストレージでファイルが見つからないときに発生するエラー。

デバイスがファイルをストレージにアップロードしているときに直接発生したエラーについては、このカテゴリではキャッチできないことに注意してください。


    {
         "authType": "{\"scope\":\"hub\",\"type\":\"sas\",\"issuer\":\"iothub\"}",
         "protocol": "HTTP",
         "time": " UTC timestamp",
         "operationName": "ingress",
         "category": "fileUpload",
         "level": "Error",
         "statusCode": 4XX,
         "statusType": 4XX001,
         "statusDescription": "MessageDescription",
         "deviceId": "device-ID",
         "blobUri": "http//bloburi.com",
         "durationMs": 1234
    }

### <a name="message-routing"></a>メッセージ ルーティング
メッセージ ルーティング カテゴリは、メッセージ ルート評価および IoT Hub によって認識されるエンドポイント正常性において発生するエラーを追跡します。 このカテゴリには、ルールが "未定義" と評価されたとき、IoT Hub がエンドポイントをデッドとしてマークしたとき、およびエンドポイントから受信したその他のすべてのエラーなどのイベントが含まれます。 このカテゴリにはメッセージ自体に関する具体的なエラーは含まれないことに注意してください (デバイス調整エラーなど)。このようなエラーは、"デバイス テレメトリ" カテゴリで報告されます。
        
    {
        "messageSizeInBytes": 1234,
        "time": "UTC timestamp",
        "operationName": "ingress",
        "category": "routes",
        "level": "Error",
        "deviceId": "device-ID",
        "messageId": "ID of message",
        "routeName": "myroute",
        "endpointName": "myendpoint",
        "details": "ExternalEndpointDisabled"
    }

## <a name="view-events"></a>イベントの表示

*iothub-explorer* ツールを使用すると、IoT Hub が監視イベントを生成していることを簡単にテストできます。 このツールをインストールする方法については、[iothub-explorer][lnk-iothub-explorer] GitHub リポジトリにある手順をご覧ください。

1. **[接続]** 監視カテゴリがポータルで **[詳細]** に設定されていることを確認します。

1. コマンド プロンプトで次のコマンドを実行して、監視エンドポイントから読み取ります。

    ```
    iothub-explorer monitor-ops --login {your iothubowner connection string}
    ```

1. 別のコマンド プロンプトで次のコマンドを実行して、デバイスからクラウドへのメッセージをデバイスが送信するようにシミュレートします。

    ```
    iothub-explorer simulate-device {your device name} --send "My test message" --login {your iothubowner connection string}
    ```

1. シミュレートされたデバイスが IoT Hub に接続すると、監視イベントが最初のコマンド プロンプトに表示されます。

## <a name="next-steps"></a>次のステップ
IoT Hub の機能を詳しく調べるには、次のリンクを使用してください。

* [IoT Hub 開発者ガイド][lnk-devguide]
* [IoT Gateway SDK を使用したデバイスのシミュレーション][lnk-gateway]

<!-- Links and images -->
[1]: media/iot-hub-operations-monitoring/enable-OM-1.png
[2]: media/iot-hub-operations-monitoring/enable-OM-2.png

[lnk-get-started]: iot-hub-csharp-csharp-getstarted.md
[lnk-diagnostic-metrics]: iot-hub-metrics.md
[lnk-scaling]: iot-hub-scaling.md
[lnk-dr]: iot-hub-ha-dr.md

[lnk-devguide]: iot-hub-devguide.md
[lnk-gateway]: iot-hub-linux-gateway-sdk-simulated-device.md
[lnk-iothub-explorer]: https://github.com/azure/iothub-explorer

