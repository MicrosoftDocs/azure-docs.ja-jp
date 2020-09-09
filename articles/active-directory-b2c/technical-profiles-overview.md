---
title: カスタム ポリシーでの技術プロファイルの概要
titleSuffix: Azure AD B2C
description: Azure Active Directory B2C 内のカスタム ポリシーで技術プロファイルがどのように使用されるかについて学習します。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 03/20/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 16fdc38d6235ddd0f72c7a35a3d71973ce01a4be
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/02/2020
ms.locfileid: "85203216"
---
# <a name="about-technical-profiles-in-azure-active-directory-b2c-custom-policies"></a>Azure Active Directory B2C カスタム ポリシーでの技術プロファイルについて

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

技術プロファイルでは、Azure Active Directory B2C (Azure AD B2C) でカスタム ポリシーを使用して、さまざまな種類の利用者と通信するためのメカニズムが組み込まれたフレームワークを提供します。 技術プロファイルは、ユーザーの作成やユーザー プロファイルの読み取りを行うために、ご利用の Azure AD B2C テナントとの通信に使用されます。 ユーザーとの相互作用を有効にするには、技術プロファイルをセルフアサートすることができます。 たとえば、ユーザーの資格情報を収集してサインインし、サインアップ ページまたはパスワードのリセット ページをレンダリングします。

## <a name="type-of-technical-profiles"></a>技術プロファイルの種類

技術プロファイルでは、これらの種類のシナリオを有効にします。

- [Application Insights](application-insights-technical-profile.md) - イベント データを [Application Insights](../azure-monitor/app/app-insights-overview.md)に送信します。
- [Azure Active Directory](active-directory-technical-profile.md) - Azure Active Directory B2C ユーザー管理をサポートします。
- [Azure Multi-Factor Authentication](multi-factor-auth-technical-profile.md) - Azure Multi-Factor Authentication (MFA) を使用して電話番号を確認するためのサポートを提供します。 
- [要求変換](claims-transformation-technical-profile.md) - 出力要求変換を呼び出して、要求の値を操作したり、要求を検証したり、一連の出力要求の既定値を設定したりすることができます。
- [JWT トークン発行者](jwt-issuer-technical-profile.md) - 証明書利用者アプリケーションに戻された JWT トークンを発行します。
- [OAuth1](oauth1-technical-profile.md) - 任意の OAuth 1.0 プロトコル ID プロバイダーとのフェデレーション。
- [OAuth2](oauth2-technical-profile.md) - 任意の OAuth 2.0 プロトコル ID プロバイダーとのフェデレーション。
- [ワンタイム パスワード](one-time-password-technical-profile.md) - ワンタイム パスワードの生成と確認を管理するためのサポートが提供されています。
- [OpenID Connect](openid-connect-technical-profile.md) - 任意の OpenID Connect プロトコル ID プロバイダーとのフェデレーション。
- [電話要素](phone-factor-technical-profile.md) - 電話番号の登録と確認をサポートします。
- [RESTful プロバイダー](restful-technical-profile.md) - ユーザーの入力の検証、ユーザー データの促進、基幹業務アプリケーションとの統合など、REST API サービスを呼び出します。
- [SAML ID プロバイダー](saml-identity-provider-technical-profile.md) - 任意の SAML プロトコル ID プロバイダーとのフェデレーション。
- [SAML トークン発行者](saml-issuer-technical-profile.md) - 証明書利用者アプリケーションに戻された SAML トークンを発行します。
- [セルフアサート](self-asserted-technical-profile.md) - ユーザーとやりとりします。 たとえば、ユーザーの資格情報を収集してサインインし、サインアップ ページまたはパスワードのリセットをレンダリングします。
- [セッション管理](custom-policy-reference-sso.md) - さまざまな種類のセッションを処理します。

## <a name="technical-profile-flow"></a>技術プロファイルのフロー

技術プロファイルのすべての種類で同じ概念を共有します。 入力要求を送信し、要求変換を実行し、ID プロバイダー、REST API、Azure AD ディレクトリ サービスなど、構成されたパーティと通信します。 プロセスが完了すると、技術プロファイルによって出力要求が戻され、出力要求変換が実行される場合があります。 次の図は、技術プロファイルで参照される変換とマッピングがどのように処理されるかを示しています。 技術プロファイルでやりとりするパーティに関係なく、すべての要求変換が実行された後、技術プロファイルからの出力要求が要求バッグにすぐに格納されます。

![技術プロファイルのフローを示している図](./media/technical-profiles-overview/technical-profile-idp-saml-flow.png)

1. **シングル サインオン (SSO) セッション管理** - [SSO セッション管理](custom-policy-reference-sso.md)を使用して、技術プロファイルのセッション状態を復元します。
1. **入力要求変換** - 要求バッグからピックアップされるすべての入力[要求変換](claimstransformations.md)の入力要求。  入力要求変換の出力要求は、後続の入力要求変換の入力要求になる場合があります。
1. **入力要求** - 要求は要求バッグからピックアップされ、技術プロファイルに使用されます。 たとえば、[セルフアサート技術プロファイル](self-asserted-technical-profile.md)では、入力要求を使用して、ユーザーが提供する出力要求を事前作成します。 REST API 技術プロファイルでは、入力要求を使用し、入力パラメーターを REST API エンドポイントに送信します。 Azure Active Directory では、アカウントの読み取り、更新、削除を行うために、一意識別子として入力要求を使用します。
1. **技術プロファイルの実行** - 技術プロファイルでは、その要求を構成されたパーティと交換します。 次に例を示します。
    - ユーザーを ID プロバイダーにリダイレクトして、サインインを完了します。 サインインが成功したら、ユーザーが返され、技術プロファイルの実行が続行されます。
    - InputClaims としてパラメーターを送信し、OutputClaims として情報を戻しながら、REST API を呼び出します。
    - ユーザー アカウントを作成または更新します。
    - MFA テキスト メッセージを送信して検証します。
1. **検証技術プロファイル** - [セルフアサート技術プロファイル](self-asserted-technical-profile.md)からは、[検証技術プロファイル](validation-technical-profile.md)を呼び出すことができます。 検証技術プロファイルでは、ユーザーによってプロファイルされたデータを検証し、出力要求を含めて、または含めないで、エラー メッセージや OK を返します。 たとえば、Azure AD B2C では、新しいアカウントを作成する前に、ユーザーがディレクトリ サービスに既に存在するかどうかを確認します。 REST API 技術プロファイルを呼び出して、独自のビジネス ロジックを追加できます。<p>検証技術プロファイルの出力要求のスコープは、検証技術プロファイルを呼び出す技術プロファイルに限定されます。 また、同じ技術プロファイル以下に他の検証技術プロファイルもあります。 次のオーケストレーションの手順で出力要求を使用する場合は、検証技術プロファイルを呼び出す技術プロファイルに出力要求を追加する必要があります。
1. **出力要求** - 要求は要求バッグに戻されます。 次のオーケストレーションの手順、または出力要求変換でこれらの要求を使用できます。
1. **出力要求変換** - すべての出力[要求変換](claimstransformations.md)の入力要求は、要求バッグからピックアップされます。 前の手順からの技術プロファイルの出力要求は、出力要求変換の入力要求になる場合があります。 実行後、出力要求は要求バッグに戻されます。 出力要求変換の出力要求は、後続の出力要求変換の入力要求になる場合があります。
1. **シングル サインオン (SSO) セッション管理** - [SSO セッション管理](custom-policy-reference-sso.md)を使用して、技術プロファイルのデータをセッションに永続化します。


## <a name="technical-profile-inclusion"></a>技術プロファイルを含める

技術プロファイルには、設定を変更したり、新しい機能を追加したりするために、別の技術プロファイルを含めることができます。  `IncludeTechnicalProfile` 要素は、技術プロファイルの派生元となる基本技術プロファイルへの参照です。 レベルの数に制限はありません。

たとえば、**AAD-UserReadUsingAlternativeSecurityId-NoError** 技術プロファイルには、**AAD-UserReadUsingAlternativeSecurityId** が含まれます。 この技術プロファイルを使うと、`RaiseErrorIfClaimsPrincipalDoesNotExist` メタデータ項目を `true` に設定し、ディレクトリにソーシャル アカウントが存在しない場合はエラーを発生させることができます。 **AAD-UserReadUsingAlternativeSecurityId-NoError** によってこの動作がオーバーライドされ、そのエラー メッセージは無効になります。

```xml
<TechnicalProfile Id="AAD-UserReadUsingAlternativeSecurityId-NoError">
  <Metadata>
    <Item Key="RaiseErrorIfClaimsPrincipalDoesNotExist">false</Item>
  </Metadata>
  <IncludeTechnicalProfile ReferenceId="AAD-UserReadUsingAlternativeSecurityId" />
</TechnicalProfile>
```

**AAD-UserReadUsingAlternativeSecurityId** には、`AAD-Common` 技術プロファイルが含まれます。

```xml
<TechnicalProfile Id="AAD-UserReadUsingAlternativeSecurityId">
  <Metadata>
    <Item Key="Operation">Read</Item>
    <Item Key="RaiseErrorIfClaimsPrincipalDoesNotExist">true</Item>
    <Item Key="UserMessageIfClaimsPrincipalDoesNotExist">User does not exist. Please sign up before you can sign in.</Item>
  </Metadata>
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="AlternativeSecurityId" PartnerClaimType="alternativeSecurityId" Required="true" />
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="objectId" />
    <OutputClaim ClaimTypeReferenceId="userPrincipalName" />
    <OutputClaim ClaimTypeReferenceId="displayName" />
    <OutputClaim ClaimTypeReferenceId="otherMails" />
    <OutputClaim ClaimTypeReferenceId="givenName" />
    <OutputClaim ClaimTypeReferenceId="surname" />
  </OutputClaims>
  <IncludeTechnicalProfile ReferenceId="AAD-Common" />
</TechnicalProfile>
```

**AAD-UserReadUsingAlternativeSecurityId-NoError** と **AAD-UserReadUsingAlternativeSecurityId** はどちらも、**AAD-Common** 技術プロファイルで指定されているため、必須の **Protocol** 要素は指定されません。

```xml
<TechnicalProfile Id="AAD-Common">
  <DisplayName>Azure Active Directory</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.AzureActiveDirectoryProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  ...
</TechnicalProfile>
```
