---
title: Azure Media Services v3 のよく寄せられる質問 | Microsoft Docs
description: この記事では、Azure Media Services v3 についてよく寄せられる質問に対する回答を提供します。
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 08/31/2020
ms.author: inhenkel
ms.openlocfilehash: d34b5aaaa12a3d296f92e0d7be34ae76931d8506
ms.sourcegitcommit: bcda98171d6e81795e723e525f81e6235f044e52
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/01/2020
ms.locfileid: "89268487"
---
# <a name="media-services-v3-frequently-asked-questions"></a>Media Services v3 のよく寄せられる質問

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

この記事では、Azure Media Services v3 についてよく寄せられる質問に対する回答を提供します。

## <a name="general"></a>全般

### <a name="what-are-the-azure-portal-limitations-for-media-services-v3"></a>Media Services v3 に関する Azure portal の制限事項とは何ですか。

[Azure portal](https://portal.azure.com/) を使用し、v3 のライブ イベントを管理し、v3 の資産を表示し、API へのアクセスに関する情報を取得し、コンテンツを暗号化できます。 <br/>他のすべての管理タスク (変換とジョブの管理や v3 コンテンツの分析など) には、[REST API](https://aka.ms/ams-v3-rest-ref)、[CLI](https://aka.ms/ams-v3-cli-ref)、あるいはサポートされているいずれかの [SDK](media-services-apis-overview.md#sdks) を使用します。

ビデオが以前に Media Services v3 API を使用して Media Services アカウントにアップロードされていた場合、またはコンテンツがライブ出力に基づいて生成された場合、Azure portal に **[エンコード]** 、 **[分析]** 、または **[暗号化]** ボタンは表示されません。 これらのタスクを実行するには、Media Services v3 API シリーズを使用してください。  

### <a name="what-azure-roles-can-perform-actions-on-azure-media-services-resources"></a>Azure Media Services リソースに対してアクションを実行できる Azure のロールは何ですか。 

「[Media Services アカウント用のロールベースのアクセス制御 (RBAC)](rbac-overview.md)」を参照してください。

### <a name="how-do-i-stream-to-apple-ios-devices"></a>Apple iOS デバイスにはどのようにしてストリーミングしますか?

パスの終わり (URL の「 **/manifest**」の部分の後) に **(format=m3u8-aapl)** が指定されていることを確認します。これは、Apple iOS ネイティブ デバイスで使用するために HTTP ライブ ストリーミング (HLS) コンテンツを返すようにストリーミング元のサーバーに指示します。 詳細については、「[コンテンツの配信](dynamic-packaging-overview.md)」を参照してください。

### <a name="how-do-i-configure-media-reserved-units"></a>メディア占有ユニットの構成方法を教えてください。

Media Services v3 または Video Indexer によってトリガーされる音声分析と動画分析ジョブでは、お使いのアカウントを 10 個の S3 メディア占有ユニット (MRU) でプロビジョニングすることをお勧めします。 10 個を超える S3 MRU が必要な場合は、[Azure portal](https://portal.azure.com/) を使用してサポート チケットを開いてください。

詳細については、「[メディア処理のスケーリング](media-reserved-units-cli-how-to.md)」を参照してください。

### <a name="what-is-the-recommended-method-to-process-videos"></a>推奨される動画の処理方法は?

[Transform](/rest/api/media/transforms) を使用して、ビデオのエンコードや分析を行うための一般的なタスクを構成できます。 各 Transform は、ビデオまたはオーディオ ファイルを処理するためのレシピ､すなわちタスクのワークフローの記述です｡ [ジョブ](/rest/api/media/jobs)は、特定の入力ビデオまたはオーディオ コンテンツに Transform を適用する Media Services への実際の要求です。 Transform を作成後､Media Services API または公開されている任意の SDK を使用してジョブを送信できます｡ 詳しくは、「[Transform と Job](transforms-jobs-concept.md)」をご覧ください。

### <a name="i-uploaded-encoded-and-published-a-video-why-wont-the-video-play-when-i-try-to-stream-it"></a>ビデオをアップロード、エンコード、および公開しました。 ストリーミングしようとしたときにビデオが再生されないのはなぜですか?

最も一般的な原因の 1 つは、再生しようとしているストリーミング エンドポイントが実行中の状態になっていないことです。

### <a name="how-does-pagination-work"></a>改ページはどのように機能しますか?

改ページを使用している場合は、常に次のリンクを使用してコレクションを列挙し、特定のページ サイズに依存しないようにする必要があります。 詳細および例については、[フィルター処理、順序付け、ページング](entities-overview.md)に関するページを参照してください。

### <a name="what-features-are-not-yet-available-in-azure-media-services-v3"></a>Azure Media Services v3 では、どのような機能がまだ利用可能になっていますか。

詳細については、「[v2 API に関する機能のギャップ](media-services-v2-vs-v3.md#feature-gaps-with-respect-to-v2-apis)」を参照してください。

### <a name="what-is-the-process-of-moving-a-media-services-account-between-subscriptions"></a>サブスクリプション間での Media Services アカウントの移動のプロセスはどのようなものですか?  

詳細については、「[サブスクリプション間での Media Services アカウントの移動](media-services-account-concept.md)」を参照してください。

## <a name="live-streaming"></a>ライブ ストリーミング 

### <a name="how-do-i-stop-the-live-stream-after-the-broadcast-is-done"></a>ブロードキャストの完了後にライブ ストリームを停止するにはどうすればいいですか?

クライアント側またはサーバー側からアプローチできます。

#### <a name="client-side"></a>クライアント側

ユーザーがブラウザーを閉じている場合、Web アプリケーションでは、ユーザーにブロードキャストを終了するかどうか確認するメッセージを表示されます。 これは、Web アプリケーションで処理できるブラウザー イベントです。

#### <a name="server-side"></a>サーバー側

Azure Event Grid イベントをサブスクライブすることで、ライブ イベントを監視できます。 詳細については、「[EventGrid イベント スキーマ](media-services-event-schemas.md#live-event-types)」を参照してください。

次のいずれかを実行できます。

* ストリーム レベルの [Microsoft.Media.LiveEventEncoderDisconnected](media-services-event-schemas.md#liveeventencoderdisconnected) を[サブスクライブ](reacting-to-media-services-events.md)して、しばらく再接続を受信していないことを監視し、ライブ イベントを停止して削除します。
* トラック レベルの[ハートビート](media-services-event-schemas.md#liveeventingestheartbeat) イベントを[サブスクライブ](reacting-to-media-services-events.md)します。 すべてのトラックで受信ビットレートが 0 に低下した場合、または最後のタイム スタンプが増加しなくなった場合は、ライブ イベントを安全にシャットダウンできます。 ハートビート イベントは各トラックで 20 秒ごとに発生するため、若干冗長になる可能性があります。

###  <a name="how-do-i-insert-breaksvideos-and-image-slates-during-a-live-stream"></a>ライブ ストリーミング中に中断 / 動画またはイメージ スレートを挿入するにはどうすればいいですか?

Media Services v3 のライブ エンコードでは、ライブ ストリーミング中の動画またはイメージ スレートの挿入はまだサポートされていません。 

[ライブ オンプレミス エンコーダー](recommended-on-premises-live-encoders.md)を使用すると、ソースの動画を切り替えることができます。 多くのアプリには、Telestream Wirecast、Switcher Studio (iOS)、OBS Studio (無料アプリ) など、ソースを切り替える機能が用意されています。

## <a name="content-protection"></a>コンテンツの保護

### <a name="should-i-use-aes-128-clear-key-encryption-or-a-drm-system"></a>AES-128 クリア キー暗号化と DRM システムのどちらを使用すべきですか?

AES 暗号化と DRM システムのどちらを使えばよいか迷うことがあります。 2 つのシステムの主な違いは、AES 暗号化ではコンテンツ キーが TLS でクライアントに送信されるため、キーが送信中に暗号化されますが、それ以外の暗号化は行われないことです (「クリア」)。 このため、コンテンツの復号化に使用されるキーは、クライアント プレーヤーがアクセスでき、クライアントのネットワーク トレースでプレーン テキストとして表示できます。 AES-128 クリア キー暗号化は、閲覧者が信頼できる相手である場合 (従業員が見るために社内で配信される企業ビデオの暗号化など) に適しています。

PlayReady、Widevine、FairPlay などすべての DRM システムでは、AES-128 クリア キーと比較してコンテンツの復号化に使用されるキーの暗号化レベルが高くなります。 TLS によって提供されるトランスポート レベルの暗号化に加え、コンテンツ キーが暗号化され、DRM ランタイムによって保護されるキーになります。 さらに、解読はオペレーティング システム レベルのセキュリティで保護された環境で行われるため、悪意のあるユーザーによる攻撃はより難しくなります。 閲覧者を信頼できない可能性があり、最高レベルのセキュリティが必要なユース ケースでは、DRM をお勧めします。

### <a name="how-do-i-show-a-video-to-only-users-who-have-a-specific-permission-without-using-azure-ad"></a>Azure AD を使用せずに特定のアクセス許可を持つユーザーにのみビデオを表示するにはどうすればいいですか?

Azure Active Directory (Azure AD) のような特定のトークン プロバイダーを使用する必要はありません。 非対称キー暗号化を使用して、独自の [JWT](https://jwt.io/) プロバイダー (いわゆる Secure Token Service (STS)) を作成できます。 カスタム STS では、ビジネス ロジックに基づいて要求を追加できます。

発行者、対象ユーザー、要求がすべて、JWT の内容と、`ContentKeyPolicy` で使用されている `ContentKeyPolicyRestriction` 値の間で完全に一致していることを確認します。

詳細については、「[Media Services 動的暗号化を使用してコンテンツを保護する](content-protection-overview.md)」を参照してください。

### <a name="how-and-where-did-i-get-a-jwt-token-before-using-it-to-request-a-license-or-key"></a>JWT トークンを使用してライセンスまたはキーを要求する前に、JWT トークンを入手する方法と入手できる場所を教えてください。

運用環境では、HTTPS 要求時に JWT トークンを発行する Secure Token Services (つまり、Web サービス) が必要です。 テスト環境では、[Program.cs](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithDRM/Program.cs) で定義されている `GetTokenAsync` メソッドに示されているコードを使用できます。

プレーヤーは、ユーザーが認証された後、そのトークンの要求を STS に対して作成し、それをトークンの値として割り当てます。 [Azure Media Player API](https://amp.azure.net/libs/amp/latest/docs/) を使用できます。

対称キーと非対称キーのどちらかを使用して STS を実行する例については、「[JWT ツール](https://aka.ms/jwt)」を参照してください。 このような JWT トークンを使用する Azure Media Player に基づくプレーヤーの例については、「[Azure メディア テスト ツール](https://aka.ms/amtest)」を参照してください。 (**player_settings** リンクを展開するとトークンの入力が表示されます。)

### <a name="how-do-i-authorize-requests-to-stream-videos-with-aes-encryption"></a>AES 暗号化を使用してビデオをストリームする要求を承認する方法を教えてください。

正しいアプローチは、セキュリティ トークン サービスを使用することです。 STS では、ユーザー プロファイルに応じて、異なる要求を追加します ("Premium ユーザー"、"Basic ユーザー"、"無料試用ユーザー" など)。 JWT の要求に応じて、ユーザーには異なるコンテンツが表示されます。 コンテンツまたはアセットが異なる場合、`ContentKeyPolicyRestriction` には対応する `RequiredClaims` 値が設定されます。

Azure Media Services API シリーズを使用して、ライセンス/キー配信の構成とご自分の資産の暗号化を行います ([このサンプル](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithAES/Program.cs)を参照してください)。

詳細については、次を参照してください。

- [コンテンツ保護の概要](content-protection-overview.md)
- [アクセス制御を使用したマルチ DRM コンテンツ保護システムの設計](design-multi-drm-system-with-access-control.md)

### <a name="should-i-use-http-or-https"></a>HTTP と HTTPS のどちらを使用する必要がありますか?
ASP.NET MVC プレーヤー アプリケーションは、以下をサポートする必要があります。

* Azure AD によるユーザー認証 (HTTPS の場合)。
* クライアントと Azure AD 間での JWT の交換 (HTTPS の場合)。
* クライアントによる DRM ライセンスの取得。ライセンス配信が Media Services によって提供される場合は、HTTPS で必要です。 PlayReady 製品スイートでは、ライセンス配信に HTTPS を必要としません。 PlayReady ライセンス サーバーが Media Services の外部にある場合は、HTTP または HTTPS を使うことができます。

ASP.NET のプレーヤー アプリケーションはベスト プラクティスとして HTTPS を使うので、Media Player はページの HTTPS の下にあります。 ただし、ストリーミングでは HTTP が優先されるので、混合コンテンツの問題を考慮する必要があります。

* ブラウザーでは混合コンテンツは許可されません。 ただし、Silverlight や、Smooth および DASH 用の OSMF プラグインでは許可されます。 顧客データが危険にさらされかねない悪意のある JavaScript を挿入できる機能の脅威があるため、混合コンテンツにはセキュリティ上の問題があります。 ブラウザーは、既定でこの機能をブロックします。 これを回避する唯一の方法は、サーバー (配信元) 側で (HTTPS か HTTP かに関係なく) すべてのドメインを許可することです。 これはおそらくよい方法ではありません。
* 混合コンテンツは避ける必要があります。 プレーヤー アプリケーションと Media Player の両方で、HTTP または HTTPS を使う必要があります。 混合コンテンツを再生する際、SilverlightSS テクノロジは混合コンテンツの警告をクリアする必要があります。 FlashSS テクノロジは、混合コンテンツ警告なしで混合コンテンツを処理します。
* 2014 年 8 月より前に作成されたストリーミング エンドポイントは、HTTPS をサポートしません。 その場合は、HTTPS 用に新しいストリーミング エンドポイントを作成して使います。

### <a name="what-about-live-streaming"></a>ライブ ストリーミングの場合

プログラムに関連付けられた資産を VOD 資産として扱うことにより、Media Services のライブ ストリーミングを保護するために、まったく同じ設計と実装を使用できます。 ライブ コンテンツのマルチ DRM 保護を提供するには、資産をライブ出力に関連付ける前に、あたかもそれが VOD 資産であるかのように、その資産に対して同じセットアップ/処理を適用します。

### <a name="what-about-license-servers-outside-media-services"></a>Media Services の外部にあるライセンス サーバーの場合

多くの場合、ユーザーは自前のデータ センター内にあるライセンス サーバー ファーム、または DRM サービス プロバイダーによってホストされているライセンス サーバー ファームを運用してきました。 Media Services コンテンツ保護の場合、ハイブリッド モードで運用できます。 コンテンツは Media Services でホストして動的に保護することができ、DRM ライセンスは Media Services の外部のサーバーによって配信されます。 この例では、次のように変更することを考えます。

* STS は、ライセンス サーバー ファームによって許容可能で検証できるトークンを発行する必要があります。 たとえば、Axinom によって提供される Widevine ライセンス サーバーには、権利メッセージを含む特定の JWT が必要です。 STS でそのような JWT を発行しなくてはなりません。 
* Media Services でライセンス配信サービスを構成する必要はもうありません。 必要なのは、`ContentKeyPolicy` を構成するときに、ライセンス取得 URL を (PlayReady、Widevine、FairPlay に) 提供することです。

> [!NOTE]
> Widevine は Google によって提供されるサービスであり、Google の利用規約とプライバシー ポリシーが適用されます。

## <a name="media-services-v2-vs-v3"></a>Media Services v2 対 v3 

### <a name="can-i-use-the-azure-portal-to-manage-v3-resources"></a>Azure portal を使用して v3 リソースを管理することはできますか?

現時点では、[Azure portal](https://portal.azure.com/) を使って次のことを実行できます。

* Media Services v3 で [ライブ イベント](live-events-outputs-concept.md)を管理します。 
* v3 [アセット](assets-concept.md)を表示します (管理ではありません)。 
* [API のアクセスに関する情報を取得](./access-api-howto.md)します。 

他のすべての管理タスク ([変換とジョブ](transforms-jobs-concept.md)や[コンテンツ保護](content-protection-overview.md)など) については、[REST API](/rest/api/media/)、[Azure CLI](https://aka.ms/ams-v3-cli-ref)、またはサポートされているいずれかの [SDK](media-services-apis-overview.md#sdks) を使用します。

### <a name="is-there-an-assetfile-concept-in-v3"></a>v3 には AssetFile の概念はありますか?

Storage SDK の依存関係から Media Services を切り離すために、`AssetFile` は Media Services API から削除されました。 現在、Media Services ではなく、Azure Storage で Storage SDK に属する情報が保持されます。 

詳細については、「[Media Services v3 に移行する](media-services-v2-vs-v3.md)」を参照してください。

### <a name="where-did-client-side-storage-encryption-go"></a>クライアント側でのストレージ暗号化は利用できなくなったのですか?

現在、サーバー側のストレージ暗号化 (既定でオン) の使用が推奨されるようになりました。 詳細については、「[保存データ向け Azure Storage Service Encryption](../../storage/common/storage-service-encryption.md)」をご覧ください。

## <a name="offline-streaming"></a>オフライン ストリーミング

### <a name="fairplay-streaming-for-ios"></a>iOS 用の FairPlay ストリーミング

iOS 用のオフライン FairPlay ストリーミングに関するトラブルシューティングには、次のよく寄せられる質問が役に立ちます。

#### <a name="why-does-only-audio-play-but-not-video-during-offline-mode"></a>オフライン モードの間は、オーディオのみが再生されて、ビデオは再生されないのはなぜですか。

この動作は、サンプル アプリの本来の設計のようです。 代替オーディオ トラックがある場合 (HLS の場合)、オフライン モードでは、iOS 10 と iOS 11 の両方が、既定で代替オーディオ トラックになります。FPS オフライン モードでこの動作を補正するには、ストリームから代替オーディオ トラックを削除します。 Media Services でこれを行うには、動的マニフェスト フィルター **audio-only=false** を追加します。 つまり、HLS URL の最後が **.ism/manifest(format=m3u8-aapl,audio-only=false)** になります。 

#### <a name="why-does-it-still-play-audio-only-without-video-during-offline-mode-after-i-add-audio-onlyfalse"></a>audio-only=false を追加した後もまだ、オフライン モードの間は、オーディオのみが再生されて、ビデオが再生されないのはなぜですか。

コンテンツ配信ネットワークのキャッシュ キーの設計によっては、コンテンツがキャッシュされることがあります。 キャッシュを消去します。

#### <a name="is-fps-offline-mode-supported-on-ios-11-in-addition-to-ios-10"></a>FPS オフライン モードは、iOS 10 だけでなく iOS 11 でもサポートされますか?

はい。 FPS オフライン モードは iOS 10 と iOS 11 でサポートされています。

#### <a name="why-cant-i-find-the-document-offline-playback-with-fairplay-streaming-and-http-live-streaming-in-the-fps-server-sdk"></a>ドキュメント『FairPlay ストリーミングと HTTP ライブ ストリーミングでのオフライン再生』が FPS Server SDK に見つからないのはなぜですか。

FPS Server SDK バージョン 4 以降、このドキュメントは『FairPlay Streaming Programming Guide』(FairPlay Streaming プログラミング ガイド) にまとめられています。

#### <a name="what-is-the-downloadedoffline-file-structure-on-ios-devices"></a>iOS デバイスでのダウンロード済み/オフライン ファイルの構造はどのようなものですか。

iOS デバイスにダウンロードされたファイルは、次のスクリーンショットのような構造になっています。 `_keys` フォルダーには、ダウンロードされた FPS ライセンスが格納されます (ライセンス サービス ホストごとに 1 つのストア ファイル)。 `.movpkg` フォルダーには、オーディオとビデオのコンテンツが格納されます。 

名前の末尾にダッシュと数字が付いている最初のフォルダーには、ビデオ コンテンツが含まれています。 数値はビデオ再生の最大帯域幅です。 名前の末尾にダッシュと 0 が付いている 2 つ目のフォルダーには、オーディオ コンテンツが格納されます。 `Data` という名前の 3 番目のフォルダーには、FPS コンテンツのマスター再生リストが含まれます。 最後に、boot.xml には、`.movpkg` フォルダーの内容が完全に説明されています。 

![FairPlay の iOS サンプル アプリのオフライン ファイルの構造](media/offline-fairplay-for-ios/offline-fairplay-file-structure.png)

以下は boot.xml ファイルのサンプルです。

```xml
<?xml version="1.0" encoding="UTF-8"?>
<HLSMoviePackage xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" xmlns="http://apple.com/IMG/Schemas/HLSMoviePackage" xsi:schemaLocation="http://apple.com/IMG/Schemas/HLSMoviePackage /System/Library/Schemas/HLSMoviePackage.xsd">
  <Version>1.0</Version>
  <HLSMoviePackageType>PersistedStore</HLSMoviePackageType>
  <Streams>
    <Stream ID="1-4DTFY3A3VDRCNZ53YZ3RJ2NPG2AJHNBD-0" Path="1-4DTFY3A3VDRCNZ53YZ3RJ2NPG2AJHNBD-0" NetworkURL="https://willzhanmswest.streaming.mediaservices.windows.net/e7c76dbb-8e38-44b3-be8c-5c78890c4bb4/MicrosoftElite01.ism/QualityLevels(127000)/Manifest(aac_eng_2_127,format=m3u8-aapl)">
      <Complete>YES</Complete>
    </Stream>
    <Stream ID="0-HC6H5GWC5IU62P4VHE7NWNGO2SZGPKUJ-310656" Path="0-HC6H5GWC5IU62P4VHE7NWNGO2SZGPKUJ-310656" NetworkURL="https://willzhanmswest.streaming.mediaservices.windows.net/e7c76dbb-8e38-44b3-be8c-5c78890c4bb4/MicrosoftElite01.ism/QualityLevels(161000)/Manifest(video,format=m3u8-aapl)">
      <Complete>YES</Complete>
    </Stream>
  </Streams>
  <MasterPlaylist>
    <NetworkURL>https://willzhanmswest.streaming.mediaservices.windows.net/e7c76dbb-8e38-44b3-be8c-5c78890c4bb4/MicrosoftElite01.ism/manifest(format=m3u8-aapl,audio-only=false)</NetworkURL>
  </MasterPlaylist>
  <DataItems Directory="Data">
    <DataItem>
      <ID>CB50F631-8227-477A-BCEC-365BBF12BCC0</ID>
      <Category>Playlist</Category>
      <Name>master.m3u8</Name>
      <DataPath>Playlist-master.m3u8-CB50F631-8227-477A-BCEC-365BBF12BCC0.data</DataPath>
      <Role>Master</Role>
    </DataItem>
  </DataItems>
</HLSMoviePackage>
```

### <a name="widevine-streaming-for-android"></a>Android 用の Widevine ストリーミング

#### <a name="how-can-i-deliver-persistent-licenses-offline-enabled-for-some-clientsusers-and-non-persistent-licenses-offline-disabled-for-others-do-i-have-to-duplicate-the-content-and-use-separate-content-keys"></a>一部のクライアント/ユーザーには永続ライセンス (オフライン有効) を提供し、他のクライアント/ユーザーには非永続ライセンス (オフライン無効) を提供するにはどうすればよいですか? コンテンツを複製し、別のコンテンツ キーを使う必要がありますか?

Media Services v3 では、1 つのアセットに複数の `StreamingLocator` インスタンスを含められるため、以下を持つことができます。

* `license_type = "persistent"`、`"persistent"` の要求を伴う `ContentKeyPolicyRestriction`、その `StreamingLocator` を含む 1 つの `ContentKeyPolicy` インスタンス。
* `license_type="nonpersistent"`、`"nonpersistent`" の要求を伴う `ContentKeyPolicyRestriction`、その `StreamingLocator` を含む別の `ContentKeyPolicy` インスタンス。
* 2 つの `StreamingLocator` インスタンスには異なる `ContentKey` 値が含まれています。

カスタム STS のビジネス ロジックに応じて、さまざまな要求が JWT トークンで発行されます。 トークンを使用して、対応するライセンスのみ取得でき、対応する URL のみを再生できます。

#### <a name="what-is-the-mapping-between-the-widevine-and-media-services-drm-security-levels"></a>Widevine と Media Services DRM のセキュリティ レベルはどのように対応しますか。

Google の「Widevine DRM Architecture Overview」 (Widevine DRM アーキテクチャの概要) では、3 つのセキュリティ レベルが定義されています。 ただし、「[Widevine ライセンス テンプレートに関するAzure Media Services ドキュメント](widevine-license-template-overview.md)」では、5 つのセキュリティ レベル (再生に必要なクライアント信頼性要件) について概説しています。 ここでは、セキュリティ レベルがどのように対応するかについて説明します。

どちらのセキュリティ レベルも、Google Widevine によって定義されます。 違いは、その使用レベル (アーキテクチャまたは API) にあります。 Widevine API では、5 つのセキュリティ レベルが使われています。 `security_level` を含む `content_key_specs` オブジェクトは、Azure Media Services Widevine ライセンス サービスにより逆シリアル化され、Widevine グローバル配信サービスに渡されます。 次の表は、2 つのセキュリティ レベル セットの間のマッピングを示しています。

| **Widevine アーキテクチャで定義されたセキュリティ レベル** |**Widevine API で使用されるセキュリティ レベル**|
|---|---| 
| **セキュリティ レベル 1**:すべてのコンテンツの処理、暗号化、および管理は、信頼できる実行環境 (TEE) 内で実行されます。 一部の実装モデルでは、セキュリティ処理が異なるチップで実行される場合があります。|**security_level=5**:暗号化、デコード、およびメディア (圧縮済みおよび圧縮解除済み) のすべての処理を、ハードウェアを基盤にした TEE で実行する必要があります。<br/><br/>**security_level=4**:コンテンツの暗号化とデコードを、ハードウェアを基盤にした TEE で実行する必要があります。|
**セキュリティ レベル 2**:暗号化は TEE 内で実行されます (ビデオ処理は実行されません)。 解読されたバッファーはアプリケーション ドメインに返され、別のビデオ ハードウェアまたはソフトウェアによって処理されます。 ただし、レベル 2 では、暗号化に関する情報はやはり TEE 内でのみ処理されます。| **security_level=3**:キー マテリアルと暗号化の操作を、ハードウェアを基盤にした TEE で実行する必要があります。 |
| **セキュリティ レベル 3**:デバイスに TEE はありません。 ホスト オペレーティング システム上の暗号化情報と解読されたコンテンツを保護するため、適切な手段が実行される場合があります。 レベル 3 では、ハードウェア暗号化エンジンも実装に含まれる場合がありますが、セキュリティのためではなく、パフォーマンス向上のみを目的としています。 | **security_level=2**:ソフトウェア暗号化と難読化デコーダーが必須です。<br/><br/>**security_level=1**:ソフトウェアベースのホワイトボックス暗号化が必須です。|

#### <a name="why-does-content-download-take-so-long"></a>コンテンツのダウンロードに時間がかかるのはなぜですか。

ダウンロードの速度を上げるには 2 つの方法があります。

* ユーザーがコンテンツ ダウンロードの起点/ストリーミング エンドポイントではなくコンテンツ配信ネットワークをヒットする可能性が高くなるように、コンテンツ配信ネットワークを有効にします。 ユーザーがストリーミング エンドポイントにヒットした場合、各 HLS セグメントまたは DASH フラグメントは、動的にパッケージ化および暗号化されます。 この待機時間は各セグメント/フラグメントでミリ秒の単位ですが、ビデオの長さが 1 時間になると、待機時間が蓄積して長いダウンロードの原因になる可能性があります。
* ユーザーが、すべてのコンテンツではなく、ビデオ品質レイヤーとオーディオ トラックを選んでダウンロードできるようにします。 オフライン モードでは、すべての品質レイヤーをダウンロードする必要はありません。 これを実現する方法は 2 つあります。

  * クライアントによる制御:ダウンロードするビデオ品質レイヤーとオーディオ トラックを、プレーヤー アプリが自動的に選ぶか、ユーザーが選びます。
  * サービスによる制御:Azure Media Services の動的マニフェスト機能を使って (グローバル) フィルターを作成し、HLS 再生リストまたは DASH MPD を、単一のビデオ品質レイヤーと選ばれたオーディオ トラックに制限することができます。 その後、ユーザーに提示されるダウンロード URL には、このフィルターが含まれます。

## <a name="next-steps"></a>次のステップ

[Media Services v3 の概要](media-services-overview.md)
