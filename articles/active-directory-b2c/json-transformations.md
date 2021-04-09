---
title: カスタム ポリシーの JSON 要求変換の例
titleSuffix: Azure AD B2C
description: Azure Active Directory B2C の Identity Experience Framework (IEF) スキーマの JSON 要求変換の例。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 03/04/2021
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: c5c8e21f2ce3f6907547bf1b2fe4681eb937864b
ms.sourcegitcommit: dac05f662ac353c1c7c5294399fca2a99b4f89c8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/04/2021
ms.locfileid: "102119877"
---
# <a name="json-claims-transformations"></a>JSON 要求変換

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

この記事では、Azure Active Directory B2C (Azure AD B2C) の Identity Experience Framework スキーマの JSON 要求変換の使用例を示します。 詳細については、「[ClaimsTransformations](claimstransformations.md)」を参照してください。

## <a name="generatejson"></a>GenerateJson

要求値か定数を使用して JSON 文字列を生成します。 ドット表記に続くパス文字列は、JSON 文字列にデータを挿入する場所を示す目的で使用されます。 ドットで分けられた後、整数は JSON 配列のインデックスとして解釈され、整数以外は JSON オブジェクトのインデックスとして解釈されます。

| Item | TransformationClaimType | データ型 | Notes |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | ドット表記に続く任意の文字列 | string | 要求値の挿入先となる JSON の JsonPath。 |
| InputParameter | ドット表記に続く任意の文字列 | string | 定数文字列値の挿入先となる JSON の JsonPath。 |
| OutputClaim | outputClaim | string | 生成された JSON 文字列。 |

### <a name="example-1"></a>例 1

次の例では、"email" と "otp" の要求値に基づいて JSON 文字列が生成され、さらに定数文字列が生成されます。

```xml
<ClaimsTransformation Id="GenerateRequestBody" TransformationMethod="GenerateJson">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="email" TransformationClaimType="personalizations.0.to.0.email" />
    <InputClaim ClaimTypeReferenceId="otp" TransformationClaimType="personalizations.0.dynamic_template_data.otp" />
  </InputClaims>
  <InputParameters>
    <InputParameter Id="template_id" DataType="string" Value="d-4c56ffb40fa648b1aa6822283df94f60"/>
    <InputParameter Id="from.email" DataType="string" Value="service@contoso.com"/>
    <InputParameter Id="personalizations.0.subject" DataType="string" Value="Contoso account email verification code"/>
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="requestBody" TransformationClaimType="outputClaim"/>
  </OutputClaims>
</ClaimsTransformation>
```

次の要求変換では、SendGrid (サードパーティ電子メール プロバイダー) に送信される要求の本文となる JSON 文字列要求が出力されます。 JSON オブジェクトの構造は、InputClaims の InputParameters と TransformationClaimTypes というドット表記で ID により定義されます。 ドット表記の数値は配列を意味します。 値は、InputClaims の値と InputParameters の "Value" プロパティから取得されます。

- 入力要求:
  - **電子メール**、変換要求の種類 **personalizations.0.to.0.email**: "someone@example.com"
  - **otp**、変換要求の種類 **personalizations.0.dynamic_template_data.otp** "346349"
- 入力パラメーター:
  - **template_id**: "d-4c56ffb40fa648b1aa6822283df94f60"
  - **from.email**: "service@contoso.com"
  - **personalizations.0.subject** "Contoso account email verification code"
- 出力要求：
  - **requestBody**:JSON 値

```json
{
  "personalizations": [
    {
      "to": [
        {
          "email": "someone@example.com"
        }
      ],
      "dynamic_template_data": {
        "otp": "346349",
        "verify-email" : "someone@example.com"
      },
      "subject": "Contoso account email verification code"
    }
  ],
  "template_id": "d-989077fbba9746e89f3f6411f596fb96",
  "from": {
    "email": "service@contoso.com"
  }
}
```

### <a name="example-2"></a>例 2

次の例では、要求値に基づいて JSON 文字列が生成され、さらに定数文字列が生成されます。

```xml
<ClaimsTransformation Id="GenerateRequestBody" TransformationMethod="GenerateJson">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="email" TransformationClaimType="customerEntity.email" />
    <InputClaim ClaimTypeReferenceId="objectId" TransformationClaimType="customerEntity.userObjectId" />
    <InputClaim ClaimTypeReferenceId="givenName" TransformationClaimType="customerEntity.firstName" />
    <InputClaim ClaimTypeReferenceId="surname" TransformationClaimType="customerEntity.lastName" />
  </InputClaims>
  <InputParameters>
    <InputParameter Id="customerEntity.role.name" DataType="string" Value="Administrator"/>
    <InputParameter Id="customerEntity.role.id" DataType="long" Value="1"/>
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="requestBody" TransformationClaimType="outputClaim"/>
  </OutputClaims>
</ClaimsTransformation>
```

次の要求変換を使用すると、REST API に送信される要求の本文となる JSON 文字列要求が出力されます。 JSON オブジェクトの構造は、InputClaims の InputParameters と TransformationClaimTypes というドット表記で ID により定義されます。 値は、InputClaims の値と InputParameters の "Value" プロパティから取得されます。

- 入力要求:
  - **電子メール**、変換要求の種類 **customerEntity.email**: "john.s@contoso.com"
  - **objectId**、変換要求の種類 **customerEntity.userObjectId** "01234567-89ab-cdef-0123-456789abcdef"
  - **givenName**、変換要求の種類 **customerEntity.firstName** "John"
  - **surname**、変換要求の種類 **customerEntity.lastName** "Smith"
- 入力パラメーター:
  - **customerEntity.role.name**:"Administrator"
  - **customerEntity.role.id** 1
- 出力要求：
  - **requestBody**:JSON 値

```json
{
   "customerEntity":{
      "email":"john.s@contoso.com",
      "userObjectId":"01234567-89ab-cdef-0123-456789abcdef",
      "firstName":"John",
      "lastName":"Smith",
      "role":{
         "name":"Administrator",
         "id": 1
      }
   }
}
```

## <a name="getclaimfromjson"></a>GetClaimFromJson

JSON データから、指定された要素を取得します。

| Item | TransformationClaimType | データ型 | Notes |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | inputJson | string | 項目を取得する要求変換で使用される ClaimTypes。 |
| InputParameter | claimToExtract | string | 抽出する JSON 要素の名前。 |
| OutputClaim | extractedClaim | string | このclaims transformation が呼び出された後に生成される ClaimType は、 _claimToExtract_ 入力パラメーターに指定された要素の値です。 |

次の例では、JSON データから要求変換によって`emailAddress`要素が抽出されます。`{"emailAddress": "someone@example.com", "displayName": "Someone"}`

```xml
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
- 出力要求:
  - **extractedClaim**: someone@example.com


## <a name="getclaimsfromjsonarray"></a>GetClaimsFromJsonArray

Json データから、指定された要素の一覧を取得します。

| Item | TransformationClaimType | データ型 | Notes |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | jsonSourceClaim | string | 項目を取得する要求変換で使用される ClaimTypes。 |
| InputParameter | errorOnMissingClaims | boolean | 要求の 1 つが不足している場合は、エラーをスローするかどうかを指定します。 |
| InputParameter | includeEmptyClaims | string | 空の要求を含めるかどうかを指定します。 |
| InputParameter | jsonSourceKeyName | string | 要素のキー名 |
| InputParameter | jsonSourceValueName | string | 要素の値の名前 |
| OutputClaim | コレクション | 文字列、int、ブール値、および datetime |抽出する要求のリスト。 要求の名前は、_jsonSourceClaim_ に指定されている入力要求の名前と同じでなければなりません。 |

次の例では、要求変換によって、次の要求が JSON データから抽出されます。 email (文字列)、displayName (文字列)、membershipNum (int)、アクティブ (ブール値) 、および birthdate (datetime)。

```json
[{"key":"email","value":"someone@example.com"}, {"key":"displayName","value":"Someone"}, {"key":"membershipNum","value":6353399}, {"key":"active","value":true}, {"key":"birthdate","value":"1980-09-23T00:00:00Z"}]
```

```xml
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

- 入力要求:
  - **jsonSourceClaim**: [{「キー」:"email"、"value":"someone@example.com"}、{「キー」:"displayName"、"value":"Someone"}、{「キー」:"membershipNum"、"value": 6353399}、{「キー」:"active"、"value": true}、{「キー」:"birthdate"、"value":"1980-09-23T00:00:00Z"}]
- 入力パラメーター:
    - **errorOnMissingClaims**：false
    - **includeEmptyClaims**：false
    - **jsonSourceKeyName**：キー
    - **jsonSourceValueName**: value
- 出力要求:
  - **email**: "someone@example.com"
  - **displayName**:"Someone"
  - **membershipNum**:6353399
  - **アクティブ**: true
  - **birthdate**:1980-09-23T00:00:00Z

## <a name="getnumericclaimfromjson"></a>GetNumericClaimFromJson

JSON データから、指定した数値の (長) 要素を取得します。

| Item | TransformationClaimType | データ型 | Notes |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | inputJson | string | 項目を取得する要求変換で使用される ClaimTypes。 |
| InputParameter | claimToExtract | string | 抽出する JSON 要素の名前。 |
| OutputClaim | extractedClaim | long | この ClaimsTransformation が呼び出された後に生成される ClaimType　は、 _claimToExtract_ 入力パラメーターに指定された要素の値であります。 |

次の例では、JSON データから要求変換によって`id`要素が抽出されます。

```json
{
    "emailAddress": "someone@example.com",
    "displayName": "Someone",
    "id" : 6353399
}
```

```xml
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

## <a name="getsingleitemfromjson"></a>GetSingleItemFromJson

JSON データから最初の要素を取得します。

| Item | TransformationClaimType | データ型 | Notes |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | inputJson | string | JSON データから項目を取得する要求変換で使用される ClaimTypes。 |
| OutputClaim | key | string | JSON 内の最初の要素キー。 |
| OutputClaim | value | string | JSON 内の最初の要素値。 |

次の例では、JSON データから要求変換によって最初の要素 (名) が抽出されます。

```xml
<ClaimsTransformation Id="GetGivenNameFromResponse" TransformationMethod="GetSingleItemFromJson">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="json" TransformationClaimType="inputJson" />
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="givenNameKey" TransformationClaimType="key" />
    <OutputClaim ClaimTypeReferenceId="givenName" TransformationClaimType="value" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>例

- 入力要求:
  - **inputJson**: {"givenName":"Emilty", "lastName":"Smith"}
- 出力要求:
  - **key**: givenName
  - **value**:Emilty


## <a name="getsinglevaluefromjsonarray"></a>GetSingleValueFromJsonArray

JSON データの配列から最初の要素を取得します。

| Item | TransformationClaimType | データ型 | Notes |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | inputJsonClaim | string | JSON 配列から項目を取得する要求変換で使用される ClaimTypes。 |
| OutputClaim | extractedClaim | string | この ClaimsTransformation が呼び出された後に生成される ClaimTypeは、 JSON 配列の最初の要素です。 |

次の例では、JSON 配列から要求変換によって最初の要素（メールアドレス）が抽出されます `["someone@example.com", "Someone", 6353399]`。

```xml
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
- 出力要求:
  - **extractedClaim**: someone@example.com

## <a name="xmlstringtojsonstring"></a>XmlStringToJsonString

XML データを JSON 形式に変換します。

| Item | TransformationClaimType | データ型 | Notes |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | xml | string | データを XML から JSON 形式に変換する要求変換で使用される ClaimTypes。 |
| OutputClaim | json | string | この ClaimsTransformation が呼び出された後に生成される ClaimType は、JSON 形式のデータであります。 |

```xml
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

```xml
<user>
  <name>Someone</name>
  <email>someone@example.com</email>
</user>
```

出力要求：

```json
{
  "user": {
    "name":"Someone",
    "email":"someone@example.com"
  }
}
```


