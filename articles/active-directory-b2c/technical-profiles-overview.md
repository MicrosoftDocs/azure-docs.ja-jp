---
title: Azure Active Directory B2C カスタム ポリシーでの技術プロファイルについて | Microsoft Docs
description: Azure Active Directory B2C 内のカスタム ポリシーで技術プロファイルがどのように使用されるかについて説明します。
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/10/2018
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: cdb1b49af5d3a85dc1853e5964c1cdfdd5c3a7cd
ms.sourcegitcommit: a52f17307cc36640426dac20b92136a163c799d0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/01/2019
ms.locfileid: "68716666"
---
# <a name="about-technical-profiles-in-azure-active-directory-b2c-custom-policies"></a>Azure Active Directory B2C カスタム ポリシーでの技術プロファイルについて

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

技術プロファイルでは、Azure Active Directory (Azure AD) B2C でカスタム ポリシーを使用して、さまざまな種類の利用者と通信するためのメカニズムが組み込まれたフレームワークを提供します。 技術プロファイルは、ユーザーの作成やユーザー プロファイルの読み取りを行うために、ご利用の Azure AD B2C テナントとの通信に使用されます。 ユーザーとの相互作用を有効にするには、技術プロファイルをセルフアサートすることができます。 たとえば、ユーザーの資格情報を収集してサインインし、サインアップ ページまたはパスワードのリセット ページをレンダリングします。

## <a name="type-of-technical-profiles"></a>技術プロファイルの種類

技術プロファイルでは、これらの種類のシナリオを有効にします。

- [Azure Active Directory](active-directory-technical-profile.md) - Azure Active Directory B2C ユーザー管理をサポートします。
- [JWT トークン発行者](jwt-issuer-technical-profile.md) - 証明書利用者アプリケーションに戻された JWT トークンを発行します。
- **電話ファクター プロバイダー** - 多要素認証。
- [OAuth1](oauth1-technical-profile.md) - 任意の OAuth 1.0 プロトコル ID プロバイダーとのフェデレーション。
- [OAuth2](oauth2-technical-profile.md) - 任意の OAuth 2.0 プロトコル ID プロバイダーとのフェデレーション。
- [OpenID Connect](openid-connect-technical-profile.md) - 任意の OpenID Connect プロトコル ID プロバイダーとのフェデレーション。
- [要求変換](claims-transformation-technical-profile.md) - 出力要求変換を呼び出して、要求の値を操作したり、要求を検証したり、一連の出力要求の既定値を設定したりすることができます。
- [RESTful プロバイダー](restful-technical-profile.md) - ユーザーの入力の検証、ユーザー データの促進、基幹業務アプリケーションとの統合など、REST API サービスを呼び出します。
- [SAML2](saml-technical-profile.md) - 任意の SAML プロトコル ID プロバイダーとのフェデレーション。
- [セルフアサート](self-asserted-technical-profile.md) - ユーザーとやりとりします。 たとえば、ユーザーの資格情報を収集してサインインし、サインアップ ページまたはパスワードのリセットをレンダリングします。
- **WsFed** - 任意の WsFed プロトコル ID プロバイダーとのフェデレーション。
- [セッション管理](active-directory-b2c-reference-sso-custom.md) - さまざまな種類のセッションを処理します。
- **Application Insights**

## <a name="technical-profile-flow"></a>技術プロファイルのフロー

技術プロファイルのすべての種類で同じ概念を共有します。 入力要求を送信し、要求変換を実行し、ID プロバイダー、REST API、Azure AD ディレクトリ サービスなど、構成されたパーティと通信します。 プロセスが完了すると、技術プロファイルによって出力要求が戻され、出力要求変換が実行される場合があります。 次の図は、技術プロファイルで参照される変換とマッピングがどのように処理されるかを示しています。 技術プロファイルでやりとりするパーティに関係なく、すべての要求変換が実行された後、技術プロファイルからの出力要求が要求バッグにすぐに格納されます。

![技術プロファイルのフローを示している図](./media/technical-profiles-overview/technical-profile-idp-saml-flow.png)
 
1. **InputClaimsTransformation** - すべての入力[要求変換](claimstransformations.md)の入力要求が要求バッグからピックアップされます。実行後、出力要求は要求バッグに戻されます。 入力要求変換の出力要求は、後続の入力要求変換の入力要求になる場合があります。
2. **InputClaims** - 要求は要求バッグからピックアップされ、技術プロファイルに使用されます。 たとえば、[セルフアサート技術プロファイル](self-asserted-technical-profile.md)では、入力要求を使用して、ユーザーが提供する出力要求を事前作成します。 REST API 技術プロファイルでは、入力要求を使用し、入力パラメーターを REST API エンドポイントに送信します。 Azure Active Directory では、アカウントの読み取り、更新、削除を行うために、一意識別子として入力要求を使用します。
3. **技術プロファイルの実行** - 技術プロファイルでは、その要求を構成されたパーティと交換します。 例:
    - ユーザーを ID プロバイダーにリダイレクトして、サインインを完了します。 サインインが成功したら、ユーザーが返され、技術プロファイルの実行が続行されます。
    - InputClaims としてパラメーターを送信し、OutputClaims として情報を戻しながら、REST API を呼び出します。
    - ユーザー アカウントを作成または更新します。
    - MFA テキスト メッセージを送信して検証します。
4. **ValidationTechnicalProfiles** - [セルフ アサート技術プロファイル](self-asserted-technical-profile.md)では、入力の[検証技術プロファイル](validation-technical-profile.md)を呼び出すことができます。 検証技術プロファイルでは、ユーザーによってプロファイルされたデータを検証し、出力要求を含めて、または含めないで、エラー メッセージや OK を返します。 たとえば、Azure AD B2C では、新しいアカウントを作成する前に、ユーザーがディレクトリ サービスに既に存在するかどうかを確認します。 REST API 技術プロファイルを呼び出して、独自のビジネス ロジックを追加できます。<p>検証技術プロファイルの出力要求の範囲は、検証技術プロファイルや同じ技術プロファイルにあるその他の技術プロファイルを呼び出す技術プロファイルに限定されます。 次のオーケストレーションの手順で出力要求を使用する場合は、検証技術プロファイルを呼び出す技術プロファイルに出力要求を追加する必要があります。
5. **OutputClaims** - 要求は要求バッグに戻されます。 次のオーケストレーションの手順、または出力要求変換でこれらの要求を使用できます。
6. **OutputClaimsTransformations** - すべての出力[要求変換](claimstransformations.md)の入力要求は、要求バッグからピックアップされます。 前の手順からの技術プロファイルの出力要求は、出力要求変換の入力要求になる場合があります。 実行後、出力要求は要求バッグに戻されます。 出力要求変換の出力要求は、後続の出力要求変換の入力要求になる場合があります。
7. **シングル サインオン (SSO) セッションの管理** - [SSO セッションの管理](active-directory-b2c-reference-sso-custom.md)では、ユーザーが認証された後に、ユーザーとの相互作用を制御します。 たとえば、管理者は、選択した ID プロバイダーを表示するかどうか、ローカル アカウントの詳細をもう一度入力する必要があるかどうかを制御できます。

技術プロファイルは、設定を変更したり、新しい機能を追加したりするために、別の技術プロファイルから継承できます。  **IncludeTechnicalProfile** 要素は、技術プロファイルの派生元の基本技術プロファイルへの参照です。

たとえば、**AAD-UserReadUsingAlternativeSecurityId-NoError** 技術プロファイルには、**AAD-UserReadUsingAlternativeSecurityId** が含まれます。 この技術プロファイルでは、**RaiseErrorIfClaimsPrincipalDoesNotExist** メタデータ項目が `true` に設定され、ソーシャル アカウントがディレクトリに存在しない場合にエラーが発生します。 **AAD-UserReadUsingAlternativeSecurityId-NoError** では、この動作がオーバーライドされ、ユーザーが存在しない場合のエラー メッセージが無効になります。

```XML
<TechnicalProfile Id="AAD-UserReadUsingAlternativeSecurityId-NoError">
  <Metadata>
    <Item Key="RaiseErrorIfClaimsPrincipalDoesNotExist">false</Item>
  </Metadata>
  <IncludeTechnicalProfile ReferenceId="AAD-UserReadUsingAlternativeSecurityId" />
</TechnicalProfile>
```

**AAD-UserReadUsingAlternativeSecurityId** には、`AAD-Common` 技術プロファイルが含まれます。

```XML
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

```XML
<TechnicalProfile Id="AAD-Common">
  <DisplayName>Azure Active Directory</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.AzureActiveDirectoryProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  ...
</TechnicalProfile>
```

技術プロファイルには、別の技術プロファイルを含めたり、継承したりすることができます。別の技術プロファイルにはまた別の技術プロファイルを含めることができます。 レベルの数に制限はありません。 ユーザー体験では、ビジネス要件に応じて、ユーザー ソーシャル アカウントが存在しない場合にエラーを発生させる **AAD-UserReadUsingAlternativeSecurityId**、またはエラーを発生させない **AAD-UserReadUsingAlternativeSecurityId-NoError** を呼び出すことができます。











