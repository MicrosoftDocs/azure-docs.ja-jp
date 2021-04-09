---
title: Apple ID でのサインアップとサインインを設定する
titleSuffix: Azure AD B2C
description: Azure Active Directory B2C を使用して、アプリケーションで Apple ID でのサインアップとサインインをユーザーに提供します。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 03/22/2021
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: 4c3308ff9b1ffeb0f14c5808e0d4cdd71dabf335
ms.sourcegitcommit: f611b3f57027a21f7b229edf8a5b4f4c75f76331
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/22/2021
ms.locfileid: "104780125"
---
# <a name="set-up-sign-up-and-sign-in-with-an-apple-id--using-azure-active-directory-b2c-preview"></a>Azure Active Directory B2C を使用して、Apple ID でのサインアップとサインインを設定する (プレビュー)

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

::: zone pivot="b2c-custom-policy"

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

::: zone-end

## <a name="prerequisites"></a>前提条件

[!INCLUDE [active-directory-b2c-customization-prerequisites](../../includes/active-directory-b2c-customization-prerequisites.md)]

## <a name="create-an-apple-id-application"></a>Apple ID アプリケーションを作成する

Azure Active Directory B2C (Azure AD B2C) で Apple ID でのユーザーのサインインを有効にするには、[https://developer.apple.com](https://developer.apple.com) でアプリケーションを作成する必要があります。 詳細については、[Apple でのサインイン](https://developer.apple.com/sign-in-with-apple/get-started/)に関するページを参照してください。 まだ Apple Developer アカウントを持っていない場合は、[Apple Developer Program](https://developer.apple.com/programs/enroll/) でサインアップできます。

1. アカウントの資格情報を使用して [Apple Developer ポータル](https://developer.apple.com/account)にサインインします。
1. メニューから、 **[Certificates, IDs, & Profiles]\(証明書、ID、プロファイル\)** を選択し、 **(+)** を選択します。
1. **[Register a New Identifier]\(新しい ID の登録\)** で **[App IDs]\(アプリ ID\)** を選択し、 **[続行]** を選択します。
1. **[種類の選択]\(Select a type\)** で **[App]\(アプリ\)** を選択し、 **[続行]** を選択します。
1. **[Register an App ID]\(アプリ ID の登録\)** の場合:
    1. **[Description]\(説明\)** を入力します。 
    1. **[Bundle ID]\(バンドル ID\)** を入力します (例: `com.contoso.azure-ad-b2c`)。 
    1. **[Capabilities]\(機能\)** については、機能の一覧から **[Sign in with Apple]\(Apple でサインイン\)** を選択します。 
    1. この手順でアプリ ID のプレフィックス (チーム ID) をメモしておいてください。 この情報は後で必要になります。
    1. **[Continue]\(続行\)** 、 **[Register]\(登録\)** の順に選択します。
1. メニューから、 **[Certificates, IDs, & Profiles]\(証明書、ID、プロファイル\)** を選択し、 **(+)** を選択します。
1. **[Register a New Identifier]\(新しい ID の登録\)** で **[Services IDs]\(サービス ID\)** を選択し、 **[続行]** を選択します。
1. **[Register a Services ID]\(サービス ID の登録\)** の場合:
    1. **[Description]\(説明\)** を入力します。 この説明は、同意画面でユーザーに表示されます。
    1. **[Identifier]\(識別子\)** を入力します (例: `com.consoto.azure-ad-b2c-service`)。 識別子は、OpenID Connect フローのクライアント ID です。
    1. **[Continue]\(続行\)** を選択し、次に **[Register]\(登録\)** を選択します。
1. **[Identifiers]\(識別子\)** から、作成した識別子を選択します。
1. **[Sign In with Apple]\(Apple でサインイン\)** を選択し、 **[Configure]\(構成\)** を選択します。
    1. Apple でのサインインを構成する **[Primary App ID]\(プライマリ アプリ ID\)** を選択します。
    1. **[Domains and Subdomains]\(ドメインとサブドメイン\)** に `your-tenant-name.b2clogin.com` を入力します。 your-tenant-name をテナントの名前に置き換えます。 [カスタム ドメイン](custom-domain.md)を使用する場合は、「`https://your-domain-name`」と入力します。
    1. **[Return URLs]\(戻り先 URL\)** に `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp` と入力します。 [カスタム ドメイン](custom-domain.md)を使用する場合は、「`https://your-domain-name/your-tenant-name.onmicrosoft.com/oauth2/authresp`」と入力します。 `your-tenant-name` を実際のテナントの名前に、`your-domain-name` を実際のカスタム ドメインに置き換えます。
    1. **[次へ]** を選択し、 **[Done]\(完了\)** を選択します。
    1. ポップアップ ウィンドウが閉じたら、 **[続行]** を選択し、 **[保存]** を選択します。

## <a name="creating-an-apple-client-secret"></a>Apple クライアント シークレットの作成

1. Apple Developer ポータルのメニューから、 **[Keys]\(キー\)** を選択し、 **(+)** を選択します。
1. **[Register a New Key]\(新しいキーの登録\)** の場合:
    1. **キー名** を入力します。
    1. **[Sign in with Apple]\(Apple でサインイン\)** を選択し、 **[続行]** を選択します。
    1. **[Primary App ID]\(プライマリ アプリ ID\)** では、以前に作成したアプリを選択し、 **[保存]** を選択します。
    1. **[Configure]\(構成\)** を選択し、次に **[Register]\(登録\)** を選択してキー登録プロセスを終了します。
1. **[Download Your Key]\(キーのダウンロード\)** では、 **[Download]\(ダウンロード\)** を選択して、自分のキーが含まれている .p8 ファイルをダウンロードします。


::: zone pivot="b2c-user-flow"

## <a name="configure-apple-as-an-identity-provider"></a>Apple を ID プロバイダーとして構成する

1. Azure AD B2C テナントの全体管理者として [Azure portal](https://portal.azure.com/) にサインインします。
1. 上部のメニューにある **[ディレクトリ + サブスクリプション]** フィルターを選択し、ご利用の Azure AD B2C テナントを含むディレクトリを選択します。
1. **[Azure サービス]** で、 **[Azure AD B2C]** を選択します。 または、検索ボックスを使用して検索し、 **[Azure AD B2C]** を選択します。
1. **[ID プロバイダー]** を選択してから、 **[Apple (プレビュー)]** を選択します。
1. **[名前]** を入力します。 たとえば、「*Apple*」など。
1. **Apple Developer ID (チーム ID)** を入力します。
1. **Apple サービス ID (クライアント ID)** を入力します。
1. **Apple キー ID** を入力します。
1. **Apple 証明書データ** を選択してアップロードします。
1. **[保存]** を選択します。


> [!IMPORTANT] 
> - Apple でのサインインでは、管理者が 6 か月ごとにクライアント シークレットを更新する必要があります。 
> - この機能のパブリック プレビュー中は、Apple クライアント シークレットの有効期限が切れたら手動で更新する必要があります。 Apple ID プロバイダーの [Configure social IDP]\(ソーシャル IDP の構成\) ページに事前に警告が表示されますが、独自のリマインダーを設定することをお勧めします。 
> - シークレットを更新する必要がある場合は、Azure portal で Azure AD B2C を開き、 **[ID プロバイダー]**  >  **[Apple]** にアクセスし、 **[シークレットの更新]** を選択します。

## <a name="add-the-apple-identity-provider-to-a-user-flow"></a>Apple ID プロバイダーをユーザー フローに追加する

ユーザーが Apple ID を使用してサインインできるようにするには、Apple ID プロバイダーをユーザー フローに追加する必要があります。 Apple でのサインインは、**推奨** されるバージョンのユーザー フローに対してのみ構成できます。 Apple ID プロバイダーをユーザー フローに追加するには、次のようにします。

1. Azure AD B2C テナントで、 **[ユーザー フロー]** を選択します。
1. Apple ID プロバイダーを追加するユーザー フローを選択します。 
1. **[ソーシャル ID プロバイダー]** で、 **[Apple (プレビュー)]** を選択します。
1. **[保存]** を選択します。
1. ポリシーをテストするには、 **[ユーザー フローを実行します]** を選択します。
1. **[アプリケーション]** には、以前に登録した *testapp1* という名前の Web アプリケーションを選択します。 **[応答 URL]** に `https://jwt.ms` と表示されます。
1. **[ユーザー フローを実行します]** ボタンを選択します。
1. サインアップまたはサインイン ページで **[Apple]** を選択して、Apple ID でサインインします。

サインイン プロセスが成功すると、ブラウザーは `https://jwt.ms` にリダイレクトされ、Azure AD B2C によって返されたトークンの内容が表示されます。

::: zone-end

::: zone pivot="b2c-custom-policy"

## <a name="signing-the-client-secret"></a>クライアント シークレットの署名

前にダウンロードした .p8 ファイルを使用して、クライアント シークレットで JWT に署名します。 JWT の作成と署名に使用できるライブラリは多数あります。 [トークンを作成する Azure 関数](https://github.com/azure-ad-b2c/samples/tree/master/policies/sign-in-with-apple/azure-function)を使用します。 

1. [Azure 関数](../azure-functions/functions-create-function-app-portal.md) を作成します。
1. **Developer** で、 **[コードとテスト]** を選択します。 
1. [run.csx](https://github.com/azure-ad-b2c/samples/blob/master/policies/sign-in-with-apple/azure-function/run.csx) ファイルの内容をコピーし、エディターに貼り付けます。
1. **[保存]** を選択します。
1. HTTP `POST` 要求を作成し、次の情報を指定します。

    - **appleTeamId**: ご利用の Apple Developer Team ID
    - **appleServiceId**: Apple サービス ID (クライアント ID でもあります)
    - **p8key**: PEM 形式のキー。 これは、テキスト エディターで .p8 ファイルを開き、改行なしで `-----BEGIN PRIVATE KEY-----` と `-----END PRIVATE KEY-----` の間のすべてをコピーすることで取得できます。
 
次の JSON は、Azure 関数への呼び出し例です。

```json
{
    "appleTeamId": "ABC123DEFG",
    "appleKeyId": "URKEYID001",
    "appleServiceId": "com.yourcompany.app1",
    "p8key": "MIGTAgEAMBMGByqGSM49AgEGCCqGSM49AwEHBHkwdwIBAQQg+s07NiAcuGEu8rxsJBG7ttupF6FRe3bXdHxEipuyK82gCgYIKoZIzj0DAQehRANCAAQnR1W/KbbaihTQayXH3tuAXA8Aei7u7Ij5OdRy6clOgBeRBPy1miObKYVx3ki1msjjG2uGqRbrc1LvjLHINWRD"
}
```

Azure 関数は、応答内の適切に書式設定され署名されたクライアント シークレット JWT を使用して応答します。次に例を示します。

```json
{
    "token": "eyJhbGciOiJFUzI1NiIsInR5cCI6IkpXVCJ9.eyJzdWIiOiJjb20ueW91cmNvbXBhbnkuYXBwMSIsIm5iZiI6MTU2MDI2OTY3NSwiZXhwIjoxNTYwMzU2MDc1LCJpc3MiOiJBQkMxMjNERUZHIiwiYXVkIjoiaHR0cHM6Ly9hcHBsZWlkLmFwcGxlLmNvbSJ9.Dt9qA9NmJ_mk6tOqbsuTmfBrQLFqc9BnSVKR6A-bf9TcTft2XmhWaVODr7Q9w1PP3QOYShFXAnNql5OdNebB4g"
}
```

## <a name="create-a-policy-key"></a>ポリシー キーを作成する

Azure AD B2C テナントで前に記録したクライアント シークレットを格納する必要があります。

1. [Azure portal](https://portal.azure.com/) にサインインします。
1. 上部のメニューにある **[ディレクトリ + サブスクリプション]** フィルターを選択し、ご利用の Azure AD B2C テナントを含むディレクトリを選択します。
2. **[Azure サービス]** で、 **[Azure AD B2C]** を選択します。 または、検索ボックスを使用して検索し、 **[Azure AD B2C]** を選択します。
1. **[概要]** ページで、 **[Identity Experience Framework]** を選択します。
1. **[ポリシー キー]** を選択し、 **[追加]** を選択します。
1. **[オプション]** では、 **[手動]** を選びます。
1. ポリシー キーの **名前** を入力します。 例: "AppleSecret"。 プレフィックス "B2C_1A_" がキーの名前に自動的に追加されます。
1. **[シークレット]** に、Azure 関数 によって返されるトークンの値を入力します (JWT トークン)。
1. **[キー使用法]** には **[署名]** を選択します。
1. **［作成］** を選択します

> [!IMPORTANT] 
> - Apple でのサインインでは、管理者が 6 か月ごとにクライアント シークレットを更新する必要があります。
> - Apple クライアント シークレットの有効期限が切れた場合は、手動で更新し、新しい値をポリシー キーに格納する必要があります。
> - 6 か月以内の独自のリマインダーを設定して、新しいクライアント シークレットを生成することをお勧めします。 

## <a name="configure-apple-as-an-identity-provider"></a>Apple を ID プロバイダーとして構成する

ユーザーが Apple ID を使用してサインインできるようにするには、そのアカウントを Azure AD B2C がエンドポイント経由で通信できる相手のクレーム プロバイダーとして定義する必要があります。 エンドポイントでは、特定のユーザーが認証されていることを確認するために Azure AD B2C で使用される一連のクレームが提供されます。

Apple ID をクレーム プロバイダーとして定義するには、それをポリシーの拡張ファイル内の **ClaimsProviders** 要素に追加します。

1. *TrustFrameworkExtensions.xml* を開きます。
2. **ClaimsProviders** 要素を見つけます。 存在しない場合は、それをルート要素の下に追加します。
3. 新しい **ClaimsProvider** を次のように追加します。

    ```xml
    <ClaimsProvider>
      <Domain>apple.com</Domain>
      <DisplayName>Apple</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="Apple-OIDC">
          <DisplayName>Apple</DisplayName>
          <Protocol Name="OpenIdConnect" />
          <Metadata>
            <Item Key="ProviderName">apple</Item>
            <Item Key="authorization_endpoint">https://appleid.apple.com/auth/authorize</Item>
            <Item Key="AccessTokenEndpoint">https://appleid.apple.com/auth/token</Item>
            <Item Key="JWKS">https://appleid.apple.com/auth/keys</Item>
            <Item Key="issuer">https://appleid.apple.com</Item>
            <Item Key="scope">name email openid</Item>
            <Item Key="HttpBinding">POST</Item>
            <Item Key="response_types">code</Item>
            <Item Key="external_user_identity_claim_id">sub</Item>
            <Item Key="response_mode">form_post</Item>
            <Item Key="ReadBodyClaimsOnIdpRedirect">user.firstName user.lastName user.email</Item>
            <Item Key="client_id">You Apple ID</Item>
            <Item Key="UsePolicyInRedirectUri">false</Item>
          </Metadata>
          <CryptographicKeys>
            <Key Id="client_secret" StorageReferenceId="B2C_1A_AppleSecret"/>
          </CryptographicKeys>
          <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="issuerUserId" PartnerClaimType="sub" />
            <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="https://appleid.apple.com" AlwaysUseDefaultValue="true" />
            <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="socialIdpAuthentication" AlwaysUseDefaultValue="true" />
            <OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="user.name.firstName"/>
            <OutputClaim ClaimTypeReferenceId="surname" PartnerClaimType="user.name.lastName"/>
            <OutputClaim ClaimTypeReferenceId="email" PartnerClaimType="user.email"/>
          </OutputClaims>
          <OutputClaimsTransformations>
            <OutputClaimsTransformation ReferenceId="CreateRandomUPNUserName"/>
            <OutputClaimsTransformation ReferenceId="CreateUserPrincipalName"/>
            <OutputClaimsTransformation ReferenceId="CreateAlternativeSecurityId"/>
            <OutputClaimsTransformation ReferenceId="CreateSubjectClaimFromAlternativeSecurityId"/>
          </OutputClaimsTransformations>
          <UseTechnicalProfileForSessionManagement ReferenceId="SM-SocialLogin" />
        </TechnicalProfile>
      </TechnicalProfiles>
    </ClaimsProvider>
    ```

4. **client_id** をサービス ID に設定します。 たとえば、「 `com.consoto.azure-ad-b2c-service` 」のように入力します。
5. ファイルを保存します。

[!INCLUDE [active-directory-b2c-add-identity-provider-to-user-journey](../../includes/active-directory-b2c-add-identity-provider-to-user-journey.md)]


```xml
<OrchestrationStep Order="1" Type="CombinedSignInAndSignUp" ContentDefinitionReferenceId="api.signuporsignin">
  <ClaimsProviderSelections>
    ...
    <ClaimsProviderSelection TargetClaimsExchangeId="AppleExchange" />
  </ClaimsProviderSelections>
  ...
</OrchestrationStep>

<OrchestrationStep Order="2" Type="ClaimsExchange">
  ...
  <ClaimsExchanges>
    <ClaimsExchange Id="AppleExchange" TechnicalProfileReferenceId="Apple-OIDC" />
  </ClaimsExchanges>
</OrchestrationStep>
```

[!INCLUDE [active-directory-b2c-configure-relying-party-policy](../../includes/active-directory-b2c-configure-relying-party-policy-user-journey.md)]

## <a name="test-your-custom-policy"></a>カスタム ポリシーのテスト

1. 証明書利用者ポリシー (`B2C_1A_signup_signin` など) を選択します。
1. **[アプリケーション]** には、[前に登録した](troubleshoot-custom-policies.md#troubleshoot-the-runtime) Web アプリケーションを選択します。 **[応答 URL]** に `https://jwt.ms` と表示されます。
1. **[今すぐ実行]** ボタンを選択します。
1. サインアップまたはサインイン ページで **[Apple]** を選択して、Apple ID でサインインします。

サインイン プロセスが成功すると、ブラウザーは `https://jwt.ms` にリダイレクトされ、Azure AD B2C によって返されたトークンの内容が表示されます。

::: zone-end
