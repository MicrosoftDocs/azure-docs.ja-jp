---
title: Azure Active Directory B2C のカスタム ポリシーを使用してマルチテナント Azure AD ID プロバイダーのサインインを設定する
description: カスタム ポリシーを使用してマルチテナントの Azure AD ID プロバイダーを追加する - Azure Active Directory B2C。
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 21bd9d98bc59424d05ee20d91e52c78b5b0efc4a
ms.sourcegitcommit: fbea2708aab06c19524583f7fbdf35e73274f657
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/13/2019
ms.locfileid: "70964493"
---
# <a name="set-up-sign-in-for-multi-tenant-azure-active-directory-using-custom-policies-in-azure-active-directory-b2c"></a>Azure Active Directory B2C のカスタム ポリシーを使用してマルチテナント Azure Active Directory を設定する

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

この記事では、Azure AD B2C 内で[カスタム ポリシー](active-directory-b2c-overview-custom.md)を使用し、Azure Active Directory (Azure AD) のマルチテナント エンドポイントによってユーザーのサインインを有効にする方法について説明します。 Azure AD テナントごとに ID プロバイダーを構成しなくても、複数のテナントのユーザーが Azure AD B2C を使用してサインインできるようになります。 ただし、これらのテナントのいずれのゲスト メンバーもサインインは**できません**。 そのため、[各テナントを個別に構成する](active-directory-b2c-setup-aad-custom.md)必要があります。

## <a name="prerequisites"></a>前提条件

「[Azure Active Directory B2C でのカスタム ポリシーの概要](active-directory-b2c-get-started-custom.md)」にある手順を完了する。

## <a name="register-an-application"></a>アプリケーションを登録する

特定の Azure AD 組織のユーザーのサインインを有効にするには、組織の Azure AD テナント内でアプリケーションを登録する必要があります。

1. [Azure Portal](https://portal.azure.com) にサインインします。
1. 組織の Azure AD テナント (contoso.com など) が含まれているディレクトリを使用していることを確認します。 上部メニューで **[ディレクトリ + サブスクリプション]** フィルターを選択し、ご利用のテナントが含まれるディレクトリを選択します。
1. Azure portal の左上隅にある **[すべてのサービス]** を選択し、 **[アプリの登録]** を検索して選択します。
1. **[新規登録]** を選択します。
1. アプリケーションの **[名前]** を入力します。 たとえば、「 `Azure AD B2C App` 」のように入力します。
1. このアプリケーションの **[任意の組織のディレクトリ内のアカウント]** を選択します。
1. **[リダイレクト URL]** では、値 **[Web]** をそのまま使用し、次の URL をすべて小文字で入力します。`your-B2C-tenant-name` は、お使いの Azure AD B2C テナントの名前に置き換えます。

    ```
    https://your-B2C-tenant-name.b2clogin.com/your-B2C-tenant-name.onmicrosoft.com/oauth2/authresp
    ```

    たとえば、「 `https://contoso.b2clogin.com/contoso.onmicrosoft.com/oauth2/authresp` 」のように入力します。

1. **[登録]** を選択します。 後の手順で使用するために、**アプリケーション (クライアント) ID** を記録しておきます。
1. **[Certificates & secrets]\(証明書とシークレット\)** を選択してから、 **[New client secret]\(新しいクライアント シークレット\)** を選択します。
1. シークレットの**説明**を入力し、有効期限を選択して、 **[追加]** を選択します。 後の手順で使用するために、シークレットの **VALUE** を記録しておきます。

## <a name="create-a-policy-key"></a>ポリシー キーを作成する

作成したアプリケーション キーを Azure AD B2C テナントに格納する必要があります。

1. ご自分の Azure AD B2C テナントが含まれるディレクトリを必ず使用してください。 上部のメニューにある **[ディレクトリ + サブスクリプション] フィルター**を選択し、Azure AD B2C テナントを含むディレクトリを選択します。
1. Azure portal の左上隅にある **[すべてのサービス]** を選択してから、 **[Azure AD B2C]** を検索して選択します。
1. **[ポリシー]** で **[Identity Experience Framework]** を選択します。
1. **[ポリシー キー]** を選択し、 **[追加]** を選択します。
1. **オプション**については、`Manual`を選択します。
1. ポリシー キーの**名前**を入力します。 たとえば、「 `AADAppSecret` 」のように入力します。  作成時に、プレフィックス `B2C_1A_` がキーの名前に自動的に追加されるため、次のセクションの XML での参照は *B2C_1A_AADAppSecret* になります。
1. **[シークレット]** に、前に記録したクライアント シークレットを入力します。
1. **[キー使用法]** として [`Signature`] を選択します。
1. **作成** を選択します。

## <a name="add-a-claims-provider"></a>クレーム プロバイダーを追加する

ユーザーが Azure AD を使用してサインインできるようにするには、エンドポイント経由で Azure AD B2C が通信できるクレーム プロバイダーとして Azure AD を定義する必要があります。 エンドポイントは、特定のユーザーが認証されていることを確認するために Azure AD B2C で使う一連の要求を提供します。

ポリシーの拡張ファイル内で Azure AD を **ClaimsProvider** 要素に追加することで、Azure AD をクレーム プロバイダーとして定義できます。

1. *TrustFrameworkExtensions.xml* ファイルを開きます。
1. **ClaimsProviders** 要素を見つけます。 存在しない場合は、それをルート要素の下に追加します。
1. 新しい **ClaimsProvider** を次のように追加します。

    ```XML
    <ClaimsProvider>
      <Domain>commonaad</Domain>
      <DisplayName>Common AAD</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="Common-AAD">
          <DisplayName>Multi-Tenant AAD</DisplayName>
          <Description>Login with your Contoso account</Description>
          <Protocol Name="OpenIdConnect"/>
          <Metadata>
            <Item Key="METADATA">https://login.windows.net/common/.well-known/openid-configuration</Item>
            <!-- Update the Client ID below to the Application ID -->
            <Item Key="client_id">00000000-0000-0000-0000-000000000000</Item>
            <Item Key="response_types">code</Item>
            <Item Key="scope">openid</Item>
            <Item Key="response_mode">form_post</Item>
            <Item Key="HttpBinding">POST</Item>
            <Item Key="UsePolicyInRedirectUri">false</Item>
            <Item Key="DiscoverMetadataByTokenIssuer">true</Item>
            <!-- The key below allows you to specify each of the Azure AD tenants that can be used to sign in. Update the GUIDs below for each tenant. -->
            <Item Key="ValidTokenIssuerPrefixes">https://sts.windows.net/00000000-0000-0000-0000-000000000000,https://sts.windows.net/11111111-1111-1111-1111-111111111111</Item>
            <!-- The commented key below specifies that users from any tenant can sign-in. Uncomment if you would like anyone with an Azure AD account to be able to sign in. -->
            <!-- <Item Key="ValidTokenIssuerPrefixes">https://sts.windows.net/</Item> -->
          </Metadata>
          <CryptographicKeys>
            <Key Id="client_secret" StorageReferenceId="B2C_1A_AADAppSecret"/>
          </CryptographicKeys>
          <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="issuerUserId" PartnerClaimType="oid"/>
            <OutputClaim ClaimTypeReferenceId="tenantId" PartnerClaimType="tid"/>
            <OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="given_name" />
            <OutputClaim ClaimTypeReferenceId="surName" PartnerClaimType="family_name" />
            <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="name" />
            <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="socialIdpAuthentication" AlwaysUseDefaultValue="true" />
            <OutputClaim ClaimTypeReferenceId="identityProvider" PartnerClaimType="iss" />
          </OutputClaims>
          <OutputClaimsTransformations>
            <OutputClaimsTransformation ReferenceId="CreateRandomUPNUserName"/>
            <OutputClaimsTransformation ReferenceId="CreateUserPrincipalName"/>
            <OutputClaimsTransformation ReferenceId="CreateAlternativeSecurityId"/>
            <OutputClaimsTransformation ReferenceId="CreateSubjectClaimFromAlternativeSecurityId"/>
          </OutputClaimsTransformations>
          <UseTechnicalProfileForSessionManagement ReferenceId="SM-SocialLogin"/>
        </TechnicalProfile>
      </TechnicalProfiles>
    </ClaimsProvider>
    ```

1. **ClaimsProvider** 要素の下で、**Domain** の値を、他の ID プロバイダーと区別するために使用できる一意の値に更新します。
1. **TechnicalProfile** 要素の下で、**DisplayName** の値を更新します (例: `Contoso Employee`)。 この値は、サインイン ページのサインイン ボタン上に表示されます。
1. **client_id** は、前に登録した Azure AD マルチテナント アプリケーションのアプリケーション ID に設定します。
1. **CryptographicKeys** で、**StorageReferenceId** の値を、前に作成したポリシー キーの名前に更新します。 たとえば、「 `B2C_1A_AADAppSecret` 」のように入力します。

### <a name="restrict-access"></a>アクセスを制限する

> [!NOTE]
> **ValidTokenIssuerPrefixes** の値として `https://sts.windows.net` を使用すると、すべての Azure AD ユーザーがアプリケーションにサインインできるようになります。

有効なトークン発行者の一覧を更新し、サインインできる Azure AD テナント ユーザーの特定の一覧へのアクセスを制限する必要があります。

値を取得するには、サインインさせるユーザーの Azure AD テナントごとに、OpenID Connect Discovery のメタデータを調べます。 メタデータの URL の形式は、`https://login.windows.net/your-tenant/.well-known/openid-configuration` のようになっています。`your-tenant` は Azure AD テナントの名前です。 例:

`https://login.windows.net/fabrikam.onmicrosoft.com/.well-known/openid-configuration`

サインインに使用する Azure AD テナントごとに次の手順を実行します。

1. ブラウザーを開き、そのテナントに対応する OpenID Connect のメタデータ URL に移動します。 **発行者**オブジェクトを探し、その値を記録します。 `https://sts.windows.net/00000000-0000-0000-0000-000000000000/` のようになっていると思います。
1. その値をコピーして **ValidTokenIssuerPrefixes** キーに貼り付けます。 複数の発行者は、コンマで区切ります。 前出の `ClaimsProvider` XML サンプルでは、発行者が 2 つ存在する例を確認できます。

### <a name="upload-the-extension-file-for-verification"></a>拡張ファイルのアップロードによる確認

ここまでで、Azure AD B2C が Azure AD ディレクトリと通信する方法を認識するようにポリシーを設定しました。 ポリシーの拡張ファイルをアップロードして、現時点で問題がないことを確認してみます。

1. Azure AD B2C テナントの **[カスタム ポリシー]** ページで、 **[ポリシーのアップロード]** を選択します。
2. **[ポリシーが存在する場合は上書きする]** を有効にし、*TrustFrameworkExtensions.xml* ファイルを参照して選択します。
3. **[アップロード]** を選択します。

## <a name="register-the-claims-provider"></a>クレーム プロバイダーを登録する

この時点では、ID プロバイダーはセットアップされていますが、サインアップ/サインイン画面で使用することはできません。 これを使用できるようにするには、既存のテンプレート ユーザー体験の複製を作成してから、Azure AD の ID プロバイダーも含まれるように変更します。

1. スターター パックの *TrustFrameworkBase.xml* ファイルを開きます。
2. `Id="SignUpOrSignIn"` を含む **UserJourney** 要素を見つけ、その内容全体をコピーします。
3. *TrustFrameworkExtensions.xml* を開き、**UserJourneys** 要素を見つけます。 要素が存在しない場合は追加します。
4. コピーした **UserJourney** 要素の内容全体を **UserJourneys** 要素の子として貼り付けます。
5. ユーザー体験の ID の名前を変更します。 たとえば、「 `SignUpSignInContoso` 」のように入力します。

### <a name="display-the-button"></a>ボタンを表示する

**ClaimsProviderSelection** 要素は、サインアップおよびサインイン画面の ID プロバイダー ボタンに類似しています。 Azure AD 用の **ClaimsProviderSelection** 要素を追加すると、ユーザーがページにアクセスしたときに新しいボタンが表示されます。

1. *TrustFrameworkExtensions.xml* で、作成したユーザー体験内に `Order="1"` を含む **OrchestrationStep** 要素を見つけます。
1. **ClaimsProviderSelects** の下に、次の要素を追加します。 **TargetClaimsExchangeId** の値を適切な値 (`AzureADExchange` など) に設定します。

    ```XML
    <ClaimsProviderSelection TargetClaimsExchangeId="AzureADExchange" />
    ```

### <a name="link-the-button-to-an-action"></a>ボタンのアクションへのリンク

ボタンが所定の位置に配置されたので、ボタンをアクションにリンクする必要があります。 この場合のアクションでは、Azure AD B2C が Azure AD と通信してトークンを受信します。 Azure AD 要求プロバイダーの技術プロファイルをリンクすることで、ボタンをアクションにリンクします。

1. ユーザー体験内で、`Order="2"` を含む **OrchestrationStep** を見つけます。
2. 次の **ClaimsExchange** 要素を追加します。**TargetClaimsExchangeId** に使用した **Id** と同じ値を必ずご使用ください。

    ```XML
    <ClaimsExchange Id="AzureADExchange" TechnicalProfileReferenceId="Common-AAD" />
    ```

    **TechnicalProfileReferenceId** の値を、前に作成した技術プロファイルの **Id** に更新します。 たとえば、「 `Common-AAD` 」のように入力します。

3. *TrustFrameworkExtensions.xml* ファイルを保存し、確認のために再度アップロードします。

## <a name="create-an-azure-ad-b2c-application"></a>Azure AD B2C アプリケーションを作成する

Azure AD B2C との通信は、B2C テナントで登録したアプリケーションを介して行われます。 このセクションでは、テスト アプリケーションをまだ作成していない場合にそれを作成するための省略可能な手順を紹介します。

1. [Azure Portal](https://portal.azure.com) にサインインします。
1. ご自分の Azure AD B2C テナントが含まれるディレクトリを必ず使用してください。 上部のメニューにある **[ディレクトリ + サブスクリプション] フィルター**を選択し、Azure AD B2C テナントを含むディレクトリを選択します。
1. Azure portal の左上隅にある **[すべてのサービス]** を選択してから、 **[Azure AD B2C]** を検索して選択します。
1. **[アプリケーション]** を選択し、 **[追加]** を選択します。
1. アプリケーションの名前を入力します (*testapp1* など)。
1. **[Web アプリ / Web API]** には `Yes` を選択し、 **[応答 URL]** に `https://jwt.ms` を入力します。
1. **作成** を選択します。

## <a name="update-and-test-the-relying-party-file"></a>証明書利用者ファイルを更新し、テストする

作成したユーザー体験を開始する証明書利用者 (RP) ファイルを更新します。

1. 作業ディレクトリに *SignUpOrSignIn.xml* のコピーを作成し、名前を変更します。 たとえば、*SignUpSignContoso.xml* に名前を変更します。
1. 新しいファイルを開き、**TrustFrameworkPolicy** の **PolicyId** 属性の値を一意の値で更新します。 たとえば、「 `SignUpSignInContoso` 」のように入力します。
1. **PublicPolicyUri** の値をポリシーの URI に更新します。 たとえば、「 `http://contoso.com/B2C_1A_signup_signin_contoso` 」のように入力します。
1. **DefaultUserJourney** 内の **ReferenceId** 属性の値を、先ほど作成したユーザー体験の ID と一致するように更新します。 たとえば、*SignUpSignInContoso* とします。
1. 変更内容を保存し、ファイルをアップロードします。
1. **[カスタム ポリシー]** で、一覧から新しいポリシーを選択します。
1. **[アプリケーションの選択]** ボックスの一覧で、前の手順で作成した Azure AD B2C アプリケーションを選択します (例: *testapp1*)。
1. **[今すぐ実行のエンドポイント]** をコピーし、プライベート ブラウザー ウィンドウ (Google のシークレット モード、Microsoft Edge の InPrivate ウィンドウなど) で開きます。 プライベート ブラウザー ウィンドウで開くと、現在キャッシュされている Azure AD の資格情報を使用せずに、ユーザー体験を全体的にテストできます。
1. Azure AD のサインイン ボタン (たとえば、*Contoso Employee*) を選択し、いずれかの Azure AD 組織テナントのユーザーの資格情報を入力します。 アプリケーションを承認するように求められたら、プロファイルの情報を入力します。

サインイン プロセスが成功すると、ブラウザーは `https://jwt.ms` にリダイレクトされ、Azure AD B2C によって返されたトークンの内容が表示されます。

マルチテナントのサインイン機能をテストするために、別の Azure AD テナントに存在するユーザーの資格情報を使用して、最後の 2 つの手順を実行してください。

## <a name="next-steps"></a>次の手順

カスタム ポリシーを操作する場合、開発過程でポリシーのトラブルシューティングを行っているときに、追加情報が必要になることがあります。

問題の診断に役立てるために、ポリシーを一時的に "開発者モード" にして、Azure Application Insights を使用してログを収集できます。 その方法については、[Azure Active Directory B2C: ログの収集](active-directory-b2c-troubleshoot-custom.md)に関するページを参照してください。
