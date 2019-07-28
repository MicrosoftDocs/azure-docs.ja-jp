---
title: Media Services v3 CLI を使用するカスタム変換のエンコード - Azure | Microsoft Docs
description: このトピックでは、Azure Media Services v3 で CLI を使用してカスタム変換をエンコードする方法を示します。
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.custom: ''
ms.date: 05/14/2019
ms.author: juliako
ms.openlocfilehash: 42b7c2d86525c428253137b424fe58bb61edba70
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/13/2019
ms.locfileid: "65762017"
---
# <a name="how-to-encode-with-a-custom-transform---cli"></a>カスタム変換を使用してエンコードする方法 - CLI

Azure Media Services でエンコードする場合、クイック スタートの「[ファイルのストリーミング配信](stream-files-cli-quickstart.md#create-a-transform-for-adaptive-bitrate-encoding)」で示されている業界のベスト プラクティスに基づき、推奨される組み込みプリセットのいずれかを使用して、すぐに作業を開始することができます。 また、特定のシナリオやデバイス要件をターゲットとするカスタム プリセットを作成することもできます。

## <a name="considerations"></a>考慮事項

カスタム プリセットを作成するときは、次の考慮事項が適用されます。

* AVC コンテンツの高さと幅のすべての値は、4 の倍数である必要があります。
* Azure Media Services v3 では、エンコード ビットレートはすべてビット/秒単位で指定されます。 これは v2 API のプリセットとは異なります。v2 では、キロビット/秒が単位として使用されていました。 たとえば、v2 でビットレートが 128 (キロビット/秒) に指定されていた場合、v3 では 128000 (ビット/秒) に設定されます。

## <a name="prerequisites"></a>前提条件 

[Media Services アカウントを作成する](create-account-cli-how-to.md) <br/>リソース グループ名と Media Services アカウント名を覚えておいてください。 

[!INCLUDE [media-services-cli-instructions](../../../includes/media-services-cli-instructions.md)]

## <a name="define-a-custom-preset"></a>カスタム プリセットを定義する

次の例では、新しい変換の要求本文を定義しています。 この変換が使用されたときに生成される一連の出力を定義します。 

この例では、まずオーディオ エンコードのための AacAudio レイヤーと、ビデオ エンコードのための 2 つの H264Video レイヤーを追加します。 ビデオ レイヤーでは、出力ファイル名で使用できるようにラベルを割り当てます。 次に、出力にサムネイルも含めます。 次の例では、入力ビデオの解像度の 50%、および 3 つのタイムスタンプ (入力ビデオの長さの {25%, 50%, 75}) で生成された、PNG 形式の画像を指定します。 最後に、出力ファイルの形式 (ビデオとオーディオの形式とサムネイルの形式) を指定します。 複数の H264Layer があるため、レイヤーごとに一意の名前を生成するマクロを使用する必要があります。 `{Label}` または `{Bitrate}` のいずれかのマクロを使用できます。この例では、前者が示されています。

この変換をファイルに保存します。 この例では、ファイルに `customPreset.json` という名前を付けます。 

```json
{
    "@odata.type": "#Microsoft.Media.StandardEncoderPreset",
    "codecs": [
        {
            "@odata.type": "#Microsoft.Media.AacAudio",
            "channels": 2,
            "samplingRate": 48000,
            "bitrate": 128000,
            "profile": "AacLc"
        },
        {
            "@odata.type": "#Microsoft.Media.H264Video",
            "keyFrameInterval": "PT2S",
            "stretchMode": "AutoSize",
            "sceneChangeDetection": false,
            "complexity": "Balanced",
            "layers": [
                {
                    "width": "1280",
                    "height": "720",
                    "label": "HD",
                    "bitrate": 3400000,
                    "maxBitrate": 3400000,
                    "bFrames": 3,
                    "slices": 0,
                    "adaptiveBFrame": true,
                    "profile": "Auto",
                    "level": "auto",
                    "bufferWindow": "PT5S",
                    "referenceFrames": 3,
                    "entropyMode": "Cabac"
                },
                {
                    "width": "640",
                    "height": "360",
                    "label": "SD",
                    "bitrate": 1000000,
                    "maxBitrate": 1000000,
                    "bFrames": 3,
                    "slices": 0,
                    "adaptiveBFrame": true,
                    "profile": "Auto",
                    "level": "auto",
                    "bufferWindow": "PT5S",
                    "referenceFrames": 3,
                    "entropyMode": "Cabac"
                }
            ]
        },
        {
            "@odata.type": "#Microsoft.Media.PngImage",
            "stretchMode": "AutoSize",
            "start": "25%",
            "step": "25%",
            "range": "80%",
            "layers": [
                {
                    "width": "50%",
                    "height": "50%"
                }
            ]
        }
    ],
    "formats": [
        {
            "@odata.type": "#Microsoft.Media.Mp4Format",
            "filenamePattern": "Video-{Basename}-{Label}-{Bitrate}{Extension}",
            "outputFiles": []
        },
        {
            "@odata.type": "#Microsoft.Media.PngFormat",
            "filenamePattern": "Thumbnail-{Basename}-{Index}{Extension}"
        }
    ]
}

```

## <a name="create-a-new-transform"></a>新しい変換を作成する  

この例では、先ほど定義したカスタム プリセットに基づいて**変換**を作成します。 変換を作成するときは、すでに存在している変換があるかどうか確認する必要があります。 変換が存在する場合は再利用します。 次の `show` コマンドは、変換が存在する場合、`customTransformName` を返します。

```cli
az ams transform show -a amsaccount -g amsResourceGroup -n customTransformName
```

次の CLI コマンドは、(先ほど定義した) カスタム プリセットに基づいて変換を作成します。 

```cli
az ams transform create -a amsaccount -g amsResourceGroup -n customTransformName --description "Basic Transform using a custom encoding preset" --preset customPreset.json
```

Media Services が、指定されたビデオまたはオーディオに変換を適用するためには、その変換に基づいてジョブを送信する必要があります。 変換に基づいてジョブを送信する方法を示す完全な例については、「[クイック スタート:ビデオ ファイルのストリーム配信 - CLI](stream-files-cli-quickstart.md)」を参照してください。

## <a name="see-also"></a>関連項目

[Azure CLI](https://docs.microsoft.com/cli/azure/ams?view=azure-cli-latest)
