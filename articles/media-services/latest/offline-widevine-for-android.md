---
title: Azure Media Services v3 を使用した Android 用の Widevine のオフライン ストリーミング
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
ms.topic: conceptual
ms.date: 08/31/2020
ms.author: willzhan
ms.custom: devx-track-csharp
ms.openlocfilehash: 8cfc426844180cabc6f42330135d8fc1ff8ebabd
ms.sourcegitcommit: 58d3b3314df4ba3cabd4d4a6016b22fa5264f05a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/02/2020
ms.locfileid: "89289394"
---
# <a name="offline-widevine-streaming-for-android-with-media-services-v3"></a>Media Services v3 を使用した Android 用のオフラインの Widevine ストリーミング

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

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

> [!NOTE]
> オフライン DRM は、コンテンツをダウンロードするときの 1 ライセンスのために、1 つの要求を発行することに対してのみ課金されます。 どのエラーにも課金は行われません。

## <a name="prerequisites"></a>前提条件 

Android デバイスに Widevine 用のオフライン DRM を実装する前に、次のことを行っておく必要があります。

- Widevine DRM を使ったオンライン コンテンツ保護に対して導入される概念をよく理解します。 これについては、次のドキュメント/サンプルで詳しく説明されています。
    - [アクセス制御を使用したマルチ DRM コンテンツ保護システムの設計](design-multi-drm-system-with-access-control.md)
    - [DRM 動的暗号化とライセンス配信サービスの使用](protect-with-drm.md)
- [https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials.git](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials.git ) を複製します。

    [.NET を使用した DRM の暗号化](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/tree/master/AMSV3Tutorials/EncryptWithDRM)に関する記事にあるコードを変更して Widevine の構成を追加する必要があります。  
- Widevine DRM のオフライン再生をサポートする機能を持つオープンソースのビデオ プレーヤー SDK である Google ExoPlayer SDK for Android についてよく理解します。 
    - [ExoPlayer SDK](https://github.com/google/ExoPlayer)
    - [ExoPlayer 開発者ガイド](https://google.github.io/ExoPlayer/guide.html)
    - [EoPlayer 開発者ブログ](https://medium.com/google-exoplayer)

## <a name="configure-content-protection-in-azure-media-services"></a>Azure Media Services でコンテンツ保護を構成する

[GetOrCreateContentKeyPolicyAsync](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithDRM/Program.cs#L189) メソッドには、次の必要なステップが存在します。

1. コンテンツキー配信がライセンス配信サービスで承認される方法を指定します。 

    ```csharp
    ContentKeyPolicySymmetricTokenKey primaryKey = new ContentKeyPolicySymmetricTokenKey(tokenSigningKey);
    List<ContentKeyPolicyTokenClaim> requiredClaims = new List<ContentKeyPolicyTokenClaim>()
    {
        ContentKeyPolicyTokenClaim.ContentKeyIdentifierClaim
    };
    List<ContentKeyPolicyRestrictionTokenKey> alternateKeys = null;
    ContentKeyPolicyTokenRestriction restriction 
        = new ContentKeyPolicyTokenRestriction(Issuer, Audience, primaryKey, ContentKeyPolicyRestrictionTokenType.Jwt, alternateKeys, requiredClaims);
    ```
2. Widevine ライセンス テンプレートを構成します。  

    ```csharp
    ContentKeyPolicyWidevineConfiguration widevineConfig = ConfigureWidevineLicenseTempate();
    ```

3. ContentKeyPolicyOptions を作成します。

    ```csharp
    options.Add(
        new ContentKeyPolicyOption()
        {
            Configuration = widevineConfig,
            Restriction = restriction
        });
    ```

## <a name="enable-offline-mode"></a>オフライン モードを有効にする

Widevine ライセンスの**オフライン** モードを有効にするには、[Widevine ライセンス テンプレート](widevine-license-template-overview.md)を構成する必要があります。 [ConfigureWidevineLicenseTempate](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithDRM/Program.cs#L563) に示すように、**policy_overrides** オブジェクトで **can_persist** プロパティを **true** に設定します (既定値は false)。 

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithDRM/Program.cs#ConfigureWidevineLicenseTempate)]

## <a name="configuring-the-android-player-for-offline-playback"></a>オフライン再生用の Android Player の構成

Android デバイス用のネイティブ プレーヤー アプリを開発する最も簡単な方法は、オープンソースのビデオ プレーヤー SDK である [Google ExoPlayer SDK](https://github.com/google/ExoPlayer) を使うことです。 ExoPlayer では、Android のネイティブ MediaPlayer API では現在サポートされていない、MPEG-DASH や Microsoft Smooth Streaming 配信プロトコルなどの機能がサポートされています。

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

一部の古い Android デバイスでは、**policy_overrides** のプロパティ **rental_duration_seconds**、**playback_duration_seconds**、**license_duration_seconds** の値を設定する必要があります ([Widevine ライセンス テンプレート](widevine-license-template-overview.md)で定義されています)。 または、これらを無期限/無制限の期間を意味する 0 に設定してもかまいません。  

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

1. CORS の問題:サンプル アプリ内のサンプル ビデオは、 https://storage.googleapis.com/biograf-video-files/videos/ でホストされます。 Google は、Google Cloud Storage バケットでホストされているすべてのテスト サンプル用に CORS を設定しています。 これらは CORS ヘッダーで提供され、CORS エントリ `https://biograf-155113.appspot.com` (Google がサンプルをホストしているドメイン) が明示的に指定されていて、他のサイトではアクセスできません。 試みると、次のような HTTP エラーが表示されます: `Failed to load https://storage.googleapis.com/biograf-video-files/videos/poly-sizzle-2015/mp4/dash.mpd: No 'Access-Control-Allow-Origin' header is present on the requested resource. Origin 'https:\//13.85.80.81:8080' is therefore not allowed access. If an opaque response serves your needs, set the request's mode to 'no-cors' to fetch the resource with CORS disabled.`
2. 証明書の問題:Chrome v 58 以降では、Widevine 用の EME には HTTPS が必要です。 したがって、X509 証明書を使って HTTPS 経由でサンプル アプリをホストする必要があります。 通常のテスト証明書は次の要件のため機能しません。次の最小要件を満たす証明書を取得する必要があります。
    - Chrome および Firefox では、SAN-Subject Alternative Name の設定が証明書に存在する必要があります
    - 証明書には信頼された CA が必要であり、開発用の自己署名証明書は機能しません
    - 証明書には、Web サーバーまたはゲートウェイの DNS 名と一致する CN が必要です。

## <a name="faqs"></a>FAQ

詳細については、[Widevine についてよく寄せられる質問](frequently-asked-questions.md#widevine-streaming-for-android)を参照してください。

## <a name="additional-notes"></a>その他のメモ

Widevine は Google Inc. によって提供されるサービスであり、Google Inc. の利用規約とプライバシー ポリシーが適用されます。

## <a name="summary"></a>まとめ

この記事では、Android デバイスで Widevine によって保護された DASH コンテンツのオフライン モード再生を実装する方法について説明しました。  また、Widevine で保護されたコンテンツのオフライン ストリーミングに関連する一般的な質問にもお答えしました。
