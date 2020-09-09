---
title: REST で Azure Media Services Encoder Standard を使用してサムネイルを生成する方法
description: この記事では、REST で Media Encoder Standard を使用して、資産をエンコードするのと同時にサムネイルを生成する方法を説明します。
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 08/31/2020
ms.author: inhenkel
ms.openlocfilehash: af320b94950d5999b6dd181b7a8e0eb198088e98
ms.sourcegitcommit: bcda98171d6e81795e723e525f81e6235f044e52
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/01/2020
ms.locfileid: "89267634"
---
# <a name="how-to-generate-thumbnails-using-encoder-standard-with-rest"></a>REST で Encoder Standard を使用してサムネイルを生成する方法

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Media Encoder Standard を使用してビデオ入力から 1 つまたは複数の [JPEG](https://en.wikipedia.org/wiki/JPEG)、[PNG](https://en.wikipedia.org/wiki/Portable_Network_Graphics)、または [BMP](https://en.wikipedia.org/wiki/BMP_file_format) 画像ファイル形式のサムネイルを生成することができます。

## <a name="recommended-reading-and-practice"></a>推奨される資料とプラクティス

「[カスタム変換を使用してエンコードする方法 - REST](custom-preset-rest-howto.md)」を参照して、カスタム変換について理解しておくことをお勧めします。

## <a name="thumbnail-parameters"></a>サムネイルのパラメーター

次のパラメーターを設定する必要があります。

- **start** - サムネイルの生成を開始する入力ビデオ内の位置。 この値は、ISO 8601 形式 (たとえば、PT05S の場合は 5 秒で開始する)、フレーム数 (たとえば、10 の場合は 10 番目のフレームで開始する)、またはストリーム期間に対する相対値 (たとえば、10% の場合はストリーム期間の 10% で開始する) とすることができます。 また、マクロ {Best} もサポートされています。これにより、ビデオの最初の数秒から最適なサムネイルを選択するようにエンコーダーに指示が送られ、ステップと範囲の他の設定に関係なく、1 つのサムネイルのみが生成されることになります。 既定値はマクロ {Best} です。
- **step** - サムネイルが生成される間隔。 この値は、ISO 8601 形式 (たとえば、PT05S の場合は 5 秒ごとに 1 つの画像)、フレーム数 (たとえば、30 の場合は 30 フレームごとに 1 つの画像)、またはストリーム期間に対する相対値 (たとえば、10% の場合はストリーム期間の 10% ごとに 1 つの画像) とすることができます。 step 値は最初に生成されたサムネイルに影響します。これは、変換のプリセットの開始時に指定されたものと厳密に一致しない場合があります。 これは、開始時刻と開始時刻からのステップ位置との間で最適なサムネイルを最初の出力として選択しようと試みるエンコーダーに原因があります。 既定値は 10% であるため、ストリームの期間が長い場合は、最初に生成されるサムネイルが開始時刻に指定されているものより離れている可能性があります。 最初のサムネイルが開始時刻に近いことが予想される場合は、step に対して妥当な値を選択してみてください。また、開始時刻にサムネイルが 1 つだけ必要な場合は、range 値を 1 に設定してみてください。
- **range** - 入力ビデオ内の変換のプリセットの開始時刻を基準とする位置であり、そこでサムネイルの生成が停止されます。 この値は、ISO 8601 形式 (たとえば、PT5M30S の場合は開始時刻から 5 分 30 秒で停止する)、またはフレーム数 (たとえば、300 の場合は開始時刻のフレームから 300 番目のフレームで停止する。 この値が 1 の場合は、開始時刻にサムネイルを 1 つだけ生成することを意味します)、またはストリーム期間に対する相対値 (たとえば、50% の場合は開始時刻から半分のストリーム期間で停止する) とすることができます。 既定値は 100% です。これは、ストリームの末尾で停止することを意味します。
- **layers** - エンコーダーによって生成される出力画像レイヤーのコレクション。

## <a name="example-of-a-single-png-file-preset"></a>"単一の PNG ファイル" プリセットの例

次の JSON プリセットを使用して、最初の数秒の入力ビデオから 1 つの出力 PNG ファイルを生成することができます。エンコーダーは、"興味深い" フレームを見つけ出すためにベストエフォートを試行します。 出力画像のサイズは 100% に設定され、これらは入力ビデオのサイズと一致しすることを意味します。 "Codecs" セクションでの "PngLayers" の使用と一致させるために "Outputs" でどのような "Format" 設定が必要かにも注意してください。  

```json
{
    "properties": {
        "description": "Basic Transform using a custom encoding preset for thumbnails",
        "outputs": [
            {
                "onError": "StopProcessingJob",
                "relativePriority": "Normal",
                "preset": {
                    "@odata.type": "#Microsoft.Media.StandardEncoderPreset",
                    "codecs": [
                        {
                            "@odata.type": "#Microsoft.Media.PngImage",
                            "stretchMode": "AutoSize",
                            "start": "{Best}",
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

## <a name="example-of-a-series-of-jpeg-images-preset"></a>"一連の JPEG 画像" プリセットの例

次の JSON プリセットは、入力タイムラインのタイムスタンプ、5%、15%、…、95% で 10 個の画像のセットを生成するために使用できます。画像のサイズは入力ビデオの 4 分の 1 になるように指定されます。

### <a name="json-preset"></a>JSON プリセット

```json
{
  "Version": 1.0,
  "Codecs": [
    {
      "JpgLayers": [
        {
          "Quality": 90,
          "Type": "JpgLayer",
          "Width": "25%",
          "Height": "25%"
        }
      ],
      "Start": "5%",
      "Step": "10%",
      "Range": "96%",
      "Type": "JpgImage"
    }
  ],
  "Outputs": [
    {
      "FileName": "{Basename}_{Index}{Extension}",
      "Format": {
        "Type": "JpgFormat"
      }
    }
  ]
}
```

## <a name="example-of-a-one-image-at-a-specific-timestamp-preset"></a>"特定のタイムスタンプで 1 つの画像" プリセットの例

次の JSON プリセットは、入力ビデオの 30 秒の目盛りで 1 つの JPEG 画像を生成するために使用できます。 このプリセットは、入力ビデオの時間が 30 秒を超えることを想定しています (それ以外の場合、ジョブは失敗します)。

### <a name="json-preset"></a>JSON プリセット

```json
{
  "Version": 1.0,
  "Codecs": [
    {
      "JpgLayers": [
        {
          "Quality": 90,
          "Type": "JpgLayer",
          "Width": "25%",
          "Height": "25%"
        }
      ],
      "Start": "00:00:30",
      "Step": "1",
      "Range": "1",
      "Type": "JpgImage"
    }
  ],
  "Outputs": [
    {
      "FileName": "{Basename}_{Index}{Extension}",
      "Format": {
        "Type": "JpgFormat"
      }
    }
  ]
}
```

## <a name="example-of-a-thumbnails-at-different-resolutions-preset"></a>"さまざまな解像度のサムネイル" プリセットの例

次のプリセットは、1 つのタスクでさまざまな解像度のサムネイルを生成するために使用できます。 例では、入力タイムラインの位置、5%、15%、…、95%でエンコーダーにより 2 個の画像 (入力ビデオ解像度が 100% の画像と 50% の画像) が生成されます。

FileName で {Resolution} マクロを使用すると、出力画像のファイル名を生成中にプリセットの "エンコード" セクションで指定した幅と高さを使用するエンコーダーを特定できます。 これにより、さまざまな画像を簡単に識別することもできます。

### <a name="json-preset"></a>JSON プリセット

```json
{
  "Version": 1.0,
  "Codecs": [
    {
      "JpgLayers": [
{
  "Quality": 90,
  "Type": "JpgLayer",
  "Width": "100%",
  "Height": "100%"
},
{
  "Quality": 90,
  "Type": "JpgLayer",
  "Width": "50%",
  "Height": "50%"
}

      ],
      "Start": "5%",
      "Step": "10%",
      "Range": "96%",
      "Type": "JpgImage"
    }
  ],
  "Outputs": [
    {
      "FileName": "{Basename}_{Resolution}_{Index}{Extension}",
      "Format": {
"Type": "JpgFormat"
      }
    }
  ]
}
```

## <a name="example-of-generating-a-thumbnail-while-encoding"></a>エンコード中のサムネイルの生成の例

上記の例ではすべて、画像のみを生成するエンコード タスクを送信する方法を説明していますが、ビデオ/オーディオ エンコードをサムネイルの生成と組み合わせることもできます。 次の JSON プリセットは、Encoder Standard にエンコード中にサムネイルを生成するよう指示します。

### <a name="json-preset"></a>JSON プリセット

スキーマの詳細については、[こちら](/azure/media-services/previous/media-services-mes-schema)の記事をご覧ください。

```json
{
  "Version": 1.0,
  "Codecs": [
    {
      "KeyFrameInterval": "00:00:02",
      "SceneChangeDetection": "true",
      "H264Layers": [
        {
          "Profile": "Auto",
          "Level": "auto",
          "Bitrate": 4500,
          "MaxBitrate": 4500,
          "BufferWindow": "00:00:05",
          "Width": 1280,
          "Height": 720,
          "ReferenceFrames": 3,
          "EntropyMode": "Cabac",
          "AdaptiveBFrame": true,
          "Type": "H264Layer",
          "FrameRate": "0/1"

        }
      ],
      "Type": "H264Video"
    },
    {
      "JpgLayers": [
        {
          "Quality": 90,
          "Type": "JpgLayer",
          "Width": "100%",
          "Height": "100%"
        }
      ],
      "Start": "{Best}",
      "Type": "JpgImage"
    },
    {
      "Channels": 2,
      "SamplingRate": 48000,
      "Bitrate": 128,
      "Type": "AACAudio"
    }
  ],
  "Outputs": [
    {
      "FileName": "{Basename}_{Index}{Extension}",
      "Format": {
        "Type": "JpgFormat"
      }
    },
    {
      "FileName": "{Basename}_{Resolution}_{VideoBitrate}.mp4",
      "Format": {
        "Type": "MP4Format"
      }
    }
  ]
}
```

## <a name="next-steps"></a>次の手順
[.NET を使用してサムネイルを生成する](media-services-generate-thumbnails-dotnet.md)
