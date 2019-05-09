---
title: メディア エンコーダー プレミアム ワークフローの形式とコーデック | Microsoft Docs
description: このトピックでは、メディア エンコーダー プレミアム ワークフローの形式とコーデックについて説明します。
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/19/2019
ms.author: juliako;anilmur
ms.openlocfilehash: 25f32750b612bb66f23eb19c378f7935689f3a73
ms.sourcegitcommit: aa3be9ed0b92a0ac5a29c83095a7b20dd0693463
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2019
ms.locfileid: "58258617"
---
# <a name="media-encoder-premium-workflow-formats-and-codecs"></a>メディア エンコーダー プレミアム ワークフローの形式とコーデック

> [!NOTE]
> プレミアム エンコーダーに関するご質問は、電子メールで mepd@microsoft.com までお送りください。
> 
> このトピックで説明するメディア エンコーダー プレミアム ワークフロー メディア プロセッサは中国では提供されません。 

このドキュメントには、以前のパブリック プレビュー バージョンの **メディア エンコーダー プレミアム ワークフロー** でサポートされる入力ファイルおよび出力ファイルの形式とコーデックの一覧が含まれています。

[メディア エンコーダー プレミアム ワークフローの入力の形式とコーデック](#input_formats)

メディア エンコーダー Premium ワークフローの出力形式とコーデック

**メディア エンコーダー プレミアム ワークフロー** では、 [この](#closed_captioning) セクションで説明されているクローズド キャプションがサポートされています。 

## <a id="input_formats"></a>メディア エンコーダー プレミアム ワークフローの入力の形式とコーデック

次のセクションに、このメディア プロセッサの入力としてサポートされたコーデックとファイル形式の一覧を示します。

### <a name="input-containerfile-formats"></a>入力コンテナー/ファイル形式

* Adobe® Flash® F4V
* MXF/SMPTE 377M
* GXF
* MPEG-2 トランスポート ストリーム
* MPEG-2 プログラム ストリーム
* MPEG-4/MP4
* Windows Media/ASF
* AVI (非圧縮 8-bit/10-bit)

### <a name="input-video-codecs"></a>入力ビデオ コーデック

* AVC 8-bit/10-bit、最大 4:2:2 (AVCIntra を含む)
* Avid DNxHD (MXF)
* DVCPro/DVCProHD (MXF)
* HEVC/H.265、メインおよびメイン 10 プロファイル
* JPEG2000
* MPEG-2 (XDCAM、XDCAM HD、XDCAM IMX、CableLabs®、D10 など、最大 422 プロファイルおよびハイ レベル)
* MPEG-1
* Windows Media ビデオ/VC-1

### <a name="input-audio-codecs"></a>入力オーディオ コーデック

* AES (SMPTE 331M および 302M、AES3-2003)
* Dolby® E
* Dolby® Digital (AC3)
* AAC (AAC-LC、AAC-HE、AAC-HEv2。最大 5.1)
* MPEG Layer 2
* MP3 (MPEG-1 Audio Layer 3)
* Windows Media オーディオ
* WAV/PCM

## <a id="output_format"></a>メディア エンコーダー プレミアム ワークフローの出力の形式とコーデック

次のセクションに、このメディア プロセッサからの出力としてサポートされたコーデックとファイル形式の一覧を示します。

### <a name="output-containerfile-formats"></a>出力コンテナー/ファイル形式

* Adobe® Flash® F4V
* MXF (OP1a、XDCAM および AS02)
* DPP (AS11 を含む)
* GXF
* MPEG-4/MP4
* Windows Media/ASF
* AVI (非圧縮 8-bit/10-bit)
* スムーズ ストリーミング ファイル形式 (PIFF 1.3)
* MPEG TS 

### <a name="output-video-codecs"></a>出力ビデオ コーデック

* AVC (H.264、8-bit、最大ハイ プロファイル、レベル 5.2、4K Ultra HD、AVC Intra)
* Avid DNxHD (MXF)
* DVCPro/DVCProHD (MXF)
* MPEG-2 (XDCAM、XDCAM HD、XDCAM IMX、CableLabs®、D10 など、最大 422 プロファイルおよびハイ レベル)
* MPEG-1
* Windows Media ビデオ/VC-1
* JPEG サムネール作成
* HEVC (8 ビットおよび 10 ビットの H.265、メインおよびメイン 10 プロファイル)

  HDR 10 のサポートは特定のシナリオでのみ提供されています。詳細については、mepd@microsoft.com にお問い合わせください


### <a name="output-audio-codecs"></a>出力オーディオ コーデック

* AES (SMPTE 331M および 302M、AES3-2003)
* Dolby® Digital (AC3)
* Dolby® Digital Plus (E-AC3) 最大 7.1
* AAC (AAC-LC、AAC-HE、AAC-HEv2。最大 5.1)
* MPEG Layer 2
* MP3 (MPEG-1 Audio Layer 3)
* Windows Media オーディオ

>[!NOTE]
>Dolby® Digital (AC3) にエンコードする場合、出力は、ISO MP4 ファイルにのみ書き込むことができます。

## <a id="closed_captioning"></a>クローズド キャプションのサポート

取り込み側時は、 **メディア エンコーダー プレミアム ワークフロー** で次がサポートされます。

1. SCC ファイル
2. SMPTE-TT ファイル
3. CEA 608/CEA 708 – ユーザー データ (H.264 基本ストリームの SEI メッセージ、ATSC/53、SCTE20) として送信、または MXF/GXF ファイルでの補助データとして送信
4. STL サブタイトル ファイル

出力時は、次のオプションを使用できます。

1. CEA-608 から CEA-708 への変換
2. CEA-608/CEA-708 パス スルー (H.264 基本ストリームの SEI メッセージに埋め込むか、MXF ファイルの補助データとして実行)
3. SCC
4. SMPTE RP2052 によるソース CEA-608 の SMPTE Timed Text (DFXP ファイル作成を含む)
5. SRT サブタイトル ファイル
6. DVB サブタイトル ストリーム

> [!NOTE]
> Azure Media Services のストリーミングによる配信で、上記の出力形式のすべてがサポートされているわけではありません。

## <a name="known-issues"></a>既知の問題

入力ビデオにクローズド キャプションが含まれない場合でも、出力アセットには空の TTML ファイルが含まれます。 

## <a name="media-services-learning-paths"></a>Media Services のラーニング パス

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>フィードバックの提供

[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

