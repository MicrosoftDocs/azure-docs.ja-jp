---
title: 組織での Multi-Factor Authentication を有効にする - Azure Active Directory
description: ライセンスに基づいて組織の Azure MFA を有効にします
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 12/06/2019
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: fe0d79f228ee2b84c00a35d4836cbda6a4847a42
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/08/2019
ms.locfileid: "74932176"
---
# <a name="enable-multi-factor-authentication-for-your-organization"></a>組織での Multi-Factor Authentication を有効にする

組織で所有しているライセンスに基づいて Azure Active Directory (AD) ユーザーに対して Azure Multi-Factor Authentication (MFA) を有効にする方法は複数あります。 

![シグナルを調査し、必要に応じて MFA を適用する](./media/concept-fundamentals-mfa-get-started/verify-signals-and-perform-mfa-if-required.png)

私たちの研究によると、MFA を使用すると、アカウントは 99.9% 以上侵害されにくくなります。

では、侵害される前に、組織が多要素認証を無料で有効にするにはどうすればよいでしょうか。

## <a name="free-option"></a>Free オプション

Azure AD の無料特典を利用しているお客様は、[セキュリティ既定値](../fundamentals/concept-fundamentals-security-defaults.md)を使用して、環境内で多要素認証を有効にすることができます。

## <a name="office-365"></a>Office 365

Office 365 をお持ちのお客様には、次の 2 つのオプションがあります。

- Azure AD を通じて[セキュリティ既定値](concept-fundamentals-security-defaults.md)を有効にして、Azure Multi-Factor Authentication を使用してすべてのユーザーを保護できます。
- 組織が多要素認証を提供する際により細かい細分性が必要な場合、Office ライセンスには[ユーザーごとの MFA](../authentication/howto-mfa-userstates.md) 機能が含まれています。 ユーザーごとの MFA は、管理者がユーザーごとに個別に有効化および適用します。

## <a name="azure-ad-premium-p1"></a>Azure AD Premium P1

Azure AD Premium P1、または Enterprise Mobility + Security E3、Microsoft 365 F1、Microsoft 365 E3 など、この機能を含む同様のライセンスを持つお客様の場合: 

最適なユーザー エクスペリエンスを実現するには、[条件付きアクセス ポリシー](../conditional-access/concept-conditional-access-policy-common.md)を使用することをお勧めします。

## <a name="azure-ad-premium-p2"></a>Azure AD Premium P2

Azure AD Premium P2、または Enterprise Mobility + Security E5 や Microsoft 365 E5 など、この機能を含む同様のライセンスを持つお客様の場合: 

最適なユーザー エクスペリエンスと適用の柔軟性を実現するために、[条件付きアクセス ポリシー](../conditional-access/concept-conditional-access-policy-common.md)を [Identity Protection](../identity-protection/overview-v2.md) リスク ポリシーと共に使用することをお勧めします。

## <a name="authentication-methods"></a>認証方法

|   | セキュリティの既定値 | その他すべての方法 |
| --- | --- | --- |
| モバイル アプリでの通知 | X | X |
| モバイル アプリからの確認コードまたはハードウェア トークン |   | X |
| 電話へのテキスト メッセージ |   | X |
| 電話の呼び出し |   | X |
| アプリ パスワード |   | X** |

** アプリ パスワードは、管理者が有効にした場合にのみ、レガシ認証シナリオでのユーザーごとの MFA でのみ使用できます。

## <a name="next-steps"></a>次の手順

[Azure AD の価格ページ](https://azure.microsoft.com/pricing/details/active-directory/)
