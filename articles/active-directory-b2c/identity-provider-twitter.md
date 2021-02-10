---
title: Twitter アカウントでのサインアップおよびサインインを設定する
titleSuffix: Azure AD B2C
description: Azure Active Directory B2C を使用するアプリケーションで Twitter アカウントを持つ顧客にサインアップとサインインを提供します。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 01/27/2021
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: 65d3badc02efbb02df50189885c28a8abe851415
ms.sourcegitcommit: d1e56036f3ecb79bfbdb2d6a84e6932ee6a0830e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/29/2021
ms.locfileid: "99050449"
---
# <a name="set-up-sign-up-and-sign-in-with-a-twitter-account-using-azure-active-directory-b2c"></a>Azure Active Directory B2C を使用して Twitter アカウントでのサインアップおよびサインインを設定する

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]
::: zone pivot="b2c-custom-policy"

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

::: zone-end

## <a name="prerequisites"></a>[前提条件]

[!INCLUDE [active-directory-b2c-customization-prerequisites](../../includes/active-directory-b2c-customization-prerequisites.md)]

## <a name="create-an-application"></a>アプリケーションの作成

Azure AD B2C で Twitter アカウントを持つユーザーのサインインを有効にするには、Twitter アプリケーションを作成する必要があります。 まだ Twitter アカウントを持っていない場合は、[https://twitter.com/signup](https://twitter.com/signup) でサインアップできます。 また、[開発者アカウントの申請](https://developer.twitter.com/en/apply/user.html)も必要です。 詳細については、[アクセスの申請](https://developer.twitter.com/en/apply-for-access)に関するページを参照してください。

1. Twitter アカウント資格情報を使用して [Twitter の開発者ポータル](https://developer.twitter.com/portal/projects-and-apps)にサインインします。
1. **[スタンドアロン アプリ]** で、 **[+ アプリの作成]** を選択します。
1. **アプリの名前** を入力し、 **[完了]** を選択します。
1. **[アプリ キー]** の値と、 **[API キー シークレット]** をコピーします。  テナントで ID プロバイダーとして Twitter を構成するには、この両方の値を使用します。 
1. **[アプリのセットアップ]** で、 **[アプリ設定]** を選択します。
1. **[認証設定]** で **[編集]** を選択します。
    1. **[3-legged OAuth の有効化]** チェックボックスをオンにします。
    1. **[ユーザーのメール アドレスを要求する]** チェックボックスをオンにします。
    1. **[コールバック URL]** に「`https://your-tenant.b2clogin.com/your-tenant.onmicrosoft.com/your-user-flow-Id/oauth1/authresp`」と入力します。 `your-tenant` を自分のテナント名に置き換え、`your-user-flow-Id` を自分のユーザー フローの識別子に置き換えます。 たとえば、「 `b2c_1a_signup_signin_twitter` 」のように入力します。 テナント名とユーザー フロー ID が Azure AD B2C に大文字で定義されている場合でも、それらを入力するときに、すべて小文字を使用します。
    1. **[Web サイトの URL]** には「`https://your-tenant.b2clogin.com`」と入力します。 `your-tenant` をテナントの名前に置き換えます。 たとえば、「 `https://contosob2c.b2clogin.com` 」のように入力します。
    1. **[サービス利用規約]** に URL を入力します (例: `http://www.contoso.com/tos`)。 ポリシーの URL は、アプリケーションの利用規約を提供するために維持されるページです。
    1. **[プライバシー ポリシー]** に URL (`http://www.contoso.com/privacy` など) を入力します。 ポリシーの URL は、アプリケーションのプライバシーに関する情報を提供するために維持されるページです。
    1. **[保存]** を選択します。

::: zone pivot="b2c-user-flow"

## <a name="configure-twitter-as-an-identity-provider"></a>ID プロバイダーとして Twitter を構成する

1. Azure AD B2C テナントの全体管理者として [Azure Portal](https://portal.azure.com/) にサインインします。
1. ご利用の Azure AD B2C テナントを含むディレクトリを使用していることを確認してください。そのためには、トップ メニューにある **[ディレクトリ + サブスクリプション]** フィルターを選択して、ご利用のテナントを含むディレクトリを選択します。
1. Azure Portal の左上隅の **[すべてのサービス]** を選択し、 **[Azure AD B2C]** を検索して選択します。
1. **[ID プロバイダー]** を選択してから、**[Twitter]** を選択します。
1. **[名前]** を入力します。 たとえば、「*Twitter*」とします。
1. **[クライアント ID]** には、前に作成した Twitter アプリケーションの *API キー* を入力します。
1. **[クライアント シークレット]** には、記録した *API キー シークレット* を入力します。
1. **[保存]** を選択します。

## <a name="add-twitter-identity-provider-to-a-user-flow"></a>ユーザー フローに Twitter ID プロバイダーを追加する 

1. Azure AD B2C テナントで、 **[ユーザー フロー]** を選択します。
1. Twitter ID プロバイダーを追加するユーザー フローを選択します。
1. **[ソーシャル ID プロバイダー]** から、 **[Twitter]** を選択します。
1. **[保存]** を選択します。
1. ポリシーをテストするには、 **[ユーザー フローを実行します]** を選択します。
1. **[アプリケーション]** には、以前に登録した *testapp1* という名前の Web アプリケーションを選択します。 **[応答 URL]** に `https://jwt.ms` と表示されます。
1. **[ユーザー フローを実行します]** をクリックします

::: zone-end

::: zone pivot="b2c-custom-policy"

## <a name="create-a-policy-key"></a>ポリシー キーを作成する

Azure AD B2C テナントで前に記録した秘密鍵を格納する必要があります。

1. [Azure portal](https://portal.azure.com/) にサインインします。
2. ご自分の Azure AD B2C テナントが含まれるディレクトリを必ず使用してください。 上部メニューで **[ディレクトリ + サブスクリプション]** フィルターを選択し、ご利用のテナントが含まれるディレクトリを選択します。
3. Azure portal の左上隅にある **[すべてのサービス]** を選択してから、 **[Azure AD B2C]** を検索して選択します。
4. [概要] ページで、 **[Identity Experience Framework]** を選択します。
5. **[ポリシー キー]** を選択し、 **[追加]** を選択します。
6. **オプション** については、`Manual`を選択します。
7. ポリシー キーの **名前** を入力します。 たとえば、「 `TwitterSecret` 」のように入力します。 プレフィックス `B2C_1A_` がキーの名前に自動的に追加されます。
8. **[シークレット]** に、前に記録したクライアント シークレットを入力します。
9. **[キー使用法]** として [`Encryption`] を選択します。
10. **Create** をクリックしてください。

## <a name="configure-twitter-as-an-identity-provider"></a>ID プロバイダーとして Twitter を構成する

ユーザーが Twitter アカウントを使用してサインインできるようにするには、そのアカウントを Azure AD B2C がエンドポイント経由で通信できる相手のクレーム プロバイダーとして定義する必要があります。 エンドポイントは、特定のユーザーが認証されていることを確認するために Azure AD B2C で使う一連の要求を提供します。

Twitter アカウントをクレーム プロバイダーとして定義するには、そのアカウントをポリシーの拡張ファイル内の **ClaimsProviders** 要素に追加します。

1. *TrustFrameworkExtensions.xml* を開きます。
2. **ClaimsProviders** 要素を見つけます。 存在しない場合は、それをルート要素の下に追加します。
3. 新しい **ClaimsProvider** を次のように追加します。

    ```xml
    <ClaimsProvider>
      <Domain>twitter.com</Domain>
      <DisplayName>Twitter</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="Twitter-OAuth1">
          <DisplayName>Twitter</DisplayName>
          <Protocol Name="OAuth1" />
          <Metadata>
            <Item Key="ProviderName">Twitter</Item>
            <Item Key="authorization_endpoint">https://api.twitter.com/oauth/authenticate</Item>
            <Item Key="access_token_endpoint">https://api.twitter.com/oauth/access_token</Item>
            <Item Key="request_token_endpoint">https://api.twitter.com/oauth/request_token</Item>
            <Item Key="ClaimsEndpoint">https://api.twitter.com/1.1/account/verify_credentials.json?include_email=true</Item>
            <Item Key="ClaimsResponseFormat">json</Item>
            <Item Key="client_id">Your Twitter application API key</Item>
          </Metadata>
          <CryptographicKeys>
            <Key Id="client_secret" StorageReferenceId="B2C_1A_TwitterSecret" />
          </CryptographicKeys>
          <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="issuerUserId" PartnerClaimType="user_id" />
            <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="screen_name" />
            <OutputClaim ClaimTypeReferenceId="email" />
            <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="twitter.com" />
            <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="socialIdpAuthentication" />
          </OutputClaims>
          <OutputClaimsTransformations>
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

4. **client_id** の値を前に記録した *API キー シークレット* に置き換えます。
5. ファイルを保存します。

[!INCLUDE [active-directory-b2c-add-identity-provider-to-user-journey](../../includes/active-directory-b2c-add-identity-provider-to-user-journey.md)]


```xml
<OrchestrationStep Order="1" Type="CombinedSignInAndSignUp" ContentDefinitionReferenceId="api.signuporsignin">
  <ClaimsProviderSelections>
    ...
    <ClaimsProviderSelection TargetClaimsExchangeId="TwitterExchange" />
  </ClaimsProviderSelections>
  ...
</OrchestrationStep>

<OrchestrationStep Order="2" Type="ClaimsExchange">
  ...
  <ClaimsExchanges>
    <ClaimsExchange Id="TwitterExchange" TechnicalProfileReferenceId="Twitter-OAuth1" />
  </ClaimsExchanges>
</OrchestrationStep>
```

[!INCLUDE [active-directory-b2c-configure-relying-party-policy](../../includes/active-directory-b2c-configure-relying-party-policy-user-journey.md)]

[!INCLUDE [active-directory-b2c-test-relying-party-policy](../../includes/active-directory-b2c-test-relying-party-policy-user-journey.md)]


::: zone-end
