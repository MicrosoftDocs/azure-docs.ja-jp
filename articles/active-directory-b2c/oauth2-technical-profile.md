---
title: カスタム ポリシーで OAuth2 技術プロファイルを定義する
titleSuffix: Azure AD B2C
description: Azure Active Directory B2C 内のカスタム ポリシーで OAuth2 技術プロファイルを定義します。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 12/11/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: f79360269c19f6770fa12120ec34497b29015e7e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "99050687"
---
# <a name="define-an-oauth2-technical-profile-in-an-azure-active-directory-b2c-custom-policy"></a>Azure Active Directory B2C カスタム ポリシーで OAuth2 技術プロファイルを定義する

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Azure Active Directory B2C (Azure AD B2C) では、OAuth2 プロトコルの ID プロバイダーのサポートを提供しています。 OAuth2 は、認可および委任された認証のための主要なプロトコルです。 詳細については、「[RFC 6749 The OAuth 2.0 Authorization Framework](https://tools.ietf.org/html/rfc6749)」を参照してください。 OAuth2 技術プロファイルを使用して、Facebook などの OAuth2 ベースの ID プロバイダーとフェデレーションできます。 ID プロバイダーとのフェデレーションにより、ユーザーは、既存のソーシャル ID またはエンタープライズ ID でサインインできます。

## <a name="protocol"></a>Protocol

**Protocol** 要素の **Name** 属性は `OAuth2` に設定する必要があります。 たとえば、**Facebook-OAUTH** 技術プロファイル用のプロトコルは `OAuth2` です。

```xml
<TechnicalProfile Id="Facebook-OAUTH">
  <DisplayName>Facebook</DisplayName>
  <Protocol Name="OAuth2" />
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

**OutputClaims** 要素には、OAuth2 ID プロバイダーにより返される要求の一覧が存在します。 お使いのポリシーに定義されている要求の名前を、ID プロバイダーで定義されている名前にマップする必要があるかもしれません。 `DefaultValue` 属性を設定している限り、ID プロバイダーにより返されない要求を追加することもできます。

**OutputClaimsTransformations** 要素には、出力要求を修正したり新しい要求を生成するために使用される、**OutputClaimsTransformation** 要素のコレクションが含まれている場合があります。

次の例は、Facebook ID プロバイダーにより返される要求を示しています。

- **givenName** 要求にマップされている **first_name** 要求。
- **surname** 要求にマップされている **last_name** 要求。
- どの名前にもマップされていない **displayName** 要求。
- どの名前にもマップされていない **email** 要求。

また、技術プロファイルは、ID プロバイダーにより返されない要求も返します。

- ID プロバイダーの名前を保持する **identityProvider** 要求。
- 既定値の **socialIdpAuthentication** である **authenticationSource** 要求。

```xml
<OutputClaims>
  <OutputClaim ClaimTypeReferenceId="issuerUserId" PartnerClaimType="id" />
  <OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="first_name" />
  <OutputClaim ClaimTypeReferenceId="surname" PartnerClaimType="last_name" />
  <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="name" />
  <OutputClaim ClaimTypeReferenceId="email" PartnerClaimType="email" />
  <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="facebook.com" />
  <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="socialIdpAuthentication" />
</OutputClaims>
```

## <a name="metadata"></a>Metadata

| 属性 | 必須 | 説明 |
| --------- | -------- | ----------- |
| client_id | はい | ID プロバイダーのアプリケーション識別子。 |
| IdTokenAudience | いいえ | id_token の対象ユーザー。 指定される場合、Azure AD B2C は、トークンが ID プロバイダーにより返された要求内にあり、そして指定されたものと等しいかどうかをチェックします。 |
| authorization_endpoint | はい | RFC 6749 に準拠した承認エンドポイントの URL。 |
| AccessTokenEndpoint | はい | RFC 6749 に準拠したトークン エンドポイントの URL。 |
| ClaimsEndpoint | はい | RFC 6749 に準拠したユーザー情報エンドポイントの URL。 |
| end_session_endpoint | はい | RFC 6749 に準拠したセッションの終了エンドポイントの URL。 |
| AccessTokenResponseFormat | いいえ | アクセス トークン エンドポイント呼び出しの形式。 たとえば、Facebook では HTTP GET メソッドが必要ですが、アクセス トークン応答は JSON 形式です。 |
| AdditionalRequestQueryParameters | いいえ | 追加要求クエリ パラメーター。 たとえば、追加のパラメーターを ID プロバイダーに送信する場合があります。 コンマ区切り記号を使用して、複数のパラメーターを列挙できます。 |
| ClaimsEndpointAccessTokenName | いいえ | アクセス トークンのクエリ文字列パラメーターの名前。 一部の ID プロバイダーの要求エンドポイントでは、GET HTTP 要求をサポートしています。 この場合は、ベアラー トークンは、authorization ヘッダーの代わりに、クエリ文字列パラメーターを使用して送信されます。 既定値: `access_token`。 |
| ClaimsEndpointFormatName | いいえ | 形式のクエリ文字列パラメーターの名前。 たとえば、LinkedIn 要求エンドポイント `https://api.linkedin.com/v1/people/~?format=json` では、名前を `format` として設定できます。 |
| ClaimsEndpointFormat | いいえ | 形式のクエリ文字列パラメーターの値。 たとえば、LinkedIn 要求エンドポイント `https://api.linkedin.com/v1/people/~?format=json` では、値を `json` として設定できます。 |
| BearerTokenTransmissionMethod | いいえ | トークンの送信方法を指定します。 既定の方法はクエリ文字列です。 トークンを要求ヘッダーとして送信するには、`AuthorizationHeader` に設定します。 |
| ProviderName | いいえ | ID プロバイダーの名前。 |
| response_mode | いいえ | Azure AD B2C に結果を返信するために、ID プロバイダーが使用するメソッド。 指定できる値: `query`、`form_post` (既定)、または `fragment`。 |
| scope | いいえ | OAuth2 ID プロバイダーの仕様に従って定義される要求の範囲。 たとえば、`openid`、`profile`、`email` などです。 |
| HttpBinding | いいえ | アクセス トークンと要求トークンのエンドポイントに予期される HTTP バインド。 指定できる値: `GET` または `POST`。  |
| ResponseErrorCodeParamName | いいえ | HTTP 200 (Ok) 経由で返されるエラー メッセージを収納するパラメーターの名前。 |
| ExtraParamsInAccessTokenEndpointResponse | いいえ | **AccessTokenEndpoint** からの応答に、一部の ID プロバイダーにより返される可能性がある余分なパラメーターが存在します。 たとえば、**AccessTokenEndpoint** からの応答には、`openid` のような余分なパラメーターがあります。これは、access_token を除けば、**ClaimsEndpoint** 要求クエリ文字列での必須パラメーターです。 複数のパラメーター名をエスケープし、コンマ ',' 区切り記号で区切るようにしてください。 |
| ExtraParamsInClaimsEndpointRequest | いいえ | **ClaimsEndpoint** 要求に、一部の ID プロバイダーにより返される可能性がある余分なパラメーターが存在します。 複数のパラメーター名をエスケープし、コンマ ',' 区切り記号で区切るようにしてください。 |
| IncludeClaimResolvingInClaimsHandling  | いいえ | 入力と出力の要求について、[要求の解決](claim-resolver-overview.md)を技術プロファイルに含めるかどうかを指定します。 指定できる値: `true` または `false` (既定値)。 技術プロファイルで要求リゾルバーを使用する場合は、これを `true` に設定します。 |
| ResolveJsonPathsInJsonTokens  | いいえ | 技術プロファイルが JSON パスを解決するかどうかを示します。 指定できる値: `true` または `false` (既定値)。 このメタデータを使用して、入れ子になった JSON 要素からデータを読み取ります。 [OutputClaim](technicalprofiles.md#output-claims) で、`PartnerClaimType` を、出力する JSON パス要素に設定します。 例: `firstName.localized`、または `data.0.to.0.email`。|
|token_endpoint_auth_method| いいえ| Azure AD B2C からトークン エンドポイントに認証ヘッダーを送信する方法を指定します。 指定できる値は、`client_secret_post` (既定値) と `client_secret_basic` (パブリック プレビュー) です。 詳細については、[OpenID Connect クライアント認証](https://openid.net/specs/openid-connect-core-1_0.html#ClientAuthentication)に関するセクションをご覧ください。 |
|SingleLogoutEnabled| いいえ| サインイン中に技術プロファイルがフェデレーション ID プロバイダーからサインアウトを試行しているかどうかを示します。 詳しくは、[Azure AD B2C のセッション サインアウト](session-behavior.md#sign-out)に関する記事をご覧ください。指定できる値は `true`(既定値) または`false`です。|
| UsePolicyInRedirectUri | いいえ | リダイレクト URI を構築するときにポリシーを使用するかどうかを示します。 ID プロバイダーでアプリケーションを構成するときは、リダイレクト URI を指定する必要があります。 リダイレクト URI は Azure AD B2C を指します (`https://{your-tenant-name}.b2clogin.com/{your-tenant-name}.onmicrosoft.com/oauth2/authresp`)。 `true` を指定した場合は、使用するポリシーごとにリダイレクト URI を追加する必要があります。 (例: `https://{your-tenant-name}.b2clogin.com/{your-tenant-name}.onmicrosoft.com/{policy-name}/oauth2/authresp`)。 |

## <a name="cryptographic-keys"></a>暗号化キー

**CryptographicKeys** 要素には次の属性が存在します。

| 属性 | 必須 | 説明 |
| --------- | -------- | ----------- |
| client_secret | はい | ID プロバイダー アプリケーションのクライアント シークレット。 **response_types** メタデータが `code` に設定されている場合にのみ、暗号化キーが必要です。 この場合、Azure AD B2C は、アクセス トークンの認証コードを交換するために、別の呼び出しを行います。 メタデータが `id_token` に設定されている場合は、暗号化キーを省略できます。 |

## <a name="redirect-uri"></a>リダイレクト URI

ID プロバイダーのリダイレクト URI を構成する場合は、`https://{tenant-name}.b2clogin.com/{tenant-name}.onmicrosoft.com/oauth2/authresp` を入力します。 `{tenant-name}` は、必ず実際のテナントの名前 (たとえば、contosob2c) に置き換えてください。 リダイレクト URI は、すべて小文字である必要があります。

例 :

- [カスタム ポリシーを使用して Google+ を OAuth2 ID プロバイダーとして追加する](identity-provider-google.md)
