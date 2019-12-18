---
title: Azure Portal を使用したコンテンツ保護ポリシーの構成 | Microsoft Docs
description: この記事では、Azure ポータルを使用してコンテンツ保護ポリシーを構成する方法を説明します。 また、アセットに対して動的な暗号化を有効にする方法についても説明します。
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.assetid: 270b3272-7411-40a9-ad42-5acdbba31154
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/19/2019
ms.author: juliako
ms.openlocfilehash: 0c2a9612fab6c685cbc690aa9bbc12d1c7b7b746
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/10/2019
ms.locfileid: "74978204"
---
# <a name="configure-content-protection-policies-by-using-the-azure-portal"></a>Azure Portal を使用したコンテンツ保護ポリシーの構成

> [!NOTE]
> このチュートリアルを完了するには、Azure アカウントが必要です。 詳細については、 [Azure の無料試用版サイト](https://azure.microsoft.com/pricing/free-trial/)を参照してください。   > Media Services v2 には新機能は追加されません。 <br/>最新のバージョンである [Media Services v3](https://docs.microsoft.com/azure/media-services/latest/) をご確認ください。 また、[v2 から v3 への移行ガイダンス](../latest/migrate-from-v2-to-v3.md)を参照してください。
>

 Azure Media Services を使用すると、メディアがコンピューターから離れてから、保存、処理、配信されるまでのセキュリティ保護が可能になります。 Media Services では、Advanced Encryption Standard (AES) 128 ビット暗号化キーを使用して、動的に暗号化されたコンテンツを配信できます。 また、PlayReady や Widevine デジタル著作権管理 (DRM) と Apple FairPlay を使用した共通暗号化 (CENC) と共に使用することもできます。 

Media Services は、承認されたクライアントに DRM ライセンスと AES クリア キーを配信するためのサービスを提供しています。 Azure Portal を使用して、すべての種類の暗号化に対して 1 つのキー/ライセンス承認ポリシーを作成できます。

この記事では、ポータルを使用してコンテンツ保護ポリシーを構成する方法を説明します。 また、動的な暗号化をアセットに適用する方法についても説明します。

## <a name="start-to-configure-content-protection"></a>コンテンツ保護の構成を始める
ポータルで Media Services アカウントを使用してグローバル コンテンツ保護を構成するには、次の手順を実行します。

1. [ポータル](https://portal.azure.com/) で Media Services アカウントを選択します。

1. **[設定]**  >  **[コンテンツ保護]** の順に選択します。

    ![コンテンツ保護](./media/media-services-portal-content-protection/media-services-content-protection001.png)

## <a name="keylicense-authorization-policy"></a>キー/ライセンス承認ポリシー
Media Services では、キーまたはライセンスを要求するユーザーを承認する複数の方法がサポートされています。 コンテンツ キー承認ポリシーを構成する必要があります。 キー/ライセンスをクライアントに配信するには、クライアントがポリシーを満たしている必要があります。 コンテンツ キー承認ポリシーには、1 つまたは複数の承認制限 (オープンまたはトークン制限) を指定できます。

ポータルを使用して、すべての種類の暗号化に対して 1 つのキー/ライセンス承認ポリシーを作成できます。

### <a name="open-authorization"></a>オープン承認
オープン制限とは、キーを要求するすべてのユーザーに、システムがキーを提供することを意味します。 この制限は、テストに便利です。 

### <a name="token-authorization"></a>トークン承認
トークン制限ポリシーには、STS (セキュリティ トークン サービス) によって発行されたトークンを含める必要があります。 Media Services では、単純 Web トークン (SWT) 形式と JSON Web トークン (JWT) 形式のトークンがサポートされます。 Media Services は STS を提供しません。 カスタム STS を作成するか、Azure Access Control Service を使用してトークンを発行することができます。 STS は、トークン、トークンの制限の構成で指定した、指定されたキーと問題要求で署名を作成するように構成する必要があります。 トークンが有効で、トークン内の要求がキー (またはライセンス) 用に構成されたものと一致する場合、Media Services キー配信サービスは要求されたキー (またはライセンス) をクライアントに返します。

トークン制限ポリシーを構成する際には、プライマリ検証キー、発行者、および対象ユーザーの各パラメーターを指定する必要があります。 プライマリ検証キーには、トークンの署名に使用されたキーが含まれています。 発行者は、トークンを発行するセキュリティ トークン サービスです。 対象ユーザー (スコープとも呼ばれる) には、トークンの目的、またはトークンがアクセスを承認するリソースが記述されます。 Media Services キー配信サービスでは、トークン内のこれらの値がテンプレート内の値と一致することが検証されます。

![キー/ライセンス承認ポリシー](./media/media-services-portal-content-protection/media-services-content-protection002.png)

## <a name="playready-license-template"></a>PlayReady ライセンス テンプレート
PlayReady ライセンス テンプレートは、PlayReady ライセンスで有効な機能を設定します。 PlayReady ライセンス テンプレートの詳細については、「[Media Services PlayReady ライセンス テンプレートの概要](media-services-playready-license-template-overview.md)」を参照してください。

### <a name="nonpersistent"></a>非永続的
ライセンスを非永続的として構成した場合にのみ、プレーヤーがライセンスを使用するときにこれがメモリに保持されます。  

![非永続コンテンツの保護](./media/media-services-portal-content-protection/media-services-content-protection003.png)

### <a name="persistent"></a>永続的
ライセンスを永続的として構成した場合、これは、クライアントの永続的ストレージに保存されます。

![永続的なコンテンツの保護](./media/media-services-portal-content-protection/media-services-content-protection004.png)

## <a name="widevine-license-template"></a>Widevine ライセンス テンプレート
Widevine ライセンス テンプレートは、Widevine ライセンスで有効な機能を設定します。

### <a name="basic"></a>Basic
**[Basic]** を選択すると、テンプレートはすべて既定値で作成されます。

### <a name="advanced"></a>詳細
Widevine 権利テンプレートの詳細については、「[Widevine ライセンス テンプレートの概要](media-services-widevine-license-template-overview.md)」を参照してください。

![高度なコンテンツの保護](./media/media-services-portal-content-protection/media-services-content-protection005.png)

## <a name="fairplay-configuration"></a>FairPlay 構成
FairPlay の暗号化を有効にするには、 **[FairPlay の構成]** を選択します。 **[アプリ証明書]** を選択し、「**Application Secret Key**」と入力します。 FairPlay の構成と要件の詳細については、「[Apple FairPlay または Microsoft PlayReady による HLS コンテンツの保護](media-services-protect-hls-with-FairPlay.md)」を参照してください。

![FairPlay 構成](./media/media-services-portal-content-protection/media-services-content-protection006.png)

## <a name="apply-dynamic-encryption-to-your-asset"></a>アセットへの動的な暗号化の適用
動的暗号化を利用するには、ソース ファイルを、一連のアダプティブ ビットレート MP4 ファイルにエンコードします。

### <a name="select-an-asset-that-you-want-to-encrypt"></a>暗号化するアセットを選択する
すべてのアセットを表示するには、 **[設定]**  >  **[アセット]** の順にクリックします。

![アセットのオプション](./media/media-services-portal-content-protection/media-services-content-protection007.png)

### <a name="encrypt-with-aes-or-drm"></a>AES または DRM による暗号化
アセットの **[暗号化]** を選択すると、 **[AES]** または **[DRM]** の 2 つの選択肢が表示されます。 

#### <a name="aes"></a>AES
AES クリア キー暗号化は、Smooth Streaming、HLS、および MPEG-DASH のすべてのストリーミング プロトコルで有効になります。

![暗号化の構成](./media/media-services-portal-content-protection/media-services-content-protection008.png)

#### <a name="drm"></a>DRM
1. **[DRM]** を選択すると、さまざまなコンテンツ保護ポリシー (この時点までに構成しておく必要があります) と一連のストリーミング プロトコルが表示されます。

    a. **MPEG-DASH との PlayReady および Widevine**: PlayReady および Widevine DRM により MPEG-DASH ストリームが動的に暗号化されます。

    b. **MPEG-DASH との PlayReady および Widevine + HLS との FairPlay**: PlayReady および Widevine DRM により MPEG-DASH ストリームが動的に暗号化されます。 このオプションを選択すると、FairPlay で HLS ストリームも暗号化されます。

    c. **Smooth Streaming、HLS、および MPEG-DASH とのみの PlayReady**: PlayReady DRM により Smooth Streaming、HLS、MPEG-DASH ストリームが動的に暗号化されます。

    d. **MPEG-DASH とのみの Widevine**: Widevine DRM により MPEG-DASH が動的に暗号化されます。
    
    e. **HLS とのみの FairPlay**: FairPlay により HLS ストリームが動的に暗号化されます。

1. FairPlay 暗号化を有効にするには、 **[Content Protection Global Settings]\(コンテンツ保護のグローバル設定\)** ブレードで、 **[FairPlay の構成]** を選択します。 **[アプリ証明書]** を選択し、「**Application Secret Key**」と入力します。

    ![暗号化の種類](./media/media-services-portal-content-protection/media-services-content-protection009.png)

1. 暗号化を選択したら、 **[適用]** を選択します。

>[!NOTE] 
>AES で暗号化された HLS を Safari で再生する予定の場合は、[Safari の暗号化された HLS](https://azure.microsoft.com/blog/how-to-make-token-authorized-aes-encrypted-hls-stream-working-in-safari/) に関するのブログ投稿を参照してください。

## <a name="additional-notes"></a>その他のメモ

* Widevine は Google Inc. によって提供されるサービスであり、Google Inc. のサービス使用条件とプライバシー ポリシーが適用されます。

## <a name="next-steps"></a>次の手順
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>フィードバックの提供
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

