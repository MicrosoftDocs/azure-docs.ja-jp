---
title: 一般的な条件付きアクセス ポリシー - Azure Active Directory
description: 組織で一般的に使用される条件付きアクセス ポリシー
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 07/02/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb, rogoya
ms.collection: M365-identity-device-management
ms.openlocfilehash: bdaaed72b85bc3c0196a8897be311efa5fb714e2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "92366345"
---
# <a name="common-conditional-access-policies"></a>一般的な条件付きアクセス ポリシー

[セキュリティの既定値群](../fundamentals/concept-fundamentals-security-defaults.md)は一部の人々には適していますが、多くの組織では、それによって提供されるものよりも高い柔軟性が必要です。 たとえば、多くの組織では、緊急アクセス用または非常用の管理アカウントのような特定のアカウントを、多要素認証を要求する条件付きアクセス ポリシーから除外できることが必要です。 それらの組織では、この記事で参照している一般的なポリシーが役に立つ可能性があります。

![Azure portal における条件付きアクセス ポリシー](./media/concept-conditional-access-policy-common/conditional-access-policies-azure-ad-listing.png)

## <a name="emergency-access-accounts"></a>緊急アクセス アカウント

緊急アクセス アカウントとそれが重要である理由の詳細については、以下の記事を参照してください。 

* [Azure AD で緊急アクセス用アカウントを管理する](../roles/security-emergency-access.md)
* [Azure Active Directory で回復性があるアクセス制御管理戦略を作成する](../authentication/concept-resilient-controls.md)

## <a name="typical-policies-deployed-by-organizations"></a>組織によってデプロイされる一般的なポリシー

* [レガシ認証をブロックする](howto-conditional-access-policy-block-legacy.md)\*
* [管理者に対して MFA を必須にする](howto-conditional-access-policy-admin-mfa.md)\*
* [Azure 管理のために MFA を必須にする](howto-conditional-access-policy-azure-management.md)\*
* [すべてのユーザーに対して MFA を必須にする](howto-conditional-access-policy-all-users-mfa.md)\*

\* これらの 4 つのポリシーをまとめて構成すると、[セキュリティの既定値群](../fundamentals/concept-fundamentals-security-defaults.md)によって有効にされた機能が模倣されます。

## <a name="additional-policies"></a>追加のポリシー

* [サインイン リスクベースの条件付きアクセス (Azure AD Premium P2 が必要)](howto-conditional-access-policy-risk.md)
* [ユーザー リスクベースの条件付きアクセス (Azure AD Premium P2 が必要)](howto-conditional-access-policy-risk-user.md)
* [信頼できる場所を MFA 登録で必須にする](howto-conditional-access-policy-registration.md)
* [場所ごとにアクセスをブロックする](howto-conditional-access-policy-location.md)
* [準拠しているデバイスを要求する](howto-conditional-access-policy-compliant-device.md)
* [特定のアプリ以外のアクセスをブロックする](howto-conditional-access-policy-block-access.md)

## <a name="next-steps"></a>次のステップ

- [条件付きアクセスの What If ツールを使用してサインイン動作をシミュレートします。](troubleshoot-conditional-access-what-if.md)

- [条件付きアクセスのレポート専用モードを使用して、新しいポリシー決定の影響を判断します。](concept-conditional-access-report-only.md)
