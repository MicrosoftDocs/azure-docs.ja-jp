---
title: ヨーロッパの顧客のための ID データ ストレージ - Azure Active Directory | Microsoft Docs
description: Azure Active Directory でヨーロッパのお客様の ID 関連データが保存されている場所について説明します。
services: active-directory
author: msaburnley
manager: daveba
ms.author: ajburnle
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: conceptual
ms.date: 03/04/2019
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 178f81cf42e5c57be4a0b69ada6560d46951a3a3
ms.sourcegitcommit: 124c3112b94c951535e0be20a751150b79289594
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/10/2019
ms.locfileid: "68942840"
---
# <a name="identity-data-storage-for-european-customers-in-azure-active-directory"></a>Azure Active Directory でのヨーロッパの顧客のための ID データ ストレージ
Office 365 や Azure などの Microsoft Online サービスをサブスクライブしている場合、ID データは Azure AD により、組織によって提供されるアドレスに基づいて地理的な場所に格納されます。 ID データの格納場所については、Microsoft Trust Center の「[データの保管場所](https://www.microsoft.com/trustcenter/privacy/where-your-data-is-located)」のセクションを参照してください。

ヨーロッパ内でアドレスを提供したお客様の場合、Azure AD は、ヨーロッパ データセンター内でほとんどの ID データを保持します。 このドキュメントでは、Azure AD サービスによりヨーロッパの外部に格納されているすべてのデータについての情報を提供します。

## <a name="microsoft-azure-multi-factor-authentication-mfa"></a>Microsoft Azure 多要素認証 (MFA)
    
- 電話または米国データセンターからの SMS を使用するすべての 2 要素認証は、グローバル プロバイダーによってもルーティングされます。
- Microsoft Authenticator アプリを使用するプッシュ通知は、米国内データセンターから発信されます。 さらに、デバイス ベンダー固有のサービスも機能することができ、それらのサービスもヨーロッパの外部からである場合があります。
- OATH コードは常に米国で検証されます。 

Azure Multi-Factor Authentication Server (MFA Server) およびクラウドベースの Azure MFA によって収集されるユーザー情報の詳細については、「[Azure Multi-Factor Authentication によるユーザー データの収集](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-reporting-datacollection)」を参照してください。

## <a name="microsoft-azure-active-directory-b2c-azure-ad-b2c"></a>Microsoft Azure Active Directory B2C (Azure AD B2C)

Azure AD B2C ポリシー構成データおよびキー コンテナーは、米国内のデータセンターに格納されます。 これにはユーザーのいかなる個人データも含まれません。 ポリシー構成の詳細については、「[Azure Active Directory B2C:組み込みのポリシー](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-reference-policies)」を参照してください。

## <a name="microsoft-azure-active-directory-b2b-azure-ad-b2b"></a>Microsoft Azure Active Directory B2B (Azure AD B2B) 
    
Azure AD B2B は、利用リンクとリダイレクト URL 情報がある招待を米国内のデータセンターに格納します。 さらに、B2B 招待の受信をサブスクライブ解除するユーザーの電子メール アドレスも、米国内のデータセンターに格納されます。

## <a name="microsoft-azure-active-directory-domain-services-azure-ad-ds"></a>Microsoft Azure Active Directory Domain Services (Azure AD DS)

Azure AD DS は、ユーザーが選択した Azure Virtual Network と同じ場所にユーザー データを保存します。 そのため、ネットワークがヨーロッパ以外にある場合、データはレプリケートされ、ヨーロッパ以外の場所に保存されます。

## <a name="federation-in-microsoft-exchange-server-2013"></a>Microsoft Exchange Server 2013 のフェデレーション
    
- アプリケーション識別子 (AppID) - Exchange の組織を識別するために Azure Active Directory 認証システムによって生成される一意の番号
- アプリケーションに対して承認されたフェデレーション ドメインの一覧
- アプリケーションのトークン署名公開キー 

Microsoft Exchange サーバーのフェデレーションの詳細については、「[フェデレーション: Exchange 2013 のヘルプ](https://docs.microsoft.com/exchange/federation-exchange-2013-help)」の記事を参照してください。


## <a name="other-considerations"></a>その他の考慮事項

Azure AD と統合されるサービスとアプリケーションは、ID データにアクセスします。 ID データの処理方法を判断するために使用する各サービスとアプリケーション、およびそれらが会社のデータ記憶域の要件を満たしているかどうかを評価します。

Microsoft サービスのデータ保存場所の詳細については、Microsoft セキュリティ センターの[データの保存場所](https://www.microsoft.com/trustcenter/privacy/where-your-data-is-located)に関するセクションを参照してください。

## <a name="next-steps"></a>次の手順
上記の機能の詳細については、以下の記事を参照してください。
- [Multi-Factor Authentication とは](https://docs.microsoft.com/azure/active-directory/authentication/multi-factor-authentication)

- [Azure AD のセルフ サービスによるパスワードのリセット](https://docs.microsoft.com/azure/active-directory/authentication/active-directory-passwords-overview)

- [Azure Active Directory B2C とは](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-overview)

- [Azure AD B2B コラボレーションとは](https://docs.microsoft.com/azure/active-directory/active-directory-b2b-what-is-azure-ad-b2b)

- [Azure Active Directory (AD) Domain Services](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-overview)
