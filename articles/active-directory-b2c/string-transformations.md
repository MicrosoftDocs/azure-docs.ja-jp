---
title: Azure Active Directory B2C の Identity Experience Framework スキーマの文字列要求変換の例 | Microsoft Docs
description: Azure Active Directory B2C の Identity Experience Framework スキーマの文字列要求変換の例
services: active-directory-b2c
author: davidmu1
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/10/2018
ms.author: davidmu
ms.subservice: B2C
ms.openlocfilehash: 32acee78b1aadbe5a461de6ea4475cf28503c325
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2019
ms.locfileid: "58113029"
---
# <a name="string-claims-transformations"></a>文字列要求変換

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

この記事では、Azure Active Directory (Azure AD) B2C の Identity Experience Framework スキーマの文字列要求変換の使用例を示します。 詳細については、「[ClaimsTransformations](claimstransformations.md)」を参照してください。

## <a name="assertstringclaimsareequal"></a>AssertStringClaimsAreEqual 

2 つの要求を比較し、それらが指定された比較 inputClaim1、inputClaim2 および stringComparison によれば等しくない場合は、例外をスローします。

| Item | TransformationClaimType | データ型 | メモ |
| ---- | ----------------------- | --------- | ----- |
| inputClaim | inputClaim1 | 文字列 | 比較する最初の要求の種類。 |
| inputClaim | inputClaim2 | 文字列 | 比較する 2 番目の要求の種類。 |
| InputParameter | stringComparison | 文字列 | 文字列比較で、次のいずれかの値です。序数、OrdinalIgnoreCase。 |

**AssertStringClaimsAreEqual** 要求変換は、[セルフアサート技術プロファイル](self-asserted-technical-profile.md)によって呼び出される[検証技術プロファイル](validation-technical-profile.md)から常に実行する必要があります。 **UserMessageIfClaimsTransformationStringsAreNotEqual** セルフアサート技術プロファイル メタデータにより、ユーザーに表示されるエラー メッセージが制御されます。

![AssertStringClaimsAreEqual の実行](./media/string-transformations/assert-execution.png)

この要求変換を使用して、2 つの ClaimTypes が同じ値を持っていることを確認できます。 そうでない場合は、エラー メッセージがスローされます。 次の例では、**strongAuthenticationEmailAddress** ClaimType が **email** ClaimType と等しいことを確認します。 そうでない場合は、エラー メッセージがスローされます。 

```XML
<ClaimsTransformation Id="AssertEmailAndStrongAuthenticationEmailAddressAreEqual" TransformationMethod="AssertStringClaimsAreEqual">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="strongAuthenticationEmailAddress" TransformationClaimType="inputClaim1" />
    <InputClaim ClaimTypeReferenceId="email" TransformationClaimType="inputClaim2" />
  </InputClaims>
  <InputParameters>
    <InputParameter Id="stringComparison" DataType="string" Value="ordinalIgnoreCase" />
  </InputParameters>
</ClaimsTransformation>
```


**login-NonInteractive** 検証技術プロファイルは、**AssertEmailAndStrongAuthenticationEmailAddressAreEqual** 要求変換を呼び出します。
```XML
<TechnicalProfile Id="login-NonInteractive">
  ...
  <OutputClaimsTransformations>
    <OutputClaimsTransformation ReferenceId="AssertEmailAndStrongAuthenticationEmailAddressAreEqual" />
  </OutputClaimsTransformations>
</TechnicalProfile>
```

セルフアサート技術プロファイルによって **login-NonInteractive** 検証技術プロファイルが呼び出されます。

```XML
<TechnicalProfile Id="SelfAsserted-LocalAccountSignin-Email">
  <Metadata>
    <Item Key="UserMessageIfClaimsTransformationStringsAreNotEqual">Custom error message the email addresses you provided are not the same.</Item>
  </Metadata>
  <ValidationTechnicalProfiles>
    <ValidationTechnicalProfile ReferenceId="login-NonInteractive" />
  </ValidationTechnicalProfiles>
</TechnicalProfile>
```

### <a name="example"></a>例

- 入力要求:
  - **inputClaim1**: someone@contoso.com
  - **inputClaim2**: someone@outlook.com
    - 入力パラメーター:
  - **stringComparison**:  ordinalIgnoreCase
- 結果:エラーがスローされます

## <a name="changecase"></a>ChangeCase 

指定された要求の大文字または小文字を演算子に従って変更します。

| Item | TransformationClaimType | データ型 | メモ |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | inputClaim1 | 文字列 | 変更する ClaimType。 |
| InputParameter | toCase | 文字列 | 次のいずれかの値を指定できます。`LOWER` または `UPPER`。 |
| OutputClaim | outputClaim | 文字列 | この要求変換が呼び出された後に生成される ClaimType。 |

この要求変換を使用して、任意の文字列 ClaimType を小文字または大文字に変更します。  

```XML
<ClaimsTransformation Id="ChangeToLower" TransformationMethod="ChangeCase">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="email" TransformationClaimType="inputClaim1" />
  </InputClaims>
<InputParameters>
  <InputParameter Id="toCase" DataType="string" Value="LOWER" />
</InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="email" TransformationClaimType="outputClaim" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>例

- 入力要求:
  - **email**: SomeOne@contoso.com
- 入力パラメーター:
    - **toCase**:LOWER
- 出力要求:
  - **email**: someone@contoso.com

## <a name="createstringclaim"></a>CreateStringClaim 

ポリシーで指定された入力パラメーターから文字列要求を作成します。

| Item | TransformationClaimType | データ型 | メモ |
|----- | ----------------------- | --------- | ----- |
| InputParameter | value | 文字列 | 設定する文字列 |
| OutputClaim | createdClaim | 文字列 | この要求変換が呼び出された後に生成される ClaimType は、入力パラメーターに指定された値で呼び出されています。 |

この要求変換を使用して、文字列 ClaimType 値を設定します。

```XML
<ClaimsTransformation Id="CreateTermsOfService" TransformationMethod="CreateStringClaim">
  <InputParameters>
    <InputParameter Id="value" DataType="string" Value="Contoso terms of service..." />
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="TOS" TransformationClaimType="createdClaim" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>例

- 入力パラメーター:
    - **value**:Contoso terms of service...
- 出力要求:
    - **createdClaim**:TOS ClaimType には「Contoso サービス利用規約...」の値が含まれています。

## <a name="compareclaims"></a>CompareClaims

文字列要求が相互に等しいかどうかを確認します。 結果は、`true` または `false` の値を含む新しい ClaimType ブール値です。

| Item | TransformationClaimType | データ型 | メモ |
| ---- | ----------------------- | --------- | ----- |
| inputClaim | inputClaim1 | 文字列 | 比較する最初の要求の種類。 |
| inputClaim | inputClaim2 | 文字列 | 比較する 2 番目の要求の種類。 |
| InputParameter | operator | 文字列 | 指定できる値: `EQUAL` または `NOT EQUAL`。 |
| InputParameter | ignoreCase | ブール値 | この比較が比較対象の文字列の大文字と小文字を無視するかどうかを指定します。 |
| OutputClaim | outputClaim | ブール値 | この要求変換が呼び出された後に生成される ClaimType。 |

この要求変換を使用して、要求が別の要求と等しいかどうかをチェックします。 たとえば、以下の要求変換は **email** 要求の値が **Verified.Email** 要求と等しいかどうかをチェックします。

```XML
<ClaimsTransformation Id="CheckEmail" TransformationMethod="CompareClaims">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="Email" TransformationClaimType="inputClaim1" />
    <InputClaim ClaimTypeReferenceId="Verified.Email" TransformationClaimType="inputClaim2" />
  </InputClaims>
  <InputParameters>
    <InputParameter Id="operator" DataType="string" Value="NOT EQUAL" />
    <InputParameter Id="ignoreCase" DataType="string" Value="true" />
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="SameEmailAddress" TransformationClaimType="outputClaim" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>例

- 入力要求:
  - **inputClaim1**: someone@contoso.com
  - **inputClaim2**: someone@outlook.com
- 入力パラメーター:
    - **operator**:NOT EQUAL
    - **ignoreCase**: true
- 出力要求:
    - **outputClaim**: true

## <a name="compareclaimtovalue"></a>CompareClaimToValue

要求の値が入力パラメーターの値と等しいかどうかを確認します。

| Item | TransformationClaimType | データ型 | メモ |
| ---- | ----------------------- | --------- | ----- |
| inputClaim | inputClaim1 | 文字列 | 比較する要求の種類。 |
| InputParameter | operator | 文字列 | 指定できる値: `EQUAL` または `NOT EQUAL`。 |
| InputParameter | compareTo | 文字列 | 文字列比較で、次のいずれかの値です。序数、OrdinalIgnoreCase。 |
| InputParameter | ignoreCase | ブール値 | この比較が比較対象の文字列の大文字と小文字を無視するかどうかを指定します。 |
| OutputClaim | outputClaim | ブール値 | この要求変換が呼び出された後に生成される ClaimType。 |

この要求変換を使用して、要求が、指定した値と等しいかどうかをチェックできます。 たとえば、以下の要求変換は **termsOfUseConsentVersion** 要求の値が `v1` と等しいかどうかをチェックします。

```XML
<ClaimsTransformation Id="IsTermsOfUseConsentRequiredForVersion" TransformationMethod="CompareClaimToValue">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="termsOfUseConsentVersion" TransformationClaimType="inputClaim1" />
  </InputClaims>
  <InputParameters>
    <InputParameter Id="compareTo" DataType="string" Value="V1" />
    <InputParameter Id="operator" DataType="string" Value="not equal" />
    <InputParameter Id="ignoreCase" DataType="string" Value="true" />
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="termsOfUseConsentRequired" TransformationClaimType="outputClaim" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>例
- 入力要求:
    - **inputClaim1**: v1
- 入力パラメーター:
    - **compareTo**:V1
    - **operator**:EQUAL 
    - **ignoreCase**:  true
- 出力要求:
    - **outputClaim**: true

## <a name="createrandomstring"></a>CreateRandomString

乱数ジェネレーターを使用してランダムな文字列を作成します。 乱数ジェネレーターの種類が `integer` の場合、必要に応じてシード パラメーターと最大数を指定できます。 省略可能な文字列形式パラメーターを使用して、出力を書式設定できます。省略可能な base64 パラメーターは、出力が base64 でエンコードされた randomGeneratorType [guid, integer] outputClaim (文字列) であるかどうかを指定します。

| Item | TransformationClaimType | データ型 | メモ |
| ---- | ----------------------- | --------- | ----- |
| InputParameter | randomGeneratorType | 文字列 | 生成されるランダムな値、`GUID` (グローバル固有 ID) または `INTEGER` (数字) を指定します。 |
| InputParameter | stringFormat | 文字列 | [省略可能] ランダムな値を書式設定します。 |
| InputParameter | base64 | ブール値 | [省略可能] ランダムな値を base64 に変換します。 文字列形式が適用されている場合、文字列形式の後の値は base64 にエンコードされます。 |
| InputParameter | maximumNumber | int | [省略可能] `INTEGER` randomGeneratorType のみです。 最大数を指定します。 |
| InputParameter | seed  | int | [省略可能] `INTEGER` randomGeneratorType のみです。 乱数値のシードを指定します。 注: 同じシードは、同じ乱数のシーケンスを生成します。 |
| OutputClaim | outputClaim | 文字列 | この要求変換が呼び出された後に生成される ClaimTypes。 ランダムな値。 |

次の例はグローバルな一意の ID を生成します。 この要求変換を使用してランダムな UPN (ユーザー プリンシパル名) を作成します。

```XML
<ClaimsTransformation Id="CreateRandomUPNUserName" TransformationMethod="CreateRandomString">
  <InputParameters>
    <InputParameter Id="randomGeneratorType" DataType="string" Value="GUID" />
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="upnUserName" TransformationClaimType="outputClaim" />
  </OutputClaims>
</ClaimsTransformation>
```
### <a name="example"></a>例

- 入力パラメーター:
    - **randomGeneratorType**:GUID
- 出力要求: 
    - **outputClaim**: bc8bedd2-aaa3-411e-bdee-2f1810b73dfc

次の例では、0 ~ 1000 の範囲の整数のランダムな値を生成します。 値は OTP_{ランダム値} に書式設定されます。

```XML
<ClaimsTransformation Id="SetRandomNumber" TransformationMethod="CreateRandomString">
  <InputParameters>
    <InputParameter Id="randomGeneratorType" DataType="string" Value="INTEGER" />
    <InputParameter Id="maximumNumber" DataType="int" Value="1000" />
    <InputParameter Id="stringFormat" DataType="string" Value="OTP_{0}" />
    <InputParameter Id="base64" DataType="boolean" Value="false" />
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="randomNumber" TransformationClaimType="outputClaim" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>例

- 入力パラメーター:
    - **randomGeneratorType**:INTEGER
    - **maximumNumber**:1,000
    - **stringFormat**:OTP_{0}
    - **base64**: false
- 出力要求: 
    - **outputClaim**:OTP_853


## <a name="formatstringclaim"></a>FormatStringClaim

指定された書式設定文字列に従って要求の書式を設定します。 この変換では､C# の `String.Format` メソッドを使用します。

| Item | TransformationClaimType | データ型 | メモ |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | inputClaim |文字列 |文字列形式 {0} パラメーターとして機能する ClaimType。 |
| InputParameter | stringFormat | 文字列 | {0} パラメーターを含む文字列の形式。 |
| OutputClaim | outputClaim | 文字列 | この要求変換が呼び出された後に生成される ClaimType。 |

この要求変換を使用して 1 つのパラメーター {0} を持つ任意の文字列の書式を設定します。 次の例では、**userPrincipalName** を作成します。 `Facebook-OAUTH` などのすべてのソーシャル ID プロバイダーの技術プロファイルは、**CreateUserPrincipalName** を呼び出して **userPrincipalName** を生成します。   

```XML
<ClaimsTransformation Id="CreateUserPrincipalName" TransformationMethod="FormatStringClaim">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="upnUserName" TransformationClaimType="inputClaim" />
  </InputClaims>
  <InputParameters>
    <InputParameter Id="stringFormat" DataType="string" Value="cpim_{0}@{RelyingPartyTenantId}" />
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="userPrincipalName" TransformationClaimType="outputClaim" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>例

- 入力要求:
    - **inputClaim**:5164db16-3eee-4629-bfda-dcc3326790e9
- 入力パラメーター:
    - **stringFormat**:  cpim_{0}@{RelyingPartyTenantId}
- 出力要求:
  - **outputClaim**: cpim_5164db16-3eee-4629-bfda-dcc3326790e9@b2cdemo.onmicrosoft.com

## <a name="formatstringmultipleclaims"></a>FormatStringMultipleClaims

指定された書式設定文字列に従って、2 つの要求の書式を設定します。 この変換は C# **String.Format** メソッドを使用します。

| Item | TransformationClaimType | データ型 | メモ |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | inputClaim |文字列 | 文字列形式 {0} パラメーターとして機能する ClaimType。 |
| InputClaim | inputClaim | 文字列 | 文字列形式 {1} パラメーターとして機能する ClaimType。 |
| InputParameter | stringFormat | 文字列 | {0} および {1} パラメーターを含む文字列の形式。 |
| OutputClaim | outputClaim | 文字列 | この要求変換が呼び出された後に生成される ClaimType。 |

この要求変換を使用して 2 つのパラメーター {0} および {1} を持つ任意の文字列の書式を設定します。 次の例では、指定した形式で **displayName** を作成します。

```XML
<ClaimsTransformation Id="CreateDisplayNameFromFirstNameAndLastName" TransformationMethod="FormatStringMultipleClaims">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="givenName" TransformationClaimType="inputClaim1" />
    <InputClaim ClaimTypeReferenceId="surName" TransformationClaimType="inputClaim2" />
  </InputClaims>
  <InputParameters>
    <InputParameter Id="stringFormat" DataType="string" Value="{0} {1}" />
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="displayName" TransformationClaimType="outputClaim" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>例

- 入力要求:
    - **inputClaim1**:Joe
    - **inputClaim2**:Fernando
- 入力パラメーター:
    - **stringFormat**: {0} {1}
- 出力要求:
    - **outputClaim**:Joe Fernando

## <a name="getmappedvaluefromlocalizedcollection"></a>GetMappedValueFromLocalizedCollection

要求 **Restriction** コレクションからの項目の検索。

| Item | TransformationClaimType | データ型 | メモ |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | mapFromClaim | 文字列 | **Restriction** コレクションがある **restrictionValueClaim** 要求から検索するテキストを含む要求。  |
| OutputClaim | restrictionValueClaim | 文字列 | **Restriction** コレクションが含まれる要求。 要求変換が呼び出された後には、この要求の値には、選択した項目の値が含まれます。 |

次の例では、エラーのキーに基づいて、エラー メッセージの説明を検索します。 **responseMsg** 要求には、エンド ユーザーに提示する、または証明書利用者に送信するエラー メッセージのコレクションが含まれています。

```XML
<ClaimType Id="responseMsg">
  <DisplayName>Error message: </DisplayName>
  <DataType>string</DataType>
  <UserInputType>Paragraph</UserInputType>
  <Restriction>
    <Enumeration Text="B2C_V1_90001" Value="You cant sign in because you are a minor" />
    <Enumeration Text="B2C_V1_90002" Value="This action can only be performed by gold members" />
    <Enumeration Text="B2C_V1_90003" Value="You have not been enabled for this operation" />
  </Restriction>
</ClaimType>
```
要求変換では、項目のテキストを検索し、その値を返します。 制限が `<LocalizedCollection>` を使用してローカライズされている場合、要求変換はローカライズされた値を返します。

```XML
<ClaimsTransformation Id="GetResponseMsgMappedToResponseCode" TransformationMethod="GetMappedValueFromLocalizedCollection">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="responseCode" TransformationClaimType="mapFromClaim" />
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="responseMsg" TransformationClaimType="restrictionValueClaim" />         
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>例

- 入力要求:
    - **mapFromClaim**:B2C_V1_90001
- 出力要求:
    - **restrictionValueClaim**:マイナーであるのでサインインできません。

## <a name="lookupvalue"></a>LookupValue

別の要求の値に基づいて、値の一覧から要求の値を検索します。

| Item | TransformationClaimType | データ型 | メモ |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | inputParameterId | 文字列 | 参照値が含まれる要求 |
| InputParameter | |文字列 | inputParameters のコレクションです。 |
| InputParameter | errorOnFailedLookup | ブール値 | 一致参照がない場合にエラーが返されるかどうかを制御します。 |
| OutputClaim | inputParameterId | 文字列 | この要求変換が呼び出された後に生成される ClaimTypes。 一致する ID の値 |

次の例では、inpuParameters コレクションの 1 つからドメイン名を検索します。 要求変換では、識別子内のドメイン名を検索し、その値 (アプリケーション ID) を返します。

```XML
 <ClaimsTransformation Id="DomainToClientId" TransformationMethod="LookupValue">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="domainName" TransformationClaimType="inputParameterId" />
  </InputClaims>
  <InputParameters>
    <InputParameter Id="contoso.com" DataType="string" Value="13c15f79-8fb1-4e29-a6c9-be0d36ff19f1" />
    <InputParameter Id="microsoft.com" DataType="string" Value="0213308f-17cb-4398-b97e-01da7bd4804e" />
    <InputParameter Id="test.com" DataType="string" Value="c7026f88-4299-4cdb-965d-3f166464b8a9" />
    <InputParameter Id="errorOnFailedLookup" DataType="boolean" Value="false" />
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="domainAppId" TransformationClaimType="outputClaim" />
  </OutputClaims>
</ClaimsTransformation> 
```

### <a name="example"></a>例

- 入力要求:
    - **inputParameterId**: test.com
- 入力パラメーター:
    - **contoso.com**:13c15f79-8fb1-4e29-a6c9-be0d36ff19f1
    - **microsoft.com**:0213308f-17cb-4398-b97e-01da7bd4804e
    - **test.com**: c7026f88-4299-4cdb-965d-3f166464b8a9
    - **errorOnFailedLookup**: false
- 出力要求:
    - **outputClaim**:  c7026f88-4299-4cdb-965d-3f166464b8a9

## <a name="nullclaim"></a>NullClaim

特定の要求の値を消去します。

| Item | TransformationClaimType | データ型 | メモ |
| ---- | ----------------------- | --------- | ----- |
| OutputClaim | claim_to_null | 文字列 | 値が NULL になる要求。 |

この要求変換は、要求のプロパティ バッグから不要なデータを削除するために使用します。 そのため、セッション cookie は小さくなります。 次の例では、`TermsOfService` 要求の種類の値を削除します。

```XML
<ClaimsTransformation Id="SetTOSToNull" TransformationMethod="NullClaim">
  <OutputClaims>
  <OutputClaim ClaimTypeReferenceId="TermsOfService" TransformationClaimType="claim_to_null" />
  </OutputClaims>
</ClaimsTransformation>
```

- 入力要求:
    - **outputClaim**:Welcome to Contoso App. この Web サイトを継続して参照および使用する場合には、次の使用条件に従い、制約を受けることに同意していただくものとします。
- 出力要求:
    - **outputClaim**:NULL

## <a name="parsedomain"></a>ParseDomain

電子メール アドレスのドメイン部分を取得します。

| Item | TransformationClaimType | データ型 | メモ |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | emailAddress | 文字列 | 電子メール アドレスが含まれている ClaimType。 |
| OutputClaim | domain | 文字列 | この要求変換が呼び出された後に生成される ClaimType - ドメイン。 |

この要求変換は、ユーザーの @ 記号の後のドメイン名を解析するために使用します。 これは、監査データから個人を特定できる情報 (PII) を削除するために役立てることができます。 次の要求変換は、**email** 要求からドメイン名を解析する方法を示しています。

```XML
<ClaimsTransformation Id="SetDomainName" TransformationMethod="ParseDomain">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="email" TransformationClaimType="emailAddress" />
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="domainName" TransformationClaimType="domain" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>例

- 入力要求:
  - **emailAddress**: joe@outlook.com
- 出力要求:
    - **domain**: outlook.com

## <a name="setclaimsifstringsareequal"></a>SetClaimsIfStringsAreEqual

文字列の要求と `matchTo` 入力パラメーターが等しいことをチェックし、出力要求を `stringMatchMsg` および `stringMatchMsgCode` 入力パラメーターにある値で設定します。同時に結果の出力要求を比較します。これは比較の結果に基づいて `true` または `false` として設定されます。

| Item | TransformationClaimType | データ型 | メモ |
| ---- | ----------------------- | --------- | ----- |
| inputClaim | inputClaim | 文字列 | 比較する要求の種類。 |
| InputParameter | matchTo | 文字列 | `inputClaim` と比較する文字列。 |
| InputParameter | stringComparison | 文字列 | 指定できる値: `Ordinal` または `OrdinalIgnoreCase`。 |
| InputParameter | stringMatchMsg | 文字列 | 文字列が等しい場合に設定する最初の値。 |
| InputParameter | stringMatchMsgCode | 文字列 | 文字列が等しい場合に設定する 2 番目の値。 |
| OutputClaim | outputClaim1 | 文字列 | 文字列が等しい場合は、この出力要求に `stringMatchMsg` 入力パラメーターの値が含まれます。 |
| OutputClaim | outputClaim2 | 文字列 | 文字列が等しい場合は、この出力要求に `stringMatchMsgCode` 入力パラメーターの値が含まれます。 |
| OutputClaim | stringCompareResultClaim | ブール値 | 比較の結果の出力要求の種類。これは比較の結果に基づいて、`true` または `false` として設定されます。 |

この要求変換を使用して、要求が、指定した値と等しいかどうかをチェックできます。 たとえば、以下の要求変換は **termsOfUseConsentVersion** 要求の値が `v1` と等しいかどうかをチェックします。 等しい場合、値を `v2` に変更します。 

```XML
<ClaimsTransformation Id="CheckTheTOS" TransformationMethod="SetClaimsIfStringsAreEqual">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="termsOfUseConsentVersion" TransformationClaimType="inputClaim" />
  </InputClaims>
  <InputParameters>
    <InputParameter Id="matchTo" DataType="string" Value="v1" />
    <InputParameter Id="stringComparison" DataType="string" Value="ordinalIgnoreCase" />
    <InputParameter Id="stringMatchMsg" DataType="string" Value="B2C_V1_90005" />
    <InputParameter Id="stringMatchMsgCode" DataType="string" Value="The TOS is upgraded to v2" />
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="termsOfUseConsentVersion" TransformationClaimType="outputClaim1" />
    <OutputClaim ClaimTypeReferenceId="termsOfUseConsentVersionUpgradeCode" TransformationClaimType="outputClaim2" />
    <OutputClaim ClaimTypeReferenceId="termsOfUseConsentVersionUpgradeResult" TransformationClaimType="stringCompareResultClaim" />
  </OutputClaims>
</ClaimsTransformation>
```
### <a name="example"></a>例

- 入力要求:
    - **inputClaim**: v1
- 入力パラメーター:
    - **matchTo**:V1
    - **stringComparison**: ordinalIgnoreCase 
    - **stringMatchMsg**:B2C_V1_90005
    - **stringMatchMsgCode**:TOS は v2 にアップグレードされます
- 出力要求:
    - **outputClaim1**:B2C_V1_90005
    - **outputClaim2**:TOS は v2 にアップグレードされます
    - **stringCompareResultClaim**: true

## <a name="setclaimsifstringsmatch"></a>SetClaimsIfStringsMatch

文字列の要求と `matchTo` 入力パラメーターが等しいことをチェックし、出力要求を `outputClaimIfMatched` 入力パラメーターにある値で設定します。同時に結果の出力要求を比較します。これは比較の結果に基づいて `true` または `false` として設定されます。

| Item | TransformationClaimType | データ型 | メモ |
| ---- | ----------------------- | --------- | ----- |
| inputClaim | claimToMatch | 文字列 | 比較する要求の種類。 |
| InputParameter | matchTo | 文字列 | inputClaim と比較する文字列。 |
| InputParameter | stringComparison | 文字列 | 指定できる値: `Ordinal` または `OrdinalIgnoreCase`。 |
| InputParameter | outputClaimIfMatched | 文字列 | 文字列が等しい場合に設定する値。 |
| OutputClaim | outputClaim | 文字列 | 文字列が等しい場合は、この出力要求に `outputClaimIfMatched` 入力パラメーターの値が含まれます。 または、文字列が一致しない場合は null です。 |
| OutputClaim | stringCompareResultClaim | ブール値 | 比較の結果の出力要求の種類。これは比較の結果に基づいて、`true` または `false` として設定されます。 |

たとえば、以下の要求変換は **ageGroup** 要求の値が `Minor` と等しいかどうかをチェックします。 等しい場合は、値を `B2C_V1_90001` に返します。 

```XML
<ClaimsTransformation Id="SetIsMinor" TransformationMethod="SetClaimsIfStringsMatch">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="ageGroup" TransformationClaimType="claimToMatch" />
  </InputClaims>
  <InputParameters>
    <InputParameter Id="matchTo" DataType="string" Value="Minor" />
    <InputParameter Id="stringComparison" DataType="string" Value="ordinalIgnoreCase" />
    <InputParameter Id="outputClaimIfMatched" DataType="string" Value="B2C_V1_90001" />
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="isMinor" TransformationClaimType="outputClaim" />
    <OutputClaim ClaimTypeReferenceId="isMinorResponseCode" TransformationClaimType="stringCompareResultClaim" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>例

- 入力要求:
    - **claimToMatch**:Minor
- 入力パラメーター:
    - **matchTo**:Minor
    - **stringComparison**: ordinalIgnoreCase 
    - **outputClaimIfMatched**:B2C_V1_90001
- 出力要求:
    - **isMinorResponseCode**:B2C_V1_90001
    - **isMinor**: true

