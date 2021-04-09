---
title: Azure Portal を使用したコンテンツ キー承認ポリシーの構成 | Microsoft Docs
description: この記事では、コンテンツ キー承認ポリシーを構成する方法について説明します。
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.assetid: ee82a3fa-c34b-48f2-a108-8ba321f1691e
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/10/2021
ms.author: inhenkel
ms.openlocfilehash: 68bd4bd2472e80a663294745368fb505767a230a
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "103008316"
---
# <a name="configure-a-content-key-authorization-policy"></a>コンテンツ キー承認ポリシーを構成する

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

[!INCLUDE [media-services-selector-content-key-auth-policy](../../../includes/media-services-selector-content-key-auth-policy.md)]

## <a name="overview"></a>概要
 Azure Media Services を使用すると、128 ビット暗号化キーまたは [PlayReady デジタル著作権管理 (DRM)](https://www.microsoft.com/playready/overview/) を使用して Advanced Encryption Standard (AES) で保護された MPEG-DASH、スムーズ ストリーミング、HTTP ライブ ストリーミング (HLS) ストリームを配信できます。 Media Services では、Widevine DRM で暗号化された DASH ストリームを配信することもできます。 PlayReady と Widevine は、いずれも Common Encryption (ISO/IEC 23001-7 CENC) 仕様に従って暗号化されます。

Media Services では、クライアントが暗号化されたコンテンツを再生するために AES キーまたは PlayReady/Widevine ライセンスを取得できるキー/ライセンス配信サービスも提供します。

この記事では、Azure Portal を使用して、コンテンツ キー承認ポリシーを構成する方法について説明します。 キーは、動的にコンテンツを暗号化するために、後で使用できます。 現在、HLS、MPEG-DASH、およびスムーズ ストリーミングの各形式を暗号化できます。 プログレッシブ ダウンロードを暗号化することはできません。

プレーヤーが動的に暗号化するように設定されたストリームを要求すると、Media Services は構成済みのキーを使用して、AES または DRM 暗号化によってコンテンツを動的に暗号化します。 ストリームの暗号化を解除するには、プレーヤーはキー配信サービスからキーを要求します。 ユーザーのキーの取得が承認されているかどうかを判断するために、サービスはキーに指定した承認ポリシーを評価します。

複数のコンテンツ キーを使用する場合や、Media Services キー配信サービス以外のキー/ライセンス配信サービスの URL を指定する場合は、Media Services .NET SDK または REST API を使用します。 詳細については、次を参照してください。

* [Media Services .NET SDK を使用したコンテンツ キー承認ポリシーの構成](media-services-dotnet-configure-content-key-auth-policy.md)
* [Media Services REST API を使用したコンテンツ キー承認ポリシーの構成](media-services-rest-configure-content-key-auth-policy.md)

### <a name="some-considerations-apply"></a>適用される考慮事項
* Media Services アカウントの作成時に、既定のストリーミング エンドポイントが "停止" 状態でアカウントに追加されます。 コンテンツのストリーミングを開始し、ダイナミック パッケージと動的暗号化を活用するには、ストリーミング エンドポイントが "実行中" 状態である必要があります。 
* 資産には、一連のアダプティブ ビットレート MP4 または アダプティブ ビットレート スムーズ ストリーミング ファイルが含まれている必要があります。 詳細については、「 [資産をエンコードする](media-services-encode-asset.md)」をご覧ください。
* キー配信サービスでは、ContentKeyAuthorizationPolicy とそれに関連するオブジェクト (ポリシーのオプションと制限) を 15 分間キャッシュします。 ContentKeyAuthorizationPolicy を作成し、トークン制限を使用するように指定してテストし、ポリシーをオープン制限に更新することができます。 このプロセスは、ポリシーがオープン バージョンに切り替わるまでに約 15 分かかります。
* Media Services ストリーミング エンドポイントは、プレフライト応答の CORS Access-Control-Allow-Origin ヘッダーをワイルドカード "\*" に設定します。 この値は、Azure Media Player、Roku、JWPlayer など、ほとんどのプレーヤーでうまく機能します。 ただし、dashjs を利用する一部のプレーヤーでは、資格情報モードを "include" に設定すると、dashjs 内の XMLHttpRequest が Access-Control-Allow-Originin の値としてワイルドカード "\*" を許可しないため、機能しません。 dashjs でのこの制限の回避策としては、単一のドメインからクライアントをホストしている場合は、Media Services はそのドメインをプレフライト応答ヘッダー内で指定できます。 サポートが必要な場合は、Azure Portal からサポート チケットを開きます。

## <a name="configure-the-key-authorization-policy"></a>キーの承認ポリシーを構成する
キーの承認ポリシーを構成するには、 **[コンテンツ保護]** ページを選択します。

Media Services では、キーを要求するユーザーを承認する複数の方法がサポートされています。 コンテンツ キー承認ポリシーには、オープン、トークン、または IP 承認制限を設定することができます (IP は REST または .NET SDK を使用して構成できます)。

### <a name="open-restriction"></a>オープン制限
オープン制限とは、キーを要求するすべてのユーザーに、システムがキーを提供することを意味します。 この制限は、テストに便利です。

![OpenPolicy][open_policy]

### <a name="token-restriction"></a>トークン制限
トークン制限ポリシーを選択するには、 **[トークン]** ボタンを選択します。

トークン制限ポリシーには、STS (セキュリティ トークン サービス) によって発行されたトークンを含める必要があります。 Media Services では、[単純 Web トークン](/previous-versions/azure/azure-services/gg185950(v=azure.100)#BKMK_2) (SWT) 形式と JSON Web トークン (JWT) 形式のトークンがサポートされます。 詳細については、「[JWT authentication](http://www.gtrifonov.com/2015/01/03/jwt-token-authentication-in-azure-media-services-and-dynamic-encryption/)」(JWT 認証) を参照してください。

Media Services は STS を提供しません。 トークンを発行するカスタム STS を作成できます。 STS は、トークン、トークンの制限の構成で指定した、指定されたキーと問題要求で署名を作成するように構成する必要があります。 Media Services のキー配信サービスは、トークンが有効で、トークン内の要求がコンテンツ キー向けに構成された要求と一致する場合、暗号化キーをクライアントに返します。

トークン制限ポリシーを構成する際には、プライマリ検証キー、発行者、および対象ユーザーの各パラメーターを指定する必要があります。 プライマリ検証キーには、トークンの署名に使用されたキーが含まれています。 発行者は、トークンを発行する STS です。 対象ユーザー (スコープとも呼ばれる) には、トークンの目的、またはトークンがアクセスを承認するリソースが記述されます。 Media Services キー配信サービスでは、トークン内のこれらの値がテンプレート内の値と一致することが検証されます。

### <a name="playready"></a>PlayReady
PlayReady を使用してコンテンツを保護する場合、承認ポリシーの指定の 1 つとして、PlayReady ライセンス テンプレートを定義する XML 文字列を指定する必要があります。 既定では、次のポリシーが設定されます。

```xml
<PlayReadyLicenseResponseTemplate xmlns:i="https://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/Azure/MediaServices/KeyDelivery/PlayReadyTemplate/v1">
  <LicenseTemplates>
    <PlayReadyLicenseTemplate><AllowTestDevices>true</AllowTestDevices>
      <ContentKey i:type="ContentEncryptionKeyFromHeader" />
      <LicenseType>Nonpersistent</LicenseType>
      <PlayRight>
        <AllowPassingVideoContentToUnknownOutput>Allowed</AllowPassingVideoContentToUnknownOutput>
      </PlayRight>
    </PlayReadyLicenseTemplate>
  </LicenseTemplates>
</PlayReadyLicenseResponseTemplate>
```

**[ポリシー xml のインポート]** ボタンを選択し、「[Media Services PlayReady ライセンス テンプレートの概要](media-services-playready-license-template-overview.md)」で定義されている XML スキーマに準拠した別の XML を指定できます。

## <a name="additional-notes"></a>その他のメモ

* Widevine は Google Inc. によって提供されるサービスであり、Google Inc. の利用規約とプライバシー ポリシーが適用されます。

## <a name="next-steps"></a>次のステップ
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>フィードバックの提供
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

[open_policy]: ./media/media-services-portal-configure-content-key-auth-policy/media-services-protect-content-with-open-restriction.png
[token_policy]: ./media/media-services-key-authorization-policy/media-services-protect-content-with-token-restriction.png
