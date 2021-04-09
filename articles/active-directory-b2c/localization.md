---
title: ローカライズ - Azure Active Directory B2C
description: Azure Active Directory B2C でカスタム ポリシーの Localization 要素を指定します。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 03/08/2021
ms.author: mimart
ms.subservice: B2C
ms.custom: b2c-support
ms.openlocfilehash: 3a5afcd8c0ef0c31353cd2369ead332675c9877f
ms.sourcegitcommit: 6386854467e74d0745c281cc53621af3bb201920
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/08/2021
ms.locfileid: "102453123"
---
# <a name="localization-element"></a>Localization 要素

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

**Localization** 要素を使用すると、ユーザー体験に関するポリシーで複数のロケールや言語をサポートすることができます。 ポリシーでのローカライズのサポートにより、次のことが可能になります。

- ポリシーでサポートされている言語の明示的な一覧を設定し、既定の言語を選択する。
- 言語固有の文字列とコレクションを指定する。

```xml
<Localization Enabled="true">
  <SupportedLanguages DefaultLanguage="en" MergeBehavior="ReplaceAll">
    <SupportedLanguage>en</SupportedLanguage>
    <SupportedLanguage>es</SupportedLanguage>
  </SupportedLanguages>
  <LocalizedResources Id="api.localaccountsignup.en">
  <LocalizedResources Id="api.localaccountsignup.es">
  ...
```

**Localization** 要素には、次の属性が含まれています。

| 属性 | 必須 | 説明 |
| --------- | -------- | ----------- |
| Enabled | いいえ | 指定できる値: `true` または `false`。 |

**Localization** 要素には、次の XML 要素が含まれています。

| 要素 | 発生回数 | 説明 |
| ------- | ----------- | ----------- |
| SupportedLanguages | 1:n | サポートされている言語の一覧。 |
| LocalizedResources | 0:n | ローカライズされたリソースの一覧。 |

## <a name="supportedlanguages"></a>SupportedLanguages

**SupportedLanguages** 要素には、次の属性が含まれています。

| 属性 | 必須 | 説明 |
| --------- | -------- | ----------- |
| DefaultLanguage | はい | ローカライズされたリソースの既定値として使用される言語。 |
| MergeBehavior | いいえ | 同じ識別子を持つ親ポリシーに存在するすべての ClaimType と一緒にマージされる、値の列挙値。 基本ポリシーで指定された要求を上書きする場合は、この属性を使用します。 指定できる値: `Append`、`Prepend`、または `ReplaceAll`。 `Append` 値は、存在するデータのコレクションを、親ポリシーで指定したコレクションの末尾に追加する必要があることを指定します。 `Prepend` 値は、存在するデータのコレクションを、親ポリシーで指定したコレクションの前に追加する必要があることを指定します。 `ReplaceAll` 値は、親ポリシーで定義されているデータのコレクションを、現在のポリシーで定義されているデータを代わりに使用して無視する必要があることを指定します。 |

### <a name="supportedlanguages"></a>SupportedLanguages

**SupportedLanguages** 要素には、次の要素が含まれています。

| 要素 | 発生回数 | 説明 |
| ------- | ----------- | ----------- |
| SupportedLanguage | 1:n | 「RFC 5646 - 言語を識別するタグ」に従って、言語タグに適合する内容を表示します。 |

## <a name="localizedresources"></a>LocalizedResources

**LocalizedResources** 要素には、次の属性が含まれています。

| 属性 | 必須 | 説明 |
| --------- | -------- | ----------- |
| Id | はい | ローカライズされたリソースを一意に識別するために使用される識別子。 |

**LocalizedResources** 要素には、次の要素が含まれています。

| 要素 | 発生回数 | 説明 |
| ------- | ----------- | ----------- |
| LocalizedCollections | 0:n | さまざまなカルチャのコレクション全体を定義します。 1 つのコレクションに、さまざまなカルチャに対する異なる数の項目と異なる文字列を含めることができます。 コレクションの例として、要求の種類に表示される列挙が挙げられます。 たとえば、国/リージョン一覧がドロップダウンリストでユーザーに表示されます。 |
| LocalizedStrings | 0:n | さまざまなカルチャのすべての文字列 (コレクションに表示される文字列を除く) を定義します。 |

### <a name="localizedcollections"></a>LocalizedCollections

**LocalizedCollections** 要素には、次の要素が含まれています。

| 要素 | 発生回数 | 説明 |
| ------- | ----------- | ----------- |
| LocalizedCollection | 1:n | サポートされている言語の一覧。 |

#### <a name="localizedcollection"></a>LocalizedCollection

**LocalizedCollection** 要素には、次の属性が含まれています。

| 属性 | 必須 | 説明 |
| --------- | -------- | ----------- |
| ElementType | はい | ポリシー ファイル内の ClaimType 要素またはユーザー インターフェイス要素を参照します。 |
| ElementId | はい | **ElementType** が ClaimType に設定されている場合に使用される、ClaimsSchema セクションで定義済みの要求の種類への参照を含む文字列。 |
| TargetCollection | はい | ターゲット コレクション。 |

**LocalizedCollection** 要素には、次の要素が含まれています。

| 要素 | 発生回数 | 説明 |
| ------- | ----------- | ----------- |
| Item | 0:n | ユーザーが要求についてユーザー インターフェイスで選択可能なオプション (ドロップダウン リストの値など) を定義します。 |

**Item** 要素には、次の属性が含まれています。

| 属性 | 必須 | 説明 |
| --------- | -------- | ----------- |
| Text | はい | このオプションのユーザー インターフェイスでユーザーに表示する必要がある、ユーザーフレンドリーな表示文字列。 |
| Value | はい | このオプションの選択に関連付けられている要求の文字列値。 |
| SelectByDefault | いいえ | このオプションが既定で UI で選択するかどうかを示します。 指定できる値True または False。 |

次の例は、**LocalizedCollections** 要素の使用を示しています。 これには、2 つの **LocalizedCollection** 要素が含まれています。1 つは英語用、もう 1 つはスペイン語用です。 両方とも、英語とスペイン語の項目の一覧を使用して、要求 `Gender` の **Restriction** コレクションを設定します。

```xml
<LocalizedResources Id="api.selfasserted.en">
 <LocalizedCollections>
   <LocalizedCollection ElementType="ClaimType" ElementId="Gender" TargetCollection="Restriction">
      <Item Text="Female" Value="F" />
      <Item Text="Male" Value="M" />
    </LocalizedCollection>
</LocalizedCollections>

<LocalizedResources Id="api.selfasserted.es">
 <LocalizedCollections>
   <LocalizedCollection ElementType="ClaimType" ElementId="Gender" TargetCollection="Restriction">
      <Item Text="Femenino" Value="F" />
      <Item Text="Masculino" Value="M" />
    </LocalizedCollection>
</LocalizedCollections>
```

### <a name="localizedstrings"></a>LocalizedStrings

**LocalizedStrings** 要素には、次の要素が含まれています。

| 要素 | 発生回数 | 説明 |
| ------- | ----------- | ----------- |
| LocalizedString | 1:n | ローカライズされた文字列。 |

**LocalizedString** 要素には、次の属性が含まれています。

| 属性 | 必須 | 説明 |
| --------- | -------- | ----------- |
| ElementType | はい | 指定できる値: [ClaimsProvider](#claimsprovider)、[ClaimType](#claimtype)、[ErrorMessage](#errormessage)、[GetLocalizedStringsTransformationClaimType](#getlocalizedstringstransformationclaimtype)、[FormatLocalizedStringTransformationClaimType](#formatlocalizedstringtransformationclaimtype)、[Predicate](#predicate)、[InputValidation](#inputvalidation)、または [UxElement](#uxelement)。   | 
| ElementId | はい | **ElementType** が `ClaimType`、`Predicate`、または `InputValidation` に設定されている場合、この要素には ClaimsSchema セクションで定義済みの要求の種類への参照が含まれます。 |
| StringId | はい | **ElementType** が `ClaimType` に設定されている場合、この要素には要求の種類の属性への参照が含まれます。 指定できる値: `DisplayName`、`AdminHelpText`、または `PatternHelpText`。 `DisplayName` 値は、要求の表示名を設定するために使用されます。 `AdminHelpText` 値は、要求ユーザーのヘルプ テキスト名を設定するために使用されます。 `PatternHelpText` 値は、要求パターンのヘルプ テキストを設定するために使用されます。 **ElementType** が `UxElement` に設定されている場合、この要素にはユーザー インターフェイス要素の属性への参照が含まれます。 **ElementType** が `ErrorMessage` に設定されている場合、この要素はエラー メッセージの識別子を指定します。 `UxElement` 識別子の完全な一覧については、「[ローカライズ文字列 ID](localization-string-ids.md)」を参照してください。|

## <a name="elementtype"></a>ElementType

ElementType は、要求の種類、要求変換、またはローカライズされるポリシーのユーザー インターフェイス要素を参照します。

| ローカライズする要素 | ElementType | ElementId |StringId |
| --------- | -------- | ----------- |----------- |
| ID プロバイダー名 |`ClaimsProvider`| | ClaimsExchange 要素の ID|
| 要求の種類の属性|`ClaimType`|要求の種類の名前| ローカライズされる要求の属性。 使用できる値: `AdminHelpText`、`DisplayName`、`PatternHelpText`、および `UserHelpText`。|
|エラー メッセージ|`ErrorMessage`||エラー メッセージの ID |
|ローカライズされた文字列を要求にコピーする|`GetLocalizedStringsTra nsformationClaimType`||出力要求の名前|
|述語ユーザー メッセージ|`Predicate`|述語の名前| ローカライズされる述語の属性。 使用可能な値: `HelpText`。|
|述語グループ ユーザー メッセージ|`InputValidation`|PredicateValidation 要素の ID。|PredicateGroup 要素の ID。 述語グループは、ElementId に定義されている述語検証要素の子である必要があります。|
|ユーザー インターフェイスの要素 |`UxElement` | | ローカライズされるユーザー インターフェイス要素の ID。|
|[表示コントロール](display-controls.md) |`DisplayControl` |表示コントロールの ID。 | ローカライズされるユーザー インターフェイス要素の ID。|

## <a name="examples"></a>例

### <a name="claimsprovider"></a>ClaimsProvider

ClaimsProvider 値は、要求プロバイダーの表示名のいずれかをローカライズするために使用されます。 

```xml
<OrchestrationStep Order="2" Type="ClaimsExchange">
  ...
  <ClaimsExchanges>
    <ClaimsExchange Id="FacebookExchange" TechnicalProfileReferenceId="Facebook-OAUTH" />
    <ClaimsExchange Id="GoogleExchange" TechnicalProfileReferenceId="Google-OAUTH" />
    <ClaimsExchange Id="LinkedInExchange" TechnicalProfileReferenceId="LinkedIn-OAUTH" />
  </ClaimsExchanges>
</OrchestrationStep>

```

次の例は、要求プロバイダーの表示名をローカライズする方法を示しています。

```xml
<LocalizedString ElementType="ClaimsProvider" StringId="FacebookExchange">Facebook</LocalizedString>
<LocalizedString ElementType="ClaimsProvider" StringId="GoogleExchange">Google</LocalizedString>
<LocalizedString ElementType="ClaimsProvider" StringId="LinkedInExchange">LinkedIn</LocalizedString>
```

### <a name="claimtype"></a>ClaimType

ClaimType 値は、いずれかの要求属性をローカライズするために使用されます。 

```xml
<ClaimType Id="email">
  <DisplayName>Email Address</DisplayName>
  <DataType>string</DataType>
  <UserHelpText>Email address that can be used to contact you.</UserHelpText>
  <UserInputType>TextBox</UserInputType>
</ClaimType>
```

次の例は、要求の種類 email の DisplayName、UserHelpText、および PatternHelpText の各属性をローカライズする方法を示しています。

```xml
<LocalizedString ElementType="ClaimType" ElementId="email" StringId="DisplayName">Email</LocalizedString>
<LocalizedString ElementType="ClaimType" ElementId="email" StringId="UserHelpText">Please enter your email</LocalizedString>
<LocalizedString ElementType="ClaimType" ElementId="email" StringId="PatternHelpText">Please enter a valid email address</LocalizedString>
```

### <a name="errormessage"></a>ErrorMessage

ErrorMessage 値は、システム エラー メッセージのいずれかをローカライズするために使用されます。 

```xml
<TechnicalProfile Id="AAD-UserWriteUsingAlternativeSecurityId">
  <Metadata>
    <Item Key="RaiseErrorIfClaimsPrincipalAlreadyExists">true</Item>
    <Item Key="UserMessageIfClaimsPrincipalAlreadyExists">You are already registered, please press the back button and sign in instead.</Item>
  </Metadata>
  ...
</TechnicalProfile>
```

次の例は、UserMessageIfClaimsPrincipalAlreadyExists エラー メッセージをローカライズする方法を示しています。


```xml
<LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfClaimsPrincipalAlreadyExists">The account you are trying to create already exists, please sign-in.</LocalizedString>
```

### <a name="formatlocalizedstringtransformationclaimtype"></a>FormatLocalizedStringTransformationClaimType

FormatLocalizedStringTransformationClaimType 値は、ローカライズされた文字列に要求を書式設定するために使用されます。 詳細については、[FormatLocalizedString 要求変換](string-transformations.md#formatlocalizedstring)に関する記事を参照してください。


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

次の例は、FormatLocalizedStringTransformationClaimType 要求変換の文字列形式をローカライズする方法を示しています。

```xml
<LocalizedString ElementType="FormatLocalizedStringTransformationClaimType" StringId="ResponseMessge_EmailExists">The email '{0}' is already an account in this organization. Click Next to sign in with that account.</LocalizedString>
```

### <a name="getlocalizedstringstransformationclaimtype"></a>GetLocalizedStringsTransformationClaimType

GetLocalizedStringsTransformationClaimType 値は、ローカライズされた文字列を要求にコピーするために使用されます。 詳細については、[GetLocalizedStringsTransformation 要求変換](string-transformations.md#getlocalizedstringstransformation)に関する記事を参照してください。


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

次の例は、GetLocalizedStringsTransformation 要求変換の出力要求をローカライズする方法を示しています。

```xml
<LocalizedString ElementType="GetLocalizedStringsTransformationClaimType" StringId="email_subject">Contoso account email verification code</LocalizedString>
<LocalizedString ElementType="GetLocalizedStringsTransformationClaimType" StringId="email_message">Thanks for verifying your account!</LocalizedString>
<LocalizedString ElementType="GetLocalizedStringsTransformationClaimType" StringId="email_code">Your code is</LocalizedString>
<LocalizedString ElementType="GetLocalizedStringsTransformationClaimType" StringId="email_signature">Sincerely</LocalizedString>
```

### <a name="predicate"></a>Predicate

Predicate 値は、[Predicate](predicates.md) エラー メッセージのいずれかをローカライズするために使用されます。 

```xml
<Predicates>
  <Predicate Id="LengthRange" Method="IsLengthRange"  HelpText="The password must be between 6 and 64 characters.">
    <Parameters>
      <Parameter Id="Minimum">6</Parameter>
      <Parameter Id="Maximum">64</Parameter>
    </Parameters>
  </Predicate>
  <Predicate Id="Lowercase" Method="IncludesCharacters" HelpText="a lowercase letter">
    <Parameters>
      <Parameter Id="CharacterSet">a-z</Parameter>
    </Parameters>
  </Predicate>
  <Predicate Id="Uppercase" Method="IncludesCharacters" HelpText="an uppercase letter">
    <Parameters>
      <Parameter Id="CharacterSet">A-Z</Parameter>
    </Parameters>
  </Predicate>
</Predicates>
```

次の例は、述語のヘルプ テキストをローカライズする方法を示しています。

```xml
<LocalizedString ElementType="Predicate" ElementId="LengthRange" StringId="HelpText">The password must be between 6 and 64 characters.</LocalizedString>
<LocalizedString ElementType="Predicate" ElementId="Lowercase" StringId="HelpText">a lowercase letter</LocalizedString>
<LocalizedString ElementType="Predicate" ElementId="Uppercase" StringId="HelpText">an uppercase letter</LocalizedString>
```

### <a name="inputvalidation"></a>InputValidation

InputValidation 値は、[PredicateValidation](predicates.md) グループのエラー メッセージのいずれかをローカライズするために使用されます。 

```xml
<PredicateValidations>
  <PredicateValidation Id="CustomPassword">
    <PredicateGroups>
      <PredicateGroup Id="LengthGroup">
        <PredicateReferences MatchAtLeast="1">
          <PredicateReference Id="LengthRange" />
        </PredicateReferences>
      </PredicateGroup>
      <PredicateGroup Id="CharacterClasses">
        <UserHelpText>The password must have at least 3 of the following:</UserHelpText>
        <PredicateReferences MatchAtLeast="3">
          <PredicateReference Id="Lowercase" />
          <PredicateReference Id="Uppercase" />
          <PredicateReference Id="Number" />
          <PredicateReference Id="Symbol" />
        </PredicateReferences>
      </PredicateGroup>
    </PredicateGroups>
  </PredicateValidation>
</PredicateValidations>
```

次の例は、述語検証グループのヘルプ テキストをローカライズする方法を示しています。

```xml
<LocalizedString ElementType="InputValidation" ElementId="CustomPassword" StringId="CharacterClasses">The password must have at least 3 of the following:</LocalizedString>
```

### <a name="uxelement"></a>UxElement

UxElement 値は、ユーザー インターフェイス要素のいずれかをローカライズするために使用されます。 次の例は、続行とキャンセルのボタンをローカライズする方法を示しています。

```xml
<LocalizedString ElementType="UxElement" StringId="button_continue">Create new account</LocalizedString>
<LocalizedString ElementType="UxElement" StringId="button_cancel">Cancel</LocalizedString>
```

### <a name="displaycontrol"></a>DisplayControl

DisplayControl 値は、[表示コントロール](display-controls.md) ユーザー インターフェイス要素のいずれかをローカライズするために使用されます。 有効にすると、表示コントロール localizedStrings は、一部の *UxElement* StringID (**ver_but_send**、**ver_but_edit**、**ver_but_resend**、**ver_but_verify** など) よりも **優先** されます。 次の例は、送信と確認のボタンをローカライズする方法を示しています。 

```xml
<LocalizedString ElementType="DisplayControl" ElementId="emailVerificationControl" StringId="but_send_code">Send verification code</LocalizedString>
<LocalizedString ElementType="DisplayControl" ElementId="emailVerificationControl" StringId="but_verify_code">Verify code</LocalizedString>
```

セルフアサート技術プロファイルの Metadata セクションで参照された ContentDefinition の DataUri を[ページ レイアウトのバージョン](page-layout.md) 2.1.0 以上に設定する必要があります。 次に例を示します。

```xml
<ContentDefinition Id="api.selfasserted">
  <DataUri>urn:com:microsoft:aad:b2c:elements:selfasserted:2.1.0</DataUri>
  ...
```

## <a name="next-steps"></a>次のステップ

ローカライズの例については、次の記事を参照してください。

- [Azure Active Directory B2C でのカスタム ポリシーによる言語のカスタマイズ](language-customization.md)
- [Azure Active Directory B2C でのユーザー フローによる言語のカスタマイズ](language-customization.md)
