---
title: カスタム ポリシーを使用して LinkedIn アカウントでのサインインを設定する - Azure Active Directory B2C
description: カスタム ポリシーを使用して Azure Active Directory B2C で LinkedIn アカウントでのサインインを設定する
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 07/25/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 41caa5807ce837e1ff00ceadd3fe5aef958d01b6
ms.sourcegitcommit: f209d0dd13f533aadab8e15ac66389de802c581b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/17/2019
ms.locfileid: "71066050"
---
# <a name="set-up-sign-in-with-a-linkedin-account-using-custom-policies-in-azure-active-directory-b2c"></a>Azure Active Directory B2C でカスタム ポリシーを使用して LinkedIn アカウントでのサインインを設定する

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

この記事では、Azure Active Directory B2C (Azure AD B2C) で[カスタム ポリシー](active-directory-b2c-overview-custom.md)を使用して LinkedIn アカウントからのユーザーのサインインを有効にする方法について説明します。

## <a name="prerequisites"></a>前提条件

- 「[Azure Active Directory B2C でのカスタム ポリシーの概要](active-directory-b2c-get-started-custom.md)」にある手順を完了する。
- LinkedIn アカウント - まだお持ちでない場合は、[アカウントを作成](https://www.linkedin.com/start/join)します。
- Linkedin ページ - 次のセクションで作成する LinkedIn アプリケーションに関連付ける [LinkedIn ページ](https://www.linkedin.com/help/linkedin/answer/710/creating-a-linkedin-company-page)が必要です。

## <a name="create-an-application"></a>アプリケーションの作成

Azure AD B2C で ID プロバイダーとして LinkedIn を使用するには、LinkedIn アプリケーションを作成する必要があります。

### <a name="create-app"></a>アプリを作成する

1. LinkedIn アカウントの資格情報を使用して、[LinkedIn アプリケーション管理](https://www.linkedin.com/secure/developer?newapp=) の Web サイトにサインインします。
1. **[Create app]\(アプリの作成\)** を選択します。
1. **アプリ名**を入力します。
1. LinkedIn ページ名に対応する**会社**名を入力します。 LinkedIn ページがまだない場合は、作成します。
1. (省略可能) **プライバシー ポリシーの URL** を入力します。 これは、有効な URL である必要がありますが、到達可能なエンドポイントである必要はありません。
1. **勤務先の電子メール**を入力します。
1. **アプリ ロゴ**の画像をアップロードします。 ロゴの画像は正方形である必要があり、その大きさは 100 x 100 ピクセル以上である必要があります。
1. **[Products]\(製品\)** セクションは既定値のままにしておいてください。
1. **[Legal terms]\(法律条項\)** に記載されている情報を確認します。 条項に同意する場合は、チェック ボックスをオンにします。
1. **[Create app]\(アプリの作成\)** を選択します。

### <a name="configure-auth"></a>auth を構成する

1. **[Auth]\(認証\)** タブを選択します。
1. **クライアント ID** を書き留めます。
1. **クライアント シークレット**を表示して記録します。
1. **[OAuth 2.0 settings]\(OAuth 2.0 設定\)** で、次の**リダイレクト URL** を追加します。 `your-tenant` をテナントの名前に置き換えます。 テナント名には、Azure AD B2C に大文字で定義されている場合でも、**すべて小文字**を使用します。

    `https://your-tenant.b2clogin.com/your-tenant.onmicrosoft.com/oauth2/authresp`

## <a name="create-a-policy-key"></a>ポリシー キーを作成する

Azure AD B2C テナントで前に記録したクライアント シークレットを格納する必要があります。

1. [Azure Portal](https://portal.azure.com/) にサインインします。
2. ご自分の Azure AD B2C テナントが含まれるディレクトリを必ず使用してください。 上部メニューで **[ディレクトリ + サブスクリプション]** フィルターを選択し、ご利用のテナントが含まれるディレクトリを選択します。
3. Azure portal の左上隅にある **[すべてのサービス]** を選択してから、 **[Azure AD B2C]** を検索して選択します。
4. [概要] ページで、 **[Identity Experience Framework]** を選択します。
5. **[ポリシー キー]** を選択し、 **[追加]** を選択します。
6. **オプション**については、`Manual`を選択します。
7. ポリシー キーの**名前**を入力します。 たとえば、「 `LinkedInSecret` 」のように入力します。 プレフィックス *B2C_1A_* がキーの名前に自動的に追加されます。
8. **[シークレット]** に、前に記録したクライアント シークレットを入力します。
9. **[キー使用法]** として [`Signature`] を選択します。
10. **Create** をクリックしてください。

## <a name="add-a-claims-provider"></a>クレーム プロバイダーを追加する

ユーザーが LinkedIn アカウントを使用してサインインするようにするには、そのアカウントを Azure AD B2C がエンドポイント経由で通信できる相手のクレーム プロバイダーとして定義する必要があります。 エンドポイントは、特定のユーザーが認証されていることを確認するために Azure AD B2C で使う一連の要求を提供します。

LinkedIn アカウントをクレーム プロバイダーとして定義するには、それをポリシーの拡張ファイル内の **ClaimsProviders** 要素に追加します。

1. エディターで *SocialAndLocalAccounts/ **TrustFrameworkExtensions.xml*** ファイルを開きます。 このファイルは、前提条件の 1 つの一部としてダウンロードした[カスタム ポリシー スターター パック][starter-pack]に含まれています。
1. **ClaimsProviders** 要素を見つけます。 存在しない場合は、それをルート要素の下に追加します。
1. 新しい **ClaimsProvider** を次のように追加します。

    ```xml
    <ClaimsProvider>
      <Domain>linkedin.com</Domain>
      <DisplayName>LinkedIn</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="LinkedIn-OAUTH">
          <DisplayName>LinkedIn</DisplayName>
          <Protocol Name="OAuth2" />
          <Metadata>
            <Item Key="ProviderName">linkedin</Item>
            <Item Key="authorization_endpoint">https://www.linkedin.com/oauth/v2/authorization</Item>
            <Item Key="AccessTokenEndpoint">https://www.linkedin.com/oauth/v2/accessToken</Item>
            <Item Key="ClaimsEndpoint">https://api.linkedin.com/v2/me</Item>
            <Item Key="scope">r_emailaddress r_liteprofile</Item>
            <Item Key="HttpBinding">POST</Item>
            <Item Key="external_user_identity_claim_id">id</Item>
            <Item Key="BearerTokenTransmissionMethod">AuthorizationHeader</Item>
            <Item Key="ResolveJsonPathsInJsonTokens">true</Item>
            <Item Key="UsePolicyInRedirectUri">0</Item>
            <Item Key="client_id">Your LinkedIn application client ID</Item>
          </Metadata>
          <CryptographicKeys>
            <Key Id="client_secret" StorageReferenceId="B2C_1A_LinkedInSecret" />
          </CryptographicKeys>
          <InputClaims />
          <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="issuerUserId" PartnerClaimType="id" />
            <OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="firstName.localized" />
            <OutputClaim ClaimTypeReferenceId="surname" PartnerClaimType="lastName.localized" />
            <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="linkedin.com" AlwaysUseDefaultValue="true" />
            <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="socialIdpAuthentication" AlwaysUseDefaultValue="true" />
          </OutputClaims>
          <OutputClaimsTransformations>
            <OutputClaimsTransformation ReferenceId="ExtractGivenNameFromLinkedInResponse" />
            <OutputClaimsTransformation ReferenceId="ExtractSurNameFromLinkedInResponse" />
            <OutputClaimsTransformation ReferenceId="CreateRandomUPNUserName" />
            <OutputClaimsTransformation ReferenceId="CreateUserPrincipalName" />
            <OutputClaimsTransformation ReferenceId="CreateAlternativeSecurityId" />
            <OutputClaimsTransformation ReferenceId="CreateSubjectClaimFromAlternativeSecurityId" />
          </OutputClaimsTransformations>
          <UseTechnicalProfileForSessionManagement ReferenceId="SM-SocialLogin" />
        </TechnicalProfile>
      </TechnicalProfiles>
    </ClaimsProvider>
    ```

1. **client_id** の値を、前に記録した LinkedIn アプリケーションのクライアント ID に置き換えます。
1. ファイルを保存します。

### <a name="add-the-claims-transformations"></a>要求変換の追加

LinkedIn 技術プロファイルでは、**ExtractGivenNameFromLinkedInResponse** と **ExtractSurNameFromLinkedInResponse** 要求変換が ClaimsTransformations のリストに追加されている必要があります。 ファイルに **ClaimsTransformations** 要素が定義されていない場合、親 XML 要素を次のように追加します。 要求変換では、**nullStringClaim** という名前の新しい要求の種類が定義されている必要があります。

*TrustFrameworkExtensions.xml* ファイルの先頭付近に **BuildingBlocks** 要素を追加します。 例として、*TrustFrameworkBase.xml* を参照します。

```XML
<BuildingBlocks>
  <ClaimsSchema>
    <!-- Claim type needed for LinkedIn claims transformations -->
    <ClaimType Id="nullStringClaim">
      <DisplayName>nullClaim</DisplayName>
      <DataType>string</DataType>
      <AdminHelpText>A policy claim to store output values from ClaimsTransformations that aren't useful. This claim should not be used in TechnicalProfiles.</AdminHelpText>
      <UserHelpText>A policy claim to store output values from ClaimsTransformations that aren't useful. This claim should not be used in TechnicalProfiles.</UserHelpText>
    </ClaimType>
  </ClaimsSchema>

  <ClaimsTransformations>
    <!-- Claim transformations needed for LinkedIn technical profile -->
    <ClaimsTransformation Id="ExtractGivenNameFromLinkedInResponse" TransformationMethod="GetSingleItemFromJson">
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="givenName" TransformationClaimType="inputJson" />
      </InputClaims>
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="nullStringClaim" TransformationClaimType="key" />
        <OutputClaim ClaimTypeReferenceId="givenName" TransformationClaimType="value" />
      </OutputClaims>
    </ClaimsTransformation>
    <ClaimsTransformation Id="ExtractSurNameFromLinkedInResponse" TransformationMethod="GetSingleItemFromJson">
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="surname" TransformationClaimType="inputJson" />
      </InputClaims>
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="nullStringClaim" TransformationClaimType="key" />
        <OutputClaim ClaimTypeReferenceId="surname" TransformationClaimType="value" />
      </OutputClaims>
    </ClaimsTransformation>
  </ClaimsTransformations>
</BuildingBlocks>
```

### <a name="upload-the-extension-file-for-verification"></a>拡張ファイルのアップロードによる確認

これで、Azure AD B2C が LinkedIn アカウントと通信する方法を認識できるようにポリシーが構成されました。 ポリシーの拡張ファイルをアップロードしてみて、現時点で問題がないことを確認します。

1. Azure AD B2C テナントの **[カスタム ポリシー]** ページで、 **[ポリシーのアップロード]** を選択します。
2. **[ポリシーが存在する場合は上書きする]** を有効にし、*TrustFrameworkExtensions.xml* ファイルを参照して選択します。
3. **[アップロード]** をクリックします。

## <a name="register-the-claims-provider"></a>クレーム プロバイダーを登録する

この時点で、ID プロバイダーは設定されていますが、まだどのサインアップまたはサインイン画面でも使用できません。 これを使用できるようにするには、既存のテンプレート ユーザー体験の複製を作成してから、それを LinkedIn ID プロバイダーも含まれるように変更します。

1. スターター パック内の *TrustFrameworkBase.xml* ファイルを開きます。
2. `Id="SignUpOrSignIn"` を含む **UserJourney** 要素を見つけ、その内容全体をコピーします。
3. *TrustFrameworkExtensions.xml* を開き、**UserJourneys** 要素を見つけます。 要素が存在しない場合は追加します。
4. コピーした **UserJourney** 要素の内容全体を **UserJourneys** 要素の子として貼り付けます。
5. ユーザー体験の ID の名前を変更します。 たとえば、「 `SignUpSignInLinkedIn` 」のように入力します。

### <a name="display-the-button"></a>ボタンを表示する

**ClaimsProviderSelection** 要素は、サインアップまたはサインイン画面の ID プロバイダーのボタンに類似しています。 LinkedIn アカウントのために **ClaimsProviderSelection** 要素を追加すると、ユーザーがこのページにアクセスしたときに新しいボタンが表示されます。

1. 作成したユーザー体験内で、`Order="1"` を含む **OrchestrationStep** 要素を見つけます。
2. **ClaimsProviderSelections** の下に、次の要素を追加します。 **TargetClaimsExchangeId** の値を適切な値 (`LinkedInExchange` など) に設定します。

    ```XML
    <ClaimsProviderSelection TargetClaimsExchangeId="LinkedInExchange" />
    ```

### <a name="link-the-button-to-an-action"></a>ボタンのアクションへのリンク

ボタンが所定の位置に配置されたので、ボタンをアクションにリンクする必要があります。 この場合のアクションは、Azure AD B2C がトークンを受信するために LinkedIn アカウントと通信することです。

1. ユーザー体験内で、`Order="2"` を含む **OrchestrationStep** を見つけます。
2. 次の **ClaimsExchange** 要素を追加します。ID には、**TargetClaimsExchangeId** に使用したのと同じ値を使用するようにしてください。

    ```XML
    <ClaimsExchange Id="LinkedInExchange" TechnicalProfileReferenceId="LinkedIn-OAUTH" />
    ```

    **TechnicalProfileReferenceId** の値を、前に作成した技術プロファイルの ID に更新します。 たとえば、「 `LinkedIn-OAUTH` 」のように入力します。

3. *TrustFrameworkExtensions.xml* ファイルを保存し、確認のために再度アップロードします。

## <a name="create-an-azure-ad-b2c-application"></a>Azure AD B2C アプリケーションを作成する

Azure AD B2C との通信は、テナントで作成したアプリケーション経由で行われます。 このセクションでは、テスト アプリケーションをまだ作成していない場合にそれを作成するための省略可能な手順を紹介します。

1. [Azure Portal](https://portal.azure.com) にサインインします。
2. ご自分の Azure AD B2C テナントが含まれるディレクトリを必ず使用してください。 上部メニューで **[ディレクトリ + サブスクリプション]** フィルターを選択し、ご利用のテナントが含まれるディレクトリを選択します。
3. Azure portal の左上隅にある **[すべてのサービス]** を選択してから、 **[Azure AD B2C]** を検索して選択します。
4. **[アプリケーション]** を選択し、 **[追加]** を選択します。
5. アプリケーションの名前を入力します (*testapp1* など)。
6. **[Web アプリ / Web API]** には `Yes` を選択し、 **[応答 URL]** に `https://jwt.ms` を入力します。
7. **Create** をクリックしてください。

## <a name="update-and-test-the-relying-party-file"></a>証明書利用者ファイルを更新し、テストする

作成したユーザー体験を開始する証明書利用者 (RP) ファイルを更新します。

1. 作業ディレクトリに *SignUpOrSignIn.xml* のコピーを作成し、名前を変更します。 たとえば、その名前を *SignUpSignInLinkedIn.xml* に変更します。
2. 新しいファイルを開き、**TrustFrameworkPolicy** の **PolicyId** 属性の値を一意の値で更新します。 たとえば、「 `SignUpSignInLinkedIn` 」のように入力します。
3. **PublicPolicyUri** の値をポリシーの URI に更新します。 たとえば、`http://contoso.com/B2C_1A_signup_signin_linkedin` とします。
4. **DefaultUserJourney** 内の **ReferenceId** 属性の値を、作成した新しいユーザー体験の ID (SignUpSignLinkedIn) に一致するように更新します。
5. 変更を保存し、ファイルをアップロードしてから、一覧内の新しいポリシーを選択します。
6. 作成した Azure AD B2C アプリケーションが **[アプリケーションの選択]** フィールドで選択されていることを確認し、 **[今すぐ実行]** をクリックしてテストします。

## <a name="migration-from-v10-to-v20"></a>v1.0 から v2.0 に移行する

LinkedIn では最近、[API が v1.0 から v2.0 に更新](https://engineering.linkedin.com/blog/2018/12/developer-program-updates)されました。 既存の構成を新しい構成に移行するには、次のセクションに記載された情報を使用して技術プロファイル内の要素を更新します。

### <a name="replace-items-in-the-metadata"></a>Metadata 内の項目を置き換える

**TechnicalProfile** の既存の **Metadata** 要素内で、次の **Item** 要素を変更します。

```XML
<Item Key="ClaimsEndpoint">https://api.linkedin.com/v1/people/~:(id,first-name,last-name,email-address,headline)</Item>
<Item Key="scope">r_emailaddress r_basicprofile</Item>
```

変更後:

```XML
<Item Key="ClaimsEndpoint">https://api.linkedin.com/v2/me</Item>
<Item Key="scope">r_emailaddress r_liteprofile</Item>
```

### <a name="add-items-to-the-metadata"></a>Metadata に項目を追加する

**TechnicalProfile** の **Metadata** 内に、次の **Item** 要素を追加します。

```XML
<Item Key="external_user_identity_claim_id">id</Item>
<Item Key="BearerTokenTransmissionMethod">AuthorizationHeader</Item>
<Item Key="ResolveJsonPathsInJsonTokens">true</Item>
```

### <a name="update-the-outputclaims"></a>OutputClaims を更新する

**TechnicalProfile** の既存の **OutputClaims** 要素内で、次の **OutputClaim** 要素を変更します。

```XML
<OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="firstName" />
<OutputClaim ClaimTypeReferenceId="surname" PartnerClaimType="lastName" />
```

変更後:

```XML
<OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="firstName.localized" />
<OutputClaim ClaimTypeReferenceId="surname" PartnerClaimType="lastName.localized" />
```

### <a name="add-new-outputclaimstransformation-elements"></a>新しい OutputClaimsTransformation 要素を追加する

**TechnicalProfile** の **OutputClaimsTransformations** 内に、次の **OutputClaimsTransformation** 要素を追加します。

```XML
<OutputClaimsTransformation ReferenceId="ExtractGivenNameFromLinkedInResponse" />
<OutputClaimsTransformation ReferenceId="ExtractSurNameFromLinkedInResponse" />
```

### <a name="define-the-new-claims-transformations-and-claim-type"></a>新しい要求変換と要求の種類を定義する

前の手順で、新しい要求変換を追加しました。この要求変換を定義する必要があります。 要求変換を定義するには、**ClaimsTransformations** のリストにその要求変換を追加します。 ファイルに **ClaimsTransformations** 要素が定義されていない場合、親 XML 要素を次のように追加します。 要求変換では、**nullStringClaim** という名前の新しい要求の種類が定義されている必要があります。

**BuildingBlocks** 要素をファイルの先頭付近に追加する必要があります。 例として、*TrustframeworkBase.xml* を参照します。

```XML
<BuildingBlocks>
  <ClaimsSchema>
    <!-- Claim type needed for LinkedIn claims transformations -->
    <ClaimType Id="nullStringClaim">
      <DisplayName>nullClaim</DisplayName>
      <DataType>string</DataType>
      <AdminHelpText>A policy claim to store unuseful output values from ClaimsTransformations. This claim should not be used in a TechnicalProfiles.</AdminHelpText>
      <UserHelpText>A policy claim to store unuseful output values from ClaimsTransformations. This claim should not be used in a TechnicalProfiles.</UserHelpText>
    </ClaimType>
  </ClaimsSchema>

  <ClaimsTransformations>
    <!-- Claim transformations needed for LinkedIn technical profile -->
    <ClaimsTransformation Id="ExtractGivenNameFromLinkedInResponse" TransformationMethod="GetSingleItemFromJson">
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="givenName" TransformationClaimType="inputJson" />
      </InputClaims>
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="nullStringClaim" TransformationClaimType="key" />
        <OutputClaim ClaimTypeReferenceId="givenName" TransformationClaimType="value" />
      </OutputClaims>
    </ClaimsTransformation>
    <ClaimsTransformation Id="ExtractSurNameFromLinkedInResponse" TransformationMethod="GetSingleItemFromJson">
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="surname" TransformationClaimType="inputJson" />
      </InputClaims>
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="nullStringClaim" TransformationClaimType="key" />
        <OutputClaim ClaimTypeReferenceId="surname" TransformationClaimType="value" />
      </OutputClaims>
    </ClaimsTransformation>
  </ClaimsTransformations>
</BuildingBlocks>
```

### <a name="obtain-an-email-address"></a>メール アドレスを取得する

LinkedIn の v1.0 から v2.0 への移行の一部として、メール アドレスを取得するために別の API への追加の呼び出しが必要です。 サインアップ中にメール アドレスを取得する必要がある場合、以下を実行します。

1. 上の手順を完了して Azure AD B2C が LinkedIn とフェデレーションできるようにすることで、ユーザーのサインインを可能にします。 フェデレーションの一部として、Azure AD B2C は LinkedIn のアクセス トークンを受信します。
2. LinkedIn アクセス トークンを要求内に保存します。 [手順についてはこちらを参照してください](idp-pass-through-custom.md)。
3. LinkedIn の `/emailAddress` API への要求を作成する次のクレーム プロバイダーを追加します。 この要求を承認するために、LinkedIn アクセス トークンが必要です。

    ```XML
    <ClaimsProvider>
      <DisplayName>REST APIs</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="API-LinkedInEmail">
          <DisplayName>Get LinkedIn email</DisplayName>
          <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
          <Metadata>
              <Item Key="ServiceUrl">https://api.linkedin.com/v2/emailAddress?q=members&amp;projection=(elements*(handle~))</Item>
              <Item Key="AuthenticationType">Bearer</Item>
              <Item Key="UseClaimAsBearerToken">identityProviderAccessToken</Item>
              <Item Key="SendClaimsIn">Url</Item>
              <Item Key="ResolveJsonPathsInJsonTokens">true</Item>
          </Metadata>
          <InputClaims>
              <InputClaim ClaimTypeReferenceId="identityProviderAccessToken" />
          </InputClaims>
          <OutputClaims>
              <OutputClaim ClaimTypeReferenceId="email" PartnerClaimType="elements[0].handle~.emailAddress" />
          </OutputClaims>
          <UseTechnicalProfileForSessionManagement ReferenceId="SM-Noop" />
        </TechnicalProfile>
      </TechnicalProfiles>
    </ClaimsProvider>
    ```

4. ユーザーが LinkedIn を使用してサインインするときに API クレーム プロバイダーがトリガーされるように、ユーザー体験に次のオーケストレーション手順を追加します。 `Order` 番号を適切に更新するようにしてください。 この手順を、LinkedIn 技術プロファイルをトリガーするオーケストレーション手順の直後に追加します。

    ```XML
    <!-- Extra step for LinkedIn to get the email -->
    <OrchestrationStep Order="3" Type="ClaimsExchange">
      <Preconditions>
        <Precondition Type="ClaimsExist" ExecuteActionsIf="false">
          <Value>identityProvider</Value>
          <Action>SkipThisOrchestrationStep</Action>
        </Precondition>
        <Precondition Type="ClaimEquals" ExecuteActionsIf="false">
          <Value>identityProvider</Value>
          <Value>linkedin.com</Value>
          <Action>SkipThisOrchestrationStep</Action>
        </Precondition>
      </Preconditions>
      <ClaimsExchanges>
        <ClaimsExchange Id="GetEmail" TechnicalProfileReferenceId="API-LinkedInEmail" />
      </ClaimsExchanges>
    </OrchestrationStep>
    ```

サインアップ中の LinkedIn からのメール アドレス取得は、オプションです。 メール アドレスを LinkedIn から取得せず、サインアップ中に必要になるように選択した場合、ユーザーはメール アドレスを手動で入力して検証する必要があります。

LinkedIn ID プロバイダーを使用するポリシーの完全なサンプルについては、[カスタム ポリシー スターター パック](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/linkedin-identity-provider)に関するページを参照してください。

<!-- Links - EXTERNAL -->
[starter-pack]: https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack
