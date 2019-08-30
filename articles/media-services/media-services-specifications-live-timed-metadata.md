---
title: Azure Media Services - ライブ ストリーミングでの時間指定メタデータのシグナル通知 | Microsoft Docs
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
ms.date: 07/2/2019
ms.author: johndeu
ms.openlocfilehash: 444d5ca996c014bdbf2e62cacf2563c7b63372e4
ms.sourcegitcommit: 5d6c8231eba03b78277328619b027d6852d57520
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/13/2019
ms.locfileid: "69015713"
---
# <a name="signaling-timed-metadata-in-live-streaming"></a>ライブ ストリーミングでの時間指定メタデータのシグナル通知 

最終更新:2019-07-02

### <a name="conformance-notation"></a>適合性の表記

このドキュメントで使用するキーワード "MUST (しなければなりません)"、"MUST NOT (してはなりません)"、"REQUIRED (必要です)"、"SHALL (することになります)"、"SHALL NOT (することはありません)"、"SHOULD (する必要があります)"、"SHOULD NOT (すべきではありません)"、"RECOMMENDED (お勧めします)"、"MAY (する場合があります)"、"OPTIONAL (オプション)" は、RFC 2119 で説明するとおりに解釈されます

## <a name="1-introduction"></a>1.はじめに 

広告の挿入またはクライアント プレーヤーでのカスタム メタデータ イベントの挿入をシグナル通知するために、ブロードキャスタはビデオ内に埋め込まれた時間指定メタデータを利用することがよくあります。 これらのシナリオを可能にするため、Media Services では、ライブ ストリーミング チャネルの取り込みポイントからクライアント アプリケーションまで、時間指定メタデータをトランスポートできます。
この仕様では、ライブ ストリーミング シグナル内の時間指定メタデータに対して Media Services がサポートするいくつかのモードについて説明します。

1. [SCTE-35]、[SCTE-214-1]、[SCTE-214-3] および [RFC8216] で概説されている標準に準拠した [SCTE-35] シグナル通知

2. RTMP 広告シグナル通知のためのレガシ [Adobe-Primetime] 仕様に準拠した [SCTE-35] シグナル通知。
   
3. [ID3v2] またはアプリケーション開発者によって定義されたその他のカスタム スキーマを送信することがある、[SCTE-35] **以外の**メッセージのための汎用時間指定メタデータ シグナル通知モード。

## <a name="11-terms-used"></a>1.1 用語

| 期間              | 定義 |
|-------------------|------------|
| 広告ブレーク          | 1 つ以上の広告の配信をスケジュールできる位置または時点。可用性および配置機会と同じ。 |
| 広告決定サービス| ユーザーに表示される広告と期間を決定する外部サービス。 サービスは通常、パートナーによって提供されるものであり、このドキュメントでは説明しません。|
| キュー               | まもなく発生する広告ブレークの時間とパラメーターの指示。 キューは、広告ブレークへの保留中の切り替え、広告ブレーク内の次の広告への保留中の切り替え、および、広告ブレークからメイン コンテンツへの保留中の切り替えを示すことがある点に注意してください。 |
| パッケージャー          | Azure Media Services の "ストリーミング エンドポイント" は、DASH および HLS 向けのダイナミック パッケージ機能を提供し、メディア業界では "パッケージャー" と呼ばれます。 
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

---

## <a name="12-normative-references"></a>1.2.1 標準リファレンス

以下の文書には、このテキストでの参照を通じて本書の規定を構成する規定が含まれています。 すべての文書は標準化団体によって改訂される可能性があります。読者の皆様には、以下に掲げる文書の最新版が適用される可能性について調査することをお勧めします。 また、参照されている文書の新しい版は、Azure Media Services の時間指定メタデータ仕様の現行バージョンとは互換性がない可能性があることも念頭に置いてください。


|Standard  |定義  |
|---------|---------|
|[Adobe-Primetime] | [Primetime Digital Program Insertion Signaling Specification (デジタル プログラム挿入シグナル通知仕様) 1.2](https://www.adobe.com/content/dam/acom/en/devnet/primetime/PrimetimeDigitalProgramInsertionSignalingSpecification.pdf) |
|[Adobe-Flash-AS] | [FLASH ActionScript 言語リファレンス](https://help.adobe.com/archive/en_US/as2/flashlite_2.x_3.x_aslr.pdf) |
| [AMF0]            | ["アクション メッセージ形式 AMF0"](https://download.macromedia.com/pub/labs/amf/amf0_spec_121207.pdf) |
| [DASH-IF-IOP]     | DASH Industry Forum Interop Guidance (DASH 業界フォーラム相互運用性ガイダンス) v 4.2 [https://dashif-documents.azurewebsites.net/DASH-IF-IOP/master/DASH-IF-IOP.html](https://dashif-documents.azurewebsites.net/DASH-IF-IOP/master/DASH-IF-IOP.html) |
| [HLS-TMD]         | HTTP ライブ ストリーミング用の時間指定メタデータ - [https://developer.apple.com/streaming](https://developer.apple.com/streaming) |
| [ID3v2]           | ID3 タグ バージョン 2.4.0  [http://id3.org/id3v2.4.0-structure](http://id3.org/id3v2.4.0-structure) |
| [ISO-14496-12]    | ISO/IEC 14496-12:パート 12 ISO ベースのメディア ファイル形式、第 4 版、2012 年 7 月 15 日  |
| [MPEGDASH]        | 情報技術 -- HTTP 経由のダイナミック アダプティブ ストリーミング (DASH) -- パート 1:メディア プレゼンテーションの説明とセグメント形式。 2014 年 5 月。 公開済み。 URL: https://www.iso.org/standard/65274.html |
| [MPEGCMAF]        | 情報技術 -- マルチメディア アプリケーション形式 (MPEG-A) -- パート 19:セグメント化メディア用の共通メディア アプリケーション形式 (CMAF)。 2018 年 1 月。 公開済み。 URL: https://www.iso.org/standard/71975.html |
| [MPEGCENC]        | 情報技術 -- MPEG システム技術 -- パート 7:ISO ベース メディア ファイル形式ファイルでの一般的な暗号化。 2016 年 2 月。 公開済み。 URL: https://www.iso.org/standard/68042.html |
| [MS-SSTR]         | [「Microsoft Smooth Streaming プロトコル」、2014 年 5 月 15 日](https://docs.microsoft.com/openspecs/windows_protocols/ms-sstr/8383f27f-7efe-4c60-832a-387274457251) |
| [MS-SSTR-Ingest]  | [Azure Media Services の Fragmented MP4 ライブ インジェスト仕様](https://docs.microsoft.com/azure/media-services/media-services-fmp4-live-ingest-overview) |
| [RFC8216]         | R. Pantos, Ed.; W. May. HTTP ライブ ストリーミング。 2017 年 8 月。 情報提供。 [https://tools.ietf.org/html/rfc8216](https://tools.ietf.org/html/rfc8216) |
| [RFC4648]         |Base16、Base32、および Base64 データ エンコード - [https://tools.ietf.org/html/rfc4648](https://tools.ietf.org/html/rfc4648) |
| [RTMP]            |[「Adobe のリアルタイム メッセージング プロトコル」、2012 年 12 月 21 日](https://www.adobe.com/devnet/rtmp.html)  |
| [SCTE-35-2019]    | SCTE 35:2019 - ケーブル接続のデジタル プログラム挿入キューイング メッセージ - https://www.scte.org/SCTEDocs/Standards/ANSI_SCTE%2035%202019r1.pdf  |
| [SCTE-214-1]      | SCTE 214-1 2016 – IP ベースのケーブル サービス向け MPEG DASH パート 1:MPD の制約と拡張 |
| [SCTE-214-3]      | SCTE 214-3 2015 IP ベースのケーブル サービス向け MPEG DASH パート 3:DASH/FF プロファイル |
| [SCTE-224]        | SCTE 224 2018r1 – イベントのスケジューリングと通知のインターフェイス |
| [SCTE-250]        | イベントおよびシグナル通知管理 API (ESAM) |

---


## <a name="2-timed-metadata-ingest"></a>2.時間指定メタデータの取り込み

## <a name="21-rtmp-ingest"></a>2.1 RTMP 取り込み

[RTMP] では、[RTMP] ストリーム内に埋め込まれた [AMF0] キュー メッセージとして時間指定メタデータ シグナルを送信できます。 キュー メッセージは、実際のイベントまたは [SCTE-35] 広告スプライス シグナルが発生する必要がある時点よりも前に送信できます。 このシナリオをサポートするために、イベントの実際の時間がキュー メッセージ内で送信されます。 詳しくは、[AMF0] をご覧ください。

次の表では、"シンプル" および [SCTE-35] メッセージ モードの両方で Media Services が取り込む AMF メッセージのペイロードの形式について説明します。

[AMF0] メッセージの名前を使って、同じ種類の複数のイベント ストリームを区別できます。  [SCTE-35] メッセージと "シンプル" モードの両方で、AMF メッセージの名前は [Adobe-Primetime] 仕様の要求どおり "onAdCue" でなければなりません。  下の一覧にないフィールドは、取り込み時に Azure Media Services によって無視されることになります。

## <a name="211-rtmp-signal-syntax"></a>2.1.1 RTMP シグナルの構文

Azure Media Services は、いくつかの [AMF0] メッセージ型をリッスンし、それらに応答することができます。これらのメッセージ型は、リアルタイムで同期される各種メタデータをライブ ストリーム内でシグナル通知するために使用できます。  [Adobe-Primetime] 仕様では、"シンプル" および "SCTE-35" モードと呼ばれる 2 つのキュー タイプを定義しています。 "シンプル" モードの場合、Media Services は、"Simple Mode" シグナル用に定義された下記の表に一致するペイロードを使用して、"onAdCue" という単一の AMF キュー メッセージをサポートします。  

次のセクションでは、RTMP "シンプル" モード ペイロードを示します。これは、HLS、DASH、Microsoft Smooth Streaming のクライアント マニフェストに送られる基本的な "spliceOut" 広告シグナルを通知するために使用できます。 これは、複雑な SCTE-35 ベースの広告シグナル通知のデプロイまたは挿入システムを顧客が有しておらず、基本的なオンプレミス エンコーダーを使用して API 経由でキュー メッセージを送信しているシナリオで非常に役立ちます。 通常、オンプレミス エンコーダーは、このシグナルをトリガーするために REST ベースの API をサポートします。このトリガーには、ビデオに IDR フレームを挿入して新しい GOP を開始することによって、ビデオ ストリームを "スプライス条件" 化する作用もあります。

## <a name="212--simple-mode-ad-signaling-with-rtmp"></a>2.1.2 RTMP を使用したシンプル モードの広告シグナル通知

| フィールド名 | フィールドの型 | 必須 | 説明                                                                                                             |
|------------|------------|----------|--------------------------------------------------------------------------------------------------------------------------|
| cue        | string     | 必須 | イベント メッセージ。  シンプル モードのスプライスを指定する "SpliceOut" である必要があります。                                              |
| id         | string     | 必須 | スプライスまたはセグメントを記述する一意識別子。 メッセージのこのインスタンスを示します。                            |
| duration   | Number     | 必須 | スプライスの継続時間。 単位は秒の小数部です。                                                                |
| elapsed    | Number     | 省略可能 | チューニングをサポートするためにシグナルを繰り返す場合は、スプライスが開始してから経過したプレゼンテーション時間の長さをこのフィールドに設定する必要があります。 単位は秒の小数部です。 シンプル モードを使う場合、この値はスプライスの元の継続時間を超えてはなりません。                                                  |
| time       | Number     | 必須 | プレゼンテーション時間で表されたスプライスの時間。 単位は秒の小数部です。                                     |

---
 
## <a name="213-scte-35-mode-ad-signaling-with-rtmp"></a>2.1.3 RTMP を使用した SCTE-35 モードの広告シグナル通知

完全な SCTE-35 ペイロード メッセージを HLS または DASH マニフェストに送信する必要がある、より高度な放送制作ワークフローで作業している場合、[Adobe-Primetime] 仕様の "SCTE-35 モード" を使用するのが最適です。  このモードは、オンプレミス ライブ エンコーダーに直接送信されるインバンド SCTE-35 シグナルをサポートします。その後、このエンコーダーが、[Adobe-Primetime] 仕様で指定された "SCTE-35 モード" を使用してシグナルを RTMP ストリームにエンコードします。 

通常、SCTE-35 メッセージが現れる可能性があるのは、オンプレミス エンコーダー上の MPEG-2 トランスポート ストリーム (TS) 入力だけです。 SCTE-35 を含むトランスポート ストリーム取り込みを構成し、Adobe SCTE-35 モードでの RTMP へのパススルーのためにそれを有効化する方法の詳細については、エンコーダーの製造元にお問い合わせください。

このシナリオでは、 **"onAdCue"** [AMF0] メッセージ型を使用してオンプレミス エンコーダーから次のペイロードを送信しなければなりません。

| フィールド名 | フィールドの型 | 必須 | 説明                                                                                                             |
|------------|------------|----------|--------------------------------------------------------------------------------------------------------------------------|
| cue        | string     | 必須 | イベント メッセージ。  [SCTE-35] メッセージの場合、HLS、Smooth、Dash クライアントにメッセージが送信されるためには、このフィールドは base64 でエンコードされた [RFC4648] バイナリの splice_info_section() でなければなりません。                                              |
| type       | string     | 必須 | メッセージのスキームを示す URN または URL。 [SCTE-35] メッセージの場合、[Adobe-Primetime] に準拠する HLS、Smooth、Dash クライアントにメッセージが送信されるためには、これが **"scte35"** である**必要があります**。 オプションで、[SCTE-35] メッセージをシグナル通知するために URN "urn:scte:scte35:2013:bin" を使用することもできます。 |
| id         | string     | 必須 | スプライスまたはセグメントを記述する一意識別子。 メッセージのこのインスタンスを示します。  同じセマンティクスを持つメッセージには、同じ値が設定されている必要があります。|
| duration   | Number     | 必須 | イベントまたは広告スプライス セグメントの継続時間 (わかっている場合)。 わからない場合は、0 にする**必要があります**。                                                                 |
| elapsed    | Number     | 省略可能 | チューニングのために [SCTE-35] 広告シグナルを繰り返す場合は、スプライスが開始してから経過したプレゼンテーション時間の長さをこのフィールドに設定する必要があります。 単位は秒の小数部です。 [SCTE-35] モードでは、この値はスプライスまたはセグメントで指定されている元の継続時間を超えてもかまいません。                                                  |
| time       | Number     | 必須 | イベントまたは広告スプライスのプレゼンテーション時間。  プレゼンテーション時間と継続時間は、[ISO-14496-12] Annex I で定義されているように、タイプ 1 または 2 のストリーム アクセス ポイント (SAP) と一致している**必要があります**。HLS エグレスの場合は、プレゼンテーション時間と継続時間はセグメントの境界と一致する**必要があります**。 同じイベント ストリームに含まれる異なるイベント メッセージのプレゼンテーション時間と継続時間は、オーバーラップしてはなりません。 単位は秒の小数部です。

---
## <a name="214-elemental-live-oncuepoint-ad-markers-with-rtmp"></a>2.1.4 RTMP を使用した Elemental Live "onCuePoint" 広告マーカー

Elemental Live オンプレミス エンコーダーは、RTMP シグナル内の広告マーカーをサポートします。 Azure Media Services は現在、RTMP に対して "onCuePoint" 広告マーカー タイプのみをサポートしています。  これは、Elemental Media Live エンコーダー設定または API の Adobe RTMP グループ設定で "**ad_markers**" を "onCuePoint" に設定することによって有効化できます。  詳細については、Elemental Live のドキュメントをご覧ください。 RTMP グループでこの機能を有効化すると、SCTE-35 シグナルが Adobe RTMP 出力に渡され、Azure Media Services によって処理されます。

"onCuePoint" メッセージ型は [Adobe-Flash-AS] で定義され、Elemental Live RTMP 出力からの送信時は次のペイロード構造を持ちます。


| プロパティ  |説明  |
|---------|---------|
|  名前     | 名前は Elemental Live による "**scte35**" である必要があります。 |
|time     |  タイムライン中にビデオ ファイルでキュー ポイントが発生した時間 (秒) |
| type     | キュー ポイントの種類は "**event**" に設定する必要があります。 |
| parameters | SCTE-35 メッセージからの (ID や期間などの) 情報を含む名前/値ペア文字列の連想配列。 これらの値は Azure Media Services によって解析され、マニフェスト装飾タグに含められます。  |


広告マーカーのこのモードが使用されるとき、HLS マニフェストの出力は Adobe "Simple" モードに似ています。 

### <a name="215-cancellation-and-updates"></a>2.1.5 キャンセルと更新

同じプレゼンテーション時間と ID で複数のメッセージを送信することにより、メッセージをキャンセルまたは更新することができます。 プレゼンテーション時間と ID はイベントを一意に識別し、プレロール制約を満たす特定のプレゼンテーション時間で受信された最後のメッセージが、処理されるメッセージになります。 更新されたイベントは、以前に受信されたメッセージを置き換えます。 プレロール制約は 4 秒です。 プレゼンテーション時間の少なくとも 4 秒前に受信されたメッセージが処理されます。

## <a name="22-fragmented-mp4-ingest-smooth-streaming"></a>2.2 フラグメント化された MP4 の取り込み (Smooth Streaming)

ライブ ストリームの取り込みの要件については、[MS-SSTR-Ingest] をご覧ください。 以降のセクションでは、時間指定プレゼンテーション メタデータの取り込みに関する詳細情報を提供します。  時間指定プレゼンテーション メタデータは、スパース トラックとして取り込まれます。これは、Live Server Manifest Box (MS-SSTR を参照) と Movie Box ("moov") の両方で定義されています。  

各スパース フラグメントは、Movie Fragment Box ("moof") と Media Data Box ("mdat") で構成されており、"mdat" ボックスはバイナリ メッセージです。

フレーム単位で正確な広告の挿入を実現するために、エンコーダーは、キューを挿入する必要があるプレゼンテーション時間のところでフラグメントを分割しなければなりません。  [ISO-14496-12] 付録 I で定義されているように、新しく作成された IDR フレームで、あるいはタイプ 1 または 2 の Stream Access Point (SAP) で始まる新しいフラグメントを作成しなければなりません。これにより、Azure Media Packager は、HLS マニフェストおよび DASH 複数期間マニフェストを適切に生成できるようになります。後者の新しい期間は、フレーム精度のスプライス条件付きプレゼンテーション時間で始まります。

### <a name="221-live-server-manifest-box"></a>2.2.1 Live Server Manifest Box

スパース トラックは、 **\<textstream\>** エントリを含む Live Server Manifest Box で宣言されて**いなければならず**、以下の属性が設定されて**いなければなりません**。

| **属性名** | **フィールドの型** | **必須** | **説明**                                                                                                                                                                                                                                                 |
|--------------------|----------------|---------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| systemBitrate      | Number         | 必須      | "0" で**なければなりません**。不明な可変ビットレートのトラックを示します。                                                                                                                                                                                                 |
| parentTrackName    | string         | 必須      | 親トラックの名前で**なければなりません**。スパース トラックの時間コードは親トラックのタイムスケールに揃えられます。 スパース トラックを親トラックにすることはできません。                                                                                                                    |
| manifestOutput     | Boolean        | 必須      | "true" で**なければなりません**。スパース トラックが Smooth クライアント マニフェストに埋め込まれることを示します。                                                                                                                                                               |
| Subtype            | string         | 必須      | 4 文字のコード "DATA" で**なければなりません**。                                                                                                                                                                                                                        |
| Scheme             | string         | 必須      | メッセージのスキームを示す URN または URL で**なければなりません**。 [SCTE-35] メッセージの場合、[SCTE-35] に準拠する HLS、Smooth、Dash クライアントにメッセージが送信されるためには、これが "urn:scte:scte35:2013:bin" で**なければなりません**。 |
| trackName          | string         | 必須      | スパース トラックの名前で**なければなりません**。trackName を使って、同じスキームの複数のイベント ストリームを区別できます。 一意のイベント ストリームはそれぞれ、一意のトラック名を持って**いなければなりません**。                                                                           |
| timescale          | Number         | 省略可能      | 親のトラックのタイムスケールで**なければなりません**。                                                                                                                                                                                                                      |

---

### <a name="222-movie-box"></a>2.2.2 Movie Box

Movie Box ("moov") は、スパース トラックのストリーム ヘッダーの一部として、Live Server Manifest Box の後に続きます。

[ISO-14496-12] で定義されているように、"moov" ボックスは **TrackHeaderBox ("tkhd")** を含む**必要があり**、次の制約があります。

| **フィールド名** | **フィールドの型**          | **必須** | **説明**                                                                                                |
|----------------|-------------------------|---------------|----------------------------------------------------------------------------------------------------------------|
| duration       | 64 ビット符号なし整数 | 必須      | 0 である**必要があります**。これは、トラック ボックスは 0 サンプルであり、トラック ボックスのサンプルの合計継続時間は 0 であるためです。 |

---

[ISO-14496-12] で定義されているように、"moov" ボックスは **HandlerBox ("hdlr")** を含む**必要があり**、次の制約があります。

| **フィールド名** | **フィールドの型**          | **必須** | **説明**   |
|----------------|-------------------------|---------------|-------------------|
| handler_type   | 32 ビット符号なし整数 | 必須      | "meta" である**必要があります**。 |

---

"stsd" ボックスは、[ISO-14496-12] で定義されているコーディング名前を持つ MetaDataSampleEntry ボックスを含む**必要があります**。  たとえば、SCTE-35 メッセージの場合は、コーディング名は "scte" である**必要があります**。

### <a name="223-movie-fragment-box-and-media-data-box"></a>2.2.3 Movie Fragment Box と Media Data Box

スパース トラックのフラグメントは、Movie Fragment Box ("moof") と Media Data Box ("mdat") で構成されます。

> [!NOTE]
> フレーム単位で正確な広告の挿入を実現するために、エンコーダーは、キューを挿入する必要があるプレゼンテーション時間のところでフラグメントを分割しなければなりません。  [ISO-14496-12] 付録 I で定義されているように、新しく作成された IDR フレームで、あるいはタイプ 1 または 2 の Stream Access Point (SAP) で始まる新しいフラグメントを作成しなければなりません。
> 

MovieFragmentBox ("moof") ボックスは、[MS-SSTR] で定義された以下のフィールドを含む **TrackFragmentExtendedHeaderBox ("uuid")** ボックスを含んで**いなければなりません**。

| **フィールド名**         | **フィールドの型**          | **必須** | **説明**                                                                               |
|------------------------|-------------------------|---------------|-----------------------------------------------------------------------------------------------|
| fragment_absolute_time | 64 ビット符号なし整数 | 必須      | イベントの到着時間で**なければなりません**。 この値により、メッセージと親トラックの位置が一致します。   |
| fragment_duration      | 64 ビット符号なし整数 | 必須      | イベントの継続時間で**なければなりません**。 継続時間は、値が不明であることを示す 0 にすることができます。 |

---


MediaDataBox ("mdat") ボックスは次のような形式で**なければなりません**。

| **フィールド名**          | **フィールドの型**                   | **必須** | **説明**                                                                                                                                                                                                                                                                                                                                                                                                                                                                                 |
|-------------------------|----------------------------------|---------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| version                 | 32 ビット符号なし整数 (uimsbf) | 必須      | "mdat" ボックスの内容の形式を決定します。 認識されないバージョンは無視されます。 現在サポートされている値は 1 だけです。                                                                                                                                                                                                                                                                                                                                                      |
| id                      | 32 ビット符号なし整数 (uimsbf) | 必須      | メッセージのこのインスタンスを示します。 同じセマンティクスのメッセージは、同じ値を持つことになります。つまり、同じ ID を持ついずれか 1 つのイベント メッセージ ボックスを処理すれば十分です。                                                                                                                                                                                                                                                                                                            |
| presentation_time_delta | 32 ビット符号なし整数 (uimsbf) | 必須      | TrackFragmentExtendedHeaderBox で指定されている fragment_absolute_time と presentation_time_delta の合計は、イベントのプレゼンテーション時間で**なければなりません**。 プレゼンテーション時間と継続時間は、[ISO-14496-12] Annex I で定義されているように、タイプ 1 または 2 のストリーム アクセス ポイント (SAP) と一致している**必要があります**。HLS エグレスの場合は、プレゼンテーション時間と継続時間はセグメントの境界と一致する**必要があります**。 同じイベント ストリームに含まれる異なるイベント メッセージのプレゼンテーション時間と継続時間は、オーバーラップ**してはなりません**。 |
| message                 | byte array                       | 必須      | イベント メッセージ。 [SCTE-35] メッセージの場合、メッセージはバイナリの splice_info_section() です。 [SCTE-35] メッセージの場合、[SCTE-35] に準拠する HLS、Smooth、Dash クライアントにメッセージが送信されるためには、このフィールドは splice_info_section() で**なければなりません**。 [SCTE-35] メッセージの場合、バイナリの splice_info_section() が "mdat" ボックスのペイロードであり、base64 でエンコードされては**いません**。                                                            |

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
StreamIndex には "DATA" の Subtype が**なければならず**、CustomAttributes には Name="Schema" かつ Value="urn:scte:scte35:2013:bin" の Attribute が含まれ**なければなりません**。

#### <a name="smooth-client-manifest-example-showing-base64-encoded-scte35-splice_info_section"></a>base64 でエンコードされた [SCTE-35] splice_info_section() を示す Smooth クライアントのマニフェストの例
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
  <StreamIndex Type=”text” Name=”scte35-sparse-stream” Subtype=”DATA” Chunks=”0” TimeScale=”10000000”
    ParentStreamIndex=”video” ManifestOutput=”true” 
    Url=”QualityLevels({bitrate})/Fragments(captions={start time})”>
    <QualityLevel Index=”0” Bitrate=”0” CodecPrivateData=”” FourCC=””>
      <CustomAttributes>
        <Attribute Name=”Scheme” Value=”urn:scte:scte35:2013:bin”/>
      </CustomAttributes>
    </QualityLevel>
    <!-- The following <c> and <f> fragments contains the base64-encoded [SCTE35] splice_info_section() message -->
    <c t=”600000000” d=”300000000”>    <f>PD94bWwgdmVyc2lvbj0iMS4wIiBlbmNvZGluZz0idXRmLTgiPz48QWNxdWlyZWRTaWduYWwgeG1sbnM9InVybjpjYWJsZWxhYnM6bWQ6eHNkOnNpZ25hbGluZzozLjAiIGFjcXVpc2l0aW9uUG9pbnRJZGVudGl0eT0iRVNQTl9FYXN0X0FjcXVpc2l0aW9uX1BvaW50XzEiIGFjcXVpc2l0aW9uU2lnbmFsSUQ9IjRBNkE5NEVFLTYyRkExMUUxQjFDQTg4MkY0ODI0MDE5QiIgYWNxdWlzaXRpb25UaW1lPSIyMDEyLTA5LTE4VDEwOjE0OjI2WiI+PFVUQ1BvaW50IHV0Y1BvaW50PSIyMDEyLTA5LTE4VDEwOjE0OjM0WiIvPjxTQ1RFMzVQb2ludERlc2NyaXB0b3Igc3BsaWNlQ29tbWFuZFR5cGU9IjUiPjxTcGxpY2VJbnNlcnQgc3BsaWNlRXZlbnRJRD0iMzQ0NTY4NjkxIiBvdXRPZk5ldHdvcmtJbmRpY2F0b3I9InRydWUiIHVuaXF1ZVByb2dyYW1JRD0iNTUzNTUiIGR1cmF0aW9uPSJQVDFNMFMiIGF2YWlsTnVtPSIxIiBhdmFpbHNFeHBlY3RlZD0iMTAiLz48L1NDVEUzNVBvaW50RGVzY3JpcHRvcj48U3RyZWFtVGltZXM+PFN0cmVhbVRpbWUgdGltZVR5cGU9IkhTUyIgdGltZVZhbHVlPSI1MTUwMDAwMDAwMDAiLz48L1N0cmVhbVRpbWVzPjwvQWNxdWlyZWRTaWduYWw+</f>
    </c>
    <c t=”1200000000” d=”400000000”>      <f>PD94bWwgdmVyc2lvbj0iMS4wIiBlbmNvZGluZz0idXRmLTgiPz48QWNxdWlyZWRTaWduYWwgeG1sbnM9InVybjpjYWJsZWxhYnM6bWQ6eHNkOnNpZ25hbGluZzozLjAiIGFjcXVpc2l0aW9uUG9pbnRJZGVudGl0eT0iRVNQTl9FYXN0X0FjcXVpc2l0aW9uX1BvaW50XzEiIGFjcXVpc2l0aW9uU2lnbmFsSUQ9IjRBNkE5NEVFLTYyRkExMUUxQjFDQTg4MkY0ODI0MDE5QiIgYWNxdWlzaXRpb25UaW1lPSIyMDEyLTA5LTE4VDEwOjE0OjI2WiI+PFVUQ1BvaW50IHV0Y1BvaW50PSIyMDEyLTA5LTE4VDEwOjE0OjM0WiIvPjxTQ1RFMzVQb2ludERlc2NyaXB0b3Igc3BsaWNlQ29tbWFuZFR5cGU9IjUiPjxTcGxpY2VJbnNlcnQgc3BsaWNlRXZlbnRJRD0iMzQ0NTY4NjkxIiBvdXRPZk5ldHdvcmtJbmRpY2F0b3I9InRydWUiIHVuaXF1ZVByb2dyYW1JRD0iNTUzNTUiIGR1cmF0aW9uPSJQVDFNMFMiIGF2YWlsTnVtPSIxIiBhdmFpbHNFeHBlY3RlZD0iMTAiLz48L1NDVEUzNVBvaW50RGVzY3JpcHRvcj48U3RyZWFtVGltZXM+PFN0cmVhbVRpbWUgdGltZVR5cGU9IkhTUyIgdGltZVZhbHVlPSI1MTYyMDAwMDAwMDAiLz48L1N0cmVhbVRpbWVzPjwvQWNxdWlyZWRTaWduYWw+</f>
    </c>
  </StreamIndex>
</SmoothStreamingMedia>
~~~

## <a name="32-apple-hls-manifest-decoration"></a>3.2 Apple HLS マニフェストの装飾

Azure Media Services は、ライブまたはオンデマンド イベント中に広告可用性情報をシグナル通知するための、以下の HLS マニフェスト タグをサポートします。 

- Apple HLS で定義されている EXT-X-DATERANGE [RFC8216]
- [Adobe-Primetime] で定義されている EXT-X-CUE - このモードは "レガシ" と見なされます。 顧客は、可能であれば EXT-X-DATERANGE タグを採用する必要があります。

各タグへのデータ出力は、使用される取り込みシグナル モードによって異なります。 たとえば、Adobe Simple モードでの RTMP 取り込みには、完全な SCTE-35 base64 エンコード済みペイロードが含まれていません。

## <a name="321-apple-hls-with-adobe-primetime-ext-x-daterange-recommended"></a>3.2.1 Adobe Primetime EXT-X-DATERANGE を使用した Apple HLS (推奨)

Apple HTTP ライブ ストリーミング [RFC8216] 仕様では、[SCTE-35] メッセージのシグナル通知を許可しています。 メッセージは、[RFC8216] の "Mapping SCTE-35 into EXT-X-DATERANGE" というタイトルのセクションに従って、EXT-X-DATERANGE タグでセグメント プレイリストに挿入されます。  クライアント アプリケーション層は、M3U プレイリストを解析して M3U タグを処理する、または Apple プレーヤー フレームワークを介してイベントを受信することができます。  

Azure Media Services (バージョン 3 API) で**お勧めする**アプローチは、[RFC8216] に従い、マニフェストでの [SCTE-35] 広告可用性装飾には EXT-X_DATERANGE タグを使用するというものです。

## <a name="322-apple-hls-with-adobe-primetime-ext-x-cue-legacy"></a>3.2.2 Adobe Primetime EXT-X-CUE を使用した Apple HLS (レガシ)

Azure Media Services (バージョン 2 および 3 API) では、[Adobe-Primetime] "SCTE-35 Mode" で定義されているように EXT-X-CUE タグを使用する "レガシ" 実装も提供されます。 このモードでは、Azure Media Services は base64 でエンコードされた [SCTE-35] splice_info_section() を EXT-X-CUE タグに埋め込みます。  

"レガシ" EXT-X-CUE タグは次のように定義されており、[Adobe-Primetime] 仕様でも標準リファレンスを確認できます。 これは、必要な場合にレガシ SCTE-35 シグナル通知にのみ使用する必要があります。そうでない場合の推奨タグは [RFC8216] で EXT-X-DATERANGE と定義されています。 

| **属性名** | **Type**                      | **必須**                             | **説明**                                                                                                                                                                                                                                                                      |
|--------------------|-------------------------------|-------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| CUE                | 引用符で囲まれた文字列                 | 必須                                  | [RFC4648] で説明されているように、base64 エンコード文字列としてエンコードされたメッセージ。 [SCTE-35] メッセージの場合、これは base64 でエンコードされた splice_info_section() です。                                                                                                |
| TYPE               | 引用符で囲まれた文字列                 | 必須                                  | メッセージのスキームを示す URN または URL。 [SCTE-35] メッセージの場合、タイプは特別な値 "scte35" です。                                                                                                                                |
| id                 | 引用符で囲まれた文字列                 | 必須                                  | イベントの一意識別子。 メッセージが取り込まれときに ID が指定されていない場合は、Azure Media Services が一意 ID を生成します。                                                                                                                                          |
| DURATION           | 10 進浮動小数点数 | 必須                                  | イベントの継続時間。 わからない場合は、0 にする**必要があります**。 単位は秒の小数部です。                                                                                                                                                                                           |
| ELAPSED            | 10 進浮動小数点数 | オプション、ただしスライディング ウィンドウの場合は必須。 | スライディング プレゼンテーション ウィンドウをサポートするためにシグナルを繰り返す場合、このフィールドは、イベントが開始してから経過したプレゼンテーション時間の長さで**なければなりません**。 単位は秒の小数部です。 この値はスプライスまたはセグメントで指定されている元の継続時間を超えてもかまいません。 |
| TIME               | 10 進浮動小数点数 | 必須                                  | イベントのプレゼンテーション時間。 単位は秒の小数部です。                                                                                                                                                                                                                    |


HLS プレーヤーのアプリケーション レイヤーは、TYPE を使って、メッセージの形式を識別し、メッセージをデコードし、必要な時間変換を適用して、イベントを処理します。  イベントは、イベントのタイムスタンプに従って、親トラックのセグメント プレイリストの時間と同期されます。  イベントは、最も近いセグメント (#EXTINF タグ) の前に挿入されます。

### <a name="323-hls-segment-playlist-example-using-legacy-adobe-primetime-ext-x-cue"></a>3.2.3 "レガシ" Adobe Primetime EXT-X-CUE を使用した HLS セグメント プレイリストの例

次の例は、Adobe Primetime EXT-X-CUE タグを使用した HLS マニフェスト装飾を示しています。  "CUE" パラメーターには、完全な base64 エンコード済み SCTE-35 splice_info ペイロードが含まれます。これは、このシグナルが Adobe SCTE-35 シグナル モードでは RTMP を使用して、SCTE-35 シグナル モードでは Smooth Streaming を介して受信されることを示します。 

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

### <a name="324-hls-message-handling-for-legacy-adobe-primetime-ext-x-cue"></a>3.2.4 "レガシ" Adobe Primetime EXT-X-CUE 向けの HLS メッセージ処理

イベントは、各ビデオ トラックおよびオーディオ トラックのセグメント プレイリスト内でシグナル通知されます。[Adobe-Primetime] で要求されているように、EXT-X-CUE タグの位置は、常に、TIME および DURATION 属性によって参照される最初の HLS セグメントの直前 (スプライス アウトまたはセグメント開始の場合)、または最後の HLS セグメントの直後 (スプライス インまたはセグメント終了の場合) で**なければなりません**。

スライディング プレゼンテーション ウィンドウが有効になっているときは、[Adobe-Primetime] で要求されているように、EXT-X-CUE タグはスプライスまたはセグメントがセグメント プレイリストで常に完全に記述されるのに十分な回数だけ繰り返され**なければならず**、ELAPSED 属性を使ってスプライスまたはセグメントがアクティブになっていた時間が示され**なければなりません**。

スライディング プレゼンテーション ウィンドウが有効になっている場合、EXT-X-CUE タグで参照されているメディア時間がスライディング プレゼンテーション ウィンドウから出たときは、EXT-X-CUE タグがセグメント プレイリストから削除されます。

## <a name="33-dash-manifest-decoration-mpd"></a>3.3 DASH マニフェスト装飾 (MPD)

[MPEGDASH] では、イベントをシグナル通知する 3 つの方法が用意されています。

1.  MPD EventStream でシグナル通知されるイベント
2.  Event Message Box ("emsg") を使用してインバンドでシグナル通知されるイベント
3.  1 と 2 の組み合わせ

MPD EventStream でシグナル通知されるイベントは、MPD がダウンロードされるとすぐにクライアントはすべてのイベントにアクセスできるため、VOD ストリーミングに便利です。 下流の SSAI ベンダーが複数期間 MPD マニフェストからのシグナルを解析して広告コンテンツを動的に挿入する必要がある SSAI シグナル通知にも役立ちます。  インバンド ("emsg") ソリューションは、クライアントが MPD を再ダウンロードする必要がない、またはクライアントとオリジン間で SSAI マニフェスト操作が発生しないライブ ストリーミングに役立ちます。 

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

| **属性名** | **Type**                | **必須** | **説明**                                                                                                                                                                                                                                                                                   |
|--------------------|-------------------------|---------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| scheme_id_uri      | string                  | 必須      | メッセージのスキームを示します。 スキームは、Live Server Manifest Box の Scheme 属性の値に設定されます。 値は、メッセージ スキームを識別する URN または URL である**必要があります**。サポートされている出力 schemeId は、[SCTE-214-1] のセクション 6.7.4 (MPD) に従って "urn:scte:scte35:2014:xml+bin" である必要があります。これは、サービスは現時点で、簡潔さのために MPD では "xml+bin" のみをサポートしているためです。  |
| value              | string                  | 省略可能      | メッセージのセマンティクスをカスタマイズするためにスキームの所有者によって使われる追加の文字列値。 スキームが同じ複数のイベント ストリームを区別するため、値はイベント ストリームの名前に設定**しなければなりません** ([MS-SSTR-Ingest] の場合は trackName、[RTMP] の取り込みの場合は AMF メッセージ名)。 |
| タイムスケール          | 32 ビット符号なし整数 | 必須      | タイムスケール (単位は 1 秒あたりのティック数)。                                                                                                                                                                                                       |


### <a name="332-example-mpeg-dash-manifest-mpd-signaling-of-scte-35-using-eventstream"></a>3.3.2 EventStream を使用した SCTE-35 の MPEG DASH マニフェスト (MPD) シグナル通知の例

~~~ xml
<!-- Example MPD using xml+bin style signaling per [SCTE-214-1] -->
  <EventStream schemeIdUri="urn:scte:scte35:2014:xml+bin" value="scte35_track_001_000" timescale="10000000">
        <Event presentationTime="15447165200227600" duration="300000000" id="1026">
            <scte35:Signal>
                <scte35:Binary>
                    /DAlAAAAAAAAAP/wFAUAAAQDf+//KaeGwP4AKTLgAAAAAAAAn75a3g==
                </scte35:Binary>
            </scte35:Signal>
        </Event>
        <Event presentationTime="15447166250227600" duration="300000000" id="1027">
           <scte35:Signal>
                <scte35:Binary>
                    /DAlAAAAAAAAAP/wFAUAAAQDf+//KaeGwP4AKTLgAAAAAAAAn75a3g==
                </scte35:Binary>
            </scte35:Signal>
        </Event>
    </EventStream>
~~~

> [!IMPORTANT]
> presentationTime は、[SCTE-35] イベントのプレゼンテーション時間を、メッセージの到着時間ではなく期間の開始時間が基準となるように変換したものであることに注意してください。
> [MPEGDASH] では Event@presentationTime を "Period の開始を基準とするイベントのプレゼンテーション時間を指定する" と定義しています。
> 秒単位のプレゼンテーション時間の値は、この属性の値と EventStream@timescale 属性の値の除算です。
> 存在しない場合、プレゼンテーション時間の値は 0 です。


### <a name="333-mpeg-dash-in-band-event-message-box-signaling"></a>3.3.3 MPEG DASH 帯域内 Event Message Box のシグナル通知

帯域内イベント ストリームでは、MPD は適応セット レベルで InbandEventStream 要素を持っている必要があります。  この要素には必須の schemeIdUri 属性とオプションの timescale 属性があり、これらも Event Message Box ("emsg") に出現します。  MPD で定義されていないスキーム識別子を含む Event Message Box が存在する**べきではありません**。

インバンド [SCTE-35] キャリッジの場合、シグナルは schemeId = "urn:scte:scte35:2013:bin" を使用**しなければなりません**。
[SCTE-35] インバンド メッセージのキャリッジの標準定義は、[SCTE-214-3] のセクション 7.3.2 (SCTE-35 キュー メッセージのキャリッジ) で定義されています。

以下の詳細では、クライアントが [SCTE-214-3] に準拠して "emsg" に期待する必要がある特定の値について概説しています。

| **フィールド名**          | **フィールドの型**          | **必須** | **説明**                                                                                                                                                                                                                                                                                                                                                    |
|-------------------------|-------------------------|---------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| scheme_id_uri           | string                  | 必須      | メッセージのスキームを示します。 スキームは、Live Server Manifest Box の Scheme 属性の値に設定されます。 値はメッセージのスキームを示す URN で**なければなりません**。 [SCTE-35] メッセージの場合、これは [SCTE-214-3] に準拠した "urn:scte:scte35:2013:bin" で**なければなりません**。 |
| 値                   | string                  | 必須      | メッセージのセマンティクスをカスタマイズするためにスキームの所有者によって使われる追加の文字列値。 スキームが同じ複数のイベント ストリームを区別するため、値はイベント ストリームの名前に設定されます (Smooth の取り込みの場合は trackName、RTMP の取り込みの場合は AMF メッセージ名)。                                                                  |
| タイムスケール               | 32 ビット符号なし整数 | 必須      | "emsg" ボックス内の時間フィールドと継続時間フィールドのタイムスケール (1 秒間のティック数)。                                                                                                                                                                                                                                                                        |
| Presentation_time_delta | 32 ビット符号なし整数 | 必須      | イベントのプレゼンテーション時間と、このセグメントでの最も早いプレゼンテーション時刻の間を表す、メディア プレゼンテーション時間の差分。 プレゼンテーション時間と継続時間は、[ISO-14496-12] Annex I で定義されているように、タイプ 1 または 2 のストリーム アクセス ポイント (SAP) と一致している**必要があります**。                                                                                            |
| event_duration          | 32 ビット符号なし整数 | 必須      | イベントの継続時間、または継続時間が不明であることを示す 0xFFFFFFFF。                                                                                                                                                                                                                                                                                          |
| Id                      | 32 ビット符号なし整数 | 必須      | メッセージのこのインスタンスを示します。 同じセマンティクスを持つメッセージには、同じ値が設定されている必要があります。 メッセージが取り込まれときに ID が指定されていない場合は、Azure Media Services が一意 ID を生成します。                                                                                                                                                    |
| Message_data            | byte array              | 必須      | イベント メッセージ。 [SCTE-35] メッセージの場合、メッセージ データは [SCTE-214-3] に準拠したバイナリ splice_info_section() です。 |

### <a name="334-dash-message-handling"></a>3.3.4 DASH メッセージの処理

イベントは、ビデオ トラックとオーディオ トラックの両方について、"emsg" ボックスの帯域内でシグナル通知されます。  シグナル通知は、presentation_time_delta が 15 秒以下であるすべてのセグメント要求に対して発生します。 

スライディング プレゼンテーション ウィンドウが有効になっている場合は、イベント メッセージのプレゼンテーション時間と継続時間の合計がマニフェスト内のメディア データの時間より小さいと、イベント メッセージが MPD から削除されます。  つまり、イベント メッセージで参照されているメディア時間がスライディング プレゼンテーション ウィンドウから出ると、そのイベント メッセージはマニフェストから削除されます。

## <a name="4-scte-35-ingest-implementation-guidance-for-encoder-vendors"></a>4.エンコーダー ベンダー向けの SCTE-35 取り込み実装ガイダンス

以下のガイドラインは、エンコーダー ベンダーによるこの仕様の実装に影響する可能性がある一般的な問題です。  以下のガイドラインは、他の主体によるこの仕様の実装を容易にするために、実際のパートナーのフィードバックに基づいて収集されたものです。 

[SCTE-35] メッセージは、[MS-SSTR-Ingest] の場合はスキーム **"urn:scte:scte35:2013:bin"** を使って、[RTMP] の取り込みの場合は **"scte35"** 型を使って、バイナリ形式で取り込まれます。 MPEG-2 トランスポート ストリーム プレゼンテーション タイムスタンプ (PTS) に基づく [SCTE-35] のタイミングの変換を容易にするため、PTS (pts_time + splice_time() の pts_adjustment) とメディア タイムラインの間のマッピングが、イベント プレゼンテーション時間 (Smooth の取り込みの場合は fragment_absolute_time フィールド、RTMP の取り込みの場合は time フィールド) によって提供されます。 33 ビットの PTS 値は約 26.5 時間ごとにロールオーバーされるため、マッピングが必要になります。

Smooth Streaming の取り込み [MS-SSTR-Ingest] では、[SCTE-35] で定義されている **splice_info_section()** が Media Data Box ("mdat") に含まれて**いなければなりません**。 

RTMP の取り込みの場合、AMF メッセージの cue 属性は、[SCTE-35] で定義されている、base64 でエンコードされた **splice_info_section()** に設定されます。  

メッセージが上で説明した形式の場合、メッセージは上で定義したように HLS、Smooth、DASH クライアントに送信されます。  

Azure Media Services プラットフォームを使用して実装をテストするときは、エンコード LiveEvent でのテストに進む前に、まずは "パススルー" LiveEvent を使用してテストを開始してください。

---

## <a name="next-steps"></a>次の手順
Media Services のラーニング パスを確認します。

[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>フィードバックの提供
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]
