---
title: 述語および PredicateValidations
titleSuffix: Azure AD B2C
description: Azure Active Directory B2C のカスタム ポリシーを使用して、不適切な形式のデータが Azure AD B2C テナントに追加されないようにします。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 03/30/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 46f04c55b40d4f1bdbbf5fd55eb648d1d3294056
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "97108418"
---
# <a name="predicates-and-predicatevalidations"></a>述語および PredicateValidations

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

**Predicates** と **PredicateValidations** 要素を使用して検証プロセスを実行し、適切に形式設定されたデータのみが Azure Active Directory B2C (Azure AD B2C) テナントに入力されるようにすることができます。

次の図に、要素の関係を示します。

![述語と述語の検証のリレーションシップを示す図](./media/predicates/predicates.png)

## <a name="predicates"></a>述語

**Predicate** 要素によって、要求タイプの値をチェックする基本的な検証が定義され、`true` または `false` が返されます。 検証は、指定された **Method** 要素と、そのメソッドに関連する **Parameter** 要素のセットを使用して行われます。 例えば、述語によって、文字列要求値の長さが、指定された最小および最大パラメーターの範囲内にあるかどうか、または文字列要求の値に文字セットが含まれているかどうかをチェックできます。 **UserHelpText** 要素により、チェックが失敗した場合にユーザーに表示されるエラー メッセージが指定されます。 **UserHelpText** 要素の値を、[言語のカスタマイズ](localization.md)を使用してローカライズできます。

**Predicates** 要素は、[BuildingBlocks](buildingblocks.md) 要素内の **ClaimsSchema** 要素の直後に記述する必要があります。

**Predicates** 要素には、次の要素が含まれています。

| 要素 | 発生回数 | 説明 |
| ------- | ----------- | ----------- |
| Predicate | 1:n | 述語の一覧。 |

**Predicate** 要素には、次の属性が含まれています。

| 属性 | 必須 | 説明 |
| --------- | -------- | ----------- |
| Id | はい | 述語に使用される識別子です。 その他の要素は、ポリシーでこの識別子を使用することができます。 |
| メソッド | はい | 検証に使用するメソッドの型。 使用可能な値: [IsLengthRange](#islengthrange)、[MatchesRegex](#matchesregex)、[IncludesCharacters](#includescharacters)、または [IsDateRange](#isdaterange)。  |
| HelpText | いいえ | チェックが失敗した場合に、ユーザーに表示されるエラー メッセージ。 この文字列は、[言語カスタマイズ](localization.md)を使ってローカライズすることができます。 |

**Predicate** 要素には、次の要素が含まれています。

| 要素 | 発生回数 | 説明 |
| ------- | ----------- | ----------- |
| UserHelpText | 0:1 | (非推奨) チェックが失敗した場合に、ユーザーに表示されるエラー メッセージ。 |
| パラメーター | 1:1 | 文字列検証のメソッド タイプのパラメーター。 |

**Parameters** 要素には、次の要素が含まれています。

| 要素 | 発生回数 | 説明 |
| ------- | ----------- | ----------- |
| パラメーター | 1:n | 文字列検証のメソッド タイプのパラメーター。 |

**Parameters** 要素には、次の属性が含まれています。

| 要素 | 発生回数 | 説明 |
| ------- | ----------- | ----------- |
| Id | 1:1 | パラメーターの識別子。 |

### <a name="predicate-methods"></a>述語メソッド

#### <a name="islengthrange"></a>IsLengthRange

IsLengthRange メソッドによって、文字列要求値の長さが、指定された最小および最大パラメーターの範囲内にあるかどうかがチェックされます。 述語要素は、次のパラメーターをサポートしています。

| パラメーター | 必須 | 説明 |
| ------- | ----------- | ----------- |
| 最大値 | はい | 入力できる最大文字数です。 |
| 最小値 | はい | 入力する必要がある最小文字数です。 |


次の例は、文字列の長さ範囲を指定する `Minimum` および `Maximum` パラメーターを持つ IsLengthRange メソッドを示しています。

```xml
<Predicate Id="IsLengthBetween8And64" Method="IsLengthRange" HelpText="The password must be between 8 and 64 characters.">
  <Parameters>
    <Parameter Id="Minimum">8</Parameter>
    <Parameter Id="Maximum">64</Parameter>
  </Parameters>
</Predicate>
```

#### <a name="matchesregex"></a>MatchesRegex

MatchesRegex メソッドによって、文字列要求値が正規表現に一致するかどうかがチェックされます。 述語要素は、次のパラメーターをサポートしています。

| パラメーター | 必須 | 説明 |
| ------- | ----------- | ----------- |
| RegularExpression | はい | 一致させる正規表現パターン。 |

次の例は、正規表現を指定する `RegularExpression` パラメーターを持つ `MatchesRegex` メソッドを示しています。

```xml
<Predicate Id="PIN" Method="MatchesRegex" HelpText="The password must be numbers only.">
  <Parameters>
    <Parameter Id="RegularExpression">^[0-9]+$</Parameter>
  </Parameters>
</Predicate>
```

#### <a name="includescharacters"></a>IncludesCharacters

IncludesCharacters メソッドによって、文字列要求値に文字セットが含まれているかどうかがチェックされます。 述語要素は、次のパラメーターをサポートしています。

| パラメーター | 必須 | 説明 |
| ------- | ----------- | ----------- |
| CharacterSet | はい | 入力できる文字のセット。 たとえば、小文字の `a-z`、大文字の `A-Z`、数字の `0-9`、`@#$%^&amp;*\-_+=[]{}|\\:',?/~"();!` などの記号の一覧です。 |

次の例は、文字セットを指定する `CharacterSet` パラメーターを持つ `IncludesCharacters` メソッドを示しています。

```xml
<Predicate Id="Lowercase" Method="IncludesCharacters" HelpText="a lowercase letter">
  <Parameters>
    <Parameter Id="CharacterSet">a-z</Parameter>
  </Parameters>
</Predicate>
```

#### <a name="isdaterange"></a>IsDateRange

IsDateRange メソッドによって、日付要求値が、指定された最小および最大パラメーターの範囲内にあるかどうかがチェックされます。 述語要素は、次のパラメーターをサポートしています。

| パラメーター | 必須 | 説明 |
| ------- | ----------- | ----------- |
| 最大値 | はい | 入力できる可能な最大の日付。 date の形式は `yyyy-mm-dd` 規則、または `Today` に従います。 |
| 最小値 | はい | 入力できる可能な最小の日付。 date の形式は `yyyy-mm-dd` 規則、または `Today` に従います。|

次の例は、`yyyy-mm-dd` と `Today` の形式で日付範囲を指定する `Minimum` および `Maximum` パラメーターを持つ `IsDateRange` メソッドを示しています。

```xml
<Predicate Id="DateRange" Method="IsDateRange" HelpText="The date must be between 1970-01-01 and today.">
  <Parameters>
    <Parameter Id="Minimum">1970-01-01</Parameter>
    <Parameter Id="Maximum">Today</Parameter>
  </Parameters>
</Predicate>
```

## <a name="predicatevalidations"></a>PredicateValidations

述語によって、要求タイプに対してチェックを行う検証が定義されますが、**PredicateValidations** は要求タイプに適用できるユーザー入力検証を形式設定する述語セットをグループ化します。 各 **PredicateValidation** 要素には、**Predicate** をポイントする **PredicateReference** 要素のセットを含む **PredicateGroup** 要素のセットが含まれています。 検証に合格するには、要求の値が、**PredicateReference** 要素のセットを持つすべての **PredicateGroup** に基づき、すべての述語のすべてのテストに合格する必要があります。

**PredicateValidations** 要素は、[BuildingBlocks](buildingblocks.md) 要素内の **Predicates** 要素の直後に表示する必要があります。

```xml
<PredicateValidations>
  <PredicateValidation Id="">
    <PredicateGroups>
      <PredicateGroup Id="">
        <UserHelpText></UserHelpText>
        <PredicateReferences MatchAtLeast="">
          <PredicateReference Id="" />
          ...
        </PredicateReferences>
      </PredicateGroup>
      ...
    </PredicateGroups>
  </PredicateValidation>
...
</PredicateValidations>
```

**PredicateValidations** 要素には、次の要素が含まれています。

| 要素 | 発生回数 | 説明 |
| ------- | ----------- | ----------- |
| PredicateValidation | 1:n | 述語検証の一覧。 |

**PredicateValidation** 要素には、次の属性が含まれています。

| 属性 | 必須 | 説明 |
| --------- | -------- | ----------- |
| Id | はい | 述語の検証に使用される識別子です。 **ClaimType** 要素は、ポリシーにこの識別子を使用できます。 |

**PredicateValidation** 要素には、次の要素が含まれています。

| 要素 | 発生回数 | 説明 |
| ------- | ----------- | ----------- |
| PredicateGroups | 1:n | 述語グループの一覧。 |

**PredicateGroups** 要素には、次の要素が含まれています。

| 要素 | 発生回数 | 説明 |
| ------- | ----------- | ----------- |
| PredicateGroup | 1:n | 述語の一覧。 |

**PredicateGroups** 要素には、次の属性が含まれています。

| 属性 | 必須 | 説明 |
| --------- | -------- | ----------- |
| Id | はい | 述語グループに使用される識別子です。  |

**PredicateGroups** 要素には、次の要素が含まれています。

| 要素 | 発生回数 | 説明 |
| ------- | ----------- | ----------- |
| UserHelpText | 0:1 |  ユーザーが入力する必要のある値を把握するのに役立つ、述語の説明。 |
| PredicateReferences | 1:n | 述語参照の一覧。 |

**PredicateReferences** 要素には、次の属性が含まれています。

| 属性 | 必須 | 説明 |
| --------- | -------- | ----------- |
| MatchAtLeast | いいえ | 入力が受け入れられるには、少なくともここに指定した述部定義の数だけ、値が一致する必要があることを指定します。 指定しなかった場合、値はすべての述語定義と一致する必要があります。 |

**PredicateReferences** 要素には、次の要素が含まれています。

| 要素 | 発生回数 | 説明 |
| ------- | ----------- | ----------- |
| PredicateReference | 1:n | 述語への参照。 |

**PredicateReference** 要素には、次の属性が含まれています。

| 属性 | 必須 | 説明 |
| --------- | -------- | ----------- |
| Id | はい | 述語の検証に使用される識別子です。  |


## <a name="configure-password-complexity"></a>パスワードの複雑さの構成

**Predicates** と **PredicateValidationsInput** を使用して、アカウントを作成するときにユーザーによって指定されるパスワードの複雑さに対する要件を制御できます。 既定では、Azure AD B2C では強力なパスワードが使用されます。 Azure AD B2C では、顧客が使用できるパスワードの複雑さを制御する構成オプションもサポートしています。 これらの述語要素を使用して、パスワードの複雑さを定義できます。

- **IsLengthBetween8And64**: `IsLengthRange` メソッドを使用して、パスワードが 8 文字から 64 文字の間であることを検証します。
- **Lowercase**: `IncludesCharacters` メソッドを使用して、パスワードに小文字が含まれていることを検証します。
- **Uppercase**: `IncludesCharacters` メソッドを使用して、パスワードに大文字が含まれていることを検証します。
- **Number**: `IncludesCharacters` メソッドを使用して、パスワードに数字が含まれていることを検証します。
- **Symbol**: `IncludesCharacters` メソッドを使用して、パスワードにいずれかの記号文字が含まれていることを検証します。
- **PIN**: `MatchesRegex` メソッドを使用して、パスワードに数字のみが含まれていることを検証します。
- **AllowedAADCharacters**: `MatchesRegex` メソッドを使用して、パスワードのみの無効な文字が指定されたことを検証します。
- **DisallowedWhitespace**: `MatchesRegex` メソッドを使用して、パスワードが空白文字で開始または終了していないことを検証します。

```xml
<Predicates>
  <Predicate Id="IsLengthBetween8And64" Method="IsLengthRange" HelpText="The password must be between 8 and 64 characters.">
    <Parameters>
      <Parameter Id="Minimum">8</Parameter>
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

  <Predicate Id="Number" Method="IncludesCharacters" HelpText="a digit">
    <Parameters>
      <Parameter Id="CharacterSet">0-9</Parameter>
    </Parameters>
  </Predicate>

  <Predicate Id="Symbol" Method="IncludesCharacters" HelpText="a symbol">
    <Parameters>
      <Parameter Id="CharacterSet">@#$%^&amp;*\-_+=[]{}|\\:',.?/`~"();!</Parameter>
    </Parameters>
  </Predicate>

  <Predicate Id="PIN" Method="MatchesRegex" HelpText="The password must be numbers only.">
    <Parameters>
      <Parameter Id="RegularExpression">^[0-9]+$</Parameter>
    </Parameters>
  </Predicate>

  <Predicate Id="AllowedAADCharacters" Method="MatchesRegex" HelpText="An invalid character was provided.">
    <Parameters>
      <Parameter Id="RegularExpression">(^([0-9A-Za-z\d@#$%^&amp;*\-_+=[\]{}|\\:',?/`~"();! ]|(\.(?!@)))+$)|(^$)</Parameter>
    </Parameters>
  </Predicate>

  <Predicate Id="DisallowedWhitespace" Method="MatchesRegex" HelpText="The password must not begin or end with a whitespace character.">
    <Parameters>
      <Parameter Id="RegularExpression">(^\S.*\S$)|(^\S+$)|(^$)</Parameter>
    </Parameters>
  </Predicate>
```

基本的な検証を定義した後、それらを組み合わせてパスワード ポリシーのセットを作成し、ポリシーで使用することができます。

- **SimplePassword**: DisallowedWhitespace、AllowedAADCharacters、および IsLengthBetween8And64 を検証します。
- **StrongPassword** DisallowedWhitespace、AllowedAADCharacters、IsLengthBetween8And64 を検証します。 最後のグループ `CharacterClasses` は、`MatchAtLeast` を 3 に設定して、述語の追加セットを実行します。 ユーザー パスワードは 8 文字から 16 文字にする必要があり、大文字、小文字、数字、または記号の中から 3 つを使用する必要があります。
- **CustomPassword**: DisallowedWhitespace、AllowedAADCharacters のみを検証します。 このため、ユーザーは、文字が有効であれば、任意の長さのパスワードを指定できます。

```xml
<PredicateValidations>
  <PredicateValidation Id="SimplePassword">
    <PredicateGroups>
      <PredicateGroup Id="DisallowedWhitespaceGroup">
        <PredicateReferences>
          <PredicateReference Id="DisallowedWhitespace" />
        </PredicateReferences>
      </PredicateGroup>
      <PredicateGroup Id="AllowedAADCharactersGroup">
        <PredicateReferences>
          <PredicateReference Id="AllowedAADCharacters" />
        </PredicateReferences>
      </PredicateGroup>
      <PredicateGroup Id="LengthGroup">
        <PredicateReferences>
          <PredicateReference Id="IsLengthBetween8And64" />
        </PredicateReferences>
      </PredicateGroup>
    </PredicateGroups>
  </PredicateValidation>

  <PredicateValidation Id="StrongPassword">
    <PredicateGroups>
      <PredicateGroup Id="DisallowedWhitespaceGroup">
        <PredicateReferences>
          <PredicateReference Id="DisallowedWhitespace" />
       </PredicateReferences>
      </PredicateGroup>
      <PredicateGroup Id="AllowedAADCharactersGroup">
        <PredicateReferences>
          <PredicateReference Id="AllowedAADCharacters" />
        </PredicateReferences>
      </PredicateGroup>
      <PredicateGroup Id="LengthGroup">
        <PredicateReferences>
          <PredicateReference Id="IsLengthBetween8And64" />
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

  <PredicateValidation Id="CustomPassword">
    <PredicateGroups>
      <PredicateGroup Id="DisallowedWhitespaceGroup">
        <PredicateReferences>
          <PredicateReference Id="DisallowedWhitespace" />
        </PredicateReferences>
      </PredicateGroup>
      <PredicateGroup Id="AllowedAADCharactersGroup">
        <PredicateReferences>
          <PredicateReference Id="AllowedAADCharacters" />
        </PredicateReferences>
      </PredicateGroup>
    </PredicateGroups>
  </PredicateValidation>
</PredicateValidations>
```

要求の種類で、**PredicateValidationReference** 要素を追加し、SimplePassword、StrongPassword、または CustomPassword など、いずれかの述語検証として識別子を指定します。

```xml
<ClaimType Id="password">
  <DisplayName>Password</DisplayName>
  <DataType>string</DataType>
  <AdminHelpText>Enter password</AdminHelpText>
  <UserHelpText>Enter password</UserHelpText>
  <UserInputType>Password</UserInputType>
  <PredicateValidationReference Id="StrongPassword" />
</ClaimType>
```

Azure AD B2C にエラー メッセージが表示された場合に、要素がどのように編成されるかを以下に示します。

![述語と PredicateGroup パスワードの複雑さの例の図](./media/predicates/predicates-pass.png)

## <a name="configure-a-date-range"></a>日付範囲の構成

**Predicates** 要素と **PredicateValidations** 要素を使用して、**UserInputType** の最小および最大日付値を、`DateTimeDropdown` を使用して制御できます。 これを行うには、`IsDateRange` メソッドを使用して **述語** を作成し、最小および最大パラメーターを指定します。

```xml
<Predicates>
  <Predicate Id="DateRange" Method="IsDateRange" HelpText="The date must be between 01-01-1980 and today.">
    <Parameters>
      <Parameter Id="Minimum">1980-01-01</Parameter>
      <Parameter Id="Maximum">Today</Parameter>
    </Parameters>
  </Predicate>
</Predicates>
```

`DateRange` 述語への参照を含む **PredicateValidation** を追加します。

```xml
<PredicateValidations>
  <PredicateValidation Id="CustomDateRange">
    <PredicateGroups>
      <PredicateGroup Id="DateRangeGroup">
        <PredicateReferences>
          <PredicateReference Id="DateRange" />
        </PredicateReferences>
      </PredicateGroup>
    </PredicateGroups>
  </PredicateValidation>
</PredicateValidations>
```

要求の種類で、**PredicateValidationReference** 要素を追加し、`CustomDateRange` に ID を指定します。

```xml
<ClaimType Id="dateOfBirth">
  <DisplayName>Date of Birth</DisplayName>
  <DataType>date</DataType>
  <AdminHelpText>The user's date of birth.</AdminHelpText>
  <UserHelpText>Your date of birth.</UserHelpText>
  <UserInputType>DateTimeDropdown</UserInputType>
  <PredicateValidationReference Id="CustomDateRange" />
</ClaimType>
 ```

## <a name="next-steps"></a>次のステップ

- 述語の検証を使用して [Azure Active Directory B2C でカスタム ポリシーを使用してパスワードの変更を構成する](password-complexity.md)方法について確認します。