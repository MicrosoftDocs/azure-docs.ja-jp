---
title: Azure AD 組織用のサインインを設定する
titleSuffix: Azure AD B2C
description: Azure Active Directory B2C で特定の Azure Active Directory 組織用のサインインを設定します。
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 08/08/2019
ms.author: marsma
ms.subservice: B2C
ms.custom: fasttrack-edit
ms.openlocfilehash: d864561273f89c510633bbc95a2d037a270401f8
ms.sourcegitcommit: 5b9287976617f51d7ff9f8693c30f468b47c2141
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/09/2019
ms.locfileid: "74947679"
---
# <a name="set-up-sign-in-for-a-specific-azure-active-directory-organization-in-azure-active-directory-b2c"></a>Azure Active Directory B2C で特定の Azure Active Directory 組織用のサインインを設定する

Azure Active Directory (Azure AD) B2C で [ID プロバイダー](active-directory-b2c-reference-oauth-code.md)として Azure AD を使用するには、それを表すアプリケーションを作成する必要があります。 この記事では、Azure AD B2C のユーザー フローを使用して、特定の Azure AD 組織のユーザーがサインインできるようにする方法について説明します。

## <a name="create-an-azure-ad-app"></a>Azure AD アプリの作成

特定の Azure AD 組織のユーザーのサインインを有効にするには、組織の Azure AD テナント内でアプリケーションを登録する必要があります。このテナントは、お使いの Azure AD B2C テナントと同じではありません。

1. [Azure Portal](https://portal.azure.com) にサインインします。
2. ご自分の Azure AD テナントが含まれるディレクトリを必ず使用してください。 上部メニューで **[ディレクトリ + サブスクリプション] フィルター**を選択し、ご利用の Azure AD テナントが含まれるディレクトリを選択します。 これは、Azure AD B2C テナントと同じテナントではありません。
3. Azure portal の左上隅にある **[すべてのサービス]** を選択し、 **[アプリの登録]** を検索して選択します。
4. **[新規登録]** を選択します。
5. アプリケーションの名前を入力します。 たとえば、「 `Azure AD B2C App` 」のように入力します。
6. このアプリケーションには **[この組織のディレクトリ内のアカウントのみ]** の選択をそのまま使用します。
7. **[リダイレクト URL]** では、値 **[Web]** をそのまま使用し、次の URL をすべて小文字で入力します。`your-B2C-tenant-name` は、お使いの Azure AD B2C テナントの名前に置き換えます。 例: `https://fabrikam.b2clogin.com/fabrikam.onmicrosoft.com/oauth2/authresp`

    ```
    https://your-B2C-tenant-name.b2clogin.com/your-B2C-tenant-name.onmicrosoft.com/oauth2/authresp
    ```

    ここでは、すべての URL で [b2clogin.com](b2clogin.md) を使用してください。

8. **[登録]** をクリックします。 後で使用するために **[アプリケーション (クライアント) ID]** をコピーします。
9. アプリケーション メニューで **[証明書とシークレット]** を選択してから、 **[新しいクライアント シークレット]** を選択します。
10. クライアント シークレットの名前を入力します。 たとえば、「 `Azure AD B2C App Secret` 」のように入力します。
11. 有効期限を選択します。 このアプリケーションには、 **[1 年]** の選択をそのまま使用します。
12. **[追加]** を選択して、後で使用するために表示されている新しいクライアント シークレットの値をコピーします。

## <a name="configure-azure-ad-as-an-identity-provider"></a>Azure AD を ID プロバイダーとして構成する

1. Azure AD B2C テナントが含まれるディレクトリを必ず使用してください。 上部のメニューにある **[ディレクトリ + サブスクリプション]** フィルターを選択し、ご利用の Azure AD B2C テナントを含むディレクトリを選択します。
1. Azure portal の左上隅にある **[すべてのサービス]** を選択してから、 **[Azure AD B2C]** を検索して選択します。
1. **[ID プロバイダー]** を選択してから、 **[新しい OpenID Connect プロバイダー ]** を選択します。
1. **[名前]** を入力します。 たとえば､「*Contoso Azure AD*」と入力します。
1. **[メタデータ URL]** には、次の URL を入力します。`your-AD-tenant-domain` は、利用する Azure AD テナントのドメイン名に置き換えます。

    ```
    https://login.microsoftonline.com/your-AD-tenant-domain/.well-known/openid-configuration
    ```

    たとえば、「 `https://login.microsoftonline.com/contoso.onmicrosoft.com/.well-known/openid-configuration` 」のように入力します。

    `https://login.microsoftonline.com/contoso.onmicrosoft.com/v2.0/.well-known/openid-configuration` などの Azure AD v2.0 メタデータ エンドポイント使用**しないでください**。 これを行うと、サインインを試みたときに `AADB2C: A claim with id 'UserId' was not found, which is required by ClaimsTransformation 'CreateAlternativeSecurityId' with id 'CreateAlternativeSecurityId' in policy 'B2C_1_SignUpOrIn' of tenant 'contoso.onmicrosoft.com'` のようなエラーが発生します。

1. **[クライアント ID]** には、前に記録したアプリケーション ID を入力します。
1. **[クライアント シークレット]** には、前に記録したクライアント シークレットを入力します。
1. **[スコープ]** 、 **[応答の種類]** 、および **[応答モード]** の既定値はそのままにします。
1. (省略可能) **Domain_hint** に値を入力します。 たとえば、「*ContosoAD*」とします。 この値は､要求で *domain_hint* を使用するこの ID プロバイダーを参照するときに使用します｡
1. **[ID プロバイダー要求のマッピング]** で、次の要求マッピング値を入力します。

    * **[ユーザー ID]** : *oid*
    * **[表示名]** : *name*
    * **[名]** : *given_name*
    * **[姓]** : *family_name*
    * **[電子メール]** : *unique_name*

1. **[保存]** を選択します。
