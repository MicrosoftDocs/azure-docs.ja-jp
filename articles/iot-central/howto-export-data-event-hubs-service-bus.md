---
title: Azure Event Hubs および Azure Service Bus にデータをエクスポートする | Microsoft Docs
description: Azure IoT Central アプリケーションから Azure Event Hubs および Azure Service Bus にデータをエクスポートする方法
services: iot-central
author: viv-liu
ms.author: viviali
ms.date: 07/09/2019
ms.topic: conceptual
ms.service: iot-central
manager: peterpr
ms.openlocfilehash: c6f10352646350152c5aac795885231697e81fe7
ms.sourcegitcommit: fa45c2bcd1b32bc8dd54a5dc8bc206d2fe23d5fb
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/12/2019
ms.locfileid: "67850203"
---
# <a name="export-your-data-in-azure-iot-central"></a>Azure IoT Central でデータをエクスポートする

*このトピックでは、管理者に適用されます。*

この記事では、データを自身の **Azure Event Hubs** および **Azure Service Bus** のインスタンスにエクスポートする Azure IoT Central の継続的データ エクスポート機能の使用方法について説明します。 ウォーム パスの分析情報と分析のため、**測定**、**デバイス**、および**デバイス テンプレート**を自身が指定した場所にエクスポートできます。 これには、Azure Stream Analytics でカスタム ルールをトリガーすること、Azure Logic Apps でカスタム ワークフローをトリガーすること、データを変換し、Azure Functions を介してそれを渡すことが含まれます。 

> [!Note]
> この場合も、連続データ エクスポートを有効にすると、その時点以降のデータのみが取得されます。 現在は、連続データ エクスポートがオフになっていたときのデータを取得することはできません。 より多くの履歴データを保持するには、連続データ エクスポートを早い段階で有効にしてください。


## <a name="prerequisites"></a>前提条件

- ご利用の IoT Central アプリケーションの管理者であること

## <a name="set-up-export-destination"></a>エクスポート先の設定

エクスポート先となる既存の Event Hubs/Service Bus がない場合は、次の手順に従います。

## <a name="create-event-hubs-namespace"></a>Event Hubs 名前空間の作成

1. [Azure portal で新しい Event Hubs 名前空間](https://ms.portal.azure.com/#create/Microsoft.EventHub)を作成します。 詳細については、[Azure Event Hubs のドキュメント](https://docs.microsoft.com/azure/event-hubs/event-hubs-create)を参照してください。
2. サブスクリプションを選択します。 

    > [!Note] 
    > これで、ご使用の従量課金制 IoT Central アプリケーションのサブスクリプションとは**異なる**別のサブスクリプションにデータをエクスポートできます。 この場合、接続文字列を使用して接続します。
3. Event Hubs 名前空間にイベント ハブを作成します。 名前空間に移動し、上部の **[+ イベント ハブ]** を選択して、イベント ハブ インスタンスを作成します。

## <a name="create-service-bus-namespace"></a>Service Bus 名前空間の作成

1. [Azure portal で新しい Service Bus 名前空間](https://ms.portal.azure.com/#create/Microsoft.ServiceBus.1.0.5)を作成します。 詳細については、[Azure Service Bus のドキュメント](https://docs.microsoft.com/azure/service-bus-messaging/service-bus-create-namespace-portal)を参照してください。
2. サブスクリプションを選択します。 

    > [!Note] 
    > これで、ご使用の従量課金制 IoT Central アプリケーションのサブスクリプションとは**異なる**別のサブスクリプションにデータをエクスポートできます。 この場合、接続文字列を使用して接続します。

3. Service Bus 名前空間に移動し、上部の **[+ キュー]** または **[+ トピック]** を選択して、エクスポート先のキューまたはトピックを作成します。


## <a name="set-up-continuous-data-export"></a>連続データ エクスポートを設定する

これでデータのエクスポート先となる Event Hubs/Service Bus ができました。次の手順に従って、継続的データ エクスポートを設定します。 

1. ご使用の IoT Central アプリケーションにサインインします。

2. 左側のメニューで、 **[継続的データ エクスポート]** を選択します。

    > [!Note]
    > 左側のメニューに [継続的データ エクスポート] が表示されない場合は、そのアプリの管理者ではありません。 データ エクスポートの設定について、管理者に問い合わせてください。

    ![新しい cde イベント ハブの作成](media/howto-export-data/export_menu1.png)

3. 右上の **[+ 新規]** ボタンを選択します。 エクスポート先として、 **[Azure Event Hubs]** または **[Azure Service Bus]** を選択します。 

    > [!NOTE] 
    > アプリごとのエクスポートの最大数は 5 です。 

    ![新しい継続的データ エクスポートの作成](media/howto-export-data/export_new1.png)

4. ドロップダウン リスト ボックスで、お使いの **Event Hubs 名前空間/Service Bus 名前空間**を選択します。 リスト内の最後のオプション ( **[Enter a connection string]\(接続文字列を入力する\)** ) を選択することもできます。 

    > [!NOTE] 
    > ご使用の **IoT Central アプリと同じサブスクリプション**の Storage Account/Event Hubs 名前空間/Service Bus 名前空間のみが表示されます。 このサブスクリプションとは異なる場所にエクスポートする場合は、 **[Enter a connection string]\(接続文字列を入力する\)** を選択して、手順 5 に進みます。

    > [!NOTE] 
    > 7 日間の試用版アプリの場合、継続的データ エクスポートを構成する唯一の方法は、接続文字列を使用することです。 7 日間の試用版アプリに関連付けられた Azure サブスクリプションがないのはこのためです。

    ![新しい cde イベント ハブの作成](media/howto-export-data/export_create1.png)

5. (省略可能) **[Enter a connection string]\(接続文字列を入力する\)** を選択すると、接続文字列を貼り付けるための新しいボックスが表示されます。 次の接続文字列を取得するには:
    - Event Hubs や Service Bus。Azure portal で名前空間に移動します。
        - **[設定]** で、 **[共有アクセス ポリシー]** を選択します。
        - 既定の **RootManageSharedAccessKey** を選択するか、新しいポリシーを作成します。
        - プライマリまたはセカンダリの接続文字列をコピーします。
 
6. ドロップダウン リスト ボックスから、イベント ハブ/キューまたはトピックを選択します。

7. **[Data to export]\(エクスポートするデータ\)** で、エクスポートするデータの種類を **[オン]** に設定して指定します。

6. 継続的データ エクスポートを有効にするには、 **[データのエクスポート]** が **[オン]** になっていることを確認します。 **[保存]** を選択します。

    ![連続データ エクスポートを構成する](media/howto-export-data/export_list1.png)

7. 数分後に、選択したエクスポート先にデータが表示されます。


## <a name="export-to-azure-event-hubs-and-azure-service-bus"></a>Azure Event Hubs および Azure Service Bus にエクスポートする

測定、デバイス、およびデバイス テンプレートのデータは、ほぼリアルタイムに、ご利用のイベント ハブや Service Bus のキュー、またはトピックにエクスポートされます。 エクスポートされた測定データには、測定自体の値だけでなく、デバイスが IoT Central に送信したメッセージ全体が含まれています。 エクスポートされたデバイス データには、すべてのデバイスのプロパティと設定に対する変更が含まれていて、エクスポートされたデバイス テンプレートには、すべてのデバイス テンプレートに対する変更が含まれています。 エクスポートされたデータは "body" プロパティ内にあり、JSON 形式になっています。

> [!NOTE]
> エクスポート先として Service Bus を選択する場合、キューおよびトピックで、**セッションまたは重複データ検出が有効になっていてはいけません**。 これらのオプションのいずれかが有効になっていると、一部のメッセージがキューやトピックに到着しません。

### <a name="measurements"></a>測定

新しいメッセージは、IoT Central がデバイスからメッセージを受信した後、迅速にエクスポートされます。 Event Hubs と Service Bus でエクスポートされた各メッセージには、デバイスが "body" プロパティで送信した完全なメッセージが JSON 形式で含まれています。 

> [!NOTE]
> 測定を送信するデバイスは、デバイス ID で表されます (以下のセクションを参照)。 デバイスの名前を取得するには、デバイスのデータをエクスポートし、デバイス メッセージの **deviceId** と一致する **connectionDeviceId** を使用して各メッセージを関連付けます。

次の例は、イベント ハブまたは Service Bus のキューまたはトピックで受信した測定データに関するメッセージを示しています。

```json
{
  "body": {
    "humidity": 29.06963648666288,
    "temp": 8.4503795661685,
    "pressure": 1075.8334910110093,
    "magnetometerX": 408.6966458887116,
    "magnetometerY": -532.8809796603962,
    "magnetometerZ": 174.70653875528205,
    "accelerometerX": 1481.546749013788,
    "accelerometerY": -1139.4316656437406,
    "accelerometerZ": 811.6928695575307,
    "gyroscopeX": 442.19879163299856,
    "gyroscopeY": 123.23710975717177,
    "gyroscopeZ": 708.5397575786151,
    "deviceState": "DANGER"
  },
  "annotations": {
    "iothub-connection-device-id": "<connectionDeviceId>",
    "iothub-connection-auth-method": "{\"scope\":\"hub\",\"type\":\"sas\",\"issuer\":\"iothub\",\"acceptingIpFilterRule\":null}",
    "iothub-connection-auth-generation-id": "<generationId>",
    "iothub-enqueuedtime": 1539381029965,
    "iothub-message-source": "Telemetry",
    "x-opt-sequence-number": 25325,
    "x-opt-offset": "<offset>",
    "x-opt-enqueued-time": 1539381030200
  },
  "sequenceNumber": 25325,
  "enqueuedTimeUtc": "2018-10-12T21:50:30.200Z",
  "offset": "<offset>",
  "properties": {
    "content_type": "application/json",
    "content_encoding": "utf-8"
  }
}
```

### <a name="devices"></a>デバイス

デバイス データを含むメッセージは、数分に 1 回、イベント ハブまたは Service Bus のキューまたはトピックに送信されます。 つまり数分ごとに、以下に関するデータを含むメッセージのバッチが到達します
- 追加された新しいデバイス
- プロパティや設定の値が変更されたデバイス

各メッセージは、最後にエクスポートされたメッセージ以降、デバイスに加えられた 1 つ以上の変更を表しています。 各メッセージで送信される情報には、以下が含まれます。
- `id` IoT Central でデバイスの
- デバイスの `name`
- `deviceId`Device Provisioning Service[ の](https://aka.ms/iotcentraldocsdps)
- デバイステンプレート情報
- プロパティ値
- 設定値。

> [!NOTE]
> 最後のバッチ以降に削除されたデバイスはエクスポートされません。 現在のところ、エクスポートされたメッセージ内に、削除されたデバイスを示すものはありません。
>
> 各デバイスが属するデバイス テンプレートは、デバイス テンプレート ID によって表されます。 デバイス テンプレートの名前を取得するには、デバイス テンプレートのデータも必ずエクスポートしてください。

次の例は、イベント ハブまたは Service Bus のキューまたはトピック内にあるデバイス データに関するメッセージを示しています。


```json
{
  "body": {
    "id": "<id>",
    "name": "<deviceName>",
    "simulated": true,
    "deviceId": "<deviceId>",
    "deviceTemplate": {
      "id": "<templateId>",
      "version": "1.0.0"
    },
    "properties": {
      "cloud": {
        "location": "Seattle"
      },
      "device": {
        "dieNumber": 5445.5862873026645
      }
    },
    "settings": {
      "device": {
        "fanSpeed": 0
      }
    }
  },
  "annotations": {
    "iotcentral-message-source": "devices",
    "x-opt-partition-key": "<partitionKey>",
    "x-opt-sequence-number": 39740,
    "x-opt-offset": "<offset>",
    "x-opt-enqueued-time": 1539274959654
  },
  "partitionKey": "<partitionKey>",
  "sequenceNumber": 39740,
  "enqueuedTimeUtc": "2018-10-11T16:22:39.654Z",
  "offset": "<offset>",
}
```

### <a name="device-templates"></a>デバイス テンプレート

デバイス テンプレート データを含むメッセージは、数分に 1 回、イベント ハブまたは Service Bus のキューまたはトピックに送信されます。 つまり数分ごとに、以下に関するデータを含むメッセージのバッチが到達します
- 追加された新しいデバイス テンプレート
- 測定、プロパティ、および設定の定義が変更されたデバイス テンプレート

各メッセージは、最後にエクスポートされたメッセージ以降、デバイス テンプレートに加えられた 1 つ以上の変更を表しています。 各メッセージで送信される情報には、以下が含まれます。
- デバイス テンプレートの`id`
- デバイス テンプレートの`name`
- デバイス テンプレートの`version`
- 測定のデータ型および最小/最大値
- プロパティのデータ型と既定値
- 設定のデータ型と既定値

> [!NOTE]
> 最後のバッチ以降に削除されたデバイス テンプレートはエクスポートされません。 現在のところ、エクスポートされたメッセージ内に、削除されたデバイス テンプレートを示すものはありません。

次の例は、イベント ハブまたは Service Bus のキューまたはトピック内にあるデバイス テンプレート データに関するメッセージを示しています。

```json
{
  "body": {
    "id": "<id>",
    "version": "1.0.0",
    "name": "<templateName>",
    "measurements": {
      "telemetry": {
        "humidity": {
          "dataType": "double",
          "name": "humidity"
        },
        "pressure": {
          "dataType": "double",
          "name": "pressure"
        },
        "temp": {
          "dataType": "double",
          "name": "temperature"
        }
      }
    },
    "properties": {
      "cloud": {
        "location": {
          "dataType": "string",
          "name": "Location"
        }
      },
      "device": {
        "dieNumber": {
          "dataType": "double",
          "name": "Die Number"
        }
      }
    },
    "settings": {
      "device": {
        "fanSpeed": {
          "dataType": "double",
          "name": "Fan Speed",
          "initialValue": 0
        }
      }
    }
  },
  "annotations": {
    "iotcentral-message-source": "deviceTemplates",
    "x-opt-partition-key": "<partitionKey>",
    "x-opt-sequence-number": 25315,
    "x-opt-offset": "<offset>",
    "x-opt-enqueued-time": 1539274985085
  },
  "partitionKey": "<partitionKey>",
  "sequenceNumber": 25315,
  "enqueuedTimeUtc": "2018-10-11T16:23:05.085Z",
  "offset": "<offset>",
}
```

## <a name="next-steps"></a>次の手順

これで、Azure Event Hubs と Azure Service Bus にデータをエクスポートする方法を理解できたので、次の手順に進みます。

> [!div class="nextstepaction"]
> [Azure Functions をトリガーする方法](howto-trigger-azure-functions.md)
