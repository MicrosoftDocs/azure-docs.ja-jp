---
title: "Azure Active Directory B2C: カスタム ポリシーを使って Google+ を OAuth2 ID プロバイダーとして追加する"
description: "OAuth2 プロトコルを使った ID プロバイダーとしての Google+ の使用のサンプル"
services: active-directory-b2c
documentationcenter: 
author: yoelhor
manager: joroja
editor: 
ms.assetid: 
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: article
ms.devlang: na
ms.date: 08/04/2017
ms.author: yoelh
ms.openlocfilehash: e0aaf710d230f7667fff32b50ddb64104509d740
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="azure-active-directory-b2c-add-google-as-an-oauth2-identity-provider-using-custom-policies"></a>Azure Active Directory B2C: カスタム ポリシーを使って Google+ を OAuth2 ID プロバイダーとして追加する

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

このガイドでは、[カスタム ポリシー](active-directory-b2c-overview-custom.md)を使って Google+ アカウントからのユーザーのサインインを有効にする方法を示します。

## <a name="prerequisites"></a>前提条件

[カスタム ポリシーの概要](active-directory-b2c-get-started-custom.md)に関する記事の手順を完了します。

その手順は次のとおりです。

1.  Google+ アカウント アプリケーションを作成する。
2.  Azure AD B2C に Google+ アカウント アプリケーション キーを追加する
3.  ポリシーにクレーム プロバイダーを追加する
4.  ユーザー体験に Google+ アカウント クレーム プロバイダーを登録する
5.  Azure AD B2C テナントにポリシーをアップロードし、テストする

## <a name="create-a-google-account-application"></a>Google+ アカウント アプリケーションを作成する
Azure Active Directory (Azure AD) B2C で ID プロバイダーとして Google+ を使用するには、Google+ アプリケーションを作成し、適切なパラメーターを提供する必要があります。 Googl + アプリケーションは、[https://accounts.google.com/SignUp](https://accounts.google.com/SignUp) で登録できます

1.  [Google Developers Console](https://console.developers.google.com/) に移動し、Google+ アカウントの資格情報でサインインします。
2.  **[プロジェクトの作成]** をクリックし、**[プロジェクト名]** を入力して、**[作成]** をクリックします。

3.  **[プロジェクトを選択] メニュー**をクリックします。

    ![Google+ アカウント - プロジェクトを選択](media/active-directory-b2c-custom-setup-goog-idp/goog-add-new-app1.png)

4.  **[+]** ボタンをクリックします。

    ![Google+ アカウント - 新しいプロジェクトの作成](media/active-directory-b2c-custom-setup-goog-idp//goog-add-new-app2.png)

5.  **[プロジェクト名]** に入力し、**[作成]** をクリックします。

    ![Google+ アカウント - 新しいプロジェクト](media/active-directory-b2c-custom-setup-goog-idp//goog-app-name.png)

6.  プロジェクトの準備が完了するまで待ち、**[プロジェクトを選択] メニュー**をクリックします。

    ![Google+ アカウント - 新しいプロジェクトを使えるようになるまで待つ](media/active-directory-b2c-custom-setup-goog-idp//goog-select-app1.png)

7.  プロジェクト名をクリックします。

    ![Google+ アカウント - 新しいプロジェクトを選択](media/active-directory-b2c-custom-setup-goog-idp//goog-select-app2.png)

8.  左側のナビゲーションで、**[API マネージャー]** をクリックし、**[認証情報]** をクリックします。
9.  上部の **[OAuth 同意画面]** タブをクリックします。

    ![Google+ アカウント - [OAuth 同意画面] の設定](media/active-directory-b2c-custom-setup-goog-idp/goog-add-cred.png)

10.  有効な **[メール アドレス]** を選択または指定し、**サービス名**を入力して、**[保存]** をクリックします。

    ![Google+ - アプリケーション認証情報](media/active-directory-b2c-custom-setup-goog-idp/goog-consent-screen.png)

11.  **[新しい資格情報]** をクリックし、**[OAuth クライアント ID]** を選択します。

    ![Google+ - 新しいアプリケーション認証情報の作成](media/active-directory-b2c-custom-setup-goog-idp/goog-add-oauth2-client-id.png)

12.  **[アプリケーションの種類]** で **[Web アプリケーション]** を選択します。

    ![Google+ - アプリケーションの種類の選択](media/active-directory-b2c-custom-setup-goog-idp/goog-web-app.png)

13.  アプリケーションの**名前**を指定します。**[承認済みの JavaScript 生成元]** フィールドに「`https://login.microsoftonline.com`」と入力し、**[承認済みのリダイレクト URI]** フィールドに「`https://login.microsoftonline.com/te/{tenant}/oauth2/authresp`」と入力します。 **{tenant}** は、実際のテナントの名前 (例: contosob2c.onmicrosoft.com) に置き換えます。 **{tenant}** の値は大文字小文字が区別されます。 **Create** をクリックしてください。

    ![Google+ - 承認された JavaScript 生成元とリダイレクト URI の指定](media/active-directory-b2c-custom-setup-goog-idp/goog-create-client-id.png)

14.  **[クライアント ID]** と **[クライアント シークレット]** の値をコピーします。 テナントで ID プロバイダーとして Google+ を構成するには、両方の値が必要です。 **[クライアント シークレット]** は、重要なセキュリティ資格情報です。

    ![Google+ - [クライアント ID] と [クライアント シークレット] の値のコピー](media/active-directory-b2c-custom-setup-goog-idp/goog-client-secret.png)

## <a name="add-the-google-account-application-key-to-azure-ad-b2c"></a>Azure AD B2C に Google+ アカウント アプリケーション キーを追加する
Google+ アカウントとのフェデレーションには、Google+ アカウントのクライアント シークレットがアプリケーションに代わって Azure AD B2C を信頼する必要があります。 Azure AD B2C テナントに Google+ アプリケーション シークレットを格納する必要があります。  

1.  Azure AD B2C テナントに移動し、**[B2C Settings]\(B2C 設定\)**  >  **[Identity Experience Framework]** の順に選択します。
2.  **[ポリシー キー]** を選択して、テナント内で利用できるキーを表示します。
3.  **[+ 追加]** をクリックします。
4.  **[オプション]** には **[Manual] \(手動)** を使用します。
5.  **[名前]** には `GoogleSecret` を使用します。  
    プレフィックス `B2C_1A_` が自動的に追加される場合があります。
6.  **[シークレット]** ボックスに、https://apps.dev.microsoft.com からの Microsoft アプリケーション シークレットを入力します
7.  **[キー使用法]** には **[署名]** を使用します。
8.  **[作成]**
9.  キー `B2C_1A_GoogleSecret` を作成したことを確認します。

## <a name="add-a-claims-provider-in-your-extension-policy"></a>拡張ポリシーにクレーム プロバイダーを追加する

ユーザーが Google+ アカウントを使ってサインインできるようにするには、Google+ アカウントをクレーム プロバイダーとして定義する必要があります。 つまり、Azure AD B2C が通信するエンドポイントを指定する必要があります。 エンドポイントは、特定のユーザーが認証されていることを確認するために Azure AD B2C で使う一連の要求を提供します。

拡張ポリシー ファイルに `<ClaimsProvider>` ノードを追加することで、Google+ アカウントをクレーム プロバイダーとして定義します。

1.  作業ディレクトリから拡張ポリシー ファイル (TrustFrameworkExtensions.xml) を開きます。 XML エディターが必要な場合は、軽量のクロスプラットフォーム エディターである [Visual Studio Code](https://code.visualstudio.com/download) をお試しください。
2.  `<ClaimsProviders>` セクションを探します。
3.  ファイルを保存する前に、`ClaimsProviders` 要素の下に次の XML スニペットを追加し、`client_id` 値を Google+ アカウント アプリケーション クライアント ID で置換します。  

```xml
<ClaimsProvider>
    <Domain>google.com</Domain>
    <DisplayName>Google</DisplayName>
    <TechnicalProfiles>
    <TechnicalProfile Id="Google-OAUTH">
        <DisplayName>Google</DisplayName>
        <Protocol Name="OAuth2" />
        <Metadata>
        <Item Key="ProviderName">google</Item>
        <Item Key="authorization_endpoint">https://accounts.google.com/o/oauth2/auth</Item>
        <Item Key="AccessTokenEndpoint">https://accounts.google.com/o/oauth2/token</Item>
        <Item Key="ClaimsEndpoint">https://www.googleapis.com/oauth2/v1/userinfo</Item>
        <Item Key="scope">email</Item>
        <Item Key="HttpBinding">POST</Item>
        <Item Key="UsePolicyInRedirectUri">0</Item>
        <Item Key="client_id">Your Google+ application ID</Item>
        </Metadata>
        <CryptographicKeys>
        <Key Id="client_secret" StorageReferenceId="B2C_1A_GoogleSecret" />
        </CryptographicKeys>
        <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="socialIdpUserId" PartnerClaimType="id" />
        <OutputClaim ClaimTypeReferenceId="email" PartnerClaimType="email" />
        <OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="given_name" />
        <OutputClaim ClaimTypeReferenceId="surname" PartnerClaimType="family_name" />
        <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="name" />
        <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="google.com" />
        <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="socialIdpAuthentication" />
        </OutputClaims>
        <OutputClaimsTransformations>
        <OutputClaimsTransformation ReferenceId="CreateRandomUPNUserName" />
        <OutputClaimsTransformation ReferenceId="CreateUserPrincipalName" />
        <OutputClaimsTransformation ReferenceId="CreateAlternativeSecurityId" />
        <OutputClaimsTransformation ReferenceId="CreateSubjectClaimFromAlternativeSecurityId" />
        </OutputClaimsTransformations>
        <UseTechnicalProfileForSessionManagement ReferenceId="SM-SocialLogin" />
        <ErrorHandlers>
        <ErrorHandler>
            <ErrorResponseFormat>json</ErrorResponseFormat>
            <ResponseMatch>$[?(@@.error == 'invalid_grant')]</ResponseMatch>
            <Action>Reauthenticate</Action>
            <!--In case of authorization code used error, we don't want the user to select his account again.-->
            <!--AdditionalRequestParameters Key="prompt">select_account</AdditionalRequestParameters-->
        </ErrorHandler>
        </ErrorHandlers>
    </TechnicalProfile>
    </TechnicalProfiles>
</ClaimsProvider>
```

## <a name="register-the-google-account-claims-provider-to-sign-up-or-sign-in-user-journey"></a>サインアップまたはサインイン ユーザー体験に Google+ アカウント クレーム プロバイダーを登録する

ID プロバイダーが設定されました。  ただし、サインアップ/サインイン画面のいずれでも使用可能ではありません。 Google+ アカウント ID プロバイダーをユーザーの `SignUpOrSignIn` ユーザー体験に追加します。 使用可能にするには、既存のテンプレート ユーザー体験の複製を作成します。  次に、Google+ アカウント ID プロバイダーを追加します。

>[!NOTE]
>
>`<UserJourneys>` 要素をポリシーの基本ファイルから拡張ファイル (TrustFrameworkExtensions.xml) にコピーした場合は、このセクションをスキップすることができます。

1.  ポリシーの基本ファイルを開きます (例: TrustFrameworkBase.xml)。
2.  `<UserJourneys>` 要素を見つけて、`<UserJourneys>` ノードのコンテンツ全体をコピーします。
3.  拡張ファイル (例: TrustFrameworkExtensions.xml) を開き、`<UserJourneys>` 要素を見つけます。 要素が存在しない場合は追加します。
4.  コピーした `<UserJournesy>` ノードのコンテンツ全体を `<UserJourneys>` 要素の子として貼り付けます。

### <a name="display-the-button"></a>ボタンを表示する
`<ClaimsProviderSelections>` 要素は、クレーム プロバイダーの選択オプションとその順序の一覧を定義します。  `<ClaimsProviderSelection>` 要素は、サインアップ/サインイン ページの ID プロバイダーのボタンに類似しています。 Google+ アカウントのために `<ClaimsProviderSelection>` 要素を追加すると、ユーザーがページにアクセスしたときに新しいボタンが表示されます。 この要素を追加するには、次の手順を実行します。

1.  コピーしたばかりのユーザー体験内で、`Id="SignUpOrSignIn"` を含む `<UserJourney>` ノードを見つけます。
2.  `Order="1"` を含む `<OrchestrationStep>` ノードを見つける
3.  `<ClaimsProviderSelections>` ノード下に次の XML スニペットを追加します。

```xml
<ClaimsProviderSelection TargetClaimsExchangeId="GoogleExchange" />
```

### <a name="link-the-button-to-an-action"></a>ボタンのアクションへのリンク
ボタンが所定の位置に配置されたので、ボタンをアクションにリンクする必要があります。 この場合のアクションでは、Azure AD B2C が Google+ アカウントと通信してトークンを受信します。

1.  `<UserJourney>` ノード内で、`Order="2"` が含まれている `<OrchestrationStep>` を見つけます。
2.  `<ClaimsExchanges>` ノード下に次の XML スニペットを追加します。

```xml
<ClaimsExchange Id="GoogleExchange" TechnicalProfileReferenceId="Google-OAUTH" />
```

>[!NOTE]
>
> * `Id` が前のセクションの `TargetClaimsExchangeId` と同じ値であることを確認します
> * `TechnicalProfileReferenceId` ID が前に作成した技術プロファイル (Google-OAUTH) に設定されていることを確認します。

## <a name="upload-the-policy-to-your-tenant"></a>ポリシーをテナントにアップロードする
1.  [Azure Portal](https://portal.azure.com) で、[Azure AD B2C テナントのコンテキストに切り替え](active-directory-b2c-navigate-to-b2c-context.md)、**[Azure AD B2C]** ブレードを開きます。
2.  **[Identity Experience Framework]** を選択します。
3.  **[すべてのポリシー]** ブレードを開きます。
4.  **[ポリシーのアップロード]** を選択します。
5.  **[ポリシーが存在する場合は上書きする]** チェック ボックスをオンにします。
6.  TrustFrameworkExtensions.xml を**アップロード**し、検証に失敗しないことを確認します。

## <a name="test-the-custom-policy-by-using-run-now"></a>[今すぐ実行] を使用してカスタム ポリシーをテストする
1.  **[Azure AD B2C の設定]** を開き、**[Identity Experience Framework]** に移動します。

    >[!NOTE]
    >
    >    **[今すぐ実行]** を使用するには、テナントに少なくとも 1 つのアプリケーションが事前登録されている必要があります。 
    >    アプリケーションを登録する方法については、 Azure AD B2C の[概要](active-directory-b2c-get-started.md)に関する記事または[アプリケーションの登録](active-directory-b2c-app-registration.md)に関する記事を参照してください。


2.  アップロードした証明書利用者 (RP) カスタム ポリシーである **B2C_1A_signup_signin** を開きます。 **[今すぐ実行]** を選択します。
3.  Google+ アカウントを使ってサインインすることができます。

## <a name="optional-register-the-google-account-claims-provider-to-profile-edit-user-journey"></a>[省略可能] プロファイル編集ユーザー体験に Google+ アカウント クレーム プロバイダーを登録する
Google+ アカウント ID プロバイダーをユーザーの `ProfileEdit` ユーザー体験に追加することもできます。 利用できるようには、最後の 2 つの手順を繰り返します。

### <a name="display-the-button"></a>ボタンを表示する
1.  ポリシーの拡張ファイル (例: TrustFrameworkExtensions.xml) を開きます。
2.  コピーしたばかりのユーザー体験内で、`Id="ProfileEdit"` を含む `<UserJourney>` ノードを見つけます。
3.  `Order="1"` を含む `<OrchestrationStep>` ノードを見つける
4.  `<ClaimsProviderSelections>` ノード下に次の XML スニペットを追加します。

```xml
<ClaimsProviderSelection TargetClaimsExchangeId="GoogleExchange" />
```

### <a name="link-the-button-to-an-action"></a>ボタンのアクションへのリンク
1.  `<UserJourney>` ノード内で、`Order="2"` が含まれている `<OrchestrationStep>` を見つけます。
2.  `<ClaimsExchanges>` ノード下に次の XML スニペットを追加します。

```xml
<ClaimsExchange Id="GoogleExchange" TechnicalProfileReferenceId="Google-OAUTH" />
```

### <a name="test-the-custom-profile-edit-policy-by-using-run-now"></a>[今すぐ実行] を使ってカスタム プロファイル編集ポリシーをテストする

1.  **[Azure AD B2C の設定]** を開き、**[Identity Experience Framework]** に移動します。
2.  アップロードした証明書利用者 (RP) カスタム ポリシーである **B2C_1A_ProfileEdit** を開きます。 **[今すぐ実行]** を選択します。
3.  Google+ アカウントを使ってサインインすることができます。

## <a name="download-the-complete-policy-files"></a>完全なポリシー ファイルをダウンロードする
省略可能: これらのサンプル ファイルを使う代わりに、カスタム ポリシーの概要チュートリアルの完了後に独自のカスタム ポリシーを使ってシナリオを構築することをお勧めします。  [参照用のサンプル ポリシー ファイル](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/aadb2c-ief-setup-goog-app)
