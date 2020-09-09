---
title: Azure AD 組織用のサインインを設定する
titleSuffix: Azure AD B2C
description: Azure Active Directory B2C で特定の Azure Active Directory 組織用のサインインを設定します。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 04/20/2020
ms.author: mimart
ms.subservice: B2C
ms.custom: fasttrack-edit
ms.openlocfilehash: ce5f373576f13a4b1bdb88b5ffb7869a2d7865cd
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/02/2020
ms.locfileid: "85388359"
---
# <a name="set-up-sign-in-for-a-specific-azure-active-directory-organization-in-azure-active-directory-b2c"></a>Azure Active Directory B2C で特定の Azure Active Directory 組織用のサインインを設定する

Azure Active Directory (Azure AD) B2C で [ID プロバイダー](authorization-code-flow.md)として Azure AD を使用するには、それを表すアプリケーションを作成する必要があります。 この記事では、Azure AD B2C のユーザー フローを使用して、特定の Azure AD 組織のユーザーがサインインできるようにする方法について説明します。

[!INCLUDE [active-directory-b2c-identity-provider-azure-ad](../../includes/active-directory-b2c-identity-provider-azure-ad.md)]

## <a name="configure-azure-ad-as-an-identity-provider"></a>Azure AD を ID プロバイダーとして構成する

1. Azure AD B2C テナントが含まれるディレクトリを必ず使用してください。 上部のメニューにある **[ディレクトリ + サブスクリプション]** フィルターを選択し、ご利用の Azure AD B2C テナントを含むディレクトリを選択します。
1. Azure portal の左上隅にある **[すべてのサービス]** を選択してから、 **[Azure AD B2C]** を検索して選択します。
1. **[ID プロバイダー]** を選択してから、 **[新しい OpenID Connect プロバイダー ]** を選択します。
1. **[名前]** を入力します。 たとえば､「*Contoso Azure AD*」と入力します。
1. **[メタデータ URL]** には、次の URL を入力します。`{tenant}` は、利用する Azure AD テナントのドメイン名に置き換えます。

    ```
    https://login.microsoftonline.com/{tenant}/v2.0/.well-known/openid-configuration
    ```

    たとえば、「 `https://login.microsoftonline.com/contoso.onmicrosoft.com/v2.0/.well-known/openid-configuration` 」のように入力します。

1. **[クライアント ID]** には、前に記録したアプリケーション ID を入力します。
1. **[クライアント シークレット]** には、前に記録したクライアント シークレットを入力します。
1. **[スコープ]** で、`openid profile` を入力します。
1. **[応答の種類]** および **[応答モード]** の既定値はそのままにします。
1. (省略可能) **[ドメインのヒント]** に、「`contoso.com`」と入力します。 詳しくは、「[Azure Active Directory B2C を使用した直接サインインの設定](direct-signin.md#redirect-sign-in-to-a-social-provider)」をご覧ください。
1. **[ID プロバイダー要求のマッピング]** で、次の要求を入力します。

    * **[ユーザー ID]** : *oid*
    * **[表示名]** : *name*
    * **[名]** : *given_name*
    * **[姓]** : *family_name*
    * **[電子メール]** : *unique_name*

1. **[保存]** を選択します。
