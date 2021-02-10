---
title: Media Services v3 動的暗号化を使用してコンテンツを保護する
description: Azure Media Services での動的暗号化によるコンテンツ保護、ストリーミング プロトコル、および暗号化の種類について説明します。
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 08/31/2020
ms.author: inhenkel
ms.custom: seodec18, devx-track-csharp
ms.openlocfilehash: edea46e86475f1731ecfae465f969ca45d63f0a2
ms.sourcegitcommit: 100390fefd8f1c48173c51b71650c8ca1b26f711
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/27/2021
ms.locfileid: "98897630"
---
# <a name="protect-your-content-with-media-services-dynamic-encryption"></a>Media Services 動的暗号化を使用してコンテンツを保護する

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Azure Media Services を使用すると、メディアがコンピューターから離れてから、保存、処理、配信されるまでの全過程をセキュリティ保護できます。 Media Services では、Advanced Encryption Standard (AES-128) または主要な 3 つのデジタル著作権管理 (DRM) システム コンテンツを配信できます。 Media Services では、承認されたクライアントに AES キーと DRM (PlayReady、Widevine、FairPlay) ライセンスを配信するためのサービスも提供しています。 コンテンツが AES クリア キーで暗号化され、HTTPS で送信される場合、クライアントに到達するまで平文になりません。

[!INCLUDE [Widevine is not available in the GovCloud region.](./includes/widevine-not-available-govcloud.md)]

Media Services v3 では、コンテンツ キーがストリーミング ロケーターに関連付けられています ([この例](protect-with-aes128.md)を参照)。 Media Services キー配信サービスを使用している場合は、Azure Media Services でコンテンツ キーを生成できます。 独自のキー配信サービスを使用している場合、または 2 つのデータセンターに同じコンテンツ キーが必要な高可用性シナリオに対応する必要がある場合は、コンテンツ キーを自分で生成する必要があります。

プレーヤーがストリームを要求すると、Media Services は指定されたキーを使用して、AES クリア キーまたは DRM 暗号化によってコンテンツを動的に暗号化します。 ストリームの暗号化を解除するために、プレーヤーは Media Services のキー配信サービスまたは指定したキー配信サービスからキーを要求します。 ユーザーによるキーの取得が承認されているかどうかを判断するために、サービスはキーに指定されたコンテンツ キー ポリシーを評価します。

REST API、または Media Services クライアント ライブラリを使用して、ライセンスとキーの承認および認証ポリシーを構成できます。

次の図は、Media Services コンテンツ保護のワークフローを示しています。

![Media Services コンテンツ保護のワークフロー](./media/content-protection/content-protection.svg)
  
&#42; *動的暗号化は、AES-128 クリア キー、CBCS、CENC をサポートします。詳細については、[サポート マトリックス](#streaming-protocols-and-encryption-types)を参照してください。*

この記事では、Media Services でのコンテンツ保護の理解に役立つ概念と用語について説明します。

## <a name="main-components-of-a-content-protection-system"></a>コンテンツ保護システムの主要コンポーネント

コンテンツ保護システムを適切に完了するには、作業の範囲をよく理解する必要があります。 以下のセクションに、実装する必要のある 3 つの部分の概要を示します。

> [!NOTE]
> 次の部分に進む前に、以下のセクションの各部分に焦点を当てて、十分にテストしておくことを強くお勧めします。 コンテンツ保護システムをテストするには、そのセクションで指定されているツールを使用してください。

### <a name="media-services-code"></a>Media Services コード
  
[DRM サンプル](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithDRM/Program.cs)からは、.NET を使用してマルチ DRM システムと Media Services v3 を実装する方法がわかります。 また、Media Services ライセンス/キー配信サービスを使用する方法がわかります。
  
複数の暗号化の種類 (AES-128、PlayReady、Widevine、FairPlay) を使用して各アセットを暗号化することができます。 合理的な組み合わせについては、「[ストリーミング プロトコルと暗号化の種類](#streaming-protocols-and-encryption-types)」を参照してください。

この例では、次のことを行っています。

1. [コンテンツ キー ポリシー](content-key-policy-concept.md)を作成して構成します。

   コンテンツ キー (資産へのアクセスをセキュリティで保護する) をエンド クライアントに届ける方法を構成するには、コンテンツ キー ポリシーを作成します。  

   * ライセンス配信承認を定義します。 JSON Web トークン (JWT) の要求に基づいて承認チェックのロジックを指定します。
   * [PlayReady](playready-license-template-overview.md)、[Widevine](widevine-license-template-overview.md)、[FairPlay](fairplay-license-overview.md) のライセンスを構成します。 テンプレートを使用すると、各 DRM の権利とアクセス許可を構成できます。

     ```
     ContentKeyPolicyPlayReadyConfiguration playReadyConfig = ConfigurePlayReadyLicenseTemplate();
     ContentKeyPolicyWidevineConfiguration widevineConfig = ConfigureWidevineLicenseTempate();
     ContentKeyPolicyFairPlayConfiguration fairPlayConfig = ConfigureFairPlayPolicyOptions();
     ```

2. 暗号化された資産をストリーミングするように構成された[ストリーミング ロケーター](streaming-locators-concept.md)を作成します。
  
   ストリーミング ロケーターには[ストリーミング ポリシー](streaming-policy-concept.md)を関連付ける必要があります。 この例では、"Predefined_MultiDrmCencStreaming" ポリシーに `StreamingLocator.StreamingPolicyName` を設定します。

   PlayReady と Widevine の暗号化が適用され、キーは構成済みの DRM ライセンスに基づいて再生クライアントに配信されます。 また、CBCS (FairPlay) でもストリームを暗号化する場合は、"Predefined_MultiDrmStreaming" ポリシーを使用します。

   ストリーミング ロケーターは、定義したコンテンツ キー ポリシーにも関連付けられます。

3. テスト トークンを作成します。

   `GetTokenAsync` メソッドは、テスト トークンの作成方法を示しています。
4. ストリーミング URL を構築します。

   `GetDASHStreamingUrlAsync` メソッドは、ストリーミング URL を構築する方法を示しています。 この場合、URL は DASH コンテンツをストリーミングします。

### <a name="player-with-an-aes-or-drm-client"></a>AES または DRM クライアントを使用するプレーヤー。

プレーヤー SDK (ネイティブまたはブラウザー ベース) に基づくビデオ プレーヤー アプリは、次の要件を満たす必要があります。

* プレーヤー SDK は、必要な DRM クライアントをサポートしている。
* プレーヤー SDK は、必要なストリーミング プロトコル (スムーズ、DASH、HTTP ライブ ストリーミング (HLS)) をサポートしている。
* プレーヤー SDK は、ライセンス取得要求で JWT トークンを渡す処理に対応できる。

[Azure Media Player API](https://amp.azure.net/libs/amp/latest/docs/) を使ってプレーヤーを作成できます。 異なる DRM プラットフォームで使う DRM テクノロジを指定するには、[Azure Media Player の ProtectionInfo API](https://amp.azure.net/libs/amp/latest/docs/) を使います。

AES または CENC (Widevine および PlayReady) で暗号化されたコンテンツをテストするには、[Azure Media Player](https://aka.ms/azuremediaplayer) を使用できます。 必ず **[詳細オプション]** を選択し、暗号化オプションを確認します。

FairPlay で暗号化されたコンテンツをテストする場合は、[このテストプレイヤー](https://aka.ms/amtest)を使用してください。 プレーヤーは、Widevine、PlayReady、FairPlay DRM、および AES-128 クリア キー暗号化をサポートしています。

さまざまな DRM をテストするのに適したブラウザーを選択します。

* Widevine には、Chrome、Opera、または Firefox。
* PlayReady には、Microsoft Edge または Internet Explorer 11。
* FairPlay には、macOS 上の Safari。

### <a name="security-token-service"></a>セキュリティ トークン サービス

セキュリティ トークン サービス (STS) は、バックエンド リソースにアクセスするためのアクセス トークンとして JWT を発行します。 バックエンド リソースとして Azure Media Services ライセンス/キー配信サービスを使用できます。 STS では次のことを定義する必要があります。

* 発行者と対象ユーザー (または範囲)。
* 要求 (コンテンツ保護におけるビジネス要件によって変わります)。
* 署名の検証のための対称検証または非対称検証。
* キー ロールオーバーのサポート (必要な場合)。

[この STS ツール](https://openidconnectweb.azurewebsites.net/DRMTool/Jwt)を使用して STS をテストできます。 対称、非対称、およびキーのロールオーバーありの Azure Active Directory (Azure AD) の 3 種類の検証キーがすべてサポートされています。

## <a name="streaming-protocols-and-encryption-types"></a>ストリーミング プロトコルと暗号化の種類

Media Services を使用すると、AES クリアキーまたは DRM 暗号化 (PlayReady、Widevine、または FairPlay を使用) によって動的に暗号化されたコンテンツを配信できます。 現在、HLS、MPEG DASH、およびスムーズ ストリーミングの各形式を暗号化できます。 各プロトコルでは、次の暗号化方式がサポートされています。

### <a name="hls"></a>HLS

HLS プロトコルでは、次のコンテナー形式と暗号化スキームがサポートされます。

|コンテナー形式|暗号化スキーム|URL の例|
|---|---|---|
|All|AES|`https://amsv3account-usw22.streaming.media.azure.net/00000000-0000-0000-0000-000000000000/ignite.ism/manifest(format=m3u8-aapl,encryption=cbc)`|
|MPG2-TS |CBCS (FairPlay) |`https://amsv3account-usw22.streaming.media.azure.net/00000000-0000-0000-0000-000000000000/ignite.ism/manifest(format=m3u8-aapl,encryption=cbcs-aapl)`|
|CMAF (fmp4) |CBCS (FairPlay) |`https://amsv3account-usw22.streaming.media.azure.net/00000000-0000-0000-0000-000000000000/ignite.ism/manifest(format=m3u8-cmaf,encryption=cbcs-aapl)`|
|MPG2-TS |CENC (PlayReady) |`https://amsv3account-usw22.streaming.media.azure.net/00000000-0000-0000-0000-000000000000/ignite.ism/manifest(format=m3u8-aapl,encryption=cenc)`|
|CMAF (fmp4) |CENC (PlayReady) |`https://amsv3account-usw22.streaming.media.azure.net/00000000-0000-0000-0000-000000000000/ignite.ism/manifest(format=m3u8-cmaf,encryption=cenc)`|

HLS/CMAF + FairPlay (HEVC/H.265 を含む) は、次のデバイスでサポートされています。

* iOS 11 以降。
* iPhone 8 以降。
* macOS High Sierra (Intel 第 7 世代 CPU 搭載)。

### <a name="mpeg-dash"></a>MPEG-DASH

MPEG-DASH プロトコルでは、次のコンテナー形式と暗号化スキームがサポートされます。

|コンテナー形式|暗号化スキーム|URL の例
|---|---|---|
|All|AES|`https://amsv3account-usw22.streaming.media.azure.net/00000000-0000-0000-0000-000000000000/ignite.ism/manifest(format=mpd-time-csf,encryption=cbc)`|
|CSF (fmp4) |CENC (Widevine + PlayReady) |`https://amsv3account-usw22.streaming.media.azure.net/00000000-0000-0000-0000-000000000000/ignite.ism/manifest(format=mpd-time-csf,encryption=cenc)`|
|CMAF (fmp4)|CENC (Widevine + PlayReady)|`https://amsv3account-usw22.streaming.media.azure.net/00000000-0000-0000-0000-000000000000/ignite.ism/manifest(format=mpd-time-cmaf,encryption=cenc)`|

### <a name="smooth-streaming"></a>スムーズ ストリーミング

スムーズ ストリーミング プロトコルでは、次のコンテナー形式と暗号化スキームがサポートされます。

|Protocol|コンテナー形式|暗号化スキーム|
|---|---|---|
|fMP4|AES|`https://amsv3account-usw22.streaming.media.azure.net/00000000-0000-0000-0000-000000000000/ignite.ism/manifest(encryption=cbc)`|
|fMP4 | CENC (PlayReady) |`https://amsv3account-usw22.streaming.media.azure.net/00000000-0000-0000-0000-000000000000/ignite.ism/manifest(encryption=cenc)`|
|fMP4 | PIFF 1.1 (PlayReady) |`https://amsv3account-usw22.streaming.media.azure.net/00000000-0000-0000-0000-000000000000/ignite.ism/manifest(encryption=piff)`|

> [!NOTE]
> PIFF 1.1 のサポートは、初期の "Silverlight" バージョンの Common Encryption を実装したスマート TV (Samsung、LG) で下位互換性を保つためのソリューションとして提供されています。 PIFF 形式は、PIFF 1.1 バージョンの PlayReady 暗号化をサポートしている 2009 ～ 2015 年に出荷された従来の Samsung または LG 製スマート TV をサポートするために必要な場合のみ使用することをお勧めします。 

### <a name="browsers"></a>ブラウザー

一般的なブラウザーでは次の DRM クライアントがサポートされます。

|Browser|暗号化|
|---|---|
|Chrome|Widevine|
|Microsoft Edge、Internet Explorer 11|PlayReady|
|Firefox|Widevine|
|Opera|Widevine|
|Safari|FairPlay|

## <a name="controlling-content-access"></a>コンテンツ アクセスの制御

コンテンツ キー ポリシーを構成することで、コンテンツにアクセスできるユーザーを制御できます。 Media Services では、キーを要求するユーザーを承認する複数の方法がサポートされています。 キーをクライアント (プレーヤー) に配信するには、クライアントがポリシーの要件を満たしている必要があります。 コンテンツ キー ポリシーは、*オープン* または *トークン* の制限を持つことができます。

オープンの制限のコンテンツ キー ポリシーは、承認を必要としないすべてのユーザーにライセンスを発行するときに使用できます。 たとえば、収益が広告ベースで、サブスクリプションベースではない場合です。  

トークン制限コンテンツ キー ポリシーでは、コンテンツ キーは、ライセンスまたはキー要求で有効な JWT トークンまたは単純 Web トークン (SWT) を提示するクライアントにのみ送信されます。 このトークンは STS によって発行される必要があります。

Azure AD を STS として使用することも、[カスタム STS](#using-a-custom-sts) をデプロイすることもできます。 STS は、トークン、トークンの制限の構成で指定した、指定されたキーと問題要求で署名を作成するように構成する必要があります。 Media Services ライセンス/キー配信サービスは、次の両方の条件が存在する場合に、要求されたライセンスまたはキーをクライアントに返します。

* トークンが有効である。
* トークン内の要求が、ライセンスまたはキー用に構成されているものと一致する。

トークン制限ポリシーを構成する際には、プライマリ検証キー、発行者、および対象ユーザーの各パラメーターを指定する必要があります。 プライマリ検証キーには、トークンの署名に使用されたキーが含まれています。 発行者は、トークンを発行する STS です。 対象ユーザー (スコープとも呼ばれる) には、トークンの目的、またはトークンがアクセスを承認するリソースが記述されます。 Media Services ライセンス/キー配信サービスでは、トークン内のこれらの値がテンプレート内の値と一致することが検証されます。

### <a name="token-replay-prevention"></a>トークン再生の防止

*トークン再生の防止* 機能を使用すると、Media Services のユーザーは、同じトークンを使用してキーまたはライセンスを要求できる回数に制限を設定できます。 ユーザーは `urn:microsoft:azure:mediaservices:maxuses` の種類の要求をトークンに追加できます。この値は、トークンを使用してライセンスまたはキーを取得できる回数です。 キー配信に対して同じトークンを使用する後続のすべての要求では、"許可されていません" 応答が返されます。 [DRM サンプル](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithDRM/Program.cs#L601)で、要求を追加する方法を参照してください。
 
#### <a name="considerations"></a>考慮事項

* ユーザーは、トークンの生成を制御する必要があります。 要求は、トークン自体に配置する必要があります。
* この機能を使用すると、要求を受信してから有効期限切れまでの時間が 1 時間を超えているトークンを含む要求は、"許可されていません" 応答により拒否されます。
* トークンは、その署名によって一意に識別されます。 ペイロードに変更が加えられる (たとえば、有効期限や要求が更新される) と、トークンの署名が変更され、以前にキー配信が確認したことがない新しいトークンとしてカウントされます。
* ユーザーによって設定された `maxuses` 値をトークンが超えると、再生は失敗します。
* この機能は、既存の保護されているすべてのコンテンツに使用できます (発行されたトークンのみを変更する必要があります)。
* この機能は、JWT と SWT の両方で動作します。

## <a name="using-a-custom-sts"></a>カスタム STS の使用

ユーザーは、カスタム STS を使ってトークンを提供することがあります。 原因は次のとおりです。

* ユーザーが使用する ID プロバイダー (IDP) が STS をサポートしていない場合。 この場合は、カスタム STS が選択肢になります。
* ユーザーが、STS とユーザーのサブスクライバー請求システムの統合において、より柔軟性の高い、または厳密な制御を必要とする場合。

   たとえば、[OTT](https://en.wikipedia.org/wiki/Over-the-top_media_services) サービス運営会社は、プレミアム、ベーシック スポーツなど、複数のサブスクライバー パッケージを提供する場合があります。 運営会社は、特定のパッケージのコンテンツだけが利用可能になるように、トークンの要求をサブスクライバーのパッケージと一致させる必要があります。 この場合、カスタム STS は必要な柔軟性と制御を提供します。

* カスタム要求をトークンに含めて、異なる DRM ライセンス パラメーター (サブスクリプション ライセンスとレンタル ライセンス) を持つさまざまな ContentKeyPolicyOptions を選択するため。
* トークンがアクセスを許可するキーのコンテンツ キー ID を表す要求を含めるため。

カスタム STS を使うときは、2 つの変更を行う必要があります。

* 資産のライセンス配信サービスを構成するときに、Azure AD からの現在のキーではなく、カスタム STS での検証に使うセキュリティ キーを指定する必要があります
* JTW トークンが生成されるときに、現在の Azure AD での X 509 証明書の秘密キーではなく、セキュリティ キーを指定します。

セキュリティ キーには次の 2 種類があります。

* 対称キー:JWT の生成と検証に同じキーが使われます。
* 非対称キー:X509 証明書の公開/秘密キー ペアの秘密キーが JWT の暗号化/生成に使われ、公開キーがトークンの検証に使われます。

開発プラットフォームとして .NET Framework/C# を使う場合、非対称セキュリティ キーに使われる X509 証明書のキーの長さは 2048 ビット以上でなければなりません。 このキーの長さは、.NET Framework の System.IdentityModel.Tokens.X509AsymmetricSecurityKey クラスの要件です。 そうでない場合は、次の例外がスローされます。IDX10630:署名の "System.IdentityModel.Tokens.X509AsymmetricSecurityKey" は "2048" ビット以上でなければなりません。

## <a name="custom-key-and-license-acquisition-url"></a>カスタム キーとライセンス取得 URL

異なるライセンス/キー配信サービス (Media Services 以外) を指定する場合は、次のテンプレートを使用します。 テンプレート内には置換可能なフィールドが 2 つあり、資産ごとにストリーミング ポリシーを作成する代わりに多数の資産でストリーミング ポリシーを共有できます。 

* `EnvelopeEncryption.CustomKeyAcquisitionUrlTemplate`:エンドユーザー プレーヤーにキーを配信するカスタムサービスの URL のテンプレート。 キーの発行に Azure Media Services を使用しているときには必要ありません。 

   このテンプレートでは、置換できるトークンがサポートされ、実行時にサービスによって要求固有の値に更新されます。  現在サポートされているトークン値は次のとおりです。
   * `{AlternativeMediaId}`。これは、StreamingLocatorId.AlternativeMediaId の値に置き換えられます。
   * `{ContentKeyId}`。これは、要求されたキーの識別子の値に置き換えられます。
* `StreamingPolicyPlayReadyConfiguration.CustomLicenseAcquisitionUrlTemplate`:エンドユーザー プレーヤーにライセンスを配信するカスタム サービスの URL のテンプレート。 ライセンスの発行に Azure Media Services を使用しているときには必要ありません。

   このテンプレートでは、置換できるトークンがサポートされ、実行時にサービスによって要求固有の値に更新されます。 現在サポートされているトークン値は次のとおりです。  
   * `{AlternativeMediaId}`。これは、StreamingLocatorId.AlternativeMediaId の値に置き換えられます。
   * `{ContentKeyId}`。これは、要求されたキーの識別子の値に置き換えられます。 
* `StreamingPolicyWidevineConfiguration.CustomLicenseAcquisitionUrlTemplate`:前のテンプレートと同じですが、Widevine でのみ使用されます。 
* `StreamingPolicyFairPlayConfiguration.CustomLicenseAcquisitionUrlTemplate`:前のテンプレートと同じですが、FairPlay でのみ使用されます。  

次に例を示します。

```csharp
streamingPolicy.EnvelopEncryption.customKeyAcquisitionUrlTemplate = "https://mykeyserver.hostname.com/envelopekey/{AlternativeMediaId}/{ContentKeyId}";
```

`ContentKeyId` は、要求されたキーの値を持っています。 要求をユーザー側のエンティティにマップする場合は、`AlternativeMediaId` を使用できます。 たとえば、`AlternativeMediaId` を使用すると、アクセス許可を調べるときに役立ちます。

カスタムのライセンス/キーの取得 URL を使用する REST の例については、「[ストリーミング ポリシー - 作成](/rest/api/media/streamingpolicies/create)」をご覧ください。

> [!NOTE]
> Widevine は Google Inc. によって提供されるサービスであり、Google Inc. の利用規約とプライバシー ポリシーが適用されます。

## <a name="troubleshoot"></a>トラブルシューティング

`MPE_ENC_ENCRYPTION_NOT_SET_IN_DELIVERY_POLICY` エラーが発生する場合は、適切なストリーミング ポリシーを指定していることを確認してください。

`_NOT_SPECIFIED_IN_URL`で終わるエラーが発生した場合は、URL に暗号化形式を指定したことを確認してください。 たとえば `…/manifest(format=m3u8-cmaf,encryption=cbcs-aapl)` です。 「[ストリーミング プロトコルと暗号化の種類](#streaming-protocols-and-encryption-types)」を参照してください。

## <a name="ask-questions-give-feedback-get-updates"></a>質問、フィードバックの送信、最新情報の入手

「[Azure Media Services community (Azure Media Services コミュニティ)](media-services-community.md)」を参照して、さまざまな質問の方法、フィードバックする方法、Media Services に関する最新情報の入手方法を確認してください。

## <a name="next-steps"></a>次のステップ

* [AES 暗号化で保護する](protect-with-aes128.md)
* [DRM での保護](protect-with-drm.md)
* [アクセス制御を使用したマルチ DRM コンテンツ保護システムの設計](design-multi-drm-system-with-access-control.md)
* [ストレージ側の暗号化](storage-account-concept.md#storage-side-encryption)
* [よく寄せられる質問](frequently-asked-questions.md)
* [JSON Web トークン ハンドラー](/dotnet/framework/security/json-web-token-handler)
