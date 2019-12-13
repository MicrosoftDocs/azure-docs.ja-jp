---
title: Azure Media Services - HEVC のための Smooth Streaming Protocol (MS-SSTR) の変更 | Microsoft Docs
description: この仕様では、Azure Media Services での HEVC による Fragmented MP4 ベースのライブ ストリーミングのプロトコルとフォーマットについて説明します。 この記事では、HEVC を配信するために必要な変更のみを記載しています。ただし、明確化の目的だけにテキストをコピーした部分は、“(変更なし)” と示しています。
services: media-services
documentationcenter: ''
author: johndeu
manager: femila
editor: ''
ms.assetid: f27d85de-2cb8-4269-8eed-2efb566ca2c6
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/19/2019
ms.author: johndeu
ms.openlocfilehash: 6dd7e0dc7e58f33f952aa5531773a84ebd31a163
ms.sourcegitcommit: 8bd85510aee664d40614655d0ff714f61e6cd328
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/06/2019
ms.locfileid: "74887868"
---
# <a name="smooth-streaming-protocol-ms-sstr-amendment-for-hevc"></a>HEVC のための Smooth Streaming Protocol (MS-SSTR) の変更 

## <a name="1-introduction"></a>1 Introduction (はじめに) 

この記事では、HEVC でエンコードされたビデオのスムーズ ストリーミングを有効化するため、Smooth Streaming Protocol [MS-SSTR] の仕様書に適用される変更についての詳細を提供します。 この仕様では、HEVC ビデオ コーデックを配信するために必要な変更のみを説明します。 この記事では、[MS SSTR] 仕様書と同じ番号付け方法を採用しています。 記事全体でを通じて示されている空の見出しは、閲覧者を [MS SSTR] 仕様書内の該当する位置に向かわせるために提供されています。  “(変更なし)” は、明確化の目的だけにテキストがコピーされていることを示します。

この記事では、Smooth Streaming マニフェストでの HEVC ビデオ コーデックの信号通知のための技術的な実装要件を提供します ('hev1' または 'hvc1' 形式のトラックを使用)。引用規格は、現在の MPEG 規格を参照するように更新されています。この規格には、最新の仕様と一致するように更新されている、HEVC、HEVC の Common Encryption、および ISO Base Media File Format のボックス名が含まれます。 

参照先の Smooth Streaming Protocol [MS-SSTR] の仕様書は、オーディオやビデオなどのデジタル メディアを、次の方法で、ライブ配信およびオンデマンド配信するためのワイヤ形式について説明しています: エンコーダーから Web サーバー、サーバーから別のサーバー、およびサーバーから HTTP クライアント。
HTTP 経由での MPEG-4 ([[MPEG4-RA])](https://go.microsoft.com/fwlink/?LinkId=327787) ベースのデータ構造の配信を使用すると、圧縮されたメディア コンテンツの異なる品質レベル間のシームレスな切り替えがほぼリアルタイムで可能になります。 その結果、クライアント コンピューターまたはデバイスのネットワークとビデオ レンダリングの条件が変更された場合でも、HTTP クライアントのエンド ユーザーの再生エクスペリエンスは変わりません。

## <a name="11-glossary"></a>1.1 Glossary (用語集) 

次の用語は *[MS GLOS]* で定義されています。

>   **グローバル一意識別子 (GUID) ユニバーサル一意識別子 (UUID)**

次の用語は、このドキュメントに固有のものです。

>  **コンポジション時間:** [[ISO/IEC-14496-12]](https://go.microsoft.com/fwlink/?LinkId=183695) で定義されている、サンプルがクライアントで示される時間。
> 
>   **CENC**: Common Encryption、[ISO/IEC 23001-7] Second Edition で定義。
> 
>   **デコード時間:** [[ISO/IEC 14496-12:2008]](https://go.microsoft.com/fwlink/?LinkId=183695) で定義されている、クライアント上でサンプルのデコードにかかる時間。

**フラグメント:** 1 つまたは複数の**サンプル**を構成する**メディア**の個別にダウンロード可能な単位。

>   **HEVC:** High Efficiency Video Coding、[ISO/IEC 23008-2] で定義。
> 
>   **マニフェスト**: クライアントが**メディア**を要求することを許可する、**プレゼンテーション**に関するメタデータ。 **メディア:** **プレゼンテーション**を再生するためにクライアントによって使用されるオーディオ、ビデオ、およびテキストの圧縮データ。 **メディア フォーマット:** オーディオまたはビデオを圧縮された**サンプル**として表すために適切に定義されたフォーマット。
> 
>   **プレゼンテーション** 1 つのムービーを再生するために必要なすべての**ストリーム**と関連メタデータのセット。 **要求:** [[RFC2616]](https://go.microsoft.com/fwlink/?LinkId=90372) に定義されている、クライアントからサーバーに送信される HTTP メッセージ **応答:** [[RFC2616]](https://go.microsoft.com/fwlink/?LinkId=90372) で定義されている、サーバーからクライアントに送信される HTTP メッセージ
> 
>   **サンプル:** **メディア**が格納および処理される最小の基本単位 (フレームなど)。
> 
>   **MAY、SHOULD、MUST、SHOULD NOT、MUST NOT:** これらの用語 (すべて大文字) は、[[RFC2119]](https://go.microsoft.com/fwlink/?LinkId=90317) の説明に従って使用されます。省略可能な動作のすべてのステートメントには MAY、SHOULD、または SHOULD NOT が使用されます。

## <a name="12-references"></a>1.2 References (参照)

>   Microsoft Open Specifications ドキュメントへの参照には、発行年は含まれていません。これは、リンクが頻繁に更新されるドキュメントの最新バージョンを参照しているためです。 その他のドキュメントへの参照には、発行年が含まれています (可能な場合)。

### <a name="121-normative-references"></a>1.2.1 Normative References (標準リファレンス) 

>  [MS-SSTR] Smooth Streaming Protocol *v20140502* [https://msdn.microsoft.com/library/ff469518.aspx](https://msdn.microsoft.com/library/ff469518.aspx)
> 
>   [ISO/IEC 14496-12] International Organization for Standardization, "Information technology -- Coding of audio-visual objects -- Part 12:ISO Base Media File Format", ISO/IEC 14496-12:2014, Edition 4, Plus Corrigendum 1, Amendments 1 & 2.
>   <https://standards.iso.org/ittf/PubliclyAvailableStandards/c061988_ISO_IEC_14496-12_2012.zip>
> 
>   [ISO/IEC 14496-15] International Organization for Standardization, "Information technology -- Coding of audio-visual objects -- Part 15:Carriage of NAL unit structured video in the ISO Base Media File Format", ISO 14496-15:2015, Edition 3.
>   <https://www.iso.org/iso/home/store/catalogue_tc/catalogue_detail.htm?csnumber=65216>
> 
>   [ISO/IEC 23008-2] Information technology -- High efficiency coding and media   delivery in heterogeneous environments -- Part 2:High efficiency video   coding:2013 or newest edition   <https://standards.iso.org/ittf/PubliclyAvailableStandards/c035424_ISO_IEC_23008-2_2013.zip>
> 
>   [ISO/IEC 23001-7] Information technology — MPEG systems technologies — Part   7:Common encryption in ISO base media file format files, CENC Edition   2:2015 <https://www.iso.org/iso/catalogue_detail.htm?csnumber=65271>
> 
>   [RFC-6381] IETF RFC-6381, “The 'Codecs' and 'Profiles' Parameters for   "Bucket" Media Types” <https://tools.ietf.org/html/rfc6381>
> 
>   [MPEG4-RA] The MP4 Registration Authority, "MP4REG", [http://www.mp4ra.org](https://go.microsoft.com/fwlink/?LinkId=327787)
> 
>   [RFC2119] Bradner, S., "Key words for use in RFCs to Indicate Requirement   Levels", BCP 14, RFC 2119, March 1997,   [https://www.rfc-editor.org/rfc/rfc2119.txt](https://go.microsoft.com/fwlink/?LinkId=90317)

### <a name="122-informative-references"></a>1.2.2 Informative References (参考文献) 

>   [MS-GLOS] Microsoft Corporation, "*Windows Protocols Master Glossary*."
> 
>   [RFC3548] Josefsson, S., Ed., "The Base16, Base32, and Base64 Data   Encodings", RFC 3548, July 2003, [https://www.ietf.org/rfc/rfc3548.txt](https://go.microsoft.com/fwlink/?LinkId=90432)
> 
>   [RFC5234] Crocker, D., Ed., and Overell, P., "Augmented BNF for Syntax   Specifications:ABNF", STD 68, RFC 5234, January 2008,   [https://www.rfc-editor.org/rfc/rfc5234.txt](https://go.microsoft.com/fwlink/?LinkId=123096)


## <a name="13-overview"></a>1.3 Overview (概要) 

>   以下では、HEVC の配信に必要な Smooth Streaming の仕様に加えられた変更のみが規定されています。 変更されていないセクション ヘッダーは、参照先の Smooth Streaming [MS SSTR] 仕様書内の場所を維持するために一覧表示されています。

## <a name="14-relationship-to-other-protocols"></a>1.4 Relationship to Other Protocols (その他のプロトコルとの関係) 

## <a name="15-prerequisitespreconditions"></a>1.5 Prerequisites/Preconditions (前提条件/前提条件) 

## <a name="16-applicability-statement"></a>1.6 Applicability Statement (適用性に関するステートメント) 

## <a name="17-versioning-and-capability-negotiation"></a>1.7 Versioning and Capability Negotiation (バージョン管理と機能のネゴシエーション) 

## <a name="18-vendor-extensible-fields"></a>1.8 Vendor-Extensible Fields (ベンダーが拡張可能なフィールド) 

>   次のメソッドは、HEVC ビデオ形式を使用してストリームを識別するために使用するものとします。
> 
>   * **Custom Descriptive Codes for Media Formats:** この機能は、セクション *2.2.2.5* で規定されているように、**FourCC** フィールドで提供されます。
>   [[ISO/IEC-14496-12]](https://go.microsoft.com/fwlink/?LinkId=183695) で規定されているように、実装者は拡張コードを MPEG4-RA に登録することで、拡張機能が競合しないことを保証できます。

## <a name="19-standards-assignments"></a>1.9 Standards Assignments (標準の割り当て) 

## <a name="2-messages"></a>2 Messages (メッセージ) 

## <a name="21-transport"></a>2.1 Transport (転送)

## <a name="22-message-syntax"></a>2.2 Message Syntax (メッセージの構文) 

### <a name="221-manifest-request"></a>2.2.1 Manifest Request (マニフェストの要求) 

### <a name="222-manifest-response"></a>2.2.2 Manifest Response (マニフェストの応答) 

#### <a name="2221-smoothstreamingmedia"></a>2.2.2.1 SmoothStreamingMedia 

>   **MinorVersion (変数):** Manifest Response メッセージのマイナー バージョン。 2 に設定する必要があります。 (変更なし)
> 
>   **TimeScale (変数):** Duration 属性のタイム スケール。1 秒間のインクリメント数として指定されます。 既定値は
> 1. (変更なし)
> 
>    ビデオ フレームと小数桁のフレーム レートのビデオ (30/1.001 Hz など) を含むフラグメントの正確な期間を表すための推奨値は、90000 です。

#### <a name="2222-protectionelement"></a>2.2.2.2 ProtectionElement 

ProtectionElement は、Common Encryption (CENC) がビデオまたはオーディオ ストリームに適用されている場合に存在するものとします。 HEVC 暗号化ストリームは、Common Encryption 2nd Edition [ISO/IEC 23001-7] に準拠するものとします。 VCL NAL ユニットではスライス データのみが暗号化されるものとします。

#### <a name="2223-streamelement"></a>2.2.2.3 StreamElement 

>   **StreamTimeScale (変数):** このストリーム内の期間と時間の値のタイム スケール。1 秒間のインクリメント数として指定されます。 HEVC ストリームには、90000 の値を使用することをお勧めします。 オーディオ ストリームには、波形サンプル周波数と一致する値 (48000 または 44100 など) を使用することをお勧めします。

##### <a name="22231-streamprotectionelement"></a>2.2.2.3.1 StreamProtectionElement

#### <a name="2224-urlpattern"></a>2.2.2.4 UrlPattern 

#### <a name="225-trackelement"></a>2.2.5 TrackElement 

>   **FourCC (変数):** 各サンプルにどのメディア フォーマットが使用されているかを識別する 4 文字のコード。 次の値の範囲は、次のセマンティックな意味で予約されています。
> 
> * "hev1": このトラックのビデオ サンプルは、[ISO/IEC-14496-15] で規定されている 'hev1' サンプル記述形式を使用する、HEVC ビデオを使用します。
>
> * "hvc1":このトラックのビデオ サンプルは、[ISO/IEC-14496-15] で規定されている 'hvc1' サンプル記述形式を使用する、HEVC ビデオを使用します。
> 
>   **CodecPrivateData (変数):** メディア フォーマットに固有のパラメーターと、トラック内のすべてのサンプルに共通のパラメーターを指定するデータ。16 進数でコード化されたバイトの文字列として表されます。 バイト シーケンスのフォーマットとセマンティックな意味は、次のように、**FourCC** フィールドの値によって異なります。
> 
>   * TrackElement で HEVC ビデオを記述する場合、**FourCC** フィールドは **"hev1"** または **"hvc1"** と等しいものとします。
> 
>   **CodecPrivateData** フィールドには、ABNF [[RFC5234]](https://go.microsoft.com/fwlink/?LinkId=123096) で規定されているとおり (MS SSTR から変更なし)、次のバイト シーケンスの 16 進数でコード化された文字列表現が含まれているものとします。
> 
>   * %x00 %x00 %x00 %x01 SPSField %x00 %x00 %x00 %x01 PPSField
> 
>   * SPSField には、Sequence Parameter Set (SPS) が含まれています。
> 
>   * SPSField には、Slice Parameter Set (PPS) が含まれています。
> 
>   注:Video Parameter Set (VPS) は CodecPrivateData には含まれていませんが、'hvcC' ボックスに格納されているファイルのファイル ヘッダーに含まれている必要があります。 Smooth Streaming プロトコルを使用しているシステムは、カスタム属性 “codecs” を使用して追加のデコード パラメーター (HEVC 層など) を信号で知らせる必要があります。

##### <a name="22251-customattributeselement"></a>2.2.2.5.1 CustomAttributesElement 

#### <a name="226-streamfragmentelement"></a>2.2.6 StreamFragmentElement 

>   **SmoothStreamingMedia の MajorVersion** フィールドを 2 に設定する必要があります。また、**MinorVersion** フィールドを 2 に設定する必要があります。 (変更なし)

##### <a name="22261-trackfragmentelement"></a>2.2.2.6.1 TrackFragmentElement 

### <a name="223-fragment-request"></a>2.2.3 Fragment Request (フラグメント要求) 

>   **メモ**:**MinorVersion** 2 と 'hev1' または 'hvc1' に要求される既定のメディア フォーマットは、[ISO/IEC 14496-12] ISO Base Media File Format Fourth Edition および、[ISO/IEC 23001-7] Common Encryption Second Edition で規定されている、'iso8' ブランド ISO Base Media File Format です。

### <a name="224-fragment-response"></a>2.2.4 Fragment Response (フラグメント応答) 

#### <a name="2241-moofbox"></a>2.2.4.1 MoofBox 

#### <a name="2242-mfhdbox"></a>2.2.4.2 MfhdBox 

#### <a name="2243-trafbox"></a>2.2.4.3 TrafBox 

#### <a name="2244-tfxdbox"></a>2.2.4.4 TfxdBox 

>   **TfxdBox** は非推奨になっています。また、その関数は、[ISO/IEC 14496-12] セクション 8.8.12 で規定されている、Track Fragment Decode Time Box (‘tfdt’) に置き換えられました。
> 
>   **メモ**:クライアントは、Track Run Box (‘trun’) にリストされているサンプル期間を合計するか、サンプル時間を規定のサンプル期間と乗算することで、フラグメントの期間を計算できます。 'tfdt' の baseMediaDecodeTime とフラグメント期間を足すと、次のフラグメントの URL time パラメーターと等しくなります。
> 
>   Movie Fragment Box で参照される最初のサンプルの Track Fragment Decode Time に対応する UTC 時間を示すには、必要に応じて、Producer Reference Time Box (‘prft’) を、Movie Fragment Box (‘moof’) の前に挿入する必要があります。[ISO/IEC 14496-12] セクション 8.16.5 で規定。

#### <a name="2245-tfrfbox"></a>2.2.4.5 TfrfBox 

>   **TfrfBox** は非推奨になっています。また、その関数は、[ISO/IEC 14496-12] セクション 8.8.12 で指定されている、Track Fragment Decode Time Box (‘tfdt’) に置き換えられました。
> 
>   **メモ**:クライアントは、Track Run Box (‘trun’) にリストされているサンプル期間を合計するか、サンプル時間を規定のサンプル期間と乗算することで、フラグメントの期間を計算できます。 'tfdt' の baseMediaDecodeTime とフラグメント期間を足すと、次のフラグメントの URL time パラメーターと等しくなります。 Look ahead アドレスは、ライブ ストリーミングを遅延させるため、非推奨となりました。

#### <a name="2246-tfhdbox"></a>2.2.4.6 TfhdBox 

>   **TfhdBox** と関連フィールドは、サンプル メタデータごとに既定値をフラグメントにカプセル化します。 **TfhdBox** フィールドの構文は、[[ISO/IEC-14496-12]](https://go.microsoft.com/fwlink/?LinkId=183695) セクション 8.8.7 で定義されている、Track Fragment Header Box の厳密なサブセットです。
> 
>   **BaseDataOffset (8 バイト):** **MdatBox** フィールドの先頭から **MdatBox** フィールド内のサンプル フィールドまでのオフセット (バイト)。 この制限を信号で知らせるには、default-base-is-moof フラグ (0x020000) を設定する必要があります。

#### <a name="2247-trunbox"></a>2.2.4.7 TrunBox 

>   **TrunBox** と関連フィールドは、要求されたフラグメントのサンプル メタデータごとにカプセル化します。 **TrunBox** の構文は、[[ISO/IEC-14496-](https://go.microsoft.com/fwlink/?LinkId=183695)*12]* セクション 8.8.8 で定義されている、Version 1 Track Fragment Run Box の厳密なサブセットです。
> 
>   **SampleCompositionTimeOffset (4 バイト):** フラグメントで最初に表示されたサンプルのプレゼンテーション時間が最初にデコードされたサンプルのデコード時間と同じになるように調整された、各サンプルの Sample Composition Time オフセット。 ビデオ サンプル コンポジションの負のオフセットは、
> 
>   [[ISO/IEC-14496-12]](https://go.microsoft.com/fwlink/?LinkId=183695) で規定されているとおりに使用するものとします。
> 
>   注:これにより、ビデオの音声遅延がデコード済み画像バッファの最大除去遅延と等しくなることで生じるビデオ同期エラーを回避し、異なる除去遅延を持つ可能性のある別のフラグメント間のプレゼンテーション タイミングを保持します。
> 
>   このセクション (ABNF [[RFC5234]](https://go.microsoft.com/fwlink/?LinkId=123096) で規定) で定義されているフィールドの構文は、以下を除いて変わりません。
> 
>   SampleCompositionTimeOffset = SIGNED_INT32

#### <a name="2248-mdatbox"></a>2.2.4.8 MdatBox 

#### <a name="2249-fragment-response-common-fields"></a>2.2.4.9 Fragment Response Common Fields (フラグメント応答の共通フィールド) 

### <a name="225-sparse-stream-pointer"></a>2.2.5 Sparse Stream Pointer (スパース ストリーム ポインター) 

### <a name="226-fragment-not-yet-available"></a>2.2.6 Fragment Not Yet Available (フラグメントはまだ使用できません) 

### <a name="227-live-ingest"></a>2.2.7 Live Ingest (ライブ取り込み) 

#### <a name="2271-filetype"></a>2.2.7.1 FileType 

>   **FileType (変数):** MPEG-4 ([[MPEG4-RA])](https://go.microsoft.com/fwlink/?LinkId=327787) ファイルと高度な属性のサブタイプと用途を指定します。
> 
>   **MajorBrand (変数):** メディア ファイルのメジャー ブランド。 "isml" に設定する必要があります。
> 
>   **MinorVersion (変数):** メディア ファイルのマイナー バージョン。 1 に設定する必要があります。
> 
>   **CompatibleBrands (変数):** MPEG-4 のサポートされているブランドを指定します。
>   "ccff" と "iso8" を含める必要があります。
> 
>   このセクションで定義されているフィールドの構文 (ABNF [[RFC5234]](https://go.microsoft.com/fwlink/?LinkId=123096) で規定) は、次のとおりです。

    FileType = MajorBrand MinorVersion CompatibleBrands
    MajorBrand = STRING_UINT32
    MinorVersion = STRING_UINT32
    CompatibleBrands = "ccff" "iso8" 0\*(STRING_UINT32)

**メモ**:互換性ブランド 'ccff' と 'iso8' は、フラグメントが “Common Container File Format”、Common Encryption [ISO/IEC 23001-7]、および ISO Base Media File Format Edition 4 [ISO/IEC 14496-12] に準拠していることを示します。

#### <a name="2272-streammanifestbox"></a>2.2.7.2 StreamManifestBox 

##### <a name="22721-streamsmil"></a>2.2.7.2.1 StreamSMIL 

#### <a name="2273-liveservermanifestbox"></a>2.2.7.3 LiveServerManifestBox 

##### <a name="22731-livesmil"></a>2.2.7.3.1 LiveSMIL 

#### <a name="2274-moovbox"></a>2.2.7.4 MoovBox 

#### <a name="2275-fragment"></a>2.2.7.5 Fragment (フラグメント) 

##### <a name="22751-track-fragment-extended-header"></a>2.2.7.5.1 Track Fragment Extended Header (トラック フラグメント拡張ヘッダー) 

### <a name="228-server-to-server-ingest"></a>2.2.8 Server-to-Server Ingest (サーバーからサーバーへの取り込み) 

## <a name="3-protocol-details"></a>3 Protocol Details (プロトコルの詳細) 


## <a name="31-client-details"></a>3.1 Client Details (クライアントの詳細) 

### <a name="311-abstract-data-model"></a>3.1.1 Abstract Data Model (抽象データ モデル) 

#### <a name="3111-presentation-description"></a>3.1.1.1 Presentation Description (プレゼンテーションの説明) 

>   Presentation Description データ要素は、プレゼンテーションのすべてのメタデータをカプセル化します。
> 
>   Presentation Metadata: プレゼンテーション内のすべてのストリームに共通するメタデータのセット。 Presentation Metadata は、セクション *2.2.2.1* で規定されている、次のフィールドで構成されます。
> 
> * **MajorVersion**
> * **MinorVersion**
> * **TimeScale**
> * **Duration**
> * **IsLive**
> * **LookaheadCount**
> * **DVRWindowLength**
> 
>   HEVC ストリームが含まれているプレゼンテーションは、以下を設定するものとします。

    MajorVersion = 2
    MinorVersion = 2

>   LookaheadCount = 0 (注: ボックスは非推奨)
> 
>   プレゼンテーションは以下も設定する必要があります。

    TimeScale = 90000

>   Stream Collection: セクション *3.1.1.1.2* で規定されている、Stream Description データ要素のコレクション。
> 
>   Protection Description: セクション *3.1.1.1.1* で規定されている、Protection System Metadata Description データ要素のコレクション。

##### <a name="31111-protection-system-metadata-description"></a>3.1.1.1.1 Protection System Metadata Description (保護システム メタデータの説明) 

>   Protection System Metadata Description データ要素は、1 つの Content Protection System に固有のメタデータをカプセル化します。 (変更なし)
> 
>   Protection Header Description: 1 つの Content Protection System に関連するコンテンツ保護のメタデータ。 Protection Header Description は、セクション *2.2.2.2* で規定されている次のフィールドで構成されます。
> 
>   * **SystemID**
>   * **ProtectionHeaderContent**

##### <a name="31112-stream-description"></a>3.1.1.1.2 Stream Description (ストリームの説明) 

###### <a name="311121-track-description"></a>3.1.1.1.2.1 Track Description (トラックの説明) 

###### <a name="3111211-custom-attribute-description"></a>3.1.1.1.2.1.1 Custom Attribute Description (カスタム属性の説明) 

##### <a name="3113-fragment-reference-description"></a>3.1.1.3 Fragment Reference Description (フラグメント参照の説明) 

###### <a name="31131-track-specific-fragment-reference-description"></a>3.1.1.3.1 Track-Specific Fragment Reference Description (トラック固有のフラグメント参照の説明) 

#### <a name="3112-fragment-description"></a>3.1.1.2 Fragment Description (フラグメントの説明) 

##### <a name="31121-sample-description"></a>3.1.1.2.1 Sample Description (サンプルの説明) 

### <a name="312-timers"></a>3.1.2 Timers (タイマー) 

### <a name="313-initialization"></a>3.1.3 Initialization (初期化) 

### <a name="314-higher-layer-triggered-events"></a>3.1.4 Higher-Layer Triggered Events (上位層でトリガーされたイベント) 

#### <a name="3141-open-presentation"></a>3.1.4.1 Open Presentation (プレゼンテーションを開く) 

#### <a name="3142-get-fragment"></a>3.1.4.2 Get Fragment (フラグメントを取得する) 

#### <a name="3143-close-presentation"></a>3.1.4.3 Close Presentation (プレゼンテーションを閉じる) 

### <a name="315-processing-events-and-sequencing-rules"></a>3.1.5 Processing Events and Sequencing Rules (イベント処理ルールとシーケンス処理ルール) 

#### <a name="3151-manifest-request-and-manifest-response"></a>3.1.5.1 Manifest Request and Manifest Response (マニフェスト要求とマニフェスト応答) 

#### <a name="3152-fragment-request-and-fragment-response"></a>3.1.5.2 Fragment Request and Fragment Response (フラグメント要求とフラグメント応答)

## <a name="32-server-details"></a>3.2 Server Details (サーバーの詳細)

## <a name="33-live-encoder-details"></a>3.3 Live Encoder Details (ライブ エンコーダーの詳細) 

## <a name="4-protocol-examples"></a>4 Protocol Examples (プロトコルの例) 

## <a name="5-security"></a>5 Security (セキュリティ) 

## <a name="51-security-considerations-for-implementers"></a>5.1 Security Considerations for Implementers (実装のセキュリティ考慮事項)

>   このプロトコルを使用して転送されるコンテンツに高い商用価値がある場合は、コンテンツの無断使用を防ぐため、Content Protection System を使用してください。 **ProtectionElement** は、Content Protection System の使用に関連するメタデータの処理に使用することができます。 保護されたオーディオおよびビデオ コンテンツは、MPEG Common Encryption Second Edition: 2015 [ISO/IEC 23001-7] で規定されているように、暗号化するものとします。
> 
>   **メモ**:HEVC ビデオの場合、VCL NAL でのスライス データのみが暗号化されます。 スライス ヘッダーとその他の NAL は、復号化する前にプレゼンテーション アプリケーションにアクセスできます。 セキュリティで保護されたビデオ パスでは、暗号化された情報はプレゼンテーション アプリケーションで使用できません。

## <a name="52-index-of-security-parameters"></a>5.2 Index of Security Parameters (セキュリティ パラメーターのインデックス) 


| **セキュリティ パラメーター**  | **セクション**         |
|-------------------------|---------------------|
| ProtectionElement       | *2.2.2.2*           |
| Common Encryption Boxes | *[ISO/IEC 23001-7]* |

## <a name="53-common-encryption-boxes"></a>5.3 Common Encryption Boxes (共通暗号化ボックス)

Common Encryption が適用されると、フラグメント応答に次のボックス ([ISO/IEC 23001-7] または [ISO/IEC 14496-12] で規定) が表示される場合があります。

1.  Protection System Specific Header Box (‘pssh’)

2.  Sample Encryption Box (‘senc’)

3.  Sample Auxiliary Information Offset Box (‘saio’)

4.  Sample Auxiliary Information Size Box (‘saiz’)

5.  Sample Group Description Box (‘sgpd’)

6.  Sample to Group Box (‘sbgp’)

---

## <a name="media-services-learning-paths"></a>Media Services のラーニング パス
[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>フィードバックの提供
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

[image1]: ./media/media-services-fmp4-live-ingest-overview/media-services-image1.png
[image2]: ./media/media-services-fmp4-live-ingest-overview/media-services-image2.png
[image3]: ./media/media-services-fmp4-live-ingest-overview/media-services-image3.png
[image4]: ./media/media-services-fmp4-live-ingest-overview/media-services-image4.png
[image5]: ./media/media-services-fmp4-live-ingest-overview/media-services-image5.png
[image6]: ./media/media-services-fmp4-live-ingest-overview/media-services-image6.png
[image7]: ./media/media-services-fmp4-live-ingest-overview/media-services-image7.png
