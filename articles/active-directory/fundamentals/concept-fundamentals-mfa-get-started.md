---
title: 組織での Azure AD Multi-Factor Authentication - Azure Active Directory
description: 組織のライセンス モデルに基づいて利用できる Azure AD Multi-Factor Authentication の機能について説明します
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 03/18/2020
ms.author: daveba
author: daveba
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 85e7f02f8cbda6218396bf4a9a4654a113b7817c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "94836744"
---
# <a name="overview-of-azure-ad-multi-factor-authentication-for-your-organization"></a>組織での Azure AD Multi-Factor Authentication の概要

Azure Active Directory (AD) ユーザーに対して Azure AD Multi-Factor Authentication を有効にする方法は、組織が所有しているライセンスに応じて、複数用意されています。 

![シグナルを調査し、必要に応じて MFA を適用する](./media/concept-fundamentals-mfa-get-started/verify-signals-and-perform-mfa-if-required.png)

私たちの研究によると、多要素認証 (MFA) を使用した場合、アカウントが侵害されるリスクは 99.9% 以上低下します。

では、侵害される前に、組織が MFA を無料で有効にするにはどうすればよいでしょうか。

## <a name="free-option"></a>Free オプション

Azure AD の無料特典を利用しているお客様は、[セキュリティ既定値](../fundamentals/concept-fundamentals-security-defaults.md)を使用して、環境内で多要素認証を有効にすることができます。

## <a name="microsoft-365-business-e3-or-e5"></a>Microsoft 365 Business、E3、または E5

Microsoft 365 をお持ちのお客様には、次の 2 つのオプションがあります。

* Azure AD Multi-Factor Authentication は、すべてのユーザーの、すべてのサインイン イベントに対して有効にするか無効にするかのどちらかとなります。 ユーザーのサブセットに対してのみ、もしくは特定のシナリオでのみ多要素認証を有効にすることはできません。 管理は Office 365 ポータルを介して行われます。 
* ユーザー エクスペリエンスを向上させるには、Azure AD Premium P1 または P2 にアップグレードし、条件付きアクセスを使用します。 詳細については、多要素認証を使用した Microsoft 365 リソースのセキュリティ保護に関するページを参照してください。

## <a name="azure-ad-premium-p1"></a>Azure AD Premium P1

Azure AD Premium P1、または Enterprise Mobility + Security E3、Microsoft 365 F1、Microsoft 365 E3 など、この機能を含む同様のライセンスを持つお客様の場合: 

[Azure AD 条件付きアクセス](../authentication/tutorial-enable-azure-mfa.md)を使用して、ビジネス要件に合わせて特定のシナリオやイベントの際に多要素認証をユーザーに求めることができます。

## <a name="azure-ad-premium-p2"></a>Azure AD Premium P2

Azure AD Premium P2、または Enterprise Mobility + Security E5 や Microsoft 365 E5 など、この機能を含む同様のライセンスを持つお客様の場合: 

最も強力なセキュリティのポジションと、向上したユーザー エクスペリエンスを提供します。 [リスク ベースの条件付きアクセス](../conditional-access/howto-conditional-access-policy-risk.md)を Azure AD Premium P1 の機能に追加することで、ユーザーのパターンに適応し、多要素認証の回数を最小限に抑えます。

## <a name="authentication-methods"></a>認証方法

| Method | セキュリティの既定値群 | その他すべての方法 |
| --- | --- | --- |
| モバイル アプリでの通知 | X | X |
| モバイル アプリからの確認コードまたはハードウェア トークン |   | X |
| 電話へのテキスト メッセージ |   | X |
| 電話の呼び出し |   | X |

## <a name="next-steps"></a>次のステップ

まずは、[Azure AD Multi-Factor Authentication を使用してユーザーのサインイン イベントのセキュリティを確保する](../authentication/tutorial-enable-azure-mfa.md)ためのチュートリアルを参照してください。

ライセンスの詳細については、「[Azure AD Multi-Factor Authentication の機能とライセンス](../authentication/concept-mfa-licensing.md)」を参照してください。
