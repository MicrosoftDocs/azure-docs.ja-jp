---
title: Media Services 動的暗号化を使用してコンテンツを保護する - Azure | Microsoft Docs
description: この記事では、動的暗号化を使用したコンテンツ保護の概要について説明します。 ストリーミング プロトコルと暗号化の種類についても説明します。
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/28/2019
ms.author: juliako
ms.custom: seodec18
ms.openlocfilehash: 0172879ff2a2c351c4ca721a449e2e2839934baa
ms.sourcegitcommit: 470041c681719df2d4ee9b81c9be6104befffcea
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/12/2019
ms.locfileid: "67854069"
---
# <a name="content-protection-with-dynamic-encryption"></a>動的暗号化によるコンテンツ保護

Azure Media Services を使用すると、メディアがコンピューターから離れてから、保存、処理、配信されるまでのセキュリティ保護が可能になります。 Media Services では、Advanced Encryption Standard (AES-128) または主要な 3 つのデジタル著作権管理 (DRM) システム (Microsoft PlayReady、Google Widevine、および Apple FairPlay) によって動的に暗号化されたライブまたはオンデマンドのコンテンツを配信できます。 Media Services では、承認されたクライアントに AES キーと DRM (PlayReady、Widevine、FairPlay) ライセンスを配信するためのサービスも提供しています。 

次の図は、Media Services でのコンテンツ保護のワークフローを示しています。 

![コンテンツの保護](./media/content-protection/content-protection.svg)

&#42; *動的暗号化は、AES-128 "クリア キー"、CBCS、および CENC をサポートします。詳細については、[こちら](#streaming-protocols-and-encryption-types)のサポート マトリックスを参照してください。*

この記事では、Media Services でのコンテンツ保護の理解に関連する概念と用語について説明します。

## <a name="main-components-of-a-content-protection-system"></a>コンテンツ保護システムの主要コンポーネント

"コンテンツ保護" システム/アプリケーションの設計を適切に完了するには、作業の範囲をよく理解する必要があります。 以下の一覧に、実装する必要のある 3 つの部分の概要を示します。 

1. Azure Media Services コード
  
   [DRM](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithDRM/Program.cs) サンプルからは、.NET を使用し、マルチ DRM システムと Media Services v3 を実装する方法がわかります。 また、Media Services ライセンス/キー配信サービスを使用する方法がわかります。 複数の暗号化の種類 (AES-128、PlayReady、Widevine、FairPlay) を使用して各アセットを暗号化することができます。 合理的な組み合わせについては、「[ストリーミング プロトコルと暗号化の種類](#streaming-protocols-and-encryption-types)」を参照してください。
  
   この例では、次のことを行っています。

   1. [コンテンツ キー ポリシー](content-key-policy-concept.md)を作成して構成します。 コンテンツ キー (資産へのアクセスをセキュリティで保護する) をエンド クライアントに届ける方法を構成するには、**コンテンツ キー ポリシー**を作成します。    

      * JWT の要求に基づいて承認チェックのロジックを指定する、ライセンス配信の承認を定義します。
      * [PlayReady](playready-license-template-overview.md)、[Widevine](widevine-license-template-overview.md)、[FairPlay](fairplay-license-overview.md) のライセンスを構成します。 テンプレートを使用すると、使用する各 DRM の権利とアクセス許可を構成できます。

        ```
        ContentKeyPolicyPlayReadyConfiguration playReadyConfig = ConfigurePlayReadyLicenseTemplate();
        ContentKeyPolicyWidevineConfiguration widevineConfig = ConfigureWidevineLicenseTempate();
        ContentKeyPolicyFairPlayConfiguration fairPlayConfig = ConfigureFairPlayPolicyOptions();
        ```
   2. 暗号化された資産をストリーミングするように構成された[ストリーミング ロケーター](streaming-locators-concept.md)を作成します。 
  
      **ストリーミング ロケーター**には[ストリーミング ポリシー](streaming-policy-concept.md)を関連付ける必要があります。 この例では、"Predefined_MultiDrmCencStreaming" ポリシーに StreamingLocator.StreamingPolicyName を設定します。 PlayReady と Widevine の暗号化が適用されます (キーは構成済みの DRM ライセンスに基づいて再生クライアントに配信されます)。 また、CBCS (FairPlay) でもストリームを暗号化する場合は、"Predefined_MultiDrmStreaming" を使用します。
      
      ストリーミング ロケーターは、定義された**コンテンツ キー ポリシー**にも関連付けられます。
    
   3. テスト トークンを作成します。

      **GetTokenAsync** メソッドは、テスト トークンの作成方法を示しています。
   4. ストリーミング URL を構築します。

      **GetDASHStreamingUrlAsync** メソッドは、ストリーミング URL を構築する方法を示しています。 この場合、URL は **DASH** コンテンツをストリームします。

2. AES または DRM クライアントを使用するプレーヤー。 プレーヤー SDK (ネイティブまたはブラウザー ベース) に基づくビデオ プレーヤー アプリは、次の要件を満たす必要があります。
   * プレーヤー SDK は必要な DRM クライアントをサポートしている
   * プレーヤー SDK は、必要なストリーミング プロトコル (Smooth、DASH、または HLS ) をサポートしている
   * プレーヤー SDK は、ライセンス取得要求で JWT トークンを渡す処理に対応できる必要がある
  
     [Azure Media Player API](https://amp.azure.net/libs/amp/latest/docs/) を使ってプレーヤーを作成できます。 異なる DRM プラットフォームで使う DRM テクノロジを指定するには、[Azure Media Player の ProtectionInfo API](https://amp.azure.net/libs/amp/latest/docs/) を使います。

     AES または CENC (Widevine および PlayReady) で暗号化されたコンテンツをテストするには、[Azure Media Player](https://aka.ms/azuremediaplayer) を使用できます。 必ず [詳細オプション] をクリックし、暗号化オプションを確認します。

     FairPlay で暗号化されたコンテンツをテストする場合は、[このテストプレイヤー](https://aka.ms/amtest)を使用してください。 プレーヤーは、Widevine、PlayReady、FairPlay DRM、および AES-128 クリア キー暗号化をサポートしています。 
    
     さまざまな DRM をテストするには、適切なブラウザーを選択する必要があります (Widevine の場合は Chrome/Opera/Firefox、PlayReady の場合は Microsoft Edge/IE11、FairPlay の場合は Safari for macOS)。

3. バックエンド リソース アクセス用のアクセス トークンとして JSON Web トークン (JWT) を発行するセキュア トークン サービス (STS)。 バックエンド リソースとして AMS ライセンス配信サービスを使用することができます。 STS は以下を定義する必要があります。

   * 発行者と対象ユーザー (または範囲)
   * 要求 (コンテンツ保護におけるビジネス要件によって変わります)
   * 署名の検証のための対称または非対称検証
   * キー ロールオーバーのサポート (必要な場合)

     [この STS ツール](https://openidconnectweb.azurewebsites.net/DRMTool/Jwt) を使用して、3 種類の検証キー (対称、非対称、またはキー ロールオーバーありの Azure AD) をすべてサポートする STS をテストできます。 

> [!NOTE]
> 次のパートに移動する前に、各パート (前述) に集中して完全にテストすることを強くお勧めします。 "コンテンツ保護" システムをテストするには、前述の一覧で指定されているツールを使用してください。  

## <a name="streaming-protocols-and-encryption-types"></a>ストリーミング プロトコルと暗号化の種類

Media Services を使用すると、AES クリアキーまたは DRM 暗号化 (PlayReady、Widevine、または FairPlay を使用) によって動的に暗号化されたコンテンツを配信できます。 現時点では、暗号化できる形式は、HTTP ライブ ストリーミング (HLS)、MPEG DASH、および Smooth Streaming です。 各プロトコルは、次の暗号化方式をサポートしています。

### <a name="hls"></a>HLS

HLS プロトコルでは、次のコンテナー形式と暗号化スキームがサポートされます。

|コンテナー形式|暗号化スキーム|URL の例|
|---|---|---|
|All|AES|`https://amsv3account-usw22.streaming.media.azure.net/00000000-0000-0000-0000-000000000000/ignite.ism/manifest(format=m3u8-aapl,encryption=cbc)`|
|MPG2-TS |CBCS (FairPlay) |`https://amsv3account-usw22.streaming.media.azure.net/00000000-0000-0000-0000-000000000000/ignite.ism/manifest(format=m3u8-aapl,encryption=cbcs-aapl)`|
|CMAF (fmp4) |CBCS (FairPlay) |`https://amsv3account-usw22.streaming.media.azure.net/00000000-0000-0000-0000-000000000000/ignite.ism/manifest(format=m3u8-cmaf,encryption=cbcs-aapl)`|
|MPG2-TS |CENC (PlayReady) |`https://amsv3account-usw22.streaming.media.azure.net/00000000-0000-0000-0000-000000000000/ignite.ism/manifest(format=m3u8-aapl,encryption=cenc)`|
|CMAF (fmp4) |CENC (PlayReady) |`https://amsv3account-usw22.streaming.media.azure.net/00000000-0000-0000-0000-000000000000/ignite.ism/manifest(format=m3u8-cmaf,encryption=cenc)`|

HLS/CMAF + FairPlay (HEVC / H.265 を含む) は、次のデバイスでサポートされています。

* iOS v11 以上 
* iPhone 8 以上
* MacOS high Sierra (Intel 7th Gen CPU 搭載)

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

### <a name="browsers"></a>ブラウザー

一般的なブラウザーでは次の DRM クライアントがサポートされます。

|Browser|暗号化|
|---|---|
|Chrome|Widevine|
|Edge、IE 11|PlayReady|
|Firefox|Widevine|
|Opera|Widevine|
|Safari|FairPlay|

## <a name="aes-128-clear-key-vs-drm"></a>AES-128 クリア キーと DRM

AES 暗号化と DRM システムのどちらを使えばよいか迷うことがあります。 2 つのシステムの主な違いは、AES 暗号化ではコンテンツ キーが TLS でクライアントに送信されるため、キーが送信中に暗号化されますが、それ以外の暗号化は行われないことです (クリア)。 その結果、コンテンツの復号化に使用されるキーは、クライアント プレーヤーがアクセスでき、クライアントのネットワーク トレースでプレーンテキストとして表示できます。 AES-128 クリア キー暗号化は、閲覧者が信頼できる相手である場合 (従業員が見るために社内で配信される企業ビデオの暗号化など) に適しています。

PlayReady、Widevine、FairPlay など DRM システムではすべて、コンテンツの復号化に使用されるキーの暗号化のレベルが、AES-128 クリア キーと比較して高くなります。 TLS によって提供されるトランスポート レベルの暗号化に加え、コンテンツ キーが暗号化され、DRM ランタイムによって保護されるキーになります。 さらに、解読はオペレーティング システム レベルのセキュリティで保護された環境で行われるため、悪意のあるユーザーによる攻撃はより難しくなります。 閲覧者が信頼できない可能性があり、最高レベルのセキュリティを必要とするユース ケースでは、DRM をお勧めします。

## <a name="dynamic-encryption-and-key-delivery-service"></a>動的暗号化とキー配信サービス

Media Services v3 では、コンテンツ キーがストリーミング ロケーターに関連付けられています ([この例](protect-with-aes128.md)を参照)。 Media Services キー配信サービスを使用している場合は、Azure Media Services でコンテンツ キーを生成できます。 独自のキー配信サービスを使用している場合、または 2 つのデータセンターに同じコンテンツ キーが必要な高可用性シナリオに対応する必要がある場合は、コンテンツ キーを手動で生成する必要があります。

プレーヤーがストリームを要求すると、Media Services は指定されたキーを使用して、AES クリア キーまたは DRM 暗号化によってコンテンツを動的に暗号化します。 ストリームの暗号化を解除するために、プレーヤーは Media Services のキー配信サービスまたは指定したキー配信サービスからキーを要求します。 ユーザーのキーの取得が承認されているかどうかを判断するために、サービスはキーに指定したコンテンツ キー ポリシーを評価します。

Media Services は、承認されたクライアントに DRM (PlayReady、Widevine、FairPlay) ライセンスと AES キーを配信するためのキー配信サービスを提供しています。 REST API、または Media Services クライアント ライブラリを使用して、ライセンスとキーの承認および認証ポリシーを構成できます。

### <a name="custom-key-and-license-acquisition-url"></a>カスタム キーとライセンス取得 URL

異なるキーとライセンス配信サービス (Media Services 以外) を指定する場合は、次のテンプレートを使用します。 テンプレート内には置換するフィールドが 2 つあり、資産ごとにストリーミング ポリシーを作成する代わりに多数の資産でストリーミング ポリシーを共有できます。 

* EnvelopeEncryption.CustomKeyAcquisitionUrlTemplate - エンドユーザー プレーヤー向けのカスタム サービス配信キーの URL 用のテンプレート。 キーの発行に Azure Media Services を使用するときは必要ありません。 このテンプレートでは、置換できるトークンがサポートされ、実行時にサービスによって要求固有の値に更新されます。  現在サポートされているトークンの値は、StreamingLocatorId.AlternativeMediaId の値で置換される {AlternativeMediaId} と、要求対象のキーの ID 値で置換される {ContentKeyId} です。
* StreamingPolicyPlayReadyConfiguration.CustomLicenseAcquisitionUrlTemplate - エンドユーザー プレーヤー向けのカスタム サービス配信ライセンスの URL 用のテンプレート。 ライセンスの発行に Azure Media Services を使用するときは必要ありません。 このテンプレートでは、置換できるトークンがサポートされ、実行時にサービスによって要求固有の値に更新されます。 現在サポートされているトークンの値は、StreamingLocatorId.AlternativeMediaId の値で置換される {AlternativeMediaId} と、要求対象のキーの ID 値で置換される {ContentKeyId} です。 
* StreamingPolicyWidevineConfiguration.CustomLicenseAcquisitionUrlTemplate - 上記と同じですが、Widevine 専用です。 
* StreamingPolicyFairPlayConfiguration.CustomLicenseAcquisitionUrlTemplate - 上記と同じですが、FairPlay 専用です。  

例:

```csharp
streamingPolicy.EnvelopEncryption.customKeyAcquisitionUrlTemplate = "https://mykeyserver.hostname.com/envelopekey/{AlternativeMediaId}/{ContentKeyId}";
```

`ContentKeyId` は要求対象のキーの値を保持します。要求をユーザー側のエンティティにマップする場合は、`AlternativeMediaId` を使用できます。 たとえば、`AlternativeMediaId` を使用すると、アクセス許可を調べるときに役立ちます。

カスタムのキーとライセンスの取得 URL を使用する REST の例については、「[Streaming Policies - Create (ストリーミング ポリシー - 作成)](https://docs.microsoft.com/rest/api/media/streamingpolicies/create)」をご覧ください。

## <a name="control-content-access"></a>コンテンツ アクセスの制御

コンテンツ キー ポリシーを構成することで、コンテンツにアクセスできるユーザーを制御できます。 Media Services では、キーを要求するユーザーを承認する複数の方法がサポートされています。 コンテンツ キー ポリシーを構成する必要があります。 キーをクライアント (プレーヤー) に配信するには、クライアントがポリシーの要件を満たしている必要があります。 コンテンツ キー ポリシーは、**オープン**または**トークン**の制限を持つことができます。 

トークン制限コンテンツ キー ポリシーでは、コンテンツ キーは、キー/ライセンス要求で有効な JSON Web トークン (JWT) または単純 Web トークン (SWT) を提示したクライアントにのみ送信されます。 このトークンは、セキュリティ トークン サービス (STS) によって発行されたものでなければなりません。 Azure Active Directory を STS として使用することも、カスタム STS をデプロイすることもできます。 STS は、トークン、トークンの制限の構成で指定した、指定されたキーと問題要求で署名を作成するように構成する必要があります。 トークンが有効であり、なおかつトークンに含まれるクレームとキー/ライセンスに対して構成されている要求とが一致した場合、Media Services キー配信サービスは要求されたキー/ライセンスをクライアントに返します。

トークン制限ポリシーを構成する際には、プライマリ検証キー、発行者、および対象ユーザーの各パラメーターを指定する必要があります。 プライマリ検証キーには、トークンの署名に使用されたキーが含まれています。 発行者は、トークンを発行するセキュリティ トークン サービスです。 対象ユーザー (スコープとも呼ばれる) には、トークンの目的、またはトークンがアクセスを承認するリソースが記述されます。 Media Services キー配信サービスでは、トークン内のこれらの値がテンプレート内の値と一致することが検証されます。

多くの場合、顧客は、カスタム STS を使用して、トークンにカスタムの要求を組み込んで、異なる DRM ライセンス パラメーター (サブスクリプション ライセンスまたはレンタル ライセンス) のさまざまな ContentKeyPolicyOptions から選択します。または、トークンによってアクセス権を付与するキーのコンテンツ キー ID を表す要求を組み込みます。
 
## <a name="storage-side-encryption"></a>ストレージ側の暗号化

保存時の資産を保護するには、ストレージ側の暗号化で資産を暗号化する必要があります。 次の表では、Media Services v3 でのストレージ側暗号化のしくみを示します。

|暗号化オプション|説明|Media Services v3|
|---|---|---|
|Media Services のストレージの暗号化| AES-256 暗号化、Media Services によって管理されるキー|サポートされていません<sup>(1)</sup>|
|[Storage Service Encryption for Data at Rest](https://docs.microsoft.com/azure/storage/common/storage-service-encryption)|Azure Storage によって提供されるサーバー側暗号化、Azure またはお客様が管理するキー|サポートされています|
|[ストレージ クライアント側暗号化](https://docs.microsoft.com/azure/storage/common/storage-client-side-encryption)|Azure Storage によって提供されるクライアント側暗号化、お客様が Key Vault で管理するキー|サポートされていません|

<sup>1</sup> Media Services v3 では、ストレージの暗号化 (AES-256 暗号化) は、Media Services v2 で資産を作成した場合の下位互換性のためにのみサポートされています。 つまり、v3 は、既存のストレージの暗号化済み資産では動作しますが、そのような資産を新規作成することはできません。

## <a name="troubleshoot"></a>トラブルシューティング

`MPE_ENC_ENCRYPTION_NOT_SET_IN_DELIVERY_POLICY` エラーを取得した場合は、適切なストリーミング ポリシーを指定してください。

`_NOT_SPECIFIED_IN_URL`で終わるエラーが発生した場合は、URL に暗号化形式を指定したことを確認してください。 たとえば、「 `…/manifest(format=m3u8-cmaf,encryption=cbcs-aapl)` 」のように入力します。 「[ストリーミング プロトコルと暗号化の種類](#streaming-protocols-and-encryption-types)」を参照してください。

## <a name="ask-questions-give-feedback-get-updates"></a>質問、フィードバックの提供、最新情報の入手

「[Azure Media Services community (Azure Media Services コミュニティ)](media-services-community.md)」を参照して、さまざまな質問の方法、フィードバックする方法、Media Services に関する最新情報の入手方法を確認してください。

## <a name="next-steps"></a>次の手順

* [AES 暗号化で保護する](protect-with-aes128.md)
* [DRM での保護](protect-with-drm.md)
* [アクセス制御を使用したマルチ DRM コンテンツ保護システムの設計](design-multi-drm-system-with-access-control.md)
* [よく寄せられる質問](frequently-asked-questions.md)

