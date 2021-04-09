---
title: Azure Media Services v3 入力メタデータのスキーマ
description: この記事では、Azure Media Services v3 入力メタデータのスキーマの概要を説明します。
author: IngridAtMicrosoft
manager: femila
editor: ''
services: media-services
documentationcenter: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: reference
ms.date: 08/31/2020
ms.author: inhenkel
ms.openlocfilehash: 0acb882410d103cf6f6c34bbecf2006094437b04
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102634687"
---
# <a name="input-metadata"></a>入力メタデータ

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

エンコード ジョブは、エンコーディング タスクを実行する入力資産に関連付けられています。  タスクが完了すると、出力資産が生成されます。 出力資産には、ビデオ、オーディオ、サムネイル、マニフェスト、およびその他のファイルが含まれます。 

この出力資産には、入力資産に関するメタデータが格納されたファイルも含まれます。 メタデータ JSON ファイルの名前にはランダムな ID が含まれており、それを使用して、出力資産が属する入力資産を特定しないでください。 属している入力資産を特定するには、`Uri` フィールドを使用します (詳細については、「[その他の子要素](#other-child-elements)」を参照してください)。  

Media Services では、メタデータを生成するために入力資産は事前にスキャンされません。 入力メタデータは、入力資産がジョブで処理されるときに、成果物としてのみ生成されます。 そのため、この成果物は、出力資産に書き込まれます。 入力資産と出力資産のメタデータの生成には、異なるツールが使用されます。 したがって、入力メタデータには、出力メタデータとは若干異なるスキーマがあります。

この記事では、入力メタデータ (&lt;asset_id&gt;_metadata.json) が基づく JSON スキーマの要素と種類について説明します。 出力資産に関するメタデータを含むファイルについては、「[出力メタデータ](output-metadata-schema.md)」を参照してください。  

JSON スキーマ の例は、この記事の最後に記載されています。  

## <a name="assetfile"></a>AssetFile  

エンコード ジョブの AssetFile 要素のコレクションが含まれます。  

> [!NOTE]
> 次の 4 つの子要素は順番に表示されます。

| 名前  | 説明 |
| --- | --- | 
| **VideoTracks**|各物理資産ファイルには、適切なコンテナー形式にインターリーブされる 0 個以上のビデオ トラックを含めることができます。 詳細については、「[VideoTracks](#videotracks)」を参照してください。 |
| **AudioTracks**|各物理資産ファイルには、適切なコンテナー形式にインターリーブされる 0 個以上のオーディオ トラックを含めることができます。 詳細については、「[AudioTracks](#audiotracks)」を参照してください。 |
| **Metadata**  |キー\値文字列として表される資産ファイルのメタデータ。 <br />例: `<Metadata key="language" value="eng" />` |

### <a name="other-child-elements"></a>その他の子要素

| 名前 | 説明 |
| --- | --- |
| **名前**<br />必須 |資産ファイルの名前。 <br /><br />例: `"Name": "Ignite-short.mp4"` |
| **Uri**<br />必須 |入力資産が配置されている URL。 出力資産が属している入力資産を特定するには、ID ではなく `Uri` フィールドを使用します。|
| **[サイズ]**<br />必須 |資産ファイルのサイズ (バイト単位)。  <br /><br />例: `"Size": 75739259`|
| **Duration**<br />必須 |コンテンツの再生時間。 <br /><br />例: `"Duration": "PT1M10.304S"`. |
| **NumberOfStreams**<br />必須 |資産ファイル内のストリーム数。  <br /><br />例: `"NumberOfStreams": 2`|
| **FormatNames**<br />必須 |形式の名前。  <br /><br />例: `"FormatNames": "mov,mp4,m4a,3gp,3g2,mj2"`|
| **FormatVerboseName**<br /> 必須 |形式の詳細な名前。 <br /><br />例: `"FormatVerboseName": "QuickTime / MOV"` |
| **StartTime** |コンテンツの開始時刻。  <br /><br />例: `"StartTime": "PT0S"` |
| **OverallBitRate** |資産ファイルの平均ビットレート (ビット/秒)。  <br /><br />例: `"OverallBitRate": 8618539`|

## <a name="videotracks"></a>VideoTracks

| 名前 | 説明 |
| --- | --- |
| **FourCC**<br />必須 |ffmpeg によって報告されるビデオ コーデックの FourCC コード。<br /><br />例: `"FourCC": "avc1" | "hev1" | "hvc1"` |
| **プロファイル** |ビデオ トラックのプロファイル。 <br /><br />例: `"Profile": "Main"`|
| **Level** |ビデオ トラックのレベル。 <br /><br />例: `"Level": "3.2"`|
| **PixelFormat** |ビデオ トラックのピクセル形式。 <br /><br />例: `"PixelFormat": "yuv420p"`|
| **Width**<br />必須 |エンコードされたビデオの幅 (ピクセル単位)。 <br /><br />例: `"Width": "1280"`|
| **Height**<br />必須 |エンコードされたビデオの高さ (ピクセル単位)。<br /><br />例: `"Height": "720"` |
| **DisplayAspectRatioNumerator**<br />必須 |ビデオ ディスプレイの縦横比の分子。<br /><br />例: `"DisplayAspectRatioNumerator": 16.0` |
| **DisplayAspectRatioDenominator**<br />必須 |ビデオ ディスプレイの縦横比の分母。 <br /><br />例: `"DisplayAspectRatioDenominator": 9.0`|
| **SampleAspectRatioNumerator** |ビデオ サンプルの縦横比の分子。 <br /><br />例: `"SampleAspectRatioNumerator": 1.0`|
| **SampleAspectRatioDenominator**|例: `"SampleAspectRatioDenominator": 1.0`|
| **FrameRate**<br />必須 |.3f 形式の測定されたビデオ フレーム レート。 <br /><br />例: `"FrameRate": 29.970`|
| **Bitrate** |資産ファイルから計算された平均ビデオ ビット レート (ビット/秒)。 基本ストリーム ペイロードのみがカウントされます。パッケージのオーバーヘッドは含まれません。 <br /><br />例: `"Bitrate": 8421583`|
| **HasBFrames** |B フレームのビデオ トラック数。 <br /><br />例: `"HasBFrames": 2`|
| **Metadata** |さまざまな情報を保持する際に使用できる汎用的なキー/値文字列。 <br />この記事の最後にある完全な例を参照してください。 |
| **Id**<br />必須 |このオーディオまたはビデオ トラックの 0 から始まるインデックス。<br /><br /> この **Id** は、必ずしも MP4 ファイルで使用されている TrackID であるとは限りません。 <br /><br />例: `"Id": 2`|
| **Codec** |ビデオ トラック コーデック文字列。 <br /><br />例: `"Codec": "h264 | hev1"`|
| **CodecLongName** |オーディオまたはビデオ トラック コーデックの長い名前。 <br /><br />例: `"CodecLongName": "H.264 / AVC / MPEG-4 AVC / MPEG-4 part 10"`|
| **Codec** |ビデオ トラック コーデック文字列。 <br /><br />例: `"Codec": "h264 | hev1"`|
| **TimeBase**<br />必須 |時間ベース。<br /><br />例: `"TimeBase": "1/30000"`|
| **NumberOfFrames** |フレームの数。 <br /><br />例: `"NumberOfFrames": 2107`|
| **StartTime** |トラックの開始時刻。<br /><br />例: `"StartTime": "PT0.033S"` |
| **Duration** |トラックの時間。 <br /><br />例: `"Duration": "PT1M10.304S"`|

## <a name="audiotracks"></a>AudioTracks

| 名前  | 説明 |
| --- | --- | 
| **SampleFormat** |サンプル形式。 <br /><br />例: `"SampleFormat": "fltp"`|
| **ChannelLayout** |チャネル レイアウト。 <br /><br />例: `"ChannelLayout": "stereo"`|
| **Channels**<br />必須 |オーディオ チャネルの数 (0 以上)。 <br /><br />例: `"Channels": 2`|
| **SamplingRate**<br />必須 |オーディオ サンプリング レート (サンプル数/秒または Hz)。 <br /><br />例: `"SamplingRate": 48000`|
| **Bitrate** |資産ファイルから計算された、平均オーディオ ビット レート (ビット/秒)。 基本ストリーム ペイロードのみがカウントされます。この値には、パッケージのオーバーヘッドは含まれません。 <br /><br />例: `"Bitrate": 192080`|
| **Metadata** |さまざまな情報を保持する際に使用できる汎用的なキー/値文字列。  <br />この記事の最後にある完全な例を参照してください。 |
| **Id**<br />必須 |このオーディオまたはビデオ トラックの 0 から始まるインデックス。<br /><br /> 必ずしも MP4 ファイルで使用されている TrackID であるとは限りません。 <br /><br />例: `"Id": 1`|
| **Codec** |ビデオ トラック コーデック文字列。 <br /><br />例: `"Codec": "aac"`|
| **CodecLongName** |オーディオまたはビデオ トラック コーデックの長い名前。 <br /><br />例: `"CodecLongName": "AAC (Advanced Audio Coding)"`|
| **TimeBase**<br />必須 |時間ベース。<br /><br />例: `"TimeBase": "1/48000"` |
| **NumberOfFrames** |フレームの数。 <br /><br />例: `"NumberOfFrames": 3294`|
| **StartTime** |トラックの開始時刻。 詳細については、「[ISO 8601](https://www.iso.org/iso-8601-date-and-time-format.html)」を参照してください。 <br /><br />例: `"StartTime": "PT0S"` |
| **Duration** |トラックの時間。 <br /><br />例: `"Duration": "PT1M10.272S"` |

## <a name="metadata"></a>Metadata

| 名前 | 説明 |
| --- | --- |
| **key**<br />必須 |キー/値ペアのキー。 |
| **value**<br /> 必須 |キー/値ペアの値。 |

## <a name="schema-example"></a>スキーマの例

```json
{
  "AssetFile": [
    {
      "VideoTracks": [
        {
          "FourCC": "avc1",
          "Profile": "Main",
          "Level": "3.2",
          "PixelFormat": "yuv420p",
          "Width": "1280",
          "Height": "720",
          "DisplayAspectRatioNumerator": 16.0,
          "DisplayAspectRatioDenominator": 9.0,
          "SampleAspectRatioNumerator": 1.0,
          "SampleAspectRatioNumeratorSpecified": true,
          "SampleAspectRatioDenominator": 1.0,
          "SampleAspectRatioDenominatorSpecified": true,
          "FrameRate": 29.970,
          "Bitrate": 8421583,
          "BitrateSpecified": true,
          "HasBFrames": 2,
          "HasBFramesSpecified": true,
          "Disposition": {
            "Default": 1
          },
          "Metadata": [
            {
              "key": "creation_time",
              "value": "2018-02-21T21:42:08.000000Z"
            },
            {
              "key": "language",
              "value": "eng"
            },
            {
              "key": "handler_name",
              "value": "Video Media Handler"
            },
            {
              "key": "encoder",
              "value": "AVC Coding"
            }
          ],
          "Id": 2,
          "Codec": "h264",
          "CodecLongName": "H.264 / AVC / MPEG-4 AVC / MPEG-4 part 10",
          "TimeBase": "1/30000",
          "NumberOfFrames": 2107,
          "NumberOfFramesSpecified": true,
          "StartTime": "PT0.033S",
          "Duration": "PT1M10.304S"
        }
      ],
      "AudioTracks": [
        {
          "SampleFormat": "fltp",
          "ChannelLayout": "stereo",
          "Channels": 2,
          "SamplingRate": 48000,
          "Bitrate": 192080,
          "BitrateSpecified": true,
          "BitsPerSampleSpecified": true,
          "Disposition": {
            "Default": 1
          },
          "Metadata": [
            {
              "key": "creation_time",
              "value": "2018-02-21T21:42:08.000000Z"
            },
            {
              "key": "language",
              "value": "eng"
            },
            {
              "key": "handler_name",
              "value": "Sound Media Handler"
            }
          ],
          "Id": 1,
          "Codec": "aac",
          "CodecLongName": "AAC (Advanced Audio Coding)",
          "TimeBase": "1/48000",
          "NumberOfFrames": 3294,
          "NumberOfFramesSpecified": true,
          "StartTime": "PT0S",
          "Duration": "PT1M10.272S"
        }
      ],
      "Metadata": [
        {
          "key": "major_brand",
          "value": "mp42"
        },
        {
          "key": "minor_version",
          "value": "19529854"
        },
        {
          "key": "compatible_brands",
          "value": "mp42isom"
        },
        {
          "key": "creation_time",
          "value": "2018-02-21T21:42:08.000000Z"
        }
      ],
      "Name": "Ignite-short.mp4",
      "Uri": "https://amsstorageacct.blob.core.windows.net/asset-00000000-0000-0000-000000000000/ignite.mp4",
      "Size": 75739259,
      "Duration": "PT1M10.304S",
      "NumberOfStreams": 2,
      "FormatNames": "mov,mp4,m4a,3gp,3g2,mj2",
      "FormatVerboseName": "QuickTime / MOV",
      "StartTime": "PT0S",
      "OverallBitRate": 8618539,
      "OverallBitRateSpecified": true
    }
  ]
}
```

## <a name="next-steps"></a>次のステップ

[出力メタデータ](output-metadata-schema.md)
