---
title: "Media Encoder Standard の形式とコーデック"
description: "このトピックでは、Media Encoder Standard の形式とコーデックの概要を示します。"
services: media-services
documentationcenter: 
author: juliako
manager: erikre
editor: 
ms.assetid: f334b1ce-2f56-4968-a019-f0a2b0016d9f
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/10/2016
ms.author: juliako;anilmur
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 382913f0501c692cc1ee460d2264118414ad20c3


---
# <a name="media-encoder-standard-formats-and-codecs"></a>Media Encoder Standard の形式とコーデック
このドキュメントでは、Media Encoder Standard で使用できる一般的なインポートおよびエクスポート ファイル形式の一覧を示しています。

## <a name="input-containerfile-formats"></a>入力コンテナー/ファイル形式
| ファイル形式 (ファイル拡張子) | サポートされています |
| --- | --- | --- | --- |
| (H.264 および AAC コーデックでの) FLV (.flv) |はい |
| MXF    (.mxf) |はい |
| GXF    (.gxf) |はい |
| MPEG2-PS、MPEG2-TS、3GP (.ts、.ps、.3gp、.3gpp、.mpg) |あり |
| Windows Media Video (WMV)/ASF (.wmv、.asf) |あり |
| AVI (非圧縮 8-bit/10-bit) (.avi) |あり |
| MP4 (.mp4、.m4a、.m4v)/ISMV (.isma、.ismv) |あり |
| [Microsoft Digital Video Recording (DVR-MS)](https://msdn.microsoft.com/library/windows/desktop/dd692984) (.dvr-ms) |あり |
| Matroska/WebM (.mkv) |あり |
| WAVE/WAV (.wav) |あり |
| QuickTime (.mov) |あり |

> [!NOTE]
> 上に記載したのは、ごく一般的なファイル拡張子の一覧です。 Media Encoder Standard は他にもさまざまな拡張子をサポートしています (例: .m2ts、.mpeg2video、.qt)。 ファイルをエンコードしたときに、ファイル形式がサポートされていないことに関するエラー メッセージが表示された場合は、 [こちら](https://feedback.azure.com/forums/169396-media-services/category/144411-encoding-and-processing/)からフィードバックをお寄せください。
> 
> 

### <a name="audio-formats-in-input-containers"></a>入力コンテナーのオーディオ形式
Media Encoder Standard の入力コンテナーは次のオーディオ形式に対応しています。

* オーディオ トラックにインターリーブ ステレオまたは 5.1 サンプルが含まれる MXF、GXF、QuickTime ファイル。

または

* オーディオが個別 PCM トラックとして送信されるが、(ステレオまたは 5.1 への) チャネル マッピングをファイル メタデータから推測できる MXF、GXF、QuickTime ファイル。

明示的/ユーザー指定のチャネル マッピングが近い将来にサポートされる予定です。

## <a name="input-video-codecs"></a>入力ビデオ コーデック
| 入力ビデオ コーデック | サポートされています |
| --- | --- | --- | --- |
| AVC 8-bit/10-bit、最大 4:2:2 (AVCIntra を含む) |8 ビット 4:2:0 および 4:2:2 |
| Avid DNxHD (MXF) |あり |
| DVCPro/DVCProHD (MXF) |あり |
| デジタル ビデオ (DV) (AVI ファイルで) |あり |
| JPEG 2000 |あり |
| MPEG-2 (XDCAM、XDCAM HD、XDCAM IMX、CableLabs®、D10 など、最大 422 プロファイルおよびハイ レベル) |最大 422 プロファイル |
| MPEG-1 |あり |
| VC-1/WMV9 |あり |
| Canopus HQ/HQX |いいえ |
| MPEG-4 Part 2 |あり |
| [Theora](https://en.wikipedia.org/wiki/Theora) |あり |
| YUV420 非圧縮または中間 |あり |
| Apple ProRes 422 |あり |
| Apple ProRes 422 LT |あり |
| Apple ProRes 422 HQ |あり |
| Apple ProRes プロキシ |あり |
| Apple ProRes 4444 |あり |
| Apple ProRes 4444 XQ |あり |

## <a name="input-audio-codecs"></a>入力オーディオ コーデック
| 入力オーディオ コーデック | サポートされています |
| --- | --- | --- | --- |
| AAC (AAC-LC、AAC-HE、AAC-HEv2。最大 5.1) |はい |
| MPEG Layer 2 |あり |
| MP3 (MPEG-1 Audio Layer 3) |あり |
| Windows Media オーディオ |あり |
| WAV/PCM |はい |
| [FLAC](https://en.wikipedia.org/wiki/FLAC)</a> |はい |
| [Opus](http://go.microsoft.com/fwlink/?LinkId=822667) |はい |
| [Vorbis](https://en.wikipedia.org/wiki/Vorbis)</a> |はい |
| AMR (アダプティブ マルチ レート) |あり |
| AES (SMPTE 331M および 302M、AES3-2003) |いいえ |
| Dolby® E |いいえ |
| Dolby® Digital (AC3) |いいえ |
| Dolby® Digital Plus (E-AC3) |いいえ |

## <a name="output-formats-and-codecs"></a>出力形式とコーデック
次の表では、エクスポートでサポートされるコーデックおよびファイル形式の一覧を示します。

| ファイル形式 | ビデオ コーデック | オーディオ コーデック |
| --- | --- | --- |
| MP4  <br/><br/>(マルチビットレートの MP4 コンテナーを含む) |H.264 (High、Main、Baseline Profile) |AAC-LC、HE-AAC v1、HE-AAC v2 |
| MPEG2-TS |H.264 (High、Main、Baseline Profile) |AAC-LC、HE-AAC v1、HE-AAC v2 |

## <a name="media-services-learning-paths"></a>Media Services のラーニング パス
[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>フィードバックの提供
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

## <a name="see-also"></a>関連項目
[Azure Media Services を使用してオンデマンド コンテンツをエンコードする](media-services-encode-asset.md)

[メディア エンコーダー スタンダードを使用したエンコード方法](media-services-dotnet-encode-with-media-encoder-standard.md)




<!--HONumber=Nov16_HO3-->


