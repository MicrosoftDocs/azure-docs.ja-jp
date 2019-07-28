---
title: Azure Active Directory B2C 内のカスタム ポリシーで OpenId Connect 技術プロファイルを定義する |Microsoft Docs
description: Azure Active Directory B2C 内のカスタム ポリシーで OpenId Connect 技術プロファイルを定義します。
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/10/2018
ms.author: marsma
ms.subservice: b2c
ms.openlocfilehash: 6d16415aa5111388ec2d2a1009ff477574ae42c5
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/13/2019
ms.locfileid: "66512907"
---
# <a name="define-an-openid-connect-technical-profile-in-an-azure-active-directory-b2c-custom-policy"></a>Azure Active Directory B2C カスタム ポリシーで OpenId Connect 技術プロファイルを定義する

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Azure Active Directory (Azure AD) B2C では、[OpenId Connect](https://openid.net/2015/04/17/openid-connect-certification-program/) プロトコルの ID プロバイダーにサポートを提供しています。 OpenID Connect 1.0 は OAuth 2.0 の上に ID レイヤーを定義し、現在の認証プロトコルにおいて最先端のものとなっています。 OpenId Connect 技術プロファイルを使用して、OpenId Connect baseの ID プロバイダー (Azure AD など) とフェデレーションできます。 ID プロバイダーとのフェデレーションにより、ユーザーは、既存のソーシャル ID またはエンタープライズ ID でサインインできます。

## <a name="protocol"></a>Protocol

**Protocol** 要素の **Name** 属性は `OpenIdConnect` に設定する必要があります。 たとえば、**MSA-OIDC** 技術プロファイル用のプロトコルは `OpenIdConnect` です。

```XML
<TechnicalProfile Id="MSA-OIDC">
  <DisplayName>Microsoft Account</DisplayName>
  <Protocol Name="OpenIdConnect" />
  ...    
```

## <a name="input-claims"></a>入力要求

**InputClaims** と **InputClaimsTransformations** の要素は不要です。 ただし、追加のパラメーターを ID プロバイダーに送信する場合があります。 次の例では、値が `contoso.com` である **domain_hint** クエリ文字列パラメーターを認可要求に追加しています。

```XML
<InputClaims>
  <InputClaim ClaimTypeReferenceId="domain_hint" DefaultValue="contoso.com" />
</InputClaims>
```

## <a name="output-claims"></a>出力要求

**OutputClaims** 要素には、OpenId Connect ID プロバイダーにより返される要求の一覧が存在します。 お使いのポリシーに定義されている要求の名前を、ID プロバイダーで定義されている名前にマップする必要があるかもしれません。 `DefaultValue` 属性を設定している限り、ID プロバイダーにより返されない要求を追加することもできます。

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

| Attribute | 必須 | 説明 |
| --------- | -------- | ----------- |
| client_id | はい | ID プロバイダーのアプリケーション識別子。 |
| IdTokenAudience | いいえ | id_token の対象ユーザー。 指定される場合、Azure AD B2C は、トークンが ID プロバイダーにより返された要求内にあり、そして指定されたものと等しいかどうかをチェックします。 |
| METADATA | はい | OpenID Connect Discovery 仕様に従ってフォーマットされた JSON 構成ドキュメントをポイントする URL。既知の openid 構成エンドポイントとも呼ばれます。 |
| ProviderName | いいえ | ID プロバイダーの名前。 |
| response_types | いいえ | OpenID Connect Core 1.0 仕様に準拠した応答の種類。 指定できる値: `id_token`、`code`、または `token`。 |
| response_mode | いいえ | Azure AD B2C に結果を返信するために、ID プロバイダーが使用するメソッド。 指定できる値: `query`、`form_post` (既定)、または `fragment`。 |
| scope | いいえ | OpenID Connect Core 1.0 の仕様に従って定義される、要求の範囲。 たとえば、`openid`、`profile`、`email` などです。 |
| HttpBinding | いいえ | アクセス トークンと要求トークンのエンドポイントに予期される HTTP バインド。 指定できる値: `GET` または `POST`。  |
| ValidTokenIssuerPrefixes | いいえ | Azure Active Directory などのマルチテナント ID プロバイダーを使用するときに、各テナントにサインインするために使用できるキー。 |
| UsePolicyInRedirectUri | いいえ | リダイレクト URI を構築するときにポリシーを使用するかどうかを示します。 ID プロバイダーでアプリケーションを構成するときは、リダイレクト URI を指定する必要があります。 リダイレクト URI は、Azure AD B2C、`https://login.microsoftonline.com/te/{tenant}/oauth2/authresp` をポイントします (login.microsoftonline.com は your-tenant-name.b2clogin.com によって変わることがあります)。  `false` を指定した場合は、使用するポリシーごとにリダイレクト URI を追加する必要があります。 (例: `https://login.microsoftonline.com/te/{tenant}/{policy}/oauth2/authresp`)。 |
| MarkAsFailureOnStatusCode5xx | いいえ | Http 状態コードが 5xx の範囲にある場合、外部サービスへの要求を失敗としてマークする必要があるかどうかを示します。 既定では、 `false`です。 |
| DiscoverMetadataByTokenIssuer | いいえ | JWT トークンで発行者を使用して OIDC メタデータを検出する必要があるかどうかを示します。 |

## <a name="cryptographic-keys"></a>暗号化キー

**CryptographicKeys** 要素には次の属性が存在します。

| Attribute | 必須 | 説明 |
| --------- | -------- | ----------- |
| client_secret | はい | ID プロバイダー アプリケーションのクライアント シークレット。 **response_types** メタデータが `code` に設定されている場合にのみ、暗号化キーが必要です。 この場合、Azure AD B2C は、アクセス トークンの認証コードを交換するために、別の呼び出しを行います。 メタデータが `id_token` に設定されている場合は、暗号化キーを省略できます。  |  

## <a name="redirect-uri"></a>リダイレクト URI
 
ID プロバイダーのリダイレクト URI を構成する場合は、`https://login.microsoftonline.com/te/tenant/oauth2/authresp` を入力します。 **tenant** は、実際のテナントの名前 (例: contosob2c.onmicrosoft.com) またはテナントの ID に必ず置き換えてください。 リダイレクト URI は、すべて小文字である必要があります。

**login.microsoftonline.com** の代わりに **b2clogin.com** ドメインを使用している場合は、login.microsoftonline.com の代わりに b2clogin.com を使用することを確認します。

次に例を示します。

- [カスタム ポリシーを使って Microsoft アカウント (MSA) を ID プロバイダーとして追加する](active-directory-b2c-custom-setup-msa-idp.md)
- [Azure AD アカウントを使用してサインインする](active-directory-b2c-setup-aad-custom.md)
- [カスタム ポリシーを使用して、ユーザーがマルチテナント Azure AD ID プロバイダーにサインインできるようにする](active-directory-b2c-setup-commonaad-custom.md)

 














