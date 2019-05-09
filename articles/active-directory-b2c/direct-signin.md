---
title: Azure Active Directory B2C を使用した直接サインインの設定 | Microsoft Docs
description: サインイン名を事前入力するか、ソーシャル ID プロバイダーに直接リダイレクトする方法について説明します。
services: active-directory-b2c
author: davidmu1
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 06/18/2018
ms.author: davidmu
ms.subservice: B2C
ms.openlocfilehash: c66fc9ecb608d211c839bab720e8f3f37b96fd5d
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/29/2019
ms.locfileid: "55169231"
---
# <a name="set-up-direct-sign-in-using-azure-active-directory-b2c"></a>Azure Active Directory B2C を使用した直接サインインの設定

Azure Active Directory (AD) B2C を使用してアプリケーションのサインインを設定する場合、サインイン名を事前入力することも、Facebook、LinkedIn、Microsoft アカウントなどの特定のソーシャル ID プロバイダーに直接サインインすることもできます。 

## <a name="prepopulate-the-sign-in-name"></a>サインイン名を事前入力する

サインインのユーザー体験中に、証明書利用者アプリケーションが特定のユーザーまたはドメイン名をターゲットとする可能性があります。 ユーザーをターゲット設定する場合、アプリケーションでは、承認要求で、ユーザー サインイン名と共に `login_hint` クエリ パラメーターを指定できます。 Azure AD B2C によってサインイン名が自動的に入力され、ユーザーはパスワードを指定する必要のみあります。

![ログイン ヒントの使用](./media/direct-signin/login-hint.png) 

ユーザーはサインイン テキストボックスの値を変更できます。

カスタム ポリシーを使用している場合は、`SelfAsserted-LocalAccountSignin-Email` 技術プロファイルをオーバーライドします。 `<InputClaims>` セクションで、signInName 要求の DefaultValue を `{OIDC:LoginHint}` に設定します。 `{OIDC:LoginHint}` 変数には、`login_hint` パラメーターの値が含まれています。 Azure AD B2C は、signInName 要求の値を読み取って、signInName テキストボックスに事前入力します。

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

## <a name="redirect-sign-in-to-a-social-provider"></a>サインインをソーシャル プロバイダーにリダイレクトする

Facebook、LinkedIn、Google などのソーシャル アカウントを含むようにアプリケーションのサインイン プロセスを構成した場合は、`domain_hint` パラメーターを指定できます。 このクエリ パラメーターは、サインインに使用する必要があるソーシャル ID プロバイダーに関するヒントを Azure AD B2C に提供します。 たとえば、アプリケーションで `domain_hint=facebook.com` を指定した場合、サインインは Facebook のサインイン ページに直接移動します。

![ドメインのヒントの使用](./media/direct-signin/domain-hint.png) 

カスタム ポリシーを使用している場合は、任意の `<ClaimsProvider>` の `<Domain>domain name</Domain>` XML 要素を使用してドメイン名を構成できます。 

```xml
<ClaimsProvider>
    <!-- Add the domain hint value to the claims provider -->
    <Domain>facebook.com</Domain>
    <DisplayName>Facebook</DisplayName>
    <TechnicalProfiles>
    ...
```


