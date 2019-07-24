---
title: Azure Active Directory Identity Protection で多要素認証登録ポリシーを構成する方法 | Microsoft Docs
description: Azure AD Identity Protection 多要素認証登録ポリシーを構成する方法について説明します。
services: active-directory
keywords: Azure Active Directory Identity Protection, Cloud App Discovery, アプリケーションの管理, セキュリティ, リスク, リスク レベル, 脆弱性, セキュリティ ポリシー
documentationcenter: ''
author: MicrosoftGuyJFlo
manager: daveba
ms.assetid: e7434eeb-4e98-4b6b-a895-b5598a6cccf1
ms.service: active-directory
ms.subservice: identity-protection
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/19/2019
ms.author: joflore
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: cd3d5a19e861bd738f3c618610aadb77da30b384
ms.sourcegitcommit: 6da4959d3a1ffcd8a781b709578668471ec6bf1b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/27/2019
ms.locfileid: "58520345"
---
# <a name="how-to-configure-the-multi-factor-authentication-registration-policy"></a>方法:多要素認証登録ポリシーを構成する

Azure AD Identity Protection は、ポリシーを構成することによって多要素認証 (MFA) 登録の展開を管理するのに役立ちます。 この記事では、このポリシーの使用目的およびその構成方法について説明します。

## <a name="what-is-the-multi-factor-authentication-registration-policy"></a>多要素認証登録ポリシーとは

Azure Multi-Factor Authentication は、本人性の確認において、ユーザー名とパスワードに加えて、その他の要素を取り入れる手法です。 ユーザーのサインインとトランザクションに 2 層構造のセキュリティを確保することができます。  

次のような理由から、ユーザーのサインインに対して Azure Multi-Factor Authentication を要求することをお勧めします。

- 簡単な検証オプションで強力な認証が提供されます。

- アカウント侵害からの組織の保護と復旧の準備において重要な役割を果たします。


詳しくは、「 [Azure Multi-Factor Authentication とは](../authentication/multi-factor-authentication.md)


## <a name="how-do-i-access-the-mfa-registration-policy"></a>MFA 登録ポリシーにアクセスする方法
   
MFA 登録ポリシーは、[[Azure AD Identity Protection]](https://portal.azure.com/#blade/Microsoft_AAD_ProtectionCenter/IdentitySecurityDashboardMenuBlade/SignInPolicy) ページの **[構成]** セクションにあります。
   
![MFA policy](./media/howto-mfa-policy/1014.png)




## <a name="policy-settings"></a>ポリシー設定

サインイン リスク ポリシーを構成する場合は、次を設定する必要があります。

- ポリシーの適用先のユーザーとグループ:

    ![ユーザーとグループ](./media/howto-mfa-policy/11.png)

- 適用されるアクセスの種類:  

    ![Access](./media/howto-mfa-policy/12.png)

- ポリシーの状態:

    ![ポリシーを適用する](./media/howto-mfa-policy/14.png)


ポリシーの構成ダイアログには、構成の影響を見積もるためのオプションが用意されています。

![推定される影響](./media/howto-mfa-policy/15.png)




## <a name="user-experience"></a>ユーザー エクスペリエンス


関連するユーザー エクスペリエンスの概要については、以下を参照してください。

* [多要素認証の登録のフロー](flows.md#multi-factor-authentication-registration)。  
* [Azure AD Identity Protection を使用したサインイン エクスペリエンス](flows.md)。  



## <a name="next-steps"></a>次の手順

Azure AD Identity Protection の概要を入手するには、[Azure AD Identity Protection の概要](overview.md)に関するページを参照してください。
