---
title: Azure Active Directory B2C の Identity Experience Framework スキーマの整数要求変換の例 | Microsoft Docs
description: Azure Active Directory B2C の Identity Experience Framework スキーマの整数要求変換の例。
services: active-directory-b2c
author: davidmu1
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/10/2018
ms.author: davidmu
ms.subservice: B2C
ms.openlocfilehash: 358ee07b8fd32edded084d406e490cae9f557fdd
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/29/2019
ms.locfileid: "55159520"
---
# <a name="integer-claims-transformations"></a>整数要求変換

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

この記事では、Azure Active Directory (Azure AD) B2C の Identity Experience Framework スキーマの整数要求変換の使用例を示します。 詳細については、「[ClaimsTransformations](claimstransformations.md)」を参照してください。

## <a name="convertnumbertostringclaim"></a>ConvertNumberToStringClaim 

long データ型を string データ型に変換します。

| Item | TransformationClaimType | データ型 | メモ |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | inputClaim | long | 文字列に変換する ClaimType。  |
| OutputClaim | outputClaim | 文字列 | この ClaimsTransformation が呼び出された後に生成される ClaimType。 |

この例では、値の型が long の `numericUserId` 要求は、値の型が string の `UserId` 要求に変換されます。

```XML
<ClaimsTransformation Id="CreateUserId" TransformationMethod="ConvertNumberToStringClaim">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="numericUserId" TransformationClaimType="inputClaim" />
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="UserId" TransformationClaimType="outputClaim" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>例

- 入力要求:
    - **inputClaim**:12334 (long)
- 出力要求: 
    - **outputClaim**:"12334" (string)

