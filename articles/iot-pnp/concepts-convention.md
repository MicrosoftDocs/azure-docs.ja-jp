---
title: IoT プラグ アンド プレイ規則 | Microsoft Docs
description: IoT プラグ アンド プレイからデバイスに対して、テレメトリとプロパティを送信し、コマンドとプロパティの更新を処理するときに、使用することが想定される規則の説明。
author: rido-min
ms.author: rmpablos
ms.date: 07/10/2020
ms.topic: conceptual
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: ef29be53e776c4c185ac8430b3340c53ca85d855
ms.sourcegitcommit: b33c9ad17598d7e4d66fe11d511daa78b4b8b330
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/25/2020
ms.locfileid: "88856051"
---
# <a name="iot-plug-and-play-conventions"></a>IoT プラグ アンド プレイ規則

IoT プラグ アンド プレイ プレビュー デバイスは、IoT ハブとメッセージを交換するとき、一連の規則に従う必要があります。 IoT プラグ アンド プレイ プレビュー デバイスは、MQTT プロトコルを使用して IoT Hub と通信します。

IoT プラグ アンド プレイ デバイスが実装するテレメトリ、プロパティ、コマンドを、[Digital Twins Definition Language v2 (DTDL)](https://github.com/Azure/opendigitaltwins-dtdl) _モデル_で記述します。 この記事では、次の 2 種類のモデルを参照します。

- **コンポーネントなし** - コンポーネントのないモデル。 このモデルでは、テレメトリ、プロパティ、およびコマンドを、メイン インターフェイスのコンテンツ セクションで最上位レベルのプロパティとして宣言します。
- **複数のコンポーネント** - 2 つ以上のインターフェイスで構成されるモデル。 テレメトリ、プロパティ、およびコマンドを含むメイン インターフェイス。 追加のテレメトリ、プロパティ、およびコマンドを備えたコンポーネントとして宣言された 1 つ以上のインターフェイス。

詳細については、「[モデル内の IoT プラグ アンド プレイ コンポーネント](concepts-components.md)」を参照してください。

## <a name="identify-the-model"></a>モデルを識別する

実装するモデルをアナウンスするために、IoT プラグ アンド プレイ デバイスでは、`USERNAME` フィールドに `model-id` を追加することにより、MQTT 接続パケットにモデル ID を含めます。

デバイスに実装されるモデルを識別するには、サービスを使用して以下でモデル ID を取得できます。

- デバイス ツインの `modelId` フィールド。
- デジタル ツインの `$metadata.$model` フィールド。
- デジタル ツインの変更通知

## <a name="telemetry"></a>テレメトリ

コンポーネントのないデバイスから送信されたテレメトリは、追加のメタデータが必要ありません。 システムによって `dt-dataschema` プロパティが追加されます。

複数のコンポーネント デバイスからテレメトリが送信された場合、メッセージ プロパティとして `$.sub` が追加されます。 システムによって、`dt-subject` および `dt-dataschema` プロパティが追加されます。

## <a name="read-only-properties"></a>読み取り専用プロパティ

### <a name="sample-no-component-read-only-property"></a>コンポーネントなしの読み取り専用プロパティのサンプル

デバイスからは、DTDL v2 の規則に従う有効な JSON を送信できます。

:::row:::
   :::column span="":::
      **DTDL**

      ```json
      "@context": "dtmi:dtdl:context;2",
      "@id": "dtmi:example: Thermostat;1",
      "@type": "Interface",
      "contents": [
        {
          "@type": "Property",
          "name": "temperature",
          "schema": "double"
        }
      ]
      ```
   :::column-end:::
   :::column span="":::
      **サンプル ペイロード**

      ```json
      "reported" :
      {
        "temperature" : 21.3
      }
      ```
   :::column-end:::
:::row-end:::

### <a name="sample-multiple-components-read-only-property"></a>複数のコンポーネントの読み取り専用プロパティのサンプル

この要素からコンポーネントを参照していることを示すために、デバイでスは `{"__t": "c"}` マーカーを追加する必要があります。

:::row:::
   :::column span="":::
      **DTDL**

      ```json
      "@context": "dtmi:dtdl:context;2",
      "@id": "dtmi:com:example:TemperatureController;1",
      "@type": "Interface",
      "displayName": "Temperature Controller",
      "contents": [
        {
          "@type" : "Component",
          "schema": "dtmi:com:example:Thermostat;1",
          "name": "thermostat1"
        }
      ]

      "@context": "dtmi:dtdl:context;2",
      "@id": "dtmi:com:example:Thermostat;1",
      "@type": "Interface",
      "contents": [
        {
          "@type": "Property",
          "name": "temperature",
          "schema": "double"
        }
      ]
      ```
   :::column-end:::
   :::column span="":::
      **報告されるプロパティ**

      ```json
      "reported": {
        "thermostat1": {
          "__t": "c",
          "temperature": 21.3
        }
      }
      ```
   :::column-end:::
:::row-end:::

## <a name="writable-properties"></a>書き込み可能なプロパティ

デバイスでは、報告されたプロパティを送信することで、プロパティを受け取ったことを確認する必要があります。 報告されたプロパティには次のものが含まれます。

- `value` - プロパティの実際の値 (通常は受信した値。ただし、デバイスは別の値を報告するように決定できます)。
- `ac` - HTTP 状態コードを使用する受信確認コード。
- `av` - 目的のプロパティの `$version` を参照する確認バージョン。
- `ad` - 省略可能な受信確認の説明。

### <a name="sample-no-component-writable-property"></a>コンポーネントなしの書き込み可能プロパティのサンプル

デバイスからは、DTDL v2 の規則に従う有効な JSON を送信できます。

:::row:::
   :::column span="":::
      **DTDL**

      ```json
      "@context": "dtmi:dtdl:context;2",
      "@id": "dtmi:example: Thermostat;1",
      "@type": "Interface",
      "contents": [
        {
          "@type": "Property",
          "name": "targetTemperature",
          "schema": "double",
          "writable": true
        }
      ]
      ```
   :::column-end:::
   :::column span="":::
      **目的のプロパティ**

      ```json
      "desired" :
      {
        "targetTemperature" : 21.3
      },
      "$version" : 3
      ```
   :::column-end:::
   :::column span="":::
      **報告されるプロパティ**

      ```json
      "reported": {
        "targetTemperature": {
          "value": 21.3,
          "ac": 200,
          "av": 3,
          "ad": "complete"
       }
     }
      ```
   :::column-end:::
:::row-end:::

### <a name="sample-multiple-components-writable-property"></a>複数のコンポーネントの書き込み可能プロパティのサンプル

この要素からコンポーネントを参照していることを示すために、デバイでスは `{"__t": "c"}` マーカーを追加する必要があります。

マーカーはコンポーネント レベルの更新の場合にのみ送信されるため、デバイスではこのフラグをチェックしないでください。

デバイスでは、報告されたプロパティを送信することで、プロパティを受け取ったことを確認する必要があります。

:::row:::
   :::column span="":::
      **DTDL**

      ```json
      "@context": "dtmi:dtdl:context;2",
      "@id": "dtmi:com:example:TemperatureController;1",
      "@type": "Interface",
      "displayName": "Temperature Controller",
      "contents": [
        {
          "@type" : "Component",
          "schema": "dtmi:com:example:Thermostat;1",
          "name": "thermostat1"
        }
      ]

      "@context": "dtmi:dtdl:context;2",
      "@id": "dtmi:com:example:Thermostat;1",
      "@type": "Interface",
      "contents": [
        {
          "@type": "Property",
          "name": "targetTemperature",
          "schema": "double",
          "writable": true
        }
      ]
      ```
   :::column-end:::
   :::column span="":::
      **目的のプロパティ**

      ```json
      "desired": {
        "thermostat1": {
          "__t": "c",
          "targetTemperature": 21.3
        }
      },
      "$version" : 3
      ```
   :::column-end:::
   :::column span="":::
      **報告されるプロパティ**

      ```json
      "reported": {
        "thermostat1": {
          "__t": "c",
          "targetTemperature": {
            "value": 23,
            "ac": 200,
            "av": 3,
            "ad": "complete"
          }
        }
      }
      ```
   :::column-end:::
:::row-end:::

## <a name="commands"></a>コマンド

プレフィックスなしでコマンド名を使用するコンポーネント インターフェイスはありません。

デバイスでは、複数のコンポーネント インターフェイスで、`componentName*commandName` という形式のコマンド名を使用します。

## <a name="next-steps"></a>次のステップ

IoT プラグ アンド プレイ規則について学習したので、いくつかの追加リソースを次に示します。

- [Digital Twins Definition Language (DTDL)](https://github.com/Azure/opendigitaltwins-dtdl)
- [C デバイス SDK](https://docs.microsoft.com/azure/iot-hub/iot-c-sdk-ref/)
- [IoT REST API](https://docs.microsoft.com/rest/api/iothub/device)
- [モデル コンポーネント](./concepts-components.md)
