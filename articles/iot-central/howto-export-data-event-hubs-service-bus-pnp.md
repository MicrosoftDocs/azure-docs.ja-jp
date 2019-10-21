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
ms.openlocfilehash: fed9c924274cb66671e233a7dc6d431d81e0dbfb
ms.sourcegitcommit: c2e7595a2966e84dc10afb9a22b74400c4b500ed
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/05/2019
ms.locfileid: "71973202"
---
# <a name="export-your-data-in-azure-iot-central-preview-features"></a>Azure IoT Central でデータをエクスポートする (プレビュー機能)

[!INCLUDE [iot-central-pnp-original](../../includes/iot-central-pnp-original-note.md)]

*このトピックでは、管理者に適用されます。*

この記事では、データを自身の **Azure Event Hubs** および **Azure Service Bus** のインスタンスにエクスポートする Azure IoT Central の継続的データ エクスポート機能の使用方法について説明します。 ウォーム パスの分析情報と分析のために自身が指定した場所に、**テレメトリ**、**デバイス**、および**デバイス テンプレート**をエクスポートできます。 これには、Azure Stream Analytics でのカスタム ルールのトリガー、Azure Logic Apps でのカスタム ワークフローのトリガー、または変換するための Azure Functions を介した受け渡しなどが含まれます。

> [!Note]
> この場合も、連続データ エクスポートを有効にすると、その時点以降のデータのみが取得されます。 現在は、連続データ エクスポートがオフになっていたときのデータを取得することはできません。 より多くの履歴データを保持するには、連続データ エクスポートを早い段階で有効にしてください。

## <a name="prerequisites"></a>前提条件

- ご利用の IoT Central アプリケーションの管理者であること

## <a name="set-up-export-destination"></a>エクスポート先の設定

エクスポート先となる既存の Event Hubs/Service Bus がない場合は、次の手順に従って作成します。

### <a name="create-event-hubs-namespace"></a>Event Hubs 名前空間の作成

1. [Azure portal で新しい Event Hubs 名前空間](https://ms.portal.azure.com/#create/Microsoft.EventHub)を作成します。 詳細については、[Azure Event Hubs のドキュメント](https://docs.microsoft.com/azure/event-hubs/event-hubs-create)を参照してください。
2. サブスクリプションを選択します。 

    > [!Note] 
    > これで、ご使用の従量課金制 IoT Central アプリケーションのサブスクリプションとは**異なる**別のサブスクリプションにデータをエクスポートできます。 この場合、接続文字列を使用して接続します。
3. Event Hubs 名前空間にイベント ハブを作成します。 名前空間に移動し、上部の **[+ イベント ハブ]** を選択して、イベント ハブ インスタンスを作成します。

### <a name="create-service-bus-namespace"></a>Service Bus 名前空間の作成

1. [Azure portal で新しい Service Bus 名前空間](https://ms.portal.azure.com/#create/Microsoft.ServiceBus.1.0.5)を作成します。 詳細については、[Azure Service Bus のドキュメント](https://docs.microsoft.com/azure/service-bus-messaging/service-bus-create-namespace-portal)を参照してください。
2. サブスクリプションを選択します。 

    > [!Note] 
    > これで、ご使用の従量課金制 IoT Central アプリケーションのサブスクリプションとは**異なる**別のサブスクリプションにデータをエクスポートできます。 この場合、接続文字列を使用して接続します。

3. Service Bus 名前空間に移動し、上部の **[+ キュー]** または **[+ トピック]** を選択して、エクスポート先のキューまたはトピックを作成します。


## <a name="set-up-continuous-data-export"></a>連続データ エクスポートを設定する

これでデータのエクスポート先となる Event Hubs/Service Bus ができました。次の手順に従って、継続的データ エクスポートを設定します。 

1. ご使用の IoT Central アプリケーションにサインインします。

2. 左側のメニューで、 **[データのエクスポート]** を選択します。

    > [!Note]
    > 左側のメニューに [データのエクスポート] が表示されない場合は、アプリの管理者ではありません。 データ エクスポートの設定について、管理者に問い合わせてください。

3. 右上の **[+ 新規]** ボタンを選択します。 エクスポート先として、 **[Azure Event Hubs]** または **[Azure Service Bus]** を選択します。 

    > [!NOTE] 
    > アプリごとのエクスポートの最大数は 5 です。 

    ![新しい継続的データ エクスポートの作成](media/howto-export-data-pnp/export-new2.png)

4. ドロップダウン リスト ボックスで、お使いの **Event Hubs 名前空間/Service Bus 名前空間**を選択します。 リスト内の最後のオプション ( **[Enter a connection string]\(接続文字列を入力する\)** ) を選択することもできます。 

    > [!NOTE] 
    > ご使用の **IoT Central アプリと同じサブスクリプション**の Storage Account/Event Hubs 名前空間/Service Bus 名前空間のみが表示されます。 このサブスクリプションとは異なる場所にエクスポートする場合は、 **[Enter a connection string]\(接続文字列を入力する\)** を選択して、手順 5 に進みます。

    > [!NOTE] 
    > 7 日間の試用版アプリの場合、継続的データ エクスポートを構成する唯一の方法は、接続文字列を使用することです。 7 日間の試用版アプリに関連付けられた Azure サブスクリプションがないのはこのためです。

    ![新しい cde イベント ハブの作成](media/howto-export-data-pnp/export-eh.png)

5. (省略可能) **[Enter a connection string]\(接続文字列を入力する\)** を選択すると、接続文字列を貼り付けるための新しいボックスが表示されます。 次の接続文字列を取得するには:
    - Event Hubs や Service Bus。Azure portal で名前空間に移動します。
        - **[設定]** で、 **[共有アクセス ポリシー]** を選択します。
        - 既定の **RootManageSharedAccessKey** を選択するか、新しいポリシーを作成します。
        - プライマリまたはセカンダリの接続文字列をコピーします。

6. ドロップダウン リスト ボックスから、イベント ハブ/キューまたはトピックを選択します。

7. **[Data to export]\(エクスポートするデータ\)** で、エクスポートするデータの種類を **[オン]** に設定して指定します。

8. 継続的データ エクスポートを有効にするには、 **[データのエクスポート]** トグルが **[オン]** になっていることを確認します。 **[保存]** を選択します。

9. 数分後に、選択したエクスポート先にデータが表示されます。


## <a name="data-format"></a>データ形式

テレメトリ、デバイス、およびデバイス テンプレートのデータは、ほぼリアルタイムで、ご利用のイベント ハブや Service Bus のキューまたはトピックにエクスポートされます。 エクスポートされたテレメトリ データには、テレメトリ値自体だけでなく、デバイスが IoT Central に送信したメッセージ全体が含まれています。 エクスポートされたデバイス データには、すべてのデバイスのプロパティとメタデータに対する変更が含まれており、エクスポートされたデバイス テンプレートには、すべてのデバイス テンプレートに対する変更が含まれています。 エクスポートされたデータは "body" プロパティ内にあり、JSON 形式になっています。

> [!NOTE]
> エクスポート先として Service Bus を選択する場合、キューおよびトピックで、**セッションまたは重複データ検出が有効になっていてはいけません**。 これらのオプションのいずれかが有効になっていると、一部のメッセージがキューやトピックに到着しません。

### <a name="telemetry"></a>テレメトリ

新しいメッセージは、IoT Central がデバイスからメッセージを受信した後、迅速にエクスポートされます。 Event Hubs と Service Bus でエクスポートされた各メッセージには、デバイスが "body" プロパティで送信した完全なメッセージが JSON 形式で含まれています。 

> [!NOTE]
> テレメトリを送信するデバイスは、デバイス ID で表されます (以下のセクションを参照)。 デバイスの名前を取得するには、デバイスのデータをエクスポートし、デバイス メッセージの **deviceId** と一致する **connectionDeviceId** を使用して各メッセージを関連付けます。

次の例は、イベント ハブまたは Service Bus のキューまたはトピックで受信されたテレメトリ データに関するメッセージを示しています。

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

### <a name="devices"></a>デバイス

デバイス データを含むメッセージは、数分に 1 回、イベント ハブまたは Service Bus のキューまたはトピックに送信されます。 つまり数分ごとに、以下に関するデータを含むメッセージのバッチが到達します
- 追加された新しいデバイス
- プロパティ値が変更されたデバイス

各メッセージは、最後にエクスポートされたメッセージ以降、デバイスに加えられた 1 つ以上の変更を表しています。 各メッセージで送信される情報には、以下が含まれます。
- `@id` IoT Central でデバイスの
- デバイスの `name`
- `deviceId`Device Provisioning Service[ の](https://aka.ms/iotcentraldocsdps)
- デバイステンプレート情報
- プロパティ値

> [!NOTE]
> 最後のバッチ以降に削除されたデバイスはエクスポートされません。 現在のところ、エクスポートされたメッセージ内に、削除されたデバイスを示すものはありません。
>
> 各デバイスが属するデバイス テンプレートは、デバイス テンプレート ID によって表されます。 デバイス テンプレートの名前を取得するには、デバイス テンプレートのデータも必ずエクスポートしてください。

次の例は、イベント ハブまたは Service Bus のキューまたはトピック内にあるデバイス データに関するメッセージを示しています。


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

### <a name="device-templates"></a>デバイス テンプレート

デバイス テンプレート データを含むメッセージは、数分に 1 回、イベント ハブまたは Service Bus のキューまたはトピックに送信されます。 つまり数分ごとに、以下に関するデータを含むメッセージのバッチが到達します
- 追加またはバージョン管理された新しいデバイス テンプレート
- capabilityModels、cloudProperties、オーバーライド、および初期値が変更されたデバイス テンプレート

各メッセージは、最後にエクスポートされたメッセージ以降、デバイス テンプレートに加えられた 1 つ以上の変更を表しています。 各メッセージで送信される情報には、以下が含まれます。
- デバイス テンプレートの`@id`
- デバイス テンプレートの`name`
- デバイス テンプレートの`version`
- デバイスの `capabilityModel` (その `interfaces` を含む)、テレメトリ、プロパティ、およびコマンドの定義
- `cloudProperties` の定義
- `capabilityModel` に埋め込まれたオーバライドと初期値

> [!NOTE]
> 最後のバッチ以降に削除されたデバイス テンプレートはエクスポートされません。 現在のところ、エクスポートされたメッセージ内に、削除されたデバイス テンプレートを示すものはありません。

次の例は、イベント ハブまたは Service Bus のキューまたはトピック内のデバイス テンプレート メッセージを示しています。

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

## <a name="next-steps"></a>次の手順

これで、Azure Event Hubs と Azure Service Bus にデータをエクスポートする方法を理解できたので、次の手順に進みます。

> [!div class="nextstepaction"]
> [Azure Functions をトリガーする方法](howto-trigger-azure-functions.md)
