---
title: Azure Active Directory B2C の Identity Experience Framework スキーマの StringCollection 要求変換の例 | Microsoft Docs
description: Azure Active Directory B2C の Identity Experience Framework スキーマの StringCollection 要求変換の例
services: active-directory-b2c
author: davidmu1
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/10/2018
ms.author: davidmu
ms.subservice: B2C
ms.openlocfilehash: c312433832f7402eaff8b40c4e0a2a61397f6f87
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2019
ms.locfileid: "58123506"
---
# <a name="stringcollection-claims-transformations"></a>StringCollection 要求変換

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

この記事では、Azure Active Directory (Azure AD) B2C の Identity Experience Framework スキーマの文字列コレクション要求変換の使用例を示します。 詳細については、「[ClaimsTransformations](claimstransformations.md)」を参照してください。

## <a name="additemtostringcollection"></a>AddItemToStringCollection

新しい stringCollection 要求に文字列要求を追加します。 

| Item | TransformationClaimType | データ型 | メモ |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | item | 文字列 | 出力要求に追加される ClaimType。 |
| InputClaim | collection | stringCollection | （省略可能）これを指定すると、要求変換によってこのコレクションから項目がコピーされ、出力コレクション要求の最後に項目が追加されます。 |
| OutputClaim | collection | stringCollection | この ClaimsTransformation が呼び出された後に生成される ClaimTypes。 |

この要求変換を使用して、新しい stringCollection または既存の stringCollection に文字列を追加します。 通常、これは **AAD-UserWriteUsingAlternativeSecurityId** 技術プロファイルで使用されます。 新しいソーシャル アカウントが作成される前に、**CreateOtherMailsFromEmail** 要求変換によって ClaimType が読み取られ、**otherMails** ClaimType に値が追加されます。 

次の要求変換によって、**email** ClaimType が **otherMails** ClaimType に追加されます。

```XML
<ClaimsTransformation Id="CreateOtherMailsFromEmail" TransformationMethod="AddItemToStringCollection">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="email" TransformationClaimType="item" />
    <InputClaim ClaimTypeReferenceId="otherMails" TransformationClaimType="collection" />
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="otherMails" TransformationClaimType="collection" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>例

- 入力要求:
  - **collection**: ["someone@outlook.com"]
  - **item**: "admin@contoso.com"
- 出力要求: 
  - **collection**: ["someone@outlook.com", "admin@contoso.com"]

## <a name="addparametertostringcollection"></a>AddParameterToStringCollection

新しい stringCollection 要求に文字列パラメーターを追加します。 

| Item | TransformationClaimType | データ型 | メモ |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | collection | stringCollection | （省略可能）これを指定すると、要求変換によってこのコレクションから項目がコピーされ、出力コレクション要求の最後に項目が追加されます。 |
| InputParameter | item | 文字列 | 出力要求に追加される値。 |
| OutputClaim | collection | stringCollection | この ClaimsTransformation が呼び出された後に生成される ClaimTypes。 |

この要求変換を使用して、新しい stringCollection または既存の stringCollection に文字列値を追加します。 次の例では、定数の電子メール アドレス (admin@contoso.com) を **otherMails** 要求に追加します。 

```XML
<ClaimsTransformation Id="SetCompanyEmail" TransformationMethod="AddParameterToStringCollection">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="otherMails" TransformationClaimType="collection" />
  </InputClaims>
  <InputParameters>
    <InputParameter Id="item" DataType="string" Value="admin@contoso.com" />
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="otherMails" TransformationClaimType="collection" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>例

- 入力要求:
  - **collection**: ["someone@outlook.com"]
- 入力パラメーター 
  - **item**: "admin@contoso.com"
- 出力要求:
  - **collection**: ["someone@outlook.com", "admin@contoso.com"]

## <a name="getsingleitemfromstringcollection"></a>GetSingleItemFromStringCollection

指定された文字列コレクションから最初の項目を取得します。 

| Item | TransformationClaimType | データ型 | メモ |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | collection | stringCollection | 項目を取得する要求変換で使用される ClaimTypes。 |
| OutputClaim | extractedItem | 文字列 | この ClaimsTransformation が呼び出された後に生成される ClaimTypes。 コレクション内の最初の項目 |

次の例では、**otherMails** 要求が読み取られ、最初の項目が **email** 要求に返されます。 

```XML
<ClaimsTransformation Id="CreateEmailFromOtherMails" TransformationMethod="GetSingleItemFromStringCollection">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="otherMails" TransformationClaimType="collection" />
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="email" TransformationClaimType="extractedItem" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>例

- 入力要求:
  - **collection**: ["someone@outlook.com", "someone@contoso.com"]
- 出力要求: 
  - **extractedItem**: "someone@outlook.com"

