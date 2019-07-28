---
title: Azure Active Directory B2C の Identity Experience Framework スキーマのブール値要求変換の例 | Microsoft Docs
description: Azure Active Directory B2C の Identity Experience Framework スキーマのブール値要求変換の例
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/10/2018
ms.author: marsma
ms.subservice: b2c
ms.openlocfilehash: 0a08849340d19055a03f85ca401757a81cd2c95d
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/13/2019
ms.locfileid: "66511739"
---
# <a name="boolean-claims-transformations"></a>ブール値要求変換

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

この記事では、Azure Active Directory (Azure AD) B2C の Identity Experience Framework スキーマのブール値要求変換の使用例を示します。 詳細については、「[ClaimsTransformations](claimstransformations.md)」を参照してください。

## <a name="andclaims"></a>AndClaims

2 つのブール値 inputClaims の AND 演算を実行し、演算の結果で outputClaim を設定します。

| Item  | TransformationClaimType  | データ型  | メモ |
|-------| ------------------------ | ---------- | ----- |
| InputClaim | inputClaim1 | ブール値 | 評価する最初の ClaimType。 |
| InputClaim | inputClaim2  | ブール値 | 評価する 2 つ目の ClaimType。 |
|OutputClaim | outputClaim | ブール値 | この claims transformation が呼び出された後に生成される ClaimType (true または false)。 |

次の要求変換は、2 つのブール値 ClaimTypes (`isEmailNotExist` および `isSocialAccount`) を AND 演算する方法が示されています。 両方の入力要求の値が `true` である場合、出力要求 `presentEmailSelfAsserted` は `true` に設定されます。 ソーシャル アカウントの電子メールが空の場合、オーケストレーションのステップで、事前条件を使用してセルフアサート ページをプリセットできます。

```XML
<ClaimsTransformation Id="CheckWhetherEmailBePresented" TransformationMethod="AndClaims">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="isEmailNotExist" TransformationClaimType="inputClaim1" />
    <InputClaim ClaimTypeReferenceId="isSocialAccount" TransformationClaimType="inputClaim2" />
  </InputClaims>                    
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="presentEmailSelfAsserted" TransformationClaimType="outputClaim" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>例

- 入力要求:
    - **inputClaim1**: true
    - **inputClaim2**: false
- 出力要求:
    - **outputClaim**: false


## <a name="assertbooleanclaimisequaltovalue"></a>AssertBooleanClaimIsEqualToValue

2 つの要求のブール値が等しいことをチェックし、等しくない場合は例外をスローします。

| Item | TransformationClaimType  | データ型  | メモ |
| ---- | ------------------------ | ---------- | ----- |
| inputClaim | inputClaim | ブール値 | アサートされる ClaimType。 |
| InputParameter |valueToCompareTo | ブール値 | 比較される値 (true または false)。 |

**AssertBooleanClaimIsEqualToValue** 要求変換は、[セルフアサート技術プロファイル](self-asserted-technical-profile.md)によって呼び出される[検証技術プロファイル](validation-technical-profile.md)から常に実行する必要があります。 **UserMessageIfClaimsTransformationBooleanValueIsNotEqual** セルフアサート技術プロファイル メタデータにより、技術プロファイルによってユーザーに表示されるエラー メッセージが制御されます。

![AssertStringClaimsAreEqual の実行](./media/boolean-transformations/assert-execution.png)

次の要求変換は、`true` 値でブール値 ClaimType の値をチェックする方法を示しています。 `accountEnabled` ClaimType の値が false の場合、エラー メッセージがスローされます。

```XML
<ClaimsTransformation Id="AssertAccountEnabledIsTrue" TransformationMethod="AssertBooleanClaimIsEqualToValue">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="accountEnabled" TransformationClaimType="inputClaim" />
  </InputClaims>
  <InputParameters>
    <InputParameter Id="valueToCompareTo" DataType="boolean" Value="true" />
  </InputParameters>
</ClaimsTransformation>
```


`login-NonInteractive` 検証技術プロファイルによって、`AssertAccountEnabledIsTrue` 要求変換が呼び出されます。
```XML
<TechnicalProfile Id="login-NonInteractive">
  ...
  <OutputClaimsTransformations>
    <OutputClaimsTransformation ReferenceId="AssertAccountEnabledIsTrue" />
  </OutputClaimsTransformations>
</TechnicalProfile>
```

セルフアサート技術プロファイルによって **login-NonInteractive** 検証技術プロファイルが呼び出されます。

```XML
<TechnicalProfile Id="SelfAsserted-LocalAccountSignin-Email">
  <Metadata>
    <Item Key="UserMessageIfClaimsTransformationBooleanValueIsNotEqual">Custom error message if account is disabled.</Item>
  </Metadata>
  <ValidationTechnicalProfiles>
    <ValidationTechnicalProfile ReferenceId="login-NonInteractive" />
  </ValidationTechnicalProfiles>
</TechnicalProfile>
```

### <a name="example"></a>例

- 入力要求:
    - **inputClaim**: false
    - **valueToCompareTo**: true
- 結果:エラーがスローされます

## <a name="notclaims"></a>NotClaims

ブール値 inputClaim の NOT 演算を実行し、演算の結果で outputClaim を設定します。

| Item | TransformationClaimType | データ型 | メモ |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | inputClaim | ブール値 | 演算処理される要求。 |
| OutputClaim | outputClaim | ブール値 | この claims transformation が呼び出された後に生成される ClaimType (true または false)。 |

この要求変換を使用して、要求に対して論理否定を実行します。

```XML
<ClaimsTransformation Id="CheckWhetherEmailBePresented" TransformationMethod="NotClaims">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="userExists" TransformationClaimType="inputClaim" />
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="userExists" TransformationClaimType="outputClaim" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>例

- 入力要求:
    - **inputClaim**: false
- 出力要求:
    - **outputClaim**: true

## <a name="orclaims"></a>OrClaims 

2 つのブール値 inputClaims の OR を計算し、演算の結果で outputClaim を設定します。

| Item | TransformationClaimType | データ型 | メモ |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | inputClaim1 | ブール値 | 評価する最初の ClaimType。 |
| InputClaim | inputClaim2 | ブール値 | 評価する 2 つ目の ClaimType。 |
| OutputClaim | outputClaim | ブール値 | この ClaimsTransformation が呼び出された後に生成される ClaimType (true または false)。 |

次の要求変換は、2 つのブール値 ClaimTypes を `Or` 演算する方法が示されています。 いずれかの要求の値が `true` である場合、オーケストレーション ステップで、事前条件を使用してセルフアサート ページをプリセットできます。

```XML
<ClaimsTransformation Id="CheckWhetherEmailBePresented" TransformationMethod="OrClaims">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="isLastTOSAcceptedNotExists" TransformationClaimType="inputClaim1" />
    <InputClaim ClaimTypeReferenceId="isLastTOSAcceptedGreaterThanNow" TransformationClaimType="inputClaim2" />
  </InputClaims>                    
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="presentTOSSelfAsserted" TransformationClaimType="outputClaim" />
  </OutputClaims>
</ClaimsTransformation>
</ClaimsTransformation>
```

### <a name="example"></a>例

- 入力要求:
    - **inputClaim1**: true
    - **inputClaim2**: false
- 出力要求:
    - **outputClaim**: true

