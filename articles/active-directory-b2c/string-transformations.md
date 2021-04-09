---
title: カスタム ポリシーの文字列要求変換の例
titleSuffix: Azure AD B2C
description: Azure Active Directory B2C の Identity Experience Framework (IEF) スキーマの文字列要求変換の例
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 03/08/2021
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 85574b7d33af6d9abfe25f5af4d811255f08ce4b
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102452239"
---
# <a name="string-claims-transformations"></a>文字列要求変換

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

この記事では、Azure Active Directory B2C (Azure AD B2C) の Identity Experience Framework スキーマの文字列要求変換の使用例を示します。 詳細については、「[ClaimsTransformations](claimstransformations.md)」を参照してください。

## <a name="assertstringclaimsareequal"></a>AssertStringClaimsAreEqual

2 つの要求を比較し、それらが指定された比較 inputClaim1、inputClaim2 および stringComparison によれば等しくない場合は、例外をスローします。

| Item | TransformationClaimType | データ型 | Notes |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | inputClaim1 | string | 比較する最初の要求の種類。 |
| InputClaim | inputClaim2 | string | 比較する 2 番目の要求の種類。 |
| InputParameter | stringComparison | string | 文字列比較で、次のいずれかの値です。序数、OrdinalIgnoreCase。 |

**AssertStringClaimsAreEqual** 要求変換は常に、[セルフアサート技術プロファイル](self-asserted-technical-profile.md)によって呼び出される [検証技術プロファイル](validation-technical-profile.md) (つまり [DisplayConrtol](display-controls.md)) から実行されます。 ユーザーに表示されるエラー メッセージは、セルフアサート技術プロファイルの `UserMessageIfClaimsTransformationStringsAreNotEqual` メタデータによって制御されます。 エラー メッセージは、[ローカライズ](localization-string-ids.md#claims-transformations-error-messages)できます。


![AssertStringClaimsAreEqual の実行](./media/string-transformations/assert-execution.png)

この要求変換を使用して、2 つの ClaimTypes が同じ値を持っていることを確認できます。 そうでない場合は、エラー メッセージがスローされます。 次の例では、**strongAuthenticationEmailAddress** ClaimType が **email** ClaimType と等しいことを確認します。 そうでない場合は、エラー メッセージがスローされます。

```xml
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
```xml
<TechnicalProfile Id="login-NonInteractive">
  ...
  <OutputClaimsTransformations>
    <OutputClaimsTransformation ReferenceId="AssertEmailAndStrongAuthenticationEmailAddressAreEqual" />
  </OutputClaimsTransformations>
</TechnicalProfile>
```

セルフアサート技術プロファイルによって **login-NonInteractive** 検証技術プロファイルが呼び出されます。

```xml
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

| Item | TransformationClaimType | データ型 | Notes |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | inputClaim1 | string | 変更する ClaimType。 |
| InputParameter | toCase | string | 次のいずれかの値を指定できます。`LOWER` または `UPPER`。 |
| OutputClaim | outputClaim | string | この要求変換が呼び出された後に生成される ClaimType。 |

この要求変換を使用して、任意の文字列 ClaimType を小文字または大文字に変更します。

```xml
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

変換で指定された入力パラメーターから文字列要求を作成します。

| Item | TransformationClaimType | データ型 | Notes |
|----- | ----------------------- | --------- | ----- |
| InputParameter | value | string | 設定する文字列。 この入力パラメーターは、[文字列要求変換式](string-transformations.md#string-claim-transformations-expressions)をサポートします。 |
| OutputClaim | createdClaim | string | この要求変換が呼び出された後に生成される ClaimType は、入力パラメーターに指定された値で呼び出されています。 |

この要求変換を使用して、文字列 ClaimType 値を設定します。

```xml
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

## <a name="copyclaimifpredicatematch"></a>CopyClaimIfPredicateMatch

入力要求の値が出力要求の述語と一致する場合は、要求の値を別の値にコピーします。 

| Item | TransformationClaimType | データ型 | Notes |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | inputClaim | string | コピーする要求の種類。 |
| OutputClaim | outputClaim | string | この要求変換が呼び出された後に生成される要求の種類。 入力要求の値は、この要求の述語に対してチェックされます。 |

次の例では、signInName が電話番号の場合にのみ、signInName 要求の値が phoneNumber 要求にコピーされます。 完全なサンプルについては、[電話番号または電子メールによるサインイン](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/blob/master/scenarios/phone-number-passwordless/Phone_Email_Base.xml)に関するスターター パック ポリシーを参照してください。

```xml
<ClaimsTransformation Id="SetPhoneNumberIfPredicateMatch" TransformationMethod="CopyClaimIfPredicateMatch">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="signInName" TransformationClaimType="inputClaim" />
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="phoneNumber" TransformationClaimType="outputClaim" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example-1"></a>例 1

- 入力要求:
    - **inputClaim**: bob@contoso.com
- 出力要求:
    - **outputclaim**: 出力要求は元の値から変更されません。

### <a name="example-2"></a>例 2

- 入力要求:
    - **inputClaim**: +11234567890
- 出力要求:
    - **outputClaim**: +11234567890

## <a name="compareclaims"></a>CompareClaims

文字列要求が相互に等しいかどうかを確認します。 結果は、`true` または `false` の値を含む新しい ClaimType ブール値です。

| Item | TransformationClaimType | データ型 | Notes |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | inputClaim1 | string | 比較する最初の要求の種類。 |
| InputClaim | inputClaim2 | string | 比較する 2 番目の要求の種類。 |
| InputParameter | operator | string | 指定できる値: `EQUAL` または `NOT EQUAL`。 |
| InputParameter | ignoreCase | boolean | この比較が比較対象の文字列の大文字と小文字を無視するかどうかを指定します。 |
| OutputClaim | outputClaim | boolean | この要求変換が呼び出された後に生成される ClaimType。 |

この要求変換を使用して、要求が別の要求と等しいかどうかをチェックします。 たとえば、以下の要求変換は **email** 要求の値が **Verified.Email** 要求と等しいかどうかをチェックします。

```xml
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

| Item | TransformationClaimType | データ型 | Notes |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | inputClaim1 | string | 比較する要求の種類。 |
| InputParameter | operator | string | 指定できる値: `EQUAL` または `NOT EQUAL`。 |
| InputParameter | compareTo | string | 文字列比較で、次のいずれかの値です。序数、OrdinalIgnoreCase。 |
| InputParameter | ignoreCase | boolean | この比較が比較対象の文字列の大文字と小文字を無視するかどうかを指定します。 |
| OutputClaim | outputClaim | boolean | この要求変換が呼び出された後に生成される ClaimType。 |

この要求変換を使用して、要求が、指定した値と等しいかどうかをチェックできます。 たとえば、以下の要求変換は **termsOfUseConsentVersion** 要求の値が `v1` と等しいかどうかをチェックします。

```xml
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

| Item | TransformationClaimType | データ型 | Notes |
| ---- | ----------------------- | --------- | ----- |
| InputParameter | randomGeneratorType | string | 生成されるランダムな値、`GUID` (グローバル固有 ID) または `INTEGER` (数字) を指定します。 |
| InputParameter | stringFormat | string | [省略可能] ランダムな値を書式設定します。 |
| InputParameter | base64 | boolean | [省略可能] ランダムな値を base64 に変換します。 文字列形式が適用されている場合、文字列形式の後の値は base64 にエンコードされます。 |
| InputParameter | maximumNumber | INT | [省略可能] `INTEGER` randomGeneratorType のみです。 最大数を指定します。 |
| InputParameter | seed  | INT | [省略可能] `INTEGER` randomGeneratorType のみです。 乱数値のシードを指定します。 注: 同じシードは、同じ乱数のシーケンスを生成します。 |
| OutputClaim | outputClaim | string | この要求変換が呼び出された後に生成される ClaimTypes。 ランダムな値。 |

次の例はグローバルな一意の ID を生成します。 この要求変換を使用してランダムな UPN (ユーザー プリンシパル名) を作成します。

```xml
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

```xml
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
    - **maximumNumber**:1000
    - **stringFormat**:OTP_{0}
    - **base64**: false
- 出力要求:
    - **outputClaim**:OTP_853


## <a name="formatlocalizedstring"></a>FormatLocalizedString

指定されたローカライズ済み書式指定文字列に従って、複数の要求の書式を設定します。 この変換では､C# の `String.Format` メソッドを使用します。


| Item | TransformationClaimType | データ型 | Notes |
| ---- | ----------------------- | --------- | ----- |
| InputClaims |  |string | 文字列形式 {0}、{1}、{2} パラメーターとして機能する一連の入力要求。 |
| InputParameter | stringFormatId | string |  [ローカライズされた文字列](localization.md)の `StringId`。   |
| OutputClaim | outputClaim | string | この要求変換が呼び出された後に生成される ClaimType。 |

> [!NOTE]
> 文字列形式の最大許容サイズは 4000 です。

FormatLocalizedString 要求変換を使用するには:

1. [ローカライズ文字列](localization.md)を定義し、それを[セルフアサート技術プロファイル](self-asserted-technical-profile.md)に関連付けます。
1. `LocalizedString` 要素の `ElementType` は `FormatLocalizedStringTransformationClaimType` に設定する必要があります。
1. `StringId` は、ユーザーが定義する一意識別子であり、後ほど要求変換 `stringFormatId` で使用します。
1. 要求変換で、ローカライズされた文字列を使用して設定される要求の一覧を指定します。 次に、`stringFormatId` を ローカライズされた文字列要素の `StringId` に設定します。 
1. [セルフアサート技術プロファイル](self-asserted-technical-profile.md)、または[表示コントロール](display-controls.md)の入力または出力要求変換で、要求変換への参照を付けます。


次の例では、アカウントが既にディレクトリに存在する場合にエラー メッセージが生成されます。 この例では、英語 (既定値) とスペイン語のローカライズされた文字列が定義されます。

```xml
<Localization Enabled="true">
  <SupportedLanguages DefaultLanguage="en" MergeBehavior="Append">
    <SupportedLanguage>en</SupportedLanguage>
    <SupportedLanguage>es</SupportedLanguage>
   </SupportedLanguages>

  <LocalizedResources Id="api.localaccountsignup.en">
    <LocalizedStrings>
      <LocalizedString ElementType="FormatLocalizedStringTransformationClaimType" StringId="ResponseMessge_EmailExists">The email '{0}' is already an account in this organization. Click Next to sign in with that account.</LocalizedString>
      </LocalizedStrings>
    </LocalizedResources>
  <LocalizedResources Id="api.localaccountsignup.es">
    <LocalizedStrings>
      <LocalizedString ElementType="FormatLocalizedStringTransformationClaimType" StringId="ResponseMessge_EmailExists">Este correo electrónico "{0}" ya es una cuenta de esta organización. Haga clic en Siguiente para iniciar sesión con esa cuenta.</LocalizedString>
    </LocalizedStrings>
  </LocalizedResources>
</Localization>
```

要求変換では、ローカライズされた文字列に基づいて応答メッセージが作成されます。 メッセージには、ローカライズされた文字列 *ResponseMessge_EmailExists* に埋め込まれているユーザーのメールアドレスが含まれます。

```xml
<ClaimsTransformation Id="SetResponseMessageForEmailAlreadyExists" TransformationMethod="FormatLocalizedString">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="email" />
  </InputClaims>
  <InputParameters>
    <InputParameter Id="stringFormatId" DataType="string" Value="ResponseMessge_EmailExists" />
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="responseMsg" TransformationClaimType="outputClaim" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>例

- 入力要求:
    - **inputClaim**: sarah@contoso.com
- 入力パラメーター:
    - **stringFormat**: ResponseMessge_EmailExists
- 出力要求:
  - **Outputclaim**: 電子メール 'sarah@contoso.com' は既にこの組織のアカウントです。 [次へ] をクリックして、そのアカウントでサインインします。


## <a name="formatstringclaim"></a>FormatStringClaim

指定された書式設定文字列に従って要求の書式を設定します。 この変換では､C# の `String.Format` メソッドを使用します。

| Item | TransformationClaimType | データ型 | Notes |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | inputClaim |string |文字列形式 {0} パラメーターとして機能する ClaimType。 |
| InputParameter | stringFormat | string | {0} パラメーターを含む文字列の形式。 この入力パラメーターは、[文字列要求変換式](string-transformations.md#string-claim-transformations-expressions)をサポートします。  |
| OutputClaim | outputClaim | string | この要求変換が呼び出された後に生成される ClaimType。 |

> [!NOTE]
> 文字列形式の最大許容サイズは 4000 です。

この要求変換を使用して 1 つのパラメーター {0} を持つ任意の文字列の書式を設定します。 次の例では、**userPrincipalName** を作成します。 `Facebook-OAUTH` などのすべてのソーシャル ID プロバイダーの技術プロファイルは、**CreateUserPrincipalName** を呼び出して **userPrincipalName** を生成します。

```xml
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

指定された書式設定文字列に従って、2 つの要求の書式を設定します。 この変換では､C# の `String.Format` メソッドを使用します。

| Item | TransformationClaimType | データ型 | Notes |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | inputClaim |string | 文字列形式 {0} パラメーターとして機能する ClaimType。 |
| InputClaim | inputClaim | string | 文字列形式 {1} パラメーターとして機能する ClaimType。 |
| InputParameter | stringFormat | string | {0} および {1} パラメーターを含む文字列の形式。 この入力パラメーターは、[文字列要求変換式](string-transformations.md#string-claim-transformations-expressions)をサポートします。   |
| OutputClaim | outputClaim | string | この要求変換が呼び出された後に生成される ClaimType。 |

> [!NOTE]
> 文字列形式の最大許容サイズは 4000 です。

この要求変換を使用して 2 つのパラメーター {0} および {1} を持つ任意の文字列の書式を設定します。 次の例では、指定した形式で **displayName** を作成します。

```xml
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

## <a name="getlocalizedstringstransformation"></a>GetLocalizedStringsTransformation

ローカライズされた文字列を要求にコピーします。

| Item | TransformationClaimType | データ型 | Notes |
| ---- | ----------------------- | --------- | ----- |
| OutputClaim | ローカライズされた文字列の名前 | string | この要求変換が呼び出された後に生成される要求の種類の一覧。 |

GetLocalizedStringsTransformation 要求変換を使用する場合は、次の操作を行います。

1. [ローカライズ文字列](localization.md)を定義し、それを[セルフアサート技術プロファイル](self-asserted-technical-profile.md)に関連付けます。
1. `LocalizedString` 要素の `ElementType` は `GetLocalizedStringsTransformationClaimType` に設定する必要があります。
1. `StringId` はユーザーが定義する一意識別子であり、後ほど要求変換で使用します。
1. 要求変換で、ローカライズされた文字列を使用して設定される要求の一覧を指定します。 `ClaimTypeReferenceId` は、ポリシー内の ClaimsSchema セクションに既に定義されている ClaimType への参照です。 `TransformationClaimType` は、`LocalizedString` 要素の `StringId` で定義されているローカライズされた文字列の名前です。
1. [セルフアサート技術プロファイル](self-asserted-technical-profile.md)、または[表示コントロール](display-controls.md)の入力または出力要求変換で、要求変換への参照を付けます。

![GetLocalizedStringsTransformation](./media/string-transformations/get-localized-strings-transformation.png)

次の例では、ローカライズされた文字列から電子メールの件名、本文、コード メッセージ、電子メールの署名を検索します。 これらの要求は、後でカスタム メール確認テンプレートによって使用されます。

英語 (既定値) とスペイン語のローカライズされた文字列を定義します。

```xml
<Localization Enabled="true">
  <SupportedLanguages DefaultLanguage="en" MergeBehavior="Append">
    <SupportedLanguage>en</SupportedLanguage>
    <SupportedLanguage>es</SupportedLanguage>
   </SupportedLanguages>

  <LocalizedResources Id="api.localaccountsignup.en">
    <LocalizedStrings>
      <LocalizedString ElementType="GetLocalizedStringsTransformationClaimType" StringId="email_subject">Contoso account email verification code</LocalizedString>
      <LocalizedString ElementType="GetLocalizedStringsTransformationClaimType" StringId="email_message">Thanks for verifying your account!</LocalizedString>
      <LocalizedString ElementType="GetLocalizedStringsTransformationClaimType" StringId="email_code">Your code is</LocalizedString>
      <LocalizedString ElementType="GetLocalizedStringsTransformationClaimType" StringId="email_signature">Sincerely</LocalizedString>
     </LocalizedStrings>
   </LocalizedResources>
   <LocalizedResources Id="api.localaccountsignup.es">
     <LocalizedStrings>
      <LocalizedString ElementType="GetLocalizedStringsTransformationClaimType" StringId="email_subject">Código de verificación del correo electrónico de la cuenta de Contoso</LocalizedString>
      <LocalizedString ElementType="GetLocalizedStringsTransformationClaimType" StringId="email_message">Gracias por comprobar la cuenta de </LocalizedString>
      <LocalizedString ElementType="GetLocalizedStringsTransformationClaimType" StringId="email_code">Su código es</LocalizedString>
      <LocalizedString ElementType="GetLocalizedStringsTransformationClaimType" StringId="email_signature">Atentamente</LocalizedString>
    </LocalizedStrings>
  </LocalizedResources>
</Localization>
```

要求変換では、`StringId` *email_subject* の値を使用して要求の種類 *subject* の値が設定されます。

```xml
<ClaimsTransformation Id="GetLocalizedStringsForEmail" TransformationMethod="GetLocalizedStringsTransformation">
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="subject" TransformationClaimType="email_subject" />
    <OutputClaim ClaimTypeReferenceId="message" TransformationClaimType="email_message" />
    <OutputClaim ClaimTypeReferenceId="codeIntro" TransformationClaimType="email_code" />
    <OutputClaim ClaimTypeReferenceId="signature" TransformationClaimType="email_signature" />
   </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>例

- 出力要求:
  - **subject**: Contoso アカウントの電子メール確認コード
  - **message**:アカウントの確認が完了しました!
  - **codeIntro**:お客様のコード
  - **signature**:ご利用ありがとうございます


## <a name="getmappedvaluefromlocalizedcollection"></a>GetMappedValueFromLocalizedCollection

要求 **Restriction** コレクションからの項目の検索。

| Item | TransformationClaimType | データ型 | Notes |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | mapFromClaim | string | **Restriction** コレクションがある **restrictionValueClaim** 要求から検索するテキストを含む要求。  |
| OutputClaim | restrictionValueClaim | string | **Restriction** コレクションが含まれる要求。 要求変換が呼び出された後には、この要求の値には、選択した項目の値が含まれます。 |

次の例では、エラーのキーに基づいて、エラー メッセージの説明を検索します。 **responseMsg** 要求には、エンド ユーザーに提示する、または証明書利用者に送信するエラー メッセージのコレクションが含まれています。

```xml
<ClaimType Id="responseMsg">
  <DisplayName>Error message: </DisplayName>
  <DataType>string</DataType>
  <UserInputType>Paragraph</UserInputType>
  <Restriction>
    <Enumeration Text="B2C_V1_90001" Value="You cannot sign in because you are a minor" />
    <Enumeration Text="B2C_V1_90002" Value="This action can only be performed by gold members" />
    <Enumeration Text="B2C_V1_90003" Value="You have not been enabled for this operation" />
  </Restriction>
</ClaimType>
```
要求変換では、項目のテキストを検索し、その値を返します。 制限が `<LocalizedCollection>` を使用してローカライズされている場合、要求変換はローカライズされた値を返します。

```xml
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

| Item | TransformationClaimType | データ型 | Notes |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | inputParameterId | string | 参照値が含まれる要求 |
| InputParameter | |string | inputParameters のコレクションです。 |
| InputParameter | errorOnFailedLookup | boolean | 一致参照がない場合にエラーが返されるかどうかを制御します。 |
| OutputClaim | inputParameterId | string | この要求変換が呼び出された後に生成される ClaimTypes。 一致する `Id` の値。 |

次の例では、inpuParameters コレクションの 1 つからドメイン名を検索します。 要求変換では、識別子内のドメイン名を検索し、その値 (アプリケーション ID) を返します。

```xml
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
    - **outputClaim**:    c7026f88-4299-4cdb-965d-3f166464b8a9

`errorOnFailedLookup` 入力パラメーターが `true` に設定されると、**LookupValue** 要求変換は常に、[セルフアサート技術プロファイル](self-asserted-technical-profile.md)によって呼び出される [検証技術プロファイル](validation-technical-profile.md) (つまり [DisplayConrtol](display-controls.md)) から実行されます。 ユーザーに表示されるエラー メッセージは、セルフアサート技術プロファイルの `LookupNotFound` メタデータによって制御されます。

![AssertStringClaimsAreEqual の実行](./media/string-transformations/assert-execution.png)

次の例では、inpuParameters コレクションの 1 つからドメイン名を検索します。 要求変換では、識別子内のドメイン名を検索し、その値 (アプリケーション ID) を返します。つまり、エラー メッセージが生成されます。

```xml
 <ClaimsTransformation Id="DomainToClientId" TransformationMethod="LookupValue">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="domainName" TransformationClaimType="inputParameterId" />
  </InputClaims>
  <InputParameters>
    <InputParameter Id="contoso.com" DataType="string" Value="13c15f79-8fb1-4e29-a6c9-be0d36ff19f1" />
    <InputParameter Id="microsoft.com" DataType="string" Value="0213308f-17cb-4398-b97e-01da7bd4804e" />
    <InputParameter Id="test.com" DataType="string" Value="c7026f88-4299-4cdb-965d-3f166464b8a9" />
    <InputParameter Id="errorOnFailedLookup" DataType="boolean" Value="true" />
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="domainAppId" TransformationClaimType="outputClaim" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>例

- 入力要求:
    - **inputParameterId**: live.com
- 入力パラメーター:
    - **contoso.com**:13c15f79-8fb1-4e29-a6c9-be0d36ff19f1
    - **microsoft.com**:0213308f-17cb-4398-b97e-01da7bd4804e
    - **test.com**: c7026f88-4299-4cdb-965d-3f166464b8a9
    - **errorOnFailedLookup**: true
- エラー:
    - 一連の入力パラメーターの ID に入力要求値との一致が見つかりませんでした。また、errorOnFailedLookup は true になっています。


## <a name="nullclaim"></a>NullClaim

特定の要求の値を消去します。

| Item | TransformationClaimType | データ型 | Notes |
| ---- | ----------------------- | --------- | ----- |
| OutputClaim | claim_to_null | string | 要求の値を Null に設定します。 |

この要求変換を使用して、不要なデータを要求プロパティ バッグから削除し、セッション Cookie のサイズを縮小します。 次の例では、`TermsOfService` 要求の種類の値を削除します。

```xml
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

| Item | TransformationClaimType | データ型 | Notes |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | emailAddress | string | 電子メール アドレスが含まれている ClaimType。 |
| OutputClaim | domain | string | この要求変換が呼び出された後に生成される ClaimType - ドメイン。 |

この要求変換は、ユーザーの @ 記号の後のドメイン名を解析するために使用します。 次の要求変換は、**email** 要求からドメイン名を解析する方法を示しています。

```xml
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

## <a name="setclaimsifregexmatch"></a>SetClaimsIfRegexMatch

文字列の要求の `claimToMatch` と `matchTo` の入力パラメーターが等しいことをチェックし、出力要求を `outputClaimIfMatched` 入力パラメーターにある値で設定します。同時に結果の出力要求を比較します。これは比較の結果に基づいて `true` または `false` として設定されます。

| Item | TransformationClaimType | データ型 | Notes |
| ---- | ----------------------- | --------- | ----- |
| inputClaim | claimToMatch | string | 比較する要求の種類。 |
| InputParameter | matchTo | string | 照合する正規表現。 |
| InputParameter | outputClaimIfMatched | string | 文字列が等しい場合に設定する値。 |
| InputParameter | extractGroups | boolean | [省略可能] Regex の一致でグループ値を抽出するかどうかを指定します。 指定できる値: `true` または `false` (既定値)。 | 
| OutputClaim | outputClaim | string | 正規表現が一致する場合は、この出力要求に `outputClaimIfMatched` 入力パラメーターの値が含まれます。 一致するものがない場合は null になります。 |
| OutputClaim | regexCompareResultClaim | boolean | 正規表現で結果の出力要求の種類が照合されます。これは照合の結果に基づいて、`true` または `false` として設定されます。 |
| OutputClaim| 要求の名前| string | extractGroups 入力パラメーターが true に設定されている場合は、この要求変換が呼び出された後に生成される要求の種類の一覧です。 claimType の名前は、Regex グループ名と一致する必要があります。 | 

### <a name="example-1"></a>例 1

電話番号の正規表現パターンに基づいて、指定された電話番号が有効かどうかをチェックします。

```xml
<ClaimsTransformation Id="SetIsPhoneRegex" TransformationMethod="SetClaimsIfRegexMatch">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="phone" TransformationClaimType="claimToMatch" />
  </InputClaims>
  <InputParameters>
    <InputParameter Id="matchTo" DataType="string" Value="^[0-9]{4,16}$" />
    <InputParameter Id="outputClaimIfMatched" DataType="string" Value="isPhone" />
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="validationResult" TransformationClaimType="outputClaim" />
    <OutputClaim ClaimTypeReferenceId="isPhoneBoolean" TransformationClaimType="regexCompareResultClaim" />
  </OutputClaims>
</ClaimsTransformation>
```

- 入力要求:
    - **claimToMatch**:"64854114520"
- 入力パラメーター:
    - **matchTo**: "^[0-9]{4,16}$"
    - **outputClaimIfMatched**:  "isPhone"
- 出力要求:
    - **outputClaim**: "isPhone"
    - **regexCompareResultClaim**: true

### <a name="example-2"></a>例 2

指定された電子メール アドレスが有効かどうかを確認し、電子メールの別名を返します。

```xml
<ClaimsTransformation Id="GetAliasFromEmail" TransformationMethod="SetClaimsIfRegexMatch">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="email" TransformationClaimType="claimToMatch" />
  </InputClaims>
  <InputParameters>
    <InputParameter Id="matchTo" DataType="string" Value="(?&lt;mailAlias&gt;.*)@(.*)$" />
    <InputParameter Id="outputClaimIfMatched" DataType="string" Value="isEmail" />
    <InputParameter Id="extractGroups" DataType="boolean" Value="true" />
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="validationResult" TransformationClaimType="outputClaim" />
    <OutputClaim ClaimTypeReferenceId="isEmailString" TransformationClaimType="regexCompareResultClaim" />
    <OutputClaim ClaimTypeReferenceId="mailAlias" />
  </OutputClaims>
</ClaimsTransformation>
```

- 入力要求:
    - **claimToMatch**: "emily@contoso.com"
- 入力パラメーター:
    - **matchTo**: `(?&lt;mailAlias&gt;.*)@(.*)$`
    - **outputClaimIfMatched**:  "isEmail"
    - **extractGroups**: true
- 出力要求:
    - **outputClaim**: "isEmail"
    - **regexCompareResultClaim**: true
    - **mailAlias**: emily
    
## <a name="setclaimsifstringsareequal"></a>SetClaimsIfStringsAreEqual

文字列の要求と `matchTo` 入力パラメーターが等しいことをチェックし、出力要求を `stringMatchMsg` および `stringMatchMsgCode` 入力パラメーターにある値で設定します。同時に結果の出力要求を比較します。これは比較の結果に基づいて `true` または `false` として設定されます。

| Item | TransformationClaimType | データ型 | Notes |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | inputClaim | string | 比較する要求の種類。 |
| InputParameter | matchTo | string | `inputClaim` と比較する文字列。 |
| InputParameter | stringComparison | string | 指定できる値: `Ordinal` または `OrdinalIgnoreCase`。 |
| InputParameter | stringMatchMsg | string | 文字列が等しい場合に設定する最初の値。 |
| InputParameter | stringMatchMsgCode | string | 文字列が等しい場合に設定する 2 番目の値。 |
| OutputClaim | outputClaim1 | string | 文字列が等しい場合は、この出力要求に `stringMatchMsg` 入力パラメーターの値が含まれます。 |
| OutputClaim | outputClaim2 | string | 文字列が等しい場合は、この出力要求に `stringMatchMsgCode` 入力パラメーターの値が含まれます。 |
| OutputClaim | stringCompareResultClaim | boolean | 比較の結果の出力要求の種類。これは比較の結果に基づいて、`true` または `false` として設定されます。 |

この要求変換を使用して、要求が、指定した値と等しいかどうかをチェックできます。 たとえば、以下の要求変換は **termsOfUseConsentVersion** 要求の値が `v1` と等しいかどうかをチェックします。 等しい場合、値を `v2` に変更します。

```xml
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

| Item | TransformationClaimType | データ型 | Notes |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | claimToMatch | string | 比較する要求の種類。 |
| InputParameter | matchTo | string | inputClaim と比較する文字列。 |
| InputParameter | stringComparison | string | 指定できる値: `Ordinal` または `OrdinalIgnoreCase`。 |
| InputParameter | outputClaimIfMatched | string | 文字列が等しい場合に設定する値。 |
| OutputClaim | outputClaim | string | 文字列が等しい場合は、この出力要求に `outputClaimIfMatched` 入力パラメーターの値が含まれます。 または、文字列が一致しない場合は null です。 |
| OutputClaim | stringCompareResultClaim | boolean | 比較の結果の出力要求の種類。これは比較の結果に基づいて、`true` または `false` として設定されます。 |

たとえば、以下の要求変換は **ageGroup** 要求の値が `Minor` と等しいかどうかをチェックします。 等しい場合は、値を `B2C_V1_90001` に返します。

```xml
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


## <a name="stringcontains"></a>StringContains

指定した substring が入力要求内に出現しているかどうかが確認されます。 結果は、`true` または `false` の値を含む新しい ClaimType ブール値です。 値パラメーターがこの文字列内に存在する場合は `true`、それ以外の場合は `false`。

| Item | TransformationClaimType | データ型 | Notes |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | inputClaim | string | 検索する要求の種類。 |
|InputParameter|contains|string|検索する値。|
|InputParameter|ignoreCase|string|この比較で比較対象の文字列の大文字と小文字を無視するかどうかを指定します。|
| OutputClaim | outputClaim | string | この ClaimsTransformation が呼び出された後に生成される ClaimType。 入力要求内で substring が出現した場合のブール値インジケーター。 |

この要求変換は、文字列要求の種類に substring が含まれているかどうかをチェックする場合に使用します。 次の例では、`roles` 文字列要求の種類に **admin** の値が含まれているかどうかをチェックしています。

```xml
<ClaimsTransformation Id="CheckIsAdmin" TransformationMethod="StringContains">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="roles" TransformationClaimType="inputClaim"/>
  </InputClaims>
  <InputParameters>
    <InputParameter  Id="contains" DataType="string" Value="admin"/>
    <InputParameter  Id="ignoreCase" DataType="string" Value="true"/>
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="isAdmin" TransformationClaimType="outputClaim"/>
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>例

- 入力要求:
    - **inputClaim**:"Admin, Approver, Editor"
- 入力パラメーター:
    - **contains**: "admin,"
    - **ignoreCase**: true
- 出力要求:
    - **outputClaim**: true

## <a name="stringsubstring"></a>StringSubstring

指定した位置にある文字から始まる文字列要求の種類の一部が抽出され、指定した文字数が返されます。

| Item | TransformationClaimType | データ型 | Notes |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | inputClaim | string | 文字列を含む要求の種類。 |
| InputParameter | startIndex | INT | このインスタンス内の substring の 0 から始まる開始文字位置。 |
| InputParameter | length | INT | substring の文字数。 |
| OutputClaim | outputClaim | boolean | このインスタンスの startIndex から始まる長さの substring と等価な文字列。startIndex がこのインスタンスの長さと等しく、length がゼロの場合は空になります。 |

たとえば、電話番号の国または地域のプレフィックスを取得します。


```xml
<ClaimsTransformation Id="GetPhonePrefix" TransformationMethod="StringSubstring">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="phoneNumber" TransformationClaimType="inputClaim" />
  </InputClaims>
<InputParameters>
  <InputParameter Id="startIndex" DataType="int" Value="0" />
  <InputParameter Id="length" DataType="int" Value="2" />
</InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="phonePrefix" TransformationClaimType="outputClaim" />
  </OutputClaims>
</ClaimsTransformation>
```
### <a name="example"></a>例

- 入力要求:
    - **inputClaim**: "+1644114520"
- 入力パラメーター:
    - **startIndex**: 0
    - **length**: 2
- 出力要求:
    - **outputClaim**: "+1"

## <a name="stringreplace"></a>StringReplace

要求の種類の文字列で指定した値が検索され、新しい要求の種類の文字列が返されます。ここでは、現在の文字列内の指定した文字列のすべての出現箇所が、別の指定した文字列に置き換えられます。

| Item | TransformationClaimType | データ型 | Notes |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | inputClaim | string | 文字列を含む要求の種類。 |
| InputParameter | oldValue | string | 検索対象の文字列。 |
| InputParameter | newValue | string | 出現するすべての `oldValue` を置換する文字列 |
| OutputClaim | outputClaim | boolean | oldValue のすべてのインスタンスが newValue で置き換えられることを除いて、現在の文字列と等価な文字列。 oldValue が現在のインスタンス内に見つからない場合、メソッドにより現在のインスタンスが変更せずに返されます。 |

たとえば、電話番号の `-` 文字が削除されて正規化されます


```xml
<ClaimsTransformation Id="NormalizePhoneNumber" TransformationMethod="StringReplace">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="phoneNumber" TransformationClaimType="inputClaim" />
  </InputClaims>
<InputParameters>
  <InputParameter Id="oldValue" DataType="string" Value="-" />
  <InputParameter Id="newValue" DataType="string" Value="" />
</InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="phoneNumber" TransformationClaimType="outputClaim" />
  </OutputClaims>
</ClaimsTransformation>
```
### <a name="example"></a>例

- 入力要求:
    - **inputClaim**: "+164-411-452-054"
- 入力パラメーター:
    - **oldValue**: "-"
    - **newValue**:  ""
- 出力要求:
    - **outputClaim**: "+164411452054"

## <a name="stringjoin"></a>StringJoin

指定した文字列コレクションの要求の種類の要素を連結します。各要素またはメンバーの間には、指定した区切り記号が使用されます。

| Item | TransformationClaimType | データ型 | Notes |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | inputClaim | stringCollection | 連結する文字列を格納しているコレクション。 |
| InputParameter | delimiter | string | 区切り記号として使用する文字列 (コンマ `,` など)。 |
| OutputClaim | outputClaim | string | `inputClaim` 文字列コレクションのメンバーからなる、`delimiter` 入力パラメーターで区切られた文字列。 |

次の例では、ユーザー ロールの文字列コレクションが取得され、それがコンマ区切り文字列に変換されています。 このメソッドを使用して、Azure AD ユーザー アカウントに文字列コレクションを格納することができます。 後でディレクトリからアカウントを読み取るときに、`StringSplit` を使用してコンマ区切り文字列を文字列コレクションに戻します。

```xml
<ClaimsTransformation Id="ConvertRolesStringCollectionToCommaDelimiterString" TransformationMethod="StringJoin">
  <InputClaims>
   <InputClaim ClaimTypeReferenceId="roles" TransformationClaimType="inputClaim" />
  </InputClaims>
  <InputParameters>
    <InputParameter DataType="string" Id="delimiter" Value="," />
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="rolesCommaDelimiterConverted" TransformationClaimType="outputClaim" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>例

- 入力要求:
  - **inputClaim**: [ "Admin", "Author", "Reader" ]
- 入力パラメーター:
  - **delimiter**: ","
- 出力要求:
  - **outputClaim**:"Admin,Author,Reader"


## <a name="stringsplit"></a>StringSplit

指定された文字列の要素で区切られた、このインスタンスの substring を格納する文字列配列が返されます。

| Item | TransformationClaimType | データ型 | Notes |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | inputClaim | string | 分割する substring を含む文字列要求の種類。 |
| InputParameter | delimiter | string | 区切り記号として使用する文字列 (コンマ `,` など)。 |
| OutputClaim | outputClaim | stringCollection | `delimiter` 入力パラメーターで区切られた文字列の要素に substring を格納する文字列コレクション。 |

次の例では、ユーザー ロールのコンマ区切り文字列が取得され、それが文字列コレクションに変換されています。

```xml
<ClaimsTransformation Id="ConvertRolesToStringCollection" TransformationMethod="StringSplit">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="rolesCommaDelimiter" TransformationClaimType="inputClaim" />
  </InputClaims>
  <InputParameters>
  <InputParameter DataType="string" Id="delimiter" Value="," />
    </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="roles" TransformationClaimType="outputClaim" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>例

- 入力要求:
  - **inputClaim**:"Admin,Author,Reader"
- 入力パラメーター:
  - **delimiter**: ","
- 出力要求:
  - **outputClaim**: [ "Admin", "Author", "Reader" ]

## <a name="string-claim-transformations-expressions"></a>文字列要求変換式
Azure AD B2C のカスタム ポリシーにおける要求変換式は、テナント ID と技術プロファイル ID についてのコンテキスト情報を提供します。

  | 式 | 説明 | 例 |
 | ----- | ----------- | --------|
 | `{TechnicalProfileId}` | 技術プロファイル ID の名前。 | Facebook-OAUTH |
 | `{RelyingPartyTenantId}` | 証明書利用者ポリシーのテナント ID。 | your-tenant.onmicrosoft.com |
 | `{TrustFrameworkTenantId}` | 信頼フレームワークのテナント ID。 | your-tenant.onmicrosoft.com |
