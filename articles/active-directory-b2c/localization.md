---
title: ローカライズ - Azure Active Directory B2C
description: Azure Active Directory B2C でカスタム ポリシーの Localization 要素を指定します。
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 08/27/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: ec9b4e7ce761d524d047f4d12cab9e5b782e6032
ms.sourcegitcommit: 94ee81a728f1d55d71827ea356ed9847943f7397
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/26/2019
ms.locfileid: "70033457"
---
# <a name="localization"></a>ローカライズ

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

**Localization** 要素を使用すると、ユーザー体験に関するポリシーで複数のロケールや言語をサポートすることができます。 ポリシーでのローカライズのサポートにより、次のことが可能になります。

- ポリシーでサポートされている言語の明示的な一覧を設定し、既定の言語を選択する。
- 言語固有の文字列とコレクションを指定する。

```XML
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

| Attribute | 必須 | 説明 |
| --------- | -------- | ----------- |
| 有効 | いいえ | 指定できる値: `true` または `false`。 |

**Localization** 要素には、次の XML 要素が含まれています。

| 要素 | 発生回数 | 説明 |
| ------- | ----------- | ----------- |
| SupportedLanguages | 1:n | サポートされている言語の一覧。 |
| LocalizedResources | 0:n | ローカライズされたリソースの一覧。 |

## <a name="supportedlanguages"></a>SupportedLanguages

**SupportedLanguages** 要素には、次の属性が含まれています。

| Attribute | 必須 | 説明 |
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

| Attribute | 必須 | 説明 |
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

| Attribute | 必須 | 説明 |
| --------- | -------- | ----------- |
| ElementType | はい | ポリシー ファイル内の ClaimType 要素またはユーザー インターフェイス要素を参照します。 |
| ElementId | はい | **ElementType** が ClaimType に設定されている場合に使用される、ClaimsSchema セクションで定義済みの要求の種類への参照を含む文字列。 |
| TargetCollection | はい | ターゲット コレクション。 |

**LocalizedCollection** 要素には、次の要素が含まれています。

| 要素 | 発生回数 | 説明 |
| ------- | ----------- | ----------- |
| Item | 0:n | ユーザーが要求についてユーザー インターフェイスで選択可能なオプション (ドロップダウン リストの値など) を定義します。 |

**Item** 要素には、次の属性が含まれています。

| Attribute | 必須 | 説明 |
| --------- | -------- | ----------- |
| Text | はい | このオプションのユーザー インターフェイスでユーザーに表示する必要がある、ユーザーフレンドリーな表示文字列。 |
| 値 | はい | このオプションの選択に関連付けられている要求の文字列値。 |
| SelectByDefault | いいえ | このオプションが既定で UI で選択するかどうかを示します。 指定できる値True または False。 |

次の例は、**LocalizedCollections** 要素の使用を示しています。 これには、2 つの**LocalizedCollection** 要素が含まれています。1 つは英語用、もう 1 つはスペイン語用です。 両方とも、英語とスペイン語の項目の一覧を使用して、要求 `Gender` の **Restriction** コレクションを設定します。

```XML
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

| Attribute | 必須 | 説明 |
| --------- | -------- | ----------- |
| ElementType | はい | ポリシー内の要求の種類要素またはユーザー インターフェイス要素への参照。 指定できる値: `ClaimType`、`UxElement`、`ErrorMessage`、`Predicate`。 `ClaimType` 値は、StringId で指定された、要求属性のいずれかをローカライズするために使用されます。 `UxElement` 値は、StringId で指定されたユーザー インターフェイス要素のいずれかをローカライズするために使用されます。 `ErrorMessage` 値は、StringId で指定されたシステム エラー メッセージのいずれかをローカライズするために使用されます。 `Predicate` 値は、StringId で指定された、[Predicate](predicates.md) エラー メッセージのいずれかをローカライズするために使用されます。 `InputValidation` 値は、StringId で指定された [PredicateValidation](predicates.md) グループ エラー メッセージのいずれかをローカライズするために使用されます。 |
| ElementId | はい | **ElementType** が `ClaimType`、`Predicate`、または `InputValidation` に設定されている場合、この要素には ClaimsSchema セクションで定義済みの要求の種類への参照が含まれます。 |
| StringId | はい | **ElementType** が `ClaimType` に設定されている場合、この要素には要求の種類の属性への参照が含まれます。 指定できる値: `DisplayName`、`AdminHelpText`、または `PatternHelpText`。 `DisplayName` 値は、要求の表示名を設定するために使用されます。 `AdminHelpText` 値は、要求ユーザーのヘルプ テキスト名を設定するために使用されます。 `PatternHelpText` 値は、要求パターンのヘルプ テキストを設定するために使用されます。 **ElementType** が `UxElement` に設定されている場合、この要素にはユーザー インターフェイス要素の属性への参照が含まれます。 **ElementType** が `ErrorMessage` に設定されている場合、この要素はエラー メッセージの識別子を指定します。 `UxElement` 識別子の完全な一覧については、「[ローカライズ文字列 ID](localization-string-ids.md)」を参照してください。|


次の例は、ローカライズされたサインアップ ページを示しています。 最初の 3 つの **LocalizedString** 値は、要求属性を設定します。 4 つ目の値は、続行ボタンの値を変更します。 最後の値は、エラーメッセージを変更します。

```XML
<LocalizedResources Id="api.selfasserted.en">
  <LocalizedStrings>
    <LocalizedString ElementType="ClaimType" ElementId="email" StringId="DisplayName">Email</LocalizedString>
    <LocalizedString ElementType="ClaimType" ElementId="email" StringId="UserHelpText">Please enter your email</LocalizedString>
    <LocalizedString ElementType="ClaimType" ElementId="email" StringId="PatternHelpText">Please enter a valid email address</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="button_continue">Create new account</LocalizedString>
   <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfClaimsPrincipalAlreadyExists">The account you are trying to create already exists, please sign-in.</LocalizedString>
  </LocalizedStrings>
</LocalizedResources>
```

次の例は、Id `IsLengthBetween8And64` を持つ **Predicate** のローカライズされた **UserHelpText** を示しています。 また、Id `StrongPassword` を持つ **PredicateValidation** の Id `CharacterClasses` を持つ **PredicateGroup** のローカライズされた **UserHelpText** を示しています。

```XML
<PredicateValidation Id="StrongPassword">
  <PredicateGroups>
    ...
    <PredicateGroup Id="CharacterClasses">
    ...
    </PredicateGroup>
  </PredicateGroups>
</PredicateValidation>

...

<Predicate Id="IsLengthBetween8And64" Method="IsLengthRange">
  ...
</Predicate>
...


<LocalizedString ElementType="InputValidation" ElementId="StrongPassword" StringId="CharacterClasses">The password must have at least 3 of the following:</LocalizedString>

<LocalizedString ElementType="Predicate" ElementId="IsLengthBetween8And64" StringId="HelpText">The password must be between 8 and 64 characters.</LocalizedString>
```

## <a name="set-up-localization"></a>ローカライズを設定する

この記事では、ユーザー体験に関するポリシーで複数のロケールや言語をサポートする方法について説明します。 ローカライズには、3 つのステップ (サポートされている言語の明示的な一覧の設定、言語固有の文字列とコレクションの指定、およびページのContentDefinition の編集) が必要です。

### <a name="set-up-the-explicit-list-of-supported-languages"></a>サポートされている言語の明示的な一覧を設定する

**BuildingBlocks** 要素の下に、サポートされている言語の一覧を含む **Localization** 要素を追加します。 次の例は、英語 (既定値) とスペイン語の両方に対するローカライズのサポートを定義する方法を示しています。

```XML
<Localization Enabled="true">
  <SupportedLanguages DefaultLanguage="en" MergeBehavior="ReplaceAll">
    <SupportedLanguage>en</SupportedLanguage>
    <SupportedLanguage>es</SupportedLanguage>
  </SupportedLanguages>
</Localization>
```

### <a name="provide-language-specific-strings-and-collections"></a>言語固有の文字列とコレクションを指定する

**SupportedLanguages** 要素を閉じた後に、**Localization** 要素内に **LocalizedResources** 要素を追加します。 各ページ (コンテンツ定義) とサポートする言語の **LocalizedResources** 要素を追加します。 英語、スペイン語、およびフランス語の統合されたサインアップまたはサインイン ページ、サインアップおよび多要素認証 (MFA) ページをカスタマイズするには、次の **LocalizedResources**  要素を追加します。

- 統合されたサインアップまたはサインイン ページ、英語 `<LocalizedResources Id="api.signuporsignin.en">`
- 統合されたサインアップまたはサインイン ページ、スペイン語 `<LocalizedResources Id="api.signuporsignin.es">`
- 統合されたサインアップまたはサインイン ページ、フランス語 `<LocalizedResources Id="api.signuporsignin.fr">`
- サインアップ、英語 `<LocalizedResources Id="api.localaccountsignup.en">`
- サインアップ、スペイン語 `<LocalizedResources Id="api.localaccountsignup.es">`
- サインアップ、フランス語 `<LocalizedResources Id="api.localaccountsignup.fr">`
- MFA、英語 `<LocalizedResources Id="api.phonefactor.en">`
- MFA、スペイン語 `<LocalizedResources Id="api.phonefactor.es">`
- MFA、フランス語 `<LocalizedResources Id="api.phonefactor.fr">`

各 **LocalizedResources** 要素には、複数の **LocalizedString** 要素を持つすべての必要な **LocalizedStrings** 要素と、複数の **LocalizedCollection** 要素を持つ **LocalizedCollections** 要素が含まれています。  次の例では、サインアップ ページの英語のローカライズ追加します。

注:この例では、`Gender` と `City` の要求の種類を参照しています。 この例を使用するには、これらの要求を定義してください。 詳細については、[ClaimsSchema](claimsschema.md)を参照してください。

```XML
<LocalizedResources Id="api.localaccountsignup.en">

 <LocalizedCollections>
   <LocalizedCollection ElementType="ClaimType" ElementId="Gender" TargetCollection="Restriction">
      <Item Text="Female" Value="F" />
      <Item Text="Male" Value="M" />
    </LocalizedCollection>
   <LocalizedCollection ElementType="ClaimType" ElementId="City" TargetCollection="Restriction">
      <Item Text="New York" Value="NY" />
      <Item Text="Paris" Value="Paris" />
      <Item Text="London" Value="London" />
    </LocalizedCollection>
  </LocalizedCollections>

  <LocalizedStrings>
    <LocalizedString ElementType="ClaimType" ElementId="email" StringId="DisplayName">Email</LocalizedString>
    <LocalizedString ElementType="ClaimType" ElementId="email" StringId="UserHelpText">Please enter your email</LocalizedString>
    <LocalizedString ElementType="ClaimType" ElementId="email" StringId="PatternHelpText">Please enter a valid email address</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="button_continue">Create new account</LocalizedString>
   <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfClaimsPrincipalAlreadyExists">The account you are trying to create already exists, please sign-in.</LocalizedString>
  </LocalizedStrings>
</LocalizedResources>
```

スペイン語のサインアップ ページのローカライズ。

```XML
<LocalizedResources Id="api.localaccountsignup.es">

 <LocalizedCollections>
   <LocalizedCollection ElementType="ClaimType" ElementId="Gender" TargetCollection="Restriction">
      <Item Text="Femenino" Value="F" />
      <Item Text="Masculino" Value="M" />
    </LocalizedCollection>
   <LocalizedCollection ElementType="ClaimType" ElementId="City" TargetCollection="Restriction">
      <Item Text="Nueva York" Value="NY" />
      <Item Text="París" Value="Paris" />
      <Item Text="Londres" Value="London" />
    </LocalizedCollection>
  </LocalizedCollections>

  <LocalizedStrings>
    <LocalizedString ElementType="ClaimType" ElementId="email" StringId="DisplayName">Dirección de correo electrónico</LocalizedString>
    <LocalizedString ElementType="ClaimType" ElementId="email" StringId="UserHelpText">Dirección de correo electrónico que puede usarse para ponerse en contacto con usted.</LocalizedString>
    <LocalizedString ElementType="ClaimType" ElementId="email" StringId="PatternHelpText">Introduzca una dirección de correo electrónico.</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="button_continue">Crear</LocalizedString>
   <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfClaimsPrincipalAlreadyExists">Ya existe un usuario con el id. especificado. Elija otro diferente.</LocalizedString>
  </LocalizedStrings>
</LocalizedResources>
```

### <a name="edit-the-contentdefinition-for-the-page"></a>ページの ContentDefinition を編集する

ローカライズするページごとに、**ContentDefinitio** で検索する言語コードを指定します。

次の例では、英語 (en) とスペイン語 (es) のカスタム文字列がサインアップ ページに追加されています。 各 **LocalizedResourcesReference** の **LocalizedResourcesReferenceId** は、それらのロケールと同じですが、識別子として任意の文字列を使用できます。 言語とページの組み合わせごとに、以前に作成した対応する **LocalizedResources** をポイントします。

```XML
<ContentDefinition Id="api.localaccountsignup">
...
  <LocalizedResourcesReferences MergeBehavior="Prepend">
    <LocalizedResourcesReference Language="en" LocalizedResourcesReferenceId="api.localaccountsignup.en" />
    <LocalizedResourcesReference Language="es" LocalizedResourcesReferenceId="api.localaccountsignup.es" />
  </LocalizedResourcesReferences>
</ContentDefinition>
```

次の例は、最終的な XML を示しています。

```XML
<BuildingBlocks>
  <ContentDefinitions>
    <ContentDefinition Id="api.localaccountsignup">
      <!-- Other content definitions elements... -->
      <LocalizedResourcesReferences MergeBehavior="Prepend">
         <LocalizedResourcesReference Language="en" LocalizedResourcesReferenceId="api.localaccountsignup.en" />
         <LocalizedResourcesReference Language="es" LocalizedResourcesReferenceId="api.localaccountsignup.es" />
      </LocalizedResourcesReferences>
    </ContentDefinition>
    <!-- More content definitions... -->
  </ContentDefinitions>

  <Localization Enabled="true">

    <SupportedLanguages DefaultLanguage="en" MergeBehavior="ReplaceAll">
      <SupportedLanguage>en</SupportedLanguage>
      <SupportedLanguage>es</SupportedLanguage>
      <!-- More supported language... -->
    </SupportedLanguages>

    <LocalizedResources Id="api.localaccountsignup.en">
      <LocalizedCollections>
        <LocalizedCollection ElementType="ClaimType" ElementId="Gender" TargetCollection="Restriction">
          <Item Text="Female" Value="F" />
          <Item Text="Male" Value="M" />
          <!-- More items... -->
        </LocalizedCollection>
        <LocalizedCollection ElementType="ClaimType" ElementId="City" TargetCollection="Restriction">
          <Item Text="New York" Value="NY" />
          <Item Text="Paris" Value="Paris" />
          <Item Text="London" Value="London" />
        </LocalizedCollection>
        <!-- More localized collections... -->
      </LocalizedCollections>
      <LocalizedStrings>
        <LocalizedString ElementType="ClaimType" ElementId="email" StringId="DisplayName">Email</LocalizedString>
      <LocalizedString ElementType="ClaimType" ElementId="email" StringId="UserHelpText">Please enter your email</LocalizedString>
        <LocalizedString ElementType="ClaimType" ElementId="email" StringId="PatternHelpText">Please enter a valid email address</LocalizedString>
        <LocalizedString ElementType="UxElement" StringId="button_continue">Create new account</LocalizedString>
       <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfClaimsPrincipalAlreadyExists">The account you are trying to create already exists, please sign-in.</LocalizedString>
        <!-- More localized strings... -->
      </LocalizedStrings>
    </LocalizedResources>

    <LocalizedResources Id="api.localaccountsignup.es">
      <LocalizedCollections>
       <LocalizedCollection ElementType="ClaimType" ElementId="Gender" TargetCollection="Restriction">
          <Item Text="Femenino" Value="F" />
          <Item Text="Masculino" Value="M" />
        </LocalizedCollection>
        <LocalizedCollection ElementType="ClaimType" ElementId="City" TargetCollection="Restriction">
          <Item Text="Nueva York" Value="NY" />
          <Item Text="París" Value="Paris" />
          <Item Text="Londres" Value="London" />
        </LocalizedCollection>
      </LocalizedCollections>
      <LocalizedStrings>
        <LocalizedString ElementType="ClaimType" ElementId="email" StringId="DisplayName">Dirección de correo electrónico</LocalizedString>
        <LocalizedString ElementType="ClaimType" ElementId="email" StringId="UserHelpText">Dirección de correo electrónico que puede usarse para ponerse en contacto con usted.</LocalizedString>
        <LocalizedString ElementType="ClaimType" ElementId="email" StringId="PatternHelpText">Introduzca una dirección de correo electrónico.</LocalizedString>
        <LocalizedString ElementType="UxElement" StringId="button_continue">Crear</LocalizedString>
      <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfClaimsPrincipalAlreadyExists">Ya existe un usuario con el id. especificado. Elija otro diferente.</LocalizedString>
      </LocalizedStrings>
    </LocalizedResources>
    <!-- More localized resources... -->
  </Localization>
</BuildingBlocks>
```
