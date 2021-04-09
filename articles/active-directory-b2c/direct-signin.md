---
title: Azure Active Directory B2C を使用した直接サインインの設定 | Microsoft Docs
description: サインイン名を事前入力するか、ソーシャル ID プロバイダーに直接リダイレクトする方法について説明します。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 12/14/2020
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: 35e8efa269ab72477b06e86824d368d0a3dced03
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "103197322"
---
# <a name="set-up-direct-sign-in-using-azure-active-directory-b2c"></a>Azure Active Directory B2C を使用した直接サインインの設定

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

Azure Active Directory (AD) B2C を使用してアプリケーションのサインインを設定する場合、サインイン名を事前入力することも、Facebook、LinkedIn、Microsoft アカウントなどの特定のソーシャル ID プロバイダーに直接サインインすることもできます。

## <a name="prepopulate-the-sign-in-name"></a>サインイン名を事前入力する

サインインのユーザー体験中に、証明書利用者アプリケーションが特定のユーザーまたはドメイン名をターゲットとする可能性があります。 ユーザーをターゲット設定する場合、アプリケーションでは、承認要求で、ユーザー サインイン名と共に `login_hint` クエリ パラメーターを指定できます。 Azure AD B2C によってサインイン名が自動的に入力され、ユーザーはパスワードを指定する必要のみあります。

![URL 内の login_hint クエリ パラメーターが強調表示されているサインアップ サインイン ページ](./media/direct-signin/login-hint.png)

ユーザーはサインイン テキストボックスの値を変更できます。

::: zone pivot="b2c-custom-policy"

ログイン ヒント パラメーターをサポートするには、`SelfAsserted-LocalAccountSignin-Email` 技術プロファイルをオーバーライドします。 `<InputClaims>` セクションで、signInName 要求の DefaultValue を `{OIDC:LoginHint}` に設定します。 `{OIDC:LoginHint}` 変数には、`login_hint` パラメーターの値が含まれています。 Azure AD B2C は、signInName 要求の値を読み取って、signInName テキストボックスに事前入力します。

```xml
<ClaimsProvider>
  <DisplayName>Local Account</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="SelfAsserted-LocalAccountSignin-Email">
      <InputClaims>
        <!-- Add the login hint value to the sign-in names claim type -->
        <InputClaim ClaimTypeReferenceId="signInName" DefaultValue="{OIDC:LoginHint}" />
      </InputClaims>
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

::: zone-end

## <a name="redirect-sign-in-to-a-social-provider"></a>サインインをソーシャル プロバイダーにリダイレクトする

Facebook、LinkedIn、Google などのソーシャル アカウントを含むようにアプリケーションのサインイン プロセスを構成した場合は、`domain_hint` パラメーターを指定できます。 このクエリ パラメーターは、サインインに使用する必要があるソーシャル ID プロバイダーに関するヒントを Azure AD B2C に提供します。 たとえば、アプリケーションで `domain_hint=facebook.com` を指定した場合、サインインは Facebook のサインイン ページに直接移動します。

![URL 内の domain_hint クエリ パラメーターが強調表示されているサインアップ サインイン ページ](./media/direct-signin/domain-hint.png)

::: zone pivot="b2c-user-flow"

ドメイン ヒント クエリ文字列パラメーターは、次のドメインのいずれかに設定できます。

- amazon.com
- facebook.com
- github.com
- google.com
- linkedin.com
- microsoft.com
- qq.com
- twitter.com
- wechat.com
- weibo.com 
- [汎用 OpenID 接続](identity-provider-generic-openid-connect.md)については、「[ドメインのヒント](identity-provider-generic-openid-connect.md#response-mode)」をご覧ください。

::: zone-end

::: zone pivot="b2c-custom-policy"

ドメイン ヒント パラメーターをサポートするには、任意の `<ClaimsProvider>` の `<Domain>domain name</Domain>` XML 要素を使用してドメイン名を構成できます。

```xml
<ClaimsProvider>
    <!-- Add the domain hint value to the claims provider -->
    <Domain>facebook.com</Domain>
    <DisplayName>Facebook</DisplayName>
    <TechnicalProfiles>
    ...
```

::: zone-end

