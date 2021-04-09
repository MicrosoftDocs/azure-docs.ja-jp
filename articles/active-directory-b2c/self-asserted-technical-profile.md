---
title: カスタムポリシーでセルフアサ―ト技術プロファイルを定義する
titleSuffix: Azure AD B2C
description: Azure Active Directory B2C のカスタム ポリシーでは、セルフ アサート技術プロファイルを定義します。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 03/10/2021
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 8d3343838216522abfc11ec3f202ae2da1c0e38f
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102611880"
---
# <a name="define-a-self-asserted-technical-profile-in-an-azure-active-directory-b2c-custom-policy"></a>Azure Active Directory B2C のカスタム ポリシーでセルフ アサート技術プロファイルを定義します。

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

ユーザーが入力を提供する Azure Active Directory B2C (Azure AD B2C) のすべての操作は、セルフ アサート技術プロファイルです。 たとえば、サインアップ ページ、サインイン ページ、またはパスワードのリセット ページ。

## <a name="protocol"></a>Protocol

**Protocol** 要素の **Name** 属性は `Proprietary` に設定する必要があります。 セルフ アサートのために **ハンドラー** 属性は、Azure AD B2C で使用されるプロトコル ハンドラーのアセンブリの完全修飾名を含める必要があります。 `Web.TPEngine.Providers.SelfAssertedAttributeProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null`

次の例は、電子メールのサインアップ用のセルフ アサート技術プロファイルを示しています。

```xml
<TechnicalProfile Id="LocalAccountSignUpWithLogonEmail">
  <DisplayName>Email signup</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.SelfAssertedAttributeProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
```

## <a name="input-claims"></a>入力クレーム

セルフアサート技術プロファイルでは、**InputClaims** と **InputClaimsTransformations** 要素を使用して、セルフアサート ページ (表示要求) に表示される要求の値を事前入力することができます。 たとえば、プロファイルの編集ポリシーでは、ユーザージャーニーはまず Azure AD B2C ディレクトリ サービスからユーザー プロファイルを読み取り、セルフ アサート技術プロファイルは、ユーザー プロファイルに格納されているユーザー データで入力要求を設定します。 これらの要求はユーザー プロファイルから収集され、後で既存のデータを編集できるユーザーに提示されます。

```xml
<TechnicalProfile Id="SelfAsserted-ProfileUpdate">
...
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="alternativeSecurityId" />
    <InputClaim ClaimTypeReferenceId="userPrincipalName" />
    <InputClaim ClaimTypeReferenceId="givenName" />
    <InputClaim ClaimTypeReferenceId="surname" />
  </InputClaims>
```

## <a name="display-claims"></a>表示要求

表示要求機能は、現在、**プレビュー** 段階です。

**DisplayClaims** 要素には、ユーザーからデータを収集するために画面に提示される要求の一覧が含まれます。 表示要求の値を事前入力するには、以前に説明した入力要求を使用します。 要素は、既定値を含めることもできます。

**DisplayClaims** 内の要求の順序によって、Azure AD B2C による画面への要求の表示順序が指定されます。 特定の要求に対する値の指定をユーザーに強制するには、**DisplayClaim** 要素の **Required** 属性を `true` に設定します。

**DisplayClaims** コレクション内の **ClaimType** 要素では、Azure AD B2C でサポートされている **UserInputType** 要素を任意のユーザー入力タイプに設定する必要があります。 たとえば、`TextBox` または `DropdownSingleSelect` です。

### <a name="add-a-reference-to-a-displaycontrol"></a>DisplayControl に参照を追加する

表示要求コレクションに、作成済みの [DisplayControl](display-controls.md) への参照を含めることができます。 表示コントロールは、特別な機能を備えた、Azure AD B2C バックエンド サービスと対話するユーザー インターフェイス要素です。 これにより、バックエンドで検証技術プロファイルを呼び出すページでユーザーが操作を実行できます。 たとえば、電子メール アドレス、電話番号、または顧客のロイヤルティ番号が検証されます。

次の例の `TechnicalProfile` では、表示要求と表示コントロールの使用方法が示されています。

* 最初の表示要求では、電子メール アドレスを収集して検証する `emailVerificationControl` 表示コントロールが参照されます。
* 5 番目の表示要求では、電話番号を収集して検証する `phoneVerificationControl` 表示コントロールが参照されます。
* その他の表示要求は、ユーザーからデータが収集される ClaimTypes です。

```xml
<TechnicalProfile Id="Id">
  <DisplayClaims>
    <DisplayClaim DisplayControlReferenceId="emailVerificationControl" />
    <DisplayClaim ClaimTypeReferenceId="displayName" Required="true" />
    <DisplayClaim ClaimTypeReferenceId="givenName" Required="true" />
    <DisplayClaim ClaimTypeReferenceId="surName" Required="true" />
    <DisplayClaim DisplayControlReferenceId="phoneVerificationControl" />
    <DisplayClaim ClaimTypeReferenceId="newPassword" Required="true" />
    <DisplayClaim ClaimTypeReferenceId="reenterPassword" Required="true" />
  </DisplayClaims>
</TechnicalProfile>
```

既に説明したように、表示コントロールを参照する表示要求では、電子メール アドレスの確認などの独自の検証を実行できます。 さらに、セルフアサート ページでは、次のオーケストレーション手順に進む前に、検証技術プロファイルを使用したユーザー入力 (要求の種類または表示コントロール) を含むページ全体の検証がサポートされます。

### <a name="combine-usage-of-display-claims-and-output-claims-carefully"></a>表示要求と出力要求の組み合わせは慎重に使用する

セルフアサート技術プロファイルに 1 つ以上の **DisplayClaim** 要素を指定する場合は、画面に表示してユーザーからデータを収集する "*すべての*" 要求に対して DisplayClaim を使用する必要があります。 少なくとも 1 つの表示要求が含まれるセルフアサート技術プロファイルでは、出力要求はまったく表示されません。

基本ポリシー内に `age` 要求が **出力** 要求として定義されている次の例を考えてみます。 セルフアサート技術プロファイルに表示要求を追加する前に、ユーザーからデータを収集するために、`age` 要求が画面に表示されます。

```xml
<TechnicalProfile Id="id">
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="age" />
  </OutputClaims>
</TechnicalProfile>
```

その後、基本を継承するリーフ ポリシーで、**表示** 要求として `officeNumber` が指定された場合:

```xml
<TechnicalProfile Id="id">
  <DisplayClaims>
    <DisplayClaim ClaimTypeReferenceId="officeNumber" />
  </DisplayClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="officeNumber" />
  </OutputClaims>
</TechnicalProfile>
```

基本ポリシーの `age` 要求は、ユーザーの画面に表示されなくなり、事実上 "非表示" になります。 `age` 要求を表示し、ユーザーから年齢の値を収集するには、`age` **DisplayClaim** を追加する必要があります。

## <a name="output-claims"></a>出力クレーム

**OutputClaims** 要素には、次のオーケストレーション手順に返される要求の一覧が含まれます。 **DefaultValue** 属性は、要求が設定されてない場合にのみ有効です。 前のオーケストレーション手順で設定されていた場合は、ユーザーが値を空白にした場合でも、既定値が有効になることはありません。 既定値の使用を強制するには、 **AlwaysUseDefaultValue** 属性を`true`に設定します。

セキュリティ上の理由により、パスワード要求の値 (`UserInputType` は `Password` に設定) は、セルフ アサート技術プロファイルの検証技術プロファイルでのみ使用できます。 次のオーケストレーション手順では、パスワード要求を使用できません。 

> [!NOTE]
> Identity Experience Framework (IEF) の以前のバージョンでは、出力要求を使用してユーザーからデータを収集していました。 ユーザーからデータを収集するには、代わりに **DisplayClaims** コレクションを使用してください。

**OutputClaimsTransformations** 要素には、出力要求を修正したり新しい要求を生成するために使用される、**OutputClaimsTransformation** 要素のコレクションが含まれている場合があります。

### <a name="when-you-should-use-output-claims"></a>いつ出力要求を使用すべきか

セルフアサート技術プロファイルでは、出力要求コレクションによって次のオーケストレーション手順に要求が返されます。

出力要求は、次の場合に使用します。

- **要求が出力要求変換による出力の場合**。
- **出力要求に既定値を設定する**。ユーザーからデータを収集しない、または検証技術プロファイルからデータを返さない場合。 `LocalAccountSignUpWithLogonEmail`セルフ アサート技術プロファイルは、**実行済みのセルフ アサート入力** の要求を`true`に設定します。
- **検証技術プロファイルは、出力要求を返します。** - 技術プロファイルは、いくつかの要求を返す検証技術プロファイルを呼び出すことがあります。 要求をバブルアップして、ユーザー体験の次のオーケストレーション ステップに戻すことができます。 たとえば、ローカル アカウントでサインインすると、`SelfAsserted-LocalAccountSignin-Email`という名前のセルフ アサート技術プロファイルが`login-NonInteractive`という名前の検証技術プロファイルを呼び出します。 この技術プロファイルは、ユーザーの資格情報を検証し、ユーザー プロファイルを返します。 'UserPrincipalName'、'displayName'、'givenName' および 'surName' など。
- **表示コントロールによって出力要求が返される**。技術プロファイルに [表示コントロール](display-controls.md)への参照を含めることができます。 表示コントロールによって、検証済みの電子メール アドレスなどの要求が返されます。 要求をバブルアップして、ユーザー体験の次のオーケストレーション ステップに戻すことができます。 表示制御機能は、現在、**プレビュー** 段階です。

次の例では、表示要求と出力要求の両方を使用するセルフアサート技術プロファイルの使用法を示します。

```xml
<TechnicalProfile Id="LocalAccountSignUpWithLogonEmail">
  <DisplayName>Email signup</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.SelfAssertedAttributeProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <Metadata>
    <Item Key="IpAddressClaimReferenceId">IpAddress</Item>
    <Item Key="ContentDefinitionReferenceId">api.localaccountsignup</Item>
    <Item Key="language.button_continue">Create</Item>
  </Metadata>
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="email" />
  </InputClaims>
  <DisplayClaims>
    <DisplayClaim DisplayControlReferenceId="emailVerificationControl" />
    <DisplayClaim DisplayControlReferenceId="SecondaryEmailVerificationControl" />
    <DisplayClaim ClaimTypeReferenceId="displayName" Required="true" />
    <DisplayClaim ClaimTypeReferenceId="givenName" Required="true" />
    <DisplayClaim ClaimTypeReferenceId="surName" Required="true" />
    <DisplayClaim ClaimTypeReferenceId="newPassword" Required="true" />
    <DisplayClaim ClaimTypeReferenceId="reenterPassword" Required="true" />
  </DisplayClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="email" Required="true" />
    <OutputClaim ClaimTypeReferenceId="objectId" />
    <OutputClaim ClaimTypeReferenceId="executed-SelfAsserted-Input" DefaultValue="true" />
    <OutputClaim ClaimTypeReferenceId="authenticationSource" />
    <OutputClaim ClaimTypeReferenceId="newUser" />
  </OutputClaims>
  <ValidationTechnicalProfiles>
    <ValidationTechnicalProfile ReferenceId="AAD-UserWriteUsingLogonEmail" />
  </ValidationTechnicalProfiles>
  <UseTechnicalProfileForSessionManagement ReferenceId="SM-AAD" />
</TechnicalProfile>
```

### <a name="output-claims-sign-up-or-sign-in-page"></a>出力要求サインアップまたはサインイン ページ

結合されたサインアップおよびサインイン ページで、`unifiedssp` または `unifiedssd` のページの種類を指定するコンテンツ定義 [DataUri](contentdefinitions.md#datauri) 要素を使用する場合、次の点に注意してください。

- ユーザー名とパスワードの要求のみがレンダリングされます。
- 最初の 2 つの出力要求は、ユーザー名とパスワード (この順序で) である必要があります。 
- その他の要求はすべてレンダリングされません。これらの要求については、`defaultValue` を設定するか、要求フォーム検証技術プロファイルを呼び出す必要があります。 

## <a name="persist-claims"></a>永続化した要求

PersistedClaims 要素は使用されません。 セルフ アサート技術プロファイルでは、データが Azure AD B2C に保持されません。 代わりに、データの永続化を担当する検証技術プロファイルが呼び出されます。 たとえば、サインアップ ポリシーは、新しいユーザー プロファイルを収集するために`LocalAccountSignUpWithLogonEmail`セルフ アサート技術プロファイルを使用します。 `LocalAccountSignUpWithLogonEmail`技術プロファイルは、Azure AD B2C でアカウントを作成するには、検証技術プロファイルを呼び出します。

## <a name="validation-technical-profiles"></a>検証技術プロファイル

検証技術プロファイルは、参照元の技術プロファイルの出力要求の一部またはすべてを検証するために使用されます。 検証技術プロファイルの入力要求は、セルフ アサート技術プロファイルの出力要求に表示される必要があります。 検証技術プロファイルは、ユーザー入力を検証し、ユーザーにエラーを返すことができます。

検証技術プロファイルは、[Azure Active Directory](active-directory-technical-profile.md) や [REST API](restful-technical-profile.md) 技術プロファイルなど、ポリシー内の任意の技術プロファイルである場合があります。 前の例では、`LocalAccountSignUpWithLogonEmail`技術プロファイルは、signinName がディレクトリに存在しないことを検証します。 検証されていない場合、検証技術プロファイルは、ローカル アカウントを作成し、objectId、authenticationSource、新規ユーザーを返します。 `SelfAsserted-LocalAccountSignin-Email`技術プロファイルは、`login-NonInteractive`検証技術プロファイルを呼び出してユーザーの資格情報を検証します。

ビジネス ロジックで REST API の技術プロファイルを呼び出したり、入力要求を上書きします。または企業の基幹業務アプリケーションをさらに統合することによってユーザー データを充実することができます。 詳細については、[検証技術プロファイル](validation-technical-profile.md)を参照してください

## <a name="metadata"></a>Metadata

| 属性 | 必須 | 説明 |
| --------- | -------- | ----------- |
| setting.operatingMode <sup>1</sup>| いいえ | サインイン ページのために、このプロパティは、入力検証とエラー メッセージなど [ユーザー名] フィールドの動作を制御します。 期待される値は`Username`または`Email`です。  |
| AllowGenerationOfClaimsWithNullValues| いいえ| null 値を含む要求を生成することを許可します。 たとえば、ユーザーがチェックボックスをオンにしていない場合などです。|
| ContentDefinitionReferenceId | はい | [コンテンツ定義](contentdefinitions.md)の識別子は、この技術プロファイルに関連付けられています。 |
| EnforceEmailVerification | いいえ | サインアップまたはプロファイルの編集のために、電子メールの検証を強制します。 指定できる値は `true`(既定値) または`false`です。 |
| setting.retryLimit | いいえ | ユーザーが検証技術プロファイルに対してチェックされたデータを提供しようとする回数を制御します。 たとえば、ユーザーは既に存在するアカウントでサインアップしようとし、制限に達するまで試行し続けます。
| SignUpTarget <sup>1</sup>| いいえ | サインアップ対象交換識別子。 ユーザーがサインアップ ボタンをクリックすると、Azure AD B2C は、指定された交換識別子を実行します。 |
| setting.showCancelButton | いいえ | [キャンセル] ボタンが表示されます。 指定できる値は `true`(既定値) または`false`です。 |
| setting.showContinueButton | いいえ | [続行する] ボタンが表示されます。 指定できる値は `true`(既定値) または`false`です。 |
| setting.showSignupLink <sup>2</sup>| いいえ | サインアップ ボタンが表示されます。 指定できる値は `true`(既定値) または`false`です。 |
| setting.forgotPasswordLinkLocation <sup>2</sup>| いいえ| パスワードを忘れた場合のリンクを表示します。 使用可能な値: `AfterLabel` (既定) では、ラベルの直後にリンクが表示されます。ラベルがない場合はパスワード入力フィールドの後にリンクが表示されます。`AfterInput` では、パスワード入力フィールドの後にリンクが表示されます。`AfterButtons` では、フォーム下部のボタンの後にリンクが表示されます。`None` では、パスワードを忘れた場合のリンクは削除されます。|
| setting.enableRememberMe <sup>2</sup>| いいえ| [[サインインしたままにする]](session-behavior.md?pivots=b2c-custom-policy#enable-keep-me-signed-in-kmsi) チェックボックスを表示します。 指定できる値: `true` または `false` (既定値)。 |
| setting.inputVerificationDelayTimeInMilliseconds <sup>3</sup>| いいえ| ユーザーが入力を停止するのを待って、値を検証することで、ユーザー エクスペリエンスを向上させます。 既定値は、2000 ミリ秒です。 |
| IncludeClaimResolvingInClaimsHandling  | いいえ | 入力と出力の要求について、[要求の解決](claim-resolver-overview.md)を技術プロファイルに含めるかどうかを指定します。 指定できる値: `true` または `false` (既定値)。 技術プロファイルで要求リゾルバーを使用する場合は、これを `true` に設定します。 |
|forgotPasswordLinkOverride <sup>4</sup>| いいえ | 実行されるパスワード リセット要求の交換。 詳細については、「[セルフサービス パスワード リセット](add-password-reset-policy.md)」を参照してください。 |

メモ:
1. コンテンツ定義 [DataUri](contentdefinitions.md#datauri) `unifiedssp`または`unifiedssd`の種類に使用できます。
1. コンテンツ定義 [DataUri](contentdefinitions.md#datauri) `unifiedssp`または`unifiedssd`の種類に使用できます。 [ページ レイアウトバージョン](page-layout.md) 1.1.0 以降。
1. [ページ レイアウト バージョン](page-layout.md) 1.2.0 以降に使用できます。
1. コンテンツ定義 [DataUri](contentdefinitions.md#datauri) `unifiedssp` の種類に使用できます。 [ページ レイアウト バージョン](page-layout.md) 2.1.2 以降。

## <a name="cryptographic-keys"></a>暗号化キー

**CryptographicKeys** 要素は使用されません。
