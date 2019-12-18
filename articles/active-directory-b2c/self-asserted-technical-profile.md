---
title: カスタムポリシーでセルフアサ―ト技術プロファイルを定義する
titleSuffix: Azure AD B2C
description: Azure Active Directory B2C のカスタム ポリシーでは、セルフ アサート技術プロファイルを定義します。
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/10/2018
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: bfa8982fb49b31540d1926bdeb75a96dc1d79cf0
ms.sourcegitcommit: 5b9287976617f51d7ff9f8693c30f468b47c2141
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/09/2019
ms.locfileid: "74950903"
---
# <a name="define-a-self-asserted-technical-profile-in-an-azure-active-directory-b2c-custom-policy"></a>Azure Active Directory B2C のカスタム ポリシーでセルフ アサート技術プロファイルを定義します。

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

ユーザーが入力を提供する Azure Active Directory B2C (Azure AD B2C) のすべての操作は、セルフ アサート技術プロファイルです。 たとえば、サインアップ ページ、サインイン ページ、またはパスワードのリセット ページ。

## <a name="protocol"></a>Protocol

**Protocol** 要素の **Name** 属性は `Proprietary` に設定する必要があります。 セルフ アサートのために**ハンドラー**属性は、Azure AD B2C で使用されるプロトコル ハンドラーのアセンブリの完全修飾名を含める必要があります。 `Web.TPEngine.Providers.SelfAssertedAttributeProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null`

次の例は、電子メールのサインアップ用のセルフ アサート技術プロファイルを示しています。

```XML
<TechnicalProfile Id="LocalAccountSignUpWithLogonEmail">
  <DisplayName>Email signup</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.SelfAssertedAttributeProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
```

## <a name="input-claims"></a>入力要求

セルフ アサート技術プロファイルでは、**InputClaims**と**InputClaimsTransformations**要素を使用して、セルフ アサートのページ (出力要求) に表示される要求の値を事前入力することができます。 たとえば、プロファイルの編集ポリシーでは、ユーザージャーニーはまず Azure AD B2C ディレクトリ サービスからユーザー プロファイルを読み取り、セルフ アサート技術プロファイルは、ユーザー プロファイルに格納されているユーザー データで入力要求を設定します。 これらの要求はユーザー プロファイルから収集され、後で既存のデータを編集できるユーザーに提示されます。

```XML
<TechnicalProfile Id="SelfAsserted-ProfileUpdate">
...
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="alternativeSecurityId" />
    <InputClaim ClaimTypeReferenceId="userPrincipalName" />
    <InputClaim ClaimTypeReferenceId="givenName" />
    <InputClaim ClaimTypeReferenceId="surname" />
  </InputClaims>
```


## <a name="output-claims"></a>出力要求

**OutputClaims** 要素には、ユーザーからのデータの収集に提示される要求の一覧が含まれています。 いくつかの値を持つ出力要求を事前に設定するには、以前に説明した入力要求を使用します。 要素は、既定値を含めることもできます。 **OutputClaims** 内の要求の順序は、Azure AD B2C が画面に要求を表示する順序を制御します。 要求が以前に設定されてない場合にのみ **DefaultValue** 属性が有効になります。 ただし、ユーザーが値を空のままにしても以前のオーケストレーション ステップで今まで設定されていた場合、既定値は有効になりません。 既定値の使用を強制するには、 **AlwaysUseDefaultValue** 属性を`true`に設定します。 特定の出力要求の値を提供するようにユーザーを強制するには、 **OutputClaims** 要素の**必要な**の属性を`true`に設定します。

**OutputClaims** コレクションの **ClaimType** 内の要素は、 `TextBox`または`DropdownSingleSelect`など Azure AD B2C がサポートする任意のユーザー入力タイプに **UserInputType** 要素を設定する必要があります。 または、 **OutputClaim** 要素は、**DefaultValue** を設定する必要があります。

**OutputClaimsTransformations** 要素には、出力要求を修正したり新しい要求を生成したりするために使用される **OutputClaimsTransformation** 要素のコレクションが含まれている場合があります。

次の出力要求は常に`live.com`に設定します:

```XML
<OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="live.com" AlwaysUseDefaultValue="true" />
```

### <a name="use-case"></a>ユース ケース

出力要求の 4 つのシナリオがあります。

- **ユーザーからの出力要求の収集** - ユーザーから情報 (たとえば、生年月日) を収集する必要がある場合は、**OutputClaims** コレクションに要求を追加する必要があります。 ユーザーに提示される要求は、**UserInputType** (たとえば、`TextBox`や`DropdownSingleSelect`など) を指定する必要があります。 セルフ アサート技術プロファイルに同じ要求を出力する検証技術プロファイルが含まれている場合、Azure AD B2C はユーザーに要求を表示しません。 ユーザーに提示する出力要求がない場合は、Azure AD B2C は、技術プロファイルをスキップします。
- **出力要求にデフォルト値を設定する** - ユーザーからのデータを収集せずに、または検証技術プロファイルからデータを返さずに設定します。 `LocalAccountSignUpWithLogonEmail`セルフ アサート技術プロファイルは、**実行済みのセルフ アサート入力**の要求を`true`に設定します。
- **検証技術プロファイルは、出力要求を返します。** - 技術プロファイルは、いくつかの要求を返す検証技術プロファイルを呼び出すことがあります。 要求をバブルアップして、ユーザー体験の次のオーケストレーション ステップに戻すことができます。 たとえば、ローカル アカウントでサインインすると、`SelfAsserted-LocalAccountSignin-Email`という名前のセルフ アサート技術プロファイルが`login-NonInteractive`という名前の検証技術プロファイルを呼び出します。 この技術プロファイルは、ユーザーの資格情報を検証し、ユーザー プロファイルを返します。 'UserPrincipalName'、'displayName'、'givenName' および 'surName' など。
- **出力要求変換を使用して要求を出力します**

次の例では、`LocalAccountSignUpWithLogonEmail`セルフ アサート技術プロファイルは、出力要求の使用を示し、**実行 SelfAsserted 入力**を`true`に設定します。 `objectId`、 `authenticationSource`、`newUser`要求は、`AAD-UserWriteUsingLogonEmail`検証技術プロファイルの出力であり、ユーザーには表示されません。

```XML
<TechnicalProfile Id="LocalAccountSignUpWithLogonEmail">
  <DisplayName>Email signup</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.SelfAssertedAttributeProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <Metadata>
    <Item Key="IpAddressClaimReferenceId">IpAddress</Item>
    <Item Key="ContentDefinitionReferenceId">api.localaccountsignup</Item>
    <Item Key="language.button_continue">Create</Item>
  </Metadata>
  <CryptographicKeys>
    <Key Id="issuer_secret" StorageReferenceId="B2C_1A_TokenSigningKeyContainer" />
  </CryptographicKeys>
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="email" />
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="objectId" />
    <OutputClaim ClaimTypeReferenceId="email" PartnerClaimType="Verified.Email" Required="true" />
    <OutputClaim ClaimTypeReferenceId="newPassword" Required="true" />
    <OutputClaim ClaimTypeReferenceId="reenterPassword" Required="true" />
    <OutputClaim ClaimTypeReferenceId="executed-SelfAsserted-Input" DefaultValue="true" />
    <OutputClaim ClaimTypeReferenceId="authenticationSource" />
    <OutputClaim ClaimTypeReferenceId="newUser" />

    <!-- Optional claims, to be collected from the user -->
    <OutputClaim ClaimTypeReferenceId="displayName" />
    <OutputClaim ClaimTypeReferenceId="givenName" />
    <OutputClaim ClaimTypeReferenceId="surName" />
  </OutputClaims>
  <ValidationTechnicalProfiles>
    <ValidationTechnicalProfile ReferenceId="AAD-UserWriteUsingLogonEmail" />
  </ValidationTechnicalProfiles>
  <UseTechnicalProfileForSessionManagement ReferenceId="SM-AAD" />
</TechnicalProfile>

```

## <a name="persist-claims"></a>永続化した要求

**PersistedClaims** 要素が存在しない場合、セルフ アサート技術プロファイルは、Azure AD B2C にデータを保持しません。 代わりに、データの永続化を担当する検証技術プロファイルが呼び出されます。 たとえば、サインアップ ポリシーは、新しいユーザー プロファイルを収集するために`LocalAccountSignUpWithLogonEmail`セルフ アサート技術プロファイルを使用します。 `LocalAccountSignUpWithLogonEmail`技術プロファイルは、Azure AD B2C でアカウントを作成するには、検証技術プロファイルを呼び出します。

## <a name="validation-technical-profiles"></a>検証技術プロファイル

検証技術プロファイルは、参照元の技術プロファイルの出力要求の一部またはすべてを検証するために使用されます。 検証技術プロファイルの入力要求は、セルフ アサート技術プロファイルの出力要求に表示される必要があります。 検証技術プロファイルは、ユーザー入力を検証し、ユーザーにエラーを返すことができます。

検証技術プロファイルは、[Azure Active Directory](active-directory-technical-profile.md) や [REST API](restful-technical-profile.md) 技術プロファイルなど、ポリシー内の任意の技術プロファイルである場合があります。 前の例では、`LocalAccountSignUpWithLogonEmail`技術プロファイルは、signinName がディレクトリに存在しないことを検証します。 検証されていない場合、検証技術プロファイルは、ローカル アカウントを作成し、objectId、authenticationSource、新規ユーザーを返します。 `SelfAsserted-LocalAccountSignin-Email`技術プロファイルは、`login-NonInteractive`検証技術プロファイルを呼び出してユーザーの資格情報を検証します。

ビジネス ロジックで REST API の技術プロファイルを呼び出したり、入力要求を上書きします。または企業の基幹業務アプリケーションをさらに統合することによってユーザー データを充実することができます。 詳細については、[検証技術プロファイル](validation-technical-profile.md)を参照してください

## <a name="metadata"></a>Metadata

| Attribute | 必須 | 説明 |
| --------- | -------- | ----------- |
| setting.showContinueButton | いいえ | [続行する] ボタンが表示されます。 指定できる値は `true` (既定値) または `false` です。 |
| setting.showCancelButton | いいえ | [キャンセル] ボタンが表示されます。 指定できる値は `true` (既定値) または `false` です。 |
| setting.operatingMode | いいえ | サインイン ページのために、このプロパティは、入力検証とエラー メッセージなど [ユーザー名] フィールドの動作を制御します。 期待される値は`Username`または`Email`です。 |
| ContentDefinitionReferenceId | はい | [コンテンツ定義](contentdefinitions.md)の識別子は、この技術プロファイルに関連付けられています。 |
| EnforceEmailVerification | いいえ | サインアップまたはプロファイルの編集のために、電子メールの検証を強制します。 指定できる値は `true`(既定値) または`false`です。 |
| setting.showSignupLink | いいえ | サインアップ ボタンが表示されます。 指定できる値は `true`(既定値) または`false`です。 |
| setting.retryLimit | いいえ | ユーザーが検証技術プロファイルに対してチェックされたデータを提供しようとする回数を制御します。 たとえば、ユーザーは既に存在するアカウントでサインアップしようとし、制限に達するまで試行し続けます。
| SignUpTarget | いいえ | サインアップ対象交換識別子。 ユーザーがサインアップ ボタンをクリックすると、Azure AD B2C は、指定された交換識別子を実行します。 |

## <a name="cryptographic-keys"></a>暗号化キー

**CryptographicKeys** 要素は使用されません。













