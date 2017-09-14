---
title: "Azure Active Directory B2C: カスタム ポリシーのサインアップを変更し、セルフ アサート プロバイダーを構成する"
description: "サインアップに要求を追加し、ユーザー入力を構成するチュートリアル"
services: active-directory-b2c
documentationcenter: 
author: rojasja
manager: krassk
editor: tbd
ms.assetid: 
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: article
ms.devlang: na
ms.date: 04/29/2017
ms.author: joroja
ms.translationtype: Human Translation
ms.sourcegitcommit: d9ae8e8948d82b9695d7d144d458fe8180294084
ms.openlocfilehash: 8731ff3a42e12d145bc259597812aded8333a095
ms.contentlocale: ja-jp
ms.lasthandoff: 05/23/2017

---
# <a name="azure-active-directory-b2c-modify-sign-up-to-add-new-claims-and-configure-user-input"></a>Azure Active Directory B2C: 新しい要求を追加するようにサインアップを変更し、ユーザー入力を構成する

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

この記事では、新しいユーザー指定のエントリ (要求) をサインアップ ユーザー体験に追加します。  エントリをドロップダウン リストとして構成し、必要に応じて定義します。

## <a name="prerequisites"></a>前提条件

* [カスタム ポリシーの概要](active-directory-b2c-get-started-custom.md)に関する記事の手順を完了します。  先に進む前に、新しいローカル アカウントのサインアップのためのサインアップ/サインイン ユーザー体験をテストします。


ユーザーからの初期データの収集は、サインアップ/サインインを通じて行われます。  追加の要求は、後でプロファイル編集のユーザー体験を通じて収集できます。 Azure AD B2C は、いつでもユーザーから情報を対話的に直接収集します。Identity Experience Framework は、その `selfasserted provider` を使用します。 次の手順は、このプロバイダーが使用される場合は常に必要です。


## <a name="define-the-claim-its-display-name-and-the-user-input-type"></a>要求、その表示名、およびユーザー入力タイプを定義する
ユーザーに市区町村の入力を求めることにしましょう。  TrustFrameWorkExtensions ポリシー ファイル内の `<ClaimsSchema>` 要素に以下の要素を追加します。

```xml
<ClaimType Id="city">
  <DisplayName>city</DisplayName>
  <DataType>string</DataType>
  <UserHelpText>Your city</UserHelpText>
  <UserInputType>TextBox</UserInputType>
</ClaimType>
```
要求をカスタマイズするために、ここで追加の選択を行うことができます。  スキーマ全体については、**Identity Experience Framework テクニカル リファレンス ガイド**を参照してください。  このガイドは、リファレンス セクションでもうすぐ公開されます。

* `<DisplayName>` は、ユーザーに表示される "*ラベル*" を定義する文字列です。

* `<UserHelpText>` は、必要なものをユーザーが理解するために役立ちます。

* `<UserInputType>` には、以下に強調表示されている 4 つのオプションがあります。
    * `TextBox`
```xml
<ClaimType Id="city">
  <DisplayName>city where you work</DisplayName>
  <DataType>string</DataType>
  <UserHelpText>Your city</UserHelpText>
  <UserInputType>TextBox</UserInputType>
</ClaimType>
```

    * `RadioSingleSelectduration` - 単一の選択を強制します。
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

    * `DropdownSingleSelect` - 有効な値の選択だけを許可します。

![ドロップダウン リストのオプションのスクリーン ショット](./media/active-directory-b2c-configure-signup-self-asserted-custom/dropdown-menu-example.png)


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


* `CheckboxMultiSelect` 1 つまたは複数の値の選択を許可します。

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

## <a name="add-the-claim-to-the-sign-upsign-in-user-journey"></a>サインアップ/サインイン ユーザー体験に要求を追加する

1. 要求を `<OutputClaim ClaimTypeReferenceId="city"/>` として TechnicalProfile `LocalAccountSignUpWithLogonEmail` (TrustFrameworkBase ポリシー ファイル内にあります) に追加します。  この TechnicalProfile は SelfAssertedAttributeProvider を使用することに注意してください。

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

2. 要求を AAD-UserWriteUsingLogonEmail に `<PersistedClaim ClaimTypeReferenceId="city" />` として追加し、要求をユーザーから収集した後で、それを AAD ディレクトリに書き込みます。 後で使用するために要求をディレクトリ内に永続的に保存することをしない場合は、この手順を省略できます。

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

3. ユーザーがログインするときにディレクトリから読み取られる TechnicalProfile に、要求を `<OutputClaim ClaimTypeReferenceId="city" />` として追加します。

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

4. ユーザー体験が成功した後、要求がトークンに含められてアプリケーションに送信されるように、`<OutputClaim ClaimTypeReferenceId="city" />` を RP ポリシー ファイル SignUporSignIn.xml に追加します。

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

## <a name="test-the-custom-policy-using-run-now"></a>"Run Now" を使用してカスタム ポリシーをテストする

1. **[Azure AD B2C] ブレード**を開き、**[Identity Experience Framework] > [カスタム ポリシー]** に移動します。
2. アップロードしたカスタム ポリシーを選択し、**[Run now]**(今すぐ実行) ボタンをクリックします。
3. メール アドレスを使用してサインアップできることを確認します。

テスト モードのサインアップ画面は、次のようになります。

![変更されたサインアップ オプションのスクリーンショット](./media/active-directory-b2c-configure-signup-self-asserted-custom/signup-with-city-claim-dropdown-example.png)

  アプリケーションに返されるトークンには、以下に示すように、`city` 要求が含まれるようになりました。
```json
{
  "exp": 1493596822,
  "nbf": 1493593222,
  "ver": "1.0",
  "iss": "https://login.microsoftonline.com/f06c2fe8-709f-4030-85dc-38a4bfd9e82d/v2.0/",
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

## <a name="optional-remove-email-verification-from-signup-journey"></a>オプション: サインアップ体験から電子メール確認を削除する

電子メールによる確認を省略するために、ポリシー作成者は `PartnerClaimType="Verified.Email"` を削除することができます。 "Required" = true が削除されていない限り、メール アドレスは必須ですが、確認はされません。  自分のユース ケースでこのオプションが適切かどうかを慎重に検討してください。

スターター パックの TrustFrameworkBase ポリシー ファイルの `<TechnicalProfile Id="LocalAccountSignUpWithLogonEmail">` で、電子メールの確認は既定で有効になっています。
```xml
<OutputClaim ClaimTypeReferenceId="email" PartnerClaimType="Verified.Email" Required="true" />
```

## <a name="next-steps"></a>次のステップ

以下に示す TechnicalProfile を変更することで、新しい要求をソーシャル アカウント ログインのフローに追加します。 これらは、ロケーターとして alternativeSecurityId を使用してユーザー データの書き込みと読み取りを行うために、ソーシャル/フェデレーション アカウント ログインで使用されます。
```xml
<TechnicalProfile Id="AAD-UserWriteUsingAlternativeSecurityId">
<TechnicalProfile Id="AAD-UserReadUsingAlternativeSecurityId">
```

