---
title: Azure Active Directory B2C の Identity Experience Framework スキーマの JSON 要求変換の例 | Microsoft Docs
description: Azure Active Directory B2C の Identity Experience Framework スキーマの JSON 要求変換の例
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/10/2018
ms.author: marsma
ms.subservice: b2c
ms.openlocfilehash: 9a026d205d3ab855ecbb51048e7464df6fb4a094
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/13/2019
ms.locfileid: "66510748"
---
# <a name="json-claims-transformations"></a>JSON 要求変換

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

この記事では、Azure Active Directory (Azure AD) B2C の Identity Experience Framework スキーマの JSON 要求変換の使用例を示します。 詳細については、「[ClaimsTransformations](claimstransformations.md)」を参照してください。

## <a name="getclaimfromjson"></a>GetClaimFromJson

JSON データから、指定された要素を取得します。

| Item | TransformationClaimType | データ型 | メモ |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | inputJson | string | 項目を取得する要求変換で使用される ClaimTypes。 |
| InputParameter | claimToExtract | string | 抽出する JSON 要素の名前。 |
| OutputClaim | extractedClaim | string | このclaims transformation が呼び出された後に生成される ClaimType は、 _claimToExtract_ 入力パラメーターに指定された要素の値です。 |

次の例では、JSON データから要求変換によって`emailAddress`要素が抽出されます。`{"emailAddress": "someone@example.com", "displayName": "Someone"}`

```XML
<ClaimsTransformation Id="GetEmailClaimFromJson" TransformationMethod="GetClaimFromJson">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="customUserData" TransformationClaimType="inputJson" />
  </InputClaims>
  <InputParameters>
    <InputParameter Id="claimToExtract" DataType="string" Value="emailAddress" />
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="extractedEmail" TransformationClaimType="extractedClaim" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>例

- 入力要求:
  - **inputJson**: {"emailAddress": "someone@example.com", "displayName":"Someone"}
- 入力パラメーター:
    - **claimToExtract**: emailAddress
- 出力要求： 
  - **extractedClaim**: someone@example.com


## <a name="getclaimsfromjsonarray"></a>GetClaimsFromJsonArray

Json データから、指定された要素の一覧を取得します。

| Item | TransformationClaimType | データ型 | メモ |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | jsonSourceClaim | string | 項目を取得する要求変換で使用される ClaimTypes。 |
| InputParameter | errorOnMissingClaims | ブール値 | 要求の 1 つが不足している場合は、エラーをスローするかどうかを指定します。 |
| InputParameter | includeEmptyClaims | string | 空の要求を含めるかどうかを指定します。 |
| InputParameter | jsonSourceKeyName | string | 要素のキー名 |
| InputParameter | jsonSourceValueName | string | 要素の値の名前 |
| OutputClaim | コレクション | 文字列、int、ブール値、および datetime |抽出する要求のリスト。 要求の名前は、_jsonSourceClaim_ に指定されている入力要求の名前と同じでなければなりません。 |

次の例では、要求変換によって、次の要求が JSON データから抽出されます。 email (文字列)、displayName (文字列)、membershipNum (int)、アクティブ (ブール値) 、および birthdate (datetime)。

```JSON
[{"key":"email","value":"someone@example.com"}, {"key":"displayName","value":"Someone"}, {"key":"membershipNum","value":6353399}, {"key":"active","value":true}, {"key":"birthdate","value":"1980-09-23T00:00:00Z"}]
```

```XML
<ClaimsTransformation Id="GetClaimsFromJson" TransformationMethod="GetClaimsFromJsonArray">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="jsonSourceClaim" TransformationClaimType="jsonSource" />
  </InputClaims>
  <InputParameters>
    <InputParameter Id="errorOnMissingClaims" DataType="boolean" Value="false" />
    <InputParameter Id="includeEmptyClaims" DataType="boolean" Value="false" />
    <InputParameter Id="jsonSourceKeyName" DataType="string" Value="key" />
    <InputParameter Id="jsonSourceValueName" DataType="string" Value="value" />
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="email" />
    <OutputClaim ClaimTypeReferenceId="displayName" />
    <OutputClaim ClaimTypeReferenceId="membershipNum" />
    <OutputClaim ClaimTypeReferenceId="active" />
    <OutputClaim ClaimTypeReferenceId="birthdate" />
  </OutputClaims>
</ClaimsTransformation>
```    

- 入力要求：
  - **jsonSourceClaim**: [{「キー」:"email"、"value":"someone@example.com"}、{「キー」:"displayName"、"value":"Someone"}、{「キー」:"membershipNum"、"value": 6353399}、{「キー」:"active"、"value": true}、{「キー」:"birthdate"、"value":"1980-09-23T00:00:00Z"}]
- 入力パラメーター：
    - **errorOnMissingClaims**：false
    - **includeEmptyClaims**：false
    - **jsonSourceKeyName**：キー
    - **jsonSourceValueName**: value
- 出力要求：
  - **email**: "someone@example.com"
  - **displayName**:"Someone"
  - **membershipNum**:6353399
  - **アクティブ**: true
  - **birthdate**:1980-09-23T00:00:00Z

## <a name="getnumericclaimfromjson"></a>GetNumericClaimFromJson

JSON データから、指定した数値の (長) 要素を取得します。

| Item | TransformationClaimType | データ型 | メモ |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | inputJson | string | 項目を取得する要求変換で使用される ClaimTypes。 |
| InputParameter | claimToExtract | string | 抽出する JSON 要素の名前。 |
| OutputClaim | extractedClaim | long | この ClaimsTransformation が呼び出された後に生成される ClaimType　は、 _claimToExtract_ 入力パラメーターに指定された要素の値であります。 |

次の例では、JSON データから要求変換によって`id`要素が抽出されます。

```JSON
{
    "emailAddress": "someone@example.com", 
    "displayName": "Someone", 
    "id" : 6353399
}
```

```XML
<ClaimsTransformation Id="GetIdFromResponse" TransformationMethod="GetNumericClaimFromJson">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="exampleInputClaim" TransformationClaimType="inputJson" />
  </InputClaims>
  <InputParameters>
    <InputParameter Id="claimToExtract" DataType="string" Value="id" />
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="membershipId" TransformationClaimType="extractedClaim" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>例

- 入力要求:
  - **inputJson**: {"emailAddress": "someone@example.com", "displayName":"Someone", "id" :6353399}
- 入力パラメーター
    - **claimToExtract**:  id
- 出力要求: 
    - **extractedClaim**:6353399

## <a name="getsinglevaluefromjsonarray"></a>GetSingleValueFromJsonArray

JSON データの配列から最初の要素を取得します。

| Item | TransformationClaimType | データ型 | メモ |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | inputJsonClaim | string | JSON 配列から項目を取得する要求変換で使用される ClaimTypes。 |
| OutputClaim | extractedClaim | string | この ClaimsTransformation が呼び出された後に生成される ClaimTypeは、 JSON 配列の最初の要素です。 |

次の例では、JSON 配列から要求変換によって最初の要素（メールアドレス）が抽出されます `["someone@example.com", "Someone", 6353399]`。

```XML
<ClaimsTransformation Id="GetEmailFromJson" TransformationMethod="GetSingleValueFromJsonArray">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="userData" TransformationClaimType="inputJsonClaim" />
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="email" TransformationClaimType="extractedClaim" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>例

- 入力要求:
  - **inputJsonClaim**: ["someone@example.com", "Someone", 6353399]
- 出力要求： 
  - **extractedClaim**: someone@example.com

## <a name="xmlstringtojsonstring"></a>XmlStringToJsonString

XML データを JSON 形式に変換します。

| Item | TransformationClaimType | データ型 | メモ |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | Xml | string | データを XML から JSON 形式に変換する要求変換で使用される ClaimTypes。 |
| OutputClaim | json | string | この ClaimsTransformation が呼び出された後に生成される ClaimType は、JSON 形式のデータであります。 |

```XML
<ClaimsTransformation Id="ConvertXmlToJson" TransformationMethod="XmlStringToJsonString">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="intpuXML" TransformationClaimType="xml" />
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="outputJson" TransformationClaimType="json" />
  </OutputClaims>
</ClaimsTransformation>
```

次の例では、ClaimsTransformationにより、下記のXML データが JSON 形式に変換されます。

#### <a name="example"></a>例
入力要求：

```XML
<user>
  <name>Someone</name>
  <email>someone@example.com</email>
</user>
```

出力要求：

```JSON
{
  "user": {
    "name":"Someone",
    "email":"someone@example.com"
  }
}
```

