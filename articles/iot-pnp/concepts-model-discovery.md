---
title: IoT プラグ アンド プレイ モデルをソリューションに使用する | Microsoft Docs
description: ソリューション ビルダーが IoT プラグ アンド プレイ モデルを IoT ソリューションにどのように使用できるかについて説明します。
author: arunmannengal
ms.author: arunmann
ms.date: 07/23/2020
ms.topic: conceptual
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: 7e0d93d3fd179d672b49967c575bbfb9c0fc5de0
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102213693"
---
# <a name="use-iot-plug-and-play-models-in-an-iot-solution"></a>Iot ソリューションで IoT プラグ アンド プレイを使用する

この記事では、IoT ソリューションにおいて、IoT プラグ アンド プレイ デバイスのモデル ID を識別し、次にそのモデル定義を取得する方法について説明します。

IoT ソリューションには、大きく分けて次の 2 つのカテゴリがあります。

- "*専用のソリューション*" は、ソリューションに接続する IoT プラグ アンド プレイ デバイスの既知のモデル セットで機能します。 ソリューションを開発するときに、これらのモデルを使用します。

- "*モデル駆動型ソリューション*" は、任意の IoT プラグ アンド プレイ デバイスのモデルで機能します。 モデル駆動型ソリューションの構築はより複雑ですが、今後追加される可能性がある任意のデバイスでソリューションが機能するという利点があります。 モデル駆動型 IoT ソリューションでは、モデルを取得し、それを使用することで、デバイスが実装するテレメトリ、プロパティ、およびコマンドが決定されます。

IoT プラグ アンド プレイ モデルを使用するには、IoT ソリューションで次のことを行います。

1. ソリューションに接続されている IoT プラグ アンド プレイ デバイス、モジュール、または IoT Edge モジュールによって実装されるモデルのモデル ID を識別します。

1. そのモデル ID を使用して、接続されているデバイスのモデル定義をモデル リポジトリまたはカスタム ストアから取得します。

## <a name="identify-model-id"></a>モデル ID を識別する

IoT プラグ アンド プレイ デバイスが IoT Hub に接続されると、それによって実装されているモデルのモデル ID が IoT Hub に登録されます。

IoT Hub は、デバイス接続フローの一環としてデバイス モデル ID を使用してソリューションに通知します。

ソリューションでは、次の 3 つの方法のいずれかを使用して、IoT プラグ アンド プレイ デバイスのモデル ID を取得できます。

### <a name="get-device-twin-api"></a>Get Device Twin API

このソリューションでは、[Get Device Twin](/java/api/com.microsoft.azure.sdk.iot.device.deviceclient.getdevicetwin) API を使用して、IoT プラグ アンド プレイ デバイスのモデル ID を取得することができます。

> [!TIP]
> モジュールと IoT Edge モジュールの場合は、[ModuleClient.getTwin](/java/api/com.microsoft.azure.sdk.iot.device.moduleclient.gettwin) を使用します。

次のデバイス ツイン応答スニペットでは、IoT プラグ アンド プレイ デバイスのモデル ID が `modelId` に含まれています。

```json
{
    "deviceId": "sample-device",
    "etag": "AAAAAAAAAAc=",
    "deviceEtag": "NTk0ODUyODgx",
    "status": "enabled",
    "statusUpdateTime": "0001-01-01T00:00:00Z",
    "connectionState": "Disconnected",
    "lastActivityTime": "2020-07-17T06:12:26.8402249Z",
    "cloudToDeviceMessageCount": 0,
    "authenticationType": "sas",
    "x509Thumbprint": {
        "primaryThumbprint": null,
        "secondaryThumbprint": null
    },
    "modelId": "dtmi:com:example:TemperatureController;1",
    "version": 15,
    "properties": {...}
    }
}
```

### <a name="get-digital-twin-api"></a>Get Digital Twin API

ソリューションでは、[Get Digital Twin](/rest/api/iothub/service/digitaltwin/getdigitaltwin) API を使用して、IoT プラグ アンド プレイ デバイスによって実装されたモデルのモデル ID を取得できます。

次のデジタル ツイン応答スニペットでは、IoT プラグ アンド プレイ デバイスのモデル ID が `$metadata.$model` に含まれています。

```json
{
    "$dtId": "sample-device",
    "$metadata": {
        "$model": "dtmi:com:example:TemperatureController;1",
        "serialNumber": {
            "lastUpdateTime": "2020-07-17T06:10:31.9609233Z"
        }
    }
}
```

### <a name="digital-twin-change-event-notification"></a>デジタル ツインの変更イベント通知

デバイスの接続により、[Digital Twin 変更イベント](concepts-digital-twin.md#digital-twin-change-events)通知が生成されます。 ソリューションは、このイベント通知をサブスクライブする必要があります。 デジタル ツイン イベントのルーティングを有効にする方法については、「[IoT Hub メッセージ ルーティングを使用して device-to-cloud メッセージを別のエンドポイントに送信する](../iot-hub/iot-hub-devguide-messages-d2c.md#non-telemetry-events)」を参照してください。

このソリューションでは、次のスニペットに示すイベントを使用して、接続している IoT プラグ アンド プレイ デバイスについて把握し、そのモデル ID を取得することができます。

```json
iothub-connection-device-id:sample-device
iothub-enqueuedtime:7/22/2020 8:02:27 PM
iothub-message-source:digitalTwinChangeEvents
correlation-id:100f322dc2c5
content-type:application/json-patch+json
content-encoding:utf-8
[
  {
    "op": "replace",
    "path": "/$metadata/$model",
    "value": "dtmi:com:example:TemperatureController;1"
  }
]
```

## <a name="retrieve-a-model-definition"></a>モデル定義を取得する

ソリューションでは、上記で識別されたモデル ID を使用して、対応するモデル定義が取得されます。

ソリューションでモデル定義を取得するには、次のいずれかのオプションを使用します。

### <a name="model-repository"></a>モデル リポジトリ

ソリューションでは、[モデル リポジトリ](concepts-model-repository.md)を使用して、モデルが取得されます。 デバイス ビルダーまたはソリューション ビルダーのいずれも、ソリューションがモデルを取得できるように、モデルを事前にリポジトリにアップロードしておく必要があります。

新しいデバイス接続用のモデル ID を識別したら、次の手順を行います。

1. モデル リポジトリからのモデル ID を使用して、モデル定義を取得します。 詳細については、「[デバイス モデル リポジトリ](concepts-model-repository.md)」を参照してください。

1. 接続されたデバイスのモデル定義を使用すれば、デバイスの機能を列挙できます。

1. デバイスの列挙機能を使用すれば、ユーザーが[デバイスと対話する](quickstart-service.md)のを許可することができます。

### <a name="custom-store"></a>カスタム ストア

ソリューションでは、これらのモデル定義をローカル ファイル システムまたはパブリック ファイル ストアに格納することも、カスタム実装を使用することもできます。

新しいデバイス接続用のモデル ID を識別したら、次の手順を行います。

1. ご利用のカスタム ストアからのモデル ID を使用して、モデル定義を取得します。

1. 接続されたデバイスのモデル定義を使用すれば、デバイスの機能を列挙できます。 

1. デバイスの列挙機能を使用すれば、ユーザーが[デバイスと対話する](quickstart-service.md)のを許可することができます。  

## <a name="next-steps"></a>次のステップ

IoT プラグ アンド プレイ モデルを IoT ソリューションに統合する方法を学習したので、次に推奨されるいくつかのステップを以下に示します。

- [ソリューションからのデバイスの操作](quickstart-service.md)
- [IoT Digital Twin REST API](/rest/api/iothub/service/digitaltwin)
- [Azure IoT エクスプローラー](howto-use-iot-explorer.md)
