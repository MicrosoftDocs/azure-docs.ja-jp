---
title: Widevine で保護されたコンテンツのオフライン ストリーミング用にアカウントを構成する - Azure
description: このトピックでは、Widevine で保護されたコンテンツのオフライン ストリーミング用に Azure Media Services アカウントを構成する方法を示します。
services: media-services
keywords: DASH, DRM, Widevine オフライン モード, ExoPlayer, Android
documentationcenter: ''
author: willzhan
manager: steveng
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/16/2019
ms.author: willzhan
ms.reviewer: dwgeo
ms.openlocfilehash: 694cdf054f74db50bcf1781e60df0f93810ae60c
ms.sourcegitcommit: de47a27defce58b10ef998e8991a2294175d2098
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/15/2019
ms.locfileid: "67875190"
---
# <a name="offline-widevine-streaming-for-android"></a>Android 用のオフラインの Widevine ストリーミング  

> [!div class="op_single_selector" title1="使用している Media Services のバージョンを選択してください:"]
> * [Version 3](../latest/offline-widevine-for-android.md)
> * [Version 2](offline-widevine-for-android.md)

> [!NOTE]
> Media Services v2 には新機能は追加されません。 <br/>最新のバージョンである [Media Services v3](https://docs.microsoft.com/azure/media-services/latest/) をご確認ください。 また、[v2 から v3 への移行ガイダンス](../latest/migrate-from-v2-to-v3.md)を参照してください。

オンライン ストリーミング用にコンテンツを保護するだけでなく、メディア コンテンツ サブスクリプション サービスとレンタル サービスは、インターネットに接続していないときに視聴できるダウンロード可能なコンテンツを提供します。 ネットワークから切断されて飛行中に機内モードで再生するには、お使いの携帯電話やタブレットにコンテンツをダウンロードすることが必要な場合があります。 コンテンツのダウンロードが必要になることがあるシナリオとしては、他に次のような場合があります。

- コンテンツ プロバイダーによっては、国/地域の境を超えた DRM ライセンス配信を許可しないことがあります。 ユーザーが外国旅行中にコンテンツの視聴を望む場合は、オフライン ダウンロードが必要です。
- 一部の国/地域では、インターネットの使用や帯域幅が制限されています。 ユーザーはコンテンツをダウンロードすることで、満足できる十分に高い解像度でコンテンツを視聴できます。

この記事では、Android デバイスで Widevine によって保護された DASH コンテンツのオフライン モード再生を実装する方法について説明します。 オフライン DRM では、コンテンツのサブスクリプション モデル、レンタル モデル、購入モデルを提供することができ、サービスの利用者はインターネットから切断しているときに簡単にコンテンツを利用できます。

Android Player アプリを構築するには、3 つのオプションがあります。

> [!div class="checklist"]
> * ExoPlayer SDK の Java API を使ってプレーヤーを構築します
> * ExoPlayer SDK の Xamarin バインドを使ってプレーヤーを構築します
> * Chrome モバイル ブラウザー v62 以降の Encrypted Media Extension (EME) と Media Source Extension (MSE) を使ってプレーヤーを構築します

この記事では、Widevine で保護されたコンテンツのオフライン ストリーミングに関連する一般的な質問にもお答えします。

## <a name="requirements"></a>必要条件 

Android デバイスに Widevine 用のオフライン DRM を実装する前に、次のことを行っておく必要があります。

- Widevine DRM を使ったオンライン コンテンツ保護に対して導入される概念をよく理解します。 これについては、次のドキュメント/サンプルで詳しく説明されています。
    - [Azure Media Services を使用して DRM ライセンスまたはAES キーを配信する](media-services-deliver-keys-and-licenses.md)
    - [CENC とマルチ DRM およびアクセス制御: Azure および Azure Media Services での参照設計と実装](media-services-cenc-with-multidrm-access-control.md)
    - [.NET で PlayReady または Widevine の動的共通暗号化を使用する](https://azure.microsoft.com/resources/samples/media-services-dotnet-dynamic-encryption-with-drm/)
    - [.NET で Azure Media Services を使用して PlayReady や Widevine のライセンスを配信する](https://azure.microsoft.com/resources/samples/media-services-dotnet-deliver-playready-widevine-licenses/)
- Widevine DRM のオフライン再生をサポートする機能を持つオープンソースのビデオ プレーヤー SDK である Google ExoPlayer SDK for Android についてよく理解します。 
    - [ExoPlayer SDK](https://github.com/google/ExoPlayer)
    - [ExoPlayer 開発者ガイド](https://google.github.io/ExoPlayer/guide.html)
    - [EoPlayer 開発者ブログ](https://medium.com/google-exoplayer)

## <a name="content-protection-configuration-in-azure-media-services"></a>Azure Media Services でのコンテンツ保護の構成

Media Services で資産の Widevine 保護を構成するときは、次の 3 つのことが指定された ContentKeyAuthorizationPolicyOption を作成する必要があります。

1. DRM システム (Widevine)
2. ライセンス配信サービスでのコンテンツ キー配信の承認方法を指定する ContentKeyAuthorizationPolicyRestriction (オープンまたはトークン承認)
3. DRM (Widevine) ライセンス テンプレート

Widevine ライセンスの**オフライン** モードを有効にするには、[Widevine ライセンス テンプレート](media-services-widevine-license-template-overview.md)を構成する必要があります。 **policy_overrides** オブジェクトで、**can_persist** プロパティを **true** に設定します (既定値は false)。 

次のコード例では、.NET を使って Widevine ライセンスの**オフライン** モードを有効にしています。 このコードは、「[ Using PlayReady and/or Widevine Dynamic Common Encryption with .NET](https://github.com/Azure-Samples/media-services-dotnet-dynamic-encryption-with-drm)」(.NET で PlayReady または Widevine の動的共通暗号化を使用する) のサンプルが基になっています。 

```
private static string ConfigureWidevineLicenseTemplateOffline(Uri keyDeliveryUrl)
{
    var template = new WidevineMessage
    {
        allowed_track_types = AllowedTrackTypes.SD_HD,
        content_key_specs = new[]
        {
            new ContentKeySpecs
            {
                required_output_protection = new RequiredOutputProtection { hdcp = Hdcp.HDCP_NONE},
                security_level = 1,
                track_type = "SD"
            }
        },
        policy_overrides = new
        {
            can_play = true,
            can_persist = true,
            //can_renew = true,                             //if you set can_renew = false, you do not need renewal_server_url
            //renewal_server_url = keyDeliveryUrl.ToString(),   //not mandatory, renewal_server_url is needed only if license_duration_seconds is set
            can_renew = false,
            //rental_duration_seconds = 1209600,
            //playback_duration_seconds = 1209600,
            //license_duration_seconds = 1209600
        }
        };

    string configuration = Newtonsoft.Json.JsonConvert.SerializeObject(template);
    return configuration;
}
```

## <a name="configuring-the-android-player-for-offline-playback"></a>オフライン再生用の Android Player の構成

Android デバイス用のネイティブ プレーヤー アプリを開発する最も簡単な方法は、オープンソースのビデオ プレーヤー SDK である [Google ExoPlayer SDK](https://github.com/google/ExoPlayer) を使うことです。 ExoPlayer は、Android のネイティブ MediaPlayer API では現在サポートされていない、MPEG-DASH や Microsoft Smooth Streaming 配信プロトコルなどの機能をサポートしています。

ExoPlayer バージョン 2.6 以降には、Widevine DRM のオフライン再生をサポートする多くのクラスが含まれています。 具体的には、OfflineLicenseHelper クラスでは、オフライン ライセンスのダウンロード、更新、解放のための DefaultDrmSessionManager の使用を容易にするユーティリティ関数が提供されています。 SDK のフォルダー "library/core/src/main/java/com/google/android/exoplayer2/offline/" で提供されているクラスは、オフライン ビデオ コンテンツのダウンロードをサポートします。

ExoPlayer SDK for Android でのオフライン モードを容易にするクラスの一覧を次に示します。

- library/core/src/main/java/com/google/android/exoplayer2/drm/OfflineLicenseHelper.java  
- library/core/src/main/java/com/google/android/exoplayer2/drm/DefaultDrmSession.java
- library/core/src/main/java/com/google/android/exoplayer2/drm/DefaultDrmSessionManager.java
- library/core/src/main/java/com/google/android/exoplayer2/drm/DrmSession.java
- library/core/src/main/java/com/google/android/exoplayer2/drm/ErrorStateDrmSession.java
- library/core/src/main/java/com/google/android/exoplayer2/drm/ExoMediaDrm.java
- library/core/src/main/java/com/google/android/exoplayer2/offline/SegmentDownloader.java
- library/core/src/main/java/com/google/android/exoplayer2/offline/DownloaderConstructorHelper.java 
- library/core/src/main/java/com/google/android/exoplayer2/offline/Downloader.java
- library/dash/src/main/java/com/google/android/exoplayer2/source/dash/offline/DashDownloader.java 

開発者は、アプリケーションの開発中に、[ExoPlayer 開発者ガイド](https://google.github.io/ExoPlayer/guide.html)および対応する[開発者ブログ](https://medium.com/google-exoplayer)を参照する必要があります。 現時点では、Widevine オフラインをサポートする ExoPlayer アプリの完全に文書化された参照実装またはサンプル コードは Google からリリースされていないため、情報は開発者ガイドとブログに限られます。 

### <a name="working-with-older-android-devices"></a>古い Android デバイスの使用

一部の古い Android デバイスでは、**policy_overrides** のプロパティ **rental_duration_seconds**、**playback_duration_seconds**、**license_duration_seconds** の値を設定する必要があります ([Widevine ライセンス テンプレート](media-services-widevine-license-template-overview.md)で定義されています)。 または、これらを無期限/無制限の期間を意味する 0 に設定してもかまいません。  

整数オーバーフローのバグを回避するには、値を設定する必要があります。 この問題に関する詳しい説明については、 https://github.com/google/ExoPlayer/issues/3150 と https://github.com/google/ExoPlayer/issues/3112 を参照してください。 <br/>値を明示的に設定しない場合、**PlaybackDurationRemaining** および **LicenseDurationRemaining** に非常に大きな値が割り当てられます (たとえば 9223372036854775807、これは 64 ビット整数の正の最大値です)。 その結果、Widevine ライセンスは有効期限切れになり、解読は行われません。 

この問題は、Android 5.0 Lollipop 以降では発生しません。Android 5.0 は ARMv8 ([Advanced RISC Machine](https://en.wikipedia.org/wiki/ARM_architecture)) と 64 ビット プラットフォームを完全にサポートするように設計された最初の Android バージョンですが、Android 4.4 KitKat は他の古い Android バージョンと同じように ARMv7 と 32 ビット プラットフォームをサポートするようにもともと設計されていました。

## <a name="using-xamarin-to-build-an-android-playback-app"></a>Xamarin を使用して Android 再生アプリを作成する

ExoPlayer 用の Xamarin バインドについては以下のリンクをご覧ください。

- [Google ExoPlayer ライブラリ用の Xamarin バインド ライブラリ](https://github.com/martijn00/ExoPlayerXamarin)
- [ExoPlayer NuGet 用の Xamarin バインド](https://www.nuget.org/packages/Xam.Plugins.Android.ExoPlayer/)

次のスレッドもご覧ください。[Xamarin バインディング](https://github.com/martijn00/ExoPlayerXamarin/pull/57)。 

## <a name="chrome-player-apps-for-android"></a>Android 用の Chrome プレーヤー アプリ

[Chrome for Android v.62](https://developers.google.com/web/updates/2017/09/chrome-62-media-updates) のリリース以降では、EME での永続ライセンスがサポートされます。 [Widevine L1](https://developers.google.com/web/updates/2017/09/chrome-62-media-updates#widevine_l1) も、Chrome for Android でサポートされるようになっています。 これにより、エンド ユーザーがこのバージョン (またはそれ以降) の Chrome を使っている場合、Chrome でオフライン再生アプリケーションを作成できます。 

さらに、Google は Progressive Web App (PWA) のサンプルを生成し、それをオープンソース化しています。 

- [ソース コード](https://github.com/GoogleChromeLabs/sample-media-pwa)
- [Google ホスト バージョン](https://biograf-155113.appspot.com/ttt/episode-2/) (Android デバイス上の Chrome v 62 以降でのみ機能します)

Android フォンでモバイル Chrome ブラウザーを v62 (またはそれ以降) にアップグレードし、上記のホストされたサンプル アプリをテストした場合、オンライン ストリーミングとオフライン再生の両方が機能することを確認できます。

上記のオープンソース PWA アプリは、Node.js で作成されています。 独自のバージョンを Ubuntu サーバーでホストする場合は、再生を妨げる可能性のある、以下のよく発生する問題に留意してください。

1. CORS の問題:サンプル アプリ内のサンプル ビデオは、 https://storage.googleapis.com/biograf-video-files/videos/ でホストされます。 Google は、Google Cloud Storage バケットでホストされているすべてのテスト サンプル用に CORS を設定しています。 これらは CORS ヘッダーで提供され、CORS エントリ https://biograf-155113.appspot.com (Google がサンプルをホストしているドメイン) が明示的に指定されていて、他のサイトではアクセスできません。 アクセスしようとすると、次のような HTTP エラーが表示されます。Failed to load https://storage.googleapis.com/biograf-video-files/videos/poly-sizzle-2015/mp4/dash.mpd: No 'Access-Control-Allow-Origin' header is present on the requested resource. Origin 'https:\//13.85.80.81:8080' is therefore not allowed access. 非透過の応答が要求に対応している場合は、要求のモードを 'no-cors' に設定し、CORS を無効にしてリソースをフェッチしてください。
2. 証明書の問題:Chrome v 58 以降では、Widevine 用の EME には HTTPS が必要です。 したがって、X509 証明書を使って HTTPS 経由でサンプル アプリをホストする必要があります。 通常のテスト証明書は次の要件のため機能しません。次の最小要件を満たす証明書を取得する必要があります。
    - Chrome および Firefox では、SAN-Subject Alternative Name の設定が証明書に存在する必要があります
    - 証明書には信頼された CA が必要であり、開発用の自己署名証明書は機能しません
    - 証明書には、Web サーバーまたはゲートウェイの DNS 名と一致する CN が必要です。

## <a name="frequently-asked-questions"></a>よく寄せられる質問

### <a name="question"></a>質問

一部のクライアント/ユーザーには永続ライセンス (オフライン有効) を提供し、他のクライアント/ユーザーには非永続ライセンス (オフライン無効) を提供するにはどうすればよいですか。 コンテンツを複製し、別のコンテンツ キーを使う必要がありますか。

### <a name="answer"></a>Answer
コンテンツを複製する必要はありません。 コンテンツのコピーと ContentKeyAuthorizationPolicy は 1 つでかまいませんが、2 つの異なる ContentKeyAuthorizationPolicyOption が必要です。

1. IContentKeyAuthorizationPolicyOption 1: 永続的ライセンスと、license_type = "Persistent" などの要求を含む ContentKeyAuthorizationPolicyRestriction 1 を使います
2. IContentKeyAuthorizationPolicyOption 2: 非永続的ライセンスと、license_type = "Nonpersistent" などの要求を含む ContentKeyAuthorizationPolicyRestriction 2 を使います

このようにすると、クライアント アプリからライセンス要求を受け取るとき、ライセンスの要求に違いはありません。 ただし、異なるエンド ユーザー/デバイスの場合は、STS に、異なる要求 (上記の 2 つ license_type のいずれか) を含む異なる JWT トークンを発行するビジネス ロジックが必要です。 ライセンス サービスは、JWT トークンの要求値を使って、発行するライセンスの種類 (永続的または非永続的) を決定します。

つまり、Secure Token Service (STS) には、対応する要求値をトークンに追加するための、ビジネス ロジックとクライアント/デバイスに関する情報が必要です。

### <a name="question"></a>質問

Widevine のセキュリティ レベルについて、Google の「[Widevine DRM Architecture Overview](https://storage.googleapis.com/wvdocs/Widevine_DRM_Architecture_Overview.pdf)」(Widevine DRM アーキテクチャの概要) ドキュメントでは、3 つの異なるセキュリティ レベルが定義されています。 一方、[Widevine ライセンス テンプレートに関する Azure Media Services のドキュメント](https://docs.microsoft.com/azure/media-services/media-services-widevine-license-template-overview)では、5 つの異なるセキュリティ レベルが示されています。 2 つの異なるセキュリティ レベル セットの間にはどのような関係または対応がありますか。

### <a name="answer"></a>Answer

Google の「[Widevine DRM Architecture Overview](https://storage.googleapis.com/wvdocs/Widevine_DRM_Architecture_Overview.pdf)」(Widevine DRM アーキテクチャの概要) では、次の 3 つのセキュリティ レベルが定義されています。

1.  セキュリティ レベル 1:すべてのコンテンツの処理、暗号化、および管理は、信頼できる実行環境 (TEE) 内で実行されます。 一部の実装モデルでは、セキュリティ処理が異なるチップで実行される場合があります。
2.  セキュリティ レベル 2:暗号化は TEE 内で実行します (ビデオ処理は実行されません)。解読されたバッファーはアプリケーション ドメインに返され、別のビデオ ハードウェアまたはソフトウェアによって処理されます。 ただし、レベル 2 では、暗号化に関する情報はやはり TEE 内でのみ処理されます。
3.  セキュリティ レベル 3: デバイス上に TEE はありません。 ホスト オペレーティング システム上の暗号化に関する情報と解読されたコンテンツを保護するため、適切な手段が実行される場合があります。 レベル 3 の実装は、ハードウェア暗号化エンジンを含む場合がありますが、セキュリティのためではなく、パフォーマンス向上のためだけです。

同時に、[Widevine ライセンス テンプレートに関する Azure Media Services のドキュメント](https://docs.microsoft.com/azure/media-services/media-services-widevine-license-template-overview)では、content_key_specs の security_level プロパティは、次の 5 つの異なる値を持つことができるようになっています (再生のクライアント堅牢性の要件)。

1.  ソフトウェアベースのホワイトボックス暗号化が必須です。
2.  ソフトウェア暗号化と難読化デコーダーが必須です。
3.  キー マテリアルと暗号化の操作を、ハードウェアを基盤にした TEE で実行する必要があります。
4.  コンテンツの暗号化とデコードを、ハードウェアを基盤にした TEE で実行する必要があります。
5.  暗号化、デコード、およびメディア (圧縮済みおよび圧縮解除済み) のすべての処理を、ハードウェアを基盤にした TEE で実行する必要があります。

両方のセキュリティ レベルは、Google Widevine によって定義されています。 違いは、その使用レベル (アーキテクチャ レベルか API レベルか) にあります。 Widevine API では、5 つのセキュリティ レベルが使われています。 security_level を含む content_key_specs オブジェクトは、Azure Media Services Widevine ライセンス サービスにより、逆シリアル化されて Widevine グローバル配信サービスに渡されます。 次の表では、2 つのセキュリティ レベル セットの間の対応を示します。

| **Widevine アーキテクチャで定義されているセキュリティ レベル** |**Widevine API で使われるセキュリティ レベル**|
|---|---| 
| **セキュリティ レベル 1**:すべてのコンテンツの処理、暗号化、および管理は、信頼できる実行環境 (TEE) 内で実行されます。 一部の実装モデルでは、セキュリティ処理が異なるチップで実行される場合があります。|**security_level=5**:暗号化、デコード、およびメディア (圧縮済みおよび圧縮解除済み) のすべての処理を、ハードウェアを基盤にした TEE で実行する必要があります。<br/><br/>**security_level=4**:コンテンツの暗号化とデコードを、ハードウェアを基盤にした TEE で実行する必要があります。|
**セキュリティ レベル 2**:暗号化は TEE 内で実行します (ビデオ処理は実行されません)。解読されたバッファーはアプリケーション ドメインに返され、別のビデオ ハードウェアまたはソフトウェアによって処理されます。 ただし、レベル 2 では、暗号化に関する情報はやはり TEE 内でのみ処理されます。| **security_level=3**:キー マテリアルと暗号化の操作を、ハードウェアを基盤にした TEE で実行する必要があります。 |
| **セキュリティ レベル 3**:デバイス上に TEE はありません。 ホスト オペレーティング システム上の暗号化に関する情報と解読されたコンテンツを保護するため、適切な手段が実行される場合があります。 レベル 3 の実装は、ハードウェア暗号化エンジンを含む場合がありますが、セキュリティのためではなく、パフォーマンス向上のためだけです。 | **security_level=2**:ソフトウェア暗号化と難読化デコーダーが必須です。<br/><br/>**security_level=1**:ソフトウェアベースのホワイトボックス暗号化が必須です。|

### <a name="question"></a>質問

コンテンツのダウンロードに時間がかかるのはなぜですか。

### <a name="answer"></a>Answer

ダウンロードの速度を上げるには 2 つの方法があります。

1.  エンド ユーザーがコンテンツ ダウンロードの起点/ストリーミング エンドポイントではなく CDN にヒットする可能性が高くなるように、CDN を有効にします。 ユーザーがストリーミング エンドポイントにヒットした場合、各 HLS セグメントまたは DASH フラグメントは、動的にパッケージ化および暗号化されます。 この待機時間は各セグメント/フラグメントについてミリ秒の単位ですが、ビデオの長さが 1 時間にもなると、待機時間が蓄積して長いダウンロードの原因になる可能性があります。
2.  エンド ユーザーが、すべてのコンテンツではなく、ビデオ品質レイヤーとオーディオ トラックを選んでダウンロードできるようにします。 オフライン モードでは、すべての品質レイヤーをダウンロードしても意味がありません。 これを実現する方法は 2 つあります。
    1.  クライアントによる制御: ダウンロードするビデオ品質レイヤーとオーディオ トラックを、プレーヤー アプリが自動的に選ぶか、またはユーザーが選びます。
    2.  サービスによる制御: Azure Media Services の動的マニフェスト機能を使って (グローバル) フィルターを作成し、HLS 再生リストまたは DASH MPD を、単一のビデオ品質レイヤーと選ばれたオーディオ トラックに制限することができます。 その後エンド ユーザーに提示されるダウンロード URL には、このフィルターが含まれます。

## <a name="summary"></a>まとめ

この記事では、Android デバイスで Widevine によって保護された DASH コンテンツのオフライン モード再生を実装する方法について説明しました。  また、Widevine で保護されたコンテンツのオフライン ストリーミングに関連する一般的な質問にもお答えしました。
