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
ms.date: 06/07/2019
ms.author: juliako
ms.openlocfilehash: b3e772ebb05f79abb70e58e63a93c3336a413e38
ms.sourcegitcommit: 5bdd50e769a4d50ccb89e135cfd38b788ade594d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/03/2019
ms.locfileid: "67542551"
---
# <a name="azure-media-services-v3-release-notes"></a>Azure Media Services v3 リリース ノート

常に最新の開発情報を把握していただけるよう、この記事では以下に関する情報を提供します。

* 最新のリリース
* 既知の問題
* バグの修正
* 非推奨の機能

## <a name="known-issues"></a>既知の問題

> [!NOTE]
> 現時点では、Azure portal を使用して v3 リソースを管理することはできません。 [REST API](https://aka.ms/ams-v3-rest-sdk)、CLI、またはサポートされている SDK のいずれかを使用します。

詳細については、「[Media Services v2 から v3 への移行のガイダンス](migrate-from-v2-to-v3.md#known-issues)」を参照してください。

## <a name="june-2019"></a>2019 年 6 月

### <a name="video-subclipping"></a>ビデオのサブクリップ

[ジョブ](https://docs.microsoft.com/rest/api/media/jobs)を使用してビデオをエンコードする際に、ビデオをトリミングまたはサブクリップできるようになりました。 

この機能は、[BuiltInStandardEncoderPreset](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#builtinstandardencoderpreset) プリセットまたは [StandardEncoderPreset](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#standardencoderpreset) プリセットを使用して構築された[変換](https://docs.microsoft.com/rest/api/media/transforms)で動作します。 

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

* [FaceDetectorPreset](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#facedetectorpreset) が組み込みのアナライザー プリセットに追加されました。
* [ContentAwareEncodingExperimental](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#encodernamedpreset) が組み込みのエンコーダー プリセットに追加されました。 詳細については、[コンテンツに対応したエンコード](cae-experimental.md)に関する記事を参照してください。 

## <a name="march-2019"></a>2019 年 3 月

動的パッケージが Dolby Atmos 対応になりました。 詳細については、「[ダイナミック パッケージによってサポートされているオーディオ コーデック](dynamic-packaging-overview.md#audio-codecs)」を参照してください。

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

CLI 2.0 モジュールが、[Azure Media Services v3 GA](https://docs.microsoft.com/cli/azure/ams?view=azure-cli-latest) – v 2.0.50 で使用できるようになりました。

### <a name="new-commands"></a>新しいコマンド

- [az ams account](https://docs.microsoft.com/cli/azure/ams/account?view=azure-cli-latest)
- [az ams account-filter](https://docs.microsoft.com/cli/azure/ams/account-filter?view=azure-cli-latest)
- [az ams asset](https://docs.microsoft.com/cli/azure/ams/asset?view=azure-cli-latest)
- [az ams asset-filter](https://docs.microsoft.com/cli/azure/ams/asset-filter?view=azure-cli-latest)
- [az ams content-key-policy](https://docs.microsoft.com/cli/azure/ams/content-key-policy?view=azure-cli-latest)
- [az ams job](https://docs.microsoft.com/cli/azure/ams/job?view=azure-cli-latest)
- [az ams live-event](https://docs.microsoft.com/cli/azure/ams/live-event?view=azure-cli-latest)
- [az ams live-output](https://docs.microsoft.com/cli/azure/ams/live-output?view=azure-cli-latest)
- [az ams streaming-endpoint](https://docs.microsoft.com/cli/azure/ams/streaming-endpoint?view=azure-cli-latest)
- [az ams streaming-locator](https://docs.microsoft.com/cli/azure/ams/streaming-locator?view=azure-cli-latest)
- [az ams account mru](https://docs.microsoft.com/cli/azure/ams/account/mru?view=azure-cli-latest) - メディア占有ユニットを管理できます。 詳細については、「[メディア占有ユニットをスケーリングする](media-reserved-units-cli-how-to.md)」を参照してください。

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

Video Indexer GA のリリースは 8 月に発表されました。 現在サポートされている機能に関する新しい情報については、「[Video Indexer とは](../../cognitive-services/video-indexer/video-indexer-overview.md?toc=/azure/media-services/video-indexer/toc.json&bc=/azure/media-services/video-indexer/breadcrumb/toc.json)」をご覧ください。 

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

* ジョブを送信するときに、Azure Blob storage 内にあるファイルへの HTTPS URL、SAS の URL またはパスを使用して、ソース ビデオを取り込むように指定できます。 現時点では、AMS v3 には、HTTPS URL 経由でチャンク転送エンコード処理がサポートされていません。

## <a name="ask-questions-give-feedback-get-updates"></a>質問、フィードバックの送信、最新情報の入手

「[Azure Media Services community (Azure Media Services コミュニティ)](media-services-community.md)」を参照して、さまざまな質問の方法、フィードバックする方法、Media Services に関する最新情報の入手方法を確認してください。

## <a name="next-steps"></a>次の手順

[概要](media-services-overview.md)
