---
title: Media Encoder Standard でビデオをトリミングする方法 - Azure | Microsoft Docs
description: トリミングは、ビデオ フレーム内で四角形のウィンドウを選択して、そのウィンドウ内のピクセルだけをエンコードするプロセスです。 この記事では、Media Encoder Standard を使ってビデオをトリミングする方法について説明します。
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 03/10/2021
ms.author: anilmur
ms.reviewer: juliako
ms.openlocfilehash: 6a15e57884f85e2d56b77c3e5eec1267133fe96d
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "103016731"
---
# <a name="crop-videos-with-media-encoder-standard"></a>Media Encoder Standard を使用してビデオをトリミングする

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)] 

Media Encoder Standard (MES) をすると、入力ビデオをトリミングできます。 トリミングは、ビデオ フレーム内で四角形のウィンドウを選択して、そのウィンドウ内のピクセルだけをエンコードするプロセスです。 次の図は、そのプロセスを示しています。

![ビデオをトリミングする](./media/media-services-crop-video/media-services-crop-video01.png)

たとえば、解像度が 1920 x 1080 ピクセル (アスペクト比は 16:9) の入力ビデオがあるとします。このビデオの左右には黒いバー (ピラー ボックス) があるため、アクティブなビデオが含まれるのは、4:3 ウィンドウ、つまり 1440 x 1080 ピクセルの範囲内だけです。 MES を使用すると、トリミングまたは編集により黒いバーを除外して、1440 x 1080 領域をエンコードできます。

MES でのトリミングは前処理段階であるため、エンコード プリセットのトリミング パラメーターは元の入力ビデオに適用されます。 エンコードは後処理段階です。幅/高さの設定は、元のビデオではなく *"前処理された"* ビデオに適用されます。 プリセットを設計するときに、(a) 元の入力ビデオに基づいてトリミング パラメーターを選択し、(b) トリミングされたビデオに基づいてエンコード設定を選択する必要があります。 エンコード設定とトリミングされたビデオが対応しないと、意図したとおりに出力されません。

[次](media-services-custom-mes-presets-with-dotnet.md#encoding_with_dotnet) のトピックは、MES でエンコード ジョブを作成する方法、およびエンコード タスクに対してカスタム プリセットを指定する方法を示しています。 

## <a name="creating-a-custom-preset"></a>カスタム プリセットの作成
図で示した例:

1. 元の入力は 1920 x 1080 です
2. この入力フレームの中央部分をトリミングして、1440 x 1080 に出力する必要があります
3. つまり、X オフセットは (1920 – 1440) ÷ 2 = 240 で、Y オフセットはゼロです
4. トリミングする四角形の幅さと高さはそれぞれ 1440 と 1080 です
5. エンコード段階で求められるのは、解像度が 1440 x 1080、960 x 720、480 x 360 の 3 つの層を作成することです

### <a name="json-preset"></a>JSON プリセット

```json
{
  "Version": 1.0,
  "Sources": [
    {
      "Streams": [],
      "Filters": {
        "Crop": {
          "X": 240,
          "Y": 0,
          "Width": 1440,
          "Height": 1080
        }
      },
      "Pad": true
    }
  ],
  "Codecs": [
    {
      "KeyFrameInterval": "00:00:02",
      "H264Layers": [
        {
          "Profile": "Auto",
          "Level": "auto",
          "Bitrate": 3400,
          "MaxBitrate": 3400,
          "BufferWindow": "00:00:05",
          "Width": 1440,
          "Height": 1080,
          "BFrames": 3,
          "ReferenceFrames": 3,
          "AdaptiveBFrame": true,
          "Type": "H264Layer",
          "FrameRate": "0/1"
        },
        {
          "Profile": "Auto",
          "Level": "auto",
          "Bitrate": 2250,
          "MaxBitrate": 2250,
          "BufferWindow": "00:00:05",
          "Width": 960,
          "Height": 720,
          "BFrames": 3,
          "ReferenceFrames": 3,
          "AdaptiveBFrame": true,
          "Type": "H264Layer",
          "FrameRate": "0/1"
        },
        {
          "Profile": "Auto",
          "Level": "auto",
          "Bitrate": 1250,
          "MaxBitrate": 1250,
          "BufferWindow": "00:00:05",
          "Width": 480,
          "Height": 360,
          "BFrames": 3,
          "ReferenceFrames": 3,
          "AdaptiveBFrame": true,
          "Type": "H264Layer",
          "FrameRate": "0/1"
        }
      ],
      "Type": "H264Video"
    },
    {
      "Profile": "AACLC",
      "Channels": 2,
      "SamplingRate": 48000,
      "Bitrate": 128,
      "Type": "AACAudio"
    }
  ],
  "Outputs": [
    {
      "FileName": "{Basename}_{Width}x{Height}_{VideoBitrate}.mp4",
      "Format": {
        "Type": "MP4Format"
      }
    }
  ]
}
```

## <a name="restrictions-on-cropping"></a>トリミングに関する制限事項
トリミングは手動で行います。 入力ビデオでさまざまな編集を行うには、適切な編集ツールに読み込む必要があります。そのツールを使用して、目的のフレームを選択したり、カーソルを配置して、四角形をトリミングするためのオフセットや、その特定のビデオ用に調整されたエンコード プリセットを決定したりできます。この機能の目的は、入力ビデオの黒いレターボックス/ピラーボックスの自動検出、枠線削除などを有効にすることではありません。

トリミング機能には次の制限が適用されます。 これが満たされない場合、エンコード タスクが失敗したり、予期しない出力が生成されたりすることがあります。

1. トリミングする四角形の座標とサイズは入力ビデオ内に収まっている必要あります
2. 前述のように、エンコード設定の幅と高さは、トリミングされたビデオに対応していなければなりません
3. トリミングは横モードでキャプチャされたビデオに適用されます (つまり、垂直方向、つまり縦モードのスマートフォンで録画されたビデオには適用されません)
4. 正方形ピクセルでキャプチャされたプログレッシブ ビデオに最適です

## <a name="provide-feedback"></a>フィードバックの提供
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="next-step"></a>次のステップ
Azure Media Services のラーニング パスをご覧ください。AMS で提供される優れた機能の学習に役立ちます。  

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]
