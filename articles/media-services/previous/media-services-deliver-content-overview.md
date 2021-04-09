---
title: 顧客へのコンテンツの配信
description: このトピックでは、Azure Media Services を使用したコンテンツの配信の概要を説明します。
services: media-services
author: IngridAtMicrosoft
manager: femila
ms.assetid: 89ede54a-6a9c-4814-9858-dcfbb5f4fed5
ms.service: media-services
ms.workload: media
ms.topic: article
ms.date: 03/10/2021
ms.author: inhenkel
ms.openlocfilehash: 1ad89345a2779766fde4559758e61dff92023741
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "103016612"
---
# <a name="deliver-content-to-customers"></a>顧客へのコンテンツ配信

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

ストリーミング コンテンツまたはビデオ オン デマンド コンテンツを顧客に配信するときの目標は、さまざまなネットワーク条件にある多様なデバイスに高品質のビデオを配信することにあります。

この目標を実現するために、次の手順を実行することができます。

* ストリームをマルチビットレート (アダプティブ ビットレート) ビデオ ストリームにエンコードします。 この手順では、品質とネットワークの条件が考慮されます。
* Microsoft Azure Media Services の [ダイナミック パッケージ](media-services-dynamic-packaging-overview.md) を使用して、ストリームをさまざまなプロトコルに動的に再パッケージ化します。 この手順では、さまざまなデバイスでのストリーミングが考慮されます。 Media Services は、次のアダプティブ ビットレート ストリーミング配信テクノロジをサポートしています。 <br/>
    * **HTTP ライブ ストリーミング** (HLS) - URL の "/Manifest" 部分に "(format=m3u8-aapl)" パスを追加して、ストリーミング配信元サーバーに **Apple iOS** ネイティブ デバイスでの使用のために HLS コンテンツを返すよう指示します (詳細については、「[ロケーター](#locators)」と [URL](#URLs) に関するセクションを参照してください)。
    * **MPEG-DASH** - URL の "/Manifest" 部分に "(format=mpd-time-csf)" パスを追加して、ストリーミング配信元サーバーに MPEG-DASH を返すよう指示します (詳細については、「[ロケーター](#locators)」と [URL](#URLs) に関するセクションを参照してください)。
    * **スムーズ ストリーミング**。

>[!NOTE]
>AMS アカウントの作成時に、**既定** のストリーミング エンドポイントが自分のアカウントに追加され、**停止** 状態になっています。 コンテンツのストリーミングを開始し、ダイナミック パッケージと動的暗号化を活用するには、コンテンツのストリーミング元のストリーミング エンドポイントが **実行中** 状態である必要があります。 

この記事では、コンテンツ配信の重要な概念の概要を説明します。

既知の問題を確認するには、「 [既知の問題](media-services-deliver-content-overview.md#known-issues)」をご覧ください。

## <a name="dynamic-packaging"></a>動的パッケージ
Media Services が提供するダイナミック パッケージを使用すると、アダプティブ ビットレートの MP4 または スムーズ ストリーミング でエンコードされたコンテンツを、Media Services でサポートされるストリーミング形式 (MPEG-DASH、HLS、スムーズ ストリーミング) でそのまま配信できます。つまり、これらのストリーミング形式に再パッケージ化する必要がありません。 ダイナミック パッケージを使用してコンテンツを配信することをお勧めします。

ダイナミック パッケージを活用するには、mezzanine (ソース) ファイルを一連のアダプティブ ビットレート MP4 ファイルまたはアダプティブ ビットレート Smooth Streaming ファイルにエンコードする必要があります。

ダイナミック パッケージを使用すると、単一のストレージ形式のファイルに対して保存と支払を行います。 Media Services は要求に応じて適切な応答を作成して返します。

ダイナミック パッケージは、Standard および Premium のストリーミング エンドポイントで使用できます。 

詳細については、「 [ダイナミック パッケージ](media-services-dynamic-packaging-overview.md)」を参照してください。

## <a name="filters-and-dynamic-manifests"></a>フィルターと動的マニフェスト
Media Services を使用すると、資産にフィルターを定義できます。 これらのフィルターは、ビデオの特定セクションのみの再生や、顧客のデバイスで処理できるオーディオやビデオ演奏のサブセットの特定 (資産に関連付けられているすべての演奏ではなく) などを顧客が実行できるようにする、サーバー側のルールです。 このフィルター処理は "*動的マニフェスト*" によって実行されます。これは、顧客が 1 つまたは複数の指定したフィルターに基づいてビデオをストリーミングするようにリクエストした場合に作成されます。

詳細については、「 [フィルターと動的マニフェスト](media-services-dynamic-manifest-overview.md)」を参照してください。

## <a name="locators"></a><a name="locators"></a>ロケーター
コンテンツのストリーミングやダウンロードに使用できる URL をユーザーに提供するには、まず、ロケーターを作成して資産を公開する必要があります。 ロケーターは、資産に含まれているファイルにアクセスするためのエントリ ポイントになります。 Media Services では、2 種類のロケーターがサポートされています。

* OnDemandOrigin ロケーターは、 メディアのストリーミング (MPEG-DASH、HLS、Smooth Streaming など) やファイルのプログレッシブ ダウンロードに使用します。
* Shared Access Signature (SAS) URL ロケーターは、 メディア ファイルをローカル コンピューターにダウンロードする際に使用します。

*アクセス ポリシー* は、アクセス許可 (読み取り、書き込み、一覧表示など) や、クライアントが特定の資産にアクセスできる期間を定義するために使用します。 一覧表示のアクセス許可 (AccessPermissions.List) は、OnDemandOrigin ロケーターの作成に使用しないでください。

ロケーターには有効期限があります。 Azure ポータルにより、ロケーターの有効期限の日付が 100 年後に設定されます。

> [!NOTE]
> 2015 年 3 月より前に Azure ポータルを使用してロケーターを作成した場合は、ロケーターの有効期限が 2 年後に設定されていました。
> 
> 

ロケーターの有効期限を更新するには、[REST](/rest/api/media/operations/locator#update_a_locator) API または [.NET](/dotnet/api/microsoft.windowsazure.mediaservices.client.ilocator) API を使用します。 SAS ロケーターの有効期限を更新すると、URL が変更されることにご注意ください。

ロケーターは、ユーザーごとのアクセス制御を管理するためのものではありません。 デジタル著作権管理 (DRM) ソリューションを使用することで、個々のユーザーに異なるアクセス権限を付与できます。 詳細については、「 [メディアの保護](/previous-versions/azure/dn282272(v=azure.100))」をご覧ください。

ロケーターを作成する際、Azure Storage に必要な記憶域や伝達プロセスの関係上 30 秒の遅延が生じる場合があります。

## <a name="adaptive-streaming"></a>アダプティブ ストリーミング
アダプティブ ビットレート テクノロジにより、ビデオ再生アプリケーションでネットワークの状態を判断し、複数のビットレートから選択することができます。 ネットワーク通信のパフォーマンスが低下した場合、低い画質レベルでビデオの再生を継続できるように、クライアントは以前より低いビットレートを選択することができます。 ネットワークの状態が改善したら、クライアントはより高画質なより高いビットレートに切り替えることができます。 Azure Media Services は、次のアダプティブ ビットレート テクノロジをサポートしています: HTTP ライブ ストリーミング (HLS)、スムーズ ストリーミング、MPEG DASH。

ユーザーにストリーミング URL を提供するには、最初に OnDemandOrigin ロケーターを作成する必要があります。 ロケーターを作成すると、ストリーミングするコンテンツが含まれている資産の基本パスが提供されます。 ただし、このコンテンツをストリーミングするためには、このパスをさらに変更する必要があります。 ストリーミング マニフェスト ファイルの完全な URL を構築するには、ロケーターのパスの値とマニフェスト ファイル (filename.ism) の名前を連結する必要があります。 その後、 **/Manifest** と適切な形式 (必要な場合) をロケーターのパスに付加します。

> [!NOTE]
> TLS 接続経由でコンテンツのストリーミングもできます。 そのためには、ストリーミング URL の先頭が HTTPS になっていることをご確認ください。 なお、現在のところ、AMS ではカスタム ドメインを使用した TLS はサポートされていません。  
> 

TLS 経由でのストリーミングを実行できるのは、コンテンツの配信元となるストリーミング エンドポイントが 2014 年 9 月 10 日より後に作成されている場合のみです。 ストリーミング URL の基になるストリーミング エンドポイントの作成日が 2014 年 9 月 10 日より後である場合、URL に "streaming.mediaservices.windows.net" が含まれます。 "origin.mediaservices.windows.net" (旧形式) を含んだストリーミング URL では、TLS がサポートされません。 URL が旧形式である場合、TLS ストリーミングに対応するためには、新しいストリーミング エンドポイントを作成してください。 TLS でコンテンツをストリーミングするには、新しいストリーミング エンドポイントに基づいた URL を使用する必要があります。

## <a name="streaming-url-formats"></a><a name="URLs"></a>ストリーミング URL の形式

### <a name="mpeg-dash-format"></a>MPEG-DASH 形式
{ストリーミング エンドポイント名-Media Services アカウント名}.streaming.mediaservices.windows.net/{ロケーター ID}/{ファイル名}.ism/Manifest(format=mpd-time-csf)

http:\//testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=mpd-time-csf)

### <a name="apple-http-live-streaming-hls-v4-format"></a>Apple HTTP Live Streaming (HLS) V4 形式
{ストリーミング エンドポイント名-Media Services アカウント名}.streaming.mediaservices.windows.net/{ロケーター ID}/{ファイル名}.ism/Manifest(format=m3u8-aapl)

http:\//testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=m3u8-aapl)

### <a name="apple-http-live-streaming-hls-v3-format"></a>Apple HTTP Live Streaming (HLS) V3 形式
{ストリーミング エンドポイント名-Media Services アカウント名}.streaming.mediaservices.windows.net/{ロケーター ID}/{ファイル名}.ism/Manifest(format=m3u8-aapl-v3)

http:\//testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=m3u8-aapl-v3)

### <a name="apple-http-live-streaming-hls-format-with-audio-only-filter"></a>Apple HTTP Live Streaming (HLS) 形式 (オーディオ専用フィルター付き)
既定では、HLS マニフェストにはオーディオ専用トラックのみが含まれます。 これは、Apple ストアのセルラー ネットワーク用の認定で必要です。 この場合、クライアントの帯域幅が十分でないか、2G 接続を超えて接続されると、再生はオーディオ専用に切り替わります。 このため、バッファリングされることなく引き続きコンテンツがストリーミングされますが、画像は表示されません。 シナリオによっては、オーディオ専用よりもプレーヤーのバッファリングのほうが好ましい場合があります。 オーディオ専用トラックを削除する場合は、 **audio-only=false** を URL に追加します。

http:\//testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=m3u8-aapl-v3,audio-only=false)

詳細については、 [動的マニフェストの構成のサポートと HLS 出力の追加機能](https://azure.microsoft.com/blog/azure-media-services-release-dynamic-manifest-composition-remove-hls-audio-only-track-and-hls-i-frame-track-support/)に関するページをご覧ください。

### <a name="smooth-streaming-format"></a>ストリーミング URL の形式
{ストリーミング エンドポイント名-Media Services アカウント名}.streaming.mediaservices.windows.net/{ロケーター ID}/{ファイル名}.ism/Manifest

例:

http:\//testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest

### <a name="smooth-streaming-20-manifest-legacy-manifest"></a><a id="fmp4_v20"></a>Smooth Streaming 2.0 マニフェスト (レガシ マニフェスト)
既定では、Smooth Streaming のマニフェスト形式には、繰り返しタグ (r タグ) が含まれています。 ただし、一部のプレーヤーは、r タグをサポートしていません。 これらのプレーヤーを使用するクライアントは、r タグを無効にする形式を使用できます。

{ストリーミング エンドポイント名-Media Services アカウント名}.streaming.mediaservices.windows.net/{ロケーター ID}/{ファイル名}.ism/Manifest(format=fmp4-v20)

`http://testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=fmp4-v20)`

## <a name="progressive-download"></a>プログレッシブ ダウンロード
プログレッシブ ダウンロードでは、ファイル全体がダウンロードされる前に、メディアの再生を開始できます。 .ism* (ismv、isma、ismt、ismc) ファイルのプログレッシブ ダウンロードはできません。

コンテンツをプログレッシブ ダウンロードするには、OnDemandOrigin のロケーター型を使用します。 次の例は、OnDemandOrigin のロケーター型に基づいた URL を示しています。

`http://amstest1.streaming.mediaservices.windows.net/3c5fe676-199c-4620-9b03-ba014900f214/BigBuckBunny_H264_650kbps_AAC_und_ch2_96kbps.mp4`

プログレッシブ ダウンロードで元のサービスからストリーミングするには、ストレージで暗号化された資産の暗号化を解除する必要があります。

## <a name="download"></a>ダウンロード
クライアント デバイスにコンテンツをダウンロードするには、SAS ロケーターを作成する必要があります。 SAS ロケーターでは、ファイルが配置されている Azure ストレージ コンテナーにアクセスできます。 ダウンロード URL を作成するには、ホストと SAS 署名の間にファイル名を埋め込む必要があります。

次の例は、SAS ロケーターに基づいている URL を示しています。

`https://test001.blob.core.windows.net/asset-ca7a4c3f-9eb5-4fd8-a898-459cb17761bd/BigBuckBunny.mp4?sv=2012-02-12&se=2014-05-03T01%3A23%3A50Z&sr=c&si=7c093e7c-7dab-45b4-beb4-2bfdff764bb5&sig=msEHP90c6JHXEOtTyIWqD7xio91GtVg0UIzjdpFscHk%3D`

次の考慮事項が適用されます。

* プログレッシブ ダウンロードで元のサービスからストリーミングするには、ストレージで暗号化された資産の暗号化を解除する必要があります。
* 12 時間以内に完了しないダウンロードは失敗します。

## <a name="streaming-endpoints"></a>ストリーミング エンドポイント

ストリーミング エンドポイントは、コンテンツをクライアント プレーヤー アプリケーションや、再配布のためのコンテンツ配信ネットワーク (CDN) に直接配信するストリーミング サービスを表します。 ストリーミング エンドポイント サービスからの送信ストリームには、Media Services アカウントのライブ ストリームやビデオ オンデマンドの資産があります。 ストリーミング エンドポイントには 2 つの種類があります。**Standard** と **Premium** です。 詳しくは、「[ストリーミング エンドポイントの概要](media-services-streaming-endpoints-overview.md)」をご覧ください。

>[!NOTE]
>AMS アカウントの作成時に、**既定** のストリーミング エンドポイントが自分のアカウントに追加され、**停止** 状態になっています。 コンテンツのストリーミングを開始し、ダイナミック パッケージと動的暗号化を活用するには、コンテンツのストリーミング元のストリーミング エンドポイントが **実行中** 状態である必要があります。 

## <a name="known-issues"></a>既知の問題
### <a name="changes-to-smooth-streaming-manifest-version"></a>スムーズ ストリーミング マニフェスト バージョンへの変更
2016 年 7 月より前のサービス リリースでは、Media Encoder Standard、メディア エンコーダー プレミアム ワークフロー、または以前の Azure Media Encoder によって生成された資産は、ダイナミック パッケージを使用してストリーミングされていました。返される Smooth Streaming のマニフェストはバージョン 2.0 に対応します。 バージョン 2.0 では、フラグメントの継続期間では、いわゆる繰り返し ("r") タグを使用しません。 次に例を示します。

```xml
<?xml version="1.0" encoding="UTF-8"?>
<SmoothStreamingMedia MajorVersion="2" MinorVersion="0" Duration="8000" TimeScale="1000">
    <StreamIndex Chunks="4" Type="video" Url="QualityLevels({bitrate})/Fragments(video={start time})" QualityLevels="3" Subtype="" Name="video" TimeScale="1000">
        <QualityLevel Index="0" Bitrate="1000000" FourCC="AVC1" MaxWidth="640" MaxHeight="360" CodecPrivateData="00000001674D4029965201405FF2E02A100000030010000003032E0A000F42400040167F18E3050007A12000200B3F8C70ED0B16890000000168EB7352" />
        <c t="0" d="2000" n="0" />
        <c d="2000" />
        <c d="2000" />
        <c d="2000" />
    </StreamIndex>
</SmoothStreamingMedia>
```

2016 年 7 月のサービス リリースでは、生成される Smooth Streaming のマニフェストがバージョン 2.2 に対応しており、フラグメントの継続期間で繰り返しタグを使用できます。 次に例を示します。

```xml
<?xml version="1.0" encoding="UTF-8"?>
<SmoothStreamingMedia MajorVersion="2" MinorVersion="2" Duration="8000" TimeScale="1000">
    <StreamIndex Chunks="4" Type="video" Url="QualityLevels({bitrate})/Fragments(video={start time})" QualityLevels="3" Subtype="" Name="video" TimeScale="1000">
        <QualityLevel Index="0" Bitrate="1000000" FourCC="AVC1" MaxWidth="640" MaxHeight="360" CodecPrivateData="00000001674D4029965201405FF2E02A100000030010000003032E0A000F42400040167F18E3050007A12000200B3F8C70ED0B16890000000168EB7352" />
        <c t="0" d="2000" r="4" />
    </StreamIndex>
</SmoothStreamingMedia>
```

一部のレガシ Smooth Streaming クライアントは繰り返しタグをサポートしいない場合があり、マニフェストを読み込むことができません。 この問題を軽減するには、レガシ マニフェスト形式のパラメーター **(format=fmp4 v20)** を使用するか、繰り返しタグをサポートする最新バージョンにクライアントを更新します。 詳細については、 [Smooth Streaming 2.0](media-services-deliver-content-overview.md#fmp4_v20)に関するセクションをご覧ください。

## <a name="media-services-learning-paths"></a>Media Services のラーニング パス
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>フィードバックの提供
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="related-topics"></a>関連トピック
[ストレージ キーの展開後に Media Services ロケーターを更新する](media-services-roll-storage-access-keys.md)