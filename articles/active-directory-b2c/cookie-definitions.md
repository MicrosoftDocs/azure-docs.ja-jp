---
title: Cookie の定義 - Azure Active Directory B2C | Microsoft Docs
description: Azure Active Directory B2C で使われる Cookie の定義を提供します。
services: active-directory-b2c
author: davidmu1
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 03/18/2019
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: ac422a00a919903063c96ac096882036b99a63e3
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/18/2019
ms.locfileid: "59786746"
---
# <a name="cookies-definitions-for-azure-active-directory-b2c"></a>Azure Active Directory B2C での Cookie の定義

次の表では、Azure Active Directory B2C で使われる Cookie の一覧を示します。

| Name | Domain | 有効期限 | 目的 |
| ----------- | ------ | -------------------------- | --------- |
| x-ms-cpim-admin | main.b2cadmin.ext.azure.com | [ブラウザー セッション](active-directory-b2c-token-session-sso.md)の終了 | テナント間でユーザーのメンバーシップ データを保持します。 ユーザーがメンバーになっているテナントと、メンバーシップのレベル (管理者またはユーザー) です。 |
| x-ms-cpim-slice | login.microsoftonline.com、b2clogin.com、ブランド化されたドメイン | [ブラウザー セッション](active-directory-b2c-token-session-sso.md)の終了 | 適切な運用インスタンスに要求をルーティングするために使われます。 |
| x-ms-cpim-trans | login.microsoftonline.com、b2clogin.com、ブランド化されたドメイン | [ブラウザー セッション](active-directory-b2c-token-session-sso.md)の終了 | トランザクション (Azure AD B2C に対する認証要求の数) と現在のトランザクションを追跡するために使われます。 |
| x-ms-cpim-sso:{Id} | login.microsoftonline.com、b2clogin.com、ブランド化されたドメイン | [ブラウザー セッション](active-directory-b2c-token-session-sso.md)の終了 | SSO セッションを維持するために使われます。 |
| x-ms-cpim-cache:{id}_n | login.microsoftonline.com、b2clogin.com、ブランド化されたドメイン | [ブラウザー セッション](active-directory-b2c-token-session-sso.md)の終了、認証の成功 | 要求の状態を維持するために使われます。 |
| x-ms-cpim-csrf | login.microsoftonline.com、b2clogin.com、ブランド化されたドメイン | [ブラウザー セッション](active-directory-b2c-token-session-sso.md)の終了 | クロスサイト リクエスト フォージェリの保護に使われる CRSF トークン。 |
| x-ms-cpim-dc | login.microsoftonline.com、b2clogin.com、ブランド化されたドメイン | [ブラウザー セッション](active-directory-b2c-token-session-sso.md)の終了 | Azure AD B2C のネットワーク ルーティングに使われます。 |
| x-ms-cpim-ctx | login.microsoftonline.com、b2clogin.com、ブランド化されたドメイン | [ブラウザー セッション](active-directory-b2c-token-session-sso.md)の終了 | Context |
| x-ms-cpim-rp | login.microsoftonline.com、b2clogin.com、ブランド化されたドメイン | [ブラウザー セッション](active-directory-b2c-token-session-sso.md)の終了 | リソース プロバイダー テナントのメンバーシップ データの格納に使われます。 |
| x-ms-cpim-rc | login.microsoftonline.com、b2clogin.com、ブランド化されたドメイン | [ブラウザー セッション](active-directory-b2c-token-session-sso.md)の終了 | リレー Cookie を格納するために使われます。 |

