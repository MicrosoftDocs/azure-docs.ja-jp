---
title: "Azure ポイント対サイト VPN 接続について | Microsoft Docs"
description: "この記事は、ポイント対サイト接続を理解するうえで役に立つほか、どの種類の P2S VPN ゲートウェイ認証を使用するかを決定するうえで役立ちます。"
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: timlt
editor: 
tags: azure-resource-manager,azure-service-management
ms.assetid: 
ms.service: vpn-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/25/2017
ms.author: cherylmc
ms.openlocfilehash: f19b58ad8f12169acefd74bfe86a28ad950dad34
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="about-point-to-site-vpn"></a>ポイント対サイト VPN について

ポイント対サイト (P2S) VPN ゲートウェイ接続では、個々のクライアント コンピューターから仮想ネットワークへの、セキュリティで保護された接続を作成することができます。 P2S 接続は、クライアント コンピューターから接続を開始することによって確立されます。 このソリューションは、在宅勤務の人間が自宅や会議室など、リモートの場所から Azure VNet に接続する場合に便利です。 P2S VPN は、VNet への接続が必要なクライアントがごく少ない場合に、S2S VPN の代わりに使用するソリューションとしても便利です。

## <a name="protocol"></a>P2S で使用されるプロトコル

ポイント対サイト VPN では、次のいずれかのプロトコルを使用できます。

* Secure Socket トンネリング プロトコル (SSTP)。これは、SSL ベースの独自の VPN プロトコルです。 SSL VPN ソリューションはファイアウォールを通過できます。これは、ほとんどのファイアウォールで開かれている TCP ポート 443 が SSL で使用されるためです。 SSTP は、Windows デバイスでのみサポートされます。 Azure では、SSTP を備えたすべてのバージョンの Windows (Windows 7 以降) がサポートされています。

* IKEv2 VPN。これは、標準ベースの IPsec VPN ソリューションです。 IKEv2 VPN は、Mac デバイス (OSX バージョン 10.11 以上) から接続する際に使用できます。

Windows デバイスと Mac デバイスが混在するクライアント環境の場合、SSTP と IKEv2 の両方を構成してください。

>[!NOTE]
>P2S 用 IKEv2 は現在プレビューの段階です。
>

## <a name="authentication"></a>P2S VPN クライアントの認証方法

Azure が P2S VPN 接続を受け入れる前に、ユーザーはまず認証を受ける必要があります。 Azure では、接続するユーザーを認証するメカニズムが 2 つ用意されています。

### <a name="authenticate-using-native-azure-certificate-authentication"></a>ネイティブ Azure 証明書認証を使用した認証

ネイティブ Azure 証明書認証を使用する場合、デバイス上にあるクライアント証明書が、接続するユーザーの認証に使用されます。 クライアント証明書は信頼されたルート証明書から生成され、各クライアント コンピューターにインストールされます。 エンタープライズ ソリューションを使って生成されたルート証明書を使用することも、自己署名証明書を生成することもできます。

クライアント証明書の検証は、P2S VPN 接続が確立される間、VPN ゲートウェイによって実行されます。 検証にはルート証明書が必要なため、そのルート証明書を Azure にアップロードする必要があります。 

### <a name="authenticate-using-active-directory-ad-domain-server"></a>Active Directory (AD) ドメイン サーバーを使用した認証

AD ドメイン認証では、ユーザーは組織のドメイン資格情報を使用して Azure に接続できます。 これには AD サーバーと統合する RADIUS サーバーが必要です。 また、組織は既存の RADIUS デプロイを利用することもできます。   
 RADIUS サーバーは、オンプレミスまたは Azure VNet にデプロイできます。 認証が行われる間、Azure VPN ゲートウェイがパススルーとして機能し、接続するデバイスと RADIUS サーバーの間で認証メッセージを転送します。 そのため、ゲートウェイが RADIUS サーバーにアクセスできることが重要です。 RADIUS サーバーがオンプレミスに存在する場合、アクセスのために、Azure からオンプレミス サイトへの VPN S2S 接続が必要になります。  
 また、RADIUS サーバーは、AD 証明書サービスとも統合できます。 これにより、Azure 証明書認証の代替手段として、P2S 証明書認証に RADIUS サーバーとエンタープライズ証明書デプロイを使用できます。 この利点は、ルート証明書と失効した証明書を Azure にアップロードする必要がないことです。

RADIUS サーバーは、他の外部 ID システムと統合することもできます。 これにより、多要素認証のオプションなど、P2S VPN 向けの多数の認証オプションを利用できるようになります。

>[!NOTE]
>P2S 用 RADIUS 認証は現在プレビューの段階です。
>

![ポイント対サイト]](./media/point-to-site-about/p2s.png "ポイント対サイト")

### <a name="configuration-requirements-for-client-devices"></a>クライアント デバイスの構成要件

ユーザーは、P2S 用の Windows デバイスと Mac デバイスでネイティブ VPN クライアントを使用します。 これらのネイティブ クライアントが Azure に接続するために必要な設定が含まれた VPN クライアント構成 zip ファイルは、Azure に用意されています。

  * Windows デバイスの場合、VPN クライアント構成は、ユーザーがデバイスにインストールするインストーラー パッケージで構成されています。
  * Mac デバイスの場合、これは、ユーザーがデバイスにインストールする mobileconfig ファイルで構成されています。

zip ファイルでは、Azure 側のいくつかの重要な設定の値も指定されており、この値を使用することで、これらのデバイス用に独自のプロファイルを作成できます。 このような値には、VPN ゲートウェイ アドレス、構成されたトンネルの種類、ルート、ゲートウェイ検証用のルート証明書などがあります。

### <a name="which-gateway-skus-support-p2s-vpn"></a>P2S VPN がサポートされるゲートウェイ SKU の種類

[!INCLUDE [p2s-skus](../../includes/vpn-gateway-table-point-to-site-skus-include.md)]

* 合計スループット ベンチマークは、1 つのゲートウェイから集計された複数のトンネルの測定値に基づいています。 インターネットのトラフィックの状況とアプリケーションの動作に依存するため、これは保証されたスループットではありません。
* 料金情報については、価格に関するページをご覧ください。 
* SLA (サービス レベル アグリーメント) 情報は SLA のページで確認できます。

## <a name="faqcert"></a>ネイティブ Azure 証明書認証に関する FAQ

[!INCLUDE [vpn-gateway-point-to-site-faq-include](../../includes/vpn-gateway-faq-p2s-azurecert-include.md)]

## <a name="faqradius"></a>RADIUS 認証に関する FAQ

[!INCLUDE [vpn-gateway-point-to-site-faq-include](../../includes/vpn-gateway-faq-p2s-radius-include.md)]

## <a name="next-steps"></a>次のステップ

[P2S 接続の構成 - RADIUS 認証](point-to-site-how-to-radius-ps.md)

[P2S 接続の構成 - Azure ネイティブ証明書認証](vpn-gateway-howto-point-to-site-rm-ps.md)