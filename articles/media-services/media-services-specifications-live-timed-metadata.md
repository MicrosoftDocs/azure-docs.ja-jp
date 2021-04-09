---
title: Azure Media Services - ライブ ストリーミングでの時間指定メタデータのシグナル通知
description: この仕様では、Azure Media Services の取り込み時とこのサービスへのストリーミング時に時間指定メタデータをシグナル通知する方法を概説します。 これには、汎用時間指定メタデータ シグナル (ID3) のサポートと、広告挿入およびスプライス条件シグナル通知のための SCTE-35 シグナル通知が含まれます。
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
ms.date: 08/22/2019
ms.author: johndeu
ms.openlocfilehash: f826ee9ef3c9fff0b721a9c79d3c12e0adbd5f7f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "91336396"
---
# <a name="signaling-timed-metadata-in-live-streaming"></a>ライブ ストリーミングでの時間指定メタデータのシグナル通知 

最終更新:2019-08-22

### <a name="conformance-notation"></a>適合性の表記

このドキュメントで使用するキーワード "MUST (しなければなりません)"、"MUST NOT (してはなりません)"、"REQUIRED (必要です)"、"SHALL (することになります)"、"SHALL NOT (することはありません)"、"SHOULD (する必要があります)"、"SHOULD NOT (すべきではありません)"、"RECOMMENDED (お勧めします)"、"MAY (する場合があります)"、"OPTIONAL (オプション)" は、RFC 2119 で説明するとおりに解釈されます

## <a name="1-introduction"></a>1.はじめに 

広告の挿入またはクライアント プレーヤーでのカスタム メタデータ イベントの挿入をシグナル通知するために、ブロードキャスタはビデオ内に埋め込まれた時間指定メタデータを利用することがよくあります。 これらのシナリオを可能にするため、Media Services では、ライブ ストリーミング チャネルの取り込みポイントからクライアント アプリケーションまで、時間指定メタデータをトランスポートできます。
この仕様では、ライブ ストリーミング シグナル内の時間指定メタデータに対して Media Services がサポートするいくつかのモードについて説明します。

1. [SCTE-35]、[SCTE-214-1]、[SCTE-214-3] および [RFC8216] で概説されている標準に準拠した [SCTE-35] シグナル通知

2. RTMP 広告シグナル通知のためのレガシ [Adobe-Primetime] 仕様に準拠した [SCTE-35] シグナル通知。
   
3. [ID3v2] またはアプリケーション開発者によって定義されたその他のカスタム スキーマを送信することがある、[SCTE-35] **以外の** メッセージのための汎用時間指定メタデータ シグナル通知モード。

## <a name="11-terms-used"></a>1.1 用語

| 期間                | 定義                                                                                                                                                                                                                                    |
| ------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| 広告ブレーク            | 1 つ以上の広告の配信をスケジュールできる位置または時点。可用性および配置機会と同じ。                                                                                                                     |
| 広告決定サービス | ユーザーに表示される広告と期間を決定する外部サービス。 サービスは通常、パートナーによって提供されるものであり、このドキュメントでは説明しません。                                                                    |
| キュー                 | まもなく発生する広告ブレークの時間とパラメーターの指示。 キューは、広告ブレークへの保留中の切り替え、広告ブレーク内の次の広告への保留中の切り替え、および、広告ブレークからメイン コンテンツへの保留中の切り替えを示すことがある点に注意してください。           |
| パッケージャー            | Azure Media Services の "ストリーミング エンドポイント" は、DASH および HLS 向けのダイナミック パッケージ機能を提供し、メディア業界では "パッケージャー" と呼ばれます。                                                                              |
| プレゼンテーション時間   | イベントが閲覧者に提示される時間。 時間は、閲覧者がイベントを見るメディア タイムライン上の時点を表します。 たとえば、SCTE-35 splice_info() コマンド メッセージのプレゼンテーション時間は splice_time() です。 |
| 到着時間        | イベント メッセージが到着する時間。 イベント メッセージはイベントのプレゼンテーション時間より前に送信されるため、通常、この時間はイベントのプレゼンテーション時間とは異なります。                                                    |
| スパース トラック        | 連続的ではなく、親トラックまたは制御トラックと時間が同期されるメディア トラック。                                                                                                                                                  |
| 出発地              | Azure メディア ストリーミング サービス                                                                                                                                                                                                             |
| チャンネル シンク        | Azure メディア ライブ ストリーミング サービス                                                                                                                                                                                                        |
| HLS                 | Apple HTTP ライブ ストリーミング プロトコル                                                                                                                                                                                                            |
| DASH                | Dynamic Adaptive Streaming Over HTTP (HTTP 経由のダイナミック アダプティブ ストリーミング)                                                                                                                                                                                                          |
| Smooth              | スムーズ ストリーミング プロトコル                                                                                                                                                                                                                     |
| MPEG2-TS            | MPEG-2 トランスポート ストリーム                                                                                                                                                                                                                      |
| RTMP                | リアルタイム マルチメディア プロトコル                                                                                                                                                                                                                 |
| uimsbf              | 符号なし整数、最上位ビットが先頭。                                                                                                                                                                                                 |

---

## <a name="12-normative-references"></a>1.2.1 標準リファレンス

以下の文書には、このテキストでの参照を通じて本書の規定を構成する規定が含まれています。 すべての文書は標準化団体によって改訂される可能性があります。読者の皆様には、以下に掲げる文書の最新版が適用される可能性について調査することをお勧めします。 また、参照されている文書の新しい版は、Azure Media Services の時間指定メタデータ仕様の現行バージョンとは互換性がない可能性があることも念頭に置いてください。


| Standard          | 定義                                                                                                                                                                                                     |
| ----------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| [Adobe-Primetime] | [Primetime Digital Program Insertion Signaling Specification (デジタル プログラム挿入シグナル通知仕様) 1.2](https://www.adobe.com/content/dam/acom/en/devnet/primetime/PrimetimeDigitalProgramInsertionSignalingSpecification.pdf)                       |
| [Adobe-Flash-AS]  | [FLASH ActionScript 言語リファレンス](https://help.adobe.com/archive/en_US/as2/flashlite_2.x_3.x_aslr.pdf)                                                                                                   |
| [AMF0]            | ["アクション メッセージ形式 AMF0"](https://download.macromedia.com/pub/labs/amf/amf0_spec_121207.pdf)                                                                                                              |
| [DASH-IF-IOP]     | DASH Industry Forum Interop Guidance (DASH 業界フォーラム相互運用性ガイダンス) v 4.2 [https://dashif-documents.azurewebsites.net/DASH-IF-IOP/master/DASH-IF-IOP.html](https://dashif-documents.azurewebsites.net/DASH-IF-IOP/master/DASH-IF-IOP.html)    |
| [HLS-TMD]         | HTTP ライブ ストリーミング用の時間指定メタデータ - [https://developer.apple.com/streaming](https://developer.apple.com/streaming)                                                                                        |
| [CMAF-ID3]        | [Common Media Application Format (CMAF) の時間指定メタデータ](https://github.com/AOMediaCodec/id3-emsg)                                                                                                        |
| [ID3v2]           | ID3 タグ バージョン 2.4.0  [http://id3.org/id3v2.4.0-structure](http://id3.org/id3v2.4.0-structure)                                                                                                                |
| [ISO-14496-12]    | ISO/IEC 14496-12:パート 12 ISO ベースのメディア ファイル形式、第 4 版、2012 年 7 月 15 日                                                                                                                                 |
| [MPEGDASH]        | 情報技術 -- HTTP 経由のダイナミック アダプティブ ストリーミング (DASH) -- パート 1:メディア プレゼンテーションの説明とセグメント形式。 2014 年 5 月。 公開済み。 URL: https://www.iso.org/standard/65274.html         |
| [MPEGCMAF]        | 情報技術 -- マルチメディア アプリケーション形式 (MPEG-A) -- パート 19:セグメント化メディア用の共通メディア アプリケーション形式 (CMAF)。 2018 年 1 月。 公開済み。 URL: https://www.iso.org/standard/71975.html |
| [MPEGCENC]        | 情報技術 -- MPEG システム技術 -- パート 7:ISO ベース メディア ファイル形式ファイルでの一般的な暗号化。 2016 年 2 月。 公開済み。 URL: https://www.iso.org/standard/68042.html                   |
| [MS-SSTR]         | [「Microsoft Smooth Streaming プロトコル」、2014 年 5 月 15 日](/openspecs/windows_protocols/ms-sstr/8383f27f-7efe-4c60-832a-387274457251)                                                     |
| [MS-SSTR-Ingest]  | [Azure Media Services の Fragmented MP4 ライブ インジェスト仕様](./media-services-fmp4-live-ingest-overview.md)                                                      |
| [RFC8216]         | R. Pantos, Ed.; W. May. HTTP ライブ ストリーミング。 2017 年 8 月。 情報提供。 [https://tools.ietf.org/html/rfc8216](https://tools.ietf.org/html/rfc8216)                                                            |
| [RFC4648]         | Base16、Base32、および Base64 データ エンコード - [https://tools.ietf.org/html/rfc4648](https://tools.ietf.org/html/rfc4648)                                                                                     |
| [RTMP]            | [「Adobe のリアルタイム メッセージング プロトコル」、2012 年 12 月 21 日](https://www.adobe.com/devnet/rtmp.html)                                                                                                            |
| [SCTE-35-2019]    | SCTE 35:2019 - ケーブル接続のデジタル プログラム挿入キューイング メッセージ - https://www.scte.org/SCTEDocs/Standards/ANSI_SCTE%2035%202019r1.pdf                                                                       |
| [SCTE-214-1]      | SCTE 214-1 2016 – IP ベースのケーブル サービス向け MPEG DASH パート 1:MPD の制約と拡張                                                                                                                 |
| [SCTE-214-3]      | SCTE 214-3 2015 IP ベースのケーブル サービス向け MPEG DASH パート 3:DASH/FF プロファイル                                                                                                                                  |
| [SCTE-224]        | SCTE 224 2018r1 – イベントのスケジューリングと通知のインターフェイス                                                                                                                                                  |
| [SCTE-250]        | イベントおよびシグナル通知管理 API (ESAM)                                                                                                                                                                      |

---


## <a name="2-timed-metadata-ingest"></a>2.時間指定メタデータの取り込み

Azure Media Services は、[RTMP] および Smooth Streaming [MS-SSTR-Ingest] プロトコルの両方について、リアルタイムのインバンド メタデータをサポートします。 リアルタイムのメタデータを使用すると、独自の一意のカスタム スキーマ (JSON、バイナリ、XML) と、ブロードキャスト ストリームでの広告シグナリングのための ID3、SCTE-35 などの業界定義形式を使用して、カスタム イベントを定義できます。 

この記事では、Azure Media Services のサポートされている取り込みプロトコルを使用して、カスタムの時間指定メタデータ シグナルを送信する方法の詳細について説明します。 この記事では、HLS、DASH、および Smooth Streaming 用のマニフェストを時間指定メタデータ シグナルで修飾する方法と、HLS の CMAF (MP4 フラグメント) またはトランスポート ストリーム (TS) セグメントを使用してコンテンツが配信されるときに、そのマニフェストをインバンドで伝達する方法について説明します。 

時間指定メタデータの一般的なユース ケース シナリオは次のとおりです。

 - ライブ イベントまたは線形ブロードキャストで広告の中断をトリガーする SCTE-35 広告シグナル
 - クライアント アプリケーション (ブラウザー、iOS、または Android) でイベントをトリガーできるカスタム ID3 メタデータ
 - クライアント アプリケーションでイベントをトリガーするカスタム定義の JSON、バイナリ、または XML メタデータ
 - ライブ エンコーダー、IP カメラ、またはドローンからのテレメトリ
 - モーション、顔検出など、IP カメラからのイベント
 - アクション カメラ、ドローン、または移動型デバイスからの地理的位置情報
 - 曲の歌詞
 - 線形ライブ フィード上のプログラムの境界
 - ライブ フィードに表示される画像または拡張メタデータ
 - スポーツのスコアまたはゲームクロックの情報
 - ブラウザーで動画と共に表示される対話型広告パッケージ
 - クイズまたはアンケート
  
Azure Media Services Live Events および Packager では、これらの時間指定メタデータ シグナルを受信し、HLS や DASH などの標準ベースのプロトコルを使用してクライアント アプリケーションに到達できるメタデータのストリームに変換できます。


## <a name="21-rtmp-timed-metadata"></a>2.1 RTMP 時間指定メタデータ

[RTMP] プロトコルを使用すると、カスタム メタデータや SCTE-35 広告シグナルなどのさまざまなシナリオに対して、時間指定メタデータ シグナルを送信することができます。 

広告シグナル (キュー メッセージ) は、[RTMP] ストリーム内に埋め込まれた [AMF0] キュー メッセージとして送信されます。 キュー メッセージは、実際のイベントまたは [SCTE-35] 広告スプライス シグナルが発生する必要がある時点よりも前に送信できます。 このシナリオをサポートするために、イベントの実際のプレゼンテーションのタイムスタンプはキュー メッセージ内で送信されます。 詳しくは、[AMF0] をご覧ください。

次の [AMF0] コマンドは、RTMP 取り込みの Azure Media Services でサポートされます。

- **onUserDataEvent** - カスタムのメタデータまたは [ID3v2] 時間指定メタデータに使用されます
- **onAdCue** - 主にライブ ストリームの広告配置機会をシグナリングするために使用されます。 シンプル モードと "SCTE-35" モードという 2 つの形式のキューがサポートされています。 
- **onCuePoint** - [SCTE35] メッセージをシグナリングするために、特定のオンプレミスのハードウェア エンコーダー (Elemental Live エンコーダーなど) によってサポートされています。 
  

次の表では、"シンプル" および [SCTE-35] メッセージ モードの両方で Media Services が取り込む AMF メッセージのペイロードの形式について説明します。

[AMF0] メッセージの名前を使って、同じ種類の複数のイベント ストリームを区別できます。  [SCTE-35] メッセージと "シンプル" モードの両方で、AMF メッセージの名前は [Adobe-Primetime] 仕様の要求どおり "onAdCue" でなければなりません。  下の一覧にないフィールドは、取り込み時に Azure Media Services によって無視されることになります。

## <a name="211-rtmp-with-custom-metadata-using-onuserdataevent"></a>2.1.1 "onUserDataEvent" を使用するカスタム メタデータによる RTMP

RTMP プロトコルを使用するアップストリーム エンコーダー、IP カメラ、ドローン、またはデバイスからカスタムのメタデータ フィードを提供する場合は、"onUserDataEvent" [AMF0] データ メッセージ コマンドの種類を使用します。

**"onUserDataEvent"** データ メッセージ コマンドは、Media Services によってキャプチャされ、インバンド ファイル形式と HLS、DASH、および Smooth Streaming のマニフェストにパッケージ化される次の定義を持つメッセージ ペイロードを伝達する必要があります。
時間指定メタデータ メッセージは、0.5 秒 (500 ミリ秒) ごとに 1 回以下の頻度で、またはライブ ストリームに関する安定性の問題が発生している可能性がある場合に送信することをお勧めします。 フレームレベルのメタデータを提供する必要がある場合、各メッセージは複数のフレームからメタデータを集計できます。 マルチビットレート ストリームを送信する場合は、帯域幅を削減し、動画やオーディオの処理による干渉を避けるために、単一ビットレートでのみメタデータを提供することもお勧めします。 

**"onUserDataEvent"** のペイロードは、[MPEGDASH] EventStream XML 形式のメッセージである必要があります。 これにより、HLS または DASH プロトコル経由で配信される CMAF [MPEGCMAF] コンテンツに対して、インバンドの 'emsg' ペイロードで伝達できるカスタムの定義スキーマを簡単に渡すことができます。 各 DASH Event Stream メッセージには、URN メッセージスキーム識別子として機能し、メッセージのペイロードを定義する schemeIdUri が含まれています。 [ID3v2] 用の "https://aomedia.org/emsg/ID3"、[SCTE-35] 用の **urn:scte:scte35:2013:bin** など、一部のスキームは、相互運用性を確保するために業界のコンソーシアムによって標準化されています。 すべてのアプリケーション プロバイダーは、制御する URL (所有ドメイン) を使用して独自のカスタム スキームを定義できます。また、必要に応じてその URL で仕様を提供できます。 プレーヤーが定義済みスキームのハンドラーを持っている場合は、それがペイロードとプロトコルを理解する必要がある唯一のコンポーネントです。

[MPEG-DASH] EventStream XML ペイロードのスキーマが定義されます (DASH ISO-IEC-23009--1-3rd Edition からの抜粋)。 現時点では、"EventStream" ごとに 1 つの "EventType" のみがサポートされていることに注意してください。 **EventStream** に複数のイベントが指定されている場合、最初の **Event** 要素のみが処理されます。

```xml
  <!-- Event Stream -->
  <xs:complexType name="EventStreamType">
    <xs:sequence>
      <xs:element name="Event" type="EventType" minOccurs="0" maxOccurs="unbounded"/>
      <xs:any namespace="##other" processContents="lax" minOccurs="0" maxOccurs="unbounded"/>
    </xs:sequence>
    <xs:attribute ref="xlink:href"/>
    <xs:attribute ref="xlink:actuate" default="onRequest"/>
    <xs:attribute name="schemeIdUri" type="xs:anyURI" use="required"/>
    <xs:attribute name="value" type="xs:string"/>
    <xs:attribute name="timescale" type="xs:unsignedInt"/>
  </xs:complexType>
  <!-- Event  -->
  <xs:complexType name="EventType">
    <xs:sequence>
      <xs:any namespace="##other" processContents="lax" minOccurs="0" maxOccurs="unbounded"/>
    </xs:sequence>
    <xs:attribute name="presentationTime" type="xs:unsignedLong" default="0"/>
    <xs:attribute name="duration" type="xs:unsignedLong"/>
    <xs:attribute name="id" type="xs:unsignedInt"/>
    <xs:attribute name="contentEncoding" type="ContentEncodingType"/>
    <xs:attribute name="messageData" type="xs:string"/>
    <xs:anyAttribute namespace="##other" processContents="lax"/>
  </xs:complexType>
```


### <a name="example-xml-event-stream-with-id3-schema-id-and-base64-encoded-data-payload"></a>ID3 スキーマ ID と base64 でエンコードされたデータ ペイロードを使用した XML イベント ストリームの例。  
```xml
   <?xml version="1.0" encoding="UTF-8"?>
   <EventStream schemeIdUri="https://aomedia.org/emsg/ID3">
         <Event contentEncoding="Base64">
          -- base64 encoded ID3v2 full payload here per [CMAF-TMD] --
         </Event>
   <EventStream>
```

### <a name="example-event-stream-with-custom-schema-id-and-base64-encoded-binary-data"></a>カスタム スキーマ ID と base64 でエンコードされたバイナリ データを使用したイベント ストリームの例  
```xml
   <?xml version="1.0" encoding="UTF-8"?>
   <EventStream schemeIdUri="urn:example.org:custom:binary">
         <Event contentEncoding="Base64">
          -- base64 encoded custom binary data message --
         </Event>
   <EventStream>
```

### <a name="example-event-stream-with-custom-schema-id-and-custom-json"></a>カスタム スキーマ ID とカスタム JSON を使用したイベント ストリームの例  
```xml
   <?xml version="1.0" encoding="UTF-8"?>
   <EventStream schemeIdUri="urn:example.org:custom:JSON">
         <Event>
          [
            {"key1" : "value1"},
            {"key2" : "value2"}
          ]
         </Event>
   <EventStream>
```

### <a name="built-in-supported-scheme-id-uris"></a>組み込みのサポートされているスキーム ID の URI
| スキーム ID URI                 | 説明                                                                                                                                                                                                                                          |
| ----------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| https:\//aomedia.org/emsg/ID3 | [ID3v2] メタデータを CMAF 互換の [MPEGCMAF] フラグメント化 MP4 内の時間指定メタデータとして伝達する方法について説明します。 詳細については、「[Timed Metadata in the Common Media Application Format (CMAF) (Common Media Application Format (CMAF) の時間指定メタデータ)](https://github.com/AOMediaCodec/id3-emsg)」を参照してください。 |

### <a name="event-processing-and-manifest-signaling"></a>イベント処理とマニフェストのシグナリング

有効な **"onUserDataEvent"** イベントを受信すると、Azure Media Services では EventStreamType ([MPEGDASH] で定義) に一致する有効な XML ペイロードが検索され、XML ペイロードが解析され、それがライブ アーカイブへの保存と Media Services Packager への送信のために [MPEGCMAF] MP4 フラグメント ’emsg’ バージョン 1 ボックスに変換されます。   次のように、Packager ではライブ ストリームの 'emsg' ボックスが検出されます。

- (a) HLS 時間指定メタデータ仕様 [HLS-TMD] に準拠して HLS クライアントに配信するために、TS セグメントに "動的にパッケージ化します"、または
- (b) HLS または DASH 経由の CMAF フラグメントでの配信のために、それをパススルーします、または 
- (c) Smooth Streaming [MS-SSTR] 経由で配信するためにスパース トラック シグナルに変換します。

HLS 用のインバンドの 'emsg' 形式の CMAF または TS PES パケットに加え、DASH (MPD) のマニフェストと Smooth Streaming には、インバンド イベント ストリームへの参照が含まれます (Smooth Streaming では、スパース ストリーム トラックとも呼ばれます)。

個々のイベントまたはそのデータのペイロードは、HLS、DASH、または Smooth マニフェストに直接出力されません。 

### <a name="additional-informational-constraints-and-defaults-for-onuserdataevent-events"></a>onUserDataEvent イベントの追加情報の制約と既定値

- EventStream 要素にタイムスケールが設定されていない場合、既定では RTMP 1 kHz のタイムスケールが使用されます。
- onUserDataEvent メッセージの配信は、最大で 500 ミリ秒ごとに 1 回に制限されています。イベントをより頻繁に送信すると、ライブ フィードの帯域幅と安定性に影響する可能性があります。

## <a name="212-rtmp-ad-cue-signaling-with-onadcue"></a>2.1.2 "onAdCue" を使用した RTMP 広告キュー シグナル通知

Azure Media Services は、いくつかの [AMF0] メッセージ型をリッスンし、それらに応答することができます。これらのメッセージ型は、リアルタイムで同期される各種メタデータをライブ ストリーム内でシグナル通知するために使用できます。  [Adobe-Primetime] 仕様では、"シンプル" および "SCTE-35" モードと呼ばれる 2 つのキュー タイプを定義しています。 "シンプル" モードの場合、Media Services は、"Simple Mode" シグナル用に定義された下記の表に一致するペイロードを使用して、"onAdCue" という単一の AMF キュー メッセージをサポートします。  

次のセクションでは、RTMP "シンプル" モード ペイロードを示します。これは、HLS、DASH、Microsoft Smooth Streaming のクライアント マニフェストに送られる基本的な "spliceOut" 広告シグナルを通知するために使用できます。 これは、複雑な SCTE-35 ベースの広告シグナル通知のデプロイまたは挿入システムを顧客が有しておらず、基本的なオンプレミス エンコーダーを使用して API 経由でキュー メッセージを送信しているシナリオで非常に役立ちます。 通常、オンプレミス エンコーダーは、このシグナルをトリガーするために REST ベースの API をサポートします。このトリガーには、ビデオに IDR フレームを挿入して新しい GOP を開始することによって、ビデオ ストリームを "スプライス条件" 化する作用もあります。

## <a name="213--rtmp-ad-cue-signaling-with-onadcue---simple-mode"></a>2.1.3 "onAdCue" を使用した RTMP 広告キュー シグナル通知 - シンプル モード

| フィールド名 | フィールドの型 | 必須 | 説明                                                                                                                                                                                                                                                                        |
| ---------- | ---------- | --------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| type       | String     | 必須  | イベント メッセージ。  シンプル モードのスプライスを指定する "SpliceOut" である必要があります。                                                                                                                                                                                                         |
| id         | String     | 必須  | スプライスまたはセグメントを記述する一意識別子。 メッセージのこのインスタンスを示します。                                                                                                                                                                                       |
| duration   | Number     | 必須  | スプライスの継続時間。 単位は秒の小数部です。                                                                                                                                                                                                                           |
| elapsed    | Number     | 省略可能  | チューニングをサポートするためにシグナルを繰り返す場合は、スプライスが開始してから経過したプレゼンテーション時間の長さをこのフィールドに設定する必要があります。 単位は秒の小数部です。 シンプル モードを使う場合、この値はスプライスの元の継続時間を超えてはなりません。 |
| time       | Number     | 必須  | プレゼンテーション時間で表されたスプライスの時間。 単位は秒の小数部です。                                                                                                                                                                                                |

---
 
#### <a name="example-mpeg-dash-manifest-output-when-using-adobe-rtmp-simple-mode"></a>Adobe RTMP シンプル モードを使用した場合の MPEG DASH マニフェストの出力例

[3.3.2.1 の Adobe シンプル モードを使用した MPEG DASH .mpd EventStream](#3321-example-mpeg-dash-mpd-manifest-signaling-of-rtmp-streaming-using-adobe-simple-mode)の例を参照してください

[3.3.3.1 の 単一期間と Adobe シンプル モードを使用した DASH マニフェスト](#3331-example-mpeg-dash-manifest-mpd-with-single-period-eventstream-using-adobe-simple-mode-signals)の例を参照してください

#### <a name="example-hls-manifest-output-when-using-adobe-rtmp-simple-mode"></a>Adobe RTMP シンプル モードを使用した場合の HLS マニフェストの出力例

[3.2.2 の Adobe シンプル モードと EXT-X-CUE タグを使用した HLS マニフェスト](#322-apple-hls-with-adobe-primetime-ext-x-cue)の例を参照してください

## <a name="214-rtmp-ad-cue-signaling-with-onadcue---scte-35-mode"></a>2.1.4 "onAdCue" を使用した RTMP 広告キュー シグナル通知 - SCTE-35 モード

完全な SCTE-35 ペイロード メッセージを HLS または DASH マニフェストに送信する必要がある、より高度な放送制作ワークフローで作業している場合、[Adobe-Primetime] 仕様の "SCTE-35 モード" を使用するのが最適です。  このモードは、オンプレミス ライブ エンコーダーに直接送信されるインバンド SCTE-35 シグナルをサポートします。その後、このエンコーダーが、[Adobe-Primetime] 仕様で指定された "SCTE-35 モード" を使用してシグナルを RTMP ストリームにエンコードします。 

通常、SCTE-35 メッセージが現れる可能性があるのは、オンプレミス エンコーダー上の MPEG-2 トランスポート ストリーム (TS) 入力だけです。 SCTE-35 を含むトランスポート ストリーム取り込みを構成し、Adobe SCTE-35 モードでの RTMP へのパススルーのためにそれを有効化する方法の詳細については、エンコーダーの製造元にお問い合わせください。

このシナリオでは、 **"onAdCue"** [AMF0] メッセージ型を使用してオンプレミス エンコーダーから次のペイロードを送信しなければなりません。

| フィールド名 | フィールドの型 | 必須 | 説明                                                                                                                                                                                                                                                                                                                                                                                                        |
| ---------- | ---------- | --------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| cue        | String     | 必須  | イベント メッセージ。  [SCTE-35] メッセージの場合、HLS、Smooth、Dash クライアントにメッセージが送信されるためには、このフィールドは base64 でエンコードされた [RFC4648] バイナリの splice_info_section() でなければなりません。                                                                                                                                                                                                                               |
| type       | String     | 必須  | メッセージのスキームを示す URN または URL。 [SCTE-35] メッセージの場合、[Adobe-Primetime] に準拠する HLS、Smooth、Dash クライアントにメッセージが送信されるためには、これが **"scte35"** である **必要があります**。 オプションで、[SCTE-35] メッセージをシグナル通知するために URN "urn:scte:scte35:2013:bin" を使用することもできます。                                                                                                        |
| id         | String     | 必須  | スプライスまたはセグメントを記述する一意識別子。 メッセージのこのインスタンスを示します。  同じセマンティクスを持つメッセージには、同じ値が設定されている必要があります。                                                                                                                                                                                                                                                       |
| duration   | Number     | 必須  | イベントまたは広告スプライス セグメントの継続時間 (わかっている場合)。 わからない場合は、0 にする **必要があります**。                                                                                                                                                                                                                                                                                                                    |
| elapsed    | Number     | 省略可能  | チューニングのために [SCTE-35] 広告シグナルを繰り返す場合は、スプライスが開始してから経過したプレゼンテーション時間の長さをこのフィールドに設定する必要があります。 単位は秒の小数部です。 [SCTE-35] モードでは、この値はスプライスまたはセグメントで指定されている元の継続時間を超えてもかまいません。                                                                                                                   |
| time       | Number     | 必須  | イベントまたは広告スプライスのプレゼンテーション時間。  プレゼンテーション時間と継続時間は、[ISO-14496-12] Annex I で定義されているように、タイプ 1 または 2 のストリーム アクセス ポイント (SAP) と一致している **必要があります**。HLS エグレスの場合は、プレゼンテーション時間と継続時間はセグメントの境界と一致する **必要があります**。 同じイベント ストリームに含まれる異なるイベント メッセージのプレゼンテーション時間と継続時間は、オーバーラップしてはなりません。 単位は秒の小数部です。 |

---

<!---
#### Example MPEG DASH .mpd manifest with SCTE-35 mode
See [Section 3.3.3.2 example DASH manifest with SCTE-35](#3332-example-mpeg-dash-manifest-mpd-with-multi-period-eventstream-using-adobe-scte35-mode-signaling)
--->

#### <a name="example-hls-manifest-m3u8-with-scte-35-mode-signal"></a>SCTE-35 モードのシグナルを使用した HLS マニフェスト .m3u8 の例
[セクション 3.2.1.1 の SCTE-35 を使用した HLS マニフェストの例](#3211-example-hls-manifest-m3u8-showing-ext-x-cue-signaling-of-scte-35)を参照してください



## <a name="215-rtmp-ad-signaling-with-oncuepoint-for-elemental-live"></a>2.1.5 Elemental Live 用の "onCuePoint" を使用した RTMP 広告シグナル通知

Elemental Live オンプレミス エンコーダーは、RTMP シグナル内の広告マーカーをサポートします。 Azure Media Services は現在、RTMP に対して "onCuePoint" 広告マーカー タイプのみをサポートしています。  これは、Elemental Media Live エンコーダー設定または API の Adobe RTMP グループ設定で "**ad_markers**" を "onCuePoint" に設定することによって有効化できます。  詳細については、Elemental Live のドキュメントをご覧ください。 RTMP グループでこの機能を有効化すると、SCTE-35 シグナルが Adobe RTMP 出力に渡され、Azure Media Services によって処理されます。

"onCuePoint" メッセージ型は [Adobe-Flash-AS] で定義され、Elemental Live RTMP 出力からの送信時は次のペイロード構造を持ちます。


| プロパティ   | 説明                                                                                                                                                                                                                     |
| ---------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| name       | 名前は Elemental Live による "**scte35**" である必要があります。                                                                                                                                                                              |
| time       | タイムライン中にビデオ ファイルでキュー ポイントが発生した時間 (秒)                                                                                                                                           |
| type       | キュー ポイントの種類は "**event**" に設定する必要があります。                                                                                                                                                                             |
| parameters | SCTE-35 メッセージからの (ID や期間などの) 情報を含む名前/値ペア文字列の連想配列。 これらの値は Azure Media Services によって解析され、マニフェスト装飾タグに含められます。 |


広告マーカーのこのモードが使用されるとき、HLS マニフェストの出力は Adobe "Simple" モードに似ています。


#### <a name="example-mpeg-dash-mpd-single-period-adobe-simple-mode-signals"></a>MPEG DASH MPD、単一期間、Adobe Simple モードのシグナルの例

```xml
<?xml version="1.0" encoding="utf-8"?>
<MPD xmlns="urn:mpeg:dash:schema:mpd:2011" 
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" profiles="urn:mpeg:dash:profile:isoff-live:2011" type="dynamic" publishTime="2020-01-07T18:58:03Z" minimumUpdatePeriod="PT0S" timeShiftBufferDepth="PT58M56S" availabilityStartTime="2020-01-07T17:44:47Z" minBufferTime="PT7S">
    <Period start="PT0S">
        <EventStream schemeIdUri="urn:com:adobe:dpi:simple:2015" value="scte35" timescale="10000000">
            <Event presentationTime="1583497601000000" duration="300000000" id="1085900"/>
            <Event presentationTime="1583500901666666" duration="300000000" id="1415966"/>
            <Event presentationTime="1583504202333333" duration="300000000" id="1746033"/>
            <Event presentationTime="1583507502666666" duration="300000000" id="2076066"/>
            <Event presentationTime="1583510803333333" duration="300000000" id="2406133"/>
            <Event presentationTime="1583514104000000" duration="300000000" id="2736200"/>
            <Event presentationTime="1583517404666666" duration="300000000" id="3066266"/>
            <Event presentationTime="1583520705333333" duration="300000000" id="3396333"/>
            <Event presentationTime="1583524006000000" duration="300000000" id="3726400"/>
            <Event presentationTime="1583527306666666" duration="300000000" id="4056466"/>
            <Event presentationTime="1583530607333333" duration="300000000" id="4386533"/>
        </EventStream>
        <AdaptationSet id="1" group="1" profiles="ccff" bitstreamSwitching="false" segmentAlignment="true" contentType="video" mimeType="video/mp4" codecs="avc1.4D400C" maxWidth="256" maxHeight="144" startWithSAP="1">
            <InbandEventStream schemeIdUri="urn:mpeg:dash:event:2012" value="1"/>
            <InbandEventStream schemeIdUri="urn:com:adobe:dpi:simple:2015" value="scte35"/>
            <SegmentTemplate timescale="10000000" presentationTimeOffset="1583486678426666" media="QualityLevels($Bandwidth$)/Fragments(video=$Time$,format=mpd-time-csf)" initialization="QualityLevels($Bandwidth$)/Fragments(video=i,format=mpd-time-csf)">
                <SegmentTimeline>
                    <S t="1583495318000000" d="64000000" r="34"/>
                    <S d="43000000"/>
                    <S d="21000000"/>
                    <!-- ... Truncated for brevity of sample-->

                </SegmentTimeline>
            </SegmentTemplate>
            <ProducerReferenceTime id="1583495318000000" type="0" wallClockTime="2020-01-07T17:59:10.957Z" presentationTime="1583495318000000"/>
            <Representation id="1_V_video_3750956353252827751" bandwidth="149952" width="256" height="144"/>
        </AdaptationSet>
        <AdaptationSet id="2" group="5" profiles="ccff" bitstreamSwitching="false" segmentAlignment="true" contentType="audio" mimeType="audio/mp4" codecs="mp4a.40.2" lang="en">
            <InbandEventStream schemeIdUri="urn:mpeg:dash:event:2012" value="1"/>
            <InbandEventStream schemeIdUri="urn:com:adobe:dpi:simple:2015" value="scte35"/>
            <Label>ambient</Label>
            <SegmentTemplate timescale="10000000" presentationTimeOffset="1583486678426666" media="QualityLevels($Bandwidth$)/Fragments(ambient=$Time$,format=mpd-time-csf)" initialization="QualityLevels($Bandwidth$)/Fragments(ambient=i,format=mpd-time-csf)">
                <SegmentTimeline>
                    <S t="1583495254426666" d="64000000" r="35"/>
                    <S d="43093334"/>
                    <S d="20906666"/>
                    <!-- ... Truncated for brevity of sample-->

                </SegmentTimeline>
            </SegmentTemplate>
            <ProducerReferenceTime id="1583495254426666" type="0" wallClockTime="2020-01-07T17:59:04.600Z" presentationTime="1583495254426666"/>
            <Representation id="5_A_ambient_9125670592623055209" bandwidth="96000" audioSamplingRate="48000"/>
        </AdaptationSet>
    </Period>
</MPD>
```

#### <a name="example-hls-playlist-adobe-simple-mode-signals-using-ext-x-cue-tag-truncated--for-brevity"></a>EXT-X-CUE タグを使用した HLS プレイリスト、Adobe Simple モードのシグナルの例 (簡潔さのために"..." で切り捨てられています)

次の例は、Adobe "シンプル" モードのシグナルとレガシ [Adobe-Primetime] EXT-X-CUE タグを使用した RTMP 取り込みストリームの、Media Services ダイナミック パッケージャーからの出力を示しています。  

```
#EXTM3U
#EXT-X-VERSION:8
#EXT-X-MEDIA-SEQUENCE:0
#EXT-X-TARGETDURATION:7
#EXT-X-INDEPENDENT-SEGMENTS
#EXT-X-PROGRAM-DATE-TIME:2020-01-07T17:44:47Z
#EXTINF:6.400000,no-desc
Fragments(video=1583486742000000,format=m3u8-aapl-v8)
#EXTINF:6.400000,no-desc
Fragments(video=1583486806000000,format=m3u8-aapl-v8)
...
#EXTINF:6.166667,no-desc
Fragments(video=1583487638000000,format=m3u8-aapl-v8)
#EXT-X-CUE:ID=95766,TYPE="SpliceOut",DURATION=30.000000,TIME=158348769.966667
#EXTINF:0.233333,no-desc
Fragments(video=1583487699666666,format=m3u8-aapl-v8)
#EXT-X-CUE:ID=95766,TYPE="SpliceOut",DURATION=30.000000,TIME=158348769.966667,ELAPSED=0.233333
#EXTINF:6.400000,no-desc
Fragments(video=1583487702000000,format=m3u8-aapl-v8)
#EXT-X-CUE:ID=95766,TYPE="SpliceOut",DURATION=30.000000,TIME=158348769.966667,ELAPSED=6.633333
#EXTINF:6.400000,no-desc
Fragments(video=1583487766000000,format=m3u8-aapl-v8)
#EXT-X-CUE:ID=95766,TYPE="SpliceOut",DURATION=30.000000,TIME=158348769.966667,ELAPSED=13.033333
#EXTINF:6.400000,no-desc
Fragments(video=1583487830000000,format=m3u8-aapl-v8)
#EXT-X-CUE:ID=95766,TYPE="SpliceOut",DURATION=30.000000,TIME=158348769.966667,ELAPSED=19.433333
#EXTINF:6.400000,no-desc
Fragments(video=1583487894000000,format=m3u8-aapl-v8)
#EXT-X-CUE:ID=95766,TYPE="SpliceOut",DURATION=30.000000,TIME=158348769.966667,ELAPSED=25.833333
#EXTINF:4.166667,no-desc
Fragments(video=1583487958000000,format=m3u8-aapl-v8)
#EXTINF:2.233333,no-desc
Fragments(video=1583487999666666,format=m3u8-aapl-v8)
#EXTINF:6.400000,no-desc
Fragments(video=1583488022000000,format=m3u8-aapl-v8)
...
```

### <a name="216-cancellation-and-updates"></a>2.1.6 キャンセルと更新

同じプレゼンテーション時間と ID で複数のメッセージを送信することにより、メッセージをキャンセルまたは更新することができます。 プレゼンテーション時間と ID はイベントを一意に識別し、プレロール制約を満たす特定のプレゼンテーション時間で受信された最後のメッセージが、処理されるメッセージになります。 更新されたイベントは、以前に受信されたメッセージを置き換えます。 プレロール制約は 4 秒です。 プレゼンテーション時間の少なくとも 4 秒前に受信されたメッセージが処理されます。

## <a name="22-fragmented-mp4-ingest-smooth-streaming"></a>2.2 フラグメント化された MP4 の取り込み (Smooth Streaming)

ライブ ストリームの取り込みの要件については、[MS-SSTR-Ingest] をご覧ください。 以降のセクションでは、時間指定プレゼンテーション メタデータの取り込みに関する詳細情報を提供します。  時間指定プレゼンテーション メタデータは、スパース トラックとして取り込まれます。これは、Live Server Manifest Box (MS-SSTR を参照) と Movie Box ("moov") の両方で定義されています。  

各スパース フラグメントは、Movie Fragment Box ("moof") と Media Data Box ("mdat") で構成されており、"mdat" ボックスはバイナリ メッセージです。

フレーム単位で正確な広告の挿入を実現するために、エンコーダーは、キューを挿入する必要があるプレゼンテーション時間のところでフラグメントを分割しなければなりません。  [ISO-14496-12] 付録 I で定義されているように、新しく作成された IDR フレームで、あるいはタイプ 1 または 2 の Stream Access Point (SAP) で始まる新しいフラグメントを作成しなければなりません。
<!--- This allows the Azure Media Packager to properly generate an HLS manifest and a DASH multi-period manifest where the new Period begins at the frame-accurate splice conditioned presentation time. --->

### <a name="221-live-server-manifest-box"></a>2.2.1 Live Server Manifest Box

スパース トラックは、 **\<textstream\>** エントリを含む Live Server Manifest Box で宣言されて **いなければならず**、以下の属性が設定されて **いなければなりません**。

| **属性名** | **フィールドの型** | **必須** | **説明**                                                                                                                                                                                                              |
| ------------------ | -------------- | ------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| systemBitrate      | Number         | 必須      | "0" で **なければなりません**。不明な可変ビットレートのトラックを示します。                                                                                                                                                          |
| parentTrackName    | String         | 必須      | 親トラックの名前で **なければなりません**。スパース トラックの時間コードは親トラックのタイムスケールに揃えられます。 スパース トラックを親トラックにすることはできません。                                                                             |
| manifestOutput     | Boolean        | 必須      | "true" で **なければなりません**。スパース トラックが Smooth クライアント マニフェストに埋め込まれることを示します。                                                                                                                        |
| Subtype            | String         | 必須      | 4 文字のコード "DATA" で **なければなりません**。                                                                                                                                                                                  |
| Scheme             | String         | 必須      | メッセージのスキームを示す URN または URL で **なければなりません**。 [SCTE-35] メッセージの場合、[SCTE-35] に準拠する HLS、Smooth、Dash クライアントにメッセージが送信されるためには、これが "urn:scte:scte35:2013:bin" で **なければなりません**。 |
| trackName          | String         | 必須      | スパース トラックの名前で **なければなりません**。trackName を使って、同じスキームの複数のイベント ストリームを区別できます。 一意のイベント ストリームはそれぞれ、一意のトラック名を持って **いなければなりません**。                                |
| timescale          | Number         | 省略可能      | 親のトラックのタイムスケールで **なければなりません**。                                                                                                                                                                               |

---

### <a name="222-movie-box"></a>2.2.2 Movie Box

Movie Box ("moov") は、スパース トラックのストリーム ヘッダーの一部として、Live Server Manifest Box の後に続きます。

[ISO-14496-12] で定義されているように、"moov" ボックスは **TrackHeaderBox ("tkhd")** を含む **必要があり**、次の制約があります。

| **フィールド名** | **フィールドの型**          | **必須** | **説明**                                                                                                    |
| -------------- | ----------------------- | ------------- | ------------------------------------------------------------------------------------------------------------------ |
| duration       | 64 ビット符号なし整数 | 必須      | 0 である **必要があります**。これは、トラック ボックスは 0 サンプルであり、トラック ボックスのサンプルの合計継続時間は 0 であるためです。 |

---

[ISO-14496-12] で定義されているように、"moov" ボックスは **HandlerBox ("hdlr")** を含む **必要があり**、次の制約があります。

| **フィールド名** | **フィールドの型**          | **必須** | **説明**       |
| -------------- | ----------------------- | ------------- | --------------------- |
| handler_type   | 32 ビット符号なし整数 | 必須      | "meta" である **必要があります**。 |

---

"stsd" ボックスは、[ISO-14496-12] で定義されているコーディング名前を持つ MetaDataSampleEntry ボックスを含む **必要があります**。  たとえば、SCTE-35 メッセージの場合は、コーディング名は "scte" である **必要があります**。

### <a name="223-movie-fragment-box-and-media-data-box"></a>2.2.3 Movie Fragment Box と Media Data Box

スパース トラックのフラグメントは、Movie Fragment Box ("moof") と Media Data Box ("mdat") で構成されます。

> [!NOTE]
> フレーム単位で正確な広告の挿入を実現するために、エンコーダーは、キューを挿入する必要があるプレゼンテーション時間のところでフラグメントを分割しなければなりません。  [ISO-14496-12] 付録 I で定義されているように、新しく作成された IDR フレームで、あるいはタイプ 1 または 2 の Stream Access Point (SAP) で始まる新しいフラグメントを作成しなければなりません。
> 

MovieFragmentBox ("moof") ボックスは、[MS-SSTR] で定義された以下のフィールドを含む **TrackFragmentExtendedHeaderBox ("uuid")** ボックスを含んで **いなければなりません**。

| **フィールド名**         | **フィールドの型**          | **必須** | **説明**                                                                                           |
| ---------------------- | ----------------------- | ------------- | --------------------------------------------------------------------------------------------------------- |
| fragment_absolute_time | 64 ビット符号なし整数 | 必須      | イベントの到着時間で **なければなりません**。 この値により、メッセージと親トラックの位置が一致します。           |
| fragment_duration      | 64 ビット符号なし整数 | 必須      | イベントの継続時間で **なければなりません**。 継続時間は、値が不明であることを示す 0 にすることができます。 |

---


MediaDataBox ("mdat") ボックスは次のような形式で **なければなりません**。

| **フィールド名**          | **フィールドの型**                   | **必須** | **説明**                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                 |
| ----------------------- | -------------------------------- | ------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| version                 | 32 ビット符号なし整数 (uimsbf) | 必須      | "mdat" ボックスの内容の形式を決定します。 認識されないバージョンは無視されます。 現在サポートされている値は 1 だけです。                                                                                                                                                                                                                                                                                                                                                                      |
| id                      | 32 ビット符号なし整数 (uimsbf) | 必須      | メッセージのこのインスタンスを示します。 同じセマンティクスのメッセージは、同じ値を持つことになります。つまり、同じ ID を持ついずれか 1 つのイベント メッセージ ボックスを処理すれば十分です。                                                                                                                                                                                                                                                                                                                            |
| presentation_time_delta | 32 ビット符号なし整数 (uimsbf) | 必須      | TrackFragmentExtendedHeaderBox で指定されている fragment_absolute_time と presentation_time_delta の合計は、イベントのプレゼンテーション時間で **なければなりません**。 プレゼンテーション時間と継続時間は、[ISO-14496-12] Annex I で定義されているように、タイプ 1 または 2 のストリーム アクセス ポイント (SAP) と一致している **必要があります**。HLS エグレスの場合は、プレゼンテーション時間と継続時間はセグメントの境界と一致する **必要があります**。 同じイベント ストリームに含まれる異なるイベント メッセージのプレゼンテーション時間と継続時間は、オーバーラップ **してはなりません**。 |
| message                 | byte array                       | 必須      | イベント メッセージ。 [SCTE-35] メッセージの場合、メッセージはバイナリの splice_info_section() です。 [SCTE-35] メッセージの場合、[SCTE-35] に準拠する HLS、Smooth、Dash クライアントにメッセージが送信されるためには、このフィールドは splice_info_section() で **なければなりません**。 [SCTE-35] メッセージの場合、バイナリの splice_info_section() が "mdat" ボックスのペイロードであり、base64 でエンコードされては **いません**。                                                                                                                     |

---


### <a name="224-cancellation-and-updates"></a>2.2.4 キャンセルと更新

同じプレゼンテーション時間と ID で複数のメッセージを送信することにより、メッセージをキャンセルまたは更新することができます。  プレゼンテーション時間と ID によって、イベントが一意に識別されます。 プレロール制約を満たす特定のプレゼンテーション時間で受信された最後のメッセージが、処理されるメッセージになります。 更新されたメッセージは、以前に受信されたメッセージを置き換えます。  プレロール制約は 4 秒です。 プレゼンテーション時間の少なくとも 4 秒前に受信されたメッセージが処理されます。 


## <a name="3-timed-metadata-delivery"></a>3 時間指定メタデータの配信

イベント ストリーム データは、Media Services に対して非透過的です。 Media Services は、取り込みエンドポイントとクライアント エンドポイントの間で、3 種類の情報を渡すだけです。 [SCTE-35] およびクライアントのストリーミング プロトコルに準拠して、以下のプロパティがクライアントに配信されます。

1.  スキーム – メッセージのスキームを示す URN または URL。
2.  プレゼンテーション時間 – メディア タイムライン上でのイベントのプレゼンテーション時間。
3.  継続時間 – イベントの継続時間。
4.  ID – イベントの一意識別子 (オプション)。
5.  メッセージ – イベント データ。

## <a name="31-microsoft-smooth-streaming-manifest"></a>3.1 Microsoft Smooth Streaming マニフェスト  

スパース メッセージ トラックのフォーマット方法の詳細については、スパース トラック処理 [MS-SSTR] を参照してください。[SCTE-35] メッセージの場合、Smooth Streaming は base64 でエンコードされた splice_info_section() をスパース フラグメントに出力します。
StreamIndex には "DATA" の Subtype が **なければならず**、CustomAttributes には Name="Schema" かつ Value="urn:scte:scte35:2013:bin" の Attribute が含まれ **なければなりません**。

#### <a name="smooth-client-manifest-example-showing-base64-encoded-scte35-splice_info_section"></a>base64 でエンコードされた [SCTE-35] splice_info_section() を示す Smooth クライアントのマニフェストの例
```xml
<?xml version="1.0" encoding="utf-8"?>
<SmoothStreamingMedia MajorVersion="2" MinorVersion="0" TimeScale="10000000" IsLive="true" Duration="0"
  LookAheadFragmentCount="2" DVRWindowLength="6000000000">

  <StreamIndex Type="video" Name="video" Subtype="" Chunks="0" TimeScale="10000000"
    Url="QualityLevels({bitrate})/Fragments(video={start time})">
    <QualityLevel Index="0" Bitrate="230000"
      CodecPrivateData="250000010FC3460B50878A0B5821FF878780490800704704DC0000010E5A67F840" FourCC="WVC1"
      MaxWidth="364" MaxHeight="272"/>
    <QualityLevel Index="1" Bitrate="305000"
      CodecPrivateData="250000010FC3480B50878A0B5821FF87878049080894E4A7640000010E5A67F840" FourCC="WVC1"
      MaxWidth="364" MaxHeight="272"/>
    <c t="0" d="20000000" r="300" />
  </StreamIndex>
  <StreamIndex Type="audio" Name="audio" Subtype="" Chunks="0" TimeScale="10000000"
    Url="QualityLevels({bitrate})/Fragments(audio={start time})">
    <QualityLevel Index="0" Bitrate="96000" CodecPrivateData="1000030000000000000000000000E00042C0"
      FourCC="WMAP" AudioTag="354" Channels="2" SamplingRate="44100" BitsPerSample="16" PacketSize="4459"/>
    <c t="0" d="20000000" r="300" />
  </StreamIndex>
  <StreamIndex Type="text" Name="scte35-sparse-stream" Subtype="DATA" Chunks="0" TimeScale="10000000"
    ParentStreamIndex="video" ManifestOutput="true" 
    Url="QualityLevels({bitrate})/Fragments(captions={start time})">
    <QualityLevel Index="0" Bitrate="0" CodecPrivateData="" FourCC="">
      <CustomAttributes>
        <Attribute Name="Scheme" Value="urn:scte:scte35:2013:bin"/>
      </CustomAttributes>
    </QualityLevel>
    <!-- The following <c> and <f> fragments contains the base64-encoded [SCTE35] splice_info_section() message -->
    <c t="600000000" d="300000000">    <f>PD94bWwgdmVyc2lvbj0iMS4wIiBlbmNvZGluZz0idXRmLTgiPz48QWNxdWlyZWRTaWduYWwgeG1sbnM9InVybjpjYWJsZWxhYnM6bWQ6eHNkOnNpZ25hbGluZzozLjAiIGFjcXVpc2l0aW9uUG9pbnRJZGVudGl0eT0iRVNQTl9FYXN0X0FjcXVpc2l0aW9uX1BvaW50XzEiIGFjcXVpc2l0aW9uU2lnbmFsSUQ9IjRBNkE5NEVFLTYyRkExMUUxQjFDQTg4MkY0ODI0MDE5QiIgYWNxdWlzaXRpb25UaW1lPSIyMDEyLTA5LTE4VDEwOjE0OjI2WiI+PFVUQ1BvaW50IHV0Y1BvaW50PSIyMDEyLTA5LTE4VDEwOjE0OjM0WiIvPjxTQ1RFMzVQb2ludERlc2NyaXB0b3Igc3BsaWNlQ29tbWFuZFR5cGU9IjUiPjxTcGxpY2VJbnNlcnQgc3BsaWNlRXZlbnRJRD0iMzQ0NTY4NjkxIiBvdXRPZk5ldHdvcmtJbmRpY2F0b3I9InRydWUiIHVuaXF1ZVByb2dyYW1JRD0iNTUzNTUiIGR1cmF0aW9uPSJQVDFNMFMiIGF2YWlsTnVtPSIxIiBhdmFpbHNFeHBlY3RlZD0iMTAiLz48L1NDVEUzNVBvaW50RGVzY3JpcHRvcj48U3RyZWFtVGltZXM+PFN0cmVhbVRpbWUgdGltZVR5cGU9IkhTUyIgdGltZVZhbHVlPSI1MTUwMDAwMDAwMDAiLz48L1N0cmVhbVRpbWVzPjwvQWNxdWlyZWRTaWduYWw+</f>
    </c>
    <c t="1200000000" d="400000000">      <f>PD94bWwgdmVyc2lvbj0iMS4wIiBlbmNvZGluZz0idXRmLTgiPz48QWNxdWlyZWRTaWduYWwgeG1sbnM9InVybjpjYWJsZWxhYnM6bWQ6eHNkOnNpZ25hbGluZzozLjAiIGFjcXVpc2l0aW9uUG9pbnRJZGVudGl0eT0iRVNQTl9FYXN0X0FjcXVpc2l0aW9uX1BvaW50XzEiIGFjcXVpc2l0aW9uU2lnbmFsSUQ9IjRBNkE5NEVFLTYyRkExMUUxQjFDQTg4MkY0ODI0MDE5QiIgYWNxdWlzaXRpb25UaW1lPSIyMDEyLTA5LTE4VDEwOjE0OjI2WiI+PFVUQ1BvaW50IHV0Y1BvaW50PSIyMDEyLTA5LTE4VDEwOjE0OjM0WiIvPjxTQ1RFMzVQb2ludERlc2NyaXB0b3Igc3BsaWNlQ29tbWFuZFR5cGU9IjUiPjxTcGxpY2VJbnNlcnQgc3BsaWNlRXZlbnRJRD0iMzQ0NTY4NjkxIiBvdXRPZk5ldHdvcmtJbmRpY2F0b3I9InRydWUiIHVuaXF1ZVByb2dyYW1JRD0iNTUzNTUiIGR1cmF0aW9uPSJQVDFNMFMiIGF2YWlsTnVtPSIxIiBhdmFpbHNFeHBlY3RlZD0iMTAiLz48L1NDVEUzNVBvaW50RGVzY3JpcHRvcj48U3RyZWFtVGltZXM+PFN0cmVhbVRpbWUgdGltZVR5cGU9IkhTUyIgdGltZVZhbHVlPSI1MTYyMDAwMDAwMDAiLz48L1N0cmVhbVRpbWVzPjwvQWNxdWlyZWRTaWduYWw+</f>
    </c>
  </StreamIndex>
</SmoothStreamingMedia>
```

## <a name="32-apple-hls-manifest-decoration"></a>3.2 Apple HLS マニフェストの装飾

Azure Media Services は、ライブまたはオンデマンド イベント中に広告可用性情報をシグナル通知するための、以下の HLS マニフェスト タグをサポートします。 

<!--- EXT-X-DATERANGE as defined in Apple HLS [RFC8216] --->
- [Adobe-Primetime] で定義されている EXT-X-CUE
<!--- this mode is considered "legacy".  Customers should adopt the EXT-X-DATERANGE tag when possible. --->

各タグへのデータ出力は、使用される取り込みシグナル モードによって異なります。 たとえば、Adobe Simple モードでの RTMP 取り込みには、完全な SCTE-35 base64 エンコード済みペイロードが含まれていません。

<!---
## 3.2.1 Apple HLS with EXT-X-DATERANGE (recommended)

The Apple HTTP Live Streaming [RFC8216] specification allows for signaling of [SCTE-35] messages. The messages are inserted into the segment playlist in an EXT-X-DATERANGE tag per [RFC8216] section titled "Mapping SCTE-35 into EXT-X-DATERANGE".  The client application layer can parse the M3U playlist and process M3U tags, or receive the events through the Apple player framework.  

The **RECOMMENDED** approach in Azure Media Services (version 3 API) is to follow [RFC8216] and use the EXT-X_DATERANGE tag for [SCTE35] ad avail decoration in the manifest.
--->


## <a name="3211-example-hls-manifest-m3u8-showing-ext-x-cue-signaling-of-scte-35"></a>3.2.1.1 SCTE-35 の EXT-X-CUE シグナル通知を示す HLS マニフェスト .m3u8 の例

次の Media Services ダイナミック パッケージャーからの HLS マニフェスト出力の例は、[Adobe-Primetime] の SCTE35 モードの EXT-X-CUE タグを示しています。 

```
#EXTM3U
#EXT-X-VERSION:8
#EXT-X-MEDIA-SEQUENCE:0
#EXT-X-TARGETDURATION:2
#EXT-X-INDEPENDENT-SEGMENTS
#EXT-X-PROGRAM-DATE-TIME:2020-01-07T19:40:50Z
#EXTINF:1.501500,no-desc
Fragments(video=22567545,format=m3u8-aapl-v8)
#EXTINF:1.501500,no-desc
Fragments(video=22702680,format=m3u8-aapl-v8)
#EXTINF:1.501500,no-desc
Fragments(video=22837815,format=m3u8-aapl-v8)
#EXTINF:1.501500,no-desc
Fragments(video=22972950,format=m3u8-aapl-v8)
#EXTINF:1.501500,no-desc
Fragments(video=23108085,format=m3u8-aapl-v8)
#EXTINF:1.234567,no-desc
Fragments(video=23243220,format=m3u8-aapl-v8)
#EXTINF:0.016689,no-desc
Fragments(video=23354331,format=m3u8-aapl-v8)
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=0.000022
#EXTINF:0.250244,no-desc
Fragments(video=23355833,format=m3u8-aapl-v8)
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=0.250267
#EXTINF:0.850856,no-desc
Fragments(video=23378355,format=m3u8-aapl-v8)
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=1.101122
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=0.000000,TIME=260.610344,CUE="/DAgAAAAAAXdAP/wDwUAAAPqf0/+AWXk0wABAQEAAGB86Fo="
#EXTINF:0.650644,no-desc
Fragments(video=23454932,format=m3u8-aapl-v8)
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=1.751767
#EXTINF:0.050044,no-desc
Fragments(video=23513490,format=m3u8-aapl-v8)
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=1.801811
#EXTINF:1.451456,no-desc
Fragments(video=23517994,format=m3u8-aapl-v8)
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=3.253267
#EXTINF:1.501500,no-desc
Fragments(video=23648625,format=m3u8-aapl-v8)
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=4.754767
#EXTINF:1.501500,no-desc
Fragments(video=23783760,format=m3u8-aapl-v8)
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=6.256267
#EXTINF:1.501500,no-desc
Fragments(video=23918895,format=m3u8-aapl-v8)
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=7.757767
#EXTINF:1.501500,no-desc
Fragments(video=24054030,format=m3u8-aapl-v8)
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=9.259267
#EXTINF:1.501500,no-desc
Fragments(video=24189165,format=m3u8-aapl-v8)
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=10.760767
#EXTINF:1.501500,no-desc
Fragments(video=24324300,format=m3u8-aapl-v8)
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=12.262267
#EXTINF:1.501500,no-desc
Fragments(video=24459435,format=m3u8-aapl-v8)
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=13.763767
#EXTINF:1.501500,no-desc
Fragments(video=24594570,format=m3u8-aapl-v8)
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=15.265267
#EXTINF:1.501500,no-desc
Fragments(video=24729705,format=m3u8-aapl-v8)
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=16.766767
#EXTINF:1.501500,no-desc
Fragments(video=24864840,format=m3u8-aapl-v8)
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=18.268267
#EXTINF:1.501500,no-desc
Fragments(video=24999975,format=m3u8-aapl-v8)
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=19.769767
#EXTINF:1.501500,no-desc
Fragments(video=25135110,format=m3u8-aapl-v8)
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=21.271267
#EXTINF:1.501500,no-desc
Fragments(video=25270245,format=m3u8-aapl-v8)
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=22.772767
#EXTINF:1.501500,no-desc
Fragments(video=25405380,format=m3u8-aapl-v8)
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=24.274267
#EXTINF:1.501500,no-desc
Fragments(video=25540515,format=m3u8-aapl-v8)
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=25.775767
#EXTINF:1.501500,no-desc
Fragments(video=25675650,format=m3u8-aapl-v8)
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=27.277267
#EXTINF:1.501500,no-desc
Fragments(video=25810785,format=m3u8-aapl-v8)
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=28.778767
#EXTINF:1.501500,no-desc
Fragments(video=25945920,format=m3u8-aapl-v8)
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=30.280267
#EXTINF:1.501500,no-desc
Fragments(video=26081055,format=m3u8-aapl-v8)
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=31.781767
#EXTINF:1.501500,no-desc
Fragments(video=26216190,format=m3u8-aapl-v8)
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=33.283267
#EXTINF:1.501500,no-desc
Fragments(video=26351325,format=m3u8-aapl-v8)
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=34.784767
#EXTINF:1.501500,no-desc
Fragments(video=26486460,format=m3u8-aapl-v8)
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=36.286267
#EXTINF:1.501500,no-desc
Fragments(video=26621595,format=m3u8-aapl-v8)
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=37.787767
#EXTINF:1.501500,no-desc
Fragments(video=26756730,format=m3u8-aapl-v8)
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=39.289267
#EXTINF:1.501500,no-desc
Fragments(video=26891865,format=m3u8-aapl-v8)
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=40.790767
#EXTINF:1.501500,no-desc
Fragments(video=27027000,format=m3u8-aapl-v8)
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=42.292267
#EXTINF:1.501500,no-desc
Fragments(video=27162135,format=m3u8-aapl-v8)
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=43.793767
#EXTINF:1.501500,no-desc
Fragments(video=27297270,format=m3u8-aapl-v8)
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=45.295267
#EXTINF:1.501500,no-desc
Fragments(video=27432405,format=m3u8-aapl-v8)
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=46.796767
#EXTINF:1.501500,no-desc
Fragments(video=27567540,format=m3u8-aapl-v8)
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=48.298267
#EXTINF:1.501500,no-desc
Fragments(video=27702675,format=m3u8-aapl-v8)
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=49.799767
#EXTINF:1.501500,no-desc
Fragments(video=27837810,format=m3u8-aapl-v8)
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=51.301267
#EXTINF:1.501500,no-desc
Fragments(video=27972945,format=m3u8-aapl-v8)
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=52.802767
#EXTINF:1.501500,no-desc
Fragments(video=28108080,format=m3u8-aapl-v8)
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=54.304267
#EXTINF:1.501500,no-desc
Fragments(video=28243215,format=m3u8-aapl-v8)
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=55.805767
#EXTINF:1.501500,no-desc
Fragments(video=28378350,format=m3u8-aapl-v8)
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=57.307267
#EXTINF:1.501500,no-desc
Fragments(video=28513485,format=m3u8-aapl-v8)
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=58.808767
#EXTINF:1.501500,no-desc
Fragments(video=28648620,format=m3u8-aapl-v8)

```


<!---
THIS VERSION HAS THE HLSv8 DATERANGE Tags in it
~~~
#EXTM3U
#EXT-X-VERSION:8
#EXT-X-MEDIA-SEQUENCE:0
#EXT-X-TARGETDURATION:2
#EXT-X-INDEPENDENT-SEGMENTS
#EXT-X-PROGRAM-DATE-TIME:2020-01-07T19:40:50Z
#EXTINF:1.501500,no-desc
Fragments(video=22567545,format=m3u8-aapl-v8)
#EXTINF:1.501500,no-desc
Fragments(video=22702680,format=m3u8-aapl-v8)
#EXTINF:1.501500,no-desc
Fragments(video=22837815,format=m3u8-aapl-v8)
#EXTINF:1.501500,no-desc
Fragments(video=22972950,format=m3u8-aapl-v8)
#EXTINF:1.501500,no-desc
Fragments(video=23108085,format=m3u8-aapl-v8)
#EXTINF:1.234567,no-desc
Fragments(video=23243220,format=m3u8-aapl-v8)
#EXTINF:0.016689,no-desc
Fragments(video=23354331,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=0.000022
#EXTINF:0.250244,no-desc
Fragments(video=23355833,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=0.250267
#EXTINF:0.850856,no-desc
Fragments(video=23378355,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=1.101122
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:10.610Z",SCTE35-IN=0xFC30200000000005DD00FFF00F05000003EA7F4FFE0165E4D3000101010000607CE85A
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=0.000000,TIME=260.610344,CUE="/DAgAAAAAAXdAP/wDwUAAAPqf0/+AWXk0wABAQEAAGB86Fo="
#EXTINF:0.650644,no-desc
Fragments(video=23454932,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=1.751767
#EXTINF:0.050044,no-desc
Fragments(video=23513490,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=1.801811
#EXTINF:1.451456,no-desc
Fragments(video=23517994,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=3.253267
#EXTINF:1.501500,no-desc
Fragments(video=23648625,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=4.754767
#EXTINF:1.501500,no-desc
Fragments(video=23783760,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=6.256267
#EXTINF:1.501500,no-desc
Fragments(video=23918895,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=7.757767
#EXTINF:1.501500,no-desc
Fragments(video=24054030,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=9.259267
#EXTINF:1.501500,no-desc
Fragments(video=24189165,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=10.760767
#EXTINF:1.501500,no-desc
Fragments(video=24324300,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=12.262267
#EXTINF:1.501500,no-desc
Fragments(video=24459435,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=13.763767
#EXTINF:1.501500,no-desc
Fragments(video=24594570,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=15.265267
#EXTINF:1.501500,no-desc
Fragments(video=24729705,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=16.766767
#EXTINF:1.501500,no-desc
Fragments(video=24864840,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=18.268267
#EXTINF:1.501500,no-desc
Fragments(video=24999975,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=19.769767
#EXTINF:1.501500,no-desc
Fragments(video=25135110,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=21.271267
#EXTINF:1.501500,no-desc
Fragments(video=25270245,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=22.772767
#EXTINF:1.501500,no-desc
Fragments(video=25405380,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=24.274267
#EXTINF:1.501500,no-desc
Fragments(video=25540515,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=25.775767
#EXTINF:1.501500,no-desc
Fragments(video=25675650,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=27.277267
#EXTINF:1.501500,no-desc
Fragments(video=25810785,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=28.778767
#EXTINF:1.501500,no-desc
Fragments(video=25945920,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=30.280267
#EXTINF:1.501500,no-desc
Fragments(video=26081055,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=31.781767
#EXTINF:1.501500,no-desc
Fragments(video=26216190,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=33.283267
#EXTINF:1.501500,no-desc
Fragments(video=26351325,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=34.784767
#EXTINF:1.501500,no-desc
Fragments(video=26486460,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=36.286267
#EXTINF:1.501500,no-desc
Fragments(video=26621595,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=37.787767
#EXTINF:1.501500,no-desc
Fragments(video=26756730,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=39.289267
#EXTINF:1.501500,no-desc
Fragments(video=26891865,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=40.790767
#EXTINF:1.501500,no-desc
Fragments(video=27027000,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=42.292267
#EXTINF:1.501500,no-desc
Fragments(video=27162135,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=43.793767
#EXTINF:1.501500,no-desc
Fragments(video=27297270,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=45.295267
#EXTINF:1.501500,no-desc
Fragments(video=27432405,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=46.796767
#EXTINF:1.501500,no-desc
Fragments(video=27567540,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=48.298267
#EXTINF:1.501500,no-desc
Fragments(video=27702675,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=49.799767
#EXTINF:1.501500,no-desc
Fragments(video=27837810,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=51.301267
#EXTINF:1.501500,no-desc
Fragments(video=27972945,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=52.802767
#EXTINF:1.501500,no-desc
Fragments(video=28108080,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=54.304267
#EXTINF:1.501500,no-desc
Fragments(video=28243215,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=55.805767
#EXTINF:1.501500,no-desc
Fragments(video=28378350,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=57.307267
#EXTINF:1.501500,no-desc
Fragments(video=28513485,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=58.808767
#EXTINF:1.501500,no-desc
Fragments(video=28648620,format=m3u8-aapl-v8)

~~~

--->

## <a name="322-apple-hls-with-adobe-primetime-ext-x-cue"></a>3.2.2 Adobe Primetime EXT-X-CUE を使用した Apple HLS

Media Services (バージョン 2 および 3 API) では、[Adobe-Primetime] "SCTE-35 Mode" で定義されているように、EXT-X-CUE タグの出力がサポートされています。 このモードでは、Azure Media Services は base64 でエンコードされた [SCTE-35] splice_info_section() を EXT-X-CUE タグに埋め込みます。  

"レガシ" EXT-X-CUE タグは次のように定義されており、[Adobe-Primetime] 仕様でも標準リファレンスを確認できます。 これは、必要な場合にレガシ SCTE-35 シグナル通知にのみ使用する必要があります。そうでない場合の推奨タグは [RFC8216] で EXT-X-DATERANGE と定義されています。 

| **属性名** | **Type**                      | **必須**                             | **説明**                                                                                                                                                                                                                                                                          |
| ------------------ | ----------------------------- | ----------------------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| CUE                | 引用符で囲まれた文字列                 | 必須                                  | [RFC4648] で説明されているように、base64 エンコード文字列としてエンコードされたメッセージ。 [SCTE-35] メッセージの場合、これは base64 でエンコードされた splice_info_section() です。                                                                                                                                      |
| TYPE               | 引用符で囲まれた文字列                 | 必須                                  | メッセージのスキームを示す URN または URL。 [SCTE-35] メッセージの場合、タイプは特別な値 "scte35" です。                                                                                                                                                                          |
| id                 | 引用符で囲まれた文字列                 | 必須                                  | イベントの一意識別子。 メッセージが取り込まれときに ID が指定されていない場合は、Azure Media Services が一意 ID を生成します。                                                                                                                                              |
| DURATION           | 10 進浮動小数点数 | 必須                                  | イベントの期間。 わからない場合は、0 にする **必要があります**。 単位は秒の小数部です。                                                                                                                                                                                           |
| ELAPSED            | 10 進浮動小数点数 | オプション、ただしスライディング ウィンドウの場合は必須。 | スライディング プレゼンテーション ウィンドウをサポートするためにシグナルを繰り返す場合、このフィールドは、イベントが開始してから経過したプレゼンテーション時間の長さで **なければなりません**。 単位は秒の小数部です。 この値はスプライスまたはセグメントで指定されている元の継続時間を超えてもかまいません。 |
| TIME               | 10 進浮動小数点数 | 必須                                  | イベントのプレゼンテーション時間。 単位は秒の小数部です。                                                                                                                                                                                                                        |

HLS プレーヤーのアプリケーション レイヤーは、TYPE を使って、メッセージの形式を識別し、メッセージをデコードし、必要な時間変換を適用して、イベントを処理します。  イベントは、イベントのタイムスタンプに従って、親トラックのセグメント プレイリストの時間と同期されます。  イベントは、最も近いセグメント (#EXTINF タグ) の前に挿入されます。


### <a name="323-hls-m3u8-manifest-example-using-adobe-primetime-ext-x-cue"></a>3.2.3 Adobe Primetime EXT-X-CUE を使用した HLS .m3u8 マニフェストの例

次の例は、Adobe Primetime EXT-X-CUE タグを使用した HLS マニフェスト装飾を示しています。  "CUE" パラメーターには TYPE プロパティと Duration プロパティのみが含まれ、これが Adobe "シンプル" モードのシグナル通知を使用した RTMP ソースであったことを意味します。  
<!---If this was a SCTE-35 mode signal, the tag would include the base64 encoded binary SCTE-35 payload as seen in the [3.2.1.1 example](#3211-example-hls-manifest-m3u8-showing-ext-x-daterange-signaling-of-scte-35).
--->


```
#EXTM3U
#EXT-X-VERSION:4
#EXT-X-PLAYLIST-TYPE:VOD
#EXT-X-ALLOW-CACHE:NO
#EXT-X-MEDIA-SEQUENCE:0
#EXT-X-TARGETDURATION:11
#EXT-X-PROGRAM-DATE-TIME:2019-12-10T09:18:14Z
#EXTINF:10.010000,no-desc
Fragments(video=4011540820,format=m3u8-aapl)
#EXTINF:10.010000,no-desc
Fragments(video=4011550830,format=m3u8-aapl)
#EXTINF:10.010000,no-desc
Fragments(video=4011560840,format=m3u8-aapl)
#EXT-X-CUE:ID=4011578265,TYPE="SpliceOut",DURATION=119.987000,TIME=4011578.265000
#EXTINF:8.008000,no-desc
Fragments(video=4011570850,format=m3u8-aapl)
#EXT-X-CUE:ID=4011578265,TYPE="SpliceOut",DURATION=119.987000,TIME=4011578.265000,ELAPSED=0.593000
#EXTINF:4.170000,no-desc
Fragments(video=4011578858,format=m3u8-aapl)
#EXT-X-CUE:ID=4011578265,TYPE="SpliceOut",DURATION=119.987000,TIME=4011578.265000,ELAPSED=4.763000
#EXTINF:9.844000,no-desc
Fragments(video=4011583028,format=m3u8-aapl)
#EXT-X-CUE:ID=4011578265,TYPE="SpliceOut",DURATION=119.987000,TIME=4011578.265000,ELAPSED=14.607000
#EXTINF:10.010000,no-desc
Fragments(video=4011592872,format=m3u8-aapl)
#EXT-X-CUE:ID=4011578265,TYPE="SpliceOut",DURATION=119.987000,TIME=4011578.265000,ELAPSED=24.617000
#EXTINF:10.010000,no-desc
Fragments(video=4011602882,format=m3u8-aapl)
#EXT-X-CUE:ID=4011578265,TYPE="SpliceOut",DURATION=119.987000,TIME=4011578.265000,ELAPSED=34.627000
#EXTINF:10.010000,no-desc
Fragments(video=4011612892,format=m3u8-aapl)
#EXT-X-CUE:ID=4011578265,TYPE="SpliceOut",DURATION=119.987000,TIME=4011578.265000,ELAPSED=44.637000
#EXTINF:10.010000,no-desc
Fragments(video=4011622902,format=m3u8-aapl)
#EXT-X-CUE:ID=4011578265,TYPE="SpliceOut",DURATION=119.987000,TIME=4011578.265000,ELAPSED=54.647000
#EXTINF:10.010000,no-desc
Fragments(video=4011632912,format=m3u8-aapl)
#EXT-X-CUE:ID=4011578265,TYPE="SpliceOut",DURATION=119.987000,TIME=4011578.265000,ELAPSED=64.657000
#EXTINF:10.010000,no-desc
Fragments(video=4011642922,format=m3u8-aapl)
#EXT-X-CUE:ID=4011578265,TYPE="SpliceOut",DURATION=119.987000,TIME=4011578.265000,ELAPSED=74.667000
#EXTINF:10.010000,no-desc
Fragments(video=4011652932,format=m3u8-aapl)
#EXT-X-CUE:ID=4011578265,TYPE="SpliceOut",DURATION=119.987000,TIME=4011578.265000,ELAPSED=84.677000
#EXTINF:10.010000,no-desc
Fragments(video=4011662942,format=m3u8-aapl)
#EXT-X-CUE:ID=4011578265,TYPE="SpliceOut",DURATION=119.987000,TIME=4011578.265000,ELAPSED=94.687000
#EXTINF:10.010000,no-desc
Fragments(video=4011672952,format=m3u8-aapl)
#EXT-X-CUE:ID=4011578265,TYPE="SpliceOut",DURATION=119.987000,TIME=4011578.265000,ELAPSED=104.697000
#EXTINF:10.010000,no-desc
Fragments(video=4011682962,format=m3u8-aapl)
#EXT-X-CUE:ID=4011578265,TYPE="SpliceOut",DURATION=119.987000,TIME=4011578.265000,ELAPSED=114.707000
#EXTINF:10.010000,no-desc
Fragments(video=4011692972,format=m3u8-aapl)
#EXTINF:8.008000,no-desc
Fragments(video=4011702982,format=m3u8-aapl)

```

### <a name="324-hls-message-handling-for-adobe-primetime-ext-x-cue"></a>3.2.4 Adobe Primetime EXT-X-CUE 向けの HLS メッセージ処理

イベントは、各ビデオ トラックおよびオーディオ トラックのセグメント プレイリスト内でシグナル通知されます。[Adobe-Primetime] で要求されているように、EXT-X-CUE タグの位置は、常に、TIME および DURATION 属性によって参照される最初の HLS セグメントの直前 (スプライス アウトまたはセグメント開始の場合)、または最後の HLS セグメントの直後 (スプライス インまたはセグメント終了の場合) で **なければなりません**。

スライディング プレゼンテーション ウィンドウが有効になっているときは、[Adobe-Primetime] で要求されているように、EXT-X-CUE タグはスプライスまたはセグメントがセグメント プレイリストで常に完全に記述されるのに十分な回数だけ繰り返され **なければならず**、ELAPSED 属性を使ってスプライスまたはセグメントがアクティブになっていた時間が示され **なければなりません**。

スライディング プレゼンテーション ウィンドウが有効になっている場合、EXT-X-CUE タグで参照されているメディア時間がスライディング プレゼンテーション ウィンドウから出たときは、EXT-X-CUE タグがセグメント プレイリストから削除されます。

## <a name="33-dash-manifest-decoration-mpd"></a>3.3 DASH マニフェスト装飾 (MPD)

[MPEGDASH] では、イベントをシグナル通知する 3 つの方法が用意されています。

1.  MPD EventStream でシグナル通知されるイベント
2.  Event Message Box ("emsg") を使用してインバンドでシグナル通知されるイベント
3.  1 と 2 の組み合わせ

MPD EventStream でシグナル通知されるイベントは、MPD がダウンロードされるとすぐにクライアントはすべてのイベントにアクセスできるため、VOD ストリーミングに便利です。 下流の SSAI ベンダーが MPD マニフェストからのシグナルを解析して広告コンテンツを動的に挿入する必要がある SSAI シグナル通知にも役立ちます。  インバンド ("emsg") ソリューションは、クライアントが MPD を再ダウンロードする必要がない、またはクライアントとオリジン間で SSAI マニフェスト操作が発生しないライブ ストリーミングに役立ちます。 

Azure Media Services の DASH 向けの既定の動作では、Event Message Box ("emsg") を使用して MPD EventStream とインバンドの両方でシグナル通知します。

[RTMP] または [MS-SSTR-Ingest] によって取り込まれたキュー メッセージは、インバンド "emsg" ボックスと MPD 内 EventStream のどちらかまたは両方を使用して DASH イベントマップされます。 

DASH 向けのインバンド SCTE-35 シグナル通知は、[SCTE-214-3] で、また [DASH-IF-IOP] のセクション 13.12.2 ("SCTE35 Events") で定義されている定義および要件に従います。 

インバンド [SCTE-35] キャリッジの場合、Event Message Box ("emsg") は schemeId = "urn:scte:scte35:2013:bin" を使用します。 MPD マニフェスト装飾の場合、EventStream schemeId は "urn:scte:scte35:2014:xml+bin" を使用します。  この形式はイベントの XML 表現であり、取り込み時に到着した完全な SCTE-35 メッセージのバイナリ base64 エンコード済み出力を含みます。 

DASH における [SCTE-35] キュー メッセージのキャリッジの標準リファレンス定義は、[SCTE-214-1] のセクション 6.7.4 (MPD) および [SCTE-214-3] のセクション 7.3.2 (SCTE 35 キュー メッセージのキャリッジ) にあります。

### <a name="331-mpeg-dash-mpd-eventstream-signaling"></a>3.3.1 MPEG DASH (MPD) EventStream シグナル通知

イベントのマニフェスト (MPD) 装飾は、Period 要素内に出現する EventStream 要素を使って、MPD でシグナル通知されます。 使用される schemeId は "urn:scte:scte35:2014:xml+bin" です。


> [!NOTE]
> 簡潔さのために、[SCTE-35] では、完全に解析されたキュー メッセージのキャリッジの代替として、(Signal.SpliceInfoSection 要素ではなく) Signal.Binary 要素内の base64 エンコード済みセクションの使用を許可しています。
> Azure Media Services では、この "xml+bin" アプローチを使用して MPD マニフェストをシグナル通知します。
> これは [DASH-IF-IOP] で使用される推奨方式でもあります。[DASHIF IOP ガイドラインの "Ad insertion event streams"](https://dashif-documents.azurewebsites.net/DASH-IF-IOP/master/DASH-IF-IOP.html#ads-insertion-event-streams) (広告挿入イベント ストリーム) というタイトルのセクションを参照してください。
> 


EventStream 要素には次の属性があります。

| **属性名** | **Type**                | **必須** | **説明**                                                                                                                                                                                                                                                                                                                                                                         |
| ------------------ | ----------------------- | ------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| scheme_id_uri      | string                  | 必須      | メッセージのスキームを示します。 スキームは、Live Server Manifest Box の Scheme 属性の値に設定されます。 値は、メッセージ スキームを識別する URN または URL である **必要があります**。サポートされている出力 schemeId は、[SCTE-214-1] のセクション 6.7.4 (MPD) に従って "urn:scte:scte35:2014:xml+bin" である必要があります。これは、サービスは現時点で、簡潔さのために MPD では "xml+bin" のみをサポートしているためです。 |
| value              | string                  | 省略可能      | メッセージのセマンティクスをカスタマイズするためにスキームの所有者によって使われる追加の文字列値。 スキームが同じ複数のイベント ストリームを区別するため、値はイベント ストリームの名前に設定 **しなければなりません** ([MS-SSTR-Ingest] の場合は trackName、[RTMP] の取り込みの場合は AMF メッセージ名)。                                                                         |
| タイムスケール          | 32 ビット符号なし整数 | 必須      | タイムスケール (単位は 1 秒あたりのティック数)。                                                                                                                                                                                                                                                                                                                                                     |


### <a name="332-example-event-streams-for-mpeg-dash"></a>3.3.2 MPEG DASH のイベント ストリームの例

#### <a name="3321-example-mpeg-dash-mpd-manifest-signaling-of-rtmp-streaming-using-adobe-simple-mode"></a>3.3.2.1 Adobe シンプル モードを使用した RTMP ストリーミングの MPEG DASH .mpd マニフェストのシグナル通知の例

次の例は、Adobe "シンプル" モードのシグナル通知を使用した RTMP ストリームの、Media Services ダイナミック パッケージャーからの EventStream の抜粋を示しています。

```xml
<!-- Example EventStream element using "urn:com:adobe:dpi:simple:2015" Adobe simple signaling per [Adobe-Primetime] -->
    <EventStream schemeIdUri="urn:com:adobe:dpi:simple:2015" value="simplesignal" timescale="10000000">
        <Event presentationTime="1583497601000000" duration="300000000" id="1085900"/>
        <Event presentationTime="1583500901666666" duration="300000000" id="1415966"/>
        <Event presentationTime="1583504202333333" duration="300000000" id="1746033"/>
        <Event presentationTime="1583507502666666" duration="300000000" id="2076066"/>
        <Event presentationTime="1583510803333333" duration="300000000" id="2406133"/>
        <Event presentationTime="1583514104000000" duration="300000000" id="2736200"/>
        <Event presentationTime="1583517404666666" duration="300000000" id="3066266"/>
        <Event presentationTime="1583520705333333" duration="300000000" id="3396333"/>
        <Event presentationTime="1583524006000000" duration="300000000" id="3726400"/>
        <Event presentationTime="1583527306666666" duration="300000000" id="4056466"/>
        <Event presentationTime="1583530607333333" duration="300000000" id="4386533"/>
    </EventStream>
```


#### <a name="3322-example-mpeg-dash-mpd-manifest-signaling-of-an-rtmp-stream-using-adobe-scte-35-mode"></a>3.3.2.2 Adobe SCTE-35 モードを使用した RTMP ストリームの MPEG DASH .mpd マニフェストのシグナル通知の例

次の例は、Adobe SCTE-35 モードのシグナル通知を使用した RTMP ストリームの、Media Services ダイナミック パッケージャーからの EventStream の抜粋を示しています。

[SCTE-214-1] に基づく xml+bin スタイルのシグナル通知を使用する EventStream 要素の例

```xml

      <EventStream schemeIdUri="urn:scte:scte35:2014:xml+bin" value="scte35" timescale="10000000">
        <Event presentationTime="2595092444" duration="11011000" id="1002">
            <Signal xmlns="http://www.scte.org/schemas/35/2016">
                <Binary>/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==</Binary>
            </Signal>
        </Event>
        <Event presentationTime="2606103444" id="1002">
            <Signal xmlns="http://www.scte.org/schemas/35/2016">
                <Binary>/DAgAAAAAAXdAP/wDwUAAAPqf0/+AWXk0wABAQEAAGB86Fo=</Binary>
            </Signal>
        </Event>
      </EventStream>
```



> [!IMPORTANT]
> presentationTime は、[SCTE-35] イベントのプレゼンテーション時間を、メッセージの到着時間ではなく期間の開始時間が基準となるように変換したものであることに注意してください。
> [MPEGDASH] では Event@presentationTime を "Period の開始を基準とするイベントのプレゼンテーション時間を指定する" と定義しています。
> 秒単位のプレゼンテーション時間の値は、この属性の値と EventStream@timescale 属性の値の除算です。
> 存在しない場合、プレゼンテーション時間の値は 0 です。


#### <a name="3331-example-mpeg-dash-manifest-mpd-with-single-period-eventstream-using-adobe-simple-mode-signals"></a>3.3.3.1 Adobe シンプル モードのシグナルを使用した単一期間、EventStream での MPEG DASH マニフェスト (MPD) の例

次の例は、Adobe "シンプル" モードの広告シグナル通知方法を使用したソース RTMP ストリームの、Media Services ダイナミック パッケージャーからの出力を示しています。 この出力は、"urn:com:adobe:dpi:simple:2015" に設定されている schemeIdUri と "simplesignal" に設定されている value プロパティを使用した EventStream を示す単一期間のマニフェストです。
各シンプル シグナルは、Event 要素内に、受信したシンプル シグナルに基づいて設定された @presentationTime、@duration、および @id プロパティで示されます。

```xml
<?xml version="1.0" encoding="utf-8"?>
<MPD xmlns="urn:mpeg:dash:schema:mpd:2011" 
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" profiles="urn:mpeg:dash:profile:isoff-live:2011" type="static" mediaPresentationDuration="PT28M1.680S" minBufferTime="PT3S">
    <Period>
        <EventStream schemeIdUri="urn:com:adobe:dpi:simple:2015" value="simplesignal" timescale="1000">
            <Event presentationTime="4011578265" duration="119987" id="4011578265"/>
        </EventStream>
        <AdaptationSet id="1" group="1" profiles="ccff" bitstreamSwitching="false" segmentAlignment="true" contentType="video" mimeType="video/mp4" codecs="avc1.4D4028" maxWidth="1920" maxHeight="1080" startWithSAP="1">
            <InbandEventStream schemeIdUri="urn:com:adobe:dpi:simple:2015" value="simplesignal"/>
            <ProducerReferenceTime id="4011460740" type="0" wallClockTime="2020-01-25T19:35:54.740Z" presentationTime="4011460740"/>
            <SegmentTemplate timescale="1000" presentationTimeOffset="4011460740" media="QualityLevels($Bandwidth$)/Fragments(video=$Time$,format=mpd-time-csf)" initialization="QualityLevels($Bandwidth$)/Fragments(video=i,format=mpd-time-csf)">
                <SegmentTimeline>
                    <S t="4011460740" d="2002" r="57"/>
                    <S d="1401"/>
                    <S d="601"/>
                    <S d="2002"/>

                     <!--> ... video segments truncated for sample brevity </-->

                </SegmentTimeline>
            </SegmentTemplate>
            <Representation id="1_V_video_14759481473095519504" bandwidth="6000000" width="1920" height="1080"/>
            <Representation id="1_V_video_1516803357996956148" bandwidth="4000000" codecs="avc1.4D401F" width="1280" height="720"/>
            <Representation id="1_V_video_5430608182379669372" bandwidth="2600000" codecs="avc1.4D401F" width="960" height="540"/>
            <Representation id="1_V_video_3780180650986497347" bandwidth="1000000" codecs="avc1.4D401E" width="640" height="360"/>
            <Representation id="1_V_video_13759117363700265707" bandwidth="699000" codecs="avc1.4D4015" width="480" height="270"/>
            <Representation id="1_V_video_6140004908920393176" bandwidth="400000" codecs="avc1.4D4015" width="480" height="270"/>
            <Representation id="1_V_video_10673801877453424365" bandwidth="200000" codecs="avc1.4D400D" width="320" height="180"/>
        </AdaptationSet>
        <AdaptationSet id="2" group="5" profiles="ccff" bitstreamSwitching="false" segmentAlignment="true" contentType="audio" mimeType="audio/mp4" codecs="mp4a.40.2">
            <InbandEventStream schemeIdUri="urn:com:adobe:dpi:simple:2015" value="simplesignal"/>
            <ProducerReferenceTime id="4011460761" type="0" wallClockTime="2020-01-25T19:35:54.761Z" presentationTime="4011460761"/>
            <Label>audio</Label>
            <SegmentTemplate timescale="1000" presentationTimeOffset="4011460740" media="QualityLevels($Bandwidth$)/Fragments(audio=$Time$,format=mpd-time-csf)" initialization="QualityLevels($Bandwidth$)/Fragments(audio=i,format=mpd-time-csf)">
                <SegmentTimeline>
                    <S t="4011460761" d="1984"/>
                    <S d="2005" r="1"/>
                    <S d="2006"/>

                    <!--> ... audio segments truncated for example brevity </-->

                </SegmentTimeline>
            </SegmentTemplate>
            <Representation id="5_A_audio_17504386117102112482" bandwidth="128000" audioSamplingRate="48000"/>
        </AdaptationSet>
    </Period>
</MPD>

```

<!---
#### 3.3.3.2 Example MPEG DASH manifest (MPD) with multi-period, EventStream, using Adobe SCTE35 mode signaling

The following example shows the output from the Media Services dynamic packager for a source RTMP stream using the Adobe SCTE35 mode signaling.
In this case, the output manifest is a multi-period DASH .mpd with an EventStream element, and @schemeIdUri property set to "urn:scte:scte35:2014:xml+bin" and a @value property set to "scte35". Each Event element in the EventStream contains the full base64 encoded binary SCTE35 signal 

~~~ xml
<?xml version="1.0" encoding="utf-8"?>
<MPD xmlns="urn:mpeg:dash:schema:mpd:2011" 
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" profiles="urn:mpeg:dash:profile:isoff-live:2011" type="dynamic" publishTime="2020-01-07T19:42:44Z" minimumUpdatePeriod="PT0S" timeShiftBufferDepth="PT58M56S" availabilityStartTime="2020-01-07T19:40:50Z" minBufferTime="PT4S">
    <Period start="PT2M48.168S" id="main-content_0">
        <AdaptationSet id="1" group="1" profiles="ccff" bitstreamSwitching="false" segmentAlignment="true" contentType="video" mimeType="video/mp4" codecs="avc1.640020" maxWidth="1280" maxHeight="720" startWithSAP="1">
            <InbandEventStream schemeIdUri="urn:mpeg:dash:event:2012" value="1"/>
            <InbandEventStream schemeIdUri="urn:com:adobe:dpi:simple:2015" value="amssignal"/>
            <SegmentTemplate timescale="90000" presentationTimeOffset="15135120" media="QualityLevels($Bandwidth$)/Fragments(video=$Time$,format=mpd-time-csf)" initialization="QualityLevels($Bandwidth$)/Fragments(video=i,format=mpd-time-csf)">
                <SegmentTimeline>
                    <S t="15135120" d="135135" r="59"/>
                    <S d="111111"/>
                    <S d="1502"/>
                </SegmentTimeline>
            </SegmentTemplate>
            <ProducerReferenceTime id="15135120" type="0" wallClockTime="2020-01-07T19:40:50Z" presentationTime="15135120"/>
            <Representation id="1_V_video_5322324134428436312" bandwidth="3500000" width="1280" height="720"/>
            <Representation id="1_V_video_16981495139092747609" bandwidth="2200000" width="960" height="540"/>
            <Representation id="1_V_video_1384718563016940751" bandwidth="1350000" codecs="avc1.64001F" width="704" height="396"/>
            <Representation id="1_V_video_4425970933904124207" bandwidth="850000" codecs="avc1.64001E" width="512" height="288"/>
            <Representation id="1_V_video_11952982975776937431" bandwidth="550000" codecs="avc1.640016" width="384" height="216"/>
            <Representation id="1_V_video_10673801877453424365" bandwidth="200000" codecs="avc1.640015" width="340" height="192"/>
        </AdaptationSet>
        <AdaptationSet id="2" group="5" profiles="ccff" bitstreamSwitching="false" segmentAlignment="true" contentType="audio" mimeType="audio/mp4" codecs="mp4a.40.5" lang="en">
            <InbandEventStream schemeIdUri="urn:mpeg:dash:event:2012" value="1"/>
            <InbandEventStream schemeIdUri="urn:com:adobe:dpi:simple:2015" value="amssignal"/>
            <Label>audio</Label>
            <SegmentTemplate timescale="44100" presentationTimeOffset="7416208" media="QualityLevels($Bandwidth$)/Fragments(audio=$Time$,format=mpd-time-csf)" initialization="QualityLevels($Bandwidth$)/Fragments(audio=i,format=mpd-time-csf)">
                <SegmentTimeline>
                    <S t="7417856" d="133120"/>
                    <S d="132096" r="1"/>
                </SegmentTimeline>
            </SegmentTemplate>
            <ProducerReferenceTime id="7417856" type="0" wallClockTime="2020-01-07T19:40:50.037Z" presentationTime="7417856"/>
            <Representation id="5_A_audio_17504386117102112482" bandwidth="128000" audioSamplingRate="44100"/>
        </AdaptationSet>
    </Period>
    <Period start="PT4M19.509S" id="scte-35_0">
        <EventStream schemeIdUri="urn:scte:scte35:2014:xml+bin" value="scte35" timescale="10000000">
            <Event presentationTime="2595092444" duration="11011000" id="1002">
                <Signal xmlns="http://www.scte.org/schemas/35/2016">
                    <Binary>/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==</Binary>
                </Signal>
            </Event>
            <Event presentationTime="2606103444" id="1002">
                <Signal xmlns="http://www.scte.org/schemas/35/2016">
                    <Binary>/DAgAAAAAAXdAP/wDwUAAAPqf0/+AWXk0wABAQEAAGB86Fo=</Binary>
                </Signal>
            </Event>
        </EventStream>
        <AdaptationSet id="1" group="1" profiles="ccff" bitstreamSwitching="false" segmentAlignment="true" contentType="video" mimeType="video/mp4" codecs="avc1.640020" maxWidth="1280" maxHeight="720" startWithSAP="1">
            <InbandEventStream schemeIdUri="urn:mpeg:dash:event:2012" value="1"/>
            <InbandEventStream schemeIdUri="urn:com:adobe:dpi:simple:2015" value="amssignal"/>
            <SegmentTemplate timescale="90000" presentationTimeOffset="23355832" media="QualityLevels($Bandwidth$)/Fragments(video=$Time$,format=mpd-time-csf)" initialization="QualityLevels($Bandwidth$)/Fragments(video=i,format=mpd-time-csf)">
                <SegmentTimeline>
                    <S t="23355833" d="22522"/>
                    <S d="76577"/>
                </SegmentTimeline>
            </SegmentTemplate>
            <ProducerReferenceTime id="23355833" type="0" wallClockTime="2020-01-07T19:42:21.341Z" presentationTime="23355833"/>
            <Representation id="1_V_video_5322324134428436312" bandwidth="3500000" width="1280" height="720"/>
            <Representation id="1_V_video_16981495139092747609" bandwidth="2200000" width="960" height="540"/>
            <Representation id="1_V_video_1384718563016940751" bandwidth="1350000" codecs="avc1.64001F" width="704" height="396"/>
            <Representation id="1_V_video_4425970933904124207" bandwidth="850000" codecs="avc1.64001E" width="512" height="288"/>
            <Representation id="1_V_video_11952982975776937431" bandwidth="550000" codecs="avc1.640016" width="384" height="216"/>
            <Representation id="1_V_video_10673801877453424365" bandwidth="200000" codecs="avc1.640015" width="340" height="192"/>
        </AdaptationSet>
        <AdaptationSet id="2" group="5" profiles="ccff" bitstreamSwitching="false" segmentAlignment="true" contentType="audio" mimeType="audio/mp4" codecs="mp4a.40.5" lang="en">
            <InbandEventStream schemeIdUri="urn:mpeg:dash:event:2012" value="1"/>
            <InbandEventStream schemeIdUri="urn:com:adobe:dpi:simple:2015" value="amssignal"/>
            <Label>audio</Label>
            <SegmentTemplate timescale="44100" presentationTimeOffset="11444358" media="QualityLevels($Bandwidth$)/Fragments(audio=$Time$,format=mpd-time-csf)" initialization="QualityLevels($Bandwidth$)/Fragments(audio=i,format=mpd-time-csf)">
                <SegmentTimeline>
                    <S t="11446272" d="49152"/>
                </SegmentTimeline>
            </SegmentTemplate>
            <ProducerReferenceTime id="11446272" type="0" wallClockTime="2020-01-07T19:42:21.384Z" presentationTime="11446272"/>
            <Representation id="5_A_audio_17504386117102112482" bandwidth="128000" audioSamplingRate="44100"/>
        </AdaptationSet>
    </Period>
    <Period start="PT4M20.610S" id="main-content_1">
        <AdaptationSet id="1" group="1" profiles="ccff" bitstreamSwitching="false" segmentAlignment="true" contentType="video" mimeType="video/mp4" codecs="avc1.640020" maxWidth="1280" maxHeight="720" startWithSAP="1">
            <InbandEventStream schemeIdUri="urn:mpeg:dash:event:2012" value="1"/>
            <InbandEventStream schemeIdUri="urn:com:adobe:dpi:simple:2015" value="amssignal"/>
            <SegmentTemplate timescale="90000" presentationTimeOffset="23454931" media="QualityLevels($Bandwidth$)/Fragments(video=$Time$,format=mpd-time-csf)" initialization="QualityLevels($Bandwidth$)/Fragments(video=i,format=mpd-time-csf)">
                <SegmentTimeline>
                    <S t="23454932" d="58558"/>
                    <S d="4504"/>
                    <S d="130631"/>
                    <S d="135135" r="12"/>
                </SegmentTimeline>
            </SegmentTemplate>
            <ProducerReferenceTime id="23454932" type="0" wallClockTime="2020-01-07T19:42:22.442Z" presentationTime="23454932"/>
            <Representation id="1_V_video_5322324134428436312" bandwidth="3500000" width="1280" height="720"/>
            <Representation id="1_V_video_16981495139092747609" bandwidth="2200000" width="960" height="540"/>
            <Representation id="1_V_video_1384718563016940751" bandwidth="1350000" codecs="avc1.64001F" width="704" height="396"/>
            <Representation id="1_V_video_4425970933904124207" bandwidth="850000" codecs="avc1.64001E" width="512" height="288"/>
            <Representation id="1_V_video_11952982975776937431" bandwidth="550000" codecs="avc1.640016" width="384" height="216"/>
            <Representation id="1_V_video_10673801877453424365" bandwidth="200000" codecs="avc1.640015" width="340" height="192"/>
        </AdaptationSet>
        <AdaptationSet id="2" group="5" profiles="ccff" bitstreamSwitching="false" segmentAlignment="true" contentType="audio" mimeType="audio/mp4" codecs="mp4a.40.5" lang="en">
            <InbandEventStream schemeIdUri="urn:mpeg:dash:event:2012" value="1"/>
            <InbandEventStream schemeIdUri="urn:com:adobe:dpi:simple:2015" value="amssignal"/>
            <Label>audio</Label>
            <SegmentTemplate timescale="44100" presentationTimeOffset="11492916" media="QualityLevels($Bandwidth$)/Fragments(audio=$Time$,format=mpd-time-csf)" initialization="QualityLevels($Bandwidth$)/Fragments(audio=i,format=mpd-time-csf)">
                <SegmentTimeline>
                    <S t="11495424" d="28672"/>
                    <S d="1024"/>
                    <S d="131072"/>
                    <S d="132096"/>
                    <S d="133120"/>
                    <S d="132096" r="1"/>
                    <S d="133120"/>
                </SegmentTimeline>
            </SegmentTemplate>
            <ProducerReferenceTime id="11495424" type="0" wallClockTime="2020-01-07T19:42:22.499Z" presentationTime="11495424"/>
            <Representation id="5_A_audio_17504386117102112482" bandwidth="128000" audioSamplingRate="44100"/>
        </AdaptationSet>
    </Period>
</MPD>

~~~

--->

### <a name="334-mpeg-dash-in-band-event-message-box-signaling"></a>3.3.4 MPEG DASH 帯域内 Event Message Box のシグナル通知

帯域内イベント ストリームでは、MPD は適応セット レベルで InbandEventStream 要素を持っている必要があります。  この要素には必須の schemeIdUri 属性とオプションの timescale 属性があり、これらも Event Message Box ("emsg") に出現します。  MPD で定義されていないスキーム識別子を含む Event Message Box が存在する **べきではありません**。

インバンド [SCTE-35] キャリッジの場合、シグナルは schemeId = "urn:scte:scte35:2013:bin" を使用 **しなければなりません**。
[SCTE-35] インバンド メッセージのキャリッジの標準定義は、[SCTE-214-3] のセクション 7.3.2 (SCTE-35 キュー メッセージのキャリッジ) で定義されています。

以下の詳細では、クライアントが [SCTE-214-3] に準拠して "emsg" に期待する必要がある特定の値について概説しています。

| **フィールド名**          | **フィールドの型**          | **必須** | **説明**                                                                                                                                                                                                                                                                                        |
| ----------------------- | ----------------------- | ------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| scheme_id_uri           | string                  | 必須      | メッセージのスキームを示します。 スキームは、Live Server Manifest Box の Scheme 属性の値に設定されます。 値はメッセージのスキームを示す URN で **なければなりません**。 [SCTE-35] メッセージの場合、これは [SCTE-214-3] に準拠した "urn:scte:scte35:2013:bin" で **なければなりません**。          |
| 値                   | string                  | 必須      | メッセージのセマンティクスをカスタマイズするためにスキームの所有者によって使われる追加の文字列値。 スキームが同じ複数のイベント ストリームを区別するため、値はイベント ストリームの名前に設定されます (Smooth の取り込みの場合は trackName、RTMP の取り込みの場合は AMF メッセージ名)。 |
| タイムスケール               | 32 ビット符号なし整数 | 必須      | "emsg" ボックス内の時間フィールドと継続時間フィールドのタイムスケール (1 秒間のティック数)。                                                                                                                                                                                                            |
| Presentation_time_delta | 32 ビット符号なし整数 | 必須      | イベントのプレゼンテーション時間と、このセグメントでの最も早いプレゼンテーション時刻の間を表す、メディア プレゼンテーション時間の差分。 プレゼンテーション時間と継続時間は、[ISO-14496-12] Annex I で定義されているように、タイプ 1 または 2 のストリーム アクセス ポイント (SAP) と一致している **必要があります**。                                  |
| event_duration          | 32 ビット符号なし整数 | 必須      | イベントの継続時間、または継続時間が不明であることを示す 0xFFFFFFFF。                                                                                                                                                                                                                              |
| Id                      | 32 ビット符号なし整数 | 必須      | メッセージのこのインスタンスを示します。 同じセマンティクスを持つメッセージには、同じ値が設定されている必要があります。 メッセージが取り込まれときに ID が指定されていない場合は、Azure Media Services が一意 ID を生成します。                                                                                        |
| Message_data            | byte array              | 必須      | イベント メッセージ。 [SCTE-35] メッセージの場合、メッセージ データは [SCTE-214-3] に準拠したバイナリ splice_info_section() です。                                                                                                                                                                        |


#### <a name="example-inbandevenstream-entity-for-adobe-simple-mode"></a>Adobe Simple モードの InBandEvenStream エンティティの例
```xml

      <InbandEventStream schemeIdUri="urn:com:adobe:dpi:simple:2015" value="amssignal"/>
```

### <a name="335-dash-message-handling"></a>3.3.5 DASH メッセージの処理

イベントは、ビデオ トラックとオーディオ トラックの両方について、"emsg" ボックスの帯域内でシグナル通知されます。  シグナル通知は、presentation_time_delta が 15 秒以下であるすべてのセグメント要求に対して発生します。 

スライディング プレゼンテーション ウィンドウが有効になっている場合は、イベント メッセージのプレゼンテーション時間と継続時間の合計がマニフェスト内のメディア データの時間より小さいと、イベント メッセージが MPD から削除されます。  つまり、イベント メッセージで参照されているメディア時間がスライディング プレゼンテーション ウィンドウから出ると、そのイベント メッセージはマニフェストから削除されます。

## <a name="4-scte-35-ingest-implementation-guidance-for-encoder-vendors"></a>4.エンコーダー ベンダー向けの SCTE-35 取り込み実装ガイダンス

以下のガイドラインは、エンコーダー ベンダーによるこの仕様の実装に影響する可能性がある一般的な問題です。  以下のガイドラインは、他の主体によるこの仕様の実装を容易にするために、実際のパートナーのフィードバックに基づいて収集されたものです。 

[SCTE-35] メッセージは、[MS-SSTR-Ingest] の場合はスキーム **"urn:scte:scte35:2013:bin"** を使って、[RTMP] の取り込みの場合は **"scte35"** 型を使って、バイナリ形式で取り込まれます。 MPEG-2 トランスポート ストリーム プレゼンテーション タイムスタンプ (PTS) に基づく [SCTE-35] のタイミングの変換を容易にするため、PTS (pts_time + splice_time() の pts_adjustment) とメディア タイムラインの間のマッピングが、イベント プレゼンテーション時間 (Smooth の取り込みの場合は fragment_absolute_time フィールド、RTMP の取り込みの場合は time フィールド) によって提供されます。 33 ビットの PTS 値は約 26.5 時間ごとにロールオーバーされるため、マッピングが必要になります。

Smooth Streaming の取り込み [MS-SSTR-Ingest] では、[SCTE-35] で定義されている **splice_info_section()** が Media Data Box ("mdat") に含まれて **いなければなりません**。 

RTMP の取り込みの場合、AMF メッセージの cue 属性は、[SCTE-35] で定義されている、base64 でエンコードされた **splice_info_section()** に設定されます。  


メッセージが上で説明した形式の場合、メッセージは上で定義したように HLS、Smooth、DASH クライアントに送信されます。  

Azure Media Services プラットフォームを使用して実装をテストするときは、エンコード LiveEvent でのテストに進む前に、まずは "パススルー" LiveEvent を使用してテストを開始してください。

---

## <a name="change-history"></a>変更履歴

| Date     | [変更点]                                                                                                             |
| -------- | ------------------------------------------------------------------------------------------------------------------- |
| 2019/07/02  | RTMP 取り込みのサポートを改訂し、Elemental Live 用に RTMP の "onCuePoint" を追加しました                                            |
| 2019/08/22 | カスタム メタデータの RTMP に OnUserDataEvent を追加するよう更新されました                                                          |
| 2020/01/08  | RTMP Simple および RTMP SCTE35 モードでのエラーを修正しました。 "OnCuePoint" から "onAdCue" に変更しました。 Simple モードの表を更新しました。 |
| 2020/08/04  | 運用サービスの実装に一致するように、DATERANGE タグのサポートを削除しました。    |

## <a name="next-steps"></a>次のステップ
Media Services のラーニング パスを確認します。

[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>フィードバックの提供
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]
