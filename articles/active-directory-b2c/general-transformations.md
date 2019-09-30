---
title: Azure Active Directory B2C の Identity Experience Framework スキーマの一般的な要求変換例
description: Azure Active Directory B2C の Identity Experience Framework スキーマの一般的な要求変換例。
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 08/27/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 7cea33cb61f8f8d0fe305a757f11c80bc5da24ca
ms.sourcegitcommit: 94ee81a728f1d55d71827ea356ed9847943f7397
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/26/2019
ms.locfileid: "70032898"
---
# <a name="general-claims-transformations"></a>一般要求変換

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

この記事では、Azure Active Directory B2C (Azure AD B2C) の Identity Experience Framework スキーマの一般的な要求変換の使用例を示します。 詳細については、「[ClaimsTransformations](claimstransformations.md)」を参照してください。

## <a name="doesclaimexist"></a>DoesClaimExist

**inputClaim** が存在するかどうかを確認し、**outputClaim** を true または false に適切に設定します。

| Item | TransformationClaimType | データ型 | メモ |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | inputClaim |Any | 存在を確認する必要のある入力要求。 |
| OutputClaim | outputClaim | ブール値 | この ClaimsTransformation が呼び出された後に生成される ClaimType。 |

この要求変換を使用して、要求が存在するかどうか、または何らかの値が含まれているかどうかをチェックします。 戻り値はブール値であり、それによって、要求が存在するかどうかが示されます。 次の例では、電子メール アドレスが存在するかどうかを確認します。

```XML
<ClaimsTransformation Id="CheckIfEmailPresent" TransformationMethod="DoesClaimExist">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="email" TransformationClaimType="inputClaim" />
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="isEmailPresent" TransformationClaimType="outputClaim" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>例

- 入力要求:
  - **inputClaim**: someone@contoso.com
- 出力要求:
  - **outputClaim**: true

## <a name="hash"></a>Hash

salt と secret を使用して、提供されたプレーン テキストをハッシュします。 使用されるハッシュ アルゴリズムは SHA-256 です。

| Item | TransformationClaimType | データ型 | メモ |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | plaintext | string | 暗号化される入力要求。 |
| InputClaim | salt | string | salt パラメーター。 `CreateRandomString` 要求変換を使用して、ランダムな値を作成できます。 |
| InputParameter | randomizerSecret | string | 既存の Azure AD B2C **ポリシー キー**をポイントします。 新しいポリシー キーを作成するには:Azure AD B2C テナントの **[管理]** で、 **[Identity Experience Framework]** を選択します。 **[ポリシー キー]** を選択して、テナント内で使用できるキーを確認します。 **[追加]** を選択します。 **[オプション]** には **[手動]** を選択します。 名前を指定します (プレフィックス *B2C_1A_* が自動的に追加される場合があります)。 **[シークレット]** テキスト ボックスに、使用するシークレットを入力します (1234567890 など)。 **[キー使用法]** には **[署名]** を選択します。 **作成** を選択します。 |
| OutputClaim | hash | string | この要求変換が呼び出された後に生成される ClaimType。 `plaintext` inputClaim で構成されている要求。 |

```XML
<ClaimsTransformation Id="HashPasswordWithEmail" TransformationMethod="Hash">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="password" TransformationClaimType="plaintext" />
    <InputClaim ClaimTypeReferenceId="email" TransformationClaimType="salt" />
  </InputClaims>
  <InputParameters>
    <InputParameter Id="randomizerSecret" DataType="string" Value="B2C_1A_AccountTransformSecret" />
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="hashedPassword" TransformationClaimType="hash" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>例

- 入力要求:
  - **plaintext**: MyPass@word1
  - **salt**: 487624568
  - **randomizerSecret**: B2C_1A_AccountTransformSecret
- 出力要求:
  - **outputClaim**:CdMNb/KTEfsWzh9MR1kQGRZCKjuxGMWhA5YQNihzV6U=
