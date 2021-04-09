---
title: Azure IoT Central のテレメトリ、プロパティ、およびコマンドのペイロード | Microsoft Docs
description: Azure IoT Central デバイス テンプレートを使用すると、実装する必要のある、デバイスのテレメトリ、プロパティ、およびコマンドを指定できます。 デバイスが IoT Central と交換できるデータの形式について説明します。
author: dominicbetts
ms.author: dobett
ms.date: 12/19/2020
ms.topic: conceptual
ms.service: iot-central
services: iot-central
ms.custom: device-developer
ms.openlocfilehash: f027b2d41f63b5aa7ea3df87e06224abd629799b
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "100535316"
---
# <a name="telemetry-property-and-command-payloads"></a>テレメトリ、プロパティ、およびコマンドのペイロード

"_この記事は、デバイス開発者を対象としています。_ "

Azure IoT Central のデバイス テンプレートは、以下を定義するブループリントです。

* デバイスが IoT Central に送信するテレメトリ。
* デバイスが IoT Central と同期するプロパティ。
* IoT Central がデバイスに対して呼び出すコマンド。

この記事では、デバイス開発者に向けて、デバイス テンプレートで定義されたテレメトリ、プロパティ、およびコマンドについてデバイスが送受信する JSON ペイロードについて説明します。

この記事では、利用可能なすべての種類のテレメトリ、プロパティ、およびコマンドのペイロードについては説明しませんが、例では全種類のキーを示します。

それぞれの例は、デバイス モデルのスニペットを示しています。これは、デバイスが IoT Central アプリケーションとどのように対話する必要があるかを示すために、種類および JSON ペイロードの例を定義するものです。

> [!NOTE]
> IoT Central は有効な JSON を受け付けますが、それが視覚化に使用されるのは、デバイス モデルでの定義と一致している場合のみです。 定義に一致していないデータはエクスポートできます。「[Azure で宛先に IoT データをエクスポートする](howto-export-data.md)」を参照してください。

デバイス モデルを定義する JSON ファイルでは、[デジタル ツイン定義言語 (DTDL) v2](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md) が使用されます。

使用中のこれらのペイロードの一部を示すサンプル デバイス コードについては、チュートリアルの「[クライアント アプリケーションを作成して Azure IoT Central アプリケーションに接続する](tutorial-connect-device.md)」を参照してください。

## <a name="view-raw-data"></a>生データを表示する

IoT Central では、デバイスからアプリケーションに送信される生データを表示できます。 この表示は、デバイスから送信されるペイロードに関する問題のトラブルシューティングに役立ちます。 デバイスが送信中の生データを表示するには、次の手順に従います。

1. **[デバイス]** ページの目的のデバイスに移動します。

1. **[生データ]** タブを選択します。

    :::image type="content" source="media/concepts-telemetry-properties-commands/raw-data.png" alt-text="[生データ] ビュー":::

    このビューでは、表示する列を選択したり、確認対象の時間の範囲を設定したりできます。 **[Unmodeled data]\(モデル化されていないデータ\)** 列には、デバイス テンプレート内のプロパティまたはテレメトリ定義と一致しないデバイス データが表示されます。

## <a name="telemetry"></a>テレメトリ

### <a name="primitive-types"></a>プリミティブ型

このセクションでは、デバイスが IoT Central アプリケーションにストリーミングするプリミティブ型のテレメトリの例を示します。

デバイス モデルの次のスニペットは、`boolean` 型のテレメトリの定義を示しています。

```json
{
  "@type": "Telemetry",
  "displayName": {
    "en": "BooleanTelemetry"
  },
  "name": "BooleanTelemetry",
  "schema": "boolean"
}
```

デバイス クライアントは、次の例のようなテレメトリを JSON として送信する必要があります。

```json
{ "BooleanTelemetry": true }
```

デバイス モデルの次のスニペットは、`string` 型のテレメトリの定義を示しています。

```json
{
  "@type": "Telemetry",
  "displayName": {
    "en": "StringTelemetry"
  },
  "name": "StringTelemetry",
  "schema": "string"
}
```

デバイス クライアントは、次の例のようなテレメトリを JSON として送信する必要があります。

```json
{ "StringTelemetry": "A string value - could be a URL" }
```

デバイス モデルの次のスニペットは、`integer` 型のテレメトリの定義を示しています。

```json
{
  "@type": "Telemetry",
  "displayName": {
    "en": "IntegerTelemetry"
  },
  "name": "IntegerTelemetry",
  "schema": "integer"
}

```

デバイス クライアントは、次の例のようなテレメトリを JSON として送信する必要があります。

```json
{ "IntegerTelemetry": 23 }
```

デバイス モデルの次のスニペットは、`double` 型のテレメトリの定義を示しています。

```json
{
  "@type": "Telemetry",
  "displayName": {
    "en": "DoubleTelemetry"
  },
  "name": "DoubleTelemetry",
  "schema": "double"
}
```

デバイス クライアントは、次の例のようなテレメトリを JSON として送信する必要があります。

```json
{ "DoubleTelemetry": 56.78 }
```

デバイス モデルの次のスニペットは、`dateTime` 型のテレメトリの定義を示しています。

```json
{
  "@type": "Telemetry",
  "displayName": {
    "en": "DateTimeTelemetry"
  },
  "name": "DateTimeTelemetry",
  "schema": "dateTime"
}
```

デバイス クライアントは、次の例のようなテレメトリを JSON として送信する必要があります。`DateTime` の型は、ISO 8061 形式にする必要があります。

```json
{ "DateTimeTelemetry": "2020-08-30T19:16:13.853Z" }
```

デバイス モデルの次のスニペットは、`duration` 型のテレメトリの定義を示しています。

```json
{
  "@type": "Telemetry",
  "displayName": {
    "en": "DurationTelemetry"
  },
  "name": "DurationTelemetry",
  "schema": "duration"
}
```

デバイス クライアントは、次の例のようなテレメトリを JSON として送信する必要があります。期間は ISO 8601 形式にする必要があります。

```json
{ "DurationTelemetry": "PT10H24M6.169083011336625S" }
```

### <a name="complex-types"></a>複合型

このセクションでは、デバイスが IoT Central アプリケーションにストリーミングする複合型のテレメトリの例を示します。

デバイス モデルの次のスニペットは、`geopoint` 型のテレメトリの定義を示しています。

```json
{
  "@type": "Telemetry",
  "displayName": {
    "en": "GeopointTelemetry"
  },
  "name": "GeopointTelemetry",
  "schema": "geopoint"
}
```

> [!NOTE]
> **geopoint** のスキーマの種類は、[Digital Twins Definition Language の仕様](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md)に含まれていません。 IoT Central では現在、**geopoint** のスキーマの種類と **location** のセマンティックの種類を下位互換性のためにサポートしています。

デバイス クライアントは、次の例のようなテレメトリを JSON として送信する必要があります。 IoT Central では、値は地図上のピンとして表示されます。

```json
{
  "GeopointTelemetry": {
    "lat": 47.64263,
    "lon": -122.13035,
    "alt": 0
  }
}
```

デバイス モデルの次のスニペットは、`Enum` 型のテレメトリの定義を示しています。

```json
{
  "@type": "Telemetry",
  "displayName": {
    "en": "EnumTelemetry"
  },
  "name": "EnumTelemetry",
  "schema": {
    "@type": "Enum",
    "displayName": {
      "en": "Enum"
    },
    "valueSchema": "integer",
    "enumValues": [
      {
        "displayName": {
          "en": "Item1"
        },
        "enumValue": 0,
        "name": "Item1"
      },
      {
        "displayName": {
          "en": "Item2"
        },
        "enumValue": 1,
        "name": "Item2"
      },
      {
        "displayName": {
          "en": "Item3"
        },
        "enumValue": 2,
        "name": "Item3"
      }
    ]
  }
}
```

デバイス クライアントは、次の例のようなテレメトリを JSON として送信する必要があります。 指定できる値は、IoT Central では `Item1`、`Item2`、`Item3` として表示される `0`、`1`、`2` です。

```json
{ "EnumTelemetry": 1 }
```

デバイス モデルの次のスニペットは、`Object` 型のテレメトリの定義を示しています。 このオブジェクトには、`dateTime` 型、`integer` 型、`Enum` 型である 3 つのフィールドがあります。

```json
{
  "@type": "Telemetry",
  "displayName": {
    "en": "ObjectTelemetry"
  },
  "name": "ObjectTelemetry",
  "schema": {
    "@type": "Object",
    "displayName": {
      "en": "Object"
    },
    "fields": [
      {
        "displayName": {
          "en": "Property1"
        },
        "name": "Property1",
        "schema": "dateTime"
      },
      {
        "displayName": {
          "en": "Property2"
        },
        "name": "Property2",
        "schema": "integer"
      },
      {
        "displayName": {
          "en": "Property3"
        },
        "name": "Property3",
        "schema": {
          "@type": "Enum",
          "displayName": {
            "en": "Enum"
          },
          "valueSchema": "integer",
          "enumValues": [
            {
              "displayName": {
                "en": "Item1"
              },
              "enumValue": 0,
              "name": "Item1"
            },
            {
              "displayName": {
                "en": "Item2"
              },
              "enumValue": 1,
              "name": "Item2"
            },
            {
              "displayName": {
                "en": "Item3"
              },
              "enumValue": 2,
              "name": "Item3"
            }
          ]
        }
      }
    ]
  }
}
```

デバイス クライアントは、次の例のようなテレメトリを JSON として送信する必要があります。 `DateTime` 型は、ISO 8061 に準拠している必要があります。 `Property3` に使用できる値は `0` と `1` で、IoT Central では `Item1`、`Item2`、`Item3` として表示されます。

```json
{
  "ObjectTelemetry": {
      "Property1": "2020-09-09T03:36:46.195Z",
      "Property2": 37,
      "Property3": 2
  }
}
```

デバイス モデルの次のスニペットは、`vector` 型のテレメトリの定義を示しています。

```json
{
  "@type": "Telemetry",
  "displayName": {
    "en": "VectorTelemetry"
  },
  "name": "VectorTelemetry",
  "schema": "vector"
}
```

デバイス クライアントは、次の例のようなテレメトリを JSON として送信する必要があります。

```json
{
  "VectorTelemetry": {
    "x": 74.72395045538597,
    "y": 74.72395045538597,
    "z": 74.72395045538597
  }
}
```

### <a name="event-and-state-types"></a>イベント型と状態型

このセクションでは、デバイスが IoT Central アプリケーションに送信する、テレメトリのイベントおよび状態の例を示します。

デバイス モデルの次のスニペットは、`integer` 型のイベントの定義を示しています。

```json
{
  "@type": [
    "Telemetry",
    "Event"
  ],
  "displayName": {
    "en": "IntegerEvent"
  },
  "name": "IntegerEvent",
  "schema": "integer"
}
```

デバイス クライアントは、次の例のようなイベント データを JSON として送信する必要があります。

```json
{ "IntegerEvent": 74 }
```

デバイス モデルの次のスニペットは、`integer` 型の状態の定義を示しています。

```json
{
  "@type": [
    "Telemetry",
    "State"
  ],
  "displayName": {
    "en": "IntegerState"
  },
  "name": "IntegerState",
  "schema": {
    "@type": "Enum",
    "valueSchema": "integer",
    "enumValues": [
      {
        "displayName": {
          "en": "Level1"
        },
        "enumValue": 1,
        "name": "Level1"
      },
      {
        "displayName": {
          "en": "Level2"
        },
        "enumValue": 2,
        "name": "Level2"
      },
      {
        "displayName": {
          "en": "Level3"
        },
        "enumValue": 3,
        "name": "Level3"
      }
    ]
  }
}
```

デバイス クライアントは、次の例のような状態を JSON として送信する必要があります。 状態の使用できる整数値は、`1`、`2`、または `3` です。

```json
{ "IntegerState": 2 }
```

## <a name="properties"></a>Properties

> [!NOTE]
> プロパティのペイロード形式は、2020 年 7 月 14 日以降に作成されたアプリケーションに適用されます。

### <a name="primitive-types"></a>プリミティブ型

このセクションでは、デバイスが IoT Central アプリケーションに送信するプリミティブ型のプロパティの例を示します。

デバイス モデルの次のスニペットは、`boolean` 型のプロパティの定義を示しています。

```json
{
  "@type": "Property",
  "displayName": {
    "en": "BooleanProperty"
  },
  "name": "BooleanProperty",
  "schema": "boolean",
  "writable": false
}
```

デバイス クライアントは、次の例のような JSON ペイロードを、デバイス ツインの reported プロパティとして送信する必要があります。

```json
{ "BooleanProperty": false }
```

デバイス モデルの次のスニペットは、`boolean` 型のプロパティの定義を示しています。

```json
{
  "@type": "Property",
  "displayName": {
    "en": "LongProperty"
  },
  "name": "LongProperty",
  "schema": "long",
  "writable": false
}
```

デバイス クライアントは、次の例のような JSON ペイロードを、デバイス ツインの reported プロパティとして送信する必要があります。

```json
{ "LongProperty": 439 }
```

デバイス モデルの次のスニペットは、`date` 型のプロパティの定義を示しています。

```json
{
  "@type": "Property",
  "displayName": {
    "en": "DateProperty"
  },
  "name": "DateProperty",
  "schema": "date",
  "writable": false
}
```

デバイス クライアントは、次の例のような JSON ペイロードを、デバイス ツインの reported プロパティとして送信する必要があります。 `Date` 型は、ISO 8061 に準拠している必要があります。

```json
{ "DateProperty": "2020-05-17" }
```

デバイス モデルの次のスニペットは、`duration` 型のプロパティの定義を示しています。

```json
{
  "@type": "Property",
  "displayName": {
    "en": "DurationProperty"
  },
  "name": "DurationProperty",
  "schema": "duration",
  "writable": false
}
```

デバイス クライアントは、次の例のような JSON ペイロードを、デバイス ツインの reported プロパティとして送信する必要があります。期間は、ISO 8601 の期間に準拠している必要があります。

```json
{ "DurationProperty": "PT10H24M6.169083011336625S" }
```

デバイス モデルの次のスニペットは、`float` 型のプロパティの定義を示しています。

```json
{
  "@type": "Property",
  "displayName": {
    "en": "FloatProperty"
  },
  "name": "FloatProperty",
  "schema": "float",
  "writable": false
}
```

デバイス クライアントは、次の例のような JSON ペイロードを、デバイス ツインの reported プロパティとして送信する必要があります。

```json
{ "FloatProperty": 1.9 }
```

デバイス モデルの次のスニペットは、`string` 型のプロパティの定義を示しています。

```json
{
  "@type": "Property",
  "displayName": {
    "en": "StringProperty"
  },
  "name": "StringProperty",
  "schema": "string",
  "writable": false
}
```

デバイス クライアントは、次の例のような JSON ペイロードを、デバイス ツインの reported プロパティとして送信する必要があります。

```json
{ "StringProperty": "A string value - could be a URL" }
```

### <a name="complex-types"></a>複合型

このセクションでは、デバイスが IoT Central アプリケーションに送信する複合型のプロパティの例を示します。

デバイス モデルの次のスニペットは、`geopoint` 型のプロパティの定義を示しています。

```json
{
  "@type": "Property",
  "displayName": {
    "en": "GeopointProperty"
  },
  "name": "GeopointProperty",
  "schema": "geopoint",
  "writable": false
}
```

> [!NOTE]
> **geopoint** のスキーマの種類は、[Digital Twins Definition Language の仕様](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md)に含まれていません。 IoT Central では現在、**geopoint** のスキーマの種類と **location** のセマンティックの種類を下位互換性のためにサポートしています。

デバイス クライアントは、次の例のような JSON ペイロードを、デバイス ツインの reported プロパティとして送信する必要があります。

```json
{
  "GeopointProperty": {
    "lat": 47.64263,
    "lon": -122.13035,
    "alt": 0
  }
}
```

デバイス モデルの次のスニペットは、`Enum` 型のプロパティの定義を示しています。

```json
{
  "@type": "Property",
  "displayName": {
    "en": "EnumProperty"
  },
  "name": "EnumProperty",
  "writable": false,
  "schema": {
    "@type": "Enum",
    "displayName": {
      "en": "Enum"
    },
    "valueSchema": "integer",
    "enumValues": [
      {
        "displayName": {
          "en": "Item1"
        },
        "enumValue": 0,
        "name": "Item1"
      },
      {
        "displayName": {
          "en": "Item2"
        },
        "enumValue": 1,
        "name": "Item2"
      },
      {
        "displayName": {
          "en": "Item3"
        },
        "enumValue": 2,
        "name": "Item3"
      }
    ]
  }
}
```

デバイス クライアントは、次の例のような JSON ペイロードを、デバイス ツインの reported プロパティとして送信する必要があります。 指定できる値は、IoT Central では `Item1`、`Item2`、`Item3` として表示される `0`、`1` です。

```json
{ "EnumProperty": 1 }
```

デバイス モデルの次のスニペットは、`Object` 型のプロパティの定義を示しています。 このオブジェクトには、`string` 型と `integer` 型である 2 つのフィールドがあります。

```json
{
  "@type": "Property",
  "displayName": {
    "en": "ObjectProperty"
  },
  "name": "ObjectProperty",
  "writable": false,
  "schema": {
    "@type": "Object",
    "displayName": {
      "en": "Object"
    },
    "fields": [
      {
        "displayName": {
          "en": "Field1"
        },
        "name": "Field1",
        "schema": "integer"
      },
      {
        "displayName": {
          "en": "Field2"
        },
        "name": "Field2",
        "schema": "string"
      }
    ]
  }
}
```

デバイス クライアントは、次の例のような JSON ペイロードを、デバイス ツインの reported プロパティとして送信する必要があります。

```json
{
  "ObjectProperty": {
    "Field1": 37,
    "Field2": "A string value"
  }
}
```

デバイス モデルの次のスニペットは、`vector` 型のプロパティの定義を示しています。

```json
{
  "@type": "Property",
  "displayName": {
    "en": "VectorProperty"
  },
  "name": "VectorProperty",
  "schema": "vector",
  "writable": false
}
```

デバイス クライアントは、次の例のような JSON ペイロードを、デバイス ツインの reported プロパティとして送信する必要があります。

```json
{
  "VectorProperty": {
    "x": 74.72395045538597,
    "y": 74.72395045538597,
    "z": 74.72395045538597
  }
}
```

### <a name="writeable-property-types"></a>書き込み可能なプロパティ型

このセクションでは、デバイスが IoT Central アプリケーションから受信する書き込み可能なプロパティ型の例を示します。

IoT Central では、デバイスからは、書き込み可能なプロパティの更新への応答があると想定しています。 応答メッセージには、`ac` フィールドと `av` フィールドが含まれている必要があります。 `ad` フィールドは省略可能です。 例については、以下のスニペットを参照してください。

`ac` は、次の表の値を使用する数値フィールドです。

| 値 | Label | 説明 |
| ----- | ----- | ----------- |
| `'ac': 200` | 完了 | プロパティの変更操作が正常に完了しました。 |
| `'ac': 202` または `'ac': 201` | 保留中 | プロパティの変更操作が保留中または進行中です |
| `'ac': 4xx` | エラー | 要求されたプロパティ変更が有効でなかったか、またはエラーを含んでいました。 |
| `'ac': 5xx` | エラー | 要求された変更の処理中に、デバイスで予期しないエラーが発生しました。 |

`av` は、デバイスに送信されるバージョン番号です。

`ad` は、オプションの文字列での説明です。

デバイス モデルの次のスニペットは、書き込み可能な `string` 型のプロパティの定義を示しています。

```json
{
  "@type": "Property",
  "displayName": {
    "en": "StringPropertyWritable"
  },
  "name": "StringPropertyWritable",
  "writable": true,
  "schema": "string"
}
```

デバイスは、IoT Central から次のペイロードを受信します。

```json
{  
  "StringPropertyWritable": "A string from IoT Central", "$version": 7
}
```

デバイスは、更新の処理後、次の JSON ペイロードを IoT Central に送信する必要があります。 このメッセージには、IoT Central から受信した元の更新のバージョン番号が含まれています。 IoT Central がこのメッセージを受信すると、このプロパティは UI で **同期済み** とマークされます。

```json
{
  "StringPropertyWritable": {
    "value": "A string from IoT Central",
    "ac": 200,
    "ad": "completed",
    "av": 7
  }
}
```

デバイス モデルの次のスニペットは、書き込み可能な `Enum` 型のプロパティの定義を示しています。

```json
{
  "@type": "Property",
  "displayName": {
    "en": "EnumPropertyWritable"
  },
  "name": "EnumPropertyWritable",
  "writable": true,
  "schema": {
    "@type": "Enum",
    "displayName": {
      "en": "Enum"
    },
    "valueSchema": "integer",
    "enumValues": [
      {
        "displayName": {
          "en": "Item1"
        },
        "enumValue": 0,
        "name": "Item1"
      },
      {
        "displayName": {
          "en": "Item2"
        },
        "enumValue": 1,
        "name": "Item2"
      },
      {
        "displayName": {
          "en": "Item3"
        },
        "enumValue": 2,
        "name": "Item3"
      }
    ]
  }
}
```

デバイスは、IoT Central から次のペイロードを受信します。

```json
{  
  "EnumPropertyWritable":  1 , "$version": 10
}
```

デバイスは、更新の処理後、次の JSON ペイロードを IoT Central に送信する必要があります。 このメッセージには、IoT Central から受信した元の更新のバージョン番号が含まれています。 IoT Central がこのメッセージを受信すると、このプロパティは UI で **同期済み** とマークされます。

```json
{
  "EnumPropertyWritable": {
    "value": 1,
    "ac": 200,
    "ad": "completed",
    "av": 10
  }
}
```

## <a name="commands"></a>コマンド

デバイス モデルの次のスニペットは、パラメーターがなく、デバイスが何かを返すことを想定していないコマンドの定義を示しています。

```json
{
  "@type": "Command",
  "displayName": {
    "en": "CommandBasic"
  },
  "name": "CommandBasic"
}
```

デバイスは要求で空のペイロードを受信します。そして応答では、成功を示すために `200` HTTP 応答コードを使用して空のペイロードを返す必要があります。

デバイス モデルの次のスニペットは、整数型のパラメーターが 1 つあり、デバイスが整数値を返すことを想定しているコマンドの定義を示しています。

```json
{
  "@type": "Command",
  "request": {
    "@type": "CommandPayload",
    "displayName": {
      "en": "RequestParam"
    },
    "name": "RequestParam",
    "schema": "integer"
  },
  "response": {
    "@type": "CommandPayload",
    "displayName": {
      "en": "ResponseParam"
    },
    "name": "ResponseParam",
    "schema": "integer"
  },
  "displayName": {
    "en": "CommandSimple"
  },
  "name": "CommandSimple"
}
```

デバイスは、要求ペイロードとして整数値を受け取ります。 デバイスは、成功を示すために、`200` HTTP 応答コードを使用して応答ペイロードとして整数値を返す必要があります。

デバイス モデルの次のスニペットは、オブジェクト パラメーターが 1 つあり、デバイスがオブジェクトを返すことを想定しているコマンドの定義を示しています。 この例では、どちらのオブジェクトにも整数フィールドと文字列フィールドがあります。

```json
{
  "@type": "Command",
  "request": {
    "@type": "CommandPayload",
    "displayName": {
      "en": "RequestParam"
    },
    "name": "RequestParam",
    "schema": {
      "@type": "Object",
      "displayName": {
        "en": "Object"
      },
      "fields": [
        {
          "displayName": {
            "en": "Field1"
          },
          "name": "Field1",
          "schema": "integer"
        },
        {
          "displayName": {
            "en": "Field2"
          },
          "name": "Field2",
          "schema": "string"
        }
      ]
    }
  },
  "response": {
    "@type": "CommandPayload",
    "displayName": {
      "en": "ResponseParam"
    },
    "name": "ResponseParam",
    "schema": {
      "@type": "Object",
      "displayName": {
        "en": "Object"
      },
      "fields": [
        {
          "displayName": {
            "en": "Field1"
          },
          "name": "Field1",
          "schema": "integer"
        },
        {
          "displayName": {
            "en": "Field2"
          },
          "name": "Field2",
          "schema": "string"
        }
      ]
    }
  },
  "displayName": {
    "en": "CommandComplex"
  },
  "name": "CommandComplex"
}
```

次のスニペットは、デバイスに送信される要求ペイロードの例を示しています。

```json
{ "Field1": 56, "Field2": "A string value" }
```

次のスニペットは、デバイスから送信される応答ペイロードの例を示しています。 `200` HTTP 応答コードを使用して成功を示します。

```json
{ "Field1": 87, "Field2": "Another string value" }
```

### <a name="long-running-commands"></a>実行時間の長いコマンド

デバイス モデルの次のスニペットは、コマンドの定義を示しています。 このコマンドには整数パラメーターが 1 つあり、デバイスが整数値を返すことが想定されています。

```json
{
  "@type": "Command",
  "request": {
    "@type": "CommandPayload",
    "displayName": {
      "en": "RequestParam"
    },
    "name": "RequestParam",
    "schema": "integer"
  },
  "response": {
    "@type": "CommandPayload",
    "displayName": {
      "en": "ResponseParam"
    },
    "name": "ResponseParam",
    "schema": "integer"
  },
  "displayName": {
    "en": "LongRunningCommandSimple"
  },
  "name": "LongRunningCommandSimple"
}
```

デバイスは、要求ペイロードとして整数値を受け取ります。 このコマンドの処理に時間がかかる場合、デバイスは、`202` HTTP 応答コードを使用して空の応答ペイロードを返し、デバイスが処理の要求を受け付けたことを示します。

デバイスは、要求の処理を完了したら、次の例のようなプロパティを IoT Central に送信する必要があります。 プロパティ名は、コマンド名と同じである必要があります。

```json
{
  "LongRunningCommandSimple": {
    "value": 87
  }
}
```

### <a name="offline-commands"></a>オフライン コマンド

IoT Central Web UI では、コマンドに **[オフラインの場合にキューに入れる]** オプションを選択できます。 オフライン コマンドは、デバイスが接続するとすぐに配信される、ユーザーのソリューションからデバイスへの一方向の通知です。 オフライン コマンドは、要求パラメーターを使用できますが、応答を返すことはできません。

**[オフラインの場合にキューに入れる]** 設定は、デバイス テンプレートからモデルまたはインターフェイスをエクスポートする場合は含まれません。 エクスポートされたモデルまたはインターフェイス JSON を見ても、コマンドがオフライン コマンドであることはわかりません。

オフライン コマンドは、[IoT Hub のクラウドからデバイスへのメッセージ](../../iot-hub/iot-hub-devguide-messages-c2d.md)を使用して、コマンドとペイロードをデバイスに送信します。

デバイス モデルの次のスニペットは、コマンドの定義を示しています。 このコマンドには、datetime フィールドと列挙型を持つオブジェクト パラメーターが含まれています。

```json
{
  "@type": "Command",
  "displayName": {
    "en": "Generate Diagnostics"
  },
  "name": "GenerateDiagnostics",
  "request": {
    "@type": "CommandPayload",
    "displayName": {
      "en": "Payload"
    },
    "name": "Payload",
    "schema": {
      "@type": "Object",
      "displayName": {
        "en": "Object"
      },
      "fields": [
        {
          "displayName": {
            "en": "StartTime"
          },
          "name": "StartTime",
          "schema": "dateTime"
        },
        {
          "displayName": {
            "en": "Bank"
          },
          "name": "Bank",
          "schema": {
            "@type": "Enum",
            "displayName": {
              "en": "Enum"
            },
            "enumValues": [
              {
                "displayName": {
                  "en": "Bank 1"
                },
                "enumValue": 1,
                "name": "Bank1"
              },
              {
                "displayName": {
                  "en": "Bank2"
                },
                "enumValue": 2,
                "name": "Bank2"
              },
              {
                "displayName": {
                  "en": "Bank3"
                },
                "enumValue": 3,
                "name": "Bank3"
              }
            ],
            "valueSchema": "integer"
          }
        }
      ]
    }
  }
}
```

前のスニペットにあるコマンドのデバイス テンプレート UI で **[オフラインの場合にキューに入れる]** オプションを有効にした場合、デバイスが受信するメッセージには次のプロパティが含まれます。

| プロパティ名 | 値の例 |
| ---------- | ----- |
| `custom_properties` | `{'method-name': 'GenerateDiagnostics'}` |
| `data` | `{"StartTime":"2021-01-05T08:00:00.000Z","Bank":2}` |

## <a name="next-steps"></a>次のステップ

デバイス開発者として、デバイス テンプレートについて学んだので、次のステップとして、「[Azure IoT Central に接続する](./concepts-get-connected.md)」を読み、デバイスを IoT Central に登録する方法と IoT Central でデバイスの接続を保護する方法の詳細を把握することをお勧めします。
