---
title: Media Services のダイナミック パッケージ
titleSuffix: Azure Media Services
description: この記事では、Azure Media Services でのダイナミック パッケージの概要について説明します。
author: Juliako
manager: femila
editor: ''
services: media-services
documentationcenter: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: overview
ms.date: 10/17/2019
ms.author: juliako
ms.openlocfilehash: c12ebe307eb370e07b785b4379f82a4274964492
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/10/2019
ms.locfileid: "74977762"
---
# <a name="dynamic-packaging-in-media-services"></a>Media Services のダイナミック パッケージ

Microsoft Azure Media Services は、多くのメディア ソース ファイル形式をエンコードするために使用できます。 これは、コンテンツ保護の有無に関係なく、さまざまなストリーミング プロトコルを介して配信され、すべての主要なデバイス (iOS デバイスや Android デバイスなど) に到達します。 これらのクライアントは、さまざまなプロトコルを認識します。 たとえば、iOS では、HTTP ライブ ストリーミング (HLS) 形式でストリームを配信する必要があります。また、Android デバイスでは、HLS と MPEG DASH がサポートされます。

Media Services では、[ストリーミング エンドポイント](streaming-endpoint-concept.md)は、ダイナミック (Just-In-Time) パッケージおよび配信元サービスを表します。これは、ライブのオンデマンド コンテンツをクライアント プレーヤー アプリに直接配信できます。 次のセクションで説明する一般的なストリーミング メディア プロトコルのいずれかを使用します。 ダイナミック パッケージは、すべてのストリーミング エンドポイント (Standard または Premium) に標準で付属する機能です。

## <a name="a-iddelivery-protocolsto-prepare-your-source-files-for-delivery"></a><a id="delivery-protocols"/>ソース ファイルをデリバリー用に準備するには

ダイナミック パッケージを活用するには、中間 (ソース) ファイルを一連の複数ビットレート MP4 (ISO Base Media 14496-12) ファイルに[エンコード](encoding-concept.md)する必要があります。 エンコードされた MP4 を含む[資産](assets-concept.md)と、Media Services のダイナミック パッケージで必要とされるストリーミング構成ファイルが必要です。 この一連の MP4 ファイルから、ダイナミック パッケージを使用して、次のストリーミング メディア プロトコルを介してビデオを配信することができます。

|Protocol|例|
|---|---|
|HLS V4 |`https://amsv3account-usw22.streaming.media.azure.net/21b17732-0112-4d76-b526-763dcd843449/ignite.ism/manifest(format=m3u8-aapl)`|
|HLS V3 |`https://amsv3account-usw22.streaming.media.azure.net/21b17732-0112-4d76-b526-763dcd843449/ignite.ism/manifest(format=m3u8-aapl-v3)`|
|HLS CMAF| `https://amsv3account-usw22.streaming.media.azure.net/21b17732-0112-4d76-b526-763dcd843449/ignite.ism/manifest(format=m3u8-cmaf)`|
|MPEG-DASH CSF| `https://amsv3account-usw22.streaming.media.azure.net/21b17732-0112-4d76-b526-763dcd843449/ignite.ism/manifest(format=mpd-time-csf)` |
|MPEG-DASH CMAF|`https://amsv3account-usw22.streaming.media.azure.net/21b17732-0112-4d76-b526-763dcd843449/ignite.ism/manifest(format=mpd-time-cmaf)` |
|スムーズ ストリーミング| `https://amsv3account-usw22.streaming.media.azure.net/21b17732-0112-4d76-b526-763dcd843449/ignite.ism/manifest`|

Media Services 動的暗号化を使用してコンテンツを保護する場合は、「[ストリーミング プロトコルと暗号化の種類](content-protection-overview.md#streaming-protocols-and-encryption-types)」を参照してください。

> [!TIP]
> MP4 およびストリーミング構成ファイルを取得する方法の 1 つは、[Media Services を使用してお使いの中間ファイルをエンコードする](#encode-to-adaptive-bitrate-mp4s)ことです。 

エンコードされた資産内のビデオをクライアントが再生できるようにするには、[ストリーミング ロケーター](streaming-locators-concept.md)を作成し、ストリーミング URL をビルドする必要があります。 その後、ストリーミング クライアント マニフェスト (HLS、MPEG DASH、またはスムーズ ストリーミング) で指定した形式に基づいて、選択したプロトコルでストリームを受信します。

その結果、保存と課金の対象となるのは、単一のストレージ形式のファイルのみです。Media Services がクライアントからの要求に応じて、適切な応答を構築して返します。

## <a name="on-demand-streaming-workflow"></a>オンデマンド ストリーミングのワークフロー

次の手順は、Azure Media Services の標準エンコーダーとダイナミック パッケージを併用した一般的な Media Services でのストリーミング ワークフローを示しています。

1. QuickTime/MOV や MXF ファイルなどの入力ファイルをアップロードします。 このファイルは、中間ファイルやソース ファイルとも呼ばれます。 サポートされている形式の一覧については、[Media Encoder Standard でサポートされている形式](media-encoder-standard-formats.md)に関するページを参照してください。
1. 中間ファイルを H.264/AAC MP4 アダプティブ ビットレート セットに[エンコード](#encode-to-adaptive-bitrate-mp4s)します。
1. アダプティブ ビットレート MP4 セットが含まれる出力資産を発行します。 ストリーミング ロケーターを作成して発行します。
1. さまざまな形式 (HLS、MPEG-DASH および Smooth Streaming) をターゲットとする URL を構築します。 これらのさまざまな形式の正しいマニフェストおよび要求の処理は、**ストリーミング エンドポイント**が行います。

次の図は、ダイナミック パッケージのワークフローを使用したオンデマンド ストリーミングを示しています。

![ダイナミック パッケージを使用したオンデマンド ストリーミングのワークフローの図](./media/dynamic-packaging-overview/media-services-dynamic-packaging.svg)

### <a name="encode-to-adaptive-bitrate-mp4s"></a>アダプティブ ビットレート MP4 へのエンコード

[Media Services を使用してビデオをエンコードする方法](encoding-concept.md)の例については、以下の記事を参照してください。

* [組み込みのプリセットを使用して HTTPS の URL をエンコードする](job-input-from-http-how-to.md)。
* [組み込みのプリセットを使用してローカル ファイルをエンコードする](job-input-from-local-file-how-to.md)。
* [自分の特定のシナリオまたはデバイス要件に対応するカスタム プリセットを構築する](customize-encoder-presets-how-to.md)。

Media Encoder Standard の[形式とコーデック](media-encoder-standard-formats.md)の一覧を参照してください。

## <a name="live-streaming-workflow"></a>ライブ ストリーミング ワークフロー

ライブ イベントには、パススルーとライブ エンコードの 2 種類があります。 

ダイナミック パッケージを使用したライブ ストリーミングの一般的なワークフローは次のとおりです。

1. [ライブ イベント](live-events-outputs-concept.md)を作成します。
1. 取り込み URL を取得し、その URL を使用してコントリビューション フィードを送信するようにオンプレミス エンコーダーを構成します。
1. プレビュー URL を取得し、それを使用して、エンコーダーからの入力が受信されていることを確認します。
1. 新しい資産を作成します。
1. ライブ出力を作成し、作成した資産の名前を使用します。<br />ライブ出力により、ストリームが資産にアーカイブされます。
1. 組み込みのストリーミング ポリシー タイプでストリーミング ロケーターを作成します。<br />コンテンツを暗号化する場合は、「[コンテンツ保護の概要](content-protection-overview.md)」を確認してください。
1. 使用する URL を取得するためのパスをストリーミング ロケーターに列挙します。
1. ストリーミングするストリーミング エンドポイントのホスト名を取得します。
1. さまざまな形式 (HLS、MPEG-DASH および Smooth Streaming) をターゲットとする URL を構築します。 さまざまな形式の正しいマニフェストおよび要求の処理は、ストリーミング エンドポイントが行います。

次の図は、ダイナミック パッケージを使用したライブ ストリーミングのワークフローを示しています。

![ダイナミック パッケージを使用したパススルー エンコードのワークフローの図](./media/live-streaming/pass-through.svg)

Media Services v3 のライブ ストリームの詳細については、[ライブ ストリーミングの概要](live-streaming-overview.md)に関するページを参照してください。

## <a name="video-codecs-supported-by-dynamic-packaging"></a>ダイナミック パッケージでサポートされているビデオ コーデック

ダイナミック パッケージでは、[H.264](https://en.m.wikipedia.org/wiki/H.264/MPEG-4_AVC) (MPEG-4 AVC または AVC1) または [H.265](https://en.m.wikipedia.org/wiki/High_Efficiency_Video_Coding) (HEVC、hev1、または hvc1) でエンコードされたビデオの MP4 ファイルがサポートされています。

> [!NOTE]
> ダイナミック パッケージでは、最大 4K の解像度および最大 60 フレーム/秒のフレーム レートをテスト済みです。 [Premium エンコーダー](https://docs.microsoft.com/azure/media-services/previous/media-services-encode-asset#media-encoder-premium-workflow)では、従来の v2 API を使用した H.265 へのエンコードがサポートされます。

## <a name="a-idaudio-codecsaudio-codecs-supported-by-dynamic-packaging"></a><a id="audio-codecs"/>ダイナミック パッケージによってサポートされているオーディオ コーデック

ダイナミック パッケージでは、以下のプロトコルでエンコードされたオーディオがサポートされています。

* [AAC](https://en.wikipedia.org/wiki/Advanced_Audio_Coding) (AAC-LC、HE-AAC v1、または HE-AAC v2)
* [Dolby Digital Plus](https://en.wikipedia.org/wiki/Dolby_Digital_Plus) (Enhanced AC-3 または E-AC3)
* Dolby Atmos<br />
   Dolby Atmos コンテンツのストリーミングは、CSF (Common Streaming Format) または CMAF (Common Media Application Format) フラグメント化 MP4 による MPEG-DASH プロトコルなどの標準でサポートされ、CMAF による HLS (HTTP ライブ ストリーミング) 経由でサポートされています。

* [DTS](https://en.wikipedia.org/wiki/DTS_%28sound_system%29)<br />
   DASH-CSF、DASH-CMAF、HLS-M2TS、HLS-CMAF パッケージ形式でサポートされている DTS コード:  

    * DTS Digital Surround (dtsc)
    * DTS-HD High Resolution と DTS-HD Master Audio (dtsh)
    * DTS Express (dtse)
    * DTS-HD Lossless (コアなし) (dtsl)

ダイナミック パッケージでは、複数のコーデックと言語が使用された複数のオーディオ トラックがある資産のストリーム配信のために、DASH または HLS (バージョン 4 以降) を使用して複数のオーディオ トラックがサポートされます。

### <a name="additional-notes"></a>その他のメモ

ダイナミック パッケージでは、[Dolby Digital](https://en.wikipedia.org/wiki/Dolby_Digital) (AC3) オーディオ (古いコーデック) を含むファイルはサポートされていません。

> [!NOTE]
> [Premium エンコーダー](https://docs.microsoft.com/azure/media-services/previous/media-services-encode-asset#media-encoder-premium-workflow)では、従来の v2 API を使用した Dolby Digital Plus へのエンコードがサポートされます。

## <a name="manifests"></a>マニフェスト

Media Services のダイナミック パッケージでは、HLS、MPEG-DASH、および Smooth Streaming のストリーミング クライアント マニフェストが、URL 内の形式セレクターに基づいて動的に生成されます。  

マニフェスト ファイルには、トラックの種類 (オーディオ、ビデオ、またはテキスト)、トラック名、開始時刻と終了時刻、ビットレート (品質)、トラック言語、プレゼンテーション ウィンドウ (固定時間のスライディング ウィンドウ)、ビデオ コーデック (FourCC) などの、ストリーミング メタデータが含まれます。 また、次に再生可能なビデオ フラグメントとその場所の情報を通知して、次のフラグメントを取得するようにプレイヤーに指示します。 フラグメント (またはセグメント) とは、ビデオ コンテンツの実際の "チャンク" です。

### <a name="examples"></a>例

#### <a name="hls"></a>HLS

以下に示すのは、HLS マニフェスト ファイル (HLS マスター プレイリストとも呼ばれます) の例です。 

```
#EXTM3U
#EXT-X-VERSION:4
#EXT-X-MEDIA:TYPE=AUDIO,GROUP-ID="audio",NAME="aac_eng_2_128041_2_1",LANGUAGE="eng",DEFAULT=YES,AUTOSELECT=YES,URI="QualityLevels(128041)/Manifest(aac_eng_2_128041_2_1,format=m3u8-aapl)"
#EXT-X-STREAM-INF:BANDWIDTH=536608,RESOLUTION=320x180,CODECS="avc1.64000d,mp4a.40.2",AUDIO="audio"
QualityLevels(381048)/Manifest(video,format=m3u8-aapl)
#EXT-X-I-FRAME-STREAM-INF:BANDWIDTH=536608,RESOLUTION=320x180,CODECS="avc1.64000d",URI="QualityLevels(381048)/Manifest(video,format=m3u8-aapl,type=keyframes)"
#EXT-X-STREAM-INF:BANDWIDTH=884544,RESOLUTION=480x270,CODECS="avc1.640015,mp4a.40.2",AUDIO="audio"
QualityLevels(721495)/Manifest(video,format=m3u8-aapl)
#EXT-X-I-FRAME-STREAM-INF:BANDWIDTH=884544,RESOLUTION=480x270,CODECS="avc1.640015",URI="QualityLevels(721495)/Manifest(video,format=m3u8-aapl,type=keyframes)"
#EXT-X-STREAM-INF:BANDWIDTH=1327398,RESOLUTION=640x360,CODECS="avc1.64001e,mp4a.40.2",AUDIO="audio"
QualityLevels(1154816)/Manifest(video,format=m3u8-aapl)
#EXT-X-I-FRAME-STREAM-INF:BANDWIDTH=1327398,RESOLUTION=640x360,CODECS="avc1.64001e",URI="QualityLevels(1154816)/Manifest(video,format=m3u8-aapl,type=keyframes)"
#EXT-X-STREAM-INF:BANDWIDTH=2413312,RESOLUTION=960x540,CODECS="avc1.64001f,mp4a.40.2",AUDIO="audio"
QualityLevels(2217354)/Manifest(video,format=m3u8-aapl)
#EXT-X-I-FRAME-STREAM-INF:BANDWIDTH=2413312,RESOLUTION=960x540,CODECS="avc1.64001f",URI="QualityLevels(2217354)/Manifest(video,format=m3u8-aapl,type=keyframes)"
#EXT-X-STREAM-INF:BANDWIDTH=3805760,RESOLUTION=1280x720,CODECS="avc1.640020,mp4a.40.2",AUDIO="audio"
QualityLevels(3579827)/Manifest(video,format=m3u8-aapl)
#EXT-X-I-FRAME-STREAM-INF:BANDWIDTH=3805760,RESOLUTION=1280x720,CODECS="avc1.640020",URI="QualityLevels(3579827)/Manifest(video,format=m3u8-aapl,type=keyframes)"
#EXT-X-STREAM-INF:BANDWIDTH=139017,CODECS="mp4a.40.2",AUDIO="audio"
QualityLevels(128041)/Manifest(aac_eng_2_128041_2_1,format=m3u8-aapl)
```

#### <a name="mpeg-dash"></a>MPEG-DASH

以下に示すのは、MPEG-DASH マニフェスト ファイル (MPEG-DASH Media Presentation Description (MPD) とも呼ばれます) の例です。

```xml
<?xml version="1.0" encoding="UTF-8"?>
<MPD xmlns="urn:mpeg:dash:schema:mpd:2011" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" profiles="urn:mpeg:dash:profile:isoff-live:2011" type="static" mediaPresentationDuration="PT1M10.315S" minBufferTime="PT7S">
   <Period>
      <AdaptationSet id="1" group="5" profiles="ccff" bitstreamSwitching="false" segmentAlignment="true" contentType="audio" mimeType="audio/mp4" codecs="mp4a.40.2" lang="en">
         <SegmentTemplate timescale="10000000" media="QualityLevels($Bandwidth$)/Fragments(aac_eng_2_128041_2_1=$Time$,format=mpd-time-csf)" initialization="QualityLevels($Bandwidth$)/Fragments(aac_eng_2_128041_2_1=i,format=mpd-time-csf)">
            <SegmentTimeline>
               <S d="60160000" r="10" />
               <S d="41386666" />
            </SegmentTimeline>
         </SegmentTemplate>
         <Representation id="5_A_aac_eng_2_128041_2_1_1" bandwidth="128041" audioSamplingRate="48000" />
      </AdaptationSet>
      <AdaptationSet id="2" group="1" profiles="ccff" bitstreamSwitching="false" segmentAlignment="true" contentType="video" mimeType="video/mp4" codecs="avc1.640020" maxWidth="1280" maxHeight="720" startWithSAP="1">
         <SegmentTemplate timescale="10000000" media="QualityLevels($Bandwidth$)/Fragments(video=$Time$,format=mpd-time-csf)" initialization="QualityLevels($Bandwidth$)/Fragments(video=i,format=mpd-time-csf)">
            <SegmentTimeline>
               <S d="60060000" r="10" />
               <S d="42375666" />
            </SegmentTimeline>
         </SegmentTemplate>
         <Representation id="1_V_video_1" bandwidth="3579827" width="1280" height="720" />
         <Representation id="1_V_video_2" bandwidth="2217354" codecs="avc1.64001F" width="960" height="540" />
         <Representation id="1_V_video_3" bandwidth="1154816" codecs="avc1.64001E" width="640" height="360" />
         <Representation id="1_V_video_4" bandwidth="721495" codecs="avc1.640015" width="480" height="270" />
         <Representation id="1_V_video_5" bandwidth="381048" codecs="avc1.64000D" width="320" height="180" />
      </AdaptationSet>
   </Period>
</MPD>
```
#### <a name="smooth-streaming"></a>スムーズ ストリーミング

以下に示すのは、Smooth Streaming マニフェスト ファイルの例です。

```xml
<?xml version="1.0" encoding="UTF-8"?>
<SmoothStreamingMedia MajorVersion="2" MinorVersion="2" Duration="703146666" TimeScale="10000000">
   <StreamIndex Chunks="12" Type="audio" Url="QualityLevels({bitrate})/Fragments(aac_eng_2_128041_2_1={start time})" QualityLevels="1" Language="eng" Name="aac_eng_2_128041_2_1">
      <QualityLevel AudioTag="255" Index="0" BitsPerSample="16" Bitrate="128041" FourCC="AACL" CodecPrivateData="1190" Channels="2" PacketSize="4" SamplingRate="48000" />
      <c t="0" d="60160000" r="11" />
      <c d="41386666" />
   </StreamIndex>
   <StreamIndex Chunks="12" Type="video" Url="QualityLevels({bitrate})/Fragments(video={start time})" QualityLevels="5">
      <QualityLevel Index="0" Bitrate="3579827" FourCC="H264" MaxWidth="1280" MaxHeight="720" CodecPrivateData="0000000167640020ACD9405005BB011000003E90000EA600F18319600000000168EBECB22C" />
      <QualityLevel Index="1" Bitrate="2217354" FourCC="H264" MaxWidth="960" MaxHeight="540" CodecPrivateData="000000016764001FACD940F0117EF01100000303E90000EA600F1831960000000168EBECB22C" />
      <QualityLevel Index="2" Bitrate="1154816" FourCC="H264" MaxWidth="640" MaxHeight="360" CodecPrivateData="000000016764001EACD940A02FF9701100000303E90000EA600F162D960000000168EBECB22C" />
      <QualityLevel Index="3" Bitrate="721495" FourCC="H264" MaxWidth="480" MaxHeight="270" CodecPrivateData="0000000167640015ACD941E08FEB011000003E90000EA600F162D9600000000168EBECB22C" />
      <QualityLevel Index="4" Bitrate="381048" FourCC="H264" MaxWidth="320" MaxHeight="180" CodecPrivateData="000000016764000DACD941419F9F011000003E90000EA600F14299600000000168EBECB22C" />
      <c t="0" d="60060000" r="11" />
      <c d="42375666" />
   </StreamIndex>
</SmoothStreamingMedia>
```

### <a name="naming-of-tracks-in-the-manifest"></a>マニフェスト内のトラックの名前付け

.ism ファイルにオーディオ トラック名が指定されている場合、Media Services によって、特定のオーディオ トラックのテクスチャ情報を指定するための `Label` 要素が `AdaptationSet` 内に追加されます。出力 DASH マニフェストの例:

```xml
<AdaptationSet codecs="mp4a.40.2" contentType="audio" lang="en" mimeType="audio/mp4" subsegmentAlignment="true" subsegmentStartsWithSAP="1">
  <Label>audio_track_name</Label>
  <Role schemeIdUri="urn:mpeg:dash:role:2011" value="main"/>
  <Representation audioSamplingRate="48000" bandwidth="131152" id="German_Forest_Short_Poem_english-en-68s-2-lc-128000bps_seg">
    <BaseURL>German_Forest_Short_Poem_english-en-68s-2-lc-128000bps_seg.mp4</BaseURL>
  </Representation>
</AdaptationSet>
```

プレーヤーは、`Label` 要素を使用して UI に表示できます。

### <a name="signaling-audio-description-tracks"></a>オーディオ説明トラックのシグナル通知

ナレーション トラックをビデオに追加することで、目が不自由なクライアントがナレーションを聞いてビデオ録画を追うことができます。 オーディオ トラックには、マニフェストでオーディオ説明として注釈を付ける必要があります。 そのためには、"accessibility" パラメーターと "role" パラメーターを .ism ファイルに追加します。 オーディオ トラックをオーディオ説明としてシグナル通知するためには、これらのパラメーターを正しく設定する必要があります。 たとえば、特定のオーディオ トラックの .ism ファイルに `<param name="accessibility" value="description" />` と `<param name="role" value="alternate"` を追加します。 

詳細については、[説明を含んだオーディオ トラックをシグナル通知する方法](signal-descriptive-audio-howto.md)の例を参照してください。

#### <a name="smooth-streaming-manifest"></a>Smooth Streaming マニフェスト

Smooth Streaming ストリームを再生している場合、そのオーディオ トラックに対応する `Accessibility` 属性と `Role` 属性の値がマニフェストに含まれています。たとえば、オーディオ説明であることを示すために、`Role="alternate" Accessibility="description"` が `StreamIndex` 要素に追加されます。

#### <a name="dash-manifest"></a>DASH マニフェスト

DASH マニフェストの場合は、オーディオ説明をシグナル通知するために次の 2 つの要素が追加されます。

```xml
<Accessibility schemeIdUri="urn:mpeg:dash:role:2011" value="description"/>
<Role schemeIdUri="urn:mpeg:dash:role:2011" value="alternate"/>
```

#### <a name="hls-playlist"></a>HLS プレイリスト

HLS v7 以降 `(format=m3u8-cmaf)` では、オーディオ説明トラックをシグナル通知する際に、そのプレイリストに `AUTOSELECT=YES,CHARACTERISTICS="public.accessibility.describes-video"` が含まれます。

#### <a name="example"></a>例

詳細については、[オーディオ説明トラックをシグナル通知する方法](signal-descriptive-audio-howto.md)に関するページを参照してください。

## <a name="dynamic-manifest"></a>動的マニフェスト

プレーヤーに送信されるトラック数、形式、ビットレート、およびプレゼンテーション時間枠を制御するために、Media Services ダイナミック パッケージャーで動的フィルターを使用できます。 詳細については、[ダイナミック パッケージャーでの事前フィルター処理マニフェスト](filters-dynamic-manifest-overview.md)に関するページを参照してください。

## <a name="dynamic-encryption"></a>動的な暗号化

"*動的暗号化*" を使用すると、AES-128 または次の 3 つの主要なデジタル著作権管理 (DRM) システムのいずれかを用いて、ライブまたはオンデマンドのコンテンツを動的に暗号化できます。コンテンツを配信できます。 Media Services では、承認されたクライアントに AES キーと DRM ライセンスを配信するためのサービスも提供しています。 詳細については、[動的暗号化](content-protection-overview.md)に関するページを参照してください。

> [!NOTE]
> Widevine は Google Inc. によって提供されるサービスであり、Google Inc. のサービス使用条件とプライバシー ポリシーが適用されます。

## <a name="more-information"></a>詳細情報

[Azure Media Services コミュニティ](media-services-community.md)を参照して、さまざまな質問の方法、フィードバックする方法、Media Services に関する最新情報の入手方法を確認してください。

## <a name="need-help"></a>お困りの際は、

[[新しいサポート要求]](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) に移動することで、サポート チケットを開くことができます。

## <a name="next-steps"></a>次の手順

> [!NOTE]
> 現時点では、Azure portal を使用して v3 リソースを管理することはできません。 [REST API](https://aka.ms/ams-v3-rest-ref)、[CLI](https://aka.ms/ams-v3-cli-ref)、またはサポートされている [SDK](media-services-apis-overview.md#sdks) のいずれかを使用してください。

[ビデオのアップロード、エンコード、およびストリーミング](stream-files-tutorial-with-api.md)の方法について学習します。
