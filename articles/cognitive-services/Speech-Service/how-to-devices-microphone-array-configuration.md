---
title: マイク配列を構成する方法 - Speech サービス
titleSuffix: Azure Cognitive Services
description: Speech Devices SDK で使用できるようにマイク配列を構成する方法について説明します。
services: cognitive-services
author: mswellsi
manager: yanbo
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 05/01/2020
ms.author: wellsi
ms.openlocfilehash: cf0580c96f5bf78f0444b2bb39088f2a417fd658
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "95025063"
---
# <a name="how-to-configure-a-microphone-array"></a>マイク配列を構成する方法

この記事では、[マイク配列](./speech-devices-sdk-microphone.md)を構成する方法について説明します。 これには、動作する角度の設定と、Speech Devices SDK に使用するマイクを選択する方法が含まれます。

Speech Devices SDK は、[Microsoft のガイドライン](./speech-devices-sdk-microphone.md)に従って設計されたマイク配列で最適に動作します。 マイク配列の構成は、オペレーティング システムによって提供されるか、次のいずれかの方法で提供されます。

元々、マイク配列は Speech Devices SDK でサポートされており、固定された一連の構成から選択することができました。

```java
private static String DeviceGeometry = "Circular6+1"; // "Circular6+1", "Linear4",
private static String SelectedGeometry = "Circular6+1"; // "Circular6+1", "Circular3+1", "Linear4", "Linear2"
```

Windows では、マイク配列の構成はオーディオ ドライバーによって提供されます。

Speech Devices SDK の v1.11.0 以降は、[JSON ファイル](https://aka.ms/sdsdk-micarray-json)からの構成もサポートされています。


## <a name="windows"></a>Windows
Windows では、マイク配列のジオメトリ情報は、オーディオ ドライバーから自動的に取得されます。 そのため、`DeviceGeometry`、`SelectedGeometry`、および `MicArrayGeometryConfigFile` プロパティは省略可能です。 `MicArrayGeometryConfigFile` を使用して提供されている [JSON ファイル](https://aka.ms/sdsdk-micarray-json)は、ビームフォーミング範囲を取得するためにのみ使用されます。

マイク配列が `AudioConfig::FromMicrophoneInput` を使用して指定されている場合は、指定されたマイクを使用します。 マイクが指定されていない場合、または `AudioConfig::FromDefaultMicrophoneInput` が呼び出されている場合は、Windows の [サウンド設定] で指定されている既定のマイクを使用します。
Speech Devices SDK の Microsoft Audio Stack では、16 KHz の整数倍のサンプル レートのダウン サンプリングのみがサポートされています。

## <a name="linux"></a>Linux
Linux では、マイクのジオメトリ情報を指定する必要があります。 `DeviceGeometry` と `SelectedGeometry` の使用は引き続きサポートされます。 また、`MicArrayGeometryConfigFile` プロパティを使用して JSON ファイルから指定することもできます。 Windows と同様に、ビームフォーミング範囲は JSON ファイルから指定されます。

マイク配列が `AudioConfig::FromMicrophoneInput` を使用して指定されている場合は、指定されたマイクを使用します。 マイクが指定されていない場合、または `AudioConfig::FromDefaultMicrophoneInput` が呼び出されている場合は、 *[既定]* という名前の ALSA デバイスから録音します。 既定では *[既定]* は常にカード 0 のデバイス 0 を指しますが、ユーザーはこれを `asound.conf` ファイルから変更できます。 

Speech Devices SDK の Microsoft Audio Stack では、16 KHz の整数倍のサンプル レートのダウン サンプリングのみがサポートされています。 さらに、サポートされている形式は32 ビット IEEE リトル エンディアン float 型、32 ビット リトル エンディアン符号付き int 型、24 ビット リトル エンディアン符号付き int 型、16 ビット リトル エンディアン符号付き int、8 ビット符号付き int 型です。

## <a name="android"></a>Android
現在、Speech Devices SDK でサポートされているのは [Roobo v1](./speech-devices-sdk-quickstart.md?pivots=platform-android%253fpivots%253dplatform-android) のみです。 動作は以前のリリースと同じですが、`MicArrayGeometryConfigFile` プロパティを使用して、ビームフォーミング範囲が含まれている JSON ファイルを指定できるようになりました。

## <a name="microphone-array-configuration-json"></a>マイク配列の構成 JSON

マイク配列のジオメトリ構成に使用する JSON ファイルは、[JSON スキーマ](https://aka.ms/sdsdk-micarray-json)に従います。 スキーマに従ういくつかの例を次に示します。


```json
{
    "micArrayType": "Linear",
    "geometry": "Linear4"
}
```


または


```json
{
    "micArrayType": "Planar",
    "horizontalAngleBegin": 0,
    "horizontalAngleEnd": 360,
    "numberOfMicrophones": 4,
    "micCoord": [
        {
            "xCoord": 0,
            "yCoord": 0,
            "zCoord": 0
        },
        {
            "xCoord": 40,
            "yCoord": 0,
            "zCoord": 0
        },
        {
            "xCoord": -20,
            "yCoord": -35,
            "zCoord": 0
        },
        {
            "xCoord": -20,
            "yCoord": 35,
            "zCoord": 0
        }
    ]
}
```

または

```json
{
    "micArrayType": "Linear",
    "horizontalAngleBegin": 70,
    "horizontalAngleEnd": 110
}
```


## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [Speech Device を選択する](get-speech-devices-sdk.md)