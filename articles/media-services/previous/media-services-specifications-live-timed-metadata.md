---
title: Azure Media Services - ライブ ストリーミングでの時間指定メタデータのシグナル通知 | Microsoft Docs
description: この仕様では、ライブ ストリーミング内での時間指定メタデータのシグナル通知に対して Media Services がサポートする 2 つのモードについて説明します。 これには、汎用時間指定メタデータ シグナルと、広告スプライス挿入に対する SCTE-35 シグナル通知が含まれます。
services: media-services
documentationcenter: ''
author: johndeu
manager: femila
editor: johndeu
ms.assetid: 265b94b1-0fb8-493a-90ec-a4244f51ce85
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/20/2019
ms.author: johndeu;
ms.openlocfilehash: 10dbf7e8cf67ab721cf525d4a1e7594473592bd4
ms.sourcegitcommit: ab6fa92977255c5ecbe8a53cac61c2cd2a11601f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2019
ms.locfileid: "58295174"
---
# <a name="signaling-timed-metadata-in-live-streaming"></a>ライブ ストリーミングでの時間指定メタデータのシグナル通知 


## <a name="1-introduction"></a>1 Introduction (はじめに) 
広告の挿入またはクライアント プレーヤーでのカスタム イベントの挿入を容易にするため、ブロードキャスタはビデオ内に埋め込まれた時間指定メタデータを利用することがよくあります。 これらのシナリオを可能にするため、Media Services では、ライブ ストリーミング チャネルの取り込みポイントからクライアント アプリケーションまで、メディアと共に時間指定メタデータをトランスポートできます。
この仕様では、ライブ ストリーミング シグナル内の時間指定メタデータに対して Media Services がサポートする 2 つのモードについて説明します。

1. [SCTE-67] で概要が示されている推奨される方法に留意した [SCTE-35] シグナル通知

2. [SCTE-35] ではないメッセージのための汎用時間指定メタデータ シグナル通知モード

### <a name="12-conformance-notation"></a>1.2 適合性の表記
このドキュメントで使用するキーワード "MUST (しなければなりません)"、"MUST NOT (してはなりません)"、"REQUIRED (必要です)"、"SHALL (することになります)"、"SHALL NOT (することはありません)"、"SHOULD (する必要があります)"、"SHOULD NOT (すべきではありません)"、"RECOMMENDED (お勧めします)"、"MAY (する場合があります)"、"OPTIONAL (オプション)" は、RFC 2119 で説明するとおりに解釈されます

### <a name="13-terms-used"></a>1.3 用語

| 用語              | 定義                                                                                                                                                                                                                       |
|-------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| プレゼンテーション時間 | イベントが閲覧者に提示される時間。 時間は、閲覧者がイベントを見るメディア タイムライン上の時点を表します。 たとえば、SCTE-35 splice_info() コマンド メッセージのプレゼンテーション時間は splice_time() です。 |
| 到着時間      | イベント メッセージが到着する時間。 イベント メッセージはイベントのプレゼンテーション時間より前に送信されるため、通常、この時間はイベントのプレゼンテーション時間とは異なります。                                     |
| スパース トラック      | 連続的ではなく、親トラックまたは制御トラックと時間が同期されるメディア トラック。                                                                                                                                    |
| Origin (配信元)            | Azure メディア ストリーミング サービス                                                                                                                                                                                                |
| チャンネル シンク      | Azure メディア ライブ ストリーミング サービス                                                                                                                                                                                           |
| HLS               | Apple HTTP ライブ ストリーミング プロトコル                                                                                                                                                                                               |
| DASH              | Dynamic Adaptive Streaming Over HTTP (HTTP 経由のダイナミック アダプティブ ストリーミング)                                                                                                                                                                                             |
| Smooth            | スムーズ ストリーミング プロトコル                                                                                                                                                                                                        |
| MPEG2-TS          | MPEG-2 トランスポート ストリーム                                                                                                                                                                                                         |
| RTMP              | リアルタイム マルチメディア プロトコル                                                                                                                                                                                                    |
| uimsbf            | 符号なし整数、最上位ビットが先頭。                                                                                                                                                                                    |

-----------------------

## <a name="2-timed-metadata-ingest"></a>2 時間指定メタデータの取り込み
## <a name="21-rtmp-ingest"></a>2.1 RTMP 取り込み
RTMP は、RTMP ストリーム内に埋め込まれた AMF キュー メッセージとして送信される時間指定メタデータ シグナルをサポートします。 キュー メッセージは、実際のイベントまたは広告スプライス挿入が発生する必要がある時点より前に送信できます。 このシナリオをサポートするため、スプライスまたはセグメントの実際の時間がキュー メッセージ内で送信されます。 詳しくは、[AMF0] をご覧ください。

次の表では、Media Services が取り込む AMF メッセージのペイロードの形式について説明します。

AMF メッセージの名前を使って、同じ種類の複数のイベント ストリームを区別できます。  [SCTE-35] メッセージの場合は、[SCTE-67] で推奨されているように、AMF メッセージの名前は "onAdCue" でなければなりません。  この設計の将来的な技術革新が妨げられないように、以下に記載されていないフィールドは無視しなければなりません。

### <a name="signal-syntax"></a>シグナルの構文
RTMP のシンプル モードでは、Media Services は次の形式の "onAdCue" と呼ばれる 1 つの AMF キュー メッセージをサポートします。

### <a name="simple-mode"></a>シンプル モード

| フィールド名 | フィールドの型 | 必須 | 説明                                                                                                             |
|------------|------------|----------|--------------------------------------------------------------------------------------------------------------------------|
| cue        | String     | 必須 | イベント メッセージ。  シンプル モードのスプライスを指定する "SpliceOut" である必要があります。                                              |
| id         | String     | 必須 | スプライスまたはセグメントを記述する一意識別子。 メッセージのこのインスタンスを示します。                            |
| duration   | Number     | 必須 | スプライスの継続時間。 単位は秒の小数部です。                                                                |
| elapsed    | Number     | 省略可能 | チューニングをサポートするためにシグナルを繰り返す場合は、スプライスが開始してから経過したプレゼンテーション時間の長さをこのフィールドに設定する必要があります。 単位は秒の小数部です。 シンプル モードを使う場合、この値はスプライスの元の継続時間を超えてはなりません。                                                  |
| time       | Number     | 必須 | プレゼンテーション時間で表されたスプライスの時間。 単位は秒の小数部です。                                     |

---------------------------

### <a name="scte-35-mode"></a>SCTE-35 モード

| フィールド名 | フィールドの型 | 必須 | 説明                                                                                                             |
|------------|------------|----------|--------------------------------------------------------------------------------------------------------------------------|
| cue        | String     | 必須 | イベント メッセージ。  [SCTE-35] メッセージの場合、[SCTE-67] に準拠する HLS、Smooth、Dash クライアントにメッセージが送信されるためには、このフィールドは base64 (IETF RFC 4648) でバイナリ エンコードされた splice_info_section() でなければなりません。                                              |
| type       | String     | 必須 | メッセージのスキームを示す URN または URL。 [SCTE-35] メッセージの場合、[SCTE-67] に準拠する HLS、Smooth、Dash クライアントにメッセージが送信されるためには、このフィールドは "urn:scte:scte35:2013a:bin" でなければなりません。  |
| id         | String     | 必須 | スプライスまたはセグメントを記述する一意識別子。 メッセージのこのインスタンスを示します。  同じセマンティクスを持つメッセージには、同じ値が設定されている必要があります。|
| duration   | Number     | 必須 | イベントまたは広告スプライス セグメントの継続時間 (わかっている場合)。 わからない場合は、0 にする必要があります。                                                                 |
| elapsed    | Number     | 省略可能 | チューニングのために [SCTE-35] 広告シグナルを繰り返す場合は、スプライスが開始してから経過したプレゼンテーション時間の長さをこのフィールドに設定する必要があります。 単位は秒の小数部です。 [SCTE-35] モードでは、この値はスプライスまたはセグメントで指定されている元の継続時間を超えてもかまいません。                                                  |
| time       | Number     | 必須 | イベントまたは広告スプライスのプレゼンテーション時間。  プレゼンテーション時間と継続時間は、[ISO-14496-12] Annex I で定義されているように、タイプ 1 または 2 のストリーム アクセス ポイント (SAP) と一致している必要があります。HLS エグレスの場合は、プレゼンテーション時間と継続時間はセグメントの境界と一致する必要があります。 同じイベント ストリームに含まれる異なるイベント メッセージのプレゼンテーション時間と継続時間は、オーバーラップしてはなりません。 単位は秒の小数部です。

---------------------------

#### <a name="211-cancellation-and-updates"></a>2.1.1 キャンセルと更新

同じプレゼンテーション時間と ID で複数のメッセージを送信することにより、メッセージをキャンセルまたは更新することができます。 プレゼンテーション時間と ID はイベントを一意に識別し、プレロール制約を満たす特定のプレゼンテーション時間で受信された最後のメッセージが、処理されるメッセージになります。 更新されたイベントは、以前に受信されたメッセージを置き換えます。 プレロール制約は 4 秒です。 プレゼンテーション時間の少なくとも 4 秒前に受信されたメッセージが処理されます。

## <a name="22-fragmented-mp4-ingest-smooth-streaming"></a>2.2 フラグメント化された MP4 の取り込み (Smooth Streaming)
ライブ ストリームの取り込みの要件については、[LIVE-FMP4] をご覧ください。 以降のセクションでは、時間指定プレゼンテーション メタデータの取り込みに関する詳細情報を提供します。  時間指定プレゼンテーション メタデータは、スパース トラックとして取り込まれます。これは、Live Server Manifest Box (MS-SSTR を参照) と Movie Box ("moov") の両方で定義されています。  各スパース フラグメントは、Movie Fragment Box ("moof") と Media Data Box ("mdat") で構成されており、"mdat" ボックスはバイナリ メッセージです。

#### <a name="221-live-server-manifest-box"></a>2.2.1 Live Server Manifest Box
スパース トラックは、\<textstream\> エントリを含む Live Server Manifest Box で宣言されている必要があり、以下の属性が設定されている必要があります。

| **属性名** | **フィールドの型** | **必須** | **説明**                                                                                                                                                                                                                                                 |
|--------------------|----------------|---------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| systemBitrate      | Number         | 必須      | "0" でなければなりません。不明な可変ビットレートのトラックを示します。                                                                                                                                                                                                 |
| parentTrackName    | String         | 必須      | 親トラックの名前でなければなりません。スパース トラックの時間コードは親トラックのタイムスケールに揃えられます。 スパース トラックを親トラックにすることはできません。                                                                                                                    |
| manifestOutput     | Boolean        | 必須      | "true" でなければなりません。スパース トラックが Smooth クライアント マニフェストに埋め込まれることを示します。                                                                                                                                                               |
| Subtype            | String         | 必須      | 4 文字のコード "DATA" でなければなりません。                                                                                                                                                                                                                         |
| Scheme             | String         | 必須      | メッセージのスキームを示す URN または URL でなければなりません。 [SCTE-35] メッセージの場合、[SCTE-67] に準拠する HLS、Smooth、Dash クライアントにメッセージが送信されるためには、このフィールドは "urn:scte:scte35:2013a:bin" でなければなりません。 |
| trackName          | String         | 必須      | スパース トラックの名前でなければなりません。trackName を使って、同じスキームの複数のイベント ストリームを区別できます。 各一意のイベント ストリームは、一意のトラック名を持っている必要があります。                                                                           |
| timescale          | Number         | 省略可能      | 親のトラックのタイムスケールでなければなりません。                                                                                                                                                                                                                      |

-------------------------------------

### <a name="222-movie-box"></a>2.2.2 Movie Box

Movie Box ("moov") は、スパース トラックのストリーム ヘッダーの一部として、Live Server Manifest Box の後に続きます。

[ISO-14496-12] で定義されているように、"moov" ボックスは **TrackHeaderBox ("tkhd")** を含む必要があり、次の制約があります。

| **フィールド名** | **フィールドの型**          | **必須** | **説明**                                                                                                |
|----------------|-------------------------|---------------|----------------------------------------------------------------------------------------------------------------|
| duration       | 64 ビット符号なし整数 | 必須      | 0 である必要があります。これは、トラック ボックスは 0 サンプルであり、トラック ボックスのサンプルの合計継続時間は 0 であるためです。 |

-------------------------------------

[ISO-14496-12] で定義されているように、"moov" ボックスは **HandlerBox ("hdlr")** を含む必要があり、次の制約があります。

| **フィールド名** | **フィールドの型**          | **必須** | **説明**   |
|----------------|-------------------------|---------------|-------------------|
| handler_type   | 32 ビット符号なし整数 | 必須      | "meta" である必要があります。 |

-------------------------------------

"stsd" ボックスは、[ISO-14496-12] で定義されているコーディング名前を持つ MetaDataSampleEntry ボックスを含む必要があります。  たとえば、SCTE-35 メッセージの場合は、コーディング名は "scte" である必要があります。

### <a name="223-movie-fragment-box-and-media-data-box"></a>2.2.3 Movie Fragment Box と Media Data Box

スパース トラックのフラグメントは、Movie Fragment Box ("moof") と Media Data Box ("mdat") で構成されます。

MovieFragmentBox ("moof") ボックスは、[MS-SSTR] で定義された以下のフィールドを含む **TrackFragmentExtendedHeaderBox ("uuid")** ボックスを含んでいなければなりません。

| **フィールド名**         | **フィールドの型**          | **必須** | **説明**                                                                               |
|------------------------|-------------------------|---------------|-----------------------------------------------------------------------------------------------|
| fragment_absolute_time | 64 ビット符号なし整数 | 必須      | イベントの到着時間でなければなりません。 この値により、メッセージと親トラックの位置が一致します。   |
| fragment_duration      | 64 ビット符号なし整数 | 必須      | イベントの継続時間でなければなりません。 継続時間は、値が不明であることを示す 0 にすることができます。 |

------------------------------------


MediaDataBox ("mdat") ボックスは次のような形式でなければなりません。

| **フィールド名**          | **フィールドの型**                   | **必須** | **説明**                                                                                                                                                                                                                                                                                                                                                                                                                                                                                 |
|-------------------------|----------------------------------|---------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| version                 | 32 ビット符号なし整数 (uimsbf) | 必須      | "mdat" ボックスの内容の形式を決定します。 認識されないバージョンは無視されます。 現在サポートされている値は 1 だけです。                                                                                                                                                                                                                                                                                                                                                      |
| id                      | 32 ビット符号なし整数 (uimsbf) | 必須      | メッセージのこのインスタンスを示します。 同じセマンティクスのメッセージは、同じ値を持つことになります。つまり、同じ ID を持ついずれか 1 つのイベント メッセージ ボックスを処理すれば十分です。                                                                                                                                                                                                                                                                                                            |
| presentation_time_delta | 32 ビット符号なし整数 (uimsbf) | 必須      | TrackFragmentExtendedHeaderBox で指定されている fragment_absolute_time と presentation_time_delta の合計は、イベントのプレゼンテーション時間でなければなりません。 プレゼンテーション時間と継続時間は、[ISO-14496-12] Annex I で定義されているように、タイプ 1 または 2 のストリーム アクセス ポイント (SAP) と一致している必要があります。HLS エグレスの場合は、プレゼンテーション時間と継続時間はセグメントの境界と一致する必要があります。 同じイベント ストリームに含まれる異なるイベント メッセージのプレゼンテーション時間と継続時間は、オーバーラップしてはなりません。 |
| message                 | byte array                       | 必須      | イベント メッセージ。 [SCTE-35] のメッセージはバイナリの splice_info_section() ですが、[SCTE-67] では他の値が推奨されます。 [SCTE-35] メッセージの場合、[SCTE-67] に準拠する HLS、Smooth、Dash クライアントにメッセージが送信されるためには、このフィールドは splice_info_section() でなければなりません。 [SCTE-35] メッセージの場合、バイナリの splice_info_section() が "mdat" ボックスのペイロードであり、base64 でエンコードされてはいません。                                                            |

------------------------------


### <a name="224-cancellation-and-updates"></a>2.2.4 キャンセルと更新
同じプレゼンテーション時間と ID で複数のメッセージを送信することにより、メッセージをキャンセルまたは更新することができます。  プレゼンテーション時間と ID によって、イベントが一意に識別されます。 プレロール制約を満たす特定のプレゼンテーション時間で受信された最後のメッセージが、処理されるメッセージになります。 更新されたメッセージは、以前に受信されたメッセージを置き換えます。  プレロール制約は 4 秒です。 プレゼンテーション時間の少なくとも 4 秒前に受信されたメッセージが処理されます。 


## <a name="3-timed-metadata-delivery"></a>3 時間指定メタデータの配信

イベント ストリーム データは、Media Services に対して非透過的です。 Media Services は、取り込みエンドポイントとクライアント エンドポイントの間で、3 種類の情報を渡すだけです。 [SCTE-67] およびクライアントのストリーミング プロトコルに準拠して、以下のプロパティがクライアントに配信されます。

1.  スキーム – メッセージのスキームを示す URN または URL。

2.  プレゼンテーション時間 – メディア タイムライン上でのイベントのプレゼンテーション時間。

3.  継続時間 – イベントの継続時間。

4.  ID – イベントの一意識別子 (オプション)。

5.  メッセージ – イベント データ。


## <a name="31-smooth-streaming-delivery"></a>3.1 Smooth Streaming の配信

[MS-SSTR] でスパース トラックの処理の詳細を確認してください。

#### <a name="smooth-client-manifest-example"></a>Smooth クライアントのマニフェストの例
~~~ xml
<?xml version=”1.0” encoding=”utf-8”?>
<SmoothStreamingMedia MajorVersion=”2” MinorVersion=”0” TimeScale=”10000000” IsLive=”true” Duration=”0”
  LookAheadFragmentCount=”2” DVRWindowLength=”6000000000”>

  <StreamIndex Type=”video” Name=”video” Subtype=”” Chunks=”0” TimeScale=”10000000”
    Url=”QualityLevels({bitrate})/Fragments(video={start time})”>
    <QualityLevel Index=”0” Bitrate=”230000”
      CodecPrivateData=”250000010FC3460B50878A0B5821FF878780490800704704DC0000010E5A67F840” FourCC=”WVC1”
      MaxWidth=”364” MaxHeight=”272”/>
    <QualityLevel Index=”1” Bitrate=”305000”
      CodecPrivateData=”250000010FC3480B50878A0B5821FF87878049080894E4A7640000010E5A67F840” FourCC=”WVC1”
      MaxWidth=”364” MaxHeight=”272”/>
    <c t=”0” d=”20000000” r=”300” />
  </StreamIndex>
  <StreamIndex Type=”audio” Name=”audio” Subtype=”” Chunks=”0” TimeScale=”10000000”
    Url=”QualityLevels({bitrate})/Fragments(audio={start time})”>
    <QualityLevel Index=”0” Bitrate=”96000” CodecPrivateData=”1000030000000000000000000000E00042C0”
      FourCC=”WMAP” AudioTag=”354” Channels=”2” SamplingRate=”44100” BitsPerSample=”16” PacketSize=”4459”/>
    <c t=”0” d=”20000000” r=”300” />
  </StreamIndex>
  <StreamIndex Type=”text” Name=”scte35-event-stream” Subtype=”DATA” Chunks=”0” TimeScale=”10000000”
    ParentStreamIndex=”video” ManifestOutput=”true” 
    Url=”QualityLevels({bitrate})/Fragments(captions={start time})”>
    <QualityLevel Index=”0” Bitrate=”0” CodecPrivateData=”” FourCC=””>
      <CustomAttributes>
        <Attribute Name=”Scheme” Value=”urn:scte:scte35:2013a:bin”/>
      </CustomAttributes>
    </QualityLevel>
    <!-- <f> contains base-64 encoded splice_info_section() message 
    <c t=”600000000” d=”300000000”>
<f>PD94bWwgdmVyc2lvbj0iMS4wIiBlbmNvZGluZz0idXRmLTgiPz48QWNxdWlyZWRTaWduYWwgeG1sbnM9InVybjpjYWJsZWxhYnM6bWQ6eHNkOnNpZ25hbGluZzozLjAiIGFjcXVpc2l0aW9uUG9pbnRJZGVudGl0eT0iRVNQTl9FYXN0X0FjcXVpc2l0aW9uX1BvaW50XzEiIGFjcXVpc2l0aW9uU2lnbmFsSUQ9IjRBNkE5NEVFLTYyRkExMUUxQjFDQTg4MkY0ODI0MDE5QiIgYWNxdWlzaXRpb25UaW1lPSIyMDEyLTA5LTE4VDEwOjE0OjI2WiI+PFVUQ1BvaW50IHV0Y1BvaW50PSIyMDEyLTA5LTE4VDEwOjE0OjM0WiIvPjxTQ1RFMzVQb2ludERlc2NyaXB0b3Igc3BsaWNlQ29tbWFuZFR5cGU9IjUiPjxTcGxpY2VJbnNlcnQgc3BsaWNlRXZlbnRJRD0iMzQ0NTY4NjkxIiBvdXRPZk5ldHdvcmtJbmRpY2F0b3I9InRydWUiIHVuaXF1ZVByb2dyYW1JRD0iNTUzNTUiIGR1cmF0aW9uPSJQVDFNMFMiIGF2YWlsTnVtPSIxIiBhdmFpbHNFeHBlY3RlZD0iMTAiLz48L1NDVEUzNVBvaW50RGVzY3JpcHRvcj48U3RyZWFtVGltZXM+PFN0cmVhbVRpbWUgdGltZVR5cGU9IkhTUyIgdGltZVZhbHVlPSI1MTUwMDAwMDAwMDAiLz48L1N0cmVhbVRpbWVzPjwvQWNxdWlyZWRTaWduYWw+</f>
    </c>
    <c t=”1200000000” d=”400000000”>      <f>PD94bWwgdmVyc2lvbj0iMS4wIiBlbmNvZGluZz0idXRmLTgiPz48QWNxdWlyZWRTaWduYWwgeG1sbnM9InVybjpjYWJsZWxhYnM6bWQ6eHNkOnNpZ25hbGluZzozLjAiIGFjcXVpc2l0aW9uUG9pbnRJZGVudGl0eT0iRVNQTl9FYXN0X0FjcXVpc2l0aW9uX1BvaW50XzEiIGFjcXVpc2l0aW9uU2lnbmFsSUQ9IjRBNkE5NEVFLTYyRkExMUUxQjFDQTg4MkY0ODI0MDE5QiIgYWNxdWlzaXRpb25UaW1lPSIyMDEyLTA5LTE4VDEwOjE0OjI2WiI+PFVUQ1BvaW50IHV0Y1BvaW50PSIyMDEyLTA5LTE4VDEwOjE0OjM0WiIvPjxTQ1RFMzVQb2ludERlc2NyaXB0b3Igc3BsaWNlQ29tbWFuZFR5cGU9IjUiPjxTcGxpY2VJbnNlcnQgc3BsaWNlRXZlbnRJRD0iMzQ0NTY4NjkxIiBvdXRPZk5ldHdvcmtJbmRpY2F0b3I9InRydWUiIHVuaXF1ZVByb2dyYW1JRD0iNTUzNTUiIGR1cmF0aW9uPSJQVDFNMFMiIGF2YWlsTnVtPSIxIiBhdmFpbHNFeHBlY3RlZD0iMTAiLz48L1NDVEUzNVBvaW50RGVzY3JpcHRvcj48U3RyZWFtVGltZXM+PFN0cmVhbVRpbWUgdGltZVR5cGU9IkhTUyIgdGltZVZhbHVlPSI1MTYyMDAwMDAwMDAiLz48L1N0cmVhbVRpbWVzPjwvQWNxdWlyZWRTaWduYWw+</f>
    </c>
  </StreamIndex>
</SmoothStreamingMedia> 
~~~ 

## <a name="32--apple-hls-delivery"></a>3.2 Apple HLS の配信
Apple HTTP ライブ ストリーミング (HLS) の時間指定メタデータは、カスタム M3U タグ内のセグメント プレイリストに埋め込むことができます。  アプリケーション レイヤーでは、M3U プレイリストを解析して、M3U タグを処理することができます。 Azure Media Services は、[HLS] で定義されている EXT-X-CUE タグに時間指定メタデータを埋め込みます。  現在、EXT-X-CUE タグは、ADI3 タイプのメッセージ用に DynaMux によって使われています。  SCTE-35 メッセージおよび非 SCTE-35 メッセージをサポートするには、以下で定義するように、EXT-X-CUE タグの属性を設定します。

| **属性名** | **Type**                      | **必須**                             | **説明**                                                                                                                                                                                                                                                                      |
|--------------------|-------------------------------|-------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| CUE                | 引用符で囲まれた文字列                 | 必須                                  | [IETF RFC 4648](https://tools.ietf.org/html/rfc4648) で説明されているように、base64 文字列としてエンコードされたメッセージ。 [SCTE-35] メッセージの場合、これは base64 でエンコードされた splice_info_section() です。                                                                                                |
| TYPE               | 引用符で囲まれた文字列                 | 必須                                  | メッセージのスキームを示す URN または URL。 [SCTE-35] メッセージの場合、タイプは特別な値 "scte35" です。                                                                                                                                |
| ID                 | 引用符で囲まれた文字列                 | 必須                                  | イベントの一意識別子。 メッセージが取り込まれときに ID が指定されていない場合は、Azure Media Services が一意 ID を生成します。                                                                                                                                          |
| DURATION           | 10 進浮動小数点数 | 必須                                  | イベントの継続時間。 わからない場合は、0 にする必要があります。 単位は秒の小数部です。                                                                                                                                                                                           |
| ELAPSED            | 10 進浮動小数点数 | オプション、ただしスライディング ウィンドウの場合は必須。 | スライディング プレゼンテーション ウィンドウをサポートするためにシグナルを繰り返す場合、このフィールドは、イベントが開始してから経過したプレゼンテーション時間の長さでなければなりません。 単位は秒の小数部です。 この値はスプライスまたはセグメントで指定されている元の継続時間を超えてもかまいません。 |
| TIME               | 10 進浮動小数点数 | 必須                                  | イベントのプレゼンテーション時間。 単位は秒の小数部です。                                                                                                                                                                                                                    |


HLS プレーヤーのアプリケーション レイヤーは、TYPE を使って、メッセージの形式を識別し、メッセージをデコードし、必要な時間変換を適用して、イベントを処理します。  イベントは、イベントのタイムスタンプに従って、親トラックのセグメント プレイリストの時間と同期されます。  イベントは、最も近いセグメント (#EXTINF タグ) の前に挿入されます。

#### <a name="hls-segment-playlist-example"></a>HLS セグメント プレイリストの例
~~~
#EXTM3U
#EXT-X-VERSION:4
#EXT-X-ALLOW-CACHE:NO
#EXT-X-MEDIA-SEQUENCE:346
#EXT-X-TARGETDURATION:6
#EXT-X-I-FRAMES-ONLY
#EXT-X-PROGRAM-DATE-TIME:2018-12-13T15:54:19.462Z
#EXTINF:4.000000,no-desc
KeyFrames(video_track=15447164594627600,format=m3u8-aapl)
#EXTINF:6.000000,no-desc
KeyFrames(video_track=15447164634627600,format=m3u8-aapl)
#EXT-X-CUE:ID="1026",TYPE="scte35",DURATION=30.000000,TIME=1544716520.022760,CUE="/DAlAAAAAAAAAP/wFAUAAAQCf+//KRjAfP4AKTLgAAAAAAAAVYsh2w=="
#EXTINF:6.000000,no-desc
KeyFrames(video_track=15447165474627600,format=m3u8-aapl)
~~~

#### <a name="hls-message-handling"></a>HLS メッセージの処理

イベントは、各ビデオ トラックおよびオーディオ トラックのセグメント プレイリスト内でシグナル通知されます。[HLS] で要求されているように、EXT-X-CUE タグの位置は、常に、TIME および DURATION 属性によって参照される最初の HLS セグメントの直前 (スプライス アウトまたはセグメント開始の場合)、または最後の HLS セグメントの直後 (スプライス インまたはセグメント終了の場合) でなければなりません。

スライディング プレゼンテーション ウィンドウが有効になっているときは、[HLS] で要求されているように、EXT-X-CUE タグはスプライスまたはセグメントがセグメント プレイリストで常に完全に記述されるのに十分な回数だけ繰り返されなければならず、ELAPSED 属性を使ってスプライスまたはセグメントがアクティブになっていた時間が示されなければなりません。

スライディング プレゼンテーション ウィンドウが有効になっている場合、EXT-X-CUE タグで参照されているメディア時間がスライディング プレゼンテーション ウィンドウから出たときは、EXT-X-CUE タグがセグメント プレイリストから削除されます。

## <a name="33--dash-delivery"></a>3.3 DASH の配信
[DASH] では、イベントをシグナル通知する 3 つの方法が用意されています。

1.  MPD でシグナル通知されるイベント
2.  Event Message Box ("emsg") を使って Representation の帯域内でシグナル通知されるイベント
3.  1 と 2 の組み合わせ

MPD でシグナル通知されるイベントは、MPD がダウンロードされるとすぐにクライアントはすべてのイベントにアクセスできるため、VOD ストリーミングに便利です。 帯域内のソリューションは、クライアントは MPD を再度ダウンロードする必要がないため、ライブ ストリーミングに便利です。 時間ベースのセグメント化では、クライアントは現在のセグメントの継続時間とタイムスタンプを加算することにより、次のセグメントの URL を決定します。 その要求が失敗した場合は、クライアントは連続していないものと想定して MPD をダウンロードしますが、それ以外の場合は、MPD をダウンロードしないでストリーミングを続けます。

Azure Media Services は、MPD でのシグナル通知と、Event Message Box を使う帯域内シグナル通知の両方を行います。

#### <a name="mpd-signaling"></a>MPD のシグナル通知

イベントは、Period 要素内に出現する EventStream 要素を使って、MPD でシグナル通知されます。

EventStream 要素には次の属性があります。

| **属性名** | **Type**                | **必須** | **説明**                                                                                                                                                                                                                                                                                   |
|--------------------|-------------------------|---------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| scheme_id_uri      | 文字列                  | 必須      | メッセージのスキームを示します。 スキームは、Live Server Manifest Box の Scheme 属性の値に設定されます。 値は、メッセージのスキームを示す URN または URL である必要があります (例: "urn:scte:scte35:2013a:bin")。                                                                |
| value              | 文字列                  | 省略可能      | メッセージのセマンティクスをカスタマイズするためにスキームの所有者によって使われる追加の文字列値。 スキームが同じ複数のイベント ストリームを区別するため、値はイベント ストリームの名前に設定しなければなりません (Smooth の取り込みの場合は trackName、RTMP の取り込みの場合は AMF メッセージ名)。 |
| タイムスケール          | 32 ビット符号なし整数 | 必須      | "emsg" ボックス内の時間フィールドと継続時間フィールドのタイムスケール (1 秒間のティック数)。                                                                                                                                                                                                       |


EventStream 要素には 0 個以上の Event 要素が含まれており、次の属性があります。

| **属性名**  | **Type**                | **必須** | **説明**                                                                                                                                                                                                             |
|---------------------|-------------------------|---------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| presentation_time   | 64 ビット符号なし整数 | 省略可能      | Period の開始を基準とするイベントのメディア プレゼンテーション時間でなければなりません。 プレゼンテーション時間と継続時間は、[ISO-14496-12] Annex I で定義されているように、タイプ 1 または 2 のストリーム アクセス ポイント (SAP) と一致している必要があります。 |
| duration            | 32 ビット符号なし整数 | 省略可能      | イベントの継続時間。 継続時間が不明の場合、この属性は省略しなければなりません。                                                                                                                                                 |
| id                  | 32 ビット符号なし整数 | 省略可能      | メッセージのこのインスタンスを示します。 同じセマンティクスを持つメッセージには、同じ値が設定されている必要があります。 メッセージが取り込まれときに ID が指定されていない場合は、Azure Media Services が一意 ID を生成します。             |
| Event element value | 文字列                  | 必須      | [IETF RFC 4648](https://tools.ietf.org/html/rfc4648) で説明されているように、base64 文字列として表されたイベント メッセージ。                                                                                                                   |

#### <a name="xml-syntax-and-example-for-dash-manifest-mpd-signaling"></a>DASH マニフェスト (MPD) シグナル通知の XML 構文と例

~~~ xml
<!-- XML Syntax -->
<xs:complexType name=”EventStreamType”>
  <xs:sequence>
    <xs:element name=”Event” type=”EventType” minOccurs=”0” maxOccurs=”unbounded”/>
    <xs:any namespace=”##other” processContents=”lax” minOccurs=”0” maxOccurs=”unbounded”/>
  </xs:sequence>
  <xs:attribute ref=”xlink:href”/>
  <xs:attribute ref=”xlink:actuate” default=”onRequest”/>
  <xs:attribute name=”schemeIdUri” type=”xs:anyURI” use=”required”/>
  <xs:attribute name=”value” type=”xs:string”/>
  <xs:attribute name=”timescale” type=”xs:unsignedInt”/>
</xs:complexType>
<!-- Event -->
<xs:complexType name=”EventType”>
  <xs:sequence>
    <xs:any namespace=”##other” processContents=”lax” minOccurs=”0” maxOccurs=”unbounded”/>
  </xs:sequence>
  <xs:attribute name=”presentationTime” type=”xs:unsignedLong” default=”0”/>
  <xs:attribute name=”duration” type=”xs:unsignedLong”/>
  <xs:attribute name=”id” type=”xs:unsignedInt”/>
  <xs:anyAttribute namespace=”##other” processContents=”lax”/>
</xs:complexType><?xml version=”1.0” encoding=”utf-8”?>


<!-- Example Section in MPD -->
  <EventStream schemeIdUri="urn:scte:scte35:2013a:bin" value="scte35_track_001_000" timescale="10000000">
        <Event presentationTime="15447165200227600" duration="300000000" id="1026">/DAlAAAAAAAAAP/wFAUAAAQCf+//KRjAfP4AKTLgAAAAAAAAVYsh2w==</Event>
        <Event presentationTime="15447166250227600" duration="300000000" id="1027">/DAlAAAAAAAAAP/wFAUAAAQDf+//KaeGwP4AKTLgAAAAAAAAn75a3g==</Event>
        <Event presentationTime="15447167300227600" duration="600000000" id="1028">/DAlAAAAAAAAAP/wFAUAAAQEf+//KjkknP4AUmXAAAAAAAAAWcEldA==</Event>
        <Event presentationTime="15447168350227600" duration="600000000" id="1029">/DAlAAAAAAAAAP/wFAUAAAQFf+//KslyqP4AUmXAAAAAAAAAvKNt0w==</Event>
        <Event presentationTime="15447169400227600" duration="300000000" id="1030">/DAlAAAAAAAAAP/wFAUAAAQGf+//K1mIvP4AKTLgAAAAAAAAt2zEbw==</Event>
        <Event presentationTime="15447170450227600" duration="600000000" id="1031">/DAlAAAAAAAAAP/wFAUAAAQHf+//K+hc/v4AUmXAAAAAAAAANNRzVw==</Event>
    </EventStream>
~~~

>[!NOTE]
>presentationTime はイベントのプレゼンテーション時間であり、メッセージの到着時間ではないことに注意してください。
>

### <a name="431-in-band-event-message-box-signaling"></a>4.3.1 帯域内 Event Message Box のシグナル通知
帯域内イベント ストリームでは、MPD は適応セット レベルで InbandEventStream 要素を持っている必要があります。  この要素には必須の schemeIdUri 属性とオプションの timescale 属性があり、これらも Event Message Box ("emsg") に出現します。  MPD で定義されていないスキーム識別子を含む Event Message Box が存在してはなりません。 DASH クライアントは、MPD で定義されていないスキームを含む Event Message Box を検出した場合は、それを無視する必要があります。
Event Message Box ("emsg") では、メディア プレゼンテーション時間に関連する汎用的なイベントのシグナル通知が提供されます。 "emsg" が存在する場合は、すべての "moof" ボックスより前に配置されることになります。

### <a name="dash-event-message-box-emsg"></a>DASH の Event Message Box "emsg"
~~~
Box Type: `emsg’
Container: Segment
Mandatory: No
Quantity: Zero or more
~~~

~~~ c
aligned(8) class DASHEventMessageBox extends FullBox(‘emsg’, version = 0, flags =0) 
{
    string scheme_id_uri;
    string value;
    unsigned int(32) timescale;
    unsigned int(32) presentation_time_delta;
    unsigned int(32) event_duration;
    unsigned int(32) id;
    unsigned int(8) message_data[];
}
~~~

DASHEventMessageBox のフィールドの定義は以下のとおりです。

| **フィールド名**          | **フィールドの型**          | **必須** | **説明**                                                                                                                                                                                                                                                                                                                                                    |
|-------------------------|-------------------------|---------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| scheme_id_uri           | 文字列                  | 必須      | メッセージのスキームを示します。 スキームは、Live Server Manifest Box の Scheme 属性の値に設定されます。 値は、メッセージのスキームを示す URN または URL である必要があります。 [SCTE-35] メッセージのこのフィールドは特別な値 "urn:scte:scte35:2013a:bin" ですが、[SCTE-67] では別の値が推奨されます。 |
| 値                   | 文字列                  | 必須      | メッセージのセマンティクスをカスタマイズするためにスキームの所有者によって使われる追加の文字列値。 スキームが同じ複数のイベント ストリームを区別するため、値はイベント ストリームの名前に設定されます (Smooth の取り込みの場合は trackName、RTMP の取り込みの場合は AMF メッセージ名)。                                                                  |
| タイムスケール               | 32 ビット符号なし整数 | 必須      | "emsg" ボックス内の時間フィールドと継続時間フィールドのタイムスケール (1 秒間のティック数)。                                                                                                                                                                                                                                                                        |
| Presentation_time_delta | 32 ビット符号なし整数 | 必須      | イベントのプレゼンテーション時間と、このセグメントでの最も早いプレゼンテーション時刻の間を表す、メディア プレゼンテーション時間の差分。 プレゼンテーション時間と継続時間は、[ISO-14496-12] Annex I で定義されているように、タイプ 1 または 2 のストリーム アクセス ポイント (SAP) と一致している必要があります。                                                                                            |
| event_duration          | 32 ビット符号なし整数 | 必須      | イベントの継続時間、または継続時間が不明であることを示す 0xFFFFFFFF。                                                                                                                                                                                                                                                                                          |
| Id                      | 32 ビット符号なし整数 | 必須      | メッセージのこのインスタンスを示します。 同じセマンティクスを持つメッセージには、同じ値が設定されている必要があります。 メッセージが取り込まれときに ID が指定されていない場合は、Azure Media Services が一意 ID を生成します。                                                                                                                                                    |
| Message_data            | byte array              | 必須      | イベント メッセージ。 [SCTE-35] のメッセージ データはバイナリの splice_info_section() ですが、[SCTE-67] では他のデータが推奨されます。                                                                                                                                                                                                                                 |

### <a name="332-dash-message-handling"></a>3.3.2 DASH メッセージの処理

イベントは、ビデオ トラックとオーディオ トラックの両方について、"emsg" ボックスの帯域内でシグナル通知されます。  シグナル通知は、presentation_time_delta が 15 秒以下であるすべてのセグメント要求に対して発生します。 スライディング プレゼンテーション ウィンドウが有効になっている場合は、イベント メッセージのプレゼンテーション時間と継続時間の合計がマニフェスト内のメディア データの時間より小さいと、イベント メッセージが MPD から削除されます。  つまり、イベント メッセージで参照されているメディア時間がスライディング プレゼンテーション ウィンドウから出ると、そのイベント メッセージはマニフェストから削除されます。

## <a name="4-scte-35-ingest"></a>4 SCTE-35 の取り込み

[SCTE-35] メッセージは、Smooth の取り込みの場合はスキーム **"urn:scte:scte35:2013a:bin"** を使って、RTMP の取り込みの場合は **"scte35"** 型を使って、バイナリ形式で取り込まれます。 MPEG-2 トランスポート ストリーム プレゼンテーション タイムスタンプ (PTS) に基づく [SCTE-35] のタイミングの変換を容易にするため、PTS (pts_time + splice_time() の pts_adjustment) とメディア タイムラインの間のマッピングが、イベント プレゼンテーション時間 (Smooth の取り込みの場合は fragment_absolute_time フィールド、RTMP の取り込みの場合は time フィールド) によって提供されます。 33 ビットの PTS 値は約 26.5 時間ごとにロールオーバーされるため、マッピングが必要になります。

Smooth Streaming の取り込みでは、[SCTE-35] の表 8-1 で定義されている **splice_info_section()** が Media Data Box ("mdat") に含まれていなければなりません。 RTMP の取り込みの場合、AMF メッセージの cue 属性は、base64encoded **splice_info_section()** に設定されます。 メッセージが上で説明した形式の場合、メッセージは [SCTE-67] に従って HLS、Smooth、Dash クライアントに送信されます。


## <a name="5-references"></a>5 参照

**[SCTE-35]** ANSI/SCTE 35 2013a – ケーブル接続のデジタル プログラム挿入キューイング メッセージ、2013a

**[SCTE-67]** ANSI/SCTE 67 2014 – SCTE 35 で推奨される方法:ケーブル接続のデジタル プログラム挿入キューイング メッセージ

**[DASH]** ISO/IEC 23009-1 2014 – 情報技術 – HTTP 経由のダイナミック アダプティブ ストリーミング (DASH) – パート 1:メディア プレゼンテーションの説明とセグメント形式、第 2 版

**[HLS]** ["HTTP ライブ ストリーミング"、draft-pantos-http-live-streaming-14、2014 年 10 月 14 日](http://tools.ietf.org/html/draft-pantos-http-live-streaming-14)

**[MS-SSTR]** ["Microsoft Smooth Streaming プロトコル"、2014 年 5 月 15 日](https://download.microsoft.com/download/9/5/E/95EF66AF-9026-4BB0-A41D-A4F81802D92C/%5bMS-SSTR%5d.pdf)

**[AMF0]** ["アクション メッセージ形式 AMF0"](https://download.macromedia.com/pub/labs/amf/amf0_spec_121207.pdf)

**[LIVE-FMP4]** [Azure Media Services の Fragmented MP4 ライブ取り込み仕様](https://docs.microsoft.com/azure/media-services/media-services-fmp4-live-ingest-overview)

**[ISO-14496-12]** ISO/IEC 14496-12:パート 12 ISO ベースのメディア ファイル形式、第 4 版、2012 年 7 月 15 日。

**[RTMP]** ["Adobe の Real-Time Messaging Protocol"、2012 年 12 月 21 日](https://www.adobe.com/devnet/rtmp.html) 

------------------------------------------

## <a name="next-steps"></a>次の手順
Media Services のラーニング パスを確認します。

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>フィードバックの提供
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]
