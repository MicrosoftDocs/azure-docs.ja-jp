---
title: カスタム ポリシーの StringCollection 要求変換の例
titleSuffix: Azure AD B2C
description: Azure Active Directory B2C の Identity Experience Framework (IEF) スキーマの StringCollection 要求変換の例。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 03/04/2021
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: c9104fb4598eb62ed96d0b21734053fa118b5237
ms.sourcegitcommit: dac05f662ac353c1c7c5294399fca2a99b4f89c8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/04/2021
ms.locfileid: "102120284"
---
# <a name="stringcollection-claims-transformations"></a>StringCollection 要求変換

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

この記事では、Azure Active Directory B2C (Azure AD B2C) の Identity Experience Framework スキーマの文字列コレクション要求変換の使用例を示します。 詳細については、「[ClaimsTransformations](claimstransformations.md)」を参照してください。

## <a name="additemtostringcollection"></a>AddItemToStringCollection

新しい一意の値 「stringCollection」の要求に文字列要求を追加します。

| Item | TransformationClaimType | データ型 | Notes |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | item | string | 出力要求に追加される ClaimType。 |
| InputClaim | collection | stringCollection | 出力要求に追加される文字列コレクション。 コレクションに項目が含まれる場合、要求変換によって項目がコピーされ、出力コレクション要求の最後に項目が追加されます。 |
| OutputClaim | collection | stringCollection | この ClaimType は、要求変換が呼び出された後に生成され、入力パラメータに指定された値で呼び出されます。 |

この要求変換を使用して、新しい stringCollection または既存の stringCollection に文字列を追加します。 通常、これは **AAD-UserWriteUsingAlternativeSecurityId** 技術プロファイルで使用されます。 新しいソーシャル アカウントが作成される前に、**CreateOtherMailsFromEmail** 要求変換によって ClaimType が読み取られ、**otherMails** ClaimType に値が追加されます。

次の要求変換によって、**email** ClaimType が **otherMails** ClaimType に追加されます。

```xml
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

新しい一意の値「stringCollection claim」に文字列パラメータを追加します。

| Item | TransformationClaimType | データ型 | Notes |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | collection | stringCollection | 出力要求に追加される文字列コレクション。 コレクションに項目が含まれる場合、要求変換によって項目がコピーされ、出力コレクション要求の最後に項目が追加されます。 |
| InputParameter | item | string | 出力要求に追加される値。 |
| OutputClaim | collection | stringCollection | この要求変換が呼び出された後に生成される ClaimType は、入力パラメーターに指定された値で呼び出されています。 |

この要求変換を使用して、新しい stringCollection または既存の stringCollection に文字列値を追加します。 次の例では、定数の電子メール アドレス (admin@contoso.com) を **otherMails** 要求に追加します。

```xml
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

| Item | TransformationClaimType | データ型 | Notes |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | collection | stringCollection | 項目を取得する要求変換で使用される ClaimTypes。 |
| OutputClaim | extractedItem | string | この ClaimsTransformation が呼び出された後に生成される ClaimTypes。 コレクション内の最初の項目 |

次の例では、**otherMails** 要求が読み取られ、最初の項目が **email** 要求に返されます。

```xml
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


## <a name="stringcollectioncontains"></a>StringCollectionContains

StringCollection 要求の種類に要素が含まれているかどうかをチェックします。

| Item | TransformationClaimType | データ型 | Notes |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | inputClaim | stringCollection | 検索する要求の種類。 |
|InputParameter|item|string|検索する値。|
|InputParameter|ignoreCase|string|この比較が比較対象の文字列の大文字と小文字を無視するかどうかを指定します。|
| OutputClaim | outputClaim | boolean | この ClaimsTransformation が呼び出された後に生成される ClaimType。 コレクションにこのような文字列が含まれているかどうかを示すブール値のインジケーター |

次の例では、`roles` stringCollection 要求の種類に **admin** の値が含まれているかどうかをチェックしています。

```xml
<ClaimsTransformation Id="IsAdmin" TransformationMethod="StringCollectionContains">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="roles" TransformationClaimType="inputClaim"/>
  </InputClaims>
  <InputParameters>
    <InputParameter  Id="item" DataType="string" Value="Admin"/>
    <InputParameter  Id="ignoreCase" DataType="string" Value="true"/>
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="isAdmin" TransformationClaimType="outputClaim"/>
  </OutputClaims>
</ClaimsTransformation>
```

- 入力要求:
    - **inputClaim**: ["reader", "author", "admin"]
- 入力パラメーター:
    - **項目**:"Admin"
    - **ignoreCase**: "true"
- 出力要求:
    - **outputClaim**: "true"

## <a name="stringcollectioncontainsclaim"></a>StringCollectionContainsClaim

StringCollection 要求の種類に要求の値が含まれているかどうかをチェックします。

| Item | TransformationClaimType | データ型 | Notes |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | collection | stringCollection | 検索する要求の種類。 |
| InputClaim | item|string| 検索する値を含む要求の種類。|
|InputParameter|ignoreCase|string|この比較が比較対象の文字列の大文字と小文字を無視するかどうかを指定します。|
| OutputClaim | outputClaim | boolean | この ClaimsTransformation が呼び出された後に生成される ClaimType。 コレクションにこのような文字列が含まれているかどうかを示すブール値のインジケーター |

次の例では、`roles` stringCollection 要求の種類に `role` の要求の値が含まれているかどうかをチェックしています。

```xml
<ClaimsTransformation Id="HasRequiredRole" TransformationMethod="StringCollectionContainsClaim">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="roles" TransformationClaimType="collection" />
    <InputClaim ClaimTypeReferenceId="role" TransformationClaimType="item" />
  </InputClaims>
  <InputParameters>
    <InputParameter Id="ignoreCase" DataType="string" Value="true" />
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="hasAccess" TransformationClaimType="outputClaim" />
  </OutputClaims>
</ClaimsTransformation> 
```

- 入力要求:
    - **collection**: ["reader", "author", "admin"]
    - **項目**:"Admin"
- 入力パラメーター:
    - **ignoreCase**: "true"
- 出力要求:
    - **outputClaim**: "true"
