---
title: ヨーロッパの顧客のための ID データ ストレージ - Azure AD
description: Azure Active Directory でヨーロッパのお客様の ID 関連データが保存されている場所について説明します。
services: active-directory
author: ajburnle
manager: daveba
ms.author: ajburnle
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: conceptual
ms.date: 09/15/2020
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7a8b013723707c4a3a087a90674227c3d41c5108
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "94836939"
---
# <a name="identity-data-storage-for-european-customers-in-azure-active-directory"></a>Azure Active Directory でのヨーロッパの顧客のための ID データ ストレージ
Microsoft 365 や Azure などの Microsoft Online サービスをサブスクライブしている場合、識別データは Azure AD により、組織によって提供されたアドレスに基づく地理的な場所に格納されます。 ID データの格納場所については、Microsoft Trust Center の「[データの保管場所](https://www.microsoft.com/trustcenter/privacy/where-your-data-is-located)」のセクションを参照してください。

ヨーロッパ内でアドレスを提供したお客様の場合、Azure AD は、ヨーロッパ データセンター内でほとんどの ID データを保持します。 このドキュメントでは、Azure AD サービスによりヨーロッパの外部に格納されているすべてのデータについての情報を提供します。

## <a name="microsoft-azure-ad-multi-factor-authentication"></a>Microsoft Azure AD Multi-Factor Authentication

クラウドベースの Azure AD Multi-Factor Authentication では、ユーザーに最も近いデータセンターで認証が完了します。 Azure AD Multi-Factor Authentication のデータセンターは、北米、ヨーロッパ、アジア太平洋にあります。

* 電話を使用する多要素認証は、米国のデータセンターから発信され、グローバル プロバイダーによってルーティングされます。
* SMS を使用する多要素認証は、グローバル プロバイダーによってルーティングされます。
* ヨーロッパのデータセンターから発信される Microsoft Authenticator アプリのプッシュ通知を使用する多要素認証要求は、ヨーロッパのデータセンターで処理されます。
    * Apple のプッシュ通知などのデバイス ベンダー固有のサービスは、ヨーロッパ外部にある可能性があります。
* ヨーロッパのデータセンターから発信される OATH コードを使用する多要素認証要求は、ヨーロッパで検証されます。

Azure Multi-Factor Authentication Server (MFA Server) およびクラウドベースの Azure AD MFA によって収集されるユーザー情報の詳細については、「[Azure Multi-Factor Authentication によるユーザー データの収集](../authentication/howto-mfa-reporting-datacollection.md)」を参照してください。

## <a name="password-based-single-sign-on-for-enterprise-applications"></a>エンタープライズ アプリケーションでのパスワードベースのシングル サインオン
 
お客様が (Azure AD ギャラリーまたはギャラリー以外を使用して) 新しいエンタープライズ アプリケーションを作成する場合、およびパスワードベースの SSO を有効にする場合は、アプリケーションのサインイン URL とカスタムのキャプチャ サインイン フィールドは米国内に格納されます。 この機能の詳細については、[パスワードベースのシングル サインオンの構成](../manage-apps/configure-password-single-sign-on-non-gallery-applications.md)に関する記事を参照してください。

## <a name="microsoft-azure-active-directory-b2c-azure-ad-b2c"></a>Microsoft Azure Active Directory B2C (Azure AD B2C)

Azure AD B2C ポリシー構成データおよびキー コンテナーは、米国内のデータセンターに格納されます。 これにはユーザーのいかなる個人データも含まれません。 ポリシー構成の詳細については、「[Azure Active Directory B2C: 組み込みのポリシー](../../active-directory-b2c/user-flow-overview.md)」を参照してください。

## <a name="microsoft-azure-active-directory-b2b-azure-ad-b2b"></a>Microsoft Azure Active Directory B2B (Azure AD B2B) 
    
Azure AD B2B は、利用リンクとリダイレクト URL 情報がある招待を米国内のデータセンターに格納します。 さらに、B2B 招待の受信をサブスクライブ解除するユーザーの電子メール アドレスも、米国内のデータセンターに格納されます。

## <a name="microsoft-azure-active-directory-domain-services-azure-ad-ds"></a>Microsoft Azure Active Directory Domain Services (Azure AD DS)

Azure AD DS は、ユーザーが選択した Azure Virtual Network と同じ場所にユーザー データを保存します。 そのため、ネットワークがヨーロッパ以外にある場合、データはレプリケートされ、ヨーロッパ以外の場所に保存されます。

## <a name="federation-in-microsoft-exchange-server-2013"></a>Microsoft Exchange Server 2013 のフェデレーション
    
- アプリケーション識別子 (AppID) - Exchange の組織を識別するために Azure Active Directory 認証システムによって生成される一意の番号
- アプリケーションに対して承認されたフェデレーション ドメインの一覧
- アプリケーションのトークン署名公開キー 

Microsoft Exchange サーバーのフェデレーションの詳細については、「[フェデレーション: Exchange 2013 のヘルプ](/exchange/federation-exchange-2013-help)」の記事を参照してください。


## <a name="other-considerations"></a>その他の考慮事項

Azure AD と統合されるサービスとアプリケーションは、ID データにアクセスします。 ID データの処理方法を判断するために使用する各サービスとアプリケーション、およびそれらが会社のデータ記憶域の要件を満たしているかどうかを評価します。

Microsoft サービスのデータ保存場所の詳細については、Microsoft Trust Center の[データの保存場所](https://www.microsoft.com/trustcenter/privacy/where-your-data-is-located)に関するセクションを参照してください。

## <a name="next-steps"></a>次の手順
上記の機能の詳細については、以下の記事を参照してください。
- [Multi-Factor Authentication とは](../authentication/concept-mfa-howitworks.md)

- [Azure AD のセルフサービス パスワード リセット](../authentication/concept-sspr-howitworks.md)

- [Azure Active Directory B2C とは](../../active-directory-b2c/overview.md)

- [Azure AD B2B コラボレーションとは](../external-identities/what-is-b2b.md)

- [Azure Active Directory (AD) Domain Services](../../active-directory-domain-services/overview.md)