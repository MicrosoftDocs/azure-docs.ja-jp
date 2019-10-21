---
title: Azure Blob Storage にデータをエクスポートする | Microsoft Docs
description: Azure IoT Central アプリケーションから Azure Blob Storage にデータをエクスポートする方法
services: iot-central
author: viv-liu
ms.author: viviali
ms.date: 09/26/2019
ms.topic: conceptual
ms.service: iot-central
manager: corywink
ms.openlocfilehash: 9ac650273a53da715b89e3233b30cf50710cfcfd
ms.sourcegitcommit: c2e7595a2966e84dc10afb9a22b74400c4b500ed
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/05/2019
ms.locfileid: "71973268"
---
# <a name="export-your-data-to-azure-blob-storage-preview-features"></a>Azure Blob Storage にデータをエクスポートする (プレビュー機能)

[!INCLUDE [iot-central-pnp-original](../../includes/iot-central-pnp-original-note.md)]

*このトピックでは、管理者に適用されます。*

この記事では、Azure IoT Central の連続データ エクスポート機能を使用して、**Azure Blob Storage アカウント**または**Azure Data Lake Storage Gen2 ストレージ アカウント**に定期的にデータをエクスポートする方法について説明します。 **テレメトリ**、**デバイス**、**デバイス テンプレート**を JSON 形式のファイルにエクスポートできます。 エクスポートしたデータは、Azure Machine Learning のトレーニング モデルや Microsoft Power BI の長期傾向分析などのコールド パス分析に使用できます。

> [!Note]
> 連続データ エクスポートを有効にすると、その時点以降のデータのみが取得されます。 現在は、連続データ エクスポートがオフになっていたときのデータを取得することはできません。 より多くの履歴データを保持するには、連続データ エクスポートを早い段階で有効にしてください。


## <a name="prerequisites"></a>前提条件

- ご利用の IoT Central アプリケーションの管理者であること

## <a name="create-storage-account"></a>ストレージ アカウントの作成
エクスポート先となる既存のストレージがない場合は、次の手順に従います。

1. [Azure portal で新しいストレージ アカウント](https://ms.portal.azure.com/#create/Microsoft.StorageAccount-ARM)を作成します。 新しい [Azure Blob Storage アカウント](https://aka.ms/blobdocscreatestorageaccount)または [Azure Data Lake Storage v2 ストレージ アカウント](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-quickstart-create-account)の作成の詳細を確認できます。

    > [!Note] 
    > ADLS v2 ストレージ アカウントにデータをエクスポートする場合は、 **[アカウントの種類]** に **[BlobStorage]** を選択する必要があります。 

    > [!Note] 
    > 従量課金制 IoT Central アプリケーションとは異なるサブスクリプションのストレージ アカウントにデータをエクスポートできます。 この場合、接続文字列を使用して接続します。

2. ご自分のストレージ アカウントでコンテナーを作成します。 ストレージ アカウントに移動します。 **[Blob service]** で **[BLOB の参照]** を選択します。 上部の **[+ コンテナー]** を選択して、新しいコンテナーを作成します。


## <a name="set-up-continuous-data-export"></a>連続データ エクスポートを設定する

これでデータのエクスポート先となるストレージができました。次の手順に従って、継続的データ エクスポートを設定します。 

1. ご使用の IoT Central アプリケーションにサインインします。

2. 左側のメニューで、 **[データのエクスポート]** を選択します。

    > [!Note]
    > 左側のメニューに [データのエクスポート] が表示されない場合は、アプリの管理者ではありません。 データ エクスポートの設定について、管理者に問い合わせてください。

3. 右上の **[+ 新規]** ボタンを選択します。 エクスポート先として、 **[Azure Blob Storage]** を選択します。 

    > [!NOTE] 
    > アプリごとのエクスポートの最大数は 5 です。 

    ![新しい継続的データ エクスポートの作成](media/howto-export-data-pnp/export-new2.png)

4. ドロップダウン リスト ボックスで、お使いの **Storage Account 名前空間**を選択します。 リスト内の最後のオプション ( **[Enter a connection string]\(接続文字列を入力する\)** ) を選択することもできます。 

    > [!NOTE] 
    > ご使用の **IoT Central アプリと同じサブスクリプション**の Storage Account 名前空間のみが表示されます。 このサブスクリプションとは異なる場所にエクスポートする場合は、 **[Enter a connection string]\(接続文字列を入力する\)** を選択して、手順 5 に進みます。

    > [!NOTE] 
    > 7 日間の試用版アプリの場合、継続的データ エクスポートを構成する唯一の方法は、接続文字列を使用することです。 7 日間の試用版アプリに関連付けられた Azure サブスクリプションがないのはこのためです。

    ![BLOB への新しいエクスポートを作成する](media/howto-export-data-pnp/export-create-blob2.png)

5. (省略可能) **[Enter a connection string]\(接続文字列を入力する\)** を選択すると、接続文字列を貼り付けるための新しいボックスが表示されます。 ストレージ アカウントの接続文字列を取得するには、Azure portal でストレージ アカウントに移動します。
    - **[設定]** で **[アクセス キー]** を選択します。
    - key1 接続文字列または key2 接続文字列のいずれかをコピーします。
 
6. ドロップダウン リスト ボックスでコンテナーを選択します。 コンテナーがない場合は、Azure portal でストレージ アカウントにアクセスします。
    - **[BLOB サービス]** で、 **[BLOB]** を選択します。 **[+ コンテナー]** をクリックし、コンテナーに名前を付けます。 データのパブリック アクセス レベルを選択します (連続データ エクスポートではいずれも機能します)。 詳細については、[Azure Storage のドキュメント](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-portal#create-a-container)を参照してください。

7.  **[Data to export]\(エクスポートするデータ\)** で、エクスポートするデータの種類を **[オン]** に設定して指定します。
   
    > [!NOTE] 
    > データは JSON 形式でエクスポートされます。

8. 連続データ エクスポートを有効にするには、 **[データのエクスポート]** トグルがオンになっていることを確認します。 **[保存]** を選択します。

   ![連続データ エクスポートを構成する](media/howto-export-data-pnp/export-list-blob2.png)

9. 数分後に、データがストレージ アカウントに表示されます。


## <a name="path-structure"></a>パス構造

テレメトリ、デバイス、およびデバイス テンプレートのデータは、1 分間に 1 回、お使いのストレージ アカウントにエクスポートされます。各ファイルには、最後にエクスポートしたファイル以降の変更バッチが含まれています。 エクスポートされたデータは、JSON 形式で 3 つのフォルダーに配置されます。 ストレージ アカウントでの既定のパスは次のとおりです。
- テレメトリ: {container}/{app-id}/telemetry/{YYYY}/{MM}/{dd}/{hh}/{mm}/{filename}
- デバイス: {container}/{app-id}/devices/{YYYY}/{MM}/{dd}/{hh}/{mm}/{filename}
- デバイス テンプレート: {container}/{app-id}/deviceTemplates/{YYYY}/{MM}/{dd}/{hh}/{mm}/{filename}

Azure portal でエクスポートされたファイルを参照するには、ファイルに移動し、 **[BLOB の編集]** タブを選択します。

## <a name="data-format"></a>データ形式
### <a name="telemetry"></a>テレメトリ

エクスポートされたテレメトリ データには、その間に IoT Central がすべてのデバイスから受信した新しいメッセージがすべて含まれています。 エクスポートされるファイルでは、[IoT Hub メッセージ ルーティング](https://docs.microsoft.com/azure/iot-hub/iot-hub-csharp-csharp-process-d2c)によって BLOB ストレージにエクスポートされるメッセージ ファイルと同じ形式が使われます。

> [!NOTE]
> デバイスが `contentType: application/JSON` および `contentEncoding:utf-8` (または `utf-16`、`utf-32`) のメッセージを送信していることを確認します。 例については、[IoT Hub のドキュメント](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-routing-query-syntax#message-routing-query-based-on-message-body)を参照してください。

> [!NOTE]
> テレメトリを送信するデバイスは、デバイス ID で表されます (以下のセクションを参照)。 デバイスの名前を取得するには、デバイスのスナップショットをエクスポートします。 デバイスレコードの**デバイス ID** と一致する **connectionDeviceId** を使っデバイスて、各メッセージ レコードを関連付けます。

次の例は、JSON 形式のレコードを示しています。

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

### <a name="devices"></a>デバイス

連続データ エクスポートを初めて有効にしたときに、すべてのデバイスを含む単一のスナップショットがエクスポートされます。 各デバイスは次のとおりです。
- `@id` IoT Central でデバイスの
- デバイスの `name`
- `deviceId`Device Provisioning Service[ の](https://aka.ms/iotcentraldocsdps)
- デバイステンプレート情報
- プロパティ値

1 分に 1 回、新しいスナップショットが書き込まれます。 スナップショットには次の値が含まれます。

- 最後のスナップショット以降に追加された新しいデバイス。
- 最後のスナップショット以降にプロパティの値が変更されたデバイス。

> [!NOTE]
> 最後のスナップショット以降に削除されたデバイスは、エクスポートされません。 現時点では、スナップショットに削除されたデバイスのインジケーターはありません。
>
> 各デバイスが属するデバイス テンプレートは `instanceOf` フィールドで表されます。 デバイス テンプレートの名前を取得するには、デバイス テンプレートのスナップショットをエクスポートします。

エクスポートされたファイルには、1 つのレコードにつき 1 行が含まれます。 次の例は、JSON 形式のレコードを示しています。

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

### <a name="device-templates"></a>デバイス テンプレート

連続データ エクスポートを初めて有効にしたときに、すべてのデバイス テンプレートを含む単一のスナップショットがエクスポートされます。 各デバイスのテンプレートは次のとおりです。
- デバイス テンプレートの`@id`
- デバイス テンプレートの`name`
- デバイス テンプレートの`version`
- デバイスの `capabilityModel` (その `interfaces` を含む)、テレメトリ、プロパティ、およびコマンドの定義
- `cloudProperties` の定義
- `capabilityModel` に埋め込まれたオーバライドと初期値

1 分に 1 回、新しいスナップショットが書き込まれます。 スナップショットには次の値が含まれます。

- 最後のスナップショット以降に追加された新しいデバイス テンプレート。 これには、新しいバージョンのデバイス テンプレートが含まれます。
- 前回のスナップショット以降にオーバーライド、初期値、およびクラウド プロパティの定義が変更されたデバイス テンプレート。

> [!NOTE]
> 最後のスナップショット以降に削除されたデバイス テンプレートは、エクスポートされません。 現時点では、スナップショットに削除されたデバイス テンプレートのインジケーターはありません。

エクスポートされたファイルには、1 つのレコードにつき 1 行が含まれます。 次の例は、JSON 形式のレコードを示しています。

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

データをエクスポートする方法がわかったら、次の手順に進みます。

> [!div class="nextstepaction"]
> [IoT Central デバイス ブリッジを使用して他の IoT クラウドを接続する方法](howto-build-iotc-device-bridge.md)
