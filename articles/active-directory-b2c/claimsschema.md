---
title: ClaimsSchema  - Azure Active Directory B2C | Microsoft Docs
description: Azure Active Directory B2C でカスタム ポリシーの ClaimsSchema 要素を指定します。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 03/05/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 2ff43408cfa6d95dbd5a235a950269c47d57a416
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "97654032"
---
# <a name="claimsschema"></a>ClaimsSchema

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

**ClaimsSchema** 要素は、ポリシーの一部として参照可能な要求の種類を定義します。 要求スキーマは、要求を宣言する場所です。 要求は、名、姓、表示名、電話番号などを指定できます。 ClaimsSchema 要素には **ClaimType** 要素の一覧が含まれています。 **ClaimType** 要素に **Id** 属性が含まれており、 それは要求の名前であります。

```xml
<BuildingBlocks>
  <ClaimsSchema>
    <ClaimType Id="Id">
      <DisplayName>Surname</DisplayName>
      <DataType>string</DataType>
      <DefaultPartnerClaimTypes>
        <Protocol Name="OAuth2" PartnerClaimType="family_name" />
        <Protocol Name="OpenIdConnect" PartnerClaimType="family_name" />
        <Protocol Name="SAML2" PartnerClaimType="http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname" />
      </DefaultPartnerClaimTypes>
      <UserHelpText>Your surname (also known as family name or last name).</UserHelpText>
      <UserInputType>TextBox</UserInputType>
```

## <a name="claimtype"></a>ClaimType

**ClaimType** 要素には、次の属性が含まれています。

| 属性 | 必須 | 説明 |
| --------- | -------- | ----------- |
| Id | はい | 要求の種類に使用される識別子です。 その他の要素は、ポリシーでこの識別子を使用することができます。 |

**ClaimType** 要素には、次の要素が含まれています。

| 要素 | 発生回数 | 説明 |
| ------- | ----------- | ----------- |
| DisplayName | 1:1 | さまざまな画面上のユーザーに表示されるタイトル。 値を[ローカライズ](localization.md)することができます。 |
| DataType | 1:1 | 要求の種類です。 |
| DefaultPartnerClaimTypes | 0:1 | パートナーの既定要求は、指定されたプロトコルに使用する種類です。 **InputClaim** または **OutputClaim** 要素で指定されている **PartnerClaimType** 内の値を上書きすることができます。 この要素を使用すると、プロトコルの既定の名前を指定できます。  |
| マスク | 0:1 | 要求を表示するときに適用できる、文字をマスクするのに使用されるオプショナルな文字列。 たとえば、電話番号 324-232-4343 をXXX-XXX-4343 としてマスクすることができます。 |
| UserHelpText | 0:1 | 要求の目的を理解するのにユーザーに役立つ種類の説明。 値を[ローカライズ](localization.md)することができます。 |
| UserInputType | 0:1 | 要求の種類の要求データを手動で入力するときに、ユーザーが利用できるような入力コントロールの種類。 このページの後半で定義されている、ユーザーにより入力された種類を参照してください。 |
| AdminHelpText | 0:1 | 管理者がその目的を理解するのに役立つ要求の種類の説明。 |
| Restriction | 0:1 | 正規表現 (Regex) または許容される値の一覧など、この要求に対する値の制限。 値を[ローカライズ](localization.md)することができます。 |
PredicateValidationReference| 0:1 | **PredicateValidationsInput** 要素への参照。 **PredicateValidationReference** 要素を使用すると、適切な形式のデータのみが入力されていることを確認する検証プロセスを実できます。 詳細については、[述語](predicates.md)を参照してください。 |



### <a name="datatype"></a>DataType

**DataType** 要素は次の値をサポートします。

| Type | 説明 |
| ------- | ----------- |
|boolean|ブール値 (`true` または `false`) を表します。|
|date| 特定の時点を表します。通常は日付で表されます。 date の値は ISO 8601 規則に準拠します。|
|dateTime|通常、日付や時刻として表現される瞬間を表します。 date の値は ISO 8601 規則に準拠します。|
|duration|年、月、日、時、分、および秒単位の時間間隔を表します。 形式は `PnYnMnDTnHnMnS` です。 `P` は正の値を示し、`N` は負の値を示します。 `nY` は、年数の後にリテラル `Y` が付いたものです。 `nMo` は、月数の後にリテラル `Mo` が付いたものです。 `nD` は、日数の後にリテラル `D` が付いたものです。 例: `P21Y` は、21 年を表します。 `P1Y2Mo` は、1 年 2 か月を表します。 `P1Y2Mo5D` は、1 年、2 か月、および 5 日を表します。  `P1Y2M5DT8H5M620S` は、1 年、2 か月、5 日、8 時間、5 分、および 20 秒を表します。  |
|phoneNumber|電話番号を表します。 |
|INT| -2,147,483,648 から 2,147,483,647 までの数値を表します|
|long| -9,223,372,036,854,775,808 から 9,223,372,036,854,775,807 までの数値を表します |
|string| テキストを一連の UTF-16 コード単位として表現します。|
|stringCollection|`string` のコレクションを表します。|
|userIdentity| ユーザー ID を表します。|
|userIdentityCollection|`userIdentity` のコレクションを表します。|

### <a name="defaultpartnerclaimtypes"></a>DefaultPartnerClaimTypes

**DefaultPartnerClaimTypes** には次の要素が含まれることがあります。

| 要素 | 発生回数 | 説明 |
| ------- | ----------- | ----------- |
| Protocol | 1:n | プロトコルとその既定のパートナー要求種類の名前一覧。 |

**Protocol** 要素には、次の属性が含まれています。

| 属性 | 必須 | 説明 |
| --------- | -------- | ----------- |
| 名前 | はい | Azure AD B2C によってサポートされている有効なプロトコルの名前。 指定できる値: OAuth1、OAuth2、SAML2、OpenIdConnect。 |
| PartnerClaimType | はい | 使用する要求種類の名。 |

次の例で、Identity Experience Framework は SAML2 id プロバイダーまたは証明書利用者アプリケーションと相互作用するときに、 **surname** 要求が`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname`にマップされ、OpenIdConnect と OAuth2 とともに、要求は、`family_name`にマップされます。

```xml
<ClaimType Id="surname">
  <DisplayName>Surname</DisplayName>
  <DataType>string</DataType>
  <DefaultPartnerClaimTypes>
    <Protocol Name="OAuth2" PartnerClaimType="family_name" />
    <Protocol Name="OpenIdConnect" PartnerClaimType="family_name" />
    <Protocol Name="SAML2" PartnerClaimType="http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname" />
  </DefaultPartnerClaimTypes>
</ClaimType>
```

その結果、Azure AD B2C によって発行された JWT トークンでは、ClaimType 名 **surname** ではなく `family_name` が出力されます。

```json
{
  "sub": "6fbbd70d-262b-4b50-804c-257ae1706ef2",
  "auth_time": 1535013501,
  "given_name": "David",
  "family_name": "Williams",
  "name": "David Williams",
}
```

### <a name="mask"></a>マスク

**マスク** 要素には、次の属性が含まれています。

| 属性 | 必須 | 説明 |
| --------- | -------- | ----------- |
| `Type` | はい | 要求マスクの種類です。 指定できる値: `Simple` または `Regex`。 `Simple`値は、単純なテキスト マスクが要求の文字列の先頭部分に適用されることを示します。 `Regex`値は、正規表現が全体として、要求の文字列に適用されることを示します。  `Regex`値が指定されている場合、オプショナルな属性も正規表現で定義することが必要となります。 |
| `Regex` | いいえ | **`Type`** が `Regex` に設定されている場合、使用される正規表現を指定します。

次の例では **PhoneNumber** 要求を `Simple` マスクで構成します。

```xml
<ClaimType Id="PhoneNumber">
  <DisplayName>Phone Number</DisplayName>
  <DataType>string</DataType>
  <Mask Type="Simple">XXX-XXX-</Mask>
  <UserHelpText>Your telephone number.</UserHelpText>
</ClaimType>
```

Identity Experience Framework では、最初の 6 桁の数字を非表示し、電話番号を表示します。

![ブラウザーに表示されている最初の 6 桁が X でマスクされている電話番号要求](./media/claimsschema/mask.png)

次の例では **AlternateEmail** 要求を `Regex` マスクで構成します。

```xml
<ClaimType Id="AlternateEmail">
  <DisplayName>Please verify the secondary email linked to your account</DisplayName>
  <DataType>string</DataType>
  <Mask Type="Regex" Regex="(?&lt;=.).(?=.*@)">*</Mask>
  <UserInputType>Readonly</UserInputType>
</ClaimType>
```

Identity Experience Framework では、電子メールアドレスと電子メールのドメイン名の最初の文字のみを表示します。

![ブラウザーに表示されている文字がアスタリスクでマスクされた電子メール要求](./media/claimsschema/mask-regex.png)


### <a name="restriction"></a>Restriction

**制限** 要素は、次の属性を含めることがあります。

| 属性 | 必須 | 説明 |
| --------- | -------- | ----------- |
| MergeBehavior | いいえ | 同じ識別子を持つ親ポリシー内の ClaimType に列挙値をマージするために使用するメソッド。 基本ポリシーで指定された要求を上書きする場合は、この属性を使用します。 指定できる値: `Append`、`Prepend`、または `ReplaceAll`。 `Append`値は、親ポリシーで指定したコレクションの末尾に追加する必要があるデータのコレクション。 `Prepend`値は、親ポリシーで指定したコレクションの前に追加する必要があるデータのコレクション。 `ReplaceAll`値が無視される親ポリシーで指定されたデータのコレクション。 |

**制限** 要素には、次の要素が含まれています。

| 要素 | 発生回数 | 説明 |
| ------- | ----------- | ----------- |
| 列挙 | 1:n | ドロップダウン リストの値など、要求に指定するオプションを選択するためにユーザーのユーザー インターフェイスで使用可能なオプションです。 |
| Pattern | 1:1 | 使用できる正規表現。 |

#### <a name="enumeration"></a>列挙

**Enumeration** 要素は、ユーザーが要求についてユーザー インターフェイスで選択可能なオプション (`CheckboxMultiSelect`、`DropdownSingleSelect`、または `RadioSingleSelect` の値など) を定義します。 または、[LocalizedCollections](localization.md#localizedcollections) 要素を使用して、使用可能なオプションを定義してローカライズすることもできます。 要求 **Enumeration** コレクションから項目を検索するには、[GetMappedValueFromLocalizedCollection](string-transformations.md#getmappedvaluefromlocalizedcollection) 要求変換を使用します。

**列挙型** 要素には、次の属性が含まれています。

| 属性 | 必須 | 説明 |
| --------- | -------- | ----------- |
| Text | はい | このオプションのユーザー インターフェイスでユーザーに表示される表示文字列。 |
|Value | はい | このオプションの選択に関連付けられている要求の値。 |
| SelectByDefault | いいえ | このオプションが既定で UI で選択するかどうかを示します。 指定できる値True または False。 |

次の例では、**市区町村** ドロップダウン リストの要求を設定し、既定値を`New York`に設定します。

```xml
<ClaimType Id="city">
  <DisplayName>city where you work</DisplayName>
  <DataType>string</DataType>
  <UserInputType>DropdownSingleSelect</UserInputType>
  <Restriction>
    <Enumeration Text="Bellevue" Value="bellevue" SelectByDefault="false" />
    <Enumeration Text="Redmond" Value="redmond" SelectByDefault="false" />
    <Enumeration Text="New York" Value="new-york" SelectByDefault="true" />
  </Restriction>
</ClaimType>
```

既定値は、ニューヨークに設定されている市区町村ドロップダウン リスト。

![ブラウザーにレンダリングされ、既定値を示しているドロップダウン コントロール](./media/claimsschema/dropdownsingleselect.png)

### <a name="pattern"></a>Pattern

**パターン** 要素には、次の属性が含まれることがあります。

| 属性 | 必須 | 説明 |
| --------- | -------- | ----------- |
| RegularExpression | はい | この種類の要求が有効になるのに一致する必要がある正規表現。 |
| HelpText | いいえ | 正規表現のチェックが失敗した場合にユーザーに表示されるエラー メッセージ。 |

次の例では、**電子メール** 要求を正規表現入力検証とヘルプ テキストとともに設定します。

```xml
<ClaimType Id="email">
  <DisplayName>Email Address</DisplayName>
  <DataType>string</DataType>
  <DefaultPartnerClaimTypes>
  <Protocol Name="OpenIdConnect" PartnerClaimType="email" />
  </DefaultPartnerClaimTypes>
  <UserHelpText>Email address that can be used to contact you.</UserHelpText>
  <UserInputType>TextBox</UserInputType>
  <Restriction>
    <Pattern RegularExpression="^[a-zA-Z0-9.+!#$%&amp;'^_`{}~-]+@[a-zA-Z0-9-]+(?:\.[a-zA-Z0-9-]+)*$" HelpText="Please enter a valid email address." />
  </Restriction>
</ClaimType>
```

Identity Experience Framework は、電子メールの入力検証の形式を表示します。

![regex 制限によってトリガーされたエラー メッセージを示しているテキストボックス](./media/claimsschema/pattern.png)

### <a name="userinputtype"></a>UserInputType

Azure AD B2C では、さまざまなクレームの種類の要求データを手動で入力するときに使用できるテキスト ボックス、パスワード、およびドロップダウン リストなど、ユーザー入力の種類をサポートしています。 [セルフ アサートの技術プロファイル](self-asserted-technical-profile.md)および [表示コントロール](display-controls.md)を使用してユーザーから情報を収集する時に **UserInputType** を指定する必要があります。

**UserInputType** 要素で使用できるユーザー入力の種類:

| UserInputType | サポートされる ClaimType | 説明 |
| --------- | -------- | ----------- |
|CheckboxMultiSelect| `string` |複数選択ドロップダウン ボックス。 要求の値は、選択された値のコンマ区切り文字列で表されます。 |
|DateTimeDropdown | `date`, `dateTime` |日、月、および年を選択するドロップダウン。 |
|DropdownSingleSelect |`string` |単一選択ドロップダウン ボックス。 要求の値は、選択された値です。|
|EmailBox | `string` |電子メールの入力フィールド。 |
|段落 | `boolean`, `date`, `dateTime`, `duration`, `int`, `long`, `string`|段落タグ内のテキストのみを表示するフィールド。 |
|Password | `string` |パスワード テキスト ボックス。|
|RadioSingleSelect |`string` | ラジオ ボタンのコレクション。 要求の値は、選択された値です。|
|Readonly | `boolean`, `date`, `dateTime`, `duration`, `int`, `long`, `string`| 読み取り専用テキスト ボックス。 |
|TextBox |`boolean`, `int`, `string` |1 行のテキスト ボックス。 |


#### <a name="textbox"></a>TextBox

**TextBox** ユーザー入力の種類が 1 行のテキスト ボックスを表示するために使用します。

![要求の種類で指定されたプロパティを示しているテキストボックス](./media/claimsschema/textbox.png)

```xml
<ClaimType Id="displayName">
  <DisplayName>Display Name</DisplayName>
  <DataType>string</DataType>
  <UserHelpText>Your display name.</UserHelpText>
  <UserInputType>TextBox</UserInputType>
</ClaimType>
```

#### <a name="emailbox"></a>EmailBox

**EmailBox** ユーザー入力の種類が基本的な電子メールの入力フィールドを提供するために使用します。

![要求の種類で指定されたプロパティを示している電子メール ボックス](./media/claimsschema/emailbox.png)

```xml
<ClaimType Id="email">
  <DisplayName>Email Address</DisplayName>
  <DataType>string</DataType>
  <UserHelpText>Email address that can be used to contact you.</UserHelpText>
  <UserInputType>EmailBox</UserInputType>
  <Restriction>
    <Pattern RegularExpression="^[a-zA-Z0-9.+!#$%&amp;'+^_`{}~-]+(?:\.[a-zA-Z0-9!#$%&amp;'+^_`{}~-]+)*@(?:[a-zA-Z0-9](?:[a-zA-Z0-9-]*[a-zA-Z0-9])?\.)+[a-zA-Z0-9](?:[a-zA-Z0-9-]*[a-zA-Z0-9])?$" HelpText="Please enter a valid email address." />
  </Restriction>
</ClaimType>
```

#### <a name="password"></a>Password

**パスワード** ユーザー入力の種類は、ユーザーが入力したパスワードを記録するために使用します。

![パスワードを使用する要求種類](./media/claimsschema/password.png)

```xml
<ClaimType Id="password">
  <DisplayName>Password</DisplayName>
  <DataType>string</DataType>
  <UserHelpText>Enter password</UserHelpText>
  <UserInputType>Password</UserInputType>
</ClaimType>
```

#### <a name="datetimedropdown"></a>DateTimeDropdown

**DateTimeDropdown** ユーザー入力の種類は、日、月、および年を選択するドロップダウン リストのセットを提供するために使用します。 述語および PredicateValidations 要素を使用すると、日付の最小値と最大値を制御できます。 詳細については、[述語および PredicateValidations](predicates.md)の **日付範囲を構成する** セクションを参照してください。

![datetimedropdown を使用する要求の種類](./media/claimsschema/datetimedropdown.png)

```xml
<ClaimType Id="dateOfBirth">
  <DisplayName>Date Of Birth</DisplayName>
  <DataType>date</DataType>
  <UserHelpText>The date on which you were born.</UserHelpText>
  <UserInputType>DateTimeDropdown</UserInputType>
</ClaimType>
```

#### <a name="radiosingleselect"></a>RadioSingleSelect

**RadioSingleSelect** ユーザー入力の種類はユーザーが 1 つのオプションを選択できるようラジオ ボタンのコレクションを提供するために使用します。

![radiodsingleselect を使用する要求の種類](./media/claimsschema/radiosingleselect.png)

```xml
<ClaimType Id="color">
  <DisplayName>Preferred color</DisplayName>
  <DataType>string</DataType>
  <UserInputType>RadioSingleSelect</UserInputType>
  <Restriction>
    <Enumeration Text="Blue" Value="Blue" SelectByDefault="false" />
    <Enumeration Text="Green " Value="Green" SelectByDefault="false" />
    <Enumeration Text="Orange" Value="Orange" SelectByDefault="true" />
  </Restriction>
</ClaimType>
```

#### <a name="dropdownsingleselect"></a>DropdownSingleSelect

**DropdownSingleSelect** ユーザー入力の種類はユーザーが 1 つのオプションを選択できるようドロップダウンボックスを提供するために使用します。

![dropdownsingleselect を使用する要求の種類](./media/claimsschema/dropdownsingleselect.png)

```xml
<ClaimType Id="city">
  <DisplayName>City where you work</DisplayName>
  <DataType>string</DataType>
  <UserInputType>DropdownSingleSelect</UserInputType>
  <Restriction>
    <Enumeration Text="Bellevue" Value="bellevue" SelectByDefault="false" />
    <Enumeration Text="Redmond" Value="redmond" SelectByDefault="false" />
    <Enumeration Text="New York" Value="new-york" SelectByDefault="true" />
  </Restriction>
</ClaimType>
```

#### <a name="checkboxmultiselect"></a>CheckboxMultiSelect

**CheckboxMultiSelect** ユーザー入力の種類はユーザーが 複数のオプションを選択できるようチェックボックスのコレクションを提供するために使用します。

![checkboxmultiselect を使用する要求の種類](./media/claimsschema/checkboxmultiselect.png)

```xml
<ClaimType Id="languages">
  <DisplayName>Languages you speak</DisplayName>
  <DataType>string</DataType>
  <UserInputType>CheckboxMultiSelect</UserInputType>
  <Restriction>
    <Enumeration Text="English" Value="English" SelectByDefault="true" />
    <Enumeration Text="France " Value="France" SelectByDefault="false" />
    <Enumeration Text="Spanish" Value="Spanish" SelectByDefault="false" />
  </Restriction>
</ClaimType>
```

#### <a name="readonly"></a>Readonly

**Readonly** ユーザー入力の種類は、要求と値を表示する読み取り専用フィールドを提供するために使用します。

![Readonly を使用する要求の種類](./media/claimsschema/readonly.png)

```xml
<ClaimType Id="membershipNumber">
  <DisplayName>Membership number</DisplayName>
  <DataType>string</DataType>
  <UserHelpText>Your membership number (read only)</UserHelpText>
  <UserInputType>Readonly</UserInputType>
</ClaimType>
```


#### <a name="paragraph"></a>段落

**段落** ユーザー入力の種類は、段落タグ内のテキストだけを表示するフィールドを提供するために使用します。  たとえば、 &lt;p&gt;テキスト&lt;/p&gt;。 セルフアサート技術プロファイルの **段落** ユーザー入力の種類 `OutputClaim` は、`Required` 属性 `false` (既定値) を設定する必要があります。

![段落を使用する要求種類](./media/claimsschema/paragraph.png)

```xml
<ClaimType Id="responseMsg">
  <DisplayName>Error message: </DisplayName>
  <DataType>string</DataType>
  <AdminHelpText>A claim responsible for holding response messages to send to the relying party</AdminHelpText>
  <UserHelpText>A claim responsible for holding response messages to send to the relying party</UserHelpText>
  <UserInputType>Paragraph</UserInputType>
  <Restriction>
    <Enumeration Text="B2C_V1_90001" Value="You cannot sign in because you are a minor" />
    <Enumeration Text="B2C_V1_90002" Value="This action can only be performed by gold members" />
    <Enumeration Text="B2C_V1_90003" Value="You have not been enabled for this operation" />
  </Restriction>
</ClaimType>
```
