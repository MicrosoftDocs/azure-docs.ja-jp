---
title: "Media Services リリース ノート | Microsoft Docs"
description: "Media Services リリース ノート"
services: media-services
documentationcenter: 
author: Juliako
manager: erikre
editor: 
ms.assetid: 3ca2d7af-1cf0-45fa-9585-3b73f3ee057d
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: media
ms.devlang: dotnet
ms.topic: article
ms.date: 03/02/2017
ms.author: juliako
translationtype: Human Translation
ms.sourcegitcommit: cea53acc33347b9e6178645f225770936788f807
ms.openlocfilehash: 353677bc7eb7fe791d23bcfdb79f3a0df6366c6f
ms.lasthandoff: 03/03/2017


---
# <a name="azure-media-services-release-notes"></a>Azure Media Services リリース ノート
このリリース ノートには、以前のリリースからの変更と既知の問題が要約されています。

> [!NOTE]
> 製品に関するご意見、ご要望をお寄せください。お客様に影響する問題の修正に尽力しています。 問題の報告または質問を行うには、[Azure Media Services MSDN フォーラム]に投稿してください。
> 
> 

## <a id="issues"></a>現在の既知の問題
### <a id="general_issues"></a>Media Services の全般的な問題
| 問題 | Description |
| --- | --- |
| REST API で一般的な HTTP ヘッダーがいくつか提供されていない。 |REST API を使用して Media Services アプリケーションを開発している場合、いくつかの一般的な HTTP フィールド (CLIENT-REQUEST-ID、REQUEST-ID、および RETURN-CLIENT-REQUEST-ID を含む) がサポートされていないことに気付きます。 ヘッダーは、今後の更新プログラムで追加される予定です。 |
| パーセントエンコーディングは利用できません。 |Media Services は、ストリーミング コンテンツ (たとえば、http://{AMSAccount}.origin.mediaservices.windows.net/{GUID}/{IAssetFile.Name}/streamingParameters) の URL を構築する際に、IAssetFile.Name プロパティの値を使用します。このため、パーセントエンコーディングは利用できません。 **Name** プロパティの値には、[パーセント エンコーディング予約文字](http://en.wikipedia.org/wiki/Percent-encoding#Percent-encoding_reserved_characters) (!*'();:@&=+$,/?%#[]") は使用できません。 "." は&amp;1; つのみです。 また、ファイル名拡張子で使用できる "." は&1; つのみです。 |
| Azure Storage SDK Version 3.x に含まれる ListBlobs メソッドが失敗する。 |Media Services は、 [2012-02-12](https://docs.microsoft.com/rest/api/storageservices/fileservices/Version-2012-02-12) バージョンに基づいて SAS URL を生成します。 Azure Storage SDK を使用して、BLOB コンテナー内の BLOB を一覧する場合は、Azure Storage SDK Version 2.x に含まれる [CloudBlobContainer.ListBlobs](http://msdn.microsoft.com/library/microsoft.windowsazure.storage.blob.cloudblobcontainer.listblobs.aspx) メソッドを使用してください。 Azure Storage SDK Version 3.x に含まれる ListBlobs メソッドは失敗します。 |
| Media Services 調整メカニズムが、サービスに対して過剰な要求を作成するアプリケーションのリソース使用を制限する。 サービスが「サービスを利用できません」(503) HTTP 状態コードを返すことがある。 |詳細については、「 [Azure Media Services エラー コード](media-services-encoding-error-codes.md) 」の 503 HTTP 状態コードの説明を参照してください。 |
| パブリック REST v2 では、クエリ結果が 1000 件に制限されているため、エンティティを照会するときには、一度に返されるエンティティが 1000 個に制限されます。 |[この .NET の例](media-services-dotnet-manage-entities.md#enumerating-through-large-collections-of-entities)と[この REST API の例](media-services-rest-manage-entities.md#enumerating-through-large-collections-of-entities)に示すように、**Skip** および **Take** (.NET)/**top** (REST) を使用する必要があります。 |
| 一部のクライアントは、スムーズ ストリーミング マニフェストで繰り返しタグに遭遇することがあります。 |詳細については、 [こちらの](media-services-deliver-content-overview.md#known-issues) セクションを参照してください。 |
| Azure Media Services .NET SDK オブジェクトをシリアル化できず、その結果、Azure Cache と連携動作しません。 |SDK AssetCollection オブジェクトをシリアル化して、Azure Cache に追加しようとすると、例外がスローされます。 |
| エンコード ジョブが、メッセージ文字列 "段階: DownloadFile. コード: System.NullReferenceException" で失敗します。 |一般的なエンコード ワークフローでは、入力ビデオ ファイルを入力資産にアップロードし、その入力資産の&1; つまたは複数のエンコード ジョブを送信します。その入力資産がさらに変更されることはありません。 ただし、(たとえば、資産内のファイルの追加/削除/名前変更のために) 入力資産を変更した場合は、これ以降のジョブが DownloadFile エラーで失敗する可能性があります。 これを回避するには、入力資産を削除して、新しい資産に入力ファイルを再度アップロードしてください。 |

## <a id="rest_version_history"></a>REST API バージョン履歴
Media Services REST API バージョン履歴の詳細については、「 [Azure Media Services REST API リファレンス]」を参照してください。

## <a name="march-2017-release"></a>2017 年 3 月のリリース

Azure Media Standard を使って、エンコード タスクを作成するときに "アダプティブ ストリーミング" プリセット文字列を指定することにより、[ビットレート ラダーを自動生成](media-services-autogen-bitrate-ladder-with-mes.md)できるようになりました。 Media Services でのストリーミング用にビデオをエンコードする場合は、"アダプティブ ストリーミング" が推奨されるプリセットです。 特定のシナリオ向けにエンコード プリセットをカスタマイズする必要がある場合は、[これら](media-services-mes-presets-overview.md)のプリセットで始めることができます。

Azure Media Standard またはメディア エンコーダー Premium ワークフローを使って、[fMP4 チャンクを生成するエンコード タスクを作成](media-services-generate-fmp4-chunks.md)できるようになりました。 


## <a name="febuary-2017-release"></a>2017 年 2 月のリリース

2017 年 4 月 1 日からは、レコードの合計数が最大クォータより小さい場合でも、アカウント内の 90 日前より古いすべてのジョブ レコードが、関連付けられているタスク レコードと共に自動的に削除されます。 ジョブやタスクの情報をアーカイブする必要がある場合は、[ここ](media-services-dotnet-manage-entities.md)で説明されているコードを使用できます。

## <a name="january-2017-release"></a>2017 年 1 月のリリース

Microsoft Azure Media Services (AMS) では、**ストリーミング エンドポイント**は、コンテンツをクライアント プレーヤー アプリケーションや、再配布のための Content Delivery Network (CDN) に直接配信するストリーミング サービスを表します。 Media Services は、シームレスな Azure CDN 統合もサポートしています。 StreamingEndpoint サービスからの送信ストリームには、ライブ ストリーム、ビデオ オンデマンド、または Media Services アカウントの資産のプログレッシブ ダウンロードを使用します。 各 Azure Media Services アカウントには、既定の StreamingEndpoint が含まれています。 追加の StreamingEndpoint をアカウントで作成できます。 StreamingEndpoint には、1.0 および 2.0 の 2 つのバージョンがあります。 2017 年 1 月 10 日から、新しく作成された AMS アカウントには、バージョン 2.0 が**既定**の StreamingEndpoint として含まれます。 このアカウントに追加する追加のストリーミング エンドポイントも、バージョン 2.0 になります。 この変更は、既存のアカウントに影響しません。既存の Streamingendpoint はバージョン 1.0 になり、バージョン 2.0 にアップグレードすることができます。 この変更により、動作、課金および機能に変更があります (詳しくは、[こちら](media-services-streaming-endpoints-overview.md)のトピックをご覧ください)。

さらに、2.15 バージョン以降の Azure Media Services では、ストリーミング エンドポイントのエンティティに次のプロパティが追加されました。**CdnProvider**、**CdnProfile**、**FreeTrialEndTime**、**StreamingEndpointVersion**。 これらのプロパティについて詳しくは、[こちら](https://docs.microsoft.com/rest/api/media/operations/streamingendpoint)をご覧ください。 

## <a name="december-2016-release"></a>2016 年 12 月のリリース

Azure Media Services を使用して、サービスのテレメトリ/メトリック データにアクセスできるようになりました。 現在のバージョンの AMS では、ライブ チャネル エンティティ、ストリーミング ポイント エンティ、およびライブ アーカイブ エンティティのテレメトリ データを取得できます。 詳細については、 [こちらの](media-services-telemetry-overview.md) トピックを参照してください。

## <a id="july_changes16"></a>2016 年 7 月のリリース
### <a name="updates-to-manifest-file-ism-generated-by-encoding-tasks"></a>エンコード タスクによって生成されたマニフェスト ファイル (* ISM) への更新
エンコード タスクが Media Encoder Standard または Azure Media Encoder に送信されると、そのエンコード タスクによって、 [ストリーミング マニフェスト ファイル](media-services-deliver-content-overview.md) (* .ism) が出力アセットに生成されます。 最新のサービス リリースでは、このストリーミング マニフェスト ファイルの構文が更新されました。

> [!NOTE]
> ストリーミング マニフェスト (.ism) ファイルの構文は、内部使用のため予約されており、今後のリリースで変更されます。 このファイルのコンテンツは変更または操作しないでください。
> 
> 

### <a name="a-new-client-manifest-ismc-file-is-generated-in-the-output-asset-when-an-encoding-task-outputs-one-or-more-mp4-files"></a>エンコード タスクが&1; つ以上の MP4 ファイルを出力すると、新しいクライアント マニフェスト (*.ISMC) ファイルが出力アセットに生成される
最新のサービス リリース以降、1 つ以上の MP4 ファイルを生成するエンコード タスクが完了すると、出力アセットにも、ストリーミング クライアント マニフェスト (*.ismc) ファイルが追加されます。 この .ismc ファイルは、動的ストリーミングのパフォーマンス向上に役立ちます。 

> [!NOTE]
> クライアント マニフェスト (.ismc) ファイルの構文は、内部使用のため予約されており、今後のリリースで変更されます。 このファイルのコンテンツは変更または操作しないでください。
> 
> 

詳細については、 [こちら](https://blogs.msdn.microsoft.com/randomnumber/2016/07/08/encoder-changes-within-azure-media-services-now-create-ismc-file/) のブログをご覧ください。

### <a name="known-issues"></a>既知の問題
一部のクライアントは、スムーズ ストリーミング マニフェストで繰り返しタグに遭遇することがあります。 詳細については、 [こちらの](media-services-deliver-content-overview.md#known-issues) セクションを参照してください。

## <a id="apr_changes16"></a>2016 年 4 月のリリース
### <a name="azure-media-analytics"></a>Azure Media Analytics
Azure Media Services に強力なビデオ インテリジェンスとして Azure Media Analytics が導入されました。 詳細については、「 [Azure Media Services Analytics の概要](media-services-analytics-overview.md)」をご覧ください。

### <a name="apple-fairplay-preview"></a>Apple FairPlay (プレビュー)
Azure Media Services では、Apple FairPlay を使用して HTTP ライブ ストリーミング (HLS) コンテンツを動的に暗号化できるようになりました。 また、AMS ライセンス配信サービスを使用して FairPlay ライセンスをクライアントに配信することもできます。 詳細については、「 [Azure Media Services を使用して Apple FairPlay で保護された HLS コンテンツをストリーミングする ](media-services-protect-hls-with-fairplay.md)」をご覧ください。

## <a id="feb_changes16"></a>2016 年 2 月のリリース
最新バージョンの Azure Media Services SDK for .NET (3.5.3) には、Widevine 関連のバグ修正が含まれています。 Widevine で暗号化された複数の資産で AssetDeliveryPolicy を再利用できないという問題がありました。 このバグ修正の一環として、 **WidevineBaseLicenseAcquisitionUrl**プロパティが SDK に追加されました。

    Dictionary<AssetDeliveryPolicyConfigurationKey, string> assetDeliveryPolicyConfiguration =
        new Dictionary<AssetDeliveryPolicyConfigurationKey, string>
    {
        {AssetDeliveryPolicyConfigurationKey.WidevineBaseLicenseAcquisitionUrl,"http://testurl"},

    };

## <a id="jan_changes_16"></a>2016 年 1 月のリリース
エンコード予約ユニットは、エンコーダー名と混同しないように名前を変更されました。

Basic、Standard、および Premium エンコード予約ユニットは、それぞれ S1、S2、および S3 予約ユニットに名前が変更されました。  Basic エンコード RU をご利用の場合は Azure ポータル (と請求書) に S1 というラベルが表示され、Standard と Premium をご利用の場合はそれぞれ S2 と S3 が表示されます。 

## <a id="dec_changes_15"></a>2015 年 12 月のリリース

### <a name="azure-media-encoder-deprecation-announcement"></a>Azure Media Encoder 廃止のお知らせ

Azure Media Encoder は Media Encoder Standard のリリースから 12 か月ほどが経った時点で廃止される予定です。

### <a name="azure-sdk-for-php"></a>Azure SDK for PHP
Azure SDK チームは [Azure SDK for PHP](http://github.com/Azure/azure-sdk-for-php) パッケージの新しいリリースを公開しました。これには Microsoft Azure Media Services の更新プログラムと新機能が含まれています。 具体的には、Azure Media Services SDK for PHP で、最新の[コンテンツ保護](media-services-content-protection-overview.md)機能である AES と DRM (PlayReady と Widevine) による動的暗号化 (トークン制限あり/なし) がサポートされるようになりました。 また、[エンコーディング ユニット](media-services-dotnet-encoding-units.md)のスケーリングにも対応しています。

詳細については、次を参照してください。

* [Microsoft Azure Media Services SDK for PHP](http://southworks.com/blog/2015/12/09/new-microsoft-azure-media-services-sdk-for-php-release-available-with-new-features-and-samples/) ブログ。
* 次の [コード サンプル](http://github.com/Azure/azure-sdk-for-php/tree/master/examples/MediaServices) はクイック ガイドとして役立ちます。
  * **vodworkflow_aes.php**: これは PHP ファイルであり、AES-128 動的暗号化とキー配信サービスの使用方法を示します。 [この](media-services-protect-with-aes128.md) 記事で説明されている .NET サンプルに基づきます。
  * **vodworkflow_aes.php**: これは PHP ファイルであり、PlayReady 動的暗号化とライセンス配信サービスの使用方法を示します。 [この](media-services-protect-with-drm.md) 記事で説明されている .NET サンプルに基づきます。
  * **scale_encoding_units.php**: これはエンコード予約ユニットのスケーリング方法を示す PHP ファイルです。

## <a id="nov_changes_15"></a>2015 年 11 月のリリース
Azure Media Services は、クラウドで Google Widevine ライセンス配信サービスを提供するようになりました。 詳しくは、 [こちらの案内ブログ](https://azure.microsoft.com/blog/announcing-google-widevine-license-delivery-services-public-preview-in-azure-media-services/)をお読みください。 また、[こちらのチュートリアル](media-services-protect-with-drm.md)および [GitHub リポジトリ](http://github.com/Azure-Samples/media-services-dotnet-dynamic-encryption-with-drm)もご覧ください。 

Azure Media Services によって提供される Widevine ライセンス配信サービスはプレビュー期間中であることに注意してください。 詳細については、 [この投稿](https://azure.microsoft.com/blog/announcing-google-widevine-license-delivery-services-public-preview-in-azure-media-services/)を参照してください。

## <a id="oct_changes_15"></a>2015 年 10 月のリリース
Azure Media Services (AMS) は、現在、ブラジル南部、インド西部、インド南部およびインド中部のデータ センターで利用可能です。 Azure Portal を使用して [Media Services アカウントを作成](media-services-portal-create-account.md)し、[ここ](https://azure.microsoft.com/documentation/services/media-services/)で示すさまざまなタスクを実行できるようになりました。 ただし、これらのデータ センターでは Live Encoding は有効ではありません。 また、これらのデータ センターで、すべての種類のエンコード予約ユニットを使用できるわけではありません。

* ブラジル南部: Standard および Basic エンコード予約ユニットのみ使用可能
* インド西部、インド南部およびインド中部: Basic エンコード予約ユニットのみ使用可能

## <a id="september_changes_15"></a>2015 年 9 月のリリース
* AMS で、Widevine Modular DRM テクノロジを使用してビデオ オン デマンド (VOD) とライブ ストリーミングの両方を保護できるようになりました。 次の配信サービス パートナーを通して Widevine ライセンスを提供できます: [Axinom](http://www.axinom.com/press/ibc-axinom-drm-6/)、[EZDRM](http://ezdrm.com/)、[castLabs](http://castlabs.com/company/partners/azure/)。 詳細については、 [このブログ](https://azure.microsoft.com/blog/azure-media-services-adds-google-widevine-packaging-for-delivering-multi-drm-stream/)をご覧ください。
  
    [AMS .NET SDK](https://www.nuget.org/packages/windowsazure.mediaservices/) (バージョン 3.5.1 以降) または REST API を使用して、Widevine を使用するように AssetDeliveryConfiguration を構成できます。  
* AMS に、Apple ProRes ビデオのサポートが追加されました。 Apple ProRes やその他のコーデックを使用する QuickTime ソース ビデオをアップロードできるようになりました。 詳細については、 [このブログ](https://azure.microsoft.com/blog/announcing-support-for-apple-prores-videos-in-azure-media-services/)をご覧ください。
* Media Encoder Standard を使用して、サブクリップとライブ アーカイブ抽出を実行できるようになりました。 詳細については、 [このブログ](https://azure.microsoft.com/blog/sub-clipping-and-live-archive-extraction-with-media-encoder-standard/)をご覧ください。
* 次のフィルター処理の更新が行われました。 
  
  * オーディオ専用フィルター付きの Apple HTTP Live Streaming (HLS) 形式を使用できるようになりました。 この更新では、(audio-only=false) を、URL に指定することで、オーディオ専用トラックを削除できます。
  * アセット用のフィルターを定義するときに、複数のフィルター (最大 3 つ) を 1 つの URL に組み合わせることができるようになりました。
    
    詳細については、 [このブログ](https://azure.microsoft.com/blog/azure-media-services-release-dynamic-manifest-composition-remove-hls-audio-only-track-and-hls-i-frame-track-support/) をご覧ください。
* AMS で HLS v4 の I フレームをサポートするようになりました。 I フレームのサポートは、早送りと巻き戻しの操作を最適化します。 既定では、すべての HLS v4 出力には、I フレームの再生リスト (EXT-X-I-FRAME-STREAM-INF) が含まれます。
  
    詳細については、 [このブログ](https://azure.microsoft.com/blog/azure-media-services-release-dynamic-manifest-composition-remove-hls-audio-only-track-and-hls-i-frame-track-support/) をご覧ください。

## <a id="august_changes_15"></a>2015 年 8 月のリリース
* Azure Media Services SDK for Java V0.8.0 リリースと新しいサンプルをご利用いただけるようになりました。 詳細については、次を参照してください。
  
  * [ブログの投稿](http://southworks.com/blog/2015/08/25/microsoft-azure-media-services-sdk-for-java-v0-8-0-released-and-new-samples-available/)
  * [Java サンプルのリポジトリ](https://github.com/southworkscom/azure-sdk-for-media-services-java-samples)
* 複数のオーディオ ストリームのサポートを含む Azure Media Player の更新。 詳細については、次を参照してください。
  * [ブログの投稿](https://azure.microsoft.com/blog/2015/08/13/azure-media-player-update-with-multi-audio-stream-support/)

## <a id="july_changes_15"></a>2015 年 7 月のリリース
* Media Encoder Standard の一般公開の発表。 詳細については、 [このブログの投稿](https://azure.microsoft.com/blog/2015/07/16/announcing-the-general-availability-of-media-encoder-standard/)を参照してください。
  
    Media Encoder Standard では、 [このセクション](http://go.microsoft.com/fwlink/?LinkId=618336) で説明されているプリセットを使用しています。 4k エンコードのプリセットを使用する場合は、 **Premium** 予約ユニットの種類を取得する必要があります。 詳細については、「 [Encoding の規模の設定方法](media-services-scale-media-processing-overview.md)」を参照してください。
* Azure Media Services と Azure Media Player のライブ リアルタイム キャプション。 詳細については、[こちらのブログ記事](https://azure.microsoft.com/blog/2015/07/08/live-real-time-captions-with-azure-media-services-and-player/)をご覧ください。

### <a name="media-services-net-sdk-updates"></a>Media Services .NET SDK の更新
Azure Media Services .NET SDK が 3.4.0.0 にバージョン アップしました。 今回のリリースでは、次の機能が追加されました。  

* ライブ アーカイブのサポートが実装されました。 ライブ アーカイブを含む資産はダウンロードできません。
* 動的フィルターのサポートが実装されました。
* ユーザーがストレージ コンテナーを維持しながら資産を削除できる機能が実装されました。
* チャネルの再試行ポリシーに関連するバグが修正されました。
* **Media Encoder Premium Workflow** が有効になりました。

## <a id="june_changes_15"></a>2015 年 6 月のリリース
### <a name="media-services-net-sdk-updates"></a>Media Services .NET SDK の更新
Azure Media Services .NET SDK が 3.3.0.0 にバージョン アップされました。 今回のリリースでは、次の機能が追加されました。  

* OpenId Connect Discovery 仕様のサポート
* ID プロバイダー側でのキーのロールオーバー処理のサポート 

OpenID Connect 検出ドキュメントを公開する ID プロバイダー (これを実施しているプロバイダーは Azure Active Directory、Google、Salesforce) を使用している場合は、OpenID Connect Discovery 仕様から JWT トークンを検証するための署名キーを取得するように Azure Media Services に指示できます。 

詳細については、 [Azure Media Services での OpenID Connect Discovery 仕様の Json Web キーを使用した JWT トークン認証の操作に関するページ](http://gtrifonov.com/2015/06/07/using-json-web-keys-from-openid-connect-discovery-spec-to-work-with-jwt-token-authentication-in-azure-media-services/)を参照してください。

## <a id="may_changes_15"></a>2015 年 5 月のリリース
以下の新しい機能が発表されています。

* [Media Services による Live Encoding のプレビュー](media-services-manage-live-encoder-enabled-channels.md)
* [動的マニフェスト](media-services-dynamic-manifest-overview.md)
* [Azure Media Hyperlapse メディア プロセッサのプレビュー](https://azure.microsoft.com/blog/?p=286281&preview=1&_ppp=61e1a0b3db)

## <a id="april_changes_15"></a>2015 年 4 月のリリース
### <a name="general-media-services-updates"></a>Media Services の全般的な更新
* [Azure Media Player の発表](https://azure.microsoft.com/blog/2015/04/15/announcing-azure-media-player/)。
* Media Services REST 2.10 以降、RTMP プロトコルをインジェストするように構成されたチャネルは、プライマリとセカンダリのインジェスト URL を使用して作成されます。 詳細については、 [チャネル入力 (インジェスト) の構成](media-services-live-streaming-with-onprem-encoders.md#channel_input)
* Azure Media Indexer の更新
* スペイン語のサポート
* 新しい構成の xml 形式

詳細については、 [この投稿](https://azure.microsoft.com/blog/2015/04/13/azure-media-indexer-spanish-v1-2/)を参照してください。

### <a name="media-services-net-sdk-updates"></a>Media Services .NET SDK の更新
Azure Media Services .NET SDK が 3.2.0.0 にバージョン アップしました。

ユーザーに対するいくつかの更新は次のとおりです。

* **重大な変更**: **TokenRestrictionTemplate.Issuer** と **TokenRestrictionTemplate.Audience** が文字列型に変更されました。
* カスタム リトライ ポリシーの作成に関する更新。
* ファイルのアップロード/ダウンロードに関連するバグの修正。
* **MediaServicesCredentials** クラスが認証するためのプライマリとセカンダリのアクセス制御エンドポイントを受け入れるようになりました。

## <a id="march_changes_15"></a>2015 年 3 月のリリース
### <a name="general-media-services-updates"></a>Media Services の全般的な更新
* Media Services で、シームレスな Azure CDN 統合が提供されるようになりました。 統合をサポートするために、**CdnEnabled** プロパティが **StreamingEndpoint** に追加されました。  **CdnEnabled** はバージョン 2.9 以降の REST API で使用できます (詳細については「 [StreamingEndpoint](https://docs.microsoft.com/rest/api/media/operations/streamingendpoint)」をご覧ください)。  **CdnEnabled** はバージョン 3.1.0.2 以降の .NET SDK で使用できます (詳細については、[StreamingEndpoint](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.mediaservices.client.istreamingendpoint\(v=azure.10\).aspx) に関するページをご覧ください)。
* **Media Encoder Premium Workflow**の発表。 詳細については、 [Azure Media Services へのプレミアム エンコードの導入に関するページ](https://azure.microsoft.com/blog/2015/03/05/introducing-premium-encoding-in-azure-media-services/)をご覧ください。

## <a id="february_changes_15"></a>2015 年 2 月のリリース
### <a name="general-media-services-updates"></a>Media Services の全般的な更新
Media Services REST API は、現在、バージョン 2.9 です。 このバージョンより、Azure CDN をストリーミング エンドポイントと統合できます。 詳細については、「 [StreamingEndpoint」](https://msdn.microsoft.com/library/dn783468.aspx)をご覧ください。

## <a id="january_changes_15"></a>2015 年 1 月のリリース
### <a name="general-media-services-updates"></a>Media Services の全般的な更新
動的な暗号化による Content Protection の General Availability (GA) についてのお知らせ。 詳細については、 [Azure Media Services での DRM テクノロジの一般的な可用性によるストリーミング セキュリティの強化に関するページ](https://azure.microsoft.com/blog/2015/01/29/azure-media-services-enhances-streaming-security-with-general-availability-of-drm-technology/)をご覧ください。

### <a name="media-services-net-sdk-updates"></a>Media Services .NET SDK の更新
Azure Media Services .NET SDK が 3.1.0.1 にバージョン アップしました。

このリリースでは、既定の Microsoft.WindowsAzure.MediaServices.Client.ContentKeyAuthorization.TokenRestrictionTemplate コンストラクターが旧式としてマークされています。 新しいコンス トラクターは、TokenType を引数として受け取ります。

    TokenRestrictionTemplate template = new TokenRestrictionTemplate(TokenType.SWT);


## <a id="december_changes_14"></a>2014 年 12 月のリリース
### <a name="general-media-services-updates"></a>Media Services の全般的な更新
* Azure Indexer メディア プロセッサに、いくつかの更新と新機能が追加されています。 詳細については、「 [Azure Media Indexer Version 1.1.6.7 のリリース ノート](https://azure.microsoft.com/blog/2014/12/03/azure-media-indexer-version-1-1-6-7-release-notes/)」をご覧ください。
* エンコード占有ユニットを更新できる新しい REST API が追加されました ( [EncodingReservedUnitType](https://docs.microsoft.com/rest/api/media/operations/encodingreservedunittype))。
* キー配信サービスの CORS サポートが追加されました。
* 承認ポリシー オプションのクエリ パフォーマンスが向上しています。
* 中国のデータ センターで、 [キー配信 URL](https://docs.microsoft.com/rest/api/media/operations/contentkey#get_delivery_service_url) が顧客単位となりました (他のデータ センターと同じです)。
* HLS 自動ターゲット期間が追加されました。 ライブ ストリーミングの実行中、HLS は常に動的にパッケージ化されます。 既定では、Live エンコーダーから送信されるキーフレーム間隔 (KeyFrameInterval) に基づき Media Services が自動的に HLS セグメント パッケージ化率 (FragmentsPerSegment) を計算します。これは GOP (Group of Pictures) と呼ばれることもあります。 詳細については、「 [Working with Azure Media Services Live Streaming (Azure Media Services ライブ ストリーミングでの動作)]」を参照してください。

### <a name="media-services-net-sdk-updates"></a>Media Services .NET SDK の更新
* [Azure Media Services .NET SDK](http://www.nuget.org/packages/windowsazure.mediaservices/) が 3.1.0.0 にバージョン アップしました。
* .Net SDK の依存関係が .NET 4.5 Framework にアップグレードされました。
* エンコード予約ユニットを更新できる新しい API が追加されました。 詳細については、 [.NET を使用した占有ユニットの種類の更新とエンコーディング占有ユニットの拡張に関するページ](media-services-dotnet-encoding-units.md)をご覧ください。
* トークン認証に JWT (JSON Web Token) が新たにサポートされています。 詳細については、「[JWT token Authentication in Azure Media Services and Dynamic Encryption (Azure Media Services と動的暗号化における JWT トークン認証)](http://www.gtrifonov.com/2015/01/03/jwt-token-authentication-in-azure-media-services-and-dynamic-encryption/)」をご覧ください。
* PlayReady ライセンス テンプレートに BeginDate と ExpirationDate の相対オフセットが追加されました。

## <a id="november_changes_14"></a>2014 年 11 月のリリース
* ライブ スムーズ ストリーミング (FMP4) コンテンツの SSL 接続での取り込みに Media Services が対応しました。 SSL 経由で取り込むには、取り込み URL を HTTPS に更新する必要があります。  なお、現在のところ、AMS ではカスタム ドメインを使用した SSL はサポートされていません。  ライブ ストリーミングの詳細については、[Working with Azure Media Services Live Streaming (Azure Media Services ライブ ストリーミングでの動作)]に関するページをご覧ください。
* RTMP ライブ ストリームについては現在、SSL 接続で取り込むことはできません。
* SSL 経由でのストリーミングを実行できるのは、コンテンツの配信元となるストリーミング エンドポイントが 2014 年 9 月 10 日より後に作成されている場合のみです。 ストリーミング URL の基になるストリーミング エンドポイントの作成日が 9 月 10 日より後である場合、URL に "streaming.mediaservices.windows.net" (新形式) が含まれています。 "origin.mediaservices.windows.net" (旧形式) を含んだストリーミング URL では、SSL がサポートされません。 URL が旧形式である場合、SSL ストリーミングに対応するためには、[新しいストリーミング エンドポイントを作成](media-services-portal-manage-streaming-endpoints.md)してください。 SSL でコンテンツをストリーミングするには、新しいストリーミング エンドポイントに基づいて作成された URL を使用する必要があります。

## <a id="october_changes_14"></a>2014 年 10 月のリリース
### <a id="new_encoder_release"></a>Media Services Encoder のリリース
Media Services Azure Media Encoder の新規リリースが発表されています。 最新の Azure Media Encoder では、出力 GB 分のみ課金されます。それ以外は、新しいエンコーダーの機能は前のエンコーダーと互換性があります。 詳細については、[Media Services の価格詳細]のページをご覧ください。

### <a id="oct_sdk"></a>Media Services .NET SDK
Media Services SDK for .NET Extensions は、現在、バージョン 2.0.0.3 です。

Media Services SDK for .NET は、現在、バージョン 3.0.0.8 です。

変更点を次に示します。

* 再試行ポリシーのクラスをリファクタリング。
* ユーザー エージェント文字列を http 要求ヘッダーに追加。
* Nuget のビルド復元ステップを追加。
* リポジトリからの x509 証明書を使用するようにシナリオ テストを修正。
* チャンネルとストリーミング エンドを更新するときの設定を検証。

### <a name="new-github-repository-to-host-media-services-samples"></a>Media Services のサンプルをホストする新しい GitHub リポジトリ
サンプルは、 [Azure Media Services のサンプル GitHub リポジトリ](https://github.com/Azure/Azure-Media-Services-Samples)に置かれています。

## <a id="september_changes_14"></a>2014 年 9 月のリリース
Media Services REST メタデータは、現在、バージョン 2.7 です。 最新の REST 更新プログラムの詳細については、「 [Azure Media Services REST API リファレンス]」を参照してください。

Media Services SDK for .NET は、現在、バージョン 3.0.0.7 です。

### <a id="sept_14_breaking_changes"></a>互換性に影響する変更
* **オリジン** の名前が [ストリーミング エンドポイント]に変更されました。
* **Azure ポータル**で MP4 ファイルをエンコードし、その後、発行する際の既定の動作が変更されました。

以前は、Azure クラシック ポータルを使用して単一ファイルの MP4 ビデオ資産を発行すると、SAS URL が作成されました (SAS URL を使用してビデオを BLOB ストレージからダウンロードできます)。 現在は、Azure クラシック ポータルを使用して、単一ファイルの MP4 ビデオ資産をエンコードし、その後、発行すると、生成された URL は Azure Media Services ストリーミング エンドポイントを指します。  この変更は、Media Services に直接アップロードされ、Azure Media Services によってエンコードされずに発行された MP4 ビデオには影響しません。

現在、問題を解決するための次の&2; つのオプションがあります。

* ストリーミング ユニットを有効にし、動的パッケージを使用して、.mp4 資産を Smooth Streaming プレゼンテーションとしてストリーミングします。
* .mp4 をダウンロード (または、漸次的に再生) するための SAS url を作成します。 SAS ロケーターの作成方法の詳細については、「 [コンテンツの配信]」を参照してください。

### <a id="sept_14_GA_changes"></a>GA リリースに含まれる新機能/シナリオ
* **インデクサー メディア プロセッサ**。 詳細については、「 [Azure Media Indexer によるメディア ファイルのインデックス作成]」を参照してください。
* 現在、 [ストリーミング エンドポイント] エンティティを使用して、カスタム ドメイン (ホスト) 名を追加できます。
  
    Media Services ストリーミング エンドポイント名として使用されるカスタム ドメイン名の場合、カスタム ホスト名をストリーミング エンドポイントに追加する必要があります。 Media Services REST API または .NET SDK を使用して、カスタム ホスト名を追加します。
  
    次の考慮事項が適用されます。
  
  * カスタム ドメイン名の所有権を保持している必要があります。
  * ドメイン名の所有権は、Azure Media Services によって検証する必要があります。 ドメインを検証するには、<MediaServicesAccountId>.<parent domain> を  verifydns.<mediaservices-dns-zone> にマップする CName を作成します。 
  * カスタム ホスト名 (たとえば、sports.contoso.com) を Media Services StreamingEndpont のホスト名 (たとえば、amstest.streaming.mediaservices.windows.net) にマップする別の CName を作成する必要があります。

    詳細については、「 **StreamingEndpoint** 」の [ストリーミング エンドポイント] プロパティを参照してください。

### <a id="sept_14_preview_changes"></a>パブリック プレビュー リリースに含まれる新機能/シナリオ
* ライブ ストリーミングのプレビュー。 詳細については、「 [Working with Azure Media Services Live Streaming (Azure Media Services ライブ ストリーミングでの動作)]」を参照してください。
* キー配信サービス。 詳細については、「 [AES-128 動的暗号化とキー配信サービスの使用]」を参照してください。
* AES 動的暗号化。 詳細については、「 [AES-128 動的暗号化とキー配信サービスの使用]」を参照してください。
* PlayReady ライセンス配信サービス。 詳細については、「 [Using PlayReady Dynamic Encryption and License Delivery Service (PlayReady 動的暗号化とライセンス配信サービスの使用)]」を参照してください。
* PlayReady 動的暗号化。 詳細については、「 [Using PlayReady Dynamic Encryption and License Delivery Service (PlayReady 動的暗号化とライセンス配信サービスの使用)]」を参照してください。
* Media Services PlayReady ライセンス テンプレート。 詳細については、「 [Media Services PlayReady ライセンス テンプレートの概要]」を参照してください。
* ストリーミング ストレージ暗号化資産。 詳細については、「 [Streaming Storage Encrypted Content (ストリーミング ストレージ暗号化コンテンツ)]」を参照してください。

## <a id="august_changes_14"></a>2014 年 8 月のリリース
資産をエンコードすると、エンコード ジョブの完了時に出力資産が生成されます。 このリリースになるまで、Azure Media Services エンコーダーは出力資産に関するメタデータを生成していました。 このリリースから、エンコーダーは入力資産に関するメタデータも生成します。 詳細については、「[Input Metadata (入力のメタデータ)]」および「[Output Metadata (出力のメタデータ)]」を参照してください。

## <a id="july_changes_14"></a>2014 年 7 月のリリース
Azure Media Services パッケージおよび暗号化機能で次のバグが修正されました。

* ライブ アーカイブ資産を HTTP ライブ ストリーミングに変換するとオーディオしか再生されない – これは修正され、現在はオーディオとビデオの両方が再生されます。
* 資産を HTTP ライブ ストリーミングおよび AES 128 ビット エンベロープ暗号化にパッケージ化すると、パッケージ化されたストリームが Android デバイス上で再生されない – このバグは修正され、パッケージ化されたストリームは HTTP ライブ ストリーミングをサポートする Android デバイス上で再生されます。

## <a id="may_changes_14"></a>2014 年 5 月のリリース
### <a id="may_14_changes"></a>Media Services の全般的な更新
現在は、[ダイナミック パッケージ]を使用して、HTTP ライブ ストリーミング (HLS) v3 をストリーミングできます。 HLS v3 をストリーミングするには、次のフォーマットをオリジン ロケーター パスに追加します。*.ism/manifest(format=m3u8-aapl-v3)。 詳細については、「[Nick Drouin's Blog (Nick Drouin のブログ)]」を参照してください。

現在、動的パッケージは、PlayReady によるスムーズ ストリーミング静的暗号化に基づく、PlayReady による HLS (v3 と v4) 暗号化の配信もサポートしています。 PlayReady によるスムーズ ストリーミングの暗号化方法の詳細については、「 [PlayReady によるスムーズ ストリーミングおよび MPEG DASH の保護]」を参照してください。

### <a name="may_14_donnet_changes"></a>Media Services .NET SDK の更新
Media Services .NET SDK 3.0.0.5 リリースでは、次の改善が加えられました。

* メディア資産のアップロードおよびダウンロードの迅速化と復元性の向上。
* 再試行ロジックと一時的な例外の処理の改善: 
  
  * ファイルのクエリ、保存、変更、アップロード、またはダウンロードによって引き起こされる例外に関する一時的なエラーの検出と再試行ロジックが改善されました。 
  * Web の例外が返される際に (たとえば、ACS トークンの要求時)、致命的なエラーが以前より早く失敗を通知することに気付きます。

詳細については、「 [RMedia Services SDK for .NET の再試行ロジック]」を参照してください。

## <a id="april_changes_14"></a>2014 年 4 月のエンコーダーのリリース
### <a name="april_14_enocer_changes"></a>Media Services Encoder の更新
* Grass Valley HQ/HQX コーデックを使用してビデオをすばやく圧縮する Grass Valley EDIUS 非線形エディターを使用して作成された AVI ファイルの取り込みがサポートされるようになりました。 詳細については、「 [Grass Valley Announces EDIUS 7 Streaming Through the Cloud (Grass Valley 社、クラウドを介した EDIUS 7 ストリーミングを発表)]」を参照してください。
* Media Encoder によって生成されたファイルの名前付け規則を指定できるようになりました。 詳細については、「 [Media Services Encoder の出力ファイル名の制御]」を参照してください。
* ビデオ オーバーレイおよびオーディオ オーバーレイのサポートが追加されました。 詳細については、「 [オーバーレイの作成]」を参照してください。
* 複数のビデオ セグメントを結合できるようになりました。 詳細については、「 [ビデオのセグメントの結合]」を参照してください。
* オーディオが MPEG-1 Audio layer 3 (MP3) でエンコードされるという、MP4 のコード変換に関するバグが修正されました。

## <a id="jan_feb_changes_14"></a>2014 年 1 月と 2 月のリリース
### <a name="jan_fab_14_donnet_changes"></a>Azure Media Services .NET SDK 3.0.0.1、3.0.0.2、および 3.0.0.3
3.0.0.1 および 3.0.0.2 における変更:

* OrderBy ステートメントでの LINQ クエリの使用に関する問題が修正されました。
* [GitHub] のテスト ソリューションをユニットベースのテストとシナリオベースのテストに分割しました。

変更の詳細については、「 [Azure Media Services .NET SDK 3.0.0.1 および 3.0.0.2 のリリース]」を参照してください。

3.0.0.3 では次の点が変更されました。

* バージョン 3.0.3.0 を使用するように Azure Storage の依存関係がアップグレードされました。 
* 3(REST) を使用する必要があります。0(REST) を使用する必要があります。*(REST) を使用する必要があります。* リリースの下位互換性の問題が修正されました。 

## <a id="december_changes_13"></a>2013 年 12 月のリリース
### <a name="dec_13_donnet_changes"></a>Azure Media Services .NET SDK 3.0.0.0
> [!NOTE]
> 3.0.x.x リリースには、2.4.x.x リリースとの下位互換性がありません。
> 
> 

現在、Media Services SDK の最新バージョンは 3.0.0.0 です。 Nuget から最新パッケージをダウンロードするか、 [GitHub]からビットを取得できます。

Media Services SDK Version 3.0.0.0 から、 [Azure Active Directory Access Control Service (ACS)] トークンを再利用できます。 詳細については、「 [Media Services SDK for .NET を使用した Media Services アカウントへの接続] 」の「Access Control Service トークンの再利用」を参照してください。

### <a name="dec_13_donnet_ext_changes"></a>Azure Media Services .NET SDK Extensions 2.0.0.0
Azure Media Services .NET SDK Extensions は、コードを簡素化し、Azure Media Services による開発を容易にする一連の拡張機能メソッドおよびヘルパー機能です。 [Azure Media Services .NET SDK Extensions]から最新のビットを取得できます。

## <a id="november_changes_13"></a>2013 年 11 月のリリース
### <a name="nov_13_donnet_changes"></a>Azure Media Services .NET SDK の変更点
このバージョンから、Media Services SDK for .NET は、Media Services REST API レイヤーの呼び出し時発生することのある一時的な障害エラーを処理します。

## <a id="august_changes_13"></a>2013 年 8 月のリリース
### <a name="aug_13_powershell_changes"></a>Azure SDK ツールに含まれる Media Services PowerShell コマンドレット
現在、次の Media Services PowerShell コマンドレットが [azure-sdk-tools]に含まれています。

* Get-AzureMediaServices 
  
    たとえば、「 `Get-AzureMediaServicesAccount`」のように入力します。
* New-AzureMediaServicesAccount 
  
    たとえば、「 `New-AzureMediaServicesAccount -Name “MediaAccountName” -Location “Region” -StorageAccountName “StorageAccountName”`」のように入力します。
* New-AzureMediaServicesKey 
  
    たとえば、「 `New-AzureMediaServicesKey -Name “MediaAccountName” -KeyType Secondary -Force`」のように入力します。
* Remove-AzureMediaServicesAccount 
  
    たとえば、「 `Remove-AzureMediaServicesAccount -Name “MediaAccountName” -Force`」のように入力します。

## <a id="june_changes_13"></a>2013 年 6 月のリリース
### <a name="june_13_general_changes"></a>Azure Media Services の変更点
ここで説明する変更点は、2013 年 6 月の Media Services リリースに含まれている更新内容です。

* 複数のストレージ アカウントを Media Service アカウントにリンクする機能。 
  
    StorageAccount
  
    Asset.StorageAccountName および Asset.StorageAccount
* Job.Priority を更新する機能。 
* エンティティおよびプロパティ関連の通知: 
  
    JobNotificationSubscription
  
    NotificationEndPoint
  
    ジョブ
* Asset.Uri 
* Locator.Name 

### <a name="june_13_dotnet_changes"></a>Azure Media Services .NET SDK の変更点
2013 年 6 月の Media Services SDK リリースには、次の変更点が加えられました。 最新の Media Services SDK は、GitHub から入手可能です。

* Media Services SDK Version 2.3.0.0 から、複数のストレージ アカウントの Media Services アカウントへのリンクがサポートされています。 次の API がこの機能をサポートしています。
  
    IStorageAccount 型。
  
    Microsoft.WindowsAzure.MediaServices.Client.CloudMediaContext.StorageAccounts プロパティ
  
    StorageAccount プロパティ
  
    StorageAccountName プロパティ
  
    詳細については、「 [複数のストレージ アカウントでの Media Services アセットの管理]」を参照してください。
* API 関連の通知。 バージョン 2.2.0.0 から、Azure キュー ストレージ通知をリッスンできます。 詳細については、「 [Media Services ジョブ通知の処理]」を参照してください。
  
    Microsoft.WindowsAzure.MediaServices.Client.IJob.JobNotificationSubscriptions プロパティ
  
    Microsoft.WindowsAzure.MediaServices.Client.INotificationEndPoint 型
  
    Microsoft.WindowsAzure.MediaServices.Client.IJobNotificationSubscription 型
  
    Microsoft.WindowsAzure.MediaServices.Client.NotificationEndPointCollection 型
  
    Microsoft.WindowsAzure.MediaServices.Client.NotificationEndPointType 型
  
    Microsoft.WindowsAzure.MediaServices.Client.NotificationJobState 型
* Azure Storage クライアント SDK 2.0 への依存関係 (Microsoft.WindowsAzure.StorageClient.dll)
* OData 5.5 への依存関係 (Microsoft.Data.OData.dll)。

## <a id="december_changes_12"></a>2012 年 12 月のリリース
### <a name="dec_12_dotnet_changes"></a>Azure Media Services .NET SDK の変更点
* Intellisense : 多くの型で不足していた Intellisense ドキュメントが追加されました。
* Microsoft.Practices.TransientFaultHandling.Core : SDK がこのアセンブリの旧バージョンに依存していた問題が修正されました。 現在、SDK はこのアセンブリのバージョン 5.1.1209.1 を参照しています。

2012 年 11 月の SDK で見つかった問題が修正されました。

* IAsset.Locators.Count : このカウントは、すべてのロケーターが削除された後、新しい IAsset インターフェイスで正しく報告されるようになりました。
* IAssetFile.ContentFileSize : この値は、IAssetFile.Upload(filepath) よるアップロード後に適切に設定されるようになりました。
* IAssetFile.ContentFileSize : このプロパティは、資産ファイルの作成時に設定できるようになりました。 以前は、読み取り専用でした。
* IAssetFile.Upload(filepath) : この同期アップロード メソッドが複数のファイルを資産にアップロードしたときに次のエラーをスローする問題が修正されました。 エラーは、「Server failed to authenticate the request. Make sure the value of Authorization header is formed correctly including the signature.(サーバーが要求を認証できませんでした。認証ヘッダーの値が、署名を含め正しく形成されていることを確認してください。)」
* IAssetFile.UploadAsync : 5 個のファイルを同時にアップロードできないという問題が修正されました。
* IAssetFile.UploadProgressChanged : このイベントは、SDK によって提供されるようになりました。
* IAssetFile.DownloadAsync(string, BlobTransferClient, ILocator, CancellationToken): このメソッドのオーバーロードが提供されるようになりました。
* IAssetFile.DownloadAsync : 5 個のファイルを同時にダウンロードできないという問題が修正されました。
* IAssetFile.Delete() : IAssetFile のファイルがアップロードされていない場合に、delete 呼び出しで例外がスローされる問題が修正されました。
* Jobs : ジョブ テンプレートを使用して ”MP4 から Smooth Streaming へのタスク” と ”PlayReady 保護タスク” を連結してもまったくタスクが作成されないという問題が修正されました。
* EncryptionUtils.GetCertificateFromStore() : このメソッドは、証明書構成の問題に基づく証明書検出エラーを原因とするヌル参照例外をスローしなくなりました。

## <a id="november_changes_12"></a>2012 年 11 月のリリース
ここで説明する変更点は、2012 年 11 月 (バージョン 2.0.0.0) SDK に含まれている更新内容です。 これらの変更では、2012 年 6 月のプレビュー SDK リリース向けに記述されたコードの変更または書き換えが必要です。

* 資産
  
    IAsset.Create(assetName) は、唯一の資産作成関数です。 IAsset.Create は、現在、メソッド呼び出しの一部としてファイルをアップロードしません。 アップロードには IAssetFile を使用してください。
  
    IAsset.Publish メソッドと AssetState.Publish 列挙値は、サービス SDK から削除されました。 尾の値に依存しているコードはすべて書き換える必要があります。
* FileInfo
  
    このクラスは削除され、IAssetFile によって置き換えられました。
  
    IAssetFiles
  
    IAssetFile は FileInfo にとって代わり、異なる動作を実行します。 使用するには、IAssetFiles オブジェクトをインスタント化し、Media Services SDK または Azure Storage SDK を使用してファイルをアップロードします。 次の IAssetFile.Upload オーバーロードを使用できます。
  
  * IAssetFile.Upload(filePath): スレッドをブロックする同期メソッドであり、単一のファイルをアップロードする場合のみ推奨されます。
  * IAssetFile.UploadAsync(filePath, blobTransferClient, locator, cancellationToken): 非同期メソッドです。 これは、推奨されるアップロード メカニズムです。 
    
    既知のバグ: cancellationToken を使用すると、本多王にアップロードがキャンセルされます。ただし、タスクのキャンセル状態は複数の状態のいずれかになります。 例外を適切にキャッチし、処理する必要があります。
* ロケーター
  
    オリジン固有のバージョンは削除されました。 SAS 固有の context.Locators.CreateSasLocator(asset, accessPolicy) は非推奨化されます。つまり、GA までに廃止されます。 更新後の動作については、新機能について説明するページの Locators のセクションをご覧ください。

## <a id="june_changes_12"></a>2012 年 6 月のプレビュー リリース
以下は、SDK の&11; 月のリリースでの新機能です。

* エンティティの削除
  
    IAsset、IAssetFile、ILocator、IAccessPolicy、IContentKey オブジェクトは、Collection、つまり cloudMediaContext.ObjCollection.Delete(objInstance) で削除する必要がありましたが、現在は代わりに、オブジェクト レベルの IObject.Delete() で削除されます。
* ロケーター
  
    現在、ロケーターは CreateLocator メソッドを使用して作成し、LocatorType.SAS または LocatorType.OnDemandOrigin 列挙値を、作成する特定の種類のロケーターの引数として使用する必要があります。
  
    コンテンツ用に使用できる URI の取得をより容易にするためのプロパティがロケーターに追加されました。 ロケーターのこの再設計により、将来のサードパーティの拡張性を確保するための柔軟性が向上し、メディア クライアント アプリケーションの使いやすさが改善されました。
* 非同期メソッドのサポート
  
    すべてのメソッドに非同期のサポートが追加されました。

## <a name="media-services-learning-paths"></a>Media Services のラーニング パス
[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>フィードバックの提供
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

<!-- Anchors. -->

<!-- Images. -->

<!--- URLs. --->
[Azure Media Services MSDN フォーラム]: http://social.msdn.microsoft.com/forums/azure/home?forum=MediaServices
[Azure Media Services REST API リファレンス]: https://docs.microsoft.com/rest/api/media/operations/azure-media-services-rest-api-reference
[Media Services の価格詳細]: http://azure.microsoft.com/pricing/details/media-services/
[Input Metadata (入力のメタデータ)]: http://msdn.microsoft.com/library/azure/dn783120.aspx
[Output Metadata (出力のメタデータ)]: http://msdn.microsoft.com/library/azure/dn783217.aspx
[コンテンツの配信]: http://msdn.microsoft.com/library/azure/hh973618.aspx
[Azure Media Indexer によるメディア ファイルのインデックス作成]: http://msdn.microsoft.com/library/azure/dn783455.aspx
[ストリーミング エンドポイント]: http://msdn.microsoft.com/library/azure/dn783468.aspx
[Working with Azure Media Services Live Streaming (Azure Media Services ライブ ストリーミングでの動作)]: http://msdn.microsoft.com/library/azure/dn783466.aspx
[AES-128 動的暗号化とキー配信サービスの使用]: http://msdn.microsoft.com/library/azure/dn783457.aspx
[Using PlayReady Dynamic Encryption and License Delivery Service (PlayReady 動的暗号化とライセンス配信サービスの使用)]: http://msdn.microsoft.com/library/azure/dn783467.aspx
[Preview features]: http://azure.microsoft.com/services/preview/
[Media Services PlayReady ライセンス テンプレートの概要]: http://msdn.microsoft.com/library/azure/dn783459.aspx
[Streaming Storage Encrypted Content (ストリーミング ストレージ暗号化コンテンツ)]: http://msdn.microsoft.com/library/azure/dn783451.aspx
[Azure portal]: https://manage.windowsazure.com
[ダイナミック パッケージ]: http://msdn.microsoft.com/library/azure/jj889436.aspx
[Nick Drouin's Blog (Nick Drouin のブログ)]: http://blog-ndrouin.azurewebsites.net/hls-v3-new-old-thing/
[PlayReady によるスムーズ ストリーミングおよび MPEG DASH の保護]: http://msdn.microsoft.com/library/azure/dn189154.aspx
[RMedia Services SDK for .NET の再試行ロジック]: http://msdn.microsoft.com/library/azure/dn745650.aspx
[Grass Valley Announces EDIUS 7 Streaming Through the Cloud (Grass Valley 社、クラウドを介した EDIUS 7 ストリーミングを発表)]: http://www.streamingmedia.com/Producer/Articles/ReadArticle.aspx?ArticleID=96351&utm_source=dlvr.it&utm_medium=twitter
[Media Services Encoder の出力ファイル名の制御]: http://msdn.microsoft.com/library/azure/dn303341.aspx
[オーバーレイの作成]: http://msdn.microsoft.com/library/azure/dn640496.aspx
[ビデオのセグメントの結合]: http://msdn.microsoft.com/library/azure/dn640504.aspx
[Azure Media Services .NET SDK 3.0.0.1 および 3.0.0.2 のリリース]: http://www.gtrifonov.com/2014/02/07/windows-azure-media-services-.net-sdk-3.0.0.2-release/
[Azure Active Directory Access Control Service (ACS)]: http://msdn.microsoft.com/library/hh147631.aspx
[Media Services SDK for .NET を使用した Media Services アカウントへの接続]: http://msdn.microsoft.com/library/azure/jj129571.aspx
[Azure Media Services .NET SDK Extensions]: https://github.com/Azure/azure-sdk-for-media-services-extensions/tree/dev
[azure-sdk-tools]: https://github.com/Azure/azure-sdk-tools
[GitHub]: https://github.com/Azure/azure-sdk-for-media-services
[複数のストレージ アカウントでの Media Services アセットの管理]: http://msdn.microsoft.com/library/azure/dn271889.aspx
[Media Services ジョブ通知の処理]: http://msdn.microsoft.com/library/azure/dn261241.aspx


