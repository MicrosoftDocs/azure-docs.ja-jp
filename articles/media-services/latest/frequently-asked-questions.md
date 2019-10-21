---
title: Azure Media Services v3 のよく寄せられる質問 | Microsoft Docs
description: この記事では、Azure Media Services v3 のよく寄せられる質問に対する回答を提供します。
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 06/21/2019
ms.author: juliako
ms.openlocfilehash: 6e52a51c82529a98ef679dd747b9c8b7d177b660
ms.sourcegitcommit: e1b6a40a9c9341b33df384aa607ae359e4ab0f53
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/27/2019
ms.locfileid: "71338831"
---
# <a name="media-services-v3-frequently-asked-questions"></a>Media Services v3 のよく寄せられる質問

この記事では、Azure Media Services (AMS) v3 のよく寄せられる質問に対する回答を提供します。

## <a name="general"></a>全般

### <a name="what-azure-roles-can-perform-actions-on-azure-media-services-resources"></a>Azure Media Services リソースに対してアクションを実行できる Azure のロールは何ですか。 

「[Media Services アカウント用のロールベースのアクセス制御 (RBAC)](rbac-overview.md)」を参照してください。

### <a name="how-do-i-configure-media-reserved-units"></a>メディア占有ユニットの構成方法を教えてください。

Media Services v3 または Video Indexer によってトリガーされる音声分析と動画分析ジョブでは、お使いのアカウントを 10 個の S3 の MRU でプロビジョニングすることを強くお勧めします。 10 個を超える S3 の MRU が必要な場合は、[Azure portal](https://portal.azure.com/) を使用してサポート チケットを開いてください。

詳細については、「[CLI を使用したメディア処理のスケーリング](media-reserved-units-cli-how-to.md)」を参照してください。

### <a name="what-is-the-recommended-method-to-process-videos"></a>推奨される動画の処理方法は?

[Transform](https://docs.microsoft.com/rest/api/media/transforms) を使用して、ビデオのエンコードや分析を行うための一般的なタスクを構成できます。 各 **Transform** は、ビデオまたはオーディオ ファイルを処理するためのレシピ､すなわちタスクのワークフローの記述です｡ [ジョブ](https://docs.microsoft.com/rest/api/media/jobs)は、特定の入力ビデオまたはオーディオ コンテンツに **Transform** を適用する、Media Services への実際の要求です。 Transform を作成すると､Media Services API または公開されている任意の SDK を使用してジョブを送信できます｡ 詳しくは、「[Transform と Job](transforms-jobs-concept.md)」をご覧ください。

### <a name="how-does-pagination-work"></a>改ページはどのように機能しますか?

改ページを使用している場合は、常に次のリンクを使用してコレクションを列挙し、特定のページ サイズには依存しないようにする必要があります。 詳細および例については、[フィルター処理、順序付け、ページング](entities-overview.md)に関するページを参照してください。

### <a name="what-features-are-not-yet-available-in-azure-media-services-v3"></a>Azure Media Services v3 では、どのような機能がまだ利用可能になっていますか。

詳細については、「[v2 API に関する機能のギャップ](migrate-from-v2-to-v3.md#feature-gaps-with-respect-to-v2-apis)」を参照してください。

### <a name="what-is-the-process-of-moving-a-media-services-account-between-subscriptions"></a>サブスクリプション間での Media Services アカウントの移動のプロセスはどのようなものですか?  

詳細については、「[サブスクリプション間での Media Services アカウントの移動](media-services-account-concept.md)」を参照してください。

## <a name="live-streaming"></a>ライブ ストリーミング 

###  <a name="how-to-insert-breaksvideos-and-image-slates-during-live-stream"></a>ライブ ストリーミング中に中断 / 動画またはイメージ スレートを挿入するにはどうすればいいですか?

Media Services v3 のライブ エンコードでは、ライブ ストリーミング中の動画またはイメージ スレートの挿入はまだサポートされていません。 

[ライブ オンプレミス エンコーダー](recommended-on-premises-live-encoders.md)を使用すると、ソースの動画を切り替えることができます。 多くのアプリには、Telestream Wirecast、Switcher Studio (iOS 上)、OBS Studio (無料アプリ) など、ソースを切り替える機能が用意されています。

## <a name="content-protection"></a>コンテンツ保護

### <a name="should-i-use-an-aes-128-clear-key-encryption-or-a-drm-system"></a>AES-128 クリア キー暗号化と DRM システムのどちらを使用すべきでしょぅか。

AES 暗号化と DRM システムのどちらを使えばよいか迷うことがあります。 2 つのシステムの主な違いは、AES 暗号化ではコンテンツ キーが TLS でクライアントに送信されるため、キーが送信中に暗号化されますが、それ以外の暗号化は行われないことです (クリア)。 その結果、コンテンツの復号化に使用されるキーは、クライアント プレーヤーがアクセスでき、クライアントのネットワーク トレースでプレーンテキストとして表示できます。 AES-128 クリア キー暗号化は、閲覧者が信頼できる相手である場合 (従業員が見るために社内で配信される企業ビデオの暗号化など) に適しています。

PlayReady、Widevine、FairPlay など DRM システムではすべて、コンテンツの復号化に使用されるキーの暗号化のレベルが、AES-128 クリア キーと比較して高くなります。 TLS によって提供されるトランスポート レベルの暗号化に加え、コンテンツ キーが暗号化され、DRM ランタイムによって保護されるキーになります。 さらに、解読はオペレーティング システム レベルのセキュリティで保護された環境で行われるため、悪意のあるユーザーによる攻撃はより難しくなります。 閲覧者が信頼できない可能性があり、最高レベルのセキュリティを必要とするユース ケースでは、DRM をお勧めします。

### <a name="how-to-show-a-video-only-to-users-who-have-a-specific-permission-without-using-azure-ad"></a>Azure AD を使用せずに特定のアクセス許可を持つユーザーにのみビデオを表示する方法

特定のトークン プロバイダー (Azure AD など) を使用する必要はありません。 非対称キー暗号化を使用して、独自の [JWT](https://jwt.io/) プロバイダー (いわゆる Secure Token Service (STS)) を作成できます。 カスタム STS では、ビジネス ロジックに基づいて要求を追加できます。

発行者、対象ユーザー、および要求がすべて、JWT の内容と、ContentKeyPolicy で使用されている ContentKeyPolicyRestriction の間で完全に一致していることを確認します。

詳細については、「[Media Services 動的暗号化を使用してコンテンツを保護する](content-protection-overview.md)」を参照してください。

### <a name="how-and-where-to-get-jwt-token-before-using-it-to-request-license-or-key"></a>JWT トークンを使用してライセンスまたはキーを要求する前に、JWT トークンを入手する方法と入手できる場所を教えてください。

1. 運用環境では、HTTPS 要求時に JWT トークンを発行する Secure Token Services (STS) (Web サービス) が必要です。 テスト環境では、[Program.cs](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithDRM/Program.cs) で定義されている **GetTokenAsync** メソッドに示されているコードを使用できます。
2. プレーヤーは、ユーザーが認証された後、そのようなトークンの要求を STS に対して作成し、それをトークンの値として割り当てる必要があります。 [Azure Media Player API](https://amp.azure.net/libs/amp/latest/docs/) を使用できます。

* 対称キーと非対称キーのどちらかを使用して STS を実行する例については、[https://aka.ms/jwt](https://aka.ms/jwt) を参照してください。 
* このような JWT トークンを使用する Azure Media Player に基づくプレーヤーの例については、[https://aka.ms/amtest](https://aka.ms/amtest) を参照してください ("player_settings" リンクを展開すると、トークンの入力が表示されます)。

### <a name="how-do-you-authorize-requests-to-stream-videos-with-aes-encryption"></a>AES 暗号化を使用してビデオをストリームする要求を承認する方法を教えてください。

正しいアプローチは、STS (セキュリティ トークン サービス) を活用することです。

STS では、ユーザー プロファイルに応じて、異なる要求 ("Premium ユーザー"、"Basic ユーザー"、"無料試用ユーザー" など) を追加します。 JWT の要求に応じて、ユーザーには異なるコンテンツが表示されます。 当然ながら、異なるコンテンツ/資産の場合、ContentKeyPolicyRestriction には対応する RequiredClaims があります。

Azure Media Services API シリーズを使用して、ライセンス/キー配信の構成とご自分の資産の暗号化を行います ([このサンプル](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithAES/Program.cs)を参照してください)。

詳細については、次を参照してください。

- [コンテンツ保護の概要](content-protection-overview.md)
- [アクセス制御を使用したマルチ DRM コンテンツ保護システムの設計](design-multi-drm-system-with-access-control.md)

### <a name="http-or-https"></a>HTTP か HTTPS か
ASP.NET MVC プレーヤー アプリケーションは、以下をサポートする必要があります。

* Azure AD によるユーザー認証 (HTTPS の場合)。
* クライアントと Azure AD 間での JWT の交換 (HTTPS の場合)。
* クライアントによる DRM ライセンスの取得。ライセンス配信が Media Services によって提供される場合は、HTTPS で必要です。 PlayReady 製品スイートでは、ライセンス配信に HTTPS を必要としません。 PlayReady ライセンス サーバーが Media Services の外部にある場合は、HTTP または HTTPS を使うことができます。

ASP.NET のプレーヤー アプリケーションはベスト プラクティスとして HTTPS を使うので、Media Player はページの HTTPS の下にあります。 ただし、ストリーミングには HTTP が優先されるので、混合コンテンツの問題を考慮する必要があります。

* ブラウザーでは混合コンテンツは許可されません。 ただし、Silverlight や、Smooth および DASH 用の OSMF プラグインでは許可されます。 顧客データが危険にさらされる可能性がある悪意のある JavaScript を挿入する機能の脅威にため、混合コンテンツにはセキュリティ上の問題があります。 ブラウザーは、既定でこの機能をブロックします。 これを回避する唯一の方法は、サーバー (配信元) 側で (HTTPS か HTTP かに関係なく) すべてのドメインを許可することです。 これはおそらくよい方法ではありません。
* 混合コンテンツは避ける必要があります。 プレーヤー アプリケーションと Media Player の両方で、HTTP または HTTPS を使う必要があります。 混合コンテンツを再生するとき、silverlightSS テクノロジは混合コンテンツの警告をクリアする必要があります。 flashSS テクノロジは、混合コンテンツ警告なしで混合コンテンツを処理します。
* 2014 年 8 月より前に作成されたストリーミング エンドポイントは、HTTPS をサポートしません。 その場合は、HTTPS 用に新しいストリーミング エンドポイントを作成して使います。

### <a name="what-about-live-streaming"></a>ライブ ストリーミングの場合

プログラムに関連付けられた資産を VOD 資産として扱うことにより、Media Services のライブ ストリーミングでもまったく同じ設計と実装を使用できます。 ライブ コンテンツのマルチ DRM 保護を提供するには、資産をライブ出力に関連付ける前に、あたかもそれが VOD 資産であるかのように、その資産に対して同じセットアップ/処理を適用します。

### <a name="what-about-license-servers-outside-media-services"></a>Media Services の外部にあるライセンス サーバーの場合

多くの場合、ユーザーは自前のデータ センター内にあるライセンス サーバー ファーム、または DRM サービス プロバイダーによってホストされているライセンス サーバー ファームを運用しています。 Media Services コンテンツ保護の場合、ハイブリッド モードで運用できます。 コンテンツは Media Services でホストして動的に保護することができ、DRM ライセンスは Media Services の外部のサーバーによって配信されます。 この例では、次のように変更することを考えます。

* STS は、ライセンス サーバー ファームによって許容可能で検証できるトークンを発行する必要があります。 たとえば、Axinom によって提供される Widevine ライセンス サーバーには、権利メッセージを含む特定の JWT が必要です。 そのため、STS でそのような JWT を発行する必要があります。 
* Media Services でライセンス配信サービスを構成する必要はもうありません。 必要なのは、ContentKeyPolicies を構成するときに、ライセンス取得 URL を (PlayReady、Widevine、FairPlay に) 提供することです。

## <a name="media-services-v2-vs-v3"></a>Media Services v2 対 v3 

### <a name="can-i-use-the-azure-portal-to-manage-v3-resources"></a>Azure portal を使用して v3 リソースを管理することはできますか?

現時点では、Azure portal を使用して v3 リソースを管理することはできません。 [REST API](https://aka.ms/ams-v3-rest-ref)、[CLI](https://aka.ms/ams-v3-cli-ref)、またはサポートされている [SDK](media-services-apis-overview.md#sdks) のいずれかを使用してください。

### <a name="is-there-an-assetfile-concept-in-v3"></a>v3 には AssetFile の概念はありますか?

Storage SDK の依存関係から Media Services を切り離すために、AssetFile は AMS API から削除されました。 現在、Media Services ではなく、Storage でそれに属する情報が保持されます。 

詳細については、「[Media Services v3 に移行する](migrate-from-v2-to-v3.md)」を参照してください。

### <a name="where-did-client-side-storage-encryption-go"></a>クライアント側でのストレージ暗号化は利用できなくなったのですか?

現在、サーバー側のストレージ暗号化 (既定でオンになる) の使用が推奨されるようになりました。 詳細については、「[保存データ向け Azure Storage Service Encryption](https://docs.microsoft.com/azure/storage/common/storage-service-encryption)」をご覧ください。

## <a name="next-steps"></a>次の手順

[Media Services v3 の概要](media-services-overview.md)
