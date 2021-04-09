---
title: カスタム ポリシーのソーシャル アカウント要求変換の例
titleSuffix: Azure AD B2C
description: Azure Active Directory B2C の Identity Experience Framework (IEF) スキーマのソーシャル アカウント要求変換の例。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/10/2018
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: c60cecb9d6bfeeefc0569a1a57185d13f0c6442f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "94953119"
---
# <a name="social-accounts-claims-transformations"></a>ソーシャル アカウント要求変換

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Azure Active Directory B2C (Azure AD B2C) では、ソーシャル アカウント ID は、**alternativeSecurityIdCollection** 要求の種類の `userIdentities` 属性に格納されます。 **alternativeSecurityIdCollection** の各項目では、発行者 (ID プロバイダー名、facebook.com など) や `issuerUserId` (発行者の一意のユーザー識別子) を指定します。

```json
"userIdentities": [{
    "issuer": "google.com",
    "issuerUserId": "MTA4MTQ2MDgyOTI3MDUyNTYzMjcw"
  },
  {
    "issuer": "facebook.com",
    "issuerUserId": "MTIzNDU="
  }]
```

この記事では、Azure AD B2C の Identity Experience Framework スキーマのソーシャル アカウント要求変換の使用例を示します。 詳細については、「[ClaimsTransformations](claimstransformations.md)」を参照してください。

## <a name="createalternativesecurityid"></a>CreateAlternativeSecurityId

Azure Active Directory の呼び出しで使用できる、ユーザーの alternativeSecurityId プロパティの JSON 表現を作成します。 詳細については、「[AlternativeSecurityId](/graph/api/resources/alternativesecurityid)のスキーマ」を参照してください。

| Item | TransformationClaimType | データ型 | Notes |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | key | string | ソーシャル ID プロバイダーによって使われる一意のユーザー識別子を指定する ClaimType。 |
| InputClaim | identityProvider | string | ソーシャル アカウント ID プロバイダー名(facebook.com など) を指定する ClaimType。 |
| OutputClaim | alternativeSecurityId | string | ClaimsTransformation が呼び出された後に生成される ClaimType。 ソーシャル アカウント ユーザーの ID に関する情報が含まれています。 **issuer** は、`identityProvider` 要求の値です。 **issuerUserId** は、base64 形式の `key` 要求の値です。 |

この要求変換を使用して `alternativeSecurityId` ClaimType を生成します。 これは、すべてのソーシャル ID プロバイダー技術プロファイル (`Facebook-OAUTH` など) によって使用されます。 次の要求変換は、ユーザー ソーシャル アカウント ID と ID プロバイダー名を受け取ります。 この技術プロファイルの出力は、Azure AD ディレクトリ サービスで使用できる JSON 文字列形式です。

```xml
<ClaimsTransformation Id="CreateAlternativeSecurityId" TransformationMethod="CreateAlternativeSecurityId">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="issuerUserId" TransformationClaimType="key" />
    <InputClaim ClaimTypeReferenceId="identityProvider" TransformationClaimType="identityProvider" />
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="alternativeSecurityId" TransformationClaimType="alternativeSecurityId" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>例

- 入力要求:
    - **key**: 12334
    - **identityProvider**: Facebook.com
- 出力要求:
    - **alternativeSecurityId**: { "issuer": "facebook.com", "issuerUserId": "MTA4MTQ2MDgyOTI3MDUyNTYzMjcw"}

## <a name="additemtoalternativesecurityidcollection"></a>AddItemToAlternativeSecurityIdCollection

`AlternativeSecurityId` を `alternativeSecurityIdCollection` 要求に追加します。

| Item | TransformationClaimType | データ型 | Notes |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | item | string | 出力要求に追加される ClaimType。 |
| InputClaim | collection | alternativeSecurityIdCollection | 要求変換で使用される ClaimTypes (ポリシーで使用可能な場合)。 指定されている場合は、要求変換によってコレクションの最後に `item` が追加されます。 |
| OutputClaim | collection | alternativeSecurityIdCollection | この ClaimsTransformation が呼び出された後に生成される ClaimTypes。 入力 `collection` と `item` の両方の項目を含む新しいコレクション。 |

次の例では、新しいソーシャル ID と既存のアカウントをリンクしています。 新しいソーシャル ID をリンクするには:
1. **AAD-UserReadUsingAlternativeSecurityId** および **AAD-UserReadUsingObjectId** 技術プロファイルで、ユーザーの **alternativeSecurityIds** 要求を出力します。
1. このユーザーに関連付けられていない ID プロバイダーのいずれかを使用してサインインするように、ユーザーに依頼します。
1. **CreateAlternativeSecurityId** 要求変換を使用して、新しい **alternativeSecurityId** 要求の種類を `AlternativeSecurityId2` という名前で作成します。
1. **AddItemToAlternativeSecurityIdCollection** 要求変換を呼び出して、既存の **AlternativeSecurityIds** 要求に **AlternativeSecurityId2** 要求を追加します。
1. ユーザー アカウントに **alternativeSecurityIds** 要求を保持します。

```xml
<ClaimsTransformation Id="AddAnotherAlternativeSecurityId" TransformationMethod="AddItemToAlternativeSecurityIdCollection">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="AlternativeSecurityId2" TransformationClaimType="item" />
    <InputClaim ClaimTypeReferenceId="AlternativeSecurityIds" TransformationClaimType="collection" />
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="AlternativeSecurityIds" TransformationClaimType="collection" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>例

- 入力要求:
    - **item**: { "issuer": "facebook.com", "issuerUserId": "MTIzNDU=" }
    - **collection**: [ { "issuer": "live.com", "issuerUserId": "MTA4MTQ2MDgyOTI3MDUyNTYzMjcw" } ]
- 出力要求:
    - **collection**: [ { "issuer": "live.com", "issuerUserId": "MTA4MTQ2MDgyOTI3MDUyNTYzMjcw" }, { "issuer": "facebook.com", "issuerUserId": "MTIzNDU=" } ]

## <a name="getidentityprovidersfromalternativesecurityidcollectiontransformation"></a>GetIdentityProvidersFromAlternativeSecurityIdCollectionTransformation

発行者の一覧を **alternativeSecurityIdCollection** 要求から新しい **stringCollection** 要求に返します。

| Item | TransformationClaimType | データ型 | Notes |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | alternativeSecurityIdCollection | alternativeSecurityIdCollection | ID プロバイダー (発行者) の一覧を取得するために使用される ClaimType。 |
| OutputClaim | identityProvidersCollection | stringCollection | この ClaimsTransformation が呼び出された後に生成される ClaimTypes。 alternativeSecurityIdCollection 入力要求に関連付けられている ID プロバイダーの一覧。 |

次の要求は、ユーザー **alternativeSecurityIds** 要求を読み取り、そのアカウントに関連付けられている ID プロバイダー名の一覧を抽出します。 出力 **identityProvidersCollection** を使用して、アカウントに関連付けられている ID プロバイダーの一覧をユーザーに表示します。 または、ID プロバイダー選択ページで、出力 **identityProvidersCollection** 要求に基づいて ID プロバイダーの一覧をフィルター処理します。 このようにして、ユーザーは、まだアカウントに関連付けられていない新しいソーシャル ID をリンクすることを選択できます。

```xml
<ClaimsTransformation Id="ExtractIdentityProviders" TransformationMethod="GetIdentityProvidersFromAlternativeSecurityIdCollectionTransformation">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="alternativeSecurityIds" TransformationClaimType="alternativeSecurityIdCollection" />
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="identityProviders" TransformationClaimType="identityProvidersCollection" />
  </OutputClaims>
</ClaimsTransformation>
```

- 入力要求:
    - **alternativeSecurityIdCollection**: [ { "issuer": "google.com", "issuerUserId": "MTA4MTQ2MDgyOTI3MDUyNTYzMjcw" }, { "issuer": "facebook.com", "issuerUserId": "MTIzNDU=" } ]
- 出力要求:
    - **identityProvidersCollection**: [ "facebook.com", "google.com" ]

## <a name="removealternativesecurityidbyidentityprovider"></a>RemoveAlternativeSecurityIdByIdentityProvider

**AlternativeSecurityId** を **alternativeSecurityIdCollection** 要求から削除します。

| Item | TransformationClaimType | データ型 | Notes |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | identityProvider | string | コレクションから削除する ID プロバイダー名を含む ClaimType。 |
| InputClaim | collection | alternativeSecurityIdCollection | 要求変換で使用される ClaimTypes。 要求変換により、コレクションから identityProvider が削除されます。 |
| OutputClaim | collection | alternativeSecurityIdCollection | この ClaimsTransformation が呼び出された後に生成される ClaimTypes。 コレクションから identityProvider が削除された の後の、新しいコレクション。 |

次の例では、ソーシャル ID の 1 つと既存のアカウントのリンクを解除します。 ソーシャル ID のリンクを解除するには:
1. **AAD-UserReadUsingAlternativeSecurityId** および **AAD-UserReadUsingObjectId** 技術プロファイルで、ユーザーの **alternativeSecurityIds** 要求を出力します。
2. このユーザーに関連付けられている ID プロバイダーの一覧から削除するソーシャル アカウントを選択するように、ユーザーに依頼します。
3. ID プロバイダー名を使用して、選択したソーシャル ID を削除した、**RemoveAlternativeSecurityIdByIdentityProvider** 要求変換を呼び出す、要求変換技術プロファイルを呼び出します。
4. ユーザー アカウントに **alternativeSecurityIds** 要求を保持します。

```xml
<ClaimsTransformation Id="RemoveAlternativeSecurityIdByIdentityProvider" TransformationMethod="RemoveAlternativeSecurityIdByIdentityProvider">
    <InputClaims>
        <InputClaim ClaimTypeReferenceId="secondIdentityProvider" TransformationClaimType="identityProvider" />
        <InputClaim ClaimTypeReferenceId="AlternativeSecurityIds" TransformationClaimType="collection" />
    </InputClaims>
    <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="AlternativeSecurityIds" TransformationClaimType="collection" />
    </OutputClaims>
</ClaimsTransformation>
</ClaimsTransformations>
```

### <a name="example"></a>例

- 入力要求:
    - **identityProvider**: facebook.com
    - **collection**: [ { "issuer": "live.com", "issuerUserId": "MTA4MTQ2MDgyOTI3MDUyNTYzMjcw" }, { "issuer": "facebook.com", "issuerUserId": "MTIzNDU=" } ]
- 出力要求:
    - **collection**: [ { "issuer": "live.com", "issuerUserId": "MTA4MTQ2MDgyOTI3MDUyNTYzMjcw" } ]