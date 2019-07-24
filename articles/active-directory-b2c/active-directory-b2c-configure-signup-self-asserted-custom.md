---
title: カスタム ポリシーを使用した要求の追加とユーザー入力のカスタマイズ - Azure Active Directory B2C | Microsoft Docs
description: ユーザー入力をカスタマイズし、Azure Active Directory B2C のサインアップまたはサインイン体験に要求を追加する方法について説明します。
services: active-directory-b2c
author: davidmu1
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 02/07/2019
ms.author: davidmu
ms.subservice: B2C
ms.openlocfilehash: e3eb7ce49c5d22b11b0b6f018e025bdfa2a67703
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/06/2019
ms.locfileid: "57432007"
---
#  <a name="add-claims-and-customize-user-input-using-custom-policies-in-azure-active-directory-b2c"></a>Azure Active Directory B2C のカスタム ポリシーを使用した要求の追加とユーザー入力のカスタマイズ - | Microsoft Docs

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

この記事では、新しいユーザー指定のエントリ (要求) を Azure Active Directory (Azure AD) B2C のサインアップ ユーザー体験に追加します。  エントリをドロップダウン リストとして構成し、必要に応じて定義します。

## <a name="prerequisites"></a>前提条件

[カスタム ポリシーの概要](active-directory-b2c-get-started-custom.md)に関する記事の手順を完了します。 先に進む前に、ローカル アカウントの新規登録のためのサインアップまたはサインイン ユーザー体験をテストします。

## <a name="add-claims"></a>要求の追加

ユーザーからの初期データの収集は、サインアップまたはサインイン ユーザー体験を使用して実現されます。 追加の要求は、後でプロファイル編集のユーザー体験を使用して収集できます。 Azure AD B2C は、いつでもユーザーから情報を対話的に直接収集します。Identity Experience Framework は、その selfasserted プロバイダーを使用します。


### <a name="define-the-claim"></a>要求の定義

ユーザーに市区町村の入力を求めることにしましょう。 TrustFrameworkBase ポリシー ファイル内の **ClaimsSchema** 要素に次の要素を追加します。

```xml
<ClaimType Id="city">
  <DisplayName>city</DisplayName>
  <DataType>string</DataType>
  <UserHelpText>Your city</UserHelpText>
  <UserInputType>TextBox</UserInputType>
</ClaimType>
```

要求の定義には次の要素が使用されます。

- **DisplayName** - ユーザーに表示される "ラベル" を定義する文字列です。
- **UserHelpText** - 必要なものをユーザーが理解するために役立ちます。
- **UserInputType** - テキスト ボックス、ラジオ選択、ドロップダウン リスト、または複数選択を使用できます。

#### <a name="textbox"></a>TextBox

```xml
<ClaimType Id="city">
  <DisplayName>city where you work</DisplayName>
  <DataType>string</DataType>
  <UserHelpText>Your city</UserHelpText>
  <UserInputType>TextBox</UserInputType>
</ClaimType>
```

#### <a name="radiosingleselect"></a>RadioSingleSelect

```xml
<ClaimType Id="city">
  <DisplayName>city where you work</DisplayName>
  <DataType>string</DataType>
  <UserInputType>RadioSingleSelect</UserInputType>
  <Restriction>
    <Enumeration Text="Bellevue" Value="bellevue" SelectByDefault="false" />
    <Enumeration Text="Redmond" Value="redmond" SelectByDefault="false" />
    <Enumeration Text="Kirkland" Value="kirkland" SelectByDefault="false" />
  </Restriction>
</ClaimType>
```

#### <a name="dropdownsingleselect"></a>DropdownSingleSelect

![ドロップダウン リストのオプションのスクリーンショット](./media/active-directory-b2c-configure-signup-self-asserted-custom/dropdown-menu-example.png)

```xml
<ClaimType Id="city">
  <DisplayName>city where you work</DisplayName>
  <DataType>string</DataType>
  <UserInputType>DropdownSingleSelect</UserInputType>
  <Restriction>
    <Enumeration Text="Bellevue" Value="bellevue" SelectByDefault="false" />
    <Enumeration Text="Redmond" Value="redmond" SelectByDefault="false" />
    <Enumeration Text="Kirkland" Value="kirkland" SelectByDefault="false" />
  </Restriction>
</ClaimType>
```

#### <a name="checkboxmultiselect"></a>CheckboxMultiSelect

![複数選択オプションのスクリーンショット](./media/active-directory-b2c-configure-signup-self-asserted-custom/multiselect-menu-example.png)

```xml
<ClaimType Id="city">
  <DisplayName>Receive updates from which cities?</DisplayName>
  <DataType>string</DataType>
  <UserInputType>CheckboxMultiSelect</UserInputType>
  <Restriction>
    <Enumeration Text="Bellevue" Value="bellevue" SelectByDefault="false" />
    <Enumeration Text="Redmond" Value="redmond" SelectByDefault="false" />
    <Enumeration Text="Kirkland" Value="kirkland" SelectByDefault="false" />
  </Restriction>
</ClaimType>
```

### <a name="add-the-claim-to-the-user-journey"></a>ユーザー体験に要求を追加する

1. 要求を、TrustFrameworkBase ポリシー ファイル内にある技術プロファイル `LocalAccountSignUpWithLogonEmail` に `<OutputClaim ClaimTypeReferenceId="city"/>` として追加します。 この技術プロファイルでは SelfAssertedAttributeProvider を使用されます。

    ```xml
    <TechnicalProfile Id="LocalAccountSignUpWithLogonEmail">
      <DisplayName>Email signup</DisplayName>
      <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.SelfAssertedAttributeProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
      <Metadata>
        <Item Key="IpAddressClaimReferenceId">IpAddress</Item>
        <Item Key="ContentDefinitionReferenceId">api.localaccountsignup</Item>
        <Item Key="language.button_continue">Create</Item>
      </Metadata>
      <CryptographicKeys>
        <Key Id="issuer_secret" StorageReferenceId="TokenSigningKeyContainer" />
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
        <OutputClaim ClaimTypeReferenceId="givenName" />
        <OutputClaim ClaimTypeReferenceId="surName" />
        <OutputClaim ClaimTypeReferenceId="city"/>
      </OutputClaims>
      <ValidationTechnicalProfiles>
        <ValidationTechnicalProfile ReferenceId="AAD-UserWriteUsingLogonEmail" />
      </ValidationTechnicalProfiles>
      <UseTechnicalProfileForSessionManagement ReferenceId="SM-AAD" />
    </TechnicalProfile>
    ```

2. 要求を技術プロファイル AAD-UserWriteUsingLogonEmail に `<PersistedClaim ClaimTypeReferenceId="city" />` として追加し、要求をユーザーから収集した後で、それを AAD ディレクトリに書き込みます。 後で使用するために要求をディレクトリ内に永続的に保存することをしない場合は、この手順を省略できます。

    ```xml
    <!-- Technical profiles for local accounts -->
    <TechnicalProfile Id="AAD-UserWriteUsingLogonEmail">
      <Metadata>
        <Item Key="Operation">Write</Item>
        <Item Key="RaiseErrorIfClaimsPrincipalAlreadyExists">true</Item>
      </Metadata>
      <IncludeInSso>false</IncludeInSso>
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="email" PartnerClaimType="signInNames.emailAddress" Required="true" />
      </InputClaims>
      <PersistedClaims>
        <!-- Required claims -->
        <PersistedClaim ClaimTypeReferenceId="email" PartnerClaimType="signInNames.emailAddress" />
        <PersistedClaim ClaimTypeReferenceId="newPassword" PartnerClaimType="password" />
        <PersistedClaim ClaimTypeReferenceId="displayName" DefaultValue="unknown" />
        <PersistedClaim ClaimTypeReferenceId="passwordPolicies" DefaultValue="DisablePasswordExpiration" />
        <!-- Optional claims. -->
        <PersistedClaim ClaimTypeReferenceId="givenName" />
        <PersistedClaim ClaimTypeReferenceId="surname" />
        <PersistedClaim ClaimTypeReferenceId="city" />
      </PersistedClaims>
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="objectId" />
        <OutputClaim ClaimTypeReferenceId="newUser" PartnerClaimType="newClaimsPrincipalCreated" />
        <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="localAccountAuthentication" />
        <OutputClaim ClaimTypeReferenceId="userPrincipalName" />
        <OutputClaim ClaimTypeReferenceId="signInNames.emailAddress" />
      </OutputClaims>
      <IncludeTechnicalProfile ReferenceId="AAD-Common" />
      <UseTechnicalProfileForSessionManagement ReferenceId="SM-AAD" />
    </TechnicalProfile>
    ```

3. ユーザーがサインインするときにディレクトリから読み取られる技術プロファイルに、`<OutputClaim ClaimTypeReferenceId="city" />` 要求を追加します。

    ```xml
    <TechnicalProfile Id="AAD-UserReadUsingEmailAddress">
      <Metadata>
        <Item Key="Operation">Read</Item>
        <Item Key="RaiseErrorIfClaimsPrincipalDoesNotExist">true</Item>
        <Item Key="UserMessageIfClaimsPrincipalDoesNotExist">An account could not be found for the provided user ID.</Item>
      </Metadata>
      <IncludeInSso>false</IncludeInSso>
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="email" PartnerClaimType="signInNames" Required="true" />
      </InputClaims>
      <OutputClaims>
        <!-- Required claims -->
        <OutputClaim ClaimTypeReferenceId="objectId" />
        <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="localAccountAuthentication" />
        <!-- Optional claims -->
        <OutputClaim ClaimTypeReferenceId="userPrincipalName" />
        <OutputClaim ClaimTypeReferenceId="displayName" />
        <OutputClaim ClaimTypeReferenceId="otherMails" />
        <OutputClaim ClaimTypeReferenceId="signInNames.emailAddress" />
        <OutputClaim ClaimTypeReferenceId="city" />
      </OutputClaims>
      <IncludeTechnicalProfile ReferenceId="AAD-Common" />
    </TechnicalProfile>
    ```

    ```xml
    <TechnicalProfile Id="AAD-UserReadUsingObjectId">
      <Metadata>
        <Item Key="Operation">Read</Item>
        <Item Key="RaiseErrorIfClaimsPrincipalDoesNotExist">true</Item>
      </Metadata>
      <IncludeInSso>false</IncludeInSso>
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="objectId" Required="true" />
      </InputClaims>
      <OutputClaims>
        <!-- Optional claims -->
        <OutputClaim ClaimTypeReferenceId="signInNames.emailAddress" />
        <OutputClaim ClaimTypeReferenceId="displayName" />
        <OutputClaim ClaimTypeReferenceId="otherMails" />
        <OutputClaim ClaimTypeReferenceId="givenName" />
        <OutputClaim ClaimTypeReferenceId="city" />
      </OutputClaims>
      <IncludeTechnicalProfile ReferenceId="AAD-Common" />
    </TechnicalProfile>
    ```
   
4. ユーザー体験が成功した後、要求がトークンに含められてアプリケーションに送信されるように、`<OutputClaim ClaimTypeReferenceId="city" />` 要求を SignUporSignIn.xml ファイルに追加します。

    ```xml
    <RelyingParty>
      <DefaultUserJourney ReferenceId="SignUpOrSignIn" />
      <TechnicalProfile Id="PolicyProfile">
        <DisplayName>PolicyProfile</DisplayName>
        <Protocol Name="OpenIdConnect" />
        <OutputClaims>
          <OutputClaim ClaimTypeReferenceId="displayName" />
          <OutputClaim ClaimTypeReferenceId="givenName" />
          <OutputClaim ClaimTypeReferenceId="surname" />
          <OutputClaim ClaimTypeReferenceId="email" />
          <OutputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="sub"/>
          <OutputClaim ClaimTypeReferenceId="identityProvider" />
          <OutputClaim ClaimTypeReferenceId="city" />
        </OutputClaims>
        <SubjectNamingInfo ClaimType="sub" />
      </TechnicalProfile>
    </RelyingParty>
    ```

## <a name="test-the-custom-policy"></a>カスタム ポリシーをテストする

1. [Azure Portal](https://portal.azure.com) にサインインします。
2. お使いの Azure AD テナントを含むディレクトリを使用していることを確認してください。確認のために、トップ メニューにある **[ディレクトリとサブスクリプション フィルター]** をクリックして、お使いの Azure AD テナントを含むディレクトリを選択します。
3. Azure portal の左上隅にある **[すべてのサービス]** を選択し、**[アプリの登録]** を検索して選択します。
4. **[Identity Experience Framework (プレビュー)]** を選択します。
5. **[カスタム ポリシーのアップロード]** を選択し、変更した 2 つのポリシー ファイルをアップロードします。
2. アップロードしたサインアップまたはサインイン ポリシーを選択し、**[今すぐ実行]** ボタンをクリックします。
3. メール アドレスを使用してサインアップできることを確認します。

サインアップ画面は、次のようになります。

![変更されたサインアップ オプションのスクリーンショット](./media/active-directory-b2c-configure-signup-self-asserted-custom/signup-with-city-claim-dropdown-example.png)

アプリケーションに返送されるトークンには、`city` 要求が含まれています。

```json
{
  "exp": 1493596822,
  "nbf": 1493593222,
  "ver": "1.0",
  "iss": "https://contoso.b2clogin.com/f06c2fe8-709f-4030-85dc-38a4bfd9e82d/v2.0/",
  "sub": "9c2a3a9e-ac65-4e46-a12d-9557b63033a9",
  "aud": "4e87c1dd-e5f5-4ac8-8368-bc6a98751b8b",
  "acr": "b2c_1a_trustf_signup_signin",
  "nonce": "defaultNonce",
  "iat": 1493593222,
  "auth_time": 1493593222,
  "email": "joe@outlook.com",
  "given_name": "Joe",
  "family_name": "Ras",
  "city": "Bellevue",
  "name": "unknown"
}
```

## <a name="optional-remove-email-verification"></a>省略可能:メールの確認を削除する

電子メールによる確認を省略するために、`PartnerClaimType="Verified.Email"` を削除することができます。 その場合、"Required" = true が削除されていない限り、メール アドレスは必須ですが、確認はされません。  自分のユース ケースでこのオプションが適切かどうかを慎重に検討してください。

TrustFrameworkBase ポリシー ファイルの `<TechnicalProfile Id="LocalAccountSignUpWithLogonEmail">` で、電子メールの確認は既定で有効になっています。

```xml
<OutputClaim ClaimTypeReferenceId="email" PartnerClaimType="Verified.Email" Required="true" />
```

## <a name="next-steps"></a>次の手順

[カスタム プロファイル編集ポリシーでカスタム属性を使用する](active-directory-b2c-create-custom-attributes-profile-edit-custom.md)方法について確認する。
