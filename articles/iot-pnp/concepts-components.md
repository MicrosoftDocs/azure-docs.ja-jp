---
title: IoT プラグ アンド プレイ モデルのコンポーネントの理解 | Microsoft Docs
description: コンポーネントを使用する IoT プラグ アンド プレイ DTDL モデルとコンポーネントを使用しないモデルの違いについて説明します。
author: ericmitt
ms.author: ericmitt
ms.date: 07/07/2020
ms.topic: conceptual
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: 4c41edc477460e6d239688aafe6d7219bed36cd4
ms.sourcegitcommit: 46f8457ccb224eb000799ec81ed5b3ea93a6f06f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/28/2020
ms.locfileid: "87352068"
---
# <a name="iot-plug-and-play-components-in-models"></a>モデル内の IoT プラグ アンド プレイ コンポーネント

Iot プラグ アンド プレイの規則では、IoT ハブへの接続時に、デバイスが Digital Twins Definition Language (DTDL) モデル ID を提示すると、デバイスは IoT プラグ アンド プレイ デバイスになります。

次のスニペットは、モデル ID の例を示しています。

```json
 "@id": "dtmi:com:example:TemperatureController;1"
 "@id": "dtmi:com:example:Thermostat;1",
```

## <a name="no-components"></a>コンポーネントなし

単純なモデルでは、埋め込みコンポーネントまたはカスケードされたコンポーネントは使用されません。 このモデルには、ヘッダー情報と、テレメトリ、プロパティ、およびコマンドを定義するためのコンテンツ セクションが含まれています。

次の例は、コンポーネントを使用しない単純なモデルの一部を示しています。

```json
{
  "@context": "dtmi:dtdl:context;2",
  "@id": "dtmi:com:example:Thermostat;1",
  "@type": "Interface",
  "displayName": "Thermostat",
  "description": "Reports current temperature and provides desired temperature control.",
  "contents": [
    {
      "@type": [
        "Telemetry",
        "Temperature"
      ],
      "name": "temperature",
      "displayName": "Temperature",
      "description": "Temperature in degrees Celsius.",
      "schema": "double",
      "unit": "degreeCelsius"
    },
    {
      "@type": [
        "Property",
...
```

このモデルにはコンポーネントが明示的に定義されていませんが、すべてのテレメトリ、プロパティ、およびコマンドの定義を含む単一のコンポーネントがあるかのように動作します。

次のスクリーンショットは、Azure IoT Explorer ツールでモデルがどのように表示されるかを示しています。

:::image type="content" source="media/concepts-components/default-component.png" alt-text="Azure IoT Explorer の既定のコンポーネント":::

モデル ID は、次のスクリーンショットに示すように、デバイス ツインのプロパティに格納されます。

:::image type="content" source="media/concepts-components/twin-model-id.png" alt-text="デジタル ツインのプロパティのモデル ID":::

コンポーネントのない DTDL モデルは、1 つのセットのテレメトリ、プロパティ、およびコマンドを持つデバイス用に簡略化されたモデルです。 コンポーネントを使用しないモデルでは、既存のデバイスを IoT プラグ アンド プレイ デバイスに簡単に移行することができます。コンポーネントを定義することなく、実際のデバイスを記述する DTDL モデルを作成します。

## <a name="multiple-components"></a>複数のコンポーネント

コンポーネントを使用すると、他のインターフェイスのアセンブリとしてモデル インターフェイスを作成できます。

たとえば、[Thermostat](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/samples/Thermostat.json) インターフェイスがモデルとして定義されているとします。 [Temperature Controller モデル](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/samples/TemperatureController.json)を定義するときに、このインターフェイスを 1 つ以上のコンポーネントとして組み込むことができます。 次の例では、これらのコンポーネントを `thermostat1` および `thermostat2` と呼んでいます。

複数のコンポーネントを持つ DTDL モデルの場合は、2 つ以上のコンポーネント セクションがあります。 次のスニペットに示すように、各セクションでは `@type` に `Component` が設定され、スキーマを明示的に参照しています。

```json
{
  "@context": "dtmi:dtdl:context;2",
  "@id": "dtmi:com:example:Thermostat;1",
  "@type": "Interface",
  "displayName": "Thermostat",
  "description": "Reports current temperature and provides desired temperature control.",
  "contents": [
... 
    {
      "@type" : "Component",
      "schema": "dtmi:com:example:Thermostat;1",
      "name": "thermostat1",
      "displayName": "Thermostat One",
      "description": "Thermostat One of Two."
    },
    {
      "@type" : "Component",
      "schema": "dtmi:com:example:Thermostat;1",
      "name": "thermostat2",
      "displayName": "Thermostat Two",
      "description": "Thermostat Two of Two."
    },
    {
      "@type": "Component",
      "schema": "dtmi:azure:DeviceManagement:DeviceInformation;1",
      "name": "deviceInformation",
      "displayName": "Device Information interface",
      "description": "Optional interface with basic device hardware information."
    }
...
```

このモデルでは、contents セクションに 3 つのコンポーネントが定義されています (2 つの `Thermostat` コンポーネントと 1 つの `DeviceInformation` コンポーネント)。 また、既定のルート コンポーネントもあります。

## <a name="next-steps"></a>次のステップ

ここまでで、デバイスのコンポーネントについて学習しました。その他のリソースを次に示します。

- [Digital Twins Definition Language v2 (DTDL)](https://github.com/Azure/opendigitaltwins-dtdl)
- [モデルのリポジトリ](./concepts-model-repository.md)