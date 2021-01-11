---
title: カスタム ポリシーで OpenID Connect の技術プロファイルを定義する
titleSuffix: Azure AD B2C
description: Azure Active Directory B2C 内のカスタム ポリシーで OpenID Connect 技術プロファイルを定義します。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 03/26/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 459e2184f5236278377bd9e655e2b1ed154cc6a3
ms.sourcegitcommit: 3246e278d094f0ae435c2393ebf278914ec7b97b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/02/2020
ms.locfileid: "89377218"
---
# <a name="define-an-openid-connect-technical-profile-in-an-azure-active-directory-b2c-custom-policy"></a>Azure Active Directory B2C カスタム ポリシーで OpenID Connect 技術プロファイルを定義する

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Azure Active Directory B2C (Azure AD B2C) では、[OpenID Connect](https://openid.net/2015/04/17/openid-connect-certification-program/) プロトコルの ID プロバイダーのサポートを提供しています。 OpenID Connect 1.0 は OAuth 2.0 の上に ID レイヤーを定義し、現在の認証プロトコルにおいて最先端のものとなっています。 OpenID Connect 技術プロファイルを使用して、OpenID Connect base の ID プロバイダー (Azure AD など) とフェデレーションできます。 ID プロバイダーとのフェデレーションにより、ユーザーは、既存のソーシャル ID またはエンタープライズ ID でサインインできます。

## <a name="protocol"></a>Protocol

**Protocol** 要素の **Name** 属性は `OpenIdConnect` に設定する必要があります。 たとえば、**MSA-OIDC** 技術プロファイル用のプロトコルは `OpenIdConnect` です。

```xml
<TechnicalProfile Id="MSA-OIDC">
  <DisplayName>Microsoft Account</DisplayName>
  <Protocol Name="OpenIdConnect" />
  ...
```

## <a name="input-claims"></a>入力クレーム

**InputClaims** と **InputClaimsTransformations** の要素は不要です。 ただし、追加のパラメーターを ID プロバイダーに送信する場合があります。 次の例では、値が `contoso.com` である **domain_hint** クエリ文字列パラメーターを認可要求に追加しています。

```xml
<InputClaims>
  <InputClaim ClaimTypeReferenceId="domain_hint" DefaultValue="contoso.com" />
</InputClaims>
```

## <a name="output-claims"></a>出力クレーム

**OutputClaims** 要素には、OpenID Connect ID プロバイダーにより返される要求の一覧が存在します。 お使いのポリシーに定義されている要求の名前を、ID プロバイダーで定義されている名前にマップする必要があるかもしれません。 `DefaultValue` 属性を設定している限り、ID プロバイダーにより返されない要求を追加することもできます。

**OutputClaimsTransformations** 要素には、出力要求を修正したり新しい要求を生成するために使用される、**OutputClaimsTransformation** 要素のコレクションが含まれている場合があります。

次の例は、Microsoft アカウント プロバイダーにより返される要求を示しています。

- **issuerUserId** 要求にマップされている **sub** 要求。
- **displayName** 要求にマップされている **name** 要求。
- どの名前にもマップされていない **email**。

また、技術プロファイルは、ID プロバイダーにより返されない要求も返します。

- ID プロバイダーの名前を保持する **identityProvider** 要求。
- 既定値の **socialIdpAuthentication** である **authenticationSource** 要求。

```xml
<OutputClaims>
  <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="live.com" />
  <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="socialIdpAuthentication" />
  <OutputClaim ClaimTypeReferenceId="issuerUserId" PartnerClaimType="sub" />
  <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="name" />
  <OutputClaim ClaimTypeReferenceId="email" />
</OutputClaims>
```

## <a name="metadata"></a>Metadata

| 属性 | Required | 説明 |
| --------- | -------- | ----------- |
| client_id | はい | ID プロバイダーのアプリケーション識別子。 |
| IdTokenAudience | いいえ | id_token の対象ユーザー。 指定される場合、Azure AD B2C により、ID プロバイダーによって返されたトークンの `aud` クレームが IdTokenAudience メタデータで指定されているものと等しいかどうかを確認します。  |
| METADATA | はい | OpenID の既知の構成エンドポイントとも呼ばれる OpenID Connect ID プロバイダー構成ドキュメントを指す URL。 URL には、テナント名に置き換えられる `{tenant}` 式を含めることができます。  |
| authorization_endpoint | いいえ | OpenID Connect ID プロバイダー構成の承認エンドポイントを指す URL。 authorization_endpoint メタデータの値は、OpenID の既知の構成エンドポイントで指定された `authorization_endpoint` よりも優先されます。 URL には、テナント名に置き換えられる `{tenant}` 式を含めることができます。 |
| 発行者 | いいえ | OpenID Connect ID プロバイダーの一意識別子。 issuer メタデータの値は、OpenID の既知の構成エンドポイントで指定された `issuer` よりも優先されます。  指定される場合、Azure AD B2C により、ID プロバイダーによって返されたトークンの `iss` クレームが issuer メタデータで指定されているものと等しいかどうかを確認します。 |
| ProviderName | いいえ | ID プロバイダーの名前。  |
| response_types | いいえ | OpenID Connect Core 1.0 仕様に準拠した応答の種類。 指定できる値: `id_token`、`code`、または `token`。 |
| response_mode | いいえ | Azure AD B2C に結果を返信するために、ID プロバイダーが使用するメソッド。 指定できる値: `query`、`form_post` (既定)、または `fragment`。 |
| scope | いいえ | OpenID Connect Core 1.0 の仕様に従って定義される、要求の範囲。 たとえば、`openid`、`profile`、`email` などです。 |
| HttpBinding | いいえ | アクセス トークンと要求トークンのエンドポイントに予期される HTTP バインド。 指定できる値: `GET` または `POST`。  |
| ValidTokenIssuerPrefixes | いいえ | Azure Active Directory などのマルチテナント ID プロバイダーを使用するときに、各テナントにサインインするために使用できるキー。 |
| UsePolicyInRedirectUri | いいえ | リダイレクト URI を構築するときにポリシーを使用するかどうかを示します。 ID プロバイダーでアプリケーションを構成するときは、リダイレクト URI を指定する必要があります。 リダイレクト URI は Azure AD B2C を指します (`https://{your-tenant-name}.b2clogin.com/{your-tenant-name}.onmicrosoft.com/oauth2/authresp`)。  `false` を指定した場合は、使用するポリシーごとにリダイレクト URI を追加する必要があります。 (例: `https://{your-tenant-name}.b2clogin.com/{your-tenant-name}.onmicrosoft.com/{policy-name}/oauth2/authresp`)。 |
| MarkAsFailureOnStatusCode5xx | いいえ | Http 状態コードが 5xx の範囲にある場合、外部サービスへの要求を失敗としてマークする必要があるかどうかを示します。 既定では、 `false`です。 |
| DiscoverMetadataByTokenIssuer | いいえ | JWT トークンで発行者を使用して OIDC メタデータを検出する必要があるかどうかを示します。 |
| IncludeClaimResolvingInClaimsHandling  | いいえ | 入力と出力の要求について、[要求の解決](claim-resolver-overview.md)を技術プロファイルに含めるかどうかを指定します。 指定できる値: `true` または `false` (既定値)。 技術プロファイルで要求リゾルバーを使用する場合は、これを `true` に設定します。 |

```xml
<Metadata>
  <Item Key="ProviderName">https://login.live.com</Item>
  <Item Key="METADATA">https://login.live.com/.well-known/openid-configuration</Item>
  <Item Key="response_types">code</Item>
  <Item Key="response_mode">form_post</Item>
  <Item Key="scope">openid profile email</Item>
  <Item Key="HttpBinding">POST</Item>
  <Item Key="UsePolicyInRedirectUri">0</Item>
  <Item Key="client_id">Your Microsoft application client ID</Item>
</Metadata>
```

### <a name="ui-elements"></a>UI 要素
 
次の設定を使用して、失敗したときに表示されるエラー メッセージを構成できます。 メタデータは、OpenID Connect 技術プロファイルで構成する必要があります。 エラー メッセージは、[ローカライズ](localization-string-ids.md#sign-up-or-sign-in-error-messages)できます。

| 属性 | Required | 説明 |
| --------- | -------- | ----------- |
| UserMessageIfClaimsPrincipalDoesNotExist | いいえ | 指定されたユーザー名のアカウントがディレクトリに見つからなかった場合に、ユーザーに表示するメッセージ。 |
| UserMessageIfInvalidPassword | いいえ | パスワードが正しくない場合にユーザーに表示するメッセージ。 |
| UserMessageIfOldPasswordUsed| いいえ |  古いパスワードが使用されている場合にユーザーに表示するメッセージ。|

## <a name="cryptographic-keys"></a>暗号化キー

**CryptographicKeys** 要素には次の属性が存在します。

| 属性 | Required | 説明 |
| --------- | -------- | ----------- |
| client_secret | はい | ID プロバイダー アプリケーションのクライアント シークレット。 **response_types** メタデータが `code` に設定されている場合にのみ、暗号化キーが必要です。 この場合、Azure AD B2C は、アクセス トークンの認証コードを交換するために、別の呼び出しを行います。 メタデータが `id_token` に設定されている場合は、暗号化キーを省略できます。  |

## <a name="redirect-uri"></a>リダイレクト URI

ID プロバイダーのリダイレクト URI を構成する場合は、`https://{your-tenant-name}.b2clogin.com/{your-tenant-name}.onmicrosoft.com/oauth2/authresp` を入力します。 `{your-tenant-name}` をテナントの名前に置き換えます。 リダイレクト URI は、すべて小文字である必要があります。

例 :

- [カスタム ポリシーを使って Microsoft アカウント (MSA) を ID プロバイダーとして追加する](identity-provider-microsoft-account-custom.md)
- [Azure AD アカウントを使用してサインインする](identity-provider-azure-ad-single-tenant-custom.md)
- [カスタム ポリシーを使用して、ユーザーがマルチテナント Azure AD ID プロバイダーにサインインできるようにする](identity-provider-azure-ad-multi-tenant-custom.md)
