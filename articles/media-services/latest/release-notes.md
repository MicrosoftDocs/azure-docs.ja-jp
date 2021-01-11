---
title: Azure Media Services v3 リリース ノート | Microsoft Docs
description: 常に最新の開発情報を把握していただけるよう、この記事では Azure Media Services v3 の最新の更新情報を提供します。
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: na
ms.topic: article
ms.date: 06/03/2020
ms.author: juliako
ms.openlocfilehash: 53e337cf4ccbabf7f0b7a227632ba5e996e2b4f3
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/23/2020
ms.locfileid: "87072138"
---
# <a name="azure-media-services-v3-release-notes"></a>Azure Media Services v3 リリース ノート

>URL `https://docs.microsoft.com/api/search/rss?search=%22Azure+Media+Services+v3+release+notes%22&locale=en-us` をコピーして、お使いの RSS フィード リーダーに貼り付け、更新内容を確認するためにこのページに再度アクセスするタイミングに関する通知を受け取るようにしてください。

常に最新の開発情報を把握していただけるよう、この記事では以下に関する情報を提供します。

* 最新のリリース
* 既知の問題
* バグの修正
* 非推奨の機能

## <a name="known-issues"></a>既知の問題

> [!NOTE]
> [Azure portal](https://portal.azure.com/) を使用して、v3 の[ライブ イベント](live-events-outputs-concept.md)の管理、v3 の[資産](assets-concept.md)とジョブの表示、API へのアクセスに関する情報の取得、コンテンツの暗号化を行うことができます。 他のすべての管理タスク (変換とジョブの管理など) については、[REST API](https://aka.ms/ams-v3-rest-ref)、[CLI](https://aka.ms/ams-v3-cli-ref)、またはサポートされているいずれかの [SDK](media-services-apis-overview.md#sdks) を使用します。
>
> 詳細については、[Media Services v3 に関する Azure portal の制限事項](frequently-asked-questions.md#what-are-the-azure-portal-limitations-for-media-services-v3)に関する記事を参照してください。

## <a name="july-2020"></a>2020 年 7 月

### <a name="live-transcriptions"></a>ライブ文字起こし

ライブ文字起こしで 19 の言語と 8 つの地域がサポートされるようになりました。

## <a name="protecting-your-content-with-media-services-and-azure-ad"></a>Media Services と Azure AD によるコンテンツの保護

「[Azure AD を使用したエンド ツー エンドのコンテンツ保護](./azure-ad-content-protection.md)」というタイトルのチュートリアルを公開しました。

### <a name="high-availablity"></a>高可用性

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

Media Services が次の Azure Government リージョンで一般公開されました。*米国政府アリゾナ*および*米国政府テキサス*。

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

*Azure Media Indexer* および "*Azure Media Indexer 2 プレビュー*" の廃止を発表します。 提供終了日については、[レガシ コンポーネント](../previous/legacy-components.md)に関するトピックを参照してください。 [Azure Media Services Video Indexer](../video-indexer/index.yml) が、これらの従来のメディア プロセッサに取って代わります。

詳細については、[Azure Media Indexer および Azure Media Indexer 2 から Azure Media Services Video Indexer への移行](../previous/migrate-indexer-v1-v2.md)に関する記事をご覧ください。

## <a name="august-2019"></a>2019 年 8 月

###  <a name="media-services-v3"></a>Media Services v3  

#### <a name="south-africa-regional-pair-is-open-for-media-services"></a>南アフリカのリージョン ペアで Media Services を提供開始 

Media Services を南アフリカ北部と南アフリカ西部の各リージョンで利用できるようになりました。

詳細については、「[Media Services v3 が存在するクラウドとリージョン](azure-clouds-regions.md)」を参照してください。

###  <a name="media-services-v2"></a>Media Services v2  

#### <a name="deprecation-of-media-processors"></a>メディア プロセッサの非推奨化

お知らせしているように *Windows Azure Media Encoder* (WAME) と *Azure Media Encoder* (AME) のメディア プロセッサは非推奨となっており、廃止される予定です。 提供終了日については、この[レガシ コンポーネント](../previous/legacy-components.md)に関するトピックを参照してください。

詳細については、[WAME から Media Encoder Standard への移行](https://go.microsoft.com/fwlink/?LinkId=2101334)と [AME から Media Encoder Standard への移行](https://go.microsoft.com/fwlink/?LinkId=2101335)に関するページを参照してください。
 
## <a name="july-2019"></a>2019 年 7 月

### <a name="content-protection"></a>コンテンツの保護

トークン制限で保護されたコンテンツをストリーミングする場合、エンドユーザーはキー配信要求の一部として送信されるトークンを取得する必要があります。 *トークン再生の防止*機能を使用すると、Media Services のユーザーは、同じトークンを使用してキーまたはライセンスを要求できる回数に制限を設定できます。 詳細については、「[トークン再生の防止](content-protection-overview.md#token-replay-prevention)」を参照してください。

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

詳細については、「[Media Services のメトリックと診断ログの監視](media-services-metrics-diagnostic-logs.md)」を参照してください。

### <a name="multi-audio-tracks-support-in-dynamic-packaging"></a>ダイナミック パッケージでの複数のオーディオ トラックのサポート 

複数のコーデックと言語を使用する複数のオーディオ トラックを持つ資産をストリーミングするとき、[ダイナミック パッケージ](dynamic-packaging-overview.md)では、HLS 出力 (バージョン 4 以上) 用の複数のオーディオ トラックがサポートされるようになりました。

### <a name="korea-regional-pair-is-open-for-media-services"></a>韓国のリージョン ペアで Media Services を提供開始 

Media Services が、韓国中部と韓国南部のリージョンで利用できるようになりました。 

詳細については、「[Media Services v3 が存在するクラウドとリージョン](azure-clouds-regions.md)」を参照してください。

### <a name="performance-improvements"></a>パフォーマンスの向上

Media Services のパフォーマンス向上を含む更新が追加されました。

* 処理でサポートされているファイルの最大サイズが更新されました。 [クォータと制限](limits-quotas-constraints.md)に関する記事を参照してください。
* [エンコードの速度の向上](media-reserved-units-cli-how-to.md#choosing-between-different-reserved-unit-types)。

## <a name="april-2019"></a>2019 年 4 月

### <a name="new-presets"></a>新しいプリセット

* [FaceDetectorPreset](/rest/api/media/transforms/createorupdate#facedetectorpreset) が組み込みのアナライザー プリセットに追加されました。
* [ContentAwareEncodingExperimental](/rest/api/media/transforms/createorupdate#encodernamedpreset) が組み込みのエンコーダー プリセットに追加されました。 詳細については、[コンテンツに対応したエンコード](content-aware-encoding.md)に関する記事を参照してください。 

## <a name="march-2019"></a>2019 年 3 月

動的パッケージが Dolby Atmos 対応になりました。 詳細については、「[ダイナミック パッケージによってサポートされているオーディオ コーデック](dynamic-packaging-overview.md#audio-codecs-supported-by-dynamic-packaging)」を参照してください。

資産またはアカウント フィルターの一覧を指定できるようになりました。これはストリーミング ロケーターに適用されます。 詳細については、[フィルターとストリーミング ロケーターの関連付け](filters-concept.md#associating-filters-with-streaming-locator)に関する記事を参照してください。

## <a name="february-2019"></a>2019 年 2 月

Media Services v3 が Azure 国内クラウドでサポートされるようになりました。 まだすべてのクラウドですべての機能が使用できるわけではありません。 詳細については、「[Azure Media Services v3 が存在するクラウドとリージョン](azure-clouds-regions.md)」を参照してください。

[Microsoft.Media.JobOutputProgress](media-services-event-schemas.md#monitoring-job-output-progress) イベントが Media Services の Azure Event Grid スキーマに追加されました。

## <a name="january-2019"></a>2019 年 1 月

### <a name="media-encoder-standard-and-mpi-files"></a>Media Encoder Standard および MPI ファイル 

Media Encoder Standard でエンコードして MP4 ファイルを生成すると、新しい .mpi ファイルが生成され、出力アセットに追加されます。 この MPI ファイルの目的は、[ダイナミック パッケージ](dynamic-packaging-overview.md)とストリーミングのシナリオのパフォーマンスを向上させることです。

MPI ファイルを変更または削除したり、サービスにこのようなファイルが存在するかどうかに依存しないようにしてください。

## <a name="december-2018"></a>2018 年 12 月

V3 API の GA リリースの更新プログラム:
       
* **資産フィルター**と**アカウント フィルター**では **PresentationTimeRange** プロパティが '必須' でなくなりました。 
* **Jobs** と **Transforms** のクエリ オプション、$top と $skip が削除され、$orderby が追加されました。 新しい順序付け機能を追加する手順の一部で、実装されていない $top オプションと $skip オプションが、以前は偶発的に公開されていたことが判明しました。
* 列挙型の拡張性が再有効化されました。 この機能はプレビュー版の SDK で有効になりましたが、GA 版で偶発的に無効になりました。
* 定義済みの 2 つのストリーミング ポリシーの名前が変更されました。 **SecureStreaming** が **MultiDrmCencStreaming** になりました。 **SecureStreamingWithFairPlay** が **Predefined_MultiDrmStreaming** になりました。

## <a name="november-2018"></a>2018 年 11 月

CLI 2.0 モジュールが、[Azure Media Services v3 GA](/cli/azure/ams?view=azure-cli-latest) – v 2.0.50 で使用できるようになりました。

### <a name="new-commands"></a>新しいコマンド

- [az ams account](/cli/azure/ams/account?view=azure-cli-latest)
- [az ams account-filter](/cli/azure/ams/account-filter?view=azure-cli-latest)
- [az ams asset](/cli/azure/ams/asset?view=azure-cli-latest)
- [az ams asset-filter](/cli/azure/ams/asset-filter?view=azure-cli-latest)
- [az ams content-key-policy](/cli/azure/ams/content-key-policy?view=azure-cli-latest)
- [az ams job](/cli/azure/ams/job?view=azure-cli-latest)
- [az ams live-event](/cli/azure/ams/live-event?view=azure-cli-latest)
- [az ams live-output](/cli/azure/ams/live-output?view=azure-cli-latest)
- [az ams streaming-endpoint](/cli/azure/ams/streaming-endpoint?view=azure-cli-latest)
- [az ams streaming-locator](/cli/azure/ams/streaming-locator?view=azure-cli-latest)
- [az ams account mru](/cli/azure/ams/account/mru?view=azure-cli-latest) - メディア占有ユニットを管理できます。 詳細については、「[メディア占有ユニットをスケーリングする](media-reserved-units-cli-how-to.md)」を参照してください。

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

新しい**変換**オブジェクトによってエンコード モデルが簡略化されます。 この新しいオブジェクトを使用すると、Resource Manager テンプレートおよびプリセットの作成と共有が簡単になります。 

#### <a name="azure-active-directory-authentication-and-rbac"></a>Azure Active Directory 認証と RBAC

Azure AD Authentication とロールベースのアクセス制御 (RBAC) によって、Azure AD のロールまたはユーザーごとのセキュアな変換、LiveEvent、コンテンツ キー ポリシー、またはアセットが実現します。

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
* **ストリーミング ポリシー**および**コンテンツ キー ポリシー**: コンテンツ配信時のキーの配信とコンテンツ保護 (DRM) を構成します。
* **ライブ イベント**および**ライブ出力**: ライブ ストリーミング コンテンツの取り込みとアーカイブを構成します。
* **Asset**: メディア コンテンツの Azure Storage への格納と発行を行います。 
* **ストリーミング エンドポイント**: ダイナミック パッケージ、暗号化、およびライブとオンデマンドの両方のメディア コンテンツのストリーミングを構成およびスケーリングします。

### <a name="known-issues"></a>既知の問題

* ジョブを送信するときに、Azure Blob storage 内にあるファイルへの HTTPS URL、SAS の URL またはパスを使用して、ソース ビデオを取り込むように指定できます。 現在、Media Services v3 では HTTPS URL を介したチャンク転送エンコードはサポートされていません。

## <a name="ask-questions-give-feedback-get-updates"></a>質問、フィードバックの送信、最新情報の入手

「[Azure Media Services community (Azure Media Services コミュニティ)](media-services-community.md)」を参照して、さまざまな質問の方法、フィードバックする方法、Media Services に関する最新情報の入手方法を確認してください。

## <a name="see-also"></a>関連項目

[Media Services v2 から v3 への移行のガイダンス](migrate-from-v2-to-v3.md#known-issues)

## <a name="next-steps"></a>次のステップ

- [概要](media-services-overview.md)
- [Media Services v3 ドキュメントの更新](docs-release-notes.md)
- [Media Services v2 リリース ノート](../previous/media-services-release-notes.md)
