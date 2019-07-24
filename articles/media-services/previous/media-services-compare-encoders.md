---
title: Azure オンデマンド メディア エンコーダーの比較 | Microsoft Docs
description: このトピックでは、**Media Encoder Standard** と **Media Encoder Premium Workflow** のエンコード機能を比較します。
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.assetid: a79437c0-4832-423a-bca8-82632b2c47cc
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/14/2019
ms.author: juliako;anilmur
ms.openlocfilehash: bb827b80f79a53f30074b9230efe3e2049471051
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2019
ms.locfileid: "57880774"
---
# <a name="comparison-of-azure-on-demand-media-encoders"></a>Azure オンデマンド メディア エンコーダーの比較  

このトピックでは、**Media Encoder Standard** と **Media Encoder Premium Workflow** のエンコード機能を比較します。

## <a name="video-and-audio-processing-capabilities"></a>ビデオおよびオーディオの処理機能

次の表では、Media Encoder Standard (MES) と Media Encoder Premium Workflow (MEPW) の機能を比較します。 

|機能|メディア エンコーダー スタンダード|メディア エンコーダー Premium ワークフロー|
|---|---|---|
|エンコード中に条件ロジックを適用する<br/>(たとえば、入力が HD の場合は、5.1 オーディオをエンコードする)|いいえ |はい|
|クローズ キャプション|いいえ |[はい](media-services-premium-workflow-encoder-formats.md#closed_captioning)|
|[Dolby® Professional Loudness Correction](https://www.dolby.com/us/en/technologies/dolby-professional-loudness-solutions.pdf)<br/> (Dialogue Intelligence™ を採用)|いいえ |はい|
|ノンインターレース、逆テレシネ|Basic|放送品質|
|黒い境界線の検出と削除 <br/>(ピラーボックス、レターボックス)|いいえ |はい|
|サムネイルの生成|[はい](media-services-dotnet-generate-thumbnail-with-mes.md)|[はい](media-services-media-encoder-premium-workflow-tutorials.md#thumbnails_to__multibitrate_MP4)|
|ビデオのクリッピング/トリミングおよび合成|[はい](media-services-advanced-encoding-with-mes.md#trim_video)|はい|
|オーディオまたはビデオのオーバーレイ|[はい](media-services-advanced-encoding-with-mes.md#overlay)|[はい](media-services-media-encoder-premium-workflow-multiplefilesinput.md#example-1--overlay-an-image-on-top-of-the-video)|
|グラフィックスのオーバーレイ|イメージ ソースから|イメージとテキスト ソースから|
|複数のオーディオ言語トラック|制限あり|[はい](media-services-media-encoder-premium-workflow-multiplefilesinput.md#example-2--multiple-audio-language-encoding)|

## <a id="billing"></a>各エンコーダーで使用される課金メーター
| メディア プロセッサ名 | 適用される価格 | メモ |
| --- | --- | --- |
| **メディア エンコーダー スタンダード** |エンコーダー |エンコード タスクは、出力として生成されたすべてのメディア ファイルの合計再生時間 (分単位) に基づいて課金されます。料金は[こちら][1] (「ENCODER」列) に記載されています。 |
| **メディア エンコーダー Premium ワークフロー** |プレミアム エンコーダー |エンコード タスクは、出力として生成されたすべてのメディア ファイルの合計再生時間 (分単位) に基づいて課金されます。料金は[こちら][1] (「PREMIUM ENCODER」列) に記載されています。 |

## <a name="input-containerfile-formats"></a>入力コンテナー/ファイル形式
| 入力コンテナー/ファイル形式 | メディア エンコーダー スタンダード | メディア エンコーダー Premium ワークフロー |
| --- | --- | --- |
| Adobe® Flash® F4V |はい |はい |
| MXF/SMPTE 377M |はい |はい |
| GXF |はい |はい |
| MPEG-2 トランスポート ストリーム |はい |はい |
| MPEG-2 プログラム ストリーム |はい |はい |
| MPEG-4/MP4 |はい |はい |
| Windows Media/ASF |はい |はい |
| AVI (非圧縮 8-bit/10-bit) |はい |はい |
| 3GPP/3GPP2 |はい |いいえ  |
| スムーズ ストリーミング ファイル形式 (PIFF 1.3) |はい |いいえ  |
| [Microsoft Digital Video Recording (DVR-MS)](https://msdn.microsoft.com/library/windows/desktop/dd692984) |はい |いいえ  |
| Matroska/WebM |はい |いいえ  |
| QuickTime (.mov) |はい |いいえ  |

## <a name="input-video-codecs"></a>入力ビデオ コーデック
| 入力ビデオ コーデック | メディア エンコーダー スタンダード | メディア エンコーダー Premium ワークフロー |
| --- | --- | --- |
| AVC 8-bit/10-bit、最大 4:2:2 (AVCIntra を含む) |8 ビット 4:2:0 および 4:2:2 |はい |
| Avid DNxHD (MXF) |はい |はい |
| DVCPro/DVCProHD (MXF) |はい |はい |
| JPEG2000 |はい |はい |
| MPEG-2 (XDCAM、XDCAM HD、XDCAM IMX、CableLabs®、D10 など、最大 422 プロファイルおよびハイ レベル) |最大 422 プロファイル |はい |
| MPEG-1 |はい |はい |
| Windows Media ビデオ/VC-1 |はい |はい |
| Canopus HQ/HQX |いいえ  |いいえ  |
| MPEG-4 Part 2 |はい |いいえ  |
| [Theora](https://en.wikipedia.org/wiki/Theora) |はい |いいえ  |
| Apple ProRes 422 |はい |いいえ  |
| Apple ProRes 422 LT |はい |いいえ  |
| Apple ProRes 422 HQ |はい |いいえ  |
| Apple ProRes プロキシ |はい |いいえ  |
| Apple ProRes 4444 |はい |いいえ  |
| Apple ProRes 4444 XQ |はい |いいえ  |
| HEVC/H.265|メイン プロファイル|メインおよびメイン 10 プロファイル|

## <a name="input-audio-codecs"></a>入力オーディオ コーデック
| 入力オーディオ コーデック | メディア エンコーダー スタンダード | メディア エンコーダー Premium ワークフロー |
| --- | --- | --- |
| AES (SMPTE 331M および 302M、AES3-2003) |いいえ  |はい |
| Dolby® E |いいえ  |はい |
| Dolby® Digital (AC3) |いいえ  |はい |
| Dolby® Digital Plus (E-AC3) |いいえ  |はい |
| AAC (AAC-LC、AAC-HE、AAC-HEv2。最大 5.1) |はい |はい |
| MPEG Layer 2 |はい |はい |
| MP3 (MPEG-1 Audio Layer 3) |はい |はい |
| Windows Media オーディオ |はい |はい |
| WAV/PCM |はい |はい |
| [FLAC](https://en.wikipedia.org/wiki/FLAC)</a> |はい |いいえ  |
| [Opus](https://en.wikipedia.org/wiki/Opus_\(audio_format\)) |はい |いいえ  |
| [Vorbis](https://en.wikipedia.org/wiki/Vorbis)</a> |はい |いいえ  |

## <a name="output-containerfile-formats"></a>出力コンテナー/ファイル形式
| 出力コンテナー/ファイル形式 | メディア エンコーダー スタンダード | メディア エンコーダー Premium ワークフロー |
| --- | --- | --- |
| Adobe® Flash® F4V |いいえ  |はい |
| MXF (OP1a、XDCAM および AS02) |いいえ  |はい |
| DPP (AS11 を含む) |いいえ  |はい |
| GXF |いいえ  |はい |
| MPEG-4/MP4 |はい |はい |
| MPEG TS |はい |はい |
| Windows Media/ASF |いいえ  |はい |
| AVI (非圧縮 8-bit/10-bit) |いいえ  |はい |
| スムーズ ストリーミング ファイル形式 (PIFF 1.3) |いいえ  |はい |

## <a name="output-video-codecs"></a>出力ビデオ コーデック
| 出力ビデオ コーデック | メディア エンコーダー スタンダード | メディア エンコーダー Premium ワークフロー |
| --- | --- | --- |
| AVC (H.264、8-bit、最大ハイ プロファイル、レベル 5.2、4K Ultra HD、AVC Intra) |8 ビット 4:2:0 のみ |はい |
| HEVC (H.265、8 ビットおよび 10 ビット)  |いいえ  |はい |
| Avid DNxHD (MXF) |いいえ  |はい |
| MPEG-2 (XDCAM、XDCAM HD、XDCAM IMX、CableLabs®、D10 など、最大 422 プロファイルおよびハイ レベル) |いいえ  |はい |
| MPEG-1 |いいえ  |はい |
| Windows Media ビデオ/VC-1 |いいえ  |はい |
| JPEG サムネール作成 |はい |はい |
| PNG サムネイル作成 |はい |はい |
| BMP サムネイル作成 |はい |いいえ  |

## <a name="output-audio-codecs"></a>出力オーディオ コーデック
| 出力オーディオ コーデック | メディア エンコーダー スタンダード | メディア エンコーダー Premium ワークフロー |
| --- | --- | --- |
| AES (SMPTE 331M および 302M、AES3-2003) |いいえ  |はい |
| Dolby® Digital (AC3) |いいえ  |はい |
| Dolby® Digital Plus (E-AC3) 最大 7.1 |いいえ  |はい |
| AAC (AAC-LC、AAC-HE、AAC-HEv2。最大 5.1) |はい |はい |
| MPEG Layer 2 |いいえ  |はい |
| MP3 (MPEG-1 Audio Layer 3) |いいえ  |はい |
| Windows Media オーディオ |いいえ  |はい |

>[!NOTE]
>Dolby® Digital (AC3) にエンコードする場合、出力は、ISO MP4 ファイルにのみ書き込むことができます。

## <a name="media-services-learning-paths"></a>Media Services のラーニング パス
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>フィードバックの提供
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="related-articles"></a>関連記事
* [Media Encoder Standard のプリセットをカスタマイズし、高度なエンコード タスクを実行する](media-services-custom-mes-presets-with-dotnet.md)
* [クォータと制限](media-services-quotas-and-limitations.md)

<!--Reference links in article-->
[1]: https://azure.microsoft.com/pricing/details/media-services/
