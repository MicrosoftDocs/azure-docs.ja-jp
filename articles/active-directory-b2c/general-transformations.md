---
title: カスタム ポリシーの一般的な要求変換の例
titleSuffix: Azure AD B2C
description: Azure Active Directory B2C の Identity Experience Framework (IEF) スキーマの一般的な要求変換の例。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 02/03/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 52831a1907d5ca8d13b0477c909d0d0358873973
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "85202222"
---
# <a name="general-claims-transformations"></a>一般要求変換

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

この記事では、Azure Active Directory B2C (Azure AD B2C) の Identity Experience Framework スキーマの一般的な要求変換の使用例を示します。 詳細については、「[ClaimsTransformations](claimstransformations.md)」を参照してください。

## <a name="copyclaim"></a>CopyClaim

要求の値を別の要求へコピーします。 どちらの要求も、同じ型から派生している必要があります。

| Item | TransformationClaimType | データ型 | Notes |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | inputClaim | string、int | コピーする要求の種類。 |
| OutputClaim | outputClaim | string、int | この ClaimsTransformation が呼び出された後に生成される ClaimType。 |

この要求変換を使用して、文字列または数値の要求から別の要求に値をコピーします。 次の例では、externalEmail 要求の値を電子メール要求にコピーします。

```xml
<ClaimsTransformation Id="CopyEmailAddress" TransformationMethod="CopyClaim">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="externalEmail" TransformationClaimType="inputClaim"/>
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="email" TransformationClaimType="outputClaim"/>
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>例

- 入力要求:
    - **inputClaim**: bob@contoso.com
- 出力要求:
    - **outputClaim**: bob@contoso.com

## <a name="doesclaimexist"></a>DoesClaimExist

**inputClaim** が存在するかどうかを確認し、**outputClaim** を true または false に適切に設定します。

| Item | TransformationClaimType | データ型 | Notes |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | inputClaim |Any | 存在を確認する必要のある入力要求。 |
| OutputClaim | outputClaim | boolean | この ClaimsTransformation が呼び出された後に生成される ClaimType。 |

この要求変換を使用して、要求が存在するかどうか、または何らかの値が含まれているかどうかをチェックします。 戻り値はブール値であり、それによって、要求が存在するかどうかが示されます。 次の例では、電子メール アドレスが存在するかどうかを確認します。

```xml
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

## <a name="hash"></a>ハッシュ インデックス

salt と secret を使用して、提供されたプレーン テキストをハッシュします。 使用されるハッシュ アルゴリズムは SHA-256 です。

| Item | TransformationClaimType | データ型 | Notes |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | plaintext | string | 暗号化される入力要求。 |
| InputClaim | salt | string | salt パラメーター。 `CreateRandomString` 要求変換を使用して、ランダムな値を作成できます。 |
| InputParameter | randomizerSecret | string | 既存の Azure AD B2C **ポリシー キー** をポイントします。 新しいポリシー キーを作成するには、Azure AD B2C テナントの **[管理]** で、 **[Identity Experience Framework]** を選択します。 **[ポリシー キー]** を選択して、テナント内で使用できるキーを確認します。 **[追加]** を選択します。 **[オプション]** には **[手動]** を選択します。 名前を指定します (プレフィックス *B2C_1A_* が自動的に追加される場合があります)。 **[シークレット]** テキスト ボックスに、使用するシークレットを入力します (1234567890 など)。 **[キー使用法]** には **[署名]** を選択します。 **［作成］** を選択します |
| OutputClaim | hash | string | この要求変換が呼び出された後に生成される ClaimType。 `plaintext` inputClaim で構成されている要求。 |

```xml
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
  - **outputClaim**: CdMNb/KTEfsWzh9MR1kQGRZCKjuxGMWhA5YQNihzV6U=
