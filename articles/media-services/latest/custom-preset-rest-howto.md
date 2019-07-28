---
title: Media Services v3 REST を使用したカスタム変換のエンコード - Azure | Microsoft Docs
description: このトピックでは、Azure Media Services v3 で REST を使用してカスタム変換をエンコードする方法について説明します。
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
ms.openlocfilehash: 30e22cb786e5dc2a667fe41ca8edf398cf0b7613
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/13/2019
ms.locfileid: "65761790"
---
# <a name="how-to-encode-with-a-custom-transform---rest"></a>カスタム変換を使用してエンコードする方法 - REST

Azure Media Services を使用してエンコードする場合、[ファイルのストリーム配信](stream-files-tutorial-with-rest.md#create-a-transform)に関するチュートリアルで示されている業界のベスト プラクティスに基づき、推奨される組み込みプリセットのいずれかを使用して速やかに作業を開始できます。 また、特定のシナリオやデバイス要件をターゲットとするカスタム プリセットを作成することもできます。

## <a name="considerations"></a>考慮事項

カスタム プリセットを作成するときは、次の考慮事項が適用されます。

* AVC コンテンツの高さと幅のすべての値は、4 の倍数である必要があります。
* Azure Media Services v3 では、エンコード ビットレートはすべてビット/秒単位で指定されます。 これは v2 API のプリセットとは異なります。v2 では、キロビット/秒が単位として使用されていました。 たとえば、v2 でビットレートが 128 (キロビット/秒) に指定されていた場合、v3 では 128000 (ビット/秒) に設定されます。

## <a name="prerequisites"></a>前提条件 

- [Media Services アカウントを作成する](create-account-cli-how-to.md) <br/>リソース グループ名と Media Services アカウント名を覚えておいてください。 
- [Azure Media Services REST API を呼び出すように Postman を構成します](media-rest-apis-with-postman.md)。<br/>トピック「[Azure AD トークンを取得する](media-rest-apis-with-postman.md#get-azure-ad-token)」の最後の手順を必ず実行してください。 

## <a name="define-a-custom-preset"></a>カスタム プリセットを定義する

次の例では、新しい変換の要求本文を定義しています。 この変換が使用されたときに生成される一連の出力を定義します。 

この例では、まずオーディオ エンコードのための AacAudio レイヤーと、ビデオ エンコードのための 2 つの H264Video レイヤーを追加します。 ビデオ レイヤーでは、出力ファイル名で使用できるようにラベルを割り当てます。 次に、出力にサムネイルも含めます。 次の例では、入力ビデオの解像度の 50%、および 3 つのタイムスタンプ (入力ビデオの長さの {25%, 50%, 75}) で生成された、PNG 形式の画像を指定します。 最後に、出力ファイルの形式 (ビデオとオーディオの形式とサムネイルの形式) を指定します。 複数の H264Layer があるため、レイヤーごとに一意の名前を生成するマクロを使用する必要があります。 `{Label}` または `{Bitrate}` のいずれかのマクロを使用できます。この例では、前者が示されています。

```json
{
    "properties": {
        "description": "Basic Transform using a custom encoding preset",
        "outputs": [
            {
                "onError": "StopProcessingJob",
                "relativePriority": "Normal",
                "preset": {
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
            }
        ]
    }
}

```

## <a name="create-a-new-transform"></a>新しい変換を作成する  

この例では、先ほど定義したカスタム プリセットに基づく**変換**を作成します。 変換を作成するときは、まず [Get](https://docs.microsoft.com/rest/api/media/transforms/get) を使用して、変換がすでに存在しているかどうかを確認する必要があります。 変換が存在する場合はそれを再利用します。 

ダウンロードした Postman のコレクションで、 **[Transforms and Jobs]\(変換とジョブ\)** -> **[Create or Update Transform]\(変換の作成または更新\)** を選択します。

**PUT** の HTTP 要求メソッドは、以下のようになります。

```
PUT https://management.azure.com/subscriptions/:subscriptionId/resourceGroups/:resourceGroupName/providers/Microsoft.Media/mediaServices/:accountName/transforms/:transformName?api-version={{api-version}}
```

**[Body]\(本文\)** タブを選択し、本文を[先ほど定義した](#define-a-custom-preset) JSON コードに置き換えます。 Media Services が、指定されたビデオまたはオーディオに変換を適用するためには、その変換に基づいてジョブを送信する必要があります。

**[送信]** を選択します。 

Media Services が、指定されたビデオまたはオーディオに変換を適用するためには、その変換に基づいてジョブを送信する必要があります。 変換に基づいてジョブを送信する方法を示す完全な例については、[ビデオ ファイルのストリーム配信 (REST) に関するチュートリアル](stream-files-tutorial-with-rest.md)をご覧ください。

## <a name="next-steps"></a>次の手順

[他の REST 操作](https://docs.microsoft.com/rest/api/media/)を参照する
