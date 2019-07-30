---
title: Azure Active Directory B2C でカスタム ポリシーを使用して Microsoft アカウント (MSA) を ID プロバイダーとして追加する
description: OpenID Connect (OIDC) プロトコルを使って Microsoft を ID プロバイダーとして使用する例。
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 07/08/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 9ae944a9d587e71c4be83bd524cf3875a7b52dd1
ms.sourcegitcommit: cf438e4b4e351b64fd0320bf17cc02489e61406a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/08/2019
ms.locfileid: "67654147"
---
# <a name="set-up-sign-in-with-a-microsoft-account-using-custom-policies-in-azure-active-directory-b2c"></a>Azure Active Directory B2C でカスタム ポリシーを使用して Microsoft アカウントでのサインインを設定する

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

この記事では、Azure Active Directory (Azure AD) B2C で[カスタム ポリシー](active-directory-b2c-overview-custom.md)を使用して Microsoft アカウントからのユーザーのサインインを有効にする方法について説明します。

## <a name="prerequisites"></a>前提条件

- 「[Azure Active Directory B2C でのカスタム ポリシーの概要](active-directory-b2c-get-started-custom.md)」にある手順を完了する。
- Microsoft アカウントをまだお持ちでない場合は、[https://www.live.com/](https://www.live.com/) で作成します。

## <a name="add-an-application"></a>アプリケーションを追加する

Microsoft アカウントを使用したユーザーのサインインを有効にするには、Azure AD テナント内でアプリケーションを登録する必要があります。 Azure AD テナントは、Azure AD B2C テナントと同じものではありません。

1. [Azure Portal](https://portal.azure.com) にサインインします。
1. お使いの Azure AD テナントを含むディレクトリを使用していることを確認してください。確認のために、トップ メニューにある **[ディレクトリとサブスクリプション フィルター]** をクリックして、お使いの Azure AD テナントを含むディレクトリを選択します。
1. Azure portal の左上隅にある **[すべてのサービス]** を選択し、 **[アプリの登録]** を検索して選択します。
1. **[新規登録]** を選択します。
1. アプリケーションの **[名前]** を入力します。 たとえば、 *MSAapp1* です。
1. **[サポートされているアカウントの種類]** で、 **[任意の組織のディレクトリ内のアカウントと、個人用の Microsoft アカウント (Skype、Xbox、Outlook.com など)]** を選択します。
1. **[リダイレクト URI (省略可能)]** で、 **[Web]** を選択し、テキスト ボックスに「`https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp`」と入力します。 `your-tenant-name` を Azure AD B2C テナント名に置き換えます。
1. **[登録]** を選択します
1. アプリケーションの [概要] ページに表示されている **[アプリケーション (クライアント) ID]** を記録します。 これは、後のセクションでクレーム プロバイダーを構成するときに必要です。
1. **[証明書とシークレット]** を選択します。
1. **[新しいクライアント シークレット]** をクリックします。
1. シークレットの **[説明]** を「*MSA アプリケーション クライアント シークレット*」のように入力して、 **[追加]** をクリックします。
1. **[値]** 列に表示されているアプリケーション パスワードを記録します。 次のセクションでこの値を使用します。

## <a name="create-a-policy-key"></a>ポリシー キーを作成する

これで、Azure AD テナント内にアプリケーションが作成されたので、そのアプリケーションのクライアント シークレットを Azure AD B2C テナント内に格納する必要があります。

1. [Azure Portal](https://portal.azure.com/) にサインインします。
1. ご自分の Azure AD B2C テナントが含まれるディレクトリを必ず使用してください。 上部メニューで **[ディレクトリとサブスクリプション] フィルター**を選択し、ご利用のテナントが含まれるディレクトリを選択します。
1. Azure portal の左上隅にある **[すべてのサービス]** を選択してから、 **[Azure AD B2C]** を検索して選択します。
1. [概要] ページで、 **[Identity Experience Framework]** を選択します。
1. **[ポリシー キー]** を選択し、 **[追加]** を選択します。
1. **オプション**については、`Manual`を選択します。
1. ポリシー キーの**名前**を入力します。 たとえば、「 `MSASecret` 」のように入力します。 プレフィックス `B2C_1A_` がキーの名前に自動的に追加されます。
1. **[シークレット]** に、前のセクションで記録したクライアント シークレットを入力します。
1. **[キー使用法]** として [`Signature`] を選択します。
1. **Create** をクリックしてください。

## <a name="add-a-claims-provider"></a>クレーム プロバイダーを追加する

ユーザーが Microsoft アカウントを使用してサインインできるようにするには、そのアカウントを Azure AD B2C がエンドポイント経由で通信できる相手のクレーム プロバイダーとして定義する必要があります。 エンドポイントは、特定のユーザーが認証されていることを確認するために Azure AD B2C で使う一連の要求を提供します。

ポリシーの拡張ファイル内で **ClaimsProvider** 要素に追加することで、Azure AD をクレーム プロバイダーとして定義できます。

1. *TrustFrameworkExtensions.xml* ポリシー ファイルを開きます。
1. **ClaimsProviders** 要素を見つけます。 存在しない場合は、それをルート要素の下に追加します。
1. 新しい **ClaimsProvider** を次のように追加します。

    ```xml
    <ClaimsProvider>
      <Domain>live.com</Domain>
      <DisplayName>Microsoft Account</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="MSA-OIDC">
          <DisplayName>Microsoft Account</DisplayName>
          <Protocol Name="OpenIdConnect" />
          <Metadata>
            <Item Key="ProviderName">https://login.live.com</Item>
            <Item Key="METADATA">https://login.live.com/.well-known/openid-configuration</Item>
            <Item Key="response_types">code</Item>
            <Item Key="response_mode">form_post</Item>
            <Item Key="scope">openid profile email</Item>
            <Item Key="HttpBinding">POST</Item>
            <Item Key="UsePolicyInRedirectUri">0</Item>
            <Item Key="client_id">Your Microsoft application client ID</Item>
          </Metadata>
          <CryptographicKeys>
            <Key Id="client_secret" StorageReferenceId="B2C_1A_MSASecret" />
          </CryptographicKeys>
          <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="live.com" />
            <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="socialIdpAuthentication" />
            <OutputClaim ClaimTypeReferenceId="issuerUserId" PartnerClaimType="sub" />
            <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="name" />
            <OutputClaim ClaimTypeReferenceId="email" />
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

1. **client_id** の値を、前に記録した Azure AD アプリケーションの "*アプリケーション (クライアント) ID*" に置き換えます。
1. ファイルを保存します。

これで、Azure AD B2C で Azure AD 内の Microsoft アカウント アプリケーションと通信する方法を認識できるようにポリシーが構成されました。

### <a name="upload-the-extension-file-for-verification"></a>拡張ファイルのアップロードによる確認

続行する前に、変更したポリシーをアップロードして、ここまで問題がないことを確認します。

1. Azure portal で Azure AD B2C テナントに移動し、 **[Identity Experience Framework]** を選択します。
1. **[カスタム ポリシー]** ページで、 **[カスタムポリシーのアップロード]** を選択します。
1. **[ポリシーが存在する場合は上書きする]** を有効にし、*TrustFrameworkExtensions.xml* ファイルを参照して選択します。
1. **[アップロード]** をクリックします。

ポータルにエラーが表示されなれば、次のセクションに進んでください。

## <a name="register-the-claims-provider"></a>クレーム プロバイダーを登録する

この時点で、ID プロバイダーは設定されていますが、まだどのサインアップまたはサインイン画面でも使用できません。 これを使用できるようにするには、既存のテンプレート ユーザー体験の複製を作成してから、Microsoft アカウント ID プロバイダーも含まれるようにそれを変更します。

1. スターター パックから *TrustFrameworkBase.xml* ファイルを開きます。
1. `Id="SignUpOrSignIn"` を含む **UserJourney** 要素を見つけ、その内容全体をコピーします。
1. *TrustFrameworkExtensions.xml* を開き、**UserJourneys** 要素を見つけます。 要素が存在しない場合は追加します。
1. コピーした **UserJourney** 要素の内容全体を **UserJourneys** 要素の子として貼り付けます。
1. ユーザー体験の ID の名前を変更します。 たとえば、「 `SignUpSignInMSA` 」のように入力します。

### <a name="display-the-button"></a>ボタンを表示する

**ClaimsProviderSelection** 要素は、サインアップまたはサインイン画面の ID プロバイダーのボタンに類似しています。 Microsoft アカウント用の **ClaimsProviderSelection** 要素を追加すると、ユーザーがこのページにアクセスしたときに新しいボタンが表示されます。

1. *TrustFrameworkExtensions.xml* ファイルで、作成したユーザー体験内に `Order="1"` を含む **OrchestrationStep** 要素を見つけます。
1. **ClaimsProviderSelects** の下に、次の要素を追加します。 **TargetClaimsExchangeId** の値を適切な値 (`MicrosoftAccountExchange` など) に設定します。

    ```XML
    <ClaimsProviderSelection TargetClaimsExchangeId="MicrosoftAccountExchange" />
    ```

### <a name="link-the-button-to-an-action"></a>ボタンのアクションへのリンク

ボタンが所定の位置に配置されたので、ボタンをアクションにリンクする必要があります。 この場合のアクションでは、Azure AD B2C が Microsoft アカウントと通信してトークンを受信します。

1. ユーザー体験内で、`Order="2"` を含む **OrchestrationStep** を見つけます。
1. 次の **ClaimsExchange** 要素を追加します。ID には、**TargetClaimsExchangeId** に使用したのと同じ値を使用するようにしてください。

    ```xml
    <ClaimsExchange Id="MicrosoftAccountExchange" TechnicalProfileReferenceId="MSA-OIDC" />
    ```

    **TechnicalProfileReferenceId** の値を、先ほど追加したクレーム プロバイダーの **TechnicalProfile** 要素内の `Id` 値と一致するように更新します。 たとえば、「 `MSA-OIDC` 」のように入力します。

1. *TrustFrameworkExtensions.xml* ファイルを保存し、確認のために再度アップロードします。

## <a name="create-an-azure-ad-b2c-application"></a>Azure AD B2C アプリケーションを作成する

Azure AD B2C との通信は、Azure AD B2C テナント内に作成されたアプリケーション経由で行われます。 このセクションでは、テスト アプリケーションをまだ作成していない場合にそれを作成するための省略可能な手順を紹介します。

1. [Azure Portal](https://portal.azure.com) にサインインします。
1. ご自分の Azure AD B2C テナントが含まれるディレクトリを必ず使用してください。 上部メニューで **[ディレクトリとサブスクリプション] フィルター**を選択し、ご利用のテナントが含まれるディレクトリを選択します。
1. Azure portal の左上隅にある **[すべてのサービス]** を選択してから、 **[Azure AD B2C]** を検索して選択します。
1. **[アプリケーション]** を選択し、 **[追加]** を選択します。
1. アプリケーションの名前を入力します (*testapp1* など)。
1. **[Web アプリ / Web API]** には `Yes` を選択し、 **[応答 URL]** に `https://jwt.ms` を入力します。
1. **Create** をクリックしてください。

## <a name="update-and-test-the-relying-party-file"></a>証明書利用者ファイルを更新し、テストする

作成したユーザー体験を開始する証明書利用者 (RP) ファイルを更新します。

1. 作業ディレクトリに *SignUpOrSignIn.xml* のコピーを作成し、名前を変更します。 たとえば、その名前を *SignUpSignInMSA.xml* に変更します。
1. 新しいファイルを開き、**TrustFrameworkPolicy** の **PolicyId** 属性の値を一意の値で更新します。 たとえば、「 `SignUpSignInMSA` 」のように入力します。
1. **PublicPolicyUri** の値をポリシーの URI に更新します。 たとえば、`http://contoso.com/B2C_1A_signup_signin_msa` にします。
1. **DefaultUserJourney** 内の **ReferenceId** 属性の値を、先ほど作成したユーザー体験の ID (SignUpSignInMSA) と一致するように更新します。
1. 変更を保存し、ファイルをアップロードし、一覧から新しいポリシーを選択します。
1. 前のセクションで作成した Azure AD B2C アプリケーション (または、前提条件の完了によるもの (*webapp1* や *testapp1*など)) が **[アプリケーションの選択]** フィールドで選択されていることを確認した後、 **[今すぐ実行]** をクリックしてそれをテストします。
1. **[Microsoft アカウント]** ボタンを選択し、サインインします。

    サインイン操作が成功した場合は、`jwt.ms` にリダイレクトされ、次のようなデコードされたトークンが表示されます。

    ```json
    {
      "typ": "JWT",
      "alg": "RS256",
      "kid": "<key-ID>"
    }.{
      "exp": 1562365200,
      "nbf": 1562361600,
      "ver": "1.0",
      "iss": "https://your-b2c-tenant.b2clogin.com/10000000-0000-0000-0000-000000000000/v2.0/",
      "sub": "20000000-0000-0000-0000-000000000000",
      "aud": "30000000-0000-0000-0000-000000000000",
      "acr": "b2c_1a_signupsigninmsa",
      "nonce": "defaultNonce",
      "iat": 1562361600,
      "auth_time": 1562361600,
      "idp": "live.com",
      "name": "Azure User",
      "email": "azureuser@contoso.com",
      "tid": "6fc3b573-7b38-4c0c-b627-2e8684f6c575"
    }.[Signature]
    ```
