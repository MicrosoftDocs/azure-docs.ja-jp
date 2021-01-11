---
title: IoT プラグ アンド プレイのデジタル ツインを理解する
description: IoT プラグ アンド プレイ プレビューでデジタル ツインがどのように使用されるかを理解する
author: prashmo
ms.author: prashmo
ms.date: 07/17/2020
ms.topic: conceptual
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: 1908abfb3d0ea20c69a68344d54076c6760e9e63
ms.sourcegitcommit: 46f8457ccb224eb000799ec81ed5b3ea93a6f06f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/28/2020
ms.locfileid: "87352044"
---
# <a name="understand-iot-plug-and-play-digital-twins"></a>IoT プラグ アンド プレイのデジタル ツインを理解する

IoT プラグ アンド プレイ デバイスは、[Digital Twins Definition Language (DTDL)](https://github.com/Azure/opendigitaltwins-dtdl) スキーマによって記述されたモデルを実装します。 モデルは、特定のデバイスが持つことができるコンポーネント、プロパティ、コマンド、およびテレメトリ メッセージのセットを表します。 デバイス ツインとデジタル ツインは、IoT プラグ アンド プレイ デバイスが IoT ハブに初めて接続した時に初期化されます。

IoT プラグ アンド プレイでは、DTDL バージョン 2 が使用されています。 このバージョンの詳細については、GitHub の[Digital Twins Definition Language (DTDL) - バージョン 2](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md) の仕様を参照してください。

DTDL は、IoT プラグ アンド プレイ専用ではありません。 [Azure Digital Twins](../digital-twins/overview.md) などの他の IoT サービスでは、これは建物やエネルギー ネットワークなどの環境全体を表すために使用されています。 詳細については、「[Azure Digital Twins のツイン モデルについて](../digital-twins/concepts-models.md)」を参照してください。

この記事では、デバイス ツインの *desired* と *reported* セクションで、コンポーネントとプロパティがどのように表現されるかについて説明します。 また、これらの概念が対応するデジタル ツインにどのようにマップしているかについても説明します。

この記事に登場する IoT プラグ アンド プレイ デバイスには、[Thermostat](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/samples/Thermostat.json) コンポーネントを持つ [Temperature Controller モデル](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/samples/TemperatureController.json)が実装されています。

## <a name="device-twins-and-digital-twins"></a>デバイス ツインとデジタル ツイン

デバイス ツインは、デバイスに関する情報 (メタデータ、構成、状態など) を格納する JSON ドキュメントです。 詳細については、「[IoT Hub のデバイス ツインの理解と使用](../iot-hub/iot-hub-devguide-device-twins.md)」を参照してください。 デバイス ビルダーとソリューション ビルダーは、どちらも引き続き同じ Device Twin API と SDK のセットを使用して、IoT プラグ アンド プレイ規則を用いてデバイスとソリューションを実装できます。

Digital Twin API は、コンポーネント、プロパティ、コマンドなどの、Digital Twins Definition Language (DTDL) の高レベル コンストラクトで機能します。 Digital Twin API を使用すると、ソリューション ビルダーで IoT プラグ アンド プレイ ソリューションを簡単に作成できます。

デバイス ツインでは、書き込み可能なプロパティの状態は、desired セクションと reported セクションに分割されます。 すべての読み取り専用プロパティは、reported セクション内で使用できます。

デジタル ツインには、プロパティの現在の状態と目的の状態を示す統合ビューがあります。 特定のプロパティの同期状態は、対応するルートレベルまたはコンポーネント `$metadata` セクションに格納されます。

### <a name="digital-twin-json-format"></a>デジタル ツインの JSON 形式

JSON オブジェクトとして表された場合、デジタル ツインには次のフィールドが含まれます。

| フィールド名 | 説明 |
| --- | --- |
| `$dtId` | デバイス デジタル ツインの ID を表すユーザー指定の文字列 |
| `{propertyName}` | JSON のプロパティの値 |
| `$metadata.$model` | [省略可能] このデジタル ツインを特徴付けるモデル インターフェイスの ID |
| `$metadata.{propertyName}.desiredValue` | [書き込み可能なプロパティ専用] 指定されたプロパティの目的の値 |
| `$metadata.{propertyName}.desiredVersion` | [書き込み可能なプロパティ専用] IoT Hub で管理される目的の値のバージョン|
| `$metadata.{propertyName}.ackVersion` | [必須、書き込み可能なプロパティ専用] デジタル ツインを実装しているデバイスによって確認されたバージョン。これは、目的のバージョン以上である必要があります |
| `$metadata.{propertyName}.ackCode` | [必須、書き込み可能なプロパティ専用] デジタル ツインを実装するデバイス アプリによって返される `ack` コード |
| `$metadata.{propertyName}.ackDescription` | [省略可能、書き込み可能なプロパティ専用] デジタル ツインを実装するデバイス アプリによって返される `ack` の説明 |
| `$metadata.{propertyName}.lastUpdateTime` | IoT Hub によって、デバイスによるプロパティの最後の更新のタイムスタンプが保持されます。 タイムスタンプは UTC で、ISO8601 形式 (YYYY-MM-DDTHH:MM:SS.mmmZ) でエンコードされています |
| `{componentName}` | コンポーネントのプロパティ値とメタデータを含む JSON オブジェクト。ルート オブジェクト同様。 |
| `{componentName}.{propertyName}` | JSON でのコンポーネントのプロパティ値 |
| `{componentName}.$metadata` | コンポーネントのメタデータ情報。ルートレベルの `$metadata` と似たものです。 |

#### <a name="device-twin-sample"></a>デバイス ツインのサンプル

次のスニペットは、JSON オブジェクトとして書式設定された IoT プラグ アンド プレイ デバイス ツインを示しています。

```json
{
    "deviceId": "sample-device",
    "modelId": "dtmi:com:example:TemperatureController;1",
    "version": 15,
    "properties": {
        "desired": {
            "thermostat1": {
                "__t": "c",
                "targetTemperature": 21.8
            },
            "$metadata": {...},
            "$version": 4
        },
        "reported": {
            "serialNumber": "alwinexlepaho8329",
            "thermostat1": {
                "maxTempSinceLastReboot": 25.3,
                "__t": "c",
                "targetTemperature": {
                    "value": 21.8,
                    "ac": 200,
                    "ad": "Successfully executed patch",
                }
            },
            "$metadata": {...},
            "$version": 11
        }
    }
}
```

#### <a name="digital-twin-sample"></a>デジタル ツインのサンプル

次のスニペットは、JSON オブジェクトとして書式設定されたデジタル ツインを示しています。

```json
{
    "$dtId": "sample-device",
    "serialNumber": "alwinexlepaho8329",
    "thermostat1": {
        "maxTempSinceLastReboot": 25.3,
        "targetTemperature": 21.8,
        "$metadata": {
            "targetTemperature": {
                "desiredValue": 21.8,
                "desiredVersion": 4,
                "ackVersion": 4,
                "ackCode": 200,
                "ackDescription": "Successfully executed patch",
                "lastUpdateTime": "2020-07-17T06:11:04.9309159Z"
            },
            "maxTempSinceLastReboot": {
                "lastUpdateTime": "2020-07-17T06:10:31.9609233Z"
            }
        }
    },
    "$metadata": {
        "$model": "dtmi:com:example:TemperatureController;1",
        "serialNumber": {
            "lastUpdateTime": "2020-07-17T06:10:31.9609233Z"
        }
    }
}
```

### <a name="properties"></a>Properties

プロパティは、エンティティの状態を表すデータ フィールドです (多くのオブジェクト指向プログラミング言語のプロパティと同様)。

#### <a name="read-only-property"></a>読み取り専用プロパティ

スキーマ:

```json
{
    "@type": "Property",
    "name": "serialNumber",
    "displayName": "Serial Number",
    "description": "Serial number of the device.",
    "schema": "string"
}
```

この例では、`alwinexlepaho8329` は、デバイスによって報告された `serialNumber` 読み取り専用プロパティの現在の値です。
次のスニペットは、`serialNumber` プロパティの JSON 表現を横に並べて示しています。

:::row:::
   :::column span="":::
      **デバイス ツイン**

```json
"properties": {
    "reported": {
        "serialNumber": "alwinexlepaho8329"
    }
}
```

   :::column-end:::
   :::column span="":::
      **デジタル ツイン**

```json
"serialNumber": "alwinexlepaho8329"
```

   :::column-end:::
:::row-end:::

#### <a name="writable-property"></a>書き込み可能なプロパティ

デバイスに、ルートレベルの以下の書き込み可能なプロパティがあるとします。

```json
{
    "@type": "Property",
    "name": "fanSpeed",
    "displayName": "Fan Speed",
    "writable": true,
    "schema": "double"
}
```

:::row:::
   :::column span="":::
      **デバイス ツイン**

```json
{
    "properties": {
        "desired": {
            "fanSpeed": 2.0,
        },
        "reported": {
            "fanSpeed": {
                "value": 3.0,
                "ac": 200,
                "av": 1,
                "ad": "Successfully executed patch version 1"
            }
        }
    },
}
```

   :::column-end:::
   :::column span="":::
      **デジタル ツイン**

```json
{
    "fanSpeed": 3.0,
    "$metadata": {
        "fanSpeed": {
            "desiredValue": 2.0,
            "desiredVersion": 2,
            "ackVersion": 1,
            "ackCode": 200,
            "ackDescription": "Successfully executed patch version 1",
            "lastUpdateTime": "2020-07-17T06:10:31.9609233Z"
        }
    }
}
```

   :::column-end:::
:::row-end:::

この例では、`3.0` は、デバイスによって報告された `fanSpeed` プロパティの現在の値です。 `2.0` は、ソリューションによって設定された目的の値です。 ルートレベル プロパティの目的の値と同期状態は、デジタル ツインのルートレベルの `$metadata` 内で設定されます。 デバイスがオンラインになると、この更新が適用され、更新された値が報告されます。

### <a name="components"></a>コンポーネント

コンポーネントを使用すると、他のインターフェイスのアセンブリとしてモデル インターフェイスを作成できます。
モデルとして定義されている [Thermostat](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/samples/Thermostat.json) インターフェイスがあるとします。
このインターフェイスを、[Temperature Controller モデル](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/samples/TemperatureController.json)を定義するときに、コンポーネント thermostat1 (および別のコンポーネント thermostat2) として組み込むことができるようになりました。

デバイス ツインでは、コンポーネントは `{ "__t": "c"}` マーカーによって識別されます。 デジタル ツインでは、`$metadata` の存在によってコンポーネントがマークされます。

この例では、`thermostat1` は 2 つのプロパティを持つコンポーネントです。

- `maxTempSinceLastReboot` は読み取り専用プロパティです。
- `targetTemperature` は、デバイスによって正常に同期された書き込み可能なプロパティです。 これらのプロパティの目的の値と同期状態は、コンポーネントの `$metadata` にあります。

次のスニペットは、`thermostat1` コンポーネントの JSON 表現を横に並べて示しています。

:::row:::
   :::column span="":::
      **デバイス ツイン**

```json
"properties": {
    "desired": {
        "thermostat1": {
            "__t": "c",
            "targetTemperature": 21.8
        },
        "$metadata": {
        },
        "$version": 4
    },
    "reported": {
        "thermostat1": {
            "maxTempSinceLastReboot": 25.3,
            "__t": "c",
            "targetTemperature": {
                "value": 21.8,
                "ac": 200,
                "ad": "Successfully executed patch",
                "av": 4
            }
        },
        "$metadata": {
        },
        "$version": 11
    }
}
```

   :::column-end:::
   :::column span="":::
      **デジタル ツイン**

```json
"thermostat1": {
    "maxTempSinceLastReboot": 25.3,
    "targetTemperature": 21.8,
    "$metadata": {
        "targetTemperature": {
            "desiredValue": 21.8,
            "desiredVersion": 4,
            "ackVersion": 4,
            "ackCode": 200,
            "ackDescription": "Successfully executed patch",
            "lastUpdateTime": "2020-07-17T06:11:04.9309159Z"
        },
        "maxTempSinceLastReboot": {
            "lastUpdateTime": "2020-07-17T06:10:31.9609233Z"
        }
    }
}
```

   :::column-end:::
:::row-end:::

## <a name="digital-twin-apis"></a>Digital Twin API

Azure Digital Twins には、デバイスのデジタル ツインを管理するための **Get Digital Twin**、**Update Digital Twin**、**Invoke Component Command**、および **Invoke Command** が用意されています。 [REST API](https://docs.microsoft.com/rest/api/iothub/service/digitaltwin) は直接使用することも、[サービス SDK](../iot-pnp/libraries-sdks.md) を通じて使用することもできます。

## <a name="digital-twin-change-events"></a>デジタル ツインの変更イベント

デジタル ツインの変更イベントを有効にすると、コンポーネントまたはプロパティの現在の値または目的の値が変更されるたびにイベントがトリガーされます。 デジタル ツインの変更イベントは [JSON Patch](http://jsonpatch.com/) 形式で生成されます。 ツイン変更イベントが有効になっている場合、対応するイベントがデバイス ツイン形式で生成されます。

デバイスおよびデジタル ツイン イベントのルーティングを有効にする方法については、「[IoT Hub メッセージ ルーティングを使用して device-to-cloud メッセージを別のエンドポイントに送信する](../iot-hub/iot-hub-devguide-messages-d2c.md#non-telemetry-events)」を参照してください。 メッセージ形式の詳細については、「[IoT Hub メッセージを作成し、読み取る](../iot-hub/iot-hub-devguide-messages-construct.md)」を参照してください。

たとえば、ソリューションによって `targetTemperature` が設定されると、次のようなデジタル ツイン変更イベントがトリガーされます。

```json
iothub-connection-device-id:sample-device
iothub-enqueuedtime:7/17/2020 6:11:04 AM
iothub-message-source:digitalTwinChangeEvents
correlation-id:275d463fa034
content-type:application/json-patch+json
content-encoding:utf-8
[
  {
    "op": "add",
    "path": "/thermostat1/$metadata/targetTemperature",
    "value": {
      "desiredValue": 21.8,
      "desiredVersion": 4
    }
  }
]
```

次のデジタル ツイン変更イベントは、上記の目的の変更が適用されたことがデバイスによって報告されたときにトリガーされます。

```json
iothub-connection-device-id:sample-device
iothub-enqueuedtime:7/17/2020 6:11:05 AM
iothub-message-source:digitalTwinChangeEvents
correlation-id:275d464a2c80
content-type:application/json-patch+json
content-encoding:utf-8
[
  {
    "op": "add",
    "path": "/thermostat1/$metadata/targetTemperature/ackCode",
    "value": 200
  },
  {
    "op": "add",
    "path": "/thermostat1/$metadata/targetTemperature/ackDescription",
    "value": "Successfully executed patch"
  },
  {
    "op": "add",
    "path": "/thermostat1/$metadata/targetTemperature/ackVersion",
    "value": 4
  },
  {
    "op": "add",
    "path": "/thermostat1/$metadata/targetTemperature/lastUpdateTime",
    "value": "2020-07-17T06:11:04.9309159Z"
  },
  {
    "op": "add",
    "path": "/thermostat1/targetTemperature",
    "value": 21.8
  }
]
```

## <a name="next-steps"></a>次のステップ

ここでは、デジタル ツインについて説明しました。その他のリソースを次に示します。

- [IoT プラグ アンド プレイの Digital Twin API の使用方法](howto-manage-digital-twin.md)
- [ソリューションからデバイスを操作する](quickstart-service-node.md)
- [IoT Digital Twin REST API](https://docs.microsoft.com/rest/api/iothub/service/digitaltwin)
- [Azure IoT エクスプローラー](howto-use-iot-explorer.md)
