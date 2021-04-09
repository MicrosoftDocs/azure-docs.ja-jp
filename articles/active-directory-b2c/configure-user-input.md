---
title: ユーザー属性を追加してユーザー入力をカスタマイズする
titleSuffix: Azure AD B2C
description: ユーザー入力をカスタマイズし、Azure Active Directory B2C のサインアップまたはサインイン体験にユーザー属性を追加する方法について説明します。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 03/10/2021
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: 4e709719d56aacacf61e247a5dbe215f766a891a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "102607953"
---
#  <a name="add-user-attributes-and-customize-user-input-in-azure-active-directory-b2c"></a>Azure Active Directory B2C でユーザー属性を追加してユーザー入力をカスタマイズする

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

::: zone pivot="b2c-custom-policy"

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

::: zone-end

この記事では、Azure Active Directory B2C (Azure AD B2C) でのサインアップ体験時に新しい属性を収集します。 ユーザーの city (市区町村) を取得し、それをドロップダウンとして構成し、それを指定することが必須かどうかを定義します。

## <a name="prerequisites"></a>前提条件

[!INCLUDE [active-directory-b2c-customization-prerequisites](../../includes/active-directory-b2c-customization-prerequisites.md)]

::: zone pivot="b2c-user-flow"

## <a name="add-user-attributes-your-user-flow"></a>ユーザー属性をユーザー フローに追加する

1. Azure AD B2C テナントで、 **[ユーザー フロー]** を選択します。
1. ポリシー ("B2C_1_SignupSignin" など) を選択して開きます。
1. **[ユーザー属性]** を選択してから、ユーザー属性 ("City" など) を選択します。 
1. **[保存]** を選択します。

## <a name="provide-optional-claims-to-your-app"></a>アプリに省略可能な要求を提供する

アプリケーション要求は、アプリケーションに返される値です。 ユーザー フローを更新して、目的の要求を含めます。

1. ポリシー ("B2C_1_SignupSignin" など) を選択して開きます。
1. **[アプリケーション クレーム]** を選択します。
1. アプリケーションに返信する属性を選択します ("City" など)。
1. **[保存]** を選択します。
 
## <a name="configure-user-attributes-input-type"></a>ユーザー属性の入力の種類を構成する

1. ポリシー ("B2C_1_SignupSignin" など) を選択して開きます。
1. **[Page layouts]\(ページ レイアウト\)** を選択します。
1. **[ローカル アカウント サインアップ ページ]** を選択します。
1. **[ユーザー属性]** で **[City]** を選択します。
    1. **[ユーザー入力の種類]** ドロップダウンで、 **[DropdownSingleSelect]** を選択します。
    1. **[省略可能]** ドロップダウンで、 **[なし]** を選択します。
1. **[保存]** を選択します。 

### <a name="provide-a-list-of-values-by-using-localized-collections"></a>ローカライズされたコレクションを使って値の一覧を提供する

city 属性の値のセットの一覧を指定するには、次のようにします。 

1. [ユーザー フローで言語のカスタマイズを有効にする](language-customization.md#support-requested-languages-for-ui_locales)
1. ポリシー ("B2C_1_SignupSignin" など) を選択して開きます。
1. ユーザー フローの **[言語]** ページで、カスタマイズする言語を選択します。
1. **[Page-level-resources files]\(ページ レベル リソース ファイル\)** で、 **[ローカル アカウントのサインアップ ページ]** を選択します。
1. **[既定値のダウンロード]** (この言語を前に編集した場合は **[オーバーライドのダウンロード]** ) を選びます。
1. `LocalizedCollections` 属性を作成します。

`LocalizedCollections` は、`Name` と `Value` のペアの配列です。 項目の順序は、表示される順序になります。 

* `ElementId` は、この `LocalizedCollections` 属性が応答になるユーザー属性です。
* `Name` は、ユーザーに表示される値です。
* `Value` は、このオプションが選択された場合に要求で返される値です。

```json
{
  "LocalizedStrings": [...],
  "LocalizedCollections": [
    {
      "ElementType": "ClaimType",
      "ElementId": "city",
      "TargetCollection": "Restriction",
      "Override": false,
      "Items": [
        {
          "Name": "Berlin",
          "Value": "Berlin"
        },
        {
          "Name": "London",
          "Value": "London"
        },
        {
          "Name": "Seattle",
          "Value": "Seattle"
        }
      ]
    }
  ]
}
```

#### <a name="upload-your-changes"></a>変更のアップロード

1. JSON ファイルの変更が完了したら、B2C テナントに戻ります。
1. **[ユーザー フロー]** を選択し、ポリシー ("B2C_1_SignupSignin" など) を選択して開きます。
1. **[言語]** を選択します。
1. 翻訳する言語を選びます。
1. **[ローカル アカウント サインアップ ページ]** を選択します。
1. フォルダー アイコンを選び、アップロードする JSON ファイルを選びます。 変更がユーザー フローに自動的に保存されます。

## <a name="test-your-user-flow"></a>ユーザー フローをテストする

1. ポリシー ("B2C_1_SignupSignin" など) を選択して開きます。
1. ポリシーをテストするには、 **[ユーザー フローを実行します]** を選択します。
1. **[アプリケーション]** には、以前に登録した *testapp1* という名前の Web アプリケーションを選択します。 **[応答 URL]** に `https://jwt.ms` と表示されます。
1. **[ユーザー フローを実行します]** をクリックします

::: zone-end

::: zone pivot="b2c-custom-policy"

> [!NOTE]
> このサンプルでは、組み込みの要求の "city" を使用します。 代わりに、サポートされている [Azure AD B2C 組み込み属性](user-profile-attributes.md)のいずれか、またはカスタム属性を選択することもできます。 カスタム属性を使用するには、[カスタム属性を有効にします](user-flow-custom-attributes.md)。 別の組み込みまたはカスタム属性を使用するには、選択した属性で "city" を置き換えます。たとえば、組み込み属性 *jobTitle* や、*extension_loyaltyId* のようなカスタム属性を使用できます。  

ユーザーからの初期データは、サインアップまたはサインインのユーザー体験を使用して収集できます。 追加の要求は、後でプロファイル編集のユーザー体験を使用して収集できます。 Azure AD B2C が対話形式でユーザーから直接情報を収集するときはいつでも、Identity Experience Framework は[セルフアサート技術プロファイル](self-asserted-technical-profile.md)を使用します。 このサンプルでは、以下を実行します。

1. "city" 要求を定義します。 
1. ユーザーに city を尋ねます。
1. Azure AD B2C ディレクトリのユーザー プロファイルに city を保持します。
1. サインインのたびに、Azure AD B2C ディレクトリから city 要求を読み取ります。
1. サインインまたはサインアップ後に、証明書利用者アプリケーションに city を返します。  

## <a name="define-a-claim"></a>要求を定義する

要求は、Azure AD B2C ポリシーの実行中に、データの一時的なストレージを提供します。 [要求スキーマ](claimsschema.md)は、要求を宣言する場所です。 要求の定義には次の要素が使用されます。

- **DisplayName** - ユーザーに表示される "ラベル" を定義する文字列です。
- [DataType](claimsschema.md#datatype) - 要求の種類です。
- **UserHelpText** - 必要なものをユーザーが理解するために役立ちます。
- [UserInputType](claimsschema.md#userinputtype) - テキスト ボックス、ラジオ選択、ドロップダウン リスト、または複数選択などの入力コントロールの種類。

お使いのポリシーの拡張ファイルを開きます。 たとえば、<em>`SocialAndLocalAccounts/`**`TrustFrameworkExtensions.xml`**</em>です。

1. [BuildingBlocks](buildingblocks.md) 要素を検索します。 要素が存在しない場合は追加します。
1. [ClaimsSchema](claimsschema.md) 要素を見つけます。 要素が存在しない場合は追加します。
1. **ClaimsSchema** 要素に city 要求を追加します。  

```xml
<!-- 
<BuildingBlocks>
  <ClaimsSchema> -->
    <ClaimType Id="city">
      <DisplayName>City where you work</DisplayName>
      <DataType>string</DataType>
      <UserInputType>DropdownSingleSelect</UserInputType>
      <Restriction>
        <Enumeration Text="Bellevue" Value="bellevue" SelectByDefault="false" />
        <Enumeration Text="Redmond" Value="redmond" SelectByDefault="false" />
        <Enumeration Text="Kirkland" Value="kirkland" SelectByDefault="false" />
      </Restriction>
    </ClaimType>
  <!-- 
  </ClaimsSchema>
</BuildingBlocks>-->
```

## <a name="add-a-claim-to-the-user-interface"></a>ユーザー インターフェイスに要求を追加する

次の技術プロファイルは、ユーザーが入力を提供すると予期されているときに呼び出される、[セルフアサート](self-asserted-technical-profile.md)です。

- **LocalAccountSignUpWithLogonEmail** - ローカル アカウントのサインアップ フロー。
- **SelfAsserted-Social** - フェデレーション アカウントの初回ユーザー サインイン。
- **SelfAsserted-ProfileUpdate** - プロファイルの編集フロー。

サインアップ時に city 要求を収集するには、それを出力要求として `LocalAccountSignUpWithLogonEmail` 技術プロファイルに追加する必要があります。 拡張ファイル内のこの技術プロファイルをオーバーライドします。 要求が画面に表示される順序を制御するには、出力要求の一覧全体を指定します。 **ClaimsProviders** 要素を見つけます。 新しい ClaimsProvider を次のように追加します。

```xml
<ClaimsProvider>
  <DisplayName>Local Account</DisplayName>
  <TechnicalProfiles>
    <!--Local account sign-up page-->
    <TechnicalProfile Id="LocalAccountSignUpWithLogonEmail">
      <OutputClaims>
       <OutputClaim ClaimTypeReferenceId="email" PartnerClaimType="Verified.Email" Required="true" />
       <OutputClaim ClaimTypeReferenceId="newPassword" Required="true" />
       <OutputClaim ClaimTypeReferenceId="reenterPassword" Required="true" />
       <OutputClaim ClaimTypeReferenceId="displayName" />
       <OutputClaim ClaimTypeReferenceId="givenName" />
       <OutputClaim ClaimTypeReferenceId="surName" />
       <OutputClaim ClaimTypeReferenceId="city"/>
     </OutputClaims>
   </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

フェデレーション アカウントを使用して初めてサインインした後に city 要求を収集するには、それを出力要求として `SelfAsserted-Social` 技術プロファイルに追加する必要があります。 ローカルとフェデレーション アカウントのユーザーが後でプロファイル データを編集できるようにするには、`SelfAsserted-ProfileUpdate` 技術プロファイルに入力/出力要求を追加します。 拡張ファイル内のこれらの技術プロファイルをオーバーライドします。 要求が画面に表示される順序を制御するには、出力要求の一覧全体を指定します。 **ClaimsProviders** 要素を見つけます。 新しい ClaimsProvider を次のように追加します。

```xml
<ClaimsProvider>
  <DisplayName>Self Asserted</DisplayName>
  <TechnicalProfiles>
    <!--Federated account first-time sign-in page-->
    <TechnicalProfile Id="SelfAsserted-Social">
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="city" />
      </InputClaims>
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="displayName"/>
        <OutputClaim ClaimTypeReferenceId="givenName"/>
        <OutputClaim ClaimTypeReferenceId="surname"/>
        <OutputClaim ClaimTypeReferenceId="city"/>
      </OutputClaims>
    </TechnicalProfile>
    <!--Edit profile page-->
    <TechnicalProfile Id="SelfAsserted-ProfileUpdate">
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="city" />
      </InputClaims>
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="displayName"/>
        <OutputClaim ClaimTypeReferenceId="givenName" />
        <OutputClaim ClaimTypeReferenceId="surname" />
        <OutputClaim ClaimTypeReferenceId="city"/>
      </OutputClaims>
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

## <a name="read-and-write-a-claim"></a>要求の読み取りと書き込み

次の技術プロファイルは、Azure Active Directory へのデータの読み取りと書き込みを行う [Active Directory 技術プロファイル](active-directory-technical-profile.md)です。  
該当する Active Directory 技術プロファイル内のユーザー プロファイルにデータを書き込むには `PersistedClaims` を使用し、ユーザー プロファイルからデータを読み取るには `OutputClaims` を使用します。

拡張ファイル内のこれらの技術プロファイルをオーバーライドします。 **ClaimsProviders** 要素を見つけます。  新しい ClaimsProvider を次のように追加します。

```xml
<ClaimsProvider>
  <DisplayName>Azure Active Directory</DisplayName>
  <TechnicalProfiles>
    <!-- Write data during a local account sign-up flow. -->
    <TechnicalProfile Id="AAD-UserWriteUsingLogonEmail">
      <PersistedClaims>
        <PersistedClaim ClaimTypeReferenceId="city"/>
      </PersistedClaims>
    </TechnicalProfile>
    <!-- Write data during a federated account first-time sign-in flow. -->
    <TechnicalProfile Id="AAD-UserWriteUsingAlternativeSecurityId">
      <PersistedClaims>
        <PersistedClaim ClaimTypeReferenceId="city"/>
      </PersistedClaims>
    </TechnicalProfile>
    <!-- Write data during edit profile flow. -->
    <TechnicalProfile Id="AAD-UserWriteProfileUsingObjectId">
      <PersistedClaims>
        <PersistedClaim ClaimTypeReferenceId="city"/>
      </PersistedClaims>
    </TechnicalProfile>
    <!-- Read data after user resets the password. -->
    <TechnicalProfile Id="AAD-UserReadUsingEmailAddress">
      <OutputClaims>  
        <OutputClaim ClaimTypeReferenceId="city" />
      </OutputClaims>
    </TechnicalProfile>
    <!-- Read data after user authenticates with a local account. -->
    <TechnicalProfile Id="AAD-UserReadUsingObjectId">
      <OutputClaims>  
        <OutputClaim ClaimTypeReferenceId="city" />
      </OutputClaims>
    </TechnicalProfile>
    <!-- Read data after user authenticates with a federated account. -->
    <TechnicalProfile Id="AAD-UserReadUsingAlternativeSecurityId">
      <OutputClaims>  
        <OutputClaim ClaimTypeReferenceId="city" />
      </OutputClaims>
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

## <a name="include-a-claim-in-the-token"></a>トークンに要求を含める 

city 要求を証明書利用者アプリケーションに返すには、出力要求を <em>`SocialAndLocalAccounts/`**`SignUpOrSignIn.xml`**</em> ファイルに追加します。 ユーザー体験が成功した後、出力要求がトークンに追加され、アプリケーションに送信されます。 証明書利用者セクション内の技術プロファイル要素を変更して、city を出力要求として追加します。
 
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
      <OutputClaim ClaimTypeReferenceId="tenantId" AlwaysUseDefaultValue="true" DefaultValue="{Policy:TenantObjectId}" />
      <OutputClaim ClaimTypeReferenceId="city" DefaultValue="" />
    </OutputClaims>
    <SubjectNamingInfo ClaimType="sub" />
  </TechnicalProfile>
</RelyingParty>
```

## <a name="test-the-custom-policy"></a>カスタム ポリシーをテストする

1. [Azure portal](https://portal.azure.com) にサインインします。
2. ご利用の Azure AD テナントを含むディレクトリを使用していることを確認してください。そのためには、トップ メニューにある **[ディレクトリ + サブスクリプション]** フィルターをクリックして、ご利用の Azure AD テナントを含むディレクトリを選択します。
3. Azure portal の左上隅にある **[すべてのサービス]** を選択し、 **[アプリの登録]** を検索して選択します。
4. **[Identity Experience Framework]** を選択します。
5. **[カスタム ポリシーのアップロード]** を選択し、変更した 2 つのポリシー ファイルをアップロードします。
2. アップロードしたサインアップまたはサインイン ポリシーを選択し、 **[今すぐ実行]** ボタンをクリックします。
3. メール アドレスを使用してサインアップできることを確認します。

::: zone-end

サインアップ画面は次のスクリーンショットのようになります。

![変更されたサインアップ オプションのスクリーンショット](./media/configure-user-input/signup-with-city-claim-drop-down-example.png)

アプリケーションに返送されるトークンには、`city` 要求が含まれています。

```json
{
  "typ": "JWT",
  "alg": "RS256",
  "kid": "X5eXk4xyojNFum1kl2Ytv8dlNP4-c57dO6QGTVBwaNk"
}.{
  "exp": 1583500140,
  "nbf": 1583496540,
  "ver": "1.0",
  "iss": "https://contoso.b2clogin.com/f06c2fe8-709f-4030-85dc-38a4bfd9e82d/v2.0/",
  "aud": "e1d2612f-c2bc-4599-8e7b-d874eaca1ee1",
  "acr": "b2c_1a_signup_signin",
  "nonce": "defaultNonce",
  "iat": 1583496540,
  "auth_time": 1583496540,
  "name": "Emily Smith",
  "email": "joe@outlook.com",
  "given_name": "Emily",
  "family_name": "Smith",
  "city": "Bellevue"
  ...
}
```

::: zone pivot="b2c-custom-policy"

## <a name="next-steps"></a>次のステップ

- IEF リファレンスで [ClaimsSchema](claimsschema.md) 要素についてさらに詳しく学習する。
- [Azure AD B2C でカスタム属性を使用する](user-flow-custom-attributes.md)方法について確認する。

::: zone-end
