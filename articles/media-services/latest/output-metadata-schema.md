---
title: Azure Media Services 出力メタデータのスキーマ
description: この記事では、Azure Media Services v3 出力メタデータのスキーマの概要を説明します。
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
ms.openlocfilehash: f26905366949c2c198e52f78bc7adb734cbb7f90
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102612458"
---
# <a name="output-metadata"></a>出力メタデータ

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

エンコード ジョブは、エンコーディング タスクを実行する入力資産に関連付けられています。 たとえば、MP4 ファイルを H.264 MP4 アダプティブ ビットレート セットにエンコードし、サムネイルを作成し、オーバーレイを作成します。 タスクが完了すると、出力資産が生成されます。  出力資産には、ビデオ、オーディオ、サムネイル、およびその他のファイルが含まれます。 出力資産には、出力資産に関するメタデータが格納されたファイルも含まれます。 メタデータ JSON ファイルの名前は、`<source_file_name>_manifest.json` (`BigBuckBunny_manifest.json` など) になります。 ソース ファイル名 (切り詰めなし) を見つけるには、すべての *_metadata.json をスキャンして、その中のファイルパス文字列のクエリを実行する必要があります。

Media Services では、メタデータを生成するために入力資産は事前にスキャンされません。 入力メタデータは、入力資産がジョブで処理されるときに、成果物としてのみ生成されます。 そのため、この成果物は、出力資産に書き込まれます。 入力資産と出力資産のメタデータの生成には、異なるツールが使用されます。 したがって、入力メタデータには、出力メタデータとは若干異なるスキーマがあります。

この記事では、出力メタデータ (&lt;source_file_name&gt;_manifest.json) が基づく JSON スキーマの要素と種類について説明します。 <!--For information about the file that contains metadata about the input asset, see [Input metadata](input-metadata-schema.md).  -->

完全なスキーマ コードと JSON の例は、この記事の最後で紹介します。  

## <a name="assetfile"></a>AssetFile

エンコード ジョブの AssetFile エントリのコレクション。  

| 名前 | 説明 |
| --- | --- |
| **Sources** |この AssetFile を生成するために処理された入力/ソース メディア ファイルのコレクション。<br />例: `"Sources": [{"Name": "Ignite-short_1280x720_AACAudio_3551.mp4"}]`|
| **VideoTracks**|各物理 AssetFile には、適切なコンテナー形式にインターリーブされる 0 個以上のビデオ トラックを含めることができます。 <br />「[VideoTracks](#videotracks)」を参照してください。 |
| **AudioTracks**|各物理 AssetFile には、適切なコンテナー形式にインターリーブされる 0 個以上のオーディオ トラックを含めることができます。 これは、そのオーディオ トラックすべてのコレクションです。<br /> 詳細については、「[AudioTracks](#audiotracks)」を参照してください。 |
| **名前**<br />必須 |メディア資産ファイルの名前。 <br /><br />例: `"Name": "Ignite-short_1280x720_AACAudio_3551.mp4"`|
| **[サイズ]**<br />必須 |資産ファイルのサイズ (バイト単位)。 <br /><br />例: `"Size": 32414631`|
| **Duration**<br />必須 |コンテンツの再生時間。 詳細については、[ISO8601](https://www.iso.org/iso-8601-date-and-time-format.html) 形式を参照してください。 <br /><br />例: `"Duration": "PT1M10.315S"`|

## <a name="videotracks"></a>VideoTracks 

各物理 AssetFile には、適切なコンテナー形式にインターリーブされる 0 個以上のビデオ トラックを含めることができます。 **VideoTracks** 要素は、すべてのビデオ トラックのコレクションを表します。  

| 名前 | 説明 |
| --- | --- |
| **Id**<br /> 必須 |このビデオ トラックの 0 から始まるインデックス。**注:** この **Id** は、必ずしも MP4 ファイルで使用されている TrackID であるとは限りません。 <br /><br />例: `"Id": 1`|
| **FourCC**<br />必須 | ffmpeg によって報告されるビデオ コーデックの FourCC コード。  <br /><br />例: `"FourCC": "avc1" | "hev1" | "hvc1"`|
| **プロファイル** |H264 プロファイル (H264 コーデックのみに適用されます)  <br /><br />例: `"Profile": "High"` |
| **Level** |H264 レベル (H264 コーデックのみに適用されます)。  <br /><br />例: `"Level": "3.2"`|
| **Width**<br />必須 |エンコードされたビデオの幅 (ピクセル単位)。  <br /><br />例: `"Width": "1280"`|
| **Height**<br />必須 |エンコードされたビデオの高さ (ピクセル単位)。  <br /><br />例: `"Height": "720"`|
| **DisplayAspectRatioNumerator**<br />必須|ビデオ ディスプレイの縦横比の分子。  <br /><br />例: `"DisplayAspectRatioNumerator": 16.0`|
| **DisplayAspectRatioDenominator**<br />必須 |ビデオ ディスプレイの縦横比の分母。  <br /><br />例: `"DisplayAspectRatioDenominator": 9.0`|
| **FrameRate**<br />必須 |.3f 形式の測定されたビデオ フレーム レート。  <br /><br />例: `"Framerate": 29.970`|
| **Bitrate**<br />必須 |AssetFile から計算された平均ビデオ ビット レート (ビット/秒)。 基本ストリーム ペイロードのみがカウントされます。パッケージのオーバーヘッドは含まれません。  <br /><br />例: `"Bitrate": 3551567`|
| **TargetBitrate**<br />必須 |エンコード プリセットを使用して要求された、このビデオ トラックのターゲット平均ビットレート (ビット/秒)。 <br /><br />例: `"TargetBitrate": 3520000` |

## <a name="audiotracks"></a>AudioTracks 

各物理 AssetFile には、適切なコンテナー形式にインターリーブされる 0 個以上のオーディオ トラックを含めることができます。 **AudioTracks** 要素は、すべてのオーディオ トラックのコレクションを表します。  

| 名前  | 説明 |
| --- | --- |
| **Id**<br />必須  |このオーディオ トラックの 0 から始まるインデックス。**注:** これは、必ずしも MP4 ファイルで使用されている TrackID であるとは限りません。  <br /><br />例: `"Id": 2`|
| **Codec**  |オーディオ トラック コーデック文字列。  <br /><br />例: `"Codec": "aac"`|
| **Language**|例: `"Language": "eng"`|
| **Channels**<br />必須|オーディオ チャネルの数。  <br /><br />例: `"Channels": 2`|
| **SamplingRate**<br />必須 |オーディオ サンプリング レート (サンプル数/秒または Hz)。  <br /><br />例: `"SamplingRate": 48000`|
| **Bitrate**<br />必須 |AssetFile から計算された、平均オーディオ ビット レート (ビット/秒)。 基本ストリーム ペイロードのみがカウントされます。パッケージのオーバーヘッドは含まれません。  <br /><br />例: `"Bitrate": 128041`|

## <a name="json-schema-example"></a>JSON スキーマの例

```json
{
  "AssetFile": [
    {
      "Sources": [
        {
          "Name": "Ignite-short_1280x720_AACAudio_3551.mp4"
        }
      ],
      "VideoTracks": [
        {
          "Id": 1,
          "FourCC": "avc1",
          "Profile": "High",
          "Level": "3.2",
          "Width": "1280",
          "Height": "720",
          "DisplayAspectRatioNumerator": 16.0,
          "DisplayAspectRatioDenominator": 9.0,
          "Framerate": 29.970,
          "Bitrate": 3551567,
          "TargetBitrate": 3520000
        }
      ],
      "AudioTracks": [
        {
          "Id": 2,
          "Codec": "aac",
          "Language": "eng",
          "Channels": 2,
          "SamplingRate": 48000,
          "Bitrate": 128041
        }
      ],
      "Name": "Ignite-short_1280x720_AACAudio_3551.mp4",
      "Size": 32414631,
      "Duration": "PT1M10.315S"
    },
    {
      "Sources": [
        {
          "Name": "Ignite-short_960x540_AACAudio_2216.mp4"
        }
      ],
      "VideoTracks": [
        {
          "Id": 1,
          "FourCC": "avc1",
          "Profile": "High",
          "Level": "3.1",
          "Width": "960",
          "Height": "540",
          "DisplayAspectRatioNumerator": 16.0,
          "DisplayAspectRatioDenominator": 9.0,
          "Framerate": 29.970,
          "Bitrate": 2216326,
          "TargetBitrate": 2210000
        }
      ],
      "AudioTracks": [
        {
          "Id": 2,
          "Codec": "aac",
          "Language": "eng",
          "Channels": 2,
          "SamplingRate": 48000,
          "Bitrate": 128041
        }
      ],
      "Name": "Ignite-short_960x540_AACAudio_2216.mp4",
      "Size": 20680897,
      "Duration": "PT1M10.315S"
    },
    {
      "Sources": [
        {
          "Name": "Ignite-short_640x360_AACAudio_1150.mp4"
        }
      ],
      "VideoTracks": [
        {
          "Id": 1,
          "FourCC": "avc1",
          "Profile": "High",
          "Level": "3.0",
          "Width": "640",
          "Height": "360",
          "DisplayAspectRatioNumerator": 16.0,
          "DisplayAspectRatioDenominator": 9.0,
          "Framerate": 29.970,
          "Bitrate": 1150440,
          "TargetBitrate": 1150000
        }
      ],
      "AudioTracks": [
        {
          "Id": 2,
          "Codec": "aac",
          "Language": "eng",
          "Channels": 2,
          "SamplingRate": 48000,
          "Bitrate": 128041
        }
      ],
      "Name": "Ignite-short_640x360_AACAudio_1150.mp4",
      "Size": 11313920,
      "Duration": "PT1M10.315S"
    },
    {
      "Sources": [
        {
          "Name": "Ignite-short_480x270_AACAudio_722.mp4"
        }
      ],
      "VideoTracks": [
        {
          "Id": 1,
          "FourCC": "avc1",
          "Profile": "High",
          "Level": "2.1",
          "Width": "480",
          "Height": "270",
          "DisplayAspectRatioNumerator": 16.0,
          "DisplayAspectRatioDenominator": 9.0,
          "Framerate": 29.970,
          "Bitrate": 722682,
          "TargetBitrate": 720000
        }
      ],
      "AudioTracks": [
        {
          "Id": 2,
          "Codec": "aac",
          "Language": "eng",
          "Channels": 2,
          "SamplingRate": 48000,
          "Bitrate": 128041
        }
      ],
      "Name": "Ignite-short_480x270_AACAudio_722.mp4",
      "Size": 7554708,
      "Duration": "PT1M10.315S"
    },
    {
      "Sources": [
        {
          "Name": "Ignite-short_320x180_AACAudio_380.mp4"
        }
      ],
      "VideoTracks": [
        {
          "Id": 1,
          "FourCC": "avc1",
          "Profile": "High",
          "Level": "1.3",
          "Width": "320",
          "Height": "180",
          "DisplayAspectRatioNumerator": 16.0,
          "DisplayAspectRatioDenominator": 9.0,
          "Framerate": 29.970,
          "Bitrate": 380655,
          "TargetBitrate": 380000
        }
      ],
      "AudioTracks": [
        {
          "Id": 2,
          "Codec": "aac",
          "Language": "eng",
          "Channels": 2,
          "SamplingRate": 48000,
          "Bitrate": 128041
        }`
      ],
      "Name": "Ignite-short_320x180_AACAudio_380.mp4",
      "Size": 4548932,
      "Duration": "PT1M10.315S"
    }
  ]
}
```

## <a name="next-steps"></a>次のステップ

[HTTPS URL からジョブの入力を作成する](job-input-from-http-how-to.md)
