---
title: Azure Media Services v3 リリース ノート
description: 常に最新の開発情報を把握していただけるよう、この記事では Azure Media Services v3 の最新の更新情報を提供します。
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: na
ms.topic: article
ms.date: 03/17/2021
ms.author: inhenkel
ms.openlocfilehash: 9e5a6737d2e37392efd305910ff5370adc84940f
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "104596741"
---
# <a name="azure-media-services-v3-release-notes"></a>Azure Media Services v3 リリース ノート

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

常に最新の開発情報を把握していただけるよう、この記事では以下に関する情報を提供します。

* 最新のリリース
* 既知の問題
* バグの修正
* 非推奨の機能

## <a name="march-2021"></a>2021 年 3 月

### <a name="new-language-support-added-to-the-audioanalyzer-preset"></a>AudioAnalyzer プリセットに追加された新しい言語サポート

AudioAnalyzer プリセット (基本と標準モードの両方) に、ビデオの文字起こしと字幕に使用できる言語が新しく追加されました。

* 英語 (オーストラリア): en-AU
* フランス語 (カナダ): fr-CA
* アラビア語 (バーレーン) 現代標準: ar-BH
* アラビア語 (エジプト): ar-EG
* アラビア語 (イラク): ar-IQ
* アラビア語 (イスラエル): ar-IL
* アラビア語 (ヨルダン): ar-JO
* アラビア語 (クウェート): ar-KW
* アラビア語 (レバノン): ar-LB
* アラビア語 (オマーン): ar-OM
* アラビア語 (カタール): ar-QA
* アラビア語 (サウジアラビア): ar-SA
* デンマーク語: da-DK
* ノルウェー語: nb-NO
* スウェーデン語: sv-SE
* フィンランド語: fi-FI
* タイ語: th-TH
* トルコ語: tr-TR

[ビデオとオーディオ ファイルの概念の分析に関する記事](analyzing-video-audio-files-concept.md)で、利用可能な最新の言語をご確認ください。

## <a name="february-2021"></a>2021 年 2 月

### <a name="hevc-encoding-support-in-standard-encoder"></a>Standard Encoder での HEVC エンコードのサポート

Standard Encoder で、8 ビットの HEVC (H.265) エンコードがサポートされるようになりました。 HEVC コンテンツは、'hev1' 形式を使用して、Dynamic Packager を通じて配信およびパッケージ化できます。  

HEVC サンプルを含む新しい .NET カスタム エンコードは、[media-services-v3-dotnet Git Hub リポジトリ](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/main/VideoEncoding/EncodingWithMESCustomPreset_HEVC)で入手できます。
カスタム エンコードに加えて、次の組み込み HEVC エンコード プリセットを新しく使用できるようになりました。

- H265ContentAwareEncoding
- H265AdaptiveStreaming
- H265SingleBitrate720P
- H265SingleBitrate1080p
- H265SingleBitrate4K

v2 API の Premium Encoder で HEVC を使用していたお客様は、Standard Encoder で新しい HEVC エンコード サポートを使用するように移行する必要があります。

### <a name="azure-media-services-v2-api-and-sdks-deprecation-announcement"></a>Azure Media Services v2 API と SDK の廃止に関するお知らせ

#### <a name="update-your-azure-media-services-rest-api-and-sdks-to-v3-by-29-february-2024"></a>2024 年 2 月 29 日までに Azure Media Services REST API と SDK を v3 に更新してください

.NET および Java 用のバージョン 3 の Azure Media Services REST API とクライアント SDK では、バージョン 2 よりも多くの機能を提供しているため、.NET および Java 用のバージョン 2 の Azure Media Services REST API とクライアント SDK を廃止する予定です。

.NET および Java 用のバージョン 3 の Azure Media Services REST API とクライアント SDK の豊富なメリットを活用するために、早めに切り替えを行うことをお勧めします。
バージョン 3 では次のものが提供されます。
 
- 24 時間 365 日体制のライブ イベント サポート
- ARM REST API、.NET Core 用のクライアント SDK、Node.js、Python、Java、Go、Ruby。
- カスタマー マネージド キー、信頼されたストレージ統合、プライベート リンクのサポート、[その他](https://docs.microsoft.com/azure/media-services/latest/migrate-v-2-v-3-migration-benefits)

#### <a name="action-required"></a>必要な操作

ワークロードの中断を最小限に抑えるために、[移行ガイド](https://go.microsoft.com/fwlink/?linkid=2149150&clcid=0x409)を参照して、2024 年 2 月 29 日までにコードをバージョン 2 の API と SDK からバージョン 3 の API と SDK に移行してください。
**2024 年 2 月 29 日以降** は、Azure Media Services は、バージョン 2 の REST API、ARM アカウント管理 API バージョン 2015-10-01、またはバージョン 2 の .NET クライアント SDK からのトラフィックを受け入れなくなります。 これには、バージョン 2 の API を呼び出す可能性があるサードパーティ製のオープンソースのクライアント SDK が含まれます。  

公式の [Azure の更新情報に関するお知らせ](https://azure.microsoft.com/updates/update-your-azure-media-services-rest-api-and-sdks-to-v3-by-29-february-2024/)をご確認ください。

### <a name="standard-encoder-support-for-v2-api-features"></a>Standard Encoder での v2 API 機能のサポート

新しく追加された HEVC (H.265) エンコードのサポートに加えて、2020-05-01 バージョンのエンコード API では次の機能を使用できるようになりました。

- 新しい **Jobinputclip** サポートを使用して、複数の入力ファイルを結合できるようになりました。
    - .NET 用の例では、[2 つのアセットを結合](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/main/VideoEncoding/EncodingWithMESCustomStitchTwoAssets)する方法を示しています。
- オーディオ トラックのオプションにより、お客様は着信オーディオ トラックを選択してマップし、出力にルーティングしてエンコードできます
    - **Audiotrackdescriptor** とトラックのオプションの詳細については、[REST API OpenAPI](https://github.com/Azure/azure-rest-api-specs/blob/8d15dc681b081cca983e4d67fbf6441841d94ce4/specification/mediaservices/resource-manager/Microsoft.Media/stable/2020-05-01/Encoding.json#L385) に関するセクションを参照してください。
- エンコードでのトラックの選択 - お客様は、ABR ソース ファイル、または複数のビットレート トラックを含むライブ アーカイブからトラックを選択できます。 ライブ イベント アーカイブ ファイルから MP4 を生成する場合に非常に便利です。
    - [Videotrackdescriptor](https://github.com/Azure/azure-rest-api-specs/blob/8d15dc681b081cca983e4d67fbf6441841d94ce4/specification/mediaservices/resource-manager/Microsoft.Media/stable/2020-05-01/Encoding.json#L1562) に関するセクションを参照してください
- FaceDetector に追加された編集 (ぼかし) 機能
    - FaceDetector プリセットの [Redact](https://github.com/Azure/azure-rest-api-specs/blob/8d15dc681b081cca983e4d67fbf6441841d94ce4/specification/mediaservices/resource-manager/Microsoft.Media/stable/2020-05-01/Encoding.json#L634) と [Combined](https://github.com/Azure/azure-rest-api-specs/blob/8d15dc681b081cca983e4d67fbf6441841d94ce4/specification/mediaservices/resource-manager/Microsoft.Media/stable/2020-05-01/Encoding.json#L649) の各モードを参照してください

### <a name="new-client-sdk-releases-for-2020-05-01-version-of-the-azure-media-services-api"></a>2020-05-01 バージョンの Azure Media Services API の新しいクライアント SDK リリース

使用可能な全言語の新しいクライアント SDK バージョン、および前述の機能を使用できるようになりました。
パッケージ マネージャーを使用して、コード ベースで最新クライアント SDK に更新してください。

- [.NET SDK パッケージ 3.0.4](https://www.nuget.org/packages/Microsoft.Azure.Management.Media/)
- [Node.js Typescript バージョン 8.1.0](https://www.npmjs.com/package/@azure/arm-mediaservices)
- [Python azure-mgmt-media 3.1.0](https://pypi.org/project/azure-mgmt-media/)
- [Java SDK 1.0.0-beta.2](https://search.maven.org/artifact/com.azure.resourcemanager/azure-resourcemanager-mediaservices/1.0.0-beta.2/jar)

### <a name="new-security-features-available-in-the-2020-05-01-version-of-the-azure-media-services-api"></a>2020-05-01 バージョンの Azure Media Services API で使用できる新しいセキュリティ機能

- **[カスタマー マネージド キー](concept-use-customer-managed-keys-byok.md)** : "2020-05-01" バージョンの API を使用して作成されたアカウントに保存されているコンテンツ キーと他のデータは、アカウント キーを使用して暗号化されます。 お客様は、アカウント キーを暗号化するためのキーを提供できます。

- **[信頼されたストレージ](concept-trusted-storage.md)** : Media Services は、Media Services アカウントに関連付けられているマネージド ID を使用して Azure Storage にアクセスするように構成できます。 マネージド ID を使用してストレージ アカウントにアクセスする場合、お客様は Media Services シナリオをブロックすることなく、より制限の厳しいネットワーク ACL をストレージ アカウントで構成できます。

- **[マネージド ID](concept-managed-identities.md)** : お客様は、Media Services アカウントのシステム割り当てマネージド ID を有効にして、キーコンテナー (カスタマー マネージド キーの場合) とストレージ アカウント (信頼されるストレージの場合) にアクセスできるようにすることができます。

### <a name="updated-typescript-nodejs-samples-using-isomorphic-sdk-for-javascript"></a>Isomorphic SDK for JavaScript を使用した Typescript Node.js のサンプルの更新

Node.js のサンプルは、最新の Isomorphic SDK を使用するように更新されています。 このサンプルでは、Typescript の使用方法が示されています。 また、Node.js/Typescript 用に、新しいライブ ストリーミング サンプルが追加されました。

**[media-services-v3-node-tutorials](https://github.com/Azure-Samples/media-services-v3-node-tutorials)** Git Hub リポジトリで、最新のサンプルを確認してください。

### <a name="new-live-stand-by-mode-to-support-faster-startup-from-warm-state"></a>ウォーム状態からの高速な起動をサポートする新しいライブ スタンバイ モード

ライブ イベントで、"スタンバイ" に低コスト課金モードが適用されるようになりました。 このため、お客様は、"ホット プール" の作成時に、ライブ イベントをより低コストで事前割り当てすることができます。 その後、お客様は、スタンバイ ライブ イベントを使用することで、作成時のコールドな状態から開始するよりも速く実行中の状態に移行することができます。  このため、チャネルの開始にかかる時間が大幅に短縮され、低価格モードで実行されているマシンをホットプールに高速に割り当てることができます。
最新の料金の詳細については、[こちら](https://azure.microsoft.com/pricing/details/media-services)を参照してください。
スタンバイ状態とライブ イベントのその他の状態の詳細については、記事「[ライブ イベントの状態と課金](https://docs.microsoft.com/azure/media-services/latest/live-event-states-billing)」を参照してください。

## <a name="december-2020"></a>2020 年 12 月

### <a name="regional-availability"></a>リージョン別の提供状況

Azure Media Services は、ノルウェー東部リージョンの Azure portal で利用できるようになりました。  このリージョンには restV2 はありません。

## <a name="october-2020"></a>2020 年 10 月

### <a name="basic-audio-analysis"></a>Basic の音声分析

音声分析のプリセットに、Basic モードの価格レベルが含まれるようになりました。 新しい Basic の音声アナライザー モードによって、音声の文字起こしの抽出、出力キャプションと字幕の書式設定の低コストのオプションが提供されます。 このモードを使用すると、音声からテキストへの文字起こし、VTT 字幕またはキャプション ファイルの生成が実行されます。 このモードの出力には、キーワード、文字起こし、タイミング情報のみを含む Insights JSON ファイルなどがあります。 このモードには、自動言語検出と話者のダイアライゼーションは含まれていません。 [サポートされている言語](analyzing-video-audio-files-concept.md#built-in-presets)のリストを参照してください。

インデクサー v1 およびインデクサー v2 を使用しているお客様は、Basic の音声分析のプリセットに移行する必要があります。

基本的な音声アナライザー モードの詳細については、[ビデオおよび音声のファイルの分析](analyzing-video-audio-files-concept.md)に関するページを参照してください。  REST API で Basic の音声アナライザー モードを使用する方法については、[基本的なオーディオ変換を作成する方法](how-to-create-basic-audio-transform.md)に関するページを参照してください。

### <a name="live-events"></a>ライブ イベント

ライブ イベントが停止したときに、ほとんどのプロパティの更新が許可されるようになりました。 さらに、ユーザーは、ライブ イベントの入力およびプレビューの URL に対して、静的ホスト名のプレフィックスを指定できます。 VanityUrl は、プロパティの意図をより適切に反映するために `useStaticHostName` と呼ばれるようになりました。

ライブ イベントにスタンバイ状態が追加されました。  「[Media Services のライブ イベントとライブ出力](./live-events-outputs-concept.md)」を参照してください。

ライブ イベントでは、さまざまな入力の縦横比の受信がサポートされています。 ストレッチ モードを使用すると、顧客が出力の拡張動作を指定できます。

ライブ エンコードに、0.5 - 20 秒の固定キー フレーム間隔フラグメントを出力する機能が追加されました。

### <a name="accounts"></a>アカウント

> [!WARNING]
> 2020-05-01 API バージョンで Media Services アカウントを作成した場合、RESTv2 では機能しません。 

## <a name="august-2020"></a>2020 年 8 月

### <a name="dynamic-encryption"></a>動的暗号化

レガシ PlayReady Protected Interoperable File Format (PIFF 1.1) 暗号化のサポートが Dynamic Packager で利用できるようになりました。 これにより、Microsoft が公開した Common Encryption 標準 (CENC) の早期ドラフトを実装した Samsung 製および LG 製のレガシ スマート TV セットのサポートが提供されます。  PIFF 1.1 形式は、Silverlight クライアント ライブラリによって以前サポートされていた暗号化形式としても知られています。 現在、この暗号化形式の唯一のユース ケース シナリオは、PIFF 1.1 暗号化によるスムーズ ストリーミングのみがサポートされている一部のリージョンで相当な数のスマート テレビが残っているレガシ スマート TV 市場をターゲットにすることです。

新しい PIFF 1.1 暗号化サポートを使用するには、ストリーミング ロケーターの URL パスで暗号化値を "piff" に変更します。 詳細については、[コンテンツ保護の概要](content-protection-overview.md)に関するページを参照してください。
例: `https://amsv3account-usw22.streaming.media.azure.net/00000000-0000-0000-0000-000000000000/ignite.ism/manifest(encryption=piff)`|

> [!NOTE]
> PIFF 1.1 のサポートは、初期の "Silverlight" バージョンの Common Encryption を実装したスマート TV (Samsung、LG) で下位互換性を保つためのソリューションとして提供されています。 PIFF 形式は、PIFF 1.1 バージョンの PlayReady 暗号化をサポートしている 2009 から 2015 年に出荷された Samsung または LG 製レガシ スマート TV をサポートするために必要な場合のみ使用することをお勧めします。 

## <a name="july-2020"></a>2020 年 7 月

### <a name="live-transcriptions"></a>ライブ文字起こし

ライブ文字起こしで 19 の言語と 8 つの地域がサポートされるようになりました。

### <a name="protecting-your-content-with-media-services-and-azure-ad"></a>Media Services と Azure AD によるコンテンツの保護

「[Azure AD を使用したエンド ツー エンドのコンテンツ保護](./azure-ad-content-protection.md)」というタイトルのチュートリアルを公開しました。

### <a name="high-availability"></a>高可用性

Media Services とビデオ オン デマンド (VOD) を使用した高可用性の[概要](./media-services-high-availability-encoding.md)と[サンプル](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/master/HighAvailabilityEncodingStreaming)を公開しました。

## <a name="june-2020"></a>2020 年 6 月

### <a name="live-video-analytics-on-iot-edge-preview-release"></a>Live Video Analytics on IoT Edge プレビュー リリース

Live Video Analytics on IoT Edge のプレビューが公開されました。 詳細については、「[リリース ノート](../live-video-analytics-edge/release-notes.md)」を参照してください。

Live Video Analytics on IoT Edge は、メディア サービス ファミリの拡張です。 独自のエッジ デバイスで任意の AI モデルを使用してライブ ビデオを分析し、必要に応じてそのビデオをキャプチャして記録することができます。 ライブ ビデオ パイプラインの構築と運用の複雑さを気にせずに、エッジでリアルタイムのビデオ分析を使用してアプリを作成できるようになりました。

## <a name="may-2020"></a>2020 年 5 月

Azure Media Services は、次のリージョンで一般提供されています。"ドイツ北部"、"ドイツ中西部"、"スイス北部"、および "スイス西部"。 お客様は、Azure portal を使用して、これらのリージョンに Media Services をデプロイできます。

## <a name="april-2020"></a>2020 年 4 月

### <a name="improvements-in-documentation"></a>ドキュメントの改善

Azure Media Player ドキュメントは、[Azure ドキュメント](../azure-media-player/azure-media-player-overview.md)に移行されました。

## <a name="january-2020"></a>2020 年 1 月

### <a name="improvements-in-media-processors"></a>メディア プロセッサの機能強化

- ビデオ分析でのインターレース ソースのサポートが強化されました。このようなコンテンツは推論エンジンに送信される前に適切にインターレースが解除されるようになりました。
- "最適" モードでサムネイルを生成すると、エンコーダーでは、モノクロではないフレームの選択にかかる検索時間が 30 秒を超えるようになりました。

### <a name="azure-government-cloud-updates"></a>Azure Government クラウドの更新

Media Services が次の Azure Government リージョンで一般公開されました。*米国政府アリゾナ* および *米国政府テキサス*。

## <a name="december-2019"></a>2019 年 12 月

ライブ ストリーミングとビデオ オンデマンド ストリーミングの両方の *Origin-Assist Prefetch* ヘッダーに対する CDN サポートが追加されました。Akamai CDN と直接契約しているお客様が利用できます。 Origin-Assist CDN-Prefetch 機能には、Akamai CDN と Azure Media Services オリジン間での次の HTTP ヘッダーの交換が含まれます。

|HTTP ヘッダー|値|送信者|受信者|目的|
| ---- | ---- | ---- | ---- | ----- |
|CDN-Origin-Assist-Prefetch-Enabled | 1 (既定) または 0 |CDN|出発地|CDN がプリフェッチ対応であることを示すこと|
|CDN-Origin-Assist-Prefetch-Path| 例: <br/>フラグメント(ビデオ= 1400000000, フォーマット = mpd-time-cmaf)|出発地|CDN|CDN にプリフェッチ パスを提供すること|
|CDN-Origin-Assist-Prefetch-Request|1 (プリフェッチ要求) または 0 (通常の要求)|CDN|出発地|CDN からの要求がプリフェッチであることを示すこと|

ヘッダー交換の一部を実際に確認するには、次の手順を実行します。

1. Postman または curl を使用して、オーディオまたはビデオのセグメントまたはフラグメントの Media Services 配信元に要求を発行します。 ヘッダー CDN-Origin-Assist-Prefetch-Enabled: 1 を必ず要求に追加してください。
2. 応答には、その値として相対パスが指定されたヘッダー CDN-Origin-Assist-Prefetch-Path が表示されます。

## <a name="november-2019"></a>2019 年 11 月

### <a name="live-transcription-preview"></a>ライブ文字起こし (プレビュー)

ライブ文字起こしのパブリック プレビューが始まっており、米国西部 2 リージョンでご利用いただけます。

ライブ文字起こしはアドオン機能としてライブ イベントと連動するように設計されています。  パススルーと Standard または Premium のエンコード ライブ イベントの両方でサポートされています。  この機能が有効になっている場合、このサービスでは Cognitive Services の[音声テキスト変換](../../cognitive-services/speech-service/speech-to-text.md)機能を使用して、受信したオーディオの音声を文字起こししてテキストにします。 その後、このテキストは、MPEG-DASH および HLS プロトコルのビデオとオーディオで配信できるようになります。 課金は、"実行中" 状態のとき、ライブ イベントの追加コストとなる新しいアドオン メーターに基づきます。  ライブ文字起こしと課金の詳細については、「[ライブ文字起こし](live-transcription.md)」を参照してください。

> [!NOTE]
> 現在、ライブ文字起こしは、米国西部 2 リージョンのプレビュー機能としてのみ提供されています。 現時点では英語 (en-us) 音声の文字起こしのみサポートされています。

### <a name="content-protection"></a>コンテンツの保護

去る 9 月に限定リージョンで公開された "*トークン再生防止*" 機能が全リージョンで利用できるようになりました。
Media Services をご利用のお客様は、キーまたはライセンスの要求に同じトークンを使用できる回数に上限を設定できるようになりました。 詳細については、「[トークン再生の防止](content-protection-overview.md#token-replay-prevention)」を参照してください。

### <a name="new-recommended-live-encoder-partners"></a>新しいおすすめのライブ エンコーダー パートナー

RTMP ライブ ストリーミングで次の新しいおすすめパートナー エンコーダーのサポートが追加されました。

- [Cambria Live 4.3](https://www.capellasystems.net/products/cambria-live/)
- [GoPro Hero7/8 および Max アクション カメラ](https://gopro.com/help/articles/block/getting-started-with-live-streaming)
- [Restream.io](https://restream.io/)

### <a name="file-encoding-enhancements"></a>ファイル エンコードの機能強化

- 新しいコンテンツに対応したエンコードのプリセットが使用できるようになりました。 これは、コンテンツに対応したエンコードを使用して、GOP がアラインメントされた MP4 セットを生成します。 入力コンテンツが指定されると、サービスは入力コンテンツの最初の軽量分析を実行します。 その結果を使用して、アダプティブ ストリーミングによる配信に最適なレイヤー数、適切なビット レート、解像度の設定を決定します。 このプリセットは、複雑さが低から中程度のビデオに特に有効です。つまり、ビット レートは低くなりますが、視聴者には快適なエクスペリエンスを提供できる品質を備えた出力ファイルということになります。 出力には、ビデオとオーディオがインターリーブされた MP4 ファイルが含まれるようになります。 詳細については、[オープン API 仕様](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/mediaservices/resource-manager/Microsoft.Media/stable/2018-07-01/Encoding.json)を参照してください。
- Standard Encoder のサイズ変更機能の性能とマルチスレッドが改善されました。 特定の条件下では、お客様は 5% から 40% の VOD エンコードで性能向上をご体験いただけます。 複雑性が低いコンテンツが複数のビットレートにエンコードされるとき、最も高い性能アップを確認できます。 
- 標準のエンコードでは、時間基準の GOP 設定の使用時、VOD エンコード中、可変フレーム レート (VFR) コンテンツで通常の GOP ペースが維持されるようになりました。  つまり、たとえば、15 fps から 30 fps の範囲で変化する混在フレーム レート コンテンツを送信すると、アダプティブ ビットレート ストリーミング MP4 ファイルへの出力に対して通常の GOP 距離が計算されます。 これにより HLS または DASH 経由で配信するときにトラック間で途切れなく切り替える機能が向上します。 
-  可変フレーム レート (VFR) ソース コンテンツの AV 同期の向上

### <a name="video-indexer-video-analytics"></a>Video Indexer、ビデオ分析

- VideoAnalyzer プリセットで抽出されたキーフレームがサイズ変更なく、動画の元の解像度で与えられるようになりました。 高解像度のキーフレーム抽出により元の画質が与えられ、Microsoft の Computer Vision サービスと Custom Vision サービスで提供される画像を基盤とする人工知能モデルを活用し、動画からさらに踏み込んだ分析情報を得ることができます。

## <a name="september-2019"></a>2019 年 9 月

###  <a name="media-services-v3"></a>Media Services v3  

#### <a name="live-linear-encoding-of-live-events"></a>ライブ イベントのライブ リニア エンコード

Media Services v3 で、24 時間 365 日のライブ イベントのライブ リニア エンコードのプレビュー版が発表されました。

###  <a name="media-services-v2"></a>Media Services v2  

#### <a name="deprecation-of-media-processors"></a>メディア プロセッサの非推奨化

*Azure Media Indexer* および "*Azure Media Indexer 2 プレビュー*" の廃止を発表します。 提供終了日については、[レガシ コンポーネント](../previous/legacy-components.md)に関する記事を参照してください。 [Azure Media Services Video Indexer](../video-indexer/index.yml) が、これらの従来のメディア プロセッサに取って代わります。

詳細については、[Azure Media Indexer および Azure Media Indexer 2 から Azure Media Services Video Indexer への移行](../previous/migrate-indexer-v1-v2.md)に関する記事をご覧ください。

## <a name="august-2019"></a>2019 年 8 月

###  <a name="media-services-v3"></a>Media Services v3  

#### <a name="south-africa-regional-pair-is-open-for-media-services"></a>南アフリカのリージョン ペアで Media Services を提供開始 

Media Services を南アフリカ北部と南アフリカ西部の各リージョンで利用できるようになりました。

詳細については、「[Media Services v3 が存在するクラウドとリージョン](azure-clouds-regions.md)」を参照してください。

###  <a name="media-services-v2"></a>Media Services v2  

#### <a name="deprecation-of-media-processors"></a>メディア プロセッサの非推奨化

お知らせしているように *Windows Azure Media Encoder* (WAME) と *Azure Media Encoder* (AME) のメディア プロセッサは非推奨となっており、廃止される予定です。 提供終了日については、この[レガシ コンポーネント](../previous/legacy-components.md)に関する記事を参照してください。

詳細については、[WAME から Media Encoder Standard への移行](../previous/migrate-windows-azure-media-encoder.md)と [AME から Media Encoder Standard への移行](../previous/migrate-azure-media-encoder.md)に関するページを参照してください。
 
## <a name="july-2019"></a>2019 年 7 月

### <a name="content-protection"></a>コンテンツの保護

トークン制限で保護されたコンテンツをストリーミングする場合、エンドユーザーはキー配信要求の一部として送信されるトークンを取得する必要があります。 *トークン再生の防止* 機能を使用すると、Media Services のユーザーは、同じトークンを使用してキーまたはライセンスを要求できる回数に制限を設定できます。 詳細については、「[トークン再生の防止](content-protection-overview.md#token-replay-prevention)」を参照してください。

7 月の時点では、プレビュー機能は米国中部と米国中西部でのみ利用できました。

## <a name="june-2019"></a>2019 年 6 月

### <a name="video-subclipping"></a>ビデオのサブクリップ

[ジョブ](/rest/api/media/jobs)を使用してビデオをエンコードする際に、ビデオをトリミングまたはサブクリップできるようになりました。 

この機能は、[BuiltInStandardEncoderPreset](/rest/api/media/transforms/createorupdate#builtinstandardencoderpreset) プリセットまたは [StandardEncoderPreset](/rest/api/media/transforms/createorupdate#standardencoderpreset) プリセットを使用して構築された[変換](/rest/api/media/transforms)で動作します。 

次の例を参照してください。

* [.NET を使用してビデオをサブクリップする](subclip-video-dotnet-howto.md)
* [REST を使用してビデオをサブクリップする](subclip-video-rest-howto.md)

## <a name="may-2019"></a>2019 年 5 月

### <a name="azure-monitor-support-for-media-services-diagnostic-logs-and-metrics"></a>Media Services の診断ログとメトリックに対する Azure Monitor のサポート

Media Services で出力されたテレメトリ データを Azure Monitor を使用して表示できるようになりました。

* Azure Monitor の診断ログを使用して、Media Services のキー配信エンドポイントによって送信された要求を監視します。 
* Media Services の[ストリーミング エンドポイント](streaming-endpoint-concept.md)によって出力されたメトリックを監視します。   

詳細については、「[Media Services のメトリックと診断ログの監視](monitoring/monitor-media-services-data-reference.md)」を参照してください。

### <a name="multi-audio-tracks-support-in-dynamic-packaging"></a>ダイナミック パッケージでの複数のオーディオ トラックのサポート 

複数のコーデックと言語を使用する複数のオーディオ トラックを持つ資産をストリーミングするとき、[ダイナミック パッケージ](dynamic-packaging-overview.md)では、HLS 出力 (バージョン 4 以上) 用の複数のオーディオ トラックがサポートされるようになりました。

### <a name="korea-regional-pair-is-open-for-media-services"></a>韓国のリージョン ペアで Media Services を提供開始 

Media Services が、韓国中部と韓国南部のリージョンで利用できるようになりました。 

詳細については、「[Media Services v3 が存在するクラウドとリージョン](azure-clouds-regions.md)」を参照してください。

### <a name="performance-improvements"></a>パフォーマンスの向上

Media Services のパフォーマンス向上を含む更新が追加されました。

* 処理でサポートされているファイルの最大サイズが更新されました。 [クォータと制限](limits-quotas-constraints.md)に関する記事を参照してください。
* [エンコードの速度の向上](concept-media-reserved-units.md)。

## <a name="april-2019"></a>2019 年 4 月

### <a name="new-presets"></a>新しいプリセット

* [FaceDetectorPreset](/rest/api/media/transforms/createorupdate#facedetectorpreset) が組み込みのアナライザー プリセットに追加されました。
* [ContentAwareEncodingExperimental](/rest/api/media/transforms/createorupdate#encodernamedpreset) が組み込みのエンコーダー プリセットに追加されました。 詳細については、[コンテンツに対応したエンコード](content-aware-encoding.md)に関する記事を参照してください。 

## <a name="march-2019"></a>2019 年 3 月

動的パッケージが Dolby Atmos 対応になりました。 詳細については、「[ダイナミック パッケージによってサポートされているオーディオ コーデック](dynamic-packaging-overview.md#audio-codecs-supported-by-dynamic-packaging)」を参照してください。

資産またはアカウント フィルターの一覧を指定できるようになりました。これはストリーミング ロケーターに適用されます。 詳細については、[フィルターとストリーミング ロケーターの関連付け](filters-concept.md#associating-filters-with-streaming-locator)に関する記事を参照してください。

## <a name="february-2019"></a>2019 年 2 月

Media Services v3 が Azure 国内クラウドでサポートされるようになりました。 まだすべてのクラウドですべての機能が使用できるわけではありません。 詳細については、「[Azure Media Services v3 が存在するクラウドとリージョン](azure-clouds-regions.md)」を参照してください。

[Microsoft.Media.JobOutputProgress](monitoring/media-services-event-schemas.md#monitoring-job-output-progress) イベントが Media Services の Azure Event Grid スキーマに追加されました。

## <a name="january-2019"></a>2019 年 1 月

### <a name="media-encoder-standard-and-mpi-files"></a>Media Encoder Standard および MPI ファイル 

Media Encoder Standard でエンコードして MP4 ファイルを生成すると、新しい .mpi ファイルが生成され、出力アセットに追加されます。 この MPI ファイルの目的は、[ダイナミック パッケージ](dynamic-packaging-overview.md)とストリーミングのシナリオのパフォーマンスを向上させることです。

MPI ファイルを変更または削除したり、サービスにこのようなファイルが存在するかどうかに依存しないようにしてください。

## <a name="december-2018"></a>2018 年 12 月

V3 API の GA リリースの更新プログラム:
       
* **資産フィルター** と **アカウント フィルター** では **PresentationTimeRange** プロパティが '必須' でなくなりました。 
* **Jobs** と **Transforms** のクエリ オプション、$top と $skip が削除され、$orderby が追加されました。 新しい順序付け機能を追加する手順の一部で、実装されていない $top オプションと $skip オプションが、以前は偶発的に公開されていたことが判明しました。
* 列挙型の拡張性が再有効化されました。 この機能はプレビュー版の SDK で有効になりましたが、GA 版で偶発的に無効になりました。
* 定義済みの 2 つのストリーミング ポリシーの名前が変更されました。 **SecureStreaming** が **MultiDrmCencStreaming** になりました。 **SecureStreamingWithFairPlay** が **Predefined_MultiDrmStreaming** になりました。

## <a name="november-2018"></a>2018 年 11 月

CLI 2.0 モジュールが、[Azure Media Services v3 GA](/cli/azure/ams) – v 2.0.50 で使用できるようになりました。

### <a name="new-commands"></a>新しいコマンド

- [az ams account](/cli/azure/ams/account)
- [az ams account-filter](/cli/azure/ams/account-filter)
- [az ams asset](/cli/azure/ams/asset)
- [az ams asset-filter](/cli/azure/ams/asset-filter)
- [az ams content-key-policy](/cli/azure/ams/content-key-policy)
- [az ams job](/cli/azure/ams/job)
- [az ams live-event](/cli/azure/ams/live-event)
- [az ams live-output](/cli/azure/ams/live-output)
- [az ams streaming-endpoint](/cli/azure/ams/streaming-endpoint)
- [az ams streaming-locator](/cli/azure/ams/streaming-locator)
- [az ams account mru](/cli/azure/ams/account/mru) - メディア占有ユニットを管理できます。 詳細については、「[メディア占有ユニットをスケーリングする](media-reserved-units-cli-how-to.md)」を参照してください。

### <a name="new-features-and-breaking-changes"></a>新機能と重大な変更

#### <a name="asset-commands"></a>アセット コマンド

- ```--storage-account``` および ```--container``` 引数が追加されました。
- ```az ams asset get-sas-url``` コマンドに有効期限 (現在 + 23 時間) とアクセス許可 (読み取り) の既定値が追加されました。

#### <a name="job-commands"></a>ジョブ コマンド

- ```--correlation-data``` および ```--label``` 引数が追加されました
- ```--output-asset-names``` が ```--output-assets``` に変更されました。 "assetName=label" 形式の、スペース区切りのアセットのリストを受け入れるようになりました。 "assetName=" のようなラベルのないアセットを送信できます。

#### <a name="streaming-locator-commands"></a>ストリーミング ロケーター コマンド

- ```az ams streaming locator``` ベース コマンドが ```az ams streaming-locator``` に置き換えられました。
- ```--streaming-locator-id``` および ```--alternative-media-id support``` 引数が追加されました。
- ```--content-keys argument``` 引数が更新されました。
- ```--content-policy-name``` が ```--content-key-policy-name``` に変更されました。

#### <a name="streaming-policy-commands"></a>ストリーミング ポリシー コマンド

- ```az ams streaming policy``` ベース コマンドが ```az ams streaming-policy``` に置き換えられました。
- ```az ams streaming-policy create``` での暗号化パラメーターのサポートが追加されました。

#### <a name="transform-commands"></a>変換コマンド

- ```--preset-names``` 引数が ```--preset``` に置き換えられました。 現在同時に設定できるのは、1 つの出力/プリセットのみです (さらに追加するには ```az ams transform output add``` を実行する必要があります)。 また、カスタムの JSON にパスを渡すことで、カスタム StandardEncoderPreset を設定することもできます。
- ```az ams transform output remove``` は、削除する出力インデックスを渡すことで実行できます。
- ```--relative-priority, --on-error, --audio-language and --insights-to-extract``` 引数が、```az ams transform create``` および ```az ams transform output add``` コマンドに追加されました。

## <a name="october-2018---ga"></a>2018 年 10 月 - GA

このセクションでは、Azure Media Services (AMS) の 10 月の更新について説明します。

### <a name="rest-v3-ga-release"></a>REST v3 GA リリース

[REST v3 GA リリース](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/mediaservices/resource-manager/Microsoft.Media/stable/2018-07-01)には、ライブの追加の API、アカウント/アセット レベルのマニフェスト フィルター、および DRM サポートが含まれています。

#### <a name="azure-resource-management"></a>Azure Resource Management 

Azure Resource Management のサポートにより、管理と操作の API が統一されます (現在はすべて一か所にあります)。

このリリース移行では、Resource Manager テンプレートを使用して、ライブ イベントを作成できます。

#### <a name="improvement-of-asset-operations"></a>アセット操作の向上 

次の機能強化が導入されています。

- HTTP(s) URL または Azure Blob Storage SAS URL からの取り込み。
- アセットで独自のコンテナー名の指定。 
- Azure Functions を使用してカスタム ワークフローを作成するための簡単な出力サポート。

#### <a name="new-transform-object"></a>新しい変換オブジェクト

新しい **変換** オブジェクトによってエンコード モデルが簡略化されます。 この新しいオブジェクトを使用すると、Resource Manager テンプレートおよびプリセットの作成と共有が簡単になります。 

#### <a name="azure-active-directory-authentication-and-azure-rbac"></a>Azure Active Directory 認証と Azure RBAC

Azure AD Authentication と Azure ロールベースのアクセス制御 (Azure RBAC) によって、Azure AD のロールまたはユーザーごとのセキュアな変換、LiveEvent、コンテンツ キー ポリシー、またはアセットが実現します。

#### <a name="client-sdks"></a>クライアント SDK  

Media Services v3 でサポートされる言語: .NET Core、Java、Node.js、Ruby、Typescript、Python、Go。

#### <a name="live-encoding-updates"></a>ライブ エンコードの更新

ライブ エンコードには次の更新が導入されています。

- ライブの新しい低待機時間モード (エンド ツー エンドで 10 秒)。
- RTMP サポートの強化 (安定性の向上およびソース エンコーダー サポートの強化)。
- RTMPS のセキュアな取り込み。

    ライブ イベントを作成すると、4 つの取り込み URL が取得されるようになりました。 4 つの取り込み URL はほとんど同じで。ストリーミング トークン (AppId) は同じですが、ポート番号の部分のみが異なります。 URL のうち 2 つは RTMPS のプライマリとバックアップです。 
- 24 時間のコード変換サポート。 
- SCTE35 経由の RTMP での広告シグナル サポートの強化。

#### <a name="improved-event-grid-support"></a>Event Grid サポートの強化

Event Grid のサポートが次のように強化されています。

- Logic Apps と Azure Functions を使用した開発を容易にするための Azure Event Grid の統合。 
- エンコーディングやライブ チャンネルなどでイベントをサブスクライブ。

### <a name="cmaf-support"></a>CMAF のサポート

CMAF と 'cbcs' 暗号化のサポート。Apple HLS (iOS 11+) および、CMAF をサポートする MPEG-DASH プレーヤーに対応します。

### <a name="video-indexer"></a>Video Indexer

Video Indexer GA のリリースは 8 月に発表されました。 現在サポートされている機能に関する新しい情報については、「[Video Indexer とは](../video-indexer/video-indexer-overview.md?bc=/azure/media-services/video-indexer/breadcrumb/toc.json&toc=/azure/media-services/video-indexer/toc.json)」をご覧ください。 

### <a name="plans-for-changes"></a>変更の計画

#### <a name="azure-cli-20"></a>Azure CLI 2.0
 
すべての機能 (ライブ、コンテンツ キー ポリシー、アカウント/アセット フィルター、ストリーミング ポリシーなど) の操作を含む Azure CLI 2.0 モジュールは近日公開されます。 

### <a name="known-issues"></a>既知の問題

アセットまたはアカウント フィルターのプレビュー API を使用していた顧客のみが、次の問題によって影響を受けます。

Media Services v3 CLI または API を使用して 9 月 28 日から 10 月 12 日までにアセットまたはアカウント フィルターを作成した場合、バージョンが競合するため、すべてのアセットとアカウント フィルターを削除してから再作成する必要があります。 

## <a name="may-2018---preview"></a>2018 年 5 月 - プレビュー

### <a name="net-sdk"></a>.NET SDK

.NET SDK には次の機能があります。

* **Transforms** と **Job**: メディア コンテンツをエンコードまたは分析します。 例については、「[ファイルのストリーミング](stream-files-tutorial-with-api.md)」と「[分析](analyze-videos-tutorial-with-api.md)」を参照してください。
* **ストリーミング ロケーター**: コンテンツをエンドユーザー デバイスに公開およびストリーミングします。
* **ストリーミング ポリシー** および **コンテンツ キー ポリシー**: コンテンツ配信時のキーの配信とコンテンツ保護 (DRM) を構成します。
* **ライブ イベント** および **ライブ出力**: ライブ ストリーミング コンテンツの取り込みとアーカイブを構成します。
* **Asset**: メディア コンテンツの Azure Storage への格納と発行を行います。 
* **ストリーミング エンドポイント**: ダイナミック パッケージ、暗号化、およびライブとオンデマンドの両方のメディア コンテンツのストリーミングを構成およびスケーリングします。

### <a name="known-issues"></a>既知の問題

* ジョブを送信するときに、Azure Blob storage 内にあるファイルへの HTTPS URL、SAS の URL またはパスを使用して、ソース ビデオを取り込むように指定できます。 現在、Media Services v3 では HTTPS URL を介したチャンク転送エンコードはサポートされていません。

## <a name="ask-questions-give-feedback-get-updates"></a>質問、フィードバックの送信、最新情報の入手

「[Azure Media Services community (Azure Media Services コミュニティ)](media-services-community.md)」を参照して、さまざまな質問の方法、フィードバックする方法、Media Services に関する最新情報の入手方法を確認してください。

## <a name="see-also"></a>関連項目

[Media Services v2 から v3 への移行のガイダンス](migrate-v-2-v-3-migration-introduction.md)

## <a name="next-steps"></a>次のステップ

- [概要](media-services-overview.md)
- [Media Services v2 リリース ノート](../previous/media-services-release-notes.md)
