---
title: Azure Active Directory Identity Protection で多要素認証登録ポリシーを構成する方法 | Microsoft Docs
description: Azure AD Identity Protection 多要素認証登録ポリシーを構成する方法について説明します。
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: article
ms.date: 05/01/2019
author: MicrosoftGuyJFlo
manager: daveba
ms.author: joflore
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: 434d07163713a139b42a5dbe1664f81dafc2a1ca
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/13/2019
ms.locfileid: "67108936"
---
# <a name="how-to-configure-the-azure-multi-factor-authentication-registration-policy"></a>方法:Azure Multi-Factor Authentication 登録ポリシーを構成する

Azure AD Identity Protection は、MFA 登録を要求する条件付きアクセス ポリシーを構成することによって、サインインするアプリに関係なく、多要素認証 (MFA) 登録の展開を管理するのに役立ちます。 この記事では、このポリシーの使用目的およびその構成方法について説明します。

## <a name="what-is-the-azure-multi-factor-authentication-registration-policy"></a>Azure Multi-Factor Authentication 登録ポリシーとは

Azure Multi-Factor Authentication は、ユーザー名とパスワードに加えて、その他の要素を使用することでユーザーを確認する手段を提供します。 ユーザーのサインインに第 2 のセキュリティ層を提供します。ユーザーが MFA プロンプトに応答できるようにするには、まず Azure Multi-Factor Authentication に登録する必要があります。

次のような理由から、ユーザーのサインインに対して Azure Multi-Factor Authentication を要求することをお勧めします。

- 簡単な検証オプションで強力な認証が提供されます。
- ID 保護におけるリスク イベントからの組織の保護と復旧の準備において重要な役割を果たします。

MFA の詳細については、[Azure Multi-Factor Authentication とは何か](../authentication/howto-mfa-getstarted.md)に関する記事を参照してください。

## <a name="how-do-i-access-the-registration-policy"></a>登録ポリシーにアクセスする方法

MFA 登録ポリシーは、[[Azure AD Identity Protection]](https://portal.azure.com/#blade/Microsoft_AAD_ProtectionCenter/IdentitySecurityDashboardMenuBlade/SignInPolicy) ページの **[構成]** セクションにあります。

![MFA policy](./media/howto-mfa-policy/1014.png)

## <a name="policy-settings"></a>ポリシー設定

MFA 登録ポリシーを構成する場合、次の構成の変更を行う必要があります。

- ポリシーが適用されるユーザーとグループ。 組織の[緊急アクセス アカウント](../users-groups-roles/directory-emergency-access.md)は必ず除外してください。

    ![ユーザーとグループ](./media/howto-mfa-policy/11.png)

- 適用する制御 - **Azure MFA の登録が必要です**

    ![Access](./media/howto-mfa-policy/12.png)

- [ポリシーの適用] を **[オン]** に設定する必要があります。

    ![ポリシーを適用する](./media/howto-mfa-policy/14.png)

- ポリシーを**保存**します

## <a name="user-experience"></a>ユーザー エクスペリエンス

関連するユーザー エクスペリエンスの概要については、以下を参照してください。

- [多要素認証の登録のフロー](flows.md#multi-factor-authentication-registration)。  
- [Azure AD Identity Protection を使用したサインイン エクスペリエンス](flows.md)。  

## <a name="next-steps"></a>次の手順

Azure AD Identity Protection の概要を入手するには、[Azure AD Identity Protection の概要](overview.md)に関するページを参照してください。
