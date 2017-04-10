---
title: "Azure Media Services を使用してコンテンツを保護する | Microsoft Docs"
description: "この記事では、Media Services でのコンテンツ保護の概要について説明します。"
services: media-services
documentationcenter: 
author: Juliako
manager: erikre
editor: 
ms.assetid: 81bc00e1-dcda-4d69-b9ab-8768b793422b
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: juliako
translationtype: Human Translation
ms.sourcegitcommit: 555e0b6340d09517bfd87efe209f0304f3266788
ms.openlocfilehash: bf2bd9bca8817f64790ac62d2981a51aa36566a3
ms.lasthandoff: 01/27/2017


---
# <a name="protecting-content-overview"></a>コンテンツ保護の概要
Microsoft Azure Media Services を使用すると、メディアがコンピューターから離れてから、保存、処理、配信されるまでのセキュリティ保護が可能になります。 Media Services では、Advanced Encryption Standard (AES) (128 ビット暗号化キーを使用)、または任意の主要な DRM (Microsoft PlayReady、Google Widevine、および Apple FairPlay) 使用して、動的に暗号化したコンテンツを配信できます。 Media Services では、承認されたクライアントに AES キーと DRM (PlayReady、Widevine、FairPlay) ライセンスを配信するためのサービスも提供しています。 

次の図に、AMS でサポートされたコンテンツ保護ワークフローを示します。 

![PlayReady による保護](./media/media-services-content-protection-overview/media-services-content-protection-with-multi-drm.png)

>[!NOTE]
>AMS アカウントの作成時に、**既定**のストリーミング エンドポイントが自分のアカウントに追加され、**停止**状態になっています。 コンテンツのストリーミングを開始し、ダイナミック パッケージと動的暗号化を活用するには、コンテンツのストリーミング元のストリーミング エンドポイントが**実行中**状態である必要があります。 

このトピックでは、AMS でのコンテンツ保護の理解に関する [概念と用語](media-services-content-protection-overview.md) を説明します。 また、ここに記載されたトピックへの [リンク](media-services-content-protection-overview.md#common-scenarios) から、コンテンツ保護タスクを実行する方法を確認することもできます。 

## <a name="dynamic-encryption"></a>動的な暗号化
Microsoft Azure Media Services では、AES クリア キーまたは DRM 暗号化 (Microsoft PlayReady、Google Widevine、および Apple FairPlay) を使用して動的に暗号化されたコンテンツを配信できます。

現時点では、以下のストリーミング形式を暗号化できます。HLS、MPEG DASH、およびスムーズ ストリーミング。 プログレッシブ ダウンロードを暗号化することはできません。

Media Services で資産を暗号化する場合は、暗号化キー (CommonEncryption か EnvelopeEncryption) を資産に関連付ける必要があります。また、キーの承認ポリシーを構成する必要があります。

資産の配信ポリシーを構成する必要もあります。 ストレージで暗号化された資産をストリーミングする場合は、資産の配信ポリシーを構成して、資産の配信方法を指定する必要があります。

プレーヤーがストリームを要求すると、Media Services は指定されたキーを使用して、AES クリア キーまたは DRM 暗号化によってコンテンツを動的に暗号化します。 ストリームの暗号化を解除するには、プレーヤーはキー配信サービスからキーを要求します。 ユーザーのキーの取得が承認されているかどうかを判断するために、サービスはキーに指定した承認ポリシーを評価します。


## <a name="storage-encryption"></a>ストレージ暗号化
ストレージ暗号化で AES 256 ビット暗号化を使用してクリア コンテンツをローカルに暗号化し、それを Azure Storage にアップロードすると、コンテンツが保存時に暗号化された状態で格納されます。 ストレージの暗号化で保護された資産は、エンコーディングの前に自動的に暗号化が解除され、暗号化されたファイル システムに置かれます。その後、新しい出力資産として再度アップロードする前に必要に応じて再度暗号化されます。 ストレージの暗号化の主な使用事例としては、高品質の入力メディア ファイルをディスクに保存するときに強力な暗号化を使用してセキュリティを保護する場合が挙げられます。

ストレージで暗号化された資産を配信するためには、資産の配信ポリシーを構成して、コンテンツの配信方法を Media Services に指示する必要があります。 資産をストリーミングするには、ストリーミング サーバーでストレージ暗号化を解除し、指定された配信ポリシー (AES、共通暗号化、暗号化なしなど) を使用してコンテンツをストリーミングする必要があります。

## <a name="common-encryption-cenc"></a>共通暗号化 (CENC)
PlayReady または Widewine を使用してコンテンツを暗号化する場合は、共通暗号化を使用します。

## <a name="using-cbcs-aapl-encryption"></a>cbcs-aapl 暗号化の使用
FairPlay によるコンテンツの暗号化時には cbcs-aapl を使用します。

## <a name="envelope-encryption"></a>エンベロープ暗号化
このオプションは、AES-128 クリア キーを使用してコンテンツを保護する場合に使用します。 より安全なオプションが必要な場合は、このトピックに示されているいずれかの DRM を選択してください。 

## <a name="licenses-and-keys-delivery-service"></a>ライセンスとキーの配信サービス
Media Services は、承認されたクライアントに DRM (PlayReady、Widevine、FairPlay) ライセンスと AES クリア キーを配信するためのサービスを提供しています。 [Azure ポータル](media-services-portal-protect-content.md)、REST API、または Media Services SDK for .NET を使用して、ライセンスとキーの承認および認証ポリシーを構成できます。

## <a name="token-restriction"></a>トークン制限
コンテンツ キー承認ポリシーには、1 つまたは複数の承認制限 (オープンまたはトークン制限) を指定できます。 トークン制限ポリシーには、STS (セキュリティ トークン サービス) によって発行されたトークンを含める必要があります。 Media Services では、Simple Web Tokens (SWT) 形式と JSON Web Token (JWT) 形式のトークンがサポートされます。 Media Services では、Secure Token Services は提供されません。 トークンを発行するには、カスタム STS を作成するか、Microsoft Azure ACS を活用できます。 STS は、トークン、トークンの制限の構成で指定した、指定されたキーと問題要求で署名を作成するように構成する必要があります。 トークンが有効であり、なおかつトークンに含まれる要求とキー (またはライセンス) に対して構成されている要求とが一致した場合、Media Services キー配信サービスは、キー (またはライセンス) をクライアントに返します。

トークン制限ポリシーを構成する際は、プライマリ検証キー、発行者、対象ユーザーの各パラメーターを指定する必要があります。 プライマリ検証キーには、トークンの署名に使用されたキーが含まれ、発行者は、トークンを発行するセキュリティ トークン サービスです。 対象ユーザー (スコープとも呼ばれる) には、トークンの目的、またはトークンがアクセスを承認するリソースが記述されます。 Media Services キー配信サービスでは、トークン内のこれらの値がテンプレート内の値と一致することが検証されます。

## <a name="streaming-urls"></a>ストリーミング URL
資産を複数の DRM を使用して暗号化した場合、ストリーミング URL で暗号化タグを使用する必要があります: (format='m3u8-aapl', encryption='xxx')。

次の考慮事項が適用されます。

* 指定できるのは、ゼロまたは&1; つの暗号化タイプのみです。
* 1 つの暗号化のみが資産に適用された場合は、暗号化タイプを URL で指定する必要はありません。
* 暗号化タイプでは大文字と小文字が区別されます。
* 指定できる暗号化タイプは次のとおりです。  
  * **cenc**: 共通暗号化 (PlayReady または Widevine)
  * **cbcs-aapl**: Fairplay
  * **cbc**: AES エンベロープ暗号化

## <a name="common-scenarios"></a>一般的なシナリオ
次のトピックでは、ストレージ内のコンテンツを保護する方法、動的に暗号化されたストリーミング メディアを配信する方法、および AMS キー/ライセンス配信サービスを使用する方法を説明します。

* [AES による保護](media-services-protect-with-aes128.md) 
* [PlayReady や Widevine による保護 ](media-services-protect-with-drm.md)
* [Apple FairPlay または PlayReady で保護された HLS コンテンツをストリーミングする](media-services-protect-hls-with-fairplay.md)

### <a name="additional-scenarios"></a>その他のシナリオ
* [Azure PlayReady ライセンス サービスと個人の暗号化/ストリーミング サーバーを統合する方法](http://mingfeiy.com/integrate-azure-playready-license-service-encryptorstreaming-server)
* [castLabs を使用して Azure Media Services に DRM ライセンスを配信する](media-services-castlabs-integration.md)

>[!NOTE]
>外部の DRM サーバー (テクノロジー) を使用し、AMS からストリーミングするシナリオは、現在サポートされていません。


## <a name="media-services-learning-paths"></a>Media Services のラーニング パス
[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>フィードバックの提供
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

## <a name="related-links"></a>関連リンク
[Announcing PlayReady as a service and AES dynamic encryption with Azure Media Services (Azure Media Services でのサービスと AES 動的暗号化としての PlayReady の発表)](http://mingfeiy.com/playready)

[Azure Media Services PlayReady license delivery pricing explained (Azure Media Services PlayReady のライセンス配信料金の説明)](http://mingfeiy.com/playready-pricing-explained-in-azure-media-services)

[How to debug for AES encrypted stream in Azure Media Services (Azure Media Services で AES 暗号化ストリームをデバッグする方法)](http://mingfeiy.com/debug-aes-encrypted-stream-azure-media-services)

[JWT トークンの承認](http://www.gtrifonov.com/2015/01/03/jwt-token-authentication-in-azure-media-services-and-dynamic-encryption/)

[Azure Media Services OWIN MVC ベースのアプリを Azure Active Directory と統合し、JWT 要求に基づいてコンテンツ キーの配信を制限する](http://www.gtrifonov.com/2015/01/24/mvc-owin-azure-media-services-ad-integration/)

[Azure ACS を使用してトークンを発行する](http://mingfeiy.com/acs-with-key-services)。

[content-protection]: ./media/media-services-content-protection-overview/media-services-content-protection.png

