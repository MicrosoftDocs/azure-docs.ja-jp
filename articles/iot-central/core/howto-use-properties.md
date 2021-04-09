---
title: Azure IoT Central ソリューションでプロパティを使用する
description: Azure IoT Central ソリューションで読み取り専用および書き込み可能なプロパティを使用する方法について説明します。
author: dominicbetts
ms.author: dobett
ms.date: 11/06/2020
ms.topic: how-to
ms.service: iot-central
services: iot-central
ms.openlocfilehash: 36329987e510372ff286a10584a115ea259afc60
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98119086"
---
# <a name="use-properties-in-an-azure-iot-central-solution"></a>Azure IoT Central ソリューションでプロパティを使用する

このハウツー ガイドでは、デバイス開発者として、Azure IoT Central アプリケーションのデバイス テンプレートで定義されているデバイス プロパティを使用する方法について説明します。

プロパティは、特定の時点の値を表します。 たとえば、デバイスはプロパティを使用して、到達しようとしているターゲット温度を報告できます。 既定では、IoT Central でのデバイスのプロパティは読み取り専用です。 書き込み可能なプロパティを使用すると、デバイスと Azure IoT Central アプリケーションの間で状態を同期することができます。

Azure IoT Central アプリケーションで、クラウドのプロパティを定義することもできます。 クラウドのプロパティ値は、デバイスと交換されることはなく、この記事の範囲外です。

## <a name="define-your-properties"></a>プロパティを定義する

プロパティは、デバイスの状態を表すデータ フィールドです。 プロパティは、デバイスのオンまたはオフの状態など、デバイスの持続的状態を表すために使用します。 プロパティでは、デバイスのソフトウェア バージョンなど、デバイスの基本的なプロパティを表すこともできます。 プロパティを読み取り専用または書き込み可能として宣言します。

次のスクリーンショットは、Azure IoT Central アプリケーションでのプロパティ定義を示しています。

:::image type="content" source="media/howto-use-properties/property-definition.png" alt-text="Azure IoT Central アプリケーションでのプロパティ定義を示すスクリーンショット。":::

プロパティ機能の構成設定を次の表に示します。

| フィールド           | 説明                                                                                                                                                                                                                        |
|-----------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| 表示名    | ダッシュボードとフォームで使用されるプロパティ値の表示名。                                                                                                                                                              |
| 名前            | プロパティの名前。 Azure IoT Central によって表示名からこのフィールドの値が生成されますが、必要に応じて独自の値を選択できます。 このフィールドは英数字にする必要があります。  デバイス コードでは、この **[名前]** 値を使用します。           |
| 機能の種類 | プロパティ。                                                                                                                                                                                                                          |
| セマンティックの種類   | テレメトリのセマンティックの種類 (温度、状態、イベントなど)。 セマンティックの種類の選択によって、次のどのフィールドを使用できるかが決まります。                                                                       |
| スキーマ          | プロパティのデータ型 (double、string、vector など)。 使用できる選択肢は、セマンティックの種類によって決まります。 スキーマは、イベントおよび状態のセマンティックの種類には使用できません。                                               |
| 書き込み可能       | プロパティが書き込み可能でない場合、デバイスから Azure IoT Central にプロパティ値を報告することができます。 プロパティが書き込み可能な場合、デバイスから Azure IoT Central にプロパティ値を報告することができます。 この場合は Azure IoT Central からデバイスにプロパティの更新を送信できます。 |
| 重大度        | イベントのセマンティックの種類にのみ使用できます。 重大度は、**エラー**、**情報**、または **警告** です。                                                                                                                         |
| 状態の値    | 状態のセマンティックの種類にのみ使用できます。 考えられる状態の値を定義します。それぞれに表示名、名前、列挙型、および値があります。                                                                                   |
| ユニット            | プロパティ値の単位 (**mph**、 **%** 、または **&deg;C** など)。                                                                                                                                                              |
| 表示単位    | ダッシュボードとフォームで使用する表示単位。                                                                                                                                                                                    |
| 解説         | プロパティ機能に関するコメント。                                                                                                                                                                                        |
| 説明     | プロパティ機能の説明。                                                                                                                                                                                          |

プロパティは、次に示すように、デバイス テンプレートのインターフェイスで定義することもできます。

``` json
{
  "@type": [
    "Property",
    "Temperature"
  ],
  "name": "targetTemperature",
  "schema": "double",
  "displayName": "Target Temperature",
  "description": "Allows to remotely specify the desired target temperature.",
  "unit" : "degreeCelsius",
  "writable": true
},
{
  "@type": [
    "Property",
    "Temperature"
  ],
  "name": "maxTempSinceLastReboot",
  "schema": "double",
  "unit" : "degreeCelsius",
  "displayName": "Max temperature since last reboot.",
  "description": "Returns the max temperature since last device reboot."
}
```

この例では、2 つのプロパティを示します。 これらのプロパティは、UI のプロパティ定義に関連しています。

* `@type` では、機能の種類 `Property` を指定します。 前の例では、両方のプロパティのセマンティックの種類 `Temperature` も示しています。
* プロパティの `name`。
* `schema` では、プロパティのデータ型を指定します。 この値は、double、integer、Boolean、string などのプリミティブ型にすることができます。 複合オブジェクト型およびマップもサポートされています。
* `writable`: 既定では、プロパティは読み取り専用です。 このフィールドを使用することで、プロパティを書き込み可能としてマークできます。

表示名や説明などのオプション フィールドを使用すると、インターフェイスや機能に詳細を追加できます。

プロパティを作成するときには、**Object** や **Enum** などの複雑なスキーマ型を指定できます。

![機能を追加する方法を示すスクリーンショット。](./media/howto-use-properties/property.png)

**Object** などの複雑な **スキーマ** を選択するときには、オブジェクトも定義する必要があります。

:::image type="content" source="media/howto-use-properties/object.png" alt-text="オブジェクトを定義する方法を示すスクリーンショット":::

次のコードは、Object プロパティ型の定義を示しています。 このオブジェクトには、文字列型と整数型の 2 つのフィールドがあります。

``` json
{
  "@type": "Property",
  "displayName": {
    "en": "ObjectProperty"
  },
  "name": "ObjectProperty",
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

## <a name="implement-read-only-properties"></a>読み取り専用プロパティを実装する

既定では、プロパティは読み取り専用です。 読み取り専用のプロパティを使用すると、デバイスによって、プロパティ値の更新が Azure IoT Central アプリケーションに報告されます。 Azure IoT Central アプリケーションでは、読み取り専用のプロパティの値を設定することはできません。

Azure IoT Central では、デバイス ツインを使用して、デバイスと Azure IoT Central アプリケーションの間でプロパティ値を同期します。 デバイスのプロパティ値では、デバイス ツインによってレポートされるプロパティが使用されます。 詳細については、[デバイス ツイン](../../iot-hub/tutorial-device-twins.md)に関するページを参照してください。

デバイス モデルの次のスニペットは、読み取り専用のプロパティ型の定義を示しています。

``` json
{
  "name": "model",
  "displayName": "Device model",
  "schema": "string",
  "comment": "Device model name or ID. Ex. Surface Book 2."
}
```

プロパティの更新は、デバイスによって、JSON ペイロードとして送信されます。 詳細については、[ペイロード](./concepts-telemetry-properties-commands.md)に関するページを参照してください。

Azure IoT device SDK を使用して、Azure IoT Central アプリケーションにプロパティの更新を送信できます。

デバイス ツインのプロパティは、次の関数を使用して Azure IoT Central アプリケーションに送信できます。

``` javascript
hubClient.getTwin((err, twin) => {
    const properties = {
        model: 'environmentalSensor1.0'
    };
    twin.properties.reported.update(properties, (err) => {
        console.log(err ? `error: ${err.toString()}` : `status: success` )
    });
});
```

この記事では、簡単にするために Node.js を使用します。 他の言語の例については、チュートリアル「[クライアント アプリケーションを作成して Azure IoT Central アプリケーションに接続する](tutorial-connect-device.md)」を参照してください。

Azure IoT Central アプリケーションの次のビューは、表示できるプロパティを示しています。 このビューでは、 **[デバイス モデル]** プロパティは自動的に "_読み取り専用のデバイス プロパティ_" になります。

:::image type="content" source="media/howto-use-properties/read-only.png" alt-text="読み取り専用のプロパティのビューを示すスクリーンショット":::

## <a name="implement-writable-properties"></a>書き込み可能なプロパティを実装する

書き込み可能なプロパティは、フォーム上の Azure IoT Central アプリケーションの演算子によって設定されます。 このプロパティは Azure IoT Central からデバイスに送信されます。 Azure IoT Central では、デバイスからの確認応答を待機します。

デバイス モデルの次のスニペットは、書き込み可能なプロパティ型の定義を示しています。

``` json
{
  "@type": "Property",
  "displayName": "Brightness Level",
  "description": "The brightness level for the light on the device. Can be specified as 1 (high), 2 (medium), 3 (low)",
  "name": "brightness",
  "writable": true,
  "schema": "long"
}
```

デバイス側で応答する書き込み可能なプロパティを定義して処理するに、次のコードを使用できます。

``` javascript
hubClient.getTwin((err, twin) => {
    twin.on('properties.desired.brightness', function(desiredBrightness) {
        console.log( `Received setting: ${desiredBrightness.value}` );
        var patch = {
            brightness: {
                value: desiredBrightness.value,
                ad: 'success',
                ac: 200,
                av: desiredBrightness.$version
            }
        }
        twin.properties.reported.update(patch, (err) => {
            console.log(err ? `error: ${err.toString()}` : `status: success` )
        });
    });
});
```

応答メッセージには、`ac` フィールドと `av` フィールドが含まれている必要があります。 `ad` フィールドは省略可能です。 例については、以下のスニペットを参照してください。

* `ac` は、次の表の値を使用する数値フィールドです。
* `av` は、デバイスに送信されるバージョン番号です。
* `ad` は、オプションの文字列での説明です。

| 値 | Label | 説明 |
| ----- | ----- | ----------- |
| `'ac': 200` | 完了 | プロパティの変更操作が正常に完了しました。 |
| `'ac': 202` または `'ac': 201` | Pending | プロパティの変更操作が保留中または進行中です。 |
| `'ac': 4xx` | エラー | 要求されたプロパティの変更は、有効でなかったか、エラーが発生しました。 |
| `'ac': 5xx` | エラー | 要求された変更の処理中に、デバイスで予期しないエラーが発生しました。 |

デバイス ツインの詳細については、「[バックエンド サービスからデバイスを構成する](../../iot-hub/tutorial-device-twins.md)」を参照してください。

オペレーターが Azure IoT Central アプリケーションで書き込み可能なプロパティを設定すると、アプリケーションではデバイス ツインによって必要とされるプロパティを使用して、デバイスに値を送信します。 次に、デバイスによってデバイス ツインの reported プロパティが使用され、応答が行われます。 Azure IoT Central は、reported プロパティの値を受け取ると、"**承認済み**" の状態でプロパティ ビューを更新します。

次のビューは、書き込み可能なプロパティを示しています。 値を入力して **[保存]** を選択すると、初期状態は "**保留中**" になります。 デバイス側が変更を受け入れると、状態は "**承認済み**" に変わります。

!["保留中" の状態を示すスクリーンショット。](./media/howto-use-properties/status-pending.png)

!["承認済み" のプロパティを示すスクリーンショット。](./media/howto-use-properties/accepted.png)

## <a name="next-steps"></a>次の手順

Azure IoT Central アプリケーションでプロパティを使用する方法を習得したら、次は以下を参照してください。

* [Payloads](concepts-telemetry-properties-commands.md)
* [クライアント アプリケーションを作成して Azure IoT Central アプリケーションに接続する](tutorial-connect-device.md)