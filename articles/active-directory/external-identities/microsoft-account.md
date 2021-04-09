---
title: Azure AD での Microsoft アカウント (MSA) ID プロバイダー
description: Azure AD を使用して、外部ユーザー (ゲスト) が Microsoft アカウント (MSA) で Azure AD アプリにサインインできるようにします。
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: how-to
ms.date: 03/02/2021
ms.author: mimart
author: msmimart
manager: celestedg
ms.collection: M365-identity-device-management
ms.openlocfilehash: 25d3380277d0e653fd5cb03069b7d91cb363dd95
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "101692935"
---
# <a name="microsoft-account-msa-identity-provider-for-external-identities-preview"></a>External Identities 用の Microsoft アカウント (MSA) ID プロバイダー (プレビュー)

> [!NOTE]
> Microsoft アカウント ID プロバイダーは、Azure Active Directory のパブリック プレビュー機能です。 詳細については、「[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)」を参照してください。

B2B ゲスト ユーザーは、追加の構成を行わなくても、B2B コラボレーションに自分の個人用 Microsoft アカウントを使用できます。 ゲスト ユーザーは、自分の個人用 Microsoft アカウントを使用して、B2B コラボレーションの招待に応じたり、サインアップ ユーザー フローを実行したりすることができます。

コンシューマー向けの Microsoft 製品およびクラウド サービス (Outlook、OneDrive、Xbox LIVE、Microsoft 365 など) にアクセスするために、ユーザー自身が Microsoft アカウントを設定します。 このアカウントは、Microsoft が運営する Microsoft コンシューマー ID アカウント システムを使用して作成、保存されます。

## <a name="guest-sign-in-using-microsoft-accounts"></a>Microsoft アカウントを使用したゲストのサインイン

Microsoft アカウントは、既定では、External Identities の ID プロバイダーの一覧で入手できます。 追加の構成を行わなくても、ゲスト ユーザーは招待フローまたはセルフサービス サインアップ ユーザー フローのどちらかを使用して、自分の Microsoft アカウントでサインインできます。

![ID プロバイダー一覧の Microsoft アカウント](media/microsoft-account/microsoft-account-identity-provider.png)

### <a name="microsoft-account-in-the-invitation-flow"></a>招待フローの Microsoft アカウント

B2B コラボレーションに[ゲスト ユーザーを招待](add-users-administrator.md)する場合、その Microsoft アカウントを、サインインに使用する電子メール アドレスとして指定できます。

![Microsoft アカウントを使用した招待](media/microsoft-account/microsoft-account-invite.png)

### <a name="microsoft-account-in-self-service-sign-up-user-flows"></a>セルフサービス サインアップ ユーザー フローの Microsoft アカウント

Microsoft アカウントは、セルフサービス サインアップ ユーザー フロー用の ID プロバイダー オプションです。 ユーザーは、自分の Microsoft アカウントを使用してアプリケーションにサインアップできます。 最初に、テナントに対して[セルフサービス サインアップを有効にする](self-service-sign-up-user-flow.md)必要があります。 次に、アプリケーション用にユーザー フローを設定し、サインイン オプションの 1 つとして Microsoft アカウントを選択します。

![セルフサービス サインアップ ユーザー フローの Microsoft アカウント](media/microsoft-account/microsoft-account-user-flow.png)

## <a name="next-steps"></a>次のステップ

- [Azure Active Directory B2B コラボレーション ユーザーを追加する](add-users-administrator.md)
- [セルフサービス サインアップをアプリに追加する](self-service-sign-up-user-flow.md)