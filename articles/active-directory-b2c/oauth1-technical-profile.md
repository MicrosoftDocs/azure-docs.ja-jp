---
title: カスタム ポリシーで OAuth1 技術プロファイルを定義する
titleSuffix: Azure AD B2C
description: Azure Active Directory B2C 内のカスタム ポリシーで OAuth 1.0 技術プロファイルを定義します。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/10/2018
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 7a2cbca8c02dcbfc0e59d31040d1fca7a790cd35
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "96936660"
---
# <a name="define-an-oauth1-technical-profile-in-an-azure-active-directory-b2c-custom-policy"></a>Azure Active Directory B2C カスタム ポリシーで OAuth1 技術プロファイルを定義する

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Azure Active Directory B2C (Azure AD B2C) では、[OAuth 1.0 プロトコル](https://tools.ietf.org/html/rfc5849) の ID プロバイダーのサポートを提供しています。 この記事では、この標準化されたプロトコルをサポートするクレーム プロバイダーとやりとりするための、技術プロファイルの詳細について説明します。 OAuth1 技術プロファイルを使用すると、Twitter など、OAuth1 ベースの ID プロバイダーとフェデレーションできます。 ID プロバイダーとのフェデレーションにより、ユーザーは、既存のソーシャル ID またはエンタープライズ ID でサインインできます。

## <a name="protocol"></a>Protocol

**Protocol** 要素の **Name** 属性は `OAuth1` に設定する必要があります。 たとえば、**Twitter-OAUTH1** 技術プロファイル用のプロトコルは `OAuth1` です。

```xml
<TechnicalProfile Id="Twitter-OAUTH1">
  <DisplayName>Twitter</DisplayName>
  <Protocol Name="OAuth1" />
  ...
```

## <a name="input-claims"></a>入力クレーム

**InputClaims** と **InputClaimsTransformations** の要素は空であるか、存在しません。

## <a name="output-claims"></a>出力クレーム

**OutputClaims** 要素には、OAuth1 ID プロバイダーにより返される要求の一覧が存在します。 お使いのポリシーに定義されている要求の名前を、ID プロバイダーで定義されている名前にマップする必要があるかもしれません。 **DefaultValue** 属性を設定している限り、ID プロバイダーにより返されない要求を追加することもできます。

**OutputClaimsTransformations** 要素には、出力要求を修正したり新しい要求を生成するために使用される、**OutputClaimsTransformation** 要素のコレクションが含まれている場合があります。

次の例は、Twitter ID プロバイダーにより返される要求を示しています。

- **issuerUserId** 要求にマップされている **user_id** 要求。
- **displayName** 要求にマップされている **screen_name** 要求。
- どの名前にもマップされていない **email** 要求。

また、技術プロファイルは、ID プロバイダーにより返されない要求も返します。

- ID プロバイダーの名前を保持する **identityProvider** 要求。
- 既定値の `socialIdpAuthentication` である **authenticationSource** 要求。

```xml
<OutputClaims>
  <OutputClaim ClaimTypeReferenceId="issuerUserId" PartnerClaimType="user_id" />
  <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="screen_name" />
  <OutputClaim ClaimTypeReferenceId="email" />
  <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="twitter.com" />
  <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="socialIdpAuthentication" />
</OutputClaims>
```

## <a name="metadata"></a>メタデータ

| 属性 | 必須 | 説明 |
| --------- | -------- | ----------- |
| client_id | はい | ID プロバイダーのアプリケーション識別子。 |
| ProviderName | いいえ | ID プロバイダーの名前。 |
| request_token_endpoint | はい | RFC 5849 に準拠した要求トークン エンドポイントの URL。 |
| authorization_endpoint | はい | RFC 5849 に準拠した承認エンドポイントの URL。 |
| access_token_endpoint | はい | RFC 5849 に準拠したトークン エンドポイントの URL。 |
| ClaimsEndpoint | いいえ | ユーザー情報エンドポイントの URL。 |
| ClaimsResponseFormat | いいえ | 要求応答の形式。|

## <a name="cryptographic-keys"></a>暗号化キー

**CryptographicKeys** 要素には次の属性が存在します。

| 属性 | 必須 | 説明 |
| --------- | -------- | ----------- |
| client_secret | はい | ID プロバイダー アプリケーションのクライアント シークレット。   |

## <a name="redirect-uri"></a>リダイレクト URI

ID プロバイダーのリダイレクト URI を構成する場合は、`https://{tenant-name}.b2clogin.com/{tenant-name}.onmicrosoft.com/{policy-id}/oauth1/authresp` を入力します。 `{tenant-name}` をテナント名 (例: contosob2c) に置き換え、`{policy-id}` をポリシーの識別子 (例: b2c_1_policy) に置き換える必要があります。 リダイレクト URI は、すべて小文字である必要があります。 ID プロバイダーのログインを使用するすべてのポリシーのために、リダイレクト URL を追加します。

例 :

- [カスタム ポリシーを使用して Twitter を OAuth1 ID プロバイダーとして追加する](identity-provider-twitter.md)
