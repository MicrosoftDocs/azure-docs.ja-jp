---
title: IoT プラグ アンド プレイのデジタル ツインを管理する方法
description: デジタル ツイン API を使用して IoT プラグ アンド プレイ デバイスを管理する方法
author: prashmo
ms.author: prashmo
ms.date: 07/20/2020
ms.topic: how-to
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: ddb8027c145f6a38bfcd953be66dae2943a20c3a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "97654610"
---
# <a name="manage-iot-plug-and-play-digital-twins"></a>IoT プラグ アンド プレイのデジタル ツインを管理する

IoT プラグ アンド プレイでは、デジタル ツインを管理するための **デジタル ツインの取得** 操作および **デジタル ツインの更新** 操作がサポートされています。 [REST API](/rest/api/iothub/service/digitaltwin)、または[サービス SDK](libraries-sdks.md)のいずれかを使用できます。

この記事の執筆時点では、デジタル ツイン API のバージョンは `2020-09-30` です。

## <a name="update-a-digital-twin"></a>デジタル ツインを更新する

IoT プラグ アンド プレイ デバイスは、[Digital Twins Definition Language v2 (DTDL)](https://github.com/Azure/opendigitaltwins-dtdl) によって記述されたモデルを実装します。 ソリューション開発者は、**デジタル ツイン API の更新** を使用して、コンポーネントの状態と デジタル ツイン のプロパティを更新できます。

この記事の例として使用されている IoT プラグ アンド プレイ デバイスには、[Thermostat](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/samples/Thermostat.json) コンポーネントを持つ [Temperature Controller モデル](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/samples/TemperatureController.json)が実装されています。

次のスニペットは、JSON オブジェクトとしてフォーマットされた、**デジタル ツインの取得** 要求に対する応答を示しています。 デジタル ツインの形式の詳細については、「[IoT プラグ アンド プレイのデジタル ツインを理解する](./concepts-digital-twin.md#digital-twin-example)」を参照してください。

```json
{
    "$dtId": "sample-device",
    "serialNumber": "alwinexlepaho8329",
    "thermostat1": {
        "maxTempSinceLastReboot": 25.3,
        "targetTemperature": 20.4,
        "$metadata": {
            "targetTemperature": {
                "desiredValue": 20.4,
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

デジタル ツインでは、[JSON Patch](http://jsonpatch.com/) を使用して、コンポーネント全体またはプロパティを更新できます。

たとえば、次のように `targetTemperature` プロパティを更新できます。

```json
[
    {
        "op": "add",
        "path": "/thermostat1/targetTemperature",
        "value": 21.4
    }
]
```

前の更新では、次のスニペットに示すように、対応するコンポーネントレベルの `$metadata` 内でプロパティの目的の値が設定されます。 IoT Hub によって、必要なバージョンのプロパティが更新されます。

```json
"thermostat1": {
    "targetTemperature": 20.4,
    "$metadata": {
        "targetTemperature": {
            "desiredValue": 21.4,
            "desiredVersion": 5,
            "ackVersion": 4,
            "ackCode": 200,
            "ackDescription": "Successfully executed patch",
            "lastUpdateTime": "2020-07-17T06:11:04.9309159Z"
        }
    }
}
```

### <a name="add-replace-or-remove-a-component"></a>コンポーネントを追加、置換、または削除する

コンポーネント レベルの操作では、値内に空のオブジェクト `$metadata` マーカーが必要となります。

コンポーネントの追加または置換操作は、指定されたすべてのプロパティの目的の値を設定します。 また、更新に指定されていない書き込み可能なプロパティについても、目的の値がクリアされます。

コンポーネントを削除すると、存在するすべての書き込み可能なプロパティの目的の値がクリアされます。 デバイスは最終的にこの削除を同期し、個々のプロパティの報告を停止します。 その後、コンポーネントがデジタル ツインから削除されます。

次の JSON Patch の例は、コンポーネントを追加、置換、または削除する方法を示しています。

```json
[
    {
        "op": "add",
        "path": "/thermostat1",
        "value": {
            "targetTemperature": 21.4,
            "anotherWritableProperty": 42,
            "$metadata": {}
        }
    },
    {
        "op": "replace",
        "path": "/thermostat1",
        "value": {
            "targetTemperature": 21.4,
            "$metadata": {}
        }
    },
    {
        "op": "remove",
        "path": "/thermostat2"
    }
]
```

### <a name="add-replace-or-remove-a-property"></a>プロパティを追加、置換、または削除する

追加または置換操作では、プロパティの目的の値を設定します。 デバイスは、状態を同期し、`ack` コード、バージョン、および説明と共に値の更新を報告できます。

プロパティを削除すると、プロパティの目的の値がクリアされます (設定されている場合)。 その後、デバイスによってこのプロパティの報告を停止でき、それがコンポーネントから削除されます。 このプロパティがコンポーネント内の最後のプロパティである場合は、コンポーネントも削除されます。

次の JSON Patch の例は、コンポーネント内のプロパティを追加、置換、または削除する方法を示しています。

```json
[
    {
        "op": "add",
        "path": "/thermostat1/targetTemperature",
        "value": 21.4
    },
    {
        "op": "replace",
        "path": "/thermostat1/anotherWritableProperty",
        "value": 42
    },
    {
        "op": "remove",
        "path": "/thermostat2/targetTemperature",
    }
]
```

### <a name="rules-for-setting-the-desired-value-of-a-digital-twin-property"></a>デジタル ツイン プロパティの目的の値を設定するための規則

**名前**

コンポーネントまたはプロパティの名前は、有効な DTDL v2 名である必要があります。

使用できる文字は、a-z、A-Z、0-9 (最初の文字にはできません)、およびアンダースコア (最初の文字または最後の文字にはできません) です。

名前は 1 ～ 64 文字の長さにすることができます。

**プロパティ値**

値は、有効な [DTDL v2 プロパティ](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md#property)である必要があります。

すべてのプリミティブ型がサポートされています。 複合型内では、列挙型、マップ、およびオブジェクトがサポートされています。 詳細については、「[DTDL v2 スキーマ](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md#schemas)」を参照してください。

プロパティでは、配列または配列を含む複雑なスキーマはサポートされません。

複合オブジェクトでサポートされる最大深度は 5 レベルです。

複合オブジェクト内のすべてのフィールド名は、DTDL v2 の有効な名前である必要があります。

すべてのマップ キーは、DTDL v2 の有効な名前である必要があります。

## <a name="troubleshoot-update-digital-twin-api-errors"></a>デジタル ツイン API の更新エラーに関するトラブルシューティング

デジタル ツイン API によって次の一般的なエラー メッセージがスローされます。

`ErrorCode:ArgumentInvalid;'{propertyName}' exists within the device twin and is not digital twin conformant property. Please refer to aka.ms/dtpatch to update this to be conformant.`

このエラーが発生した場合は、更新パッチが「[デジタル ツイン プロパティの目的の値を設定するための規則](#rules-for-setting-the-desired-value-of-a-digital-twin-property)」に従っていることを確認してください

コンポーネントを更新するときに、[空のオブジェクト $metadata マーカー](#add-replace-or-remove-a-component)が設定されていることを確認してください。

デバイスの報告される値が「[IoT プラグアンドプレイ規則](./concepts-convention.md#writable-properties)」に準拠していない場合、更新が失敗することがあります。

## <a name="next-steps"></a>次のステップ

ここまでで、デジタル ツインについて学習しました。その他のリソースを次に示します。

- [ソリューションからデバイスを操作する](quickstart-service.md)
- [IoT Digital Twin REST API](/rest/api/iothub/service/digitaltwin)
- [Azure IoT エクスプローラー](howto-use-iot-explorer.md)
