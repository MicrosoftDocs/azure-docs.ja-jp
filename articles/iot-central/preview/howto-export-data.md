---
title: Azure IoT Central データをエクスポートする | Microsoft Docs
description: Azure IoT Central アプリケーションから Azure Event Hubs、Azure Service Bus、および Azure Blob Storage にデータをエクスポートする方法
services: iot-central
author: viv-liu
ms.author: viviali
ms.date: 12/06/2019
ms.topic: conceptual
ms.service: iot-central
manager: corywink
ms.openlocfilehash: 1aac5af916e414178676a1caf42fead41109de68
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/10/2019
ms.locfileid: "74974463"
---
# <a name="export-your-azure-iot-central-data-preview-features"></a>Azure IoT Central データをエクスポートする (プレビュー機能)

[!INCLUDE [iot-central-pnp-original](../../../includes/iot-central-pnp-original-note.md)]

*このトピックでは、管理者に適用されます。*

この記事では、Azure IoT Central で連続データ エクスポートを使用して、**Azure Event Hubs**、**Azure Service Bus**、または **Azure Blob Storage** のインスタンスにデータをエクスポートする方法について説明します。 データは JSON 形式でエクスポートされ、テレメトリ、デバイス情報、デバイス テンプレート情報を含めることができます。 エクスポートされたデータは次のことに使用します。

- ウォーム パスの分析情報と分析。 このオプションには、Azure Stream Analytics でのカスタム ルールのトリガー、Azure Logic Apps でのカスタム ワークフローのトリガー、または変換するための Azure Functions を介した受け渡しなどが含まれます。
- Azure Machine Learning でのモデルのトレーニングや Microsoft Power BI での長期傾向分析などのコールド パス分析。

> [!Note]
> 連続データ エクスポートを有効にすると、その時点以降のデータのみが取得されます。 現在は、連続データ エクスポートがオフになっていたときのデータを取得することはできません。 より多くの履歴データを保持するには、連続データ エクスポートを早い段階で有効にしてください。

## <a name="prerequisites"></a>前提条件

ご利用の IoT Central アプリケーションの管理者であること

## <a name="set-up-export-destination"></a>エクスポート先の設定

連続データ エクスポートを構成する前に、エクスポート先が存在している必要があります。

### <a name="create-event-hubs-namespace"></a>Event Hubs 名前空間の作成

エクスポート先となる既存の Event Hubs 名前空間がない場合は、次の手順に従います。

1. [Azure portal で新しい Event Hubs 名前空間](https://ms.portal.azure.com/#create/Microsoft.EventHub)を作成します。 詳細については、[Azure Event Hubs のドキュメント](../../event-hubs/event-hubs-create.md)を参照してください。

2. サブスクリプションを選択します。 ご使用の従量課金制 IoT Central アプリケーションと同じサブスクリプションではない、他のサブスクリプションにデータをエクスポートできます。 この場合、接続文字列を使用して接続します。

3. Event Hubs 名前空間にイベント ハブを作成します。 名前空間に移動し、上部の **[+ イベント ハブ]** を選択して、イベント ハブ インスタンスを作成します。

### <a name="create-service-bus-namespace"></a>Service Bus 名前空間の作成

エクスポート先となる既存の Service Bus 名前空間がない場合は、次の手順に従います。

1. [Azure portal で新しい Service Bus 名前空間](https://ms.portal.azure.com/#create/Microsoft.ServiceBus.1.0.5)を作成します。 詳細については、[Azure Service Bus のドキュメント](../../service-bus-messaging/service-bus-create-namespace-portal.md)を参照してください。
2. サブスクリプションを選択します。 ご使用の従量課金制 IoT Central アプリケーションと同じサブスクリプションではない、他のサブスクリプションにデータをエクスポートできます。 この場合、接続文字列を使用して接続します。

3. Service Bus 名前空間に移動し、上部の **[+ キュー]** または **[+ トピック]** を選択して、エクスポート先のキューまたはトピックを作成します。

エクスポート先として Service Bus を選択する場合、キューとトピックでセッションまたは重複の検出が有効になっていてはいけません。 これらのオプションのいずれかが有効になっていると、一部のメッセージがキューやトピックに到着しません。

### <a name="create-storage-account"></a>ストレージ アカウントの作成

エクスポート先となる既存の Azure Storage アカウントがない場合は、次の手順に従います。

1. [Azure portal で新しいストレージ アカウント](https://ms.portal.azure.com/#create/Microsoft.StorageAccount-ARM)を作成します。 新しい [Azure Blob Storage アカウント](https://aka.ms/blobdocscreatestorageaccount)または [Azure Data Lake Storage v2 ストレージ アカウント](../../storage/blobs/data-lake-storage-quickstart-create-account.md)の作成の詳細を確認できます。

    - データを Azure Data Lake Storage v2 ストレージ アカウントにエクスポートする場合は、 **[アカウントの種類]** として **[BlobStorage]** を選択する必要があります。
    - 従量課金制 IoT Central アプリケーションとは異なるサブスクリプションのストレージ アカウントにデータをエクスポートできます。 この場合、接続文字列を使用して接続します。

2. ご自分のストレージ アカウントでコンテナーを作成します。 ストレージ アカウントに移動します。 **[Blob service]** で **[BLOB の参照]** を選択します。 上部の **[+ コンテナー]** を選択して、新しいコンテナーを作成します。

## <a name="set-up-continuous-data-export"></a>連続データ エクスポートを設定する

これでデータのエクスポート先ができたので、次の手順に従って連続データ エクスポートを設定します。

1. ご使用の IoT Central アプリケーションにサインインします。

2. 左側のペインで、 **[データのエクスポート]** を選択します。

    > [!Note]
    > 左側のペインに [データのエクスポート] が表示されない場合は、アプリの管理者ではありません。 データ エクスポートの設定について、管理者に問い合わせてください。

3. 右上の **[+ 新規]** ボタンを選択します。 エクスポート先として、**Azure Event Hubs**、**Azure Service Bus**、または **Azure Blob Storage** のいずれかを選択します。 アプリケーションごとのエクスポートの最大数は 5 です。

    ![新しい継続的データ エクスポートの作成](media/howto-export-data/export-new2.png)

4. ドロップダウン リスト ボックスで、**Event Hubs 名前空間**、**Service Bus 名前空間**、**Storage Account 名前空間**、または **[接続文字列を入力してください]** を選択します。

    - ご使用の IoT Central アプリケーションと同じサブスクリプション内の Storage Account、Event Hubs 名前空間、Service Bus 名前空間のみが表示されます。 このサブスクリプションとは異なる場所にエクスポートする場合は、 **[Enter a connection string]\(接続文字列を入力する\)** を選択して、手順 5 に進みます。
    - 7 日間の試用版アプリの場合、連続データ エクスポートを構成する唯一の方法は、接続文字列を使用することです。 7 日間の試用版アプリには、関連付けられた Azure サブスクリプションがありません。

    ![新しい Event Hub を作成する](media/howto-export-data/export-eh.png)

5. (省略可能) **[Enter a connection string]\(接続文字列を入力する\)** を選択すると、接続文字列を貼り付けるための新しいボックスが表示されます。 次の接続文字列を取得するには:
    - Event Hubs や Service Bus。Azure portal で名前空間に移動します。
        - **[設定]** で、 **[共有アクセス ポリシー]** を選択します。
        - 既定の **RootManageSharedAccessKey** を選択するか、新しいポリシーを作成します。
        - プライマリまたはセカンダリの接続文字列をコピーします。
    - ストレージ アカウント。Azure portal でストレージ アカウントに移動します。
        - **[設定]** で **[アクセス キー]** を選択します。
        - key1 接続文字列または key2 接続文字列のいずれかをコピーします。

6. ドロップダウン リスト ボックスから、イベント ハブ、キュー、トピック、またはコンテナーを選択します。

7. **[エクスポートするデータ]** で、種類を **[オン]** に設定して、エクスポートするデータの種類を選択します。

8. 連続データ エクスポートを有効にするには、 **[データのエクスポート]** トグルが **[オン]** になっていることを確認します。 **[保存]** を選択します。

9. 数分後に、選択したエクスポート先にデータが表示されます。

## <a name="export-contents-and-format"></a>エクスポートの内容と形式

エクスポートされたテレメトリ データには、テレメトリ値自体だけでなく、デバイスが IoT Central に送信したメッセージ全体が含まれています。 エクスポートされたデバイス データには、すべてのデバイスのプロパティとメタデータに対する変更が含まれており、エクスポートされたデバイス テンプレートには、すべてのデバイス テンプレートに対する変更が含まれています。

Event Hubs と Service Bus では、データはほぼリアルタイムでエクスポートされます。 データは body プロパティに入り、JSON 形式になります (例については、以下を参照)。

Blob Storage の場合、データは 1 分に 1 回エクスポートされ、各ファイルには、最後にエクスポートされたファイル以降の変更のバッチが含まれます。 エクスポートされたデータは、JSON 形式で 3 つのフォルダーに配置されます。 ストレージ アカウントでの既定のパスは次のとおりです。

- テレメトリ: _{container}/{app-id}/telemetry/{YYYY}/{MM}/{dd}/{hh}/{mm}/{filename}_
- デバイス: _{container}/{app-id}/devices/{YYYY}/{MM}/{dd}/{hh}/{mm}/{filename}_
- デバイス テンプレート: _{container}/{app-id}/deviceTemplates/{YYYY}/{MM}/{dd}/{hh}/{mm}/{filename}_

Azure portal でエクスポートされたファイルを参照するには、ファイルに移動し、 **[BLOB の編集]** タブを選択します。


## <a name="telemetry"></a>テレメトリ

Event Hubs と Service Bus の場合、IoT Central がデバイスからメッセージを受信すると新しいメッセージが直ちにエクスポートされ、エクスポートされた各メッセージには、デバイスが body プロパティで送信した完全なメッセージが JSON 形式で含まれています。

Blob Storage の場合、メッセージはバッチ処理され、1 分に 1 回エクスポートされます。 エクスポートされたファイルでは、[IoT Hub メッセージ ルーティング](../../iot-hub/iot-hub-csharp-csharp-process-d2c.md)によって Blob Storage にエクスポートされたメッセージ ファイルと同じ形式が使用されます。 

> [!NOTE]
> Blob Storage の場合、デバイスが `contentType: application/JSON` と `contentEncoding:utf-8` (または `utf-16`、`utf-32`) を持つメッセージを送信していることを確認してください。 例については、[IoT Hub のドキュメント](../../iot-hub/iot-hub-devguide-routing-query-syntax.md#message-routing-query-based-on-message-body)を参照してください。

テレメトリを送信したデバイスは、デバイス ID で表されます (以下のセクションを参照)。 デバイスの名前を取得するには、デバイスのデータをエクスポートし、デバイス メッセージの **deviceId** と一致する **connectionDeviceId** を使用して各メッセージを関連付けます。

これは、イベント ハブまたは Service Bus のキューまたはトピックで受信したメッセージの例です。

```json
{
  "body":{
    "temp":67.96099945281145,
    "humid":58.51139305465015,
    "pm25":36.91162432340187
  },
  "annotations":{
    "iothub-connection-device-id":"<deviceId>",
    "iothub-connection-auth-method":"{\"scope\":\"hub\",\"type\":\"sas\",\"issuer\":\"iothub\",\"acceptingIpFilterRule\":null}",
    "iothub-connection-auth-generation-id":"<generationId>",
    "iothub-enqueuedtime":1539381029965,
    "iothub-message-source":"Telemetry",
    "x-opt-sequence-number":25325,
    "x-opt-offset":"<offset>",
    "x-opt-enqueued-time":1539381030200
  },
  "sequenceNumber":25325,
  "enqueuedTimeUtc":"2018-10-12T21:50:30.200Z",
  "offset":"<offset>",
  "properties":{
    "content_type":"application/json",
    "content_encoding":"utf-8"
  }
}
```

次に、Blob Storage にエクスポートされたレコードの例を示します。

```json
{
  "EnqueuedTimeUtc":"2019-09-26T17:46:09.8870000Z",
  "Properties":{

  },
  "SystemProperties":{
    "connectionDeviceId":"<deviceid>",
    "connectionAuthMethod":"{\"scope\":\"device\",\"type\":\"sas\",\"issuer\":\"iothub\",\"acceptingIpFilterRule\":null}",
    "connectionDeviceGenerationId":"637051167384630591",
    "contentType":"application/json",
    "contentEncoding":"utf-8",
    "enqueuedTime":"2019-09-26T17:46:09.8870000Z"
  },
  "Body":{
    "temp":49.91322758395974,
    "humid":49.61214852573155,
    "pm25":25.87332214661367
  }
}
```

## <a name="devices"></a>デバイス

スナップショット内の各メッセージまたはレコードは、最後にエクスポートされたメッセージ以降に、デバイスとそのプロパティに加えられた 1 つ以上の変更を表しています。 これには次のものが含まれます

- `@id` IoT Central でデバイスの
- デバイスの `name`
- `deviceId`Device Provisioning Service[ の](../core/howto-connect-nodejs.md?toc=/azure/iot-central/preview/toc.json&bc=/azure/iot-central/preview/breadcrumb/toc.json)
- デバイステンプレート情報
- プロパティ値

各デバイスが属するデバイス テンプレートは `instanceOf` で表されます。 デバイス テンプレートの名前と追加情報を取得するには、デバイス テンプレートのデータも必ずエクスポートしてください。

削除されたデバイスはエクスポートされません。 現在のところ、エクスポートされたメッセージ内に、削除されたデバイスを示すものはありません。

Event Hubs と Service Bus の場合、デバイス データを含むメッセージは、IoT Central に表示されるように、ほぼリアルタイムでイベント ハブまたは Service Bus のキューまたはトピックに送信されます。 

Blob Storage の場合は、最後のものが書き込まれてから以降のすべての変更を含む新しいスナップショットが 1 分間に 1 回エクスポートされます。

次に示すのは、イベント ハブまたは Service Bus のキューまたはトピック内のデバイスとプロパティ データに関するメッセージの例です。

```json
{
  "body":{
    "@id":"<id>",
    "@type":"Device",
    "displayName":"Airbox - 266d30aedn5",
    "data":{
      "$cloudProperties":{
        "Color":"blue"
      },
      "EnvironmentalSensor":{
        "thsensormodel":{
          "reported":{
            "value":"A1",
            "$lastUpdatedTimestamp":"2019-10-02T18:14:49.3820326Z"
          }
        },
        "pm25sensormodel":{
          "reported":{
            "value":"P1",
            "$lastUpdatedTimestamp":"2019-10-02T18:14:49.3820326Z"
          }
        }
      },
      "urn_azureiot_DeviceManagement_DeviceInformation":{
        "totalStorage":{
          "reported":{
            "value":3088.1959855710156,
            "$lastUpdatedTimestamp":"2019-10-02T18:14:49.3820326Z"
          }
        },
        "totalMemory":{
          "reported":{
            "value":16005.703586477555,
            "$lastUpdatedTimestamp":"2019-10-02T18:14:49.3820326Z"
          }
        }
      }
    },
    "instanceOf":"<templateId>",
    "deviceId":"<deviceId>",
    "simulated":true
  },
  "annotations":{
    "iotcentral-message-source":"devices",
    "x-opt-partition-key":"<partitionKey>",
    "x-opt-sequence-number":39740,
    "x-opt-offset":"<offset>",
    "x-opt-enqueued-time":1539274959654
  },
  "partitionKey":"<partitionKey>",
  "sequenceNumber":39740,
  "enqueuedTimeUtc":"2019-10-02T18:14:49.3820326Z",
  "offset":"<offset>"
}
```

Blob Storage のデバイスとプロパティ データを含むスナップショットの例を次に示します。 エクスポートされたファイルには、1 つのレコードにつき 1 行が含まれます。

```json
{
  "@id":"<id-value>",
  "@type":"Device",
  "displayName":"Airbox",
  "data":{
    "$cloudProperties":{
        "Color":"blue"
    },
    "EnvironmentalSensor":{
      "thsensormodel":{
        "reported":{
          "value":"Neque quia et voluptatem veritatis assumenda consequuntur quod.",
          "$lastUpdatedTimestamp":"2019-09-30T20:35:43.8478978Z"
        }
      },
      "pm25sensormodel":{
        "reported":{
          "value":"Aut alias odio.",
          "$lastUpdatedTimestamp":"2019-09-30T20:35:43.8478978Z"
        }
      }
    },
    "urn_azureiot_DeviceManagement_DeviceInformation":{
      "totalStorage":{
        "reported":{
          "value":27900.9730905171,
          "$lastUpdatedTimestamp":"2019-09-30T20:35:43.8478978Z"
        }
      },
      "totalMemory":{
        "reported":{
          "value":4667.82916715811,
          "$lastUpdatedTimestamp":"2019-09-30T20:35:43.8478978Z"
        }
      }
    }
  },
  "instanceOf":"<template-id>",
  "deviceId":"<device-id>",
  "simulated":true
}
```

## <a name="device-templates"></a>デバイス テンプレート

各メッセージまたはスナップショット レコードは、最後にエクスポートされたメッセージ以降にデバイス テンプレートに加えられた 1 つ以上の変更を表しています。 各メッセージまたはレコードで送信される情報としては、次のものがあります。

- 上記のデバイス ストリームの `instanceOf` に一致するデバイス テンプレートの `@id`
- デバイス テンプレートの`name`
- デバイス テンプレートの`version`
- デバイスの `capabilityModel` (その `interfaces` を含む)、テレメトリ、プロパティ、およびコマンドの定義
- `cloudProperties` の定義
- `capabilityModel` に埋め込まれたオーバライドと初期値

削除されたデバイス テンプレートはエクスポートされません。 現在のところ、エクスポートされたメッセージ内に、削除されたデバイス テンプレートを示すものはありません。

Event Hubs と Service Bus の場合、デバイス テンプレート データを含むメッセージは、IoT Central に表示されるように、ほぼリアルタイムでイベント ハブまたは Service Bus のキューまたはトピックに送信されます。 

Blob Storage の場合は、最後のものが書き込まれてから以降のすべての変更を含む新しいスナップショットが 1 分間に 1 回エクスポートされます。

次に示すのは、イベント ハブまたは Service Bus のキューまたはトピック内のデバイス テンプレート データに関するメッセージの例です。

```json
{
  "body":{
    "@id":"<template-id>",
    "@type":"DeviceModelDefinition",
    "displayName":"Airbox",
    "capabilityModel":{
      "@id":"<id>",
      "@type":"CapabilityModel",
      "implements":[
        {
          "@id":"<id>",
          "@type":"InterfaceInstance",
          "name":"EnvironmentalSensor",
          "schema":{
            "@id":"<id>",
            "@type":"Interface",
            "comment":"Requires temperature and humidity sensors.",
            "description":"Provides functionality to report temperature, humidity. Provides telemetry, commands and read-write properties",
            "displayName":"Environmental Sensor",
            "contents":[
              {
                "@id":"<id>",
                "@type":"Telemetry",
                "description":"Current temperature on the device",
                "displayName":"Temperature",
                "name":"temp",
                "schema":"double",
                "unit":"Units/Temperature/celsius",
                "valueDetail":{
                  "@id":"<id>",
                  "@type":"ValueDetail/NumberValueDetail",
                  "minValue":{
                    "@value":"50"
                  }
                },
                "visualizationDetail":{
                  "@id":"<id>",
                  "@type":"VisualizationDetail"
                }
              },
              {
                "@id":"<id>",
                "@type":"Telemetry",
                "description":"Current humidity on the device",
                "displayName":"Humidity",
                "name":"humid",
                "schema":"integer"
              },
              {
                "@id":"<id>",
                "@type":"Telemetry",
                "description":"Current PM2.5 on the device",
                "displayName":"PM2.5",
                "name":"pm25",
                "schema":"integer"
              },
              {
                "@id":"<id>",
                "@type":"Property",
                "description":"T&H Sensor Model Name",
                "displayName":"T&H Sensor Model",
                "name":"thsensormodel",
                "schema":"string"
              },
              {
                "@id":"<id>",
                "@type":"Property",
                "description":"PM2.5 Sensor Model Name",
                "displayName":"PM2.5 Sensor Model",
                "name":"pm25sensormodel",
                "schema":"string"
              }
            ]
          }
        },
        {
          "@id":"<id>",
          "@type":"InterfaceInstance",
          "name":"urn_azureiot_DeviceManagement_DeviceInformation",
          "schema":{
            "@id":"<id>",
            "@type":"Interface",
            "displayName":"Device information",
            "contents":[
              {
                "@id":"<id>",
                "@type":"Property",
                "comment":"Total available storage on the device in kilobytes. Ex. 20480000 kilobytes.",
                "displayName":"Total storage",
                "name":"totalStorage",
                "displayUnit":"kilobytes",
                "schema":"long"
              },
              {
                "@id":"<id>",
                "@type":"Property",
                "comment":"Total available memory on the device in kilobytes. Ex. 256000 kilobytes.",
                "displayName":"Total memory",
                "name":"totalMemory",
                "displayUnit":"kilobytes",
                "schema":"long"
              }
            ]
          }
        }
      ],
      "displayName":"AAEONAirbox52"
    },
    "solutionModel":{
      "@id":"<id>",
      "@type":"SolutionModel",
      "cloudProperties":[
        {
          "@id":"<id>",
          "@type":"CloudProperty",
          "displayName":"Color",
          "name":"Color",
          "schema":"string",
          "valueDetail":{
            "@id":"<id>",
            "@type":"ValueDetail/StringValueDetail"
          },
          "visualizationDetail":{
            "@id":"<id>",
            "@type":"VisualizationDetail"
          }
        }
      ]
    },
    "annotations":{
      "iotcentral-message-source":"deviceTemplates",
      "x-opt-partition-key":"<partitionKey>",
      "x-opt-sequence-number":25315,
      "x-opt-offset":"<offset>",
      "x-opt-enqueued-time":1539274985085
    },
    "partitionKey":"<partitionKey>",
    "sequenceNumber":25315,
    "enqueuedTimeUtc":"2019-10-02T16:23:05.085Z",
    "offset":"<offset>"
  }
}
```

Blob Storage のデバイスとプロパティ データを含むスナップショットの例を次に示します。 エクスポートされたファイルには、1 つのレコードにつき 1 行が含まれます。

```json
{
  "@id":"<template-id>",
  "@type":"DeviceModelDefinition",
  "displayName":"Airbox",
  "capabilityModel":{
    "@id":"<id>",
    "@type":"CapabilityModel",
    "implements":[
      {
        "@id":"<id>",
        "@type":"InterfaceInstance",
        "name":"EnvironmentalSensor",
        "schema":{
          "@id":"<id>",
          "@type":"Interface",
          "comment":"Requires temperature and humidity sensors.",
          "description":"Provides functionality to report temperature, humidity. Provides telemetry, commands and read-write properties",
          "displayName":"Environmental Sensor",
          "contents":[
            {
              "@id":"<id>",
              "@type":"Telemetry",
              "description":"Current temperature on the device",
              "displayName":"Temperature",
              "name":"temp",
              "schema":"double",
              "unit":"Units/Temperature/celsius",
              "valueDetail":{
                "@id":"<id>",
                "@type":"ValueDetail/NumberValueDetail",
                "minValue":{
                  "@value":"50"
                }
              },
              "visualizationDetail":{
                "@id":"<id>",
                "@type":"VisualizationDetail"
              }
            },
            {
              "@id":"<id>",
              "@type":"Telemetry",
              "description":"Current humidity on the device",
              "displayName":"Humidity",
              "name":"humid",
              "schema":"integer"
            },
            {
              "@id":"<id>",
              "@type":"Telemetry",
              "description":"Current PM2.5 on the device",
              "displayName":"PM2.5",
              "name":"pm25",
              "schema":"integer"
            },
            {
              "@id":"<id>",
              "@type":"Property",
              "description":"T&H Sensor Model Name",
              "displayName":"T&H Sensor Model",
              "name":"thsensormodel",
              "schema":"string"
            },
            {
              "@id":"<id>",
              "@type":"Property",
              "description":"PM2.5 Sensor Model Name",
              "displayName":"PM2.5 Sensor Model",
              "name":"pm25sensormodel",
              "schema":"string"
            }
          ]
        }
      },
      {
        "@id":"<id>",
        "@type":"InterfaceInstance",
        "name":"urn_azureiot_DeviceManagement_DeviceInformation",
        "schema":{
          "@id":"<id>",
          "@type":"Interface",
          "displayName":"Device information",
          "contents":[
            {
              "@id":"<id>",
              "@type":"Property",
              "comment":"Total available storage on the device in kilobytes. Ex. 20480000 kilobytes.",
              "displayName":"Total storage",
              "name":"totalStorage",
              "displayUnit":"kilobytes",
              "schema":"long"
            },
            {
              "@id":"<id>",
              "@type":"Property",
              "comment":"Total available memory on the device in kilobytes. Ex. 256000 kilobytes.",
              "displayName":"Total memory",
              "name":"totalMemory",
              "displayUnit":"kilobytes",
              "schema":"long"
            }
          ]
        }
      }
    ],
    "displayName":"AAEONAirbox52"
  },
  "solutionModel":{
    "@id":"<id>",
    "@type":"SolutionModel",
    "cloudProperties":[
      {
        "@id":"<id>",
        "@type":"CloudProperty",
        "displayName":"Color",
        "name":"Color",
        "schema":"string",
        "valueDetail":{
          "@id":"<id>",
          "@type":"ValueDetail/StringValueDetail"
        },
        "visualizationDetail":{
          "@id":"<id>",
          "@type":"VisualizationDetail"
        }
      }
    ]
  }
}
```

## <a name="next-steps"></a>次の手順

これで、Azure Event Hubs、Azure Service Bus、および Azure Blob Storage にデータをエクスポートする方法がわかったので、次の手順に進みます。

> [!div class="nextstepaction"]
> [Azure Functions をトリガーする方法](../core/howto-trigger-azure-functions.md?toc=/azure/iot-central/preview/toc.json&bc=/azure/iot-central/preview/breadcrumb/toc.json)
